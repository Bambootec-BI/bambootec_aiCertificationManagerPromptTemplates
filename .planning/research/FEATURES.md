# Feature Research: Prompt Template System Maintainability

**Domain:** JSON prompt template systems for Custom GPTs with iterative output
**Researched:** 2026-02-05
**Confidence:** MEDIUM (based on prompt engineering principles and observed patterns; WebSearch unavailable for external verification)

## Feature Landscape

### Table Stakes (Users Expect These)

Features that must exist or template quality degrades. Missing these creates observable drift, inconsistent output, or maintenance burden.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| **Template encapsulation** | Each template must be self-contained with its own inputs, outputs, and handoff format. Leaking logic to router creates coupling that makes changes risky. | LOW | Currently summary generation leaks to router—this is a table-stakes violation |
| **Format specification completeness** | Every output format rule must be explicit in the template. GPT "fills in gaps" unpredictably when rules are implicit or inherited. | MEDIUM | Link formats, field names, ordering must all be specified per-template |
| **Iteration consistency rules** | PARTS systems need explicit "across-iteration invariants" stating what must NOT change mid-stream. | LOW | Current templates have this partially; needs systematization |
| **Input/output contracts** | Clear specification of what each template receives and produces, like function signatures. | LOW | Enables surgical changes without understanding entire system |
| **Version tracking** | `template_version` field with changelog. Without this, impossible to know what behavior was intended. | LOW | Already exists but usage inconsistent |
| **Negative examples (anti-patterns)** | Templates must specify what NOT to do, not just what to do. GPT follows positive examples better when negative cases are explicit. | MEDIUM | "Do NOT change field names mid-PARTS" more reliable than "keep format consistent" |

### Differentiators (Competitive Advantage)

Features that elevate template maintainability beyond basic functioning. Not required but create significant long-term value.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Behavioral commit messages** | Commits describe expected behavior change, not code change. "Link drift should stop" vs "updated sources_non_negotiable rule". | LOW | Changes maintenance mindset from code-centric to behavior-centric |
| **Before/after change documentation** | Each template change includes exact previous behavior and expected new behavior. | LOW | Enables "this didn't work, trying X" pattern without losing history |
| **Failure mode documentation** | Each rule includes what happens if GPT violates it. Makes diagnosis faster. | MEDIUM | "If links appear as plain text, check sources_non_negotiable enforcement" |
| **Observation log pattern** | Structured way to record "GPT did X instead of Y" observations that drive template changes. | MEDIUM | Links template evolution to empirical behavior, not theory |
| **Cross-template consistency rules** | Explicit rules for what must stay consistent across templates in the system (field names, format conventions, terminology). | MEDIUM | Prevents drift between templates that share data |
| **Graduated rule strength** | Distinguish NON-NEGOTIABLE from preferred from default. GPT responds differently to rule strength signaling. | LOW | Already partially implemented; systematize across all templates |
| **Recovery instructions** | What to do when GPT violates a rule mid-PARTS. Should it restart? Continue with correction? | MEDIUM | Prevents cascading format failures |

### Anti-Features (Commonly Requested, Often Problematic)

Features that seem good but create problems in prompt template systems.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| **Inheritance/template composition** | DRY principle—don't repeat format rules across templates | GPT doesn't reliably "import" from other templates. Creates hidden dependencies. When base changes, derived templates break silently. | **Copy with attribution**: duplicate rules explicitly, note "copied from X" for future sync |
| **Dynamic rule generation** | Templates that generate rules based on context | GPT generates rules inconsistently; loses the benefit of explicit, tested constraints | **Explicit variants**: create separate templates for each variant (technical vs conceptual skill) |
| **Overly granular versioning** | Track every small change with semver patches | Creates version fatigue, encourages skipping updates. Real value is knowing "what behavior changed" not "version 1.7.3 vs 1.7.4" | **Behavioral tags**: tag stable behaviors ("links-reliable-v2") instead of mechanical version increments |
| **Automated testing via API** | Run templates through OpenAI API and validate output | Custom GPT behavior differs from API. Tests pass but production fails. Creates false confidence. | **Manual verification protocol**: structured checklist for manual testing with specific test cases |
| **Centralized formatting rules** | One place for all format specifications | GPT loses format rules that aren't "near" the content they govern. Centralized rules get ignored for distant output sections. | **Local redundancy**: repeat format rules near each output section that uses them |
| **Abstract/generic examples** | Show output shape without specific content | GPT produces abstract/generic output. Concrete examples produce concrete output. | **Full concrete examples**: show complete realistic examples even if they take space |
| **Implicit defaults** | "If not specified, use X" | GPT doesn't reliably apply implicit defaults. Behavior varies by context length, prompt position. | **Explicit all defaults**: state every default value explicitly in every template |
| **Complex conditional logic** | "If X then do Y, else if Z then do W" | GPT follows simple conditionals but loses complex branching. Later conditions get applied inconsistently. | **Template variants**: separate templates for different conditions, router picks correct one |

## Feature Dependencies

```
[Template encapsulation]
    └──requires──> [Input/output contracts]
                       └──enables──> [Cross-template consistency rules]

[Behavioral commit messages]
    └──requires──> [Before/after change documentation]
                       └──enables──> [Observation log pattern]

[Format specification completeness]
    └──requires──> [Negative examples (anti-patterns)]
                       └──requires──> [Iteration consistency rules]

[Version tracking] ──enhances──> [Behavioral commit messages]

[Recovery instructions] ──requires──> [Failure mode documentation]
```

### Dependency Notes

- **Template encapsulation requires input/output contracts:** You can't make a template self-contained without clearly defining its boundaries
- **Behavioral commits require before/after documentation:** You can't describe expected behavior change without documenting current behavior
- **Format completeness requires negative examples:** Positive rules alone leave gaps; GPT needs explicit "do not" to avoid drift
- **Recovery instructions require failure mode documentation:** You can't recover from a violation you haven't characterized

## MVP Definition

### Launch With (v1)

Minimum viable improvements—what's needed to address current pain points.

- [ ] **Template encapsulation** — Fix summary generation leakage; each template specifies its own handoff
- [ ] **Iteration consistency rules** — Explicit "across-PARTS invariants" in each iterative template
- [ ] **Negative examples for link formatting** — Explicit "do NOT" examples for common link drift patterns
- [ ] **Input/output contracts** — Document what each template receives/produces
- [ ] **Behavioral commit messages** — Start describing expected behavior change in commits

### Add After Validation (v1.x)

Features to add once core issues are resolved and we have working observation data.

- [ ] **Failure mode documentation** — Once we've cataloged common GPT violations from observation logs
- [ ] **Cross-template consistency rules** — After encapsulation is solid, formalize shared conventions
- [ ] **Observation log pattern** — Structure for capturing "GPT did X instead of Y" that drives changes

### Future Consideration (v2+)

Features to defer until template system is stable and patterns are well-understood.

- [ ] **Graduated rule strength systematization** — Audit and normalize NON-NEGOTIABLE vs preferred across all templates
- [ ] **Recovery instructions** — Requires understanding common mid-PARTS failure modes first
- [ ] **Before/after change documentation** — Formalize as structured metadata, not just commit messages

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Template encapsulation | HIGH | LOW | P1 |
| Iteration consistency rules | HIGH | LOW | P1 |
| Negative examples for links | HIGH | LOW | P1 |
| Input/output contracts | MEDIUM | LOW | P1 |
| Behavioral commit messages | HIGH | LOW | P1 |
| Failure mode documentation | MEDIUM | MEDIUM | P2 |
| Cross-template consistency | MEDIUM | MEDIUM | P2 |
| Observation log pattern | MEDIUM | MEDIUM | P2 |
| Graduated rule strength | LOW | LOW | P3 |
| Recovery instructions | LOW | MEDIUM | P3 |

**Priority key:**
- P1: Must have—addresses current pain points
- P2: Should have—improves maintainability workflow
- P3: Nice to have—optimization once system is stable

## Complexity/Effort Analysis

### Low Complexity (Hours)

| Feature | Effort Estimate | Notes |
|---------|-----------------|-------|
| Template encapsulation | 2-4 hours | Move summary generation into each template; update router to expect it |
| Input/output contracts | 2-3 hours | Add "inputs" and "outputs" documentation sections to each template |
| Negative examples | 1-2 hours per template | Add "prohibited" examples to existing rules sections |
| Behavioral commit messages | Process change | Requires discipline, not implementation |
| Version tracking improvement | 1 hour | Standardize version field usage across templates |

### Medium Complexity (Days)

| Feature | Effort Estimate | Notes |
|---------|-----------------|-------|
| Iteration consistency rules | 1-2 days | Requires auditing each template for implicit consistency assumptions |
| Failure mode documentation | 2-3 days | Requires collecting observations first, then documenting patterns |
| Cross-template consistency | 2-3 days | Audit all templates for shared conventions, document explicitly |
| Observation log pattern | 1-2 days | Design structure, create template, establish workflow |

### High Complexity (Weeks)

| Feature | Effort Estimate | Notes |
|---------|-----------------|-------|
| Graduated rule strength systematization | 1 week | Full audit of all rules across all templates |
| Recovery instructions | 1-2 weeks | Requires observing and categorizing failure patterns first |

## Prompt Template System Principles

Derived from analysis of current system and prompt engineering fundamentals.

### Principle 1: Locality of Rules

**What:** Rules must be stated near the content they govern.

**Why:** GPT attention patterns favor nearby context. A formatting rule 200 tokens from the output section it governs will be followed less reliably than one immediately preceding the output.

**Implication:** Repeat rules rather than reference them. Redundancy is a feature, not a bug.

### Principle 2: Negative Specification

**What:** Explicit "do NOT" rules are more reliable than implicit expectations.

**Why:** GPT will try to be helpful by filling gaps. If you don't say "do NOT output search queries," GPT may substitute them when it can't find a real URL.

**Implication:** For every positive rule, consider what unwanted behavior it might not prevent, and add negative rules.

### Principle 3: Concrete Over Abstract

**What:** Concrete examples produce concrete output; abstract examples produce abstract output.

**Why:** GPT pattern-matches. If your example shows `[PLACEHOLDER]`, output may contain literal placeholder patterns. If your example shows `https://kaggle.com/datasets/specific-dataset`, output will follow that pattern.

**Implication:** Full realistic examples, even if verbose, outperform compact abstract schemas.

### Principle 4: Behavioral Framing

**What:** Frame template changes in terms of expected behavior change, not structural change.

**Why:** The goal is GPT behavior. Template structure is a means to that end. Tracking "what should GPT do differently" keeps focus on observable outcomes.

**Implication:** Commit messages, changelogs, and version notes should describe behavior, not JSON changes.

### Principle 5: Encapsulation at Template Boundary

**What:** Each template should be a complete, self-contained unit that doesn't require external context to function correctly.

**Why:** Cross-template dependencies create coupling. When template A depends on behavior specified in router R, changes to R may break A without any change to A. This makes reasoning about changes difficult.

**Implication:** Summary generation, format rules, and handoff specifications belong in the template that produces them, not in orchestration layers.

## Sources

- Analysis of existing templates: `skill_tree_to_content_tree_template.json`, `custom_gpt_instructions.json`
- Project context: `.planning/PROJECT.md`, `CLAUDE.md`
- Prompt engineering principles (training data knowledge—MEDIUM confidence)
- Custom GPT behavior patterns (training data knowledge—MEDIUM confidence)

**Limitations:**
- WebSearch unavailable; no external verification of current best practices
- Recommendations based on prompt engineering fundamentals and project-specific observations
- Custom GPT behavior may differ from documented patterns; empirical validation required

---
*Feature research for: Prompt template system maintainability*
*Researched: 2026-02-05*
