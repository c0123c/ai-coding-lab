# VS Code 工具与上下文引用速查

区分执行工具与上下文项，列出文件搜索编辑、命令测试、网络、GitHub 和委派入口，帮助准确组织提示。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/tools-reference.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/tools-reference.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 工具与上下文引用速查.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 6257086f-4936-4f53-bfa1-fec1cfd4dfb5
DateApproved: 10/7/2026
MetaDescription: Look up tools, tool sets, and context references for AI chat in {% data variables.product.prodname_vscode_shortname %}.
MetaSocialImage: ../images/chat-tools/agent-mode-select-tools.png
Keywords:
- agents
- chat
- tools
- tool sets
- context
- reference
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 工具和上下文参考

在 {% data variables.product.prodname_vscode %} 中查找聊天提示的常用工具名称和上下文参考。有关选择工具、调用工具以及管理批准的说明，请参阅[将工具与agents](<https://github.com/docs/agents/run/tools.md>)一起使用。

## 参考文献类型和可用性

`#` 选择器包含用于不同目的的参考：

* **工具**执行操作，例如读取文件或运行命令。例如，`#search/textSearch` 搜索文件内容。
* **工具集**组相关工具。例如，`#search` 对工作区搜索工具进行分组。
* **上下文项**向提示提供信息，而不要求agent执行操作。例如，`#selection` 添加选定的编辑器文本。

可用的参考取决于您的线束、启用的扩展和会话功能。在聊天输入中输入 `#` 以查看当前列表。扩展和 MCP 服务器可以提供此处列出的其他工具之外的其他工具。

引用工具不会绕过其 [ 批准和权限控制 ](<https://github.com/docs/agents/run/approvals.md>)。

## 工具和工具组

### 读取和搜索工作区文件

|参考|目的|
|---|---|
|`#read`（工具组）|读取工作区文件和执行输出。|
| `#read/readFile` |读取文件。|
| `#read/problems` |从 ** 问题 ** 面板获取诊断信息。|
|`#search`（工具组）|搜索工作区。|
| `#search/changes` |列出源代码控制更改。|
| `#search/codebase` |通过工作区代码搜索查找相关代码。|
| `#search/fileSearch` |通过 glob 模式查找文件。|
| `#search/listDirectory` |列出目录内容。|
| `#search/textSearch` |在文件中查找文本。|
| `#search/usages` |查找符号的参考、实现和定义。|

### 编辑文件并使用笔记本

|参考|目的|
|---|---|
|`#edit`（工具组）|修改工作区文件和笔记本。|
| `#edit/createDirectory` |创建一个目录。|
| `#edit/createFile` |创建一个文件。|
| `#edit/editFiles` |将编辑应用到文件。|
| `#edit/editNotebook` |编辑笔记本。|
| `#read/getNotebookSummary` |列出笔记本单元及其详细信息。|
| `#read/readNotebookCellOutput` |读取笔记本单元的输出。|
| `#execute/runNotebookCell` |运行笔记本单元。|

### 运行命令和诊断测试

|参考|目的|
|---|---|
|`#execute`（工具组）|运行命令、任务和笔记本单元。|
| `#execute/createAndRunTask` |创建并运行 [ 工作区任务 ](<https://github.com/docs/debugtest/tasks.md>)。|
| `#execute/getTerminalOutput` |从正在运行的终端命令获取输出。|
| `#execute/runInTerminal` |在集成终端中运行 shell 命令。|
| `#execute/testFailure` |获取单元测试失败信息。|
| `#read/terminalLastCommand` |获取最后一个终端命令及其输出。|
| `#read/terminalSelection` |获取当前终端选择。|

### 访问网络并 GitHub

|参考|目的|
|---|---|
|`#browser`（工具组）|使用 [ 浏览器工具 ](<https://github.com/docs/agents/run/browser-tools.md>) 导航、检查页面并与之交互。|
|`#web`（工具组）|访问网页内容。|
| `#web/fetch` |获取一个网页。|
| `#githubRepo` |按语义搜索 GitHub 存储库。将存储库指定为 `owner/repo`。|
| `#githubTextSearch` |在 GitHub 存储库或组织中搜索文本或代码模式。|

### 委派和跟踪工作

|参考|目的|
|---|---|
|`#agent`（工具组）|将任务委派给 [ 子代理 ](<https://github.com/docs/agents/run/subagents.md>)。|
| `#agent/runSubagent` |在隔离的子代理上下文中运行任务。|
| `#todos` |使用待办事项列表跟踪任务进度。|
| `#vscode/askQuestions` |通过交互式控件提出澄清问题。|

有关跨 Agent 主机会话协调工作的工具，请参阅 [ 会话编排 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#orchestrate-sessions-from-agent-host-sessions>)。

### 配置项目和编辑器

|参考|目的|
|---|---|
| `#newWorkspace` |创建一个工作区。|
| `#vscode/extensions` |搜索扩展及其相关信息。|
| `#vscode/getProjectSetupInfo` |获取项目脚手架说明和配置。|
| `#vscode/installExtension` |安装扩展。|
| `#vscode/runCommand` |运行 {% data variables.product.prodname_vscode_shortname %} 命令。|
| `#vscode/VSCodeAPI` |获取有关编辑器功能和扩展 API 的信息。|

## 上下文项目

键入 `#`，后跟文件、文件夹或符号名称，然后从选取器中选择项目。下面的占位符代表您要附加的项目的名称。

|参考|提供上下文|
|---|---|
| `#<file>` |从上下文选择器中选择的文件。|
| `#<folder>` |从上下文选择器中选择的文件夹。|
| `#<symbol>` |一个代码符号。首先打开包含该符号的文件。|
| `#selection` |当前编辑器选择，在选择文本时可用。|

使用 ** 添加上下文 ** 或将项目拖到聊天中以附加其他上下文，例如图像、错误或 GitHub 问题。请参阅 [向聊天添加上下文 ](<https://github.com/docs/chat/copilot-chat-context.md>) 了解支持的来源和附件方法。

## 相关资源

* [了解agent循环](<https://github.com/docs/agents/concepts/tools.md>)中的工具。
* [创建和使用工具集](<https://github.com/docs/agent-customization/tool-sets.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tools and context reference

Look up common tool names and context references for chat prompts in {% data variables.product.prodname_vscode %}. For instructions on selecting tools, invoking them, and managing approvals, see [Use tools with agents](<https://github.com/docs/agents/run/tools.md>).

## Reference types and availability

The `#` picker includes references that serve different purposes:

* **Tools** perform actions, such as reading a file or running a command. For example, `#search/textSearch` searches file contents.
* **Tool sets** group related tools. For example, `#search` groups workspace search tools.
* **Context items** supply information to a prompt without asking the agent to perform an action. For example, `#selection` adds the selected editor text.

Available references depend on your harness, enabled extensions, and session capabilities. Type `#` in the chat input to see the current list. Extensions and MCP servers can provide additional tools beyond those listed here.

Referencing a tool does not bypass its [approval and permission controls](<https://github.com/docs/agents/run/approvals.md>).

## Tools and tool sets

### Read and search workspace files

| Reference | Purpose |
|---|---|
| `#read` (tool set) | Read workspace files and execution output. |
| `#read/readFile` | Read a file. |
| `#read/problems` | Get diagnostics from the **Problems** panel. |
| `#search` (tool set) | Search the workspace. |
| `#search/changes` | List source control changes. |
| `#search/codebase` | Find relevant code with workspace code search. |
| `#search/fileSearch` | Find files by a glob pattern. |
| `#search/listDirectory` | List directory contents. |
| `#search/textSearch` | Find text in files. |
| `#search/usages` | Find references, implementations, and definitions of symbols. |

### Edit files and work with notebooks

| Reference | Purpose |
|---|---|
| `#edit` (tool set) | Modify workspace files and notebooks. |
| `#edit/createDirectory` | Create a directory. |
| `#edit/createFile` | Create a file. |
| `#edit/editFiles` | Apply edits to files. |
| `#edit/editNotebook` | Edit a notebook. |
| `#read/getNotebookSummary` | List notebook cells and their details. |
| `#read/readNotebookCellOutput` | Read the output from a notebook cell. |
| `#execute/runNotebookCell` | Run a notebook cell. |

### Run commands and diagnose tests

| Reference | Purpose |
|---|---|
| `#execute` (tool set) | Run commands, tasks, and notebook cells. |
| `#execute/createAndRunTask` | Create and run a [workspace task](<https://github.com/docs/debugtest/tasks.md>). |
| `#execute/getTerminalOutput` | Get output from a running terminal command. |
| `#execute/runInTerminal` | Run a shell command in the integrated terminal. |
| `#execute/testFailure` | Get unit test failure information. |
| `#read/terminalLastCommand` | Get the last terminal command and its output. |
| `#read/terminalSelection` | Get the current terminal selection. |

### Access the web and GitHub

| Reference | Purpose |
|---|---|
| `#browser` (tool set) | Navigate, inspect, and interact with pages using [browser tools](<https://github.com/docs/agents/run/browser-tools.md>). |
| `#web` (tool set) | Access web content. |
| `#web/fetch` | Fetch a web page. |
| `#githubRepo` | Search a GitHub repository semantically. Specify the repository as `owner/repo`. |
| `#githubTextSearch` | Search a GitHub repository or organization for text or code patterns. |

### Delegate and track work

| Reference | Purpose |
|---|---|
| `#agent` (tool set) | Delegate tasks to [subagents](<https://github.com/docs/agents/run/subagents.md>). |
| `#agent/runSubagent` | Run a task in an isolated subagent context. |
| `#todos` | Track task progress with a todo list. |
| `#vscode/askQuestions` | Ask clarifying questions with interactive controls. |

For tools that coordinate work across Agent Host sessions, see [session orchestration](<https://github.com/docs/agents/run/sessions/manage-sessions.md#orchestrate-sessions-from-agent-host-sessions>).

### Configure projects and the editor

| Reference | Purpose |
|---|---|
| `#newWorkspace` | Create a workspace. |
| `#vscode/extensions` | Search for extensions and information about them. |
| `#vscode/getProjectSetupInfo` | Get project-scaffolding instructions and configuration. |
| `#vscode/installExtension` | Install an extension. |
| `#vscode/runCommand` | Run a {% data variables.product.prodname_vscode_shortname %} command. |
| `#vscode/VSCodeAPI` | Get information about editor functionality and extension APIs. |

## Context items

Type `#` followed by a file, folder, or symbol name, then select the item from the picker. The placeholders below represent the name of the item you want to attach.

| Reference | Context supplied |
|---|---|
| `#<file>` | A file selected from the context picker. |
| `#<folder>` | A folder selected from the context picker. |
| `#<symbol>` | A code symbol. Open the file containing the symbol first. |
| `#selection` | The current editor selection, available when text is selected. |

Use **Add Context** or drag items into chat to attach other context, such as images, errors, or GitHub issues. See [Add context to chat](<https://github.com/docs/chat/copilot-chat-context.md>) for supported sources and attachment methods.

## Related resources

* [Understand tools in the agent loop](<https://github.com/docs/agents/concepts/tools.md>).
* [Create and use tool sets](<https://github.com/docs/agent-customization/tool-sets.md>).


</details>
