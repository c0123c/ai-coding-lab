# Codex MCP 接入

介绍支持的 MCP 能力、添加服务、认证与配置方式，按 CLI、桌面和网页说明操作入口。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/extend/mcp>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/extend/mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex MCP 接入.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Model Context Protocol

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Model Context Protocol (MCP) 将模型连接到工具和上下文。使用它
授予ChatGPT或Codex对第三方文档的访问权限，或者让它
与浏览器或Figma等开发人员工具交互。

ChatGPT网络可以使用插件提供的远程MCP-支持的工具。本地Codex
客户端还可以直接连接到MCP服务器并共享其配置。

<a id="supported-mcp-features"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

ChatGPT 桌面应用程序、Codex CLI 和 IDE 扩展支持 MCP 服务器和 
 共享同一 Codex 主机的 MCP 配置。

以下支持的服务器功能适用于在 Codex
 主机上配置的 MCP 服务器。托管插件工具可以具有不同的功能。

## 支持的 MCP 功能

- **STDIO 服务器**：作为本地进程运行的服务器（由命令启动）。
  - 环境变量
- **流式HTTP服务器**：您通过某个地址访问的服务器。
  - 承载令牌认证
  - OAuth 身份验证，包括客户端 ID 元数据文档 (CIMD) 和
动态客户端注册（DCR）
  - ChatGPT 受信任的第一方服务器的会话身份验证
- **服务器指令**：Codex读取初始化期间返回的MCP`instructions`字段，并将其与服务器工具一起用作服务器范围的指导。

如果您为 Codex 构建或维护 MCP 服务器，请使用 `instructions` 来实现跨服务器应用的跨工具工作流程、约束和速率限制。保持第一个 512 字符独立，以便在 Codex 决定如何使用服务器时提供最重要的指导。

## 将 Codex 连接到 MCP 服务器

Codex 将 MCP 配置与其他 Codex 配置设置一起存储在 `config.toml` 中。默认情况下，这是 `~/.codex/config.toml`，但您也可以将 MCP 服务器范围限定为具有 `.codex/config.toml` 的项目（仅限受信任的项目）。

ChatGPT 桌面应用程序、Codex CLI 和 IDE 扩展共享此配置。
配置 MCP 服务器后，您可以在这些客户端之间切换，而无需 
重做设置。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

### 在ChatGPT桌面应用程序中配置

1. 打开**设置**，然后选择**MCP服务器**。
2. 选择**添加服务器**。
3. 输入名称，选择 **STDIO** 或 **Streamable HTTP**，然后提供
服务器的命令或URL。
4. 保存服务器，然后选择**重新启动**。

服务器列表显示哪些服务器已启用以及哪些服务器需要 OAuth。当 OAuth 服务器需要登录时，选择
**身份验证**。在编辑器中，输入 `/mcp`
 以查看连接的服务器。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="web">

## 在 ChatGPT 网络中使用 MCP- 支持的工具

使用本地计算机访问与另一台设备上的 Work Cloud 继续对话不会安装插件或授予对连接帐户的访问权限。

在使用 Work Cloud 进行本地计算机访问时，Agent 安全性中的企业 `mcp_servers` TOML 要求适用于本地执行器。云容器保留 Work Cloud 策略。本地 MCP 服务器和传输可能在云中不可用；分别检查连接和身份验证。

对于使用 Work Cloud 进行本地计算机访问中的企业挂钩：

- **企业挂钩。** 在为工作区启用的情况下，使用 Work Cloud 进行本地计算机访问支持管理员定义的 MCP 挂钩，这些挂钩在云协调器（协调器）上运行，以实现受支持的生命周期和工具事件。云编排不支持命令挂钩以及来自本地配置或插件的挂钩，即使工具在本地执行也是如此。
- **审核。**在依赖这些钩子之前，测试回调连接，确认它接收到的事件，并检查故障如何影响任务。 MCP 挂钩不提供完整的合规性 API 审核跟踪。
- **限制：**云编排不支持来自本地配置或插件的命令挂钩和挂钩，即使工具在本地执行也是如此。消费者在此功能中没有钩子支持。当编排和执行都是本地时，现有支持的挂钩将继续在仅限本地的工作和 Codex 线程中工作。管理员仍然可以在 Agent 安全性中为这些工作流程配置受支持的托管挂钩。

请参阅 [Hooks](<https://learn.chatgpt.com/docs/hooks>) 和 [ 配置参考 ](<https://learn.chatgpt.com/docs/config-file/config-reference>) 了解支持范围。

在托管的 ChatGPT 工作聊天中，安装 [ 插件 ](<https://learn.chatgpt.com/docs/plugins>) 以使用其 
 捆绑连接器和远程 MCP 工具。安装后，聊天和工作可以
使用这些工具。工作区管理员可以控制哪些插件和工具
可用。

ChatGPT web 不会读取本地 Codex 配置文件或公开本地 
Codex 命令菜单。打开 ** 插件 ** 选项卡以浏览和管理可用的 
 工具。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

### 使用CLI进行配置

#### 添加 MCP 服务器

```bash
codex mcp add <server-name> --env VAR1=VALUE1 --env VAR2=VALUE2 -- <stdio server-command>
```

例如，要添加 Context7（用于开发人员文档的免费 MCP 服务器），您可以运行以下命令：

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
```

#### 其他CLI命令

运行`codex mcp list`以查看配置的服务器。要查看所有可用的 MCP
 命令，请运行 `codex mcp --help`。对于支持 OAuth 的服务器，运行
`codex mcp login <server-name>`。

#### 终端 UI (TUI)

在 `codex` TUI 中，使用 `/mcp` 查看活动的 MCP 服务器。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

### 在IDE扩展中配置

1. 打开齿轮菜单，然后选择**MCP服务器**。
2. 选择**添加服务器**。
3. 输入名称，选择 **STDIO** 或 **Streamable HTTP**，然后提供
服务器的命令或URL。
4. 保存服务器，然后选择**重新启动扩展**。

MCP 服务器列表显示哪些服务器已启用以及哪些服务器需要 OAuth。
当 OAuth 服务器需要登录时，选择 ** 身份验证 **。

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

### 配置config.toml

要进行更细粒度的控制，请编辑 `~/.codex/config.toml` 或项目范围的 
`.codex/config.toml`。有关每个受支持的 MCP 选项的可搜索列表，请参阅 [ 配置参考 ](<https://learn.chatgpt.com/docs/config-file/config-reference>)
。

在配置文件中使用 `[mcp_servers.<server-name>]` 表配置每个 MCP 服务器。

</ContentModeSwitch>

<a id="stdio-servers"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

#### STDIO 服务器

- `command`（必需）：启动服务器的命令。
- `args`（可选）：传递给服务器的参数。
- `env`（可选）：为服务器设置的环境变量。
- `env_vars`（可选）：允许和转发的环境变量。
- `cwd`（可选）：启动服务器的工作目录。
- `experimental_environment`（可选）：设置为`remote`以启动stdio
当远程执行器环境可用时，通过远程执行器环境运行服务器。

`env_vars` 可以包含纯变量名称或具有源的对象：

```toml
env_vars = ["LOCAL_TOKEN", { name = "REMOTE_TOKEN", source = "remote" }]
```

字符串条目和 `source = "local"` 从 Codex 的本地环境读取。
`source = "remote"` 从远程执行器环境读取并需要 
 远程 MCP stdio。

</ContentModeSwitch>

<a id="streamable-http-servers"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

#### 可流式传输的 HTTP 服务器

- `url`（必填）：服务器地址。
- `auth`（可选）：配置承载令牌后尝试进行身份验证
授权标头。使用 `oauth`（默认值）存储 MCP OAuth
 凭据。使用 `chatgpt` 将当前 ChatGPT 会话用于受信任的 
 第一方 ChatGPT 源，并使用存储的 OAuth 作为后备。
- `bearer_token_env_var`（可选）：要在 `Authorization` 中发送的不记名令牌的环境变量名称。
- `http_headers`（可选）：标头名称到静态值的映射。
- `env_http_headers`（可选）：标头名称到环境变量名称（从环境中提取的值）的映射。
- `http_headers_helper`（可选）：打印 JSON 对象的本地命令
标头名称和字符串值，例如 `{"X-Auth": "temporary-token"}`.
 支持从本地环境建立的 HTTP MCP 连接；不适用于
 stdio 服务器或通过远程执行环境建立的连接。

Codex 缓存连接的辅助标头。同源 POST
 返回 `401` 或 `403` 后，它会刷新一次标头，并仅在 
 帮助程序返回更改的值时重试。显式承载令牌和 OAuth 凭据 
 优先于帮助程序提供的 `Authorization` 标头。
报告范围不足的 OAuth `403` 响应不会触发 
 帮助程序刷新。

如果没有解析任何凭据源，Codex 无需 
 身份验证即可连接到服务器。单独运行`codex mcp login <server-name>`以启动MCP
OAuth登录。

#### 其他配置选项

- `startup_timeout_sec`（可选）：服务器启动超时（秒）。默认值：`10`。
- `tool_timeout_sec`（可选）：服务器运行工具的超时（秒）。默认值：`60`。
- `enabled`（可选）：设置`false`以禁用服务器而不删除它。
- `required`（可选）：设置`true`以使启用的服务器无法初始化时启动失败。
- `enabled_tools`（可选）：工具允许列表。
- `disabled_tools`（可选）：工具拒绝列表（在 `enabled_tools` 之后应用）。
- `default_tools_approval_mode`（可选）：默认批准行为
来自该服务器的工具。支持的值为 `auto`、`prompt`、`writes` 和 
 `approve`。 `writes` 模式提示未标记为只读的工具。
- `tools.<tool>.approval_mode`（可选）：每个工具批准行为覆盖。
- `tools.<tool>.output_token_limit`（可选）：1个正代币预算
工具的输出，在标准 20% 序列化余量之前。覆盖该工具的
模型的默认输出截断预算。

顶级 `mcp_optional_startup_grace_ms` 设置控制构建初始工具目录时 Codex
 等待可选 MCP 服务器的时间。它
默认为`1000`毫秒。将其设置为`0`以等待每个服务器的
`startup_timeout_sec`。所需的服务器仍使用其启动
超时。

#### OAuth客户端注册和回调

当您的授权服务器需要预先注册OAuth客户端时，请在添加MCP服务器时提供
其客户端ID：

```bash
codex mcp add example --url https://mcp.example.com --oauth-client-id my-client
```

Codex 显示完整的回调 URL 以向您的提供商注册：

```text
OAuth callback URL: http://127.0.0.1/callback
```

Codex 将回调与客户端 ID 一起保存在 `config.toml` 中，以供以后 
 登录：

```toml
[mcp_servers.example]
url = "https://mcp.example.com"

[mcp_servers.example.oauth]
client_id = "my-client"
callback_url = "http://127.0.0.1/callback"
```

仅当
授权服务器通告
`authorization_response_iss_parameter_supported: true`并提供元数据
`issuer`时，新添加的预注册客户端才使用稳定回调。如果未公布发行者支持，Codex 会附加服务器特定的 
 回调 ID，例如 `http://127.0.0.1/callback/XuuuHAzzHOni`。没有保存回调的现有客户端
继续使用其回调ID-特定的重定向。

登录期间，回调选择取决于OAuth配置和
授权服务器元数据：

|OAuth配置|发行人支持|使用回调|
| ------------------------------------------------------------------ | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
|`callback_url` 不带 `client_id`|支持|配置的回调用于客户端注册。|
|`callback_url` 不带 `client_id`|不支持|配置的回调用于客户端注册，并附加服务器特定的回调 ID。|
|`client_id` 和 `callback_url`|支持|复用配置的回调；授权响应必须包含匹配的`iss`。|
|`client_id` 和 `callback_url` 以正确的回调 ID 结尾|不支持|配置的回调将原封不动地重复使用。|
|`client_id` 和 `callback_url` 缺少正确的回调 ID|不支持|配置的回调将被忽略。 Codex 在未设置时使用 `mcp_oauth_callback_url` 或 `http://127.0.0.1/callback`，并附加回调 ID。|
|`client_id` 未配置 `callback_url`|支持或不支持|Codex 使用全局或默认回调，并附加服务器特定的回调 ID。|

回退不会修改存储的回调 URL。 Codex从MCP服务器URL派生回调
ID，包括其路径和查询字符串。相同的
选择规则适用于自动和显式登录。

当您需要自定义回调路径或远程
Devbox入口URL时，请设置`mcp_oauth_callback_url`。当提供商支持发行人识别时，新添加的预注册客户将使用URL不变的
。否则，他们使用
配置的URL并附加服务器特定的回调ID。始终注册
由`codex mcp add`显示的确切回调。

对于 `http://127.0.0.1` 没有显式端口的回调， Codex 省略侦听器端口
的 URL 它显示并存储，然后在期间插入活动侦听器端口
授权。此替换不适用于 `localhost`、IPv6 主机、
HTTPS URL 或已包含端口的回调。授权服务器
必须接受以下变量环回端口
[RFC 8252, 部分 7.3](<https://www.rfc-editor.org/rfc/rfc8252#section-7.3>).

设置 `mcp_oauth_callback_port` 以选择固定的全局侦听器端口，或设置 
`mcp_servers.<server-name>.oauth.callback_port` 以覆盖一台服务器的端口。
回调 URL 中的显式端口不会配置侦听器。对于 
 直接环回回调，请使用不带显式端口的 `http://127.0.0.1`，或为回调 URL 和侦听器配置相同的 
 显式端口。代理回调可以
故意使用与本地侦听器
端口不同的外部URL端口。本地回调 URL 绑定到本地接口；非本地回调 URL
 绑定到 `0.0.0.0`。

Codex 在交换授权码之前验证任何返回的 `iss`。 A
不匹配的`iss`始终拒绝响应。当发布发行者支持时，
缺失的`iss`也会拒绝它。两种失败都不会交换代码或落入
回到另一个回调。格式错误的回调 URL 或在没有元数据颁发者的情况下发布的颁发者支持 
 也仍然是硬故障。请参阅
[对用户进行身份验证](<https://developers.openai.com/plugins/build/auth>)。

如果 MCP 服务器通告 `scopes_supported`，则 Codex 在 OAuth 登录期间会优先选择那些 
 服务器通告的范围。否则，Codex 回退到 `config.toml` 中配置的 
 范围。

#### OAuth 客户端注册

Codex 支持 [OAuth客户端 ID 元数据文档（CIMD)](<https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/>)
和动态客户端注册（DCR）。默认情况下， Codex 自动选择
CIMD 当授权服务器发布广告时
`client_id_metadata_document_supported: true`，包括 `none` 在
`token_endpoint_auth_methods_supported`，并且回调使用支持的
环回 URL。否则， Codex 用途 DCR 当可用时。配置好的 OAuth 客户端
ID 始终优先并跳过客户端注册。

对于没有发行者绑定响应的 CIMD，Codex CLI 使用特定于 MCP 服务器的 ChatGPT 托管元数据 
 文档：

```text
https://chatgpt.com/oauth/codex/<callback_id>/client.json
```

Codex从MCP服务器URL派生`<callback_id>`并将其包含在
环回重定向URI中，例如
`http://127.0.0.1:<port>/callback/<callback_id>`。元数据文档注册
匹配的环回URI，无需端口。授权服务器必须接受登录时选择的 
 端口，同时按照 
[RFC 8252](<https://www.rfc-editor.org/rfc/rfc8252.html#section-7.3>) 的要求精确匹配主机和路径。自定义
回调主机、路径或查询参数需要DCR或配置的OAuth
客户端ID。

Codex CLI 还支持稳定的、共享的 CIMD 文档：

```text
https://chatgpt.com/oauth/codex/client.json
```

当 
 授权服务器通告 
`authorization_response_iss_parameter_supported: true` 时，Codex CLI 使用具有共享 `/callback` 路径的稳定文档，在其元数据中提供有效的 
`issuer`，并在其中包含匹配的 `iss`授权
响应。没有发行者绑定响应的服务器使用
回调特定文档。

要为一次 CLI 登录选择注册方法，请使用 
`--oauth-client-registration`：

```bash
codex mcp login <server-name> --oauth-client-registration cimd
codex mcp login <server-name> --oauth-client-registration dcr
```

默认值为 `auto`。注册选项仅适用于当前登录，
 不会存储在 `config.toml` 中。

#### config.toml 示例

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
env_vars = ["LOCAL_TOKEN"]

[mcp_servers.context7.env]
MY_ENV_VAR = "MY_ENV_VALUE"
```

```toml
# Optional MCP OAuth callback overrides (used by `codex mcp login`)
mcp_oauth_callback_port = 5555
mcp_oauth_callback_url = "https://devbox.example.internal/callback"
```

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
http_headers = { "X-Figma-Region" = "us-east-1" }
```

```toml
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
enabled_tools = ["open", "screenshot"]
disabled_tools = ["screenshot"] # applied after enabled_tools
default_tools_approval_mode = "prompt"
startup_timeout_sec = 20
tool_timeout_sec = 45
enabled = true

[mcp_servers.chrome_devtools.tools.open]
approval_mode = "approve"
output_token_limit = 30000
```

### 插件提供的MCP服务器

安装的插件可以将 MCP 服务器捆绑在其插件清单中。这些
服务器是从插件启动的，因此用户配置不会设置它们的
传输命令。用户配置仍然可以控制`plugins.<plugin>.mcp_servers.<server>`下的on/off状态和工具策略
。

```toml
[plugins."sample@test".mcp_servers.sample]
enabled = true
default_tools_approval_mode = "prompt"
enabled_tools = ["read", "search"]

[plugins."sample@test".mcp_servers.sample.tools.search]
approval_mode = "approve"
```

插件提供的 HTTP MCP 服务器还可以在 `.mcp.json` 中声明 OAuth 设置。
插件清单使用 camelCase 字段名称 `clientId`、`callbackUrl`、和
`callbackPort`：

```json
{
  "mcpServers": {
    "sample": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "my-pre-registered-client",
        "callbackUrl": "http://127.0.0.1/callback/registered"
      }
    }
  }
}
```

插件提供的 MCP 服务器遵循与其他 
MCP 服务器相同的回调选择规则。如果插件提供了 `clientId`，则其提供程序不支持 
 发行者绑定的回调，并且 `callbackUrl` 缺少服务器特定的回调 
ID，Codex 会忽略登录的 URL 并使用`mcp_oauth_callback_url`，或未设置时的
`http://127.0.0.1/callback`，并附加回调ID。 
配置的`callbackUrl`保持不变。

插件的 `oauth.callbackPort` 覆盖全局 
`mcp_oauth_callback_port`；如果两者均未设置，则Codex选择临时端口。
嵌入在`callbackUrl`中的端口不选择侦听器端口。对于具有固定端口的 
 直接环回回调，请将两个值配置为匹配：

```json
{
  "callbackUrl": "http://127.0.0.1:4321/callback/registered",
  "callbackPort": 4321
}
```

对于远程入口或其他代理，当代理转发到配置的 
 侦听器时，回调 URL 端口和本地侦听器 
 端口可以故意不同。

## 有用的 MCP 服务器示例

MCP 服务器列表不断增长。以下是一些常见的：

- [OpenAI 文档 MCP](<https://developers.openai.com/learn/docs-mcp>)：搜索并阅读 OpenAI 开发人员文档。
- [Context7](<https://github.com/upstash/context7>)：连接到最新的开发人员文档。
- Figma [本地](<https://developers.figma.com/docs/figma-mcp-server/local-server-installation/>)和[远程](<https://developers.figma.com/docs/figma-mcp-server/remote-server-installation/>)：访问您的Figma设计。
- [Playwright](<https://www.npmjs.com/package/@playwright/mcp>)：使用 Playwright 控制和检查浏览器。
- [Chrome开发者工具](<https://github.com/ChromeDevTools/chrome-devtools-mcp/>)：控制和检查Chrome。
- [Sentry](<https://docs.sentry.io/product/sentry-mcp/#codex>)：访问Sentry日志。
- [GitHub](<https://github.com/github/github-mcp-server>)：管理 GitHub 超出 `git` 支持范围（例如，拉取请求和问题）。

</ContentModeSwitch>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Model Context Protocol

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Model Context Protocol (MCP) connects models to tools and context. Use it to
give ChatGPT or Codex access to third-party documentation, or to let it
interact with developer tools like your browser or Figma.

ChatGPT web can use remote MCP-backed tools supplied by plugins. Local Codex
clients can also connect directly to MCP servers and share their configuration.

<a id="supported-mcp-features"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

The ChatGPT desktop app, Codex CLI, and IDE extension support MCP servers and
share MCP configuration for the same Codex host.

The supported server features below apply to MCP servers configured on a Codex
host. Hosted plugin tools can have different capabilities.

## Supported MCP features

- **STDIO servers**: Servers that run as a local process (started by a command).
  - Environment variables
- **Streamable HTTP servers**: Servers that you access at an address.
  - Bearer token authentication
  - OAuth authentication, including Client ID Metadata Documents (CIMD) and
    Dynamic Client Registration (DCR)
  - ChatGPT session authentication for trusted first-party servers
- **Server instructions**: Codex reads the MCP `instructions` field returned during initialization and uses it as server-wide guidance alongside the server's tools.

If you build or maintain an MCP server for Codex, use `instructions` for cross-tool workflows, constraints, and rate limits that apply across the server. Keep the first 512 characters self-contained so the most important guidance is available when Codex is deciding how to use the server.

## Connect Codex to an MCP server

Codex stores MCP configuration in `config.toml` alongside other Codex configuration settings. By default this is `~/.codex/config.toml`, but you can also scope MCP servers to a project with `.codex/config.toml` (trusted projects only).

The ChatGPT desktop app, Codex CLI, and IDE extension share this configuration.
Once you configure your MCP servers, you can switch among those clients without
redoing setup.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="app">

### Configure in the ChatGPT desktop app

1. Open **Settings**, then select **MCP servers**.
2. Select **Add server**.
3. Enter a name, choose **STDIO** or **Streamable HTTP**, and provide the
   server's command or URL.
4. Save the server, then select **Restart**.

The server list shows which servers are enabled and which require OAuth. Select
**Authenticate** when an OAuth server requires sign-in. In the composer, type `/mcp`
to view connected servers.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="web">

## Use MCP-backed tools in ChatGPT web

Continuing a conversation using Local computer access with Work Cloud on another device doesn't install plugins or grant access to connected accounts.

In Local computer access with Work Cloud, the enterprise `mcp_servers` TOML requirement in Agent Security applies to local executors. Cloud containers retain Work Cloud policies. Local MCP servers and transports may not be available in the cloud; check connectivity and authentication separately.

For enterprise hooks in Local computer access with Work Cloud:

- **Enterprise hooks.** Where enabled for your workspace, Local computer access with Work Cloud supports admin-defined MCP hooks that run on the cloud coordinator (orchestrator) for supported lifecycle and tool events. Command hooks and hooks from local configuration or plugins are not supported with cloud orchestration, even when tools execute locally.
- **Auditing.** Before relying on these hooks, test the callback connection, confirm the events it receives, and check how failures affect the task. MCP hooks do not provide a complete Compliance API audit trail.
- **Limitations:** Command hooks and hooks from local configuration or plugins are not supported with cloud orchestration, even when tools execute locally. Consumers do not have hooks support in this feature. When both orchestration and execution are local, existing supported hooks continue to work in local-only Work and Codex threads. Admins can still configure supported managed hooks in Agent Security for those workflows.

See [Hooks](<https://learn.chatgpt.com/docs/hooks>) and the [Configuration Reference](<https://learn.chatgpt.com/docs/config-file/config-reference>) for support scope.

In a hosted ChatGPT Work chat, install a [plugin](<https://learn.chatgpt.com/docs/plugins>) to use its
bundled connectors and remote MCP tools. After installation, Chat and Work can
use those tools. Workspace administrators can control which plugins and tools
are available.

ChatGPT web doesn't read local Codex configuration files or expose the local
Codex command menu. Open the **Plugins** tab to browse and manage available
tools.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="cli">

### Configure with the CLI

#### Add an MCP server

```bash
codex mcp add <server-name> --env VAR1=VALUE1 --env VAR2=VALUE2 -- <stdio server-command>
```

For example, to add Context7 (a free MCP server for developer documentation), you can run the following command:

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
```

#### Other CLI commands

Run `codex mcp list` to see configured servers. To see all available MCP
commands, run `codex mcp --help`. For a server that supports OAuth, run
`codex mcp login <server-name>`.

#### Terminal UI (TUI)

In the `codex` TUI, use `/mcp` to see your active MCP servers.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" id="ide">

### Configure in the IDE extension

1. Open the gear menu, then select **MCP servers**.
2. Select **Add server**.
3. Enter a name, choose **STDIO** or **Streamable HTTP**, and provide the
   server's command or URL.
4. Save the server, then select **Restart extension**.

The MCP server list shows which servers are enabled and which require OAuth.
Select **Authenticate** when an OAuth server requires sign-in.

</ContentModeSwitch>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

### Configure with config.toml

For more fine-grained control, edit `~/.codex/config.toml` or a project-scoped
`.codex/config.toml`. See the [configuration reference](<https://learn.chatgpt.com/docs/config-file/config-reference>)
for a searchable list of every supported MCP option.

Configure each MCP server with a `[mcp_servers.<server-name>]` table in the configuration file.

</ContentModeSwitch>

<a id="stdio-servers"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

#### STDIO servers

- `command` (required): The command that starts the server.
- `args` (optional): Arguments to pass to the server.
- `env` (optional): Environment variables to set for the server.
- `env_vars` (optional): Environment variables to allow and forward.
- `cwd` (optional): Working directory to start the server from.
- `experimental_environment` (optional): Set to `remote` to start the stdio
  server through a remote executor environment when one is available.

`env_vars` can contain plain variable names or objects with a source:

```toml
env_vars = ["LOCAL_TOKEN", { name = "REMOTE_TOKEN", source = "remote" }]
```

String entries and `source = "local"` read from Codex's local environment.
`source = "remote"` reads from the remote executor environment and requires
remote MCP stdio.

</ContentModeSwitch>

<a id="streamable-http-servers"></a>

<ContentModeSwitch group="codex-surface" ids="app,cli,ide">

#### Streamable HTTP servers

- `url` (required): The server address.
- `auth` (optional): Authentication to try after configured bearer tokens and
  authorization headers. Use `oauth` (the default) for stored MCP OAuth
  credentials. Use `chatgpt` to use the current ChatGPT session for the trusted
  first-party ChatGPT origin, with stored OAuth as a fallback.
- `bearer_token_env_var` (optional): Environment variable name for a bearer token to send in `Authorization`.
- `http_headers` (optional): Map of header names to static values.
- `env_http_headers` (optional): Map of header names to environment variable names (values pulled from the environment).
- `http_headers_helper` (optional): Local command that prints a JSON object of
  header names and string values, such as `{"X-Auth": "temporary-token"}`.
  Supported for HTTP MCP connections made from the local environment; not for
  stdio servers or connections made through a remote execution environment.

Codex caches helper headers for the connection. After a same-origin POST
returns `401` or `403`, it refreshes the headers once and retries only if the
helper returns changed values. Explicit bearer tokens and OAuth credentials
take precedence over a helper-provided `Authorization` header.
An OAuth `403` response that reports insufficient scope doesn't trigger a
helper refresh.

If no credential source resolves, Codex can connect to the server without
authentication. Run `codex mcp login <server-name>` separately to start an MCP
OAuth login.

#### Other configuration options

- `startup_timeout_sec` (optional): Timeout (seconds) for the server to start. Default: `10`.
- `tool_timeout_sec` (optional): Timeout (seconds) for the server to run a tool. Default: `60`.
- `enabled` (optional): Set `false` to disable a server without deleting it.
- `required` (optional): Set `true` to make startup fail if this enabled server can't initialize.
- `enabled_tools` (optional): Tool allow list.
- `disabled_tools` (optional): Tool deny list (applied after `enabled_tools`).
- `default_tools_approval_mode` (optional): Default approval behavior for
  tools from this server. Supported values are `auto`, `prompt`, `writes`, and
  `approve`. The `writes` mode prompts for tools that aren't marked read-only.
- `tools.<tool>.approval_mode` (optional): Per-tool approval behavior override.
- `tools.<tool>.output_token_limit` (optional): Positive token budget for one
  tool's output, before the standard 20% serialization allowance. Overrides the
  model's default output truncation budget for that tool.

The top-level `mcp_optional_startup_grace_ms` setting controls how long Codex
waits for optional MCP servers when building the initial tool catalog. It
defaults to `1000` milliseconds. Set it to `0` to wait for each server's
`startup_timeout_sec` instead. Required servers still use their startup
timeouts.

#### OAuth client registration and callbacks

When your authorization server requires a pre-registered OAuth client, provide
its client ID when adding the MCP server:

```bash
codex mcp add example --url https://mcp.example.com --oauth-client-id my-client
```

Codex displays the complete callback URL to register with your provider:

```text
OAuth callback URL: http://127.0.0.1/callback
```

Codex saves the callback alongside the client ID in `config.toml` for later
logins:

```toml
[mcp_servers.example]
url = "https://mcp.example.com"

[mcp_servers.example.oauth]
client_id = "my-client"
callback_url = "http://127.0.0.1/callback"
```

Newly added pre-registered clients use a stable callback only when the
authorization server advertises
`authorization_response_iss_parameter_supported: true` and provides a metadata
`issuer`. If issuer support isn't advertised, Codex appends a server-specific
callback ID, such as `http://127.0.0.1/callback/XuuuHAzzHOni`. Existing clients
without a saved callback continue using their callback-ID-specific redirect.

During login, callback selection depends on the OAuth configuration and
authorization server metadata:

| OAuth configuration                                                | Issuer support           | Callback used                                                                                                                                      |
| ------------------------------------------------------------------ | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `callback_url` without `client_id`                                 | Supported                | The configured callback is used for client registration.                                                                                           |
| `callback_url` without `client_id`                                 | Unsupported              | The configured callback is used for client registration with the server-specific callback ID appended.                                             |
| `client_id` and `callback_url`                                     | Supported                | The configured callback is reused; the authorization response must contain the matching `iss`.                                                     |
| `client_id` and a `callback_url` ending in the correct callback ID | Unsupported              | The configured callback is reused unchanged.                                                                                                       |
| `client_id` and a `callback_url` missing the correct callback ID   | Unsupported              | The configured callback is ignored. Codex uses `mcp_oauth_callback_url`, or `http://127.0.0.1/callback` when unset, with the callback ID appended. |
| `client_id` without a configured `callback_url`                    | Supported or unsupported | Codex uses the global or default callback with the server-specific callback ID appended.                                                           |

The fallback doesn't modify the stored callback URL. Codex derives the callback
ID from the MCP server URL, including its path and query string. The same
selection rules apply to automatic and explicit login.

Set `mcp_oauth_callback_url` when you need a custom callback path or remote
Devbox ingress URL. Newly added pre-registered clients use that URL unchanged
when their provider supports issuer identification. Otherwise, they use the
configured URL with the server-specific callback ID appended. Always register
the exact callback displayed by `codex mcp add`.

For `http://127.0.0.1` callbacks without an explicit port, Codex omits the listener port from
the URL it displays and stores, then inserts the active listener port during
authorization. This substitution doesn't apply to `localhost`, IPv6 hosts,
HTTPS URLs, or callbacks that already include a port. Authorization servers
must accept variable loopback ports under
[RFC 8252, Section 7.3](<https://www.rfc-editor.org/rfc/rfc8252#section-7.3>).

Set `mcp_oauth_callback_port` to choose a fixed global listener port, or set
`mcp_servers.<server-name>.oauth.callback_port` to override it for one server.
An explicit port in the callback URL doesn't configure the listener. For a
direct loopback callback, use `http://127.0.0.1` without an explicit port or configure the same
explicit port for both the callback URL and listener. A proxied callback can
intentionally use an external URL port that differs from the local listener
port. Local callback URLs bind to the local interface; non-local callback URLs
bind to `0.0.0.0`.

Codex validates any returned `iss` before exchanging the authorization code. A
mismatched `iss` always rejects the response. When issuer support is advertised,
a missing `iss` also rejects it. Neither failure exchanges the code or falls
back to another callback. A malformed callback URL or issuer support advertised
without a metadata issuer also remains a hard failure. See
[Authenticate users](<https://developers.openai.com/plugins/build/auth>).

If the MCP server advertises `scopes_supported`, Codex prefers those
server-advertised scopes during OAuth login. Otherwise, Codex falls back to the
scopes configured in `config.toml`.

#### OAuth client registration

Codex supports [OAuth Client ID Metadata Documents (CIMD)](<https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/>)
and Dynamic Client Registration (DCR). By default, Codex automatically chooses
CIMD when the authorization server advertises
`client_id_metadata_document_supported: true`, includes `none` in
`token_endpoint_auth_methods_supported`, and the callback uses a supported
loopback URL. Otherwise, Codex uses DCR when available. A configured OAuth client
ID always takes precedence and skips client registration.

For CIMD without issuer-bound responses, Codex CLI uses a ChatGPT-hosted metadata
document specific to the MCP server:

```text
https://chatgpt.com/oauth/codex/<callback_id>/client.json
```

Codex derives `<callback_id>` from the MCP server URL and includes it in the
loopback redirect URI, such as
`http://127.0.0.1:<port>/callback/<callback_id>`. The metadata document registers
the matching loopback URI without a port. Authorization servers must accept the
port selected at login while matching the host and path exactly, as required by
[RFC 8252](<https://www.rfc-editor.org/rfc/rfc8252.html#section-7.3>). Custom
callback hosts, paths, or query parameters require DCR or a configured OAuth
client ID.

Codex CLI also supports a stable, shared CIMD document:

```text
https://chatgpt.com/oauth/codex/client.json
```

Codex CLI uses the stable document with the shared `/callback` path when the
authorization server advertises
`authorization_response_iss_parameter_supported: true`, provides a valid
`issuer` in its metadata, and includes a matching `iss` in authorization
responses. Servers without issuer-bound responses use the
callback-specific document.

To choose a registration method for one CLI login, use
`--oauth-client-registration`:

```bash
codex mcp login <server-name> --oauth-client-registration cimd
codex mcp login <server-name> --oauth-client-registration dcr
```

The default is `auto`. Registration choices apply only to the current login and
aren't stored in `config.toml`.

#### config.toml examples

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
env_vars = ["LOCAL_TOKEN"]

[mcp_servers.context7.env]
MY_ENV_VAR = "MY_ENV_VALUE"
```

```toml
# Optional MCP OAuth callback overrides (used by `codex mcp login`)
mcp_oauth_callback_port = 5555
mcp_oauth_callback_url = "https://devbox.example.internal/callback"
```

```toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
http_headers = { "X-Figma-Region" = "us-east-1" }
```

```toml
[mcp_servers.chrome_devtools]
url = "http://localhost:3000/mcp"
enabled_tools = ["open", "screenshot"]
disabled_tools = ["screenshot"] # applied after enabled_tools
default_tools_approval_mode = "prompt"
startup_timeout_sec = 20
tool_timeout_sec = 45
enabled = true

[mcp_servers.chrome_devtools.tools.open]
approval_mode = "approve"
output_token_limit = 30000
```

### Plugin-provided MCP servers

Installed plugins can bundle MCP servers in their plugin manifest. Those
servers are launched from the plugin, so user config doesn't set their
transport command. User config can still control on/off state and tool policy
under `plugins.<plugin>.mcp_servers.<server>`.

```toml
[plugins."sample@test".mcp_servers.sample]
enabled = true
default_tools_approval_mode = "prompt"
enabled_tools = ["read", "search"]

[plugins."sample@test".mcp_servers.sample.tools.search]
approval_mode = "approve"
```

Plugin-provided HTTP MCP servers can also declare OAuth settings in `.mcp.json`.
Plugin manifests use the camelCase field names `clientId`, `callbackUrl`, and
`callbackPort`:

```json
{
  "mcpServers": {
    "sample": {
      "type": "http",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "my-pre-registered-client",
        "callbackUrl": "http://127.0.0.1/callback/registered"
      }
    }
  }
}
```

Plugin-provided MCP servers follow the same callback-selection rules as other
MCP servers. If a plugin provides a `clientId`, its provider doesn't support
issuer-bound callbacks, and `callbackUrl` lacks the server-specific callback
ID, Codex ignores that URL for the login and uses `mcp_oauth_callback_url`, or
`http://127.0.0.1/callback` when unset, with the callback ID appended. The
configured `callbackUrl` remains unchanged.

A plugin's `oauth.callbackPort` overrides the global
`mcp_oauth_callback_port`; if neither is set, Codex chooses an ephemeral port.
The port embedded in `callbackUrl` doesn't select the listener port. For a
direct loopback callback with a fixed port, configure both values to match:

```json
{
  "callbackUrl": "http://127.0.0.1:4321/callback/registered",
  "callbackPort": 4321
}
```

For remote ingress or another proxy, the callback URL port and local listener
port can intentionally differ when the proxy forwards to the configured
listener.

## Examples of useful MCP servers

The list of MCP servers keeps growing. Here are a few common ones:

- [OpenAI Docs MCP](<https://developers.openai.com/learn/docs-mcp>): Search and read OpenAI developer docs.
- [Context7](<https://github.com/upstash/context7>): Connect to up-to-date developer documentation.
- Figma [Local](<https://developers.figma.com/docs/figma-mcp-server/local-server-installation/>) and [Remote](<https://developers.figma.com/docs/figma-mcp-server/remote-server-installation/>): Access your Figma designs.
- [Playwright](<https://www.npmjs.com/package/@playwright/mcp>): Control and inspect a browser using Playwright.
- [Chrome Developer Tools](<https://github.com/ChromeDevTools/chrome-devtools-mcp/>): Control and inspect Chrome.
- [Sentry](<https://docs.sentry.io/product/sentry-mcp/#codex>): Access Sentry logs.
- [GitHub](<https://github.com/github/github-mcp-server>): Manage GitHub beyond what `git` supports (for example, pull requests and issues).

</ContentModeSwitch>


</details>
