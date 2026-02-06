# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Repository Purpose

This is the **AI Certifications Manager** — a prompt-orchestration system that chains parameterized templates to generate skill-based certifications, learning paths, practice exercises, and evaluation guides. The system works via a Custom GPT that executes these templates in sequence.

**Core Workflow:**
1. **Skill Tree** generation (decomposition into learnable units)
2. **Content Plan** generation (sources, outcomes, time estimates)
3. **Practice Exercises** generation (dataset/artifact-grounded tasks)
4. **Exam + Evaluator Guide** generation
5. **Evaluation** of candidate submissions (rubric-based grading)

---

## File Structure

The repository contains **two formats** of templates:

### JSON Format (json_format/)
Pure JSON with proper object hierarchy for programmatic parsing and API consumption.

### XML/Markdown Format (xml_format/)
Markdown files with XML tags for Custom GPT prompt engineering (better instruction adherence).

| Template Name | Purpose | Inputs | Outputs |
|---|---|---|---|
| `skill_tree_builder_template` | Generates numbered 2-level skill trees (book-index style) with impact weights | skill, level, context | skill_tree (numbered hierarchical list) |
| `skill_tree_to_content_tree_template` | Converts skill trees to learning content plans with sources | skill, skill_tree, level, context | content_summary (+ full content tree to user) |
| `skill_to_practice_exercises_template` | Creates dataset-grounded practice exercises | skill, content_summary, level, context | exercise_summary (+ full exercise tree to user) |
| `skill_to_evaluation_exam_template` | Generates certification exams + evaluator guides as 3 separate files | skill, content_summary, exercise_summary, level, context | exam PDF (learner), exam MD (learner), evaluation_guide MD (evaluator) |
| `custom_gpt_instructions` | Orchestrates the workflow (router/state machine) | N/A | N/A |

**Note:** The Custom GPT references `.md` files from xml_format/ in its workflow steps.

---

## Template Architecture

### Design Principles

1. **Abstraction-Level Agnostic**: Templates work for both:
   - **Conceptual/mental-model skills** (tool-agnostic, evaluated via scenarios and artifacts)
   - **Technical implementation skills** (code completions, hands-on deliverables with datasets)

2. **Dual-Format Approach**:
   - **JSON format** (json_format/): Pure JSON structure with keys for programmatic parsing
   - **XML/Markdown format** (xml_format/): XML tags in Markdown files for prompt engineering
   - Both formats contain identical content, just different structures

3. **Language Contract**:
   - **Instruction text** (inside templates): English (for model performance)
   - **User-visible outputs**: Spanish (except code blocks and proper nouns)

4. **Real Sources Only**: No search queries or invented URLs
   - Every source must be a clickable URL to existing content
   - Sources are bare URLs only (no titles, no names, no descriptions)

5. **Video Source Priority** (content tree): At least 40% of sources must be video content
   - YouTube tutorials, playlists, conference talks, screencasts, live-coding sessions
   - Each node must have at least 1 video source; nodes with 4+ sources need at least 2
   - Prefer hands-on/demo videos over lecture-only formats

6. **Dataset Policy**: Only real, downloadable datasets
   - Framework built-in loaders (e.g., `sklearn.datasets`)
   - Registry loaders (OpenML, HuggingFace)
   - Direct download URLs (exact file links)
   - No synthetic data generation; no inline datasets

### Template Data Flow

Templates pass data forward based on workflow requirements:

```
Step 1: skill_tree_builder
  Inputs: skill, level, context
  Outputs to user: Numbered hierarchical skill tree (book-index style, no table)
  Outputs to next step: skill_tree (summary)

Step 2: skill_tree_to_content_tree
  Inputs: skill, skill_tree, level, context
  Outputs to user: Full content tree with sources
  Outputs to next step: content_summary

Step 3: skill_to_practice_exercises
  Inputs: skill, content_summary, level, context
  Outputs to user: Full exercise tree with datasets
  Outputs to next step: exercise_summary

Step 4: skill_to_evaluation_exam
  Inputs: skill, content_summary, exercise_summary, level, context
  Outputs to user: 3 downloadable files:
    - examen_{skill_slug}.pdf (exam only, for learner)
    - examen_{skill_slug}.md (exam only, for learner)
    - guia_evaluacion_{skill_slug}.md (evaluation guide only, for AI evaluator)
  Outputs to next step: None (end of workflow)
```

**Key Principle:** Each template only receives inputs available from previous steps. No configuration parameters - sensible defaults are hardcoded within templates.

---

## Common Template Patterns

### Inputs Structure
Each template has simplified inputs based on data flow:
- **Core inputs**: skill, level, context (present in all templates)
- **Summary inputs**: skill_tree, content_summary, exercise_summary (passed from previous steps)
- **No configuration parameters**: All settings (time_unit, language preferences, density, etc.) are hardcoded to sensible defaults

### Output Requirements
All templates produce two types of outputs:

1. **User-facing outputs** (full, detailed):
   - **Hierarchical structure** (numbered sections, maintained across PARTS)
   - **Time estimates** (per item + cumulative total)
   - **Spanish labels** (for all user-visible fields)
   - **Sources** (bare URLs only, nothing else)

2. **Summary outputs** (for next step):
   - Condensed versions capturing essential information
   - Generated at the end of each template execution
   - Self-contained in each template specification

### PARTS System
Templates support iterative output across multiple chat turns:
- Hardcoded limits per template (e.g., 40 items for content, 28 for exercises, 24 for exam)
- **Counting rule**: Only numbered items count (1., 1.1, 1.2...); bullets don't count
- **Continuity**: Numbering and format must remain consistent across PARTS
- **Running totals**: Each PART must include cumulative time estimate

---

## Critical Standards

### 1. Link Rendering Standard (ABSOLUTE)

**Problem**: Links must be reliably clickable in ChatGPT interface.

**Rule**: Bare URLs only, separated by blank lines:

```
https://www.kaggle.com/datasets/example

https://example.com/another-resource
```

**Never**:
- Add titles, names, or descriptions before URLs
- Put links inside code blocks
- Use markdown [text](url) syntax
- Wrap URLs in parentheses, quotes, or code blocks
- Add "Nota:", "Query:", "Extraer:", "Extract:", or language tags before/after sources
- Add any explanatory text after the URL
- Use search query placeholders
- Invent URLs
- Put parenthetical explanations on same line as URL
- Add labels like "(Book)", "(Microsoft Learn)", "(Video)", etc.

### 2. Validation Invariants

Each template must enforce:

**skill_tree_builder**:
- Impact weights sum to exactly 100
- Exactly 2 levels of hierarchy
- Action-oriented, assessable nodes
- No vague outcomes ("understand X" → "apply X to...")

**skill_tree_to_content_tree**:
- Every source is a real URL
- At least 40% of all sources must be video content (YouTube, conference talks, screencasts)
- Each node with 2–6 sources must have at least 1 video; nodes with 4+ sources need at least 2 videos
- Total time computed and reported
- Minimum 80% English sources (hardcoded default)

**skill_to_practice_exercises**:
- Dataset acquisition is real (URL or loader ID + steps)
- Explicit PASS/FAIL acceptance criteria (no code solutions)
- Evidence requirements specified
- Block exercises if no real dataset available

**skill_to_evaluation_exam**:
- Exactly 3 separate downloadable documents produced (exam PDF, exam MD, evaluation guide MD)
- Exam documents contain NO answers, rubrics, or evaluator notes
- Evaluation guide contains NO exam questions (references by number only)
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

1. **Bump** `version` in meta section (use semantic versioning)
2. **Add** changelog entry or note explaining changes
3. **Update** validation invariants if schema changes
4. **Update both formats**: json_format/ AND xml_format/ versions
5. **Maintain** at least 2 examples:
   - 1 conceptual skill (tool-agnostic)
   - 1 technical skill (implementation-focused)

---

## Workflow State Machine (custom_gpt_instructions)

The orchestrator defines:

### State Artifacts
- `skill_tree`: Hierarchical skill decomposition
- `content_summary`: Learning resources and coverage (condensed)
- `exercise_summary`: Practice tasks and datasets (condensed)
- `exam` (PDF + MD) + `evaluation_guide` (MD): Assessment materials as 3 separate files

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
   - Check: every "Fuente" is a bare URL only (no titles or descriptions)
   - Check: no search queries present
   - Check: at least 40% of sources are video URLs (YouTube, conference talks, etc.)
   - Check: every node has at least 1 video source; nodes with 4+ sources have at least 2
   - Check: total time computed
   - Check: URLs are separated by blank lines

3. **Exercise validation**:
   - Check: dataset acquisition method present
   - Check: no full solutions provided
   - Check: explicit PASS/FAIL criteria
   - Check: blocked exercises omitted if no dataset available

4. **Exam validation**:
   - Check: exactly 3 separate downloadable files produced (exam PDF, exam MD, evaluation guide MD)
   - Check: exam files contain NO answers, rubrics, or evaluator notes
   - Check: evaluation guide references questions by number only, does NOT repeat exam content
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

### Dual Format Maintenance

When modifying templates, **always update both formats**:

1. **json_format/*.json**: Pure JSON with object keys
   - Use proper JSON structure: `{"key": "value"}`, `{"key": ["item1", "item2"]}`
   - No XML tags in strings

2. **xml_format/*.md**: Markdown with XML tags
   - Use XML tags for structure: `<key>value</key>`, `<items><item>...</item></items>`
   - No XML declarations (`<?xml version...?>`)

### Reading Templates

Key sections in templates:

- `meta`: Version and notes
- `inputs`: Only data available from workflow (no config params)
- `prompt`: The main instruction structure
  - JSON format: nested objects with keys
  - XML format: nested tags
- `output_format`: Required structure
- Content requirements: Per-section/per-item specifications

### Modifying Templates

**Before changing**:
1. Read current version completely (both formats if modifying)
2. Identify which section needs changes
3. Check dependencies on other templates
4. Verify input availability per workflow data flow

**During changes**:
1. Maintain format validity (JSON or XML/Markdown)
2. Update BOTH formats identically
3. Keep instruction text in English, output labels in Spanish
4. Don't add configuration parameters - hardcode sensible defaults instead
5. Ensure inputs match what's available from previous workflow steps

**After changes**:
1. Bump `version` in meta section
2. Add note explaining changes
3. Test with both conceptual and technical examples
4. Verify PARTS continuity if output structure changed
5. Commit with clear message explaining changes

### Common Modifications

**Simplifying inputs** (already done - maintain this):
- Only include inputs available from previous steps per workflow
- Remove configuration parameters
- Hardcode defaults within template logic

**Adding output requirements**:
- Update output_format section
- Update content_requirements to match
- Test PARTS continuity
- Document in version notes

**Changing URL/source format** (current standard):
```
https://example.com/resource

https://example.com/another-resource
```

Each source is just a bare URL on its own line with blank lines for separation. No titles, no names, no descriptions.

---

## Key Constraints

### Hard Requirements (Non-Negotiable)

1. **No fake sources**: Every URL must point to real, accessible content
2. **No search queries**: No "Consulta de búsqueda:" or placeholder text
3. **Real datasets only**: Framework loaders or direct download URLs; no synthetic generation
4. **Spanish user output**: All candidate/evaluator-facing text in Spanish
5. **One step per turn**: Router must not skip steps or confuse PARTS with steps
6. **Sources mandatory**: Step 2 (content plan) must always include "Fuentes" section
7. **Simplified inputs**: Only use data available from previous workflow steps
8. **Dual format maintenance**: Always update both json_format/ and xml_format/ versions

### Soft Preferences

1. **Prefer free resources**: Paid MOOCs allowed but de-prioritized and labeled "Pago"
2. **Prefer English sources**: Target 80%+ English (hardcoded default)
3. **Prefer deep links**: No generic landing pages; link to specific content
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
- Two formats serve different purposes:
  - **JSON**: Programmatic parsing, API consumption
  - **XML/Markdown**: Custom GPT prompt engineering (better instruction adherence)
- Changes require understanding of **prompt engineering** and **GPT behavior**
- Test changes by running through Custom GPT, not locally
- Always update **both formats** when making changes
- Keep requirements **testable** and **observable**
- Maintain **backward compatibility** unless explicitly breaking changes
- Follow the **workflow data flow**: each template only uses inputs from previous steps
- Document all assumptions and defaults explicitly in templates
