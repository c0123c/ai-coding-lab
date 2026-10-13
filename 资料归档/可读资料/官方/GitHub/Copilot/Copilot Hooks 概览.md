# Copilot Hooks 概览

介绍在会话、提示和工具事件发生时执行命令的机制，说明规则文件与输入信息、事件类型、自动校验及性能和安全注意事项。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-15](<https://github.com/github/docs/commit/b32e08ff7345b996e5bb059bfd5bfc11d08bcc36>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/concepts/agents/hooks>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Hooks 概览.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 关于GitHub Copilot的挂钩

通过在 agent 执行期间的关键点执行自定义 shell 命令来扩展和自定义 GitHub Copilot agent 行为。

## 什么是钩子？

挂钩是一种在 agent 工作流程中的战略点执行自定义 shell 命令的方法，例如当 agent 会话开始或结束时、输入提示时或调用工具时。

挂钩通过 JSON 输入接收有关 agent 操作的详细信息，从而实现上下文感知自动化。例如，您可以使用钩子来：

* 以编程方式批准或拒绝工具执行。
* 使用秘密扫描等内置安全功能来防止凭据泄露。
* 实施自定义验证规则和审核日志记录以确保合规性。

挂钩可用于：

* **Copilot 云 agent** 在 GitHub 上。
* **GitHub Copilot CLI** 在您的终端中。

您在 JSON 文件中定义挂钩，这些文件存储在存储库的 `.github/hooks/*.json` 中。每当在存储库中使用 Copilot agents 时，这些都适用。 Copilot CLI 还支持您存储在主目录 `~/.copilot/hooks/*.json` 中的个人挂钩。每当您使用 Copilot CLI 时，这些都适用。

## 钩子的类型

可以使用以下类型的挂钩：

* **sessionStart**：当新的agent会话开始或恢复现有会话时执行。可用于初始化环境、记录会话启动以进行审核、验证项目状态以及设置临时资源。
* **sessionEnd**：agent会话完成或终止时执行。可用于清理临时资源、生成和存档会话报告和日志，或发送有关会话完成的通知。
* **userPromptSubmitted**：当用户向agent提交提示时执行。可用于记录用户请求以进行审核和使用情况分析。
* **preToolUse**：在agent使用任何工具（例如`bash`、`edit`、`view`）之前执行。这是最强大的挂钩，因为它可以**批准或拒绝工具执行**。使用此挂钩来阻止危险命令、强制执行安全策略和编码标准、要求敏感操作获得批准或记录工具使用情况以确保合规性。
* **postToolUse**：工具执行完成后（无论成功还是失败）执行。可用于记录执行结果、跟踪使用统计信息、生成审计跟踪、监控性能指标以及发送故障警报。
* **agentStop**：当主agent完成对提示的响应时执行。
* **subagentStop**：子代理完成时执行，然后将结果返回给父代理 agent。
* **errorOccurred**：agent执行期间发生错误时执行。可用于记录错误以进行调试、发送通知、跟踪错误模式以及生成报告。

要查看钩子类型的完整参考以及示例用例、最佳实践和高级模式，请参阅 [GitHub Copilot 钩子参考 ](<https://docs.github.com/en/copilot/reference/hooks-reference>)。

## 钩子配置格式

您可以使用特殊的 JSON 格式配置挂钩。 JSON 必须包含值为 `1` 的 `version` 字段和包含钩子定义数组的 `hooks` 对象。

```json copy
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "command",
        "bash": "string (optional)",
        "powershell": "string (optional)",
        "cwd": "string (optional)",
        "env": { "KEY": "value" },
        "timeoutSec": 30
      }
    ]
  }
}
```

钩子对象可以包含以下键：

|财产|必填|描述|
| ------------ | --------------------- | ------------------------------------------------------------------------------ |
| `type`       |是的|必须是 `"command"`|
| `bash`       |是（在 Unix 系统上）|要执行的 bash 脚本的路径|
| `powershell` |是（在 Windows 上）|要执行的 PowerShell 脚本的路径|
| `cwd`        |否|脚本的工作目录（相对于存储库根目录）|
| `env`        |否|与现有环境合并的其他环境变量|
| `timeoutSec` |否|最大执行时间（以秒为单位）（默认值：30）|

## 挂钩配置文件示例

这是一个示例配置文件，位于存储库的 `~/.github/hooks/project-hooks.json` 中。

```json copy
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "command",
        "bash": "echo \"Session started: $(date)\" >> logs/session.log",
        "powershell": "Add-Content -Path logs/session.log -Value \"Session started: $(Get-Date)\"",
        "cwd": ".",
        "timeoutSec": 10
      }
    ],
    "userPromptSubmitted": [
      {
        "type": "command",
        "bash": "./scripts/log-prompt.sh",
        "powershell": "./scripts/log-prompt.ps1",
        "cwd": "scripts",
        "env": {
          "LOG_LEVEL": "INFO"
        }
      }
    ],
    "preToolUse": [
      {
        "type": "command",
        "bash": "./scripts/security-check.sh",
        "powershell": "./scripts/security-check.ps1",
        "cwd": "scripts",
        "timeoutSec": 15
      },
      {
        "type": "command",
        "bash": "./scripts/log-tool-use.sh",
        "powershell": "./scripts/log-tool-use.ps1",
        "cwd": "scripts"
      }
    ],
    "postToolUse": [
      {
        "type": "command",
        "bash": "cat >> logs/tool-results.jsonl",
        "powershell": "$input | Add-Content -Path logs/tool-results.jsonl"
      }
    ],
    "sessionEnd": [
      {
        "type": "command",
        "bash": "./scripts/cleanup.sh",
        "powershell": "./scripts/cleanup.ps1",
        "cwd": "scripts",
        "timeoutSec": 60
      }
    ]
  }
}
```

## 性能考虑

挂钩同步运行并阻止 agent 执行。为了确保响应式体验，请记住以下注意事项：

* **最小化执行时间**：尽可能将钩子执行时间保持在5秒以下。
* **优化日志记录**：使用异步日志记录，例如附加到文件，而不是同步日志记录I/O.
* **使用后台处理**：对于昂贵的操作，请考虑后台处理。
* **缓存结果**：尽可能缓存昂贵的计算。

## 安全考虑

为了确保使用钩子时保持安全性，请记住以下注意事项：

* **始终验证和清理由钩子处理的输入**。不受信任的输入可能会导致意外行为。
* **构建命令时使用正确的 shell 转义**。这可以防止命令注入漏洞。
* **切勿记录敏感数据，例如令牌或密码**。
* **确保挂钩脚本和日志具有适当的权限**。
* **谨慎使用进行外部网络调用的钩子**。这些可能会导致延迟、故障或将数据暴露给第三方。
* **设置适当的超时以防止资源耗尽**。长时间运行的挂钩可能会阻止 agent 执行并降低性能。

## 后续步骤

要开始创建钩子，请参阅：

* [使用挂钩](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/use-hooks>)为Copilot云agent自定义agent工作流程
* [使用带有GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>)的挂钩用于GitHub Copilot CLI


## 原文对照

<details>
<summary>展开完整原文</summary>

# About hooks for GitHub Copilot

Extend and customize GitHub Copilot agent behavior by executing custom shell commands at key points during agent execution.

## What are hooks?

Hooks are a way of executing custom shell commands at strategic points in an agent's workflow, such as when an agent session starts or ends, when you enter a prompt, or when a tool is called.

Hooks receive detailed information about agent actions via JSON input, enabling context-aware automation. For example, you can use hooks to:

* Programmatically approve or deny tool executions.
* Use built-in security features like secret scanning to prevent credential leaks.
* Implement custom validation rules and audit logging for compliance.

Hooks are available for use with:

* **Copilot cloud agent** on GitHub.
* **GitHub Copilot CLI** in your terminal.

You define hooks in JSON files, stored in your repository at `.github/hooks/*.json`. These apply whenever Copilot agents are used in the repository. Copilot CLI also supports personal hooks that you store in your home directory at `~/.copilot/hooks/*.json`. These apply whenever you use Copilot CLI.

## Types of hooks

The following types of hooks are available:

* **sessionStart**: Executed when a new agent session begins or when resuming an existing session. Can be used to initialize environments, log session starts for auditing, validate project state, and set up temporary resources.
* **sessionEnd**: Executed when the agent session completes or is terminated. Can be used to cleanup temporary resources, generate and archive session reports and logs, or send notifications about session completion.
* **userPromptSubmitted**: Executed when the user submits a prompt to the agent. Can be used to log user requests for auditing and usage analysis.
* **preToolUse**: Executed before the agent uses any tool (such as `bash`, `edit`, `view`). This is the most powerful hook as it can **approve or deny tool executions**. Use this hook to block dangerous commands, enforce security policies and coding standards, require approval for sensitive operations, or log tool usage for compliance.
* **postToolUse**: Executed after a tool completes execution (whether successful or failed). Can be used to log execution results, track usage statistics, generate audit trails, monitor performance metrics, and send failure alerts.
* **agentStop**: Executed when the main agent has finished responding to your prompt.
* **subagentStop**: Executed when a subagent completes, before returning results to the parent agent.
* **errorOccurred**: Executed when an error occurs during agent execution. Can be used to log errors for debugging, send notifications, track error patterns, and generate reports.

To see a complete reference of hook types with example use cases, best practices, and advanced patterns, see [GitHub Copilot hooks reference](<https://docs.github.com/en/copilot/reference/hooks-reference>).

## Hook configuration format

You configure hooks using a special JSON format. The JSON must contain a `version` field with a value of `1` and a `hooks` object containing arrays of hook definitions.

```json copy
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "command",
        "bash": "string (optional)",
        "powershell": "string (optional)",
        "cwd": "string (optional)",
        "env": { "KEY": "value" },
        "timeoutSec": 30
      }
    ]
  }
}
```

The hook object can contain the following keys:

| Property     | Required              | Description                                                                    |
| ------------ | --------------------- | ------------------------------------------------------------------------------ |
| `type`       | Yes                   | Must be `"command"`                                                            |
| `bash`       | Yes (on Unix systems) | Path to the bash script to execute                                             |
| `powershell` | Yes (on Windows)      | Path to the PowerShell script to execute                                       |
| `cwd`        | No                    | Working directory for the script (relative to repository root)                 |
| `env`        | No                    | Additional environment variables that are merged with the existing environment |
| `timeoutSec` | No                    | Maximum execution time in seconds (default: 30)                                |

## Example hook configuration file

This is an example configuration file that lives in `~/.github/hooks/project-hooks.json` within a repository.

```json copy
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "command",
        "bash": "echo \"Session started: $(date)\" >> logs/session.log",
        "powershell": "Add-Content -Path logs/session.log -Value \"Session started: $(Get-Date)\"",
        "cwd": ".",
        "timeoutSec": 10
      }
    ],
    "userPromptSubmitted": [
      {
        "type": "command",
        "bash": "./scripts/log-prompt.sh",
        "powershell": "./scripts/log-prompt.ps1",
        "cwd": "scripts",
        "env": {
          "LOG_LEVEL": "INFO"
        }
      }
    ],
    "preToolUse": [
      {
        "type": "command",
        "bash": "./scripts/security-check.sh",
        "powershell": "./scripts/security-check.ps1",
        "cwd": "scripts",
        "timeoutSec": 15
      },
      {
        "type": "command",
        "bash": "./scripts/log-tool-use.sh",
        "powershell": "./scripts/log-tool-use.ps1",
        "cwd": "scripts"
      }
    ],
    "postToolUse": [
      {
        "type": "command",
        "bash": "cat >> logs/tool-results.jsonl",
        "powershell": "$input | Add-Content -Path logs/tool-results.jsonl"
      }
    ],
    "sessionEnd": [
      {
        "type": "command",
        "bash": "./scripts/cleanup.sh",
        "powershell": "./scripts/cleanup.ps1",
        "cwd": "scripts",
        "timeoutSec": 60
      }
    ]
  }
}
```

## Performance considerations

Hooks run synchronously and block agent execution. To ensure a responsive experience, keep the following considerations in mind:

* **Minimize execution time**: Keep hook execution time under 5 seconds when possible.
* **Optimize logging**: Use asynchronous logging, like appending to files, rather than synchronous I/O.
* **Use background processing**: For expensive operations, consider background processing.
* **Cache results**: Cache expensive computations when possible.

## Security considerations

To ensure security is maintained when using hooks, keep the following considerations in mind:

* **Always validate and sanitize the input processed by hooks**. Untrusted input could lead to unexpected behavior.
* **Use proper shell escaping when constructing commands**. This prevents command injection vulnerabilities.
* **Never log sensitive data, such as tokens or passwords**.
* **Ensure hook scripts and logs have the appropriate permissions**.
* **Be cautious with hooks that make external network calls**. These can introduce latency, failures, or expose data to third parties.
* **Set appropriate timeouts to prevent resource exhaustion**. Long-running hooks can block agent execution and degrade performance.

## Next steps

To start creating hooks, see:

* [Customize agent workflows with hooks](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/use-hooks>) for Copilot cloud agent
* [Using hooks with GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>) for GitHub Copilot CLI


</details>
