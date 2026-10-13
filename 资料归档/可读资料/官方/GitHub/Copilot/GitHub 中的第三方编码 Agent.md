# GitHub 中的第三方编码 Agent

说明支持的合作方、异步开发入口、启用方式、模型、安全验证与费用；区分合作方 Agent 和 Copilot 自身能力。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/about-third-party-coding-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/GitHub 中的第三方编码 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About third-party coding agents
allowTitleToDifferFromFilename: true
shortTitle: Third-party coding agents
intro: 'You can use third-party coding agents alongside {% data variables.copilot.copilot_cloud_agent %} to work asynchronously on your development tasks on {% data variables.product.github %}.'
product: '{% data reusables.copilot.plans.permission-paid-plans-no-purchase-link %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: concepts
redirect_from:
  - /copilot/concepts/agents/about-third-party-agents
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE] 第三方编码agents目前位于{% data variables.release-phases.public_preview %}中。

## 简介

您可以使用第三方编码 agents 和 {% data variables.copilot.copilot_cloud_agent %} 来异步处理您的开发任务。您可以分配现有问题或向 agent 发出提示，这将处理所需的更改并创建拉取请求。当agent完成后，它会请求您进行审查，您可以留下拉取请求评论以要求agent进行迭代。

编码 agents 受到与 {% data variables.copilot.copilot_cloud_agent %} 相同的安全保护、缓解和限制。要了解有关如何使用编码 agents 的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)。

### 哪里可以使用编码 agents

您可以在以下位置通过编码 agents 来启动任务：

* **Agents选项卡**：在[Agents选项卡](<https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text&utm_source=docs-3p-agents-tab-cta&utm_medium=docs&utm_campaign=agent-3p-platform-feb-2026>)的提示框中选择agent，然后开始一个新任务并观察agent开始处理拉取请求。
* **问题**：将agent分配给存储库中的现有问题。
* **拉取请求**：在现有拉取请求的评论中提及`@AGENT_NAME`，要求其进行更改。
* 在 [**{% data variables.product.prodname_mobile %}**](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-mobile>) 上：从 **主页 ** 视图中，单击 {% octicon "plus" aria-hidden="true" aria-label="plus" %} 启动新的 agent 会话。
* 在 [**{% data variables.product.prodname_vscode %}**](<https://code.visualstudio.com/docs/copilot/agents/overview#_create-an-agent-session>) 中：在聊天视图中启动新会话，或将现有会话委托给不同的 agent。

### 使编码 agents 可用

您必须先在帐户策略中启用任务，然后才能将任务分配给 {% data variables.product.github %} 上的编码 agents。

* 对于 **{% data variables.copilot.copilot_pro %}、{% data variables.copilot.copilot_pro_plus %} 和 {% data variables.copilot.copilot_max %} 订阅者 **，请参阅 [AUTOTITLE](<https://github.com/free-pro-team@latest/copilot/how-tos/manage-your-account/manage-policies#enabling-or-disabling-third-party-coding-agents-in-your-repositories>)。
* 对于 **{% data variables.copilot.copilot_for_business %} 和 {% data variables.copilot.copilot_enterprise %} 订阅者 **，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>) 或 [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>)。

这些政策不适用于 {% data variables.product.prodname_vscode %} 中的 **本地 ** agents。要在 {% data variables.product.prodname_vscode %} 中配置 agent 设置，请参阅 {% data variables.product.prodname_vscode %} 文档中的 [agents](<https://code.visualstudio.com/docs/copilot/agents/overview#_types-of-agents>) 类型。要调整 {% data variables.product.prodname_vscode %} 中的企业 agent 设置，请参阅 {% data variables.product.prodname_vscode %} 文档中的 [启用或禁用 [​​agents](<https://code.visualstudio.com/docs/enterprise/ai-settings#_enable-or-disable-the-use-of-agents>) 的使用。

## 支持的编码agents

{% data variables.product.github %} 支持以下第三方 agents：

* [{% data variables.product.prodname_anthropic_claude %}](<https://github.com/copilot/concepts/agents/anthropic-claude>)
* [{% data variables.product.prodname_openai_codex %}](<https://github.com/copilot/concepts/agents/openai-codex>)

## AI 第三方型号 agents

使用第三方【agent】启动任务时，可以选择【agent】使用的【AI】型号。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于任务类型。如需帮助决定使用哪个模型，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>)。

您还可以选择**自动**，在以下支持的型号中进行选择。选择**自动**与第三方agents不会**而不是**杠杆{% data variables.copilot.copilot_auto_model_selection %}。参见[AUTOTITLE](<https://github.com/copilot/concepts/models/auto-model-selection>)。

每个agent有以下型号：

### {% data variables.product.prodname_openai_codex %}

* 汽车
{% data reusables.copilot.openai-codex-agent-models %}

### {% data variables.product.prodname_anthropic_claude %}

* 汽车
{% data reusables.copilot.anthropic-claude-agent-models %}

## 安全验证

当第三方编码 agent 创建或修改代码时，{% data variables.product.github %} 会自动扫描生成的代码是否存在安全问题，并在拉取请求最终确定之前尝试解决这些问题。这降低了 agent 引入硬编码机密、不安全依赖项和其他漏洞等问题的可能性。

以下工具和流程扫描安全问题：

* **{% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %}** 识别代码安全问题。
* **{% data variables.product.prodname_secret_scanning_caps %}** 检测敏感信息，例如 API 密钥、令牌和其他秘密。
* 新引入的依赖项将根据 **{% data variables.product.prodname_advisory_database %}** 检查恶意软件建议和 CVSS- 评级为高或严重漏洞。

安全验证不需要 {% data variables.product.prodname_GHAS %} 许可证。

## 使用费用

编码 agents 消耗 **{% data variables.product.prodname_actions %} 分钟 ** 和 **{% data variables.product.prodname_ai_credits_short %}**。每个 agent 会话根据使用的模型和处理的令牌数量消耗 {% data variables.product.prodname_ai_credits_short %}。

在您包含的{% data variables.product.prodname_actions %}分钟和{% data variables.product.prodname_ai_credits_short %}内，您可以使用agents，而不会产生额外费用。参见[AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>)。

## 合作伙伴 agents

在您的用户或组织{% data variables.copilot.copilot_cloud_agent %}设置中启用合作伙伴agents时，将为相应的agent安装{% data variables.product.prodname_github_app %}。

* **允许Claude编码agent**将安装`anthropic code agent`
* **允许Codex编码agent**将安装`openai code agent`

这些 {% data variables.product.prodname_github_apps %} 采取的操作将在您的审核日志中可见，但 {% data variables.product.prodname_github_apps %} 本身不会在您帐户的 {% data variables.product.prodname_github_app %} 安装列表中可见。

## 后续步骤

* 要开始管理 agents，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)。
* 要了解如何托管和提供 AI 模型，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE] Third-party coding agents are currently in {% data variables.release-phases.public_preview %}.

## Introduction

You can use third-party coding agents alongside {% data variables.copilot.copilot_cloud_agent %} to work asynchronously on your development tasks. You can assign an existing issue or give a prompt to an agent, which will work on the required changes and create a pull request. When the agent finishes, it will request a review from you, and you can leave pull request comments to ask the agent to iterate.

Coding agents are subject to the same security protections, mitigations, and limitations as {% data variables.copilot.copilot_cloud_agent %}. To learn more about how you can use coding agents, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>).

### Where you can use coding agents

You can kick off tasks with coding agents in the following locations:

* **The Agents tab**: Select an agent under the prompt box in the [Agents tab](<https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text&utm_source=docs-3p-agents-tab-cta&utm_medium=docs&utm_campaign=agent-3p-platform-feb-2026>), then kick off a new task and watch the agent get to work on a pull request.
* **Issues**: Assign the agent to an existing issue in a repository.
* **Pull requests**: Mention `@AGENT_NAME` in a comment on an existing pull request to ask it to make changes.
* On [**{% data variables.product.prodname_mobile %}**](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-mobile>): From the **Home** view, click {% octicon "plus" aria-hidden="true" aria-label="plus" %} to start a new agent session.
* In [**{% data variables.product.prodname_vscode %}**](<https://code.visualstudio.com/docs/copilot/agents/overview#_create-an-agent-session>): Start a new session in the chat view, or delegate an existing session to a different agent.

### Making coding agents available

Before you can assign tasks to coding agents on {% data variables.product.github %}, they must be enabled in your account policies.

* For **{% data variables.copilot.copilot_pro %}, {% data variables.copilot.copilot_pro_plus %}, and {% data variables.copilot.copilot_max %} subscribers**, see [AUTOTITLE](<https://github.com/free-pro-team@latest/copilot/how-tos/manage-your-account/manage-policies#enabling-or-disabling-third-party-coding-agents-in-your-repositories>).
* For **{% data variables.copilot.copilot_for_business %} and {% data variables.copilot.copilot_enterprise %} subscribers**, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>) or [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>).

These policies do not apply to **local** agents in {% data variables.product.prodname_vscode %}. To configure agent settings in {% data variables.product.prodname_vscode %}, see [Types of agents](<https://code.visualstudio.com/docs/copilot/agents/overview#_types-of-agents>) in the {% data variables.product.prodname_vscode %} documentation. To adjust enterprise agent settings in {% data variables.product.prodname_vscode %}, see [Enable or disable the use of agents](<https://code.visualstudio.com/docs/enterprise/ai-settings#_enable-or-disable-the-use-of-agents>) in the {% data variables.product.prodname_vscode %} documentation.

## Supported coding agents

The following third-party agents are supported on {% data variables.product.github %}:

* [{% data variables.product.prodname_anthropic_claude %}](<https://github.com/copilot/concepts/agents/anthropic-claude>)
* [{% data variables.product.prodname_openai_codex %}](<https://github.com/copilot/concepts/agents/openai-codex>)

## AI models for third-party agents

When starting a task with a third-party agent, you can select the AI model used by the agent. You may find that different models perform better, or provide more useful responses, depending on the type of task. For help deciding which model to use, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>).

You can also select **Auto**, which chooses between the supported models below. Choosing **Auto** with third-party agents does **not** leverage {% data variables.copilot.copilot_auto_model_selection %}. See [AUTOTITLE](<https://github.com/copilot/concepts/models/auto-model-selection>).

The following models are available for each agent:

### {% data variables.product.prodname_openai_codex %}

* Auto
{% data reusables.copilot.openai-codex-agent-models %}

### {% data variables.product.prodname_anthropic_claude %}

* Auto
{% data reusables.copilot.anthropic-claude-agent-models %}

## Security validation

When a third-party coding agent creates or modifies code, {% data variables.product.github %} automatically scans the generated code for security issues and attempts to resolve them before the pull request is finalized. This reduces the likelihood of the agent introducing problems such as hardcoded secrets, insecure dependencies, and other vulnerabilities.

The following tools and processes scan for security issues:

* **{% data variables.product.prodname_codeql %} {% data variables.product.prodname_code_scanning %}** identifies code security issues.
* **{% data variables.product.prodname_secret_scanning_caps %}** detects sensitive information such as API keys, tokens, and other secrets.
* Newly introduced dependencies are checked against the **{% data variables.product.prodname_advisory_database %}** for malware advisories and CVSS-rated High or Critical vulnerabilities.

Security validation does not require a {% data variables.product.prodname_GHAS %} license.

## Usage costs

Coding agents consume **{% data variables.product.prodname_actions %} minutes** and **{% data variables.product.prodname_ai_credits_short %}**. Each agent session consumes {% data variables.product.prodname_ai_credits_short %} based on the model used and the number of tokens processed.

Within your included {% data variables.product.prodname_actions %} minutes and {% data variables.product.prodname_ai_credits_short %}, you can use agents without incurring additional costs. See [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>).

## Partner agents

When enabling partner agents in your user or organization {% data variables.copilot.copilot_cloud_agent %} settings, a {% data variables.product.prodname_github_app %} will be installed for the corresponding agent.

* **Allow Claude coding agent** will install `anthropic code agent`
* **Allow Codex coding agent** will install `openai code agent`

Actions taken by these {% data variables.product.prodname_github_apps %} will be visible in your audit log, but the {% data variables.product.prodname_github_apps %} themselves will not be visible in your account's list of {% data variables.product.prodname_github_app %} installations.

## Next steps

* To start managing agents, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>).
* To learn how AI models are hosted and served, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>).


</details>
