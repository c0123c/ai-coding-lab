# 连接 Copilot CLI 与 VS Code

说明自动或手动连接、共享上下文与信任设置、查看差异和恢复会话，使终端 Agent 与编辑器操作衔接。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-04-29](<https://github.com/github/docs/commit/c2e2506470b8e1f73b3e71580206751c5a7790c9>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/use-copilot-cli/connecting-vs-code.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/连接 Copilot CLI 与 VS Code.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Connecting {% data variables.copilot.copilot_cli %} to {% data variables.product.prodname_vscode_shortname %}
shortTitle: Connect to {% data variables.product.prodname_vscode_shortname %}
allowTitleToDifferFromFilename: true
intro: 'Connect {% data variables.copilot.copilot_cli_short %} to {% data variables.product.prodname_vscode_shortname %} to share context, trust settings, and output.'
versions:
  feature: copilot
contentType: how-tos
redirect_from:
  - /copilot/how-tos/copilot-cli/connecting-vs-code
category:
  - Author and optimize with Copilot # Copilot discovery page
  - Build with Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

将 {% data variables.copilot.copilot_cli_short %} 连接到 {% data variables.product.prodname_vscode_shortname %} 可为您提供两种环境的最佳性能：基于终端的工作流程的速度和灵活性，以及编辑器丰富的可视化工具。建立连接后，您可以：

* **使用您的编辑器选择作为上下文** - 选择{% data variables.product.prodname_vscode_shortname %}中的代码并直接在CLI提示中引用它，无需指定文件路径或行号。
* **以差异形式查看建议的更改** - 当 {% data variables.product.prodname_copilot_short %} 建议进行文件编辑时，它们会在 {% data variables.product.prodname_vscode_shortname %} 中显示为并排差异，以便您可以直观地查看、接受或拒绝每个更改。
* **表面实时诊断** - {% data variables.product.prodname_copilot_short %}可以访问来自{% data variables.product.prodname_vscode_shortname %}的实时错误和警告，因此它可以找到并修复编辑器已经检测到的问题。
* **跨工具拾取会话** — 查看{% data variables.product.prodname_vscode_shortname %}中的CLI会话记录，并在集成终端中恢复它们，而不会丢失上下文。

## 连接到{% data variables.product.prodname_vscode_shortname %}

当您启动CLI会话时，{% data variables.copilot.copilot_cli_short %}可以自动连接到{% data variables.product.prodname_vscode_shortname %}。此外，在交互式会话期间，您可以选择连接到本地计算机上的 {% data variables.product.prodname_vscode_shortname %} 中当前打开的任何工作区。

### 启动时自动连接

当您启动{% data variables.copilot.copilot_cli_short %}时，它会检查您启动CLI的当前工作目录是否与您在受信任模式下在{% data variables.product.prodname_vscode_shortname %}中打开的任何工作区文件夹匹配。如果存在匹配，则 CLI 连接到相关的 {% data variables.product.prodname_vscode_shortname %} 实例。无论您在何处使用 {% data variables.copilot.copilot_cli_short %}，都会发生连接：在 {% data variables.product.prodname_vscode_shortname %} 中的内置终端中，或在单独窗口中运行的外部终端应用程序中。

如果 {% data variables.copilot.copilot_cli_short %} 成功连接到 {% data variables.product.prodname_vscode_shortname %}，启动时显示的环境消息将包括“Visual Studio Code已连接”或“Visual Studio Code - 内部人员已连接”。

如果您在多个 {% data variables.product.prodname_vscode_shortname %} 窗口中打开相同的工作区，CLI 会自动连接到其中之一。它无法同时连接到多个IDE实例。如果您希望连接到 {% data variables.product.prodname_vscode_shortname %} 的不同实例，可以使用 `/ide` 命令进行切换。

> [!NOTE]
> 如果您使用 {% data variables.product.prodname_github_codespaces %}，则本地运行的 CLI 会话无法连接到在远程代码空间中运行的 {% data variables.product.prodname_vscode_shortname %} 工作区。但是，当您在代码空间内使用 CLI 时，即在 {% data variables.product.prodname_vscode_shortname %} 的内置终端内或在远程代码空间主机上的 SSH 会话中，您可以进行连接。

### 交互式会话期间手动连接

如果启动 {% data variables.copilot.copilot_cli_short %} 后在 {% data variables.product.prodname_vscode_shortname %} 中打开工作区，或者从与任何打开的工作区都不匹配的目录启动 CLI，则可以使用 `/ide` 斜杠命令手动连接到 {% data variables.product.prodname_vscode_shortname %} 工作区。您要连接的工作区当前必须在 {% data variables.product.prodname_vscode_shortname %} 中以可信模式打开。

## 使用 `/ide` 斜线命令管理连接

在交互式 {% data variables.copilot.copilot_cli_short %} 会话中使用 `/ide` 斜杠命令可以：

* **查看**当前连接状态 - 例如，如果您想检查当前连接的是哪个工作区。
* **将**连接到不同的{% data variables.product.prodname_vscode_shortname %}工作区。
* **断开**与{% data variables.product.prodname_vscode_shortname %}的连接。

您还可以从 `/ide` 菜单切换以下设置：

* **自动连接到匹配的IDE工作区**—控制CLI是否在启动时自动连接到匹配的{% data variables.product.prodname_vscode_shortname %}工作区。
* **在 IDE** 中打开文件编辑差异 - 控制建议的文件更改是否在 {% data variables.product.prodname_vscode_shortname %} 编辑器选项卡中显示为差异。

## 在提示中使用 {% data variables.product.prodname_vscode_shortname %} 上下文

当{% data variables.copilot.copilot_cli_short %}连接到{% data variables.product.prodname_vscode_shortname %}时，只要选择发生变化，它就会接收您当前的编辑器选择。该选择显示在 CLI 中的提示下，并右对齐。每当您在{% data variables.product.prodname_vscode_shortname %}中选择不同的代码时，该选择指示器就会更新。

这允许您选择 {% data variables.product.prodname_vscode_shortname %} 中的某些代码，然后使用提示，例如：

```copilot
Debug this
```

或者，您可以选择一些代码，但询问 {% data variables.product.prodname_copilot_short %} 有关整个文件的信息：

```copilot
Explain this file
```

## 以差异形式查看文件更改

当您要求 {% data variables.product.prodname_copilot_short %} 对工作区中的文件进行更改时，{% data variables.product.prodname_vscode_shortname %} 在新的编辑器选项卡中将建议的更改显示为差异。这样可以很容易地准确了解 {% data variables.product.prodname_copilot_short %} 的提议。使用差异视图右上角的接受 (✓) 或拒绝 (✗) 按钮应用或放弃更改。一旦您接受或拒绝差异，待处理的文件编辑权限就会得到解决，并且 CLI 会继续其工作流程。

> [!NOTE]
> * 如果您允许 {% data variables.product.prodname_copilot_short %} 在未经您批准的情况下编辑文件，则不会显示差异视图 - 例如，使用 `--allow-all` 或 `--yolo` 命令行选项，或者 `/allow-all` 或 `/yolo` 斜线命令。相反，建议的更改将直接应用于工作区中的文件，而不显示差异，并且 CLI 立即继续更新文件内容。
> * 如果您不想在 {% data variables.product.prodname_vscode_shortname %} 中使用差异视图，您可以在 `/ide` 菜单中关闭此功能。当您关闭此功能时，建议的文件更改将显示在 CLI 中。

## 查看并恢复 {% data variables.product.prodname_vscode_shortname %} 中的 CLI 会话

您可以从 {% data variables.product.prodname_vscode_shortname %} 中读取当前工作区的任何 {% data variables.copilot.copilot_cli_short %} 会话的记录。

1. 打开{% data variables.product.prodname_vscode_shortname %}中的**{% data variables.product.prodname_copilot_short %}聊天**侧栏。
1. 单击聊天面板右上角的会话图标 (<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" aria-hidden="true" style="vertical-align: middle;"><path d="M12.5 1H3.5C2.122 1 1 2.122 1 3.5V12.5C1 13.879 2.122 15 3.5 15H12.5C13.878 15 15 13.879 15 12.5V3.5C15 2.122 13.878 1 12.5 1ZM2 12.5V3.5C2 2.673 2.673 2 3.5 2H9V14H3.5C2.673 14 2 13.327 2 12.5ZM14 12.5C14 13.327 13.327 14 12.5 14H10V2H12.5C13.327 2 14 2.673 14 3.5V12.5Z"/></svg>) 以显示会话视图。

会话视图列出了您最近的 {% data variables.product.prodname_copilot_short %} 会话，最新的会话位于顶部。

1. 单击会话可阅读完整的输入和输出文本。对于 CLI 会话，记录与该会话期间终端中显示的内容相同。

如果您已为当前工作区运行了 CLI 会话，但尚未在会话视图中查看，则 {% data variables.product.prodname_vscode_shortname %} 标题栏中的聊天图标旁边会显示一个点图标和未读计数。单击它可切换未读会话的过滤列表。再次单击可清除过滤器并查看所有会话。

![{% data variables.product.prodname_vscode_shortname %} 中未读会话指示器的屏幕截图。](<https://github.com/assets/images/help/copilot/copilot-cli-vscode-unread-session.png>)

要在 {% data variables.product.prodname_vscode_shortname %} 的集成终端中继续 CLI 会话，请在会话视图中右键单击该会话，然后选择 **在终端中恢复 **。这是一种从外部终端窗口获取工作的快速方法，而不会丢失任何会话上下文。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)


## 原文对照

<details>
<summary>展开完整原文</summary>

Connecting {% data variables.copilot.copilot_cli_short %} to {% data variables.product.prodname_vscode_shortname %} gives you the best of both environments: the speed and flexibility of a terminal-based workflow, combined with the rich visual tools of your editor. With a connection established, you can:

* **Use your editor selection as context** — Select code in {% data variables.product.prodname_vscode_shortname %} and reference it directly in CLI prompts, without needing to specify file paths or line numbers.
* **Review proposed changes as diffs** — When {% data variables.product.prodname_copilot_short %} suggests file edits, they appear as a side-by-side diff in {% data variables.product.prodname_vscode_shortname %} so you can review, accept, or reject each change visually.
* **Surface live diagnostics** — {% data variables.product.prodname_copilot_short %} can access real-time errors and warnings from {% data variables.product.prodname_vscode_shortname %}, so it can find and fix problems that your editor has already detected.
* **Pick up sessions across tools** — View CLI session transcripts in {% data variables.product.prodname_vscode_shortname %} and resume them in the integrated terminal without losing context.

## Connecting to {% data variables.product.prodname_vscode_shortname %}

{% data variables.copilot.copilot_cli_short %} can automatically connect to {% data variables.product.prodname_vscode_shortname %} when you start a CLI session. Additionally, during an interactive session, you can choose to connect to any workspace that is currently open in {% data variables.product.prodname_vscode_shortname %} on the local machine.

### Automatic connection at startup

When you start {% data variables.copilot.copilot_cli_short %}, it checks whether the current working directory from which you started the CLI matches any workspace folder you have open in {% data variables.product.prodname_vscode_shortname %} in trusted mode. If there is a match, the CLI connects to the relevant {% data variables.product.prodname_vscode_shortname %} instance. The connection happens regardless of where you are using {% data variables.copilot.copilot_cli_short %}: in a built-in terminal in {% data variables.product.prodname_vscode_shortname %}, or in an external terminal application running in a separate window.

If {% data variables.copilot.copilot_cli_short %} successfully connects to {% data variables.product.prodname_vscode_shortname %}, the environment message that's displayed at startup will include either "Visual Studio Code connected" or "Visual Studio Code - Insiders connected."

If you have the same workspace open in more than one {% data variables.product.prodname_vscode_shortname %} window, the CLI connects to one of them automatically. It cannot connect to multiple IDE instances at the same time. If you prefer to connect to a different instance of {% data variables.product.prodname_vscode_shortname %}, you can switch by using the `/ide` command.

> [!NOTE]
> If you are using {% data variables.product.prodname_github_codespaces %}, a CLI session running locally cannot connect to a {% data variables.product.prodname_vscode_shortname %} workspace running in the remote codespace. You can, however, connect when you use the CLI inside the codespace—that is, within {% data variables.product.prodname_vscode_shortname %}'s built-in terminal or in an SSH session on the remote codespace host.

### Manual connection during an interactive session

If you open a workspace in {% data variables.product.prodname_vscode_shortname %} after starting {% data variables.copilot.copilot_cli_short %}, or if you started the CLI from a directory that doesn't match any open workspace, you can use the `/ide` slash command to manually connect to a {% data variables.product.prodname_vscode_shortname %} workspace. The workspace you want to connect to must be currently open in trusted mode in {% data variables.product.prodname_vscode_shortname %}.

## Managing the connection with the `/ide` slash command

Use the `/ide` slash command in an interactive {% data variables.copilot.copilot_cli_short %} session to:

* **View** the current connection status—for example, if you want to check which workspace is currently connected.
* **Connect** to a different {% data variables.product.prodname_vscode_shortname %} workspace.
* **Disconnect** from {% data variables.product.prodname_vscode_shortname %}.

You can also toggle the following settings from the `/ide` menu:

* **Auto-connect to matching IDE workspace**—controls whether the CLI automatically connects to a matching {% data variables.product.prodname_vscode_shortname %} workspace at startup.
* **Open file edit diffs in IDE**—controls whether proposed file changes are shown as diffs in a {% data variables.product.prodname_vscode_shortname %} editor tab.

## Using {% data variables.product.prodname_vscode_shortname %} context in prompts

When {% data variables.copilot.copilot_cli_short %} is connected to {% data variables.product.prodname_vscode_shortname %}, it receives your current editor selection whenever the selection changes. The selection is displayed under your prompt in the CLI, aligned to the right. This selection indicator is updated whenever you select different code in {% data variables.product.prodname_vscode_shortname %}.

This allows you to select some code in {% data variables.product.prodname_vscode_shortname %} and then use a prompt such as:

```copilot
Debug this
```

Alternatively, you can select some code but ask {% data variables.product.prodname_copilot_short %} about the whole file:

```copilot
Explain this file
```

## Reviewing file changes as diffs

When you ask {% data variables.product.prodname_copilot_short %} to make changes to a file in the workspace, {% data variables.product.prodname_vscode_shortname %} displays the proposed changes as a diff in a new editor tab. This makes it easy to see exactly what {% data variables.product.prodname_copilot_short %} is proposing. Use the accept (✓) or reject (✗) buttons in the top-right of the diff view to apply or discard the changes. Once you accept or reject the diff, the pending file-edit permission is resolved and the CLI continues its workflow.

> [!NOTE]
> * The diff view is not shown if you have allowed {% data variables.product.prodname_copilot_short %} to edit files without your approval—for example, using the `--allow-all` or `--yolo` command-line options, or the `/allow-all` or `/yolo` slash commands. Instead, the proposed changes are applied directly to the file in the workspace without showing a diff, and the CLI continues immediately with the updated file content.
> * If you prefer not to use the diff view in {% data variables.product.prodname_vscode_shortname %} you can turn this feature off in the `/ide` menu. When you turn this off, the proposed file changes are displayed in the CLI.

## Viewing and resuming CLI sessions in {% data variables.product.prodname_vscode_shortname %}

You can read the transcript of any {% data variables.copilot.copilot_cli_short %} session for the current workspace from within {% data variables.product.prodname_vscode_shortname %}.

1. Open the **{% data variables.product.prodname_copilot_short %} Chat** side bar in {% data variables.product.prodname_vscode_shortname %}.
1. Click the Sessions icon (<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" aria-hidden="true" style="vertical-align: middle;"><path d="M12.5 1H3.5C2.122 1 1 2.122 1 3.5V12.5C1 13.879 2.122 15 3.5 15H12.5C13.878 15 15 13.879 15 12.5V3.5C15 2.122 13.878 1 12.5 1ZM2 12.5V3.5C2 2.673 2.673 2 3.5 2H9V14H3.5C2.673 14 2 13.327 2 12.5ZM14 12.5C14 13.327 13.327 14 12.5 14H10V2H12.5C13.327 2 14 2.673 14 3.5V12.5Z"/></svg>) at the top right of the Chat panel to display the Sessions view.

   The Sessions view lists your most recent {% data variables.product.prodname_copilot_short %} sessions, with the most recent at the top.

1. Click a session to read the full input and output text. For CLI sessions, the transcript is identical to what was displayed in the terminal during that session.

If you have run a CLI session for the current workspace that you have not yet viewed in the Sessions view, a dot icon and an unread count are shown next to the Chat icon in the {% data variables.product.prodname_vscode_shortname %} title bar. Click it to toggle a filtered list of unread sessions. Click it again to clear the filter and view all sessions.

![Screenshot of the unread sessions indicator in {% data variables.product.prodname_vscode_shortname %}.](<https://github.com/assets/images/help/copilot/copilot-cli-vscode-unread-session.png>)

To continue a CLI session in {% data variables.product.prodname_vscode_shortname %}'s integrated terminal, right-click the session in the Sessions view and choose **Resume in Terminal**. This is a quick way to pick up work from an external terminal window without losing any session context.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)


</details>
