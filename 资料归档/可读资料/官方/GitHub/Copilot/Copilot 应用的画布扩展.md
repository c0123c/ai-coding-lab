# Copilot 应用的画布扩展

介绍共享交互产物的用途、发现与创建、目录结构和协作操作；属于应用扩展开发参考。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-25](<https://github.com/github/docs/commit/7d75fdb54f2037075e5aaaa14e52ddb8324c69e9>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/github-copilot-app/working-with-canvas-extensions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 应用的画布扩展.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Working with canvas extensions in the GitHub Copilot app
shortTitle: Canvas extensions
intro: 'Use canvases in the {% data variables.copilot.github_copilot_app %} to build shared, agent-driven artifacts and interfaces for human-agent collaboration.'
allowTitleToDifferFromFilename: true
product: '{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/ai/github-app" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Download {% data variables.copilot.github_copilot_app %}</span> {% octicon "link-external" height:16 %}</a>'
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

## 关于画布扩展

画布扩展是工作工件的共享交互式表面，例如计划、分类板、浏览器会话、发布清单、仪表板、事件或电子表格。

画布扩展可以与其他功能一起包含在插件中，例如技能或 MCP 服务器。

虽然聊天对于定义意图和讨论任务很有用，但大多数工作都是在终端、浏览器、文档或仪表板等工作界面中进行的。画布允许您直接在这些工作界面中与 agent 进行协作。

画布是双向的：agent 可以在画布工作时更新画布，并且您可以在同一表面上进行编辑。当您创建画布时，agent 会根据您的提示和工作流程生成功能。在迭代时，您可以要求它添加、删除或修改这些功能。创建后，画布将在应用程序的右侧面板中打开。

例如，您可以创建一个代理看板画布，人们可以在其中使用 UI 控件来创建或移动卡片，并要求 agent 添加 agent 可调用功能，例如 `get_board`、`add_card` 和 `move_card`。这让人们和 agents 在一个共享板上协调工作。

## 为什么要使用画布

当任务或工作流程需要结构、迭代和验证，而聊天还不够时，画布会很有帮助。您可以使用聊天来指导 agent 并通过歧义进行推理，然后使用画布直接检查、引导和验证可见工作，而无需仅依赖提示。

画布提供两种价值：它们通过为双方提供共享的工作表面来改善人与agent的合作关系，并且它们允许您自定义该表面以匹配您的工作流程。

当您需要执行以下操作时，画布非常有用：

* 地面 agent 在适合您的工作流程的实际工件或环境中工作。
* 直接在共享表面上引导或纠正工作，然后让 agent 继续进行这些更改。
* 检查进度和结果作为共享工件的可见更改，而不仅仅是聊天响应。
* 在轮流、会话和交接之间保持工作连续性。

## 示例用例

可以自定义画布扩展，以满足您或您的团队在各种场景中的需求。例如：

* **代理看板：**让人类和agents添加卡片、移动工作并启动任务。
* **问题分类板：**总结存储库的首要问题、重复出现的主题和用户痛点。
* **Markdown 画布：** 构建一个持久的 Markdown 文件，用于规划您的一天、确定问题和拉取请求的优先级、启动和监控 agent 会话，以及将相关工作保留在一个可编辑的界面中。
* **文档画布：** 直接在应用程序中打开、编辑文档、电子表格、幻灯片和其他工件并进行协作。

## 探索画布

**Canvas** 视图提供精选画布的列表。有些画布是通过插件提供的，您必须安装这些插件才能在会话中使用画布。例如，安装 Azure DevOps 插件使您可以访问画布来规划和管理 Azure DevOps 工作。

1. 在应用程序侧栏中，单击**自定义**。
1. 单击**画布**。
1. 浏览特色画布。
1. 如果画布需要插件，请点击**安装插件**。
1. 单击**新建会话**以打开与画布的会话。

您还可以单击**已安装**来查看所有已安装的自定义项，包括可供您使用的画布。

## 创建画布

您可以使用 `/create-canvas` 技能在会话中创建新画布。

1. 打开或启动 agent 会话。
1. 在提示框中输入 `/create-canvas`，然后描述您希望画布支持的工作流程和功能，包括人们应该能够做什么以及 agent 应该能够做什么。
例如，您可以要求 agent：

   * `Create an agentic kanban canvas with actions to create, assign, and move cards.`
   * `Create a markdown canvas that combines my meetings with prioritized issues and pull requests, then lets me launch and track agent sessions from that canvas.`
1. 您可以选择画布是否应与您的团队共享或保留为个人的：
   * **项目范围：** `.github/extensions` 用于提交到存储库的团队共享画布。
   * **用户范围：** `~/.copilot/extensions` 用于您机器上的个人画布。

agent 将构建画布并在完成后在右侧面板中将其打开。之后，您可以通过要求 agent 更改接口、共享状态和功能来继续迭代。

## 画布扩展的结构如何

您在应用程序中创建或直接安装的画布扩展位于 `.github/extensions`（项目范围）或 `~/.copilot/extensions`（用户范围）下的自己的目录中。

尽管实现可能有所不同，但画布扩展通常包括：

* 用于扩展元数据和依赖项的 `package.json` 文件。
* 定义画布行为和功能的扩展条目文件，例如 `extension.mjs`。
* 用于持久画布数据和状态的可选 JSON 工件（例如，`artifacts` 目录下的文件）。

当画布在应用程序侧面板中打开时，人和 agents 通过 UI 操作和 agent 可调用功能与相同的共享状态进行交互。

## 在画布上工作

打开画布后，您可以使用 agent 快速迭代。

* 添加或修改画布的功能。
* 使用画布控件（例如按钮、卡片或过滤器）直接更新表面。
* 要求 agent 调用画布公开的功能来更新数据或采取操作。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/agent-sessions>).
* [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/customize-github-copilot-app>).


## 原文对照

<details>
<summary>展开完整原文</summary>

## About canvas extensions

A canvas extension is a shared, interactive surface for a work artifact, such as a plan, triage board, browser session, release checklist, dashboard, incident, or spreadsheet.

A canvas extension can be included in a plugin alongside other capabilities, such as skills or MCP servers.

While chat is useful for defining intent and discussing tasks, most work happens in work surfaces such as a terminal, a browser, a document, or a dashboard. Canvases allow you to collaborate with the agent directly in those work surfaces.

Canvases are bidirectional: the agent can update the canvas while it works, and you can edit on that same surface. When you create a canvas, the agent generates capabilities based on your prompt and workflow. As you iterate, you can ask it to add, remove, or revise those capabilities. Once created, canvases open in the app's right side panel.

For example, you can create an agentic kanban canvas where people use UI controls to create or move cards, and ask the agent to add agent-callable capabilities such as `get_board`, `add_card`, and `move_card`. This lets people and agents coordinate work from one shared board.

## Why use a canvas

Canvases are helpful when a task or workflow needs structure, iteration, and verification, and a chat is not enough. You can use chat to instruct the agent and reason through ambiguity, then use the canvas to inspect, steer, and verify visible work directly without needing to rely on prompts only.

Canvases provide two kinds of value: they improve the human-agent partnership by giving both sides a shared surface to work in, and they let you customize that surface to match your workflow.

Canvases are useful when you need to:

* Ground agent work in an actual artifact or environment that fits your workflow.
* Steer or correct work directly on the shared surface, then let the agent continue from those changes.
* Inspect progress and outcomes as visible changes to a shared artifact, not just chat responses.
* Keep work continuous across turns, sessions, and handoffs.

## Example use cases

Canvas extensions can be customized to fit your needs or your team's needs across a range of scenarios. For example:

* **Agentic kanban boards:** Let humans and agents add cards, move work, and kick off tasks.
* **Issue triage boards:** Summarize top issues, recurring themes, and user pain points for a repository.
* **Markdown canvases:** Build a persistent markdown file for planning your day, prioritizing issues and pull requests, launching and monitoring agent sessions, and keeping related work in one editable surface.
* **Document canvases:** Open, edit, and collaborate on documents, spreadsheets, slide decks, and other artifacts directly in the app.

## Discovering canvases

The **Canvas** view provides a curated list of featured canvases. Some canvases are delivered through plugins, which you must install before you can use the canvas in a session. For example, installing the Azure DevOps plugin gives you access to a canvas for planning and managing Azure DevOps work.

1. In the app sidebar, click **Customize**.
1. Click **Canvas**.
1. Browse the featured canvases.
1. If a canvas requires a plugin, click **Install plugin**.
1. Click **New session** to open a session with the canvas.

You can also click **Installed** to view all installed customizations, including the canvases available for you to use.

## Creating a canvas

You can create a new canvas from within a session using the `/create-canvas` skill.

1. Open or start an agent session.
1. In the prompt box, type `/create-canvas`, then describe the workflow and capabilities that you want the canvas to support, including what people should be able to do and what the agent should be able to do.
   For example, you can ask the agent to:

   * `Create an agentic kanban canvas with actions to create, assign, and move cards.`
   * `Create a markdown canvas that combines my meetings with prioritized issues and pull requests, then lets me launch and track agent sessions from that canvas.`
1. You can choose whether the canvas should be shared with your team or kept personal:
   * **Project scope:** `.github/extensions` for team-shared canvases committed to the repository.
   * **User scope:** `~/.copilot/extensions` for personal canvases on your machine.

The agent will build the canvas and open it in the right side panel once complete. After that, you can continue iterating by asking the agent to change the interface, shared state, and capabilities.

## How canvas extensions are structured

Canvas extensions that you create in the app or install directly live in their own directory under either `.github/extensions` (project scope) or `~/.copilot/extensions` (user scope). 

Although implementations can vary, a canvas extension commonly includes:

* A `package.json` file for extension metadata and dependencies.
* An extension entry file, such as `extension.mjs`, that defines the canvas behavior and capabilities.
* Optional JSON artifacts (for example, files under an `artifacts` directory) for persisted canvas data and state.

When a canvas opens in the app side panel, both people and agents interact with that same shared state through UI actions and agent-callable capabilities.

## Working in a canvas

Once a canvas is open, you can iterate quickly with the agent.

* Add or revise the capabilities of the canvas.
* Use canvas controls (such as buttons, cards, or filters) to update the surface directly.
* Ask the agent to call capabilities exposed by the canvas to update data or take actions.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/agent-sessions>).
* [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/customize-github-copilot-app>).


</details>
