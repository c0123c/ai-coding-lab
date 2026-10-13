# OpenCode JavaScript 与 TypeScript SDK

SDK 参考。展示安装、服务或客户端创建、类型、错误、结构化输出和会话、文件、认证、事件等 API，适合开发自己的 OpenCode 集成。

- 来源：OpenCode
- 资料类型：SDK 参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-12](<https://github.com/anomalyco/opencode/commit/ecab692ca15dceb065463731adfdee45ea91c49a>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：依赖 SDK 与服务端兼容版本，示例 API 须在实际环境验证。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/sdk.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode JavaScript 与 TypeScript SDK.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: SDK
description: Type-safe JS client for opencode server.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

opencode JS/TS SDK 提供类型安全的客户端，用于与服务器交互。
使用它以编程方式构建集成和控制 opencode。

[详细了解](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>)有关服务器如何工作的信息。例如，请查看社区构建的 [ 项目 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#projects>)。

---

## 安装

从 npm 安装 SDK：

```bash
npm install @opencode-ai/sdk
```

---

## 创建客户端

创建opencode的实例：

```javascript
import { createOpencode } from "@opencode-ai/sdk"

const { client } = await createOpencode()
```

这会同时启动服务器和客户端

#### 选项

|选项|类型|描述|默认|
| ---------- | ------------- | ------------------------------ | ----------- |
| `hostname` | `string`      |服务器主机名| `127.0.0.1` |
| `port`     | `number`      |服务器端口| `4096`      |
| `signal`   | `AbortSignal` |取消的中止信号| `undefined` |
| `timeout`  | `number`      |服务器启动超时（以毫秒为单位）| `5000`      |
| `config`   | `Config`      |配置对象| `{}`        |

---

## 配置

您可以传递配置对象来自定义行为。该实例仍会选择您的 `opencode.json`，但您可以覆盖或添加内联配置：

```javascript
import { createOpencode } from "@opencode-ai/sdk"

const opencode = await createOpencode({
  hostname: "127.0.0.1",
  port: 4096,
  config: {
    model: "anthropic/claude-3-5-sonnet-20241022",
  },
})

console.log(`Server running at ${opencode.server.url}`)

opencode.server.close()
```

## 仅限客户

如果您已经有一个正在运行的 opencode 实例，您可以创建一个客户端实例来连接到它：

```javascript
import { createOpencodeClient } from "@opencode-ai/sdk"

const client = createOpencodeClient({
  baseUrl: "http://localhost:4096",
})
```

#### 选项

|选项|类型|描述|默认|
| --------------- | ---------- | -------------------------------- | ----------------------- |
| `baseUrl`       | `string`   |服务器的URL| `http://localhost:4096` |
| `fetch`         | `function` |自定义获取实现| `globalThis.fetch`      |
| `parseAs`       | `string`   |响应解析方法| `auto`                  |
| `responseStyle` | `string`   |返回样式：`data`或`fields`| `fields`                |
| `throwOnError`  | `boolean`  |抛出错误而不是返回| `false`                 |

---

## 类型

SDK 包括所有 API 类型的 TypeScript 定义。直接导入它们：

```typescript
import type { Session, Message, Part } from "@opencode-ai/sdk"
```

所有类型均根据服务器的 OpenAPI 规范生成，并在 [ 类型文件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) 中提供。

---

## 错误

SDK 可能会引发错误，您可以捕获并处理这些错误：

```typescript
try {
  await client.session.get({ path: { id: "invalid-id" } })
} catch (error) {
  console.error("Failed to get session:", (error as Error).message)
}
```

---

## 结构化输出

您可以通过指定具有 JSON 架构的 `format` 来请求模型的结构化 JSON 输出。该模型将使用 `StructuredOutput` 工具返回与您的架构匹配的经过验证的 JSON。

### 基本用法

```typescript
const result = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [{ type: "text", text: "Research Anthropic and provide company info" }],
    format: {
      type: "json_schema",
      schema: {
        type: "object",
        properties: {
          company: { type: "string", description: "Company name" },
          founded: { type: "number", description: "Year founded" },
          products: {
            type: "array",
            items: { type: "string" },
            description: "Main products",
          },
        },
        required: ["company", "founded"],
      },
    },
  },
})

// Access the structured output
console.log(result.data.info.structured_output)
// { company: "Anthropic", founded: 2021, products: ["Claude", "Claude API"] }
```

### 输出格式类型

|类型|描述|
| ------------- | ------------------------------------------------------ |
| `text`        |默认。标准文本响应（无结构化输出）|
| `json_schema` |返回与提供的架构匹配的经过验证的 JSON|

### JSON 架构格式

使用`type: 'json_schema'`时，请提供：

|领域|类型|描述|
| ------------ | --------------- | ---------------------------------------------------------- |
| `type`       | `'json_schema'` |必填。指定 JSON 架构模式|
| `schema`     | `object`        |必填。 JSON 定义输出结构的模式对象|
| `retryCount` | `number`        |可选。验证重试次数（默认值：2）|

### 错误处理

如果模型在所有重试后未能生成有效的结构化输出，则响应将包含 `StructuredOutputError`：

```typescript
if (result.data.info.error?.name === "StructuredOutputError") {
  console.error("Failed to produce structured output:", result.data.info.error.message)
  console.error("Attempts:", result.data.info.error.retries)
}
```

### 最佳实践

1. **在架构属性中提供清晰的描述**，以帮助模型了解要提取的数据
2. **使用`required`**指定必须存在哪些字段
3. **保持模式集中** - 复杂的嵌套模式可能更难模型正确填充
4. **设置适当的`retryCount`** - 复杂模式增加，简单模式减少

---

## API

SDK 通过类型安全的客户端公开所有服务器 API。

---

### 全球

|方法|描述|回应|
| ----------------- | ------------------------------- | ------------------------------------ |
| `global.health()` |检查服务器健康状况和版本| `{ healthy: true, version: string }` |

---

#### 示例

```javascript
const health = await client.global.health()
console.log(health.data.version)
```

---

### 应用程序

|方法|描述|回应|
| -------------- | ------------------------- | ------------------------------------------- |
| `app.log()`    |写入日志条目| `boolean`                                   |
| `app.agents()` |列出所有可用的 agents| [`Agent[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

#### 示例

```javascript
// Write a log entry
await client.app.log({
  body: {
    service: "my-app",
    level: "info",
    message: "Operation completed",
  },
})

// List available agents
const agents = await client.app.agents()
```

---

### 项目

|方法|描述|回应|
| ------------------- | ------------------- | --------------------------------------------- |
| `project.list()`    |列出所有项目| [`Project[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |
| `project.current()` |获取当前项目| [`Project`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)   |

---

#### 示例

```javascript
// List all projects
const projects = await client.project.list()

// Get current project
const currentProject = await client.project.current()
```

---

### 路径

|方法|描述|回应|
| ------------ | ---------------- | ---------------------------------------- |
| `path.get()` |获取当前路径| [`Path`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

#### 示例

```javascript
// Get current path information
const pathInfo = await client.path.get()
```

---

### 配置

|方法|描述|回应|
| -------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `config.get()`       |获取配置信息| [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                            |
| `config.providers()` |列出提供者和默认模型| `{ providers: `[`Provider[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: { [key: string]: string } }` |

---

#### 示例

```javascript
const config = await client.config.get()

const { providers, default: defaults } = await client.config.providers()
```

---

### 会议

|方法|描述|注释|
| ---------------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `session.list()`                                           |列出会话|返回[`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.get({ path })`                                    |获取会话|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.children({ path })`                               |列出子会话|返回[`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.create({ body })`                                 |创建会话|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.delete({ path })`                                 |删除会话|返回`boolean`|
| `session.update({ path, body })`                           |更新会话属性|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.init({ path, body })`                             |分析应用并创建 `AGENTS.md`|返回`boolean`|
| `session.abort({ path })`                                  |中止正在运行的会话|返回`boolean`|
| `session.share({ path })`                                  |分享会|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.unshare({ path })`                                |取消共享会话|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.summarize({ path, body })`                        |总结会议|返回`boolean`|
| `session.messages({ path })`                               |列出会话中的消息|返回`{ info: `[`Message`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}[]`|
| `session.message({ path })`                                |获取消息详情|返回`{ info: `[`Message`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|
| `session.prompt({ path, body })`                           |发送提示信息|`body.noReply: true` 返回 UserMessage（仅限上下文）。默认返回 [`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) 和 AI 响应。支持`body.outputFormat`为[结构化输出](<#结构化输出>)|
| `session.command({ path, body })`                          |发送命令到会话|返回`{ info: `[`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`|
| `session.shell({ path, body })`                            |运行外壳命令|返回[`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.revert({ path, body })`                           |回复消息|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `session.unrevert({ path })`                               |恢复已恢复的消息|返回[`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)|
| `postSessionByIdPermissionsByPermissionId({ path, body })` |响应权限请求|返回`boolean`|

---

#### 示例

```javascript
// Create and manage sessions
const session = await client.session.create({
  body: { title: "My session" },
})

const sessions = await client.session.list()

// Send a prompt message
const result = await client.session.prompt({
  path: { id: session.id },
  body: {
    model: { providerID: "anthropic", modelID: "claude-3-5-sonnet-20241022" },
    parts: [{ type: "text", text: "Hello!" }],
  },
})

// Inject context without triggering AI response (useful for plugins)
await client.session.prompt({
  path: { id: session.id },
  body: {
    noReply: true,
    parts: [{ type: "text", text: "You are a helpful assistant." }],
  },
})
```

---

### 文件

|方法|描述|回应|
| ------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------- |
| `find.text({ query })`    |在文件中搜索文本|具有 `path`、`lines`、`line_number`、`absolute_offset`、`submatches` 的匹配对象数组|
| `find.files({ query })`   |按名称查找文件和目录|`string[]`（路径）|
| `find.symbols({ query })` |查找工作区符号| [`Symbol[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                |
| `file.read({ query })`    |读取文件| `{ type: "raw" \| "patch", content: string }`                                               |
| `file.status({ query? })` |获取跟踪文件的状态| [`File[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                  |

`find.files`支持一些可选的查询字段：

- `type`：`"file"`或`"directory"`
- `directory`：覆盖搜索的项目根目录
- `limit`：最大结果（1–200）

---

#### 示例

```javascript
// Search and read files
const textResults = await client.find.text({
  query: { pattern: "function.*opencode" },
})

const files = await client.find.files({
  query: { query: "*.ts", type: "file" },
})

const directories = await client.find.files({
  query: { query: "packages", type: "directory", limit: 20 },
})

const content = await client.file.read({
  query: { path: "src/index.ts" },
})
```

---

### TUI

|方法|描述|回应|
| ------------------------------ | ------------------------- | --------- |
| `tui.appendPrompt({ body })`   |将文本附加到提示| `boolean` |
| `tui.openHelp()`               |打开帮助对话框| `boolean` |
| `tui.openSessions()`           |打开会话选择器| `boolean` |
| `tui.openThemes()`             |打开主题选择器| `boolean` |
| `tui.openModels()`             |打开模型选择器| `boolean` |
| `tui.submitPrompt()`           |提交当前提示| `boolean` |
| `tui.clearPrompt()`            |清除提示| `boolean` |
| `tui.executeCommand({ body })` |执行命令| `boolean` |
| `tui.showToast({ body })`      |显示 Toast 通知| `boolean` |

---

#### 示例

```javascript
// Control TUI interface
await client.tui.appendPrompt({
  body: { text: "Add this to prompt" },
})

await client.tui.showToast({
  body: { message: "Task completed", variant: "success" },
})
```

---

### 授权

|方法|描述|回应|
| ------------------- | ------------------------------ | --------- |
| `auth.set({ ... })` |设置身份验证凭证| `boolean` |

---

#### 示例

```javascript
await client.auth.set({
  path: { id: "anthropic" },
  body: { type: "api", key: "your-api-key" },
})
```

---

### 活动

|方法|描述|回应|
| ------------------- | ------------------------- | ------------------------- |
| `event.subscribe()` |服务器发送的事件流|服务器发送的事件流|

---

#### 示例

```javascript
// Listen to real-time events
const events = await client.event.subscribe()
for await (const event of events.stream) {
  console.log("Event:", event.type, event.properties)
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

The opencode JS/TS SDK provides a type-safe client for interacting with the server.
Use it to build integrations and control opencode programmatically.

[Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>) about how the server works. For examples, check out the [projects](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#projects>) built by the community.

---

## Install

Install the SDK from npm:

```bash
npm install @opencode-ai/sdk
```

---

## Create client

Create an instance of opencode:

```javascript
import { createOpencode } from "@opencode-ai/sdk"

const { client } = await createOpencode()
```

This starts both a server and a client

#### Options

| Option     | Type          | Description                    | Default     |
| ---------- | ------------- | ------------------------------ | ----------- |
| `hostname` | `string`      | Server hostname                | `127.0.0.1` |
| `port`     | `number`      | Server port                    | `4096`      |
| `signal`   | `AbortSignal` | Abort signal for cancellation  | `undefined` |
| `timeout`  | `number`      | Timeout in ms for server start | `5000`      |
| `config`   | `Config`      | Configuration object           | `{}`        |

---

## Config

You can pass a configuration object to customize behavior. The instance still picks up your `opencode.json`, but you can override or add configuration inline:

```javascript
import { createOpencode } from "@opencode-ai/sdk"

const opencode = await createOpencode({
  hostname: "127.0.0.1",
  port: 4096,
  config: {
    model: "anthropic/claude-3-5-sonnet-20241022",
  },
})

console.log(`Server running at ${opencode.server.url}`)

opencode.server.close()
```

## Client only

If you already have a running instance of opencode, you can create a client instance to connect to it:

```javascript
import { createOpencodeClient } from "@opencode-ai/sdk"

const client = createOpencodeClient({
  baseUrl: "http://localhost:4096",
})
```

#### Options

| Option          | Type       | Description                      | Default                 |
| --------------- | ---------- | -------------------------------- | ----------------------- |
| `baseUrl`       | `string`   | URL of the server                | `http://localhost:4096` |
| `fetch`         | `function` | Custom fetch implementation      | `globalThis.fetch`      |
| `parseAs`       | `string`   | Response parsing method          | `auto`                  |
| `responseStyle` | `string`   | Return style: `data` or `fields` | `fields`                |
| `throwOnError`  | `boolean`  | Throw errors instead of return   | `false`                 |

---

## Types

The SDK includes TypeScript definitions for all API types. Import them directly:

```typescript
import type { Session, Message, Part } from "@opencode-ai/sdk"
```

All types are generated from the server's OpenAPI specification and available in the [types file](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>).

---

## Errors

The SDK can throw errors that you can catch and handle:

```typescript
try {
  await client.session.get({ path: { id: "invalid-id" } })
} catch (error) {
  console.error("Failed to get session:", (error as Error).message)
}
```

---

## Structured Output

You can request structured JSON output from the model by specifying an `format` with a JSON schema. The model will use a `StructuredOutput` tool to return validated JSON matching your schema.

### Basic Usage

```typescript
const result = await client.session.prompt({
  path: { id: sessionId },
  body: {
    parts: [{ type: "text", text: "Research Anthropic and provide company info" }],
    format: {
      type: "json_schema",
      schema: {
        type: "object",
        properties: {
          company: { type: "string", description: "Company name" },
          founded: { type: "number", description: "Year founded" },
          products: {
            type: "array",
            items: { type: "string" },
            description: "Main products",
          },
        },
        required: ["company", "founded"],
      },
    },
  },
})

// Access the structured output
console.log(result.data.info.structured_output)
// { company: "Anthropic", founded: 2021, products: ["Claude", "Claude API"] }
```

### Output Format Types

| Type          | Description                                            |
| ------------- | ------------------------------------------------------ |
| `text`        | Default. Standard text response (no structured output) |
| `json_schema` | Returns validated JSON matching the provided schema    |

### JSON Schema Format

When using `type: 'json_schema'`, provide:

| Field        | Type            | Description                                                |
| ------------ | --------------- | ---------------------------------------------------------- |
| `type`       | `'json_schema'` | Required. Specifies JSON schema mode                       |
| `schema`     | `object`        | Required. JSON Schema object defining the output structure |
| `retryCount` | `number`        | Optional. Number of validation retries (default: 2)        |

### Error Handling

If the model fails to produce valid structured output after all retries, the response will include a `StructuredOutputError`:

```typescript
if (result.data.info.error?.name === "StructuredOutputError") {
  console.error("Failed to produce structured output:", result.data.info.error.message)
  console.error("Attempts:", result.data.info.error.retries)
}
```

### Best Practices

1. **Provide clear descriptions** in your schema properties to help the model understand what data to extract
2. **Use `required`** to specify which fields must be present
3. **Keep schemas focused** - complex nested schemas may be harder for the model to fill correctly
4. **Set appropriate `retryCount`** - increase for complex schemas, decrease for simple ones

---

## APIs

The SDK exposes all server APIs through a type-safe client.

---

### Global

| Method            | Description                     | Response                             |
| ----------------- | ------------------------------- | ------------------------------------ |
| `global.health()` | Check server health and version | `{ healthy: true, version: string }` |

---

#### Examples

```javascript
const health = await client.global.health()
console.log(health.data.version)
```

---

### App

| Method         | Description               | Response                                    |
| -------------- | ------------------------- | ------------------------------------------- |
| `app.log()`    | Write a log entry         | `boolean`                                   |
| `app.agents()` | List all available agents | [`Agent[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

#### Examples

```javascript
// Write a log entry
await client.app.log({
  body: {
    service: "my-app",
    level: "info",
    message: "Operation completed",
  },
})

// List available agents
const agents = await client.app.agents()
```

---

### Project

| Method              | Description         | Response                                      |
| ------------------- | ------------------- | --------------------------------------------- |
| `project.list()`    | List all projects   | [`Project[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |
| `project.current()` | Get current project | [`Project`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)   |

---

#### Examples

```javascript
// List all projects
const projects = await client.project.list()

// Get current project
const currentProject = await client.project.current()
```

---

### Path

| Method       | Description      | Response                                 |
| ------------ | ---------------- | ---------------------------------------- |
| `path.get()` | Get current path | [`Path`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) |

---

#### Examples

```javascript
// Get current path information
const pathInfo = await client.path.get()
```

---

### Config

| Method               | Description                       | Response                                                                                              |
| -------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `config.get()`       | Get config info                   | [`Config`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                            |
| `config.providers()` | List providers and default models | `{ providers: `[`Provider[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, default: { [key: string]: string } }` |

---

#### Examples

```javascript
const config = await client.config.get()

const { providers, default: defaults } = await client.config.providers()
```

---

### Sessions

| Method                                                     | Description                        | Notes                                                                                                                                                                                                                    |
| ---------------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `session.list()`                                           | List sessions                      | Returns [`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                    |
| `session.get({ path })`                                    | Get session                        | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.children({ path })`                               | List child sessions                | Returns [`Session[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                    |
| `session.create({ body })`                                 | Create session                     | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.delete({ path })`                                 | Delete session                     | Returns `boolean`                                                                                                                                                                                                        |
| `session.update({ path, body })`                           | Update session properties          | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.init({ path, body })`                             | Analyze app and create `AGENTS.md` | Returns `boolean`                                                                                                                                                                                                        |
| `session.abort({ path })`                                  | Abort a running session            | Returns `boolean`                                                                                                                                                                                                        |
| `session.share({ path })`                                  | Share session                      | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.unshare({ path })`                                | Unshare session                    | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.summarize({ path, body })`                        | Summarize session                  | Returns `boolean`                                                                                                                                                                                                        |
| `session.messages({ path })`                               | List messages in a session         | Returns `{ info: `[`Message`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}[]`                                                                                                  |
| `session.message({ path })`                                | Get message details                | Returns `{ info: `[`Message`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`                                                                                                    |
| `session.prompt({ path, body })`                           | Send prompt message                | `body.noReply: true` returns UserMessage (context only). Default returns [`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>) with AI response. Supports `body.outputFormat` for [structured output](<#structured-output>) |
| `session.command({ path, body })`                          | Send command to session            | Returns `{ info: `[`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`, parts: `[`Part[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)`}`                                                                                           |
| `session.shell({ path, body })`                            | Run a shell command                | Returns [`AssistantMessage`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                             |
| `session.revert({ path, body })`                           | Revert a message                   | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `session.unrevert({ path })`                               | Restore reverted messages          | Returns [`Session`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                                                                                                                                      |
| `postSessionByIdPermissionsByPermissionId({ path, body })` | Respond to a permission request    | Returns `boolean`                                                                                                                                                                                                        |

---

#### Examples

```javascript
// Create and manage sessions
const session = await client.session.create({
  body: { title: "My session" },
})

const sessions = await client.session.list()

// Send a prompt message
const result = await client.session.prompt({
  path: { id: session.id },
  body: {
    model: { providerID: "anthropic", modelID: "claude-3-5-sonnet-20241022" },
    parts: [{ type: "text", text: "Hello!" }],
  },
})

// Inject context without triggering AI response (useful for plugins)
await client.session.prompt({
  path: { id: session.id },
  body: {
    noReply: true,
    parts: [{ type: "text", text: "You are a helpful assistant." }],
  },
})
```

---

### Files

| Method                    | Description                        | Response                                                                                    |
| ------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------- |
| `find.text({ query })`    | Search for text in files           | Array of match objects with `path`, `lines`, `line_number`, `absolute_offset`, `submatches` |
| `find.files({ query })`   | Find files and directories by name | `string[]` (paths)                                                                          |
| `find.symbols({ query })` | Find workspace symbols             | [`Symbol[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                |
| `file.read({ query })`    | Read a file                        | `{ type: "raw" \| "patch", content: string }`                                               |
| `file.status({ query? })` | Get status for tracked files       | [`File[]`](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/sdk/js/src/gen/types.gen.ts>)                                                  |

`find.files` supports a few optional query fields:

- `type`: `"file"` or `"directory"`
- `directory`: override the project root for the search
- `limit`: max results (1–200)

---

#### Examples

```javascript
// Search and read files
const textResults = await client.find.text({
  query: { pattern: "function.*opencode" },
})

const files = await client.find.files({
  query: { query: "*.ts", type: "file" },
})

const directories = await client.find.files({
  query: { query: "packages", type: "directory", limit: 20 },
})

const content = await client.file.read({
  query: { path: "src/index.ts" },
})
```

---

### TUI

| Method                         | Description               | Response  |
| ------------------------------ | ------------------------- | --------- |
| `tui.appendPrompt({ body })`   | Append text to the prompt | `boolean` |
| `tui.openHelp()`               | Open the help dialog      | `boolean` |
| `tui.openSessions()`           | Open the session selector | `boolean` |
| `tui.openThemes()`             | Open the theme selector   | `boolean` |
| `tui.openModels()`             | Open the model selector   | `boolean` |
| `tui.submitPrompt()`           | Submit the current prompt | `boolean` |
| `tui.clearPrompt()`            | Clear the prompt          | `boolean` |
| `tui.executeCommand({ body })` | Execute a command         | `boolean` |
| `tui.showToast({ body })`      | Show toast notification   | `boolean` |

---

#### Examples

```javascript
// Control TUI interface
await client.tui.appendPrompt({
  body: { text: "Add this to prompt" },
})

await client.tui.showToast({
  body: { message: "Task completed", variant: "success" },
})
```

---

### Auth

| Method              | Description                    | Response  |
| ------------------- | ------------------------------ | --------- |
| `auth.set({ ... })` | Set authentication credentials | `boolean` |

---

#### Examples

```javascript
await client.auth.set({
  path: { id: "anthropic" },
  body: { type: "api", key: "your-api-key" },
})
```

---

### Events

| Method              | Description               | Response                  |
| ------------------- | ------------------------- | ------------------------- |
| `event.subscribe()` | Server-sent events stream | Server-sent events stream |

---

#### Examples

```javascript
// Listen to real-time events
const events = await client.event.subscribe()
for await (const event of events.stream) {
  console.log("Event:", event.type, event.properties)
}
```


</details>
