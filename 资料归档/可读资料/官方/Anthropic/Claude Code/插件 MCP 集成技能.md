# 插件 MCP 集成技能

介绍独立配置和清单内配置、服务类型、环境变量与工具调用，帮助为插件连接外部系统。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/mcp-integration/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/插件 MCP 集成技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: MCP Integration
description: This skill should be used when the user asks to "add MCP server", "integrate MCP", "configure MCP in plugin", "use .mcp.json", "set up Model Context Protocol", "connect external service", mentions "${CLAUDE_PLUGIN_ROOT} with MCP", or discusses MCP server types (SSE, stdio, HTTP, WebSocket). Provides comprehensive guidance for integrating Model Context Protocol servers into Claude Code plugins for external tool and service integration.
version: 0.1.0
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# MCP Claude Code 插件集成

## 概述

Model Context Protocol (MCP) 使 Claude Code 插件能够通过提供结构化工具访问来与外部服务和 API 集成。使用 MCP 集成将外部服务功能公开为 Claude Code 中的工具。

**关键功能：**
- 连接到外部服务（数据库、API、文件系统）
- 通过单一服务提供10+相关工具
- 处理 OAuth 和复杂的身份验证流程
- 捆绑 MCP 服务器以及用于自动设置的插件

## MCP服务器配置方法

插件可以通过两种方式捆绑MCP服务器：

### 方法1：专用。mcp.json（推荐）

在插件根目录创建 `.mcp.json`：

```json
{
  "database-tools": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/db-server",
    "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
    "env": {
      "DB_URL": "${DB_URL}"
    }
  }
}
```

**优点：**
- 明确的关注点分离
- 更容易维护
- 更适合多个服务器

### 方法2：内联在plugin.json中

将 `mcpServers` 字段添加到 plugin.json：

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "mcpServers": {
    "plugin-api": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/api-server",
      "args": ["--port", "8080"]
    }
  }
}
```

**优点：**
- 单个配置文件
- 适合简单的单服务器插件

## MCP 服务器类型

### stdio（本地进程）

将本地 MCP 服务器作为子进程执行。最适合本地工具和自定义服务器。

**配置：**
```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"],
    "env": {
      "LOG_LEVEL": "debug"
    }
  }
}
```

**用例：**
- 文件系统访问
- 本地数据库连接
- 自定义 MCP 服务器
- NPM-打包的MCP服务器

**进程管理：**
- Claude Code 生成并管理进程
- 通过 stdin/stdout 进行通信
- Claude Code 退出时终止

### SSE（服务器发送的事件）

连接到具有 OAuth 支持的托管 MCP 服务器。最适合云服务。

**配置：**
```json
{
  "asana": {
    "type": "sse",
    "url": "https://mcp.asana.com/sse"
  }
}
```

**用例：**
- 官方托管的 MCP 服务器（Asana、GitHub 等）
- 具有 MCP 端点的云服务
- 基于 OAuth 的身份验证
- 无需本地安装

**身份验证：**
- 自动处理 OAuth 流程
- 用户首次使用时提示
- 由Claude Code管理的代币

### HTTP (REST API)

通过令牌身份验证连接到 RESTful MCP 服务器。

**配置：**
```json
{
  "api-service": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}",
      "X-Custom-Header": "value"
    }
  }
}
```

**用例：**
- REST API-基于 MCP 服务器
- 基于令牌的身份验证
- 自定义 API 后端
- 无状态交互

### WebSocket（实时）

连接到WebSocketMCP服务器进行实时双向通信。

**配置：**
```json
{
  "realtime-service": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws",
    "headers": {
      "Authorization": "Bearer ${TOKEN}"
    }
  }
}
```

**用例：**
- 实时数据流
- 持久连接
- 来自服务器的推送通知
- 低延迟要求

## 环境变量扩展

所有MCP配置都支持环境变量替换：

**${CLAUDE_PLUGIN_ROOT}** - 插件目录（始终用于可移植性）：
```json
{
  "command": "${CLAUDE_PLUGIN_ROOT}/servers/my-server"
}
```

**用户环境变量** - 来自用户的 shell：
```json
{
  "env": {
    "API_KEY": "${MY_API_KEY}",
    "DATABASE_URL": "${DB_URL}"
  }
}
```

**最佳实践：**在插件README中记录所有必需的环境变量。

## MCP 工具命名

当MCP服务器提供工具时，它们会自动加上前缀：

**格式：** `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`

**示例：**
- 插件：`asana`
- 服务器：`asana`
- 工具：`create_task`
- **全名：** `mcp__plugin_asana_asana__asana_create_task`

### 在命令中使用 MCP 工具

在命令 frontmatter 中预先允许特定的 MCP 工具：

```markdown
---
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task",
  "mcp__plugin_asana_asana__asana_search_tasks"
]
---
```

**通配符（谨慎使用）：**
```markdown
---
allowed-tools: ["mcp__plugin_asana_asana__*"]
---
```

**最佳实践：**为了安全起见，预先允许特定工具，而不是通配符。

## 生命周期管理

**自动启动：**
- MCP 插件启用时服务器启动
- 首次使用工具之前建立的连接
- 配置更改需要重新启动

**生命周期：**
1. 插件加载
2. MCP配置已解析
3. 服务器进程已启动 (stdio) 或已建立连接 (SSE/HTTP/WS)
4. 发现并注册的工具
5. 可用工具为 `mcp__plugin_...__...`

**查看服务器：**
使用`/mcp`命令查看所有服务器，包括插件提供的服务器。

## 身份验证模式

### OAuth（SSE/HTTP）

OAuth 由 Claude Code 自动处理：

```json
{
  "type": "sse",
  "url": "https://mcp.example.com/sse"
}
```

用户首次使用时在浏览器中进行身份验证。无需额外配置。

### 基于令牌（标头）

静态或环境变量标记：

```json
{
  "type": "http",
  "url": "https://api.example.com",
  "headers": {
    "Authorization": "Bearer ${API_TOKEN}"
  }
}
```

在 README 中记录所需的环境变量。

### 环境变量（stdio）

将配置传递到MCP服务器：

```json
{
  "command": "python",
  "args": ["-m", "my_mcp_server"],
  "env": {
    "DATABASE_URL": "${DB_URL}",
    "API_KEY": "${API_KEY}",
    "LOG_LEVEL": "info"
  }
}
```

## 集成模式

### 图案 1：简单工具包装

命令使用 MCP 工具与用户交互：

```markdown
# Command: create-item.md
---
allowed-tools: ["mcp__plugin_name_server__create_item"]
---

Steps:
1. Gather item details from user
2. Use mcp__plugin_name_server__create_item
3. Confirm creation
```

**用于：** 在 MCP 调用之前添加验证或预处理。

### 模式 2：自主 Agent

Agents自主使用MCP工具：

```markdown
# Agent: data-analyzer.md

Analysis Process:
1. Query data via mcp__plugin_db_server__query
2. Process and analyze results
3. Generate insights report
```

**用于：** 无需用户交互的多步骤 MCP 工作流程。

### 模式3：多服务器插件

集成多个MCP服务器：

```json
{
  "github": {
    "type": "sse",
    "url": "https://mcp.github.com/sse"
  },
  "jira": {
    "type": "sse",
    "url": "https://mcp.jira.com/sse"
  }
}
```

**用于：** 跨越多个服务的工作流程。

## 安全最佳实践

### 使用HTTPS/WSS

始终使用安全连接：

```json
✅ "url": "https://mcp.example.com/sse"
❌ "url": "http://mcp.example.com/sse"
```

### 代币管理

**DO:**
- ✅ 使用环境变量作为令牌
- ✅ 在 README 中记录所需的环境变量
- ✅ 让 OAuth 流程处理身份验证

**DON'T:**
- ❌ 配置中的硬编码令牌
- ❌ 将令牌提交到 git
- ❌ 在文档中共享代币

### 权限范围

仅预允许必要的 MCP 工具：

```markdown
✅ allowed-tools: [
  "mcp__plugin_api_server__read_data",
  "mcp__plugin_api_server__create_item"
]

❌ allowed-tools: ["mcp__plugin_api_server__*"]
```

## 错误处理

### 连接失败

处理MCP服务器不可用：
- 在命令中提供后备行为
- 通知用户连接问题
- 检查服务器URL和配置

### 工具调用错误

处理失败的 MCP 操作：
- 在调用 MCP 工具之前验证输入
- 提供清晰的错误消息
- 检查速率限制和配额

### 配置错误

验证MCP配置：
- 在开发过程中测试服务器连接
- 验证 JSON 语法
- 检查所需的环境变量

## 性能考虑因素

### 延迟加载

MCP服务器按需连接：
- 并非所有服务器在启动时都连接
- 第一个工具使用触发连接
- 自动管理连接池

### 配料

尽可能批量处理类似的请求：

```
# Good: Single query with filters
tasks = search_tasks(project="X", assignee="me", limit=50)

# Avoid: Many individual queries
for id in task_ids:
    task = get_task(id)
```

## 测试 MCP 集成

### 本地测试

1. 在`.mcp.json`中配置MCP服务器
2. 本地安装插件（`.claude-plugin/`）
3. 运行`/mcp`以验证服务器是否出现
4. 测试工具在命令中调用
5. 检查 `claude --debug` 日志是否存在连接问题

### 验证清单

- [ ] MCP 配置有效 JSON
- [ ] 服务器 URL 正确且可访问
- [ ] 记录所需的环境变量
- [ ] 工具出现在 `/mcp` 输出中
- [ ] 身份验证有效（OAuth 或令牌）
- [ ] 命令调用工具成功
- [ ] 错误案例得到妥善处理

## 调试

### 启用调试日志记录

```bash
claude --debug
```

寻找：
- MCP 服务器连接尝试
- 工具发现日志
- 身份验证流程
- 工具调用错误

### 常见问题

**服务器未连接：**
- 检查URL是否正确
- 验证服务器正在运行 (stdio)
- 检查网络连接
- 查看身份验证配置

**工具不可用：**
- 验证服务器连接是否成功
- 检查工具名称是否完全匹配
- 运行 `/mcp` 查看可用工具
- 配置更改后重新启动Claude Code

**身份验证失败：**
- 清除缓存的身份验证令牌
- 重新验证
- 检查令牌范围和权限
- 验证环境变量设置

## 快速参考

### MCP 服务器类型

|类型|交通|最适合|授权|
|------|-----------|----------|------|
|标准输入输出|工艺流程|本地工具、自定义服务器|环境变量|
| SSE | HTTP |托管服务、云 API|开放认证|
| HTTP | REST |API 后端、令牌身份验证|代币|
|WS| WebSocket |实时、流式传输|代币|

### 配置清单

- [ ] 指定的服务器类型 (stdio/SSE/HTTP/ws)
- [ ] 类型特定字段完整（命令或 url）
- [ ] 身份验证已配置
- [ ] 记录环境变量
- 使用 [ ] HTTPS/WSS（不是 HTTP/WS）
- [ ] ${CLAUDE_PLUGIN_ROOT} 用于路径

### 最佳实践

**DO:**
- ✅ 使用 ${CLAUDE_PLUGIN_ROOT} 作为可移植路径
- ✅ 记录所需的环境变量
- ✅ 使用安全连接 (HTTPS/WSS)
- ✅ 在命令中预先允许特定的 MCP 工具
- ✅ 在发布之前测试 MCP 集成
- ✅ 优雅地处理连接和工具错误

**DON'T:**
- ❌ 硬编码绝对路径
- ❌ 将凭据提交到 git
- ❌使用HTTP代替HTTPS
- ❌ 预先允许所有带有通配符的工具
- ❌ 跳过错误处理
- ❌ 忘记记录设置

## 其他资源

### 参考文件

详细信息请咨询：

- **`references/server-types.md`** - 深入探讨每种服务器类型
- **`references/authentication.md`** - 身份验证模式和 OAuth
- **`references/tool-usage.md`** - 在命令和 agents 中使用 MCP 工具

### 配置示例

`examples/`中的工作示例：

- **`stdio-server.json`** - 本地 stdio MCP 服务器
- **`sse-server.json`** - 使用 OAuth 托管 SSE 服务器
- **`http-server.json`** - REST API 具有令牌身份验证

### 外部资源

- **官方MCP文档**：https://modelcontextprotocol.io/
- **Claude Code MCP 文档**：https://docs.claude.com/en/docs/claude-code/mcp
- **MCP SDK**: @modelcontextprotocol/sdk
- **测试**：使用`claude --debug`和`/mcp`命令

## 实施工作流程

要将 MCP 集成添加到插件：

1. 选择 MCP 服务器类型（stdio、SSE、HTTP、ws）
2. 使用配置在插件根目录创建 `.mcp.json`
3. 对所有文件引用使用 ${CLAUDE_PLUGIN_ROOT}
4. 在 README 中记录所需的环境变量
5. 使用`/mcp`命令在本地测试
6. 在相关命令中预先允许MCP工具
7. 处理身份验证（OAuth 或令牌）
8. 测试错误案例（连接失败、身份验证错误）
9. 文档 MCP 集成在插件 README 中

重点关注 custom/local 服务器的 stdio，以及使用 OAuth 的托管服务的 SSE。


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP Integration for Claude Code Plugins

## Overview

Model Context Protocol (MCP) enables Claude Code plugins to integrate with external services and APIs by providing structured tool access. Use MCP integration to expose external service capabilities as tools within Claude Code.

**Key capabilities:**
- Connect to external services (databases, APIs, file systems)
- Provide 10+ related tools from a single service
- Handle OAuth and complex authentication flows
- Bundle MCP servers with plugins for automatic setup

## MCP Server Configuration Methods

Plugins can bundle MCP servers in two ways:

### Method 1: Dedicated .mcp.json (Recommended)

Create `.mcp.json` at plugin root:

```json
{
  "database-tools": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/db-server",
    "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
    "env": {
      "DB_URL": "${DB_URL}"
    }
  }
}
```

**Benefits:**
- Clear separation of concerns
- Easier to maintain
- Better for multiple servers

### Method 2: Inline in plugin.json

Add `mcpServers` field to plugin.json:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "mcpServers": {
    "plugin-api": {
      "command": "${CLAUDE_PLUGIN_ROOT}/servers/api-server",
      "args": ["--port", "8080"]
    }
  }
}
```

**Benefits:**
- Single configuration file
- Good for simple single-server plugins

## MCP Server Types

### stdio (Local Process)

Execute local MCP servers as child processes. Best for local tools and custom servers.

**Configuration:**
```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-filesystem", "/allowed/path"],
    "env": {
      "LOG_LEVEL": "debug"
    }
  }
}
```

**Use cases:**
- File system access
- Local database connections
- Custom MCP servers
- NPM-packaged MCP servers

**Process management:**
- Claude Code spawns and manages the process
- Communicates via stdin/stdout
- Terminates when Claude Code exits

### SSE (Server-Sent Events)

Connect to hosted MCP servers with OAuth support. Best for cloud services.

**Configuration:**
```json
{
  "asana": {
    "type": "sse",
    "url": "https://mcp.asana.com/sse"
  }
}
```

**Use cases:**
- Official hosted MCP servers (Asana, GitHub, etc.)
- Cloud services with MCP endpoints
- OAuth-based authentication
- No local installation needed

**Authentication:**
- OAuth flows handled automatically
- User prompted on first use
- Tokens managed by Claude Code

### HTTP (REST API)

Connect to RESTful MCP servers with token authentication.

**Configuration:**
```json
{
  "api-service": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}",
      "X-Custom-Header": "value"
    }
  }
}
```

**Use cases:**
- REST API-based MCP servers
- Token-based authentication
- Custom API backends
- Stateless interactions

### WebSocket (Real-time)

Connect to WebSocket MCP servers for real-time bidirectional communication.

**Configuration:**
```json
{
  "realtime-service": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws",
    "headers": {
      "Authorization": "Bearer ${TOKEN}"
    }
  }
}
```

**Use cases:**
- Real-time data streaming
- Persistent connections
- Push notifications from server
- Low-latency requirements

## Environment Variable Expansion

All MCP configurations support environment variable substitution:

**${CLAUDE_PLUGIN_ROOT}** - Plugin directory (always use for portability):
```json
{
  "command": "${CLAUDE_PLUGIN_ROOT}/servers/my-server"
}
```

**User environment variables** - From user's shell:
```json
{
  "env": {
    "API_KEY": "${MY_API_KEY}",
    "DATABASE_URL": "${DB_URL}"
  }
}
```

**Best practice:** Document all required environment variables in plugin README.

## MCP Tool Naming

When MCP servers provide tools, they're automatically prefixed:

**Format:** `mcp__plugin_<plugin-name>_<server-name>__<tool-name>`

**Example:**
- Plugin: `asana`
- Server: `asana`
- Tool: `create_task`
- **Full name:** `mcp__plugin_asana_asana__asana_create_task`

### Using MCP Tools in Commands

Pre-allow specific MCP tools in command frontmatter:

```markdown
---
allowed-tools: [
  "mcp__plugin_asana_asana__asana_create_task",
  "mcp__plugin_asana_asana__asana_search_tasks"
]
---
```

**Wildcard (use sparingly):**
```markdown
---
allowed-tools: ["mcp__plugin_asana_asana__*"]
---
```

**Best practice:** Pre-allow specific tools, not wildcards, for security.

## Lifecycle Management

**Automatic startup:**
- MCP servers start when plugin enables
- Connection established before first tool use
- Restart required for configuration changes

**Lifecycle:**
1. Plugin loads
2. MCP configuration parsed
3. Server process started (stdio) or connection established (SSE/HTTP/WS)
4. Tools discovered and registered
5. Tools available as `mcp__plugin_...__...`

**Viewing servers:**
Use `/mcp` command to see all servers including plugin-provided ones.

## Authentication Patterns

### OAuth (SSE/HTTP)

OAuth handled automatically by Claude Code:

```json
{
  "type": "sse",
  "url": "https://mcp.example.com/sse"
}
```

User authenticates in browser on first use. No additional configuration needed.

### Token-Based (Headers)

Static or environment variable tokens:

```json
{
  "type": "http",
  "url": "https://api.example.com",
  "headers": {
    "Authorization": "Bearer ${API_TOKEN}"
  }
}
```

Document required environment variables in README.

### Environment Variables (stdio)

Pass configuration to MCP server:

```json
{
  "command": "python",
  "args": ["-m", "my_mcp_server"],
  "env": {
    "DATABASE_URL": "${DB_URL}",
    "API_KEY": "${API_KEY}",
    "LOG_LEVEL": "info"
  }
}
```

## Integration Patterns

### Pattern 1: Simple Tool Wrapper

Commands use MCP tools with user interaction:

```markdown
# Command: create-item.md
---
allowed-tools: ["mcp__plugin_name_server__create_item"]
---

Steps:
1. Gather item details from user
2. Use mcp__plugin_name_server__create_item
3. Confirm creation
```

**Use for:** Adding validation or preprocessing before MCP calls.

### Pattern 2: Autonomous Agent

Agents use MCP tools autonomously:

```markdown
# Agent: data-analyzer.md

Analysis Process:
1. Query data via mcp__plugin_db_server__query
2. Process and analyze results
3. Generate insights report
```

**Use for:** Multi-step MCP workflows without user interaction.

### Pattern 3: Multi-Server Plugin

Integrate multiple MCP servers:

```json
{
  "github": {
    "type": "sse",
    "url": "https://mcp.github.com/sse"
  },
  "jira": {
    "type": "sse",
    "url": "https://mcp.jira.com/sse"
  }
}
```

**Use for:** Workflows spanning multiple services.

## Security Best Practices

### Use HTTPS/WSS

Always use secure connections:

```json
✅ "url": "https://mcp.example.com/sse"
❌ "url": "http://mcp.example.com/sse"
```

### Token Management

**DO:**
- ✅ Use environment variables for tokens
- ✅ Document required env vars in README
- ✅ Let OAuth flow handle authentication

**DON'T:**
- ❌ Hardcode tokens in configuration
- ❌ Commit tokens to git
- ❌ Share tokens in documentation

### Permission Scoping

Pre-allow only necessary MCP tools:

```markdown
✅ allowed-tools: [
  "mcp__plugin_api_server__read_data",
  "mcp__plugin_api_server__create_item"
]

❌ allowed-tools: ["mcp__plugin_api_server__*"]
```

## Error Handling

### Connection Failures

Handle MCP server unavailability:
- Provide fallback behavior in commands
- Inform user of connection issues
- Check server URL and configuration

### Tool Call Errors

Handle failed MCP operations:
- Validate inputs before calling MCP tools
- Provide clear error messages
- Check rate limiting and quotas

### Configuration Errors

Validate MCP configuration:
- Test server connectivity during development
- Validate JSON syntax
- Check required environment variables

## Performance Considerations

### Lazy Loading

MCP servers connect on-demand:
- Not all servers connect at startup
- First tool use triggers connection
- Connection pooling managed automatically

### Batching

Batch similar requests when possible:

```
# Good: Single query with filters
tasks = search_tasks(project="X", assignee="me", limit=50)

# Avoid: Many individual queries
for id in task_ids:
    task = get_task(id)
```

## Testing MCP Integration

### Local Testing

1. Configure MCP server in `.mcp.json`
2. Install plugin locally (`.claude-plugin/`)
3. Run `/mcp` to verify server appears
4. Test tool calls in commands
5. Check `claude --debug` logs for connection issues

### Validation Checklist

- [ ] MCP configuration is valid JSON
- [ ] Server URL is correct and accessible
- [ ] Required environment variables documented
- [ ] Tools appear in `/mcp` output
- [ ] Authentication works (OAuth or tokens)
- [ ] Tool calls succeed from commands
- [ ] Error cases handled gracefully

## Debugging

### Enable Debug Logging

```bash
claude --debug
```

Look for:
- MCP server connection attempts
- Tool discovery logs
- Authentication flows
- Tool call errors

### Common Issues

**Server not connecting:**
- Check URL is correct
- Verify server is running (stdio)
- Check network connectivity
- Review authentication configuration

**Tools not available:**
- Verify server connected successfully
- Check tool names match exactly
- Run `/mcp` to see available tools
- Restart Claude Code after config changes

**Authentication failing:**
- Clear cached auth tokens
- Re-authenticate
- Check token scopes and permissions
- Verify environment variables set

## Quick Reference

### MCP Server Types

| Type | Transport | Best For | Auth |
|------|-----------|----------|------|
| stdio | Process | Local tools, custom servers | Env vars |
| SSE | HTTP | Hosted services, cloud APIs | OAuth |
| HTTP | REST | API backends, token auth | Tokens |
| ws | WebSocket | Real-time, streaming | Tokens |

### Configuration Checklist

- [ ] Server type specified (stdio/SSE/HTTP/ws)
- [ ] Type-specific fields complete (command or url)
- [ ] Authentication configured
- [ ] Environment variables documented
- [ ] HTTPS/WSS used (not HTTP/WS)
- [ ] ${CLAUDE_PLUGIN_ROOT} used for paths

### Best Practices

**DO:**
- ✅ Use ${CLAUDE_PLUGIN_ROOT} for portable paths
- ✅ Document required environment variables
- ✅ Use secure connections (HTTPS/WSS)
- ✅ Pre-allow specific MCP tools in commands
- ✅ Test MCP integration before publishing
- ✅ Handle connection and tool errors gracefully

**DON'T:**
- ❌ Hardcode absolute paths
- ❌ Commit credentials to git
- ❌ Use HTTP instead of HTTPS
- ❌ Pre-allow all tools with wildcards
- ❌ Skip error handling
- ❌ Forget to document setup

## Additional Resources

### Reference Files

For detailed information, consult:

- **`references/server-types.md`** - Deep dive on each server type
- **`references/authentication.md`** - Authentication patterns and OAuth
- **`references/tool-usage.md`** - Using MCP tools in commands and agents

### Example Configurations

Working examples in `examples/`:

- **`stdio-server.json`** - Local stdio MCP server
- **`sse-server.json`** - Hosted SSE server with OAuth
- **`http-server.json`** - REST API with token auth

### External Resources

- **Official MCP Docs**: https://modelcontextprotocol.io/
- **Claude Code MCP Docs**: https://docs.claude.com/en/docs/claude-code/mcp
- **MCP SDK**: @modelcontextprotocol/sdk
- **Testing**: Use `claude --debug` and `/mcp` command

## Implementation Workflow

To add MCP integration to a plugin:

1. Choose MCP server type (stdio, SSE, HTTP, ws)
2. Create `.mcp.json` at plugin root with configuration
3. Use ${CLAUDE_PLUGIN_ROOT} for all file references
4. Document required environment variables in README
5. Test locally with `/mcp` command
6. Pre-allow MCP tools in relevant commands
7. Handle authentication (OAuth or tokens)
8. Test error cases (connection failures, auth errors)
9. Document MCP integration in plugin README

Focus on stdio for custom/local servers, SSE for hosted services with OAuth.


</details>
