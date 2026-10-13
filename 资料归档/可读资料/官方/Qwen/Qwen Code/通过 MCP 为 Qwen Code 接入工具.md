# 通过 MCP 为 Qwen Code 接入工具

介绍服务发现、配置范围、传输、提示与资源、超时、信任和断线处理，帮助接入外部工具与数据。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-27](<https://github.com/QwenLM/qwen-code/commit/81c260bb040c81a0a59b7e49ab8a4bbb3cae71c1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/mcp.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 通过MCP将Qwen Code连接到工具

Qwen Code可以通过[Model Context Protocol（MCP）](<https://modelcontextprotocol.io/introduction>)连接到外部工具和数据源。 MCP 服务器允许 Qwen Code 访问您的工具、数据库和 API。

## 你可以用MCP做什么

连接MCP服务器后，您可以要求Qwen Code：

- 使用文件和存储库（read/search/write，取决于您启用的工具）
- 查询数据库（模式检查、查询、报告）
- 集成内部服务（将您的 API 包装为 MCP 工具）
- 自动化工作流程（显示为 tools/prompts 的重复任务）

> [！提示]
>
> 如果您正在寻找“一个命令即可开始”，请跳转到[快速入门](<#快速启动>)。

## 快速启动

Qwen Code 从 `settings.json` 中的 `mcpServers` 加载 MCP 服务器。您可以配置服务器：

- 直接编辑`settings.json`
- 通过使用`qwen mcp`命令（参见[CLI参考](<#使用-qwen-mcp-管理-mcp-服务器>)）

### 添加您的第一台服务器

1. 添加服务器（示例：远程 HTTP MCP 服务器）：

```bash
qwen mcp add --transport http my-server http://localhost:3000/mcp
```

2. 启动【Qwen Code】，打开【MCP】管理对话框进行查看和管理
服务器：

```bash
qwen
```

然后输入：

```text
/mcp
```

3. 如果在添加服务器之前 Qwen Code 已在运行，请在
同一个项目。然后要求模型使用该服务器中的工具。

## 配置存储位置（范围）

大多数用户只需要这两个范围：

- **用户范围（默认）**：`~/.qwen/settings.json` 跨计算机上的所有项目
- **项目范围**：项目根目录中的`.qwen/settings.json`

写入用户范围：

```bash
qwen mcp add --scope user --transport http my-server http://localhost:3000/mcp
```

> [!提示]
>
> 有关高级配置层（系统defaults/system设置和优先规则），请参阅[设置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>)。

## 配置服务器

### 选择交通工具

|交通|何时使用|JSON 字段|
| --------- | ----------------------------------------------------------------- | ------------------------------------------- |
| `http`    |推荐用于远程服务；适用于云 MCP 服务器|`httpUrl`（+ 可选 `headers`）|
| `sse`     |Legacy/deprecated 仅支持服务器发送事件的服务器|`url`（+ 可选 `headers`）|
| `stdio`   |计算机上的本地进程（脚本、CLI、Docker）|`command`、`args`（+ 可选 `cwd`、`env`）|

> [！注意]
>
> 如果服务器同时支持两者，则优先选择**HTTP**而不是**SSE**。

### 通过 `settings.json` 与 `qwen mcp add` 配置

两种方法都会在您的 `settings.json` 中生成相同的 `mcpServers` 条目 - 使用您喜欢的任何一个。

#### Stdio 服务器（本地进程）

JSON (`.qwen/settings.json`):

```json
{
  "mcpServers": {
    "pythonTools": {
      "command": "python",
      "args": ["-m", "my_mcp_server", "--port", "8080"],
      "cwd": "./mcp-servers/python",
      "env": {
        "DATABASE_URL": "$DB_CONNECTION_STRING",
        "API_KEY": "${EXTERNAL_API_KEY}"
      },
      "timeout": 15000
    }
  }
}
```

CLI（默认写入用户范围）：

```bash
qwen mcp add pythonTools -e DATABASE_URL=$DB_CONNECTION_STRING -e API_KEY=$EXTERNAL_API_KEY \
  --timeout 15000 python -m my_mcp_server --port 8080
```

#### HTTP 服务器（远程流式传输 HTTP）

JSON:

```json
{
  "mcpServers": {
    "httpServerWithAuth": {
      "httpUrl": "http://localhost:3000/mcp",
      "headers": {
        "Authorization": "Bearer your-api-token"
      },
      "timeout": 5000
    }
  }
}
```

CLI:

```bash
qwen mcp add --transport http httpServerWithAuth http://localhost:3000/mcp \
  --header "Authorization: Bearer your-api-token" --timeout 5000
```

#### SSE 服务器（远程服务器发送的事件）

JSON:

```json
{
  "mcpServers": {
    "sseServer": {
      "url": "http://localhost:8080/sse",
      "timeout": 30000
    }
  }
}
```

CLI:

```bash
qwen mcp add --transport sse sseServer http://localhost:8080/sse --timeout 30000
```

## 使用 MCP 提示和资源

除了工具之外，Qwen Code 还发现并展示了另外两个 MCP 原语。

### 提示（斜杠命令）

服务器通过 `prompts/list` 发布的任何提示都将成为可执行的 
** 斜杠命令 **。发现后，输入`/`，你会看到列出的提示
（标记为`MCP: <server>`）；像任何其他命令一样运行它：

```text
/my_prompt --arg1="value" --arg2="value"
# positional form also works:
/my_prompt "value" "value"
# show the prompt's arguments:
/my_prompt help
```

提示的消息被发送到模型，然后模型对这些消息进行操作。

> 对于声明的`prompts`功能的发现是宽松的：一些
> 服务器实现`prompts/list`，但从其
> `initialize`功能中省略`prompts`。无论如何，Qwen Code尝试`prompts/list`，所以
> 那些提示仍然出现。真正没有提示的服务器只是简单地
> 回答`Method not found`，它被忽略。

### 资源

服务器通过 `resources/list` 通告的资源是按 
 服务器发现的。使用 `/mcp` 打开管理对话框，然后选择一个服务器以查看 
 其 ** 资源 ** 及其工具和提示。选择**查看
资源**浏览服务器的资源URI；选择其中一个会显示其 
 描述和 MIME 类型以及对 
 粘贴到邮件中的确切 `@server:uri` 引用。与提示一样，不需要声明 `resources` 功能 
。

使用 `@server:uri`
 语法将资源的内容插入到您的消息中 — 输入 `@`，然后输入服务器名称、冒号和资源 URI：

```text
summarize @myserver:file:///docs/spec.md and list the open questions
```

键入 `@myserver:` 将显示该服务器资源的自动完成列表；
继续键入以进行过滤，匹配（不区分大小写）资源 URI
 或其友好的 name/title. 您不必记住 URI —在 
 到达冒号之前，键入服务器名称的一部分也会建议匹配公开资源的 
 服务器，因此您可以选择一个并直接深入到 
 其资源列表。提交时，将读取引用的资源，并将其内容
附加到您的消息中（内联文本，二进制 blob 作为附件）； 
`@server:uri` 引用保留在提示中，以便模型知道它正在查看什么 
。 `server` 前缀必须与配置的 MCP 服务器匹配 - 
，否则令牌将被视为普通文件路径，因此现有的 
`@path/to/file` 引用不受影响。 
不受信任的文件夹中禁用资源读取。

## 渐进可用性和发现超时

Qwen Code 在 UI 已经 
 交互后在后台发现 MCP 服务器。即使您的其中一个 MCP 服务器需要几秒钟 
（或从不响应），您也会在几百 
 毫秒内看到 cli 的第一个提示，并且模型的工具列表在每台服务器完成发现后大约 
 一帧（~16 毫秒）内更新握手。

- **交互模式**：立即出现UI；一颗 MCP 状态药丸
右下角显示 `N/M MCP servers ready`，而发现处于 
 飞行中。在 MCP 完成之前发送提示只是意味着模型 
 看到当时已准备好的工具 _ _；服务器上线后，后续提示会看到
更多工具。
- **非交互模式**（`--prompt`，stream-json，ACP）：cli仍然
在发送第一个提示之前等待 MCP 发现解决，因此 
 脚本/管道调用会看到与 
 遗留同步行为产生的相同完整工具集。

### 每服务器 `discoveryTimeoutMs`

每个 MCP 服务器都会获得一个仅限发现的超时，该超时限制了允许 
 初始握手 (`connect` + `tools/list` + `prompts/list` + 
`resources/list`) 所允许的时间。默认值：

- **stdio 服务器**：30
- **远程HTTP/SSE服务器**：5s（网络风险较高）

需要时覆盖每个服务器：

```jsonc
{
  "mcpServers": {
    "slow-stdio": {
      "command": "node",
      "args": ["./slow-server.js"],
      "discoveryTimeoutMs": 60000,
    },
    "flaky-remote": {
      "httpUrl": "https://example.com/mcp",
      "discoveryTimeoutMs": 10000,
    },
  },
}
```

现有的 `timeout` 字段是 ** 工具调用 ** 超时（用于每个 
`tools/call` 请求，默认 10 分钟），并且不受 
`discoveryTimeoutMs` 影响 - 长时间运行的工具调用不是启动
病理。

### 自动工作室协商

Stdio 服务器默认使用单进程传统初始化流程。要
连接到仅限现代的 stdio 服务器，请选择自动协议协商：

```jsonc
{
  "mcpServers": {
    "modern-server": {
      "command": "node",
      "args": ["./server.js"],
      "versionNegotiation": "auto",
    },
  },
}
```

自动协商会在
启动会话过程之前运行已配置服务器的短期副本，并且最多可使用五秒的发现
预算。为具有非幂等启动
副作用、单所有者锁或PID文件或缓慢初始化握手的服务器保留默认旧策略。

### 回滚渐进式 MCP

如果您需要旧的同步行为（cli 在显示任何 UI 之前等待每个 MCP 服务器 
），请在 
 环境中设置 `QWEN_CODE_LEGACY_MCP_BLOCKING=1`。这被保留作为至少一次释放的逃生口。

## 安全与控制

### 信任（跳过确认）

- **服务器信任** (`trust: true`)：仅在受信任的工作区中绕过该服务器的确认提示（谨慎使用）。

### 连接丢失重放

仅当服务器具有 `trust: true`、工作区受信任并且该工具显式声明 `idempotentHint: true` 或一致的只读注释时，Qwen Code 才会重新连接并重播当前的 MCP 工具调用。只读注释与 `destructiveHint: true` 或 `idempotentHint: false` 冲突，不会重播。

连接失败后，不会重播缺少注释、冲突注释、不受信任的服务器或不受信任的工作区的调用。 Qwen Code 报告结果可能未知，因为服务器可能在响应丢失之前完成了操作。再次尝试之前请验证结果。这种保守的行为可能与透明地重试未注释工具的早期版本不同。

注释是服务器提供的行为提示，而不是权限或授权边界。仅为您控制且已验证其注释的服务器配置 `trust: true`。

### OAuth认证

Qwen Code 支持 MCP 服务器的 OAuth 2.0 身份验证。这在访问需要身份验证的远程服务器时非常有用。

#### 基本用法

当您添加具有 OAuth 凭据的 MCP 服务器时，Qwen Code 将自动处理身份验证流程：

```bash
qwen mcp add --transport sse oauth-server https://api.example.com/sse/ \
  --oauth-client-id your-client-id \
  --oauth-redirect-uri https://your-server.com/oauth/callback \
  --oauth-authorization-url https://provider.example.com/authorize \
  --oauth-token-url https://provider.example.com/token
```

#### 重要提示：重定向 URI 配置

OAuth 流程需要重定向 URI，其中授权提供程序发送身份验证代码。

- **本地开发**：默认情况下，Qwen Code使用`http://localhost:7777/oauth/callback`。当使用本地浏览器在本地计算机上运行 Qwen Code 时，此功能有效。

- **Remote/cloud部署**：在远程服务器、云IDE或Web终端上运行Qwen Code时，默认的`localhost`重定向将NOT起作用。使用以 `/oauth/callback` 结尾的公共 URL 配置 `--oauth-redirect-uri`，然后将该路径反向代理到运行 Qwen Code 的计算机上的 `http://127.0.0.1:7777/oauth/callback`。 Qwen Code 不终止 TLS；代理人必须这样做。

远程服务器示例：

```bash
qwen mcp add --transport sse remote-server https://api.example.com/sse/ \
  --oauth-redirect-uri https://your-remote-server.example.com/oauth/callback
```

例如，反向代理只能将此回调路径转发到本地侦听器：

```nginx
location = /oauth/callback {
  proxy_pass http://127.0.0.1:7777;
}
```

#### 通过settings.json手动配置

您还可以直接编辑`settings.json`来配置OAuth：

```json
{
  "mcpServers": {
    "oauthServer": {
      "url": "https://api.example.com/sse/",
      "oauth": {
        "enabled": true,
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "authorizationUrl": "https://provider.example.com/authorize",
        "tokenUrl": "https://provider.example.com/token",
        "redirectUri": "https://your-server.com/oauth/callback",
        "scopes": ["read", "write"]
      }
    }
  }
}
```

OAuth 配置属性：

|财产|描述|
| ------------------ | --------------------------------------------------------------------------------------------------------------------- |
| `enabled`          |为此服务器启用 OAuth（布尔值）|
| `clientId`         |OAuth 客户端标识符（字符串，动态注册时可选）|
| `clientSecret`     |OAuth 客户端密钥（字符串，公共客户端可选）|
| `authorizationUrl` |OAuth 授权端点（字符串，如果省略则自动发现）|
| `tokenUrl`         |OAuth 令牌端点（字符串，如果省略则自动发现）|
| `scopes`           |所需的 OAuth 范围（字符串数组）|
| `redirectUri`      |自定义重定向 URI（字符串）。 **对于远程部署至关重要**。默认为 `http://localhost:7777/oauth/callback`|
| `tokenParamName`   |SSE URL 中令牌的查询参数名称（字符串）|
| `audiences`        |令牌有效的受众（字符串数组）|

#### 代币管理

OAuth 令牌会自动：

- **默认将**存储在`~/.qwen/mcp-oauth-tokens.json`中（明文，模式0600）。如果设置了 `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true`，则 Qwen Code 使用可用的钥匙串支持存储，或使用 AES-256-GCM 加密的 `~/.qwen/mcp-oauth-tokens-v2.json`。
- **过期时刷新**（如果刷新令牌可用）
- **在每次连接尝试之前验证**

> [!WARNING]
> 默认情况下，OAuth 令牌未加密地存储在磁盘上。在共享或多用户计算机上，设置 `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` 以保护凭据。

使用 Qwen Code 中的 `/mcp` 对话框来检查 MCP 服务器并以交互方式管理 
 身份验证。

### 工具过滤（每个服务器的allow/deny工具）

使用 `includeTools` / `excludeTools` 限制服务器公开的工具（从 Qwen Code 的角度来看）。

示例：仅包含一些工具：

```json
{
  "mcpServers": {
    "filteredServer": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "includeTools": ["safe_tool", "file_reader", "data_processor"],
      "timeout": 30000
    }
  }
}
```

### 全球 allow/deny 列表

`settings.json` 中的 `mcp` 对象定义所有 MCP 服务器的全局规则：

- `mcp.allowed`：MCP服务器名称的允许列表（`mcpServers`中的键）
- `mcp.excluded`：MCP 服务器名称的拒绝列表

两个列表都支持 glob 模式：`*` 匹配任何字符序列，`?` 匹配单个字符（例如，`"*puppeteer*"` 匹配名称包含 `puppeteer` 的每个服务器）。没有通配符的条目会完全匹配。当服务器匹配两个列表时，`mcp.excluded` 优先。

示例：

```json
{
  "mcp": {
    "allowed": ["my-trusted-server", "*-internal"],
    "excluded": ["experimental-server"]
  }
}
```

## 故障排除

- **服务器在`qwen mcp list`**中显示“已断开连接”：验证URL/command是否正确，然后增加`timeout`。
- **Stdio服务器无法启动**：使用绝对`command`路径，并仔细检查`cwd`/`env`。
- **JSON中的环境变量无法解析**：确保它们存在于Qwen Code运行的环境中（shell与GUI应用程序环境可能不同）。

## 参考

### `settings.json`结构

#### 服务器特定配置（`mcpServers`）

将 `mcpServers` 对象添加到您的 `settings.json` 文件中：

```json
// ... file contains other config objects
{
  "mcpServers": {
    "serverName": {
      "command": "path/to/server",
      "args": ["--arg1", "value1"],
      "env": {
        "API_KEY": "$MY_API_TOKEN"
      },
      "cwd": "./server-directory",
      "timeout": 30000,
      "trust": false
    }
  }
}
```

配置属性：

必需（以下之一）：

|财产|描述|
| --------- | ------------------------------------------------------ |
| `command` |Stdio 传输可执行文件的路径|
| `url`     |SSE 端点 URL（例如，`"http://localhost:8080/sse"`）|
| `httpUrl` |HTTP 流端点 URL|

可选：

|财产| Type/Default                                  |描述|
| ---------------------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `args`                 |数组|Stdio 传输的命令行参数|
| `headers`              |对象|使用 `url` 或 `httpUrl` 时自定义 HTTP 标头|
| `env`                  |对象|服务器进程的环境变量。值可以使用 `$VAR_NAME` 或 `${VAR_NAME}` 语法引用环境变量|
| `cwd`                  |字符串|Stdio 传输的工作目录|
| `timeout`              |编号<br>（默认：600,000）|请求超时以毫秒为单位（默认值：600,000ms = 10 分钟）|
| `versionNegotiation`   |`"auto" \| "legacy"`<br>（默认：`"legacy"`）|对于 Stdio 服务器，`"auto"` 选择在一次性同级进程上进行协议协商。默认`"legacy"`仅启动会话进程。|
| `trust`                |布尔值<br>（默认值：false）|当 `true` 时，绕过受信任工作区中此服务器的工具调用确认（默认值：`false`）|
| `includeTools`         |数组|要包含在此 MCP 服务器中的工具名称列表。指定后，只有此处列出的工具可从此服务器上使用（白名单行为）。如果未指定，则默认启用服务器中的所有工具。|
| `excludeTools`         |数组|要从此 MCP 服务器中排除的工具名称列表。此处列出的工具将不可用于模型，即使它们是由服务器公开的。<br>注意：`excludeTools` 优先于 `includeTools` - 如果工具同时在两个列表中，则它将被排除。|
| `targetAudience`       |字符串|OAuth 客户端 ID 已列入您尝试访问的 IAP- 受保护应用程序的白名单。与 `authProviderType: 'service_account_impersonation'` 一起使用。|
| `targetServiceAccount` |字符串|要模拟的 Google 云服务帐户的电子邮件地址。与 `authProviderType: 'service_account_impersonation'` 一起使用。|

### MCP 应用程序资源限制

MCP 应用程序返回的捆绑 HTML 可能大于默认 1 MiB，或者加载时间 
 比默认 10 秒长。仅配置 `settings.json` 中需要更多 
 净空的服务器：

```json
{
  "mcpServers": {
    "amplitude": {
      "httpUrl": "https://mcp.amplitude.com/mcp",
      "appResourceMaxBytes": 4194304,
      "appResourceTimeoutMs": 120000
    }
  }
}
```

Amplitude 仍然需要 OAuth 身份验证。这些设置不会启用
额外的应用程序桥接功能或保证每个应用程序都是兼容的。

- `appResourceMaxBytes`：解码的最大 HTML 大小（以 UTF-8 字节为单位）。默认为
1,048,576 (1 MiB);固定为 1–4,194,304 字节 (4 MiB)。
- `appResourceTimeoutMs`：资源读取截止时间，与工具执行无关
当明确设置时；钳位至 100–120,000 ms。如果没有覆盖，
 截止时间仍然是一般 `timeout` 和 10,000 毫秒中较小的一个。
- 有限值会向下舍入并限制。非数值或非有限值
回退到默认值。取消在任何配置的限制下仍然有效。

限制失败会保留成功的工具结果，并显示警告
以及适用的配置密钥。较大的 HTML 保留在模型上下文之外，
 同时增加保留的转录本和重播大小。大小检查发生在
**之后**，SDK读取响应；它不限制网络传输或峰值
内存。这两个限制设置不会更改沙箱或CSP。

应用程序 HTML 仅安装在守护程序支持的 WebShell 会话中以及 
 录制的文字记录的重播中。终端和无头会话会渲染后备文本 
，因此这些键仅更改警告文本、获取成本和 
 延迟以及保留的转录本大小。

在声明服务器的同一源中设置这些键。配置
源按优先级替换整个服务器对象，而不是合并字段，
因此在另一个文件中添加的同名条目会隐藏原始服务器
，而不是调整其限制。

<a id="qwen-mcp-cli"></a>

### 使用 `qwen mcp` 管理 MCP 服务器

您始终可以通过手动编辑`settings.json`来配置MCP服务器，但CLI通常更快。

#### 添加服务器（`qwen mcp add`）

```bash
qwen mcp add [options] <name> <commandOrUrl> [args...]
```

| Argument/Option             |描述|默认|示例|
| --------------------------- | ------------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------------ |
| `<name>`                    |服务器的唯一名称。| —                                      | `example-server`                                                   |
| `<commandOrUrl>`            |要执行的命令（对于 `stdio`）或 URL（对于 `http`/`sse`）。| —                                      |`/usr/bin/python` 或 `http://localhost:8`|
| `[args...]`                 |`stdio` 命令的可选参数。| —                                      | `--port 5000`                                                      |
| `-s`, `--scope`             |配置范围（用户或项目）。| `user`                                 | `-s user`                                                          |
| `-t`, `--transport`         |传输类型（`stdio`、`sse`、`http`）。| `stdio`                                | `-t sse`                                                           |
| `-e`, `--env`               |设置环境变量。| —                                      | `-e KEY=value`                                                     |
| `-H`, `--header`            |为 SSE 和 HTTP 传输设置 HTTP 标头。| —                                      | `-H "X-Api-Key: abc123"`                                           |
| `--timeout`                 |设置连接超时（以毫秒为单位）。| —                                      | `--timeout 30000`                                                  |
| `--trust`                   |信任服务器；跳过受信任工作区中的确认。| — (`false`)                            | `--trust`                                                          |
| `--description`             |设置服务器的描述。| —                                      | `--description "Local tools"`                                      |
| `--include-tools`           |要包含的以逗号分隔的工具列表。|包括所有工具| `--include-tools mytool,othertool`                                 |
| `--exclude-tools`           |要排除的工具的逗号分隔列表。|无| `--exclude-tools mytool`                                           |
| `--oauth-client-id`         |用于 MCP 服务器身份验证的 OAuth 客户端 ID。| —                                      | `--oauth-client-id your-client-id`                                 |
| `--oauth-client-secret`     |用于 MCP 服务器身份验证的 OAuth 客户端密钥。| —                                      | `--oauth-client-secret your-client-secret`                         |
| `--oauth-redirect-uri`      |OAuth 重定向 URI 用于身份验证回调。| `http://localhost:7777/oauth/callback` | `--oauth-redirect-uri https://your-server.com/oauth/callback`      |
| `--oauth-authorization-url` |OAuth 授权 URL。| —                                      | `--oauth-authorization-url https://provider.example.com/authorize` |
| `--oauth-token-url`         |OAuth 令牌 URL。| —                                      | `--oauth-token-url https://provider.example.com/token`             |
| `--oauth-scopes`            |OAuth 范围（以逗号分隔）。| —                                      | `--oauth-scopes scope1,scope2`                                     |

> `--oauth-*` 标志仅适用于 `--transport sse` 和 `--transport http`。将它们与 `--transport stdio` 组合会被拒绝。

#### 删除服务器 (`qwen mcp remove`)

```bash
qwen mcp remove <name>
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Connect Qwen Code to tools via MCP

Qwen Code can connect to external tools and data sources through the [Model Context Protocol (MCP)](<https://modelcontextprotocol.io/introduction>). MCP servers give Qwen Code access to your tools, databases, and APIs.

## What you can do with MCP

With MCP servers connected, you can ask Qwen Code to:

- Work with files and repos (read/search/write, depending on the tools you enable)
- Query databases (schema inspection, queries, reporting)
- Integrate internal services (wrap your APIs as MCP tools)
- Automate workflows (repeatable tasks exposed as tools/prompts)

> [!tip]
>
> If you’re looking for the “one command to get started”, jump to [Quick start](<#quick-start>).

## Quick start

Qwen Code loads MCP servers from `mcpServers` in your `settings.json`. You can configure servers either:

- By editing `settings.json` directly
- By using `qwen mcp` commands (see [CLI reference](<#manage-mcp-servers-with-qwen-mcp>))

### Add your first server

1. Add a server (example: remote HTTP MCP server):

```bash
qwen mcp add --transport http my-server http://localhost:3000/mcp
```

2. Start Qwen Code and open the MCP management dialog to view and manage
   servers:

```bash
qwen
```

Then enter:

```text
/mcp
```

3. If Qwen Code was already running before you added the server, restart it in
   the same project. Then ask the model to use tools from that server.

## Where configuration is stored (scopes)

Most users only need these two scopes:

- **User scope (default)**: `~/.qwen/settings.json` across all projects on your machine
- **Project scope**: `.qwen/settings.json` in your project root

Write to user scope:

```bash
qwen mcp add --scope user --transport http my-server http://localhost:3000/mcp
```

> [!tip]
>
> For advanced configuration layers (system defaults/system settings and precedence rules), see [Settings](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>).

## Configure servers

### Choose a transport

| Transport | When to use                                                       | JSON field(s)                               |
| --------- | ----------------------------------------------------------------- | ------------------------------------------- |
| `http`    | Recommended for remote services; works well for cloud MCP servers | `httpUrl` (+ optional `headers`)            |
| `sse`     | Legacy/deprecated servers that only support Server-Sent Events    | `url` (+ optional `headers`)                |
| `stdio`   | Local process (scripts, CLIs, Docker) on your machine             | `command`, `args` (+ optional `cwd`, `env`) |

> [!note]
>
> If a server supports both, prefer **HTTP** over **SSE**.

### Configure via `settings.json` vs `qwen mcp add`

Both approaches produce the same `mcpServers` entries in your `settings.json`—use whichever you prefer.

#### Stdio server (local process)

JSON (`.qwen/settings.json`):

```json
{
  "mcpServers": {
    "pythonTools": {
      "command": "python",
      "args": ["-m", "my_mcp_server", "--port", "8080"],
      "cwd": "./mcp-servers/python",
      "env": {
        "DATABASE_URL": "$DB_CONNECTION_STRING",
        "API_KEY": "${EXTERNAL_API_KEY}"
      },
      "timeout": 15000
    }
  }
}
```

CLI (writes to user scope by default):

```bash
qwen mcp add pythonTools -e DATABASE_URL=$DB_CONNECTION_STRING -e API_KEY=$EXTERNAL_API_KEY \
  --timeout 15000 python -m my_mcp_server --port 8080
```

#### HTTP server (remote streamable HTTP)

JSON:

```json
{
  "mcpServers": {
    "httpServerWithAuth": {
      "httpUrl": "http://localhost:3000/mcp",
      "headers": {
        "Authorization": "Bearer your-api-token"
      },
      "timeout": 5000
    }
  }
}
```

CLI:

```bash
qwen mcp add --transport http httpServerWithAuth http://localhost:3000/mcp \
  --header "Authorization: Bearer your-api-token" --timeout 5000
```

#### SSE server (remote Server-Sent Events)

JSON:

```json
{
  "mcpServers": {
    "sseServer": {
      "url": "http://localhost:8080/sse",
      "timeout": 30000
    }
  }
}
```

CLI:

```bash
qwen mcp add --transport sse sseServer http://localhost:8080/sse --timeout 30000
```

## Using MCP prompts and resources

Besides tools, Qwen Code discovers and surfaces two other MCP primitives.

### Prompts (slash commands)

Any prompt a server advertises via `prompts/list` becomes an executable
**slash command**. After discovery, type `/` and you'll see the prompt
listed (labeled `MCP: <server>`); run it like any other command:

```text
/my_prompt --arg1="value" --arg2="value"
# positional form also works:
/my_prompt "value" "value"
# show the prompt's arguments:
/my_prompt help
```

The prompt's messages are sent to the model, which then acts on them.

> Discovery is lenient about the declared `prompts` capability: some
> servers implement `prompts/list` but omit `prompts` from their
> `initialize` capabilities. Qwen Code attempts `prompts/list` anyway, so
> those prompts still appear. A server that genuinely has no prompts simply
> answers `Method not found`, which is ignored.

### Resources

Resources a server advertises via `resources/list` are discovered per
server. Open the management dialog with `/mcp` and select a server to see
its **Resources** count alongside its tools and prompts. Choose **View
resources** to browse the server's resource URIs; selecting one shows its
description and MIME type along with the exact `@server:uri` reference to
paste into a message. As with prompts, the `resources` capability is not
required to be declared.

Inject a resource's contents into your message with the `@server:uri`
syntax — type `@`, then the server name, a colon, and the resource URI:

```text
summarize @myserver:file:///docs/spec.md and list the open questions
```

Typing `@myserver:` shows an autocomplete list of that server's resources;
keep typing to filter, matching (case-insensitively) either the resource URI
or its friendly name/title. You don't have to know a URI by heart — before
you reach the colon, typing part of a server name also suggests matching
servers that expose resources, so you can pick one and drill straight into
its resource list. On submit, the referenced resource is read and its contents are
appended to your message (text inline, binary blobs as attachments); the
`@server:uri` reference is preserved in the prompt so the model knows what
it is looking at. The `server` prefix must match a configured MCP server —
otherwise the token is treated as a normal file path, so existing
`@path/to/file` references are unaffected. Resource reads are disabled in
untrusted folders.

## Progressive availability and discovery timeouts

Qwen Code discovers MCP servers in the background after the UI is already
interactive. You see the cli's first prompt within a few hundred
milliseconds even when one of your MCP servers takes several seconds
(or never responds), and the model's tool list updates within roughly
one frame (~16 ms) of each server completing its discover handshake.

- **Interactive mode**: the UI appears immediately; an MCP status pill in
  the bottom-right shows `N/M MCP servers ready` while discovery is in
  flight. Sending a prompt before MCP finishes simply means the model
  sees the tools that are ready _at that moment_; subsequent prompts see
  more tools as servers come online.
- **Non-interactive mode** (`--prompt`, stream-json, ACP): the cli still
  waits for MCP discovery to settle before sending the first prompt, so
  scripted / piped invocations see the same complete tool set the
  legacy synchronous behavior produced.

### Per-server `discoveryTimeoutMs`

Each MCP server gets a discovery-only timeout that caps how long the
initial handshake (`connect` + `tools/list` + `prompts/list` +
`resources/list`) is allowed to take. Defaults:

- **stdio servers**: 30 s
- **remote HTTP / SSE servers**: 5 s (network risk is higher)

Override per server when needed:

```jsonc
{
  "mcpServers": {
    "slow-stdio": {
      "command": "node",
      "args": ["./slow-server.js"],
      "discoveryTimeoutMs": 60000,
    },
    "flaky-remote": {
      "httpUrl": "https://example.com/mcp",
      "discoveryTimeoutMs": 10000,
    },
  },
}
```

The existing `timeout` field is **tool-call** timeout (used for each
`tools/call` request, default 10 minutes) and is unaffected by
`discoveryTimeoutMs` — a long-running tool invocation is not a startup
pathology.

### Automatic stdio negotiation

Stdio servers use the single-process legacy initialize flow by default. To
connect to a modern-only stdio server, opt into automatic protocol negotiation:

```jsonc
{
  "mcpServers": {
    "modern-server": {
      "command": "node",
      "args": ["./server.js"],
      "versionNegotiation": "auto",
    },
  },
}
```

Automatic negotiation runs a short-lived copy of the configured server before
starting the session process and can use up to five seconds of the discovery
budget. Keep the default legacy policy for servers with non-idempotent startup
side effects, single-owner locks or PID files, or slow initialize handshakes.

### Rolling back progressive MCP

If you need the old synchronous behavior (cli waits for every MCP server
before showing any UI), set `QWEN_CODE_LEGACY_MCP_BLOCKING=1` in your
environment. This is kept as an escape hatch for at least one release.

## Safety and control

### Trust (skip confirmations)

- **Server trust** (`trust: true`): bypasses confirmation prompts for that server only in a trusted workspace (use sparingly).

### Connection-loss replay

Qwen Code only reconnects and replays the current MCP tool call when the server has `trust: true`, the workspace is trusted, and the tool explicitly declares either `idempotentHint: true` or a consistent read-only annotation. Read-only annotations conflict with `destructiveHint: true` or `idempotentHint: false` and are not replayed.

Calls with missing annotations, conflicting annotations, an untrusted server, or an untrusted workspace are not replayed after a connection failure. Qwen Code reports that the result may be unknown because the server could have completed the operation before the response was lost. Verify the outcome before trying again. This conservative behavior can differ from earlier releases that transparently retried unannotated tools.

Annotations are server-provided behavior hints, not permissions or an authorization boundary. Only configure `trust: true` for servers you control and whose annotations you have verified.

### OAuth authentication

Qwen Code supports OAuth 2.0 authentication for MCP servers. This is useful when accessing remote servers that require authentication.

#### Basic usage

When you add an MCP server with OAuth credentials, Qwen Code will automatically handle the authentication flow:

```bash
qwen mcp add --transport sse oauth-server https://api.example.com/sse/ \
  --oauth-client-id your-client-id \
  --oauth-redirect-uri https://your-server.com/oauth/callback \
  --oauth-authorization-url https://provider.example.com/authorize \
  --oauth-token-url https://provider.example.com/token
```

#### Important: Redirect URI configuration

The OAuth flow requires a redirect URI where the authorization provider sends the authentication code.

- **Local development**: By default, Qwen Code uses `http://localhost:7777/oauth/callback`. This works when running Qwen Code on your local machine with a local browser.

- **Remote/cloud deployments**: When running Qwen Code on remote servers, cloud IDEs, or web terminals, the default `localhost` redirect will NOT work. Configure `--oauth-redirect-uri` with a public URL ending in `/oauth/callback`, then reverse-proxy that path to `http://127.0.0.1:7777/oauth/callback` on the machine running Qwen Code. Qwen Code does not terminate TLS; the proxy must do so.

Example for remote servers:

```bash
qwen mcp add --transport sse remote-server https://api.example.com/sse/ \
  --oauth-redirect-uri https://your-remote-server.example.com/oauth/callback
```

For example, a reverse proxy can forward only this callback path to the local listener:

```nginx
location = /oauth/callback {
  proxy_pass http://127.0.0.1:7777;
}
```

#### Manual configuration via settings.json

You can also configure OAuth by editing `settings.json` directly:

```json
{
  "mcpServers": {
    "oauthServer": {
      "url": "https://api.example.com/sse/",
      "oauth": {
        "enabled": true,
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "authorizationUrl": "https://provider.example.com/authorize",
        "tokenUrl": "https://provider.example.com/token",
        "redirectUri": "https://your-server.com/oauth/callback",
        "scopes": ["read", "write"]
      }
    }
  }
}
```

OAuth configuration properties:

| Property           | Description                                                                                                           |
| ------------------ | --------------------------------------------------------------------------------------------------------------------- |
| `enabled`          | Enable OAuth for this server (boolean)                                                                                |
| `clientId`         | OAuth client identifier (string, optional with dynamic registration)                                                  |
| `clientSecret`     | OAuth client secret (string, optional for public clients)                                                             |
| `authorizationUrl` | OAuth authorization endpoint (string, auto-discovered if omitted)                                                     |
| `tokenUrl`         | OAuth token endpoint (string, auto-discovered if omitted)                                                             |
| `scopes`           | Required OAuth scopes (array of strings)                                                                              |
| `redirectUri`      | Custom redirect URI (string). **Critical for remote deployments**. Defaults to `http://localhost:7777/oauth/callback` |
| `tokenParamName`   | Query parameter name for tokens in SSE URLs (string)                                                                  |
| `audiences`        | Audiences the token is valid for (array of strings)                                                                   |

#### Token management

OAuth tokens are automatically:

- **Stored** in `~/.qwen/mcp-oauth-tokens.json` (plaintext, mode 0600) by default. If `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` is set, Qwen Code uses keychain-backed storage where available, or `~/.qwen/mcp-oauth-tokens-v2.json` with AES-256-GCM encryption.
- **Refreshed** when expired (if refresh tokens are available)
- **Validated** before each connection attempt

> [!WARNING]
> By default, OAuth tokens are stored unencrypted on disk. On shared or multi-user machines, set `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` to protect credentials.

Use the `/mcp` dialog within Qwen Code to inspect MCP servers and manage
authentication interactively.

### Tool filtering (allow/deny tools per server)

Use `includeTools` / `excludeTools` to restrict tools exposed by a server (from Qwen Code’s perspective).

Example: include only a few tools:

```json
{
  "mcpServers": {
    "filteredServer": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "includeTools": ["safe_tool", "file_reader", "data_processor"],
      "timeout": 30000
    }
  }
}
```

### Global allow/deny lists

The `mcp` object in your `settings.json` defines global rules for all MCP servers:

- `mcp.allowed`: allow-list of MCP server names (keys in `mcpServers`)
- `mcp.excluded`: deny-list of MCP server names

Both lists support glob patterns: `*` matches any sequence of characters and `?` matches a single character (for example, `"*puppeteer*"` matches every server whose name contains `puppeteer`). Entries without glob characters are matched exactly. When a server matches both lists, `mcp.excluded` takes precedence.

Example:

```json
{
  "mcp": {
    "allowed": ["my-trusted-server", "*-internal"],
    "excluded": ["experimental-server"]
  }
}
```

## Troubleshooting

- **Server shows “Disconnected” in `qwen mcp list`**: verify the URL/command is correct, then increase `timeout`.
- **Stdio server fails to start**: use an absolute `command` path, and double-check `cwd`/`env`.
- **Environment variables in JSON don’t resolve**: ensure they exist in the environment where Qwen Code runs (shell vs GUI app environments can differ).

## Reference

### `settings.json` structure

#### Server-specific configuration (`mcpServers`)

Add an `mcpServers` object to your `settings.json` file:

```json
// ... file contains other config objects
{
  "mcpServers": {
    "serverName": {
      "command": "path/to/server",
      "args": ["--arg1", "value1"],
      "env": {
        "API_KEY": "$MY_API_TOKEN"
      },
      "cwd": "./server-directory",
      "timeout": 30000,
      "trust": false
    }
  }
}
```

Configuration properties:

Required (one of the following):

| Property  | Description                                            |
| --------- | ------------------------------------------------------ |
| `command` | Path to the executable for Stdio transport             |
| `url`     | SSE endpoint URL (e.g., `"http://localhost:8080/sse"`) |
| `httpUrl` | HTTP streaming endpoint URL                            |

Optional:

| Property               | Type/Default                                  | Description                                                                                                                                                                                                                                                       |
| ---------------------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `args`                 | array                                         | Command-line arguments for Stdio transport                                                                                                                                                                                                                        |
| `headers`              | object                                        | Custom HTTP headers when using `url` or `httpUrl`                                                                                                                                                                                                                 |
| `env`                  | object                                        | Environment variables for the server process. Values can reference environment variables using `$VAR_NAME` or `${VAR_NAME}` syntax                                                                                                                                |
| `cwd`                  | string                                        | Working directory for Stdio transport                                                                                                                                                                                                                             |
| `timeout`              | number<br>(default: 600,000)                  | Request timeout in milliseconds (default: 600,000ms = 10 minutes)                                                                                                                                                                                                 |
| `versionNegotiation`   | `"auto" \| "legacy"`<br>(default: `"legacy"`) | For Stdio servers, `"auto"` opts into protocol negotiation on a disposable sibling process. The default `"legacy"` starts only the session process.                                                                                                               |
| `trust`                | boolean<br>(default: false)                   | When `true`, bypasses tool call confirmations for this server in a trusted workspace (default: `false`)                                                                                                                                                           |
| `includeTools`         | array                                         | List of tool names to include from this MCP server. When specified, only the tools listed here will be available from this server (allowlist behavior). If not specified, all tools from the server are enabled by default.                                       |
| `excludeTools`         | array                                         | List of tool names to exclude from this MCP server. Tools listed here will not be available to the model, even if they are exposed by the server.<br>Note: `excludeTools` takes precedence over `includeTools` - if a tool is in both lists, it will be excluded. |
| `targetAudience`       | string                                        | The OAuth Client ID allowlisted on the IAP-protected application you are trying to access. Used with `authProviderType: 'service_account_impersonation'`.                                                                                                         |
| `targetServiceAccount` | string                                        | The email address of the Google Cloud Service Account to impersonate. Used with `authProviderType: 'service_account_impersonation'`.                                                                                                                              |

### MCP App resource limits

MCP Apps can return bundled HTML larger than the default 1 MiB or take longer
than the default 10 seconds to load. Configure only the server that needs more
headroom in `settings.json`:

```json
{
  "mcpServers": {
    "amplitude": {
      "httpUrl": "https://mcp.amplitude.com/mcp",
      "appResourceMaxBytes": 4194304,
      "appResourceTimeoutMs": 120000
    }
  }
}
```

Amplitude still requires OAuth authentication. These settings do not enable
additional App bridge capabilities or guarantee that every App is compatible.

- `appResourceMaxBytes`: maximum decoded HTML size in UTF-8 bytes. Defaults to
  1,048,576 (1 MiB); clamped to 1–4,194,304 bytes (4 MiB).
- `appResourceTimeoutMs`: resource-read deadline, independent of tool execution
  when explicitly set; clamped to 100–120,000 ms. Without an override, the
  deadline remains the smaller of the general `timeout` and 10,000 ms.
- Finite values are rounded down and clamped. Nonnumeric or nonfinite values
  fall back to defaults. Cancellation remains effective at any configured limit.

A limit failure preserves the successful tool result and displays a warning
with the applicable configuration key. Larger HTML stays outside model context,
while increasing retained transcript and replay size. The size check happens
**after** the SDK reads the response; it does not cap network transfer or peak
memory. These two limit settings do not change the sandbox or CSP.

App HTML is mounted only in a daemon-backed WebShell session and in replay of
a recorded transcript. Terminal and headless sessions render the fallback text
instead, so there these keys change only the warning text, the fetch cost and
latency, and the retained transcript size.

Set these keys in the same source that declares the server. Configuration
sources replace whole server objects by precedence rather than merging fields,
so a same-named entry added in another file shadows the original server
instead of adjusting its limits.

<a id="qwen-mcp-cli"></a>

### Manage MCP servers with `qwen mcp`

You can always configure MCP servers by manually editing `settings.json`, but the CLI is usually faster.

#### Adding a server (`qwen mcp add`)

```bash
qwen mcp add [options] <name> <commandOrUrl> [args...]
```

| Argument/Option             | Description                                                         | Default                                | Example                                                            |
| --------------------------- | ------------------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------------ |
| `<name>`                    | A unique name for the server.                                       | —                                      | `example-server`                                                   |
| `<commandOrUrl>`            | The command to execute (for `stdio`) or the URL (for `http`/`sse`). | —                                      | `/usr/bin/python` or `http://localhost:8`                          |
| `[args...]`                 | Optional arguments for a `stdio` command.                           | —                                      | `--port 5000`                                                      |
| `-s`, `--scope`             | Configuration scope (user or project).                              | `user`                                 | `-s user`                                                          |
| `-t`, `--transport`         | Transport type (`stdio`, `sse`, `http`).                            | `stdio`                                | `-t sse`                                                           |
| `-e`, `--env`               | Set environment variables.                                          | —                                      | `-e KEY=value`                                                     |
| `-H`, `--header`            | Set HTTP headers for SSE and HTTP transports.                       | —                                      | `-H "X-Api-Key: abc123"`                                           |
| `--timeout`                 | Set connection timeout in milliseconds.                             | —                                      | `--timeout 30000`                                                  |
| `--trust`                   | Trust the server; skip confirmations in trusted workspaces.         | — (`false`)                            | `--trust`                                                          |
| `--description`             | Set the description for the server.                                 | —                                      | `--description "Local tools"`                                      |
| `--include-tools`           | A comma-separated list of tools to include.                         | all tools included                     | `--include-tools mytool,othertool`                                 |
| `--exclude-tools`           | A comma-separated list of tools to exclude.                         | none                                   | `--exclude-tools mytool`                                           |
| `--oauth-client-id`         | OAuth client ID for MCP server authentication.                      | —                                      | `--oauth-client-id your-client-id`                                 |
| `--oauth-client-secret`     | OAuth client secret for MCP server authentication.                  | —                                      | `--oauth-client-secret your-client-secret`                         |
| `--oauth-redirect-uri`      | OAuth redirect URI for authentication callback.                     | `http://localhost:7777/oauth/callback` | `--oauth-redirect-uri https://your-server.com/oauth/callback`      |
| `--oauth-authorization-url` | OAuth authorization URL.                                            | —                                      | `--oauth-authorization-url https://provider.example.com/authorize` |
| `--oauth-token-url`         | OAuth token URL.                                                    | —                                      | `--oauth-token-url https://provider.example.com/token`             |
| `--oauth-scopes`            | OAuth scopes (comma-separated).                                     | —                                      | `--oauth-scopes scope1,scope2`                                     |

> `--oauth-*` flags apply only to `--transport sse` and `--transport http`. Combining them with `--transport stdio` is rejected.

#### Removing a server (`qwen mcp remove`)

```bash
qwen mcp remove <name>
```


</details>
