# 从 Linear 启动 Copilot 任务

说明安装应用、提供任务上下文、选择模型与 Agent、设置分支和引导会话，帮助从问题跟踪系统推进代码修改。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-integrations/integrate-cloud-agent-with-linear.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/从 Linear 启动 Copilot 任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating Copilot cloud agent with Linear
shortTitle: Integrate cloud agent with Linear
allowTitleToDifferFromFilename: true
intro: 'Use the {% data variables.product.prodname_copilot_short %} integration in Linear to provide context, customize how the agent runs, and open pull requests, all from within your Linear workspace.'
product: '{% data reusables.copilot.plans.permission-paid-plans-cfi %}'
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/integrate-cloud-agent-with-linear
  - /copilot/how-tos/use-copilot-agents/coding-agent/integrate-coding-agent-with-linear
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> {% data variables.product.prodname_copilot %} 使用 AI。检查是否有错误。参见[AUTOTITLE](<https://github.com/copilot/responsible-use/agents>)。

Linear 中的 {% data variables.product.prodname_copilot_short %} 集成允许您在不离开 Linear 工作区的情况下调用 {% data variables.copilot.copilot_cloud_agent %}。在线性问题中，您可以启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话、自定义模型、自定义 agent 或 agent 使用的分支、引导会话并打开拉取请求，所有这些都使用问题描述和评论的上下文。

有关其他 {% data variables.product.prodname_copilot_short %} 集成的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>)。

> [!NOTE]
> 当您在线性问题中提及 @{% data variables.product.github %} 或将 {% data variables.product.prodname_copilot_short %} 分配给线性问题时，agent 会捕获整个问题描述和评论作为您的请求的上下文。这有助于 agent 了解问题并根据问题中的所有信息实施适当的解决方案。此上下文存储在拉取请求中。

## 先决条件

* 您必须拥有一个 {% data variables.product.github %} 帐户，可以通过付费 {% data variables.product.prodname_copilot_short %} 计划访问 {% data variables.product.prodname_copilot_short %}。
* 您必须拥有 Linear 帐户并且是团队成员。

## 在 Linear 中安装 {% data variables.product.prodname_copilot_short %} 应用程序

> [!NOTE]
> * 要在 Linear 中安装 {% data variables.product.prodname_copilot_short %} 应用程序，您必须是要安装该应用程序的组织或企业的所有者。您还必须是 Linear 中的工作区管理员。
> * 如果您的组织是使用 {% data variables.enterprise.data_residency %} 的企业的一部分，请从 `SUBDOMAIN.ghe.com/apps/external-app/github-copilot-for-linear` 上可供您的企业使用的应用程序列表中安装集成。将 SUBDOMAIN 替换为您企业的子域 {% data variables.enterprise.data_residency_site %}。

{% data variables.product.prodname_copilot_short %} 应用程序只需在组织中安装一次。安装应用程序后，组织的任何成员都可以将其 {% data variables.product.prodname_copilot_short %} 帐户连接到该应用程序并开始使用它。

1. Go 到 [{% data variables.product.prodname_copilot_short %} 对于线性页面](<https://github.com/apps/github-copilot-for-linear?ref_product=copilot&ref_type=engagement&ref_style=text>) 然后单击 **配置**.
1. 按照屏幕上的提示在您想要使用该应用程序的组织或企业中配置和授权该应用程序。

## 在 Linear 中使用 {% data variables.product.prodname_copilot_short %} 应用程序

第一次在 Linear 中使用 {% data variables.product.prodname_copilot_short %} 应用程序时，您必须将其连接到您的 {% data variables.product.github %} 帐户。您还必须指定要使用的 {% data variables.copilot.copilot_cloud_agent %} 存储库。只有对指定存储库具有 ** 写入 ** 访问权限的用户才能触发 {% data variables.copilot.copilot_cloud_agent %} 在该存储库中工作。没有存储库 **write** 访问权限的问题贡献者可以通过向问题对话提供输入来帮助指导 {% data variables.product.prodname_copilot_short %}，该对话在创建拉取请求时用作上下文。

1. 在 Linear 中，创建一个要使用 {% data variables.copilot.copilot_cloud_agent %} 的问题。
1. 单击 ** 分配 ** 下拉列表，然后选择 **{% data variables.product.prodname_copilot %}**。
1. 如果您尚未指定要使用的 {% data variables.copilot.copilot_cloud_agent %} 存储库，系统会提示您立即指定。这是 {% data variables.copilot.copilot_cloud_agent %} 打开与此问题相关的拉取请求的位置。
1. 如果这是您第一次使用该应用程序，系统会提示您登录您的 {% data variables.product.github %} 帐户并授权该应用程序。按照提示完成授权。
1. 在线性问题的“链接”部分中，您将看到由 {% data variables.copilot.copilot_cloud_agent %} 创建的链接“[WIP]”拉取请求。单击链接查看 {% data variables.product.github %} 上的拉取请求。
1. {% data variables.copilot.copilot_cloud_agent %} 完成拉取请求后，系统会在线性问题的“活动”部分添加一条通知。

## 在线性中自定义 {% data variables.copilot.copilot_cloud_agent %}

您可以使用 Linear 的 agent 指南自定义 {% data variables.copilot.copilot_cloud_agent %} 在 Linear 中的运行方式，无论是按问题还是在整个工作区中运行。默认存储库、首选模型或分支约定等重复首选项最好使用 [线性 agent 指导 ](<#使用线性-agent-引导>) 设置一次。

### 使用线性 agent 引导

Linear 的 agent 指导是您在工作区或团队级别设置的提示。每次触发时，Linear 都会自动将此指导传递给 {% data variables.copilot.copilot_cloud_agent %}。

agent 指南的常见用途包括：

* 指定要打开拉取请求的默认存储库。
* 指定首选模型。
* 指定要使用的自定义 agent。
* 定义分支命名约定或默认基本分支。

有关 Linear 的 agent 指南的更多信息，请参阅 Linear 文档中的 [ 指南 agents](<https://linear.app/docs/agents-in-linear#guiding-agents>)。

### 选择型号

当您从线性问题触发 {% data variables.copilot.copilot_cloud_agent %} 时，您可以通过在问题描述中指定模型来选择 {% data variables.product.prodname_copilot_short %} 使用哪个模型。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>)。

要为工作区或团队中的每次运行设置默认模型，请将其添加到您的 agent 指南中。请参阅 [使用线性 agent 指南 ](<#使用线性-agent-引导>)。

### 使用自定义agent

你可以有 {% data variables.copilot.copilot_cloud_agent %} 使用自定义 agent 从你的 {% data variables.product.github %} 当它通过指定哪个从 Linear 打开拉取请求时，存储库 agent 在问题描述中使用。有关更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>).

要始终在工作区或团队中使用相同的自定义 agent，请在 agent 指南中指定。请参阅 [使用线性 agent 指南 ](<#使用线性-agent-引导>)。

### 设置基础分支和工作分支

当{% data variables.copilot.copilot_cloud_agent %}打开线性问题的拉取请求时，您可以选择：

* 拉取请求目标的 ** 基本分支 **。
* **工作分支**名称{% data variables.copilot.copilot_cloud_agent %}用于其提交。

要在每次运行中应用相同的基本分支或分支命名约定，请将其添加到您的 agent 指南中。请参阅 [使用线性 agent 指南 ](<#使用线性-agent-引导>)。

### 主持会议

当你从线性问题触发{% data variables.copilot.copilot_cloud_agent %}后，你可以继续指挥agent。为此，请在线性问题的评论中提及 `@{% data variables.product.prodname_copilot %}` 以及附加说明，或在 agent 聊天面板中输入附加说明。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> {% data variables.product.prodname_copilot %} uses AI. Check for mistakes. See [AUTOTITLE](<https://github.com/copilot/responsible-use/agents>).

The {% data variables.product.prodname_copilot_short %} integration in Linear allows you to invoke {% data variables.copilot.copilot_cloud_agent %} without leaving your Linear workspace. From within a Linear issue you can initiate {% data variables.copilot.copilot_cloud_agent_short %} sessions, customize which model, custom agent, or branches the agent uses, steer a session, and open pull requests, all using the context of your issue description and comments.

For information about additional {% data variables.product.prodname_copilot_short %} integrations, see [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>).

> [!NOTE]
> When you mention @{% data variables.product.github %} in, or assign {% data variables.product.prodname_copilot_short %} to, a Linear issue, the agent captures the entire issue description and comments as context for your request. This helps the agent to understand the issue and implement an appropriate solution, based on all of the information in the issue. This context is stored in the pull request.

## Prerequisites

* You must have a {% data variables.product.github %} account with access to {% data variables.product.prodname_copilot_short %} through a paid {% data variables.product.prodname_copilot_short %} plan.
* You must have a Linear account and be a member of a team.

## Installing the {% data variables.product.prodname_copilot_short %} app in Linear

> [!NOTE]
> * To install the {% data variables.product.prodname_copilot_short %} app in Linear, you must be an owner of the organization or enterprise where you want to install the app. You must also be a workspace administrator in Linear.
> * If your organization is part of an enterprise that uses {% data variables.enterprise.data_residency %}, install the integration from the list of apps available to your enterprise at `SUBDOMAIN.ghe.com/apps/external-app/github-copilot-for-linear`. Replace SUBDOMAIN with your enterprise's subdomain of {% data variables.enterprise.data_residency_site %}.

The {% data variables.product.prodname_copilot_short %} app only needs to be installed once in an organization. After the app is installed, any member of the organization can connect their {% data variables.product.prodname_copilot_short %} account to the app and start using it.

1. Go to the [{% data variables.product.prodname_copilot_short %} for Linear page](<https://github.com/apps/github-copilot-for-linear?ref_product=copilot&ref_type=engagement&ref_style=text>) and click **Configure**.
1. Follow the prompts on screen to configure and authorize the app in the organization or enterprise where you want to use it.

## Using the {% data variables.product.prodname_copilot_short %} app in Linear

The first time you use the {% data variables.product.prodname_copilot_short %} app in Linear, you must connect it to your {% data variables.product.github %} account. You must also specify a repository for {% data variables.copilot.copilot_cloud_agent %} to use. Only users with **write** access to the specified repository can trigger {% data variables.copilot.copilot_cloud_agent %} to work in that repository. Contributors to the issue without repository **write** access can help guide {% data variables.product.prodname_copilot_short %} by providing input to the issue conversation, which is used as context when creating the pull request.

1. In Linear, create an issue where you want to use {% data variables.copilot.copilot_cloud_agent %}.
1. Click the **Assign** dropdown, then select **{% data variables.product.prodname_copilot %}**.
1. If you haven't yet specified a repository for {% data variables.copilot.copilot_cloud_agent %} to use, you are prompted to do so now. This is where {% data variables.copilot.copilot_cloud_agent %} opens the pull request related to this issue.
1. If this is your first time using the app, you are prompted to sign in to your {% data variables.product.github %} account and authorize the app. Follow the prompts to complete the authorization.
1. In the "Links" section of your Linear issue, you will see a linked "[WIP]" pull request created by {% data variables.copilot.copilot_cloud_agent %}. Click the link to view the pull request on {% data variables.product.github %}.
1. Once {% data variables.copilot.copilot_cloud_agent %} has finished working on the pull request, a notification is added to the "Activity" section of your Linear issue.

## Customizing {% data variables.copilot.copilot_cloud_agent %} in Linear

You can customize how {% data variables.copilot.copilot_cloud_agent %} runs in Linear, either per issue or across your entire workspace by using Linear's agent guidance. Recurring preferences such as a default repository, a preferred model, or branch conventions are best set once with [Linear agent guidance](<#using-linear-agent-guidance>).

### Using Linear agent guidance

Linear's agent guidance is a prompt that you set at the workspace or team level. Linear automatically passes this guidance to {% data variables.copilot.copilot_cloud_agent %} every time you trigger it.

Common uses for agent guidance include:

* Specifying a default repository to open pull requests against.
* Specifying a preferred model.
* Specifying a custom agent to use.
* Defining branch-naming conventions or a default base branch.

For more information about Linear's agent guidance, see [Guiding agents](<https://linear.app/docs/agents-in-linear#guiding-agents>) in the Linear documentation.

### Choosing a model

When you trigger {% data variables.copilot.copilot_cloud_agent %} from a Linear issue you can choose which model {% data variables.product.prodname_copilot_short %} uses by specifying the model in the issue description. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>).

To set a default model for every run in your workspace or team, add it to your agent guidance. See [Using Linear agent guidance](<#using-linear-agent-guidance>).

### Using a custom agent

You can have {% data variables.copilot.copilot_cloud_agent %} use a custom agent from your {% data variables.product.github %} repository when it opens a pull request from Linear by specifying which agent to use in the issue description. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>).

To always use the same custom agent across your workspace or team, specify it in your agent guidance. See [Using Linear agent guidance](<#using-linear-agent-guidance>).

### Setting the base branch and working branch

When {% data variables.copilot.copilot_cloud_agent %} opens a pull request from a Linear issue, you can choose:

* The **base branch** the pull request targets.
* The **working branch** name {% data variables.copilot.copilot_cloud_agent %} uses for its commits.

To apply the same base branch or branch-naming convention across every run, add it to your agent guidance. See [Using Linear agent guidance](<#using-linear-agent-guidance>).

### Steering a session

After you trigger {% data variables.copilot.copilot_cloud_agent %} from a Linear issue, you can continue to direct the agent. To do this, mention `@{% data variables.product.prodname_copilot %}` in a comment on the Linear issue with additional instructions, or input additional instructions in the agent chat panel.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


</details>
