# Template Evaluation Against Prompt Engineering Best Practices

**Date:** 2026-02-05
**Templates Version:** v1.0.0
**Evaluator:** Claude Sonnet 4.5

## Research Sources

### OpenAI Best Practices
- [Prompt engineering | OpenAI API](https://platform.openai.com/docs/guides/prompt-engineering)
- [Best practices for prompt engineering with the OpenAI API](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api)
- [GPT-5 prompting guide | OpenAI Cookbook](https://cookbook.openai.com/examples/gpt-5/gpt-5_prompting_guide)
- [Structured model outputs | OpenAI API](https://platform.openai.com/docs/guides/structured-outputs)
- [Prompt engineering techniques - Azure OpenAI](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/prompt-engineering?view=foundry-classic)

### Anthropic Claude Best Practices
- [Prompt engineering overview - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)
- [Prompting best practices - Claude Docs](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/claude-4-best-practices)
- [GitHub - anthropics/prompt-eng-interactive-tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- [Claude's Context Engineering Secrets](https://01.me/en/2025/12/context-engineering-from-claude/)

### Multilingual Translation Best Practices
- [Multilingual Prompt Engineering for Semantic Alignment](https://latitude-blog.ghost.io/blog/multilingual-prompt-engineering-for-semantic-alignment/)
- [AI Translation Prompt Engineering](https://contentech.com/how-to-craft-effective-ai-translation-prompts-for-success-with-examples/)
- [Multilingual Prompt Engineering in Large Language Models](https://arxiv.org/html/2505.11665v1)

---

## Evaluation Summary

**Overall Grade: A (90/100)**

The templates demonstrate **excellent** adherence to modern prompt engineering best practices with strong structural organization, clear instruction hierarchy, and effective multilingual handling. Minor improvements possible in XML usage and example inclusion.

---

## Detailed Analysis

### 1. Structural Organization ✅ EXCELLENT

**Best Practice:** Use hierarchical structure with clear sections (OpenAI GPT-4.1/5 guidelines, Claude 4.x)

**Template Implementation:**
- ✅ Clear JSON hierarchy with distinct sections: `meta`, `inputs`, `language_policy`, `role`, `rules`, `instruction`
- ✅ Logical flow from high-level context to detailed requirements
- ✅ Consistent structure across all four templates
- ✅ Uses nested objects for complex rule sets (e.g., `parts`, `dataset_constraints`)

**Evidence:**
```json
{
  "meta": {...},
  "inputs": {...},
  "language_policy": {...},
  "role": {...},
  "rules": {...},
  "instruction": {...},
  "output": {...}
}
```

**Score: 10/10**

---

### 2. Clarity and Explicitness ✅ EXCELLENT

**Best Practice:** Be explicit, specific, and goal-oriented (OpenAI, Claude 4.x)

**Template Implementation:**
- ✅ Explicit task definitions at the beginning
- ✅ Clear role/persona assignment (`"persona": "Instructional designer..."`)
- ✅ Specific constraints with quantified requirements (e.g., `"min_english_source_ratio": 0.8`)
- ✅ Hard rules with NON-NEGOTIABLE markers for critical requirements

**Evidence:**
```json
"sources_non_negotiable": [
  "NON-NEGOTIABLE: Every source MUST be a raw URL on its own line...",
  "Do NOT use markdown link syntax like [Title](URL).",
  "Do NOT output plain text titles without URLs."
]
```

**Score: 10/10**

---

### 3. Instruction Priority and Placement ✅ EXCELLENT

**Best Practice:** Task at beginning, context after (OpenAI); Human messages > System messages (Claude)

**Template Implementation:**
- ✅ Task/mission stated early in each template
- ✅ Role and objective before detailed rules
- ✅ `instruction` field provides high-level task before detailed `content_requirements`

**Evidence:**
```json
"instruction": "Using the provided skill tree, produce a simplified actionable content tree..."
```

**Score: 10/10**

---

### 4. Multilingual Handling ✅ EXCELLENT

**Best Practice:** Explicit language separation, specify audience and context (Multilingual PE research)

**Template Implementation:**
- ✅ **Absolute enforcement clause**: "Translate user input into English. Think in English. Only translate to Spanish for the output."
- ✅ Clear instruction vs. output language separation
- ✅ Exceptions specified (proper names, code blocks)
- ✅ Semantic alignment: English thinking ensures consistent reasoning

**Evidence:**
```json
"language_policy": {
  "absolute_enforcement": "Translate user input into English. Think in English. Only translate to Spanish for the output.",
  "instruction_language": "English",
  "output_language": "Spanish",
  "exceptions": ["proper_names", "commonly_used_names", "code_blocks"]
}
```

**Score: 10/10**

---

### 5. Context and Reasoning ✅ EXCELLENT

**Best Practice:** Provide context/motivation behind instructions (Claude 4.x)

**Template Implementation:**
- ✅ Explains "why" for many rules (e.g., "ChatGPT renders raw URLs with clickable previews")
- ✅ Justifies constraints (e.g., dataset must be free, legal, accessible)
- ✅ `baseline_sustainability_definition` provides reasoning for coverage requirements

**Evidence:**
```json
"url_format_requirement": "URLs must be raw URLs on their own line (not markdown link syntax). ChatGPT renders raw URLs with clickable previews."
```

**Score: 9/10** (-1 for occasional missing "why" explanations)

---

### 6. Negative Instructions and Edge Cases ✅ EXCELLENT

**Best Practice:** Explicitly state what NOT to do (OpenAI, Claude)

**Template Implementation:**
- ✅ Strong use of negative clauses: "Do NOT...", "Never..."
- ✅ `negative_clauses` array in dataset constraints
- ✅ Blocking behavior specified for edge cases (BLOCKED when no dataset available)
- ✅ Multiple layers of prevention (e.g., no search queries mentioned 3+ times)

**Evidence:**
```json
"negative_clauses": [
  "Do NOT use markdown link syntax like [Title](URL).",
  "Do NOT output plain text titles without URLs.",
  "Do NOT output search queries or 'look up...' instructions.",
  "Do NOT output 'Search query:' or query placeholders."
]
```

**Score: 10/10**

---

### 7. Delimiters and Markers ⚠️ GOOD (Could Improve)

**Best Practice:** Use XML tags or Markdown for structure (OpenAI GPT-5, Claude training)

**Template Implementation:**
- ✅ Markdown recommended in output format
- ⚠️ JSON structure instead of XML tags within prompt text
- ⚠️ Could use `<instruction>`, `<examples>`, `<rules>` XML tags for better adherence

**Evidence:**
Current: JSON structure
Recommended: Consider XML-style markers for critical sections

**Score: 7/10** (-3 for not leveraging XML tags which Claude and GPT-5 prefer)

---

### 8. Examples and Few-Shot Learning ⚠️ FAIR (Needs Improvement)

**Best Practice:** Include realistic examples, edge cases (OpenAI, Claude, Multilingual PE)

**Template Implementation:**
- ⚠️ Minimal examples provided in templates
- ⚠️ No few-shot examples showing expected output format
- ✅ Output structure examples provided (good)
- ⚠️ No edge case examples for model to learn from

**Evidence:**
```json
"Format: Title on one line, then raw URL on the next line (e.g., 'Introduction to Python\\nhttps://example.com/intro')."
```
Only inline format examples, no full realistic examples.

**Score: 6/10** (-4 for lacking comprehensive examples)

---

### 9. Step-by-Step and Reasoning ✅ EXCELLENT

**Best Practice:** Break complex tasks into steps, allow thinking time (Claude 4.x, OpenAI)

**Template Implementation:**
- ✅ Clear step-by-step in content_requirements
- ✅ PARTS system breaks large outputs into manageable chunks
- ✅ Sequential workflow defined (skill tree → content → exercises → exam)
- ✅ Continuity rules for maintaining context across parts

**Evidence:**
```json
"parts": {
  "enabled": true,
  "counting_rule": "Only numbered items count...",
  "stop_conditions": [...],
  "continuity": ["Maintain numbering across PARTS."]
}
```

**Score: 10/10**

---

### 10. Output Format Specification ✅ EXCELLENT

**Best Practice:** Specify exact output format (OpenAI Structured Outputs, Claude)

**Template Implementation:**
- ✅ Detailed `required_structure` with template placeholders
- ✅ Field names specified explicitly
- ✅ Data types indicated (integer range, bullets, etc.)
- ✅ Format examples provided

**Evidence:**
```json
"required_structure": [
  "1. **<Section Title (verbatim)>** — <1-line objective>",
  "   - Expected learning outcomes: <3–6 bullets>",
  "   - Coverage (details): ...",
  "   - Estimated time: <integer range in minutes>"
]
```

**Score: 10/10**

---

### 11. Parameter Handling and Defaults ✅ EXCELLENT

**Best Practice:** Handle optional parameters, provide defaults (OpenAI, Claude)

**Template Implementation:**
- ✅ Clear `inputs` section with placeholder defaults
- ✅ `defaults` object for configurable parameters
- ✅ Fallback behavior specified (e.g., skill_tree optional)
- ✅ Parameter types indicated

**Evidence:**
```json
"skill_tree": {
  "provided": false,
  "format": "hierarchical_text_or_json",
  "value": "[OPTIONAL: SKILL_TREE_FROM_PREVIOUS_STEP]"
}
```

**Score: 10/10**

---

### 12. Constraint Enforcement ✅ EXCELLENT

**Best Practice:** Clear, testable constraints (OpenAI, Claude)

**Template Implementation:**
- ✅ Quantified constraints (e.g., sum to 100, exactly 2 levels)
- ✅ Validation rules (e.g., every source must have URL)
- ✅ Blocking conditions for violations
- ✅ NON-NEGOTIABLE markers for critical rules

**Evidence:**
```json
"The sum of Impact (%) across ALL concrete skills MUST equal exactly 100."
"min_english_source_ratio": 0.8
```

**Score: 10/10**

---

## Strengths

1. **Exceptional structural clarity** - Clear JSON hierarchy mirrors recommended prompt structure
2. **Strong multilingual handling** - Explicit English-thinking, Spanish-output separation aligns with best practices
3. **Comprehensive negative instructions** - Prevents common failure modes effectively
4. **Well-defined output format** - Detailed structure specifications reduce ambiguity
5. **Excellent constraint specification** - Quantified, testable requirements
6. **PARTS system** - Elegant solution for iterative output management
7. **Cross-template consistency** - All templates follow same organizational pattern

---

## Areas for Improvement

### Priority 1: Add Examples (Impact: High)

**Recommendation:** Add `examples` section to each template with 2-3 realistic input/output pairs.

**Rationale:** OpenAI and Claude research shows examples significantly improve output quality, especially for edge cases. Few-shot learning proven effective in multilingual contexts.

**Implementation:**
```json
"examples": [
  {
    "input": {"skill": "Python web scraping", "level": "intermediate", ...},
    "expected_output": "1. **Web Fundamentals**\n   - Expected learning outcomes: ...",
    "note": "Shows intermediate-level scope with real URLs"
  }
]
```

### Priority 2: Consider XML Tag Integration (Impact: Medium)

**Recommendation:** Add XML-style markers for major sections in instruction text.

**Rationale:** GPT-5 and Claude trained with XML tags show improved instruction adherence.

**Implementation:**
```json
"instruction": "<task>Build a learning skill tree...</task>\n<constraints>NON-NEGOTIABLE: ...</constraints>"
```

### Priority 3: Add Edge Case Examples (Impact: Medium)

**Recommendation:** Include challenging scenarios and expected handling.

**Implementation:**
```json
"edge_cases": [
  {
    "scenario": "User requests technical skill but provides only non-technical context",
    "expected_behavior": "Infer technical requirements from skill name, clarify with user if ambiguous"
  }
]
```

---

## Compliance Matrix

| Best Practice | OpenAI | Claude | Multi-PE | Status |
|--------------|--------|---------|----------|--------|
| Structured organization | ✅ | ✅ | ✅ | ✅ Excellent |
| Clear task at start | ✅ | ✅ | ✅ | ✅ Excellent |
| Explicit instructions | ✅ | ✅ | ✅ | ✅ Excellent |
| Negative instructions | ✅ | ✅ | - | ✅ Excellent |
| Context/reasoning | ✅ | ✅ | ✅ | ✅ Excellent |
| XML/Markdown delimiters | ✅ | ✅ | - | ⚠️ Good |
| Examples/few-shot | ✅ | ✅ | ✅ | ⚠️ Fair |
| Step-by-step breakdown | ✅ | ✅ | ✅ | ✅ Excellent |
| Output format specs | ✅ | ✅ | - | ✅ Excellent |
| Language separation | - | - | ✅ | ✅ Excellent |

---

## Final Recommendations

### Immediate Actions (High Priority)
1. **Add 2-3 realistic examples per template** - Include input, expected output, and notes
2. **Document edge case handling** - Add explicit scenarios and expected behaviors

### Future Enhancements (Medium Priority)
1. **Experiment with XML tags** - Test if `<task>`, `<rules>`, `<examples>` improve adherence
2. **Add few-shot learning examples** - Include challenging cases for model training
3. **Create validation test suite** - Automated checks against example outputs

### Maintain (Current Excellence)
1. **JSON structure and hierarchy** - Keep current organization
2. **Language policy implementation** - Excellent multilingual handling
3. **Negative instruction patterns** - Strong failure mode prevention
4. **PARTS system** - Well-designed iterative output management

---

## Conclusion

The templates demonstrate **professional-grade prompt engineering** with strong adherence to 2024-2025 best practices from OpenAI, Anthropic, and multilingual PE research. The main gap is the absence of comprehensive examples, which would elevate these from excellent to exceptional. The explicit language separation and structural organization are particularly noteworthy implementations that align perfectly with current research.

**Recommended Grade: A (90/100)**

With the addition of examples and edge cases, these templates could easily achieve A+ (95+).
