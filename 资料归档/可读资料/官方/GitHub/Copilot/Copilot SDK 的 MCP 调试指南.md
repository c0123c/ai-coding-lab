# Copilot SDK 的 MCP 调试指南

提供日志、独立协议测试和工具列表检查，排查服务无法启动、工具未调用、超时、JSON-RPC 及平台路径问题。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-20](<https://github.com/github/docs/commit/cf960fc42d0894ea600cb10548e681ab141acaf4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/troubleshooting/mcp-debugging.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 的 MCP 调试指南.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: MCP server debugging guide
shortTitle: MCP Debugging
intro: >-
  This guide covers debugging techniques specific to MCP (Model Context
  Protocol) servers when using the Copilot SDK.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/troubleshooting/debug-mcp-servers
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 目录

* [快速诊断](<#快速诊断>)
* [独立测试MCP服务器](<#独立测试mcp服务器>)
* [常见问题](<#常见问题>)
* [特定于平台的问题](<#平台特定问题>)
* [高级调试](<#高级调试>)

## 快速诊断

### 清单

在深入研究之前，请验证以下基础知识：

* [ ] MCP 服务器可执行文件存在并且可以运行
* [ ] 命令路径正确（如有疑问，请使用绝对路径）
* [ ] 工具已启用（`tools: ["*"]` 或特定工具名称）
* [ ] 服务器正确实现 MCP 协议（响应 `initialize`）
* [ ] 没有 firewall/antivirus 阻止进程 (Windows)

### 启用 MCP 调试日志记录

将环境变量添加到您的 MCP 服务器配置中：

```typescript
mcpServers: {
  "my-server": {
    type: "local",
    command: "/path/to/server",
    args: [],
    env: {
      MCP_DEBUG: "1",
      DEBUG: "*",
      NODE_DEBUG: "mcp",  // For Node.js MCP servers
    },
  },
}
```

## 独立测试MCP服务器

始终首先在 SDK 之外测试您的 MCP 服务器。

### 手动协议测试

通过 stdin 发送 `initialize` 请求：

```bash
# Unix/macOS
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' | /path/to/your/mcp-server

# Windows (PowerShell)
'{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' | C:\path\to\your\mcp-server.exe
```

**预期回复：**

```json
{"jsonrpc":"2.0","id":1,"result":{"protocolVersion":"2024-11-05","capabilities":{"tools":{}},"serverInfo":{"name":"your-server","version":"1.0"}}}
```

### 测试工具清单

初始化后，请求工具列表：

```bash
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}' | /path/to/your/mcp-server
```

**预期回复：**

```json
{"jsonrpc":"2.0","id":2,"result":{"tools":[{"name":"my_tool","description":"Does something","inputSchema":{...}}]}}
```

### 交互式测试脚本

创建一个测试脚本以交互方式调试您的 MCP 服务器：

```bash
#!/bin/bash
# test-mcp.sh

SERVER="$1"

# Initialize
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'

# Send initialized notification
echo '{"jsonrpc":"2.0","method":"notifications/initialized"}'

# List tools
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}'

# Keep stdin open
cat
```

用途：

```bash
./test-mcp.sh | /path/to/mcp-server
```

## 常见问题

### 服务器未启动

**症状：**没有工具出现，日志中没有错误。

**原因及解决方案：**

|原因|解决方案|
|-------|----------|
|命令路径错误|使用绝对路径：`/usr/local/bin/server`|
|缺少可执行权限|运行`chmod +x /path/to/server`|
|缺少依赖项|使用 `ldd` (Linux) 检查或手动运行|
|工作目录问题|在配置中设置`cwd`|

**手动运行调试：**

```bash
# Run exactly what the SDK would run
cd /expected/working/dir
/path/to/command arg1 arg2
```

### 服务器启动但工具不出现

**症状：**服务器进程运行，但没有可用的工具。

**原因及解决方案：**

1. **配置中未启用工具：**

   ```typescript
   mcpServers: {
     "server": {
       // ...
       tools: ["*"],  // Must be "*" or list of tool names
     },
   }
   ```

1. **服务器不公开工具：**
   * 手动使用 `tools/list` 请求进行测试
   * 检查服务器是否实现了`tools/list`方法

1. **初始化握手失败：**
   * 服务器必须正确响应`initialize`
   * 服务器必须处理`notifications/initialized`

### 列出但从未调用过的工具

**症状：** 工具出现在调试日志中，但模型不使用它们。

**原因及解决方案：**

1. **提示明显不需要该工具：**

   ```typescript
   // Too vague
   await session.sendAndWait({ prompt: "What's the weather?" });
   
   // Better - explicitly mentions capability
   await session.sendAndWait({ 
     prompt: "Use the weather tool to get the current temperature in Seattle" 
   });
   ```

1. **工具说明不清楚：**

   ```typescript
   // Bad - model doesn't know when to use it
   { name: "do_thing", description: "Does a thing" }
   
   // Good - clear purpose
   { name: "get_weather", description: "Get current weather conditions for a city. Returns temperature, humidity, and conditions." }
   ```

1. **工具架构问题：**
   * 确保 `inputSchema` 有效 JSON 架构
   * 必填字段必须位于 `required` 数组中

### 超时错误

**症状：** `MCP tool call timed out` 错误。

**解决方案：**

1. **增加超时：**

   ```typescript
   mcpServers: {
     "slow-server": {
       // ...
       timeout: 300000,  // 5 minutes
     },
   }
   ```

1. **优化服务器性能：**
   * 添加进度日志记录以识别瓶颈
   * 考虑异步操作
   * 检查是否阻塞 I/O

1. **对于长时间运行的工具**，如果支持，请考虑流式响应。

### JSON-RPC 错误

**症状：**解析错误，无效请求错误。

**常见原因：**

1. **服务器错误地写入标准输出：**
   * 调试输出转到 stdout 而不是 stderr
   * 额外的换行符或空格
   
   ```typescript
   // Wrong - pollutes stdout
   console.log("Debug info");
   
   // Correct - use stderr for debug
   console.error("Debug info");
   ```

1. **编码问题：**
   * 确保UTF-8编码
   * 无 BOM（字节顺序标记）

1. **消息帧：**
   * 每条消息必须是一个完整的JSON对象
   * 换行符分隔（每行一条消息）

## 平台特定问题

### 窗户

#### .NET 控制台应用程序/工具

```csharp
// Correct configuration for .NET exe
["my-dotnet-server"] = new McpStdioServerConfig
{
    Command = @"C:\Tools\MyServer\MyServer.exe",  // Full path with .exe
    Args = new List<string>(),
    WorkingDirectory = @"C:\Tools\MyServer",  // Set working directory
    Tools = new List<string> { "*" },
}

// For dotnet tool (DLL)
["my-dotnet-tool"] = new McpStdioServerConfig
{
    Command = "dotnet",
    Args = new List<string> { @"C:\Tools\MyTool\MyTool.dll" },
    WorkingDirectory = @"C:\Tools\MyTool",
    Tools = new List<string> { "*" },
}
```

#### npx命令

```csharp
// Windows needs cmd /c for npx
["filesystem"] = new McpStdioServerConfig
{
    Command = "cmd",
    Args = new List<string> { "/c", "npx", "-y", "@modelcontextprotocol/server-filesystem", "C:\\allowed\\path" },
    Tools = new List<string> { "*" },
}
```

#### 路径问题

* 使用原始字符串 (`@"C:\path"`) 或正斜杠 (`"C:/path"`)
* 尽可能避免路径中出现空格
* 如果需要空格，请确保正确引用

#### Antivirus/firewall

Windows Defender 或其他 AV 可能会阻止：
* 新的可执行文件
* 进程通过 stdin/stdout 进行通信

**解决方案：** 为您的 MCP 服务器可执行文件添加排除项。

### macOS

#### 网守封锁

```bash
# If the server is blocked
xattr -d com.apple.quarantine /path/to/mcp-server
```

#### 自制路径

```typescript
// GUI apps may not have /opt/homebrew in PATH
mcpServers: {
  "my-server": {
    command: "/opt/homebrew/bin/node",  // Full path
    args: ["/path/to/server.js"],
  },
}
```

### Linux

#### 权限问题

```bash
chmod +x /path/to/mcp-server
```

#### 缺少共享库

```bash
# Check dependencies
ldd /path/to/mcp-server

# Install missing libraries
apt install libfoo  # Debian/Ubuntu
yum install libfoo  # RHEL/CentOS
```

## 高级调试

### 捕获所有 MCP 流量

创建一个包装脚本来记录所有通信：

```bash
#!/bin/bash
# mcp-debug-wrapper.sh

LOG="./mcp-debug-$(date +%s).log"
ACTUAL_SERVER="$1"
shift

echo "=== MCP Debug Session ===" >> "$LOG"
echo "Server: $ACTUAL_SERVER" >> "$LOG"
echo "Args: $@" >> "$LOG"
echo "=========================" >> "$LOG"

# Tee stdin/stdout to log file
tee -a "$LOG" | "$ACTUAL_SERVER" "$@" 2>> "$LOG" | tee -a "$LOG"
```

使用它：

```typescript
mcpServers: {
  "debug-server": {
    command: "/path/to/mcp-debug-wrapper.sh",
    args: ["/actual/server/path", "arg1", "arg2"],
  },
}
```

### 与MCP检查员一起检查

使用官方MCP Inspector工具：

```bash
npx @modelcontextprotocol/inspector /path/to/your/mcp-server
```

这提供了一个网络 UI 来：
* 发送测试请求
* 查看回复
* 检查工具架构

### 协议版本不匹配

检查您的服务器支持 SDK 使用的协议版本：

```json
// In initialize response, check protocolVersion
{"result":{"protocolVersion":"2024-11-05",...}}
```

如果版本不匹配，请更新您的 MCP 服务器库。

## 调试清单

当提出问题或寻求帮助时，请收集：

* [ ] SDK 语言和版本
* [ ] CLI 版本 (`copilot --version`)
* [ ] MCP 服务器类型（Node.js、Python、.NET、Go、Rust 等）
* [ ] 完整的 MCP 服务器配置（编辑机密）
* [ ] 手动测试结果 `initialize`
* [ ] 手动测试结果 `tools/list`
* [ ] 来自 SDK 的调试日志
* [ ] 任何错误消息

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/mcp>) - 配置和设置
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - SDK-宽调试
* [MCP 规范](<https://modelcontextprotocol.io/>) - 官方协议文档


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## Table of contents

* [Quick Diagnostics](<#quick-diagnostics>)
* [Testing MCP Servers Independently](<#testing-mcp-servers-independently>)
* [Common Issues](<#common-issues>)
* [Platform-Specific Issues](<#platform-specific-issues>)
* [Advanced Debugging](<#advanced-debugging>)

## Quick diagnostics

### Checklist

Before diving deep, verify these basics:

* [ ] MCP server executable exists and is runnable
* [ ] Command path is correct (use absolute paths when in doubt)
* [ ] Tools are enabled (`tools: ["*"]` or specific tool names)
* [ ] Server implements MCP protocol correctly (responds to `initialize`)
* [ ] No firewall/antivirus blocking the process (Windows)

### Enable MCP debug logging

Add environment variables to your MCP server config:

```typescript
mcpServers: {
  "my-server": {
    type: "local",
    command: "/path/to/server",
    args: [],
    env: {
      MCP_DEBUG: "1",
      DEBUG: "*",
      NODE_DEBUG: "mcp",  // For Node.js MCP servers
    },
  },
}
```

## Testing MCP servers independently

Always test your MCP server outside the SDK first.

### Manual protocol test

Send an `initialize` request via stdin:

```bash
# Unix/macOS
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' | /path/to/your/mcp-server

# Windows (PowerShell)
'{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' | C:\path\to\your\mcp-server.exe
```

**Expected response:**

```json
{"jsonrpc":"2.0","id":1,"result":{"protocolVersion":"2024-11-05","capabilities":{"tools":{}},"serverInfo":{"name":"your-server","version":"1.0"}}}
```

### Test tool listing

After initialization, request the tools list:

```bash
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}' | /path/to/your/mcp-server
```

**Expected response:**

```json
{"jsonrpc":"2.0","id":2,"result":{"tools":[{"name":"my_tool","description":"Does something","inputSchema":{...}}]}}
```

### Interactive testing script

Create a test script to interactively debug your MCP server:

```bash
#!/bin/bash
# test-mcp.sh

SERVER="$1"

# Initialize
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'

# Send initialized notification
echo '{"jsonrpc":"2.0","method":"notifications/initialized"}'

# List tools
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}'

# Keep stdin open
cat
```

Usage:

```bash
./test-mcp.sh | /path/to/mcp-server
```

## Common issues

### Server not starting

**Symptoms:** No tools appear, no errors in logs.

**Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| Wrong command path | Use absolute path: `/usr/local/bin/server` |
| Missing executable permission | Run `chmod +x /path/to/server` |
| Missing dependencies | Check with `ldd` (Linux) or run manually |
| Working directory issues | Set `cwd` in config |

**Debug by running manually:**

```bash
# Run exactly what the SDK would run
cd /expected/working/dir
/path/to/command arg1 arg2
```

### Server starts but tools don't appear

**Symptoms:** Server process runs but no tools are available.

**Causes & Solutions:**

1. **Tools not enabled in config:**

   ```typescript
   mcpServers: {
     "server": {
       // ...
       tools: ["*"],  // Must be "*" or list of tool names
     },
   }
   ```

1. **Server doesn't expose tools:**
   * Test with `tools/list` request manually
   * Check server implements `tools/list` method

1. **Initialization handshake fails:**
   * Server must respond to `initialize` correctly
   * Server must handle `notifications/initialized`

### Tools listed but never called

**Symptoms:** Tools appear in debug logs but model doesn't use them.

**Causes & Solutions:**

1. **Prompt doesn't clearly need the tool:**

   ```typescript
   // Too vague
   await session.sendAndWait({ prompt: "What's the weather?" });
   
   // Better - explicitly mentions capability
   await session.sendAndWait({ 
     prompt: "Use the weather tool to get the current temperature in Seattle" 
   });
   ```

1. **Tool description unclear:**

   ```typescript
   // Bad - model doesn't know when to use it
   { name: "do_thing", description: "Does a thing" }
   
   // Good - clear purpose
   { name: "get_weather", description: "Get current weather conditions for a city. Returns temperature, humidity, and conditions." }
   ```

1. **Tool schema issues:**
   * Ensure `inputSchema` is valid JSON Schema
   * Required fields must be in `required` array

### Timeout errors

**Symptoms:** `MCP tool call timed out` errors.

**Solutions:**

1. **Increase timeout:**

   ```typescript
   mcpServers: {
     "slow-server": {
       // ...
       timeout: 300000,  // 5 minutes
     },
   }
   ```

1. **Optimize server performance:**
   * Add progress logging to identify bottleneck
   * Consider async operations
   * Check for blocking I/O

1. **For long-running tools**, consider streaming responses if supported.

### JSON-RPC errors

**Symptoms:** Parse errors, invalid request errors.

**Common causes:**

1. **Server writes to stdout incorrectly:**
   * Debug output going to stdout instead of stderr
   * Extra newlines or whitespace
   
   ```typescript
   // Wrong - pollutes stdout
   console.log("Debug info");
   
   // Correct - use stderr for debug
   console.error("Debug info");
   ```

1. **Encoding issues:**
   * Ensure UTF-8 encoding
   * No BOM (Byte Order Mark)

1. **Message framing:**
   * Each message must be a complete JSON object
   * Newline-delimited (one message per line)

## Platform-specific issues

### Windows

#### .NET console apps / tools

```csharp
// Correct configuration for .NET exe
["my-dotnet-server"] = new McpStdioServerConfig
{
    Command = @"C:\Tools\MyServer\MyServer.exe",  // Full path with .exe
    Args = new List<string>(),
    WorkingDirectory = @"C:\Tools\MyServer",  // Set working directory
    Tools = new List<string> { "*" },
}

// For dotnet tool (DLL)
["my-dotnet-tool"] = new McpStdioServerConfig
{
    Command = "dotnet",
    Args = new List<string> { @"C:\Tools\MyTool\MyTool.dll" },
    WorkingDirectory = @"C:\Tools\MyTool",
    Tools = new List<string> { "*" },
}
```

#### npx commands

```csharp
// Windows needs cmd /c for npx
["filesystem"] = new McpStdioServerConfig
{
    Command = "cmd",
    Args = new List<string> { "/c", "npx", "-y", "@modelcontextprotocol/server-filesystem", "C:\\allowed\\path" },
    Tools = new List<string> { "*" },
}
```

#### Path issues

* Use raw strings (`@"C:\path"`) or forward slashes (`"C:/path"`)
* Avoid spaces in paths when possible
* If spaces required, ensure proper quoting

#### Antivirus/firewall

Windows Defender or other AV may block:
* New executables
* Processes communicating via stdin/stdout

**Solution:** Add exclusions for your MCP server executable.

### macOS

#### Gatekeeper blocking

```bash
# If the server is blocked
xattr -d com.apple.quarantine /path/to/mcp-server
```

#### Homebrew paths

```typescript
// GUI apps may not have /opt/homebrew in PATH
mcpServers: {
  "my-server": {
    command: "/opt/homebrew/bin/node",  // Full path
    args: ["/path/to/server.js"],
  },
}
```

### Linux

#### Permission issues

```bash
chmod +x /path/to/mcp-server
```

#### Missing shared libraries

```bash
# Check dependencies
ldd /path/to/mcp-server

# Install missing libraries
apt install libfoo  # Debian/Ubuntu
yum install libfoo  # RHEL/CentOS
```

## Advanced debugging

### Capture all MCP traffic

Create a wrapper script to log all communication:

```bash
#!/bin/bash
# mcp-debug-wrapper.sh

LOG="./mcp-debug-$(date +%s).log"
ACTUAL_SERVER="$1"
shift

echo "=== MCP Debug Session ===" >> "$LOG"
echo "Server: $ACTUAL_SERVER" >> "$LOG"
echo "Args: $@" >> "$LOG"
echo "=========================" >> "$LOG"

# Tee stdin/stdout to log file
tee -a "$LOG" | "$ACTUAL_SERVER" "$@" 2>> "$LOG" | tee -a "$LOG"
```

Use it:

```typescript
mcpServers: {
  "debug-server": {
    command: "/path/to/mcp-debug-wrapper.sh",
    args: ["/actual/server/path", "arg1", "arg2"],
  },
}
```

### Inspect with MCP inspector

Use the official MCP Inspector tool:

```bash
npx @modelcontextprotocol/inspector /path/to/your/mcp-server
```

This provides a web UI to:
* Send test requests
* View responses
* Inspect tool schemas

### Protocol version mismatches

Check your server supports the protocol version the SDK uses:

```json
// In initialize response, check protocolVersion
{"result":{"protocolVersion":"2024-11-05",...}}
```

If versions don't match, update your MCP server library.

## Debugging checklist

When opening an issue or asking for help, collect:

* [ ] SDK language and version
* [ ] CLI version (`copilot --version`)
* [ ] MCP server type (Node.js, Python, .NET, Go, Rust, and more)
* [ ] Full MCP server configuration (redact secrets)
* [ ] Result of manual `initialize` test
* [ ] Result of manual `tools/list` test
* [ ] Debug logs from SDK
* [ ] Any error messages

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/mcp>) - Configuration and setup
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>) - SDK-wide debugging
* [MCP Specification](<https://modelcontextprotocol.io/>) - Official protocol docs


</details>
