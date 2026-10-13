# 在 VS Code 中使用提示文件

介绍可复用提示的位置、格式、文件引用、调用与迁移，说明工具列表优先级和任务模板组织方式。

- 来源：Microsoft／VS Code
- 资料类型：存档文档
- 文档时间：官方审核：[2026-10-07](<https://code.visualstudio.com/docs/agent-customization/prompt-files>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/eeb6997081a1b674fb894129737e40ffbe7dfc05>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.visualstudio.com/docs/agent-customization/prompt-files>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/在 VS Code 中使用提示文件.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用VS Code中的提示文件

* [以 Markdown 形式查看](<https://code.visualstudio.com/docs/agent-customization/prompt-files.md>)
* [在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问
* [在VS Code内部人士中提问](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[在VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)中询问

---

提示文件（也称为斜杠命令）可让您通过将它们编码为可在聊天中直接调用的独立 Markdown 文件来简化常见任务的提示。每个提示文件都包含特定于任务的上下文以及有关如何执行任务的指南。

与自动应用的[自定义指令](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)不同，您可以在聊天中手动调用提示文件。请参阅 [ 决策矩阵 ](<https://code.visualstudio.com/docs/agents/concepts/customization#_customization-options-at-a-glance>) 将提示文件与自定义 agents、agent skills 和其他选项进行比较。

使用提示文件可以：

* 简化常见任务的提示，例如搭建新组件、运行和修复测试或准备拉取请求
* 覆盖自定义 agent 的默认行为，例如创建最小实施计划或为 API 调用生成模型

您可以使用 [Agent 自定义编辑器 ](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>)（预览版）在一处发现、创建和管理所有 agent 自定义。运行 **Chat：从命令面板打开自定义 **。

重要

[Agent 主机](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) 会话已弃用提示文件，并且 Agent 主机不会加载提示文件。目前，他们继续使用本地 agent，但本地 agent 将在未来版本中删除。使用[提示文件迁移](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_convert-prompt-files-to-skills>)将现有提示转换为agent skills。

## 提示文件位置

您可以为特定工作区或用户级别定义提示文件，这些文件可在所有工作区中使用。下表列出了本地 agent 用于提示文件的默认文件位置。

|适用范围|默认文件位置|
| --- | --- |
|工作空间|`.github/prompts`文件夹|
|用户资料|您的用户数据（特定于您的 VS Code 个人资料）|

要创建用户级提示文件，请使用 Agent 自定义编辑器或 **Chat：新建提示文件 ** 命令。

提示

在 monorepo 中，启用


聊天。useCustomizationsInParentRepositories

在 VS Code
 中打开 在 VS Code Insiders
 中打开，以从父存储库根目录中发现提示文件。了解有关 [ 父存储库发现 ](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>) 的更多信息。

## 提示文件格式

提示文件是带有 `.prompt.md` 扩展名的 Markdown 文件。可选的 YAML frontmatter 标头配置提示的行为：

|领域|必填|描述|
| --- | --- | --- |
| `description` |否|提示的简短描述。|
| `name` |否|提示名称，在聊天中输入`/`后使用。如果未指定，则使用文件名。|
| `argument-hint` |否|聊天输入字段中显示的提示文本可指导用户如何与提示进行交互。|
| `agent` |否|用于运行提示符的 agent：`ask`、`agent`、`plan` 或 [ 自定义 agent](<https://code.visualstudio.com/docs/agent-customization/custom-agents>) 的名称。默认情况下，使用当前的agent。如果指定了工具，则默认agent为`agent`。|
| `model` |否|运行提示时使用的语言模型。如果未指定，则使用模型选择器中当前选择的模型。|
| `tools` |否| 工具列表或 [工具组](<https://code.visualstudio.com/docs/agent-customization/tool-sets>) 此提示可用的名称。可以包括内置工具、工具集、 MCP 工具，或扩展贡献的工具。包括所有工具 MCP 服务器，使用 `<server name>/*` 格式。了解更多关于 [工具与 agents](<https://code.visualstudio.com/docs/agents/run/tools>). |

注意事项

如果运行提示时给定工具不可用，则会忽略该工具。

正文包含 Markdown 格式的提示文本。提供您希望 AI 遵循的具体说明、指南或任何其他相关信息。

### 参考文件

您可以使用 Markdown 链接或 `#file:` 语法引用提示正文中的文件。

* 对于工作区中的文件，请使用相对于提示文件的路径。例如，`[coding standards](../instructions/coding.instructions.md)` 引用同级 `instructions` 文件夹中的文件。
* 对于环境用户主文件夹中的文件，请使用 `~` 或以 `~/` 开头的路径。独立的 `~` 引用用户主文件夹，并且从该文件夹解析诸如 `~/prompts/style-guide.md` 之类的路径。

使用 Unix 风格的 `/` 路径分隔符来保持提示文件跨操作系统的可移植性。

以下示例使用两种引用格式来引用用户主文件夹中的文件：

```
Review [my personal instructions](~/copilot/instructions.md).
Use #file:~/templates/component.md as the component template.
```

要在正文中引用 agent 工具，请使用 `#tool:<tool-name>` 语法。例如，要引用 `browser` 工具，请使用 `#tool:browser`。

提示

如果您希望用户提供额外的信息，可以使用`vscode/askQuestion`工具。您还可以使用 `${input:variableName}`、`${input:variableName:placeholder}` 等语法。大多数语言模型都理解这种语法并会提示输入这些输入。

以下示例演示如何使用提示文件。有关更多社区贡献的示例，请参阅 [Awesome Copilot 存储库 ](<https://github.com/github/awesome-copilot/tree/main>)。

示例：生成一个React表单组件

```
---
agent: 'agent'
model: GPT-4o
tools: ['search/codebase', 'vscode/askQuestions']
description: 'Generate a new React form component'
---
Your goal is to generate a new React form component based on the templates in the Github repo contoso/react-templates.

Use the #tool:vscode/askQuestions to ask for the form name and fields if not provided.

Requirements for the form:
* Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
* Use `react-hook-form` for form state management:
* Always define TypeScript types for your form data
* Prefer *uncontrolled* components using register
* Use `defaultValues` to prevent unnecessary rerenders
* Use `yup` for validation:
* Create reusable validation schemas in separate files
* Use TypeScript types to ensure type safety
* Customize UX-friendly validation rules
```


示例：对 REST API 执行安全审查

```
---
agent: 'ask'
model: Claude Sonnet 4
description: 'Perform a REST API security review'
---
Perform a REST API security review and provide a TODO list of security issues to address.

* Ensure all endpoints are protected by authentication and authorization
* Validate all user inputs and sanitize data
* Implement rate limiting and throttling
* Implement logging and monitoring for security events

Return the TODO list in a Markdown format, grouped by priority and issue type.
```

## 创建提示文件

创建提示文件时，选择是将其存储在工作区还是用户配置文件中。工作区提示文件仅适用于该工作区，而用户提示文件可跨多个工作区使用。

要创建提示文件：

提示

在聊天输入中输入`/prompts`即可快速打开**配置提示文件**菜单。

1. 在聊天视图中，选择 ** 配置聊天 **（齿轮图标）以打开 Agent 自定义编辑器，然后选择 ** 提示 ** 选项卡。
2. 从下拉列表中选择 **新提示（工作空间）** 或 **新提示（用户）**，具体取决于您要存储提示文件的位置。

![Agent 自定义编辑器的屏幕截图，显示“提示”选项卡和用于创建新提示文件的下拉列表。](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-prompt-file.png>)

或者，使用命令面板中的 **Chat：新建提示文件 ** 或 **Chat：新建无标题提示文件 ** 命令 (⇧⌘P (Windows、Linux Ctrl+Shift+P))。
3. 选择提示文件的位置并输入文件名。这是您在聊天中键入 `/` 时显示的默认名称。
4. 使用 Markdown 格式创作聊天提示。

   * 填写文件顶部的 YAML frontmatter 以配置提示的描述、agent、工具和其他设置。
   * 在文件正文中添加提示说明。

您可以通过在 Agent 自定义编辑器中打开现有提示文件来修改它们。

### 生成带有 AI 的提示文件

您可以使用AI根据任务描述生成提示文件。在聊天中输入 `/create-prompt` 并描述您想要自动化的任务（例如，“生成单元测试的提示”）。 agent 提出澄清问题，生成包含适当前言和说明的 `.prompt.md` 文件，并为您提供工作区和用户存储之间的选择。

您还可以从正在进行的对话中提取可重复使用的提示。例如，在多轮聊天会话后，询问“将此转换为可重用的提示”或“将此工作流程另存为提示”，agent 将创建一个捕获工作流程的提示文件。

您还可以通过从下拉列表中选择 ** 生成提示 **，从 Agent 自定义编辑器生成提示文件。

## 在聊天中使用提示文件

您有多种选项来运行提示文件：

* 在聊天视图中，键入 `/`，然后在聊天输入字段中键入提示名称。 [Agent skills](<https://code.visualstudio.com/docs/agent-customization/agent-skills>) 也作为斜杠命令与提示文件一起出现。

您可以在聊天输入字段中添加额外信息。例如，`/create-react-form formName=MyForm` 或 `/create-api for listing customers`。
* 运行 **Chat：从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 运行 Prompt** 命令，然后从快速选择中选择提示文件。
* 在编辑器中打开提示文件，然后按编辑器标题区域中的播放按钮。您可以选择在当前聊天会话中运行提示或打开新的聊天会话。

此选项对于快速测试和迭代提示文件非常有用。

提示

使用


聊天。promptFilesRecommendations

在 VS Code
中打开 在 VS Code Insiders
 设置中打开，以在开始新的聊天会话时显示提示作为建议操作。

![在聊天视图中显示“解释”提示文件推荐的屏幕截图。](<https://code.visualstudio.com/assets/docs/agent-customization/customization/prompt-file-recommendations.png>)

## 工具列表优先级

您可以使用 `tools` 元数据字段指定自定义 agent 和提示文件的可用工具列表。提示文件还可以使用 `agent` 元数据字段引用自定义 agent。

聊天中的可用工具列表由以下优先级顺序确定：

1. 提示文件中指定的工具（如果有）
2. 提示文件中引用的自定义 agent 中的工具（如果有）
3. 所选 agent 的默认工具（如果有）

## 跨设备同步用户提示文件

VS Code可以使用[设置同步](<https://code.visualstudio.com/docs/configure/settings-sync>)在多个设备上同步您的用户提示文件。

要同步用户提示文件，请启用设置同步并运行 **设置同步：从命令面板配置 ** (⇧⌘P (Windows、Linux Ctrl+Shift+P))。从要同步的设置列表中选择**提示和说明**。

## 撰写有效提示的技巧

* 清楚地描述提示应该完成什么以及预期的输出格式。
* 提供预期输入和输出的示例，以指导 AI 的响应。
* 使用 Markdown 链接来引用自定义说明，而不是在每个提示中重复指南。
* 利用 `${selection}` 等内置变量和输入变量使提示更加灵活。
* 使用编辑器播放按钮测试您的提示并根据结果完善它们。

## 常见问题

### 我如何知道提示文件来自哪里？

提示文件可以来自不同的来源：内置的、配置文件中用户定义的、当前工作区中工作区定义的提示或扩展提供的提示。

要识别提示文件的来源：

1. 从命令面板 (⇧⌘P (Windows、Linux Ctrl+Shift+P)) 中选择 ** 聊天：配置提示文件 **。
2. 将鼠标悬停在列表中的提示文件上。源位置显示在工具提示中。

提示

使用 Agent 调试日志面板用于检查提示文件发现和相关错误。在聊天视图中，选择省略号 (**...**) 菜单，然后选择 **显示 Agent 调试日志**。了解更多关于 [故障排除 AI 在 VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>).

## 相关资源

* [创建自定义指令](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [配置agent工具](<https://code.visualstudio.com/docs/agents/run/tools>)
* [社区贡献的说明、提示和自定义 agents](<https://github.com/github/awesome-copilot>)

10/7/2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Use prompt files in VS Code

* [View as Markdown](<https://code.visualstudio.com/docs/agent-customization/prompt-files.md>)
* [Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)
* [Ask in VS Code Insiders](<vscode-insiders://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

[Ask in VS Code](<vscode://GitHub.Copilot-Chat/chat?prompt=Read%20https%3A%2F%2Fcode.visualstudio.com%2Fdocs%2Fagent-customization%2Fprompt-files%20and%20answer%20questions%20about%20the%20content.&windowId=_blank>)

---

Prompt files, also known as slash commands, let you simplify prompting for common tasks by encoding them as standalone Markdown files that you can invoke directly in chat. Each prompt file includes task-specific context and guidelines about how the task should be performed.

Unlike [custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>) that are applied automatically, you invoke prompt files manually in chat. See the [decision matrix](<https://code.visualstudio.com/docs/agents/concepts/customization#_customization-options-at-a-glance>) to compare prompt files with custom agents, agent skills, and the other options.

Use prompt files to:

* Simplify prompting for common tasks, such as scaffolding a new component, running and fixing tests, or preparing a pull request
* Override default behavior of a custom agent, such as creating a minimal implementation plan or generating mockups for API calls

You can use the [Agent Customizations editor](<https://code.visualstudio.com/docs/agent-customization/overview#_agent-customizations-editor>) (Preview) to discover, create, and manage all your agent customizations in one place. Run **Chat: Open Customizations** from the Command Palette.

Important

Prompt files are deprecated for [Agent Host](<https://code.visualstudio.com/docs/agents/concepts/agent-host>) sessions and aren't loaded by Agent Host. They continue to work with the Local agent for now, but the Local agent will be removed in a future release. Use [prompt file migration](<https://code.visualstudio.com/docs/agent-customization/migrate-customizations#_convert-prompt-files-to-skills>) to convert existing prompts to agent skills.

## Prompt file locations

You can define prompt files for a specific workspace or at the user level, where they are available across all your workspaces. The following table lists the default file locations that the Local agent uses for prompt files.

| Scope | Default file location |
| --- | --- |
| Workspace | `.github/prompts` folder |
| User profile | Your user data (specific to your VS Code profile) |

To create a user-level prompt file, use the Agent Customizations editor or the **Chat: New Prompt File** command.

Tip

In a monorepo, enable 


chat.useCustomizationsInParentRepositories

Open in VS Code
Open in VS Code Insiders
 to discover prompt files from the parent repository root. Learn more about [parent repository discovery](<https://code.visualstudio.com/docs/agent-customization/overview#_use-customizations-in-a-monorepo>).

## Prompt file format

Prompt files are Markdown files with the `.prompt.md` extension. The optional YAML frontmatter header configures the prompt's behavior:

| Field | Required | Description |
| --- | --- | --- |
| `description` | No | A short description of the prompt. |
| `name` | No | The name of the prompt, used after typing `/` in chat. If not specified, the file name is used. |
| `argument-hint` | No | Hint text shown in the chat input field to guide users on how to interact with the prompt. |
| `agent` | No | The agent used for running the prompt: `ask`, `agent`, `plan`, or the name of a [custom agent](<https://code.visualstudio.com/docs/agent-customization/custom-agents>). By default, the current agent is used. If tools are specified, the default agent is `agent`. |
| `model` | No | The language model used when running the prompt. If not specified, the currently selected model in model picker is used. |
| `tools` | No | A list of tool or [tool set](<https://code.visualstudio.com/docs/agent-customization/tool-sets>) names that are available for this prompt. Can include built-in tools, tool sets, MCP tools, or tools contributed by extensions. To include all tools of an MCP server, use the `<server name>/*` format. Learn more about [tools with agents](<https://code.visualstudio.com/docs/agents/run/tools>). |

Note

If a given tool is not available when running the prompt, it is ignored.

The body contains the prompt text in Markdown format. Provide specific instructions, guidelines, or any other relevant information that you want the AI to follow.

### Reference files

You can reference files in the prompt body by using Markdown links or the `#file:` syntax.

* For files in your workspace, use paths relative to the prompt file. For example, `[coding standards](../instructions/coding.instructions.md)` references a file in a sibling `instructions` folder.
* For files in your environment user home folder, use `~` or start the path with `~/`. A standalone `~` references the user home folder, and a path such as `~/prompts/style-guide.md` resolves from that folder.

Use Unix-style `/` path separators to keep prompt files portable across operating systems.

The following example uses both reference formats to reference files in the user home folder:

```
Review [my personal instructions](~/copilot/instructions.md).
Use #file:~/templates/component.md as the component template.
```

To reference agent tools in the body text, use the `#tool:<tool-name>` syntax. For example, to reference the `browser` tool, use `#tool:browser`.

Tip

If you want the user to provide additional information, you can use the `vscode/askQuestion` tool. You can also use a syntax like `${input:variableName}`, `${input:variableName:placeholder}`. Most language models understand this syntax and will prompt for these inputs.

The following examples demonstrate how to use prompt files. For more community-contributed examples, see the [Awesome Copilot repository](<https://github.com/github/awesome-copilot/tree/main>).

Example: generate a React form component

```
---
agent: 'agent'
model: GPT-4o
tools: ['search/codebase', 'vscode/askQuestions']
description: 'Generate a new React form component'
---
Your goal is to generate a new React form component based on the templates in the Github repo contoso/react-templates.

Use the #tool:vscode/askQuestions to ask for the form name and fields if not provided.

Requirements for the form:
* Use form design system components: [design-system/Form.md](../docs/design-system/Form.md)
* Use `react-hook-form` for form state management:
* Always define TypeScript types for your form data
* Prefer *uncontrolled* components using register
* Use `defaultValues` to prevent unnecessary rerenders
* Use `yup` for validation:
* Create reusable validation schemas in separate files
* Use TypeScript types to ensure type safety
* Customize UX-friendly validation rules
```


Example: perform a security review of a REST API

```
---
agent: 'ask'
model: Claude Sonnet 4
description: 'Perform a REST API security review'
---
Perform a REST API security review and provide a TODO list of security issues to address.

* Ensure all endpoints are protected by authentication and authorization
* Validate all user inputs and sanitize data
* Implement rate limiting and throttling
* Implement logging and monitoring for security events

Return the TODO list in a Markdown format, grouped by priority and issue type.
```

## Create a prompt file

When you create a prompt file, choose whether to store it in your workspace or user profile. Workspace prompt files apply only to that workspace, while user prompt files are available across multiple workspaces.

To create a prompt file:

Tip

Type `/prompts` in the chat input to quickly open the **Configure Prompt Files** menu.

1. In the Chat view, select **Configure Chat** (gear icon) to open the Agent Customizations editor and then select the **Prompts** tab.
2. Select **New Prompt (Workspace)** or **New Prompt (User)** from the dropdown, depending on where you want to store the prompt file.

   ![Screenshot of the Agent Customizations editor, showing the Prompts tab and the dropdown to create a new prompt file.](<https://code.visualstudio.com/assets/docs/agent-customization/customization/create-prompt-file.png>)

   Alternatively, use the **Chat: New Prompt File** or **Chat: New Untitled Prompt File** command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
3. Select the location and enter a file name for your prompt file. This is the default name that appears when you type `/` in chat.
4. Author the chat prompt by using Markdown formatting.

   * Fill in the YAML frontmatter at the top of the file to configure the prompt's description, agent, tools, and other settings.
   * Add instructions for the prompt in the body of the file.

You can modify existing prompt files by opening them in the Agent Customizations editor.

### Generate a prompt file with AI

You can use AI to generate a prompt file based on a description of the task. Type `/create-prompt` in chat and describe the task you want to automate (for example, "a prompt for generating unit tests"). The agent asks clarifying questions, generates a `.prompt.md` file with appropriate frontmatter and instructions, and offers you a choice between workspace and user storage.

You can also extract a reusable prompt from an ongoing conversation. For example, after a multi-turn chat session, ask "turn this into a reusable prompt" or "save this workflow as a prompt", and the agent creates a prompt file that captures the workflow.

You can also generate a prompt file from the Agent Customizations editor by selecting **Generate Prompt** from the dropdown.

## Use a prompt file in chat

You have multiple options to run a prompt file:

* In the Chat view, type `/` followed by the prompt name in the chat input field. [Agent skills](<https://code.visualstudio.com/docs/agent-customization/agent-skills>) also appear as slash commands alongside prompt files.

  You can add extra information in the chat input field. For example, `/create-react-form formName=MyForm` or `/create-api for listing customers`.
* Run the **Chat: Run Prompt** command from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)) and select a prompt file from the Quick Pick.
* Open the prompt file in the editor, and press the play button in the editor title area. You can choose to run the prompt in the current chat session or open a new chat session.

  This option is useful for quickly testing and iterating on your prompt files.

Tip

Use the 


chat.promptFilesRecommendations

Open in VS Code
Open in VS Code Insiders
 setting to show prompts as recommended actions when starting a new chat session.

![Screenshot showing a "explain" prompt file recommendation in the Chat view.](<https://code.visualstudio.com/assets/docs/agent-customization/customization/prompt-file-recommendations.png>)

## Tool list priority

You can specify the list of available tools for both a custom agent and prompt file by using the `tools` metadata field. Prompt files can also reference a custom agent by using the `agent` metadata field.

The list of available tools in chat is determined by the following priority order:

1. Tools specified in the prompt file (if any)
2. Tools from the referenced custom agent in the prompt file (if any)
3. Default tools for the selected agent (if any)

## Sync user prompt files across devices

VS Code can sync your user prompt files across multiple devices by using [Settings Sync](<https://code.visualstudio.com/docs/configure/settings-sync>).

To sync your user prompt files, enable Settings Sync and run **Settings Sync: Configure** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)). Select **Prompts and Instructions** from the list of settings to sync.

## Tips for writing effective prompts

* Clearly describe what the prompt should accomplish and what output format is expected.
* Provide examples of the expected input and output to guide the AI's responses.
* Use Markdown links to reference custom instructions rather than duplicating guidelines in each prompt.
* Take advantage of built-in variables like `${selection}` and input variables to make prompts more flexible.
* Use the editor play button to test your prompts and refine them based on the results.

## Frequently asked questions

### How do I know where a prompt file comes from?

Prompt files can come from different sources: built-in, user-defined in your profile, workspace-defined prompts in your current workspace, or extension-contributed prompts.

To identify the source of a prompt file:

1. Select **Chat: Configure Prompt Files** from the Command Palette (⇧⌘P (Windows, Linux Ctrl+Shift+P)).
2. Hover over the prompt file in the list. The source location is displayed in a tooltip.

Tip

Use the Agent Debug Log panel to inspect prompt file discovery and related errors. In the Chat view, select the ellipsis (**...**) menu, then select **Show Agent Debug Logs**. Learn more about [troubleshooting AI in VS Code](<https://code.visualstudio.com/docs/agents/agent-troubleshooting/troubleshooting>).

## Related resources

* [Create custom instructions](<https://code.visualstudio.com/docs/agent-customization/custom-instructions>)
* [Configure agent tools](<https://code.visualstudio.com/docs/agents/run/tools>)
* [Community contributed instructions, prompts, and custom agents](<https://github.com/github/awesome-copilot>)

10/7/2026


</details>
