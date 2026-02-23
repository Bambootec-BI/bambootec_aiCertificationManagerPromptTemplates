<name>
  skill_tree_to_content_tree
</name>

<version>
  v1.9.0
</version>
<inputs>
  <skill>[SKILL_NAME]</skill>
  <skill_tree>PASTE_THE_HIERARCHICAL_SKILL_TREE_HERE__KEEP_NAMES_VERBATIM</skill_tree>
  <level>[LEVEL_TARGET]</level>
  <context>[WHO/WHERE/WHY + constraints + target outcomes]</context>
</inputs>

<command>
  Using the provided skill tree, produce a simplified actionable content tree in organic Markdown while preserving hierarchy (Section -> Micro-skills). Output in PARTS per the parts rules. Every node must include: Expected learning outcomes, Coverage (details), Sources, and Estimated time.
</command>

<non_negotiable>
  - Sources must be clickable URLs to real content (no search queries). Strong preference for English sources and deep, micro-skill-specific links (no generic docs/landing pages).
  - Every node must include at least 1 video source, with allocation based on node type (procedural 2+, conceptual 1-2, reference 1). Prefer free/practical sources; paid MOOCs may be suggested but de-prioritized and clearly labeled. This video source generation must be maintained in all the PARTS. This must not stop in the first PART and then not be applied.
  - Video recency is absolutely enforced: always select the most recent video available, but NEVER sacrifice relevance for recency. Between two equally relevant videos, always pick the newer one. Between a recent but off-topic video and an older but highly relevant one, pick the relevant one. Recency matters as much as possible without compromising relevance.
</non_negotiable>

<role>
  Persona: Instructional designer and domain practitioner
  Mission: Turn the provided skill tree into a learner-friendly content tree with curated sources so a learner can reach {level} in {context} for {skill}.
</role>

<alignment>
  - Preserve every section name and micro-skill name verbatim (exact spelling/case)
  - Every content item must reference the micro-skill(s) it supports using the exact names from the input skill tree
</alignment>

<no_full_teaching_content>
  - Do NOT write full lesson text or long explanations
  - Do provide compact headings and curated learning resources; keep it easy to read
</no_full_teaching_content>


<source_quality_and_cost>
  - Prioritize the most practical, high-signal resources for fast learning in this {inputs.context}
  - Prefer free resources first when quality is comparable
  - Paid MOOCs/courses are allowed but MUST be de-prioritized; include them only when they are clearly superior or uniquely comprehensive
  - Avoid low-quality SEO content; prefer official docs, reputable educators, and established publishers
  - Prefer sources with hands-on steps, examples, and demonstrated workflows over purely conceptual overviews
  - Among equally relevant sources, prefer video content over text-only content
</source_quality_and_cost>

<ordering>
  - Order sections and micro-skills by learning progression AND by impact for success in {inputs.context}
  - Use Impact level (Low, Moderate, High, Very High, Critical) if present as a signal, but respect prerequisites
</ordering>

<parts_system>
  Enabled: true
  Max numbered items per part: 40
  Counting rule: Only numbered items count toward the limit (1., 1.1, 1.2, ...). Bullets under a numbered item do NOT count as separate items.

  Stop conditions:
  - Stop immediately when you reach the limit
  - Stop immediately if you suspect the response might exceed the chat window

  End text: Reply: continue

  Continuity:
  - Maintain consistent hierarchical numbering across PARTS
  - Continue exactly where you left off; do not repeat previously output items
  - Once all skill tree items are covered, STOP. Do NOT suggest or create optional extra PARTs beyond the input skill tree.
</parts_system>

<time_estimation>
  - Every section and micro-skill MUST include estimated time as an integer range in minutes
  - Use [60-180] for sections and [20-60] for micro-skills as baselines and adjust based on complexity and prerequisites
  - Time must be plausible for a learner at {inputs.level} in {inputs.context}
</time_estimation>

<time_total>
  - You MUST compute and report a total estimated study time range for the entire content plan
  - Because output is split into PARTS, include a running total at the end of EVERY PART showing cumulative estimated total time
  - When you finish the last item of the skill tree, also include final estimated total time
  - Total time must equal the sum of the time ranges you provided for all numbered section + micro-skill items
</time_total>

<source_language_preference>
  Prefer sources in English. If a suitable English source cannot be found for a specific node, you may use a non-English source, but keep it minimal.
</source_language_preference>

<content_requirements>
  <for_each_section>
    - Write a 1-line section goal grounded in {inputs.context}
    - Include 'Expected learning outcomes' as 3–6 bullets (observable)
    - Include 'Coverage (details)' with: (a) the micro-skills verbatim, and (b) 3–6 bullets of what the section covers at a higher level (sub-areas/topics)
    - Include 'Estimated time' as a range in {inputs.time_unit}
  </for_each_section>

  <for_each_micro_skill>
    - Include 'Expected learning outcomes' as 2–5 bullets (concrete)
    - Include 'Coverage (details)' as 3–7 short headings/bullets (topics only; no long explanations)
    - Include 'Sources' with 2–6 items (must include video sources per video_source_non_negotiable).
    - Include 'Estimated time' as a range in {inputs.time_unit}
    - Optionally include 'Prerequisites' ONLY when necessary to prevent confusion; otherwise omit it
  </for_each_micro_skill>
</content_requirements>

<constraint_priority>
  If constraints conflict, prioritize in this exact order:
  1. Relevance to micro-skill
  2. Technical correctness
  3. Practical applicability
  4. Source quality
  5. Recency
  6. Language preference
  7. Cost preference

  Never sacrifice relevance or correctness for recency.
</constraint_priority>

<anti_hallucination_protocol>
  - Only include sources you are confident are real and accessible.
  - If a suitable recent video cannot be found, explicitly state that and provide the best available relevant video.
  - Do NOT fabricate publication dates.
  - If browsing is not available, do not claim recency validation; instead select widely known authoritative sources.
</anti_hallucination_protocol>

<pre_output_validation>
  Before outputting each PART, verify:
  - Cumulative time is recalculated and correct
  - Numbering continuity is maintained from the previous PART
  - Every node includes all required fields (learning outcomes, coverage, sources, time)
  - Video quota is met for every node (procedural 2+, conceptual 1-2, reference 1)
</pre_output_validation>

<output_format>
  ALL output MUST be in English.

</output_format>
