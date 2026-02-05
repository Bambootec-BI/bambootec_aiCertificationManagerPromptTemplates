# Roadmap: AI Certifications Manager Template Improvement

## Overview

This roadmap transforms the current template system from loosely-coupled JSON files with logic leakage into a maintainable architecture with explicit contracts, anti-drift rules, and behavioral versioning. The journey progresses from foundational contracts (schemas, format standards) through template encapsulation (fixing architecture violations), format stability (PARTS reliability), observation infrastructure (systematic testing), to behavioral versioning (change discipline).

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Contract Infrastructure** - Schemas and format standards foundation
- [ ] **Phase 2: Template Encapsulation** - Self-contained templates with explicit contracts
- [ ] **Phase 3: Anti-Drift Rules** - PARTS stability and format consistency
- [ ] **Phase 4: Observation Infrastructure** - Systematic testing and logging
- [ ] **Phase 5: Behavioral Versioning** - Change discipline and verification protocol

## Phase Details

### Phase 1: Contract Infrastructure
**Goal**: Establish the foundational schemas and format standards that all subsequent template work references
**Depends on**: Nothing (first phase)
**Requirements**: SCH-01, SCH-02, SCH-03, TMPL-03
**Success Criteria** (what must be TRUE):
  1. JSON Schema exists for each of the 5 templates that validates required sections and field types
  2. Running `ajv validate` on any template catches structural errors before upload
  3. format_standards.json exists with single definitions for indentation, list style, language policy
  4. Version markers in schemas detect when handoff formats between templates drift
**Plans**: TBD

Plans:
- [ ] 01-01: TBD
- [ ] 01-02: TBD

### Phase 2: Template Encapsulation
**Goal**: Each template is a self-contained module with explicit input/output contracts; summary generation moves from router into templates
**Depends on**: Phase 1 (needs format standards to reference)
**Requirements**: TMPL-01, TMPL-02, TMPL-04, TMPL-05
**Success Criteria** (what must be TRUE):
  1. Each template has input_contract and output_contract sections defining what it receives and produces
  2. Summary generation rules live in the template that produces the summary (not in router)
  3. Router references templates by explicit name at each step (no implicit selection)
  4. PARTS iteration rules (max items, continuation logic) are embedded in each iterative template
  5. Templates can theoretically run standalone with mock inputs (no router dependency for logic)
**Plans**: TBD

Plans:
- [ ] 02-01: TBD
- [ ] 02-02: TBD
- [ ] 02-03: TBD

### Phase 3: Anti-Drift Rules
**Goal**: PARTS iteration produces consistent output across all turns; format and link rendering remain stable
**Depends on**: Phase 2 (anti-drift rules belong in encapsulated templates)
**Requirements**: FMT-01, FMT-02, FMT-03, FMT-04
**Success Criteria** (what must be TRUE):
  1. Single canonical link format (raw URLs on own line) renders as clickable preview in ChatGPT
  2. Continuity checkpoints at PART boundaries echo exact field names from previous PART
  3. Negative examples in templates explicitly show what NOT to do (search queries, plain titles, markdown links, field name changes)
  4. Format fingerprint at end of each PART makes drift immediately visible if next PART deviates
**Plans**: TBD

Plans:
- [ ] 03-01: TBD
- [ ] 03-02: TBD

### Phase 4: Observation Infrastructure
**Goal**: Template changes are driven by systematic observation logs with explicit checklists per template
**Depends on**: Phase 3 (templates must be stable enough to test systematically)
**Requirements**: OBS-01, OBS-02, OBS-03
**Success Criteria** (what must be TRUE):
  1. OBSERVATIONS.md template exists with structured format (date, template, version, expected vs observed, classification)
  2. Per-template validation checklists exist derived from template rules (what to check for each template)
  3. Unenforceable rules have explicit failure modes (BLOCK markers when GPT cannot comply)
  4. Observation entries link to commits that address them (observation -> commit -> verification)
**Plans**: TBD

Plans:
- [ ] 04-01: TBD
- [ ] 04-02: TBD

### Phase 5: Behavioral Versioning
**Goal**: Version tags reflect verified behavior; commits describe expected behavior changes; reference outputs enable regression detection
**Depends on**: Phase 4 (needs observation infrastructure to verify behavior)
**Requirements**: VER-01, VER-02, VER-03, VER-04, OBS-04
**Success Criteria** (what must be TRUE):
  1. Commits use behavioral message format describing what GPT behavior should change (not JSON structure change)
  2. Template changes include before/after documentation showing exact lines changed
  3. Tags are applied only after manual verification in Custom GPT confirms expected behavior
  4. Verification logs in commit messages document what was tested and observed
  5. Known-good reference outputs exist for one conceptual skill and one technical skill workflow
**Plans**: TBD

Plans:
- [ ] 05-01: TBD
- [ ] 05-02: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Contract Infrastructure | 0/2 | Not started | - |
| 2. Template Encapsulation | 0/3 | Not started | - |
| 3. Anti-Drift Rules | 0/2 | Not started | - |
| 4. Observation Infrastructure | 0/2 | Not started | - |
| 5. Behavioral Versioning | 0/2 | Not started | - |

---
*Roadmap created: 2025-02-05*
*Total v1 requirements: 17*
*Coverage: 17/17 mapped*
