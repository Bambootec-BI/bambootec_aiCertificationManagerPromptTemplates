# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Purpose

This is the **AI Certifications Manager** — a prompt-orchestration system that chains parameterized JSON templates to generate skill-based certifications, learning paths, practice exercises, and evaluation guides. The system works via a Custom GPT that executes these templates in sequence.

**Core Workflow:**
1. **Skill Tree** generation (decomposition into learnable units)
2. **Content Plan** generation (sources, outcomes, time estimates)
3. **Practice Exercises** generation (dataset/artifact-grounded tasks)
4. **Exam + Evaluator Guide** generation
5. **Evaluation** of candidate submissions (rubric-based grading)

---

## File Structure

The repository contains 5 JSON template files that define the prompt orchestration:

| Current Filename | Canonical Name | Purpose |
|---|---|---|
| [skill_tree_builder_template.json](skill_tree_builder_template.json) | `skill_tree_builder.json` | Generates 2-level skill trees with impact weights |
| [skill_tree_to_content_tree_template.json](skill_tree_to_content_tree_template.json) | `skill_tree_to_content_tree.json` | Converts skill trees to learning content plans with sources |
| [skill_to_practice_exercises_template.json](skill_to_practice_exercises_template.json) | `skill_to_practice_exercises.json` | Creates dataset-grounded practice exercises |
| [skill_to_evaluation_exam_template.json](skill_to_evaluation_exam_template.json) | `skill_to_evaluation_exam.json` | Generates certification exams + evaluator guides |
| [custom_gpt_instructions.json](custom_gpt_instructions.json) | `certifications_manager_router.json` | Orchestrates the workflow (router/state machine) |

**Note:** Files should be renamed to canonical names; internal references must be updated accordingly.

---

## Template Architecture

### Design Principles

1. **Abstraction-Level Agnostic**: Templates work for both:
   - **Conceptual/mental-model skills** (tool-agnostic, evaluated via scenarios and artifacts)
   - **Technical implementation skills** (code completions, hands-on deliverables with datasets)

2. **Language Contract**:
   - **Instruction text** (inside JSON): English (for model performance)
   - **User-visible outputs**: Spanish (except code blocks and proper nouns)

3. **Real Sources Only**: No search queries or invented URLs
   - Every source must be a clickable URL to existing content

4. **Dataset Policy**: Only real, downloadable datasets
   - Framework built-in loaders (e.g., `sklearn.datasets`)
   - Registry loaders (OpenML, HuggingFace)
   - Direct download URLs (exact file links)
   - No synthetic data generation; no inline datasets

### Template Interconnections

Templates pass data forward through **summaries**:

```
skill_tree_builder
  ↓ (outputs: skill_tree)
skill_tree_to_content_tree
  ↓ (outputs: content_tree, content_summary)
skill_to_practice_exercises
  ↓ (outputs: exercise_tree, exercise_summary)
skill_to_evaluation_exam
  ↓ (outputs: exam, evaluation_guide, submission_requirements)
```

Each template receives inputs from previous steps and produces machine-usable summaries for the next step.

---

## Common Template Patterns

### Inputs Structure
Every template defines:
- `skill`: The skill name
- `level`: Target ability level (entry/intermediate/expert)
- `context`: WHO/WHERE/WHY + constraints + target outcomes
- Template-specific parameters (tools, platforms, language preferences)

### Output Requirements
All templates must produce:
- **Hierarchical structure** (numbered sections, maintained across PARTS)
- **Time estimates** (per item + cumulative total)
- **Spanish labels** (for all user-visible fields)
- **Sources** (clickable URLs)

### PARTS System
Templates support iterative output across multiple chat turns:
- `max_items_per_part`: Limits numbered items per response
- **Counting rule**: Only numbered items count (1., 1.1, 1.2...); bullets don't count
- **Continuity**: Numbering and format must remain consistent across PARTS
- **Running totals**: Each PART must include cumulative time estimate

---

## Critical Standards

### 1. Link Rendering Standard (ABSOLUTE)

**Problem**: Links must be reliably clickable in ChatGPT interface.

**Rule**: Use one of these formats:

**Preferred** (when UI supports preview):
```
https://www.kaggle.com/datasets/example
```
(URL alone on its own line, no bullets, no punctuation)

**Fallback** (maximum reliability per OpenAI):
```
[https://www.kaggle.com/datasets/example](https://www.kaggle.com/datasets/example)
```

**Never**:
- Put links inside code blocks
- Use search query placeholders
- Invent URLs

### 2. Validation Invariants

Each template must enforce:

**skill_tree_builder.json**:
- Impact weights sum to exactly 100
- Exactly 2 levels of hierarchy
- Action-oriented, assessable nodes
- No vague outcomes ("understand X" → "apply X to...")

**skill_tree_to_content_tree.json**:
- Every source is a real URL
- Total time computed and reported
- Minimum 80% English sources (when specified)

**skill_to_practice_exercises.json**:
- Dataset acquisition is real (URL or loader ID + steps)
- Explicit PASS/FAIL acceptance criteria (no code solutions)
- Evidence requirements specified
- Block exercises if no real dataset available

**skill_to_evaluation_exam.json**:
- Total score normalized to 100 points
- Submission manifest for complex tasks (required files, folder structure)
- Code completions only for technical/mixed skills
- Use cases for non-technical skills
- Rubrics with explicit scoring dimensions

### 3. Abstraction Profile Inference

Templates infer `skill_type` from inputs:
- **tecnico**: Requires implementation/coding/engineering → include "Completar código" sections
- **no_tecnico**: Decision-making/communication/process → include "Use cases" sections
- **mixto**: Both → include both with balanced weight

---

## Template Versioning

When modifying any template:

1. **Bump** `template_version` (use semantic versioning)
2. **Add** changelog entry explaining changes
3. **Update** validation invariants if schema changes
4. **Maintain** at least 2 examples:
   - 1 conceptual skill (tool-agnostic)
   - 1 technical skill (implementation-focused)

---

## Workflow State Machine (custom_gpt_instructions.json)

The orchestrator defines:

### State Artifacts
- `skill_tree`: Hierarchical skill decomposition
- `content_tree` + `content_summary`: Learning resources and coverage
- `exercise_tree` + `exercise_summary`: Practice tasks and datasets
- `exam` + `evaluation_guide`: Assessment materials
- `submission_requirements`: Required deliverables
- `grading_report`: Evaluation results

### Routing Rules
- **One step per turn**: Never output content from multiple steps in one message
- **Steps vs PARTS**: PARTS are iterations within a step, not new steps
- **Continuation**: User must explicitly request moving to next step
- **After completion**: Offer A/B choice menu (generate new certification OR evaluate submission)

### Formatting Policy
- **Primary format**: Markdown hierarchical lists
- **Indentation**: Spaces only (2 per level), no tabs
- **List markers**: `-` for all levels
- **Consistency**: Same structure/field names/ordering across all PARTS of a step

---

## Acceptance Tests

Before considering templates "done":

1. **Skill tree validation**:
   - Run with conceptual skill (e.g., "Mental model of Data Engineering")
   - Run with technical skill (e.g., "Python web scraping")
   - Verify: weights sum to 100, 2 levels, assessable nodes

2. **Content plan validation**:
   - Check: every "Fuente" is clickable URL
   - Check: no search queries present
   - Check: total time computed

3. **Exercise validation**:
   - Check: dataset acquisition method present
   - Check: no full solutions provided
   - Check: explicit PASS/FAIL criteria
   - Check: blocked exercises omitted if no dataset available

4. **Exam validation**:
   - Check: Spanish output labels
   - Check: total score = 100 points
   - Check: code completions present for technical skills
   - Check: submission manifest present for hands-on tasks

5. **End-to-end smoke tests**:
   - Run full workflow for tool-agnostic skill
   - Run full workflow for technical skill
   - Verify state transitions and summary handoffs

---

## Working with Templates

### Reading Templates
Templates are large JSON files with nested instruction structures. Key sections:

- `meta`: Version and notes
- `inputs`: Placeholder definitions and defaults
- `rules`: Hard constraints and processing logic
- `instruction`: The main prompt text
- `output`: Required structure and format
- `content_requirements`: Per-section/per-item requirements

### Modifying Templates

**Before changing**:
1. Read current version completely
2. Identify which section needs changes (inputs/rules/instruction/output)
3. Check dependencies on other templates

**During changes**:
1. Maintain JSON validity
2. Preserve existing field names unless renaming is intentional
3. Keep instruction text in English, output labels in Spanish
4. Update validation rules if schema changes

**After changes**:
1. Bump `template_version`
2. Add changelog entry
3. Test with both conceptual and technical examples
4. Verify PARTS continuity if output structure changed

### Common Modifications

**Adding new input parameter**:
```json
"inputs": {
  "new_parameter": "[DEFAULT_VALUE]",
  ...
}
```

**Adding validation rule**:
```json
"rules": {
  "new_rule": [
    "Clear constraint description",
    "Expected behavior",
    "Failure mode"
  ]
}
```

**Changing output structure**:
1. Update `output.required_structure`
2. Update `content_requirements` to match
3. Test PARTS continuity
4. Document in changelog

---

## Key Constraints

### Hard Requirements (Non-Negotiable)

1. **No fake sources**: Every URL must point to real, accessible content
2. **No search queries**: No "Consulta de búsqueda:" or placeholder text
3. **Real datasets only**: Framework loaders or direct download URLs; no synthetic generation
4. **Spanish user output**: All candidate/evaluator-facing text in Spanish
5. **One step per turn**: Router must not skip steps or confuse PARTS with steps
6. **Sources mandatory**: Step 2 (content plan) must always include "Fuentes" section

### Soft Preferences

1. **Prefer free resources**: Paid MOOCs allowed but de-prioritized and labeled "Pago"
2. **Prefer English sources**: Target 80%+ English when specified
3. **Prefer deep links**: No generic landing pages; link to specific sections
4. **Prefer no-code instructions**: Tool UI steps over code when possible

---

## Future Enhancements

Templates should support (not yet implemented):

1. **Router state validation**: Checkpoints between steps to verify summary quality
2. **Source verification**: Automated URL reachability checks
3. **Dataset verification**: Size/license/accessibility validation
4. **Schema evolution**: Version migration for older generated artifacts
5. **Multi-language support**: Parameterized output language beyond Spanish
6. **Quality scoring**: Automated template output quality metrics

---

## Notes for Claude Code

- Templates are **schema-driven prompts**, not executable code
- Changes require understanding of **prompt engineering** and **GPT behavior**
- Test changes by running through Custom GPT, not locally
- Keep requirements **testable** and **observable**
- Maintain **backward compatibility** unless explicitly breaking changes
- Document all assumptions and defaults explicitly in template JSON
