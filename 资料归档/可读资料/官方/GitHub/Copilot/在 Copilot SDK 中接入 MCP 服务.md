# 在 Copilot SDK 中接入 MCP 服务

介绍本地和远端服务配置、按会话停用、允许的工具范围、多语言示例与工具不可见等问题排查。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/github/docs/commit/3816ce34e9bc41e454accf2807e0d89f5e9ce294>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/mcp.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 Copilot SDK 中接入 MCP 服务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using MCP servers with the GitHub Copilot SDK
shortTitle: MCP
intro: >-
  The Copilot SDK can integrate with **MCP servers** (Model Context Protocol) to
  extend the assistant's capabilities with external tools. MCP servers run as
  separate processes and expose tools (functions) that Copilot can invoke during
  conversations.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-copilot-sdk/mcp-servers
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

> [！NOTE]
> 这是一个不断发展的功能。请参阅 [ 问题 #36](<https://github.com/github/copilot-sdk/issues/36>) 以了解正在进行的讨论。

## MCP是什么？

[Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) 是一种开放标准，用于将 AI 助手连接到外部工具和数据源。 MCP 服务器可以：

* 执行代码或脚本
* 查询数据库
* 访问文件系统
* 调用外部API
* 还有更多

## 服务器类型

SDK支持两种类型的MCP服务器：

|类型|描述|使用案例|
|------|-------------|----------|
| **Local/Stdio** |作为子进程运行，通过 stdin/stdout 进行通信|本地工具、文件访问、自定义脚本|
| **HTTP/SSE** |通过HTTP访问远程服务器|共享服务、云托管工具|

## 配置

### Node.js / TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5",
    mcpServers: {
        // Local MCP server (stdio)
        "my-local-server": {
            type: "local",
            command: "node",
            args: ["./mcp-server.js"],
            env: { DEBUG: "true" },
            cwd: "./servers",
            tools: ["*"],  // "*" = all tools, [] = none, or list specific tools
            timeout: 30000,
        },
        // Remote MCP server (HTTP)
        "github": {
            type: "http",
            url: "https://api.githubcopilot.com/mcp/",
            headers: { "Authorization": "Bearer ${TOKEN}" },
            tools: ["*"],
        },
    },
});
```

### Python

```python
import asyncio
from copilot import CopilotClient
from copilot.session import PermissionHandler

async def main():
    client = CopilotClient()
    await client.start()

    session = await client.create_session(on_permission_request=PermissionHandler.approve_all, model="gpt-5", mcp_servers={
        # Local MCP server (stdio)
        "my-local-server": {
            "type": "local",
            "command": "python",
            "args": ["./mcp_server.py"],
            "env": {"DEBUG": "true"},
            "cwd": "./servers",
            "tools": ["*"],
            "timeout": 30000,
        },
        # Remote MCP server (HTTP)
        "github": {
            "type": "http",
            "url": "https://api.githubcopilot.com/mcp/",
            "headers": {"Authorization": "Bearer ${TOKEN}"},
            "tools": ["*"],
        },
    })

    response = await session.send_and_wait("List my recent GitHub notifications")
    print(response.data.content)

    await client.stop()

asyncio.run(main())
```

### Go

```golang
package main

import (
    "context"
    "log"
    copilot "github.com/github/copilot-sdk/go"
)

func main() {
    ctx := context.Background()
    client := copilot.NewClient(nil)
    if err := client.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer client.Stop()

    session, err := client.CreateSession(ctx, &copilot.SessionConfig{
        Model: "gpt-5",
        MCPServers: map[string]copilot.MCPServerConfig{
            "my-local-server": copilot.MCPStdioServerConfig{
                Command: "node",
                Args:    []string{"./mcp-server.js"},
                Tools:   []string{"*"},
            },
        },
    })
    if err != nil {
        log.Fatal(err)
    }
    defer session.Disconnect()

    // Use the session...
}
```

### .NET

```csharp
using GitHub.Copilot;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5",
    McpServers = new Dictionary<string, McpServerConfig>
    {
        ["my-local-server"] = new McpStdioServerConfig
        {
            Command = "node",
            Args = new List<string> { "./mcp-server.js" },
            Tools = new List<string> { "*" },
        },
    },
});
```

## 禁用每个会话配置的服务器

将 `disabledMcpServers` 设置为不得在会话中运行的确切 MCP 服务器名称。
该设置的范围仅限于单个创建或恢复请求；它不会
修改全局MCP设置或服务器配置。

```typescript
const session = await client.createSession({
    mcpServers: {
        filesystem: { type: "local", command: "npx", args: ["-y", "@modelcontextprotocol/server-filesystem", "."] },
        github: { type: "http", url: "https://api.githubcopilot.com/mcp/" },
    },
    disabledMcpServers: ["github"],
});
```

| SDK |配置属性|
| --- | --- |
| Node.js | `disabledMcpServers` |
| Python | `disabled_mcp_servers` |
| Go | `DisabledMCPServers` |
| .NET | `DisabledMcpServers` |
| Java | `setDisabledMcpServers(...)` |
| Rust | `with_disabled_mcp_servers(...)` |

在会话创建和**冷**恢复时，禁用的服务器不会启动，并且
运行时不会启动其身份验证。驻留恢复无法
撤消运行时已生成的服务器。名字完全匹配。

## 工具配置

您可以使用 `tools` 字段控制 MCP 服务器可以使用哪些工具。

### 允许所有工具

使用`"*"`启用MCP服务器提供的所有工具：

```typescript
tools: ["*"]
```

### 允许特定工具

提供限制访问的工具名称列表：

```typescript
tools: ["bash", "edit"]
```

agent 仅可使用列出的工具。

### 禁用所有工具

使用空数组禁用所有工具：

```typescript
tools: []
```

### 注释

* `tools` 字段定义允许使用哪些工具。
* 没有单独的 `allow` 或 `disallow` 配置 — 工具访问直接通过此列表控制。

## 快速入门：文件系统 MCP 服务器

这是使用官方 [`@modelcontextprotocol/server-filesystem`](<https://www.npmjs.com/package/@modelcontextprotocol/server-filesystem>) MCP 服务器的完整工作示例：

```typescript
import { CopilotClient } from "@github/copilot-sdk";

async function main() {
    const client = new CopilotClient();

    // Create session with filesystem MCP server
    const session = await client.createSession({
        mcpServers: {
            filesystem: {
                type: "local",
                command: "npx",
                args: ["-y", "@modelcontextprotocol/server-filesystem", "/tmp"],
                tools: ["*"],
            },
        },
    });

    console.log("Session created:", session.sessionId);

    // The model can now use filesystem tools
    const result = await session.sendAndWait({
        prompt: "List the files in the allowed directory",
    });

    console.log("Response:", result?.data?.content);

    await session.disconnect();
    await client.stop();
}

main();
```

**输出：**

```text
Session created: 18b3482b-bcba-40ba-9f02-ad2ac949a59a
Response: The allowed directory is `/tmp`, which contains various files
and subdirectories including temporary system files, log files, and
directories for different applications.
```

> [！TIP]
> 您可以使用[MCP服务器目录](<https://github.com/modelcontextprotocol/servers>)中的任何MCP服务器。热门选项包括 `@modelcontextprotocol/server-github`、`@modelcontextprotocol/server-sqlite` 和 `@modelcontextprotocol/server-puppeteer`。

## 配置选项

### Local/stdio服务器

|财产|类型|必填|描述|
|----------|------|----------|-------------|
| `type` |`"local"` 或 `"stdio"`|否|服务器类型（默认为本地）|
| `command` | `string` |是的|要执行的命令|
| `args` | `string[]` |是的|命令参数|
| `env` | `object` |否|环境变量|
| `cwd` | `string` |否|工作目录|
| `tools` | `string[]` |否|启用工具（`["*"]` 表示所有，`[]` 表示无）|
| `timeout` | `number` |否|超时（以毫秒为单位）|

### 远程服务器（HTTP/SSE）

|财产|类型|必填|描述|
|----------|------|----------|-------------|
| `type` |`"http"` 或 `"sse"`|是的|服务器类型|
| `url` | `string` |是的|服务器URL|
| `headers` | `object` |否|HTTP 标头（例如，用于身份验证）|
| `tools` | `string[]` |否|启用工具|
| `timeout` | `number` |否|超时（以毫秒为单位）|

## 故障排除

### 工具未显示或未调用

1. **验证MCP服务器是否正确启动**
   * 检查命令和参数是否正确
   * 确保服务器进程在启动时不会崩溃
   * 在 stderr 中查找错误输出

1. **检查工具配置**
   * 确保`tools`设置为`["*"]`或列出您需要的特定工具
   * 空数组 `[]` 表示未启用任何工具

1. **验证远程服务器的连接性**
   * 确保 URL 可访问
   * 检查身份验证标头是否正确

### 常见问题

|问题|解决方案|
|-------|----------|
|“MCP 找不到服务器”|验证命令路径是否正确且可执行|
|“连接被拒绝”（HTTP）|检查URL并确保服务器正在运行|
|“超时”错误|增加`timeout`值或检查服务器性能|
|工具可以工作但未被调用|确保您的提示明确需要该工具的功能|

详细的调试指导请参见**[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/mcp-debugging>)**。

## 相关资源

* [Model Context Protocol 规格](<https://modelcontextprotocol.io/>)
* [MCP 服务器目录](<https://github.com/modelcontextprotocol/servers>) - 社区 MCP 服务器
* [GitHub MCP 服务器](<https://github.com/github/github-mcp-server>) - 官方 GitHub MCP 服务器
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart>) - SDK 基础知识和自定义工具
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - SDK-宽调试

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/mcp-debugging>) - 详细MCP故障排除
* [问题#9](<https://github.com/github/copilot-sdk/issues/9>) - 原始MCP工具使用问题
* [问题#36](<https://github.com/github/copilot-sdk/issues/36>) - MCP 文档跟踪问题


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

> [!NOTE]
> This is an evolving feature. See [issue #36](<https://github.com/github/copilot-sdk/issues/36>) for ongoing discussion.

## What is MCP?

[Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) is an open standard for connecting AI assistants to external tools and data sources. MCP servers can:

* Execute code or scripts
* Query databases
* Access file systems
* Call external APIs
* And much more

## Server types

The SDK supports two types of MCP servers:

| Type | Description | Use Case |
|------|-------------|----------|
| **Local/Stdio** | Runs as a subprocess, communicates via stdin/stdout | Local tools, file access, custom scripts |
| **HTTP/SSE** | Remote server accessed via HTTP | Shared services, cloud-hosted tools |

## Configuration

### Node.js / TypeScript

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5",
    mcpServers: {
        // Local MCP server (stdio)
        "my-local-server": {
            type: "local",
            command: "node",
            args: ["./mcp-server.js"],
            env: { DEBUG: "true" },
            cwd: "./servers",
            tools: ["*"],  // "*" = all tools, [] = none, or list specific tools
            timeout: 30000,
        },
        // Remote MCP server (HTTP)
        "github": {
            type: "http",
            url: "https://api.githubcopilot.com/mcp/",
            headers: { "Authorization": "Bearer ${TOKEN}" },
            tools: ["*"],
        },
    },
});
```

### Python

```python
import asyncio
from copilot import CopilotClient
from copilot.session import PermissionHandler

async def main():
    client = CopilotClient()
    await client.start()

    session = await client.create_session(on_permission_request=PermissionHandler.approve_all, model="gpt-5", mcp_servers={
        # Local MCP server (stdio)
        "my-local-server": {
            "type": "local",
            "command": "python",
            "args": ["./mcp_server.py"],
            "env": {"DEBUG": "true"},
            "cwd": "./servers",
            "tools": ["*"],
            "timeout": 30000,
        },
        # Remote MCP server (HTTP)
        "github": {
            "type": "http",
            "url": "https://api.githubcopilot.com/mcp/",
            "headers": {"Authorization": "Bearer ${TOKEN}"},
            "tools": ["*"],
        },
    })

    response = await session.send_and_wait("List my recent GitHub notifications")
    print(response.data.content)

    await client.stop()

asyncio.run(main())
```

### Go

```golang
package main

import (
    "context"
    "log"
    copilot "github.com/github/copilot-sdk/go"
)

func main() {
    ctx := context.Background()
    client := copilot.NewClient(nil)
    if err := client.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer client.Stop()

    session, err := client.CreateSession(ctx, &copilot.SessionConfig{
        Model: "gpt-5",
        MCPServers: map[string]copilot.MCPServerConfig{
            "my-local-server": copilot.MCPStdioServerConfig{
                Command: "node",
                Args:    []string{"./mcp-server.js"},
                Tools:   []string{"*"},
            },
        },
    })
    if err != nil {
        log.Fatal(err)
    }
    defer session.Disconnect()

    // Use the session...
}
```

### .NET

```csharp
using GitHub.Copilot;

await using var client = new CopilotClient();
await using var session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5",
    McpServers = new Dictionary<string, McpServerConfig>
    {
        ["my-local-server"] = new McpStdioServerConfig
        {
            Command = "node",
            Args = new List<string> { "./mcp-server.js" },
            Tools = new List<string> { "*" },
        },
    },
});
```

## Disabling configured servers per session

Set `disabledMcpServers` to exact MCP server names that must not run in a session.
The setting is scoped to the individual create or resume request; it does not
modify global MCP settings or the server configuration.

```typescript
const session = await client.createSession({
    mcpServers: {
        filesystem: { type: "local", command: "npx", args: ["-y", "@modelcontextprotocol/server-filesystem", "."] },
        github: { type: "http", url: "https://api.githubcopilot.com/mcp/" },
    },
    disabledMcpServers: ["github"],
});
```

| SDK | Configuration property |
| --- | --- |
| Node.js | `disabledMcpServers` |
| Python | `disabled_mcp_servers` |
| Go | `DisabledMCPServers` |
| .NET | `DisabledMcpServers` |
| Java | `setDisabledMcpServers(...)` |
| Rust | `with_disabled_mcp_servers(...)` |

On session creation and a **cold** resume, disabled servers are not started and
the runtime does not initiate their authentication. A resident resume cannot
undo a server that the runtime has already spawned. Names are matched exactly.

## Tool configuration

You can control which tools are available to an MCP server using the `tools` field.

### Allow all tools

Use `"*"` to enable all tools provided by the MCP server:

```typescript
tools: ["*"]
```

### Allow specific tools

Provide a list of tool names to restrict access:

```typescript
tools: ["bash", "edit"]
```

Only the listed tools will be available to the agent.

### Disable all tools

Use an empty array to disable all tools:

```typescript
tools: []
```

### Notes

* The `tools` field defines which tools are allowed.
* There is no separate `allow` or `disallow` configuration—tool access is controlled directly through this list.

## Quick start: filesystem MCP server

Here's a complete working example using the official [`@modelcontextprotocol/server-filesystem`](<https://www.npmjs.com/package/@modelcontextprotocol/server-filesystem>) MCP server:

```typescript
import { CopilotClient } from "@github/copilot-sdk";

async function main() {
    const client = new CopilotClient();

    // Create session with filesystem MCP server
    const session = await client.createSession({
        mcpServers: {
            filesystem: {
                type: "local",
                command: "npx",
                args: ["-y", "@modelcontextprotocol/server-filesystem", "/tmp"],
                tools: ["*"],
            },
        },
    });

    console.log("Session created:", session.sessionId);

    // The model can now use filesystem tools
    const result = await session.sendAndWait({
        prompt: "List the files in the allowed directory",
    });

    console.log("Response:", result?.data?.content);

    await session.disconnect();
    await client.stop();
}

main();
```

**Output:**

```text
Session created: 18b3482b-bcba-40ba-9f02-ad2ac949a59a
Response: The allowed directory is `/tmp`, which contains various files
and subdirectories including temporary system files, log files, and
directories for different applications.
```

> [!TIP]
> You can use any MCP server from the [MCP Servers Directory](<https://github.com/modelcontextprotocol/servers>). Popular options include `@modelcontextprotocol/server-github`, `@modelcontextprotocol/server-sqlite`, and `@modelcontextprotocol/server-puppeteer`.

## Configuration options

### Local/stdio server

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `type` | `"local"` or `"stdio"` | No | Server type (defaults to local) |
| `command` | `string` | Yes | Command to execute |
| `args` | `string[]` | Yes | Command arguments |
| `env` | `object` | No | Environment variables |
| `cwd` | `string` | No | Working directory |
| `tools` | `string[]` | No | Tools to enable (`["*"]` for all, `[]` for none) |
| `timeout` | `number` | No | Timeout in milliseconds |

### Remote server (HTTP/SSE)

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `type` | `"http"` or `"sse"` | Yes | Server type |
| `url` | `string` | Yes | Server URL |
| `headers` | `object` | No | HTTP headers (e.g., for auth) |
| `tools` | `string[]` | No | Tools to enable |
| `timeout` | `number` | No | Timeout in milliseconds |

## Troubleshooting

### Tools not showing up or not being invoked

1. **Verify the MCP server starts correctly**
   * Check that the command and args are correct
   * Ensure the server process doesn't crash on startup
   * Look for error output in stderr

1. **Check tool configuration**
   * Make sure `tools` is set to `["*"]` or lists the specific tools you need
   * An empty array `[]` means no tools are enabled

1. **Verify connectivity for remote servers**
   * Ensure the URL is accessible
   * Check that authentication headers are correct

### Common issues

| Issue | Solution |
|-------|----------|
| "MCP server not found" | Verify the command path is correct and executable |
| "Connection refused" (HTTP) | Check the URL and ensure the server is running |
| "Timeout" errors | Increase the `timeout` value or check server performance |
| Tools work but aren't called | Ensure your prompt clearly requires the tool's functionality |

For detailed debugging guidance, see the **[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/mcp-debugging>)**.

## Related resources

* [Model Context Protocol Specification](<https://modelcontextprotocol.io/>)
* [MCP Servers Directory](<https://github.com/modelcontextprotocol/servers>) - Community MCP servers
* [GitHub MCP Server](<https://github.com/github/github-mcp-server>) - Official GitHub MCP server
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart>) - SDK basics and custom tools
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - SDK-wide debugging

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/mcp-debugging>) - Detailed MCP troubleshooting
* [Issue #9](<https://github.com/github/copilot-sdk/issues/9>) - Original MCP tools usage question
* [Issue #36](<https://github.com/github/copilot-sdk/issues/36>) - MCP documentation tracking issue


</details>
