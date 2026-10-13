# 为组织启用 Copilot 云端 Agent

说明管理员如何开放成员访问并控制可用仓库，用于组织层面的编码 Agent 部署与访问管理。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/为组织启用 Copilot 云端 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Adding GitHub Copilot cloud agent to your organization
intro: 'Enable {% data variables.copilot.copilot_cloud_agent %} for your members and control the repositories where it is available.'
allowTitleToDifferFromFilename: true
permissions: Organization owners
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/github-copilot/purchase?ref_product=copilot&ref_type=trial&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span>{% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
shortTitle: 'Add {% data variables.copilot.copilot_cloud_agent %}'
redirect_from:
  - /copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-coding-agent
  - /copilot/managing-copilot/managing-github-copilot-in-your-organization/adding-copilot-coding-agent-to-organization
  - /copilot/how-tos/administer/organizations/adding-copilot-coding-agent-to-organization
  - /copilot/how-tos/administer/organizations/add-copilot-coding-agent
  - /copilot/how-tos/administer/manage-for-organization/add-copilot-coding-agent
contentType: how-tos
category: 
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

> [！NOTE]
> 有关{% data variables.copilot.copilot_cloud_agent %}的介绍，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)。

## 为您的会员启用 {% data variables.copilot.copilot_cloud_agent %}

> [!NOTE]
> {% data reusables.organizations.copilot-policy-ent-overrides-org %}

默认情况下，对于您的组织分配了 {% data variables.copilot.copilot_enterprise %} 或 {% data variables.copilot.copilot_business_short %} 许可证的组织成员，{% data variables.copilot.copilot_cloud_agent %} 和第三方 MCP 服务器的使用被禁用。

具有 {% data variables.copilot.copilot_enterprise_short %} 或 {% data variables.copilot.copilot_business_short %} 的组织可以在其组织的 {% data variables.product.prodname_copilot_short %} 策略页面上为成员启用这些功能。请参阅 [在您的组织中启用 {% data variables.product.prodname_copilot_short %} 功能 ](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies#enabling-copilot-features-and-models-in-your-organization>)。

* 对于“{% data variables.copilot.copilot_cloud_agent %}”策略，选择“启用”。
* 对于“{% data variables.product.prodname_dotcom_the_website %} 上的MCP 服务器”策略，选择“启用”。

## 在您的存储库中禁用或启用 {% data variables.copilot.copilot_cloud_agent %}

默认情况下，{% data variables.copilot.copilot_cloud_agent %} 在所有存储库中可供有权访问 agent 的用户使用，但您可以阻止它在您的组织拥有的部分或所有存储库中使用。您可以使用以下说明来管理存储库可用性，或使用 [REST API](<https://github.com/rest/copilot/copilot-cloud-agent-management>) 以编程方式管理存储库可用性。

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. 使用“存储库访问”控件来定义哪些存储库允许 {% data variables.copilot.copilot_cloud_agent %}。
1. 如果选择“选定的存储库”，则在“选择存储库”对话框中，选择允许{% data variables.copilot.copilot_cloud_agent %}的存储库，然后单击**选择**。

一旦为存储库启用了 {% data variables.copilot.copilot_cloud_agent %}，任何有权访问 {% data variables.copilot.copilot_cloud_agent %} 并具有存储库写入权限的用户都可以将工作委派给 {% data variables.product.prodname_copilot_short %}。

## 控制存储库中的 {% data variables.copilot.copilot_automations %}

{% data variables.copilot.copilot_automations_cap %} 允许用户按计划或响应事件自动运行 {% data variables.copilot.copilot_cloud_agent %}。请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-automations>)。

默认情况下，在 {% data variables.copilot.copilot_cloud_agent %} 可用的存储库中允许 {% data variables.copilot.copilot_automations_cap %}。您可以独立于 {% data variables.copilot.copilot_cloud_agent %} 策略来控制您的组织拥有的存储库中是否允许 {% data variables.copilot.copilot_automations %}。

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. 要允许或阻止 {% data variables.copilot.copilot_automations %}，请切换 ** 允许自动化 ** 设置。

## 管理您组织的 agent 防火墙

组织所有者可以为其组织配置 {% data variables.copilot.copilot_cloud_agent %} 防火墙，包括是否为组织启用防火墙以及 agent 可以访问哪些外部主机和 URL。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)。

## 后续步骤

* 告诉 {% data variables.copilot.copilot_cloud_agent %} 可用的存储库成员，他们可以将工作委托给 {% data variables.copilot.copilot_cloud_agent_short %}。
* 配置组织中 {% data variables.copilot.copilot_cloud_agent %} 的默认运行程序类型。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/configure-runner-for-coding-agent>)。
* 鼓励成员自我教育如何设置存储库，以充分利用 {% data variables.copilot.copilot_cloud_agent %}。有用的资源：

   * [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)
   * [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
   * [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/risks-and-mitigations>)


## 原文对照

<details>
<summary>展开完整原文</summary>

> [!NOTE]
> For an introduction to {% data variables.copilot.copilot_cloud_agent %}, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>).

## Enabling {% data variables.copilot.copilot_cloud_agent %} for your members

> [!NOTE]
> {% data reusables.organizations.copilot-policy-ent-overrides-org %}

{% data variables.copilot.copilot_cloud_agent %} and use of third-party MCP servers are disabled by default for organization members assigned a {% data variables.copilot.copilot_enterprise %} or {% data variables.copilot.copilot_business_short %} license by your organization.

Organizations with {% data variables.copilot.copilot_enterprise_short %} or {% data variables.copilot.copilot_business_short %} can enable these features for members on the {% data variables.product.prodname_copilot_short %} policies page for their organization. See [Enabling {% data variables.product.prodname_copilot_short %} features in your organization](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies#enabling-copilot-features-and-models-in-your-organization>).

* For the "{% data variables.copilot.copilot_cloud_agent %}" policy, select "Enabled".
* For the "MCP servers on {% data variables.product.prodname_dotcom_the_website %}" policy, select "Enabled".

## Disabling or enabling {% data variables.copilot.copilot_cloud_agent %} in your repositories

By default, {% data variables.copilot.copilot_cloud_agent %} is available in all repositories for users who have access to the agent, but you can block it from being used in some or all repositories owned by your organization. You can manage repository availability using the following instructions, or programmatically using the [REST API](<https://github.com/rest/copilot/copilot-cloud-agent-management>).

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. Use the "Repository access" control to define which repositories allow {% data variables.copilot.copilot_cloud_agent %}.
1. If you choose "Selected repositories", in the "Select repositories" dialog, select the repositories that allow {% data variables.copilot.copilot_cloud_agent %}, then click **Select**.

Once {% data variables.copilot.copilot_cloud_agent %} is enabled for a repository, any user with access to {% data variables.copilot.copilot_cloud_agent %} and write permission for the repository can delegate work to {% data variables.product.prodname_copilot_short %}.

## Controlling {% data variables.copilot.copilot_automations %} in your repositories

{% data variables.copilot.copilot_automations_cap %} let users run {% data variables.copilot.copilot_cloud_agent %} automatically, on a schedule or in response to events. See [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/about-automations>).

{% data variables.copilot.copilot_automations_cap %} are allowed by default in repositories where {% data variables.copilot.copilot_cloud_agent %} is available. You can control whether {% data variables.copilot.copilot_automations %} are allowed in repositories owned by your organization, separately from the {% data variables.copilot.copilot_cloud_agent %} policy.

{% data reusables.profile.access_org %}
{% data reusables.profile.org_settings %}
{% data reusables.copilot.cloud-agent-settings %}
1. To allow or block {% data variables.copilot.copilot_automations %}, toggle the **Allow automations** setting.

## Managing the agent firewall for your organization

Organization owners can configure the {% data variables.copilot.copilot_cloud_agent %} firewall for their organization, including whether it is enabled for the organization and which external hosts and URLs the agent can access. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>).

## Next steps

* Tell the members of repositories where {% data variables.copilot.copilot_cloud_agent %} is available that they can delegate work to the {% data variables.copilot.copilot_cloud_agent_short %}.
* Configure the default runner type for {% data variables.copilot.copilot_cloud_agent %} in your organization. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-organization/configure-runner-for-coding-agent>).
* Encourage members to educate themselves about setting up their repository to get the most from {% data variables.copilot.copilot_cloud_agent %}. Useful resources:

   * [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)
   * [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
   * [AUTOTITLE](<https://github.com/copilot/concepts/agents/cloud-agent/risks-and-mitigations>)


</details>
