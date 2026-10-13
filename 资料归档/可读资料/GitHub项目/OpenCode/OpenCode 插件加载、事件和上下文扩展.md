# OpenCode 插件加载、事件和上下文扩展

扩展指南。说明本地和 npm 插件、依赖与加载顺序、事件钩子，给出通知、环境变量、工具、日志及压缩上下文的例子，帮助定制开发流程。

- 来源：OpenCode
- 资料类型：扩展指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-20](<https://github.com/anomalyco/opencode/commit/d86c10816d75837c8f85e7b1ab0de5ff37ecf77b>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：社区插件和例子需单独核验，不能直接执行存档示例。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/plugins.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 插件加载、事件和上下文扩展.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Plugins
description: Write your own plugins to extend OpenCode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

插件允许您通过挂钩各种事件和自定义行为来扩展 OpenCode。您可以创建插件来添加新功能、与外部服务集成或修改 OpenCode 的默认行为。

例如，查看社区创建的 [ 插件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#plugins>)。

---

## 使用插件

有两种加载插件的方法。

---

### 从本地文件

将JavaScript或TypeScript文件放在插件目录中。

- `.opencode/plugins/` - 项目级插件
- `~/.config/opencode/plugins/` - 全局插件

这些目录中的文件会在启动时自动加载。

---

### 来自npm

在配置文件中指定 npm 包。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-helicone-session", "opencode-wakatime", "@my-org/custom-plugin"]
}
```

支持常规包和范围 npm 包。

浏览 [ 生态系统 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#plugins>) 中的可用插件。

---

### 插件是如何安装的

**npm 插件 ** 在启动时使用 Bun 自动安装。包及其依赖项缓存在 `~/.cache/opencode/node_modules/` 中。

**本地插件**直接从插件目录加载。要使用外部包，您必须在配置目录中创建`package.json`（请参阅[依赖项](<#依赖关系>)），或将插件发布到npm和[将其添加到您的配置](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#plugins>)。

---

### 加载顺序

插件从所有源加载，所有挂钩按顺序运行。加载顺序为：

1. 全局配置（`~/.config/opencode/opencode.json`）
2. 项目配置（`opencode.json`）
3. 全局插件目录（`~/.config/opencode/plugins/`）
4. 项目插件目录（`.opencode/plugins/`）

具有相同名称和版本的重复 npm 包将被加载一次。但是，本地插件和名称相似的 npm 插件都是单独加载的。

---

## 创建一个插件

插件是一个 **JavaScript/TypeScript 模块 **，它导出一个或多个插件 
 功能。每个函数接收一个上下文对象并返回一个钩子对象。

---

### 依赖关系

本地插件和自定义工具可以使用外部npm包。将 `package.json` 添加到您的配置目录以及您需要的依赖项。

**代码示例：** `.opencode/package.json`

```json
{
  "dependencies": {
    "shescape": "^2.1.0"
  }
}
```

OpenCode 在启动时运行 `bun install` 来安装这些。然后您的插件和工具就可以导入它们。

**代码示例：** `.opencode/plugins/my-plugin.ts`

```ts
import { escape } from "shescape"

export const MyPlugin = async (ctx) => {
  return {
    "tool.execute.before": async (input, output) => {
      if (input.tool === "bash") {
        output.args.command = escape(output.args.command)
      }
    },
  }
}
```

---

### 基本结构

**代码示例：** `.opencode/plugins/example.js`

```js
export const MyPlugin = async ({ project, client, $, directory, worktree }) => {
  console.log("Plugin initialized!")

  return {
    // Hook implementations go here
  }
}
```

插件函数接收：

- `project`：当前项目信息。
- `directory`：当前工作目录。
- `worktree`：git 工作树路径。
- `client`：用于与AI交互的opencode SDK客户端。
- `$`：Bun的[shell API](<https://bun.com/docs/runtime/shell>)用于执行命令。

---

### TypeScript 支持

对于TypeScript插件，您可以从插件包中导入类型：

**代码示例：** `my-plugin.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const MyPlugin: Plugin = async ({ project, client, $, directory, worktree }) => {
  return {
    // Type-safe hook implementations
  }
}
```

---

### 活动

插件可以订阅事件，如下面的示例部分所示。以下是可用的不同事件的列表。

#### 命令事件

- `command.executed`

#### 文件事件

- `file.edited`
- `file.watcher.updated`

#### 安装活动

- `installation.updated`

#### LSP 活动

- `lsp.client.diagnostics`
- `lsp.updated`

#### 消息事件

- `message.part.removed`
- `message.part.updated`
- `message.removed`
- `message.updated`

#### 权限事件

- `permission.asked`
- `permission.replied`

#### 服务器事件

- `server.connected`

#### 会议活动

- `session.created`
- `session.compacted`
- `session.deleted`
- `session.diff`
- `session.error`
- `session.idle`
- `session.status`
- `session.updated`

#### 都都活动

- `todo.updated`

#### 壳牌活动

- `shell.env`

#### 工具事件

- `tool.execute.after`
- `tool.execute.before`

#### TUI 活动

- `tui.prompt.append`
- `tui.command.execute`
- `tui.toast.show`

---

## 示例

以下是一些可用于扩展 opencode 的插件示例。

---

### 发送通知

当某些事件发生时发送通知：

**代码示例：** `.opencode/plugins/notification.js`

```js
export const NotificationPlugin = async ({ project, client, $, directory, worktree }) => {
  return {
    event: async ({ event }) => {
      // Send notification on session completion
      if (event.type === "session.idle") {
        await $`osascript -e 'display notification "Session completed!" with title "opencode"'`
      }
    },
  }
}
```

我们正在使用 `osascript` 在 macOS 上运行 AppleScript。这里我们用它来发送通知。

**注释：**

如果您使用 OpenCode 桌面应用程序，它可以在响应准备就绪或会话出错时自动发送系统通知。

---

### .env 保护

阻止opencode读取`.env`文件：

**代码示例：** `.opencode/plugins/env-protection.js`

```javascript
export const EnvProtection = async ({ project, client, $, directory, worktree }) => {
  return {
    "tool.execute.before": async (input, output) => {
      if (input.tool === "read" && output.args.filePath.includes(".env")) {
        throw new Error("Do not read .env files")
      }
    },
  }
}
```

---

### 注入环境变量

将环境变量注入所有 shell 执行（AI 工具和用户终端）：

**代码示例：** `.opencode/plugins/inject-env.js`

```javascript
export const InjectEnvPlugin = async () => {
  return {
    "shell.env": async (input, output) => {
      output.env.MY_API_KEY = "secret"
      output.env.PROJECT_ROOT = input.cwd
    },
  }
}
```

---

### 定制工具

插件还可以将自定义工具添加到opencode：

**代码示例：** `.opencode/plugins/custom-tools.ts`

```ts
import { type Plugin, tool } from "@opencode-ai/plugin"

export const CustomToolsPlugin: Plugin = async (ctx) => {
  return {
    tool: {
      mytool: tool({
        description: "This is a custom tool",
        args: {
          foo: tool.schema.string(),
        },
        async execute(args, context) {
          const { directory, worktree } = context
          return `Hello ${args.foo} from ${directory} (worktree: ${worktree})`
        },
      }),
    },
  }
}
```

`tool` 帮助程序创建一个 opencode 可以调用的自定义工具。它采用 Zod 模式函数并返回一个工具定义：

- `description`：该工具的作用
- `args`：工具参数的 Zod 架构
- `execute`：调用工具时运行的函数

您的自定义工具将与内置工具一起可供 opencode 使用。

**注释：**

如果插件工具与内置工具使用相同的名称，则插件工具优先。

---

### 记录

使用 `client.app.log()` 而不是 `console.log` 进行结构化日志记录：

**代码示例：** `.opencode/plugins/my-plugin.ts`

```ts
export const MyPlugin = async ({ client }) => {
  await client.app.log({
    body: {
      service: "my-plugin",
      level: "info",
      message: "Plugin initialized",
      extra: { foo: "bar" },
    },
  })
}
```

级别：`debug`、`info`、`warn`、`error`。有关详细信息，请参阅[SDK文档](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode JavaScript 与 TypeScript SDK.md>)。

---

### 压实钩

自定义压缩会话时包含的上下文：

**代码示例：** `.opencode/plugins/compaction.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const CompactionPlugin: Plugin = async (ctx) => {
  return {
    "experimental.session.compacting": async (input, output) => {
      // Inject additional context into the compaction prompt
      output.context.push(`
## Custom Context

Include any state that should persist across compaction:
- Current task status
- Important decisions made
- Files being actively worked on
`)
    },
  }
}
```

`experimental.session.compacting` 钩子在 LLM 生成延续摘要之前触发。使用它来注入默认压缩提示会错过的特定于域的上下文。

您还可以通过设置 `output.prompt` 来完全替换压缩提示：

**代码示例：** `.opencode/plugins/custom-compaction.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const CustomCompactionPlugin: Plugin = async (ctx) => {
  return {
    "experimental.session.compacting": async (input, output) => {
      // Replace the entire compaction prompt
      output.prompt = `
You are generating a continuation prompt for a multi-agent swarm session.

Summarize:
1. The current task and its status
2. Which files are being modified and by whom
3. Any blockers or dependencies between agents
4. The next steps to complete the work

Format as a structured prompt that a new agent can use to resume work.
`
    },
  }
}
```

当设置`output.prompt`时，它完全取代默认的压缩提示。在这种情况下，`output.context` 数组将被忽略。


## 原文对照

<details>
<summary>展开完整原文</summary>

Plugins allow you to extend OpenCode by hooking into various events and customizing behavior. You can create plugins to add new features, integrate with external services, or modify OpenCode's default behavior.

For examples, check out the [plugins](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#plugins>) created by the community.

---

## Use a plugin

There are two ways to load plugins.

---

### From local files

Place JavaScript or TypeScript files in the plugin directory.

- `.opencode/plugins/` - Project-level plugins
- `~/.config/opencode/plugins/` - Global plugins

Files in these directories are automatically loaded at startup.

---

### From npm

Specify npm packages in your config file.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-helicone-session", "opencode-wakatime", "@my-org/custom-plugin"]
}
```

Both regular and scoped npm packages are supported.

Browse available plugins in the [ecosystem](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 社区插件、项目和 Agent 导航.md#plugins>).

---

### How plugins are installed

**npm plugins** are installed automatically using Bun at startup. Packages and their dependencies are cached in `~/.cache/opencode/node_modules/`.

**Local plugins** are loaded directly from the plugin directory. To use external packages, you must create a `package.json` within your config directory (see [Dependencies](<#dependencies>)), or publish the plugin to npm and [add it to your config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#plugins>).

---

### Load order

Plugins are loaded from all sources and all hooks run in sequence. The load order is:

1. Global config (`~/.config/opencode/opencode.json`)
2. Project config (`opencode.json`)
3. Global plugin directory (`~/.config/opencode/plugins/`)
4. Project plugin directory (`.opencode/plugins/`)

Duplicate npm packages with the same name and version are loaded once. However, a local plugin and an npm plugin with similar names are both loaded separately.

---

## Create a plugin

A plugin is a **JavaScript/TypeScript module** that exports one or more plugin
functions. Each function receives a context object and returns a hooks object.

---

### Dependencies

Local plugins and custom tools can use external npm packages. Add a `package.json` to your config directory with the dependencies you need.

**代码示例：** `.opencode/package.json`

```json
{
  "dependencies": {
    "shescape": "^2.1.0"
  }
}
```

OpenCode runs `bun install` at startup to install these. Your plugins and tools can then import them.

**代码示例：** `.opencode/plugins/my-plugin.ts`

```ts
import { escape } from "shescape"

export const MyPlugin = async (ctx) => {
  return {
    "tool.execute.before": async (input, output) => {
      if (input.tool === "bash") {
        output.args.command = escape(output.args.command)
      }
    },
  }
}
```

---

### Basic structure

**代码示例：** `.opencode/plugins/example.js`

```js
export const MyPlugin = async ({ project, client, $, directory, worktree }) => {
  console.log("Plugin initialized!")

  return {
    // Hook implementations go here
  }
}
```

The plugin function receives:

- `project`: The current project information.
- `directory`: The current working directory.
- `worktree`: The git worktree path.
- `client`: An opencode SDK client for interacting with the AI.
- `$`: Bun's [shell API](<https://bun.com/docs/runtime/shell>) for executing commands.

---

### TypeScript support

For TypeScript plugins, you can import types from the plugin package:

**代码示例：** `my-plugin.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const MyPlugin: Plugin = async ({ project, client, $, directory, worktree }) => {
  return {
    // Type-safe hook implementations
  }
}
```

---

### Events

Plugins can subscribe to events as seen below in the Examples section. Here is a list of the different events available.

#### Command Events

- `command.executed`

#### File Events

- `file.edited`
- `file.watcher.updated`

#### Installation Events

- `installation.updated`

#### LSP Events

- `lsp.client.diagnostics`
- `lsp.updated`

#### Message Events

- `message.part.removed`
- `message.part.updated`
- `message.removed`
- `message.updated`

#### Permission Events

- `permission.asked`
- `permission.replied`

#### Server Events

- `server.connected`

#### Session Events

- `session.created`
- `session.compacted`
- `session.deleted`
- `session.diff`
- `session.error`
- `session.idle`
- `session.status`
- `session.updated`

#### Todo Events

- `todo.updated`

#### Shell Events

- `shell.env`

#### Tool Events

- `tool.execute.after`
- `tool.execute.before`

#### TUI Events

- `tui.prompt.append`
- `tui.command.execute`
- `tui.toast.show`

---

## Examples

Here are some examples of plugins you can use to extend opencode.

---

### Send notifications

Send notifications when certain events occur:

**代码示例：** `.opencode/plugins/notification.js`

```js
export const NotificationPlugin = async ({ project, client, $, directory, worktree }) => {
  return {
    event: async ({ event }) => {
      // Send notification on session completion
      if (event.type === "session.idle") {
        await $`osascript -e 'display notification "Session completed!" with title "opencode"'`
      }
    },
  }
}
```

We are using `osascript` to run AppleScript on macOS. Here we are using it to send notifications.

**注释：**

If you’re using the OpenCode desktop app, it can send system notifications automatically when a response is ready or when a session errors.

---

### .env protection

Prevent opencode from reading `.env` files:

**代码示例：** `.opencode/plugins/env-protection.js`

```javascript
export const EnvProtection = async ({ project, client, $, directory, worktree }) => {
  return {
    "tool.execute.before": async (input, output) => {
      if (input.tool === "read" && output.args.filePath.includes(".env")) {
        throw new Error("Do not read .env files")
      }
    },
  }
}
```

---

### Inject environment variables

Inject environment variables into all shell execution (AI tools and user terminals):

**代码示例：** `.opencode/plugins/inject-env.js`

```javascript
export const InjectEnvPlugin = async () => {
  return {
    "shell.env": async (input, output) => {
      output.env.MY_API_KEY = "secret"
      output.env.PROJECT_ROOT = input.cwd
    },
  }
}
```

---

### Custom tools

Plugins can also add custom tools to opencode:

**代码示例：** `.opencode/plugins/custom-tools.ts`

```ts
import { type Plugin, tool } from "@opencode-ai/plugin"

export const CustomToolsPlugin: Plugin = async (ctx) => {
  return {
    tool: {
      mytool: tool({
        description: "This is a custom tool",
        args: {
          foo: tool.schema.string(),
        },
        async execute(args, context) {
          const { directory, worktree } = context
          return `Hello ${args.foo} from ${directory} (worktree: ${worktree})`
        },
      }),
    },
  }
}
```

The `tool` helper creates a custom tool that opencode can call. It takes a Zod schema function and returns a tool definition with:

- `description`: What the tool does
- `args`: Zod schema for the tool's arguments
- `execute`: Function that runs when the tool is called

Your custom tools will be available to opencode alongside built-in tools.

**注释：**

If a plugin tool uses the same name as a built-in tool, the plugin tool takes precedence.

---

### Logging

Use `client.app.log()` instead of `console.log` for structured logging:

**代码示例：** `.opencode/plugins/my-plugin.ts`

```ts
export const MyPlugin = async ({ client }) => {
  await client.app.log({
    body: {
      service: "my-plugin",
      level: "info",
      message: "Plugin initialized",
      extra: { foo: "bar" },
    },
  })
}
```

Levels: `debug`, `info`, `warn`, `error`. See [SDK documentation](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode JavaScript 与 TypeScript SDK.md>) for details.

---

### Compaction hooks

Customize the context included when a session is compacted:

**代码示例：** `.opencode/plugins/compaction.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const CompactionPlugin: Plugin = async (ctx) => {
  return {
    "experimental.session.compacting": async (input, output) => {
      // Inject additional context into the compaction prompt
      output.context.push(`
## Custom Context

Include any state that should persist across compaction:
- Current task status
- Important decisions made
- Files being actively worked on
`)
    },
  }
}
```

The `experimental.session.compacting` hook fires before the LLM generates a continuation summary. Use it to inject domain-specific context that the default compaction prompt would miss.

You can also replace the compaction prompt entirely by setting `output.prompt`:

**代码示例：** `.opencode/plugins/custom-compaction.ts`

```ts
import type { Plugin } from "@opencode-ai/plugin"

export const CustomCompactionPlugin: Plugin = async (ctx) => {
  return {
    "experimental.session.compacting": async (input, output) => {
      // Replace the entire compaction prompt
      output.prompt = `
You are generating a continuation prompt for a multi-agent swarm session.

Summarize:
1. The current task and its status
2. Which files are being modified and by whom
3. Any blockers or dependencies between agents
4. The next steps to complete the work

Format as a structured prompt that a new agent can use to resume work.
`
    },
  }
}
```

When `output.prompt` is set, it completely replaces the default compaction prompt. The `output.context` array is ignored in this case.


</details>
