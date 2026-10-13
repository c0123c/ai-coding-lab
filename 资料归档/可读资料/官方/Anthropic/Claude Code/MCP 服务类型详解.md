# MCP 服务类型详解

比较不同服务传输方式、进程生命周期、配置、使用场景和运行建议，帮助选择插件接入方式。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/mcp-integration/references/server-types.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/MCP 服务类型详解.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# MCP 服务器类型：深入探讨

Claude Code 插件支持的所有 MCP 服务器类型的完整参考。

## stdio（标准 Input/Output）

### 概述

将本地 MCP 服务器作为子进程执行，并通过 stdin/stdout. 进行通信，是本地工具、自定义服务器和 NPM 软件包的最佳选择。

### 配置

**基本：**
```json
{
  "my-server": {
    "command": "npx",
    "args": ["-y", "my-mcp-server"]
  }
}
```

**搭配环境：**
```json
{
  "my-server": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/custom-server",
    "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
    "env": {
      "API_KEY": "${MY_API_KEY}",
      "LOG_LEVEL": "debug",
      "DATABASE_URL": "${DB_URL}"
    }
  }
}
```

### 流程生命周期

1. **启动**：Claude Code使用`command`和`args`生成进程
2. **通信**：通过stdin/stdout发送JSON-RPC消息
3. **生命周期**：整个Claude Code会话的进程运行
4. **关闭**：Claude Code退出时进程终止

### 使用案例

**NPM 封装：**
```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
  }
}
```

**自定义脚本：**
```json
{
  "custom": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/my-server.js",
    "args": ["--verbose"]
  }
}
```

**Python 服务器：**
```json
{
  "python-server": {
    "command": "python",
    "args": ["-m", "my_mcp_server"],
    "env": {
      "PYTHONUNBUFFERED": "1"
    }
  }
}
```

### 最佳实践

1. **使用绝对路径或${CLAUDE_PLUGIN_ROOT}**
2. **为Python服务器设置PYTHONUNBUFFERED**
3. **通过args或env传递配置，而不是stdin**
4. **优雅地处理服务器崩溃**
5. **登录到stderr，而不是stdout（stdout用于MCP协议）**

### 故障排除

**服务器无法启动：**
- 检查命令是否存在并且可执行
- 验证文件路径是否正确
- 检查权限
- 查看 `claude --debug` 日志

**通讯失败：**
- 确保服务器正确使用stdin/stdout
- 检查杂散 print/console.log 语句
- 验证JSON-RPC格式

## SSE（服务器发送的事件）

### 概述

通过 HTTP 连接到托管的 MCP 服务器，并使用服务器发送的事件进行流式传输。最适合云服务和 OAuth 身份验证。

### 配置

**基本：**
```json
{
  "hosted-service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  }
}
```

**带标头：**
```json
{
  "service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse",
    "headers": {
      "X-API-Version": "v1",
      "X-Client-ID": "${CLIENT_ID}"
    }
  }
}
```

### 连接生命周期

1. **初始化**：HTTP与URL建立连接
2. **握手**：MCP协议协商
3. **流式处理**：服务器通过SSE发送事件
4. **请求**：客户端发送HTTP POST以进行工具调用
5. **重新连接**：断开连接时自动重新连接

### 认证

**OAuth（自动）：**
```json
{
  "asana": {
    "type": "sse",
    "url": "https://mcp.asana.com/sse"
  }
}
```

Claude Code 处理 OAuth 流程：
1. 用户首次使用时提示进行身份验证
2. 打开浏览器以查看 OAuth 流程
3. 安全存储的令牌
4. 自动令牌刷新

**自定义标头：**
```json
{
  "service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

### 使用案例

**官方服务：**
- 体式：`https://mcp.asana.com/sse`
- GitHub: `https://mcp.github.com/sse`
- 其他托管 MCP 服务器

**自定义托管服务器：**
部署您自己的MCP服务器并通过HTTPS + SSE公开。

### 最佳实践

1. **始终使用HTTPS，从不使用HTTP**
2. **让 OAuth 在可用时处理身份验证**
3. **使用令牌的环境变量**
4. **优雅地处理连接失败**
5. **所需的文档 OAuth 范围**

### 故障排除

**连接被拒绝：**
- 检查 URL 是否正确且可访问
- 验证 HTTPS 证书是否有效
- 检查网络连接
- 检查防火墙设置

**OAuth 失败：**
- 清除缓存的令牌
- 检查 OAuth 范围
- 验证重定向 URL
- 重新验证

## HTTP (REST API)

### 概述

通过标准 HTTP 请求连接到 RESTful MCP 服务器。最适合基于令牌的身份验证和无状态交互。

### 配置

**基本：**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp"
  }
}
```

**有认证：**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}",
      "Content-Type": "application/json",
      "X-API-Version": "2024-01-01"
    }
  }
}
```

### Request/Response 流程

1. **工具发现**：GET发现可用工具
2. **工具调用**：POST包含工具名称和参数
3. **响应**：JSON响应，包含结果或错误
4. **Stateless**：每个请求独立

### 认证

**基于令牌：**
```json
{
  "headers": {
    "Authorization": "Bearer ${API_TOKEN}"
  }
}
```

**API 密钥：**
```json
{
  "headers": {
    "X-API-Key": "${API_KEY}"
  }
}
```

**自定义身份验证：**
```json
{
  "headers": {
    "X-Auth-Token": "${AUTH_TOKEN}",
    "X-User-ID": "${USER_ID}"
  }
}
```

### 使用案例

- REST API 后端
- 内部服务
- 微服务
- 无服务器功能

### 最佳实践

1. **所有连接均使用HTTPS**
2. **将令牌存储在环境变量中**
3. **实现瞬态故障的重试逻辑**
4. **处理速率限制**
5. **设置适当的超时**

### 故障排除

**HTTP错误：**
- 401：检查身份验证标头
- 403：验证权限
- 429：实施速率限制
- 500：检查服务器日志

**超时问题：**
- 如果需要增加超时
- 检查服务器性能
- 优化工具实施

## WebSocket（实时）

### 概述

通过WebSocket连接到MCP服务器，进行实时双向通信。最适合流媒体和低延迟应用程序。

### 配置

**基本：**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws"
  }
}
```

**有认证：**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws",
    "headers": {
      "Authorization": "Bearer ${TOKEN}",
      "X-Client-ID": "${CLIENT_ID}"
    }
  }
}
```

### 连接生命周期

1. **握手**：WebSocket升级请求
2. **连接**：持久双向通道
3. **消息**：JSON-RPC超过WebSocket
4. **Heartbeat**：保持活动消息
5. **重新连接**：断开连接时自动

### 使用案例

- 实时数据流
- 实时更新和通知
- 协同编辑
- 低延迟工具调用
- 来自服务器的推送通知

### 最佳实践

1. **使用WSS（安全WebSocket），切勿使用WS**
2. **实施heartbeat/ping-pong**
3. **处理重连逻辑**
4. **断开连接时缓冲消息**
5. **设置连接超时**

### 故障排除

**连接断开：**
- 实现重连逻辑
- 检查网络稳定性
- 验证服务器支持WebSocket
- 检查防火墙设置

**消息传递：**
- 实施消息确认
- 处理无序消息
- 断开连接时的缓冲

## 比较矩阵

|特点|标准输入输出| SSE | HTTP | WebSocket |
|---------|-------|-----|------|-----------|
|**交通**|工艺流程| HTTP/SSE | HTTP | WebSocket |
|**方向**|双向|服务器→客户端| Request/Response |双向|
|**状态**|有状态的|有状态的|无国籍|有状态的|
|**授权**|环境变量| OAuth/Headers |标头|标头|
|**用例**|本地工具|云服务|REST API|实时|
|**延迟**|最低|中等|中等|低|
|**设置**|简单|中等|简单|中等|
|**重新连接**|进程重生|自动| N/A |自动|

## 选择正确的类型

**在以下情况下使用 stdio：**
- 运行本地工具或自定义服务器
- 需要最低延迟
- 使用文件系统或本地数据库
- 带插件的分布式服务器

**在以下情况下使用SSE：**
- 连接到托管服务
- 需要OAuth认证
- 使用官方 MCP 服务器（Asana、GitHub）
- 想要自动重连

**在以下情况下使用HTTP：**
- 与 REST API 集成
- 需要无状态交互
- 使用基于令牌的身份验证
- 简单的request/response图案

**在以下情况下使用WebSocket：**
- 需要实时更新
- 构建协作功能
- 低延迟至关重要
- 需要双向流媒体

## 类型之间的迁移

### 从 stdio 到 SSE

**之前（stdio）：**
```json
{
  "local-server": {
    "command": "node",
    "args": ["server.js"]
  }
}
```

**之后（SSE - 部署服务器）：**
```json
{
  "hosted-server": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  }
}
```

### 从 HTTP 到 WebSocket

**之前（HTTP）：**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp"
  }
}
```

**（WebSocket）之后：**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://api.example.com/ws"
  }
}
```

优点：实时更新、低延迟、双向通信。

## 高级配置

### 多台服务器

组合不同类型：

```json
{
  "local-db": {
    "command": "npx",
    "args": ["-y", "mcp-server-sqlite", "./data.db"]
  },
  "cloud-api": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  },
  "internal-service": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

### 条件配置

使用环境变量切换服务器：

```json
{
  "api": {
    "type": "http",
    "url": "${API_URL}",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

为dev/prod设置不同的值：
- 开发人员：`API_URL=http://localhost:8080/mcp`
- 产品：`API_URL=https://api.production.com/mcp`

## 安全考虑

### 工作室安全

- 验证命令路径
- 不执行用户提供的命令
- 限制环境变量访问
- 限制文件系统访问

### 网络安全

- 始终使用 HTTPS/WSS
- 验证 SSL 证书
- 不要跳过证书验证
- 使用安全令牌存储

### 代币管理

- 切勿对令牌进行硬编码
- 使用环境变量
- 定期轮换代币
- 实施令牌刷新
- 所需文件范围

## 结论

根据您的用例选择 MCP 服务器类型：
- **stdio** 用于本地、自定义或 NPM- 打包服务器
- **SSE** 用于使用 OAuth 的托管服务
- **HTTP** 用于具有令牌身份验证的 REST API
- **WebSocket** 用于实时双向通信

彻底测试并妥善处理错误，以实现稳健的 MCP 集成。


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP Server Types: Deep Dive

Complete reference for all MCP server types supported in Claude Code plugins.

## stdio (Standard Input/Output)

### Overview

Execute local MCP servers as child processes with communication via stdin/stdout. Best choice for local tools, custom servers, and NPM packages.

### Configuration

**Basic:**
```json
{
  "my-server": {
    "command": "npx",
    "args": ["-y", "my-mcp-server"]
  }
}
```

**With environment:**
```json
{
  "my-server": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/custom-server",
    "args": ["--config", "${CLAUDE_PLUGIN_ROOT}/config.json"],
    "env": {
      "API_KEY": "${MY_API_KEY}",
      "LOG_LEVEL": "debug",
      "DATABASE_URL": "${DB_URL}"
    }
  }
}
```

### Process Lifecycle

1. **Startup**: Claude Code spawns process with `command` and `args`
2. **Communication**: JSON-RPC messages via stdin/stdout
3. **Lifecycle**: Process runs for entire Claude Code session
4. **Shutdown**: Process terminated when Claude Code exits

### Use Cases

**NPM Packages:**
```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
  }
}
```

**Custom Scripts:**
```json
{
  "custom": {
    "command": "${CLAUDE_PLUGIN_ROOT}/servers/my-server.js",
    "args": ["--verbose"]
  }
}
```

**Python Servers:**
```json
{
  "python-server": {
    "command": "python",
    "args": ["-m", "my_mcp_server"],
    "env": {
      "PYTHONUNBUFFERED": "1"
    }
  }
}
```

### Best Practices

1. **Use absolute paths or ${CLAUDE_PLUGIN_ROOT}**
2. **Set PYTHONUNBUFFERED for Python servers**
3. **Pass configuration via args or env, not stdin**
4. **Handle server crashes gracefully**
5. **Log to stderr, not stdout (stdout is for MCP protocol)**

### Troubleshooting

**Server won't start:**
- Check command exists and is executable
- Verify file paths are correct
- Check permissions
- Review `claude --debug` logs

**Communication fails:**
- Ensure server uses stdin/stdout correctly
- Check for stray print/console.log statements
- Verify JSON-RPC format

## SSE (Server-Sent Events)

### Overview

Connect to hosted MCP servers via HTTP with server-sent events for streaming. Best for cloud services and OAuth authentication.

### Configuration

**Basic:**
```json
{
  "hosted-service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  }
}
```

**With headers:**
```json
{
  "service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse",
    "headers": {
      "X-API-Version": "v1",
      "X-Client-ID": "${CLIENT_ID}"
    }
  }
}
```

### Connection Lifecycle

1. **Initialization**: HTTP connection established to URL
2. **Handshake**: MCP protocol negotiation
3. **Streaming**: Server sends events via SSE
4. **Requests**: Client sends HTTP POST for tool calls
5. **Reconnection**: Automatic reconnection on disconnect

### Authentication

**OAuth (Automatic):**
```json
{
  "asana": {
    "type": "sse",
    "url": "https://mcp.asana.com/sse"
  }
}
```

Claude Code handles OAuth flow:
1. User prompted to authenticate on first use
2. Opens browser for OAuth flow
3. Tokens stored securely
4. Automatic token refresh

**Custom Headers:**
```json
{
  "service": {
    "type": "sse",
    "url": "https://mcp.example.com/sse",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

### Use Cases

**Official Services:**
- Asana: `https://mcp.asana.com/sse`
- GitHub: `https://mcp.github.com/sse`
- Other hosted MCP servers

**Custom Hosted Servers:**
Deploy your own MCP server and expose via HTTPS + SSE.

### Best Practices

1. **Always use HTTPS, never HTTP**
2. **Let OAuth handle authentication when available**
3. **Use environment variables for tokens**
4. **Handle connection failures gracefully**
5. **Document OAuth scopes required**

### Troubleshooting

**Connection refused:**
- Check URL is correct and accessible
- Verify HTTPS certificate is valid
- Check network connectivity
- Review firewall settings

**OAuth fails:**
- Clear cached tokens
- Check OAuth scopes
- Verify redirect URLs
- Re-authenticate

## HTTP (REST API)

### Overview

Connect to RESTful MCP servers via standard HTTP requests. Best for token-based auth and stateless interactions.

### Configuration

**Basic:**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp"
  }
}
```

**With authentication:**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}",
      "Content-Type": "application/json",
      "X-API-Version": "2024-01-01"
    }
  }
}
```

### Request/Response Flow

1. **Tool Discovery**: GET to discover available tools
2. **Tool Invocation**: POST with tool name and parameters
3. **Response**: JSON response with results or errors
4. **Stateless**: Each request independent

### Authentication

**Token-Based:**
```json
{
  "headers": {
    "Authorization": "Bearer ${API_TOKEN}"
  }
}
```

**API Key:**
```json
{
  "headers": {
    "X-API-Key": "${API_KEY}"
  }
}
```

**Custom Auth:**
```json
{
  "headers": {
    "X-Auth-Token": "${AUTH_TOKEN}",
    "X-User-ID": "${USER_ID}"
  }
}
```

### Use Cases

- REST API backends
- Internal services
- Microservices
- Serverless functions

### Best Practices

1. **Use HTTPS for all connections**
2. **Store tokens in environment variables**
3. **Implement retry logic for transient failures**
4. **Handle rate limiting**
5. **Set appropriate timeouts**

### Troubleshooting

**HTTP errors:**
- 401: Check authentication headers
- 403: Verify permissions
- 429: Implement rate limiting
- 500: Check server logs

**Timeout issues:**
- Increase timeout if needed
- Check server performance
- Optimize tool implementations

## WebSocket (Real-time)

### Overview

Connect to MCP servers via WebSocket for real-time bidirectional communication. Best for streaming and low-latency applications.

### Configuration

**Basic:**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws"
  }
}
```

**With authentication:**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://mcp.example.com/ws",
    "headers": {
      "Authorization": "Bearer ${TOKEN}",
      "X-Client-ID": "${CLIENT_ID}"
    }
  }
}
```

### Connection Lifecycle

1. **Handshake**: WebSocket upgrade request
2. **Connection**: Persistent bidirectional channel
3. **Messages**: JSON-RPC over WebSocket
4. **Heartbeat**: Keep-alive messages
5. **Reconnection**: Automatic on disconnect

### Use Cases

- Real-time data streaming
- Live updates and notifications
- Collaborative editing
- Low-latency tool calls
- Push notifications from server

### Best Practices

1. **Use WSS (secure WebSocket), never WS**
2. **Implement heartbeat/ping-pong**
3. **Handle reconnection logic**
4. **Buffer messages during disconnection**
5. **Set connection timeouts**

### Troubleshooting

**Connection drops:**
- Implement reconnection logic
- Check network stability
- Verify server supports WebSocket
- Review firewall settings

**Message delivery:**
- Implement message acknowledgment
- Handle out-of-order messages
- Buffer during disconnection

## Comparison Matrix

| Feature | stdio | SSE | HTTP | WebSocket |
|---------|-------|-----|------|-----------|
| **Transport** | Process | HTTP/SSE | HTTP | WebSocket |
| **Direction** | Bidirectional | Server→Client | Request/Response | Bidirectional |
| **State** | Stateful | Stateful | Stateless | Stateful |
| **Auth** | Env vars | OAuth/Headers | Headers | Headers |
| **Use Case** | Local tools | Cloud services | REST APIs | Real-time |
| **Latency** | Lowest | Medium | Medium | Low |
| **Setup** | Easy | Medium | Easy | Medium |
| **Reconnect** | Process respawn | Automatic | N/A | Automatic |

## Choosing the Right Type

**Use stdio when:**
- Running local tools or custom servers
- Need lowest latency
- Working with file systems or local databases
- Distributing server with plugin

**Use SSE when:**
- Connecting to hosted services
- Need OAuth authentication
- Using official MCP servers (Asana, GitHub)
- Want automatic reconnection

**Use HTTP when:**
- Integrating with REST APIs
- Need stateless interactions
- Using token-based auth
- Simple request/response pattern

**Use WebSocket when:**
- Need real-time updates
- Building collaborative features
- Low-latency critical
- Bi-directional streaming required

## Migration Between Types

### From stdio to SSE

**Before (stdio):**
```json
{
  "local-server": {
    "command": "node",
    "args": ["server.js"]
  }
}
```

**After (SSE - deploy server):**
```json
{
  "hosted-server": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  }
}
```

### From HTTP to WebSocket

**Before (HTTP):**
```json
{
  "api": {
    "type": "http",
    "url": "https://api.example.com/mcp"
  }
}
```

**After (WebSocket):**
```json
{
  "realtime": {
    "type": "ws",
    "url": "wss://api.example.com/ws"
  }
}
```

Benefits: Real-time updates, lower latency, bi-directional communication.

## Advanced Configuration

### Multiple Servers

Combine different types:

```json
{
  "local-db": {
    "command": "npx",
    "args": ["-y", "mcp-server-sqlite", "./data.db"]
  },
  "cloud-api": {
    "type": "sse",
    "url": "https://mcp.example.com/sse"
  },
  "internal-service": {
    "type": "http",
    "url": "https://api.example.com/mcp",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

### Conditional Configuration

Use environment variables to switch servers:

```json
{
  "api": {
    "type": "http",
    "url": "${API_URL}",
    "headers": {
      "Authorization": "Bearer ${API_TOKEN}"
    }
  }
}
```

Set different values for dev/prod:
- Dev: `API_URL=http://localhost:8080/mcp`
- Prod: `API_URL=https://api.production.com/mcp`

## Security Considerations

### Stdio Security

- Validate command paths
- Don't execute user-provided commands
- Limit environment variable access
- Restrict file system access

### Network Security

- Always use HTTPS/WSS
- Validate SSL certificates
- Don't skip certificate verification
- Use secure token storage

### Token Management

- Never hardcode tokens
- Use environment variables
- Rotate tokens regularly
- Implement token refresh
- Document scopes required

## Conclusion

Choose the MCP server type based on your use case:
- **stdio** for local, custom, or NPM-packaged servers
- **SSE** for hosted services with OAuth
- **HTTP** for REST APIs with token auth
- **WebSocket** for real-time bidirectional communication

Test thoroughly and handle errors gracefully for robust MCP integration.


</details>
