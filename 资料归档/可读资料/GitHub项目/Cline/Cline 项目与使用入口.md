# Cline 项目与使用入口

概述 IDE、终端、桌面版和 SDK 的使用入口，以及计划与执行、代码修改、规则、工具扩展和多 Agent 等能力。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-09-17](<https://github.com/cline/cline/commit/48285afa652e97a548a3ab3a6c41376ccc152fbb>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/README.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 项目与使用入口.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

<p align="center">
  

![Cline](<https://raw.githubusercontent.com/cline/cline/f87705844954a86ac993bfc4e2c86402e86d1d60/assets/icons/icon.png>)

</p>

<h1 align="center">Cline</h1>

<p align="center">
您的IDE、终端和桌面中的开源编码agent。
</p>

<div align="center">

<div align="center">
<table>
<tbody>
<td align="center">
<a href="https://docs.cline.bot" target="_blank"><strong>文档</strong></a>
</td>
<td align="center">
<a href="https://discord.gg/cline" target="_blank"><strong>Discord</strong></a>
</td>
<td align="center">
<a href="https://www.reddit.com/r/cline/" target="_blank"><strong>r/cline</strong></a>
</td>
<td align="center">
<a href="https://github.com/cline/cline/discussions/categories/feature-requests?discussions_q=is%3Aopen+category%3A%22Feature+Requests%22+sort%3Atop" target="_blank"><strong>功能请求</strong></a>
</td>
<td align="center">
<a href="https://cline.bot/join-us" target="_blank"><strong>加入我们！</strong></a>
</td>
</tbody>
</table>
</div>

</div>

<br>

<div align="center">
<table>
<tr>
<td align="center" width="50%">

### CLI

在终端中运行 Cline。
交互式聊天或完全无头 
 用于 CI/CD 和脚本。

```
npm i -g cline
```

<a href="./apps/cli/README.md">了解更多</a>
<br><br>

</td>
<td align="center" width="50%">

### 桌面应用程序

Cline 作为 macOS 和 Windows 的本机应用程序。
在任何文件夹中运行 agent 会话，安排 
 例程，并管理模型、插件和 MCP 服务器。

<a href="https://cline.bot/desktop">下载macOS和Windows</a>
<br><br>

</td>
</tr>
<tr>
<td align="center" width="50%">

### VS Code 扩展

AI 编辑器中的编码助手。
创建文件、运行命令、浏览网页，
并使用经过人机交互批准的工具。

<a href="https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev">从VS市场安装</a>
<br><br>

</td>
<td align="center" width="50%">

### JetBrains 插件

IntelliJ、IDEA、
PyCharm、WebStorm、GoLand 以及 JetBrains 系列的其余部分 
 具有相同的 Cline 体验。

<a href="https://plugins.jetbrains.com/plugin/28247-cline">从JetBrains市场安装</a>
<br><br>

</td>
</tr>
</table>
</div>

<div align="center">
<table>
<tr>
<td align="center">

### SDK

构建您自己的 AI agents 和集成，由运行 CLI、桌面应用程序、VS Code 扩展和 JetBrains 插件的同一引擎提供支持。自定义工具、多 agent 团队、连接器、预定自动化等等。

```
npm install @cline/sdk
```

<a href="https://docs.cline.bot/cline-sdk/overview">文档</a>
<br><br>

</td>
</tr>
</table>
</div>

---

## 索引

|产品展示|描述|地点| CHANGELOG |
|---------|------------|--------------|--------------|
| **SDK** |Node.js 编程 agent API 和扩展导出。| [`sdk/`](<https://github.com/cline/cline/tree/main/sdk>) |[CHANGELOG.md](<https://github.com/cline/cline/blob/main/sdk/CHANGELOG.md>)|
| **CLI** |终端 UI、无头模式、shell 命令和 CLI- 特定流程。| [`apps/cli/`](<https://github.com/cline/cline/tree/main/apps/cli>) |[CHANGELOG.md](<https://github.com/cline/cline/blob/main/apps/cli/CHANGELOG.md>)|
|**VS Code 分机**|Marketplace 扩展和扩展主机集成。|[`/`](<https://github.com/cline/cline/tree/main>)（WIP 正在迁移）|[CHANGELOG.md](<https://github.com/cline/cline/blob/main/CHANGELOG.md>)|
|**桌面应用程序**|本机 macOS 和 Windows 应用程序（Tauri shell、Bun sidecar、Next.js UI）。| [`apps/examples/desktop-app/`](<https://github.com/cline/cline/tree/main/apps/examples/desktop-app>) |[CHANGELOG.md](<https://github.com/cline/cline/blob/main/apps/examples/desktop-app/CHANGELOG.md>)|
|**JetBrains 插件**|JetBrains 托管客户端，与共享 agent 核心对话。|目前我们没有开源 JetBrains 插件| - |
|**文档站点**|公共文档页面。| [`docs/`](<https://docs.cline.bot/>) | - |

## 编辑项目中的代码

Cline 读取您的项目结构，了解文件之间的关系，并在您的代码库中进行协调更改。它在工作时监视 linter 和编译器错误，在您看到丢失导入、类型不匹配和语法错误等问题之前修复它们。在 VS Code 和 JetBrains 中，每个编辑都显示为您可以查看、修改或恢复的差异。所有更改都通过检查点进行跟踪，因此您可以轻松撤消 agent 的工作。

## 运行 Bash 命令

Cline 直接在终端中执行命令并实时观察输出。安装包、运行构建脚本、执行测试、部署应用程序、管理数据库。对于开发服务器等长时间运行的进程，Cline 继续在后台工作，并对出现的新输出做出反应，捕获发生的编译错误、测试失败和服务器崩溃。

## 计划与行动

在计划模式和行动模式之间切换。在计划模式下，Cline 探索您的代码库，提出澄清问题，并制定策略。对齐后，切换到行动模式，Cline 就会执行计划。每个文件编辑和终端命令都需要您的批准，因此您可以控制实际更改的内容。或者切换自动批准并让 Cline 自主运行。

## 规则与技巧

在 `.clinerules` 文件中定义项目特定的规则，指导 Cline 在代码库中的工作方式：编码标准、架构约定、部署过程、测试要求。 CLI、VS Code 扩展和 JetBrains 插件自动选取规则。使用技能让模型在需要时加载特定规则。

## 适用于所有型号

Cline 未锁定到单个 AI 提供商。使用适合您的工作流程的模型：

|提供者|型号|
|----------|--------|
| Anthropic |Claude 作品、十四行诗、俳句|
| OpenAI |GPT系列型号|
| Google |Gemini系列型号|
| OpenRouter |200+ 来自任何提供商的模型|
|Vercel AI 网关|通过一个网关路由到多个提供商|
|AWS 基岩|Claude、骆驼等等|
|天蓝色 / GCP 顶点|所有托管模型|
|大脑 / Groq|快速推理模型|
|奥拉玛 / LM 工作室|在您的计算机上运行本地模型|
|任何 OpenAI 兼容 API|自托管或第三方端点|

## 使用插件或 MCP 服务器扩展

通过插件扩展 Cline 的功能。使用 SDK，通过插件系统以编程方式注册工具和生命周期挂钩，以进行日志记录、审核、策略执行或添加特定于域的功能。下面是简单的插件示例。

```typescript
import { Agent, createTool } from "@cline/sdk"

const deployTool = createTool({
  name: "deploy",
  description: "Deploy the current branch to staging.",
  inputSchema: { type: "object", properties: { env: { type: "string" } }, required: ["env"] },
  execute: async (input) => {
    // your deployment logic
  },
})

const agent = new Agent({ tools: [deployTool], /* ... */ })
```
...或使用 [MCP](<https://github.com/modelcontextprotocol>) 连接到数据库、查询 API、管理云基础设施以及与外部系统交互。使用 [ 社区构建的服务器 ](<https://github.com/modelcontextprotocol/servers>) 或要求 Cline 即时创建自定义工具。在CLI中，使用`cline mcp`管理服务器。

## 多Agent团队

协调多个 agents 共同完成复杂的任务。协调员 agent 将工作分解为子任务并委托给专家 agents，每个子任务都有自己的工具和上下文。团队状态在会话中持续存在，因此您可以从上次中断的地方继续。

```bash
cline --team-name auth-sprint "Plan and implement user authentication with tests"
```

## 预定 Agents

在 cron 计划上运行 agents 以实现重复自动化。每日 PR 摘要、每周依赖性检查、代码库运行状况报告。计划在重新启动后仍然存在，并且独立于任何终端会话运行。

```bash
cline schedule create "PR summary" \
  --cron "0 9 * * MON-FRI" \
  --prompt "List all open PRs and their review status" \
  --workspace /path/to/repo
```

## 连接到 Slack、Telegram、Discord 等

从任何消息平台与您的 agent 聊天：Telegram、Slack、Discord、Google 聊天、WhatsApp 和 Linear。每个对话线程都映射到具有完整上下文的 agent 会话。设置访问控制以限制谁可以与您的 agent 交互。

```bash
# Connect to Telegram
cline connect telegram -k $BOT_TOKEN
# Connect to Slack through webhook
cline connect slack --bot-token $SLACK_TOKEN --signing-secret $SECRET --base-url $URL
# Connect to Slack using socket mode
cline connect slack --bot-token $SLACK_TOKEN --app-token $SLACK_APP_TOKEN
```

## 无头 CLI 适用于 CI/CD

以零交互的方式运行 Cline，以实现脚本编写和自动化。管道输入，得到JSON输出，链接命令，集成到CI/CD管道中。

```bash
cline "Run tests and fix any failures"
git diff origin/main | cline "Review these changes for issues"
cline --json "List all TODO comments" | jq -r 'select(.type == "agent_event" and .event.text) | .event.text'
```

## 贡献

从 [ 贡献指南 ](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/CONTRIBUTING.md>) 开始。加入我们的 [Discord](<https://discord.gg/cline>) 并前往 `#contributors` 频道与其他贡献者联系。请查看我们的 [ 职业页面 ](<https://cline.bot/join-us>) 了解全职职位。

## 许可证

[Apache 2.0 © 2026 Cline Bot Inc.](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/LICENSE>)


## 原文对照

<details>
<summary>展开完整原文</summary>

<p align="center">
  

![Cline](<https://raw.githubusercontent.com/cline/cline/f87705844954a86ac993bfc4e2c86402e86d1d60/assets/icons/icon.png>)

</p>

<h1 align="center">Cline</h1>

<p align="center">
The open source coding agent in your IDE, terminal, & desktop.
</p>

<div align="center">

<div align="center">
<table>
<tbody>
<td align="center">
<a href="https://docs.cline.bot" target="_blank"><strong>Docs</strong></a>
</td>
<td align="center">
<a href="https://discord.gg/cline" target="_blank"><strong>Discord</strong></a>
</td>
<td align="center">
<a href="https://www.reddit.com/r/cline/" target="_blank"><strong>r/cline</strong></a>
</td>
<td align="center">
<a href="https://github.com/cline/cline/discussions/categories/feature-requests?discussions_q=is%3Aopen+category%3A%22Feature+Requests%22+sort%3Atop" target="_blank"><strong>Feature Requests</strong></a>
</td>
<td align="center">
<a href="https://cline.bot/join-us" target="_blank"><strong>Join us!</strong></a>
</td>
</tbody>
</table>
</div>

</div>

<br>

<div align="center">
<table>
<tr>
<td align="center" width="50%">

### CLI

Run Cline in your terminal.
Interactive chat or fully headless 
for CI/CD and scripting.

```
npm i -g cline
```

<a href="./apps/cli/README.md">Learn more</a>
<br><br>

</td>
<td align="center" width="50%">

### Desktop App

Cline as a native app for macOS and Windows.
Run agent sessions in any folder, schedule
routines, and manage models, plugins, and MCP servers.

<a href="https://cline.bot/desktop">Download for macOS and Windows</a>
<br><br>

</td>
</tr>
<tr>
<td align="center" width="50%">

### VS Code Extension

AI coding assistant in your editor.
Create files, run commands, browse the web,
and use tools with human-in-the-loop approval.

<a href="https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev">Install from VS Marketplace</a>
<br><br>

</td>
<td align="center" width="50%">

### JetBrains Plugin

The same Cline experience in IntelliJ IDEA,
PyCharm, WebStorm, GoLand, and the rest of
the JetBrains family.

<a href="https://plugins.jetbrains.com/plugin/28247-cline">Install from JetBrains Marketplace</a>
<br><br>

</td>
</tr>
</table>
</div>

<div align="center">
<table>
<tr>
<td align="center">

### SDK

Build your own AI agents and integrations powered by the same engine that runs the CLI, desktop app, VS Code extension, and JetBrains plugin. Custom tools, multi-agent teams, connectors, scheduled automations, and more.

```
npm install @cline/sdk
```

<a href="https://docs.cline.bot/cline-sdk/overview">Documentation</a>
<br><br>

</td>
</tr>
</table>
</div>

---

## Index

| Product | Description | Location | CHANGELOG |
|---------|------------|--------------|--------------|
| **SDK** | Node.js programmatic agent API and extension exports. | [`sdk/`](<https://github.com/cline/cline/tree/main/sdk>) | [CHANGELOG.md](<https://github.com/cline/cline/blob/main/sdk/CHANGELOG.md>) |
| **CLI** | Terminal UI, headless mode, shell commands, and CLI-specific flows. | [`apps/cli/`](<https://github.com/cline/cline/tree/main/apps/cli>) | [CHANGELOG.md](<https://github.com/cline/cline/blob/main/apps/cli/CHANGELOG.md>) |
| **VS Code Extension** | The Marketplace extension and extension host integration. | [`/`](<https://github.com/cline/cline/tree/main>) (WIP migrating) | [CHANGELOG.md](<https://github.com/cline/cline/blob/main/CHANGELOG.md>) |
| **Desktop App** | Native macOS and Windows app (Tauri shell, Bun sidecar, Next.js UI). | [`apps/examples/desktop-app/`](<https://github.com/cline/cline/tree/main/apps/examples/desktop-app>) | [CHANGELOG.md](<https://github.com/cline/cline/blob/main/apps/examples/desktop-app/CHANGELOG.md>) |
| **JetBrains Plugin** | JetBrains-hosted client that talks to the shared agent core. | Currently we are not open-sourcing JetBrains plugins | - |
| **Docs site** | Public documentation pages. | [`docs/`](<https://docs.cline.bot/>) | - |

## Edits Code Across Your Project

Cline reads your project structure, understands the relationships between files, and makes coordinated changes across your codebase. It monitors linter and compiler errors as it works, fixing issues like missing imports, type mismatches, and syntax errors before you even see them. In VS Code and JetBrains, every edit shows up as a diff you can review, modify, or revert. All changes are tracked with checkpoints, so you can easily undo the agent's work.

## Runs Bash Commands

Cline executes commands directly in your terminal and watches the output in real time. Install packages, run build scripts, execute tests, deploy applications, manage databases. For long-running processes like dev servers, Cline continues working in the background and reacts to new output as it appears, catching compile errors, test failures, and server crashes as they happen.

## Plan and Act

Toggle between Plan mode and Act mode. In Plan mode, Cline explores your codebase, asks clarifying questions, and lays out a strategy. Once you're aligned, switch to Act mode and Cline executes the plan. Every file edit and terminal command requires your approval, so you stay in control of what actually changes. Or toggle auto-approve and let Cline run autonomously.

## Rules and Skills

Define project-specific rules in `.clinerules` files that guide how Cline works in your codebase: coding standards, architecture conventions, deployment procedures, testing requirements. Rules are picked up automatically by the CLI, VS Code extension, and JetBrains plugin. Use skills to let the model load specific rules when needed.

## Works With Every Model

Cline is not locked to a single AI provider. Use whichever model fits your workflow:

| Provider | Models |
|----------|--------|
| Anthropic | Claude Opus, Sonnet, Haiku |
| OpenAI | GPT series models |
| Google | Gemini series models |
| OpenRouter | 200+ models from any provider |
| Vercel AI Gateway | Route to many providers through one gateway |
| AWS Bedrock | Claude, Llama, and more |
| Azure / GCP Vertex | All hosted models |
| Cerebras / Groq | Fast inference models |
| Ollama / LM Studio | Run local models on your machine |
| Any OpenAI-compatible API | Self-hosted or third-party endpoints |

## Extend With Plugins or MCP Servers

Extend Cline's capabilities with plugins. Using the SDK, register tools and lifecycle hooks programmatically through the plugin system for logging, auditing, policy enforcement, or adding domain-specific capabilities. Simple plugin example below.

```typescript
import { Agent, createTool } from "@cline/sdk"

const deployTool = createTool({
  name: "deploy",
  description: "Deploy the current branch to staging.",
  inputSchema: { type: "object", properties: { env: { type: "string" } }, required: ["env"] },
  execute: async (input) => {
    // your deployment logic
  },
})

const agent = new Agent({ tools: [deployTool], /* ... */ })
```
...or use [MCPs](<https://github.com/modelcontextprotocol>) to connect to databases, query APIs, manage cloud infrastructure, and interact with external systems. Use [community-built servers](<https://github.com/modelcontextprotocol/servers>) or ask Cline to create custom tools on the fly. In the CLI, manage servers with `cline mcp`.

## Multi-Agent Teams

Coordinate multiple agents working together on complex tasks. A coordinator agent breaks the work into subtasks and delegates to specialist agents, each with their own tools and context. Team state persists across sessions so you can pick up where you left off.

```bash
cline --team-name auth-sprint "Plan and implement user authentication with tests"
```

## Scheduled Agents

Run agents on cron schedules for recurring automations. Daily PR summaries, weekly dependency checks, codebase health reports. Schedules persist across restarts and run independently of any terminal session.

```bash
cline schedule create "PR summary" \
  --cron "0 9 * * MON-FRI" \
  --prompt "List all open PRs and their review status" \
  --workspace /path/to/repo
```

## Connect to Slack, Telegram, Discord, and More

Chat with your agent from any messaging platform: Telegram, Slack, Discord, Google Chat, WhatsApp, and Linear. Each conversation thread maps to an agent session with full context. Set up access control to restrict who can interact with your agent.

```bash
# Connect to Telegram
cline connect telegram -k $BOT_TOKEN
# Connect to Slack through webhook
cline connect slack --bot-token $SLACK_TOKEN --signing-secret $SECRET --base-url $URL
# Connect to Slack using socket mode
cline connect slack --bot-token $SLACK_TOKEN --app-token $SLACK_APP_TOKEN
```

## Headless CLI for CI/CD

Run Cline with zero interaction for scripting and automation. Pipe input, get JSON output, chain commands, integrate into CI/CD pipelines.

```bash
cline "Run tests and fix any failures"
git diff origin/main | cline "Review these changes for issues"
cline --json "List all TODO comments" | jq -r 'select(.type == "agent_event" and .event.text) | .event.text'
```

## Contributing

Start with the [Contributing Guide](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/CONTRIBUTING.md>). Join our [Discord](<https://discord.gg/cline>) and head to the `#contributors` channel to connect with other contributors. Check our [careers page](<https://cline.bot/join-us>) for full-time roles.

## License

[Apache 2.0 © 2026 Cline Bot Inc.](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/LICENSE>)


</details>
