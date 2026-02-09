<name>
  skill_tree_builder
</name>

<version>
  v1.6.0
</version>

<inputs>
  <skill>Skill: the skill name and some specification if required</skill>
  <level>Ability Level: level of proficiency with the skill</level>
  <context>Context: where/how the skill is used in real work</context>
</inputs>

<command>
  Build a learning skill tree for {inputs.skill} at {inputs.level} for {inputs.context}.

  Audience: first-time learners entering to this skill-level domain.
  Style: textbook index (short section titles + short skill titles)
</command>

<critical_commands>
  Ordering is learning progression; Impact (%) is informational and must sum to 100.
</critical_commands>

<language_policy>
  Use language_policy from instructions. Reminder:
  Translate user input into English. Think in English. Only translate to Spanish for the output.
</language_policy>

<non_negotiable>
  - Order MUST follow a learning progression (what to learn first → later)
  - Each concrete skill MUST include an Impact (%) integer, but Impact MUST NOT affect ordering
  - The sum of Impact (%) across ALL concrete skills MUST equal exactly 100
  - Exactly two levels: Level 1 = Section titles (capability areas), Level 2 = Skill titles
  - Each section MUST contain at least two skills.
  - Skill titles must be short, beginner-friendly, easily understandable and action-oriented
  - Each skill must include representative 'how to do it' examples in parentheses (examples only, not extra skills)
  - Keep skill count within a practical range for the requested level (e.g., 18–35), unless the context clearly demands fewer
  - FIRST SECTION MUST be "Conceptual Overview" with learning order 1, containing foundational understanding before practical skills
</non_negotiable>

<conceptual_overview_requirements>
  <purpose>
    Every skill tree MUST start with a conceptual overview section that provides mental models and context before practical skills
  </purpose>

  <section_title_output>
    Conceptual Overview (translated per language_policy in output)
  </section_title_output>

  <learning_order>
    1 (always first, before all practical skills which start at 2)
  </learning_order>

  <level_adaptation>
    <beginner_entry>
      Include 2-3 foundational understanding skills in this section:
      - What is {skill} and why it matters (core definition, practical value in context, key use cases)
      - Basic mental model (simplified workflow, key analogies, fundamental concepts)
      - Essential terminology (3-5 key terms with simple definitions)

      Each skill gets its own Impact (%) allocation.
    
    </beginner_entry>

    <intermediate>
      Include 3-4 foundational understanding skills in this section:
      - What is {skill} and strategic value (definition with distinctions, strategic value, common use cases)
      - Mental model and workflow (conceptual framework, workflow diagram, key principles)
      - Key terminology and concepts (5-8 terms including advanced concepts)
      - Common conceptual mistakes (2-3 misconceptions that learners often have)

      Each skill gets its own Impact (%) allocation.
    </intermediate>

    <expert_advanced>
      Include 4-5 foundational understanding skills in this section:
      - What is {skill}, scope and boundaries (definition, scope, boundaries, tradeoffs)
      - Advanced mental model (system-level view, architectural principles, design patterns)
      - Expert terminology and concepts (8-12 terms including expert-level concepts)
      - Subtle errors and pitfalls (3-4 common misconceptions and subtle pitfalls)
      - Connections to adjacent domains (how this skill relates to and integrates with adjacent areas)

      Each skill gets its own Impact (%) allocation.
    </expert_advanced>
  </level_adaptation>

  <impact_allocation>
    - Each individual conceptual skill receives its own Impact (%) value like any other skill
    - Impact MUST still sum to exactly 100 across ALL skills (including conceptual overview skills)
  </impact_allocation>

  <examples_in_parentheses>
    Conceptual skills should also include examples in parentheses showing how to apply understanding:
    - Example: "Basic mental model (data flow diagrams, component interaction maps, simplified architecture)"
    - Example: "Essential terminology (define: pipeline, transformation, orchestration)"
  </examples_in_parentheses>
</conceptual_overview_requirements>

<baseline_sustainability>
  Baseline sustainability (minimum) means the learner can:
  - perform the core workflow end-to-end in {inputs.context}
  - avoid the most common correctness mistakes
  - apply basic safety/governance rules relevant to {inputs.context}
  - detect issues early using basic validation checks
  - troubleshoot common failures and recover
  - produce work that is understandable and maintainable by someone else
</baseline_sustainability>

<level_scaling>
  Principle: Always cover the baseline sustainability definition. Then expand depth and scope according to inputs.level.

  - If inputs.level implies beginner/entry-level: focus on the essential/common workflow; exclude expert-only optimizations and rare edge cases
  - If inputs.level implies intermediate: include common robustness and performance patterns; include a small set of context-relevant edge cases
  - If inputs.level implies expert: include expert-level optimizations, deeper debugging/diagnostics, and context-relevant edge cases and tradeoffs
  - If inputs.level is ambiguous: default to 'entry-level production' scope unless the level explicitly requests advanced/expert coverage
</level_scaling>

<coverage_requirements>
  <must_include>
    - Workflow basics (where work happens, how changes apply, how outputs are produced)
    - Inputs and access (setup, permissions/credentials if applicable)
    - Core operations (the main actions people do in this skill)
    - Combining or integrating parts (if applicable)
    - Quality checks (common errors, validation, sanity checks)
    - Safety/governance (privacy, security, compliance, safe defaults)
    - Performance/reliability basics (avoid obvious slow/brittle patterns)
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

  <requirements>
    - Two levels only: Level 1 = Sections as ### headings with number, Level 2 = Skills as plain text lines with decimal number
    - Ordered by learning progression
    - Impact (%) shown per skill
    - No table, no justifications, no "Why this matters" — just the tree
  </requirements>

  <example>
1 Conceptual Overview
- 1.1 Understand what X is and its value (definition, use cases, practical value) — Impact 4%
- 1.2 Internalize the mental workflow (key steps, analogies) - Impact 4%
2 Skill Section One
- 2.1 First skill (example A, example B) — Impact 5%    
- 2.2 Second skill (example C, example D) — Impact 4%
3 Skill Section Two
- 3.1 Third skill (example E, example F) — Impact 3%
  </example>
</output_format>
