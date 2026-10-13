# 从 Jira 启动 Copilot 任务

讲解安装、分派工作项、查看和引导会话、指定模型与 Agent，以及权限和登录问题排查。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-integrations/integrate-cloud-agent-with-jira.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/从 Jira 启动 Copilot 任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating Copilot cloud agent with Jira
shortTitle: Integrate cloud agent with Jira
allowTitleToDifferFromFilename: true
intro: 'You can use the {% data variables.product.github %} integration in Jira to provide context and open pull requests, all from within your Jira workspace.'
product: '{% data reusables.copilot.plans.permission-paid-plans-cfi %}'
versions:
  feature: copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/integrate-cloud-agent-with-jira
  - /copilot/how-tos/use-copilot-agents/coding-agent/integrate-coding-agent-with-jira
  - /early-access/copilot/integrate-coding-agent-with-jira
contentType: how-tos
category:
  - Integrate Copilot with your tools
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [!NOTE]
> {% data variables.product.prodname_copilot %} 使用 AI。检查是否有错误。参见[AUTOTITLE](<https://github.com/copilot/responsible-use/agents>)。

Jira 中的 {% data variables.product.prodname_copilot %} 集成允许您在不离开 Jira 工作区的情况下调用 {% data variables.copilot.copilot_cloud_agent %}。在 Jira 中，您可以使用工作项的标题、描述、标签、评论和任何 Atlassian 自定义字段（例如验收标准）的上下文来启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话并打开拉取请求。

## 先决条件

* 您必须拥有一个 {% data variables.product.github %} 帐户，可以通过付费 {% data variables.product.prodname_copilot_short %} 计划访问 {% data variables.product.prodname_copilot_short %}。
* 您必须拥有 Jira Cloud 帐户，**Jira 必须是启用 AI- 的应用程序 **，并且必须为您的组织激活 Rovo。请参阅 Atlassian 文档中的 [为应用程序 ](<https://support.atlassian.com/organization-administration/docs/activate-atlassian-intelligence-for-products>) 激活 AI。
* Jira 和 {% data variables.product.github %} 都必须完成安装和身份验证。

## 安装

要安装 {% data variables.product.prodname_copilot %} for Jira 应用程序并为您的 {% data variables.product.github %} 组织或企业帐户授权，您需要：

* 您的 Jira 站点的管理员权限。
* 您的 {% data variables.product.github %} 组织的所有者或 {% data variables.product.github %} 应用程序管理员权限。

此集成依赖于 Atlassian Forge 应用程序和 {% data variables.product.github %} 应用程序。两者都是集成所必需的。成功安装后，对 {% data variables.product.github %} 存储库具有 ** 写入 ** 访问权限的 Jira 工作区的授权用户将能够从 Jira 触发 agent。

### 为 {% data variables.product.prodname_dotcom_the_website %} 安装 Jira 应用程序 {% data variables.product.prodname_copilot %}

1. 导航至 [{% data variables.product.prodname_copilot %} Jira 安装页面](<https://marketplace.atlassian.com/apps/1582455624?ref_product=copilot&ref_type=engagement&ref_style=text>) 在 Atlassian 市场上。
1. 点击**立即获取**。
1. 选择您想要安装 {% data variables.product.github %} 应用程序的 Atlassian 站点。
1. 单击**查看**检查安装详细信息，然后单击**立即获取**。

在您的 Jira 站点中安装 Jira 的 {% data variables.product.prodname_copilot %} 后，您需要授权该应用程序访问您的 {% data variables.product.github %} 组织和存储库。

1. 安装完成后，在 Jira 中的确认消息中单击**配置**。
   * 如果没有自动重定向，请转到{% data variables.product.prodname_marketplace %}上的[{% data variables.product.prodname_copilot %} Jira安装页面](<https://github.com/apps/github-copilot-for-jira?ref_product=copilot&ref_type=engagement&ref_style=text>)。单击**安装**。
1. If you are not already logged in to {% data variables.product.prodname_dotcom %}, click the highlighted **Log in to {% data variables.product.prodname_dotcom %}** and follow the prompts to log in to your {% data variables.product.github %} account and authorize the application.
   * If your organization or enterprise uses single sign-on (SSO), you may need to start an active SAML session for your organization and perform an additional authorization step.
1. 单击**安装应用程序**，授予应用程序访问您的GitHub帐户信息的权限。
1. 选择应用程序有权访问的组织和存储库。默认情况下，您的 Jira 工作区已启用 {% data variables.product.github %} 组织。 Optionally, in the **Install {% data variables.product.prodname_copilot %} for Jira** page, _deselect_ the organization and repositories you _don't want the application to have access to_.
1. 单击**安装**。
1. 安装完成后，您将在 Jira 中的 Jira 应用程序配置页面的 {% data variables.product.prodname_copilot %} 上看到已连接组织的列表。

### 为 {% data variables.enterprise.data_residency_site %} 安装 Jira 应用程序 {% data variables.product.prodname_copilot %}

1. 导航至 [{% data variables.product.prodname_copilot %} 对于吉拉（GHEC 与数据驻留）安装页面](<https://marketplace.atlassian.com/apps/3637796809?ref_product=copilot&ref_type=engagement&ref_style=text>) 在 Atlassian 市场上。
1. 在应用程序名称右侧，单击{% octicon "kebab-horizontal" aria-label="Configure" %}，然后在文本框中输入您的`SUBDOMAIN.ghe.com`。将 SUBDOMAIN 替换为您企业的子域 {% data variables.enterprise.data_residency_site %}。
1. 单击**保存配置**。
1. 点击**立即获取**。
1. 选择您想要安装 {% data variables.product.github %} 应用程序的 Atlassian 站点。
1. 单击**查看**检查安装详细信息，然后单击**立即获取**。

在您的 Jira 站点中安装 Jira 的 {% data variables.product.prodname_copilot %} 后，您需要授权该应用程序访问您的 {% data variables.product.github %} 组织和存储库。

1. 安装完成后，在 Jira 中的确认消息中单击**配置**。
   * 如果您没有自动重定向，请在您的企业可用的应用程序列表中找到 Jira 应用程序的 {% data variables.product.prodname_copilot %}，网址为 `SUBDOMAIN.ghe.com/apps/external-app/github-copilot-for-jira`。单击**安装**。
1. If you are not already logged in to {% data variables.product.prodname_dotcom %}, click the highlighted **Log in to {% data variables.product.prodname_dotcom %}** and follow the prompts to log in to your {% data variables.product.github %} account and authorize the application.
   * If your organization or enterprise uses SSO, you may need to start an active SAML session for your organization and perform an additional authorization step.
1. 单击**安装应用程序**，授予应用程序访问您的GitHub帐户信息的权限。
1. 选择应用程序有权访问的组织和存储库。默认情况下，您的 Jira 工作区已启用 {% data variables.product.github %} 组织。 Optionally, in the **Install {% data variables.product.prodname_copilot %} for Jira** page, _deselect_ the organization and repositories you _don't want the application to have access to_.
1. 单击**安装**。
1. 安装完成后，您将在 Jira 中的 Jira 应用程序配置页面的 {% data variables.product.prodname_copilot %} 上看到已连接组织的列表。

## 在 Jira 中使用 {% data variables.product.prodname_copilot %} 应用程序

必须先为您所属的 {% data variables.product.github %} 组织启用 {% data variables.product.prodname_copilot_short %} 应用程序，然后才能开始使用它。

第一次在 Jira 中使用 {% data variables.copilot.copilot_cloud_agent %} 时，您需要将其连接到您的 {% data variables.product.github %} 帐户。

只有对存储库具有 ** 写入 ** 访问权限的用户才能触发 {% data variables.copilot.copilot_cloud_agent %} 在该存储库中工作。

您可以通过多种方式触发{% data variables.copilot.copilot_cloud_agent %}：

* 使用受让人字段将 ** 将 ** {% data variables.product.prodname_copilot %} 分配给工作项。
* **在工作项的评论中提及** `@{% data variables.product.prodname_copilot %}`。
* **Use a Jira automation.** In your Jira automation rules, select the **Use {% data variables.product.prodname_copilot %}** action and configure your flow to use a custom trigger based on Jira events, such as when a work item is created or transitioned, or a label is applied.有关更多信息，请参阅 Atlassian 文档中的 [在 Jira](<https://support.atlassian.com/jira-software-cloud/docs/work-with-ai-agents-in-jira/>) 中使用 AI agents。

> [!NOTE]
> When you assign {% data variables.product.prodname_copilot_short %} to a Jira work item, the context the agent captures from Jira will be added to the pull request and **visible to everyone** if the repository is public.

### 示例：从 Jira 工作项触发 {% data variables.copilot.copilot_cloud_agent %}

1. 在 Jira 中，打开或创建一个工作项，其中包含您想要委派给 {% data variables.copilot.copilot_cloud_agent %} 的明确要求。
1. 要指定您想要 {% data variables.product.prodname_copilot_short %} 工作的存储库，请在工作项描述或注释中提及它。
1. 将 `{% data variables.product.prodname_copilot %}` 分配给工作项，或在评论中提及 `@{% data variables.product.prodname_copilot %}`。例如：

    ```text
   @{% data variables.product.prodname_copilot %} create a new API endpoint for user authentication in octo-org/octorepo
    ```

1. If you have not previously connected the {% data variables.product.github %} application in Jira to your {% data variables.product.github %} account, follow the prompts to authorize the application for both {% data variables.product.github %} and Atlassian.
1. 一旦 {% data variables.copilot.copilot_cloud_agent %} 开始处理拉取请求，Jira 的聊天面板中就会出现一条评论。发起 agent 会话的用户可以在那里查看进度。
1. 您可以跟进 {% data variables.product.prodname_copilot_short %} 的进一步说明：
    * Use the **Continue in Chat** button under the **Agents** heading to chat directly with {% data variables.product.prodname_copilot_short %} to have updates made to the _current_ pull request.
    * 在工作项的评论中提及 `@{% data variables.product.prodname_copilot %}`，以便在 _new_ 拉取请求中进行更新。

> [！TIP]
> 如果您在1分钟后仍未收到触发{% data variables.copilot.copilot_cloud_agent %}的确认信息，请刷新Jira工作项页面。

### 查看 Jira 中的 agent 活动

虽然 {% data variables.copilot.copilot_cloud_agent_short %} 将其活动流实时传输到 Jira 的聊天面板中，所以您可以在不离开工作项目的情况下跟踪 agent 正在执行的操作。活动流包含指向 {% data variables.product.github %} 上关联的 agent 会话的链接。

### 会议后指导 Jira 的 {% data variables.product.prodname_copilot_short %}

当 {% data variables.copilot.copilot_cloud_agent %} 完成会话时，例如当拉取请求准备好供审核时，您可以指示 {% data variables.product.prodname_copilot_short %} 继续工作：

* 在 Jira 的聊天面板中，选择 {% data variables.product.github %} 上关联的 agent 会话的链接。这将打开 {% data variables.product.github %} 上的 agents 面板，您可以在其中查看会话并发送后续说明以更新 ** 现有的 ** 拉取请求。
* 添加对 Jira 工作项的后续 `@{% data variables.product.prodname_copilot %}` 提及或评论。这将启动一个新会话并打开一个 ** 新的 ** 拉取请求，而不是更新现有请求。

## 在 Jira 中自定义 {% data variables.copilot.copilot_cloud_agent %}

您可以通过指定模型、agents 和自定义指令来自定义 {% data variables.copilot.copilot_cloud_agent %} 在 Jira 工作区中的工作方式。

### 指定型号

当您希望任务在特定模型而不是默认模型上运行时，请指定模型。例如，您可以选择更轻的模型来进行例行的、范围广泛的更改。要更改 {% data variables.copilot.copilot_cloud_agent %} 用于特定任务的模型，请在 {% data variables.product.prodname_copilot_short %} 的说明中包含模型名称，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>)。

### 指定自定义 agent

指定自定义 agent 以根据特定工作流程或存储库定制 {% data variables.copilot.copilot_cloud_agent_short %} 的行为。您可以直接在 Jira 票证中指定来自 {% data variables.product.github %} 存储库的自定义 agent。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)。

### 使用自定义指令

使用自定义指令设置适用于每个会话的默认值，例如目标存储库，因此 {% data variables.product.prodname_copilot_short %} 不必在会话中暂停并要求您输入。您可以在 Jira 工作区级别定义每次触发 {% data variables.copilot.copilot_cloud_agent %} 时应用的自定义指令。

## 使用费用

{% data variables.copilot.copilot_cloud_agent %} 使用 {% data variables.product.prodname_actions %} 分钟和 {% data variables.product.prodname_ai_credits_short %}。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>)。

## 在 Jira 应用程序的 {% data variables.product.prodname_copilot %} 中添加或删除组织

Jira 管理员和 {% data variables.product.github %} 组织所有者可以启用或禁用组织集成。

> [！NOTE]
> 如果在初始安装后将新的 SSO- 受保护组织添加到应用程序中，用户将需要在 {% data variables.product.github %} 中为该组织启动活动的 SAML 会话，以便触发 {% data variables.copilot.copilot_cloud_agent %} 在新组织的存储库中工作来自吉拉。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/enterprise-cloud@latest/authentication/authenticating-with-single-sign-on/about-authentication-with-single-sign-on#about-oauth-apps-github-apps-and-sso>)。

要更改组织对 Jira 应用程序 {% data variables.product.prodname_copilot %} 的访问权限：

1. 在 Jira 中，转到工作区的设置页面。
1. Go 至 {% data variables.product.prodname_copilot %} 应用程序的应用程序设置页面。
1. 或者，单击 **连接更多 {% data variables.product.github %} 组织 ** 将新组织添加到列表中。
1. 为一个或多个列出的组织启用或禁用 {% data variables.product.prodname_copilot_short %} 应用程序。

## 故障排除

如果您遇到问题，请尝试以下解决方案。

### 您看不到 {% data variables.copilot.copilot_cloud_agent %}，并且无法将其分配给 Jira 工作项

在 Jira 中，检查您组织的 Atlassian 管理设置是否设置如下。

1. Jira 是一款启用了 AI- 的应用程序，请参阅 Atlassian 文档中的 [ 为应用程序激活 AI ](<https://support.atlassian.com/organization-administration/docs/activate-atlassian-intelligence-for-products>)。

### 您可以看到 {% data variables.copilot.copilot_cloud_agent %}，但无法将其分配给 Jira 工作项

检查您是否已将 {% data variables.product.github %} 上的个人帐户连接到 Jira 应用程序的 {% data variables.product.prodname_copilot %}。

1. 在 Jira 中，转到您的个人帐户的设置页面。
1. 在常规设置下，为 Jira** 应用程序选择 **{% data variables.product.prodname_copilot %}。
1. 如果您尚未登录{% data variables.product.github %}，请按照提示登录并授权应用程序。

### 与{% data variables.product.prodname_copilot %}聊天时，提示您登录

To sign in to {% data variables.product.prodname_copilot %} for Jira app, follow the steps above in [You can see the {% data variables.copilot.copilot_cloud_agent %} but it is not possible to assign it to a Jira work item](<#you-can-see-the-copilot-cloud-agent-but-it-is-not-possible-to-assign-it-to-a-jira-work-item>).

### 您工作区中的其他用户可以将 {% data variables.copilot.copilot_cloud_agent %} 分配给 Jira 工作项，但您不能

If {% data variables.copilot.copilot_cloud_agent %} cannot see or work with your organization's resources in Jira and your organization uses SSO in {% data variables.product.github %}, you may need to reauthorize the {% data variables.product.prodname_copilot %} for Jira app for your {% data variables.product.github %} account.欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/enterprise-cloud@latest/authentication/authenticating-with-single-sign-on/about-authentication-with-single-sign-on#about-oauth-apps-github-apps-and-sso>)。

要解决此问题，请按照以下步骤为您的组织启动新的活动 SSO 会话：

1. Go 到 {% data variables.product.github %} 中的 [ 组织设置 ](<https://github.com/settings/organizations>)。
1. Under "Single sign-on", find the organization you need to authenticate to and click **Sign out**, and then **Sign in**.
   * If your enterprise manages SSO for your organization, signing in to one organization in the enterprise works as an SSO session for all organizations in the enterprise.
1. 返回 Jira，然后刷新您正在使用的页面。
1. 再次尝试在 Jira 中使用 {% data variables.copilot.copilot_cloud_agent %}。

### {% data variables.product.prodname_copilot %} 没有响应

* 检查 {% data variables.product.github %} 的 [ 状态页面 ](<https://githubstatus.com>) 是否有任何活动事件。
* 检查 [Atlassian 状态页面 ](<https://status.atlassian.com>) 是否有任何活动事件。
* 通过测试您是否可以将 {% data variables.product.prodname_copilot_short %} 分配给 {% data variables.product.github %} 上的问题，验证 {% data variables.copilot.copilot_cloud_agent %} 是否有权访问存储库。
* 验证 Jira 应用程序的 {% data variables.product.prodname_copilot %} 是否有权访问存储库。参见[AUTOTITLE](<https://github.com/apps/using-github-apps/reviewing-and-modifying-installed-github-apps>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#example-atlassian>)
* [与 Atlassian 文档中的 AI agents](<https://support.atlassian.com/jira-software-cloud/docs/collaborate-on-work-items-with-ai-agents/>) 协作处理工作项目


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> {% data variables.product.prodname_copilot %} uses AI. Check for mistakes. See [AUTOTITLE](<https://github.com/copilot/responsible-use/agents>).

The {% data variables.product.prodname_copilot %} integration in Jira allows you to invoke {% data variables.copilot.copilot_cloud_agent %} without leaving your Jira workspace. From within Jira you can initiate {% data variables.copilot.copilot_cloud_agent_short %} sessions and open pull requests, using the context of the work item's title, description, labels, comments, and any Atlassian custom fields such as acceptance criteria.

## Prerequisites

* You must have a {% data variables.product.github %} account with access to {% data variables.product.prodname_copilot_short %} through a paid {% data variables.product.prodname_copilot_short %} plan.
* You must have a Jira Cloud account, **Jira must be an AI-enabled app**, and Rovo must be activated for your organization. See [Activate AI for apps](<https://support.atlassian.com/organization-administration/docs/activate-atlassian-intelligence-for-products>) in the Atlassian documentation.
* Installation and authentication must be completed for both Jira and {% data variables.product.github %}.

## Installation

To install the {% data variables.product.prodname_copilot %} for Jira app and authorize it for your {% data variables.product.github %} organization or enterprise account, you need:

* Administrator permission for your Jira site.
* Owner or {% data variables.product.github %} App manager permissions for your {% data variables.product.github %} organization.

This integration relies on an Atlassian Forge application and a {% data variables.product.github %} application. Both are required for the integration. Once successfully installed, authorized users of the Jira workspace with **write** access to a {% data variables.product.github %} repository will be able to trigger the agent from Jira.

### Installing the {% data variables.product.prodname_copilot %} for Jira app for {% data variables.product.prodname_dotcom_the_website %}

1. Navigate to the [{% data variables.product.prodname_copilot %} for Jira installation page](<https://marketplace.atlassian.com/apps/1582455624?ref_product=copilot&ref_type=engagement&ref_style=text>) on the Atlassian Marketplace.
1. Click **Get it now**.
1. Select the Atlassian site you want to install the {% data variables.product.github %} application in.
1. Click **Review** to check the installation details, and then click **Get it now**.

   Once {% data variables.product.prodname_copilot %} for Jira is installed in your Jira site, you need to authorize the app to access your {% data variables.product.github %} organization and repositories.

1. Click **Configure** in the confirmation message in Jira after installation.
   * If you are not automatically redirected, go to the [{% data variables.product.prodname_copilot %} for Jira installation page](<https://github.com/apps/github-copilot-for-jira?ref_product=copilot&ref_type=engagement&ref_style=text>) on the {% data variables.product.prodname_marketplace %}. Click **Install**.
1. If you are not already logged in to {% data variables.product.prodname_dotcom %}, click the highlighted **Log in to {% data variables.product.prodname_dotcom %}** and follow the prompts to log in to your {% data variables.product.github %} account and authorize the application.
   * If your organization or enterprise uses single sign-on (SSO), you may need to start an active SAML session for your organization and perform an additional authorization step.
1. Click **Install app** to give the app permission to access information on your GitHub account.
1. Choose the organization and repositories the app has access to. Your {% data variables.product.github %} organizations are enabled by default for your Jira workspace. Optionally, in the **Install {% data variables.product.prodname_copilot %} for Jira** page, _deselect_ the organization and repositories you _don't want the application to have access to_.
1. Click **Install**.
1. When installation is complete, you will see a list of connected organizations on the {% data variables.product.prodname_copilot %} for Jira app configuration page in Jira.

### Installing the {% data variables.product.prodname_copilot %} for Jira app for {% data variables.enterprise.data_residency_site %}

1. Navigate to the [{% data variables.product.prodname_copilot %} for Jira (GHEC with Data Residency) installation page](<https://marketplace.atlassian.com/apps/3637796809?ref_product=copilot&ref_type=engagement&ref_style=text>) on the Atlassian Marketplace.
1. To the right of the app name, click {% octicon "kebab-horizontal" aria-label="Configure" %}, and enter your `SUBDOMAIN.ghe.com` in the text box. Replace SUBDOMAIN with your enterprise's subdomain of {% data variables.enterprise.data_residency_site %}.
1. Click **Save configuration**.
1. Click **Get it now**.
1. Select the Atlassian site you want to install the {% data variables.product.github %} application in.
1. Click **Review** to check the installation details, and then click **Get it now**.

   Once {% data variables.product.prodname_copilot %} for Jira is installed in your Jira site, you need to authorize the app to access your {% data variables.product.github %} organization and repositories.

1. Click **Configure** in the confirmation message in Jira after installation.
   * If you are not automatically redirected, find the {% data variables.product.prodname_copilot %} for Jira app in the list of apps available to your enterprise at `SUBDOMAIN.ghe.com/apps/external-app/github-copilot-for-jira`. Click **Install**.
1. If you are not already logged in to {% data variables.product.prodname_dotcom %}, click the highlighted **Log in to {% data variables.product.prodname_dotcom %}** and follow the prompts to log in to your {% data variables.product.github %} account and authorize the application.
   * If your organization or enterprise uses SSO, you may need to start an active SAML session for your organization and perform an additional authorization step.
1. Click **Install app** to give the app permission to access information on your GitHub account.
1. Choose the organization and repositories the app has access to. Your {% data variables.product.github %} organizations are enabled by default for your Jira workspace. Optionally, in the **Install {% data variables.product.prodname_copilot %} for Jira** page, _deselect_ the organization and repositories you _don't want the application to have access to_.
1. Click **Install**.
1. When installation is complete, you will see a list of connected organizations on the {% data variables.product.prodname_copilot %} for Jira app configuration page in Jira.

## Using the {% data variables.product.prodname_copilot %} app in Jira

The {% data variables.product.prodname_copilot_short %} app must be enabled for a {% data variables.product.github %} organization you are a member of, before you can start using it.

The first time you use {% data variables.copilot.copilot_cloud_agent %} in Jira, you will need to connect it to your {% data variables.product.github %} account.

Only users with **write** access to a repository can trigger {% data variables.copilot.copilot_cloud_agent %} to work in that repository.

You can trigger {% data variables.copilot.copilot_cloud_agent %} in several ways:

* **Assign** {% data variables.product.prodname_copilot %} to a work item using the Assignee field.
* **Mention** `@{% data variables.product.prodname_copilot %}` in a comment on a work item.
* **Use a Jira automation.** In your Jira automation rules, select the **Use {% data variables.product.prodname_copilot %}** action and configure your flow to use a custom trigger based on Jira events, such as when a work item is created or transitioned, or a label is applied. For more information, see [Work with AI agents in Jira](<https://support.atlassian.com/jira-software-cloud/docs/work-with-ai-agents-in-jira/>) in the Atlassian documentation.

> [!NOTE]
> When you assign {% data variables.product.prodname_copilot_short %} to a Jira work item, the context the agent captures from Jira will be added to the pull request and **visible to everyone** if the repository is public.

### Example: Triggering {% data variables.copilot.copilot_cloud_agent %} from a Jira work item

1. In Jira, open or create a work item that contains clear requirements you want to delegate to {% data variables.copilot.copilot_cloud_agent %}.
1. To specify a repository you want {% data variables.product.prodname_copilot_short %} to work in, mention it in the work item description or in a comment.
1. Assign `{% data variables.product.prodname_copilot %}` to the work item, or mention `@{% data variables.product.prodname_copilot %}` in a comment. For example:

    ```text
   @{% data variables.product.prodname_copilot %} create a new API endpoint for user authentication in octo-org/octorepo
    ```

1. If you have not previously connected the {% data variables.product.github %} application in Jira to your {% data variables.product.github %} account, follow the prompts to authorize the application for both {% data variables.product.github %} and Atlassian.
1. Once {% data variables.copilot.copilot_cloud_agent %} has started working on the pull request, a comment will appear in the chat panel in Jira. The user who initiated the agent session can view progress there.
1. You can follow up with further instructions for {% data variables.product.prodname_copilot_short %}:
    * Use the **Continue in Chat** button under the **Agents** heading to chat directly with {% data variables.product.prodname_copilot_short %} to have updates made to the _current_ pull request.
    * Mention `@{% data variables.product.prodname_copilot %}` in a comment on the work item to have updates made in a _new_ pull request.

> [!TIP]
> If you have not received confirmation of triggering {% data variables.copilot.copilot_cloud_agent %} after 1 minute, refresh the Jira work item page.

### Viewing agent activity in Jira

While {% data variables.copilot.copilot_cloud_agent_short %} works its activity streams live into the chat panel in Jira, so you can follow what the agent is doing without leaving your work item. The activity stream includes a link to the associated agent session on {% data variables.product.github %}.

### Directing {% data variables.product.prodname_copilot_short %} from Jira post-session

When {% data variables.copilot.copilot_cloud_agent %} has completed a session, for example when a pull request is ready for review, you can direct {% data variables.product.prodname_copilot_short %} to continue the work:

* In the chat panel in Jira, select the link to the associated agent session on {% data variables.product.github %}. This opens the agents panel on {% data variables.product.github %}, where you can review the session and send follow-up instructions to update the **existing** pull request.
* Add a follow-up `@{% data variables.product.prodname_copilot %}` mention or comment on the Jira work item. This starts a new session and opens a **new** pull request rather than updating the existing one.

## Customizing {% data variables.copilot.copilot_cloud_agent %} in Jira

You can customize how {% data variables.copilot.copilot_cloud_agent %} works in your Jira workspace by specifying models, agents, and custom instructions.

### Specifying a model

Specify a model when you want a task to run on a specific model rather than the default. For example, you may choose a lighter model for routine, well-scoped changes. To change the model used by {% data variables.copilot.copilot_cloud_agent %} for a particular task, include the model name in your instructions to {% data variables.product.prodname_copilot_short %}, see [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>).

### Specifying a custom agent

Specify a custom agent to tailor {% data variables.copilot.copilot_cloud_agent_short %}'s behavior to a particular workflow or repository. You can specify a custom agent from your {% data variables.product.github %} repository directly in the Jira ticket. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>).

### Using custom instructions

Use custom instructions to set defaults that apply to every session, such as the target repository, so {% data variables.product.prodname_copilot_short %} does not have to pause and ask you for input mid-session. You can define custom instructions at the Jira workspace level that apply every time {% data variables.copilot.copilot_cloud_agent %} is triggered.

## Usage costs

{% data variables.copilot.copilot_cloud_agent %} uses {% data variables.product.prodname_actions %} minutes and {% data variables.product.prodname_ai_credits_short %}.

For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/billing-and-usage/organizations-and-enterprises/billing>).

## Adding or removing an organization to the {% data variables.product.prodname_copilot %} for Jira app

A Jira administrator and {% data variables.product.github %} organization owner can enable or disable organizations for the integration.

> [!NOTE]
> If a new SSO-protected organization is added to the app after the initial installation, users will need to start an active SAML session for the organization in {% data variables.product.github %}, in order to trigger {% data variables.copilot.copilot_cloud_agent %} to work in the new organization's repositories from Jira. For more information, see [AUTOTITLE](<https://github.com/enterprise-cloud@latest/authentication/authenticating-with-single-sign-on/about-authentication-with-single-sign-on#about-oauth-apps-github-apps-and-sso>).

To change access for the {% data variables.product.prodname_copilot %} for Jira app for an organization:

1. In Jira, go to the settings page for your workspace.
1. Go to the applications setting page for the {% data variables.product.prodname_copilot %} app.
1. Optionally, click **Connect More {% data variables.product.github %} Organizations** to add new organizations to the list.
1. Enable or disable the {% data variables.product.prodname_copilot_short %} app for one or more of the listed organizations.

## Troubleshooting

If you run into problems, try the following solutions.

### You can't see the {% data variables.copilot.copilot_cloud_agent %} and it is not possible to assign it to a Jira work item

In Jira, check your Atlassian Administration settings for your organization are set as follows.

1. Jira is an AI-enabled app, see [Activate AI for apps](<https://support.atlassian.com/organization-administration/docs/activate-atlassian-intelligence-for-products>) in the Atlassian documentation.

### You can see the {% data variables.copilot.copilot_cloud_agent %} but it is not possible to assign it to a Jira work item

Check that you have connected your personal account on {% data variables.product.github %} to the {% data variables.product.prodname_copilot %} for Jira app.

1. In Jira, go to the settings page for your personal account.
1. Under your general settings, select the **{% data variables.product.prodname_copilot %} for Jira** app.
1. If you are not already signed in with {% data variables.product.github %}, follow the prompts to sign in and authorize the application.

### When chatting with {% data variables.product.prodname_copilot %}, you are prompted to sign in

To sign in to {% data variables.product.prodname_copilot %} for Jira app, follow the steps above in [You can see the {% data variables.copilot.copilot_cloud_agent %} but it is not possible to assign it to a Jira work item](<#you-can-see-the-copilot-cloud-agent-but-it-is-not-possible-to-assign-it-to-a-jira-work-item>).

### Other users in your workspace can assign {% data variables.copilot.copilot_cloud_agent %} to a Jira work item, but you cannot

If {% data variables.copilot.copilot_cloud_agent %} cannot see or work with your organization's resources in Jira and your organization uses SSO in {% data variables.product.github %}, you may need to reauthorize the {% data variables.product.prodname_copilot %} for Jira app for your {% data variables.product.github %} account. For more information, see [AUTOTITLE](<https://github.com/enterprise-cloud@latest/authentication/authenticating-with-single-sign-on/about-authentication-with-single-sign-on#about-oauth-apps-github-apps-and-sso>).

To resolve this issue, follow these steps to start a new active SSO session for your organization:

1. Go to your [organization settings](<https://github.com/settings/organizations>) in {% data variables.product.github %}.
1. Under "Single sign-on", find the organization you need to authenticate to and click **Sign out**, and then **Sign in**.
   * If your enterprise manages SSO for your organization, signing in to one organization in the enterprise works as an SSO session for all organizations in the enterprise.
1. Return to Jira, and refresh the page you are working in.
1. Try working with {% data variables.copilot.copilot_cloud_agent %} in Jira again.

### {% data variables.product.prodname_copilot %} is not responding

* Check {% data variables.product.github %}'s [Status page](<https://githubstatus.com>) for any active incidents.
* Check the [Atlassian status page](<https://status.atlassian.com>) for any active incidents.
* Verify that {% data variables.copilot.copilot_cloud_agent %} has access to the repository by testing if you can assign {% data variables.product.prodname_copilot_short %} to an issue on {% data variables.product.github %}.
* Verify that the {% data variables.product.prodname_copilot %} for Jira application has access to the repository. See [AUTOTITLE](<https://github.com/apps/using-github-apps/reviewing-and-modifying-installed-github-apps>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#example-atlassian>)
* [Collaborate on work items with AI agents](<https://support.atlassian.com/jira-software-cloud/docs/collaborate-on-work-items-with-ai-agents/>) in the Atlassian documentation


</details>
