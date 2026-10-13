# 在 Cline 中配置、管理和排查 MCP

产品指南。介绍手动添加、CLI 向导、STDIO 和 Streamable HTTP 配置、凭据、服务器管理、安全基础及连接排查，帮助接入外部开发工具。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/cline/cline/commit/0adccbdb70efcd5317ee25caab71abdc0fb29393>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：服务器工具和凭据的实际权限取决于所接服务。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/mcp/mcp-overview.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/在 Cline 中配置、管理和排查 MCP.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "MCP"
sidebarTitle: "MCP"
description: "Add, configure, and use MCP servers in Cline."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

MCP (Model Context Protocol) 允许 Cline 通过 MCP 服务器使用外部工具和数据源。

## MCP 给你什么

- 将 Cline 连接到外部 API 和服务
- 添加内置 Cline 工具之外的自定义工具
- 使用本地服务器或远程托管服务器

## 快速启动

1. 在Cline中打开**MCP服务器**
2. 手动添加本地服务器，或连接到托管的远程服务器
3. 配置credentials/environment变量
4. 验证工具是否出现并测试一个工具调用

## 添加服务器

### 手动配置

编辑您的 MCP 配置文件并添加：

- **CLI:** `~/.cline/data/settings/cline_mcp_settings.json`（使用 `CLINE_MCP_SETTINGS_PATH` 环境变量覆盖）
- **IDE 扩展名:**
  1. 在 Cline 面板中，单击 **MCP 服务器 ** 图标（顶部工具栏中的堆叠服务器图标）。
  2. 打开**配置**选项卡。
  3. 单击**配置MCP服务器**（底部附近的按钮）。
  4. 这将打开扩展程序使用的MCP设置JSON； `mcpServers` 下的 add/update 条目。
  - 如果您要添加托管端点（而不是直接编辑 JSON），请使用 **远程服务器** 选项卡：
    1. 输入**服务器名称**（任何唯一标签）。
    2. 输入**服务器URL**（完整端点URL）。
    3. 选择**传输类型**：
       - **可流式传输HTTP**（推荐）
       - **SSE（旧版）**
    4. 单击**添加服务器**。

  - 服务器配置形状：
    - **本地 (STDIO)** 服务器使用 `command` + `args`
    - **使用 `url` 的远程 (HTTP/SSE)** 服务器

### CLI MCP 向导

在你的终端中运行：

```bash
cline mcp
```

该向导支持：

|行动|描述|
|---|---|
|列出服务器|显示已配置的服务器和enabled/disabled状态|
|添加服务器|创建新的 MCP 服务器条目|
|编辑服务器|修改现有服务器|
|Enable/disable服务器|切换服务器而不删除它|
|授权OAuth|运行或重新运行远程服务器的浏览器授权|
|删除服务器|永久删除服务器|

添加服务器时，CLI 会提示输入服务器名称、传输类型、command/args（对于 stdio）或 URL/headers（对于远程传输）。

要从脚本中添加或删除服务器，请跳过菜单：

```bash
cline mcp add files --yes -- npx -y @modelcontextprotocol/server-filesystem .
cline mcp add docs https://example.com/mcp --transport http --header "Authorization: Bearer TOKEN" --yes
cline mcp remove docs
```

如果没有 `--yes`，`cline mcp add` 将打开添加向导并填写这些字段。

## 配置举例

### 本地服务器（STDIO）

```json
{
  "mcpServers": {
    "local-server": {
      "command": "node",
      "args": ["/path/to/server.js"],
      "env": {
        "API_KEY": "your_api_key"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### 远程服务器（可流式传输 HTTP）

```json
{
  "mcpServers": {
    "remote-server": {
      "type": "streamableHttp",
      "url": "https://example.com/mcp",
      "headers": {
        "Authorization": "Bearer your-token"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

`type` 字段选择传输。省略它默认为旧的 `sse` 传输以实现向后兼容性，因此为推荐的传输显式设置 `"type": "streamableHttp"`。仅将 `"type": "sse"` 用于旧版 SSE 服务器。

## 运输类型

- **STDIO**：本地进程，更低的延迟，更简单的本地设置
- **远程HTTP/SSE**：托管端点，集中部署，支持多客户端使用

使用 STDIO 进行本地工具和共享托管服务的远程传输。

## 管理服务器

在MCP设置中您可以：

- Enable/disable 服务器
- 重新启动无响应的服务器
- 设置请求超时
- 删除服务器

## 安全基础知识

- 只安装您信任的服务器
- 将机密存储在环境变量中
- 将 `autoApprove` 限制为安全工具
- 在批准之前审查工具调用

## 故障排除

|问题|修复|
|---|---|
|服务器无法连接|验证command/URL、服务器进程状态和端口|
|缺少工具|确认服务器成功启动并且工具已公开|
|验证错误|重新检查 API keys/tokens 和所需的标头|
|超时错误|增加MCP超时并直接测试服务器响应|

## CLI

MCP 也适用于 Cline CLI。在 CLI MCP 设置中配置服务器并使用相同的服务器定义。

要在没有向导的情况下列出服务器：

```bash
cline config mcp
cline config mcp --json
```


## 原文对照

<details>
<summary>展开完整原文</summary>

MCP (Model Context Protocol) lets Cline use external tools and data sources through MCP servers.

## What MCP gives you

- Connect Cline to external APIs and services
- Add custom tools beyond built-in Cline tools
- Use either local servers or remote hosted servers

## Quick start

1. Open **MCP Servers** in Cline
2. Add a local server manually, or connect to a hosted remote server
3. Configure credentials/environment variables
4. Verify tools appear and test one tool call

## Add servers

### Manual config

Edit your MCP config file and add either:

- **CLI:** `~/.cline/data/settings/cline_mcp_settings.json` (override with the `CLINE_MCP_SETTINGS_PATH` env var)
- **IDE extensions:**
  1. In the Cline panel, click the **MCP Servers** icon (stacked server icon in the top toolbar).
  2. Open the **Configure** tab.
  3. Click **Configure MCP Servers** (button near the bottom).
  4. This opens the MCP settings JSON used by the extension; add/update entries under `mcpServers`.
  - If you're adding a hosted endpoint (instead of editing JSON directly), use the **Remote Servers** tab:
    1. Enter **Server Name** (any unique label).
    2. Enter **Server URL** (full endpoint URL).
    3. Choose **Transport Type**:
       - **Streamable HTTP** (recommended)
       - **SSE (Legacy)**
    4. Click **Add Server**.

  - Server config shape:
    - **Local (STDIO)** server using `command` + `args`
    - **Remote (HTTP/SSE)** server using `url`

### CLI MCP wizard

Run this in your terminal:

```bash
cline mcp
```

The wizard supports:

| Action | Description |
|---|---|
| List servers | Show configured servers and enabled/disabled status |
| Add server | Create a new MCP server entry |
| Edit server | Modify an existing server |
| Enable/disable server | Toggle a server without deleting it |
| Authorize OAuth | Run or rerun browser authorization for a remote server |
| Delete server | Remove a server permanently |

When adding a server, the CLI prompts for server name, transport type, command/args (for stdio), or URL/headers (for remote transports).

To add or remove a server from a script, skip the menu:

```bash
cline mcp add files --yes -- npx -y @modelcontextprotocol/server-filesystem .
cline mcp add docs https://example.com/mcp --transport http --header "Authorization: Bearer TOKEN" --yes
cline mcp remove docs
```

Without `--yes`, `cline mcp add` opens the add wizard with those fields filled in.

## Configuration examples

### Local server (STDIO)

```json
{
  "mcpServers": {
    "local-server": {
      "command": "node",
      "args": ["/path/to/server.js"],
      "env": {
        "API_KEY": "your_api_key"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### Remote server (Streamable HTTP)

```json
{
  "mcpServers": {
    "remote-server": {
      "type": "streamableHttp",
      "url": "https://example.com/mcp",
      "headers": {
        "Authorization": "Bearer your-token"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

The `type` field selects the transport. Omitting it defaults to the legacy `sse` transport for backward compatibility, so set `"type": "streamableHttp"` explicitly for the recommended transport. Use `"type": "sse"` only for legacy SSE servers.

## Transport types

- **STDIO**: local process, lower latency, simpler local setup
- **Remote HTTP/SSE**: hosted endpoint, centralized deployment, supports multi-client usage

Use STDIO for local tools and remote transport for shared hosted services.

## Managing servers

In MCP settings you can:

- Enable/disable servers
- Restart unresponsive servers
- Set request timeouts
- Remove servers

## Security basics

- Only install servers you trust
- Store secrets in environment variables
- Limit `autoApprove` to safe tools
- Review tool calls before approval

## Troubleshooting

| Issue | Fix |
|---|---|
| Server won't connect | Verify command/URL, server process status, and port |
| Missing tools | Confirm server started successfully and tools are exposed |
| Auth errors | Re-check API keys/tokens and required headers |
| Timeout errors | Increase MCP timeout and test server response directly |

## CLI

MCP also works in Cline CLI. Configure servers in CLI MCP settings and use the same server definitions.

To list servers without the wizard:

```bash
cline config mcp
cline config mcp --json
```


</details>
