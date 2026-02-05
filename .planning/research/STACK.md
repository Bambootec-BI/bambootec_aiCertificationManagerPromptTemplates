# Stack Research

**Domain:** JSON-based prompt template systems for Custom GPTs (improvement patterns)
**Researched:** 2026-02-05
**Confidence:** MEDIUM (based on training data + analysis of existing system; WebSearch unavailable)

## Executive Summary

This research addresses maintaining and improving an existing 5-template JSON prompt system for a Custom GPT. The key constraint is **no API access** — templates are deployed as Knowledge files via manual copy-paste, and testing is observational only.

The recommended stack focuses on:
1. **Git-based version control** with behavior-tracking commit conventions
2. **JSON Schema validation** for offline pre-deployment checks
3. **Structured observation logs** for drift detection
4. **Template modularization** through consistent section contracts

This is NOT a typical software stack — there is no runtime, no deployment pipeline, no automated testing. The "stack" is a methodology for managing prompt artifacts.

---

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Git | 2.x | Version control with behavior tracking | Standard, universal; commit messages become the behavior changelog; tags mark stable versions [HIGH confidence] |
| JSON Schema | Draft-07+ | Offline template validation | Validates structure before manual upload; catches field name typos, missing sections, type errors [HIGH confidence] |
| ajv-cli | 5.x | Command-line JSON Schema validator | Fast, scriptable, works offline; runs `ajv validate -s schema.json -d template.json` [MEDIUM confidence — verify latest version] |
| VS Code | Latest | Template editing with JSON support | Built-in JSON syntax validation, schema association, diff view for before/after comparisons [HIGH confidence] |

### Supporting Tools

| Tool | Purpose | When to Use |
|------|---------|-------------|
| jq | CLI JSON processor | Extract/compare specific sections; diff templates between versions |
| diff / git diff | Change visualization | Review exact changes before commit; required for surgical edits |
| Markdown files | Observation logging | Record GPT behavior deviations, link to commits |

### Observation & Tracking Infrastructure

| Component | Format | Purpose |
|-----------|--------|---------|
| OBSERVATIONS.md | Markdown log | Chronological record of GPT behavior observations |
| CHANGELOG.md | Keep-a-changelog format | Human-readable behavior changes per version |
| Git tags | `v{major}.{minor}.{patch}` | Mark stable versions after confirmed good behavior |
| Commit messages | Conventional commits + behavior notes | Link changes to expected behavioral impact |

---

## Version Control Strategy

### Commit Message Convention

**Format:** Conventional Commits adapted for behavioral impact

```
<type>(<template>): <what changed>

<why this change was made — observed behavior that prompted it>

Expected behavior: <what the GPT should now do>
Observed trigger: <what GPT was doing wrong>

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

**Types:**
- `fix` — Corrects a behavioral drift (GPT was doing X, should do Y)
- `feat` — Adds new capability to template
- `refactor` — Restructures without changing expected behavior
- `docs` — Template documentation only (changelog, comments)
- `chore` — Non-template changes (schema, tooling)

**Example:**
```
fix(content_tree): enforce raw URL format in Fuentes section

GPT was outputting markdown links [Title](URL) which don't render
as previewable cards in ChatGPT interface.

Expected behavior: Raw URLs on separate lines for UI preview
Observed trigger: Links in 3 consecutive runs rendered without previews

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

### Tagging Strategy

| Tag Pattern | Meaning | When to Create |
|-------------|---------|----------------|
| `v1.2.0` | Stable release | After 3+ runs with consistent expected behavior |
| `v1.2.1-rc1` | Release candidate | After fix, before confirming behavior |
| `v1.2.1-broken` | Known bad state | Mark commits that caused worse behavior |

### Branch Strategy

**Recommended:** Single `main` branch with tags, not feature branches.

**Rationale:**
- Templates are deployed individually (no atomic multi-template deploys)
- Manual copy-paste doesn't support rollback anyway
- Git tags provide the "restore point" functionality
- Feature branches add complexity without deployment benefit

**Confidence:** MEDIUM — this is opinionated; some teams prefer branches even for manual workflows.

---

## Schema Validation Approach

### JSON Schema Design

Create one schema per template that validates:

1. **Required top-level sections** (meta, inputs, rules, instruction, output)
2. **Version field format** (semver string)
3. **Input parameter structure** (each has name, description)
4. **Rule consistency** (rules reference only defined inputs)

**File structure:**
```
schemas/
  skill_tree_builder.schema.json
  skill_tree_to_content.schema.json
  skill_to_practice_exercises.schema.json
  skill_to_evaluation_exam.schema.json
  custom_gpt_instructions.schema.json
```

### Pre-Commit Validation

**Simple approach (manual):**
```bash
# Validate before committing
ajv validate -s schemas/skill_tree_builder.schema.json -d skill_tree_builder_template.json
```

**Better approach (git hook):**
```bash
# .git/hooks/pre-commit
#!/bin/bash
for template in *.json; do
  schema="schemas/${template%.json}.schema.json"
  if [ -f "$schema" ]; then
    ajv validate -s "$schema" -d "$template" || exit 1
  fi
done
```

### What Schema Validation Catches

| Issue | Detectable | Example |
|-------|------------|---------|
| Missing required section | Yes | Forgot `output` section |
| Field name typo | Yes | `rule` instead of `rules` |
| Wrong type | Yes | String where array expected |
| Invalid version format | Yes | `1.2` instead of `1.2.0` |
| Cross-template consistency | No | Different input names across templates |
| Behavioral effectiveness | No | Whether GPT will actually follow the rule |

**Confidence:** HIGH for structural validation; schema cannot validate behavior.

---

## Drift Detection Patterns

### Manual Observation Protocol

Since no automated testing exists, drift detection is observational.

**Observation Log Format (OBSERVATIONS.md):**
```markdown
## 2026-02-05 — Content Tree PART 3

**Run ID:** manual-run-005
**Template version:** v1.7
**Input skill:** Python web scraping

### Expected behavior
- Fuentes section shows raw URLs
- Each source has Alineacion note

### Observed behavior
- URLs appeared in markdown link format
- 2/6 sources missing Alineacion

### Classification
- [x] Drift (behavior changed from previous runs)
- [ ] Bug (never worked correctly)
- [ ] Edge case (new input pattern)

### Action
Opened issue to investigate link formatting rule
```

### Systematic Observation Checklist

For each template run, verify:

**skill_tree_builder:**
- [ ] Impact weights sum to 100
- [ ] Exactly 2 hierarchy levels
- [ ] All skills have action verbs
- [ ] Spanish output labels

**skill_tree_to_content:**
- [ ] Raw URLs (not markdown links)
- [ ] Alineacion notes present per source
- [ ] Time estimates at every level
- [ ] Running total at PART end
- [ ] Format consistent across PARTS

**skill_to_practice_exercises:**
- [ ] Real dataset acquisition method per exercise
- [ ] No synthetic data generation
- [ ] Pass/fail criteria explicit
- [ ] No solution code (only acquisition code)

**skill_to_evaluation_exam:**
- [ ] Score totals to 100 points
- [ ] Skill type correctly inferred
- [ ] Submission manifest for complex tasks
- [ ] Rubric dimensions present

**Confidence:** HIGH — this is directly derived from template validation invariants in CLAUDE.md.

---

## Template Modularization Techniques

### Current Problem

Summary generation logic lives in router (`custom_gpt_instructions.json`), not in templates. This creates:
- Coupling between router and template internals
- Summary format inconsistency across templates
- Difficulty testing templates in isolation

### Recommended Pattern: Self-Contained Templates

Each template should specify its own:

1. **Input contract** — What it receives (schema)
2. **Output contract** — What it produces (schema)
3. **Summary generation** — How to produce the handoff artifact
4. **Verification checklist** — What makes a valid output

**Template Section Structure:**
```json
{
  "meta": { "version": "1.2.0", "name": "template_name" },
  "input_contract": {
    "required": ["skill", "level", "context"],
    "optional": ["skill_tree"],
    "from_previous_step": ["content_summary"]
  },
  "output_contract": {
    "primary": "content_tree_markdown",
    "summary": "content_summary_for_next_step",
    "summary_generation": {
      "when": "at end of final PART",
      "format": "3-5 bullet points covering: concepts, pitfalls, constraints, artifacts, scope",
      "purpose": "handoff to next template"
    }
  },
  "rules": { ... },
  "instruction": { ... }
}
```

### Encapsulation Checklist

For each template, verify:

- [ ] Input contract lists all required fields with types
- [ ] Output contract describes primary output + summary format
- [ ] Summary generation rules are IN the template, not in router
- [ ] Router only routes — doesn't specify template internals
- [ ] Template can theoretically run standalone with mock inputs

**Confidence:** MEDIUM — this is a design pattern, not a verified library approach.

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| Prompt testing frameworks (PromptFoo, Langsmith) | Require API access; Custom GPTs don't expose API | Manual observation + git-based tracking |
| OpenAI Assistants API | Different product; Custom GPTs have no API | Keep using Knowledge file uploads |
| Automated CI/CD pipelines | Nothing to automate; deployment is manual copy-paste | Git pre-commit hooks for schema validation only |
| Complex schema languages (OpenAPI, JSON-LD) | Overkill for template structure; adds learning curve | Standard JSON Schema Draft-07 |
| Template inheritance/composition frameworks | No runtime execution; templates are static documents | Manual consistency via copy + review |
| Database for version history | Git already tracks history; no query needs | Git log + git diff + tags |
| Prompt chaining libraries (LangChain, etc.) | Require code execution and API access | Templates as static JSON Knowledge files |

**Confidence:** HIGH — these are ruled out by the hard constraint of no API access.

---

## Alternatives Considered

| Category | Recommended | Alternative | When to Use Alternative |
|----------|-------------|-------------|-------------------------|
| Version control | Git with tags | Numbered file copies | If git setup is blocked (but why?) |
| Schema validation | ajv-cli | Online JSON Schema validators | One-off validation, no local setup |
| Observation tracking | Markdown logs | Spreadsheet | If team prefers spreadsheet for filtering |
| Diff visualization | git diff | VS Code diff | When comparing non-committed changes |

---

## Installation

```bash
# Schema validation (one-time setup)
npm install -g ajv-cli

# Verify installation
ajv --version

# Validate a template
ajv validate -s schemas/skill_tree_builder.schema.json -d skill_tree_builder_template.json
```

**Note:** No package.json or npm project required. This is CLI tooling, not a Node.js application.

---

## Version Compatibility

| Tool | Version | Notes |
|------|---------|-------|
| ajv-cli | 5.x | Works with JSON Schema Draft-07 |
| Git | 2.x | Any modern version |
| Node.js | 18+ | Required only for ajv-cli installation |
| VS Code | Any | Built-in JSON validation; no extension required |

---

## Roadmap Implications

Based on this research:

1. **Phase: Schema Creation** — Define JSON Schema for each template (structural validation)
2. **Phase: Observation Infrastructure** — Create OBSERVATIONS.md template, checklist, workflow
3. **Phase: Template Encapsulation** — Move summary generation into each template
4. **Phase: Commit Convention Adoption** — Establish behavior-tracking commit messages
5. **Phase: Version Tagging** — Tag current stable state before making changes

**Research flags:**
- Schema creation is standard work (no deeper research needed)
- Encapsulation refactoring may require multiple iterations to find right abstraction

---

## Confidence Assessment

| Area | Confidence | Reason |
|------|------------|--------|
| Git version control | HIGH | Universal best practice, directly applicable |
| JSON Schema validation | HIGH | Well-documented, tool exists, solves stated problem |
| Commit conventions | MEDIUM | Opinionated; needs team buy-in; effective but not verified |
| Observation logging | MEDIUM | Pattern is sound; specific format is proposed, not proven |
| Modularization pattern | MEDIUM | Design principle extrapolated; no external validation |
| What NOT to use | HIGH | Directly ruled out by constraint (no API access) |

---

## Sources

- Training data knowledge of JSON Schema, Git best practices, prompt engineering (confidence: MEDIUM — 6-18 months stale)
- Analysis of existing templates in this repository (confidence: HIGH — direct observation)
- PROJECT.md constraints and requirements (confidence: HIGH — project documentation)
- CLAUDE.md validation invariants (confidence: HIGH — project documentation)

**Note:** WebSearch was unavailable during this research. All recommendations are based on training data and existing project analysis. Specific tool versions should be verified before use.

---
*Stack research for: JSON-based prompt template systems for Custom GPTs*
*Researched: 2026-02-05*
