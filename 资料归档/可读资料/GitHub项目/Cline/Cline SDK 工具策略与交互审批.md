# Cline SDK 工具策略与交互审批

SDK 指南。解释工具启用和自动审批策略、按工具分级、条件审批回调及拒绝后的处理；特别指出未显式列出的工具默认启用并自动批准。

- 来源：Cline
- 资料类型：SDK 指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/cline/cline/commit/6354d209c8e54274e78732b4923162b494d97b81>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：权限默认值与产品扩展界面不能混为一谈。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/guides/permission-handling.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline SDK 工具策略与交互审批.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Permission Handling"
sidebarTitle: "Permission Handling"
description: "Control which tools agents can run automatically and which require explicit approval."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

工具策略控制是否启用工具以及是否未经批准运行工具。 `toolPolicies` 中未列出的工具名称默认启用并自动批准，因此为需要审查的工具明确设置策略。

## 工具政策

管理权限的最简单方法是通过工具策略。创建 agent 时按工具设置它们：

```typescript
import { Agent, createBuiltinTools } from "@cline/sdk"

const agent = new Agent({
  // ...config
  tools: createBuiltinTools({ cwd: process.cwd() }),
  toolPolicies: {
    read_files: { autoApprove: true },       // Always run without asking
    search_codebase: { autoApprove: true },  // Always run without asking
    editor: { autoApprove: false },          // Always ask before running
    run_commands: { autoApprove: false },    // Always ask before running
  },
})
```

### 政策选择

|政策|效果|
|--------|--------|
| `{ autoApprove: true }` |工具未经批准立即执行|
| `{ autoApprove: false }` |工具在执行前等待批准|
| `{ enabled: false }` |工具完全禁用（模型看不到它）|
|没有制定政策|默认为启用并自动批准|

## 自动批准一切

对于可信环境（CI管道、沙盒容器、开发脚本）：

```typescript
const agent = new Agent({
  // ...config
  tools: allTools,
  toolPolicies: Object.fromEntries(
    allTools.map((t) => [t.name, { autoApprove: true }])
  ),
})
```

或者使用 ClineCore：

```typescript
const session = await cline.start({
  config: {
    enableTools: true,
  },
  toolPolicies: {
    run_commands: { autoApprove: true },
    editor: { autoApprove: true },
    read_files: { autoApprove: true },
    apply_patch: { autoApprove: true },
    search_codebase: { autoApprove: true },
    fetch_web_content: { autoApprove: true },
  },
  capabilities: {
    requestToolApproval: async () => ({ approved: true }),
  },
  // ...
})
```

**注意：**

自动批准所有工具意味着 agent 可以执行任何 shell 命令、修改任何文件以及发出网络请求，而无需您的审核。仅在 agent 的操作处于沙盒或完全受信任的环境中使用此功能。

## 互动审批

对于需要人工监督的应用程序，实施自定义审批处理程序：

```typescript
import { ClineCore } from "@cline/sdk"
import * as readline from "readline"

const rl = readline.createInterface({ input: process.stdin, output: process.stdout })
const ask = (q: string) => new Promise<string>((res) => rl.question(q, res))

const cline = await ClineCore.create({
  clientName: "interactive-app",
  capabilities: {
    requestToolApproval: async (request) => {
      console.log(`\nTool: ${request.toolName}`)
      console.log(`Input: ${JSON.stringify(request.input, null, 2)}`)

      const answer = await ask("Approve? (y/n): ")
      return { approved: answer.toLowerCase() === "y" }
    },
  },
})
```

## 分级权限

实用的中间立场：自动批准只读操作，需要批准写入：

```typescript
const READ_TOOLS = new Set(["read_files", "search_codebase", "fetch_web_content"])
const WRITE_TOOLS = new Set(["run_commands", "editor", "apply_patch"])

const toolPolicies: Record<string, { autoApprove: boolean }> = {}

for (const tool of READ_TOOLS) {
  toolPolicies[tool] = { autoApprove: true }
}
for (const tool of WRITE_TOOLS) {
  toolPolicies[tool] = { autoApprove: false }
}
```

## 有条件批准逻辑

根据该工具的实际用途进行批准，而不仅仅是它是什么工具：

```typescript
const cline = await ClineCore.create({
  clientName: "smart-approval",
  capabilities: {
    requestToolApproval: async (request) => {
      // request.input is the model's raw tool input, so check its shape first.
      const input = request.input as { commands?: unknown; files?: unknown }

      // Auto-approve a fixed set of commands that only read: { commands: string[] }.
      // Match whole commands, because flags can change what a command does.
      // Git can also run programs named in a repository's config (for example
      // core.fsmonitor), so approve Git commands only in repositories you trust.
      if (request.toolName === "run_commands" && Array.isArray(input.commands)) {
        const safeCommands = new Set(["ls", "git status", "git log --oneline -20"])
        if (input.commands.every((cmd) => typeof cmd === "string" && safeCommands.has(cmd.trim()))) {
          return { approved: true }
        }
      }

      // Auto-approve reads in specific directories: { files: { path: string }[] }.
      if (request.toolName === "read_files" && Array.isArray(input.files)) {
        const isAllowed = (file: unknown) => {
          const path = (file as { path?: unknown })?.path
          return typeof path === "string" && !path.includes("..") &&
            (path.startsWith("/src/") || path.startsWith("/tests/"))
        }
        if (input.files.every(isAllowed)) {
          return { approved: true }
        }
      }

      // Everything else requires manual approval
      console.log(`Approval needed: ${request.toolName}`)
      console.log(`Input: ${JSON.stringify(request.input)}`)
      return { approved: false }
    },
  },
})
```

## 工具被拒绝时会发生什么

当批准被拒绝时，agent会收到拒绝消息并可以调整其方法。它可能：
- 请用户澄清
- 尝试不同的工具来实现相同的目标
- 修改其方法并使用不同的参数重试
- 放弃该子任务并继续

agent 不会陷入循环。拒绝算作响应，并且 agent 继续进行下一次迭代。


## 原文对照

<details>
<summary>展开完整原文</summary>

Tool policies control whether tools are enabled and whether they run without approval. Tool names not listed in `toolPolicies` default to enabled and auto-approved, so set policies explicitly for tools that need review.

## Tool Policies

The simplest way to manage permissions is through tool policies. Set them per-tool when creating an agent:

```typescript
import { Agent, createBuiltinTools } from "@cline/sdk"

const agent = new Agent({
  // ...config
  tools: createBuiltinTools({ cwd: process.cwd() }),
  toolPolicies: {
    read_files: { autoApprove: true },       // Always run without asking
    search_codebase: { autoApprove: true },  // Always run without asking
    editor: { autoApprove: false },          // Always ask before running
    run_commands: { autoApprove: false },    // Always ask before running
  },
})
```

### Policy Options

| Policy | Effect |
|--------|--------|
| `{ autoApprove: true }` | Tool executes immediately without approval |
| `{ autoApprove: false }` | Tool waits for approval before executing |
| `{ enabled: false }` | Tool is completely disabled (model won't see it) |
| No policy set | Defaults to enabled and auto-approved |

## Auto-Approve Everything

For trusted environments (CI pipelines, sandboxed containers, development scripts):

```typescript
const agent = new Agent({
  // ...config
  tools: allTools,
  toolPolicies: Object.fromEntries(
    allTools.map((t) => [t.name, { autoApprove: true }])
  ),
})
```

Or with ClineCore:

```typescript
const session = await cline.start({
  config: {
    enableTools: true,
  },
  toolPolicies: {
    run_commands: { autoApprove: true },
    editor: { autoApprove: true },
    read_files: { autoApprove: true },
    apply_patch: { autoApprove: true },
    search_codebase: { autoApprove: true },
    fetch_web_content: { autoApprove: true },
  },
  capabilities: {
    requestToolApproval: async () => ({ approved: true }),
  },
  // ...
})
```

**注意：**

Auto-approving all tools means the agent can execute any shell command, modify any file, and make network requests without your review. Only use this in environments where the agent's actions are either sandboxed or fully trusted.

## Interactive Approval

For applications that need human oversight, implement a custom approval handler:

```typescript
import { ClineCore } from "@cline/sdk"
import * as readline from "readline"

const rl = readline.createInterface({ input: process.stdin, output: process.stdout })
const ask = (q: string) => new Promise<string>((res) => rl.question(q, res))

const cline = await ClineCore.create({
  clientName: "interactive-app",
  capabilities: {
    requestToolApproval: async (request) => {
      console.log(`\nTool: ${request.toolName}`)
      console.log(`Input: ${JSON.stringify(request.input, null, 2)}`)

      const answer = await ask("Approve? (y/n): ")
      return { approved: answer.toLowerCase() === "y" }
    },
  },
})
```

## Tiered Permissions

A practical middle ground: auto-approve read-only operations, require approval for writes:

```typescript
const READ_TOOLS = new Set(["read_files", "search_codebase", "fetch_web_content"])
const WRITE_TOOLS = new Set(["run_commands", "editor", "apply_patch"])

const toolPolicies: Record<string, { autoApprove: boolean }> = {}

for (const tool of READ_TOOLS) {
  toolPolicies[tool] = { autoApprove: true }
}
for (const tool of WRITE_TOOLS) {
  toolPolicies[tool] = { autoApprove: false }
}
```

## Conditional Approval Logic

Approve based on what the tool is actually doing, not just which tool it is:

```typescript
const cline = await ClineCore.create({
  clientName: "smart-approval",
  capabilities: {
    requestToolApproval: async (request) => {
      // request.input is the model's raw tool input, so check its shape first.
      const input = request.input as { commands?: unknown; files?: unknown }

      // Auto-approve a fixed set of commands that only read: { commands: string[] }.
      // Match whole commands, because flags can change what a command does.
      // Git can also run programs named in a repository's config (for example
      // core.fsmonitor), so approve Git commands only in repositories you trust.
      if (request.toolName === "run_commands" && Array.isArray(input.commands)) {
        const safeCommands = new Set(["ls", "git status", "git log --oneline -20"])
        if (input.commands.every((cmd) => typeof cmd === "string" && safeCommands.has(cmd.trim()))) {
          return { approved: true }
        }
      }

      // Auto-approve reads in specific directories: { files: { path: string }[] }.
      if (request.toolName === "read_files" && Array.isArray(input.files)) {
        const isAllowed = (file: unknown) => {
          const path = (file as { path?: unknown })?.path
          return typeof path === "string" && !path.includes("..") &&
            (path.startsWith("/src/") || path.startsWith("/tests/"))
        }
        if (input.files.every(isAllowed)) {
          return { approved: true }
        }
      }

      // Everything else requires manual approval
      console.log(`Approval needed: ${request.toolName}`)
      console.log(`Input: ${JSON.stringify(request.input)}`)
      return { approved: false }
    },
  },
})
```

## What Happens When a Tool is Rejected

When approval is denied, the agent receives a rejection message and can adjust its approach. It might:
- Ask the user for clarification
- Try a different tool to accomplish the same goal
- Modify its approach and try again with different parameters
- Give up on that subtask and move on

The agent does not get stuck in a loop. The rejection counts as a response, and the agent proceeds with its next iteration.


</details>
