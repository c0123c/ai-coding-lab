# Cline SDK 包层次与职责边界

SDK 架构参考。解释 core、agents、llms、shared 的依赖方向、安装和导出，区分浏览器兼容 Agent 循环与 Node 编排、工具和会话存储。

- 来源：Cline
- 资料类型：SDK 架构参考
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-05-12](<https://github.com/cline/cline/commit/0b0ca9b1fa95ed9f3e0891a34f1f9b3904309710>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：适用于使用或开发 Cline SDK 的工程。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/architecture/overview.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline SDK 包层次与职责边界.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Packages"
sidebarTitle: "Packages"
description: "How SDK packages fit together, what each package exports, and where boundaries are enforced."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

SDK 被分成分层包。依赖关系向下流动：`core` 依赖于 `agents`、`llms` 和 `shared`； `agents` 取决于 `llms` 和 `shared`； `llms` 取决于 `shared`。

## 包堆栈

```txt
Your application / CLI / VS Code / JetBrains
          │
          ▼
@cline/core
Sessions, storage, built-in tools, hub, automation, telemetry
          │
          ├── @cline/agents
          │   Browser-compatible AgentRuntime / Agent loop
          │
          ├── @cline/llms
          │   Provider handlers, gateway, model catalogs
          │
          └── @cline/shared
              Types, schemas, tools, hooks, extension contracts
```

## 套餐

### @cline/core

节点runtime/orchestration层。

主要出口产品包括：

|出口|描述|
|--------|-------------|
| `ClineCore` |主运行时入口点|
| `ClineCoreOptions` |构造函数选项|
| `ClineCoreStartInput` |会话开始输入|
| `CoreSessionConfig` |会话配置|
| `SessionRecord` |持久会话元数据|
| `AgentPlugin` |公共插件类型|
| `createTool` |从共享重新导出|

能力包括：

- local/hub/remote 运行时后端
- 会话清单和消息工件
- 内置工具
- 工具批准
- automation/scheduling服务
- 遥测挂钩
- plugin/extension 加载中
- team/sub-agent 工具

取决于：`@cline/shared`、`@cline/llms`、`@cline/agents`。

### @cline/agents

浏览器兼容的 agent 执行循环。

主要出口产品包括：

|出口|描述|
|--------|-------------|
| `AgentRuntime` |核心运行时类|
| `Agent` |`AgentRuntime` 的别名|
| `createAgentRuntime`, `createAgent` |工厂功能|
| `AgentRuntimeConfig` |构造函数配置联合|
| `AgentRunInput`, `AgentEventListener` |运行时帮助程序类型|
| `createTool` |从 `@cline/shared` 重新导出|

`AgentRuntime` 上的方法包括 `run`、`continue`、`abort`、`subscribe`、`restore` 和 `snapshot`。

取决于：`@cline/shared`、`@cline/llms`。

### @cline/llms

提供者和模型层。

主要出口产品包括：

|出口|描述|
|--------|-------------|
| `DefaultGateway`, `createGateway` |用于创建提供商支持的 agent 模型的网关|
| `createHandler`, `createHandlerAsync` |提供者处理程序工厂|
| `getAllProviders`, `getProviderIds`, `getModelsForProvider` |目录助手|
| `registerProvider`, `registerModel` |运行时注册表扩展|
| `ModelInfo`, `ProviderInfo` |Provider/model 元数据|

取决于：`@cline/shared`。

### @cline/shared

共享合同和公用事业的基础包。

主要出口产品包括：

|出口|描述|
|--------|-------------|
| `createTool` |创建类型化工具的助手|
| `AgentTool`, `AgentToolContext`, `ToolPolicy` |工具接口|
| `AgentEvent`, `AgentResult`, `AgentConfig` |面向主机的 agent 类型|
| `AgentRuntimeEvent`, `AgentRunResult` |面向运行时的 agent 类型|
| `HookEngine`, `HookStage`, `HookPolicies` |挂钩合约和引擎|
| `ContributionRegistry`, `AgentExtensionApi` |延期登记合同|
| `ModelInfo`, `Message`, `ContentBlock` |Model/message 类型|
| `BasicLogger`, `noopBasicLogger` |伐木合同|

没有更高层的依赖关系。

## 安装

```bash
npm install @cline/sdk
```

`@cline/sdk` 重新导出 `@cline/core` 中的所有内容。仅当需要较低级别控制时，直接安装`@cline/agents`或`@cline/llms`。

## 设计原则

### 严格依赖方向

依赖关系仅向下流动。较低层保持可嵌入性，无需拉动整个运行时间。

### 浏览器兼容的 agent 循环

`@cline/agents` 公开了一个与浏览器兼容的运行时。它不拥有会话存储、内置 file/shell 工具、集线器传输或特定于节点的编排。

### 核心作为编排层

`@cline/core` 拥有 Node 运行时集成：会话持久性、内置工具、自动化、hub/remote 传输、遥测和扩展加载。


## 原文对照

<details>
<summary>展开完整原文</summary>

The SDK is split into layered packages. Dependencies flow downward: `core` depends on `agents`, `llms`, and `shared`; `agents` depends on `llms` and `shared`; `llms` depends on `shared`.

## Package Stack

```txt
Your application / CLI / VS Code / JetBrains
          │
          ▼
@cline/core
Sessions, storage, built-in tools, hub, automation, telemetry
          │
          ├── @cline/agents
          │   Browser-compatible AgentRuntime / Agent loop
          │
          ├── @cline/llms
          │   Provider handlers, gateway, model catalogs
          │
          └── @cline/shared
              Types, schemas, tools, hooks, extension contracts
```

## Packages

### @cline/core

Node runtime/orchestration layer.

Key exports include:

| Export | Description |
|--------|-------------|
| `ClineCore` | Main runtime entry point |
| `ClineCoreOptions` | Constructor options |
| `ClineCoreStartInput` | Session start input |
| `CoreSessionConfig` | Session configuration |
| `SessionRecord` | Persisted session metadata |
| `AgentPlugin` | Public plugin type |
| `createTool` | Re-export from shared |

Capabilities include:

- local/hub/remote runtime backends
- session manifests and message artifacts
- built-in tools
- tool approvals
- automation/scheduling services
- telemetry hooks
- plugin/extension loading
- team/sub-agent tools

Depends on: `@cline/shared`, `@cline/llms`, `@cline/agents`.

### @cline/agents

Browser-compatible agent execution loop.

Key exports include:

| Export | Description |
|--------|-------------|
| `AgentRuntime` | Core runtime class |
| `Agent` | Alias for `AgentRuntime` |
| `createAgentRuntime`, `createAgent` | Factory functions |
| `AgentRuntimeConfig` | Constructor config union |
| `AgentRunInput`, `AgentEventListener` | Runtime helper types |
| `createTool` | Re-export from `@cline/shared` |

Methods on `AgentRuntime` include `run`, `continue`, `abort`, `subscribe`, `restore`, and `snapshot`.

Depends on: `@cline/shared`, `@cline/llms`.

### @cline/llms

Provider and model layer.

Key exports include:

| Export | Description |
|--------|-------------|
| `DefaultGateway`, `createGateway` | Gateway for creating provider-backed agent models |
| `createHandler`, `createHandlerAsync` | Provider handler factories |
| `getAllProviders`, `getProviderIds`, `getModelsForProvider` | Catalog helpers |
| `registerProvider`, `registerModel` | Runtime registry extension |
| `ModelInfo`, `ProviderInfo` | Provider/model metadata |

Depends on: `@cline/shared`.

### @cline/shared

Foundation package for shared contracts and utilities.

Key exports include:

| Export | Description |
|--------|-------------|
| `createTool` | Helper for creating typed tools |
| `AgentTool`, `AgentToolContext`, `ToolPolicy` | Tool interfaces |
| `AgentEvent`, `AgentResult`, `AgentConfig` | Host-facing agent types |
| `AgentRuntimeEvent`, `AgentRunResult` | Runtime-facing agent types |
| `HookEngine`, `HookStage`, `HookPolicies` | Hook contracts and engine |
| `ContributionRegistry`, `AgentExtensionApi` | Extension registration contracts |
| `ModelInfo`, `Message`, `ContentBlock` | Model/message types |
| `BasicLogger`, `noopBasicLogger` | Logging contracts |

No higher-layer dependencies.

## Install

```bash
npm install @cline/sdk
```

`@cline/sdk` re-exports everything from `@cline/core`. Install `@cline/agents` or `@cline/llms` directly only if you need lower-level control.

## Design Principles

### Strict dependency direction

Dependencies flow downward only. Lower layers stay embeddable without pulling in the full runtime.

### Browser-compatible agent loop

`@cline/agents` exposes a browser-compatible runtime. It does not own session storage, built-in file/shell tools, hub transports, or Node-specific orchestration.

### Core as orchestration layer

`@cline/core` owns Node runtime integration: session persistence, built-in tools, automation, hub/remote transports, telemetry, and extension loading.


</details>
