# OpenCode 内置、自定义与 MCP 工具

工具参考。介绍终端、编辑、检索、LSP、技能、问题等内置工具，以及权限、自定义工具、MCP 和忽略规则，对应 Agent 的实际代码操作。

- 来源：OpenCode
- 资料类型：工具参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-08-26](<https://github.com/anomalyco/opencode/commit/fd9bd448a2e68990e7aed3495e5590cecb934bfb>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：工具可用不等于操作已验证或权限适合自己的项目。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/tools.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 内置、自定义与 MCP 工具.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Tools
description: Manage the tools an LLM can use.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

工具允许 LLM 在您的代码库中执行操作。 OpenCode 附带一组内置工具，但您可以使用 [ 自定义工具 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_为 OpenCode 编写自定义工具.md>) 或 [MCP 服务器 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>) 对其进行扩展。

默认情况下，所有工具都是**启用**并且不需要运行权限。您可以通过[权限](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>)控制工具行为。

---

## 配置

使用 `permission` 字段控制工具行为。您可以允许、拒绝或要求批准每个工具。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny",
    "bash": "ask",
    "webfetch": "allow"
  }
}
```

您还可以使用通配符同时控制多个工具。例如，要求 MCP 服务器批准所有工具：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "mymcp_*": "ask"
  }
}
```

[了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>)有关配置权限的信息。

---

## 内置

以下是OpenCode中可用的所有内置工具。

---

### 巴什

在项目环境中执行 shell 命令。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": "allow"
  }
}
```

该工具允许 LLM 运行终端命令，例如 `npm install`、`git status` 或任何其他 shell 命令。

---

### 编辑

使用精确的字符串替换修改现有文件。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

该工具通过替换精确的文本匹配来对文件执行精确编辑。这是 LLM 修改代码的主要方式。

---

### 写

创建新文件或覆盖现有文件。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

使用它允许LLM创建新文件。如果现有文件已经存在，它将覆盖它们。

**注释：**

`write`工具由`edit`权限控制，该权限涵盖所有文件修改（`edit`、`write`、`apply_patch`）。

---

### 读

从代码库中读取文件内容。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "read": "allow"
  }
}
```

该工具读取文件并返回其内容。它支持读取大文件的特定行范围。

---

### grep

使用正则表达式搜索文件内容。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "grep": "allow"
  }
}
```

在您的代码库中快速进行内容搜索。支持完整的正则表达式语法和文件模式过滤。

---

### 全局

通过模式匹配查找文件。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "glob": "allow"
  }
}
```

使用 `**/*.js` 或 `src/**/*.ts` 等全局模式搜索文件。返回按修改时间排序的匹配文件路径。

---

### lsp（实验性）

与您配置的 LSP 服务器交互，以获得代码智能功能，例如定义、引用、悬停信息和调用层次结构。

**注释：**

该工具仅在`OPENCODE_EXPERIMENTAL_LSP_TOOL=true`（或`OPENCODE_EXPERIMENTAL=true`）时可用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "lsp": "allow"
  }
}
```

支持的操作包括 `goToDefinition`、`findReferences`、`hover`、`documentSymbol`、`workspaceSymbol`、`goToImplementation`、`prepareCallHierarchy`、`incomingCalls` 和`outgoingCalls`。

要配置哪些 LSP 服务器可用于您的项目，请参阅 [LSP 服务器 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode LSP 诊断反馈与服务器配置.md>)。

---

### apply_patch

对文件应用补丁。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

该工具将补丁文件应用到您的代码库。对于应用来自各种来源的差异和补丁很有用。

处理 `tool.execute.before` 或 `tool.execute.after` 挂钩时，请检查 `input.tool === "apply_patch"`（而不是 `"patch"`）。

`apply_patch` 使用 `output.args.patchText` 而不是 `output.args.filePath`。路径嵌入在 `patchText` 内的标记行中，并且相对于项目根（例如：`*** Add File: src/new-file.ts`、`*** Update File: src/existing.ts`、`*** Move to: src/renamed.ts`、`*** Delete File: src/obsolete.ts`）。

**注释：**

`apply_patch`工具由`edit`权限控制，该权限涵盖所有文件修改（`edit`、`write`、`apply_patch`）。

---

### 技能

加载 [ 技能 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Skills 发现、格式与权限.md>)（`SKILL.md` 文件）并在对话中返回其内容。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "skill": "allow"
  }
}
```

---

### 待办写入

在编码会话期间管理待办事项列表。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "todowrite": "allow"
  }
}
```

创建和更新任务列表以跟踪复杂操作期间的进度。 LLM 使用它来组织多步骤任务。

**注释：**

默认情况下，子代理禁用此工具，但您可以手动启用它。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#permissions>)

---

### 网页抓取

获取网页内容。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "webfetch": "allow"
  }
}
```

允许LLM获取和读取网页。对于查找文档或研究在线资源很有用。

---

### 网络搜索

在网络上搜索信息。

**注释：**

仅当使用 OpenCode 或 OpenCode Go 提供程序时，或者当 `OPENCODE_ENABLE_EXA` 或 `OPENCODE_ENABLE_PARALLEL` 环境变量设置为任何真值（例如，`true` 或`1`）。

启动OpenCode时启用：

```bash
OPENCODE_ENABLE_EXA=1 opencode
# or
OPENCODE_ENABLE_PARALLEL=1 opencode
```

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "websearch": "allow"
  }
}
```

使用 Exa 或 Parallel 执行网络搜索以在线查找相关信息。对于研究主题、查找时事或收集超出训练数据截止范围的信息很有用。

不需要 API 密钥 - 该工具无需身份验证即可直接连接到后端托管的 MCP 服务。

**提示：**

当您需要查找信息（发现）时使用 `websearch`，当您需要从特定 URL 检索内容（检索）时使用 `webfetch`。

---

### 问题

在执行过程中询问用户问题。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "question": "allow"
  }
}
```

该工具允许 LLM 在任务期间询问用户问题。它适用于：

- 收集用户偏好或要求
- 澄清不明确的指令
- 就实施选择做出决策
- 提供选择方向

每个问题都包含标题、问题文本和选项列表。用户可以从提供的选项中进行选择或输入自定义答案。当存在多个问题时，用户可以在提交所有答案之前在它们之间导航。

---

## 定制工具

自定义工具可让您定义 LLM 可以调用的自己的函数。这些是在您的配置文件中定义的并且可以执行任意代码。

[了解有关创建自定义工具的更多信息](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_为 OpenCode 编写自定义工具.md>)。

---

## MCP 服务器

MCP (Model Context Protocol) 服务器允许您集成外部工具和服务。这包括数据库访问、API 集成和第三方服务。

[详细了解](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>)有关配置MCP服务器的信息。

---

## 内部结构

在内部，像 `grep` 和 `glob` 这样的工具在底层使用 [ripgrep](<https://github.com/BurntSushi/ripgrep>)。默认情况下，ripgrep 遵循 `.gitignore` 模式，这意味着 `.gitignore` 中列出的文件和目录将从搜索和列表中排除。

---

### 忽略模式

要包含通常会被忽略的文件，请在项目根目录中创建一个 `.ignore` 文件。该文件可以明确允许某些路径。

**代码示例：** `.ignore`

```text
!node_modules/
!dist/
!build/
```

例如，此 `.ignore` 文件允许 ripgrep 在 `node_modules/`、`dist/` 和 `build/` 目录中搜索，即使它们列在 `.gitignore` 中。


## 原文对照

<details>
<summary>展开完整原文</summary>

Tools allow the LLM to perform actions in your codebase. OpenCode comes with a set of built-in tools, but you can extend it with [custom tools](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_为 OpenCode 编写自定义工具.md>) or [MCP servers](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>).

By default, all tools are **enabled** and don't need permission to run. You can control tool behavior through [permissions](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>).

---

## Configure

Use the `permission` field to control tool behavior. You can allow, deny, or require approval for each tool.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "deny",
    "bash": "ask",
    "webfetch": "allow"
  }
}
```

You can also use wildcards to control multiple tools at once. For example, to require approval for all tools from an MCP server:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "mymcp_*": "ask"
  }
}
```

[Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 工具权限与细粒度匹配.md>) about configuring permissions.

---

## Built-in

Here are all the built-in tools available in OpenCode.

---

### bash

Execute shell commands in your project environment.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": "allow"
  }
}
```

This tool allows the LLM to run terminal commands like `npm install`, `git status`, or any other shell command.

---

### edit

Modify existing files using exact string replacements.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

This tool performs precise edits to files by replacing exact text matches. It's the primary way the LLM modifies code.

---

### write

Create new files or overwrite existing ones.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

Use this to allow the LLM to create new files. It will overwrite existing files if they already exist.

**注释：**

The `write` tool is controlled by the `edit` permission, which covers all file modifications (`edit`, `write`, `apply_patch`).

---

### read

Read file contents from your codebase.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "read": "allow"
  }
}
```

This tool reads files and returns their contents. It supports reading specific line ranges for large files.

---

### grep

Search file contents using regular expressions.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "grep": "allow"
  }
}
```

Fast content search across your codebase. Supports full regex syntax and file pattern filtering.

---

### glob

Find files by pattern matching.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "glob": "allow"
  }
}
```

Search for files using glob patterns like `**/*.js` or `src/**/*.ts`. Returns matching file paths sorted by modification time.

---

### lsp (experimental)

Interact with your configured LSP servers to get code intelligence features like definitions, references, hover info, and call hierarchy.

**注释：**

This tool is only available when `OPENCODE_EXPERIMENTAL_LSP_TOOL=true` (or `OPENCODE_EXPERIMENTAL=true`).

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "lsp": "allow"
  }
}
```

Supported operations include `goToDefinition`, `findReferences`, `hover`, `documentSymbol`, `workspaceSymbol`, `goToImplementation`, `prepareCallHierarchy`, `incomingCalls`, and `outgoingCalls`.

To configure which LSP servers are available for your project, see [LSP Servers](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode LSP 诊断反馈与服务器配置.md>).

---

### apply_patch

Apply patches to files.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "allow"
  }
}
```

This tool applies patch files to your codebase. Useful for applying diffs and patches from various sources.

When handling `tool.execute.before` or `tool.execute.after` hooks, check `input.tool === "apply_patch"` (not `"patch"`).

`apply_patch` uses `output.args.patchText` instead of `output.args.filePath`. Paths are embedded in marker lines within `patchText` and are relative to the project root (for example: `*** Add File: src/new-file.ts`, `*** Update File: src/existing.ts`, `*** Move to: src/renamed.ts`, `*** Delete File: src/obsolete.ts`).

**注释：**

The `apply_patch` tool is controlled by the `edit` permission, which covers all file modifications (`edit`, `write`, `apply_patch`).

---

### skill

Load a [skill](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Skills 发现、格式与权限.md>) (a `SKILL.md` file) and return its content in the conversation.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "skill": "allow"
  }
}
```

---

### todowrite

Manage todo lists during coding sessions.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "todowrite": "allow"
  }
}
```

Creates and updates task lists to track progress during complex operations. The LLM uses this to organize multi-step tasks.

**注释：**

This tool is disabled for subagents by default, but you can enable it manually. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#permissions>)

---

### webfetch

Fetch web content.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "webfetch": "allow"
  }
}
```

Allows the LLM to fetch and read web pages. Useful for looking up documentation or researching online resources.

---

### websearch

Search the web for information.

**注释：**

This tool is only available when using the OpenCode or OpenCode Go provider, or when either the `OPENCODE_ENABLE_EXA` or `OPENCODE_ENABLE_PARALLEL` environment variable is set to any truthy value (e.g., `true` or `1`).

To enable when launching OpenCode:

```bash
OPENCODE_ENABLE_EXA=1 opencode
# or
OPENCODE_ENABLE_PARALLEL=1 opencode
```

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "websearch": "allow"
  }
}
```

Performs web searches using Exa or Parallel to find relevant information online. Useful for researching topics, finding current events, or gathering information beyond the training data cutoff.

No API key is required — the tool connects directly to the backend's hosted MCP service without authentication.

**提示：**

Use `websearch` when you need to find information (discovery), and `webfetch` when you need to retrieve content from a specific URL (retrieval).

---

### question

Ask the user questions during execution.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "question": "allow"
  }
}
```

This tool allows the LLM to ask the user questions during a task. It's useful for:

- Gathering user preferences or requirements
- Clarifying ambiguous instructions
- Getting decisions on implementation choices
- Offering choices about what direction to take

Each question includes a header, the question text, and a list of options. Users can select from the provided options or type a custom answer. When there are multiple questions, users can navigate between them before submitting all answers.

---

## Custom tools

Custom tools let you define your own functions that the LLM can call. These are defined in your config file and can execute arbitrary code.

[Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_为 OpenCode 编写自定义工具.md>) about creating custom tools.

---

## MCP servers

MCP (Model Context Protocol) servers allow you to integrate external tools and services. This includes database access, API integrations, and third-party services.

[Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode MCP 连接、OAuth 与工具范围.md>) about configuring MCP servers.

---

## Internals

Internally, tools like `grep` and `glob` use [ripgrep](<https://github.com/BurntSushi/ripgrep>) under the hood. By default, ripgrep respects `.gitignore` patterns, which means files and directories listed in your `.gitignore` will be excluded from searches and listings.

---

### Ignore patterns

To include files that would normally be ignored, create a `.ignore` file in your project root. This file can explicitly allow certain paths.

**代码示例：** `.ignore`

```text
!node_modules/
!dist/
!build/
```

For example, this `.ignore` file allows ripgrep to search within `node_modules/`, `dist/`, and `build/` directories even if they're listed in `.gitignore`.


</details>
