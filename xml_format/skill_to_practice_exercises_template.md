<template>
  <meta>
    <version>v1.1.0</version>
    <name>skill_to_practice_exercises.real_datasets_only.fully_agnostic.eslabels.parts</name>
    <description>Generates practice exercises for ANY skill, ANY level, ANY context using XML-structured prompts for better instruction adherence.</description>
    <parameters_required_minimal>
      <parameter>skill</parameter>
      <parameter>content_summary</parameter>
      <parameter>level</parameter>
      <parameter>context</parameter>
    </parameters_required_minimal>
  </meta>

  <inputs>
    <skill>[SKILL_NAME]</skill>
    <content_summary>[SUMMARY_FROM_PREVIOUS_STEP: concepts covered, pitfalls, constraints, expected artifacts, terminology, scope boundaries]</content_summary>
    <level>[LEVEL_TARGET]</level>
    <context>[WHO/WHERE/WHY + constraints + target outcomes]</context>
  </inputs>

  <prompt>
    <language_policy>
Translate user input into English. Think in English. Only translate to Spanish for the output.
Instruction language: English | Output language: Spanish
Exceptions: proper names, commonly used names, code blocks
    </language_policy>

    <role>
Persona: Instructional designer + practitioner
Mission: Create dataset-grounded practice exercises for {inputs.skill} at {inputs.level} in {inputs.context}, aligned to {inputs.content_summary}.
    </role>

    <task>
Generate an iterative practice exercise hierarchy for {inputs.skill} based solely on inputs.level, inputs.context, and inputs.content_summary. Every exercise must use real datasets only with immediate acquisition (exact URL or exact dataset id/name + loader steps). No search queries. No invented URLs. No inline datasets. No test code; acceptance tests must be directives/checklists with PASS/FAIL thresholds derived from inputs. Output in PARTS with stable numbering and running totals.
    </task>

    <constraints>
      <agnosticity_guards>
- Do NOT assume any specific domain, industry, data columns, schemas, KPIs, or file formats unless they are implied by inputs.content_summary or inputs.context
- Do NOT assume any specific tools unless clearly implied by inputs.context
- Do NOT assume any specific level requirements; interpret level ONLY from inputs.level and the constraints/pitfalls in inputs.content_summary
- Do NOT include any dataset suggestions unless you can provide a real acquisition method (exact URL or exact dataset id/name + loader steps)
- Never include search queries or placeholders such as 'look up'/'Search query'
      </agnosticity_guards>

      <dataset_constraints>
NON-NEGOTIABLE: Every dataset MUST include a raw, working URL.

URL requirements for Dataset (acquisition) section:
- URLs must be bare, clickable links on their own lines
- Format: Just the plain URL with blank lines around it

https://example.com/dataset.csv

- NEVER wrap dataset URLs in code blocks (```csv, ```mathematica, ```python, etc.)
- NEVER use markdown [text](url) syntax for dataset URLs
- NEVER wrap URLs in parentheses, quotes, or any formatting
- NEVER add "Dataset A", "Dataset B", "Dataset C" labels before URLs
- NEVER invent URLs - only real, accessible resources

Prohibited patterns in Dataset (acquisition) section:
- Code blocks around URLs: ```css\nhttps://url\n``` ❌
- Code blocks around descriptions: ```mathematica\n- Dataset X - file.csv\n``` ❌
- Titles/labels before URLs: "Dataset A — orders.csv (CSV)" ❌
- Language tags before URLs: "Nota:", "Query:" ❌
- Parenthetical explanations on same line as URL ❌

Correct format for Dataset (acquisition):
Just output the bare URL separated by blank lines:

https://raw.githubusercontent.com/example/data/main/file.csv

Dataset requirements:
- Free, legal to use, avoid login walls
- Max 200MB download target
- Real acquisition method: framework loader, registry loader (OpenML/HuggingFace), direct download URL, public repo file URL, or API bulk export URL

Blocking behavior: If you cannot provide at least ONE real acquisition method (exact URL OR standard loader with exact dataset id/name), output BLOCKED and omit the exercise.
      </dataset_constraints>

      <code_policy>
Code allowed ONLY in 'Dataset (acquisition)' section.
Limit: Only minimal dataset load/download + save-to-disk (or tool steps). No solution code, no test code.
Prefer no-code when possible.
      </code_policy>

      <use_content_summary>
- For each exercise, explicitly tie the exercise to at least one item from content_summary (concept OR pitfall OR constraint OR expected artifact)
- Use content_summary to define: what success looks like, which mistakes to catch, what outputs are expected, and which checks matter
      </use_content_summary>

      <acceptance_tests_no_code>
- Do NOT provide code for tests
- Provide 'Acceptance tests (how to test)' as a checklist with explicit PASS/FAIL criteria and concrete thresholds
- Thresholds must come from content_summary/context when available; otherwise use generic minimums (e.g., file exists; schema non-empty; counts coherent; rerun yields same counts)
      </acceptance_tests_no_code>

      <parts_system>
Enabled: true
Max numbered items per part: 28
Counting rule: Only numbered items count (1., 1.1, 1.1.1...). Bullets do NOT count.

Stop conditions:
- Stop immediately when the max is reached
- Stop early if overflow is likely

End text: Reply: continue

Continuity:
- Maintain numbering across PARTS
- Continue exactly where you left off; do not repeat items
      </parts_system>

      <time_total>
- Every numbered node MUST include estimated time as an integer range in minutes
- End of EVERY PART: show cumulative estimated total time
- Final PART: also include final estimated total time
      </time_total>
    </constraints>

    <output_format>
Format: markdown with Spanish labels

Structure:
PART N
1. **&lt;Section&gt;** — Practical objective: &lt;1 line based on inputs.context + inputs.content_summary&gt;
   - Micro-skills: &lt;list&gt;
   - Estimated time: &lt;range&gt;
   1.1 **&lt;Micro-skill&gt;** — Practice approach: &lt;1 line based on inputs.content_summary&gt;
       - Estimated time: &lt;range&gt;
       1.1.1 **&lt;Exercise: title&gt;**
             - Statement: ...
             - Verifiable objective: ... (what must be true at the end, unambiguous)
             - Dataset (acquisition): ... (title, raw URL separated by blank lines, note; or loader with exact dataset id/name)
             - Prerequisites: ... (only what's necessary)
             - Setup (copy/paste): ... (concrete steps, no assumptions)
             - Tasks: ... (concrete actions, aligned to content_summary)
             - Execution command: ... (1 command or 1 main action in the tool)
             - Expected output: ... (exact paths/format/contract)
             - Expected file tree: ... (exact paths, if applicable)
             - Definitions: ... (define ambiguous terms used in exercise)
             - Acceptance tests (how to test) — Pass/Fail: ... (checklist + explicit thresholds; no code)
             - Evidence: ... (what to show/attach exactly)
             - Hints (optional): ... (no complete solutions)
             - Estimated time: ...

At the end of PART: Cumulative estimated total time: X–Y minutes
If content remaining: Reply: continue

AFTER ALL ITERATIONS COMPLETE: Generate an exercise_summary section including: exercise types created, datasets used, success criteria employed, observed weaknesses or gaps that should be addressed in evaluation. This summary will be consumed by skill_to_evaluation_exam_template.json.
    </output_format>
  </prompt>
</template>
