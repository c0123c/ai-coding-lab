# Qwen Code TypeScript SDK 指南

说明程序调用、消息、超时、会话恢复、权限和运行控制，并提供多轮示例；正文标记接口为实验性。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-28](<https://github.com/QwenLM/qwen-code/commit/e39567eb564cf5473dc703b25b219c66059fcb1b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/sdk-typescript.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/sdk-typescript.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 打字稿 SDK

## @qwen-code/sdk

用于以编程方式访问 Qwen Code 的最低实验性 TypeScript SDK。

欢迎提交功能 request/issue/PR.

## 安装

```bash
npm install @qwen-code/sdk
```

## 要求

- Node.js >= 22.0.0
- [Qwen Code](<https://github.com/QwenLM/qwen-code>) >= 0.4.0（稳定）。 SDK默认使用其捆绑的CLI；仅当您需要运行自定义 `qwen` 二进制文件或 CLI 捆绑包时才设置 `pathToQwenExecutable`。

## 快速入门

```typescript
import { query } from '@qwen-code/sdk';

// Single-turn query
const result = query({
  prompt: 'What files are in the current directory?',
  options: {
    cwd: '/path/to/project',
  },
});

// Iterate over messages
for await (const message of result) {
  if (message.type === 'assistant') {
    console.log('Assistant:', message.message.content);
  } else if (message.type === 'result') {
    console.log('Result:', message.result);
  }
}
```

## API 参考

### `query(config)`

使用 Qwen Code 创建一个新的查询会话。

#### 参数

- `prompt`：`string | AsyncIterable<SDKUserMessage>` - 发送提示。使用字符串进行单轮查询，或使用异步迭代进行多轮对话。
- `options`：`QueryOptions` - 查询会话的配置选项。

#### QueryOptions

|选项|类型|默认|描述|
| ------------------------ | -------------------------------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd`                    | `string`                                                             | `process.cwd()`  |查询会话的工作目录。确定执行文件操作和命令的上下文。|
| `model`                  | `string`                                                             | -                |要使用的 AI 型号（例如，`'qwen-max'`、`'qwen-plus'`、`'qwen-turbo'`）。优先于 `OPENAI_MODEL` 和 `QWEN_MODEL` 环境变量。|
| `pathToQwenExecutable`   | `string`                                                             |捆绑 CLI|Qwen Code 可执行文件的路径。支持多种格式：`'qwen'`（来自PATH的本机二进制文件），`'/path/to/qwen'`（显式路径），`'/path/to/cli.js'`（Node.js捆绑），`'node:/path/to/cli.js'`（强制Node.js运行时）， `'bun:/path/to/cli.js'`（强制Bun运行时）。如果未提供，SDK将使用包装中附带的CLI。|
| `permissionMode`         | `'default' \| 'plan' \| 'auto-edit' \| 'auto' \| 'yolo'`             | `'default'`      |控制工具执行审批的权限模式。详情请参见[权限模式](<#权限模式>)。|
| `canUseTool`             | `CanUseTool`                                                         | -                |用于工具执行批准的自定义权限处理程序。当工具需要确认时调用。必须在 60 秒内响应，否则请求将被自动拒绝。请参阅[自定义权限处理程序](<#自定义权限处理程序>)。|
| `env`                    | `Record<string, string>`                                             | -                |要传递给 Qwen Code 进程的环境变量。与当前流程环境合并。|
| `systemPrompt`           | `string \| QuerySystemPromptPreset`                                  | -                |主会话的系统提示配置。使用字符串完全覆盖内置的 Qwen Code 系统提示符，或使用预设对象来保留内置提示符并附加额外的指令。|
| `mcpServers`             | `Record<string, McpServerConfig>`                                    | -                |要连接的 MCP (Model Context Protocol) 服务器。支持外部服务器（stdio/SSE/HTTP）和SDK-嵌入式服务器。外部服务器配置有传输选项，例如 `command`、`args`、`url`、`httpUrl` 等。SDK 服务器使用 `{ type: 'sdk', name: string, instance: Server }`。|
| `abortController`        | `AbortController`                                                    | -                |控制器取消查询会话。调用`abortController.abort()`终止会话并清理资源。|
| `debug`                  | `boolean`                                                            | `false`          |启用调试模式以从 CLI 进程进行详细日志记录。|
| `maxSessionTurns`        | `number`                                                             |`-1`（无限制）|会话自动终止之前的最大对话轮数。必须是整数。一个回合由用户消息和助理响应组成。|
| `coreTools`              | `string[]`                                                           | -                |使用旧版 `coreTools` / CLI `--core-tools` 白名单语义。如果指定，则仅为会话注册匹配的核心工具。这是唯一限制内置工具注册的白名单样式选项；整体工具 `permissions.deny` / `excludeTools` 规则（以及 settings.json 中的 `tools.disabled`）也会从注册表中删除工具。 settings.json 中的 `permissions.allow` 是纯粹的自动批准，绝不会删除、降级或隐藏工具 (#10075)。要将工具的架构排除在初始模型请求之外，请在 settings.json 中使用 `tools.eager`（需要重新启动，#9827） - `tool_search`、`tool_call`、`structured_output`、计划模式生命周期工具、 `task_stop`、`mcp__*` 和 `computer_use__*` 工具不受该允许列表的约束并保持正常加载；以这种方式降级的工具保持注册并可通过 `tool_search` + `tool_call` 访问，同时两个桥接工具均已注册 - 当任一桥接工具未注册时（`tools.toolSearch.enabled: false` 拒绝两者；`tool_search` 或 `tool_call` 拒绝规则，或 `tools.disabled` 条目删除一个）降级的工具保持隐藏的工具不会提供给模型，并且无法通过该会话的桥访问，并且警告会写入 CLI 进程的 stderr（SDK 仅使用管道 stderr 和有效的 `debug` 日志记录来转发它；显式的 `logLevel` 总是获胜）；他们保持注册状态，因此以自己的名字进行的直接调用仍然会正常评估和批准 - 除了在`tools.visible`中列出的工具（这些工具是预先声明的）以及其实时历史记录包含对仍然隐藏的降级工具的直接调用的会话（任何工具集刷新（恢复，MCP发现，会话中的第一个计划模式条目，子代理定义更改）重新声明）。要完全删除工具，请使用整体工具 `excludeTools` / `permissions.deny` 规则 — 带有说明符的规则（例如 `'Bash(rm *)'`）仅拒绝运行时匹配的调用。 MCP 工具免于基于拒绝的删除：使用每服务器 `excludeTools` / `tools.disabled` 过滤器隐藏它们（拒绝仍然在运行时阻止它们的调用）。示例：`['read_file', 'edit', 'run_shell_command']`。|
| `excludeTools`           | `string[]`                                                           | -                |相当于settings.json中的`permissions.deny`。排除的工具立即返回权限错误。比所有其他权限设置具有最高优先级。支持工具名称别名和模式匹配：工具名称（`'write_file'`）、shell 命令前缀（`'Bash(rm *)'`）或路径模式（`'Read(.env)'`、`'Edit(/src/**)'`）。|
| `allowedTools`           | `string[]`                                                           | -                |相当于自动批准的settings.json中的`permissions.allow`。匹配工具绕过`canUseTool`回调并自动执行。仅当工具需要确认时适用。与 `permissions.allow` 一样，这是纯粹的自动批准，永远不会影响注册哪些工具或发送哪些模式 (#10075)。支持与`excludeTools`相同的模式匹配。示例：`['Bash(git status)', 'Bash(npm test)']`。|
| `authType`               | `'openai' \| 'anthropic' \| 'qwen-oauth' \| 'gemini' \| 'vertex-ai'` | -                |AI 服务的身份验证类型。当提供时，SDK将其转发到CLI作为`--auth-type`。|
| `agents`                 | `SubagentConfig[]`                                                   | -                |可在会话期间调用的子代理的配置。子代理专门用于特定任务或域的 AI agents。|
| `includePartialMessages` | `boolean`                                                            | `false`          |当 `true` 时，SDK 在生成时发出不完整的消息，从而允许实时流式传输 AI 的响应。|
| `resume`                 | `string`                                                             | -                |通过提供会话 ID 来恢复先前的会话。相当于CLI的`--resume`标志。|
| `sessionId`              | `string`                                                             | -                |为新会话指定会话 ID。确保SDK和CLI使用相同的ID而不恢复历史记录。相当于CLI的`--session-id`标志。|

> [！注意]
> 对于 `coreTools`，`Read`、`Edit` 和 `Bash` 等别名也可以使用，但 `Bash(git *)` 等调用说明符将被删除。 `coreTools` 限制工具注册，而不是调用模式。

### 超时

SDK 强制执行以下默认超时：

|超时|默认|描述|
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `canUseTool`     |1 分钟|`canUseTool`回调响应的最长时间。如果超出，工具请求将被自动拒绝。|
| `mcpRequest`     |1 分钟|SDK MCP 工具调用完成的最长时间。|
| `controlRequest` |1 分钟|`initialize()`、`setModel()`、`setPermissionMode()`、`getContextUsage()` 和 `interrupt()` 等控制操作完成的最长时间。|
| `streamClose`    |1 分钟|在使用 SDK MCP 服务器的多轮模式下关闭 CLI stdin 之前等待初始化完成的最长时间。|

您可以通过 `timeout` 选项自定义这些超时：

```typescript
import { query } from '@qwen-code/sdk';

const q = query({
  prompt: 'Your prompt',
  options: {
    timeout: {
      canUseTool: 60000, // 60 seconds for permission callback
      mcpRequest: 600000, // 10 minutes for MCP tool calls
      controlRequest: 60000, // 60 seconds for control requests
      streamClose: 15000, // 15 seconds for stream close wait
    },
  },
});
```

### 消息类型

SDK提供类型保护来识别不同的消息类型：

```typescript
import {
  isSDKUserMessage,
  isSDKAssistantMessage,
  isSDKSystemMessage,
  isSDKResultMessage,
  isSDKPartialAssistantMessage,
} from '@qwen-code/sdk';

for await (const message of result) {
  if (isSDKAssistantMessage(message)) {
    // Handle assistant message
  } else if (isSDKResultMessage(message)) {
    // Handle result message
  }
}
```

### 查询实例方法

`query()`返回的`Query`实例提供了几种方法：

```typescript
const q = query({ prompt: 'Hello', options: {} });

// Get session ID
const sessionId = q.getSessionId();

// Check if closed
const closed = q.isClosed();

// Interrupt the current operation
await q.interrupt();

// Change permission mode mid-session
await q.setPermissionMode('yolo');

// Change model mid-session
await q.setModel('qwen-max');

// Get context window usage breakdown (token counts per category)
const usage = await q.getContextUsage();
// Pass true to hint that per-item details should be displayed
const detail = await q.getContextUsage(true);

// Close the session
await q.close();
```

`interrupt()` 仅取消当前回合。对于使用
异步可迭代提示创建的多轮查询，查询及其输入流保持打开状态，因此稍后来自可迭代的
消息会正常处理。当您想要结束整个会话时，请使用`close()`或中止
配置的`AbortController`。

## 守护程序调用者提供的会话 ID

`DaemonClient.createOrAttachSession` 接受可选的 `sessionId`，调用者必须在创建会话之前保留身份：

```typescript
import { DaemonClient } from '@qwen-code/sdk';

const daemon = new DaemonClient({ baseUrl: 'http://127.0.0.1:4170' });
const session = await daemon.createOrAttachSession({
  workspaceCwd: '/path/to/project',
  sessionId: '550E8400-E29B-41D4-A716-446655440000',
});

console.log(session.sessionId); // 550e8400-e29b-41d4-a716-446655440000
```

SDK 在发送突变之前需要守护进程的 `session_id_override` 能力。 REST模式直接序列化`sessionId`；活动的 ACP 适配器将其映射到 `session/new._meta["qwen-code/sessionId"]`。 SDK 验证成功响应，如果守护程序返回不同的 ID，则抛出 `DaemonSessionIdProtocolError`。

此选项始终创建新的线程会话，并且不是幂等附加。如果创建结果不明确，请使用已知的 ID 进行加载或恢复。省略该选项将保留现有的创建或附加行为。

## Daemon启动模型及推理选择

`DaemonClient.createOrAttachSession` 和 `createStandaloneSession` 在创建之前接受 `startupConfig: { modelServiceId, reasoningEffort? }` 和预检 `session_startup_config`。型号为必填项；省略仅模型选择的推理，包括没有推理控制的模型。根据模型验证显式工作。 SDK 检查 `modelApplied: true` 和规范的 `startupConfigApplied` 响应；仅模型确认省略了推理字段。

启动选择不会保存共享默认值或施加生命周期引脚。明确选择拒绝创建失败；不确定的独立创建保留其现有的恢复结果。启动确认缺失或不匹配的成功响应将被直接拒绝，并且不会触发独立recovery/adoption.请参阅[协议](<Qwen Code 守护进程 HTTP 协议参考.md#能力>)。

## 谈论正在运行的会话

`@qwen-code/sdk/peer` 允许非 Qwen Code 会话的程序加入在同一台计算机上以同一用户身份运行的 
 会话 — 语音前端、
 中继、构建观察程序。该程序显示在 `qwen sessions ps` 中，以及参与跨会话消息传递的每个会话的 
`list_agents` 中 - 它的 
`agents.crossSessionMessaging` 设置已打开，并且它不是以 
`--bare` 或 `--safe-mode` 启动 - 这也是让这些会话通过 
 向其发送消息的原因名称为 `send_message`。它可以给他们回复消息。它仅在 Node 上运行，
除了 Node 本身之外不需要任何东西。

```typescript
import { PeerEndpoint } from '@qwen-code/sdk/peer';

const endpoint = await PeerEndpoint.start({
  name: 'voice-bridge',
  onMessage: (message) =>
    console.log(`${message.fromName}: ${message.content}`),
});

const [session] = await endpoint.list();
if (session) {
  const sent = await endpoint.send({
    to: session.address,
    content: 'What are you working on?',
  });
  if (sent.kind === 'sent') {
    const receipt = await endpoint.awaitReceipt(sent.msgId, { final: true });
    console.log(receipt?.status); // delivered, denied, refused, ...
  }
}

await endpoint.close();
```

类似的消息将保留以供会话的用户查看。要在没有审查的情况下直接进行 
 会话，请使用 
`qwen sessions controllers add --label voice-bridge` 创建一个控制器令牌，将其提供给端点 
 并标记应呈现它的发送：

```typescript
const endpoint = await PeerEndpoint.start({
  name: 'voice-bridge',
  controllerToken: process.env['QWEN_CONTROLLER_TOKEN'],
});
await endpoint.send({
  to: 'my-app-3f',
  content: 'run the tests',
  controller: true,
});
```

要知道的事情：

- 会话仅在其 `agents.crossSessionMessaging` 设置时才具有收件箱
是打开的，默认情况下是打开的，并且它不是以 `--bare` 或 
 `--safe-mode` 启动 - 无论设置如何，这些都会关闭消息传递。将其关闭的会话
不会出现在`list()`中，并且其自己的`list_agents`和
`send_message`也无法看到或访问该程序。 `qwen sessions ps`
 无论如何都会列出该程序。
- 在两种情况下，消息未经审核就被传递：
提供一个控制器令牌 (`controller: true`)，或其 `fromMode` 命名 
 接收会话自己的审阅类。 `fromMode` 是一个没有
 验证的声明，因此不是编码会话的程序应该将其
 排除在外。记录中没有任何内容——不是`kind`，不是`name`——购买交货。 
 接收会话的 `agents.crossSessionInbound` 设置优于以下两者：
 `hold` 或 `refuse` 赢得控制器令牌。
- 仅标记旨在引导会话的发送。地址解析自
记录您可以编写的任何正在运行的程序，因此控制器将
令牌发送到响应该地址的任何进程的记录。发送到另一个对等端点的 
 控制器会在未读状态下被丢弃，因为 
 端点的收件箱仅接受其自己的令牌。
- 端点的收件箱不应用 Qwen Code 会话的任何保护
适用于它自己的：没有速率限制，没有保留，并且没有超出
它回复的最后200消息的重复窗口。每条消息都会在到达时得到答复 `delivered` 和 
 并交给 `onMessage`，因此，如果您 
 需要它们，请在那里应用您自己的限制。如果没有`onMessage`，每条消息都会得到回复`refused`。
- 在退出之前调用 `close()`，包括从您自己的信号处理程序中调用。一个
未关闭而被杀死的进程会留下其记录，直到 Qwen Code
 会话列出目录并看到该进程消失。
- 仅 UNIX 域套接字：尚不支持 Windows。

记录模式、传输格式和接收状态记录在
[跨会话协议](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/cross-session-protocol.md>)中。

## 权限模式

SDK支持不同的权限模式来控制工具执行：

- **`default`**：除非通过`canUseTool`回调或在`allowedTools`中批准，否则写入工具将被拒绝。只读工具无需确认即可执行。
- **`plan`**：阻止所有写入工具，指示AI先提出计划。
- **`auto-edit`**：自动批准编辑工具（`edit`、`write_file`、`notebook_edit`），而其他工具需要确认。
- **`auto`**：使用内置分类器自动批准安全工具调用并阻止有风险的工具调用，并在重复策略阻止或分类器中断后手动批准回退。
- **`yolo`**：所有工具自动执行，无需确认。

### 权限优先级链

决策优先级（从高到低）：`deny` > `ask` > `allow` > _（default/interactive模式）_

第一个匹配的规则获胜。

1. `excludeTools` / `permissions.deny` - 完全阻止工具（返回权限错误）
2. `permissions.ask` - 始终需要用户确认
3. `permissionMode: 'plan'` - 阻止所有非只读工具
4. `permissionMode: 'yolo'` - 自动批准所有工具
5. `allowedTools` / `permissions.allow` - 自动批准匹配工具
6. `permissionMode: 'auto'` - 剩余工具的分类器介导批准
7. `canUseTool` 回调 - 自定义批准逻辑（如果提供，则不会为允许的工具调用）
8. 默认行为 - SDK 模式下自动拒绝（写入工具需要明确批准）

## 示例

### 多轮对话

```typescript
import { query, type SDKUserMessage } from '@qwen-code/sdk';

async function* generateMessages(): AsyncIterable<SDKUserMessage> {
  yield {
    type: 'user',
    session_id: 'my-session',
    message: { role: 'user', content: 'Create a hello.txt file' },
    parent_tool_use_id: null,
  };

  // Wait for some condition or user input
  yield {
    type: 'user',
    session_id: 'my-session',
    message: { role: 'user', content: 'Now read the file back' },
    parent_tool_use_id: null,
  };
}

const result = query({
  prompt: generateMessages(),
  options: {
    permissionMode: 'auto-edit',
  },
});

for await (const message of result) {
  console.log(message);
}
```

### 自定义权限处理程序

```typescript
import { query, type CanUseTool } from '@qwen-code/sdk';

const canUseTool: CanUseTool = async (toolName, input, { signal }) => {
  // Allow all read operations
  if (toolName.startsWith('read_')) {
    return { behavior: 'allow', updatedInput: input };
  }

  // Prompt user for write operations (in a real app)
  const userApproved = await promptUser(`Allow ${toolName}?`);

  if (userApproved) {
    return { behavior: 'allow', updatedInput: input };
  }

  return { behavior: 'deny', message: 'User denied the operation' };
};

const result = query({
  prompt: 'Create a new file',
  options: {
    canUseTool,
  },
});
```

### 使用外部 MCP 服务器

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Use the custom tool from my MCP server',
  options: {
    mcpServers: {
      'my-server': {
        command: 'node',
        args: ['path/to/mcp-server.js'],
        env: { PORT: '3000' },
      },
    },
  },
});
```

### 覆盖系统提示

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Say hello in one sentence.',
  options: {
    systemPrompt: 'You are a terse assistant. Answer in exactly one sentence.',
  },
});
```

### 附加到内置系统提示符

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Review the current directory.',
  options: {
    systemPrompt: {
      type: 'preset',
      preset: 'qwen_code',
      append: 'Be terse and focus on concrete findings.',
    },
  },
});
```

### 使用 SDK- 嵌入式 MCP 服务器

SDK 提供 `tool` 和 `createSdkMcpServer` 来创建与 SDK 应用程序在同一进程中运行的 MCP 服务器。当您想要向 AI 公开自定义工具而不运行单独的服务器进程时，这非常有用。

#### `tool(name, description, inputSchema, handler)`

使用 Zod 架构类型推断创建工具定义。

|参数|类型|描述|
| ------------- | ---------------------------------- | ------------------------------------------------------------------------ |
| `name`        | `string`                           |工具名称（1-64字符，以字母、字母数字和下划线开头）|
| `description` | `string`                           |该工具功能的人类可读描述|
| `inputSchema` | `ZodRawShape`                      |Zod 架构对象定义工具的输入参数|
| `handler`     | `(args, extra) => Promise<Result>` |执行该工具并返回 MCP 内容块的异步函数|

处理程序必须返回具有以下结构的 `CallToolResult` 对象：

```typescript
{
  content: Array<
    | { type: 'text'; text: string }
    | { type: 'image'; data: string; mimeType: string }
    | { type: 'resource'; uri: string; mimeType?: string; text?: string }
  >;
  isError?: boolean;
}
```

#### `createSdkMcpServer(options)`

创建一个 SDK- 嵌入式 MCP 服务器实例。

|选项|类型|默认|描述|
| --------- | ------------------------ | --------- | ------------------------------------ |
| `name`    | `string`                 |必填|MCP 服务器的唯一名称|
| `version` | `string`                 | `'1.0.0'` |服务器版本|
| `tools`   | `SdkMcpToolDefinition[]` | -         |使用 `tool()` 创建的工具数组|

返回一个 `McpSdkServerConfigWithInstance` 对象，可以直接传递给 `mcpServers` 选项。

#### 示例

```typescript
import { z } from 'zod';
import { query, tool, createSdkMcpServer } from '@qwen-code/sdk';

// Define a tool with Zod schema
const calculatorTool = tool(
  'calculate_sum',
  'Add two numbers',
  { a: z.number(), b: z.number() },
  async (args) => ({
    content: [{ type: 'text', text: String(args.a + args.b) }],
  }),
);

// Create the MCP server
const server = createSdkMcpServer({
  name: 'calculator',
  tools: [calculatorTool],
});

// Use the server in a query
const result = query({
  prompt: 'What is 42 + 17?',
  options: {
    permissionMode: 'yolo',
    mcpServers: {
      calculator: server,
    },
  },
});

for await (const message of result) {
  console.log(message);
}
```

### 中止查询

```typescript
import { query, isAbortError } from '@qwen-code/sdk';

const abortController = new AbortController();

const result = query({
  prompt: 'Long running task...',
  options: {
    abortController,
  },
});

// Abort after 5 seconds
setTimeout(() => abortController.abort(), 5000);

try {
  for await (const message of result) {
    console.log(message);
  }
} catch (error) {
  if (isAbortError(error)) {
    console.log('Query was aborted');
  } else {
    throw error;
  }
}
```

## 错误处理

SDK 提供了一个 `AbortError` 类来处理中止的查询：

```typescript
import { AbortError, isAbortError } from '@qwen-code/sdk';

try {
  // ... query operations
} catch (error) {
  if (isAbortError(error)) {
    // Handle abort
  } else {
    // Handle other errors
  }
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Typescript SDK

## @qwen-code/sdk

A minimum experimental TypeScript SDK for programmatic access to Qwen Code.

Feel free to submit a feature request/issue/PR.

## Installation

```bash
npm install @qwen-code/sdk
```

## Requirements

- Node.js >= 22.0.0
- [Qwen Code](<https://github.com/QwenLM/qwen-code>) >= 0.4.0 (stable). The SDK uses its bundled CLI by default; set `pathToQwenExecutable` only when you need to run a custom `qwen` binary or CLI bundle.

## Quick Start

```typescript
import { query } from '@qwen-code/sdk';

// Single-turn query
const result = query({
  prompt: 'What files are in the current directory?',
  options: {
    cwd: '/path/to/project',
  },
});

// Iterate over messages
for await (const message of result) {
  if (message.type === 'assistant') {
    console.log('Assistant:', message.message.content);
  } else if (message.type === 'result') {
    console.log('Result:', message.result);
  }
}
```

## API Reference

### `query(config)`

Creates a new query session with the Qwen Code.

#### Parameters

- `prompt`: `string | AsyncIterable<SDKUserMessage>` - The prompt to send. Use a string for single-turn queries or an async iterable for multi-turn conversations.
- `options`: `QueryOptions` - Configuration options for the query session.

#### QueryOptions

| Option                   | Type                                                                 | Default          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------ | -------------------------------------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cwd`                    | `string`                                                             | `process.cwd()`  | The working directory for the query session. Determines the context in which file operations and commands are executed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `model`                  | `string`                                                             | -                | The AI model to use (e.g., `'qwen-max'`, `'qwen-plus'`, `'qwen-turbo'`). Takes precedence over `OPENAI_MODEL` and `QWEN_MODEL` environment variables.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `pathToQwenExecutable`   | `string`                                                             | Bundled CLI      | Path to the Qwen Code executable. Supports multiple formats: `'qwen'` (native binary from PATH), `'/path/to/qwen'` (explicit path), `'/path/to/cli.js'` (Node.js bundle), `'node:/path/to/cli.js'` (force Node.js runtime), `'bun:/path/to/cli.js'` (force Bun runtime). If not provided, the SDK uses the bundled CLI included with the package.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `permissionMode`         | `'default' \| 'plan' \| 'auto-edit' \| 'auto' \| 'yolo'`             | `'default'`      | Permission mode controlling tool execution approval. See [Permission Modes](<#permission-modes>) for details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `canUseTool`             | `CanUseTool`                                                         | -                | Custom permission handler for tool execution approval. Invoked when a tool requires confirmation. Must respond within 60 seconds or the request will be auto-denied. See [Custom Permission Handler](<#custom-permission-handler>).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `env`                    | `Record<string, string>`                                             | -                | Environment variables to pass to the Qwen Code process. Merged with the current process environment.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `systemPrompt`           | `string \| QuerySystemPromptPreset`                                  | -                | System prompt configuration for the main session. Use a string to fully override the built-in Qwen Code system prompt, or a preset object to keep the built-in prompt and append extra instructions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `mcpServers`             | `Record<string, McpServerConfig>`                                    | -                | MCP (Model Context Protocol) servers to connect. Supports external servers (stdio/SSE/HTTP) and SDK-embedded servers. External servers are configured with transport options like `command`, `args`, `url`, `httpUrl`, etc. SDK servers use `{ type: 'sdk', name: string, instance: Server }`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `abortController`        | `AbortController`                                                    | -                | Controller to cancel the query session. Call `abortController.abort()` to terminate the session and cleanup resources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `debug`                  | `boolean`                                                            | `false`          | Enable debug mode for verbose logging from the CLI process.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `maxSessionTurns`        | `number`                                                             | `-1` (unlimited) | Maximum number of conversation turns before the session automatically terminates. Must be an integer. A turn consists of a user message and an assistant response.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| `coreTools`              | `string[]`                                                           | -                | Uses the legacy `coreTools` / CLI `--core-tools` allowlist semantics. If specified, only matching core tools are registered for the session. This is the only allowlist-style option that restricts built-in tool registration; a whole-tool `permissions.deny` / `excludeTools` rule (and `tools.disabled` in settings.json) also removes a tool from the registry. `permissions.allow` in settings.json is pure auto-approval and never removes, demotes, or hides a tool (#10075). To keep a tool's schema out of the initial model request, use `tools.eager` in settings.json (requires restart, #9827) — `tool_search`, `tool_call`, `structured_output`, plan-mode lifecycle tools, `task_stop`, `mcp__*` and `computer_use__*` tools are exempt from that allowlist and keep their normal loading; tools demoted this way stay registered and reachable through `tool_search` + `tool_call` while both bridge tools are registered — when either is unregistered (`tools.toolSearch.enabled: false` denies both; a `tool_search` or `tool_call` deny rule, or a `tools.disabled` entry removes one) the demoted tools that remain hidden are not offered to the model and cannot be reached through the bridge for that session, and a warning is written to the CLI process's stderr (SDK forwards it only with piped stderr and effective `debug` logging; an explicit `logLevel` always wins); they stay registered, so a direct call by their own name is still evaluated and approved normally — except tools also listed in `tools.visible`, which are declared upfront, and sessions whose live history contains a direct call to a still-hidden demoted tool, which any tool-set refresh (resume, MCP discovery, the first plan-mode entry in a session, a subagent definition change) re-declares.; to remove a tool entirely, use a whole-tool `excludeTools` / `permissions.deny` rule — a rule with a specifier (such as `'Bash(rm *)'`) only denies matching invocations at runtime. MCP tools are exempt from deny-based removal: hide them with the per-server `excludeTools` / `tools.disabled` filters instead (deny still blocks their calls at runtime). Example: `['read_file', 'edit', 'run_shell_command']`. |
| `excludeTools`           | `string[]`                                                           | -                | Equivalent to `permissions.deny` in settings.json. Excluded tools return a permission error immediately. Takes highest priority over all other permission settings. Supports tool name aliases and pattern matching: tool name (`'write_file'`), shell command prefix (`'Bash(rm *)'`), or path patterns (`'Read(.env)'`, `'Edit(/src/**)'`).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `allowedTools`           | `string[]`                                                           | -                | Equivalent to `permissions.allow` in settings.json for auto-approval. Matching tools bypass `canUseTool` callback and execute automatically. Only applies when tool requires confirmation. Like `permissions.allow`, this is pure auto-approval and never affects which tools are registered or which schemas are sent (#10075). Supports same pattern matching as `excludeTools`. Example: `['Bash(git status)', 'Bash(npm test)']`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `authType`               | `'openai' \| 'anthropic' \| 'qwen-oauth' \| 'gemini' \| 'vertex-ai'` | -                | Authentication type for the AI service. When provided, the SDK forwards it to the CLI as `--auth-type`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `agents`                 | `SubagentConfig[]`                                                   | -                | Configuration for subagents that can be invoked during the session. Subagents are specialized AI agents for specific tasks or domains.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `includePartialMessages` | `boolean`                                                            | `false`          | When `true`, the SDK emits incomplete messages as they are being generated, allowing real-time streaming of the AI's response.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| `resume`                 | `string`                                                             | -                | Resume a previous session by providing its session ID. Equivalent to CLI's `--resume` flag.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `sessionId`              | `string`                                                             | -                | Specify a session ID for the new session. Ensures SDK and CLI use the same ID without resuming history. Equivalent to CLI's `--session-id` flag.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |

> [!note]
> For `coreTools`, aliases like `Read`, `Edit`, and `Bash` also work, but invocation specifiers such as `Bash(git *)` are stripped. `coreTools` restricts tool registration, not invocation patterns.

### Timeouts

The SDK enforces the following default timeouts:

| Timeout          | Default  | Description                                                                                                                                       |
| ---------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `canUseTool`     | 1 minute | Maximum time for `canUseTool` callback to respond. If exceeded, the tool request is auto-denied.                                                  |
| `mcpRequest`     | 1 minute | Maximum time for SDK MCP tool calls to complete.                                                                                                  |
| `controlRequest` | 1 minute | Maximum time for control operations like `initialize()`, `setModel()`, `setPermissionMode()`, `getContextUsage()`, and `interrupt()` to complete. |
| `streamClose`    | 1 minute | Maximum time to wait for initialization to complete before closing CLI stdin in multi-turn mode with SDK MCP servers.                             |

You can customize these timeouts via the `timeout` option:

```typescript
import { query } from '@qwen-code/sdk';

const q = query({
  prompt: 'Your prompt',
  options: {
    timeout: {
      canUseTool: 60000, // 60 seconds for permission callback
      mcpRequest: 600000, // 10 minutes for MCP tool calls
      controlRequest: 60000, // 60 seconds for control requests
      streamClose: 15000, // 15 seconds for stream close wait
    },
  },
});
```

### Message Types

The SDK provides type guards to identify different message types:

```typescript
import {
  isSDKUserMessage,
  isSDKAssistantMessage,
  isSDKSystemMessage,
  isSDKResultMessage,
  isSDKPartialAssistantMessage,
} from '@qwen-code/sdk';

for await (const message of result) {
  if (isSDKAssistantMessage(message)) {
    // Handle assistant message
  } else if (isSDKResultMessage(message)) {
    // Handle result message
  }
}
```

### Query Instance Methods

The `Query` instance returned by `query()` provides several methods:

```typescript
const q = query({ prompt: 'Hello', options: {} });

// Get session ID
const sessionId = q.getSessionId();

// Check if closed
const closed = q.isClosed();

// Interrupt the current operation
await q.interrupt();

// Change permission mode mid-session
await q.setPermissionMode('yolo');

// Change model mid-session
await q.setModel('qwen-max');

// Get context window usage breakdown (token counts per category)
const usage = await q.getContextUsage();
// Pass true to hint that per-item details should be displayed
const detail = await q.getContextUsage(true);

// Close the session
await q.close();
```

`interrupt()` cancels only the active turn. For a multi-turn query created with
an async iterable prompt, the query and its input stream remain open, so later
messages from the iterable are processed normally. Use `close()` or abort the
configured `AbortController` when you want to end the entire session.

## Daemon caller-supplied session IDs

`DaemonClient.createOrAttachSession` accepts an optional `sessionId` for callers that must persist an identity before session creation:

```typescript
import { DaemonClient } from '@qwen-code/sdk';

const daemon = new DaemonClient({ baseUrl: 'http://127.0.0.1:4170' });
const session = await daemon.createOrAttachSession({
  workspaceCwd: '/path/to/project',
  sessionId: '550E8400-E29B-41D4-A716-446655440000',
});

console.log(session.sessionId); // 550e8400-e29b-41d4-a716-446655440000
```

The SDK requires the daemon's `session_id_override` capability before sending the mutation. REST mode serializes `sessionId` directly; an active ACP adapter maps it to `session/new._meta["qwen-code/sessionId"]`. The SDK verifies the success response and throws `DaemonSessionIdProtocolError` if the daemon returns a different ID.

This option always creates a new thread session and is not an idempotent attach. If the create outcome is ambiguous, use the known ID with load or resume. Omitting the option preserves the existing create-or-attach behavior.

## Daemon startup model and reasoning selection

`DaemonClient.createOrAttachSession` and `createStandaloneSession` accept `startupConfig: { modelServiceId, reasoningEffort? }` and preflight `session_startup_config` before creation. The model is required; omit reasoning for model-only selection, including models with no reasoning control. An explicit effort is validated against the model. The SDK checks `modelApplied: true` and the canonical `startupConfigApplied` response; model-only confirmation omits reasoning fields.

Startup selection does not save shared defaults or impose a lifetime pin. Definite selection rejection fails creation; uncertain standalone creation retains its existing recovery result. A successful response whose startup confirmation is missing or mismatched is rejected directly and does not trigger standalone recovery/adoption. See the [protocol](<Qwen Code 守护进程 HTTP 协议参考.md#capabilities>).

## Talking to running sessions

`@qwen-code/sdk/peer` lets a program that is not a Qwen Code session join the
sessions running as the same user on the same machine — a voice front-end, a
relay, a build watcher. The program shows up in `qwen sessions ps`, and in the
`list_agents` of every session that takes part in cross-session messaging — its
`agents.crossSessionMessaging` setting is on, and it was not started with
`--bare` or `--safe-mode` — which is also what lets those sessions message it
by name with `send_message`. It can message them back. It runs on Node only and
needs nothing beyond Node itself.

```typescript
import { PeerEndpoint } from '@qwen-code/sdk/peer';

const endpoint = await PeerEndpoint.start({
  name: 'voice-bridge',
  onMessage: (message) =>
    console.log(`${message.fromName}: ${message.content}`),
});

const [session] = await endpoint.list();
if (session) {
  const sent = await endpoint.send({
    to: session.address,
    content: 'What are you working on?',
  });
  if (sent.kind === 'sent') {
    const receipt = await endpoint.awaitReceipt(sent.msgId, { final: true });
    console.log(receipt?.status); // delivered, denied, refused, ...
  }
}

await endpoint.close();
```

A message like that is held for the session's user to review. To direct a
session without that review, mint a controller token with
`qwen sessions controllers add --label voice-bridge`, give it to the endpoint,
and mark the sends that should present it:

```typescript
const endpoint = await PeerEndpoint.start({
  name: 'voice-bridge',
  controllerToken: process.env['QWEN_CONTROLLER_TOKEN'],
});
await endpoint.send({
  to: 'my-app-3f',
  content: 'run the tests',
  controller: true,
});
```

Things to know:

- A session has an inbox only while its `agents.crossSessionMessaging` setting
  is on, which it is by default, and it was not started with `--bare` or
  `--safe-mode` — those turn messaging off whatever the setting says. A session
  that turned it off does not appear in `list()`, and its own `list_agents` and
  `send_message` cannot see or reach the program either. `qwen sessions ps`
  lists the program regardless.
- A message is delivered without review in exactly two cases: the send
  presents a controller token (`controller: true`), or its `fromMode` names the
  receiving session's own review class. `fromMode` is a claim nothing
  authenticates, so a program that is not a coding session should leave it
  out. Nothing in the record — not `kind`, not `name` — buys delivery. The
  receiving session's `agents.crossSessionInbound` setting outranks both:
  `hold` or `refuse` there wins over a controller token.
- Mark only the sends meant to direct a session. Addresses are resolved from
  records any program running as you can write, so a controller send presents
  the token to whichever process's record answers to that address. A
  controller send to another peer endpoint is dropped unread, because an
  endpoint's inbox accepts only its own token.
- The endpoint's inbox applies none of the protections a Qwen Code session
  applies to its own: no rate limit, no holds, and no duplicate window beyond
  the last 200 messages it answered. Every message is answered `delivered` and
  handed to `onMessage` as it arrives, so apply your own limits there if you
  need them. Without `onMessage`, every message is answered `refused`.
- Call `close()` before exiting, including from your own signal handlers. A
  process killed without closing leaves its record behind until a Qwen Code
  session lists the directory and sees the process is gone.
- UNIX domain sockets only: Windows is not supported yet.

The record schema, wire format and receipt states are documented in
[Cross-Session Protocol](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/cross-session-protocol.md>).

## Permission Modes

The SDK supports different permission modes for controlling tool execution:

- **`default`**: Write tools are denied unless approved via `canUseTool` callback or in `allowedTools`. Read-only tools execute without confirmation.
- **`plan`**: Blocks all write tools, instructing AI to present a plan first.
- **`auto-edit`**: Auto-approve edit tools (`edit`, `write_file`, `notebook_edit`) while other tools require confirmation.
- **`auto`**: Uses the built-in classifier to auto-approve safe tool calls and block risky ones, with manual-approval fallback after repeated policy blocks or classifier outages.
- **`yolo`**: All tools execute automatically without confirmation.

### Permission Priority Chain

Decision priority (highest first): `deny` > `ask` > `allow` > _(default/interactive mode)_

The first matching rule wins.

1. `excludeTools` / `permissions.deny` - Blocks tools completely (returns permission error)
2. `permissions.ask` - Always requires user confirmation
3. `permissionMode: 'plan'` - Blocks all non-read-only tools
4. `permissionMode: 'yolo'` - Auto-approves all tools
5. `allowedTools` / `permissions.allow` - Auto-approves matching tools
6. `permissionMode: 'auto'` - Classifier-mediated approval for remaining tools
7. `canUseTool` callback - Custom approval logic (if provided, not called for allowed tools)
8. Default behavior - Auto-deny in SDK mode (write tools require explicit approval)

## Examples

### Multi-turn Conversation

```typescript
import { query, type SDKUserMessage } from '@qwen-code/sdk';

async function* generateMessages(): AsyncIterable<SDKUserMessage> {
  yield {
    type: 'user',
    session_id: 'my-session',
    message: { role: 'user', content: 'Create a hello.txt file' },
    parent_tool_use_id: null,
  };

  // Wait for some condition or user input
  yield {
    type: 'user',
    session_id: 'my-session',
    message: { role: 'user', content: 'Now read the file back' },
    parent_tool_use_id: null,
  };
}

const result = query({
  prompt: generateMessages(),
  options: {
    permissionMode: 'auto-edit',
  },
});

for await (const message of result) {
  console.log(message);
}
```

### Custom Permission Handler

```typescript
import { query, type CanUseTool } from '@qwen-code/sdk';

const canUseTool: CanUseTool = async (toolName, input, { signal }) => {
  // Allow all read operations
  if (toolName.startsWith('read_')) {
    return { behavior: 'allow', updatedInput: input };
  }

  // Prompt user for write operations (in a real app)
  const userApproved = await promptUser(`Allow ${toolName}?`);

  if (userApproved) {
    return { behavior: 'allow', updatedInput: input };
  }

  return { behavior: 'deny', message: 'User denied the operation' };
};

const result = query({
  prompt: 'Create a new file',
  options: {
    canUseTool,
  },
});
```

### With External MCP Servers

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Use the custom tool from my MCP server',
  options: {
    mcpServers: {
      'my-server': {
        command: 'node',
        args: ['path/to/mcp-server.js'],
        env: { PORT: '3000' },
      },
    },
  },
});
```

### Override the System Prompt

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Say hello in one sentence.',
  options: {
    systemPrompt: 'You are a terse assistant. Answer in exactly one sentence.',
  },
});
```

### Append to the Built-in System Prompt

```typescript
import { query } from '@qwen-code/sdk';

const result = query({
  prompt: 'Review the current directory.',
  options: {
    systemPrompt: {
      type: 'preset',
      preset: 'qwen_code',
      append: 'Be terse and focus on concrete findings.',
    },
  },
});
```

### With SDK-Embedded MCP Servers

The SDK provides `tool` and `createSdkMcpServer` to create MCP servers that run in the same process as your SDK application. This is useful when you want to expose custom tools to the AI without running a separate server process.

#### `tool(name, description, inputSchema, handler)`

Creates a tool definition with Zod schema type inference.

| Parameter     | Type                               | Description                                                              |
| ------------- | ---------------------------------- | ------------------------------------------------------------------------ |
| `name`        | `string`                           | Tool name (1-64 chars, starts with letter, alphanumeric and underscores) |
| `description` | `string`                           | Human-readable description of what the tool does                         |
| `inputSchema` | `ZodRawShape`                      | Zod schema object defining the tool's input parameters                   |
| `handler`     | `(args, extra) => Promise<Result>` | Async function that executes the tool and returns MCP content blocks     |

The handler must return a `CallToolResult` object with the following structure:

```typescript
{
  content: Array<
    | { type: 'text'; text: string }
    | { type: 'image'; data: string; mimeType: string }
    | { type: 'resource'; uri: string; mimeType?: string; text?: string }
  >;
  isError?: boolean;
}
```

#### `createSdkMcpServer(options)`

Creates an SDK-embedded MCP server instance.

| Option    | Type                     | Default   | Description                          |
| --------- | ------------------------ | --------- | ------------------------------------ |
| `name`    | `string`                 | Required  | Unique name for the MCP server       |
| `version` | `string`                 | `'1.0.0'` | Server version                       |
| `tools`   | `SdkMcpToolDefinition[]` | -         | Array of tools created with `tool()` |

Returns a `McpSdkServerConfigWithInstance` object that can be passed directly to the `mcpServers` option.

#### Example

```typescript
import { z } from 'zod';
import { query, tool, createSdkMcpServer } from '@qwen-code/sdk';

// Define a tool with Zod schema
const calculatorTool = tool(
  'calculate_sum',
  'Add two numbers',
  { a: z.number(), b: z.number() },
  async (args) => ({
    content: [{ type: 'text', text: String(args.a + args.b) }],
  }),
);

// Create the MCP server
const server = createSdkMcpServer({
  name: 'calculator',
  tools: [calculatorTool],
});

// Use the server in a query
const result = query({
  prompt: 'What is 42 + 17?',
  options: {
    permissionMode: 'yolo',
    mcpServers: {
      calculator: server,
    },
  },
});

for await (const message of result) {
  console.log(message);
}
```

### Abort a Query

```typescript
import { query, isAbortError } from '@qwen-code/sdk';

const abortController = new AbortController();

const result = query({
  prompt: 'Long running task...',
  options: {
    abortController,
  },
});

// Abort after 5 seconds
setTimeout(() => abortController.abort(), 5000);

try {
  for await (const message of result) {
    console.log(message);
  }
} catch (error) {
  if (isAbortError(error)) {
    console.log('Query was aborted');
  } else {
    throw error;
  }
}
```

## Error Handling

The SDK provides an `AbortError` class for handling aborted queries:

```typescript
import { AbortError, isAbortError } from '@qwen-code/sdk';

try {
  // ... query operations
} catch (error) {
  if (isAbortError(error)) {
    // Handle abort
  } else {
    // Handle other errors
  }
}
```


</details>
