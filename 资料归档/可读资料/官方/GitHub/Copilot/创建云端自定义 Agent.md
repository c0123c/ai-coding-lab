# 创建云端自定义 Agent

介绍在仓库中编写 Agent 配置、指定行为与工具，并在任务中调用；包含角色示例和支持条件。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-06-12](<https://github.com/github/docs/commit/21a6a142d04455daaba3ce419f3f5ee80c30ee82>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/创建云端自定义 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Creating custom agents for {% data variables.copilot.copilot_cloud_agent %}
shortTitle: Create custom agents
intro: You can create specialized agents with tailored expertise for specific development tasks.
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
category:
  - Configure Copilot
  - Author and optimize with Copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents
  - /copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.copilot.custom_agents_caps_short %} 允许您针对特定任务定制 {% data variables.product.prodname_copilot_short %} 的专业知识。有关 {% data variables.copilot.custom_agents_short %} 的概念概述，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>)。

{% data reusables.copilot.custom-agents-ide-preview %}

## 在 {% data variables.product.github %} 上的存储库中创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件

1. Go 到 [https://github.com/copilot/agents](https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text 的 agents 选项卡）。

1. 使用提示框中的下拉菜单，选择要在其中创建 {% data variables.copilot.copilot_custom_agent_short %} 配置文件的存储库。

   > [!NOTE]
   > 组织所有者可以在组织的 `.github` 或 `.github-private` 存储库中创建组织级 {% data variables.copilot.custom_agents_short %}，这些存储库可在其组织内的所有存储库中使用。企业所有者可以在企业设置中指定的组织的 `.github-private` 存储库中创建企业级 {% data variables.copilot.custom_agents_short %}，该存储库可在企业中的所有组织中使用。欲了解更多信息，请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>)和[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>)。

1. 或者，选择要在其中创建 {% data variables.copilot.agent_profile %} 的分支。默认为主分支。
1. 单击{% octicon "copilot" aria-label="Select a custom agent" %}，然后单击**{% octicon "plus" aria-label="Plus button" %} 创建一个agent**。这将在目标存储库的 `.github/agents` 目录中打开一个名为 `my-agent.agent.md` 的模板 agent 配置文件。
1. 如果您要创建组织或企业级 {% data variables.copilot.copilot_custom_agent_short %}，请删除文件路径的 `.github/` 部分，以将模板移动到根 `agents` 目录。
1. 编辑文件名（`.agent.md` 之前的文本），选择一个唯一的描述性名称来标识 agent 的用途。请注意，文件名只能包含以下字符：`.`、`-`、`_`、`a-z`、`A-Z`、`0-9`。
1. 配置【{% data variables.copilot.agent_profile %}】，包括名称、描述、工具和提示。有关 {% data variables.copilot.agent_profile %} 可以包含的内容的更多信息，请参阅 [配置 {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>)。
1. 将文件提交到存储库并将其合并到默认分支中。 Go 返回到 agents 选项卡并根据需要刷新页面。当您在提示框中单击{% octicon "copilot" aria-hidden="true" aria-label="copilot" %}时，您的{% data variables.copilot.copilot_custom_agent_short %}现在将出现在下拉列表中。

## 配置{% data variables.copilot.agent_profile %}

{% data reusables.copilot.custom-agents-configuring-profile %}

## 示例 {% data variables.copilot.agent_profiles %}

{% data reusables.copilot.custom-agents-example-profiles %}

## 使用{% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-using %}

## 后续步骤

{% data reusables.copilot.custom-agents-next-steps %}


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.copilot.custom_agents_caps_short %} allow you to tailor {% data variables.product.prodname_copilot_short %}'s expertise for specific tasks. For a conceptual overview of {% data variables.copilot.custom_agents_short %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/agents/cloud-agent/about-custom-agents>).

{% data reusables.copilot.custom-agents-ide-preview %}

## Creating a {% data variables.copilot.copilot_custom_agent_short %} profile in a repository on {% data variables.product.github %}

1. Go to the agents tab at [https://github.com/copilot/agents](<https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text>).

1. Using the dropdown menu in the prompt box, select the repository you want to create the {% data variables.copilot.copilot_custom_agent_short %} profile in.

   > [!NOTE]
   > Organization owners can create organization-level {% data variables.copilot.custom_agents_short %} in the organization's `.github` or `.github-private` repository that are available across all repositories within their organization. Enterprise owners can create enterprise-level {% data variables.copilot.custom_agents_short %} in the `.github-private` repository of an organization designated in enterprise settings, available across all organizations in the enterprise. For more information, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>) and [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>).

1. Optionally, select the branch you want to create the {% data variables.copilot.agent_profile %} in. The default is the main branch.
1. Click {% octicon "copilot" aria-label="Select a custom agent" %}, then click **{% octicon "plus" aria-label="Plus button" %} Create an agent**. This will open a template agent profile called `my-agent.agent.md` in the `.github/agents` directory of your target repository.
1. If you are creating an organization or enterprise-level {% data variables.copilot.copilot_custom_agent_short %}, delete the `.github/` portion of the file path to move your template to the root `agents` directory.
1. Edit the filename (the text before `.agent.md`), selecting a unique, descriptive name that identifies the agent's purpose. Note that the filename may only contain the following characters: `.`, `-`, `_`, `a-z`, `A-Z`, `0-9`.
1. Configure the {% data variables.copilot.agent_profile %}, including the name, description, tools, and prompts. For more information on what the {% data variables.copilot.agent_profile %} can include, see [Configuring an {% data variables.copilot.agent_profile %}](<#configuring-an-agent-profile>).
1. Commit the file to the repository and merge it into the default branch. Go back to the agents tab and refresh the page if needed. Your {% data variables.copilot.copilot_custom_agent_short %} will now appear in the dropdown when you click {% octicon "copilot" aria-hidden="true" aria-label="copilot" %} in the prompt box.

## Configuring an {% data variables.copilot.agent_profile %}

{% data reusables.copilot.custom-agents-configuring-profile %}

## Example {% data variables.copilot.agent_profiles %}

{% data reusables.copilot.custom-agents-example-profiles %}

## Using {% data variables.copilot.custom_agents_short %}

{% data reusables.copilot.custom-agents-using %}

## Next steps

{% data reusables.copilot.custom-agents-next-steps %}


</details>
