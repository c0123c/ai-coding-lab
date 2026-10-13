# OpenSpec 变更工件的编辑与迭代

说明如何修改 proposal、specs、design 和 tasks，如何在实施后复查计划，以及手工代码修改后如何协调已有工件。

- 来源：OpenSpec
- 资料类型：变更编辑与迭代指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/editing-changes.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/editing-changes.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 编辑和迭代更改

**变更中的每个工件都只是一个可以随时编辑的 Markdown 文件。** 没有锁定的“计划阶段”，没有批准门，没有需要进入的特殊编辑模式。开始构建后想要更改提案吗？打开`proposal.md`并更改它。在实施过程中意识到设计是错误的？修复 `design.md` 并继续。这就是全部答案，而且是设计使然。

此页面适用于您认为“等等，我可以返回并更改它吗？”的时刻。是的。以下是针对每种常见情况的方法。

## 编辑任何内容的两种方法

你总是拥有两者：

1. **直接编辑文件。**工件是`openspec/changes/<name>/`中的纯Markdown。在编辑器中打开 `proposal.md`、`design.md`、`tasks.md` 或 `specs/` 下的增量规格并进行更改。不需要其他任何东西。

2. **请你的AI修改它。**在聊天中，只需说出你想要的内容：“更新提案以放弃缓存想法并添加速率限制部分”或“设计应该使用队列，而不是轮询。” AI 使用其余更改作为上下文来为您编辑工件。

使用适合当时的情况。小措辞调整？编辑文件。实质性反思？让 AI 在完整的上下文中进行修改。

## “开始后如何更新提案（或规格）？”

更新一下吧。同样的变化，精致。

如果您使用扩展命令，自然流程是：编辑工件，然后运行 `/opsx:continue` 以从新状态获取，或运行 `/opsx:apply` 以继续根据更新的计划实施。如果您使用默认的 `core` 命令，请编辑工件并运行 `/opsx:apply`；它读取当前文件，因此它会根据工件现在所说的内容进行构建。

心智模型：工件是实时计划，而不是签署的合同。 AI 始终根据其当前内容工作，因此编辑它们可以引导工作。

```text
You: I want to change the approach in this change.

You: [edit design.md, or tell the AI:]
     Update design.md to use a background job instead of a synchronous call.

AI:  Updated design.md. The task list still fits; want me to continue applying?

You: /opsx:apply
```

这回答了一个非常常见的问题：没有单独的“更新提案”命令，因为您不需要它。该文件是事实来源，编辑它（手动或通过 AI）就是更新。

## “实施后如何回去审核？”

你不必“回去”，因为你从未离开过。工作流程是流畅的：审查、编辑和实施并不是您所陷入的连续阶段。

具体来说，经过一些`/opsx:apply`工作后：

- 想重新审视计划吗？打开工件并阅读它们，或在终端中运行 `openspec show <change>` 以获得综合视图。
- 发现有什么需要改变的吗？编辑工件（或询问 AI），然后继续。
- 想要进行结构化检查以确保代码与计划相符吗？运行`/opsx:verify`（扩展命令）。它报告完整性、正确性和连贯性，而不会阻塞任何内容。请参阅[工作流程：验证](<OpenSpec 快速与扩展工作流.md#验证检查你的工作>)。

没有可返回的“审查阶段”，因为审查是您可以在任何时候进行的事情，包括在实施之后。

## “我手动编辑了代码。如何将其与 OpenSpec 协调一致？”

这种情况经常发生，而且没关系。您在编辑器中调整了一些内容，但现在代码和工件不一致。让它们在正确的方向上恢复同步：

- **代码现在是正确的，规范已过时。**更新增量规范（和任务，如果相关）以描述您实际交付的行为。在归档之前，规范应该与现实相符，因为归档会将规范合并到事实来源中。
- **规范是正确的，代码漂移了。**继续构建或修复，直到代码与规范匹配。

发现不匹配的快速方法是 `/opsx:verify`：它读取您的工件和代码并告诉您它们的分歧之处。将其输出视为协调的待办事项列表，然后在他们同意后存档。

原则：在存档时，您的规格将成为记录的真相。因此，在归档之前，请诚实地说明代码的用途。欢迎手动编辑；只是不要让他们悄悄地取消规范的同步。

## 完善您不满意的提案

如果生成的提案未达标，您可以采取以下三项不错的举措：

- **就地迭代。**告诉AI哪里出了问题（“范围太宽，放弃管理功能”）并让它修改。最便宜而且通常是正确的。
- **先探索，然后重新提出。**如果问题是想法本身不清楚，请回到`/opsx:explore`，仔细思考，然后提出更尖锐的建议。请参阅[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)。
- **重新开始。**如果意图发生根本改变，新的更改可能比修补旧的更改更清晰。

接下来，最后一步有自己的决策指南。

## 何时更新与开始新的更改

简版：**同作品精炼时更新；当意图发生根本性改变或范围扩展到不同的工作时开始新的工作。**

- 相同的目标，更好的方法？更新。
- 范围缩小（现在发布 MVP，稍后再发布）？更新，然后存档，然后是第二阶段的新更改。
- 问题本身发生了变化（“添加黑暗模式”变成了“构建完整的主题系统”）？新变化。

[工作流程：何时更新与开始新鲜](<OpenSpec 快速与扩展工作流.md#何时更新与重新开始>)中有完整的流程图和工作示例，[OPSX：何时更新与开始新鲜](<OpenSpec OPSX 工作流机制.md#何时更新与重新开始>)中有更深入的处理。

## 关于任务的注释

`tasks.md` 是一个实时清单，而不是一个冻结的计划。在实施时，您可以添加发现的任务、删除不必要的任务或重新排序它们。 AI 在 `/opsx:apply` 期间完成项目时会检查项目，如果您稍后回来，它会从第一个未检查的任务恢复。预计会在飞行途中编辑列表。

## 下一步去哪里

- [工作流程](<OpenSpec 快速与扩展工作流.md>) - 模式，以及更新与新的决策指南
- [审查变更](<OpenSpec 变更审阅指南.md>) - 在构建计划之前花两分钟的时间浏览计划
- [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>) - 当一个想法需要重新思考时退后一步的地方
- [命令](<OpenSpec AI 聊天命令参考.md>) - `/opsx:continue`、`/opsx:apply` 和 `/opsx:verify` 详细信息
- [概念：工件](<OpenSpec 核心概念与规格结构.md#文物>) - 每个工件的用途


## 原文对照

<details>
<summary>展开完整原文</summary>

# Editing & Iterating on a Change

**Every artifact in a change is just a Markdown file you can edit at any time.** There is no locked "planning phase," no approval gate, no special edit mode to enter. Want to change the proposal after you've started building? Open `proposal.md` and change it. Realized the design is wrong mid-implementation? Fix `design.md` and keep going. That's the whole answer, and it's by design.

This page is for the moment you think "wait, can I go back and change that?" Yes. Here's how, for each common case.

## Two ways to edit anything

You always have both:

1. **Edit the file directly.** Artifacts are plain Markdown in `openspec/changes/<name>/`. Open `proposal.md`, `design.md`, `tasks.md`, or a delta spec under `specs/` in your editor and change it. Nothing else is required.

2. **Ask your AI to revise it.** In chat, just say what you want: "Update the proposal to drop the caching idea and add a rate-limit section," or "the design should use a queue, not polling." The AI edits the artifact for you, using the rest of the change as context.

Use whichever fits the moment. Small wording tweak? Edit the file. Substantive rethink? Let the AI revise with full context.

## "How do I update the proposal (or specs) after I've started?"

Just update it. Same change, refined.

If you're using the expanded commands, the natural flow is: edit the artifact, then run `/opsx:continue` to pick up from the new state, or `/opsx:apply` to keep implementing against the updated plan. If you're on the default `core` commands, edit the artifact and run `/opsx:apply`; it reads the current files, so it builds against whatever the artifacts now say.

The mental model: artifacts are the live plan, not a signed contract. The AI always works from their current contents, so editing them steers the work.

```text
You: I want to change the approach in this change.

You: [edit design.md, or tell the AI:]
     Update design.md to use a background job instead of a synchronous call.

AI:  Updated design.md. The task list still fits; want me to continue applying?

You: /opsx:apply
```

This answers a very common question: there's no separate "update proposal" command because you don't need one. The file is the source of truth, and editing it (by hand or via the AI) is the update.

## "How do I go back to review after implementing?"

You don't have to "go back," because you never left. The workflow is fluid: review, edit, and implementation aren't sequential phases you're trapped in.

Concretely, after some `/opsx:apply` work:

- Want to re-examine the plan? Open the artifacts and read them, or run `openspec show <change>` in your terminal for a consolidated view.
- Found something to change? Edit the artifact (or ask the AI to), then continue.
- Want a structured check that the code matches the plan? Run `/opsx:verify` (expanded command). It reports completeness, correctness, and coherence without blocking anything. See [Workflows: Verify](<OpenSpec 快速与扩展工作流.md#verify-check-your-work>).

There's no "review phase" to return to, because review is something you can do at any point, including after implementation.

## "I edited the code by hand. How do I reconcile that with OpenSpec?"

This happens constantly and it's fine. You tweaked something in your editor, and now the code and the artifacts disagree. Bring them back in sync in whichever direction is true:

- **The code is now correct, the spec is stale.** Update the delta spec (and tasks, if relevant) to describe the behavior you actually shipped. The spec should match reality before you archive, because archiving merges the spec into your source of truth.
- **The spec is correct, the code drifted.** Keep building or fixing until the code matches the spec.

A fast way to surface mismatches is `/opsx:verify`: it reads your artifacts and your code and tells you where they diverge. Treat its output as a to-do list for reconciliation, then archive once they agree.

The principle: at archive time, your specs become the truth of record. So before you archive, make the specs honest about what the code does. Manual edits are welcome; just don't let them quietly desync the spec.

## Refining a proposal you're not happy with

If a generated proposal misses the mark, you have three good moves:

- **Iterate in place.** Tell the AI what's off ("the scope is too broad, drop the admin features") and let it revise. Cheapest and usually right.
- **Explore first, then re-propose.** If the problem is that the idea itself is unclear, step back to `/opsx:explore`, think it through, and let a sharper proposal come out of that. See [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>).
- **Start fresh.** If the intent has fundamentally changed, a new change can be clearer than patching the old one.

That last move has its own decision guide, next.

## When to update vs. start a new change

Short version: **update when it's the same work refined; start new when the intent fundamentally changed or the scope exploded into different work.**

- Same goal, better approach? Update.
- Scope narrowing (ship the MVP now, more later)? Update, then archive, then a new change for phase two.
- The problem itself changed ("add dark mode" became "build a full theming system")? New change.

There's a full flowchart and worked examples in [Workflows: When to Update vs Start Fresh](<OpenSpec 快速与扩展工作流.md#when-to-update-vs-start-fresh>) and a deeper treatment in [OPSX: When to Update vs. Start Fresh](<OpenSpec OPSX 工作流机制.md#when-to-update-vs-start-fresh>).

## A note on tasks

`tasks.md` is a living checklist, not a frozen plan. As you implement, you can add tasks you discover, remove ones that turned out unnecessary, or reorder them. The AI checks items off as it completes them during `/opsx:apply`, and it resumes from the first unchecked task if you come back later. Editing the list mid-flight is expected.

## Where to go next

- [Workflows](<OpenSpec 快速与扩展工作流.md>) - patterns, plus the update-vs-new decision guide
- [Reviewing a Change](<OpenSpec 变更审阅指南.md>) - the two-minute pass on a plan before you build it
- [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) - the place to step back to when an idea needs rethinking
- [Commands](<OpenSpec AI 聊天命令参考.md>) - `/opsx:continue`, `/opsx:apply`, and `/opsx:verify` in detail
- [Concepts: Artifacts](<OpenSpec 核心概念与规格结构.md#artifacts>) - what each artifact is for


</details>
