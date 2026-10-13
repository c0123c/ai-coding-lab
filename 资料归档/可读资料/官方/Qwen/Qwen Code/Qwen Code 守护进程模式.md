# Qwen Code 守护进程模式

介绍 qwen serve 启动、认证、会话和事件流、网页或手机接入、渠道及防护配置；区分实验性能力与版本限制。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/QwenLM/qwen-code/commit/8505479d876040174872ecdce6882fcc090e1b09>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/qwen-serve.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/qwen-serve.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 守护进程模式（`qwen serve`）

将 Qwen Code 作为本地 HTTP 守护进程运行，以便多个客户端（IDE 插件、Web UI、CI 脚本、自定义 CLI）通过 HTTP + 服务器发送事件共享一个 agent 会话，而不是每个客户端生成自己的子进程。

> **🚧 v0.16-alpha**：`qwen serve` 首先以 v0.16-alpha 作为 ** 纯文本聊天/编码 ** 运送到 npm **仅本地部署**。当企业试点提交时，提示路径上的图像/文件附件、容器化部署（Docker / k8s / nginx 反向代理）以及远程/多守护程序强化将在后续补丁中进行。有关完整的延迟列表，请参阅 [v0.16-alpha 已知限制 ](<#v016-alpha-已知限制>)。

> **状态：**阶段1（实验）。协议表面被锁定在问题 [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>) 的 §04 路由表中。阶段 1.5（`qwen --serve` 标志 - TUI 共同托管相同的 HTTP 服务器）和阶段 2（进程内重构 + `mDNS`/OpenAPI/WebSocket/Prometheus 抛光）紧邻下游。
>
> **范围诚实：**阶段1的大小适合**开发人员根据协议表面**对客户端进行原型设计以及**本地单用户/小团队协作**。生产级多客户端/长时间运行/网络不稳定的工作负载（移动伴侣、IM 机器人达到 1000+ 聊天）需要阶段 1.5+ 保证，但此版本中没有。请参阅 [阶段 1.5+ 运行时保证 ](<#阶段-15-运行时保证>) 了解完整的差距列表，并参阅 #3803 了解收敛路线图。

## 它给你什么

- **内置 Web Shell UI** — `qwen serve` 在其根 (`http://127.0.0.1:4170/`) 开箱即用地提供基于浏览器的 Web Shell；运行 `qwen serve --open` 以自动在浏览器中启动它。它与 API 在同一源上提供服务，因此不需要第二个端口或反向代理。将 `--no-web` 传递给仅 API- 的守护进程。
- **最多一个主ACP子级，每个受信任的辅助级加一个按需子级，许多客户端** - 生产尝试预热主桥并在失败后首次使用时重试；受信任的辅助运行时按需启动自己的子运行时，而不受信任的辅助运行时从不启动子运行时。在默认 `sessionScope: 'single'` 下，面向同一工作区的客户端共享一个 ACP 会话，并在同一对话、文件差异和权限提示上进行协作。
- **重新连接安全流媒体** — SSE 和 `Last-Event-ID` 重新连接让客户端可以准确地从中断处（在环的重播窗口内）开始接起。
- **分页持久记录** — `GET /session/:id/transcript` 将完整的活动磁盘记录作为重播页面返回，无需附加客户端或更改实时 SSE 重播窗口。
- **急救人员权限** - 当agent请求运行工具的权限时，每个连接的客户端都会看到该请求；哪个客户先回答就获胜。
- **一个守护进程，一个或多个工作区** - 重复`--workspace`以在一个侦听器下注册隔离的工作区运行时。第一个工作区是主要工作区，并且对于省略 `cwd` 的请求仍然是默认工作区。
- ** 实验性守护进程管理的通道 ** — 以 `qwen serve --channel <name>` 开始，或者在没有通道的情况下开始，然后使用 `qwen channel set` 选择一个通道。工作线程是守护进程生命周期拥有的独立进程。可以查询、替换、重新加载和停止它们的选择，而无需重新启动守护程序。
- **远程运行时控制** - 更改会话的批准模式（`POST /session/:id/approval-mode`），切换每个工作区的工具（`POST /workspace/tools/:name/enable`）或加载的技能（`POST /workspace/skills/:name/enable`），搭建一个空的`QWEN.md`（`POST /workspace/init`，仅机械 - NOT 调用模型；对于 AI- 填充，后续使用 `POST /session/:id/prompt`），在运行时重新启动单个 MCP 服务器并进行预算预检查（`POST /workspace/mcp/:server/restart`），或在运行时重新启动 add/remove MCP 服务器守护进程重新启动（`POST /workspace/mcp/servers`、`DELETE /workspace/mcp/servers/:name`）。默认的无令牌环回侦听器可以使用所有这些；严格的工作空间突变还需要其他部署模式下的操作员凭据。
- **会话回顾**（[#4175](<https://github.com/QwenLM/qwen-code/issues/4175>)后续）-获取活动会话的一句话“我在哪里离开”摘要（`POST /session/:id/recap`）。将核心的 `generateSessionRecap` 包装为针对快速模型的侧查询；既不污染主聊天历史记录，也不污染 SSE 流。非严格门（与`/prompt`姿势相同）； SDK 助手`client.recapSession(sessionId)`。
  - **已知限制 - 令牌成本放大：** 路由是纯成本端点（每个调用都是 LLM 侧查询，没有状态优势），并且守护进程在 v1 中没有每条路由速率限制。在无令牌环回默认情况下，有错误或恶意的本地客户端可以向其发送垃圾邮件以销毁令牌。在公开守护进程之前，在共享开发主机上配置 `--token`（以及可选的 `--require-auth`）。
  - **并发回顾安全性：**同一会话上的两个同时 `/recap` 调用运行两个独立的侧面查询。 `generateSessionRecap` 通过 `GeminiClient.getChat().getHistory()` 读取聊天记录的快照，并将其提供给单独的 `BaseLlmClient.generateText` 调用（通过 `runSideQuery`）；它永远不会追加或改变会话的 `GeminiChat`。无需协调即可安全地从多个客户端进行呼叫。

## v0.16-alpha 已知限制

`qwen serve` (v0.16-alpha) 的第一个 npm 版本有意缩小范围——为在自己的机器上运行守护进程的开发人员提供纯文本聊天/编码。下面的列表使延迟表面变得明确，以便采用者可以围绕它进行计划；这里的所有内容都在 v0.16.x 补丁路线图或近期后续版本中。

**产品表面 — 纯文本：**

- ✅ 文本提示和文本响应（聊天、编码、工具调用、MCP 集成）
- ❌ **提示路径上的图片/文件附件** — `MessageEmitter`当前仅渲染文本；当提交具有图像需求的 alpha 目标时，多模态回波落地（#4175 chiga0 #27 P0 项目）
- ❌ **流式上传** — 与多模式相同的门控

**部署表面 — 仅限本地：**

- ✅ Loopback（`127.0.0.1`，默认）——无需身份验证，适合开发工作站
- ✅ 通过 `systemd` / `launchd` / `nohup &` / `tmux` 本地启动 — 请参阅 [本地启动模板](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/qwen-serve-deploy-local.md>)
- ✅ 通过 `QWEN_SERVER_TOKEN` env var 携带您自己的不记名令牌（[身份验证](<#认证>)用于设置）
- ❌ **容器化部署** - Docker / Compose / Kubernetes / nginx 反向代理，在 v0.16-alpha 中具有 TLS 终止 NOT。一旦提交了企业试点，就会遵循 v0.16.x（否则会因无人验证而失效）。
- ❌ **一台主机上的多守护进程协调** — 一个守护进程可以托管多个显式注册的工作区，但守护进程彼此之间不协调。跨主机联合、实例路径令牌密钥和过时令牌清理遵循 v0.16.x。
- ✅ **可撤销本地控制配对令牌** - `--local-control` 铸造一个由守护进程拥有的单独的 LAN 配对令牌。一般守护进程令牌存储仍然是BYO-令牌。

**强化——本地单用户的最低可行方案：**

- ✅ 启动时安全门（非环回绑定始终携带不记名令牌 - 操作员提供或在启动时生成；显式空源仍然拒绝启动，[PR 15 / #4236](<https://github.com/QwenLM/qwen-code/pull/4236>)）
- ✅ 突变路由验证门、会话范围的权限路由（Wave 4 PR）
- ✅ MCP护栏+多客户端权限协调（F2 / F3）
- ✅ **提示绝对截止日期 + SSE 写入器空闲超时 ** — 通过 `--prompt-deadline-ms` 和 `--writer-idle-timeout-ms` 选择加入；启用后通过 `prompt_absolute_deadline` 和 `writer_idle_timeout` 进行通告。
- ✅ **HTTP 速率限制 ** — 通过 `--rate-limit` 和每层阈值选择加入；启用后通过 `rate_limit` 进行通告。
- ⏸️ **Prometheus 指标 + 负载测试工具 ** — 当 30-50 活动会话成为真正目标时，遵循 v0.17 F4 阶段 1 规模仪器。
- ⏸️ **`--max-body-size` CLI 标志 ** — 守护进程默认强制执行 `express.json({ limit: '10mb' })`，它可以轻松地覆盖纯文本提示（模型上下文窗口位于字符的 10 MiB 下方）。可通过 v0.16.x 中的标志进行调整。

对于更深入的“我们不会在 Stage 1 中修复的内容”枚举（单主机会话状态突变模型 + N 个并行会话在每个工作区运行时共享一个 ACP 子级），请参阅下面的 [Stage 1 范围边界 ](<#stage-1-范围边界---我们不会在-stage-15-中修复的内容>)。

## 快速入门

### 远程 Web Shell 和电话访问

```bash
qwen serve --hostname 0.0.0.0
```

当 `--token` 和 `QWEN_SERVER_TOKEN` 均未提供时，非环回侦听器会生成 128 位临时承载（22 URL-安全字符）并在启动时打印一次。显式标志值优先于环境；在非环回绑定上，显式空值仍然无法关闭并拒绝启动，而在环回上，它会隐藏 `QWEN_SERVER_TOKEN` 并将绑定保留在 `--require-auth` 中描述的可信令牌较少模式中。默认环回行为未更改。

在通配符绑定上，启动会列出来自实际接口的无令牌 local/network URL，以及（对于生成的令牌，或对于交互式终端上您自己的令牌）一个标记为私有 LAN 地址的 ** 秘密承载 QR 代码 **。通配符枚举仅列出本地控制通告的相同 private/link-local LAN 群体（RFC 1918 / RFC 3927 IPv4，加上 fc00::/7 ULA 双栈绑定上的 IPv6）；将 VPN 适配器和容器桥排除在外的软件接口过滤器是 ** 名称启发式 ** ([#9158](<https://github.com/QwenLM/qwen-code/issues/9158>))，不是保证，因此 k8s 覆盖名称 (`cbr0`, `cali*`、`vxlan.*`）可以溜过去。相反，显式非环回绑定会准确打印您选择的地址并生成 QR，包括可路由的地址。在容器内部，枚举的地址是容器自己的接口，因此到达已发布的端口意味着替换主机地址； pty 分配的容器被视为交互式终端，因此其日志保持秘密状态。扫描QR以打开内置Web Shell，其承载在URL片段中。浏览器从 URL 中删除凭据并将其保留在每个选项卡 `sessionStorage` 中。或者，打开打印的 URL 并输入令牌；丢失或过期的凭据会显示重试表单。生成的令牌在重新启动时轮换。 `--no-web` 省略了 QR，并且 piped/redirected 标准输出永远不会在 QR 内重新发布稳定的操作员令牌 - 这种情况会打印一个名为 `--token-qr` 的抑制提示加上仅地址 QR（Web Shell 要求提供令牌）抵达时）改为；无论如何，在用户、系统或系统默认设置中传递 `--token-qr` 或设置 `serve.tokenQr` 来打印完整的令牌承载 QR； `--no-token-qr` 抑制在每个快速启动路径上运行的启动快速启动令牌 QR，包括交互式终端和生成的令牌（仍打印为自己的纯文本行）；它不管理本地控制配对 QR，`--local-control` 按设计打印。请求没有人可以打印的 QR — `--no-web`、未解析的 Web Shell 资产或环回绑定 — 在 stderr 上这样说，而不是默默地什么也不做。地址并不意味着防火墙或 NAT 可达性；不会自动发现公共 IP。

内置Web Shell的同源**HTTP**请求无需`--allow-origin`即可工作；其他浏览器来源仍然需要该允许列表，并且 `Origin: null` 仍然被拒绝。 WebSocket 支持的功能（终端、语音）和通过 TLS- 终止反向代理到达守护进程的浏览器仍然需要显式的 `--allow-origin <origin>` 条目 — 永远不信任转发标头来建立同源。任何重写 `Host` 标头的普通 HTTP 中介也是如此：nginx 的默认 `proxy_set_header Host $proxy_host` 和 k8s Ingress 都使守护进程看到不再与浏览器的 `Origin` 匹配的 `Host`。传递 `--allow-origin <origin>` 作为浏览器实际看到的源，或配置代理以逐字转发 `Host`。在 ** 非环回 ** 绑定上，仅端口转换 (`docker -p 8080:4170`) 不需要任何东西 — 同源检查将 `Origin` 与转发的 `Host` 进行比较，并且从不咨询侦听端口。在默认的 ** 环回 ** 上，它不会绑定：DNS- 重新绑定主机白名单仅接受守护程序自己的端口，因此对于包括 shell 文档在内的每个请求，端口转换隧道 (`ssh -L 8080:localhost:4170`) 都会被 `403 Invalid Host header` 拒绝，并且`--allow-origin` 无法覆盖它 — 转发相同端口 (`ssh -L 4170:localhost:4170`) 或绑定非环回。 **移动访问**配对QR是根据请求的方案和`Host`构建的——而不是来自转发的标头——因此在TLS-终止代理后面，它保留守护进程自己的`http`方案，并且Exchange 拒绝电话的 `https` 页面，因为邀请与其发出的来源绑定。在浏览守护程序的直接地址时进行配对，或者在手机上输入令牌。持有者授予完整的守护进程 API 权限，包括代码执行。将终端令牌和 QR 捕获视为机密。 HTTP未加密；现有的 `--tls-cert` 和 `--tls-key` 选项为 HTTPS 提供您自己的证书。

要从您自己的计算机上的 Web Shell 使用远程守护程序，请在连接屏幕或 ** 守护程序状态 ** 中输入远程守护程序的来源（例如 `https://dev-box.example:4170`）及其承载令牌。远程守护进程必须允许本地页面的来源，例如`qwen serve --hostname 0.0.0.0 --allow-origin http://localhost:4170`；终端的 WebSocket 也需要该条目。该令牌按选项卡和每个守护进程源保存，并且永远不会重新用于其他守护进程。侧边栏一次列出一个选定守护进程的工作区；切换守护进程会重新加载页面，并且不会合并本地和远程项目列表。使用现有的**添加工作区**表单在所选守护程序上注册绝对路径。除非您在连接屏幕上确认，否则不会连接 `?daemon=` 指定此浏览器之前未连接到的源的链接。无论是在独立还是嵌入式 Web Shell 中，跨源连接都不提供浏览器本地文件桥。 HTTPS 页面无法连接到 HTTP 守护程序，因此通过 HTTPS 外部受信任网络提供服务。

### 1。启动守护进程（环回，无身份验证）

```bash
cd your-project/
qwen serve
# → qwen serve listening on http://127.0.0.1:4170 (mode=http-bridge, workspace=/path/to/your-project)
# → qwen serve: trusted loopback mode; local callers have full API access without bearer authentication, including code execution as the daemon user. Use --require-auth with QWEN_SERVER_TOKEN on shared or untrusted hosts.
```

默认绑定是`127.0.0.1:4170`。承载身份验证为 **off**，并且主侦听器是受信任的，因此可以到达该端口的任何本地进程都可以使用完整操作符 API，包括作为守护程序用户执行代码。特定于路由的工作区信任、会话所有权、`X-Qwen-Client-Id`、权限、功能、验证和资源检查仍然适用。守护进程将当前工作目录注册为其主工作空间；使用绝对值 `--workspace /path/to/dir` 覆盖它，并重复该标志以注册其他隔离运行时。

对于仅 API- 的守护程序，禁用 Web Shell。会话、提示、工作空间、权限和SSE路由不变；绑定到 Web Shell 的表面随之而来 — 本地控制启用在每个平台上都无法关闭，并且 `/live/*` 路由（另外，在 macOS 上使用 `QWEN_SERVE_LIVE_NATIVE_HOST=1`、`/live/host` WebSocket）未注册：

```bash
qwen serve --no-web
```

**打开 Web Shell UI。** 浏览到 `http://127.0.0.1:4170/`（或使用 `qwen serve --open` 启动守护程序以自动启动它）以获得完整的浏览器终端 - 聊天、差异、提交历史记录、工具调用和权限提示。 UI 在与 API 相同的源的守护进程根上提供服务。本指南的其余部分使用原始 HTTP，因此您可以直接针对 API 编写脚本。

对于无需手动创建令牌的经过身份验证的单用户启动，请显式选择加入：

```bash
qwen serve --open-with-auth
```

这种仅环回模式会生成 256-bit 不记名令牌，当两者都不是时 `--token` 也不 `QWEN_SERVER_TOKEN` 提供一个，然后将其作为打开的 Web Shell `#token=` URL 片段。 shell 会删除该片段并将凭据保留在该选项卡中 `sessionStorage`;刷新有效，但关闭选项卡或重新启动守护程序会丢失凭据。在 CI, SSH，或其他自动打开不可用的环境，守护进程启动并打印片段 URL 用于手动打开。印刷的 URL 是有秘密的。

该标志默认关闭，包括浏览器打开行为，并且需要 Web Shell、构建的 Web Shell 资产和环回绑定。裸 `qwen serve --open` 在环回上保持无令牌。在authentiated-open模式下，正常的API路由会拒绝其他没有承载的本地客户端；静态 Web Shell 资产和环回 `/health` 保留其现有的预身份验证行为，除非还设置了 `--require-auth`。对于多个客户端或可重新打开的 Web Shell，请改用稳定的共享令牌：

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
qwen serve --open
```

### 2。健全性检查

```bash
curl http://127.0.0.1:4170/health
# → {"status":"ok"}

curl http://127.0.0.1:4170/capabilities
# → {"v":1,"mode":"http-bridge","features":["health","daemon_status","capabilities","session_create",...],"workspaceCwd":"/path/to/your-project"}

curl http://127.0.0.1:4170/daemon/status
# → {"v":1,"detail":"summary","status":"ok","runtime":{...}}
```

的 `workspaceCwd` 字段显示主要兼容性工作区，以便客户可以有意省略 `cwd` 上 `POST /session`。当前客户应从以下位置选择一个可信条目 `workspaces[]` 并发送该条目的 `cwd` 当明确定位运行时时。
的 `limits.maxPendingPromptsPerSession` 字段公布活动的每会话提示入场上限； `null` 表示上限已禁用。 `limits.maxTotalSessions` 宣传可选的守护进程范围的新鲜会话上限； `null` 意思是无限的。

### 工作空间注册容量

默认注册限制为256。设置`QWEN_SERVE_MAX_WORKSPACES=25 qwen serve --workspace /path/to/project`降低；接受的值为从 1 到 256 的十进制整数。无效值导致启动失败。这是守护进程启动设置：项目`.env`和项目`settings.env`无法更改它。嵌入式调用者可以设置`ServeOptions.maxRegisteredWorkspaces`，它优先于启动环境。

该限制包括主工作区、注册的辅助工作区和用户创建的临时工作区。内部对话运行时不受限制。动态注册按容量返回 `409 workspace_limit_reached`。 `GET /capabilities` 和 `GET /daemon/status` 公开了 `limits.maxRegisteredWorkspaces`。

默认注册容量为 256，标准守护进程默认为固定全局会话上限 800，包括仅使用一个工作区启动的守护进程。相同的会话默认值适用于任何高于 25 的已配置注册容量。 `--max-total-sessions` 仍然覆盖它； `0` 禁用它。当容量为 25 或更少时，现有的默认值仍然存在：一个启动或恢复的工作空间不受限制，并且有几个工作空间从每个工作空间的上限和计数中得出总数。注册更改不会重新计算总数。这不会更改 ACP 子内存策略或承诺 800 安全并发会话。

通道控制独立支持最多 25 个工作区所有者，公开为 `limits.maxChannelControlWorkspaces`。过渡还包括为恢复而保留的所有者；在容量最大的情况下，在添加新所有者之前成功停止旧所有者上的所有选定通道。过大的转换会返回 `409 channel_control_workspace_limit_reached`。 `--channel all` 继续仅选择主工作区。通道 SDK 超时不变。

持久注册文件保留架构版本 1 并支持 8 MiB 内最多 255 个辅助记录。原始记录（包括过时的别名）会消耗持久槽，直到被遗忘。如果完整的有效启动集超出配置的限制，启动将失败并保持文件不变。要降低容量，请使用以前的容量重新启动，忘记足够的注册，并在使用下限重新启动之前减少显式 `--workspace` 参数。在降级到较旧的二进制文件之前，请备份该文件并使用较新的二进制文件将其缩小到最多 24 个辅助记录和 256 KiB。否则，较旧的二进制文件会拒绝该存储并仅从显式工作区开始；他们的存储突变在写入之前失败。

### 调整会话实时状态轮询

默认情况下，Web Shell 每 **5 秒 ** 刷新工作区会话实时状态。要使用不同的时间间隔，请在启动守护程序之前设置环境变量：

```bash
QWEN_SESSION_LIVE_STATE_POLL_INTERVAL_MS=10000 qwen serve --web
```

整数形式，单位为毫秒，取值范围为`1000`～`2147483647`。缺失或无效的值会回退到 `5000`。该间隔适用于该守护进程服务的所有工作区；更改后重新启动守护程序并重新加载所有打开的 Web Shell 页面。本地会话操作和返回可见选项卡仍然可以立即刷新。这仅更改实时状态轮询，而不更改完整会话目录轮询。

守护进程将有效间隔发布为 `/capabilities` 中的 `sessionLiveStatePollIntervalMs`。 Web Shell 使用该值，并在连接到忽略该值的旧守护程序时回退到五秒。 SDK消费者可以在选择自己的轮询时间表时读取该值。

### 从守护进程运行通道

```bash
# Start one configured channel under qwen serve
qwen serve --channel telegram

# Start several configured channels under daemon-owned workspace workers
qwen serve --channel telegram --channel feishu

# Start all configured channels
qwen serve --channel all

# Or start a token-protected daemon with no channel worker
QWEN_SERVER_TOKEN=secret qwen serve

# Enable or replace its runtime selection later
qwen channel set telegram --token secret
qwen channel set telegram feishu --token secret
qwen channel set all --token secret

# Inspect or stop daemon-managed channels
qwen channel status --daemon-url http://127.0.0.1:4170 --token secret
qwen channel stop --daemon-url http://127.0.0.1:4170 --token secret
```

此模式是实验性的且由守护进程管理。它不会取代独立的 `qwen channel start` 命令：如果没有 `--daemon-url`，现有的 `qwen channel start`、`stop` 和 `status` 行为仍然是独立的。使用 `qwen serve --channel`，显式选择获胜，守护进程在侦听之前保留通道服务租约，如果初始工作线程无法准备好，则启动失败。在无标志启动时，守护进程会从每个受信任的已注册工作区恢复 `serve.channels`，因此配置为启动的非主工作区的通道会随守护进程一起返回，而不需要运行时 API 调用。如果两个源都没有选择通道，则繁重的通道运行时将保持卸载状态，并且不会保留通道服务租用，直到第一个运行时 PUT 或配置其自己的 `serve.channels` 的工作区注册为止。守护进程启动后注册的受信任工作区也会恢复其自己的 `serve.channels`，每个守护进程运行一次：稍后再次注册（在删除它之后或在重新配置其信任之后）不会重复恢复，因此您在其间停止的通道将保持停止状态。使用显式 `--channel` 启动守护进程会完全跳过此操作，使用 `qwen channel stop` (`DELETE /workspace/channel`) 停止通道也是如此，直到 `qwen channel set` 再次启用它们：注册工作区并不是重新打开托管的请求。与引导恢复不同的是，引导恢复会跳过无法托管的名称并保留其余名称，列表中包含此类名称的后期工作区不会取回其任何通道，并且守护程序日志会记录原因。注册工作区不会等待其通道出现。无法托管该通道的工作区所列出的通道名称将通过日志跳过，而使其他工作区的通道保持运行； `all` 仍然仅适用于主工作区。如果就绪工作线程稍后崩溃，守护进程将继续运行，在有界重启策略下重新启动它，并在 `GET /daemon/status` 中报告其状态（包括 `channel_worker_exited` 警告）。

自动恢复会在工作进程启动之前跳过无效的启动字段或验证或租用错误，记录其 `serve.channels` 源，并使不相关的设置保持有效。工作进程启动失败后，守护进程仅在清理成功后继续运行。全局运行时启动超时和未经确认的工作停止保留正常的启动失败行为；在工人终止合同未得到确认的情况下，服务租约仍将保留。检查守护程序日志中是否有跳过或失败的恢复。

存储的启动名称必须非空，没有前导或尾随空格，并且不包含不安全的控制或不可见字符。无效条目将被单独跳过并按数组索引记录。启动既不会将它们修剪为不同的实例名称，也不会重写设置。通道启动切换反映了持久配置，而运行时状态则报告通道是否正在运行。

运行时控件公开为 `GET`、`PUT` 和 `DELETE /workspace/channel`； SDK 帮助者是 `getChannelWorkerControl()`、`setChannelWorkerSelection()` 和 `stopChannelWorker()`。 PUT/DELETE/reload 使用严格的突变门：可信环回主要调用者可以在没有令牌的情况下使用它们，而其他部署则需要操作员凭据。运行时选择是故意短暂的：PUT 不会编辑设置或启动选项，并且重新启动将返回到显式 `qwen serve --channel` 选择，否则返回到受信任的主工作区的 `serve.channels`，或者在两者都不存在时禁用。显式 CLI 和运行时命名选择按首次出现顺序进行修剪和重复数据删除；顺序被保留，因为第一个通道会影响共享模型选择。

守护进程支持的 `qwen channel set` 和 `qwen channel reload`，加上 `status` 和 `stop` 以及 `--daemon-url`，无法发现 `--open-with-auth` 生成的令牌。使用 `QWEN_SERVER_TOKEN` 并将与 `--token` 相同的值传递给这些客户端，或省略身份验证打开模式。

当其工作进程启动时，守护进程会读取每个通道的设置（令牌、`proxy`、每个通道 `model`）。要重新读取设置而不更改提交的选择，请调用 `POST /workspace/channel/reload`（SDK、`client.reloadChannelWorker()` 或 `qwen channel reload`）。重新加载重新解析工作区所有权并通过相同的回滚安全协调路径重新启动选定的工作人员。只要连接运行时控制，就会出现 `channel_control` 功能； `channel_reload` 仅在启用管理器时出现。持久线程从磁盘恢复。

每个选定通道的 `cwd` 必须解析为已注册的工作区，并且通道按所属工作区分组：单工作区守护进程运行一个工作线程（与之前相同）；多工作空间守护进程（`--workspace` 重复）在每个拥有选定通道的工作空间运行一个工作线程，每个工作线程绑定到该工作空间的 cwd、`QWEN_DAEMON_WORKSPACE` 和 env 覆盖。要在非主工作区中托管通道，请在该工作区自己的 `.qwen/settings.json` 中定义它（不需要 `cwd`）或设置等于工作区路径的显式 `cwd`。仅在 user/system 范围内定义且没有 `cwd` 的通道在多个工作区中不明确：显式 `--channel` 选择会导致启动失败，而自动恢复会记录所有权错误并在工作进程启动之前跳过该选择。 `--channel all` 仅保留主通道（它托管主工作区的通道），并且不能与命名通道组合。

在停止任何操作之前替换选择的预检配置、所有权和信任。它使工作区工作人员的有序选择保持不变。如果更改后的工作程序无法启动，守护进程将停止新的工作程序并恢复旧的选择。如果守护进程即使在 SIGKILL 之后也无法确认旧子进程已退出，它将保留 PID 租约并拒绝创建重复的工作进程。当至少一个请求的适配器连接时，工作人员仍被视为准备就绪； PUT 然后返回 `partial: true`，`/daemon/status` 报告缺少适配器的 `channel_worker_partial_connect`。守护程序无法启动的 `serve.channels` 名称 - 其工作程序无法启动的已配置选择、名称引导无法归因，或者延迟注册的恢复被保留 - 在 `/daemon/status` 上报告为 `channel_restore_failed`，并在该工作区的频道列表中显示为 `error` 及其 `lastError`工作区自己的设置定义了它。名称保持非托管状态的其他方式仅在守护程序日志中。

当适配器拒绝 `connect()` 时，当前工作线程快照可能包括带有通道的 `startupFailures` 条目、`phase: "connect"`、可选适配器代码和凭据编辑消息。 `qwen channel set`、`qwen channel reload` 和远程 `qwen channel status --daemon-url …` 打印这些原因。如果每个适配器在动态设置或重新加载期间发生故障，则该命令会收到 `502 channel_worker_start_failed`；响应原因描述了该尝试，其 `state` 描述了回滚后的结果。失败的尝试不会被后续状态请求保留。每次工作启动时最多保留 64 个原因，并且适配器代码应被视为诊断类别而不是稳定类别。当没有适配器连接时，初始 `qwen serve --channel …` 启动仍会退出。

该守护进程还公开客户端 UI 和 
 操作符的只读运行时快照：`GET /daemon/status`、`GET /workspace/mcp`、
`GET /workspace/skills`、`GET /workspace/providers`、 `GET /workspace/env`、
`GET /workspace/preflight`、
`GET /workspace/:id/session-info`、
`GET /session/:id/status`、`GET /session/:id/context`、
`GET /session/:id/supported-commands`和
`GET /session/:id/tasks`、`GET /session/:id/lsp`、
`GET /session/:id/resources`、和
`GET /session/:id/transcript`。

`GET /workspace/:id/session-info`（和复数
`GET /workspaces/:workspace/session-info`孪生）返回工作区的聚合会话
计数：持久的`active` / `archived` / `total`，加上
当前内存中`live` 实时状态可用时进行计数。已注册的
不受信任的辅助工作区省略`live`，因为它们的目录读取不会
查询实时桥。分页的 `GET /workspace/:id/sessions` 列表 
 不包含总数，因此这是“存在多少个会话 
？”的专用表面。 - 当计划或重复任务离开大型本地存储时很有用。

> ⚠️ **磁盘扫描 — 不轮询。** 此端点会遍历工作区聊天目录下的本地会话 JSONL
>  文件。响应始终包含 
> `expensive: true` 和 `cost: "disk_scan"`。不经常调用它（手动
> 刷新、操作员工具、偶尔UI加载）——永远不要在紧张的计时器上或在每个侧边栏渲染上
> 。首选 `GET /workspace/:id/sessions` 浏览 
>  页面，首选 `GET /daemon/status` 进行实时内存会话计数。带有 `truncated: true` 的响应 
>  意味着扫描达到了安全限制，或者无法 
>  对每个候选文件进行分类，因此持久计数是下限。

```bash
curl http://127.0.0.1:4170/workspace/$(python3 -c "import urllib.parse,os; print(urllib.parse.quote(os.getcwd(), safe=''))")/session-info
# → {"active":450,"archived":30,"total":480,"live":2,"expensive":true,"cost":"disk_scan"}
```

`GET /session/:id/status` 返回单个会话的实时桥摘要：
`sessionId`、`workspaceCwd`、`createdAt`、可选 `displayName`、`clientCount`、
 和 `hasActivePrompt`。当守护进程使用该 id 持有 
 实时会话时，它会用摘要回答 `200`，否则 `404`（正文 `{ "error": …, "sessionId": … }`）
。使用它来轮询一个已知会话是否仍在运行
（`hasActivePrompt`）或连接了多少客户端（`clientCount`），而无需
获取和扫描整个分页会话列表：

```bash
curl http://127.0.0.1:4170/session/$SESSION_ID/status
# → {"sessionId":"…","workspaceCwd":"…","createdAt":"…","clientCount":1,"hasActivePrompt":false}
```

这是原始实时会话视图，因此 `clientCount` 和 `hasActivePrompt` 与 
 匹配 `GET /workspace/:id/sessions` 中的相应条目 - 但两条路由 
 字节不同。列表端点用持久的
会话存储数据丰富了每个项目：它的`createdAt`是持久的第一次提示时间，它
添加`updatedAt`加上从存储的标题或第一个
提示派生的`displayName`。 `/status` 而是报告实时会话自己的 `createdAt`，省略 
`updatedAt`，并仅在实时会话中设置时才返回 `displayName`。

`GET /session/:id/lsp` 返回结构化的每个会话 LSP 状态。使用 `--experimental-lsp` 启动 
 守护进程，以在生成的 agent 会话中启用 LSP；
，否则路由返回 `enabled: false`，没有服务器。

`GET /session/:id/resources` 从该实时会话自己的配置返回已清理的技能和 MCP 快照 
。当会话可能从 
 工作树或资源不同于基本 
 工作空间的另一个有效目录运行时，请使用它。此会话视图中省略了工作区拥有的 MCP 身份验证、池、预算和发现 
 错误详细信息。在调用之前检查 
`session_resources` 功能。

`GET /daemon/status` 是综合故障排除快照。默认的
`detail=summary`仅读取内存中的守护进程状态（会话、权限、
SSE/ACP传输计数、速率限制拒绝、进程内存、已解决的限制）
并且不启动ACP子进程。当您主动调查问题时，将 `GET /daemon/status?detail=full` 用于 
 每个会话诊断、ACP 连接详细信息、身份验证设备流计数和 
 工作区状态部分。

`GET /workspace/mcp`、`GET /workspace/skills` 和 `GET /workspace/providers`
 报告实时 ACP 运行时，并且在空闲时不启动 ACP 子级； 
空闲守护进程返回`initialized: false`并带有空快照。一旦 
 会话处于活动状态，它们就会切换到 `initialized: true` 并显示真实的 
 状态。

要按名称更新工作区技能设置，请致电 `POST /workspace/skills/:name/enable` 与 `{ "enabled": true | false }` 检查后 `workspace_skill_settings_toggle` 能力。要更改多个名称，请检查 `workspace_skill_settings_batch_toggle` 并打电话 `POST /workspace/skills/enable` 与 `{ "skillNames": ["review", "deploy"], "enabled": false }`;它一起处理每个结构上有效的名称，将所有生成的声明更改保留在最多一个锁定设置写入中，并刷新活动状态 ACP 当任何事情发生变化时，都会召开一次会议。这些路由写入工作区 `skills.disabled` 和 `skills.enabled` 无需查阅加载的技能目录，因此名称可能在安装前或扩展处于非活动状态时被禁用。启用会删除匹配的工作区禁用并记录显式 `skills.enabled` 选择加入，甚至在安装之前，因此此条目可以覆盖扩展的内部技能禁用。重复相同的声明是无操作的（`changed: false`）。他们的路径和请求体没有改变；特定于设置的功能取代了已停用的经目录验证的技能切换标签。该批次 `errors` 数组保留是为了线路兼容性，对于结构上有效的名称为空。一个硬 `skills.disabled` 从更高范围继承的条目对于有效可用性仍然具有权威性，但不会阻止工作区记录或删除其自己的声明。技能状态单元格暴露 `disabledReason` (`hard`, `default`, 或 `inactive_extension`）和一个可选的 `lockedScope`。不受信任的工作区写入仍会被拒绝。一个 `deferred` 响应意味着在活动检查时没有孩子活着，或者更改后的请求丢失了其 child/session 在所需的刷新期间；当 `changed` 为 true 时，持久声明在子进程启动时适用。声明是否实际更改由报告 `changed` （针对批量请求的每个结果）。 `skills.disabled` 与手动和模型不同，禁用手动和模型使用 `disable-model-invocation: true`，它保持直接 `/skill-name` 可用调用。对于 V2 延长批次，检查 `extension_batch_activation_v2`: `PUT /extensions/activation` 更改全局默认值，同时 `PUT /workspaces/:workspace/extensions/activation` 更改所选工作区的精确覆盖并接受 `"inherit"` 清除它们。两者都接受名称 `extensionNames`; `enabled` 和 `disabled` 可以在安装之前声明，同时 `inherit` 对于未知的名称是无操作的。每个请求都会返回一个操作以进行轮询。当 `extension_activation_explicit_refresh` 通告后，该操作在提交激活策略后完成，并且不会直接刷新活动会话。要立即应用它，请在激活成功后提交独立的主要或工作区限定的扩展刷新。如果没有显式刷新，其他工作区可以继续使用先前的激活直到下一个工作区 30-第二代调节器通道；失败的协调将由后续通道重试。

`GET /workspace/env` 和 `GET /workspace/preflight` 始终以 
`initialized: true` 应答，无论 ACP 状态如何。 `env` 从不查阅 ACP
（仅守护进程信息）； `preflight` 应答来自 
`process.*` 的守护进程级单元格，并在子进程空闲时发出 ACP- 级 
 单元格的 `status: 'not_started'` 占位符。

`GET /workspace/env` 报告守护进程的运行时、平台、沙箱、
代理，以及 **存在** （不是值）白名单秘密环境变量
比如 `OPENAI_API_KEY`。代理 URL 被剥夺凭据并减少
到 `host:port` 在他们撞到电线之前。该路线总是从
直接守护进程并且从不产生 ACP 孩子。

`GET /workspace/preflight` 返回准备情况检查列表。 **守护进程级
单元格**（节点版本，CLI条目，工作区目录，ripgrep，git，npm）
始终渲染。 **ACP-级别单元**（身份验证、MCP发现、技能、提供者、
工具注册表、出口）需要一个活动的ACP子级 - 当守护进程空闲时
它们发出`status: 'not_started'`占位符而不是生成 ACP 只是 
 来填充它们。故障映射到封闭的 `errorKind` 枚举（`missing_binary`、
`auth_env_error`、`init_timeout`、`restore_timeout`、`protocol_error`、`missing_file`、
`parse_error`、 `blocked_egress`）因此客户端 UI 可以呈现结构化的
修复。

该守护进程还公开工作区文件助手：

- `GET /file` 读取文本文件。完整快照响应返回原始字节
`sha256:<hex>` 哈希；上面文件中的有限行窗口 256 KiB 忽略它。
- `GET /file/bytes` 读取有界原始字节窗口并返回 base64 内容。
- `POST /file/write` 创建或替换文本文件。
- `POST /file/edit` 应用一种精确的文本替换。

Write/edit是**严格突变路线**：无令牌可信环回
主要侦听器授予操作员权限；不可信嵌入，没有 
 凭证返回 `token_required`。替换 
 和编辑需要完整快照 `GET /file`
（或全窗口 `GET /file/bytes`）中的最新 `expectedHash`。部分大文件窗口不能
用作乐观并发令牌。 `create` 永远不会覆盖。允许显式
写入被忽略的路径，但会经过审核。二进制写入，
delete/move/mkdir 和递归父创建不属于该表面的一部分。

### 3。打开一个会话

```bash
curl -X POST http://127.0.0.1:4170/session \
  -H 'Content-Type: application/json' \
  -d '{}'
# → {"sessionId":"<uuid>","workspaceCwd":"…","attached":false}
```

`cwd` 可以省略 - 路由回退到守护程序的主工作区。发布未规范化到任何已注册工作区的 `cwd` 将返回 `400 workspace_mismatch`。

为同一已解析工作区运行时发布到 `/session` 的第二个客户端在默认 `sessionScope: 'single'` 下获取 `"attached": true` — 他们现在正在共享该运行时的 agent 会话。省略 `cwd` 解析为主；选择另一个已注册的工作区会创建或附加到该运行时的单独默认会话。

### 4。订阅事件流（首先在另一个终端中）

```bash
SESSION_ID="<from step 3>"
curl -N http://127.0.0.1:4170/session/$SESSION_ID/events
# → id: 1
#   event: session_update
#   data: {"id":1,"v":1,"type":"session_update","data":{"sessionUpdate":"agent_message_chunk","content":{"type":"text","text":"…"}}}
```

`data:` 行是在单行上字符串化的 ** 完整事件包络 ** — `{id?, v, type, data, originatorClientId?}` — JSON-。 ACP 有效负载（本例中的 `sessionUpdate` 块）位于该信封内的 `data` 下方。 SSE-级别`id:`/`event:`线路方便EventSource客户；相同的值出现在 JSON 信封内，因此原始 `fetch` 消费者也能得到它们。

在 ** 发送提示之前打开此 ** — SSE 重播缓冲区保存 
 最后的 8000 事件，以便迟到的订阅者可以通过 `Last-Event-ID`、
 赶上，但对于简单的“观看单个提示”的情况下，最简单的方法是先订阅 
 并让它直播。

该流发出 `session_update`（LLM 块、工具调用、用法）、
`permission_request`（工具需要批准）、`permission_resolved`
（有人投票）、`model_switched`、`model_switch_failed` 和终端
帧`session_died`（agent孩子崩溃 - SSE然后关闭）和
`client_evicted`（你的队列溢出 - SSE然后关闭）。

### 5。发送提示（返回原始终端）

```bash
curl -X POST http://127.0.0.1:4170/session/$SESSION_ID/prompt \
  -H 'Content-Type: application/json' \
  -d '{"prompt":[{"type":"text","text":"What does src/main.ts do?"}]}'
# → {"stopReason":"end_turn"}
```

步骤 4 中的 `curl -N` 将在帧到达时打印帧。

### 可选的 Todo 停止防护装置

当
当前工作链成功写入顶级待办事项列表，然后停止
且项目仍处于待处理或正在进行中时，长时间运行的守护进程客户端可以选择进入有界延续。将其添加到`settings.json`并
重新启动守护进程：

```json
{
  "experimental": {
    "todoStopGuard": true
  },
  "tools": {
    "todoWrite": {
      "enabled": true
    }
  }
}
```

防护程序最多添加两个连续的主要模型调用，无需新用户
输入。中途用户消息首先运行并启动新的两次尝试阶段；
retry/continue 和相关后台结果保留当前阶段的 
 预算。每次调用和最终耗尽状态都显示为带有 `_meta.source: "todo_stop_guard"` 的可重播 
`session_update` 事件；元数据
包括尝试和未完成的计数，但不包括待办事项文本。排队的完整 
 提示也会首先运行，并且现有的 permission/cancellation 规则 
 不变。

当武装链等待相关后台工作时，不相关的 cron/loop
 火灾和旧任务通知将被推迟。重复工作是有限的，并且
每个任务都会合并，直到链产生。

该选项默认为`false`，需要重新启动，并在安全
模式、裸模式和批准`plan`模式下强制关闭。它仅位于内存中：从磁盘加载 Todo
state 或重新启动守护进程不会对其进行武装。在settings.json中设置
`tools.todoWrite.enabled`并重新启动Qwen Code，则新的
普通提示必须成功运行其自己的顶级`todo_write`;
retry/continue和live
客户端重新附加保持当前内存中工作链。成功更改 
 会话工作目录会将其清除，以便旧的待办事项无法在新的 
 工作空间中恢复。

## 认证

对于环回之外的任何内容，每个 API 请求都需要一个不记名令牌。为可重用的客户端提供一个稳定的令牌，或者在没有稳定令牌的情况下启动并使用守护进程生成和打印的临时令牌：

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
qwen serve --hostname 0.0.0.0 --port 4170
# → uses QWEN_SERVER_TOKEN; generates an ephemeral token if neither token source is supplied
```

然后，客户端在正常的 API 请求上发送 `Authorization: Bearer <token>` — 当您导出一个请求时发送 `$QWEN_SERVER_TOKEN`，或者当守护进程生成它时在启动时打印的承载（该路径永远不会设置环境变量，因此从启动输出中复制该值）。 `/health` 仅在普通环回绑定 ** 上被豁免 **，因此 Pod 内的 k8s/Compose 活动探针不需要凭据。在非环回绑定（`--hostname 0.0.0.0` 等）上，`/health` 需要具有其他正常 API 路由的令牌 - 否则攻击者可以探测任意地址以确认守护进程的存在。通道 webhook 入口在每种模式下都是独立的，并使用其配置的 `x-qwen-webhook-secret` 而不是守护进程承载。使用 `/capabilities` 验证您配置的令牌端到端正确：

> **强化环回（`--require-auth`）。**默认环回无令牌行为对于单用户笔记本电脑来说很好，但在共享开发主机、CI运行器或多租户工作站上不安全，任何本地用户都可以`curl 127.0.0.1:4170`。传递 `--require-auth` 以使不记名令牌在每个正常的 API 路由上强制 - 包括 `/health` 和 `/capabilities` - 即使绑定到 `127.0.0.1` 时也是如此。通道 webhook 入口仍然由 `x-qwen-webhook-secret` 独立验证。 Web Shell 文档和资产路由保持预授权；使用`--no-web`删除它们。当没有令牌源解析时，启动失败，并且在环回时，这意味着 `--token`、`QWEN_SERVER_TOKEN` 或 `--open-with-auth` — 最后一个在启动前安装自己生成的令牌，因此 `--require-auth --open-with-auth` 启动。 `--hostname` 拼写上的生成键：环回拼写（`127.0.0.0/8`、`localhost`、`::1`、`[::1]`）永远不会生成，而任何其他拼写（包括恰好解析为环回的 DNS 名称）会生成临时承载满足 `--require-auth` 并在启动时打印（当套接字绑定环回时仅使用令牌），每次重新启动时轮换，因此为可重用客户端固定 `--token` 或 `QWEN_SERVER_TOKEN`。启用该标志后，**未经身份验证的**客户端无法读取`/capabilities`来发现需要身份验证；发现表面是401响应体本身。经过身份验证后，`caps.features.require_auth` 标记是部署已强化的身份验证后确认（对于审核/合规性 UI 很有用）：
>
> ```bash
> qwen 服务 --require-auth --token "$(openssl rand -hex 32)"
> # → /health、/capabilities、/session、……都需要授权：持有者……
> 卷曲 http://127.0.0.1:4170/health
> # → 401
> curl -H "授权：承载 $TOKEN" http://127.0.0.1:4170/capabilities | jq '.features | 索引("require_auth")'
> # → 13（或任何索引 - 身份验证后非空意味着标签存在）
> ```

```bash
# Paste the bearer the daemon printed at startup, or your own QWEN_SERVER_TOKEN.
TOKEN='<printed bearer>'
curl -H "Authorization: Bearer $TOKEN" http://your-host:4170/capabilities
# → {"v":1,"mode":"http-bridge","features":[...],"modelServices":[],"workspaceCwd":"/path/to/your-project"}
# Wrong token → 401
```

令牌比较是恒定时间的（SHA-256 + `crypto.timingSafeEqual`）； 401 响应在“缺少标头”、“错误方案”和“错误令牌”方面是统一的，因此侧通道无法区分。

`--open-with-auth` 是 CLI- 拥有的便利，而不是另一个守护进程令牌源：定义该选项时（即使为空），它选择 `--token`，否则选择 `QWEN_SERVER_TOKEN`，然后修剪所选值并仅在结果为空时生成。守护进程不会保留生成的值或将其导出为 `QWEN_SERVER_TOKEN`；现有的内部经过身份验证的子切换保持不变。 Web Shell 仅将其浏览器副本存储在接收选项卡的 `sessionStorage` 中；此模式不添加交叉表或外部客户端凭证发现机制。该令牌不可独立撤销或与客户身份绑定。拥有权授予与任何其他不记名令牌相同的守护进程权限。请参阅 [ 经过身份验证的 Web Shell 启动设计 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/2026-08-22-serve-open-with-auth.md>) 以及 [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>) 中的相关未来工作。

## HTTPS / TLS（用于移动/跨设备访问）

默认情况下，守护进程提供普通的 HTTP。这在 `localhost` 上没问题，但是手机或平板电脑击中 LAN IP (`https://192.168.x.x:4170`) 是 ** 而不是 ** 是 [ 安全上下文 ](<https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts>) `http://` — 因此浏览器会阻止 `getUserMedia`（语音输入）、WebRTC 和其他仅限安全上下文的 API。传递 `--tls-cert` + `--tls-key` 通过 HTTPS 提供 Web Shell 并解锁它们：

```bash
# 1. Install a local CA and trust it (one-time). The mobile device must
#    also trust this CA — mkcert prints where the root cert lives.
mkcert -install

# 2. Generate a cert for your machine's LAN IP. Add localhost / 127.0.0.1 to
#    the SANs too: with `--open`, the daemon rewrites the browser URL to
#    127.0.0.1, so a cert scoped to only the LAN IP would be rejected with
#    ERR_CERT_COMMON_NAME_INVALID. (mkcert names the output after all hosts.)
mkcert 192.168.1.100 localhost 127.0.0.1

# 3. Start the daemon over HTTPS. Non-loopback binds still require a token,
#    and the browser Origin must be allowed through CORS.
qwen serve \
  --hostname 0.0.0.0 \
  --token "$(openssl rand -hex 32)" \
  --tls-cert "./192.168.1.100+2.pem" \
  --tls-key "./192.168.1.100+2-key.pem" \
  --allow-origin "https://192.168.1.100:4170"
# → qwen serve listening on https://0.0.0.0:4170
```

注意事项：

- **两个标志或两者都没有** - 如果仅给出一个标志，则启动失败（没有密钥的证书无法启动 HTTPS 侦听器）。
- **TLS 与 auth** 正交 - HTTPS 加密传输；配置承载令牌后，它会在普通环回绑定上对除 `/health` 之外的正常 API 路由进行门控。也将 `--require-auth` 传递给门环回 `/health`。通道 webhook 入口始终使用自己的共享密钥。非环回绑定总是带有一个带或不带 TLS 的令牌——您传递的令牌，或者守护进程在启动时生成并打印的临时令牌。
- **范围仅限于TLS终止** - 没有自动生成，没有ACME/让我们加密。这是一个 LAN/dev 的便利；对于面向互联网的部署，在反向代理处终止 TLS（请参阅下面的威胁模型）。
- ** 通道工作人员通过 `https://`** 拨回守护进程 — 因此他们也需要信任服务证书。自签名证书（或带有自己根的全链）不需要任何东西：守护进程将其注入每个工作人员的`NODE_EXTRA_CA_CERTS`中。上面的 mkcert 流程是 **CA- 发布的 **，因此叶子无法单独锚定链 - 在以 `--channel` 启动之前在守护程序的启动环境中导出 `NODE_EXTRA_CA_CERTS="$(mkcert -CAROOT)/rootCA.pem"`。操作员设置值 _ 与守护程序证书合并 _，而不是替换。如果没有它，守护进程将启动绿色，而每个通道工作人员都会在 `UNABLE_TO_VERIFY_LEAF_SIGNATURE` 上循环重新启动；守护进程日志命名启动时的间隙。
- **IPv6 通配符绑定在该主机实际分配的环回上回拨** — `--hostname ::`（或`[::]`）绑定 IPv6 套接字。 （空的 `--hostname` 永远不会到达侦听器：引导在绑定之前将其作为操作员错误拒绝，因此未设置 `HOSTNAME` 的撰写文件会大声失败，而不是默默地通配符。）该套接字是双堆栈的（节点在其上引脚 `IPV6_V6ONLY=0`，因此 `net.ipv6.bindv6only` sysctl 不会更改它），并且两个环回通常到达它 - 但根本没有 IPv4 的主机只有 `::1`，而绑定 `::` 但在其环回上不携带 `::1` 的主机（例如 `net.ipv6.conf.lo.disable_ipv6=1`）只有 `127.0.0.1`。当主机分配时，工作人员将被发送到 `[::1]`，否则将被发送到 `127.0.0.1`。 IPv6 通配符绑定的服务证书应在其 SAN 中携带两个环回（`mkcert localhost 127.0.0.1 ::1` 涵盖它）；启动信任诊断会检查工作人员将拨打的确切 URL 并命名差距。 `--hostname 0.0.0.0` 不变，仍然需要 `127.0.0.1`。
- **旋转`--tls-cert`需要守护进程重新启动** - 守护进程提供它在启动时读取的字节，因此在它重新启动之前，重生的工作人员可以加载比守护进程呈现的更新的内容，并且他们的握手失败。

## 将 Web Shell 作为应用程序安装

在支持的浏览器上，打开守护程序的 Web Shell 并使用浏览器的
**安装应用程序** 或 **添加到主屏幕** 操作。安装的应用程序在独立窗口中打开
同一服务器，名称和图标为Qwen Code。在iOS、
上使用浏览器的共享菜单和**添加到主屏幕**。可用性取决于
浏览器和设备。

守护进程本身提供 PWA 元数据：`GET /manifest.webmanifest` 和 
`GET /sw.js` 是预身份验证路由（浏览器在任何 
 授权标头存在之前获取两者），并且 `sw.js` 与 
`Service-Worker-Allowed: /` 一起提供，因此其范围涵盖整个源。工作器
仅缓存内容散列的Vite构建资产，缓存优先。未散列的公共 
 图标和清单通过 HTTP 重新验证。 API 获取、事件流 
 和携带授权标头的请求保留在网络上。

使用电话或其他远程设备的可信 HTTPS 连接； 
`localhost` 上的 HTTP 或环回地址支持本地开发。 LAN IP 上的 Plain
HTTP 不会启用服务工作线程。参见上面的TLS说明
；安装不会绕过证书或身份验证检查。

安装始终启动服务器根目录。当前地址中的令牌、会话链接或 
 工作空间永远不会包含在应用程序的启动 URL 中。
您可能需要在已安装的应用程序中再次输入令牌：它使用 
 现有的 Web Shell 身份验证流程，并且不会添加持久性凭证
存储。

服务器必须保持运行且可访问。在服务工作线程
激活并控制页面后，当
连接失败时打开根或会话链接会显示重试页面。服务工作线程缓存中不会保存任何对话、文件、API
响应或凭据。现有的HTTP
错误仍然来自服务器。第一步在应用程序关闭时不提供离线
工作、网络推送或通知。更新的工作人员
立即使用`skipWaiting()`和`clients.claim()`激活，替换
打开选项卡的先前工作人员，而无需强制页面重新加载。激活
会删除较旧的Qwen shell 缓存并保留不相关的缓存。

安装元数据使用内置的Qwen Code标识；运行时自定义
品牌仍然适用于 Web Shell 内部。嵌入式库使用者不会
注册工作线程。如需删除安装，请使用浏览器或运行
系统的应用管理；要删除其 Service Worker 和存储的站点数据，
使用浏览器的站点设置。

## Qwen 直播（实验）

Qwen Live 在每个平台上通过 Web Shell 运行：浏览器选项卡是麦克风和扬声器 (WS `/live/web`)，因此通过 `https` 或 `localhost` 打开 Web Shell 以访问麦克风。本机macOS`Qwen Live Host`应用程序默认关闭；要尝试一下，请使用 `QWEN_SERVE_LIVE_NATIVE_HOST=1` 启动 macOS 守护程序，该守护程序会在 Live 打开时恢复 `/live/host` WebSocket、`realtime_voice` 功能和主机下载。如果没有它，已安装的 `Qwen Live Host.app` 将无法连接（守护程序不会注册 `/live/host`）；退出它并使用 Web Shell。

## CLI 标志

|旗帜|默认|目的|
| --------------------------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--port <n>`                            | `4170`             |TCP 端口。 `0` = OS-分配的临时端口。|
| `--hostname <addr>`                     | `127.0.0.1`        |绑定接口。环回之外的任何事情都需要令牌；当 `--token` 和 `QWEN_SERVER_TOKEN` 都不提供时，会在启动时生成并打印一个临时令牌。|
| `--local-control`                       | `false`            |使用守护程序拥有的可撤销配对令牌、终端 QR 代码、确切的浏览器来源和尽力睡眠抑制在一个选定的私有 IPv4 接口上共享 Web Shell。由`--token`、`--allow-origin`、`--port 0`组成；与 `--no-web` 和非默认 `--hostname` 冲突。当有多个 LAN 候选可用时，请使用 `--local-control-address`，并为安全上下文浏览器 API（例如语音输入）添加 `--tls-cert` + `--tls-key`。|
| `--local-control-address <ip>`          | —                  |当主机有多个候选地址时，要共享哪个 LAN IPv4 地址。仅当 `--local-control` 报告不明确的选择时才需要。|
| `--token-qr`                            | —                  |即使捕获了标准输出（不是交互式终端）并且承载是运营商提供的（稳定）令牌，也打印承载令牌 QR，默认情况下会保留该令牌，以将稳定凭据保留在收集的日志之外。也可以通过 `serve.tokenQr` 设置进行设置，仅在用户、系统和系统默认范围内生效；旗帜通过后获胜。省略该标志是**而不是**，与`--no-token-qr`相同：省略保留默认策略（交互式终端或生成的令牌的QR），而`--no-token-qr`抑制在每个快速启动上运行的启动快速启动令牌QR路径，因此每次运行都可以否决持久设置。两者都不控制本地控制配对 QR。|
| `--token <str>`                         | —                  |不记名令牌。回落到 `QWEN_SERVER_TOKEN` 环境变量（leading/trailing 空格被剥离 - 对于 `$(cat token.txt)` 很方便）。与任何一个源的非环回绑定都会生成一个临时令牌。显式为空的值仍然拒绝在非环回绑定上启动；在环回时，它会隐藏环境变量并选择受信任的无令牌模式（或在`--require-auth`下完全拒绝）。|
| `--require-auth`                        | `false`            |即使在环回时，也拒绝在没有不记名令牌的情况下启动。强化共享开发主机/CI运行器/多租户工作站的`127.0.0.1`开发人员默认设置，任何本地用户都可以在其中监听。这种快速失败是仅环回的：环回仅使用其三个令牌源之一启动 - `--token`、`QWEN_SERVER_TOKEN` 或 `--open-with-auth`，它在启动前安装自己生成的令牌（生成是环回抑制的） - 而在非环回绑定上，生成的临时令牌满足标志。盖茨`/health`也在持票人后面。|
| `--enable-session-shell`                | `false`            |启用直接会话 shell 执行。对配置的承载令牌或可信环回模式有效；调用仍然需要有效的会话绑定 `X-Qwen-Client-Id`。|
| `--tls-cert <path>`                     | —                  |PEM 证书文件的路径。通过 **HTTPS** 而不是 HTTP 发球。必须与`--tls-key`配对（如果只给出一个则启动失败）。通过 LAN IP 解锁安全上下文浏览器 API - 语音输入 (`getUserMedia`)、WebRTC，否则浏览器会阻止普通 `http://`。 TLS 仅终止；无自动生成/ACME。请参阅下面的 [HTTPS / TLS](<#https--tls用于移动跨设备访问>)。|
| `--tls-key <path>`                      | —                  |PEM 私钥文件的路径。必须与`--tls-cert`配对。|
| `--max-sessions <n>`                    | `32`               |并发实时会话上限。当达到上限时，新的 `POST /session` 请求将生成一个新的子返回 `503`（带有 `Retry-After: 5`）；附加到现有会话的计数为 NOT。设置为 `0` 以禁用。适合单用户/小团队使用；如果您的部署具有 RAM/FD 余量（每个会话 ~30–50 MB），则提高它。|
| `--max-total-sessions <n>`              |`800` 或派生|在所有已注册工作区运行时创建新会话的可选非负整数守护程序范围上限。它适用于新的子会话、会话恢复和branch/fork-created会话；附加到现有的实时会话不会消耗插槽。设置为 `0` 表示无限制。当注册容量大于25时，默认为800（包括默认的256）。当容量为 25 或更少时，一个启动或恢复的工作空间是无限的；其中一些从每个工作空间的限制和计数中得出总数。以后的动态注册不会重新计算它。|
| `--max-pending-prompts-per-session <n>` | `5`                |`POST /session/:id/prompt` 接受但尚未解决的提示的每会话上限，包括排队提示和活动提示。桥接器在返回 `promptId` 之前与 `503`、`Retry-After: 5` 和 `code: "prompt_queue_full"` 同步拒绝溢出。设置为 `0` 以禁用。 `branchSession` 在相同的 FIFO 上序列化，但不计入此提示上限。|
| `--workspace <path>`                    | `process.cwd()`    |此守护程序注册的绝对工作区目录。重复该标志以在一个进程中托管多个工作区；第一个是主要的，当请求省略 `cwd` 时，它仍然是默认值。相对值被拒绝。规范 `cwd` 未注册的会话请求返回 `400 workspace_mismatch`。|
| `--memory-project-scope <mode>`         | `workspace`        |项目内存分区模式。 `workspace`（默认）通过精确注册的工作区目录来键控内存，以便每个守护程序工作区获得自己的独立内存； `git-root` 是解析到同一 Git 根的工作区共享的旧兼容模式。当提供时覆盖 `QWEN_CODE_MEMORY_PROJECT_SCOPE`；空白的 env 值被视为未设置，而无法识别的非空值将被忽略并出现一次性警告，并保留旧的 `git-root` 行为。新的默认设置不会迁移现有的 git-root 项目内存 - 使用显式的 `git-root` 范围在迁移期间读取这些条目。|
| `--channel <name\|all>`                 | —                  |实验性守护进程管理的通道工作线程。重复该标志以选择多个已配置的通道，或通过 `all` 启动每个已配置的通道。 `all` 不能与命名通道组合。选定的通道 `cwd` 值必须解析为已注册的工作区；多工作空间守护进程为每个拥有的工作空间运行一个工作线程。该工人归`qwen serve`所有；停止守护进程以停止服务管理的通道。|
| `--max-connections <n>`                 | `256`              |监听器级 TCP 连接上限 (`server.maxConnections`)。限制原始套接字计数，而不考虑会话计数 — 慢速/幻像 SSE 客户端一旦满了，就会在接受时被拒绝。如果您的部署预计每个会话有许多 SSE 订阅者，请与 `--max-sessions` 一起筹集。|
| `--memory-budget-mb <n>`                |50% 的 cgroup/host|整个守护进程树的总内存预算在 MB 中。未设置时，派生为 cgroup 限制或主机内存的 50%；无论哪种方式，有效值都以解析的可用内存为上限，并且会报告配置的数字和有效的数字。它驱动`--child-heap-mode`选择的固定ACP分区和自适应实时日志增长；这不是总的 RSS 限制：一个守护进程范围的增长池派生为有效预算的 5%（上限为 `1024` MB；在报告 `insufficientMemory` 的主机上，池为 0 并且自适应增长被禁用）由每个工作区桥共享 - 请参阅`--max-journal-bytes`。已解决的数字显示在 `GET /daemon/status` 中的 `limits.memory` 下，以及 `runtime.memory` 下的注册和活着的儿童数量以及每个儿童的顾问份额。主机太小，无法满足最低报告 `insufficientMemory` 而不是被向上夹紧；因为派生分数是 50%，所以 ~2 GB 下的任何主机都会触发此错误。在此类主机上传递显式 `--memory-budget-mb 1024` 以覆盖派生数字（该标志仍需要至少 1024 MB 可用内存来清除警告）。必须是 `[1024, 1048576]` 中的整数。|
| `--memory-pressure-mode <mode>`         | `observe`          |守护进程是否将其自己的内存读取转化为判决。 `observe`（默认）报告 `GET /daemon/status` 中 `runtime.memory.pressure` 下的压力水平，并提出 `daemon_memory_pressure` 问题 — `warning`，因此总体 `status` 离开 `ok` — 每当水平离开时`normal`。 `off` 仍然报告每个数字，包括级别，但没有提出任何问题，因此总体 `status` 没有变化；在校准时或在您收到顶级状态警报时使用它。该级别是两个比率中最差的一个：RSS 相对于可用内存（cgroup OOM 杀手监视的内容）和 V8 堆用于相对于该进程的堆上限。它仅涵盖守护进程根进程；为孩子们将其与 `runtime.memory.children.rssBytes` 进行比较。在这两种模式下都没有任何补救措施。 `off`、`observe` 之一。|
| `--child-heap-mode <mode>`              | `observe`          |`off`禁用该分区； `observe`报告`limits.memory.childHeap`而不改变生成； `admit` 仅强制模拟的并发子计数。实验性选择加入 `enforce` 还为每个受管理的 ACP 子级提供固定的 `perChildCeilingMb` 旧空间上限并报告 `limits.memory.enforced: true`。两种准入模式都可以在以`acp_child_capacity_exhausted`拒绝之前回收一个符合条件的空闲ACP；实时会话仍然受到保护。注册与儿童的容量无关。零槽位准入配置启动失败。修复了继承的 Node 堆标志在 `enforce` 中标准化；百分比标志冲突并被拒绝。天花板限制了旧空间，而不是RSS或MCP后代。启用前校准您的工作负载；零拒绝计数并不能证明堆安全。模式和预算更改需要重新启动守护进程；使用 `admit` 重新启动以回滚堆强制。|
| `--event-ring-size <n>`                 | `8000`             |每个会话 SSE 重播环深度（#3803 §02 目标）。使用 `Last-Event-ID: N` 设置 `GET /session/:id/events` 可用的积压。更大 = 更多的重新连接余量，但每次会话需要额外花费数百 KB RAM。 SDK 客户端还可以通过 `?maxQueued=N`（范围 `[16, 2048]`，默认值 256）请求特定订阅的更大每个订阅者积压上限。守护进程还在 75% 队列填充时发出非终端 `slow_client_warning` SSE 帧，以便客户端可以在被驱逐之前耗尽/重新连接。飞行前 `caps.features.slow_client_warning`。|
| `--compacted-replay-max-bytes <n>`      | `4194304`          |`POST /session/:id/load` 返回的有界快照中保留的重播事件的每个实时会话字节上限。该上限适用于`compactedReplay`；当前运行中的 `liveJournal` 分别受到 `--max-journal-events` 和 `--max-journal-bytes` 的上限（自适应增长可以提高的基线上限 - 请参阅 `--max-journal-bytes`）。值必须是正安全整数；无效值在启动时失败，硬上限为 256 MiB。当较旧的保留重播被删除时，快照以 `history_truncated` 开头。这不限制磁盘上的转录本。|
| `--max-journal-events <n>`              | `10000`            |当前未完成回合的飞行中 `liveJournal` 中保留的重播条目的每会话基线上限。连续的兼容文本或思想块共享一个条目，每个条目最多包含 256 个源事件；其他事件边界被保留。当超过时，守护进程首先尝试自适应增长（参见`--max-journal-bytes`）；如果未授予余量或授予未涵盖超调，则将删除最旧的条目并在前面添加 `history_truncated` 标记。标记的 `truncatedEvents` 和 `retainedEvents` 计数描述源事件。必须是正安全整数。固定此标志（或`--max-journal-bytes`）会禁用自适应增长。|
| `--max-journal-bytes <n>`               | `8388608`          |运行中 `liveJournal` 上的每会话基线字节上限，从序列化源事件中计算，即使兼容块共享重播条目也是如此。当轮次超出上限时，自适应增长会将会话的上限提高一倍（每个会话的硬上限为 256 MiB，受剩余池空间限制），而在守护程序的所有实时会话中授予的增长适合大小为守护程序有效内存预算的 5% 的一个共享增长池 —传递时的 `--memory-budget-mb` 值，上限为解析的可用内存，否则自动检测内存的 50%（参见 `--memory-budget-mb`） — 上限为 `1024` MB；在报告 `insufficientMemory` 的主机上，池为 0 并且禁用自适应增长。增长按需发生，并且只有在池允许的范围内；当它被拒绝时，池已耗尽，或者拨款不能覆盖超调，最旧的条目将被整个丢弃（至少一个条目始终保留），因此保留的尾部可以比上限小得多。固定此标志（或`--max-journal-events`）会禁用自适应增长。必须是正安全整数。默认为 8 MiB。|
| `--mcp-client-budget <n>`               | —                  |实时 MCP 客户端的正整数上限。当公布 `mcp_workspace_pool` 时，每个工作区运行时共享上限和传输；当标签不存在时，旧的每会话管理器会强制执行它。与`--mcp-budget-mode`结合。未设置时，不会有记账驱动的强制执行（但 `GET /workspace/mcp` 仍报告 `clientCount`）。与 claude-code 的 `MCP_SERVER_CONNECTION_BATCH_SIZE` 不同，后者控制启动并发而不是总的实时客户端。飞行前 `caps.features.mcp_guardrails` 和 `caps.features.mcp_workspace_pool`。|
| `--mcp-budget-mode <m>`                 | `warn` / `off`     |`--mcp-client-budget` 如何执行。 `warn`（预算设置时默认）：不拒绝，快照的`budgets[0].status`在预算≥75%时翻转到`warning`。 `enforce`：超过上限的连接被拒绝，每个服务器单元显示`disabledReason: 'budget'`，由`mcpServers`声明顺序确定。 `off`（预算未设置时的默认值）：纯粹的可观察性。 Boot 在没有预算的情况下拒绝 `enforce`。|
| `--external-tool-guard-mode <m>`        | `off`              |管理 ACP 外部预执行策略。 `off` 不进行提供商调用，也不宣传任何功能。 `required` 启动失败，除非兼容提供程序完成 v1 握手，然后每个受支持的顶级工具调用都会失败，除非允许其单个准备请求。|
| `--external-tool-guard-endpoint <url>`  | —                  |仅源环回 HTTP(S) 提供程序 URL 用于 `required` 模式，例如 `http://127.0.0.1:8787`。不接受路径、URL 凭据、重定向、非环回主机和代理路由。|
| `--external-tool-guard-timeout-ms <n>`  | `3000`             |整数`100..30000`；独立应用于启动握手和每个准备请求。|
| `--http-bridge`                         | `true`             |阶段 1 模式：生产尝试预热一个主要 `qwen --acp` 子项以实现兼容性，并在失败后首次使用时重试，而每个受信任的辅助项可以根据需要启动一个子项。通过 ACP `newSession()` 将运行时复用目标定位到其子级的会话；不受信任的辅助节点无法启动 ACP。阶段 2 本机进程内稍后将可用。|
| `--initialize-timeout-ms <n>`           | `10000`            |ACP 子请求超时，包括 `initialize` 握手（毫秒）。必须是 `2147483647` 以内的正整数。高于 JS 计时器上限 (`2^31-1`) 的值在启动时会被拒绝，因为 Node 会默默地将它们压缩到 1 毫秒。需要额外空间用于子启动的冷容器部署可能会引发这种情况；相同的值控制 `newSession`、工作区状态轮询和其他 ACP 外部方法截止日期。|
| `--session-restore-timeout-ms <n>`      | `60000`            |ACP 会话 load/resume 截止时间（以毫秒为单位）。必须是 `2147483647` 以内的正整数； `0`无效。如果省略，则默认值为 60 秒，当该值较大时，将提高到显式提供的 `--initialize-timeout-ms` ；较短的初始化超时绝不会降低恢复预算。 SDK 和 Web Shell 添加 10 和 15 秒的客户端空间。超时返回可重试`504 session_restore_timeout`；它并不意味着守护进程本身已退出。|
| `--allow-origin <pat>`                  | —                  |T2.4([#4514](<https://github.com/QwenLM/qwen-code/issues/4514>))。浏览器客户端的跨源白名单。可重复。每个值都是 `*`（任何来源 - 在没有配置承载令牌时，在环回上绑定启动拒绝；在非环回上绑定生成的临时令牌满足这些保护，因此拒绝仅环回；建议在环回上使用 `--require-auth`，因此 `/health` 也是承载门控的，因为它是在环回上进行预身份验证的默认情况下；Web Shell 静态资产在每种模式下都保持预身份验证，因此请传递 `--no-web` 来删除它们）或规范的 URL 来源（`<scheme>://<host>[:<port>]`，无尾部斜杠/路径/用户信息/查询）。如果没有令牌，HTTP(S) 源必须在环回绑定上进行环回；在非环回绑定生成的令牌满足相同的保护。远程 HTTP(S) 起源与每个 API 路由上的承载者进行身份验证；预身份验证例外仅是 Web Shell document/asset 路由、MCP 应用程序沙箱、通道 Webhook 入口（使用自己的 `x-qwen-webhook-secret` 进行身份验证）以及环回绑定上的 `/health`（在其他地方进行承载门控）。浏览器扩展在本地保留无令牌访问。故意不支持子域通配符 (`https://*.example.com`) — 显式列出每个子域，或使用 `*` 和已配置的令牌（以及 `--require-auth` 进行完全强化）。匹配的源接收 CORS 响应标头（`Access-Control-Allow-Origin`、`Vary: Origin`、方法、标头、最大年龄和公开的 `Retry-After`）；无与伦比的起源仍然得到一个403，其信封与今天的墙壁相同。 `Origin: null`（沙盒 iframe，文件：// 文档）始终被拒绝，即使在 `*` 下也是如此。飞行前通过 `caps.features.allow_origin`。环回自源命中不受影响。|
| `--web` / `--no-web`                    | `true`             |在守护程序根目录处提供构建的 Web Shell SPA（`GET /`、`/assets/*`、`/manifest.webmanifest`、`/sw.js` 和 `GET /session/<id>` 文档导航）。这些入口点在 ** 承载身份验证门之前注册 ** — 浏览器无法将令牌附加到 `<script>` 子资源或地址栏导航，并且 shell 不携带任何秘密。 API 请求遵循守护程序的正常权限策略：配置的令牌门正常 API 路由（环回 `/health` 除外），除非设置 `--require-auth`，通道 Webhook 入口始终使用其自己的共享密钥，并且无令牌可信环回主侦听器具有完全操作员访问权限。所有其他路径的 SPA 深层链接后备位于承载中间件后面。在非环回上绑定单行 stderr 警告，指出 UI 无需身份验证即可访问。将 `--no-web` 用于仅 API- 的守护进程。当构建忽略 Web Shell 资产时没有效果（守护进程记录面包屑并仅运行 API-）。|
| `--open`                                | `false`            |侦听器启动后，在默认浏览器中的守护进程 URL 中打开 Web Shell（当令牌解析（已配置或生成）时，`#token=` 会作为 URL 片段附加，因为片段永远不会发送到服务器，从而使令牌远离访问日志和 Referer 标头）。使用 `--no-web` 进行无操作，或者在无头/CI/SSH 环境中没有可用的浏览器。|
| `--open-with-auth`                      | `false`            |打开带有环回承载身份验证的 Web Shell。需要启用的 Web Shell 和构建的资产。重用已配置的令牌或生成进程生命周期 256 位承载并将其传递到 Web Shell 片段中。不适合浏览器的环境打印秘密承载手册 URL。其他客户端需要相同的显式配置的共享令牌。|

> **内存项目范围警告。**
>
> - **守护进程与独立进程 CLI.** 标志或守护进程启动环境设置
>   该守护进程拥有的每个运行时都有一个冻结范围。对于一个已注册的工作区，工作区 `.env`
>    或 `settings.env` 无法覆盖它。 A
>   独立`qwen`TUI仍然默认为git-root范围；要保持两个条目 
>    点一致，请在启动它们的 shell 或 
>    服务环境中导出 `QWEN_CODE_MEMORY_PROJECT_SCOPE`。
> - **目录名称冲突。**存储密钥由以下方式派生
>   `sanitizeCwd`，将每个非字母数字字符替换为 
>   `-`。仅标点符号不同的同级目录（例如
>   `feature_1`和`feature-1`）映射到相同的内存目录，甚至是`workspace`范围下的
>   。依赖工作空间
>   隔离时避免此类命名。
> - **标志和环境变量之间的标准化不同。**环境变量
>    被修剪并小写（`"  Workspace  "` 有效）； CLI 标志是 
>    通过 yargs `choices` 区分大小写匹配（`--memory-project-scope
Workspace` 被拒绝）。在两者之间复制时使用小写值。

### 内置守护进程 Git 重定位守卫

每个托管守护进程 ACP 会话对 
 模型 shell 命令应用内置的预执行保护，独立于 `--external-tool-guard-mode`，并且没有 
 任何功能通告。守护进程拥有绑定的工作空间和
会话的当前有效工作目录；两者均由受信任的 
 会话状态提供，并且从未从 ACP 子级接受。

防护程序会检查运行 shell 命令行的工具 - `run_shell_command`
 和 `monitor` - 并在其存储库位置在 
 会话的有效工作目录之外解析时在执行之前拒绝变异的 Git
 命令。对于文字 
 形式的 `git -C <path>`、`git --git-dir[=]<path>`、
`git --work-tree[=]<path>`、前导 
`GIT_DIR`/`GIT_WORK_TREE`/`GIT_COMMON_DIR`/`GIT_INDEX_FILE` 赋值，可识别重定位（也是
当通过`export`/`declare`/`readonly`制作时，将它们保留在链中每个后续命令的
环境中），
目录移动包装器标志（`env -C`， `sudo -D`），以及同一命令链中较早的 `cd`、`pushd` 或 
`popd` 内置命令。常见包装前缀
(`sh -c`、`bash -c`、`eval`、`sudo`、`nohup`、`timeout`、`exec`、 `command`、
`builtin`、
`env`、路径限定的 `git` 二进制文件和 `{ …; }` / `! …` shell 语法）是 
 展开的，因此相同的策略适用于内部 Git 调用，并且`$(…)`
或反引号替换体作为它们自己的命令进行分析。

固定到其自己的工作树的子agent包含在该工作树中，而不是
而不是会话的目录中； 
守护进程无法放置其执行目录的 shell 调用被拒绝。

相对目标从命令的有效起始目录解析
(`arguments.directory` 如果存在，否则会话当前有效
工作目录）在规范路径解析后，包括 `.git` git文件
重定向、符号链接和每个工作树的管理目录。一个搬迁的
执行前无法完全解析的目标——动态目标
(`$VAR`, 反引号, `~`，glob），尚不存在的路径，或
不可读的间接寻址——对于变异或不可分类的子命令被拒绝。
无论使用什么子命令，都将拒绝无法解析的重定位目标
是 - 包括只读的。重定位了子命令为 1 的命令
一个小的经过验证的只读集（`rev-parse`, `cat-file`) 仍然允许
一旦目标解析，除非命令携带命令执行 `-c`
配置，或者
它带有一个 `--output`, `--textconv`, 或 `--filters` 标志：那些写入文件
或运行目标存储库的已配置驱动程序。无法识别的命令
搬迁保留他们现有的行为。
否认是最终的，并报告给模型：
`Daemon shell guard denied a mutating Git command…` 对于一个解决的、动态的、
或无法解析的存储库位置，以及
`Daemon shell guard denied a shell command…` 当命令无法执行时
已解析，其有效负载无法解析，或者无法识别的程序可能会运行
重新定位的 Git 命令。

该防护对于以上面的文字形式
编写的Git重定位是可靠的——该控件存在的目标错误的命令——并且是
**尽力而为，而不是边界**，针对为击败它而编写的shell文本：
对静态读取器隐藏重定位的结构可能会通过，并且新的
将会不断被发现。不要授予守护进程对其 
 强度更广泛的信任。它不解释脚本文件，
跨命令跟踪环境变量值，或分析heredoc主体
（即使shell从不
执行它，也可以拒绝heredoc内的Git形状文本）。 `/fork` 和 agent 支持的工作区内存 remember/dream 在内置防护下保持 
 可用；它们仅在下面的
外部提供商模式处于活动状态时受到限制。可选的外部工具防护
仍然是附加策略，并且仅在
内置策略允许后才接收相同的请求。

### 所需的外部工具防护罩

此选择适用于在最终工具执行器边界需要外部 allow/deny
 决策的托管 ACP 部署。除非存在 
`--external-tool-guard-mode=required`，否则它是完全黑暗的：

```sh
export QWEN_CODE_EXTERNAL_TOOL_GUARD_TOKEN='replace-with-local-secret'

qwen serve \
  --external-tool-guard-mode=required \
  --external-tool-guard-endpoint=http://127.0.0.1:8787 \
  --external-tool-guard-timeout-ms=3000
```

提供者必须公开 `POST /v1/handshake` 和 `POST /v1/prepare`，要求 
`Authorization: Bearer <token>`，返回 JSON，回显提供的随机数或 
 请求 ID，并使用协议版本 `1`。令牌必须是非空白，最多为 
8192 UTF-16 个代码单元，并且不包含控制字符。请求仅限于
至1MiB，响应仅限于64KiB，可选拒绝原因至500UTF-16代码
单位，不受控制字符。成功的准备响应是：

```json
{ "protocolVersion": 1, "requestId": "<echo>", "allowed": true }
```

否认使用 `allowed:false` 并可以添加一个短 `reason`。对于每个支持的
通过现有权限的顶级工具调用 `PreToolUse`
门并到达最终执行边界， Qwen Code 发送一个准备
请求并且从不重试。较早的一个 permission/hook 拒绝发送不
准备请求。超时、取消、传输失败、格式错误或
不匹配的响应和明确的拒绝会阻止执行程序运行。
每一个都产生了 ACP 频道还必须确认它安装了所需的
回调；丢失或不兼容的确认会拒绝之前的通道
会话创建。
提供者请求携带 `sessionId`, `promptId`, `toolCallId`, 规范的
`toolName`，以及最后的 `arguments`; `toolCallId` 是相关标签，而不是
身份验证身份或独立幂等密钥。

最终参数可以包含敏感的应用程序数据。在
提供商日志和审计存储中将它们视为此类。

`PreToolUse` 挂钩在最终执行者决定之前运行。必需的Guard模式
不授权或沙箱hook行为；需要围绕每个可能的副作用建立边界 
 的部署必须禁用挂钩或单独管理其 
 实现。

斜杠命令操作也在 model/tool 调度之前运行，并且不是 Guard
 调用。一些内置程序可以直接更改文件或设置。需要全效果边界的托管 
 部署必须拒绝斜杠命令输入 
 或通过 `slashCommands.disabled` 或 
`--disabled-slash-commands` 禁用每个未经批准的命令。

v1 托管范围是由活动前台 
 托管提示调用的顶级工具。嵌套或委派 `agent`、`workflow`、
`create_sub_session`、`send_message`、直接 `/fork` 和 agent 支持的 
 工作区内存 remember/dream 控件是当所需模式
活动时被拒绝。顶级后台 shell 或监视器启动仍然是一个受保护的 
 调用，其最终参数到达提供者，但此功能不会 
 持续授权进程或添加进程完成审核 
 协议；需要前台完成的策略应该拒绝这些
形状。受保护的 MCP 调用还会在 
 传输错误后禁用自动 reconnect/replay。成功启动握手后，`/capabilities`
通告`external_tool_guard`；它的缺失意味着客户不得承担
强制执行。

此功能不授权显式守护进程 REST/ACP 管理调用；
 那些继续使用守护进程的现有身份验证和路由 
 合约。它还不会使允许的工具或 shell 命令
具有确定性或将其内部沙箱化；托管部署必须将
提供商决策与其正常工具策略和隔离边界结合起来。

> **调整负载旋钮的大小。** `--max-sessions` 是每个工作空间的新鲜会话上限。 `--max-total-sessions` 是守护进程范围的新鲜会话上限；省略时，默认为 800，默认注册容量为 256。
> 其他三个层也限制负载 - 在调整高并发 
>  部署的大小时，将它们一起调整：
>
> - **听众级别**：`--max-connections` / `server.maxConnections=256`
>   限制原始TCP连接（慢速客户端背压）。
> - **每会话订阅者**：EventBus 的订阅者上限为 SSE
默认情况下每个会话 >   64；第 65 个客户端获得终端 
>   `stream_error` 并关闭。
> - **每场次提示入场**：
>   `--max-pending-prompts-per-session=5` 边界已排队 + 活动提示
>    已接受一个会话。溢出得到 `503` 和 `Retry-After: 5`。
> - **守护进程范围的新鲜会话**：`--max-total-sessions=N`界限新鲜
>   跨守护进程创建会话。溢出得到与 `scope: "total"` 相同的 
>   `session_limit_exceeded` 形状。
> - **每个订阅者积压**：每个SSE客户端一个256帧队列；一个
>    容量过剩的客户端获得一个终端 `client_evicted` 框架，并且被 
>    关闭（一个缓慢的消费者无法固定守护进程）。
>
>  这些上限相互作用：每个运行时由 `--max-sessions` 限制，而 
> `--max-total-sessions` 限制其聚合。有效会话上限 
>  是任何有限守护进程范围上限和每个运行时聚合上限 
>  中的较低者（如果每个工作空间上限不受限制，则将该聚合视为无限制）。如果
> 两者都不是有限的——默认注册容量需要
> `--max-total-sessions 0`——则没有有限的会话上限。有限上限 × 64
> 订阅者 × 256 帧数是 EventBus
>  层最坏情况的动态内存；将其乘以 
> `--max-pending-prompts-per-session` 范围即可在 
>  准入层接受即时工作。默认大小调整假设单用户/小团队负载；逐步提高
> （并观察RSS）以进行更大规模的部署。

> **MCP 客户端护栏（问题 [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14）。** 声明 30 MCP 服务器的工作区`mcpServers` 将启动没有上游上限的 30 客户端，除非您设置了上限。 `--mcp-client-budget=N` 限制实时 MCP 客户端数量； `--mcp-budget-mode={enforce,warn,off}` 选择行为。设置预算时，默认值为 `warn`（快照显示警告，但没有客户端被拒绝 - 对于在强制实施之前测量现实世界的扇出很有用）。 `enforce` 模式下的拒绝服务器在其每个服务器单元上获取 `disabledReason: 'budget'`，并且 `budgets[0]` 单元显示 `status: 'error'` + `errorKind: 'budget_exhausted'`。插槽预留是按服务器名称进行的，并且在重新连接/发现超时后仍然存在 - 被拒绝的服务器无法从健康的服务器中获取插槽。
>
> **当前范围是功能驱动的。**当存在`mcp_workspace_pool`时，一个工作区运行时中的所有会话共享其MCP传输池和预算控制器； `GET /workspace/mcp` 发出 `scope: 'workspace'`。第二个工作区有独立的池和预算。当标签不存在时（包括`QWEN_SERVE_NO_MCP_POOL=1`），守护进程使用旧的每会话`McpClientManager`并发出`scope: 'session'`；在该回退中，N 个会话都可以消耗配置的上限。
>
> ```sh
> qwen 服务 --mcp-client-budget=10 --mcp-budget-mode=warn
> # 稍后，在遥测显示您的真实世界分布之后：
> qwen 服务 --mcp-client-budget=10 --mcp-budget-mode=enforce
> ```
>
> 这是 ** 而不是 ** 与 claude-code 的 `MCP_SERVER_CONNECTION_BATCH_SIZE` 相同（它控制启动并发）；它们是正交的。客户端必须在 `mcp_workspace_pool` 上分支，而不是单独假设协议版本的范围。
>
> **推送事件（问题 [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14b）。** SDK 客户端订阅`GET /session/:id/events` 在预算阈值交叉时接收键入的帧 - `mcp_budget_warning`（合成的，每次向上 75% 交叉时触发一次，并在 37.5% 处进行滞后重新准备，通过 `mcp_guardrail_events` 进行通告）和 `mcp_child_refused_batch`（每次发现合并一次）在 `enforce` 模式下通过；来自 `readResource` 延迟生成拒绝的长度 - 1。 `GET /workspace/mcp` 处的快照仍然是重新连接后状态的真实来源；事件是变化的边缘。在无需轮询的情况下实时显示仪表板时很有用。

## 默认部署威胁模型

- **仅环回** — `127.0.0.0/8`、`localhost` 或 `::1`；无需授权。
- **`localhost` 在监听 ** 后解析一次、固定并验证 — 如果其实际侦听器地址位于 `127.0.0.0/8` 或 `::1` 之外，则无令牌启动将关闭侦听器并失败。
- **`--hostname 0.0.0.0` 需要一个令牌 ** — 当没有提供令牌源时会生成一个令牌；显式为空的源仍然无法关闭。
- **`LOOPBACK_BINDS` 包括 IPv6** — `::1` 和 `[::1]` 计为无令牌规则的环回。
- **主机标头白名单** — **环回**上绑定守护程序检查`Host:`匹配`localhost:port` / `127.0.0.1:port` / `[::1]:port` / `host.docker.internal:port` 或确切绑定的环回地址和端口（根据 RFC 7230 §5.4 不区分大小写），以防止 DNS 重新绑定。当监听端口 80 或 443 时，相应的无端口形式也会被接受，因为浏览器忽略了方案默认端口。这支持完整的 IPv4 环回范围 (`127.0.0.0/8`)，而无需接纳不相关的主机。 **非环回绑定（`--hostname 0.0.0.0`）故意绕过主机白名单** - 运营商已选择表面区域，因此承载令牌门是正常API路由的唯一身份验证层；反向代理/SNI/客户端证书固定是操作员的责任，而不是守护程序的责任。如果您需要在非环回绑定上进行基于主机的隔离，请终止 TLS + 检查前端代理处的主机。
- **CORS 默认拒绝任何浏览器来源 ** — 返回 `403` JSON。通过 **`--allow-origin <pattern>`**（可重复，T2.4 #4514）来选择特定的浏览器来源。每个值都是文字 `*`（任何来源 — 在 ** 环回 ** 上，如果未配置承载令牌，则绑定启动会拒绝，而非环回绑定启动，因为令牌生成提供一个；建议环回上的 `--require-auth` 进行完全强化，因为 `/health` 在默认情况下环回 - 请注意，Web Shell 静态资源（`/`、`/assets/*`、`/manifest.webmanifest`、`/sw.js`、`/session/:id`、`/plugins`、`/channels`、`/live`、 `/scheduled-tasks`、`/goals` 和 `/settings` 文档导航）在每种模式下都安装在承载者之前，并且即使在 `--require-auth` 下也保持预身份验证，因此，当剩余浏览器表面很重要时，请使用 `--no-web`）或规范的 URL 来源（`<scheme>://<host>[:<port>]`，没有尾部斜杠/路径/用户信息）。在没有令牌的 **loopback** 绑定上，HTTP(S) 条目必须使用环回主机；非环回绑定总是携带一个令牌（提供或生成），正是因为远程托管的浏览器源可以通过操作员 API 作为守护程序用户执行代码。显式浏览器扩展源保留其无令牌本地自动化路径。匹配的源接收正确的 CORS 响应标头（`Access-Control-Allow-Origin: <echoed>`、`Vary: Origin`，加上标准方法/标头/max-age 和公开的 `Retry-After`）；不匹配的起源仍然会得到一个与默认墙具有相同信封的403。 `caps.features.allow_origin` 是有条件地公布的，因此 SDK / webui 客户端可以在发出它们之前预先检查守护进程是否支持跨源命中。示例：`qwen serve --allow-origin http://localhost:3000 --allow-origin http://localhost:5173`。环回自源命中（例如 Web Shell UI）不受影响 - 一个单独的 Origin-strip shim 会处理它们，无论 `--allow-origin` 如何。内置远程 Web Shell 的同源 **HTTP** 请求在没有 `--allow-origin` 的情况下也可以工作：在非环回主侦听器上，等于直接套接字方案的 `Origin` 加上规范化的 `Host` 权限经过承载身份验证，然后在 CORS 墙之前剥离，而其现有的公共静态路由仍未经过身份验证。两个排除仍然需要 `--allow-origin` - 转发的标头永远不会建立同源信任，因此 TLS- 终止反向代理的 `https` 浏览器源不是同源的（并且重写 `Host` 标头的普通 HTTP 中介也不是同源的，例如 nginx 的默认 `proxy_set_header Host $proxy_host` 或 k8s Ingress；对于浏览器看到的源，补救措施是 `--allow-origin <origin>`，或者逐字转发 `Host` 的代理 — 端口转换不需要任何东西，因为只将转发的 `Host` 与方案默认进行比较`:80`/`:443` 剥离），并且 WebSocket 升级路由（终端、语音）保留单独的 CSWSH 门，仅允许环回源、白名单源和本地控制侦听器自己的源。
- **Chrome 扩展程序浏览器自动化与框架分离。** `qwen serve --allow-origin chrome-extension://<id>` 让扩展程序框架 Web Shell 并连接到守护程序。 Console/network/screenshot/click 工具需要外部 CDP MCP 适配器命令：`QWEN_CDP_MCP_COMMAND=/path/to/cdp-mcp-adapter qwen serve --allow-origin chrome-extension://<id>`。主要的CLI包不捆绑浏览器自动化适配器；客户可以在提供这些可用工具之前检查 `caps.features.includes('browser_automation_mcp')`。
- **生成的`qwen --acp`子进程接收其所属运行时的有效环境。**守护进程冻结进程环境基础，将该工作区的settings/env-file覆盖应用到运行时本地快照，并且永远不会将覆盖写回到`process.env`；另一个运行时中的同名键不会交叉。 `QWEN_SERVER_TOKEN` 在生成之前被清除，因为 agent 不需要守护进程承载者。影响加载程序的变量（`NODE_OPTIONS`、`npm_config_node_options` 和 npm 的配置文件重定向、`NODE_PATH`、`OPENSSL_CONF`、`NODE_REPL_EXTERNAL_MODULE`、`npm_config_node_gyp`、 `npm_config_init_module`、`LD_PRELOAD`、`LD_AUDIT`、`DYLD_INSERT_LIBRARIES`、`BASH_ENV`、`ZDOTDIR`、导出的 bash 函数定义 `BASH_FUNC_*`）同样不会传递给会话子进程 — 守护进程从自己的子进程中清除它们`process.env` 以及会话托管子级生成的冻结基础环境（基础环境仅将它们保留在 `DEV=true` 线束下，其 `.ts` 条目仍然需要 tsx 加载程序），并且 `.env` / `settings.json` `env` 源拒绝它们（参见[设置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md>)）；这适用于守护进程托管的每个会话。 `OPENAI_API_KEY`、`ANTHROPIC_API_KEY`、`QWEN_*` 和 `DASHSCOPE_API_KEY` 等基本凭据否则会传递，除非运行时覆盖更改它们。 **这是故意的，而不是沙箱。** agent 与具有 shell 工具访问权限的相同 UID 运行，因此 `~/.bashrc`、`~/.aws/credentials` 或 `~/.npmrc` 中的任何内容都可以通过提示注入访问不管怎样。运行时之间的环境隔离不是操作系统安全边界；不要以您不信任 agent 的凭据的身份运行 `qwen serve`。
- **Agent 文本读取是子本地的，并遵循常规 CLI 权限规则，而不是工作区文件系统边界。** 直接 `read_file` 可以到达每个已注册工作区外部的主机文本路径：外部路径默认为确认，并且允许规则或批准模式可以自动批准它们。批准的读取使用可配置的 CLI 输出限制，而不是工作区文件系统的返回输出、完整快照和大文本扫描上限。这适用于每个共享文本读取消费者，因此由写入、编辑、笔记本、sed 和工件操作执行的预读以及工作区文件系统的读取审核、符号链接拒绝和读取端 TOCTOU 保护都会失去这些上限 - 有关确切列表，请参阅 [ 读取设计 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/daemon-local-text-reads.md>)。由于确认负载是通过读取文件构建的，因此在任何人批准之前，工作空间外的差异都会被扇出到 ** 每个 ** 连接的 SSE 订阅者 - 在交互式 CLI 中，内容只能由终端上的人员看到。将经过身份验证的守护程序客户端视为相同的安全主体。 HTTP 文件系统路由仍然在工作区范围内，并且 agent 发现工具行为保持不变。
- **来自内置文本工具的批准的最终写入具有狭窄的同主机路由。** `write_file`、`edit`、`notebook_edit` 和 shell 工具的模拟 sed 编辑器仅在现有权限策略允许执行后才附加内部来源。因此，他们的最终 ACP 文本写入可以定位所属工作区之外的绝对路径，而无需第二次确认；允许规则，AUTO/AUTO_EDIT 和 YOLO 的行为类似于 CLI，而拒绝、计划、Hook/Guard 拒绝和预执行取消不会发送最终写入。在工具已经进入不可取消的文件系统操作后取消，会保留该工具的现有行为。工作区目标仍然使用WFS。外部目标使用具有相同信任快照的守护进程主机编写器、5 MiB 编码限制、叶符号链接拒绝、规范路径锁定、原子重命名、模式保留、默认情况下 `0600` 新文件模式（可配置 — 请参阅 [ 新文件模式了解 agent 文本写](<#agent-文本写入的新文件模式>)），生成保护和文件系统审计。 HTTP 写入、通用或未标记的 ACP 写入、注入 bridge/workspace-registry/factory 集成和任意 shell 重定向不会收到此异常。参见[外部写入设计](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/daemon-external-tool-text-writes.md>)。
- **每个订阅者有界 SSE 队列 ** — 溢出其队列的慢速客户端获得 `client_evicted` 终端帧并关闭；一个被卡住的消费者无法固定守护进程。
- **每会话提示入场上限** — 默认为每个会话 5 接受但未解决的提示。有问题的客户端无法将无限制的提示承诺排入队列，或者临时 SSE 等待一个会话。
- ** 正常关闭 ** — SIGINT/SIGTERM 在关闭侦听器之前耗尽 agent 子级（每个子级 10 秒的截止时间）。

> ⚠️ **权限投票现在是会话范围的 - 加上常设共享承载警告。** 更喜欢 `POST /session/:id/permission/:requestId` (Wave 4 / F3)：它路由到拥有指定 live 的运行时会话并且永远不会回退到主桥 — 新的多工作区集成应始终使用此路由（飞行前 `caps.features.session_permission_vote`）。遗留进程全局 `POST /permission/:requestId` 仍然适用于 F3 之前的单工作空间客户端：它是主绑定的，并且 `404` 用于另一个注册运行时拥有的会话 - 与默认 `first-responder` 策略下的失败投票相同的主体 - 因此 `404` 本身并不意味着请求已得到答复。路由表：[守护进程RESTAPI参考](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md#permissions>)。长期警告：持有不记名令牌的每个客户都是相同的安全主体 - 不要在与不受信任方共享的不记名后面运行 `qwen serve`。
>
> ⚠️ **阶段 1 已知间隙 - `POST /session/:id/prompt` 主体上限为10 MB (BUy4L)。** 包含超过 10 MB 的图像/PDF/音频的多模式提示将在路由逻辑运行之前的正文解析时间失败（无流式传输，无上传中止）。解决方法：缩小客户端内容，或传递路径引用并让 agent 通过 `readTextFile` 读取文件。舞台 1.5 将接受 `multipart/form-data` 或 `/prompt` 上的分块编码，因此大的提示不会陷入悬崖。
>
> ⚠️ **舞台 1 已知间隙 - 后面的幻像 SSE 连接NAT.**
> 守护进程通过TCP心跳背压
> （15秒间隔）检测死亡客户端。消失的客户端 WITHOUT a TCP RST （例如，一个 
> NAT 盒子默默地丢弃空闲流）保持内核级套接字 
> “活动”，直到节点的 keepalive 探测超时 - 通常〜2 Linux 上的默认时间
> 。在此类 
> NAT 背后的 `--hostname 0.0.0.0` 部署中，幻影 SSE 连接可能会累积并最终达到 
> 256 `server.maxConnections` 上限。
>
> 设置 [`--writer-idle-timeout-ms <n>`](<#截止日期和写入器空闲超时>)
> （问题[#4514](<https://github.com/QwenLM/qwen-code/issues/4514>) T2.9)
> 通过明确的应用程序级空闲截止时间来缩小差距：
> 当守护进程 `n` ms 内没有写入成功刷新时发出 
>  带有 
> `reason: 'writer_idle_timeout'` 的终端 `client_evicted` 帧并关闭流。默认情况下，该标志处于 
>  关闭状态，以保留旧合约 - 
>  网络上吞下 RST 的运营商应选择远高于 15 秒 
>  心跳间隔的值（例如 `60000`–`300000`），这样才合法空闲
> 连接不会被驱逐，而真正卡住的写入器会
> 立即收获。从您的 SDK 进行飞行前 `caps.features.includes('writer_idle_timeout')`
>  以确认守护进程支持它。

### 截止日期和写入器空闲超时

问题 [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>) T2.9 提供了两个选择加入标志，可以弥补 15 秒心跳 + AbortSignal 未涵盖的长期运行/远程部署差距。此处还列出了共享权限响应超时。默认情况下，这三个选项均处于关闭状态。

|旗帜|环境变量|默认|它的作用|
| -------------------------------------- | ----------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--prompt-deadline-ms <n>`             | `QWEN_SERVE_PROMPT_DEADLINE_MS`     |未设置|单个 `POST /session/:id/prompt` 上的服务器端挂钟上限。到期时，守护进程中止提示的 AbortController 并返回 HTTP `504` 和 `{code:"prompt_deadline_exceeded", errorKind:"prompt_deadline_exceeded", deadlineMs:n}`。每个提示请求正文字段 `deadlineMs` 可以 SHORTEN 标志下方的有效截止日期，但永远不会延长它。能力标签（有条件）：`prompt_absolute_deadline`。|
| `--writer-idle-timeout-ms <n>`         | `QWEN_SERVE_WRITER_IDLE_TIMEOUT_MS` |未设置|每个SSE-连接空闲截止时间。当 `n` 毫秒内没有写入刷新 SUCCESSFULLY 时（既不是真实事件也不是 15 秒心跳），守护进程会发出带有 `data.reason = 'writer_idle_timeout'` 的终端 `client_evicted` 帧（镜像在 `data.errorKind` 上）并关闭流。 **选择一个高于 15 秒心跳的值**（例如`30000`–`300000`），这样合法的空闲流就不会被驱逐；值 `< 15000` WILL 在第一个心跳触发之前驱逐其他健康的空闲连接（仅适用于测试/短期开发会话）。能力标签（有条件）：`writer_idle_timeout`。|
| `--permission-response-timeout-ms <n>` | —                                   | `0`     |用于普通权限的共享挂钟和守护进程模式下的 `ask_user_question` 响应。 `0` 或省略标志无限期等待；正整数对两者都施加了最后期限。当计时器被禁用时，投票者取消、会话取消和守护程序关闭仍然可以解决待处理的交互。|

提示符和写入器标志接受以毫秒为单位的正整数； `0`、`NaN`、非整数或负值在启动时会被拒绝，并显示明确的错误消息。权限响应超时接受 `0` 或正整数。对于两个环境支持的截止日期，显式的 `ServeOptions` 字段胜过 env 值。 SDK 消费者应在依赖提示和编写者行为之前预先检查匹配能力标签。

### agent 文本写入的新文件模式

Agent 文本写入（`write_file`、`edit`、`notebook_edit` 和 shell 工具的模拟 sed 编辑器）通过守护程序的原子编写器发布，它保留现有目标的模式，并且 — 对于 ** 新的 ** 文件 —默认为仅所有者 `0600`，忽略守护进程的 umask。这种故障关闭默认值是有意为之的：无论主管 umask 多么宽松，新的 agent 创建的文件永远不会被意外读取 group/world。

部署约定是 umask 驱动的操作员（例如具有 `UMask=0002` 的 systemd 单元、共享组存储库）可以选择新文件到标准 POSIX 处理中：

|环境变量|价值观|默认|它的作用|
| -------------------------- | ------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `QWEN_SERVE_NEW_FILE_MODE` | `owner` \| `system` | `owner` |`system` 在 `0o666 & ~umask` 创建 NEW 文件，因此 agent 创建的文件像计算机上的任何其他进程一样遵循守护进程的 umask。 `owner` 保留与 umask 无关的 `0600` 默认值。值不区分大小写；文字 `0600` 被接受作为 `owner` 的别名（不支持其他八进制模式），并且任何其他值都会被拒绝并显示 stderr 警告，并且保留 `0600` 默认值。|

范围和限制：

- 适用于由文本写入路由（工作区目标、同一主机外部主机编写器和 HTTP 文本写入）创建的 NEW 文件。现有文件始终保持其磁盘模式 - 编辑 `0600` 机密会保留其 `0600`，可执行文件会保留 `+x`。
- 无论此设置如何，二进制上传 (`POST /file/upload`) 始终在 `0600` 处创建。
- 守护进程在工作区文件系统构建时读取变量；更改后重新启动守护进程。

### 会话附件存储

会话附件（Web Shell 通过 `POST /session/:id/attachments` 上传的文件和图像）默认存储在工作区的运行时临时目录下：`<runtimeBaseDir>/tmp/<projectHash>/attachments`，每个会话的密钥为 `session-<sessionId>`。希望附件保留在运行时临时目录之外（例如在专用卷上）的操作员可以覆盖根目录：

|环境变量|价值观|默认|它的作用|
| ------------------------------------- | ------ | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `QWEN_SERVE_SESSION_ATTACHMENTS_ROOT` |路径|未设置|在此目录下存储会话附件，而不是默认的运行时临时目录。接受绝对路径、相对于守护程序 cwd 的路径或针对主目录展开的 `~` / `~/…`。|

范围和限制：

- **单向迁移。** 设置 env 后，新附件仅写入配置的根目录下。读取并删除丢失配置根目录的文件，回退到默认运行时临时目录，以便上传附件 **之前** 开关保持可读和可移动，而默认后备目录保持可写 - 无法取消链接旧副本的删除（例如只读后备卷）会显示错误而不是报告成功。相反的方向——在附件写入配置的根目录后删除环境——使这些附件无法访问；在给定的工作空间中保持变量稳定。
- **每会话布局。** 文件位于两个位置的 `<root>/<projectHash>/attachments/session-<sessionId>/` 下，其中 `<projectHash>` 与默认运行时临时目录使用的工作区哈希相同；后备查找在默认目录中使用相同的会话布局。指向同一配置根的两个工作区彼此保持隔离。
- **删除清理。**删除会话时，其附件目录将从配置的根目录和默认后备目录中删除。归档会话会保留其附件，以便它们在未归档的情况下仍能保存。
- 守护进程在启动时读取该变量；更改后重新启动守护进程。该目录必须可由守护进程写入。

## 多会话和多工作区部署

多次传递 `--workspace` 以在一个 `qwen serve` 进程中注册多个不重叠的工作区。第一条路径是主要路径。每个注册的工作区都拥有一个隔离的运行时边界，而守护程序范围的侦听器、身份验证策略和总会话限制是共享的。生产尝试预热主要 ACP 子项以实现兼容性，并在失败后首次使用时重试；受信任的辅助节点按需启动自己的子节点，不受信任的辅助节点不会启动 ACP。请求可以通过规范`cwd`选择已注册的工作空间；省略 `cwd` 的请求使用主工作区。每个用户或安全主体使用一个守护程序；工作区信任是一个执行门，而不是 ACL。

不受信任的辅助工作区在 Web Shell 中显示为 `untrusted` 和 `read-only`。可以对其进行扩展以检查持久会话目录，但尚无法在 Web Shell 中选择或打开、恢复、用于创建会话或完全导出。 REST API 遵循现有的有界文件系统读取策略，并且还公开其持久会话组目录，并且当通告 `workspace_persisted_transcript` 时，通过有界工作区限定的寻呼机公开其活动的持久转录。转发和 cursor 读取不会启动 ACP 子级；实时会话的向后第一页可能会启动一个刷新持久尾部的页面。完全符合工作区要求的导出需要受信任的工作区和单独的 `workspace_session_export` 功能。在使用执行、突变或导出功能之前，信任工作区并重新启动守护程序。不受信任的主节点在 Web Shell 中保持禁用状态。

当您需要较小的故障或安全边界、独立的承载令牌、配额、审核边界、操作系统隔离或独立的资源监管时，请使用单独的守护进程进程。多工作空间模式适用于一个操作员托管多个存储库；它不是多租户隔离边界。单个守护进程令牌授权守护进程公开的每个承载门控 API 路由，包括所有注册工作区允许的只读目录。

> **订阅BEFORE在附加上发布`modelServiceId`。**当具有`modelServiceId`的客户端`POST /session`并且工作区已经有一个运行不同模型的会话时，守护进程发出内部`setSessionModel`调用 - 失败NOT 作为 HTTP 错误传播（会话在其当前模型上保持运行）。可见的失败信号是会话的 SSE 流上的 `model_switch_failed` 事件。如果你调用`POST /session`并且只THEN打开`GET /session/:id/events`，你将错过失败事件并默默地继续与错误的模型对话。首先打开 SSE 流，或者在订阅时传递 `Last-Event-ID: 0` 以重放环中最早的可用事件。

要处理多个 ** 用户或安全主体 **（每个用户或安全主体都有一个独立的令牌、配额、审核日志、沙箱或进程故障边界）或扩展到超出一个进程的范围（冷启动预算、FD 计数、RSS），请在外部协调器后面为每个主体生成一个守护进程。每个这样的守护进程仍然可以为该主体托管多个工作区。协调器（多租户/OIDC/配额/审计/k8s）**超出了 qwen-code 项目的范围** — 请参阅问题 [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>)“外部参考架构”以获取设计指南。

## 加载并恢复持久会话

该守护进程公开 ACP 的 `session/load` 并恢复 HTTP 上的流程，以及一个单独的只读转录分页器：

|路线|使用时|
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `POST /session/:id/load`                                |客户端没有**没有**渲染有用的本地历史记录（冷重新连接，选择器然后打开）。对于实时会话，守护进程返回并注入当前有界重播快照窗口；如果较旧的重播被删除，则快照以 `history_truncated` 开头。能力标签：`session_load`。|
| `POST /session/:id/resume`                              |客户端已经打开屏幕，只需要返回守护程序端句柄。模型上下文在 agent 端恢复，无需 UI 重播 - SSE 流保持干净。功能标签：`session_resume`（`unstable_session_resume` 对于旧客户端仍然是已弃用的别名）。|
| `GET /session/:id/transcript`                           |客户端需要完整的活动持久记录。它返回 cursor 页面中的无 id 重播帧，并且不会调用 `/load`、附加客户端、播种实时 EventBus、创建实时会话或更改实时重播窗口。能力标签：`session_transcript`。|
| `GET /workspaces/:workspace/session/:id/transcript`     |客户端需要来自选定工作区的活动持久记录。转发和 cursor 页面保持守护进程本地；实时会话的向后首页可能会启动 ACP 并加载工作区设置以刷新持久尾部。已注册的不受信任的辅助工作区可以使用此只读路径。能力标签：`workspace_persisted_transcript`。|
| `GET /workspaces/:workspace/session/:id/export`         |客户端需要来自所选受信任工作区的完整 `html`、`md`、`json` 或 `jsonl` 附件。它读取活动的持久存储，而无需启动 ACP 或回退到主存储。能力标签：`workspace_session_export`。|
| `GET /workspaces/:workspace/session/:id/archive/export` |客户端需要来自所选可信工作区中的存档持久存储的相同附件格式。它不会取消存档、启动 ACP 或回退到活动或主要会话。能力标签：`workspace_archived_session_export`。|

对于加载和恢复，TypeScript SDK 在 
`DaemonSessionClient` 上公开静态工厂：

```ts
import { DaemonClient, DaemonSessionClient } from '@qwen-code/sdk';

const client = new DaemonClient({ baseUrl: 'http://127.0.0.1:4170' });

// Cold reconnect — daemon will replay the bounded snapshot window through SSE.
const session = await DaemonSessionClient.load(client, 'persisted-id');

// Or, if your UI already has the history, skip the replay:
// const session = await DaemonSessionClient.resume(client, 'persisted-id');

for await (const event of session.events()) {
  // First the replayed `session_update` frames (load only),
  // then live events.
}
```

在调用匹配路由之前预飞行 `caps.features.session_load`、`caps.features.session_resume` 或 `caps.features.session_transcript` — 较旧的守护进程返回 `404`。 `unstable_session_resume` 仍被宣传为已弃用的兼容性别名。对同一 id 的并发相同操作请求合并；交叉动作竞赛（`load` 与 `resume` 竞赛）和调用者提供的 ID 会产生与 `Retry-After: 5` 竞赛恢复获取 `409 restore_in_progress`。超过 `limits.sessionRestoreTimeoutMs` 的恢复可重试 `504 session_restore_timeout`，并使用预算派生的 `Retry-After`（限制为 5-120 秒）；仍在运行的子请求保持隔离状态，直到清理完成，并且在该窗口期间重试相同 ID 时，会获得 `409 restore_in_progress` 和 `reason: awaiting_abandoned_cleanup`，并且预算派生的 `Retry-After` 被限制在 5-120 秒，而不是固定的 5 秒延迟。如果清理不确定，或者放弃的恢复在截止日期后仍未解决完整的恢复预算，新的会话工作将暂时获得 `503 acp_channel_unavailable` 和 `reason: restore_cleanup_failed` 或 `restore_settlement_overdue`。当后期清理不确定时，超时但未立即解决的会话初始化可以类似地返回 `reason: new_session_cleanup_failed`，或者当子请求在另一个初始化预算中仍然未完成时返回 `new_session_settlement_overdue`。由于所有四个原因，已经存在的会话仍然可用。有关完整的误差包络，请参阅 [ 协议参考 ](<Qwen Code 守护进程 HTTP 协议参考.md>)。

对于完整的持久重播，请使用 `DaemonClient.getSessionTranscriptPage(sessionId, { cursor, limit })` 或原始 REST 路由进行寻呼：

```bash
curl "http://127.0.0.1:4170/session/$SESSION_ID/transcript?limit=100"
```

对于已注册的工作区，请使用 `client.workspaceById(workspaceId).getSessionTranscriptPage(sessionId, { cursor, limit })` 或 `/workspaces/:workspace/session/:id/transcript`。即使 SDK 客户端具有可替换的 ACP 传输，工作区限定方法始终使用本机 REST。它的游标仅限于守护程序生命周期，并且必须在守护程序重新启动后从第一页重新启动。实时会话的向后第一页可能会启动 ACP 并加载工作区设置以在读取之前刷新持久的尾部。

如需来自受信任的已注册工作空间的完整附件，请在飞行前 `workspace_session_export` 并调用 `client.workspaceById(workspaceId).exportSession(sessionId, { format: 'html' })` 或原始 `/workspaces/:workspace/session/:id/export` 路线。不要推断来自 `session_export` 或 `workspace_qualified_rest_core` 的支持：较旧的守护进程可以在保留仅主要导出的同时通告两者。当前的 Web Shell 导出操作仍然是主要的；将 SDK 或 REST 路由用于另一个工作区。

对于已存档的附件，请在飞行前 `workspace_archived_session_export` 并致电 `client.workspaceById(workspaceId).exportArchivedSession(sessionId, { format: 'html' })` 或 `/workspaces/:workspace/session/:id/archive/export`。此路径读取存档存储并返回 `409 session_not_archived` 作为仅活动 ID；它不会取消存档会话。当该功能存在时，Web Shell 会为受信任的主工作区和辅助工作区中的存档行公开相同的导出。

`limit` 是活动聊天记录的目标数量，不发出重播帧；一条记录可以产生多个 `session_update` 事件。向后页最多可以扩展到 `3 * limit` 条记录，以保留转向和 tool-call/result 边界。第一个响应冻结 JSONL 快照大小并返回 `nextCursor`，而 `hasMore` 为 true。后面的页面会忽略页面 1 之后的附加内容，但如果文件被删除、截断、替换、存档或以其他方式与冻结的 cursor 冲突，则返回 `409`。非常大的快照在索引之前返回 `413 transcript_too_large`，因此守护程序不会扫描请求路径上的无限转录文件。

对于通过传统单一路由重复寻呼，请将 `--channel-idle-timeout-ms` 设置为正值。使用默认的 `0`，空闲工作区的 ACP 子项以及它所保存的进程内转录索引缓存在每个页面后都会被收获，因此每个页面都会重新生成子项并通过重新扫描整个冻结的前缀（每页 `O(snapshotSize)`）来重建索引。正超时使子进程在 cursor 遍历中保持活动状态，因此它会重用其缓存的转录本索引和重播配置，除非 `--child-heap-mode admit` 或 `enforce` 回收它以满足另一个工作区中容量受限的生成。在这种情况下，下一页会重新生成子页面并重建索引。工作区限定转发和 cursor 页面保持守护进程本地；只有实时会话的向后第一页会跨越刷新屏障。

注意：实时会话历史记录重播受两次限制：`Last-Event-ID` 重新连接的 SSE 环和 `POST /session/:id/load` 返回的快照的 `--compacted-replay-max-bytes`。漫长的历史和喋喋不休的转折可能会超出任何一个界限。守护进程使用 `history_truncated` 来显示快照截断；当您需要完整的活动持久历史记录时，请使用 `/transcript`。

## 耐久性模型

**跨守护进程重新启动**，阶段1中的会话仍然是短暂的，但可以重新加载磁盘上的持久会话：

- 子进程崩溃会发布 `session_died` 并从守护进程的映射中删除实时会话。如果新的 agent 子项是可生成的，则持久的磁盘会话 ** 可以通过 `POST /session/:id/load` 重新加载 **。
- 守护程序重新启动会丢失每个正在进行的实时会话。持久会话保留在磁盘上，并且可以根据相同的工作区绑定规则加载新的守护进程。
- 长时间的客户端断开连接（>5分钟在闲聊回合）可以超过SSE重播环（默认8000帧） - `Last-Event-ID`重新连接触发器`state_resync_required`。对于移动/不稳定网络客户端，计划在长时间掉线时重新打开 SSE 或调用 `POST /session/:id/load` 恢复当前有界重放快照；不要假设路由返回完整的成绩单。
- 文件操作（`writeTextFile`）在崩溃时是原子的（写入然后重命名）；从重播的意义上来说，它们在守护进程重新启动时不是原子的——文件写入要么落地，要么没有落地。

如果您的集成需要的服务器端交叉重启持久性超出了 `session/load` 的范围（例如服务器管理的重试队列），那么您仍然需要应用程序级状态恢复。不要在守护程序会话中保留长时间运行的、重新启动敏感的状态。

## 阶段 1.5+ 运行时保证

舞台 1 的合同大小适合原型设计。根据 [#3889 chiga0 下游消费者评论 ](<https://github.com/QwenLM/qwen-code/pull/3889#issuecomment-4427875644>)，以下是阶段 1 中的 ** 而不是 ** — 生产级集成在依赖它们之前需要阶段 1.5+：

**用于严重下游用途的阻断剂：**

1. **`loadSession` / `unstable_resumeSession` 优于 HTTP** — 如果没有这个，任何集成都无法在子崩溃或守护进程重新启动后幸存下来，并且任何协调守护进程的编排器也无法恢复状态。
2. **持久客户端身份（一对令牌 + 每个客户端撤销）** — 阶段 1 使用一个共享承载；泄露的令牌会撤销所有人，并且 `originatorClientId` 是客户端自行声明的，而不是根据经过身份验证的身份进行守护进程标记的。

**可靠性基线：**

3. ~~**客户端启动的心跳路径**~~ — 通过 [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 9 发送。 `POST /session/:id/heartbeat` 记录守护进程上最后一次看到的时间戳（功能标签 `client_heartbeat`）； SDK 帮助者是 `DaemonClient.heartbeat()` / `DaemonSessionClient.heartbeat()`。
4. **`permission_already_resolved` 事件 ** 当投票输掉第一响应者竞赛时 — 当前 UI 必须从 `404` 推断状态。
5. ~~**更大的重播环**~~ — 撞到8000。 **每会话可配置环**仍然开放 - 移动/闲聊工作负载可能需要每会话覆盖。
6. **`slow_client_warning` 事件在 `client_evicted`** 之前 - 软背压，因此行为良好的慢速客户端可以在终止之前进行自我调节（修剪渲染深度、删除块）。

**集成人体工程学：**

7. **`POST /session/:id/_meta` 用于 IM- 样式上下文 ** — 附加到后续提示（聊天 ID、发件人、线程 ID）的每个会话键值取代了每个通道的即兴创作。
8. **`/capabilities` 实际功能协商 ** — `protocol_versions: { acp: '0.14.x', daemon_envelope: 1 }` 因此客户端可以检测漂移而不是陷入“未知帧，忽略”。
9. **一流的耐用性文档**（本节）- 已在上面发货。

完整的收敛路线图可在 [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>) 上跟踪。

## Stage 1 范围边界 - 我们不会在 Stage 1.5 中修复的内容

两个结构选择是阶段 1 / 1.5 / 2 主线路线图的明确非目标。如果您的用例取决于其中任何一个，请围绕它们进行计划，而不是等待我们。

### 会话状态仅是本地突变（根据 [LaZzyMan 评论#4270256721](<https://github.com/QwenLM/qwen-code/pull/3889#pullrequestreview-4270256721>)）

阶段 1.5 计划将 TUI 描述为进程中的 EventBus 订户。实际上 **TUI UI 严格大于有线协议 **：

- **仅限本地 UI** — ~15 墨迹对话框组件（`ModelDialog`、`MemoryDialog`、`PermissionsDialog`、`SessionPicker`、`WelcomeBackDialog`、 `FolderTrustDialog`, …) 和 `local-jsx` 斜杠命令（`/ide`、`/auth`、`/init`、`/resume`、`/rename`、`/delete`、 `/language`、`/arena`、…) 渲染终端特定的 Ink JSX。 HTTP/SSE 上的远程客户端无法等效地渲染 Ink，并且这些流不发出任何连线事件。
- **没有线路事件的会话状态突变** — `/approval-mode`、`/memory add`、`/mcp add-server`、`/agents`、`/tools enable/disable`、`/auth`、 `/init`（写作`CLAUDE.md`）都更改了agent行为，但只有`/model`当前发布了事件（`model_switched`）。

**阶段1选择——评论中的选项(A)**：不要将这些突变促进为连线事件。两种部署模式具有不同的后果。

#### 模式 1 — 无头 `qwen serve`（此 PR）

守护进程内没有运行 TUI shell。在此模式下，上面列出的斜线命令 ** 不存在 ** — 没有终端 UI 可以发出它们。因此会话状态是：

- **启动时间冻结 ** for `approval-mode` / `memory` / `agents` / `tools` 白名单 / `auth` — 全部在守护程序启动时从设置 + 磁盘加载`qwen --acp`孩子开始；在会话的生命周期内不可变。设置定义的 MCP 服务器同样会在启动时冻结，但 ** 运行时添加的服务器 **（通过 `POST /workspace/mcp/servers`）可以添加或删除，无需重新启动。
- **通过`POST /session/:id/model`（发布`model_switched`）、`POST /workspace/mcp/servers`/`DELETE /workspace/mcp/servers/:name`（发布`mcp_server_added`/`mcp_server_removed`）和权限在HTTP**上可变投票（`POST /session/:id/permission/:requestId`；遗留进程全局 `POST /permission/:requestId` 仍然适用于 F3 之前的单工作空间客户端）。

**结果：**无头模式下的远程客户端看到**完整会话状态**。没有TUI隐藏额外的状态；没有漂移是可能的。如果您想更改 `approval-mode`，请使用新设置重新启动守护程序。 MCP服务器现在可以通过突变路由（`POST /workspace/mcp/servers`，`DELETE /workspace/mcp/servers/:name`）在运行时成为added/removed - 请参阅[运行时MCP服务器管理](<#runtime-mcp-server-management-issue-4514>)。

#### 模式 2 — 舞台 1.5 `qwen --serve` 共同主持 TUI（不在本 PR 中）

当阶段 1.5 登陆 `qwen --serve`（TUI 进程共同托管同一 HTTP 服务器）时，TUI ** 确实 ** 与远程客户端并存。本地操作员输入 `/approval-mode yolo` 或 `/mcp add-server` 会改变会话状态，并且 HTTP 上的远程客户端没有事件来观察更改。

在这种模式下，TUI是一个**“超级客户端”**——它观察远程客户端看到的相同的agent对话，AND可以改变远程客户端不能改变的会话状态。不对称性为：

- ✅ TUI 和远程客户端都会看到相同的 agent 消息、工具调用、文件差异、权限提示。
- ❌ 只有 TUI 可以看到/改变批准模式/内存/MCP服务器列表/agents/工具白名单/身份验证状态。

**模式2的后果：**如果远程客户端UI尝试镜像会话设置，它可能会在任何TUI斜杠命令之后漂移。远程客户端应该**重新获取连接/重新连接的状态**（使用`Last-Event-ID: 0`重播环上最旧的事件，例如`model_switched`）；他们应该NOT依赖于TUI-侧面突变的增量事件。

#### 为什么 (A) 而不是 (B)（促进 `session_state_changed` 事件家族突变）

(B) 是更雄心勃勃的答案，但将阶段 1.5 锁定在一个更大的线表面中，该表面还必须干净地通过计划的进程内重构。我们宁愿诚实地走较小的范围。会话状态事件分类工作 — 枚举哪些 TUI 流在设计上仅限于本地，哪些流可能在未来选择加入 (B) 风格的扩展下进行连接 — 移至 [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>)，而不是阶段 1.5 代码。

### N 个并行会话在每个工作区运行时共享一个 `qwen --acp` 子级

同一可信工作区 ** 上的多个会话通过 agent 的本机多会话支持 (`packages/cli/src/acp-integration/acpAgent.ts:194: private sessions: Map<string, Session>`) 共享该运行时的 `qwen --acp` 子进程 **。桥接器为每个会话调用 `connection.newSession({cwd, mcpServers})` — agent 将它们存储在其会话映射中，并对每次调用进行解复用 sessionId。生产环境最多可以拥有一个主要子项（默认情况下会尝试预热），加上每个受信任辅助项的一个按需子项；不受信任的二级节点不拥有任何内容。

同一工作空间上 N=5 个会话的具体成本：

|资源|每节课|在 N=5|
| ------------------------------------ | ----------------------------------------------------- | ---------------------------------------------------------------- |
|守护节点进程|一|**30–50 MB**（一个守护进程）|
|`qwen --acp` 孩子|共享|**60–100 MB**（一个孩子）|
|MCP 服务器儿童|发布广告时的工作空间池；否则每个会话|由匹配的池条目共享，或者在传统回退中最多 3×N|
|`FileReadCache`（子内堆）|共享|解析一次|
|`CLAUDE.md`/层次结构内存解析|共享|解析一次|
|OAuth 刷新令牌状态|共享|**一个刷新路径**|
|自动记忆学到的事实|共享|每个孩子一个知识库|
|冷启动|仅第一|<200 第一次会话后的毫秒|

每个活动工作区运行时都保留**一个桥接边界**。生产尝试预热主通道并在失败后重试首次使用；受信任的辅助节点会根据需要打开其通道和子节点，而不受信任的辅助节点则永远不会这样做。当至少一个会话处于活动状态时，频道就会保持活动状态。在最后一个 `killSession` 之后，运行时默认情况下或在配置的通道空闲宽限后立即终止其子进程；通道级崩溃也会将其拆除，而无需选择另一个运行时。

当通告 `mcp_workspace_pool` 时，**MCP 服务器子级 ** 使用工作区范围的传输池：匹配的 `(workspace runtime, server name, config fingerprint)` 条目在会话之间重新计数。如果不存在该功能，则遗留的每会话管理器将独立生成它们。

**对等点agents（Cursor / Continue / Claude Code / OpenCode / Gemini CLI）都进行单进程多会话。** qwen-code 在 agent 层匹配它们；此 PR 中的舞台 1 桥使相同的架构在 HTTP 上可见。

## 登录到远程守护程序（问题#4175 PR 21）

当守护程序在远程 Pod 上运行时（不与您共享显示），客户端可以
触发通过 HTTP 的 OAuth 设备流。守护进程轮询 IdP 本身；你的工作
只是在任何有浏览器的设备上打开URL。

> [！注意]
>
> Qwen OAuth 免费套餐已在 2026-04-15 上停止。下面的 `qwen-oauth`
>  示例记录了设备流协议形状和旧提供程序 
>  标识符；新设置应使用当前支持的身份验证提供程序。

```bash
# 1. Start a flow. The daemon contacts the IdP, returns a code + URL.
curl -X POST http://127.0.0.1:4170/workspace/auth/device-flow \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"providerId":"qwen-oauth"}'
# → 201 {
#     "deviceFlowId": "fa07c61b-…",
#     "userCode": "USER-1",
#     "verificationUri": "https://chat.qwen.ai/api/v1/oauth2/device",
#     "verificationUriComplete": "https://chat.qwen.ai/...?user_code=USER-1",
#     "expiresAt": 1700000600000,
#     "intervalMs": 5000,
#     "attached": false
#   }

# 2. Visit the URL on your phone / laptop, enter the user code.
# 3. Poll for completion (or subscribe to SSE for the auth_device_flow_authorized event):
curl http://127.0.0.1:4170/workspace/auth/device-flow/fa07c61b-… \
  -H "Authorization: Bearer $TOKEN"
# → status transitions: pending → authorized
```

TypeScript SDK 将这两个步骤包装到一个助手中：

```ts
import { DaemonClient } from '@qwen-code/sdk';

const client = new DaemonClient({ baseUrl, token });
const flow = await client.auth.start({ providerId: 'qwen-oauth' });
console.log(`Open ${flow.verificationUri}\nCode: ${flow.userCode}`);
const result = await flow.awaitCompletion({ signal: abortCtrl.signal });
// result.status === 'authorized'
```

**守护进程永远不会代表您打开浏览器。**即使在本地运行时，守护进程也保持被动状态 - 它返回URL并让SDK/用户选择在哪里打开它。这是故意的：无头 Pod 上名为 `xdg-open` 的守护进程会默默地失败，掩盖实际的身份验证表面。在您的客户端中镜像 `gh auth login` 的“按 Enter 打开浏览器”UX。

**`--require-auth` 和开发便利性。** 设备流路由使用严格的突变门。无令牌可信环回默认值可以直接启动和管理流；没有凭据的不受信任嵌入会返回 `401 token_required`。设置托管 `QWEN_SERVER_TOKEN` 并在共享主机、CI 运行器、远程开发计算机或任何不相互信任的本地进程上传递 `--require-auth`。

**跨守护程序限制。** `oauth_creds.json` 是守护程序共享的 (`~/.qwen/oauth_creds.json`)，因此守护程序 B 的下一次令牌刷新会自动获取守护程序 A 中的成功登录 — 但守护程序 B 的 SDK 客户端不会收到`auth_device_flow_authorized` 事件（事件是针对每个守护进程的）。

**跨客户端接管。**同一守护进程上的两个SDK客户端，同一提供商的两个`POST /workspace/auth/device-flow`都获得每个提供商的单例：第一个调用启动一个新的IdP请求并返回`attached: false`；第二次调用返回 EXISTING 飞行中条目和 `attached: true`。接管记录在审计跟踪中（在第二个客户端的 `X-Qwen-Client-Id` 下），但 NOT 会发出单独的事件 - 一旦用户完成 IdP 页面，两个客户端最终都会观察到 SAME `auth_device_flow_authorized`。如果您的 UI 区分“我开始了这个”和“我加入了其他人的流程”，则在 `start()` 返回的 `attached` 字段上进行分支。

## 守护进程日志文件

`qwen serve` 在稳定的 
 活动路径上正常重启时附加诊断记录：

```
${QWEN_RUNTIME_DIR or ~/.qwen}/debug/daemon/daemon.log
```

每个文件记录都包含一个随机的每次启动`runId`和守护进程PID。 
成功的稳定所有者还在支持符号链接的
平台上将`debug/daemon/latest`更新为`daemon.log`。在 macOS/Linux 上，跟随旋转：

```bash
tail -F ~/.qwen/debug/daemon/daemon.log
```

在其他平台上，将查看器配置为在替换 
 后重新打开路径名。仅保留旧文件句柄的查看器将在旋转后保留在 
 存档中。

该日志捕获生命周期消息、路由错误（使用 `route=` 和 `sessionId=` 上下文）、ACP 子 stderr，以及（当设置 `QWEN_SERVE_DEBUG=1` 时）额外的桥面包屑。今天转到 stderr 的行仍然转到 stderr；文件日志是**附加**，而不是替换。

失败的独立创建会添加 `Standalone session creation failed.` 以及已验证的目标 `sessionId`、失败的 `phase`、安全的 `reason`、`dispatchState` 和 `cleanupOutcome`。子项创建保留子项 ID，并将验证的父项单独记录为 `relatedSessionId`。按会话 ID 关联 `source_persistence_failed` 行以获取私有源确认或传输详细信息； `write_not_confirmed` 未识别文件系统原因。已完成的隔离不同于回滚，并且 `unknown` 并不表明清理成功。公共错误代码和重试行为保持不变。这些诊断忽略了原始原因、提示和源有效负载。

活动文件在超过 10 MiB 之前进行旋转。每个族在`archive/`下保留
四个档案，每个档案记录上限为256KiB。 
 内存队列最多接受 4 MiB 未解决的文件负载。因此，队列压力、
轮换失败或文件系统故障可能会删除文件副本；
`GET /daemon/status?detail=full` 会暴露记录器运行状况、问题和删除的 
record/byte 计数器。

在日志命名空间中，只有一个守护进程可以拥有稳定系列。并发的
守护进程写入`debug/daemon/runs/run-<runId>/daemon.log`；启动横幅
和完整状态包含权威路径。 `runs/recent-fallback` 是最近后备家庭的 
 尽力定位器，可能指向 
 仍然存在的一个。一个健康的命名空间大致收敛于 100 MiB：关于 50 MiB 对于 
stable 加上一个不活动的后备系列。保留活动或尚未过时的回退
系列，因此并发守护进程或crash/restart风暴可以
暂时使用更多。

一个运行时目录就是一个所有权和保留命名空间。当守护进程需要独立历史记录时，请使用不同的
`QWEN_RUNTIME_DIR`值。新的守护进程日志
目录是用户(`0700`)私有的，新文件在POSIX上使用`0600`。
没有基于年龄的到期时间。

### 禁用

设置`QWEN_DAEMON_LOG_FILE=0`（或`false`/`off`/`no`）以完全跳过文件记录。 Stderr 输出不受影响。

### 与会话调试日志的关系

会话范围的调试日志（`~/.qwen/debug/<sessionId>.txt` 和 `~/.qwen/debug/latest` 符号链接）是独立的。守护进程日志位于同级 `daemon/` 子目录中；此功能不会改变每个会话的调试语义。

### 外旋

不要将外部 logrotate 规则指向活动的 `daemon.log`。守护进程
是唯一受支持的写入器和旋转器；外部重命名、删除或
截断会使其大小模型无效。复制或传送记录而不
改变家族是安全的。较旧的 `serve-<pid>.log` 和 
`serve-<pid>-<workspaceHash>.log` 文件保持不变，并且不会被新的保留策略计入 
。

## 运行时MCP服务器管理（问题[#4514](<https://github.com/QwenLM/qwen-code/issues/4514>)）

在运行时添加或删除 MCP 服务器，无需重新启动守护程序。运行时条目存在于一个短暂的覆盖层中，**影子**设置定义的同名服务器；底层的 `settings.json` / `mcpServers` 配置永远不会被写入。

**飞行前：** 在呼叫任一航线之前检查 `caps.features` 中的 `mcp_server_runtime_mutation`。没有此标签的旧守护程序将返回 `404`。

### `POST /workspace/mcp/servers` — 添加运行时 MCP 服务器

严格门控（需要可信环回主要或操作员凭据）。通过实时 `McpClientManager` 立即连接服务器并发现其工具。

要求：

```json
{
  "name": "my-server",
  "config": {
    "command": "npx",
    "args": ["-y", "@my-org/mcp-server"]
  }
}
```

`name` 必须是字母数字加上 `_` 和 `-`（最多 256 个字符）。 `config` 与 `settings.json` `mcpServers` 条目中使用的 MCP 服务器配置对象相同（传输相关字段：对于 stdio，`command`/`args`，对于 `url` SSE/HTTP）。安全敏感字段（`trust`、`env`、`cwd`、`oauth`、`headers`、`authProviderType`、`includeTools`、`excludeTools`、 `type`) 被守护进程剥离并忽略。

响应 (200) — 成功：

```json
{
  "name": "my-server",
  "transport": "stdio",
  "replaced": false,
  "shadowedSettings": false,
  "toolCount": 3,
  "originatorClientId": "client-1"
}
```

- `replaced: true` — 已存在同名的运行时条目，并且配置指纹不同；旧的连接被拆除，新的连接被建立。当指纹匹配时（幂等重加），`replaced`为`false`。
- `shadowedSettings: true` — 存在同名的设置定义服务器；运行时条目现在遮蔽了它。如果稍后删除运行时条目，设置条目将保持不变并重新出现。
- `toolCount` — 在新连接的服务器上发现的工具数量。

响应（200）——软拒绝（预算警告模式）：

```json
{
  "name": "my-server",
  "skipped": true,
  "reason": "budget_warning_only"
}
```

当`--mcp-budget-mode=warn`并且添加服务器将超出配置的`--mcp-client-budget`时返回。服务器已连接NOT。呼叫者应向用户表明预算压力。

错误：

|状态|代码|当|
| ------ | ------------------------- | -------------------------------------------------------------------------------------------------- |
| `400`  | `invalid_server_name`     |名称为空，超过 256 个字符，或包含 `[A-Za-z0-9_-]` 之外的字符|
| `400`  | `missing_required_field`  |`config` 缺少或不是非空对象|
| `400`  | `invalid_client_id`       |`X-Qwen-Client-Id` 标头存在，但未为此工作区注册|
| `400`  | `invalid_config`          |配置形状被 MCP 传输验证器拒绝|
| `401`  | `token_required`          |严格门拒绝不可信的无令牌请求|
| `409`  | `mcp_budget_would_exceed` |`--mcp-budget-mode=enforce` 预算已满|
| `502`  | `mcp_server_spawn_failed` |连接过程中服务器进程退出或超时；机身带有`serverName`、`exitCode`、`stderr`|
| `503`  | `acp_channel_unavailable` |没有实时 ACP 子项（尚未创建会话）|

### `DELETE /workspace/mcp/servers/:name` — 删除运行时 MCP 服务器

门禁严密。断开服务器连接并将其从运行时覆盖中删除。幂等 - 删除从未添加的名称会返回跳过响应（不是错误）。

`:name` 路径参数是 URL- 编码的服务器名称。

响应 (200) — 成功：

```json
{
  "name": "my-server",
  "removed": true,
  "wasShadowingSettings": false,
  "originatorClientId": "client-1"
}
```

- `wasShadowingSettings: true` — 删除的运行时条目正在隐藏同名的设置定义服务器。该设置条目现已取消隐藏，并将在下一个 discovery/restart. 中使用

响应 (200) — 幂等跳过：

```json
{
  "name": "ghost",
  "skipped": true,
  "reason": "not_present"
}
```

当名称不在运行时覆盖中时返回（它可能仍然存在于设置中 - 设置条目无法通过此路由删除）。

错误：

|状态|代码|当|
| ------ | ------------------------- | ----------------------------------------------------------------------------- |
| `400`  | `invalid_server_name`     |名称为空，超过 256 个字符，或包含 `[A-Za-z0-9_-]` 之外的字符|
| `400`  | `invalid_client_id`       |`X-Qwen-Client-Id` 标头存在，但未为此工作区注册|
| `401`  | `token_required`          |严格门拒绝不可信的无令牌请求|
| `503`  | `acp_channel_unavailable` |没有直播ACP孩子|

### 影子语义

运行时条目在设置定义的 MCP 服务器之上形成一个临时覆盖层：

- **添加**一个与设置条目同名的运行时服务器**阴影**它 - 运行时配置优先。原始设置条目未修改。
- **删除**正在隐藏设置条目的运行时服务器**取消隐藏**它 - 设置定义的配置在下次连接时再次激活。
- **守护进程重新启动**丢失所有运行时条目。只有设置定义的服务器在重新启动后仍能存活。运行时服务器是会话生命周期范围内的。
- **`GET /workspace/mcp`** 报告合并视图 - 设置定义的服务器和运行时服务器都出现在 `servers[]` 数组中。今天的快照中的两个源之间没有线路级别的区别。

### 活动

两条路由都会发出 ** 工作空间范围的 ** SSE 事件（所有活动会话总线都会接收它们）：

|活动|发出时间|有效负载字段|
| -------------------- | ------------------------------- | -------------------------------------------------------------------------------------- |
| `mcp_server_added`   |`POST` 成功（未跳过）| `name`, `transport`, `replaced`, `shadowedSettings`, `toolCount`, `originatorClientId` |
| `mcp_server_removed` |`DELETE` 成功（未跳过）| `name`, `wasShadowingSettings`, `originatorClientId`                                   |

跳过的响应（`budget_warning_only`、`not_present`）会 NOT 发出事件。

当运行时添加量超过预算阈值时，现有 `mcp_guardrail_events` 表面（`mcp_budget_warning`、`mcp_child_refused_batch`）中的预算相关事件也会触发。

## 接下来是什么

- **设置长时间运行的守护进程？** [本地启动模板（systemd / launchd / nohup / tmux）](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/qwen-serve-deploy-local.md>) 用于 v0.16-alpha（仅限本地）。
- **构建客户端？** 请参阅 [DaemonClient TypeScript 快速入门 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/daemon-client-quickstart.md>) 和 [HTTP 协议参考 ](<Qwen Code 守护进程 HTTP 协议参考.md>)。
- **阅读源代码？**桥代码位于`packages/cli/src/serve/`； SDK 客户端位于 `packages/sdk-typescript/src/daemon/`。
- **跟踪路线图？** 阶段 1.5 / 阶段 2 问题 [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>) 的进度已跟踪。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Daemon mode (`qwen serve`)

Run Qwen Code as a local HTTP daemon so multiple clients (IDE plugins, web UIs, CI scripts, custom CLIs) share one agent session over HTTP + Server-Sent Events instead of each spawning their own subprocess.

> **🚧 v0.16-alpha**: `qwen serve` first ships to npm in v0.16-alpha as **text-only chat / coding** with **local-only deployment**. Image / file attachments on the prompt path, containerized deployment (Docker / k8s / nginx reverse-proxy), and remote / multi-daemon hardening land in a follow-up patch when an enterprise pilot is committed. See [v0.16-alpha known limits](<#v016-alpha-known-limits>) for the full deferred list.

> **Status:** Stage 1 (experimental). The protocol surface is locked at the §04 routes table from issue [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>). Stage 1.5 (`qwen --serve` flag — TUI co-hosts the same HTTP server) and Stage 2 (in-process refactor + `mDNS`/OpenAPI/WebSocket/Prometheus polish) are immediately downstream.
>
> **Scope honesty:** Stage 1 is sized for **developers prototyping clients against the protocol surface** and for **local single-user / small-team collaboration**. Production-grade multi-client / long-running / network-flaky workloads (mobile companions, IM bots reaching 1000+ chats) need Stage 1.5+ guarantees that aren't in this release. See [Stage 1.5+ runtime guarantees](<#stage-15-runtime-guarantees>) for the full gap list and #3803 for the convergence roadmap.

## What it gives you

- **Built-in Web Shell UI** — `qwen serve` serves the browser-based Web Shell at its root (`http://127.0.0.1:4170/`) out of the box; run `qwen serve --open` to launch it in your browser automatically. It is served on the same origin as the API, so no second port or reverse proxy is needed. Pass `--no-web` for an API-only daemon.
- **Up to one primary ACP child plus one on-demand child per trusted secondary, many clients** — production attempts to preheat the primary bridge and retries on first use after failure; trusted secondary runtimes start their own child on demand, while untrusted secondaries never start one. Under the default `sessionScope: 'single'`, clients targeting the same workspace share one ACP session and collaborate on the same conversation, file diffs, and permission prompts.
- **Reconnect-safe streaming** — SSE with `Last-Event-ID` reconnect lets a client drop and pick up exactly where it left off (within the ring's replay window).
- **Paged persisted transcripts** — `GET /session/:id/transcript` returns the complete active on-disk transcript as replay pages without attaching a client or changing the live SSE replay window.
- **First-responder permissions** — when the agent asks for permission to run a tool, every connected client sees the request; whichever client answers first wins.
- **One daemon, one or more workspaces** — repeat `--workspace` to register isolated workspace runtimes under one listener. The first workspace is primary and remains the default for requests that omit `cwd`.
- **Experimental daemon-managed channels** — start with `qwen serve --channel <name>`, or start without a channel and select one later with `qwen channel set`. Workers are separate processes owned by the daemon lifecycle. Their selection can be queried, replaced, reloaded, and stopped without restarting the daemon.
- **Remote runtime control** — change a session's approval mode (`POST /session/:id/approval-mode`), toggle a tool (`POST /workspace/tools/:name/enable`) or loaded skill (`POST /workspace/skills/:name/enable`) per workspace, scaffold an empty `QWEN.md` (`POST /workspace/init`, mechanical only — does NOT call the model; for AI-fill, follow up with `POST /session/:id/prompt`), restart a single MCP server with a budget pre-check (`POST /workspace/mcp/:server/restart`), or add/remove MCP servers at runtime without a daemon restart (`POST /workspace/mcp/servers`, `DELETE /workspace/mcp/servers/:name`). The default token-less loopback listener can use all of them; the strict workspace mutations additionally require operator credentials in other deployment modes.
- **Session recap** ([#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) follow-up) — fetch a one-sentence "where did I leave off" summary of an active session (`POST /session/:id/recap`). Wraps core's `generateSessionRecap` as a side-query against the fast model; pollutes neither the main chat history nor the SSE stream. Non-strict gate (same posture as `/prompt`); SDK helper `client.recapSession(sessionId)`.
  - **Known limit — token-cost amplification:** the route is a pure-cost endpoint (each call is an LLM side-query, no state benefit) and the daemon has no per-route rate limit in v1. On a no-token loopback default a buggy or malicious local client can spam it to burn tokens. Configure `--token` (and optionally `--require-auth`) on shared dev hosts before exposing the daemon.
  - **Concurrent recap safety:** two simultaneous `/recap` calls on the same session run two independent side-queries. `generateSessionRecap` reads a snapshot of the chat history via `GeminiClient.getChat().getHistory()` and feeds it to a separate `BaseLlmClient.generateText` call (via `runSideQuery`); it never appends to or mutates the session's `GeminiChat`. Safe to call from multiple clients without coordination.

## v0.16-alpha known limits

The first npm release of `qwen serve` (v0.16-alpha) is intentionally narrow — text-only chat / coding for developers running the daemon on their own machine. The list below makes the deferred surface explicit so adopters can plan around it; everything here is on the v0.16.x patch roadmap or a near-term follow-up release.

**Product surface — text-only:**

- ✅ Text prompts and text responses (chat, coding, tool calls, MCP integration)
- ❌ **Image / file attachments on the prompt path** — `MessageEmitter` currently only renders text; multimodal echo lands when an alpha target with image needs is committed (#4175 chiga0 #27 P0 item)
- ❌ **Streaming uploads** — same gating as multimodal

**Deployment surface — local-only:**

- ✅ Loopback (`127.0.0.1`, default) — no auth required, suitable for dev workstations
- ✅ Local launch via `systemd` / `launchd` / `nohup &` / `tmux` — see [Local launch templates](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/qwen-serve-deploy-local.md>)
- ✅ Bring-your-own bearer token via `QWEN_SERVER_TOKEN` env var ([Authentication](<#authentication>) for setup)
- ❌ **Containerized deployment** — Docker / Compose / Kubernetes / nginx reverse-proxy with TLS termination NOT in v0.16-alpha. Defers to v0.16.x once an enterprise pilot is committed (would otherwise rot from no-one-validating).
- ❌ **Multi-daemon coordination on one host** — one daemon can host several explicitly registered workspaces, but daemons do not coordinate with each other. Cross-host federation, instance-path token keying, and stale-token cleanup defer to v0.16.x.
- ✅ **Revocable Local Control pairing tokens** — `--local-control` mints a separate LAN pairing token owned by the daemon. General daemon token storage remains BYO-token.

**Hardening — minimum viable for local single-user:**

- ✅ Boot-time security gate (non-loopback binds always carry a bearer token — operator-supplied or generated at startup; explicitly empty sources still refuse to boot, [PR 15 / #4236](<https://github.com/QwenLM/qwen-code/pull/4236>))
- ✅ Mutation-route auth gate, session-scoped permission routing (Wave 4 PRs)
- ✅ MCP guardrails + multi-client permission coordination (F2 / F3)
- ✅ **Prompt absolute deadline + SSE writer idle timeout** — opt-in via `--prompt-deadline-ms` and `--writer-idle-timeout-ms`; advertised through `prompt_absolute_deadline` and `writer_idle_timeout` when enabled.
- ✅ **HTTP rate limiting** — opt-in via `--rate-limit` and per-tier thresholds; advertised through `rate_limit` when enabled.
- ⏸️ **Prometheus metrics + load test harness** — defers to v0.17 F4 Phase-1 scale instrumentation when 30-50 active sessions becomes a real target.
- ⏸️ **`--max-body-size` CLI flag** — daemon enforces `express.json({ limit: '10mb' })` by default which comfortably covers text-only prompts (model context windows are well under 10 MiB of chars). Tunable via flag in v0.16.x.

For the deeper "what we won't fix in Stage 1" enumeration (single-host session-state mutation model + N parallel sessions sharing one ACP child inside each workspace runtime), see [Stage 1 scope boundaries](<#stage-1-scope-boundaries--what-we-wont-fix-in-stage-15>) below.

## Quickstart

### Remote Web Shell and phone access

```bash
qwen serve --hostname 0.0.0.0
```

When neither `--token` nor `QWEN_SERVER_TOKEN` is supplied, a non-loopback listener generates a 128-bit ephemeral bearer (22 URL-safe characters) and prints it once at startup. Explicit flag values take precedence over the environment; on a non-loopback bind an explicitly empty value still fails closed and refuses to boot, while on loopback it shadows `QWEN_SERVER_TOKEN` and leaves the bind in the trusted token-less mode described under `--require-auth`. Default loopback behavior is unchanged.

On wildcard binds, startup lists token-free local/network URLs from actual interfaces and — for the generated token, or for your own token at an interactive terminal — a **secret-bearing QR code** for one labeled private-LAN address. Wildcard enumeration lists only the same private/link-local LAN population Local Control advertises (RFC 1918 / RFC 3927 IPv4, plus fc00::/7 ULA IPv6 on dual-stack binds); the software-interface filter that keeps VPN adapters and container bridges out is a **name heuristic** ([#9158](<https://github.com/QwenLM/qwen-code/issues/9158>)), not a guarantee, so k8s overlay names (`cbr0`, `cali*`, `vxlan.*`) can slip through. An explicit non-loopback bind instead prints and QRs exactly the address you chose — including a routable one. Inside a container the enumerated address is the container's own interface, so reaching a published port means substituting the host address; a pty-allocated container counts as an interactive terminal, so its logs stay secret-bearing. Scan the QR to open the built-in Web Shell with the bearer in the URL fragment. The browser removes the credential from the URL and keeps it in per-tab `sessionStorage`. Alternatively, open a printed URL and enter the token; missing or expired credentials show a retry form. Generated tokens rotate on restart. `--no-web` omits the QR, and a piped/redirected stdout never re-publishes a stable operator token inside a QR — that case prints a suppression hint naming `--token-qr` plus an address-only QR (the Web Shell asks for the token on arrival) instead; pass `--token-qr` or set `serve.tokenQr` in user, system, or system-defaults settings to print the full token-bearing QR anyway; `--no-token-qr` suppresses the startup quickstart token QR for that run on every quickstart path, including an interactive terminal and a generated token (which still prints as its own plain-text line); it does not govern the Local Control pairing QR, which `--local-control` prints by design. Requesting the QR where none can print — `--no-web`, unresolved Web Shell assets, or a loopback bind — says so on stderr instead of doing nothing silently. Addresses do not imply firewall or NAT reachability; no public IP is discovered automatically.

The built-in Web Shell's same-origin **HTTP** requests work without `--allow-origin`; other browser origins still need that allowlist, and `Origin: null` remains rejected. WebSocket-backed features (terminal, voice) and browsers reaching the daemon through a TLS-terminating reverse proxy still require an explicit `--allow-origin <origin>` entry — forwarded headers are never trusted to establish same-origin. So does any plain-HTTP intermediary that rewrites the `Host` header: nginx's default `proxy_set_header Host $proxy_host` and k8s Ingress both make the daemon see a `Host` that no longer matches the browser's `Origin`. Pass `--allow-origin <origin>` for the origin your browser actually sees, or configure the proxy to forward `Host` verbatim. On a **non-loopback** bind, port translation alone (`docker -p 8080:4170`) needs nothing — the same-origin check compares `Origin` against the forwarded `Host` and never consults the listening port. On the default **loopback** bind it does not: the DNS-rebinding Host allowlist only accepts the daemon's own port, so a port-translating tunnel (`ssh -L 8080:localhost:4170`) is rejected with `403 Invalid Host header` for every request including the shell document, and `--allow-origin` cannot override it — forward the same port (`ssh -L 4170:localhost:4170`) or bind non-loopback. The **Mobile access** pairing QR is built from the scheme and `Host` of the request that asks for it — never from forwarded headers — so behind a TLS-terminating proxy it keeps the daemon's own `http` scheme, and the exchange rejects the phone's `https` page because the invitation is bound to the origin it was issued for. Pair while browsing the daemon's direct address, or enter the token on the phone instead. The bearer grants full daemon API authority, including code execution. Treat terminal tokens and QR captures as secrets. HTTP is unencrypted; the existing `--tls-cert` and `--tls-key` options provide HTTPS with your own certificate.

To use a remote daemon from the Web Shell on your own machine, enter the remote daemon's origin (for example `https://dev-box.example:4170`) and its bearer token in the connection screen or **Daemon Status**. The remote daemon must allow your local page's origin, for example `qwen serve --hostname 0.0.0.0 --allow-origin http://localhost:4170`; the terminal's WebSocket needs that entry too. The token is kept per tab and per daemon origin and is never reused for another daemon. The sidebar lists workspaces from one selected daemon at a time; switching daemons reloads the page and does not combine local and remote project lists. Use the existing **Add workspace** form to register an absolute path on the selected daemon. A link whose `?daemon=` names an origin this browser has not connected to before is not contacted until you confirm it on the connection screen. Cross-origin connections do not offer the browser-local file bridge, in either standalone or embedded Web Shell. An HTTPS page cannot connect to an HTTP daemon, so serve both over HTTPS outside trusted networks.

### 1. Start the daemon (loopback, no auth)

```bash
cd your-project/
qwen serve
# → qwen serve listening on http://127.0.0.1:4170 (mode=http-bridge, workspace=/path/to/your-project)
# → qwen serve: trusted loopback mode; local callers have full API access without bearer authentication, including code execution as the daemon user. Use --require-auth with QWEN_SERVER_TOKEN on shared or untrusted hosts.
```

The default bind is `127.0.0.1:4170`. Bearer auth is **off** and the primary listener is trusted, so any local process that can reach the port can use the full operator API, including executing code as the daemon user. Route-specific workspace trust, session ownership, `X-Qwen-Client-Id`, permission, feature, validation, and resource checks still apply. The daemon registers the current working directory as its primary workspace; use an absolute `--workspace /path/to/dir` to override it, and repeat the flag to register additional isolated runtimes.

For an API-only daemon, disable the Web Shell. The session, prompt, workspace, permission and SSE routes are unchanged; the surfaces bound to the Web Shell go with it — Local Control enablement fails closed on every platform, and the `/live/*` routes (plus, on macOS with `QWEN_SERVE_LIVE_NATIVE_HOST=1`, the `/live/host` WebSocket) are not registered:

```bash
qwen serve --no-web
```

**Open the Web Shell UI.** Browse to `http://127.0.0.1:4170/` (or start the daemon with `qwen serve --open` to launch it automatically) for the full browser terminal — chat, diffs, commit history, tool calls, and permission prompts. The UI is served at the daemon root on the same origin as the API. The rest of this guide uses raw HTTP so you can script against the API directly.

For an authenticated single-user launch without manually creating a token, opt in explicitly:

```bash
qwen serve --open-with-auth
```

This loopback-only mode generates a 256-bit bearer token when neither `--token` nor `QWEN_SERVER_TOKEN` supplies one, then gives it to the opened Web Shell as a `#token=` URL fragment. The shell removes the fragment and keeps the credential in that tab's `sessionStorage`; refresh works, but closing the tab or restarting the daemon loses the credential. In CI, SSH, or another environment where auto-open is unavailable, the daemon starts and prints the fragment URL for manual opening. The printed URL is secret-bearing.

The flag is default-off, includes the browser-opening behavior, and requires the Web Shell, built Web Shell assets, and a loopback bind. Bare `qwen serve --open` remains token-less on loopback. In authenticated-open mode, normal API routes reject other local clients without the bearer; static Web Shell assets and loopback `/health` keep their existing pre-authentication behavior unless `--require-auth` is also set. For multiple clients or a reopenable Web Shell, use a stable shared token instead:

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
qwen serve --open
```

### 2. Sanity-check it

```bash
curl http://127.0.0.1:4170/health
# → {"status":"ok"}

curl http://127.0.0.1:4170/capabilities
# → {"v":1,"mode":"http-bridge","features":["health","daemon_status","capabilities","session_create",...],"workspaceCwd":"/path/to/your-project"}

curl http://127.0.0.1:4170/daemon/status
# → {"v":1,"detail":"summary","status":"ok","runtime":{...}}
```

The `workspaceCwd` field surfaces the primary compatibility workspace so clients can intentionally omit `cwd` on `POST /session`. Current clients should select a trusted entry from `workspaces[]` and send that entry's `cwd` when targeting a runtime explicitly.
The `limits.maxPendingPromptsPerSession` field advertises the active per-session prompt admission cap; `null` means the cap is disabled. `limits.maxTotalSessions` advertises the optional daemon-wide fresh-session cap; `null` means unlimited.

### Workspace registration capacity

The default registration limit is 256. Set `QWEN_SERVE_MAX_WORKSPACES=25 qwen serve --workspace /path/to/project` to lower it; accepted values are decimal integers from 1 through 256. Invalid values fail startup. This is a daemon startup setting: project `.env` and project `settings.env` cannot change it. Embedded callers may set `ServeOptions.maxRegisteredWorkspaces`, which takes precedence over the startup environment.

The limit counts the primary workspace, registered secondary workspaces, and user-created scratch workspaces. The internal Conversations runtime is exempt. Dynamic registration returns `409 workspace_limit_reached` at capacity. `GET /capabilities` and `GET /daemon/status` expose `limits.maxRegisteredWorkspaces`.

With the default registration capacity of 256, the standard daemon defaults to a fixed global session cap of 800, including a daemon started with only one workspace. The same session default applies to any configured registration capacity above 25. `--max-total-sessions` still overrides it; `0` disables it. At capacities of 25 or less, the existing default remains: one startup or restored workspace is unlimited, and several derive the total from the per-workspace cap and that count. Registration changes do not recompute the total. This does not change ACP child memory policy or promise 800 safe concurrent sessions.

Channel control independently supports at most 25 workspace owners, exposed as `limits.maxChannelControlWorkspaces`. A transition also counts owners retained for recovery; at capacity, successfully stop all selected channels on an old owner before adding a new owner. An oversized transition returns `409 channel_control_workspace_limit_reached`. `--channel all` continues to select the primary workspace only. The channel SDK timeout is unchanged.

Persisted registration files retain schema version 1 and support up to 255 secondary records within 8 MiB. Raw records, including stale aliases, consume persistent slots until forgotten. If the complete valid startup set exceeds the configured limit, startup fails and leaves the file unchanged. To lower capacity, restart with the previous capacity, forget enough registrations, and reduce explicit `--workspace` arguments before restarting with the lower limit. Before downgrading to an older binary, back up the file and use the newer binary to shrink it to at most 24 secondary records and 256 KiB. Older binaries otherwise reject that store and start with explicit workspaces only; their store mutations fail before writing.

### Adjust session live-state polling

Web Shell refreshes workspace session live-state every **5 seconds** by default. To use a different interval, set the daemon environment variable before starting it:

```bash
QWEN_SESSION_LIVE_STATE_POLL_INTERVAL_MS=10000 qwen serve --web
```

The value is an integer in milliseconds, from `1000` to `2147483647`. Missing or invalid values fall back to `5000`. The interval applies to all workspaces served by that daemon; restart the daemon and reload any open Web Shell pages after changing it. Local session actions and returning to a visible tab can still refresh immediately. This only changes live-state polling, not full session-catalog polling.

The daemon publishes the effective interval as `sessionLiveStatePollIntervalMs` in `/capabilities`. Web Shell uses that value and falls back to five seconds when connecting to an older daemon that omits it. SDK consumers can read the value when choosing their own polling schedule.

### Run channels from the daemon

```bash
# Start one configured channel under qwen serve
qwen serve --channel telegram

# Start several configured channels under daemon-owned workspace workers
qwen serve --channel telegram --channel feishu

# Start all configured channels
qwen serve --channel all

# Or start a token-protected daemon with no channel worker
QWEN_SERVER_TOKEN=secret qwen serve

# Enable or replace its runtime selection later
qwen channel set telegram --token secret
qwen channel set telegram feishu --token secret
qwen channel set all --token secret

# Inspect or stop daemon-managed channels
qwen channel status --daemon-url http://127.0.0.1:4170 --token secret
qwen channel stop --daemon-url http://127.0.0.1:4170 --token secret
```

This mode is experimental and daemon-managed. It does not replace the standalone `qwen channel start` command: without `--daemon-url`, existing `qwen channel start`, `stop`, and `status` behavior remains standalone. With `qwen serve --channel`, the explicit selection wins, the daemon reserves the channel-service lease before listening, and startup fails if the initial worker cannot become ready. On a flagless boot, the daemon restores `serve.channels` from every trusted registered workspace, so a channel a non-primary workspace is configured to start comes back with the daemon instead of needing a runtime API call. If neither source selects channels, the heavy channel runtime remains unloaded and no channel-service lease is reserved until the first runtime PUT, or until a workspace that configures its own `serve.channels` registers. A trusted workspace registered after the daemon started restores its own `serve.channels` too, once per daemon run: registering it again later — after removing it, or after its trust was reconfigured — does not repeat the restore, so a channel you stopped in between stays stopped. Starting the daemon with an explicit `--channel` skips this entirely, and so does stopping channels with `qwen channel stop` (`DELETE /workspace/channel`) until a `qwen channel set` enables them again: registering a workspace is not a request to turn hosting back on. Unlike the boot restore, which skips a name it cannot host and keeps the rest, a late workspace whose list contains such a name gets none of its channels back, and the daemon log records why. Registering the workspace does not wait for its channels to come up. A channel name listed by a workspace that cannot host it is skipped with a log, leaving the other workspaces' channels running; `all` still applies only to the primary workspace. If a ready worker later crashes, the daemon keeps running, relaunches it under a bounded restart policy, and reports its state (including `channel_worker_exited` warnings) in `GET /daemon/status`.

Automatic restore skips an invalid startup field or a validation or lease error before workers start, logs its `serve.channels` source, and keeps unrelated settings in effect. After a worker startup fails, the daemon continues only once cleanup succeeds. Global runtime startup timeouts and unconfirmed worker stops retain the normal startup-failure behavior; the service lease remains held while worker termination is unconfirmed. Check the daemon log for skipped or failed restores.

Stored startup names must be non-empty, have no leading or trailing whitespace, and contain no unsafe control or invisible characters. Invalid entries are skipped individually and logged by array index. Startup neither trims them into a different instance name nor rewrites settings. The channel startup toggle reflects persisted configuration, while runtime state reports whether the channel is running.

Runtime control is exposed as `GET`, `PUT`, and `DELETE /workspace/channel`; SDK helpers are `getChannelWorkerControl()`, `setChannelWorkerSelection()`, and `stopChannelWorker()`. PUT/DELETE/reload use the strict mutation gate: trusted-loopback primary callers may use them without a token, while other deployments require operator credentials. Runtime selections are deliberately ephemeral: PUT does not edit settings or the boot options, and a restart returns to the explicit `qwen serve --channel` selection, otherwise to the trusted primary workspace's `serve.channels`, or to disabled when neither exists. Explicit CLI and runtime named selections are trimmed and deduplicated in first-occurrence order; order is preserved because the first channel can affect shared model selection.

Daemon-backed `qwen channel set` and `qwen channel reload`, plus `status` and `stop` with `--daemon-url`, cannot discover a token generated by `--open-with-auth`. Use `QWEN_SERVER_TOKEN` and pass the same value with `--token` to those clients, or omit authenticated-open mode.

The daemon reads each channel's settings (tokens, `proxy`, per-channel `model`) when its worker starts. To re-read settings without changing the committed selection, call `POST /workspace/channel/reload` (SDK `client.reloadChannelWorker()`, or `qwen channel reload`). Reload re-resolves workspace ownership and restarts selected workers through the same rollback-safe reconcile path. The `channel_control` capability is present whenever runtime control is wired; `channel_reload` is present only while the manager is enabled. Persisted threads are restored from disk.

Each selected channel's `cwd` must resolve to a registered workspace, and channels are grouped by that owning workspace: a single-workspace daemon runs one worker (unchanged from before); a multi-workspace daemon (`--workspace` repeated) runs one worker per workspace that owns a selected channel, each bound to that workspace's cwd, `QWEN_DAEMON_WORKSPACE`, and env overlay. To host a channel in a non-primary workspace, define it in that workspace's own `.qwen/settings.json` (no `cwd` needed) or set an explicit `cwd` equal to the workspace path. A channel defined only in user/system scope with no `cwd` is ambiguous across multiple workspaces: an explicit `--channel` selection fails startup, while automatic restore logs the ownership error and skips that selection before workers start. `--channel all` stays primary-only (it hosts the primary workspace's channels) and cannot be combined with named channels.

Replacing a selection preflights configuration, ownership, and trust before stopping anything. It keeps workspace workers whose ordered selection is unchanged. If a changed worker cannot start, the daemon stops new workers and restores the old selection. If the daemon cannot confirm that an old child exited even after SIGKILL, it keeps the PID lease and refuses to create a duplicate worker. A worker is still considered ready when at least one requested adapter connects; PUT then returns `partial: true`, and `/daemon/status` reports `channel_worker_partial_connect` for the missing adapters. A `serve.channels` name the daemon could not bring up — a configured selection whose worker failed to start, a name boot could not attribute, or one a late registration's restore left down — is reported as `channel_restore_failed` on `/daemon/status`, and shows as `error` with its `lastError` in that workspace's channel list when that workspace's own settings define it. Other ways a name can stay unhosted are in the daemon log only.

When an adapter rejects `connect()`, current worker snapshots may include `startupFailures` entries with the channel, `phase: "connect"`, an optional adapter code, and a credential-redacted message. `qwen channel set`, `qwen channel reload`, and remote `qwen channel status --daemon-url …` print these reasons. If every adapter fails during a dynamic set or reload, the command receives `502 channel_worker_start_failed`; the response reasons describe that attempt and its `state` describes the result after rollback. The failed attempt is not retained by later status requests. At most 64 reasons are retained per worker startup, and adapter codes should be treated as diagnostic rather than stable categories. Initial `qwen serve --channel …` startup still exits when no adapter connects.

The daemon also exposes read-only runtime snapshots for client UIs and
operators: `GET /daemon/status`, `GET /workspace/mcp`,
`GET /workspace/skills`, `GET /workspace/providers`, `GET /workspace/env`,
`GET /workspace/preflight`,
`GET /workspace/:id/session-info`,
`GET /session/:id/status`, `GET /session/:id/context`,
`GET /session/:id/supported-commands`, and
`GET /session/:id/tasks`, `GET /session/:id/lsp`,
`GET /session/:id/resources`, and
`GET /session/:id/transcript`.

`GET /workspace/:id/session-info` (and the plural
`GET /workspaces/:workspace/session-info` twin) returns aggregate session
counts for a workspace: persisted `active` / `archived` / `total`, plus the
current in-memory `live` count when live state is available. Registered
untrusted secondary workspaces omit `live` because their catalog reads do not
query the live bridge. The paginated `GET /workspace/:id/sessions` list does
not include a total, so this is the dedicated surface for “how many sessions
exist?” — useful when scheduled or recurring tasks leave a large local store.

> ⚠️ **Disk scan — do not poll.** This endpoint walks local session JSONL
> files under the workspace chats directory. Responses always include
> `expensive: true` and `cost: "disk_scan"`. Call it infrequently (manual
> refresh, operator tooling, occasional UI load) — never on a tight timer or
> on every sidebar render. Prefer `GET /workspace/:id/sessions` for browsing
> pages and `GET /daemon/status` for live in-memory session counts. A response
> with `truncated: true` means the scan hit its safety limit or could not
> classify every candidate file, so persisted counts are lower bounds.

```bash
curl http://127.0.0.1:4170/workspace/$(python3 -c "import urllib.parse,os; print(urllib.parse.quote(os.getcwd(), safe=''))")/session-info
# → {"active":450,"archived":30,"total":480,"live":2,"expensive":true,"cost":"disk_scan"}
```

`GET /session/:id/status` returns the live bridge summary for a single session:
`sessionId`, `workspaceCwd`, `createdAt`, optional `displayName`, `clientCount`,
and `hasActivePrompt`. It answers `200` with the summary when the daemon holds a
live session with that id, and `404` (body `{ "error": …, "sessionId": … }`)
otherwise. Use it to poll whether one known session is still running
(`hasActivePrompt`) or how many clients are attached (`clientCount`) without
fetching and scanning the whole paginated session list:

```bash
curl http://127.0.0.1:4170/session/$SESSION_ID/status
# → {"sessionId":"…","workspaceCwd":"…","createdAt":"…","clientCount":1,"hasActivePrompt":false}
```

This is the raw live-session view, so `clientCount` and `hasActivePrompt` match
the corresponding entry in `GET /workspace/:id/sessions` — but the two routes
are not byte-identical. The list endpoint enriches each item with persisted
session-store data: its `createdAt` is the persisted first-prompt time, and it
adds `updatedAt` plus a `displayName` derived from the stored title or first
prompt. `/status` instead reports the live session's own `createdAt`, omits
`updatedAt`, and returns `displayName` only when one is set on the live session.

`GET /session/:id/lsp` returns structured per-session LSP status. Start the
daemon with `--experimental-lsp` to enable LSP in spawned agent sessions;
otherwise the route returns `enabled: false` with no servers.

`GET /session/:id/resources` returns the sanitized Skill and MCP snapshots
from that live session's own Config. Use it when a session may run from a
worktree or another effective directory whose resources differ from the base
workspace. Workspace-owned MCP authentication, pool, budget, and discovery
error details are omitted from this session view. Check the
`session_resources` capability before calling it.

`GET /daemon/status` is the consolidated troubleshooting snapshot. The default
`detail=summary` reads only in-memory daemon state (sessions, permissions,
SSE/ACP transport counts, rate limit rejects, process memory, resolved limits)
and does not start the ACP child. Use `GET /daemon/status?detail=full` for
per-session diagnostics, ACP connection details, auth device-flow counts, and
workspace status sections when you are actively investigating a problem.

`GET /workspace/mcp`, `GET /workspace/skills`, and `GET /workspace/providers`
report the live ACP runtime and do not start the ACP child when idle; an
idle daemon returns `initialized: false` with an empty snapshot. Once a
session is alive they switch to `initialized: true` and surface the real
state.

To update workspace Skill settings by name, call `POST /workspace/skills/:name/enable` with `{ "enabled": true | false }` after checking the `workspace_skill_settings_toggle` capability. To change several names, check `workspace_skill_settings_batch_toggle` and call `POST /workspace/skills/enable` with `{ "skillNames": ["review", "deploy"], "enabled": false }`; it processes every structurally valid name together, persists all resulting declaration changes in at most one locked settings write, and refreshes active ACP sessions once when anything changed. These routes write workspace `skills.disabled` and `skills.enabled` without consulting the loaded Skill catalog, so names may be disabled before installation or while their Extension is inactive. Enabling removes a matching workspace disable and records an explicit `skills.enabled` opt-in, even before installation, so this entry can override an Extension's internal Skill disablement. Repeating the same declaration is a no-op (`changed: false`). Their paths and request bodies are unchanged; the settings-specific capabilities replace the retired catalog-validated Skill toggle tags. The batch `errors` array remains for wire compatibility and is empty for structurally valid names. A hard `skills.disabled` entry inherited from a higher scope remains authoritative for effective availability, but does not block the workspace from recording or removing its own declaration. Skill status cells expose `disabledReason` (`hard`, `default`, or `inactive_extension`) and an optional `lockedScope`. Untrusted workspace writes are still rejected. A `deferred` response means no child was live at the liveness check or a changed request lost its child/session during the required refresh; when `changed` is true, the persisted declaration applies when a child starts. Whether a declaration actually changed is reported by `changed` (on each result for a batch request). `skills.disabled` disables both manual and model use, unlike `disable-model-invocation: true`, which keeps direct `/skill-name` invocation available. For V2 Extension batches, check `extension_batch_activation_v2`: `PUT /extensions/activation` changes global defaults, while `PUT /workspaces/:workspace/extensions/activation` changes exact overrides for the selected workspace and accepts `"inherit"` to clear them. Both accept names in `extensionNames`; `enabled` and `disabled` may be declared before installation, while `inherit` for an unknown name is a no-op. Each request returns one operation to poll. When `extension_activation_explicit_refresh` is advertised, that operation finishes after the activation policy is committed and does not directly refresh active sessions. To apply it immediately, submit the independent primary or workspace-qualified Extension refresh after activation succeeds. Without that explicit refresh, other workspaces can continue using the previous activation for up to the next 30-second generation-reconciler pass; failed reconciliation is retried by later passes.

`GET /workspace/env` and `GET /workspace/preflight` always answer with
`initialized: true` regardless of ACP state. `env` never consults ACP
(daemon-process info only); `preflight` answers daemon-level cells from
`process.*` and emits `status: 'not_started'` placeholders for ACP-level
cells when the child is idle.

`GET /workspace/env` reports the daemon process's runtime, platform, sandbox,
proxy, and the **presence** (never the value) of whitelisted secret env vars
such as `OPENAI_API_KEY`. Proxy URLs are stripped of credentials and reduced
to `host:port` before they hit the wire. The route always answers from the
daemon process directly and never spawns an ACP child.

`GET /workspace/preflight` returns a list of readiness checks. **Daemon-level
cells** (Node version, CLI entry, workspace directory, ripgrep, git, npm)
always render. **ACP-level cells** (auth, MCP discovery, skills, providers,
tool registry, egress) require a live ACP child — when the daemon is idle
they emit `status: 'not_started'` placeholders rather than spawning ACP just
to populate them. Failures map to a closed `errorKind` enum (`missing_binary`,
`auth_env_error`, `init_timeout`, `restore_timeout`, `protocol_error`, `missing_file`,
`parse_error`, `blocked_egress`) so client UIs can render structured
remediation.

The daemon also exposes workspace file helpers:

- `GET /file` reads text files. Full-snapshot responses return a raw-byte
  `sha256:<hex>` hash; finite-line windows from files above 256 KiB omit it.
- `GET /file/bytes` reads bounded raw byte windows and returns base64 content.
- `POST /file/write` creates or replaces text files.
- `POST /file/edit` applies one exact text replacement.

Write/edit are **strict mutation routes**: the token-less trusted-loopback
primary listener grants operator authority; non-trusted embeds without a
credential return `token_required`. Replacements
and edits require the latest `expectedHash` from a full-snapshot `GET /file`
(or a full-window `GET /file/bytes`). A partial large-file window cannot be
used as an optimistic-concurrency token. `create` never overwrites. Explicit
writes to ignored paths are allowed but audited. Binary writes,
delete/move/mkdir, and recursive parent creation are not part of this surface.

### 3. Open a session

```bash
curl -X POST http://127.0.0.1:4170/session \
  -H 'Content-Type: application/json' \
  -d '{}'
# → {"sessionId":"<uuid>","workspaceCwd":"…","attached":false}
```

`cwd` may be omitted — the route falls back to the daemon's primary workspace. Posting a `cwd` that does not canonicalize to any registered workspace returns `400 workspace_mismatch`.

A second client posting to `/session` for the same resolved workspace runtime gets `"attached": true` under the default `sessionScope: 'single'` — they are now sharing that runtime's agent session. Omitting `cwd` resolves to primary; selecting another registered workspace creates or attaches to that runtime's separate default session.

### 4. Subscribe to the event stream (in another terminal first)

```bash
SESSION_ID="<from step 3>"
curl -N http://127.0.0.1:4170/session/$SESSION_ID/events
# → id: 1
#   event: session_update
#   data: {"id":1,"v":1,"type":"session_update","data":{"sessionUpdate":"agent_message_chunk","content":{"type":"text","text":"…"}}}
```

The `data:` line is the **full event envelope** — `{id?, v, type, data, originatorClientId?}` — JSON-stringified on a single line. The ACP payload (the `sessionUpdate` block in this example) sits under `data` inside that envelope. The SSE-level `id:` / `event:` lines are convenience for EventSource clients; the same values appear inside the JSON envelope so raw-`fetch` consumers get them too.

Open this **before** sending the prompt — the SSE replay buffer holds the
last 8000 events so a late subscriber can catch up via `Last-Event-ID`,
but for the simple "watch a single prompt" case it's easiest to subscribe
first and let it stream live.

The stream emits `session_update` (LLM chunks, tool calls, usage),
`permission_request` (tool needs approval), `permission_resolved`
(someone voted), `model_switched`, `model_switch_failed`, and the terminal
frames `session_died` (agent child crashed — SSE then closes) and
`client_evicted` (your queue overflowed — SSE then closes).

### 5. Send a prompt (back in the original terminal)

```bash
curl -X POST http://127.0.0.1:4170/session/$SESSION_ID/prompt \
  -H 'Content-Type: application/json' \
  -d '{"prompt":[{"type":"text","text":"What does src/main.ts do?"}]}'
# → {"stopReason":"end_turn"}
```

The `curl -N` from step 4 will print frames as they arrive.

### Optional Todo Stop Guard

Long-running daemon clients can opt into a bounded continuation when the
current work chain successfully writes a top-level Todo list and then stops
with items still pending or in progress. Add this to `settings.json` and
restart the daemon:

```json
{
  "experimental": {
    "todoStopGuard": true
  },
  "tools": {
    "todoWrite": {
      "enabled": true
    }
  }
}
```

The guard adds at most two consecutive primary-model calls without new user
input. A mid-turn user message runs first and starts a fresh two-attempt stage;
retry/continue and related background results retain the current stage's
budget. Every call and the final exhaustion state appear as replayable
`session_update` events with `_meta.source: "todo_stop_guard"`; the metadata
includes the attempt and unfinished count but never Todo text. A queued full
prompt also runs first, and existing permission/cancellation rules are
unchanged.

While an armed chain waits on related background work, unrelated cron/loop
fires and old-task notifications are deferred. Recurring work is bounded and
coalesced per task until the chain yields.

The option defaults to `false`, requires restart, and is forced off in safe
mode, bare mode, and Approval `plan` mode. It is in-memory only: loading Todo
state from disk or restarting the daemon does not arm it. Set
`tools.todoWrite.enabled` in settings.json and restart Qwen Code, then a new
ordinary prompt must successfully run its own top-level `todo_write`;
retry/continue and live
client reattach keep the current in-memory work chain. Successfully changing
the session working directory clears it so an old Todo cannot resume in a new
workspace.

## Authentication

For anything beyond loopback, every API request requires a bearer token. Supply a stable one for reusable clients, or start without one and use the ephemeral token the daemon generates and prints:

```bash
export QWEN_SERVER_TOKEN="$(openssl rand -hex 32)"
qwen serve --hostname 0.0.0.0 --port 4170
# → uses QWEN_SERVER_TOKEN; generates an ephemeral token if neither token source is supplied
```

Clients then send `Authorization: Bearer <token>` on normal API requests — `$QWEN_SERVER_TOKEN` when you exported one, or the bearer printed at startup when the daemon generated it (that path never sets the env var, so copy the value out of the startup output). `/health` is exempted **only on ordinary loopback binds** so k8s/Compose liveness probes inside the pod don't need credentials. On non-loopback binds (`--hostname 0.0.0.0` etc.) `/health` requires the token with the other normal API routes — otherwise an attacker can probe arbitrary addresses to confirm the daemon's existence. Channel webhook ingress is separate in every mode and uses its configured `x-qwen-webhook-secret` instead of the daemon bearer. Use `/capabilities` to verify your configured token is correct end-to-end:

> **Hardened loopback (`--require-auth`).** The default loopback no-token behavior is fine for a single-user laptop but unsafe on shared dev hosts, CI runners, or multi-tenant workstations where any local user can `curl 127.0.0.1:4170`. Pass `--require-auth` to make the bearer token mandatory on every normal API route — including `/health` and `/capabilities` — even when bound to `127.0.0.1`. Channel webhook ingress remains independently authenticated by `x-qwen-webhook-secret`. Web Shell document and asset routes remain pre-auth; use `--no-web` to remove them. Boot fails when no token source resolves, and on loopback that means `--token`, `QWEN_SERVER_TOKEN`, or `--open-with-auth` — the last installs its own generated token before boot, so `--require-auth --open-with-auth` starts. Generation keys on the `--hostname` spelling: loopback spellings (`127.0.0.0/8`, `localhost`, `::1`, `[::1]`) never generate, while any other spelling — including a DNS name that happens to resolve to loopback — generates an ephemeral bearer that satisfies `--require-auth` and is printed at startup (token-only when the socket bound loopback), rotating on every restart, so pin `--token` or `QWEN_SERVER_TOKEN` for reusable clients. With the flag on, an **unauthenticated** client can't read `/capabilities` to discover that auth is required; the discovery surface is the 401 response body itself. Once authenticated, the `caps.features.require_auth` tag is a post-auth confirmation that the deployment is hardened (useful for audit / compliance UIs):
>
> ```bash
> qwen serve --require-auth --token "$(openssl rand -hex 32)"
> # → /health, /capabilities, /session, … all require Authorization: Bearer …
> curl http://127.0.0.1:4170/health
> # → 401
> curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:4170/capabilities | jq '.features | index("require_auth")'
> # → 13   (or whatever index — non-null after authenticating means the tag is present)
> ```

```bash
# Paste the bearer the daemon printed at startup, or your own QWEN_SERVER_TOKEN.
TOKEN='<printed bearer>'
curl -H "Authorization: Bearer $TOKEN" http://your-host:4170/capabilities
# → {"v":1,"mode":"http-bridge","features":[...],"modelServices":[],"workspaceCwd":"/path/to/your-project"}
# Wrong token → 401
```

The token comparison is constant-time (SHA-256 + `crypto.timingSafeEqual`); 401 responses are uniform across "missing header", "wrong scheme", and "wrong token" so a side-channel can't distinguish.

`--open-with-auth` is a CLI-owned convenience, not another daemon token source: it selects `--token` when that option is defined (even if blank), otherwise `QWEN_SERVER_TOKEN`, then trims the selected value and generates only if the result is empty. The daemon does not persist the generated value or export it as `QWEN_SERVER_TOKEN`; the existing internal authenticated-child handoff remains unchanged. The Web Shell stores its browser copy only in the receiving tab's `sessionStorage`; this mode adds no cross-tab or external-client credential discovery mechanism. The token is not independently revocable or tied to a client identity. Possession grants the same daemon authority as any other bearer token. See the [authenticated Web Shell launch design](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/2026-08-22-serve-open-with-auth.md>) and related future work in [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>).

## HTTPS / TLS (for mobile / cross-device access)

By default the daemon serves plain HTTP. That's fine on `localhost`, but a phone or tablet hitting a LAN IP (`https://192.168.x.x:4170`) is **not** a [secure context](<https://developer.mozilla.org/en-US/docs/Web/Security/Secure_Contexts>) over `http://` — so browsers block `getUserMedia` (voice input), WebRTC, and other secure-context-only APIs. Pass `--tls-cert` + `--tls-key` to serve the Web Shell over HTTPS and unlock them:

```bash
# 1. Install a local CA and trust it (one-time). The mobile device must
#    also trust this CA — mkcert prints where the root cert lives.
mkcert -install

# 2. Generate a cert for your machine's LAN IP. Add localhost / 127.0.0.1 to
#    the SANs too: with `--open`, the daemon rewrites the browser URL to
#    127.0.0.1, so a cert scoped to only the LAN IP would be rejected with
#    ERR_CERT_COMMON_NAME_INVALID. (mkcert names the output after all hosts.)
mkcert 192.168.1.100 localhost 127.0.0.1

# 3. Start the daemon over HTTPS. Non-loopback binds still require a token,
#    and the browser Origin must be allowed through CORS.
qwen serve \
  --hostname 0.0.0.0 \
  --token "$(openssl rand -hex 32)" \
  --tls-cert "./192.168.1.100+2.pem" \
  --tls-key "./192.168.1.100+2-key.pem" \
  --allow-origin "https://192.168.1.100:4170"
# → qwen serve listening on https://0.0.0.0:4170
```

Notes:

- **Both flags or neither** — boot fails if only one is given (a cert with no key can't start an HTTPS listener).
- **TLS is orthogonal to auth** — HTTPS encrypts the transport; when a bearer token is configured, it gates normal API routes except `/health` on an ordinary loopback bind. Pass `--require-auth` to gate loopback `/health` too. Channel webhook ingress always uses its own shared secret. Non-loopback binds always carry a token with or without TLS — the one you pass, or the ephemeral one the daemon generates and prints at startup.
- **Scope is TLS termination only** — no auto-generation, no ACME / Let's Encrypt. This is a LAN / dev convenience; for internet-facing deployments terminate TLS at a reverse proxy (see the threat model below).
- **Channel workers dial the daemon back over `https://`** — so they need to trust the serving certificate too. A self-signed cert (or a fullchain that carries its own root) needs nothing: the daemon injects it into each worker's `NODE_EXTRA_CA_CERTS`. The mkcert flow above is **CA-issued**, so the leaf alone cannot anchor the chain — export `NODE_EXTRA_CA_CERTS="$(mkcert -CAROOT)/rootCA.pem"` in the daemon's launch environment before starting with `--channel`. An operator-set value is _merged_ with the daemon cert, not replaced. Without it the daemon boots green while every channel worker restart-loops on `UNABLE_TO_VERIFY_LEAF_SIGNATURE`; the daemon log names the gap at boot.
- **An IPv6 wildcard bind is dialled back on the loopback this host actually assigns** — `--hostname ::` (or `[::]`) binds an IPv6 socket. (An empty `--hostname` never reaches the listener: boot refuses it as operator error before binding, so a compose file with an unset `HOSTNAME` fails loudly instead of silently wildcarding.) That socket is dual-stack (Node pins `IPV6_V6ONLY=0` on it, so the `net.ipv6.bindv6only` sysctl does not change this), and both loopbacks usually reach it — but a host with no IPv4 at all has only `::1`, while a host that binds `::` yet carries no `::1` on its loopback (for example `net.ipv6.conf.lo.disable_ipv6=1`) has only `127.0.0.1`. Workers are sent to `[::1]` when this host assigns it and to `127.0.0.1` otherwise. A serving certificate for an IPv6 wildcard bind should carry both loopbacks in its SANs (`mkcert localhost 127.0.0.1 ::1` covers it); the boot trust diagnostic inspects the exact URL workers will dial and names the gap. `--hostname 0.0.0.0` is unchanged and still needs `127.0.0.1`.
- **Rotating `--tls-cert` in place needs a daemon restart** — the daemon serves the bytes it read at boot, so until it restarts, respawned workers can load newer contents than the daemon presents and their handshakes fail.

## Install the Web Shell as an app

On a supported browser, open the daemon's Web Shell and use the browser's
**Install app** or **Add to Home screen** action. The installed app opens the
same server in a standalone window with the Qwen Code name and icon. On iOS,
use the browser's Share menu and **Add to Home Screen**. Availability depends
on the browser and device.

The daemon serves the PWA metadata itself: `GET /manifest.webmanifest` and
`GET /sw.js` are pre-auth routes (the browser fetches both before any
Authorization header exists), and `sw.js` is served with
`Service-Worker-Allowed: /` so its scope covers the whole origin. The worker
caches only content-hashed Vite build assets, cache-first. Unhashed public
icons and the manifest are revalidated over HTTP. API fetches, event streams
and requests carrying an authorization header stay on the network.

Use a trusted HTTPS connection for a phone or other remote device; HTTP on
`localhost` or a loopback address is supported for local development. Plain
HTTP on a LAN IP does not enable the service worker. See the TLS instructions
above; installation does not bypass certificate or authentication checks.

Installation always launches the server root. A token, session link or
workspace in the current address is never included in the app's launch URL.
You may need to enter the token again in the installed app: it uses the
existing Web Shell authentication flow and does not add persistent credential
storage.

The server must remain running and reachable. After the service worker has
activated and taken control of a page, opening the root or a session link when
the connection fails shows a retry page. No conversations, files, API
responses or credentials are saved in a service-worker cache. Existing HTTP
errors still come from the server. This first step does not provide offline
work, Web Push or notifications while the app is closed. Updated workers
activate immediately with `skipWaiting()` and `clients.claim()`, replacing
the previous worker for open tabs without forcing a page reload. Activation
removes older Qwen shell caches and preserves unrelated caches.

The installation metadata uses the built-in Qwen Code identity; runtime custom
branding still applies inside the Web Shell. Embedded library consumers do not
register the worker. To remove the installation, use your browser or operating
system's app management; to remove its service worker and stored site data,
use the browser's site settings.

## Qwen Live (experimental)

Qwen Live runs through the Web Shell on every platform: the browser tab is the microphone and speaker (WS `/live/web`), so open the Web Shell over `https` or `localhost` for microphone access. The native macOS `Qwen Live Host` app is off by default; to try it, start a macOS daemon with `QWEN_SERVE_LIVE_NATIVE_HOST=1`, which restores the `/live/host` WebSocket, the `realtime_voice` capability and the Host download when Live is turned on. Without it, a `Qwen Live Host.app` that is already installed stays unable to connect (the daemon does not register `/live/host`); quit it and use the Web Shell.

## CLI flags

| Flag                                    | Default            | Purpose                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| --------------------------------------- | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--port <n>`                            | `4170`             | TCP port. `0` = OS-assigned ephemeral port.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `--hostname <addr>`                     | `127.0.0.1`        | Bind interface. Anything beyond loopback requires a token; when neither `--token` nor `QWEN_SERVER_TOKEN` supplies one, an ephemeral token is generated and printed at startup.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `--local-control`                       | `false`            | Share the Web Shell on one selected private IPv4 interface with a daemon-owned revocable pairing token, terminal QR code, exact browser origin, and best-effort sleep inhibition. Composes with `--token`, `--allow-origin`, and `--port 0`; conflicts with `--no-web` and non-default `--hostname`. Use `--local-control-address` when multiple LAN candidates are available, and add `--tls-cert` + `--tls-key` for secure-context browser APIs such as voice input.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `--local-control-address <ip>`          | —                  | Which LAN IPv4 address to share when the host has more than one candidate. Only needed if `--local-control` reports an ambiguous choice.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `--token-qr`                            | —                  | Print the token-bearing QR even when stdout is captured (not an interactive terminal) and the bearer is an operator-supplied (stable) token, which is withheld by default to keep stable credentials out of collected logs. Can also be set via the `serve.tokenQr` setting, honored from user, system, and system-defaults scopes only; the flag wins when passed. Omitting the flag is **not** the same as `--no-token-qr`: omission leaves the default policy (QR at an interactive terminal or for a generated token), while `--no-token-qr` suppresses the startup quickstart token QR for that run on every quickstart path, so a persistent setting can be vetoed per run. Neither governs the Local Control pairing QR.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `--token <str>`                         | —                  | Bearer token. Falls back to `QWEN_SERVER_TOKEN` env var (with leading/trailing whitespace stripped — handy for `$(cat token.txt)`). A non-loopback bind with neither source generates an ephemeral token. An explicitly empty value still refuses to boot on a non-loopback bind; on loopback it shadows the env var and selects the trusted token-less mode (or refuses outright under `--require-auth`).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `--require-auth`                        | `false`            | Refuse to start without a bearer token, even on loopback. Hardens the `127.0.0.1` developer default for shared dev hosts / CI runners / multi-tenant workstations where any local user can hit the listener. That fail-fast is loopback-only: loopback boots only with one of its three token sources — `--token`, `QWEN_SERVER_TOKEN`, or `--open-with-auth`, which installs its own generated token before boot (generation is loopback-suppressed) — while on a non-loopback bind the generated ephemeral token satisfies the flag. Gates `/health` behind the bearer too.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `--enable-session-shell`                | `false`            | Enable direct session shell execution. Effective with either a configured bearer token or trusted-loopback mode; calls still require a valid session-bound `X-Qwen-Client-Id`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--tls-cert <path>`                     | —                  | Path to a PEM certificate file. Serve over **HTTPS** instead of HTTP. Must be paired with `--tls-key` (boot fails if only one is given). Unlocks secure-context browser APIs — voice input (`getUserMedia`), WebRTC — over a LAN IP, which browsers otherwise block on plain `http://`. TLS termination only; no auto-generation / ACME. See [HTTPS / TLS](<#https--tls-for-mobile--cross-device-access>) below.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--tls-key <path>`                      | —                  | Path to a PEM private key file. Must be paired with `--tls-cert`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `--max-sessions <n>`                    | `32`               | Cap on concurrent live sessions. New `POST /session` requests that would spawn a fresh child return `503` (with `Retry-After: 5`) when the cap is hit; attaches to existing sessions are NOT counted. Set to `0` to disable. Sized for single-user / small-team usage; raise it if your deployment has the RAM/FD headroom (~30–50 MB per session).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `--max-total-sessions <n>`              | `800` or derived   | Optional non-negative integer daemon-wide cap on fresh session creation across all registered workspace runtimes. It applies to new child sessions, session restore, and branch/fork-created sessions; attaching to an existing live session does not consume a slot. Set to `0` for unlimited. Defaults to 800 when registration capacity is above 25 (including the default 256). At capacities of 25 or less, one startup or restored workspace is unlimited; several derive the total from the per-workspace limit and that count. Later dynamic registration does not recompute it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `--max-pending-prompts-per-session <n>` | `5`                | Per-session cap on prompts accepted by `POST /session/:id/prompt` but not yet settled, including queued prompts and the active prompt. The bridge rejects overflow synchronously with `503`, `Retry-After: 5`, and `code: "prompt_queue_full"` before returning a `promptId`. Set to `0` to disable. `branchSession` serializes on the same FIFO but does not count against this prompt cap.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `--workspace <path>`                    | `process.cwd()`    | Absolute workspace directory registered by this daemon. Repeat the flag to host multiple workspaces in one process; the first is primary and remains the default when a request omits `cwd`. Relative values are rejected. Session requests whose canonical `cwd` is not registered return `400 workspace_mismatch`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--memory-project-scope <mode>`         | `workspace`        | Project-memory partitioning mode. `workspace` (default) keys memory by the exact registered workspace directory so each daemon workspace gets its own isolated memory; `git-root` is the legacy compatibility mode shared by workspaces resolved to the same Git root. Overrides `QWEN_CODE_MEMORY_PROJECT_SCOPE` when provided; a blank env value is treated as unset, while an unrecognized non-empty value is ignored with a one-time warning and retains the legacy `git-root` behavior. The new default does not migrate existing git-root project memory — use an explicit `git-root` scope to read those entries during migration.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `--channel <name\|all>`                 | —                  | Experimental daemon-managed channel worker. Repeat the flag to select multiple configured channels, or pass `all` to start every configured channel. `all` cannot be combined with named channels. Selected channel `cwd` values must resolve to a registered workspace; a multi-workspace daemon runs one worker per owning workspace. The worker is owned by `qwen serve`; stop the daemon to stop serve-managed channels.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `--max-connections <n>`                 | `256`              | Listener-level TCP connection cap (`server.maxConnections`). Bounds raw socket count irrespective of session count — slow / phantom SSE clients get rejected at accept time once full. Raise alongside `--max-sessions` if your deployment expects many SSE subscribers per session.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--memory-budget-mb <n>`                | 50% of cgroup/host | Total memory budget in MB for the whole daemon process tree. When unset, derived as 50% of the cgroup limit or host memory; either way the effective value is capped at resolved available memory, and both the configured and effective figures are reported. It drives the fixed ACP partition selected by `--child-heap-mode` and adaptive live-journal growth; it is not a total RSS limit: one daemon-wide growth pool derived as 5% of the effective budget (capped at `1024` MB; on hosts reporting `insufficientMemory` the pool is 0 and adaptive growth is disabled) is shared by every workspace bridge — see `--max-journal-bytes`. Resolved figures appear under `limits.memory` in `GET /daemon/status`, alongside registered and live child counts and advisory per-child shares under `runtime.memory`. A host too small for the minimum reports `insufficientMemory` rather than being clamped upward; because the derived fraction is 50%, any host under ~2 GB trips this. Pass an explicit `--memory-budget-mb 1024` on such a host to override the derived figure (the flag still requires at least 1024 MB of available memory to clear the warning). Must be an integer in `[1024, 1048576]`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--memory-pressure-mode <mode>`         | `observe`          | Whether the daemon turns its own memory reading into a verdict. `observe` (default) reports the pressure level under `runtime.memory.pressure` in `GET /daemon/status` and raises a `daemon_memory_pressure` issue — a `warning`, so the overall `status` leaves `ok` — whenever the level leaves `normal`. `off` still reports every figure, including the level, but raises no issue, so the overall `status` is unchanged; use it while calibrating, or if you alert on the top-level status. The level is the worse of two ratios: RSS against available memory (what the cgroup OOM killer watches) and V8 heap used against this process's heap ceiling. It covers the daemon root process only; compare it against `runtime.memory.children.rssBytes` for the children. Nothing remediates in either mode. One of `off`, `observe`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `--child-heap-mode <mode>`              | `observe`          | `off` disables the partition; `observe` reports `limits.memory.childHeap` without changing spawning; `admit` enforces only the modeled concurrent child count. Experimental opt-in `enforce` also gives every managed ACP child the fixed `perChildCeilingMb` old-space ceiling and reports `limits.memory.enforced: true`. Both admission modes may reclaim one eligible idle ACP before refusing with `acp_child_capacity_exhausted`; live sessions remain protected. Registration is independent of child capacity. Zero-slot admission configurations fail startup. Fixed inherited Node heap flags are normalized in `enforce`; percentage flags conflict and are rejected. The ceiling limits old space, not RSS or MCP descendants. Calibrate your workload before enabling; a zero refusal count does not prove heap safety. Mode and budget changes require a daemon restart; restart with `admit` to roll back heap enforcement.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `--event-ring-size <n>`                 | `8000`             | Per-session SSE replay ring depth (#3803 §02 target). Sets the backlog available to `GET /session/:id/events` with `Last-Event-ID: N`. Larger = more reconnect headroom at the cost of a few hundred KB extra RAM per session. SDK clients can additionally request a larger per-subscriber backlog cap on a specific subscription via `?maxQueued=N` (range `[16, 2048]`, default 256). Daemons also emit a non-terminal `slow_client_warning` SSE frame at 75% queue fill so clients can drain / reconnect before getting evicted. Pre-flight `caps.features.slow_client_warning`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--compacted-replay-max-bytes <n>`      | `4194304`          | Per-live-session byte cap for the retained replay events in the bounded snapshot returned by `POST /session/:id/load`. The cap applies to `compactedReplay`; the current in-flight `liveJournal` is separately capped by `--max-journal-events` and `--max-journal-bytes` (baseline caps that adaptive growth can raise — see `--max-journal-bytes`). Values must be positive safe integers; invalid values fail at boot, and the hard ceiling is 256 MiB. When older retained replay is dropped, the snapshot begins with `history_truncated`. This does not limit the on-disk transcript.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `--max-journal-events <n>`              | `10000`            | Per-session baseline cap on replay entries retained in the in-flight `liveJournal` for the current unfinished turn. Consecutive compatible text or thought chunks share an entry, with at most 256 source events per entry; other event boundaries are preserved. When exceeded, the daemon first tries adaptive growth (see `--max-journal-bytes`); if no headroom is granted or the grant does not cover the overshoot, the oldest entries are dropped and a `history_truncated` marker is prepended. The marker's `truncatedEvents` and `retainedEvents` counts describe source events. Must be a positive safe integer. Pinning this flag (or `--max-journal-bytes`) disables adaptive growth.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `--max-journal-bytes <n>`               | `8388608`          | Per-session baseline byte cap on the in-flight `liveJournal`, accounted from the serialized source events even when compatible chunks share a replay entry. When a turn outgrows the cap, adaptive growth raises the session's caps toward double (up to a per-session hard cap of 256 MiB, limited by the remaining pool headroom) while the growth granted across all of the daemon's live sessions fits in one shared growth pool sized at 5% of the daemon's effective memory budget — the `--memory-budget-mb` value when passed, capped at resolved available memory, otherwise 50% of auto-detected memory (see `--memory-budget-mb`) — capped at `1024` MB; on hosts reporting `insufficientMemory` the pool is 0 and adaptive growth is disabled. Growth happens on demand, and only as far as the pool allows; when it is refused, the pool is exhausted, or a grant does not cover the overshoot, the oldest entries are dropped whole (at least one entry is always kept), so the retained tail can be much smaller than the cap. Pinning this flag (or `--max-journal-events`) disables adaptive growth. Must be a positive safe integer. Defaults to 8 MiB.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `--mcp-client-budget <n>`               | —                  | Positive integer cap on live MCP clients. When `mcp_workspace_pool` is advertised, the cap and transports are shared per workspace runtime; when the tag is absent, the legacy per-session manager enforces it. Combine with `--mcp-budget-mode`. When unset, no accounting-driven enforcement (but `GET /workspace/mcp` still reports `clientCount`). Distinct from claude-code's `MCP_SERVER_CONNECTION_BATCH_SIZE`, which gates startup concurrency rather than total live clients. Pre-flight `caps.features.mcp_guardrails` and `caps.features.mcp_workspace_pool`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `--mcp-budget-mode <m>`                 | `warn` / `off`     | How `--mcp-client-budget` is enforced. `warn` (default when budget set): no refusal, snapshot's `budgets[0].status` flips to `warning` at ≥75% of budget. `enforce`: connects past the cap are refused, per-server cell shows `disabledReason: 'budget'`, deterministic by `mcpServers` declaration order. `off` (default when budget unset): pure observability. Boot rejects `enforce` without a budget.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `--external-tool-guard-mode <m>`        | `off`              | Managed ACP external pre-execution policy. `off` makes no provider calls and advertises no capability. `required` fails startup unless a compatible provider completes the v1 handshake, then fails every supported top-level tool invocation closed unless its single prepare request is allowed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `--external-tool-guard-endpoint <url>`  | —                  | Origin-only loopback HTTP(S) provider URL used in `required` mode, for example `http://127.0.0.1:8787`. Paths, URL credentials, redirects, non-loopback hosts, and proxy routing are not accepted.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `--external-tool-guard-timeout-ms <n>`  | `3000`             | Integer `100..30000`; applies independently to the startup handshake and each prepare request.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--http-bridge`                         | `true`             | Stage 1 mode: production attempts to preheat one primary `qwen --acp` child for compatibility and retries on first use after failure, while each trusted secondary can start one child on demand. Sessions targeting a runtime multiplex onto its child via ACP `newSession()`; untrusted secondaries cannot start ACP. Stage 2 native in-process becomes available later.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `--initialize-timeout-ms <n>`           | `10000`            | ACP child request timeout, including the `initialize` handshake (ms). Must be a positive integer up to `2147483647`. Values above the JS timer ceiling (`2^31-1`) are rejected at boot because Node silently compresses them to 1 ms. Cold-container deployments that need extra headroom for child startup can raise this; the same value governs `newSession`, workspace-status polls, and other ACP ext-method deadlines.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `--session-restore-timeout-ms <n>`      | `60000`            | ACP session load/resume deadline in milliseconds. Must be a positive integer up to `2147483647`; `0` is invalid. If omitted, the default is 60 seconds, raised to an explicitly supplied `--initialize-timeout-ms` when that value is larger; a shorter initialize timeout never lowers the restore budget. The SDK and Web Shell add 10 and 15 seconds of client headroom. A timeout returns retryable `504 session_restore_timeout`; it does not imply that the daemon itself exited.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `--allow-origin <pat>`                  | —                  | T2.4 ([#4514](<https://github.com/QwenLM/qwen-code/issues/4514>)). Cross-origin allowlist for browser clients. Repeatable. Each value is `*` (any origin — on loopback binds boot refuses when no bearer token is configured; on non-loopback binds a generated ephemeral token satisfies these guards, so the refusals are loopback-only; `--require-auth` on loopback is recommended so `/health` is also bearer-gated, since it is pre-auth on loopback by default; the Web Shell static assets stay pre-auth in every mode, so pass `--no-web` to remove them) or a canonical URL origin (`<scheme>://<host>[:<port>]`, no trailing slash / path / userinfo / query). Without a token, HTTP(S) origins must be loopback on loopback binds; on non-loopback binds the generated token satisfies the same guard. Remote HTTP(S) origins authenticate with the bearer on every API route; the pre-auth exceptions are only the Web Shell document/asset routes, the MCP App sandbox, the channel webhook ingress, which authenticates with its own `x-qwen-webhook-secret`, and `/health` on loopback binds (bearer-gated elsewhere). Browser extensions retain tokenless access locally. Subdomain wildcards (`https://*.example.com`) are intentionally unsupported — list each subdomain explicitly, or use `*` with a configured token (and `--require-auth` for full hardening). Matched origins receive CORS response headers (`Access-Control-Allow-Origin`, `Vary: Origin`, methods, headers, max-age, and exposed `Retry-After`); unmatched origins still get a 403 with the same envelope as today's wall. `Origin: null` (sandboxed iframes, file:// docs) is always rejected, even under `*`. Pre-flight via `caps.features.allow_origin`. Loopback self-origin hits are unaffected. |
| `--web` / `--no-web`                    | `true`             | Serve the built Web Shell SPA at the daemon root (`GET /`, `/assets/*`, `/manifest.webmanifest`, `/sw.js`, and `GET /session/<id>` document navigations). These entry points are registered **before** the bearer-auth gate — a browser can't attach a token to a `<script>` subresource or an address-bar navigation, and the shell carries no secrets. API requests follow the daemon's normal authority policy: configured tokens gate normal API routes except loopback `/health` unless `--require-auth` is set, channel webhook ingress always uses its own shared secret, and the token-less trusted-loopback primary listener has full operator access. The SPA deep-link fallback for all other paths sits behind the bearer middleware. On non-loopback binds a one-line stderr warning notes the UI is reachable without auth. Use `--no-web` for an API-only daemon. No effect when the build omits the Web Shell assets (the daemon logs a breadcrumb and runs API-only).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `--open`                                | `false`            | After the listener is up, open the Web Shell in your default browser at the daemon URL (with `#token=` appended as a URL fragment when a token resolves — configured or generated — since a fragment is never sent to the server, keeping the token out of access logs and Referer headers). No-op with `--no-web`, or in headless / CI / SSH environments where no browser is available.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `--open-with-auth`                      | `false`            | Open the Web Shell with bearer authentication on loopback. Requires an enabled Web Shell and built assets. Reuses a configured token or generates a process-lifetime 256-bit bearer and delivers it in the Web Shell fragment. Browser-ineligible environments print the secret-bearing manual URL. Other clients need the same explicitly configured shared token instead.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

> **Memory project scope caveats.**
>
> - **Daemon vs. standalone CLI.** The flag or daemon launch environment sets
>   one frozen scope for every runtime owned by that daemon. A workspace `.env`
>   or `settings.env` cannot override it for one registered workspace. A
>   standalone `qwen` TUI still defaults to git-root scope; to keep both entry
>   points consistent, export `QWEN_CODE_MEMORY_PROJECT_SCOPE` in the shell or
>   service environment that launches them.
> - **Directory-name collisions.** The storage key is derived by
>   `sanitizeCwd`, which replaces every non-alphanumeric character with
>   `-`. Sibling directories that differ only in punctuation (e.g.
>   `feature_1` and `feature-1`) map to the same memory directory even
>   under `workspace` scope. Avoid such naming when relying on workspace
>   isolation.
> - **Normalization differs between flag and env var.** The environment variable
>   is trimmed and lowercased (`"  Workspace  "` works); the CLI flag is
>   matched case-sensitively by yargs `choices` (`--memory-project-scope
Workspace` is rejected). Use lowercase values when copying between the two.

### Built-in daemon Git relocation guard

Every managed daemon ACP session applies a built-in pre-execution guard for
model shell commands, independent of `--external-tool-guard-mode` and without
any capability advertisement. The daemon owns the bound workspace and the
session's current effective working directory; both are supplied from trusted
session state and never accepted from the ACP child.

The guard inspects the tools that run a shell command line — `run_shell_command`
and `monitor` — and denies a mutating Git
command before execution when its repository location resolves outside the
session's effective working directory. Relocation is recognized for literal
forms of `git -C <path>`, `git --git-dir[=]<path>`,
`git --work-tree[=]<path>`, leading
`GIT_DIR`/`GIT_WORK_TREE`/`GIT_COMMON_DIR`/`GIT_INDEX_FILE` assignments (also
when made through `export`/`declare`/`readonly`, which keep them in the
environment of every later command in the chain),
directory-shifting wrapper flags (`env -C`, `sudo -D`), and `cd`, `pushd`, or
`popd` builtins earlier in the same command chain. Common wrapper prefixes
(`sh -c`, `bash -c`, `eval`, `sudo`, `nohup`, `timeout`, `exec`, `command`,
`builtin`,
`env`, path-qualified `git` binaries, and `{ …; }` / `! …` shell syntax) are
unwrapped so the same policy applies to the inner Git invocation, and `$(…)`
or backtick substitution bodies are analyzed as commands of their own.

A sub-agent pinned to its own worktree is contained to that worktree rather
than to the session's directory; a shell call whose execution directory the
daemon cannot place is denied.

Relative targets resolve from the command's effective starting directory
(`arguments.directory` when present, otherwise the session's current effective
working directory) after canonical path resolution, including `.git` gitfile
redirects, symlinks, and per-worktree administrative directories. A relocated
target that cannot be fully resolved before execution — a dynamic target
(`$VAR`, backticks, `~`, globs), a path that does not exist yet, or an
unreadable indirection — is denied for mutating or unclassifiable subcommands.
A relocated target that cannot be resolved is denied whatever the subcommand
is — including the read-only ones. Relocated commands whose subcommand is one
of a small verified read-only set (`rev-parse`, `cat-file`) remain allowed
once the target resolves, unless the command carries command-executing `-c`
config, or
it carries a `--output`, `--textconv`, or `--filters` flag: those write a file
or run the target repository's configured drivers. Commands with no recognized
relocation keep their existing behavior.
Denials are final and are reported to the model as
`Daemon shell guard denied a mutating Git command…` for a resolved, dynamic,
or unresolvable repository location, and as
`Daemon shell guard denied a shell command…` when the command could not be
parsed, its payload could not be resolved, or an unrecognized program may run
a relocated Git command.

The guard is reliable against Git relocation written in the literal forms
above — the mis-targeted command this control exists for — and is
**best-effort, not a boundary**, against shell text written to defeat it:
constructions that hide the relocation from a static reader may pass, and new
ones will keep being found. Do not grant a daemon broader trust on the
strength of it. It does not interpret script files,
track environment variable values across commands, or analyze heredoc bodies
(Git-shaped text inside a heredoc can be denied even though the shell never
executes it). `/fork` and agent-backed workspace memory remember/dream remain
available under the built-in guard; they are only restricted while the
external provider mode below is active. An optional external tool guard
remains an additional policy and receives the same request only after the
built-in policy allows it.

### Required external Tool Guard

This opt-in is for managed ACP deployments that need an external allow/deny
decision at the final tool-executor boundary. It is fully dark unless
`--external-tool-guard-mode=required` is present:

```sh
export QWEN_CODE_EXTERNAL_TOOL_GUARD_TOKEN='replace-with-local-secret'

qwen serve \
  --external-tool-guard-mode=required \
  --external-tool-guard-endpoint=http://127.0.0.1:8787 \
  --external-tool-guard-timeout-ms=3000
```

The provider must expose `POST /v1/handshake` and `POST /v1/prepare`, require
`Authorization: Bearer <token>`, return JSON, echo the supplied nonce or
request ID, and use protocol version `1`. The token must be non-blank, at most
8192 UTF-16 code units, and contain no control characters. Requests are limited
to 1 MiB, responses to 64 KiB, and optional denial reasons to 500 UTF-16 code
units without control characters. A successful prepare response is:

```json
{ "protocolVersion": 1, "requestId": "<echo>", "allowed": true }
```

A denial uses `allowed:false` and may add a short `reason`. For each supported
top-level tool invocation that passes existing permission and `PreToolUse`
gates and reaches the final execution boundary, Qwen Code sends one prepare
request and never retries it. An earlier permission/hook denial sends no
prepare request. Timeout, cancellation, transport failure, malformed or
mismatched responses, and explicit denial prevent the executor from running.
Each spawned ACP channel must also acknowledge that it installed the required
callback; a missing or incompatible acknowledgement rejects the channel before
Session creation.
The provider request carries `sessionId`, `promptId`, `toolCallId`, canonical
`toolName`, and final `arguments`; `toolCallId` is a correlation label, not an
authentication identity or standalone idempotency key.

Final arguments can contain sensitive application data. Treat them as such in
provider logs and audit storage.

`PreToolUse` hooks run before this final executor decision. Required Guard mode
does not authorize or sandbox hook behavior; deployments that need a boundary
around every possible side effect must disable hooks or govern their
implementations separately.

Slash-command actions also run before model/tool scheduling and are not Guard
invocations. Some built-ins can directly change files or settings. A managed
deployment that needs an all-effects boundary must reject slash-command input
or disable every non-approved command through `slashCommands.disabled` or
`--disabled-slash-commands`.

The v1 managed scope is top-level tools invoked by an active foreground
managed Prompt. Nested or delegating `agent`, `workflow`,
`create_sub_session`, `send_message`, direct `/fork`, and agent-backed
workspace memory remember/dream controls are rejected while required mode is
active. A top-level background shell or monitor start is still one guarded
invocation and its final arguments reach the provider, but this feature does
not continuously authorize the process or add a process-completion audit
protocol; a policy that requires foreground completion should deny those
shapes. Guarded MCP calls also disable automatic reconnect/replay after a
transport error. After a successful startup handshake, `/capabilities`
advertises `external_tool_guard`; its absence means clients must not assume
enforcement.

This feature does not authorize explicit daemon REST/ACP management calls;
those continue to use the daemon's existing authentication and route
contracts. It also does not make an allowed tool or shell command
deterministic or sandbox its internals; managed deployments must combine the
provider decision with their normal tool policy and isolation boundary.

> **Sizing the load knobs.** `--max-sessions` is the per-workspace fresh-session cap. `--max-total-sessions` is the daemon-wide fresh-session cap; when omitted it defaults to 800 at the default registration capacity of 256.
> Three other layers also limit load — when sizing for a high-concurrency
> deployment, tune them together:
>
> - **listener-level**: `--max-connections` / `server.maxConnections=256`
>   bounds raw TCP connections (slow-client back-pressure).
> - **per-session subscribers**: the EventBus caps SSE subscribers at
>   64 per session by default; the 65th client gets a terminal
>   `stream_error` and is closed.
> - **per-session prompt admissions**:
>   `--max-pending-prompts-per-session=5` bounds queued + active prompts
>   accepted for one session. Overflow gets `503` with `Retry-After: 5`.
> - **daemon-wide fresh sessions**: `--max-total-sessions=N` bounds fresh
>   session creation across the daemon. Overflow gets the same
>   `session_limit_exceeded` shape with `scope: "total"`.
> - **per-subscriber backlog**: a 256-frame queue per SSE client; an
>   over-capacity client gets a terminal `client_evicted` frame and is
>   closed (one slow consumer can't pin the daemon).
>
> These caps interact: each runtime is bounded by `--max-sessions`, while
> `--max-total-sessions` bounds their aggregate. The effective session ceiling
> is the lower of any finite daemon-wide cap and the aggregate per-runtime cap
> (treat that aggregate as unlimited if the per-workspace cap is unlimited). If
> neither is finite — which at the default registration capacity requires
> `--max-total-sessions 0` — there is no finite session ceiling. A finite ceiling × 64
> subscribers × 256 frames is the worst-case in-flight memory at the EventBus
> layer; multiplying it by
> `--max-pending-prompts-per-session` bounds accepted prompt work at the
> admission layer. Default sizing assumes single-user / small-team load; raise
> progressively (and watch RSS) for larger deployments.

> **MCP client guardrails (issue [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14).** A workspace declaring 30 MCP servers in `mcpServers` will start 30 clients with no upstream cap unless you set one. `--mcp-client-budget=N` caps the live MCP client count; `--mcp-budget-mode={enforce,warn,off}` chooses the behavior. Default is `warn` when a budget is set (snapshot surfaces the warning but no client is refused — useful for measuring real-world fanout before flipping on enforcement). Refused servers under `enforce` mode get `disabledReason: 'budget'` on their per-server cell, and the `budgets[0]` cell shows `status: 'error'` + `errorKind: 'budget_exhausted'`. Slot reservation is by server name and survives reconnects / discovery timeouts — a refused server can't take a slot from a healthy one.
>
> **Current scope is capability-driven.** When `mcp_workspace_pool` is present, all sessions in one workspace runtime share its MCP transport pool and budget controller; `GET /workspace/mcp` emits `scope: 'workspace'`. A second workspace has an independent pool and budget. When the tag is absent (including `QWEN_SERVE_NO_MCP_POOL=1`), the daemon uses the legacy per-session `McpClientManager` and emits `scope: 'session'`; in that fallback, N sessions can each consume the configured cap.
>
> ```sh
> qwen serve --mcp-client-budget=10 --mcp-budget-mode=warn
> # later, after telemetry shows your real-world distribution:
> qwen serve --mcp-client-budget=10 --mcp-budget-mode=enforce
> ```
>
> This is **not** the same as claude-code's `MCP_SERVER_CONNECTION_BATCH_SIZE` (which gates startup concurrency); they are orthogonal. Clients must branch on `mcp_workspace_pool`, not assume a scope from the protocol version alone.
>
> **Push events (issue [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 14b).** SDK clients subscribed to `GET /session/:id/events` receive typed frames when budget thresholds cross — `mcp_budget_warning` (synthetic, fires once per upward 75% crossing with hysteresis re-arm at 37.5%, advertised via `mcp_guardrail_events`) and `mcp_child_refused_batch` (coalesced once per discovery pass under `enforce` mode; length-1 from `readResource` lazy-spawn refusal). The snapshot at `GET /workspace/mcp` is still the source-of-truth for state-after-reconnect; events are change-edges. Useful when dashboarding in real-time without polling.

## Default deployment threat model

- **Loopback only** — `127.0.0.0/8`, `localhost`, or `::1`; no auth needed.
- **`localhost` is resolved once, pinned, and verified after listen** — if its actual listener address is outside `127.0.0.0/8` or `::1`, token-less startup closes the listener and fails.
- **`--hostname 0.0.0.0` requires a token** — one is generated when neither token source is supplied; explicitly empty sources still fail closed.
- **`LOOPBACK_BINDS` includes IPv6** — `::1` and `[::1]` count as loopback for the no-token rule.
- **Host header allowlist** — on **loopback** binds the daemon checks `Host:` matches `localhost:port` / `127.0.0.1:port` / `[::1]:port` / `host.docker.internal:port` or the exact bound loopback address and port (case-insensitive per RFC 7230 §5.4) to defend against DNS rebinding. When listening on port 80 or 443, the corresponding port-less forms are also accepted because browsers omit scheme-default ports. This supports the complete IPv4 loopback range (`127.0.0.0/8`) without admitting unrelated Hosts. **Non-loopback binds (`--hostname 0.0.0.0`) intentionally bypass the Host allowlist** — the operator has chosen the surface area, so the bearer-token gate is the sole authentication layer for normal API routes; reverse proxies / SNI / client cert pinning are the operator's responsibility, not the daemon's. If you need Host-based isolation on a non-loopback bind, terminate TLS + check Host at a front proxy.
- **CORS denies any browser Origin by default** — returns `403` JSON. Pass **`--allow-origin <pattern>`** (repeatable, T2.4 #4514) to opt specific browser origins through. Each value is either the literal `*` (any origin — on a **loopback** bind boot refuses if no bearer token is configured, while a non-loopback bind boots because token generation supplies one; `--require-auth` on loopback is recommended for full hardening since `/health` remains pre-auth on loopback by default — note that the Web Shell static assets (`/`, `/assets/*`, `/manifest.webmanifest`, `/sw.js`, `/session/:id`, `/plugins`, `/channels`, `/live`, `/scheduled-tasks`, `/goals`, and `/settings` document navigations) are mounted before the bearer in every mode and stay pre-auth even under `--require-auth`, so use `--no-web` when the residual browser surface matters) or a canonical URL origin (`<scheme>://<host>[:<port>]`, no trailing slash / path / userinfo). On a **loopback** bind without a token, HTTP(S) entries must use a loopback host; a non-loopback bind always carries a token — supplied or generated — precisely because remotely hosted browser origins can execute code through the operator API as the daemon user. Explicit browser-extension origins retain their tokenless local-automation path. Matched origins receive proper CORS response headers (`Access-Control-Allow-Origin: <echoed>`, `Vary: Origin`, plus standard methods / headers / max-age and exposed `Retry-After`); unmatched origins still get a 403 with the same envelope as the default wall. `caps.features.allow_origin` is advertised conditionally so SDK / webui clients can pre-flight whether the daemon honors cross-origin hits before issuing them. Example: `qwen serve --allow-origin http://localhost:3000 --allow-origin http://localhost:5173`. Loopback self-origin hits (e.g. the Web Shell UI) are unaffected — a separate Origin-strip shim handles them regardless of `--allow-origin`. The built-in remote Web Shell's same-origin **HTTP** requests also work without `--allow-origin`: on a non-loopback primary listener an `Origin` equal to the direct socket scheme plus the normalized `Host` authority is bearer-authenticated and then stripped ahead of the CORS wall, while its existing public static routes stay unauthenticated. Two exclusions still need `--allow-origin` — forwarded headers never establish same-origin trust, so a TLS-terminating reverse proxy's `https` browser origin is not same-origin (and neither is a plain-HTTP intermediary that rewrites the `Host` header, such as nginx's default `proxy_set_header Host $proxy_host` or k8s Ingress; the remedy there is `--allow-origin <origin>` for the origin your browser sees, or a proxy that forwards `Host` verbatim — port translation alone needs nothing, because only the forwarded `Host` is compared, with just the scheme-default `:80`/`:443` stripped), and the WebSocket upgrade routes (terminal, voice) keep a separate CSWSH gate that admits only loopback origins, allowlisted origins, and the Local Control listener's own origin.
- **Chrome extension browser automation is separate from framing.** `qwen serve --allow-origin chrome-extension://<id>` lets the extension frame the Web Shell and connect to the daemon. Console/network/screenshot/click tools require an external CDP MCP adapter command: `QWEN_CDP_MCP_COMMAND=/path/to/cdp-mcp-adapter qwen serve --allow-origin chrome-extension://<id>`. The main CLI package does not bundle a browser automation adapter; clients can check `caps.features.includes('browser_automation_mcp')` before presenting those tools as available.
- **A spawned `qwen --acp` child receives its owning runtime's effective environment.** The daemon freezes a process-env base, applies that workspace's settings/env-file overlay to a runtime-local snapshot, and never writes the overlay back to `process.env`; same-named keys in another runtime do not cross over. `QWEN_SERVER_TOKEN` is scrubbed before spawn because the agent does not need the daemon bearer. Loader-affecting variables (`NODE_OPTIONS`, `npm_config_node_options` and npm's config-file redirects, `NODE_PATH`, `OPENSSL_CONF`, `NODE_REPL_EXTERNAL_MODULE`, `npm_config_node_gyp`, `npm_config_init_module`, `LD_PRELOAD`, `LD_AUDIT`, `DYLD_INSERT_LIBRARIES`, `BASH_ENV`, `ZDOTDIR`, exported bash function definitions `BASH_FUNC_*`) are likewise never passed to session subprocesses — the daemon scrubs them from its own `process.env` and from the frozen base env that session-hosting children spawn with (the base env keeps them only under the `DEV=true` harness, whose `.ts` entries still need the tsx loader), and `.env` / `settings.json` `env` sources reject them (see [settings](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings.md>)); this applies to every session the daemon hosts. Base credentials such as `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `QWEN_*`, and `DASHSCOPE_API_KEY` otherwise pass through unless the runtime overlay changes them. **This is intentional, not a sandbox.** The agent runs as the same UID with shell-tool access, so anything in `~/.bashrc`, `~/.aws/credentials`, or `~/.npmrc` is reachable by prompt injection regardless. Environment isolation between runtimes is not an operating-system security boundary; do not run `qwen serve` under an identity that has credentials you would not trust the agent with.
- **Agent text reads are child-local and follow the regular CLI permission rules, not the workspace filesystem boundary.** Direct `read_file` can reach host text paths outside every registered workspace: external paths default to confirmation, and allow rules or approval modes may approve them automatically. Approved reads use the configurable CLI output limits rather than the workspace filesystem's returned-output, full-snapshot, and large-text scan caps. This applies to every shared text-read consumer, so the pre-reads performed by write, edit, notebook, sed, and artifact operations lose those caps together with the workspace filesystem's read audit, symlink rejection, and read-side TOCTOU protections — see [the read design](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/daemon-local-text-reads.md>) for the exact list. Because a confirmation payload is built by reading the file, an out-of-workspace diff is fanned out to **every** attached SSE subscriber before anyone approves it — in the interactive CLI that content is seen only by the person at the terminal. Treat authenticated daemon clients as the same security principal. HTTP filesystem routes remain workspace-scoped and agent discovery-tool behavior is unchanged.
- **Approved final writes from built-in text tools have a narrow same-host route.** `write_file`, `edit`, `notebook_edit`, and the shell tool's simulated sed editor attach internal provenance only after the existing permission policy allows execution. Their final ACP text write can therefore target an absolute path outside the owning workspace without a second confirmation; allow rules, AUTO/AUTO_EDIT and YOLO behave like the CLI, while rejection, Plan, Hook/Guard refusal and pre-execution cancellation do not send the final write. Cancellation after a tool has already entered a non-cancellable filesystem operation keeps that tool's existing behavior. Workspace targets still use WFS. External targets use a daemon host writer with the same trust snapshot, 5 MiB encoded limit, leaf-symlink rejection, canonical path lock, atomic rename, mode preservation, `0600` new-file mode by default (configurable — see [New-file mode for agent text writes](<#new-file-mode-for-agent-text-writes>)), generation guard and filesystem audit. HTTP writes, generic or unmarked ACP writes, injected bridge/workspace-registry/factory integrations and arbitrary shell redirection do not receive this exception. See [the external-write design](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/design/daemon-external-tool-text-writes.md>).
- **Per-subscriber bounded SSE queues** — a slow client that overflows its queue gets a `client_evicted` terminal frame and is closed; one stuck consumer can't pin the daemon.
- **Per-session prompt admission cap** — defaults to 5 accepted-but-unsettled prompts per session. A buggy client cannot enqueue unbounded prompt promises or temporary SSE waits for one session.
- **Graceful shutdown** — SIGINT/SIGTERM drain the agent children before closing the listener (10s deadline per child).

> ⚠️ **Permission votes are session-scoped now — plus a standing shared-bearer caveat.** Prefer `POST /session/:id/permission/:requestId` (Wave 4 / F3): it routes to the runtime that owns the named live session and never falls back to the primary bridge — new multi-workspace integrations should always use this route (pre-flight `caps.features.session_permission_vote`). The legacy process-global `POST /permission/:requestId` remains for pre-F3 single-workspace clients: it is primary-bound and `404`s for a session owned by another registered runtime — the same body as a lost vote under the default `first-responder` policy — so a `404` there does not by itself mean the request was already answered. Route table: [daemon REST API reference](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/daemon-rest-api-reference.md#permissions>). Standing caveat: every client holding the bearer token is the same security principal — don't run `qwen serve` behind a bearer shared with untrusted parties.
>
> ⚠️ **Stage 1 known gap — `POST /session/:id/prompt` body capped at 10 MB (BUy4L).** Multimodal prompts containing images / PDFs / audio that exceed 10 MB will fail at body-parse time before route logic runs (no streaming, no mid-upload abort). Workaround: shrink the content client-side, or pass a path reference and let the agent read the file via `readTextFile`. Stage 1.5 will accept `multipart/form-data` or chunked encoding on `/prompt` so large prompts don't hit a cliff.
>
> ⚠️ **Stage 1 known gap — phantom SSE connections behind NAT.** The
> daemon detects dead clients via TCP back-pressure on heartbeats
> (15s interval). A client that vanishes WITHOUT a TCP RST (e.g. a
> NAT box silently dropping idle flows) keeps the kernel-level socket
> "alive" until Node's keepalive probes time out — typically ~2 hours
> on Linux defaults. On `--hostname 0.0.0.0` deployments behind such
> NATs, phantom SSE connections can accumulate and eventually hit the
> 256 `server.maxConnections` ceiling.
>
> Set [`--writer-idle-timeout-ms <n>`](<#deadlines-and-writer-idle-timeout>)
> (issue [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>) T2.9)
> to close the gap with an explicit application-level idle deadline:
> when no write has successfully flushed for `n` ms the daemon emits
> a terminal `client_evicted` frame with
> `reason: 'writer_idle_timeout'` and closes the stream. The flag is
> off by default to preserve the legacy contract — operators on
> networks that swallow RSTs should pick a value well above the 15s
> heartbeat interval (e.g. `60000`–`300000`) so legitimate idle
> connections aren't evicted while genuinely stuck writers are
> reaped promptly. Pre-flight `caps.features.includes('writer_idle_timeout')`
> from your SDK to confirm the daemon supports it.

### Deadlines and writer idle timeout

Issue [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>) T2.9 ships two opt-in flags that close the long-running / remote-deployment gaps the 15s heartbeat + AbortSignal don't cover. The shared permission-response timeout is listed here as well. All three are off by default.

| Flag                                   | Env var                             | Default | What it does                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| -------------------------------------- | ----------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--prompt-deadline-ms <n>`             | `QWEN_SERVE_PROMPT_DEADLINE_MS`     | unset   | Server-side wallclock cap on a single `POST /session/:id/prompt`. On expiry the daemon aborts the prompt's AbortController and returns HTTP `504` with `{code:"prompt_deadline_exceeded", errorKind:"prompt_deadline_exceeded", deadlineMs:n}`. A per-prompt request body field `deadlineMs` can SHORTEN the effective deadline below the flag but never extend it. Capability tag (conditional): `prompt_absolute_deadline`.                                                                                                                                                                                                |
| `--writer-idle-timeout-ms <n>`         | `QWEN_SERVE_WRITER_IDLE_TIMEOUT_MS` | unset   | Per-SSE-connection idle deadline. When no write has SUCCESSFULLY flushed for `n` ms — neither a real event nor the 15s heartbeat — the daemon emits a terminal `client_evicted` frame with `data.reason = 'writer_idle_timeout'` (mirrored on `data.errorKind`) and closes the stream. **Pick a value comfortably above the 15s heartbeat** (e.g. `30000`–`300000`) so legitimate idle streams aren't evicted; values `< 15000` WILL evict otherwise-healthy idle connections before the first heartbeat fires (intentional only for tests / short-lived dev sessions). Capability tag (conditional): `writer_idle_timeout`. |
| `--permission-response-timeout-ms <n>` | —                                   | `0`     | Shared wallclock for ordinary permission and `ask_user_question` responses in daemon mode. `0` or an omitted flag waits indefinitely; a positive integer imposes a deadline on both. Voter cancellation, session cancellation, and daemon shutdown still resolve pending interactions when the timer is disabled.                                                                                                                                                                                                                                                                                                            |

The prompt and writer flags accept a positive integer in milliseconds; `0`, `NaN`, non-integer, or negative values are rejected at boot with a clear error message. The permission-response timeout accepts `0` or a positive integer. For the two environment-backed deadlines, explicit `ServeOptions` fields win over env values. SDK consumers should pre-flight the matching capability tag before relying on the prompt and writer behaviors.

### New-file mode for agent text writes

Agent text writes (`write_file`, `edit`, `notebook_edit`, and the shell tool's simulated sed editor) publish through the daemon's atomic writer, which preserves an existing target's mode and — for **new** files — defaults to owner-only `0600`, ignoring the daemon process's umask. This fail-closed default is intentional: a fresh agent-created file is never group/world readable by accident, no matter how permissive the supervisor umask is.

Operators whose deployment convention is umask-driven (e.g. a systemd unit with `UMask=0002`, shared-group repositories) can opt new files into the standard POSIX handling with:

| Env var                    | Values              | Default | What it does                                                                                                                                                                                                                                                                                                                                                                                                        |
| -------------------------- | ------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `QWEN_SERVE_NEW_FILE_MODE` | `owner` \| `system` | `owner` | `system` creates NEW files at `0o666 & ~umask`, so agent-created files follow the daemon process's umask like any other process on the machine. `owner` keeps the umask-independent `0600` default. Values are case-insensitive; the literal `0600` is accepted as an alias for `owner` (no other octal modes are supported), and any other value is rejected with a stderr warning and the `0600` default is kept. |

Scope and limits:

- Applies to NEW files created by the text-write routes (workspace targets, the same-host external host writer, and HTTP text writes). Existing files always keep their on-disk mode — editing a `0600` secret keeps it `0600`, an executable keeps `+x`.
- Binary uploads (`POST /file/upload`) always create at `0600` regardless of this setting.
- The daemon reads the variable at workspace-filesystem construction; restart the daemon after changing it.

### Session attachment storage

Session attachments (files and images uploaded by Web Shell through `POST /session/:id/attachments`) are stored by default under the workspace's runtime temp dir: `<runtimeBaseDir>/tmp/<projectHash>/attachments`, keyed per session as `session-<sessionId>`. Operators who want attachments to persist outside the runtime temp dir (e.g. on a dedicated volume) can override the root:

| Env var                               | Values | Default | What it does                                                                                                                                                                                                |
| ------------------------------------- | ------ | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `QWEN_SERVE_SESSION_ATTACHMENTS_ROOT` | path   | unset   | Stores session attachments under this directory instead of the default runtime temp dir. Accepts an absolute path, a path relative to the daemon's cwd, or `~` / `~/…` expanded against the home directory. |

Scope and limits:

- **One-way migration.** When the env is set, new attachments are written only under the configured root. Reads and removes that miss the configured root fall back to the default runtime temp dir, so attachments uploaded **before** the switch remain readable, and removable while the default fallback dir stays writable — a remove whose legacy copy cannot be unlinked (e.g. a read-only fallback volume) surfaces the error rather than reporting success. The reverse direction — removing the env after attachments were written to the configured root — makes those attachments unreachable; keep the variable stable for a given workspace.
- **Per-session layout.** Files live under `<root>/<projectHash>/attachments/session-<sessionId>/` in both locations, where `<projectHash>` is the same workspace hash used by the default runtime temp dir; the fallback lookup uses the same session layout in the default dir. Two workspaces pointing at the same configured root stay isolated from each other.
- **Delete cleanup.** When a session is deleted, its attachment directory is removed from both the configured root and the default fallback dir. Archiving a session keeps its attachments so they survive unarchive.
- The daemon reads the variable at startup; restart the daemon after changing it. The directory must be writable by the daemon process.

## Multi-session & multi-workspace deployment

Pass `--workspace` more than once to register several non-overlapping workspaces in one `qwen serve` process. The first path is primary. Each registered workspace owns an isolated runtime boundary, while the daemon-wide listener, authentication policy, and total-session limit are shared. Production attempts to preheat the primary ACP child for compatibility and retries on first use after failure; trusted secondaries start their own child on demand, and untrusted secondaries do not start ACP. Requests may select a registered workspace by canonical `cwd`; requests that omit `cwd` use the primary workspace. Use one daemon per user or security principal; workspace trust is an execution gate, not an ACL.

An untrusted secondary workspace is visible in Web Shell as `untrusted` and `read-only`. It can be expanded to inspect the persisted session catalog, but it cannot yet be selected or opened in Web Shell, resumed, used to create sessions, or fully exported. The REST API follows the existing bounded filesystem read policy and also exposes its persisted session-group catalog and, when `workspace_persisted_transcript` is advertised, its active persisted transcript through the bounded workspace-qualified pager. Forward and cursor reads do not start an ACP child; a backward first page for a live session may start one to flush the persisted tail. Full workspace-qualified export requires a trusted workspace and the separate `workspace_session_export` capability. Trust the workspace and restart the daemon before using execution, mutation, or export features. An untrusted primary remains disabled in Web Shell.

Use separate daemon processes when you need a smaller fault or security boundary, independent bearer tokens, quotas, audit boundaries, operating-system isolation, or independent resource supervision. Multi-workspace mode is intended for one operator hosting several repos; it is not a multi-tenant isolation boundary. A single daemon token authorizes every bearer-gated API route the daemon exposes, including the allowed read-only catalog for all registered workspaces.

> **Subscribe BEFORE posting `modelServiceId` on attach.** When a client `POST /session` with a `modelServiceId` and the workspace already has a session running a different model, the daemon issues an internal `setSessionModel` call — failures are NOT propagated as an HTTP error (the session stays operational on its current model). The visible failure signal is a `model_switch_failed` event on the session's SSE stream. If you call `POST /session` and only THEN open `GET /session/:id/events`, you'll miss the failure event and silently keep talking to the wrong model. Open the SSE stream first, or pass `Last-Event-ID: 0` on subscribe to replay the ring's oldest available event.

To handle multiple **users or security principals** (each with an independent token, quota, audit log, sandbox, or process fault boundary) or to scale beyond one process's reach (cold-start budget, FD count, RSS), spawn one daemon per principal behind an external orchestrator. Each such daemon may still host several workspaces for that principal. The orchestrator (multi-tenancy / OIDC / Quota / Audit / k8s) is **out of scope** for the qwen-code project — see issue [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>) "External Reference Architecture" for the design pointers.

## Loading and resuming a persisted session

The daemon exposes ACP's `session/load` and resume flow over HTTP, plus a separate read-only transcript pager:

| Route                                                   | Use when                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `POST /session/:id/load`                                | The client has **no** useful local history rendered (cold reconnect, picker-then-open). For a live session, the daemon returns and injects the current bounded replay snapshot window; if older replay was dropped, the snapshot begins with `history_truncated`. Capability tag: `session_load`.                                                               |
| `POST /session/:id/resume`                              | The client already has the turns on screen and only needs the daemon-side handle back. Model context is restored on the agent side without UI replay — the SSE stream stays clean. Capability tag: `session_resume` (`unstable_session_resume` remains a deprecated alias for older clients).                                                                   |
| `GET /session/:id/transcript`                           | The client needs the complete active persisted transcript. It returns id-less replay frames in cursor pages and does not call `/load`, attach a client, seed the live EventBus, create a live session, or change the live replay window. Capability tag: `session_transcript`.                                                                                  |
| `GET /workspaces/:workspace/session/:id/transcript`     | The client needs an active persisted transcript from a selected workspace. Forward and cursor pages stay daemon-local; a backward first page for a live session may start ACP and load workspace settings to flush the persisted tail. Registered untrusted secondary workspaces may use this read-only path. Capability tag: `workspace_persisted_transcript`. |
| `GET /workspaces/:workspace/session/:id/export`         | The client needs a complete `html`, `md`, `json`, or `jsonl` attachment from a selected trusted workspace. It reads active persisted storage without starting ACP or falling back to primary. Capability tag: `workspace_session_export`.                                                                                                                       |
| `GET /workspaces/:workspace/session/:id/archive/export` | The client needs the same attachment formats from archived persisted storage in a selected trusted workspace. It does not unarchive, start ACP, or fall back to an active or primary session. Capability tag: `workspace_archived_session_export`.                                                                                                              |

For load and resume, the TypeScript SDK exposes static factories on
`DaemonSessionClient`:

```ts
import { DaemonClient, DaemonSessionClient } from '@qwen-code/sdk';

const client = new DaemonClient({ baseUrl: 'http://127.0.0.1:4170' });

// Cold reconnect — daemon will replay the bounded snapshot window through SSE.
const session = await DaemonSessionClient.load(client, 'persisted-id');

// Or, if your UI already has the history, skip the replay:
// const session = await DaemonSessionClient.resume(client, 'persisted-id');

for await (const event of session.events()) {
  // First the replayed `session_update` frames (load only),
  // then live events.
}
```

Pre-flight `caps.features.session_load`, `caps.features.session_resume`, or `caps.features.session_transcript` before calling the matching route — older daemons return `404`. `unstable_session_resume` is still advertised as a deprecated compatibility alias. Concurrent same-action requests for the same id coalesce; cross-action races (a `load` racing a `resume`) and caller-supplied-id spawns racing a restore get `409 restore_in_progress` with `Retry-After: 5`. A restore that exceeds `limits.sessionRestoreTimeoutMs` gets retryable `504 session_restore_timeout` with a budget-derived `Retry-After` (clamped to 5-120s); the still-running child request remains fenced until cleanup settles, and same-id retries during that window get `409 restore_in_progress` with `reason: awaiting_abandoned_cleanup` and a budget-derived `Retry-After` clamped to 5-120 seconds instead of a fixed 5-second delay. If cleanup is uncertain, or the abandoned restore has still not settled a full restore budget after its deadline, fresh session work temporarily gets `503 acp_channel_unavailable` with `reason: restore_cleanup_failed` or `restore_settlement_overdue`. A session initialization that times out but does not settle promptly can similarly return `reason: new_session_cleanup_failed` when late cleanup is inconclusive or `new_session_settlement_overdue` when the child request remains outstanding for another initialization budget. Already-live sessions remain usable for all four reasons. See the [protocol reference](<Qwen Code 守护进程 HTTP 协议参考.md>) for the full error envelope.

For full persisted replay, page with `DaemonClient.getSessionTranscriptPage(sessionId, { cursor, limit })` or the raw REST route:

```bash
curl "http://127.0.0.1:4170/session/$SESSION_ID/transcript?limit=100"
```

For a registered workspace, use `client.workspaceById(workspaceId).getSessionTranscriptPage(sessionId, { cursor, limit })` or `/workspaces/:workspace/session/:id/transcript`. The workspace-qualified method always uses native REST even when the SDK client has a replaceable ACP transport. Its cursors are daemon-lifetime-only and must be restarted from page one after a daemon restart. A backward first page for a live session may start ACP and load workspace settings to flush the persisted tail before reading it.

For a full attachment from a trusted registered workspace, pre-flight `workspace_session_export` and call `client.workspaceById(workspaceId).exportSession(sessionId, { format: 'html' })` or the raw `/workspaces/:workspace/session/:id/export` route. Do not infer support from `session_export` or `workspace_qualified_rest_core`: older daemons can advertise both while retaining primary-only export. The current Web Shell export action remains primary-only; use the SDK or REST route for another workspace.

For an archived attachment, pre-flight `workspace_archived_session_export` and call `client.workspaceById(workspaceId).exportArchivedSession(sessionId, { format: 'html' })` or `/workspaces/:workspace/session/:id/archive/export`. This path reads archived storage in place and returns `409 session_not_archived` for an active-only id; it does not unarchive the session. Web Shell exposes the same export for archived rows in trusted primary and secondary workspaces when the capability is present.

`limit` is the target number of active chat records, not emitted replay frames; one record can produce several `session_update` events. A backward page may expand to at most `3 * limit` records to preserve turn and tool-call/result boundaries. The first response freezes the JSONL snapshot size and returns `nextCursor` while `hasMore` is true. Later pages ignore appends after page 1, but return `409` if the file is deleted, truncated, replaced, archived, or otherwise conflicts with the frozen cursor. Very large snapshots return `413 transcript_too_large` before indexing so the daemon does not scan unbounded transcript files on the request path.

For repeated paging through the legacy singular route, set `--channel-idle-timeout-ms` to a positive value. With the default `0`, an idle workspace's ACP child — and the in-process transcript index cache it holds — is reaped after every page, so each page re-spawns the child and rebuilds the index by re-scanning the whole frozen prefix (`O(snapshotSize)` per page). A positive timeout keeps the child alive across the cursor walk so it reuses its cached transcript index and replay config, unless `--child-heap-mode admit` or `enforce` reclaims it to satisfy a capacity-constrained spawn in another workspace. In that case, the next page re-spawns the child and rebuilds the index. Workspace-qualified forward and cursor pages stay daemon-local; only a backward first page for a live session crosses the flush barrier.

Note: live-session history replay is bounded twice: by the SSE ring for `Last-Event-ID` reconnects and by `--compacted-replay-max-bytes` for the snapshot returned by `POST /session/:id/load`. Long histories with chatty turns can exceed either bound. The daemon surfaces snapshot truncation with `history_truncated`; use `/transcript` when you need the complete active persisted history.

## Durability model

**Sessions are still ephemeral in Stage 1 across daemon restarts**, but persisted sessions on disk can be reloaded:

- A child process crash publishes `session_died` and removes the live session from the daemon's maps. The persisted on-disk session **can** be reloaded via `POST /session/:id/load` if a fresh agent child is spawnable.
- A daemon restart loses every in-flight live session. The persisted sessions remain on disk and can be loaded against a new daemon process, subject to the same workspace binding rules.
- Long client disconnects (>5 min on a chatty turn) can outrun the SSE replay ring (default 8000 frames) — `Last-Event-ID` reconnect triggers `state_resync_required`. For mobile / flaky-network clients, plan to re-open SSE on long drops or call `POST /session/:id/load` to recover the current bounded replay snapshot; do not assume that route returns the full transcript.
- File operations (`writeTextFile`) are atomic across crashes (write-then-rename); they aren't atomic across daemon restarts in the sense of replaying — the file write either landed or it didn't.

If your integration needs server-side cross-restart durability beyond what `session/load` covers (e.g. server-managed retry queues), you still need application-level state recovery. Don't hold long-running, restart-sensitive state inside the daemon's session.

## Stage 1.5+ runtime guarantees

Stage 1's contract is sized for prototyping. Per [#3889 chiga0 downstream-consumer review](<https://github.com/QwenLM/qwen-code/pull/3889#issuecomment-4427875644>), the following are **not** in Stage 1 — production-grade integrations need Stage 1.5+ before relying on them:

**Blockers for serious downstream use:**

1. **`loadSession` / `unstable_resumeSession` over HTTP** — without this, no integration can survive a child crash or daemon restart, and any orchestrator coordinating the daemon can't recover state either.
2. **Persistent client identity (pair tokens + per-client revocation)** — Stage 1 uses one shared bearer; a leaked token revokes everyone, and `originatorClientId` is client-self-declared rather than daemon-stamped from authenticated identity.

**Reliability baseline:**

3. ~~**Client-initiated heartbeat path**~~ — shipped via [#4175](<https://github.com/QwenLM/qwen-code/issues/4175>) PR 9. `POST /session/:id/heartbeat` records last-seen timestamps on the daemon (capability tag `client_heartbeat`); SDK helpers are `DaemonClient.heartbeat()` / `DaemonSessionClient.heartbeat()`.
4. **`permission_already_resolved` event** when a vote loses the first-responder race — currently UIs have to infer state from a `404`.
5. ~~**Larger replay ring**~~ — bumped to 8000. **Per-session-configurable ring** still open — mobile / chatty-turn workloads may need per-session overrides.
6. **`slow_client_warning` event before `client_evicted`** — soft backpressure so well-behaved slow clients can self-throttle (trim render depth, drop chunks) before being terminated.

**Integration ergonomics:**

7. **`POST /session/:id/_meta` for IM-style context** — per-session key-value attached to subsequent prompts (chat id, sender, thread id) replaces the per-channel improvisation.
8. **`/capabilities` actual feature negotiation** — `protocol_versions: { acp: '0.14.x', daemon_envelope: 1 }` so clients can detect drift instead of falling through to "unknown frame, ignore".
9. **First-class durability documentation** (this section) — already shipped above.

The full convergence roadmap is tracked on [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>).

## Stage 1 scope boundaries — what we won't fix in Stage 1.5

Two structural choices are explicit non-goals for the Stage 1 / 1.5 / 2 main-line roadmap. If your use case depends on either, plan around them rather than waiting for us.

### Session state is local-mutation-only (per [LaZzyMan review #4270256721](<https://github.com/QwenLM/qwen-code/pull/3889#pullrequestreview-4270256721>))

The Stage 1.5 plan describes TUI as an in-process EventBus subscriber. In practice **TUI UI is strictly larger than the wire protocol**:

- **Local-only UI** — the ~15 Ink dialog components (`ModelDialog`, `MemoryDialog`, `PermissionsDialog`, `SessionPicker`, `WelcomeBackDialog`, `FolderTrustDialog`, …) and the `local-jsx` slash commands (`/ide`, `/auth`, `/init`, `/resume`, `/rename`, `/delete`, `/language`, `/arena`, …) render terminal-specific Ink JSX. Remote clients on HTTP/SSE can't equivalently render Ink, and these flows emit no wire event.
- **Session-state mutations without wire events** — `/approval-mode`, `/memory add`, `/mcp add-server`, `/agents`, `/tools enable/disable`, `/auth`, `/init` (writing `CLAUDE.md`) all change agent behavior, but only `/model` currently publishes an event (`model_switched`).

**Stage 1 choice — option (A) from the review**: don't promote these mutations to wire events. The two deployment modes have different consequences.

#### Mode 1 — headless `qwen serve` (this PR)

No TUI shell runs inside the daemon. The slash commands listed above **don't exist** in this mode — there's no terminal UI to issue them from. Session state is therefore:

- **Boot-time-frozen** for `approval-mode` / `memory` / `agents` / `tools` allowlist / `auth` — all loaded from settings + disk when the daemon's `qwen --acp` child starts; immutable for the session's lifetime. Settings-defined MCP servers are likewise frozen at boot, but **runtime-added servers** (via `POST /workspace/mcp/servers`) can be added or removed without restart.
- **Mutable over HTTP** via `POST /session/:id/model` (publishes `model_switched`), `POST /workspace/mcp/servers` / `DELETE /workspace/mcp/servers/:name` (publishes `mcp_server_added` / `mcp_server_removed`), and permission votes (`POST /session/:id/permission/:requestId`; the legacy process-global `POST /permission/:requestId` remains for pre-F3 single-workspace clients).

**Consequence:** remote clients in headless mode see the **full session state**. No TUI hides additional state; no drift is possible. If you want to change `approval-mode`, restart the daemon with new settings. MCP servers can now be added/removed at runtime via the mutation routes (`POST /workspace/mcp/servers`, `DELETE /workspace/mcp/servers/:name`) — see [Runtime MCP server management](<#runtime-mcp-server-management-issue-4514>).

#### Mode 2 — Stage 1.5 `qwen --serve` co-hosted TUI (not in this PR)

When Stage 1.5 lands `qwen --serve` (TUI process co-hosts the same HTTP server), the TUI **does** exist alongside remote clients. A local operator typing `/approval-mode yolo` or `/mcp add-server` mutates session state, and remote clients on HTTP have no event to observe the change.

In this mode, TUI is a **"super-client"** — it observes the same agent conversation remote clients see, AND can mutate session state remote clients can't. The asymmetry is:

- ✅ Both TUI and remote clients see the same agent messages, tool calls, file diffs, permission prompts.
- ❌ Only TUI sees / mutates approval-mode / memory / MCP server list / agents / tools allowlist / auth state.

**Consequence in Mode 2:** if a remote-client UI tries to mirror session settings, it can drift after any TUI slash command. Remote clients should **re-fetch state on attach / reconnect** (use `Last-Event-ID: 0` to replay the ring's oldest event for things like `model_switched`); they should NOT rely on incremental events for TUI-side mutations.

#### Why (A) and not (B) (promote mutations to `session_state_changed` event family)

(B) is the more ambitious answer but locks Stage 1.5 into a substantially larger wire surface that must also pass cleanly through the planned in-process refactor. We'd rather walk the smaller scope honestly. The session-state-event taxonomy work — enumerating which TUI flows are local-only by design vs. could plausibly graduate to wire under a future opt-in (B)-flavor extension — moves to [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>), not Stage 1.5 code.

### N parallel sessions share one `qwen --acp` child per workspace runtime

Multiple sessions on the same trusted workspace **share that runtime's `qwen --acp` child process** via the agent's native multi-session support (`packages/cli/src/acp-integration/acpAgent.ts:194: private sessions: Map<string, Session>`). The bridge calls `connection.newSession({cwd, mcpServers})` for each session — the agent stores them in its sessions map and demultiplexes per-call sessionId. Production can own up to one primary child (preheat attempted by default) plus one on-demand child per trusted secondary; untrusted secondaries own none.

Concrete cost at N=5 sessions on the same workspace:

| Resource                             | Per session                                           | At N=5                                                           |
| ------------------------------------ | ----------------------------------------------------- | ---------------------------------------------------------------- |
| Daemon Node process                  | one                                                   | **30–50 MB** (one daemon)                                        |
| `qwen --acp` child                   | shared                                                | **60–100 MB** (one child)                                        |
| MCP server children                  | workspace pool when advertised; otherwise per-session | shared by matching pool entries, or up to 3×N in legacy fallback |
| `FileReadCache` (in-child heap)      | shared                                                | parsed once                                                      |
| `CLAUDE.md` / hierarchy memory parse | shared                                                | parsed once                                                      |
| OAuth refresh-token state            | shared                                                | **one refresh path**                                             |
| Auto-memory learned facts            | shared                                                | one knowledge base per child                                     |
| Cold start                           | first only                                            | <200 ms after first session                                      |

Each active workspace runtime keeps **one bridge boundary**. Production attempts to preheat the primary channel and retries on first use after failure; a trusted secondary opens its channel and child on demand, while an untrusted secondary never does. A channel stays alive while at least one session is live. After the last `killSession`, the runtime kills its child immediately by default or after the configured channel idle grace; a channel-level crash also tears it down without selecting another runtime.

**MCP server children** use the workspace-scoped transport pool when `mcp_workspace_pool` is advertised: matching `(workspace runtime, server name, config fingerprint)` entries are refcounted across sessions. If the capability is absent, the legacy per-session manager independently spawns them.

**Peer agents (Cursor / Continue / Claude Code / OpenCode / Gemini CLI) all do single-process multi-session.** qwen-code matches them at the agent layer; the Stage 1 bridge in this PR makes the same architecture visible over HTTP.

## Logging in to a remote daemon (issue #4175 PR 21)

When the daemon runs on a remote pod (no shared display with you), a client can
trigger an OAuth device flow over HTTP. The daemon polls the IdP itself; your job
is just to open a URL on whatever device has a browser.

> [!note]
>
> Qwen OAuth free tier was discontinued on 2026-04-15. The `qwen-oauth`
> examples below document the device-flow protocol shape and legacy provider
> identifier; new setups should use a currently supported auth provider.

```bash
# 1. Start a flow. The daemon contacts the IdP, returns a code + URL.
curl -X POST http://127.0.0.1:4170/workspace/auth/device-flow \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"providerId":"qwen-oauth"}'
# → 201 {
#     "deviceFlowId": "fa07c61b-…",
#     "userCode": "USER-1",
#     "verificationUri": "https://chat.qwen.ai/api/v1/oauth2/device",
#     "verificationUriComplete": "https://chat.qwen.ai/...?user_code=USER-1",
#     "expiresAt": 1700000600000,
#     "intervalMs": 5000,
#     "attached": false
#   }

# 2. Visit the URL on your phone / laptop, enter the user code.
# 3. Poll for completion (or subscribe to SSE for the auth_device_flow_authorized event):
curl http://127.0.0.1:4170/workspace/auth/device-flow/fa07c61b-… \
  -H "Authorization: Bearer $TOKEN"
# → status transitions: pending → authorized
```

The TypeScript SDK wraps both steps into a single helper:

```ts
import { DaemonClient } from '@qwen-code/sdk';

const client = new DaemonClient({ baseUrl, token });
const flow = await client.auth.start({ providerId: 'qwen-oauth' });
console.log(`Open ${flow.verificationUri}\nCode: ${flow.userCode}`);
const result = await flow.awaitCompletion({ signal: abortCtrl.signal });
// result.status === 'authorized'
```

**The daemon never opens a browser on your behalf.** Even when running locally, the daemon stays passive — it returns the URL and lets the SDK / user choose where to open it. This is intentional: a daemon on a headless pod that called `xdg-open` would silently fail, masking the actual auth surface. Mirror `gh auth login`'s "Press Enter to open browser" UX in your client.

**`--require-auth` and dev convenience.** Device-flow routes use the strict mutation gate. The token-less trusted-loopback default may start and manage a flow directly; a non-trusted embed without credentials returns `401 token_required`. Set a managed `QWEN_SERVER_TOKEN` and pass `--require-auth` on shared hosts, CI runners, remote development machines, or anywhere local processes are not mutually trusted.

**Cross-daemon limitation.** `oauth_creds.json` is daemon-shared (`~/.qwen/oauth_creds.json`), so a successful login in daemon A is automatically picked up by daemon B's next token refresh — but daemon B's SDK clients won't receive the `auth_device_flow_authorized` event (events are per-daemon).

**Cross-client take-over.** Two SDK clients on the same daemon that both `POST /workspace/auth/device-flow` for the same provider get the per-provider singleton: the first call starts a fresh IdP request and returns `attached: false`; the second call returns the EXISTING in-flight entry with `attached: true`. The take-over is recorded on the audit trail (under the second client's `X-Qwen-Client-Id`) but does NOT emit a separate event — both clients eventually observe the SAME `auth_device_flow_authorized` once the user finishes the IdP page. If your UI distinguishes "I started this" from "someone else's flow I joined", branch on the `attached` field returned by `start()`.

## Daemon log file

`qwen serve` appends diagnostic records across normal restarts at the stable
active path:

```
${QWEN_RUNTIME_DIR or ~/.qwen}/debug/daemon/daemon.log
```

Every file record includes a random per-start `runId` and the daemon PID. A
successful stable owner also updates `debug/daemon/latest` to `daemon.log` on
platforms that support symlinks. On macOS/Linux, follow rotation with:

```bash
tail -F ~/.qwen/debug/daemon/daemon.log
```

On other platforms, configure the viewer to reopen the pathname after it is
replaced. A viewer that keeps only the old file handle will remain on the
archive after rotation.

The log captures lifecycle messages, route errors (with `route=` and `sessionId=` context), ACP child stderr, and — when `QWEN_SERVE_DEBUG=1` is set — extra bridge breadcrumbs. Lines that go to stderr today still go to stderr; the file log is **additive**, not a replacement.

Failed standalone creation adds `Standalone session creation failed.` with the validated target `sessionId`, failed `phase`, safe `reason`, `dispatchState` and `cleanupOutcome`. Child creation retains the child ID and records the validated parent separately as `relatedSessionId`. Correlate `source_persistence_failed` lines by session ID for private source acknowledgement or transport details; `write_not_confirmed` does not identify a filesystem cause. A completed quarantine is distinct from rollback, and `unknown` does not establish cleanup success. Public error codes and retry behavior remain unchanged. These diagnostics omit raw causes, prompts and source payloads.

The active file rotates before it would exceed 10 MiB. Each family retains
four archives under `archive/`, and each file record is capped at 256 KiB. The
in-memory queue accepts at most 4 MiB of unsettled file payload. Queue pressure,
rotation failures, or filesystem failures can therefore drop file copies;
`GET /daemon/status?detail=full` exposes logger health, issues, and dropped
record/byte counters.

Only one daemon may own the stable family in a log namespace. A concurrent
daemon writes to `debug/daemon/runs/run-<runId>/daemon.log`; the startup banner
and full status contain the authoritative path. `runs/recent-fallback` is a
best-effort locator for a recent fallback family and may point to one that is
still live. A healthy namespace converges to roughly 100 MiB: about 50 MiB for
stable plus one inactive fallback family. Live or not-yet-stale fallback
families are retained, so concurrent daemons or crash/restart storms can
temporarily use more.

One runtime directory is one ownership and retention namespace. Use distinct
`QWEN_RUNTIME_DIR` values when daemons need independent history. New daemon log
directories are private to the user (`0700`) and new files use `0600` on POSIX.
There is no age-based expiry.

### Disabling

Set `QWEN_DAEMON_LOG_FILE=0` (or `false`/`off`/`no`) to skip file logging entirely. Stderr output is unaffected.

### Relation to session debug logs

Session-scoped debug logs (`~/.qwen/debug/<sessionId>.txt` and the `~/.qwen/debug/latest` symlink) are independent. The daemon log lives in a sibling `daemon/` subdirectory; per-session debug semantics are unchanged by this feature.

### External rotation

Do not point an external logrotate rule at the active `daemon.log`. The daemon
is the sole supported writer and rotator; external rename, deletion, or
truncation invalidates its size model. Copying or shipping records without
mutating the family is safe. Older `serve-<pid>.log` and
`serve-<pid>-<workspaceHash>.log` files are left untouched and are not counted
by the new retention policy.

## Runtime MCP server management (issue [#4514](<https://github.com/QwenLM/qwen-code/issues/4514>))

Add or remove MCP servers at runtime without restarting the daemon. Runtime entries live in an ephemeral overlay that **shadows** settings-defined servers of the same name; the underlying `settings.json` / `mcpServers` config is never written to.

**Pre-flight:** check `caps.features` for `mcp_server_runtime_mutation` before calling either route. Older daemons without this tag return `404`.

### `POST /workspace/mcp/servers` — add a runtime MCP server

Strict-gated (trusted-loopback primary or operator credentials required). Connects the server immediately via the live `McpClientManager` and discovers its tools.

Request:

```json
{
  "name": "my-server",
  "config": {
    "command": "npx",
    "args": ["-y", "@my-org/mcp-server"]
  }
}
```

`name` must be alphanumeric plus `_` and `-` (max 256 characters). `config` is the same MCP server configuration object used in `settings.json` `mcpServers` entries (transport-dependent fields: `command`/`args` for stdio, `url` for SSE/HTTP). Security-sensitive fields (`trust`, `env`, `cwd`, `oauth`, `headers`, `authProviderType`, `includeTools`, `excludeTools`, `type`) are stripped by the daemon and ignored.

Response (200) — success:

```json
{
  "name": "my-server",
  "transport": "stdio",
  "replaced": false,
  "shadowedSettings": false,
  "toolCount": 3,
  "originatorClientId": "client-1"
}
```

- `replaced: true` — a runtime entry with the same name already existed and the config fingerprint differs; old connection torn down, new one established. When the fingerprint matches (idempotent re-add), `replaced` is `false`.
- `shadowedSettings: true` — a settings-defined server with the same name exists; the runtime entry now shadows it. The settings entry is untouched and re-emerges if the runtime entry is later removed.
- `toolCount` — number of tools discovered on the newly connected server.

Response (200) — soft refuse (budget warning mode):

```json
{
  "name": "my-server",
  "skipped": true,
  "reason": "budget_warning_only"
}
```

Returned when `--mcp-budget-mode=warn` and adding the server would exceed the configured `--mcp-client-budget`. The server is NOT connected. Callers should surface the budget pressure to the user.

Errors:

| Status | Code                      | When                                                                                               |
| ------ | ------------------------- | -------------------------------------------------------------------------------------------------- |
| `400`  | `invalid_server_name`     | Name empty, exceeds 256 chars, or contains characters outside `[A-Za-z0-9_-]`                      |
| `400`  | `missing_required_field`  | `config` missing or not a non-null object                                                          |
| `400`  | `invalid_client_id`       | `X-Qwen-Client-Id` header present but not registered for this workspace                            |
| `400`  | `invalid_config`          | Config shape rejected by the MCP transport validator                                               |
| `401`  | `token_required`          | Non-trusted token-less request denied by the strict gate                                           |
| `409`  | `mcp_budget_would_exceed` | `--mcp-budget-mode=enforce` and budget is full                                                     |
| `502`  | `mcp_server_spawn_failed` | Server process exited or timed out during connect; body carries `serverName`, `exitCode`, `stderr` |
| `503`  | `acp_channel_unavailable` | No live ACP child (no session has been created yet)                                                |

### `DELETE /workspace/mcp/servers/:name` — remove a runtime MCP server

Strict-gated. Disconnects the server and removes it from the runtime overlay. Idempotent — removing a name that was never added returns a skip response (not an error).

The `:name` path parameter is the URL-encoded server name.

Response (200) — success:

```json
{
  "name": "my-server",
  "removed": true,
  "wasShadowingSettings": false,
  "originatorClientId": "client-1"
}
```

- `wasShadowingSettings: true` — the removed runtime entry was shadowing a settings-defined server of the same name. That settings entry is now un-shadowed and will be used on next discovery/restart.

Response (200) — idempotent skip:

```json
{
  "name": "ghost",
  "skipped": true,
  "reason": "not_present"
}
```

Returned when the name was not in the runtime overlay (it may still exist in settings — settings entries cannot be removed via this route).

Errors:

| Status | Code                      | When                                                                          |
| ------ | ------------------------- | ----------------------------------------------------------------------------- |
| `400`  | `invalid_server_name`     | Name empty, exceeds 256 chars, or contains characters outside `[A-Za-z0-9_-]` |
| `400`  | `invalid_client_id`       | `X-Qwen-Client-Id` header present but not registered for this workspace       |
| `401`  | `token_required`          | Non-trusted token-less request denied by the strict gate                      |
| `503`  | `acp_channel_unavailable` | No live ACP child                                                             |

### Shadow semantics

Runtime entries form an ephemeral overlay on top of settings-defined MCP servers:

- **Adding** a runtime server with the same name as a settings entry **shadows** it — the runtime config takes precedence. The original settings entry is not modified.
- **Removing** a runtime server that was shadowing a settings entry **un-shadows** it — the settings-defined config becomes active again on next connection.
- **Daemon restart** loses all runtime entries. Only settings-defined servers survive across restarts. Runtime servers are session-lifetime scoped.
- **`GET /workspace/mcp`** reports the merged view — both settings-defined and runtime servers appear in the `servers[]` array. There is no wire-level distinction between the two origins in the snapshot today.

### Events

Both routes emit **workspace-scoped** SSE events (all active session buses receive them):

| Event                | Emitted when                    | Payload fields                                                                         |
| -------------------- | ------------------------------- | -------------------------------------------------------------------------------------- |
| `mcp_server_added`   | `POST` succeeds (not skipped)   | `name`, `transport`, `replaced`, `shadowedSettings`, `toolCount`, `originatorClientId` |
| `mcp_server_removed` | `DELETE` succeeds (not skipped) | `name`, `wasShadowingSettings`, `originatorClientId`                                   |

Skipped responses (`budget_warning_only`, `not_present`) do NOT emit events.

Budget-related events from the existing `mcp_guardrail_events` surface (`mcp_budget_warning`, `mcp_child_refused_batch`) also fire when runtime additions cross the budget threshold.

## What's next

- **Setting up a long-running daemon?** [Local launch templates (systemd / launchd / nohup / tmux)](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/qwen-serve-deploy-local.md>) for v0.16-alpha (local-only).
- **Build a client?** See the [DaemonClient TypeScript quickstart](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/daemon-client-quickstart.md>) and the [HTTP protocol reference](<Qwen Code 守护进程 HTTP 协议参考.md>).
- **Reading the source?** Bridge code lives at `packages/cli/src/serve/`; SDK client at `packages/sdk-typescript/src/daemon/`.
- **Tracking the roadmap?** Stage 1.5 / Stage 2 progress is tracked on issue [#3803](<https://github.com/QwenLM/qwen-code/issues/3803>).


</details>
