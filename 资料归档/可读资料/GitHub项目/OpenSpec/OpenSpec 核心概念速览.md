# OpenSpec 核心概念速览

用规格、变更、工件与日常命令循环概括 OpenSpec 的协作方式，并解释各动作的启用条件和轻量规划成本。

- 来源：OpenSpec
- 资料类型：概念速览与工作循环说明
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/overview.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/overview.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 核心概念一览

**OpenSpec 是您和您的 AI 之间的轻量级协议层。** 您写下更改应该做什么，AI 起草详细信息，你们都查看相同的计划，然后才编写代码。此页面是一个屏幕上的整个心智模型。当你想要长版本时，[Concepts](<OpenSpec 核心概念与规格结构.md>)有它。

这是五个词的整个想法：**首先同意，然后自信地构建。**

## 五个想法

OpenSpec 中的所有内容都是由五个概念构建的。学会这些，剩下的就是细节了。

**1。规格才是真相。** 规格描述了您的系统现在的行为方式 **。它位于 `openspec/specs/` 中，按域（`auth/`、`payments/`、`ui/`）组织。规范由要求（“系统 SHALL 在 30 分钟后过期会话”）和场景（具体 given/when/then 示例）组成。将规范视为“该软件的用途是什么？”的唯一商定答案。

**2。更改是一个工作单元。** 当您想要添加、修改或删除行为时，您可以创建一个更改：`openspec/changes/` 中的一个文件夹，将有关该工作的所有内容保存在一个位置。提案、设计、任务列表和规范编辑。一项更改、一个文件夹、一项功能。

**3。 Delta 规范描述了正在发生的变化，而不是整个世界。** 在更改中，您不必重写整个规范。你写了一个小增量：`ADDED`这个要求，`MODIFIED`那个，`REMOVED`另一个。这是使 OpenSpec 擅长编辑现有系统而不仅仅是新建系统的技巧。您描述的是差异，而不是目的地。

**4。工件相互构建。** 更改包含一些文档，这些文档按自然顺序创建，每个文档都提供下一个文档：

```text
proposal ──► specs ──► design ──► tasks ──► implement
   why        what       how       steps      do it
```

您可以随时重新访问其中任何一个。它们是推动者，而不是大门。 （更多内容见下文。）

**5。归档将更改折叠回事实。** 工作完成后，您可以归档更改。其增量规格合并到您的主要规格中，并且更改文件夹移动到带有日期戳的 `changes/archive/`。现在您的规格描述了新的现实，您已准备好迎接下一次变化。循环结束。

## 图片

```text
┌─────────────────────────────────────────────────────────────────┐
│                          openspec/                              │
│                                                                 │
│   ┌──────────────────┐         ┌──────────────────────────┐    │
│   │     specs/       │         │        changes/          │    │
│   │                  │ ◄─────  │                          │    │
│   │ source of truth  │  merge  │ one folder per change    │    │
│   │ how things work  │  on     │ proposal · design ·      │    │
│   │ today            │ archive │ tasks · delta specs      │    │
│   └──────────────────┘         └──────────────────────────┘    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

两个文件夹。 `specs/` 是真的。 `changes/` 就是您所提议的。归档将提案变成现实。

## 您将实际运行的循环

在默认设置中，您的一天是这样的。可以选择先考虑清楚；然后，一个命令起草计划，您阅读它，下一个命令构建它，最后一个命令将其归档。

```text
/opsx:explore                   →  (optional) think it through with the AI first
/opsx:propose add-dark-mode     →  AI drafts proposal, specs, design, tasks
        (you read and adjust the plan)
/opsx:apply                     →  AI builds it, checking off tasks
/opsx:archive                   →  specs updated, change archived
```

**如有疑问，请从探索开始。** `/opsx:explore` 是一个无风险的思考伙伴：它会读取您的代码，列出选项，并在编写任何代码之前将模糊的想法转变为具体的计划。这是对 AI 的最好解药，否则它会根据模糊的提示构建 * 东西 *。已经清楚地知道自己想要什么了吗？直接跳到`/opsx:propose`。无论哪种方式，在默认配置文件中探索船舶，因此它始终存在。请参阅[探索指南](<使用 OpenSpec 探索问题与澄清需求.md>)。

这些是斜杠命令，在您的 AI 助理的聊天中输入。设置（`openspec init`）发生在您的终端中。如果您不熟悉该拆分，请先阅读 [命令如何工作](<OpenSpec 两类命令如何协作.md>)；这是最常见的混淆点。

## “推动者，而非大门”

这句话在OpenSpec中随处可见，所以这就是它的简单含义。

老派的规范流程是瀑布式的：完成规划，*然后*你可以实施，然后回去是痛苦的。 OpenSpec 拒绝这一点。顺序 `proposal → specs → design → tasks` 显示接下来会 * 可能 * 做什么，而不是你接下来 * 被迫 * 做什么。

实施过程中发现设计错误？编辑 `design.md` 并继续。意识到范围应该缩小吗？更新提案。没有什么锁。依赖关系的存在只是为了让 AI 拥有它需要的上下文（如果没有规范作为基础，你就无法编写好的任务），而不是让你陷入困境。

这里的优势是诚实：真正的工作是混乱和迭代的，OpenSpec 就这样吧。权衡是纪律：因为没有什么能迫使你前进，所以你有责任保持变革的重点，而不是让它蔓延。 [工作流程](<OpenSpec 快速与扩展工作流.md>)指南在这方面有良好的习惯。

## 为什么这是值得的小额开销

简单的事实：OpenSpec 增加了一个步骤。在建造之前你要写一个简短的计划。那么你能得到什么？

- **在你付出代价之前抓住错误的转折点。**纠正单段提案中的误解是免费的。在 AI 写入 400 行之后修复它是不行的。
- **计划和代码保留在同一个存储库中。**六个月后，规范会告诉您（以及下一个AI会话）系统为何以这种方式工作。
- **更改是可审查的。**更改文件夹是一个整洁的包：阅读提案，浏览增量，检查任务。没有通过聊天记录进行考古。
- **它适合现有的代码库。** Deltas 意味着您可以指定对 50,000 行应用程序的更改，而无需先记录整个内容。

诚实的权衡是：对于一个真正微不足道的单行修复，仪式可能不会得到回报，但这没关系。 OpenSpec 被设计为轻量级，但它不是免费的。在协议很重要的地方使用它，事实证明，一旦您使用AI，它就会自信地构建您模糊要求的任何内容，这在大多数情况下都是如此。

## 下一步去哪里

- 新来的？ [入门](<OpenSpec 入门与首个变更.md>)完整地进行第一个更改。
- 还不知道要构建什么？ [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)是起点。
- 对命令在哪里运行感到困惑？ [命令如何工作](<OpenSpec 两类命令如何协作.md>)。
- 想要以上所有内容的深度版本吗？ [概念](<OpenSpec 核心概念与规格结构.md>)。
- 通过实例学习？ [示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md>)。
- 需要定义术语吗？ [术语表](<OpenSpec 术语表.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Core Concepts at a Glance

**OpenSpec is a lightweight agreement layer between you and your AI.** You write down what a change should do, the AI drafts the details, you both look at the same plan, and only then does code get written. This page is the whole mental model on one screen. When you want the long version, [Concepts](<OpenSpec 核心概念与规格结构.md>) has it.

Here's the entire idea in five words: **agree first, then build confidently.**

## The five ideas

Everything in OpenSpec is built from five concepts. Learn these and the rest is detail.

**1. Specs are the truth.** A spec describes how your system behaves *right now*. It lives in `openspec/specs/`, organized by domain (`auth/`, `payments/`, `ui/`). Specs are made of requirements ("the system SHALL expire sessions after 30 minutes") and scenarios (concrete given/when/then examples). Think of specs as the single agreed-upon answer to "what does this software do?"

**2. A change is one unit of work.** When you want to add, modify, or remove behavior, you create a change: a folder in `openspec/changes/` holding everything about that work in one place. A proposal, a design, a task list, and the spec edits. One change, one folder, one feature.

**3. Delta specs describe what's changing, not the whole world.** Inside a change, you don't rewrite the entire spec. You write a small delta: `ADDED` this requirement, `MODIFIED` that one, `REMOVED` this other one. This is the trick that makes OpenSpec good at editing existing systems, not just green-field ones. You describe the diff, not the destination.

**4. Artifacts build on each other.** A change contains a few documents, created in a natural order, each feeding the next:

```text
proposal ──► specs ──► design ──► tasks ──► implement
   why        what       how       steps      do it
```

You can revisit any of them at any time. They're enablers, not gates. (More on that below.)

**5. Archiving folds the change back into the truth.** When the work is done, you archive the change. Its delta specs merge into your main specs, and the change folder moves to `changes/archive/` with a date stamp. Now your specs describe the new reality, and you're ready for the next change. The cycle closes.

## The picture

```text
┌─────────────────────────────────────────────────────────────────┐
│                          openspec/                              │
│                                                                 │
│   ┌──────────────────┐         ┌──────────────────────────┐    │
│   │     specs/       │         │        changes/          │    │
│   │                  │ ◄─────  │                          │    │
│   │ source of truth  │  merge  │ one folder per change    │    │
│   │ how things work  │  on     │ proposal · design ·      │    │
│   │ today            │ archive │ tasks · delta specs      │    │
│   └──────────────────┘         └──────────────────────────┘    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

Two folders. `specs/` is what's true. `changes/` is what you're proposing. Archiving moves a proposal into truth.

## The loop you'll actually run

In the default setup, your day looks like this. Optionally think it through first; then one command drafts the plan, you read it, the next builds it, and the last files it away.

```text
/opsx:explore                   →  (optional) think it through with the AI first
/opsx:propose add-dark-mode     →  AI drafts proposal, specs, design, tasks
        (you read and adjust the plan)
/opsx:apply                     →  AI builds it, checking off tasks
/opsx:archive                   →  specs updated, change archived
```

**When in doubt, start by exploring.** `/opsx:explore` is a no-stakes thinking partner: it reads your code, lays out options, and turns a fuzzy idea into a concrete plan before any code gets written. It's the best antidote to an AI that will otherwise build *something* from a vague prompt. Already know exactly what you want? Skip straight to `/opsx:propose`. Either way, explore ships in the default profile, so it's always there. See the [Explore guide](<使用 OpenSpec 探索问题与澄清需求.md>).

Those are slash commands, typed in your AI assistant's chat. Setup (`openspec init`) happens in your terminal. If that split is new to you, read [How Commands Work](<OpenSpec 两类命令如何协作.md>) first; it's the most common point of confusion.

## "Enablers, not gates"

This phrase shows up everywhere in OpenSpec, so here's what it means in plain terms.

Old-school spec processes are waterfalls: finish planning, *then* you're allowed to implement, and going back is painful. OpenSpec refuses that. The order `proposal → specs → design → tasks` shows what becomes *possible* next, not what you're *forced* to do next.

Discover during implementation that the design was wrong? Edit `design.md` and keep going. Realize the scope should shrink? Update the proposal. Nothing locks. The dependencies exist only so the AI has the context it needs (you can't write good tasks without specs to base them on), not to box you in.

The strength here is honesty: real work is messy and iterative, and OpenSpec lets it be. The tradeoff is discipline: because nothing forces you forward, it's on you to keep a change focused rather than letting it sprawl. The [Workflows](<OpenSpec 快速与扩展工作流.md>) guide has good habits for that.

## Why this is worth the small overhead

Plain truth: OpenSpec adds a step. You write a short plan before building. So what do you get for it?

- **You catch wrong turns before they cost you.** Fixing a misunderstanding in a one-paragraph proposal is free. Fixing it after the AI wrote 400 lines is not.
- **The plan and the code stay in the same repo.** Six months later, the spec tells you (and the next AI session) why the system works the way it does.
- **Changes are reviewable.** A change folder is a tidy package: read the proposal, skim the deltas, check the tasks. No archaeology through chat history.
- **It fits existing codebases.** Deltas mean you can specify a change to a 50,000-line app without first documenting the whole thing.

And the honest tradeoff: for a truly trivial one-line fix, the ceremony may not pay off, and that's fine. OpenSpec is designed to be lightweight, but it isn't free. Use it where agreement matters, which turns out to be most of the time once you're working with an AI that will confidently build whatever you vaguely asked for.

## Where to go next

- New here? [Getting Started](<OpenSpec 入门与首个变更.md>) walks the first change in full.
- Not sure what to build yet? [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) is the place to start.
- Confused about where commands run? [How Commands Work](<OpenSpec 两类命令如何协作.md>).
- Want the deep version of everything above? [Concepts](<OpenSpec 核心概念与规格结构.md>).
- Learn by example? [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>).
- Need a term defined? [Glossary](<OpenSpec 术语表.md>).


</details>
