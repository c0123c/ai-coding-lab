# 从 Azure Boards 启动 Copilot 任务

说明安装 GitHub 集成、批准权限并从工作项生成拉取请求，将需求管理与云端编码任务连接起来。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-integrations/integrate-cloud-agent-with-azure-boards.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/从 Azure Boards 启动 Copilot 任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating Copilot cloud agent with Azure Boards
shortTitle: Integrate cloud agent with Azure Boards
allowTitleToDifferFromFilename: true
intro: 'Use the {% data variables.product.prodname_copilot_short %} integration in Azure Boards to send work items directly to {% data variables.copilot.copilot_cloud_agent %} and generate pull requests, all from within your Azure DevOps workspace.'
product: '{% data reusables.copilot.plans.permission-paid-plans-cfi %}'
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
redirect_from:
  - /copilot/how-tos/use-copilot-agents/cloud-agent/integrate-cloud-agent-with-azure-boards
  - /copilot/how-tos/use-copilot-agents/coding-agent/integrate-coding-agent-with-azure-boards
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Azure Boards {% data variables.product.github %} 集成允许您在不离开工作区的情况下调用 {% data variables.copilot.copilot_cloud_agent %}。在 Azure Boards 工作项中，您可以使用工作项描述和注释的上下文启动 {% data variables.copilot.copilot_cloud_agent_short %} 会话并打开拉取请求。

有关其他 {% data variables.product.prodname_copilot_short %} 集成的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>)。

> [!NOTE]
> * {% data variables.product.prodname_copilot %} 使用 AI。检查是否有错误。请参阅[AUTOTITLE](<https://github.com/copilot/responsible-use/agents>)。
> * 当您将工作项发送到 {% data variables.copilot.copilot_cloud_agent %} 时，agent 将从文本字段捕获内容（例如描述和复制步骤）以及最后的 50 注释。此上下文存储在拉取请求中，并且对有权访问存储库的任何人都可见。

## 先决条件

* 您必须拥有一个 {% data variables.product.github %} 帐户，可以通过付费 {% data variables.product.prodname_copilot_short %} 计划访问 {% data variables.product.prodname_copilot_short %}。
* 连接到 Azure DevOps 项目的存储库必须启用 {% data variables.copilot.copilot_cloud_agent %}。

## 在 {% data variables.product.github %} 上安装 Azure Boards 应用程序

> [!NOTE]
> 要安装 Azure Boards 应用程序，您必须是 {% data variables.product.github %} 上的组织或企业的所有者或应用程序管理员。

Azure Boards 应用程序只需在组织中安装一次。安装应用程序后，组织的任何成员都可以将其 {% data variables.product.github %} 帐户连接到该应用程序并开始使用它。

1. Go 到 [Azure Boards 安装页面 ](<https://github.com/marketplace/azure-boards>)。
1. 滚动到页面底部，然后使用 **帐户** 下拉菜单选择您想要安装应用程序的帐户。
1. 单击**安装**。
1. 选择您希望 Azure Boards 应用程序有权访问的存储库。
1. 按照屏幕上的提示在 Azure DevOps 组织和项目中配置和授权应用程序。

## 批准 Azure Boards 应用程序权限

如果您已在 {% data variables.product.github %} 上安装了 Azure Boards 应用程序，则需要批准所需的权限更改，以允许应用程序与 {% data variables.product.prodname_copilot %} 进行通信。

1. 导航到 [您已安装的 {% data variables.product.github %} 应用程序 ](<https://github.com/settings/installations>)。
1. 找到 Azure Boards 应用程序，然后单击 ** 审核请求 ** 链接。
1. 检查权限，然后单击**接受新权限**。

## 从工作项创建拉取请求

1. 在 Azure Boards 中，打开要发送到 {% data variables.copilot.copilot_cloud_agent %} 的工作项目。
1. 单击工作项上的 {% octicon "copilot" aria-label="Copilot" %} 图标。
1. 选择 ** 使用 {% data variables.product.prodname_copilot_short %}** 创建拉取请求。
1. 在 **{% data variables.product.github %} 存储库 ** 下，选择 {% data variables.product.prodname_copilot_short %} 应在其中创建拉取请求的存储库。
1. （可选）更改 {% data variables.product.prodname_copilot_short %} 应用于拉取请求的基本分支。
1. （可选）添加任何其他说明以向 {% data variables.product.prodname_copilot_short %} 提供更多上下文。
1. 单击**创建**。

{% data variables.copilot.copilot_cloud_agent %} 将开始处理工作项并创建链接回该工作项的草稿拉取请求。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


## 原文对照

<details>
<summary>展开完整原文</summary>

The Azure Boards {% data variables.product.github %} integration allows you to invoke {% data variables.copilot.copilot_cloud_agent %} without leaving your workspace. From within a Azure Boards work item you can initiate {% data variables.copilot.copilot_cloud_agent_short %} sessions and open pull requests, using the context of your work item description and comments.

For information about additional {% data variables.product.prodname_copilot_short %} integrations, see [AUTOTITLE](<https://github.com/copilot/concepts/tools/about-copilot-integrations>).

> [!NOTE]
> * {% data variables.product.prodname_copilot %} uses AI. Check for mistakes. See [AUTOTITLE](<https://github.com/copilot/responsible-use/agents>).
> * When you send a work item to {% data variables.copilot.copilot_cloud_agent %}, the agent will capture content from text fields (such as the description and reproduction steps), along with the last 50 comments. This context is stored in the pull request, and is visible to anyone with access to the repository.

## Prerequisites

* You must have a {% data variables.product.github %} account with access to {% data variables.product.prodname_copilot_short %} through a paid {% data variables.product.prodname_copilot_short %} plan.
* The repositories connected to the Azure DevOps project must have {% data variables.copilot.copilot_cloud_agent %} enabled.

## Installing the Azure Boards application on {% data variables.product.github %}

> [!NOTE]
> To install the Azure Boards application, you must be an owner or App manager of the organization or enterprise on {% data variables.product.github %}.

The Azure Boards app only needs to be installed once in an organization. After the app is installed, any member of the organization can connect their {% data variables.product.github %} account to the app and start using it.

1. Go to the [Azure Boards installation page](<https://github.com/marketplace/azure-boards>).
1. Scroll to the bottom of the page, then use the **Account** dropdown menu to select an account you would like to install the app in.
1. Click **Install**.
1. Select the repositories you would like the Azure Boards app to have access to.
1. Follow the prompts on screen to configure and authorize the app in your Azure DevOps organization and project.

## Approving the Azure Boards application permissions

If you already have the Azure Boards application installed on {% data variables.product.github %}, you will need to approve the required permission changes to allow the app to communicate with {% data variables.product.prodname_copilot %}.

1. Navigate to [your installed {% data variables.product.github %} Apps](<https://github.com/settings/installations>).
1. Find the Azure Boards application, then click the **Review request** link.
1. Review the permissions, then click **Accept new permission**.

## Creating a pull request from a work item

1. In Azure Boards, open the work item you want to send to {% data variables.copilot.copilot_cloud_agent %}.
1. Click the {% octicon "copilot" aria-label="Copilot" %} icon on the work item.
1. Select **Create a pull request with {% data variables.product.prodname_copilot_short %}**.
1. Under **{% data variables.product.github %} repository**, select the repository where {% data variables.product.prodname_copilot_short %} should create the pull request.
1. Optionally, change the base branch that {% data variables.product.prodname_copilot_short %} should use for the pull request.
1. Optionally, add any additional instructions to provide {% data variables.product.prodname_copilot_short %} with more context.
1. Click **Create**.

{% data variables.copilot.copilot_cloud_agent %} will begin processing the work item and create a draft pull request linked back to the work item.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/cloud-agent-access>)


</details>
