# Qwen Code REST API 集成指南

说明以守护进程作为产品后端的路线、发现、会话、提示流、权限和工作区上下文，并提供最小集成流程。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-25](<https://github.com/QwenLM/qwen-code/commit/be1a0a1fb03d5d6a40a1c2c4674986fee9e3f7b0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/rest-api-integration.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/Qwen Code REST API 集成指南.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# REST API 集成指南

对于将 Qwen Code 放入自己的产品而不是 HTTP 的团队：运行 `qwen serve`
 作为后端并从您自己的前端驱动它。

该页面是入口点。策划的
[守护进程RESTAPI参考](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md>)涵盖了稳定的
集成表面并链接到其OpenAPI3.1合约。完整协议为
[`qwen-serve-protocol.md`](<Qwen Code 守护进程 HTTP 协议参考.md>)；内部结构是
[守护进程深入研究](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/00-index.md>)；可运行的 TypeScript 演练是 
[`examples/daemon-client-quickstart.md`](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/daemon-client-quickstart.md>)。

## 存在哪些路径

在守护进程上构建的七种方法，由一个问题分隔 - **您拥有多少
前端？**

|路径|你拥有|状态|
| ------------------------------------ | -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|守护进程 + 捆绑的 Web Shell|什么都没有——按发货时使用|今天发货（[用户指南](<Qwen Code 守护进程模式.md>)）|
|守护进程 `--no-web` + 你自己的 UI|整个前端|今天发货 — **此页**|
|守护进程 + 品牌 Web Shell|品牌，而不是代码|未建成 ([#11357](<https://github.com/QwenLM/qwen-code/issues/11357>))|
|守护进程 + 自托管 Web Shell 构建|前端构建|未建成 ([#11358](<https://github.com/QwenLM/qwen-code/issues/11358>))|
|守护进程通过 SDK `DaemonClient`|客户端代码，绝不是原始代码 HTTP|今天发货（[TS](<Qwen Code TypeScript SDK 指南.md>)、[Java](<Qwen Code Java SDK 指南.md>)）——[Python SDK](<Qwen Code Python SDK 指南.md>) 仅用于进程传输，没有守护进程客户端，因此 Python 集成驱动路径 2 优于原始 HTTP|
|通过 MCP 桥的守护进程|什么都没有——另一个agent驱动它|在 `@qwen-code/sdk` 中以 `qwen-serve-mcp` 形式发布 — 请参阅 [通过 MCP](<#通过mcp驱动守护进程>) 驱动守护进程|
|可嵌入的 Web Shell 组件|您的应用程序和服务器； QC 提供聊天 UI|工作空间内部（尚未在 npm 上）- 从 monorepo 构建，请参阅 [嵌入 Web Shell ](<#嵌入-web-shell>)|

编辑器通过 stdio 的无头 `qwen -p` 和 ACP 是单独的集成 
 路径。通道和扩展也可以通过守护进程运行；请参阅
[频道指南](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/channels/overview.md>)和
[扩展参考](<Qwen Code 守护进程 HTTP 协议参考.md#分机管理v2电汇合同>)。

### 通过MCP驱动守护进程

`@qwen-code/sdk` 发布了 `qwen-serve-mcp`，这是一个 stdio MCP 服务器，它将其 
 工具映射到正在运行的守护进程。启动 `qwen serve`，然后将此服务器添加到 Node.js 22 或更新版本上的任何 MCP
 客户端：

```json
{
  "mcpServers": {
    "qwen-serve-bridge": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "-p", "@qwen-code/sdk", "qwen-serve-mcp"],
      "env": {
        "QWEN_DAEMON_URL": "http://127.0.0.1:4170",
        "QWEN_DAEMON_TOKEN": "<your-token>",
        "QWEN_WORKSPACE_CWD": "/path/to/your/project"
      }
    }
  }
}
```

`QWEN_DAEMON_URL`默认为`http://127.0.0.1:4170`；
`QWEN_DAEMON_TOKEN`在
启用认证时携带守护进程承载令牌； `QWEN_WORKSPACE_CWD` 可以选择为 
 会话创建提供默认工作空间 — 该路径必须已在守护进程中注册（使用 `--workspace <path>` 启动 
），因为该值将逐字转发为 
`POST /session` 的 `cwd` 和未注册的路径使每个 
 会话建立工具失败并显示 `400 workspace_mismatch`；保留其未设置以
使用主工作区。这些名称由 MCP 桥读取。守护进程
通道和TUI适配器使用`QWEN_DAEMON_WORKSPACE`来覆盖其
工作空间；他们还读取`QWEN_DAEMON_URL`和`QWEN_DAEMON_TOKEN`。
保留`QWEN_BRIDGE_ALLOW_GLOBAL_SCOPE`未设置，除非MCP客户端被信任
执行受限写入。将其设置为 `true` 以准确启用：
`auto-edit`、`auto` 和 `yolo` 批准模式以及任何 `persist: true`
 批准模式写入；工作区工具切换； MCP服务器重启；和
全局范围内存和agent写入。在 `plan` 和 `default`
 之间切换以及工作区范围内存写入工作无需它。该包的
[桥README](<https://github.com/QwenLM/qwen-code/blob/main/packages/sdk-typescript/src/daemon-mcp/serve-bridge/README.md>)
列出了每个工具和编程API。

### 嵌入 Web Shell

当您的 React 应用程序应拥有页面和 
 服务器，而 Qwen Code 提供聊天 UI 时，请使用 `@qwen-code/web-shell`。该包目前是工作区内部的
 \- 还没有发布步骤将其发布到npm - 因此从
[monorepo](<https://github.com/QwenLM/qwen-code>)构建它并通过路径依赖它，
以与`packages/web-templates`相同的方式使用它：

```bash
cd qwen-code
npm ci
npm run build --workspace=@qwen-code/web-shell
```

然后在您的应用程序中：

```bash
npm install @qwen-code/sdk react react-dom
npm install file:../qwen-code/packages/web-shell
```

```tsx
import { WebShellWithProviders } from '@qwen-code/web-shell';

<WebShellWithProviders
  baseUrl="https://daemon.example.com"
  token={daemonToken}
  sessionId={sessionId}
  sessionContext={{ kind: 'standalone' }}
/>;
```

这种直接浏览器连接仅适用于如下所述的受信任的
单操作员模型：`token` 授予浏览器完整的
守护程序权限。当应用程序和守护进程使用不同源时，以`--allow-origin <application-origin>`启动
守护进程；非环回源
也需要一个不记名令牌，并且守护进程拒绝在没有承载令牌的情况下启动-
参见[启动守护进程](<#启动守护进程>)。该软件包还导出 
 提供程序，用于在多个视图之间共享一个守护进程会话，以及一个单独的 
`@qwen-code/web-shell/transcript` 入口点，用于只读转录 
 渲染。有关这些变体和道具合同，请参阅
[包README](<https://github.com/QwenLM/qwen-code/blob/main/packages/web-shell/README.md>)
。

## 设计前需要了解的两件事

**守护进程不会在进程中运行推理。**它会生成`qwen --acp`子
进程以及它们与HTTP之间的代理。它使用 `QWEN_CLI_ENTRY` 或 `process.argv[1]` 在 
 相同的 Node 二进制文件下运行 CLI 入口脚本。
嵌入节点后端必须将 `QWEN_CLI_ENTRY` 指向已安装的 Qwen CLI
入口脚本； `PATH` 上没有 `qwen` 查找。缺少的入口点将 
 显示为 `MissingCliEntryError`。

在稳定状态下有 **每个活动工作区运行时有一个子项**, 每一个都没有
会议。工作区中的每个会话都会多路传输到该子进程并共享其
进程、OAuth 状态、文件缓存和层次结构内存解析。所以故障域
是工作空间：如果子进程退出，则复用到其上的每个会话都会被破坏
一起下来。为守护进程加上每个注册的一个子进程设置容器大小
工作空间，在通道交换期间每个运行时有一个额外的子空间。
当会话必须独立失败时，运行单独的守护进程 -
`--max-sessions` 限制并发数，而不是爆炸半径。

**身份验证是单操作员。**运行时承载令牌授予
整个承载门控API，受信任的环回调用者获得完全权限
，包括作为守护程序用户执行代码。没有每最终用户主体
模型。如果您将
将此放在多用户产品后面，则您的后端拥有用户身份，并且不得
将守护程序令牌交给浏览器。容器化和多租户部署
被明确推迟 - 请参阅
[用户指南](<Qwen Code 守护进程模式.md>)中的“v0.16-alpha已知限制”。

配置的通道 webhook 入口 (`POST /channels/:channelName/webhooks/:source`)
 在承载 
 身份验证之前使用自己的 `x-qwen-webhook-secret` 身份验证；在配置通道 Webhook 源之前它是惰性的。

## 启动守护进程

在终端 1 中生成令牌一次。 shell 内置程序会打印它，以便您可以
将相同的值粘贴到每个其他终端中如下所示的隐藏提示中：

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
printf 'Copy this token to the other terminals: %s\n' "$QWEN_SERVER_TOKEN"
export DAEMON_URL=http://127.0.0.1:4170
```

终端 1 — 此命令会阻塞，因此请让它运行：

```bash
qwen serve --no-web --require-auth \
  --hostname 0.0.0.0 --port 4170 \
  --workspace /srv/project
```

在每个其他终端中，当 `read`
 提示时粘贴终端 1 打印的令牌。这将使令牌脱离 shell 历史记录和子进程 
 参数：

```bash
read -rsp 'QWEN_SERVER_TOKEN: ' QWEN_SERVER_TOKEN; printf '\n'
export QWEN_SERVER_TOKEN
export DAEMON_URL=http://127.0.0.1:4170
```

`DAEMON_URL` 是环回基数 URL 下面的每个客户端命令都使用 —export
它具有相同的值，在您运行它们的每个终端中 - 并且它匹配
`servers[0].url` 在 [OpenAPI 神器](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md>)。的
守护进程仍然绑定 `0.0.0.0` 这样远程主机就可以访问它，但不要指向
`DAEMON_URL` 在该主机上以明文形式表示：可以驱动 shell 的不记名令牌是
路径上的任何人都可以读取。通过以下方式到达非环回主机 TLS （见下文）。

`--no-web` 保留下面列出的路由，但禁用 Web Shell 资产和 
 依赖表面：在 macOS 上，`/live/*` 路由和 `/live/host` 套接字，以及在 
 每个平台 `GET /mcp-app-sandbox` 上。通过环境而不是
`--token`传递令牌，任何本地用户都可以通过`/proc/<pid>/cmdline`读取令牌。

下面的 Bash 示例通过文件描述符传递授权标头
使用外壳的 `printf` 内置的，将令牌保留在curl的参数之外。
它们需要 Bash、curl 和 jq。对于跨设备访问，终止 TLS 作为
描述于 [HTTPS / TLS 用于移动和跨设备访问](<Qwen Code 守护进程模式.md#https--tls用于移动跨设备访问>);
然后守护进程服务 `https://` 在同一端口上，因此重新导出 `DAEMON_URL`
与 `https://` 运行以下命令之前的方案。

## 集成实际使用的路由

守护进程注册的大部分内容都是为了驱动 Web Shell — git
 操作、扩展安装、工作区信任、语音、计划任务 — 以及 
 随 UI 的变化而变化。下面的子集要小一个数量级。

这些是 REST 集成所需的。将其余部分视为内部。

### 发现

|路线|目的|
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| [`GET /health`](<Qwen Code 守护进程 HTTP 协议参考.md#get-health>)             |活性探针|
| [`GET /capabilities`](<Qwen Code 守护进程 HTTP 协议参考.md#get-capabilities>) |飞行前 — 请先阅读 `workspaceCwd` 和 `policy.permission`|

### 会话生命周期

|路线|目的|
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| [`POST /session`](<Qwen Code 守护进程 HTTP 协议参考.md#post-session>)                                                                                              |创建。发送 `sessionScope: "thread"` 进行独立对话|
| [`DELETE /session/:id`](<Qwen Code 守护进程 HTTP 协议参考.md#delete-sessionid>)                                                                                    |关闭。持久会话仍然存在并且可以重新加载|
| [`POST /session/:id/load`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidload>) · [`POST /session/:id/resume`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidresume>) |恢复持久会话|
| [`POST /session/:id/heartbeat`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidheartbeat>)                                                                     |推迟闲置收割机|
| [`PATCH /session/:id/metadata`](<Qwen Code 守护进程 HTTP 协议参考.md#patch-sessionidmetadata>)                                                                     |会话元数据|
| [`POST /session/:id/model`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidmodel>)                                                                             |绑定服务内切换模型|
| [`GET /session/:id/status`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidstatus>)                                                                             |运行时状态|

### 提示和流式传输

|路线|目的|
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| [`POST /session/:id/prompt`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidprompt>)                                                                                             |提交。在 ** 准入 ** 上返回 `202`，而不是完成|
| [`POST /session/:id/cancel`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidcancel>)                                                                                             |仅取消活动提示|
| [`GET /session/:id/events`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidevents-sse>)                                                                                           |SSE 流。在**提示之前订阅**|
| [`GET /session/:id/transcript`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidtranscript>)                                                                                       |对话历史记录|
| [`GET /session/:id/context`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidcontext>)                                                                                             |顶级模型、模式和配置选项状态；虚拟子代理返回空 `state`|
| [`GET /session/:id/export`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidexport>) · [`GET /session/:id/pending-prompts`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidpending-prompts>) |导出持久记录·列出排队的提示|

令牌使用不是此表面的一部分：对于顶级会话，
`GET /session/:id/context` 返回实时模型、模式和
配置选项状态。一个 `subagent.`-前缀虚拟会话ID解析
针对其父运行时并返回空 `state` 对象。使用计数器
坐在 `GET /session/:id/context-usage`，本合同未指定的路线
— 它承载着 `session_context_usage` 功能标签并且仅被描述
在内部 [会话生命周期注释](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/08-session-lifecycle.md#context-usage-session_context_usage-capability-tag>).

### 权限

|路线|目的|
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`POST /session/:id/permission/:requestId`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidpermissionrequestid>) |回答 `permission_request`。路由到拥有会话的运行时，并且永远不会回退到主桥；不受信任的非主要所有者被拒绝，而不受信任的主要所有者则继续执行活动权限策略|
| [`POST /permission/:requestId`](<Qwen Code 守护进程 HTTP 协议参考.md#post-permissionrequestid>)                      |进程全局表单，仅连接到 ** 主 ** 工作区的桥：它 `404` 用于另一个注册运行时拥有的会话，其主体与默认 `first-responder` 策略下的失败投票相同 - 因此这里的 `404` 本身并不意味着请求已得到答复|

### 只读工作区上下文

|路线|目的|
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| [`GET /file`](<Qwen Code 守护进程 HTTP 协议参考.md#get-file>) · [`GET /file/bytes`](<Qwen Code 守护进程 HTTP 协议参考.md#get-filebytes>)                                         |读取文件或字节范围|
| [`GET /stat`](<Qwen Code 守护进程 HTTP 协议参考.md#get-stat>) · [`GET /list`](<Qwen Code 守护进程 HTTP 协议参考.md#get-list>) · [`GET /glob`](<Qwen Code 守护进程 HTTP 协议参考.md#get-glob>) |路径元数据、目录列表、glob|
| [`GET /workspace/tools`](<Qwen Code 守护进程 HTTP 协议参考.md#get-workspacetools>)                                                                                  |直播ACP孩子举报的工具；如果没有，响应将包含 `acpChannelLive: false`、`tools: []` 和 `not_started` 错误|

## 最小流量

**1。预检。** 阅读 `workspaceCwd`（这样您可以在创建时省略 `cwd`）和 
`policy.permission`（这样您就知道谁可以回答权限请求）。

```bash
curl -sH @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") "$DAEMON_URL/capabilities"
```

**2。创建会话。** 使用 `sessionScope: "thread"`，除非呼叫者打算 
 共享一个对话 — 默认 `"single"` 会创建第二个 
 相同工作空间，创建 _ 重用 _ 现有会话，通过一个队列序列化不相关的
调用者。

```bash
SESSION_JSON="$(curl -sX POST "$DAEMON_URL/session" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"sessionScope":"thread"}')" || echo "create failed (curl exit $?)" >&2
printf '%s\n' "$SESSION_JSON"
SID="$(printf '%s' "$SESSION_JSON" | jq -er '.sessionId // empty')"
export SID
: "${SID:?no sessionId in the create response}"
# → {"sessionId":"…","workspaceCwd":"/srv/project","attached":false}
```

**3。在提示之前订阅。** 在第二个终端中使用相同的命令运行它
`QWEN_SERVER_TOKEN` 和 `DAEMON_URL`，并与 `SID` 设置为 `sessionId` 步骤
2 打印：导出不会跨终端，因此重新导出令牌并
`DAEMON_URL` 在那里并设置 `SID` 对此 `sessionId` 你自己。请勿粘贴
阻止返回终端 1 — 它的 `SID=` 赋值会覆盖该值
步骤 4-6 使用。 `Last-Event-ID: 0` 从最旧的开始重播
保留事件，这是您捕获创建和创建之间触发的事件的方式
订阅——尤其是 `model_switch_failed`。在一个 **附加** （默认
`sessionScope: "single"` 重用现有会话）该事件是唯一的
表明情况不好 `modelServiceId` 被拒绝了，因为失败的是
故意不作为传播 HTTP 错误。上一个 **新鲜创造** 携带着
`modelServiceId` — 哪一步 2的身体没有—— `200` 身体还承载着
`modelApplied`, `false` 当开关被拒绝时，那就是
确定性的事件而不是有界环上的事件。一个创建
两者都不携带 `modelServiceId` 也不 `startupConfig` 没有 `modelApplied` 钥匙
它自己的——与飞行中的生成合并的一个报告生成
业主的 `modelApplied`，这就是为什么要执行的附加信号保持不变
`model_switch_failed`.

要确认启动选择而不是使用传统的尽力而为切换，
预检 `session_startup_config` 并发送 `startupConfig: { modelServiceId,
reasoningEffort? }`。仅模型创建无需推理控制； 
 显式不受支持的工作失败，并返回 `422 startup_config_rejected`，而不是 
 返回 `modelApplied: false`。启动时不写入共享默认值。

```bash
# terminal 2 — re-export what you need; shell variables do not cross terminals
# export QWEN_SERVER_TOKEN='<the token from step 1>'
# export DAEMON_URL=http://127.0.0.1:4170
SID='<sessionId from step 2>'
curl -N "$DAEMON_URL/session/$SID/events" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") \
  -H 'Accept: text/event-stream' -H 'Last-Event-ID: 0'
```

每 `data:` 行都是一行上的完整信封；信封的 `type` 与 
 的 `event:` 行匹配。

重播受到 `--event-ring-size` 和固定 8 MiB 每个订阅字节 
 预算的限制。如果流发出 `state_resync_required` 和 
`reason: "replay_budget_exceeded"`，则通过 `POST /session/:id/load`
 恢复，而不是将重放视为已完成。

**4。提示。**`202`表示已录取，未完成。通过 `promptId` 关联流上的 `turn_complete` /
`turn_error`。读取 `turn_complete` 上的 `stopReason`；读取 `turn_error` 上的 
，读取 `message` 和任何可选的 `code` / `errorKind` — 参见 
[`POST /session/:id/prompt`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidprompt>)。

```bash
curl -sX POST "$DAEMON_URL/session/$SID/prompt" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"prompt":[{"type":"text","text":"What does src/main.ts do?"}]}'
# → 202 {"promptId":"…","lastEventId":42}
```

**5。回答权限请求。**当agent想要运行一个工具_并且其
批准模式要求确认_时，它会发出`permission_request`并且转弯
会阻塞，直到有人回答或者您取消 - **默认情况下没有超时**
（`--permission-response-timeout-ms`默认为`0` =无限期等待），因此
未应答的请求将在会话的提示队列中保留一个位置，直到您
取消或关闭会话。如果流程需要，请设定自己的截止日期。

该模式是孩子自己的Qwen设置`tools.approvalMode`，从
守护进程主机和`--workspace`目录的设置解析；守护进程在生成时没有固定
任何内容。它的默认值是 `auto`，它会在不询问的情况下批准一类工具调用 
 — 这些工具根本不发布 `permission_request` — 并且仍然要求 
 提供其余的信息。不受信任的工作区文件夹被强制降至 `default`（询问）、
，这就是为什么一个部署看到这些事件而另一个部署看不到任何事件，并且 
`GET /capabilities` 报告投票调解策略而不是批准 
 模式，因此预检不会告诉您处于哪种状态。您的
集成取决于批准门控，明确固定`tools.approvalMode`并
预先决定它如何回答：自动批准可能已经生效，而无需
任何人选择它。

关于会话范围路由的答案：只要 
 正好有一个实时运行时拥有会话，它就会到达所属工作区，并且永远不会回退到主 
 桥。不受信任的非主要所有者返回`403 untrusted_workspace`； 
主要运行时不受此信任检查，因此不受信任的主要所有者
可以接受投票。未解决的所有者无法关闭，而不是对 
 错误的运行时进行投票 — `404 session_not_found`、`500 ambiguous_session_owner` 或 
`503 workspace_runtime_unavailable` 和 `Retry-After: 1`（重试；投票 
 未记录）。从`permission_request`事件中复制`data.requestId`并在投票前设置
：

```bash
export REQUEST_ID='<data.requestId>'
curl -sX POST "$DAEMON_URL/session/$SID/permission/$REQUEST_ID" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"outcome":{"outcome":"selected","optionId":"proceed_once"}}'
```

**6。关闭。** `DELETE /session/$SID` → `204`。磁盘上的会话将被保留。

## 运营

|关注|哪里|
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|并发上限|`--max-sessions`，`--max-total-sessions`；上限创造回报 `503` 和 `Retry-After`|
|速率限制|`--rate-limit` 加上每个类别的 `--rate-limit-*` 标志|
|空闲清理|`--session-idle-timeout-ms`；与`POST /session/:id/heartbeat`保持活力|
|内存|`--child-heap-mode`默认为`observe`；选择加入 `admit` 和 `enforce` 在 HTTP 503 和 `code: "acp_child_capacity_exhausted"` 的建模计数限制中拒绝新的 ACP 子项。在拒绝之前，完全托管的守护进程可以从守护进程拥有的不同的、受信任的、活动的非内部工作空间运行时回收一个最近最少使用的空 ACP，没有实时会话或挂起的工作，并重试准入一次。如果没有其他工作空间符合条件（包括单一工作空间的情况），则拒绝不变。加载的会话仍然受到保护；工作区注册和保存的历史记录在回收后仍然存在。 `--memory-budget-mb` 控制 `POST /session/:id/load` 的自适应实时日志增长池，而不是 SSE 重播；固定 `--max-journal-bytes` 或 `--max-journal-events` 会禁用生长。实验性选择加入 `enforce` 还根据建模预算设置每个托管子项的固定旧空间上限；其他模式保留旧的主机派生参数。 `limits.memory.enforced` 报告接线，而不是 RSS 保证。启用前校准工作负载；使用 `admit` 重新启动以回滚。预算计算参见[配置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/17-configuration.md>)。 SSE 重播分别受 `--event-ring-size` 和固定 8 MiB 每订阅预算的限制；省略的尾部生成 `state_resync_required` 和 `reason: "replay_budget_exceeded"`|
|及时的截止日期|`--prompt-deadline-ms`；到期发出 `turn_error`|
|错误|[错误分类](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/18-error-taxonomy.md>)|
|可观察性|[可观察性](<Qwen Code 可观测性与调试.md>)|
|完整标志列表|[配置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/17-configuration.md>)|

## 当 ACP 容量耗尽时选择工作区

当应用程序首次提交会话创建、现有会话 load/resume（包括嵌入式窗格）或显式技能运行时准备安全返回 `acp_child_capacity_exhausted` 时，如果守护程序通告 `workspace_runtime_stop`，则 Web Shell 会提供工作区选择器。取消会保留初始草稿和现有工作。选择工作区并确认会中断所有预览的会话及其工具，同时保留注册、文件和保存的历史记录。具有独立服务或已启用计划任务的工作区被禁用是有原因的。嵌入主机创建新会话仍然是主机的责任；空的 ChatPane 不会通过 sendPrompt 延迟创建会话。不需要额外的环境变量，自动回收仍然只选择空运行时。

API客户端可以读取`GET /workspaces/runtime-stop-options`，获得用户确认，并将准确的channel/epoch/token/session设置提交为`POST /workspaces/:workspace/runtime/stop`。对待未知或部分结果如下：刷新选定的收据，不要假设丢失的响应意味着没有停止，并且不要盲目地重试原始提示。 Continue 仅在 `stopped` 和 `released` 为 true 后执行一次原始操作，并且其守护进程、工作区和草稿保持最新状态。当会话关闭正在进行时预算到期的停靠点会在清理继续时返回失败的收据；未确认的释放然后会保持工作区隔离并计算其容量，因此刷新匹配的收据以观察稍后的清理，而无需再次停止 POST。刷新的收据报告 `stopped`/`released` 可能仍带有 `error` 中较早的拆卸警告 - 将 `released: true` 视为释放信号。在会话关闭之间预算耗尽的停止会返回 409 `workspace_runtime_stop_incomplete`，并且清理已结束 - 工作区不再隔离，收据不会更改，并且停止剩余会话需要重新预览和确认。有关请求字段、错误代码和接收语义，请参阅[运行时停止协议](<Qwen Code 守护进程 HTTP 协议参考.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# REST API integration guide

For teams putting Qwen Code inside their own product over HTTP: run `qwen serve`
as a backend and drive it from your own front end.

This page is the entry point. The curated
[Daemon REST API reference](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md>) covers the stable
integration surface and links to its OpenAPI 3.1 contract. The full protocol is
[`qwen-serve-protocol.md`](<Qwen Code 守护进程 HTTP 协议参考.md>); the internals are the
[daemon deep dive](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/00-index.md>); a runnable TypeScript walkthrough is
[`examples/daemon-client-quickstart.md`](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/daemon-client-quickstart.md>).

## Which paths exist

Seven ways to build on the daemon, separated by one question — **how much of the
front end do you own?**

| Path                                 | You own                                      | Status                                                                                                                                                                                                     |
| ------------------------------------ | -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| daemon + bundled Web Shell           | nothing — use it as shipped                  | ships today ([user guide](<Qwen Code 守护进程模式.md>))                                                                                                                                                         |
| daemon `--no-web` + your own UI      | the entire front end                         | ships today — **this page**                                                                                                                                                                                |
| daemon + branded Web Shell           | branding, not code                           | not built ([#11357](<https://github.com/QwenLM/qwen-code/issues/11357>))                                                                                                                                     |
| daemon + self-hosted Web Shell build | the front-end build                          | not built ([#11358](<https://github.com/QwenLM/qwen-code/issues/11358>))                                                                                                                                     |
| daemon via SDK `DaemonClient`        | client code, never raw HTTP                  | ships today ([TS](<Qwen Code TypeScript SDK 指南.md>), [Java](<Qwen Code Java SDK 指南.md>)) — the [Python SDK](<Qwen Code Python SDK 指南.md>) is process-transport-only and has no daemon client, so a Python integration drives path 2 over raw HTTP |
| daemon via MCP bridge                | nothing — another agent drives it            | ships as `qwen-serve-mcp` in `@qwen-code/sdk` — see [Drive the daemon through MCP](<#drive-the-daemon-through-mcp>)                                                                                          |
| embeddable Web Shell components      | your app and server; QC supplies the chat UI | workspace-internal (not yet on npm) — build from the monorepo, see [Embed the Web Shell](<#embed-the-web-shell>)                                                                                             |

Headless `qwen -p` and ACP over stdio for editors are separate integration
paths. Channels and extensions can also run through the daemon; see the
[channel guide](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/channels/overview.md>) and
[Extension reference](<Qwen Code 守护进程 HTTP 协议参考.md#extension-management-v2-wire-contract>).

### Drive the daemon through MCP

`@qwen-code/sdk` publishes `qwen-serve-mcp`, a stdio MCP server that maps its
tools to a running daemon. Start `qwen serve`, then add this server to any MCP
client on Node.js 22 or newer:

```json
{
  "mcpServers": {
    "qwen-serve-bridge": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "-p", "@qwen-code/sdk", "qwen-serve-mcp"],
      "env": {
        "QWEN_DAEMON_URL": "http://127.0.0.1:4170",
        "QWEN_DAEMON_TOKEN": "<your-token>",
        "QWEN_WORKSPACE_CWD": "/path/to/your/project"
      }
    }
  }
}
```

`QWEN_DAEMON_URL` defaults to `http://127.0.0.1:4170`;
`QWEN_DAEMON_TOKEN` carries the daemon bearer token when authentication is
enabled; and `QWEN_WORKSPACE_CWD` optionally supplies the default workspace for
session creation — the path must already be registered with the daemon (start
it with `--workspace <path>`), because the value is forwarded verbatim as
`POST /session`'s `cwd` and an unregistered path makes every
session-establishing tool fail with `400 workspace_mismatch`; leave it unset to
use the primary workspace. These names are read by the MCP bridge. Daemon
channel and TUI adapters instead use `QWEN_DAEMON_WORKSPACE` for their
workspace override; they also read `QWEN_DAEMON_URL` and `QWEN_DAEMON_TOKEN`.
Leave `QWEN_BRIDGE_ALLOW_GLOBAL_SCOPE` unset unless the MCP client is trusted
to perform restricted writes. Set it to `true` to enable exactly: the
`auto-edit`, `auto` and `yolo` approval modes plus any `persist: true`
approval-mode write; workspace tool toggling; MCP server restart; and
global-scope memory and agent writes. Switching between `plan` and `default`
and workspace-scope memory writes work without it. The package's
[bridge README](<https://github.com/QwenLM/qwen-code/blob/main/packages/sdk-typescript/src/daemon-mcp/serve-bridge/README.md>)
lists every tool and the programmatic API.

### Embed the Web Shell

Use `@qwen-code/web-shell` when your React application should own the page and
server while Qwen Code supplies the chat UI. The package is workspace-internal
for now — no release step publishes it to npm yet — so build it from the
[monorepo](<https://github.com/QwenLM/qwen-code>) and depend on it by path, the
same way `packages/web-templates` consumes it:

```bash
cd qwen-code
npm ci
npm run build --workspace=@qwen-code/web-shell
```

Then in your application:

```bash
npm install @qwen-code/sdk react react-dom
npm install file:../qwen-code/packages/web-shell
```

```tsx
import { WebShellWithProviders } from '@qwen-code/web-shell';

<WebShellWithProviders
  baseUrl="https://daemon.example.com"
  token={daemonToken}
  sessionId={sessionId}
  sessionContext={{ kind: 'standalone' }}
/>;
```

This direct browser connection is suitable only for the trusted,
single-operator model described below: the `token` grants the browser full
daemon authority. When the application and daemon use different origins, start
the daemon with `--allow-origin <application-origin>`; a non-loopback origin
also requires a bearer token, and the daemon refuses to start without one —
see [Start the daemon](<#start-the-daemon>). The package also exports
providers for sharing one daemon session across several views and a separate
`@qwen-code/web-shell/transcript` entry point for read-only transcript
rendering. See the
[package README](<https://github.com/QwenLM/qwen-code/blob/main/packages/web-shell/README.md>)
for those variants and the prop contract.

## Two things to know before designing

**The daemon does not run inference in-process.** It spawns `qwen --acp` child
processes and brokers between them and HTTP. It runs the CLI entry script under
the same Node binary, using `QWEN_CLI_ENTRY` or otherwise `process.argv[1]`.
An embedding Node backend must point `QWEN_CLI_ENTRY` at the installed Qwen CLI
entry script; there is no `qwen` lookup on `PATH`. A missing entry point surfaces
as `MissingCliEntryError`.

In steady state there is **one child per active workspace runtime**, not one per
session. Every session in a workspace multiplexes onto that child and shares its
process, OAuth state, file cache and hierarchy-memory parse. So the fault domain
is the workspace: if the child exits, every session multiplexed onto it is torn
down together. Size the container for the daemon plus one child per registered
workspace, with headroom for one extra child per runtime during a channel swap.
When sessions must fail independently, run separate daemons —
`--max-sessions` caps concurrency, not blast radius.

**Authentication is single-operator.** The runtime bearer token grants the
whole bearer-gated API, and a trusted loopback caller gets full authority
including code execution as the daemon user. There is no per-end-user principal
model. If you are putting
this behind a multi-user product, your backend owns user identity and must not
hand the daemon token to browsers. Containerised and multi-tenant deployment
are explicitly deferred — see "v0.16-alpha known limits" in the
[user guide](<Qwen Code 守护进程模式.md>).

Configured channel webhook ingress (`POST /channels/:channelName/webhooks/:source`)
uses its own `x-qwen-webhook-secret` authentication before bearer
authentication; it is inert until a channel webhook source is configured.

## Start the daemon

Generate the token once in terminal 1. The shell builtin prints it so you can
paste the same value into the hidden prompt shown below in every other terminal:

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
printf 'Copy this token to the other terminals: %s\n' "$QWEN_SERVER_TOKEN"
export DAEMON_URL=http://127.0.0.1:4170
```

Terminal 1 — this command blocks, so leave it running:

```bash
qwen serve --no-web --require-auth \
  --hostname 0.0.0.0 --port 4170 \
  --workspace /srv/project
```

In every other terminal, paste the token printed by terminal 1 when `read`
prompts for it. This keeps the token out of shell history and child-process
arguments:

```bash
read -rsp 'QWEN_SERVER_TOKEN: ' QWEN_SERVER_TOKEN; printf '\n'
export QWEN_SERVER_TOKEN
export DAEMON_URL=http://127.0.0.1:4170
```

`DAEMON_URL` is the loopback base URL every client command below uses — export
it, with the same value, in each terminal you run them in — and it matches
`servers[0].url` in the [OpenAPI artifact](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md>). The
daemon still binds `0.0.0.0` so a remote host can reach it, but do not point
`DAEMON_URL` at that host in plaintext: a bearer token that can drive a shell is
readable by anyone on the path. Reach a non-loopback host over TLS (see below).

`--no-web` preserves the routes listed below, but disables Web Shell assets and
dependent surfaces: on macOS the `/live/*` routes and `/live/host` socket, and on
every platform `GET /mcp-app-sandbox`. Pass the token by environment rather than
`--token`, which is readable by any local user through `/proc/<pid>/cmdline`.

The Bash examples below pass the Authorization header through a file descriptor
using the shell's `printf` builtin, keeping the token out of curl's arguments.
They require Bash, curl, and jq. For cross-device access, terminate TLS as
described in [HTTPS / TLS for mobile and cross-device access](<Qwen Code 守护进程模式.md#https--tls-for-mobile--cross-device-access>);
the daemon then serves `https://` on the same port, so re-export `DAEMON_URL`
with the `https://` scheme before running the commands below.

## The routes an integration actually uses

Most of what the daemon registers exists to drive the Web Shell — git
operations, extension install, workspace trust, voice, scheduled tasks — and
changes with that UI. The subset below is an order of magnitude smaller.

These are the ones a REST integration needs. Treat the rest as internal.

### Discovery

| Route                                                            | Purpose                                                                      |
| ---------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| [`GET /health`](<Qwen Code 守护进程 HTTP 协议参考.md#get-health>)             | Liveness probe                                                               |
| [`GET /capabilities`](<Qwen Code 守护进程 HTTP 协议参考.md#get-capabilities>) | Preflight — read `workspaceCwd` and `policy.permission` before anything else |

### Session lifecycle

| Route                                                                                                                                                 | Purpose                                                               |
| ----------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| [`POST /session`](<Qwen Code 守护进程 HTTP 协议参考.md#post-session>)                                                                                              | Create. Send `sessionScope: "thread"` for an independent conversation |
| [`DELETE /session/:id`](<Qwen Code 守护进程 HTTP 协议参考.md#delete-sessionid>)                                                                                    | Close. The persisted session survives and can be reloaded             |
| [`POST /session/:id/load`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidload>) · [`POST /session/:id/resume`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidresume>) | Restore a persisted session                                           |
| [`POST /session/:id/heartbeat`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidheartbeat>)                                                                     | Defer the idle reaper                                                 |
| [`PATCH /session/:id/metadata`](<Qwen Code 守护进程 HTTP 协议参考.md#patch-sessionidmetadata>)                                                                     | Session metadata                                                      |
| [`POST /session/:id/model`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidmodel>)                                                                             | Switch model within the bound service                                 |
| [`GET /session/:id/status`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidstatus>)                                                                             | Runtime status                                                        |

### Prompting and streaming

| Route                                                                                                                                                                   | Purpose                                                                                   |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| [`POST /session/:id/prompt`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidprompt>)                                                                                             | Submit. Returns `202` on **admission**, not completion                                    |
| [`POST /session/:id/cancel`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidcancel>)                                                                                             | Cancel the active prompt only                                                             |
| [`GET /session/:id/events`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidevents-sse>)                                                                                           | SSE stream. Subscribe **before** prompting                                                |
| [`GET /session/:id/transcript`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidtranscript>)                                                                                       | Conversation history                                                                      |
| [`GET /session/:id/context`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidcontext>)                                                                                             | Top-level model, mode, and config-option state; virtual subagents return an empty `state` |
| [`GET /session/:id/export`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidexport>) · [`GET /session/:id/pending-prompts`](<Qwen Code 守护进程 HTTP 协议参考.md#get-sessionidpending-prompts>) | Export the persisted transcript · list queued prompts                                     |

Token usage is not part of this surface: for a top-level session,
`GET /session/:id/context` returns the live model, mode, and
configuration-option state. A `subagent.`-prefixed virtual session id resolves
against its parent runtime and returns an empty `state` object. Usage counters
sit on `GET /session/:id/context-usage`, a route this contract does not specify
— it carries the `session_context_usage` capability tag and is described only
in the internal [session lifecycle notes](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/08-session-lifecycle.md#context-usage-session_context_usage-capability-tag>).

### Permissions

| Route                                                                                                   | Purpose                                                                                                                                                                                                                                                                                     |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`POST /session/:id/permission/:requestId`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidpermissionrequestid>) | Answer a `permission_request`. Routed to the runtime that owns the session and never falls back to the primary bridge; an untrusted non-primary owner is rejected, while an untrusted primary owner proceeds to the active permission policy                                                |
| [`POST /permission/:requestId`](<Qwen Code 守护进程 HTTP 协议参考.md#post-permissionrequestid>)                      | Process-global form, wired to the **primary** workspace's bridge only: it `404`s for a session owned by another registered runtime, with the same body as a lost vote under the default `first-responder` policy — so a `404` here does not by itself mean the request was already answered |

### Read-only workspace context

| Route                                                                                                                                                  | Purpose                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| [`GET /file`](<Qwen Code 守护进程 HTTP 协议参考.md#get-file>) · [`GET /file/bytes`](<Qwen Code 守护进程 HTTP 协议参考.md#get-filebytes>)                                         | Read a file, or a byte range                                                                                                        |
| [`GET /stat`](<Qwen Code 守护进程 HTTP 协议参考.md#get-stat>) · [`GET /list`](<Qwen Code 守护进程 HTTP 协议参考.md#get-list>) · [`GET /glob`](<Qwen Code 守护进程 HTTP 协议参考.md#get-glob>) | Path metadata, directory listing, glob                                                                                              |
| [`GET /workspace/tools`](<Qwen Code 守护进程 HTTP 协议参考.md#get-workspacetools>)                                                                                  | Tools reported by the live ACP child; without one, the response has `acpChannelLive: false`, `tools: []`, and a `not_started` error |

## Minimal flow

**1. Preflight.** Read `workspaceCwd` (so you can omit `cwd` on create) and
`policy.permission` (so you know who may answer permission requests).

```bash
curl -sH @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") "$DAEMON_URL/capabilities"
```

**2. Create a session.** Use `sessionScope: "thread"` unless callers are meant
to share one conversation — the default `"single"` makes a second
same-workspace create _reuse_ the existing session, serialising unrelated
callers through one queue.

```bash
SESSION_JSON="$(curl -sX POST "$DAEMON_URL/session" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"sessionScope":"thread"}')" || echo "create failed (curl exit $?)" >&2
printf '%s\n' "$SESSION_JSON"
SID="$(printf '%s' "$SESSION_JSON" | jq -er '.sessionId // empty')"
export SID
: "${SID:?no sessionId in the create response}"
# → {"sessionId":"…","workspaceCwd":"/srv/project","attached":false}
```

**3. Subscribe before prompting.** Run this in a second terminal with the same
`QWEN_SERVER_TOKEN` and `DAEMON_URL`, and with `SID` set to the `sessionId` step
2 printed: exports do not cross terminals, so re-export the token and
`DAEMON_URL` there and set `SID` to that `sessionId` yourself. Do not paste the
block back into terminal 1 — its `SID=` assignment would overwrite the value
steps 4-6 use. `Last-Event-ID: 0` replays from the oldest
retained event, which is how you catch events fired between create and
subscribe — notably `model_switch_failed`. On an **attach** (the default
`sessionScope: "single"` reusing an existing session) that event is the only
signal that a bad `modelServiceId` was rejected, because the failure is
deliberately not propagated as an HTTP error. On a **fresh create** that carries
`modelServiceId` — which step 2's body does not — the `200` body also carries
`modelApplied`, `false` when the switch was rejected, and that is the
deterministic one to act on rather than an event on a bounded ring. A create
carrying neither `modelServiceId` nor `startupConfig` has no `modelApplied` key
of its own — one that coalesced with an in-flight spawn reports the spawn
owner's `modelApplied`, which is why the attach signal to act on stays
`model_switch_failed`.

To confirm a startup selection instead of using legacy best-effort switching,
preflight `session_startup_config` and send `startupConfig: { modelServiceId,
reasoningEffort? }`. Model-only creation works without a reasoning control; an
explicit unsupported effort fails with `422 startup_config_rejected` rather than
returning `modelApplied: false`. Startup does not write shared defaults.

```bash
# terminal 2 — re-export what you need; shell variables do not cross terminals
# export QWEN_SERVER_TOKEN='<the token from step 1>'
# export DAEMON_URL=http://127.0.0.1:4170
SID='<sessionId from step 2>'
curl -N "$DAEMON_URL/session/$SID/events" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") \
  -H 'Accept: text/event-stream' -H 'Last-Event-ID: 0'
```

Each `data:` line is a full envelope on one line; the envelope's `type` matches
the `event:` line.

Replay is limited by `--event-ring-size` and a fixed 8 MiB per-subscription byte
budget. If the stream emits `state_resync_required` with
`reason: "replay_budget_exceeded"`, recover through `POST /session/:id/load`
instead of treating the replay as complete.

**4. Prompt.** `202` means admitted, not finished. Correlate `turn_complete` /
`turn_error` on the stream by `promptId`. Read `stopReason` on `turn_complete`;
on `turn_error`, read `message` and any optional `code` / `errorKind` — see
[`POST /session/:id/prompt`](<Qwen Code 守护进程 HTTP 协议参考.md#post-sessionidprompt>).

```bash
curl -sX POST "$DAEMON_URL/session/$SID/prompt" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"prompt":[{"type":"text","text":"What does src/main.ts do?"}]}'
# → 202 {"promptId":"…","lastEventId":42}
```

**5. Answer permission requests.** When the agent wants to run a tool _and its
approval mode asks for confirmation_, it emits `permission_request` and the turn
blocks until someone answers or you cancel — **by default there is no timeout**
(`--permission-response-timeout-ms` defaults to `0` = wait indefinitely), so an
unanswered request keeps holding a slot in the session's prompt queue until you
cancel or close the session. Arm your own deadline if the flow needs one.

The mode is the child's own Qwen setting `tools.approvalMode`, resolved from the
daemon host's and the `--workspace` directory's settings; the daemon pins
nothing at spawn. Its default is `auto`, which approves one class of tool calls
without asking — those publish no `permission_request` at all — and still asks
for the rest. An untrusted workspace folder is forced down to `default` (ask),
which is why one deployment sees these events and another sees none, and
`GET /capabilities` reports the vote-mediation policy rather than the approval
mode, so preflight will not tell you which posture you are in. If your
integration depends on approval gating, pin `tools.approvalMode` explicitly and
decide up front how it answers: auto-approval can already be in effect without
anyone having chosen it.

Answer on the session-scoped route: it reaches the owning workspace whenever
exactly one live runtime owns the session, and never falls back to the primary
bridge. An untrusted non-primary owner returns `403 untrusted_workspace`; the
primary runtime is exempt from this trust check, so an untrusted primary owner
may accept the vote. An unresolved owner fails closed instead of voting on the
wrong runtime — `404 session_not_found`, `500 ambiguous_session_owner`, or
`503 workspace_runtime_unavailable` with `Retry-After: 1` (retry; the vote was
not recorded). Copy `data.requestId` from the `permission_request` event and
set it before voting:

```bash
export REQUEST_ID='<data.requestId>'
curl -sX POST "$DAEMON_URL/session/$SID/permission/$REQUEST_ID" \
  -H @<(printf 'Authorization: Bearer %s\n' "$QWEN_SERVER_TOKEN") -H 'Content-Type: application/json' \
  -d '{"outcome":{"outcome":"selected","optionId":"proceed_once"}}'
```

**6. Close.** `DELETE /session/$SID` → `204`. The on-disk session is retained.

## Operations

| Concern          | Where                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Concurrency caps | `--max-sessions`, `--max-total-sessions`; over-cap creates return `503` with `Retry-After`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Rate limiting    | `--rate-limit` plus the per-class `--rate-limit-*` flags                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Idle cleanup     | `--session-idle-timeout-ms`; keep alive with `POST /session/:id/heartbeat`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Memory           | `--child-heap-mode` defaults to `observe`; opt-in `admit` and `enforce` refuse new ACP children at the modeled count limit with HTTP 503 and `code: "acp_child_capacity_exhausted"`. Before rejecting, a fully managed daemon may reclaim one least-recently-used empty ACP from a different, trusted, active non-internal workspace runtime owned by the daemon, with no live sessions or pending work and retry admission once. If no other workspace qualifies (including the single-workspace case), the rejection is unchanged. Loaded sessions remain protected; workspace registration and saved history survive reclamation. `--memory-budget-mb` controls the adaptive live-journal growth pool for `POST /session/:id/load`, not SSE replay; pinning either `--max-journal-bytes` or `--max-journal-events` disables growth. Experimental opt-in `enforce` also sets each managed child's fixed old-space ceiling from the modeled budget; other modes keep legacy host-derived arguments. `limits.memory.enforced` reports that wiring, not an RSS guarantee. Calibrate the workload before enabling; restart with `admit` to roll back. See [Configuration](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/17-configuration.md>) for budget calculation. SSE replay is separately bounded by `--event-ring-size` and a fixed 8 MiB per-subscription budget; an omitted tail produces `state_resync_required` with `reason: "replay_budget_exceeded"` |
| Prompt deadlines | `--prompt-deadline-ms`; expiry emits `turn_error`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Errors           | [Error taxonomy](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/18-error-taxonomy.md>)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Observability    | [Observability](<Qwen Code 可观测性与调试.md>)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Full flag list   | [Configuration](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/17-configuration.md>)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

## Choosing a workspace when ACP capacity is exhausted

When an App first-submit session creation, an existing-session load/resume (including embedded panes), or explicit Skills runtime preparation safely returns `acp_child_capacity_exhausted`, Web Shell offers a workspace chooser if the daemon advertises `workspace_runtime_stop`. Cancel preserves the initiating draft and existing work. Selecting a workspace and confirming interrupts all previewed sessions and their tools, while retaining registration, files and saved history. Workspaces with independent services or enabled scheduled tasks are disabled with a reason. New-session creation by an embedding host remains the host's responsibility; an empty ChatPane does not lazily create a session through sendPrompt. No additional environment variables are needed, and automatic reclamation still only selects empty runtimes.

API clients can read `GET /workspaces/runtime-stop-options`, obtain user confirmation, and submit the exact channel/epoch/token/session set to `POST /workspaces/:workspace/runtime/stop`. Treat unknown or partial outcomes as such: refresh the selected receipt, do not assume a lost response means nothing stopped, and do not blindly retry the original prompt. Continue the original operation once only after `stopped` and `released` are true and its daemon, workspace and draft remain current. A stop whose budget expires while a session close is in flight returns a failed receipt while cleanup continues; unconfirmed release then keeps the workspace isolated and its capacity counted, so refresh the matching receipt to observe later cleanup without another stop POST. A refreshed receipt reporting `stopped`/`released` may still carry the earlier teardown warning in `error` — treat `released: true` as the release signal. A stop whose budget is exhausted between session closes instead returns 409 `workspace_runtime_stop_incomplete` with cleanup already ended — the workspace is no longer isolated, that receipt will not change, and stopping the remaining sessions requires a fresh preview and confirmation. See the [runtime-stop protocol](<Qwen Code 守护进程 HTTP 协议参考.md>) for request fields, error codes and receipt semantics.


</details>
