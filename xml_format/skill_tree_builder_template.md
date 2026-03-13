<name>
  skill_tree_builder
</name>

<version>
  v2.3.0
</version>

<inputs>
  <skill>Skill: the skill name and some specification if required</skill>
  <level>Ability Level: level of proficiency with the skill</level>
  <context>Context: where/how the skill is used in real work</context>
</inputs>

<command>
  Build a learning skill tree for {inputs.skill} at {inputs.level} for {inputs.context}.

  Audience: practitioners at {inputs.level} level — people who can independently deliver production-ready work in {inputs.context}, handling the core workflow end-to-end with correct, maintainable results.
  Style: textbook table of contents (didactic, descriptive chapter titles that teach the reader what the chapter covers + short skill titles).
</command>

<critical_commands>
  Order MUST follow a learning progression (what to learn first → later). Impact is a categorical level (Low, Moderate, High, Very High, Critical) — it does not affect ordering.
</critical_commands>

<non_negotiable>  
  - Each concrete skill MUST include an Impact level (one of: Low, Moderate, High, Very High, Critical), but Impact MUST NOT affect ordering
  - Two levels: Level 1 = Chapter titles (### headings), Level 2 = Subtitles (conceptual foundation, example, and skills as numbered lines)
  - For chapters 2+: the first subtitle MUST be the chapter's conceptual foundation, the second MUST be a concrete example of that conceptual foundation, followed by the actionable skills
  - Chapter 1 (Conceptual Overview) is the exception: it already IS the global conceptual foundation, so it has no nested conceptual foundation subtitle — it contains an example subtitle and descriptive subtitles directly
  - Each chapter MUST contain at least two actionable skills (in addition to the conceptual foundation and example subtitles for chapters 2+)
  - Never use acronyms — always spell out the full words (e.g., "Artificial Intelligence" not "AI")
  - Maintain terminology consistency across the entire tree: once a governing term is chosen from the skill name, reuse that same term throughout the tree instead of alternating with near-synonyms unless a genuinely different concept is intended
  - Chose one term to represent and concept and reuse such term every time you want to refer to that concept
  - Do not force a fixed subtitle count; include only the number of subtitles needed to fully explain the chapter for the requested level
  - If a chapter title or subtitle mentions multiple concepts, each concept MUST be made explicit through separate subtitles rather than being bundled implicitly
  - The final chapter MUST be a grouped chapter titled "Advanced and Related Topics for {inputs.skill}"
  - Skill titles must be short, beginner-friendly, easily understandable and action-oriented
  - Each skill must include representative 'how to do it' examples in parentheses (examples only, not extra skills)
  - Keep skill count within a practical range for the requested level (e.g., 15–35), unless the context clearly demands fewer
  - FIRST CHAPTER MUST be "Conceptual Foundations of {inputs.skill}" with learning order 1, containing foundational understanding before practical skills
</non_negotiable>

<chapter_structure>
  <purpose>
    Every chapter frames its content with a conceptual foundation and a concrete example before listing actionable or explanatory subtitles. This helps learners understand the governing concept before the practical breakdown.
  </purpose>

  <chapters_2_and_beyond>
    Each chapter (2, 3, 4, ...) MUST follow this internal order:
    1. Conceptual foundation subtitle — a short sentence describing the key idea or principle of the chapter
    2. Example subtitle — a concrete, relatable scenario that illustrates the conceptual foundation
    3. Remaining subtitles — explicit explanatory subtitles or actionable skills, depending on the chapter purpose

    The conceptual foundation and example subtitles also receive an Impact level like any other subtitle.

    If any remaining subtitle mentions multiple ideas, split them into separate subtitles so each idea is explicit.
  </chapters_2_and_beyond>

  <chapter_1_exception>
    Chapter 1 (Conceptual Overview) already serves as the conceptual foundation for the entire skill.
    It does NOT need a nested conceptual foundation subtitle.
    It DOES include an example subtitle and the descriptive subtitles defined in conceptual_overview_requirements.
  </chapter_1_exception>
</chapter_structure>

<conceptual_overview_requirements>
  <purpose>
    Chapter 1 is the conceptual overview — it IS the conceptual foundation for the entire skill.
    Unlike chapters 2+, it does NOT have a nested conceptual foundation subtitle.
    It DOES include an example subtitle and descriptive subtitles that explain the global conceptual foundation.
  </purpose>

  <chapter_title_output>
    Conceptual Foundations of {inputs.skill}
  </chapter_title_output>

  <learning_order>
    1 (always first, before all practical chapters which start at 2)
  </learning_order>

  <level_adaptation>
    <beginner_entry>
      Include 2-3 foundational understanding subtitles in this chapter:
      - What is {skill} and why it matters (core definition, practical value in context, key use cases)
      - Basic conceptual foundation (simplified workflow, key analogies, fundamental concepts)
      - Essential terminology (3-5 key terms with simple definitions)

      Each subtitle gets its own Impact level.
    </beginner_entry>

    <intermediate>
      Include at least 4 foundational understanding subtitles in this chapter:
      - What is {skill} and strategic value (definition with distinctions, strategic value, common use cases)
      - Conceptual foundation and workflow (conceptual framework, workflow diagram, key principles)
      - Key terminology and concepts (5-8 terms including advanced concepts)
      - Common conceptual mistakes (2-3 misconceptions that learners often have)

      Each subtitle gets its own Impact level.
    </intermediate>

    <expert_advanced>
      Include 4-5 foundational understanding subtitles in this chapter:
      - What is {skill}, scope and boundaries (definition, scope, boundaries, tradeoffs)
      - Advanced conceptual foundation (system-level view, architectural principles, design patterns)
      - Expert terminology and concepts (8-12 terms including expert-level concepts)
      - Subtle errors and pitfalls (3-4 common misconceptions and subtle pitfalls)
      - Connections to adjacent domains (how this skill relates to and integrates with adjacent areas)

      Each subtitle gets its own Impact level.
    </expert_advanced>
  </level_adaptation>

  <examples_in_parentheses>
    Conceptual subtitles should also include examples in parentheses showing how to apply understanding:
    - Example: "Basic conceptual foundation (data flow diagrams, component interaction maps, simplified architecture)"
    - Example: "Essential terminology (define: pipeline, transformation, orchestration)"
  </examples_in_parentheses>
</conceptual_overview_requirements>

<final_chapter_requirements>
  <purpose>
    The skill tree must end with a grouped final chapter that shows what comes after the core learning path and what adjacent areas connect to the skill.
  </purpose>

  <title>
    Advanced and Related Topics for {inputs.skill}
  </title>
</final_chapter_requirements>

<baseline_sustainability>
  This is what "junior/entry-level production readiness" means — the minimum bar where a worker can deliver real, usable output:
  - perform the core workflow end-to-end in {inputs.context}
  - avoid the most common correctness mistakes
  - apply basic safety and best practices relevant to {inputs.context}
  - detect issues early using basic validation checks
  - troubleshoot common failures and recover
  - produce work that is understandable and maintainable by someone else
</baseline_sustainability>

<level_scaling>
  Principle: Always cover baseline sustainability first (the junior/entry-level production readiness bar). Then expand depth and scope according to inputs.level.

  - If inputs.level implies beginner/entry-level: focus on the essential workflow and best practices; exclude expert-only optimizations and rare edge cases
  - If inputs.level implies intermediate: add common robustness patterns and best practices for quality; include a small set of context-relevant edge cases
  - If inputs.level implies expert: add expert-level optimizations, deeper debugging, and context-relevant edge cases and tradeoffs
  - If inputs.level is ambiguous: default to junior/entry-level production readiness unless the level explicitly requests advanced/expert coverage
</level_scaling>

<coverage_requirements>
  <must_include>
    - Workflow basics (where work happens, how changes apply, how outputs are produced)
    - Inputs and access (setup, permissions/credentials if applicable)
    - Core operations (the main actions people do in this skill)
    - Combining or integrating parts (if applicable)
    - Quality checks (common errors, validation, sanity checks)
    - Safety/governance (privacy, security, compliance, safe defaults)
    - Best practices for performance and reliability (avoid obvious slow or fragile patterns)
    - Handoff readiness (naming, structure, minimal documentation)
    - Production use (run/deploy behavior in {inputs.context}, if applicable)
  </must_include>

  <exclude>
    - Expert-only optimizations when inputs.level is entry-level
    - Deep theory not required for {inputs.level}
    - Rare edge cases unless inputs.level is expert or the context makes them common
  </exclude>
</coverage_requirements>

<output_format>
  <formatting>
    Primary format: Numbered hierarchical lists

    Indentation:
    - Use spaces only (2 spaces per level)
    - Tabs forbidden

    List markers: Numbers like 1. or 1.1 or 1.1.1
  </formatting>
</output_format>
