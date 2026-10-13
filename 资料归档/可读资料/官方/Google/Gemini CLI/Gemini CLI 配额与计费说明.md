# Gemini CLI 配额与计费说明

按认证和账号类型解释免费额度、固定订阅及按量使用，介绍查询限制与控制成本；这是归档版本的计费信息。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-06-18](<https://github.com/google-gemini/gemini-cli/commit/93844dfa10f6d71edc09be40dfde205edfbcc939>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/resources/quota-and-pricing.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/resources/quota-and-pricing.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI：配额和定价

Gemini CLI 提供慷慨的免费套餐，涵盖许多个人开发者的 
 用例。对于企业或专业用途，或者如果您需要增加配额，
可以根据您的身份验证帐户类型使用多个选项。

有关可用订阅的高级比较以及选择适合您需求的 
 配额，请参阅 [ 计划页面 ](<https://geminicli.com/plans/>)。

## 概述

本文概述了使用不同身份验证方法时适用于 Gemini CLI
 的具体配额和定价。

下表总结了可用配额及其各自的限制：

|认证方式|等级/订阅|每个用户每天的最大请求数|
| :-------------------- | :------------------------------ | :-------------------------------- |
|**Google 帐号**|Gemini 代码辅助（个人）|1,000 请求|
|                       |Google AI 专业版|1,500 请求|
|                       |Google AI 超|2,000 请求|
|**Gemini API 键**|免费套餐（未付费）|250 请求|
|                       |即用即付（付费）|各不相同|
|**顶点 AI**|快速模式（免费）|各不相同|
|                       |即用即付（付费）|各不相同|
|**Google 工作空间**|代码辅助标准|1,500 请求|
|                       |代码协助企业|2,000 请求|
|                       |工作空间 AI 超|2,000 请求|

一般来说，有以下三类可供选择：

- 免费使用：非常适合实验和轻度使用。
- 付费等级（固定价格）：适合有需要的个人开发者或企业
更慷慨的每日配额和可预测的成本。
- 即付即用-Go：专业使用、长期运行的最灵活选项
任务，或者当您需要完全控制您的使用情况时。

每个用户每分钟的请求数受到限制，并且取决于 
 服务在高需求时的可用性。

## 免费使用

访问 Gemini CLI 从慷慨的免费套餐开始，非常适合 
 实验和轻度使用。

您的免费使用受到以下限制，具体取决于您的
授权类型。

### 使用 Google 登录（Gemini 个人代码协助）

对于使用Google帐户进行身份验证以访问Gemini代码
个人的协助。这包括：

- 1000 最大模型请求数/用户/天
- 模型请求将在 Gemini 模型系列中提出，具体取决于
  Gemini CLI.

了解更多信息，请访问
[Gemini个人限制代码协助](<https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli>)。

### 使用 Gemini API 密钥登录（未付费）

如果您使用 Gemini API 密钥，您还可以享受免费套餐。此
包括：

- 250 最大模型请求数/用户/天
- 模型仅请求 Flash 模型。

了解更多信息，请访问
[Gemini API 速率限制](<https://ai.google.dev/gemini-api/docs/rate-limits>)。

### 使用 Vertex AI 登录（快速模式）

Vertex AI 提供快速模式，无需启用计费。此
包括：

- 您需要启用计费之前的 90 天。
- 配额和模型特定于您的帐户，其限制也有所不同。

了解更多信息，请参阅
[顶点AI快速模式限制](<https://cloud.google.com/vertex-ai/generative-ai/docs/start/express-mode/overview#quotas>)。

## 付费等级：固定成本的更高限额

如果您用完初始请求数，您可以通过升级到以下订阅之一来继续受益于 
Gemini CLI：

### 个人

当您使用个人帐户登录时，这些级别适用。要验证您是否使用
个人帐户，请访问
[Google One](<https://one.google.com/about/plans?hl=en-US&g1_landing_page=0>)：

- 如果您使用个人帐户，您将看到您的个人仪表板。
- 如果您不是个人帐户，您将看到：“您当前已签名
进入您的 Google 工作区帐户。”

**支持的层级：** _- 上面未列出的层级，包括 Google AI 另外，
 不受支持。_

- [Google AI Pro 和 AI Ultra ](<https://gemini.google/subscriptions/>)。这是
推荐给个人开发者。配额和定价基于固定
价格订阅。

对于可预测的费用，您可以使用Google登录。

了解更多信息，请访问 
 [Gemini 代码辅助配额和限制 ](<https://developers.google.com/gemini-code-assist/resources/quotas>)

### 通过您的组织

当您使用 Google 工作区 
 帐户登录时，这些级别适用。

- 要验证您的帐户类型，请访问
[Google一页](<https://one.google.com/about/plans?hl=en-US&g1_landing_page=0>)。
- 如果您看到消息“您当前已签名”，则您正在使用工作区帐户
进入您的 Google 工作区帐户”。

**支持的层：** _- 不支持上面未列出的层，包括工作区 AI
Standard/Plus 和 AI 扩展。_

- [工作空间AI Ultra Access](<https://workspace.google.com/products/ai-ultra/>)。
- [通过Google云](<https://cloud.google.com/gemini/docs/codeassist/overview>)购买Gemini代码辅助订阅。

配额和定价基于分配的 
 许可证席位的固定价格订阅。对于可预测的费用，您可以使用 Google 登录。

这包括以下请求限制：

  - Gemini代码辅助标准版：
    - 1500 最大模型请求数/用户/天
  - Gemini代码辅助企业版：
    - 2000 最大模型请求数/用户/天
  - 模型请求将在 Gemini 模型系列中提出，具体取决于
    Gemini CLI.

[详细了解 Gemini 代码辅助许可证限制 ](<https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli>)。

## 随用随付

如果您达到每日请求限制或在 
 升级后耗尽您的 Gemini Pro 配额，最灵活的解决方案是切换到即用即付模式，
 在该模式中，您根据使用的特定处理量付费。这是
推荐的不间断访问路径。

为此，请使用 Gemini API 键或 Vertex AI 登录。

### 顶点 AI（常规模式）

用于构建、部署和管理 AI 模型的企业级平台，
 包括 Gemini。它提供增强的安全性、数据治理以及与其他 Google 云服务的集成 
。

- 配额：由动态共享配额系统或预购配置管理
吞吐量。
- 成本：基于模型和代币使用情况。

了解更多信息，请访问 
[Vertex AI 动态共享配额 ](<https://cloud.google.com/vertex-ai/generative-ai/docs/resources/dynamic-shared-quota>)
 和 [Vertex AI 定价 ](<https://cloud.google.com/vertex-ai/pricing>)。

### Gemini API 键

非常适合想要使用 Gemini
 模型快速构建应用程序的开发人员。这是使用模型最直接的方法。

- 配额：因定价等级而异。
- 成本：根据定价等级和 model/token 使用情况而变化。

了解更多信息，请访问
[Gemini API 速率限制](<https://ai.google.dev/gemini-api/docs/rate-limits>)、
[Gemini API 定价](<https://ai.google.dev/gemini-api/docs/pricing>)

需要强调的是，使用 API 密钥时，您需要按 token/call.
 付费，这对于许多代币很少的小型调用来说可能会更昂贵，但这是确保您的工作流程不会因达到 
 配额限制而中断的唯一方法 
。

## Gemini 工作空间计划

这些计划目前仅适用于使用由基于 Google 的体验提供的 Gemini 基于网络的产品 
（例如，Gemini 网络应用程序或 
Flow 视频编辑器）。这些计划不适用于为 
Gemini CLI 提供动力的 API 使用。正在积极考虑为未来的
支持支持这些计划。

## 检查使用情况和限制

您可以使用
`/stats model`命令检查当前的令牌使用情况和适用的限制。此命令提供当前 
 会话的令牌使用情况的快照，以及有关与 
 当前配额关联的限制的信息。

有关`/stats`命令及其子命令的详细信息，请参见
[命令参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md#stats>)。

会话结束时退出时还会显示模型使用情况的摘要。

## 避免高成本的技巧

使用即用即付计划时，请注意您的使用情况，以避免意外的
费用。

- **有选择性地提出建议**：在接受建议之前，尤其是
对于重构大型代码库等计算密集型任务，
 考虑这是否是最具成本效益的方法。
- **使用精准提示**：您是按次付费，所以要考虑最多的
获得您想要的结果的有效方法。精心设计的提示通常可以在一次通话中获得
您所需的答案，而不是多次来回
交互。
- **监控您的使用情况**：使用`/stats model`命令跟踪您的代币
会话期间的使用情况。这可以帮助您实时了解自己的支出
。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI: Quotas and pricing

Gemini CLI offers a generous free tier that covers many individual developers'
use cases. For enterprise or professional usage, or if you need increased quota,
several options are available depending on your authentication account type.

For a high-level comparison of available subscriptions and to select the right
quota for your needs, see the [Plans page](<https://geminicli.com/plans/>).

## Overview

This article outlines the specific quotas and pricing applicable to Gemini CLI
when using different authentication methods.

The following table summarizes the available quotas and their respective limits:

| Authentication method | Tier / Subscription             | Maximum requests per user per day |
| :-------------------- | :------------------------------ | :-------------------------------- |
| **Google account**    | Gemini Code Assist (Individual) | 1,000 requests                    |
|                       | Google AI Pro                   | 1,500 requests                    |
|                       | Google AI Ultra                 | 2,000 requests                    |
| **Gemini API key**    | Free tier (Unpaid)              | 250 requests                      |
|                       | Pay-as-you-go (Paid)            | Varies                            |
| **Vertex AI**         | Express mode (Free)             | Varies                            |
|                       | Pay-as-you-go (Paid)            | Varies                            |
| **Google Workspace**  | Code Assist Standard            | 1,500 requests                    |
|                       | Code Assist Enterprise          | 2,000 requests                    |
|                       | Workspace AI Ultra              | 2,000 requests                    |

Generally, there are three categories to choose from:

- Free Usage: Ideal for experimentation and light use.
- Paid Tier (fixed price): For individual developers or enterprises who need
  more generous daily quotas and predictable costs.
- Pay-As-You-Go: The most flexible option for professional use, long-running
  tasks, or when you need full control over your usage.

Requests are limited per user per minute and are subject to the availability of
the service in times of high demand.

## Free usage

Access to Gemini CLI begins with a generous free tier, perfect for
experimentation and light use.

Your free usage is governed by the following limits, which depend on your
authorization type.

### Log in with Google (Gemini Code Assist for individuals)

For users who authenticate by using their Google account to access Gemini Code
Assist for individuals. This includes:

- 1000 maximum model requests / user / day
- Model requests will be made across the Gemini model family as determined by
  Gemini CLI.

Learn more at
[Gemini Code Assist for Individuals Limits](<https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli>).

### Log in with Gemini API Key (unpaid)

If you are using a Gemini API key, you can also benefit from a free tier. This
includes:

- 250 maximum model requests / user / day
- Model requests to Flash model only.

Learn more at
[Gemini API Rate Limits](<https://ai.google.dev/gemini-api/docs/rate-limits>).

### Log in with Vertex AI (Express Mode)

Vertex AI offers an Express Mode without the need to enable billing. This
includes:

- 90 days before you need to enable billing.
- Quotas and models are specific to your account and their limits vary.

Learn more at
[Vertex AI Express Mode Limits](<https://cloud.google.com/vertex-ai/generative-ai/docs/start/express-mode/overview#quotas>).

## Paid tier: Higher limits for a fixed cost

If you use up your initial number of requests, you can continue to benefit from
Gemini CLI by upgrading to one of the following subscriptions:

### Individuals

These tiers apply when you sign in with a personal account. To verify whether
you're on a personal account, visit
[Google One](<https://one.google.com/about/plans?hl=en-US&g1_landing_page=0>):

- If you are on a personal account, you will see your personal dashboard.
- If you are not on a personal account, you will see: "You're currently signed
  in to your Google Workspace Account."

**Supported tiers:** _- Tiers not listed above, including Google AI Plus, are
not supported._

- [Google AI Pro and AI Ultra](<https://gemini.google/subscriptions/>). This is
  recommended for individual developers. Quotas and pricing are based on a fixed
  price subscription.

  For predictable costs, you can log in with Google.

  Learn more at
  [Gemini Code Assist Quotas and Limits](<https://developers.google.com/gemini-code-assist/resources/quotas>)

### Through your organization

These tiers are applicable when you are signing in with a Google Workspace
account.

- To verify your account type, visit
  [the Google One page](<https://one.google.com/about/plans?hl=en-US&g1_landing_page=0>).
- You are on a workspace account if you see the message "You're currently signed
  in to your Google Workspace Account".

**Supported tiers:** _- Tiers not listed above, including Workspace AI
Standard/Plus and AI Expanded, are not supported._

- [Workspace AI Ultra Access](<https://workspace.google.com/products/ai-ultra/>).
- [Purchase a Gemini Code Assist Subscription through Google Cloud](<https://cloud.google.com/gemini/docs/codeassist/overview>).

  Quotas and pricing are based on a fixed price subscription with assigned
  license seats. For predictable costs, you can sign in with Google.

  This includes the following request limits:

  - Gemini Code Assist Standard edition:
    - 1500 maximum model requests / user / day
  - Gemini Code Assist Enterprise edition:
    - 2000 maximum model requests / user / day
  - Model requests will be made across the Gemini model family as determined by
    Gemini CLI.

  [Learn more about Gemini Code Assist license limits](<https://developers.google.com/gemini-code-assist/resources/quotas#quotas-for-agent-mode-gemini-cli>).

## Pay as you go

If you hit your daily request limits or exhaust your Gemini Pro quota even after
upgrading, the most flexible solution is to switch to a pay-as-you-go model,
where you pay for the specific amount of processing you use. This is the
recommended path for uninterrupted access.

To do this, log in using a Gemini API key or Vertex AI.

### Vertex AI (regular mode)

An enterprise-grade platform for building, deploying, and managing AI models,
including Gemini. It offers enhanced security, data governance, and integration
with other Google Cloud services.

- Quota: Governed by a dynamic shared quota system or pre-purchased provisioned
  throughput.
- Cost: Based on model and token usage.

Learn more at
[Vertex AI Dynamic Shared Quota](<https://cloud.google.com/vertex-ai/generative-ai/docs/resources/dynamic-shared-quota>)
and [Vertex AI Pricing](<https://cloud.google.com/vertex-ai/pricing>).

### Gemini API key

Ideal for developers who want to quickly build applications with the Gemini
models. This is the most direct way to use the models.

- Quota: Varies by pricing tier.
- Cost: Varies by pricing tier and model/token usage.

Learn more at
[Gemini API Rate Limits](<https://ai.google.dev/gemini-api/docs/rate-limits>),
[Gemini API Pricing](<https://ai.google.dev/gemini-api/docs/pricing>)

It’s important to highlight that when using an API key, you pay per token/call.
This can be more expensive for many small calls with few tokens, but it's the
only way to ensure your workflow isn't interrupted by reaching a limit on your
quota.

## Gemini for workspace plans

These plans currently apply only to the use of Gemini web-based products
provided by Google-based experiences (for example, the Gemini web app or the
Flow video editor). These plans do not apply to the API usage which powers the
Gemini CLI. Supporting these plans is under active consideration for future
support.

## Check usage and limits

You can check your current token usage and applicable limits using the
`/stats model` command. This command provides a snapshot of your current
session's token usage, as well as information about the limits associated with
your current quota.

For more information on the `/stats` command and its subcommands, see the
[Command Reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md#stats>).

A summary of model usage is also presented on exit at the end of a session.

## Tips to avoid high costs

When using a pay-as-you-go plan, be mindful of your usage to avoid unexpected
costs.

- **Be selective with suggestions**: Before accepting a suggestion, especially
  for a computationally intensive task like refactoring a large codebase,
  consider if it's the most cost-effective approach.
- **Use precise prompts**: You are paying per call, so think about the most
  efficient way to get your desired result. A well-crafted prompt can often get
  you the answer you need in a single call, rather than multiple back-and-forth
  interactions.
- **Monitor your usage**: Use the `/stats model` command to track your token
  usage during a session. This can help you stay aware of your spending in real
  time.


</details>
