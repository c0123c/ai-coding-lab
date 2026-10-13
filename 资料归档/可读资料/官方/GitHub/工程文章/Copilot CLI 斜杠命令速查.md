# Copilot CLI 斜杠命令速查

按会话、任务、工具与支持等场景整理命令，帮助在终端中管理开发工作和使用状态。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-01-21](<https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/>)；更新：[2026-05-13](<https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/a-cheat-sheet-to-slash-commands-in-github-copilot-cli/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/Copilot CLI 斜杠命令速查.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot CLI 中斜杠命令的备忘单

您是否曾经感觉自己在不同工具之间切换所花费的时间比编写代码的时间还要多？如果您在终端中茁壮成长，并且想要更快、更可预测的方式来运行测试、修复代码和管理上下文，[Copilot CLI](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli#model-usage>) 斜杠命令可以让您在不中断流程的情况下进行控制。您可以使用斜杠命令执行各种任务，例如配置 [ 要使用的 AI 模型 ](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli#model-usage>) 或设置 MCP 服务器，甚至在外部共享您的会话。斜线命令提供快速、可重复的操作，无需每次都创建新的提示。

TL;DR：在本文底部查看所有斜杠命令及其作用。 😉

## 什么是斜杠命令？

斜杠命令是一个简单的指令，如 `/clear` 或 `/session`，它准确地告诉 Copilot 你想要做什么。它们以 `/` 为前缀，并立即触发 Copilot 来执行上下文感知操作。要开始使用斜杠命令，请打开 Copilot CLI 并输入 `/` 以查看可用命令的列表。

除了 Copilot CLI 之外，您还可以在 [Copilot 聊天和 agent 模式 ](<https://github.blog/developer-skills/github/how-to-use-github-copilot-in-your-ide-tips-tricks-and-best-practices/#13-slash-commands-for-common-tasks>) 中使用斜杠命令。

## 为什么要使用斜杠命令？

作为开发人员，我们希望工具能够在终端中快速运行。 Copilot CLI 中的斜杠命令就是这样做的。您无需为每个任务编写新的提示，而是直接在工作流程中使用快速、明确且可重复的命令。

在实践中，他们帮助：

* **速度和可预测性**：使用斜杠命令，Copilot的动作更加透明和可预测。与可以用不同方式解释的自然语言提示不同，斜杠命令始终触发相同的响应。这消除了猜测，因为你总是立即知道你会得到什么。
* **生产力**：在使用斜线命令之前，您可能已经复制并粘贴了代码，编写了长提示，或者在工具之间来回切换。现在，您可以直接从 CLI 清除错误、运行测试并获取代码说明，而无需离开终端。
* **清晰度和安全性**：`/add-dir`和`/list-dirs`等命令为文件访问提供了明确的边界并创建可审核的跟踪，这对于在敏感环境中工作的团队至关重要。这消除了幕后发生的事情的不确定性，降低了意外数据泄露的风险，并帮助团队在敏感环境中保持控制。
* **更好的可访问性**：斜线命令无缝地融入键盘驱动且可访问的工作流程中。 `/help` 等命令提供可用操作的即时概述，而 `/list-dirs` 或 `/list-files` 让用户无需导航复杂的界面即可进行浏览。这些命令使依赖键盘快捷键或辅助技术的用户能够快速发现和使用 Copilot 功能。
* **信任与合规性**：斜杠命令通过使每个Copilot操作明确且可追踪来增强信任。例如，团队可以使用 `/add-dir` 授予 Copilot 对特定目录的访问权限。这可确保敏感文件受到保护。使用 `/session` 或 `/usage` 等斜线命令，团队可以管理工具访问、监控活动并保持合规性。
* **自定义工作流程和可扩展性**：随着斜杠命令支持的扩展，您可以定制Copilot以处理您自己的任务和自动化。使用 `/delegate`、`/agent` 和 `/mcp` 等命令从 CLI 委托拉取请求、切换 agents 或连接到 CI/CD 管道。

将斜杠命令视为您已经执行的操作的显式快捷方式。您可以使用 Copilot CLI 做很多事情，斜杠命令使该过程变得更容易。

## 适用于日常工作流程的有用 Copilot CLI 斜线命令

以下是最常用的斜线命令，按您通常需要在工作流程中控制的内容进行分组：上下文、范围、配置和协作。

💡**提示**：如果只记住三个命令，请从`/clear`、`/cwd`和`/model`开始。这些使您可以立即控制上下文、范围和输出质量。

## 会话管理命令

### `/clear`：删除当前会话的通话记录。

Copilot 在您工作时积累上下文。当你有太多的继承上下文或者当你试图切换任务时，这种继承的上下文可能会使建议变得混乱。 `/clear` 可让您在处理多任务或在项目之间工作时快速擦除记录。

何时使用：

* 切换到新任务或存储库
* Copilot 回复引用旧文件或早期对话
* 您希望避免项目之间的上下文流失

### `/exit`、`/quit`：退出CLI。

命令 `/exit` 和 `/quit` 提供了结束会话并断开与 Copilot 的直接方法，确保资源清理和基于会话的工作的清晰边界。

何时使用：

* 结束您的会议
* 退出共享终端

### `/session`、`/usage`：显示有关当前 CLI 会话的会话使用指标。

这些命令可以让您了解 Copilot 在会话期间执行的操作，从而帮助进行审核、故障排除和资源跟踪。

何时使用：

* 审核 team/individual Copilot CLI 使用情况
* 在会话期间查看模型或工具的使用情况
* 调试运行或模型使用

当您运行 `/session` 或 `/usage` 命令时，Copilot 显示类似于以下内容的输出，显示有关会话的使用指标：

```
Session ID: 221b5571-3998-47e1-b57a-552cf9078947      
Started: 11/24/2025, 11:18:54 AM  
Last Modified: 11/24/2025, 11:18:54 AM      
Duration: 50s
Working Directory: /Users/jacklynlee31
      
Usage:        
Total usage est:       0 Premium requests   
Total duration (API):  0s         
Total duration (wall): 50s        
Total code changes:    0 lines added, 0 lines removed 
    
Hit Enter or Esc to continue
```

## 目录和文件访问命令

### `/add-dir`：允许Copilot访问目录。

通过限制 Copilot 对您选择的文件的访问权限，您可以确保响应与您当前的范围相关并提高安全性。

何时使用：

* 将 Copilot 范围限定到特定存储库或子目录
* 浏览包含敏感文件的大型代码库

```
/add-dir <directory>
```

例如，在这里我将 Documents 目录添加到文件访问的允许列表中：

```
/add-dir /Users/jacklynlee31/Documents
```

Copilot 然后给出以下输出：

```
Added directory to allowed list: /Users/jacklynlee31/Documents
```

### `/list-dirs`：显示允许的目录。

此命令有助于保持文件访问透明。这有助于团队合规政策。

何时使用：

* 验证 Copilot 的范围
* 解决访问问题
* 运行命令之前检查权限

```
/list-dirs
```

运行命令后，Copilot将显示目录列表。例如：

```
Allowed directories for file access:

   1. /Users/jacklynlee31
   2. /Users/jacklynlee31/Documents

   Total: 2 directories
```

### `/cwd`：显示或更改工作目录。

这使得 Copilot 专注于您正在积极工作的代码库部分。

何时使用：

* 浏览复杂的项目树
* 在存储库之间切换
* 缩小上下文以获得更好的建议

```
/cwd
```

例如，使用该命令后，Copilot 给出了以下输出：

```
Current working directory: /Users/jacklynlee31/Downloads
```

使用`/cwd [directory]`时，您可以切换到不同的目录：

```
/cwd /Users/jacklynlee31/Downloads
```

Copilot 将为您提供类似的输出以显示新的工作目录路径：

```
Changed working directory to: /Users/jacklynlee31/Downloads
```

## 配置命令

### `/model`：选择AI型号。

【Copilot】支持多种型号，但不用考虑太多。从默认模型开始，然后当您注意到速度、推理深度或成本方面的差异时进行实验。

何时使用：

* 比较输出
* 测试新模型或预览模型
* 排除意外响应问题

```
/model
```

运行命令后，Copilot将显示类似如下的交互式模型选择菜单：

```
Choose the AI model to use for Copilot CLI. The selected model will be persisted and used for future sessions.

 ❯ 1. Claude Sonnet 4.5 (1x) (default) (current)
   2. Claude Opus 4.5 (Preview) (1x)
   3. Claude Haiku 4.5 (0.33x)
   4. Claude Sonnet 4 (1x)
   5. GPT-5.1 (1x)
   6. GPT-5.1-Codex-Mini (0.33x)
   7. GPT-5.1-Codex (1x)
   8. GPT-5 (1x)
   9. GPT-5-Mini (0x)
   10. GPT-4.1 (0x)
   11. Gemini 3 Pro (Preview) (1x)
   12. Cancel (Esc)
```

您可以使用数字键或箭头键从列表中选择型号，然后按 Enter。您也可以使用【`/model [model]`】直接更改【AI】型号。

### `/theme [show|set|list]  [auto|dark|light]`：配置终端主题。

* `Show`：显示当前主题偏好。
* `Set`：用于设置终端主题为`auto`、`dark`或`light`。
* `List`：显示可用主题的列表。

何时使用：

* 提高可读性
* 匹配团队或环境标准

```
/theme set dark
```

设置主题后，Copilot会确认您的偏好并提示您重新启动CLI以应用新主题：

```
● Theme preference set to: dark

   The new theme will be applied on the next restart of the CLI.
```

### `/terminal-setup`：启用多线输入。

这对于复杂的指令或多步代码更改特别有帮助。此命令可确保您的终端准备好执行高级任务和协作工作流程。

何时使用：

* 写更长的提示
* 执行大型重构或审查
* 改进大型代码编辑期间的提示格式

```
/terminal-setup
```

### `/reset-allowed-tools`：重置工具权限。

此命令可帮助您快速将允许的工具集回滚到干净的状态，删除过时或有风险的项目。

何时使用：

* 团队或角色变更后
* 演示或实验后清理

```
/reset-allowed-tools
```

使用该命令后，Copilot将显示确认信息：

```
The list of allowed tools has been reset.
```

## 外部服务命令

### `/agent`：选择自定义agent。

自定义 agents 让您可以针对专门任务或集成。

何时使用：

* 通过repository/org/project切换agent配置
* 专业或第三方测试agents

### `/delegate <prompt>`：创建AI-生成的拉取请求。

这使您可以自动进行更改并创建拉取请求，而无需离开终端。

何时使用：

* 跨多个存储库应用更改
* 快速开始可审查的工作

例如，在这里我在存储库中生成了一个拉取请求以添加暗模式支持：

```
/delegate Add dark mode support
```

### `/share [file|gist] [path]`：导出您的会话。

文档非常重要，此命令可让您捕获整个会话历史记录以进行共享或存档。

何时使用：

* 异步切换
* 记录决策或实验
* 将上下文附加到问题或拉取请求

```
/share file /Users/jacklynlee31/Desktop
```

共享文件后，Copilot将确认会话已成功共享到您选择的位置：

```
● Session shared successfully to: /Users/jacklynlee31/Desktop/copilot-session-221b5571-3998-47e1-b57a-552cf9078947.md
```

### `/login`、`/logout`：登录或退出Copilot。

何时使用：

* 轮换证书
* 在共享设备上切换帐户

### `/mcp [show|add|edit|delete|disable|enable]`：管理MCP配置。

直接从终端管理 [MCP 服务器 ](<https://github.com/mcp>) 配置意味着您无需在工具或界面之间切换。

* `Show`：显示可用的MCP服务器列表。
* `Add`：添加新的MCP服务器。
* `Edit`：编辑现有的MCP服务器。
* `Delete`：删除MCP服务器。
* `Disable`：禁用MCP服务器。
* `Enable`：启用MCP服务器。

### `/user [show|list|switch]`：管理您正在使用的GitHub帐户。

多用户和企业开发通常意味着帐户之间的切换。 `/user` 可以帮助您完成基于角色的工作流程和测试。

何时使用：

* 多用户机器
* 管理服务帐户与个人帐户
* 在组织之间轮换

```
/user show
```

```
/user list
```

```
/user switch
```

### `/help`：显示所有可用命令。

何时使用：

* 发现新功能
* 使用CLI时的快速参考

## 将所有内容整合在一起

使用 Copilot CLI 中的斜线命令，您可以使常见的工作流程任务快速且可重复。您无需离开终端即可获得对上下文、范围和自动化的明确控制。

体验这一点的最佳方法是亲自尝试斜线命令。以 `/clear`、`/cwd` 和 `/help` 开头。然后随着工作流程的增长而分层。

随着斜线命令功能的不断增强，您的反馈可以帮助我们制定接下来的计划。使用 `/feedback` 分享哪些有效，哪些无效。

## 快速参考

|**斜线命令**|**它的作用**|**何时使用**|
| --- | --- | --- |
| `/clear` |清除会话 history/context|转移任务，重置Copilot的上下文，解决混乱|
| `/exit`, `/quit` |退出 Copilot 会话|完成会话，重置 CLI|
| `/session`, `/usage` |显示当前会话和使用统计信息|审核活动，监控 Copilot CLI 使用情况|
| `/add-dir <directory>` |添加允许的文件访问目录|限制范围，提高security/auditing|
| `/list-dirs` |列出Copilot可以访问的目录|确认或管理文件访问权限|
| `/cwd [directory]` |Changes/outputs 工作目录|导航项目，限制 Copilot 上下文|
| `/model [model]` |将 Copilot AI 型号更改为 CLI|实验、故障排除、优化模型行为|
| `/theme [show|set|list]` |管理终端输出主题|根据环境或团队标准进行定制|
| `/reset-allowed-tools` |重置允许的外部工具|删除工具权限，重置审核|
| `/agent` |选择自定义 Copilot agent|使用repo/org专用的agents时|
| `/delegate <prompt>` |将更改委托为远程存储库中的 PR|自动化更改、多存储库工作流程|
| `/share [file|gist]` |以降价形式分享会话或 GitHub 要点|文档会话、异步切换、团队共享|
| `/login`, `/logout` |在CLI中签署Copilot的in/out|更改用户、轮换凭据|
| `/mcp [show|add|edit|...]` |MCP服务器配置管理|更新 CI/CD 代理配置、企业设置|
| `/user [show|list|switch]` |GitHub用户管理|多用户或团队CLI管理|
| `/help` |列出所有 CLI 命令和快捷键|入职、可发现性|
| `/feedback` |提交有关 Copilot CLI 的反馈|与 GitHub 分享建议或错误报告|

尝试 GitHub CLI 中的 Copilot 斜线命令并加快您的工作流程。安装 [Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-slash-commands-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 或 [ 阅读文档 ](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli>) 开始。

### 其他资源

* [Copilot专题页面](<https://github.com/features/copilot>)
* [Copilot CLI](<https://github.com/features/copilot/cli>)
* [Copilot 聊天食谱](<https://docs.github.com/en/copilot/tutorials/copilot-chat-cookbook>)

---

## 标签：

* [GitHub CLI](<https://github.blog/tag/github-cli/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [斜杠命令](<https://github.blog/tag/slash-commands/>)

## 撰写者

![杰克琳·卡罗尔](<https://avatars.githubusercontent.com/u/13090072?v=4&s=200>)

### [杰克琳·卡罗尔](<https://github.blog/author/jacklynlee31/>)

[@jacklynlee31](<https://github.com/jacklynlee31>)

Jacklyn 是 GitHub 的内容作家，也是一位热爱讲好故事的技术作家。


## 原文对照

<details>
<summary>展开完整原文</summary>

# A cheat sheet to slash commands in GitHub Copilot CLI

Do you ever feel like you’re spending more time moving between different tools than you are writing code? If you thrive in the terminal and want faster, more predictable ways to run tests, fix code, and manage context, [Copilot CLI](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli#model-usage>) slash commands give you that control without breaking your flow. You can use slash commands to perform a variety of tasks like configuring [which AI model to use](<https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli#model-usage>) or setting up an MCP server, or even sharing your session externally. Slash commands offer fast, repeatable actions without needing to craft a new prompt each time.

TL;DR: See all the slash commands and what they do at the bottom of this post. 😉

## What are slash commands?

A slash command is a simple instruction, like `/clear` or `/session`, that tells Copilot exactly what you want to do. They are prefixed with a `/` and instantly trigger Copilot to carry out context-aware actions. To start using slash commands , open Copilot CLI and type `/` to see a list of available commands.

In addition to Copilot CLI, you can use slash commands across [Copilot Chat and with agent mode](<https://github.blog/developer-skills/github/how-to-use-github-copilot-in-your-ide-tips-tricks-and-best-practices/#13-slash-commands-for-common-tasks>), too.

## Why use slash commands?

As developers, we want tools that work fast in the terminal. Slash commands in Copilot CLI do just that. Instead of writing a new prompt for each task, you use quick, explicit, and repeatable commands directly in your workflow.

In practice, they help with:

* **Speed and predictability**: With slash commands, Copilot’s actions are more transparent and predictable. Unlike natural language prompts, which can be interpreted in different ways, slash commands always trigger the same response. This removes guesswork because you always know what you’re going to get, instantly.
* **Productivity**: Before slash commands, you might have copied and pasted code, written long prompts, or switched back and forth between tools. Now you can clean up errors, run tests, and get code explanations right from the CLI, without leaving your terminal.
* **Clarity and security**: Commands like `/add-dir` and `/list-dirs` give clear boundaries for file access and create an auditable trail, which is essential for teams working in sensitive environments. This eliminates uncertainty about what’s happening behind the scenes, reduces the risk of accidental data exposure, and helps teams maintain control in sensitive environments.
* **Better accessibility**: Slash commands fit seamlessly into keyboard-driven and accessible workflows. Commands like `/help` provide an instant overview of available actions, while `/list-dirs` or `/list-files` let users browse without navigating complex interfaces. These commands enable users who rely on keyboard shortcuts or assistive technologies to quickly discover and use Copilot features.
* **Trust and compliance**: Slash commands enhance trust by making every Copilot action explicit and traceable. For example, teams can use `/add-dir` to grant Copilot access to a specific directory. This ensures that sensitive files stay protected. With slash commands like `/session` or `/usage`, teams can manage tool access, monitor activity, and stay compliant.
* **Custom workflows and extensibility**: As support for slash commands expands, you can tailor Copilot to work with your own tasks and automations. Delegate pull requests, switch agents, or connect to CI/CD pipelines, all from the CLI, with commands like `/delegate`, `/agent`, and `/mcp`.

Think of slash commands as explicit shortcuts for things you already do. There’s a lot you can do with Copilot CLI, and slash commands make the process easier.

## Useful Copilot CLI slash commands for your everyday workflow

Below are the most commonly used slash commands, grouped by what you typically need to control in your workflows: context, scope, configuration, and collaboration.

💡 **Tip**: If you only remember three commands, start with `/clear`, `/cwd`, and `/model`. These give you immediate control over context, scope, and output quality.

## Session management commands

### `/clear`: Delete the current session’s conversation history.

Copilot accumulates context as you work. This inherited context can muddy suggestions when you have too much of it, or when you’re trying to switch tasks. `/clear` lets you quickly wipe the slate when you’re multitasking or working between projects.

When to use:

* Switching to a new task or repository
* Copilot responses are referencing old files or earlier conversations
* You want to avoid context bleed between projects

### `/exit`, `/quit`: Exit the CLI.

The commands `/exit` and `/quit` provide a direct way to end your session and disconnect from Copilot, ensuring resource cleanup and a clear boundary for session-based work.

When to use:

* Wrapping up your session
* Logging out of a shared terminal

### `/session`, `/usage`: Display session usage metrics about the current CLI session.

These commands give visibility into the actions Copilot has performed during your session, helping with audits, troubleshooting, and resource tracking.

When to use:

* Auditing team/individual Copilot CLI usage
* Reviewing model or tool usage during a session
* Debugging runs or model use

When you run either the `/session` or `/usage` commands, Copilot shows output similar to the following, displaying usage metrics about your session:

```
Session ID: 221b5571-3998-47e1-b57a-552cf9078947      
Started: 11/24/2025, 11:18:54 AM  
Last Modified: 11/24/2025, 11:18:54 AM      
Duration: 50s
Working Directory: /Users/jacklynlee31
      
Usage:        
Total usage est:       0 Premium requests   
Total duration (API):  0s         
Total duration (wall): 50s        
Total code changes:    0 lines added, 0 lines removed 
    
Hit Enter or Esc to continue
```

## Directory and file access commands

### `/add-dir`: Allow Copilot to access a directory.

By limiting Copilot’s access to the files you choose, you can ensure responses are relevant to your current scope and increase security.

When to use:

* Scoping Copilot to a specific repository or subdirectory
* Navigating large codebases with sensitive files

```
/add-dir <directory>
```

For example, here I am adding the Documents directory to the allowed list for file access:

```
/add-dir /Users/jacklynlee31/Documents
```

Copilot then gives me the following output:

```
Added directory to allowed list: /Users/jacklynlee31/Documents
```

### `/list-dirs`: Show allowed directories.

This command helps keep file access transparent. This can help with team compliance policies.

When to use:

* Verifying Copilot’s scope
* Troubleshooting access issues
* Reviewing permissions before running commands

```
/list-dirs
```

After running the command, Copilot will show you the list of directories. For example:

```
Allowed directories for file access:

   1. /Users/jacklynlee31
   2. /Users/jacklynlee31/Documents

   Total: 2 directories
```

### `/cwd`: Show or change the working directory.

This keeps Copilot focused on the part of your codebase you’re actively working in.

When to use:

* Navigating complex project trees
* Switching between repositories
* Narrowing context for better suggestions

```
/cwd
```

For example, after using the command, Copilot gave me the following output:

```
Current working directory: /Users/jacklynlee31/Downloads
```

When using `/cwd [directory]`, you are able to switch to a different directory:

```
/cwd /Users/jacklynlee31/Downloads
```

Copilot will give you a similar output to show the new working directory path:

```
Changed working directory to: /Users/jacklynlee31/Downloads
```

## Configuration commands

### `/model`: Select an AI model.

Copilot supports multiple models, but you don’t need to overthink it. Start with the default model, then experiment when you notice differences in speed, reasoning depth, or cost.

When to use:

* Comparing outputs
* Testing new or preview models
* Troubleshooting unexpected responses

```
/model
```

After running the command, Copilot will display an interactive model selection menu similar to the following:

```
Choose the AI model to use for Copilot CLI. The selected model will be persisted and used for future sessions.

 ❯ 1. Claude Sonnet 4.5 (1x) (default) (current)
   2. Claude Opus 4.5 (Preview) (1x)
   3. Claude Haiku 4.5 (0.33x)
   4. Claude Sonnet 4 (1x)
   5. GPT-5.1 (1x)
   6. GPT-5.1-Codex-Mini (0.33x)
   7. GPT-5.1-Codex (1x)
   8. GPT-5 (1x)
   9. GPT-5-Mini (0x)
   10. GPT-4.1 (0x)
   11. Gemini 3 Pro (Preview) (1x)
   12. Cancel (Esc)
```

You can select a model from the list with the number or arrow keys and press Enter. You can also use `/model [model]` to directly change the AI model.

### `/theme [show|set|list]  [auto|dark|light]`: Configure the terminal theme.

* `Show`: shows the current theme preference.
* `Set`: used to set the terminal theme to `auto`, `dark`, or `light`.
* `List`: shows a list of available themes.

When to use:

* Improving readability
* Matching team or environment standards

```
/theme set dark
```

After setting the theme, Copilot will confirm your preference and prompt you to restart the CLI to apply the new theme:

```
● Theme preference set to: dark

   The new theme will be applied on the next restart of the CLI.
```

### `/terminal-setup`: Enable multiline inputs.

This is especially helpful for complex instructions or multi step code changes. This command ensures your terminal is ready for advanced tasks and collaborative workflows.

When to use:

* Writing longer prompts
* Performing large refactors or reviews
* Improving prompt formatting during large code edits

```
/terminal-setup
```

### `/reset-allowed-tools`: Reset tool permissions.

This command helps you quickly roll back the allowed tools set to a clean slate, removing obsolete or risky items.

When to use:

* After team or role changes
* Cleaning up after demos or experiments

```
/reset-allowed-tools
```

After using the command, Copilot will show a confirmation:

```
The list of allowed tools has been reset.
```

## External services commands

### `/agent`: Select a custom agent.

Custom agents let you target specialized tasks or integrations.

When to use:

* Switching agent configurations by repository/org/project
* Testing specialized or third-party agents

### `/delegate <prompt>`: Create an AI-generated pull request.

This lets you automate changes and create pull requests without leaving the terminal.

When to use:

* Applying changes across multiple repositories
* Kicking off reviewable work quickly

For example, here I generated a pull request in my repository to add dark mode support:

```
/delegate Add dark mode support
```

### `/share [file|gist] [path]`: Export your session.

Documentation is critical—and this command lets you capture entire session histories to share or archive.

When to use:

* Async handoffs
* Documenting decisions or experiments
* Attaching context to issues or pull requests

```
/share file /Users/jacklynlee31/Desktop
```

After sharing the file, Copilot will confirm that the session was shared successfully to your chosen location:

```
● Session shared successfully to: /Users/jacklynlee31/Desktop/copilot-session-221b5571-3998-47e1-b57a-552cf9078947.md
```

### `/login`, `/logout`: Log in or out of Copilot.

When to use:

* Rotating credentials
* Switching accounts on a shared device

### `/mcp [show|add|edit|delete|disable|enable]`: Manage MCP configurations.

Managing [MCP server](<https://github.com/mcp>) configuration directly from the terminal means you don’t have to switch between tools or interfaces.

* `Show`: show the list of available MCP servers.
* `Add`: add a new MCP server.
* `Edit`: edit an existing MCP server.
* `Delete`: delete a MCP server.
* `Disable`: disable a MCP server.
* `Enable`: enable a MCP server.

### `/user [show|list|switch]`: Manage what GitHub account you’re using.

Multi-user and enterprise development often means switching between accounts. `/user` can help you with your role-based workflows and testing.

When to use:

* Multi-user machines
* Managing service accounts vs. personal accounts
* Rotating between organizations

```
/user show
```

```
/user list
```

```
/user switch
```

### `/help`: Show all available commands.

When to use:

* Discovering new features
* Quick reference while using the CLI

## Bringing it all together

With slash commands in Copilot CLI, you can make common workflow tasks fast and repeatable. You’re gaining explicit control over context, scope, and automation without leaving the terminal.

The best way to experience this is to dive in and try slash commands yourself. Start with `/clear`, `/cwd`, and `/help`. Then layer in others as your workflows grow.

As slash command capabilities grow, your feedback helps us shape what comes next. Use `/feedback` to share what’s working, and what isn’t.

## Quick reference

| **Slash command** | **What it does** | **When to use** |
| --- | --- | --- |
| `/clear` | Clears session history/context | Shift tasks, reset Copilot’s context, resolve confusion |
| `/exit`, `/quit` | Exits the Copilot session | Finish a session, reset the CLI |
| `/session`, `/usage` | Shows current session and usage stats | Audit activity, monitor Copilot CLI usage |
| `/add-dir <directory>` | Adds allowed directory for file access | Limit scope, improve security/auditing |
| `/list-dirs` | Lists directories Copilot can access | Confirm or manage file access permissions |
| `/cwd [directory]` | Changes/outputs the working directory | Navigate projects, limit Copilot context |
| `/model [model]` | Changes Copilot AI model for the CLI | Experiment, troubleshoot, optimize model behavior |
| `/theme [show|set|list]` | Manage terminal output theme | Customize for environment or team standards |
| `/reset-allowed-tools` | Resets allowed external tools | Remove tool permissions, reset for audits |
| `/agent` | Selects a custom Copilot agent | When using specialized agents by repo/org |
| `/delegate <prompt>` | Delegates changes as a PR in a remote repository | Automate changes, multi-repo workflows |
| `/share [file|gist]` | Shares session as markdown or GitHub Gist | Document sessions, async handoff, team sharing |
| `/login`, `/logout` | Sign in/out of Copilot in the CLI | Change user, rotate credentials |
| `/mcp [show|add|edit|...]` | MCP server configuration management | Update CI/CD proxy config, enterprise setups |
| `/user [show|list|switch]` | GitHub user management | Multi-user or team CLI management |
| `/help` | Lists all CLI commands and shortcuts | Onboarding, discoverability |
| `/feedback` | Submit feedback about Copilot CLI | Share suggestions or bug reports with GitHub |

Try Copilot slash commands in the GitHub CLI and speed up your workflow. Install [Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-slash-commands-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) or [read the docs](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli>) to get started.

### Additional resources

* [Copilot feature page](<https://github.com/features/copilot>)
* [Copilot CLI](<https://github.com/features/copilot/cli>)
* [Copilot Chat Cookbook](<https://docs.github.com/en/copilot/tutorials/copilot-chat-cookbook>)

---

## Tags:

* [GitHub CLI](<https://github.blog/tag/github-cli/>)
* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)
* [slash commands](<https://github.blog/tag/slash-commands/>)

## Written by

![Jacklyn Carroll](<https://avatars.githubusercontent.com/u/13090072?v=4&s=200>)

### [Jacklyn Carroll](<https://github.blog/author/jacklynlee31/>)

[@jacklynlee31](<https://github.com/jacklynlee31>)

Jacklyn is a Content Writer at GitHub and a technical writer with a love for good storytelling.


</details>
