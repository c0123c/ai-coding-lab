# 在 VS Code 中配置 Agent Hooks

按 Copilot、Claude、Codex 和本地会话比较 Hook 实现，介绍配置、迁移、生命周期事件与管理方式；标注预览状态。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/hooks>)；官方文件提交（截至抓取）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/hooks>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中配置 Agent Hooks.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在 Visual Studio Code 中配置 agent 挂钩（预览）

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/hooks.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

挂钩在 agent 生命周期的特定点运行自定义操作。使用钩子来自动化工作流程、验证操作、创建审计跟踪或独立于语言模型强制执行策略。

选定的 [agent 线束 ](<https://code.visualstudio.com/docs/agents/concepts/agent-harnesses>) 确定运行哪个钩子实现。在创建或重用挂钩之前，请确定会话目标并使用该工具的配置和事件架构。

本文帮助您选择正确的钩子实现、管理 VS Code 中的钩子以及为本地线束配置钩子。

注意事项

VS Code 挂钩体验处于预览状态。各个提供程序实现可能具有不同的生命周期状态。例如CopilotSDK中的钩子一般都是可用的。

重要

您的组织可能会限制哪些挂钩可以运行。请联系您的管理员以获取更多信息。管理员可以了解如何[管理企业环境中的挂钩](<https://code.visualstudio.com/docs/enterprise/manage-ai-settings#_enable-or-disable-hooks>)。

## 为您的会话选择钩子实现

**会话目标**控件选择agent线束。线束拥有钩子生命周期和有效负载。

Agent 主机是托管 Copilot、Claude 和 Codex 线束的进程。本地线束在扩展主机中运行。聊天视图和 Agents 窗口是显示和控制任一主机上的会话的客户端。

|会话目标|线束运行的地方|钩子实现|配置和事件参考|
| --- | --- | --- | --- |
| **Copilot** |Agent 主机|共享 Copilot SDK 实施|使用 [GitHub Copilot 挂钩参考 ](<https://docs.github.com/en/copilot/reference/hooks-reference>)。|
| **Claude** |Agent 主机| Claude Agent SDK |使用 [Claude 挂钩参考 ](<https://code.claude.com/docs/en/hooks>)。|
| **Codex** |Agent 主机或 Codex 分机|Codex 运行时|使用 [Codex 挂钩文档 ](<https://developers.openai.com/codex/hooks/>)。|
|**云**|提供商基础设施|已选云【agent】|使用提供商文档。对于 Copilot 云 agent，请参阅 [GitHub Copilot 挂钩参考 ](<https://docs.github.com/en/copilot/reference/hooks-reference>)。|
|**本地**|分机主机|VS Code 本地挂钩|使用本文中的[本地配置](<#_configure-hooks-for-the-local-harness>)和[本地挂钩参考](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>)。|

某些线束会发现相同的挂钩文件，例如 `.github/hooks/*.json` 或 `.claude/settings.json`。此文件兼容性并不使它们的行为相同。支持的事件、事件名称、匹配器、命令属性、工具名称、有效负载和输出决策可能有所不同。

Agent 主机上的 Copilot 会话使用与 Copilot CLI 相同的 SDK 挂钩实现。使用 GitHub 参考的 Copilot CLI 部分进行运行时配置和有效负载，但验证该事件在所选 VS Code 版本中可用。

### 在线束之间迁移挂钩

在切换现有工作流程的会话目标之前：

* 确认目标线束发现挂钩文件。
* 比较支持的事件和命令属性。
* 在重用过滤器或验证逻辑之前，请检查工具名称和输入形状。
* 验证读取聊天记录的脚本。转录格式不是稳定的交叉工具 API。
* 测试输出决策，例如阻止工具调用或添加上下文。
* 确认 hook 命令运行的位置以及它可以访问哪些文件和环境变量。

不要仅通过更改语言模型来验证迁移。从 **Session Target** 控件中选择目标线束并启动测试会话。

## 创建和管理挂钩

在打开 hooks 界面之前选择会话目标。这可确保 VS Code 显示预期线束的自定义设置。

查看和管理挂钩：

1. 打开聊天视图或 Agents 窗口。
2. 从 **会话目标** 控件中选择目标。
3. 打开Agent自定义编辑器：

   * 在聊天视图中，选择 **配置聊天**（齿轮图标），然后选择 **Hooks**。
   * 在Agents窗口中，选择**自定义**面板中的**Hooks**。
   * 在聊天输入中输入`/hooks`。
4. 查看所选目标的挂钩源。选择一个挂钩以打开其配置。

您还可以从命令面板（⇧⌘P（Windows、Linux Ctrl+Shift+P））运行 **Chat：配置 Hooks** 来管理本地挂钩文件。

要使用 AI 生成挂钩，请在聊天中输入 `/create-hook <description>` 或运行 **Chat：从命令面板生成挂钩 **。生成的钩子使用`.github/hooks/`。使用前对照目标线束参考查看结果。

对于 Claude 和 Codex，请使用提供程序文档获取其他配置和管理选项。

## 为本地线束配置挂钩

本文的其余部分描述了在扩展主机中运行的本地钩子实现。这些事件、设置、负载和输出决策不会自动应用于 Agent 主机上的 Copilot、Claude 或 Codex 会话。

### 创建您的第一个本地钩子

下面的钩子记录了 Local agent 调用每个工具之前的名称。在您的工作区中创建 `.github/hooks/audit.json`：

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "node .github/hooks/log-tool-use.cjs"
      }
    ]
  }
}
```

创建`.github/hooks/log-tool-use.cjs`：

```
const fs = require('node:fs');

let input = '';
process.stdin.setEncoding('utf8');
process.stdin.on('data', chunk => (input += chunk));
process.stdin.on('end', () => {
  const event = JSON.parse(input);
  fs.appendFileSync(
    '.github/hooks/tool-use.log',
    `${event.timestamp} ${event.tool_name}\n`
  );
});
```

启动本地 agent 会话并要求 agent 执行使用工具的任务。该挂钩将每个工具名称添加到 `.github/hooks/tool-use.log`。如果您不想提交，请将此日志文件添加到 `.gitignore`。

此示例还可以帮助您发现要在验证挂钩中使用的确切本地工具名称。工具名称和参数因线束而异。

### 本地挂钩文件位置

本地线束从这些内置位置发现钩子：

|适用范围|文件位置|注释|
| --- | --- | --- |
|工作空间| `.github/hooks/*.json` |本机 VS Code 或 Copilot 兼容的挂钩文件。|
|工作区，Claude 格式| `.claude/settings.json`, `.claude/settings.local.json` |需要聊天。useClaudeHooks 在 VS Code 在 VS Code Insiders 中打开，默认关闭。|
|用户| `~/.copilot/hooks/*.json` |在本地会话中可用。|
|用户，Claude 格式| `~/.claude/settings.json` |需要聊天。useClaudeHooks 在 VS Code 在 VS Code 内部人士中打开。|
|定制【agent】|`hooks` 在 `.agent.md` 前言中|仅针对本地线束中的自定义 agent 运行。|
|插件|`hooks.json` 或 `hooks/hooks.json`，取决于插件格式|请参阅插件中的 [ 挂钩 ](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_hooks-in-plugins>)。|

的


聊天。useHooks

Open in VS Code
Open in VS Code Insiders
 设置控制本地挂钩执行，默认情况下处于启用状态。工作区挂钩文件受 [ 工作区信任 ](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>) 的约束。

使用


聊天。hookFilesLocations

在 VS Code
中打开 在 VS Code Insiders
 中打开以添加或禁用本地挂钩位置。该设置的默认值为空，因为内置位置是单独注册的。指定一个文件夹来加载其所有 `*.json` 文件，或指定单个 `.json` 文件。路径可以相对于工作空间或以 `~` 开头。

```
"chat.hookFilesLocations": {
  "custom/hooks": true,              // Load all JSON hook files in this folder.
  "~/my-hooks/security.json": true,  // Load one hook file.
  ".claude/settings.json": false     // Disable a built-in location.
}
```

提示

在 monorepo 中，启用


聊天。useCustomizationsInParentRepositories

在 VS Code
中打开 在 VS Code Insiders
 中打开，以发现来自父存储库根的钩子。了解有关 [ 父存储库发现 ](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>) 的更多信息。

### 本地钩子配置格式

对于新的本地挂钩，请使用具有 PascalCase 事件名称和命令属性的 JSON 文件：

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "./scripts/validate-tool.sh",
        "windows": "powershell -File scripts\\validate-tool.ps1",
        "timeout": 15
      }
    ],
    "PostToolUse": [
      {
        "type": "command",
        "command": "npx prettier --write ."
      }
    ]
  }
}
```

本地解析器还接受其他挂钩文件格式：

|源格式|Local 如何识别它|需要审查的本地行为|
| --- | --- | --- |
|本机 VS Code|没有数字 `version` 属性的 PascalCase 事件|使用`command`、`windows`、`linux`、`osx`和`timeout`。|
| Copilot |数字 `version` 属性和小驼峰事件|将 Copilot 事件和命令属性名称映射到本地格式。运行时有效负载仍然使用本地架构。|
| Claude |`.claude` 文件夹中的 `settings.json` 或 `settings.local.json` 文件|需要聊天。useClaudeHooks 在 VS Code 在 VS Code 内部人士中打开。本地解析嵌套命令但忽略匹配器值，因此事件的每个命令都会运行。|

有关完整的本地命令属性、输入字段、输出字段和退出代码行为，请参阅[本地挂钩参考](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>)。

注意事项

从扩展主机平台选择操作系统覆盖。在远程开发窗口中，此平台可能与显示 VS Code UI 的操作系统不同。

### 本地钩子生命周期事件

本地线束支持以下事件：

|活动|当它着火时|常见用途|
| --- | --- | --- |
| [`SessionStart`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_sessionstart>) |第一个提示启动一个会话。|初始化资源或添加项目上下文。|
| [`UserPromptSubmit`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_userpromptsubmit>) |用户提交提示。|审核请求或添加上下文。|
| [`PreToolUse`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_pretooluse>) |在agent调用工具之前。|阻止操作、请求批准或更改工具输入。|
| [`PostToolUse`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_posttooluse>) |工具成功完成后。|验证结果、运行格式化程序或添加上下文。|
| [`PreCompact`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_precompact>) |在压缩对话上下文之前。|保存应该在压缩中幸存下来的状态。|
| [`SubagentStart`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_subagentstart>) |子代理启动。|跟踪嵌套 agent 使用或添加子代理上下文。|
| [`SubagentStop`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_subagentstop>) |一个子代理即将停止。|验证子代理结果或需要更多工作。|
| [`Stop`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_stop>) |当前的agent执行即将停止。|验证完成情况或要求执行其他操作。|

当事件触发时，本地线束通过标准输入 (stdin) 将 JSON 对象传递给命令。该命令可以将 JSON 写入标准输出 (stdout) 以添加上下文或控制下一个操作。该事件不一定对应于整个会话的结束。

### Agent 本地范围的挂钩

注意事项

Agent 范围的挂钩处于预览状态，并且仅受本地线束支持。

将 `hooks` 映射添加到 [ 自定义 agent](<https://code.visualstudio.com/docs/agent-customization/custom-agents>)，以便仅在 agent 处于活动状态时运行命令。除了适用的用户、工作区和插件挂钩之外，还运行 Agent 范围的挂钩。

```
---
name: Strict Formatter
description: Format code after the agent uses a tool
hooks:
  PostToolUse:
    - type: command
      command: "./scripts/format-changed-files.sh"
---

Follow the project's formatting requirements.
```

当自定义 agent 作为子代理运行时，其 `Stop` 挂钩将被视为 `SubagentStop`。 Agent-范围挂钩需要


聊天。useHooks

在 VS Code
 中打开 在 VS Code 内部人员 
 和受信任的工作区中打开。

对于由 Claude 格式或旧版 OpenPlugin 插件贡献的自定义 agents，请使用 [plugin-root 令牌 ](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_reference-plugin-paths-in-hook-commands>) 来引用捆绑脚本，而无需硬编码安装路径。

### 本地钩子示例

请求批准特定工具

首先，使用 [ 快速启动 ](<#_create-your-first-local-hook>) 或 agent 调试日志中的审核挂钩来识别准确的本地工具名称和输入架构。

创建`.github/hooks/approval.json`：

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "node .github/hooks/require-approval.cjs",
        "env": {
          "SENSITIVE_TOOL_NAME": "<tool-name>"
        }
      }
    ]
  }
}
```

创建`.github/hooks/require-approval.cjs`：

```
let input = '';
process.stdin.setEncoding('utf8');
process.stdin.on('data', chunk => (input += chunk));
process.stdin.on('end', () => {
  const event = JSON.parse(input);
  const decision =
    event.tool_name === process.env.SENSITIVE_TOOL_NAME
      ? {
          hookSpecificOutput: {
            hookEventName: 'PreToolUse',
            permissionDecision: 'ask',
            permissionDecisionReason: 'This tool requires manual approval.'
          }
        }
      : { continue: true };

  process.stdout.write(JSON.stringify(decision));
});
```

将 `<tool-name>` 替换为准确的本地工具名称。请勿从其他线束复制工具名称。


会话开始时添加项目上下文

创建`.github/hooks/context.json`：

```
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "node .github/hooks/project-context.cjs"
      }
    ]
  }
}
```

创建`.github/hooks/project-context.cjs`：

```
const packageJson = require('../../package.json');

process.stdout.write(
  JSON.stringify({
    hookSpecificOutput: {
      hookEventName: 'SessionStart',
      additionalContext: `Project: ${packageJson.name} ${packageJson.version}`
    }
  })
);
```

## 排除钩子故障

首先，确认会话目标与您遵循的挂钩引用相匹配。当您切换线束时，Agent 自定义编辑器中显示的挂钩可能仍会使用不同的事件或负载。

对于本地挂钩：

* 运行 **Chat：配置 Hooks** 以检查本地线束发现哪些文件。
* 打开 [agent 调试日志 ](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_agent-debug-logs-panel>) 以检查事件名称、工具架构、挂钩输入和挂钩输出。
* 打开 **Output** 面板并选择 **GitHub Copilot Chat Hooks** 通道以查看命令输出和错误。
* 验证一下


聊天。useHooks

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 已启用，并且工作区受信任。
* 如果您使用 Claude 格式的文件，请启用


聊天。useClaudeHooks

在 VS Code
 中打开 在 VS Code Insiders
 中打开，并记住 Local 会忽略匹配器值。
* 仅在确认命令正在进行后才增加 `timeout`。

对于 Agent 主机线束，请使用提供商的挂钩参考和诊断。本地设置


聊天。useHooks

在VS Code
中打开在VS Code内部人员
中打开，


聊天。hookFilesLocations

在 VS Code
中打开 在 VS Code 内部人士 
 中打开，并且


聊天。useClaudeHooks

在 VS Code
 中打开 在 VS Code 中打开 Insiders
 未配置 Copilot、Claude 或 Codex 挂钩执行。

## 安全考虑

注意事项

挂钩使用其利用进程的权限执行代码。在运行之前检查每个钩子和引用的脚本，尤其是在共享存储库或插件中。

* 将钩子配置和脚本视为可执行代码。
* agent 编辑稍后挂钩可以执行的脚本之前需要获得批准。
* 在 shell 命令中使用值之前，请验证所有 JSON 输入。
* 仅向挂钩命令提供它们所需的文件、进程和网络访问权限。
* 将凭证存储在批准的秘密存储中。不要将机密放置在钩子配置、脚本、输出或 agent 上下文中。
* 检查所选工具的信任和策略模型。这些控件不可跨运行时移植。

## 相关资源

* [本地挂钩参考](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>)
* [选择并使用agent安全带](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>)
* [AI agents](<https://code.visualstudio.com/docs/agents/run/security>) 的安全注意事项

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configure agent hooks in Visual Studio Code (Preview)

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/hooks.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fhooks%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Hooks run custom actions at specific points in an agent's lifecycle. Use hooks to automate workflows, validate operations, create audit trails, or enforce policies independently of the language model.

The selected [agent harness](<https://code.visualstudio.com/docs/agents/concepts/agent-harnesses>) determines which hook implementation runs. Before you create or reuse a hook, identify the session target and use the configuration and event schema for that harness.

This article helps you choose the correct hook implementation, manage hooks from VS Code, and configure hooks for the Local harness.

Note

The VS Code hooks experience is in Preview. Individual provider implementations might have a different lifecycle status. For example, hooks in the Copilot SDK are generally available.

Important

Your organization might restrict which hooks can run. Contact your administrator for more information. Administrators can learn how to [manage hooks in enterprise environments](<https://code.visualstudio.com/docs/enterprise/manage-ai-settings#_enable-or-disable-hooks>).

## Choose the hook implementation for your session

The **Session Target** control selects the agent harness. The harness owns the hook lifecycle and payloads.

The Agent Host is the process that hosts the Copilot, Claude, and Codex harnesses. The Local harness runs in the extension host. The Chat view and Agents window are clients that display and control sessions on either host.

| Session target | Where the harness runs | Hook implementation | Configuration and event reference |
| --- | --- | --- | --- |
| **Copilot** | Agent Host | Shared Copilot SDK implementation | Use the [GitHub Copilot hooks reference](<https://docs.github.com/en/copilot/reference/hooks-reference>). |
| **Claude** | Agent Host | Claude Agent SDK | Use the [Claude hooks reference](<https://code.claude.com/docs/en/hooks>). |
| **Codex** | Agent Host or the Codex extension | Codex runtime | Use the [Codex hooks documentation](<https://developers.openai.com/codex/hooks/>). |
| **Cloud** | Provider infrastructure | Selected cloud agent | Use the provider documentation. For Copilot cloud agent, see the [GitHub Copilot hooks reference](<https://docs.github.com/en/copilot/reference/hooks-reference>). |
| **Local** | Extension host | VS Code Local hooks | Use the [Local configuration](<#_configure-hooks-for-the-local-harness>) in this article and the [Local hooks reference](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>). |

Some harnesses discover the same hook files, such as `.github/hooks/*.json` or `.claude/settings.json`. This file compatibility does not make their behavior identical. Supported events, event names, matchers, command properties, tool names, payloads, and output decisions can differ.

Copilot sessions on Agent Host use the same SDK hook implementation as Copilot CLI. Use the Copilot CLI sections of the GitHub reference for runtime configuration and payloads, but verify that the event is available in the selected VS Code version.

### Migrate hooks between harnesses

Before you switch the session target for an existing workflow:

* Confirm that the destination harness discovers the hook file.
* Compare the supported events and command properties.
* Check tool names and input shapes before you reuse filters or validation logic.
* Validate scripts that read chat transcripts. Transcript formats are not a stable cross-harness API.
* Test output decisions, such as blocking a tool call or adding context.
* Confirm where the hook command runs and which files and environment variables it can access.

Do not validate a migration by changing only the language model. Select the destination harness from the **Session Target** control and start a test session.

## Create and manage hooks

Select the session target before you open the hooks interface. This ensures that VS Code shows the customizations for the intended harness.

To view and manage hooks:

1. Open the Chat view or Agents window.
2. Select the target from the **Session Target** control.
3. Open the Agent Customizations editor:

   * In the Chat view, select **Configure Chat** (gear icon) and then select **Hooks**.
   * In the Agents window, select **Hooks** in the **Customizations** panel.
   * Enter `/hooks` in the chat input.
4. Review the hook sources for the selected target. Select a hook to open its configuration.

You can also run **Chat: Configure Hooks** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)) to manage Local hook files.

To generate a hook with AI, enter `/create-hook <description>` in chat or run **Chat: Generate Hook** from the Command Palette. The generated hook uses `.github/hooks/`. Review the result against the destination harness reference before you use it.

For Claude and Codex, use the provider documentation for additional configuration and management options.

## Configure hooks for the Local harness

The rest of this article describes the Local hook implementation that runs in the extension host. These events, settings, payloads, and output decisions do not automatically apply to Copilot, Claude, or Codex sessions on Agent Host.

### Create your first Local hook

The following hook records the name of every tool before the Local agent invokes it. Create `.github/hooks/audit.json` in your workspace:

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "node .github/hooks/log-tool-use.cjs"
      }
    ]
  }
}
```

Create `.github/hooks/log-tool-use.cjs`:

```
const fs = require('node:fs');

let input = '';
process.stdin.setEncoding('utf8');
process.stdin.on('data', chunk => (input += chunk));
process.stdin.on('end', () => {
  const event = JSON.parse(input);
  fs.appendFileSync(
    '.github/hooks/tool-use.log',
    `${event.timestamp} ${event.tool_name}\n`
  );
});
```

Start a Local agent session and ask the agent to perform a task that uses tools. The hook adds each tool name to `.github/hooks/tool-use.log`. Add this log file to `.gitignore` if you don't want to commit it.

This example also helps you discover the exact Local tool names to use in validation hooks. Tool names and arguments differ between harnesses.

### Local hook file locations

The Local harness discovers hooks from these built-in locations:

| Scope | File location | Notes |
| --- | --- | --- |
| Workspace | `.github/hooks/*.json` | Native VS Code or Copilot-compatible hook files. |
| Workspace, Claude format | `.claude/settings.json`, `.claude/settings.local.json` | Requires    chat.useClaudeHooks  Open in VS Code Open in VS Code Insiders , which is off by default. |
| User | `~/.copilot/hooks/*.json` | Available across Local sessions. |
| User, Claude format | `~/.claude/settings.json` | Requires    chat.useClaudeHooks  Open in VS Code Open in VS Code Insiders . |
| Custom agent | `hooks` in `.agent.md` frontmatter | Runs only for that custom agent in the Local harness. |
| Plugin | `hooks.json` or `hooks/hooks.json`, depending on the plugin format | See [hooks in plugins](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_hooks-in-plugins>). |

The 


chat.useHooks

Open in VS Code
Open in VS Code Insiders
 setting controls Local hook execution and is on by default. Workspace hook files are subject to [Workspace Trust](<https://code.visualstudio.com/docs/editing/workspaces/workspace-trust>).

Use 


chat.hookFilesLocations

Open in VS Code
Open in VS Code Insiders
 to add or disable Local hook locations. The setting's default value is empty because the built-in locations are registered separately. Specify a folder to load all its `*.json` files, or specify an individual `.json` file. Paths can be relative to the workspace or start with `~`.

```
"chat.hookFilesLocations": {
  "custom/hooks": true,              // Load all JSON hook files in this folder.
  "~/my-hooks/security.json": true,  // Load one hook file.
  ".claude/settings.json": false     // Disable a built-in location.
}
```

Tip

In a monorepo, enable 


chat.useCustomizationsInParentRepositories

Open in VS Code
Open in VS Code Insiders
 to discover hooks from the parent repository root. Learn more about [parent repository discovery](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>).

### Local hook configuration formats

For new Local hooks, use a JSON file with PascalCase event names and command properties:

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "./scripts/validate-tool.sh",
        "windows": "powershell -File scripts\\validate-tool.ps1",
        "timeout": 15
      }
    ],
    "PostToolUse": [
      {
        "type": "command",
        "command": "npx prettier --write ."
      }
    ]
  }
}
```

The Local parser also accepts other hook file formats:

| Source format | How Local recognizes it | Local behavior to review |
| --- | --- | --- |
| Native VS Code | PascalCase events without a numeric `version` property | Uses `command`, `windows`, `linux`, `osx`, and `timeout`. |
| Copilot | Numeric `version` property and lower camel case events | Maps Copilot event and command property names to the Local format. Runtime payloads still use the Local schema. |
| Claude | A `settings.json` or `settings.local.json` file in a `.claude` folder | Requires    chat.useClaudeHooks  Open in VS Code Open in VS Code Insiders . Local parses nested commands but ignores matcher values, so every command for the event runs. |

For the complete Local command properties, input fields, output fields, and exit-code behavior, see the [Local hooks reference](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>).

Note

An operating system override is selected from the extension host platform. In Remote Development windows, this platform might differ from the operating system that displays the VS Code UI.

### Local hook lifecycle events

The Local harness supports these events:

| Event | When it fires | Common uses |
| --- | --- | --- |
| [`SessionStart`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_sessionstart>) | The first prompt starts a session. | Initialize resources or add project context. |
| [`UserPromptSubmit`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_userpromptsubmit>) | The user submits a prompt. | Audit requests or add context. |
| [`PreToolUse`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_pretooluse>) | Before the agent invokes a tool. | Block an operation, request approval, or change tool input. |
| [`PostToolUse`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_posttooluse>) | After a tool completes successfully. | Validate results, run a formatter, or add context. |
| [`PreCompact`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_precompact>) | Before conversation context is compacted. | Save state that should survive compaction. |
| [`SubagentStart`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_subagentstart>) | A subagent starts. | Track nested agent use or add subagent context. |
| [`SubagentStop`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_subagentstop>) | A subagent is about to stop. | Validate subagent results or require more work. |
| [`Stop`](<https://code.visualstudio.com/docs/agents/reference/hooks-reference#_stop>) | The current agent execution is about to stop. | Validate completion or require another action. |

When an event fires, the Local harness passes a JSON object to the command through standard input (stdin). The command can write JSON to standard output (stdout) to add context or control the next action. The event does not necessarily correspond to the end of the entire session.

### Agent-scoped hooks for Local

Note

Agent-scoped hooks are in Preview and are supported only by the Local harness.

Add a `hooks` map to a [custom agent](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) to run commands only while that agent is active. Agent-scoped hooks run in addition to applicable user, workspace, and plugin hooks.

```
---
name: Strict Formatter
description: Format code after the agent uses a tool
hooks:
  PostToolUse:
    - type: command
      command: "./scripts/format-changed-files.sh"
---

Follow the project's formatting requirements.
```

When the custom agent runs as a subagent, its `Stop` hook is treated as `SubagentStop`. Agent-scoped hooks require 


chat.useHooks

Open in VS Code
Open in VS Code Insiders
 and a trusted workspace.

For custom agents contributed by Claude-format or legacy OpenPlugin plugins, use [plugin-root tokens](<https://code.visualstudio.com/docs/agent-customization/agent-plugins#_reference-plugin-paths-in-hook-commands>) to reference bundled scripts without hardcoding installation paths.

### Local hook examples

Request approval for a specific tool

First, use the audit hook in the [quick start](<#_create-your-first-local-hook>) or the agent debug logs to identify the exact Local tool name and input schema.

Create `.github/hooks/approval.json`:

```
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "node .github/hooks/require-approval.cjs",
        "env": {
          "SENSITIVE_TOOL_NAME": "<tool-name>"
        }
      }
    ]
  }
}
```

Create `.github/hooks/require-approval.cjs`:

```
let input = '';
process.stdin.setEncoding('utf8');
process.stdin.on('data', chunk => (input += chunk));
process.stdin.on('end', () => {
  const event = JSON.parse(input);
  const decision =
    event.tool_name === process.env.SENSITIVE_TOOL_NAME
      ? {
          hookSpecificOutput: {
            hookEventName: 'PreToolUse',
            permissionDecision: 'ask',
            permissionDecisionReason: 'This tool requires manual approval.'
          }
        }
      : { continue: true };

  process.stdout.write(JSON.stringify(decision));
});
```

Replace `<tool-name>` with the exact Local tool name. Do not copy a tool name from another harness.


Add project context when a session starts

Create `.github/hooks/context.json`:

```
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "node .github/hooks/project-context.cjs"
      }
    ]
  }
}
```

Create `.github/hooks/project-context.cjs`:

```
const packageJson = require('../../package.json');

process.stdout.write(
  JSON.stringify({
    hookSpecificOutput: {
      hookEventName: 'SessionStart',
      additionalContext: `Project: ${packageJson.name} ${packageJson.version}`
    }
  })
);
```

## Troubleshoot hooks

First, confirm that the session target matches the hook reference you followed. A hook that appears in the Agent Customizations editor might still use different events or payloads when you switch harnesses.

For Local hooks:

* Run **Chat: Configure Hooks** to check which files the Local harness discovers.
* Open the [agent debug logs](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/chat-debug-view#_agent-debug-logs-panel>) to inspect event names, tool schemas, hook input, and hook output.
* Open the **Output** panel and select the **GitHub Copilot Chat Hooks** channel to review command output and errors.
* Verify that 


  chat.useHooks

  Open in VS Code
  Open in VS Code Insiders
   is enabled and the workspace is trusted.
* If you use a Claude-format file, enable 


  chat.useClaudeHooks

  Open in VS Code
  Open in VS Code Insiders
   and remember that Local ignores matcher values.
* Increase `timeout` only after you confirm that the command is making progress.

For Agent Host harnesses, use the provider's hook reference and diagnostics. The Local settings 


chat.useHooks

Open in VS Code
Open in VS Code Insiders
, 


chat.hookFilesLocations

Open in VS Code
Open in VS Code Insiders
, and 


chat.useClaudeHooks

Open in VS Code
Open in VS Code Insiders
 do not configure Copilot, Claude, or Codex hook execution.

## Security considerations

Caution

Hooks execute code with the permissions of their harness process. Review every hook and referenced script before you run it, especially in a shared repository or plugin.

* Treat hook configuration and scripts as executable code.
* Require approval before an agent edits a script that a later hook can execute.
* Validate all JSON input before you use values in a shell command.
* Give hook commands only the file, process, and network access they need.
* Store credentials in approved secret storage. Do not place secrets in hook configuration, scripts, output, or agent context.
* Review the trust and policy model for the selected harness. These controls are not portable across runtimes.

## Related resources

* [Local hooks reference](<https://code.visualstudio.com/docs/agents/reference/hooks-reference>)
* [Choose and use an agent harness](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>)
* [Security considerations for AI agents](<https://code.visualstudio.com/docs/agents/run/security>)

10/7/2026


</details>
