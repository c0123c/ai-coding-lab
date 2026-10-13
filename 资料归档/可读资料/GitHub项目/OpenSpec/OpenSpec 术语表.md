# OpenSpec 术语表

按主题解释规格、工件、生命周期、工作流、定制与跨仓库协调等术语，并附相关文档链接。

- 来源：OpenSpec
- 资料类型：术语目录
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/glossary.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/glossary.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 词汇表

每个 OpenSpec 术语都在一个地方，用简单的语言定义。浏览一次，其余文档读起来会更快。

术语按主题分组，然后在每组中按字母顺序排列。

## 核心名词

**Spec.** 描述系统部分行为方式的文档。规范位于 `openspec/specs/` 中，按领域组织，由需求和场景组成。该规范是“这个软件的用途是什么？”的商定答案。请参阅[概念](<OpenSpec 核心概念与规格结构.md#规格>)。

**事实来源。**整个`openspec/specs/`目录。它保存系统当前商定的行为。更改建议对其进行编辑；归档应用它们。

**更改。** 一个工作单元，打包为 `openspec/changes/<name>/` 下的文件夹。变更包含了该工作的所有内容：它的提案、设计、任务以及它引入的规范编辑。一项更改、一项功能或修复。

**Artifact.** 里面的一个文件发生了变化。标准工件是提案、增量规格、设计和任务。它们是按照依赖顺序创建的，并且相互补充。

**Delta 规范。** 更改中的规范，仅使用 `ADDED`、`MODIFIED` 和 `REMOVED` 部分描述正在更改的内容，而不是重述整个规范。这就是让 OpenSpec 干净地编辑现有系统的原因。请参阅[概念](<OpenSpec 核心概念与规格结构.md#达美规格>)。

**域。** 规范的逻辑分组，例如 `auth/`、`payments/` 或 `ui/`。您可以选择与您对系统的看法相匹配的领域。

## 规范内

**要求。**系统必须具有的单一行为，通常用RFC2119关键字编写：“系统SHALL在30分钟后使会话过期。”要求说明*什么*，而不是*如何*。

**场景。**实际需求的具体、可测试示例，通常采用Given/When/Then形式。场景使需求变得可验证：您可以根据场景编写自动化测试。

**RFC 2119 关键字。** 单词 MUST、SHALL、SHOULD 和 MAY 具有关于要求有多严格的标准化含义。 MUST 和 SHALL 是绝对的。建议使用 SHOULD，但也有例外情况。 MAY 是可选的。该名称来自定义它们的互联网标准文档。

## 文物

**提案（`proposal.md`）。**变更的*原因*和*内容*：其意图、范围和高级方法。您创建的第一个工件。

**设计（`design.md`）。** *如何*：技术方法、架构决策以及您期望接触的文件。对于简单的更改是可选的。

**任务 (`tasks.md`)。** 实施清单，带有复选框。 AI 在 `/opsx:apply` 期间工作并检查项目。

## 生命周期

**存档。** 完成更改的行为。其增量规格合并到主要规格中，并且更改文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`。存档后，您的规格描述了新的现实。请参阅[概念](<OpenSpec 核心概念与规格结构.md#存档>)。

**同步。** 将变更的增量规格合并到主要规格中 *，而不 * 存档变更。通常是自动的（存档提供这样做），但可以单独使用 `/opsx:sync` 以进行长期运行的更改。请参阅[命令](<OpenSpec AI 聊天命令参考.md#opsxsync>)。

## 工作流程和命令

**OPSX.** 当前标准 OpenSpec 工作流程，围绕流体动作而不是刚性阶段构建。其斜杠命令均以`/opsx:`开头。请参阅[OPSX工作流程](<OpenSpec OPSX 工作流机制.md>)。

**斜线命令。** 您在 AI 助理聊天中输入的命令，例如 `/opsx:propose`。斜线命令驱动工作流程。它们不是终端命令。请参阅[命令如何工作](<OpenSpec 两类命令如何协作.md>)。

**探索 (`/opsx:explore`)。** 思考伙伴命令。它读取您的代码库，比较选项，并将模糊的想法澄清为具体的计划。它从不编写代码，也不会编写任何其他内容，除非您要求它将探索捕获为更改，或者在它提供时说“是”。当您遇到问题但尚未制定计划时，建议您从这里开始。请参阅[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)。

**CLI.** 您在终端中运行的 `openspec` 程序。它设置项目、列出并验证更改、打开仪表板和存档。 OpenSpec 的终端一半。请参阅[CLI](<OpenSpec 命令行参考.md>)。

**技能。** AI 助手自动检测并遵循的说明文件夹 (`.../skills/openspec-*/SKILL.md`)。技能是新兴的跨工具标准，用于向您的助手交付 OpenSpec 工作流程。

**命令文件。** 每个工具斜杠命令文件（`.../commands/opsx-*`）。旧的交付机制仍然与技能一起支持。你很少直接接触这些。

**配置文件。** 项目中安装的斜杠命令集。 **核心**（默认）为`propose`、`explore`、`apply`、`update`、`sync`、`archive`。 **扩展的**集添加了`new`、`continue`、`ff`、`verify`、`bulk-archive`、`onboard`。用`openspec config profile`更改它。

**交付。** OpenSpec 是否为您的工具安装技能、命令文件或两者。全局配置并应用`openspec update`。

## 定制化

**架构。**工作流具有哪些工件以及它们如何相互依赖的定义。内置默认值为`spec-driven`（提案→规格→设计→任务）。您可以分叉它或编写自己的。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#自定义模式>)。

**Template.** 架构内的 Markdown 文件，用于塑造 AI 为给定工件生成的内容。编辑模板会立即更改 AI 的输出，无需重建。

**项目配置 (`openspec/config.yaml`)。** 每个项目设置：默认架构、注入每个规划请求的 `context:` 以及每个工件 `rules:`。教授 OpenSpec 有关堆栈和约定的最简单方法。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#项目配置>)。

**上下文注入。**将项目背景放入`config.yaml`的`context:`字段中，以便它自动添加到AI生成的每个工件中。比希望 AI 读取单独的文件更可靠。

**依赖图。**由工件`requires:`关系形成的有向图。它是一个 DAG（有向无环图：箭头仅指向前方，从不在循环中），OpenSpec 使用它来知道接下来可以创建什么。

**启用器，而不是门。**工件依赖关系的原则显示接下来什么是*可能的*，而不是接下来*需要的*。您可以随时重新访问和编辑任何工件。请参阅[核心概念概览](<OpenSpec 核心概念速览.md#推动者而非大门>)。

## 跨存储库的协调（测试版）

仅当您的计划跨越多个存储库时，这些条款才适用。他们正处于测试阶段。大多数用户可以忽略它们。请参阅[商店用户指南](<OpenSpec Store 跨仓库规划指南（Beta）.md>)。

**Store。**一个独立的存储库，其整个工作就是规划。它具有与您已知的相同的 `openspec/` 形状（规格和更改）以及一个小型身份文件。您可以按名称在计算机上注册一次，然后任何 OpenSpec 命令都可以从任何地方在其中运行。

**参考。**代码存储库的`openspec/config.yaml`中的存储库引用的声明。引用是只读的：存储库保留其自己的根，并且 `openspec instructions` 获得引用存储规范的索引，每个索引都有获取它的确切命令。

**工作上下文。**`openspec context`为当前存储库组装的内容：其OpenSpec根加上它引用的每个存储，每个存储都有如何获取它。 “我在做什么？”的答案

**工作集。**您一起打开的个人计算机本地文件夹集（与您处理的代码存储库并存的存储区）。使用 `openspec workset create` 显式创建；这些本地路径的任何内容都不会提交给共享规划存储库。

## 另请参阅

- [核心概念概览](<OpenSpec 核心概念速览.md>)：五个想法，一页
- [概念](<OpenSpec 核心概念与规格结构.md>)：详细解释
- [命令如何工作](<OpenSpec 两类命令如何协作.md>)：斜杠命令与CLI


## 原文对照

<details>
<summary>展开完整原文</summary>

# Glossary

Every OpenSpec term in one place, defined in plain language. Skim it once and the rest of the docs read faster.

Terms are grouped by topic, then alphabetized within each group.

## The core nouns

**Spec.** A document describing how part of your system behaves. Specs live in `openspec/specs/`, are organized by domain, and are made of requirements and scenarios. The spec is the agreed-upon answer to "what does this software do?" See [Concepts](<OpenSpec 核心概念与规格结构.md#specs>).

**Source of truth.** The `openspec/specs/` directory as a whole. It holds the current, agreed-upon behavior of your system. Changes propose edits to it; archiving applies them.

**Change.** One unit of work, packaged as a folder under `openspec/changes/<name>/`. A change holds everything about that work: its proposal, design, tasks, and the spec edits it introduces. One change, one feature or fix.

**Artifact.** A document inside a change. The standard artifacts are the proposal, the delta specs, the design, and the tasks. They're created in dependency order and feed into each other.

**Delta spec.** A spec inside a change that describes only what's changing, using `ADDED`, `MODIFIED`, and `REMOVED` sections, rather than restating the entire spec. This is what lets OpenSpec edit existing systems cleanly. See [Concepts](<OpenSpec 核心概念与规格结构.md#delta-specs>).

**Domain.** A logical grouping for specs, like `auth/`, `payments/`, or `ui/`. You choose domains that match how you think about your system.

## Inside a spec

**Requirement.** A single behavior the system must have, usually written with an RFC 2119 keyword: "The system SHALL expire sessions after 30 minutes." Requirements state the *what*, not the *how*.

**Scenario.** A concrete, testable example of a requirement in action, typically in Given/When/Then form. Scenarios make a requirement verifiable: you could write an automated test from one.

**RFC 2119 keywords.** The words MUST, SHALL, SHOULD, and MAY, which carry standardized meaning about how strict a requirement is. MUST and SHALL are absolute. SHOULD is recommended with room for exceptions. MAY is optional. The name comes from the internet standards document that defined them.

## The artifacts

**Proposal (`proposal.md`).** The *why* and *what* of a change: its intent, scope, and high-level approach. The first artifact you create.

**Design (`design.md`).** The *how*: technical approach, architecture decisions, and the files you expect to touch. Optional for simple changes.

**Tasks (`tasks.md`).** The implementation checklist, with checkboxes. The AI works through it during `/opsx:apply` and checks items off as it goes.

## The lifecycle

**Archive.** The act of finishing a change. Its delta specs merge into the main specs, and the change folder moves to `openspec/changes/archive/YYYY-MM-DD-<name>/`. After archiving, your specs describe the new reality. See [Concepts](<OpenSpec 核心概念与规格结构.md#archive>).

**Sync.** Merging a change's delta specs into the main specs *without* archiving the change. Usually automatic (archive offers to do it), but available on its own as `/opsx:sync` for long-running changes. See [Commands](<OpenSpec AI 聊天命令参考.md#opsxsync>).

## Workflow and commands

**OPSX.** The current standard OpenSpec workflow, built around fluid actions instead of rigid phases. Its slash commands all start with `/opsx:`. See [OPSX Workflow](<OpenSpec OPSX 工作流机制.md>).

**Slash command.** A command you type into your AI assistant's chat, like `/opsx:propose`. Slash commands drive the workflow. They are not terminal commands. See [How Commands Work](<OpenSpec 两类命令如何协作.md>).

**Explore (`/opsx:explore`).** The thinking-partner command. It reads your codebase, compares options, and clarifies a fuzzy idea into a concrete plan. It never writes code, and writes nothing else unless you ask it to capture the exploration as a change, or say yes when it offers. The recommended starting point whenever you have a problem but not yet a plan. See [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>).

**CLI.** The `openspec` program you run in your terminal. It sets up projects, lists and validates changes, opens the dashboard, and archives. The terminal half of OpenSpec. See [CLI](<OpenSpec 命令行参考.md>).

**Skill.** A folder of instructions (`.../skills/openspec-*/SKILL.md`) that your AI assistant auto-detects and follows. Skills are the emerging cross-tool standard for delivering the OpenSpec workflow to your assistant.

**Command file.** A per-tool slash command file (`.../commands/opsx-*`). The older delivery mechanism, still supported alongside skills. You rarely touch these directly.

**Profile.** The set of slash commands installed in your project. **Core** (the default) is `propose`, `explore`, `apply`, `update`, `sync`, `archive`. The **expanded** set adds `new`, `continue`, `ff`, `verify`, `bulk-archive`, `onboard`. Change it with `openspec config profile`.

**Delivery.** Whether OpenSpec installs skills, command files, or both for your tools. Configured globally and applied with `openspec update`.

## Customization

**Schema.** The definition of which artifacts a workflow has and how they depend on one another. The built-in default is `spec-driven` (proposal → specs → design → tasks). You can fork it or write your own. See [Customization](<OpenSpec 项目配置与工作流定制.md#custom-schemas>).

**Template.** A Markdown file inside a schema that shapes what the AI generates for a given artifact. Editing a template changes the AI's output immediately, with no rebuild.

**Project config (`openspec/config.yaml`).** Per-project settings: the default schema, the `context:` injected into every planning request, and per-artifact `rules:`. The easiest way to teach OpenSpec about your stack and conventions. See [Customization](<OpenSpec 项目配置与工作流定制.md#project-configuration>).

**Context injection.** Putting project background in `config.yaml`'s `context:` field so it's automatically added to every artifact the AI generates. More reliable than hoping the AI reads a separate file.

**Dependency graph.** The directed graph formed by artifact `requires:` relationships. It's a DAG (directed acyclic graph: arrows only point forward, never in a loop), and OpenSpec uses it to know what you can create next.

**Enablers, not gates.** The principle that artifact dependencies show what becomes *possible* next, not what's *required* next. You can revisit and edit any artifact at any time. See [Core Concepts at a Glance](<OpenSpec 核心概念速览.md#enablers-not-gates>).

## Coordination across repos (beta)

These terms apply only if your planning spans more than one repo. They're in beta. Most users can ignore them. See the [Stores User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>).

**Store.** A standalone repo whose whole job is planning. It has the same `openspec/` shape you already know (specs and changes) plus a small identity file. You register it on your machine once, by name, and then any OpenSpec command can work in it from anywhere.

**Reference.** A declaration, in a code repo's `openspec/config.yaml`, of a store that repo draws on. References are read-only: the repo keeps its own root, and `openspec instructions` gains an index of the referenced store's specs, each with the exact command to fetch it.

**Working context.** What `openspec context` assembles for the current repo: its OpenSpec root plus every store it references, each with how to fetch it. The answer to "what am I working with?"

**Workset.** A personal, machine-local set of folders you open together (a store alongside the code repos you work on). Created explicitly with `openspec workset create`; nothing about those local paths is committed to the shared planning repo.

## See also

- [Core Concepts at a Glance](<OpenSpec 核心概念速览.md>): the five ideas, on one page
- [Concepts](<OpenSpec 核心概念与规格结构.md>): the long-form explanation
- [How Commands Work](<OpenSpec 两类命令如何协作.md>): slash commands versus the CLI


</details>
