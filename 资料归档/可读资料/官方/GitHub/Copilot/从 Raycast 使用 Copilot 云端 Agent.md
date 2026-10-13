# 从 Raycast 使用 Copilot 云端 Agent

说明启动器中的任务启动、Issue 分派和会话跟踪，属于特定客户端接入指南。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-06-24](<https://github.com/github/docs/commit/e8640e4f74ba1e4357f99aa7c1939cb86d949a97>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-from-raycast.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/从 Raycast 使用 Copilot 云端 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using Copilot cloud agent from Raycast
shortTitle: Use cloud agent from Raycast
intro: 'Start and track {% data variables.copilot.copilot_cloud_agent %} sessions from the Raycast launcher.'
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.cloud-agent.raycast-intro %}

## 先决条件

{% data reusables.copilot.cloud-agent.raycast-setup %}

## 开始会话

1. 打开Raycast，搜索“{% data variables.product.prodname_copilot_short %}”，找到**创建任务**命令，然后按<kbd>输入</kbd>。
1. 单击**使用{% data variables.product.github %}**登录，然后完成身份验证流程。 Raycast 将重新开放。
1. 键入一个提示，描述您想要 {% data variables.product.prodname_copilot_short %} 执行的操作。

例如，`Implement a user friendly message for common errors.`
1. 选择您想要 {% data variables.product.prodname_copilot_short %} 使用的存储库。
1. （可选）为 {% data variables.product.prodname_copilot_short %} 的拉取请求选择一个基本分支。 {% data variables.product.prodname_copilot_short %} 将基于此分支创建一个新分支，然后将更改推送到针对该分支的拉取请求。
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. 按<kbd>命令</kbd>+<kbd>输入</kbd>（macOS）或<kbd>Ctrl</kbd>+<kbd>输入</kbd> (Windows) 启动任务。

{% data variables.product.prodname_copilot_short %}将开始一个新的会话。 {% data variables.product.prodname_copilot_short %} 将处理该任务并将更改推送到其拉取请求，然后在完成后将您添加为审阅者，从而触发通知。

{% data reusables.copilot.cloud-agent.raycast-oauth-access-restrictions %}

## 从 Raycast 分配问题

1. 打开 Raycast，搜索“{% data variables.product.prodname_copilot_short %}”，找到 **将问题分配给 {% data variables.product.prodname_copilot_short %}** 命令，然后按 <kbd>Enter</kbd>。
1. 单击**使用{% data variables.product.github %}**登录，然后完成身份验证流程。 Raycast 将重新开放。
1. 选择您想要 {% data variables.product.prodname_copilot_short %} 使用的存储库。
1. 选择您要分配给 {% data variables.product.prodname_copilot_short %} 的问题。
1. （可选）为 {% data variables.product.prodname_copilot_short %} 的拉取请求选择一个基本分支。 {% data variables.product.prodname_copilot_short %} 将基于此分支创建一个新分支，然后将更改推送到针对该分支的拉取请求。
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. （可选）提供附加说明。这些将与您的问题内容一起传递到 {% data variables.product.prodname_copilot_short %}。
1. 按<kbd>命令</kbd>+<kbd>输入</kbd>（macOS）或<kbd>Ctrl</kbd>+<kbd>输入</kbd> (Windows) 分配问题。

{% data variables.product.prodname_copilot_short %}将开始一个新的会话。 {% data variables.product.prodname_copilot_short %} 将处理该任务并将更改推送到其拉取请求，然后在完成后将您添加为审阅者，从而触发通知。

{% data reusables.copilot.cloud-agent.raycast-oauth-access-restrictions %}

## 跟踪您的会话

1. 打开Raycast，搜索“{% data variables.product.prodname_copilot_short %}”，找到**查看任务**命令，然后按<kbd>输入</kbd>。
1. 单击**使用{% data variables.product.github %}**登录，然后完成身份验证流程。 Raycast 将重新开放。
1. 您将看到您的任务列表。选择一个任务，然后使用以下键盘快捷键：
   * 要实时观看会话日志，请按 <kbd> 输入 </kbd>。日志实时更新，因此您无需离开 Raycast 即可监控 {% data variables.product.prodname_copilot_short %} 的进度。
   * 要在浏览器中打开会话日志，请按 <kbd>Command</kbd>+<kbd>Enter</kbd> (macOS) 或<kbd>Ctrl</kbd>+<kbd>输入</kbd> (Windows)。
   * 要打开链接的拉取请求，请按 <kbd>Command</kbd>+<kbd>P</kbd> (macOS) 或<kbd>Ctrl</kbd>+<kbd>P</kbd> (Windows)。

> [!NOTE]
> 如果您无法在 Raycast 中查看某些任务，则拥有存储库的组织可能启用了 {% data variables.product.prodname_oauth_app %} 访问限制。要了解如何请求“{% data variables.product.prodname_copilot %} for Raycast”{% data variables.product.prodname_oauth_app %} 的批准，请参阅 [AUTOTITLE](<https://github.com/account-and-profile/how-tos/organization-membership/requesting-organization-approval-for-oauth-apps>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.cloud-agent.raycast-intro %}

## Prerequisites

{% data reusables.copilot.cloud-agent.raycast-setup %}

## Starting a session

1. Open Raycast, search for "{% data variables.product.prodname_copilot_short %}," find the **Create Task** command, then press <kbd>Enter</kbd>.
1. Click **Sign in with {% data variables.product.github %}**, then complete the authentication flow. Raycast will re-open.
1. Type a prompt describing what you want {% data variables.product.prodname_copilot_short %} to do.

    For example, `Implement a user friendly message for common errors.`
1. Select the repository you want {% data variables.product.prodname_copilot_short %} to work in.
1. Optionally, select a base branch for {% data variables.product.prodname_copilot_short %}'s pull request. {% data variables.product.prodname_copilot_short %} will create a new branch based on this branch, then push the changes to a pull request targeting that branch.
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. Press <kbd>Command</kbd>+<kbd>Enter</kbd> (macOS) or <kbd>Ctrl</kbd>+<kbd>Enter</kbd> (Windows) to start the task.

    {% data variables.product.prodname_copilot_short %} will start a new session. {% data variables.product.prodname_copilot_short %} will work on the task and push changes to its pull request, then add you as a reviewer when it has finished, triggering a notification.

{% data reusables.copilot.cloud-agent.raycast-oauth-access-restrictions %}

## Assigning an issue from Raycast

1. Open Raycast, search for "{% data variables.product.prodname_copilot_short %}," find the **Assign Issues to {% data variables.product.prodname_copilot_short %}** command, then press <kbd>Enter</kbd>.
1. Click **Sign in with {% data variables.product.github %}**, then complete the authentication flow. Raycast will re-open.
1. Select the repository you want {% data variables.product.prodname_copilot_short %} to work in.
1. Select the issue you want to assign to {% data variables.product.prodname_copilot_short %}.
1. Optionally, select a base branch for {% data variables.product.prodname_copilot_short %}'s pull request. {% data variables.product.prodname_copilot_short %} will create a new branch based on this branch, then push the changes to a pull request targeting that branch.
{% data reusables.copilot.optional-select-custom-agent-generic %}
{% data reusables.copilot.optional-select-copilot-cloud-agent-model %}
1. Optionally, provide additional instructions. These will be passed to {% data variables.product.prodname_copilot_short %} alongside your issue contents.
1. Press <kbd>Command</kbd>+<kbd>Enter</kbd> (macOS) or <kbd>Ctrl</kbd>+<kbd>Enter</kbd> (Windows) to assign the issue.

    {% data variables.product.prodname_copilot_short %} will start a new session. {% data variables.product.prodname_copilot_short %} will work on the task and push changes to its pull request, then add you as a reviewer when it has finished, triggering a notification.

{% data reusables.copilot.cloud-agent.raycast-oauth-access-restrictions %}

## Tracking your sessions

1. Open Raycast, search for "{% data variables.product.prodname_copilot_short %}," find the **View Tasks** command, then press <kbd>Enter</kbd>.
1. Click **Sign in with {% data variables.product.github %}**, then complete the authentication flow. Raycast will re-open.
1. You'll see a list of your tasks. Select a task, then use the following keyboard shortcuts:
   * To watch the session logs live, press <kbd>Enter</kbd>. The logs update in real time, so you can monitor {% data variables.product.prodname_copilot_short %}'s progress without leaving Raycast.
   * To open the session logs in the browser, press <kbd>Command</kbd>+<kbd>Enter</kbd> (macOS) or <kbd>Ctrl</kbd>+<kbd>Enter</kbd> (Windows).
   * To open the linked pull request, press <kbd>Command</kbd>+<kbd>P</kbd> (macOS) or <kbd>Ctrl</kbd>+<kbd>P</kbd> (Windows).

> [!NOTE]
> If you are unable to see some tasks in Raycast, the organization that owns the repository may have enabled {% data variables.product.prodname_oauth_app %} access restrictions. To learn how to request approval for the "{% data variables.product.prodname_copilot %} for Raycast" {% data variables.product.prodname_oauth_app %}, see [AUTOTITLE](<https://github.com/account-and-profile/how-tos/organization-membership/requesting-organization-approval-for-oauth-apps>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)


</details>
