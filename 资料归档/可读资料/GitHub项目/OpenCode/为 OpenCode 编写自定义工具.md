# 为 OpenCode 编写自定义工具

扩展指南。介绍工具文件位置、TypeScript/JavaScript 定义、参数 Schema、上下文、命名冲突及调用 Python 的例子，帮助连接自己的开发操作。

- 来源：OpenCode
- 资料类型：扩展指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-05-27](<https://github.com/anomalyco/opencode/commit/86dc66eae99d7c28e9577488e3e815867d94f621>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：工具定义和实际执行脚本是不同层次，接入后需要测试。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/custom-tools.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/为 OpenCode 编写自定义工具.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Custom Tools
description: Create tools the LLM can call in opencode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

自定义工具是您创建的功能，LLM 可以在对话期间调用。它们与 opencode 的 [ 内置工具 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>) 一起工作，例如 `read`、`write` 和 `bash`。

---

## 创建工具

工具定义为 **TypeScript** 或 **JavaScript** 文件。但是，工具定义可以调用以**任何语言编写的脚本** - TypeScript或JavaScript仅用于工具定义本身。

---

### 地点

它们可以定义为：

- 在本地将它们放置在项目的 `.opencode/tools/` 目录中。
- 或者在全局范围内，将它们放置在 `~/.config/opencode/tools/` 中。

---

### 结构

创建工具的最简单方法是使用 `tool()` 帮助程序，它提供类型安全和验证。

**代码示例：** `.opencode/tools/database.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Query the project database",
  args: {
    query: tool.schema.string().describe("SQL query to execute"),
  },
  async execute(args) {
    // Your database logic here
    return `Executed query: ${args.query}`
  },
})
```

**文件名**成为**工具名称**。以上创建了一个 `database` 工具。

---

#### 每个文件多个工具

您还可以从单个文件导出多个工具。每次导出都会成为 ** 一个名为 **`<filename>_<exportname>`** 的单独工具 **：

**代码示例：** `.opencode/tools/math.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export const add = tool({
  description: "Add two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return (args.a + args.b).toString()
  },
})

export const multiply = tool({
  description: "Multiply two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return (args.a * args.b).toString()
  },
})
```

这将创建两个工具：`math_add` 和 `math_multiply`。

---

#### 与内置工具的名称冲突

自定义工具按工具名称键入。如果自定义工具与内置工具使用相同的名称，则自定义工具优先。

例如，此文件替换内置的 `bash` 工具：

**代码示例：** `.opencode/tools/bash.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Restricted bash wrapper",
  args: {
    command: tool.schema.string(),
  },
  async execute(args) {
    return `blocked: ${args.command}`
  },
})
```

**注释：**

最好使用唯一的名称，除非您有意要替换内置工具。如果您想禁用内置工具但不覆盖它，请使用[权限](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>)。

---

### 论点

您可以使用 `tool.schema`（即 [Zod](<https://zod.dev>)）来定义参数类型。

```ts
args: {
  query: tool.schema.string().describe("SQL query to execute")
}
```

您还可以直接导入 [Zod](<https://zod.dev>) 并返回一个普通对象：

```ts
import { z } from "zod"

export default {
  description: "Tool description",
  args: {
    param: z.string().describe("Parameter description"),
  },
  async execute(args, context) {
    // Tool implementation
    return "result"
  },
}
```

---

### 背景

工具接收有关当前会话的上下文：

**代码示例：** `.opencode/tools/project.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Get project information",
  args: {},
  async execute(args, context) {
    // Access context information
    const { agent, sessionID, messageID, directory, worktree } = context
    return `Agent: ${agent}, Session: ${sessionID}, Message: ${messageID}, Directory: ${directory}, Worktree: ${worktree}`
  },
})
```

使用 `context.directory` 作为会话工作目录。
使用 `context.worktree` 作为 git 工作树根。

---

## 示例

### 在Python中编写一个工具

您可以用任何您想要的语言编写工具。下面是使用 Python 将两个数字相加的示例。

首先，将该工具创建为 Python 脚本：

**代码示例：** `.opencode/tools/add.py`

```python
import sys

a = int(sys.argv[1])
b = int(sys.argv[2])
print(a + b)
```

然后创建调用它的工具定义：

**代码示例：** `.opencode/tools/python-add.ts`

```ts
import { tool } from "@opencode-ai/plugin"
import path from "path"

export default tool({
  description: "Add two numbers using Python",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args, context) {
    const script = path.join(context.worktree, ".opencode/tools/add.py")
    const result = await Bun.$`python3 ${script} ${args.a} ${args.b}`.text()
    return result.trim()
  },
})
```

这里我们使用 [`Bun.$`](<https://bun.com/docs/runtime/shell>) 实用程序来运行 Python 脚本。


## 原文对照

<details>
<summary>展开完整原文</summary>

Custom tools are functions you create that the LLM can call during conversations. They work alongside opencode's [built-in tools](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 内置、自定义与 MCP 工具.md>) like `read`, `write`, and `bash`.

---

## Creating a tool

Tools are defined as **TypeScript** or **JavaScript** files. However, the tool definition can invoke scripts written in **any language** — TypeScript or JavaScript is only used for the tool definition itself.

---

### Location

They can be defined:

- Locally by placing them in the `.opencode/tools/` directory of your project.
- Or globally, by placing them in `~/.config/opencode/tools/`.

---

### Structure

The easiest way to create tools is using the `tool()` helper which provides type-safety and validation.

**代码示例：** `.opencode/tools/database.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Query the project database",
  args: {
    query: tool.schema.string().describe("SQL query to execute"),
  },
  async execute(args) {
    // Your database logic here
    return `Executed query: ${args.query}`
  },
})
```

The **filename** becomes the **tool name**. The above creates a `database` tool.

---

#### Multiple tools per file

You can also export multiple tools from a single file. Each export becomes **a separate tool** with the name **`<filename>_<exportname>`**:

**代码示例：** `.opencode/tools/math.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export const add = tool({
  description: "Add two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return (args.a + args.b).toString()
  },
})

export const multiply = tool({
  description: "Multiply two numbers",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args) {
    return (args.a * args.b).toString()
  },
})
```

This creates two tools: `math_add` and `math_multiply`.

---

#### Name collisions with built-in tools

Custom tools are keyed by tool name. If a custom tool uses the same name as a built-in tool, the custom tool takes precedence.

For example, this file replaces the built-in `bash` tool:

**代码示例：** `.opencode/tools/bash.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Restricted bash wrapper",
  args: {
    command: tool.schema.string(),
  },
  async execute(args) {
    return `blocked: ${args.command}`
  },
})
```

**注释：**

Prefer unique names unless you intentionally want to replace a built-in tool. If you want to disable a built in tool but not override it, use [permissions](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>).

---

### Arguments

You can use `tool.schema`, which is just [Zod](<https://zod.dev>), to define argument types.

```ts
args: {
  query: tool.schema.string().describe("SQL query to execute")
}
```

You can also import [Zod](<https://zod.dev>) directly and return a plain object:

```ts
import { z } from "zod"

export default {
  description: "Tool description",
  args: {
    param: z.string().describe("Parameter description"),
  },
  async execute(args, context) {
    // Tool implementation
    return "result"
  },
}
```

---

### Context

Tools receive context about the current session:

**代码示例：** `.opencode/tools/project.ts`

```ts
import { tool } from "@opencode-ai/plugin"

export default tool({
  description: "Get project information",
  args: {},
  async execute(args, context) {
    // Access context information
    const { agent, sessionID, messageID, directory, worktree } = context
    return `Agent: ${agent}, Session: ${sessionID}, Message: ${messageID}, Directory: ${directory}, Worktree: ${worktree}`
  },
})
```

Use `context.directory` for the session working directory.
Use `context.worktree` for the git worktree root.

---

## Examples

### Write a tool in Python

You can write your tools in any language you want. Here's an example that adds two numbers using Python.

First, create the tool as a Python script:

**代码示例：** `.opencode/tools/add.py`

```python
import sys

a = int(sys.argv[1])
b = int(sys.argv[2])
print(a + b)
```

Then create the tool definition that invokes it:

**代码示例：** `.opencode/tools/python-add.ts`

```ts
import { tool } from "@opencode-ai/plugin"
import path from "path"

export default tool({
  description: "Add two numbers using Python",
  args: {
    a: tool.schema.number().describe("First number"),
    b: tool.schema.number().describe("Second number"),
  },
  async execute(args, context) {
    const script = path.join(context.worktree, ".opencode/tools/add.py")
    const result = await Bun.$`python3 ${script} ${args.a} ${args.b}`.text()
    return result.trim()
  },
})
```

Here we are using the [`Bun.$`](<https://bun.com/docs/runtime/shell>) utility to run the Python script.


</details>
