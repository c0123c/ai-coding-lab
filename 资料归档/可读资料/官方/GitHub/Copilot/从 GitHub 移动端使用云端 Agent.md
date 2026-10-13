# 从 GitHub 移动端使用云端 Agent

介绍启动会话、分派 Issue 和跟踪任务，帮助在移动端管理异步代码开发。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-05-28](<https://github.com/github/docs/commit/d84d12b6318444f8e9785399abf311d3cc954dda>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-mobile.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/从 GitHub 移动端使用云端 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using Copilot cloud agent on GitHub Mobile
shortTitle: Use cloud agent on GitHub Mobile
intro: 'Start and track {% data variables.copilot.copilot_cloud_agent %} sessions from the {% data variables.product.prodname_mobile %} app.'
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
allowTitleToDifferFromFilename: true
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 开始会话

1. 在{% data variables.product.prodname_mobile %}中，点击屏幕右下角的**{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}**图标，然后点击**{% octicon "agent" aria-label="The Agents icon" %}新建会话**。
1. 使用提示字段中的下拉菜单，选择您想要 {% data variables.product.prodname_copilot_short %} 使用的存储库。
1. 输入描述您的请求的提示。

例如：`Implement a user friendly message for common errors.`

如果您希望 {% data variables.product.prodname_copilot_short %} 打开拉取请求，您可以在提示中询问，例如 `Open a pull request to implement a user friendly message for common errors.`
1. （可选）为 {% data variables.product.prodname_copilot_short %} 的更改选择一个基础分支。 {% data variables.product.prodname_copilot_short %}将基于此分支创建一个新分支。
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. 单击提交按钮。

{% data variables.product.prodname_copilot_short %} 将启动一个新会话、处理任务并推送任何代码更改。

## 将问题分配给 {% data variables.product.prodname_copilot_short %}

1. 在 {% data variables.product.prodname_mobile %} 中，导航到包含要分配给 {% data variables.product.prodname_copilot_short %} 的问题的存储库。
1. 单击**问题**。
1. 打开您要分配给 {% data variables.product.prodname_copilot_short %} 的问题。
1. 点击**{% octicon "info" aria-label="information" %}**图标。
1. 在“受让人”旁边，点击**编辑**。
1. 单击“{% data variables.product.prodname_copilot_short %}”旁边的加号。
1. 单击**完成**。

## 跟踪您的会话

您可以在 {% data variables.product.prodname_mobile %} 中查看 agents 生成的正在运行和过去的拉取请求列表。

1. 在{% data variables.product.prodname_mobile %}主页的“Agents”部分中，点击**Agent任务**。

将显示您要求 {% data variables.product.prodname_copilot_short %} 生成的当前打开的拉取请求列表。

1. 使用列表顶部的按钮来过滤拉取请求列表。

例如，要仅显示合并的任务，请点击 ** 打开 **，然后在下拉列表中点击 ** 合并 **。

   

![状态下拉列表的屏幕截图，带有“打开”复选标记。](<https://github.com/assets/images/help/copilot/cloud-agent/mobile-status-dropdown.png>)


## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Starting a session

1. In {% data variables.product.prodname_mobile %}, tap the **{% octicon "copilot" aria-hidden="true" aria-label="Copilot" %}** icon in the bottom right corner of the screen, then tap **{% octicon "agent" aria-label="The Agents icon" %} New Session**.
1. Using the dropdown menu in the prompt field, select the repository you want {% data variables.product.prodname_copilot_short %} to work in.
1. Enter a prompt describing your request.

   For example: `Implement a user friendly message for common errors.`

   If you want {% data variables.product.prodname_copilot_short %} to open a pull request, you can ask in your prompt, for example `Open a pull request to implement a user friendly message for common errors.`
1. Optionally, select a base branch for {% data variables.product.prodname_copilot_short %}'s changes. {% data variables.product.prodname_copilot_short %} will create a new branch based on this branch.
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. Click the submit button.

    {% data variables.product.prodname_copilot_short %} will start a new session, work on the task, and push any code changes.

## Assigning an issue to {% data variables.product.prodname_copilot_short %}

1. In {% data variables.product.prodname_mobile %}, navigate to the repository that contains the issue you want to assign to {% data variables.product.prodname_copilot_short %}.
1. Click **Issues**.
1. Open the issue that you want to assign to {% data variables.product.prodname_copilot_short %}.
1. Tap the **{% octicon "info" aria-label="information" %}** icon.
1. Beside "Assignees", tap **Edit**.
1. Beside "{% data variables.product.prodname_copilot_short %}", click the plus sign.
1. Click **Done**.

## Tracking your sessions

You can see a list of your running and past pull requests generated by agents in {% data variables.product.prodname_mobile %}.

1. In the "Agents" section on the {% data variables.product.prodname_mobile %} Home page, tap **Agent Tasks**.

   A list of the currently open pull requests, that you asked {% data variables.product.prodname_copilot_short %} to generate, is displayed.

1. Use the buttons at the top of the list to filter the list of pull requests.

   For example, to show only merged tasks, tap **Open** and then, in the dropdown, tap **Merged**.

   

![Screenshot of the status dropdown list with a check mark against 'Open'.](<https://github.com/assets/images/help/copilot/cloud-agent/mobile-status-dropdown.png>)


## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)


</details>
