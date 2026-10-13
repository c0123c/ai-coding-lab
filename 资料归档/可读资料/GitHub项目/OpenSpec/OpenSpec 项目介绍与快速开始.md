# OpenSpec 项目介绍与快速开始

介绍 OpenSpec 的规格与变更管理目标、安装与初始化方式、与 AI 编程助手配合的流程，以及文档、社区和贡献入口。

- 来源：OpenSpec
- 资料类型：仓库 README：项目介绍与入门文档
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/README.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/README.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec">
    <picture>
      <source srcset="assets/openspec_bg.png">
      

![OpenSpec 标志](<https://raw.githubusercontent.com/Fission-AI/OpenSpec/9111a7654d7800391459431fff4eaf66e33a3d2e/assets/openspec_bg.png>)

    </picture>
  </a>
</p>

<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml">

![CI](<https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml/badge.svg>)

</a>
  <a href="https://www.npmjs.com/package/@fission-ai/openspec">

![npm 版本](<https://img.shields.io/npm/v/@fission-ai/openspec?style=flat-square>)

</a>
  <a href="./LICENSE">

![许可证：MIT](<https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square>)

</a>
  <a href="https://discord.gg/YctCnvvshC">

![不和谐](<https://img.shields.io/discord/1411657095639601154?style=flat-square&logo=discord&logoColor=white&label=Discord&suffix=%20online>)

</a>
</p>



<summary><strong>最受欢迎的规范框架。</strong></summary>

[！[星星](<https://img.shields.io/github/stars/Fission-AI/OpenSpec?style=flat-square&label=Stars>)](<https://github.com/Fission-AI/OpenSpec/stargazers>)
[！[下载](<https://img.shields.io/npm/dm/@fission-ai/openspec?style=flat-square&label=Downloads/mo>)](<https://www.npmjs.com/package/@fission-ai/openspec>)
[！[贡献者](<https://img.shields.io/github/contributors/Fission-AI/OpenSpec?style=flat-square&label=Contributors>)](<https://github.com/Fission-AI/OpenSpec/graphs/contributors>)



<p></p>
我们的理念：

```text
→ fluid not rigid
→ iterative not waterfall
→ easy not complex
→ built for brownfield not just greenfield
→ scalable from personal projects to enterprises
```

> [！TIP]
> **新工作流程现已可用！**我们使用新的工件引导工作流程重建了OpenSpec。
>
> 运行`/opsx:propose "your idea"`即可开始。 → [在此了解更多信息](<OpenSpec OPSX 工作流机制.md>)

<p align="center">
 在 X</a> 上关注 <a href="https://x.com/0xTab">@0xTab 以获取更新 · 加入 <a href="https://discord.gg/YctCnvvshC">OpenSpec Discord</a> 寻求帮助和问题。
</p>

<!-- TODO: Add GIF demo of /opsx:propose → /opsx:archive workflow -->

## 查看实际效果

```text
You: /opsx:explore
AI:  What would you like to explore?
You: I want dark mode but I'm not sure how to do it cleanly.
AI:  Let me look at your styling setup...
     Cleanest path here: CSS variables + a small theme context,
     with system-preference detection. No new dependencies. Scope it?
You: Yes, let's do it.

You: /opsx:propose add-dark-mode
AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md — why we're doing this, what's changing
     ✓ specs/       — requirements and scenarios
     ✓ design.md    — technical approach
     ✓ tasks.md     — implementation checklist
     Ready for implementation!

You: /opsx:apply
AI:  Implementing tasks...
     ✓ 1.1 Add theme context provider
     ✓ 1.2 Create toggle component
     ✓ 2.1 Add CSS variables
     ✓ 2.2 Wire up localStorage
     All tasks complete!

You: /opsx:archive
AI:  Archived to openspec/changes/archive/2025-01-23-add-dark-mode/
     Specs updated. Ready for the next feature.
```



<summary><strong>规格实际上是什么样的？</strong></summary>

普通 Markdown — 需要具体场景，无需学习特殊语法。以下是上面创建的 `specs/` 文件夹中的内容：

```markdown
## ADDED Requirements

### Requirement: Theme selection
The app SHALL let users switch between light and dark themes,
defaulting to the system preference.

#### Scenario: User toggles dark mode
- **WHEN** the user clicks the theme toggle
- **THEN** the app switches to dark mode and persists the choice
```

你的AI写了这些；在编写任何代码之前先检查计划。

OpenSpec 是用 OpenSpec 构建的 - 浏览此存储库的实时 [ 规格 ](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/openspec/specs>) 和正在进行的 [ 更改 ](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/openspec/changes>) 以获取大规模的真实示例。






<summary><strong>OpenSpec 仪表板</strong></summary>

<p align="center">
  

![OpenSpec 仪表板预览](<https://raw.githubusercontent.com/Fission-AI/OpenSpec/9111a7654d7800391459431fff4eaf66e33a3d2e/assets/openspec_dashboard.png>)

</p>




## 为什么团队采用 OpenSpec

独奏，OpenSpec 让你和你的 AI 在一个存储库上保持诚实。在团队中，最困难的部分是移动的：功能跨越 API 服务器、Web 应用程序和共享库；需求由一个团队拥有并由其他团队消耗；在任何代码存在之前就开始规划。

**[商店](<OpenSpec Store 跨仓库规划指南（Beta）.md>)**就是答案——在自己的仓库中进行规划。您已经知道的相同 `openspec/` 形状（规格和更改），由 `git push` 共享，就像其他任何东西一样。您的整个团队和每个代码 agent 都可以在每个存储库中读取一个事实来源。

- **跨存储库功能** — 一项更改，一项计划，即使代码位于三个存储库中也是如此。
- **共享需求** - 平台团队拥有规范；产品团队以只读方式引用它们，就在他们的编码 agent 可以读取它们的地方。没有漂移维基。
- **编码前的计划** — 现在捕获商店中的计划；代码仓库稍后会赶上。

> 商店处于**测试版**。从 [商店用户指南 ](<OpenSpec Store 跨仓库规划指南（Beta）.md>) 开始。

## 快速入门

**需要Node.js 20.19.0或更高版本。** Homebrew将其安装为依赖项。

全局安装OpenSpec：

```bash
npm install -g @fission-ai/openspec@latest
```

或者在macOS或Linux上安装官方[自制公式](<https://formulae.brew.sh/formula/openspec>)：

```bash
brew install openspec
```

然后导航到您的项目目录并初始化：

```bash
cd your-project
openspec init
```

> **想要您的AI执行此操作吗？**将[设置提示](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs-lab/start/installation.md#install-with-your-ai-assistant>)粘贴到您的编码助手中 - 它会安装CLI，运行`openspec init`，并验证结果。

现在与您的 AI 交谈：

- ** 还不知道要构建什么？** 从 `/opsx:explore` 开始，它是一个无利害关系的思考伙伴，可以在编写任何代码之前读取您的代码、权衡选项并制定计划。 （[探索指南](<使用 OpenSpec 探索问题与澄清需求.md>)）
- **已经知道你想要什么了吗？** Go直接到`/opsx:propose <what-you-want-to-build>`。

两者都在默认配置文件中。如果您需要扩展工作流程（`/opsx:new`、`/opsx:continue`、`/opsx:ff`、`/opsx:verify`、`/opsx:bulk-archive`、`/opsx:onboard`），请使用 `openspec config profile` 选择并使用 `openspec update` 应用。

`/opsx:propose` 是规范名称；您的工具可能会将其拼写为 `/opsx-propose`（Cursor、GitHub Copilot）、`@opsx-propose`（Amazon Q）或 `$openspec-propose`（Codex）。 `openspec init` 为您选择的工具打印正确的表单 - 请参阅[如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)。

> [！NOTE]
> 不确定您的工具是否受支持？ [查看完整列表](<OpenSpec 支持的 AI 工具与调用方式.md>) – 我们支持 30+ 工具和成长。
>
> 也适用于 Homebrew、pnpm、yarn、bun 和 Nix。 [请参阅安装选项](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs-lab/start/installation.md>)。

## 文档

**从这里开始：****[文档主页](<OpenSpec 文档导航与学习路线.md>)**映射了所有内容。 OpenSpec 新手？阅读 [ 入门 ](<OpenSpec 入门与首个变更.md>)，然后阅读 [ 命令如何工作 ](<OpenSpec 两类命令如何协作.md>)（您实际键入 `/opsx:propose` 的位置）。

→ **[开始使用](<OpenSpec 入门与首个变更.md>)**：第一步<br>
→ **[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)**：在提交之前仔细考虑`/opsx:explore`<br>
→ **[命令如何工作](<OpenSpec 两类命令如何协作.md>)**：斜杠命令在哪里运行与CLI<br>
→**[核心概念概览](<OpenSpec 核心概念速览.md>)**：整个心理模型，一页<br>
→**[示例&配方](<OpenSpec 功能、修复与重构工作流实例.md>)**：真正的变化，从头到尾<br>
→ **[工作流程](<OpenSpec 快速与扩展工作流.md>)**：组合和图案<br>
→ **[现有项目](<在已有项目中采用 OpenSpec.md>)**：采用OpenSpec 位于棕地代码库上<br>
→ **[编辑更改](<OpenSpec 变更工件的编辑与迭代.md>)**：更新工件、返回、协调手动编辑<br>
→ **[命令](<OpenSpec AI 聊天命令参考.md>)**：斜线命令 &技能<br>
→ **[CLI](<OpenSpec 命令行参考.md>)**：终端参考<br>
→ **[商店](<OpenSpec Store 跨仓库规划指南（Beta）.md>)**：在单独的存储库中计划，在团队中共享（测试版）<br>
→ **[支持工具](<OpenSpec 支持的 AI 工具与调用方式.md>)**：工具集成和安装路径<br>
→ **[概念](<OpenSpec 核心概念与规格结构.md>)**：如何适应<br>
→ **[多语言](<OpenSpec 多语言工件配置.md>)**：多语言支持<br>
→ **[定制](<OpenSpec 项目配置与工作流定制.md>)**：让它成为你的<br>
→ **[社区展示](<OpenSpec 社区项目与资源目录.md>)**：使用OpenSpec<br>
构建的项目和资源→ **[FAQ](<OpenSpec 常见问题.md>)** · **[故障排除](<OpenSpec 安装与变更排障.md>)** · **[术语表](<OpenSpec 术语表.md>)**：快速帮助


## 社区模式

通过独立存储库分发的第三方模式捆绑包 — 这些提供了将 OpenSpec 与其他工具集成的固执己见的工作流程，类似于 [github/spec-kit 的社区扩展目录 ](<https://github.com/github/spec-kit/tree/main/extensions>) 处理工具集成的方式。

→ **[浏览定制文档中的目录](<OpenSpec 项目配置与工作流定制.md#社区模式>)**。


## 为什么是OpenSpec？

AI 编码助手功能强大，但当需求仅存在于聊天历史记录中时，它是不可预测的。 OpenSpec 添加了一个轻量级规范层，以便您在编写任何代码之前就要构建的内容达成一致。

- **在构建之前达成一致**——在编写代码之前，人类和AI在规范上保持一致
- **保持井井有条** - 每个更改都有自己的文件夹，其中包含提案、规格、设计和任务
- **工作流畅** - 随时更新任何工件，没有严格的阶段门
- **使用你的工具** — 通过斜线命令与 30+ AI 助手一起使用

### 我们如何比较

** 对比[规格套件](<https://github.com/github/spec-kit>)** (GitHub) — 彻底但重量级。刚性相位门，大量 Markdown，Python 设置。 OpenSpec更轻量，让你自由迭代。

** 对比[Kiro](<https://kiro.dev>)** (AWS) — 功能强大，但你被锁定在他们的 IDE 中并仅限于 Claude 型号。 OpenSpec 可与您已使用的工具配合使用。

** 对比Nothing** — AI 没有规范的编码意味着模糊的提示和不可预测的结果。 OpenSpec 无需仪式即可带来可预测性。

## 正在更新OpenSpec

**升级包**

```bash
npm install -g @fission-ai/openspec@latest
```

如果您使用 Homebrew 安装了 OpenSpec：

```bash
brew upgrade openspec
```

**刷新agent指令**

在每个项目中运行此命令以重新生成 AI 指南并确保最新的斜线命令处于活动状态：

```bash
openspec update
```

## 使用说明

**模型选择**：OpenSpec最适合高推理模型。我们推荐 Codex 5.5 和 Opus 4.7 进行规划和实施。

**上下文卫生**：OpenSpec受益于干净的上下文窗口。在开始实施之前清除您的上下文，并在整个会话过程中保持良好的上下文卫生。

## 贡献

在打开 PR 之前打开讨论（针对核心设计更改）或问题，并链接 PR 中的问题或讨论。新功能、重大重构和架构变更首先需要 OpenSpec 变更提案。

→ **[CONTRIBUTING.md](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/CONTRIBUTING.md>)**：完整的流程，从第一期到合并 PR

## 其他



<summary><strong>遥测</strong></summary>

OpenSpec 收集匿名使用统计数据。

我们仅收集命令名称和版本来了解使用模式。没有参数、路径、内容或 PII。在 CI 中自动禁用。

**选择退出（任意一项即可）：**
- `openspec config set telemetry.enabled false`（全局配置；未设置表示打开）
- `export OPENSPEC_TELEMETRY=0` 或 `export DO_NOT_TRACK=1`（env 覆盖配置）






<summary><strong>维护人员和顾问</strong></summary>

请参阅 [MAINTAINERS.md](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/MAINTAINERS.md>) 以获取帮助指导项目的核心维护者和顾问的列表。






## 许可证

MIT


## 原文对照

<details>
<summary>展开完整原文</summary>

<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec">
    <picture>
      <source srcset="assets/openspec_bg.png">
      

![OpenSpec logo](<https://raw.githubusercontent.com/Fission-AI/OpenSpec/9111a7654d7800391459431fff4eaf66e33a3d2e/assets/openspec_bg.png>)

    </picture>
  </a>
</p>

<p align="center">
  <a href="https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml">

![CI](<https://github.com/Fission-AI/OpenSpec/actions/workflows/ci.yml/badge.svg>)

</a>
  <a href="https://www.npmjs.com/package/@fission-ai/openspec">

![npm version](<https://img.shields.io/npm/v/@fission-ai/openspec?style=flat-square>)

</a>
  <a href="./LICENSE">

![License: MIT](<https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square>)

</a>
  <a href="https://discord.gg/YctCnvvshC">

![Discord](<https://img.shields.io/discord/1411657095639601154?style=flat-square&logo=discord&logoColor=white&label=Discord&suffix=%20online>)

</a>
</p>



<summary><strong>The most loved spec framework.</strong></summary>

[![Stars](<https://img.shields.io/github/stars/Fission-AI/OpenSpec?style=flat-square&label=Stars>)](<https://github.com/Fission-AI/OpenSpec/stargazers>)
[![Downloads](<https://img.shields.io/npm/dm/@fission-ai/openspec?style=flat-square&label=Downloads/mo>)](<https://www.npmjs.com/package/@fission-ai/openspec>)
[![Contributors](<https://img.shields.io/github/contributors/Fission-AI/OpenSpec?style=flat-square&label=Contributors>)](<https://github.com/Fission-AI/OpenSpec/graphs/contributors>)



<p></p>
Our philosophy:

```text
→ fluid not rigid
→ iterative not waterfall
→ easy not complex
→ built for brownfield not just greenfield
→ scalable from personal projects to enterprises
```

> [!TIP]
> **New workflow now available!** We've rebuilt OpenSpec with a new artifact-guided workflow.
>
> Run `/opsx:propose "your idea"` to get started. → [Learn more here](<OpenSpec OPSX 工作流机制.md>)

<p align="center">
  Follow <a href="https://x.com/0xTab">@0xTab on X</a> for updates · Join the <a href="https://discord.gg/YctCnvvshC">OpenSpec Discord</a> for help and questions.
</p>

<!-- TODO: Add GIF demo of /opsx:propose → /opsx:archive workflow -->

## See it in action

```text
You: /opsx:explore
AI:  What would you like to explore?
You: I want dark mode but I'm not sure how to do it cleanly.
AI:  Let me look at your styling setup...
     Cleanest path here: CSS variables + a small theme context,
     with system-preference detection. No new dependencies. Scope it?
You: Yes, let's do it.

You: /opsx:propose add-dark-mode
AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md — why we're doing this, what's changing
     ✓ specs/       — requirements and scenarios
     ✓ design.md    — technical approach
     ✓ tasks.md     — implementation checklist
     Ready for implementation!

You: /opsx:apply
AI:  Implementing tasks...
     ✓ 1.1 Add theme context provider
     ✓ 1.2 Create toggle component
     ✓ 2.1 Add CSS variables
     ✓ 2.2 Wire up localStorage
     All tasks complete!

You: /opsx:archive
AI:  Archived to openspec/changes/archive/2025-01-23-add-dark-mode/
     Specs updated. Ready for the next feature.
```



<summary><strong>What do the specs actually look like?</strong></summary>

Plain Markdown — requirements with concrete scenarios, no special syntax to learn. Here's what goes in the `specs/` folder created above:

```markdown
## ADDED Requirements

### Requirement: Theme selection
The app SHALL let users switch between light and dark themes,
defaulting to the system preference.

#### Scenario: User toggles dark mode
- **WHEN** the user clicks the theme toggle
- **THEN** the app switches to dark mode and persists the choice
```

Your AI writes these; you review the plan before any code is written.

OpenSpec is built with OpenSpec — browse this repo's live [specs](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/openspec/specs>) and in-flight [changes](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/openspec/changes>) for real examples at scale.






<summary><strong>OpenSpec Dashboard</strong></summary>

<p align="center">
  

![OpenSpec dashboard preview](<https://raw.githubusercontent.com/Fission-AI/OpenSpec/9111a7654d7800391459431fff4eaf66e33a3d2e/assets/openspec_dashboard.png>)

</p>




## Why teams adopt OpenSpec

Solo, OpenSpec keeps you and your AI honest on a single repo. On a team, the hard part moves: a feature spans the API server, the web app, and a shared library; requirements are owned by one team and consumed by others; planning starts before any code exists.

**[Stores](<OpenSpec Store 跨仓库规划指南（Beta）.md>)** are the answer — planning in a repo of its own. The same `openspec/` shape you already know (specs and changes), shared by `git push` like anything else. One source of truth your whole team and every coding agent can read, across every repo.

- **Cross-repo features** — one change, one plan, even when the code lands in three repos.
- **Shared requirements** — a platform team owns the specs; product teams reference them read-only, right where their coding agent can read them. No drifting wiki.
- **Plan before code** — capture the plan in the store now; the code repos catch up later.

> Stores are in **beta**. Start with the [Stores User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>).

## Quick Start

**Requires Node.js 20.19.0 or higher.** Homebrew installs it as a dependency.

Install OpenSpec globally:

```bash
npm install -g @fission-ai/openspec@latest
```

Or install the official [Homebrew formula](<https://formulae.brew.sh/formula/openspec>) on macOS or Linux:

```bash
brew install openspec
```

Then navigate to your project directory and initialize:

```bash
cd your-project
openspec init
```

> **Want your AI to do it?** Paste the [setup prompt](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs-lab/start/installation.md#install-with-your-ai-assistant>) into your coding assistant — it installs the CLI, runs `openspec init`, and verifies the result.

Now talk to your AI:

- **Not sure what to build yet?** Start with `/opsx:explore`, a no-stakes thinking partner that reads your code, weighs options, and shapes a plan before any code gets written. ([Explore guide](<使用 OpenSpec 探索问题与澄清需求.md>))
- **Already know what you want?** Go straight to `/opsx:propose <what-you-want-to-build>`.

Both are in the default profile. If you want the expanded workflow (`/opsx:new`, `/opsx:continue`, `/opsx:ff`, `/opsx:verify`, `/opsx:bulk-archive`, `/opsx:onboard`), select it with `openspec config profile` and apply with `openspec update`.

`/opsx:propose` is the canonical name; your tool may spell it `/opsx-propose` (Cursor, GitHub Copilot), `@opsx-propose` (Amazon Q) or `$openspec-propose` (Codex). `openspec init` prints the right form for the tools you picked — see [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>).

> [!NOTE]
> Not sure if your tool is supported? [View the full list](<OpenSpec 支持的 AI 工具与调用方式.md>) – we support 30+ tools and growing.
>
> Also works with Homebrew, pnpm, yarn, bun, and Nix. [See installation options](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs-lab/start/installation.md>).

## Docs

**Start here:** the **[Documentation Home](<OpenSpec 文档导航与学习路线.md>)** maps everything. New to OpenSpec? Read [Getting Started](<OpenSpec 入门与首个变更.md>), then [How Commands Work](<OpenSpec 两类命令如何协作.md>) (where you actually type `/opsx:propose`).

→ **[Getting Started](<OpenSpec 入门与首个变更.md>)**: first steps<br>
→ **[Explore First](<使用 OpenSpec 探索问题与澄清需求.md>)**: think it through with `/opsx:explore` before you commit<br>
→ **[How Commands Work](<OpenSpec 两类命令如何协作.md>)**: where slash commands run vs the CLI<br>
→ **[Core Concepts at a Glance](<OpenSpec 核心概念速览.md>)**: the whole mental model, one page<br>
→ **[Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>)**: real changes, start to finish<br>
→ **[Workflows](<OpenSpec 快速与扩展工作流.md>)**: combos and patterns<br>
→ **[Existing Projects](<在已有项目中采用 OpenSpec.md>)**: adopt OpenSpec on a brownfield codebase<br>
→ **[Editing a Change](<OpenSpec 变更工件的编辑与迭代.md>)**: update artifacts, go back, reconcile manual edits<br>
→ **[Commands](<OpenSpec AI 聊天命令参考.md>)**: slash commands & skills<br>
→ **[CLI](<OpenSpec 命令行参考.md>)**: terminal reference<br>
→ **[Stores](<OpenSpec Store 跨仓库规划指南（Beta）.md>)**: plan in a separate repo, shared across your team (beta)<br>
→ **[Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>)**: tool integrations & install paths<br>
→ **[Concepts](<OpenSpec 核心概念与规格结构.md>)**: how it all fits<br>
→ **[Multi-Language](<OpenSpec 多语言工件配置.md>)**: multi-language support<br>
→ **[Customization](<OpenSpec 项目配置与工作流定制.md>)**: make it yours<br>
→ **[Community Showcase](<OpenSpec 社区项目与资源目录.md>)**: projects and resources built with and for OpenSpec<br>
→ **[FAQ](<OpenSpec 常见问题.md>)** · **[Troubleshooting](<OpenSpec 安装与变更排障.md>)** · **[Glossary](<OpenSpec 术语表.md>)**: quick help


## Community schemas

Third-party schema bundles distributed via standalone repositories — these provide opinionated workflows that integrate OpenSpec with other tools, similar to how [github/spec-kit's community extension catalog](<https://github.com/github/spec-kit/tree/main/extensions>) handles tool integrations.

→ **[Browse the catalog](<OpenSpec 项目配置与工作流定制.md#community-schemas>)** in the customization docs.


## Why OpenSpec?

AI coding assistants are powerful but unpredictable when requirements live only in chat history. OpenSpec adds a lightweight spec layer so you agree on what to build before any code is written.

- **Agree before you build** — human and AI align on specs before code gets written
- **Stay organized** — each change gets its own folder with proposal, specs, design, and tasks
- **Work fluidly** — update any artifact anytime, no rigid phase gates
- **Use your tools** — works with 30+ AI assistants via slash commands

### How we compare

**vs. [Spec Kit](<https://github.com/github/spec-kit>)** (GitHub) — Thorough but heavyweight. Rigid phase gates, lots of Markdown, Python setup. OpenSpec is lighter and lets you iterate freely.

**vs. [Kiro](<https://kiro.dev>)** (AWS) — Powerful but you're locked into their IDE and limited to Claude models. OpenSpec works with the tools you already use.

**vs. nothing** — AI coding without specs means vague prompts and unpredictable results. OpenSpec brings predictability without the ceremony.

## Updating OpenSpec

**Upgrade the package**

```bash
npm install -g @fission-ai/openspec@latest
```

If you installed OpenSpec with Homebrew:

```bash
brew upgrade openspec
```

**Refresh agent instructions**

Run this inside each project to regenerate AI guidance and ensure the latest slash commands are active:

```bash
openspec update
```

## Usage Notes

**Model selection**: OpenSpec works best with high-reasoning models. We recommend Codex 5.5 and Opus 4.7 for both planning and implementation.

**Context hygiene**: OpenSpec benefits from a clean context window. Clear your context before starting implementation and maintain good context hygiene throughout your session.

## Contributing

Open a discussion (for core design changes) or an issue before you open a PR, and link the issue or discussion from the PR. New features, significant refactors, and architectural changes need an OpenSpec change proposal first.

→ **[CONTRIBUTING.md](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/CONTRIBUTING.md>)**: the full process, from first issue to merged PR

## Other



<summary><strong>Telemetry</strong></summary>

OpenSpec collects anonymous usage stats.

We collect only command names and version to understand usage patterns. No arguments, paths, content, or PII. Automatically disabled in CI.

**Opt-out (any one is enough):**
- `openspec config set telemetry.enabled false` (global config; unset means on)
- `export OPENSPEC_TELEMETRY=0` or `export DO_NOT_TRACK=1` (env overrides config)






<summary><strong>Maintainers & Advisors</strong></summary>

See [MAINTAINERS.md](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/MAINTAINERS.md>) for the list of core maintainers and advisors who help guide the project.






## License

MIT


</details>
