# 用 Copilot 应用管理 Issue 与 PR

说明从问题启动 Agent、生成 PR、阅读和回应审查以及合并的操作，将任务与代码交付连接在一个界面中。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-18](<https://github.com/github/docs/commit/cdb3465fb2ef81dda6947530592c59d502b8dba4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot 应用管理 Issue 与 PR.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Managing issues and pull requests with the GitHub Copilot app
shortTitle: Managing issues and pull requests
allowTitleToDifferFromFilename: true
intro: 'Pick up an issue, direct an agent to implement changes, and land a pull request—all without leaving the {% data variables.copilot.github_copilot_app %}.'
product: '{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/ai/github-app" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Download {% data variables.copilot.github_copilot_app %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/github-app/managing-issues-and-pull-requests
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 浏览您的问题和拉取请求

打开侧边栏中的 **[我的作品 ](<https://github.com/copilot/app/launch?open=ghapp%3A%2F%2Fmywork>)**，即可在一处查看您的问题和拉取请求。该视图按部分组织 - 默认情况下，**全部**、**活动**、**审阅请求**和**完成**。您可以编辑默认部分或使用您自己的过滤器添加新部分。使用任何部分中的搜索栏按关键字或限定符（例如 `label:bug`）查找项目。

## 从会话创建问题或拉取请求

当您要求应用程序中的agent创建问题时，agent会根据问题类型选择存储库问题模板。要使用特定模板，请在提示中指定它。

当您要求 agent 创建拉取请求时，它也会遵循存储库的拉取请求模板。

## 从问题开始会话

1. 在侧边栏打开**[我的作品](<https://github.com/copilot/app/launch?open=ghapp%3A%2F%2Fmywork>)**。
1. 浏览或筛选以查找问题，然后单击它以查看其详细信息。
1. 单击**新会话**。该应用程序创建一个新会话，其中已加载问题上下文。
1. 从提示字段下方的下拉列表中选择会话模式，例如，**计划**让agent首先提出计划，或者**交互式**与agent协作。
1. 提示 agent 你想要它做什么。如果您选择**计划**模式，agent会提出一个计划供您先查看；否则，agent 将开始解决该问题并提出您可以迭代的更改。在对话视图中进行操作并提供反馈以引导 agent。

## 审查拉取请求

1. 单击 ** 我的工作 ** 中的拉取请求可查看其概述 - 包括摘要、CI 检查结果和审核活动。
1. 切换到 ** 文件已更改 ** 选项卡以查看差异。
1. 单击 **新建会话** 启动拉取请求的会话。在会话中，您可以对差异留下评论意见，或要求 agent 进行更改。
1. 在会话中完成审核后，您可以返回到拉取请求详细信息视图，然后单击顶部的 **审核** 提交审核。

您还可以直接从应用程序在浏览器或另一个 IDE 中打开拉取请求。

## 回复评论

您可以回复审阅意见并解决 {% data variables.copilot.github_copilot_app %} 中失败的 CI 检查。

1. 打开拉取请求。
1. 向下滚动页面即可查看您的 PR 上的评论。要要求 agent 解决评论，请单击 **{% octicon "copilot" aria-label="The Copilot icon" %} 修复 **。
1. 在页面底部，查看 CI 支票的状态。要要求 agent 修复失败的检查，请单击 **{% octicon "copilot" aria-label="The Copilot icon" %} 修复失败的检查 **。

## 合并拉取请求

当您想要合并拉取请求时，您可以在应用程序顶部启用**agent合并**。 Agent 合并将提示工作区的 Copilot 会话读取您的拉取请求，修复阻止它的内容，并在 {% data variables.product.github %} 允许的情况下尽快合并它。它在后台运行，在应用程序重新启动后仍然存在，并在合并拉取请求后自行关闭。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Browsing your issues and pull requests

Open **[My work](<https://github.com/copilot/app/launch?open=ghapp%3A%2F%2Fmywork>)** in the sidebar to see your issues and pull requests in one place. The view is organized into sections—by default, **All**, **Active**, **Review requests**, and **Done**. You can edit the default sections or add new ones with your own filters. Use the search bar within any section to find items by keyword or qualifiers like `label:bug`.

## Creating an issue or pull request from a session

When you ask an agent in the app to create an issue, the agent chooses a repository issue template based on the type of issue. To use a particular template, specify it in your prompt.

When you ask an agent to create a pull request, it also follows the repository's pull request template.

## Starting a session from an issue

1. Open **[My work](<https://github.com/copilot/app/launch?open=ghapp%3A%2F%2Fmywork>)** in the sidebar.
1. Browse or filter to find an issue, then click it to view its details.
1. Click **New session**. The app creates a new session with the issue context already loaded.
1. Select a session mode from the dropdown below the prompt field—for example, **Plan** to have the agent propose a plan first, or **Interactive** to work collaboratively with the agent.
1. Prompt the agent with what you want it to do. If you chose **Plan** mode, the agent proposes a plan for you to review first; otherwise, the agent will start working on the issue and propose changes that you can iterate on. Follow along in the conversation view and provide feedback to steer the agent.

## Reviewing a pull request

1. Click a pull request in **My work** to see its overview—including the summary, CI check results, and review activity.
1. Switch to the **Files changed** tab to review the diff.
1. Click **New session** to start a session for the pull request. Within the session, you can leave review comments on the diff, or ask the agent to make changes.
1. Once done reviewing in the session, you can go back to the pull request detail view and click **Review** at the top to submit a review.

You can also open the pull request in your browser or in another IDE directly from the app.

## Responding to a review

You can respond to review comments and resolve failing CI checks in the {% data variables.copilot.github_copilot_app %}.

1. Open a pull request.
1. Scroll down the page to see review comments on your PR. To ask an agent to resolve a comment, click **{% octicon "copilot" aria-label="The Copilot icon" %} Fix**.
1. At the bottom of the page, view the status of CI checks. To ask an agent to fix failing checks, click **{% octicon "copilot" aria-label="The Copilot icon" %} Fix failing checks**.

## Merging a pull request

When you want to merge a pull request, you can enable **agent merge** at the top of the app. Agent merge will prompt the workspace's Copilot session to read your pull request, fix what is blocking it, and merge it as soon as {% data variables.product.github %} allows. It runs in the background, survives app restarts, and turns itself off once your pull request is merged.


</details>
