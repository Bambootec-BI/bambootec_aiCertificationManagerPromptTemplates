# Phase 1: Contract Infrastructure - Research

**Researched:** 2026-02-05
**Domain:** JSON Schema validation for prompt template systems
**Confidence:** HIGH (based on official documentation, Context7-equivalent sources, and verified tooling)

## Summary

This research addresses establishing foundational schemas and format standards for a 5-template Custom GPT prompt orchestration system. The key challenge is creating structural validation for JSON templates that will be deployed as Knowledge files (no API access, no runtime validation).

The recommended approach focuses on:
1. **JSON Schema Draft-07** for maximum validator compatibility (Draft 2020-12 for greenfield, but Draft-07 has widest support)
2. **ajv-cli** for command-line pre-commit validation
3. **Centralized format standards** with version markers to detect drift
4. **Input/output contracts** between templates to prevent handoff corruption

**Primary recommendation:** Create 5 template schemas + 1 shared format_standards.json, validate with `ajv validate` before any commit, and embed version markers in both schemas and templates to detect contract drift.

## Standard Stack

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| JSON Schema | Draft-07 | Template structure validation | Maximum compatibility across validators; supported by ajv, VS Code, online validators; backward-compatible with Draft-06; no advanced features needed for this use case |
| ajv-cli | 5.x | Command-line schema validation | Fastest JSON validator; supports Draft-07; scriptable; works offline; `npm install -g ajv-cli` |
| Node.js | 18+ | Required for ajv-cli | LTS version; only needed for CLI tool installation |

### Supporting

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| @jirutka/ajv-cli | 1.x | Enhanced ajv-cli fork | Human-friendly "pretty" error format; line-based output for CI; use if original ajv-cli output is hard to parse |
| VS Code JSON validation | Built-in | Editor-time validation | Associate schemas with files in `.vscode/settings.json` for immediate feedback |
| jq | 1.6+ | JSON manipulation | Extract/compare specific sections; useful for debugging |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Draft-07 | Draft 2020-12 | Newer features (unevaluatedProperties) but less validator support; overkill for template validation |
| ajv-cli | Online validators | Works for spot-checks but not scriptable; can't integrate into workflow |
| JSON Schema | TypeScript types | Requires compilation; templates aren't TypeScript; schema is language-agnostic |

**Installation:**
```bash
# One-time setup
npm install -g ajv-cli

# Verify installation
ajv --version

# Optional: enhanced fork with better error messages
npm install -g @jirutka/ajv-cli
```

## Architecture Patterns

### Recommended Project Structure

```
/
├── skill_tree_builder_template.json
├── skill_tree_to_content_tree_template.json
├── skill_to_practice_exercises_template.json
├── skill_to_evaluation_exam_template.json
├── custom_gpt_instructions.json
│
├── schemas/
│   ├── skill_tree_builder.schema.json
│   ├── skill_tree_to_content.schema.json
│   ├── skill_to_practice_exercises.schema.json
│   ├── skill_to_evaluation_exam.schema.json
│   ├── custom_gpt_instructions.schema.json
│   └── shared/
│       ├── format_standards.schema.json
│       ├── input_contract.schema.json
│       └── output_contract.schema.json
│
└── .planning/
    └── ...
```

### Pattern 1: Shared Format Standards

**What:** Define formatting rules (indentation, list style, language policy) once; all templates reference them.

**When to use:** Always for cross-cutting formatting concerns.

**Example:**
```json
// format_standards.json (embedded in router OR standalone)
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "$id": "format_standards",
  "version": "1.0.0",
  "definitions": {
    "indentation": {
      "use_spaces_only": true,
      "spaces_per_level": 2,
      "tabs_forbidden": true
    },
    "list_style": {
      "all_levels": "-"
    },
    "language_policy": {
      "instruction_language": "English",
      "output_language": "Spanish",
      "exceptions": ["proper_names", "code_blocks", "technical_terms"]
    },
    "link_format": {
      "preferred": "raw_url_own_line",
      "fallback": "[URL](URL)",
      "forbidden": ["code_blocks", "search_queries", "invented_urls"]
    }
  }
}
```

**Reference in templates:**
```json
{
  "format_standards": {
    "$ref": "format_standards.json#/definitions",
    "version_expected": "1.0.0"
  }
}
```

### Pattern 2: Version Markers for Contract Compatibility

**What:** Each template declares what version of upstream output it expects and what version of output it produces.

**When to use:** For all templates in the chain to detect drift.

**Example:**
```json
// In skill_tree_to_content_tree_template.json
{
  "meta": {
    "name": "skill_tree_to_content",
    "version": "1.8.0",
    "contract_versions": {
      "format_standards": "1.0.0",
      "input_expects": {
        "skill_tree": "skill_tree_builder@1.2.0"
      },
      "output_produces": {
        "content_tree": "1.8.0",
        "content_summary": "1.0.0"
      }
    }
  }
}
```

**Drift detection:** When `skill_tree_builder` bumps to 1.3.0 with schema changes, downstream template still expects 1.2.0 - this mismatch signals required review.

### Pattern 3: Input/Output Contract Schemas

**What:** Define explicit schemas for what each template receives and produces, especially for handoff summaries.

**When to use:** For all template boundaries where data passes forward.

**Example (content_summary contract):**
```json
// schemas/shared/content_summary.schema.json
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "$id": "content_summary",
  "type": "object",
  "required": [
    "concepts_covered",
    "pitfalls_identified",
    "time_total_minutes",
    "source_count"
  ],
  "properties": {
    "concepts_covered": {
      "type": "array",
      "items": { "type": "string" },
      "minItems": 1
    },
    "pitfalls_identified": {
      "type": "array",
      "items": { "type": "string" }
    },
    "time_total_minutes": {
      "type": "string",
      "pattern": "^\\d+-\\d+$",
      "description": "Integer range like '120-180'"
    },
    "source_count": {
      "type": "integer",
      "minimum": 1
    },
    "english_source_ratio": {
      "type": "number",
      "minimum": 0,
      "maximum": 1
    }
  }
}
```

### Anti-Patterns to Avoid

- **Inline format rules in each template:** Creates duplication, leads to drift when one template updated but not others.
- **Implicit handoff formats:** Using placeholder text like `[SUMMARY_FROM_PREVIOUS_STEP: concepts, pitfalls...]` instead of explicit schema.
- **Version numbers without schema backing:** Template version "1.7" means nothing if there's no schema defining what 1.7 requires.
- **Monolithic schemas:** One huge schema for all templates; use composition with `$ref` instead.

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| JSON validation | String parsing/regex | JSON Schema + ajv | Edge cases (nested objects, optional fields, arrays) are subtle |
| Version comparison | String comparison | Semantic versioning | "1.10.0" > "1.9.0" but string comparison says otherwise |
| Schema composition | Copy-paste common sections | `$ref` to shared definitions | DRY; single source of truth |
| Deep JSON comparison | Manual property checks | jq or dedicated diff tools | Object key ordering, nested structures |

**Key insight:** JSON Schema validation is declarative and well-tested. Hand-rolling validation logic for template structure will inevitably miss edge cases and create maintenance burden.

## Common Pitfalls

### Pitfall 1: Schema Validates Structure But Not Behavior

**What goes wrong:** Schema passes validation but GPT ignores or misinterprets the rules.

**Why it happens:** JSON Schema validates JSON structure, not prompt effectiveness. A template can be structurally valid but behaviorally broken.

**How to avoid:**
- Schema is for structural gatekeeping only (typos, missing sections, wrong types)
- Behavioral validation requires manual testing in Custom GPT
- Don't expect schema to catch "rule is unclear" or "rule conflicts with GPT training"

**Warning signs:**
- Believing "schema passes = template works"
- Skipping Custom GPT testing after schema validation passes

### Pitfall 2: $ref Resolution Path Confusion

**What goes wrong:** Schema references don't resolve correctly; ajv reports "can't resolve reference."

**Why it happens:** `$ref` resolution is relative to the schema's base URI (`$id`), not the filesystem path. Local file references work differently than URL references.

**How to avoid:**
- Use `-r` flag to provide referenced schemas: `ajv validate -s main.schema.json -d data.json -r "shared/*.schema.json"`
- Set explicit `$id` in each schema file
- Test schema loading before committing

**Warning signs:**
- "Error: can't resolve reference" from ajv
- References work in one environment but not another

### Pitfall 3: Version Marker Theater

**What goes wrong:** Version markers exist but nobody checks them; drift happens anyway.

**Why it happens:** Markers are metadata without enforcement. Changing template version doesn't automatically validate against downstream expectations.

**How to avoid:**
- Create a version compatibility check script that runs during validation
- Document what version changes require (major = breaking contract, minor = additive, patch = fixes)
- Include version check in pre-commit workflow

**Warning signs:**
- Templates have version numbers but nobody knows what they mean
- Downstream templates updated without checking upstream version changes

### Pitfall 4: Over-Specified Schemas

**What goes wrong:** Schema is so strict it rejects valid variations; every small change requires schema update.

**Why it happens:** Trying to encode all rules in schema instead of focusing on structural requirements.

**How to avoid:**
- Schema validates structure (required sections, field types, array shapes)
- Schema does NOT validate content quality (rule clarity, behavior)
- Use `additionalProperties: true` for sections that may evolve
- Start minimal, add constraints as needed

**Warning signs:**
- Schema changes required for every template tweak
- Schema rejects templates that work correctly in Custom GPT

## Code Examples

### Validate a Single Template

```bash
# Source: ajv-cli official documentation
ajv validate -s schemas/skill_tree_builder.schema.json -d skill_tree_builder_template.json
```

### Validate with Referenced Schemas

```bash
# Source: ajv-cli -r flag documentation
ajv validate \
  -s schemas/skill_tree_to_content.schema.json \
  -d skill_tree_to_content_tree_template.json \
  -r "schemas/shared/*.schema.json"
```

### Validate All Templates (Pre-Commit Script)

```bash
#!/bin/bash
# validate_all.sh

SCHEMAS_DIR="schemas"
TEMPLATES=(
  "skill_tree_builder_template.json:skill_tree_builder.schema.json"
  "skill_tree_to_content_tree_template.json:skill_tree_to_content.schema.json"
  "skill_to_practice_exercises_template.json:skill_to_practice_exercises.schema.json"
  "skill_to_evaluation_exam_template.json:skill_to_evaluation_exam.schema.json"
  "custom_gpt_instructions.json:custom_gpt_instructions.schema.json"
)

for pair in "${TEMPLATES[@]}"; do
  template="${pair%%:*}"
  schema="${pair##*:}"

  echo "Validating $template..."
  ajv validate -s "$SCHEMAS_DIR/$schema" -d "$template" -r "$SCHEMAS_DIR/shared/*.schema.json"

  if [ $? -ne 0 ]; then
    echo "FAILED: $template"
    exit 1
  fi
done

echo "All templates valid"
```

### Minimal Template Schema Example

```json
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "$id": "skill_tree_builder.schema",
  "title": "Skill Tree Builder Template Schema",
  "type": "object",
  "required": ["meta", "inputs", "instruction", "output"],
  "properties": {
    "meta": {
      "type": "object",
      "required": ["name", "version"],
      "properties": {
        "name": { "type": "string", "const": "skill_tree_builder" },
        "version": { "type": "string", "pattern": "^\\d+\\.\\d+(\\.\\d+)?$" }
      }
    },
    "inputs": {
      "type": "object",
      "required": ["skill", "level", "context"],
      "properties": {
        "skill": { "type": "string" },
        "level": { "type": "string" },
        "context": { "type": "string" }
      }
    },
    "instruction": {
      "type": "object",
      "required": ["task", "hard_rules"]
    },
    "output": {
      "type": "object"
    }
  }
}
```

### Format Standards Schema

```json
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "$id": "format_standards.schema",
  "title": "Format Standards Schema",
  "type": "object",
  "required": ["version", "indentation", "list_style", "language_policy", "link_format"],
  "properties": {
    "version": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$"
    },
    "indentation": {
      "type": "object",
      "required": ["use_spaces_only", "spaces_per_level"],
      "properties": {
        "use_spaces_only": { "type": "boolean", "const": true },
        "spaces_per_level": { "type": "integer", "minimum": 1 },
        "tabs_forbidden": { "type": "boolean", "const": true }
      }
    },
    "list_style": {
      "type": "object",
      "required": ["all_levels"],
      "properties": {
        "all_levels": { "type": "string", "enum": ["-", "*", "+"] }
      }
    },
    "language_policy": {
      "type": "object",
      "required": ["instruction_language", "output_language"],
      "properties": {
        "instruction_language": { "type": "string", "enum": ["English", "Spanish"] },
        "output_language": { "type": "string", "enum": ["English", "Spanish"] },
        "exceptions": {
          "type": "array",
          "items": { "type": "string" }
        }
      }
    },
    "link_format": {
      "type": "object",
      "required": ["preferred", "forbidden"],
      "properties": {
        "preferred": { "type": "string" },
        "fallback": { "type": "string" },
        "forbidden": {
          "type": "array",
          "items": { "type": "string" }
        }
      }
    }
  }
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| No schema validation | JSON Schema validation | Always available | Catch structural errors before upload |
| Implicit contracts | Explicit contract schemas | Best practice | Prevents handoff corruption |
| Duplicated format rules | Centralized format_standards | Best practice | Single source of truth |
| Ad-hoc versioning | Semantic versioning with markers | Best practice | Detectable drift |

**Current best practice (2025-2026):**
- JSON Schema Draft-07 for broad compatibility (Draft 2020-12 if you need `unevaluatedProperties`)
- ajv-cli for command-line validation
- `$ref` for schema composition
- Version markers in schemas for contract compatibility

**Deprecated/outdated:**
- Draft-04: Still works but lacks features like `$defs` (uses `definitions` instead)
- Manual JSON validation: Error-prone, not maintainable

## Open Questions

1. **Where to store format_standards.json?**
   - What we know: Should be single source of truth
   - What's unclear: Embed in router? Separate file? Both have tradeoffs
   - Recommendation: Embed in router with schema validation; Custom GPT can read it directly

2. **Should schemas be uploaded to Custom GPT?**
   - What we know: Custom GPT Knowledge files accept JSON
   - What's unclear: Does GPT use schemas for self-validation? Likely not.
   - Recommendation: Schemas are for pre-upload validation only; don't upload to GPT

3. **How granular should version markers be?**
   - What we know: Need to detect when upstream changes break downstream
   - What's unclear: Per-field versions vs per-section vs per-template
   - Recommendation: Start with per-template versioning; add finer granularity if drift still happens

## Sources

### Primary (HIGH confidence)
- [ajv-cli official documentation](https://ajv.js.org/packages/ajv-cli.html) - validate command syntax, referenced schemas
- [ajv-cli GitHub](https://github.com/ajv-validator/ajv-cli) - installation, usage examples
- [JSON Schema Draft-07](https://json-schema.org/draft-07) - specification reference
- [JSON Schema Structuring](https://json-schema.org/understanding-json-schema/structuring) - $defs, $ref, bundling

### Secondary (MEDIUM confidence)
- [Ajv schema language guide](https://ajv.js.org/guide/schema-language.html) - Draft version recommendations
- [Schema-First JSON Contracts](https://coding-cloud.com/blog/json-schema-contracts) - microservice contract patterns
- [SchemaVer for semantic versioning](https://snowplow.io/blog/introducing-schemaver-for-semantic-versioning-of-schemas) - schema versioning alternatives

### Tertiary (LOW confidence)
- Prior project research: STACK.md, ARCHITECTURE.md, PITFALLS.md - project-specific context

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - ajv-cli and JSON Schema Draft-07 are well-documented, current
- Architecture: HIGH - patterns derived from official JSON Schema documentation
- Pitfalls: MEDIUM - based on prior research and general schema validation experience

**Research date:** 2026-02-05
**Valid until:** 2026-03-07 (30 days - stable tooling, unlikely to change)
