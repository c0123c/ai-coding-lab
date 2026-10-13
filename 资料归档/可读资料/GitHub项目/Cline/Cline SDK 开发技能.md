# Cline SDK 开发技能

汇总 Agent 运行时、会话、自定义工具、插件、事件、模型接入、调度和部署的参考入口，并给出按开发任务选择 API 的阅读顺序。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-05-14](<https://github.com/cline/cline/commit/83dcd4c17847e0b2bae9eeb4d4bfddf78ff1e97a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.agents/skills/cline-sdk/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline SDK 开发技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: cline-sdk
description: Comprehensive Cline SDK skill for building AI agents. Covers the Agent runtime, ClineCore sessions, custom tools, plugins, events, LLM providers, scheduling, multi-agent teams, and production deployment. Use for any task involving @cline/sdk or its sub-packages.
metadata:
   references: agent, clinecore
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Cline SDK 技能

与 Cline SDK 构建 AI agents 的综合技能。使用下面的决策树找到正确的入口点和 API 表面，然后加载详细的参考。

## 关键规则

在所有 Cline SDK 代码中遵循以下规则：

1. 使用`npm install @cline/sdk`安装。 `@cline/sdk` 包重新导出 `@cline/core`、`@cline/agents`、`@cline/llms` 和 `@cline/shared` 中的所有内容。
2. 需要 Node.js 22 或更高版本。
3. 使用 `@cline/sdk`（或 `@cline/shared`）中的 `createTool()` 定义工具。工具名称必须是`snake_case`。
4. 从工具 `execute` 函数将错误作为结构化数据返回。投掷算作“错误”，违反了 agent 的错误限制。
5. 在应该结束 agent 循环的工具上使用 `lifecycle: { completesRun: true }`（例如“提交答案”工具）。
6. 使用 `ClineCore` 时，请务必在完成后调用 `dispose()` 来清理资源。
7. 独立的`Agent`和`ClineCore`具有不同的事件系统。对于 `Agent`：使用 `agent.subscribe()` 获取 `AgentRuntimeEvent` 类型（文本流为 `"assistant-text-delta"`，结果文本为 `result.outputText`）。对于 `ClineCore`：使用 `cline.subscribe()` 获取 `CoreSessionEvent` 类型（文本流为 `"chunk"` 和 `payload.type === "text"`，结果文本为 `result.text`）。 `AgentRuntimeConfig` 上没有顶级 `onEvent` 字段 - 请改用 `agent.subscribe()` 或 `hooks.onEvent`。不要将 `"content_update"` 或 `"content_start"` 等事件类型与 `agent.subscribe()` 一起使用 - 这些是来自 ClineCore 适配器层的内部旧类型。

## 如何使用此技能

### 参考文件结构

两个主要的 API 表面（`Agent` 和 `ClineCore`）遵循 4 文件模式。横切概念是单文件指南。

`./references/<api>/` 中的每个主 API 表面包含：

|文件|目的|何时阅读|
|------|---------|--------------|
| `REFERENCE.md` |概述、何时使用、快速入门|始终先阅读|
| `api.md` |完整 API：类、方法、配置、类型|编写代码|
| `patterns.md` |常见模式、最佳实践|实施指导|
| `gotchas.md` |陷阱、限制、调试|故障排除|

`./references/<concept>/` 中的横切概念以 `REFERENCE.md` 作为切入点。

### 阅读顺序

1. 从 `REFERENCE.md` 开始为您选择的 API 表面
2. 然后阅读与您的任务相关的其他文件：
   - 写入 agent 代码 -> `api.md`
   - 常见模式 -> `patterns.md`
   - 创建工具 -> `tools/REFERENCE.md`
   - 添加 plugins/hooks -> `plugins/REFERENCE.md`
   - 配置 LLM 提供商 -> `providers/REFERENCE.md`
   - 流媒体事件 -> `events/REFERENCE.md`
   - 部署到生产 -> `production/REFERENCE.md`
   - 调度 agents -> `scheduling/REFERENCE.md`
   - 多agent编排 -> `multi-agent/REFERENCE.md`
   - 调试-> `gotchas.md`

### 示例路径

```
./references/agent/REFERENCE.md           # Start here for lightweight agents
./references/clinecore/REFERENCE.md       # Start here for full runtime
./references/agent/api.md                 # Agent class, config, methods
./references/tools/REFERENCE.md           # Creating and using tools
./references/plugins/REFERENCE.md         # Plugin system
./references/providers/REFERENCE.md       # LLM provider configuration
```

## 快速决策树

### “我应该使用哪个 API 表面？”

```
Which API?
+-- I want a simple, stateless agent with custom tools
|   +-- agent/ (Agent class from @cline/agents)
+-- I need session persistence, built-in tools, config discovery
|   +-- clinecore/ (ClineCore from @cline/core)
+-- I want built-in file/shell/search/web tools
|   +-- clinecore/ (has built-in tools; Agent does not)
+-- I want scheduled or recurring agents
|   +-- clinecore/ (automation API)
+-- I need multi-process or multi-client session sharing
|   +-- clinecore/ (hub-backed runtime)
+-- I'm building a browser-compatible agent
|   +-- agent/ (no Node.js dependencies)
```

### “我需要创造工具”

```
Tools?
+-- Define a custom tool with schema -> tools/REFERENCE.md
+-- Use built-in tools (bash, editor, read_files) -> tools/REFERENCE.md (built-in section)
+-- Control tool approval/policies -> tools/REFERENCE.md (policies section)
+-- Tool that ends the agent loop -> tools/REFERENCE.md (completion tools)
+-- Package tools as a reusable plugin -> plugins/REFERENCE.md
```

### “我需要处理事件”

```
Events?
+-- Stream text/reasoning in real time -> events/REFERENCE.md
+-- Track token usage and costs -> events/REFERENCE.md
+-- Watch tool calls -> events/REFERENCE.md
+-- Detect completion/errors -> events/REFERENCE.md
+-- Hook into lifecycle stages -> plugins/REFERENCE.md
```

### “我需要配置模型提供者”

```
Providers?
+-- Anthropic (Claude) -> providers/REFERENCE.md
+-- OpenAI (GPT) -> providers/REFERENCE.md
+-- Google (Gemini/Vertex) -> providers/REFERENCE.md
+-- AWS Bedrock -> providers/REFERENCE.md
+-- Mistral -> providers/REFERENCE.md
+-- OpenAI-compatible (vLLM, Together, etc.) -> providers/REFERENCE.md
+-- Custom/self-hosted provider -> providers/REFERENCE.md
```

### “我需要插件或挂钩”

```
Plugins?
+-- Package tools + hooks together -> plugins/REFERENCE.md
+-- Observe tool calls (logging, metrics) -> plugins/REFERENCE.md
+-- Intercept lifecycle events -> plugins/REFERENCE.md
+-- Add system prompt rules -> plugins/REFERENCE.md
+-- Distribute via npm/git -> plugins/REFERENCE.md
```

### “我需要多agent协调”

```
Multi-agent?
+-- Spawn one-off background agents -> multi-agent/REFERENCE.md (sub-agents)
+-- Persistent cross-session teams -> multi-agent/REFERENCE.md (teams)
+-- Parent-child delegation -> multi-agent/REFERENCE.md (sub-agents)
+-- Peer-to-peer task board -> multi-agent/REFERENCE.md (teams)
```

### “我需要调度或自动化”

```
Scheduling?
+-- Recurring cron jobs -> scheduling/REFERENCE.md
+-- One-off scheduled tasks -> scheduling/REFERENCE.md
+-- Event-driven triggers -> scheduling/REFERENCE.md
+-- CLI schedule management -> scheduling/REFERENCE.md
```

### “我需要去制作”

```
Production?
+-- Error handling and status checks -> production/REFERENCE.md
+-- Cost control and token limits -> production/REFERENCE.md
+-- Observability (OpenTelemetry) -> production/REFERENCE.md
+-- Security and sandboxing -> production/REFERENCE.md
+-- Deployment patterns -> production/REFERENCE.md
```

### 故障排除索引

- Agent 循环不停止 -> `tools/REFERENCE.md`（完成工具）
- 工具错误导致 agent -> `agent/gotchas.md` 或 `clinecore/gotchas.md` 崩溃
- 提供商身份验证失败 -> `providers/REFERENCE.md`
- 会话未持续 -> `clinecore/gotchas.md`
- 代币使用率过高 -> `production/REFERENCE.md`（成本控制）
- 集线器连接问题 -> `clinecore/gotchas.md`
- 插件未加载 -> `plugins/REFERENCE.md`
- 事件未触发 -> `events/REFERENCE.md`

## 产品索引

### API 表面
| API |条目文件|描述|
|-----|------------|-------------|
| Agent | `./references/agent/REFERENCE.md` |轻量级无状态 agent 循环|
| ClineCore | `./references/clinecore/REFERENCE.md` |具有会话、持久性、内置工具的完整运行时|

### 跨领域概念
|概念|条目文件|描述|
|---------|------------|-------------|
|工具| `./references/tools/REFERENCE.md` |内置和自定义工具创建|
|插件| `./references/plugins/REFERENCE.md` |带挂钩的扩展系统|
|活动| `./references/events/REFERENCE.md` |实时流媒体事件|
|供应商| `./references/providers/REFERENCE.md` |LLM提供商配置|
|生产| `./references/production/REFERENCE.md` |部署、安全性、可观察性|
|调度| `./references/scheduling/REFERENCE.md` |Cron 作业和自动化|
|多Agent| `./references/multi-agent/REFERENCE.md` |团队和子agents|

### 套餐地图
|套餐|目的|
|---------|---------|
| `@cline/sdk` |你需要的一切，安装这个|
| `@cline/core` |会话、持久性、内置工具、配置、集线器|
| `@cline/agents` |无状态 agent 循环、工具编排、流式传输|
| `@cline/llms` |LLM 提供商网关|
| `@cline/shared` |类型、工具助手、钩子引擎|

## 资源

存储库：https://github.com/cline/cline
SDK 来源：https://github.com/cline/cline/tree/main/sdk
文档：https://docs.cline.bot/sdk/overview
不和谐：https://discord.gg/cline


## 原文对照

<details>
<summary>展开完整原文</summary>

# Cline SDK Skill

Consolidated skill for building AI agents with the Cline SDK. Use the decision trees below to find the right entry point and API surface, then load detailed references.

## Critical Rules

Follow these rules in all Cline SDK code:

1. Install with `npm install @cline/sdk`. The `@cline/sdk` package re-exports everything from `@cline/core`, `@cline/agents`, `@cline/llms`, and `@cline/shared`.
2. Requires Node.js 22 or later.
3. Use `createTool()` from `@cline/sdk` (or `@cline/shared`) to define tools. Tool names must be `snake_case`.
4. Return errors as structured data from tool `execute` functions. Throwing counts as a "mistake" against the agent's mistake limit.
5. Use `lifecycle: { completesRun: true }` on tools that should end the agent loop (e.g. a "submit answer" tool).
6. When using `ClineCore`, always call `dispose()` when done to clean up resources.
7. The standalone `Agent` and `ClineCore` have different event systems. For `Agent`: use `agent.subscribe()` to get `AgentRuntimeEvent` types (text streaming is `"assistant-text-delta"`, result text is `result.outputText`). For `ClineCore`: use `cline.subscribe()` to get `CoreSessionEvent` types (text streaming is `"chunk"` with `payload.type === "text"`, result text is `result.text`). There is no top-level `onEvent` field on `AgentRuntimeConfig` -- use `agent.subscribe()` or `hooks.onEvent` instead. Do not use event types like `"content_update"` or `"content_start"` with `agent.subscribe()` -- those are internal legacy types from the ClineCore adapter layer.

## How to Use This Skill

### Reference File Structure

The two main API surfaces (`Agent` and `ClineCore`) follow a 4-file pattern. Cross-cutting concepts are single-file guides.

Each main API surface in `./references/<api>/` contains:

| File | Purpose | When to Read |
|------|---------|--------------|
| `REFERENCE.md` | Overview, when to use, quick start | Always read first |
| `api.md` | Full API: classes, methods, config, types | Writing code |
| `patterns.md` | Common patterns, best practices | Implementation guidance |
| `gotchas.md` | Pitfalls, limitations, debugging | Troubleshooting |

Cross-cutting concepts in `./references/<concept>/` have `REFERENCE.md` as the entry point.

### Reading Order

1. Start with `REFERENCE.md` for your chosen API surface
2. Then read additional files relevant to your task:
   - Writing agent code -> `api.md`
   - Common patterns -> `patterns.md`
   - Creating tools -> `tools/REFERENCE.md`
   - Adding plugins/hooks -> `plugins/REFERENCE.md`
   - Configuring LLM providers -> `providers/REFERENCE.md`
   - Streaming events -> `events/REFERENCE.md`
   - Deploying to production -> `production/REFERENCE.md`
   - Scheduling agents -> `scheduling/REFERENCE.md`
   - Multi-agent orchestration -> `multi-agent/REFERENCE.md`
   - Debugging -> `gotchas.md`

### Example Paths

```
./references/agent/REFERENCE.md           # Start here for lightweight agents
./references/clinecore/REFERENCE.md       # Start here for full runtime
./references/agent/api.md                 # Agent class, config, methods
./references/tools/REFERENCE.md           # Creating and using tools
./references/plugins/REFERENCE.md         # Plugin system
./references/providers/REFERENCE.md       # LLM provider configuration
```

## Quick Decision Trees

### "Which API surface should I use?"

```
Which API?
+-- I want a simple, stateless agent with custom tools
|   +-- agent/ (Agent class from @cline/agents)
+-- I need session persistence, built-in tools, config discovery
|   +-- clinecore/ (ClineCore from @cline/core)
+-- I want built-in file/shell/search/web tools
|   +-- clinecore/ (has built-in tools; Agent does not)
+-- I want scheduled or recurring agents
|   +-- clinecore/ (automation API)
+-- I need multi-process or multi-client session sharing
|   +-- clinecore/ (hub-backed runtime)
+-- I'm building a browser-compatible agent
|   +-- agent/ (no Node.js dependencies)
```

### "I need to create tools"

```
Tools?
+-- Define a custom tool with schema -> tools/REFERENCE.md
+-- Use built-in tools (bash, editor, read_files) -> tools/REFERENCE.md (built-in section)
+-- Control tool approval/policies -> tools/REFERENCE.md (policies section)
+-- Tool that ends the agent loop -> tools/REFERENCE.md (completion tools)
+-- Package tools as a reusable plugin -> plugins/REFERENCE.md
```

### "I need to handle events"

```
Events?
+-- Stream text/reasoning in real time -> events/REFERENCE.md
+-- Track token usage and costs -> events/REFERENCE.md
+-- Watch tool calls -> events/REFERENCE.md
+-- Detect completion/errors -> events/REFERENCE.md
+-- Hook into lifecycle stages -> plugins/REFERENCE.md
```

### "I need to configure a model provider"

```
Providers?
+-- Anthropic (Claude) -> providers/REFERENCE.md
+-- OpenAI (GPT) -> providers/REFERENCE.md
+-- Google (Gemini/Vertex) -> providers/REFERENCE.md
+-- AWS Bedrock -> providers/REFERENCE.md
+-- Mistral -> providers/REFERENCE.md
+-- OpenAI-compatible (vLLM, Together, etc.) -> providers/REFERENCE.md
+-- Custom/self-hosted provider -> providers/REFERENCE.md
```

### "I need plugins or hooks"

```
Plugins?
+-- Package tools + hooks together -> plugins/REFERENCE.md
+-- Observe tool calls (logging, metrics) -> plugins/REFERENCE.md
+-- Intercept lifecycle events -> plugins/REFERENCE.md
+-- Add system prompt rules -> plugins/REFERENCE.md
+-- Distribute via npm/git -> plugins/REFERENCE.md
```

### "I need multi-agent coordination"

```
Multi-agent?
+-- Spawn one-off background agents -> multi-agent/REFERENCE.md (sub-agents)
+-- Persistent cross-session teams -> multi-agent/REFERENCE.md (teams)
+-- Parent-child delegation -> multi-agent/REFERENCE.md (sub-agents)
+-- Peer-to-peer task board -> multi-agent/REFERENCE.md (teams)
```

### "I need scheduling or automation"

```
Scheduling?
+-- Recurring cron jobs -> scheduling/REFERENCE.md
+-- One-off scheduled tasks -> scheduling/REFERENCE.md
+-- Event-driven triggers -> scheduling/REFERENCE.md
+-- CLI schedule management -> scheduling/REFERENCE.md
```

### "I need to go to production"

```
Production?
+-- Error handling and status checks -> production/REFERENCE.md
+-- Cost control and token limits -> production/REFERENCE.md
+-- Observability (OpenTelemetry) -> production/REFERENCE.md
+-- Security and sandboxing -> production/REFERENCE.md
+-- Deployment patterns -> production/REFERENCE.md
```

### Troubleshooting Index

- Agent loop not stopping -> `tools/REFERENCE.md` (completion tools)
- Tool errors crashing the agent -> `agent/gotchas.md` or `clinecore/gotchas.md`
- Provider auth failures -> `providers/REFERENCE.md`
- Session not persisting -> `clinecore/gotchas.md`
- Token usage too high -> `production/REFERENCE.md` (cost control)
- Hub connection issues -> `clinecore/gotchas.md`
- Plugin not loading -> `plugins/REFERENCE.md`
- Events not firing -> `events/REFERENCE.md`

## Product Index

### API Surfaces
| API | Entry File | Description |
|-----|------------|-------------|
| Agent | `./references/agent/REFERENCE.md` | Lightweight stateless agent loop |
| ClineCore | `./references/clinecore/REFERENCE.md` | Full runtime with sessions, persistence, built-in tools |

### Cross-Cutting Concepts
| Concept | Entry File | Description |
|---------|------------|-------------|
| Tools | `./references/tools/REFERENCE.md` | Built-in and custom tool creation |
| Plugins | `./references/plugins/REFERENCE.md` | Extension system with hooks |
| Events | `./references/events/REFERENCE.md` | Real-time streaming events |
| Providers | `./references/providers/REFERENCE.md` | LLM provider configuration |
| Production | `./references/production/REFERENCE.md` | Deployment, security, observability |
| Scheduling | `./references/scheduling/REFERENCE.md` | Cron jobs and automation |
| Multi-Agent | `./references/multi-agent/REFERENCE.md` | Teams and sub-agents |

### Package Map
| Package | Purpose |
|---------|---------|
| `@cline/sdk` | Everything you need, install this one |
| `@cline/core` | Sessions, persistence, built-in tools, config, hub |
| `@cline/agents` | Stateless agent loop, tool orchestration, streaming |
| `@cline/llms` | LLM provider gateway |
| `@cline/shared` | Types, tool helpers, hook engine |

## Resources

Repository: https://github.com/cline/cline
SDK Source: https://github.com/cline/cline/tree/main/sdk
Documentation: https://docs.cline.bot/sdk/overview
Discord: https://discord.gg/cline


</details>
