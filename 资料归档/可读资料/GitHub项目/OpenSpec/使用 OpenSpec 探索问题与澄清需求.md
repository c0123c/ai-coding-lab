# 使用 OpenSpec 探索问题与澄清需求

介绍 explore 的适用情形、代码库调查与方案讨论方式，以及从探索结果转入 propose 的示例和取舍。

- 来源：OpenSpec
- 资料类型：需求探索使用指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/explore.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/explore.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 首先探索

**`/opsx:explore`是您的思想伙伴。每当您遇到问题但还没有计划时，就可以使用它。** 它会在编写一行代码之前调查您的代码库，与您一起权衡选项，并阐明您真正想要的内容。当图像清晰时，将其交给`/opsx:propose`。

如果您从这些文档中养成了一种习惯，请采用以下一种习惯：**当您不确定时，请在提出建议之前进行探索。**

这就是为什么这很重要。 AI 编码助手很渴望。含糊地询问，他们会自信地构建 * 一些东西 *，只是可能不是您需要的东西。探索是良药。这是一次无利害关系的对话，你和 AI 一起找出正确的行动，这样当你提出建议时，你就提出了正确的事情。

## 何时探索

探索是正确的第一步，这种情况比人们预期的要多。当满足以下任一条件时使用它：

- 您知道 * 问题 *，但不知道 * 解决方案 *。 （“页面感觉很慢。”“身份验证一团糟。”“我们不断收到重复的订单。”）
- 您正在多种方法之间进行选择，并希望根据实际代码进行权衡。
- 您是代码库的新手，需要在更改某些内容之前了解其工作原理。
- 需求是模糊的，你需要在提交之前明确它们。
- 您怀疑这项工作比看起来更大或更小，并且想要诚实地确定其范围。

仅当您已经确切知道自己想要什么以及如何做时，才可以跳过探索。在这种情况下，请直接转到 [`/opsx:propose`](<OpenSpec AI 聊天命令参考.md#opsxpropose>)。

## 它做什么（和不做什么）

探索是一个**对话**，而不是一个生成器。

**确实是：**
- 阅读并搜索您的代码库以回答实际问题。
- 比较选项并列出每个选项的权衡。
- 绘制图表以使设计清晰易读。
- 帮助您将模糊的想法缩小为具体的、可构建的范围。
- 当您提出要求或接受其提议时，捕获探索：它使用 `openspec new change` 构建变更并写入您命名的规划工件，或更新现有变更的工件。
- 准备好后过渡到 `/opsx:propose`。

**它没有：**
- 编写或修改代码。探索永远不会在任何路径上编写代码，包括捕获。
- 设计或编辑您的架构或模板。塑造这些是一种改变，而不是思考。
- 开始更改或自行编写工件。除非您询问，或者在它提供时说“是”，否则它不会写任何内容，然后只写您同意的内容，加上启动更改需求的设置文件（见下文）。
- 推动你去捕捉。当思想具体化时，它就会提供；你决定。

这就是重点。探索不会让你付出任何代价，也不会让你承担任何责任，除非你这么说。你可以探索三个死胡同，从每个死胡同中学习一些东西，然后才能提出幸存的道路。

## 已经安装好了

好消息：`/opsx:explore` 采用默认的 ** 核心 ** 配置文件，与 `propose`、`apply`、`update`、`sync` 和 `archive` 一起提供。您不需要启用任何东西。如果您的项目中设置了 OpenSpec，则可以在您的 AI 聊天中进行探索。 （与所有 `/opsx:*` 命令一样，您可以在助手的聊天室中键入它，而不是终端。请参阅[命令如何工作](<OpenSpec 两类命令如何协作.md>)。）

## 一个完整的例子

观察模糊的担忧如何变成尖锐的、可实现的改变。

```text
You: /opsx:explore

AI:  What would you like to explore?

You: Our checkout sometimes creates duplicate orders. I don't know why
     and I don't know how to fix it safely.

AI:  Let me dig into the checkout path...
     [reads the order service, the payment webhook handler, and the
      client submit logic]

     Here's what I found. There are two ways a duplicate can happen:

     1. The client can submit twice (double-click, retry on slow network).
        Nothing debounces it.
     2. The payment webhook can fire more than once, and the handler
        creates an order each time. There's no idempotency key.

     Cause #2 is the dangerous one: it can charge and fulfill twice.

     Three ways to fix it, roughly in order of robustness:
     - Idempotency key on the order-creation endpoint (handles both causes)
     - Dedup on the webhook by payment-intent id (handles #2 only)
     - Client-side debounce (handles #1 only, papers over the real bug)

     Given your code already stores the payment-intent id, the
     idempotency-key approach is a small change with the biggest payoff.
     Want me to scope that?

You: Yes, let's do the idempotency key.

You: /opsx:propose add-order-idempotency-key

AI:  Created openspec/changes/add-order-idempotency-key/, with a proposal
     and delta spec grounded in what we just found. Ready for implementation.
```

注意发生了什么。出发点是“有些不对劲，我不敢碰它”。二十秒的探索将其变成了命名的根本原因、三个排名选项、与现有代码相关的建议以及精确的更改。接下来的建议很尖锐，因为思考首先发生。

## 移交求婚

探索不会存档成任何东西。准备好后，您只需开始更改，AI 就会将对话中的上下文带入工件中。

```text
explore  ──►  propose  ──►  apply  ──►  archive
 (think)     (agree)       (build)     (record)
```

你可以用简单的语言说出来（“让我们把这变成一个改变”）或者直接运行`/opsx:propose <name>`。无论哪种方式，您刚刚所做的探索都会成为提案的基础，而不是一次性的聊天。

您还可以要求探索捕获更改本身，而无需离开对话：“为此开始更改”构建文件夹，“也编写提案”准确写入您命名的工件。脚手架还奠定了更改自己的元数据，并填充项目在顶层缺少的任何内容（`openspec/specs/`、`openspec/changes/archive/`、`config.yaml`）。

这与移交的目的地相同，但有一个区别：提议写入您的模式实现实现所需的整个集合，而捕获仅写入您指定的工件。

如果您使用扩展的命令集，探索可以转交给 `/opsx:new`，以逐步创建工件。请参阅[工作流程](<OpenSpec 快速与扩展工作流.md>)。

## 良好探索的提示

- **提出问题，而不是解决方案。**“登录感觉很慢”给了AI调查的空间。 “添加 Redis 缓存”会预先向您承诺尚未测试的答案。
- **大声询问权衡。**“每个选项的缺点是什么？”让您进行更诚实的比较。
- **让它先读一下。**最好的探索始于AI实际查看您的代码，而不是猜测。如果有帮助，请将其指向相关区域。
- **可以放弃。**如果探索表明这个想法不值得，那就是胜利。你学的很便宜。
- **在变化中再次探索。**在`/opsx:apply`期间卡住了？您可以退一步探索子问题，然后返回。

## 诚实的权衡

**你将获得什么：**在你做出任何承诺之前，探索会在最便宜的时刻抓住错误的转折点。它在不熟悉的代码中尤其强大，AI 阅读和总结系统的能力可以帮助您节省一个下午的探索时间。

**需要什么：**一点耐心。探索是一种对话，因此它比发出`/opsx:propose`并希望要慢。对于您已经真正理解的工作，这个额外的步骤纯粹是开销，您应该跳过它。

经验法则：任务越模糊，探索的回报就越大。任务越清晰，你就越可以直接跳到求婚。

## 下一步去哪里

- [命令： `/opsx:explore`](<OpenSpec AI 聊天命令参考.md#opsxexplore>)：精确参考
- [工作流程](<OpenSpec 快速与扩展工作流.md>)：作为日常循环的一部分进行探索
- [示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md#食谱3在提交之前探索>)：探索完整的演练
- [入门](<OpenSpec 入门与首个变更.md>)：首次更改指南，包括探索


## 原文对照

<details>
<summary>展开完整原文</summary>

# Explore First

**`/opsx:explore` is your thinking partner. Reach for it whenever you have a problem but not yet a plan.** It investigates your codebase, weighs options with you, and clarifies what you actually want, all before a line of code is written. When the picture is clear, it hands off to `/opsx:propose`.

If you take one habit from these docs, take this one: **when you're not sure, explore before you propose.**

Here's why that matters. AI coding assistants are eager. Ask vaguely and they'll confidently build *something*, just maybe not the thing you needed. Explore is the cure. It's a no-stakes conversation where you and the AI figure out the right move together, so that by the time you propose, you're proposing the right thing.

## When to explore

Explore is the right first step more often than people expect. Use it when any of these is true:

- You know the *problem* but not the *solution*. ("Pages feel slow." "Auth is a mess." "We keep getting duplicate orders.")
- You're choosing between approaches and want the tradeoffs laid out against your actual code.
- You're new to a codebase and need to understand how something works before you change it.
- The requirements are fuzzy and you want to sharpen them before committing.
- You suspect the work is bigger or smaller than it looks and want to scope it honestly.

Skip explore only when you already know exactly what you want and how. In that case go straight to [`/opsx:propose`](<OpenSpec AI 聊天命令参考.md#opsxpropose>).

## What it does (and doesn't)

Explore is a **conversation**, not a generator.

**It does:**
- Read and search your codebase to answer real questions.
- Compare options and name the tradeoffs of each.
- Draw diagrams to make a design legible.
- Help you narrow a vague idea into a concrete, buildable scope.
- Capture the exploration when you ask, or when you accept its offer: it scaffolds the change with `openspec new change` and writes the planning artifacts you named, or updates an existing change's artifacts.
- Transition to `/opsx:propose` when you're ready.

**It does not:**
- Write or modify code. Explore never writes code, on any path, capture included.
- Design or edit your schemas or templates. Shaping those is a change, not thinking.
- Start a change or write an artifact on its own. It writes nothing unless you ask, or say yes when it offers, and then only what you agreed to, plus the setup files starting a change needs (see below).
- Push you toward capturing. It offers when the thinking crystallizes; you decide.

That's the point. Exploring costs you nothing and commits you to nothing until you say so. You can explore three dead ends, learn something from each, and only then propose the path that survived.

## It's already installed

Good news: `/opsx:explore` ships in the default **core** profile, right alongside `propose`, `apply`, `update`, `sync`, and `archive`. You don't need to enable anything. If OpenSpec is set up in your project, explore is ready in your AI chat. (As with all `/opsx:*` commands, you type it in your assistant's chat, not the terminal. See [How Commands Work](<OpenSpec 两类命令如何协作.md>).)

## A full example

Watch how a vague worry becomes a sharp, buildable change.

```text
You: /opsx:explore

AI:  What would you like to explore?

You: Our checkout sometimes creates duplicate orders. I don't know why
     and I don't know how to fix it safely.

AI:  Let me dig into the checkout path...
     [reads the order service, the payment webhook handler, and the
      client submit logic]

     Here's what I found. There are two ways a duplicate can happen:

     1. The client can submit twice (double-click, retry on slow network).
        Nothing debounces it.
     2. The payment webhook can fire more than once, and the handler
        creates an order each time. There's no idempotency key.

     Cause #2 is the dangerous one: it can charge and fulfill twice.

     Three ways to fix it, roughly in order of robustness:
     - Idempotency key on the order-creation endpoint (handles both causes)
     - Dedup on the webhook by payment-intent id (handles #2 only)
     - Client-side debounce (handles #1 only, papers over the real bug)

     Given your code already stores the payment-intent id, the
     idempotency-key approach is a small change with the biggest payoff.
     Want me to scope that?

You: Yes, let's do the idempotency key.

You: /opsx:propose add-order-idempotency-key

AI:  Created openspec/changes/add-order-idempotency-key/, with a proposal
     and delta spec grounded in what we just found. Ready for implementation.
```

Notice what happened. The starting point was "something is wrong and I'm scared to touch it." Twenty seconds of exploration turned that into a named root cause, three ranked options, a recommendation tied to the existing code, and a precise change. The proposal that follows is sharp because the thinking happened first.

## Handing off to propose

Explore doesn't archive into anything. When you're ready, you simply start a change, and the AI carries the context from your conversation into the artifacts.

```text
explore  ──►  propose  ──►  apply  ──►  archive
 (think)     (agree)       (build)     (record)
```

You can say it in plain language ("let's turn this into a change") or run `/opsx:propose <name>` directly. Either way, the exploration you just did becomes the foundation of the proposal, not throwaway chat.

You can also ask explore to capture the change itself, without leaving the conversation: "start a change for this" scaffolds the folder, and "write the proposal too" writes exactly the artifacts you named. Scaffolding also lays down the change's own metadata, and fills in anything your project is missing at the top level (`openspec/specs/`, `openspec/changes/archive/`, a `config.yaml`).

That's the same destination as handing off, with one difference: propose writes the whole set your schema requires to reach implementation, while capture writes only the artifacts you named.

If you use the expanded command set, explore can hand off to `/opsx:new` instead, for step-by-step artifact creation. See [Workflows](<OpenSpec 快速与扩展工作流.md>).

## Tips for a good exploration

- **Bring the problem, not the solution.** "Logins feel slow" gives the AI room to investigate. "Add a Redis cache" pre-commits you to an answer you haven't tested yet.
- **Ask for the tradeoffs out loud.** "What are the downsides of each option?" gets you a more honest comparison.
- **Let it read first.** The best explorations start with the AI actually looking at your code, not guessing. Point it at the relevant area if it helps.
- **It's okay to bail.** If exploration reveals the idea isn't worth it, that's a win. You learned it cheaply.
- **Explore again mid-change.** Stuck during `/opsx:apply`? You can step back and explore a sub-problem, then return.

## The honest tradeoffs

**What you gain:** explore catches wrong turns at the cheapest possible moment, before you've committed to anything. It's especially powerful in unfamiliar code, where the AI's ability to read and summarize the system saves you an afternoon of spelunking.

**What it costs:** a little patience. Explore is a conversation, so it's slower than firing off `/opsx:propose` and hoping. For work you genuinely understand already, that extra step is pure overhead, and you should skip it.

The rule of thumb: the fuzzier the task, the more explore pays off. The clearer the task, the more you can skip straight to proposing.

## Where to go next

- [Commands: `/opsx:explore`](<OpenSpec AI 聊天命令参考.md#opsxexplore>): the precise reference
- [Workflows](<OpenSpec 快速与扩展工作流.md>): explore as part of the everyday loop
- [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md#recipe-3-exploring-before-you-commit>): explore in a full walkthrough
- [Getting Started](<OpenSpec 入门与首个变更.md>): the first-change guide, exploration included


</details>
