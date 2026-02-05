# Project Research Summary

**Project:** AI Certifications Manager (Prompt Template System)
**Domain:** JSON prompt template systems for Custom GPTs with iterative output
**Researched:** 2026-02-05
**Confidence:** MEDIUM

## Executive Summary

This project maintains a 5-template JSON prompt system for a Custom GPT that generates AI certification learning paths. The core challenge is unique: **no API access, no automated testing, manual deployment via copy-paste**. Templates are deployed as Knowledge files, and the only feedback mechanism is observational testing in the ChatGPT interface.

The research reveals this is fundamentally a **prompt artifact maintenance problem**, not a software engineering problem. The recommended approach centers on: (1) Git-based version control with behavior-tracking commit conventions, (2) JSON Schema validation for structural integrity, (3) systematic observation logging for drift detection, and (4) template encapsulation to make each template self-contained. The current system suffers from logic leakage (summary generation rules live in the router instead of templates), format drift during iterative output (PARTS), and lack of behavioral verification before version tagging.

Key risks include instruction drift during multi-part output (field names and formatting degrade as iteration continues), link format degradation (URLs become search queries or markdown that doesn't render), and cross-template handoff corruption (summaries passed between templates get lost or malformed). Mitigation requires explicit anti-drift rules, negative examples showing what NOT to do, and structured handoff contracts between templates. The hardest constraint is unenforceable rules—templates can only suggest behavior, not constrain it—requiring systematic manual verification with explicit checklists.

## Key Findings

### Recommended Stack

The "stack" is a methodology for managing prompt artifacts, not traditional software infrastructure. There is no runtime, no deployment pipeline, no automated testing. The focus is on version control, offline validation, and observation-based iteration.

**Core technologies:**
- **Git with behavioral commit messages**: Version control where commits describe expected behavior change ("link drift should stop"), not code change ("updated sources rule"). Provides behavior changelog.
- **JSON Schema (ajv-cli)**: Offline validation catches structural errors (missing sections, type mismatches, field name typos) before manual upload to Custom GPT.
- **Markdown observation logs**: Structured recording of "GPT did X instead of Y" observations that drive template refinements. Links behavior to template versions.
- **VS Code with JSON support**: Built-in syntax validation, schema association, and diff view for comparing template versions.

**Critical insight:** No prompt testing frameworks (PromptFoo, Langsmith) can be used because Custom GPTs have no API access. All testing is manual and observational. The stack compensates with rigorous version control and observation logging.

### Expected Features

The feature landscape is about maintainability, not end-user features. The templates already produce the desired outputs; the goal is to make template evolution reliable and predictable.

**Must have (table stakes):**
- **Template encapsulation** — Each template self-contained with own input/output contracts. Currently summary generation leaks to router, creating coupling.
- **Format specification completeness** — Every output format rule explicit. GPT fills gaps unpredictably when rules are implicit.
- **Iteration consistency rules** — Explicit "across-PARTS invariants" stating what must NOT change mid-stream (field names, ordering, structure).
- **Negative examples (anti-patterns)** — Templates must specify what NOT to do. "Do NOT change field names mid-PARTS" more reliable than "keep format consistent."
- **Behavioral commit messages** — Commits describe expected behavior change, not structural change. Tracks "why this change should fix X behavior."

**Should have (competitive):**
- **Failure mode documentation** — Each rule includes what happens if GPT violates it. Speeds diagnosis.
- **Observation log pattern** — Structured format for capturing GPT deviations that drive template changes.
- **Cross-template consistency rules** — Explicit rules for what must stay consistent across templates (field names, format conventions).

**Defer (v2+):**
- **Graduated rule strength systematization** — Audit and normalize NON-NEGOTIABLE vs preferred across all templates.
- **Recovery instructions** — What to do when GPT violates a rule mid-PARTS. Requires understanding common failure modes first.

### Architecture Approach

The system uses a **self-contained template module pattern** where each template is an independent unit with explicit input/output contracts. Currently violates this—the router contains logic (summary generation rules) that belongs in templates, creating tight coupling.

**Target architecture (Module Pattern):**
1. **Input Contract** — Declare required/optional inputs with types, upstream dependencies
2. **Processing Rules** — Template-specific logic, PARTS behavior, validation rules
3. **Output Contract** — Primary output schema, summary schema for handoff, format specifications
4. **Format Standards** — Reference shared definitions (indentation, list style, language)

**Major components:**
1. **skill_tree_builder** — Generates skill decomposition with impact weights; produces skill_summary handoff
2. **skill_tree_to_content** — Transforms skills to learning content with sources (iterative PARTS); produces content_summary
3. **skill_to_practice_exercises** — Generates dataset-grounded practice tasks (iterative PARTS); produces exercise_summary
4. **skill_to_evaluation_exam** — Creates certification exam + evaluator guide; terminal output (no summary)
5. **Router (certifications_manager)** — Step sequencing only; should NOT contain template logic

**Current problems:**
- Router defines summary generation rules (should be in templates)
- Format rules duplicated across templates (should be centralized with references)
- No explicit handoff contracts (summaries are implicit, causing corruption)

### Critical Pitfalls

1. **Instruction drift during PARTS iteration** — GPT follows rules correctly in PART 1 but field names, ordering, and formatting degrade as iteration continues. Prevention: explicit continuity checkpoints with exact field names at start of each PART, anti-drift examples showing wrong variations, format fingerprint at end of each PART that next PART must echo.

2. **Link format degradation** — Links degrade into search queries, plain titles without URLs, or markdown that doesn't render clickable in ChatGPT UI. Prevention: ONE canonical format with explicit anti-patterns ("NEVER: [Title] without URL"), source validation checkpoint (URLs must start with https://), explicit "BLOCK IF NO URL" instruction (better to omit than fake).

3. **Logic leakage from templates to router** — Content-generation logic lives in router instead of templates that produce content. Creates brittleness—templates can't be used independently. Prevention: template encapsulation principle (router only sequences, templates fully specify output behavior), document ownership boundaries.

4. **Unenforceable rules** — Templates contain rules GPT can simply ignore. No runtime validation possible with Knowledge file approach. Prevention: accept unenforceable nature, design for graceful failure with explicit "FAILURE MODE" instructions, create observable failure states for manual testing, prioritize fewer clearer rules.

5. **Version tag without behavior verification** — Versions tagged based on code changes without verified behavior. Version 1.7 may have bug that 1.6 didn't. Prevention: behavior-first versioning (tag only after manual verification), versioning protocol with test-document-tag sequence, verification log in commit messages.

## Implications for Roadmap

Based on research, the roadmap should follow a **foundation-first, encapsulation-second, verification-third** pattern. The dependency chain is clear: format standards must exist before templates can reference them, templates must be encapsulated before testing makes sense, observation infrastructure must exist before behavior can be tracked.

### Phase 1: Contract Infrastructure (Foundation)
**Rationale:** Contracts are the foundation for all subsequent work. Can't encapsulate templates without defining their boundaries. Can't validate handoffs without schemas. Low risk—doesn't change template behavior yet, just documents current state.

**Delivers:**
- format_standards.json (single source of truth for formatting rules)
- Summary schemas: skill_summary.schema.json, content_summary.schema.json, exercise_summary.schema.json
- Router updated to reference format_standards instead of defining inline

**Addresses:** Cross-template consistency rules (from FEATURES.md)
**Avoids:** Format rule duplication pitfall (from PITFALLS.md)
**Research flag:** Standard work—no deeper research needed. JSON Schema is well-documented.

### Phase 2: Template Encapsulation (Core Fix)
**Rationale:** Fixes the fundamental architecture violation—logic leakage to router. Each template must be self-contained with own input/output contracts. This enables surgical changes without understanding entire system. Prerequisite for all other improvements.

**Delivers:**
- Each template has input_contract and output_contract sections
- Summary generation moved from router into templates that produce summaries
- Templates can theoretically run standalone with mock inputs
- Router simplified to pure sequencing

**Addresses:** Template encapsulation (table stakes from FEATURES.md), input/output contracts (table stakes)
**Avoids:** Logic leakage pitfall (from PITFALLS.md), cross-template handoff corruption (from PITFALLS.md)
**Uses:** Contract schemas from Phase 1
**Research flag:** Medium complexity—requires careful refactoring. May need iteration to find right abstraction.

### Phase 3: Anti-Drift Rules (PARTS Stability)
**Rationale:** Iterative output is a core system feature but currently unreliable. Field names and formatting degrade across PARTS. Must stabilize before adding complexity.

**Delivers:**
- Explicit continuity checkpoints in templates with iterative output
- Anti-drift examples showing prohibited variations
- Format fingerprint pattern (end of PART includes exact schema next PART must follow)
- "Across-PARTS invariants" documented per template

**Addresses:** Iteration consistency rules (table stakes from FEATURES.md), negative examples (table stakes)
**Avoids:** Instruction drift pitfall (from PITFALLS.md)
**Research flag:** Standard pattern—no deeper research. Implement, test, iterate.

### Phase 4: Link Format Standardization
**Rationale:** Link rendering is a known pain point causing frequent manual corrections. Single canonical format eliminates ambiguity. Quick win with high user-visible impact.

**Delivers:**
- ONE link format defined in format_standards
- Explicit negative examples in each template that outputs sources
- Source validation checkpoint (URLs must be https://, no queries)
- "BLOCK IF NO URL" instruction in relevant templates

**Addresses:** Format specification completeness (table stakes from FEATURES.md)
**Avoids:** Link format degradation pitfall (from PITFALLS.md)
**Research flag:** No research needed—pattern is clear from current issues.

### Phase 5: Observation Infrastructure
**Rationale:** Can't improve what isn't measured. Systematic observation logging enables data-driven template refinement. Provides empirical basis for future changes.

**Delivers:**
- OBSERVATIONS.md template with structured format
- Observation protocol: what to check per template run
- Checklists derived from template validation invariants
- Linkage pattern: observations -> commits -> version tags

**Addresses:** Observation log pattern (competitive feature from FEATURES.md), behavioral commit messages (table stakes)
**Avoids:** Version tag without verification pitfall (from PITFALLS.md)
**Research flag:** No research needed—pattern defined in STACK.md.

### Phase 6: Behavioral Versioning Protocol
**Rationale:** Tags currently don't reflect verified behavior. Establishes discipline: change -> test -> document -> tag. Prevents regressions.

**Delivers:**
- Versioning protocol: (1) make change, (2) test in Custom GPT, (3) document observed behavior, (4) tag if correct
- Commit message convention with behavioral framing
- Known-good reference outputs (one conceptual skill, one technical skill)
- Verification checklists per template

**Addresses:** Behavioral commit messages (table stakes from FEATURES.md), version tracking improvement
**Avoids:** Version without verification pitfall (from PITFALLS.md)
**Research flag:** Process definition—no technical research needed. Requires discipline adoption.

### Phase Ordering Rationale

- **Phase 1 before 2:** Templates can't reference format standards that don't exist yet. Contracts must be defined before templates can declare compliance.
- **Phase 2 before 3:** Anti-drift rules belong IN templates. Can't add them until templates are encapsulated and self-contained.
- **Phase 3 before 4:** Link formatting is one dimension of PARTS stability. Stabilize overall iteration behavior before focusing on specific output types.
- **Phase 5 before 6:** Can't verify behavior systematically without observation infrastructure. Logs enable verification protocol.
- **Phase 2 is the pivot:** Everything before Phase 2 is setup. Everything after Phase 2 depends on encapsulation being complete.

### Research Flags

**Phases needing deeper research during planning:**
- None. All patterns are well-established or directly derived from project constraints.

**Phases with standard patterns (skip research-phase):**
- Phase 1: JSON Schema is standard, schemas are straightforward
- Phase 3: Anti-drift pattern is prompt engineering fundamentals
- Phase 4: Link format problem and solution are both explicit
- Phase 5: Observation logging structure defined in research
- Phase 6: Versioning protocol is process definition, not technical

**Potential iteration areas:**
- Phase 2 (encapsulation): May require multiple attempts to find right contract abstraction
- Phase 3 (anti-drift): May need empirical testing to find effective checkpoint frequency

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Constrained by "no API access"—recommendations are direct consequences. Git, JSON Schema, observation logs are proven patterns for artifact management. |
| Features | HIGH | Derived from project documentation (CLAUDE.md validation invariants) and prompt engineering principles. Table stakes vs competitive distinction is clear. |
| Architecture | HIGH | Module pattern for templates is standard software engineering applied to prompts. Current problems (logic leakage, coupling) are observable in codebase. |
| Pitfalls | MEDIUM-HIGH | Top pitfalls are documented in project history or inferred from prompt engineering experience. Prevention strategies are untested but sound. |

**Overall confidence:** MEDIUM-HIGH

The recommendations are sound, but untested in this specific context. The hard constraint (no API, no automation) rules out many standard approaches, forcing creative solutions. All recommendations are logical consequences of constraints, but empirical validation will be needed during implementation.

### Gaps to Address

**Gap 1: Effective anti-drift techniques**
- Research identifies the problem (instruction drift) and suggests solutions (continuity checkpoints, format fingerprints), but optimal implementation is unknown.
- **How to handle:** Implement minimum viable version in Phase 3, iterate based on observation logs. Expect refinement.

**Gap 2: Handoff corruption detectability**
- Templates will have explicit contracts, but no automated validation to catch when GPT produces malformed summaries.
- **How to handle:** Phase 5 observation checklists must include "verify summary structure." Manual but systematic.

**Gap 3: Rule complexity budget**
- Research warns against template complexity explosion but doesn't define concrete limits.
- **How to handle:** During Phase 2 refactoring, count rules per template. Flag any template exceeding 10 top-level rules for simplification.

**Gap 4: Optimal PARTS size**
- Current max_numbered_items_per_part values may not be optimal for drift prevention.
- **How to handle:** Phase 3 should include experimentation: test 30, 40, 50 items per PART. Observation logs will reveal drift patterns.

## Sources

### Primary (HIGH confidence)
- Project documentation: `.planning/PROJECT.md`, `CLAUDE.md` — explicit system requirements, validation invariants, known issues
- Existing templates: `skill_tree_builder_template.json`, `skill_tree_to_content_tree_template.json`, `skill_to_practice_exercises_template.json`, `skill_to_evaluation_exam_template.json`, `custom_gpt_instructions.json` — direct observation of current architecture
- Custom GPT constraints: No API access, manual Knowledge file upload, observational testing only — hard constraints that shape all recommendations

### Secondary (MEDIUM confidence)
- JSON Schema best practices — training data knowledge (6-18 months stale but fundamentals stable)
- Git version control patterns — universal best practices, directly applicable
- Prompt engineering principles — training data knowledge of attention patterns, context windows, iterative output challenges

### Tertiary (LOW confidence, needs validation)
- ajv-cli version recommendations — training data may be outdated; verify latest stable version
- Specific JSON Schema Draft-07 features — confirm compatibility with ajv-cli before implementation
- ChatGPT UI link rendering requirements — inferred from project issues; should be verified by testing

### Limitations
- **WebSearch unavailable:** No external verification of current best practices in prompt template management (emerging field)
- **No automated testing possible:** All recommendations assume manual verification; can't leverage typical testing frameworks
- **Custom GPT behavior undocumented:** OpenAI doesn't publish Custom GPT internals; recommendations based on observed patterns

---
*Research completed: 2026-02-05*
*Ready for roadmap: yes*
