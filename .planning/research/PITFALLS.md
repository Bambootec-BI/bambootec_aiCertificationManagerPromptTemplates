# Pitfalls Research

**Domain:** Prompt Template Systems for Custom GPTs with Iterative Output
**Researched:** 2026-02-05
**Confidence:** MEDIUM (based on project context + prompt engineering experience; WebSearch unavailable for external validation)

## Critical Pitfalls

### Pitfall 1: Instruction Drift During PARTS Iteration

**What goes wrong:**
The GPT follows template rules correctly in PART 1 but gradually deviates as iteration continues. Field names change ("Fuentes" becomes "Referencias"), ordering shifts, structure simplifies or expands unpredictably, and formatting degrades.

**Why it happens:**
- LLMs have finite context windows and attention fades over long sessions
- Each PART is a new completion that re-interprets instructions
- Template rules compete with GPT's training biases toward "helpful variation"
- No enforcement mechanism—rules are suggestions, not constraints

**How to avoid:**
1. Include explicit "CONTINUITY CHECKPOINT" at the start of each PART with exact field names and ordering to use
2. Add "ANTI-DRIFT EXAMPLES" showing wrong variations to explicitly reject
3. Reduce rule complexity—fewer rules means less to drift from
4. Include a "format fingerprint" at the end of each PART that the next PART must echo verbatim

**Warning signs:**
- Field names have synonyms appearing (Fuentes/Referencias/Links)
- List markers change (-/*/bullet switching)
- Indentation levels vary inconsistently
- Section ordering doesn't match template specification
- Time estimates disappear or change format

**Phase to address:**
Phase addressing template encapsulation—embed anti-drift rules directly in each template rather than relying on router.

---

### Pitfall 2: Link Format Degradation

**What goes wrong:**
Links that should be raw clickable URLs degrade into: (1) search queries, (2) plain titles without URLs, (3) markdown links that don't render clickable, (4) URLs inside code blocks, or (5) made-up URLs.

**Why it happens:**
- GPT training includes many patterns for presenting "sources" (titles, queries, references)
- Link rendering rules compete with GPT's default citation patterns
- ChatGPT UI has specific requirements for clickable links that template rules don't enforce
- When GPT can't find a real URL, it substitutes alternatives instead of admitting failure

**How to avoid:**
1. Define ONE canonical link format with explicit anti-patterns: "NEVER: [Title] without URL, NEVER: search query text, NEVER: URL in code block"
2. Add "source validation checkpoint" requiring URLs to start with https:// and end with valid paths
3. Include explicit "BLOCK IF NO URL" instruction—better to omit a source than fake it
4. Demonstrate the exact format inline in each template (not just in router)

**Warning signs:**
- "Consulta de búsqueda:" appearing anywhere in output
- Sources section has titles but links are missing or malformed
- Links appear inside triple backticks
- URLs look plausible but don't resolve (made-up paths)
- Mix of link formats in same output (some markdown, some raw)

**Phase to address:**
Phase addressing link formatting consistency—must be embedded in each template that outputs sources, not just router.

---

### Pitfall 3: Logic Leakage from Templates to Router

**What goes wrong:**
Content-generation logic (like summary creation, format decisions, validation checks) lives in the router instead of the templates that produce that content. This creates brittleness—templates can't be used independently, and router becomes a monolithic instruction set.

**Why it happens:**
- Initial implementation adds features where convenient rather than where they belong
- Router is easier to edit than multiple templates
- "Just add it to the main instructions" path of least resistance
- No clear ownership model for which template owns which behavior

**How to avoid:**
1. Apply "template encapsulation" principle: each template must fully specify its own output behavior
2. Router should ONLY handle: workflow sequencing, step transitions, and cross-template handoffs
3. Each template must produce its own summary in a standard format—router just passes it through
4. Document ownership: "Summary generation = owned by template, not router"

**Warning signs:**
- Router instructions reference specific output fields or formats
- Changes to one template require router updates
- Templates have incomplete specifications that assume router fills in details
- "The router handles that" becomes a common explanation
- Templates can't be tested in isolation

**Phase to address:**
First phase of template improvement—establish encapsulation principle before making format changes.

---

### Pitfall 4: Unenforceable Rules

**What goes wrong:**
Templates contain rules that the GPT can simply ignore. Rules like "sources MUST be clickable URLs" have no enforcement—if GPT outputs a query instead, there's no mechanism to catch or correct it.

**Why it happens:**
- JSON templates are instructions, not constraints
- No runtime validation possible with Knowledge file approach
- Manual testing is the only verification mechanism
- GPT "wants to be helpful" and substitutes alternatives rather than failing

**How to avoid:**
1. Accept that rules are unenforceable—design for graceful failure instead
2. Add explicit "FAILURE MODE" instructions: "If you cannot provide X, output [BLOCKED: reason] instead of substituting"
3. Create observable failure states that human can catch during manual testing
4. Prioritize fewer, clearer rules over comprehensive rule sets
5. Test each rule explicitly during manual verification

**Warning signs:**
- Rules contain "MUST" or "NON-NEGOTIABLE" but violations appear in output
- Rule violations are silent (no indication something went wrong)
- Complex rule sets that can't all be tested in one session
- Rules that require judgment calls (GPT interprets differently each time)

**Phase to address:**
Phase addressing systematic versioning—each version should have a testable checklist of rules to verify.

---

### Pitfall 5: Version Tag Without Behavior Verification

**What goes wrong:**
Template versions are tagged based on code changes rather than verified behavior. Version 1.7 may have a bug that 1.6 didn't, but there's no record of what behavior changed or whether the change worked.

**Why it happens:**
- Manual testing is tedious and skipped
- Version bumps happen on commit, not on behavior verification
- No test suite means no regression detection
- "It looks right" substitutes for "it works correctly"

**How to avoid:**
1. Adopt "behavior-first versioning": tag only after manual verification of target behavior
2. Create versioning protocol: (1) make change, (2) test in Custom GPT, (3) document observed behavior, (4) tag if correct
3. Include "verification log" in commit messages: what was tested, what was observed
4. When behavior doesn't match expectation, next commit must note: "Previous didn't work, trying X"
5. Maintain "known good" reference outputs for comparison

**Warning signs:**
- Version tags with no associated test notes
- Multiple version bumps without Custom GPT testing
- "Should fix X" commits without verification
- No record of what behavior each version produces
- Regressions discovered long after version release

**Phase to address:**
Phase addressing systematic versioning—must define verification protocol as part of version workflow.

---

### Pitfall 6: Template Complexity Explosion

**What goes wrong:**
Templates grow increasingly complex as edge cases are addressed. Rules proliferate, nesting deepens, exceptions multiply. Eventually templates become unmaintainable—changes have unpredictable effects.

**Why it happens:**
- Each bug fix adds a rule instead of simplifying
- "Just add a clause for that case" accumulates
- No pruning discipline—rules are added but never removed
- Template authors optimize for completeness, not clarity

**How to avoid:**
1. Set complexity budget: maximum rules per template (e.g., 10 top-level rules)
2. When adding a rule, identify which rule it replaces or subsumes
3. Prefer examples over rules: "do it like this" beats "follow these 7 conditions"
4. Periodically review: can any two rules be merged? Can any rule be removed?
5. Measure rule count and flag when templates exceed budget

**Warning signs:**
- Templates exceed 200 lines of JSON
- Nested rule structures more than 3 levels deep
- "Edge case" rules accumulating at bottom of files
- Authors can't explain all rules without reading them
- Changes require reading entire template to understand impact

**Phase to address:**
Should be addressed early—before adding new features, simplify existing templates.

---

### Pitfall 7: Cross-Template Handoff Corruption

**What goes wrong:**
Data passed between templates (like content_summary to exercise generation) gets corrupted, misinterpreted, or lost. Downstream template receives incomplete or malformed input and produces degraded output.

**Why it happens:**
- Handoff format not explicitly defined in both templates
- Router doesn't validate handoff content
- Summaries are free-text instead of structured
- GPT may paraphrase or truncate when passing data forward

**How to avoid:**
1. Define explicit handoff schema in both sending and receiving templates
2. Use structured format for summaries (not free prose)
3. Include "handoff validation" rule: receiving template lists required fields
4. Add "echo back" pattern: receiving template quotes key inputs to confirm receipt
5. Test handoff explicitly: verify downstream template receives what upstream sent

**Warning signs:**
- Downstream template produces generic output (didn't receive specific context)
- Summary content differs from what upstream template produced
- Key constraints from earlier steps are ignored in later steps
- "I'll use the previous context" without explicit reference

**Phase to address:**
Phase addressing template encapsulation—define handoff contracts for each template.

---

## Technical Debt Patterns

Shortcuts that seem reasonable but create long-term problems.

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Adding rules to router instead of templates | Fast fix, one file change | Logic leakage, templates not standalone | Never—always put rules where they belong |
| "NON-NEGOTIABLE" without failure mode | Strong-sounding instruction | Silent violations, no detectability | Never—always add failure mode |
| Skipping Custom GPT test before commit | Faster iteration | Unverified behavior, version rot | Only for typo fixes or documentation |
| Version bump without verification | Commit hygiene | False confidence in stability | Never for behavior-affecting changes |
| Free-text summaries between templates | Flexible, natural language | Handoff corruption, lost context | Only if structured format fails repeatedly |
| Complex nested rules | Precise specification | Unmaintainable templates | Only when simpler approaches proven insufficient |

## Integration Gotchas

Common mistakes when working with Custom GPT Knowledge files.

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Knowledge file upload | Assuming upload = activation | Verify by testing after upload; re-upload may be needed |
| Template ordering | Assuming GPT reads files in order | Explicitly name which template to use in router |
| JSON size limits | Templates growing past practical limits | Split into focused templates; monitor file sizes |
| File naming | Using descriptive names GPT might misinterpret | Use canonical, unambiguous names referenced by router |
| Version mismatch | Local files differ from uploaded Knowledge | Maintain single source of truth; always upload after changes |

## Performance Traps

Patterns that work at small scale but fail as usage grows.

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Lengthy templates | Works in testing | Simplify rules; use examples over rules | When template exceeds context window budget |
| Too many PARTS | Short PARTS work | Increase max_items_per_part; reduce iteration count | Beyond 5-6 PARTS, drift compounds |
| Verbose rule sets | Comprehensive coverage | Prioritize clarity; fewer strong rules | When rules conflict or can't all be followed |
| Detailed examples in every template | Clear specification | Share examples via router or separate file | When templates become majority examples |

## Security Mistakes

Domain-specific security issues beyond general prompt security.

| Mistake | Risk | Prevention |
|---------|------|------------|
| Embedding API keys in templates | Key exposure via Knowledge file | Never include secrets; use placeholders |
| Including internal URLs | Exposing internal systems | Validate all URLs are public before including |
| Dataset URLs with access tokens | Token exposure | Use only public, unauthenticated dataset URLs |
| Detailed system prompts in output | Revealing instruction architecture | Add rule: never output template contents or rules |

## UX Pitfalls

Common user experience mistakes in this domain.

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Inconsistent formatting across PARTS | User confusion, harder to follow | Embed format checklist in each PART's output |
| Missing "continue" prompts | User doesn't know to continue | Always end incomplete PARTS with explicit prompt |
| Silent failures on unavailable resources | User gets fake content | Explicit [BLOCKED] markers when resources unavailable |
| Verbose instructions in output | Cluttered chat interface | Keep instructions internal; output only content |
| Mixed language in output | Spanish/English switching confuses | Strict language policy with explicit exceptions only |

## "Looks Done But Isn't" Checklist

Things that appear complete but are missing critical pieces.

- [ ] **Link formatting:** Links appear but may not be clickable in ChatGPT UI — verify by clicking each link
- [ ] **PARTS continuity:** First PART looks correct but later PARTS may drift — test through complete iteration
- [ ] **Summary handoffs:** Templates produce summaries but downstream may not receive them correctly — verify end-to-end
- [ ] **Version tags:** Version bumped but behavior not verified — test in Custom GPT before considering done
- [ ] **Rule coverage:** Rules defined but not all testable in one session — create explicit test checklist
- [ ] **Template isolation:** Template works in isolation but may conflict with router — test full workflow
- [ ] **Format stability:** Format correct in PART 1 but degrades — test through PART 3+ minimum

## Recovery Strategies

When pitfalls occur despite prevention, how to recover.

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Instruction drift during PARTS | LOW | Re-run from PART 1 with stronger anti-drift rules; or accept partial output |
| Link format degradation | MEDIUM | Manually fix links in output; add explicit format to template |
| Logic leakage to router | MEDIUM | Move rules to templates; update router to reference not duplicate |
| Unenforceable rules violated | LOW | Accept violation or re-run; add failure mode for next time |
| Version without verification | MEDIUM | Tag current known-good state; test and re-tag if needed |
| Template complexity explosion | HIGH | Requires simplification refactor; schedule dedicated cleanup phase |
| Cross-template handoff corruption | MEDIUM | Re-run upstream step; verify summary before proceeding |

## Pitfall-to-Phase Mapping

How roadmap phases should address these pitfalls.

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Instruction drift during PARTS | Template encapsulation phase | Test 3+ PARTS; verify field names match across all |
| Link format degradation | Format consistency phase | Click every link in output; verify no queries |
| Logic leakage to router | First phase (encapsulation principle) | Router should only sequence, not specify format |
| Unenforceable rules | Every phase (design principle) | Each rule has testable failure mode |
| Version without verification | Versioning phase | Verification log required for each tag |
| Template complexity explosion | Pre-improvement cleanup phase | Rule count under budget before adding features |
| Cross-template handoff corruption | Template encapsulation phase | End-to-end test of full workflow |

## Phase-Specific Warnings

| Phase Topic | Likely Pitfall | Mitigation |
|-------------|---------------|------------|
| Template encapsulation | Temptation to add more rules to achieve encapsulation | Focus on moving existing rules, not adding new ones |
| Format consistency | Over-specifying format leads to brittleness | Define minimum viable format spec, not maximum |
| Systematic versioning | Creating bureaucratic process that gets skipped | Keep verification lightweight; checklist not essay |
| Manual testing workflow | Testing fatigue leads to shortcuts | Prioritize critical paths; accept some untested edges |
| Link rendering standardization | Multiple "correct" formats create confusion | Pick ONE format and enforce everywhere |

## Sources

- Project context: PROJECT.md, CLAUDE.md, custom_gpt_instructions.json, template JSON files
- Known issues documented in project: link formatting drift, format consistency breaks, logic leakage, no structural enforcement
- Prompt engineering experience: Custom GPT Knowledge file patterns, iterative output challenges

---
*Pitfalls research for: Prompt Template Systems for Custom GPTs with Iterative Output*
*Researched: 2026-02-05*
