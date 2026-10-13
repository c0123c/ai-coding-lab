# 准备组织级自定义 Agent 仓库

说明组织如何准备集中保存 Agent 配置的仓库，以及权限和后续配置路径，便于成员共享开发角色。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-07-09](<https://github.com/github/docs/commit/f6563f9663db132ef0f3fe55519a6d7608b67d30>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/准备组织级自定义 Agent 仓库.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Preparing to use custom agents in your organization
intro: 'Configure the repository that stores {% data variables.copilot.custom_agents_short %} for your organization.'
permissions: Organization owners
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

{% data reusables.copilot.custom-agents-preview-note %}

## 先决条件

您应该了解 {% data variables.copilot.copilot_custom_agents %} 是什么以及它们如何工作。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>)。

> [!NOTE]
> 如果您的组织是企业的一部分，企业所有者可以配置限制 {% data variables.copilot.custom_agents_short %} 的规则集。请联系您的企业所有者，检查您是否可以创建和管理组织级别 {% data variables.copilot.custom_agents_short %}。

## 让您的组织为 {% data variables.copilot.custom_agents_short %} 做好准备

组织级别 {% data variables.copilot.custom_agents_short %} 存储在组织的 `.github` 或 `.github-private` 存储库的 `/agents` 目录中。两者的工作方式相同，并使组织的所有成员都可以使用 {% data variables.copilot.custom_agents_short %}，无论他们是否有权访问存储库本身。

有关 `.github` 和 `.github-private` 存储库的更多信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile>)。

如果您还没有合适的存储库，请按照以下步骤创建一个。这些步骤描述了创建 `.github-private` 存储库，但您也可以通过将其命名为 `.github` 来创建 `.github` 存储库。

1. 使用 [{% data variables.product.github %} 的模板存储库 ](<https://github.com/docs/custom-agents-template?ref_product=copilot&ref_type=engagement&ref_style=text>) 创建 {% data variables.copilot.copilot_custom_agent_short %} 存储库。该模板包含 README 和您需要的文件结构。
1. 在 **选择所有者** {% octicon "triangle-down" aria-hidden="true" aria-label="triangle-down" %} 下拉菜单中，选择您的组织。
1. 将存储库命名为 `.github-private` 并编写简短说明。
1. 在可见性下拉菜单中，单击 {% octicon "lock" aria-hidden="true" aria-label="lock" %} **私人**。
     * 组织成员仍然能够使用存储在该存储库中的 {% data variables.copilot.custom_agents_short %}，无论他们是否可以直接访问存储库本身。保持存储库私有还可以避免与需要 `.github-private` 私有的其他功能发生冲突，例如仅限组织成员的配置文件 README。
    * 如果您希望组织成员直接查看 {% data variables.copilot.agent_profiles %} 并进行协作，请考虑使用具有 {% octicon "organization" aria-hidden="true" aria-label="organization" %} **内部 ** 可见性的 `.github` 存储库。如果您是开源组织并希望公开共享 {% data variables.copilot.custom_agents_short %}，请选择 {% octicon "globe" aria-hidden="true" aria-label="globe" %} **公开 ** 可见性。

{% data reusables.repositories.create-repo %}
1. 更新模板README。包括 {% data variables.copilot.custom_agents_short %} 的任何创建指南或特定于您的组织的合规性注意事项。

## 后续步骤

要试用{% data variables.copilot.custom_agents_short %}，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.custom-agents-preview-note %}

## Prerequisites

You should understand what {% data variables.copilot.copilot_custom_agents %} are and how they work. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>).

> [!NOTE]
> If your organization is part of an enterprise, enterprise owners can configure a ruleset that restricts {% data variables.copilot.custom_agents_short %}. Contact your enterprise owners to check whether you can create and manage organization-level {% data variables.copilot.custom_agents_short %}.

## Preparing your organization for {% data variables.copilot.custom_agents_short %}

Organization-level {% data variables.copilot.custom_agents_short %} are stored in the `/agents` directory of your organization's `.github` or `.github-private` repository. Both work the same way and make the {% data variables.copilot.custom_agents_short %} available to all members of the organization, regardless of whether they have access to the repository itself.

For more information on `.github` and `.github-private` repositories, see [AUTOTITLE](<https://raw.githubusercontent.com/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile>).

If you don't already have a suitable repository, follow the steps below to create one. These steps describe creating a `.github-private` repository, but you can also create a `.github` repository by naming it `.github` instead.

1. Create your {% data variables.copilot.copilot_custom_agent_short %} repository using [{% data variables.product.github %}'s template repository](<https://github.com/docs/custom-agents-template?ref_product=copilot&ref_type=engagement&ref_style=text>). The template includes a README and the file structure you need.
1. In the **Choose an owner** {% octicon "triangle-down" aria-hidden="true" aria-label="triangle-down" %} dropdown menu, choose your organization.
1. Name the repository `.github-private` and write a brief description.
1. In the visibility dropdown menu, click {% octicon "lock" aria-hidden="true" aria-label="lock" %} **Private**.
     * Organization members will still be able to use {% data variables.copilot.custom_agents_short %} stored in this repository, regardless of whether they have direct access to the repository itself. Keeping the repository private also avoids conflicts with other features that require `.github-private` to be private, such as the organization member-only profile README.
    * If you want organization members to view and collaborate on the {% data variables.copilot.agent_profiles %} directly, consider using a `.github` repository with {% octicon "organization" aria-hidden="true" aria-label="organization" %} **Internal** visibility. If you are an open source organization and want to share the {% data variables.copilot.custom_agents_short %} publicly, select {% octicon "globe" aria-hidden="true" aria-label="globe" %} **Public** visibility.

{% data reusables.repositories.create-repo %}
1. Update the template README. Include any creation guidelines for {% data variables.copilot.custom_agents_short %} or compliance considerations specific to your organization.

## Next steps

To trial {% data variables.copilot.custom_agents_short %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents>).


</details>
