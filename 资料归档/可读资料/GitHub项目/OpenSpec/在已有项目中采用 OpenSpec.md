# 在已有项目中采用 OpenSpec

解释从即将修改的功能切片开始编写增量规格，如何处理已有需求文档、大型代码库和跨仓库变更。

- 来源：OpenSpec
- 资料类型：已有项目接入指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/existing-projects.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/existing-projects.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在现有项目中使用OpenSpec

**您无需记录整个代码库即可开始。您只为要更改的内容编写规范。** 这是在现有项目上采用 OpenSpec 时需要了解的最重要的事情，这也是为什么 OpenSpec 是棕地优先构建的原因。

一个常见的担忧听起来像这样：“我的应用程序 80,000 已经很旧了。在 OpenSpec 有用之前我是否必须为所有它编写规范？”不，你会讨厌这样，我们也会。 OpenSpec 一次一变地增加您的规格。您的第一个更改记录了它所涉及的部分，下一个更改记录了它的部分，几个月后您的规格自然地围绕您实际所做的工作填充。

本指南展示了如何在不让海洋沸腾的情况下开始第一天的工作。

## 第三十秒版本

```bash
$ cd your-existing-project
$ openspec init          # adds openspec/ and your AI tool's commands
```

然后，在您的 AI 聊天中：

```text
/opsx:explore            # optional: have the AI read the area you'll touch
/opsx:propose <a real, small change you actually need>
/opsx:apply
/opsx:archive
```

您的规格现在准确地描述了系统中发生变化的部分，仅此而已。这是正确的。您不再担心其他 80,000 行。

## 为什么增量优先是整个技巧

OpenSpec 更改写为 ** 增量 **：`ADDED`、`MODIFIED`、`REMOVED`。增量描述了相对于当前行为的变化，而不是整个系统。

这正是棕地工作所需要的。你很少从无到有。您要添加一个字段、修复重定向、收紧超时。增量可以让您精确地指定一个更改，而无需先编写其周围所有内容的 40 页规范。

所以你的 `openspec/specs/` 目录开始时并不完整。它开始时几乎是空的，然后逐渐积累。每个存档的更改都会合并其增量。只有在您进行了多次身份验证更改后，`auth/` 的规范才会变得彻底，而这正是您希望它彻底的时候。

如果您想要更深入的机制，请参阅[概念：Delta Specs](<OpenSpec 核心概念与规格结构.md#达美规格>)。

## 您对真实代码库的第一次更改

选择一些小而真实的东西。不是玩具，也不是重写。无论如何，这周你都会做出改变。最初的小改变会教你低风险的工作流程。

**步骤1：让AI读取相关区域。**这是`/opsx:explore`在不熟悉或大型代码库中获得生存的地方。将其指向您将要触摸的部分，让它在提出任何建议之前描绘出事情的运作方式。

```text
You: /opsx:explore

AI:  What would you like to explore?

You: I need to add rate limiting to our public API, but I'm not sure
     how requests currently flow through the middleware.

AI:  Let me trace it... [reads the router, middleware stack, and config]
     Requests hit Express, pass through auth middleware, then your
     controllers. There's no rate-limiting layer today. The cleanest
     insertion point is a middleware right after auth. Want me to scope it?
```

请注意，AI 现在可以理解您的实际结构，因此它编写的提案将适合您的代码，而不是通用模板。在大型代码库中，这个习惯可以减轻最大的痛苦。请参阅[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)。

**步骤2：提出变更。**提案及其增量规范仅捕获此变更。

```text
You: /opsx:propose add-api-rate-limiting
```

**步骤3：使用`/opsx:apply`和`/opsx:archive`构建并归档**，与任何更改相同。归档后，您就拥有了速率限制行为的真实规范，该规范源自您无论如何都需要的更改。

## 更喜欢导游服务？机上使用

如果您希望通过旁白观看整个循环在您自己的代码上发生，扩展命令 `/opsx:onboard` 正是这样做的：它扫描您的代码库以查找小的、安全的改进，然后引导您完成建议、构建和归档，并解释每个步骤。

首先打开扩展命令：

```bash
$ openspec config profile      # select the expanded workflows
$ openspec update              # apply them to this project
```

然后在聊天中：

```text
/opsx:onboard
```

这是对真实项目最温和的介绍，它给你留下了一个真正的（小）改变，你可以保留或放弃。请参阅[命令：`/opsx:onboard`](<OpenSpec AI 聊天命令参考.md#opsxonboard>)。

## “但我已经有了需求文档”

也许您有一个 PRD、一个 SRS、一个正式规格，甚至 TLA+ 型号。好的。你不会批发进口它们，也不会扔掉它们。

将现有文档视为**探索的源材料**，而不是作为要转换的规范。当您开始更改时，将 AI 粘贴或指向相关部分，并让它从中形成一个集中的 OpenSpec 增量。增量以 OpenSpec 的可测试需求和场景形式捕获您现在正在更改的行为。您的原始文件保留在原处作为背景。

诚实的原因：OpenSpec 规范故意以行为为先，并限制更改范围。 40 页 PRD 是具有不同作业的不同工件。强制进行一次性批量转换往往会产生一个没人信任的大型、过时的规范。让规格随着实际变化而增长，以保持其准确性。

```text
You: /opsx:explore
You: Here's the section of our PRD about checkout. I'm implementing the
     "guest checkout" requirement next.
     [paste the relevant requirement]
AI:  [reads it, asks clarifying questions, then helps scope a change]
You: /opsx:propose add-guest-checkout
```

## 在大型代码库中组织规范

规格位于 `openspec/specs/` 下，按 ** 域 ** 分组：与您的团队对系统的看法相匹配的逻辑区域。您不必预先设计整个分类法。当您在该区域进行第一次更改时需要创建一个域文件夹。

常见的域名切片方式：

- **按功能区域：** `auth/`、`payments/`、`search/`
- **按组件：** `api/`、`frontend/`、`workers/`
- **按有界上下文：** `ordering/`、`fulfillment/`、`inventory/`

选择让新人点头的任何东西。您可以稍后完善。请参阅[概念：规格](<OpenSpec 核心概念与规格结构.md#规格>)。

## Monorepos 和跨回购的工作

对于单一存储库，最简单的模型是存储库根目录中的一个 `openspec/` 目录，其中的域映射到您的包或服务。这涵盖了大多数团队。

如果您的工作确实跨越**多个存储库**（或您视为独立的多个包），OpenSpec有一个测试版**存储**功能：在自己的独立存储库中规划生活，您的任何代码存储库都可以引用，因此该计划不必生活在一个存储库中存储库的 `openspec/` 文件夹。它是测试版，因此将其命令和状态视为不断发展的。从 [商店用户指南 ](<OpenSpec Store 跨仓库规划指南（Beta）.md>) 开始了解心智模型和最小有用路径。

## 一些诚实的警告

- **抵制回填所有内容的冲动。**为不更改的代码编写规范感觉很有成效，但通常情况并非如此。这些规范已经过时了，因为没有什么能迫使它们追踪现实。让真正的变化推动您的规格。
- **保持早期的变化较小。**你的前几次变化与学习节奏和运输一样重要。范围狭窄使得循环速度更快并且课程成本更低。
- **将`openspec/`提交到git。**您的规范和存档与它们描述的代码一起属于版本控制。
- **给出AI上下文。**在具有强约定的大型代码库上，填写`openspec/config.yaml`的`context:`，以便每个提案都尊重您的堆栈和模式。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#项目配置>)。

## 下一步去哪里

- [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>) - 在更改代码之前理解代码的关键习惯
- [入门](<OpenSpec 入门与首个变更.md>) - 完整的首次更改演练
- [编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md>) - 在学习时调整更改
- [概念：Delta Specs](<OpenSpec 核心概念与规格结构.md#达美规格>) - 为什么三角洲使棕地变得干净
- [定制](<OpenSpec 项目配置与工作流定制.md>) - 教授OpenSpec您的项目约定


## 原文对照

<details>
<summary>展开完整原文</summary>

# Using OpenSpec in an Existing Project

**You do not document your whole codebase to start. You write specs only for what you're about to change.** That's the single most important thing to know about adopting OpenSpec on an existing project, and it's why OpenSpec is built brownfield-first.

A common worry sounds like this: "My app is 80,000 lines old. Do I have to write specs for all of it before OpenSpec is useful?" No. You'd hate that, and so would we. OpenSpec grows your specs one change at a time. Your first change documents the slice it touches, the next change documents its slice, and over months your specs fill in naturally around the work you actually do.

This guide shows how to start on day one without boiling the ocean.

## The thirty-second version

```bash
$ cd your-existing-project
$ openspec init          # adds openspec/ and your AI tool's commands
```

Then, in your AI chat:

```text
/opsx:explore            # optional: have the AI read the area you'll touch
/opsx:propose <a real, small change you actually need>
/opsx:apply
/opsx:archive
```

Your specs now describe exactly the part of the system that change touched, and nothing more. That's correct. You're done worrying about the other 80,000 lines.

## Why delta-first is the whole trick

OpenSpec changes are written as **deltas**: `ADDED`, `MODIFIED`, `REMOVED`. A delta describes what's changing relative to current behavior, not the entire system.

This is exactly what brownfield work needs. You're rarely building from nothing. You're adding a field, fixing a redirect, tightening a timeout. A delta lets you specify that one change precisely without first writing a 40-page spec of everything around it.

So your `openspec/specs/` directory doesn't start full and complete. It starts nearly empty and accumulates. Each archived change merges its delta in. The spec for `auth/` becomes thorough only after you've made several auth changes, which is exactly when you want it thorough.

If you want the deeper mechanics, see [Concepts: Delta Specs](<OpenSpec 核心概念与规格结构.md#delta-specs>).

## Your first change on a real codebase

Pick something small and real. Not a toy, not a rewrite. A change you were going to make this week anyway. Small first changes teach you the workflow with low stakes.

**Step 1: Let the AI read the relevant area.** This is where `/opsx:explore` earns its keep on an unfamiliar or large codebase. Point it at the part you're about to touch and let it map how things work before proposing anything.

```text
You: /opsx:explore

AI:  What would you like to explore?

You: I need to add rate limiting to our public API, but I'm not sure
     how requests currently flow through the middleware.

AI:  Let me trace it... [reads the router, middleware stack, and config]
     Requests hit Express, pass through auth middleware, then your
     controllers. There's no rate-limiting layer today. The cleanest
     insertion point is a middleware right after auth. Want me to scope it?
```

Notice the AI now understands your actual structure, so the proposal it writes will fit your code, not a generic template. On a big codebase, this single habit saves the most pain. See [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>).

**Step 2: Propose the change.** The proposal and its delta spec capture just this change.

```text
You: /opsx:propose add-api-rate-limiting
```

**Step 3: Build and archive** with `/opsx:apply` and `/opsx:archive`, same as any change. After archiving, you have a real spec for your rate-limiting behavior, born from a change you needed anyway.

## Prefer a guided tour? Use onboard

If you'd rather watch the whole loop happen on your own code with narration, the expanded command `/opsx:onboard` does exactly that: it scans your codebase for a small, safe improvement, then walks you through proposing, building, and archiving it, explaining each step.

Turn on the expanded commands first:

```bash
$ openspec config profile      # select the expanded workflows
$ openspec update              # apply them to this project
```

Then in chat:

```text
/opsx:onboard
```

It's the gentlest possible introduction on a real project, and it leaves you with a genuine (small) change you can keep or discard. See [Commands: `/opsx:onboard`](<OpenSpec AI 聊天命令参考.md#opsxonboard>).

## "But I already have requirements docs"

Maybe you have a PRD, an SRS, a formal spec, even TLA+ models. Good. You don't import them wholesale, and you don't throw them away either.

Treat existing docs as **source material for exploration**, not as specs to convert. When you start a change, paste or point the AI at the relevant section, and let it shape a focused OpenSpec delta from it. The delta captures the behavior you're changing now, in OpenSpec's testable requirement-and-scenario form. Your original documents stay where they are as background.

The honest reason: OpenSpec specs are deliberately behavior-first and scoped to changes. A 40-page PRD is a different artifact with a different job. Forcing a one-time bulk conversion tends to produce a large, stale spec nobody trusts. Letting specs grow from real changes keeps them accurate.

```text
You: /opsx:explore
You: Here's the section of our PRD about checkout. I'm implementing the
     "guest checkout" requirement next.
     [paste the relevant requirement]
AI:  [reads it, asks clarifying questions, then helps scope a change]
You: /opsx:propose add-guest-checkout
```

## Organizing specs in a big codebase

Specs live under `openspec/specs/`, grouped by **domain**: a logical area that matches how your team thinks about the system. You don't have to design the whole taxonomy up front. Create a domain folder when your first change in that area needs one.

Common ways to slice domains:

- **By feature area:** `auth/`, `payments/`, `search/`
- **By component:** `api/`, `frontend/`, `workers/`
- **By bounded context:** `ordering/`, `fulfillment/`, `inventory/`

Pick whatever makes a newcomer nod. You can refine later. See [Concepts: Specs](<OpenSpec 核心概念与规格结构.md#specs>).

## Monorepos and work that spans repos

For a monorepo, the simplest model is one `openspec/` directory at the repo root, with domains that map to your packages or services. That covers most teams.

If your work genuinely spans **multiple repositories** (or several packages you treat as separate), OpenSpec has a beta **stores** feature: planning lives in its own standalone repo that any of your code repos can reference, so the plan does not have to live inside one repo's `openspec/` folder. It's beta, so treat its commands and state as evolving. Start with the [Stores User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>) for the mental model and the smallest useful path.

## A few honest cautions

- **Resist the urge to back-fill everything.** Writing specs for code you aren't changing feels productive and usually isn't. Those specs go stale, because nothing forces them to track reality. Let real changes drive your specs.
- **Keep early changes small.** Your first few changes are as much about learning the rhythm as shipping. A tight scope makes the loop fast and the lessons cheap.
- **Commit `openspec/` to git.** Your specs and archive belong in version control alongside the code they describe.
- **Give the AI context.** On a large codebase with strong conventions, fill in `openspec/config.yaml`'s `context:` so every proposal respects your stack and patterns. See [Customization](<OpenSpec 项目配置与工作流定制.md#project-configuration>).

## Where to go next

- [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) - the key habit for understanding code before you change it
- [Getting Started](<OpenSpec 入门与首个变更.md>) - the full first-change walkthrough
- [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md>) - adjusting a change as you learn
- [Concepts: Delta Specs](<OpenSpec 核心概念与规格结构.md#delta-specs>) - why deltas make brownfield work clean
- [Customization](<OpenSpec 项目配置与工作流定制.md>) - teach OpenSpec your project's conventions


</details>
