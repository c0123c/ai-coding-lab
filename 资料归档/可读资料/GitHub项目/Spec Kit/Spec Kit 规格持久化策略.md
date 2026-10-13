# Spec Kit 规格持久化策略

比较回写代码、向前迭代和持续维护三种策略，帮助团队决定需求变化后如何保存规格、计划和任务。

- 来源：Spec Kit
- 资料类型：存档文档
- 存档版本：`0443760542e7`。
- 文档时间：源码提交（存档版）：[2026-06-09](<https://github.com/github/spec-kit/commit/0b82a1ddf1d00af4df99f6a84fdd58a9d435cb5c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/spec-kit/blob/main/docs/concepts/spec-persistence.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Spec Kit/docs/concepts/spec-persistence.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 规范持久性模型

规格套件有意让团队控制需求更改后 `spec.md`、
`plan.md` 和 `tasks.md` 发生的情况。该工具包为您提供了
可重复的工作流程，但它并不强制采用一种工件维护策略。

本页面列出了三种常见模型，以便团队可以明确选择。
没有默认模型，规格套件也不需要任何模型。

## 两个单独的问题

规范驱动的开发有一个暂时的问题：
规范应该持续多久？ 
[SDD工具](<https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html>)
框架的概述将生命周期分为三个级别：

- **Spec-first**：在编码之前编写规范，然后允许将其丢弃。
- **规范锚定**：实现后保留规范并用于将来
变化。
- **Spec-as-source**：将规范视为唯一的人工编辑源，并且
从中重新生成实现工件。

规格套件还暴露了第二个问题：当
需求发生变化时，工件集会发生什么？下面的模型描述了该突变策略。

## 回流规格

当`spec.md`、`plan.md`、`tasks.md`和实现
都允许相互通知时使用回流。

在此模型中，编辑可以从任何工件开始。开发人员可能会在实施过程中更新
`tasks.md`，在技术发现后修改`plan.md`，
或在产品澄清后调整`spec.md`。然后，团队会协调手动设置的 
 工件，以便最终的项目历史记录仍然有意义。

回流在以下情况下效果很好：

- 团队规模足够小，可以快速发现并协调偏差
- 实施发现预计将重塑最初的计划
- 速度比保持每个中间决策不可变更重要
历史

主要风险是无声分歧。如果团队更改较低级别的工件 
，而没有将决策反映回 `spec.md`，则未来的贡献者可能 
 不知道应该信任哪个工件。

## 前流规格

当每个功能目录应保留历史记录时，请使用 flow-forward。

在此模型中，已完成的工件被视为不可变的。当需求
发生变化时，团队会创建一个新的功能目录，而不是改变
现有的`spec.md`、`plan.md`或`tasks.md`。旧目录对于审核、比较或解释项目如何达到当前状态仍然有用
。

在以下情况下，前向流动效果很好：

- 可审核性和可追溯性很重要
- 功能范围很广，很少重新审视
- 团队希望随着时间的推移，需求变化有一个清晰的顺序

主要的权衡是重复。相关决策可以分布在
多个功能目录中，因此团队需要命名、链接或审查习惯
，以使沿袭易于遵循。

## 生活规格

当 `spec.md` 是合约并且其他工件是 
 派生自它时，请使用实时规范。

在此模型中，团队首先更新 `spec.md`，然后从该源重新生成或修改 
`plan.md` 和 `tasks.md`。计划和任务列表仍然
有价值，但它们被视为一次性派生，而不是永久
事实来源。

生活规范在以下情况下效果良好：

- 产品合同足够稳定，可以拥有工作流程
- 团队可以在规范更改后轻松地重新生成派生工件
- 需求和实施之间的一致性比保持
每个中间计划都完好无损

主要风险是如果派生工件被
丢弃而没有在其他地方保留重要决策，那么就会失去有用的实现原理。

## 选择型号

该模型是团队约定，而不是 CLI 设置。一个项目甚至可以在不同领域使用
不同的模型，只要贡献者知道哪个
适用。

|型号|突变规则|最适合|留意|
|---|---|---|---|
|回流规格|编辑任何工件，然后协调|快速迭代、紧密协作|工件之间的无声漂移|
|前流规格|为新需求创建新功能目录|审计追踪和历史清晰性|重复或支离破碎的上下文|
|生活规格|编辑`spec.md`；重新生成派生的工件|合同规格|重新生成的文件中丢失了基本原理|

如果您的团队尚未选择模型，请首先回答两个问题：

1. 已完成的功能目录应该是历史记录还是可编辑的作品
地区？
2. `spec.md` 是唯一事实来源，还是 `plan.md` 和 `tasks.md`
允许成为同等来源吗？

一旦这些答案明确，请在您的项目
章程或团队入职说明中记录约定，以便未来的贡献者知道如何处理
更改。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Spec Persistence Models

Spec Kit intentionally leaves teams in control of what happens to `spec.md`,
`plan.md`, and `tasks.md` after requirements change. The toolkit gives you a
repeatable workflow, but it does not force one artifact maintenance strategy.

This page names three common models so teams can make that choice explicit.
None is the default, and none is required by Spec Kit.

## Two Separate Questions

Spec-driven development has a temporal question: how long should the
specification matter? One
[overview of SDD tooling](<https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html>)
frames that lifecycle in three levels:

- **Spec-first**: write a spec before coding, then allow it to be discarded.
- **Spec-anchored**: keep the spec after implementation and use it for future
  changes.
- **Spec-as-source**: treat the spec as the only human-edited source and
  regenerate implementation artifacts from it.

Spec Kit also exposes a second question: what happens to the artifact set when
requirements change? The models below describe that mutation strategy.

## Flow-Back Spec

Use flow-back when `spec.md`, `plan.md`, `tasks.md`, and the implementation are
all allowed to inform each other.

In this model, edits can begin in any artifact. A developer might update
`tasks.md` during implementation, revise `plan.md` after a technical discovery,
or adjust `spec.md` after a product clarification. The team then reconciles the
artifact set manually so the final project history still makes sense.

Flow-back works well when:

- the team is small enough to notice and reconcile drift quickly
- implementation discoveries are expected to reshape the original plan
- speed matters more than preserving each intermediate decision as immutable
  history

The main risk is silent divergence. If the team changes lower-level artifacts
without reflecting the decision back into `spec.md`, future contributors may
not know which artifact to trust.

## Flow-Forward Spec

Use flow-forward when each feature directory should remain a historical record.

In this model, completed artifacts are treated as immutable. When requirements
change, the team creates a new feature directory instead of mutating the
existing `spec.md`, `plan.md`, or `tasks.md`. The older directory remains useful
for audit, comparison, or explaining how the project reached its current state.

Flow-forward works well when:

- auditability and traceability matter
- features are well-scoped and rarely revisited in place
- the team wants a clear sequence of requirement changes over time

The main tradeoff is duplication. Related decisions can be spread across
multiple feature directories, so teams need naming, linking, or review habits
that make the lineage easy to follow.

## Living Spec

Use living spec when `spec.md` is the contract and the other artifacts are
derived from it.

In this model, teams update `spec.md` first and then regenerate or revise
`plan.md` and `tasks.md` from that source. The plan and task list are still
valuable, but they are treated as disposable derivations rather than permanent
sources of truth.

Living spec works well when:

- the product contract is stable enough to own the workflow
- the team is comfortable regenerating derived artifacts after spec changes
- consistency between requirements and implementation matters more than keeping
  every intermediate plan intact

The main risk is losing useful implementation rationale if derived artifacts are
discarded without preserving important decisions elsewhere.

## Choosing a Model

The model is a team convention, not a CLI setting. A project can even use
different models in different areas, as long as contributors know which one
applies.

| Model | Mutation rule | Best fit | Watch out for |
|---|---|---|---|
| Flow-back spec | Edit any artifact, then reconcile | Fast iteration and close collaboration | Silent drift between artifacts |
| Flow-forward spec | Create a new feature directory for new requirements | Audit trails and historical clarity | Duplicate or fragmented context |
| Living spec | Edit `spec.md`; regenerate derived artifacts | Spec as contract | Lost rationale in regenerated files |

If your team has not chosen a model yet, start by answering two questions:

1. Should completed feature directories be historical records or editable work
   areas?
2. Is `spec.md` the single source of truth, or are `plan.md` and `tasks.md`
   allowed to become co-equal sources?

Once those answers are clear, document the convention in your project
constitution or team onboarding notes so future contributors know how to handle
changes.


</details>
