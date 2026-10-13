# Cline 子 Agent 的任务、上下文与审批

产品指南。说明子 Agent 的独立上下文、并行报告、启停、成本和授权方式，适合分工调查代码库；正文限定 CLI 与 Desktop，并提示审批主调用后子工具不再逐次询问。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/cline/cline/commit/db0be4c3fa8c19c201878e5dc4b3f4d74ffebdaf>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：存档正文限定 CLI 和 Desktop；子任务授权范围需单独理解。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/features/subagents.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 子 Agent 的任务、上下文与审批.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Subagents"
sidebarTitle: "Subagents"
description: "Run parallel research agents to explore your codebase without filling the main agent's context window."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

**注意：**

子代理可在 Cline CLI 和 Cline 桌面中使用。 VS Code 和 JetBrains 扩展不使用它们。

子代理让 Cline 将重点任务交给另一个 agent。每个子代理都有自己的提示和上下文窗口，执行工作，并将报告返回给主代理 agent。这可以保持主 agent 的上下文干净，并且多个子代理可以同时运行。

## 它是如何运作的

当Cline使用`spawn_agent`工具时，它会启动一个子代理。每一个：

- 从主agent获取自己的指令和任务
- 使用单独的上下文窗口运行
- 具有Cline当前模式的内置工具。在行动模式下，它可以读取和编辑文件以及运行命令。在计划模式下，它没有用于编辑文件的工具，并且Cline拒绝通常更改文件的shell命令，例如`rm`和`git commit`。其他命令仍然运行，其中一些命令，例如`python -c`，可以写入文件。子代理可以启动自己的子代理。它没有获得 MCP 工具。
- 返回对主agent的最终回复

主代理 agent 等待其子代理，然后继续。他们的代币使用和成本将添加到会话总量中。

## 批准

批准 `spawn_agent` 调用即批准子代理随后执行的所有操作：其自己的工具调用不请求批准。批准设置不适用于子代理内部，因此请在批准之前阅读子代理的任务。

## 关闭子代理

子代理在“行动”和“计划”模式下处于启用状态。要关闭它们，请关闭 `spawn_agent` 工具：

- **CLI:**运行`cline`，输入`/settings`，然后关闭**工具**选项卡上的`spawn_agent`。
- **桌面：**打开**自定义**，选择**工具**，关闭`spawn_agent`。

该设置适用于这两种产品中的新会话。

## 使用子代理

Cline 决定任务何时值得拆分。您还可以在提示中询问子代理。

提示示例：

- “使用子代理探索身份验证的工作原理以及数据库模型的定义位置”
- “启动子代理以调查 API 路由、测试设置和部署配置”
- “我是这个代码库的新手。使用子代理来规划主要入口点、路由层和数据访问模式”

向每个子代理提出一个重点问题。仅请求研究并在计划模式下工作，使子代理不太可能更改文件，但两者都不会阻止每次更改。

## 何时使用子代理

当您同时需要来自代码库的多个区域的广泛上下文时，子代理效果最佳：

- **加入一个不熟悉的项目**：要求子代理并行地规划架构、关键入口点和数据流。
- **调查横切问题**：让单独的子代理同时跟踪身份验证、日志记录和错误处理。
- **预编辑研究**：在进行更改之前，使用子代理从相关文件中收集上下文，以便主agent可以进行明智的编辑，而无需烧毁其上下文窗口。
- **大型代码库**：当顺序读取许多文件会消耗太多主agent的上下文时，子代理可以让您广泛探索，而无需进行这种权衡。

对于您已经知道要查看哪些文件的小型、集中任务，子代理会增加不必要的开销。直接询问Cline即可。


## 原文对照

<details>
<summary>展开完整原文</summary>

**注意：**

Subagents are available in the Cline CLI and Cline Desktop. The VS Code and JetBrains extensions don't use them.

Subagents let Cline hand a focused task to another agent. Each subagent gets its own prompt and context window, does the work, and returns a report to the main agent. This keeps the main agent's context clean, and several subagents can run at the same time.

## How It Works

When Cline uses the `spawn_agent` tool, it starts a subagent. Each one:

- Gets its own instructions and task from the main agent
- Runs with a separate context window
- Has Cline's built-in tools for the current mode. In Act mode it can read and edit files and run commands. In Plan mode it has no tools for editing files, and Cline refuses shell commands that usually change files, such as `rm` and `git commit`. Other commands still run, and some of them, such as `python -c`, can write files. A subagent can start subagents of its own. It doesn't get MCP tools.
- Returns its final reply to the main agent

The main agent waits for its subagents, then continues. Their token use and cost are added to the session's total.

## Approvals

Approving the `spawn_agent` call approves everything the subagent then does: its own tool calls don't ask for approval. Approval settings don't apply inside a subagent, so read the subagent's task before you approve it.

## Turning Subagents Off

Subagents are on in Act and Plan modes. To turn them off, turn off the `spawn_agent` tool:

- **CLI:** run `cline`, type `/settings`, and turn off `spawn_agent` on the **Tools** tab.
- **Desktop:** open **Customize**, select **Tools**, and turn off `spawn_agent`.

The setting applies to new sessions in both products.

## Using Subagents

Cline decides when a task is worth splitting. You can also ask for subagents in your prompt.

Example prompts:

- "Use subagents to explore how authentication works and where the database models are defined"
- "Spin up subagents to investigate the API routes, the test setup, and the deployment config"
- "I'm new to this codebase. Use subagents to map out the main entry points, the routing layer, and the data access patterns"

Give each subagent one focused question. Asking for research only, and working in Plan mode, make subagents less likely to change files, but neither stops every change.

## When to Use Subagents

Subagents work best when you need broad context from multiple areas of a codebase at once:

- **Onboarding to an unfamiliar project**: Ask subagents to map out the architecture, key entry points, and data flow in parallel.
- **Investigating cross-cutting concerns**: Have separate subagents trace authentication, logging, and error handling simultaneously.
- **Pre-edit research**: Before making changes, use subagents to gather context from related files so the main agent can make informed edits without burning through its context window.
- **Large codebases**: When reading many files sequentially would consume too much of the main agent's context, subagents let you explore broadly without that tradeoff.

For small, focused tasks where you already know which files to look at, subagents add unnecessary overhead. Just ask Cline directly.


</details>
