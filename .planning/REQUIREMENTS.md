# Requirements

## v1 Requirements

### Template System Foundation

- [ ] **TMPL-01**: Each template has explicit input/output contracts (schema-defined)
  - Defines what template receives and produces
  - Enables validation of cross-template handoffs
  - Makes templates testable in isolation

- [ ] **TMPL-02**: Summary generation logic encapsulated in each template (not router)
  - Each template specifies its own summary format
  - Router only passes summaries, doesn't define them
  - Fixes current logic leakage architecture violation

- [ ] **TMPL-03**: Format standards defined once in router; templates reference them
  - Single source of truth for indentation, list style, language policy
  - Templates reference rather than duplicate
  - Version markers detect drift

- [ ] **TMPL-04**: PARTS iteration rules embedded in each iterative template
  - Each template defines max items per PART
  - Continuation rules specific to template's content
  - Anti-drift checkpoints at PART boundaries

- [ ] **TMPL-05**: Router explicitly names which template to use in each step
  - No implicit template selection
  - Clear step-to-template mapping
  - Custom GPT knows exactly which Knowledge file to use

### Format & Consistency

- [ ] **FMT-01**: Single canonical link format (raw URLs on own line for ChatGPT preview)
  - Raw URL format: `https://example.com/path` on its own line
  - No bullets, no punctuation, no markdown wrappers
  - Renders as clickable preview card in ChatGPT

- [ ] **FMT-02**: Anti-drift rules with continuity checkpoints across PARTS
  - Explicit field names to use at start of each PART
  - Format fingerprint at end of PART that next PART must echo
  - Reduces instruction drift during iteration

- [ ] **FMT-03**: Negative examples showing prohibited patterns
  - Explicit "do NOT" for search queries instead of URLs
  - Explicit "do NOT" for plain text links without URLs
  - Explicit "do NOT" for markdown link format
  - Explicit "do NOT" for changing field names mid-PARTS

- [ ] **FMT-04**: Format fingerprints at PART boundaries to detect drift
  - Last line of PART includes structure summary
  - Next PART echoes fingerprint before continuing
  - Makes drift immediately visible

### Version Control & Tracking

- [ ] **VER-01**: Behavioral commit messages (describe expected behavior change, not code change)
  - Format: `fix(template): what should change behaviorally`
  - Include "Expected behavior:" and "Observed trigger:"
  - Focus on GPT behavior, not JSON structure

- [ ] **VER-02**: Before/after documentation for each template change
  - Show exact lines changed with context
  - Review all related sections for consistency
  - Surgical edits visible in commit diff

- [ ] **VER-03**: Tags only after manual verification in Custom GPT
  - Run workflow in Custom GPT
  - Verify expected behavior occurs
  - Tag format: `v{major}.{minor}.{patch}` after confirmation

- [ ] **VER-04**: Verification logs in commit messages documenting what was tested
  - What inputs were tested
  - What behavior was observed
  - Whether it matched expectation
  - If not: "previous didn't work, trying X instead"

### Observation & Testing

- [ ] **OBS-01**: Structured observation log format (OBSERVATIONS.md template)
  - Chronological entries with date, template, version
  - Expected vs observed behavior
  - Classification: drift/bug/edge case
  - Action taken

- [ ] **OBS-02**: Per-template validation checklists (derived from template rules)
  - skill_tree_builder: weights sum to 100, 2 levels, action verbs, Spanish labels
  - skill_tree_to_content: raw URLs, alignment notes, time estimates, running totals, format consistency
  - skill_to_practice_exercises: real datasets, no synthetic data, PASS/FAIL criteria, no solution code
  - skill_to_evaluation_exam: 100 points total, correct skill type, submission manifest, rubrics

- [ ] **OBS-03**: Explicit failure modes for unenforceable rules (BLOCK markers when violated)
  - Rules specify what happens if GPT can't comply
  - "If no real URL available, output [BLOCKED: reason]"
  - Makes violations observable rather than silent

- [ ] **OBS-04**: Known-good reference outputs for regression comparison
  - Full workflow run for conceptual skill
  - Full workflow run for technical skill
  - Serves as baseline for detecting regressions

### Schema Validation

- [ ] **SCH-01**: JSON Schema for each template's structure
  - Validates required sections exist
  - Validates field types
  - Catches typos and missing fields
  - Cannot validate behavior (only structure)

- [ ] **SCH-02**: Manual validation workflow (ajv-cli command before commit)
  - Install: `npm install -g ajv-cli`
  - Before commit: `ajv validate -s schemas/{template}.schema.json -d {template}.json`
  - Protects against JSON syntax errors and structural mistakes
  - Part of change checklist, not automated

- [ ] **SCH-03**: Version markers for contract compatibility checking
  - Input contracts specify expected upstream version
  - Output contracts specify version they produce
  - Detects when handoff formats drift

## v2 Requirements (Deferred)

### Advanced Observation
- [ ] **OBS-05**: Automated schema validation in CI (when tooling allows)
- [ ] **OBS-06**: Diff visualization for template changes
- [ ] **OBS-07**: Observation pattern analysis (which issues repeat most)

### Template Optimization
- [ ] **TMPL-06**: Template complexity budget enforcement (max rules per template)
- [ ] **TMPL-07**: Rule simplification audit (merge/remove redundant rules)
- [ ] **TMPL-08**: Recovery instructions for mid-PARTS failures

### Cross-Template
- [ ] **XREF-01**: Shared contract schemas in separate files
- [ ] **XREF-02**: Cross-template consistency audit tool
- [ ] **XREF-03**: Template dependency visualization

## Out of Scope

- **Automated testing via API** — OpenAI doesn't provide Custom GPT API access; manual testing only
- **CI/CD pipeline** — Deployment is manual copy-paste to Knowledge files; no automation possible
- **Template inheritance/composition** — Creates coupling; prefer explicit duplication with attribution
- **Multi-language output** — Spanish is the current requirement; no need for parameterization
- **Prompt testing frameworks** — Require API access that Custom GPTs don't expose
- **Real-time drift detection** — No runtime monitoring possible; observation logs are post-hoc

## Traceability

### Template Foundation → Roadmap Phases
- TMPL-01, TMPL-02, TMPL-03, TMPL-04, TMPL-05 → Phase: Contract Infrastructure & Template Encapsulation

### Format & Consistency → Roadmap Phases
- FMT-01, FMT-02, FMT-03, FMT-04 → Phase: Anti-Drift Rules & Link Format Standardization

### Version Control → Roadmap Phases
- VER-01, VER-02, VER-03, VER-04 → Phase: Behavioral Versioning Protocol

### Observation → Roadmap Phases
- OBS-01, OBS-02, OBS-03, OBS-04 → Phase: Observation Infrastructure

### Schema Validation → Roadmap Phases
- SCH-01, SCH-02, SCH-03 → Phase: Contract Infrastructure

---
*Requirements for: AI Certifications Manager Template Improvement System*
*Last updated: 2026-02-05*
