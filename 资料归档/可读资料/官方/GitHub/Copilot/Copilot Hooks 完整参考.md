# Copilot Hooks 完整参考

列出配置位置、命令与 HTTP Hook、事件输入和运行环境，说明策略 Hook、进度消息、沙箱及错误行为等详细规则。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-08](<https://github.com/github/docs/commit/a88a068893eccd85833c44d7ef83a342a4ea9795>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/reference/hooks-reference>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Hooks 完整参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# GitHub Copilot 挂钩参考

在 Copilot CLI 和 Copilot 云 agent 中查找挂钩事件、配置格式和输入负载。

## 简介

挂钩是在会话期间在特定生命周期点执行的外部命令，支持自定义自动化、安全控制和集成。

两个 Copilot 表面支持挂钩：Copilot CLI 和 Copilot 云 agent。大多数配置格式和事件有效负载是相同的，但执行环境和可以触发的事件集不同。

在本文中，两个表面之间的不同行为在“仅 CLI”和“仅云 agent”注释中指出。任何未标记的内容都适用于两者。

## 挂钩位置

挂钩运行的位置以及可以存储挂钩配置文件的位置取决于表面：

* **Copilot CLI** — 挂钩在开发人员的本地计算机上与 CLI 相同的 shell 中运行。 CLI 支持本文中描述的所有挂钩事件。

钩子按顺序从以下源加载（策略，然后用户，然后项目，然后插件）并组合。当同一事件出现在多个源中时，来自所有源的所有钩子条目都会运行。

  * **策略级挂钩文件** — 平台相应策略目录中的JSON 文件，按字母顺序加载。策略挂钩是机器范围内的，并且在所有其他挂钩之前加载。它们无法被 `disableAllHooks` 禁用，并且无论文件夹信任状态如何都可用。请参阅下面的 [ 策略挂钩 ](<#政策挂钩>)。
  * **存储库级挂钩文件** - 存储库根目录中的`.github/hooks/*.json`。
  * **用户级挂钩文件** — 用户级挂钩目录中的`*.json`文件。默认情况下，在 macOS 和 Linux 上为 `~/.copilot/hooks/`，在 Windows 上为 `%USERPROFILE%\.copilot\hooks\`。如果设置了`COPILOT_HOME`，则为`$COPILOT_HOME/hooks/`。
  * ** 存储库设置中的内联 `hooks` 块 ** — 存储库中 `.github/copilot/settings.json`（Git 已提交）或 `.github/copilot/settings.local.json`（通常被 gitignored 且特定于用户）顶层的 `hooks` 字段。存储库中的交叉工具 `.claude/settings.json` 和 `.claude/settings.local.json` 文件也会被读取。
  * **用户级配置中的内联`hooks`块** - `~/.copilot/settings.json`顶层的`hooks`字段。 CLI 不再读取 `~/.copilot/config.json` 的钩子。
  * **由已安装插件贡献的钩子** - 由每个插件在插件安装目录内其自己的`hooks.json`（或在`hooks/hooks.json`下）声明。

* **Copilot 云 agent** — 挂钩在云 agent 为每个作业提供的临时 Linux 沙箱内运行。沙箱是非交互式的，具有受限的网络，并在作业结束时被销毁。事件子集会触发，并且仅 `bash`（或 `command`）条目受到认可。

挂钩配置是从克隆存储库中的 `.github/hooks/*.json` 文件加载的。

### 政策挂钩

仅限 > \[!NOTE]
> **Copilot CLI。** Copilot 云 agent 不支持策略挂钩。

策略挂钩是由管理员加载的计算机范围的挂钩。它们在所有其他挂钩之前加载，并且不能被 `disableAllHooks` 禁用。

策略挂钩有两个来源：

* **文件系统**：平台相应策略目录中的JSON文件，按字母顺序加载：
  * Linux/macOS: `/etc/github-copilot/policy.d/*.json`
  * Windows：`C:\ProgramData\GitHub\Copilot\policy.d\*.json`
* **Windows 注册表**：`HKLM\Software\Policies\GitHub\Copilot` 下的值（每个子项包含一个 `Policy` REG\_SZ 值，其中包含 JSON 策略文档）。

策略挂钩文件使用与用户和项目挂钩相同的挂钩配置格式（`{ "version": 1, "hooks": { ... } }`）。在 POSIX 系统上，策略文件必须由 root 所有，并且不能是组可写或全局可写。

策略挂钩旨在供企业 IT 管理员使用，并且需要提升的权限才能安装。最终用户无法修改它们。即使启用了会话沙箱，策略挂钩也始终在主机上运行 - 请参阅[沙盒会话](<#沙盒会话>)。

## 云agent执行环境

本节仅适用于**Copilot云agent**。它描述了影响您如何为云 agent 作业编写挂钩脚本和配置挂钩条目的约束。

|财产|价值|
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|操作系统|Linux。仅支持命令挂钩上的 `bash` 字段； `powershell` 条目将被忽略。跨平台 `command` 字段被视为后备。|
|工作目录|克隆存储库时为 `/workspace`，否则为 `/root`。在挂钩条目上设置 `cwd` 或从脚本引用文件时，请使用此路径。|
|文件系统|短暂的。作业结束时，由挂钩写入的文件（日志、CSV、成绩单）将被丢弃。要保留挂钩输出，请通过 `http` 挂钩条目发送它。|
|出站网络|受云agent防火墙限制。默认情况下，只有 GitHub 和 Copilot 主机名是可访问的；到达任何其他主机（例如 `https://hooks.example.com`）需要管理员配置的防火墙允许规则。|
|可用的环境变量|`GITHUB_COPILOT_API_TOKEN`和`GITHUB_COPILOT_GIT_TOKEN`在沙箱中设置。 `COPILOT_AGENT_PROMPT` 保存调用作业时使用的提示。 `HOME` 设置为 `/root`，因此任何解析 `~/...` 路径的挂钩脚本都会写入临时沙箱。未设置`GITHUB_TOKEN`。|
|互动性|完全非交互。 agent 运行时会预先授予所有工具权限，因此不会显示任何权限对话框，也不会向用户显示任何通知。|
|配置发现|在云 agent 作业中，默认情况下存在的唯一挂钩配置是克隆存储库内的 `.github/hooks/*.json`。沙箱不附带用户级挂钩文件、`settings.json`、`config.json` 或已安装的插件。|

## 钩子配置格式

挂钩配置文件使用 JSON 格式，版本为 `1`。

> \[!NOTE]
> 如果从目录（例如，`.github/hooks/`）加载的挂钩配置文件包含格式错误的挂钩项目，则仅删除并记录该项目 - 同一文件中的有效同级挂钩仍会加载。结构错误（无效的 JSON、错误的 `version` 或非数组事件列表）仍然会拒绝整个文件。 `settings.json` 中内联定义的挂钩仍然严格：任何项目级验证错误都会拒绝整个 `hooks` 字段。其他配置文件始终独立加载。

### 命令挂钩

命令挂钩运行 shell 脚本或可执行文件，并且受所有挂钩类型支持。

> \[!NOTE]
> **仅限云 agent。** 云 agent 在 Linux 沙箱中运行挂钩。仅支持 `bash` 字段； `powershell` 条目将被忽略。跨平台 `command` 字段被视为后备。

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "type": "command",
        "bash": "YOUR_BASH_COMMAND",
        "powershell": "YOUR_POWERSHELL_COMMAND",
        "cwd": "OPTIONAL/WORKING/DIRECTORY",
        "env": { "VAR": "VALUE" },
        "timeoutSec": 30
      }
    ]
  }
}
```

在Copilot CLI中，您可以使用`exec`和`args`直接运行可执行文件，而不是使用shell：

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "type": "command",
        "exec": "YOUR_EXECUTABLE",
        "args": ["YOUR_ARGUMENT"],
        "cwd": "OPTIONAL/WORKING/DIRECTORY",
        "env": { "VAR": "VALUE" },
        "timeoutSec": 30
      }
    ]
  }
}
```

将 `YOUR_EXECUTABLE` 替换为可执行文件名称或路径，将 `YOUR_ARGUMENT` 替换为要传递给它的参数。您可以在 `args` 数组中包含其他参数。

请勿将 `exec` 与 `bash`、`powershell` 或 `command` 组合使用。参数直接传递给可执行文件，无需 shell 解释，因此管道、重定向和全局扩展等 shell 功能不可用。

|领域|类型|必填|描述|
| ------------ | ---------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `args`       |字符串数组|否|参数直接传递给`exec`。仅在 Copilot CLI 中受支持。|
| `bash`       |字符串|`bash`、`powershell` 或 `command` 之一，除非指定 `exec`|Unix 的 shell 命令。|
| `command`    |字符串|`bash`、`powershell` 或 `command` 之一，除非指定 `exec`|跨平台回退。当这些字段不存在时，复制到 `bash` 和 `powershell`；显式的 `bash` 或 `powershell` 条目在其各自的平台上优先。|
| `cwd`        |字符串|否|命令的工作目录（相对于存储库根目录或绝对目录）。|
| `env`        |对象|否|要设置的环境变量（支持变量扩展）。|
| `exec`       |字符串|而不是 `bash`、`powershell` 和 `command`|可执行文件的名称或路径。无需 shell 即可直接运行可执行文件。仅在 Copilot CLI 中受支持。|
| `powershell` |字符串|`bash`、`powershell` 或 `command` 之一，除非指定 `exec`|适用于 Windows 的 Shell 命令。|
| `timeout`    |数量|否|`timeoutSec` 的别名，以秒为单位。仅当`timeoutSec`不存在时使用；当两者都存在时，`timeoutSec` 优先。|
| `timeoutSec` |数量|否|超时（以秒为单位）。默认值：`30`。|
| `type`       | `"command"`      |否|钩型。省略时默认为 `"command"`。|

#### 进度消息

命令挂钩可以在执行时向 CLI 时间轴发出进度状态行。在写入最终输出之前，将 `{"type": "progress", "message": "..."}` JSON 对象写入 stdout：

```bash
echo '{"type": "progress", "message": "Checking policy..."}'
# ... perform work ...
echo '{"permissionDecision": "allow"}'
```

设置 `"temporary": true` 以发出瞬态状态线。瞬态线会替换之前的瞬态线，并在助手响应时被清除，而不是在时间轴中累积：

```bash
echo '{"type": "progress", "message": "Routing...", "temporary": true}'
echo '{"type": "progress", "message": "Thinking...", "temporary": true}'
# ... perform work ...
echo '{"permissionDecision": "allow"}'
```

进度消息仅用于显示，不会影响挂钩输出或决策逻辑。

**混合进度消息时如何解析 stdout。** — CLI 在钩子运行时逐行扫描 stdout。修剪后，带有 `"type": "progress"` 的单个完整 JSON 对象的任何行都将作为进度事件使用，并从挂钩的输出流 ** 中删除 **。每隔一行（空白行、纯文本和不是进度消息的 JSON 对象）都会逐字保留。当挂钩退出时，保留的行将通过单个 `JSON.parse` 调用进行连接、修剪和解析：该结果是挂钩的输出（本文其他地方引用的“挂钩输出 JSON”）。这意味着：

* 与最终决策对象一起发出进度线（如上面的示例）是安全的，并且是预期的模式 - 进度线永远不会到达 JSON 解析器。
* 每条进度消息必须位于其自己的行上，并且在该行上必须有效 JSON。多行/漂亮打印的进度对象不会被识别为进度，并将保留在输出流中，它们可能会导致最终的 `JSON.parse` 失败。
* 相比之下，最终的决策对象可能跨越多行——只有进度*识别*是面向行的；进度剥离后剩下的内容被解析为一个 JSON 文档，而不是行分隔的 JSON。
* 如果剩余输出为空，或者无法解析为 JSON，则该钩子将被视为未产生任何输出，并会陷入默认行为。因此，stdout 上的两个或多个非进度 JSON 对象（例如，两个 `echo '{"permissionDecision": ...}'` 调用）将连接成无效的 JSON 并被忽略——仅发出一个最终决策对象。

#### 沙盒会话

仅> \[！NOTE]
> **Copilot CLI。**

启用会话沙箱后，来自存储库的命令挂钩、您的用户设置和插件将在其中运行，具有与 agent 的 shell 命令相同的访问权限。钩子还可以读取它加载的目录，因此插件可以运行它附带的脚本，并且插件钩子可以写入其数据目录（`$COPILOT_PLUGIN_DATA`）。

钩子的 `cwd` 和 `env` 字段不会扩大该访问权限：会话授权之外的 `cwd` 不会使钩子获得那里的访问权限，并且在钩子的 `env` 中设置的诸如 `TMPDIR` 或 `PATH` 之类的变量不会授予任何权限。当沙箱中的挂钩失败时，Copilot 每个挂钩和会话都会显示一次警告。要为挂钩提供更多访问权限，请在设置中将所需的路径添加到 `sandbox.userPolicy`。参见[GitHub CopilotCLI配置目录](<https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-config-dir-reference#user-settings-copilotsettingsjson>)。

策略挂钩始终在沙箱外部的主机上运行，即使启用了会话沙箱也是如此。策略挂钩不应从工作区运行脚本。

### HTTP 挂钩

HTTP 挂钩将输入有效负载作为 JSON `POST` 发送到 URL。

> \[!NOTE]
>
> * 默认情况下，仅允许 `https://` URL。当设置了 `COPILOT_HOOK_ALLOW_LOCALHOST=1` 时，非 TLS `http://` 请求将被拒绝，`http://localhost`、`http://127.*` 和 `http://[::1]` 除外。
> * **仅限云 agent。** 来自沙箱的出站网络受到云 agent 防火墙的限制，因此 `url` 必须以允许列表中的主机为目标。

```json
{
  "version": 1,
  "hooks": {
    "postToolUse": [
      {
        "type": "http",
        "url": "https://hooks.example.com/copilot",
        "headers": { "X-Source": "copilot-cli" },
        "allowedEnvVars": ["GITHUB_TOKEN"],
        "timeoutSec": 30
      }
    ]
  }
}
```

|领域|类型|必填|描述|
| ---------------- | --------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `allowedEnvVars` |字符串\[]|否|可以在 `headers` 值内扩展的环境变量名称。设置后，`url`必须使用`https://`。|
| `headers`        |对象|否|要包含的请求标头。|
| `timeout`        |数量|否|`timeoutSec` 的别名，以秒为单位。仅当`timeoutSec`不存在时使用；当两者都存在时，`timeoutSec` 优先。|
| `timeoutSec`     |数量|否|超时（以秒为单位）。默认值：`30`。|
| `type`           | `"http"`  |是的|必须是`"http"`。|
| `url`            |字符串|是的|目标URL。必须使用`http:`或`https:`。对于 `preToolUse` 和 `permissionRequest`，必须使用 `https://`，因为响应可以授予工具权限。|

### 提示钩子

提示挂钩自动提交文本，就像用户键入文本一样。它们仅在 `sessionStart` 上受支持。该文本可以是自然语言提示或斜杠命令。

> \[!NOTE]
> **Copilot CLI 仅限。** 提示挂钩仅针对 **新交互会话 ** 触发。它们不会在恢复时触发，并且不会在非交互式提示模式下触发 (`-p`)。

> \[!NOTE]
> **云 agent.** 云 agent 作业以非交互方式运行（类似于 `-p`），因此 `prompt` 挂钩条目可能不会触发。在依赖它们之前先确认您的环境中的行为。

```json
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "prompt",
        "prompt": "YOUR_PROMPT_TEXT_OR_SLASH_COMMAND"
      }
    ]
  }
}
```

|领域|类型|必填|描述|
| -------- | ---------- | -------- | -------------------------------------------------------------------- |
| `type`   | `"prompt"` |是的|必须是`"prompt"`。|
| `prompt` |字符串|是的|要提交的文本 - 可以是自然语言消息或斜杠命令。|

## 挂钩事件

下表列出了每个支持的事件。 **云 agent** 列显示事件是否在云 agent 下触发，并记录任何行为差异。

|活动|触发时|输出已处理|云agent|
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agentStop`             |主agent完成一回合。|是的——可以阻止并强制继续。|火灾。 `decision: "block"` 强制进行另一轮，这仍然计入作业的超时时间。|
| `errorOccurred`         |执行过程中出现错误。|否|火灾。|
| `notification`          |当 CLI 发出系统通知（shell 完成、agent 完成或空闲、权限提示、引发对话框）时异步触发。即发即忘：永远不会阻塞会话。支持 `notification_type` 上的 `matcher` 正则表达式模式（`matcher` 字段的值）。|可选 — 可以将 `additionalContext` 注入会话中。|**不触发。**云agent不会向用户显示通知（请参阅上面云agent执行环境表中的**交互性**行）。|
| `permissionRequest`     |在权限服务运行之前触发（规则引擎、会话批准、auto-allow/auto-deny 和用户提示）。如果合并的钩子输出返回 `behavior: "allow"` 或 `"deny"`，则该决定会短路正常的权限流 - 沙箱绕过请求 (`requestSandboxBypass: true`) 除外，其中 `allow` 不会预先批准转义，仅传播 `deny`（请参阅[`permissionRequest`决策控制](<#permissionrequest决策控制>)沙箱绕过异常）。支持 `toolName` 上的 `matcher` 正则表达式模式（`matcher` 字段的值）。|是的 - 可以以编程方式允许或拒绝。|工具调用是预先批准的，因此该挂钩要么不会触发，要么没有效果。使用 `preToolUse` 来做出权限决定。|
| `postToolUse`           |每个工具成功完成后。|是 - 可以修改工具结果或为模型注入额外的上下文。|火灾。|
| `postToolUseFailure`    |工具完成后出现故障。|是的 - 可以通过 `additionalContext`（命令挂钩的退出代码 `2`）提供恢复指导。|火灾。|
| `preCompact`            |上下文压缩即将开始（手动或自动）。支持 `matcher` 正则表达式模式（`matcher` 字段的值）以按触发器（`"manual"` 或 `"auto"`）进行过滤。|否——仅通知。|仅与 `trigger: "auto"` 一起触发。没有用户要求手动压实。|
| `preToolUse`            |在每个工具执行之前。|是的 — 可以允许、拒绝或修改。|火灾。 `"ask"` 的决策被视为 `"deny"`，因为没有用户可以回答。|
| `sessionEnd`            |会话终止。|否|每个作业触发一次。 `reason` 通常为 `"complete"`、`"error"` 或 `"timeout"`； `"abort"` 和 `"user_exit"` 不是预期的，因为没有用户。|
| `sessionStart`          |新的或恢复的会话开始。|可选 — 可以将 `additionalContext` 注入会话中。|每个作业作为新会话（而不是简历）触发一次。有关云 agent 下 `prompt` 条目的行为，请参阅上面的提示挂钩注释。|
| `subagentStart`         |生成子代理（在运行之前）。支持 `matcher` 正则表达式模式（`matcher` 字段的值）以按 agent 名称进行过滤。|可选 — 无法阻止创建，但 `additionalContext` 会添加到子代理的提示符之前。|火灾。|
| `subagentStop`          |一个子代理完成。|是的——可以阻止并强制继续。|火灾。|
| `userPromptSubmitted`   |用户提交提示。|可选 - `modifiedPrompt` 仅受 SDK 编程挂钩支持。|对于为作业提供的提示，最多触发一次。没有后续的用户输入。|
| `userPromptTransformed` |在运行时将提交的提示转换为其面向模型的内容之后触发，就在该内容被发出并保留到会话历史记录之前。针对主消息以及批量提交中的每条前面的消息运行。仅变异——它可以重写模型接收到的内容，但不能阻止或处理转弯。系统通知永远不会触发它。|是的——可以重写面向模型的内容。|火灾。|

## 挂钩事件输入负载

每个钩子事件都会向钩子处理程序传递一个 JSON 负载。支持两种有效负载格式，通过挂钩配置中使用的事件名称进行选择：

* **camelCase 格式** — 在camelCase 中配置事件名称（例如`sessionStart`）。字段使用 camelCase。
* **VS Code 兼容格式** — 在PascalCase 中配置事件名称（例如，`SessionStart`）。字段使用 Snake\_ 大小写来匹配 VS Code Copilot 扩展格式。

### `sessionStart` / `SessionStart`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;      // Unix timestamp in milliseconds
    cwd: string;
    source: "startup" | "resume" | "new";
    initialPrompt?: string;
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "SessionStart";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    source: "startup" | "resume" | "new";
    initial_prompt?: string;
}
```

**输出：**

```typescript
{
    additionalContext?: string;
}
```

`sessionStart`（命令和 HTTP 变体）仅消耗 `additionalContext`。返回 `{}` 或空表示不执行任何操作。

当多个 `sessionStart` 挂钩运行时，返回非空字符串 `additionalContext` 的成功挂钩按执行顺序贡献，并以 `"\n\n"` 精确分隔。空字符串或仅包含空格的字符串不会删除已经累积的上下文；如果每个钩子仅返回空或仅包含空格的字符串，则保留最后一个。组合字符串（包括分隔符）受到相同的 10 MiB 挂钩输出限制的限制 - 跨越它的贡献将被删除，先前累积的上下文将被保留，并且在会话中记录并引发仅大小警告。

### `sessionEnd` / `SessionEnd`

> \[!NOTE]
> **Copilot CLI 仅 - `/clear` 处于交互模式。** `/clear` 关闭旧会话并触发其 `sessionEnd` 与 `reason: "user_exit"` 挂钩，而CLI 继续运行。替换会话有自己独立的生命周期。因为 CLI 本身没有退出，所以这些钩子分派分离——它们在后台运行完整的 `timeoutSec`，而 `/clear` 立即返回，所以真正工作的钩子既不会被缩短，也不会停止提示。当您稍后退出 CLI 时，仍在运行的分离挂钩将随进程一起终止。

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    reason: "complete" | "error" | "abort" | "timeout" | "user_exit";
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "SessionEnd";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    reason: "complete" | "error" | "abort" | "timeout" | "user_exit";
}
```

### `userPromptSubmitted` / `UserPromptSubmit`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    prompt: string;
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "UserPromptSubmit";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    prompt: string;
}
```

**输出：**

```typescript
{
    modifiedPrompt?: string; // Replaces the prompt for the rest of the turn (SDK programmatic hooks only)
}
```

返回 `{}` 或空以保持提示不变。

> \[!NOTE]
>
> * `modifiedPrompt` 仅受 SDK 编程挂钩支持。命令和 HTTP 配置文件 `userPromptSubmitted` 挂钩的输出已被删除，包括 `modifiedPrompt`。托管或转向 Copilot 云 agent 会话使用的较轻的钩子处理运行时也会忽略它。这与 `preToolUse` 的运行时划分相同。
> * 非字符串 `modifiedPrompt`、`modifiedTransformedPrompt` 或已处理的 `responseContent` 值将被忽略，而不是破坏会话 - 命名该字段的类型警告将被记录并作为 `session.warning` 事件发出。空字符串覆盖会被拒绝，而不是清空面向模型的内容。 `null` `additionalContext` 值被视为不存在，而不是作为文字文本 `null` 注入。钩子输出（命令钩子的标准输出，HTTP 钩子的响应主体）在每次调用时限制在 10 MiB — 较大的响应会被截断而不是耗尽内存。

### `userPromptTransformed`

在运行时将提交的提示转换为其面向模型的内容之后触发，就在该内容被发出并保留到会话历史记录之前。针对主消息以及批量提交中的每条前面的消息运行。仅变异——它可以重写模型接收到的内容，但不能阻止或处理转弯。系统通知永远不会触发它。

**输入：**

```typescript
{
    sessionId: string;
    timestamp: number;         // epoch-ms integer
    cwd: string;
    prompt: string;            // user prompt after userPromptSubmitted hooks have run
    transformedPrompt: string; // runtime-transformed content the model will receive
}
```

**输出：**

```typescript
{
    modifiedTransformedPrompt?: string; // Replaces the model-facing content
}
```

返回 `{}` 或空以保持转换后的内容不变。 `modifiedTransformedPrompt` 仅替换发送到模型并存储在会话历史记录中的内容 - 时间线中显示的提示不受影响 - 如果会话恢复，替换内容将按原样重播。

### `preToolUse` / `PreToolUse`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
}
```

**VS Code 兼容输入：**

当配置了 PascalCase 事件名称 `PreToolUse` 时，负载使用 Snake\_case 字段名称来匹配 VS Code Copilot 扩展格式：

```typescript
{
    hook_event_name: "PreToolUse";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;    // Tool arguments (parsed from JSON string when possible)
}
```

**Claude 格式匹配器 (PascalCase `PreToolUse`):** 使用 PascalCase 事件名称配置的挂钩 `PreToolUse` — 如 Claude Code 插件和开放插件格式中使用 — 应用Claude 的匹配器语义而不是本机正则表达式规则：

* 每个工具都会触发 `*`、`**` 或空的 `matcher` 值。
* 当任何标记等于下表中的运行时工具名称或其 Claude 工具名称时，会触发文字名称或 `|` 分隔的替换（例如，`Bash` 或 `Edit|Write`）。
* 任何其他值都被视为区分大小写的正则表达式，锚定为 `^(?:PATTERN)$`，针对 Claude 工具名称（或没有 Claude 等效项的工具的运行时名称）进行测试。

PascalCase `PreToolUse` 的有效负载将 `tool_name` 报告为 Claude 工具名称（例如，`Bash`，而不是 `bash`）。

|运行时工具|Claude工具名称|
| ------------------------------------------- | --------------------------------------------- |
| `bash`, `powershell`                        | `Bash`                                        |
| `view`                                      | `Read`                                        |
| `create`                                    | `Write`                                       |
| `edit`, `str_replace_editor`, `apply_patch` | `Edit`                                        |
| `grep`, `rg`                                | `Grep`                                        |
| `glob`                                      | `Glob`                                        |
| `web_fetch`                                 | `WebFetch`                                    |
| `web_search`                                | `WebSearch`                                   |
| `ask_user`                                  | `AskUserQuestion`                             |
| `update_todo`                               | `TodoWrite`                                   |
| `task`                                      |`Agent`（也接受文字 `Task`）|

没有 Claude 等效项的工具保留其运行时名称。

> \[!IMPORTANT]
> **命令与 HTTP 的 `preToolUse` 失败行为：** 命令 `preToolUse` 挂钩在发生错误时 ** 失败关闭 ** - 崩溃或非零退出（包括退出 `2`）拒绝工具调用，即使钩子的标准输出 JSON 报告 `permissionDecision: "allow"`。命令挂钩 ** 超时始终是故障开放的，即使对于 `preToolUse` 和管理部署的策略挂钩 ** 也是如此 - 超时挂钩会显示警告，并让工具调用通过正常权限流程继续进行，而不是拒绝它。 HTTP `preToolUse` 挂钩是 ** 失败打开 ** - 网络错误、超时或非 2xx 响应落入默认权限流。选择符合您的安全要求的变体。

### `postToolUse` / `PostToolUse`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
    toolResult: {
        resultType: "success";
        textResultForLlm: string;
    }
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "PostToolUse";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;
    tool_result: {
        result_type: "success";
        text_result_for_llm: string;
    }
}
```

### `postToolUseFailure` / `PostToolUseFailure`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
    error: string;
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "PostToolUseFailure";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;
    error: string;
}
```

### `agentStop` / `Stop`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    stopReason: "end_turn";
    stop_hook_active: boolean; // true when this turn was already forced to continue by a prior "block" decision from this hook
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "Stop";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    stop_reason: "end_turn";
    stop_hook_active: boolean;
}
```

### `subagentStart`

> \[!NOTE]
> 内置 `general-purpose` agent 不会发出 `subagentStart` 或 `subagentStop` 事件。所有其他基于 YAML- 的内置 agents — 包括 `explore`、`task`、`code-review`、`rubber-duck`、`research` 和 `security-review` — 以及用户定义的自定义agents 发出这些事件。

**输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    agentName: string;
    agentDisplayName?: string;
    agentDescription?: string;
}
```

**输出：**

```typescript
{
    additionalContext?: string;
}
```

如果返回 `additionalContext`，它将被添加到子代理的第一条用户消息之前，从而为钩子提供了一种将项目特定的上下文、策略或指令注入到每个子代理调用中的方法。

当多个 `subagentStart` 挂钩运行时，它们的累积方式与 `sessionStart` 相同：具有非空字符串 `additionalContext` 的成功挂钩按 `"\n\n"` 连接的执行顺序贡献，空或仅空白字符串不会删除已经累积的上下文，并且组合字符串以 10 为界MiB 挂钩输出限制（超出限制的贡献将被丢弃，保留先前的上下文，并在会话中记录并引发仅大小警告）。

**匹配器：** 支持按 agent 名称过滤的可选 `matcher` 字段。该值被视为包装为 `^(?:matcher)$` 的正则表达式，并针对 `agentName` 进行测试。该模式必须与 ** 整个 ** agent 名称匹配，而不仅仅是子字符串。如果模式不是有效的正则表达式，则完全跳过挂钩（它不会针对任何 agent 触发）。

### `subagentStop` / `SubagentStop`

当子代理正常完成时触发，然后将结果返回给父代理。 `stopReason` 当前始终为 `"end_turn"`。此钩子在大响应溢出处理之前触发，因此 `response`（或 VS Code 兼容格式中的 `last_assistant_message`）携带完整的最终子代理响应文本。

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    agentId: string;
    agentType: string;
    agentName: string;
    agentDisplayName?: string;
    response: string;       // Full final subagent response text
    stopReason: "end_turn";
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "SubagentStop";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    agent_id: string;
    agent_type: string;
    agent_name: string;
    agent_display_name?: string;
    last_assistant_message: string; // The `response` text
    stop_reason: "end_turn";
}
```

### `errorOccurred` / `ErrorOccurred`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    error: {
        message: string;
        name: string;
        stack?: string;
    };
    errorContext: "model_call" | "tool_execution" | "system" | "user_input";
    recoverable: boolean;
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "ErrorOccurred";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    error: {
        message: string;
        name: string;
        stack?: string;
    };
    error_context: "model_call" | "tool_execution" | "system" | "user_input";
    recoverable: boolean;
}
```

### `preCompact` / `PreCompact`

**camelCase 输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    trigger: "manual" | "auto";
    customInstructions: string;
}
```

**VS Code 兼容输入：**

```typescript
{
    hook_event_name: "PreCompact";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    trigger: "manual" | "auto";
    custom_instructions: string;
}
```

## `preToolUse`决策控制

`preToolUse` 挂钩可以通过将 JSON 对象写入 stdout 来控制工具执行。

|领域|价值观|描述|
| -------------------------- | ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `permissionDecision`       | `"allow"`, `"deny"`, `"ask"` |该工具是否执行。空输出使用默认行为。在云 agent 下，`"ask"` 被视为 `"deny"`，因为没有用户可以应答。|
| `permissionDecisionReason` |字符串|原因显示在agent。当决策为 `"deny"` 时需要。|
| `modifiedArgs`             |对象|使用替换工具参数来代替原始参数。|

当Copilot CLI可以显示挂钩许可提示时，用户可以键入可选的反馈以及拒绝。该反馈被附加到 agent 收到的消息：`Denied by user via preToolUse hook prompt: <permissionDecisionReason>. The user provided the following feedback: <feedback>`。

## `agentStop` / `subagentStop` 决策控制

|领域|价值观|描述|
| ------------------ | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `decision`         | `"block"`, `"allow"` |`"block"` 使用 `reason` 作为提示强制进行另一个 agent 回合。|
| `reason`           |字符串|当`decision`为`"block"`时提示下一回合。|
| `modifiedResponse` |字符串|仅限 **`subagentStop`。** 替换允许子代理完成时返回到父级的响应 — 对于编辑或重新格式化子代理输出非常有用。不适用于`agentStop`。|

`decision` 和 `reason` 对于 `agentStop` 和 `subagentStop` 的行为相同。 `modifiedResponse`仅适用于`subagentStop`：

* 有效的 `block` 决策胜过 `modifiedResponse`：如果挂钩返回两者，则子代理继续并丢弃重写。
* 重写不会跨多个匹配的钩子进行组合。每个钩子都会收到相同的原始 `response`，最后一个返回 `modifiedResponse` 的钩子获胜 - 链接编辑器和格式化程序不会将编辑后的文本输入格式化程序。
* camelCase 和 VS Code 兼容配置的输出字段名称（`decision`、`reason`、`modifiedResponse`）相同。
* 命令和 HTTP 挂钩保留其他挂钩事件的许可行为：忽略不支持的判决字段和非对象 JSON 输出，并且 `reason` 仅与具有非空字符串的 `block` 决策一起生效。
* SDK 回调输出在合并之前进行验证：无效的 `decision`、不带 `block` 的 `reason`、不带非空 `reason` 的 `block` 或非对象输出使子代理挂钩失败。可选字段中的显式 `null` 被视为不存在。

> \[！NOTE]
> **失控守卫。**在8连续`block`延续后，CLI覆盖钩子并结束转弯，以防止无限循环。使用`agentStop`上的`stop_hook_active`输入字段检测本回合已被强制继续，并在达到上限之前进行自我限制。

## `postToolUse` 输出

`postToolUse` 挂钩可以修改工具结果或通过将 JSON 对象写入标准输出来为模型注入额外的上下文。

```typescript
{
    modifiedResult?: {
        resultType: "success";
        textResultForLlm: string;
    };
    additionalContext?: string;
}
```

|领域|类型|描述|
| ------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `modifiedResult`    |对象|更换工具结果。必须有 `resultType: "success"`。如果返回 `resultType: "failure"`，则故障将路由到下游，并且接下来会触发 `postToolUseFailure`。|
| `additionalContext` |字符串|`textResultForLlm` 附加了附加指南，以便模型在同一回合的工具输出后看到它。当多个挂钩返回 `additionalContext` 时，结果将使用双换行符连接，并以 10 KB 为上限。|

返回`{}`或空输出以保留原始成功结果。

> \[!NOTE]
> `modifiedResult` 受到 SDK 编程挂钩和 command/HTTP 配置文件 `postToolUse` 挂钩的尊重。

**匹配器：**针对`toolName`测试的可选正则表达式。正则表达式模式是 `matcher` 字段的值，编译为 `^(?:PATTERN)$`，并且必须与整个工具名称匹配。如果模式不是有效的正则表达式，则跳过挂钩。省略 `matcher` 以接收来自所有工具的结果。

```json
{
    "type": "command",
    "matcher": "bash|edit",
    "bash": "./scripts/log-tool.sh"
}
```

## `permissionRequest`决策控制

仅限 > \[!NOTE]
> **Copilot CLI。** `permissionRequest` 挂钩不适用于 Copilot 云 agent — 那里的工具调用已预先批准（请参阅**交互性**云agent执行环境表中的行）。使用`preToolUse`在云agent中做出权限决策。

`permissionRequest` 挂钩在权限服务运行之前触发 - 在规则检查、会话批准、auto-allow/auto-deny 和用户提示之前。如果挂钩返回 `behavior: "allow"` 或 `"deny"`，则该决定会短路正常的权限流程。不返回任何内容会导致正常的权限处理。使用它以编程方式批准或拒绝工具调用 - 在 CLI 管道模式 (`-p`) 和其他没有可用交互式提示的 CLI CI 用法中特别有用。它不适用于云agent。

所有配置的 `permissionRequest` 挂钩都会针对每个请求运行（`read` 和 `hook` 权限类型除外，它们在挂钩之前短路）。钩子输出与后来的钩子输出合并并覆盖先前的输出。

**沙箱绕过异常：**对于任何要求逃离沙箱的请求（`toolInput`中的`requestSandboxBypass: true`），挂钩`allow`不会预先批准该请求或短路用户提示 - 离开沙箱是用户必须进行的权限升级始终以交互方式确认。这涵盖了一个要求在沙箱外部运行的 shell 命令和一个沙箱网络策略拒绝其 URL 的 `web_fetch`。只有 `deny` 仍在传播（因此策略挂钩可以阻止逃逸）； `allow`（或无决定）会转至正常提示。

**匹配器：**针对`toolName`测试的可选正则表达式。正则表达式模式是 `matcher` 字段的值，锚定为 `^(?:PATTERN)$`，并且必须与完整工具名称匹配。设置后，挂钩仅在匹配工具名称时触发。

> \[!NOTE]
> **Claude 格式匹配器 (PascalCase `PermissionRequest`):** 使用 PascalCase 事件名称 `PermissionRequest` 配置的挂钩使用相同的 Claude 匹配器语义为`PreToolUse`。有关匹配器规则和工具名称表，请参阅 [Claude 格式匹配器 (PascalCase PreToolUse)](<#claude-format-matchers-pascalcase-pretooluse>)。

将 JSON 输出到 stdout 以控制权限决策：

|领域|价值观|描述|
| ----------- | ------------------- | ------------------------------------------------------------- |
| `behavior`  | `"allow"`, `"deny"` |是否批准或拒绝工具调用。|
| `message`   |字符串|拒绝时将原因反馈给LLM。|
| `interrupt` |布尔值|当`true`与`"deny"`组合时，完全停止agent。|

返回空输出或 `{}` 以进入正常权限流程。对于命令挂钩，退出代码 `2` 被视为拒绝； stdout JSON（如果有）与 `{"behavior":"deny"}` 合并，并且 stderr 被忽略。

## `notification` 挂钩

仅限 > \[！NOTE]
> **Copilot CLI。** `notification` 挂钩不会在 Copilot 云 agent 下触发。

当 CLI 发出系统通知时，`notification` 挂钩会异步触发。这些钩子是即发即忘的：它们永远不会阻塞会话，并且会记录并跳过任何错误。

**输入：**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    hook_event_name: "Notification";
    message: string;           // Human-readable notification text
    title?: string;            // Short title (e.g., "Permission needed", "Shell completed")
    notification_type: string; // One of the types listed below
}
```

**通知类型：**

|类型|当它着火时|
| -------------------------- | ------------------------------------------------------------------------------------ |
| `shell_completed`          |后台（异步）shell 命令完成|
| `shell_detached_completed` |分离的 shell 会话完成|
| `agent_completed`          |后台子代理完成（完成或失败）|
| `agent_idle`               |背景agent完成一轮并进入空闲状态（等待`write_agent`）|
| `permission_prompt`        |agent 请求执行工具的权限|
| `elicitation_dialog`       |agent 请求用户提供附加信息|

**输出：**

```typescript
{
    additionalContext?: string; // Injected into the session as a user message
}
```

如果返回 `additionalContext`，则该文本将作为前置用户消息注入到会话中。如果会话空闲，这可以触发进一步的 agent 处理。返回 `{}` 或空输出以不执行任何操作。

**匹配器：** `notification_type` 上的可选正则表达式。正则表达式模式是 `matcher` 字段的值，锚定为 `^(?:PATTERN)$`。省略 `matcher` 以接收所有通知类型。

## 匹配器过滤

多个事件在每个钩子条目上接受可选的 `matcher` 正则表达式，用于过滤触发钩子的调用。它被编译为`^(?:PATTERN)$`并且必须匹配完整值。无效的正则表达式会导致钩子条目被跳过。

|活动|`matcher` 匹配|
| ------------------- | ---------------------------------- |
| `notification`      | `notification_type`                |
| `permissionRequest` | `toolName`                         |
| `postToolUse`       | `toolName`                         |
| `preCompact`        |`trigger`（`"manual"` 或 `"auto"`）|
| `preToolUse`        | `toolName`                         |
| `subagentStart`     | `agentName`                        |

## 钩子匹配的工具名称

|工具名称|描述|
| ------------ | ----------------------------------------------------------------------------------------------------------------------- |
| `ask_user`   |向用户询问一个澄清的问题。在云 agent 下没有用户，因此 `ask_user` 不会产生有用的结果。|
| `bash`       |执行 shell 命令 (Unix)。|
| `create`     |创建新文件。|
| `edit`       |修改文件内容。|
| `glob`       |按模式查找文件。|
| `grep`       |搜索文件内容。|
| `powershell` |执行 shell 命令 (Windows)。不会出现在云 agent（Linux 沙箱）下。|
| `task`       |运行子代理任务。|
| `view`       |读取文件内容。|
| `web_fetch`  |获取网页。|

如果配置了多个相同类型的钩子，则它们按顺序执行。对于`preToolUse`，如果任何钩子返回`"deny"`，则该工具被阻止。对于大多数事件，都会记录并跳过挂钩失败（`2` 之外的非零退出代码或超时）。 **异常：`preToolUse`命令挂钩在退出`2`和非超时错误**时失败关闭 - 退出`2`、崩溃或任何其他非零退出（超时除外）都会拒绝工具调用，即使挂钩的标准输出JSON 报告`permissionDecision: "allow"`。 **超时始终是失败开放的，包括`preToolUse`和管理部署的策略挂钩**：出现警告，并且工具调用通过正常权限流程进行，而不是被拒绝。

## 命令挂钩的退出代码

|退出代码|含义|
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `0`            |成功。 `stdout` 被解析为钩子输出 JSON（如果存在）。|
| `2`            |默认情况下被视为警告。 `stderr` 已向用户显示，但运行仍在继续。对于 `permissionRequest` 和 `preToolUse`，退出 `2` 被视为拒绝：任何 `stdout` JSON 都会与拒绝决策合并，并且即使 JSON 报告 `permissionDecision: "allow"`，工具调用也会被拒绝。对于 `postToolUseFailure`，退出 `2` 被视为 `additionalContext`，并且 `stdout` 被附加到 agent 显示的故障中。|
|其他非零|记录为挂钩失败。运行继续（故障打开）。 **异常：`preToolUse` 失败关闭** - 非零退出（退出 2 除外）拒绝使用 `"Denied by preToolUse hook (hook errored)"` 进行工具调用。|
|超时|在`timeoutSec`之后被杀。记录错误，继续执行。 **每个事件的超时都是故障开放的，包括`preToolUse`和管理员部署的策略挂钩** - 会出现警告并继续处理，就好像挂钩尚未运行一样。对于 `preToolUse`，工具调用通过正常的权限流程进行，而不是被拒绝。崩溃或显式拒绝的钩子仍然无法关闭；只有超时可以豁免。记录的消息包括超时的命令，例如 `Hook command timed out after 30 seconds: my-validation-script.sh`（bash/PowerShell 脚本文本，或用于 exec 挂钩的 `program arg1 arg2 …`），被截断为 80 个字符。|

对于大多数事件，会记录并跳过非零退出和超时 - agent 执行继续。对于 `preToolUse` 命令挂钩，退出 2、崩溃和其他非零退出均会失败关闭并拒绝工具调用 — 退出 2 始终拒绝，即使挂钩的 `stdout` JSON 报告 `permissionDecision: "allow"` — 但**超时总是失败打开** - 缓慢或无法访问的挂钩不得默默地阻止工具调用或工作，即使该挂钩是由管理员作为策略部署的。

## 禁用所有钩子

当您想要将挂钩配置保留在磁盘上但阻止其运行时，请使用 `disableAllHooks`，例如：

* 调试问题并且您想要确认挂钩是原因而不删除您的配置。
* 在敏感任务（代码审查、发布分支、使用机密）期间暂停自动化，而不会丢失设置。 （仅**Copilot CLI。**）
* 在源代码管理中传送钩子文件，贡献者可以通过在其存储库中设置选项来选择在本地退出 `settings.json`。 （仅**Copilot CLI。**）
* 在交互会话期间暂时使缓慢或嘈杂的挂钩静音。 （仅**Copilot CLI。**）

在顶层将 `disableAllHooks` 设置为 `true` 以跳过文件中的每个钩子而不删除它。

```json
{
  "version": 1,
  "disableAllHooks": false,
  "hooks": {
    "preToolUse": [ /* hook entries */ ]
  }
}
```

行为取决于您设置标志的位置：

* **在单个 `.github/hooks/*.json` 文件内 ** — 仅跳过该文件中声明的钩子。受到CopilotCLI和Copilot云agent的荣誉。
* **仅在存储库的顶层 `settings.json`** — **Copilot CLI。** 对于该存储库中的会话，将跳过来自每个源（存储库文件、用户文件、插件和内联钩子块）的每个挂钩。策略挂钩不受影响并继续运行。云 agent 未加载 `settings.json`。

## 进一步阅读

* [与 GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>) 一起使用挂钩
* [GitHub Copilot 挂钩参考](<https://docs.github.com/en/copilot/reference/hooks-reference>)
* [GitHub Copilot CLI 命令参考 ](<https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference>)
* [GitHub Copilot 云 agent](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent>) 的概念


## 原文对照

<details>
<summary>展开完整原文</summary>

# GitHub Copilot hooks reference

Find hook events, configuration formats, and input payloads for hooks in Copilot CLI and Copilot cloud agent.

## Introduction

Hooks are external commands that execute at specific lifecycle points during a session, enabling custom automation, security controls, and integrations.

Hooks are supported in two Copilot surfaces: Copilot CLI and Copilot cloud agent. Most of the configuration format and event payloads are identical, but the execution environment and the set of events that can fire differ.

Throughout this article, behavior that differs between the two surfaces is called out in "CLI only" and "Cloud agent only" notes. Anything not marked applies to both.

## Hooks locations

The locations where hooks run, and where you can store hook configuration files, depend on the surface:

* **Copilot CLI** — hooks run on the developer's local machine in the same shell as the CLI. All hook events described in this article are supported by the CLI.

  Hooks are loaded from the following sources in order (policy, then user, then project, then plugins) and combined. When the same event appears in multiple sources, all hook entries from all sources are run.

  * **Policy-level hook files** — JSON files in the platform-appropriate policy directory, loaded in alphabetical order. Policy hooks are machine-wide and load before all other hooks. They cannot be disabled by `disableAllHooks` and are available regardless of folder trust state. See [Policy hooks](<#policy-hooks>) below.
  * **Repository-level hook files** — `.github/hooks/*.json` in the repository root.
  * **User-level hook files** — `*.json` files in the user-level hooks directory. By default this is `~/.copilot/hooks/` on macOS and Linux, or `%USERPROFILE%\.copilot\hooks\` on Windows. If `COPILOT_HOME` is set, it is `$COPILOT_HOME/hooks/`.
  * **Inline `hooks` block in repository settings** — the `hooks` field at the top level of `.github/copilot/settings.json` (Git committed) or `.github/copilot/settings.local.json` (typically gitignored and user specific) in the repository. Cross-tool `.claude/settings.json` and `.claude/settings.local.json` files in the repository are also read.
  * **Inline `hooks` block in user-level config** — the `hooks` field at the top level of `~/.copilot/settings.json`. The CLI no longer reads `~/.copilot/config.json` for hooks.
  * **Hooks contributed by installed plugins** — declared by each plugin in its own `hooks.json` (or under `hooks/hooks.json`) inside the plugin's installation directory.

* **Copilot cloud agent** — hooks run inside the ephemeral Linux sandbox that cloud agent provisions for each job. The sandbox is non-interactive, has a constrained network, and is destroyed when the job ends. A subset of events fires, and only `bash` (or `command`) entries are honored.

  Hook configuration is loaded from `.github/hooks/*.json` files in the cloned repository.

### Policy hooks

> \[!NOTE]
> **Copilot CLI only.** Policy hooks are not supported under Copilot cloud agent.

Policy hooks are machine-wide hooks loaded by administrators. They load before all other hooks and cannot be disabled by `disableAllHooks`.

Policy hooks are discovered from two sources:

* **Filesystem**: JSON files in the platform-appropriate policy directory, loaded in alphabetical order:
  * Linux/macOS: `/etc/github-copilot/policy.d/*.json`
  * Windows: `C:\ProgramData\GitHub\Copilot\policy.d\*.json`
* **Windows Registry**: Values under `HKLM\Software\Policies\GitHub\Copilot` (each subkey holds a `Policy` REG\_SZ value containing a JSON policy document).

Policy hook files use the same hook configuration format as user and project hooks (`{ "version": 1, "hooks": { ... } }`). On POSIX systems, policy files must be owned by root and must not be group- or world-writable.

Policy hooks are intended for use by enterprise IT administrators and require elevated privileges to install. End users cannot modify them. Policy hooks always run on the host, even when the session sandbox is enabled—see [Sandboxed sessions](<#sandboxed-sessions>).

## Cloud agent execution environment

This section applies to **Copilot cloud agent only**. It describes constraints that affect how you write hook scripts and configure hook entries for cloud agent jobs.

| Property                        | Value                                                                                                                                                                                                                                                                                      |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Operating system                | Linux. Only the `bash` field on command hooks is honored; `powershell` entries are ignored. The cross-platform `command` field is honored as a fallback.                                                                                                                                   |
| Working directory               | `/workspace` when a repository is cloned, otherwise `/root`. Use this path when setting `cwd` on a hook entry or when referencing files from a script.                                                                                                                                     |
| Filesystem                      | Ephemeral. Files written by hooks (logs, CSVs, transcripts) are discarded when the job ends. To retain hook output, send it via an `http` hook entry.                                                                                                                                      |
| Outbound network                | Restricted by the cloud agent firewall. By default only GitHub and Copilot hostnames are reachable; reaching any other host (for example `https://hooks.example.com`) requires an admin-configured firewall allow rule.                                                                    |
| Available environment variables | `GITHUB_COPILOT_API_TOKEN` and `GITHUB_COPILOT_GIT_TOKEN` are set in the sandbox. `COPILOT_AGENT_PROMPT` holds the prompt the job was invoked with. `HOME` is set to `/root`, so any hook script that resolves `~/...` paths writes into the ephemeral sandbox. `GITHUB_TOKEN` is not set. |
| Interactivity                   | Fully non-interactive. The agent runs with all tool permissions pre-granted, so no permission dialogs are shown and no notifications are surfaced to a user.                                                                                                                               |
| Configuration discovery         | In a cloud agent job, the only hook configuration that exists by default is `.github/hooks/*.json` inside the cloned repository. The sandbox does not ship with user-level hook files, `settings.json`, `config.json`, or installed plugins.                                               |

## Hook configuration format

Hook configuration files use JSON format with version `1`.

> \[!NOTE]
> If a hook configuration file loaded from a directory (for example, `.github/hooks/`) contains a malformed hook item, only that item is dropped and logged—valid sibling hooks in the same file still load. Structural errors (invalid JSON, a bad `version`, or a non-array event list) still reject the entire file. Hooks defined inline in `settings.json` remain strict: any item-level validation error rejects the whole `hooks` field. Other configuration files always load independently.

### Command hooks

Command hooks run shell scripts or executables and are supported on all hook types.

> \[!NOTE]
> **Cloud agent only.** Cloud agent runs hooks in a Linux sandbox. Only the `bash` field is honored; `powershell` entries are ignored. The cross-platform `command` field is honored as a fallback.

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "type": "command",
        "bash": "YOUR_BASH_COMMAND",
        "powershell": "YOUR_POWERSHELL_COMMAND",
        "cwd": "OPTIONAL/WORKING/DIRECTORY",
        "env": { "VAR": "VALUE" },
        "timeoutSec": 30
      }
    ]
  }
}
```

In Copilot CLI, you can use `exec` and `args` to run an executable directly instead of using a shell:

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "type": "command",
        "exec": "YOUR_EXECUTABLE",
        "args": ["YOUR_ARGUMENT"],
        "cwd": "OPTIONAL/WORKING/DIRECTORY",
        "env": { "VAR": "VALUE" },
        "timeoutSec": 30
      }
    ]
  }
}
```

Replace `YOUR_EXECUTABLE` with the executable name or path and `YOUR_ARGUMENT` with an argument to pass to it. You can include additional arguments in the `args` array.

Do not combine `exec` with `bash`, `powershell`, or `command`. Arguments are passed directly to the executable without shell interpretation, so shell features such as pipes, redirection, and glob expansion are not available.

| Field        | Type             | Required                                                              | Description                                                                                                                                                                          |
| ------------ | ---------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `args`       | array of strings | No                                                                    | Arguments passed directly to `exec`. Only supported in Copilot CLI.                                                                                                                  |
| `bash`       | string           | One of `bash`, `powershell`, or `command`, unless `exec` is specified | Shell command for Unix.                                                                                                                                                              |
| `command`    | string           | One of `bash`, `powershell`, or `command`, unless `exec` is specified | Cross-platform fallback. Copied to both `bash` and `powershell` when those fields are absent; explicit `bash` or `powershell` entries take precedence on their respective platforms. |
| `cwd`        | string           | No                                                                    | Working directory for the command (relative to repository root or absolute).                                                                                                         |
| `env`        | object           | No                                                                    | Environment variables to set (supports variable expansion).                                                                                                                          |
| `exec`       | string           | Instead of `bash`, `powershell`, and `command`                        | Executable name or path. Runs the executable directly without a shell. Only supported in Copilot CLI.                                                                                |
| `powershell` | string           | One of `bash`, `powershell`, or `command`, unless `exec` is specified | Shell command for Windows.                                                                                                                                                           |
| `timeout`    | number           | No                                                                    | Alias for `timeoutSec`, in seconds. Used only when `timeoutSec` is absent; `timeoutSec` takes precedence when both are present.                                                      |
| `timeoutSec` | number           | No                                                                    | Timeout in seconds. Default: `30`.                                                                                                                                                   |
| `type`       | `"command"`      | No                                                                    | Hook type. Defaults to `"command"` when omitted.                                                                                                                                     |

#### Progress messages

Command hooks can emit progress status lines to the CLI timeline while executing. Write a `{"type": "progress", "message": "..."}` JSON object to stdout before writing the final output:

```bash
echo '{"type": "progress", "message": "Checking policy..."}'
# ... perform work ...
echo '{"permissionDecision": "allow"}'
```

Set `"temporary": true` to emit a transient status line. A transient line replaces the previous transient line and is cleared when the assistant responds, instead of accumulating in the timeline:

```bash
echo '{"type": "progress", "message": "Routing...", "temporary": true}'
echo '{"type": "progress", "message": "Thinking...", "temporary": true}'
# ... perform work ...
echo '{"permissionDecision": "allow"}'
```

Progress messages are display-only and do not affect hook output or decision logic.

**How stdout is parsed when progress messages are mixed in.** — The CLI scans stdout line-by-line as the hook runs. Any line that, after trimming, is a single complete JSON object with `"type": "progress"` is consumed as a progress event and **removed from the hook's output stream**. Every other line—blank lines, plain text, and JSON objects that are not progress messages—is preserved verbatim. When the hook exits, the preserved lines are concatenated, trimmed, and parsed with a single `JSON.parse` call: that result is the hook's output (the "hook output JSON" referenced elsewhere in this article). This means:

* Emitting progress lines alongside a final decision object (as in the examples above) is safe and is the intended pattern—the progress lines never reach the JSON parser.
* Each progress message must be on its own line and must be valid JSON on that single line. Multi-line / pretty-printed progress objects are not recognized as progress and will be left in the output stream, where they will likely cause the final `JSON.parse` to fail.
* The final decision object, by contrast, may span multiple lines—only progress *recognition* is line-oriented; what remains after progress stripping is parsed as one JSON document, not as line-delimited JSON.
* If the leftover output is empty, or fails to parse as JSON, the hook is treated as having produced no output and falls through to default behavior. Two or more non-progress JSON objects on stdout (for example, two `echo '{"permissionDecision": ...}'` calls) will therefore concatenate into invalid JSON and be ignored—emit exactly one final decision object.

#### Sandboxed sessions

> \[!NOTE]
> **Copilot CLI only.**

When the session sandbox is enabled, command hooks from the repository, your user settings, and plugins run inside it, with the same access as the agent's shell commands. A hook can also read the directory it was loaded from, so a plugin can run the scripts it ships, and a plugin hook can write to its data directory (`$COPILOT_PLUGIN_DATA`).

A hook's `cwd` and `env` fields don't widen that access: a `cwd` outside the session's grants gives the hook no access there, and variables such as `TMPDIR` or `PATH` set in the hook's `env` grant nothing. When a hook fails in the sandbox, Copilot shows a warning once per hook and session. To give a hook more access, add the required paths to `sandbox.userPolicy` in your settings. See [GitHub Copilot CLI configuration directory](<https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-config-dir-reference#user-settings-copilotsettingsjson>).

Policy hooks always run on the host, outside the sandbox, even when the session sandbox is enabled. A policy hook should not run scripts from the workspace.

### HTTP hooks

HTTP hooks send the input payload as a JSON `POST` to a URL.

> \[!NOTE]
>
> * By default, only `https://` URLs are allowed. Non-TLS `http://` requests are rejected, except for `http://localhost`, `http://127.*`, and `http://[::1]` when `COPILOT_HOOK_ALLOW_LOCALHOST=1` is set.
> * **Cloud agent only.** Outbound network from the sandbox is restricted by the cloud agent firewall, so `url` must target an allow-listed host.

```json
{
  "version": 1,
  "hooks": {
    "postToolUse": [
      {
        "type": "http",
        "url": "https://hooks.example.com/copilot",
        "headers": { "X-Source": "copilot-cli" },
        "allowedEnvVars": ["GITHUB_TOKEN"],
        "timeoutSec": 30
      }
    ]
  }
}
```

| Field            | Type      | Required | Description                                                                                                                                              |
| ---------------- | --------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `allowedEnvVars` | string\[] | No       | Environment variable names that may be expanded inside `headers` values. When set, `url` must use `https://`.                                            |
| `headers`        | object    | No       | Request headers to include.                                                                                                                              |
| `timeout`        | number    | No       | Alias for `timeoutSec`, in seconds. Used only when `timeoutSec` is absent; `timeoutSec` takes precedence when both are present.                          |
| `timeoutSec`     | number    | No       | Timeout in seconds. Default: `30`.                                                                                                                       |
| `type`           | `"http"`  | Yes      | Must be `"http"`.                                                                                                                                        |
| `url`            | string    | Yes      | Target URL. Must use `http:` or `https:`. For `preToolUse` and `permissionRequest`, must use `https://` because the response can grant tool permissions. |

### Prompt hooks

Prompt hooks auto-submit text as if the user typed it. They are only supported on `sessionStart`. The text can be a natural language prompt or a slash command.

> \[!NOTE]
> **Copilot CLI only.** Prompt hooks fire only for **new interactive sessions**. They do not fire on resume, and they do not fire in non-interactive prompt mode (`-p`).

> \[!NOTE]
> **Cloud agent.** Cloud agent jobs run non-interactively (similar to `-p`), so `prompt` hook entries may not fire. Confirm the behavior in your environment before relying on them.

```json
{
  "version": 1,
  "hooks": {
    "sessionStart": [
      {
        "type": "prompt",
        "prompt": "YOUR_PROMPT_TEXT_OR_SLASH_COMMAND"
      }
    ]
  }
}
```

| Field    | Type       | Required | Description                                                          |
| -------- | ---------- | -------- | -------------------------------------------------------------------- |
| `type`   | `"prompt"` | Yes      | Must be `"prompt"`.                                                  |
| `prompt` | string     | Yes      | Text to submit—can be a natural language message or a slash command. |

## Hook events

The table below lists every supported event. The **Cloud agent** column shows whether the event fires under cloud agent and notes any behavior differences.

| Event                   | Fires when                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Output processed                                                                                 | Cloud agent                                                                                                                                                   |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `agentStop`             | The main agent finishes a turn.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Yes — can block and force continuation.                                                          | Fires. `decision: "block"` forces another turn, which still counts against the job's timeout.                                                                 |
| `errorOccurred`         | An error occurs during execution.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | No                                                                                               | Fires.                                                                                                                                                        |
| `notification`          | Fires asynchronously when the CLI emits a system notification (shell completion, agent completion or idle, permission prompts, elicitation dialogs). Fire-and-forget: never blocks the session. Supports a `matcher` regex pattern (the value of the `matcher` field) on `notification_type`.                                                                                                                                                                                                                                                                                                         | Optional — can inject `additionalContext` into the session.                                      | **Does not fire.** Cloud agent does not surface notifications to a user (see the **Interactivity** row in the Cloud agent execution environment table above). |
| `permissionRequest`     | Fires before the permission service runs (rules engine, session approvals, auto-allow/auto-deny, and user prompting). If the merged hook output returns `behavior: "allow"` or `"deny"`, that decision short-circuits the normal permission flow—except for a sandbox-bypass request (`requestSandboxBypass: true`), where an `allow` does not pre-approve the escape and only `deny` propagates (see the [`permissionRequest` decision control](<#permissionrequest-decision-control>) sandbox-bypass exception). Supports a `matcher` regex pattern (the value of the `matcher` field) on `toolName`. | Yes — can allow or deny programmatically.                                                        | Tool calls are pre-approved, so this hook either does not fire or has no effect. Use `preToolUse` to make permission decisions instead.                       |
| `postToolUse`           | After each tool completes successfully.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Yes — can modify the tool result or inject additional context for the model.                     | Fires.                                                                                                                                                        |
| `postToolUseFailure`    | After a tool completes with a failure.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Yes — can provide recovery guidance via `additionalContext` (exit code `2` for command hooks).   | Fires.                                                                                                                                                        |
| `preCompact`            | Context compaction is about to begin (manual or automatic). Supports a `matcher` regex pattern (the value of the `matcher` field) to filter by trigger (`"manual"` or `"auto"`).                                                                                                                                                                                                                                                                                                                                                                                                                      | No — notification only.                                                                          | Fires only with `trigger: "auto"`. There is no user to request manual compaction.                                                                             |
| `preToolUse`            | Before each tool executes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Yes — can allow, deny, or modify.                                                                | Fires. A decision of `"ask"` is treated as `"deny"` because no user is available to answer.                                                                   |
| `sessionEnd`            | The session terminates.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | No                                                                                               | Fires once per job. `reason` is typically `"complete"`, `"error"`, or `"timeout"`; `"abort"` and `"user_exit"` are not expected because there is no user.     |
| `sessionStart`          | A new or resumed session begins.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Optional — can inject `additionalContext` into the session.                                      | Fires once per job, as a new session (not a resume). See the Prompt hooks note above for the behavior of `prompt` entries under cloud agent.                  |
| `subagentStart`         | A subagent is spawned (before it runs). Supports a `matcher` regex pattern (the value of the `matcher` field) to filter by agent name.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Optional — cannot block creation, but `additionalContext` is prepended to the subagent's prompt. | Fires.                                                                                                                                                        |
| `subagentStop`          | A subagent completes.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes — can block and force continuation.                                                          | Fires.                                                                                                                                                        |
| `userPromptSubmitted`   | The user submits a prompt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Optional—`modifiedPrompt` is honored only by SDK programmatic hooks.                             | Fires at most once, for the prompt supplied to the job. There is no follow-up user input.                                                                     |
| `userPromptTransformed` | Fires after the runtime transforms a submitted prompt into its model-facing content, just before that content is emitted and persisted to session history. Runs for the primary message and for every preceding message in a batched submission. Mutation-only — it can rewrite the content the model receives, but not block or handle the turn. System notifications never trigger it.                                                                                                                                                                                                              | Yes — can rewrite the model-facing content.                                                      | Fires.                                                                                                                                                        |

## Hook event input payloads

Each hook event delivers a JSON payload to the hook handler. Two payload formats are supported, selected by the event name used in the hook configuration:

* **camelCase format** — Configure the event name in camelCase (for example, `sessionStart`). Fields use camelCase.
* **VS Code compatible format** — Configure the event name in PascalCase (for example, `SessionStart`). Fields use snake\_case to match the VS Code Copilot extension format.

### `sessionStart` / `SessionStart`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;      // Unix timestamp in milliseconds
    cwd: string;
    source: "startup" | "resume" | "new";
    initialPrompt?: string;
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "SessionStart";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    source: "startup" | "resume" | "new";
    initial_prompt?: string;
}
```

**Output:**

```typescript
{
    additionalContext?: string;
}
```

Only `additionalContext` is consumed for `sessionStart` (command and HTTP variants). Return `{}` or empty for no action.

When multiple `sessionStart` hooks run, successful hooks that return a non-empty string `additionalContext` contribute in execution order, separated by exactly `"\n\n"`. An empty or whitespace-only string does not erase already-accumulated context; if every hook returns only empty or whitespace-only strings, the last one is kept. The combined string (including separators) is bounded by the same 10 MiB hook-output limit—a contribution that would cross it is dropped, the previously accumulated context is kept, and a size-only warning is logged and raised in the session.

### `sessionEnd` / `SessionEnd`

> \[!NOTE]
> **Copilot CLI only — `/clear` in interactive mode.** `/clear` closes the old session and fires its `sessionEnd` hooks with `reason: "user_exit"` while the CLI keeps running. The replacement session has its own independent lifecycle. Because the CLI itself isn't exiting, these hooks dispatch detached—they run in the background with their full `timeoutSec` while `/clear` returns immediately, so a hook doing real work is neither cut short nor able to stall the prompt. A detached hook still running when you later quit the CLI is terminated along with the process.

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    reason: "complete" | "error" | "abort" | "timeout" | "user_exit";
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "SessionEnd";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    reason: "complete" | "error" | "abort" | "timeout" | "user_exit";
}
```

### `userPromptSubmitted` / `UserPromptSubmit`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    prompt: string;
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "UserPromptSubmit";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    prompt: string;
}
```

**Output:**

```typescript
{
    modifiedPrompt?: string; // Replaces the prompt for the rest of the turn (SDK programmatic hooks only)
}
```

Return `{}` or empty to leave the prompt unchanged.

> \[!NOTE]
>
> * `modifiedPrompt` is only honored by SDK programmatic hooks. Command and HTTP config-file `userPromptSubmitted` hooks have their output dropped, including `modifiedPrompt`. The lighter hooks-processing runtime used by hosted or steering Copilot cloud agent sessions also ignores it. This is the same runtime split as `preToolUse`.
> * A non-string `modifiedPrompt`, `modifiedTransformedPrompt`, or a handled `responseContent` value is ignored rather than corrupting the session—a type warning naming the field is logged and emitted as a `session.warning` event. An empty-string override is rejected instead of blanking the model-facing content. A `null` `additionalContext` value is treated as absent instead of being injected as the literal text `null`. Hook output (stdout for command hooks, the response body for HTTP hooks) is bounded at 10 MiB per invocation—a larger response is truncated rather than exhausting memory.

### `userPromptTransformed`

Fires after the runtime transforms a submitted prompt into its model-facing content, just before that content is emitted and persisted to session history. Runs for the primary message and for every preceding message in a batched submission. Mutation-only—it can rewrite the content the model receives, but not block or handle the turn. System notifications never trigger it.

**Input:**

```typescript
{
    sessionId: string;
    timestamp: number;         // epoch-ms integer
    cwd: string;
    prompt: string;            // user prompt after userPromptSubmitted hooks have run
    transformedPrompt: string; // runtime-transformed content the model will receive
}
```

**Output:**

```typescript
{
    modifiedTransformedPrompt?: string; // Replaces the model-facing content
}
```

Return `{}` or empty to leave the transformed content unchanged. `modifiedTransformedPrompt` replaces only the content sent to the model and stored in session history—the prompt displayed in the timeline is unaffected—and the replacement is replayed unchanged if the session is resumed.

### `preToolUse` / `PreToolUse`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
}
```

**VS Code compatible input:**

When configured with the PascalCase event name `PreToolUse`, the payload uses snake\_case field names to match the VS Code Copilot extension format:

```typescript
{
    hook_event_name: "PreToolUse";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;    // Tool arguments (parsed from JSON string when possible)
}
```

**Claude-format matchers (PascalCase `PreToolUse`):** Hooks configured with the PascalCase event name `PreToolUse`—as used in Claude Code plugins and the Open Plugins format—apply Claude's matcher semantics instead of the native regex rule:

* `*`, `**`, or an empty `matcher` value fires for every tool.
* A literal name or `|`-separated alternation (for example, `Bash` or `Edit|Write`) fires when any token equals the runtime tool name or its Claude tool name from the table below.
* Any other value is treated as a case-sensitive regex anchored as `^(?:PATTERN)$` tested against the Claude tool name (or the runtime name for tools with no Claude equivalent).

Payloads for PascalCase `PreToolUse` report `tool_name` as the Claude tool name (for example, `Bash`, not `bash`).

| Runtime tool                                | Claude tool name                              |
| ------------------------------------------- | --------------------------------------------- |
| `bash`, `powershell`                        | `Bash`                                        |
| `view`                                      | `Read`                                        |
| `create`                                    | `Write`                                       |
| `edit`, `str_replace_editor`, `apply_patch` | `Edit`                                        |
| `grep`, `rg`                                | `Grep`                                        |
| `glob`                                      | `Glob`                                        |
| `web_fetch`                                 | `WebFetch`                                    |
| `web_search`                                | `WebSearch`                                   |
| `ask_user`                                  | `AskUserQuestion`                             |
| `update_todo`                               | `TodoWrite`                                   |
| `task`                                      | `Agent` (the literal `Task` is also accepted) |

Tools with no Claude equivalent keep their runtime names.

> \[!IMPORTANT]
> **Command vs HTTP fail behavior for `preToolUse`:** Command `preToolUse` hooks are **fail-closed** on errors—a crash or non-zero exit (including exit `2`) denies the tool call, even if the hook's stdout JSON reports `permissionDecision: "allow"`. Command hook **timeouts are always fail-open, even for `preToolUse` and admin-deployed policy hooks**—a timed-out hook surfaces a warning and lets the tool call proceed through the normal permission flow instead of denying it. HTTP `preToolUse` hooks are **fail-open**—a network error, timeout, or non-2xx response falls through to the default permission flow. Choose the variant that matches your security requirements.

### `postToolUse` / `PostToolUse`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
    toolResult: {
        resultType: "success";
        textResultForLlm: string;
    }
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "PostToolUse";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;
    tool_result: {
        result_type: "success";
        text_result_for_llm: string;
    }
}
```

### `postToolUseFailure` / `PostToolUseFailure`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    toolName: string;
    toolArgs: unknown;
    error: string;
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "PostToolUseFailure";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    tool_name: string;
    tool_input: unknown;
    error: string;
}
```

### `agentStop` / `Stop`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    stopReason: "end_turn";
    stop_hook_active: boolean; // true when this turn was already forced to continue by a prior "block" decision from this hook
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "Stop";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    stop_reason: "end_turn";
    stop_hook_active: boolean;
}
```

### `subagentStart`

> \[!NOTE]
> The built-in `general-purpose` agent does not emit `subagentStart` or `subagentStop` events. All other built-in YAML-based agents—including `explore`, `task`, `code-review`, `rubber-duck`, `research`, and `security-review`—and user-defined custom agents emit these events.

**Input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    agentName: string;
    agentDisplayName?: string;
    agentDescription?: string;
}
```

**Output:**

```typescript
{
    additionalContext?: string;
}
```

If `additionalContext` is returned, it is prepended to the subagent's first user message, giving hooks a way to inject project-specific context, policies, or instructions into every subagent invocation.

When multiple `subagentStart` hooks run, they accumulate the same way as `sessionStart`: successful hooks with a non-empty string `additionalContext` contribute in execution order joined by `"\n\n"`, empty or whitespace-only strings don't erase already-accumulated context, and the combined string is bounded by the 10 MiB hook-output limit (an over-limit contribution is dropped, the prior context is kept, and a size-only warning is logged and raised in the session).

**Matcher:** Supports an optional `matcher` field that filters by agent name. The value is treated as a regular expression wrapped as `^(?:matcher)$` and tested against `agentName`. The pattern must match the **entire** agent name, not just a substring. If the pattern is not a valid regular expression, the hook is skipped entirely (it will not fire for any agent).

### `subagentStop` / `SubagentStop`

Fires when a subagent completes normally, before returning results to the parent. `stopReason` is currently always `"end_turn"`. This hook fires before large-response spill handling, so `response` (or `last_assistant_message` in the VS Code compatible format) carries the full final subagent response text.

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    agentId: string;
    agentType: string;
    agentName: string;
    agentDisplayName?: string;
    response: string;       // Full final subagent response text
    stopReason: "end_turn";
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "SubagentStop";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    agent_id: string;
    agent_type: string;
    agent_name: string;
    agent_display_name?: string;
    last_assistant_message: string; // The `response` text
    stop_reason: "end_turn";
}
```

### `errorOccurred` / `ErrorOccurred`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    error: {
        message: string;
        name: string;
        stack?: string;
    };
    errorContext: "model_call" | "tool_execution" | "system" | "user_input";
    recoverable: boolean;
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "ErrorOccurred";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    error: {
        message: string;
        name: string;
        stack?: string;
    };
    error_context: "model_call" | "tool_execution" | "system" | "user_input";
    recoverable: boolean;
}
```

### `preCompact` / `PreCompact`

**camelCase input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    transcriptPath: string;
    trigger: "manual" | "auto";
    customInstructions: string;
}
```

**VS Code compatible input:**

```typescript
{
    hook_event_name: "PreCompact";
    session_id: string;
    timestamp: string;      // ISO 8601 timestamp
    cwd: string;
    transcript_path: string;
    trigger: "manual" | "auto";
    custom_instructions: string;
}
```

## `preToolUse` decision control

The `preToolUse` hook can control tool execution by writing a JSON object to stdout.

| Field                      | Values                       | Description                                                                                                                                              |
| -------------------------- | ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `permissionDecision`       | `"allow"`, `"deny"`, `"ask"` | Whether the tool executes. Empty output uses default behavior. Under cloud agent, `"ask"` is treated as `"deny"` because no user is available to answer. |
| `permissionDecisionReason` | string                       | Reason shown to the agent. Required when decision is `"deny"`.                                                                                           |
| `modifiedArgs`             | object                       | Substitute tool arguments to use instead of the originals.                                                                                               |

When Copilot CLI can show the hook-permission prompt, the user can type optional feedback along with a denial. That feedback is appended to the message the agent receives: `Denied by user via preToolUse hook prompt: <permissionDecisionReason>. The user provided the following feedback: <feedback>`.

## `agentStop` / `subagentStop` decision control

| Field              | Values               | Description                                                                                                                                                                                         |
| ------------------ | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `decision`         | `"block"`, `"allow"` | `"block"` forces another agent turn using `reason` as the prompt.                                                                                                                                   |
| `reason`           | string               | Prompt for the next turn when `decision` is `"block"`.                                                                                                                                              |
| `modifiedResponse` | string               | **`subagentStop` only.** Replaces the response returned to the parent when the subagent is allowed to complete—useful for redacting or reformatting subagent output. Not applicable to `agentStop`. |

`decision` and `reason` behave the same for both `agentStop` and `subagentStop`. `modifiedResponse` applies only to `subagentStop`:

* A valid `block` decision wins over `modifiedResponse`: if a hook returns both, the subagent continues and the rewrite is discarded.
* Rewrites do not compose across multiple matching hooks. Every hook receives the same original `response`, and the last hook to return `modifiedResponse` wins—chaining a redactor and a formatter does not feed the redacted text into the formatter.
* The output field names (`decision`, `reason`, `modifiedResponse`) are the same for both the camelCase and VS Code compatible configs.
* Command and HTTP hooks keep the permissive behavior of other hook events: unsupported verdict fields and non-object JSON outputs are ignored, and `reason` only takes effect alongside a `block` decision with a nonempty string.
* SDK callback outputs are validated before merging: an invalid `decision`, a `reason` without `block`, a `block` without a nonempty `reason`, or a non-object output fails the subagent hook. An explicit `null` in an optional field is treated as absent.

> \[!NOTE]
> **Runaway guard.** After 8 consecutive `block` continuations, the CLI overrides the hook and ends the turn anyway, to prevent an unbounded loop. Use the `stop_hook_active` input field on `agentStop` to detect that this turn was already forced to continue, and self-limit before hitting the cap.

## `postToolUse` output

The `postToolUse` hook can modify the tool result or inject additional context for the model by writing a JSON object to stdout.

```typescript
{
    modifiedResult?: {
        resultType: "success";
        textResultForLlm: string;
    };
    additionalContext?: string;
}
```

| Field               | Type   | Description                                                                                                                                                                                                                       |
| ------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `modifiedResult`    | object | Replacement tool result. Must have `resultType: "success"`. If returned with `resultType: "failure"`, the failure routes downstream and `postToolUseFailure` fires next.                                                          |
| `additionalContext` | string | Additional guidance appended to `textResultForLlm` so the model sees it after the tool output on the same turn. When multiple hooks return `additionalContext`, the results are joined with a double newline and capped at 10 KB. |

Return `{}` or empty output to keep the original successful result.

> \[!NOTE]
> `modifiedResult` is honored by both SDK programmatic hooks and command/HTTP config-file `postToolUse` hooks.

**Matcher:** Optional regex tested against `toolName`. The regex pattern is the value of the `matcher` field, compiled as `^(?:PATTERN)$`, and must match the entire tool name. If the pattern is not a valid regular expression, the hook is skipped. Omit `matcher` to receive results from all tools.

```json
{
    "type": "command",
    "matcher": "bash|edit",
    "bash": "./scripts/log-tool.sh"
}
```

## `permissionRequest` decision control

> \[!NOTE]
> **Copilot CLI only.** The `permissionRequest` hook does not apply under Copilot cloud agent—tool calls there are pre-approved (see the **Interactivity** row in the Cloud agent execution environment table). Use `preToolUse` to make permission decisions in cloud agent.

The `permissionRequest` hook fires before the permission service runs—before rule checks, session approvals, auto-allow/auto-deny, and user prompting. If hooks return `behavior: "allow"` or `"deny"`, that decision short-circuits the normal permission flow. Returning nothing falls through to normal permission handling. Use it to approve or deny tool calls programmatically—especially useful in CLI pipe mode (`-p`) and other CLI CI usages where no interactive prompt is available. It does not apply to cloud agent.

All configured `permissionRequest` hooks run for each request (except `read` and `hook` permission kinds, which short-circuit before hooks). Hook outputs are merged with later hook outputs overriding earlier ones.

**Sandbox-bypass exception:** for any request that asks to escape the sandbox (`requestSandboxBypass: true` in `toolInput`), a hook `allow` does not pre-approve the request or short-circuit the user prompt—leaving the sandbox is a privilege escalation the user must always confirm interactively. This covers a shell command asking to run outside the sandbox and a `web_fetch` whose URL the sandbox network policy denies. Only `deny` still propagates (so a policy hook can block the escape); an `allow` (or no decision) falls through to the normal prompt.

**Matcher:** Optional regex tested against `toolName`. The regex pattern is the value of the `matcher` field, anchored as `^(?:PATTERN)$`, and must match the full tool name. When set, the hook fires only for matching tool names.

> \[!NOTE]
> **Claude-format matchers (PascalCase `PermissionRequest`):** Hooks configured with the PascalCase event name `PermissionRequest` use the same Claude matcher semantics as `PreToolUse`. See [Claude-format matchers (PascalCase PreToolUse)](<#claude-format-matchers-pascalcase-pretooluse>) for the matcher rules and tool name table.

Output JSON to stdout to control the permission decision:

| Field       | Values              | Description                                                   |
| ----------- | ------------------- | ------------------------------------------------------------- |
| `behavior`  | `"allow"`, `"deny"` | Whether to approve or deny the tool call.                     |
| `message`   | string              | Reason fed back to the LLM when denying.                      |
| `interrupt` | boolean             | When `true` combined with `"deny"`, stops the agent entirely. |

Return empty output or `{}` to fall through to the normal permission flow. For command hooks, exit code `2` is treated as a deny; stdout JSON (if any) is merged with `{"behavior":"deny"}`, and stderr is ignored.

## `notification` hook

> \[!NOTE]
> **Copilot CLI only.** The `notification` hook does not fire under Copilot cloud agent.

The `notification` hook fires asynchronously when the CLI emits a system notification. These hooks are fire-and-forget: they never block the session, and any errors are logged and skipped.

**Input:**

```typescript
{
    sessionId: string;
    timestamp: number;
    cwd: string;
    hook_event_name: "Notification";
    message: string;           // Human-readable notification text
    title?: string;            // Short title (e.g., "Permission needed", "Shell completed")
    notification_type: string; // One of the types listed below
}
```

**Notification types:**

| Type                       | When it fires                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------ |
| `shell_completed`          | A background (async) shell command finishes                                          |
| `shell_detached_completed` | A detached shell session completes                                                   |
| `agent_completed`          | A background subagent finishes (completed or failed)                                 |
| `agent_idle`               | A background agent finishes a turn and enters idle state (waiting for `write_agent`) |
| `permission_prompt`        | The agent requests permission to execute a tool                                      |
| `elicitation_dialog`       | The agent requests additional information from the user                              |

**Output:**

```typescript
{
    additionalContext?: string; // Injected into the session as a user message
}
```

If `additionalContext` is returned, the text is injected into the session as a prepended user message. This can trigger further agent processing if the session is idle. Return `{}` or empty output to take no action.

**Matcher:** Optional regex on `notification_type`. The regex pattern is the value of the `matcher` field, anchored as `^(?:PATTERN)$`. Omit `matcher` to receive all notification types.

## Matcher filtering

Several events accept an optional `matcher` regex on each hook entry that filters which invocations the hook fires for. It is compiled as `^(?:PATTERN)$` and must match the full value. Invalid regexes cause the hook entry to be skipped.

| Event               | `matcher` is matched against       |
| ------------------- | ---------------------------------- |
| `notification`      | `notification_type`                |
| `permissionRequest` | `toolName`                         |
| `postToolUse`       | `toolName`                         |
| `preCompact`        | `trigger` (`"manual"` or `"auto"`) |
| `preToolUse`        | `toolName`                         |
| `subagentStart`     | `agentName`                        |

## Tool names for hook matching

| Tool name    | Description                                                                                                             |
| ------------ | ----------------------------------------------------------------------------------------------------------------------- |
| `ask_user`   | Ask the user a clarifying question. Under cloud agent there is no user, so `ask_user` does not produce a useful result. |
| `bash`       | Execute shell commands (Unix).                                                                                          |
| `create`     | Create new files.                                                                                                       |
| `edit`       | Modify file contents.                                                                                                   |
| `glob`       | Find files by pattern.                                                                                                  |
| `grep`       | Search file contents.                                                                                                   |
| `powershell` | Execute shell commands (Windows). Does not appear under cloud agent (Linux sandbox).                                    |
| `task`       | Run subagent tasks.                                                                                                     |
| `view`       | Read file contents.                                                                                                     |
| `web_fetch`  | Fetch web pages.                                                                                                        |

If multiple hooks of the same type are configured, they execute in order. For `preToolUse`, if any hook returns `"deny"`, the tool is blocked. For most events, hook failures (non-zero exit codes other than `2`, or timeouts) are logged and skipped. **Exception: `preToolUse` command hooks are fail-closed on exit `2` and on non-timeout errors**—exit `2`, a crash, or any other non-zero exit (other than a timeout) denies the tool call, even if the hook's stdout JSON reports `permissionDecision: "allow"`. **Timeouts are always fail-open, including for `preToolUse` and admin-deployed policy hooks**: a warning is surfaced and the tool call proceeds through the normal permission flow rather than being denied.

## Exit codes for command hooks

| Exit code      | Meaning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `0`            | Success. `stdout` is parsed as the hook output JSON if present.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `2`            | Treated as a warning by default. `stderr` is surfaced to the user but the run continues. For `permissionRequest` and `preToolUse`, exit `2` is treated as a deny: any `stdout` JSON is merged with the deny decision and the tool call is denied even if that JSON reports `permissionDecision: "allow"`. For `postToolUseFailure`, exit `2` is treated as `additionalContext` and `stdout` is appended to the failure shown to the agent.                                                                                                                                                                                                                                         |
| Other non-zero | Logged as a hook failure. The run continues (fail-open). **Exception: `preToolUse` is fail-closed**—a non-zero exit (other than exit 2) denies the tool call with `"Denied by preToolUse hook (hook errored)"`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Timeout        | Killed after `timeoutSec`. Error logged, execution continues. **Timeouts are fail-open for every event, including `preToolUse` and admin-deployed policy hooks**—a warning is surfaced and processing proceeds as if the hook had not run. For `preToolUse`, the tool call proceeds through the normal permission flow rather than being denied. A crashed or explicitly-denying hook still fails-closed; only timeouts are exempt. The logged message includes the command that timed out, for example `Hook command timed out after 30 seconds: my-validation-script.sh` (the bash/PowerShell script text, or `program arg1 arg2 …` for exec hooks), truncated to 80 characters. |

For most events, non-zero exits and timeouts are logged and skipped—agent execution continues. For `preToolUse` command hooks, exit 2, crashes, and other non-zero exits all fail-closed and deny the tool call—exit 2 always denies, even if the hook's `stdout` JSON reports `permissionDecision: "allow"`—but **timeouts always fail-open**—a slow or unreachable hook must not silently block tool calls or work, even when the hook was deployed by an administrator as policy.

## Disable all hooks

Use `disableAllHooks` when you want to keep your hook configuration on disk but stop it from running—for example:

* Debugging an issue and you want to confirm a hook is the cause without deleting your config.
* Pausing automation during a sensitive task (a code review, a release branch, working with secrets) without losing the setup. (**Copilot CLI only.**)
* Shipping a hooks file in source control that contributors can opt out of locally by setting the option in their repository `settings.json`. (**Copilot CLI only.**)
* Temporarily silencing slow or noisy hooks during an interactive session. (**Copilot CLI only.**)

Set `disableAllHooks` to `true` at the top level to skip every hook in the file without deleting it.

```json
{
  "version": 1,
  "disableAllHooks": false,
  "hooks": {
    "preToolUse": [ /* hook entries */ ]
  }
}
```

Behavior depends on where you set the flag:

* **Inside a single `.github/hooks/*.json` file** — only the hooks declared in that file are skipped. Honored by both Copilot CLI and Copilot cloud agent.
* **At the top level of repository `settings.json`** — **Copilot CLI only.** Every hook from every source (repository files, user files, plugins, and inline hook blocks) is skipped for sessions in that repository. Policy hooks are not affected and continue to run. Cloud agent does not load `settings.json`.

## Further reading

* [Using hooks with GitHub Copilot CLI](<https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks>)
* [GitHub Copilot hooks reference](<https://docs.github.com/en/copilot/reference/hooks-reference>)
* [GitHub Copilot CLI command reference](<https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference>)
* [Concepts for GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/concepts/agents/cloud-agent>)


</details>
