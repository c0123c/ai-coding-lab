# Gemini CLI 工具参考

介绍文件、命令、MCP、记忆、规划和网络工具的发现、配置、手动调用及执行审批，帮助理解操作能力与范围。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-09-01](<https://github.com/google-gemini/gemini-cli/commit/4963a4456a886bb6af7dcfb807ad6e3e46ce46fc>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/tools.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/reference/tools.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 工具参考

Gemini CLI 使用工具与您的本地环境交互、访问 
 信息并代表您执行操作。这些工具将模型的 
 功能扩展到文本生成之外，使其能够读取文件、执行命令、
 和搜索网络。

## 如何使用Gemini CLI的工具

当需要执行
某个操作时，工具通常由Gemini CLI自动调用。但是，您也可以使用简写
语法手动触发特定工具。

### 自动执行和安全

当模型想要使用工具时，Gemini CLI 会根据其 
 安全策略评估请求。

- **用户确认：**您必须手动批准修改文件或
执行 shell 命令（修改器）。在确认之前，CLI 会向您显示差异或确切的 
 命令。
- **沙盒：**您可以在安全的容器化环境中运行工具执行
环境以将更改与主机系统隔离。有关更多详细信息，请参阅 
 [ 沙盒 ](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>) 指南。
- **受信任的文件夹：**您可以配置哪些目录允许模型
使用系统工具。有关更多详细信息，请参阅
 [受信任文件夹](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/trusted-folders.md>) 指南。

在允许工具执行之前仔细查看确认提示。

### 如何使用手动触发工具

您可以在提示中使用特殊语法直接触发关键工具：

- **[文件访问](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md#read_many_files>) (`@`):** 使用`@`
符号后跟文件或目录路径，以将其内容包含在 
 提示符中。这将触发 `read_many_files` 工具。
- **[Shell 命令](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/shell.md>) (`!`):** 使用 `!` 符号，后跟
直接执行系统命令。这将触发 `run_shell_command`
 工具。

## 如何管理工具

使用内置命令，您可以检查可用工具并配置它们
的行为方式。

### 工具发现

使用 `/tools` 命令查看当前会话中哪些工具处于活动状态。

- **`/tools`**：列出所有已注册的工具及其显示名称。
- **`/tools desc`**：列出所有工具及其完整说明。

这对于验证 
[MCP 服务器 ](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>) 或自定义工具是否正确加载特别有用。

### 工具配置

您可以在设置中启用、禁用或配置特定工具。对于
例如，您可以为 shell 命令设置特定的寻呼机或配置
用于网络搜索的浏览器。请参阅 [设置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/settings.md>) 指南
详细信息。

## 可用工具

以下部分列出了所有可用工具，按其主要 
 功能进行分类。有关详细参数信息，请参阅
每个工具的链接文档。

### 执行

|工具|种类|描述|
| :--------------------------------------- | :-------- | :----------------------------------------------------------------------------------------------------------------------- |
| [`run_shell_command`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/shell.md>) | `Execute` |执行任意 shell 命令。支持交互式会话和后台进程。需要手动确认。|

### 文件系统

|工具|种类|描述|
| :------------------------------------------- | :------- | :---------------------------------------------------------------------------------------------------- |
| [`glob`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)            | `Search` |查找工作区中与特定全局模式匹配的文件。|
| [`grep_search`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)     | `Search` |在文件内容中搜索正则表达式模式。旧别名：`search_file_content`。|
| [`list_directory`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)  | `Read`   |列出指定路径中的文件和子目录的名称。|
| [`read_file`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)       | `Read`   |读取特定文件的内容。支持文本、图像、音频和PDF。|
| [`read_many_files`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>) | `Read`   |读取并连接多个文件的内容。通常由提示中的 `@` 符号触发。|
| [`replace`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)         | `Edit`   |在文件中执行精确的文本替换。需要手动确认。|
| [`write_file`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)      | `Edit`   |使用新内容创建或覆盖文件。需要手动确认。|

### 互动

|工具|种类|描述|
| :--------------------------------- | :------------ | :------------------------------------------------------------------------------------- |
| [`ask_user`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/ask-user.md>) | `Communicate` |通过交互式对话框请求澄清或缺少信息。|
| [`write_todos`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/todos.md>) | `Other`       |维护子任务的内部列表。该模型使用它来跟踪自己的进度。|

### 任务跟踪器（实验性）

<!-- prettier-ignore -->
> [！NOTE]
> 这是一项目前正在积极开发的实验性功能。通过`experimental.taskTracker`启用。

|工具|种类|描述|
| :---------------------------------------------- | :------ | :-------------------------------------------------------------------------- |
| [`tracker_create_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)    | `Other` |在实验跟踪器中创建一个新任务。|
| [`tracker_update_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)    | `Other` |更新现有任务的状态、描述或依赖项。|
| [`tracker_get_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)       | `Other` |检索特定任务的完整详细信息。|
| [`tracker_list_tasks`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)     | `Other` |列出跟踪器中的任务，可以选择按状态、类型或父级进行筛选。|
| [`tracker_add_dependency`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>) | `Other` |添加两个任务之间的依赖关系，确保拓扑执行。|
| [`tracker_visualize`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)      | `Other` |渲染当前任务图的 ASCII 树可视化。|

### MCP

|工具|种类|描述|
| :------------------------------------------------ | :------- | :--------------------------------------------------------------------- |
| [`list_mcp_resources`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-resources.md>) | `Search` |列出连接的 MCP 服务器公开的所有可用资源。|
| [`read_mcp_resource`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-resources.md>)  | `Read`   |读取特定 Model Context Protocol (MCP) 资源的内容。|

### 内存

|工具|种类|描述|
| :----------------------------------------------- | :------ | :----------------------------------------------------------------------------------- |
| [`activate_skill`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/activate-skill.md>)   | `Other` |从 `.gemini/skills` 目录加载专门的程序专业知识。|
| [`get_internal_docs`](<Gemini CLI 内部文档查询工具.md>) | `Think` |访问 Gemini CLI 自己的文档以获得有关其功能的准确答案。|

### 规划

|工具|种类|描述|
| :---------------------------------------- | :----- | :--------------------------------------------------------------------------------------- |
| [`enter_plan_mode`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/planning.md>) | `Plan` |将 CLI 切换到安全、只读的“计划模式”以研究复杂的变化。|
| [`exit_plan_mode`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/planning.md>)  | `Plan` |最终确定计划，提交审查，并请求批准以开始实施。|

### 系统

|工具|种类|描述|
| :-------------- | :------ | :----------------------------------------------------------------------------------------------------------------- |
| `complete_task` | `Other` |完成子代理的任务并将结果返回给父代理 agent。用户无法使用此工具。|

### 任务追踪

|工具|种类|描述|
| :----------------------- | :------ | :-------------------------------------------------------------------------- |
| `tracker_add_dependency` | `Think` |在跟踪器中的两个现有任务之间添加依赖关系。|
| `tracker_create_task`    | `Think` |在内部跟踪器中创建一个新任务来监控进度。|
| `tracker_get_task`       | `Think` |检索特定跟踪任务的详细信息和当前状态。|
| `tracker_list_tasks`     | `Think` |列出当前正在跟踪的所有任务。|
| `tracker_update_task`    | `Think` |更新现有任务的状态或详细信息。|
| `tracker_visualize`      | `Think` |生成当前任务依赖图的可视化表示。|
| `update_topic`           | `Think` |更新当前主题和状态以使用户了解进度。|

### 网络

|工具|种类|描述|
| :-------------------------------------------- | :------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`google_web_search`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/web-search.md>) | `Search` |执行 Google 搜索以查找最新信息。|
| [`web_fetch`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/web-fetch.md>)          | `Fetch`  |检索并处理来自特定 URL 的内容。出站请求根据私有和保留的 IP 范围进行验证。在计划模式下，此工具需要明确的用户确认。|

### 工具参数键

写作时 [`argsPattern`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md#arguments-pattern>) 的规则
[政策引擎](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>)，你需要知道 JSON 参数键
每个工具。下表列出了出现在 JSON
每个工具参数的表示。

|工具|JSON 参数键|
| :----------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `run_shell_command`      | `command`, `description`, `dir_path`, `is_background`                                                                                                                                                |
| `glob`                   | `pattern`, `dir_path`, `case_sensitive`, `respect_git_ignore`, `respect_gemini_ignore`                                                                                                               |
| `grep_search`            | `pattern`, `dir_path`, `include_pattern`, `exclude_pattern`, `names_only`, `case_sensitive`, `fixed_strings`, `context`, `after`, `before`, `no_ignore`, `max_matches_per_file`, `total_max_matches` |
| `list_directory`         | `dir_path`, `ignore`, `file_filtering_options`                                                                                                                                                       |
| `read_file`              | `file_path`, `start_line`, `end_line`                                                                                                                                                                |
| `read_many_files`        | `include`, `exclude`, `recursive`, `useDefaultExcludes`                                                                                                                                              |
| `write_file`             | `file_path`, `content`                                                                                                                                                                               |
| `replace`                | `file_path`, `old_string`, `new_string`, `instruction`, `allow_multiple`                                                                                                                             |
| `ask_user`               |`questions`（`question`、`header`、`type`、`options`的数组）|
| `write_todos`            |`todos`（`description`、`status`的数组）|
| `activate_skill`         | `name`                                                                                                                                                                                               |
| `get_internal_docs`      | `path`                                                                                                                                                                                               |
| `enter_plan_mode`        | `reason`                                                                                                                                                                                             |
| `exit_plan_mode`         | `plan_path`                                                                                                                                                                                          |
| `tracker_create_task`    | `title`, `description`, `type`                                                                                                                                                                       |
| `tracker_update_task`    | `id`, `title`, `description`, `status`, `dependencies`                                                                                                                                               |
| `tracker_get_task`       | `id`                                                                                                                                                                                                 |
| `tracker_list_tasks`     | `status`, `type`, `parentId`                                                                                                                                                                         |
| `tracker_add_dependency` | `taskId`, `dependencyId`                                                                                                                                                                             |
| `tracker_visualize`      |_(无)_|
| `update_topic`           | `title`, `summary`, `strategic_intent`                                                                                                                                                               |
| `google_web_search`      | `query`                                                                                                                                                                                              |
| `web_fetch`              | `prompt`                                                                                                                                                                                             |

例如，要编写阻止任何 `write_file` 呼叫定位 
a `.env` 文件的策略规则，您需要与 `file_path` 键进行匹配：

```toml
[[rule]]
toolName = "write_file"
argsPattern = '"file_path":".*\.env"'
decision = "deny"
priority = 100
denyMessage = "Writing to .env files is not allowed."
```

有关完整的参数描述和类型，请参阅链接的各个工具页面
在 [上表](<#可用工具>).

## 在引擎盖下

对于开发人员来说，工具系统被设计为可扩展且健壮。 
`ToolRegistry` 类管理所有可用的工具。

您可以通过在设置中配置 
`tools.discoveryCommand` 或连接到 MCP 服务器来使用自定义工具扩展 Gemini CLI。

<!-- prettier-ignore -->
> [！NOTE]
> 要深入了解内部工具 API 以及如何在代码库中实现您的 
>  自己的工具，请参阅 
> GitHub 中的 `packages/core/src/tools/` 目录。

## 后续步骤

- 了解如何[设置MCP服务器](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>)。
- 探索 [Agent Skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/skills.md>) 以获得专业知识。
- 斜杠命令请参见[命令参考](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tools reference

Gemini CLI uses tools to interact with your local environment, access
information, and perform actions on your behalf. These tools extend the model's
capabilities beyond text generation, letting it read files, execute commands,
and search the web.

## How to use Gemini CLI's tools

Tools are generally invoked automatically by Gemini CLI when it needs to perform
an action. However, you can also trigger specific tools manually using shorthand
syntax.

### Automatic execution and security

When the model wants to use a tool, Gemini CLI evaluates the request against its
security policies.

- **User confirmation:** You must manually approve tools that modify files or
  execute shell commands (mutators). The CLI shows you a diff or the exact
  command before you confirm.
- **Sandboxing:** You can run tool executions in secure, containerized
  environments to isolate changes from your host system. For more details, see
  the [Sandboxing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>) guide.
- **Trusted folders:** You can configure which directories allow the model to
  use system tools. For more details, see the
  [Trusted folders](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/trusted-folders.md>) guide.

Review confirmation prompts carefully before allowing a tool to execute.

### How to use manually-triggered tools

You can directly trigger key tools using special syntax in your prompt:

- **[File access](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md#read_many_files>) (`@`):** Use the `@`
  symbol followed by a file or directory path to include its content in your
  prompt. This triggers the `read_many_files` tool.
- **[Shell commands](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/shell.md>) (`!`):** Use the `!` symbol followed by
  a system command to execute it directly. This triggers the `run_shell_command`
  tool.

## How to manage tools

Using built-in commands, you can inspect available tools and configure how they
behave.

### Tool discovery

Use the `/tools` command to see what tools are currently active in your session.

- **`/tools`**: Lists all registered tools with their display names.
- **`/tools desc`**: Lists all tools with their full descriptions.

This is especially useful for verifying that
[MCP servers](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>) or custom tools are loaded correctly.

### Tool configuration

You can enable, disable, or configure specific tools in your settings. For
example, you can set a specific pager for shell commands or configure the
browser used for web searches. See the [Settings](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/settings.md>) guide for
details.

## Available tools

The following sections list all available tools, categorized by their primary
function. For detailed parameter information, see the linked documentation for
each tool.

### Execution

| Tool                                     | Kind      | Description                                                                                                              |
| :--------------------------------------- | :-------- | :----------------------------------------------------------------------------------------------------------------------- |
| [`run_shell_command`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/shell.md>) | `Execute` | Executes arbitrary shell commands. Supports interactive sessions and background processes. Requires manual confirmation. |

### File System

| Tool                                         | Kind     | Description                                                                                           |
| :------------------------------------------- | :------- | :---------------------------------------------------------------------------------------------------- |
| [`glob`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)            | `Search` | Finds files matching specific glob patterns across the workspace.                                     |
| [`grep_search`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)     | `Search` | Searches for a regular expression pattern within file contents. Legacy alias: `search_file_content`.  |
| [`list_directory`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)  | `Read`   | Lists the names of files and subdirectories within a specified path.                                  |
| [`read_file`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)       | `Read`   | Reads the content of a specific file. Supports text, images, audio, and PDF.                          |
| [`read_many_files`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>) | `Read`   | Reads and concatenates content from multiple files. Often triggered by the `@` symbol in your prompt. |
| [`replace`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)         | `Edit`   | Performs precise text replacement within a file. Requires manual confirmation.                        |
| [`write_file`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/file-system.md>)      | `Edit`   | Creates or overwrites a file with new content. Requires manual confirmation.                          |

### Interaction

| Tool                               | Kind          | Description                                                                            |
| :--------------------------------- | :------------ | :------------------------------------------------------------------------------------- |
| [`ask_user`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/ask-user.md>) | `Communicate` | Requests clarification or missing information via an interactive dialog.               |
| [`write_todos`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/todos.md>) | `Other`       | Maintains an internal list of subtasks. The model uses this to track its own progress. |

### Task Tracker (Experimental)

<!-- prettier-ignore -->
> [!NOTE]
> This is an experimental feature currently under active development. Enable via `experimental.taskTracker`.

| Tool                                            | Kind    | Description                                                                 |
| :---------------------------------------------- | :------ | :-------------------------------------------------------------------------- |
| [`tracker_create_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)    | `Other` | Creates a new task in the experimental tracker.                             |
| [`tracker_update_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)    | `Other` | Updates an existing task's status, description, or dependencies.            |
| [`tracker_get_task`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)       | `Other` | Retrieves the full details of a specific task.                              |
| [`tracker_list_tasks`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)     | `Other` | Lists tasks in the tracker, optionally filtered by status, type, or parent. |
| [`tracker_add_dependency`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>) | `Other` | Adds a dependency between two tasks, ensuring topological execution.        |
| [`tracker_visualize`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/tracker.md>)      | `Other` | Renders an ASCII tree visualization of the current task graph.              |

### MCP

| Tool                                              | Kind     | Description                                                            |
| :------------------------------------------------ | :------- | :--------------------------------------------------------------------- |
| [`list_mcp_resources`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-resources.md>) | `Search` | Lists all available resources exposed by connected MCP servers.        |
| [`read_mcp_resource`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-resources.md>)  | `Read`   | Reads the content of a specific Model Context Protocol (MCP) resource. |

### Memory

| Tool                                             | Kind    | Description                                                                          |
| :----------------------------------------------- | :------ | :----------------------------------------------------------------------------------- |
| [`activate_skill`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/activate-skill.md>)   | `Other` | Loads specialized procedural expertise from the `.gemini/skills` directory.          |
| [`get_internal_docs`](<Gemini CLI 内部文档查询工具.md>) | `Think` | Accesses Gemini CLI's own documentation for accurate answers about its capabilities. |

### Planning

| Tool                                      | Kind   | Description                                                                              |
| :---------------------------------------- | :----- | :--------------------------------------------------------------------------------------- |
| [`enter_plan_mode`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/planning.md>) | `Plan` | Switches the CLI to a safe, read-only "Plan Mode" for researching complex changes.       |
| [`exit_plan_mode`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/planning.md>)  | `Plan` | Finalizes a plan, presents it for review, and requests approval to start implementation. |

### System

| Tool            | Kind    | Description                                                                                                        |
| :-------------- | :------ | :----------------------------------------------------------------------------------------------------------------- |
| `complete_task` | `Other` | Finalizes a subagent's mission and returns the result to the parent agent. This tool is not available to the user. |

### Task Tracking

| Tool                     | Kind    | Description                                                                 |
| :----------------------- | :------ | :-------------------------------------------------------------------------- |
| `tracker_add_dependency` | `Think` | Adds a dependency between two existing tasks in the tracker.                |
| `tracker_create_task`    | `Think` | Creates a new task in the internal tracker to monitor progress.             |
| `tracker_get_task`       | `Think` | Retrieves the details and current status of a specific tracked task.        |
| `tracker_list_tasks`     | `Think` | Lists all tasks currently being tracked.                                    |
| `tracker_update_task`    | `Think` | Updates the status or details of an existing task.                          |
| `tracker_visualize`      | `Think` | Generates a visual representation of the current task dependency graph.     |
| `update_topic`           | `Think` | Updates the current topic and status to keep the user informed of progress. |

### Web

| Tool                                          | Kind     | Description                                                                                                                                                                              |
| :-------------------------------------------- | :------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`google_web_search`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/web-search.md>) | `Search` | Performs a Google Search to find up-to-date information.                                                                                                                                 |
| [`web_fetch`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/web-fetch.md>)          | `Fetch`  | Retrieves and processes content from specific URLs. Outbound requests are validated against private and reserved IP ranges. In Plan Mode, this tool requires explicit user confirmation. |

### Tool argument keys

When writing [`argsPattern`](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md#arguments-pattern>) rules for the
[policy engine](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>), you need to know the JSON argument keys for
each tool. The following table lists the keys that appear in the JSON
representation of each tool's arguments.

| Tool                     | JSON argument keys                                                                                                                                                                                   |
| :----------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `run_shell_command`      | `command`, `description`, `dir_path`, `is_background`                                                                                                                                                |
| `glob`                   | `pattern`, `dir_path`, `case_sensitive`, `respect_git_ignore`, `respect_gemini_ignore`                                                                                                               |
| `grep_search`            | `pattern`, `dir_path`, `include_pattern`, `exclude_pattern`, `names_only`, `case_sensitive`, `fixed_strings`, `context`, `after`, `before`, `no_ignore`, `max_matches_per_file`, `total_max_matches` |
| `list_directory`         | `dir_path`, `ignore`, `file_filtering_options`                                                                                                                                                       |
| `read_file`              | `file_path`, `start_line`, `end_line`                                                                                                                                                                |
| `read_many_files`        | `include`, `exclude`, `recursive`, `useDefaultExcludes`                                                                                                                                              |
| `write_file`             | `file_path`, `content`                                                                                                                                                                               |
| `replace`                | `file_path`, `old_string`, `new_string`, `instruction`, `allow_multiple`                                                                                                                             |
| `ask_user`               | `questions` (array of `question`, `header`, `type`, `options`)                                                                                                                                       |
| `write_todos`            | `todos` (array of `description`, `status`)                                                                                                                                                           |
| `activate_skill`         | `name`                                                                                                                                                                                               |
| `get_internal_docs`      | `path`                                                                                                                                                                                               |
| `enter_plan_mode`        | `reason`                                                                                                                                                                                             |
| `exit_plan_mode`         | `plan_path`                                                                                                                                                                                          |
| `tracker_create_task`    | `title`, `description`, `type`                                                                                                                                                                       |
| `tracker_update_task`    | `id`, `title`, `description`, `status`, `dependencies`                                                                                                                                               |
| `tracker_get_task`       | `id`                                                                                                                                                                                                 |
| `tracker_list_tasks`     | `status`, `type`, `parentId`                                                                                                                                                                         |
| `tracker_add_dependency` | `taskId`, `dependencyId`                                                                                                                                                                             |
| `tracker_visualize`      | _(none)_                                                                                                                                                                                             |
| `update_topic`           | `title`, `summary`, `strategic_intent`                                                                                                                                                               |
| `google_web_search`      | `query`                                                                                                                                                                                              |
| `web_fetch`              | `prompt`                                                                                                                                                                                             |

For example, to write a policy rule that blocks any `write_file` call targeting
a `.env` file, you would match against the `file_path` key:

```toml
[[rule]]
toolName = "write_file"
argsPattern = '"file_path":".*\.env"'
decision = "deny"
priority = 100
denyMessage = "Writing to .env files is not allowed."
```

For full argument descriptions and types, see the individual tool pages linked
in the [tables above](<#available-tools>).

## Under the hood

For developers, the tool system is designed to be extensible and robust. The
`ToolRegistry` class manages all available tools.

You can extend Gemini CLI with custom tools by configuring
`tools.discoveryCommand` in your settings or by connecting to MCP servers.

<!-- prettier-ignore -->
> [!NOTE]
> For a deep dive into the internal Tool API and how to implement your
> own tools in the codebase, see the `packages/core/src/tools/` directory in
> GitHub.

## Next steps

- Learn how to [Set up an MCP server](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/tools/mcp-server.md>).
- Explore [Agent Skills](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/skills.md>) for specialized expertise.
- See the [Command reference](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>) for slash commands.


</details>
