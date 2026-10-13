# 理解 VS Code Agent Host 架构

解释专用进程、Agent Host Protocol、本地与远端运行、客户端工具及扩展宿主行为；正文标明仍在持续开发。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/agent-host.md>)；源码提交（存档版）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/agent-host.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/理解 VS Code Agent Host 架构.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 9c358671-d18a-4c50-beab-e69beb997ea2
DateApproved: 10/7/2026
MetaDescription: Understand how the {% data variables.product.prodname_vscode_shortname %} Agent Host runs local, remote, and Dev Container sessions.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- agent host
- agent host protocol
- ahp
- ai agents
- architecture
- remote agents
- headless agents
- multi-client
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 了解{% data variables.product.prodname_vscode_shortname %}Agent主机

{% data variables.product.prodname_vscode_shortname %} 在称为 Agent 主机的专用进程中运行 AI 编码 agents，它通过 Agent 主机协议 (AHP) 与该进程进行通信。主机拥有独立于显示和控制会话的客户端的 agent 会话。

> [！NOTE]
> Agent主机和AHP正在积极开发中，新功能不断推出。

## 为什么需要专用的 Agent 主机？

agents 的专用 Agent 主机进程提供以下功能：

* **共享会话**：多个客户端可以观察和控制同一个会话，保持同步。
* **远程执行**：主机可以在另一台计算机上的工作区旁边运行，而客户端从其他地方连接。
* **独立执行**：当没有编辑器或其他客户端连接时，agent会话可以继续。
* **多种agent实现**：不同的agent运行时插入一个面向主机的接口并向客户端呈现通用会话概念。
* **专用进程**：agents在自己的进程中运行，它们不会被繁忙的扩展阻止。

早期版本在扩展主机中运行 agent 逻辑，以及 Copilot 聊天扩展。扩展主机对于可扩展性仍然很重要，但它是围绕扩展的生命周期和 API 设计的，并且长期运行的自主工作有不同的需求。扩展程序仍然可以提供聊天自定义功能，例如工具、MCP 服务器和自定义 agents，但 agent 运行时本身在 Agent 主机进程中运行。默认情况下，扩展程序中的工具仅在运行扩展程序的编辑器窗口的聊天中可用。

![显示 {% data variables.product.prodname_vscode_shortname %} 与扩展主机自定义和 Agent 主机通信的屏幕截图，其中包含 Copilot、Claude 和 Codex 的适配器。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/concepts/agent-host-transition.svg>)

## 流程架构

Agent 主机可以作为本地实用程序进程运行，也可以作为远程计算机上的独立服务器运行。 {% data variables.product.prodname_vscode_shortname %} 通过 WebSocket 使用本地 IPC 和 AHP JSON-RPC 的消息端口进行远程连接。

第一方 agent 适配器在 Agent 主机进程内运行。适配器在其 agent 运行时和通用 AHP 会话模型之间进行转换。

Agent 主机位于工作区旁边。它可以在您的计算机上、开发容器内或远程计算机上运行。文件编辑和命令在包含主机的环境中运行。

## Agent 主机协议

[Agent 主机协议](<https://microsoft.github.io/agent-host-protocol/>) 是主机与其客户端之间的开放式、agent 不可知的协议。它使用 JSON-RPC 进行通信和不可变状态，并使用纯减速器来同步会话数据。

楼主是真理的来源。每个客户端订阅 URI- 寻址通道以获取会话、聊天、终端和变更集等资源。客户端接收初始状态快照，然后接收有序操作。如果连接断开，客户端会重新连接并接收错过的操作或新快照。

## 独立的，带有可选的客户端工具

定义Agent主机原理是agent可以在没有客户端的情况下运行。客户端是可以来来去去的查看器和控制器。因此，主机包括管理会话和使用工作区所需的基线功能。

Agent 会话与其工作区窗口的生命周期无关。您可以关闭该窗口，稍后从另一个窗口重新打开会话。当 Agent 主机保持运行时，活动轮流可以在没有连接的客户端的情况下继续。

连接的客户端也可以贡献工具。例如，{% data variables.product.prodname_vscode_shortname %} 可以宣传客户端提供的工具（如浏览器工具）或已安装的扩展。 Agent 主机将这些定义添加到活动会话中，并将工具调用路由回提供它的客户端。

## 本地和远程主机

桌面 {% data variables.copilot.agents_window %} 可以连接到同一台计算机上或已连接的 SSH、隧道或 WSL 主机上的 Agent 主机。基于 [ 浏览器的 {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/remote-agent-sessions.md#use-the-agents-window-in-the-browser>) 通过开发隧道连接到您的开发计算机。浏览器是客户端，而不是运行会话的主机。

![显示桌面和浏览器客户端连接到 Agent 主机的屏幕截图。桌面客户端可以使用主机工作区或开发容器，而浏览器则通过开发隧道连接到开发计算机。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/concepts/agent-host-deployment.svg>)

客户端显示和控制会话。 Agent 主机拥有它们。桌面和浏览器客户端可以连接到同一隧道主机。

`feature(agent-host-dev-containers)`

对于开发容器会话，Agent 主机在项目的容器内运行。容器可以在您的计算机或受支持的 SSH、隧道或 WSL 主机上运行，​​而桌面 {% data variables.copilot.agents_window %} 保留在您的计算机上。工作区文件编辑和命令使用容器内的工具和依赖项，而不是直接安装在源主机上的工具和依赖项。

开发容器执行是一种环境选择，而不是不同的工具。请参阅[会话执行选项图](<https://github.com/docs/agents/concepts/agent-harnesses.md#relate-execution-environments-and-code-isolation>)，了解本地、连接主机、容器和云执行之间的关系。有关设置步骤，请参阅 [在开发容器中运行 agent 会话 ](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>)。

与[{% data variables.product.prodname_vscode_shortname %}远程开发](<https://github.com/docs/remote/remote-overview.md>)一样，用户界面保留在客户端上，而工作区操作靠近源代码和开发工具运行。

要运行您自己的独立 Agent 主机，请使用 `code agent host`。默认情况下，该命令在本地主机上启动服务器并使用连接令牌保护它。使用 `--tunnel` 选项通过开发隧道公开它。

## 扩展主机上的行为

不在 Agent 主机上运行的 Agent 会话在扩展主机中运行。现有的分机-主机会话继续在那里运行。

在扩展主机上运行的 agent 会话的行为存在一些差异：

|行为|差异|
|----------|------------|
|审查变更|Agent 主机会话将编辑直接应用于会话文件夹或工作树。查看生成的差异，然后提交、合并或放弃更改。扩展主机会话将编辑跟踪为待处理，直到您保留或撤消它们。了解有关 [ 审核 AI- 生成的代码编辑 ](<https://github.com/docs/agents/run/review-code-edits.md>) 的更多信息。|
|定制|Agent 主机从与线束无关的文件夹（例如 `~/.copilot` 和 `~/.claude`）读取用户级自定义设置。仅存储在 {% data variables.product.prodname_vscode_shortname %} 个人资料用户数据中的自定义是 Copilot agent 无法读取的旧位置。了解如何[迁移agent自定义项](<https://github.com/docs/agent-customization/migrate-customizations.md>)。|
|挂钩|Agent 主机没有为每个 agent 定义一个共享钩子模式。选定的 Copilot、Claude 或 Codex 工具执行其提供程序挂钩实现。分机主机会话使用本地挂钩实现和本地设置。了解如何[选择会话的挂钩实现](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>)。|
|自动驾驶仪|在 Agent 主机上，[Autopilot](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>) 是 agent 模式。在扩展主机上，它是一个权限级别。|
|辅助权限`feature(assisted-permissions)`|[辅助权限](<https://github.com/docs/agents/run/approvals.md#permission-levels>)级别仅适用于受支持的Agent主机会话，并且在稳定版中默认处于关闭状态。|
|会话功能|共享多窗口会话、每个会话多个聊天、快速聊天和远程托管仅在 Agent 主机上可用。|
|扩展提供的工具|扩展程序中的工具仅在扩展程序运行的编辑器窗口中的聊天中可用。|
|MCP配置|Agent 主机从 `.mcp.json`（工作空间）和 `~/.copilot/mcp-config.json`（用户）读取与线束无关的 MCP 配置。它不会直接读取 `.vscode/mcp.json`，但 {% data variables.product.prodname_vscode_shortname %} 会将您在 {% data variables.product.prodname_vscode_shortname %} 中配置的服务器转发到 Agent 主机，需要交互式输入的服务器（例如 `${input:...}` 变量）除外。了解有关 [ 配置 MCP 服务器 ](<https://github.com/docs/agent-customization/mcp-servers.md>) 的更多信息。|

## 相关资源

* [Agent 主机协议文档](<https://microsoft.github.io/agent-host-protocol/>)
* [Agent 主机协议源存储库](<https://github.com/microsoft/agent-host-protocol>)
* [Agents 在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/concepts/agents.md>) 中
* [远程 agent 会话](<https://github.com/docs/agents/run/remote-agent-sessions.md>)
* [{% data variables.product.prodname_vscode_shortname %} 远程开发架构](<https://github.com/docs/remote/remote-overview.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Understand the {% data variables.product.prodname_vscode_shortname %} Agent Host

{% data variables.product.prodname_vscode_shortname %} runs AI coding agents in a dedicated process called the Agent Host, which it communicates with through the Agent Host Protocol (AHP). The host owns agent sessions independently of the clients that display and control them.

> [!NOTE]
> The Agent Host and AHP are under active development, and new capabilities continue to roll out.

## Why a dedicated Agent Host?

A dedicated Agent Host process for agents provides the following capabilities:

* **Shared sessions**: multiple clients can observe and control the same session, staying in sync.
* **Remote execution**: the host can run next to the workspace on another machine while clients connect from elsewhere.
* **Independent execution**: an agent session can continue when no editor or other client is connected.
* **Multiple agent implementations**: different agent runtimes plug into one host-facing interface and present common session concepts to clients.
* **Dedicated process**: agents run in their own process, where they won't be blocked by busy extensions.

Earlier versions ran agent logic in the extension host, alongside the Copilot Chat extension. The extension host remains important for extensibility, but it is designed around the lifecycle and APIs of extensions, and long-running autonomous work has different needs. Extensions can still contribute chat customizations such as tools, MCP servers, and custom agents, but the agent runtime itself runs in the Agent Host process. By default, tools from extensions are only available in chats in an editor window where the extension is running.

![Screenshot showing {% data variables.product.prodname_vscode_shortname %} communicating with extension-host customizations and the Agent Host, which contains adapters for Copilot, Claude, and Codex.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/concepts/agent-host-transition.svg>)

## Process architecture

The Agent Host can run as a local utility process or as a standalone server on a remote machine. {% data variables.product.prodname_vscode_shortname %} uses a message port for local IPC and AHP JSON-RPC over WebSocket for remote connections.

The first-party agent adapters run inside the Agent Host process. An adapter translates between its agent runtime and the common AHP session model.

The Agent Host lives next to the workspace. It can run on your machine, inside a Dev Container, or on a remote machine. File edits and commands run in the environment that contains the host.

## Agent Host Protocol

[Agent Host Protocol](<https://microsoft.github.io/agent-host-protocol/>) is an open, agent-agnostic protocol between a host and its clients. It uses JSON-RPC for communication and immutable state with pure reducers for synchronized session data.

The host is the source of truth. Each client subscribes to URI-addressed channels for resources such as sessions, chats, terminals, and changesets. The client receives an initial state snapshot followed by ordered actions. If the connection drops, the client reconnects and receives missed actions or a fresh snapshot.

## Self-contained, with optional client tools

The defining Agent Host principle is that the agent can run without a client. A client is a viewer and controller that can come and go. The host therefore includes the baseline capabilities needed to manage sessions and work with the workspace.

Agent sessions are not tied to the lifetime of the window for their workspace. You can close the window and reopen the session later from another window. While the Agent Host remains running, an active turn can continue without a connected client.

Connected clients can also contribute tools. For example, {% data variables.product.prodname_vscode_shortname %} can advertise tools that are provided by the client (like the browser tools) or by installed extensions. The Agent Host adds those definitions to the active session and routes a tool call back to the client that contributed it.

## Local and remote hosts

The desktop {% data variables.copilot.agents_window %} can connect to an Agent Host on the same machine or on a connected SSH, Tunnel, or WSL host. The [browser-based {% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/remote-agent-sessions.md#use-the-agents-window-in-the-browser>) connects to your development machine through a dev tunnel. The browser is a client, not the host that runs the session.

![Screenshot showing desktop and browser clients connecting to Agent Hosts. The desktop client can use a host workspace or a Dev Container, while the browser connects to a development machine through a dev tunnel.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/concepts/agent-host-deployment.svg>)

Clients display and control sessions. The Agent Host owns them. Desktop and browser clients can connect to the same tunnel host.

`feature(agent-host-dev-containers)`

For a Dev Container session, the Agent Host runs inside the project's container. The container can run on your machine or on a supported SSH, Tunnel, or WSL host, while the desktop {% data variables.copilot.agents_window %} remains on your machine. Workspace file edits and commands use the tools and dependencies inside the container, rather than those installed directly on the source host.

Dev Container execution is an environment choice, not a different harness. See the [session execution options diagram](<https://github.com/docs/agents/concepts/agent-harnesses.md#relate-execution-environments-and-code-isolation>) for how local, connected-host, container, and cloud execution relate. For setup steps, see [Run an agent session in a Dev Container](<https://github.com/docs/agents/run/agents-window.md#run-a-session-in-a-dev-container>).

Like [{% data variables.product.prodname_vscode_shortname %} Remote Development](<https://github.com/docs/remote/remote-overview.md>), the user interface stays on the client while workspace operations run close to the source code and development tools.

To run your own standalone Agent Host, use `code agent host`. By default, the command starts a server on localhost and protects it with a connection token. Use the `--tunnel` option to expose it through a dev tunnel.

## Behavior on the extension host

Agent sessions that don't run on the Agent Host run in the extension host. Existing extension-host sessions continue to run there.

There are some differences in behavior for agent sessions that run on the extension host:

| Behavior | Difference |
|----------|------------|
| Reviewing changes | Agent Host sessions apply edits directly to the session folder or worktree. Review the resulting diffs and then commit, merge, or discard the changes. Extension-host sessions track edits as pending until you keep or undo them. Learn more about [reviewing AI-generated code edits](<https://github.com/docs/agents/run/review-code-edits.md>). |
| Customizations | The Agent Host reads user-level customizations from harness-agnostic folders like `~/.copilot` and `~/.claude`. Customizations stored only in your {% data variables.product.prodname_vscode_shortname %} profile user data are a legacy location that the Copilot agent doesn't read. Learn how to [migrate agent customizations](<https://github.com/docs/agent-customization/migrate-customizations.md>). |
| Hooks | Agent Host does not define one shared hook schema for every agent. The selected Copilot, Claude, or Codex harness executes its provider hook implementation. Extension-host sessions use the Local hook implementation and Local settings. Learn how to [choose the hook implementation for a session](<https://github.com/docs/agent-customization/hooks.md#choose-the-hook-implementation-for-your-session>). |
| Autopilot | On the Agent Host, [Autopilot](<https://github.com/docs/agents/run/approvals.md#how-autopilot-works>) is an agent mode. On the extension host, it's a permission level. |
| Assisted permissions `feature(assisted-permissions)` | The [Assisted permissions](<https://github.com/docs/agents/run/approvals.md#permission-levels>) level is available only for supported Agent Host sessions and is off by default in Stable. |
| Session capabilities | Shared multi-window sessions, multiple chats per session, quick chats, and remote hosting are available only on the Agent Host. |
| Extension-provided tools | Tools from extensions are only available in chats in an editor window where the extension is running. |
| MCP configuration | The Agent Host reads harness-agnostic MCP config from `.mcp.json` (workspace) and `~/.copilot/mcp-config.json` (user). It doesn't read `.vscode/mcp.json` directly, but {% data variables.product.prodname_vscode_shortname %} forwards servers you configure in {% data variables.product.prodname_vscode_shortname %} to the Agent Host, except servers that require interactive input (for example, `${input:...}` variables). Learn more about [configuring MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md>). |

## Related resources

* [Agent Host Protocol documentation](<https://microsoft.github.io/agent-host-protocol/>)
* [Agent Host Protocol source repository](<https://github.com/microsoft/agent-host-protocol>)
* [Agents in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agents/concepts/agents.md>)
* [Remote agent sessions](<https://github.com/docs/agents/run/remote-agent-sessions.md>)
* [{% data variables.product.prodname_vscode_shortname %} Remote Development architecture](<https://github.com/docs/remote/remote-overview.md>)


</details>
