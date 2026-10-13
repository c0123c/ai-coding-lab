# [Proposal] Schema-Driven Artifact Validation


<!-- Archive scaffolding: the following dates and comment headings identify original GitHub content. -->


Source: https://github.com/Fission-AI/OpenSpec/issues/829

Author: acookienot

Created: 2026-03-11T07:18:30Z

Issue updatedAt: 2026-08-27T18:30:04Z


<!-- Original issue body begins. -->


# [Proposal] Schema-Driven Artifact Validation

## Problem

`openspec validate` only validates `spec` and `change` artifacts. The rules are hardcoded in [`validator.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts) and [`constants.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts). The validator never reads the schema.

Custom artifacts (`research.md`, `adr.md`, whatever a schema author creates) get zero validation. Built-in rules like `MIN_PURPOSE_LENGTH = 50` or the required `## Purpose` heading can't be tuned without editing the source. Adding a third artifact type means editing the source too; [`validateByType`](https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts) branches on two string literals (`'change'` / `'spec'`).

[`ArtifactSchema`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts) has `id`, `generates`, `description`, `template`, `instruction`, `requires`. No validation fields. There is nowhere for a schema author to say "this artifact needs these headings" or "this section must be at least N characters."

---

## Relationship to Existing Issues

### [#666](https://github.com/Fission-AI/OpenSpec/issues/666): Configurable Spec Format

Issue [#666](https://github.com/Fission-AI/OpenSpec/issues/666) proposes making the **parser** configurable: requirement/scenario header patterns, normative keywords, and section names. It also proposes `sections.required` and `sections.optional` fields on artifacts, which overlap with this proposal.

The `sections` fields in [#666](https://github.com/Fission-AI/OpenSpec/issues/666) configure the parser (how OpenSpec *reads* spec structure). This proposal's `validation` block configures enforcement (what `openspec validate` *checks* on any artifact). The field shapes are similar and should be unified in a final implementation. The work is separable regardless: this proposal doesn't touch parsers, and [#666](https://github.com/Fission-AI/OpenSpec/issues/666) doesn't address validation of non-spec artifacts.

This proposal uses a separate `validation` block. If [#666](https://github.com/Fission-AI/OpenSpec/issues/666) lands first and establishes an artifact-level `sections` block, these fields can nest there instead.

### [#777](https://github.com/Fission-AI/OpenSpec/issues/777): Hardcoded Artifact Patterns Override Schema `instruction` Field

Issue [#777](https://github.com/Fission-AI/OpenSpec/issues/777) is about skill orchestration ignoring `instruction` fields. This proposal is about the validator ignoring schema-declared rules. Different system, same root cause.

### [#783](https://github.com/Fission-AI/OpenSpec/issues/783): Cross-Artifact Quality Review

Semantic consistency checks across artifacts (contradictions, scope drift, duplication). This proposal handles per-artifact structural validation only. It can't detect "proposal says X but design says not-X." That's covered in Future Extensions below.

---

## Proposed Schema Extension

The `validation` block is optional on any artifact in [`schema.yaml`](https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml). Omit it to skip structural checks entirely.

```yaml
artifacts:
  - id: proposal
    generates: proposal.md
    template: proposal.md
    requires: []
    validation:
      sections:
        required: ["Why", "What Changes"]
        optional: ["Impact", "Capabilities"]
      minSectionLength:
        Why: 50
      requiredPatterns:
        - pattern: "\\*\\*BREAKING\\*\\*"
          description: "Breaking changes must be flagged."
          scope: "What Changes"
          level: "WARNING"

  - id: specs
    generates: "specs/**/*.md"
    template: spec.md
    requires: [proposal]
    validation:
      sections:
        required: ["Purpose", "Requirements"]
        optional: ["Scope", "Definitions"]
      minSectionLength:
        Purpose: 50

  - id: design
    generates: design.md
    template: design.md
    requires: [proposal]
    validation:
      sections:
        required: ["Context", "Decisions"]
        optional: ["Goals / Non-Goals", "Risks / Trade-offs", "Migration Plan", "Open Questions"]

  - id: tasks
    generates: tasks.md
    template: tasks.md
    requires: [specs, design]
    # no validation block = no structural checks
```

### Fields

| Field | Type | Default | What it does |
| --- | --- | --- | --- |
| `validation` | object | *(omitted)* | Validation config for this artifact. |
| `sections.required` | `string[]` | `[]` | Headings (`## Heading`) that must exist. |
| `sections.optional` | `string[]` | `[]` | Recognised headings. Any `##` heading not in `required` or `optional` triggers a WARNING. If `optional` is empty, no unknown-heading warnings are emitted. |
| `minSectionLength` | `Record<string, number>` | `{}` | Min character count for content under a named heading. |
| `requiredPatterns` | `object[]` | `[]` | Regex patterns that must match. |
| `requiredPatterns[].pattern` | `string` | *(required)* | The regex. |
| `requiredPatterns[].description` | `string` | *(required)* | Why the pattern matters (shown in error output). |
| `requiredPatterns[].scope` | `string` | whole doc | Limit the match to one section. |
| `requiredPatterns[].level` | `"ERROR" \| "WARNING" \| "INFO"` | `"ERROR"` | Severity when the pattern isn't found. |

---

## Affected Code

### [`types.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts)

Add an optional `validation` field to `ArtifactSchema` with Zod definitions for the fields above.

### [`schema.yaml`](https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml)

Add a `validation` block to `specs` that reproduces the current hardcoded behavior (backward compatibility baseline), and add blocks to `proposal` and `design` as new example declarations for schema authors.

### [`validator.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts)

New method: `validateArtifact(filePath, artifactDef)`. Parses markdown into a heading-to-content map, checks required sections, checks min lengths, checks patterns. Returns `ValidationIssue[]`.

`applySpecRules` and `applyChangeRules` stay untouched for now. Migrating them is a separate follow-up after this lands.

### [`validate.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts)

`validateByType` needs to load the active schema for the change being validated. The resolution chain already exists: each change stores its schema name in `.openspec.yaml` (via `change-metadata.ts`), then `resolveSchema` looks it up in project-local → user-override → package built-in order. Once the schema is loaded, the command iterates over artifacts that have a `validation` block, finds files matching `generates`, and calls `validateArtifact`.

### [`constants.ts`](https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts)

New message templates:

- `SECTION_MISSING: 'Required section "{section}" not found'`
- `SECTION_TOO_SHORT: 'Section "{section}" has {actual} characters, minimum is {min}'`
- `PATTERN_NOT_FOUND: 'Required pattern not found: {description}'`

---

## Backward Compatibility

Every artifact except `spec` and `change` has no structural checks. Omitting `validation` preserves that. Existing schemas work without modification.

`applySpecRules` and `applyChangeRules` stay in place. Both paths run and their issues are merged. `spec` and `change` validation is additive until a follow-up migrates the hardcoded logic.

---

## Future Extensions (Out of Scope)

- **Filename convention validation.** Enforce naming patterns on artifact files (e.g., `specs/{kebab-case}/spec.md`).
- **Filename ID uniqueness.** Prevent duplicate IDs across files under the same `generates` glob.
- **Migrate hardcoded rules to schema.** Moving `applySpecRules` / `applyChangeRules` into `validation` blocks makes the built-in schema fully self-describing. Partially blocked on [#666](https://github.com/Fission-AI/OpenSpec/issues/666).
- **Cross-artifact validation.** Rules that span artifacts (e.g., every capability in `proposal.md` needs a matching `specs/{name}/spec.md`) require a graph-aware validation pass. See [#783](https://github.com/Fission-AI/OpenSpec/issues/783).



<!-- Original issue body ends. Comments below retain exact publisher body strings. -->


## Comment 5409587872 — ohpauleez

Source: https://github.com/Fission-AI/OpenSpec/issues/829#issuecomment-5409587872

Created: 2026-08-25T11:17:28Z

Last edited: not supplied


This improvement would have a significant positive impact for my team's use of OpenSpec (as well as my personal use).

Our team embeds Alloy models within the spec.md files and we usually have a section at the end for the final state machine and verification (you can see this in an example project, [Devbox](https://github.com/ohpauleez/devbox/blob/main/openspec/specs/box-registry/spec.md)).  These projects typically use the [srs-driven](https://github.com/ohpauleez/openspec_srs-driven) schema (or some derived schema) and we'd like to control or extend the `validate` rules to encode how the schema is used in practice.

I'm more than happy to help however I can to get this change (or a related, approved design) integrated into OpenSpec.
