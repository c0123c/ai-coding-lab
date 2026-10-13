# Qwen Code 守护进程 HTTP 协议参考

列出认证、错误格式、能力、工作区与会话等接口，以及 SSE 事件传递，供开发客户端集成。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/QwenLM/qwen-code/commit/4970bfa172077147adf7abc226e3d0462b179ac5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/qwen-serve-protocol.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/qwen-serve-protocol.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# `qwen serve` HTTP 协议参考

[qwen 代码守护进程设计 ](<https://github.com/QwenLM/qwen-code/issues/3803>) 的阶段 1。所有路由都位于守护程序的基础 URL（默认 `http://127.0.0.1:4170`）下。

本参考描述了 HTTP 路由和 SSE 成帧。与
有效负载合同的[类型化事件模式](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md>)、
重新同步原因和历史锚点、
[事件总线和重播指南](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/10-event-bus.md>)一起阅读以供交付和
背压，以及[现有`v1`协议和兼容性规则的功能和版本控制](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/11-capabilities-versioning.md>)
。 
[守护进程文档索引](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/00-index.md>)链接了完整的合约集。

## 认证

当守护进程使用 `--token` 或 `QWEN_SERVER_TOKEN` 启动时 — 或者两者都绑定非环回，这会生成一个临时承载并在启动时打印一次 — ** 每个正常的 API 路由，普通环回上的 `/health` 除外绑定** 必须携带：

```
Authorization: Bearer <token>
```

如果没有在环回默认值上配置令牌，则标头是可选的，并且通过主侦听器到达的请求具有完整的操作员 API 权限。工作区信任、会话所有权、`X-Qwen-Client-Id`、权限、功能、验证和资源检查仍然适用。令牌比较是恒定时间的。 401 在 `missing header` / `wrong scheme` / `wrong token` 中的响应是统一的。

**`--open-with-auth`.** 此默认关闭的 CLI 模式需要环回绑定和可用的 Web Shell。它重用正常的 `--token`-over-`QWEN_SERVER_TOKEN` 选择，或者在守护程序启动之前当该选择为空时生成编码为 base64url 的 32 随机字节。浏览器通过`#token=`接收选定的承载并将其存储在每个选项卡中；协议和中间件看到一个普通的配置令牌。已解析令牌上的片段传递密钥，而不是此标志上：任何 `--open` 启动都会将已解析的承载（配置或生成）附加到已启动的 URL 的 `#token=` 片段（本地用户可以通过 `ps` / `/proc` 看到，如启动器所警告的那样），因此是非环回绑定裸`--open`以同样的方式将其生成的承载传递给浏览器；该标志的独特贡献是环回上的令牌 _ 生成 _ 和浏览器不合格的手册 - URL 后备。只有忽略 `RunHandle.resolvedToken` 的直接嵌入式调用方以及从不启动浏览器的客户端不会收到自动凭据。不适合浏览器的环境会打印包含秘密的片段 URL 以供手动打开。 Loopback `/health` 和静态 Web Shell 资产保留下述豁免； `--require-auth`仍然是门`/health`。

通道 webhook 入口 (`POST /channels/:channelName/webhooks/:source`) 在每种模式下都与此不记名合约分开。挂载后，它会在`bearerAuth`之前注册，并与其配置的`x-qwen-webhook-secret`进行身份验证；轮换守护进程承载不会轮换 Webhook 源机密。

**`/health`豁免**（Bctum）：在环回绑定上（`127.0.0.0/8`/`localhost`/`::1`/`[::1]`）`/health`已注册BEFORE承载中间件，因此即使守护进程以 `--token` 启动，pod 内的活性探测器也不需要携带令牌。非环回将（`--hostname 0.0.0.0` 等）门 `/health` 与其他正常 API 路由绑定 — 请参阅 [`GET /health`](<#get-health>) 部分了解基本原理。

**`--require-auth` (#4175 PR 15).** 在启动时传递此标志以将“必须有令牌”规则扩展到环回。当没有令牌源解析时启动失败 - 在环回上这意味着 `--token`、`QWEN_SERVER_TOKEN` 或 `--open-with-auth`（在启动前安装其自己生成的令牌，因此 `--require-auth --open-with-auth` 启动）。快速失败是仅环回的：当配置的源都不存在并且满足标志时，非环回绑定解析它生成的临时承载。无论哪种方式，`/health` 豁免都会被删除，因此 `/health` 还需要 `Authorization: Bearer …`。

当该标志打开时，全局 `bearerAuth` 中间件将控制 ** 每个正常 API 路由 ** — 包括 `/health` 和 `/capabilities`。通道 Webhook 入口保持独立共享秘密身份验证，Web Shell 文档和资产路由保持预身份验证。因此，**未经身份验证的**客户端无法预先飞行`caps.features`以发现需要身份验证：这种情况的发现表面是**401响应正文**本身（根据[身份验证](<#认证>)部分）。 `require_auth` 功能标签是 ** 身份验证后确认 ** — 一旦客户端成功身份验证并读取 `/capabilities`，标签的存在确认守护进程已使用 `--require-auth` 启动（对于审计/合规性 UI 和SDK 客户端在设置面板中显示“此部署已强化”）。严格突变路由接受可信环回主侦听器请求、承载身份验证请求或配对本地控制请求。不受信任的无令牌嵌入仍然收到 `401 { code: "token_required", error: "…" }`；对于 `--require-auth`，全局承载中间件首先拒绝旧版 `Unauthorized` 主体。

**`--allow-origin <pattern>` (T2.4 [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>)).** 默认情况下，会阻止浏览器客户端触及守护程序跨源 - 携带 `Origin` 标头的请求返回`403 {"error":"Request denied by CORS policy"}` 因为 CLI/SDK 客户端从不发送 `Origin`，并且守护进程将其存在视为请求来自操作员未选择的浏览器上下文的标志。在使用令牌进行非环回绑定的墙之前有一个例外：同源请求（`Origin` 等于直接套接字方案加上规范化的 `Host` 权限）经过承载身份验证，并且其 `Origin` 在主侦听器 ** 上被剥离 ** — 具有有效的bearer 路由自身状态如下，缺少或无效的承载者为 `401`、**（预身份验证 Web Shell 文档除外）、`/assets/*`、`/manifest.webmanifest`、`/sw.js` 和 `/mcp-app-sandbox` 路由，这些路由在没有凭证的情况下提供服务，如所有其他模式 ** — 并且只有跨源或不匹配的 `Origin` 值保留 `403` 包络。在启动时传递 `--allow-origin <pattern>`（可重复）以安装白名单而不是隔离墙。每个模式是：

- 字面意思是`*`——承认任何起源。 **Risky**：当配置了 `*` 但没有承载令牌解析时，启动会被拒绝。守卫读取 _ 解析的 _ 令牌 - `--token`、`QWEN_SERVER_TOKEN`、`--open-with-auth` 生成的环回令牌，或者当配置的源都不存在时非环回绑定生成的临时承载 - 因此此拒绝仅环回。当 `*` 位于列表中时，引导面包屑会发出 stderr 警告。 **推荐**：在环回绑定上与`--require-auth`配对，因此`/health`也由承载者门控 - 默认情况下，它在环回上的承载中间件之前注册（因此k8s/Compose探针可以在没有令牌的情况下到达它），并且`*` 白名单使其可以从任何跨域浏览器访问。 `--require-auth` 仍保留 Web Shell 静态资产（`/`、`/assets/*`、`/manifest.webmanifest`、`/sw.js` 和 `/session/:id`、`/plugins`、`/channels`， `/live`、`/scheduled-tasks`、`/goals` 和 `/settings` 文档导航）按设计对环回进行预身份验证 - 它们安装在承载中间件之前 - 因此在 `*` 白名单下，它们仍然可以从任何跨源浏览器读取； `--no-web` 删除该表面。在非环回绑定上，承载在启动时已经是强制的，并且 `/health` 在其后面注册。正常的 API 路由是承载门控的，通道 webhook 入口保留其自己的共享秘密门，以及 Web Shell 静态资产（`/`、`/assets/*`、`/manifest.webmanifest`、`/sw.js` 和 `/session/:id`， `/plugins`、`/channels`、`/live`、`/scheduled-tasks`、`/goals` 和 `/settings` 文档导航）将保持预身份验证，除非 `--no-web` 删除它们。
- 规范的 URL 起源 — `<scheme>://<host>[:<port>]`。 **没有尾部斜杠，没有路径，没有用户信息，没有查询。**如果条目往返`new URL(pattern).origin === pattern`失败，则引导拒绝`InvalidAllowOriginPatternError`；错误消息指出了错误模式和规范形式。严格意图：静默规范化（例如修剪尾随的 `/`）会让打字错误溜走并接受不明确的输入。如果没有解析的令牌 - 生成后意味着环回绑定 - HTTP(S) 条目仅限于环回主机；非环回浏览器源需要令牌，因为否则它可以驱动完整的操作员 API，包括作为守护程序用户执行代码。显式浏览器扩展源保留其现有的无令牌本地自动化路径。启动记录授予任何无令牌允许的浏览器源的权限。

匹配的源在每个请求上接收标准 CORS 响应标头：

```
Access-Control-Allow-Origin: <echoed origin>
Vary: Origin
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE, OPTIONS
Access-Control-Allow-Headers: Authorization, Content-Type, X-Qwen-Client-Id, Last-Event-ID, X-Qwen-Event-Epoch
Access-Control-Max-Age: 86400
Access-Control-Expose-Headers: Retry-After, X-Qwen-Event-Epoch, X-Qwen-SSE-Stream-Id
```

`Access-Control-Allow-Origin` 逐字回显请求的来源（浏览器发送的小写/大写），而不是文字 `*`，即使在 `*` 模式下也是如此 — 浏览器缓存其上与 `Vary: Origin` 配对的关键响应，并且回显为在以后的版本中添加 `Access-Control-Allow-Credentials` 留下了空间，而无需模式改变。公开的标头使浏览器客户端能够遵守重试提示，保留 SSE 纪元，并关联接受的物理流。 `Access-Control-Allow-Credentials` 是今天发送的 **NOT**：配置的守护进程凭证使用 bearer-in-`Authorization`，无需 `credentials: 'include'` 即可跨域工作；可信环回权限不需要浏览器凭据。

OPTIONS 预检请求（OPTIONS 与 `Access-Control-Request-Method` 或 `Access-Control-Request-Headers`）与 `204 No Content` 加上上面的标头短路。这是传统的 CORS 模式，并且是安全的 — 预检仅确认守护进程将接受哪个 methods/headers；实际的后续请求仍然运行主机门，然后在读取或改变任何状态之前运行 bearer/listener 权限或通道 webhook 共享秘密门。来自匹配源的普通 OPTIONS 请求继续向下游流动，并附加 CORS 标头。

与白名单不匹配的源仍然会获得 `403 {"error":"Request denied by CORS policy"}` - 与默认墙相同的信封，因此已经解析墙响应的客户端不必使用特殊情况白名单部署的守护进程。拒绝路径 ** 不会 ** 发出任何 `Access-Control-*` 标头（浏览器会忽略它们，并且发出会通过标头存在间接通告允许列表大小）。

配置的模式列表是故意在 `/capabilities` 中回显的 NOT - 浏览器客户端已经知道自己的来源（毕竟，它称为守护进程），并且显示该列表将使 `/capabilities` 的未经身份验证的读者枚举每个受信任的来源（对于配置错误的部署进行有用的侦察）。 SDK 客户端通过 `caps.features.allow_origin` 标签来控制“此守护进程支持跨源浏览器点击”，而无需知道具体的来源。

环回自源请求（例如，在同一 `127.0.0.1:port` 处调用守护进程的 Web Shell）由 ** 单独的 ** Origin-strip shim 处理，该垫片运行 BEFORE CORS 中间件并删除`Origin` `127.0.0.1:port` / `localhost:port` / `[::1]:port` / `host.docker.internal:port` 标头或确切的绑定环回地址和端口。它还接受浏览器为默认端口发送的方案匹配的无端口表单：端口 80 上的 `http://host` 和端口 443 上的 `https://host`。无论 `--allow-origin` 配置如何，这些请求都会通过 - 操作员不需要列出守护程序自己的端口来使 Web Shell 工作。

## 常见错误形状

5xx 响应携带原始错误的 `code` 和 `data`（如果存在）（JSON-RPC 样式 — ACP SDK 从 agent 转发 `{code, message, data}`）：

```json
{
  "error": "Internal error",
  "code": -32000,
  "data": { "reason": "model quota exceeded" }
}
```

请求正文中返回格式错误的 JSON：

```json
{ "error": "Invalid JSON in request body" }
```

状态为 `400`。

对于未知会话 ID，返回 `SessionNotFoundError`：

```json
{
  "error": "No session with id \"<sid>\"",
  "sessionId": "<sid>",
  "code": "session_not_found"
}
```

状态为 `404`。并发关闭使用 `code: "session_closing"`。

`WorkspaceMismatchError` 对于 `POST /session`，其 `cwd` 未规范化为已注册工作区，则返回 `400`，其中：

```json
{
  "error": "Workspace mismatch: daemon is bound to \"…\"",
  "code": "workspace_mismatch",
  "boundWorkspace": "/path/the/daemon/uses/as-primary",
  "requestedWorkspace": "/path/in/the/request"
}
```

使用此功能可以在飞行前检测不匹配：从 `/capabilities` 中读取 `workspaceCwd` 并从 `POST /session` 中省略 `cwd`（它回退到主工作区），或者当公布 `multi_workspace_sessions` 时，选择 `workspaces[].cwd` 之一。

`POST /session` 超过守护进程的 `--max-sessions` 上限将返回 `503` 和 `Retry-After: 5` 标头，并且：

```json
{
  "error": "Session limit reached (20)",
  "code": "session_limit_exceeded",
  "limit": 20,
  "scope": "workspace"
}
```

当有效的守护进程范围的总上限拒绝新会话时 - `--max-total-sessions`，或 `limits.maxTotalSessions` 中描述的默认值 - 会返回相同的响应形状 `"scope": "total"`。

附加到现有会话的 NOT 计入上限，因此空闲守护进程的重新连接即使在满负荷时也能继续工作。

如果 ACP 通道初始化预算在 `newSession` 调度之前到期，则 `POST /session` 将返回 `504` 和 `Retry-After: 5`，并且：

```json
{
  "error": "AcpSessionBridge initialize timed out after 10000ms",
  "code": "init_timeout",
  "errorKind": "init_timeout",
  "retryable": true,
  "sideEffectPossible": false,
  "phase": "channel.initialize",
  "timeoutMs": 10000
}
```

`timeoutMs` — 以及 `error` 的数字后缀 — 反映守护进程配置的 `--initialize-timeout-ms` 预算（上面的值是默认值）。上面的完整安全重试形状仅针对普通会话创建而发出，其中通道初始化严格先于每个持久突变：在该路径上，`sideEffectPossible: false`字段是权威的，因为初始化先于ACP`newSession`请求，并且理解此结构化合约的客户端可以在广告延迟后重试，而不会冒重复会话的风险。

携带 `branch` 或 `worktree` 的请求，并初始化由普通创建之外的突变承载路由（例如 `POST /session/:id/branch` 和 `POST /session/:id/side-task`，其中提交的分叉可以比失败的握手更长久）出现的超时，返回与 `code: "init_timeout"` 相同的 `504`， `phase` 和 `timeoutMs` — 但 WITHOUT `Retry-After`、`retryable` 或 `sideEffectPossible`。对于那些突变结果未知的人：分支和工作树准备在通道初始化之前对 git 进行突变，并且在失败时尝试回滚是尽力而为的（失败的签出回滚将工作区留在新分支上，重试可能会出现分支已存在冲突）。将所有 5xx 突变响应分类为不明确的客户端仍然保守地失败关闭，并且应该对简化的形状应用相同的策略。当在调度恢复请求之前通道初始化超时时，`POST /session/:id/load`和`POST /session/:id/resume`表面相同减少`init_timeout``504`（`ensureChannel`阶段）；与错误列表中记录的 `session_restore_timeout` `504` 不同，此形状不包含 `Retry-After`，不包含 `retryable`，并且不安装任何栅栏，因为从未调度恢复。 `POST /session` 上的 `newSession` 调度超时是一个例外：它返回 `504`，其中包含 `code: "init_timeout"`、`retryable: true` 和预算派生的 `Retry-After`，但没有 `phase` 或`sideEffectPossible`，因为创作结果不明确。所有其他超时标签都保留通用映射。

`RestoreInProgressError` — 由 `POST /session/:id/load`、`POST /session/:id/resume` 或调用者提供的 id `POST /session` 发出，当另一个注册已拥有该 id — 返回 `409` 并且：

```json
{
  "error": "Session \"<sid>\" is already being restored via session/<resume|load>; retry session/<load|resume> after it completes",
  "code": "restore_in_progress",
  "reason": "restore_in_progress",
  "retryable": true,
  "sessionId": "<sid>",
  "activeAction": "load",
  "requestedAction": "resume"
}
```

当为已在飞行中具有 `session/resume` 的 id 发出 `session/load` 时触发（反之亦然），或者当调用者提供的 id 生成竞赛恢复方向时触发。等待至少 `Retry-After` 秒，然后重试。恢复处于活动状态时，相同操作竞争（`load` 与 `load`、`resume` 与 `resume`）会合并而不是出错。

`reason` 区分共享此代码的两个栅栏，并且 `Retry-After` 标头跟踪它：

| `reason`                     |含义| `Retry-After`                                                                                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `restore_in_progress`        |普通恢复正在运行。|`5`（匹配`session_limit_exceeded`）|
| `awaiting_abandoned_cleanup` |公共调用者已经超时（`504` 用于恢复，或 `init_timeout` 用于会话初始化），并且不可取消的 ACP 请求及其清理尚未解决。|超时操作的预算（以秒为单位） - 恢复或初始化调用者提供的 id 生成 - 限制为 `5`–`120`|

公共恢复请求由 `limits.sessionRestoreTimeoutMs` 管理（默认 60 秒）。在 `504` 之后，id 会保持隔离状态，直到迟到的 ACP 请求和清理解决，因此以普通 5 秒节奏重试的客户端将针对它无法清除的 409 进行旋转 — 遵守 `awaiting_abandoned_cleanup` 附带的预算衍生提示。

`SessionWorkspaceConflictError` — 当请求的 `cwd` 瞄准一个已注册的工作区，但相同的会话 ID 已经存在或正在由另一运行时恢复时，由 `POST /session/:id/load` 和 `POST /session/:id/resume` 发出 — 返回 `409`，其中：

```json
{
  "error": "Session \"<sid>\" is already live or restoring in another workspace runtime.",
  "code": "session_workspace_conflict",
  "sessionId": "<sid>",
  "workspaceCwd": "/requested/workspace",
  "workspaceId": "requested-workspace-id",
  "liveWorkspaceCwd": "/live/owner/workspace",
  "liveWorkspaceId": "live-owner-workspace-id"
}
```

客户端应使用所属工作区重试，或等待进行中恢复完成，然后再将 ID 恢复到其他工作区。同一工作空间恢复竞赛继续使用桥的 `restore_in_progress` / 合并行为。

当调用者尝试加载或恢复 JSONL 位于 `chats/archive/` 下的会话时，会发出 `SessionArchivedError`：

```json
{
  "error": "Session \"<sid>\" is archived. Unarchive it before loading.",
  "code": "session_archived",
  "sessionId": "<sid>"
}
```

状态为 `409`。

当会话存档或取消存档转换已针对同一 ID 进行时，会发出 `SessionArchivingError`：

```json
{
  "error": "Session \"<sid>\" is being archived or unarchived; retry later.",
  "code": "session_archiving",
  "sessionId": "<sid>"
}
```

状态为 `409` 和 `Retry-After: 5`。

## 能力

守护进程从服务功能
注册表中通告其支持的功能标签。客户端 ** 必须 ** 门 UI 关闭 `features`，而不是关闭 `mode`（根据设计 
§10）。

```
['health', 'capabilities', 'session_create', 'session_startup_config', 'session_id_override', 'session_scope_override',
 'session_load', 'session_resume', 'session_transcript',
 'unstable_session_resume',
 'session_list', 'session_catalog_batch', 'session_info', 'session_prompt', 'session_mid_turn_message_mutation',
 'session_cancel', 'session_events',
 'slow_client_warning', 'typed_event_schema',
 'session_set_model', 'client_identity', 'client_heartbeat',
 'session_permission_vote', 'permission_vote', 'workspace_mcp', 'workspace_skills',
 'workspace_skills_config_runtime',
 'workspace_providers', 'workspace_acp_preheat', 'workspace_acp_status',
 'auth_provider_install', 'workspace_memory',
 'workspace_agents', 'workspace_agent_generate', 'workspace_env',
 'workspace_preflight', 'session_context', 'session_context_usage',
 'session_supported_commands', 'session_tasks', 'session_monitor_tool_correlation', 'session_stats',
 'session_lsp', 'session_resources', 'session_status',
 'session_close', 'session_metadata', 'session_organization',
 'session_archive', 'mcp_guardrails',
 'workspace_mcp_manage', 'mcp_guardrail_events',
 'mcp_server_runtime_mutation',
 'workspace_file_read', 'workspace_file_bytes', 'workspace_file_write',
 'workspace_file_upload',
 'session_approval_mode_control', 'workspace_tool_toggle',
 'workspace_skill_settings_toggle', 'workspace_skill_settings_batch_toggle',
 'extension_batch_activation_v2', 'extension_activation_explicit_refresh',
 'extension_state',
 'workspace_settings', 'workspace_init', 'workspace_mcp_restart',
 'session_recap', 'session_generation', 'session_btw', 'session_shell_command',
 'standalone_sessions_v1', 'standalone_session_options_v1',
 'mcp_workspace_pool', 'mcp_pool_restart',
 'require_auth', 'allow_origin', 'auth_device_flow',
 'permission_mediation', 'prompt_absolute_deadline', 'writer_idle_timeout',
 'non_blocking_prompt', 'session_language', 'user_language_sync', 'session_rewind',
 'workspace_hooks', 'session_hooks', 'workspace_extensions',
 'session_branch', 'rate_limit', 'workspace_reload', 'channel_delivery',
 'multi_workspace_sessions', 'multi_workspace_session_rewind',
 'multi_workspace_session_shell', 'persistent_workspace_registration',
 'workspace_display_name', 'workspace_runtime_removal', 'workspace_runtime',
 'workspace_qualified_rest_core', 'workspace_qualified_voice',
 'workspace_qualified_memory', 'extension_management_v2', 'extension_git_credentials',
 'extension_local_path_install',
 'workspace_persisted_transcript',
 'workspace_session_export', 'workspace_archived_session_export',
 'workspace_session_live_state',
 'client_mcp_over_ws', 'cdp_tunnel_over_ws', 'browser_automation_mcp']
```

> 条件标记仅在其匹配的部署开关打开时才会出现（请参见下表）。 F3 的 `permission_mediation` 标签始终开启并带有 `modes: ['first-responder', 'designated', 'consensus', 'local-only']`，因此 SDK 客户端可以内省构建支持的集；运行时主动策略位于 `body.policy.permission`。

`session_startup_config` 在普通和独立创建中宣传可选的 `startupConfig: { modelServiceId, reasoningEffort? }`。始终预检此标记：较旧的普通创建路由可能会默默地忽略该对象。 `modelServiceId` 为必填项（1–256 个字符）；当所选型号支持时，`reasoningEffort` 接受 `none`、`default`、`low`、`medium`、`high`、`xhigh` 或 `max`。省略仅适用于模型，没有推理设置器或隐式 `default`。该对象不能与旧的顶级 `modelServiceId` 或显式单一作用域组合，并且意味着一个新线程。启动从不保存共享默认值；稍后的会话行为不变。

成功包括 `modelApplied: true` 和 `startupConfigApplied: { modelServiceId, reasoningEffort?, effectiveReasoning? }`。模型选择器是规范的。只有显式推理请求才包含两个推理字段；有效状态是 `{ state: 'disabled' }`、`{ state: 'provider-default' }` 或 `{ state: 'enabled', effort? }`（仅切换推理不费力）。无效结构为`400 invalid_startup_config`；被拒绝或未经确认的选择是`422 startup_config_rejected`。明确的独立拒绝回滚其拥有的记录并尝试在返回之前丢弃空的输出目录；无法确认删除的记录回滚和瞬时启动失败保留`standalone_creation_outcome_unknown`，而仅记录失败的目录丢弃，仍然返回明确的拒绝。 SDK 中的成功主体确认失败是协议错误，而不是采用恢复会话的指令。

`session_scope_override` 是 `POST /session` 上每个请求 `sessionScope` 字段的协商句柄（见下文）。较旧的守护进程会默默地忽略该字段，因此 SDK 客户端应在发送此标签之前预检 `caps.features`。

`session_id_override` 是 `POST /session` 和 ACP `session/new` 元数据上可选的调用者提供的 `sessionId` 的协商句柄。客户端必须在发送该字段之前确认 `caps.features` 包含此标记，因为较旧的守护进程可能会默默地忽略它。

`persistent_workspace_registration` 宣传运行时添加的工作区的持久注册。 `POST /workspaces`接受`{ "cwd": "/absolute/path", "persist": true }`；成功包括`persisted: true`。注册范围仅限于用户 Qwen 主目录下守护程序的规范主工作区，并在下次守护程序启动时恢复。省略 `persist` 会保留进程本地注册。 `GET /workspace-registrations` 列出了存储的所需集，并且 `DELETE /workspace-registrations/:id` 忘记了下次重新启动的条目，而没有热删除活动运行时。

`workspace_display_name` 在 `POST /workspaces` 上公布可选的 `displayName` 输入，通过 `PATCH /workspaces/:workspace` 更新工作区元数据，以及工作区投影中的可选显示名称字段。名称不参与查找或路由：`id` 和规范 `cwd` 仍然是唯一的选择器，并且允许重复的名称。

`workspace_runtime_removal`通过`DELETE /workspaces/:workspace`发布同步热移除。能力工作区条目添加可选的`removable`；只能删除带有 `removable: true` 的行。删除还会忘记运行时的每个持久注册别名，但绝不会删除文件、设置、记录或存档。

`workspace_runtime` 宣传 `GET /workspace/runtime/status`、`POST /workspace/runtime/ensure` 及其 `/workspaces/:workspace/runtime/...` 等效项。 `ensure` 不接受任何功能选择：它启动或重用所选可信工作区的 ACP 运行时，并返回其生命周期状态和单调运行时纪元。主要路由仅由主要运行时拥有；合格的路由仅解析选定的注册运行时，并且永远不会回退。成功的确保会更新运行时的十分钟保活窗口。状态是只读的并且从不启动子进程。并发确保共享一个物理启动。启动正在进行或失败返回可重试 `503 runtime_still_starting` 或 `503 runtime_initialization_failed`。确保观测预算到期后可以继续进行能力准备；在这种情况下，确保仍返回具有未就绪功能状态的实时运行时以进行状态轮询。仅当所有活动运行时桥提供权威生命周期快照时，才会公布该功能；选定的遗留注入桥返回 `501 workspace_runtime_not_supported` 而不是猜测的状态或纪元。

`workspace_runtime_stop` 独立通告用户确认的运行时停止。 `GET /workspaces/runtime-stop-options` 是只读的进程全局预览：它包括已提交的子项计数、建模的子项上限以及每个可见工作区的会话、阻止原因、通道 ID、纪元、当前停止令牌和最新收据。普通加载的会话可能会被显式停止；独立的 ACP 客户端、启用的计划、挂起的 scheduler/management/start 工作、内存任务、工作人员、语音和特殊用途运行时被阻止。 `POST /workspaces/:workspace/runtime/stop` 使用严格的变异门并且仅使用选定的可信运行时。发送预览中的 `confirmInterruptions: true`、`expectedChannelId`、`expectedRuntimeEpoch`、`expectedStopToken` 和准确的 `expectedSessionIds`。永远不要回退到主运行时。预览和每个收据将运行时的实时 ACP 通道列为 `channels`（`channelId`、`runtimeEpoch`，可选 `executionEngine`）；运行时每个执行引擎可以有一个通道，并且停止涵盖所有通道。 `channelId` 命名第一个列出的频道，`runtimeEpoch` 是最新列出的纪元，因此预览后启动的频道会使确认过时。

在确认会话关闭并且每个列出的子进程的进程注册表释放后，成功的响应具有 `state: "stopped"`、`stopped: true` 和 `released: true`。注册、文件和保存的历史记录仍然存在。响应包括受影响的、中断的、关闭的和剩余的会话 ID。过时或被阻止的确认为 HTTP 409 (`workspace_runtime_stop_stale` / `workspace_runtime_stop_blocked`)；部分平仓为 409 `workspace_runtime_stop_incomplete`；正在进行的拆解或未经过验证发布的失败拆解是503 `workspace_runtime_stop_in_progress` / `workspace_runtime_stop_failed`；不支持的管理是501 `workspace_runtime_stop_not_supported`。稍后证明注册表发布的不干净拆解可能会返回 200 并带有错误警告；未经确认的会话刷新仍返回 partial/error 结果。总 observation/close 预算为 60 秒。如果它在会话关闭仍在进行时过期，则响应以 `state: "failed"` 和 `503 workspace_runtime_stop_failed` 结算；清理工作将单独进行。即使调用者收到失败，所选工作区仍保持隔离，并且其子工作区仍保持计数状态，直到实际清理完成为止。只读选项可以在确认关闭和验证发布后报告 `stopped`，或者在未确认持久性时报告 `incomplete`；已经返回的失败快照不会改变。如果预算在会话关闭之间耗尽，则响应以 `state: "incomplete"` 和 `409 workspace_runtime_stop_incomplete` 解决：不再启动进一步的关闭或强制终止，释放停止的准入守卫（幸存的子进程仍像往常一样计数），并且停止剩余会话需要重新预览和确认。强制终止剩余会话不能绕过刷新拒绝。仅根退出或全局进程计数下降并不能确定所选子进程的释放。在 Windows 上，注册表不跟踪后代进程组，根的确定退出是发布证据，因此 `released: true` 并不能证明进程树已消失。

重复确认最新消耗的令牌会观察到相同的操作。新的确认可以重试不完全停止，轮换令牌；较旧的消耗令牌就会变得陈旧。如果响应丢失，请读取选项并匹配工作区的最新收据并停止令牌，而不是停止另一个目标。删除、信任更改或重新创建 daemon/bridge 后，收据可能不可用；缺席会使结果未知，并且永远不会授权继续。 SDK `runtimeStopOptions()` 和 `workspaceById(id).stopRuntime(confirmation)` 即使使用 ACP 传输也使用 REST，并且永远不会自动重复 POST。会话订阅者收到`session_closed`和`reason: "client_close"`和`cause: "workspace_runtime_stop"`；与致命停止相关的退出另外标记 `persistenceUnconfirmed: true`。更新的 Web Shell 客户端保留已保存的 selection/transcript 并等待显式恢复。这是一次性停止，而不是持续暂停；错过活动的客户可能会再次争夺容量。

运行时状态并确保响应使用此形状：

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "state": "active",
  "runtimeLive": true,
  "runtimeEpoch": 4,
  "capabilities": {
    "skills": {
      "state": "ready",
      "revision": 2,
      "runtimeEpoch": 4
    }
  }
}
```

当工作区运行时具有多个执行引擎时，`runtimeLive` 和 `runtimeEpoch` 描述提供工作区控制的通道（MCP 和技能）：当该通道处于活动状态时，另一个引擎启动或退出不会更改它们。 `state` 将该频道报告为 `cold`、`starting` 或 `stopping`，但该频道未直播； `active` 和 `idle` 计算每个引擎的工作量。

`capabilities.skills.state` 是 `not_started`、`starting`、`ready`、`stale` 或 `error`；故障包括`capabilities.skills.error`中的`{code, message}`。仅当顶级和能力 `runtimeEpoch` 值匹配时，技能目录才是最新的。 `revision` 命令在一个运行时时期内进行技能准备，并且不得跨时期进行比较。

`workspace_skills_config_runtime` 通告 `/workspace/{config,runtime}/skills` 和 `/workspaces/:workspace/{config,runtime}/skills` 下的拆分技能读取和配置突变。 Web Shell 使用这些路由进行技能管理，并在编写新会话时，立即从配置中填充斜杠命令，然后将其替换为匹配的纪元运行时目录。当该功能不存在时，客户端必须继续使用旧版技能路由，并且不得仅为技能调用运行时确保。

`session_load`和`session_resume`通告显式恢复路由（`POST /session/:id/load`和`POST /session/:id/resume`）。较旧的守护进程会为这些路径返回 `404`，因此 SDK 客户端应在调用之前预检 `caps.features`。 `unstable_session_resume` 仍被宣传为已弃用的别名，以与发布的 SDK 兼容，而底层 ACP 方法被命名为 `connection.unstable_resumeSession`；新客户应选择`session_resume`。

`limits.sessionRestoreTimeoutMs`（如果存在）是底层 ACP `loadSession` / `unstable_resumeSession` 请求的守护进程的挂钟预算。它是一个附加的 v1 字段。 TypeScript SDK 为守护进程提供 10 秒的客户端余量，Web Shell 看门狗为其提供 15 秒；与旧守护进程通信的客户端应分别使用 70 秒和 75 秒。

`session_transcript` 通告 `GET /session/:id/transcript`，这是持久活动会话 JSONL 上的只读分页重播视图。它与 `/load` 分开：它不附加客户端、播种实时 EventBus、创建实时会话或更改实时重播窗口。当客户端需要长时间会话的完整磁盘记录时，应使用它，并仅在冷 UI 恢复期间继续使用 `/load` 进行有限实时重播。

`workspace_persisted_transcript` 通告 `GET /workspaces/:workspace/session/:id/transcript`，这是一个守护进程本地持久化寻呼机，不会启动 ACP、查询实时网桥状态、加载设置、发现项目功能或创建旧的持久化 cursor 密钥。该标签是无条件的，因为受信任的单工作空间主节点可以使用复数路由；每个工作空间的信任授权仍然根据每个请求进行评估。已注册的不受信任的辅助工作空间可以读取，而不受信任的主要工作空间仍被拒绝。

`workspace_session_export` 公布 `GET /workspaces/:workspace/session/:id/export`，这是所选工作区的活动持久会话的仅可信完整导出。它独立于 `session_export` 和 `workspace_qualified_rest_core`：已发布的守护进程可以在不实现复数路由的情况下通告两个旧标签，因此客户端必须直接预检此标签。该标记是无条件的，因为受信任的单工作区主节点可以通过 id 或 cwd 使用路由。导出无法解析实时所有者、启动 ACP、附加客户端或回退到另一个工作区。

`workspace_archived_session_export` 宣传 `GET /workspaces/:workspace/session/:id/archive/export`，这是来自选定工作区的存档持久存储的仅可信完整导出。独立于`workspace_session_export`和`workspace_qualified_rest_core`；客户必须直接预检此标签。不同的路由可防止较旧的守护进程忽略存档意图并返回具有相同 ID 的活动记录。

`workspace_session_live_state` 宣传 `GET /workspaces/:workspace/sessions/live-state`，这是所选工作区运行时的实时会话的仅可信、仅内存快照，以及内存中目录版本，该版本告诉客户端何时需要重新加载完整的持久目录。它独立于 `workspace_qualified_rest_core`：已发布的守护进程可以在不实现此路由的情况下宣传更广泛的工作区 REST 功能，因此客户端必须直接预检此标签。该标记是无条件的，因为受信任的单工作区主节点可以通过 id 或 cwd 使用路由；每个工作空间的信任检查仍然适用于每个请求，并且路由不会将允许的不可信辅助持久目录读取策略扩展到实时桥接状态。 tag表示端点存在；它不承诺每个活动项目都带有可选的 `updatedAt` 活动水印，该水印与生命周期相关。

可选的顶级 `/capabilities` 字段 `sessionLiveStatePollIntervalMs` 通告守护程序范围的实时状态轮询间隔（以毫秒为单位）。它从启动环境变量 `QWEN_SESSION_LIVE_STATE_POLL_INTERVAL_MS` 解析一次，独立于工作区环境覆盖。接受从 `1000` 到 `2147483647` 的整数值；缺失或无效值使用 `5000`。 Web Shell 使用此提示来进行所有工作区实时状态轮询，并且对于来自较旧或不兼容的守护进程的不存在或无效字段，还会回退到 `5000`。此字段不会更改路由的快照语义、立即 local/visibility 刷新或完整目录轮询。 SDK 客户端仍然对自己的计时器负责。

`slow_client_warning` 涵盖 SSE 背压行为：(a) 当订户的实时帧积压或实时序列化字节积压超过 75% 时，守护进程发出 `slow_client_warning` 合成事件流帧，每个溢出事件一次（在两个测量值都低于 37.5% 后重新准备）； (b) `GET /session/:id/events` 接受 `?maxQueued=N` 查询参数（范围 `[16, 2048]`）来预先调整每个订阅者帧积压的大小，以便针对大型重播环进行冷重新连接。序列化字节上限是守护进程拥有的（每个订阅者默认为 **2 MiB**），仅实时使用，并且有意没有查询参数。守护进程范围的环大小由 `--event-ring-size` 控制（默认为 **8000**，根据 #3803 §02）。旧守护进程默默地缺乏 warning/query 行为 - 在选择加入之前预检此标签。

`typed_event_schema` 通告与 SDK 的 `KnownDaemonEvent` 架构匹配的守护程序事件负载。较旧的守护进程可能仍会传输兼容的帧，但 SDK 客户端应在假设类型化事件覆盖之前预先检查此标记。

`client_heartbeat` 为 `POST /session/:id/heartbeat` 做广告。较旧的守护进程返回 `404`；在发出定期心跳之前预飞行此标签。

`session_close` 和 `session_metadata` 为 `DELETE /session/:id` 和 `PATCH /session/:id/metadata` 做广告。较旧的守护进程返回 `404`；在公开关闭或重命名可供性之前预检这些标签。

`session_organization` 公布自定义会话组和固定。它添加了 `GET/POST/PATCH/DELETE /workspace/:id/session-groups`、`PATCH /session/:id/organization` 和选择加入的组织列表视图 `GET /workspace/:id/sessions?view=organized`。当 `session_organization` 和 `workspace_qualified_rest_core` 都被公布时，工作区限定的组织突变 `PATCH /workspaces/:workspace/session/:id/organization` 也可用。遗留突变仍然仅限于主工作空间。较旧的守护进程为 mutation/group 路由返回 `404`，并忽略组织的视图契约，因此 WebShell/SDK 客户端必须在显示匹配分组或固定 UI 之前预先检查这些标签。

`session_archive` 通告 v1 目录状态存档 API：`POST /sessions/archive`、`POST /sessions/unarchive` 和 `GET /workspace/:id/sessions?archiveState=active|archived`。存档的会话在取消存档之前无法加载或恢复。 `session_storage_conflict_repair` 通告如下所述的附加 `resolveConflicts` 请求选项和 `resolvedConflicts` 响应存储桶。

`workspace_qualified_rest_core`在`/workspaces/:workspace/...`下通告多个核心REST路由。选择器首先解析为精确的工作区 ID，然后在规范化后解析为 URL- 编码的绝对 cwd。较新的单工作空间守护进程包括 `workspaces[]` 中的主运行时，即使 `multi_workspace_sessions` 不存在，也允许客户端发现工作空间限定路由所需的 ID；对于省略数组的旧守护程序，客户端应回退到 `capabilities.workspaceCwd`。已注册的不受信任的工作空间可以使用信任状态和信任请求路由；文件读取路由遵循现有的文件系统读取策略。注册的不受信任的辅助工作区还公开仅持久会话和会话组目录：这些读取不会附加到会话、启动 ACP 或合并实时桥接状态。文件写入、目录突变和其他复数核心路由需要受信任的工作空间，除非单独的功能记录了更窄的策略：`workspace_persisted_transcript`（只读），或信任授予突变，无论是否公布`workspace_trust_grant`，该信任授予突变都会被注册的不受信任的工作空间接受 - 该标签仅报告信任热重载是否已连接，因此记录的决策可以在不重新启动守护程序的情况下到达正在运行的运行时；它从不报告该路线是否有服务。不受信任的主节点继续从复数目录和转录路由接收 `403 { code: "untrusted_workspace" }`；传统的单一主路由保持其现有的兼容性行为。此标签涵盖核心文件、状态、设置、权限、信任、生命周期、MCP 控制、工具和技能切换、内存、工作区 agent CRUD 和会话存储表面。它不涵盖身份验证、语音、扩展、ACP/WebSocket 传输、通道工作人员路由或工作区限定的会话导出；单独飞行前 `workspace_session_export` 或 `workspace_archived_session_export`。工作空间信任不是 ACL：持有守护程序令牌的客户端可以读取此策略允许的每个已注册工作空间表面。

`workspace_qualified_voice` 通告受信任工作区运行时选择的语音路由：`GET` 和 `POST /workspaces/:workspace/voice`、`POST /workspaces/:workspace/voice/transcribe` 和 `WS /workspaces/:workspace/voice/stream`。仅当多工作区运行时和共享 ACP/Voice WebSocket 侦听器均启用时才会公布。选择器遵循与其他复数路由相同的 id-or-encoded-absolute-cwd 规则。对于REST，未知选择器返回`400 { code: "workspace_mismatch" }`，不受信任选择器返回`403 { code: "untrusted_workspace" }`； WebSocket 升级拒绝会公开相应的 HTTP 400/403 状态，而没有结构化的 JSON 信封。这两种传输方式都不会退回到主要传输方式。旧版 `/workspace/voice`、`/workspace/voice/transcribe` 和 `/voice/stream` 仍仅为主。客户端将 `workspace_qualified_voice` 用于所有合格的语音模式，并让选定的运行时报告特定于配置的错误。旧版 `workspace_voice`、`workspace_voice_transcription` 和 `voice_transcribe` 标记仅描述主要绑定路由，不得隐藏合格的辅助配置。

`workspace_qualified_memory` 通告工作区限定的托管内存路由：`POST /workspaces/:workspace/memory/{remember,forget,dream}` 将任务排队并 `GET /workspaces/:workspace/memory/{remember,forget,dream}/:taskId` 读回它们。仅当 ACP HTTP 和多工作区运行时均启用时才会公布。选择器遵循与其他复数路由相同的 id-or-encoded-absolute-cwd 规则。每个注册的工作空间都有自己的任务通道；主通道的合格通道与单个 `/workspace/memory` 表面相同，因此在一个表面上排队的任务在另一个表面上是可读的。分辨率严格按照选定的运行时进行，没有主要回退：未知选择器返回 `400 { code: "workspace_mismatch" }`，不受信任的选择器返回 `403 { code: "untrusted_workspace" }`，不活动或耗尽运行时返回 `503 { code: "workspace_runtime_unavailable" }`。读取永远不会分配通道，因此轮询没有任务的工作区会返回 `404 { code: "<kind>_task_not_found" }`。任务 ID 的范围仅限于其通道，并且不会在工作区重新配置或运行时替换后继续存在；过时的 ID 返回 `404`，而不是数据丢失情况。当 ACP HTTP 禁用时，不会通告标签，并且非主要合格请求返回不可重试的 `501 { code: "workspace_memory_unavailable" }`，而主要合格路由继续通过本地拥有的通道工作。

`session_lsp` 通告 `GET /session/:id/lsp`，即守护进程客户端的只读结构化 LSP 状态快照。较旧的守护进程返回 `404`；在公开远程 LSP 状态之前预检此标签。

`session_resources` 公布 `GET /session/:id/resources`，一对只读的已清理技能和根据所选实时会话的配置构建的 MCP 快照。该路由的范围是实时会话所有者：它永远不会回退到主运行时，也不会从工作区状态推断会话的资源。嵌套的 `skills` 和 `mcp` 对象重用相应的工作区状态负载，但省略 MCP 身份验证、池、工作区预算和工作区发现错误丰富。所选会话的 MCP 管理器报告的状态、发现和记帐仍然存在。较旧的守护进程返回 `404`；在显示会话资源目录之前预检此标签。

`session_status` 通告 `GET /session/:id/status`，即按 ID 的单个会话的实时桥接摘要。除了 `clientCount` 和 `hasActivePrompt` 之外，实时会话还会公开 `isWaitingForPermission`、`isWaitingForUserQuestion`、`pendingInteractionCount` 以及失败回合后保留的 `turnError`。当下一个提示实际开始时，错误会清除。在当前桥中已解决运行转弯的实时会话还带有 `updatedAt`，与实时状态路线下记录的相同活动水印；因为此路由直接返回网桥摘要，所以该值不会与持久记录的 mtime 合并，并且可能早于会话列表报告的值。单会话状态响应和工作区会话列表都包括 `turnError` 和 `pendingInteractions`：渲染就绪权限操作或 `ask_user_question` 问题以及 `requestId` 和现有权限投票路由所需的可选选项。每个用户问题都有一个`answerKey`；使用 `answers` 进行投票，例如 `{ "0": "Polling" }`，以该值作为键控。仅持久会话会忽略运行时状态，因为不存在运行时。较旧的守护进程返回 `404`；在轮询单个会话的状态之前预检此标签，而不是扫描完整的会话列表。

`session_info` 为 `GET /workspace/:id/session-info` 及其双胞胎 `/workspaces/:workspace/session-info` 做广告。该响应聚合持久的活动和存档会话计数，而不需要补充列表元数据。这是显式的 O(n) 磁盘扫描，不能进行轮询；客户应将 `truncated: true` 视为下限结果。

`session_approval_mode_control`、`workspace_tool_toggle`、`workspace_skill_settings_toggle`、`workspace_skill_settings_batch_toggle`、`extension_batch_activation_v2`、`workspace_init` 和 `workspace_mcp_restart` 通告下面记录的突变控制路由。批准模式控制保留其非严格兼容性门。其他控制由操作员权限严格控制：可信环回主、承载验证或配对本地控制请求通过。在没有可信环回权限的情况下到达严格门的无令牌主请求将返回 401 `token_required`；丢失或无效的配置凭据和未配对的本地控制凭据会被承载中间件以简单的 `401 Unauthorized` 提前拒绝。缺少这些路由之一的守护进程将返回 `404`。特定于设置的技能标签是不同的：来自退休标签一代的守护进程广告 `workspace_skill_toggle` 和 `workspace_skill_batch_toggle` 并在相同的路径上提供其目录验证的合同。退役的单目标路由可以返回HTTP`404 skill_not_found`或`409 skill_not_toggleable`；停用的批处理路由返回 HTTP 200 并将目录派生的故障放置在 `errors[]` 中。在公开其可供性之前对每个标签进行预飞行，并且不要通过探测路线可达性来推断特定于设置的技能合同。路由路径和请求主体没有改变。

`mcp_guardrails`（问题[#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14）涵盖MCP预算面：`clientCount` / `clientBudget` / `GET /workspace/mcp` 上的 `budgetMode` / `budgets[]` 字段、每个服务器单元上的 `disabledReason` 字段以及 `--mcp-client-budget` / `--mcp-budget-mode` CLI 标志。较旧的守护进程完全忽略新字段； SDK 客户端在依赖 `budgets[]` 语义之前预检此标签。注册表描述符还带有 `modes: ['warn', 'enforce']`，用于将来的功能模式公开 - 目前，客户端从快照的 `budgetMode` 字段推断模式。 `enforce`模式下的服务器拒绝由`Object.entries(mcpServers)`声明顺序确定；未来的范围优先级层（如果 qwen-code 采用该层）会将其转变为“最低优先级优先”，以反映 claude-code 的 `plugin < user < project < local` 约定。

> **范围是能力驱动的。**使用`mcp_workspace_pool`，一个工作区运行时内的会话共享一个传输池和`WorkspaceMcpBudget`，并且快照发出`budgets[0].scope: 'workspace'`。不同的工作空间运行时拥有独立的池。如果没有标签，每个 ACP 会话都会使用其旧版 `McpClientManager`，快照会发出 `scope: 'session'`，并且 N 个会话可能各自消耗配置的上限。

`workspace_file_read` 覆盖 text/list/stat/glob 工作区文件路由 
(`GET /file`、`GET /list`、`GET /glob`、`GET /stat`)。 `workspace_file_bytes`
 涵盖了后来添加的 `GET /file/bytes`，以便客户端可以针对 PR19- 时代守护进程预先提供原始 
 字节窗口支持。 `workspace_file_write`覆盖
哈希感知文本突变路由（`POST /file/write`，`POST /file/edit`）。
写标签表示路由契约存在；这并不意味着当前的
部署对匿名突变开放。 Write/edit为严格变异
路线，需要操作员权限；无令牌可信环回符合条件。
`workspace_file_upload` 覆盖 `POST /file/upload`，二进制入口路由：
an `application/octet-stream` 主体上限为 `MAX_UPLOAD_BYTES` (50 MiB)是
写入工作区而不覆盖 - 占用的名称是
自动编号（`name (1).ext`，`name (2).ext`，...）。这也是一条严格的
突变路线。

当公布 `workspace_qualified_rest_core` 时，相同的文件表面也可用于 `/workspaces/:workspace/file`、`/workspaces/:workspace/file/bytes`、`/workspaces/:workspace/stat`、`/workspaces/:workspace/list`、`/workspaces/:workspace/glob`、`/workspaces/:workspace/file/write`、`/workspaces/:workspace/file/edit` 和`/workspaces/:workspace/file/upload`。

同一标签还在 `/workspaces/:workspace/agents` 和 `/workspaces/:workspace/agents/:agentType` 处公开工作区限定项目 - agent CRUD。这些复数路径仅读取或改变所选工作空间的项目级 agents； `global` 和 `user` 范围请求返回 `400 { code: "global_scope_not_supported_for_workspace_route" }`。无工作空间 `/workspace/agents` 路由保留其现有的主工作空间行为，并仍然是用户级 agent 范围的唯一 REST 表面。

`extension_management_v2` 在 `/extensions/*` 处宣传用户级扩展目录和突变表面，以及在 `/workspaces/:workspace/extensions/*` 处的工作区激活投影。工件是全球性的；工作区路由仅公开投影读取、精确激活覆盖和运行时刷新。读取可能针对不受信任的已注册工作区，而激活、刷新和工作区范围内的安装则需要受信任的目标。慢速突变使用 `/extensions/operations/:operationId` 处的守护进程本地操作；存储生成（而不是操作历史记录）在重新启动和跨守护进程中具有权威性。已发布的 `workspace_extensions` 功能和 `/workspace/extensions/*` 路由仍然是主工作区兼容性适配器。客户端必须预检 `extension_management_v2`，并且不得从守护进程模式或 `workspace_qualified_rest_core` 推断它。

`extension_git_credentials` 通告经过身份验证的 HTTPS Git 安装在 `POST /workspace/extensions/install` 和 `POST /extensions/install` 上。客户端在发送 URL userinfo 或 `credentialPersistence` 之前必须预检此标签；较旧的守护进程拒绝 URL 凭据。该标签描述了后端协议支持，而不是钥匙串的可用性：存储模式在终端操作结果中报告所选后端。

`extension_local_path_install` 在 `POST /workspace/extensions/install` 和 `POST /extensions/install` 上通告守护程序本地扩展源。 `source` 必须是守护程序主机上存在的绝对路径。相对路径仍然不受支持，因此守护进程 cwd 无法更改源标识或隐藏 GitHub `owner/repo` 简写。现有安装操作将扩展复制到托管存储中；它没有链接来源。客户端必须预检此标签，因为较旧的守护程序拒绝本地源。

`extension_batch_activation_v2` 添加了 `PUT /extensions/activation` 和 `PUT /workspaces/:workspace/extensions/activation`。两者都接受 `extensionNames` 中的 1–100 名称，不区分大小写地重复删除它们，同时保留第一次看到的顺序，在一代中保留更改的目标，并返回一个 `202` 操作句柄。设置 `enabled` 或 `disabled` 时不需要安装目标：其名称创建一个所需状态声明，在安装具有该名称的扩展时会保留该声明。全局路由接受`state: "enabled" | "disabled"`并写入V2`defaultActivation`；工作区路由还接受 `"inherit"` 并应用或清除所选可信运行时的精确覆盖。 `inherit`没有声明未知名称，并且完全未知的明确报告`updated: false`。

`extension_activation_explicit_refresh` 表示单个和批量激活操作在持久策略提交后完成，而不直接刷新活动会话。需要立即应用的调用者应等待激活成功，然后提交同步主工作空间 `POST /workspace/extensions/refresh`（直接返回刷新计数）或所选工作空间的异步 `POST /workspaces/:workspace/extensions/refresh`（返回单独的操作句柄）。这两种形式不可互换：异步操作在运行时协调完成时记录所应用的生成，而同步路由则不记录任何内容，因此生成协调器仍将该工作区视为待处理，并在下一次传递时再次刷新其会话。可以寻址特定工作区的调用者应该更喜欢异步形式。刷新失败不会回滚或降级激活结果。没有此功能的守护程序已在激活中包含运行时刷新，因此兼容性客户端不得提交第二次刷新。独立的 30-第二代协调器保持启用状态，并通常在下一次传递时应用已提交的策略；失败的协调将由后续通道重试。

### 分机管理V2电汇合同

所有路由都使用上面的守护进程承载身份验证规则。 `X-Qwen-Client-Id`对于V2突变途径是可选的；当提供时，它必须标识向突变的目标工作空间运行时之一注册的客户端。 `:extensionId` 是小写的 64-十六进制扩展标识。 `:workspace` 首先解析为精确的工作区 ID，否则在规范化后解析为 URL- 编码的绝对 cwd。

|方法及路径|成功|
| ------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| `GET /extensions`                                                  |`200` 全局工件目录|
| `PUT /extensions/activation`                                       |`202`全局默认激活批量操作|
| `PUT /extensions/:extensionId/activation`                          |`202`全局默认激活操作|
| `POST /extensions/install`                                         |`202`安装操作|
| `POST /extensions/check-updates`                                   |`202`更新检查操作|
| `POST /extensions/:extensionId/update`                             |`202`更新操作|
| `DELETE /extensions/:extensionId`                                  |`202`卸载操作，或当扩展不存在时幂等`204`|
| `GET /extensions/operations/:operationId`                          |`200`操作快照|
| `GET /workspaces/:workspace/extensions`                            |`200`工作区激活投影|
| `GET /workspaces/:workspace/extensions/:extensionId/state`         |`200` 工作区资源状态 (`extension_state`)|
| `PUT /workspaces/:workspace/extensions/:extensionId/state`         |`202` 工作区资源状态操作 (`extension_state`)|
| `PUT /workspaces/:workspace/extensions/activation`                 |`202`精确工作区激活批量操作|
| `PUT /workspaces/:workspace/extensions/:extensionId/activation`    |`202` 精确的工作区激活操作|
| `DELETE /workspaces/:workspace/extensions/:extensionId/activation` |`202` 清除-覆盖操作|
| `POST /workspaces/:workspace/extensions/refresh`                   |`202`运行时刷新操作|

#### 工作区资源状态

独立预检 `extension_state`。它仅支持技能；它并不意味着MCP资源管理。两条路由都选择已注册的工作区运行时，并且永远不会回退到主工作区。 GET遵循现有的只读信任规则； PUT 需要可信工作区。

```json
{
  "skills": [
    { "name": "review", "state": "enabled" },
    { "name": "deploy", "state": "disabled" }
  ]
}
```

PUT 接受 1–100 条目，包括单元素批次。它在排队之前拒绝格式错误的条目、不区分大小写的重复名称和不支持的组。所有名称必须属于已安装的目标扩展，包括当前禁用的技能；无效的目标会导致操作失败而没有部分写入。一项锁定的存储提交会合并列出的精确规范工作区的覆盖。保留未列出的技能和其他工作空间。没有设置写入、未来名称声明或父扩展的隐式激活。

GET 返回 `v: 1`、`workspaceId`、`workspaceCwd`、`extensionId`、`name` 和 `skills`。每个技能都有`name`、`defaultEnabled`、可为空的`workspaceEnabled`、`effectiveEnabled`和可选的`disabledReason`/`lockedScope`。清单的可选 `skillStates` 提供默认值；缺失值默认启用。对于活动父级，优先级是设置硬禁用、设置显式启用、设置默认禁用、工作区内部覆盖、清单默认。内部禁用使用 `disabledReason: "default"` 并且从不锁定设置。残疾或被移除的父母无法通过技能覆盖来复活。

`set_extension_state`操作返回`status: "updated"`，有序`result.resourceStates.skills`； `result.states`保留其更新检查的含义。持久状态并不能证明每个会话都已刷新。该守护进程仅刷新目标运行时的技能及其 commands/model 上下文，包括引导程序和实时会话，而不重新启动不相关的 MCP/LSP/hooks. 提交后刷新失败会产生警告而不回滚状态。覆盖在重新启动和扩展更新后仍然存在，并且在卸载时被删除。客户端不得退回到技能设置 API，该设置会写入更高优先级的设置。

#### 全球目录

全局目录响应是：

```json
{
  "v": 1,
  "generation": 12,
  "extensions": [
    {
      "id": "<64 lowercase hex characters>",
      "name": "demo",
      "version": "1.2.3",
      "installType": "npm",
      "defaultActivation": "enabled",
      "workspaceOverrideCount": 1
    }
  ]
}
```

当没有可用的安装元数据时，将省略 `installType`。 `defaultActivation` 是 `enabled` 或 `disabled`。 `workspaceOverrideCount` 排除存储的 `inherit` 条目。

工作空间投影响应为：

```json
{
  "v": 1,
  "workspaceId": "workspace-id",
  "workspaceCwd": "/absolute/workspace",
  "trusted": true,
  "desiredGeneration": 12,
  "appliedGeneration": 11,
  "extensions": [
    {
      "extensionId": "<64 lowercase hex characters>",
      "name": "demo",
      "version": "1.2.3",
      "defaultActivation": "enabled",
      "workspaceActivation": "disabled",
      "effectiveActivation": "disabled",
      "activationSource": "workspace_override"
    }
  ]
}
```

对于继承，`workspaceActivation` 是 `enabled`、`disabled` 或 `null`。 `activationSource` 是 `default`、`workspace_override`、`legacy_path_rule` 或 `cli_override`。 `desiredGeneration`是耐用品一代； `appliedGeneration` 是记录为应用于该工作区运行时的最新一代控制器，可能会暂时滞后。

安装需要明确同意并初始激活：

```json
{
  "source": "@scope/demo",
  "consent": true,
  "activation": { "scope": "user" },
  "ref": "optional-git-ref",
  "autoUpdate": true,
  "allowPreRelease": false,
  "registry": "https://registry.npmjs.org"
}
```

对于仅限工作区的初始激活，请使用 `{ "scope": "workspace", "workspaceId": "target-workspace-id" }`；目标必须存在且可信。守护进程安装接受 GitHub、Git 和 npm 源。当通告 `extension_local_path_install` 时，它们还接受守护程序主机上存在的绝对路径。 `ref` 不适用于 npm，`ref` 和 `autoUpdate` 不适用于本地源，`registry` 仅适用于 npm。 `ref`、`autoUpdate`、`allowPreRelease` 和 `registry` 是可选的。

当公布 `extension_git_credentials` 时，HTTPS Git 源可能包括用户信息，例如 `https://username:token@git.example.com/org/repository.git`。 `credentialPersistence` 仅对此类来源有效。它是`stored`或`one_time`，省略时默认为`one_time`。存储模式通过守护程序的混合秘密存储保存凭据，并仅在安装元数据中保留干净的存储库 URL，因此扩展保持可更新。一次性模式既不保存存储库 URL 也不保存凭证，并创建不可更新的 `snapshot`； `autoUpdate: true` 在此模式下被拒绝。提供不带 URL 凭据的字段、提供无效凭据或使用具有 npm、存档、本地、SSH 或非 Git 源的凭据将返回 `400`。

凭据安装响应和操作会公开 `credentialPersistence`，并可能将 `credentialStorage` 公开为 `keychain` 或 `encrypted_file`。一次性操作省略`source`；存储的操作可能会返回干净的源。快照 catalog/status 条目省略源，将 `credentialPersistence` 设置为 `one_time`，并报告 `not updatable`。更新失败并显示 `extension_not_updatable`；在使用 `extension_credential_unavailable` 进行网络访问之前，不可用的存储机密会失败。

全局和工作区激活 `PUT` 请求使用相同的正文：

```json
{ "state": "enabled" }
```

`state` 是 `enabled` 或 `disabled`。更新、卸载、检查更新、清除激活和刷新请求没有必需的正文。

批量激活请求使用扩展名称：

```json
{
  "extensionNames": ["formatter", "review-tools"],
  "state": "disabled"
}
```

工作区批次还接受 `"state": "inherit"`。终端全局结果包含`name`和`defaultActivation`；工作区结果包含 `name`、`workspaceActivation`（`null` 表示继承）和 `effectiveActivation`。名称格式错误会拒绝请求；如果没有部分提交，与现有存储标识的冲突会自动失败。未知的 `inherit` 目标不会持久存在，因为清除覆盖不得生成默认激活声明或替换后续安装同意。

每个接受的异步突变都会返回：

```http
HTTP/1.1 202 Accepted
Location: /extensions/operations/<operation-id>
Retry-After: 1
Content-Type: application/json

{"accepted":true,"operationId":"<operation-id>"}
```

工作区限定的突变使用相同的全局 `/extensions/operations/:operationId` 轮询路径。操作历史记录是进程本地的，仅保留有限数量的终端条目，并且在守护进程重新启动时丢失；当操作 ID 消失时，客户端必须重新读取目录或工作区投影并比较各代。

操作快照具有以下形状：

```json
{
  "v": 1,
  "operationId": "<operation-id>",
  "operation": "install",
  "status": "running",
  "phase": "preparing",
  "createdAt": 1750000000000,
  "updatedAt": 1750000000100,
  "source": "owner/repository",
  "name": "demo"
}
```

`status` 从 `queued` 转换到 `running`，然后转换到 `succeeded`、`succeeded_with_warnings` 或 `failed`。运行时，`phase` 为 `preparing`、`committing` 或 `reconciling`。终端成功可能包括 `result`，其中 `status` 等于 `installed`、`enabled`、`disabled`、`updated`、`uninstalled`、`checked` 或`refreshed`；协调结果还可以包含 `refreshed`、`failed` 和 `error`，而批量激活结果包含有序 `results`。更新检查返回 `result.states`，由扩展名键入，其值例如 `checking for updates`、`update available`、`up to date`、`not updatable` 或 `error`。凭证和授权标头绝不是操作字段。

持久提交后进行不完整的清理或运行时协调不会被报告为失败的突变。它返回 `succeeded_with_warnings` 并保留提交的结果：

```json
{
  "v": 1,
  "operationId": "<operation-id>",
  "operation": "uninstall",
  "status": "succeeded_with_warnings",
  "createdAt": 1750000000000,
  "updatedAt": 1750000000200,
  "result": {
    "status": "uninstalled",
    "name": "demo",
    "refreshed": 2,
    "failed": 0
  },
  "warnings": [
    {
      "workspaceId": "workspace-id",
      "workspaceCwd": "/absolute/workspace",
      "code": "reconcile_slow",
      "error": "Runtime reconciliation took 31000ms."
    }
  ]
}
```

警告【`workspaceId`】和【`code`】可选； `workspaceCwd` 和 `error` 始终存在。客户端应显示警告，刷新其 catalog/projection，并且不得盲目重试持久突变。

当存在稳定代码时，验证和授权失败是使用 `{ "error": "...", "code": "..." }` 的同步 HTTP 错误。重要的情况是`400 invalid_extension_id`、`400 invalid_extension_names`、`400 invalid_extension_name`、`400 invalid_extension_activation`、`400 workspace_mismatch`、`403 untrusted_workspace`、`404 extension_operation_not_found`和`429 extension_queue_full`。对于无效的 source/ref/registry 选项、缺少同意或 missing/invalid 初始激活，安装验证还会返回 `400`。表示在`202`之后失败的突变，同时保留在操作历史中，具有`status: "failed"`、`error`和可选的稳定`code`；常见代码包括`extension_prepare_timeout`和`extension_conflict`。操作的 HTTP `404` 并不意味着回滚，因为操作历史记录不持久。

`daemon_status` 公布 `GET /daemon/status`，即下面记录的合并只读 
 操作员诊断快照。

`daemon_update` 通告进程全局 `GET /daemon/update`、
`POST /daemon/update/prepare` 和 `POST /daemon/update/restart` 表面。

**条件标记。** 这些功能标记仅在其部署切换、运行时连接或可用性条件处于活动状态时才会公布。标签存在意味着记录的行为可用；缺席意味着该标记之前的较旧守护程序或该条件为假的当前守护程序。目前：

<!-- conditional-serve-features:start -->

|标签|刊登广告时...|
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agent_collaboration_v1`                 | 至少一个已注册工作区选择加入持久工作区 Agent 协作通过 `experimental.agentCollaboration` （包括任何范围、工作空间）或 `QWEN_CODE_ENABLE_AGENT_COLLABORATION=1`。现在的手段 `/workspaces/:workspace/agent/*` 和 Agent 主机运输路线已挂载； `workspaces[].agentCollaborationEnabled` 标识每个工作区的选择加入状态，客户端必须仅显示或调用已启用条目的协作界面。较旧的守护程序忽略该工作区字段，因此客户端可能会回退到此顶级标记以实现兼容性。缺席意味着在此响应上不提供协作 - 标签根据请求重新计算，而不是在启动时快照 - 因此客户端必须删除入口点而不是呈现它并让调用 404。首次启用需要重启；当另一个工作区已经启用它时，将其切换到另一个工作区会在下一个请求时生效。与无条件的不同 `workspace_agents` 标签，这是子代理定义 CRUD.                            |
| `hosted_harness_private_v1`              |私有 Hosted Harness 配置文件处于活动状态；仅其经过身份验证的会话 API 可用。|
| `managed_session_journal_delta_v1`       |私有托管线束配置文件处于活动状态（与 `hosted_harness_private_v1` 的条件相同），并且此版本可以打开包含 `message.delta` 记录的托管会话日志。控制平面需要能力协商时的标签，因此早于这些记录的回滚线束一旦出现就会被拒绝，而不是使每个打开的会话失败（`managed_session_open_failed`）。|
| `require_auth`                           |守护进程是使用 `--require-auth`（或通过嵌入的 API 的 `requireAuth: true`）启动的。承载令牌在每个正常的 API 路由上都是强制性的，包括环回绑定上的 `/health`；通道 Webhook 入口保持其独立的共享秘密身份验证，并且 Web Shell 文档和资产路由保持预身份验证。|
| `mcp_workspace_pool`                     |共享 MCP 传输池处于活动状态。当 `QWEN_SERVE_NO_MCP_POOL=1` 禁用池时省略。|
| `mcp_pool_restart`                       |共享 MCP 传输池处于活动状态；重新启动响应可以包括池感知的多条目形状。|
| `external_tool_guard`                    |`qwen serve`完成了`--external-tool-guard-mode=required`的启动握手；每个生成的 ACP 通道必须在会话创建之前确认已安装的回调，并且每个到达最终执行边界的受支持的顶级托管 ACP 工具调用都必须接收一个外部预执行允许。早期的permission/hook拒绝没有提出提供商请求。嵌套 AgentCore 执行位于 v1 外部，并且在此外部提供程序模式处于活动状态时被拒绝。该标签仅反映外部提供者：独立于它，每个守护程序将内置的 Git 重定位保护应用于带有 shell 命令行（`run_shell_command` 和 `monitor`）的托管工具，因此缺少此标签并不意味着没有预执行拒绝。|
| `allow_origin`                           |T2.4([#4514](<https://github.com/QwenLM/qwen-code/issues/4514>))。该守护进程至少使用一个 `--allow-origin <pattern>`（或通过嵌入式 API 的 `allowOrigins: [...]`）启动。来自匹配源的跨源请求接收正确的 CORS 响应标头；不匹配的来源仍然获得默认的403。配置的模式列表有意在 `/capabilities` 中回显 NOT，以避免将可信来源集泄露给未经身份验证的读者 - 浏览器客户端已经知道自己的来源。|
| `prompt_absolute_deadline`               |`--prompt-deadline-ms` / `QWEN_SERVE_PROMPT_DEADLINE_MS` / `ServeOptions.promptDeadlineMs` 设置为正整数。|
| `writer_idle_timeout`                    |`--writer-idle-timeout-ms` / `QWEN_SERVE_WRITER_IDLE_TIMEOUT_MS` / `ServeOptions.writerIdleTimeoutMs` 设置为正整数。|
| `workspace_settings`                     |该守护进程是在设置持久性可用的情况下创建的。|
| `user_language_sync`                     |该守护进程是在设置持久性可用的情况下创建的（与 `workspace_settings` 相同的条件），因此注册了无会话 `POST /language` 路由。|
| `workspace_voice`                        |设置持久性可用，因此旧主工作区语音设置路由处于活动状态。|
| `workspace_voice_transcription`          |主工作区具有已配置的语音转录模型。|
| `session_shell_command`                  |会话 shell 执行是显式启用的，并且在承载身份验证或可信环回权限下有效；调用仍然需要会话绑定的客户端 ID。|
| `standalone_sessions_v1`                 |该守护进程已安装完整的独立会话运行时、生命周期协调器、持久删除日志、托管目录实现和 `/standalone/sessions` 路由系列。没有完整依赖图的直接嵌入会忽略路由和此标签。|
| `standalone_session_options_v1`          |安装了完整的独立会话运行时（与 `standalone_sessions_v1` 的条件相同），因此只读、无会话 `GET /standalone/session-options` 路由在内部对话运行时注册。|
| `session_artifacts_persistence`          |会话工件持久性是为运行时连接的。|
| `session_sources`                        |会话源持久性是为运行时连接的。通过实时会话所有者注册仅元数据工作区文件、上传的附件和 HTTP(S) 链接。|
| `session_generation`                     |会话生成助手可用。|
| `scheduled_task_session_reuse`           |持久计划任务会话管理处于活动状态，并且每个托管守护程序运行时都安装了回调，该回调允许任务显式绑定到其当前现有会话。|
| `workspace_generation`                   |工作区范围的生成助手可用。|
| `rate_limit`                             |`--rate-limit` / `QWEN_SERVE_RATE_LIMIT=1` / `ServeOptions.rateLimit` 已启用。|
| `workspace_reload`                       |嵌入式路由配置中提供工作区重新加载支持。|
| `workspace_trust_grant`                  |信任热重载已连接（与 `workspace_trust_hot_reload` 的条件相同）。两个授予路由仅信任绑定的文件夹，保留现有的 `TRUST_FOLDER` 或 `TRUST_PARENT` 规则。持久化后，仅当主机策略和报告的状态可信时，`200`才返回v1状态；否则，`409 { error, code: "trust_grant_ineffective" }` 将在 `error` 中命名已检查的 state/source，包括策略加载或 IDE/file 拒绝。信任文件持久性失败针对 `FatalConfigError` 返回 `500 trusted_folders_invalid`，否则返回 `500 internal_error`。信任文件读取接受 JSONC 作者的注释和尾随逗号。路由也无需热重载即可提供：守护进程自己的 API 会立即授予缓存 v1 状态的更新，但启动绑定的门需要重新启动 (`requiresDaemonRestartForChanges: true`)。外部 terminal/file 编辑需要重新启动守护程序才能更新缓存状态。|
| `workspace_trust_hot_reload`             |工作区信任策略监控和运行时生成协调是有线的，因此信任更改无需重新启动守护程序即可生效，并且 v2 信任状态报告收敛。|
| `channel_reload`                         |守护进程管理的通道工作管理器已启用，并且可以重新加载其当前选择。|
| `channel_control`                        |守护进程管理的通道工作线程运行时控制是有线的。|
| `channel_management`                     |工作区范围内的通道设置、生命周期和配对管理都是有线的。|
| `channel_delete_config_loss_convergence` |工作区 通道管理是有线的（与 `channel_management` 的条件相同）。 `DELETE /workspaces/:workspace/channels/:name` 聚合一个 Channel，该 Channel 的配置已从 Worker 解析的合并设置视图中消失（系统 + 用户 + 工作区范围）：运行时必须对 Channel 保持沉默，或者确认该工作区中确切的一个已提交的 Worker 所有者，该所有者在删除该范围内保留的持久启动选择之前停止，而不是报告 `channel_instance_not_found`。合并视图仍包含其配置但工作区解析的通道设置范围不拥有的通道（仅在用户或系统范围配置，而工作区是项目目录）报告 `channel_instance_not_found`，运行时不变；解析范围配置的通道通常会被删除，并且目录是用户主目录的工作空间会将该范围解析为共享用户文件，因此用户范围的通道会在那里配置和删除。|
| `multi_workspace_sessions`               |注册了多个工作区运行时，因此会话创建可以通过cwd选择一个受信任的运行时。|
| `multi_workspace_session_rewind`         |注册了多个工作区运行时；单一的实时会话倒带路线解决了所属的运行时间。|
| `multi_workspace_session_shell`          |注册了多个工作区运行时，并在承载身份验证或可信环回权限下显式启用会话 shell 执行；单数 REST shell 解析所属运行时。|
| `dynamic_workspace_registration`         |工作区运行时工厂连接到守护程序中，因此现有的受信任目录可以在运行时注册为辅助运行时。|
| `persistent_workspace_registration`      |工作区注册存储连接到守护进程中。生产`runQwenServe`自动供应用户级商店；直接 `createServeApp` 嵌入必须显式注入一个并自行启动恢复其工作区注册表。|
| `scratch_workspace_registration`         |可以创建托管临时工作空间 - 运行时工厂、经过验证的托管临时根和运行时处置已连接，并且每个托管运行时都尊重临时根边界。|
| `workspace_runtime_removal`              |可移动的动态或持久性恢复的辅助运行时可以通过管理路径耗尽和删除。|
| `native_directory_picker`                |守护进程主机可以打开本机 OS 目录选择器（macOS 上为 `osascript`，Windows 上为 PowerShell，带显示器的 Linux 主机上为 `zenity`）。无头主机省略了标签，因此客户端隐藏了浏览功能，而不是暴露出有保证的选择器失败。|
| `workspace_runtime_stop`                 |该守护进程拥有 ACP 进程记帐、完整的独立活动和计划任务观察，以及支持确认停止和接收快照的运行时桥。不受支持的单个运行时在预览中被禁用。|
| `workspace_runtime`                      |每个活动工作区桥都提供权威的运行时生命周期快照；混合或遗留注入的桥省略了标签。|
| `workspace_skills_config_runtime`        |每个活动工作区都支持权威的运行时生命周期以及技能管理和新会话编辑器使用的拆分 config/runtime 技能路线。|
| `workspace_local_open`                   |守护进程主机可以在主机的 OS 文件管理器中打开工作区目录（macOS 上为 `open`，Windows 上为 `explorer.exe`，带显示器的 Linux 主机上为 `xdg-open`）。无头主机省略了标签，因此客户端隐藏了本地开放功能，而不是暴露出有保证的启动失败。打开的路径始终是解析的注册工作空间cwd，通过`POST /workspaces/:workspace/open`；该路由接受可选的 JSON 主体 `{ "target": "terminal" }`（absent/other = 文件夹）并回答 `{ kind: 'workspace-local-open', opened: true, target }`，其中 `target` 设置为 `folder` 或 `terminal`。|
| `workspace_local_terminal`               |守护进程主机可以在工作区目录中打开终端窗口（macOS 上的 `open -a Terminal`，Windows 上带有 `cmd.exe` 回退的 `wt.exe`，带显示屏的 Linux 主机上的 `gnome-terminal`/`konsole`/`xterm`）。无头主机省略了标签，因此客户端隐藏了终端开放功能，而不是暴露出有保证的启动失败。由`POST /workspaces/:workspace/open`和身体`{ "target": "terminal" }`服务。|
| `workspace_qualified_acp`                |ACP HTTP 和多工作空间运行时处于活动状态，因此复数 ACP 端点可以选择辅助运行时。|
| `workspace_qualified_voice`              |多工作空间运行时和共享的 ACP/Voice WebSocket 侦听器处于活动状态，因此每个工作空间限定的语音模态都可用于辅助运行时。|
| `workspace_qualified_memory`             |ACP HTTP 和多工作区运行时处于活动状态，因此工作区限定的托管内存路由可以选择每个工作区任务通道来进行记住、忘记和梦想操作。|
| `client_mcp_over_ws`                     |该守护进程通过 ACP WebSocket 接受客户端托管的 MCP 服务器。这是显式选择加入，CDP 隧道路径不需要。|
| `cdp_tunnel_over_ws`                     |该守护进程通过显式选择加入或因为允许 Chrome 扩展源而公开反向 `/cdp` WebSocket 隧道。这仅意味着隧道存在；这并不意味着 Chrome DevTools MCP 工具已注册。|
| `browser_automation_mcp`                 |ACP HTTP 已启用，`cdp_tunnel_over_ws` 处于活动状态，无承载令牌块 `/cdp`，并且 `QWEN_CDP_MCP_COMMAND` 命名外部 stdio MCP 适配器。主要的CLI包不捆绑浏览器自动化适配器；如果没有此标签，Chrome 扩展程序侧面板聊天可能仍然有效，但默认情况下不会注册 console/network/screenshot/click 工具。|
| `voice_transcribe`                       |语音 WebSocket 端点已安装；成功转录仍需要配置的语音模型。|
| `realtime_voice`                         |macOS WebShell 守护程序已启用实时语音，并且本机主机选择使用 `QWEN_SERVE_LIVE_NATIVE_HOST=1`（默认情况下关闭；见下文）。 `/live/status` 报告准备就绪，但在启用该功能之前该功能将被撤回。|
| `realtime_voice_web`                     |实时语音已启用，Web Shell 页面本身可能是任何平台上 WS `/live/web` 上的音频端点，以及默认端点（本机主机仅限 macOS 且可选择加入）。该路由像 `/voice/stream`（承载子协议）一样进行身份验证，需要受信任的主工作区，并使用 Live Host 协议，并减少 hello：麦克风权限以及音频 input/output 自检。保留单个主机租约：本机主机取代浏览器 (`4010`)，浏览器永远不会取代本机主机 (`4009`)，第二个选项卡仅接管 `?takeover=1`。 `/live/status`报告`host.kind: "browser"`；屏幕捕获需要共享屏幕（如下），并且为下一个本机主机存储而不是注册快捷方式更改。|
| `web_terminal`                           |ACP HTTP 已启用，因此经过身份验证的 Web 终端端点可用。|

<!-- conditional-serve-features:end -->

默认情况下，Live Voice 在每个平台上使用 Web Shell 端点（`realtime_voice_web`、WS `/live/web`）。本机 macOS 主机是选择加入的：只有以 `QWEN_SERVE_LIVE_NATIVE_HOST=1` 启动的 macOS 守护程序会通告 `realtime_voice`、注册 WS `/live/host` 并在启用 Live 时安装主机。如果没有它，`/live/setup` 会报告 `nativeHost: false` 并出现不可重试的 `install` 错误，并且 `POST /live/setup/install` 和 `POST /live/setup/launch` 会回答 `409 live_native_host_unavailable`。

仅当用户与页面共享 
 屏幕时，浏览器主机才会回答 `host.capture_visual`，页面在其问候中将其报告为 
`selfChecks.screenShare`。共享是一个单独的、显式的行为 - 模型在中间询问 
，并且 `getDisplayMedia` 需要用户手势 - 因此页面保存 
 流并为每个请求获取一帧。由于没有共享任何内容，它会立即回答
`success: false`，而不是让请求超时。

由于页面无法写入守护程序的文件系统，因此它仅发送 JPEG。
守护程序会忽略浏览器租约报告的任何 `screenshotPath`，并将 
 图像本身存储在它选择的名称下，与 
 本机主机写入的同一私有捕获目录中。本机主机仍然保留自己的 PNG 并像以前一样传递 
 路径。

在此条件表中，`mcp_guardrails` 是 ** 而不是 ** — 它是一个始终开启的标记，每当二进制文件支持新的 `/workspace/mcp` 预算字段时就会进行通告，无论操作员是否配置了预算。未设置 `--mcp-client-budget` 的操作员仍会获得新字段（使用 `budgetMode: 'off'`、`budgets: []`）。

`mcp_guardrail_events`（问题 [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14b）通告类型化的 SSE 推送事件，这些事件在没有轮询循环的情况下表面 MCP 预算状态交叉。两种帧类型到达 `GET /session/:id/events`：

- `mcp_budget_warning` — 在 `reservedSlots.size / clientBudget` 的向上 75% 交叉处开火一次。仅当比率低于 37.5% (`MCP_BUDGET_REARM_FRACTION`) 时才重新启动。镜像 PR 10 的 `slow_client_warning` 滞后，但在管理器级别而不是每个订户积压级别。有效负载：`{ liveCount, reservedCount, budget, thresholdRatio: 0.75, mode: 'warn' | 'enforce' }`。在 `warn` 和 `enforce` 模式下触发；永远不会在`off`下。
- `mcp_child_refused_batch` — 当一台或多台服务器被拒绝时，在每个 `discoverAllMcpTools*` 传递结束时触发，AND 作为 `readResource` 延迟生成拒绝路径上的长度 1 批次。有效负载：`{ refusedServers: [{ name, transport, reason: 'budget_exhausted' }, ...], budget, liveCount, reservedCount, mode: 'enforce' }`。 `mode` 是字面量 `'enforce'`，因为 `warn` 模式从不拒绝。

这两个事件都存在于每个会话的 SSE 重播环中（它们带有 `id`），因此与 `Last-Event-ID` 重新连接的客户端可以通过它们恢复； `GET /workspace/mcp` 处的快照仍然是扩展断开连接后状态的真实来源。一旦发布广告就永远在线——没有条件切换。 SDK 减速器状态 (`DaemonSessionViewState`) 为需要简单滞后式 UI 的适配器公开 `mcpBudgetWarningCount`、`lastMcpBudgetWarning`、`mcpChildRefusedBatchCount`、`lastMcpChildRefusedBatch`。

## 路线

客户端可以对 `session_turn_status` 进行功能检测并轮询 `GET /session/:id/turns/current` 或 `GET /session/:id/turns/:promptId`。这些路线需要实时拥有会话并且从不加载或扫描另一个工作区。确定的结果是通过有界扫描从活动分支读取的尽力而为的转录记录； `prompt_not_found` 表示在实时队列、64-条目终端覆盖或有界活动窗口中找不到结果。 `resultText` 是最后一个工具边界之后、可选消息重写之前的原始最终父模型答案，并且可能不存在。 32,768 UTF-16 代码单元的结果包括 `resultTruncated: true` 和 `resultCode: "RESULT_TEXT_TRUNCATED"`。

### `GET /health`

活性探针。如果侦听器已启动，则默认形式返回 `200 {"status":"ok"}` — 便宜，无桥接访问，适合高频 k8s/Compose 活性探针。

通过 `?deep=1`（也接受 `?deep=true` 或裸 `?deep`）进行守护进程范围的探测，该探测在每个托管工作区运行时聚合桥 ** 计数器 **，包括仍在耗尽的工作区（仅供参考，不是真正的活动检查）：

```json
{
  "status": "ok",
  "workspaceCount": 2,
  "sessions": 3,
  "pendingPermissions": 1,
  "activePrompts": 1,
  "activeWork": true,
  "activeWorkReporting": "full",
  "activeWorkStaleMs": 4200,
  "connectedClients": 2,
  "channelAlive": true,
  "lastActivityAt": "2026-07-15T08:30:00.000Z",
  "idleSinceMs": 120000
}
```

`sessions`、`pendingPermissions` 和 `activePrompts` 是总和。当任何运行时具有已接受但未解决的提示（包括 FIFO- 等待提示）、正在运行的后台 Agent、queued/in-progress Agent 终端通知、会话管理的后台 shell 或工作流工作或子拥有的会话轮流时，`activeWork` 为 true。聚合 `session` 保留涵盖目标和 cron 处理、历史突变以及排队或运行监视器延续；前台提示仍然由守护进程拥有。运行监视器、后续建议和 shell 注册表无法再跟踪的外部进程仍保留在该字段之外。它是会话范围的：尚未附加会话的通道级工作 - 飞行中的生成、待定的恢复、MCP 发现或身份验证 - 不计算在内，因此 `activeWork` 可能会读取 false，而守护程序仍然拒绝回收该通道。不要将此字段理解为“守护进程是可回收的”；它仅描述会话拥有的工作。 `activeWorkReporting` 表示该布尔值的实际担保量：`full` 当每个实时会话都由报告所有必需类别的子项的新报告覆盖时，`none` 当没有会话协商报告时，以及 `partial` 之间的任何内容 - 包括过时的快照或省略所需类别的协商子项。早于三个报告间隔的快照将停止计为覆盖范围：这不是会话空闲的报告，因此会话将返回到保留的读取状态，就像子级从未报告过一样。对于协商但不完整的子进程，普通的自动清理也会被禁用；不理解 `shell` 或 `session` 的子级无法根据完整的当前谓词安全地授权条件关闭。完全不受支持的历史子项保留遗留清理行为，并且显式关闭、终止、关闭和通道退出仍然强制操作。 `activeWorkStaleMs` 是布尔值所依赖的最旧快照的年龄 ** 在覆盖的会话 ** 中，并且当没有会话被覆盖时是 `0` ；它是诊断性的，因为新鲜度已经由守护进程分级为 `activeWorkReporting`（只有守护进程知道每个通道的协商节奏）。成绩是在每个托管运行时而不是每个运行时计算一次，然后合并 - 没有会话的运行时是空洞完整的，并将其视为证据将使空工作区证明另一个工作区未报告的会话。 `lastActivityAt` 是最新的非空工作区活动时间，`idleSinceMs` 是从同一快照派生的。 `channelAlive` 表示至少有一个托管工作区频道处于活动状态；这并不意味着每个工作空间都是健康的。 `connectedClients` 和可选的 `rateLimitHits` 仍然是守护进程范围的计数器，而不是每个工作空间的总和。

在以下情况下，重新启动控制器应将守护进程视为繁忙：

```ts
const busy =
  health.activePrompts > 0 ||
  health.activeWork ||
  health.activeWorkReporting !== 'full';
```

删除第三项使得 `activeWork === false` 与“没有孩子告诉我任何事情”无法区分，这是一种采取行动是不安全的情况。未知的响应和失败的探测也必须防止重新启动。 `activePrompts`仍然是一个独立的兼容性信号。

这些字段是观察缓存，而不是重新启动租约：即使是新鲜的、完全分级的、空的答案也描述了它被采样的时刻，并且工作可以在之后立即开始。上述规则大大降低了错误重启的风险，但并没有消除它——严格的安全性需要一个准备重启栅栏，它可以阻止新的工作进入，确认耗尽，然后才关闭。

> ⚠️深度探测是**信息性**，而不是真正的活性验证或原子回收租赁。协商的 ACP 子进程按照协商的节奏发布频道范围内的活动工作快照，并且守护进程将其新鲜度分级为 `activeWorkReporting` — 但它永远不会因为丢失的报告而杀死频道，因为一个会话的沉默并不证明进程已终止。传输活跃度和停滞 Agent 检测是独立的机制。 `connectedClients` 计数 REST SSE 连接，而不是每个 ACP 传输。使用重复采样和优雅关闭来进行空闲回收；使用经过身份验证的 `/daemon/status` 进行传输和每个工作空间的诊断。如果任何托管运行时 getter 抛出异常，则深度运行状况会失败并以 `503 {"status":"degraded","reason":"aggregation_failed"}` 关闭，而不是返回部分总数，并且守护程序日志会识别失败的工作区运行时。在引导期间，在运行时注册表准备就绪之前，它会返回 `503 {"status":"degraded","reason":"bootstrap"}` 和 `Retry-After: 1`。对于侦听器活跃度，请使用默认的 `/health`，而不使用 `?deep`。

**Auth：** 在非环回绑定上以及使用 `--require-auth` 强化环回时需要。在普通环回绑定（`127.0.0.0/8`、`localhost`、`::1`、`[::1]`）上，`/health` 在承载中间件之前注册，因此 pod 内的 k8s/Compose 探针不需要携带令牌。在非环回（`--hostname 0.0.0.0`等）或硬化环回上，路由在承载中间件之后注册，并在没有有效令牌的情况下返回401——否则未经身份验证的调用者可以探测任意地址以确认`qwen serve`存在，这是一种低严重性的信息泄漏，与端口扫描结合不良。 CORS 拒绝 + 主机允许列表仍然适用于普通环回豁免。

### `GET /daemon/update`、`POST /daemon/update/prepare` 和 `POST /daemon/update/restart`

这些经过身份验证的路由管理守护程序的 Qwen Code 安装，
 独立于所选工作区。他们始终使用 REST，包括来自具有 ACP 会话传输的 SDK
 客户端。 `daemon_update`能力通告
协议；可用性由返回的状态决定。

GET 检查版本，无需下载或激活它们。成功的
检查会缓存15分钟，错误会缓存一分钟； `?refresh=true`
请求重新检查。并发检查共享一个查找。响应包含
`state`（`available`、`up-to-date`、`installing`、`ready`、`restarting`、
`unavailable`或`error`）， `canInstall`，可选`currentVersion`和
`latestVersion`，以及可选`instructions`和`message`。 `currentVersion`
标识正在运行的守护程序，包括在准备更新之后。不支持的
安装方法可以使用`canInstall: false`和手动
说明报告`available`。

自动更新需要真正的 CLI 生命周期，包括 POSIX `process.execve`、
 独立或托管全局 npm 安装以及允许 
`general.enableAutoUpdate` 的操作员设置。嵌入式服务器、Windows、不受支持的 Node 运行时，
 和开发模式报告 `unavailable`。仅适用 global/system 设置；
 工作区设置无法启用安装。连接必须使用 
 主侦听器及其守护程序运行时令牌，或不带令牌的显式可信 
 环回部署。配对的浏览器和本地控制连接
报告`unavailable`，因为它们的凭据或侦听器将无法在
重新启动后继续存在；他们的更新突变返回`403 update_connection_unsupported`。

两条POST路线都需要严格的突变门守护进程操作员权限
并且不接受任何参数。服务器选择发布、目的地和
启动器；客户端无法提供版本或命令。

- `POST /daemon/update/prepare`开始后台下载并返回`202`
与`state: "installing"`。它不会更改活动安装。轮询GET
，直到状态为`ready`；重复的准备共享相同的操作，并且
准备好的更新返回`200`。由于没有可用的自动更新，它会返回
 `409 update_unavailable`。
- `POST /daemon/update/restart` 需要准备更新，否则返回
`409 update_not_ready`。在 
 激活更新之前，它会响应 `202` 和 `state: "restarting"`，正常关闭守护进程，并用更新的启动器替换进程 
。重复点击共享一次重启。替换
保留PID、工作目录、CLI选项、绑定端口和有效守护进程
令牌，清除旧版本引脚，并且不重新打开浏览器。客户端轮询
 GET，直到`currentVersion`发生更改，然后在请求更新时捕获的URL
处重新加载文档。独立启动器验证在关闭
服务之前运行；失败的检查会使守护进程继续运行。关闭
开始后或进程更换期间的故障不保证能够恢复。

Web Shell 自动在 
 后台检查和准备可用的支持更新，并仅在准备就绪时显示其版本相邻的更新按钮。
 准备和准备工作在工作区运行时重新加载后仍然存在。普通守护进程
关闭会丢弃准备好的下载。激活失败使守护进程
保持运行并报告`error`；一分钟错误缓存后的下一次检查可以
准备新的下载以供重试。禁用自动更新会取消准备状态 
 并在重新启动开始之前清除下载。单击更新按钮 
 会显式重新启动整个守护程序并中断其活动会话。

### `GET /daemon/status`

只读操作员诊断。与`/health`不同，这是一个普通的守护进程API：
它是在承载身份验证和速率限制之后注册的，包括环回
绑定。查询参数：

- `detail=summary`（默认）仅读取内存中的守护进程状态。
- `detail=full` 还包括实时会话诊断、ACP 连接
诊断、验证设备流计数和工作区状态部分。
- 任何其他 `detail` 返回 `400 { "code": "invalid_detail" }`。

`summary` 故意不查询工作区状态方法、启动 ACP
 子级或生成会话。 `full` 独立查询每个工作区部分；
 超时或异常仅将该部分标记为 `unavailable` 并添加 
`workspace_status_unavailable` 问题。

响应形状：

```json
{
  "v": 1,
  "detail": "summary",
  "generatedAt": "2026-06-16T00:00:00.000Z",
  "status": "ok",
  "issues": [],
  "daemon": {
    "pid": 12345,
    "uptimeMs": 3600000,
    "mode": "http-bridge",
    "workspaceCwd": "/repo",
    "qwenCodeVersion": "0.18.1",
    "daemonId": "serve-..."
  },
  "security": {
    "tokenConfigured": true,
    "requireAuth": false,
    "loopbackBind": true,
    "allowOriginConfigured": false,
    "allowOriginMode": "none",
    "sessionShellCommandEnabled": false
  },
  "limits": {
    "maxRegisteredWorkspaces": 256,
    "maxChannelControlWorkspaces": 25,
    "maxSessions": 32,
    "maxTotalSessions": 800,
    "maxPendingPromptsPerSession": 5,
    "listenerMaxConnections": 256,
    "eventRingSize": 8000,
    "compactedReplayMaxBytes": 4194304,
    "promptDeadlineMs": null,
    "writerIdleTimeoutMs": null,
    "channelIdleTimeoutMs": 0,
    "sessionIdleTimeoutMs": 1800000,
    "acpConnectionCap": 64
  },
  "runtime": {
    "sessions": { "active": 0 },
    "permissions": { "pending": 0, "policy": "first-responder" },
    "channel": { "live": false },
    "channelWorker": {
      "enabled": false,
      "state": "disabled",
      "channels": []
    },
    "transport": {
      "restSseActive": 0,
      "acp": {
        "enabled": true,
        "connections": 0,
        "connectionStreams": 0,
        "sessionStreams": 0,
        "sseStreams": 0,
        "wsStreams": 0,
        "pendingClientRequests": 0
      }
    },
    "perf": {
      "eventLoop": { "meanMs": 0, "p50Ms": 0, "p99Ms": 0, "maxMs": 0 },
      "promptQueueWait": {
        "count": 0,
        "meanMs": 0,
        "maxMs": 0,
        "lastMs": null
      },
      "pipe": {
        "inbound": { "count": 0, "totalBytes": 0, "maxBytes": 0 },
        "outbound": { "count": 0, "totalBytes": 0, "maxBytes": 0 }
      }
    },
    "activity": {
      "activePrompts": 0,
      "pendingPrompts": 0,
      "queuedPrompts": 0,
      "lastActivityAt": null,
      "idleSinceMs": null
    }
  }
}
```

多工作空间响应还包括顶级 `workspaces[]` 行与
`{ id, cwd, displayName?, primary, trusted }`。可选的显示名称是
未设置时省略并仅用于演示；消费者必须保持的地位
使用 `id` 或 `cwd` 关联运行时。

`runtime.perf` 是可选的。如果存在，它仅报告守护进程事件循环 
 滞后、提示 FIFO 队列等待样本和守护进程子管道字节计数器；
ACP 子事件循环滞后不包含在 `/daemon/status` 中。

如果任何问题具有错误严重性，则 `status` 为 `error`，如果任何问题具有 
 警告严重性，则 `warning`，否则为 `ok`。发行代码稳定，包括
`session_capacity_high`、`connection_capacity_high`、`pending_permissions`、
`acp_channel_down`、`preflight_error`、`mcp_budget_warning`、
`mcp_budget_exhausted`、`rate_limit_hits`、 `channel_worker_exited`、
`channel_worker_partial_connect`、`channel_restore_failed` 和 
`workspace_status_unavailable`。在侦听器准备好后但在完整运行时安装
之前的
短暂窗口期间，`/daemon/status`可能会报告`daemon_runtime_starting`；如果异步
运行时挂载失败，则会报告`daemon_runtime_failed`，而非状态
运行时路由返回`503`。

`runtime.activity` 报告守护程序范围内的提示活动。 `activePrompts` 通过飞行中提示来计算会话数。 `pendingPrompts`统计所有已接受但尚未结算的提示，包括正在运行的提示和FIFO-等待提示。 `queuedPrompts` 统计已接受但未发送的FIFO-等待提示。 `lastActivityAt` 是最后一个提示 start/end 或会话生成的 ISO 8601 时间戳； `null` 当守护进程自启动以来从未处理过任何活动时。 `idleSinceMs` 是在响应生成时根据 `lastActivityAt` 计算得出的。

`limits.memory` 是附加的，报告守护进程解析的内存数字：必需的 `enforced: false`、`childHeap` 对象（`mode`：`off`、`observe` 或 `admit`； `admissionEnforced`，仅适用于托管 `admit`；`maxConcurrentChildren` 和 `perChildCeilingMb`，两者都是 `mode: 'off'` 下的 `null`，它不建模 — 并且 `perChildCeilingMb` 另外 `null`，只要没有分区在 `modeled.minChildHeapMb` 内建模 - 要么泳池不能覆盖该楼层的一个孩子，要么天花板一旦达到 `modeled.legacyChildCeilingMb`（即 `floor(available / 2)`）就会落在泳池下方，因此会落在 1024 MB 下面的主机的地板下，而它永远不会是 0，并且在这些情况下，`maxConcurrentChildren` 是 `0`，因为没有建模的主机是计算出的答案，而不是不存在的模型；并且 `refusals`，将超出建模限制的生成）、`configuredBudgetMb`、`effectiveBudgetMb`（配置值上限为解析的 cgroup/host内存）、`budgetSource`（`flag`/`derived`）、`availableMemoryMb`、`availableMemorySource`（`constrained`/`host`）、`insufficientMemory`和`modeled` 物体持有 `rootReserveMb`、`childPoolMb`、`minChildHeapMb`、`maxChildHeapMb` 和 `legacyChildCeilingMb`（ACP 孩子今天收到的天花板的保守模型，可以坐在真实人物的下方）。 `runtime.memory` 还报告 `registeredWorkspaces`（注册计数 - 未删除的工作区条目，包括耗尽、转换或阻止的条目；不是活子计数），`activeAcpChildren`（守护进程管理的 ACP 具有活动、非死亡通道的子项 - 包括转换或阻止的条目，但不包括已开始终止的工作区，即使子进程尚未退出；不是通道工作人员、MCP 后代或独立的生成保留），`childRssCoverage`（`active_children` — 每个具有实时通道的 ACP 子进程，这是集合 `activeAcpChildren` 计数；较旧的守护进程发送 `primary_only`），下面描述的 `children` 对象，以及一个包含 `recommendedShareAtRegisteredMb`（没有注册工作空间时为 `null`）和 `recommendedShareAtActiveMb`（没有子进程处于活动状态时为 `null`）的 `modeled` 对象。每个共享的上限为旧子上限，并且仅当上限允许时才以最小子堆为底 - 在小型主机上，上限位于地板下方，因此共享 × 计数可能超过子池。将共享读取为建议，而不是池的分区。子堆大小调整仍然是建议性的：没有子生成参数源自这些值。选择加入 `admit` 强制执行建模的并发子计数，而默认 `observe` 仅记录可能的拒绝。 `runtime.memory.committedAcpChildren` 统计管理的 ACP 保留和附加子项，包括在注册表释放之前终止子项；当托管注册表不可用时，它是`null`。 `childHeap` 对 `modeled.childPoolMb` 的固定分区进行建模 - 每个子项都会收到相同的 `perChildCeilingMb`，因此建模的总数保留在池内，而不是像每个生成的份额那样累积。仅将 `refusals` 视为准入压力：0 的计数 ** 而非 ** 意味着该分区可以安全应用，因为子级在更大的主机派生上限上运行，因此需要比 `perChildCeilingMb` 更多旧空间的工作负载在这里是健康的，并且只有在应用分区后才会失败。非零计数不一定意味着容量压力的另外两个原因：准入决策对终止子进程进行计数，直到其退出，因此在已经位于 `maxConcurrentChildren` 的守护进程上，每个通道替换都会在重叠窗口期间预订拒绝；在太小而无法建模的主机上，分区 `maxConcurrentChildren` 为 `0`，因此 `refusals` 等于总 ACP 生成计数，其中 `insufficientMemory` 作为解释它的字段。在`admit`中，零插槽模型启动失败。面临容量耗尽的完全托管守护进程可能会回收一个最近最少使用的空 ACP，没有实时会话或挂起的工作，等待跟踪的释放，然后在生成之前重试准入一次。加载的会话仍然受到保护；工作区注册和保存的历史记录将被保留。 `refusals` 计数器对拒绝的准入尝试进行计数，包括初始拒绝和成功回收，而不是最终用户可见的失败。耗尽容量返回 REST HTTP 503，其中包含 `code: "acp_child_capacity_exhausted"`、`maxConcurrentChildren` 和 `committedAcpChildren`（不包括被拒绝的预留），而不包含 `Retry-After`。 ACP JSON-RPC 使用 `-32603` 和带有该代码的 `data.errorKind`，`data.httpStatus: 503` 和相同的计数器。经过验证的预调度独立创建回滚保留`standalone_creation_rolled_back`、其会话ID和可重试性，并将容量代码和计数器嵌套在`capacity`下；不确定的创建结果保留其现有的恢复语义。客户端应停止自动容量重试并保留草稿或选定的会话以进行手动重试。在正常的 `runQwenServe` 路径上，预算在创建引导应用程序之前解决，因此 `limits.memory` 已在引导窗口期间填充。仅在解决无预算问题的路径上`null`（例如直接嵌入绕过`runQwenServeImpl`）。 SDK 类型允许 `null`，因此正确的客户端可以应对。

`runtime.memory.children` 是该块内的累加并报告聚合 RSS 跨越孩子们 `childRssCoverage` 名称： `rssBytes` （他们的自我报告总结 RSS), `sampled` （有多少人进行了阅读），以及 `oldestReadingAgeMs` （总和中最旧读数的年龄，因此调用者可以知道其各部分之间的距离有多远）。分母为 `sampled` 是兄弟姐妹 `activeAcpChildren`，在块内不重复；当 `sampled` 较低， `rssBytes` 是一个楼层而不是总数。采样是在活动的门控上进行的 SSE/WS 观察者，因此针对守护进程的状态请求没有人从报告中流式传输 `sampled: 0` 即使有活生生的孩子—— `activeAcpChildren` 在它旁边使间隙可见，并且 `rssBytes: 0` 与 `sampled: 0` 绝不意味着测量为零。 `oldestReadingAgeMs` 是 `null` 当没有任何采样，并且每个贡献者都是该领域之前的桥梁时，因此它永远不会意味着“新鲜”。立即将总和读取为过量计数和不足计数：按进程求和 RSS 对子进程共享的页面进行双重计数，而每个子进程仅报告自己的进程，因此其 MCP 后代和每一位渠道工人都失踪了。它不是守护进程树的内存。该字段是可选的 SDK 镜像，因为守护进程报告 `primary_only` 永远不要发送它。

`runtime.memory.children.heap` 是该块中的累加，并报告每个 ACP 子级的生命周期 V8 老一代高水位线，聚合为 ** 最大值，而不是总和 **：`peakOldGenerationBytes`， `peakLiveSetBytes`、`peakTotalHeapBytes`、`majorGcCount`、`majorGcMs`、`unclassifiedSpaceNames` 和 `reported`。每个孩子都有堆上限，并且在不同时间达到峰值，因此总计不会回答任何问题；每个字段都是报告儿童的独立最大值，而不是一个儿童的肖像，并且每个儿童的上限是根据每个轴单独判断的。 `reported` 计算 `sampled` 贡献的数量，当某些子项早于字段时，该值会较低。每个字节数字都涵盖了 ** 老一代 ** — `--max-old-space-size` 实际限制的范围 — 而不是单独的 `old_space`，因为孩子可以用几兆字节的 `old_space` 耗尽其上限，而 `large_object_space` 则拥有一切。 `peakOldGenerationBytes` 是提交的字节，并随着给定子进程的上限而上升，因此将其视为工作负载需要的上限，而不是其要求； `peakLiveSetBytes` 是在主要 GC 中幸存下来的东西，并且不随天花板移动，这就是为什么它可以说是一个孩子无法容纳的数字；将其作为上限而不是精确的实时集读取，因为 GC 条目异步到达，并且对集合和读取之间分配的任何内容进行计数。 `peakLiveSetBytes` 是 `0`，直到观察到主要的 GC，这是缺失而不是测量。 `unclassifiedSpaceNames` 是报告子级无法分类的堆空间的并集； V8 重命名并在版本之间添加空格，从总和中删除未知空格，并删除计数不足的数据 - 因此非空数组意味着字节数字不完整，不能作为完整的测量来读取。当没有采样的子对象报告 1 时，整个对象是 `null`，绝不是归零对象；如果没有附加 SSE/WS 观察者，则根本不会采样任何内容，因此这是常规状态而不是边缘情况。所有这些都是观察性的：这里没有任何内容可以确定子级的大小、拒绝生成或将 `limits.memory.enforced` 移出 `false`。

`runtime.memory.pressure` 是该块中的附加值，并报告守护进程根自身的内存压力： `mode` (`off` / `observe`), `level` (`normal` / `soft` / `hard` / `critical`), `source` (`rss` / `heap` / `unknown`), `ratio`，以及比率来自的六个原始数字 - `rssBytes`, `rssRatio`, `availableBytes`, `heapUsedBytes`, `heapRatio`, `heapLimitBytes`. `ratio` 是较大的 `rssRatio` 和 `heapRatio`, 和 `source` 说出它是哪一个；关系报告为 `rss`. `availableBytes` 是 `limits.memory.availableMemoryMb` 以字节为单位——故意检测到 cgroup/host 数字而不是 `effectiveBudgetMb`，因为结束该过程的是真正的限制，而不是运营商的保单号码。 `source: "unknown"` 意味着分母都不可测量，并且不能被解读为健康； `level` 是 `normal` 在这种情况下，只是因为没有什么可以分类。这些数字覆盖了守护进程 **仅根进程**：它们是这个进程自己的 `memoryUsage()`，所以孩子的成长不会让他们感动。 `runtime.memory.children` 分别报告这些，并且两个数字都不是进程树内存。两种模式都会报告整个块；仅 `observe` 另外还提高了无路径 `daemon_memory_pressure` 警告进入状态汇总，所以 `off` 离开顶层 `status` 不变。在这两种模式下都没有任何补救措施。该字段是可选的 SDK 镜像，因为守护进程已发布 `runtime.memory` 在它存在之前发送没有它的块。

`limits.maxRegisteredWorkspaces` 是附加的，报告已解析的用户注册上限（默认 256，可从 1 到 256 进行配置）。 `limits.maxChannelControlWorkspaces` 报告标准守护进程上 25 的独立 control/recovery 所有者上限，即使通道已禁用。自定义控制器仅在强制执行时才会公布该字段。这些字段出现在引导响应和就绪响应中；较旧的守护进程可能会忽略它们。在构建候选工人之前，通道控制者以`409 channel_control_workspace_limit_reached`拒绝过渡业主工会超过其上限；在发布侦听器之前，上限上的初始启动时联合无法启动，因此它没有 HTTP 表面。注册容量并不决定SDK通道超时。

`limits.maxTotalSessions` 是可加的。 `null` 表示有效的守护进程范围的新鲜会话上限已禁用。当解析的注册容量（默认 256，可通过 `QWEN_SERVE_MAX_WORKSPACES` 或嵌入的 `maxRegisteredWorkspaces` 配置）超过 25 且省略 `--max-total-sessions` 时，标准守护程序将使用固定总数 800，即使只有一个启动工作空间。在注册容量为 25 或更少时，具有有限 `maxSessionsPerWorkspace` 的多个 startup/restored 工作区在相同的启动加恢复计数上得出有效总数为 `maxSessionsPerWorkspace * workspaceCount`；一个启动或恢复的工作区保留无限的默认值。明确的总限制（包括禁用值）优先。稍后动态注册不会重新计算总数。直接 `createServeApp` 嵌入必须提供自己的共享准入策略。设置后，它会限制跨守护程序创建新会话，并使用现有的 `session_limit_exceeded` 错误形状加上 `scope: "total"` 报告总限制失败。

`runtime.channel.live` 报告守护进程内的 ACP 桥接通道。它是
而不是通道适配器工作线程。守护进程管理的通道使用
`runtime.channelWorker`，其`state`是`disabled`、`starting`、
`running`、`exited`、`failed`或`stopped`之一。当工作人员到达 `running`
 然后退出时，`/daemon/status` 使守护进程保持在线状态并报告警告 
 问题代码 `channel_worker_exited`。

从显式“qwen 服务 --channel
...”启动的守护进程管理的通道工作线程仍然是快速失败的，并且优先于持久启动设置。
无标志启动会从每个受信任的注册的 
 工作空间恢复 `serve.channels`，每个工作空间都在自己的工作空间范围设置中贡献列表。 
 恢复的名称形成一个选择，其所有者照常解析，
 工作空间列出了一个名称，打破了原本不明确的所有权关系。
 只要守护进程运行，启动属性就会持续：“PUT
/workspace/channel”重新启用这样的名称，以启动的方式解析它，甚至在选择停止之后
。当无法解析时，非主工作区 
 提供的名称将被删除，并带有标识它的日志，
 因此一个工作区的过时条目不会影响其他工作区；列出的主 
 工作空间的名称仍然会导致整个恢复失败，无论另一个 
 工作空间是否也列出它。
`all` 仍然仅是主工作空间：它会在其他任何地方被忽略和报告。

启动后注册的可信非主工作区也会恢复其自己的 
`serve.channels`，加载通道运行时并保留 
 通道服务租用（如果没有其他空间）。即恢复：

- 每次守护进程运行 ** 时发生一次 ** — 在工作区要求时立即记录
任何事情 - 因此稍后删除和重新注册，或信任
重新实现，不会撤消在其间停止其中一个
通道的操作员；
- 在没有工作区的显式 `--channel` 选择下不会发生
注册后延期；
- `DELETE /workspace/channel` 停止托管后不会发生，直到
`PUT /workspace/channel` 再次提交选择或守护进程重新启动；
- 保留已提交的 `all` 选择；
- 通过一次更改将该工作区的名称添加到提交的选择中，一
  一次晚恢复一次。与 boot 不同，boot 会删除它无法托管的名称并
  保留其余部分，无法归属的名称会留下该工作区的
  整个列表未恢复，并按描述记录和报告错误
  下面。

注册根本不等待通道控制。恢复在通道控制通道上排队 
，并且无需等待 
 即可写入响应，协调守护程序已托管的 
 通道的注册或信任更改触发器也是如此。因此，永远不会准备就绪的工作线程 
 仅延迟其后面排队的通道工作，而不会进行其他注册或 
 信任协调。另一方面，来自 `POST /workspaces` 的 `201` 并不 
 意味着工作区的通道已启动，或者它 
 拥有的已托管通道已完成重新启动；请阅读`GET /workspace/channel`。

如果没有显式选择、启动时 `serve.channels` 恢复或像上面那样的 
 注册，通道运行时加载就会保持惰性。

存储的启动名称必须非空，没有前导或尾随空格，
并且不包含不安全控制或不可见字符。无效条目被
单独跳过并按数组索引记录；启动不会将它们修剪为
其他实例名称或重写设置。 Worker 参数使用 
`--channel=<value>`，保留前导破折号作为名称的一部分。

工作进程启动前无效的启动字段或验证或租用错误 
 会跳过自动恢复，并显示标识 `serve.channels` 的日志，而 
 不相关的设置仍然有效。失败的工作进程启动允许守护进程 
 在清理成功后继续。全局运行时启动超时和
未确认的工作线程停止遵循现有的启动失败路径；在工人终止合同未得到确认时，租约
仍然保留。通道管理
报告持久的启动设置和实际运行时状态。

守护进程未调出的一些 `serve.channels` 名称会被报告，而不是 
，而不是仅被记录。这样的名称永远不会加入已提交的选择，因此没有
worker快照携带它。报告了三个案例：

- 已配置的启动选择，其工作程序在启动时无法启动
使守护进程在没有通道的情况下保持服务的路径；
- 启动所有权解析器在将其归因于某个名称时删除的名称
工作区，针对列出它的每个工作区进行报告；
- 延迟注册恢复失败后，每个名称仍处于未托管状态。

它们出现在两个地方：

- `/daemon/status` 每个工作区报告一个 `channel_restore_failed` 警告，
命名每个通道及其错误。这是完整的表面，并且它 
 也出现在引导响应中，这是写入引导记录时唯一存在的表面 
。最多 64 通道按 
 工作区命名，每个名称绑定到 128 个字符，当 
 列表较长时使用 `and <n> more`；
- 该工作区的 `GET /workspaces/:workspace/channels` 列出了带有
`runtime: { state: "error", lastError }` 而不是 `stopped`，其中 
 `lastError` 是当工作线程报告该通道的一个 
 时适配器自身的启动错误，否则尝试的错误。该表面是从 
 工作区自己的通道设置范围构建的，因此它只能携带 
 范围定义的名称 - 没有注册的工作区配置的名称，或者其 
 配置位于另一个工作区中的名称，此处没有行，并且仅在 
 `/daemon/status` 上报告。

使 `serve.channels` 名称处于未托管状态的所有其他内容仅在 
 守护程序日志中进行诊断：由于整个列表不可读或 
 无效而导致名称丢失，由于选择了主工作区而丢弃了非主工作区的列表 
`all`，由于提交的选择被跳过而导致了后期恢复`all` 和 
 远程 (SSH) 工作区列表。检查这些守护进程日志。

报告将持续到通道被托管（由该工作区或稍后从另一个工作区恢复
）为止，直到其工作区不再注册，或
直到操作员采取行动：`POST`在通道上启动、停止或重新启动，
`PUT`/`DELETE /workspace/channels/:name`，或`PUT`/`DELETE
/workspace/channel` that changes the selection. `PUT
/workspace/channels/:name/startup` 不会清除它：它重写 
`serve.channels` 而不说明任何有关失败的尝试。 
报告保存在内存中：重新启动的守护进程再次恢复并报告
该尝试所做的事情。错误文本经过凭据编辑，并以 
 守护程序日志呈现它的方式进行限制。

工作线程就绪后，服务
supervisor会在有界策略内重新启动意外退出：在5分钟
窗口内最多尝试3次重新启动尝试，先有1秒、5秒，然后是15秒的退避。 Worker每
15s发送IPC心跳；如果 45 秒内没有观察到心跳，supervisor 会将工作进程视为 
 陈旧，杀死它，记录 `staleHeartbeatAt`，并使用相同的重启路径。

`runtime.channelWorker` 可以包括附加操作字段：
`requestedChannels`、`pid`、`startedAt`、`exitCode`、`signal`、`error`、
`restartCount`、 `lastExitAt`、`lastRestartAt`、`nextRestartAt`、
`lastHeartbeatAt`、`staleHeartbeatAt`、`startupFailures` 和 
`startupFailuresTruncated`。每个启动失败都有`channel`、`phase`
（当前为`connect`）、可选适配器提供的`code`和凭据-
编辑的`message`。当前worker
代最多保留64个故障；截断标志意味着观察到更多故障。 `code` 是
诊断，不是稳定的交叉适配器分类。 `restartCount` 是该服务进程的生命周期
重新启动尝试的次数；除非存在其他问题，否则具有 
`restartCount > 0` 的正在运行的工作人员是健康的。正在运行的工作人员 
，其 `requestedChannels` 包含 `channels` 报告 
`channel_worker_partial_connect` 中缺少的姓名。

在多工作空间守护进程上（重复`--workspace`），`runtime`另外
包括`channelWorkers[]` - 每个拥有的工作空间一个条目，每个
`channelWorker`快照用`workspaceId`，`workspaceCwd`注释，和
`primary`。 `channelWorker` 保持填充为主工作区的快照 
 以实现兼容性。单工作区守护进程省略 `channelWorkers[]`。

### 守护进程管理的通道控制

`channel_control` 功能通告运行时选择资源。
该资源是守护进程范围的，即使其兼容性路径使用 
 单数 `/workspace` 前缀。运行时选择不会保留，并且不会
修改守护程序的启动时`--channel`选项。

`GET /workspace/channel` 返回一个不可变的管理器快照：

```json
{
  "enabled": true,
  "selection": { "mode": "names", "names": ["telegram", "feishu"] },
  "pendingSelection": { "mode": "names", "names": ["telegram"] },
  "transition": "reconciling",
  "workers": [
    {
      "workspaceId": "primary-id",
      "workspaceCwd": "/work/primary",
      "primary": true,
      "enabled": true,
      "state": "running",
      "channels": ["telegram"],
      "pid": 1234
    }
  ]
}
```

禁用时，`selection` 为 `null`。 `pendingSelection` 仅在 
 突变期间存在。 `transition` 是 `idle`、`starting`、`reconciling`、
`stopping` 或 `rolling_back` 之一。

`PUT /workspace/channel` 受到严格限制，只接受一种选择：

```json
{ "selection": { "mode": "all" } }
```

```json
{ "selection": { "mode": "names", "names": ["telegram", "feishu"] } }
```

名称经过修剪和重复删除，无需排序；空名称数组 
 无效。 `all` 仍然仅限于主工作区。禁用到启用的更改
返回`201`；幂等 PUT 或替换返回 `200`。响应是
`{ changed, replaced, partial, state }`。平等选择将健康的
工作人员保持在适当的位置，但恢复其工作人员停止或
失败的平等选择。

`DELETE /workspace/channel` 是严格门控和幂等的。它返回
`{ changed, state }`;成功状态为禁用。 `POST
/workspace/channel/reload` 也是严格门控并重新读取设置，
重新解析工作区组，并强制协调提交的选择。
它返回 `409 channel_worker_not_enabled` 禁用时。的
`channel_reload` 仅当管理器
有一个承诺的、可重新加载的选择。

每次启用、替换、重新加载、停止和守护进程关闭都会进入一个 FIFO
 生命周期通道。 GET 不等待该车道。其有序 
 选择未更改的工作区组仍保持在线状态。替换失败尝试停止
新启动的工作人员并恢复之前提交的选择。客户端
必须检查`rolledBack`、`rollbackError`和`state`，因为清理或
恢复也可能失败。守护进程在整个事务中保留通道服务 PID 租约 
，并且在确认每个相关子 
 退出之前不会释放它。

稳定控制误差为：

- `400 invalid_channel_selection`、`channel_workspace_mismatch` 或 `ambiguous_channel_workspace`
- `403 untrusted_workspace`
- `409 channel_service_conflict`、`channel_worker_not_enabled` 或 `channel_control_workspace_limit_reached`
- `500 channel_worker_stop_failed`
- `502 channel_worker_start_failed`，带有 `rolledBack` 和可选的凭据编辑 `rollbackError`
- `503 daemon_draining`

在控制代码 
 运行之前，在没有 
 可信环回权限返回 `401 token_required` 的情况下，从无令牌主请求进行严格写入。丢失或无效的配置凭据以及未配对的本地控制 
 凭据会提前被普通 `401 Unauthorized` 拒绝。
受信任的环回主请求正常执行。
请求开始后，断开 
 与 HTTP 客户端的连接不会取消生命周期事务；客户端可以安全地重试
相同的PUT。

对于`502 channel_worker_start_failed`，响应还可以包括
`startupFailures[]`和`startupFailuresTruncated`。每次失败都会添加尝试工作人员的
可信`workspaceCwd`。这些字段描述
失败的事务，而`state`描述回滚后的当前状态；
稍后的GET不保留失败的尝试。部分连接的工作线程 
 相反会返回成功并在工作线程快照中公开其失败。未连接适配器的 
 显式 `--channel` 引导无法启动。记录设置-
派生的启动失败，并允许守护进程在
清理成功后继续，但受全局运行时启动超时的影响。未能
确认清理将保留正常的启动失败行为和服务租用。

`qwen channel status` 不带 `--daemon-url` 继续读取 pidfile 元数据；
 带 `--daemon-url` 则读取 `GET /workspace/channel`。在重新启动
窗口期间，服务拥有的pid文件保持保留，但`workerPid`被省略，因此
客户端不会显示过时的工作进程。在多工作空间守护进程上，
pid文件还带有一个附加的`workers[]`数组（每个工作空间
`workspaceId`/`workspaceCwd`/`channels`/实时`workerPid`），而
顶级`channels`（联合）和 `workerPid`（主要）为老
读者保留填充；单工作空间守护进程保持原始的单工作线程形状。工作人员 
stdout/stderr 被转发到守护程序日志中，其中包含不记名令牌、敏感的 
 工作人员环境值和经过编辑的代理 URL 凭据。

### 工作空间 渠道管理

`channel_management` 功能通告工作区范围的通道 
 配置和运行时管理。单数 `/workspace` 将目标 
 路由到主运行时。 `/workspaces/:workspace` 解析精确注册的、
 受信任的运行时，并且永远不会回退到主运行时。

只读发现使用：

- `GET /workspace/channel-types`
- `GET /workspace/channels`
- `GET /workspaces/:workspace/channel-types`
- `GET /workspaces/:workspace/channels`

该目录用
`manageable: true`标记了该管理API支持的类型。实例快照包括修订、编辑秘密
存在元数据、启动状态和运行时状态；字面上的秘密永远不会
返回。通道快照使用`Cache-Control: no-store`。

字段描述符可以通过 `properties` 公开嵌套对象元数据。
数字描述符可以使用 `exclusiveMinimum` 来开放下限。字符串
和秘密描述符可以使用`multiline`向客户端询问多行文本
区域；描述符类型仅允许在顶级字段上使用。 
不呈现广告字段类型的客户端必须保留其现有配置值
，而不是强制或删除它，并且在单行控件中呈现`multiline`
字段的客户端必须逐字保留存储的值，而不是
写回其换行符删除的输入值。对象字段不能是
必需的，并且嵌套属性不能是秘密或环境可解析的
字段；这些管理协议仅保留顶层。仅当其父对象存在于写入中时，才会强制执行嵌套的 `required`
 属性；
 省略父对象会使其嵌套要求处于未选中状态。写入
批量替换每个字段的存储值，因此保留对象意味着
重新发送存储的对象；守护进程不会合并部分对象。

配置写入使用乐观并发和严格的操作员权限
门：

- `PUT /workspace/channels/:name`
- `DELETE /workspace/channels/:name`
- `PUT /workspace/channels/:name/startup`
- 等效的 `/workspaces/:workspace/...` 路线

每个设置突变都包含`expectedRevision`。 Upsert 请求包含 
`config` 对象，并且可能包含显式秘密操作：`preserve`、
`replace` 或 `clear`。通道配置无法选择已解析工作空间之外的工作目录
。

运行时操作是对 
`.../channels/:name/start`、`stop` 或 `restart` 的严格门控 `POST` 请求。它们仅在已解析工作区拥有的 
 工作线程上运行。

配对管理仅适用于配置了 
`pairing` 发件人策略或组策略的实例：

- `GET .../channels/:name/pairing-requests`
- `POST .../channels/:name/pairing-requests/approve` 与 `{ "code": "..." }`
- `GET .../channels/:name/pairing-approvals`
- `DELETE .../channels/:name/pairing-approvals` 与
`{ "senderId": "..." }` 或 `{ "groupId": "..." }`

所有配对路由都需要严格的操作员权限并使用 `Cache-Control: no-store`。
请求、批准和撤销的范围仅限于所选通道 
 实例和工作区。待处理的请求包括键入的用户或组主题；
组请求还保留发起请求的发件人。批准
快照包含`senderIds`和`groupIds`，因为白名单不会保留
显示名称。撤销未知用户或组将返回
`404 channel_pairing_approval_not_found`。

### 渠道交付和通知

`channel_delivery` 宣传即时、尽力交付支持。它是
协议功能，而不是工作人员健康信号。交付永远不会启动
失踪的工作人员，回退到另一个工作区，重试，保留发件箱，
或重播历史通知。

Direct Notify 绕过 Agent 和会话并等待一次发送尝试：

```http
POST /workspace/notify
POST /workspaces/:workspace/notify
Authorization: Bearer <token>
Content-Type: application/json

{
  "text": "service unavailable",
  "delivery": {
    "kind": "channel",
    "target": {
      "channelName": "dingtalk",
      "type": "user",
      "id": "platform-user-id"
    }
  }
}
```

两条路线都使用严格的突变门。合格的路由仅解析
已注册、受信任的工作区。成功是 `200 {delivered:true,deliveryId}`.
`delivered:true` 表示 Channel 发送 Promise 已解决；它并没有证明
提供商接受、用户收据或已读收据。特定于提供商
响应验证和一致的错误原因语义 IM 适配器
在此之外 V1 合同。
错误是 `400 channel_delivery_invalid`, `503 channel_worker_unavailable` 或
`channel_delivery_queue_full`, `504 channel_delivery_timeout`，和`502
channel_delivery_rejected` or `channel_delivery_failed`。超时有一个
结果未知，不会重试。
故意没有单独的连接测试端点：正常
通知调用是端到端测试。

可重播结果事件仅包含相关性和清理状态：

```json
{
  "type": "channel_delivery_result",
  "promptId": "prompt-1",
  "data": {
    "sessionId": "session-1",
    "deliveryId": "prompt-1",
    "source": "prompt",
    "status": "failed",
    "promptId": "prompt-1",
    "code": "channel_worker_unavailable",
    "error": "Channel worker is not running."
  }
}
```

空的成功提示最终会忽略错误字段：

```json
{
  "type": "channel_delivery_result",
  "promptId": "prompt-1",
  "data": {
    "sessionId": "session-1",
    "deliveryId": "prompt-1",
    "source": "prompt",
    "status": "skipped",
    "promptId": "prompt-1"
  }
}
```

`source`为`prompt`或`scheduled`； `status` 是 `delivered`、`failed` 或 
`skipped`。 `skipped` 表示符合条件的转弯已成功完成，但其
最后一个无工具辅助响应块为空或仅包含空格。 
 守护进程使用传递授权并发布事件，而无需 
 解析通道工作线程。计划关联使用 `taskId` 和 `firedAt`。
事件从不包含目标 ID、消息文本、凭据或 Webhook 
秘密。

安全性：响应从不包含承载令牌、客户端 ID、完整的 ACP
 连接 ID、设备流用户代码或验证 URL。两个细节
级别都可以包括附加的`daemon.runId`、`daemon.logMode`和
`daemon.logHealth`。 `summary` 省略守护程序日志路径和丢失详细信息；
`full` 可能包括经过身份验证的操作员的 `logPath`、`logIssues`、`logDroppedRecords` 和 
`logDroppedBytes`。降级文件日志记录将 
 无路径 `daemon_log_degraded` 警告添加到正常状态汇总中。

### `GET /capabilities`

```json
{
  "v": 1,
  "protocolVersions": {
    "current": "v1",
    "supported": ["v1"]
  },
  "mode": "http-bridge",
  "features": [
    "health",
    "daemon_status",
    "capabilities",
    "multi_workspace_sessions",
    "..."
  ],
  "limits": {
    "maxRegisteredWorkspaces": 256,
    "maxChannelControlWorkspaces": 25,
    "maxPendingPromptsPerSession": 5,
    "maxSessionsPerWorkspace": 32,
    "maxTotalSessions": 800,
    "sessionRestoreTimeoutMs": 60000
  },
  "modelServices": [],
  "workspaceCwd": "/canonical/path/to/primary-workspace",
  "workspaces": [
    {
      "id": "stable-workspace-id",
      "cwd": "/canonical/path/to/primary-workspace",
      "primary": true,
      "trusted": true
    },
    {
      "id": "stable-secondary-workspace-id",
      "cwd": "/canonical/path/to/secondary-workspace",
      "displayName": "Payments Production",
      "primary": false,
      "trusted": true
    }
  ]
}
```

稳定契约：当 `v` 递增时，框架布局已以向后不兼容的方式更改。

> **`protocolVersions`** 描述了守护进程可以使用的服务协议版本。 `current` 是守护程序的首选协议版本，`supported` 是兼容集。需要特定协议的客户端应检查`supported`；特定功能的 UI 仍应选择 `features`。 v=1 的附加项：较旧的 v=1 守护进程忽略此字段，因此针对较旧版本的 SDK 客户端应将其视为可选。

> **`modelServices` 在阶段 1 中始终为 `[]`。** agent 使用其单一默认模型服务，并且不会通过网络枚举它。阶段 2 将从注册的模型适配器中填充此内容，以便 SDK 客户端可以构建服务选择器；在那之前，NOT 是否依赖该字段非空。

> **`workspaceCwd`** 是守护程序主工作区的规范绝对路径。使用它可以省略 `POST /session` 上的 `cwd`（路由回退到此主路径）并保持旧的单工作区客户端兼容。 v=1 的附加：前§02 v=1 守护进程忽略该字段 - 面向旧版本的客户端应在使用它之前进行空检查。

> **`workspaces[]`** 列出每个已注册的运行时。较新的单工作空间守护进程包括主运行时，即使 `multi_workspace_sessions` 不存在也是如此，因此客户端可以发现工作空间限定路由所需的稳定 ID；较旧的守护进程可能会省略该数组。每个条目都是`{ id, cwd, displayName?, primary, trusted, removable? }`。 `displayName` 仅用于演示，未设置时将被省略。 first/primary 工作空间仍由 `workspaceCwd` 镜像；新客户端通过将该条目的 `cwd` 传递到 `POST /session` 来选择非主运行时。不受信任的工作区会进行诊断广告，但会拒绝使用 `403 untrusted_workspace` 创建新的会话，直到信任发生变化。 `removable` 存在于支持运行时删除的守护进程上，并且仅适用于进程动态或持久性恢复的辅助运行时。

> **`session_worktree_persistence_v1`** 表示守护进程可以持久保存并验证第 4A 部分工作树所有权。成功的工作树创建响应，以及其子级在空闲时重新定位或已报告已验证工作树 cwd 的恢复响应，携带 `worktree` 元数据加上 `worktreeState: "persisted-v1"`。旧版尽力而为的恢复，或者恢复提示被触发而不是暂停的冷恢复（`suppressWorktreeContextRestore` 关闭，因此路由要求网桥不延迟），因此报告没有当前 cwd 的活动提示，仍然可能返回 `worktree`，而无需该证明。请求隔离的客户必须预先检查此标签并验证每个响应； `worktree` 对象本身并不是持久所有权证明。

> **`session_worktree_reset_v1`** 表示守护进程支持工作树所有权转移：`POST /session/:id/worktree-reset` 将持久工作树会话的签出所有权移动到新的替换会话。恢复响应获得三个类型的 409 分类：`worktree_session_superseded`（会话的 sidecar 带有 `supersededBy` 链接 - 在读取任何标记之前，仅根据该链接决定分类，并且在标记翻转之前写入链接，因此主体的 `replacementSessionId` 是用于验证的重定向，而不是所有权证明：在预提交中中断状态，它指定一个不是标记所有者的会话，本身无法恢复，并通过重试重置获得），`worktree_marker_missing`（检出标记不存在；重置任务以重新创建它 - 恢复重试不能，因为没有恢复路径写入标记）和`worktree_reset_interrupted`（先前的传输在飞行中崩溃，其 sidecar 链接同意；重试重置）。首先检查中断的分类：`supersedes`/`supersededBy`链接的缺失标记同意表面为`worktree_reset_interrupted`，而不是`worktree_marker_missing`。有关传输协议和故障分类，请参阅下面的路由部分。

工作区功能标签和 `workspaces[]` 是动态的。添加工作区的客户端必须在突变完成后再次获取 `/capabilities`；守护进程不会向缓存早期响应的客户端广播功能更改。忘记持久性不会卸载活动的运行时，因此运行时在重新启动之前将保持广告状态。

### `GET /brand`

Web Shell 的产品品牌，适用于想要对 shell 进行白标签的主机。已从操作员设置范围中的 `ui.brand` 解决。由 `web_shell_brand` 功能标签控制；没有路由的守护进程会回答404。

```json
{
  "name": "QiuQiu Code",
  "logoDataUri": "data:image/svg+xml,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%2F%3E"
}
```

> **两个字段都是可选的，`{}` 是正常响应。** 缺少字段意味着“使用客户端的内置品牌”——Web Shell 呈现自己的名称和内联徽标。客户端不得将空正文视为错误。

> **处理程序总是回答200，即使配置的徽标被拒绝。**丢失的文件、符号链接、硬链接文件、目录、非SVG文档或超过32的内容KiB生成一个没有任何内容的正文`logoDataUri`，原因以`qwen serve: GET /brand: ui.brand.logoPath …`写入守护进程的stderr。因此，客户端无法区分“未配置品牌”和“拒绝运营商徽标”；面向操作员的通道是守护进程日志。一个建议比拒绝更温和，有两个：根 `<svg>`，没有可用的 `viewBox`，并且没有正的非百分比 `width`/`height`（格式错误或零区域 viewBox 算作不可用），浏览器可能会在侧边栏的固定大小；以及一个前缀绑定的根，其无前缀的元素缺乏默认命名空间绑定，这使得它们不可见。处理程序前面的中间件在运行之前进行应答 - `401` 当需要承载身份验证且不存在时，`429` 当启用可选速率限制器时。耗尽守护进程不会拒绝此路由：速率限制器在耗尽时是允许的，因此处理程序会继续应答 200 直到侦听器关闭并且客户端看到连接失败。在此路由上启动不是 503：在默认的延迟运行时路径上，请求是 _ 保留 _ 直到运行时准备好然后应答 200，因此较短的客户端超时可以提前触发。具有 `code: "daemon_runtime_starting"` 的 503 仅在运行时准备好之前应答的配置上到达此路由（例如 `--open`）并且可重试；带有 `code: "daemon_runtime_failed"` 的 503 是终端，直到守护进程重新启动并且不携带 `Retry-After` — 不要重试。

> **工作区设置永远不会起作用。**路由使用`skipWorkspaceSettings`加载设置，因此存储库的`.qwen/settings.json`无法重命名产品或命名文件以供守护程序读取并内联到每个连接的浏览器中。按照该优先顺序，仅读取系统默认值、用户和系统。出于同样的原因，每当占位符替换会更改品牌值时，品牌值都会被拒绝，并在守护程序的 stderr 上发出警告：替换从进程范围的环境中提取，工作空间的 `.qwen/.env` 或 `env` 块在启动时首先填充。无法解析的占位符（变量未设置）会逐字保留，因此拼写错误的变量显示为文字文本，而不是默默地回退。

> **`logoDataUri` 必须呈现为图像，切勿作为标记注入。** 守护进程不会清理它读取的 SVG。通过 `img` src 或 favicon href 加载的 SVG 无法执行脚本； SVG注入文件即可。 Web Shell 只将其分配给图像上下文，而这种不变性使得没有消毒剂是安全的。

> **进程全局。** 该路由不采用工作区选择器和会话 ID：该值源自用户全局配置，因此对于守护程序服务的每个工作区来说都是相同的。它在 `bearerAuth` 和速率限制器之后、Web Shell SPA 回退之前注册，因此它会为任何 `Accept` 标头应答 JSON。

### `POST /workspaces`

注册额外的工作区运行时。该路径必须是现有的、可访问的绝对目录，并且不会与其他已注册的工作区重复或嵌套。注册是进程本地的，除非客户端发送 `persist: true`；客户端必须在请求持久性之前进行预飞行 `persistent_workspace_registration`。当通告`workspace_display_name`时，请求还可以包括可选的`displayName`。

```json
{
  "cwd": "/canonical/path/to/secondary-workspace",
  "persist": true,
  "displayName": "Payments Production"
}
```

新创建的运行时返回`201`；将已经活跃的辅助工作空间提升为持久回报 `200`。持续的成功包括`persisted: true`：

```json
{
  "id": "stable-workspace-id",
  "cwd": "/canonical/path/to/secondary-workspace",
  "displayName": "Payments Production",
  "primary": false,
  "trusted": true,
  "persisted": true
}
```

`displayName` 必须是修剪周围空格后不超过 256 个字符的字符串。空结果被视为无名称，并且内部 C0 (`U+0000`–`U+001F`) 或 DEL (`U+007F`) 控制字符将被拒绝。 JSON `null`不是创建值，返回`400 invalid_display_name`；省略该字段将不提供初始名称。允许重复的显示名称。进程本地注册提供的名称仅适用于该守护进程； `persist: true`将其与持久注册一起存储，以便重启后可以恢复。重复请求已经持久的工作空间是幂等的，并且不会重命名它。

错误包括`400 invalid_path` / `invalid_persist_flag` / `invalid_persist_target` / `invalid_display_name`、`409 workspace_exists` / `workspace_nested` / `workspace_limit_reached` / `workspace_registration_store_too_large`、 `500 workspace_registration_store_error` / `runtime_creation_failed` 和 `501 persistence_not_available` / `not_implemented`。

### `PATCH /workspaces/:workspace`

更新由工作区 ID 或 URL- 编码的绝对 cwd 选择的活动工作区资源。端点当前仅支持显示名称元数据：

```json
{ "displayName": "Payments Production" }
```

发送`{ "displayName": null }`清除名称。这里`null`是一个只更新的删除哨兵；非空值遵循与 `POST /workspaces` 相同的字符串规范化规则。响应是更新的 `{ id, cwd, displayName?, primary, trusted, removable? }` 工作空间投影。运行时元数据始终更新。如果运行时具有匹配的持久注册标识，则每个别名都会通过现有模式-v1注册存储自动更新；端点从不创建或促进持久注册。

不支持的字段会失败关闭，而不是被默默地忽略。错误包括 `400 empty_patch` / `invalid_display_name` / `unsupported_field` / `workspace_mismatch`、`409 workspace_registration_in_progress`、`500 workspace_registration_store_error` 和 `503 daemon_shutting_down`。

### `DELETE /workspaces/:workspace`

删除一个可移动的辅助运行时。选择器遵循复数工作区路由规则并接受工作区 ID 或 URL- 编码的绝对 cwd。可选的【JSON】本体为【`{ "force": boolean }`】；省略它要求非强制删除。

当冻结的运行时具有会话、提示、挂起的启动、ACP 连接、内存任务或工作区通道工作人员时，非强制删除会返回 `409 workspace_busy` 和 `activity` 快照。发送 `{ "force": true }` 请求终止这些资源。持久性删除是提交点：后续清理是有界的且尽力而为，清理失败会被记录下来，逻辑删除仍然收敛而不是恢复运行时。成功的响应是：

```json
{
  "removed": true,
  "workspaceId": "stable-workspace-id",
  "workspaceCwd": "/canonical/path/to/secondary-workspace",
  "forced": true,
  "persistedRegistrationRemoved": true,
  "activity": {
    "sessions": 2,
    "activePrompts": 1,
    "pendingSessionStarts": 0,
    "acpConnections": 1,
    "memoryTasks": 0,
    "channelWorkers": 0,
    "voiceSessions": 0
  }
}
```

立即繁忙的非强制请求会返回快速的预耗尽活动快照。一旦开始排空，繁忙或成功响应将包含接纳后拍摄的最终快照，并且 ACP 排空门关闭且清理开始之前。错误包括`400 invalid_force_flag` / `workspace_mismatch`、`409 workspace_busy` / `primary_workspace_removal_forbidden` / `static_workspace_removal_forbidden` / `workspace_removal_in_progress` / `workspace_registration_in_progress`、`500 workspace_persist_failed` / `workspace_runtime_removal_failed`、`501 workspace_runtime_removal_unsupported` 和 `503 daemon_shutting_down`。

### `GET /workspace-registrations`

列出此主工作区的持久所需工作区集。当当前启动期间无法恢复存储的目录时，条目仍然可见 `active: false`。
条目在其运行时耗尽时仍保持 `active: true`，因为运行时仍然拥有实时资源，直到删除完成。
当持久注册有一个条目时，条目包括可选的 `displayName`。

```json
{
  "schemaVersion": 1,
  "primaryWorkspace": "/canonical/path/to/primary-workspace",
  "entries": [
    {
      "id": "stable-registration-id",
      "cwd": "/canonical/path/to/secondary-workspace",
      "displayName": "Payments Production",
      "active": true,
      "persisted": true
    }
  ]
}
```

当未配置注册存储时返回 `501 persistence_not_available`，当无法读取存储时返回 `500 workspace_registration_store_error`。

### `DELETE /workspace-registrations/:id`

忘记一项持久注册。这不会卸载活动运行时或终止其会话； `restartRequired: true` 表示活动运行时在下次守护程序重新启动时消失。

```json
{ "removed": true, "active": true, "restartRequired": true }
```

返回 `404 workspace_registration_not_found`、`500 workspace_registration_store_error` 或 `501 persistence_not_available`。与其他突变路由一样，当启用守护程序身份验证时，此端点需要突变身份验证。

### 只读运行时状态路由

这些路由报告守护程序端运行时快照。它们是附加的v1路由，
不会改变状态，并且不会更改服务协议版本。工作空间
状态路由故意执行**而不是**启动ACP子进程，只是因为
客户端轮询GET路由：如果守护进程空闲，它们返回
`initialized: false`并带有空快照。会话状态路由需要 
 实时会话，并针对未知 
id 返回 `404 { code: "session_not_found", ... }`。

能力标签：

- `workspace_mcp` → `GET /workspace/mcp`
- `workspace_skills` → `GET /workspace/skills`
- `workspace_providers` → `GET /workspace/providers`
- `workspace_acp_status` → `GET /workspace/acp/status`
- `workspace_env` → `GET /workspace/env`
- `workspace_preflight` → `GET /workspace/preflight`
- `session_context` → `GET /session/:id/context`
- `session_supported_commands` → `GET /session/:id/supported-commands`
- `session_tasks` → `GET /session/:id/tasks`
- `session_resources` → `GET /session/:id/resources`
- `session_monitor_tool_correlation` → 监控来自 `GET /session/:id/tasks` 的条目
包括 `toolUseId` 用于转录本与任务的关联
- `session_status` → `GET /session/:id/status`
- `session_info` → `GET /workspace/:id/session-info` 和 `GET /workspaces/:workspace/session-info`
- `session_transcript` → `GET /session/:id/transcript`
- `workspace_persisted_transcript` → `GET /workspaces/:workspace/session/:id/transcript`
- `workspace_session_export` → `GET /workspaces/:workspace/session/:id/export`
- `workspace_archived_session_export` → `GET /workspaces/:workspace/session/:id/archive/export`
- `workspace_session_live_state` → `GET /workspaces/:workspace/sessions/live-state`
- `workspace_qualified_memory` → `POST /workspaces/:workspace/memory/{remember,forget,dream}` 和 `GET /workspaces/:workspace/memory/{remember,forget,dream}/:taskId`

`workspace_acp_status` 将主工作区 ACP 通道的 
 时间点活跃度报告为 `{ channelLive: boolean }`。处理程序不会
创建通道，但到达运行时路由可以首先启动延迟守护进程
运行时，其配置的启动策略可以独立预热ACP。 
快照不是租约：客户端必须让会话创建重新验证或启动
通道。

### ACP预热

能力标签：`workspace_acp_preheat`。

`POST /workspace/acp/preheat?timeoutMs=N` 尽力初始化主
工作空间ACP通道。 `timeoutMs` 默认为 5000，并且必须是不大于 60000 的正 
 整数。并发调用者和会话创建共享
相同的桥初始化。请求超时仅在HTTP等待时结束；它
不取消共享初始化。

```ts
interface WorkspaceAcpPreheatResult {
  ready: boolean;
  channelLive: boolean;
  durationMs: number;
  reason?: 'timeout' | 'error';
  error?: string;
}
```

`ready` 始终等于 `channelLive`。实时响应省略了 `reason` 和 
`error`；否则 `reason` 为 `timeout` 或 `error`。 `durationMs` 测量 
 当前 HTTP 调用，而不是调用加入的初始化的完整生命周期。
操作超时或失败返回 HTTP 200。无效的 `timeoutMs` 返回 
400，而身份验证、速率限制和延迟运行时失败则保留 
 的正常响应。

两条 ACP 工作空间路线都是单一的且仅限主工作空间。客户
不得将它们用作辅助工作空间或将任一响应解释为
持久的准备保证。

常见状态单元格：

```ts
type DaemonStatus =
  | 'ok'
  | 'warning'
  | 'error'
  | 'disabled'
  | 'not_started'
  | 'unknown';

type DaemonErrorKind =
  | 'missing_binary'
  | 'blocked_egress'
  | 'auth_env_error'
  | 'init_timeout'
  | 'restore_timeout'
  | 'protocol_error'
  | 'missing_file'
  | 'parse_error';

interface DaemonStatusCell {
  kind: string;
  status: DaemonStatus;
  error?: string;
  errorKind?: DaemonErrorKind;
  hint?: string;
}
```

`errorKind` 是由 `/workspace/preflight`、
`/workspace/env` 和（最终）MCP 护栏共享的封闭枚举，因此 SDK 客户端可以按类别呈现 
 修复，而不是解析自由格式的消息。原始的
七个状态文字来自#4175； `restore_timeout` 单独添加
 用于会话恢复请求。 `blocked_egress` 保持保留状态，直到 
 出口探测器着陆。

状态负载永远不会暴露 MCP 环境值、标头、 OAuth/service-account
详细信息，提供商 API 密钥、提供者 `baseUrl` / `envKey`、技能体、技能
文件系统路径、钩子定义或秘密环境的值
变量。 `/workspace/env` 报告了 **存在** 列入白名单的环境
仅变量；代理 URL 被剥夺凭证并简化为
`host:port` 在他们撞到电线之前。

### `GET /workspace/mcp`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "discoveryState": "completed",
  "servers": [
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "docs",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
      "description": "Documentation server",
      "extensionName": "docs-ext"
    }
  ]
}
```

`discoveryState` 是 `not_started`、`in_progress` 或 `completed` 之一。
`transport` 是 `stdio`、`sse`、`http` 之一， `websocket`、`sdk` 或 
`unknown`。发现成功时，将省略 `errors`。

**MCP 客户端护栏（问题 [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>)）。** 当前守护进程通过四个附加字段和一个功能范围的预算单元扩展有效负载：

```jsonc
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "discoveryState": "completed",
  "clientCount": 3,
  "clientBudget": 2,
  "budgetMode": "enforce",
  "budgets": [
    {
      "kind": "mcp_budget",
      "scope": "workspace",
      "status": "error",
      "errorKind": "budget_exhausted",
      "hint": "Raise --mcp-client-budget or remove servers from mcpServers config.",
      "liveCount": 2,
      "budget": 2,
      "mode": "enforce",
      "refusedCount": 1,
    },
  ],
  "servers": [
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "a",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
    },
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "b",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
    },
    {
      "kind": "mcp_server",
      "status": "error",
      "name": "c",
      "mcpStatus": "disconnected",
      "transport": "stdio",
      "disabled": false,
      "disabledReason": "budget",
      "errorKind": "budget_exhausted",
      "hint": "...",
    },
  ],
}
```

`budgetMode` 是 `enforce`、`warn` 或 `off` 之一。未设置预算时，`clientBudget` 不存在。 `budgets[]` 是 ** 始终是广告 `mcp_guardrails` 的守护进程上的数组 **（当 `budgetMode === 'off'` 时可能为空）；旧的守护进程完全省略了该字段。当公布 `mcp_workspace_pool` 时，单元具有 `scope: 'workspace'` 并覆盖所选工作区运行时的共享池。当该标签不存在时，包括在 `QWEN_SERVE_NO_MCP_POOL=1` 下，旧版管理器会发出 `scope: 'session'`。消费者 MUST 容忍额外的无法识别的范围值。

每个服务器单元上的 `disabledReason` 将操作员禁用（`'config'` - `disabledMcpServers` 配置列表）与预算拒绝（`'budget'` - 发现但由于 `enforce` 模式而从未连接）区分开来。拒绝是由 `Object.entries(mcpServers)` 声明顺序确定的。每个服务器的 `status: 'error', errorKind: 'budget_exhausted'` 隐藏原始 `mcpStatus: 'disconnected'`（这是事实，但不是面向操作员的严重性）。

预算执行是能力驱动的。使用 `mcp_workspace_pool`，一个工作区运行时内的会话共享传输和一个 `WorkspaceMcpBudget`；不同的工作区运行时从不共享池或预算。如果没有标签，每个 ACP 会话的 `McpClientManager` 都会强制执行其自己的上限副本，并且快照表示该旧会话视图。

**检测预算压力。**两个表面，均在PR-14b后填充：

- **推送事件**（通过`mcp_guardrail_events`发布）：订阅`GET /session/:id/events`并通过`KnownDaemonEvent`缩小`mcp_budget_warning`/`mcp_child_refused_batch`框架。状态机每次向上 75% 交叉都会触发一次（在 37.5% 下面重新武装）；在 `enforce` 模式下，每次发现传递都会合并一次拒绝。
- **快照民意调查**（通过`mcp_guardrails`公布）：`GET /workspace/mcp`并检查预算单元格（`budgets[0]`）和`mcp_workspace_pool`以确定其范围：

- `budgets[0].status === 'warning'` ⇔ `liveCount >= 0.75 * clientBudget`（与 14b 的推送事件将使用的滞后阈值 PR 匹配）。
- `budgets[0].status === 'error'` ⇔ `refusedCount > 0`（一台或多台服务器拒绝此发现传递）。
- `budgets[0].status === 'ok'` ⇔ 低于 75% 阈值 AND 则不拒绝。

建议的民意调查节奏：与已经民意调查的内容保持一致 `/workspace/mcp`；快照很便宜，并且预算单元不承担额外的发现成本。订阅推送事件的 SDK 客户端仍然受益于扩展断开连接后状态的快照（SSE 重播环深度是有限的 - `--event-ring-size`，默认 8000 - 因此客户端离线时间超过环的覆盖范围后会回退到快照重新同步）。

### `GET /workspace/skills`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "skills": [
    {
      "kind": "skill",
      "status": "ok",
      "name": "review",
      "description": "Review code",
      "level": "project",
      "modelInvocable": true,
      "userInvocable": false,
      "installedPath": "/home/alice/project/.qwen/skills/review/SKILL.md",
      "argumentHint": "[path]"
    },
    {
      "kind": "skill",
      "status": "ok",
      "name": "database-review",
      "description": "Review database changes",
      "level": "extension",
      "modelInvocable": true,
      "installedPath": "/home/alice/.qwen/extensions/alibabacloud-database-suite/skills/database-review/SKILL.md",
      "extensionName": "alibabacloud-database-suite",
      "extensionDisplayName": "Alibaba Cloud Database Suite"
    }
  ]
}
```

`level` 是其中之一 `project`, `user`, `extension`, 或 `bundled`.
`userInvocable` （布尔值，可选）对于普通技能被省略（意思是
`true`）并且仅存在于 `false` 当技能无法发动时
手动。它不会限制所描述的仅限设置的技能切换路线
下面。 `modelInvocable` 是独立的： `false`
意味着该技能仍然可以手动使用，但在模型调用中隐藏。
`installedPath` 是该技能的现有绝对路径 `SKILL.md`;的
守护进程将其返回为存储状态，而无需单独解析符号链接或
将其规范化。当前的守护进程为每项技能发出它，而客户端必须
容忍老年人的缺席 v1 守护进程。技能体、钩子、 `skillRoot`,
以及其他技能配置仍然被排除在外。 `errors` 当
发现成功。

对于扩展拥有的技能，`extensionName` 是规范清单名称，
 可以安全地用作所有者身份。 `extensionDisplayName` 是可选的，
本地化表示值并且可能不是唯一的。新客户应显示
`extensionDisplayName ?? extensionName`；旧的守护进程省略了显示字段。

重复读取是从最后提交的工作区快照中提供的，
根据子项的内存缓存定期重新验证。读取从不
扫描技能目录或重新解析`SKILL.md`文件。子进程确实验证
其扩展源未更改 - 扩展
目录的一个`readdir`加上每个条目的`stat`、启用文件和存储的
激活状态 - 并且仅在它们移动时刷新，因此下次读取时仍会拾取在守护程序外部安装或切换的扩展
。
安全和裸模式跳过检查，匹配其对扩展的排除。

### `GET /workspace/providers`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "current": { "authType": "qwen", "modelId": "qwen3(qwen)" },
  "providers": [
    {
      "kind": "model_provider",
      "status": "ok",
      "authType": "qwen",
      "current": true,
      "models": [
        {
          "modelId": "qwen3(qwen)",
          "baseModelId": "qwen3",
          "name": "Qwen 3",
          "description": null,
          "contextLimit": 4096,
          "isCurrent": true,
          "isRuntime": false
        }
      ]
    }
  ]
}
```

模型按身份验证类型分组。提供商连接诊断实时存在于 
`/workspace/preflight` 的 `providers` 单元上；环境预检位于 
`/workspace/preflight` 和 `/workspace/env`（如下）。快照构建成功时，`errors`省略
。

### `GET /workspace/env`

报告守护进程的运行时、平台、沙箱、代理以及白名单秘密环境变量的
**存在**。始终从 `process.*` 状态回答 
 — 守护进程永远不会生成 ACP 子进程来服务 
 这条路由，并且无论 ACP 是启动还是空闲，响应都是相同的。 
`acpChannelLive` 字段仅供参考。

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": false,
  "cells": [
    { "kind": "runtime", "name": "node", "status": "ok", "value": "22.4.0" },
    { "kind": "platform", "name": "darwin", "status": "ok", "value": "arm64" },
    {
      "kind": "sandbox",
      "name": "SANDBOX",
      "status": "disabled",
      "present": false
    },
    {
      "kind": "proxy",
      "name": "HTTPS_PROXY",
      "status": "ok",
      "present": true,
      "value": "proxy.internal:1080"
    },
    {
      "kind": "proxy",
      "name": "NO_PROXY",
      "status": "disabled",
      "present": false
    },
    {
      "kind": "env_var",
      "name": "OPENAI_API_KEY",
      "status": "ok",
      "present": true
    },
    {
      "kind": "env_var",
      "name": "ANTHROPIC_BASE_URL",
      "status": "disabled",
      "present": false
    }
  ]
}
```

细胞形状：

```ts
type DaemonEnvKind =
  | 'runtime' // name: 'node' | 'bun' | 'unknown'; value: process.versions.node
  | 'platform' // name: process.platform; value: process.arch
  | 'sandbox' // name: 'SANDBOX' | 'SEATBELT_PROFILE'; value optional
  | 'proxy' // name: HTTP_PROXY | HTTPS_PROXY | NO_PROXY | ALL_PROXY; value: redacted host
  | 'env_var'; // presence-only; value field is ALWAYS omitted

interface DaemonEnvCell extends DaemonStatusCell {
  kind: DaemonEnvKind;
  name: string;
  present?: boolean;
  value?: string;
}
```

**编辑策略。** `kind: 'env_var'` 单元格从不包含 `value`
 字段；客户只能看到 `present: boolean`。 `kind: 'proxy'` 单元通过凭据编辑 (`redactProxyCredentials`) 和 
 运行 
 原始环境值，然后通过 `URL` 解析，因此线路仅携带 `host:port`。 `NO_PROXY`
 逐字通过编辑传递，因为它是主机列表而不是 
a URL。枚举秘密环境变量的白名单当前包括
`OPENAI_API_KEY`、`ANTHROPIC_API_KEY`、`GEMINI_API_KEY`、`GOOGLE_API_KEY`、
`DASHSCOPE_API_KEY`、`OPENROUTER_API_KEY`和`QWEN_SERVER_TOKEN`。其他
env vars未枚举，因此意外设置的秘密保持不可见。

### `GET /workspace/preflight`

报告守护进程准备情况检查。 **守护进程级单元格**（`node_version`、
`cli_entry`、`workspace_dir`、`ripgrep`、`git`、`npm`）是始终由 `process.*` 和 `node:fs` 填充 
。 **ACP-水平单元格**（`auth`、
`mcp_discovery`、`skills`、`providers`、`tool_registry`、 `egress`)
需要一个实时的ACP子进程——当守护进程空闲时，它们会发出
`status: 'not_started'`占位符。该路线永远不会单独生成 ACP 
 来填充单元格；相应的单元格回落到`not_started`。

空闲响应（无 ACP 子项）：

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": false,
  "cells": [
    {
      "kind": "node_version",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "22.4.0", "required": ">=22" }
    },
    {
      "kind": "cli_entry",
      "status": "ok",
      "locality": "daemon",
      "detail": { "path": "/usr/local/bin/qwen", "source": "process.argv[1]" }
    },
    {
      "kind": "workspace_dir",
      "status": "ok",
      "locality": "daemon",
      "detail": { "path": "/canonical/path" }
    },
    { "kind": "ripgrep", "status": "ok", "locality": "daemon" },
    {
      "kind": "git",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "2.45.0" }
    },
    {
      "kind": "npm",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "10.7.0" }
    },
    {
      "kind": "auth",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "mcp_discovery",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "skills",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "providers",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "tool_registry",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "egress",
      "status": "not_started",
      "locality": "acp",
      "hint": "egress probing lands in PR 14 (#4175)"
    }
  ]
}
```

细胞形状：

```ts
type DaemonPreflightKind =
  | 'node_version'
  | 'cli_entry'
  | 'workspace_dir'
  | 'ripgrep'
  | 'git'
  | 'npm'
  | 'auth'
  | 'mcp_discovery'
  | 'skills'
  | 'providers'
  | 'tool_registry'
  | 'egress';

interface DaemonPreflightCell extends DaemonStatusCell {
  kind: DaemonPreflightKind;
  locality: 'daemon' | 'acp';
  detail?: Record<string, unknown>;
}
```

`errorKind` 语义：

- `missing_binary` - 需要以下节点版本，缺少 `QWEN_CLI_ENTRY`，
ripgrep / git / npm 不在 PATH 上（
 可选二进制文件的警告而不是错误）。
- `missing_file` — `boundWorkspace` 不存在或不是目录；
技能解析错误指向丢失或无法读取的文件。
- `parse_error` — `SKILL.md` 解析失败，配置格式错误 JSON。
- `auth_env_error` — `validateAuthMethod` 返回非空失败
字符串，或从提供者
解析传播的`ModelConfigError`子类。
- `init_timeout` — `withTimeout` 在桥中拒绝（实际超时
等待 ACP 往返时）。通过 
 `BridgeTimeoutError` 类型类识别。注意：具有 `connecting > 0` 的瞬态 `mcp_discovery`
 `warning` 单元确实 NOT 携带这种类型 - 这是 
 正常的握手进行中状态，与真正的超时不同。
- `restore_timeout` — 会话负载或恢复超出了专用恢复
预算。 REST响应是`504`并且是可重试的；它与 
 子初始化和有界重播窗口限制不同。
- `protocol_error` — ACP `extMethod` 因通道关闭而被拒绝
请求中途，或者因为工具注册表意外缺失。
- `blocked_egress` — 保留给 PR 14 (#4175)。 PR 13 离开
`egress` 单元格为 `status: 'not_started'`。

如果桥接器在服务预检 
 请求时无法到达 ACP 子级（例如，请求中通道关闭），则信封的 `errors` 数组 
 会携带一个描述失败的 `ServeStatusCell` 和单元格 
 回退到 `not_started` ACP 占位符。守护进程级单元仍然
返回。

### `GET /workspace/tools`

返回主工作区的 ACP 子工作区报告的工具目录。这是
旧的主工作空间路由：它没有工作空间选择器，并且不能
用于推断非主运行时的工具。

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": true,
  "tools": [
    {
      "name": "ReadFile",
      "displayName": "Read",
      "description": "Read a file",
      "enabled": true
    }
  ]
}
```

当没有 ACP 子进程处于活动状态时，路由仍返回 `200` 和 
`acpChannelLive: false`、一个空 `tools` 数组以及 
 可选 `errors` 数组中的 `not_started` 条目。意外的网桥故障使用标准`500`
网桥错误响应。 TypeScriptSDK方法为`workspaceTools()`； 
没有专用的功能标签，因此支持旧守护程序的客户端应
将`404`视为不受支持。

### 工作区文件路径

所有文件路径都通过守护程序的主工作区解析。对于正常的 
 成功案例，响应使用 
 工作空间相对路径，并且从不返回绝对文件系统路径。成功的文件响应包括：

```http
Cache-Control: no-store
X-Content-Type-Options: nosniff
```

文件系统错误使用此 JSON 形状：

```json
{
  "errorKind": "hash_mismatch",
  "error": "expected sha256:..., found sha256:...",
  "hint": "re-read the file and retry with the latest hash",
  "status": 409
}
```

`errorKind`值包括`path_outside_workspace`、`symlink_escape`、
`path_not_found`、`binary_file`、`file_too_large`、`untrusted_workspace`、
`permission_denied`、 `parse_error`、`hash_mismatch`、
`file_already_exists`、`text_not_found` 和 `ambiguous_text_match`。

#### `GET /file`

读取文本文件。查询参数：`path`（必填）、`maxBytes`、`line`、`limit`、
和`cursor`。守护程序拒绝二进制文件。高于 256 KiB
 完整快照 
 上限的文件需要至少一个显式窗口参数（`line`、`limit` 或 
`maxBytes`）；没有保留任何一个的请求`file_too_large`。这样的
窗口是流式传输的，其返回的UTF-8内容的上限保持在256KiB。
`maxBytes`始终适用于解码后的UTF-8响应字节，包括
当源使用另一个完整快照上限内支持的编码。

行偏移是通过从文件开头扫描来解决的，因此当到达窗口时，窗口 
 也会被 `file_too_large` 拒绝，它会读取超过 
8 MiB (`MAX_TEXT_SCAN_BYTES`) 的内容。使用`GET /file/bytes`直接达到更深的偏移
。编码中的大文本路由无法解码返回
`binary_file`，而不是`file_too_large` - 使用较小的窗口重试无法
帮助，并且`readBytes`与已经适用于二进制的补救措施相同。

对于完整快照上限内的文件，响应包括 `hash`、整个文件的原始磁盘字节的 SHA-256
 摘要，即使 `line`、`limit`、
 或 `maxBytes` 返回切片时也是如此。大的部分窗口省略`hash`，保留
完整`sizeBytes`，设置`truncated: true`，当流在EOF之前停止时返回
`originalLineCount: null`。

##### 使用 `cursor` 寻呼

需要 `workspace_file_read_cursor` 能力。具有更多 
 的响应返回 `hasMore: true`，并且当文件字节偏移可导出时，返回 
`nextCursor` 令牌。将其传递回为 `cursor` 在 O(1) 中恢复，其中深度 
`line` 偏移量需要从字节 0 进行扫描，并且在超过 8 MiB 后被拒绝。

```
GET /file?path=big.log&limit=500          → { content, nextCursor, hasMore: true }
GET /file?path=big.log&limit=500&cursor=… → next page
```

`cursor` 和 `line` 是互斥的 (`parse_error`) — 都命名为 
 起点。格式错误或过长的 cursor 为 `parse_error`； a cursor
，其文件已被替换或截断为 `hash_mismatch` (409)。附加
确实**而不是**使未完成的cursor无效，这是功能
存在的情况。

`content` 省略了最后一行的终止换行符，就像其他读取 
 所做的那样，因此客户端重新组装页面时将它们与 `\n` 连接起来。 `hasMore` 不是 `nextCursor` 的 
 重述：使用 `limit` 读取的小型非 UTF-8 文件具有 
 更多内容，但没有可导出的字节偏移量，因此它报告 `hasMore: true`与
`nextCursor: null`。当字节上限剪切当前的 
 行时，cursor 也为空，因为从该偏移量恢复将返回部分行。对于许多
短行，降低`limit`直到页面在字节上限之前结束并返回
a cursor。对于单个超大行，显式请求以下行
（例如，从第1行开始时为`line=2`），然后使用光标继续；
当需要完整的超大行时，使用`GET /file/bytes`。

```json
{
  "kind": "file",
  "path": "src/index.ts",
  "content": "export {};\n",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "sizeBytes": 11,
  "returnedBytes": 11,
  "truncated": false,
  "hash": "sha256:...",
  "matchedIgnore": null,
  "originalLineCount": null
}
```

#### `GET /file/bytes`

从文件中读取原始字节而不解码。查询参数：`path`（必填）、
`offset`（默认`0`）和`maxBytes`（默认`65536`，最大`262144`）。此 
 路由支持大型二进制文件上的有界窗口，而无需占用整个 
 文件。仅当返回的窗口覆盖
整个文件时，响应才包含`hash`。

```json
{
  "kind": "file_bytes",
  "path": "assets/logo.png",
  "offset": 0,
  "sizeBytes": 3912,
  "returnedBytes": 3912,
  "truncated": false,
  "contentBase64": "...",
  "hash": "sha256:..."
}
```

#### `GET /stat`

返回一个主工作区路径的元数据。查询参数`path`为
必填。

```json
{
  "kind": "stat",
  "path": "src/index.ts",
  "type": "file",
  "sizeBytes": 128,
  "modifiedMs": 1700000000123
}
```

`type` 是 `file`、`directory`、`symlink` 或 `other`。该路由使用上面
描述的
工作空间文件边界和常见文件系统错误包络。 TypeScript SDK 方法是 `fileStat()`。

#### `GET /list`

列出一个主工作区目录。查询参数 `path` 是必需的；
`includeIgnored=1`（或 `true`）包含忽略规则匹配的条目。 
 响应的上限为 2,000 条目，并在存在更多条目时设置 `truncated: true`。

```json
{
  "kind": "list",
  "path": ".",
  "entries": [{ "name": "src", "kind": "directory", "ignored": false }],
  "truncated": false,
  "matchedIgnore": null
}
```

每个条目的 `kind` 为 `file`、`directory`、`symlink` 或 `other`。 
TypeScript SDK 方法是 `dirList()`。

#### `GET /glob`

匹配主工作空间内的路径。查询参数`pattern`为
必填。可选的`cwd`缩小搜索范围，`includeIgnored=1`（或`true`）
包括忽略的路径，`maxResults`是从1到50,000
的整数（默认5,000）。

```json
{
  "kind": "glob",
  "pattern": "**/*.ts",
  "cwd": "",
  "matches": ["src/index.ts"],
  "count": 1,
  "truncated": false,
  "durationMs": 4
}
```

匹配与工作空间相关。无效查询值返回`400`；工作区
信任、遏制、路径丢失和意外故障使用常见的
文件系统错误包络。 TypeScript SDK 方法是 `glob()`。

#### `POST /file/write`

创建或替换文本文件。这是严格的突变路线：授权无令牌
可信环回主请求。到达没有可信环回权限的网关的无令牌主请求 
 返回 
`401 { "code": "token_required" }`。配置令牌和本地控制 
 凭证失败会被简单的 `401 Unauthorized` 提前拒绝；对于
`--require-auth`，全局承载中间件在路由运行之前拒绝未经身份验证的请求
。

身体：

```json
{
  "path": "src/new.ts",
  "content": "export const value = 1;\n",
  "mode": "create"
}
```

```json
{
  "path": "src/existing.ts",
  "content": "export const value = 2;\n",
  "mode": "replace",
  "expectedHash": "sha256:..."
}
```

`mode` 必须是 `create` 或 `replace`。 `create` 永远不会覆盖现有的 
 文件 (`409 file_already_exists`)。 `replace`需要`expectedHash`；缺失或
格式错误的哈希值是`400 parse_error`，过时的哈希值是
`409 hash_mismatch`。 `expectedHash` 是 `sha256:` 加上 64 小写十六进制 
 字符，根据原始磁盘字节计算。

可以提供`bom`、`encoding`和`lineEnding`。替换默认保留
现有文件的编码配置文件；显式字段覆盖它。
二进制写入超出范围。

守护进程写入目标目录中的随机临时文件，在
支持的情况下进行fsync，在`rename()`之前重新检查当前哈希，然后将
重命名到位。这可以防止部分文件观察并序列化
守护进程发起的对同一文件的写入，但它不是跨进程
内核比较和交换：外部编辑器仍然可以在最终哈希检查和重命名之间的小窗口
中进行竞争。

```json
{
  "kind": "file_write",
  "path": "src/existing.ts",
  "mode": "replace",
  "created": false,
  "sizeBytes": 24,
  "hash": "sha256:...",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "matchedIgnore": null
}
```

#### `POST /file/edit`

对现有文本文件应用一个精确的文本替换。这也是
严格的突变路线，需要`expectedHash`。

```json
{
  "path": "src/config.ts",
  "oldText": "timeout: 30000",
  "newText": "timeout: 60000",
  "expectedHash": "sha256:..."
}
```

`oldText` 必须非空并且只出现一次。没有匹配返回
`422 text_not_found`；多个匹配返回 `422 ambiguous_text_match`。
 路由保留编码、BOM 和行结尾，并在原子重命名之前立即重新检查 
`expectedHash`。

允许显式 writes/edits 忽略路径，因为经过身份验证的 
 调用者命名了该路径。成功响应和审核事件包括
`matchedIgnore: "file" | "directory" | null`。

```json
{
  "kind": "file_edit",
  "path": "src/config.ts",
  "replacements": 1,
  "sizeBytes": 128,
  "hash": "sha256:...",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "matchedIgnore": null
}
```

### `GET /session/:id/context`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "state": {
    "models": {},
    "modes": {},
    "configOptions": []
  }
}
```

对于顶级会议， `state` 镜像相同 ACP
model/mode/config-option 使用的形状 `POST /session`,
`POST /session/:id/load`, 和 `POST /session/:id/resume`. A
`subagent.`-前缀虚拟会话 ID 根据其父运行时进行解析
并返回一个空的 `state` 对象。

### `GET /session/:id/supported-commands`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "availableCommands": [
    {
      "name": "init",
      "description": "Initialize the project",
      "input": null,
      "_meta": { "source": "builtin" }
    }
  ],
  "availableSkills": ["review"]
}
```

`availableCommands` 与 
`available_commands_update` SSE 通知使用的命令快照相同。 `availableSkills`仅列出技能
名称；客户不得期望通过这条路线获得技能机构或路径。

### `GET /session/:id/tasks`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "now": 1700000000000,
  "tasks": [
    {
      "kind": "agent",
      "id": "agent-1",
      "label": "reviewer: check failure",
      "description": "check failure",
      "status": "running",
      "startTime": 1699999999000,
      "runtimeMs": 1000,
      "outputFile": "/tmp/agent-1.jsonl",
      "isBackgrounded": true,
      "subagentType": "reviewer"
    },
    {
      "kind": "agent",
      "id": "agent-2",
      "label": "general-purpose: run the failing test",
      "description": "run the failing test",
      "status": "running",
      "startTime": 1699999999500,
      "runtimeMs": 500,
      "outputFile": "/tmp/agent-2.jsonl",
      "isBackgrounded": false,
      "subagentType": "general-purpose",
      "parentAgentId": "agent-1",
      "parentName": "reviewer",
      "depth": 1
    }
  ]
}
```

该路由是只读带外快照。它故意不是 
 提示，并且可以在会话流式传输时进行查询。响应仅
包含来自agent、shell和监视任务
注册表的白名单元数据；控制器、计时器、偏移量、挂起消息和原始注册表
对象永远不会暴露。

由另一个子agent（嵌套子agents，以
`maxSubagentDepth`为界）生成的Agent任务带有三个可选的谱系字段：`parentAgentId`（
生成agent任务的`id`），`parentName`（生成的agent的
`subagentType`，在注册时捕获，因此它在父级从注册表中驱逐
后仍然存在）和`depth`（基于0的启动深度；0 = 由
顶级会话生成）。 Agents由顶级会话发起，省略
`parentAgentId`和`parentName`；客户端应将所有三个字段视为
可选，并在它们不存在时退回到平面列表。

### `POST /session/:id/tasks/:taskId/workflow-action`

控制工作流程运行，或启动新的工作流程。 ACP方法是
`_qwen/session/tasks/workflow_action`。

```json
{
  "action": "run-script",
  "script": "export const meta = { name: 'daily-audit', description: 'Audits yesterday' }\nreturn await agent(args.question)",
  "args": { "question": "which tables grew?" },
  "sourceRef": { "id": "definition-7", "revision": "rev-3" }
}
```

`taskId` 的名称取决于操作：

| `action`                            | `taskId`                                                           |开始跑步|
| ----------------------------------- | ------------------------------------------------------------------ | ---------------------- |
| `pause`, `resume`, `retry`, `rerun` |运行 ID|`retry` 和 `rerun` 做|
| `delete-history`                    |运行 ID|不|
| `run-saved`                         |保存的工作流程的名称，`<extension>:<name>` 表示扩展的|是的|
| `run-script`                        |呼叫者选择的开始键|是的|

`args`、`sourceRef` 和 `script` 只能由 `run-saved` 和 `run-script`
 读取。 `args` 绑定到脚本的 `args` 全局，可以是任何 JSON 值；
`sourceRef` 是调用者自己的 `{id, revision}`，记录在运行、其 
 日志及其快照上； `script` 是 `run-script` 运行的源，并且是 
 所需的。重试或重新运行会重播原始运行自己的 `args` 和 
`sourceRef` — 这就是使其运行相同的原因 — 因此这些字段与控制操作一起被忽略 
。

已启动的运行是会话拥有的：它在后台运行，无需批准
提示，并且其完成到达会话的完成通道。像任何其他运行一样，它之后是一个 
`retry`/`rerun` 目标。

`retry` 和 `rerun` 还接受从历史记录恢复的运行（
`GET /session/:id/tasks` 中的 `isHistorical`），例如守护进程重新启动中断：运行是 
 从其快照脚本再次启动，`args` 和`sourceRef`，并且它
在发送
操作的会话中注册并向其报告其完成情况。 `retry` 在同一运行 ID 下恢复运行日志，并将 
 应用于没有进程仍在运行的 `failed` 运行； `rerun` 从任何已完成的运行开始新的运行 
id。带有 `argsOmitted` 的历史条目已启动 
，但 `args` 太大而无法保留，因此这两个操作都不适用于它。也不保留在 `args` 之前写入的 
 快照，该快照既不包含 `argsOmitted`
 也不包含 `argsRecorded`：运行的日志是根据其 `args` 的哈希值键入的，因此 
 重新启动其日志`args`其历史记录无法命名，不会重播任何内容，并且
重新调度每个agent。两个拒绝都是 `workflow_args_unavailable`，并且 
`GET /session/:id/tasks` 在条目上都报告为 `argsUnavailable`，因此 
 客户端可以保留这两个操作，而不是通过 
 进行调用来发现拒绝。 `argsOmitted` 留在旁边作为原因，对于想要说出哪个的客户 
。 `args` 本身永远不会放在电线上。

`run-script` 不传递定义名称，因此运行由脚本自己的 
`export const meta` 标记 — 已编译的脚本应声明一个，或者运行仅显示 
 其 id。

对于已开始的运行，响应为 `{"changed": true, "status": "running", "taskId": "<runId>"}`
；对于已生效的控制 
 操作，响应为 `{"changed": true, "status": "<status>"}`；当没有发生任何事情时，响应为 `{"changed": false}`：
工作流对于会话不可用（已禁用、裸模式、不受信任的 
 文件夹）、工作区不受信任、保存的工作流名称未知、
 运行 ID 未知，或者另一个启动已在同一 
`taskId` 下运行。仅对重叠的开始进行重复数据删除：一个 `taskId` 下的两个并发 
`run-script` 调用启动一次运行。一旦开始返回，
按键被释放；再次提交可以开始另一次运行，即使
第一次运行仍然处于活动状态。在
丢失响应后，该密钥不提供重试幂等性。

拒绝的参数是 `400`（`-32602` 超过 ACP），而不是已启动的运行：
未知 `action`、没有 `script` 的 `run-script`、 `sourceRef` 不是非空字符串的 
`{id, revision}`，或者由于语法无效或违反确定性而在启动之前被拒绝的脚本 
。工作流程启动输入
错误携带`workflow_invalid_params`并保留拒绝消息。

四个拒绝来自运行的存储状态而不是请求。每个
是ACP上的`-32602`，携带其`errorKind`和`data.httpStatus`。
三个是`409`；第四个，`workflow_not_recorded`，是一个`503`，因为
没有任何东西启动，并且可以简单地再次进行相同的调用：`retry`
记录运行在开始之前再次运行 - 该记录是
阻止另一个进程再次启动它 - 并且该记录不能写
。

| `code` / `errorKind`           | HTTP  |当|该怎么办|
| ------------------------------ | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `workflow_journal_unavailable` | `409` |一个 `retry`，其运行在磁盘上没有日志，或者无法读取日志|`rerun` it，从头开始|
| `workflow_args_unavailable`    | `409` |标记为 `argsUnavailable` 的历史条目的 `retry` 或 `rerun`：其 `args` 太大而无法保留，或者其快照早于保留它们|使用 `run-saved` 或 `run-script` 及其 `args` 重新启动它|
| `workflow_run_live_elsewhere`  | `409` |一次运行的 `retry`，其检查点记录了一个尚未退出的进程 — 一个仍在运行，一个在另一台机器上，或者一个 pid 被重用的进程|`rerun` 它，它需要一个新的运行 ID|
| `workflow_not_recorded`        | `503` |一个`retry`，其运行再次运行的记录无法写入，因此没有启动|再次拨打同样的电话；运行未受影响|

`GET /session/:id/supported-commands` 中的 `workflowToolFeatures` 广告 
`runSavedArgs` 和 `runScript`；没有它们的守护进程既不接受启动
输入，也不接受`run-script`操作。它还携带 `nameOnly`，当 
 会话的模型只能运行命名工作流 (`tools.workflowNameOnly`) 时为 true：
 模型自己的 `script` 和 `scriptPath` 调用被拒绝，而每个上面的操作
，包括`run-script`，仍然开始运行。当
`retry`和`rerun`接受从历史记录恢复的运行时，`retryHistorical`为真；较旧的守护进程用 `{"changed": false}` 回答 
，因此客户端应仅在报告 
 历史记录条目时提供这些控制。

### `GET /session/:id/lsp`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "enabled": true,
  "configuredServers": 1,
  "readyServers": 1,
  "failedServers": 0,
  "inProgressServers": 0,
  "notStartedServers": 0,
  "servers": [
    {
      "name": "typescript",
      "status": "READY",
      "languages": ["typescript", "javascript"],
      "transport": "stdio",
      "command": "typescript-language-server"
    }
  ]
}
```

`status` 是 `NOT_STARTED`、`IN_PROGRESS`、`READY` 或 `FAILED` 之一。
可选的 `error` 存在于故障服务器上（如果可用）。禁用 LSP
（包括裸模式）返回 HTTP 200，其中包含 `enabled: false`、零计数和 
`servers: []`。在未配置服务器的情况下启用 LSP 将返回 `enabled: true`、
`configuredServers: 0` 和 `servers: []`。如果在
客户端存在之前初始化失败，则响应可能包含`initializationError`；如果实时客户端
无法提供快照，则响应包括`statusUnavailable: true`。

该路由仅公开面向客户端的稳定字段。它故意省略了 
 调试内部结构，例如进程 ID、生成参数、stderr 尾部、根 URI 和 
 工作空间文件夹路径。

### `GET /session/:id/resources`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/session/path",
  "skills": {
    "v": 1,
    "workspaceCwd": "/canonical/session/path",
    "initialized": true,
    "skills": []
  },
  "mcp": {
    "v": 1,
    "workspaceCwd": "/canonical/session/path",
    "initialized": true,
    "discoveryState": "completed",
    "servers": []
  }
}
```

此实时会话所有者路由通过其 
 自己的 ACP 连接读取所选会话的配置。它不会组合进程全局或工作区级别
状态路由、初始化冷运行时、附加客户端或回退到
主工作区。未知和仅持久会话返回现有的 
`session_not_found` 响应。

嵌套对象使用确切的 `GET /workspace/skills` 和 
`GET /workspace/mcp` 状态协定。它们的 `workspaceCwd` 字段标识 
 生成快照并匹配顶级值的配置。现有的 
 编辑规则仍然适用：MCP 凭据和标头、环境值、
 技能正文和原始设置永远不会出现在响应中。 MCP
身份验证、池、工作区预算和工作区发现错误
丰富功能不存在，因为它们的支持状态是工作区拥有的或仅通过服务器名称而不是会话键控
。来自所选会话自己的 MCP 管理器的状态、发现和记帐 
 仍然可用。

### 独立会话生命周期（`standalone_sessions_v1`）

当 `/capabilities.features` 包含 `standalone_sessions_v1` 时，守护进程会为其专用对话运行时拥有的顶级独立会话公开进程全局路由系列。这些路由从不接受工作区选择器，也从不回退到主工作区。无法构建完整对话所有权、运行时、目录、生命周期和删除日志依赖关系图的直接嵌入会忽略以下功能和所有路由。

|路线|请求|成功|
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `POST /standalone/sessions`                      | `{ "sessionId": "<UUID>", "modelServiceId"?: string, "startupConfig"?: { "modelServiceId": string, "reasoningEffort"?: ReasoningSelection }, "approvalMode"?: ApprovalMode }`        |`200` 与独立会话 `context: { "kind": "standalone" }` 及其托管无项目输出目录。创造是无提示的。|
| `GET /standalone/session-options`                |没有；任何查询字段都会被拒绝并显示 400|`200` 与 `{ v, initialized, current?, approvalMode?, providers, errors? }`；省略内部工作空间路径和 ACP- 通道状态|
| `GET /standalone/sessions`                       |查询：`cursor?`、`size?`（1-100）、`archiveState?`（`active`或`archived`）| `200 { sessions, nextCursor?, liveMergeFailed?, truncated? }`                                                                                  |
| `GET /standalone/sessions/:id`                   |无|`202 { sessionId, state: "creating" }` 本地创作正在进行中，否则 `200` 包含确切的摘要。|
| `POST /standalone/sessions/:id/load`             |仅现有恢复选项：`historyPageSize?`、`liveReplayMode?`、`compactedReplayMode?`、`hideInheritedHistory?`、`approvalMode?`；客户端身份保留在 `X-Qwen-Client-Id` 中。|`200` 恢复独立会话。|
| `POST /standalone/sessions/:id/resume`           |与 `load` 相同的恢复选项。|`200` 恢复独立会话，无需重放加载历史记录。|
| `POST /standalone/sessions/:id/repair-directory` |空体或`{}`|`200` 包含已验证或重新创建的托管目录。|
| `PATCH /standalone/sessions/:id/metadata`        | `{ "displayName": string }`                                                                                                                                                          | `200 { sessionId, displayName }`                                                                                                               |
| `GET /standalone/sessions/:id/export`            |查询：`format=html`、`format=md`、`format=json` 或 `format=jsonl`（默认为 `html`）。|现有导出内容类型、文件名和正文。|
| `POST /standalone/sessions/archive`              | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { archived, alreadyArchived, notFound, errors }`                                                                                          |
| `POST /standalone/sessions/unarchive`            | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { unarchived, alreadyActive, notFound, errors }`                                                                                          |
| `POST /standalone/sessions/delete`               | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { removed, notFound, errors, fileCleanupPending }`                                                                                        |

当 `POST /standalone/sessions` 携带旧版 `modelServiceId` 时，响应包括 `modelApplied`：`false` 表示生成时模型切换失败（也通过 `model_switch_failed` 会话事件浮出水面）并且会话在 agent 默认模型上运行 — 创建本身仍然成功，因此调用者可以显式警告、释放或重试。

主体必须是没有未知字段的 JSON 对象。 ID 为 RFC UUID v1-v5 值；守护进程将它们规范化为小写。批量请求包含 1-100 字符串，并在突变之前进行验证和去重。批量失败报告为`{ sessionId, code, message }`，并且不会回滚其他ID上的成功操作。 `fileCleanupPending` 表示已提交转录本删除，但必须通过协调重试期刊授权的 sidecar 或托管目录清理；该会话已在逻辑上被删除。

仅显式独立转录本和记录的顶级遗留兼容性形状可见。子、实时、项目、工作树、不明确、不可读或删除日志记录无法关闭。如果其 HTTP 响应断开连接，则创建将继续；已提交的会话不会被删除，并且响应客户端会被分离。通过精确的 GET 和 load/resume 进行恢复，而不是重试创建为附加。

存档、取消存档、修复、重命名和删除与 load/resume 和提示共享相同的每会话生命周期准入。删除使用转录取消链接作为其持久提交点和私有日志以及用于崩溃恢复的原子托管目录暂存。恢复在转录本保持完整时恢复目录，并在转录本消失时完成清理；任何不匹配的身份、冲突的路径、外国所有者或不明确的记录都会返回结构化的失败关闭错误。

### `POST /session`

可选的`startupConfig`合约在[功能](<#能力>)下描述。与传统的尽力而为模型选择不同，被拒绝的启动选择将导致创建失败。

生成一个新的 agent 或附加到现有的（默认情况下在 `sessionScope: 'single'` 下）。

要求：

```json
{
  "cwd": "/absolute/path/to/workspace",
  "modelServiceId": "qwen-prod",
  "sessionId": "550e8400-e29b-41d4-a716-446655440000",
  "sessionScope": "thread",
  "worktree": { "slug": "feature-a" }
}
```

|领域|必填|注释|
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd`            |不|与一个已注册工作区匹配的绝对路径。如果省略，路线将回退到主工作区（将其读出 `/capabilities.workspaceCwd`）。不匹配的非空 `cwd` 返回 `400 workspace_mismatch`。当`features`包含`multi_workspace_sessions`时，客户端可以传递任何可信的`workspaces[].cwd`；否则仅接受主工作区。工作空间路径通过 `realpathSync.native` 规范化（仅对不存在的路径进行解析回退），因此不区分大小写的文件系统不会拒绝每个拼写的会话。|
| `modelServiceId` |不|选择 agent 将通过哪个配置的 _ 模型服务 _（后端提供商 — 阿里巴巴 ModelStudio、OpenRouter 等）。如果省略，agent 将使用其默认值。如果工作区已有会话，则会在现有会话上调用 `setSessionModel` 并广播 `model_switched`。与 `POST /session/:id/model` 上的 `modelId` 不同，后者选择已绑定服务 ** 内的模型 **。 `/capabilities`上的`modelServices`数组保留用于通告配置的服务；在阶段 1 中，它始终是 `[]`（使用 agent 的默认服务，并且未在 HTTP 上枚举）。|
| `startupConfig`  |不|`{ modelServiceId: string, reasoningEffort?: ReasoningSelection }`；需要 `session_startup_config`，创建一个新线程并确认请求的选择，而不写入共享默认值。不能与顶级 `modelServiceId` 或显式单一作用域混合。|
| `sessionId`      |不|RFC- 变体 UUID v1-v5 由调用者选择。守护进程将其标准化为小写并始终创建一个新的线程会话；它从不将该字段视为幂等附加。在发送之前确认 `caps.features` 包含 `session_id_override`，因为较旧的守护程序可能会忽略未知字段。 `null`相当于省略。|
| `sessionScope`   |不|会话共享的每个请求覆盖。 `'single'`（守护进程范围的默认值）使第二个相同工作空间`POST /session`重用现有会话（`attached: true`）； `'thread'` 每次调用都会强制进行新的不同会话。省略继承守护进程范围的默认值。枚举之外的值返回 `400 { code: 'invalid_session_scope' }`。旧守护进程（#4175 PR 5 之前）默默地忽略该字段 - 发送前飞行前 `caps.features.session_scope_override`。今天在生产中，守护进程范围的默认值被硬编码为 `'single'`； #4175后续可能会添加`--sessionScope`CLI标志。|
| `worktree`       |不|在用户命名的 Git 工作树中创建一个新的线程会话。可选的 `slug` 使用守护程序的工作树名称验证。飞行前 `session_worktree_persistence_v1`；客户不得从旧的工作树形响应中推断出持久的隔离。工作树创建不是附加操作，不能与分支创建结合使用。该路由仅在重定位、独占所有权标记创建、sidecar 持久性和最终运行时生成检查之后才返回成功。|

回应：

```json
{
  "sessionId": "<uuid>",
  "workspaceCwd": "/canonical/path",
  "attached": false,
  "worktree": {
    "slug": "feature-a",
    "path": "/canonical/path/.qwen/worktrees/feature-a",
    "branch": "worktree-feature-a"
  },
  "worktreeState": "persisted-v1"
}
```

`attached: true` 表示该工作区的会话已存在，并且您现在正在共享它。

`worktreeState` 仅适用于经过验证的工作树会话。旧版尽力还原可能会返回 `worktree`，但不返回 `worktreeState`。冷第 4A 部分恢复的恢复提示被触发而不是暂停（`suppressWorktreeContextRestore` 关闭，因此路由要求网桥不要延迟）也会返回未经验证的 `worktree` 元数据，直到提示解决：子进程在没有报告的 cwd 的情况下读取活动状态，在实时提示下不可能进行重定位，并且路由保留会话而不是杀死调用者刚刚恢复的会话。这两种响应都不是持久的执行根证明。请求工作树隔离的客户端必须在路由提示之前需要 `worktreeState: "persisted-v1"` 和预期的规范路径；活动提示稳定后可以重试初始加载。在 SDK 上，根据响应是否仍携带 `worktree` 元数据重新附加结果：携带缺少证明或更改路径的响应是该客户端的终端 — 新附件已分离且不会重试提示 — 而根本不具有 `worktree` 对象的响应将恢复：客户端删除其缓存的工作树声明，保留新附件，并且在同一呼叫中重试提示。修复不会控制重试，因此一轮仍然可以在客户端无法再证明的目录中执行（客户端从未见过的 `exit_worktree` 是常见原因，客户端无法将其与已清除的内存中关联区分开来）。因此，具有隔离意识的调用者必须在分派提示之前重新验证工作树证明本身；删除的声明只会使调用者在下一次加载或选择时无法在其自己的身份门处关闭。

调用者提供的 ID 在所有当前注册的工作区运行时和每个仍处于活动状态的桥代（包括耗尽替换）中都是唯一的。实时、待处理、活动、存档或工作树支持的重复项返回 `409 session_id_conflict`。无效值返回`400 invalid_session_id`；不可用的实时所有者或持久状态检查返回可重试 `503 session_id_admission_unavailable`。在桥接或存储运行状况发生变化后，使用有界退避重试； `retryable` 表示另一次尝试是安全的，而不是立即重试会成功。如果下游 agent 返回不同的 ID，则守护程序会删除该孤儿并返回 `500 session_id_not_honored`。在出现不明确的响应后，加载或恢复已知的 ID，而不是重试创建为附加。

想要独立对话的多客户端集成应在每个 `POST /session` 上发送 
`sessionScope: "thread"`。仅当客户端有意共享一个协作会话时，才使用默认的 `single`
 范围；共享
会话通过一个FIFO序列化提示，通过
`/daemon/status`可见，如`runtime.activity.pendingPrompts`和
`runtime.activity.queuedPrompts`。

对同一工作区的并发 `POST /session` 调用是 ** 合并 ** 到一个生成 — 两个调用者都会得到相同的 `sessionId`，只有一个报告 `attached: false`。如果底层生成失败（初始化超时、格式错误的 agent 输出、OOM），**所有合并的调用者都会收到相同的错误 ** — 正在进行的插槽被清除，以便后续调用可以从头开始重试。

> ⚠️ **`modelServiceId` 对新会话的拒绝对 
> HTTP 响应保持沉默。** 错误的 `modelServiceId`（拼写错误，未配置的服务）
> 确实 NOT 500 创建 — 会话在 
> agent 的默认模型上保持运行，因此调用者仍然会获得 `sessionId`，他们 
>  可以重试模型切换（通过 `POST /session/:id/model`）。
>  可见的失败信号是
>  会话的 SSE 流上的 `model_switch_failed` 事件，在生成握手和您的 
>  首次订阅之间触发。 **需要观察此事件的订阅者
> 应在其第一个“GET
/session/：id/events”**上传递`Last-Event-ID: 0`，以从环中最旧的可用
> 事件重播生成时间 `model_switch_failed`，即使 
>  订阅在创建响应后几毫秒到达）。

### `GET /session/:id/status`

从拥有会话的运行时返回实时摘要。此路由
不会加载仅持久会话，并且永远不会回退到主运行时。
飞行前`caps.features.session_status`。

```json
{
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "createdAt": "2026-09-10T08:00:00.000Z",
  "clientCount": 1,
  "hasActivePrompt": true,
  "isWaitingForPermission": false,
  "isWaitingForUserQuestion": false,
  "pendingInteractionCount": 0,
  "pendingInteractions": []
}
```

响应是 `DaemonSessionSummary` 线形状。可选字段包括
显示和源元数据、`activeWorkState`、`updatedAt`、`turnError`、
工作树或分支元数据以及PR绑定。 `404` 表示
不存在活着的所有者；自举、耗尽或不可用的所有者返回
`503`而不是回退。不受信任的非主要所有者返回 
`403 untrusted_workspace`，并且位于多个工作区的 ID 返回 
`500 ambiguous_session_owner`。 TypeScript SDK 方法是 `sessionStatus()`。

### ACP `session/new` 呼叫者提供的 ID

ACP 客户端通过扩展元数据字段请求相同的行为：

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "session/new",
  "params": {
    "cwd": "/absolute/path/to/workspace",
    "_meta": {
      "qwen-code/sessionId": "550E8400-E29B-41D4-A716-446655440000"
    }
  }
}
```

响应包含标准化小写字母 ID。主要和工作区合格的 ACP 安装与 REST 的共享许可，包括 `session/load` 和 `session/resume`。无效 ID 将 ACP `INVALID_PARAMS` 与 `data.httpStatus=400` 和 `data.errorKind="invalid_session_id"` 一起使用；冲突使用`data.httpStatus=409`；不可用的实时所有者或持久状态检查使用 `data.httpStatus=503` 和 `data.retryable=true`。

从未收到提示的 ACP- 创建的会话不会留下任何持久的跟踪，并且当其拥有的连接以零附加会话关闭时，守护进程会获取它。之后，可以再次创建相同的 ID — 即连接生命周期，而不是 ID 重用：当连接（或任何附件）处于活动状态时，准入会拒绝重复项。

### `POST /session/:id/load`

`liveReplayMode` 和 `compactedReplayMode` 接受 `full`（默认）或 `summary`。
它们分别投影 `liveJournal` 和 `compactedReplay`，而不更改 
 存储的历史记录。页面选择先于过滤，因此可见项目可能
少于`historyPageSize`。无效值返回 `400 invalid_live_replay_mode` 或 
`400 invalid_compacted_replay_mode`。独立加载接受这两个选项，但 
 将无效值报告为 `400 invalid_request`。

通过 id 恢复持久的 ACP 会话，并通过 SSE 重播其历史记录。路径id具有权威性；正文中的任何 `sessionId` 字段都将被忽略。飞行前 `caps.features.session_load` — 较旧的守护进程为此航线返回 `404`。

要求：

```json
{
  "cwd": "/absolute/path/to/workspace"
}
```

|领域|必填|注释|
| ----- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd` |不|与 `POST /session` 相同的规范化 + `workspace_mismatch` 规则。省略继承`/capabilities.workspaceCwd`。当`features`包含`multi_workspace_sessions`时，调用者可以传递任何可信注册的`workspaces[].cwd`；不受信任的非主工作区返回 `403 untrusted_workspace`。 `mcpServers` 故意在这里接受 NOT — 守护进程范围的 MCP 是设置驱动的（匹配 `POST /session`）。|

回应：

```json
{
  "sessionId": "persisted-1",
  "workspaceCwd": "/canonical/path",
  "attached": false,
  "state": {
    "models": { ... },
    "modes": { ... },
    "configOptions": [ ... ]
  }
}
```

`state` 镜像 ACP 的 `LoadSessionResponse` — `models` 是 `SessionModelState`，`modes` 是 `SessionModeState`，`configOptions` 的数组`SessionConfigOption`。缺失的字段由 agent 决定。后期附加程序（下面的 `attached: true` 路径）获取原始加载调用者看到的 SAME `state` 快照 - 守护进程将其缓存在条目上；运行时突变（例如 `model_switched`）在 SSE 流上传递，而不是在后续附加响应上传递。

`attached: true` 表示会话已经存在（要么来自之前的 `session/load`/`session/resume`，要么因为合并的并发调用者就在前面）。

当持久会话拥有第 4A 部分工作树时，load/resume 验证其 sidecar、规范包含和精确签出标记。空闲子进程在响应之前被重新定位；仅当其报告的 cwd 已经等于工作树时才证明活动子项，并且报告的 cwd 不存在或在其他地方失败关闭而不是在其提示下移动的活动子项 - 无法推迟其恢复提示的冷恢复除外（`suppressWorktreeContextRestore` 关闭 - 有效源不属于 Channel 的任何恢复 - 或没有延迟 API 的桥）：该形状仍然返回`worktree` 没有 `worktreeState`，未重定位，并且会话仍然存在。重新定位和证明的响应返回 `worktree` 加上 `worktreeState: "persisted-v1"`。恢复端类型 409 分类（由 `session_worktree_reset_v1` 公布）：`worktree_session_superseded` 当会话的 sidecar 带有 `supersededBy` 链接时 - 分类在每次签出所有权锁定下且在任何标记读取之前单独读取该链接，因此在加载等待时回滚的传输无法将调用者重定向到相同的会话守护进程正在删除；不过，链接是在标记翻转之前写入的，因此主体的 `replacementSessionId` 是一个用于验证的重定向，而不是证明所有权已移动：在提交的转移之后，调用者应该加载该 id 并更新其簿记，但在预提交中断状态下，它会指定一个不是标记所有者的替代者，其自己的恢复返回 `worktree_reset_interrupted`，并且重试的重置然后会收获 - 所以`replacementSessionId` 在任务加载成功之前不得保留为任务的会话 ID； `worktree_reset_interrupted` 当恢复会话的 `supersedes` 链接和指定会话的 `supersededBy` 链接一致但标记从未移动或不存在时 — 之前的重置在传输过程中崩溃，重试重置即可修复； `worktree_marker_missing` 当没有一致的链接对时检出标记不存在 - 重置任务就是修复，因为没有恢复路径重新创建标记，并且恢复重试返回相同的 409。首先检查中断的分类，因此一致的链接对永远不会以 `worktree_marker_missing` 的形式出现。每当有效恢复源为 Channel 所有时，路由都会抑制 agent 对第 4A 部分或不可分类的 sidecar 状态的最大努力清理，因此陈旧、外来、不明确或所有权不匹配的持久状态将被保留并使请求失败。持久化源元数据优先；当它不存在时，load/resume请求提供有效源。没有第 4A 部分 `workspaceCwd` 字段的结构上有效的遗留 sidecar 保留现有的尽力而为的 agent 恢复，可以通过该路径进行清理，并且可以返回没有 `worktreeState` 的 `worktree`；客户端不得推断与该兼容性元数据的隔离。除了明确的遗留兼容性情况之外，只有有效恢复源不属于通道所有的会话才会在路由验证之前保留现有的尽力清理。丢失的 sidecar 不会返回任何工作树证明；具有隔离意识的客户端必须拒绝该响应，而不是将会话重新绑定到共享工作区。

**通过 SSE 进行历史回放。** 当 `loadSession` 在 agent 侧飞行时，agent 可能会发出 `session_update` 持续转弯通知，或在响应中返回批量重播更新元数据。在路由响应返回之前，守护进程将这些事件植入会话的有界重播快照窗口中。对于实时会话，`POST /session/:id/load` 仅承诺有界窗口（`compactedReplay`、`liveJournal`、`lastEventId`），而不是完整的转录本。窗口字节上限为`--compacted-replay-max-bytes`（默认4MiB，最大256MiB）；如果旧的重播条目被删除，`compactedReplay[0]` 是一个无 id 的 `history_truncated` 标记。进行中的 `liveJournal` 分别由 `--max-journal-events`（默认 10 000 重播条目）和 `--max-journal-bytes`（序列化源事件的默认 8 MiB）分别限制。这些是每个会话 ** 基线 ** 上限。当运行中的轮流超出它们时，守护进程首先尝试自适应增长：它将会话的上限提高到两倍（最高为 256 MiB 的每个会话硬上限，条目按比例缩放，受剩余池净空限制），而每个实时会话授予的增长适合大小为 5% 的一个守护进程范围的增长池。守护进程的有效内存预算 — 传递时的 `--memory-budget-mb` 值，上限为解析的可用内存，否则自动检测到的内存的 50% — 上限为 `1024` MB。会计是守护进程范围内的——多工作空间守护进程为每个工作空间运行一个桥，并且所有工作空间共享一个池。增长是按需增长的，并且只在池允许的范围内；操作员固定的 `--max-journal-events` 或 `--max-journal-bytes` 会禁用它，有效预算低于 1024 MB 最小值 (`insufficientMemory`) 的主机也会禁用它：池是 0 并且自适应增长被彻底禁用。连续兼容的 `agent_message_chunk` 或 `agent_thought_chunk` 源事件共享一个重播条目，每个条目最多 256 个源事件，而工具、归属、出处和离散消息边界保持不变。当日志在增长后仍然超出池允许的（可能增长的）上限时 - 包括没有授予净空空间或拨款仅涵盖部分超调时 - 最旧的条目将被整个删除（因此保留的尾部可能比字节上限小得多），并在前面添加带有 `scope: 'live_journal'` 的 `history_truncated` 标记；其 `truncatedEvents` 和 `retainedEvents` 字段计数源事件，而不是重播条目，其 `maxBytes` / `maxEvents` 反映有效的上限（可能已经增加）。客户端应将该标记呈现为状态并继续应用保留的事件。完整的持久转录本访问通过 `GET /session/:id/transcript` 单独公开。

重播窗口字节上限在子进程重建持久记录后应用；它们不限制磁盘上的 JSONL 读取。超出守护程序预算的恢复将返回 `504`，其中包含从恢复预算（限制为 5-120 秒）和 `{code: "session_restore_timeout", errorKind: "restore_timeout", retryable: true, sessionId, action, timeoutMs}` 派生的 `Retry-After`。守护进程会隔离仍在运行的 ACP 请求并清理所有延迟的会话，而不是注册它。对同一 ID 的重试将返回 `409 restore_in_progress`、`reason: "awaiting_abandoned_cleanup"` 和恢复预算的 `Retry-After`（限制为 5-120 秒），直到清理解决。如果后期恢复清理不确定，或者放弃的恢复在截止日期后仍未解决完整恢复预算，则该工作区上的新会话将返回 `503 acp_channel_unavailable` 和 `reason: "restore_cleanup_failed"` 或 `"restore_settlement_overdue"`。超时会话初始化遵循与晚解决的较旧的 ACP 子级相同的失败关闭准入策略：不确定的清理返回 `reason: "new_session_cleanup_failed"`，而尚未解决的进一步初始化预算的请求返回 `reason: "new_session_settlement_overdue"`。逾期结算状态在迟到故障解决后或迟到成功完成其确切的ID清理后立即清除；不确定的清理会转换为相应的清理失败状态。清理失败状态持续到工作空间通道耗尽并被回收为止。在这两种情况下，已经存在的会话仍然可用。

**错误：**

- `404` — 持久会话 ID 不存在 (`SessionNotFoundError`)。
- `400` — `workspace_mismatch`（与 `POST /session` 形状相同）。
- `403` — `untrusted_workspace`，当 `cwd` 面向不受信任的非主工作区时。
- `503` — `session_limit_exceeded`（对 `--max-sessions` 进行计数；进行中的恢复也被考虑在内）。
- `504` — `session_restore_timeout`；可重试，具有从恢复预算派生的 `Retry-After`（限制为 5-120 秒），因为相同的会话 ID 保持隔离状态，直到后期清理解决。
- `504` — `init_timeout`； NOT可重试，无`Retry-After`，无`sideEffectPossible`，未安装围栏。在发送恢复请求之前通道初始化超时时发出（`ensureChannel` 阶段）；从未尝试过恢复，因此没有会​​话 ID 被隔离，也没有待处理的清理。
- `503` — `acp_channel_unavailable` 当工作区通道对新会话工作关闭时。 `reason` 说明原因：`restore_cleanup_failed` 当无法最终清理放弃的恢复时； `restore_settlement_overdue` 当放弃的恢复在截止日期后仍未解决完整的恢复预算时； `new_session_cleanup_failed` 当公共初始化超时后创建的会话无法最终关闭时；或 `new_session_settlement_overdue` 当超时初始化在其截止时间后仍未解决进一步的初始化预算时。现有会话仍然可用。在具有多个执行引擎的工作区运行时，任何这些引擎通道的隔离：其会话也不进行新的工作（提示、延续、侧面请求、shell 命令、中间轮消息以及目标或工作流启动返回相同的 `503`），已确定的会话将关闭，并且该引擎的新会话将保持拒绝状态，直到通道的进程树消失为止，其中 `channel_exit_unverified` 无法确认。其中，`workspace_change_unacknowledged` 表示通道未确认影响会话的工作区更改（权限规则、设置重新加载、会话工作流门、模型提供程序或技能）；对每一个缺失的变更的迟到确认会提前结束隔离。在迟来的失败解决或迟来的成功完成其精确的ID清理后，结算逾期状态将被清除；不确定的清理会转换为匹配的清理失败状态，这需要工作空间通道进行排空和回收。主体带有 `retryAfterSeconds`，标头带有匹配的预算派生的 `Retry-After`，因此客户端使用操作预算规模退避，而不是以普通的 5 秒节奏进行轮询。
- `409` — `restore_in_progress`（同一 ID 的 `session/resume` 已在飞行中，或者新生成的 ID 提供了恢复所拥有的 ID）。 `Retry-After: 5` 当恢复处于活动状态时；一旦被屏蔽为 `awaiting_abandoned_cleanup`，就会得到预算衍生的提示。相同动作竞赛（同一 ID 的两个并发 `session/load`）合并 — 恰好一个返回 `attached: false`，其余返回具有相同 `state` 的 `attached: true`。
- `409` — `session_workspace_conflict` 当相同的会话 ID 已经存在或正在由另一个工作区运行时恢复时。
- `409` — `session_archived`，当 id 仅存在于 `chats/archive/` 下时；在 `load` 或 `resume` 之前调用 `POST /sessions/unarchive`。
- `409` — `session_archiving` 当同一 ID 正在进行存档或取消存档时。 `Retry-After: 5`。
- `409` — `session_conflict`，当 id 存在于 `chats/` 和 `chats/archive/` 中时；加载之前删除带有 `POST /sessions/delete` 的会话。

### `GET /session/:id/transcript`

返回一页无 id `session_update` 重播帧，该帧是根据活动的持久 JSONL 转录本重建的。飞行前 `caps.features.session_transcript` — 较旧的守护进程为此航线返回 `404`。

查询参数：

|领域|必填|注释|
| --------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cursor`              |不|上一页返回的不透明base64url cursor。省略第一页。 cursor由守护进程发布并经过篡改检查；修改它会返回 `400 invalid_transcript_cursor`。它绑定到转录文件身份和冻结第一页字节大小；删除、截断、替换或归档文件会使其无效并返回 `409`。|
| `limit`               |不|页面中活动 `ChatRecord` 的目标数量。默认为 `100`，最大为 `500`。向后页最多可以扩展到 `3 * limit` 条记录，以保留转向和 tool-call/result 边界。一条记录可以产生多个重放帧，因此 `events.length` 可能会更大。无效值返回 `400 invalid_transcript_limit`。|
| `direction`           |不|唯一接受的值是 `backward`，它从最新记录开始一页。省略该参数可在第一页上向前翻页，或继续按`cursor`中冻结的方向。不能与`cursor`、`beforeRecordId`、`atRecordId`或`snapshot`组合使用。|
| `beforeRecordId`      |不|在此记录 ID 之前开始向后翻页。意味着向后，所以不要同时发送 `direction`。不能与`cursor`或`atRecordId`组合使用；可与 `snapshot` 配对。|
| `compactedReplayMode` |不|`full`（默认）或`summary`。选择页面后投影 `events`，因此可见计数可能小于 `limit`。不更改存储的历史记录或 cursor 选择。也被工作区合格的转录路线所接受。无效值返回 `400 invalid_compacted_replay_mode`。|

回应：

```json
{
  "v": 1,
  "sessionId": "persisted-1",
  "events": [
    {
      "v": 1,
      "type": "session_update",
      "data": {
        "sessionUpdate": "user_message_chunk",
        "content": { "type": "text", "text": "..." }
      }
    }
  ],
  "nextCursor": "opaque",
  "hasMore": true,
  "startTime": "2026-07-08T00:00:00.000Z",
  "lastUpdated": "2026-07-08T00:01:00.000Z"
}
```

`events` 仅是重播帧：`{ v: 1, type: "session_update", data: SessionUpdate }`。它们不携带 EventBus id，并且响应从不包含 `lastEventId`。调用此路由不会调用 `/load`、附加客户端、播种实时 EventBus、创建实时会话或更改当前实时重播窗口。活动会话和非活动会话均由子端只读状态方法重建，因此重播使用相同的工作区设置、运行时输出目录、发射器和 `/load` 历史语义，而无需改变守护进程会话状态。

第一页冻结当前的 JSONL 快照大小。后面的页面仅读取该字节前缀，因此在页面 1 之后附加不会更改结果集。如果文件消失、被截断为低于冻结大小、被替换为不同的索引节点或移至存档，则下一页将返回 `409`，并且客户端应从页面 1 重新启动或要求用户重新打开记录。

为了保护守护进程内存和延迟，在守护进程扫描 JSONL 之前，高于转录索引上限的快照会失败。客户端收到 `413 transcript_too_large` 并应回退到 export/offline 处理或要求用户 shorten/archive 较旧的历史记录。

如果在生成一些帧后重播转换失败，可能会出现`partial: true`和`replayError`。部分响应从不包含 `nextCursor`，因此客户端无法默默地对未转换的过去记录进行分页。

**错误：**

- `400` — 无效的 `limit`、`cursor`、`direction`、`beforeRecordId` 或会话 ID 形状。寻呼参数互斥：`cursor`不能与`beforeRecordId`、`atRecordId`、`snapshot`组合使用； `snapshot`需要`atRecordId`或`beforeRecordId`； `atRecordId`需要`snapshot`； `backward` 不能与 cursor 或任何记录锚点组合。
- `404` — 第一个页面请求中不存在活动的持久会话 ID。
- `409` — `session_archived`、`session_archiving` 或 `session_conflict` 来自与 `/load` 相同的可负载性检查。
- `409` — 副本快照不可用，因为在发出 cursor 后文件被删除、截断、替换或存档；当预检无法再找到 cursor 请求的活动文件时，这也适用。
- `413` — `transcript_too_large` 当冻结的转录本快照超出守护程序索引上限时。
- `413` — `transcript_page_too_large` 当一条聚合记录超出工作区限定的页面预算或序列化页面超出其响应预算时。

### `GET /session/:id/export`

下载主工作区的活动持久会话脚本。 
可选 `format` 查询为 `html`（默认）、`md`、`json` 或 `jsonl`.
飞行前 `caps.features.session_export`。

成功的响应是带有经过清理的文件名 
`Cache-Control: no-store` 和 `X-Content-Type-Options: nosniff` 的附件。根据所选格式，内容
类型为`text/html`、`text/markdown`、`application/json`或
`application/jsonl`。该路由仅读取
持久存储：它不会解析实时所有者、启动ACP或附加
客户端。当目标可能位于
非主工作空间中时，请使用下面的工作空间限定路径。无效格式返回 `400 invalid_export_format`；
缺少活动会话返回 `404`；已归档、正在转换或冲突的
存储返回`409`。 TypeScript SDK 方法是 `exportSession()`。

### `GET /workspaces/:workspace/session/:id/transcript`

返回相同的 `DaemonSessionTranscriptPage` 投影作为来自所选注册工作空间的活动持久 JSONL 的单一路线。飞行前 `workspace_persisted_transcript`；此功能独立于 `multi_workspace_sessions`，适用于由 id 或 cwd 选择的受信任的单工作区主数据库。

选择器和查询参数遵循现有的复数工作区和转录规则。受信任的主要和辅助运行时以及不受信任的辅助运行时可以读取。不受信任的主节点返回 `403 untrusted_workspace`。存档内容不会返回。

对于此工作区限定路由，`limit` 是转发页面的最大记录计数。向后的页面最多可以扩展到 `3 * limit` 条记录，因此轮数和 tool-call/result 对保持不变。页面可能会提前停止在 4 MiB 持久源预算并返回延续 cursor。序列化响应的上限为 32 MiB，光标位于 64 KiB。如果重播状态超出 cursor 上限，页面将返回其成功转换的事件，其中包含 `partial: true`、`hasMore: false`，但不返回 `nextCursor`。

转发页面和cursor页面完全在守护进程内实现。实时会话的向后第一页首先跨越单记录工作区桥刷新屏障，因此持久尾部是当前的；该屏障可能会启动 ACP 并加载工作区设置。持久页面读取本身不会解析项目定义的 agents 或技能或 create/repair `session-transcript-cursor-key`。工具框架使用持久的工具名称和描述，而无需咨询运行时工具注册表。它的 HMAC cursor 密钥仅存在于守护程序内存中，每个工作区都是隔离的，并在重新启动时轮换；来自先前守护进程的 cursor 返回 `400 invalid_transcript_cursor`。

### `GET /workspaces/:workspace/session/:id/export`

将选定的已注册工作区的活动持久会话导出为附件。飞行前 `workspace_session_export`；不要从 `session_export` 或 `workspace_qualified_rest_core` 推断支持。选择器首先解析为精确的工作区 ID，然后在规范化后解析为 URL- 编码的绝对 cwd。主运行时和辅助运行时都必须可信。不受信任的运行时在会话或格式验证之前返回 `403 untrusted_workspace`。

可选的 `format` 查询为 `html`（默认）、`md`、`json` 或 `jsonl`。正文、MIME 类型、文件名清理、`Cache-Control: no-store`、`X-Content-Type-Options: nosniff` 和附件处理匹配 `GET /session/:id/export`。旧路由仍然绑定到主存储。

复数路径仅读取现有共享存档协调器下选定工作区的活动持久 JSONL。它不会扫描其他工作区存储、回退到主工作区、解析实时所有者、调用工作区桥、启动 ACP、附加客户端或加载设置。仅存在于另一个工作空间中的会话 ID 返回 `404 { code: "session_not_found" }`；存档会话返回 `409 session_archived`。无效格式返回 `400 invalid_export_format`，存储竞争保留现有的 `session_archiving` 和 `session_conflict` 错误。

### `GET /workspaces/:workspace/session/:id/archive/export`

将选定的已注册工作区的存档持久会话导出为附件。飞行前 `workspace_archived_session_export`；不能从主动出口或多元化核心能力中推断出支持。工作区选择器解析和信任检查在会话 ID 和格式验证之前运行。

TypeScript SDK 呼叫者使用 `WorkspaceDaemonClient.exportArchivedSession(sessionId, options)`。该方法始终使用本机 REST 并返回现有的 `DaemonSessionExportResult` 附件投影。

可选的 `format` 查询、响应正文、MIME 类型、清理文件名、缓存策略、安全标头和附件配置与活动工作区导出相同。重建前，存档源 JSONL 的上限为 256 MiB；较大的文件返回 `413 transcript_too_large` 以及 `sessionId`、`snapshotSize` 和 `maxBytes`。活动导出保持其现有的大小行为。

该路由仅读取共享存档协调器租约下选定的受信任工作区中的 `chats/archive/<id>.jsonl`。它不会检查活动内容的回退、扫描另一个工作区、解析实时所有者、调用桥、启动 ACP、附加客户端或加载设置。仅活动 ID 返回 `409 { code: "session_not_archived" }`；缺少 id 返回 `404 { code: "session_not_found" }`；同时活动和归档文件返回 `409 session_conflict`；并且存档转换返回 `409 session_archiving` 和 `Retry-After: 5`。

### `POST /session/:id/resume`

通过 id WITHOUT 恢复持久的 ACP 会话，通过 SSE 重播历史记录。模型上下文在 agent 端内部恢复（通过 `geminiClient.initialize` 读取 `config.getResumedSessionData`）；对于已经呈现历史记录的客户端来说，SSE 流保持干净。飞行前 `caps.features.session_resume`； `unstable_session_resume` 仍然是旧客户端的已弃用兼容性别名。

接受与 `/load` 相同的 `cwd`、`approvalMode`、`sourceType` 和 `sourceId` 字段 
。仅加载重播字段 `historyPageSize`、`liveReplayMode`、
 和 `compactedReplayMode` 不会在此处解析、验证或转发 — 它们 
 会被静默忽略，与已发布的恢复请求架构相匹配。相同的 
 响应形状 — `state` 镜像 ACP 的 
`ResumeSessionResponse`。相同的错误包络线，包括
`409 restore_in_progress`（当`session/load`飞行时触发；
`session/resume`在另一个`session/resume`后面赛车合并）。

当客户端没有渲染历史记录时使用`/load`（冷重新连接，选择器→打开）。当客户端已经打开屏幕并且只需要后台进程句柄时，请使用 `/resume`。

> ⚠️ **为什么`unstable_session_resume`仍然通告？**守护进程的HTTP路由和`session_resume`能力对于v1来说是稳定的，但网桥仍然调用ACP的`connection.unstable_resumeSession`。保留旧标签只是为了让 `session_resume` 之前发布的 SDK 可以继续工作。

### `POST /session/:id/worktree-reset`

将持久的第 4A 部分工作树会话的签出所有权转移到新的替换会话。飞行前 `session_worktree_reset_v1` — 较旧的守护进程为此航线返回 `404`。该路线是频道命名任务 `/clear` 如何重置工作树任务而不丢弃其文件：替换获得一个全新的对话，并且被取代的会话将其副本保留在目录中，但永远无法再次恢复。

请求正文（所有字段可选）：

```json
{
  "cwd": "/canonical/path",
  "modelServiceId": "service-id",
  "approvalMode": "plan",
  "sourceType": "channel",
  "sourceId": "dingtalk-main"
}
```

`cwd` 遵循与 `POST /session/:id/load` 相同的分辨率，并且对于守护程序的主工作区可以省略。元数据字段使用与工作树创建相同的线程范围和源约定来标记替换会话。

成功后，路由将返回 `200` 以及替换会话的创建形状响应 — 新的 `sessionId`、`workspaceCwd`、`currentCwd` 等于已验证的规范工作树路径、`worktree` 元数据和 `worktreeState: "persisted-v1"`。调用者丢弃旧 ID 上的句柄，并从此使用替换 ID；旧 ID 的后续恢复会返回 `worktree_session_superseded` 和 `replacementSessionId`，客户端也可以使用它来自我修复陈旧的簿记 — 但只有在该 id 加载成功后。分类仅由 sidecar 链接决定，并且该链接是在标记翻转之前写入的，因此中断的预提交传输将返回一个不是标记所有者的替代者，其自己的恢复返回 `worktree_reset_interrupted`，然后重试重置即可收获。

该路由永远不会读取 `X-Qwen-Client-Id`，因此替换会独立生成 - 但成功响应并不是免注册的。新的传输会为该生成创建一个所有者风格的 `clientId`，将其注册到替换对象上，并在正文中报告它；已提交传输的幂等恢复不会报告任何内容。铸造的 id 不是附件（替换的附加计数不变，因此 `killSession` 和 `requireZeroAttaches` 仍然将其视为未附加），但实时客户端注册正是阻止守护程序空闲清理的原因：分离您不继续使用的 id，否则替换将无限期地保持活动状态 - 而仍然有效且没有标记的替换是稍后重置拒绝的形状拆除。将响应视为替代者的身份，并在需要注册客户端时通过正常的 `POST /session/:id/load` 或 `/resume` 表面附加它。

每次针对工作树恢复和其他重置的签出都会序列化传输，拒绝在任一会话繁忙时运行（在飞行中提示或待处理的交互），并在被取代的会话上设置准入屏障：当它已设置时，恰好有八个写入者在准入时被 `worktree_reset_active` 拒绝 - `POST /session/:id/prompt`、`/rewind`、 `/cd`、`/branch`、`/fork`、`/shell`、`/goal` 和 `/tasks/:taskId/workflow-action`。其他七个被隔离，因为每个都移动会话 cwd 或在没有通过提示准入的情况下开始其中的工作：shell 命令在会话的有效 cwd 中运行，对于重新定位的工作树会话来说，它是结帐本身；一个 fork agent 在该 cwd 中运行其工具；倒回恢复与其相关的文件； CD 将其移动，包括移动到结账的子目录中；当所有权不断变化时，分支会改变被取代会话的持久历史并产生派生会话；目标 `resume` 促进排队轮次并对延续进行排队；工作流操作通过会话自己的工具注册表运行已保存的工作流或调用者提供的脚本，或重新启动实时运行。 `POST /session/:id/continue` 不是第九个门：它通过围栏提示入口驱动已接受的延续，并在那里被拒绝。该列表是整个栅栏，栅栏并不是到达子进程的所有内容 - 释放和停止路径在设计上保持可用（取消任务，清除目标，分离客户端，终止会话），并且守护进程内部后台通知排队 - 子会话对其父进程的完成确认，没有 HTTP 路由调用 - 也是不受保护的，因为它排队通知而不是开始转弯。传输的最后一步将切断被取代会话的客户端注册，清除其内存中的工作树关联，并报告被取代会话是否实际上已消失。一个幸存者——一个仍然持有后台工作的孩子，所以最后一个分离触发器的空闲关闭被拒绝和推迟——被浮出水面，而不是被掩盖：守护进程记录它，屏障在该入口上保持武装，作为唯一剩下的栅栏，并且`200`主体携带`supersededSessionLive: true`，因此调用者知道被取代的ID仍然有效，并且可以在所有权刚刚移动的结帐内重新附加。崩溃安全性是针对每个窗口的，写入顺序定义了窗口：首先写入旧 sidecar 的 `supersededBy` 链接，然后写入带有 `supersedes` 的替换 sidecar，最后翻转标记。因此，翻转之前的崩溃会使旧会话以重试区分的三种形式之一保持权威 - 根本没有链接（替换是普通的孤儿，重试只是开始新的传输），仅向后链接，而替换的 sidecar 丢失（重试失败，以 `worktree_reset_invalid_state` 关闭，并保持中断状态不变以供操作员修复），或两个链接都存在（重试将部分传输回滚并完成新的传输 - 除非中断无法删除替换，因为客户端仍然连接到它，在这种情况下，回滚失败，以 `worktree_reset_invalid_state` 关闭并保留两个链接，因此一旦该客户端消失，稍后的重试就会收敛）。翻转后的崩溃使替换具有权威性，并且重试以无操作的方式恢复。旧会话永远不会被删除，工作树签出永远不会被重置删除。

**错误：**

- `404` — 当会话记录不存在时为 `session_not_found`。
- `409` — `worktree_reset_unsupported` 当会话没有有效的第 4A 部分 sidecar（不是工作树会话）时。
- `409` — `worktree_reset_active` 当涉及传输的会话繁忙时；稳定后重试。在会话中间传输中，允许屏障涵盖的任何编写器（提示、倒回、cwd 更改、分支、分叉、shell 命令、目标控制或工作流任务操作）失败，并出现相同的代码。
- `409` — `worktree_reset_invalid_state` 表示损坏的 sidecar、无效标记、遏制失败或不一致的取代链路对。对于此请求开始的内容来说是非破坏性的：此请求开始的任何部分传输都会在调用者看到它之前回滚，而失败关闭的恢复分支 - 无效的标记、不同意的取代链接对（包括缺少替换边车的向后链接）、不明确的标记所有者以及在标记不存在时仍在此守护进程上运行的替换 - 保持预先存在的中断状态不变以供操作员修复，以便形状在以后的重置或恢复中保持可见。针对这些状态之一的重试会重新读取它并返回相同的 409：它不会收敛，因此该状态需要运算符修复而不是重试循环。具体原因是写入守护进程日志而不是线路。
- `500` — 失败关闭的内部错误（例如没有重置支持的桥接器，或回滚后重定位拒绝）；不可按原样重试。一个`500`在种类上有所不同：标记传输其自己的提交后尾部在_之后失败_翻转持久提交报告所有权已移动，从不运行破坏性回滚，并通过重试重置来修复，这会幂等地恢复提交的传输。

### `GET /workspace/:id/session-info` 和 `GET /workspaces/:workspace/session-info`

返回所选工作区的聚合持久会话计数，而不更改分页会话列表路径：

```json
{
  "active": 450,
  "archived": 30,
  "total": 480,
  "live": 2,
  "expensive": true,
  "cost": "disk_scan"
}
```

`active`、`archived` 和 `total` 对用户可见的本地 JSONL 会话进行计数；内部守护程序主机会话和工作区-agent 任务会话（`sourceType` `agent-host` 和 `agent`）不包括在这些计数和每个会话列表中；某人仍然通过 id 打开 agent 会话。 `live` 是匹配的用户可见内存桥计数，对于已注册的不受信任的辅助工作区被省略，因为仅持久读取不得查询实时状态。 `expensive` 始终为 `true`，`cost` 始终为 `"disk_scan"`；客户端必须很少调用此端点而不是轮询它。如果扫描达到其安全限制或无法对每个候选文件进行分类，则响应会添加 `"truncated": true` 并且保留的计数为下限。丢失存储返回零持久计数。复数路由使用与复数会话目录相同的工作空间选择器和信任策略；不受信任的主节点仍返回 `403 untrusted_workspace`。

TypeScript 守护进程 SDK 通过 `workspaceById(...)` 或 `workspaceByCwd(...)` 公开复数路由，后跟 `getWorkspaceSessionInfo()`。

### `GET /workspace/:id/sessions` 和 `GET /workspaces/:workspace/sessions`

列出规范工作区与 `:id` 或 `:workspace` 匹配的会话。路径参数首先解析为精确的工作区 ID，然后解析为 URL- 编码的绝对 cwd。主要工作区包括现有的 persisted/live 合并：默认列表是来自 `chats/` 的活动会话；传递 `archiveState=archived` 以列出 `chats/archive/` 中的存档会话。受信任的非主工作区包括来自其自己的 `chats/` 存储的活动持久会话，并合并匹配的实时摘要，无需重复；如果不存在活动的持久会话，则路由将保留之前的仅实时 cursor 行为。受信任的非主工作区还支持 `archiveState=archived`、组织的 `view=organized` 列表和 `group` 过滤器，从它们自己的 `chats/`、`chats/archive/` 和会话组织存储中读取；组合的 `view=organized&archiveState=archived` 查询仅返回没有实时合并的存档会话。注册的不受信任的非主工作区支持相同的列表、过滤器和分页形状，但仅返回持久条目：守护程序不会查询实时桥或从运行时填充挂起的交互、转向错误或客户端状态。为了线路兼容性，保留的默认值（例如 `clientCount: 0` 和 `hasActivePrompt: false`）仍然存在。缺少存储会返回一个空列表。对于不受信任的主路由，复数路由仍返回 `403 { code: "untrusted_workspace" }`；旧主路由保留其现有的兼容性行为。 v1 不支持 `archiveState=all`。主列表和持久支持列表保留现有的数字 `cursor` 语义；无持久可信非主要实时回退保持其现有的不透明实时 cursor。

```bash
curl http://127.0.0.1:4170/workspace/$(jq -rn --arg c "$PWD" '$c|@uri')/sessions
curl http://127.0.0.1:4170/workspace/$(jq -rn --arg c "$PWD" '$c|@uri')/sessions?archiveState=archived
curl http://127.0.0.1:4170/workspaces/<workspace-id>/sessions
```

公布 `workspace_qualified_rest_core` 后，工作区范围的会话批量操作、组 CRUD 和会话组织突变在 `/workspaces/:workspace/sessions/{delete,archive,unarchive}`、`/workspaces/:workspace/session-groups` 和 `/workspaces/:workspace/session/:id/organization` 下可用。对于不受信任的辅助节点，组 GET 仍然可用；每个团体、会议和组织的突变仍然受到信任控制。无工作空间的批处理和组织突变路由仅保留主要工作空间以实现兼容性。

查询参数：

|领域|必填|注释|
| -------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `archiveState` |不|`active`（默认）或`archived`。任何其他值都会返回 `400 { code: "invalid_archive_state" }`。|
| `cursor`       |不|来自先前响应的分页 cursor。|
| `size`         |不|页面大小。无效值返回 `400 { code: "invalid_cursor" }` 或现有的页面大小验证。|
| `view`         |不|省略旧的最近列表。 `organized` 选择服务器端 pinned/group 排序并添加可选的组织字段。任何其他值都会返回 `400 { code: "invalid_session_view" }`。|
| `group`        |不|仅对 `view=organized` 有意义。 `all`（默认）、`pinned`、`ungrouped` 或自定义组 ID。未知组 ID 返回 `404 { code: "group_not_found" }`。|

回应：

```json
{
  "sessions": [
    {
      "sessionId": "<uuid>",
      "workspaceCwd": "/canonical/path",
      "createdAt": "2026-05-17T08:30:00.000Z",
      "displayName": "My Session",
      "clientCount": 2,
      "hasActivePrompt": false,
      "isArchived": false
    }
  ],
  "nextCursor": 1772251200000
}
```

对于 `view=organized`，守护进程读取 `<Storage.getProjectDir(cwd)>/session-organization.v1.json`，首先返回固定会话，然后按降序排列活动时间，然后返回 `sessionId` 以获取稳定的关系。组织好的 cursor 是不透明的 base64url JSON，并且不得与旧的最近列表一起重复使用。 `pinned` 是一个虚拟过滤器，而不是一个组。 `groupId: null` 表示未分组。存档会话保留其组织元数据，但 `archiveState=archived&view=organized` 仍然仅返回存档会话。

活动排序游标 — 组织视图和 `parentSessionId` / `sourceType` 过滤列表 — 不是快照隔离的，并且受信任的活动列表按转录本时间和实时活动水印中较晚的一个对行进行排序。实时水印仅存在于内存中，因此当实时条目在两次页面获取之间退出时，会话的密钥可以回归到其创建时间。 cursor 进行了补偿：它携带已在实时派生密钥处发出的身份 - 当页面集合中不存在该行时保留它们，并且引脚翻转可以重新接纳它们 - 并在传递的其余部分中排除它们，因此实时派生密钥移动每次传递最多返回一次会话。保证的范围仅限于进位：它以 64 身份为界（一次传递中的多余身份会降级为最多一次重复而不是错误），并且在其引脚状态更改之前发出的仅持久行永远不会被携带，因此在提取之间取消固定可以再次返回该行，就像该字段存在之前一样。因此，累积页面的调用者应始终按 `sessionId` 对行进行键控，而不仅仅是在超过 64 的情况下。在并发活动下，行仍然可以移动或跳过，就像以前一样；需要连贯视图的调用者会在活动更改后从第一页重新加载。

当 `view=organized` 时，每个会话上可能会出现其他字段：

```json
{
  "isPinned": true,
  "pinnedAt": "2026-07-04T12:00:00.000Z",
  "groupId": "018f..."
}
```

可信活动列表包括实时守护程序覆盖字段，例如 `clientCount`、`hasActivePrompt` 和 `activeWorkState`。不受信任的辅助列表和存档列表仅用于存储：实时覆盖字段保持不存在或为假，并且存档条目将 `isArchived` 设置为 `true`。当不存在会话时为空数组（不是 404） - 会话选择器 UI 不应仅仅因为工作区空闲而出错。

### `POST /sessions/catalog`

在一次请求中读取多个已注册工作区的独立分页会话目录。飞行前 `session_catalog_batch` 一次；旧的守护进程保留工作区限定的会话列表和组端点。每个批处理成员都具有持久的工作空间所有权：身份验证和请求准入是进程全局的，而每个条目仅解析和读取其所属的工作空间。它从不注册工作区、创建会话、启动 ACP 或回退到主运行时。

```json
{
  "workspaces": [
    { "workspace": "workspace-a", "cursor": "previous-a-cursor" },
    { "workspace": "/canonical/workspace-b" }
  ],
  "options": {
    "size": 20,
    "archiveState": "active",
    "view": "organized",
    "group": "all"
  },
  "includeGroups": true
}
```

`workspaces` 是 1–20 `{ workspace, cursor? }` 条目的有序数组，或 `"all"` 用于选择所有公共注册工作区。选择器在规范绝对路径之前解析为精确 id。 `all` 排除内部对话和已删除的条目，包括暂时不可用的条目，如果选择多于 20 的条目，则会失败；然后调用者必须拆分显式选择。空的公共注册表将返回空的结果。显式内部选择器返回条目错误而不启动内部运行时。

共享`options`接受`size`（整数1–100，默认20），`archiveState`，`view`，`group`， `parentSessionId`、`sourceType` 和 `sourceId` 以及现有列表过滤器和组合。 `group` 和 `parentSessionId` 仅限于 256 字符。 `includeGroups` 默认为 false。每个工作区都有自己的cursor。批量默认页面使用活动排序和不透明光标，包括仅实时会话；这些游标不能与传统数字列表游标互换。 Continue 该条目返回 `cwd` 和 `nextCursor`，保留过滤器。没有全局排序或全局分页cursor。

```json
{
  "workspaces": [
    {
      "workspace": "workspace-a",
      "workspaceId": "workspace-a",
      "cwd": "/canonical/workspace-a",
      "sessions": [],
      "groups": {
        "groups": [],
        "colorOptions": ["red", "orange", "yellow", "green", "blue", "purple"]
      }
    },
    {
      "workspace": "/canonical/workspace-b",
      "error": {
        "code": "workspace_not_found",
        "message": "Workspace is not registered with this daemon.",
        "status": 404
      }
    }
  ]
}
```

结果保留选择顺序，原始选择器为 `workspace`。成功的条目包括规范的 `cwd`、`workspaceId` 和现有页面字段（`sessions`、可选的 `nextCursor`、`truncated` 和 `liveMergeFailed`）。每个返回的会话的 `workspaceCwd` 都会标准化为其封闭条目的规范 `cwd`。根据要求，`groups`包含完整的组目录；组身份的范围仅限于其封闭的工作空间。组读取保留现有的规范化以及针对格式错误或不可读的组织存储的警告和清空回退。失败的条目包含 `error: { code, message, status }` 和解析的身份（如果可用），但没有成功的空 `sessions` 列表。即使某些条目失败，有效批次也会返回 HTTP 200。格式错误的请求信封在存储读取之前返回 HTTP 400。

未知、内部和已删除的工作空间会产生 `404 workspace_not_found`；不可用或更改的运行时生成会产生 `503 workspace_runtime_unavailable`；不受信任的主节点生成 `403 untrusted_workspace`。不受信任的辅助节点保留现有的仅持久目录策略，而无需实时桥读取、修复写入或调试会话日志记录。抛出cursor且组失败保留其现有代码；其他抛出的读取失败变为`500 session_catalog_failed`。受信任的活动条目可以合并现有的活动桥状态；归档条目仅用于存储。

该守护进程每批最多同时运行四个工作区读取，将选择器限制为 4096 字符，将光标限制为 16384 字符，并将每个序列化的成功条目限制为 512 KiB。过大的条目返回 `413 catalog_response_too_large`；重试之前减少 `size` 或省略组。这将批量响应限制为大约 10 MiB 加上 envelope/error 开销。客户端断开连接会取消挂起的读取并阻止其他工作区读取开始。现有守护进程 JSON 正文限制和持久扫描限制也适用。目录请求使用读取速率限制层。

### `GET /workspaces/:workspace/sessions/live-state`

返回所选工作区运行时的仅内存实时会话快照以及内存中目录版本，以便客户端可以停止轮询 `GET /workspaces/:workspace/sessions` 处的持久目录以获取易失性状态，例如 `hasActivePrompt`、等待标志和 `clientCount`。飞行前 `workspace_session_live_state`；该标签独立于 `workspace_qualified_rest_core`，因此通告更广泛工作空间 REST 功能的旧守护进程不会实现此路由。选择器首先解析为精确的工作空间 ID，然后在规范化后解析为 URL- 编码的绝对 cwd，与其他多个会话路由相匹配。该路由仅对于主运行时和辅助运行时都是可信的：它永远不会回退到主运行时，并且它不使用授予不受信任的辅助有界目录读取的宽松持久目录策略。端点没有查询参数，也不执行会话存储、设置、外部命令或 ACP 往返，因此其成本与持久会话计数和 JSONL 大小无关；默认的实时会话上限使响应受到限制，并且在禁用上限的情况下，成本仅与实时会话的数量成正比。

回应：

```json
{
  "v": 1,
  "catalogVersion": {
    "generation": "7eca3164-bce1-4f50-94d8-c842c480f213",
    "revision": 17
  },
  "sessions": [
    {
      "sessionId": "session-123",
      "clientCount": 1,
      "hasActivePrompt": true,
      "activeWorkState": "active",
      "isWaitingForPermission": false,
      "isWaitingForUserQuestion": false,
      "updatedAt": "2026-08-18T08:12:30.123Z"
    }
  ]
}
```

`v` 是响应架构版本。每个成功的响应都包含 `Cache-Control: no-store`。 `sessions` 是当前位于所选运行时中的完整、未分页、无序的会话集；空的实时运行时返回 `200` 和 `sessions: []`。 `clientCount`、`hasActivePrompt`、`isWaitingForPermission` 和 `isWaitingForUserQuestion` 是必需的接线字段，缺少可选桥接值将投影到 `0` 或 `false`。 `activeWorkState` 是线路附加的，并且在较旧的守护进程中不存在：`active` 表示守护进程拥有未解决的工作或子进程发送了新的非空保留快照； `idle` 仅针对覆盖每个所需类别的新空快照发出； `unknown` 表示协商报告已过时或不完整； `unsupported` 表示孩子没有协商报告。它不会改变 `hasActivePrompt`：后台 shell、cron 轮转或挂起的终端通知是活动工作，而不会成为前台提示符。静态目录字段（例如显示名称、创建时间、组织和源元数据）被故意排除并保留为完整目录所拥有。不存在的活动状态行仅清除已知目录行的易失性字段；它永远不会删除持久化的目录行。

`updatedAt` 是可选的守护进程观察活动水印，当达到运行状态的提示已在当前桥中发布正式终端时出现。每个此类终端（成功、错误、取消和截止日期等）都会在终端事件发布之前写入一次，并且在每个实时会话中严格增加一次，即使两个终端在一个挂钟毫秒内着陆或挂钟向后移动；因此，时钟向前跳跃会一直持续到墙上时间赶上为止。它永远不会早于会话的 `createdAt`：创建时的第一个高级楼层，因此创建和第一个终端之间的挂钟回滚不能在已列出的 `createdAt` 后面键入一行。提示准入、队列等待、流式更新、仅队列取消、心跳和交互等待永远不会推进它。客户端使用它来刷新他们已经持有的目录行的新近度，而不是在完成轮次后重新加载完整目录。它不是持久性确认：记录器异步写入结果，因此该值仅证明守护进程观察到运行尝试稳定。它在桥生成中第一个运行的终端之前不存在（包括从磁盘恢复的会话），因此缺失不是支持探针，并且当守护程序重新启动或工作区运行时替换安装新桥时，它会消失。当一个会话同时存在实时摘要和持久摘要时，完整目录响应会报告较晚的有效时间戳，因此 `GET /session/:id/status`（直接返回桥摘要而不进行合并）可能会报告比列表响应更早的值。

`catalogVersion` 是守护程序观察到的目录更改的等号标记。 `generation` 是随每个桥接实例创建的随机 UUID，并在守护进程重新启动或工作区运行时替换时发生更改； `revision` 从零开始，并在一代内单调增加。唯一支持的操作是整个对的相等：相同的生成和修订意味着没有守护程序观察到的目录更改，任何差异都意味着重新加载完整目录。客户端不得执行修订算术或跨代比较修订，并且允许保守的额外增量。该版本涵盖了守护进程观察到的目录成员身份和静态元数据更改；普通的回合活动、提示生命周期、attach/detach和等待状态转换不会推进它，因为实时快照已经携带了相应的易失性字段。因此，在未更改的版本下更改的 `updatedAt` 是有效且符合预期的，并且它不会使守护程序的持久列表缓存无效。两个易失性覆盖值故意位于两个信号之外：转向错误状态 (`hasTurnError`/`turnError`) 和待处理交互 count/content (`pendingInteractionCount`/`pendingInteractions`) 既不推进版本也不出现在快照中，因此需要它们的客户端必须继续读取每个会话事件流或完整目录而不是依赖这条路线；当具体用户需要时，可以通过线附加方式添加任一字段。不会观察到由另一个守护进程、TUI 或外部进程直接写入的突变，因此一旦客户端停止定期全目录轮询，这些写入就没有有限的发现时间，并且只有在显式完全重新加载、另一个观察到的目录突变、重新连接或 daemon/runtime 替换之后才会出现。

客户端通过两次读取握手来协调目录包：读取实时状态 A，加载完整会话列表（当客户端使用 `session_organization` 时加上 `GET /workspaces/:workspace/session-groups`），然后读取实时状态 B。同等 A 和 B 版本接受该包；不同的版本将目录标记为陈旧，并最多合并一次尾随重新加载，而不是进入紧密的重试循环。每个接受的目录请求都必须在 A 之后启动 - 在 A 之前开始的请求或重复数据删除承诺无法满足协调。版本驱动的重新加载是每个工作区的单次飞行，并遵循非零的后台最小间隔，因此持续的目录改动无法驱动每个实时状态轮询的一次完整目录扫描；显式本地突变仍可能通过相同的单次操作请求立即刷新。

**错误：**

- `400` — 未知、格式错误、嵌套或未注册选择器的现有选择器验证或 `workspace_mismatch` 行为；该路由永远不会将未知选择器解析为主运行时。
- `403` — `untrusted_workspace` 适用于任何不受信任的运行时，包括不受信任的主运行时。
- `503` — `workspace_runtime_unavailable` 和 `Retry-After` 用于引导、转换、耗尽、阻止或删除运行时，或关闭中间请求的运行时生成。
- `500` — 意外的本地错误使用现有的网桥错误映射。

### `GET /workspace/:id/session-groups`

列出工作区的用户定义会话组。单数 GET 选择器接受任何注册的工作区 ID 或 URL- 编码的规范 cwd。复数 GET 别名也可用于不受信任的辅助节点，并且仅读取组织 sidecar。多个组突变保持信任门控，而单个组突变保留其仅主要的兼容性行为。飞行前 `caps.features.includes('session_organization')`。

回应：

```json
{
  "groups": [
    {
      "id": "018f...",
      "name": "Frontend",
      "color": "blue",
      "order": 0,
      "createdAt": "2026-07-04T12:00:00.000Z",
      "updatedAt": "2026-07-04T12:00:00.000Z"
    }
  ],
  "colorOptions": ["red", "orange", "yellow", "green", "blue", "purple"]
}
```

颜色只是协议标记；客户端本地化显示名称。不会创建默认的颜色命名组。

### `POST /workspace/:id/session-groups`

创建自定义会话组。严格的突变门。飞行前 `caps.features.includes('session_organization')`。

要求：

```json
{ "name": "Frontend", "color": "blue" }
```

`name` 已被修剪，必须是 1-64 字符，不能包含控制字符，并且通过不区分大小写的修剪比较在工作区中是唯一的。重复的名称返回 `409 { code: "group_name_conflict" }`。 `color` 必须是返回的 `colorOptions` 之一。

回应：

```json
{
  "group": {
    "id": "018f...",
    "name": "Frontend",
    "color": "blue",
    "order": 0,
    "createdAt": "...",
    "updatedAt": "..."
  }
}
```

### `PATCH /workspace/:id/session-groups/:groupId`

更新自定义会话组。严格的突变门。飞行前 `caps.features.includes('session_organization')`。正文字段是可选的：`{ "name"?: string, "color"?: string, "order"?: number }`。未知组 ID 返回 `404 { code: "group_not_found" }`； duplicate/invalid 名称和颜色使用与创建相同的错误。

### `DELETE /workspace/:id/session-groups/:groupId`

删除自定义会话组。严格的突变门。飞行前 `caps.features.includes('session_organization')`。引用该组的会话已清除为 `groupId: null`；保留固定状态。当组被删除时，响应为 `{ "deleted": true }`；当 id 不存在时，响应为 `{ "deleted": false }`。

### `POST /sessions/delete`

硬删除一个或多个持久会话 JSONL 文件。守护进程首先尽力关闭实时会话，然后删除活动或存档的 JSONL。如果同一 ID 的活动副本和存档副本都存在，则两者都会被删除。清理两侧工作树边车；文件历史记录、子代理记录和运行时 sidecar 均被有意保留。

要求：

```json
{ "sessionIds": ["<uuid>"] }
```

回应：

```json
{
  "removed": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

### `POST /sessions/archive`

存档一个或多个会话。存档是状态转换，而不是删除：JSONL 从 `chats/<id>.jsonl` 移动到 `chats/archive/<id>.jsonl`。文件历史记录、子代理记录和运行时 sidecar 保留在原处。如果会话处于活动状态，守护进程首先执行严格关闭，并要求 ACP agent 的关闭处理程序刷新聊天记录；如果关闭或刷新失败，则 JSONL 不会移动。飞行前 `caps.features.session_archive`。

要求：

```json
{ "sessionIds": ["<uuid>"], "resolveConflicts": true }
```

`sessionIds` 必须是最多具有 100 个 id 的非空字符串数组。重复项已折叠。

回应：

```json
{
  "archived": ["<uuid>"],
  "alreadyArchived": [],
  "resolvedConflicts": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

`resolveConflicts` 可选，默认为 `false`。默认情况下，具有相同 ID 的活动文件和存档文件会在 `errors` 中报告，并且不会移动、删除或覆盖两个副本。在对冲突进行分类之前，归档实时会话仍会执行上述严格关闭，以便关闭可以将排队记录刷新到活动记录中。使用 `resolveConflicts: true`，仅当两个副本都是所选工作区可能维护的常规转录本文件（包括拥有的空转录本或损坏的转录本）时，存档才会修复冲突。它保留存档副本，删除活动副本，并报告 `archived` 和 `resolvedConflicts` 中的 ID。该选项不会绕过所有权检查； `errors` 中报告了混合 local/foreign 或其他不明确的所有权，并且两个副本均未移动。 `errors` 条目具有 `{ "sessionId": "<uuid>", "error": "message" }`。

在守护进程获取写入器或维护租约以协调挂起的 sidecar 清理之后，在 `alreadyArchived` 中返回仅存档会话。如果另一个进程仍然持有写入器租约，则仅存档 ID 会在 `errors` 中报告，直到租约变得可用。

如果稍后的清理所有权检查失败，则不会回滚副本移动或冲突修复。在这种情况下，即使存档状态已更改，id 也可能仅出现在 `errors` 中，并且可能会从 `archived` 和 `resolvedConflicts` 中省略。在将错误视为活动副本或冲突仍然存在的证据之前，对同一工作区重试相同的生命周期请求。当服务可以验证存储的转录本身份并获取所需的编写器或维护租用时，即使对于会话列表忽略的空或损坏的转录本，重试也会报告权威的 `alreadyArchived` 状态，并恢复挂起的 sidecar 清理。存储身份无法验证的成绩单重试时继续报告`errors`，需要人工检查；无法获取所需租约的重试将保留在 `errors` 中，直到它变得可用。

生命周期冲突是批处理项结果：无工作区和工作区限定的路由返回 HTTP `200`，但冲突在 `errors` 中。这取代了早期的工作区限定 HTTP `409 session_conflict` 信封；调用该路由的客户端必须检查批量响应。内部运行时 REST 批处理保留安全冲突消息，同时继续编辑其他每会话失败详细信息。

### `POST /sessions/unarchive`

将存档会话恢复到活动目录。这不会自行恢复会话；它只会将 `chats/archive/<id>.jsonl` 移回 `chats/<id>.jsonl`。解档成功后，客户端可以调用`POST /session/:id/load`或`POST /session/:id/resume`。

要求：

```json
{ "sessionIds": ["<uuid>"], "resolveConflicts": true }
```

回应：

```json
{
  "unarchived": ["<uuid>"],
  "alreadyActive": [],
  "resolvedConflicts": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

`resolveConflicts` 可选，默认为 `false`。默认情况下，同时活动和存档的 JSONL 文件会在 `errors` 中产生冲突，并且两个副本都不会被移动、删除或覆盖；在守护进程获取写入器或维护租约以协调挂起的 sidecar 清理之后，在 `alreadyActive` 中返回仅活动会话。如果实时会话仍保留写入器租约，则在 `errors` 中报告仅活动 ID，直到该会话关闭。使用 `resolveConflicts: true`，仅当两个副本都是所选工作区可能维护的常规转录本文件（包括拥有的空转录本或损坏的转录本）时，取消存档才会修复冲突。它保留活动副本，删除存档副本，并在 `unarchived` 和 `resolvedConflicts` 中报告 ID。该选项不会绕过所有权检查； `errors` 中报告了混合 local/foreign 或其他不明确的所有权，并且两个副本均未移动。在开始批处理之前，对相同 ID 进行归档或取消归档会返回 `409 session_archiving`。

如果稍后的清理所有权检查失败，则不会回滚副本移动或冲突修复。在这种情况下，即使存档状态已更改，id 也可能仅出现在 `errors` 中，并且可能会从 `unarchived` 和 `resolvedConflicts` 中省略。在将错误视为存档副本或冲突仍然存在的证据之前，对同一工作区重试相同的生命周期请求。当服务可以验证存储的转录本身份并获取所需的编写器或维护租用时，即使对于会话列表忽略的空或损坏的转录本，重试也会报告权威的 `alreadyActive` 状态，并恢复挂起的 sidecar 清理。存储身份无法验证的成绩单重试时继续报告`errors`，需要人工检查；无法获取所需租约的重试（包括因为活动会话仍保留该租约）将保留在 `errors` 中，直到租约变得可用。

ACP-over-HTTP 通过供应商方法 `_qwen/sessions/archive` 和 `_qwen/sessions/unarchive` 使用相同的请求和响应主体。 REST 路由表将 `POST /sessions/archive` 和 `POST /sessions/unarchive` 映射到 ACP 传输的那些方法。

### 多工作空间实时会话路由

当公布 `multi_workspace_sessions` 时，实时会话操作会从 `sessionId` 中识别其工作区；客户端不会将工作区选择器添加到 URL。除了现有的所有者路由的生命周期操作之外，这还适用于 `PATCH /session/:id/metadata`、`POST /session/:id/recap`、`POST /session/:id/generate`、`POST /session/:id/btw`、`POST /session/:id/mid-turn-message`、`GET /session/:id/mid-turn-messages`、`DELETE /session/:id/mid-turn-messages/:messageId`、 `POST /session/:id/tasks/:taskId/cancel`、`POST /session/:id/goal/clear`、`POST /session/:id/continue`、`POST /session/:id/language`、`POST /session/:id/artifacts`、`DELETE /session/:id/artifacts/:artifactId`、`GET /session/:id/sources`、`POST /session/:id/sources`和`DELETE /session/:id/sources/:sourceId`。守护进程将每个请求路由到拥有实时会话的可信运行时。不受信任的非主要所有者返回 `403 untrusted_workspace`，缺失的实时所有者返回 `404 session_not_found`，不明确的所有者无法通过 `500 ambiguous_session_owner` 关闭。

此规则仅适用于实时会话，并且不会使每个无工作空间的会话路由具有多工作空间感知能力。持久化或归档操作使用其记录的工作区限定路径。 `POST /session/:id/branch`、`POST /session/:id/fork` 和 `POST /session/:id/cd` 有意保留为主要所有者，并为非主要所有者返回 `non_primary_session_route_not_supported`。

### 中途消息

`POST /session/:id/mid-turn-message`接受`{ "message": "...", "messageId": "<optional-message-id>" }`。成功的准入返回 `{ "accepted": true, "messageId": "<id>" }` 并将所有权转移给守护程序：当会话空闲时，消息将被排出到活动轮中或提升到正常提示 FIFO。使用`session_mid_turn_message_query`的客户端发送稳定的`messageId`；当它保持排队、挂起或在有界协调环中时，重复它是幂等的。拒绝守护程序验证的请求将返回 `{ "accepted": false }` 并且从不转移所有权：打开会话，没有提示承认其提示 FIFO，并且没有活动的目标转向报告 `{ "accepted": false, "reason": "session_idle" }`，因此客户端可以将消息作为普通提示重新提交，而不是显示失败。对于真正的新准入，在空闲和队列判决之前，引用会话不再保留的附件（或无效引用）的 `content` 块将被拒绝：使用 `{ "error", "code" }` 正文应答 `410 session_attachment_gone`（或 `400 invalid_session_attachment_reference`）请求，即使会话空闲，该正文也不携带 `reason`。两个较早的门抢占了它：一个重复的 `messageId`，其有效负载仍然与 `{ "accepted": true, "messageId" }` 幂等地结算，即使附件已被删除，并且正在关闭或授权关闭的会话会被无理的 `{ "accepted": false }` 拒绝。该判决仅描述了哪些内容可以耗尽中间轮消息，而不是会话可能保存的所有内容 - 会话快照仍然可以为其报告 `hasActivePrompt: true`，例如，当延迟恢复提示被暂停时。无理由的拒绝还有另一个原因 - 队列已满，会话正在关闭或授权关闭，排队的内联附件预算已用完，或者重复的 `messageId` 不再与守护程序持有的有效负载匹配。守护进程保留它已经承认的有效负载，而不是替换它，但这不是一个交付承诺：客户端删除的升级消息立即从挂起提示快照中消失 - 尚未启动的消息被丢弃在原处，已经运行的消息被隐藏，直到其中止的回合解决 - 因此删除响应是客户端观察到的唯一 `removed = true`。关闭会话永远不会提升仍在排队的内容，而在仍授权关闭时解决的轮次会将队列交给准入门，准入门会拒绝它，因此这些消息将被丢弃而不是提升并落在无环中。然后，孩子拒绝关闭，使会话保持活跃状态​​，剩余排队的内容仍然列出，并且没有安排任何内容来促进它，直到稍后的回合结束。这些消息仍然是守护进程的，因此默认恢复是等待：下一回合的解决会促进积压，不会丢失任何内容，也不会发送两次。无法等待的客户端（没有进一步的轮次到来并且用户正在观看不会耗尽的队列）可能会强制耗尽，唯一支持的方法是在再次发送之前释放每个有效负载。两条路径都从同一组开始：根据客户端自己在该窗口期间排队的记录减去其具有积极交付证据的记录来构建它，而不是根据列表的沉默来构建它。该查询不是守护进程所持有内容的清单——它省略了一个已经标记为已删除的提示，两个环都是有界的，并且在没有客户端 ID 的情况下发送的队列根本不会在此表面上进行跟踪——因此无处报告的 ID 可能在交付后已过期、未交付而被丢弃或从未列出。肯定的交付证据是 `mid_turn_message_injected` 回显、该 id 的 `pending_prompt_started` 或任一环中的成员资格。

中转POST也接受可选的`eventDetailMode`：`full`（默认）或
`summary`；无效值返回 `400 invalid_event_detail_mode`。守护进程
保留该消息模式，并在未删除的消息变成
新提示时使用它。排入现有回合不会更改该回合的模式。
在排队或提升并待处理时，在同一`messageId`下重试必须
匹配文本、媒体和有效模式（省略等于完整）。 Web Shell 发送其
当前提供程序模式。 REST 路由仍然拒绝带有 
`reason: "session_idle"` 的新空闲接纳；然后呼叫者提交一个普通的提示。

为了强制耗尽，`DELETE /session/:id/mid-turn-messages/:messageId` 该集合中查询仍报告为已排队的每个 id，然后才在新的 `messageId` 下再次发布文本，因为重复的 id 会幂等地被确认并且不会重新装备任何内容。该顺序很重要，并且一次适用于整个集合，而不是一次一个 id：守护进程拥有的仍在排队的有效负载由下一个结算提升，因此重新发布其有效负载从未释放的 id 会将该消息传递两次 - 一次来自该提升，一次来自重新发送。在未公布 `session_mid_turn_message_mutation` 的情况下，无法释放有效负载，因此等待是唯一安全的选择。没有 DELETE 裁决可以自行确定交付。 `{ "removed": false }` 覆盖了一个已经注入或完成的 ID — 已交付，因此不得重新发布 — 并且，根据下面的 DELETE 路由，未找到 ID，对于一个查询刚刚列为已排队的查询来说，这意味着它在两个调用之间离开了队列，并且可能已被上面的密切授权路径丢弃而未交付；根据客户自己的证据来决定该案件。 `{ "removed": true }` 释放有效负载，但对于在该窗口中升级的 ID，它涵盖了在发送之前拼接的 FIFO 条目（可以安全地重新发布）和已开始的升级提示的中止（重新发布然后第二次发送）。即使列表不再将其报告为已排队，升级的 ID 也可以通过相同的路径保持可删除状态，直到其解决为止。重新发布本身就是中途入场，可以被拒绝`{ "accepted": false, "reason": "session_idle" }`；这不是失败，而是上面描述的信号表明会话现在采用普通提示，并且通过普通路由提交会启动一个轮次，其结算会促进仍在排队的任何内容 - 这与必须首先释放集合中的每个 id 的原因相同。

因此，丢失的 `reason` 并不是会话繁忙的证据。 `reason` 之前的守护进程在每种情况下都会忽略它，因此客户端将自己的空闲检测与它一起保留。连接到旧守护程序的新客户端会检测到缺失的功能并保留其旧的本地回退。

`GET /session/:id/mid-turn-messages` 返回会话范围的守护程序拥有的队列以及有界的 `settledMessageIds` 和 `promotedMessageIds` 环。已确定的 ID 被注入或显式删除；升级的 ids 输入正常提示符 FIFO。任一环中的 ID 都不得重新发送。

当排队的消息被排入活动回合时，守护进程会发布 `mid_turn_message_injected`，其中携带对齐的 `messages` 和 `messageIds` 数组（以及运行回合的 `promptId`（如果已知））。它是一个瞬态重复数据删除信号，而不是一个转录项：客户端解决在这些消息 ID 下注册的完成回调，并删除它们的任何本地待处理行。较旧的守护进程还在有效负载中携带 `originatorClientId`。通过上面的查询，可以从稳定的环中恢复丢失的回声。

当通告`session_mid_turn_message_mutation`时，附加的会话客户端可以调用`DELETE /session/:id/mid-turn-messages/:messageId`。它从中途队列或其提升的挂起提示状态中删除消息；删除已在运行的升级消息会中止，这与普通的挂起提示删除相匹配。守护进程拥有的队列添加和删除会发布现有的 `pending_prompt_added` 和 `pending_prompt_completed` 会话事件，以便附加的客户端刷新两个权威队列快照。 `{ "removed": false }` 表示消息已注入、已完成或未找到。

### `GET /session/:id/pending-prompts`

返回当前正在运行的提示符以及在实时
会话的FIFO中等待的提示符。该请求可能包含`X-Qwen-Client-Id`；当存在时，它必须
识别连接的客户端。

```json
{
  "pendingPrompts": [
    {
      "promptId": "<prompt-id>",
      "text": "Explain the failure",
      "queuedAt": 1700000000123,
      "state": "running",
      "originatorClientId": "<client-id>"
    }
  ]
}
```

`state` 是 `running` 对于正在发送的提示和 `queued` 等待
提示。 `content` 当提示包含结构化内容时出现，例如
图像。这是实时会话所有者路线： `404` 意味着没有活着的主人并且
`503` 表示所有者暂时无法联系。不受信任的非主要
业主退货 `403 untrusted_workspace`，并且一个 id 居住在多个地方
工作区返回 `500 ambiguous_session_owner`。没有专门的
能力标签；旧守护进程返回 `404`。的 TypeScript SDK 方法是
`getPendingPrompts()`.

### `POST /session/:id/prompt`

`eventDetailMode` 接受 `full`（默认）或 `summary`；无效值返回
`400 invalid_event_detail_mode`。摘要在
环保留和SSE交付之前过滤嵌套子代理详细信息，并删除Agent提示、嵌入式工具和
进行中的令牌计数器，同时保留根内容和主模型使用情况。
已解决的Agent结果保留`tokenCount` 和 `executionSummary` 代币总数 
（包括 failure/cancellation）用于回合指标；嵌套使用框架保持
过滤。相同的规则适用于摘要 load/transcript 响应。该模式是
在准入时捕获，仅在调度时应用，并在结算时重置为完全。
后期子代理事件在发布时使用该模式：空闲时完全，或在运行后续提示时使用活动
提示的模式。它们不继承最初启动子代理的提示的模式
。
它适用于会话的所有订阅者；过滤后的详细信息以后无法从环中
检索。守护进程的 `/acp` `session/prompt` 接受具有相同作用域的相同 
 可选顶级扩展（无效值为 JSON-RPC
`-32602`）；它不是标准的 ACP 字段，不会转发给子项。

将提示转发至 agent。每个会话多提示呼叫者 FIFO- 排队（ACP 保证每个会话有一个活动提示）。

要求：

```json
{
  "prompt": [{ "type": "text", "text": "What does src/main.ts do?" }],
  "delivery": {
    "kind": "channel",
    "target": {
      "channelName": "dingtalk",
      "type": "user",
      "id": "platform-user-id"
    }
  }
}
```

`delivery` 是可选的并且需要 `channel_delivery` 能力。的
守护进程仍然返回 `202 {promptId,lastEventId}` 当提示被承认时。
成功后 `end_turn`，会话将可见的最终文本提交给
确切的工作区已经运行的 Channel Worker。有效负载只是
最后一个免工具辅助响应块；工具调用前导码、工具间
旁白、被取代的重试和早期的自动延续块是
排除。空的或仅有空格的词尾仍然会产生相关的
`channel_delivery_result` 与 `status: "skipped"` 授权后是
消耗了，但它不联系工人。投递成功或失败到达
后来通过相同的可重播事件并且永远不会改变 `turn_complete` 进入
`turn_error`。取消， Agent 失败和令牌限制终止不会
发送或发布交付结果。

验证：`prompt` 必须是非空对象数组。其他故障在到达桥之前返回`400`。

回应：

```json
{ "promptId": "session-id########1", "lastEventId": 42 }
```

`202` 响应确认承认，而不是 Agent 完成。观察 `lastEventId` 之后的 
 会话 SSE 流，并通过 `promptId` 关联 `turn_complete` 或 
`turn_error`。

`turn_complete.data.stopReason` 携带 ACP `StopReason` 返回的 agent
 — `end_turn`、`max_tokens`、`max_turn_requests`、`refusal`或
`cancelled`。守护进程还可以发出 `cancelled` 来表示在没有 
 agent 运行它的情况下中止的提示，包括排队提示删除、调用者断开连接或 
drain/teardown；该值并不能证明 agent 运行了提示。 **将
字段视为开放字符串**：在线上键入`string`，ACP集可以
增长，并且彻底打开它的客户端将中断接下来
添加。

两个守护进程端结果 ** 未 ** 到达此字段。守护进程内失败的回合
——截止日期到期、拆卸刷新、子崩溃——被发布为
作为`turn_error`事件，而不是作为`turn_complete`stopReason。其`data`
始终携带`message`； `code`仅当守护进程分类为
故障时才存在（截止日期到期→`prompt_deadline_exceeded`，拆卸刷新→
`channel_closed`、`session_closed`、`session_killed`或`daemon_shutdown`）， 
提示帧被拒绝，因为ACP孩子在请求中死亡，携带
既不是`code`也不是`errorKind`。将两者视为可选并在 `message` 上分支。

重启后从持久历史记录中恢复的回合不会重新发布
根本没有溪流；它表面于 `promptTerminals[]` 在
`POST /session/:id/load` 响应体——如
`{ terminal: "completed", stopReason: "reconstructed_from_transcript" }` 当
持续的尾部表明转弯已完成，或者
`{ terminal: "interrupted", code: "daemon_lost" }` 与 **不** `stopReason` 当
恶魔在回合中死亡。匹配条目 `promptId` 并分支
`terminal`; `promptTerminals` 当
ledger 没有该会话的证据。

如果 HTTP 客户端在提示中断开连接，则守护程序会向 agent 发送 ACP `cancel` 通知，从而通过 `stopReason: "cancelled"` 结束提示。

当通告`prompt_absolute_deadline`时，`deadlineMs`可能会缩短
配置的服务器期限。到期会发出与 
`code: "prompt_deadline_exceeded"` 相关的 `turn_error`。截止日期释放调用者而不
杀死agent；如果 agent 稍后稳定，则 
`promptId` 的回合状态轮询将返回已确定的成绩单结果，而不是截止日期错误。

### `POST /session/:id/cancel`

取消会话中当前活动的 **** 提示。 ACP-这边这是一个通知，而不是一个请求——agent通过用`cancelled`解析活动的`prompt()`来确认。

```bash
curl -X POST http://127.0.0.1:4170/session/$SID/cancel
# → 204 No Content
```

> **多提示合约：**取消仅影响活动提示。同一客户端先前发布且仍在活动提示后面排队的任何提示都将继续执行。多提示排队是守护进程引入的行为（不在 ACP 规范中）；排队提示的合同是“除非您取消每个提示，或者通过通道退出终止会话，否则它们将继续运行”。

如果在多客户端部署中出现意外的排队提示，请首先确认
呼叫者是否共享默认的`sessionScope: "single"`会话。对于 
 独立的每线程对话，请使用 
`sessionScope: "thread"` 创建会话，以便提示仅在该线程内进行序列化。

### `DELETE /session/:id`

明确关闭实时会话。即使连接了其他客户端，也会强制关闭 — 取消任何活动提示，将挂起的权限解析为已取消，发布 `session_closed` 事件，关闭 EventBus，并从守护进程映射中删除会话。磁盘上的持久会话已NOT删除——它们可以通过`POST /session/:id/load`重新加载。飞行前 `caps.features.session_close`。

```bash
curl -X DELETE http://127.0.0.1:4170/session/$SID
# → 204 No Content
```

幂等：对于未知会话返回 `404`。误差包络线采用`code: "session_not_found"`；并发关闭可能会返回 `code: "session_closing"`，客户端可能会将其视为该路由的相同成功最终状态。

> **`session_closed` 事件。** SSE 订阅者在流结束之前收到带有 `{ sessionId, reason: 'client_close', closedBy?: '<clientId>' }` 的终端 `session_closed` 事件。 SDK 减速器对此的处理与 `session_died` 相同（设置 `alive: false`，清除 `pendingPermissions`）。

### `PATCH /session/:id/metadata`

更新可变会话元数据。飞行前 `caps.features.session_metadata`.
分组和固定不是此航线的一部分；使用`session_organization`下的
`PATCH /session/:id/organization`。

要求：

```json
{
  "displayName": "My Investigation Session",
  "pr": {
    "number": 123,
    "url": "https://github.com/QwenLM/qwen-code/pull/123",
    "state": "open"
  }
}
```

|领域|必填|注释|
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `displayName` |不|字符串。长于 256 UTF-16 代码单元的值将被截断，并且剪切不支持代理项对，因此以非 BMP 字符结尾的名称可能会丢失单独的代理项一半。空值或仅包含空格的值将被拒绝并显示为 `400 invalid_metadata`；省略该字段以使名称保持不变。|
| `pr`          |不|绑定一个拉取请求。需要正整数 `number`、最多 2,048 个字符（不含控制字符）的 HTTP(S) `url`，以及可选的 `state`：`open`、`merged`，或`closed`。|

回应：

```json
{
  "sessionId": "<uuid>",
  "displayName": "My Investigation Session",
  "prs": [
    {
      "number": 123,
      "url": "https://github.com/QwenLM/qwen-code/pull/123",
      "state": "open"
    }
  ]
}
```

`prs` 是有效的有界绑定历史记录，可能包括刷新的问题 
 链接。在会话的 SSE 流 
 上发布 `session_metadata_updated` 事件，仅携带已更改的字段组：重命名会发出 `displayName`（设置名称时加上 
`titleSource`）并让 `prs` 不存在，而PR 绑定 
change 发出 `prs` 并在设置时回显当前的 `displayName`。将事件中缺少的 
 字段视为未更改、未清除，并在需要完整元数据时重新读取 `200` 正文 
 或会话列表。

### `PATCH /session/:id/organization` 和 `PATCH /workspaces/:workspace/session/:id/organization`

通过现有的变异门更新本地会话组织状态。飞行前 `caps.features.includes('session_organization')`；复数路线还需要`workspace_qualified_rest_core`。在复数路由上，`:workspace` 首先解析为精确注册的工作区 ID，然后解析为 URL- 编码的规范绝对 cwd。所选运行时必须可信。会话存在和非空 `groupId` 验证的范围仅限于该运行时的活动持久、存档持久和实时会话状态和组存储，不会回退到主工作区或其他工作区。旧路由仍然仅限于主工作区。

要求：

```json
{ "isPinned": true, "groupId": "018f..." }
```

|领域|必填|注释|
| ---------- | -------- | ---------------------------------------------------------------------------------------------------- |
| `isPinned` |不|布尔值。如果尚未固定，则 `true` 设置 `pinnedAt`； `false` 清除`pinnedAt`。|
| `groupId`  |不|自定义组 ID 或 `null` 表示未分组。未知组 ID 返回 `404 { code: "group_not_found" }`。|
| `color`    |不|支持的会话颜色标记，或 `null` 来清除会话颜色。|

回应：

```json
{
  "sessionId": "<uuid>",
  "groupId": "018f...",
  "color": "blue",
  "isPinned": true,
  "pinnedAt": "2026-07-04T12:00:00.000Z",
  "updatedAt": "2026-07-04T12:00:00.000Z"
}
```

该状态存储在守护进程运行时存储目录下的项目级会话组织 sidecar 中。它不是成绩单内容，不更新成绩单 `mtime`，不与成绩单一起导出，并在 archive/unarchive. 中保留

### `POST /session/:id/heartbeat`

修改守护进程在本次会话中最后一次看到的记录。长寿命适配器 (TUI/IDE/web) 会定期对其进行 ping 操作，以便未来的撤销策略 (Wave 5 PR 24) 可以区分死客户端和安静客户端。

标题：

|标头|必填|注释|
| ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `X-Qwen-Client-Id` |不|回显来自 `POST /session` 的守护进程颁发的 id。已识别的客户还会增加其每个客户的时间戳；匿名心跳只会影响每个会话的水印。必须满足与其他地方相同的 `[A-Za-z0-9._:-]{1,128}` 形状。|

请求正文为空（`{}` 很好 - 今天没有读取任何字段）。

回应：

```json
{
  "sessionId": "<sid>",
  "clientId": "<cid>",
  "lastSeenAt": 1700000000123
}
```

仅当提供可信的 `X-Qwen-Client-Id` 时，才会回显 `clientId`。 `lastSeenAt` 是桥存储的守护进程端 `Date.now()` 纪元 (ms)。

错误：

- `400` - `{ code: 'invalid_client_id' }` 当标头格式错误（标头形状规则）或当它携带未为此会话注册的 `clientId` 时（桥在碰撞任何时间戳之前抛出 `InvalidClientIdError`）。
- `404` — 未知会话。

能力门控：飞行前 `caps.features.client_heartbeat`。较旧的守护进程为此路径返回 `404`。

### `POST /session/:id/model`

在会话当前绑定的模型服务 ** 内切换活动模型 **。通过每会话模型更改队列进行序列化。

（对于切换 _ 服务 _ 本身 - 阿里巴巴 ModelStudio 与 OpenRouter 等 - 在 `POST /session` 上传递 `modelServiceId` 进行新会话。阶段 1 没有实时服务切换路由。）

要求：

```json
{ "modelId": "qwen-staging" }
```

响应：ACP agent 的模型切换结果，逐字转发 — 
 守护进程不会重塑它，因此顶层不包含 `modelId`。从`_meta.qwenModelSwitch`读取
开关详细信息。

成功后，将 `model_switched` 发布到 SSE 流。失败时，发布 `model_switch_failed`（以便被动订阅者看到失败，而不仅仅是调用者）。与 agent 通道出口竞争，因此楔入的子级无法阻止 HTTP 处理程序。成功的切换还会尽力将会话模型记录在会话JSONL中；写入记录后，守护程序 load/resume 会尝试在身份验证之前恢复此会话的模型。如果记录的模型无法再应用（模型已删除，凭据不可用），则恢复会使用相同 ID 的注册表路由（如果存在）（对于可以是与记录的绑定不同的端点的运行时快照记录），并且仅当没有路由解析时才继续使用 `settings.model.name` 默认值。 `settings.model.name` 仍更新为 ** 新 ** 会话的默认值。

### `POST /session/:id/recap`

能力标签：`session_recap`。桥 → ACP extMethod `qwen/control/session/recap`。

生成一句话“我在哪里停止了”会话摘要。包装核心的 `generateSessionRecap` (`packages/core/src/services/sessionRecap.ts`)，它在禁用工具 `maxOutputTokens: 300` 和严格的 `<recap>...</recap>` 输出格式的情况下对快速模型运行侧面查询。侧面查询读取会话现有的 GeminiClient 聊天历史记录，并添加 ** 而不是 **。

请求正文被忽略（发送 `{}` 或空）。非严格变异门——姿势镜像`/session/:id/prompt`（调用花费代币但不改变状态）。未发布 SSE 事件。

响应（200）：

```json
{
  "sessionId": "sess:42",
  "recap": "Debugging the auth retry race. Next: add deterministic timing to the integration test."
}
```

在以下情况下，`recap` 为 `null`（正常的 200，不是错误）：

- 该会话的对话轮数还不到两轮，
- 侧面查询返回没有可提取的 `<recap>...</recap>` 有效负载，
- 或发生任何底层模型错误（核心助手尽力而为，从不抛出异常）。

错误：

- `400 {code: 'invalid_client_id'}` — 格式错误的 `X-Qwen-Client-Id` 标头。
- `404` — 会话未知。

取消：**v1**中无。该路由不会侦听 HTTP 客户端断开连接，也不会将 `AbortSignal` 插入桥中，并且 ACP 子级将运行侧查询直至完成，无论调用者是否已断开连接。唯一的上限是桥的 60 秒逆止器超时 (`SESSION_RECAP_TIMEOUT_MS`) 和针对 ACP 通道死亡的传输关闭竞赛。这是可以接受的，因为回顾很短（单次尝试，`maxOutputTokens: 300`，~1– 5 秒典型）；如果带宽成本证明合理，基于请求 ID 的取消扩展方法可以在未来版本中实现完全的端到端取消。

### `POST /session/:id/generate`

能力标签：`session_generation`。

从调用者提供的提示运行请求范围的文本生成。该请求
不会读取或改变对话历史记录，并且不公开任何工具。它更喜欢
配置的快速模型，如果快速
模型丢失或无法解析，则回退到会话的主模型。端点与任务无关；
翻译只是一种可能的调用者定义的提示。

要求：

```json
{ "prompt": "Translate into Chinese: Hello" }
```

响应是 `text/event-stream`。服务器立即写入初始 SSE 注释 
，然后是 `started`、可选的 `thinking` 进度事件、零个 
 或多个 `delta` 事件以及 `done`。 `thinking`事件不携带推理
内容。流式传输开始后模型失败会产生 `error` 事件；它
不会用其他模型重试。提示仅限于 UTF-8 文本的 32 KiB。
断开 HTTP 客户端的连接将取消生成请求。

### 变异：批准、工具、技能、init、MCP重启

该守护进程公开了五个突变控制路由，允许远程客户端更改运行时状态，而无需触及守护进程主机的 CLI。批准模式控制保留其非严格兼容性门。工具切换、技能切换、工作区初始化和 MCP 重新启动使用严格的突变门：可信环回主、承载身份验证和配对本地控制请求通过。到达没有可信环回权限的网关的无令牌主请求会收到 `401 {code: 'token_required'}`；丢失或无效的配置凭据以及未配对的本地控制凭据会提前通过普通 `401 Unauthorized` 被拒绝。所有五个：

- 接受并标记 `X-Qwen-Client-Id` 标头（PR 7 审核链）。当标头携带可信 ID 时，守护进程会在相应的 SSE 事件上发出 `originatorClientId`，以便跨客户端 UI 可以抑制其自身突变的回声。
- 在公开可供性之前预检每个标签的功能。缺少路由的守护进程返回 `404`。对于技能设置路由，标签缺失可能意味着相同的路径服务于已停用的目录验证合约：单目标路由可以返回 HTTP `404 skill_not_found` 或 `409 skill_not_toggleable`，而批处理路由返回 HTTP 200，并在 `errors[]` 中出现目录衍生的故障。不要使用路由探测作为版本检查。

工具切换、技能切换、init 和 MCP 重新启动路由会发出 ** 工作空间范围的 ** 事件：每个活动会话 SSE 总线都会接收该事件，无论触发突变时连接的是哪个会话。 `approval-mode` 发出 ** 会话范围的 ** 事件，因为更改是一个会话的 `Config` 的本地更改。

#### `POST /session/:id/approval-mode`

能力标签：`session_approval_mode_control`。桥 → ACP extMethod `qwen/control/session/approval_mode`。

更改直播会话的审批模式。新模式会立即进入 ACP 孩子的每个会话 `Config`，并在聊天记录可用时从该会话的记录中恢复。计划选择的执行模式将随之恢复。工作区设置默认写入 NOT — 传递 `persist: true` 也可将 `tools.approvalMode` 写入工作区设置。如果录制被禁用或降级，该模式将保持仅限实时会话。

要求：

```json
{ "mode": "auto-edit", "persist": false }
```

`mode` 必须是 `'plan' | 'default' | 'auto-edit' | 'auto' | 'yolo'` 之一（核心的 `ApprovalMode` 枚举的镜像；SDK 导出 `DAEMON_APPROVAL_MODES` 以进行运行时验证）。 `persist` 默认为 `false`。

响应（200）：

```json
{
  "sessionId": "sess:42",
  "mode": "auto-edit",
  "previous": "default",
  "persisted": false
}
```

错误：

- `400 {code: 'invalid_approval_mode', allowed: [...]}` — 未知模式文字。
- `400 {code: 'invalid_persist_flag'}` — `persist` 是非布尔值。
- `403 {code: 'trust_gate', errorKind: 'auth_env_error'}` — 请求的模式需要受信任的文件夹（不受信任的工作区中的特权模式被核心的 `Config.setApprovalMode` 拒绝）。
- `404` — 会话未知。

SSE 事件（会话范围）：`approval_mode_changed` 和 `{sessionId, previous, next, persisted, originatorClientId?}`。

#### `POST /workspace/tools/:name/enable`

能力标签：`workspace_tool_toggle`。纯文件 IO — 无 ACP 往返。

在工作区的 `tools.disabled` 设置列表中切换工具名称。那里列出的工具**根本没有注册**（与`permissions.deny`不同，它使工具保持注册状态并拒绝调用）。内置工具和 MCP- 发现工具都流经 `ToolRegistry.registerTool`，后者查阅禁用集。

> ⚠️ **名称必须与注册表的公开标识符完全匹配。**不会发生别名解析 - 路由将路径参数中的任何字符串存储到`tools.disabled`中，下一个ACP子级在注册时与`tool.name`进行比较。内置程序使用其规范注册表名称（snake_case 动词形式）：`run_shell_command`、`read_file`、`write_file`、`list_directory`、`glob`、`grep_search`、 `web_fetch` 等 — NOT CLI 表面的显示标签（`Shell`、`Read`、`Write`）。 MCP-发现的工具使用合格的`mcp__<server>__<name>`形式（这也是`tool_toggled`事件广播的形式以及`GET /workspace/mcp`列出的内容）。禁用 `Bash` 将 NOT 阻止 `run_shell_command` 在下一个会话中注册。

实时 ACP 子项保留已注册的工具 - 切换对 ** 下一个 ** ACP 子项生成生效。与 `POST /workspace/mcp/:server/restart`（对于 MCP-源工具）或新会话创建结合使用，使更改在当前守护进程中生效。

接受未知的工具名称：预禁用尚未安装的 MCP 工具是一个合法的用例。

要求：

```json
{ "enabled": false }
```

响应（200）：

```json
{ "toolName": "run_shell_command", "enabled": false }
```

错误：

- `400 {code: 'invalid_tool_name'}` — 空路径参数，或路径参数超出 256 字符上限。
- `400 {code: 'invalid_enabled_flag'}` — `enabled` 缺失或非布尔值。

SSE 事件（工作区范围）：`tool_toggled` 和 `{toolName, enabled, originatorClientId?}`。

#### 分割技能配置和运行时路线

能力标签：`workspace_skills_config_runtime`。

`GET /workspace/config/skills` 读取守护程序本地全局配置所有者。 `GET /workspaces/:workspace/config/skills` 读取准确的注册工作区配置，不需要实时或可信的运行时。两者都返回正常的技能状态形状，而不需要 `runtimeEpoch`：

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "initialized": true,
  "skills": []
}
```

`GET /workspace/runtime/skills` 和 `GET /workspaces/:workspace/runtime/skills` 读取选定的可信运行时而不启动它。实时目录包括生成它的运行时生成：

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "initialized": true,
  "runtimeEpoch": 4,
  "skills": []
}
```

全局安装和删除使用 `POST /workspace/config/skills/install` 和 `DELETE /workspace/config/skills/:name?scope=global`。工作区安装、删除和启用使用 `POST /workspaces/:workspace/config/skills/install`, `DELETE /workspaces/:workspace/config/skills/:name?scope=workspace`, 和 `POST /workspaces/:workspace/config/skills/:name/enable`。在安排运行时协调之前，配置会写入提交持久状态。他们的 `activation` 是 `deferred` 当当前运行时无法更新时 `reconciling` 当更新已排队时；无操作切换保留外观的激活，而不是声明发生了运行时刷新。

当无法枚举守护程序本地技能清单时，配置删除将返回 `503 skills_config_unavailable`，而不是明确的未找到响应。

奇异突变所有者拒绝使用 `400 workspace_scope_requires_qualified_workspace` 的工作空间范围；合格的突变拒绝全局范围`400 global_scope_requires_singular_owner`。合格的写入需要可信的工作空间。对于需要运行时协调的合格配置写入，传统注入桥会返回 `501 workspace_runtime_not_supported`。未知、已删除、过渡和耗尽工作空间遵循标准合格工作空间错误，并且永远不会回退到主工作空间。

#### `POST /workspace/skills/:name/enable`

能力标签：`workspace_skill_settings_toggle`。工作区限定形式为 `POST /workspaces/:workspace/skills/:name/enable`。

更新名称的工作区技能设置，而无需查阅加载的技能目录。修剪后的请求名称将传递到持久性并在响应中返回。启用任何名称记录工作区 `skills.enabled` 选择加入，甚至在安装之前；禁用会删除该选择并添加工作区 `skills.disabled` 条目。不再加载的现有技能条目将被保留，目标的 duplicate/case-variant 条目将被折叠。从更高范围继承的硬 `skills.disabled` 条目对于有效可用性仍然具有权威性，但不会阻止工作区范围记录或删除其自己的声明。工作区声明否则参与通常的 `skills.disabled > skills.enabled > skills.defaultDisabled` 解析，并且可以覆盖更高范围的 `skills.defaultDisabled` 或 `skills.enabled` 条目。

这与 ACP `qwen/skills/setEnabled` 托管技能操作和 `disable-model-invocation` frontmatter 字段不同。对于活动的分机父级，有效的技能可用性遵循 `skills.disabled` > `skills.enabled` > `skills.defaultDisabled` > 工作区内部覆盖 > 清单 `skillStates` > 已启用。硬禁用和默认禁用都会从 slash-command/model 可用性中删除该技能，并拒绝以后的技能执行。 `disable-model-invocation: true` 保持直接用户调用可用，并且仅隐藏模型调用的技能。

要求：

```json
{ "enabled": false }
```

响应（200）：

```json
{
  "skillName": "review",
  "enabled": false,
  "changed": true,
  "activation": "applied",
  "sessionsRefreshed": 2,
  "sessionsFailed": 0
}
```

`activation` 反映子项活跃度和任何独立于 `changed` 所需的刷新。当 ACP 子项处于活动状态并且任何所需的刷新成功时，它是 `applied`；当活动检查时没有子项处于活动状态或更改的请求在所需刷新期间丢失其 child/session 时，它是 `deferred`；当至少一项其他所需刷新失败时，它是 `partial`。因此，无操作可以是 `applied` 或 `deferred` 和 `changed: false`；当 `changed` 为 true 并且激活为 `deferred` 时，在子进程启动时使用持久声明。繁忙的会话包含在所需的刷新中。该守护进程重新加载 ACP 子级和每个活动会话的工作区设置，通知 SkillManager 使用者，并推送 `available_commands_update`。已经发送到模型的请求不会被重写；后续验证、命令快照和模型上下文都使用新状态。如果持久性失败，则不会发出任何刷新或事件。如果会话刷新失败，则保留已提交的设置。当子项返回每个会话结果时，会话计数是准确的。如果刷新控制本身在返回这些结果之前失败，则 `sessionsFailed: 1` 是一个保守的下限，指示刷新请求失败。

错误：

- `400 {code: 'invalid_skill_name'}` — 空路径参数，或超过 256 个字符。
- `400 {code: 'invalid_enabled_flag'}` — `enabled` 缺失或非布尔值。
- `403 {code: 'untrusted_workspace'}` — 所选工作区不受信任。

对于每个更改的键（`skills.disabled` and/or `skills.enabled`），突变重用工作区范围的 `settings_changed` 事件；它不会添加新的事件类型。每个事件都包含相同的 `mutation` 对象：`{ id, kind: 'skill_toggle', skills: [{ name, enabled }], activation, sessionsRefreshed, sessionsFailed }`。 `id` 关联一个切换请求生成的每个设置事件。 `skills` 列出了工作区设置声明实际更改的请求名称和请求启用值；更高范围的设置可以保持有效可用性不变。工作区技能状态单元格包括可选的 `disabledReason: 'hard' | 'default' | 'inactive_extension'` 和 `lockedScope: 'system' | 'user' | 'systemDefaults'` 字段。

#### `POST /workspace/skills/enable`

能力标签：`workspace_skill_settings_batch_toggle`。工作区限定形式为 `POST /workspaces/:workspace/skills/enable`。

在一次请求中更新最多 100 个姓名的工作区技能设置；上限在重复数据删除之前对原始 `skillNames` 条目进行计数。名称会进行不区分大小写的修剪和重复数据删除，同时保留首次看到的顺序和大小写。该守护进程不会查阅加载的技能目录。它将所有生成的声明更改应用到最多一个锁定设置写入中，并且当发生任何更改时，刷新活动会话一次。启用始终记录显式工作区 `skills.enabled` 选择加入，包括尚未安装的名称，因此它可以覆盖扩展的内部禁用。重复相同的声明仍然是无效的。意外的持久性或运行时生成失败会导致整个请求失败。

要求：

```json
{
  "skillNames": ["review", "deploy", "missing"],
  "enabled": false
}
```

响应（200）：

```json
{
  "enabled": false,
  "activation": "applied",
  "sessionsRefreshed": 2,
  "sessionsFailed": 0,
  "results": [
    {
      "skillName": "review",
      "enabled": false,
      "changed": true
    },
    {
      "skillName": "deploy",
      "enabled": false,
      "changed": true
    },
    {
      "skillName": "missing",
      "enabled": false,
      "changed": true
    }
  ],
  "errors": []
}
```

格式错误的请求返回 HTTP 400 和 `invalid_skill_names`、`invalid_skill_name` 或 `invalid_enabled_flag`。身份验证、工作区信任、客户端身份、意外的持久性失败和运行时生成失败使整个请求无法通过标准路由门。 `errors` 保留在响应中以实现线路兼容性，并且对于结构有效的名称为空。批处理级别 `activation`、`sessionsRefreshed` 和 `sessionsFailed` 描述了子项活动性以及所有更改结果共享的单个实时会话刷新。没有更改目标的批次仍然可以在子项存在时回答 `applied`，或者在不存在子项时回答 `deferred`，与单技能无操作响应相匹配，因此从每个结果的 `changed` 标志中得出实际更改的内容。当至少一个目标发生变化时，守护进程会发出与单技能路由相同的 `settings_changed` 突变元数据；该请求中的每个 `skills.disabled` / `skills.enabled` 事件共享一个 `mutation.id`。

#### `POST /workspace/init`

能力标签：`workspace_init`。纯文件 IO — 无 ACP 往返，** 无 LLM 调用 **。

在守护程序的主工作空间根目录中搭建一个空的 `QWEN.md`（或工作空间 `context.fileName` 设置覆盖）。仅限机械 — 对于 AI- 驱动的内容填充，请跟进 `POST /session/:id/prompt`。

当目标文件存在非空白内容时，默认拒绝覆盖。仅空白文件被视为不存在（与本地 `/init` 斜杠命令匹配）。

要求：

```json
{ "force": false }
```

响应（200）：

```json
{ "path": "/work/bound/QWEN.md", "action": "created" }
```

`action` 是 `'created'` 对于新鲜创作， `'noop'` 当现有的纯空白文件保持不变（不执行写入）时，以及 `'overwrote'` 当 `force: true` 替换非空内容。的 `workspace_initialized` SSE 事件反映了响应动作——观察者可以过滤 `action !== 'noop'` 仅对磁盘上的实际更改做出反应。

错误：

- `400 {code: 'invalid_force_flag'}` — `force` 是非布尔值。
- `409 {code: 'workspace_init_conflict', path, existingSize}` — 文件存在非空白内容，并且 `force` 是 omitted/false. 正文携带绝对路径和大小（字节），因此 SDK 客户端可以呈现“覆盖 N 字节？”提示无需重新启动。

SSE 事件（工作区范围）：`workspace_initialized` 和 `{path, action, originatorClientId?}`。

#### `POST /workspace/mcp/reload`

将保留的 MCP 设置重新加载到工作区发现配置和每个 
 活动会话中。工作区限定的形式是
`POST /workspaces/:workspace/mcp/reload`。

请求正文：

```json
{ "forceReconnectAll": true }
```

`forceReconnectAll` 是可选的，默认为 `false`，保留 
 增量调节。如果为 true，则守护程序会在设置协调后重新连接每个符合条件的 
 配置的 MCP 服务器。或者，传递 
`forceReconnectWhich: ["server-a", "server-b"]` 仅重新连接命名的 
 服务器。这些选项是互斥的。强制重新连接会导致每个
传输读取另一个本地Qwen Code进程可能已
写入令牌存储的凭据；它不会启动 OAuth 授权流程。

路由返回`202 { "accepted": true }`；轮询`GET /workspace/mcp`以获取
最终连接状态。无效选项值返回 400。

#### `POST /workspace/mcp/:server/restart`

能力标签：`workspace_mcp_restart`。桥 → ACP extMethod `qwen/control/workspace/mcp/restart`。

通过ACP子级的`McpClientManager.discoverMcpToolsForServer`重新启动已配置的MCP服务器（断开+重新连接+重新发现）。预先检查来自 PR 14 v1 会计的实时预算快照，以便在预算饱和的工作区上重新启动会返回软拒绝，而不是触发 `BudgetExhaustedError` 级联。

请求正文为空 (`{}`)。路径参数是 URL- 编码的服务器名称，如 `mcpServers` 配置中所示。

响应 (200) — `restarted` 上的歧视联盟：

```json
{ "serverName": "docs", "restarted": true, "durationMs": 1234 }
```

```json
{
  "serverName": "docs",
  "restarted": false,
  "skipped": true,
  "reason": "budget_would_exceed"
}
```

软跳过原因（全部返回200）：

| `reason`                |含义|
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `'in_flight'`           |该服务器的另一次发现/重新启动已经在进行中。路线立即返回，而不是等待最初的承诺。调用者应在短暂延迟后重试。|
| `'disabled'`            |服务器已配置但在 `excludedMcpServers` 中列出。重新启动前重新启用。|
| `'budget_would_exceed'` |守护进程为`--mcp-budget-mode=enforce`，目标服务器当前不在`reservedSlots`中，并且实时总数已达到`clientBudget`。调用者应首先释放一个插槽。|

错误（非 2xx）：

- `400 {code: 'invalid_server_name'}` — 空路径参数。
- `404` — 服务器名称不在 `mcpServers` 配置中，或者不存在实时 ACP 通道（重新启动本质上需要实时 `McpClientManager` 实例）。
- `500` — 内部错误（例如 `ToolRegistry` 未初始化）。

SSE 事件（工作区范围）：`mcp_server_restarted` 与 `{serverName, durationMs, originatorClientId?}` 成功； `mcp_server_restart_refused` 和 `{serverName, reason, originatorClientId?}` 处于软跳过状态。

#### `POST /language`

功能标签：`user_language_sync`（有条件的 — 仅当设置持久性可用时才公布，与 `workspace_settings` 的条件相同）。桥接 → ACP extMethod `qwen/control/user/language` 每个具有实时通道的可信运行时。

无会话 ** 用户级 ** 语言同步（问题 #10234），适用于在任何会话存在之前需要切换语言的主机（例如欢迎页面）。所有权分类：进程全局 - 它仅改变用户全局状态（`~/.qwen/settings.json`，全局`~/.qwen/output-language.md`）加上尽力运行时刷新，因此它既不需要工作空间选择器，也不需要会话 ID。在非严格 `mutate()` 门后面注册，与同级 `POST /session/:id/language` 匹配。

守护进程是单个编写器：它在扇出之前保留 `general.language`（并且，当 `syncOutputLanguage` 为 true 时，`general.outputLanguage` 加上全局 `output-language.md`）。然后，每个受信任的运行时切换其自己的进程 UI 语言并从磁盘重新加载用户范围设置；当`syncOutputLanguage`为真时，它还会刷新每个本地会话的系统指令。与会话范围路由的区别：

- 项目绑定的输出语言文件被 NOT 重写 - 其工作区有自己的 `.qwen/output-language.md` 的会话保留该覆盖（项目范围在刷新时获胜）。
- 零会话和零实时运行时通道是**成功**，而不是错误；没有实时通道的运行时将被跳过（未失败），并在其通道下次生成时读取持久文件。
- 跳过不受信任的工作区运行时，与会话路由的 `403 untrusted_workspace` 状态相匹配。

要求：

```json
{ "language": "zh", "syncOutputLanguage": true }
```

`language` 必须是 `/capabilities` `supportedLanguages` 中列出的代码之一（加上 `'auto'`）； `syncOutputLanguage` 默认为 `false`。

响应（200）：

```json
{
  "language": "zh",
  "outputLanguage": "Chinese",
  "refresh": { "runtimes": 1, "sessions": 2, "failed": 0 }
}
```

`language` 是守护进程解析的 UI 语言环境，因此 `'auto'` 请求可以返回具体语言；主机应读取选择器状态的持久 `general.language` 设置。当 `syncOutputLanguage` 为 false 时，`outputLanguage` 为 `null`。 `refresh.runtimes` 计算在直播频道上应用切换的运行时间； `refresh.sessions` 每个会话系统指令刷新的总和； `refresh.failed` 聚合每个会话刷新失败和失败的运行时（扇出是尽力而为，并且在持久性成功后永远不会失败请求）。

错误：

- `400 {code: 'invalid_language', allowed: [...]}` — 未知语言代码。
- `400 {code: 'invalid_sync_flag'}` — `syncOutputLanguage` 是非布尔值。
- `400 {code: 'invalid_client_id'}` — `X-Qwen-Client-Id` 不是任何运行时已知的客户端（标头是可选的；当不存在会话时忽略它）。
- `500 {code: 'persist_error'}` — 用户设置或输出语言文件写入失败；失败的步骤及其之后的所有内容均未运行（较早应用的步骤，例如 `general.language` 写入，不会回滚）。
- `404` — 守护进程早于路由，或者是在没有设置持久性的情况下构建的（检查功能标签）。

SSE 事件（工作区范围，在每个运行时的会话总线上）：`language_changed` 和 `{language, outputLanguage, userLevel: true, originatorClientId?}`。 `userLevel: true` 标记将此扇出与会话路由的每会话事件区分开来，后者携带 `sessionId`。

### `GET /session/:id/events` (SSE)

订阅会话的事件流。

标题：

```
Accept: text/event-stream
Last-Event-ID: 42        ← optional, replays from after id 42
X-Qwen-Event-Epoch: ...  ← optional, pairs the cursor with its bus epoch
X-Qwen-Client-Id: ...    ← optional client identity and diagnostic correlation
```

查询参数：

|参数|必填|注释|
| ------------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `maxQueued`        |不|每个订阅者 ** 实时帧积压 ** 上限。范围`[16, 2048]`，默认256。订阅时强制推送的重播帧不受帧和字节上限的限制；真正消耗它们的是在订阅者仍在消耗大量 `Last-Event-ID: 0` 重播时到达的实时事件。冷重新连接的碰撞，这样实时尾部就不会在消费者跟上之前触发慢客户端警告/驱逐。实时序列化字节上限是固定的守护进程端（默认 2 MiB），并且没有查询参数。在 SSE 握手打开之前，超出范围/非十进制/存在但为空的值返回 `400 invalid_max_queued`。飞行前 `caps.features.slow_client_warning` — 旧守护进程默默地忽略该参数。|
| `connectReason`    |不|客户端报告的诊断提示：`initial`、`resume`、`prompt_restart`、`stream_end`、`transport_error`、`state_resync` 或 `unknown`。无效值标准化为 `unknown` 并且从不拒绝握手。守护程序不使用此字段进行身份验证、重播、逐出、重复数据删除或流替换。|
| `previousStreamId` |不|客户端报告的先前接受的REST/SSE流的UUID。无效值将被忽略。这只是尽力而为的沿袭，永远不会改变流行为。|

成功的握手包括`X-Qwen-SSE-Stream-Id: <uuid>`。浏览器网关必须保留该响应标头并通过 `Access-Control-Expose-Headers` 公开它。旧的守护进程或中介可能会省略它；客户必须正常继续并将血统视为不可用。该 ID 标识此物理 REST/SSE 连接并关联其守护程序生命周期、队列诊断和请求跟踪。

帧格式。 `data:` 行是 ** 完整事件包络 **，JSON- 字符串化在单行上 - `{id?, v, type, data, originatorClientId?}`。 ACP-特定负载（`sessionUpdate`、`requestPermission`参数等）位于信封的`data`字段下；信封自己的 `type` 与 SSE `event:` 行匹配。

```
id: 7
event: session_update
data: {"id":7,"v":1,"type":"session_update","data":{"sessionUpdate":"agent_message_chunk","content":{"type":"text","text":"…"}}}

id: 8
event: permission_request
data: {"id":8,"v":1,"type":"permission_request","data":{"requestId":"<uuid>","sessionId":"<sid>","toolCall":{...},"options":[...]}}

: heartbeat              ← every 15s, no payload

event: client_evicted    ← terminal frame, no id (synthetic)
data: {"v":1,"type":"client_evicted","data":{"reason":"queue_overflow","droppedAfter":42,"queueSize":256,"maxQueued":256,"queuedBytes":1800000,"maxQueuedBytes":2097152}}

event: client_evicted    ← terminal frame for byte overflow, no id (synthetic)
data: {"v":1,"type":"client_evicted","data":{"reason":"queue_bytes_overflow","droppedAfter":43,"queueSize":1,"maxQueued":256,"queuedBytes":1900000,"maxQueuedBytes":2097152,"eventBytes":300000}}
```

SSE- 级别 `id:` / `event:` 行重复 `envelope.id` / `envelope.type` 以实现 EventSource 兼容性。 Raw-`fetch` 消费者（SDK 的 `parseSseStream`）读取 JSON 信封中的所有内容，并忽略 SSE 前导行。

|事件类型|触发|
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `session_update`          |任何 ACP `sessionUpdate` 通知（LLM 块、工具调用、用法）|
| `permission_request`      |Agent 请求工具批准|
| `permission_resolved`     |一些客户通过 `POST /permission/:requestId` 投票同意|
| `permission_partial_vote` |（仅达成共识）已记录投票，但尚未达到法定人数。携带`{requestId, sessionId, votesReceived, votesNeeded, quorum, optionTallies}`。飞行前 `caps.features.permission_mediation`。|
| `permission_forbidden`    |投票被活动策略拒绝（`designated` 不匹配、`local-only` 非环回或 `consensus` 投票者不在快照中）。携带`{requestId, sessionId, clientId?, reason}`。飞行前 `caps.features.permission_mediation`。|
| `model_switched`          |`POST /session/:id/model`成功|
| `model_switch_failed`     |`POST /session/:id/model` 被拒绝|
| `session_died`            |Agent 孩子意外坠毁。 **终端：SSE流在此帧后关闭；会话已从 `byId` 消失。** 订阅者应通过 `POST /session` 重新连接以生成新的会话。|
| `slow_client_warning`     |订阅者本地：实时帧积压或实时序列化字节积压 ≥ 75% 已满。 **非终端** — 流继续；该警告是驱逐前的警告。携带 `{queueSize, maxQueued, lastEventId, queuedBytes?, maxQueuedBytes?, threshold?}`，其中 `threshold` 是 `frames`、`bytes` 或 `frames_and_bytes`。每个溢出剧集都会触发 ONCE；两次测量值均低于 37.5% 后重新准备。否 `id`（合成）。飞行前 `caps.features.slow_client_warning`。|
| `client_evicted`          |订阅者本地：队列溢出。 `reason` 是实时帧上限的 `queue_overflow`，实时序列化字节上限的 `queue_bytes_overflow`。 **终端：SSE流在此帧后关闭**（无`id` - 合成）。同一会话中的其他订阅者继续。|
| `stream_error`            |扇出期间守护程序端错误。 **终端：SSE流在此帧后关闭**（无`id` - 合成）。|

重新连接语义：

- 发送 `Last-Event-ID: <n>` 以从每个会话环中重播 `id > n` 事件（默认深度 **8000**，可通过 `qwen serve --event-ring-size <n>` 调整）。
- **间隙检测：** 在同一 epoch 内，如果第一个保留环 id 大于 `<n> + 1`，守护进程发出一个无 id 的 `state_resync_required` 框架与 `reason: 'ring_evicted'` 在重播幸存的后缀之前。其他原因是 `epoch_reset`, `seeded_replay_not_in_ring`, 和 `replay_budget_exceeded`;预算信号遵循所传送的重播前缀。参见 [重新同步原因和排序](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#resync-reasons-and-ordering>) 对于他们的触发因素， cursor 字段和恢复规则。
- SDK锁存器`awaitingResync`；客户端应调用 `POST /session/:id/load` 并从当前有界重播快照窗口重建。当旧的内存重播条目被删除时，该快照本身可能以 `history_truncated` 开头；该标记是信息性的，不得启动另一个重新同步循环。其可选的分页锚点在重播窗口驱逐时被冻结[](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#history-truncation-anchor>)。
- ID 在会话的事件总线纪元内是单调的，从 1 开始。被拒绝的发布不得消耗 id；请参阅[事件序列不变量](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#state-and-forward-compatibility>)。
- 合成帧（`client_evicted`、`slow_client_warning`、`stream_error`、`state_resync_required`、`replay_complete`）故意省略`id`，这样它们就不会为其他订户烧毁序列槽。 `replay_complete`标志着回放结束，包括空回放或不完整回放；这并不意味着重新同步已完成。

背压：

- 每个订阅者队列默认为 `maxQueued: 256` 实时项目加上守护程序拥有的 2 MiB 实时序列化字节上限。重新连接期间重播帧，`slow_client_warning` 和 `client_evicted` 绕过这两个上限。
- 通过 SSE 请求上的 `?maxQueued=N`（范围 `[16, 2048]`）仅覆盖帧上限。故意没有`?maxQueuedBytes`；客户端无法提高守护程序内存预算。
- 当订阅者的实时帧积压或实时字节积压超过 75% 时，总线会强制将 `slow_client_warning` 合成帧推送到该订阅者（每个溢出事件一次；在两个测量值都低于 37.5% 后重新准备）。流保持打开状态 - 警告是一个警告，因此客户端可以更快地耗尽或干净地分离+重新连接。
- 如果实时帧上限溢出，总线将发出 `client_evicted` 和 `reason: "queue_overflow"`。空的实时队列允许一个未更改的超大事件。在拒绝非空队列中的字节溢出之前，当 `fallbackText` 非空时，总线可能会将 MCP 应用程序的整个 `rawOutput.html` 丢弃到 `''`。如果该副本适合，则传递将继续，无需驱逐或重新同步信号。否则它会发出`reason: "queue_bytes_overflow"`。在任一驱逐中，终端框架都会被强制推送并且订阅将关闭。
- 此可选应用程序降级仅影响排队交付。直送、重播环保留原件；压缩重放窗口应用自己的预算。降级的副本带有与原始环条目相同的事件ID。因此，带有 id 的传递的有效负载不需要等于环的有效负载，并且在该 id 之后恢复将不会恢复其 HTML。需要文档的客户必须明确地重新分页持久记录，这也受到页面大小限制。

### `POST /session/:id/permission/:requestId`

进行与下面记录的相同的投票，但通过拥有 
 指定实时会话的运行时进行路由。新的多工作空间集成应使用此形式
，而不是旧的流程全局路由。飞行前
`caps.features.session_permission_vote`。

请求正文、调解策略、结果和成功响应
与`POST /permission/:requestId`相同。可选的
`X-Qwen-Client-Id`标头参与指定和共识策略。
失败使用稳定的`code`值（如注明）；格式错误的输入和丢失的
待处理请求竞赛可以省略`code`：

- `400` — 格式错误的投票主体（无 `code`）或无效的客户端身份
(`invalid_client_id`)，或 `invalid_option_id`（当未提供所选选项 
 时）。重新阅读提供的选项，而不是重试相同的投票。
- `403` - `permission_forbidden` 当活动策略拒绝投票者时，或者
`untrusted_workspace` 当非主要拥有的工作空间不受信任时。
 不受信任的主要所有者不受此信任检查的约束，并且投票可以
 被接受。
- `404` — `session_not_found`（当不存在实时所有者时），或者当没有 `code`（当
请求未待处理。
- `500` — `cancel_sentinel_collision` 当 agent 的 `allowedOptionIds` 时
包含保留的 `__cancelled__` 标记，或者当多个工作区声明会话时包含 `ambiguous_session_owner`
。
- `501` — `permission_policy_not_implemented` 对于此版本不包含的策略
实施。
- `503` — `workspace_runtime_unavailable` 当所属运行时为
不可用，或当守护进程不再接受
工作时`daemon_draining`。

它永远不会重试主桥。 TypeScript SDK 方法是
`respondToSessionPermission()`。

### `POST /permission/:requestId`

对待决的 `permission_request` 进行投票。有效的 ** 调解政策 ** 决定谁获胜：

|政策|行为|
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|`first-responder`（默认）|任何经过验证的选民都会获胜；后来选民得到`404`。 F3 之前的基线。|
| `designated`                |只有提示发起者（`originatorClientId`）决定；非发起者获得`403 permission_forbidden / designated_mismatch`。返回第一响应者以获取匿名提示。|
| `consensus`                 |N-of-M 选民必须同意（默认 `N = floor(M/2) + 1`，通过 `policy.consensusQuorum` 覆盖）。第一个到达 `N` 的选项获胜。非解决投票获得 `200` + `permission_partial_vote` SSE 帧。|
| `local-only`                |只有环回选民才能决定；远程调用者得到 `403 permission_forbidden / remote_not_allowed`。|

活动策略在 `policy.permissionStrategy` 下的 `settings.json` 中配置，并在 `/capabilities` 的 `body.policy.permission` 上显示。构建支持集的预检 `caps.features.permission_mediation`（带有 `modes: [...]`）。

> **F3(#4175)：多客户端权限协调。**F3添加了上述四个策略。预F3守护进程硬编码第一响应程序；当配置的策略为`first-responder`时，线路形状保持逐位不变。新事件（`permission_partial_vote`、`permission_forbidden`）是附加的——旧的 SDK 将它们视为 `unrecognized_known_event` 并优雅地忽略。

> **权限超时（默认禁用）。** A `permission_request`
>  保持待处理状态，直到：(a) 某些客户端在此投票，(b) `POST /session/:id/cancel`
>  触发，(c) HTTP 客户端驱动提示断开连接
> （中间提示取消将未完成的权限解析为`cancelled`），
> (d) 会话被终止，(e) 守护进程关闭，**或
> (f) 其配置的超时触发**。超时时，agent 的 
> `requestPermission` 将 
>  解析为 `{outcome: 'cancelled'}`，审核环记录 
> `permission.timeout` 条目，守护进程 stderr 发出单行 
>  面包屑，并且SSE总线扇出标准
> `permission_resolved`取消了框架，以便订户清理。 
> 共享超时可通过
> `BridgeOptions.permissionResponseTimeoutMs`或
> `qwen serve --permission-response-timeout-ms`进行配置。它的默认值为`0`，因此
> 普通权限和`ask_user_question`都会无限期地等待人类
> 的决定。投票者取消、会话取消、断开连接清理和
> 守护进程关闭仍然会将待处理的交互解析为已取消。

要求：

```json
{
  "outcome": {
    "outcome": "selected",
    "optionId": "proceed_once"
  }
}
```

结果：

- `{ "outcome": "selected", "optionId": "<one-of-the-options>" }` — 根据 agent 提供的选择，接受/拒绝/继续一次/等等
- `{ "outcome": "cancelled" }` — 删除请求（与 `cancelSession` / `shutdown` 内部执行的操作匹配）

回应：

- `200 {}` — 您的投票已被接受（已在共识法定人数下记录为OR）
- `400` — 格式错误的投票主体（无 `code`）、`invalid_client_id`，或
`invalid_option_id` 当未提供所选选项时；重新阅读
提供的选项，而不是重试相同的投票
- `403 { "code": "permission_forbidden", "reason": "designated_mismatch" | "remote_not_allowed", "requestId", "sessionId" }` — F3：活动政策拒绝了您的投票
- `404 { "error": "..." }` — requestId 未知（已解决、从未存在或会话已被拆除）
- `500 { "code": "cancel_sentinel_collision", ... }` — F3：agent的`allowedOptionIds`包含保留哨兵`'__cancelled__'`； agent / 违反守护进程合约
- `501 { "code": "permission_policy_not_implemented", "policy": "<name>" }` - F3 前向兼容：策略文字已进入模式，但其中介分支尚未构建（当前无法访问；为未来策略保留）

投票成功后，每个连接的客户端都会看到具有相同 `requestId` 的 `permission_resolved` 和所选的 `outcome`。在`consensus`下，中间投票另外扇出`permission_partial_vote`直到法定人数。

### 验证设备流路由（问题#4175 PR 21）

该守护进程代理 OAuth 2.0 设备授权授予 (RFC 8628)，因此远程 SDK 客户端可以触发登录，其令牌落在 ** 守护进程 ** 文件系统上 — 而不是客户端上。守护进程轮询 IdP 本身；客户端的唯一工作是显示验证 URL + 用户代码并（可选）订阅 SSE 完成事件。

能力标签：`auth_device_flow`（始终公布）。 
v1 中支持的提供商：`qwen-oauth`。

> [！注意]
>
> Qwen OAuth 免费套餐已在 2026-04-15 上停止。将 `qwen-oauth` 视为本协议中的 
>  旧版 v1 提供商标识符；新客户应该更喜欢
> 当前支持的身份验证提供程序（如果可用）。

**运行时位置。**守护进程永远不会生成浏览器——即使它可以。客户端决定是否本地调用`open(verificationUri)`；在无头 Pod（规范 B 模式部署）上，用户在拥有浏览器的任何设备上打开 URL。推荐的UX参见`docs/users/qwen-serve.md`。

**事件中没有代币泄漏。** `auth_device_flow_started`仅携带`{deviceFlowId, providerId, expiresAt}`。用户代码和验证URL在POST201主体中通过`GET /workspace/auth/device-flow/:id`点对点返回；它们从未在 SSE 上播出。

**每个提供者单例。**当流待处理时，同一提供者的第二个`POST`是幂等接管——它使用`attached: true`返回现有条目，而不是启动新的IdP请求。

#### `POST /workspace/auth/device-flow`

严格的变异门：无令牌可信环回主请求通过。到达没有可信环回权限的网关的无令牌主请求会收到 `401 token_required`；丢失或无效的配置凭据以及未配对的本地控制凭据会提前通过普通 `401 Unauthorized` 被拒绝。

要求：

```json
{ "providerId": "qwen-oauth" }
```

响应（`201`新开始，`200`幂等接管）：

```json
{
  "deviceFlowId": "fa07c61b-…",
  "providerId": "qwen-oauth",
  "status": "pending",
  "userCode": "USER-1",
  "verificationUri": "https://chat.qwen.ai/api/v1/oauth2/device",
  "verificationUriComplete": "https://chat.qwen.ai/api/v1/oauth2/device?user_code=USER-1",
  "expiresAt": 1700000600000,
  "intervalMs": 5000,
  "attached": false
}
```

错误：

- `400 unsupported_provider` — 未知 `providerId`（响应包括 `supportedProviders`）
- `409 too_many_active_flows` — 达到工作空间上限 (4)；使用 `DELETE` 取消一项
- `401 token_required` — 严格门在没有可信环回权限的情况下拒绝无令牌主请求
- `502 upstream_error` — IdP 返回意外错误

#### `GET /workspace/auth/device-flow/:id`

读取当前状态。待处理条目回显 `userCode/verificationUri/expiresAt/intervalMs`；终端条目（5-最小宽限）将其丢弃并显示`status` +可选的`errorKind/hint`。

对于未知 id 和宽限后驱逐条目，返回 `404 device_flow_not_found`。

#### `DELETE /workspace/auth/device-flow/:id`

幂等取消：

- 待处理条目 → `204` + 发出 `auth_device_flow_cancelled`
- 终端输入 → `204` 无操作（无事件重新发出）
- 未知 ID → `404`

#### `GET /workspace/auth/status`

待处理流 + 支持的提供商的快照：

```json
{
  "v": 1,
  "workspaceCwd": "/work/bound",
  "providers": [],
  "pendingDeviceFlows": [
    {
      "deviceFlowId": "fa07c61b-…",
      "providerId": "qwen-oauth",
      "expiresAt": 1700000600000
    }
  ],
  "supportedDeviceFlowProviders": ["qwen-oauth"]
}
```

#### 设备流 SSE 事件

五个类型的事件（工作区范围，扇形分布到每个活动会话总线）：

- `auth_device_flow_started` `{deviceFlowId, providerId, expiresAt}` — POST 成功； SDK 应该订阅（这里没有 userCode，如果需要可以通过 GET 获取）
- `auth_device_flow_throttled` `{deviceFlowId, intervalMs}` — 上游守护进程 `slow_down`；轮询 GET 的客户端应提高其间隔以匹配
- `auth_device_flow_authorized` `{deviceFlowId, providerId, expiresAt?, accountAlias?}` — 凭证保留； `accountAlias` 是非 PII 标签（绝不是 email/phone）
- `auth_device_flow_failed` `{deviceFlowId, errorKind, hint?}` — 终端； `errorKind` 是`expired_token | access_denied | invalid_grant | upstream_error | persist_failed` 之一。 `persist_failed` 是守护进程内部的：IdP 交换成功，但守护进程无法持久存储凭据（EACCES / EROFS / ENOSPC）。一旦底层磁盘状况修复，用户应重试。
- `auth_device_flow_cancelled` `{deviceFlowId}` — DELETE 成功处理待处理条目

> **与 MCP- 不兼容。** MCP 授权规范 (2025-06-18) 强制执行 OAuth 2.1 + PKCE 带有重定向回调的授权代码，不适用于 headless-pod 守护进程。模式 B 的设备流表面是守护进程专用的 — 针对 MCP- 兼容服务器的客户端应使用不同的身份验证路径。

### 会话附件块上传

能力：`session_attachment_chunk_upload`。下面的所有四个路由都是实时会话运行时所拥有的突变。它们使用现有的承载和可选的 `X-Qwen-Client-Id` 标头。上传属于其创建会话存储和确切的客户端身份；省略的身份与指定的客户端不同。

让`U = /session/:id/attachment-uploads`：

|请求|身体|回应|
| ---------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------- |
| `POST U`                           |JSON `{ name, mimeType, size }`，最多 4 KiB| 201 `{ uploadId }`                                                              |
| `POST U/:uploadId/chunks?offset=N` |原始 `application/octet-stream`，最多 512 KiB|200 `{ offset }`，下一个字节偏移量|
| `POST U/:uploadId/complete`        |不需要身体； JSON 正文被解析并忽略|200现有附件参考`{ type, attachmentId, mimeType, size }`|
| `DELETE U/:uploadId`               |空|204 表示接收、失败或缺少上传； 409正在完成或已完成|

最大附件仍然是8 MiB。元数据遵循现有的filename/MIME规则；空图像文件无效。发送连续的 512 KiB 块，最后一个块具有精确的余数。仅可以使用相同的偏移量、长度和字节重试紧邻的前一个块。间隙、重叠、更改重试或过早完成返回 409；格式错误的输入返回 400，过大的输入 413，不支持的块媒体类型或 JSON 字符集返回 415 `invalid_attachment_upload_content_type`。创建和块请求上不支持的请求编码返回 415 `invalid_attachment_upload_encoding`。未知、过期或不同拥有的上传 ID 在三个 POST 路由上返回 404； DELETE 对于缺少上传的情况返回 204，在完成期间或完成后返回 409。创建和块请求不接受压缩体。完整的路由使用全局 JSON 解析器，因此有效的压缩 JSON 正文可能会被忽略，而格式错误的 JSON 在完成之前会被拒绝。

完成验证存储前后的 session/client/runtime 所有权。并发或重复完成返回相同的结果，无需再次写入。并发会话复制暂时返回`503 attachment_upload_store_busy`和`retryable: true`；复制结束后，暂存字节仍可通过相同的上传 ID 完成。对于该上传，实际存储故障仍然是终端ID。接收上传的内容将在创建后五分钟后过期。 Success/failure 收据将在五分钟后过期，并且每个守护进程的上限为 1024。会话 close/delete 和最终客户端注销会使未完成的上传无效。只有完整的附件才能阅读、引用或复制。 DELETE 无法回滚已完成或已完成的附件；丢失响应后可以保留已完成的文件。

暂存仅限于进程中所有运行时的 128 MiB 和 32 活动上传，每个会话存储最多有 8 个活动上传。耗尽返回`429 attachment_upload_capacity_exceeded`；现有的请求速率限制也适用。这是暂存缓冲区限制，而不是总进程内存。临时 ID 在守护程序重新启动后不会继续存在。

TypeScript SDK 在通告时自动使用此协议来处理大于 512 KiB 的附件，每个客户端有两个并发文件和顺序块。即使使用 ACP 传输，它也使用经过身份验证的 REST，将有效发现缓存 60 秒，并保留较小文件和较旧守护程序的现有原始端点。 Append/completion网络错误、请求超时或502/503/504后最多重试两次；速率限制重试在五分钟操作期限内遵守 `Retry-After`。不重试不明确的创建和容量失败。失败的上传会进行一次独立的两秒清理尝试。 `DaemonAttachmentUploadError`保留原因和`httpStatus`；它的 `status` 省略了上传内部 404，因此消费者不会将其误认为是不受支持的旧版附件路由。

有关生命周期和兼容性详细信息，请参阅[完整设计](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/session-attachment-chunk-upload.md>)。

## 流媒体格式

事件作为标准 EventSource 帧发出。守护进程每帧写入一行 `data:`（JSON 在 `JSON.stringify` 之后没有嵌入换行符）； `packages/sdk-typescript/src/daemon/sse.ts` 处的 SDK 解析器处理接收端规范允许的多 `data:` 形式。

## 流式传输期间出现错误帧

如果桥迭代器在为 SSE 订户提供服务时抛出异常，则守护进程会发出终端 `stream_error` 帧（无 `id`）。 `data:` 线是完整的信封（与本文档中所有其他 SSE 框架的形状相同）；实际的错误消息位于 `envelope.data.error` 下：

```
event: stream_error
data: {"v":1,"type":"stream_error","data":{"error":"<message>"}}
```

然后连接关闭。

## 环境变量

|瓦尔|目的|
| ------------------- | -------------------------------------------------------------- |
| `QWEN_SERVER_TOKEN` |不记名令牌。启动时删除 leading/trailing 空格。|

## 源码布局

|路径|目的|
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `packages/cli/src/commands/serve.ts`                 |yargs 命令 + 标志模式|
| `packages/cli/src/serve/run-qwen-serve.ts`           |监听器生命周期+信号处理|
| `packages/cli/src/serve/server.ts`                   |快速应用程序组装、中间件排序和剩余直接路由|
| `packages/cli/src/serve/routes/*.ts`                 |重点 Express 路由组，包括会话、SSE、工作区身份验证、工作区状态和文件路由|
| `packages/cli/src/serve/auth.ts`                     |承载 + 主机白名单 + CORS 拒绝|
| `packages/cli/src/serve/acp-session-bridge.ts`       |CLI-用于生成或附加、每会话 FIFO 和权限注册表的本地桥兼容性外观|
| `packages/acp-bridge/src/status.ts`                  |只读守护进程状态线类型 + `ServeErrorKind` + `BridgeTimeoutError` + `mapDomainErrorToErrorKind`|
| `packages/cli/src/serve/env-snapshot.ts`             |从 `process.*` 状态构建 `/workspace/env` 有效负载的纯助手，包括凭证编辑|
| `packages/acp-bridge/src/eventBus.ts`                |有界异步队列+重放环|
| `packages/sdk-typescript/src/daemon/DaemonClient.ts` |TS客户端|
| `packages/sdk-typescript/src/daemon/sse.ts`          |EventSource 帧解析器|
| `integration-tests/cli/qwen-serve-routes.test.ts`    |18 案例，无 LLM|
| `integration-tests/cli/qwen-serve-streaming.test.ts` |3 案例，由本地假 OpenAI 服务器支持的真实 `qwen --acp` 孩子（仅限 POSIX；在 Windows 上跳过）|


## 原文对照

<details>
<summary>展开完整原文</summary>

# `qwen serve` HTTP protocol reference

Stage 1 of the [qwen-code daemon design](<https://github.com/QwenLM/qwen-code/issues/3803>). All routes live under the daemon's base URL (default `http://127.0.0.1:4170`).

This reference describes HTTP routes and SSE framing. Read it together with
the [typed event schema](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md>) for payload contracts,
resync reasons, and history anchors, the
[event bus and replay guide](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/10-event-bus.md>) for delivery and
backpressure, and [capabilities and versioning](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/11-capabilities-versioning.md>)
for the existing `v1` protocol and compatibility rules. The
[daemon documentation index](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/00-index.md>) links the full contract set.

## Authentication

When the daemon was started with `--token` or `QWEN_SERVER_TOKEN` — or bound non-loopback with neither, which generates an ephemeral bearer and prints it once at startup — **every normal API route except `/health` on ordinary loopback binds** must carry:

```
Authorization: Bearer <token>
```

Without a configured token on the loopback default, the header is optional and requests arriving through the primary listener have full operator API authority. Workspace trust, session ownership, `X-Qwen-Client-Id`, permission, feature, validation, and resource checks still apply. Token comparison is constant-time. 401 responses are uniform across `missing header` / `wrong scheme` / `wrong token`.

**`--open-with-auth`.** This default-off CLI mode requires a loopback bind and an available Web Shell. It reuses the normal `--token`-over-`QWEN_SERVER_TOKEN` selection, or generates 32 random bytes encoded as base64url before daemon startup when that selection is empty. The browser receives the selected bearer through `#token=` and stores it per tab; the protocol and middleware see an ordinary configured token. Fragment delivery keys on the resolved token, not on this flag: any `--open` launch attaches the resolved bearer — configured or generated — to the launched URL's `#token=` fragment (visible to local users via `ps` / `/proc`, as the launcher warns), so a non-loopback bind with bare `--open` hands its generated bearer to the browser the same way; this flag's distinct contributions are token _generation_ on loopback and the browser-ineligible manual-URL fallback. Only direct embedded callers that ignore `RunHandle.resolvedToken`, and clients that never launch a browser, receive no automatic credential. Browser-ineligible environments print the secret-bearing fragment URL for manual opening. Loopback `/health` and static Web Shell assets retain the exemptions described below; `--require-auth` still gates `/health`.

Channel webhook ingress (`POST /channels/:channelName/webhooks/:source`) is separate from this bearer contract in every mode. When mounted, it is registered before `bearerAuth` and authenticates with its configured `x-qwen-webhook-secret`; rotating the daemon bearer does not rotate webhook source secrets.

**`/health` exemption** (Bctum): on loopback binds (`127.0.0.0/8` / `localhost` / `::1` / `[::1]`) `/health` is registered BEFORE the bearer middleware, so liveness probes inside the pod don't need to carry the token even when the daemon was started with `--token`. Non-loopback binds (`--hostname 0.0.0.0` etc.) gate `/health` with the other normal API routes — see the [`GET /health`](<#get-health>) section for the rationale.

**`--require-auth` (#4175 PR 15).** Pass this flag at boot to extend the "must have a token" rule to loopback as well. Boot fails when no token source resolves — on loopback that means `--token`, `QWEN_SERVER_TOKEN`, or `--open-with-auth` (which installs its own generated token before boot, so `--require-auth --open-with-auth` starts). The fail-fast is loopback-only: a non-loopback bind resolves the ephemeral bearer it generates when neither configured source is present, and that satisfies the flag. The `/health` exemption is dropped either way, so `/health` also requires `Authorization: Bearer …`.

When the flag is on, the global `bearerAuth` middleware gates **every normal API route** — including `/health` and `/capabilities`. Channel webhook ingress remains independently shared-secret-authenticated, and Web Shell document and asset routes remain pre-auth. An **unauthenticated** client therefore cannot pre-flight `caps.features` to discover that auth is required: the discovery surface for that case is the **401 response body** itself (uniform across bearer-gated routes per the [Authentication](<#authentication>) section). The `require_auth` capability tag is a **post-authentication confirmation** — once a client successfully authenticates and reads `/capabilities`, the tag's presence confirms the daemon was started with `--require-auth` (useful for audit / compliance UIs and for SDK clients to surface "this deployment is hardened" in a settings panel). Strict mutation routes accept trusted-loopback primary-listener requests, bearer-authenticated requests, or paired Local Control requests. Non-trusted token-less embeds still receive `401 { code: "token_required", error: "…" }`; with `--require-auth`, global bearer middleware rejects first with the legacy `Unauthorized` body.

**`--allow-origin <pattern>` (T2.4 [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>)).** Browser clients hitting the daemon cross-origin are blocked by default — a request carrying an `Origin` header returns `403 {"error":"Request denied by CORS policy"}` because CLI/SDK clients never send `Origin` and the daemon treats its presence as a sign the request came from a browser context the operator has not opted into. One exception precedes the wall on a non-loopback bind with a token: a same-origin request (`Origin` equal to the direct socket scheme plus the normalized `Host` authority) is bearer-authenticated and its `Origin` stripped **on the primary listener** — with a valid bearer the route's own status follows, with a missing or invalid bearer a `401`, **except the pre-auth Web Shell document, `/assets/*`, `/manifest.webmanifest`, `/sw.js`, and `/mcp-app-sandbox` routes, which are served without a credential as in every other mode** — and only cross-origin or non-matching `Origin` values keep the `403` envelope. Pass `--allow-origin <pattern>` (repeatable) at boot to install an allowlist instead of the wall. Each pattern is either:

- The literal `*` — admit any origin. **Risky**: boot refuses when `*` is configured but no bearer token resolves. The guard reads the _resolved_ token — `--token`, `QWEN_SERVER_TOKEN`, `--open-with-auth`'s generated loopback token, or the ephemeral bearer a non-loopback bind generates when neither configured source is present — so this refusal is loopback-only. The boot breadcrumb emits a stderr warning when `*` is in the list. **Recommendation**: pair with `--require-auth` on loopback binds so `/health` is also gated by the bearer — it's registered before the bearer middleware on loopback by default (so k8s/Compose probes can reach it without a token), and a `*` allowlist makes it reachable from any cross-origin browser. `--require-auth` still leaves the Web Shell static assets (`/`, `/assets/*`, `/manifest.webmanifest`, `/sw.js`, and `/session/:id`, `/plugins`, `/channels`, `/live`, `/scheduled-tasks`, `/goals`, and `/settings` document navigations) pre-auth on loopback by design — they are mounted before the bearer middleware — so under a `*` allowlist they remain readable from any cross-origin browser; `--no-web` removes that surface. On non-loopback binds the bearer is already mandatory at boot and `/health` is registered behind it. Normal API routes are bearer-gated, channel webhook ingress retains its own shared-secret gate, and Web Shell static assets (`/`, `/assets/*`, `/manifest.webmanifest`, `/sw.js`, and `/session/:id`, `/plugins`, `/channels`, `/live`, `/scheduled-tasks`, `/goals`, and `/settings` document navigations) remain pre-auth unless `--no-web` removes them.
- A canonical URL origin — `<scheme>://<host>[:<port>]`. **No trailing slash, no path, no userinfo, no query.** Boot refuses with `InvalidAllowOriginPatternError` if the entry fails the round-trip `new URL(pattern).origin === pattern`; the error message names the bad pattern and the canonical form. Strict-by-intent: silent normalization (e.g. trimming a trailing `/`) would let typos slip through and accept ambiguous input. Without a resolved token — which after generation means a loopback bind — HTTP(S) entries are limited to loopback hosts; a non-loopback browser origin requires a token because it can otherwise drive the full operator API, including code execution as the daemon user. Explicit browser-extension origins keep their existing tokenless local-automation path. Startup logs the authority granted to any tokenless allowed browser origin.

Matched origins receive the standard CORS response headers on every request:

```
Access-Control-Allow-Origin: <echoed origin>
Vary: Origin
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE, OPTIONS
Access-Control-Allow-Headers: Authorization, Content-Type, X-Qwen-Client-Id, Last-Event-ID, X-Qwen-Event-Epoch
Access-Control-Max-Age: 86400
Access-Control-Expose-Headers: Retry-After, X-Qwen-Event-Epoch, X-Qwen-SSE-Stream-Id
```

`Access-Control-Allow-Origin` echoes the request's origin verbatim (lowercase / uppercase as the browser sent it) rather than the literal `*`, even under the `*` pattern — browser caches key responses on it paired with `Vary: Origin`, and echoing leaves room to add `Access-Control-Allow-Credentials` in a later release without a schema change. The exposed headers let browser clients honor retry hints, retain the SSE epoch, and correlate accepted physical streams. `Access-Control-Allow-Credentials` is **NOT** sent today: configured daemon credentials use bearer-in-`Authorization`, which works cross-origin without `credentials: 'include'`; trusted-loopback authority needs no browser credential.

OPTIONS preflight requests (OPTIONS with `Access-Control-Request-Method` or `Access-Control-Request-Headers`) short-circuit with `204 No Content` plus the headers above. This is the conventional CORS pattern and is safe — the preflight only confirms which methods/headers the daemon will accept; the actual subsequent request still runs the Host gate and then either bearer/listener authority or the channel webhook shared-secret gate before any state is read or mutated. Plain OPTIONS requests from matched origins keep flowing downstream with CORS headers attached.

Origins that don't match the allowlist still get `403 {"error":"Request denied by CORS policy"}` — same envelope as the default wall, so clients that already parsed the wall's response don't have to special-case allowlist-deployed daemons. The reject path **does not** emit any `Access-Control-*` headers (the browser would ignore them, and emitting would indirectly advertise the allowlist size through header presence).

The configured pattern list is intentionally NOT echoed in `/capabilities` — a browser client already knows its own origin (it called the daemon, after all), and surfacing the list would let an unauthenticated reader of `/capabilities` enumerate every trusted origin (useful recon for a misconfigured deployment). SDK clients gate on the `caps.features.allow_origin` tag for "this daemon honors cross-origin browser hits" without needing to know which specific origins.

Loopback self-origin requests (e.g. the Web Shell calling the daemon at the same `127.0.0.1:port`) are handled by a **separate** Origin-strip shim that runs BEFORE the CORS middleware and removes the `Origin` header for `127.0.0.1:port` / `localhost:port` / `[::1]:port` / `host.docker.internal:port` or the exact bound loopback address and port. It also accepts the scheme-matched port-less forms that browsers send for default ports: `http://host` on port 80 and `https://host` on port 443. These requests pass through regardless of `--allow-origin` configuration — operators don't need to list the daemon's own port to make the Web Shell work.

## Common error shape

5xx responses carry the original error's `code` and `data` when present (JSON-RPC style — the ACP SDK forwards `{code, message, data}` from the agent):

```json
{
  "error": "Internal error",
  "code": -32000,
  "data": { "reason": "model quota exceeded" }
}
```

Malformed JSON in a request body returns:

```json
{ "error": "Invalid JSON in request body" }
```

with status `400`.

`SessionNotFoundError` for an unknown session id returns:

```json
{
  "error": "No session with id \"<sid>\"",
  "sessionId": "<sid>",
  "code": "session_not_found"
}
```

with status `404`. A concurrent close uses `code: "session_closing"`.

`WorkspaceMismatchError` for a `POST /session` whose `cwd` doesn't canonicalize to a registered workspace returns `400` with:

```json
{
  "error": "Workspace mismatch: daemon is bound to \"…\"",
  "code": "workspace_mismatch",
  "boundWorkspace": "/path/the/daemon/uses/as-primary",
  "requestedWorkspace": "/path/in/the/request"
}
```

Use this to detect mismatch pre-flight: read `workspaceCwd` off `/capabilities` and omit `cwd` from `POST /session` (it falls back to the primary workspace), or when `multi_workspace_sessions` is advertised choose one of `workspaces[].cwd`.

`POST /session` past the daemon's `--max-sessions` cap returns `503` with a `Retry-After: 5` header and:

```json
{
  "error": "Session limit reached (20)",
  "code": "session_limit_exceeded",
  "limit": 20,
  "scope": "workspace"
}
```

When the effective daemon-wide total cap rejects a fresh session — `--max-total-sessions`, or the default described under `limits.maxTotalSessions` — the same response shape is returned with `"scope": "total"`.

Attaches to existing sessions are NOT counted toward the cap, so an idle daemon's reconnects keep working even when at-capacity.

If the ACP channel initialization budget expires before `newSession` is dispatched, `POST /session` returns `504` with `Retry-After: 5` and:

```json
{
  "error": "AcpSessionBridge initialize timed out after 10000ms",
  "code": "init_timeout",
  "errorKind": "init_timeout",
  "retryable": true,
  "sideEffectPossible": false,
  "phase": "channel.initialize",
  "timeoutMs": 10000
}
```

`timeoutMs` — and the numeric suffix of `error` — reflect the daemon's configured `--initialize-timeout-ms` budget (the values above are the default). The full safe-retry shape above is emitted only for plain session creation, where channel initialization strictly precedes every durable mutation: on that path the `sideEffectPossible: false` field is authoritative because initialization precedes the ACP `newSession` request, and a client that understands this structured contract may retry after the advertised delay without risking a duplicate Session.

Requests carrying `branch` or `worktree`, and initialize timeouts surfaced by mutation-bearing routes other than plain creation (for example `POST /session/:id/branch` and `POST /session/:id/side-task`, where a committed fork can outlive the failed handshake), return the same `504` with `code: "init_timeout"`, `phase`, and `timeoutMs` — but WITHOUT `Retry-After`, `retryable`, or `sideEffectPossible`. For those the mutation outcome is unknown: branch and worktree preparation mutates git before the channel initializes, and the rollback attempted on failure is best-effort (a failed checkout rollback leaves the workspace on the new branch, and a retry may surface a branch-already-exists conflict). Clients that classify all 5xx mutation responses as ambiguous remain conservatively fail-closed, and should apply the same policy to the reduced shape. `POST /session/:id/load` and `POST /session/:id/resume` surface the same reduced `init_timeout` `504` when channel initialization times out before the restore request is dispatched (the `ensureChannel` stage); unlike the `session_restore_timeout` `504` documented in their Errors lists, this shape carries no `Retry-After`, no `retryable`, and installs no fence because the restore was never dispatched. The `newSession` dispatch timeout on `POST /session` is an exception: it returns `504` with `code: "init_timeout"`, `retryable: true`, and a budget-derived `Retry-After`, but without `phase` or `sideEffectPossible`, because the creation outcome is ambiguous. All other timeout labels keep the generic mapping.

`RestoreInProgressError` — emitted by `POST /session/:id/load`, `POST /session/:id/resume`, or a caller-supplied-id `POST /session` when another registration already owns that id — returns `409` and:

```json
{
  "error": "Session \"<sid>\" is already being restored via session/<resume|load>; retry session/<load|resume> after it completes",
  "code": "restore_in_progress",
  "reason": "restore_in_progress",
  "retryable": true,
  "sessionId": "<sid>",
  "activeAction": "load",
  "requestedAction": "resume"
}
```

Fired when a `session/load` is issued for an id that already has a `session/resume` in flight (or vice versa), or when a caller-supplied-id spawn races either restore direction. Wait at least `Retry-After` seconds and retry. Same-action races (`load` vs `load`, `resume` vs `resume`) coalesce instead of erroring while the restore is active.

`reason` distinguishes two fences that share this code, and the `Retry-After` header tracks it:

| `reason`                     | Meaning                                                                                                                                                                              | `Retry-After`                                                                                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| `restore_in_progress`        | An ordinary restore is running.                                                                                                                                                      | `5` (matching `session_limit_exceeded`)                                                                                        |
| `awaiting_abandoned_cleanup` | The public caller already got a timeout (`504` for restore, or `init_timeout` for session initialization) and the non-cancellable ACP request plus its cleanup have not settled yet. | the timed-out operation's budget in seconds — restore, or initialization for a caller-supplied-id spawn — clamped to `5`–`120` |

The public restore request is governed by `limits.sessionRestoreTimeoutMs` (default 60s). After a `504` the id stays fenced until the late ACP request and cleanup settle, so a client that keeps retrying at the ordinary 5-second cadence would spin against a 409 it cannot clear — honor the budget-derived hint that comes with `awaiting_abandoned_cleanup`.

`SessionWorkspaceConflictError` — emitted by `POST /session/:id/load` and `POST /session/:id/resume` when the requested `cwd` targets one registered workspace but the same session id is already live or being restored by another runtime — returns `409` with:

```json
{
  "error": "Session \"<sid>\" is already live or restoring in another workspace runtime.",
  "code": "session_workspace_conflict",
  "sessionId": "<sid>",
  "workspaceCwd": "/requested/workspace",
  "workspaceId": "requested-workspace-id",
  "liveWorkspaceCwd": "/live/owner/workspace",
  "liveWorkspaceId": "live-owner-workspace-id"
}
```

Clients should retry with the owning workspace or wait for the in-flight restore to finish before restoring the id into a different workspace. Same-workspace restore races continue to use the bridge's `restore_in_progress` / coalescing behavior.

`SessionArchivedError` is emitted when a caller tries to load or resume a session whose JSONL is under `chats/archive/`:

```json
{
  "error": "Session \"<sid>\" is archived. Unarchive it before loading.",
  "code": "session_archived",
  "sessionId": "<sid>"
}
```

with status `409`.

`SessionArchivingError` is emitted when a session archive or unarchive transition is already in flight for the same id:

```json
{
  "error": "Session \"<sid>\" is being archived or unarchived; retry later.",
  "code": "session_archiving",
  "sessionId": "<sid>"
}
```

with status `409` and `Retry-After: 5`.

## Capabilities

The daemon advertises its supported feature tags from the serve capability
registry. Clients **must** gate UI off `features`, not off `mode` (per design
§10).

```
['health', 'capabilities', 'session_create', 'session_startup_config', 'session_id_override', 'session_scope_override',
 'session_load', 'session_resume', 'session_transcript',
 'unstable_session_resume',
 'session_list', 'session_catalog_batch', 'session_info', 'session_prompt', 'session_mid_turn_message_mutation',
 'session_cancel', 'session_events',
 'slow_client_warning', 'typed_event_schema',
 'session_set_model', 'client_identity', 'client_heartbeat',
 'session_permission_vote', 'permission_vote', 'workspace_mcp', 'workspace_skills',
 'workspace_skills_config_runtime',
 'workspace_providers', 'workspace_acp_preheat', 'workspace_acp_status',
 'auth_provider_install', 'workspace_memory',
 'workspace_agents', 'workspace_agent_generate', 'workspace_env',
 'workspace_preflight', 'session_context', 'session_context_usage',
 'session_supported_commands', 'session_tasks', 'session_monitor_tool_correlation', 'session_stats',
 'session_lsp', 'session_resources', 'session_status',
 'session_close', 'session_metadata', 'session_organization',
 'session_archive', 'mcp_guardrails',
 'workspace_mcp_manage', 'mcp_guardrail_events',
 'mcp_server_runtime_mutation',
 'workspace_file_read', 'workspace_file_bytes', 'workspace_file_write',
 'workspace_file_upload',
 'session_approval_mode_control', 'workspace_tool_toggle',
 'workspace_skill_settings_toggle', 'workspace_skill_settings_batch_toggle',
 'extension_batch_activation_v2', 'extension_activation_explicit_refresh',
 'extension_state',
 'workspace_settings', 'workspace_init', 'workspace_mcp_restart',
 'session_recap', 'session_generation', 'session_btw', 'session_shell_command',
 'standalone_sessions_v1', 'standalone_session_options_v1',
 'mcp_workspace_pool', 'mcp_pool_restart',
 'require_auth', 'allow_origin', 'auth_device_flow',
 'permission_mediation', 'prompt_absolute_deadline', 'writer_idle_timeout',
 'non_blocking_prompt', 'session_language', 'user_language_sync', 'session_rewind',
 'workspace_hooks', 'session_hooks', 'workspace_extensions',
 'session_branch', 'rate_limit', 'workspace_reload', 'channel_delivery',
 'multi_workspace_sessions', 'multi_workspace_session_rewind',
 'multi_workspace_session_shell', 'persistent_workspace_registration',
 'workspace_display_name', 'workspace_runtime_removal', 'workspace_runtime',
 'workspace_qualified_rest_core', 'workspace_qualified_voice',
 'workspace_qualified_memory', 'extension_management_v2', 'extension_git_credentials',
 'extension_local_path_install',
 'workspace_persisted_transcript',
 'workspace_session_export', 'workspace_archived_session_export',
 'workspace_session_live_state',
 'client_mcp_over_ws', 'cdp_tunnel_over_ws', 'browser_automation_mcp']
```

> Conditional tags appear only when their matching deployment toggle is on (see the table below). F3's `permission_mediation` tag is always-on and carries `modes: ['first-responder', 'designated', 'consensus', 'local-only']` so SDK clients can introspect the build-supported set; the runtime-active strategy is at `body.policy.permission`.

`session_startup_config` advertises optional `startupConfig: { modelServiceId, reasoningEffort? }` on ordinary and standalone creation. Always preflight this tag: older ordinary-create routes may silently ignore the object. `modelServiceId` is required (1–256 characters); `reasoningEffort` accepts `none`, `default`, `low`, `medium`, `high`, `xhigh`, or `max` when supported by the selected model. Omission applies only the model, without a reasoning setter or implicit `default`. The object cannot be combined with the legacy top-level `modelServiceId` or explicit single scope and implies a new thread. Startup never saves shared defaults; later session behavior is unchanged.

Success includes `modelApplied: true` and `startupConfigApplied: { modelServiceId, reasoningEffort?, effectiveReasoning? }`. The model selector is canonical. Only explicit reasoning requests include the two reasoning fields; effective state is `{ state: 'disabled' }`, `{ state: 'provider-default' }`, or `{ state: 'enabled', effort? }` (toggle-only reasoning has no effort). Invalid structure is `400 invalid_startup_config`; a rejected or unconfirmed selection is `422 startup_config_rejected`. Definite standalone rejection rolls back its owned recording and attempts to discard the empty output directory before returning; a recording rollback whose removal cannot be confirmed and transient startup failures retain `standalone_creation_outcome_unknown`, while a failed directory discard is only logged and the definite rejection is still returned. A success-body confirmation failure in the SDK is a protocol error, not an instruction to adopt a recovered session.

`session_scope_override` is the negotiation handle for the per-request `sessionScope` field on `POST /session` (see below). Older daemons silently ignore the field, so SDK clients should pre-flight `caps.features` for this tag before sending it.

`session_id_override` is the negotiation handle for the optional caller-supplied `sessionId` on `POST /session` and ACP `session/new` metadata. Clients must confirm that `caps.features` contains this tag before sending the field because older daemons may silently ignore it.

`persistent_workspace_registration` advertises durable registration for workspaces added at runtime. `POST /workspaces` accepts `{ "cwd": "/absolute/path", "persist": true }`; success includes `persisted: true`. Registrations are scoped to the daemon's canonical primary workspace under the user's Qwen home and are restored on the next daemon start. Omitting `persist` preserves process-local registration. `GET /workspace-registrations` lists the stored desired set, and `DELETE /workspace-registrations/:id` forgets an entry for the next restart without hot-removing an active runtime.

`workspace_display_name` advertises optional `displayName` input on `POST /workspaces`, workspace metadata updates through `PATCH /workspaces/:workspace`, and optional display-name fields in workspace projections. Names do not participate in lookup or routing: `id` and canonical `cwd` remain the only selectors, and duplicate names are allowed.

`workspace_runtime_removal` advertises synchronous hot removal through `DELETE /workspaces/:workspace`. Capability workspace entries add optional `removable`; only rows with `removable: true` may be removed. Removal also forgets every persistent registration alias for the runtime, but never deletes files, settings, transcripts, or archives.

`workspace_runtime` advertises `GET /workspace/runtime/status`, `POST /workspace/runtime/ensure`, and their `/workspaces/:workspace/runtime/...` equivalents. `ensure` accepts no capability selection: it starts or reuses the selected trusted workspace's ACP runtime and returns its lifecycle state and monotonic runtime epoch. The primary routes are owned only by the primary runtime; qualified routes resolve only the selected registered runtime and never fall back. A successful ensure renews the runtime's ten-minute keepalive window. Status is read-only and never starts the child. Concurrent ensures share one physical startup. Startup in progress or failure returns retryable `503 runtime_still_starting` or `503 runtime_initialization_failed`. Capability preparation may continue after the ensure observation budget expires; in that case ensure still returns the live runtime with a non-ready capability state for status polling. The capability is advertised only when all active runtime bridges provide the authoritative lifecycle snapshot; a selected legacy injected bridge returns `501 workspace_runtime_not_supported` instead of a guessed state or epoch.

`workspace_runtime_stop` independently advertises user-confirmed runtime stopping. `GET /workspaces/runtime-stop-options` is a read-only, process-global preview: it includes committed child count, modeled child ceiling, and each visible workspace's sessions, blocked reasons, channel ID, epoch, current stop token and latest receipt. Ordinary loaded sessions may be explicitly stopped; independent ACP clients, enabled schedules, pending scheduler/management/start work, memory tasks, workers, voice and special-purpose runtimes are blocked. `POST /workspaces/:workspace/runtime/stop` uses the strict mutation gate and only the selected trusted runtime. Send `confirmInterruptions: true`, `expectedChannelId`, `expectedRuntimeEpoch`, `expectedStopToken` and the exact `expectedSessionIds` from the preview. Never fall back to the primary runtime. The preview and every receipt list the runtime's live ACP channels as `channels` (`channelId`, `runtimeEpoch`, optional `executionEngine`); a runtime can have one channel per execution engine, and a stop covers all of them. `channelId` names the first listed channel and `runtimeEpoch` is the newest listed epoch, so a channel started after the preview makes the confirmation stale.

A successful response has `state: "stopped"`, `stopped: true` and `released: true`, after acknowledged session closes and the process-registry release of every listed child. Registration, files and saved history remain. Responses include affected, interrupted, closed and remaining session IDs. Stale or blocked confirmation is HTTP 409 (`workspace_runtime_stop_stale` / `workspace_runtime_stop_blocked`); partial close is 409 `workspace_runtime_stop_incomplete`; ongoing teardown or failed teardown without proven release is 503 `workspace_runtime_stop_in_progress` / `workspace_runtime_stop_failed`; unsupported management is 501 `workspace_runtime_stop_not_supported`. An unclean teardown that later proves registry release may return 200 with an error warning; unconfirmed session flush still returns a partial/error result. The total observation/close budget is 60 seconds. If it expires while a session close is still in flight, the response settles with `state: "failed"` and `503 workspace_runtime_stop_failed`; cleanup continues separately. The selected workspace stays isolated and its child remains counted until actual cleanup completion, even though the caller has received a failure. Read-only options can later report `stopped` after acknowledged closes and proven release, or `incomplete` when persistence was unconfirmed; the already returned failure snapshot does not change. If the budget is instead exhausted between session closes, the response settles with `state: "incomplete"` and `409 workspace_runtime_stop_incomplete`: no further close or forced kill is started, the stop's admission guard is released (the surviving child remains counted as usual), and stopping the remaining sessions requires a fresh preview and confirmation. A flush refusal is not bypassed by force-killing the remaining sessions. Root exit or a fall in the global process count alone does not establish release of the selected child. On Windows, where the registry does not track descendant process groups, the root's settled exit is the release evidence, so `released: true` does not prove the process tree is gone there.

Repeated confirmation of the latest consumed token observes the same operation. A fresh confirmation can retry an incomplete stop, rotating the token; older consumed tokens then become stale. If a response is lost, read options and match the latest receipt by workspace and stop token instead of stopping another target. Receipts may be unavailable after removal, trust changes, or daemon/bridge recreation; absence leaves the result unknown and never authorizes continuation. SDK `runtimeStopOptions()` and `workspaceById(id).stopRuntime(confirmation)` use REST even with an ACP transport and never automatically repeat the POST. Session subscribers receive `session_closed` with `reason: "client_close"` and `cause: "workspace_runtime_stop"`; fatal stop-associated exits additionally mark `persistenceUnconfirmed: true`. Updated Web Shell clients retain the saved selection/transcript and wait for explicit Resume. This is a one-time stop, not persistent suspension; a client that missed the event may compete for capacity again.

Runtime status and ensure responses use this shape:

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "state": "active",
  "runtimeLive": true,
  "runtimeEpoch": 4,
  "capabilities": {
    "skills": {
      "state": "ready",
      "revision": 2,
      "runtimeEpoch": 4
    }
  }
}
```

When a workspace runtime has more than one execution engine, `runtimeLive` and `runtimeEpoch` describe the channel that serves workspace control (MCP and Skills): while that channel is live, another engine starting or exiting does not change them. `state` reports that channel as `cold`, `starting` or `stopping` while it is not live; `active` and `idle` count work on every engine.

`capabilities.skills.state` is `not_started`, `starting`, `ready`, `stale`, or `error`; failures include `{code, message}` in `capabilities.skills.error`. A Skills catalog is current only when the top-level and capability `runtimeEpoch` values match. `revision` orders Skills preparation within one runtime epoch and must not be compared across epochs.

`workspace_skills_config_runtime` advertises the split Skills reads and configuration mutations under `/workspace/{config,runtime}/skills` and `/workspaces/:workspace/{config,runtime}/skills`. The Web Shell uses these routes for Skills management and, while composing a new session, to populate slash commands from config immediately before replacing them with a matching-epoch runtime catalog. When the feature is absent, clients must keep using the legacy Skills routes and must not call runtime ensure solely for Skills.

`session_load` and `session_resume` advertise the explicit-restore routes (`POST /session/:id/load` and `POST /session/:id/resume`). Older daemons return `404` for these paths, so SDK clients should pre-flight `caps.features` before calling. `unstable_session_resume` is still advertised as a deprecated alias for compatibility with SDKs that shipped while the underlying ACP method was named `connection.unstable_resumeSession`; new clients should gate on `session_resume`.

`limits.sessionRestoreTimeoutMs`, when present, is the daemon's wall-clock budget for the underlying ACP `loadSession` / `unstable_resumeSession` request. It is an additive v1 field. The TypeScript SDK gives the daemon 10 seconds of client headroom, and the Web Shell watchdog gives it 15 seconds; clients talking to an older daemon should use 70 seconds and 75 seconds respectively.

`session_transcript` advertises `GET /session/:id/transcript`, a read-only paged replay view over the persisted active-session JSONL. It is separate from `/load`: it does not attach a client, seed the live EventBus, create a live session, or change the live replay window. Clients should use it when they need the complete on-disk transcript for a long session, and continue using `/load` only for bounded live replay during cold UI restore.

`workspace_persisted_transcript` advertises `GET /workspaces/:workspace/session/:id/transcript`, a daemon-local persisted-only pager that does not start ACP, query live bridge state, load settings, discover project capabilities, or create the legacy persisted cursor key. The tag is unconditional because trusted single-workspace primaries can use the plural route; per-workspace trust authorization is still evaluated on every request. Registered untrusted secondary workspaces may read, while an untrusted primary remains rejected.

`workspace_session_export` advertises `GET /workspaces/:workspace/session/:id/export`, a trusted-only full export of the selected workspace's active persisted session. It is independent of `session_export` and `workspace_qualified_rest_core`: released daemons can advertise both older tags without implementing the plural route, so clients must pre-flight this tag directly. The tag is unconditional because a trusted single-workspace primary can use the route by id or cwd. The export does not resolve a live owner, start ACP, attach a client, or fall back to another workspace.

`workspace_archived_session_export` advertises `GET /workspaces/:workspace/session/:id/archive/export`, a trusted-only full export from the selected workspace's archived persisted storage. It is independent of `workspace_session_export` and `workspace_qualified_rest_core`; clients must pre-flight this tag directly. A distinct route prevents an older daemon from ignoring archive intent and returning an active transcript with the same id.

`workspace_session_live_state` advertises `GET /workspaces/:workspace/sessions/live-state`, a trusted-only, memory-only snapshot of the selected workspace runtime's live sessions plus an in-memory catalog version that tells clients when a full persisted-catalog reload is warranted. It is independent of `workspace_qualified_rest_core`: released daemons can advertise the broader workspace REST capability without implementing this route, so clients must pre-flight this tag directly. The tag is unconditional because a trusted single-workspace primary can use the route by id or cwd; per-workspace trust checks still apply on every request, and the route does not extend the permissive untrusted-secondary persisted-catalog read policy to live bridge state. The tag means the endpoint exists; it does not promise that every live item carries the optional `updatedAt` activity watermark, which is lifecycle-dependent.

The optional top-level `/capabilities` field `sessionLiveStatePollIntervalMs` advertises the daemon-wide live-state polling interval in milliseconds. It is resolved once from the startup environment variable `QWEN_SESSION_LIVE_STATE_POLL_INTERVAL_MS`, independently of workspace environment overlays. Integer values from `1000` to `2147483647` are accepted; missing or invalid values use `5000`. Web Shell consumes this hint for all workspace live-state polling and also falls back to `5000` for an absent or invalid field from an older or incompatible daemon. This field does not change the route's snapshot semantics, immediate local/visibility refreshes, or full-catalog polling. SDK clients remain responsible for their own timers.

`slow_client_warning` covers SSE backpressure behavior: (a) the daemon emits a `slow_client_warning` synthetic event-stream frame when a subscriber's live frame backlog or live serialized-byte backlog crosses 75% full, once per overflow episode (rearmed after both measurements drain below 37.5%); (b) `GET /session/:id/events` accepts a `?maxQueued=N` query param (range `[16, 2048]`) to pre-size the per-subscriber frame backlog for cold reconnects against a large replay ring. The serialized-byte cap is daemon-owned (default **2 MiB** per subscriber), live-only, and intentionally has no query parameter. The daemon-wide ring size is controlled by `--event-ring-size` (default **8000**, per #3803 §02). Old daemons silently lack the warning/query behavior — pre-flight this tag before opting in.

`typed_event_schema` advertises daemon event payloads that match the SDK's `KnownDaemonEvent` schema. Older daemons may still stream compatible frames, but SDK clients should pre-flight this tag before assuming typed event coverage.

`client_heartbeat` advertises `POST /session/:id/heartbeat`. Older daemons return `404`; pre-flight this tag before issuing periodic heartbeats.

`session_close` and `session_metadata` advertise `DELETE /session/:id` and `PATCH /session/:id/metadata`. Older daemons return `404`; pre-flight these tags before exposing close or rename affordances.

`session_organization` advertises custom session groups and pinning. It adds `GET/POST/PATCH/DELETE /workspace/:id/session-groups`, `PATCH /session/:id/organization`, and the opt-in organized list view `GET /workspace/:id/sessions?view=organized`. When both `session_organization` and `workspace_qualified_rest_core` are advertised, the workspace-qualified organization mutation `PATCH /workspaces/:workspace/session/:id/organization` is also available. The legacy mutation remains primary-workspace-only. Older daemons return `404` for the mutation/group routes and ignore the organized view contract, so WebShell/SDK clients must pre-flight these tags before showing the matching grouping or pinning UI.

`session_archive` advertises the v1 directory-state archive API: `POST /sessions/archive`, `POST /sessions/unarchive`, and `GET /workspace/:id/sessions?archiveState=active|archived`. Archived sessions cannot be loaded or resumed until they are unarchived. `session_storage_conflict_repair` advertises the additive `resolveConflicts` request option and `resolvedConflicts` response bucket described below.

`workspace_qualified_rest_core` advertises plural core REST routes under `/workspaces/:workspace/...`. The selector resolves as exact workspace id first, then as a URL-encoded absolute cwd after canonicalization. Newer single-workspace daemons include the primary runtime in `workspaces[]` even when `multi_workspace_sessions` is absent, allowing clients to discover the id required by workspace-qualified routes; clients should fall back to `capabilities.workspaceCwd` for older daemons that omit the array. Trust status and trust request routes are available for registered untrusted workspaces; file read routes follow the existing filesystem read policy. Registered untrusted secondary workspaces also expose persisted-only session and session-group catalogs: these reads do not attach to a session, start ACP, or merge live bridge state. File writes, catalog mutations, and other plural core routes require a trusted workspace unless a separate capability documents a narrower policy: `workspace_persisted_transcript` (read-only), or the trust grant mutation, which is accepted for a registered untrusted workspace whether or not `workspace_trust_grant` is advertised — that tag reports only whether trust hot-reload is wired, so a recorded decision can reach the running runtime without a daemon restart; it never reports whether the route is served. An untrusted primary continues to receive `403 { code: "untrusted_workspace" }` from the plural catalog and transcript routes; legacy singular primary routes keep their existing compatibility behavior. This tag covers the core file, status, settings, permissions, trust, lifecycle, MCP control, tool and skill toggles, memory, workspace agent CRUD, and session storage surfaces. It does not cover auth, voice, extensions, ACP/WebSocket transport, channel-worker routing, or workspace-qualified session export; pre-flight `workspace_session_export` or `workspace_archived_session_export` separately. Workspace trust is not an ACL: a client holding the daemon token can read every registered workspace surface allowed by this policy.

`workspace_qualified_voice` advertises Voice routes selected by a trusted workspace runtime: `GET` and `POST /workspaces/:workspace/voice`, `POST /workspaces/:workspace/voice/transcribe`, and `WS /workspaces/:workspace/voice/stream`. It is advertised only when multi-workspace runtimes and the shared ACP/Voice WebSocket listener are both enabled. The selector follows the same id-or-encoded-absolute-cwd rules as other plural routes. For REST, an unknown selector returns `400 { code: "workspace_mismatch" }` and an untrusted selector returns `403 { code: "untrusted_workspace" }`; WebSocket upgrade rejection exposes the corresponding HTTP 400/403 status without a structured JSON envelope. Neither transport falls back to primary. Legacy `/workspace/voice`, `/workspace/voice/transcribe`, and `/voice/stream` remain primary-only. Clients use `workspace_qualified_voice` for all qualified Voice modalities and let the selected runtime report configuration-specific errors. The legacy `workspace_voice`, `workspace_voice_transcription`, and `voice_transcribe` tags describe only the primary-bound routes and must not hide a qualified secondary configuration.

`workspace_qualified_memory` advertises the workspace-qualified managed-memory routes: `POST /workspaces/:workspace/memory/{remember,forget,dream}` enqueue tasks and `GET /workspaces/:workspace/memory/{remember,forget,dream}/:taskId` reads them back. It is advertised only when ACP HTTP and multi-workspace runtimes are both enabled. The selector follows the same id-or-encoded-absolute-cwd rules as other plural routes. Each registered workspace gets its own task lane; the primary's qualified lane is the same instance as the singular `/workspace/memory` surface, so a task enqueued on one is readable on the other. Resolution is strictly per selected runtime with no primary fallback: an unknown selector returns `400 { code: "workspace_mismatch" }`, an untrusted selector returns `403 { code: "untrusted_workspace" }`, and an inactive or draining runtime returns `503 { code: "workspace_runtime_unavailable" }`. Reads never allocate a lane, so polling a workspace that has no tasks returns `404 { code: "<kind>_task_not_found" }`. Task ids are scoped to their lane and do not survive a workspace reconfiguration or runtime replacement; a stale id returns `404`, not a data-loss condition. When ACP HTTP is disabled the tag is not advertised and a non-primary qualified request returns a non-retryable `501 { code: "workspace_memory_unavailable" }`, while the primary qualified route keeps working through the locally-owned lane.

`session_lsp` advertises `GET /session/:id/lsp`, the read-only structured LSP status snapshot for daemon clients. Older daemons return `404`; pre-flight this tag before exposing remote LSP status.

`session_resources` advertises `GET /session/:id/resources`, a read-only pair of sanitized Skill and MCP snapshots built from the selected live session's Config. The route is live-session-owner scoped: it never falls back to the primary runtime and does not infer the session's resources from workspace status. The nested `skills` and `mcp` objects reuse the corresponding workspace status payloads, but omit MCP authentication, pool, workspace-budget, and workspace discovery-error enrichments. Status, discovery, and accounting reported by the selected session's MCP manager remain present. Older daemons return `404`; pre-flight this tag before showing a session resource catalog.

`session_status` advertises `GET /session/:id/status`, the live bridge summary for a single session by id. In addition to `clientCount` and `hasActivePrompt`, live sessions expose `isWaitingForPermission`, `isWaitingForUserQuestion`, `pendingInteractionCount`, and a retained `turnError` after a failed turn. The error clears when the next prompt actually starts. A live session that has settled a running turn in the current bridge also carries `updatedAt`, the same activity watermark documented under the live-state route; because this route returns the bridge summary directly, the value is not merged with the persisted transcript mtime and may be earlier than the one a session list reports. Both the single-session status response and workspace session lists include `turnError` and `pendingInteractions`: render-ready permission actions or `ask_user_question` questions plus the `requestId` and selectable options required by the existing permission vote routes. Each user question has an `answerKey`; vote with `answers`, for example `{ "0": "Polling" }`, keyed by that value. Persisted-only sessions omit runtime state because no runtime exists. Older daemons return `404`; pre-flight this tag before polling a single session's status instead of scanning the full session list.

`session_info` advertises `GET /workspace/:id/session-info` and its `/workspaces/:workspace/session-info` twin. The response aggregates persisted active and archived session counts without hydrating list metadata. It is an explicit O(n) disk scan and must not be polled; clients should treat `truncated: true` as a lower-bound result.

`session_approval_mode_control`, `workspace_tool_toggle`, `workspace_skill_settings_toggle`, `workspace_skill_settings_batch_toggle`, `extension_batch_activation_v2`, `workspace_init`, and `workspace_mcp_restart` advertise the mutation control routes documented below. Approval-mode control retains its non-strict compatibility gate. The other controls are strict-gated by operator authority: trusted-loopback primary, bearer-authenticated, or paired Local Control requests pass. A token-less primary request that reaches the strict gate without trusted-loopback authority returns 401 `token_required`; missing or invalid configured credentials and unpaired Local Control credentials are rejected earlier by bearer middleware with plain `401 Unauthorized`. Daemons that lack one of these routes return `404`. The settings-specific Skill tags are different: daemons from the retired-tag generation advertise `workspace_skill_toggle` and `workspace_skill_batch_toggle` and serve their catalog-validated contract at the same paths. The retired single-target route can return HTTP `404 skill_not_found` or `409 skill_not_toggleable`; the retired batch route returns HTTP 200 and places catalog-derived failures in `errors[]`. Pre-flight each tag before exposing its affordance, and do not infer the settings-specific Skill contract by probing route reachability. The route paths and request bodies did not change.

`mcp_guardrails` (issue [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14) covers the MCP budget surface: the `clientCount` / `clientBudget` / `budgetMode` / `budgets[]` fields on `GET /workspace/mcp`, the `disabledReason` field on per-server cells, and the `--mcp-client-budget` / `--mcp-budget-mode` CLI flags. Older daemons omit the new fields entirely; SDK clients pre-flight this tag before relying on `budgets[]` semantics. The registry descriptor also carries `modes: ['warn', 'enforce']` for future feature-modes exposure — for now, clients infer mode from the snapshot's `budgetMode` field. Server refusal under `enforce` mode is deterministic by `Object.entries(mcpServers)` declaration order; a future scope-precedence layer (if qwen-code adopts one) would shift this to "lowest-precedence first" to mirror claude-code's `plugin < user < project < local` convention.

> **Scope is capability-driven.** With `mcp_workspace_pool`, sessions inside one workspace runtime share a transport pool and `WorkspaceMcpBudget`, and the snapshot emits `budgets[0].scope: 'workspace'`. Different workspace runtimes own independent pools. Without the tag, each ACP session uses its legacy `McpClientManager`, the snapshot emits `scope: 'session'`, and N sessions may each consume the configured cap.

`workspace_file_read` covers the text/list/stat/glob workspace file routes
(`GET /file`, `GET /list`, `GET /glob`, `GET /stat`). `workspace_file_bytes`
covers `GET /file/bytes`, which was added later so clients can pre-flight raw
byte-window support against PR19-era daemons. `workspace_file_write` covers
the hash-aware text mutation routes (`POST /file/write`, `POST /file/edit`).
The write tag means the route contract exists; it does not mean the current
deployment is open for anonymous mutation. Write/edit are strict mutation
routes and require operator authority; token-less trusted loopback qualifies.
`workspace_file_upload` covers `POST /file/upload`, the binary ingress route:
an `application/octet-stream` body capped at `MAX_UPLOAD_BYTES` (50 MiB) is
written into the workspace without ever overwriting — an occupied name is
auto-numbered (`name (1).ext`, `name (2).ext`, ...). It is also a strict
mutation route.

When `workspace_qualified_rest_core` is advertised, the same file surface is also available at `/workspaces/:workspace/file`, `/workspaces/:workspace/file/bytes`, `/workspaces/:workspace/stat`, `/workspaces/:workspace/list`, `/workspaces/:workspace/glob`, `/workspaces/:workspace/file/write`, `/workspaces/:workspace/file/edit`, and `/workspaces/:workspace/file/upload`.

The same tag also exposes workspace-qualified project-agent CRUD at `/workspaces/:workspace/agents` and `/workspaces/:workspace/agents/:agentType`. These plural routes only read or mutate project-level agents for the selected workspace; `global` and `user` scope requests return `400 { code: "global_scope_not_supported_for_workspace_route" }`. Workspace-less `/workspace/agents` routes retain their existing primary-workspace behavior and remain the only REST surface for user-level agent scope.

`extension_management_v2` advertises a user-level extension catalog and mutation surface at `/extensions/*`, plus workspace activation projections at `/workspaces/:workspace/extensions/*`. Artifacts are global; workspace routes expose only projection reads, exact activation overrides, and runtime refresh. Reads may target an untrusted registered workspace, while activation, refresh, and workspace-scoped install require a trusted target. Slow mutations use daemon-local operations at `/extensions/operations/:operationId`; store generation, not operation history, is authoritative across restart and across daemons. The published `workspace_extensions` capability and `/workspace/extensions/*` routes remain a primary-workspace compatibility adapter. Clients must preflight `extension_management_v2` and must not infer it from daemon mode or `workspace_qualified_rest_core`.

`extension_git_credentials` advertises authenticated HTTPS Git installs on both `POST /workspace/extensions/install` and `POST /extensions/install`. Clients must preflight this tag before sending URL userinfo or `credentialPersistence`; older daemons reject URL credentials. The tag describes backend protocol support, not the availability of a keychain: stored mode reports the selected backend in the terminal operation result.

`extension_local_path_install` advertises daemon-local Extension sources on both `POST /workspace/extensions/install` and `POST /extensions/install`. The `source` must be an absolute path that exists on the daemon host. Relative paths remain unsupported so daemon process cwd cannot change source identity or shadow a GitHub `owner/repo` shorthand. The existing install operation copies the Extension into managed storage; it does not link the source. Clients must preflight this tag because older daemons reject local sources.

`extension_batch_activation_v2` adds `PUT /extensions/activation` and `PUT /workspaces/:workspace/extensions/activation`. Both accept 1–100 names in `extensionNames`, deduplicate them case-insensitively while preserving first-seen order, persist changed targets in one generation, and return one `202` operation handle. A target does not need to be installed when setting `enabled` or `disabled`: its name creates a desired-state declaration that is preserved when an Extension with that name is installed. The global route accepts `state: "enabled" | "disabled"` and writes V2 `defaultActivation`; the workspace route also accepts `"inherit"` and applies or clears exact overrides for the selected trusted runtime. `inherit` does not declare an unknown name, and an all-unknown clear reports `updated: false`.

`extension_activation_explicit_refresh` means singular and batch activation operations finish after the durable policy commit without directly refreshing active sessions. Callers that need immediate application should wait for activation success and then submit either the synchronous primary-workspace `POST /workspace/extensions/refresh`, which returns refresh counts directly, or the selected workspace's asynchronous `POST /workspaces/:workspace/extensions/refresh`, which returns a separate operation handle. The two forms are not interchangeable: the asynchronous operation records the applied generation when its runtime reconciliation completes, while the synchronous route records nothing, so the generation reconciler still treats that workspace as pending and refreshes its sessions again on its next pass. Callers that can address a specific workspace should prefer the asynchronous form. A refresh failure does not roll back or downgrade the activation result. Daemons without this capability already include runtime refresh in activation, so compatibility clients must not submit a second refresh. The independent 30-second generation reconciler remains enabled and normally applies the committed policy by its next pass; failed reconciliation is retried by later passes.

### Extension Management V2 wire contract

All routes use the daemon bearer authentication rules above. `X-Qwen-Client-Id` is optional for the V2 mutation routes; when supplied, it must identify a client registered with one of the mutation's target workspace runtimes. `:extensionId` is the lowercase 64-hex extension identity. `:workspace` resolves as an exact workspace id first and otherwise as a URL-encoded absolute cwd after canonicalization.

| Method and path                                                    | Success                                                                     |
| ------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| `GET /extensions`                                                  | `200` global artifact catalog                                               |
| `PUT /extensions/activation`                                       | `202` global default-activation batch operation                             |
| `PUT /extensions/:extensionId/activation`                          | `202` global default-activation operation                                   |
| `POST /extensions/install`                                         | `202` install operation                                                     |
| `POST /extensions/check-updates`                                   | `202` update-check operation                                                |
| `POST /extensions/:extensionId/update`                             | `202` update operation                                                      |
| `DELETE /extensions/:extensionId`                                  | `202` uninstall operation, or idempotent `204` when the extension is absent |
| `GET /extensions/operations/:operationId`                          | `200` operation snapshot                                                    |
| `GET /workspaces/:workspace/extensions`                            | `200` workspace activation projection                                       |
| `GET /workspaces/:workspace/extensions/:extensionId/state`         | `200` workspace resource state (`extension_state`)                          |
| `PUT /workspaces/:workspace/extensions/:extensionId/state`         | `202` workspace resource-state operation (`extension_state`)                |
| `PUT /workspaces/:workspace/extensions/activation`                 | `202` exact workspace-activation batch operation                            |
| `PUT /workspaces/:workspace/extensions/:extensionId/activation`    | `202` exact workspace-activation operation                                  |
| `DELETE /workspaces/:workspace/extensions/:extensionId/activation` | `202` clear-override operation                                              |
| `POST /workspaces/:workspace/extensions/refresh`                   | `202` runtime-refresh operation                                             |

#### Workspace resource state

Preflight `extension_state` independently. It supports Skills only; it does not imply MCP resource management. Both routes select the registered workspace runtime and never fall back to primary. GET follows the existing read-only trust rules; PUT requires a trusted workspace.

```json
{
  "skills": [
    { "name": "review", "state": "enabled" },
    { "name": "deploy", "state": "disabled" }
  ]
}
```

PUT accepts 1–100 entries, including a single-element batch. It rejects malformed entries, case-insensitive duplicate names and unsupported groups before queuing. All names must belong to the installed target Extension, including currently disabled Skills; an invalid target fails the operation without partial writes. One locked store commit merges the listed overrides for the exact canonical workspace. Unlisted Skills and other workspaces are preserved. There is no settings write, future-name declaration or implicit activation of the parent Extension.

GET returns `v: 1`, `workspaceId`, `workspaceCwd`, `extensionId`, `name` and `skills`. Each Skill has `name`, `defaultEnabled`, nullable `workspaceEnabled`, `effectiveEnabled`, and optional `disabledReason`/`lockedScope`. The manifest's optional `skillStates` supplies defaults; missing values default to enabled. For an active parent, precedence is settings hard disable, settings explicit enable, settings default disable, workspace internal override, manifest default. Internal disablement uses `disabledReason: "default"` and never locks settings. A disabled or removed parent cannot be revived by a Skill override.

The `set_extension_state` operation returns `status: "updated"` with ordered `result.resourceStates.skills`; `result.states` retains its update-check meaning. Persisted state is not proof that every session refreshed. The daemon refreshes only Skills and their commands/model context for the target runtime, including bootstrap and live sessions, without restarting unrelated MCP/LSP/hooks. Post-commit refresh failures produce warnings without rolling back state. Overrides survive restart and Extension updates and are removed on uninstall. Clients must not fall back to the Skill settings API, which writes a higher-priority setting.

#### Global catalog

The global catalog response is:

```json
{
  "v": 1,
  "generation": 12,
  "extensions": [
    {
      "id": "<64 lowercase hex characters>",
      "name": "demo",
      "version": "1.2.3",
      "installType": "npm",
      "defaultActivation": "enabled",
      "workspaceOverrideCount": 1
    }
  ]
}
```

`installType` is omitted when no install metadata is available. `defaultActivation` is `enabled` or `disabled`. `workspaceOverrideCount` excludes stored `inherit` entries.

The workspace projection response is:

```json
{
  "v": 1,
  "workspaceId": "workspace-id",
  "workspaceCwd": "/absolute/workspace",
  "trusted": true,
  "desiredGeneration": 12,
  "appliedGeneration": 11,
  "extensions": [
    {
      "extensionId": "<64 lowercase hex characters>",
      "name": "demo",
      "version": "1.2.3",
      "defaultActivation": "enabled",
      "workspaceActivation": "disabled",
      "effectiveActivation": "disabled",
      "activationSource": "workspace_override"
    }
  ]
}
```

`workspaceActivation` is `enabled`, `disabled`, or `null` for inheritance. `activationSource` is `default`, `workspace_override`, `legacy_path_rule`, or `cli_override`. `desiredGeneration` is the durable store generation; `appliedGeneration` is the latest generation the controller recorded as applied to that workspace runtime and can temporarily lag.

Install requires explicit consent and an initial activation:

```json
{
  "source": "@scope/demo",
  "consent": true,
  "activation": { "scope": "user" },
  "ref": "optional-git-ref",
  "autoUpdate": true,
  "allowPreRelease": false,
  "registry": "https://registry.npmjs.org"
}
```

For workspace-only initial activation use `{ "scope": "workspace", "workspaceId": "target-workspace-id" }`; the target must exist and be trusted. Daemon installs accept GitHub, Git, and npm sources. When `extension_local_path_install` is advertised, they also accept an absolute path that exists on the daemon host. `ref` does not apply to npm, `ref` and `autoUpdate` do not apply to local sources, and `registry` applies only to npm. `ref`, `autoUpdate`, `allowPreRelease`, and `registry` are optional.

When `extension_git_credentials` is advertised, an HTTPS Git source may include userinfo, for example `https://username:token@git.example.com/org/repository.git`. `credentialPersistence` is valid only with such a source. It is `stored` or `one_time` and defaults to `one_time` when omitted. Stored mode saves the credential through the daemon's hybrid secret storage and keeps only the clean repository URL in install metadata, so the extension remains updatable. One-time mode saves neither the repository URL nor the credential and creates a non-updatable `snapshot`; `autoUpdate: true` is rejected for this mode. Supplying the field without URL credentials, supplying invalid credentials, or using credentials with npm, archive, local, SSH, or non-Git sources returns `400`.

Credentialed install responses and operations expose `credentialPersistence` and may expose `credentialStorage` as `keychain` or `encrypted_file`. One-time operations omit `source`; stored operations may return the clean source. Snapshot catalog/status entries omit source, set `credentialPersistence` to `one_time`, and report `not updatable`. Update fails with `extension_not_updatable`; an unavailable stored secret fails before network access with `extension_credential_unavailable`.

Global and workspace activation `PUT` requests use the same body:

```json
{ "state": "enabled" }
```

`state` is `enabled` or `disabled`. Update, uninstall, check-updates, clear-activation, and refresh requests have no required body.

Batch activation requests use Extension names:

```json
{
  "extensionNames": ["formatter", "review-tools"],
  "state": "disabled"
}
```

The workspace batch also accepts `"state": "inherit"`. Terminal global results contain `name` and `defaultActivation`; workspace results contain `name`, `workspaceActivation` (`null` for inherit), and `effectiveActivation`. Malformed names reject the request; conflicts with existing Store identities fail atomically without a partial commit. An unknown `inherit` target is not persisted, because clearing an override must not manufacture a default-activation declaration or replace later install consent.

Every accepted asynchronous mutation returns:

```http
HTTP/1.1 202 Accepted
Location: /extensions/operations/<operation-id>
Retry-After: 1
Content-Type: application/json

{"accepted":true,"operationId":"<operation-id>"}
```

Workspace-qualified mutations use the same global `/extensions/operations/:operationId` polling path. Operation history is process-local, keeps only a bounded number of terminal entries, and is lost on daemon restart; clients must re-read the catalog or workspace projection and compare generations when an operation id disappears.

An operation snapshot has this shape:

```json
{
  "v": 1,
  "operationId": "<operation-id>",
  "operation": "install",
  "status": "running",
  "phase": "preparing",
  "createdAt": 1750000000000,
  "updatedAt": 1750000000100,
  "source": "owner/repository",
  "name": "demo"
}
```

`status` transitions from `queued` to `running`, then to `succeeded`, `succeeded_with_warnings`, or `failed`. While running, `phase` is `preparing`, `committing`, or `reconciling`. Terminal success may include `result` with `status` equal to `installed`, `enabled`, `disabled`, `updated`, `uninstalled`, `checked`, or `refreshed`; reconciliation results can additionally contain `refreshed`, `failed`, and `error`, while batch activation results contain ordered `results`. Update checks return `result.states`, keyed by extension name, with values such as `checking for updates`, `update available`, `up to date`, `not updatable`, or `error`. Credentials and authorization headers are never operation fields.

A durable commit followed by incomplete cleanup or runtime reconciliation is not reported as a failed mutation. It returns `succeeded_with_warnings` and preserves the committed result:

```json
{
  "v": 1,
  "operationId": "<operation-id>",
  "operation": "uninstall",
  "status": "succeeded_with_warnings",
  "createdAt": 1750000000000,
  "updatedAt": 1750000000200,
  "result": {
    "status": "uninstalled",
    "name": "demo",
    "refreshed": 2,
    "failed": 0
  },
  "warnings": [
    {
      "workspaceId": "workspace-id",
      "workspaceCwd": "/absolute/workspace",
      "code": "reconcile_slow",
      "error": "Runtime reconciliation took 31000ms."
    }
  ]
}
```

Warning `workspaceId` and `code` are optional; `workspaceCwd` and `error` are always present. Clients should display warnings, refresh their catalog/projection, and must not retry the durable mutation blindly.

Validation and authorization failures are synchronous HTTP errors using `{ "error": "...", "code": "..." }` when a stable code exists. Important cases are `400 invalid_extension_id`, `400 invalid_extension_names`, `400 invalid_extension_name`, `400 invalid_extension_activation`, `400 workspace_mismatch`, `403 untrusted_workspace`, `404 extension_operation_not_found`, and `429 extension_queue_full`. Install validation also returns `400` for invalid source/ref/registry options, missing consent, or missing/invalid initial activation. A mutation that fails after `202` is represented, while retained in operation history, with `status: "failed"`, `error`, and an optional stable `code`; common codes include `extension_prepare_timeout` and `extension_conflict`. HTTP `404` for an operation does not imply rollback because operation history is not durable.

`daemon_status` advertises `GET /daemon/status`, the consolidated read-only
operator diagnostic snapshot documented below.

`daemon_update` advertises the process-global `GET /daemon/update`,
`POST /daemon/update/prepare`, and `POST /daemon/update/restart` surface.

**Conditional tags.** These feature tags are advertised only when their deployment toggle, runtime wiring, or availability condition is active. Tag presence means the documented behavior is available; absence means either an older daemon predating the tag or a current daemon where that condition is false. Currently:

<!-- conditional-serve-features:start -->

| Tag                                      | Advertised when …                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agent_collaboration_v1`                 | at least one registered workspace opted into persistent workspace Agent collaboration via `experimental.agentCollaboration` (any scope, workspace included) or `QWEN_CODE_ENABLE_AGENT_COLLABORATION=1`. Present means `/workspaces/:workspace/agent/*` and the Agent Host transport routes are mounted; `workspaces[].agentCollaborationEnabled` identifies each workspace's opt-in state, and clients must only show or call the collaboration surface for enabled entries. Older daemons omit that workspace field, so clients may fall back to this top-level tag for compatibility. Absent means collaboration is not served on this response — the tag is recomputed per request, not snapshotted at boot — so a client must drop the entry point rather than render it and let the calls 404. First enabling it requires a restart; toggling it for a workspace while another already has it on takes effect on the next request. Distinct from the unconditional `workspace_agents` tag, which is subagent-definition CRUD.                            |
| `hosted_harness_private_v1`              | the private Hosted Harness profile is active; only its authenticated session API is available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `managed_session_journal_delta_v1`       | the private Hosted Harness profile is active (same condition as `hosted_harness_private_v1`) and this build can open Managed Session journals containing `message.delta` records. A control plane requires the tag at capability negotiation, so a rolled-back Harness predating those records is refused once there instead of failing every Session open (`managed_session_open_failed`).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `require_auth`                           | the daemon was started with `--require-auth` (or `requireAuth: true` via the embedded API). Bearer token is mandatory on every normal API route, including `/health` on loopback binds; channel webhook ingress keeps its independent shared-secret authentication, and Web Shell document and asset routes remain pre-auth.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `mcp_workspace_pool`                     | the shared MCP transport pool is active. Omitted when `QWEN_SERVE_NO_MCP_POOL=1` disables the pool.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `mcp_pool_restart`                       | the shared MCP transport pool is active; restart responses may include pool-aware multi-entry shapes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `external_tool_guard`                    | `qwen serve` completed the startup handshake for `--external-tool-guard-mode=required`; every spawned ACP channel must acknowledge the installed callback before Session creation, and every supported top-level managed ACP tool invocation that reaches the final execution boundary must receive one external pre-execution allow. Earlier permission/hook denials make no provider request. Nested AgentCore execution is outside v1 and is rejected while this external provider mode is active. The tag reflects only the external provider: independently of it, every daemon applies the built-in Git relocation guard to the managed tools that carry a shell command line (`run_shell_command` and `monitor`), so the absence of this tag does not mean no pre-execution denials.                                                                                                                                                                                                                                                                    |
| `allow_origin`                           | T2.4 ([#4514](<https://github.com/QwenLM/qwen-code/issues/4514>)). The daemon was started with at least one `--allow-origin <pattern>` (or `allowOrigins: [...]` via the embedded API). Cross-origin requests from matched origins receive proper CORS response headers; unmatched origins still get the default 403. The configured pattern list is intentionally NOT echoed in `/capabilities` to avoid leaking the trusted-origin set to unauthenticated readers — a browser client already knows its own origin.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `prompt_absolute_deadline`               | `--prompt-deadline-ms` / `QWEN_SERVE_PROMPT_DEADLINE_MS` / `ServeOptions.promptDeadlineMs` is set to a positive integer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `writer_idle_timeout`                    | `--writer-idle-timeout-ms` / `QWEN_SERVE_WRITER_IDLE_TIMEOUT_MS` / `ServeOptions.writerIdleTimeoutMs` is set to a positive integer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `workspace_settings`                     | the daemon was created with settings persistence available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `user_language_sync`                     | the daemon was created with settings persistence available (same condition as `workspace_settings`), so the sessionless `POST /language` route is registered.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `workspace_voice`                        | settings persistence is available, so the legacy primary workspace Voice settings routes are active.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `workspace_voice_transcription`          | the primary workspace has a configured Voice transcription model.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `session_shell_command`                  | Session shell execution is explicitly enabled and effective under bearer auth or trusted-loopback authority; calls still require a session-bound client id.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `standalone_sessions_v1`                 | the daemon has installed the complete standalone-session runtime, lifecycle coordinator, durable deletion journal, managed-directory implementation, and `/standalone/sessions` route family. Direct embeds without the complete dependency graph omit both the routes and this tag.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `standalone_session_options_v1`          | the complete standalone-session runtime is installed (same condition as `standalone_sessions_v1`), so the read-only, sessionless `GET /standalone/session-options` route is registered on the internal Conversations runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `session_artifacts_persistence`          | session artifact persistence is wired for the runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `session_sources`                        | session source persistence is wired for the runtime. Registers metadata-only workspace files, uploaded attachments, and HTTP(S) links through the live session owner.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `session_generation`                     | session generation helpers are available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `scheduled_task_session_reuse`           | durable scheduled-task session management is active and every managed daemon runtime has installed the callback that lets a task explicitly bind to its current existing session.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `workspace_generation`                   | workspace-scoped generation helpers are available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `rate_limit`                             | `--rate-limit` / `QWEN_SERVE_RATE_LIMIT=1` / `ServeOptions.rateLimit` is enabled.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `workspace_reload`                       | workspace reload support is available in the embedded route configuration.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `workspace_trust_grant`                  | Trust hot-reload is wired (same condition as `workspace_trust_hot_reload`). Both grant routes trust only the bound folder, retaining an existing `TRUST_FOLDER` or `TRUST_PARENT` rule. After persistence, `200` returns v1 status only if host policy and reported status are trusted; otherwise `409 { error, code: "trust_grant_ineffective" }` names the checked state/source in `error`, including policy-load or IDE/file refusal. Trust-file persistence failures return `500 trusted_folders_invalid` for `FatalConfigError`, otherwise `500 internal_error`. Trust-file reads accept the JSONC writer's comments and trailing commas. Routes are served without hot reload too: the daemon's own API grant updates cached v1 status immediately, but startup-bound gates require restart (`requiresDaemonRestartForChanges: true`). External terminal/file edits need a daemon restart to update that cached status.                                                                                                                                  |
| `workspace_trust_hot_reload`             | workspace trust policy monitoring and runtime-generation reconciliation are wired, so trust changes take effect without restarting the daemon and v2 trust status reports convergence.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `channel_reload`                         | a daemon-managed channel worker manager is enabled and can reload its current selection.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `channel_control`                        | daemon-managed channel worker runtime control is wired.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `channel_management`                     | workspace-scoped Channel settings, lifecycle, and pairing management are wired.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `channel_delete_config_loss_convergence` | workspace Channel management is wired (same condition as `channel_management`). `DELETE /workspaces/:workspace/channels/:name` converges a Channel whose configuration is gone from the merged settings view the Worker resolves (system + user + workspace scopes): the runtime must be silent about the Channel or confirm exactly one committed Worker owner in that workspace, which is stopped before the persisted startup selection held in that scope is removed, instead of reporting `channel_instance_not_found`. A Channel whose configuration the merged view still contains but the workspace's resolved channel settings scope does not own — one configured only at user or system scope while the workspace is a project directory — reports `channel_instance_not_found` with the runtime untouched; a Channel the resolved scope does configure is deleted normally, and a workspace whose directory is the user's home directory resolves that scope to the shared user file, so a user-scope Channel is configured — and deleted — there. |
| `multi_workspace_sessions`               | more than one workspace runtime is registered, so session creation can select a trusted runtime by cwd.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `multi_workspace_session_rewind`         | more than one workspace runtime is registered; singular live-session rewind routes resolve the owning runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `multi_workspace_session_shell`          | More than one workspace runtime is registered and session shell execution is explicitly enabled under bearer auth or trusted-loopback authority; singular REST shell resolves the owning runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `dynamic_workspace_registration`         | a workspace runtime factory is wired into the daemon, so an existing trusted directory can be registered as a secondary runtime at runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `persistent_workspace_registration`      | a workspace registration store is wired into the daemon. Production `runQwenServe` supplies the user-level store automatically; direct `createServeApp` embeds must inject one explicitly and own startup restoration of their workspace registry.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `scratch_workspace_registration`         | managed scratch workspace creation is available — a runtime factory, a validated managed scratch root, and runtime disposal are wired, and every managed runtime respects the scratch root boundary.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `workspace_runtime_removal`              | removable dynamic or persistence-restored secondary runtimes can be drained and removed through the management route.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `native_directory_picker`                | the daemon host can open a native OS directory picker (`osascript` on macOS, PowerShell on Windows, `zenity` on a Linux host with a display). Headless hosts omit the tag so clients hide the Browse affordance instead of surfacing a guaranteed picker failure.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `workspace_runtime_stop`                 | the daemon owns ACP process accounting, complete independent-activity and scheduled-task observations, and a runtime bridge supporting confirmed stop and receipt snapshots. Unsupported individual runtimes are disabled in the preview.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `workspace_runtime`                      | every active workspace bridge provides an authoritative runtime lifecycle snapshot; mixed or legacy injected bridges omit the tag.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `workspace_skills_config_runtime`        | every active workspace supports authoritative runtime lifecycle and the split config/runtime Skills routes used by Skills management and the new-session composer.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `workspace_local_open`                   | the daemon host can open a workspace directory in the host's OS file manager (`open` on macOS, `explorer.exe` on Windows, `xdg-open` on a Linux host with a display). Headless hosts omit the tag so clients hide the Open-locally affordance instead of surfacing a guaranteed launch failure. The opened path is always the resolved registered workspace cwd, via `POST /workspaces/:workspace/open`; the route accepts an optional JSON body `{ "target": "terminal" }` (absent/other = folder) and answers `{ kind: 'workspace-local-open', opened: true, target }` with `target` set to `folder` or `terminal`.                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `workspace_local_terminal`               | the daemon host can open a terminal window in a workspace directory (`open -a Terminal` on macOS, `wt.exe` with `cmd.exe` fallback on Windows, `gnome-terminal`/`konsole`/`xterm` on a Linux host with a display). Headless hosts omit the tag so clients hide the Open-in-terminal affordance instead of surfacing a guaranteed launch failure. Served by `POST /workspaces/:workspace/open` with body `{ "target": "terminal" }`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `workspace_qualified_acp`                | ACP HTTP and multi-workspace runtimes are active, so the plural ACP endpoint can select a secondary runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `workspace_qualified_voice`              | multi-workspace runtimes and the shared ACP/Voice WebSocket listener are active, so every workspace-qualified Voice modality is reachable for a secondary runtime.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `workspace_qualified_memory`             | ACP HTTP and multi-workspace runtimes are active, so workspace-qualified managed-memory routes can select a per-workspace task lane for remember, forget, and dream operations.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `client_mcp_over_ws`                     | the daemon accepts client-hosted MCP servers over the ACP WebSocket. This is an explicit opt-in, not required for the CDP tunnel path.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `cdp_tunnel_over_ws`                     | the daemon exposes the reverse `/cdp` WebSocket tunnel, either by explicit opt-in or because a Chrome extension origin is allowed. This only means the tunnel exists; it does not mean Chrome DevTools MCP tools are registered.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `browser_automation_mcp`                 | ACP HTTP is enabled, `cdp_tunnel_over_ws` is active, no bearer token blocks `/cdp`, and `QWEN_CDP_MCP_COMMAND` names an external stdio MCP adapter. The main CLI package does not bundle a browser automation adapter; without this tag, Chrome extension side-panel chat may still work, but console/network/screenshot/click tools are not registered by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `voice_transcribe`                       | the Voice WebSocket endpoint is mounted; a configured Voice model is still required for a successful transcription.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `realtime_voice`                         | the macOS WebShell daemon has Live Voice enabled and the native Host opted in with `QWEN_SERVE_LIVE_NATIVE_HOST=1` (off by default; see below). `/live/status` reports readiness, but the capability is withdrawn until the feature is enabled.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `realtime_voice_web`                     | Live Voice is enabled and the Web Shell page may itself be the audio endpoint over WS `/live/web`, on any platform, and the default endpoint (the native Host is macOS-only and opt-in). The route authenticates like `/voice/stream` (bearer subprotocol), requires a trusted primary workspace, and speaks the Live Host protocol with a reduced hello: microphone permission plus the audio input/output self-checks. A single Host lease remains: a native Host supersedes a browser (`4010`), a browser never displaces a native Host (`4009`), and a second tab takes over only with `?takeover=1`. `/live/status` reports `host.kind: "browser"`; screen capture needs a shared screen (below), and a shortcut change is stored for the next native Host rather than registered.                                                                                                                                                                                                                                                                        |
| `web_terminal`                           | ACP HTTP is enabled, so the authenticated Web Terminal endpoint is available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

<!-- conditional-serve-features:end -->

Live Voice uses the Web Shell endpoint (`realtime_voice_web`, WS `/live/web`) on every platform by default. The native macOS Host is opt-in: only a macOS daemon started with `QWEN_SERVE_LIVE_NATIVE_HOST=1` advertises `realtime_voice`, registers WS `/live/host` and installs the Host when Live is enabled. Without it, `/live/setup` reports `nativeHost: false` with a non-retryable `install` error, and `POST /live/setup/install` and `POST /live/setup/launch` answer `409 live_native_host_unavailable`.

A browser Host answers `host.capture_visual` only while the user is sharing a
screen with the page, which the page reports in its hello as
`selfChecks.screenShare`. Sharing is a separate, explicit act — the model asks
mid-turn, and `getDisplayMedia` needs a user gesture — so the page holds the
stream and takes one frame per request. With nothing shared it answers
`success: false` immediately rather than letting the request time out.

Because a page cannot write to the daemon's filesystem, it sends only the JPEG.
The daemon ignores any `screenshotPath` a browser lease reports and stores the
image itself, under a name it chooses, in the same private capture directory a
native Host writes to. A native Host still persists its own PNG and passes the
path, as before.

`mcp_guardrails` is **not** in this conditional table — it's an always-on tag, advertised whenever the binary supports the new `/workspace/mcp` budget fields, regardless of whether the operator configured a budget. Operators who haven't set `--mcp-client-budget` still get the new fields (with `budgetMode: 'off'`, `budgets: []`).

`mcp_guardrail_events` (issue [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14b) advertises the typed SSE push events that surface MCP budget state crossings without a poll loop. Two frame types arrive on `GET /session/:id/events`:

- `mcp_budget_warning` — fires once on the upward 75% crossing of `reservedSlots.size / clientBudget`. Re-arms only after the ratio drops below 37.5% (`MCP_BUDGET_REARM_FRACTION`). Mirrors PR 10's `slow_client_warning` hysteresis, but at the manager level rather than the per-subscriber backlog level. Payload: `{ liveCount, reservedCount, budget, thresholdRatio: 0.75, mode: 'warn' | 'enforce' }`. Fires under both `warn` and `enforce` modes; never under `off`.
- `mcp_child_refused_batch` — fires at end of each `discoverAllMcpTools*` pass when one or more servers were refused, AND as a length-1 batch on the `readResource` lazy-spawn refusal path. Payload: `{ refusedServers: [{ name, transport, reason: 'budget_exhausted' }, ...], budget, liveCount, reservedCount, mode: 'enforce' }`. `mode` is the literal `'enforce'` because `warn` mode never refuses.

Both events live in the per-session SSE replay ring (they carry an `id`) so a client reconnecting with `Last-Event-ID` resumes through them; the snapshot at `GET /workspace/mcp` is still the source-of-truth for state-after-extended-disconnect. Always-on once advertised — there is no conditional toggle. SDK reducer state (`DaemonSessionViewState`) exposes `mcpBudgetWarningCount`, `lastMcpBudgetWarning`, `mcpChildRefusedBatchCount`, `lastMcpChildRefusedBatch` for adapters that want simple lag-style UI.

## Routes

Clients can feature-detect `session_turn_status` and poll `GET /session/:id/turns/current` or `GET /session/:id/turns/:promptId`. These routes require the live owning Session and never load or scan another workspace. Settled results are best-effort transcript records read from the active branch with a bounded scan; `prompt_not_found` means no result was found in the live queue, 64-entry terminal overlay, or bounded active window. `resultText` is the raw final parent-model answer after the last tool boundary, before optional message rewriting, and may be absent. Results over 32,768 UTF-16 code units include `resultTruncated: true` and `resultCode: "RESULT_TEXT_TRUNCATED"`.

### `GET /health`

Liveness probe. Default form returns `200 {"status":"ok"}` if the listener is up — cheap, no bridge access, suitable for high-frequency k8s/Compose liveness probes.

Pass `?deep=1` (also accepts `?deep=true` or bare `?deep`) for a daemon-wide probe that aggregates bridge **counters** across every managed workspace runtime, including a workspace that is still draining (informational only, not a true liveness check):

```json
{
  "status": "ok",
  "workspaceCount": 2,
  "sessions": 3,
  "pendingPermissions": 1,
  "activePrompts": 1,
  "activeWork": true,
  "activeWorkReporting": "full",
  "activeWorkStaleMs": 4200,
  "connectedClients": 2,
  "channelAlive": true,
  "lastActivityAt": "2026-07-15T08:30:00.000Z",
  "idleSinceMs": 120000
}
```

`sessions`, `pendingPermissions`, and `activePrompts` are sums. `activeWork` is true when any runtime has an accepted but unsettled prompt (including a FIFO-waiting prompt), a running background Agent, a queued/in-progress Agent terminal notification, Session-managed background shell or workflow work, or a child-owned Session turn. The aggregate `session` hold covers goal and cron processing, history mutation, and queued or running Monitor continuations; foreground prompts remain daemon-owned. Running Monitors, follow-up suggestions, and external processes the shell registry can no longer track remain outside the field. It is session-scoped: channel-level work with no session attached yet — a spawn in flight, a pending restore, MCP discovery or authentication — is not counted, so `activeWork` may read false while the daemon still declines to reclaim that channel. Do not read this field as "the daemon is reclaimable"; it describes session-owned work only. `activeWorkReporting` says how much of that boolean is actually vouched for: `full` when every live session is covered by a fresh report from a child that reports all required categories, `none` when no session negotiated reporting, and `partial` for anything between — including a stale snapshot or a negotiated child that omits a required category. A snapshot older than three report intervals stops counting as coverage: it is not a report that the session is idle, so the session goes back to reading as retained, exactly as if the child had never reported. Ordinary automatic cleanup is also disabled for a negotiated-but-incomplete child; a child that does not understand `shell` or `session` cannot safely authorize conditional close according to the complete current predicate. Completely unsupported historical children retain legacy cleanup behavior, and explicit close, kill, shutdown, and channel exit remain force operations. `activeWorkStaleMs` is the age of the oldest snapshot the boolean rests on **among the covered sessions**, and is `0` when no session is covered; it is diagnostic, because freshness is already graded into `activeWorkReporting` by the daemon (only the daemon knows each channel's negotiated cadence). The grade is computed once over every managed runtime rather than per runtime and then combined — a runtime with no sessions is vacuously complete, and treating that as evidence would let an empty workspace vouch for another workspace's unreported sessions. `lastActivityAt` is the latest non-null workspace activity time and `idleSinceMs` is derived from that same snapshot. `channelAlive` means at least one managed workspace channel is live; it does not mean every workspace is healthy. `connectedClients` and the optional `rateLimitHits` remain daemon-wide counters rather than per-workspace sums.

Restart controllers should treat the daemon as busy when:

```ts
const busy =
  health.activePrompts > 0 ||
  health.activeWork ||
  health.activeWorkReporting !== 'full';
```

Dropping the third term makes `activeWork === false` indistinguishable from "no child told me anything", which is the one case where acting on it is unsafe. Unknown responses and failed probes must also prevent restart. `activePrompts` remains an independent compatibility signal.

These fields are an observation cache, not a restart lease: even a fresh, fully-graded, empty answer describes the moment it was sampled, and work can start immediately afterwards. The rule above lowers the risk of a wrong restart substantially but does not eliminate it — strict safety needs a prepare-restart fence that stops new work admission, confirms the drain, and only then shuts down.

> ⚠️ The deep probe is **informational**, not a real liveness verification or an atomic reclaim lease. Negotiated ACP children publish channel-wide active-work snapshots on a negotiated cadence, and the daemon grades their freshness into `activeWorkReporting` — but it never kills a channel over a missing report, because one session's silence is not evidence the process died. Transport liveness and stalled-Agent detection are separate mechanisms. `connectedClients` counts REST SSE connections, not every ACP transport. Use repeated samples and graceful shutdown for idle reclamation; use authenticated `/daemon/status` for transport and per-workspace diagnostics. If any managed runtime getter throws, deep health fails closed with `503 {"status":"degraded","reason":"aggregation_failed"}` rather than returning partial totals, and the daemon log identifies the failing workspace runtime. During bootstrap, before the runtime registry is ready, it returns `503 {"status":"degraded","reason":"bootstrap"}` with `Retry-After: 1`. For listener liveness, use the default `/health` without `?deep`.

**Auth:** required on non-loopback binds and when loopback is hardened with `--require-auth`. On an ordinary loopback bind (`127.0.0.0/8`, `localhost`, `::1`, `[::1]`), `/health` is registered before the bearer middleware so k8s/Compose probes inside the pod don't need to carry the token. On non-loopback (`--hostname 0.0.0.0` etc.) or hardened loopback, the route is registered after the bearer middleware and returns 401 without a valid token — otherwise an unauthenticated caller could probe arbitrary addresses to confirm a `qwen serve` exists, a low-severity info leak that combines poorly with port scanning. CORS deny + Host allowlist still apply on the ordinary-loopback exemption.

### `GET /daemon/update`, `POST /daemon/update/prepare`, and `POST /daemon/update/restart`

These authenticated routes manage the daemon's Qwen Code installation,
independent of the selected workspace. They always use REST, including from SDK
clients with an ACP session transport. The `daemon_update` capability advertises
the protocol; availability is determined by the returned state.

GET checks for releases without downloading or activating them. Successful
checks are cached for 15 minutes and errors for one minute; `?refresh=true`
requests a fresh check. Concurrent checks share one lookup. The response contains
`state` (`available`, `up-to-date`, `installing`, `ready`, `restarting`,
`unavailable`, or `error`), `canInstall`, optional `currentVersion` and
`latestVersion`, and optional `instructions` and `message`. `currentVersion`
identifies the running daemon, including after an update is prepared. Unsupported
installation methods can report `available` with `canInstall: false` and manual
instructions.

Automatic updates require a real CLI lifecycle with POSIX `process.execve`, a
standalone or managed global npm installation, and operator settings that permit
`general.enableAutoUpdate`. Embedded servers, Windows, unsupported Node runtimes,
and development mode report `unavailable`. Only global/system settings apply;
workspace settings cannot enable installation. The connection must use the
primary listener and its daemon runtime token, or the explicitly trusted
loopback deployment without a token. Paired browsers and Local Control connections
report `unavailable`, because their credentials or listener would not survive
restart; their update mutations return `403 update_connection_unsupported`.

Both POST routes require the strict mutation gate's daemon operator authority
and accept no parameters. The server chooses the release, destination, and
launcher; clients cannot supply a version or command.

- `POST /daemon/update/prepare` starts a background download and returns `202`
  with `state: "installing"`. It does not change the active installation. Poll GET
  until the state is `ready`; repeated preparations share the same operation and
  a prepared update returns `200`. With no automatic update available it returns
  `409 update_unavailable`.
- `POST /daemon/update/restart` requires a prepared update, otherwise it returns
  `409 update_not_ready`. It responds with `202` and `state: "restarting"` before
  activating the update, gracefully closing the daemon, and replacing the process
  with the updated launcher. Repeated clicks share one restart. The replacement
  preserves PID, working directory, CLI options, bound port, and effective daemon
  token, clears old version pins, and does not reopen a browser. The client polls
  GET until `currentVersion` changes, then reloads the document at the URL
  captured when the update was requested. Standalone launcher validation runs before closing
  services; a failed check leaves the daemon running. Failures after shutdown
  begins or during process replacement are not guaranteed to recover.

Web Shell automatically checks and prepares available supported updates in the
background, and displays its version-adjacent update button only when ready.
Preparation and readiness survive workspace runtime reloads. Ordinary daemon
shutdown discards the prepared download. Activation failures keep the daemon
running and report `error`; the next check after the one-minute error cache can
prepare a new download for retry. Disabling automatic updates removes readiness
and cleans the download before a restart can begin. Clicking the update button
explicitly restarts the entire daemon and interrupts its active sessions.

### `GET /daemon/status`

Read-only operator diagnostics. Unlike `/health`, this is a normal daemon API:
it is registered after bearer auth and rate limiting, including on loopback
binds. Query parameter:

- `detail=summary` (default) reads only in-memory daemon state.
- `detail=full` also includes live session diagnostics, ACP connection
  diagnostics, auth device-flow counts, and workspace status sections.
- any other `detail` returns `400 { "code": "invalid_detail" }`.

`summary` intentionally does not query workspace status methods, start an ACP
child, or spawn a session. `full` queries each workspace section independently;
a timeout or exception marks only that section as `unavailable` and adds a
`workspace_status_unavailable` issue.

Response shape:

```json
{
  "v": 1,
  "detail": "summary",
  "generatedAt": "2026-06-16T00:00:00.000Z",
  "status": "ok",
  "issues": [],
  "daemon": {
    "pid": 12345,
    "uptimeMs": 3600000,
    "mode": "http-bridge",
    "workspaceCwd": "/repo",
    "qwenCodeVersion": "0.18.1",
    "daemonId": "serve-..."
  },
  "security": {
    "tokenConfigured": true,
    "requireAuth": false,
    "loopbackBind": true,
    "allowOriginConfigured": false,
    "allowOriginMode": "none",
    "sessionShellCommandEnabled": false
  },
  "limits": {
    "maxRegisteredWorkspaces": 256,
    "maxChannelControlWorkspaces": 25,
    "maxSessions": 32,
    "maxTotalSessions": 800,
    "maxPendingPromptsPerSession": 5,
    "listenerMaxConnections": 256,
    "eventRingSize": 8000,
    "compactedReplayMaxBytes": 4194304,
    "promptDeadlineMs": null,
    "writerIdleTimeoutMs": null,
    "channelIdleTimeoutMs": 0,
    "sessionIdleTimeoutMs": 1800000,
    "acpConnectionCap": 64
  },
  "runtime": {
    "sessions": { "active": 0 },
    "permissions": { "pending": 0, "policy": "first-responder" },
    "channel": { "live": false },
    "channelWorker": {
      "enabled": false,
      "state": "disabled",
      "channels": []
    },
    "transport": {
      "restSseActive": 0,
      "acp": {
        "enabled": true,
        "connections": 0,
        "connectionStreams": 0,
        "sessionStreams": 0,
        "sseStreams": 0,
        "wsStreams": 0,
        "pendingClientRequests": 0
      }
    },
    "perf": {
      "eventLoop": { "meanMs": 0, "p50Ms": 0, "p99Ms": 0, "maxMs": 0 },
      "promptQueueWait": {
        "count": 0,
        "meanMs": 0,
        "maxMs": 0,
        "lastMs": null
      },
      "pipe": {
        "inbound": { "count": 0, "totalBytes": 0, "maxBytes": 0 },
        "outbound": { "count": 0, "totalBytes": 0, "maxBytes": 0 }
      }
    },
    "activity": {
      "activePrompts": 0,
      "pendingPrompts": 0,
      "queuedPrompts": 0,
      "lastActivityAt": null,
      "idleSinceMs": null
    }
  }
}
```

Multi-workspace responses also include top-level `workspaces[]` rows with
`{ id, cwd, displayName?, primary, trusted }`. The optional display name is
omitted when unset and remains presentation-only; status consumers must keep
using `id` or `cwd` to correlate runtimes.

`runtime.perf` is optional. When present, it reports daemon-process event loop
lag, prompt FIFO queue wait samples, and daemon-child pipe byte counters only;
ACP child event loop lag is not included in `/daemon/status`.

`status` is `error` if any issue has error severity, `warning` if any issue has
warning severity, otherwise `ok`. Issue codes are stable and include
`session_capacity_high`, `connection_capacity_high`, `pending_permissions`,
`acp_channel_down`, `preflight_error`, `mcp_budget_warning`,
`mcp_budget_exhausted`, `rate_limit_hits`, `channel_worker_exited`,
`channel_worker_partial_connect`, `channel_restore_failed`, and
`workspace_status_unavailable`. During
the short window after the listener is ready but before the full runtime is
mounted, `/daemon/status` may report `daemon_runtime_starting`; if the async
runtime mount fails, it reports `daemon_runtime_failed` while non-status
runtime routes return `503`.

`runtime.activity` reports daemon-wide prompt activity. `activePrompts` counts sessions with an in-flight prompt. `pendingPrompts` counts all accepted prompts that have not settled yet, including the running prompt and FIFO-waiting prompts. `queuedPrompts` counts FIFO-waiting prompts that have been accepted but not dispatched. `lastActivityAt` is the ISO 8601 timestamp of the last prompt start/end or session spawn; `null` when the daemon has never processed any activity since boot. `idleSinceMs` is computed from `lastActivityAt` at response generation time.

`limits.memory` is additive and reports the daemon's resolved memory figures: a required `enforced: false`, a `childHeap` object (`mode`: `off`, `observe`, or `admit`; `admissionEnforced`, true only for managed `admit`; `maxConcurrentChildren` and `perChildCeilingMb`, both `null` under `mode: 'off'`, which models nothing — and `perChildCeilingMb` additionally `null` wherever no partition can be modeled within `modeled.minChildHeapMb` — either the pool cannot cover one child at that floor, or the ceiling would land under it once capped at `modeled.legacyChildCeilingMb`, which is `floor(available / 2)` and so drops under the floor on a host below 1024 MB. It is never 0, and `maxConcurrentChildren` is `0` in those cases, since a host that models no partition is a computed answer rather than an absent model; and `refusals`, the spawns that would have exceeded the modeled limit), `configuredBudgetMb`, `effectiveBudgetMb` (the configured value capped at resolved cgroup/host memory), `budgetSource` (`flag` / `derived`), `availableMemoryMb`, `availableMemorySource` (`constrained` / `host`), `insufficientMemory`, and a `modeled` object holding `rootReserveMb`, `childPoolMb`, `minChildHeapMb`, `maxChildHeapMb`, and `legacyChildCeilingMb` (a conservative model of the ceiling an ACP child receives today, which can sit below the real figure). `runtime.memory` additionally reports `registeredWorkspaces` (the registration count — non-removed workspace entries, including draining, transitioning, or blocked ones; not a live-child count), `activeAcpChildren` (daemon-managed ACP children with a live, non-dying channel — includes transitioning or blocked entries, but excludes a workspace whose kill has started even if the child has not exited; not channel workers, MCP descendants, or unattached spawn reservations), `childRssCoverage` (`active_children` — every ACP child with a live channel, which is the set `activeAcpChildren` counts; older daemons send `primary_only`), a `children` object described below, and a `modeled` object holding `recommendedShareAtRegisteredMb` (`null` when no workspace is registered) and `recommendedShareAtActiveMb` (`null` when no child is active). Each share is capped at the legacy child ceiling, and floored at the minimum child heap only when the ceiling allows — on a small host the ceiling sits below the floor, so share × count can exceed the child pool. Read a share as advisory, not a partition of the pool. Child heap sizing remains advisory: no child spawn argument derives from these values. Opt-in `admit` enforces the modeled concurrent child count, while the default `observe` only records would-be refusals. `runtime.memory.committedAcpChildren` counts managed ACP reservations and attached children, including terminating children until registry release; it is `null` when the managed registry is unavailable. `childHeap` models a fixed partition of `modeled.childPoolMb` — every child would receive the same `perChildCeilingMb`, so the modeled total stays inside the pool rather than accumulating as a per-spawn share would. Read `refusals` as admission pressure only: a count of 0 does **not** mean the partition is safe to apply, because children run on the much larger host-derived ceiling, so a workload needing more old space than `perChildCeilingMb` is healthy here and would only fail once the partition were applied. Two further reasons a nonzero count need not mean capacity pressure: the admission decision counts a terminating child until it exits, so on a daemon already at `maxConcurrentChildren` every channel replacement books a refusal during the overlap window; and on a host too small to model a partition `maxConcurrentChildren` is `0`, so `refusals` equals the total ACP spawn count, with `insufficientMemory` as the field that explains it. In `admit`, a zero-slot model fails startup. A fully managed daemon facing capacity exhaustion may reclaim one least-recently-used empty ACP with no live sessions or pending work, wait for tracked release, then retry admission once before spawning. Loaded sessions remain protected; workspace registration and saved history are retained. The `refusals` counter counts rejected admission attempts, including an initial rejection followed by successful reclamation, not final user-visible failures. Exhausted capacity returns REST HTTP 503 with `code: "acp_child_capacity_exhausted"`, `maxConcurrentChildren`, and `committedAcpChildren` (excluding the rejected reservation), without `Retry-After`. ACP JSON-RPC uses `-32603` with `data.errorKind` carrying that code, `data.httpStatus: 503`, and the same counters. A verified pre-dispatch standalone creation rollback retains `standalone_creation_rolled_back`, its session ID and retryability, and nests the capacity code and counters under `capacity`; uncertain creation outcomes retain their existing recovery semantics. Clients should stop automatic capacity retries and preserve the draft or selected session for manual retry. On the normal `runQwenServe` path the budget is resolved before the bootstrap app is created, so `limits.memory` is already populated during the bootstrap window. It is `null` only on paths that resolve no budget (such as direct-embed bypassing `runQwenServeImpl`). The SDK type allows `null`, so correct clients cope.

`runtime.memory.children` is additive within that block and reports aggregate RSS across the children `childRssCoverage` names: `rssBytes` (their summed self-reported RSS), `sampled` (how many produced a reading), and `oldestReadingAgeMs` (the age of the oldest reading in the sum, so a caller can tell how far apart its parts were taken). The denominator for `sampled` is the sibling `activeAcpChildren`, not repeated inside the block; when `sampled` is lower, `rssBytes` is a floor rather than a total. Sampling is gated on an active SSE/WS watcher, so a status request against a daemon nobody is streaming from reports `sampled: 0` even with live children — `activeAcpChildren` beside it makes that gap visible, and `rssBytes: 0` with `sampled: 0` never means a measured zero. `oldestReadingAgeMs` is `null` when nothing was sampled and also when every contributor is a bridge predating the field, so it never means "fresh". Read the sum as an over-count and an under-count at once: summing per-process RSS double-counts pages the children share, while each child reports only its own process, so its MCP descendants and every channel worker are missing. It is not the daemon tree's memory. The field is optional in the SDK mirror because daemons reporting `primary_only` never send it.

`runtime.memory.children.heap` is additive within that block and reports each ACP child's lifetime V8 old-generation high-water marks, aggregated as a **maximum, not a sum**: `peakOldGenerationBytes`, `peakLiveSetBytes`, `peakTotalHeapBytes`, `majorGcCount`, `majorGcMs`, `unclassifiedSpaceNames`, and `reported`. A heap ceiling applies per child and the peaks were reached at different times, so a total would answer no question; each field is an independent maximum across the reporting children, not a portrait of one child, and a per-child ceiling is judged against each axis on its own. `reported` counts how many of `sampled` contributed, and is lower when some children predate the fields. Every byte figure covers the **old generation** — what `--max-old-space-size` actually bounds — and not `old_space` alone, because a child can exhaust its ceiling with `old_space` at a few megabytes while `large_object_space` holds everything. `peakOldGenerationBytes` is committed bytes and rises with the ceiling the child was given, so read it as an upper bound on what the workload needs rather than as its requirement; `peakLiveSetBytes` is what survives a major GC and does not move with the ceiling, which is what makes it the figure able to say a child cannot fit one; read it as an upper bound rather than an exact live set, because GC entries arrive asynchronously and anything allocated between the collection and the read is counted. `peakLiveSetBytes` is `0` until a major GC is observed, which is an absence rather than a measurement. `unclassifiedSpaceNames` is the union of heap spaces no reporting child could classify; V8 renames and adds spaces between versions, an unknown space is dropped from the sums, and dropping under-counts — so a non-empty array means the byte figures are incomplete and must not be read as a full measurement. The whole object is `null`, never a zeroed object, when no sampled child reported one; with no SSE/WS watcher attached nothing is sampled at all, so that is a routine state rather than an edge case. All of it is observational: nothing here sizes a child, refuses a spawn, or moves `limits.memory.enforced` off `false`.

`runtime.memory.pressure` is additive within that block and reports the daemon root's own memory pressure: `mode` (`off` / `observe`), `level` (`normal` / `soft` / `hard` / `critical`), `source` (`rss` / `heap` / `unknown`), `ratio`, and the six raw figures the ratios come from — `rssBytes`, `rssRatio`, `availableBytes`, `heapUsedBytes`, `heapRatio`, `heapLimitBytes`. `ratio` is the larger of `rssRatio` and `heapRatio`, and `source` names which one it was; ties are reported as `rss`. `availableBytes` is `limits.memory.availableMemoryMb` in bytes — deliberately the detected cgroup/host figure rather than `effectiveBudgetMb`, because what ends the process is the real limit, not an operator's policy number. `source: "unknown"` means neither denominator was measurable and must not be read as healthy; `level` is `normal` in that case only because there is nothing to classify. The figures cover the daemon **root process only**: they are this process's own `memoryUsage()`, so children growing does not move them. `runtime.memory.children` reports those separately, and neither figure is process-tree memory. Both modes report the whole block; only `observe` additionally raises the path-free `daemon_memory_pressure` warning into the status rollup, so `off` leaves the top-level `status` unchanged. Nothing remediates in either mode. The field is optional in the SDK mirror because daemons that shipped `runtime.memory` before it exists send the block without it.

`limits.maxRegisteredWorkspaces` is additive and reports the resolved user registration cap (default 256, configurable from 1 through 256). `limits.maxChannelControlWorkspaces` reports the independent control/recovery owner cap of 25 on the standard daemon, even while channels are disabled. Custom controllers advertise that field only when they enforce it. These fields are present on both bootstrap and ready responses; older daemons may omit them. The channel controller rejects transitional owner unions over its cap with `409 channel_control_workspace_limit_reached` before constructing candidate workers; an initial boot-time union over the cap fails startup before the listener is published, so it has no HTTP surface. Registration capacity does not determine the SDK channel timeout.

`limits.maxTotalSessions` is additive. `null` means the effective daemon-wide fresh-session cap is disabled. When the resolved registration capacity (default 256, configurable through `QWEN_SERVE_MAX_WORKSPACES` or embedded `maxRegisteredWorkspaces`) exceeds 25 and `--max-total-sessions` is omitted, the standard daemon uses a fixed total of 800, even with one startup workspace. At registration capacities of 25 or less, several startup/restored workspaces with a finite `maxSessionsPerWorkspace` derive the effective total once as `maxSessionsPerWorkspace * workspaceCount` over that same startup-plus-restored count; one startup or restored workspace retains an unlimited default. Explicit total limits, including disabled values, take precedence. Later dynamic registration does not recompute the total. Direct `createServeApp` embeds must supply their own shared admission policy. When set, it limits fresh session creation across the daemon and reports total-limit failures with the existing `session_limit_exceeded` error shape plus `scope: "total"`.

`runtime.channel.live` reports the ACP bridge channel inside the daemon. It is
not the channel-adapter worker. Daemon-managed channels use
`runtime.channelWorker`, whose `state` is one of `disabled`, `starting`,
`running`, `exited`, `failed`, or `stopped`. When a worker reaches `running`
and then exits, `/daemon/status` keeps the daemon online and reports warning
issue code `channel_worker_exited`.

Daemon-managed channel worker startup from an explicit `qwen serve --channel
...` remains fail-fast and takes precedence over persisted startup settings.
A flagless boot restores `serve.channels` from every trusted registered
workspace, each contributing the list in its own workspace-scope settings. The
restored names form one selection whose owners are resolved as usual, with the
workspace that listed a name breaking an otherwise ambiguous ownership tie.
That boot attribution lasts as long as the daemon runs: a `PUT
/workspace/channel` re-enabling such a name resolves it the way boot did, even
after the selection was stopped in between. A name a non-primary workspace
contributed is dropped, with a log identifying it, when it cannot be resolved,
so one workspace's stale entry does not strand the others; a name the primary
workspace listed still fails the restore as a whole, whether or not another
workspace lists it too.
`all` remains primary-only: it is ignored, and reported, anywhere else.

A trusted non-primary workspace registered after boot restores its own
`serve.channels` too, loading the channel runtime and reserving the
channel-service lease if nothing else has. That restore:

- happens **once per daemon run** — recorded as soon as the workspace asks for
  anything — so a later removal and re-registration, or a trust
  re-materialization, does not undo an operator who stopped one of those
  channels in between;
- does not happen under an explicit `--channel` selection, which no workspace
  registered later extends;
- does not happen after `DELETE /workspace/channel` stopped hosting, until a
  `PUT /workspace/channel` commits a selection again or the daemon restarts;
- leaves a committed `all` selection as it is;
- adds that workspace's names to the committed selection in one change, one
  late restore at a time. Unlike boot, which drops a name it cannot host and
  keeps the rest, a name that cannot be attributed leaves that workspace's
  whole list unrestored, with the error logged and reported as described
  below.

Registration does not wait on channel control at all. The restore is queued
on the channel-control lane and the response is written without waiting for
it, and so is the reconcile a registration or trust change triggers for
channels the daemon already hosts. A worker that never becomes ready therefore
delays only the channel work queued behind it, never another registration or a
trust reconcile. The flip side is that a `201` from `POST /workspaces` does not
mean that workspace's channels are up, or that an already-hosted channel it
owns has finished restarting; read `GET /workspace/channel` for that.

Without an explicit selection, a boot-time `serve.channels` restore, or a
registration like the one above, channel runtime loading stays lazy.

Stored startup names must be non-empty, have no leading or trailing whitespace,
and contain no unsafe control or invisible characters. Invalid entries are
skipped individually and logged by array index; startup does not trim them into
other instance names or rewrite settings. Worker arguments use
`--channel=<value>`, preserving a leading dash as part of the name.

An invalid startup field or a validation or lease error before workers start
skips the automatic restore, with a log identifying `serve.channels`, while
unrelated settings remain in effect. A failed worker startup allows the daemon
to continue after cleanup succeeds. Global runtime startup timeouts and
unconfirmed worker stops follow the existing startup-failure path; the lease
remains held while worker termination is unconfirmed. Channel management
reports persisted startup settings and actual runtime state.

Some `serve.channels` names the daemon did not bring up are reported rather
than only logged. Such a name never joins the committed selection, so no
worker snapshot carries it. Three cases are reported:

- a configured startup selection whose worker failed to start, on the boot
  path that keeps the daemon serving without its channels;
- a name the boot ownership resolver dropped while attributing it to a
  workspace, reported against every workspace that listed it;
- every name still unhosted after a late registration's restore failed.

They surface in two places:

- `/daemon/status` reports one `channel_restore_failed` warning per workspace,
  naming each channel with its error. This is the complete surface, and it is
  present on the bootstrap response as well, which is the only one that exists
  while boot records are being written. At most 64 channels are named per
  workspace, each name bounded to 128 characters, with `and <n> more` when the
  list is longer;
- that workspace's `GET /workspaces/:workspace/channels` lists the channel with
  `runtime: { state: "error", lastError }` instead of `stopped`, where
  `lastError` is the adapter's own startup error when the worker reported one
  for that channel, otherwise the attempt's error. This surface is built from
  the workspace's own channel settings scope, so it can only carry a name that
  scope defines — a name no registered workspace configures, or one whose
  config lives in another workspace, has no row here and is reported on
  `/daemon/status` alone.

Everything else that leaves a `serve.channels` name unhosted is diagnosed in
the daemon log only: a name lost because the whole list was unreadable or
invalid, a non-primary workspace's list discarded because the primary selected
`all`, a late restore skipped because the committed selection is `all`, and a
remote (SSH) workspace's list. Inspect daemon logs for those.

A report lasts until the channel is hosted — by this workspace or by a later
restore from another one — until its workspace is no longer registered, or
until an operator acts: `POST` start, stop or restart on the channel,
`PUT`/`DELETE /workspace/channels/:name`, or a `PUT`/`DELETE
/workspace/channel` that changes the selection. `PUT
/workspace/channels/:name/startup` does not clear it: it rewrites
`serve.channels` without saying anything about the attempt that failed. The
report is kept in memory: a restarted daemon restores again and reports what
that attempt did. The error text is credential-redacted and bounded the way
the daemon log renders it.

After a worker has reached ready, unexpected exits are restarted by the serve
supervisor within a bounded policy: up to 3 restart attempts in a 5 minute
window, with 1s, 5s, then 15s backoff. The worker sends IPC heartbeats every
15s; if no heartbeat is observed for 45s, the supervisor treats the worker as
stale, kills it, records `staleHeartbeatAt`, and uses the same restart path.

`runtime.channelWorker` may include additive operational fields:
`requestedChannels`, `pid`, `startedAt`, `exitCode`, `signal`, `error`,
`restartCount`, `lastExitAt`, `lastRestartAt`, `nextRestartAt`,
`lastHeartbeatAt`, `staleHeartbeatAt`, `startupFailures`, and
`startupFailuresTruncated`. Each startup failure has `channel`, `phase`
(currently `connect`), optional adapter-provided `code`, and a credential-
redacted `message`. At most 64 failures are retained for the current worker
generation; the truncation flag means more failures were observed. `code` is
diagnostic and is not a stable cross-adapter classification. `restartCount` is the lifetime
number of restart attempts made by this serve process; a running worker with
`restartCount > 0` is healthy unless another issue applies. A running worker
whose `requestedChannels` include names missing from `channels` reports
`channel_worker_partial_connect`.

On a multi-workspace daemon (`--workspace` repeated), `runtime` additionally
includes `channelWorkers[]` — one entry per owning workspace, each a
`channelWorker` snapshot annotated with `workspaceId`, `workspaceCwd`, and
`primary`. `channelWorker` stays populated as the primary workspace's snapshot
for compatibility. Single-workspace daemons omit `channelWorkers[]`.

### Daemon-managed channel control

The `channel_control` capability advertises the runtime selection resource.
The resource is daemon-wide even though its compatibility path uses the
singular `/workspace` prefix. Runtime selections are not persisted and do not
modify the daemon's boot-time `--channel` option.

`GET /workspace/channel` returns an immutable manager snapshot:

```json
{
  "enabled": true,
  "selection": { "mode": "names", "names": ["telegram", "feishu"] },
  "pendingSelection": { "mode": "names", "names": ["telegram"] },
  "transition": "reconciling",
  "workers": [
    {
      "workspaceId": "primary-id",
      "workspaceCwd": "/work/primary",
      "primary": true,
      "enabled": true,
      "state": "running",
      "channels": ["telegram"],
      "pid": 1234
    }
  ]
}
```

`selection` is `null` while disabled. `pendingSelection` is present only during
a mutation. `transition` is one of `idle`, `starting`, `reconciling`,
`stopping`, or `rolling_back`.

`PUT /workspace/channel` is strict-gated and accepts exactly one selection:

```json
{ "selection": { "mode": "all" } }
```

```json
{ "selection": { "mode": "names", "names": ["telegram", "feishu"] } }
```

Names are trimmed and deduplicated without sorting; an empty names array is
invalid. `all` remains primary-workspace-only. A disabled-to-enabled change
returns `201`; an idempotent PUT or replacement returns `200`. The response is
`{ changed, replaced, partial, state }`. An equal selection keeps healthy
workers in place, but recovers an equal selection whose worker is stopped or
failed.

`DELETE /workspace/channel` is strict-gated and idempotent. It returns
`{ changed, state }`; a successful state is disabled. `POST
/workspace/channel/reload` is also strict-gated and re-reads settings,
re-resolves workspace groups, and force-reconciles the committed selection.
It returns `409 channel_worker_not_enabled` while disabled. The
`channel_reload` capability is advertised dynamically only while the manager
has a committed, reloadable selection.

Every enable, replace, reload, stop, and daemon shutdown enters one FIFO
lifecycle lane. GET does not wait for that lane. Workspace groups whose ordered
selection did not change remain online. Replacement failures attempt to stop
newly started workers and restore the previous committed selection. Clients
must inspect `rolledBack`, `rollbackError`, and `state` because cleanup or
restoration can also fail. The daemon keeps the channel-service PID lease
throughout a transaction and does not release it until every relevant child
exit is confirmed.

Stable control errors are:

- `400 invalid_channel_selection`, `channel_workspace_mismatch`, or `ambiguous_channel_workspace`
- `403 untrusted_workspace`
- `409 channel_service_conflict`, `channel_worker_not_enabled`, or `channel_control_workspace_limit_reached`
- `500 channel_worker_stop_failed`
- `502 channel_worker_start_failed`, with `rolledBack` and an optional credential-redacted `rollbackError`
- `503 daemon_draining`

Strict writes from a token-less primary request that reaches the gate without
trusted-loopback authority return `401 token_required` before control code
runs. Missing or invalid configured credentials and unpaired Local Control
credentials are rejected earlier with plain `401 Unauthorized`.
Trusted-loopback primary requests execute normally.
Once a request begins, disconnecting
the HTTP client does not cancel the lifecycle transaction; clients may retry
the same PUT safely.

For `502 channel_worker_start_failed`, the response may also include
`startupFailures[]` and `startupFailuresTruncated`. Each failure adds the
trusted `workspaceCwd` of the attempted worker. These fields describe the
failed transaction, while `state` describes the current state after rollback;
a later GET does not retain the failed attempt. A partially connected worker
instead returns success and exposes its failures in the worker snapshot. An
explicit `--channel` boot with no connected adapter fails startup. A settings-
derived startup failure is logged and allows the daemon to continue after
cleanup succeeds, subject to the global runtime startup timeout. Failure to
confirm cleanup retains the normal startup-failure behavior and service lease.

`qwen channel status` without `--daemon-url` continues to read pidfile metadata;
with `--daemon-url` it reads `GET /workspace/channel`. During a restart
window the serve-owned pidfile remains reserved, but `workerPid` is omitted so
clients do not display a stale worker process. On a multi-workspace daemon the
pidfile also carries an additive `workers[]` array (per-workspace
`workspaceId` / `workspaceCwd` / `channels` / live `workerPid`) while the
top-level `channels` (union) and `workerPid` (primary) stay populated for older
readers; single-workspace daemons keep the original single-worker shape. Worker
stdout/stderr are forwarded into the daemon log with bearer tokens, sensitive
worker environment values, and proxy URL credentials redacted.

### Workspace Channel management

The `channel_management` capability advertises workspace-scoped Channel
configuration and runtime management. The singular `/workspace` routes target
the primary runtime. `/workspaces/:workspace` resolves the exact registered,
trusted runtime and never falls back to the primary runtime.

Read-only discovery uses:

- `GET /workspace/channel-types`
- `GET /workspace/channels`
- `GET /workspaces/:workspace/channel-types`
- `GET /workspaces/:workspace/channels`

The catalog marks the types supported by this management API with
`manageable: true`. Instance snapshots include a revision, redacted secret
presence metadata, startup state, and runtime state; literal secrets are never
returned. Channel snapshots use `Cache-Control: no-store`.

Field descriptors can expose nested object metadata through `properties`.
Numeric descriptors can use `exclusiveMinimum` for open lower bounds. String
and secret descriptors can use `multiline` to ask clients for a multi-line text
area; the descriptor types allow it only on top-level fields. Clients that do
not render an advertised field kind must preserve its existing config value
instead of coercing or deleting it, and a client that renders a `multiline`
field in a single-line control must preserve the stored value verbatim instead
of writing back its newline-stripped input value. Object fields cannot be
required, and nested properties cannot be secrets or environment-resolvable
fields; those management protocols remain top-level only. A nested `required`
property is enforced only while its parent object is present in the write;
omitting the parent object leaves its nested requirements unchecked. Writes
replace each field's stored value wholesale, so preserving an object means
resending the stored object; the daemon does not merge partial objects.

Configuration writes use optimistic concurrency and the strict operator-authority
gate:

- `PUT /workspace/channels/:name`
- `DELETE /workspace/channels/:name`
- `PUT /workspace/channels/:name/startup`
- the equivalent `/workspaces/:workspace/...` routes

Each settings mutation includes `expectedRevision`. Upsert requests contain a
`config` object and may contain explicit secret operations: `preserve`,
`replace`, or `clear`. A Channel config cannot select a working directory
outside the resolved workspace.

Runtime actions are strict-gated `POST` requests to
`.../channels/:name/start`, `stop`, or `restart`. They operate only on the
worker owned by the resolved workspace.

Pairing management is available only for instances configured with the
`pairing` sender policy or group policy:

- `GET .../channels/:name/pairing-requests`
- `POST .../channels/:name/pairing-requests/approve` with `{ "code": "..." }`
- `GET .../channels/:name/pairing-approvals`
- `DELETE .../channels/:name/pairing-approvals` with
  either `{ "senderId": "..." }` or `{ "groupId": "..." }`

All pairing routes require strict operator authority and use `Cache-Control: no-store`.
Requests, approvals, and revocations are scoped to the selected Channel
instance and workspace. Pending requests include a typed user or group subject;
group requests also retain the sender who initiated the request. Approval
snapshots contain `senderIds` and `groupIds` because allowlists do not persist
display names. Revoking an unknown user or group returns
`404 channel_pairing_approval_not_found`.

### Channel delivery and Notify

`channel_delivery` advertises immediate, best-effort delivery support. It is a
protocol capability, not a worker health signal. Delivery never starts a
missing worker, falls back to another workspace, retries, persists an outbox,
or replays historical notifications.

Direct Notify bypasses Agent and Session and waits for one send attempt:

```http
POST /workspace/notify
POST /workspaces/:workspace/notify
Authorization: Bearer <token>
Content-Type: application/json

{
  "text": "service unavailable",
  "delivery": {
    "kind": "channel",
    "target": {
      "channelName": "dingtalk",
      "type": "user",
      "id": "platform-user-id"
    }
  }
}
```

Both routes use the strict mutation gate. The qualified route resolves only a
registered, trusted workspace. Success is `200 {delivered:true,deliveryId}`.
`delivered:true` means the Channel send Promise resolved; it does not prove
provider acceptance, user receipt, or a read receipt. Provider-specific
response validation and consistent error-reason semantics across IM adapters
are outside this V1 contract.
Errors are `400 channel_delivery_invalid`, `503 channel_worker_unavailable` or
`channel_delivery_queue_full`, `504 channel_delivery_timeout`, and `502
channel_delivery_rejected` or `channel_delivery_failed`. A timeout has an
unknown outcome and is not retried.
There is intentionally no separate connectivity-test endpoint: a normal
Notify call is the end-to-end test.

The replayable result event contains only correlation and sanitized status:

```json
{
  "type": "channel_delivery_result",
  "promptId": "prompt-1",
  "data": {
    "sessionId": "session-1",
    "deliveryId": "prompt-1",
    "source": "prompt",
    "status": "failed",
    "promptId": "prompt-1",
    "code": "channel_worker_unavailable",
    "error": "Channel worker is not running."
  }
}
```

An empty successful Prompt final omits error fields:

```json
{
  "type": "channel_delivery_result",
  "promptId": "prompt-1",
  "data": {
    "sessionId": "session-1",
    "deliveryId": "prompt-1",
    "source": "prompt",
    "status": "skipped",
    "promptId": "prompt-1"
  }
}
```

`source` is `prompt` or `scheduled`; `status` is `delivered`, `failed`, or
`skipped`. `skipped` means the eligible turn completed successfully but its
last tool-free assistant response block was empty or whitespace-only. The
daemon consumes the delivery authorization and publishes the event without
resolving a Channel Worker. Scheduled correlation uses `taskId` and `firedAt`.
The event never contains target IDs, message text, credentials, or webhook
secrets.

Security: the response never includes bearer tokens, client ids, full ACP
connection ids, device-flow user codes, or verification URLs. Both detail
levels may include additive `daemon.runId`, `daemon.logMode`, and
`daemon.logHealth`. `summary` omits the daemon log path and loss details;
`full` may include `logPath`, `logIssues`, `logDroppedRecords`, and
`logDroppedBytes` for authenticated operators. Degraded file logging adds the
path-free `daemon_log_degraded` warning to the normal status rollup.

### `GET /capabilities`

```json
{
  "v": 1,
  "protocolVersions": {
    "current": "v1",
    "supported": ["v1"]
  },
  "mode": "http-bridge",
  "features": [
    "health",
    "daemon_status",
    "capabilities",
    "multi_workspace_sessions",
    "..."
  ],
  "limits": {
    "maxRegisteredWorkspaces": 256,
    "maxChannelControlWorkspaces": 25,
    "maxPendingPromptsPerSession": 5,
    "maxSessionsPerWorkspace": 32,
    "maxTotalSessions": 800,
    "sessionRestoreTimeoutMs": 60000
  },
  "modelServices": [],
  "workspaceCwd": "/canonical/path/to/primary-workspace",
  "workspaces": [
    {
      "id": "stable-workspace-id",
      "cwd": "/canonical/path/to/primary-workspace",
      "primary": true,
      "trusted": true
    },
    {
      "id": "stable-secondary-workspace-id",
      "cwd": "/canonical/path/to/secondary-workspace",
      "displayName": "Payments Production",
      "primary": false,
      "trusted": true
    }
  ]
}
```

Stable contract: when `v` increments the frame layout has changed in a backwards-incompatible way.

> **`protocolVersions`** describes the serve protocol versions the daemon can speak. `current` is the daemon's preferred protocol version and `supported` is the compatible set. Clients that require a specific protocol should check `supported`; feature-specific UI should still gate on `features`. Additive to v=1: older v=1 daemons omit this field, so SDK clients that target older builds should treat it as optional.

> **`modelServices` is always `[]` in Stage 1.** The agent uses its single default model service and doesn't enumerate it over the wire. Stage 2 will populate this from registered model adapters so SDK clients can build service-pickers; until then, do NOT rely on this field being non-empty.

> **`workspaceCwd`** is the canonical absolute path for the daemon's primary workspace. Use it to omit `cwd` on `POST /session` (the route falls back to this primary path) and to keep old single-workspace clients compatible. Additive to v=1: pre-§02 v=1 daemons omit the field — clients that target older builds should null-check before consuming it.

> **`workspaces[]`** lists every registered runtime. Newer single-workspace daemons include the primary runtime even when `multi_workspace_sessions` is absent so clients can discover the stable id required by workspace-qualified routes; older daemons may omit the array. Each entry is `{ id, cwd, displayName?, primary, trusted, removable? }`. `displayName` is presentation-only and omitted when unset. The first/primary workspace remains mirrored by `workspaceCwd`; new clients choose a non-primary runtime by passing that entry's `cwd` to `POST /session`. Untrusted workspaces are advertised for diagnostics but reject fresh session creation with `403 untrusted_workspace` until trust changes. `removable` is present on daemons that support runtime removal and is true only for process-dynamic or persistence-restored secondary runtimes.

> **`session_worktree_persistence_v1`** means the daemon can persist and verify Part 4A worktree ownership. Successful worktree creation responses, and restore responses whose child is either relocated while idle or already reports the verified worktree cwd, carry `worktree` metadata plus `worktreeState: "persisted-v1"`. A legacy best-effort restore, or a cold restore whose restore-prompt was fired rather than parked (`suppressWorktreeContextRestore` off, so the route asked the bridge for no deferral) and which therefore reports an active prompt with no current cwd, may still return `worktree` without that attestation. Clients requesting isolation must pre-flight this tag and verify each response; the `worktree` object alone is not durable-ownership proof.

> **`session_worktree_reset_v1`** means the daemon supports worktree ownership transfer: `POST /session/:id/worktree-reset` moves a persisted worktree session's checkout ownership to a fresh replacement session. Restore responses gain three typed 409 classifications alongside it: `worktree_session_superseded` (the session's sidecar carries a `supersededBy` link — the classification is decided from that link alone, before any marker read, and the link is written before the marker flips, so the body's `replacementSessionId` is a redirect to verify rather than proof of ownership: in a pre-commit interrupted state it names a session that is not the marker owner, cannot itself be restored, and is reaped by the retried reset), `worktree_marker_missing` (the checkout marker is absent; reset the task to recreate it — a restore retry cannot, because no restore path writes a marker), and `worktree_reset_interrupted` (a previous transfer crashed mid-flight with its sidecar links agreeing; retry the reset). The interrupted classification is checked first: a missing marker whose `supersedes`/`supersededBy` links agree surfaces as `worktree_reset_interrupted`, never as `worktree_marker_missing`. See the route section below for the transfer protocol and failure taxonomy.

The workspace feature tags and `workspaces[]` are dynamic. Clients that add a workspace must fetch `/capabilities` again after the mutation completes; the daemon does not broadcast capability changes to clients that cached an earlier response. Forgetting persistence does not unload an active runtime, so that runtime remains advertised until restart.

### `GET /brand`

The Web Shell's product branding, for hosts that want to white-label the shell. Resolved from `ui.brand` in the operator settings scopes. Gated by the `web_shell_brand` feature tag; a daemon without the route answers 404.

```json
{
  "name": "QiuQiu Code",
  "logoDataUri": "data:image/svg+xml,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%2F%3E"
}
```

> **Both fields are optional, and `{}` is a normal response.** An absent field means "use the client's built-in brand" — the Web Shell renders its own name and inline logo. Clients must not treat an empty body as an error.

> **The handler always answers 200, even when the configured logo was rejected.** A missing file, a symlink, a hard-linked file, a directory, a non-SVG document, or content over 32 KiB yields a body with no `logoDataUri`, and the reason is written to the daemon's stderr as `qwen serve: GET /brand: ui.brand.logoPath …`. A client therefore cannot distinguish "no brand configured" from "the operator's logo was refused"; the operator-facing channel is the daemon log. One advisory is softer than a rejection, and there are two: a root `<svg>` with no usable `viewBox` and no positive, non-percentage `width`/`height` (a malformed or zero-area viewBox counts as unusable), which the browser may render blank at the sidebar's fixed size; and a prefix-bound root whose unprefixed elements lack a default-namespace binding, which renders them invisible. Middleware in front of the handler answers before it ever runs — `401` when bearer auth is required and absent, `429` when the optional rate limiter is engaged. A draining daemon does not reject this route: the rate limiter is permissive while draining, so the handler keeps answering 200 until the listener closes and the client sees a connection failure instead. Startup is not a 503 on this route: on the default deferred-runtime path the request is _held_ until the runtime is ready and then answered 200, so a short client timeout is what can fire early. A 503 with `code: "daemon_runtime_starting"` reaches this route only on configurations that answer before the runtime is ready (e.g. `--open`) and is retryable; a 503 with `code: "daemon_runtime_failed"` is terminal until the daemon restarts and carries no `Retry-After` — do not retry it.

> **Workspace settings never contribute.** The route loads settings with `skipWorkspaceSettings`, so a repository's `.qwen/settings.json` cannot rename the product or name a file for the daemon to read and inline into every connected browser. Only System Defaults, User and System are read, in that precedence. For the same reason, a brand value is refused with a warning on the daemon's stderr whenever placeholder substitution would change it: substitution draws from the process-wide environment, which a workspace's `.qwen/.env` or `env` block populates first at boot. An unresolvable placeholder (the variable is unset) is kept verbatim, so a typo'd variable shows as literal text rather than silently falling back.

> **`logoDataUri` must be rendered as an image, never injected as markup.** The daemon does not sanitize the SVG it read. SVG loaded through an `img` src or a favicon href cannot execute script; SVG injected into the document can. The Web Shell only ever assigns it to an image context, and that invariant is what makes the absence of a sanitizer safe.

> **Process-global.** The route takes no workspace selector and no session id: the value derives from user-global configuration, so it is the same for every workspace the daemon serves. It is registered after `bearerAuth` and the rate limiter, and before the Web Shell SPA fallback, so it answers JSON for any `Accept` header.

### `POST /workspaces`

Register an additional workspace runtime. The path must be an existing, accessible, absolute directory that does not duplicate or nest with another registered workspace. Registration is process-local unless the client sends `persist: true`; clients must pre-flight `persistent_workspace_registration` before requesting persistence. When `workspace_display_name` is advertised, the request may also include an optional `displayName`.

```json
{
  "cwd": "/canonical/path/to/secondary-workspace",
  "persist": true,
  "displayName": "Payments Production"
}
```

A newly created runtime returns `201`; promoting an already-active secondary workspace to persistent returns `200`. Persistent success includes `persisted: true`:

```json
{
  "id": "stable-workspace-id",
  "cwd": "/canonical/path/to/secondary-workspace",
  "displayName": "Payments Production",
  "primary": false,
  "trusted": true,
  "persisted": true
}
```

`displayName` must be a string no longer than 256 characters after surrounding whitespace is trimmed. An empty result is treated as no name, and internal C0 (`U+0000`–`U+001F`) or DEL (`U+007F`) control characters are rejected. JSON `null` is not a creation value and returns `400 invalid_display_name`; omit the field to supply no initial name. Duplicate display names are allowed. A name supplied with a process-local registration lasts only for that daemon process; `persist: true` stores it with the persistent registration so it can be restored after restart. Repeating the request for an already-persistent workspace is idempotent and does not rename it.

Errors include `400 invalid_path` / `invalid_persist_flag` / `invalid_persist_target` / `invalid_display_name`, `409 workspace_exists` / `workspace_nested` / `workspace_limit_reached` / `workspace_registration_store_too_large`, `500 workspace_registration_store_error` / `runtime_creation_failed`, and `501 persistence_not_available` / `not_implemented`.

### `PATCH /workspaces/:workspace`

Update an active workspace resource selected by workspace ID or URL-encoded absolute cwd. The endpoint currently supports only display-name metadata:

```json
{ "displayName": "Payments Production" }
```

Send `{ "displayName": null }` to clear the name. Here `null` is an update-only deletion sentinel; non-null values follow the same string normalization rules as `POST /workspaces`. The response is the updated `{ id, cwd, displayName?, primary, trusted, removable? }` workspace projection. Runtime metadata is always updated. If the runtime has matching persistent registration identities, every alias is updated atomically through the existing schema-v1 registration store; the endpoint never creates or promotes a persistent registration.

Unsupported fields fail closed rather than being silently ignored. Errors include `400 empty_patch` / `invalid_display_name` / `unsupported_field` / `workspace_mismatch`, `409 workspace_registration_in_progress`, `500 workspace_registration_store_error`, and `503 daemon_shutting_down`.

### `DELETE /workspaces/:workspace`

Remove one removable secondary runtime. The selector follows the plural workspace routing rules and accepts either a workspace ID or a URL-encoded absolute cwd. The optional JSON body is `{ "force": boolean }`; omitting it requests non-force removal.

Non-force removal returns `409 workspace_busy` with an `activity` snapshot when the frozen runtime has sessions, prompts, pending starts, ACP connections, memory tasks, or workspace channel workers. Sending `{ "force": true }` requests termination of those resources. Persistence removal is the commit point: subsequent cleanup is bounded and best-effort, cleanup failures are logged, and logical removal still converges instead of restoring the runtime. A successful response is:

```json
{
  "removed": true,
  "workspaceId": "stable-workspace-id",
  "workspaceCwd": "/canonical/path/to/secondary-workspace",
  "forced": true,
  "persistedRegistrationRemoved": true,
  "activity": {
    "sessions": 2,
    "activePrompts": 1,
    "pendingSessionStarts": 0,
    "acpConnections": 1,
    "memoryTasks": 0,
    "channelWorkers": 0,
    "voiceSessions": 0
  }
}
```

An immediately busy non-force request returns a fast pre-drain activity snapshot. Once drain starts, the busy or success response contains the final snapshot taken after admission and ACP drain gates close and before cleanup begins. Errors include `400 invalid_force_flag` / `workspace_mismatch`, `409 workspace_busy` / `primary_workspace_removal_forbidden` / `static_workspace_removal_forbidden` / `workspace_removal_in_progress` / `workspace_registration_in_progress`, `500 workspace_persist_failed` / `workspace_runtime_removal_failed`, `501 workspace_runtime_removal_unsupported`, and `503 daemon_shutting_down`.

### `GET /workspace-registrations`

List the persisted desired workspace set for this primary workspace. Entries remain visible with `active: false` when a stored directory could not be restored during the current start.
An entry remains `active: true` while its runtime is draining because the runtime still owns live resources until removal completes.
Entries include optional `displayName` when the persistent registration has one.

```json
{
  "schemaVersion": 1,
  "primaryWorkspace": "/canonical/path/to/primary-workspace",
  "entries": [
    {
      "id": "stable-registration-id",
      "cwd": "/canonical/path/to/secondary-workspace",
      "displayName": "Payments Production",
      "active": true,
      "persisted": true
    }
  ]
}
```

Returns `501 persistence_not_available` when no registration store is configured and `500 workspace_registration_store_error` when the store cannot be read.

### `DELETE /workspace-registrations/:id`

Forget one persisted registration. This does not unload an active runtime or terminate its sessions; `restartRequired: true` means the active runtime disappears on the next daemon restart.

```json
{ "removed": true, "active": true, "restartRequired": true }
```

Returns `404 workspace_registration_not_found`, `500 workspace_registration_store_error`, or `501 persistence_not_available`. Like other mutation routes, this endpoint requires mutation authentication when daemon authentication is enabled.

### Read-only runtime status routes

These routes report daemon-side runtime snapshots. They are additive v1 routes,
do not mutate state, and do not change the serve protocol version. Workspace
status routes intentionally do **not** start the ACP child process just because
a client polls a GET route: if the daemon is idle, they return
`initialized: false` with an empty snapshot. Session status routes require a
live session and return `404 { code: "session_not_found", ... }` for unknown
ids.

Capability tags:

- `workspace_mcp` → `GET /workspace/mcp`
- `workspace_skills` → `GET /workspace/skills`
- `workspace_providers` → `GET /workspace/providers`
- `workspace_acp_status` → `GET /workspace/acp/status`
- `workspace_env` → `GET /workspace/env`
- `workspace_preflight` → `GET /workspace/preflight`
- `session_context` → `GET /session/:id/context`
- `session_supported_commands` → `GET /session/:id/supported-commands`
- `session_tasks` → `GET /session/:id/tasks`
- `session_resources` → `GET /session/:id/resources`
- `session_monitor_tool_correlation` → monitor entries from `GET /session/:id/tasks`
  include `toolUseId` for transcript-to-task correlation
- `session_status` → `GET /session/:id/status`
- `session_info` → `GET /workspace/:id/session-info` and `GET /workspaces/:workspace/session-info`
- `session_transcript` → `GET /session/:id/transcript`
- `workspace_persisted_transcript` → `GET /workspaces/:workspace/session/:id/transcript`
- `workspace_session_export` → `GET /workspaces/:workspace/session/:id/export`
- `workspace_archived_session_export` → `GET /workspaces/:workspace/session/:id/archive/export`
- `workspace_session_live_state` → `GET /workspaces/:workspace/sessions/live-state`
- `workspace_qualified_memory` → `POST /workspaces/:workspace/memory/{remember,forget,dream}` and `GET /workspaces/:workspace/memory/{remember,forget,dream}/:taskId`

`workspace_acp_status` reports the primary workspace ACP channel's
point-in-time liveness as `{ channelLive: boolean }`. The handler does not
create a channel, but reaching a runtime route can first start a deferred daemon
runtime, whose configured startup policy may independently preheat ACP. The
snapshot is not a lease: clients must let Session creation revalidate or start
the channel.

### ACP preheat

Capability tag: `workspace_acp_preheat`.

`POST /workspace/acp/preheat?timeoutMs=N` best-effort initializes the primary
workspace ACP channel. `timeoutMs` defaults to 5000 and must be a positive
integer no greater than 60000. Concurrent callers and Session creation share
the same bridge initialization. A request timeout ends only that HTTP wait; it
does not cancel the shared initialization.

```ts
interface WorkspaceAcpPreheatResult {
  ready: boolean;
  channelLive: boolean;
  durationMs: number;
  reason?: 'timeout' | 'error';
  error?: string;
}
```

`ready` always equals `channelLive`. A live response omits `reason` and
`error`; otherwise `reason` is `timeout` or `error`. `durationMs` measures the
current HTTP call, not the full lifetime of an initialization the call joined.
Operational timeout or failure returns HTTP 200. Invalid `timeoutMs` returns
400, while authentication, rate limiting, and deferred-runtime failures retain
their normal responses.

Both ACP workspace routes are singular and primary-workspace-only. Clients
must not use them for a secondary workspace or interpret either response as a
durable readiness guarantee.

Common status cell:

```ts
type DaemonStatus =
  | 'ok'
  | 'warning'
  | 'error'
  | 'disabled'
  | 'not_started'
  | 'unknown';

type DaemonErrorKind =
  | 'missing_binary'
  | 'blocked_egress'
  | 'auth_env_error'
  | 'init_timeout'
  | 'restore_timeout'
  | 'protocol_error'
  | 'missing_file'
  | 'parse_error';

interface DaemonStatusCell {
  kind: string;
  status: DaemonStatus;
  error?: string;
  errorKind?: DaemonErrorKind;
  hint?: string;
}
```

`errorKind` is a closed enum shared by `/workspace/preflight`,
`/workspace/env`, and (eventually) MCP guardrails so SDK clients can render
remediation per category instead of parsing free-form messages. The original
seven status literals came from #4175; `restore_timeout` was added separately
for session restore requests. `blocked_egress` remains reserved until the
egress probe lands.

Status payloads never expose MCP env values, headers, OAuth/service-account
details, provider API keys, provider `baseUrl` / `envKey`, skill body, skill
filesystem paths, hook definitions, or values of secret environment
variables. `/workspace/env` reports the **presence** of whitelisted env
vars only; proxy URLs are stripped of credentials and reduced to
`host:port` before they hit the wire.

### `GET /workspace/mcp`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "discoveryState": "completed",
  "servers": [
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "docs",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
      "description": "Documentation server",
      "extensionName": "docs-ext"
    }
  ]
}
```

`discoveryState` is one of `not_started`, `in_progress`, or `completed`.
`transport` is one of `stdio`, `sse`, `http`, `websocket`, `sdk`, or
`unknown`. `errors` is omitted when discovery succeeds.

**MCP client guardrails (issue [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>)).** Current daemons extend the payload with four additive fields and a capability-scoped budget cell:

```jsonc
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "discoveryState": "completed",
  "clientCount": 3,
  "clientBudget": 2,
  "budgetMode": "enforce",
  "budgets": [
    {
      "kind": "mcp_budget",
      "scope": "workspace",
      "status": "error",
      "errorKind": "budget_exhausted",
      "hint": "Raise --mcp-client-budget or remove servers from mcpServers config.",
      "liveCount": 2,
      "budget": 2,
      "mode": "enforce",
      "refusedCount": 1,
    },
  ],
  "servers": [
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "a",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
    },
    {
      "kind": "mcp_server",
      "status": "ok",
      "name": "b",
      "mcpStatus": "connected",
      "transport": "stdio",
      "disabled": false,
    },
    {
      "kind": "mcp_server",
      "status": "error",
      "name": "c",
      "mcpStatus": "disconnected",
      "transport": "stdio",
      "disabled": false,
      "disabledReason": "budget",
      "errorKind": "budget_exhausted",
      "hint": "...",
    },
  ],
}
```

`budgetMode` is one of `enforce`, `warn`, or `off`. `clientBudget` is absent when no budget was set. `budgets[]` is **always an array** on daemons advertising `mcp_guardrails` (possibly empty when `budgetMode === 'off'`); older daemons omit the field entirely. When `mcp_workspace_pool` is advertised, the cell has `scope: 'workspace'` and covers the selected workspace runtime's shared pool. When that tag is absent, including under `QWEN_SERVE_NO_MCP_POOL=1`, the legacy manager emits `scope: 'session'`. Consumers MUST tolerate additional unrecognized scope values.

`disabledReason` on per-server cells distinguishes operator-disabled (`'config'` — `disabledMcpServers` config list) from budget-refused (`'budget'` — discovered but never connected due to `enforce` mode). Refusals are deterministic by `Object.entries(mcpServers)` declaration order. The per-server `status: 'error', errorKind: 'budget_exhausted'` shadows the raw `mcpStatus: 'disconnected'` (which is true but not the operator-facing severity).

Budget enforcement is capability-driven. With `mcp_workspace_pool`, sessions inside one workspace runtime share transports and one `WorkspaceMcpBudget`; different workspace runtimes never share a pool or budget. Without the tag, each ACP session's `McpClientManager` enforces its own copy of the cap and the snapshot represents that legacy session view.

**Detecting budget pressure.** Two surfaces, both populated post-PR-14b:

- **Push events** (advertised via `mcp_guardrail_events`): subscribe to `GET /session/:id/events` and narrow `mcp_budget_warning` / `mcp_child_refused_batch` frames through `KnownDaemonEvent`. The state machine fires once per upward 75% crossing (re-armed below 37.5%); refusals are coalesced once per discovery pass under `enforce` mode.
- **Snapshot poll** (advertised via `mcp_guardrails`): `GET /workspace/mcp` and inspect the budget cell (`budgets[0]`) together with `mcp_workspace_pool` to determine its scope:

- `budgets[0].status === 'warning'` ⇔ `liveCount >= 0.75 * clientBudget` (matches the hysteresis threshold PR 14b's push event will use).
- `budgets[0].status === 'error'` ⇔ `refusedCount > 0` (one or more servers refused this discovery pass).
- `budgets[0].status === 'ok'` ⇔ below the 75% threshold AND no refusals.

Recommended poll cadence: aligned with whatever already polls `/workspace/mcp`; the snapshot is cheap and the budget cell carries no extra discovery cost. SDK clients that subscribe to push events still benefit from the snapshot for state-after-extended-disconnect (the SSE replay ring depth is finite — `--event-ring-size`, default 8000 — so a client offline longer than the ring's coverage falls back to snapshot resync).

### `GET /workspace/skills`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "skills": [
    {
      "kind": "skill",
      "status": "ok",
      "name": "review",
      "description": "Review code",
      "level": "project",
      "modelInvocable": true,
      "userInvocable": false,
      "installedPath": "/home/alice/project/.qwen/skills/review/SKILL.md",
      "argumentHint": "[path]"
    },
    {
      "kind": "skill",
      "status": "ok",
      "name": "database-review",
      "description": "Review database changes",
      "level": "extension",
      "modelInvocable": true,
      "installedPath": "/home/alice/.qwen/extensions/alibabacloud-database-suite/skills/database-review/SKILL.md",
      "extensionName": "alibabacloud-database-suite",
      "extensionDisplayName": "Alibaba Cloud Database Suite"
    }
  ]
}
```

`level` is one of `project`, `user`, `extension`, or `bundled`.
`userInvocable` (boolean, optional) is omitted for normal skills (meaning
`true`) and is present only as `false` when the skill cannot be invoked
manually. It does not gate the settings-only Skill toggle routes described
below. `modelInvocable` is independent: `false`
means the skill remains manually available but is hidden from model invocation.
`installedPath` is the existing absolute path to the skill's `SKILL.md`; the
daemon returns it as stored without separately resolving symlinks or
canonicalizing it. Current daemons emit it for every skill, while clients must
tolerate its absence from older v1 daemons. Skill bodies, hooks, `skillRoot`,
and other skill configuration remain excluded. `errors` is omitted when
discovery succeeds.

For extension-owned skills, `extensionName` is the canonical manifest name and
is safe to use as the owner identity. `extensionDisplayName` is an optional,
localized presentation value and may be non-unique. New clients should display
`extensionDisplayName ?? extensionName`; older daemons omit the display field.

Repeated reads are served from the last committed workspace snapshot,
periodically revalidated against the child's in-memory cache. A read never
scans skill directories or reparses `SKILL.md` files. The child does verify
that its extension sources are unchanged — one `readdir` of the extensions
directory plus a `stat` per entry, the enablement file, and the store's
activation state — and refreshes only when they moved, so an extension
installed or toggled outside the daemon is still picked up on the next read.
Safe and bare mode skip the check, matching their exclusion of extensions.

### `GET /workspace/providers`

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "current": { "authType": "qwen", "modelId": "qwen3(qwen)" },
  "providers": [
    {
      "kind": "model_provider",
      "status": "ok",
      "authType": "qwen",
      "current": true,
      "models": [
        {
          "modelId": "qwen3(qwen)",
          "baseModelId": "qwen3",
          "name": "Qwen 3",
          "description": null,
          "contextLimit": 4096,
          "isCurrent": true,
          "isRuntime": false
        }
      ]
    }
  ]
}
```

Models are grouped by auth type. Provider connection diagnostics live on
`/workspace/preflight`'s `providers` cell; environment preflight lives on
`/workspace/preflight` and `/workspace/env` (below). `errors` is omitted
when snapshot construction succeeds.

### `GET /workspace/env`

Reports the daemon process's runtime, platform, sandbox, proxy, and the
**presence** of whitelisted secret environment variables. Always answers
from `process.*` state — the daemon never spawns an ACP child to serve
this route, and the response is identical whether ACP is up or idle. The
`acpChannelLive` field is informational only.

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": false,
  "cells": [
    { "kind": "runtime", "name": "node", "status": "ok", "value": "22.4.0" },
    { "kind": "platform", "name": "darwin", "status": "ok", "value": "arm64" },
    {
      "kind": "sandbox",
      "name": "SANDBOX",
      "status": "disabled",
      "present": false
    },
    {
      "kind": "proxy",
      "name": "HTTPS_PROXY",
      "status": "ok",
      "present": true,
      "value": "proxy.internal:1080"
    },
    {
      "kind": "proxy",
      "name": "NO_PROXY",
      "status": "disabled",
      "present": false
    },
    {
      "kind": "env_var",
      "name": "OPENAI_API_KEY",
      "status": "ok",
      "present": true
    },
    {
      "kind": "env_var",
      "name": "ANTHROPIC_BASE_URL",
      "status": "disabled",
      "present": false
    }
  ]
}
```

Cell shape:

```ts
type DaemonEnvKind =
  | 'runtime' // name: 'node' | 'bun' | 'unknown'; value: process.versions.node
  | 'platform' // name: process.platform; value: process.arch
  | 'sandbox' // name: 'SANDBOX' | 'SEATBELT_PROFILE'; value optional
  | 'proxy' // name: HTTP_PROXY | HTTPS_PROXY | NO_PROXY | ALL_PROXY; value: redacted host
  | 'env_var'; // presence-only; value field is ALWAYS omitted

interface DaemonEnvCell extends DaemonStatusCell {
  kind: DaemonEnvKind;
  name: string;
  present?: boolean;
  value?: string;
}
```

**Redaction policy.** `kind: 'env_var'` cells never include a `value`
field; clients see `present: boolean` only. `kind: 'proxy'` cells run the
raw env value through credential redaction (`redactProxyCredentials`) and
then through `URL` parsing so the wire only carries `host:port`. `NO_PROXY`
is passed through redaction verbatim because it is a host list rather than
a URL. The whitelist of enumerated secret env vars currently includes
`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GEMINI_API_KEY`, `GOOGLE_API_KEY`,
`DASHSCOPE_API_KEY`, `OPENROUTER_API_KEY`, and `QWEN_SERVER_TOKEN`. Other
env vars are not enumerated, so accidentally-set secrets stay invisible.

### `GET /workspace/preflight`

Reports daemon readiness checks. **Daemon-level cells** (`node_version`,
`cli_entry`, `workspace_dir`, `ripgrep`, `git`, `npm`) are always
populated from `process.*` and `node:fs`. **ACP-level cells** (`auth`,
`mcp_discovery`, `skills`, `providers`, `tool_registry`, `egress`)
require a live ACP child — when the daemon is idle they emit
`status: 'not_started'` placeholders. The route never spawns ACP solely
to populate cells; the corresponding cells fall back to `not_started`.

Idle response (no ACP child):

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": false,
  "cells": [
    {
      "kind": "node_version",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "22.4.0", "required": ">=22" }
    },
    {
      "kind": "cli_entry",
      "status": "ok",
      "locality": "daemon",
      "detail": { "path": "/usr/local/bin/qwen", "source": "process.argv[1]" }
    },
    {
      "kind": "workspace_dir",
      "status": "ok",
      "locality": "daemon",
      "detail": { "path": "/canonical/path" }
    },
    { "kind": "ripgrep", "status": "ok", "locality": "daemon" },
    {
      "kind": "git",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "2.45.0" }
    },
    {
      "kind": "npm",
      "status": "ok",
      "locality": "daemon",
      "detail": { "version": "10.7.0" }
    },
    {
      "kind": "auth",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "mcp_discovery",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "skills",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "providers",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "tool_registry",
      "status": "not_started",
      "locality": "acp",
      "hint": "spawn a session to populate"
    },
    {
      "kind": "egress",
      "status": "not_started",
      "locality": "acp",
      "hint": "egress probing lands in PR 14 (#4175)"
    }
  ]
}
```

Cell shape:

```ts
type DaemonPreflightKind =
  | 'node_version'
  | 'cli_entry'
  | 'workspace_dir'
  | 'ripgrep'
  | 'git'
  | 'npm'
  | 'auth'
  | 'mcp_discovery'
  | 'skills'
  | 'providers'
  | 'tool_registry'
  | 'egress';

interface DaemonPreflightCell extends DaemonStatusCell {
  kind: DaemonPreflightKind;
  locality: 'daemon' | 'acp';
  detail?: Record<string, unknown>;
}
```

`errorKind` semantics:

- `missing_binary` — Node version below required, missing `QWEN_CLI_ENTRY`,
  ripgrep / git / npm not on PATH (warnings rather than errors for the
  optional binaries).
- `missing_file` — `boundWorkspace` does not exist or is not a directory;
  skill parse error pointing at a missing or unreadable file.
- `parse_error` — `SKILL.md` parse failure, malformed config JSON.
- `auth_env_error` — `validateAuthMethod` returned a non-null failure
  string, or a `ModelConfigError` subclass propagated from provider
  resolution.
- `init_timeout` — `withTimeout` reject in the bridge (an actual timeout
  while waiting on an ACP roundtrip). Recognized via the
  `BridgeTimeoutError` typed class. Note: a transient `mcp_discovery`
  `warning` cell with `connecting > 0` does NOT carry this kind — that's
  a normal handshake-in-progress state, distinct from a real timeout.
- `restore_timeout` — a session load or resume exceeded the dedicated restore
  budget. The REST response is `504` and is retryable; it is distinct from
  child initialization and from the bounded replay-window limits.
- `protocol_error` — ACP `extMethod` rejected because the channel closed
  mid-request, or because tool registry was unexpectedly absent.
- `blocked_egress` — reserved for PR 14 (#4175). PR 13 leaves the
  `egress` cell as `status: 'not_started'`.

If the bridge fails to reach the ACP child while serving a preflight
request (e.g. a mid-request channel close), the envelope's `errors` array
carries a single `ServeStatusCell` describing the failure and the cells
fall back to `not_started` ACP placeholders. Daemon-level cells are still
returned.

### `GET /workspace/tools`

Return the tool catalog reported by the primary workspace's ACP child. This is
a legacy primary-workspace route: it has no workspace selector and must not be
used to infer the tools of a non-primary runtime.

```json
{
  "v": 1,
  "workspaceCwd": "/canonical/path",
  "initialized": true,
  "acpChannelLive": true,
  "tools": [
    {
      "name": "ReadFile",
      "displayName": "Read",
      "description": "Read a file",
      "enabled": true
    }
  ]
}
```

When no ACP child is live, the route still returns `200` with
`acpChannelLive: false`, an empty `tools` array, and a `not_started` entry in
the optional `errors` array. Unexpected bridge failures use the standard `500`
bridge error response. The TypeScript SDK method is `workspaceTools()`; there
is no dedicated capability tag, so clients that support older daemons should
treat `404` as unsupported.

### Workspace file routes

All file paths are resolved through the daemon's primary workspace. Responses use
workspace-relative paths and never return absolute filesystem paths for normal
success cases. Successful file responses include:

```http
Cache-Control: no-store
X-Content-Type-Options: nosniff
```

Filesystem errors use this JSON shape:

```json
{
  "errorKind": "hash_mismatch",
  "error": "expected sha256:..., found sha256:...",
  "hint": "re-read the file and retry with the latest hash",
  "status": 409
}
```

`errorKind` values include `path_outside_workspace`, `symlink_escape`,
`path_not_found`, `binary_file`, `file_too_large`, `untrusted_workspace`,
`permission_denied`, `parse_error`, `hash_mismatch`,
`file_already_exists`, `text_not_found`, and `ambiguous_text_match`.

#### `GET /file`

Reads a text file. Query params: `path` (required), `maxBytes`, `line`, `limit`,
and `cursor`. The daemon rejects binary files. Files above the 256 KiB
full-snapshot
cap require at least one explicit window argument (`line`, `limit`, or
`maxBytes`); a request with none of them remains `file_too_large`. Such a
window is streamed, and its returned UTF-8 content stays capped at 256 KiB.
`maxBytes` always applies to the UTF-8 response bytes after decoding, including
when the source uses another supported encoding within the full-snapshot cap.

Line offsets are resolved by scanning from the start of the file, so a window
is also refused with `file_too_large` when reaching it would read more than
8 MiB (`MAX_TEXT_SCAN_BYTES`). Use `GET /file/bytes` to reach a deeper offset
directly. Large text in an encoding the route cannot decode returns
`binary_file`, not `file_too_large` — retrying with a smaller window cannot
help, and `readBytes` is the same remedy that already applies to binary.

For files within the full-snapshot cap, the response includes `hash`, a SHA-256
digest over the raw on-disk bytes for the whole file, even when `line`, `limit`,
or `maxBytes` returned a slice. Large partial windows omit `hash`, retain the
complete `sizeBytes`, set `truncated: true`, and return
`originalLineCount: null` when the stream stops before EOF.

##### Paging with `cursor`

Requires the `workspace_file_read_cursor` capability. A response that has more
to give returns `hasMore: true` and, when a file byte offset is derivable, a
`nextCursor` token. Passing it back as `cursor` resumes in O(1), where a deep
`line` offset costs a scan from byte 0 and is refused past 8 MiB.

```
GET /file?path=big.log&limit=500          → { content, nextCursor, hasMore: true }
GET /file?path=big.log&limit=500&cursor=… → next page
```

`cursor` and `line` are mutually exclusive (`parse_error`) — both name a
starting point. A malformed or over-long cursor is `parse_error`; a cursor
whose file has been replaced or truncated is `hash_mismatch` (409). Appending
does **not** invalidate an outstanding cursor, which is the case the feature
exists for.

`content` omits the terminating newline of its last line, as every other read
does, so a client reassembling pages joins them with `\n`. `hasMore` is not a
restatement of `nextCursor`: a small non-UTF-8 file read with a `limit` has
more content but no derivable byte offset, so it reports `hasMore: true` with
`nextCursor: null`. The cursor is also null when the byte cap cuts the current
line, because resuming from that offset would return a partial line. For many
short lines, lower `limit` until the page ends before the byte cap and returns
a cursor. For a single oversized line, request the following line explicitly
(for example, `line=2` when starting at line 1), then continue with cursors;
use `GET /file/bytes` when the complete oversized line is required.

```json
{
  "kind": "file",
  "path": "src/index.ts",
  "content": "export {};\n",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "sizeBytes": 11,
  "returnedBytes": 11,
  "truncated": false,
  "hash": "sha256:...",
  "matchedIgnore": null,
  "originalLineCount": null
}
```

#### `GET /file/bytes`

Reads raw bytes from a file without decoding. Query params: `path` (required),
`offset` (default `0`), and `maxBytes` (default `65536`, max `262144`). This
route supports bounded windows on large binary files without slurping the whole
file. The response includes `hash` only when the returned window covers the
entire file.

```json
{
  "kind": "file_bytes",
  "path": "assets/logo.png",
  "offset": 0,
  "sizeBytes": 3912,
  "returnedBytes": 3912,
  "truncated": false,
  "contentBase64": "...",
  "hash": "sha256:..."
}
```

#### `GET /stat`

Return metadata for one primary-workspace path. Query parameter `path` is
required.

```json
{
  "kind": "stat",
  "path": "src/index.ts",
  "type": "file",
  "sizeBytes": 128,
  "modifiedMs": 1700000000123
}
```

`type` is `file`, `directory`, `symlink`, or `other`. The route uses the
workspace file boundary and the common filesystem error envelope described
above. The TypeScript SDK method is `fileStat()`.

#### `GET /list`

List one primary-workspace directory. Query parameter `path` is required;
`includeIgnored=1` (or `true`) includes entries matched by ignore rules. The
response is capped at 2,000 entries and sets `truncated: true` when more exist.

```json
{
  "kind": "list",
  "path": ".",
  "entries": [{ "name": "src", "kind": "directory", "ignored": false }],
  "truncated": false,
  "matchedIgnore": null
}
```

Each entry's `kind` is `file`, `directory`, `symlink`, or `other`. The
TypeScript SDK method is `dirList()`.

#### `GET /glob`

Match paths inside the primary workspace. Query parameter `pattern` is
required. Optional `cwd` narrows the search, `includeIgnored=1` (or `true`)
includes ignored paths, and `maxResults` is an integer from 1 to 50,000
(default 5,000).

```json
{
  "kind": "glob",
  "pattern": "**/*.ts",
  "cwd": "",
  "matches": ["src/index.ts"],
  "count": 1,
  "truncated": false,
  "durationMs": 4
}
```

Matches are workspace-relative. Invalid query values return `400`; workspace
trust, containment, missing-path, and unexpected failures use the common
filesystem error envelope. The TypeScript SDK method is `glob()`.

#### `POST /file/write`

Creates or replaces a text file. This is a strict mutation route: a token-less
trusted-loopback primary request is authorized. A token-less primary request
that reaches the gate without trusted-loopback authority returns
`401 { "code": "token_required" }`. Configured-token and Local Control
credential failures are rejected earlier with plain `401 Unauthorized`; with
`--require-auth`, the global bearer middleware rejects unauthenticated requests
before the route runs.

Body:

```json
{
  "path": "src/new.ts",
  "content": "export const value = 1;\n",
  "mode": "create"
}
```

```json
{
  "path": "src/existing.ts",
  "content": "export const value = 2;\n",
  "mode": "replace",
  "expectedHash": "sha256:..."
}
```

`mode` must be `create` or `replace`. `create` never overwrites an existing
file (`409 file_already_exists`). `replace` requires `expectedHash`; missing or
malformed hashes are `400 parse_error`, and stale hashes are
`409 hash_mismatch`. `expectedHash` is `sha256:` plus 64 lowercase hex
characters, computed over raw on-disk bytes.

`bom`, `encoding`, and `lineEnding` may be supplied. Replacement preserves the
existing file's encoding profile by default; explicit fields override it.
Binary writes are out of scope.

The daemon writes to a random temp file in the target directory, fsyncs where
supported, re-checks the current hash immediately before `rename()`, then
renames into place. This prevents partial-file observation and serializes
daemon-originated writes to the same file, but it is not a cross-process
kernel compare-and-swap: an external editor can still race in the tiny window
between final hash check and rename.

```json
{
  "kind": "file_write",
  "path": "src/existing.ts",
  "mode": "replace",
  "created": false,
  "sizeBytes": 24,
  "hash": "sha256:...",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "matchedIgnore": null
}
```

#### `POST /file/edit`

Applies one exact text replacement to an existing text file. This is also a
strict mutation route and requires `expectedHash`.

```json
{
  "path": "src/config.ts",
  "oldText": "timeout: 30000",
  "newText": "timeout: 60000",
  "expectedHash": "sha256:..."
}
```

`oldText` must be non-empty and occur exactly once. No match returns
`422 text_not_found`; multiple matches return `422 ambiguous_text_match`.
The route preserves encoding, BOM, and line endings, and re-checks
`expectedHash` immediately before the atomic rename.

Explicit writes/edits to ignored paths are allowed because the authenticated
caller named the path. Success responses and audit events include
`matchedIgnore: "file" | "directory" | null`.

```json
{
  "kind": "file_edit",
  "path": "src/config.ts",
  "replacements": 1,
  "sizeBytes": 128,
  "hash": "sha256:...",
  "encoding": "utf-8",
  "bom": false,
  "lineEnding": "lf",
  "matchedIgnore": null
}
```

### `GET /session/:id/context`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "state": {
    "models": {},
    "modes": {},
    "configOptions": []
  }
}
```

For a top-level session, `state` mirrors the same ACP
model/mode/config-option shapes used by `POST /session`,
`POST /session/:id/load`, and `POST /session/:id/resume`. A
`subagent.`-prefixed virtual session id resolves against its parent runtime
and returns an empty `state` object.

### `GET /session/:id/supported-commands`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "availableCommands": [
    {
      "name": "init",
      "description": "Initialize the project",
      "input": null,
      "_meta": { "source": "builtin" }
    }
  ],
  "availableSkills": ["review"]
}
```

`availableCommands` is the same command snapshot used by the
`available_commands_update` SSE notification. `availableSkills` lists skill
names only; clients must not expect skill bodies or paths over this route.

### `GET /session/:id/tasks`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "now": 1700000000000,
  "tasks": [
    {
      "kind": "agent",
      "id": "agent-1",
      "label": "reviewer: check failure",
      "description": "check failure",
      "status": "running",
      "startTime": 1699999999000,
      "runtimeMs": 1000,
      "outputFile": "/tmp/agent-1.jsonl",
      "isBackgrounded": true,
      "subagentType": "reviewer"
    },
    {
      "kind": "agent",
      "id": "agent-2",
      "label": "general-purpose: run the failing test",
      "description": "run the failing test",
      "status": "running",
      "startTime": 1699999999500,
      "runtimeMs": 500,
      "outputFile": "/tmp/agent-2.jsonl",
      "isBackgrounded": false,
      "subagentType": "general-purpose",
      "parentAgentId": "agent-1",
      "parentName": "reviewer",
      "depth": 1
    }
  ]
}
```

This route is a read-only out-of-band snapshot. It is intentionally not a
prompt and can be queried while the session is streaming. The response only
contains whitelisted metadata from the agent, shell, and monitor task
registries; controllers, timers, offsets, pending messages, and raw registry
objects are never exposed.

Agent tasks spawned by another sub-agent (nested sub-agents, bounded by
`maxSubagentDepth`) carry three optional lineage fields: `parentAgentId` (the
spawning agent task's `id`), `parentName` (the spawning agent's
`subagentType`, captured at registration so it survives the parent's eviction
from the registry), and `depth` (0-based launch depth; 0 = spawned by the
top-level session). Agents launched by the top-level session omit
`parentAgentId` and `parentName`; clients should treat all three fields as
optional and fall back to a flat list when they are absent.

### `POST /session/:id/tasks/:taskId/workflow-action`

Controls a workflow run, or starts a new one. The ACP method is
`_qwen/session/tasks/workflow_action`.

```json
{
  "action": "run-script",
  "script": "export const meta = { name: 'daily-audit', description: 'Audits yesterday' }\nreturn await agent(args.question)",
  "args": { "question": "which tables grew?" },
  "sourceRef": { "id": "definition-7", "revision": "rev-3" }
}
```

What `taskId` names depends on the action:

| `action`                            | `taskId`                                                           | Starts a run           |
| ----------------------------------- | ------------------------------------------------------------------ | ---------------------- |
| `pause`, `resume`, `retry`, `rerun` | the run id                                                         | `retry` and `rerun` do |
| `delete-history`                    | the run id                                                         | no                     |
| `run-saved`                         | the saved workflow's name, `<extension>:<name>` for an extension's | yes                    |
| `run-script`                        | a start key the caller chooses                                     | yes                    |

`args`, `sourceRef` and `script` are read by `run-saved` and `run-script`
only. `args` is bound to the script's `args` global and may be any JSON value;
`sourceRef` is the caller's own `{id, revision}`, recorded on the run, its
journal and its snapshot; `script` is the source `run-script` runs and is
required by it. A retry or rerun replays the original run's own `args` and
`sourceRef` — that is what makes it the same run — so those fields are ignored
alongside the control actions.

A started run is session-owned: it runs in the background without an approval
prompt, and its completion reaches the session's completion channel. It is a
`retry`/`rerun` target afterwards like any other run.

`retry` and `rerun` also accept a run restored from history (`isHistorical` in
`GET /session/:id/tasks`), such as one a daemon restart interrupted: the run is
started again from its snapshot's script, `args` and `sourceRef`, and it
registers in, and reports its completion to, the session that sent the
action. A `retry` resumes the run's journal under the same run id and applies
to a `failed` run that no process is still running; a `rerun` starts a new run
id from any finished run. A history entry carrying `argsOmitted` was launched
with `args` too large to keep, so neither action applies to it. Neither does a
snapshot written before `args` were kept, which carries neither `argsOmitted`
nor `argsRecorded`: a run's journal is keyed from a hash of its `args`, so
restarting one whose `args` its history cannot name would replay nothing and
re-dispatch every agent. Both refusals are `workflow_args_unavailable`, and
`GET /session/:id/tasks` reports both as `argsUnavailable` on the entry, so a
client can withhold the two actions instead of discovering the refusal by
making the call. `argsOmitted` stays beside it as the reason, for a client
that wants to say which. The `args` themselves are never put on the wire.

`run-script` passes no definition name, so the run is labelled by the script's own
`export const meta` — a compiled script should declare one, or the run shows
only its id.

The response is `{"changed": true, "status": "running", "taskId": "<runId>"}`
for a started run, `{"changed": true, "status": "<status>"}` for a control
action that took effect, and `{"changed": false}` when nothing happened:
Workflow is unavailable for the session (disabled, bare mode, untrusted
folder), the workspace is untrusted, the saved workflow name is unknown, the
run id is unknown, or another start is already in flight under the same
`taskId`. Only overlapping starts are deduplicated: two concurrent
`run-script` calls under one `taskId` start one run. Once that start returns,
the key is released; submitting it again can start another run, even if the
first run is still active. The key does not provide retry idempotency after
a lost response.

A rejected parameter is a `400` (`-32602` over ACP), not a started run: an
unknown `action`, a `run-script` with no `script`, a `sourceRef` that is not
`{id, revision}` of non-empty strings, or a script rejected before launch
because of invalid syntax or a determinism violation. Workflow start input
errors carry `workflow_invalid_params` and retain the rejection message.

Four refusals come from the run's stored state rather than the request. Each
is `-32602` over ACP, carrying its `errorKind` and its `data.httpStatus`.
Three are a `409`; the fourth, `workflow_not_recorded`, is a `503`, because
nothing was started and the same call may simply be made again: a `retry`
records that the run is running again before it starts — that record is what
keeps another process from starting it a second time — and the record could
not be written.

| `code` / `errorKind`           | HTTP  | When                                                                                                                                                          | What to do                                                     |
| ------------------------------ | ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `workflow_journal_unavailable` | `409` | a `retry` whose run has no journal on disk, or one that cannot be read                                                                                        | `rerun` it, which starts it from the beginning                 |
| `workflow_args_unavailable`    | `409` | a `retry` or `rerun` of a history entry marked `argsUnavailable`: its `args` were too large to keep, or its snapshot predates keeping them                    | start it again with `run-saved` or `run-script` and its `args` |
| `workflow_run_live_elsewhere`  | `409` | a `retry` of a run whose checkpoint records a process that has not been seen to exit — one still running, one on another machine, or one whose pid was reused | `rerun` it, which takes a new run id                           |
| `workflow_not_recorded`        | `503` | a `retry` whose record that the run is running again could not be written, so it did not start                                                                | make the same call again; the run is untouched                 |

`workflowToolFeatures` in `GET /session/:id/supported-commands` advertises
`runSavedArgs` and `runScript`; a daemon without them accepts neither the start
input nor the `run-script` action. It also carries `nameOnly`, true when the
session's model may run named workflows only (`tools.workflowNameOnly`): the
model's own `script` and `scriptPath` calls are refused, while every action
above, `run-script` included, still starts runs. `retryHistorical` is true when
`retry` and `rerun` accept a run restored from history; an older daemon answers
them with `{"changed": false}`, so a client should offer those controls for a
history entry only when it is reported.

### `GET /session/:id/lsp`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "enabled": true,
  "configuredServers": 1,
  "readyServers": 1,
  "failedServers": 0,
  "inProgressServers": 0,
  "notStartedServers": 0,
  "servers": [
    {
      "name": "typescript",
      "status": "READY",
      "languages": ["typescript", "javascript"],
      "transport": "stdio",
      "command": "typescript-language-server"
    }
  ]
}
```

`status` is one of `NOT_STARTED`, `IN_PROGRESS`, `READY`, or `FAILED`.
Optional `error` is present on failed servers when available. Disabled LSP
(including bare mode) returns HTTP 200 with `enabled: false`, zero counts, and
`servers: []`. LSP enabled with no configured servers returns `enabled: true`,
`configuredServers: 0`, and `servers: []`. If initialization fails before the
client exists, the response may include `initializationError`; if a live client
cannot provide a snapshot, the response includes `statusUnavailable: true`.

This route exposes only stable client-facing fields. It intentionally omits
debug internals such as process IDs, spawn args, stderr tails, root URIs, and
workspace-folder paths.

### `GET /session/:id/resources`

```json
{
  "v": 1,
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/session/path",
  "skills": {
    "v": 1,
    "workspaceCwd": "/canonical/session/path",
    "initialized": true,
    "skills": []
  },
  "mcp": {
    "v": 1,
    "workspaceCwd": "/canonical/session/path",
    "initialized": true,
    "discoveryState": "completed",
    "servers": []
  }
}
```

This live-session-owner route reads the selected session's Config through its
own ACP connection. It does not combine the process-global or workspace-level
status routes, initialize a cold runtime, attach a client, or fall back to the
primary workspace. Unknown and persisted-only sessions return the existing
`session_not_found` response.

The nested objects use the exact `GET /workspace/skills` and
`GET /workspace/mcp` status contracts. Their `workspaceCwd` fields identify
the Config that produced the snapshot and match the top-level value. Existing
redaction rules still apply: MCP credentials and headers, environment values,
Skill bodies, and raw settings never appear in the response. MCP
authentication, pool, workspace-budget, and workspace discovery-error
enrichments are absent because their backing state is workspace-owned or keyed
only by server name rather than by session. Status, discovery, and accounting
from the selected session's own MCP manager remain available.

### Standalone session lifecycle (`standalone_sessions_v1`)

When `/capabilities.features` contains `standalone_sessions_v1`, the daemon exposes a process-global route family for top-level standalone sessions owned by its dedicated Conversations runtime. These routes never accept a workspace selector and never fall back to the primary workspace. Direct embeds that cannot construct the complete Conversations ownership, runtime, directory, lifecycle, and deletion-journal dependency graph omit both the feature and all routes below.

| Route                                            | Request                                                                                                                                                                              | Success                                                                                                                                        |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `POST /standalone/sessions`                      | `{ "sessionId": "<UUID>", "modelServiceId"?: string, "startupConfig"?: { "modelServiceId": string, "reasoningEffort"?: ReasoningSelection }, "approvalMode"?: ApprovalMode }`        | `200` with the standalone session, `context: { "kind": "standalone" }`, and its managed projectless output directory. Creation is prompt-less. |
| `GET /standalone/session-options`                | none; any query field is rejected with 400                                                                                                                                           | `200` with `{ v, initialized, current?, approvalMode?, providers, errors? }`; the internal workspace path and ACP-channel state are omitted    |
| `GET /standalone/sessions`                       | Query: `cursor?`, `size?` (1-100), `archiveState?` (`active` or `archived`)                                                                                                          | `200 { sessions, nextCursor?, liveMergeFailed?, truncated? }`                                                                                  |
| `GET /standalone/sessions/:id`                   | none                                                                                                                                                                                 | `202 { sessionId, state: "creating" }` while local creation is in flight, otherwise `200` with the exact summary.                              |
| `POST /standalone/sessions/:id/load`             | Existing restore options only: `historyPageSize?`, `liveReplayMode?`, `compactedReplayMode?`, `hideInheritedHistory?`, `approvalMode?`; client identity stays in `X-Qwen-Client-Id`. | `200` restored standalone session.                                                                                                             |
| `POST /standalone/sessions/:id/resume`           | Same restore options as `load`.                                                                                                                                                      | `200` restored standalone session without load-history replay.                                                                                 |
| `POST /standalone/sessions/:id/repair-directory` | Empty body or `{}`                                                                                                                                                                   | `200` with the verified or recreated managed directory.                                                                                        |
| `PATCH /standalone/sessions/:id/metadata`        | `{ "displayName": string }`                                                                                                                                                          | `200 { sessionId, displayName }`                                                                                                               |
| `GET /standalone/sessions/:id/export`            | Query: `format=html`, `format=md`, `format=json`, or `format=jsonl` (defaults to `html`).                                                                                            | Existing export content type, filename, and body.                                                                                              |
| `POST /standalone/sessions/archive`              | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { archived, alreadyArchived, notFound, errors }`                                                                                          |
| `POST /standalone/sessions/unarchive`            | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { unarchived, alreadyActive, notFound, errors }`                                                                                          |
| `POST /standalone/sessions/delete`               | `{ "sessionIds": ["<UUID>", ...] }`                                                                                                                                                  | `200 { removed, notFound, errors, fileCleanupPending }`                                                                                        |

When `POST /standalone/sessions` carries the legacy `modelServiceId`, the response includes `modelApplied`: `false` means the spawn-time model switch failed (also surfaced via the `model_switch_failed` session event) and the session is running on the agent default model — the create itself still succeeds so the caller can warn, release, or retry explicitly.

Bodies must be JSON objects with no unknown fields. IDs are RFC UUID v1-v5 values; the daemon canonicalizes them to lowercase. Batch requests contain 1-100 strings and are validated and de-duplicated before mutation. A batch failure is reported as `{ sessionId, code, message }` and does not roll back successful operations on other IDs. `fileCleanupPending` means transcript deletion committed but journal-authorized sidecar or managed-directory cleanup must be retried by reconciliation; the session is already logically removed.

Only explicit standalone transcripts and the documented top-level legacy compatibility shape are visible. Child, Live, project, worktree, ambiguous, unreadable, or deletion-journaled records fail closed. Creation continues if its HTTP response disconnects; a committed session is not deleted, and the response client is detached. Recover by exact GET followed by load/resume instead of retrying create as an attach.

Archive, unarchive, repair, rename, and delete share the same per-session lifecycle admission as load/resume and prompts. Delete uses transcript unlink as its durable commit point and a private journal plus atomic managed-directory staging for crash recovery. Recovery restores the directory when the transcript remains intact and completes cleanup when the transcript is gone; any mismatched identity, conflicting path, foreign owner, or ambiguous transcript returns a structured fail-closed error.

### `POST /session`

The optional `startupConfig` contract is described under [Capabilities](<#capabilities>). Unlike legacy best-effort model selection, a rejected startup selection fails creation.

Spawn a new agent or attach to an existing one (under `sessionScope: 'single'`, the default).

Request:

```json
{
  "cwd": "/absolute/path/to/workspace",
  "modelServiceId": "qwen-prod",
  "sessionId": "550e8400-e29b-41d4-a716-446655440000",
  "sessionScope": "thread",
  "worktree": { "slug": "feature-a" }
}
```

| Field            | Required | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd`            | no       | Absolute path matching one registered workspace. If omitted, the route falls back to the primary workspace (read it off `/capabilities.workspaceCwd`). A mismatched non-empty `cwd` returns `400 workspace_mismatch`. When `features` contains `multi_workspace_sessions`, clients may pass any trusted `workspaces[].cwd`; otherwise only the primary workspace is accepted. Workspace paths are canonicalized via `realpathSync.native` (with a resolve-only fallback for non-existent paths) so case-insensitive filesystems don't reject sessions per spelling.                                                      |
| `modelServiceId` | no       | Selects which configured _model service_ the agent will route through (the back-end provider — Alibaba ModelStudio, OpenRouter, etc). If omitted the agent uses its default. If the workspace already has a session, this calls `setSessionModel` on the existing one and broadcasts `model_switched`. Distinct from `modelId` on `POST /session/:id/model`, which selects the model **within** an already-bound service. The `modelServices` array on `/capabilities` is reserved for advertising configured services; in Stage 1 it is always `[]` (the agent's default service is used and not enumerated over HTTP). |
| `startupConfig`  | no       | `{ modelServiceId: string, reasoningEffort?: ReasoningSelection }`; requires `session_startup_config`, creates a new thread and confirms the requested selection without writing shared defaults. Cannot be mixed with top-level `modelServiceId` or explicit single scope.                                                                                                                                                                                                                                                                                                                                              |
| `sessionId`      | no       | RFC-variant UUID v1-v5 chosen by the caller. The daemon normalizes it to lowercase and always creates a fresh thread session; it never treats this field as an idempotent attach. Confirm that `caps.features` contains `session_id_override` before sending it because older daemons may ignore unknown fields. `null` is equivalent to omission.                                                                                                                                                                                                                                                                       |
| `sessionScope`   | no       | Per-request override for session sharing. `'single'` (the daemon-wide default) makes a second same-workspace `POST /session` reuse the existing session (`attached: true`); `'thread'` forces a fresh distinct session every call. Omit to inherit the daemon-wide default. Values outside the enum return `400 { code: 'invalid_session_scope' }`. Old daemons (pre-#4175 PR 5) silently ignore the field — pre-flight `caps.features.session_scope_override` before sending. The daemon-wide default is hardcoded to `'single'` in production today; #4175 may add a `--sessionScope` CLI flag in a follow-up.         |
| `worktree`       | no       | Create a fresh thread session in a user-named Git worktree. The optional `slug` uses the daemon's worktree-name validation. Pre-flight `session_worktree_persistence_v1`; clients must not infer durable isolation from older worktree-shaped responses. Worktree creation is not an attach operation and cannot be combined with branch creation. The route returns success only after relocation, exclusive ownership-marker creation, sidecar persistence, and a final runtime-generation check.                                                                                                                      |

Response:

```json
{
  "sessionId": "<uuid>",
  "workspaceCwd": "/canonical/path",
  "attached": false,
  "worktree": {
    "slug": "feature-a",
    "path": "/canonical/path/.qwen/worktrees/feature-a",
    "branch": "worktree-feature-a"
  },
  "worktreeState": "persisted-v1"
}
```

`attached: true` means a session for that workspace already existed and you're now sharing it.

`worktreeState` is present only for an attested worktree session. Legacy best-effort restore may return `worktree` without `worktreeState`. A cold Part 4A restore whose restore-prompt was fired rather than parked (`suppressWorktreeContextRestore` off, so the route asked the bridge for no deferral) also returns unverified `worktree` metadata until the prompt settles: the child reads active with no reported cwd, relocation is impossible under a live prompt, and the route keeps the session rather than killing the one the caller just recovered. Neither response is durable execution-root proof. A client that requested worktree isolation must require `worktreeState: "persisted-v1"` and the expected canonical path before routing a prompt; an initial load may be retried after the active prompt settles. On SDK reattach the outcome splits on whether the response still carries `worktree` metadata: a response that carries it with a missing attestation or a changed path is terminal for that client — the new attachment is detached and the prompt is not retried — while a response with no `worktree` object at all heals: the client drops its cached worktree claim, keeps the new attachment, and retries the prompt in the same call. The heal does not gate that retry, so one turn can still execute in a directory the client can no longer attest (an `exit_worktree` the client never saw is the ordinary cause, and the client cannot distinguish it from a cleared in-memory association). An isolation-aware caller must therefore re-verify the worktree attestation itself before dispatching a prompt; the dropped claim only makes that caller fail closed at its own identity gate on its next load or selection.

Caller-supplied IDs are unique across all currently registered workspace runtimes and every still-live bridge generation, including draining replacements. A live, pending, active, archived, or worktree-backed duplicate returns `409 session_id_conflict`. Invalid values return `400 invalid_session_id`; an unavailable live-owner or persisted-state check returns retryable `503 session_id_admission_unavailable`. Retry with bounded backoff after bridge or storage health changes; `retryable` means another attempt is safe, not that an immediate retry will succeed. If the downstream agent returns a different ID, the daemon removes that orphan and returns `500 session_id_not_honored`. After an ambiguous response, load or resume the known ID instead of retrying create as an attach.

Multi-client integrations that want independent conversations should send
`sessionScope: "thread"` on each `POST /session`. Use the default `single`
scope only when clients intentionally share one collaborative session; shared
sessions serialize prompts through one FIFO, visible through
`/daemon/status` as `runtime.activity.pendingPrompts` and
`runtime.activity.queuedPrompts`.

Concurrent `POST /session` calls for the same workspace are **coalesced** to one spawn — both callers get the same `sessionId`, exactly one reports `attached: false`. If the underlying spawn fails (init timeout, malformed agent output, OOM), **all coalesced callers receive the same error** — the in-flight slot is cleared so a follow-up call can retry from scratch.

> ⚠️ **`modelServiceId` rejection on a fresh session is silent on the
> HTTP response.** A bad `modelServiceId` (typo, unconfigured service)
> does NOT 500 the create — the session stays operational on the
> agent's default model so the caller still gets a `sessionId` they
> can retry the model switch against (via `POST /session/:id/model`).
> The visible failure signal is a `model_switch_failed` event on the
> session's SSE stream, fired between the spawn handshake and your
> first subscribe. **Subscribers that need to observe this event
> should pass `Last-Event-ID: 0` on their first `GET
/session/:id/events`** to replay from the ring's oldest available
> event (covers the spawn-time `model_switch_failed` even if the
> subscribe lands a few ms after the create response).

### `GET /session/:id/status`

Return the live summary from the runtime that owns the session. This route does
not load a persisted-only session and never falls back to the primary runtime.
Pre-flight `caps.features.session_status`.

```json
{
  "sessionId": "<sid>",
  "workspaceCwd": "/canonical/path",
  "createdAt": "2026-09-10T08:00:00.000Z",
  "clientCount": 1,
  "hasActivePrompt": true,
  "isWaitingForPermission": false,
  "isWaitingForUserQuestion": false,
  "pendingInteractionCount": 0,
  "pendingInteractions": []
}
```

The response is the `DaemonSessionSummary` wire shape. Optional fields include
display and source metadata, `activeWorkState`, `updatedAt`, `turnError`,
worktree or branch metadata, and PR bindings. `404` means
no live owner exists; a bootstrapping, draining, or unavailable owner returns
`503` instead of falling back. An untrusted non-primary owner returns
`403 untrusted_workspace`, and an id live in more than one workspace returns
`500 ambiguous_session_owner`. The TypeScript SDK method is `sessionStatus()`.

### ACP `session/new` caller-supplied ID

ACP clients request the same behavior through the extension metadata field:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "session/new",
  "params": {
    "cwd": "/absolute/path/to/workspace",
    "_meta": {
      "qwen-code/sessionId": "550E8400-E29B-41D4-A716-446655440000"
    }
  }
}
```

The response contains the normalized lowercase ID. Primary and workspace-qualified ACP mounts share admission with REST, including `session/load` and `session/resume`. Invalid IDs use ACP `INVALID_PARAMS` with `data.httpStatus=400` and `data.errorKind="invalid_session_id"`; conflicts use `data.httpStatus=409`; unavailable live-owner or persisted-state checks use `data.httpStatus=503` and `data.retryable=true`.

An ACP-created session that never receives a prompt leaves no persisted trace, and the daemon reaps it when its owning connection closes with zero attached sessions. After that reap the same ID can be created again — that is connection lifecycle, not ID reuse: while the connection (or any attachment) is live, admission rejects the duplicate.

### `POST /session/:id/load`

`liveReplayMode` and `compactedReplayMode` accept `full` (default) or `summary`.
They project `liveJournal` and `compactedReplay` respectively without changing
stored history. Page selection precedes filtering, so visible items may be fewer
than `historyPageSize`. Invalid values return `400 invalid_live_replay_mode` or
`400 invalid_compacted_replay_mode`. Standalone load accepts both options but
reports invalid values as `400 invalid_request`.

Restore a persisted ACP session by id and replay its history through SSE. The path id is authoritative; any `sessionId` field in the body is ignored. Pre-flight `caps.features.session_load` — older daemons return `404` for this route.

Request:

```json
{
  "cwd": "/absolute/path/to/workspace"
}
```

| Field | Required | Notes                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ----- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd` | no       | Same canonicalization + `workspace_mismatch` rules as `POST /session`. Omit to inherit `/capabilities.workspaceCwd`. When `features` contains `multi_workspace_sessions`, callers may pass any trusted registered `workspaces[].cwd`; untrusted non-primary workspaces return `403 untrusted_workspace`. `mcpServers` is intentionally NOT accepted here — daemon-wide MCP is settings-driven (matches `POST /session`). |

Response:

```json
{
  "sessionId": "persisted-1",
  "workspaceCwd": "/canonical/path",
  "attached": false,
  "state": {
    "models": { ... },
    "modes": { ... },
    "configOptions": [ ... ]
  }
}
```

`state` mirrors ACP's `LoadSessionResponse` — `models` is a `SessionModelState`, `modes` a `SessionModeState`, `configOptions` an array of `SessionConfigOption`. Missing fields are agent-decided. Late attachers (the `attached: true` paths below) get the SAME `state` snapshot the original load caller saw — the daemon caches it on the entry; runtime mutations (e.g. `model_switched`) are delivered on the SSE stream, not on subsequent attach responses.

`attached: true` means the session was already live (either from a prior `session/load`/`session/resume`, or because a coalesced concurrent caller raced just ahead).

When the persisted session owns a Part 4A worktree, load/resume validates its sidecar, canonical containment, and exact checkout marker. An idle child is relocated before the response; an active child is attested only when its reported cwd already equals the worktree, and an active child whose reported cwd is absent or elsewhere fails closed instead of being moved under its prompt — except for a cold restore that could not defer its restore-prompt (`suppressWorktreeContextRestore` off — any restore whose effective source is not Channel-owned — or a bridge without the deferral API): that shape still returns `worktree` without `worktreeState`, unrelocated, and the session survives. The relocated and attested responses return `worktree` plus `worktreeState: "persisted-v1"`. Restore-side typed 409 classifications (advertised by `session_worktree_reset_v1`): `worktree_session_superseded` when the session's sidecar carries a `supersededBy` link — the classification reads that link alone, under the per-checkout ownership lock and before any marker read, so a transfer that rolls back while the load waits cannot redirect the caller to a session the same daemon is deleting; the link is written before the marker flips, though, so the body's `replacementSessionId` is a redirect to verify rather than proof that ownership moved: after a committed transfer callers should load that id instead and update their bookkeeping, but in a pre-commit interrupted state it names a replacement that is not the marker owner, whose own restore returns `worktree_reset_interrupted`, and that the retried reset then reaps — so `replacementSessionId` must not be persisted as the task's session id until a load of it succeeds; `worktree_reset_interrupted` when the restored session's `supersedes` link and the named session's `supersededBy` link agree but the marker never moved or is absent — a previous reset crashed mid-transfer and a retried reset is the repair; and `worktree_marker_missing` when the checkout marker is absent without that agreeing link pair — resetting the task is the repair, because no restore path recreates a marker and a restore retry returns the same 409. The interrupted classification is checked first, so an agreeing link pair never surfaces as `worktree_marker_missing`. Whenever the effective restore source is Channel-owned, the route suppresses the agent's best-effort cleanup for Part 4A or unclassifiable sidecar state, so stale, foreign, ambiguous, or ownership-mismatched persisted state is preserved and fails the request. Persisted source metadata takes precedence; when it is absent, the load/resume request supplies the effective source. A structurally valid legacy sidecar without the Part 4A `workspaceCwd` field retains the existing best-effort agent restore, may be cleaned up by that path, and may return `worktree` without `worktreeState`; clients must not infer isolation from that compatibility metadata. Apart from that explicit legacy compatibility case, only sessions whose effective restore source is not Channel-owned retain the existing best-effort cleanup before route validation. A missing sidecar returns no worktree attestation; isolation-aware clients must reject that response rather than rebinding the session to the shared workspace.

**History replay over SSE.** While `loadSession` is in flight on the agent side, the agent may emit `session_update` notifications for persisted turns, or return bulk replay updates in the response metadata. The daemon seeds those events into the session's bounded replay snapshot window before the route response returns. For live sessions, `POST /session/:id/load` only promises that bounded window (`compactedReplay`, `liveJournal`, `lastEventId`), not the full transcript. The window is byte-capped by `--compacted-replay-max-bytes` (default 4 MiB, maximum 256 MiB); if older replay entries were dropped, `compactedReplay[0]` is an id-less `history_truncated` marker. The in-flight `liveJournal` is separately capped by `--max-journal-events` (default 10 000 replay entries) and `--max-journal-bytes` (default 8 MiB of serialized source events). These are per-session **baseline** caps. When an in-flight turn outgrows them, the daemon first tries adaptive growth: it raises that session's caps toward double (up to a per-session hard cap of 256 MiB, entries scaled proportionally, limited by the remaining pool headroom) while the growth granted across every live session fits in one daemon-wide growth pool sized at 5% of the daemon's effective memory budget — the `--memory-budget-mb` value when passed, capped at resolved available memory, otherwise 50% of auto-detected memory — capped at `1024` MB. Accounting is daemon-wide — a multi-workspace daemon runs one bridge per workspace and all of them share the single pool. Growth is on demand and only as far as the pool allows; an operator-pinned `--max-journal-events` or `--max-journal-bytes` disables it, as does a host whose effective budget falls below the 1024 MB minimum (`insufficientMemory`): the pool is 0 and adaptive growth is disabled outright. Consecutive compatible `agent_message_chunk` or `agent_thought_chunk` source events share a replay entry, up to 256 source events per entry, while tool, attribution, provenance, and discrete-message boundaries remain intact. When the journal still exceeds its (possibly grown) caps after the growth the pool allows — including when no headroom is granted or a grant covers only part of the overshoot — the oldest entries are dropped whole (so the retained tail can be much smaller than the byte cap) and a `history_truncated` marker with `scope: 'live_journal'` is prepended; its `truncatedEvents` and `retainedEvents` fields count source events, not replay entries, and its `maxBytes` / `maxEvents` reflect the caps in force (which may already have grown). Clients should render that marker as status and continue applying retained events. Full persisted transcript access is exposed separately through `GET /session/:id/transcript`.

The replay-window byte caps apply after the child has reconstructed the persisted transcript; they do not cap the on-disk JSONL read. A restore that exceeds the daemon budget returns `504` with a `Retry-After` derived from the restore budget (clamped to 5-120s) and `{code: "session_restore_timeout", errorKind: "restore_timeout", retryable: true, sessionId, action, timeoutMs}`. The daemon fences the still-running ACP request and cleans up any late session instead of registering it. A retry for the same id returns `409 restore_in_progress` with `reason: "awaiting_abandoned_cleanup"` and a `Retry-After` of the restore budget (clamped to 5-120s) until that cleanup settles. If late restore cleanup is uncertain, or the abandoned restore has still not settled a full restore budget after its deadline, new sessions on that workspace return `503 acp_channel_unavailable` with `reason: "restore_cleanup_failed"` or `"restore_settlement_overdue"`. A timed-out session initialization follows the same fail-closed admission policy for an older ACP child that settles late: inconclusive cleanup returns `reason: "new_session_cleanup_failed"`, while a request that remains unsettled for one further initialization budget returns `reason: "new_session_settlement_overdue"`. A settlement-overdue state clears immediately after a late failure settles, or after a late success completes its exact-ID cleanup; inconclusive cleanup transitions to the corresponding cleanup-failed state instead. Cleanup-failed states last until the workspace channel drains and is recycled. Already-live sessions remain usable in either case.

**Errors:**

- `404` — persisted session id doesn't exist (`SessionNotFoundError`).
- `400` — `workspace_mismatch` (same shape as `POST /session`).
- `403` — `untrusted_workspace` when `cwd` targets an untrusted non-primary workspace.
- `503` — `session_limit_exceeded` (counts against `--max-sessions`; in-flight restores are accounted for too).
- `504` — `session_restore_timeout`; retryable, with a `Retry-After` derived from the restore budget (clamped to 5-120s) because the same session id stays fenced until late cleanup settles.
- `504` — `init_timeout`; NOT retryable, no `Retry-After`, no `sideEffectPossible`, no fence installed. Emitted when channel initialization times out before the restore request is dispatched (the `ensureChannel` stage); the restore was never attempted, so no session id is fenced and no cleanup is pending.
- `503` — `acp_channel_unavailable` when the workspace channel is closed to new session work. `reason` says why: `restore_cleanup_failed` when an abandoned restore could not be cleaned up conclusively; `restore_settlement_overdue` when an abandoned restore has still not settled one full restore budget after its deadline; `new_session_cleanup_failed` when a session created after the public initialization timeout could not be closed conclusively; or `new_session_settlement_overdue` when the timed-out initialization has still not settled one further initialization budget after its deadline. Existing sessions remain available. On a workspace runtime with more than one execution engine, any of these quarantines that engine's channel instead: its sessions take no new work either (prompts, continuations, side requests, shell commands, mid-turn messages, and Goal or workflow starts return the same `503`), settled sessions are closed, and fresh sessions of that engine stay refused until the channel's process tree is gone, with `channel_exit_unverified` while that cannot be confirmed. There, `workspace_change_unacknowledged` means the channel did not acknowledge a session-affecting workspace change (permission rules, settings reload, the Session Workflow gate, model providers or Skills); a late acknowledgement of every missing change ends that quarantine early. A settlement-overdue state clears after a late failure settles or a late success completes its exact-ID cleanup; inconclusive cleanup transitions to the matching cleanup-failed state, which requires the workspace channel to drain and recycle. The body carries `retryAfterSeconds` and the header a matching budget-derived `Retry-After` so clients use an operation-budget-scale backoff instead of polling at the ordinary 5-second cadence.
- `409` — `restore_in_progress` (a `session/resume` for the same id is already in flight, or a fresh spawn supplied an id a restore owns). `Retry-After: 5` while the restore is active; a budget-derived hint once it is fenced as `awaiting_abandoned_cleanup`. Same-action races (two concurrent `session/load` for the same id) coalesce — exactly one returns `attached: false`, the rest return `attached: true` with the same `state`.
- `409` — `session_workspace_conflict` when the same session id is already live or being restored by another workspace runtime.
- `409` — `session_archived` when the id exists only under `chats/archive/`; call `POST /sessions/unarchive` before `load` or `resume`.
- `409` — `session_archiving` when archive or unarchive is in flight for the same id. `Retry-After: 5`.
- `409` — `session_conflict` when the id exists in both `chats/` and `chats/archive/`; delete the session with `POST /sessions/delete` before loading.

### `GET /session/:id/transcript`

Return one page of id-less `session_update` replay frames reconstructed from the active persisted JSONL transcript. Pre-flight `caps.features.session_transcript` — older daemons return `404` for this route.

Query parameters:

| Field                 | Required | Notes                                                                                                                                                                                                                                                                                                                                                    |
| --------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `cursor`              | no       | Opaque base64url cursor returned by the previous page. Omit for the first page. The cursor is daemon-issued and tamper-checked; modifying it returns `400 invalid_transcript_cursor`. It binds to the transcript file identity and frozen first-page byte size; deleting, truncating, replacing, or archiving the file invalidates it and returns `409`. |
| `limit`               | no       | Target number of active `ChatRecord`s in a page. Defaults to `100`, maximum `500`. A backward page may expand to at most `3 * limit` records to preserve turn and tool-call/result boundaries. One record can produce multiple replay frames, so `events.length` may be larger still. Invalid values return `400 invalid_transcript_limit`.              |
| `direction`           | no       | The only accepted value is `backward`, which starts a page at the newest records. Omit the parameter to page forward on a first page, or to continue the direction frozen in `cursor`. Cannot be combined with `cursor`, `beforeRecordId`, `atRecordId`, or `snapshot`.                                                                                  |
| `beforeRecordId`      | no       | Start a backward page before this record id. Implies backward, so do not also send `direction`. Cannot be combined with `cursor` or `atRecordId`; may be paired with `snapshot`.                                                                                                                                                                         |
| `compactedReplayMode` | no       | `full` (default) or `summary`. Projects `events` after page selection, so visible count may be smaller than `limit`. Does not change stored history or cursor selection. Also accepted by the workspace-qualified transcript route. Invalid values return `400 invalid_compacted_replay_mode`.                                                           |

Response:

```json
{
  "v": 1,
  "sessionId": "persisted-1",
  "events": [
    {
      "v": 1,
      "type": "session_update",
      "data": {
        "sessionUpdate": "user_message_chunk",
        "content": { "type": "text", "text": "..." }
      }
    }
  ],
  "nextCursor": "opaque",
  "hasMore": true,
  "startTime": "2026-07-08T00:00:00.000Z",
  "lastUpdated": "2026-07-08T00:01:00.000Z"
}
```

`events` are replay frames only: `{ v: 1, type: "session_update", data: SessionUpdate }`. They do not carry EventBus ids, and the response never includes `lastEventId`. Calling this route does not call `/load`, attach a client, seed the live EventBus, create a live session, or change the current live replay window. Live and inactive active sessions are both reconstructed by the child-side read-only status method so replay uses the same workspace settings, runtime output directory, emitters, and `/load` history semantics without mutating daemon session state.

The first page freezes the current JSONL snapshot size. Later pages read only that byte prefix, so appends after page 1 do not change the result set. If the file disappears, is truncated below the frozen size, is replaced with a different inode, or is moved to archive, the next page returns `409` and the client should restart from page 1 or ask the user to reopen the transcript.

To protect daemon memory and latency, snapshots above the transcript indexing cap fail before the daemon scans the JSONL. Clients receive `413 transcript_too_large` and should fall back to export/offline processing or ask the user to shorten/archive older history.

`partial: true` and `replayError` may appear if replay conversion fails after producing some frames. Partial responses never include `nextCursor`, so clients cannot silently paginate past records that were not converted.

**Errors:**

- `400` — invalid `limit`, `cursor`, `direction`, `beforeRecordId`, or session id shape. The paging parameters are mutually exclusive: `cursor` cannot be combined with `beforeRecordId`, `atRecordId`, or `snapshot`; `snapshot` requires `atRecordId` or `beforeRecordId`; `atRecordId` requires `snapshot`; and `backward` cannot be combined with a cursor or any record anchor.
- `404` — active persisted session id does not exist on the first page request.
- `409` — `session_archived`, `session_archiving`, or `session_conflict` from the same loadability checks as `/load`.
- `409` — transcript snapshot is unavailable because the file was deleted, truncated, replaced, or archived after the cursor was issued; this also applies when preflight can no longer find the active file for a cursor request.
- `413` — `transcript_too_large` when the frozen transcript snapshot exceeds the daemon indexing cap.
- `413` — `transcript_page_too_large` when one aggregate record exceeds the workspace-qualified page budget or the serialized page exceeds its response budget.

### `GET /session/:id/export`

Download the primary workspace's active persisted session transcript. The
optional `format` query is `html` (default), `md`, `json`, or `jsonl`.
Pre-flight `caps.features.session_export`.

Successful responses are attachments with a sanitized filename,
`Cache-Control: no-store`, and `X-Content-Type-Options: nosniff`. The content
type is `text/html`, `text/markdown`, `application/json`, or
`application/jsonl` according to the selected format. The route reads
persisted storage only: it does not resolve a live owner, start ACP, or attach
a client. Use the workspace-qualified route below when the target may be in a
non-primary workspace. Invalid formats return `400 invalid_export_format`;
missing active sessions return `404`; archived, transitioning, or conflicting
storage returns `409`. The TypeScript SDK method is `exportSession()`.

### `GET /workspaces/:workspace/session/:id/transcript`

Return the same `DaemonSessionTranscriptPage` projection as the singular route from the selected registered workspace's active persisted JSONL. Pre-flight `workspace_persisted_transcript`; this capability is independent of `multi_workspace_sessions` and works for a trusted single-workspace primary selected by id or cwd.

The selector and query parameters follow the existing plural workspace and transcript rules. Trusted primary and secondary runtimes and untrusted secondary runtimes may read. An untrusted primary returns `403 untrusted_workspace`. Archived content is not returned.

For this workspace-qualified route, `limit` is the maximum record count for forward pages. A backward page may expand to at most `3 * limit` records so turns and tool-call/result pairs stay intact. A page may stop earlier at the 4 MiB persisted-source budget and return a continuation cursor. Serialized responses are capped at 32 MiB and cursors at 64 KiB. If replay state would exceed the cursor cap, the page returns its successfully converted events with `partial: true`, `hasMore: false`, and no `nextCursor`.

Forward pages and cursor pages are implemented entirely inside the daemon process. A backward first page for a live session first crosses a one-record workspace-bridge flush barrier so the persisted tail is current; that barrier may start ACP and load workspace settings. The persisted page read itself does not parse project-defined agents or skills or create/repair `session-transcript-cursor-key`. Tool frames use persisted tool names and descriptions without consulting the runtime tool registry. Its HMAC cursor key exists only in daemon memory, is isolated per workspace, and rotates on restart; a cursor from a previous daemon process returns `400 invalid_transcript_cursor`.

### `GET /workspaces/:workspace/session/:id/export`

Export the selected registered workspace's active persisted session as an attachment. Pre-flight `workspace_session_export`; do not infer support from `session_export` or `workspace_qualified_rest_core`. The selector resolves as exact workspace id first, then as a URL-encoded absolute cwd after canonicalization. Both primary and secondary runtimes must be trusted. An untrusted runtime returns `403 untrusted_workspace` before session or format validation.

The optional `format` query is `html` (default), `md`, `json`, or `jsonl`. The body, MIME type, filename sanitization, `Cache-Control: no-store`, `X-Content-Type-Options: nosniff`, and attachment disposition match `GET /session/:id/export`. The legacy route remains bound to primary storage.

The plural route reads only the selected workspace's active persisted JSONL under the existing shared archive coordinator. It does not scan other workspace stores, fall back to primary, resolve a live owner, call the workspace bridge, start ACP, attach a client, or load settings. A session id that exists only in another workspace returns `404 { code: "session_not_found" }`; archived sessions return `409 session_archived`. Invalid formats return `400 invalid_export_format`, and storage races retain the existing `session_archiving` and `session_conflict` errors.

### `GET /workspaces/:workspace/session/:id/archive/export`

Export the selected registered workspace's archived persisted session as an attachment. Pre-flight `workspace_archived_session_export`; support cannot be inferred from active export or plural core capabilities. Workspace selector resolution and trust checks run before session-id and format validation.

TypeScript SDK callers use `WorkspaceDaemonClient.exportArchivedSession(sessionId, options)`. The method always uses native REST and returns the existing `DaemonSessionExportResult` attachment projection.

The optional `format` query, response body, MIME type, sanitized filename, cache policy, security header, and attachment disposition are identical to the active workspace export. Archived source JSONL is capped at 256 MiB before reconstruction; a larger file returns `413 transcript_too_large` with `sessionId`, `snapshotSize`, and `maxBytes`. The active export keeps its existing size behavior.

The route reads only `chats/archive/<id>.jsonl` in the selected trusted workspace under a shared archive-coordinator lease. It does not inspect active content for fallback, scan another workspace, resolve a live owner, call a bridge, start ACP, attach a client, or load settings. An active-only id returns `409 { code: "session_not_archived" }`; a missing id returns `404 { code: "session_not_found" }`; simultaneous active and archived files return `409 session_conflict`; and an archive transition returns `409 session_archiving` with `Retry-After: 5`.

### `POST /session/:id/resume`

Restore a persisted ACP session by id WITHOUT replaying history through SSE. The model context is restored internally on the agent side (via `geminiClient.initialize` reading `config.getResumedSessionData`); the SSE stream stays clean for clients that already have history rendered. Pre-flight `caps.features.session_resume`; `unstable_session_resume` remains a deprecated compatibility alias for older clients.

Accepts the same `cwd`, `approvalMode`, `sourceType`, and `sourceId` fields
as `/load`. The load-only replay fields `historyPageSize`, `liveReplayMode`,
and `compactedReplayMode` are not parsed, validated, or forwarded here — they
are silently ignored, matching the published resume request schema. Same
response shape — `state` mirrors ACP's
`ResumeSessionResponse`. Same error envelope, including
`409 restore_in_progress` (which fires when a `session/load` is in flight;
`session/resume` racing behind another `session/resume` coalesces).

Use `/load` when the client has no history rendered (cold reconnect, picker → open). Use `/resume` when the client already has the turns on screen and only needs the daemon-side handle back.

> ⚠️ **Why is `unstable_session_resume` still advertised?** The daemon's HTTP route and `session_resume` capability are stable for v1, but the bridge still calls ACP's `connection.unstable_resumeSession`. The old tag remains only so SDKs that shipped before `session_resume` can keep working.

### `POST /session/:id/worktree-reset`

Transfer a persisted Part 4A worktree session's checkout ownership to a fresh replacement session. Pre-flight `session_worktree_reset_v1` — older daemons return `404` for this route. The route is how Channel named-task `/clear` resets a worktree task without discarding its files: the replacement gets a brand-new conversation, and the superseded session keeps its transcript in the catalog but can never be restored again.

Request body (all fields optional):

```json
{
  "cwd": "/canonical/path",
  "modelServiceId": "service-id",
  "approvalMode": "plan",
  "sourceType": "channel",
  "sourceId": "dingtalk-main"
}
```

`cwd` follows the same resolution as `POST /session/:id/load` and may be omitted for the daemon's primary workspace. The metadata fields stamp the replacement session with the same thread-scope and source conventions as a worktree creation.

On success the route returns `200` with the replacement session's create-shape response — the new `sessionId`, `workspaceCwd`, `currentCwd` equal to the verified canonical worktree path, `worktree` metadata, and `worktreeState: "persisted-v1"`. Callers discard their handle on the old id and use the replacement id from then on; the old id's later restore returns `worktree_session_superseded` with `replacementSessionId`, which clients may also use to self-heal stale bookkeeping — but only once a load of that id has succeeded. The classification is decided from the sidecar link alone, and that link is written before the marker flips, so an interrupted pre-commit transfer hands back a replacement that is not the marker owner, whose own restore returns `worktree_reset_interrupted`, and that the retried reset then reaps.

This route never reads `X-Qwen-Client-Id`, so the replacement spawns unattached — but the success response is not registration-free. A fresh transfer mints an owner-style `clientId` for that spawn, registers it on the replacement, and reports it in the body; an idempotent resume of a committed transfer reports none. The minted id is not an attachment (the replacement's attach count is unchanged, so `killSession` with `requireZeroAttaches` still sees it as unattached), but a live client registration is exactly what holds the daemon's idle cleanup off: detach an id you do not keep using, or the replacement stays live indefinitely — and a replacement that is still live with no marker is the shape a later reset refuses to dismantle. Treat the response as the replacement's identity, and attach it through the normal `POST /session/:id/load` or `/resume` surface when a registered client is required.

The transfer is serialized per checkout against worktree restores and other resets, refuses to run while either session is busy (prompt in flight or a pending interaction), and arms an admission barrier on the superseded session: while it is armed, exactly eight writers are refused with `worktree_reset_active` at admission — `POST /session/:id/prompt`, `/rewind`, `/cd`, `/branch`, `/fork`, `/shell`, `/goal`, and `/tasks/:taskId/workflow-action`. The other seven are fenced because each moves the session cwd or starts work in it without passing prompt admission: a shell command runs in the session's effective cwd, which for a relocated worktree session is the checkout itself; a fork agent runs its tools in that cwd; a rewind restores files relative to it; a cd moves it, including into a subdir of the checkout; a branch mutates the superseded session's persisted history and spawns a derivative session while ownership is in flux; a goal `resume` promotes a queued turn and queues a continuation; and a workflow action runs a saved workflow or a caller-supplied script, or restarts a live run, through the session's own tool registry. `POST /session/:id/continue` is not a ninth gate: it drives an accepted continuation through the fenced prompt admission and is refused there. That list is the whole fence, and the fence is not everything that reaches the child — the release and stop paths stay usable mid-transfer by design (cancelling a task, clearing a goal, detaching a client, killing the session), and the daemon-internal background-notification enqueue — a sub-session's completion acknowledgement to its parent, which no HTTP route calls — is unfenced as well, because it enqueues a notification rather than starting a turn. The transfer's last step severs the superseded session's client registrations, clears its in-memory worktree association, and reports whether the superseded session is actually gone. A survivor — a child that still holds background work, so the idle close the last detach triggers is refused and deferred — is surfaced rather than papered over: the daemon logs it, the barrier stays armed on that entry as the only fence left, and the `200` body carries `supersededSessionLive: true` so the caller knows the superseded id is still live and re-attachable inside the checkout whose ownership just moved. Crash safety is per window, and the write order defines the windows: the old sidecar's `supersededBy` link is written first, then the replacement's sidecar carrying `supersedes`, and the marker flips last. A crash before the flip therefore leaves the old session authoritative in one of three shapes a retry tells apart — no links at all (the replacement is an ordinary orphan and a retry simply starts a fresh transfer), the backward link alone with the replacement's sidecar missing (a retry fails closed with `worktree_reset_invalid_state` and leaves the interrupted state untouched for operator repair), or both links present (a retry rolls the partial transfer back and completes a fresh one — unless the interrupted replacement cannot be removed because a client is still attached to it, in which case the rollback fails closed with `worktree_reset_invalid_state` and keeps both links, so a later retry converges once that client is gone). A crash after the flip leaves the replacement authoritative and a retry resumes as a no-op. The old session is never deleted and the worktree checkout is never removed by reset.

**Errors:**

- `404` — `session_not_found` when the session record does not exist.
- `409` — `worktree_reset_unsupported` when the session has no valid Part 4A sidecar (not a worktree session).
- `409` — `worktree_reset_active` when a session involved in the transfer is busy; retry once it settles. Admission of any writer the barrier covers — a prompt, rewind, cwd change, branch, fork, shell command, goal control, or workflow-task action — on a session mid-transfer fails with the same code.
- `409` — `worktree_reset_invalid_state` for corrupt sidecar, invalid marker, containment failure, or an inconsistent supersede link pair. Non-destructive for what this request started: any partial transfer this request began is rolled back before the caller sees it, while the fail-closed resume branches — an invalid marker, a supersede link pair that does not agree (including a backward link whose replacement sidecar is missing), an ambiguous marker owner, and a replacement that is still live on this daemon while the marker is absent — leave the pre-existing interrupted state untouched for operator repair, so that shape stays visible to a later reset or restore. A retry against one of those states re-reads it and returns the same 409: it does not converge, so the state needs operator repair rather than a retry loop. The specific reason is written to the daemon log rather than the wire.
- `500` — fail-closed internal errors (for example a bridge without reset support, or a relocation rejection after rollback); not retryable as-is. One `500` differs in kind: a marker transfer whose own post-commit tail failed _after_ the flip durably committed reports that ownership moved, never runs the destructive rollback, and is repaired by retrying the reset, which resumes the committed transfer idempotently.

### `GET /workspace/:id/session-info` and `GET /workspaces/:workspace/session-info`

Return aggregate persisted session counts for the selected workspace without changing the paginated session-list path:

```json
{
  "active": 450,
  "archived": 30,
  "total": 480,
  "live": 2,
  "expensive": true,
  "cost": "disk_scan"
}
```

`active`, `archived`, and `total` count user-visible local JSONL sessions; internal daemon host sessions and workspace-agent task sessions (`sourceType` `agent-host` and `agent`) are excluded from these counts and from every session listing; a person still opens an agent session by id. `live` is the matching user-visible in-memory bridge count and is omitted for a registered untrusted secondary workspace because that persisted-only read must not query live state. `expensive` is always `true` and `cost` is always `"disk_scan"`; clients must call this endpoint infrequently rather than poll it. If the scan reaches its safety limit or cannot classify every candidate file, the response adds `"truncated": true` and the persisted counts are lower bounds. Missing storage returns zero persisted counts. The plural route uses the same workspace selector and trust policy as the plural session catalog; an untrusted primary still returns `403 untrusted_workspace`.

The TypeScript daemon SDK exposes the plural route through `workspaceById(...)` or `workspaceByCwd(...)`, followed by `getWorkspaceSessionInfo()`.

### `GET /workspace/:id/sessions` and `GET /workspaces/:workspace/sessions`

List sessions whose canonical workspace matches `:id` or `:workspace`. The path parameter first resolves as an exact workspace id and then as a URL-encoded absolute cwd. Primary workspaces include the existing persisted/live merge: the default list is active sessions from `chats/`; pass `archiveState=archived` to list archived sessions from `chats/archive/`. Trusted non-primary workspaces include active persisted sessions from their own `chats/` store and merge matching live summaries without duplicates; if no active persisted sessions exist, the route preserves the previous live-only cursor behavior. Trusted non-primary workspaces also support `archiveState=archived`, the organized `view=organized` list, and `group` filters, reading from their own `chats/`, `chats/archive/`, and session-organization stores; a combined `view=organized&archiveState=archived` query returns only archived sessions without a live merge. Registered untrusted non-primary workspaces support the same list, filter, and pagination shapes but return persisted entries only: the daemon does not query the live bridge or populate pending interactions, turn errors, or client state from the runtime. Persisted defaults such as `clientCount: 0` and `hasActivePrompt: false` remain present for wire compatibility. Missing storage returns an empty list. The plural route still returns `403 { code: "untrusted_workspace" }` for an untrusted primary; legacy primary routes keep their existing compatibility behavior. `archiveState=all` is not supported in v1. Primary and persisted-backed lists keep the existing numeric `cursor` semantics; the no-persisted trusted non-primary live fallback keeps its existing opaque live cursor.

```bash
curl http://127.0.0.1:4170/workspace/$(jq -rn --arg c "$PWD" '$c|@uri')/sessions
curl http://127.0.0.1:4170/workspace/$(jq -rn --arg c "$PWD" '$c|@uri')/sessions?archiveState=archived
curl http://127.0.0.1:4170/workspaces/<workspace-id>/sessions
```

When `workspace_qualified_rest_core` is advertised, workspace-scoped session batch operations, group CRUD, and session organization mutation are available under `/workspaces/:workspace/sessions/{delete,archive,unarchive}`, `/workspaces/:workspace/session-groups`, and `/workspaces/:workspace/session/:id/organization`. For an untrusted secondary, group GET remains available; every group, session, and organization mutation remains trust-gated. Workspace-less batch and organization mutation routes remain primary-workspace-only for compatibility.

Query parameters:

| Field          | Required | Notes                                                                                                                                                                                           |
| -------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `archiveState` | no       | `active` (default) or `archived`. Any other value returns `400 { code: "invalid_archive_state" }`.                                                                                              |
| `cursor`       | no       | Pagination cursor from the previous response.                                                                                                                                                   |
| `size`         | no       | Page size. Invalid values return `400 { code: "invalid_cursor" }` or the existing page-size validation.                                                                                         |
| `view`         | no       | Omit for the legacy recent list. `organized` opts into server-side pinned/group ordering and adds optional organization fields. Any other value returns `400 { code: "invalid_session_view" }`. |
| `group`        | no       | Only meaningful with `view=organized`. `all` (default), `pinned`, `ungrouped`, or a custom group id. Unknown group ids return `404 { code: "group_not_found" }`.                                |

Response:

```json
{
  "sessions": [
    {
      "sessionId": "<uuid>",
      "workspaceCwd": "/canonical/path",
      "createdAt": "2026-05-17T08:30:00.000Z",
      "displayName": "My Session",
      "clientCount": 2,
      "hasActivePrompt": false,
      "isArchived": false
    }
  ],
  "nextCursor": 1772251200000
}
```

With `view=organized`, the daemon reads `<Storage.getProjectDir(cwd)>/session-organization.v1.json`, returns pinned sessions first, then activity time descending, and then `sessionId` for stable ties. The organized cursor is opaque base64url JSON and must not be reused with the legacy recent list. `pinned` is a virtual filter, not a group. `groupId: null` means ungrouped. Archived sessions keep their organization metadata, but `archiveState=archived&view=organized` still returns only archived sessions.

Activity-ordered cursors — the organized view and the `parentSessionId` / `sourceType` filtered lists — are not snapshot-isolated, and a trusted active list orders rows by the later of the transcript mtime and the live activity watermark. A live watermark is in-memory only, so a session's key can regress to its mtime when the live entry retires between two page fetches. The cursor compensates: it carries the identities already emitted at a live-derived key — retaining them while the row is absent from a page's collection and while a pin flip could re-admit them — and excludes them for the rest of the pass, so live-derived key movement returns a session at most once per pass. The guarantee is scoped to the carry: it is bounded at 64 identities (excess identities in one pass degrade to an at-most-once duplicate rather than an error), and a persisted-only row emitted before its pin state changed is never carried, so an unpin between fetches can return that row a second time exactly as before this field existed. Callers that accumulate pages should therefore always key rows by `sessionId`, not only in the over-64 case. Rows can still move or be skipped under concurrent activity, exactly as before; a caller that needs a coherent view reloads from the first page after an activity change.

Additional fields may appear on each session when `view=organized`:

```json
{
  "isPinned": true,
  "pinnedAt": "2026-07-04T12:00:00.000Z",
  "groupId": "018f..."
}
```

Trusted active lists include live daemon overlay fields such as `clientCount`, `hasActivePrompt`, and `activeWorkState`. Untrusted-secondary and archived lists are storage-only: live overlay fields remain absent or false, and archived entries set `isArchived` to `true`. Empty array (not 404) when no sessions exist — a session-picker UI shouldn't error just because the workspace is idle.

### `POST /sessions/catalog`

Read independently paginated session catalogs for several registered workspaces in one request. Pre-flight `session_catalog_batch` once; older daemons retain the workspace-qualified session-list and group endpoints. Each batch member has persisted-workspace ownership: authentication and request admission are process-global, while each entry resolves and reads only its owning workspace. It never registers a workspace, creates a session, starts ACP, or falls back to the primary runtime.

```json
{
  "workspaces": [
    { "workspace": "workspace-a", "cursor": "previous-a-cursor" },
    { "workspace": "/canonical/workspace-b" }
  ],
  "options": {
    "size": 20,
    "archiveState": "active",
    "view": "organized",
    "group": "all"
  },
  "includeGroups": true
}
```

`workspaces` is an ordered array of 1–20 `{ workspace, cursor? }` entries, or `"all"` to select all public registered workspaces. Selectors resolve as exact ids before canonical absolute paths. `all` excludes internal Conversations and removed entries, includes temporarily unavailable entries, and fails if more than 20 entries would be selected; callers must then split explicit selections. An empty public registry returns an empty result. Explicit internal selectors return an entry error without booting the internal runtime.

Shared `options` accept `size` (integer 1–100, default 20), `archiveState`, `view`, `group`, `parentSessionId`, `sourceType`, and `sourceId` with the existing list filters and combinations. `group` and `parentSessionId` are limited to 256 characters. `includeGroups` defaults to false. Each workspace has its own cursor. Batch default pages use activity ordering and opaque cursors, including for live-only sessions; these cursors are not interchangeable with legacy numeric list cursors. Continue with that entry's returned `cwd` and `nextCursor`, preserving the filters. There is no global sort or global pagination cursor.

```json
{
  "workspaces": [
    {
      "workspace": "workspace-a",
      "workspaceId": "workspace-a",
      "cwd": "/canonical/workspace-a",
      "sessions": [],
      "groups": {
        "groups": [],
        "colorOptions": ["red", "orange", "yellow", "green", "blue", "purple"]
      }
    },
    {
      "workspace": "/canonical/workspace-b",
      "error": {
        "code": "workspace_not_found",
        "message": "Workspace is not registered with this daemon.",
        "status": 404
      }
    }
  ]
}
```

The result preserves selection order and the original selector as `workspace`. Successful entries include canonical `cwd`, `workspaceId`, and the existing page fields (`sessions`, optional `nextCursor`, `truncated`, and `liveMergeFailed`). Every returned session's `workspaceCwd` is normalized to its enclosing entry's canonical `cwd`. When requested, `groups` contains the complete group catalog; group identities are scoped to their enclosing workspace. Group reads retain the existing normalization and warn-and-empty fallback for malformed or unreadable organization stores. Failed entries contain `error: { code, message, status }` and resolved identity when available, without a successful empty `sessions` list. Valid batches return HTTP 200 even when some entries fail. Malformed request envelopes return HTTP 400 before storage reads.

Unknown, internal, and removed workspaces produce `404 workspace_not_found`; unavailable or changed runtime generations produce `503 workspace_runtime_unavailable`; an untrusted primary produces `403 untrusted_workspace`. Untrusted secondaries keep the existing persisted-only catalog policy without live bridge reads, repair writes, or debug-session logging. Thrown cursor and group failures retain their existing codes; other thrown read failures become `500 session_catalog_failed`. Trusted active entries can merge existing live bridge state; archived entries remain storage-only.

The daemon runs at most four workspace reads concurrently per batch, limits selectors to 4096 characters and cursors to 16384 characters, and caps each serialized successful entry at 512 KiB. An oversized entry returns `413 catalog_response_too_large`; reduce `size` or omit groups before retrying. This bounds a batch response to approximately 10 MiB plus envelope/error overhead. Client disconnect cancels pending reads and prevents additional workspace reads from starting. The existing daemon JSON body limit and persisted scan limits also apply. Catalog requests use the read rate-limit tier.

### `GET /workspaces/:workspace/sessions/live-state`

Return the selected workspace runtime's memory-only live-session snapshot plus an in-memory catalog version, so clients can stop polling the persisted catalog at `GET /workspaces/:workspace/sessions` for volatile state such as `hasActivePrompt`, waiting flags, and `clientCount`. Pre-flight `workspace_session_live_state`; the tag is independent of `workspace_qualified_rest_core`, so older daemons advertising the broader workspace REST capability do not implement this route. The selector resolves as exact workspace id first, then as a URL-encoded absolute cwd after canonicalization, matching the other plural session routes. The route is trusted-only for primary and secondary runtimes alike: it never falls back to the primary runtime, and it does not use the permissive persisted-catalog policy that grants an untrusted secondary bounded catalog reads. The endpoint has no query parameters and performs no session storage, settings, external command, or ACP round trips, so its cost is independent of persisted session count and JSONL size; the default live-session cap keeps the response bounded, and with the cap disabled cost stays proportional only to the number of live sessions.

Response:

```json
{
  "v": 1,
  "catalogVersion": {
    "generation": "7eca3164-bce1-4f50-94d8-c842c480f213",
    "revision": 17
  },
  "sessions": [
    {
      "sessionId": "session-123",
      "clientCount": 1,
      "hasActivePrompt": true,
      "activeWorkState": "active",
      "isWaitingForPermission": false,
      "isWaitingForUserQuestion": false,
      "updatedAt": "2026-08-18T08:12:30.123Z"
    }
  ]
}
```

`v` is the response schema version. Every successful response includes `Cache-Control: no-store`. `sessions` is the complete, unpaginated, unordered set of sessions currently live in the selected runtime; an empty live runtime returns `200` with `sessions: []`. `clientCount`, `hasActivePrompt`, `isWaitingForPermission`, and `isWaitingForUserQuestion` are required wire fields, and missing optional bridge values project to `0` or `false`. `activeWorkState` is wire-additive and absent on older daemons: `active` means the daemon owns unsettled work or the child sent a fresh non-empty hold snapshot; `idle` is emitted only for a fresh empty snapshot covering every required category; `unknown` means negotiated reporting is stale or incomplete; and `unsupported` means the child did not negotiate reporting. It does not change `hasActivePrompt`: a background shell, cron turn, or pending terminal notification is active work without becoming a foreground prompt. Static catalog fields such as display name, creation time, organization, and source metadata are deliberately excluded and remain owned by the full catalog. An absent live-state row only clears a known catalog row's volatile fields; it never deletes a persisted catalog row.

`updatedAt` is an optional daemon-observed activity watermark, present when a prompt that reached the running state has published a formal terminal in the current bridge. It advances exactly once per such terminal — success, error, cancellation, and deadline alike — is written before the terminal event is published, and is strictly increasing per live session even when two terminals land in one wall-clock millisecond or the wall clock moves backward; a forward clock jump therefore persists until wall time catches up. It is never earlier than the session's `createdAt`: the first advance floors at creation time, so a wall-clock rollback between creation and the first terminal cannot key a row behind the `createdAt` it was already listed at. Prompt admission, queue waits, streamed updates, queue-only cancellation, heartbeats, and interaction waits never advance it. Clients use it to refresh the recency of a catalog row they already hold instead of reloading the full catalog after a completed turn. It is not a persistence acknowledgement: the recorder writes turn results asynchronously, so the value proves only that the daemon observed a running attempt settle. It is absent before the first running terminal in a bridge generation — including for a session restored from disk — so absence is not a support probe, and it disappears when a daemon restart or workspace runtime replacement installs a new bridge. When both a live and a persisted summary exist for one session, full catalog responses report the later valid timestamp, so `GET /session/:id/status`, which returns the bridge summary directly without that merge, may report an earlier value than a list response.

`catalogVersion` is an equality token for daemon-observed catalog changes. `generation` is a random UUID created with each bridge instance and changes on daemon restart or workspace runtime replacement; `revision` starts at zero and increases monotonically within a generation. The only supported operation is equality over the whole pair: same generation and revision means no daemon-observed catalog change, and any difference means reload the full catalog. Clients must not perform revision arithmetic or compare revisions across generations, and conservative extra increments are allowed. The version covers catalog membership and static metadata changes observed by the daemon; ordinary turn activity, prompt lifecycle, attach/detach, and waiting-state transitions do not advance it because the live snapshot already carries the corresponding volatile fields. A changed `updatedAt` under an unchanged version is therefore valid and expected, and it does not invalidate the daemon's persisted-list caches. Two volatile overlay values are deliberately outside both signals: turn-error state (`hasTurnError`/`turnError`) and the pending-interaction count/content (`pendingInteractionCount`/`pendingInteractions`) neither advance the version nor appear in the snapshot, so a client that needs them must keep reading the per-session event stream or the full catalog rather than relying on this route; either field can be added wire-additively when a concrete consumer requires it. Mutations written directly by another daemon, a TUI, or an external process are not observed, so once a client stops periodic full-catalog polling those writes have no bounded discovery time and surface only after an explicit full reload, another observed catalog mutation, reconnect, or daemon/runtime replacement.

Clients reconcile a catalog bundle with a two-read handshake: read live-state A, load the full session list (plus `GET /workspaces/:workspace/session-groups` when the client consumes `session_organization`), then read live-state B. Equal A and B versions accept the bundle; differing versions mark the catalog stale and coalesce at most one trailing reload rather than entering a tight retry loop. Every accepted catalog request must be initiated after A — a request or deduplicated promise that began before A cannot satisfy the reconciliation. Version-driven reloads are single-flight per workspace and obey a non-zero background minimum interval, so sustained catalog churn cannot drive one full catalog scan per live-state poll; explicit local mutations may still request an immediate refresh through the same single-flight operation.

**Errors:**

- `400` — existing selector-validation or `workspace_mismatch` behavior for an unknown, malformed, nested, or unregistered selector; the route never resolves an unknown selector to the primary runtime.
- `403` — `untrusted_workspace` for any untrusted runtime, including an untrusted primary.
- `503` — `workspace_runtime_unavailable` with `Retry-After` for a bootstrapping, transitioning, draining, blocked, or removed runtime, or a runtime generation that closes mid-request.
- `500` — unexpected local errors use the existing bridge error mapping.

### `GET /workspace/:id/session-groups`

List user-defined session groups for a workspace. The singular GET selector accepts any registered workspace id or URL-encoded canonical cwd. The plural GET alias is also available to an untrusted secondary and reads only the organization sidecar. Plural group mutations remain trust-gated, while singular group mutations retain their primary-only compatibility behavior. Pre-flight `caps.features.includes('session_organization')`.

Response:

```json
{
  "groups": [
    {
      "id": "018f...",
      "name": "Frontend",
      "color": "blue",
      "order": 0,
      "createdAt": "2026-07-04T12:00:00.000Z",
      "updatedAt": "2026-07-04T12:00:00.000Z"
    }
  ],
  "colorOptions": ["red", "orange", "yellow", "green", "blue", "purple"]
}
```

Colors are protocol tokens only; clients localize display names. No default color-named groups are created.

### `POST /workspace/:id/session-groups`

Create a custom session group. Strict mutation gate. Pre-flight `caps.features.includes('session_organization')`.

Request:

```json
{ "name": "Frontend", "color": "blue" }
```

`name` is trimmed, must be 1-64 characters, cannot contain control characters, and is unique within the workspace by case-insensitive trimmed comparison. Duplicate names return `409 { code: "group_name_conflict" }`. `color` must be one of the returned `colorOptions`.

Response:

```json
{
  "group": {
    "id": "018f...",
    "name": "Frontend",
    "color": "blue",
    "order": 0,
    "createdAt": "...",
    "updatedAt": "..."
  }
}
```

### `PATCH /workspace/:id/session-groups/:groupId`

Update a custom session group. Strict mutation gate. Pre-flight `caps.features.includes('session_organization')`. Body fields are optional: `{ "name"?: string, "color"?: string, "order"?: number }`. Unknown group ids return `404 { code: "group_not_found" }`; duplicate/invalid names and colors use the same errors as create.

### `DELETE /workspace/:id/session-groups/:groupId`

Delete a custom session group. Strict mutation gate. Pre-flight `caps.features.includes('session_organization')`. Sessions referencing the group are cleared to `groupId: null`; pinned state is preserved. Response is `{ "deleted": true }` when a group was removed and `{ "deleted": false }` when the id did not exist.

### `POST /sessions/delete`

Hard-delete one or more persisted session JSONL files. The daemon first best-effort closes live sessions, then removes the active or archived JSONL. If both active and archived copies exist for the same id, both are removed. Worktree sidecars on both sides are cleaned; file history, subagent transcripts, and runtime sidecars are intentionally preserved.

Request:

```json
{ "sessionIds": ["<uuid>"] }
```

Response:

```json
{
  "removed": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

### `POST /sessions/archive`

Archive one or more sessions. Archive is a state transition, not deletion: the JSONL moves from `chats/<id>.jsonl` to `chats/archive/<id>.jsonl`. File history, subagent transcripts, and runtime sidecars stay in place. If a session is live, the daemon first performs a strict close and requires the ACP agent's close handler to flush the chat recording; if close or flush fails, the JSONL is not moved. Pre-flight `caps.features.session_archive`.

Request:

```json
{ "sessionIds": ["<uuid>"], "resolveConflicts": true }
```

`sessionIds` must be a non-empty string array with at most 100 ids. Duplicates are collapsed.

Response:

```json
{
  "archived": ["<uuid>"],
  "alreadyArchived": [],
  "resolvedConflicts": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

`resolveConflicts` is optional and defaults to `false`. By default, active and archived files with the same id are reported in `errors`, and neither copy is moved, removed, or overwritten. Archiving a live session still performs the strict close described above before classifying the conflict, so that close may flush queued records to the active transcript. With `resolveConflicts: true`, archive repairs the conflict only when both copies are regular transcript files that the selected workspace may maintain, including owned empty or damaged transcripts. It keeps the archived copy, removes the active copy, and reports the id in both `archived` and `resolvedConflicts`. The option does not bypass ownership checks; mixed local/foreign or otherwise ambiguous ownership is reported in `errors`, and neither copy is moved. `errors` entries have `{ "sessionId": "<uuid>", "error": "message" }`.

An archived-only session is returned in `alreadyArchived` after the daemon acquires a writer or maintenance lease to reconcile pending sidecar cleanup. If another process still holds the writer lease, the archived-only id is reported in `errors` until the lease becomes available.

The transcript move or conflict repair is not rolled back if a later cleanup ownership check fails. In that case the id may appear only in `errors`, even though the archive state already changed, and may be omitted from `archived` and `resolvedConflicts`. Retry the same lifecycle request for the same workspace before treating the error as proof that the active copy or conflict remains. When the service can verify the stored transcript identity and acquire the required writer or maintenance lease, the retry reports the authoritative `alreadyArchived` state even for empty or damaged transcripts that session listing omits, and resumes pending sidecar cleanup. Transcripts whose stored identity cannot be verified continue to report `errors` on retry and require manual inspection; retries that cannot acquire the required lease remain in `errors` until it becomes available.

Lifecycle conflicts are batch item outcomes: the workspace-less and workspace-qualified routes return HTTP `200` with the conflict in `errors`. This replaces the earlier workspace-qualified HTTP `409 session_conflict` envelope; clients that called that route must inspect the batch response. Internal-runtime REST batches preserve the safe conflict message while continuing to redact other per-session failure details.

### `POST /sessions/unarchive`

Restore archived sessions to the active directory. This does not resume the session by itself; it only moves `chats/archive/<id>.jsonl` back to `chats/<id>.jsonl`. After unarchive succeeds, clients may call `POST /session/:id/load` or `POST /session/:id/resume`.

Request:

```json
{ "sessionIds": ["<uuid>"], "resolveConflicts": true }
```

Response:

```json
{
  "unarchived": ["<uuid>"],
  "alreadyActive": [],
  "resolvedConflicts": ["<uuid>"],
  "notFound": [],
  "errors": []
}
```

`resolveConflicts` is optional and defaults to `false`. By default, simultaneous active and archived JSONL files produce a conflict in `errors`, and neither copy is moved, removed, or overwritten; an active-only session is returned in `alreadyActive` after the daemon acquires a writer or maintenance lease to reconcile pending sidecar cleanup. If a live session still holds the writer lease, the active-only id is reported in `errors` until that session closes. With `resolveConflicts: true`, unarchive repairs the conflict only when both copies are regular transcript files that the selected workspace may maintain, including owned empty or damaged transcripts. It keeps the active copy, removes the archived copy, and reports the id in both `unarchived` and `resolvedConflicts`. The option does not bypass ownership checks; mixed local/foreign or otherwise ambiguous ownership is reported in `errors`, and neither copy is moved. Archive or unarchive in flight for the same id returns `409 session_archiving` before starting the batch.

The transcript move or conflict repair is not rolled back if a later cleanup ownership check fails. In that case the id may appear only in `errors`, even though the archive state already changed, and may be omitted from `unarchived` and `resolvedConflicts`. Retry the same lifecycle request for the same workspace before treating the error as proof that the archived copy or conflict remains. When the service can verify the stored transcript identity and acquire the required writer or maintenance lease, the retry reports the authoritative `alreadyActive` state even for empty or damaged transcripts that session listing omits, and resumes pending sidecar cleanup. Transcripts whose stored identity cannot be verified continue to report `errors` on retry and require manual inspection; retries that cannot acquire the required lease, including because an active session still holds it, remain in `errors` until the lease becomes available.

ACP-over-HTTP uses the same request and response bodies through vendor methods `_qwen/sessions/archive` and `_qwen/sessions/unarchive`. The REST route table maps `POST /sessions/archive` and `POST /sessions/unarchive` to those methods for ACP transports.

### Multi-workspace live-session routing

When `multi_workspace_sessions` is advertised, live-session operations identify their workspace from the `sessionId`; clients do not add a workspace selector to the URL. In addition to the existing owner-routed lifecycle operations, this applies to `PATCH /session/:id/metadata`, `POST /session/:id/recap`, `POST /session/:id/generate`, `POST /session/:id/btw`, `POST /session/:id/mid-turn-message`, `GET /session/:id/mid-turn-messages`, `DELETE /session/:id/mid-turn-messages/:messageId`, `POST /session/:id/tasks/:taskId/cancel`, `POST /session/:id/goal/clear`, `POST /session/:id/continue`, `POST /session/:id/language`, `POST /session/:id/artifacts`, `DELETE /session/:id/artifacts/:artifactId`, `GET /session/:id/sources`, `POST /session/:id/sources`, and `DELETE /session/:id/sources/:sourceId`. The daemon routes each request to the trusted runtime that owns the live session. An untrusted non-primary owner returns `403 untrusted_workspace`, a missing live owner returns `404 session_not_found`, and an ambiguous owner fails closed with `500 ambiguous_session_owner`.

This rule is live-session-only and does not make every workspace-less session route multi-workspace-aware. Persisted or archived operations use their documented workspace-qualified routes. `POST /session/:id/branch`, `POST /session/:id/fork`, and `POST /session/:id/cd` intentionally remain primary-only and return `non_primary_session_route_not_supported` for non-primary owners.

### Mid-turn messages

`POST /session/:id/mid-turn-message` accepts `{ "message": "...", "messageId": "<optional-message-id>" }`. A successful admission returns `{ "accepted": true, "messageId": "<id>" }` and transfers ownership to the daemon: the message is drained into the active turn or promoted into the normal prompt FIFO when the session becomes idle. Clients using `session_mid_turn_message_query` send a stable `messageId`; repeating it is idempotent while it remains queued, pending, or in the bounded reconciliation rings. A rejection of a request the daemon validated returns `{ "accepted": false }` and never transfers ownership: an open session with no prompt admitted to its prompt FIFO and no active Goal turn reports `{ "accepted": false, "reason": "session_idle" }` so the client can resubmit the message as an ordinary prompt instead of surfacing a failure. For a genuinely new admission, a `content` block referencing an attachment the session no longer holds — or an invalid reference — is declined before the idle and queue verdicts: the request is answered `410 session_attachment_gone` (or `400 invalid_session_attachment_reference`) with an `{ "error", "code" }` body, which carries no `reason` even when the session is idle. Two earlier gates preempt it: a repeated `messageId` whose payload still matches settles idempotently with `{ "accepted": true, "messageId" }` even if the attachment has since been removed, and a session that is closing or authorizing a close is refused with a reasonless `{ "accepted": false }`. The verdict describes only what can drain a mid-turn message, not everything the session may hold — a session snapshot can still report `hasActivePrompt: true` for it, for example while a deferred restore prompt is parked. A reasonless rejection has another cause — the queue is full, the session is closing or authorizing a close, the queued inline-attachment budget is exhausted, or a repeated `messageId` no longer matches the payload the daemon holds. The daemon keeps the payload it already admitted rather than replacing it, but that is not a delivery promise: a promoted message the client removed disappears from pending-prompt snapshots at once — one that had not started is dropped where it stands, one already running is hidden until its aborted turn settles — so the removal response is the only `removed = true` a client ever observes. A closing session never promotes what remains queued, and a turn that settles while a close is still being authorized hands the queue to the admission gate, which refuses it, so those messages are dropped rather than promoted and land in no ring. A close the child then refuses leaves the session live with whatever remained queued still listed and nothing scheduled to promote it until some later turn ends. Those messages are still the daemon's, so the default recovery is to wait: the next turn's settle promotes the backlog, nothing is lost and nothing is delivered twice. A client that cannot wait — no further turn is coming and the user is watching a queue that will not drain — may force the drain instead, and the only supported way is to release each payload before sending it again. Both paths start from the same set: build it from the client's own record of what it enqueued during that window minus whatever it has positive delivery evidence for, not from the listing's silence. The query is not an inventory of what the daemon holds — it omits a prompt already marked removed, both rings are bounded, and an enqueue sent without a client id is not tracked on this surface at all — so an id reported nowhere may have aged out after delivery, been dropped undelivered, or never been listed. Positive delivery evidence is a `mid_turn_message_injected` echo, a `pending_prompt_started` for that id, or membership in either ring.

The mid-turn POST also accepts optional `eventDetailMode`: `full` (default) or
`summary`; invalid values return `400 invalid_event_detail_mode`. The daemon
retains this mode with the message and uses it if an undrained message becomes
a new prompt. Draining into an existing turn does not change that turn's mode.
While queued or promoted-and-pending, a retry under the same `messageId` must
match text, media and effective mode (omitted equals full). Web Shell sends its
current Provider mode. The REST route still rejects new idle admissions with
`reason: "session_idle"`; callers then submit an ordinary prompt.

To force the drain, `DELETE /session/:id/mid-turn-messages/:messageId` every id in that set which the query still reports as queued, and only then post the text again under a new `messageId`, because a repeated id is acked idempotently and re-arms nothing. The order matters and applies to the whole set rather than one id at a time: a still-queued payload the daemon owns is promoted by the next settle, so re-posting an id whose payload was never released delivers that message twice — once from that promotion and once from the resend. Where `session_mid_turn_message_mutation` is not advertised there is no way to release a payload, so waiting is the only safe option. No DELETE verdict establishes delivery on its own. `{ "removed": false }` covers an id already injected or completed — delivered, so it must not be re-posted — and, per the DELETE route below, an id not found, which for one the query had just listed as queued means it left the queue between the two calls and may have been dropped undelivered by the close-authorization path above; decide that case from the client's own evidence. `{ "removed": true }` releases the payload, but for an id a settle promoted in that window it covers both a FIFO entry spliced before it ever dispatched, which is safe to re-post, and an abort of a promoted prompt that had already started, which a re-post then sends a second time. A promoted id also stays deletable through the same route until it settles, even though the listing no longer reports it as queued. The re-post itself is a mid-turn admission and can be refused `{ "accepted": false, "reason": "session_idle" }`; that is not a failure but the signal described above that the session now takes an ordinary prompt, and submitting through the ordinary route starts a turn whose settle promotes whatever is still queued — which is the same reason every id in the set has to be released first.

A missing `reason` is therefore not evidence of a busy session. Daemons that predate `reason` omit it in every case, so clients keep their own idle detection alongside it. New clients connected to an older daemon detect the missing capability and retain their legacy local fallback.

`GET /session/:id/mid-turn-messages` returns the session-wide daemon-owned queue plus bounded `settledMessageIds` and `promotedMessageIds` rings. Settled ids were injected or explicitly deleted; promoted ids entered the normal prompt FIFO. An id in either ring must not be resent.

When a queued message is drained into the active turn, the daemon publishes `mid_turn_message_injected` carrying aligned `messages` and `messageIds` arrays (and the running turn's `promptId` when known). It is a transient dedupe signal, not a transcript item: clients settle completion callbacks registered under those message ids and drop any local pending rows for them. Older daemons additionally carry `originatorClientId` in the payload. A missed echo is recovered from the settled ring via the query above.

When `session_mid_turn_message_mutation` is advertised, an attached session client may call `DELETE /session/:id/mid-turn-messages/:messageId`. It removes the message from either the mid-turn queue or its promoted pending-prompt state; removing a promoted message that is already running aborts that turn, matching ordinary pending-prompt removal. Daemon-owned queue additions and removals publish the existing `pending_prompt_added` and `pending_prompt_completed` session events so attached clients refresh both authoritative queue snapshots. `{ "removed": false }` means the message was already injected, completed, or not found.

### `GET /session/:id/pending-prompts`

Return the currently running prompt and the prompts waiting in the live
session's FIFO. The request may include `X-Qwen-Client-Id`; when present it must
identify an attached client.

```json
{
  "pendingPrompts": [
    {
      "promptId": "<prompt-id>",
      "text": "Explain the failure",
      "queuedAt": 1700000000123,
      "state": "running",
      "originatorClientId": "<client-id>"
    }
  ]
}
```

`state` is `running` for the prompt being dispatched and `queued` for waiting
prompts. `content` appears when the prompt includes structured content such as
images. This is a live-session-owner route: `404` means no live owner and
`503` means the owner is temporarily unavailable. An untrusted non-primary
owner returns `403 untrusted_workspace`, and an id live in more than one
workspace returns `500 ambiguous_session_owner`. There is no dedicated
capability tag; older daemons return `404`. The TypeScript SDK method is
`getPendingPrompts()`.

### `POST /session/:id/prompt`

`eventDetailMode` accepts `full` (default) or `summary`; invalid values return
`400 invalid_event_detail_mode`. Summary filters nested subagent details before
ring retention and SSE delivery, and removes Agent prompts, embedded tools and
in-progress token counters while preserving root content and main-model usage.
Settled Agent results retain `tokenCount` and `executionSummary` token totals
(including failure/cancellation) for turn metrics; nested usage frames remain
filtered. The same rule applies to summary load/transcript responses. The mode is
captured at admission, applied only at dispatch, and resets to full on settlement.
Late subagent events use the mode at publication: full while idle, or the active
prompt's mode when a subsequent prompt is running. They do not inherit the mode
of the prompt that originally launched the subagent.
It applies to all subscribers of the session; filtered details cannot later be
retrieved from the ring. The daemon's `/acp` `session/prompt` accepts the same
optional top-level extension with the same scope (invalid values are JSON-RPC
`-32602`); it is not a standard ACP field and is not forwarded to the child.

Forward a prompt to the agent. Multi-prompt callers FIFO-queue per session (ACP guarantees one active prompt per session).

Request:

```json
{
  "prompt": [{ "type": "text", "text": "What does src/main.ts do?" }],
  "delivery": {
    "kind": "channel",
    "target": {
      "channelName": "dingtalk",
      "type": "user",
      "id": "platform-user-id"
    }
  }
}
```

`delivery` is optional and requires the `channel_delivery` capability. The
daemon still returns `202 {promptId,lastEventId}` when the prompt is admitted.
After a successful `end_turn`, the session submits the visible final text to
the exact workspace's already-running Channel Worker. The payload is only the
last tool-free assistant response block; tool-call preambles, inter-tool
narration, superseded retries, and earlier automatic-continuation blocks are
excluded. An empty or whitespace-only final still produces a correlated
`channel_delivery_result` with `status: "skipped"` after authorization is
consumed, but it does not contact a worker. Delivery success or failure arrives
later through the same replayable event and never changes `turn_complete` into
`turn_error`. Cancellation, Agent failure, and token-limit termination do not
send or publish a delivery result.

Validation: `prompt` must be a non-empty array of objects. Other failures return `400` before reaching the bridge.

Response:

```json
{ "promptId": "session-id########1", "lastEventId": 42 }
```

The `202` response acknowledges admission, not Agent completion. Observe the
session SSE stream after `lastEventId` and correlate `turn_complete` or
`turn_error` by `promptId`.

`turn_complete.data.stopReason` carries the ACP `StopReason` the agent
returned — `end_turn`, `max_tokens`, `max_turn_requests`, `refusal` or
`cancelled`. The daemon can also emit `cancelled` for a prompt aborted without
the agent running it, including queued-prompt removal, caller disconnect, or
drain/teardown; that value does not prove the agent ran the prompt. **Treat the
field as an open string**: it is typed `string` on the wire, the ACP set can
grow, and a client that exhaustively switches on it will break on the next
addition.

Two daemon-side outcomes do **not** arrive on this field. A turn that fails
inside the daemon — deadline expiry, teardown flush, child crash — is published
as a `turn_error` event, never as a `turn_complete` stopReason. Its `data`
always carries `message`; `code` is present only when the daemon classified the
failure (deadline expiry → `prompt_deadline_exceeded`, teardown flush →
`channel_closed`, `session_closed`, `session_killed` or `daemon_shutdown`), and
the frame for a prompt rejected because the ACP child died mid-request carries
neither `code` nor `errorKind`. Treat both as optional and branch on `message`.

A turn recovered from persisted history after a restart is not re-published on
the stream at all; it surfaces in `promptTerminals[]` in the
`POST /session/:id/load` response body — as
`{ terminal: "completed", stopReason: "reconstructed_from_transcript" }` when
the persisted tail shows the turn finished, or
`{ terminal: "interrupted", code: "daemon_lost" }` with **no** `stopReason` when
the daemon died mid-turn. Match the entry by `promptId` and branch on
`terminal`; `promptTerminals` is omitted from the response entirely when the
ledger holds no evidence for the session.

If the HTTP client disconnects mid-prompt, the daemon sends an ACP `cancel` notification to the agent, which winds the prompt down with `stopReason: "cancelled"`.

When `prompt_absolute_deadline` is advertised, `deadlineMs` may shorten the
configured server deadline. Expiry emits a correlated `turn_error` with
`code: "prompt_deadline_exceeded"`. The deadline releases the caller without
killing the agent; if the agent later settles, turn-status polls for that
`promptId` return the settled transcript outcome instead of the deadline error.

### `POST /session/:id/cancel`

Cancel the **currently active** prompt on the session. ACP-side this is a notification, not a request — the agent acknowledges by resolving the active `prompt()` with `cancelled`.

```bash
curl -X POST http://127.0.0.1:4170/session/$SID/cancel
# → 204 No Content
```

> **Multi-prompt contract:** cancel only affects the active prompt. Any prompts the same client previously POSTed and are still queued behind the active one will continue to execute. Multi-prompt queueing is a daemon-introduced behavior (not in ACP spec); the contract for queued prompts is "they keep running unless you cancel each, or kill the session via channel exit".

If queued prompts are unexpected in a multi-client deployment, first confirm
whether callers are sharing a default `sessionScope: "single"` session. For
independent per-thread conversations, create sessions with
`sessionScope: "thread"` so prompts serialize only within that thread.

### `DELETE /session/:id`

Explicitly close a live session. Force-closes even when other clients are attached — cancels any active prompt, resolves pending permissions as cancelled, publishes `session_closed` event, closes the EventBus, and removes the session from daemon maps. On-disk persisted sessions are NOT deleted — they can be reloaded via `POST /session/:id/load`. Pre-flight `caps.features.session_close`.

```bash
curl -X DELETE http://127.0.0.1:4170/session/$SID
# → 204 No Content
```

Idempotent: returns `404` for unknown sessions. The error envelope uses `code: "session_not_found"`; a concurrent close may return `code: "session_closing"`, which clients may treat as the same successful terminal state for this route.

> **`session_closed` event.** SSE subscribers receive a terminal `session_closed` event with `{ sessionId, reason: 'client_close', closedBy?: '<clientId>' }` before the stream ends. SDK reducers treat this identically to `session_died` (sets `alive: false`, clears `pendingPermissions`).

### `PATCH /session/:id/metadata`

Update mutable session metadata. Pre-flight `caps.features.session_metadata`.
Grouping and pinning are intentionally not part of this route; use
`PATCH /session/:id/organization` under `session_organization`.

Request:

```json
{
  "displayName": "My Investigation Session",
  "pr": {
    "number": 123,
    "url": "https://github.com/QwenLM/qwen-code/pull/123",
    "state": "open"
  }
}
```

| Field         | Required | Notes                                                                                                                                                                                                                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `displayName` | no       | String. Values longer than 256 UTF-16 code units are truncated, and the cut is not surrogate-pair aware, so a name ending in a non-BMP character can lose a lone surrogate half. An empty or whitespace-only value is rejected with `400 invalid_metadata`; omit the field to leave the name unchanged. |
| `pr`          | no       | Bind one pull request. Requires a positive integer `number`, an HTTP(S) `url` of at most 2,048 characters without control characters, and optional `state`: `open`, `merged`, or `closed`.                                                                                                              |

Response:

```json
{
  "sessionId": "<uuid>",
  "displayName": "My Investigation Session",
  "prs": [
    {
      "number": 123,
      "url": "https://github.com/QwenLM/qwen-code/pull/123",
      "state": "open"
    }
  ]
}
```

`prs` is the effective bounded binding history and may include refreshed issue
links. Publishes a `session_metadata_updated` event on the session's SSE stream
carrying only the field group that changed: a rename emits `displayName` (plus
`titleSource` when the name is set) and leaves `prs` absent, while a PR binding
change emits `prs` and echoes the current `displayName` when one is set. Treat a
field absent from the event as unchanged, not cleared, and re-read the `200` body
or the session list when you need the full metadata.

### `PATCH /session/:id/organization` and `PATCH /workspaces/:workspace/session/:id/organization`

Update local session organization state through the existing mutation gate. Pre-flight `caps.features.includes('session_organization')`; the plural route additionally requires `workspace_qualified_rest_core`. On the plural route, `:workspace` resolves as an exact registered workspace id first and then as a URL-encoded canonical absolute cwd. The selected runtime must be trusted. Session existence and non-null `groupId` validation are scoped to that runtime's active persisted, archived persisted, and live session state and group store, with no fallback to the primary or another workspace. The legacy route remains primary-workspace-only.

Request:

```json
{ "isPinned": true, "groupId": "018f..." }
```

| Field      | Required | Notes                                                                                                |
| ---------- | -------- | ---------------------------------------------------------------------------------------------------- |
| `isPinned` | no       | Boolean. `true` sets `pinnedAt` if it was not already pinned; `false` clears `pinnedAt`.             |
| `groupId`  | no       | Custom group id or `null` for ungrouped. Unknown group ids return `404 { code: "group_not_found" }`. |
| `color`    | no       | A supported session color token, or `null` to clear the session color.                               |

Response:

```json
{
  "sessionId": "<uuid>",
  "groupId": "018f...",
  "color": "blue",
  "isPinned": true,
  "pinnedAt": "2026-07-04T12:00:00.000Z",
  "updatedAt": "2026-07-04T12:00:00.000Z"
}
```

This state is stored in the project-level session organization sidecar under the daemon runtime storage directory. It is not transcript content, does not update transcript `mtime`, is not exported with transcripts, and is preserved across archive/unarchive.

### `POST /session/:id/heartbeat`

Bump the daemon's last-seen bookkeeping for this session. Long-lived adapters (TUI/IDE/web) ping this on an interval so future revocation policy (Wave 5 PR 24) can distinguish dead clients from quiet ones.

Headers:

| Header             | Required | Notes                                                                                                                                                                                                                                   |
| ------------------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `X-Qwen-Client-Id` | no       | Echoes the daemon-issued id from `POST /session`. Identified clients also bump their per-client timestamp; anonymous heartbeats only bump the per-session watermark. Must satisfy the same `[A-Za-z0-9._:-]{1,128}` shape as elsewhere. |

Request body is empty (`{}` is fine — no fields are read today).

Response:

```json
{
  "sessionId": "<sid>",
  "clientId": "<cid>",
  "lastSeenAt": 1700000000123
}
```

`clientId` is echoed only when a trusted `X-Qwen-Client-Id` was supplied. `lastSeenAt` is the daemon-side `Date.now()` epoch (ms) the bridge stored.

Errors:

- `400` — `{ code: 'invalid_client_id' }` when the header is malformed (header-shape rule) or when it carries a `clientId` that isn't registered for this session (the bridge throws `InvalidClientIdError` before bumping any timestamp).
- `404` — unknown session.

Capability gating: pre-flight `caps.features.client_heartbeat`. Older daemons return `404` for this path.

### `POST /session/:id/model`

Switch the active model **within** the session's currently bound model service. Serialized through the per-session model-change queue.

(For switching the _service_ itself — Alibaba ModelStudio vs OpenRouter etc — pass `modelServiceId` on `POST /session` for a fresh session. Stage 1 has no live service-switch route.)

Request:

```json
{ "modelId": "qwen-staging" }
```

Response: the ACP agent's model-switch result, forwarded verbatim — the
daemon does not reshape it, so the top level carries no `modelId`. Read the
switch details from `_meta.qwenModelSwitch`.

On success, publishes `model_switched` to the SSE stream. On failure, publishes `model_switch_failed` (so passive subscribers see the failure, not just the caller). Races against the agent channel exit so a wedged child can't block the HTTP handler. A successful switch also records the session model in the session JSONL on a best-effort basis; when the record is written, daemon load/resume attempts to restore this session's model before authentication. If the recorded model can no longer be applied (model removed, credentials unavailable), restore uses a same-id registry route when one exists — for a runtime-snapshot record that can be a different endpoint than the recorded binding — and continues on the `settings.model.name` default only when no route resolves. `settings.model.name` is still updated as the default for **new** sessions.

### `POST /session/:id/recap`

Capability tag: `session_recap`. Bridge → ACP extMethod `qwen/control/session/recap`.

Generate a one-sentence "where did I leave off" summary of the session. Wraps core's `generateSessionRecap` (`packages/core/src/services/sessionRecap.ts`), which runs a side-query against the fast model with tools disabled, `maxOutputTokens: 300`, and a strict `<recap>...</recap>` output format. The side-query reads the session's existing GeminiClient chat history and does **not** add to it.

Request body is ignored (send `{}` or empty). Non-strict mutation gate — posture mirrors `/session/:id/prompt` (the call costs tokens but mutates no state). No SSE event is published.

Response (200):

```json
{
  "sessionId": "sess:42",
  "recap": "Debugging the auth retry race. Next: add deterministic timing to the integration test."
}
```

`recap` is `null` (a normal 200, not an error) when:

- the session has fewer than two dialog turns yet,
- the side-query returned no extractable `<recap>...</recap>` payload,
- or any underlying model error occurred (the core helper is best-effort and never throws).

Errors:

- `400 {code: 'invalid_client_id'}` — malformed `X-Qwen-Client-Id` header.
- `404` — session unknown.

Cancellation: **none in v1**. The route does not listen for HTTP client disconnect, no `AbortSignal` is plumbed into the bridge, and the ACP child runs the side-query to completion regardless of whether the caller has disconnected. The only ceilings are the bridge's 60s backstop timeout (`SESSION_RECAP_TIMEOUT_MS`) and the transport-closed race against ACP channel death. This is acceptable because recap is short (single-attempt, `maxOutputTokens: 300`, ~1–5s typical); a request-id-based cancel ext-method can plumb full end-to-end cancellation in a future release if the bandwidth cost ever justifies it.

### `POST /session/:id/generate`

Capability tag: `session_generation`.

Run request-scoped text generation from a caller-supplied prompt. The request
does not read or mutate conversation history and exposes no tools. It prefers
the configured fast model, falling back to the session's main model if the fast
model is missing or cannot be resolved. The endpoint is task-agnostic;
translation is only one possible caller-defined prompt.

Request:

```json
{ "prompt": "Translate into Chinese: Hello" }
```

The response is `text/event-stream`. The server writes an initial SSE comment
immediately, followed by `started`, an optional `thinking` progress event, zero
or more `delta` events, and `done`. The `thinking` event carries no reasoning
content. A model failure after streaming starts produces an `error` event; it
does not retry with another model. Prompts are limited to 32 KiB of UTF-8 text.
Disconnecting the HTTP client cancels the generation request.

### Mutation: approval, tools, skills, init, MCP restart

The daemon exposes five mutation control routes that let remote clients change runtime posture without touching the daemon host's CLI. Approval-mode control retains its non-strict compatibility gate. Tool toggle, skill toggle, workspace init, and MCP restart use the strict mutation gate: trusted-loopback primary, bearer-authenticated, and paired Local Control requests pass. A token-less primary request that reaches the gate without trusted-loopback authority receives `401 {code: 'token_required'}`; missing or invalid configured credentials and unpaired Local Control credentials are rejected earlier with plain `401 Unauthorized`. All five:

- Accept and stamp the `X-Qwen-Client-Id` header (PR 7 audit chain). When the header carries a trusted id, the daemon emits `originatorClientId` on the corresponding SSE event so cross-client UIs can suppress echoes of their own mutations.
- Pre-flight each per-tag capability before exposing the affordance. A daemon that lacks a route returns `404`. For the Skill settings routes, tag absence can instead mean that the same paths serve the retired catalog-validated contract: the single-target route can return HTTP `404 skill_not_found` or `409 skill_not_toggleable`, while the batch route returns HTTP 200 with catalog-derived failures in `errors[]`. Do not use route probing as the version check.

The tool toggle, skill toggle, init, and MCP restart routes emit **workspace-scoped** events: every active session SSE bus receives the event, regardless of which session was attached when the mutation was triggered. `approval-mode` emits a **session-scoped** event because the change is local to one session's `Config`.

#### `POST /session/:id/approval-mode`

Capability tag: `session_approval_mode_control`. Bridge → ACP extMethod `qwen/control/session/approval_mode`.

Change the approval mode of a live session. The new mode lands inside the ACP child's per-session `Config` immediately and is restored from that session's transcript when chat recording is available. Plan's selected execution mode is restored with it. Workspace settings are NOT written by default — pass `persist: true` to also write `tools.approvalMode` to workspace settings. If recording is disabled or degraded, the mode remains live-session-only.

Request:

```json
{ "mode": "auto-edit", "persist": false }
```

`mode` must be one of `'plan' | 'default' | 'auto-edit' | 'auto' | 'yolo'` (mirror of core's `ApprovalMode` enum; the SDK exports `DAEMON_APPROVAL_MODES` for runtime validation). `persist` defaults to `false`.

Response (200):

```json
{
  "sessionId": "sess:42",
  "mode": "auto-edit",
  "previous": "default",
  "persisted": false
}
```

Errors:

- `400 {code: 'invalid_approval_mode', allowed: [...]}` — unknown mode literal.
- `400 {code: 'invalid_persist_flag'}` — `persist` is non-boolean.
- `403 {code: 'trust_gate', errorKind: 'auth_env_error'}` — the requested mode requires a trusted folder (privileged modes in untrusted workspaces are rejected by core's `Config.setApprovalMode`).
- `404` — session unknown.

SSE event (session-scoped): `approval_mode_changed` with `{sessionId, previous, next, persisted, originatorClientId?}`.

#### `POST /workspace/tools/:name/enable`

Capability tag: `workspace_tool_toggle`. Pure file IO — no ACP roundtrip.

Toggle a tool name in the workspace's `tools.disabled` settings list. Tools listed there are **not registered** at all (distinct from `permissions.deny`, which keeps the tool registered and rejects invocation). Both built-in tools and MCP-discovered tools flow through `ToolRegistry.registerTool`, which consults the disabled set.

> ⚠️ **Names must match the registry's exposed identifier exactly.** No alias resolution happens — the route stores whatever string is in the path parameter into `tools.disabled`, and the next ACP child compares against `tool.name` at register time. Built-ins use their canonical registry name (snake_case verb form): `run_shell_command`, `read_file`, `write_file`, `list_directory`, `glob`, `grep_search`, `web_fetch`, etc. — NOT the display labels (`Shell`, `Read`, `Write`) that the CLI surfaces. MCP-discovered tools use the qualified `mcp__<server>__<name>` form (which is also the form `tool_toggled` events broadcast and what `GET /workspace/mcp` lists). Disabling `Bash` will NOT prevent `run_shell_command` from registering on the next session.

Live ACP children retain already-registered tools — the toggle takes effect on the **next** ACP child spawn. Combine with `POST /workspace/mcp/:server/restart` (for MCP-sourced tools) or new-session creation to make the change effective in the current daemon.

Unknown tool names are accepted: pre-disabling a not-yet-installed MCP tool is a legitimate use case.

Request:

```json
{ "enabled": false }
```

Response (200):

```json
{ "toolName": "run_shell_command", "enabled": false }
```

Errors:

- `400 {code: 'invalid_tool_name'}` — empty path parameter, or path parameter exceeds the 256-character cap.
- `400 {code: 'invalid_enabled_flag'}` — `enabled` missing or non-boolean.

SSE event (workspace-scoped): `tool_toggled` with `{toolName, enabled, originatorClientId?}`.

#### Split Skills config and runtime routes

Capability tag: `workspace_skills_config_runtime`.

`GET /workspace/config/skills` reads the daemon-local global configuration owner. `GET /workspaces/:workspace/config/skills` reads the exact registered workspace configuration and does not require a live or trusted runtime. Both return the normal Skills status shape without a required `runtimeEpoch`:

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "initialized": true,
  "skills": []
}
```

`GET /workspace/runtime/skills` and `GET /workspaces/:workspace/runtime/skills` read the selected trusted runtime without starting it. A live catalog includes the runtime generation that produced it:

```json
{
  "v": 1,
  "workspaceCwd": "/work/project",
  "initialized": true,
  "runtimeEpoch": 4,
  "skills": []
}
```

Global installation and deletion use `POST /workspace/config/skills/install` and `DELETE /workspace/config/skills/:name?scope=global`. Workspace installation, deletion, and enablement use `POST /workspaces/:workspace/config/skills/install`, `DELETE /workspaces/:workspace/config/skills/:name?scope=workspace`, and `POST /workspaces/:workspace/config/skills/:name/enable`. Config writes commit durable state before scheduling runtime reconciliation. Their `activation` is `deferred` when no current runtime can be updated and `reconciling` when an update has been queued; a no-op toggle preserves the facade's activation instead of claiming that a runtime refresh occurred.

Config deletion returns `503 skills_config_unavailable` instead of a definitive not-found response when the daemon-local Skill inventory cannot be enumerated.

The singular mutation owner rejects workspace scope with `400 workspace_scope_requires_qualified_workspace`; qualified mutations reject global scope with `400 global_scope_requires_singular_owner`. Qualified writes require a trusted workspace. A legacy injected bridge returns `501 workspace_runtime_not_supported` for qualified config writes that require runtime coordination. Unknown, removed, transitioning, and draining workspaces follow the standard qualified-workspace errors and never fall back to primary.

#### `POST /workspace/skills/:name/enable`

Capability tag: `workspace_skill_settings_toggle`. The workspace-qualified form is `POST /workspaces/:workspace/skills/:name/enable`.

Update the workspace Skill settings for a name without consulting the loaded Skill catalog. The trimmed request name is passed to persistence and returned in the response. Enabling any name records a workspace `skills.enabled` opt-in, even before installation; disabling removes that opt-in and adds a workspace `skills.disabled` entry. Existing entries for Skills that are no longer loaded are preserved, and duplicate/case-variant entries for the target are collapsed. A hard `skills.disabled` entry inherited from a higher scope remains authoritative for effective availability, but does not prevent the workspace scope from recording or removing its own declaration. Workspace declarations otherwise participate in the usual `skills.disabled > skills.enabled > skills.defaultDisabled` resolution and can override higher-scope `skills.defaultDisabled` or `skills.enabled` entries.

This is different from the ACP `qwen/skills/setEnabled` managed-skill operation and the `disable-model-invocation` frontmatter field. For an active Extension parent, effective Skill availability follows `skills.disabled` > `skills.enabled` > `skills.defaultDisabled` > workspace internal override > manifest `skillStates` > enabled. Both hard and default disables remove the skill from slash-command/model availability and reject later skill execution. `disable-model-invocation: true` keeps direct user invocation available and only hides the skill from model invocation.

Request:

```json
{ "enabled": false }
```

Response (200):

```json
{
  "skillName": "review",
  "enabled": false,
  "changed": true,
  "activation": "applied",
  "sessionsRefreshed": 2,
  "sessionsFailed": 0
}
```

`activation` reflects child liveness and any required refresh independently from `changed`. It is `applied` when an ACP child is live and any required refresh succeeds, `deferred` when no child was live at the liveness check or a changed request loses its child/session during the required refresh, and `partial` when at least one other required refresh fails. A no-op can therefore be `applied` or `deferred` with `changed: false`; when `changed` is true and activation is `deferred`, the persisted declaration is used when a child starts. Busy sessions are included in a required refresh. The daemon reloads workspace settings for the ACP child and every active session, notifies SkillManager consumers, and pushes `available_commands_update`. A request already sent to the model is not rewritten; subsequent validation, command snapshots, and model contexts use the new state. If persistence fails, no refresh or event is emitted. If a session refresh fails, the committed setting is retained. When the child returns per-session results, the session counts are exact. If the refresh control itself fails before returning those results, `sessionsFailed: 1` is a conservative lower bound indicating that the refresh request failed.

Errors:

- `400 {code: 'invalid_skill_name'}` — empty path parameter, or more than 256 characters.
- `400 {code: 'invalid_enabled_flag'}` — `enabled` missing or non-boolean.
- `403 {code: 'untrusted_workspace'}` — the selected workspace is not trusted.

The mutation reuses the workspace-scoped `settings_changed` event for each changed key (`skills.disabled` and/or `skills.enabled`); it does not add a new event type. Each of those events includes the same `mutation` object: `{ id, kind: 'skill_toggle', skills: [{ name, enabled }], activation, sessionsRefreshed, sessionsFailed }`. `id` correlates every settings event produced by one toggle request. `skills` lists the requested names and requested enabled values whose workspace settings declarations actually changed; a higher-scope setting can leave effective availability unchanged. Workspace skill status cells include optional `disabledReason: 'hard' | 'default' | 'inactive_extension'` and `lockedScope: 'system' | 'user' | 'systemDefaults'` fields.

#### `POST /workspace/skills/enable`

Capability tag: `workspace_skill_settings_batch_toggle`. The workspace-qualified form is `POST /workspaces/:workspace/skills/enable`.

Update workspace Skill settings for up to 100 names in one request; the cap counts the raw `skillNames` entries before deduplication. Names are trimmed and deduplicated case-insensitively while preserving first-seen order and casing. The daemon does not consult the loaded Skill catalog. It applies all resulting declaration changes in at most one locked settings write and, when anything changed, refreshes active sessions once. Enabling always records an explicit workspace `skills.enabled` opt-in, including names not yet installed, so it can override an Extension's internal disablement. Repeating an identical declaration remains a no-op. Unexpected persistence or runtime-generation failures fail the whole request.

Request:

```json
{
  "skillNames": ["review", "deploy", "missing"],
  "enabled": false
}
```

Response (200):

```json
{
  "enabled": false,
  "activation": "applied",
  "sessionsRefreshed": 2,
  "sessionsFailed": 0,
  "results": [
    {
      "skillName": "review",
      "enabled": false,
      "changed": true
    },
    {
      "skillName": "deploy",
      "enabled": false,
      "changed": true
    },
    {
      "skillName": "missing",
      "enabled": false,
      "changed": true
    }
  ],
  "errors": []
}
```

Malformed requests return HTTP 400 with `invalid_skill_names`, `invalid_skill_name`, or `invalid_enabled_flag`. Authentication, workspace trust, client identity, unexpected persistence failures, and runtime-generation failures fail the whole request through the standard route gates. `errors` remains in the response for wire compatibility and is empty for structurally valid names. Batch-level `activation`, `sessionsRefreshed`, and `sessionsFailed` describe child liveness and the single live-session refresh shared by all changed results. A batch in which no target changed can still answer `applied` when a child is live or `deferred` when none exists, matching the single-Skill no-op response, so derive what actually changed from each result's `changed` flag. When at least one target changes, the daemon emits the same `settings_changed` mutation metadata as the single-Skill route; every `skills.disabled` / `skills.enabled` event from that request shares one `mutation.id`.

#### `POST /workspace/init`

Capability tag: `workspace_init`. Pure file IO — no ACP roundtrip, **no LLM invocation**.

Scaffold an empty `QWEN.md` (or the workspace `context.fileName` settings override) at the daemon's primary workspace root. Mechanical only — for AI-driven content fill, follow up with `POST /session/:id/prompt`.

Default refuses to overwrite when the target file exists with non-whitespace content. Whitespace-only files are treated as absent (matches the local `/init` slash command).

Request:

```json
{ "force": false }
```

Response (200):

```json
{ "path": "/work/bound/QWEN.md", "action": "created" }
```

`action` is `'created'` for fresh creates, `'noop'` when an existing whitespace-only file was left untouched (no write performed), and `'overwrote'` when `force: true` replaced non-empty content. The `workspace_initialized` SSE event mirrors the response action — observers can filter for `action !== 'noop'` to react only to actual on-disk changes.

Errors:

- `400 {code: 'invalid_force_flag'}` — `force` is non-boolean.
- `409 {code: 'workspace_init_conflict', path, existingSize}` — file exists with non-whitespace content and `force` is omitted/false. Body carries the absolute path and size (bytes) so SDK clients can render an "overwrite N bytes?" prompt without re-stat'ing.

SSE event (workspace-scoped): `workspace_initialized` with `{path, action, originatorClientId?}`.

#### `POST /workspace/mcp/reload`

Reload persisted MCP settings into the workspace discovery config and every
active session. The workspace-qualified form is
`POST /workspaces/:workspace/mcp/reload`.

Request body:

```json
{ "forceReconnectAll": true }
```

`forceReconnectAll` is optional and defaults to `false`, preserving
incremental reconciliation. When true, the daemon reconnects every eligible
configured MCP server after the settings reconciliation. Alternatively, pass
`forceReconnectWhich: ["server-a", "server-b"]` to reconnect only named
servers. The options are mutually exclusive. A forced reconnect causes each
transport to read credentials that another local Qwen Code process may have
written to token storage; it does not start an OAuth authorization flow.

The route returns `202 { "accepted": true }`; poll `GET /workspace/mcp` for
the final connection status. Invalid option values return 400.

#### `POST /workspace/mcp/:server/restart`

Capability tag: `workspace_mcp_restart`. Bridge → ACP extMethod `qwen/control/workspace/mcp/restart`.

Restart a configured MCP server through the ACP child's `McpClientManager.discoverMcpToolsForServer` (disconnect + reconnect + rediscover). Pre-checks the live budget snapshot from PR 14 v1's accounting so a restart on a budget-saturated workspace returns a soft refusal rather than triggering a `BudgetExhaustedError` cascade.

Request body is empty (`{}`). The path parameter is the URL-encoded server name as it appears in `mcpServers` config.

Response (200) — discriminated union on `restarted`:

```json
{ "serverName": "docs", "restarted": true, "durationMs": 1234 }
```

```json
{
  "serverName": "docs",
  "restarted": false,
  "skipped": true,
  "reason": "budget_would_exceed"
}
```

Soft skip reasons (all return 200):

| `reason`                | Meaning                                                                                                                                                                               |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `'in_flight'`           | Another discovery / restart for this server is already in progress. The route returns immediately rather than awaiting the original promise. Caller should retry after a short delay. |
| `'disabled'`            | Server is configured but listed in `excludedMcpServers`. Re-enable before restart.                                                                                                    |
| `'budget_would_exceed'` | Daemon is `--mcp-budget-mode=enforce`, the target server is not currently in `reservedSlots`, and the live total has reached `clientBudget`. Caller should free a slot first.         |

Errors (non-2xx):

- `400 {code: 'invalid_server_name'}` — empty path parameter.
- `404` — server name not in `mcpServers` config, or no live ACP channel exists (restart inherently requires a live `McpClientManager` instance).
- `500` — internal error (e.g. `ToolRegistry` not initialized).

SSE events (workspace-scoped): `mcp_server_restarted` with `{serverName, durationMs, originatorClientId?}` on success; `mcp_server_restart_refused` with `{serverName, reason, originatorClientId?}` on soft skip.

#### `POST /language`

Capability tag: `user_language_sync` (conditional — advertised only when settings persistence is available, same condition as `workspace_settings`). Bridge → ACP extMethod `qwen/control/user/language` per trusted runtime with a live channel.

Sessionless **user-level** language sync (issue #10234), for hosts that need to switch language before any session exists (e.g. a welcome page). Ownership classification: process-global — it mutates user-global state only (`~/.qwen/settings.json`, the global `~/.qwen/output-language.md`) plus best-effort runtime refresh, so it takes neither a workspace selector nor a session id. Registered behind the non-strict `mutate()` gate, matching the sibling `POST /session/:id/language`.

The daemon process is the single writer: it persists `general.language` (and, when `syncOutputLanguage` is true, `general.outputLanguage` plus the global `output-language.md`) before fanning out. Each trusted runtime then switches its own process UI language and reloads user-scope settings from disk; when `syncOutputLanguage` is true, it also refreshes every local session's system instruction. Differences from the session-scoped route:

- Project-bound output-language files are NOT rewritten — a session whose workspace has its own `.qwen/output-language.md` keeps that override (project scope wins on refresh).
- Zero sessions and zero live runtime channels are a **success**, not an error; a runtime without a live channel is skipped (not failed) and reads the persisted files when its channel next spawns.
- Untrusted workspace runtimes are skipped, matching the session route's `403 untrusted_workspace` posture.

Request:

```json
{ "language": "zh", "syncOutputLanguage": true }
```

`language` must be one of the codes listed in `/capabilities` `supportedLanguages` (plus `'auto'`); `syncOutputLanguage` defaults to `false`.

Response (200):

```json
{
  "language": "zh",
  "outputLanguage": "Chinese",
  "refresh": { "runtimes": 1, "sessions": 2, "failed": 0 }
}
```

`language` is the daemon-resolved UI locale, so an `'auto'` request can return a concrete language; hosts should read the persisted `general.language` setting for selector state. `outputLanguage` is `null` when `syncOutputLanguage` was false. `refresh.runtimes` counts runtimes that applied the switch over a live channel; `refresh.sessions` sums per-session system-instruction refreshes; `refresh.failed` aggregates per-session refresh failures and failed runtimes (fan-out is best-effort and never fails the request after persistence succeeded).

Errors:

- `400 {code: 'invalid_language', allowed: [...]}` — unknown language code.
- `400 {code: 'invalid_sync_flag'}` — `syncOutputLanguage` is non-boolean.
- `400 {code: 'invalid_client_id'}` — `X-Qwen-Client-Id` is not a client known to any runtime (header is optional; omit it when no session exists).
- `500 {code: 'persist_error'}` — user-settings or output-language file write failed; the failing step and everything after it did not run (earlier applied steps, such as the `general.language` write, are not rolled back).
- `404` — daemon predates the route, or was built without settings persistence (check the capability tag).

SSE event (workspace-scoped, on every runtime's session buses): `language_changed` with `{language, outputLanguage, userLevel: true, originatorClientId?}`. The `userLevel: true` marker distinguishes this fan-out from the session route's per-session event, which carries `sessionId` instead.

### `GET /session/:id/events` (SSE)

Subscribe to the session's event stream.

Headers:

```
Accept: text/event-stream
Last-Event-ID: 42        ← optional, replays from after id 42
X-Qwen-Event-Epoch: ...  ← optional, pairs the cursor with its bus epoch
X-Qwen-Client-Id: ...    ← optional client identity and diagnostic correlation
```

Query params:

| Param              | Required | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `maxQueued`        | no       | Per-subscriber **live frame backlog** cap. Range `[16, 2048]`, default 256. Replay frames force-pushed at subscribe time are exempt from the frame and byte caps; what actually consumes them is live events that arrive while the subscriber is still draining a large `Last-Event-ID: 0` replay. Bump for cold reconnects so the live tail doesn't trip the slow-client warning / eviction before the consumer catches up. The live serialized-byte cap is fixed daemon-side (default 2 MiB) and has no query parameter. Out-of-range / non-decimal / present-but-empty values return `400 invalid_max_queued` before the SSE handshake opens. Pre-flight `caps.features.slow_client_warning` — old daemons silently ignore the param. |
| `connectReason`    | no       | Client-reported diagnostic hint: `initial`, `resume`, `prompt_restart`, `stream_end`, `transport_error`, `state_resync`, or `unknown`. Invalid values normalize to `unknown` and never reject the handshake. The daemon does not use this field for auth, replay, eviction, deduplication, or stream replacement.                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `previousStreamId` | no       | UUID of the previous accepted REST/SSE stream reported by the client. Invalid values are ignored. This is best-effort lineage only and never changes stream behavior.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

A successful handshake includes `X-Qwen-SSE-Stream-Id: <uuid>`. Browser gateways must preserve that response header and expose it through `Access-Control-Expose-Headers`. Old daemons or intermediaries may omit it; clients must continue normally and treat lineage as unavailable. The id identifies this physical REST/SSE connection and correlates its daemon lifecycle, queue diagnostics, and request trace.

Frame format. The `data:` line is the **full event envelope**, JSON-stringified on a single line — `{id?, v, type, data, originatorClientId?}`. The ACP-specific payload (`sessionUpdate`, `requestPermission` arguments, etc.) sits under the envelope's `data` field; the envelope's own `type` matches the SSE `event:` line.

```
id: 7
event: session_update
data: {"id":7,"v":1,"type":"session_update","data":{"sessionUpdate":"agent_message_chunk","content":{"type":"text","text":"…"}}}

id: 8
event: permission_request
data: {"id":8,"v":1,"type":"permission_request","data":{"requestId":"<uuid>","sessionId":"<sid>","toolCall":{...},"options":[...]}}

: heartbeat              ← every 15s, no payload

event: client_evicted    ← terminal frame, no id (synthetic)
data: {"v":1,"type":"client_evicted","data":{"reason":"queue_overflow","droppedAfter":42,"queueSize":256,"maxQueued":256,"queuedBytes":1800000,"maxQueuedBytes":2097152}}

event: client_evicted    ← terminal frame for byte overflow, no id (synthetic)
data: {"v":1,"type":"client_evicted","data":{"reason":"queue_bytes_overflow","droppedAfter":43,"queueSize":1,"maxQueued":256,"queuedBytes":1900000,"maxQueuedBytes":2097152,"eventBytes":300000}}
```

The SSE-level `id:` / `event:` lines duplicate `envelope.id` / `envelope.type` for EventSource compatibility. Raw-`fetch` consumers (the SDK's `parseSseStream`) read everything off the JSON envelope and ignore the SSE preamble lines.

| Event type                | Trigger                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `session_update`          | Any ACP `sessionUpdate` notification (LLM chunks, tool calls, usage)                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `permission_request`      | Agent asked for tool approval                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `permission_resolved`     | Some client voted on a permission via `POST /permission/:requestId`                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `permission_partial_vote` | (consensus only) A vote was recorded but quorum not yet reached. Carries `{requestId, sessionId, votesReceived, votesNeeded, quorum, optionTallies}`. Pre-flight `caps.features.permission_mediation`.                                                                                                                                                                                                                                                                                |
| `permission_forbidden`    | A vote was rejected by the active policy (`designated` mismatch, `local-only` non-loopback, or `consensus` voter not in snapshot). Carries `{requestId, sessionId, clientId?, reason}`. Pre-flight `caps.features.permission_mediation`.                                                                                                                                                                                                                                              |
| `model_switched`          | `POST /session/:id/model` succeeded                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `model_switch_failed`     | `POST /session/:id/model` rejected                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `session_died`            | Agent child crashed unexpectedly. **Terminal: SSE stream closes after this frame; the session is gone from `byId`.** Subscribers should reconnect via `POST /session` to spawn a fresh one.                                                                                                                                                                                                                                                                                           |
| `slow_client_warning`     | Subscriber-local: live frame backlog or live serialized-byte backlog ≥ 75% full. **Non-terminal** — the stream continues; the warning is a heads-up before eviction. Carries `{queueSize, maxQueued, lastEventId, queuedBytes?, maxQueuedBytes?, threshold?}` where `threshold` is `frames`, `bytes`, or `frames_and_bytes`. Fires ONCE per overflow episode; re-arms after both measurements drain below 37.5%. No `id` (synthetic). Pre-flight `caps.features.slow_client_warning`. |
| `client_evicted`          | Subscriber-local: queue overflow. `reason` is `queue_overflow` for the live frame cap and `queue_bytes_overflow` for the live serialized-byte cap. **Terminal: SSE stream closes after this frame** (no `id` — synthetic). Other subscribers on the same session continue.                                                                                                                                                                                                            |
| `stream_error`            | Daemon-side error during fan-out. **Terminal: SSE stream closes after this frame** (no `id` — synthetic).                                                                                                                                                                                                                                                                                                                                                                             |

Reconnect semantics:

- Send `Last-Event-ID: <n>` to replay events with `id > n` from the per-session ring (default depth **8000**, tunable via `qwen serve --event-ring-size <n>`).
- **Gap detection:** within the same epoch, if the first retained ring id is greater than `<n> + 1`, the daemon emits an id-less `state_resync_required` frame with `reason: 'ring_evicted'` before replaying the surviving suffix. The other reasons are `epoch_reset`, `seeded_replay_not_in_ring`, and `replay_budget_exceeded`; the budget signal follows the delivered replay prefix. See [resync reasons and ordering](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#resync-reasons-and-ordering>) for their triggers, cursor fields, and recovery rules.
- The SDK latches `awaitingResync`; clients should call `POST /session/:id/load` and rebuild from the current bounded replay snapshot window. That snapshot may itself start with `history_truncated` when older in-memory replay entries were dropped; this marker is informational and must not start another resync loop. Its optional pagination anchor is [frozen at replay-window eviction](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#history-truncation-anchor>).
- IDs are monotonic within a session's event-bus epoch, starting at 1. A rejected publish must not consume an id; see [event sequence invariants](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon/09-event-schema.md#state-and-forward-compatibility>).
- Synthetic frames (`client_evicted`, `slow_client_warning`, `stream_error`, `state_resync_required`, `replay_complete`) intentionally omit `id` so they don't burn a sequence slot for other subscribers. `replay_complete` marks the end of replay, including empty or incomplete replay; it does not mean resync has completed.

Backpressure:

- Per-subscriber queue defaults to `maxQueued: 256` live items plus a daemon-owned 2 MiB live serialized-byte cap. Replay frames during reconnect, `slow_client_warning`, and `client_evicted` bypass both caps.
- Override only the frame cap via `?maxQueued=N` (range `[16, 2048]`) on the SSE request. There is deliberately no `?maxQueuedBytes`; clients cannot raise daemon memory budget.
- When a subscriber's live frame backlog or live byte backlog crosses 75% full the bus force-pushes a `slow_client_warning` synthetic frame to that subscriber (once per overflow episode; re-armed after both measurements drain below 37.5%). The stream stays open — the warning is a heads-up so the client can drain faster or detach + reconnect cleanly.
- If the live frame cap overflows, the bus emits `client_evicted` with `reason: "queue_overflow"`. An empty live queue admits one oversized event unchanged. Before rejecting a byte overflow in a nonempty queue, the bus may drop an MCP App's whole `rawOutput.html` to `''` when its `fallbackText` is nonempty. If that copy fits, delivery continues without eviction or a resync signal. Otherwise it emits `reason: "queue_bytes_overflow"`. On either eviction the terminal frame is force-pushed and the subscription closes.
- This optional App degradation affects queued delivery only. Direct delivery and the replay ring retain the original; the compaction replay window applies its own budget. The degraded copy carries the same event id as the original ring entry. Therefore an id-bearing delivered payload need not equal the ring's payload, and resuming after that id will not recover its HTML. Clients requiring the document must explicitly re-page the persisted transcript, which is also subject to page-size limits.

### `POST /session/:id/permission/:requestId`

Cast the same vote documented below, but route it through the runtime that owns
the named live session. New multi-workspace integrations should use this form
instead of the legacy process-global route. Pre-flight
`caps.features.session_permission_vote`.

The request body, mediation policies, outcomes, and success response are
identical to `POST /permission/:requestId`. The optional
`X-Qwen-Client-Id` header participates in designated and consensus policy.
Failures use stable `code` values where noted; malformed input and a lost
pending-request race can omit `code`:

- `400` — a malformed vote body (no `code`) or an invalid client identity
  (`invalid_client_id`), or `invalid_option_id` when the selected option was
  not offered. Re-read the offered options instead of retrying the same vote.
- `403` — `permission_forbidden` when the active policy rejects the voter, or
  `untrusted_workspace` when a non-primary owning workspace is not trusted.
  An untrusted primary owner is exempt from this trust check and the vote may
  be accepted.
- `404` — `session_not_found` when no live owner exists, or no `code` when the
  request is not pending.
- `500` — `cancel_sentinel_collision` when the agent's `allowedOptionIds`
  contains the reserved `__cancelled__` sentinel, or `ambiguous_session_owner`
  when more than one workspace claims the session.
- `501` — `permission_policy_not_implemented` for a policy this build does not
  implement.
- `503` — `workspace_runtime_unavailable` when the owning runtime is
  unavailable, or `daemon_draining` when the daemon is no longer accepting
  work.

It never retries against the primary bridge. The TypeScript SDK method is
`respondToSessionPermission()`.

### `POST /permission/:requestId`

Cast a vote on a pending `permission_request`. The active **mediation policy** decides who wins:

| Policy                      | Behavior                                                                                                                                                                                              |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `first-responder` (default) | Any validated voter wins; later voters get `404`. Pre-F3 baseline.                                                                                                                                    |
| `designated`                | Only the prompt originator (`originatorClientId`) decides; non-originators get `403 permission_forbidden / designated_mismatch`. Falls back to first-responder for anonymous prompts.                 |
| `consensus`                 | N-of-M voters must agree (default `N = floor(M/2) + 1`, override via `policy.consensusQuorum`). First option to reach `N` wins. Non-resolving votes get `200` + `permission_partial_vote` SSE frames. |
| `local-only`                | Only loopback voters decide; remote callers get `403 permission_forbidden / remote_not_allowed`.                                                                                                      |

The active policy is configured in `settings.json` under `policy.permissionStrategy` and surfaced on `/capabilities` at `body.policy.permission`. Pre-flight `caps.features.permission_mediation` (with `modes: [...]`) for the build-supported set.

> **F3 (#4175): multi-client permission coordination.** F3 added the four policies above. Pre-F3 daemons hardcoded first-responder; the wire shape stays bit-for-bit unchanged when the configured policy is `first-responder`. New events (`permission_partial_vote`, `permission_forbidden`) are additive — old SDKs see them as `unrecognized_known_event` and gracefully ignore.

> **Permission timeout (disabled by default).** A `permission_request`
> stays pending until: (a) some client votes here, (b) `POST /session/:id/cancel`
> fires, (c) the HTTP client driving the prompt disconnects
> (mid-prompt cancel resolves outstanding permissions as `cancelled`),
> (d) the session is killed, (e) the daemon shuts down, **or
> (f) its configured timeout fires**. On timeout fire the agent's
> `requestPermission` resolves
> as `{outcome: 'cancelled'}`, the audit ring records a
> `permission.timeout` entry, daemon stderr emits a one-line
> breadcrumb, and the SSE bus fans out the standard
> `permission_resolved` cancelled frame so subscribers clean up. The
> shared timeout is configurable via
> `BridgeOptions.permissionResponseTimeoutMs` or
> `qwen serve --permission-response-timeout-ms`. Its default is `0`, so both
> ordinary permissions and `ask_user_question` wait indefinitely for a human
> decision. Voter cancellation, session cancellation, disconnect cleanup, and
> daemon shutdown still resolve pending interactions as cancelled.

Request:

```json
{
  "outcome": {
    "outcome": "selected",
    "optionId": "proceed_once"
  }
}
```

Outcomes:

- `{ "outcome": "selected", "optionId": "<one-of-the-options>" }` — accept / reject / proceed-once / etc, per the agent's offered choices
- `{ "outcome": "cancelled" }` — drop the request (matches what `cancelSession` / `shutdown` do internally)

Response:

- `200 {}` — your vote was accepted (resolved OR recorded under consensus quorum)
- `400` — a malformed vote body (no `code`), `invalid_client_id`, or
  `invalid_option_id` when the selected option was not offered; re-read the
  offered options instead of retrying the same vote
- `403 { "code": "permission_forbidden", "reason": "designated_mismatch" | "remote_not_allowed", "requestId", "sessionId" }` — F3: the active policy rejected your vote
- `404 { "error": "..." }` — the requestId is unknown (already resolved, never existed, or session torn down)
- `500 { "code": "cancel_sentinel_collision", ... }` — F3: the agent's `allowedOptionIds` contains the reserved sentinel `'__cancelled__'`; agent / daemon contract violation
- `501 { "code": "permission_policy_not_implemented", "policy": "<name>" }` — F3 forward-compat: a policy literal landed in the schema but its mediator branch isn't built yet (currently unreachable; reserved for future policies)

After a successful vote, every connected client sees `permission_resolved` with the same `requestId` and the chosen `outcome`. Under `consensus`, intermediate votes additionally fan out `permission_partial_vote` until quorum.

### Auth device-flow routes (issue #4175 PR 21)

The daemon brokers an OAuth 2.0 Device Authorization Grant (RFC 8628) so a remote SDK client can trigger a login whose tokens land on the **daemon** filesystem — not on the client. The daemon polls the IdP itself; the client's only job is to display the verification URL + user code and (optionally) subscribe to SSE for completion events.

Capability tag: `auth_device_flow` (always advertised). Supported providers in
v1: `qwen-oauth`.

> [!note]
>
> Qwen OAuth free tier was discontinued on 2026-04-15. Treat `qwen-oauth` as the
> legacy v1 provider identifier in this protocol; new clients should prefer a
> currently supported auth provider when one is available.

**Runtime locality.** The daemon never spawns a browser — even if it can. The client decides whether to call `open(verificationUri)` locally; on a headless pod (the canonical Mode B deployment) the user opens the URL on whatever device they have a browser on. See `docs/users/qwen-serve.md` for the recommended UX.

**No token leakage in events.** `auth_device_flow_started` carries `{deviceFlowId, providerId, expiresAt}` only. The user code and verification URL come back point-to-point in the POST 201 body and via `GET /workspace/auth/device-flow/:id`; they are never broadcast on SSE.

**Per-provider singleton.** A second `POST` for the same provider while a flow is pending is an idempotent take-over — it returns the existing entry with `attached: true` rather than starting a fresh IdP request.

#### `POST /workspace/auth/device-flow`

Strict mutation gate: token-less trusted-loopback primary requests pass. A token-less primary request that reaches the gate without trusted-loopback authority receives `401 token_required`; missing or invalid configured credentials and unpaired Local Control credentials are rejected earlier with plain `401 Unauthorized`.

Request:

```json
{ "providerId": "qwen-oauth" }
```

Response (`201` fresh start, `200` idempotent take-over):

```json
{
  "deviceFlowId": "fa07c61b-…",
  "providerId": "qwen-oauth",
  "status": "pending",
  "userCode": "USER-1",
  "verificationUri": "https://chat.qwen.ai/api/v1/oauth2/device",
  "verificationUriComplete": "https://chat.qwen.ai/api/v1/oauth2/device?user_code=USER-1",
  "expiresAt": 1700000600000,
  "intervalMs": 5000,
  "attached": false
}
```

Errors:

- `400 unsupported_provider` — unknown `providerId` (response includes `supportedProviders`)
- `409 too_many_active_flows` — workspace cap (4) reached; cancel one with `DELETE`
- `401 token_required` — strict gate denied a token-less primary request without trusted-loopback authority
- `502 upstream_error` — IdP returned an unexpected error

#### `GET /workspace/auth/device-flow/:id`

Read the current state. Pending entries echo `userCode/verificationUri/expiresAt/intervalMs`; terminal entries (5-min grace) drop them and surface `status` + optional `errorKind/hint`.

Returns `404 device_flow_not_found` for unknown ids and post-grace evicted entries.

#### `DELETE /workspace/auth/device-flow/:id`

Idempotent cancel:

- pending entry → `204` + emit `auth_device_flow_cancelled`
- terminal entry → `204` no-op (no event re-emit)
- unknown id → `404`

#### `GET /workspace/auth/status`

Snapshot of pending flows + supported providers:

```json
{
  "v": 1,
  "workspaceCwd": "/work/bound",
  "providers": [],
  "pendingDeviceFlows": [
    {
      "deviceFlowId": "fa07c61b-…",
      "providerId": "qwen-oauth",
      "expiresAt": 1700000600000
    }
  ],
  "supportedDeviceFlowProviders": ["qwen-oauth"]
}
```

#### Device-flow SSE events

Five typed events (workspace-scoped, fanned out to every active session bus):

- `auth_device_flow_started` `{deviceFlowId, providerId, expiresAt}` — POST succeeded; SDK should subscribe (no userCode here, fetch via GET if needed)
- `auth_device_flow_throttled` `{deviceFlowId, intervalMs}` — daemon honored upstream `slow_down`; clients polling GET should bump their interval to match
- `auth_device_flow_authorized` `{deviceFlowId, providerId, expiresAt?, accountAlias?}` — credentials persisted; `accountAlias` is a non-PII label (never email/phone)
- `auth_device_flow_failed` `{deviceFlowId, errorKind, hint?}` — terminal; `errorKind` is one of `expired_token | access_denied | invalid_grant | upstream_error | persist_failed`. `persist_failed` is daemon-internal: the IdP exchange succeeded but the daemon couldn't durably store credentials (EACCES / EROFS / ENOSPC). The user should retry once the underlying disk condition is fixed.
- `auth_device_flow_cancelled` `{deviceFlowId}` — DELETE succeeded against a pending entry

> **Not MCP-compatible.** The MCP authorization spec (2025-06-18) mandates OAuth 2.1 + PKCE auth-code with a redirect callback, which doesn't work for headless-pod daemons. Mode B's device-flow surface is daemon-private — clients targeting MCP-compliant servers should use a different auth path.

### Session attachment chunk upload

Capability: `session_attachment_chunk_upload`. All four routes below are mutations owned by the live session's runtime. They use the existing bearer and optional `X-Qwen-Client-Id` headers. An upload belongs to its creating session store and exact client identity; an omitted identity is distinct from a named client.

Let `U = /session/:id/attachment-uploads`:

| Request                            | Body                                              | Response                                                                        |
| ---------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------- |
| `POST U`                           | JSON `{ name, mimeType, size }`, at most 4 KiB    | 201 `{ uploadId }`                                                              |
| `POST U/:uploadId/chunks?offset=N` | Raw `application/octet-stream`, at most 512 KiB   | 200 `{ offset }`, next byte offset                                              |
| `POST U/:uploadId/complete`        | No body needed; a JSON body is parsed and ignored | 200 existing attachment reference `{ type, attachmentId, mimeType, size }`      |
| `DELETE U/:uploadId`               | Empty                                             | 204 for receiving, failed, or absent uploads; 409 while completing or completed |

The maximum attachment is still 8 MiB. Metadata follows the existing filename/MIME rules; empty image files are invalid. Send sequential 512 KiB chunks, with an exact remainder for the last chunk. Only the immediately preceding chunk may be retried with identical offset, length, and bytes. Gaps, overlaps, changed retries, or premature completion return 409; malformed input returns 400, oversized input 413, and unsupported chunk media type or JSON charset returns 415 `invalid_attachment_upload_content_type`. Unsupported request encodings on create and chunk requests return 415 `invalid_attachment_upload_encoding`. Unknown, expired, or differently owned upload IDs return 404 on the three POST routes; DELETE returns 204 for absent uploads and 409 during or after completion. Create and chunk requests do not accept compressed bodies. The complete route uses the global JSON parser, so a valid compressed JSON body may be ignored, while malformed JSON is rejected before completion.

Completion validates session/client/runtime ownership before and after storage. Concurrent or repeated completion returns the same result without another write. A concurrent session copy temporarily returns `503 attachment_upload_store_busy` with `retryable: true`; the staged bytes remain available for completion with the same upload ID after copying ends. Actual storage failures remain terminal for that upload ID. Receiving uploads expire five minutes after creation. Success/failure receipts expire after five minutes and are capped at 1024 per daemon process. Session close/delete and final client deregistration invalidate unfinished uploads. Only completed attachments can be read, referenced or copied. A DELETE cannot roll back an already finalizing or completed attachment; a completed file can remain after a lost response.

Staging is bounded at 128 MiB and 32 active uploads across all runtimes in a process, with at most eight active uploads per session store. Exhaustion returns `429 attachment_upload_capacity_exceeded`; existing request rate limits also apply. This is a staging-buffer bound, not total process memory. Temporary IDs do not survive a daemon restart.

The TypeScript SDK automatically uses this protocol for attachments larger than 512 KiB when advertised, with two concurrent files per client and sequential chunks. It uses authenticated REST even with ACP transports, caches valid discovery for 60 seconds, and retains the existing raw endpoint for smaller files and older daemons. Append/completion retry at most twice after network errors, request timeouts or 502/503/504; rate-limit retries honor `Retry-After` within a five-minute operation deadline. Ambiguous creation and capacity failures are not retried. Failed uploads get one independent two-second cleanup attempt. `DaemonAttachmentUploadError` preserves the cause and `httpStatus`; its `status` omits upload-internal 404 so consumers do not mistake it for unsupported legacy attachment routing.

See the [complete design](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/session-attachment-chunk-upload.md>) for lifecycle and compatibility details.

## Streaming wire format

Events are emitted as standard EventSource frames. The daemon writes one `data:` line per frame (the JSON has no embedded newlines after `JSON.stringify`); the SDK parser at `packages/sdk-typescript/src/daemon/sse.ts` handles both that and the spec-allowed multi-`data:` form on the receive side.

## Error frames during streaming

If the bridge iterator throws while serving an SSE subscriber, the daemon emits a terminal `stream_error` frame (no `id`). The `data:` line is the full envelope (same shape as every other SSE frame in this doc); the actual error message lives under `envelope.data.error`:

```
event: stream_error
data: {"v":1,"type":"stream_error","data":{"error":"<message>"}}
```

The connection then closes.

## Environment variables

| Var                 | Purpose                                                        |
| ------------------- | -------------------------------------------------------------- |
| `QWEN_SERVER_TOKEN` | Bearer token. Stripped of leading/trailing whitespace at boot. |

## Source layout

| Path                                                 | Purpose                                                                                                    |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `packages/cli/src/commands/serve.ts`                 | yargs command + flag schema                                                                                |
| `packages/cli/src/serve/run-qwen-serve.ts`           | listener lifecycle + signal handling                                                                       |
| `packages/cli/src/serve/server.ts`                   | Express app assembly, middleware ordering, and remaining direct routes                                     |
| `packages/cli/src/serve/routes/*.ts`                 | Focused Express route groups, including session, SSE, workspace auth, workspace status, and file routes    |
| `packages/cli/src/serve/auth.ts`                     | bearer + Host allowlist + CORS deny                                                                        |
| `packages/cli/src/serve/acp-session-bridge.ts`       | CLI-local bridge compatibility facade for spawn-or-attach, per-session FIFO, and permission registry       |
| `packages/acp-bridge/src/status.ts`                  | read-only daemon status wire types + `ServeErrorKind` + `BridgeTimeoutError` + `mapDomainErrorToErrorKind` |
| `packages/cli/src/serve/env-snapshot.ts`             | pure helper that builds `/workspace/env` payloads from `process.*` state, including credential redaction   |
| `packages/acp-bridge/src/eventBus.ts`                | bounded async queue + replay ring                                                                          |
| `packages/sdk-typescript/src/daemon/DaemonClient.ts` | TS client                                                                                                  |
| `packages/sdk-typescript/src/daemon/sse.ts`          | EventSource frame parser                                                                                   |
| `integration-tests/cli/qwen-serve-routes.test.ts`    | 18 cases, no LLM                                                                                           |
| `integration-tests/cli/qwen-serve-streaming.test.ts` | 3 cases, real `qwen --acp` child backed by the local fake OpenAI server (POSIX only; skipped on Windows)   |


</details>
