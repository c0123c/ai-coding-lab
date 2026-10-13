# 测试自定义 Agent

介绍通过真实任务和响应结果验证 Agent 配置，检查提示、工具与角色行为是否符合预期，并据此调整。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-07-09](<https://github.com/github/docs/commit/3af7cb3042ac2f16645785e9f6dc89922d5e5588>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/测试自定义 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Testing and releasing {% data variables.copilot.custom_agents_short %} in your organization or enterprise
intro: 'Ensure your {% data variables.copilot.custom_agents_short %} are performant and compliant before releasing them to your company.'
versions:
  feature: copilot
shortTitle: Test custom agents
contentType: how-tos
category:
  - Configure Copilot
  - Manage Copilot for a team
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/test-custom-agents
  - /copilot/how-tos/use-copilot-agents/cloud-agent/test-custom-agents
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.custom-agents-preview-note %}

## 简介

在您的组织或企业中发布或更新 {% data variables.copilot.copilot_custom_agent_short %} 之前，您可以私下测试 agent 以确保其符合您的标准。一旦您满意，您就可以轻松更改 {% data variables.copilot.agent_profile %} 的位置，以使其在整个公司内可用。

## 先决条件

在测试 {% data variables.copilot.copilot_custom_agent_short %} 之前，您需要为 {% data variables.copilot.custom_agents_short %} 设置您的组织或企业。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>)或[AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>)。

## 1。创建您的测试 {% data variables.copilot.copilot_custom_agent_short %}

1. 在您的组织或企业的 `.github-private` 存储库中，创建一个名为 `.github/agents` 的新目录。此目录中存储的 Agents 仅可供有权访问 `.github-private` 存储库的组织或企业成员使用，并且只能在他们在该存储库中启动任务时使用。
1. 在您的 `.github/agents` 目录中，为您的测试 agent 创建 {% data variables.copilot.agent_profile %}。您可以创建一个全新的配置文件或复制现有的配置文件来测试潜在的更新。有关配置 {% data variables.copilot.agent_profile %} 的信息，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents#configuring-an-agent-profile>)。
1. 将您的测试 {% data variables.copilot.agent_profile %} 合并到存储库的默认分支中。

## 2。测试你的{% data variables.copilot.copilot_custom_agent_short %}

1. Go 到 [https://github.com/copilot/agents](https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text 的 agents 选项卡）。
1. 使用提示框中的下拉菜单，选择您的 `.github-private` 存储库。
1. 选择 {% octicon "copilot" aria-label="Select a custom agent" %}，然后单击您的测试 agent。
1. 要测试您的 {% data variables.copilot.copilot_custom_agent_short %}，请向 {% data variables.product.prodname_copilot_short %} 发送提示。
1. 在“最近的会话”部分中，单击您的会话以查看有关结果的详细信息。
1. Continue 根据需要进行更改并测试您的 {% data variables.copilot.copilot_custom_agent_short %}，直到您对其性能感到满意为止。

## 3。释放你的{% data variables.copilot.copilot_custom_agent_short %}

1. 在您的 `.github-private` 存储库中，将 {% data variables.copilot.agent_profile %} 从 `.github/agents` 目录移动到 `agents` 目录中。
1. 将您的更改合并到默认分支中。您的 {% data variables.copilot.copilot_custom_agent_short %} 现在可供您的组织或企业中的所有用户使用。

## 后续步骤

要监控组织中 {% data variables.copilot.custom_agents_short %} 的使用情况，请按 `actor:Copilot` 过滤组织的审核日志。请参阅[AUTOTITLE](<https://raw.githubusercontent.com/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization>)。

要监控 {% data variables.copilot.custom_agents_short %} 在您的企业中的使用情况，请参阅 [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.custom-agents-preview-note %}

## Introduction

Before you release or update a {% data variables.copilot.copilot_custom_agent_short %} in your organization or enterprise, you can test the agent privately to ensure it meets your standards. Once you are satisfied, you can then easily change the location of your {% data variables.copilot.agent_profile %} to make it available across your company.

## Prerequisites

Before you can test a {% data variables.copilot.copilot_custom_agent_short %}, you need to set up your organization or enterprise for {% data variables.copilot.custom_agents_short %}. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents>) or [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents>).

## 1. Create your test {% data variables.copilot.copilot_custom_agent_short %}

1. In your organization or enterprise's `.github-private` repository, create a new directory called `.github/agents`. Agents stored in this directory are only available to members of your organization or enterprise who have access to the `.github-private` repository, and can only be used when they start a task within that repository.
1. In your `.github/agents` directory, create the {% data variables.copilot.agent_profile %} for your test agent. You can create a net-new profile or duplicate an existing profile to test potential updates. For information on configuring an {% data variables.copilot.agent_profile %}, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents#configuring-an-agent-profile>).
1. Merge your test {% data variables.copilot.agent_profile %} into the default branch of your repository.

## 2. Test your {% data variables.copilot.copilot_custom_agent_short %}

1. Go to the agents tab at [https://github.com/copilot/agents](<https://github.com/copilot/agents?ref_product=copilot&ref_type=engagement&ref_style=text>).
1. Using the dropdown menu in the prompt box, select your `.github-private` repository.
1. Select {% octicon "copilot" aria-label="Select a custom agent" %}, then click your test agent.
1. To test your {% data variables.copilot.copilot_custom_agent_short %}, send {% data variables.product.prodname_copilot_short %} a prompt.
1. In the "Recent sessions" section, click your session to see detailed information about your results.
1. Continue making changes and testing your {% data variables.copilot.copilot_custom_agent_short %} as needed until you are satisfied with its performance.

## 3. Release your {% data variables.copilot.copilot_custom_agent_short %}

1. In your `.github-private` repository, move your {% data variables.copilot.agent_profile %} from the `.github/agents` directory into the `agents` directory.
1. Merge your changes into the default branch. Your {% data variables.copilot.copilot_custom_agent_short %} is now available to all users in your organization or enterprise.

## Next steps

To monitor the usage of {% data variables.copilot.custom_agents_short %} in your organization, filter your organization's audit log by `actor:Copilot`. See [AUTOTITLE](<https://raw.githubusercontent.com/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization>).

To monitor the usage of {% data variables.copilot.custom_agents_short %} in your enterprise, see [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/monitor-agentic-activity>).


</details>
