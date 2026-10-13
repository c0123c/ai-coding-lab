# 在企业中启用 Copilot 云端 Agent

说明企业管理员选择可用组织、启用 MCP 和合作方 Agent 的操作；属于企业部署管理资料。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在企业中启用 Copilot 云端 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Enabling GitHub Copilot cloud agent in your enterprise
intro: 'Choose which organizations can use {% data variables.copilot.copilot_cloud_agent %} and connect it to MCP servers.'
allowTitleToDifferFromFilename: true
permissions: Enterprise owners and AI managers
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
shortTitle: 'Enable {% data variables.copilot.copilot_cloud_agent %}'
redirect_from:
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/manage-copilot-coding-agent
  - /copilot/managing-copilot/managing-copilot-for-your-enterprise/adding-copilot-coding-agent-to-enterprise
  - /copilot/managing-copilot/managing-copilot-for-your-enterprise/making-copilot-coding-agent-available-to-enterprise
  - /copilot/managing-copilot/managing-copilot-for-your-enterprise/managing-copilot-coding-agent-in-your-enterprise
  - /copilot/how-tos/administer/enterprises/managing-copilot-coding-agent-in-your-enterprise
  - /copilot/how-tos/administer/enterprises/manage-copilot-coding-agent
  - /copilot/how-tos/administer/manage-for-enterprise/manage-copilot-coding-agent
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-copilot-coding-agent
  - /copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/manage-copilot-cloud-agent
contentType: how-tos
category:
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 先决条件

您可能希望在为企业启用 {% data variables.copilot.copilot_cloud_agent %} 之前运行试用。参见[AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/pilot-cloud-agent>)。

## 启用{% data variables.copilot.copilot_cloud_agent %}

默认情况下禁用 {% data variables.copilot.copilot_cloud_agent %} 和使用第三方 MCP 服务器。您可以为从您的企业或组织收到 {% data variables.product.prodname_copilot_short %} 许可证的用户启用这些功能。

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.cca-policies %}
1. 选择 {% data variables.copilot.copilot_cloud_agent %} 的全局策略，然后与您的组织沟通您的决定。

   >[!TIP] 如果您选择**为所选组织启用**，您可以在UI中选择单个组织。要根据自定义属性选择组织，请使用 REST API。请参阅[AUTOTITLE](<https://github.com/rest/copilot/copilot-coding-agent-management#selecting-organizations-with-custom-properties>)。

1. 默认情况下，agent 将在选定组织的所有存储库中可用。如果存在应对所有用户阻止 {% data variables.copilot.copilot_cloud_agent %} 的存储库，请告诉组织所有者配置此设置。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent#disabling-or-enabling-copilot-cloud-agent-in-your-repositories>)。

## 启用 MCP 服务器

{% data variables.copilot.copilot_cloud_agent %} 自动有权访问少量默认的 MCP 服务器。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent#default-mcp-servers>)。

您可以启用第三方 MCP 服务器，以允许开发人员将 {% data variables.copilot.copilot_cloud_agent %} 与 DevOps 工具链中的其他服务集成，例如错误跟踪平台或日志系统。

{% data reusables.enterprise-accounts.view-mcp-policies %}
1. 为{% data variables.product.prodname_copilot_short %}**中的**MCP服务器设置策略。

> [!NOTE] 的“MCP 登记处 URL”和“限制 MCP 访问注册服务器”策略 **不** 适用于 {% data variables.copilot.copilot_cloud_agent %}.

## 启用{% data variables.copilot.agent_apps %}和第三方agents

企业所有者可以为其组织启用第三方 agents，包括 {% data variables.copilot.agent_apps %}。在企业级别启用后，组织所有者可以选择允许哪个 agents。有关 {% data variables.copilot.agent_apps %} 的概述，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/agents/agent-apps>)。

要管理agent和企业策略，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Prerequisites

You may want to run a trial before enabling {% data variables.copilot.copilot_cloud_agent %} for the enterprise. See [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/pilot-cloud-agent>).

## Enabling {% data variables.copilot.copilot_cloud_agent %}

{% data variables.copilot.copilot_cloud_agent %} and use of third-party MCP servers are disabled by default. You can enable these features for users who receive a {% data variables.product.prodname_copilot_short %} license from your enterprise or organizations.

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
{% data reusables.enterprise-accounts.cca-policies %}
1. Select a global policy for {% data variables.copilot.copilot_cloud_agent %}, then communicate your decision with your organizations.

   >[!TIP] If you select **Enabled for selected organizations**, you can select individual organizations in the UI. To select organizations based on custom properties instead, use the REST API. See [AUTOTITLE](<https://github.com/rest/copilot/copilot-coding-agent-management#selecting-organizations-with-custom-properties>).

1. By default, the agent will be available in all repositories in selected organizations. If there are repositories where {% data variables.copilot.copilot_cloud_agent %} should be blocked for all users, tell organization owners to configure this setting. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent#disabling-or-enabling-copilot-cloud-agent-in-your-repositories>).

## Enabling MCP servers

{% data variables.copilot.copilot_cloud_agent %} automatically has access to a small number of default MCP servers. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent#default-mcp-servers>).

You can enable third-party MCP servers to allow developers to integrate {% data variables.copilot.copilot_cloud_agent %} with other services in your DevOps toolchain, such as error-tracking platforms or logging systems.

{% data reusables.enterprise-accounts.view-mcp-policies %}
1. Set a policy for **MCP servers in {% data variables.product.prodname_copilot_short %}**.

> [!NOTE] The "MCP Registry URL" and "Restrict MCP access to registry servers" policies do **not** apply to {% data variables.copilot.copilot_cloud_agent %}.

## Enabling {% data variables.copilot.agent_apps %} and third-party agents

Enterprise owners can enable third-party agents, including {% data variables.copilot.agent_apps %}, for their organizations. Once enabled at the enterprise level, organization owners can choose which agents to allow. For an overview of {% data variables.copilot.agent_apps %}, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/agent-apps>).

To manage agent and enterprise policies, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>).


</details>
