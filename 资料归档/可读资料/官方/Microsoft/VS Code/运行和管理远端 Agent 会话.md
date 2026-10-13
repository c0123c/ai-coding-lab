# 运行和管理远端 Agent 会话

讲解 SSH、开发隧道、浏览器、远端工作区和 Dev Container 接入，帮助利用远端资源并查看任务进度。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/remote-agent-sessions.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/run/remote-agent-sessions.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/运行和管理远端 Agent 会话.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: c7e2f4a1-8d3b-4a6e-9c5d-2f1b3e8a7d4c
DateApproved: 10/7/2026
MetaDescription: Delegate work to remote agent hosts, use remote Dev Containers, and manage sessions in the browser-based {% data variables.copilot.agents_window %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 运行和管理远程 agent 会话

[{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) 允许您连接到远程计算机以启动 agent 会话或检查现有会话。您可以通过开发隧道通过 SSH 进行连接，或直接在任何设备的浏览器中使用 {% data variables.copilot.agents_window %}。

当您想要利用远程计算机的资源、通过移动设备工作或在离开主开发计算机时检查 agent 的进度时，这非常有用。

{% data variables.copilot.agents_window %} 通过 SSH 或开发隧道使用 [Agent 主机协议 (AHP)](<https://microsoft.github.io/agent-host-protocol/>) 连接到远程计算机。连接时，{% data variables.copilot.agents_window %} 会自动在远程计算机上安装并启动 {% data variables.product.prodname_vscode_shortname %} CLI。远程计算机必须已开机并可通过网络进行访问。

## 在远程主机上没有工作区的情况下开始聊天

当任务需要远程主机的资源或环境但不需要存储库时，请使用没有工作区的聊天。首先，启用 `setting(sessions.chat.unifiedWorkspacePicker.enabled)` 并通过 SSH 或开发隧道连接主机。

要在没有工作区的情况下开始聊天：

1. 选择**新建**或按`kb(workbench.action.chat.newChat)`开始新的agent会话。

1. 在工作区选择器中，选择已连接远程主机的 **Chat** 条目，而不选择文件夹。该条目标识运行聊天的主机。

1. 选择 agent 线束，输入提示，然后按 `kbstyle(Enter)`。

主工作区选择器还显示来自连接的远程主机的文件夹以及本地文件夹。当任务需要现有工作区时，选择远程文件夹。

## 通过 SSH 连接

**先决条件**：远程计算机必须可通过SSH访问。远程计算机上不需要额外安装 agent。

要通过 SSH 在远程计算机上启动会话：

1. 选择**新建**或按`kb(workbench.action.chat.newChat)`开始新的agent会话。

1. 在工作区下拉列表中，选择 **远程** 选项卡，然后选择 **SSH**。如果您已经设置了 SSH 连接，它们将在下拉列表中显示为选项。

![屏幕截图显示了在 {% data variables.copilot.agents_window %} 中启动新的 agent 会话时如何在工作区下拉列表中选择 SSH。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-remote.png>)

1. 输入远程计算机的 SSH 连接字符串（例如，`user@hostname`）。

1. 选择远程计算机上用于会话的文件夹。

1. 输入提示并按 `kbstyle(Enter)` 启动会话。

## 通过开发隧道连接

**先决条件**：开发隧道已在远程计算机上运行。有关设置说明，请参阅[使用远程隧道进行开发](<https://github.com/docs/remote/tunnels.md>)。

要通过开发隧道在远程计算机上启动会话：

1. 选择**新建**或按`kb(workbench.action.chat.newChat)`开始新的agent会话。

1. 在工作区下拉列表中，选择 **远程** 选项卡，然后选择 **隧道** 并选择您的帐户类型。

![屏幕截图显示了在 {% data variables.copilot.agents_window %} 中启动新的 agent 会话时如何在工作区下拉列表中选择隧道。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-remote.png>)

1. 从列表中选择活动的开发隧道。

1. 选择远程计算机上用于会话的文件夹。

1. 输入提示并按 `kbstyle(Enter)` 启动会话。

> [!IMPORTANT]
> 确保您的开发隧道需要身份验证（GitHub 或 Microsoft 帐户）。如果隧道允许匿名访问，则发现 URL 的任何人都可以访问您的计算机并启动 agent 会话。当自动批准模式处于活动状态时，这尤其危险，因为未经授权的用户可以使用您的凭据触发 AI- 辅助命令执行。有关更多信息，请参阅[安全性](<https://github.com/docs/agents/run/security.md>)。

## 将工作委派给远程 agent 主机（实验性）

从 {% data variables.copilot.agents_window %} 中的 Agent 主机会话中，将任务委派给已连接的远程主机，而无需在工作区选择器中选择主机。同时启用【`setting(chat.remoteAgentHostsEnabled)`】和【`setting(chat.remoteSessions.tools.enabled)`】，并连接【agent】可以使用的主机。

远程委派工具让 agent：

* 使用`list_agent_hosts`发现连接的主机、可用型号、资源容量和会话负载。
* 使用`create_remote_session`在特定主机上启动会话或自动选择主机。对于自动放置，请指定 Windows、Linux 或 macOS、最小内存、逻辑 CPU 计数和可选模型等条件。在匹配的主机中，布局优先于正在运行的会话和待处理会话创建最少的主机。如果没有连接的主机满足所有条件，该工具将报告错误，而不是选择不匹配的主机。
* 使用`get_remote_session`检查远程会话的状态和最新响应。
* 使用 `send_remote_message` 将后续工作或报告结果和问题发送到确切的发起聊天。如果该聊天很忙，则消息会在队列中等待。

对于存储库工作，请直接或在新的 Git 工作树中指定目标主机上的现有受信任文件夹。这些工具不会将协调会话的工作区克隆或复制到远程主机。如果您不指定工作区，远程会话将在没有工作区的情况下启动。

正常的批准要求仍然适用于委派的工作和消息。在批准工具调用之前，请检查所选的主机、文件夹和工作树，尤其是当 agent 使用自动放置时。

在委派工作运行时保持协调 {% data variables.copilot.agents_window %} 打开和连接，以便消息可以在会话之间流动。远程会话的最终响应不会自动转发。要求远程agent使用`send_remote_message`报告其结果。

关闭 `setting(chat.remoteSessions.tools.enabled)` 会删除远程委派工具，但不会断开主机连接或停止已在运行的远程会话。

## 在远程开发容器中运行会话

`feature(agent-host-dev-containers)`

在远程项目的开发容器内运行 agents 以使用其配置的工具和依赖项，而无需将它们直接安装在主机上。从 {% data variables.product.prodname_vscode_shortname %} 1.139 开始，桌面 {% data variables.copilot.agents_window %} 中的 SSH、隧道和 WSL 主机支持此功能。

开始之前，请启用 `setting(chat.agentHost.devContainer.enabled)`。 Docker 必须在远程主机上安装并运行，并且项目必须包含 [Dev Container 配置 ](<https://github.com/docs/devcontainers/create-dev-container.md>)。

要在远程开发容器中运行会话：

1. 连接到 SSH、隧道或 WSL 主机，然后在工作区选取器中选择项目文件夹。

1. 展开远程文件夹的菜单并选择 **Use Dev Container**。

工作区标签获得 **- Dev Container** 后缀。

1. 选择可用的 agent 线束，配置会话，然后输入提示。

仅当源主机通告 Dev Container 支持时，才会出现 **Use Dev Container** 选项。它不适用于不受支持的主机或源嵌套在另一个远程环境中的文件夹。有关先决条件、切换回源主机以及故障排除的更多信息，请参阅[在开发容器中运行会话](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)。

## 在浏览器中使用{% data variables.copilot.agents_window %}

{% data variables.copilot.agents_window %} 还可以在 <https://insiders.vscode.dev/agents> 上作为 Web 客户端使用，因此您可以使用浏览器从任何设备管理 agent 会话。当您离开主开发计算机、使用移动设备工作或想要检查远程主机上运行的会话而不在本地安装 {% data variables.product.prodname_vscode %} 时，这非常有用。

基于浏览器的 {% data variables.copilot.agents_window %} 通过 [ 开发隧道 ](<https://github.com/docs/remote/tunnels.md>) 连接到您的开发计算机。 Agent 会话在远程主机上运行，​​浏览器充当轻量级客户端，用于聊天、查看更改和管理会话。

### 设置开发隧道

在浏览器中使用 {% data variables.copilot.agents_window %} 之前，请在要连接的计算机上启动开发隧道：

1. 在远程主机上，运行以下命令来启动开发隧道：

    ```bash
    code-insiders tunnel
    ```

如果您使用的是稳定版本，请运行 `code tunnel`。

第一次运行此命令时，系统会提示您使用 GitHub 或 Microsoft 帐户进行身份验证。为了安全起见，隧道默认需要身份验证。

1. 隧道运行后，在任何设备上的浏览器中打开<https://insiders.vscode.dev/agents>。

1. 出现提示时使用 GitHub** 使用 **Continue 登录。如果您已经通过身份验证，则会直接加载 {% data variables.copilot.agents_window %}。

1. 您的隧道主机将显示在窗口顶部的主机栏中。选择它进行连接。

1. 选择远程计算机上的文件夹，选择 agent，然后启动会话。

### 主机管理

基于浏览器的 {% data variables.copilot.agents_window %} 中的主机栏显示您可用的隧道主机。每个主机显示其连接状态：

* **在线**：主机可访问，您可以在其上启动或继续会话。
* **离线**：主机上的隧道未运行。启动主机上的隧道以使其重新联机。

您可以直接通过主机栏连接和断开与主机的连接。如果主机在您有活动会话时脱机，则该会话将显示断开连接状态。当主机重新上线时，会话会自动重新连接。

## 相关资源

* [跨多个项目使用 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - agent-first 工作流程。
* [选择 agent 工具](<https://github.com/docs/agents/run/agent-harnesses.md>) - 比较工具、执行环境和隔离选项。
* [使用远程隧道进行开发](<https://github.com/docs/remote/tunnels.md>) - 设置和管理开发隧道。
* [安全](<https://github.com/docs/agents/run/security.md>) - 信任边界和安全注意事项。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Run and manage remote agent sessions

The [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) lets you connect to remote machines to start agent sessions or check in on existing ones. You can connect over SSH, through a dev tunnel, or use the {% data variables.copilot.agents_window %} directly in a browser from any device.

This is useful when you want to take advantage of a remote machine's resources, work from a mobile device, or check in on your agent's progress when you're away from your main development machine.

The {% data variables.copilot.agents_window %} connects to the remote machine by using the [Agent Host Protocol (AHP)](<https://microsoft.github.io/agent-host-protocol/>) over SSH or a dev tunnel. When you connect, the {% data variables.copilot.agents_window %} automatically installs and starts the {% data variables.product.prodname_vscode_shortname %} CLI on the remote machine. The remote machine must be powered on and accessible over the network.

## Start a chat without a workspace on a remote host

Use a chat without a workspace when a task needs the resources or environment of a remote host but doesn't need a repository. First, enable `setting(sessions.chat.unifiedWorkspacePicker.enabled)` and connect the host over SSH or a dev tunnel.

To start a chat without a workspace:

1. Select **New** or press `kb(workbench.action.chat.newChat)` to start a new agent session.

1. In the workspace picker, select the **Chat** entry for a connected remote host without choosing a folder. The entry identifies the host where the chat runs.

1. Choose an agent harness, enter your prompt, and press `kbstyle(Enter)`.

The main workspace picker also shows folders from connected remote hosts alongside local folders. Select a remote folder there when the task needs an existing workspace.

## Connect via SSH

**Prerequisite**: the remote machine must be accessible over SSH. No extra agent installation is needed on the remote machine.

To start a session on a remote machine via SSH:

1. Select **New** or press `kb(workbench.action.chat.newChat)` to start a new agent session.

1. In the workspace dropdown, select the **Remote** tab, and then select **SSH**. If you've already set up SSH connections, they appear as options in the dropdown.

    ![Screenshot showing how to select SSH in the workspace dropdown when starting a new agent session in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-remote.png>)

1. Enter the SSH connection string for the remote machine (for example, `user@hostname`).

1. Select the folder on the remote machine to use for the session.

1. Type a prompt and press `kbstyle(Enter)` to start the session.

## Connect via dev tunnel

**Prerequisite**: a dev tunnel is already running on the remote machine. See [Developing with Remote Tunnels](<https://github.com/docs/remote/tunnels.md>) for setup instructions.

To start a session on a remote machine via dev tunnel:

1. Select **New** or press `kb(workbench.action.chat.newChat)` to start a new agent session.

1. In the workspace dropdown, select the **Remote** tab, and then select **Tunnels** and choose your account type.

    ![Screenshot showing how to select Tunnels in the workspace dropdown when starting a new agent session in the {% data variables.copilot.agents_window %}.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/agents-window/agents-window-remote.png>)

1. Choose the active dev tunnel from the list.

1. Select the folder on the remote machine to use for the session.

1. Type a prompt and press `kbstyle(Enter)` to start the session.

> [!IMPORTANT]
> Ensure your dev tunnel requires authentication (GitHub or Microsoft account). If the tunnel allows anonymous access, anyone who discovers the URL can reach your machine and start agent sessions. This is especially dangerous when auto-approval modes are active, because unauthorized users can trigger AI-assisted command execution with your credentials. For more information, see [Security](<https://github.com/docs/agents/run/security.md>).

## Delegate work to remote agent hosts (Experimental)

From an Agent Host session in the {% data variables.copilot.agents_window %}, delegate a task to a connected remote host without selecting the host in the workspace picker. Enable both `setting(chat.remoteAgentHostsEnabled)` and `setting(chat.remoteSessions.tools.enabled)`, and connect the hosts that the agent can use.

The remote delegation tools let an agent:

* Use `list_agent_hosts` to discover connected hosts, available models, resource capacity, and session load.
* Use `create_remote_session` to start a session on a specific host or choose a host automatically. For automatic placement, specify criteria such as Windows, Linux, or macOS, minimum memory, logical CPU count, and an optional model. Among matching hosts, placement favors the host with the fewest running sessions and pending session creations. If no connected host meets all the criteria, the tool reports an error instead of selecting a nonmatching host.
* Use `get_remote_session` to check a remote session's status and latest response.
* Use `send_remote_message` to send follow-up work or report results and questions to the exact originating chat. If that chat is busy, the message waits in its queue.

For repository work, specify an existing trusted folder on the target host, either directly or in a new Git worktree. The tools don't clone or copy the coordinating session's workspace to the remote host. If you don't specify a workspace, the remote session starts without one.

Normal approval requirements still apply to delegated work and messages. Review the selected host, folder, and worktree before you approve a tool call, especially when the agent uses automatic placement.

Keep the coordinating {% data variables.copilot.agents_window %} open and connected while delegated work runs so messages can flow between sessions. A remote session's final response isn't forwarded automatically. Ask the remote agent to use `send_remote_message` to report its result.

Turning off `setting(chat.remoteSessions.tools.enabled)` removes the remote delegation tools, but doesn't disconnect hosts or stop remote sessions that are already running.

## Run a session in a remote Dev Container

`feature(agent-host-dev-containers)`

Run agents inside your remote project's Dev Container to use its configured tools and dependencies without installing them directly on the host. Starting in {% data variables.product.prodname_vscode_shortname %} 1.139, this is supported for SSH, Tunnel, and WSL hosts in the desktop {% data variables.copilot.agents_window %}.

Before you start, enable `setting(chat.agentHost.devContainer.enabled)`. Docker must be installed and running on the remote host, and the project must contain a [Dev Container configuration](<https://github.com/docs/devcontainers/create-dev-container.md>).

To run a session in a remote Dev Container:

1. Connect to an SSH, Tunnel, or WSL host and select the project folder in the workspace picker.

1. Expand the menu for the remote folder and select **Use Dev Container**.

    The workspace label gains the **- Dev Container** suffix.

1. Choose an available agent harness, configure the session, and enter your prompt.

The **Use Dev Container** option appears only when the source host advertises Dev Container support. It isn't available for unsupported hosts or for folders whose source is nested inside another remote environment. For more information about prerequisites, switching back to the source host, and troubleshooting, see [Run a session in a Dev Container](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>).

## Use the {% data variables.copilot.agents_window %} in the browser

The {% data variables.copilot.agents_window %} is also available as a web client at <https://insiders.vscode.dev/agents>, so you can manage agent sessions from any device with a browser. This is useful when you're away from your main development machine, working from a mobile device, or want to check in on sessions running on a remote host without installing {% data variables.product.prodname_vscode %} locally.

The browser-based {% data variables.copilot.agents_window %} connects to your development machine through a [dev tunnel](<https://github.com/docs/remote/tunnels.md>). Agent sessions run on the remote host, and the browser acts as a lightweight client for chatting, reviewing changes, and managing sessions.

### Set up a dev tunnel

Before you can use the {% data variables.copilot.agents_window %} in the browser, start a dev tunnel on the machine you want to connect to:

1. On your remote host, run the following command to start a dev tunnel:

    ```bash
    code-insiders tunnel
    ```

    If you're using the stable release, run `code tunnel` instead.

    The first time you run this command, you're prompted to authenticate with your GitHub or Microsoft account. The tunnel requires authentication by default for security.

1. After the tunnel is running, open <https://insiders.vscode.dev/agents> in a browser on any device.

1. Sign in with **Continue with GitHub** when prompted. If you're already authenticated, the {% data variables.copilot.agents_window %} loads directly.

1. Your tunnel host appears in the hosts bar at the top of the window. Select it to connect.

1. Choose a folder on the remote machine, select an agent, and start a session.

### Host management

The hosts bar in the browser-based {% data variables.copilot.agents_window %} shows your available tunnel hosts. Each host displays its connection status:

* **Online**: the host is reachable and you can start or continue sessions on it.
* **Offline**: the tunnel on the host is not running. Start the tunnel on the host to bring it back online.

You can connect and disconnect from hosts directly through the hosts bar. If a host goes offline while you have an active session, the session shows a disconnected state. When the host comes back online, the session reconnects automatically.

## Related resources

* [Use the {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) - agent-first workflows across multiple projects.
* [Choose an agent harness](<https://github.com/docs/agents/run/agent-harnesses.md>) - compare harnesses, execution environments, and isolation options.
* [Developing with Remote Tunnels](<https://github.com/docs/remote/tunnels.md>) - set up and manage dev tunnels.
* [Security](<https://github.com/docs/agents/run/security.md>) - trust boundaries and security considerations.


</details>
