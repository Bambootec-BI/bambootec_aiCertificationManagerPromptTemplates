<template>
  <meta>
    <name>skill_tree_builder</name>
    <version>v1.4.0</version>
    <notes>
      <note>Fill ONLY inputs.skill, inputs.level, inputs.context.</note>
      <note>Ordering is learning progression; Impact (%) is informational and must sum to 100.</note>
      <note>This template is skill-agnostic (no domain-specific references).</note>
    </notes>
  </meta>

  <inputs>
    <skill>[SKILL_NAME]</skill>
    <level>[ABILITY_LEVEL_REQUIRED]</level>
    <context>[CONTEXT: where/how the skill is used in real work]</context>
  </inputs>

  <prompt>
    <language_policy>
      Translate user input into English. Think in English. Only translate to Spanish for the output.

      Instruction language: English
      Output language: Spanish
      Exceptions: proper names, commonly used names, code blocks
    </language_policy>

    <task>
      Build a learning skill tree for {inputs.skill} at {inputs.level} for {inputs.context}.

      Audience: first-time learners entering this kind of work
      Style: textbook index (short section titles + short skill titles)
    </task>

    <constraints>
      <hard_rules>
        <rule>Order MUST follow a learning progression (what to learn first → later)</rule>
        <rule>Each concrete skill MUST include an Impact (%) integer, but Impact MUST NOT affect ordering</rule>
        <rule>The sum of Impact (%) across ALL concrete skills MUST equal exactly 100</rule>
        <rule>Exactly two levels: Level 1 = Section titles (capability areas), Level 2 = Skill titles</rule>
        <rule>Each section MUST contain at least two skills</rule>
        <rule>Skill titles must be short, beginner-friendly, and action-oriented</rule>
        <rule>Each skill must include representative 'how to do it' examples in parentheses (examples only, not extra skills)</rule>
        <rule>Keep skill count within a practical range for the requested level (e.g., 18–35), unless the context clearly demands fewer</rule>
        <rule>FIRST SECTION MUST be "Conceptual Overview" with learning order 0, containing foundational understanding before practical skills</rule>
      </hard_rules>
    </constraints>

    <conceptual_overview_requirements>
      <purpose>Every skill tree MUST start with a conceptual overview section that provides mental models and context before practical skills</purpose>
      <section_title_output>Visión Conceptual (in Spanish output)</section_title_output>
      <learning_order>0 (always first, before all practical skills which start at 1)</learning_order>

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
        - Conceptual overview skills should collectively account for 10-15% of total impact
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
        <item>Workflow basics (where work happens, how changes apply, how outputs are produced)</item>
        <item>Inputs and access (setup, permissions/credentials if applicable)</item>
        <item>Core operations (the main actions people do in this skill)</item>
        <item>Combining or integrating parts (if applicable)</item>
        <item>Quality checks (common errors, validation, sanity checks)</item>
        <item>Safety/governance (privacy, security, compliance, safe defaults)</item>
        <item>Performance/reliability basics (avoid obvious slow/brittle patterns)</item>
        <item>Handoff readiness (naming, structure, minimal documentation)</item>
        <item>Production use (run/deploy behavior in {inputs.context}, if applicable)</item>
      </must_include>

      <exclude>
        <item>Expert-only optimizations when inputs.level is entry-level</item>
        <item>Deep theory not required for {inputs.level}</item>
        <item>Rare edge cases unless inputs.level is expert or the context makes them common</item>
      </exclude>
    </coverage_requirements>

    <output_format>
      <single_output>
        <type>Numbered hierarchical skill tree (book-index style)</type>
        <format>Numbered markdown list</format>
        <requirements>
          <requirement>Two levels only: Level 1 = Section numbers (1, 2, 3…), Level 2 = Skill numbers (1.1, 1.2, 2.1…)</requirement>
          <requirement>Ordered by learning progression</requirement>
          <requirement>Conceptual Overview section uses 0 as its number (0, 0.1, 0.2, 0.3)</requirement>
          <requirement>Impact (%) shown per skill — e.g., "1.1 Skill title (examples) — Impacto X%"</requirement>
          <requirement>No table, no justifications, no "Why this matters" column — just the numbered tree</requirement>
        </requirements>
        <example>
0. **Visión Conceptual**
   0.1 Entender qué es X y su valor (definición, casos de uso, valor práctico) — Impacto 4%
   0.2 Interiorizar el flujo mental de trabajo (pasos clave, analogías) — Impacto 4%
1. **Sección Uno**
   1.1 Primera habilidad (ejemplo A, ejemplo B) — Impacto 5%
   1.2 Segunda habilidad (ejemplo C, ejemplo D) — Impacto 4%
2. **Sección Dos**
   2.1 Tercera habilidad (ejemplo E, ejemplo F) — Impacto 3%
        </example>
      </single_output>
    </output_format>
  </prompt>
</template>
