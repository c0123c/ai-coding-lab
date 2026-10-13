# 提案：以 Schema 驱动工件验证

社区提案讨论在 Schema 中定义工件验证规则，供 openspec validate 与工作流使用；正文与评论均按抓取时实际状态保存。

- 来源：GitHub Issues／OpenSpec
- 资料类型：社区Issue正文与全部评论
- 文档时间：发表（Issue创建）：2026-03-11；Issue活动更新：2026-08-27；抓取：2026-10-13
- 阅读状态：完整中文译文，由 AI 翻译；包括帖子原文与全部已保存评论。
- 使用边界：社区提案和评论；不能据此声称功能已经实现或这是维护者的正式推荐。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/issues/829>) · [原始文件](<../../../../原始抓取备份/原文/社区/GitHub Issues/OpenSpec/提案：以 Schema 驱动工件验证.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# ［提案］以 Schema 驱动工件验证

## 问题

`openspec validate` 只验证 `spec` 和 `change` 工件。规则硬编码在 [`validator.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts>) 和 [`constants.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts>) 中。验证器从不读取 Schema。

自定义工件（`research.md`、`adr.md`，或 Schema 作者创建的其他任何工件）完全得不到验证。诸如 `MIN_PURPOSE_LENGTH = 50` 或必须存在 `## Purpose` 标题这样的内置规则，只有编辑源码才能调整。增加第三种工件类型同样需要编辑源码；[`validateByType`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts>) 按两个字符串字面量（`'change'` / `'spec'`）分支处理。

[`ArtifactSchema`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts>) 有 `id`、`generates`、`description`、`template`、`instruction`、`requires`，没有验证字段。Schema 作者没有地方可以声明“这个工件需要这些标题”或“这个章节至少需要 N 个字符”。

---

## 与现有 Issue 的关系

### [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>)：可配置的规格格式

Issue [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) 提议让**解析器**可配置：包括需求/场景标题模式、规范性关键词和章节名称。它还提议在工件上添加 `sections.required` 和 `sections.optional` 字段，与本提案有重叠。

[#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) 的 `sections` 字段配置解析器（OpenSpec 如何**读取**规格结构）。本提案的 `validation` 区块配置规则执行（`openspec validate` 在任何工件上**检查**什么）。字段形式相似，最终实现应该将其统一。无论如何，这两部分工作都可以分别推进：本提案不涉及解析器，[#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) 也没有处理非规格工件的验证。

本提案使用单独的 `validation` 区块。如果 [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) 先落地并建立了工件级 `sections` 区块，这些字段也可以改为嵌套在那里。

### [#777](<https://github.com/Fission-AI/OpenSpec/issues/777>)：硬编码的工件模式覆盖 Schema 的 `instruction` 字段

Issue [#777](<https://github.com/Fission-AI/OpenSpec/issues/777>) 讨论 Skill 编排忽略 `instruction` 字段的问题。本提案讨论验证器忽略 Schema 声明规则的问题。它们处于不同系统，但根因相同。

### [#783](<https://github.com/Fission-AI/OpenSpec/issues/783>)：跨工件质量审阅

它涉及工件之间的语义一致性检查（矛盾、范围漂移、重复）。本提案只处理单个工件的结构验证，无法检测“proposal 说 X，而 design 说非 X”。下文“未来扩展”会说明这一点。

---

## 提议的 Schema 扩展

[`schema.yaml`](<https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml>) 中的任意工件都可以选择添加 `validation` 区块。省略它就完全跳过结构检查。

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

### 字段

| 字段 | 类型 | 默认值 | 作用 |
| --- | --- | --- | --- |
| `validation` | object | *（省略）* | 该工件的验证配置。 |
| `sections.required` | `string[]` | `[]` | 必须存在的标题（`## Heading`）。 |
| `sections.optional` | `string[]` | `[]` | 可识别的标题。任何未出现在 `required` 或 `optional` 中的 `##` 标题都会触发 WARNING。如果 `optional` 为空，不会发出未知标题警告。 |
| `minSectionLength` | `Record<string, number>` | `{}` | 指定标题下内容的最小字符数。 |
| `requiredPatterns` | `object[]` | `[]` | 必须匹配的正则表达式模式。 |
| `requiredPatterns[].pattern` | `string` | *（必填）* | 正则表达式。 |
| `requiredPatterns[].description` | `string` | *（必填）* | 模式为何重要（显示在错误输出中）。 |
| `requiredPatterns[].scope` | `string` | 整篇文档 | 将匹配范围限制在一个章节。 |
| `requiredPatterns[].level` | `"ERROR" \| "WARNING" \| "INFO"` | `"ERROR"` | 未找到模式时的严重程度。 |

---

## 受影响的代码

### [`types.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts>)

为 `ArtifactSchema` 添加一个可选的 `validation` 字段，并为上述字段提供 Zod 定义。

### [`schema.yaml`](<https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml>)

为 `specs` 添加一个 `validation` 区块，复现当前硬编码行为（作为向后兼容基线）；为 `proposal` 和 `design` 添加区块，作为给 Schema 作者的新声明示例。

### [`validator.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts>)

新增方法：`validateArtifact(filePath, artifactDef)`。它把 Markdown 解析为标题到内容的映射，检查必需章节、最小长度和模式，返回 `ValidationIssue[]`。

暂时保持 `applySpecRules` 和 `applyChangeRules` 不变。它们的迁移属于本提案落地之后另行开展的后续工作。

### [`validate.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts>)

`validateByType` 需要加载被验证变更的当前 Schema。解析链已经存在：每个变更会把 Schema 名称存储在 `.openspec.yaml` 中（通过 `change-metadata.ts`），然后 `resolveSchema` 按“项目本地 → 用户覆盖 → 软件包内置”的顺序查找。加载 Schema 后，该命令遍历具有 `validation` 区块的工件，找到匹配 `generates` 的文件，并调用 `validateArtifact`。

### [`constants.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts>)

新增消息模板：

- `SECTION_MISSING: 'Required section "{section}" not found'`
- `SECTION_TOO_SHORT: 'Section "{section}" has {actual} characters, minimum is {min}'`
- `PATTERN_NOT_FOUND: 'Required pattern not found: {description}'`

---

## 向后兼容

目前，除了 `spec` 和 `change`，其他所有工件都没有结构检查。省略 `validation` 会保留这一行为。现有 Schema 无需修改即可工作。

`applySpecRules` 和 `applyChangeRules` 保持原样。两条路径都会运行，其产生的问题会合并。在后续工作迁移硬编码逻辑之前，`spec` 和 `change` 的验证是增量叠加的。

---

## 未来扩展（不在本次范围内）

- **文件名约定验证。** 对工件文件强制执行命名模式（例如 `specs/{kebab-case}/spec.md`）。
- **文件名 ID 的唯一性。** 防止同一个 `generates` glob 下的文件之间出现重复 ID。
- **将硬编码规则迁入 Schema。** 把 `applySpecRules` / `applyChangeRules` 移到 `validation` 区块中，可以让内置 Schema 完全自描述。这部分工作在一定程度上受 [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) 阻塞。
- **跨工件验证。** 跨越多个工件的规则（例如 `proposal.md` 中的每个 capability 都需要一个对应的 `specs/{name}/spec.md`）需要一轮能够理解图结构的验证。参见 [#783](<https://github.com/Fission-AI/OpenSpec/issues/783>)。

## 评论 5409587872 — ohpauleez

- [原评论](<https://github.com/Fission-AI/OpenSpec/issues/829#issuecomment-5409587872>)
- 创建：2026-08-25T11:17:28Z
- 最后编辑时间：原页未提供

这项改进将对我们团队使用 OpenSpec（以及我个人使用）产生显著的积极影响。

我们的团队会在 spec.md 文件中嵌入 Alloy 模型，并通常在末尾设置一个章节来放最终的状态机和验证（可以在示例项目 [Devbox](<https://github.com/ohpauleez/devbox/blob/main/openspec/specs/box-registry/spec.md>) 中看到）。这些项目通常使用 [srs-driven](<https://github.com/ohpauleez/openspec_srs-driven>) Schema（或某个派生 Schema）；我们希望控制或扩展 `validate` 规则，把 Schema 在实际中的使用方式编码进去。

我非常愿意尽我所能，帮助把这一变更（或相关的、已获批准的设计）集成进 OpenSpec。


## 原文对照

<details>
<summary>展开完整原文</summary>

# [Proposal] Schema-Driven Artifact Validation


<!-- Archive scaffolding: the following dates and comment headings identify original GitHub content. -->


Source: https://github.com/Fission-AI/OpenSpec/issues/829

Author: acookienot

Created: 2026-03-11T07:18:30Z

Issue updatedAt: 2026-08-27T18:30:04Z


<!-- Original issue body begins. -->


# [Proposal] Schema-Driven Artifact Validation

## Problem

`openspec validate` only validates `spec` and `change` artifacts. The rules are hardcoded in [`validator.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts>) and [`constants.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts>). The validator never reads the schema.

Custom artifacts (`research.md`, `adr.md`, whatever a schema author creates) get zero validation. Built-in rules like `MIN_PURPOSE_LENGTH = 50` or the required `## Purpose` heading can't be tuned without editing the source. Adding a third artifact type means editing the source too; [`validateByType`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts>) branches on two string literals (`'change'` / `'spec'`).

[`ArtifactSchema`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts>) has `id`, `generates`, `description`, `template`, `instruction`, `requires`. No validation fields. There is nowhere for a schema author to say "this artifact needs these headings" or "this section must be at least N characters."

---

## Relationship to Existing Issues

### [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>): Configurable Spec Format

Issue [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) proposes making the **parser** configurable: requirement/scenario header patterns, normative keywords, and section names. It also proposes `sections.required` and `sections.optional` fields on artifacts, which overlap with this proposal.

The `sections` fields in [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) configure the parser (how OpenSpec *reads* spec structure). This proposal's `validation` block configures enforcement (what `openspec validate` *checks* on any artifact). The field shapes are similar and should be unified in a final implementation. The work is separable regardless: this proposal doesn't touch parsers, and [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) doesn't address validation of non-spec artifacts.

This proposal uses a separate `validation` block. If [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>) lands first and establishes an artifact-level `sections` block, these fields can nest there instead.

### [#777](<https://github.com/Fission-AI/OpenSpec/issues/777>): Hardcoded Artifact Patterns Override Schema `instruction` Field

Issue [#777](<https://github.com/Fission-AI/OpenSpec/issues/777>) is about skill orchestration ignoring `instruction` fields. This proposal is about the validator ignoring schema-declared rules. Different system, same root cause.

### [#783](<https://github.com/Fission-AI/OpenSpec/issues/783>): Cross-Artifact Quality Review

Semantic consistency checks across artifacts (contradictions, scope drift, duplication). This proposal handles per-artifact structural validation only. It can't detect "proposal says X but design says not-X." That's covered in Future Extensions below.

---

## Proposed Schema Extension

The `validation` block is optional on any artifact in [`schema.yaml`](<https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml>). Omit it to skip structural checks entirely.

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

### [`types.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/artifact-graph/types.ts>)

Add an optional `validation` field to `ArtifactSchema` with Zod definitions for the fields above.

### [`schema.yaml`](<https://github.com/Fission-AI/OpenSpec/blob/main/schemas/spec-driven/schema.yaml>)

Add a `validation` block to `specs` that reproduces the current hardcoded behavior (backward compatibility baseline), and add blocks to `proposal` and `design` as new example declarations for schema authors.

### [`validator.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/validator.ts>)

New method: `validateArtifact(filePath, artifactDef)`. Parses markdown into a heading-to-content map, checks required sections, checks min lengths, checks patterns. Returns `ValidationIssue[]`.

`applySpecRules` and `applyChangeRules` stay untouched for now. Migrating them is a separate follow-up after this lands.

### [`validate.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/commands/validate.ts>)

`validateByType` needs to load the active schema for the change being validated. The resolution chain already exists: each change stores its schema name in `.openspec.yaml` (via `change-metadata.ts`), then `resolveSchema` looks it up in project-local → user-override → package built-in order. Once the schema is loaded, the command iterates over artifacts that have a `validation` block, finds files matching `generates`, and calls `validateArtifact`.

### [`constants.ts`](<https://github.com/Fission-AI/OpenSpec/blob/main/src/core/validation/constants.ts>)

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
- **Migrate hardcoded rules to schema.** Moving `applySpecRules` / `applyChangeRules` into `validation` blocks makes the built-in schema fully self-describing. Partially blocked on [#666](<https://github.com/Fission-AI/OpenSpec/issues/666>).
- **Cross-artifact validation.** Rules that span artifacts (e.g., every capability in `proposal.md` needs a matching `specs/{name}/spec.md`) require a graph-aware validation pass. See [#783](<https://github.com/Fission-AI/OpenSpec/issues/783>).



<!-- Original issue body ends. Comments below retain exact publisher body strings. -->


## Comment 5409587872 — ohpauleez

Source: https://github.com/Fission-AI/OpenSpec/issues/829#issuecomment-5409587872

Created: 2026-08-25T11:17:28Z

Last edited: not supplied


This improvement would have a significant positive impact for my team's use of OpenSpec (as well as my personal use).

Our team embeds Alloy models within the spec.md files and we usually have a section at the end for the final state machine and verification (you can see this in an example project, [Devbox](<https://github.com/ohpauleez/devbox/blob/main/openspec/specs/box-registry/spec.md>)).  These projects typically use the [srs-driven](<https://github.com/ohpauleez/openspec_srs-driven>) schema (or some derived schema) and we'd like to control or extend the `validate` rules to encode how the schema is used in practice.

I'm more than happy to help however I can to get this change (or a related, approved design) integrated into OpenSpec.


</details>
