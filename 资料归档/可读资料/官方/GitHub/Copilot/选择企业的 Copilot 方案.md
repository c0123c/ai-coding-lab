# 选择企业的 Copilot 方案

比较 Business 与 Enterprise 的能力、使用额度、业务目标和成本；属于采购与管理参考资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-16](<https://github.com/github/docs/commit/57fae3bbe1c9ae41241bba99290c3e59918c03b8>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/assign-licenses/choose-enterprise-plan.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/选择企业的 Copilot 方案.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Choosing your enterprise's plan for GitHub Copilot
shortTitle: Choose enterprise plan
intro: 'Choose between {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %}.'
versions:
  feature: copilot
redirect_from:
  - /copilot/rolling-out-github-copilot-at-scale/choosing-your-enterprises-plan-for-github-copilot
  - /copilot/rolling-out-github-copilot-at-scale/planning-your-rollout/choosing-your-enterprises-plan-for-github-copilot
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/planning-your-rollout/choosing-your-enterprises-plan-for-github-copilot
  - /copilot/get-started/choosing-your-enterprises-plan-for-github-copilot
  - /copilot/get-started/choose-enterprise-plan
contentType: tutorials
category:
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

当您在公司采用{% data variables.product.prodname_copilot %}时，您将签署专为企业设计的{% data variables.product.prodname_copilot_short %}计划。这些计划允许您向用户授予访问权限并通过策略和审核日志满足安全要求。

要确定哪个计划适合您的公司：

1. 定义您希望通过 {% data variables.product.prodname_copilot_short %} 部署实现的总体目标。考虑下游目标，例如减少安全债务，而不是早期的成功指标，例如开发人员满意度或功能采用率。
1. 了解 {% data variables.copilot.copilot_business_short %} 和 {% data variables.copilot.copilot_enterprise_short %} 的优点，包括附带的 {% data variables.product.prodname_ai_credits_short %}。
1. 选择 {% data variables.product.prodname_copilot_short %} 计划，其津贴和功能将帮助您实现目标。

   >[!TIP] 当您在企业级订阅{% data variables.product.prodname_copilot_short %}时，您可以为企业中的每个组织单独选择一个计划。这使您可以通过试点计划评估 {% data variables.copilot.copilot_enterprise_short %} 的优势，或在影响最大的组织中启用它。

本文介绍了可用的计划，并提供了 {% data variables.copilot.copilot_enterprise_short %} 如何帮助您实现特定业务目标的示例。

## 计划和{% data variables.product.prodname_ai_credits_short %}

{% data variables.product.company_short %} 为 {% data variables.product.prodname_ghe_cloud %} 上的客户提供两种 {% data variables.product.prodname_copilot_short %} 计划：

* **{% data variables.copilot.copilot_business_short %}**（每个用户每月 {% data variables.copilot.cfb_price_per_month %}）：包括每个用户的 {% data variables.copilot.ai_credits_per_user_business %} {% data variables.product.prodname_ai_credits_short %} 以及 IDE 和 {% data variables.product.github %} 上的大多数 {% data variables.product.prodname_copilot_short %} 功能。
* **{% data variables.copilot.copilot_enterprise_short %}**（每个用户每月 {% data variables.copilot.ce_price_per_month %}）：包括每个用户 {% data variables.copilot.ai_credits_per_user_enterprise %} {% data variables.product.prodname_ai_credits_short %}，并且通常允许提前访问新功能和模型。

如需完整比较，请参阅我们的 [ 计划页面 ](<https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=text>)。

{% data variables.product.prodname_ai_credits_short %} 由高级 {% data variables.product.prodname_copilot_short %} 功能和模型消耗，包括 AI agents。每个计划包含的 {% data variables.product.prodname_ai_credits_short %} 都集中在您的企业中，因此较重的用户可以从较轻的用户未使用的部分中提取。通过让会员访问更多 {% data variables.product.prodname_ai_credits_short %}，您可以通过 AI agents 扩展您的公司并推动真正的业务成果。

默认情况下，使用量可以在包含的池之外继续使用，额外使用量按 {% data variables.product.prodname_ai_credit_singular %} 按 {% data variables.product.prodname_ai_credits_value %} 收费。您可以通过预算控制来控制这一点。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>)。

## {% data variables.copilot.copilot_enterprise_short %} 如何支持业务目标？

下表显示了您的公司可能为 {% data variables.product.prodname_copilot_short %} 部署设置的目标示例，以及 {% data variables.product.prodname_ai_credits_short %} 和其他 {% data variables.copilot.copilot_enterprise_short %} 功能如何帮助您实现这些目标。

|目标|需要解决的问题|{% data variables.copilot.copilot_enterprise_short %} 如何提供帮助|
| ---- | ---------------- | --------------------------------------------------------------- |
|**减少积压**|团队可能没有能力解决开发过程中出现的积压问题或非必要问题。这可能会导致特征质量逐渐下降。|随着包含更多{% data variables.product.prodname_ai_credits_short %}，用户可以将更多问题分配给**{% data variables.copilot.copilot_cloud_agent %}**，它可以在后台完成修复错误或添加功能增强等任务。|
|**加速拉取请求**|由于审查周期过长，团队在合并拉取请求时经常会遇到延迟。这可能会导致开发过程中出现瓶颈，并减慢功能的交付和改进速度。|通过包含更多内容 {% data variables.product.prodname_ai_credits_short %}，用户可以收到来自 **{% data variables.copilot.copilot_code-review_short %}** 的拉取请求的更多评论，通常会在人工审阅者可用之前标记错误或可能的改进。|
|**减少技术债务**|低效或难以阅读的代码会随着时间的推移而积累，使团队成员更难加入和理解代码的新领域。|随着更多包含的{% data variables.product.prodname_ai_credits_short %}和对最新模型的访问，用户可以在IDE中使用agent模式来重构代码，选择具有**更好的上下文感知**的模型，这些模型更适合复杂重构等任务。|

## {% data variables.copilot.copilot_enterprise_short %} 是最具成本效益的选择吗？

我们建议将 {% data variables.copilot.copilot_business_short %} 计划中包含的 {% data variables.product.prodname_ai_credits_short %} 视为基准，而不是限制。使用代理工作流程（包括 agent 模式、{% data variables.copilot.copilot_cloud_agent %} 和 {% data variables.copilot.copilot_code-review_short %} 等功能）的开发人员可能会消耗超出所包含的数量。

如果您的公司正在从代理工作流程中获得价值，您可能希望为开发人员提供更多的 {% data variables.product.prodname_ai_credits_short %}。根据消费模式，最具成本效益的方法是将用户升级到 {% data variables.copilot.copilot_enterprise_short %}，或者通过适当的预算控制允许超出所含池的额外使用量。

要查看企业的消耗情况，请参阅企业计费设置中的 AI 使用情况仪表板。有关管理支出的指南，请参阅 [AUTOTITLE](<https://github.com/copilot/tutorials/budgets/optimizing-your-budget-configuration>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

When you adopt {% data variables.product.prodname_copilot %} in a company, you will sign up to a {% data variables.product.prodname_copilot_short %} plan designed for businesses. These plans allow you to grant access to users and meet security requirements with policies and audit logs.

To identify which plan is right for your company:

1. Define the overall goals you are hoping to achieve with your {% data variables.product.prodname_copilot_short %} rollout. Think about downstream goals such as reducing security debt, as opposed to earlier success indicators such as developer satisfaction or feature adoption.
1. Understand the benefits of {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %}, including included {% data variables.product.prodname_ai_credits_short %}.
1. Choose the {% data variables.product.prodname_copilot_short %} plan with the allowances and features that will help you meet your goals.

   >[!TIP] When you subscribe to {% data variables.product.prodname_copilot_short %} at the enterprise level, you can choose a plan individually for each organization in your enterprise. This allows you to evaluate the benefits of {% data variables.copilot.copilot_enterprise_short %} with a pilot program or enable it in the organizations where it will have the most impact.

This article explains the available plans and provides examples for how {% data variables.copilot.copilot_enterprise_short %} can help you achieve specific business goals.

## Plans and {% data variables.product.prodname_ai_credits_short %}

{% data variables.product.company_short %} offers two {% data variables.product.prodname_copilot_short %} plans for customers on {% data variables.product.prodname_ghe_cloud %}:

* **{% data variables.copilot.copilot_business_short %}** ({% data variables.copilot.cfb_price_per_month %} per user per month): includes {% data variables.copilot.ai_credits_per_user_business %} {% data variables.product.prodname_ai_credits_short %} per user and most {% data variables.product.prodname_copilot_short %} features in IDEs and on {% data variables.product.github %}.
* **{% data variables.copilot.copilot_enterprise_short %}** ({% data variables.copilot.ce_price_per_month %} per user per month): includes {% data variables.copilot.ai_credits_per_user_enterprise %} {% data variables.product.prodname_ai_credits_short %} per user, and often allows earlier access to new features and models.

For a full comparison, see our [plans page](<https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=text>).

{% data variables.product.prodname_ai_credits_short %} are consumed by advanced {% data variables.product.prodname_copilot_short %} features and models, including AI agents. Each plan's included {% data variables.product.prodname_ai_credits_short %} are pooled across your enterprise, so heavier users can draw from lighter users' unused portions. By giving members access to more {% data variables.product.prodname_ai_credits_short %}, you can scale your company with AI agents and drive real business outcomes.

By default, usage can continue beyond the included pool, with additional usage charged at {% data variables.product.prodname_ai_credits_value %} per {% data variables.product.prodname_ai_credit_singular %}. You can control this with budget controls. See [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>).

## How does {% data variables.copilot.copilot_enterprise_short %} support business goals?

The following table shows examples of goals your company might set for a {% data variables.product.prodname_copilot_short %} rollout, and how {% data variables.product.prodname_ai_credits_short %} and other {% data variables.copilot.copilot_enterprise_short %} features can help you achieve those goals.

| Goal | Problem to solve | How {% data variables.copilot.copilot_enterprise_short %} helps |
| ---- | ---------------- | --------------------------------------------------------------- |
| **Reduce your backlog** | Teams may not have capacity to work on backlog issues or non-essential issues that come up during development. This can lead to a gradual degradation in feature quality. | With more included {% data variables.product.prodname_ai_credits_short %}, users can assign more issues to **{% data variables.copilot.copilot_cloud_agent %}**, which can complete tasks like fixing bugs or adding feature enhancements in the background. |
| **Accelerate pull requests** | Teams often experience delays in merging pull requests due to lengthy review cycles. This can lead to bottlenecks in the development process and slow down the delivery and improvement of features. | With more included {% data variables.product.prodname_ai_credits_short %}, users can receive more reviews on pull requests from **{% data variables.copilot.copilot_code-review_short %}**, often flagging bugs or possible improvements before a human reviewer is available. |
| **Reduce technical debt** | Inefficient or hard-to-read code can accumulate over time, making it harder for team members to onboard and understand new areas of the code. | With more included {% data variables.product.prodname_ai_credits_short %} and access to the latest models, users can use agent mode in their IDE to refactor code, choosing models with **greater contextual awareness** that are more suited to tasks like complex refactoring. |

## Is {% data variables.copilot.copilot_enterprise_short %} the most cost effective choice?

We recommend considering the included {% data variables.product.prodname_ai_credits_short %} in the {% data variables.copilot.copilot_business_short %} plan as a baseline, not a limit. Developers using agentic workflows including features like agent mode, {% data variables.copilot.copilot_cloud_agent %}, and {% data variables.copilot.copilot_code-review_short %} are likely to consume beyond the included amount.

If your company is gaining value from agentic workflows, you will likely want to make more {% data variables.product.prodname_ai_credits_short %} available to developers. Depending on consumption patterns, the most cost effective approach is either to upgrade users to {% data variables.copilot.copilot_enterprise_short %} or to allow additional usage beyond the included pool with appropriate budget controls.

To review your enterprise's consumption, see the AI usage dashboard in your enterprise billing settings. For guidance on managing spend, see [AUTOTITLE](<https://github.com/copilot/tutorials/budgets/optimizing-your-budget-configuration>).


</details>
