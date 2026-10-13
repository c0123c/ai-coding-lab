# Qwen Code IDE 伴随插件接口规范

定义发现连接、上下文通知、差异打开接受拒绝及生命周期，用于为其他编辑器实现伴随插件。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2025-12-19](<https://github.com/QwenLM/qwen-code/commit/52cd1da4a73a74bc0f97130a8da1640e47a5610c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/ide-integration/ide-companion-spec.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code IDE 伴随插件接口规范.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Qwen Code 配套插件：接口规范

> 最后更新：九月 15、2025

本文档定义了构建配套插件以启用 Qwen Code 的 IDE 模式的合约。对于 VS Code，这些功能（原生差异、上下文感知）由官方扩展 ([marketplace](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>)) 提供。此规范适用于希望为其他编辑器（如 JetBrains IDE、Sublime Text 等）带来类似功能的贡献者。

## 一、通讯接口

Qwen Code 和 IDE 插件通过本地通信通道进行通信。

### 1。传输层：MCP 之上 HTTP

插件 **MUST** 运行本地 HTTP 服务器，该服务器实现 **Model Context Protocol (MCP)**。

- **协议：**服务器必须是有效的MCP服务器。我们建议您使用现有的 MCP SDK 作为您选择的语言（如果有）。
- **端点：** 服务器应为所有 MCP 通信公开单个端点（例如，`/mcp`）。
- **端口：** 服务器**MUST**侦听动态分配的端口（即侦听端口`0`）。

### 2。发现机制：锁文件

要连接 Qwen Code，它需要发现您的服务器正在使用哪个端口。插件 **MUST** 通过创建“锁定文件”并设置端口环境变量来实现此目的。

- **CLI如何查找文件：**CLI从`QWEN_CODE_IDE_SERVER_PORT`读取端口，然后读取`~/.qwen/ide/<PORT>.lock`。 （旧扩展存在遗留后备；请参阅下面的注释。）
- **文件位置：** 该文件必须在特定目录中创建：`~/.qwen/ide/`。如果该目录不存在，您的插件必须创建该目录。
- **文件命名约定：** 文件名至关重要，**MUST** 遵循以下模式：
  `<PORT>.lock`
  - `<PORT>`：您的MCP服务器正在侦听的端口。
- **文件内容和工作空间验证：** 文件 **MUST** 包含具有以下结构的 JSON 对象：

  ```json
  {
    "port": 12345,
    "workspacePath": "/path/to/project1:/path/to/project2",
    "authToken": "a-very-secret-token",
    "ppid": 1234,
    "ideName": "VS Code"
  }
  ```
  - `port`（数字，必填）：MCP服务器的端口。
  - `workspacePath`（字符串，必需）：所有打开的工作区根路径的列表，由 OS- 特定路径分隔符分隔（对于 Linux/macOS 为 `:`，对于 Windows 为 `;`）。 CLI 使用此路径来确保它在 IDE 中打开的同一项目文件夹中运行。如果CLI的当前工作目录不是`workspacePath`的子目录，则连接将被拒绝。您的插件 **MUST** 提供了打开工作空间根目录的正确绝对路径。
  - `authToken`（字符串，必需）：用于保护连接的秘密令牌。 CLI 会在所有请求的 `Authorization: Bearer <token>` 标头中包含此令牌。
  - `ppid`（数字，必需）：IDE进程的父进程ID。
  - `ideName`（字符串，必需）：IDE 的用户友好名称（例如，`VS Code`、`JetBrains IDE`）。

- **身份验证：**为了保护连接，插件**MUST**生成一个唯一的秘密令牌并将其包含在发现文件中。然后，对于对 MCP 服务器的所有请求（例如，`Authorization: Bearer a-very-secret-token`），CLI 会将此令牌包含在 `Authorization` 标头中。您的服务器 **MUST** 在每个请求上验证此令牌并拒绝任何未经授权的请求。
- **环境变量（必需）：**您的插件**MUST**在集成终端中设置`QWEN_CODE_IDE_SERVER_PORT`，以便CLI可以找到正确的`<PORT>.lock`文件。

**旧注释：** 对于早于 v0.5.1 的扩展，Qwen Code 可能会回退到读取名为 `qwen-code-ide-server-<PID>.json` 或 `qwen-code-ide-server-<PORT>.json` 的系统临时目录中的 JSON 文件。新的集成不应依赖这些旧文件。

## II。上下文接口

为了实现上下文感知，插件 **MAY** 为 CLI 提供有关 IDE 中用户活动的实时信息。

### `ide/contextUpdate`通知

每当用户的上下文发生变化时，插件 **MAY** 都会向 CLI 发送 `ide/contextUpdate` [ 通知 ](<https://modelcontextprotocol.io/specification/2025-06-18/basic/index#notifications>)。

- **触发事件：**在以下情况下应发送此通知（建议去抖时间为 50 毫秒）：
  - 文件被打开、关闭或聚焦。
  - 用户的 cursor 位置或文本选择在活动文件中发生变化。
- **Payload (`IdeContext`):** 通知参数 **MUST** 是一个 `IdeContext` 对象：

  ```typescript
  interface IdeContext {
    workspaceState?: {
      openFiles?: File[];
      isTrusted?: boolean;
    };
  }

  interface File {
    // Absolute path to the file
    path: string;
    // Last focused Unix timestamp (for ordering)
    timestamp: number;
    // True if this is the currently focused file
    isActive?: boolean;
    cursor?: {
      // 1-based line number
      line: number;
      // 1-based character number
      character: number;
    };
    // The text currently selected by the user
    selectedText?: string;
  }
  ```

**注意：** `openFiles` 列表应仅包含磁盘上存在的文件。虚拟文件（例如，没有路径的未保存文件、编辑器设置页面）**MUST** 被排除。

### CLI 如何使用此上下文

收到 `IdeContext` 对象后，CLI 会在将信息发送到模型之前执行几个标准化和截断步骤。

- **文件排序：** CLI 使用 `timestamp` 字段来确定最近使用的文件。它根据该值对 `openFiles` 列表进行排序。因此，您的插件 **MUST** 为文件上次聚焦的时间提供了准确的 Unix 时间戳。
- **活动文件：** CLI 仅将最近的文件（排序后）视为“活动”文件。它将忽略所有其他文件上的 `isActive` 标志并清除其 `cursor` 和 `selectedText` 字段。您的插件应专注于设置 `isActive: true` 并仅为当前关注的文件提供 cursor/selection 详细信息。
- **截断：** 为了管理令牌限制，CLI 截断文件列表（至 10 文件）和 `selectedText`（至 16KB）。

虽然 CLI 处理最终截断，但强烈建议您的插件也限制它发送的上下文量。

## III。差异化接口

为了启用交互式代码修改，插件 **MAY** 公开了一个差异接口。这允许 CLI 请求 IDE 打开差异视图，显示对文件的建议更改。然后，用户可以直接在 IDE 中查看、编辑并最终接受或拒绝这些更改。

### `openDiff` 工具

插件**MUST**在其MCP服务器上注册了`openDiff`工具。

- **描述：**此工具指示IDE打开特定文件的可修改差异视图。
- **请求 (`OpenDiffRequest`):** 该工具通过 `tools/call` 请求调用。请求的 `params` **MUST** 中的 `arguments` 字段是一个 `OpenDiffRequest` 对象。

  ```typescript
  interface OpenDiffRequest {
    // The absolute path to the file to be diffed.
    filePath: string;
    // The proposed new content for the file.
    newContent: string;
  }
  ```

- **响应（`CallToolResult`）：**工具**MUST**立即返回`CallToolResult`以确认请求并报告差异视图是否已成功打开。
  - 成功时：如果差异视图已成功打开，则响应 **MUST** 包含空内容（即 `content: []`）。
  - 失败时：如果错误阻止差异视图打开，则响应 **MUST** 具有 `isError: true` 并在描述错误的 `content` 数组中包含 `TextContent` 块。

差异的实际结果（接受或拒绝）通过通知异步传达。

### `closeDiff` 工具

插件**MUST**在其MCP服务器上注册了`closeDiff`工具。

- **描述：**此工具指示IDE关闭特定文件的打开差异视图。
- **请求 (`CloseDiffRequest`):** 该工具通过 `tools/call` 请求调用。请求的 `params` **MUST** 中的 `arguments` 字段是一个 `CloseDiffRequest` 对象。

  ```typescript
  interface CloseDiffRequest {
    // The absolute path to the file whose diff view should be closed.
    filePath: string;
  }
  ```

- **响应（`CallToolResult`）：**工具**MUST**返回`CallToolResult`。
  - 成功时：如果差异视图成功关闭，则响应 **MUST** 在内容数组中包含单个 **TextContent** 块，其中包含关闭前文件的最终内容。
  - 失败时：如果错误阻止 diff 视图关闭，则响应 **MUST** 具有 `isError: true` 并在描述错误的 `content` 数组中包含 `TextContent` 块。

### `ide/diffAccepted`通知

当用户接受差异视图中的更改（例如，通过单击“应用”或“保存”按钮）时，插件 **MUST** 会向 CLI 发送 `ide/diffAccepted` 通知。

- **Payload:**通知参数**MUST**包括文件路径和文件的最终内容。如果用户在差异视图中进行手动编辑，内容可能与原始 `newContent` 有所不同。

  ```typescript
  {
    // The absolute path to the file that was diffed.
    filePath: string;
    // The full content of the file after acceptance.
    content: string;
  }
  ```

### `ide/diffRejected`通知

当用户拒绝更改时（例如，通过关闭差异视图而不接受），插件 **MUST** 会向 CLI 发送 `ide/diffRejected` 通知。

- **有效负载：**通知参数**MUST**包括被拒绝的差异的文件路径。

  ```typescript
  {
    // The absolute path to the file that was diffed.
    filePath: string;
  }
  ```

## IV。生命周期接口

插件**MUST**根据IDE的生命周期正确管理其资源和发现文件。

- **激活时（IDE startup/plugin 已启用）：**
  1.  启动MCP服务器。
  2.  创建发现文件。
- **停用时（IDE shutdown/plugin 已禁用）：**
  1.  停止MCP服务器。
  2.  删除发现文件。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Qwen Code Companion Plugin: Interface Specification

> Last Updated: September 15, 2025

This document defines the contract for building a companion plugin to enable Qwen Code's IDE mode. For VS Code, these features (native diffing, context awareness) are provided by the official extension ([marketplace](<https://marketplace.visualstudio.com/items?itemName=qwenlm.qwen-code-vscode-ide-companion>)). This specification is for contributors who wish to bring similar functionality to other editors like JetBrains IDEs, Sublime Text, etc.

## I. The Communication Interface

Qwen Code and the IDE plugin communicate through a local communication channel.

### 1. Transport Layer: MCP over HTTP

The plugin **MUST** run a local HTTP server that implements the **Model Context Protocol (MCP)**.

- **Protocol:** The server must be a valid MCP server. We recommend using an existing MCP SDK for your language of choice if available.
- **Endpoint:** The server should expose a single endpoint (e.g., `/mcp`) for all MCP communication.
- **Port:** The server **MUST** listen on a dynamically assigned port (i.e., listen on port `0`).

### 2. Discovery Mechanism: The Lock File

For Qwen Code to connect, it needs to discover what port your server is using. The plugin **MUST** facilitate this by creating a "lock file" and setting the port environment variable.

- **How the CLI Finds the File:** The CLI reads the port from `QWEN_CODE_IDE_SERVER_PORT`, then reads `~/.qwen/ide/<PORT>.lock`. (Legacy fallbacks exist for older extensions; see note below.)
- **File Location:** The file must be created in a specific directory: `~/.qwen/ide/`. Your plugin must create this directory if it doesn't exist.
- **File Naming Convention:** The filename is critical and **MUST** follow the pattern:
  `<PORT>.lock`
  - `<PORT>`: The port your MCP server is listening on.
- **File Content & Workspace Validation:** The file **MUST** contain a JSON object with the following structure:

  ```json
  {
    "port": 12345,
    "workspacePath": "/path/to/project1:/path/to/project2",
    "authToken": "a-very-secret-token",
    "ppid": 1234,
    "ideName": "VS Code"
  }
  ```
  - `port` (number, required): The port of the MCP server.
  - `workspacePath` (string, required): A list of all open workspace root paths, delimited by the OS-specific path separator (`:` for Linux/macOS, `;` for Windows). The CLI uses this path to ensure it's running in the same project folder that's open in the IDE. If the CLI's current working directory is not a sub-directory of `workspacePath`, the connection will be rejected. Your plugin **MUST** provide the correct, absolute path(s) to the root of the open workspace(s).
  - `authToken` (string, required): A secret token for securing the connection. The CLI will include this token in an `Authorization: Bearer <token>` header on all requests.
  - `ppid` (number, required): The parent process ID of the IDE process.
  - `ideName` (string, required): A user-friendly name for the IDE (e.g., `VS Code`, `JetBrains IDE`).

- **Authentication:** To secure the connection, the plugin **MUST** generate a unique, secret token and include it in the discovery file. The CLI will then include this token in the `Authorization` header for all requests to the MCP server (e.g., `Authorization: Bearer a-very-secret-token`). Your server **MUST** validate this token on every request and reject any that are unauthorized.
- **Environment Variables (Required):** Your plugin **MUST** set `QWEN_CODE_IDE_SERVER_PORT` in the integrated terminal so the CLI can locate the correct `<PORT>.lock` file.

**Legacy note:** For extensions older than v0.5.1, Qwen Code may fall back to reading JSON files in the system temp directory named `qwen-code-ide-server-<PID>.json` or `qwen-code-ide-server-<PORT>.json`. New integrations should not rely on these legacy files.

## II. The Context Interface

To enable context awareness, the plugin **MAY** provide the CLI with real-time information about the user's activity in the IDE.

### `ide/contextUpdate` Notification

The plugin **MAY** send an `ide/contextUpdate` [notification](<https://modelcontextprotocol.io/specification/2025-06-18/basic/index#notifications>) to the CLI whenever the user's context changes.

- **Triggering Events:** This notification should be sent (with a recommended debounce of 50ms) when:
  - A file is opened, closed, or focused.
  - The user's cursor position or text selection changes in the active file.
- **Payload (`IdeContext`):** The notification parameters **MUST** be an `IdeContext` object:

  ```typescript
  interface IdeContext {
    workspaceState?: {
      openFiles?: File[];
      isTrusted?: boolean;
    };
  }

  interface File {
    // Absolute path to the file
    path: string;
    // Last focused Unix timestamp (for ordering)
    timestamp: number;
    // True if this is the currently focused file
    isActive?: boolean;
    cursor?: {
      // 1-based line number
      line: number;
      // 1-based character number
      character: number;
    };
    // The text currently selected by the user
    selectedText?: string;
  }
  ```

  **Note:** The `openFiles` list should only include files that exist on disk. Virtual files (e.g., unsaved files without a path, editor settings pages) **MUST** be excluded.

### How the CLI Uses This Context

After receiving the `IdeContext` object, the CLI performs several normalization and truncation steps before sending the information to the model.

- **File Ordering:** The CLI uses the `timestamp` field to determine the most recently used files. It sorts the `openFiles` list based on this value. Therefore, your plugin **MUST** provide an accurate Unix timestamp for when a file was last focused.
- **Active File:** The CLI considers only the most recent file (after sorting) to be the "active" file. It will ignore the `isActive` flag on all other files and clear their `cursor` and `selectedText` fields. Your plugin should focus on setting `isActive: true` and providing cursor/selection details only for the currently focused file.
- **Truncation:** To manage token limits, the CLI truncates both the file list (to 10 files) and the `selectedText` (to 16KB).

While the CLI handles the final truncation, it is highly recommended that your plugin also limits the amount of context it sends.

## III. The Diffing Interface

To enable interactive code modifications, the plugin **MAY** expose a diffing interface. This allows the CLI to request that the IDE open a diff view, showing proposed changes to a file. The user can then review, edit, and ultimately accept or reject these changes directly within the IDE.

### `openDiff` Tool

The plugin **MUST** register an `openDiff` tool on its MCP server.

- **Description:** This tool instructs the IDE to open a modifiable diff view for a specific file.
- **Request (`OpenDiffRequest`):** The tool is invoked via a `tools/call` request. The `arguments` field within the request's `params` **MUST** be an `OpenDiffRequest` object.

  ```typescript
  interface OpenDiffRequest {
    // The absolute path to the file to be diffed.
    filePath: string;
    // The proposed new content for the file.
    newContent: string;
  }
  ```

- **Response (`CallToolResult`):** The tool **MUST** immediately return a `CallToolResult` to acknowledge the request and report whether the diff view was successfully opened.
  - On Success: If the diff view was opened successfully, the response **MUST** contain empty content (i.e., `content: []`).
  - On Failure: If an error prevented the diff view from opening, the response **MUST** have `isError: true` and include a `TextContent` block in the `content` array describing the error.

  The actual outcome of the diff (acceptance or rejection) is communicated asynchronously via notifications.

### `closeDiff` Tool

The plugin **MUST** register a `closeDiff` tool on its MCP server.

- **Description:** This tool instructs the IDE to close an open diff view for a specific file.
- **Request (`CloseDiffRequest`):** The tool is invoked via a `tools/call` request. The `arguments` field within the request's `params` **MUST** be an `CloseDiffRequest` object.

  ```typescript
  interface CloseDiffRequest {
    // The absolute path to the file whose diff view should be closed.
    filePath: string;
  }
  ```

- **Response (`CallToolResult`):** The tool **MUST** return a `CallToolResult`.
  - On Success: If the diff view was closed successfully, the response **MUST** include a single **TextContent** block in the content array containing the file's final content before closing.
  - On Failure: If an error prevented the diff view from closing, the response **MUST** have `isError: true` and include a `TextContent` block in the `content` array describing the error.

### `ide/diffAccepted` Notification

When the user accepts the changes in a diff view (e.g., by clicking an "Apply" or "Save" button), the plugin **MUST** send an `ide/diffAccepted` notification to the CLI.

- **Payload:** The notification parameters **MUST** include the file path and the final content of the file. The content may differ from the original `newContent` if the user made manual edits in the diff view.

  ```typescript
  {
    // The absolute path to the file that was diffed.
    filePath: string;
    // The full content of the file after acceptance.
    content: string;
  }
  ```

### `ide/diffRejected` Notification

When the user rejects the changes (e.g., by closing the diff view without accepting), the plugin **MUST** send an `ide/diffRejected` notification to the CLI.

- **Payload:** The notification parameters **MUST** include the file path of the rejected diff.

  ```typescript
  {
    // The absolute path to the file that was diffed.
    filePath: string;
  }
  ```

## IV. The Lifecycle Interface

The plugin **MUST** manage its resources and the discovery file correctly based on the IDE's lifecycle.

- **On Activation (IDE startup/plugin enabled):**
  1.  Start the MCP server.
  2.  Create the discovery file.
- **On Deactivation (IDE shutdown/plugin disabled):**
  1.  Stop the MCP server.
  2.  Delete the discovery file.


</details>
