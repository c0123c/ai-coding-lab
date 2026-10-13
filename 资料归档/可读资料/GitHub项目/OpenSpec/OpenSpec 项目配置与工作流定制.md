# OpenSpec 项目配置与工作流定制

介绍项目配置、定制 Schema 与全局覆盖三层定制方式，包含上下文规则、默认 Schema、解析顺序和配置示例。

- 来源：OpenSpec
- 资料类型：配置与工作流定制指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/customization.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/customization.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 定制化

OpenSpec提供三个级别的定制：

|级别|它的作用|最适合|
|-------|--------------|----------|
|**项目配置**|设置默认值，注入context/rules|大多数球队|
|**自定义架构**|定义您自己的工作流程工件|具有独特流程的团队|
|**全局覆盖**|跨所有项目共享架构|高级用户|

---

## 项目配置

`openspec/config.yaml` 文件是为您的团队自定义 OpenSpec 的最简单方法。它可以让您：

- **设置默认架构** - 在每个命令上跳过 `--schema`
- **注入项目上下文** - AI查看您的技术堆栈、约定等。
- **添加每个工件规则** - 特定工件的自定义规则
- **添加每个操作指南** - 应用和归档工作的咨询首选项
- **记住集成选择** - 例如[GitHub Copilot 云编码 agent](<OpenSpec 支持的 AI 工具与调用方式.md#github-copilot云编码agent>) 选择加入

### 快速设置

```bash
openspec init
```

这将引导您以交互方式创建配置。或者手动创建一个：

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js, PostgreSQL
  API style: RESTful, documented in docs/api.md
  Testing: Jest + React Testing Library
  We value backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format
    - Reference existing patterns before inventing new ones

operations:
  apply:
    guidance:
      - Run focused tests before the full suite
  archive:
    guidance:
      - Keep the completion summary concise

# Set by `openspec init` when you choose (or decline) the GitHub Copilot
# cloud coding agent; controls whether `init`/`update` generate its files.
githubCopilot:
  cloudAgent: false
```

### 它是如何运作的

**默认架构：**

```bash
# Without config
openspec new change my-feature --schema spec-driven

# With config - schema is automatic
openspec new change my-feature
```

**上下文和规则注入：**

生成任何工件时，您的上下文和规则将被注入到 AI 提示符中：

```xml
<context>
Tech stack: TypeScript, React, Node.js, PostgreSQL
...
</context>

<rules>
- Include rollback plan
- Identify affected teams
</rules>

<template>
[Schema's built-in template]
</template>
```

- **上下文**出现在ALL工件中
- **规则** ONLY出现匹配工件

**操作指导：**

`operations.apply.guidance` 和 `operations.archive.guidance` 是关于 agent 应如何执行这些操作的咨询指令的可选数组 
。它们
与`rules`分开：操作指南不限制工件内容，
并且工件规则永远不会被重新标记为操作指南。

应用并归档在执行时获取这些输入：

```bash
openspec instructions apply --change my-feature --json
openspec instructions archive --change my-feature --json
```

两个表面都返回当前项目 `context` 和匹配 
`operationGuidance` 作为单独的可选字段。每次调用都会从解析的根中读取一个新的
快照。选择 `--store <id>` 时，更改、
 上下文和指导均来自该存储而不是当前存储库。
 存档指令命令是只读的：它不会检查或合并增量 
 规范、写入主要规范、移动更改或运行静态存档工作流程。

项目上下文是必需的提示级输入。生成的工作流程读取它并
应用相关的项目事实、约定和约束。操作指导是
可选的附加建议：工作流程考虑每个条目并遵循
适用且与内置工作流程兼容的条目。

这两个字段仍然与 CLI- 控制状态、解析路径、内置 
 步骤、显式用户选择和工件规则分开。工作流报告上下文
冲突，同时保留控制值。它不遵循不适用的
或相互冲突的指导，并解释了原因。这两个字段都不是可执行的检查，
并且工作流程不会将其文本复制到实施文件、规范、变更
工件或摘要中，除非用户单独请求该内容。

**存档和规范同步输入安全：**

存档、批量存档和独立同步使用 `openspec status --json` 中的 
`artifactPaths.specs.existingOutputPaths` 作为 
 唯一的 delta-spec 源。没有 `specs` 工件的模式，或者 
 具体输出列表为空的更改，没有任何可同步的内容；不使用其他工件
来推断增量规格。

在语义合并写入主要规范之前，工作流程会消耗当前的 
`openspec instructions specs --change <name> --json` 输出。返回的
`specs`规则仅约束该合并产生的主要规格。单个存档
将该快照传递到内联同步中，独立同步直接获取它，而
批量存档在第一次规范写入之前获取每个所需的快照。 
非零或无效的 JSON archive/specs 指令响应是查找失败，
不是空输入：工作流程在受影响的规范写入或更改之前停止
移动（对于批量存档，在任何批量写入或移动之前）。

此配置不会更改存档执行阶段、用户提示、
文件系统操作、语义合并所有权、直接`openspec archive`
命令或工件`rules`的结构和输出。

### 模式解析顺序

当 OpenSpec 需要模式时，它按以下顺序检查：

1. CLI 标志：`--schema <name>`
2. 更改元数据（更改文件夹中的`.openspec.yaml`）
3. 项目配置（`openspec/config.yaml`）
4. 默认（`spec-driven`）

---

## 自定义模式

当项目配置不够时，可以使用完全自定义的工作流程创建您自己的架构。自定义架构位于项目的 `openspec/schemas/` 目录中，并由您的代码进行版本控制。

```text
your-project/
├── openspec/
│   ├── config.yaml        # Project config
│   ├── schemas/           # Custom schemas live here
│   │   └── my-workflow/
│   │       ├── schema.yaml
│   │       └── templates/
│   └── changes/           # Your changes
└── src/
```

### 分叉现有架构

最快的自定义方法是分叉内置模式：

```bash
openspec schema fork spec-driven my-workflow
```

这会将整个 `spec-driven` 架构复制到 `openspec/schemas/my-workflow/`，您可以在其中自由编辑。

**您将得到：**

```text
openspec/schemas/my-workflow/
├── schema.yaml           # Workflow definition
└── templates/
    ├── proposal.md       # Template for proposal artifact
    ├── spec.md           # Template for specs
    ├── design.md         # Template for design
    └── tasks.md          # Template for tasks
```

现在编辑 `schema.yaml` 以更改工作流程，或编辑模板以更改 AI 生成的内容。

### 从头开始创建架构

对于全新的工作流程：

```bash
# Interactive
openspec schema init research-first

# Non-interactive
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

### 模式结构

架构定义了工作流程中的工件以及它们如何相互依赖：

```yaml
# openspec/schemas/my-workflow/schema.yaml
name: my-workflow
version: 1
description: My team's custom workflow

artifacts:
  - id: proposal
    generates: proposal.md
    description: Initial proposal document
    template: proposal.md
    instruction: |
      Create a proposal that explains WHY this change is needed.
      Focus on the problem, not the solution.
    requires: []

  - id: design
    generates: design.md
    description: Technical design
    template: design.md
    instruction: |
      Create a design document explaining HOW to implement.
    requires:
      - proposal    # Can't create design until proposal exists

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires:
      - design

apply:
  requires: [tasks]
  tracks: tasks.md
```

**关键字段：**

|领域|目的|
|-------|---------|
| `id` |唯一标识符，用于命令和规则|
| `generates` |输出文件名（支持像`specs/**/*.md`这样的glob）|
| `template` |`templates/`目录中的模板文件|
| `instruction` |AI 创建此工件的说明|
| `requires` |依赖关系 - 哪些工件必须首先存在|

按照您希望的写入顺序列出工件。 `requires`决定什么是
可能； `artifacts:` 列表的顺序决定了当 
 多个工件同时准备就绪时，首先出现的内容。

### 模板

模板是指导 AI 的 Markdown 文件。创建该工件时，它们会被注入到提示中。

```markdown
<!-- templates/proposal.md -->
## Why

<!-- Explain the motivation for this change. What problem does this solve? -->

## What Changes

<!-- Describe what will change. Be specific about new capabilities or modifications. -->

## Impact

<!-- Affected code, APIs, dependencies, systems -->
```

模板可以包括：
- 应填写 AI 的节标题
- HTML 评论以及 AI 的指导
- 显示预期结构的示例格式

### 验证您的架构

在使用自定义架构之前，请验证它：

```bash
openspec schema validate my-workflow
```

这会检查：
- `schema.yaml` 语法正确
- 所有引用的模板都存在
- 无循环依赖
- 工件 ID 有效

### 使用您的自定义架构

创建后，将您的架构用于：

```bash
# Specify on command
openspec new change feature --schema my-workflow

# Or set as default in config.yaml
schema: my-workflow
```

### 调试模式解析

不确定正在使用哪个架构？检查：

```bash
# See where a specific schema resolves from
openspec schema which my-workflow

# List all available schemas
openspec schema which --all
```

输出显示它是来自您的项目、用户目录还是包：

```text
Schema: my-workflow
Source: project
Path: /path/to/project/openspec/schemas/my-workflow
```

---

> **注意：** OpenSpec 还支持 `~/.local/share/openspec/schemas/` 中的用户级架构以便跨项目共享，但建议使用 `openspec/schemas/` 中的项目级架构，因为它们是由代码进行版本控制的。

---

## 示例

### 快速迭代工作流程

用于快速迭代的最小工作流程：

```yaml
# openspec/schemas/rapid/schema.yaml
name: rapid
version: 1
description: Fast iteration with minimal overhead

artifacts:
  - id: proposal
    generates: proposal.md
    description: Quick proposal
    template: proposal.md
    instruction: |
      Create a brief proposal for this change.
      Focus on what and why, skip detailed specs.
    requires: []

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires: [proposal]

apply:
  requires: [tasks]
  tracks: tasks.md
```

### 添加评论工件

分叉默认值并添加审核步骤：

```bash
openspec schema fork spec-driven with-review
```

然后编辑`schema.yaml`添加：

```yaml
  - id: review
    generates: review.md
    description: Pre-implementation review checklist
    template: review.md
    instruction: |
      Create a review checklist based on the design.
      Include security, performance, and testing considerations.
    requires:
      - design

  - id: tasks
    # ... existing tasks config ...
    requires:
      - specs
      - design
      - review    # Now tasks require review too
```

---

## 社区模式

OpenSpec 还支持通过独立存储库分发的社区维护模式。这些提供了将 OpenSpec 与其他工具或系统集成的固执己见的工作流程，类似于 [github/spec-kit 的社区扩展目录 ](<https://github.com/github/spec-kit/tree/main/extensions>) 对于规范套件的工作方式。

社区模式不提供到 OpenSpec 核心中 - 它们以自己的发布节奏存在于自己的存储库中。要使用其中一个，请将模式包复制到项目的 `openspec/schemas/<schema-name>/` 目录中（每个存储库的 README 都有安装说明）。

|模式|维护者|存储库|描述|
|--------|-----------|-----------|-------------|
| `intent-driven` |@harikrishnan83| [intent-driven-dev/openspec-schemas](<https://github.com/intent-driven-dev/openspec-schemas/tree/main/openspec/schemas/intent-driven>) |在实施之前捕获变更意图、可观察的行为、技术设计和持久的架构决策。添加本地变更 ADR 审核清单，并将合格的长期决策写入不可变、可取代的 ADR。|
| `superpowers-bridge` | @JiangWay | [JiangWay/openspec-schemas](<https://github.com/JiangWay/openspec-schemas/tree/main/superpowers-bridge>) |将 OpenSpec 的工件治理与 [obra/superpowers](<https://github.com/obra/superpowers>) 执行技能（集思广益、编写计划、TDD 通过子代理、代码审查、整理）集成。添加了证据优先的 `retrospective` 工件，填补了 Superpowers 本身未涵盖的空白。|
| `nanopm` |@nmrtn| [nmrtn/nanopm](<https://github.com/nmrtn/nanopm/tree/main/openspec-schema>) |PM-第一个工作流程。在实施上游运行 [nanopm](<https://github.com/nmrtn/nanopm>) 的规划管道（审核→策略→路线图→PRD）。将产品规划与 OpenSpec 的规范驱动工程工作流程联系起来。工件从 `.nanopm/` 中读取（如果存在）——提案源自审计，设计源自策略，任务源自 PRD 细分。|
| `e2e-runbooks` |@Lukk17| [Lukk17/openspec-schemas](<https://github.com/Lukk17/openspec-schemas/tree/master/openspec/schemas/e2e-runbooks>) |能力级端到端测试运行手册。每个功能都会获得一个不可变的规范、一个不可变的任务模板以及每次执行的一个带时间戳的运行记录。断言只是可观察的行为（HTTP 状态、响应正文、持久状态 - 从不记录子字符串）；每次运行都会记录 start/end UTC、持续时间和最佳估计 LLM 代币消耗。|
| `anvil` |@jikkujoyce| [jikkujoyce/openspec-schemas](<https://github.com/jikkujoyce/openspec-schemas/tree/main/schemas/anvil>) | 规范驱动的工作流程 TDD 纪律和对抗性审查步骤。流程： `proposal` → `specs` → `design` → `review` → `test-plan` → `tasks` → `apply` → `verify`. `review` 由新上下文、只读审阅者编写（如果可用，则为第二个模型）并发出 `VERDICT:` 线告诉 agent 到门 `test-plan`, `tasks`, 和 `apply`; OpenSpec 只检查工件是否存在，所以用你自己的门来强制执行 CI 或钩子。 `test-plan` 将每个规范场景映射到命名测试并兼作 red/green 分类帐 `verify` 审计。 |

> 想要贡献社区架构吗？通过指向您的存储库的链接打开问题，或提交 PR 在此表中添加一行。

---

## 另请参阅

- [CLI 参考：架构命令](<OpenSpec 命令行参考.md#架构命令>) - 完整命令文档


## 原文对照

<details>
<summary>展开完整原文</summary>

# Customization

OpenSpec provides three levels of customization:

| Level | What it does | Best for |
|-------|--------------|----------|
| **Project Config** | Set defaults, inject context/rules | Most teams |
| **Custom Schemas** | Define your own workflow artifacts | Teams with unique processes |
| **Global Overrides** | Share schemas across all projects | Power users |

---

## Project Configuration

The `openspec/config.yaml` file is the easiest way to customize OpenSpec for your team. It lets you:

- **Set a default schema** - Skip `--schema` on every command
- **Inject project context** - AI sees your tech stack, conventions, etc.
- **Add per-artifact rules** - Custom rules for specific artifacts
- **Add per-operation guidance** - Advisory preferences for apply and archive work
- **Remember integration choices** - e.g. the [GitHub Copilot cloud coding agent](<OpenSpec 支持的 AI 工具与调用方式.md#github-copilot-cloud-coding-agent>) opt-in

### Quick Setup

```bash
openspec init
```

This walks you through creating a config interactively. Or create one manually:

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js, PostgreSQL
  API style: RESTful, documented in docs/api.md
  Testing: Jest + React Testing Library
  We value backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format
    - Reference existing patterns before inventing new ones

operations:
  apply:
    guidance:
      - Run focused tests before the full suite
  archive:
    guidance:
      - Keep the completion summary concise

# Set by `openspec init` when you choose (or decline) the GitHub Copilot
# cloud coding agent; controls whether `init`/`update` generate its files.
githubCopilot:
  cloudAgent: false
```

### How It Works

**Default schema:**

```bash
# Without config
openspec new change my-feature --schema spec-driven

# With config - schema is automatic
openspec new change my-feature
```

**Context and rules injection:**

When generating any artifact, your context and rules are injected into the AI prompt:

```xml
<context>
Tech stack: TypeScript, React, Node.js, PostgreSQL
...
</context>

<rules>
- Include rollback plan
- Identify affected teams
</rules>

<template>
[Schema's built-in template]
</template>
```

- **Context** appears in ALL artifacts
- **Rules** ONLY appear for the matching artifact

**Operation guidance:**

`operations.apply.guidance` and `operations.archive.guidance` are optional arrays
of advisory instructions for how an agent should conduct those operations. They
are separate from `rules`: operation guidance does not constrain artifact content,
and artifact rules are never relabeled as operation guidance.

Apply and archive fetch these inputs at execution time:

```bash
openspec instructions apply --change my-feature --json
openspec instructions archive --change my-feature --json
```

Both surfaces return current project `context` and matching
`operationGuidance` as separate optional fields. Each invocation reads a fresh
snapshot from the resolved root. When `--store <id>` is selected, the change,
context, and guidance all come from that store rather than the current repository.
The archive instruction command is read-only: it does not inspect or merge delta
specs, write main specs, move the change, or run the static archive workflow.

Project context is a required prompt-level input. Generated workflows read it and
apply relevant project facts, conventions, and constraints. Operation guidance is
optional additive advice: workflows consider every entry and follow entries that
are applicable and compatible with the built-in workflow.

Both fields remain separate from CLI-controlled state, resolved paths, built-in
steps, explicit user choices, and artifact rules. A workflow reports context
conflicts while preserving the controlling value. It does not follow inapplicable
or conflicting guidance and explains why. Neither field is an enforceable check,
and workflows do not copy their text into implementation files, specs, change
artifacts, or summaries unless the user separately requests that content.

**Archive and spec-sync input safety:**

Archive, bulk archive, and standalone sync use
`artifactPaths.specs.existingOutputPaths` from `openspec status --json` as the
only delta-spec source. A schema without a `specs` artifact, or a change whose
concrete output list is empty, has nothing to sync; other artifacts are not used
to infer delta specs.

Before a semantic merge writes a main spec, the workflow consumes current
`openspec instructions specs --change <name> --json` output. The returned
`specs` rules constrain only the main specs produced by that merge. Single archive
passes that snapshot into inline sync, standalone sync fetches it directly, and
bulk archive obtains every required snapshot before its first spec write. A
non-zero or invalid JSON archive/specs instruction response is a lookup failure,
not an empty input: the workflow stops before the affected spec write or change
move (for bulk archive, before any batch write or move).

This configuration does not change archive execution phases, user prompts,
filesystem operations, semantic merge ownership, the direct `openspec archive`
command, or the structure and output of artifact `rules`.

### Schema Resolution Order

When OpenSpec needs a schema, it checks in this order:

1. CLI flag: `--schema <name>`
2. Change metadata (`.openspec.yaml` in the change folder)
3. Project config (`openspec/config.yaml`)
4. Default (`spec-driven`)

---

## Custom Schemas

When project config isn't enough, create your own schema with a completely custom workflow. Custom schemas live in your project's `openspec/schemas/` directory and are version-controlled with your code.

```text
your-project/
├── openspec/
│   ├── config.yaml        # Project config
│   ├── schemas/           # Custom schemas live here
│   │   └── my-workflow/
│   │       ├── schema.yaml
│   │       └── templates/
│   └── changes/           # Your changes
└── src/
```

### Fork an Existing Schema

The fastest way to customize is to fork a built-in schema:

```bash
openspec schema fork spec-driven my-workflow
```

This copies the entire `spec-driven` schema to `openspec/schemas/my-workflow/` where you can edit it freely.

**What you get:**

```text
openspec/schemas/my-workflow/
├── schema.yaml           # Workflow definition
└── templates/
    ├── proposal.md       # Template for proposal artifact
    ├── spec.md           # Template for specs
    ├── design.md         # Template for design
    └── tasks.md          # Template for tasks
```

Now edit `schema.yaml` to change the workflow, or edit templates to change what AI generates.

### Create a Schema from Scratch

For a completely fresh workflow:

```bash
# Interactive
openspec schema init research-first

# Non-interactive
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

### Schema Structure

A schema defines the artifacts in your workflow and how they depend on each other:

```yaml
# openspec/schemas/my-workflow/schema.yaml
name: my-workflow
version: 1
description: My team's custom workflow

artifacts:
  - id: proposal
    generates: proposal.md
    description: Initial proposal document
    template: proposal.md
    instruction: |
      Create a proposal that explains WHY this change is needed.
      Focus on the problem, not the solution.
    requires: []

  - id: design
    generates: design.md
    description: Technical design
    template: design.md
    instruction: |
      Create a design document explaining HOW to implement.
    requires:
      - proposal    # Can't create design until proposal exists

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires:
      - design

apply:
  requires: [tasks]
  tracks: tasks.md
```

**Key fields:**

| Field | Purpose |
|-------|---------|
| `id` | Unique identifier, used in commands and rules |
| `generates` | Output filename (supports globs like `specs/**/*.md`) |
| `template` | Template file in `templates/` directory |
| `instruction` | AI instructions for creating this artifact |
| `requires` | Dependencies - which artifacts must exist first |

List artifacts in the order you want them written. `requires` decides what is
possible; the order of the `artifacts:` list decides what comes first when
several artifacts are ready at once.

### Templates

Templates are markdown files that guide the AI. They're injected into the prompt when creating that artifact.

```markdown
<!-- templates/proposal.md -->
## Why

<!-- Explain the motivation for this change. What problem does this solve? -->

## What Changes

<!-- Describe what will change. Be specific about new capabilities or modifications. -->

## Impact

<!-- Affected code, APIs, dependencies, systems -->
```

Templates can include:
- Section headers the AI should fill in
- HTML comments with guidance for the AI
- Example formats showing expected structure

### Validate Your Schema

Before using a custom schema, validate it:

```bash
openspec schema validate my-workflow
```

This checks:
- `schema.yaml` syntax is correct
- All referenced templates exist
- No circular dependencies
- Artifact IDs are valid

### Use Your Custom Schema

Once created, use your schema with:

```bash
# Specify on command
openspec new change feature --schema my-workflow

# Or set as default in config.yaml
schema: my-workflow
```

### Debug Schema Resolution

Not sure which schema is being used? Check with:

```bash
# See where a specific schema resolves from
openspec schema which my-workflow

# List all available schemas
openspec schema which --all
```

Output shows whether it's from your project, user directory, or the package:

```text
Schema: my-workflow
Source: project
Path: /path/to/project/openspec/schemas/my-workflow
```

---

> **Note:** OpenSpec also supports user-level schemas at `~/.local/share/openspec/schemas/` for sharing across projects, but project-level schemas in `openspec/schemas/` are recommended since they're version-controlled with your code.

---

## Examples

### Rapid Iteration Workflow

A minimal workflow for quick iterations:

```yaml
# openspec/schemas/rapid/schema.yaml
name: rapid
version: 1
description: Fast iteration with minimal overhead

artifacts:
  - id: proposal
    generates: proposal.md
    description: Quick proposal
    template: proposal.md
    instruction: |
      Create a brief proposal for this change.
      Focus on what and why, skip detailed specs.
    requires: []

  - id: tasks
    generates: tasks.md
    description: Implementation checklist
    template: tasks.md
    requires: [proposal]

apply:
  requires: [tasks]
  tracks: tasks.md
```

### Adding a Review Artifact

Fork the default and add a review step:

```bash
openspec schema fork spec-driven with-review
```

Then edit `schema.yaml` to add:

```yaml
  - id: review
    generates: review.md
    description: Pre-implementation review checklist
    template: review.md
    instruction: |
      Create a review checklist based on the design.
      Include security, performance, and testing considerations.
    requires:
      - design

  - id: tasks
    # ... existing tasks config ...
    requires:
      - specs
      - design
      - review    # Now tasks require review too
```

---

## Community Schemas

OpenSpec also supports community-maintained schemas distributed via standalone repositories. These provide opinionated workflows that integrate OpenSpec with other tools or systems, similar to how [github/spec-kit's community extension catalog](<https://github.com/github/spec-kit/tree/main/extensions>) works for spec-kit.

Community schemas are not vendored into OpenSpec core — they live in their own repositories with their own release cadence. To use one, copy the schema bundle into your project's `openspec/schemas/<schema-name>/` directory (each repo's README has install instructions).

| Schema | Maintainer | Repository | Description |
|--------|-----------|-----------|-------------|
| `intent-driven` | @harikrishnan83 | [intent-driven-dev/openspec-schemas](<https://github.com/intent-driven-dev/openspec-schemas/tree/main/openspec/schemas/intent-driven>) | Captures change intent, observable behaviour, technical design, and durable architectural decisions before implementation. Adds a change-local ADR review manifest and writes qualifying long-lived decisions as immutable, supersedable ADRs. |
| `superpowers-bridge` | @JiangWay | [JiangWay/openspec-schemas](<https://github.com/JiangWay/openspec-schemas/tree/main/superpowers-bridge>) | Integrates OpenSpec's artifact governance with [obra/superpowers](<https://github.com/obra/superpowers>) execution skills (brainstorming, writing-plans, TDD via subagents, code review, finishing). Adds an evidence-first `retrospective` artifact filling a gap Superpowers does not natively cover. |
| `nanopm` | @nmrtn | [nmrtn/nanopm](<https://github.com/nmrtn/nanopm/tree/main/openspec-schema>) | PM-first workflow. Runs [nanopm](<https://github.com/nmrtn/nanopm>)'s planning pipeline (audit → strategy → roadmap → PRD) upstream of implementation. Bridges product planning to OpenSpec's spec-driven engineering workflow. Artifacts read from `.nanopm/` if present — proposal sources the audit, design sources the strategy, and tasks source the PRD breakdown. |
| `e2e-runbooks` | @Lukk17 | [Lukk17/openspec-schemas](<https://github.com/Lukk17/openspec-schemas/tree/master/openspec/schemas/e2e-runbooks>) | Capability-level end-to-end test runbooks. Each capability gets an immutable spec, an immutable tasks-template, and one timestamped run record per execution. Assertions are observable behaviour only (HTTP status, response body, persisted state — never log substrings); each run records start/end UTC, duration, and best-estimate LLM token consumption. |
| `anvil` | @jikkujoyce | [jikkujoyce/openspec-schemas](<https://github.com/jikkujoyce/openspec-schemas/tree/main/schemas/anvil>) | Spec-driven workflow with TDD discipline and an adversarial review step. Flow: `proposal` → `specs` → `design` → `review` → `test-plan` → `tasks` → `apply` → `verify`. `review` is written by a fresh-context, read-only reviewer (a second model when one is available) and emits a `VERDICT:` line telling the agent to gate `test-plan`, `tasks`, and `apply`; OpenSpec only checks that artifacts exist, so enforce the gate with your own CI or hook. `test-plan` maps every spec scenario to a named test and doubles as a red/green ledger that `verify` audits. |

> Want to contribute a community schema? Open an issue with a link to your repository, or submit a PR adding a row to this table.

---

## See Also

- [CLI Reference: Schema Commands](<OpenSpec 命令行参考.md#schema-commands>) - Full command documentation


</details>
