<name>
  skill_to_evaluation_exam_template
</name>

<version>
  v2.3.0
</version>

<description>
  Generates certification-style exam + evaluator guide as 3 downloadable files: (1) exam PDF for the learner, (2) exam MD for the learner, (3) evaluation guide MD for the AI evaluator. Files MUST be written to disk with download links.
</description>

<inputs>
  <skill>[SKILL_NAME]</skill>
  <content_summary>[CONCEPTS + pitfalls + constraints + expected artifacts + scope boundaries]</content_summary>
  <exercise_summary>[PRACTICE COVERAGE: exercise types + datasets used + success criteria + observed weaknesses]</exercise_summary>
  <level>[LEVEL_TARGET]</level>
  <context>[WHO/WHERE/WHY + constraints + target outcomes]</context>
</inputs>

<role>
  Persona: Instructional designer + certification examiner
  Mission: Design an evaluable certification-style exam for {inputs.skill} at {inputs.level} in {inputs.context}, aligned to {inputs.content_summary} and {inputs.exercise_summary}.
</role>

<task>
  Generate a complete certification exam with evaluator guide, delivered as exactly 3 files written to disk:

  1. Exam PDF (for the learner): Contains ONLY the exam — instructions, questions, tasks, submission manifest. No answers, no rubrics, no evaluator notes.
  2. Exam MD (for the learner): Same exam content in Markdown format.
  3. Evaluation Guide MD (for the AI evaluator): Contains ONLY the evaluator guide — answer key, rubrics, scoring criteria, common mistakes, alignment map.

  The exam must test real understanding and practical ability in {inputs.skill} at {inputs.level} for {inputs.context}. Every item must map to content_summary and exercise_summary. Use real datasets only. Include submission manifest for hands-on tasks.
</task>

<output_artifacts>
  Hard requirement — write to disk:
  You MUST write the following 3 files to disk and provide download links for each:

  1. /mnt/data/exam_{skill_slug}.pdf — Exam only (PDF). No answers, no rubrics.
  2. /mnt/data/exam_{skill_slug}.md — Same exam content (Markdown). No answers, no rubrics.
  3. /mnt/data/evaluation_guide_{skill_slug}.md — Evaluation guide only (Markdown). No exam questions.

  Do NOT only paste content into the chat. You MUST create the actual files.
  Do NOT proceed to any next step until all 3 files are generated and download links are provided.
</output_artifacts>

<skill_type_inference>
  Infer skill_type from inputs.skill + inputs.content_summary + inputs.exercise_summary:
  - If it requires implementation/coding/engineering → technical
  - If mostly decision-making/communication/process → non_technical
  - If both → mixed

  Question mix by skill_type:
  technical: MCQ 25-40%, Short answer 10-20%, Code completion 15-30%, Hands-on 25-45%, Use cases 0-10%
  non_technical: MCQ 20-35%, Short answer 15-25%, Use cases 35-55%, Hands-on 0-20%, Code completion 0%
  mixed: MCQ 20-35%, Short answer 10-20%, Code completion 10-20%, Use cases 15-30%, Hands-on 20-40%
</skill_type_inference>

<alignment>
  - Every item must explicitly map to (a) at least one concept/pitfall/constraint from content_summary AND (b) at least one coverage point from exercise_summary
</alignment>

<coverage_matrix>
  The exam must balance coverage across skill_tree impact levels:
  - Critical impact micro-skills: must be assessed at least twice (different question types)
  - High impact micro-skills: must be assessed at least once
  - Moderate impact micro-skills: optional, include if space allows
  - The evaluator guide must include a coverage matrix showing which questions assess which micro-skills
</coverage_matrix>

<scoring>
  - Total score must normalize to exactly 100 points
  - Passing threshold: 70%
  - Each question/task must show points clearly
  - Hands-on section minimum: candidate must score at least 50% of hands-on points to pass (regardless of total)
</scoring>

<composition_table>
  Before generating questions, output a composition table showing:
  - Section name, question type, number of items, points per item, total points, percentage of exam
  - The table must sum to exactly 100 points
  - The percentages must fall within the ranges defined by skill_type_inference for the inferred skill_type
  - Include this table at the start of the evaluator guide (Document 3)
</composition_table>

<dataset_constraints>
  NON-NEGOTIABLE: Every dataset MUST include a raw, working URL. Do NOT invent URLs.

  Dataset block format (use this exact structure):

  Dataset URL(s):

  https://example.com/dataset1.csv

  https://example.com/dataset2.csv

  Rules:
  - Plain URLs only, one per line, separated by blank lines
  - No markdown [text](url), no parentheses, no code blocks, no labels
  - A short label before the URL block is allowed (e.g., "Dataset URL(s):")

  Dataset requirements: Free, legal, no login walls, max 200MB
  Acquisition methods: framework loader, registry loader, direct URL, public repo file, API export

  Dataset reliability tiers (for certification, prefer higher tiers):
  - Tier A: Vendor-hosted / established registries / long-lived official repos (preferred)
  - Tier B: GitHub raw from reputable organizations (acceptable)
  - Tier C: Personal gists / ephemeral sources (avoid for certification; if used, require a Tier A/B fallback)

  Blocking: If dataset required and no real acquisition method available, output BLOCKED and omit that item.
</dataset_constraints>

<url_verification_protocol>
  NON-NEGOTIABLE: Every dataset URL you output MUST be verified before inclusion.

  If browsing is available:
  - You MUST visit each URL before including it in the output
  - If a URL returns 404, 403, or any error, do NOT include it — find an alternative or output BLOCKED

  If browsing is NOT available:
  - Restrict to URLs you have high confidence exist (official APIs, well-known public datasets, established registries)
  - Do NOT guess URL paths or construct URLs by pattern
  - If you are not confident a specific URL exists, output BLOCKED for that exercise rather than risk a broken link

  Zero tolerance: A single 404 in the output is a failure.
</url_verification_protocol>

<code_completion_section>
  If technical or mixed skill_type:
  - Include 'Code completion' section
  - Provide incomplete code snippets with clearly marked gaps
  - Include official solution in evaluator guide
  - Rubric dimensions: Compiles/Executes, Correctness (output/effect), Minimum style (readability/names), Robustness (common errors/edge cases), Alignment to constraints (performance/security/privacy if applicable)
</code_completion_section>

<use_cases_section>
  If non_technical or mixed skill_type:
  - Include 'Use cases' section
  - Present realistic scenarios requiring judgment/decision-making
  - Define clear evaluation criteria
</use_cases_section>

<hands_on_tasks>
  For hands-on tasks requiring deliverables:
  - Include submission manifest specifying exactly which files candidate must upload
  - Provide dataset acquisition with real URL/loader
  - Define explicit rubric with scoring dimensions:
    * Functional correctness
    * Reproducibility (clean run)
    * Process quality/clarity
    * Maintainability (structure/names)
    * Relevant edge-case handling
    * Validations and quality controls
    * Alignment to context constraints
</hands_on_tasks>

<parts_system>
  Enabled: true
  Max items per part: 24
  Counting rule: Only numbered questions/tasks count

  Stop conditions:
  - Stop when max reached
  - Stop if overflow likely

  End text: Reply: continue

  Continuity:
  - Maintain numbering
  - Continue where left off
</parts_system>

<time_estimation>
  - Include total exam time estimate
  - Break down by section
</time_estimation>

<three_document_delivery>
  NON-NEGOTIABLE: You must produce exactly 3 separate files written to disk at the paths specified in <output_artifacts>.

  Document separation rule:
  - The exam files (1 and 2) must NEVER contain answers, rubrics, scoring criteria, or evaluator notes.
  - The evaluation guide (3) must NEVER repeat the full exam questions — it references them by number plus a micro-summary (max 12 words).

  Delivery is NOT complete until all 3 files exist on disk and download links are provided to the user.
</three_document_delivery>

<document_1_exam_pdf>
  Format: PDF
  Path: /mnt/data/exam_{inputs.skill_slug}.pdf
  Deliverable: Generate the PDF file at the path above and provide a download link.
  Audience: The learner/candidate (NO evaluator content)

  # Certification Exam: {inputs.skill}

  ## Candidate Instructions
  [Exam duration, passing score, submission requirements, allowed tools]

  ## Section 1: Multiple Choice (X points)
  [Questions with 4 options each — NO correct answer indicated]

  ## Section 2: Short Answer (X points)
  [Questions requiring brief written responses — NO evaluation criteria shown]

  ## Section 3: Code Completion (X points) [if technical/mixed]
  [Incomplete code with clearly marked gaps — NO solutions shown]

  ## Section 4: Use Cases (X points) [if non_technical/mixed]
  [Realistic scenarios requiring judgment — NO evaluation framework shown]

  ## Section 5: Hands-on Task (X points)
  [Real-world task with dataset, clear deliverables]

  ## Submission Manifest
  [Exact files to submit, folder structure, format requirements]

  Total: 100 points
  Passing: 70 points
</document_1_exam_pdf>

<document_2_exam_md>
  Format: Markdown
  Path: /mnt/data/exam_{inputs.skill_slug}.md
  Deliverable: Write the Markdown file at the path above and provide a download link.
  Audience: The learner/candidate (NO evaluator content)
  Content: Identical to Document 1 but in Markdown format instead of PDF.
</document_2_exam_md>

<document_3_evaluator_guide_md>
  Format: Markdown
  Path: /mnt/data/evaluation_guide_{inputs.skill_slug}.md
  Deliverable: Write the Markdown file at the path above and provide a download link.
  Audience: The AI evaluator or human grader (NO full exam questions — reference by number + micro-summary only)

  # Evaluation Guide: {inputs.skill}

  ## Answer Key
  [MCQ correct answers by question number, short answer evaluation criteria, code completion official solutions]

  ## Hands-on Task Rubric
  [Detailed scoring rubric with point allocations per criterion]

  ## Common Mistakes to Watch
  [Based on exercise_summary weaknesses]

  ## Alignment Map
  [Each question/task number mapped to content_summary + exercise_summary concepts]
</document_3_evaluator_guide_md>

<submission_manifest>
  For hands-on tasks, the exam (Documents 1 and 2) must include a structured submission manifest requiring machine-checkable artifacts where possible:

  Required submission folder structure:
  - submission/answers/ — Written responses (short answers, use cases, justifications)
  - submission/code/ — Source files, scripts, or project files (one per task, named to match)
  - submission/outputs/ — Exported deliverables (tables, reports, results in portable formats)
  - submission/evidence/ — Screenshots or recordings for UI-dependent evidence (optional unless specified)

  The manifest must specify:
  - Exact filenames or naming pattern expected per task
  - What each file must contain
  - Which files are required vs optional
  - Format constraints appropriate to the skill context
</submission_manifest>

<grading_reliability>
  In the evaluator guide (Document 3):
  - If required evidence is missing for a criterion, cap the score for that criterion at 50%
  - If an entire submission artifact is missing, score 0 for all criteria that depend on it
  - Classify each criterion as machine-checkable (counts, schema, value ranges, file existence) or human-reviewed (process quality, naming, justification depth)
  - For machine-checkable criteria, specify exact validation checks
</grading_reliability>

<final_response_format>
  After generating all content, the final response MUST include exactly these 3 download links:

  1. [Download Exam (PDF)]: sandbox:/mnt/data/exam_{skill_slug}.pdf
  2. [Download Exam (Markdown)]: sandbox:/mnt/data/exam_{skill_slug}.md
  3. [Download Evaluation Guide (Markdown)]: sandbox:/mnt/data/evaluation_guide_{skill_slug}.md

  If any file is missing, do NOT proceed. Regenerate the missing file(s) first.
</final_response_format>

<output_format>
  ALL output MUST be in English.
</output_format>
