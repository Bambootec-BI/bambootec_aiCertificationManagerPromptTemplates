# AI Certifications Manager - Template Improvement System

## What This Is

A version-controlled prompt template system for a Custom GPT that generates skill-based certifications, learning paths, practice exercises, and evaluation guides. The system uses 5 JSON templates uploaded as Knowledge files to a Custom GPT, which orchestrates them in sequence to produce high-quality educational content where users learn real-world skills effectively.

## Core Value

Templates produce certifications where users effectively learn real-world work skills, with reliable formatting that keeps the chat interface clean and usable.

## Requirements

### Validated

- ✓ 5 JSON templates exist (skill_tree_builder, skill_tree_to_content, skill_to_practice_exercises, skill_to_evaluation_exam, custom_gpt_instructions) — existing
- ✓ Templates uploaded as Knowledge files to Custom GPT — existing
- ✓ Workflow orchestration through custom_gpt_instructions.json — existing
- ✓ Spanish output labels, English instruction text — existing
- ✓ PARTS system for iterative output — existing
- ✓ Link rendering standard defined in CLAUDE.md — existing

### Active

- [ ] Template encapsulation: Each iterative template specifies its own summary generation (don't leak to router)
- [ ] Self-contained templates: Each knows inputs, outputs, and handoff format like software modules
- [ ] Link formatting consistency: Raw URLs in ChatGPT's default clickable/previewable format across all PARTS
- [ ] Format consistency enforcement: Structure/field names/ordering stay stable during iteration
- [ ] Systematic versioning: Commits describe expected behavior changes, tags mark stable versions
- [ ] Change tracking: When fixes don't work, next commit notes "previous didn't work, trying X"
- [ ] Surgical edits: Changes show exact before/after with consistency review of related sections
- [ ] Observation logging: Template changes driven by Custom GPT behavior deviations

### Out of Scope

- Automated test suite — OpenAI API doesn't support Custom GPT management via code yet
- Multi-language output support — Spanish output is the current requirement
- Real-time GPT behavior monitoring — Manual observation is the current workflow

## Context

**Current System:**
- 5 JSON templates define a certification generation workflow
- Templates are uploaded to a Custom GPT as Knowledge files
- Custom GPT orchestrates: skill tree → content plan → exercises → exam → evaluation
- System works for both conceptual (tool-agnostic) and technical skills
- Output must be in Spanish (except code blocks and proper nouns)
- Templates use a PARTS system for long outputs split across multiple chat turns

**Key Pain Points:**
- Link formatting drifts during iteration (URLs come as plain text, titles only, search queries, or non-clickable formats instead of raw clickable URLs)
- Format consistency breaks mid-PARTS (field names change, ordering shifts, structure varies)
- Summary generation logic currently lives in router instructions instead of being encapsulated in each template

**Workflow:**
1. Run certification generation in Custom GPT
2. Observe where output deviates from template specifications
3. Make targeted template changes with explicit before/after
4. Review all related sections for consistency
5. Commit with "what this change should fix"
6. If behavior doesn't improve, document why and try alternative approach
7. Tag versions when behavior is stable and reliable

**Quality Criteria:**
- Users effectively learn real-world work skills from generated certifications
- Custom GPT reliably follows template rules
- Chat interface remains clean and usable (proper formatting, clickable links)

## Constraints

- **Deployment**: Changes require manual copy-paste into Custom GPT Knowledge files — OpenAI doesn't provide API for Custom GPT management
- **Testing**: Manual verification only — no automated test suite possible with current OpenAI limitations
- **Language Contract**: Instruction text must stay in English (for model performance), output labels must be Spanish
- **Template Format**: JSON schema with specific sections (meta, inputs, rules, instruction, output)
- **Git Workflow**: Main branch represents production templates uploaded to Custom GPT

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Templates as Knowledge files in Custom GPT | OpenAI's current architecture for Custom GPTs | — Pending |
| Manual testing only | No API access for Custom GPT automation | — Pending |
| Spanish output / English instructions | Balance between model performance and user needs | — Pending |
| Link rendering standard in CLAUDE.md | Documented pattern but not enforced by templates themselves | ⚠️ Revisit — needs encapsulation in templates |
| Summary generation in router | Initial implementation approach | ⚠️ Revisit — should be encapsulated in each template |

---
*Last updated: 2026-02-05 after initialization*
