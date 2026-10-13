# 准备企业自定义 Agent 配置

讲解治理仓库、Agent 文件保护规则和管理权限安排，帮助组织部署并维护团队共享角色。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/df9bd944684a61fc90f8cfda11cf031984998e04>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/准备企业自定义 Agent 配置.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Preparing to use custom agents in your enterprise
intro: 'Set up your enterprise for {% data variables.copilot.custom_agents_short %} by configuring their source organization and repository, availability, and management permissions.'
permissions: Enterprise owners
versions:
  feature: copilot
shortTitle: Prepare for custom agents
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

企业级 {% data variables.copilot.custom_agents_short %} 在您企业组织内的 `.github-private` 存储库中定义。准备您的企业包括创建该存储库、将其配置为您的治理源、保护您的 agent 文件以及决定谁可以管理 {% data variables.copilot.custom_agents_short %}。

通过以下步骤来建立您的企业。

## 设置您的治理存储库

1. **创建一个 `.github-private` 存储库 ** 来容纳您企业的 {% data variables.copilot.agent_profiles %}、客户端权限和插件设置。
1. **选择存储库作为您的治理源**，以便您的企业从存储库中读取其设置。

对于这两个步骤，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/create-github-private-repo>)。

## 使用规则集保护您的 agent 文件

要自动配置仅允许企业所有者在整个企业中编辑 {% data variables.copilot.agent_profiles %} 的规则集：

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. 在“Agents”选项卡上的“使用规则集保护 agent 文件”部分中，单击“**创建规则集**”。

> [!NOTE]
> * 对 {% data variables.copilot.copilot_custom_agent_short %} 存储库具有写入权限的企业成员仍然可以创建拉取请求，建议对您的 {% data variables.copilot.agent_profiles %} 进行更改。然后，具有规则集旁路访问权限的企业成员可以根据需要合并这些拉取请求。
> * 创建此规则集还将阻止企业中的组织所有者创建或编辑组织级别 {% data variables.copilot.custom_agents_short %}。为了防止出现这种情况，您可以编辑规则集以仅定位包含企业级 {% data variables.copilot.custom_agents_short %} 的组织。

## 决定由谁管理您的自定义 agents

为了减轻您的管理负担并增强中小企业的能力，您可以通过创建一个 AI 经理团队来委托您企业中 {% data variables.copilot.custom_agents_short %} 的创建和管理。请参阅[AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/govern-at-scale/establish-ai-managers>)。

如果您希望保持对企业工具的完全控制以确保安全性和合规性，您可以自行创建和管理 {% data variables.copilot.custom_agents_short %}。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents>)。

## 后续步骤

要集中控制整个企业中的 {% data variables.product.prodname_copilot_short %} 客户端行为，请配置企业托管设置。参见[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

Enterprise-level {% data variables.copilot.custom_agents_short %} are defined in a `.github-private` repository within an organization in your enterprise. Preparing your enterprise involves creating that repository, configuring it as your source of governance, protecting your agent files, and deciding who can manage {% data variables.copilot.custom_agents_short %}.

Work through the following steps to set up your enterprise.

## Set up your governance repository

1. **Create a `.github-private` repository** to house your enterprise's {% data variables.copilot.agent_profiles %}, client permissions, and plugin settings.
1. **Select the repository as your source of governance** so that your enterprise reads its settings from the repository.

For both steps, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/create-github-private-repo>).

## Protecting your agent files using rulesets

To automatically configure a ruleset that allows only enterprise owners to edit {% data variables.copilot.agent_profiles %} across your enterprise:

{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.ai-controls-tab %}
1. On the "Agents" tab, in the "Protect agent files using rulesets" section, click **Create ruleset**.

> [!NOTE]
> * Members of your enterprise with write access to the {% data variables.copilot.copilot_custom_agent_short %} repository can still create pull requests proposing changes to your {% data variables.copilot.agent_profiles %}. Enterprise members with bypass access to the ruleset can then merge those pull requests as they see fit.
> * Creating this ruleset will also block organization owners in your enterprise from creating or editing organization-level {% data variables.copilot.custom_agents_short %}. To prevent this, you can edit the ruleset to target only the organization containing your enterprise-level {% data variables.copilot.custom_agents_short %}.

## Decide who manages your custom agents

To reduce your administrative burden and empower your SMEs, you can delegate the creation and management of {% data variables.copilot.custom_agents_short %} in your enterprise by creating a team of AI managers. See [AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/govern-at-scale/establish-ai-managers>).

If you prefer to maintain full control over your enterprise's tooling to ensure security and compliance, you can create and manage {% data variables.copilot.custom_agents_short %} yourself. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents>).

## Next steps

To centrally control {% data variables.product.prodname_copilot_short %} client behavior across your enterprise, configure enterprise managed settings. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>).


</details>
