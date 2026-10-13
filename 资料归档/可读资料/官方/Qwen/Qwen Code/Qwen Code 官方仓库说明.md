# Qwen Code 官方仓库说明

汇总安装、使用、能力、版本评测、生态、隐私及贡献入口，帮助了解产品和仓库状态。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-22](<https://github.com/QwenLM/qwen-code/commit/38711a9ae32bc5650693a118dca44bcea6ee6ffd>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 官方仓库说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

<div align="center">

[![npm版本](<https://img.shields.io/npm/v/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)
[![许可证](<https://img.shields.io/github/license/QwenLM/qwen-code.svg>)](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/LICENSE>)
[![Node.js版本](<https://img.shields.io/badge/node-%3E%3D22.0.0-brightgreen.svg>)](<https://nodejs.org/>)
[！[下载](<https://img.shields.io/npm/dm/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)

<a href="https://trendshift.io/repositories/15287" target="_blank">

![QwenLM%2Fqwen 代码 | Trendshift](<https://trendshift.io/api/badge/repositories/15287>)

</a>

**适用于您的终端、编辑器、桌面、浏览器和聊天的开源 AI 编码 agent。**

<a href="https://qwenlm.github.io/qwen-code-docs/zh/users/overview">中文</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/de/users/overview">德语</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/fr/users/overview">法语</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ja/users/overview">日本语</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ru/users/overview">Русский</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/pt-BR/users/overview">葡萄牙语（巴西）</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ko/users/overview">한국어</a>

</div>

![Qwen Code 在终端、桌面应用程序、浏览器、编辑器和聊天中，与 VS Code、Zed、JetBrains、Telegram、DingTalk、WeChat 和飞书集成 ](<https://img.alicdn.com/imgextra/i1/O1CN01DXWdF8BDUdD3XPhl_!!6000000001721-2-tps-1743-902.png>)

## 为什么是Qwen Code？

- **开箱即用的代理** — 自动记忆、自动技能、SubAgents、Agent 团队和 MCP。动态工作流程，零设置。
- **开源，从内到外** — 框架和 Qwen 模型都是开源的。他们一起进化。没有供应商锁定。
- **多协议** — 支持OpenAI、Anthropic、Gemini和Qwen API。任何第三方提供商或本地模型（Ollama / vLLM）。运行时切换。
- **超越终端** - IDE插件、桌面应用程序、Web UI、SDK 和聊天集成（Telegram / DingTalk / WeChat / Feishu）。

> [！TIP]
> Qwen Code 正在积极迭代自身 - 使用自己的 agent 和模型来提交问题、提交 PR、审查代码和运行测试。由社区提供支持，由 AI 驱动。

## 安装

**Linux / macOS:**

```bash
curl -fsSL https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.sh | bash
```

**Windows：**

```powershell
irm https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.ps1 | iex
```

> 安装完成后重启终端，确保环境变量生效。



<summary>NPM /自制</summary>

**NPM**（需要[Node.js 22+](<https://nodejs.org/>)）：

```bash
npm install -g @qwen-code/qwen-code@latest
```

**自制软件**（macOS / Linux）：

```bash
brew install qwen-code
```




## 快速入门

在项目中打开终端并启动 Qwen Code：

```bash
cd /path/to/your-project
qwen
```

在会话中，运行 `/auth` 以配置您的提供程序和 API 密钥。然后尝试：

```text
Explain this repository and show me where to start.
```

有关详细设置，请参阅[身份验证指南](<https://qwenlm.github.io/qwen-code-docs/en/users/configuration/auth/>)和[设置参考](<https://qwenlm.github.io/qwen-code-docs/en/users/configuration/settings/>)。



<summary>查看终端界面</summary>

![Qwen Code 终端接口](<https://img.alicdn.com/imgextra/i2/O1CN01K0nwj41RM1Il8kB0t_!!6000000002096-2-tps-1544-1060.png>)




## 如何使用Qwen Code

选择适合您工作流程的界面：

|接口|开始使用|
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|**终端**|在您的项目中运行 `qwen` — 请参阅 [快速入门 ](<#快速入门>)。|
|**台式机**|[下载适用于 macOS、Windows 和 Linux 的 Qwen Code 桌面 ](<https://github.com/QwenLM/qwen-code/releases/tag/desktop-latest>)。|
|**网页**|运行`qwen serve --open`，在浏览器中打开[Web UI](<https://qwenlm.github.io/qwen-code-docs/en/users/qwen-serve/>)。 _（实验）_|
|**编辑**|设置[VS Code](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-vscode/>)、[Zed](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-zed/>)或[JetBrains](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-jetbrains/>)。|
|**聊天**|连接[Telegram](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/telegram/>)、[DingTalk](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/dingtalk/>)、[WeChat](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/weixin/>)或[飞书](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/feishu/>)，然后运行`qwen channel start`。|

对于自动化和自定义集成：

- **Headless** — 在脚本、CI/CD 或批处理作业中运行 `qwen -p "..."`。
- **SDK** — 使用 [TypeScript](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-typescript/README.md>)、[Python](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-python/README.md>) 或 [Java](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-java/qwencode/README.md>) 构建。
- ** 守护进程 ** — 运行 `qwen serve` 以通过 HTTP + SSE (ACP) 连接客户端。 _（实验）_ [守护进程指南](<https://qwenlm.github.io/qwen-code-docs/en/users/qwen-serve/>)。



<summary>SDK 示例 (Python)</summary>

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main() -> None:
    result = query(
        "Summarize the repository layout.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    )

    async for message in result:
        if is_sdk_result_message(message):
            print(message["result"])


asyncio.run(main())
```




## 能力

如果您知道 Claude Code，那么您就已经知道 Qwen Code — 甚至更多。我们投入了大量精力 [，使 Qwen Code 与 Claude Code](<https://github.com/wenshao/codeagents/blob/main/docs/comparison/qwen-code-improvement-report.md>) 具有同等功能，全面提高广度和可靠性。

|特点| Qwen Code | Claude Code |
| ------------------------------------------------------------------ | :-------: | :---------: |
|SubAgents、Agent 团队、动态工作流程|     ✓     |      ✓      |
|自动记忆、自动技能、挂钩|     ✓     |      ✓      |
|内置技能（/review、/batch、/loop、/bugfix…）|     ✓     |      ✓      |
|MCP、计划模式、LSP集成|     ✓     |      ✓      |
|自动模式、沙箱、Git 工作树|     ✓     |      ✓      |
|计算机使用（桌面自动化）|     ✓     |      ✓      |
|IDE 插件 (VS Code / JetBrains / Zed)|     ✓     |      ✓      |
| SDK                                                                |     ✓     |      ✓      |
|无头模式、会话管理|     ✓     |      ✓      |
|开源——模型和框架|     ✓     |      —      |
|多协议（OpenAI / Anthropic / Gemini / Qwen + 任何提供商）|     ✓     |      —      |
|Agent Arena（同一任务的多模型正面交锋）|     ✓     |      —      |
|守护进程模式 — `qwen serve`（多客户端共享 agent）|     ✓     |      —      |
|IM频道（电报/DingTalk/WeChat/飞书）|     ✓     |      —      |

## Qwen Code评价

### 评估配置

|配置|价值|
| ----------------------- | --------------------------------------------------------------------------------------------------- |
|数据集|`princeton-nlp/SWE-bench_Verified`、500 案例|
|跑步|每个版本 3 个试用，每个版本 1,500 个作业； 7 Qwen Code 版本|
|型号| `Qwen 3.7 Max`                                                                                      |
|取样| `temperature=1`, `top_p=0.95`                                                                       |
|思考/推理| `interleaved_thinking=true`, `thinking_type=adaptive`, `reasoning_effort=high`                      |
|上下文/输出限制| `context_1m=true`, `model_context_length=1,000,000`, `max_tokens=64,000`, `model_max_tokens=65,535` |
|安全带限制| `max_iterations=500`, `runtime_timeout_sec=7,200`, `agent_timeout_multiplier=3.0`                   |

### 按 Qwen Code 版本评分

|版本|平均分|通过率|通过@3|通过^3|
| ------- | ---------: | --------: | -----: | -----: |
| 0.0.14  | **77.80%** |     84.6% |  84.6% |  69.2% |
| 0.1.0   | **76.73%** |     84.6% |  84.6% |  68.0% |
| 0.5.0   | **76.60%** |     83.0% |  83.0% |  68.4% |
| 0.10.0  | **76.47%** |     84.4% |  84.4% |  67.4% |
| 0.15.0  | **77.67%** |     84.6% |  84.6% |  68.6% |
| 0.19.0  | **76.40%** |     85.0% |  85.0% |  65.8% |
| 0.22.0  | **77.33%** |     84.4% |  84.4% |  68.0% |

### 平均分数趋势

![Qwen Code版本平均得分趋势](<https://raw.githubusercontent.com/QwenLM/qwen-code/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/.github/assets/qwen-code-avg-score-trend.svg>)

## 生态系统

- [**Qwen Code 桌面 **](<https://github.com/QwenLM/qwen-code/releases/tag/desktop-latest>) — 适用于 macOS、Windows 和 Linux 的官方桌面应用程序
- [**AionUi**](<https://github.com/iOfficeAI/AionUi>) — 用于命令行 AI 工具的现代 GUI，包括 Qwen Code
- [**Gemini CLI 桌面版 **](<https://github.com/Piebald-AI/gemini-cli-desktop>) — 适用于 Qwen Code 的跨平台 desktop/web/mobile UI

- [**🦞 Qwen Code Claw**](<https://github.com/openclaw/acpx>) — 让其他agents（Claude、Codex等）通过ACP将编码任务委托给Qwen Code。将此提示粘贴到您的 agent 中：

```text
Use Qwen Code as a coding agent. First, fetch and read this skill reference:
https://raw.githubusercontent.com/QwenLM/qwen-code/refs/heads/main/.qwen/skills/qwen-code-claw/SKILL.md
Then follow the instructions inside to install, authenticate, and use Qwen Code via acpx for all coding tasks.
```

- [**阿里云模型工作室 CLI**](<https://github.com/modelstudioai/cli>) — 阿里云 AI 平台 (`bailian-cli`) 的官方 CLI。通过 image/video 生成、知识检索、应用程序编排和模型部署扩展 Qwen Code

## 隐私

请参阅[服务条款和隐私声明](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/tos-privacy.md>)，包括[Chrome扩展程序和浏览器使用数据处理](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/tos-privacy.md#chrome-extension-and-browser-use>)。

## 贡献

欢迎贡献！请参阅 [CONTRIBUTING.md](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/CONTRIBUTING.md>) 以获取指南。

## 致谢

该项目最初基于[Google Gemini CLI](<https://github.com/google-gemini/gemini-cli>) v0.8.2。我们衷心感谢 Gemini CLI 团队的出色工作。从Qwen Code v0.1开始，我们停止与上游同步，并开始独立开发为多协议、多平台agent框架，并与Qwen模型及其他模型进行深度集成。


## 原文对照

<details>
<summary>展开完整原文</summary>

<div align="center">

[![npm version](<https://img.shields.io/npm/v/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)
[![License](<https://img.shields.io/github/license/QwenLM/qwen-code.svg>)](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/LICENSE>)
[![Node.js Version](<https://img.shields.io/badge/node-%3E%3D22.0.0-brightgreen.svg>)](<https://nodejs.org/>)
[![Downloads](<https://img.shields.io/npm/dm/@qwen-code/qwen-code.svg>)](<https://www.npmjs.com/package/@qwen-code/qwen-code>)

<a href="https://trendshift.io/repositories/15287" target="_blank">

![QwenLM%2Fqwen-code | Trendshift](<https://trendshift.io/api/badge/repositories/15287>)

</a>

**The open-source AI coding agent for your terminal, editor, desktop, browser, and chat.**

<a href="https://qwenlm.github.io/qwen-code-docs/zh/users/overview">中文</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/de/users/overview">Deutsch</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/fr/users/overview">français</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ja/users/overview">日本語</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ru/users/overview">Русский</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/pt-BR/users/overview">Português (Brasil)</a> |
<a href="https://qwenlm.github.io/qwen-code-docs/ko/users/overview">한국어</a>

</div>

![Qwen Code in the terminal, desktop app, browser, editor, and chat, with VS Code, Zed, JetBrains, Telegram, DingTalk, WeChat, and Feishu integrations](<https://img.alicdn.com/imgextra/i1/O1CN01DXWdF8BDUdD3XPhl_!!6000000001721-2-tps-1743-902.png>)

## Why Qwen Code?

- **Agentic out of the box** — Auto-Memory, Auto-Skills, SubAgents, Agent Teams, and MCP. Dynamic workflows, zero setup.
- **Open-source, inside and out** — The framework and the Qwen models are open-source. They evolve together. No vendor lock-in.
- **Multi-protocol** — Supports OpenAI, Anthropic, Gemini, and Qwen APIs. Any third-party provider or local model (Ollama / vLLM). Switch at runtime.
- **Beyond the terminal** — IDE plugins, Desktop app, Web UI, SDKs, and chat integrations (Telegram / DingTalk / WeChat / Feishu).

> [!TIP]
> Qwen Code is actively iterating on itself — using its own agent and models to file issues, submit PRs, review code, and run tests. Powered by the community, driven by AI.

## Installation

**Linux / macOS:**

```bash
curl -fsSL https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.sh | bash
```

**Windows:**

```powershell
irm https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen-standalone.ps1 | iex
```

> Restart your terminal after installation to ensure environment variables take effect.



<summary>NPM / Homebrew</summary>

**NPM** (requires [Node.js 22+](<https://nodejs.org/>)):

```bash
npm install -g @qwen-code/qwen-code@latest
```

**Homebrew** (macOS / Linux):

```bash
brew install qwen-code
```




## Quick Start

Open a terminal in your project and start Qwen Code:

```bash
cd /path/to/your-project
qwen
```

Inside the session, run `/auth` to configure your provider and API key. Then try:

```text
Explain this repository and show me where to start.
```

See the [Authentication Guide](<https://qwenlm.github.io/qwen-code-docs/en/users/configuration/auth/>) and [Settings Reference](<https://qwenlm.github.io/qwen-code-docs/en/users/configuration/settings/>) for detailed setup.



<summary>See the terminal interface</summary>

![Qwen Code terminal interface](<https://img.alicdn.com/imgextra/i2/O1CN01K0nwj41RM1Il8kB0t_!!6000000002096-2-tps-1544-1060.png>)




## How to Use Qwen Code

Choose the interface that fits your workflow:

| Interface    | Get started                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Terminal** | Run `qwen` in your project — see [Quick Start](<#quick-start>).                                                                                                                                                                                                                                                                                                                                             |
| **Desktop**  | [Download Qwen Code Desktop](<https://github.com/QwenLM/qwen-code/releases/tag/desktop-latest>) for macOS, Windows, and Linux.                                                                                                                                                                                                                                                                              |
| **Web**      | Run `qwen serve --open` to open the [Web UI](<https://qwenlm.github.io/qwen-code-docs/en/users/qwen-serve/>) in your browser. _(experimental)_                                                                                                                                                                                                                                                              |
| **Editor**   | Set up [VS Code](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-vscode/>), [Zed](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-zed/>), or [JetBrains](<https://qwenlm.github.io/qwen-code-docs/en/users/integration-jetbrains/>).                                                                                                                                                |
| **Chat**     | Connect [Telegram](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/telegram/>), [DingTalk](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/dingtalk/>), [WeChat](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/weixin/>), or [Feishu](<https://qwenlm.github.io/qwen-code-docs/en/users/features/channels/feishu/>), then run `qwen channel start`. |

For automation and custom integrations:

- **Headless** — Run `qwen -p "..."` in scripts, CI/CD, or batch jobs.
- **SDKs** — Build with [TypeScript](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-typescript/README.md>), [Python](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-python/README.md>), or [Java](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/packages/sdk-java/qwencode/README.md>).
- **Daemon** — Run `qwen serve` to connect clients over HTTP + SSE (ACP). _(experimental)_ [Daemon guide](<https://qwenlm.github.io/qwen-code-docs/en/users/qwen-serve/>).



<summary>SDK example (Python)</summary>

```python
import asyncio

from qwen_code_sdk import is_sdk_result_message, query


async def main() -> None:
    result = query(
        "Summarize the repository layout.",
        {
            "cwd": "/path/to/project",
            "path_to_qwen_executable": "qwen",
        },
    )

    async for message in result:
        if is_sdk_result_message(message):
            print(message["result"])


asyncio.run(main())
```




## Capabilities

If you know Claude Code, you already know Qwen Code — and then some. We've put significant effort into [bringing Qwen Code to feature parity with Claude Code](<https://github.com/wenshao/codeagents/blob/main/docs/comparison/qwen-code-improvement-report.md>), improving both breadth and reliability across the board.

| Feature                                                            | Qwen Code | Claude Code |
| ------------------------------------------------------------------ | :-------: | :---------: |
| SubAgents, Agent Teams, Dynamic Workflows                          |     ✓     |      ✓      |
| Auto-Memory, Auto-Skills, Hooks                                    |     ✓     |      ✓      |
| Built-in Skills (/review, /batch, /loop, /bugfix…)                 |     ✓     |      ✓      |
| MCP, Plan Mode, LSP Integration                                    |     ✓     |      ✓      |
| Auto Mode, Sandbox, Git Worktrees                                  |     ✓     |      ✓      |
| Computer Use (desktop automation)                                  |     ✓     |      ✓      |
| IDE Plugins (VS Code / JetBrains / Zed)                            |     ✓     |      ✓      |
| SDK                                                                |     ✓     |      ✓      |
| Headless Mode, Session Management                                  |     ✓     |      ✓      |
| Open-source — model and framework                                  |     ✓     |      —      |
| Multi-protocol (OpenAI / Anthropic / Gemini / Qwen + any provider) |     ✓     |      —      |
| Agent Arena (multi-model head-to-head on same task)                |     ✓     |      —      |
| Daemon Mode — `qwen serve` (multi-client shared agent)             |     ✓     |      —      |
| IM Channels (Telegram / DingTalk / WeChat / Feishu)                |     ✓     |      —      |

## Qwen Code Evaluation

### Evaluation Configuration

| Configuration           | Value                                                                                               |
| ----------------------- | --------------------------------------------------------------------------------------------------- |
| Dataset                 | `princeton-nlp/SWE-bench_Verified`, 500 cases                                                       |
| Runs                    | 3 trials per version, 1,500 jobs per version; 7 Qwen Code versions                                  |
| Model                   | `Qwen 3.7 Max`                                                                                      |
| Sampling                | `temperature=1`, `top_p=0.95`                                                                       |
| Thinking / Reasoning    | `interleaved_thinking=true`, `thinking_type=adaptive`, `reasoning_effort=high`                      |
| Context / Output Limits | `context_1m=true`, `model_context_length=1,000,000`, `max_tokens=64,000`, `model_max_tokens=65,535` |
| Harness Limits          | `max_iterations=500`, `runtime_timeout_sec=7,200`, `agent_timeout_multiplier=3.0`                   |

### Scores by Qwen Code Version

| Version |  Avg Score | Pass Rate | pass@3 | pass^3 |
| ------- | ---------: | --------: | -----: | -----: |
| 0.0.14  | **77.80%** |     84.6% |  84.6% |  69.2% |
| 0.1.0   | **76.73%** |     84.6% |  84.6% |  68.0% |
| 0.5.0   | **76.60%** |     83.0% |  83.0% |  68.4% |
| 0.10.0  | **76.47%** |     84.4% |  84.4% |  67.4% |
| 0.15.0  | **77.67%** |     84.6% |  84.6% |  68.6% |
| 0.19.0  | **76.40%** |     85.0% |  85.0% |  65.8% |
| 0.22.0  | **77.33%** |     84.4% |  84.4% |  68.0% |

### Avg Score Trend

![Qwen Code version Avg Score trend](<https://raw.githubusercontent.com/QwenLM/qwen-code/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/.github/assets/qwen-code-avg-score-trend.svg>)

## Ecosystem

- [**Qwen Code Desktop**](<https://github.com/QwenLM/qwen-code/releases/tag/desktop-latest>) — Official desktop app for macOS, Windows, and Linux
- [**AionUi**](<https://github.com/iOfficeAI/AionUi>) — A modern GUI for command-line AI tools including Qwen Code
- [**Gemini CLI Desktop**](<https://github.com/Piebald-AI/gemini-cli-desktop>) — A cross-platform desktop/web/mobile UI for Qwen Code

- [**🦞 Qwen Code Claw**](<https://github.com/openclaw/acpx>) — Let other agents (Claude, Codex, etc.) delegate coding tasks to Qwen Code via ACP. Paste this prompt into your agent:

```text
Use Qwen Code as a coding agent. First, fetch and read this skill reference:
https://raw.githubusercontent.com/QwenLM/qwen-code/refs/heads/main/.qwen/skills/qwen-code-claw/SKILL.md
Then follow the instructions inside to install, authenticate, and use Qwen Code via acpx for all coding tasks.
```

- [**Aliyun Model Studio CLI**](<https://github.com/modelstudioai/cli>) — Official CLI for Aliyun's AI platform (`bailian-cli`). Extends Qwen Code with image/video generation, knowledge retrieval, app orchestration, and model deployment

## Privacy

See the [Terms of Service and Privacy Notice](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/tos-privacy.md>), including [Chrome extension and Browser Use data handling](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/tos-privacy.md#chrome-extension-and-browser-use>).

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/CONTRIBUTING.md>) for guidelines.

## Acknowledgments

This project was originally based on [Google Gemini CLI](<https://github.com/google-gemini/gemini-cli>) v0.8.2. We gratefully acknowledge the Gemini CLI team's excellent work. Starting from Qwen Code v0.1, we stopped syncing with upstream and began independent development as a multi-protocol, multi-platform agent framework with deep integrations for Qwen models and beyond.


</details>
