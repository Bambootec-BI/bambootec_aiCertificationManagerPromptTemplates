<template>
  <meta>
    <version>v1.5.0</version>
    <name>skill_tree_to_content_tree</name>
    <description>English prompt for best model performance; Spanish output labels. Simplified organic hierarchy. NON-NEGOTIABLE: sources must be clickable URLs to real content (no search queries). Strong preference for English sources and deep, micro-skill-specific links (no generic docs/landing pages). NON-NEGOTIABLE: every node must include at least 1 video source, with allocation based on node type (procedural 2+, conceptual 1-2, reference 1). Prefer free/practical sources; paid MOOCs may be suggested but de-prioritized and clearly labeled.</description>
  </meta>

  <inputs>
    <skill>[SKILL_NAME]</skill>
    <skill_tree>PASTE_THE_HIERARCHICAL_SKILL_TREE_HERE__KEEP_NAMES_VERBATIM</skill_tree>
    <level>[LEVEL_TARGET]</level>
    <context>[WHO/WHERE/WHY + constraints + target outcomes]</context>
  </inputs>

  <prompt>
    <language_policy>
Translate user input into English. Think in English. Only translate to Spanish for the output.

Instruction language: English
Output language: Spanish
Exceptions: proper names, commonly used names, code blocks
    </language_policy>

    <role>
Persona: Instructional designer and domain practitioner
Mission: Turn the provided skill tree into a learner-friendly content tree with curated sources so a learner can reach {level} in {context} for {skill}.
    </role>

    <task>
Using the provided skill tree, produce a simplified actionable content tree in organic Markdown while preserving hierarchy (Section -> Micro-skills). Output in PARTS per the parts rules. Every node must include: expected learning outcomes, coverage details, sources with real URLs highly aligned to the micro-skill (preferably in English), and time estimates. All output labels and user-facing text must be in Spanish.
    </task>

    <constraints>
      <alignment>
- Preserve every section name and micro-skill name verbatim (exact spelling/case)
- Every content item must reference the micro-skill(s) it supports using the exact names from the input skill tree
      </alignment>

      <no_full_teaching_content>
- Do NOT write full lesson text or long explanations
- Do provide compact headings and curated learning resources; keep it easy to read
      </no_full_teaching_content>

      <sources_non_negotiable>
NON-NEGOTIABLE: Every source MUST be a bare URL only, nothing else.

URL requirements:
- Output ONLY the URL as plain text on its own line: https://example.com
- Add blank lines before and after each URL for separation
- Do NOT include titles, names, or descriptions
- Do NOT use markdown link syntax [text](url)
- Do NOT wrap URLs in parentheses, quotes, code blocks, or any special formatting
- Output as regular text, NOT in code blocks
- Do NOT add labels like 'Nota:', 'Query:', or language tags before URLs
- Do NOT invent URLs - only real, accessible resources
- Sources must be immediately usable for learning (YouTube videos/playlists, targeted documentation pages, practical articles, labs, notebooks). Every node must include at least 1 video source (see video_source_policy).

Prohibited patterns:
- Source titles, names, or labels before URLs
- Parenthetical additions like (Book), (Microsoft Learn), (Video), etc.
- "Nota:", "Query:", "Extraer:", "Extract:", or language tags before/after URLs
- Code blocks (mathematica, python, etc.) around URLs
- Any explanatory text after the URL
- Parenthetical explanations on the same line as URL
- Em dashes or descriptions before/after URLs
      </sources_non_negotiable>

      <video_source_policy>
NON-NEGOTIABLE: Every node MUST include at least 1 video source. Video allocation depends on node type:

Node type inference (infer from micro-skill name and context):
- Procedural/visual (UI walkthroughs, tool demos, debugging, data manipulation): 2+ video sources
- Conceptual/analytical (mental models, architecture, design decisions): 1-2 video sources
- Reference/definitional (terminology, conventions, syntax rules): 1 video source

Global floor: at least 30% of all sources across the content tree should be videos.

Video source types (in priority order):
- YouTube tutorials and walkthroughs (10-20 min preferred over 1-hour lectures)
- YouTube playlists covering the section topic
- Conference talks and recorded workshops
- Screencasts and live-coding sessions
- Platform-hosted video courses (free first; paid only when clearly superior)

Rules:
- Video sources follow the same bare-URL-only format rule — just the video URL on its own line
- Video sources must be tightly aligned to the micro-skill — no generic "intro to X" playlists unless the node is itself introductory
- Prefer shorter, denser videos that demonstrate hands-on workflows over long lecture-only formats
- Video sources count toward the 2–6 source range per node (they do not add extra slots)
      </video_source_policy>

      <source_quality_and_cost>
- Prioritize the most practical, high-signal resources for fast learning in this {context}
- Prefer free resources first when quality is comparable
- Paid MOOCs/courses are allowed but MUST be de-prioritized; include them only when they are clearly superior or uniquely comprehensive
- Avoid low-quality SEO content; prefer official docs, reputable educators, and established publishers
- Prefer sources with hands-on steps, examples, and demonstrated workflows over purely conceptual overviews
- Among equally relevant sources, prefer video content over text-only content
      </source_quality_and_cost>

      <ordering>
- Order sections and micro-skills by learning progression AND by impact for success in {context}
- Use Impact (%) if present as a signal, but respect prerequisites
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
      </parts_system>

      <output_labels_language>
IMPORTANT: All output point labels must be in Spanish.
Do not print machine tags like [SECTION] or node_type blocks; use natural headings + compact bullets.
      </output_labels_language>

      <remove_difficult_points>
Do NOT include these labels/sections in the output: 'Practice', 'Validation', 'Failure modes', 'Artifacts'.
      </remove_difficult_points>

      <time_estimation>
- Every section and micro-skill MUST include estimated time as an integer range in minutes
- Use [60-180] for sections and [20-60] for micro-skills as baselines and adjust based on complexity and prerequisites
- Time must be plausible for a learner at {level} in {context}
      </time_estimation>

      <time_total>
- You MUST compute and report a total estimated study time range for the entire content plan
- Because output is split into PARTS, include a running total at the end of EVERY PART showing cumulative estimated total time
- When you finish the last item of the skill tree, also include final estimated total time
- Total time must equal the sum of the time ranges you provided for all numbered section + micro-skill items
      </time_total>

      <source_language_preference>
NON-NEGOTIABLE: Prefer sources in English. Aim for at least 80% of sources per PART to be in English.

If (and only if) a suitable English source cannot be found for a specific node, you may use a non-English source (Spanish allowed), but keep it minimal.
      </source_language_preference>

      <no_generic_sources>
NON-NEGOTIABLE: Do NOT use generic/umbrella sources (homepages, category pages, or broad 'getting started' pages) unless the page itself is narrowly focused on the exact micro-skill.

- Avoid linking to general product documentation entry points (e.g., documentation home, overview pages). Use deep links to the specific page/section that teaches the exact micro-skill
- Every source must be tightly aligned to the node: the title/section/video must clearly match the micro-skill name or its immediate subtopics
      </no_generic_sources>
    </constraints>

    <content_requirements>
      <for_each_section>
- Write a 1-line section goal grounded in {context}
- Include 'Expected learning outcomes' as 3–6 bullets (observable)
- Include 'Coverage (details)' with: (a) the micro-skills verbatim, and (b) 3–6 bullets of what the section covers at a higher level (sub-areas/topics)
- Include 'Sources' with 2–6 items. Each source: just the URL on its own line with blank lines for separation. Nothing else.
- Include 'Estimated time' as a range in {inputs.time_unit}
      </for_each_section>

      <for_each_micro_skill>
- Include 'Expected learning outcomes' as 2–5 bullets (concrete)
- Include 'Coverage (details)' as 3–7 short headings/bullets (topics only; no long explanations)
- Include 'Sources' with 2–6 items. Each source: just the URL on its own line with blank lines for separation. Nothing else.
- Include 'Estimated time' as a range in {inputs.time_unit}
- Optionally include 'Prerequisites' ONLY when necessary to prevent confusion; otherwise omit it
      </for_each_micro_skill>
    </content_requirements>

    <input>
Skill tree: {skill_tree}
    </input>

    <output_format>
      <required_structure>
1. **&lt;Section Title (verbatim)&gt;** — &lt;1-line objective&gt;
   - Expected learning outcomes: &lt;3–6 bullets&gt;
   - Coverage (details):
     - Micro-skills: &lt;list of micro-skills verbatim as bullets&gt;
     - Scope: &lt;3–6 bullets of sub-areas/topics&gt;
   - Sources: &lt;2–6 items. Each source: just the URL on its own line with blank lines for separation. Nothing else. Output as regular markdown text, NOT in code blocks.&gt;
   - Estimated time: &lt;integer range in minutes&gt;
   1.1 **&lt;Micro-skill (verbatim)&gt;**
       - Expected learning outcomes: &lt;2–5 bullets&gt;
       - Coverage (details): &lt;3–7 bullets/headings&gt;
       - Sources: &lt;2–6 items. Each source: just the URL on its own line with blank lines for separation. Nothing else. Output as regular markdown text, NOT in code blocks.&gt;
       - Estimated time: &lt;integer range in minutes&gt;

At the end of each PART: Cumulative estimated total time: &lt;integer range in minutes&gt;
Upon completing everything: Final estimated total time: &lt;integer range in minutes&gt;

AFTER ALL ITERATIONS COMPLETE: Generate a content_summary section including: concepts covered, pitfalls mentioned in sources, constraints from context, terminology introduced, and scope boundaries. This summary will be consumed by skill_to_practice_exercises_template.md.
      </required_structure>

      <optional>
Coverage map (compact markdown table):
Columns: Micro-skill (verbatim) | Content IDs (e.g., 1.1, 1.2) | Primary sources (short)
Note: Only include if it fits without harming readability.
      </optional>
    </output_format>
  </prompt>
</template>
