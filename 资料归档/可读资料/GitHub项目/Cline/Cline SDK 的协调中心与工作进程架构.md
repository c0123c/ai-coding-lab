# Cline SDK 的协调中心与工作进程架构

SDK 架构参考。说明 Hub、Spoke 和客户端的职责、通信、能力代理、会话持久化、多客户端接入及进程隔离；适合研究自己的 Agent 执行系统。

- 来源：Cline
- 资料类型：SDK 架构参考
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/cline/cline/commit/0adccbdb70efcd5317ee25caab71abdc0fb29393>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：面向 SDK 集成和实现，不是所有编码项目都需要采用的架构。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/architecture/hub-spoke.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline SDK 的协调中心与工作进程架构.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Hub-Spoke Architecture"
sidebarTitle: "Hub & Spoke"
description: "A local daemon coordinates sessions across clients, while spoke workers handle agent execution."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

SDK 使用中心辐射架构进行生产部署。后台守护进程（中心）协调会话状态和事件路由，分支工作线程执行 agent 循环，客户端（CLI、VS Code、JetBrains 等）通过 WebSocket 作为对等点连接。

## 为什么采用轮辐式？

单进程架构适用于简单的脚本，但当您需要时就会崩溃：

- 窗口关闭或 CLI 退出时仍然存在的会话
- 多个客户端查看和控制同一会话
- 没有客户端连接时运行的计划 agents
- 进程隔离，因此失控的 agent 不会冻结 UI

中心辐射模型通过将协调与执行分离以及两者与客户端分离来解决所有这些问题UI。

## 三种角色

#### 枢纽

每台机器的单例守护进程。协调会议、路由事件和批准、管理日程、代理客户之间的能力。不运行 agent 循环。

#### 辐条

工作进程正在运行 `@cline/core`。执行 agent 循环、调用工具、流输出。将事件报告回中心。由守护进程拥有，不属于任何客户端。

#### 客户

CLI、VS Code、JetBrains 或任何自定义应用程序。发现集线器、通过 WebSocket 注册、附加到会话、发送用户输入、接收流式事件。

保持清洁的单一规则：客户参与、辐条执行、中心协调。任何两个角色都不应该重叠。

## 通讯流程

客户端通过 WebSocket 连接到集线器。集线器路由命令并传输事件。辐条向中心报告，而不是直接向客户报告。

#### 客户端发现或启动集线器

集线器是每台机器的单例守护进程。如果未运行，`ClineCore` 会自动启动它。 Discovery 使用 `~/.cline/data/locks/hub/owners/` 处的锁定文件。

#### 客户端注册并附加到会话

客户端通过 WebSocket 发送 `client.register` 命令，宣传其功能（shell 访问、文件编辑、差异查看等）。然后它创建或附加到会话。

#### Hub分配一个分支来执行

中心生成或分配一个分支工作线程来运行 agent 循环。辐条执行工具、流式传输部分输出并处理 abort/cancel.

#### 集线器将事件扇出到所有连接的客户端

当辐条产生事件（文本增量、工具调用、使用）时，集线器将它们中继到连接到该会话的每个客户端。客户可以来来去去，而不会中断执行。

#### 客户端断开连接，会话继续运行

如果 CLI 退出或窗口关闭，辐条将继续执行。另一个客户端可以附加到同一会话并在飞行中获取事件流。

## 后端模式

`ClineCore`根据配置选择执行模式：

```typescript
const cline = await ClineCore.create({
  clientName: "my-app",
  backendMode: "auto",
})
```

|模式|行为|
|------|----------|
| `auto` |在可用时首选兼容的本地集线器，在不可用时回退到本地进程内执行。这是默认设置。|
| `hub` |需要兼容的 WebSocket 集线器。如果无法访问则抛出异常。|
| `remote` |需要显式远程 WebSocket 集线器端点。适用于集线器不在用户计算机上的部署。|
| `local` |始终使用本地进程内执行和本地 SQLite/file 存储。没有中心，没有共享会话。|

### 何时使用每种模式

使用 `local` 用于：
- 简单的脚本和一次性任务
- 测试和开发
- 不适合后台进程的环境

使用 `hub`（或 `auto`）用于：
- 客户端重启后会话保持不变
- 多个客户端共享同一会话
- 预定 agents
- 连接器集成（Telegram、Slack 等）

使用 `remote` 用于：
- 中心在服务器上运行的云部署
- 团队共享中心实例

## 能力经纪

当多个客户端连接到会话时，集线器会将功能请求路由到可以处理它们的客户端。客户在注册时宣传他们的能力。

例如，VS Code 可能注册 `open-file`、`reveal-diff` 和 `run-build` 功能。 CLI 可能会注册 `shell` 和 `run-tests`。当agent需要打开差异时，集线器将请求路由到VS Code。当需要运行 shell 命令时，集线器会路由到 CLI。

这意味着随着更多客户加入，会话会变得更加丰富。 CLI 会话稍后可以通过 VS Code 附加来丰富——可以将 IDE 选择发送到正在运行的终端会话中，或者一个客户端创建的差异可以由另一个客户端打开。

## 会话保持

会话存储有完整的对话历史记录、工具调用记录和元数据。该中心保留一个用于列出会话的 SQLite 索引，以及每个会话及其元数据和消息的文件夹。

```
~/.cline/data/
  db/
    sessions.db                  # SQLite session index
  sessions/
    [session-id]/
      [session-id].json          # Session metadata
      [session-id].messages.json # Conversation history
```

会话可以有多个参与者，每个参与者都有一个角色（`creator`、`participant`、`observer`）。会话的持续存在与任何单个客户端的生命周期无关。

## 管理中心

当 `ClineCore` 需要时，集线器会自动启动。您也可以手动管理：

```bash
cline hub start      # Start the hub daemon
cline hub stop       # Stop it
cline hub status     # Check if running
cline hub ensure     # Start if not running, return URL
cline hub drain      # Refuse new work while accepted runs finish (--off lifts it)
cline hub upgrade    # Drain the hub and restart it; does not wait for --zen or scheduled tasks
```

集线器默认侦听 `127.0.0.1:25463`（设置 `CLINE_HUB_PORT` 以更改端口）并记录到 `~/.cline/data/logs/hub-daemon.log`。

## 多客户端访问

多个客户端可以连接到同一个集线器并同时附加到同一个会话：

```bash
# Terminal 1: start a task
cline "Work on feature X"

# Terminal 2: attach to the same session from VS Code or another CLI

# Terminal 3: Telegram connector
cline connect telegram -k $TOKEN

# All three share the same hub and can access the same sessions
```

## 进程隔离

如果客户端崩溃，集线器及其正在运行的辐条不会受到影响。会话继续在分支工作线程中执行。当客户端重新连接（或连接不同的客户端）时，它会接收完整的会话历史记录并恢复实时事件流。

辐条还具有与集线器本身相距的进程边界。分支中失控的模型调用不会降低中心协调其他会话、路由批准或扇出事件的能力。


## 原文对照

<details>
<summary>展开完整原文</summary>

The SDK uses a hub-spoke architecture for production deployments. A background daemon (the hub) coordinates session state and event routing, spoke workers execute the agent loop, and clients (CLI, VS Code, JetBrains, etc.) attach as peers over WebSocket.

## Why Hub-Spoke?

A single-process architecture works for simple scripts, but breaks down when you need:

- Sessions that survive when a window closes or the CLI exits
- Multiple clients viewing and controlling the same session
- Scheduled agents that run when no client is connected
- Process isolation so a runaway agent doesn't freeze the UI

The hub-spoke model solves all of these by separating coordination from execution, and both from the client UI.

## Three Roles

#### Hub

Singleton daemon per machine. Coordinates sessions, routes events and approvals, manages schedules, brokers capabilities between clients. Does not run the agent loop.

#### Spoke

Worker process running `@cline/core`. Executes the agent loop, calls tools, streams output. Reports events back to the hub. Owned by the daemon, not by any client.

#### Client

CLI, VS Code, JetBrains, or any custom app. Discovers the hub, registers over WebSocket, attaches to sessions, sends user input, receives streamed events.

The single rule that keeps it clean: clients participate, spokes execute, the hub coordinates. No two roles should overlap.

## Communication Flow

Clients connect to the hub over WebSocket. The hub routes commands and streams events. Spokes report back to the hub, never directly to clients.

#### Client discovers or starts the hub

The hub is a singleton daemon per machine. If one isn't running, `ClineCore` starts it automatically. Discovery uses lock files at `~/.cline/data/locks/hub/owners/`.

#### Client registers and attaches to a session

The client sends a `client.register` command over WebSocket, advertising its capabilities (shell access, file editing, diff viewing, etc.). It then creates or attaches to a session.

#### Hub assigns a spoke to execute

The hub spawns or assigns a spoke worker to run the agent loop. The spoke executes tools, streams partial output, and handles abort/cancel.

#### Hub fans out events to all attached clients

As the spoke produces events (text deltas, tool calls, usage), the hub relays them to every client attached to that session. Clients can come and go without interrupting execution.

#### Client disconnects, session keeps running

If the CLI exits or a window closes, the spoke continues executing. Another client can attach to the same session and pick up the event stream mid-flight.

## Backend Modes

`ClineCore` selects the execution mode based on configuration:

```typescript
const cline = await ClineCore.create({
  clientName: "my-app",
  backendMode: "auto",
})
```

| Mode | Behavior |
|------|----------|
| `auto` | Prefers a compatible local hub when available, falls back to local in-process execution when not. This is the default. |
| `hub` | Requires a compatible WebSocket hub. Throws if one is not reachable. |
| `remote` | Requires an explicit remote WebSocket hub endpoint. For deployments where the hub doesn't live on the user's machine. |
| `local` | Always uses local in-process execution with local SQLite/file storage. No hub, no shared sessions. |

### When to Use Each Mode

Use `local` for:
- Simple scripts and one-off tasks
- Testing and development
- Environments where a background process isn't appropriate

Use `hub` (or `auto`) for:
- Session persistence across client restarts
- Multiple clients sharing the same session
- Scheduled agents
- Connector integrations (Telegram, Slack, etc.)

Use `remote` for:
- Cloud deployments where the hub runs on a server
- Team-shared hub instances

## Capability Brokerage

When multiple clients are attached to a session, the hub routes capability requests to whichever client can handle them. Clients advertise their capabilities at registration.

For example, VS Code might register `open-file`, `reveal-diff`, and `run-build` capabilities. The CLI might register `shell` and `run-tests`. When the agent needs to open a diff, the hub routes the request to VS Code. When it needs to run a shell command, the hub routes to the CLI.

This means a session gets richer as more clients join. A CLI session can later be enriched by VS Code attaching -- an IDE selection can be sent into a running terminal session, or a diff created by one client can be opened by another.

## Session Persistence

Sessions are stored with full conversation history, tool call records, and metadata. The hub keeps a SQLite index for listing sessions, and a folder for each session with its metadata and messages.

```
~/.cline/data/
  db/
    sessions.db                  # SQLite session index
  sessions/
    [session-id]/
      [session-id].json          # Session metadata
      [session-id].messages.json # Conversation history
```

Sessions can have multiple participants, each tracked with a role (`creator`, `participant`, `observer`). The session persists independently of any single client's lifecycle.

## Managing the Hub

The hub starts automatically when `ClineCore` needs it. You can also manage it manually:

```bash
cline hub start      # Start the hub daemon
cline hub stop       # Stop it
cline hub status     # Check if running
cline hub ensure     # Start if not running, return URL
cline hub drain      # Refuse new work while accepted runs finish (--off lifts it)
cline hub upgrade    # Drain the hub and restart it; does not wait for --zen or scheduled tasks
```

The hub listens on `127.0.0.1:25463` by default (set `CLINE_HUB_PORT` to change the port) and logs to `~/.cline/data/logs/hub-daemon.log`.

## Multi-Client Access

Multiple clients can connect to the same hub and attach to the same session simultaneously:

```bash
# Terminal 1: start a task
cline "Work on feature X"

# Terminal 2: attach to the same session from VS Code or another CLI

# Terminal 3: Telegram connector
cline connect telegram -k $TOKEN

# All three share the same hub and can access the same sessions
```

## Process Isolation

If a client crashes, the hub and its running spokes are unaffected. The session continues executing in the spoke worker. When the client reconnects (or a different client attaches), it receives the full session history and resumes the live event stream.

The spoke also has a process boundary from the hub itself. A runaway model call in a spoke doesn't degrade the hub's ability to coordinate other sessions, route approvals, or fan out events.


</details>
