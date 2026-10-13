# 通过 Gemini CLI 执行 Shell 命令

介绍直接运行构建、Git 与系统命令，以及测试失败修复、后台进程和交互命令的管理方式。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-10](<https://geminicli.com/docs/cli/tutorials/shell-commands/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/tutorials/shell-commands/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/通过 Gemini CLI 执行 Shell 命令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 执行外壳命令

使用 CLI 运行构建、管理 git 并自动执行系统任务，而无需离开 
 对话。在本指南中，您将学习如何直接运行命令、
自动化复杂的工作流程以及安全地管理后台进程。

## 先决条件

- Gemini CLI已安装并经过身份验证。

- 基本熟悉系统的 shell（Bash、Zsh、PowerShell 等）。

## 如何直接运行命令（！）

有时您只需要检查文件大小或 git 状态，而不需要 AI
 为您做这件事。您可以使用 ! 将命令直接传递到 shell。前缀。

示例：!ls -la

这会立即执行 ls -la 并将输出打印到您的终端。
Gemini CLI 还会在当前会话
 上下文中记录命令及其输出，以便模型可以在后续提示中引用它。非常大的输出
可能会被截断。

### 场景：进入Shell模式

如果您正在进行大量手动工作，请通过输入切换“Shell 模式”！并
按 Enter 。现在，您输入的所有内容都会发送到 shell，直到您退出
（通常通过按 Esc 或输入 exit ）。

## 如何自动化复杂的任务

您可以使用 Gemini CLI 和 shell 命令的组合来自动执行任务。

### 场景：运行测试并修复故障

您想要运行测试并修复任何故障。

提示：运行单元测试。如果失败，请分析错误并尝试修复代码。

工作流程：

- Gemini 调用 run_shell_command('npm test') 。

- 您会看到确认提示：允许命令“npm test”？ [y/N] 。

- 您按 y 。

- 测试运行。如果失败，Gemini 会读取错误输出。

- Gemini 使用 read_file 检查失败的测试。

- Gemini使用替换来修复该错误。

- Gemini 再次运行 npm 测试以验证修复。

该循环让 Gemini 自主工作。

## 如何管理后台进程

您可以要求 Gemini 启动长时间运行的任务，例如开发服务器或文件 
 观察程序。

提示：在后台启动React开发服务器。

Gemini 将运行命令（例如， npm run dev ）并将其分离。

### 场景：查看活动 shell

要查看后台运行的内容，请使用 /shells 命令。

命令：/shells

这将打开一个仪表板，您可以在其中查看日志或终止失控的进程。

## 如何处理交互式命令

Gemini CLI 尝试通过将输出流式传输给您来处理交互式命令（例如 git add -p 或 
 确认提示）。然而，对于高度
交互式工具（如 vim 或 top ），通常最好在单独的终端窗口中自己运行它们
或使用 !前缀。

## 安全特性

授予 AI 访问权限虽然功能强大，但存在风险。 Gemini CLI包括
几个安全层。

### 确认提示

默认情况下，agent 请求的每个 shell 命令都需要您的 
 明确批准。

- 允许一次：运行该命令一次。

- 始终允许：在会话的其余部分信任此特定命令。

- 拒绝：停止agent。

### 沙盒

为了获得最大的安全性，特别是在运行不受信任的代码或探索新的
项目时，我们强烈建议启用沙盒。这将在安全 Docker 容器内运行所有 shell
 命令。

启用沙箱：启动 CLI 时使用 --sandbox 标志：gemini --sandbox 。

## 后续步骤

- 了解沙箱以安全地运行破坏性的
命令。

- 有关配置选项，请参阅 Shell 工具参考
例如超时和工作目录。

- 探索任务规划以了解 shell 命令如何适应
更大的工作流程。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Execute shell commands

Use the CLI to run builds, manage git, and automate system tasks without leaving
the conversation. In this guide, you’ll learn how to run commands directly,
automate complex workflows, and manage background processes safely.

## Prerequisites

- Gemini CLI installed and authenticated.

- Basic familiarity with your system’s shell (Bash, Zsh, PowerShell, and so on).

## How to run commands directly ( ! )

Sometimes you just need to check a file size or git status without asking the AI
to do it for you. You can pass commands directly to your shell using the ! prefix.

Example: !ls -la

This executes ls -la immediately and prints the output to your terminal.
Gemini CLI also records the command and its output in the current session
context, so the model can reference it in follow-up prompts. Very large outputs
may be truncated.

### Scenario: Entering Shell mode

If you’re doing a lot of manual work, toggle “Shell Mode” by typing ! and
pressing Enter . Now, everything you type is sent to the shell until you exit
(usually by pressing Esc or typing exit ).

## How to automate complex tasks

You can automate tasks using a combination of Gemini CLI and shell commands.

### Scenario: Run tests and fix failures

You want to run tests and fix any failures.

Prompt: Run the unit tests. If any fail, analyze the error and try to fix the code.

Workflow:

- Gemini calls run_shell_command('npm test') .

- You see a confirmation prompt: Allow command 'npm test'? [y/N] .

- You press y .

- The tests run. If they fail, Gemini reads the error output.

- Gemini uses read_file to inspect the failing test.

- Gemini uses replace to fix the bug.

- Gemini runs npm test again to verify the fix.

This loop lets Gemini work autonomously.

## How to manage background processes

You can ask Gemini to start long-running tasks, like development servers or file
watchers.

Prompt: Start the React dev server in the background.

Gemini will run the command (for example, npm run dev ) and detach it.

### Scenario: Viewing active shells

To see what’s running in the background, use the /shells command.

Command: /shells

This opens a dashboard where you can view logs or kill runaway processes.

## How to handle interactive commands

Gemini CLI attempts to handle interactive commands (like git add -p or
confirmation prompts) by streaming the output to you. However, for highly
interactive tools (like vim or top ), it’s often better to run them yourself
in a separate terminal window or use the ! prefix.

## Safety features

Giving an AI access to your shell is powerful but risky. Gemini CLI includes
several safety layers.

### Confirmation prompts

By default, every shell command requested by the agent requires your
explicit approval.

- Allow once: Runs the command one time.

- Allow always: Trusts this specific command for the rest of the session.

- Deny: Stops the agent.

### Sandboxing

For maximum security, especially when running untrusted code or exploring new
projects, we strongly recommend enabling Sandboxing. This runs all shell
commands inside a secure Docker container.

Enable sandboxing: Use the --sandbox flag when starting the CLI: gemini --sandbox .

## Next steps

- Learn about Sandboxing to safely run destructive
commands.

- See the Shell tool reference for configuration options
like timeouts and working directories.

- Explore Task planning to see how shell commands fit into
larger workflows.


</details>
