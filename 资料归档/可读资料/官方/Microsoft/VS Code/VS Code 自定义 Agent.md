# VS Code 自定义 Agent

说明如何创建、生成和管理专门角色，配置工具与提示、选择文件位置并控制 Agent 列表。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/custom-agents>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 自定义 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# VS Code中的自定义agents

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/custom-agents.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

自定义 agents 将指令、工具和可选语言模型组合成特定角色（例如规划或代码审查）的可重用配置。切换到自定义 agent，而不是为每个对话重复相同的说明并选择工具。

本文向您展示如何在 VS Code 中创建、使用和管理自定义 agents、配置其文件以及通过切换连接它们。

提示

**不确定要使用哪个自定义？** 请参阅 [ 决策矩阵 ](<https://code.visualstudio.com/docs/agents/concepts/customization#_customization-options-at-a-glance>) 将自定义 agents 与提示文件、agent skills 和其他选项进行比较。

## 什么是定制agents？

[内置agent角色](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_choose-a-built-in-agent-role>)提供聊天的通用配置。自定义 agents 提供您自己的角色特定配置，在 `.agent.md` Markdown 文件中定义。

将 agent 存储在您的工作区中以与项目贡献者共享，或在用户级别存储以跨工作区重复使用它。 [支持的文件位置](<#_custom-agent-file-locations>)取决于线束和文件格式。

您可以将 agent 定义与 [ 不同的 agent 线束 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>) 重复使用。可用的工具和管理控件取决于所选的线束。

## 为什么使用自定义agents？

当重复任务需要自己的说明和工具选择时，请使用自定义 agent。例如：

* 提供规划 agent 只读工具，以便在实施之前研究变更。
* 进行代码审查 agent 您团队的审查标准。
* 提供一个实现 agent 编辑工具和说明，以遵循现有的项目模式。

注意事项

子代理可以使用自定义运行 agent。了解有关跑步的更多信息 [具有自定义功能的子代理 agents](<https://code.visualstudio.com/docs/agents/run/subagents#_run-a-custom-agent-as-a-subagent>).

## 创建自定义 agent

使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>)（预览）在您的工作区或用户级别创建 agent。

使用 **Session Target** 控件选择要使用的 [agent 线束 ](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_choose-a-session-target>)，然后从任一表面打开编辑器：

* **Agents窗口**：在左侧边栏的**自定义**面板中选择**Agents**。
* **聊天视图**：选择**配置聊天**（齿轮图标），然后选择**Agents**。

编辑器显示所选线束的自定义设置。您可以[使用AI](<#_generate-a-custom-agent-with-ai>)生成自定义agent，或按照以下步骤手动创建文件：

1. 从下拉列表中选择 **新建 Agent（工作空间）** 或 **新建 Agent（用户）**，具体取决于您要存储 agent 文件的位置。

![显示 Agent 自定义编辑器和用于创建新自定义 agent 的下拉列表的屏幕截图。](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-custom-agent.png>)

或者，从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 运行 **Chat：新建自定义 Agent** 命令。
2. 选择位置并输入自定义文件名 agent。这是显示在 agents 下拉列表中的默认名称。
3. 在新创建的 `.agent.md` 文件中提供详细信息，然后保存。

   * 填写【YAML】的frontmatter，配置【agent】的名称、描述、工具等选项。
   * 在文件正文中添加 agent 的说明。使用 [ 示例 ](<#_examples>) 作为起点。

您可以通过在 Agent 自定义编辑器中打开现有自定义 agents 来修改它们。

提示

在聊天视图中，选择 **Copilot** 后，在聊天输入中键入 `/agents` 以打开 Agent 自定义编辑器的 **Agents** 部分。选择**本地**后，`/agents`将打开agent选择器，您可以在其中选择**配置自定义Agents**来打开编辑器。

### 使用 AI 生成自定义 agent

从 Agent 自定义编辑器的 ** 概述 ** 页面开始，根据描述生成自定义 agent：

1. 打开 Agent 自定义编辑器并选择 ** 概述 **。
2. 输入一个提示，描述 agent 的角色以及它是否应在工作区中或跨工作区可用。
3. 回答有关角色或工作流程的任何澄清问题。
4. 查看生成的 `.agent.md` 文件，包括其工具、说明和 frontmatter。更正任何不准确的信息并保存文件。

例如，输入以下提示：

```
Create a workspace custom agent for code reviews. Review changes for correctness, maintainability, and consistency with this repository's conventions. Report findings without editing files.
```

您还可以从正在进行的对话中提取自定义 agent。例如，在多轮调试会话之后，要求“为此类任务制作 agent”以将工作流捕获为可重用的自定义 agent。

选择 **Local** 后，您还可以使用以下快捷键：

* 在Agent模式聊天中输入`/create-agent`并描述您想要的角色。
* 从 Agent 自定义编辑器的 **Agents** 部分的下拉列表中选择 ** 生成 Agent**。

这些快捷方式在 [Agent 主机 ](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) 会话中不可用，例如 **Copilot**。请改用 **概述** 工作流程。

## 使用自定义agent

1. 在聊天视图或 Agents 窗口中，从 **会话目标 ** 控件中选择所需的线束。
2. 打开 **Agent** 下拉列表并选择您的自定义 agent。
3. 输入其角色的提示。例如，要求计划 agent 概述新功能所需的更改。
4. 查看响应和任何工具调用，以检查 agent 是否遵循预期指令并使用适当的工具。

如果 agent 未出现，请检查其文件是否位于所选线束的 [ 支持的位置 ](<#_custom-agent-file-locations>) 中，以及其 [ 可见性配置 ](<#_customize-the-agents-dropdown-list>) 是否允许用户调用。

## 自定义agents下拉列表

要保留自定义 agent 作为 [ 子代理 ](<https://code.visualstudio.com/docs/agents/run/subagents>) 而不将其列在 **Agent** 下拉列表中，请在其 YAML frontmatter 中设置 `user-invocable: false`：

```
user-invocable: false
```

此属性控制 **Local** 和 **Copilot** 中的选择器可见性。要再次显示 agent，请将其设置为 `true` 或删除该属性。子代理调用由`disable-model-invocation`单独控制。

编辑此属性会更改 agent 定义，而不是个人可见性首选项。如果您在存储库中共享该文件，则更改适用于使用该定义的每个人。它不会删除 agent 文件。

注意事项

Agent 自定义编辑器不为 ** 本地 ** 或 **Copilot** 中的自定义 agents 提供眼睛图标显示或隐藏控件。对于由扩展贡献的 agents，请改为管理贡献的扩展。请参阅[如何删除自定义agent？](<#_how-do-i-remove-a-custom-agent>)。

## 自定义 agent 文件位置

您可以为特定工作区或用户级别定义自定义 agents，它们可在所有工作区中使用。下表根据范围列出了自定义 agents 支持的文件位置。

|适用范围|默认文件位置|
| --- | --- |
|工作空间|`.github/agents`文件夹|
|工作区（Claude 格式）|`.claude/agents`文件夹|
|用户|`~/.copilot/agents` 或 `~/.claude/agents`|

要创建用户级自定义 agent，请使用 Agent 自定义编辑器或 **聊天：新建自定义 Agent** 命令。

重要

对于在 [Agent 主机 ](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) 上运行的会话，agent 从所选主机的文件夹（例如 `~/.copilot/agents` 或 `~/.claude/agents`）读取用户级自定义 agents，而不是从 VS Code 配置文件用户数据。要移动现有用户级自定义 agents，请使用 [ 用户自定义迁移 ](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-user-agents-and-instructions>)。

注意事项

的


聊天。agentFilesLocations

在 VS Code
中打开 在 VS Code 内部人员 
 中打开并且


聊天。modeFilesLocations

在 VS Code
 中打开 在 VS Code 内部人员中打开 
 设置已弃用，仅由本地 agent 使用。如果您使用这些设置配置了其他 agent 位置，[会将自定义项迁移到支持的位置 ](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>)。

提示

在 monorepo 中，启用


聊天。useCustomizationsInParentRepositories

在 VS Code
 中打开 在 VS Code 内部人员 
 中打开，以从父存储库根目录中发现自定义 agents。了解有关 [父存储库发现 ](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>) 的更多信息。

## 自定义agent文件结构

自定义 agent 文件使用 `.agent.md` 扩展名。可选的 YAML 标头配置 agent，Markdown 正文提供其指令。

注意事项

VS Code 会将工作区的 `.github/agents` 文件夹中的任何 `.md` 文件检测为自定义 agents。

### 标头（可选）

标头格式为 YAML frontmatter，包含以下字段：

|领域|描述|
| --- | --- |
| `description` |自定义 agent 的简短描述，在聊天输入字段中显示为占位符文本。|
| `name` |自定义的名称agent。如果未指定，则使用文件名。|
| `argument-hint` |聊天输入字段中显示的可选提示文本，用于指导用户如何与自定义 agent 进行交互。|
| `tools` | 工具列表或 [工具组](<https://code.visualstudio.com/docs/agent-customization/tool-sets>) 可用于此自定义的名称 agent。可以包括内置工具、工具集、 MCP 工具，或扩展贡献的工具。包括所有工具 MCP 服务器，使用 `<server name>/*` 格式。了解更多关于 [工具与 agents](<https://code.visualstudio.com/docs/agents/run/tools>). |
| `agents` |在此 agent 中可用作 [ 子代理 ](<https://code.visualstudio.com/docs/agents/run/subagents>) 的 agent 名称列表。使用 `*` 允许所有 agents，或使用空数组 `[]` 以防止使用任何子代理。如果指定 `agents`，请确保 `agent` 工具包含在 `tools` 属性中。要创建在 `agents` 中列出自身的自引用 agent，请启用 chat.subagents.allowInvocationsFromSubagents Open in VS Code Open in VS Code Insiders 。了解有关 [ 嵌套子代理 ](<https://code.visualstudio.com/docs/agents/run/subagents#_nested-subagents>) 的更多信息。|
| `model` |运行提示时使用的 AI 模型。指定单个模型名称（字符串）或模型的优先级列表（数组）。当您指定一个数组时，系统会按顺序尝试每个模型，直到找到可用的模型。如果未指定，则使用模型选择器中当前选择的模型。|
| `user-invocable` |可选的布尔标志，用于控制 agent 是否出现在聊天中的 agents 下拉列表中（默认为 `true`）。设置为 `false` 以创建只能作为 [ 子代理 ](<https://code.visualstudio.com/docs/agents/run/subagents>) 或以编程方式访问的 agents。请参阅[自定义agents下拉列表](<#_customize-the-agents-dropdown-list>)。|
| `disable-model-invocation` |可选布尔标志，用于防止 agent 被其他 agents 作为子代理调用（默认为 `false`）。|
| `infer` |**已弃用。** 使用 `user-invocable` 和 `disable-model-invocation` 来独立控制选取器可见性和子代理调用。|
| `target` |自定义 agent（`vscode` 或 `github-copilot`）的目标环境或上下文。|
| `mcp-servers` | 可选列表 Model Context Protocol (MCP) 要使用的服务器配置 json [定制 agents 在 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>) （目标： `github-copilot`). |
| `handoffs` |建议下一步操作的可选列表或在自定义 agents 之间转换的提示。聊天响应完成后，切换按钮将显示为交互式建议。|
| `handoffs.label` |切换按钮上显示的显示文本。|
| `handoffs.agent` |要切换到的目标 agent 标识符。|
| `handoffs.prompt` |发送到目标的提示文本agent。|
| `handoffs.send` |用于自动提交提示的可选布尔标志（默认为 `false`）|
| `handoffs.model` |执行切换时使用的可选语言模型。使用格式为 `Model Name (vendor)` 的限定型号名称，例如 `GPT-5 (copilot)` 或 `Claude Sonnet 4.5 (copilot)`。|
|`hooks`（预览）|可选的本地挂钩命令范围仅限于此 agent。此处定义的挂钩仅当此 agent 处于活动状态（由用户调用或作为子代理调用）时在本地线束中运行。需要聊天。useHooks 在 VS Code 在 VS Code 内部人员和受信任的工作区中打开。请参阅 [agent 范围的钩子 ](<https://code.visualstudio.com/docs/agent-customization/hooks#_agent-scoped-hooks-for-local>)。|

注意事项

如果使用自定义 agent 时给定工具不可用，则会忽略该工具。

### 身体

Markdown 正文包含 agent 遵循的指令，例如其任务、指南和预期输出。

您可以使用 Markdown 链接或 `#file:` 语法引用其他文件，例如重用说明文件。相对文件路径从自定义 agent 文件解析。要引用您的环境用户主文件夹，请使用 `~`，或以 `~/` 开头的路径，例如 `#file:~/copilot/security-guidelines.md`。使用 Unix 风格的 `/` 路径分隔符来保持自定义 agent 文件跨操作系统的可移植性。

要在正文中引用 agent 工具，请使用 `#tool:<tool-name>` 语法。例如，要引用 `fetch` 工具，请使用 `#tool:web/fetch`。

当您在聊天视图中选择自定义 agent 时，自定义 agent 文件正文中的指南将添加到用户聊天提示之前。

### 示例

规划agent示例

以下代码片段显示了“计划”自定义 agent 文件的示例，该文件生成实施计划并且不进行任何代码编辑。有关更多社区贡献的示例，请参阅 [Awesome Copilot 存储库 ](<https://github.com/github/awesome-copilot/tree/main>)。

```
---
description: Generate an implementation plan for new features or refactoring existing code.
name: Planner
tools: ['web/fetch', 'search/codebase', 'search/usages']
model: ['Claude Opus 4.5', 'GPT-5.2']  # Tries models in order
handoffs:
  - label: Implement Plan
    agent: agent
    prompt: Implement the plan outlined above.
    send: false
---
# Planning instructions
You are in planning mode. Your task is to generate an implementation plan for a new feature or for refactoring existing code.
Don't make any code edits, just generate a plan.

The plan consists of a Markdown document that describes the implementation plan, including the following sections:

* Overview: A brief description of the feature or refactoring task.
* Requirements: A list of requirements for the feature or refactoring task.
* Implementation Steps: A detailed list of steps to implement the feature or refactoring task.
* Testing: A list of tests that need to be implemented to verify the feature or refactoring task.
```


Agent编排示例

以下示例显示了一个“功能生成器”agent，它协调专门的子代理以进行研究然后实施工作流程。主 agent 使用 `agents` 属性来限制哪些 agents 可以作为子代理调用。

**feature-builder.agent.md** - 协调 agent：

```
---
name: Feature Builder
description: Build features by researching first, then implementing
tools: ['agent']
agents: ['Researcher', 'Implementer']
---
You are a feature builder. For each task:
1. Use the Researcher agent to gather context and find relevant patterns in the codebase
2. Use the Implementer agent to make the actual code changes based on research findings
```

**researcher.agent.md** - 只读研究 agent：

```
---
name: Researcher
description: Research codebase patterns and gather context
tools: ['search/codebase', 'web/fetch', 'search/usages']
---
Research thoroughly using read-only tools. Return a summary of findings.
```

**implementer.agent.md** - 代码编辑agent：

```
---
name: Implementer
description: Implement code changes based on provided context
tools: ['edit', 'read/terminalLastCommand']
---
Implement changes following existing code patterns. Make minimal, focused edits.
```


Agent 带有作用域挂钩示例（预览）

以下示例显示了一个自定义 agent，它在其 frontmatter 中定义了钩子。 `PostToolUse` 挂钩在每次成功调用工具后运行，并且仅当此 agent 在本地线束中处于活动状态时运行。必须启用本地挂钩


聊天。useHooks

在 VS Code
中打开 在 VS Code 内部人员 
 中打开，并且工作区必须受信任。

```
---
name: Strict Formatter
description: Agent that formats code after every successful tool call
hooks:
  PostToolUse:
    - type: command
      command: "./scripts/format-changed-files.sh"
---

You are a code editing agent. Follow the project's formatting requirements.
```

在 [Agent 挂钩 ](<https://code.visualstudio.com/docs/agent-customization/hooks>) 中了解有关挂钩的更多信息。

### Claude agent 格式

`.claude/agents` 文件夹中的 Agent 文件使用纯 `.md` 文件，遵循 [Claude 子 agents 格式 ](<https://code.claude.com/docs/en/sub-agents>)。它们支持 Claude 特定的 frontmatter 属性：

|领域|描述|
| --- | --- |
| `name` |Agent 姓名（必填）|
| `description` |agent 的作用|
| `tools` |允许的工具的逗号分隔字符串（例如，`"Read, Grep, Glob, Bash"`）|
| `disallowedTools` |要阻止的以逗号分隔的工具字符串|

VS Code 将 Claude 特定工具名称映射到相应的 VS Code 工具。支持 VS Code `.agent.md` 格式（带有用于工具的 YAML 数组）和 Claude 格式（带有逗号分隔的字符串）。

## 交接

切换将 agents 连接到工作流程中，并提供建议的后续步骤。聊天响应完成后，切换按钮可让您切换到另一个带有对话上下文和预填充提示的 agent。

在开始下一阶段之前，使用交接来检查任务的一个阶段。例如：

* **规划到实施**：审查计划，然后移交给实施agent开始编码。
* **实施审查**：完成实施，然后切换到代码审查agent。
* **未通过测试到通过测试**：审查生成的测试，然后交给 agent 实施通过它们所需的更改。

要定义切换，请将它们添加到 agent 的 frontmatter 中。每次切换都会指定目标 agent、按钮标签和可选提示：

```
---
description: Generate an implementation plan
tools: ['search', 'web']
handoffs:
  - label: Start Implementation
    agent: implementation
    prompt: Now implement the plan outlined above.
    send: false
    model: GPT-5.2 (copilot)
---
```

当您选择切换按钮时，您会切换到目标 agent，并带有预先填充的提示。如果是`send: true`，则提示自动提交。

## 工具列表优先级

对于 **Local**，当您在自定义 agent 和提示文件中使用 `tools` 时，提示文件的工具优先。 Agent 主机会话不加载提示文件。有关完整优先级顺序，请参阅提示文件文档中的[工具列表优先级](<https://code.visualstudio.com/docs/agent-customization/prompt-files#_tool-list-priority>)。

## 跨团队共享自定义 agents

要在团队中共享自定义 agents，您可以创建工作区级自定义 agent（`.github/agents` 文件夹）。如果您想要在组织内的多个工作区共享自定义 agents，您可以在 GitHub 组织级别定义它们。

VS Code 自动检测在您的帐户有权访问的组织级别定义的自定义 agents。这些 agents 与内置 agents 以及您的个人和工作区自定义 agents 一起显示在聊天中的 Agents 下拉列表中。

要启用组织级自定义 agents 的发现，请设置


github.copilot.chat.organizationCustomAgents.enabled

在VS Code
中打开在VS Code内部人员
至`true`中打开。

在 GitHub 文档中了解如何[为您的组织 ](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>) 创建自定义 agents。

## 常见问题

### 自定义agents与聊天模式有什么不同吗？

自定义 agents 以前称为自定义聊天模式。功能保持不变，但术语已更新，以更好地反映其为特定任务定制 AI 行为的目的。

如果您有现有的 `.chatmode.md` 文件，请将它们重命名为 `.agent.md` 并将它们放置在 [ 支持的自定义 agent 位置 ](<#_custom-agent-file-locations>) 之一。

### 如何删除自定义 agent？

要删除您维护的自定义 agent，请使用以下方法之一：

* 从工作区或 [ 用户级位置 ](<#_custom-agent-file-locations>) 删除其文件。
* 从 agents 下拉列表中选择 ** 配置自定义 Agents** 以打开 Agent 自定义编辑器，将鼠标悬停在自定义 agent 上，然后选择垃圾桶图标。

删除共享工作区 agent 也会在其他人收到存储库更改时将其删除。要保留定义但将其从选择器中删除，请参阅[自定义agents下拉列表](<#_customize-the-agents-dropdown-list>)。

您无法从 Agent 自定义编辑器中删除扩展提供的 agents。禁用或卸载贡献扩展以删除其 agents。这也会影响扩展的其他功能。

### 我如何知道自定义的 agent 来自哪里？

agents 下拉列表包括来自用户级文件、工作区、组织、扩展和插件的内置角色和自定义 agents。

要识别自定义 agent 的来源：

1. 从 agents 下拉列表中选择 ** 配置自定义 Agents**。
2. 将鼠标悬停在列表中的自定义 agent 上。源位置显示在工具提示中。

提示

使用聊天自定义诊断视图查看所有已加载的自定义 agents、提示文件、说明文件和技能以及任何错误。在聊天视图中右键单击并选择 **诊断**。了解更多关于 [故障排除 AI 在 VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>).

## 安全考虑

自定义 agents 可以限制哪些工具可用，这使您可以控制 AI 可以执行的操作。对于安全敏感的工作流程，请使用只读工具创建 agents 以防止意外修改。在存储库中共享 agents 时，请查看工具列表和说明，以确保它们遵循最小权限原则。

## 相关资源

* [与agents](<https://code.visualstudio.com/docs/agents/run/planning>)一起规划
* [使用自定义指令自定义AI](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [使用具有agents](<https://code.visualstudio.com/docs/agents/run/tools>)的工具

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Custom agents in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/custom-agents.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fcustom-agents%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Custom agents combine instructions, tools, and an optional language model into a reusable configuration for a specific role, such as planning or code review. Switch to a custom agent instead of repeating the same instructions and selecting tools for every conversation.

This article shows you how to create, use, and manage custom agents in VS Code, configure their files, and connect them with handoffs.

Tip

**Not sure which customization to use?** See the [decision matrix](<https://code.visualstudio.com/docs/agents/concepts/customization#_customization-options-at-a-glance>) to compare custom agents with prompt files, agent skills, and the other options.

## What are custom agents?

The [built-in agent roles](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_choose-a-built-in-agent-role>) provide general-purpose configurations for chat. Custom agents provide your own role-specific configurations, defined in `.agent.md` Markdown files.

Store an agent in your workspace to share it with project contributors, or at the user level to reuse it across workspaces. The [supported file locations](<#_custom-agent-file-locations>) depend on the harness and file format.

You can reuse agent definitions with [different agent harnesses](<https://code.visualstudio.com/docs/agents/run/agent-harnesses>). The available tools and management controls depend on the selected harness.

## Why use custom agents?

Use a custom agent when a recurring task needs its own instructions and tool selection. For example:

* Give a planning agent read-only tools to research a change before implementation.
* Give a code review agent your team's review criteria.
* Give an implementation agent editing tools and instructions to follow existing project patterns.

Note

Subagents can run with a custom agent. Learn more about running [subagents with custom agents](<https://code.visualstudio.com/docs/agents/run/subagents#_run-a-custom-agent-as-a-subagent>).

## Create a custom agent

Use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) (Preview) to create an agent in your workspace or at the user level.

Use the **Session Target** control to select the [agent harness](<https://code.visualstudio.com/docs/agents/run/agent-harnesses#_choose-a-session-target>) you intend to use, then open the editor from either surface:

* **Agents window**: Select **Agents** in the **Customizations** panel in the left sidebar.
* **Chat view**: Select **Configure Chat** (gear icon), and then select **Agents**.

The editor shows customizations for the selected harness. You can [generate a custom agent with AI](<#_generate-a-custom-agent-with-ai>), or follow these steps to create the file manually:

1. Select **New Agent (Workspace)** or **New Agent (User)** from the dropdown, depending on where you want to store the agent file.

   ![Screenshot showing the Agent Customizations editor and the dropdown to create a new custom agent.](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-custom-agent.png>)

   Alternatively, run the **Chat: New Custom Agent** command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
2. Select the location and enter a file name for the custom agent. This is the default name that appears in the agents dropdown.
3. Provide the details in the newly created `.agent.md` file, and then save it.

   * Fill in the YAML frontmatter to configure the agent's name, description, tools, and other options.
   * Add instructions for the agent in the body of the file. Use the [examples](<#_examples>) as a starting point.

You can modify existing custom agents by opening them in the Agent Customizations editor.

Tip

In the Chat view, with **Copilot** selected, type `/agents` in the chat input to open the **Agents** section of the Agent Customizations editor. With **Local** selected, `/agents` opens the agent picker, where you can select **Configure Custom Agents** to open the editor.

### Generate a custom agent with AI

Start from the **Overview** page of the Agent Customizations editor to generate a custom agent from a description:

1. Open the Agent Customizations editor and select **Overview**.
2. Enter a prompt that describes the agent's role and whether it should be available in the workspace or across your workspaces.
3. Answer any clarifying questions about the role or workflow.
4. Review the generated `.agent.md` file, including its tools, instructions, and frontmatter. Correct any inaccurate information and save the file.

For example, enter the following prompt:

```
Create a workspace custom agent for code reviews. Review changes for correctness, maintainability, and consistency with this repository's conventions. Report findings without editing files.
```

You can also extract a custom agent from an ongoing conversation. For example, after a multi-turn debugging session, ask "make an agent for this kind of task" to capture the workflow as a reusable custom agent.

With **Local** selected, you can also use these shortcuts:

* Type `/create-agent` in Agent mode chat and describe the role you want.
* Select **Generate Agent** from the dropdown in the **Agents** section of the Agent Customizations editor.

These shortcuts aren't available in [Agent Host](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) sessions, such as **Copilot**. Use the **Overview** workflow instead.

## Use a custom agent

1. In the Chat view or Agents window, select the intended harness from the **Session Target** control.
2. Open the **Agent** dropdown and select your custom agent.
3. Enter a prompt for its role. For example, ask a planning agent to outline the changes needed for a new feature.
4. Review the response and any tool calls to check that the agent follows the intended instructions and uses appropriate tools.

If the agent doesn't appear, check that its file is in a [supported location](<#_custom-agent-file-locations>) for the selected harness and that its [visibility configuration](<#_customize-the-agents-dropdown-list>) permits user invocation.

## Customize the agents dropdown list

To keep a custom agent available as a [subagent](<https://code.visualstudio.com/docs/agents/run/subagents>) without listing it in the **Agent** dropdown, set `user-invocable: false` in its YAML frontmatter:

```
user-invocable: false
```

This property controls picker visibility in both **Local** and **Copilot**. To show the agent again, set it to `true` or remove the property. Subagent invocation is controlled separately by `disable-model-invocation`.

Editing this property changes the agent definition, not a personal visibility preference. If you share the file in a repository, the change applies to everyone who uses that definition. It doesn't delete the agent file.

Note

The Agent Customizations editor doesn't provide an eye-icon show or hide control for custom agents in either **Local** or **Copilot**. For agents contributed by an extension, manage the contributing extension instead. See [How do I remove a custom agent?](<#_how-do-i-remove-a-custom-agent>).

## Custom agent file locations

You can define custom agents for a specific workspace or at the user level, where they are available across all your workspaces. The following table lists the supported file locations for custom agents based on their scope.

| Scope | Default file location |
| --- | --- |
| Workspace | `.github/agents` folder |
| Workspace (Claude format) | `.claude/agents` folder |
| User | `~/.copilot/agents` or `~/.claude/agents` |

To create a user-level custom agent, use the Agent Customizations editor or the **Chat: New Custom Agent** command.

Important

For sessions that run on [Agent Host](<https://code.visualstudio.com/docs/agents/concepts/agent-host>), the agent reads user-level custom agents from the selected host's folder, such as `~/.copilot/agents` or `~/.claude/agents`, and not from VS Code profile user data. To move existing user-level custom agents, use the [user customization migration](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-user-agents-and-instructions>).

Note

The 


chat.agentFilesLocations

Open in VS Code
Open in VS Code Insiders
 and 


chat.modeFilesLocations

Open in VS Code
Open in VS Code Insiders
 settings are deprecated and only used by the Local agent. If you configured other agent locations with these settings, [migrate the customizations to supported locations](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_move-customizations-from-configured-locations>).

Tip

In a monorepo, enable 


chat.useCustomizationsInParentRepositories

Open in VS Code
Open in VS Code Insiders
 to discover custom agents from the parent repository root. Learn more about [parent repository discovery](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>).

## Custom agent file structure

Custom agent files use the `.agent.md` extension. An optional YAML header configures the agent, and the Markdown body provides its instructions.

Note

VS Code detects any `.md` files in the `.github/agents` folder of your workspace as custom agents.

### Header (optional)

The header is formatted as YAML frontmatter with the following fields:

| Field | Description |
| --- | --- |
| `description` | A brief description of the custom agent, shown as placeholder text in the chat input field. |
| `name` | The name of the custom agent. If not specified, the file name is used. |
| `argument-hint` | Optional hint text shown in the chat input field to guide users on how to interact with the custom agent. |
| `tools` | A list of tool or [tool set](<https://code.visualstudio.com/docs/agent-customization/tool-sets>) names that are available for this custom agent. Can include built-in tools, tool sets, MCP tools, or tools contributed by extensions. To include all tools of an MCP server, use the `<server name>/*` format. Learn more about [tools with agents](<https://code.visualstudio.com/docs/agents/run/tools>). |
| `agents` | A list of agent names that are available as [subagents](<https://code.visualstudio.com/docs/agents/run/subagents>) in this agent. Use `*` to allow all agents, or an empty array `[]` to prevent any subagent use. If you specify `agents`, ensure the `agent` tool is included in the `tools` property. To create a self-referential agent that lists itself in `agents`, enable    chat.subagents.allowInvocationsFromSubagents  Open in VS Code Open in VS Code Insiders . Learn more about [nested subagents](<https://code.visualstudio.com/docs/agents/run/subagents#_nested-subagents>). |
| `model` | The AI model to use when running the prompt. Specify a single model name (string) or a prioritized list of models (array). When you specify an array, the system tries each model in order until an available one is found. If not specified, the currently selected model in model picker is used. |
| `user-invocable` | Optional boolean flag to control whether the agent appears in the agents dropdown in chat (default is `true`). Set to `false` to create agents that are only accessible as [subagents](<https://code.visualstudio.com/docs/agents/run/subagents>) or programmatically. See [Customize the agents dropdown list](<#_customize-the-agents-dropdown-list>). |
| `disable-model-invocation` | Optional boolean flag to prevent the agent from being invoked as a subagent by other agents (default is `false`). |
| `infer` | **Deprecated.** Use `user-invocable` and `disable-model-invocation` instead to control picker visibility and subagent invocation independently. |
| `target` | The target environment or context for the custom agent (`vscode` or `github-copilot`). |
| `mcp-servers` | Optional list of Model Context Protocol (MCP) server config json to use with [custom agents in GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>) (target: `github-copilot`). |
| `handoffs` | Optional list of suggested next actions or prompts to transition between custom agents. Handoff buttons appear as interactive suggestions after a chat response completes. |
| `handoffs.label` | The display text shown on the handoff button. |
| `handoffs.agent` | The target agent identifier to switch to. |
| `handoffs.prompt` | The prompt text to send to the target agent. |
| `handoffs.send` | Optional boolean flag to auto-submit the prompt (default is `false`) |
| `handoffs.model` | Optional language model to use when the handoff executes. Use the qualified model name in the format `Model Name (vendor)`, for example `GPT-5 (copilot)` or `Claude Sonnet 4.5 (copilot)`. |
| `hooks` (Preview) | Optional Local hook commands scoped to this agent. Hooks defined here only run in the Local harness when this agent is active, either invoked by the user or as a subagent. Requires    chat.useHooks  Open in VS Code Open in VS Code Insiders  and a trusted workspace. See [agent-scoped hooks](<https://code.visualstudio.com/docs/agent-customization/hooks#_agent-scoped-hooks-for-local>). |

Note

If a given tool is not available when using the custom agent, it is ignored.

### Body

The Markdown body contains the instructions the agent follows, such as its task, guidelines, and expected output.

You can reference other files by using Markdown links or the `#file:` syntax, for example to reuse instructions files. Relative file paths resolve from the custom agent file. To reference your environment user home folder, use `~`, or start a path with `~/`, such as `#file:~/copilot/security-guidelines.md`. Use Unix-style `/` path separators to keep custom agent files portable across operating systems.

To reference agent tools in the body text, use the `#tool:<tool-name>` syntax. For example, to reference the `fetch` tool, use `#tool:web/fetch`.

When you select the custom agent in the Chat view, the guidelines in the custom agent file body are prepended to the user chat prompt.

### Examples

Planning agent example

The following code snippet shows an example of a "Plan" custom agent file that generates an implementation plan and doesn't make any code edits. For more community-contributed examples, see the [Awesome Copilot repository](<https://github.com/github/awesome-copilot/tree/main>).

```
---
description: Generate an implementation plan for new features or refactoring existing code.
name: Planner
tools: ['web/fetch', 'search/codebase', 'search/usages']
model: ['Claude Opus 4.5', 'GPT-5.2']  # Tries models in order
handoffs:
  - label: Implement Plan
    agent: agent
    prompt: Implement the plan outlined above.
    send: false
---
# Planning instructions
You are in planning mode. Your task is to generate an implementation plan for a new feature or for refactoring existing code.
Don't make any code edits, just generate a plan.

The plan consists of a Markdown document that describes the implementation plan, including the following sections:

* Overview: A brief description of the feature or refactoring task.
* Requirements: A list of requirements for the feature or refactoring task.
* Implementation Steps: A detailed list of steps to implement the feature or refactoring task.
* Testing: A list of tests that need to be implemented to verify the feature or refactoring task.
```


Agent orchestration example

The following example shows a "Feature Builder" agent that coordinates specialized subagents for a research-then-implement workflow. The main agent uses the `agents` property to restrict which agents can be invoked as subagents.

**feature-builder.agent.md** - The coordinating agent:

```
---
name: Feature Builder
description: Build features by researching first, then implementing
tools: ['agent']
agents: ['Researcher', 'Implementer']
---
You are a feature builder. For each task:
1. Use the Researcher agent to gather context and find relevant patterns in the codebase
2. Use the Implementer agent to make the actual code changes based on research findings
```

**researcher.agent.md** - Read-only research agent:

```
---
name: Researcher
description: Research codebase patterns and gather context
tools: ['search/codebase', 'web/fetch', 'search/usages']
---
Research thoroughly using read-only tools. Return a summary of findings.
```

**implementer.agent.md** - Code editing agent:

```
---
name: Implementer
description: Implement code changes based on provided context
tools: ['edit', 'read/terminalLastCommand']
---
Implement changes following existing code patterns. Make minimal, focused edits.
```


Agent with scoped hooks example (Preview)

The following example shows a custom agent that defines hooks in its frontmatter. The `PostToolUse` hook runs after every successful tool call and only runs when this agent is active in the Local harness. Local hooks must be enabled with 


chat.useHooks

Open in VS Code
Open in VS Code Insiders
, and the workspace must be trusted.

```
---
name: Strict Formatter
description: Agent that formats code after every successful tool call
hooks:
  PostToolUse:
    - type: command
      command: "./scripts/format-changed-files.sh"
---

You are a code editing agent. Follow the project's formatting requirements.
```

Learn more about hooks in [Agent hooks](<https://code.visualstudio.com/docs/agent-customization/hooks>).

### Claude agent format

Agent files in the `.claude/agents` folder use plain `.md` files, following the [Claude sub-agents format](<https://code.claude.com/docs/en/sub-agents>). They support Claude-specific frontmatter properties:

| Field | Description |
| --- | --- |
| `name` | Agent name (required) |
| `description` | What the agent does |
| `tools` | Comma-separated string of allowed tools (for example, `"Read, Grep, Glob, Bash"`) |
| `disallowedTools` | Comma-separated string of tools to block |

VS Code maps Claude-specific tool names to the corresponding VS Code tools. Both the VS Code `.agent.md` format (with YAML arrays for tools) and the Claude format (with comma-separated strings) are supported.

## Handoffs

Handoffs connect agents into a workflow with suggested next steps. After a chat response completes, handoff buttons let you switch to another agent with the conversation context and a pre-filled prompt.

Use handoffs to review one stage of a task before starting the next. For example:

* **Planning to implementation**: Review a plan, then hand off to an implementation agent to start coding.
* **Implementation to review**: Complete implementation, then switch to a code review agent.
* **Failing tests to passing tests**: Review generated tests, then hand off to an agent that implements the changes needed to pass them.

To define handoffs, add them to the agent's frontmatter. Each handoff specifies the target agent, the button label, and an optional prompt:

```
---
description: Generate an implementation plan
tools: ['search', 'web']
handoffs:
  - label: Start Implementation
    agent: implementation
    prompt: Now implement the plan outlined above.
    send: false
    model: GPT-5.2 (copilot)
---
```

When you select a handoff button, you switch to the target agent with the prompt pre-filled. If `send: true`, the prompt submits automatically.

## Tool list priority

With **Local**, when you use `tools` in both a custom agent and a prompt file, the prompt file's tools take precedence. Agent Host sessions don't load prompt files. For the full priority order, see [Tool list priority](<https://code.visualstudio.com/docs/agent-customization/prompt-files#_tool-list-priority>) in the prompt files documentation.

## Share custom agents across teams

To share custom agents across your team, you can create a workspace-level custom agent (`.github/agents` folder). If you want to share custom agents across multiple workspaces within your organization, you can define them at the GitHub organization level.

VS Code automatically detects custom agents defined at the organization level to which your account has access. These agents appear in the Agents dropdown in chat alongside the built-in agents, and your personal and workspace custom agents.

To enable discovery of organization-level custom agents, set 


github.copilot.chat.organizationCustomAgents.enabled

Open in VS Code
Open in VS Code Insiders
 to `true`.

Learn how you can [create custom agents for your organization](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents>) in the GitHub documentation.

## Frequently asked questions

### Are custom agents different from chat modes?

Custom agents were previously known as custom chat modes. The functionality remains the same, but the terminology has been updated to better reflect their purpose in customizing AI behavior for specific tasks.

If you have existing `.chatmode.md` files, rename them to `.agent.md` and place them in one of the [supported custom agent locations](<#_custom-agent-file-locations>).

### How do I remove a custom agent?

To remove a custom agent you maintain, use either of these methods:

* Delete its file from the workspace or [user-level location](<#_custom-agent-file-locations>).
* Select **Configure Custom Agents** from the agents dropdown to open the Agent Customizations editor, hover over the custom agent, and select the trash icon.

Deleting a shared workspace agent also removes it for others when they receive the repository change. To keep the definition but remove it from the picker, see [Customize the agents dropdown list](<#_customize-the-agents-dropdown-list>).

You can't delete extension-provided agents from the Agent Customizations editor. Disable or uninstall the contributing extension to remove its agents. This also affects the extension's other features.

### How do I know where a custom agent comes from?

The agents dropdown includes built-in roles and custom agents from user-level files, your workspace, organizations, extensions, and plugins.

To identify the source of a custom agent:

1. Select **Configure Custom Agents** from the agents dropdown.
2. Hover over the custom agent in the list. The source location is displayed in a tooltip.

Tip

Use the chat customization diagnostics view to see all loaded custom agents, prompt files, instruction files, and skills along with any errors. Right-click in the Chat view and select **Diagnostics**. Learn more about [troubleshooting AI in VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>).

## Security considerations

Custom agents can restrict which tools are available, which gives you control over what the AI can do. For security-sensitive workflows, create agents with read-only tools to prevent unintended modifications. When sharing agents in a repository, review the tool list and instructions to ensure they follow the principle of least privilege.

## Related resources

* [Planning with agents](<https://code.visualstudio.com/docs/agents/run/planning>)
* [Customize AI with custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [Use tools with agents](<https://code.visualstudio.com/docs/agents/run/tools>)

10/7/2026


</details>
