# 在 Copilot 应用中管理 Agent 会话

讲解各自拥有分支的隔离会话、模式与模型选择、聊天管理、沙箱、审查工具、语音和快捷键。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/github/docs/commit/03b432e6d532db63f40312771e3ba8212d1995c0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/github-copilot-app/agent-sessions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Copilot 应用中管理 Agent 会话.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Working with agent sessions in the GitHub Copilot app
shortTitle: Agent sessions
intro: 'Run multiple isolated agent sessions simultaneously, each with its own branch, and steer them using different session modes, models, and tools.'
allowTitleToDifferFromFilename: true
product: '{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/ai/github-app" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Download {% data variables.copilot.github_copilot_app %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/github-app/agent-sessions
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 开始会话

{% data variables.copilot.github_copilot_app %} 中的每个会话都在其自己的隔离工作区中运行，因此您可以并行运行多个会话并在多个任务上取得进展而不会发生冲突。

1. 在侧边栏中，单击 **项目 ** 旁边的 {% octicon "plus" aria-label="Start a new session" %}。
1. 选择一个项目 - 您可以使用计算机上已有的文件夹，从 {% data variables.product.github %} 中选择，或者从 Git URL 克隆，以用于托管在 {% data variables.product.github %} 外部的存储库（例如，在 Azure DevOps 上）或没有应用程序访问权限的私有存储库。
1. 从提示框下的下拉列表中，选择会话应运行的位置：在新的工作树中、在本地存储库中或在云沙箱中。云沙箱是由 {% data variables.product.github %} 托管的完全隔离的环境。
1. 从提示字段下方的下拉列表中选择会话模式、模型和推理工作。如果您为模型选择 **自动**，应用程序会根据任务复杂性为您的任务选择最佳模型。
1. 在提示字段中描述任务。您可以使用`#`引用问题，使用`@`添加文件，或使用`/`作为命令。

agent开始工作。您的活动会话显示在按存储库分组的侧栏中 - 单击任何会话即可切换到该会话。

有关可以在提示框中使用的命令列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/github-copilot-app-reference/slash-commands>)。

## 选择会话模式

会话模式控制 agent 拥有多少自主权。您可以从提示字段下方的下拉列表中设置模式并随时更改。

* **互动**：你和agent一起工作。 agent 建议进行更改并等待您的输入，然后再继续。
* **计划**：agent首先创建一个计划。您在 agent 执行该计划之前审查并批准该计划。
* **Autopilot**：agent完全自主工作——编写代码、运行测试和迭代，无需等待输入。

## 选择型号

您可以从提示字段下方的下拉列表中选择模型和推理工作。较高的推理能力使 agent 有更多的时间来思考复杂的问题，但可能需要更长时间。您可以在会话期间随时更改这两个设置。

如果您在模型选择器中选择**自动**，应用程序会根据任务复杂性自动为您的任务选择最佳模型。会话开始后，**Auto** 旁边的选择器显示哪个模型处理每个响应。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/models/auto-model-selection>)。

如果您在应用程序中配置了自己的模型提供程序，这些模型也会显示在选择器中。有关设置步骤，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/use-byok-models>)。

将鼠标悬停在模型选择器中的 {% octicon "info" aria-label="the info icon" %} 图标上可查看模型详细信息。

## 使用聊天功能

侧边栏中的 **Chats** 部分可打开对话模式，而无需创建专用分支或工作树。在开始会议之前，用它来进行头脑风暴、提出问题或探索想法。您的聊天记录将被保存并按对话名称列出。

要存档聊天而不丢失其历史记录，请右键单击侧边栏中的聊天以打开其菜单，然后单击**存档聊天**。

## 管理会话和聊天

使用**管理会话**查看、搜索和过滤会话和聊天，包括存档的聊天。您可以选择多个项目进行存档或删除，并恢复之前存档的聊天。该视图还显示工作文件和聊天历史记录所使用的磁盘空间的单独的可排序列。

1. 打开应用程序设置，然后选择**会话**。
1. 单击**管理会话**。
1. 搜索或过滤以查找您要管理的会话或聊天。
1. 选择一个或多个会话或聊天，然后将其存档或删除。要恢复存档的聊天，请找到该聊天，然后将其恢复。

## 使用云和本地沙箱

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

云沙箱和本地沙箱以不同的方式保护会话：

* **云沙箱**：在{% data variables.product.github %}托管的隔离环境中运行整个会话。当您想要使会话远离计算机或并行运行多个资源密集型任务时，请使用云沙箱。启动会话时选择云沙箱执行位置。
* **本地沙箱**：在您的计算机上运行会话，但限制agent运行工具可用的文件系统、网络和凭据访问。当 agent 需要本地工具或开发服务，但您希望限制其对计算机其余部分的访问时，请使用本地沙箱。在项目设置中配置新本地存储库和工作树会话的默认值，或使用 `/sandbox on` 或 `/sandbox off` 更改活动本地会话。

本地沙盒不适用于云沙盒会话或在远程主机上运行的会话。有关这两种类型的沙箱如何工作的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)。要启用本地沙箱，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/cloud-and-local-sandboxes/using-local-sandboxing>)。

## 在应用会话中使用 `/security-review`

> [!NOTE]
> `/security-review` 斜线命令当前位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。

您可以在活动的 agent 会话中运行 `/security-review` 斜杠命令来检查当前工作流更改是否存在高可信度漏洞。该命令返回带有严重性和置信度分数的优先结果，以及您可以在同一会话中应用和验证的建议修复。

1. 打开一个正在进行代码更改的活动会话。
1. 在提示框中输入`/security-review`，然后按<kbd>输入</kbd>。
1. 查看结果，然后根据需要更新代码更改。

这种轻量级的按需审查是对 {% data variables.product.github %}、{% data variables.product.prodname_code_scanning %}、{% data variables.product.prodname_dependabot %} 和 {% data variables.product.prodname_secret_scanning %} 的补充，可在打开拉取请求之前对本地更改进行初步检查。

## 使用橡皮鸭 agent

橡皮鸭 agent 是一个内置的 agent，充当建设性批评者，审查您当前的计划、实施或测试并返回具体反馈。 agent 运行的模型与驱动当前会话的模型不同。

当橡皮鸭启用时，{% data variables.product.prodname_copilot_short %}在工作时可以在关键点自动查阅它。主会议 agent 将工作传递给橡皮鸭 agent，接收批评，然后决定如何应用该反馈，然后再继续。

您也可以手动询问{% data variables.product.prodname_copilot_short %}以获得橡皮鸭agent的评论。

1. 打开一个活动会话。
1. 在提示框中，输入 `/rubber-duck` 并要求对您当前的计划、实施或测试进行批评。

有关橡皮鸭agent的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/rubber-duck>)。

## 在应用会话中使用 `/chronicle`

由于 {% data variables.copilot.github_copilot_app %} 是基于 {% data variables.copilot.copilot_cli %} 构建的，因此您可以使用 {% data variables.copilot.copilot_cli_short %} 会话历史记录功能（例如 `/chronicle`）从您在应用程序和其他 {% data variables.copilot.copilot_cli_short %} 会话中所做的工作中获取见解。

例如，您可以使用`/chronicle standup`来总结最近的工作。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)。

## 使用语音听写

您可以使用语音听写在会话中说出提示，而不是键入提示。

在使用语音听写之前，您需要在应用程序设置中进行配置。

1. 打开应用程序设置，然后选择**语音听写**选项卡。
1. 选择键盘快捷键。
1. 在操作系统设置中允许访问麦克风。
1. 下载本地转录模型。

设置后，使用快捷方式开始和停止语音转录。该应用程序会将转录的文本插入提示框中，以便您可以在发送之前查看或编辑它。

## 键盘快捷键

{% data variables.copilot.github_copilot_app %} 支持键盘快捷键，以帮助您导航会话、在工作区之间切换以及执行常见操作。要查看可用的快捷键，请打开应用程序，转至 ** 帮助 **，然后转至 ** 键盘快捷键 **。

您还可以使用 Markdown 文件、第三方工具或脚本的深层链接直接在应用程序中启动会话。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/open-with-deep-links>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

## Starting a session

Each session in the {% data variables.copilot.github_copilot_app %} runs in its own isolated workspace, so you can run multiple sessions in parallel and make progress on several tasks without conflicts.

1. In the sidebar, next to **Projects**, click {% octicon "plus" aria-label="Start a new session" %}.
1. Choose a project—you can use a folder already on your machine, choose from {% data variables.product.github %}, or clone from a Git URL for repositories hosted outside {% data variables.product.github %} (for example, on Azure DevOps) or for private repositories without app access.
1. From the dropdown under the prompt box, choose where the session should run: in a new working tree, in your local repository, or in a cloud sandbox. Cloud sandboxes are fully isolated environments hosted by {% data variables.product.github %}.
1. Select a session mode, model, and reasoning effort from the dropdowns below the prompt field. If you choose **Auto** for the model, the app selects the optimal model for your task based on task complexity.
1. Describe the task in the prompt field. You can reference issues with `#`, add files with `@`, or use `/` for commands.

The agent starts working. Your active sessions appear in the sidebar grouped by repository—click any session to switch to it.

For a list of commands you can use in the prompt box, see [AUTOTITLE](<https://github.com/copilot/reference/github-copilot-app-reference/slash-commands>).

## Choosing a session mode

The session mode controls how much autonomy the agent has. You can set the mode from the dropdown below the prompt field and change it at any time.

* **Interactive**: You and the agent work together. The agent suggests changes and waits for your input before proceeding.
* **Plan**: The agent creates a plan first. You review and approve the plan before the agent executes it.
* **Autopilot**: The agent works fully autonomously—writing code, running tests, and iterating without waiting for input.

## Choosing a model

You can select a model and reasoning effort from the dropdowns below the prompt field. Higher reasoning effort gives the agent more time to think through complex problems but may take longer. You can change both settings at any time during a session.

If you choose **Auto** in the model picker, the app automatically selects the optimal model for your task based on task complexity. After the session starts, the picker next to **Auto** shows which model handled each response. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/models/auto-model-selection>).

If you configured your own model provider in the app, those models also appear in the picker. For setup steps, see [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/use-byok-models>).

Hover over the {% octicon "info" aria-label="the info icon" %} icon in the model picker to see model details.

## Using chats

The **Chats** section in the sidebar opens a conversation mode without creating a dedicated branch or worktree. Use it for brainstorming, asking questions, or exploring ideas before starting a session. Your chat history is saved and listed by conversation name.

To archive a chat without losing its history, right-click the chat in the sidebar to open its menu, then click **Archive chat**.

## Managing sessions and chats

Use **Manage sessions** to view, search, and filter sessions and chats, including archived chats. You can select multiple items to archive or delete, and restore chats that you previously archived. The view also shows separate, sortable columns for the disk space used by working files and chat history.

1. Open the app settings, then select **Sessions**.
1. Click **Manage sessions**.
1. Search or filter to find the sessions or chats you want to manage.
1. Select one or more sessions or chats, then archive or delete them. To restore an archived chat, find the chat, then restore it.

## Using cloud and local sandboxes

> [!NOTE]
> {% data reusables.cli.public-preview-cloud-sandbox %}

Cloud and local sandboxes protect sessions in different ways:

* **Cloud sandbox**: Runs the entire session in an isolated environment hosted by {% data variables.product.github %}. Use a cloud sandbox when you want to keep the session off your machine or run several resource-intensive tasks in parallel. Choose the cloud sandbox execution location when starting a session.
* **Local sandbox**: Runs the session on your machine, but restricts the filesystem, network, and credential access available to agent-run tools. Use local sandboxing when the agent needs your local tools or development services, but you want to limit its access to the rest of your machine. Configure the default for new local repository and working tree sessions in the project settings, or use `/sandbox on` or `/sandbox off` to change an active local session.

Local sandboxing does not apply to cloud sandbox sessions or sessions that run on a remote host. For more information about how both types of sandbox work, see [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>). To enable local sandboxing, see [AUTOTITLE](<https://github.com/copilot/how-tos/cloud-and-local-sandboxes/using-local-sandboxing>).

## Using `/security-review` in app sessions

> [!NOTE]
> The `/security-review` slash command is currently in {% data variables.release-phases.public_preview %} and subject to change.

You can run the `/security-review` slash command in an active agent session to review your current workstream changes for high-confidence vulnerabilities. The command returns prioritized findings with severity and confidence scores, plus suggested fixes you can apply and verify in the same session.

1. Open an active session that has in-progress code changes.
1. In the prompt box, type `/security-review` and press <kbd>Enter</kbd>.
1. Review the findings, then update your code changes as needed.

This lightweight, on-demand review complements {% data variables.product.github %} {% data variables.product.prodname_code_scanning %}, {% data variables.product.prodname_dependabot %}, and {% data variables.product.prodname_secret_scanning %} by giving you an initial check on your local changes before opening a pull request.

## Using the rubber duck agent

The rubber duck agent is a built-in agent that acts as a constructive critic, reviewing your current plan, implementation, or tests and returning concrete feedback. The agent runs on a different model from the one driving your current session.

When rubber duck is enabled, {% data variables.product.prodname_copilot_short %} can consult it automatically at key points while it works. The main session agent passes work to the rubber duck agent, receives the critique, then decides how to apply that feedback before continuing.

You can also manually ask {% data variables.product.prodname_copilot_short %} to get a review from the rubber duck agent.

1. Open an active session.
1. In the prompt box, type `/rubber-duck` and ask for a critique of your current plan, implementation, or tests.

For more information on the rubber duck agent, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/rubber-duck>).

## Using `/chronicle` with app sessions

Because the {% data variables.copilot.github_copilot_app %} is built on {% data variables.copilot.copilot_cli %}, you can use {% data variables.copilot.copilot_cli_short %} session history features such as `/chronicle` to get insights from work you did in the app and in other {% data variables.copilot.copilot_cli_short %} sessions.

For example, you can use `/chronicle standup` to summarize recent work. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>).

## Using voice dictation

You can use voice dictation to speak prompts in a session instead of typing them.

Before you can use voice dictation, you need to configure it in the app settings.

1. Open the app settings, then select the **Voice dictation** tab.
1. Choose a keyboard shortcut.
1. Allow microphone access in your operating system settings.
1. Download a local transcription model.

After setup, use your shortcut to start and stop voice transcription. The app inserts transcribed text into the prompt box so you can review or edit it before sending.

## Keyboard shortcuts

The {% data variables.copilot.github_copilot_app %} supports keyboard shortcuts to help you navigate sessions, switch between workspaces, and perform common actions. To see available shortcuts, open the app, go to **Help**, then **Keyboard Shortcuts**.

You can also start a session directly in the app using deep links from markdown files, third-party tools, or scripts. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/open-with-deep-links>).


</details>
