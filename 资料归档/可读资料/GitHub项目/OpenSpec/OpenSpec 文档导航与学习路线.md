# OpenSpec 文档导航与学习路线

按入门、日常使用、核心概念、定制和跨仓库协作组织文档，帮助读者选择阅读路径并理解完整工作循环。

- 来源：OpenSpec
- 资料类型：文档目录与阅读路线
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/README.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/README.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OpenSpec 文档

欢迎。这是一切的家OpenSpec。

OpenSpec 帮助您和您的 AI 编码助理 ** 在编写任何代码之前就要构建的内容达成一致。** 您描述更改，AI 起草一份简短的规范和任务列表，你们俩都会查看相同的计划，然后工作就会发生。中途不再发现 AI 构建了错误的东西。

如果您没有阅读其他内容，请阅读这两页：

1. [入门](<OpenSpec 入门与首个变更.md>)：安装、初始化并交付您的第一个更改。
2. [命令如何工作](<OpenSpec 两类命令如何协作.md>)：您实际键入`/opsx:propose`的位置（提示：在您的AI聊天中，而不是终端）。这几乎让每个人都绊倒过一次。

第二个比看起来更重要。 OpenSpec 有两部分：您在终端中运行的命令行工具，以及您向 AI 助手提供的斜线命令。知道哪个是哪个可以帮助您避免最常见的混乱时刻。

> **首先建立的最佳习惯：当你不确定要构建什么时，从`/opsx:explore`开始。**它是一个无利害关系的思考伙伴，在编写任何代码之前阅读你的代码，权衡选项，并将模糊的想法锐化为具体的计划。 [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)指南就是这样的情况。

## 选择你的道路

**我是全新的。**从[入门](<OpenSpec 入门与首个变更.md>)开始，然后浏览[核心概念概览](<OpenSpec 核心概念速览.md>)。当感觉有些神秘时，[FAQ](<OpenSpec 常见问题.md>)和[词汇表](<OpenSpec 术语表.md>)就在附近。

**我有问题，但没有计划。**这是常见情况，它有专门的答案：[先探索](<使用 OpenSpec 探索问题与澄清需求.md>)。在做出任何决定之前，使用 `/opsx:explore` 与 AI 一起思考。

**我有一个很大的现有代码库。**您没有记录所有内容。 [在现有项目中使用OpenSpec](<在已有项目中采用 OpenSpec.md>)展示了如何在不沸腾的情况下开始真正的棕地代码。

**我只是想让它工作。** [安装](<OpenSpec 安装与初始化指南.md>)，运行`openspec init`，然后阅读[命令如何工作](<OpenSpec 两类命令如何协作.md>)，以便您的第一个斜杠命令位于正确的位置。或者通过 [AI- 辅助安装提示 ](<OpenSpec 安装与初始化指南.md#使用-ai-助手安装>) 将设置交给您的助手。

**我通过示例学习。** [示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md>)页面从头到尾介绍了真正的更改：一个小功能、一个错误修复、一个重构、一个探索。

**AI刚刚起草了一份计划——现在怎么办？**阅读它。 [审查变更](<OpenSpec 变更审阅指南.md>)展示了在价格仍然便宜的情况下抓住错误转弯的两分钟通行证，而[编写良好的规范](<编写清晰的 OpenSpec 行为规格.md>)涵盖了值得批准的计划的组成部分。

**我在一个团队中工作。** [OpenSpec 在团队中](<OpenSpec 团队协作与 Git 工作流.md>) 展示了更改如何映射到分支和拉取请求，以及团队成员如何在代码之前审查计划。

**我来自旧的工作流程。** [迁移指南](<从旧版 OpenSpec 工作流迁移到 OPSX.md>)解释了更改的内容和原因，并承诺您现有的工作是安全的。

**我想将其融入我团队的流程。** [自定义](<OpenSpec 项目配置与工作流定制.md>)涵盖项目配置、自定义模式和共享上下文。

**有些东西坏了。** [故障排除](<OpenSpec 安装与变更排障.md>)收集人们实际遇到的故障，并进行修复。

## 整个地图

### 从这里开始

|医生|它给你什么|
|-----|-------------------|
|[开始使用](<OpenSpec 入门与首个变更.md>)|安装、初始化并端到端运行您的第一个更改|
|[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)|在提交之前使用 `/opsx:explore` 仔细思考一个想法|
|[命令如何工作](<OpenSpec 两类命令如何协作.md>)|斜杠命令在哪里运行，“交互模式”意味着什么，终端与聊天|
|[核心概念一览](<OpenSpec 核心概念速览.md>)|整个心智模型在一页上：规格、变更、增量、存档|
|[安装](<OpenSpec 安装与初始化指南.md>)|npm、pnpm、yarn、bun、Nix，将设置交给您的 AI 助手的提示，以及如何验证其是否有效|

### 日常使用它

|医生|它给你什么|
|-----|-------------------|
|[工作流程](<OpenSpec 快速与扩展工作流.md>)|常见模式以及何时执行每个命令|
|[示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md>)|真实变化的完整演练，可复制粘贴|
|[编写良好的规格](<编写清晰的 OpenSpec 行为规格.md>)|强烈的需求和场景是什么样的，以及如何调整变更的大小|
|[查看更改](<OpenSpec 变更审阅指南.md>)|在编写任何代码之前花两分钟时间讨论起草的计划|
|[OpenSpec 在团队中](<OpenSpec 团队协作与 Git 工作流.md>)|变更如何适应分支、拉取请求和审查|
|[在现有项目中使用OpenSpec](<在已有项目中采用 OpenSpec.md>)|在大型棕地代码库上采用 OpenSpec|
|[编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md>)|更新工件、返回、协调手动编辑|
|[命令](<OpenSpec AI 聊天命令参考.md>)|每个 `/opsx:*` 斜线命令的参考|
| [CLI](<OpenSpec 命令行参考.md>) |每个`openspec`终端命令的参考|

### 深刻理解它

|医生|它给你什么|
|-----|-------------------|
|[概念](<OpenSpec 核心概念与规格结构.md>)|对规范、变更、工件、模式和存档的详细解释|
|[OPSX 工作流程](<OpenSpec OPSX 工作流机制.md>)|为什么工作流程是流畅的而不是锁相的，加上架构深入探讨|
|[术语表](<OpenSpec 术语表.md>)|每个术语都在一个地方定义|

### 让它成为你的

|医生|它给你什么|
|-----|-------------------|
|[定制](<OpenSpec 项目配置与工作流定制.md>)|项目配置、自定义模式、共享上下文|
|[多语言](<OpenSpec 多语言工件配置.md>)|生成英语以外语言的工件|
|[支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)|30+ AI 工具 OpenSpec 集成，以及文件所在的位置|
|[社区展示](<OpenSpec 社区项目与资源目录.md>)|使用 OpenSpec 构建的项目和资源|

### 当你需要帮助时

|医生|它给你什么|
|-----|-------------------|
| [FAQ](<OpenSpec 常见问题.md>) |快速回答人们最常问的问题|
|[故障排除](<OpenSpec 安装与变更排障.md>)|针对混凝土故障的具体修复|
|[迁移指南](<从旧版 OpenSpec 工作流迁移到 OPSX.md>)|从旧工作流程转移到 OPSX|

### 跨存储库协调（测试版）

|医生|它给你什么|
|-----|-------------------|
|[商店：用户指南](<OpenSpec Store 跨仓库规划指南（Beta）.md>)|当您的工作跨越存储库或团队时，在自己的存储库中进行规划|
|[Agent 合约](<OpenSpec 面向代理的 CLI 输出契约.md>)|机器可读的CLI表面agents驱动器|

## 第三十秒版本

```text
1. Install        npm install -g @fission-ai/openspec@latest
2. Initialize     cd your-project && openspec init
3. Explore        (in your AI chat)  /opsx:explore           ← optional, but a great habit
4. Propose        (in your AI chat)  /opsx:propose add-dark-mode
5. Build          (in your AI chat)  /opsx:apply
6. Archive        (in your AI chat)  /opsx:archive
```

步骤 1 和 2 发生在您的终端中。其余的发生在您的 AI 助理的聊天中。这种分割是值得记住的一件事，[命令如何工作](<OpenSpec 两类命令如何协作.md>)确切地解释了原因。步骤 3 是可选的，但当您不确定是否是最值得养成的习惯时，请从 `/opsx:explore` 开始。

## 还有哪里可以获得帮助

- **Discord：** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>) 提出问题、想法和帮助。
- **GitHub 问题：** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>) 有关错误和功能请求。
- **`openspec feedback "your message"`** 直接从您的终端发送反馈（它会打开 GitHub 问题）。

在这些文档中发现了错误、陈旧或令人困惑的内容吗？这是一个错误。打开问题或 PR。文档改进是您可以做出的最有价值的贡献之一。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenSpec Documentation

Welcome. This is the home for everything OpenSpec.

OpenSpec helps you and your AI coding assistant **agree on what to build before any code is written.** You describe the change, the AI drafts a short spec and a task list, you both look at the same plan, and then the work happens. No more discovering halfway through that the AI built the wrong thing.

If you read nothing else, read these two pages:

1. [Getting Started](<OpenSpec 入门与首个变更.md>): install, initialize, and ship your first change.
2. [How Commands Work](<OpenSpec 两类命令如何协作.md>): where you actually type `/opsx:propose` (hint: in your AI chat, not the terminal). This trips up almost everyone once.

That second one matters more than it looks. OpenSpec has two halves: a command line tool you run in your terminal, and slash commands you give to your AI assistant. Knowing which is which saves you the most common moment of confusion.

> **The best habit to build first: when you're not sure what to build, start with `/opsx:explore`.** It's a no-stakes thinking partner that reads your code, weighs options, and sharpens a fuzzy idea into a concrete plan before any code gets written. The [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) guide makes the case.

## Pick your path

**I'm brand new.** Start with [Getting Started](<OpenSpec 入门与首个变更.md>), then skim the [Core Concepts at a Glance](<OpenSpec 核心概念速览.md>). When something feels mysterious, the [FAQ](<OpenSpec 常见问题.md>) and [Glossary](<OpenSpec 术语表.md>) are nearby.

**I have a problem but not a plan.** This is the common case, and it has a dedicated answer: [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>). Use `/opsx:explore` to think it through with the AI before committing to anything.

**I have a big existing codebase.** You don't document all of it. [Using OpenSpec in an Existing Project](<在已有项目中采用 OpenSpec.md>) shows how to start on real, brownfield code without boiling the ocean.

**I just want to get it working.** [Install](<OpenSpec 安装与初始化指南.md>), run `openspec init`, then read [How Commands Work](<OpenSpec 两类命令如何协作.md>) so your first slash command lands in the right place. Or hand the setup to your assistant with the [AI-assisted install prompt](<OpenSpec 安装与初始化指南.md#install-with-your-ai-assistant>).

**I learn by example.** The [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>) page walks through real changes start to finish: a small feature, a bug fix, a refactor, an exploration.

**The AI just drafted a plan — now what?** Read it. [Reviewing a Change](<OpenSpec 变更审阅指南.md>) shows the two-minute pass that catches a wrong turn while it's still cheap, and [Writing Good Specs](<编写清晰的 OpenSpec 行为规格.md>) covers what a plan worth approving is made of.

**I work on a team.** [OpenSpec on a Team](<OpenSpec 团队协作与 Git 工作流.md>) shows how a change maps onto a branch and a pull request, and how teammates review a plan before the code.

**I'm coming from the old workflow.** The [Migration Guide](<从旧版 OpenSpec 工作流迁移到 OPSX.md>) explains what changed and why, and promises your existing work is safe.

**I want to bend it to my team's process.** [Customization](<OpenSpec 项目配置与工作流定制.md>) covers project config, custom schemas, and shared context.

**Something's broken.** [Troubleshooting](<OpenSpec 安装与变更排障.md>) collects the failures people actually hit, with fixes.

## The whole map

### Start here

| Doc | What it gives you |
|-----|-------------------|
| [Getting Started](<OpenSpec 入门与首个变更.md>) | Install, initialize, and run your first change end to end |
| [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) | Use `/opsx:explore` to think through an idea before you commit |
| [How Commands Work](<OpenSpec 两类命令如何协作.md>) | Where slash commands run, what "interactive mode" means, terminal vs chat |
| [Core Concepts at a Glance](<OpenSpec 核心概念速览.md>) | The whole mental model on one page: specs, changes, deltas, archive |
| [Installation](<OpenSpec 安装与初始化指南.md>) | npm, pnpm, yarn, bun, Nix, a prompt that hands setup to your AI assistant, and how to verify it worked |

### Use it day to day

| Doc | What it gives you |
|-----|-------------------|
| [Workflows](<OpenSpec 快速与扩展工作流.md>) | Common patterns and when to reach for each command |
| [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>) | Full walkthroughs of real changes, copy-pasteable |
| [Writing Good Specs](<编写清晰的 OpenSpec 行为规格.md>) | What a strong requirement and scenario look like, and how to right-size a change |
| [Reviewing a Change](<OpenSpec 变更审阅指南.md>) | The two-minute pass on a drafted plan before any code is written |
| [OpenSpec on a Team](<OpenSpec 团队协作与 Git 工作流.md>) | How changes fit branches, pull requests, and review |
| [Using OpenSpec in an Existing Project](<在已有项目中采用 OpenSpec.md>) | Adopting OpenSpec on a large brownfield codebase |
| [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md>) | Update artifacts, go back, reconcile manual edits |
| [Commands](<OpenSpec AI 聊天命令参考.md>) | Reference for every `/opsx:*` slash command |
| [CLI](<OpenSpec 命令行参考.md>) | Reference for every `openspec` terminal command |

### Understand it deeply

| Doc | What it gives you |
|-----|-------------------|
| [Concepts](<OpenSpec 核心概念与规格结构.md>) | The long-form explanation of specs, changes, artifacts, schemas, and archive |
| [OPSX Workflow](<OpenSpec OPSX 工作流机制.md>) | Why the workflow is fluid instead of phase-locked, plus an architecture deep dive |
| [Glossary](<OpenSpec 术语表.md>) | Every term defined in one place |

### Make it yours

| Doc | What it gives you |
|-----|-------------------|
| [Customization](<OpenSpec 项目配置与工作流定制.md>) | Project config, custom schemas, shared context |
| [Multi-Language](<OpenSpec 多语言工件配置.md>) | Generate artifacts in languages other than English |
| [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>) | The 30+ AI tools OpenSpec integrates with, and where files land |
| [Community Showcase](<OpenSpec 社区项目与资源目录.md>) | Projects and resources built with and for OpenSpec |

### When you need help

| Doc | What it gives you |
|-----|-------------------|
| [FAQ](<OpenSpec 常见问题.md>) | Quick answers to the questions people ask most |
| [Troubleshooting](<OpenSpec 安装与变更排障.md>) | Concrete fixes for concrete failures |
| [Migration Guide](<从旧版 OpenSpec 工作流迁移到 OPSX.md>) | Moving from the legacy workflow to OPSX |

### Coordinate across repos (beta)

| Doc | What it gives you |
|-----|-------------------|
| [Stores: User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>) | Plan in its own repo when your work spans repos or teams |
| [Agent Contract](<OpenSpec 面向代理的 CLI 输出契约.md>) | The machine-readable CLI surfaces agents drive |

## The thirty-second version

```text
1. Install        npm install -g @fission-ai/openspec@latest
2. Initialize     cd your-project && openspec init
3. Explore        (in your AI chat)  /opsx:explore           ← optional, but a great habit
4. Propose        (in your AI chat)  /opsx:propose add-dark-mode
5. Build          (in your AI chat)  /opsx:apply
6. Archive        (in your AI chat)  /opsx:archive
```

Steps 1 and 2 happen in your terminal. The rest happen in your AI assistant's chat. That split is the one thing worth memorizing, and [How Commands Work](<OpenSpec 两类命令如何协作.md>) explains exactly why. Step 3 is optional, but starting with `/opsx:explore` when you're unsure is the habit most worth forming.

## Where else to get help

- **Discord:** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>) for questions, ideas, and help.
- **GitHub Issues:** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>) for bugs and feature requests.
- **`openspec feedback "your message"`** sends feedback straight from your terminal (it opens a GitHub issue).

Found something in these docs that's wrong, stale, or confusing? That's a bug. Open an issue or a PR. Documentation improvements are some of the most valuable contributions you can make.


</details>
