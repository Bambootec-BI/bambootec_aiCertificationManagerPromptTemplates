# Architecture Research

**Domain:** JSON Prompt Template Systems for LLM Orchestration
**Researched:** 2026-02-05
**Confidence:** HIGH (analysis-based; patterns derived from software engineering principles applied to current codebase)

## Standard Architecture

### System Overview

```
+-----------------------------------------------------------------------+
|                        TEMPLATE LAYER                                   |
+-----------------------------------------------------------------------+
|  +----------------+  +----------------+  +----------------+            |
|  | skill_tree     |  | content_tree   |  | exercises      |            |
|  | _builder       |->| _template      |->| _template      |--+         |
|  +----------------+  +----------------+  +----------------+  |         |
|         |                   |                   |            |         |
|         v                   v                   v            v         |
|    [skill_tree]      [content_tree]      [exercise_tree]    |         |
|    [skill_summary]   [content_summary]   [exercise_summary] |         |
|                                                              |         |
|                                          +----------------+  |         |
|                                          | exam_template  |<-+         |
|                                          +----------------+            |
+-----------------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------------+
|                        CONTRACT LAYER                                   |
+-----------------------------------------------------------------------+
|  Handoff Schemas: skill_summary_schema, content_summary_schema,        |
|                   exercise_summary_schema                               |
|  Format Standards: link_format, list_format, time_format               |
+-----------------------------------------------------------------------+
                              |
                              v
+-----------------------------------------------------------------------+
|                        ROUTER LAYER                                     |
+-----------------------------------------------------------------------+
|  +------------------------------------------------------------------+  |
|  | certifications_manager_router                                     |  |
|  |   - Workflow state machine (steps, not PARTS)                    |  |
|  |   - Step sequencing only (no content generation logic)           |  |
|  |   - Input collection and validation                              |  |
|  |   - Format standards enforcement reference                        |  |
|  +------------------------------------------------------------------+  |
+-----------------------------------------------------------------------+
```

### Component Responsibilities

| Component | Responsibility | Communicates With |
|-----------|----------------|-------------------|
| **skill_tree_builder** | Generate skill decomposition with impact weights | Router (receives inputs), content_tree (produces skill_summary) |
| **skill_tree_to_content** | Transform skills to learning content with sources | skill_tree_builder (consumes skill_tree), exercises (produces content_summary) |
| **skill_to_practice_exercises** | Generate dataset-grounded practice tasks | content_tree (consumes content_summary), exam (produces exercise_summary) |
| **skill_to_evaluation_exam** | Create certification exam + evaluator guide | exercises (consumes exercise_summary), Router (final output) |
| **Router** | Step sequencing, input collection, format enforcement | All templates (orchestration only) |
| **Contract Layer** | Define handoff schemas and format standards | All templates (shared vocabulary) |

## Recommended Architecture: Self-Contained Templates

### Current State Problems

**Problem 1: Router contains logic that belongs in templates**
```
CURRENT:
  Router Step 2: "... At the end, generate a content summary."
  Router Step 3: "... Reuse parameters; for content, use content_summary. Create an exercise_summary."

PROBLEM: Summary generation rules are in router, not templates.
         Templates don't know how to produce their own handoff output.
```

**Problem 2: Format rules are duplicated**
```
CURRENT:
  Router: formatting_policy.indentation (spaces only, 2 per level)
  Router: formatting_policy.list_style (- markers)
  skill_tree_to_content: output_labels_language (Spanish)
  skill_to_practice_exercises: rules.language (Spanish)

PROBLEM: Same rules stated differently in multiple places.
         Changes require updating multiple files.
         Drift happens when one file is updated and others aren't.
```

**Problem 3: No explicit handoff contracts**
```
CURRENT:
  skill_tree_to_content.inputs.skill_tree_source: "PASTE_THE_HIERARCHICAL_SKILL_TREE_HERE"
  skill_to_practice_exercises.inputs.context_summary: "[SUMMARY_FROM_PREVIOUS_STEP: concepts covered, pitfalls...]"

PROBLEM: Contracts are implicit (described in placeholder text).
         No validation that upstream output matches downstream expectations.
         Summaries can drift from expected schema.
```

### Target State: Module Pattern for Templates

Each template should be a **self-contained module** with:

```
+------------------------------------------------------------------+
|                      TEMPLATE MODULE                               |
+------------------------------------------------------------------+
| 1. INPUT CONTRACT                                                  |
|    - Required inputs with types/schemas                           |
|    - Optional inputs with defaults                                |
|    - Upstream dependency declaration (which template produces it) |
+------------------------------------------------------------------+
| 2. PROCESSING RULES                                               |
|    - Template-specific logic                                      |
|    - PARTS behavior (iteration rules)                             |
|    - Validation rules                                             |
+------------------------------------------------------------------+
| 3. OUTPUT CONTRACT                                                |
|    - Primary output schema                                        |
|    - Summary output schema (handoff to next template)             |
|    - Format specifications                                        |
+------------------------------------------------------------------+
| 4. FORMAT STANDARDS (reference, not definition)                   |
|    - Reference to shared format definitions                       |
|    - Template-specific overrides if needed                        |
+------------------------------------------------------------------+
```

## Architectural Patterns

### Pattern 1: Explicit Input/Output Contracts

**What:** Define machine-readable schemas for inputs and outputs at template boundaries.

**When to use:** Always for templates that chain to other templates.

**Trade-offs:**
- (+) Prevents drift: clear expectations documented
- (+) Enables validation: can check if upstream output matches downstream input
- (+) Self-documenting: reader knows exactly what flows between templates
- (-) Requires maintenance: schema must be updated when requirements change

**Example:**
```json
{
  "input_contract": {
    "required": {
      "skill": {
        "type": "string",
        "description": "Skill name",
        "source": "user_input"
      },
      "content_summary": {
        "type": "object",
        "description": "Summary from skill_tree_to_content template",
        "source": "skill_tree_to_content.output.summary",
        "schema": {
          "concepts_covered": "array<string>",
          "pitfalls_identified": "array<string>",
          "time_total_minutes": "integer_range",
          "source_count": "integer",
          "english_source_ratio": "float"
        }
      }
    }
  },
  "output_contract": {
    "primary": {
      "type": "exercise_tree",
      "format": "markdown_es_labels"
    },
    "summary": {
      "type": "exercise_summary",
      "description": "Handoff to skill_to_evaluation_exam",
      "schema": {
        "exercises_by_section": "array<section_exercise_count>",
        "datasets_used": "array<dataset_reference>",
        "acceptance_criteria_patterns": "array<string>",
        "observed_difficulty_areas": "array<string>"
      }
    }
  }
}
```

### Pattern 2: Summary Generation Encapsulation

**What:** Each template defines HOW to generate its own summary output, not the router.

**When to use:** All templates that produce summaries consumed by downstream templates.

**Trade-offs:**
- (+) Single source of truth: template owns its output definition
- (+) Testable: can verify template produces valid summary
- (+) Decoupled: router only sequences, doesn't know internal logic
- (-) Larger templates: summary rules add to template size

**Example:**
```json
{
  "summary_generation": {
    "trigger": "After completing final PART of output",
    "format": "structured_json_block",
    "rules": [
      "Summary MUST be generated before asking to continue to next step",
      "Summary MUST include all schema fields",
      "Summary MUST be compact (fit in one message)"
    ],
    "schema": {
      "concepts_covered": {
        "derive_from": "all section.micro_skills names",
        "format": "array<string>"
      },
      "pitfalls_identified": {
        "derive_from": "any 'Prerequisitos' or 'Nota' sections mentioning common mistakes",
        "format": "array<string>"
      },
      "time_total_minutes": {
        "derive_from": "final 'Tiempo total estimado (final)'",
        "format": "integer_range"
      }
    }
  }
}
```

### Pattern 3: Format Standards as Shared Reference

**What:** Define format rules once in a shared location; templates reference rather than redefine.

**When to use:** For formatting rules that apply across all templates (indentation, list style, language).

**Trade-offs:**
- (+) DRY: changes propagate automatically
- (+) Consistency: all templates use identical rules
- (+) Auditability: one place to check for format standards
- (-) Indirection: reader must look in two places

**Implementation options:**

**Option A: Inline reference (recommended for Custom GPT)**
```json
{
  "format_standards": {
    "reference": "See certifications_manager_router.format_standards",
    "applies": ["indentation", "list_style", "language_policy", "link_format"],
    "template_overrides": null
  }
}
```

**Option B: Duplicated with consistency marker**
```json
{
  "format_standards": {
    "version": "1.0",
    "consistency_check": "MUST match router.format_standards.version",
    "rules": {
      "indentation": {"spaces_only": true, "spaces_per_level": 2},
      "list_style": {"all_levels": "-"},
      "link_format": {"preferred": "raw_url_own_line", "fallback": "markdown_link_with_visible_url"}
    }
  }
}
```

### Pattern 4: PARTS Behavior Encapsulation

**What:** Each template defines its own iteration behavior (when to split, how to continue).

**When to use:** All templates that produce output too long for single message.

**Trade-offs:**
- (+) Template autonomy: template knows its content best
- (+) Predictable: iteration rules don't change based on router
- (-) Potential inconsistency: different templates might have different rules

**Example:**
```json
{
  "parts_behavior": {
    "enabled": true,
    "max_numbered_items_per_part": 40,
    "counting_rule": "Only numbered items (1., 1.1, 1.2...) count. Bullets do NOT count.",
    "stop_conditions": [
      "Immediately when max reached",
      "Early if response might exceed chat window"
    ],
    "continuation": {
      "end_text": "Reply: continue",
      "resume_rules": [
        "Maintain numbering across PARTS",
        "Continue exactly where left off",
        "Do not repeat items",
        "Keep identical format (field names, ordering, structure)"
      ]
    },
    "final_part": {
      "must_include": ["summary_generation", "time_total_final"],
      "must_not_include": ["continuation_prompt"]
    }
  }
}
```

## Data Flow

### Request Flow

```
[User provides skill, level, context]
    |
    v
[Router] --> Validate inputs --> Route to skill_tree_builder
    |
    v
[skill_tree_builder]
    |
    +--> [skill_tree output] --> stored
    +--> [skill_summary] --> handoff
    |
    v
[Router] --> User confirms --> Route to skill_tree_to_content
    |
    v
[skill_tree_to_content] <-- consumes skill_tree + skill_summary
    |
    +--> [content_tree output] --> stored (across PARTS)
    +--> [content_summary] --> handoff
    |
    v
[Router] --> User confirms --> Route to skill_to_practice_exercises
    |
    v
[skill_to_practice_exercises] <-- consumes content_summary
    |
    +--> [exercise_tree output] --> stored (across PARTS)
    +--> [exercise_summary] --> handoff
    |
    v
[Router] --> User confirms --> Route to skill_to_evaluation_exam
    |
    v
[skill_to_evaluation_exam] <-- consumes content_summary + exercise_summary
    |
    +--> [exam + evaluation_guide output] --> final
```

### State Management

```
[Conversation State]
    |
    +--> inputs: {skill, level, context, preferred_tools, ...}
    +--> artifacts:
    |       skill_tree: [generated in step 1]
    |       content_tree: [generated in step 2, PARTS 1-N]
    |       exercise_tree: [generated in step 3, PARTS 1-N]
    |       exam: [generated in step 4]
    |
    +--> summaries:
    |       skill_summary: [produced by skill_tree_builder]
    |       content_summary: [produced by skill_tree_to_content]
    |       exercise_summary: [produced by skill_to_practice_exercises]
    |
    +--> current_step: [1|2|3|4|5]
    +--> current_part: [only relevant within step 2/3/4]
```

### Key Data Flows

1. **skill_tree --> content_tree:** Template receives full skill hierarchy verbatim; must preserve names exactly.

2. **content_summary --> exercises:** Compact summary with concepts, pitfalls, constraints; exercises derive tasks from this.

3. **content_summary + exercise_summary --> exam:** Exam draws from both to ensure coverage alignment.

## Recommended Project Structure

```
templates/
├── skill_tree_builder.json           # Step 1: Skill decomposition
├── skill_tree_to_content.json        # Step 2: Content plan with sources
├── skill_to_practice_exercises.json  # Step 3: Practice exercises
├── skill_to_evaluation_exam.json     # Step 4: Certification exam
├── certifications_manager_router.json # Orchestrator/state machine
│
├── contracts/                        # Shared contract definitions
│   ├── skill_summary.schema.json     # What skill_tree_builder outputs
│   ├── content_summary.schema.json   # What content_tree outputs
│   ├── exercise_summary.schema.json  # What exercises outputs
│   └── format_standards.json         # Shared formatting rules
│
└── examples/                         # Validation examples
    ├── conceptual_skill/             # Tool-agnostic skill example
    │   ├── skill_tree.md
    │   ├── content_tree.md
    │   ├── exercises.md
    │   └── exam.md
    └── technical_skill/              # Implementation skill example
        ├── skill_tree.md
        ├── content_tree.md
        ├── exercises.md
        └── exam.md
```

### Structure Rationale

- **templates/:** Core template files, one per workflow step
- **contracts/:** Explicit schemas for template boundaries; enables validation and documentation
- **examples/:** Known-good outputs for both skill types; serves as regression baseline

**Note:** Custom GPT Knowledge files only support uploading individual files, not directories. The `contracts/` and `examples/` folders are for development reference. For deployment:
- Contract schemas should be embedded in each template's `input_contract`/`output_contract` sections
- Format standards should be in the router with templates referencing them

## Anti-Patterns

### Anti-Pattern 1: Logic Leakage to Router

**What people do:** Put content generation rules (like "generate a summary") in the router instead of the template.

**Why it's wrong:**
- Router becomes coupled to template internals
- Template changes require router changes
- Summary format can drift if router and template disagree
- Violates separation of concerns

**Do this instead:**
- Templates define their own summary generation rules
- Router only sequences: "proceed to next step when current step complete"
- Router checks completion, doesn't define what completion means

### Anti-Pattern 2: Implicit Contracts

**What people do:** Use placeholder text like `[SUMMARY_FROM_PREVIOUS_STEP: concepts covered, pitfalls...]` to describe expected input.

**Why it's wrong:**
- No validation possible
- Easy to forget required fields
- Upstream template might produce different structure
- Debugging requires reading two templates

**Do this instead:**
- Define explicit `input_contract` with schema
- Define explicit `output_contract.summary` with schema
- Use consistent field names across chain
- Document which template produces each summary

### Anti-Pattern 3: Duplicated Format Rules

**What people do:** Copy formatting rules (indentation, list style, language) into each template.

**Why it's wrong:**
- Changes require updating all templates
- Easy to miss one, causing inconsistency
- Harder to audit for drift
- Increases template size

**Do this instead:**
- Define format standards once (in router or separate file)
- Templates reference standards, don't redefine
- Use version markers to detect drift
- Apply "single source of truth" principle

### Anti-Pattern 4: PARTS Logic in Router

**What people do:** Router defines iteration rules like "split output into PARTS of N items."

**Why it's wrong:**
- Different templates have different optimal PART sizes
- Router can't know template content complexity
- Breaks encapsulation
- Makes templates dependent on router for basic function

**Do this instead:**
- Each template defines its own `parts_behavior`
- Template knows its content density and complexity
- Router only knows "this step may have multiple PARTS"
- Router's job: sequence steps, not manage PARTS

## Refactoring Order

Based on dependency analysis, recommended implementation order:

### Phase 1: Contract Infrastructure (Foundation)

**Work:**
1. Define `format_standards.json` (single source of truth for formatting)
2. Define summary schemas: `skill_summary.schema.json`, `content_summary.schema.json`, `exercise_summary.schema.json`
3. Update router to reference `format_standards` instead of defining inline

**Rationale:**
- Contracts are the foundation for all other changes
- Format standards must exist before templates can reference them
- Low risk: doesn't change template behavior yet

### Phase 2: Template Encapsulation (Core Fix)

**Work:**
1. Add `output_contract.summary` to `skill_tree_builder.json`
   - Define what summary it produces
   - Add summary generation rules to template

2. Add `input_contract` and `output_contract.summary` to `skill_tree_to_content.json`
   - Define expected input (skill_tree + skill_summary)
   - Define what content_summary it produces
   - Move "generate content summary" from router to template

3. Add `input_contract` and `output_contract.summary` to `skill_to_practice_exercises.json`
   - Define expected input (content_summary)
   - Define what exercise_summary it produces
   - Move "create exercise_summary" from router to template

4. Add `input_contract` to `skill_to_evaluation_exam.json`
   - Define expected inputs (content_summary + exercise_summary)
   - No output summary needed (terminal template)

**Rationale:**
- Each template becomes self-contained
- Router can be simplified to pure sequencing
- Changes can be validated against contracts

**Dependencies:** Requires Phase 1 (contracts exist to reference)

### Phase 3: Router Simplification

**Work:**
1. Remove content generation instructions from router steps
2. Remove summary generation instructions from router steps
3. Keep only: step sequencing, input collection, completion checks, format standard reference

**Before (current):**
```json
"steps": [
  "... At the end, generate a content summary. Then ask whether to continue.",
  "... Reuse parameters; for content, use content_summary. Create an exercise_summary."
]
```

**After:**
```json
"steps": [
  "Execute skill_tree_to_content template. Template will produce content_tree and content_summary. Ask whether to continue.",
  "Execute skill_to_practice_exercises template with content_summary. Template will produce exercise_tree and exercise_summary."
]
```

**Rationale:**
- Router becomes pure orchestrator
- Templates own their behavior completely
- Easier to maintain and debug

**Dependencies:** Requires Phase 2 (templates have encapsulated logic)

### Phase 4: Format Standard Enforcement

**Work:**
1. Add format reference to each template
2. Add consistency checks (version markers)
3. Consolidate link formatting rules (currently scattered)

**Rationale:**
- Prevents drift by making format standards explicit and versioned
- Enables detection of inconsistencies

**Dependencies:** Requires Phase 1 (format standards exist)

### Phase 5: Validation Examples

**Work:**
1. Run full workflow for conceptual skill, capture outputs
2. Run full workflow for technical skill, capture outputs
3. Document as known-good baselines
4. Create checklist for manual validation

**Rationale:**
- Provides regression baseline
- Documents expected behavior concretely
- Enables future automated validation if API access becomes available

**Dependencies:** Requires Phases 2-4 (templates stabilized)

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 5 templates (current) | Single router, inline contracts sufficient |
| 10-20 templates | Separate contract files, consider template categories |
| 20+ templates | Template registry, automated contract validation |

### Scaling Priorities

1. **First bottleneck:** Manual consistency checking across templates
   - Fix: Explicit contracts with version markers
   - Fix: Automated schema validation (when tooling allows)

2. **Second bottleneck:** Router complexity with many steps
   - Fix: Sub-workflows (router calls sub-router)
   - Fix: Template categories with shared behavior

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Custom GPT (OpenAI) | Knowledge file upload | Manual; no API for management |
| Source URLs | Template references | Must be real, clickable URLs |
| Dataset registries | Loader instructions | OpenML, HuggingFace, direct URLs |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| skill_tree_builder <-> skill_tree_to_content | skill_tree + skill_summary | skill_tree is full output; summary is compact handoff |
| skill_tree_to_content <-> exercises | content_summary | No full content_tree; only summary |
| exercises <-> exam | exercise_summary | Plus content_summary (exam needs both) |
| All templates <-> Router | Step completion signals | Router doesn't access internal template state |

## Drift Prevention Mechanisms

### Structural Patterns

1. **Contract Versioning:**
   ```json
   {
     "input_contract": {
       "version": "1.0",
       "expects_from": "skill_tree_to_content@1.7"
     }
   }
   ```
   - Version mismatch = explicit drift detection

2. **Format Standard Reference:**
   ```json
   {
     "format_standards": {
       "reference": "certifications_manager_router.format_standards@1.0",
       "verified_compatible": "2026-02-05"
     }
   }
   ```
   - Templates declare which format version they implement

3. **Summary Schema Enforcement:**
   ```json
   {
     "summary_generation": {
       "schema": { ... },
       "validation_rule": "All schema fields MUST be present in generated summary"
     }
   }
   ```
   - Template can't produce incomplete summary

### Process Patterns

1. **Change Protocol:**
   - Any change to contract schema bumps version
   - All downstream templates must acknowledge new version
   - Router references format_standards version

2. **Consistency Review:**
   - When modifying any template, check all templates for:
     - Format standard reference version
     - Input contract expectations
     - Output contract compatibility

3. **Observation Log:**
   - Document GPT behavior deviations with specific examples
   - Link deviations to template sections
   - Track whether fixes work (commit message pattern)

## Sources

- Analysis of existing templates in `/Users/bambootec/Documents/02_bambootec/04_repos/bambootec_aiCertificationsManager/`
- Software engineering modularity principles (encapsulation, single responsibility, explicit contracts)
- CLAUDE.md documentation in repository
- PROJECT.md problem statement and requirements

---
*Architecture research for: JSON Prompt Template Systems*
*Researched: 2026-02-05*
