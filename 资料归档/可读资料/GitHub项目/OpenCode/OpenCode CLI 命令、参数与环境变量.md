# OpenCode CLI 命令、参数与环境变量

命令参考。覆盖 Agent、认证、MCP、会话、非交互运行、服务、PR、插件和调试等子命令，提供全局参数和实验功能说明，适合查具体操作。

- 来源：OpenCode
- 资料类型：命令参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-08-26](<https://github.com/anomalyco/opencode/commit/fd9bd448a2e68990e7aed3495e5590cecb934bfb>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：实验参数与各子命令支持范围可能随版本变化。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/cli.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode CLI 命令、参数与环境变量.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: CLI
description: OpenCode CLI options and commands.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

当不带任何参数运行时，OpenCode CLI 默认启动 [TUI](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md>)。

```bash
opencode
```

但它也接受本页记录的命令。这允许您以编程方式与 OpenCode 进行交互。

```bash
opencode run "Explain how closures work in JavaScript"
```

---

### 推

启动OpenCode终端用户界面。

```bash
opencode [project]
```

#### 旗帜

|旗帜|短|描述|
| ------------------------------------------- | ----- | ----------------------------------------------------------------------- |
| `--continue`    | `-c`  |Continue 最后一次会议|
| `--session`     | `-s`  |会话 ID 继续|
| `--fork`        |       |继续时分叉会话（与 `--continue` 或 `--session` 一起使用）|
| `--prompt`      |       |提示使用|
| `--model`       | `-m`  |以 provider/model 形式使用的型号|
| `--agent`       |       |Agent使用|
| `--auto`        |       |自动批准未明确拒绝的权限|
| `--port`        |       |监听端口|
| `--hostname`    |       |要监听的主机名|
| `--mdns`        |       |启用 mDNS 发现|
| `--mdns-domain` |       |自定义mDNS域名|
| `--cors`        |       |允许 CORS 的其他浏览器源|

---

## 命令

OpenCode CLI 还具有以下命令。

---

### agent

管理OpenCode的agents。

```bash
opencode agent [command]
```

---

#### 创造

使用自定义配置创建新的 agent。

```bash
opencode agent create
```

此命令将指导您使用自定义系统提示和权限配置创建新的 agent。任何您不允许的内容都会在生成的 agent 的 frontmatter 中被拒绝。

#### 旗帜

|旗帜|短|描述|
| ------------------------------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--path`        |       |将 agent 文件写入的目录（根据提示默认为全局或 `.opencode/agent`）|
| `--description` |       |agent 应该做什么|
| `--mode`        |       |Agent 模式：`all`、`primary` 或 `subagent`|
| `--permissions` |       |以逗号分隔的允许权限列表（默认值：全部）。可用：`bash`、`read`、`edit`、`glob`、`grep`、`webfetch`、`task`、`todowrite`、 `websearch`、`lsp`、`skill`。任何遗漏都会被拒绝。别名：`--tools`|
| `--model`       | `-m`  |要使用的模型，格式为 `provider/model`|

传递所有 `--path`、`--description`、`--mode` 和 `--permissions` 以非交互方式运行该命令。

---

#### 列表

列出所有可用的agents。

```bash
opencode agent list
```

---

### 附加

将终端连接到已通过 `serve` 或 `web` 命令启动的正在运行的 OpenCode 后端服务器。

```bash
opencode attach [url]
```

这允许将 TUI 与远程 OpenCode 后端一起使用。例如：

```bash
# Start the backend server for web/mobile access
opencode web --port 4096 --hostname 0.0.0.0

# In another terminal, attach the TUI to the running backend
opencode attach http://10.20.30.40:4096
```

#### 旗帜

|旗帜|短|描述|
| ---------------------------------------- | ----- | -------------------------------------------------------------------------- |
| `--dir`      |       |启动 TUI 的工作目录|
| `--continue` | `-c`  |Continue 最后一次会议|
| `--session`  | `-s`  |会话 ID 继续|
| `--fork`     |       |继续时分叉会话（与 `--continue` 或 `--session` 一起使用）|
| `--password` | `-p`  |基本身份验证密码（默认为 `OPENCODE_SERVER_PASSWORD`）|
| `--username` | `-u`  |基本身份验证用户名（默认为 `OPENCODE_SERVER_USERNAME` 或 `opencode`）|

---

### 授权

用于管理提供商的凭据和登录的命令。

```bash
opencode auth [command]
```

---

#### 登录

OpenCode 由 [Models.dev](<https://models.dev>) 上的提供程序列表提供支持，因此您可以使用 `opencode auth login` 为您想要使用的任何提供程序配置 API 密钥。这存储在`~/.local/share/opencode/auth.json`中。

```bash
opencode auth login
```

当 OpenCode 启动时，它会从凭据文件加载提供程序。如果您的环境中定义了任何密钥或项目中的 `.env` 文件。

##### 旗帜

|旗帜|短|描述|
| ---------------------------------------- | ----- | ---------------------------------------------------- |
| `--provider` | `-p`  |提供商 ID 或要登录的名称|
| `--method`   | `-m`  |使用的登录方法标签，跳过方法选择|

---

#### 列表

列出凭证文件中存储的所有经过身份验证的提供程序。

```bash
opencode auth list
```

或者简短的版本。

```bash
opencode auth ls
```

---

#### 注销

通过从凭据文件中清除提供程序，将您从提供程序中注销。

```bash
opencode auth logout
```

---

### github

管理 GitHub agent 以实现存储库自动化。

```bash
opencode github [command]
```

---

#### 安装

在您的存储库中安装 GitHub agent。

```bash
opencode github install
```

这将设置必要的 GitHub 操作工作流程并指导您完成配置过程。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 GitHub Actions 中运行 OpenCode.md>)。

---

#### 跑

运行GitHub agent。这通常用在 GitHub 操作中。

```bash
opencode github run
```

##### 旗帜

|旗帜|描述|
| ------------------------------------- | -------------------------------------- |
| `--event` |GitHub 模拟事件运行 agent|
| `--token` |GitHub 个人访问令牌|

---

### MCP

管理Model Context Protocol服务器。

```bash
opencode mcp [command]
```

---

#### 添加

将 MCP 服务器添加到您的配置中。

```bash
opencode mcp add
```

此命令将指导您添加本地或远程 MCP 服务器。

---

#### 列表

列出所有已配置的MCP服务器及其连接状态。

```bash
opencode mcp list
```

或者使用简短版本。

```bash
opencode mcp ls
```

---

#### 授权

使用启用 OAuth 的 MCP 服务器进行身份验证。

```bash
opencode mcp auth [name]
```

如果您不提供服务器名称，系统将提示您从可用的支持 OAuth 的服务器中进行选择。

您还可以列出支持 OAuth 的服务器及其身份验证状态。

```bash
opencode mcp auth list
```

或者使用简短版本。

```bash
opencode mcp auth ls
```

---

#### 注销

删除 MCP 服务器的 OAuth 凭据。

```bash
opencode mcp logout [name]
```

---

#### 调试

调试 MCP 服务器的 OAuth 连接问题。

```bash
opencode mcp debug <name>
```

---

### 型号

列出已配置提供商的所有可用模型。

```bash
opencode models [provider]
```

此命令以 `provider/model` 格式显示您配置的提供程序中可用的所有模型。

这对于确定在 [ 你的配置 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中使用的确切模型名称很有用。

您可以选择传递提供程序 ID 以按该提供程序过滤模型。

```bash
opencode models anthropic
```

#### 旗帜

|旗帜|描述|
| --------------------------------------- | ------------------------------------------------------------ |
| `--refresh` |从 models.dev 刷新模型缓存|
| `--verbose` |使用更详细的模型输出（包括成本等元数据）|

使用 `--refresh` 标志更新缓存的模型列表。当新模型已添加到提供程序并且您希望在 OpenCode 中查看它们时，这非常有用。

```bash
opencode models --refresh
```

---

### 跑

通过直接传递提示以非交互模式运行opencode。

```bash
opencode run [message..]
```

这对于脚本编写、自动化，或者当您想要快速得到答案而不启动完整的 TUI 时非常有用。例如。

```bash
opencode run Explain the use of context in Go
```

您还可以附加到正在运行的 `opencode serve` 实例，以避免每次运行时 MCP 服务器冷启动时间：

```bash
# Start a headless server in one terminal
opencode serve

# In another terminal, run commands that attach to it
opencode run --attach http://localhost:4096 "Explain async/await in JavaScript"
```

#### 旗帜

|旗帜|短|描述|
| ---------------------------------------- | ----- | -------------------------------------------------------------------------- |
| `--command`  |       |要运行的命令，使用消息作为参数|
| `--continue` | `-c`  |Continue 最后一次会议|
| `--session`  | `-s`  |会话 ID 继续|
| `--fork`     |       |继续时分叉会话（与 `--continue` 或 `--session` 一起使用）|
| `--share`    |       |分享会议|
| `--model`    | `-m`  |以 provider/model 形式使用的型号|
| `--agent`    |       |Agent使用|
| `--file`     | `-f`  |要附加到消息的文件|
| `--format`   |       |格式：默认（格式化）或 json（原始 JSON 事件）|
| `--title`    |       |会话的标题（如果未提供值，则使用截断的提示）|
| `--attach`   |       |连接到正在运行的 opencode 服务器（例如，http://localhost:4096）|
| `--password` | `-p`  |基本身份验证密码（默认为 `OPENCODE_SERVER_PASSWORD`）|
| `--username` | `-u`  |基本身份验证用户名（默认为 `OPENCODE_SERVER_USERNAME` 或 `opencode`）|
| `--dir`      |       |连接时运行的目录或远程服务器上的路径|
| `--port`     |       |本地服务器的端口（默认为随机端口）|
| `--variant`  |       |模型变体（特定于提供商的推理工作）|
| `--thinking` |       |显示思维块|
| `--auto`     |       |自动批准未明确拒绝的权限|

---

### 服务

启动无头 OpenCode 服务器以进行 API 访问。查看 [ 服务器文档 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>) 以获取完整的 HTTP 接口。

```bash
opencode serve
```

这将启动一个 HTTP 服务器，该服务器提供对 opencode 功能的 API 访问，而无需 TUI 接口。设置`OPENCODE_SERVER_PASSWORD`以启用HTTP基本身份验证（用户名默认为`opencode`）。

#### 旗帜

|旗帜|描述|
| ------------------------------------------- | ------------------------------------------ |
| `--port`        |监听端口|
| `--hostname`    |要监听的主机名|
| `--mdns`        |启用 mDNS 发现|
| `--mdns-domain` |自定义mDNS域名|
| `--cors`        |允许 CORS 的其他浏览器源|

---

### 会议

管理 OpenCode 会话。

```bash
opencode session [command]
```

---

#### 列表

列出所有 OpenCode 会话。

```bash
opencode session list
```

##### 旗帜

|旗帜|短|描述|
| ----------------------------------------- | ----- | ------------------------------------ |
| `--max-count` | `-n`  |限制为最近 N 个会话|
| `--format`    |       |输出格式：table或json（表）|

---

#### 删除

删除 OpenCode 会话。

```bash
opencode session delete <sessionID>
```

---

### 统计数据

显示您的 OpenCode 会话的令牌使用情况和成本统计信息。

```bash
opencode stats
```

#### 旗帜

|旗帜|描述|
| --------------------------------------- | --------------------------------------------------------------------------- |
| `--days`    |显示过去 N 天的统计数据（所有时间）|
| `--tools`   |显示的工具数量（全部）|
| `--models`  |显示模型使用情况细分（默认隐藏）。传递一个数字来显示前 N 个|
| `--project` |按项目过滤（所有项目，空字符串：当前项目）|

---

### 出口

将会话数据导出为 JSON。

```bash
opencode export [sessionID]
```

如果您不提供会话 ID，系统将提示您从可用会话中进行选择。

#### 旗帜

|旗帜|描述|
| ---------------------------------------- | ------------------------------------- |
| `--sanitize` |编辑敏感 transcript/file 数据|

---

### 进口

从 JSON 文件或 OpenCode 共享 URL 导入会话数据。

```bash
opencode import <file>
```

您可以从本地文件或 OpenCode 共享 URL 导入。

```bash
opencode import session.json
opencode import https://opncd.ai/s/abc123
```

---

### 网络

使用 Web 界面启动无头 OpenCode 服务器。

```bash
opencode web
```

这将启动 HTTP 服务器并打开 Web 浏览器以通过 Web 界面访问 OpenCode。设置`OPENCODE_SERVER_PASSWORD`以启用HTTP基本身份验证（用户名默认为`opencode`）。

#### 旗帜

|旗帜|描述|
| ------------------------------------------- | ------------------------------------------ |
| `--port`        |监听端口|
| `--hostname`    |要监听的主机名|
| `--mdns`        |启用 mDNS 发现|
| `--mdns-domain` |自定义mDNS域名|
| `--cors`        |允许 CORS 的其他浏览器源|

---

### 丙烯酰胺

启动 ACP（Agent 客户端协议）服务器。

```bash
opencode acp
```

此命令启动一个 ACP 服务器，该服务器使用 nd-JSON 通过 stdin/stdout 进行通信。

#### 旗帜

|旗帜|描述|
| ------------------------------------------- | ------------------------------------------ |
| `--cwd`         |工作目录|
| `--port`        |监听端口|
| `--hostname`    |要监听的主机名|
| `--mdns`        |启用 mDNS 发现|
| `--mdns-domain` |自定义mDNS域名|
| `--cors`        |允许 CORS 的其他浏览器源|

---

### 插件

安装插件并更新您的配置。

```bash
opencode plugin <module>
```

或者使用别名。

```bash
opencode plug <module>
```

#### 旗帜

|旗帜|短|描述|
| -------------------------------------- | ----- | ------------------------------- |
| `--global` | `-g`  |安装在全局配置中|
| `--force`  | `-f`  |替换现有插件版本|

---

### 公关

获取并签出 GitHub PR 分支，然后运行 OpenCode。

```bash
opencode pr <number>
```

---

### 数据库

数据库工具。

```bash
opencode db [query]
```

#### 旗帜

|旗帜|描述|
| -------------------------------------- | ------------------------------ |
| `--format` |输出格式：`json`或`tsv`|

---

#### 路径

打印数据库路径。

```bash
opencode db path
```

---

### 调试

调试和故障排除工具。

```bash
opencode debug [command]
```

---

### 卸载

卸载OpenCode并删除所有相关文件。

```bash
opencode uninstall
```

#### 旗帜

|旗帜|短|描述|
| ------------------------------------------- | ----- | ------------------------------------------- |
| `--keep-config` | `-c`  |保留配置文件|
| `--keep-data`   | `-d`  |保留会话数据和快照|
| `--dry-run`     |       |显示在不删除的情况下将删除的内容|
| `--force`       | `-f`  |跳过确认提示|

---

### 升级

将opencode更新到最新版本或特定版本。

```bash
opencode upgrade [target]
```

升级到最新版本。

```bash
opencode upgrade
```

升级到特定版本。

```bash
opencode upgrade v0.1.48
```

#### 旗帜

|旗帜|短|描述|
| -------------------------------------- | ----- | ----------------------------------------------------------------- |
| `--method` | `-m`  |使用的安装方法；卷曲，npm，pnpm，bun，酿造|

---

## 全球旗帜

opencode CLI 采用以下全局标志。

|旗帜|短|描述|
| ------------------------------------------ | ----- | ------------------------------------ |
| `--help`       | `-h`  |显示帮助|
| `--version`    | `-v`  |打印版本号|
| `--print-logs` |       |将日志打印到 stderr|
| `--log-level`  |       |日志级别（DEBUG、INFO、WARN、ERROR）|
| `--pure`       |       |无需外部插件即可运行|

---

## 环境变量

OpenCode 可以使用环境变量进行配置。

|变量|类型|描述|
| ------------------------------------- | ------- | ------------------------------------------------- |
| `OPENCODE_AUTO_SHARE`                 |布尔值|自动共享会话|
| `OPENCODE_GIT_BASH_PATH`              |字符串|Windows 上 Git Bash 可执行文件的路径|
| `OPENCODE_CONFIG`                     |字符串|配置文件的路径|
| `OPENCODE_TUI_CONFIG`                 |字符串|TUI 配置文件的路径|
| `OPENCODE_CONFIG_DIR`                 |字符串|配置目录的路径|
| `OPENCODE_CONFIG_CONTENT`             |字符串|内联 json 配置内容|
| `OPENCODE_DISABLE_AUTOUPDATE`         |布尔值|禁用自动更新检查|
| `OPENCODE_DISABLE_PRUNE`              |布尔值|禁用旧数据的修剪|
| `OPENCODE_DISABLE_TERMINAL_TITLE`     |布尔值|禁用自动终端标题更新|
| `OPENCODE_PERMISSION`                 |字符串|内联 json 权限配置|
| `OPENCODE_DISABLE_DEFAULT_PLUGINS`    |布尔值|禁用默认插件|
| `OPENCODE_DISABLE_LSP_DOWNLOAD`       |布尔值|禁用自动 LSP 服务器下载|
| `OPENCODE_ENABLE_EXPERIMENTAL_MODELS` |布尔值|启用实验模型|
| `OPENCODE_DISABLE_AUTOCOMPACT`        |布尔值|禁用自动上下文压缩|
| `OPENCODE_DISABLE_CLAUDE_CODE`        |布尔值|禁止读取`.claude`（提示+技能）|
| `OPENCODE_DISABLE_CLAUDE_CODE_PROMPT` |布尔值|禁用读取 `~/.claude/CLAUDE.md`|
| `OPENCODE_DISABLE_CLAUDE_CODE_SKILLS` |布尔值|禁用加载`.claude/skills`|
| `OPENCODE_DISABLE_MODELS_FETCH`       |布尔值|禁用从远程源获取模型|
| `OPENCODE_DISABLE_MOUSE`              |布尔值|在 TUI 中禁用鼠标捕获|
| `OPENCODE_FAKE_VCS`                   |字符串|用于测试目的的假 VCS 提供商|
| `OPENCODE_CLIENT`                     |字符串|客户端标识符（默认为 `cli`）|
| `OPENCODE_ENABLE_EXA`                 |布尔值|启用 Exa 网络搜索工具|
| `OPENCODE_ENABLE_PARALLEL`            |布尔值|启用并行网络搜索工具|
| `OPENCODE_SERVER_PASSWORD`            |字符串|启用 `serve`/`web` 的基本身份验证|
| `OPENCODE_SERVER_USERNAME`            |字符串|覆盖基本身份验证用户名（默认 `opencode`）|
| `OPENCODE_MODELS_URL`                 |字符串|自定义 URL 用于获取模型配置|

---

### 实验性的

这些环境变量启用可能会更改或删除的实验性功能。

|变量|类型|描述|
| ----------------------------------------------- | ------- | --------------------------------------- |
| `OPENCODE_EXPERIMENTAL`                         |布尔值|启用实验性伞标志|
| `OPENCODE_EXPERIMENTAL_ICON_DISCOVERY`          |布尔值|启用图标发现|
| `OPENCODE_EXPERIMENTAL_DISABLE_COPY_ON_SELECT`  |布尔值|在 TUI 中选择时禁用复制|
| `OPENCODE_EXPERIMENTAL_BASH_DEFAULT_TIMEOUT_MS` |数量|bash 命令的默认超时（以毫秒为单位）|
| `OPENCODE_EXPERIMENTAL_OUTPUT_TOKEN_MAX`        |数量|LLM 响应的最大输出令牌|
| `OPENCODE_EXPERIMENTAL_FILEWATCHER`             |布尔值|为整个目录启用文件观察器|
| `OPENCODE_EXPERIMENTAL_OXFMT`                   |布尔值|启用 oxfmt 格式化程序|
| `OPENCODE_EXPERIMENTAL_LSP_TOOL`                |布尔值|启用实验性 LSP 工具|
| `OPENCODE_EXPERIMENTAL_DISABLE_FILEWATCHER`     |布尔值|禁用文件观察器|
| `OPENCODE_EXPERIMENTAL_EXA`                     |布尔值|启用实验性 Exa 功能|
| `OPENCODE_EXPERIMENTAL_LSP_TY`                  |布尔值|为 python 文件启用 TY LSP|
| `OPENCODE_EXPERIMENTAL_PLAN_MODE`               |布尔值|启用计划模式|
| `OPENCODE_EXPERIMENTAL_BACKGROUND_SUBAGENTS`    |布尔值|启用后台子代理任务|
| `OPENCODE_EXPERIMENTAL_EVENT_SYSTEM`            |布尔值|启用实验事件系统|
| `OPENCODE_EXPERIMENTAL_NATIVE_LLM`              |布尔值|启用本机 LLM 请求路径|
| `OPENCODE_EXPERIMENTAL_PARALLEL`                |布尔值|启用并行网络搜索执行|
| `OPENCODE_EXPERIMENTAL_SCOUT`                   |布尔值|启用 Scout 子代理|
| `OPENCODE_EXPERIMENTAL_WORKSPACES`              |布尔值|启用工作区支持|


## 原文对照

<details>
<summary>展开完整原文</summary>

The OpenCode CLI by default starts the [TUI](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端界面的上下文、命令与撤销.md>) when run without any arguments.

```bash
opencode
```

But it also accepts commands as documented on this page. This allows you to interact with OpenCode programmatically.

```bash
opencode run "Explain how closures work in JavaScript"
```

---

### tui

Start the OpenCode terminal user interface.

```bash
opencode [project]
```

#### Flags

| Flag                                        | Short | Description                                                             |
| ------------------------------------------- | ----- | ----------------------------------------------------------------------- |
| `--continue`    | `-c`  | Continue the last session                                               |
| `--session`     | `-s`  | Session ID to continue                                                  |
| `--fork`        |       | Fork the session when continuing (use with `--continue` or `--session`) |
| `--prompt`      |       | Prompt to use                                                           |
| `--model`       | `-m`  | Model to use in the form of provider/model                              |
| `--agent`       |       | Agent to use                                                            |
| `--auto`        |       | Auto-approve permissions that are not explicitly denied                 |
| `--port`        |       | Port to listen on                                                       |
| `--hostname`    |       | Hostname to listen on                                                   |
| `--mdns`        |       | Enable mDNS discovery                                                   |
| `--mdns-domain` |       | Custom mDNS domain name                                                 |
| `--cors`        |       | Additional browser origin(s) to allow CORS                              |

---

## Commands

The OpenCode CLI also has the following commands.

---

### agent

Manage agents for OpenCode.

```bash
opencode agent [command]
```

---

#### create

Create a new agent with custom configuration.

```bash
opencode agent create
```

This command will guide you through creating a new agent with a custom system prompt and permission configuration. Anything you don't allow is denied in the generated agent's frontmatter.

#### Flags

| Flag                                        | Short | Description                                                                                                                                                                                                                |
| ------------------------------------------- | ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--path`        |       | Directory to write the agent file to (defaults to global or `.opencode/agent` based on the prompt)                                                                                                                         |
| `--description` |       | What the agent should do                                                                                                                                                                                                   |
| `--mode`        |       | Agent mode: `all`, `primary`, or `subagent`                                                                                                                                                                                |
| `--permissions` |       | Comma-separated list of permissions to allow (default: all). Available: `bash`, `read`, `edit`, `glob`, `grep`, `webfetch`, `task`, `todowrite`, `websearch`, `lsp`, `skill`. Anything omitted is denied. Alias: `--tools` |
| `--model`       | `-m`  | Model to use, in `provider/model` format                                                                                                                                                                                   |

Passing all of `--path`, `--description`, `--mode`, and `--permissions` runs the command non-interactively.

---

#### list

List all available agents.

```bash
opencode agent list
```

---

### attach

Attach a terminal to an already running OpenCode backend server started via `serve` or `web` commands.

```bash
opencode attach [url]
```

This allows using the TUI with a remote OpenCode backend. For example:

```bash
# Start the backend server for web/mobile access
opencode web --port 4096 --hostname 0.0.0.0

# In another terminal, attach the TUI to the running backend
opencode attach http://10.20.30.40:4096
```

#### Flags

| Flag                                     | Short | Description                                                                |
| ---------------------------------------- | ----- | -------------------------------------------------------------------------- |
| `--dir`      |       | Working directory to start TUI in                                          |
| `--continue` | `-c`  | Continue the last session                                                  |
| `--session`  | `-s`  | Session ID to continue                                                     |
| `--fork`     |       | Fork the session when continuing (use with `--continue` or `--session`)    |
| `--password` | `-p`  | Basic auth password (defaults to `OPENCODE_SERVER_PASSWORD`)               |
| `--username` | `-u`  | Basic auth username (defaults to `OPENCODE_SERVER_USERNAME` or `opencode`) |

---

### auth

Command to manage credentials and login for providers.

```bash
opencode auth [command]
```

---

#### login

OpenCode is powered by the provider list at [Models.dev](<https://models.dev>), so you can use `opencode auth login` to configure API keys for any provider you'd like to use. This is stored in `~/.local/share/opencode/auth.json`.

```bash
opencode auth login
```

When OpenCode starts up it loads the providers from the credentials file. And if there are any keys defined in your environments or a `.env` file in your project.

##### Flags

| Flag                                     | Short | Description                                          |
| ---------------------------------------- | ----- | ---------------------------------------------------- |
| `--provider` | `-p`  | Provider ID or name to log in to                     |
| `--method`   | `-m`  | Login method label to use, skipping method selection |

---

#### list

Lists all the authenticated providers as stored in the credentials file.

```bash
opencode auth list
```

Or the short version.

```bash
opencode auth ls
```

---

#### logout

Logs you out of a provider by clearing it from the credentials file.

```bash
opencode auth logout
```

---

### github

Manage the GitHub agent for repository automation.

```bash
opencode github [command]
```

---

#### install

Install the GitHub agent in your repository.

```bash
opencode github install
```

This sets up the necessary GitHub Actions workflow and guides you through the configuration process. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 GitHub Actions 中运行 OpenCode.md>).

---

#### run

Run the GitHub agent. This is typically used in GitHub Actions.

```bash
opencode github run
```

##### Flags

| Flag                                  | Description                            |
| ------------------------------------- | -------------------------------------- |
| `--event` | GitHub mock event to run the agent for |
| `--token` | GitHub personal access token           |

---

### mcp

Manage Model Context Protocol servers.

```bash
opencode mcp [command]
```

---

#### add

Add an MCP server to your configuration.

```bash
opencode mcp add
```

This command will guide you through adding either a local or remote MCP server.

---

#### list

List all configured MCP servers and their connection status.

```bash
opencode mcp list
```

Or use the short version.

```bash
opencode mcp ls
```

---

#### auth

Authenticate with an OAuth-enabled MCP server.

```bash
opencode mcp auth [name]
```

If you don't provide a server name, you'll be prompted to select from available OAuth-capable servers.

You can also list OAuth-capable servers and their authentication status.

```bash
opencode mcp auth list
```

Or use the short version.

```bash
opencode mcp auth ls
```

---

#### logout

Remove OAuth credentials for an MCP server.

```bash
opencode mcp logout [name]
```

---

#### debug

Debug OAuth connection issues for an MCP server.

```bash
opencode mcp debug <name>
```

---

### models

List all available models from configured providers.

```bash
opencode models [provider]
```

This command displays all models available across your configured providers in the format `provider/model`.

This is useful for figuring out the exact model name to use in [your config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>).

You can optionally pass a provider ID to filter models by that provider.

```bash
opencode models anthropic
```

#### Flags

| Flag                                    | Description                                                  |
| --------------------------------------- | ------------------------------------------------------------ |
| `--refresh` | Refresh the models cache from models.dev                     |
| `--verbose` | Use more verbose model output (includes metadata like costs) |

Use the `--refresh` flag to update the cached model list. This is useful when new models have been added to a provider and you want to see them in OpenCode.

```bash
opencode models --refresh
```

---

### run

Run opencode in non-interactive mode by passing a prompt directly.

```bash
opencode run [message..]
```

This is useful for scripting, automation, or when you want a quick answer without launching the full TUI. For example.

```bash
opencode run Explain the use of context in Go
```

You can also attach to a running `opencode serve` instance to avoid MCP server cold boot times on every run:

```bash
# Start a headless server in one terminal
opencode serve

# In another terminal, run commands that attach to it
opencode run --attach http://localhost:4096 "Explain async/await in JavaScript"
```

#### Flags

| Flag                                     | Short | Description                                                                |
| ---------------------------------------- | ----- | -------------------------------------------------------------------------- |
| `--command`  |       | The command to run, use message for args                                   |
| `--continue` | `-c`  | Continue the last session                                                  |
| `--session`  | `-s`  | Session ID to continue                                                     |
| `--fork`     |       | Fork the session when continuing (use with `--continue` or `--session`)    |
| `--share`    |       | Share the session                                                          |
| `--model`    | `-m`  | Model to use in the form of provider/model                                 |
| `--agent`    |       | Agent to use                                                               |
| `--file`     | `-f`  | File(s) to attach to message                                               |
| `--format`   |       | Format: default (formatted) or json (raw JSON events)                      |
| `--title`    |       | Title for the session (uses truncated prompt if no value provided)         |
| `--attach`   |       | Attach to a running opencode server (e.g., http://localhost:4096)          |
| `--password` | `-p`  | Basic auth password (defaults to `OPENCODE_SERVER_PASSWORD`)               |
| `--username` | `-u`  | Basic auth username (defaults to `OPENCODE_SERVER_USERNAME` or `opencode`) |
| `--dir`      |       | Directory to run in, or path on the remote server when attaching           |
| `--port`     |       | Port for the local server (defaults to random port)                        |
| `--variant`  |       | Model variant (provider-specific reasoning effort)                         |
| `--thinking` |       | Show thinking blocks                                                       |
| `--auto`     |       | Auto-approve permissions that are not explicitly denied                    |

---

### serve

Start a headless OpenCode server for API access. Check out the [server docs](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode HTTP 服务、认证与 API.md>) for the full HTTP interface.

```bash
opencode serve
```

This starts an HTTP server that provides API access to opencode functionality without the TUI interface. Set `OPENCODE_SERVER_PASSWORD` to enable HTTP basic auth (username defaults to `opencode`).

#### Flags

| Flag                                        | Description                                |
| ------------------------------------------- | ------------------------------------------ |
| `--port`        | Port to listen on                          |
| `--hostname`    | Hostname to listen on                      |
| `--mdns`        | Enable mDNS discovery                      |
| `--mdns-domain` | Custom mDNS domain name                    |
| `--cors`        | Additional browser origin(s) to allow CORS |

---

### session

Manage OpenCode sessions.

```bash
opencode session [command]
```

---

#### list

List all OpenCode sessions.

```bash
opencode session list
```

##### Flags

| Flag                                      | Short | Description                          |
| ----------------------------------------- | ----- | ------------------------------------ |
| `--max-count` | `-n`  | Limit to N most recent sessions      |
| `--format`    |       | Output format: table or json (table) |

---

#### delete

Delete an OpenCode session.

```bash
opencode session delete <sessionID>
```

---

### stats

Show token usage and cost statistics for your OpenCode sessions.

```bash
opencode stats
```

#### Flags

| Flag                                    | Description                                                                 |
| --------------------------------------- | --------------------------------------------------------------------------- |
| `--days`    | Show stats for the last N days (all time)                                   |
| `--tools`   | Number of tools to show (all)                                               |
| `--models`  | Show model usage breakdown (hidden by default). Pass a number to show top N |
| `--project` | Filter by project (all projects, empty string: current project)             |

---

### export

Export session data as JSON.

```bash
opencode export [sessionID]
```

If you don't provide a session ID, you'll be prompted to select from available sessions.

#### Flags

| Flag                                     | Description                           |
| ---------------------------------------- | ------------------------------------- |
| `--sanitize` | Redact sensitive transcript/file data |

---

### import

Import session data from a JSON file or OpenCode share URL.

```bash
opencode import <file>
```

You can import from a local file or an OpenCode share URL.

```bash
opencode import session.json
opencode import https://opncd.ai/s/abc123
```

---

### web

Start a headless OpenCode server with a web interface.

```bash
opencode web
```

This starts an HTTP server and opens a web browser to access OpenCode through a web interface. Set `OPENCODE_SERVER_PASSWORD` to enable HTTP basic auth (username defaults to `opencode`).

#### Flags

| Flag                                        | Description                                |
| ------------------------------------------- | ------------------------------------------ |
| `--port`        | Port to listen on                          |
| `--hostname`    | Hostname to listen on                      |
| `--mdns`        | Enable mDNS discovery                      |
| `--mdns-domain` | Custom mDNS domain name                    |
| `--cors`        | Additional browser origin(s) to allow CORS |

---

### acp

Start an ACP (Agent Client Protocol) server.

```bash
opencode acp
```

This command starts an ACP server that communicates via stdin/stdout using nd-JSON.

#### Flags

| Flag                                        | Description                                |
| ------------------------------------------- | ------------------------------------------ |
| `--cwd`         | Working directory                          |
| `--port`        | Port to listen on                          |
| `--hostname`    | Hostname to listen on                      |
| `--mdns`        | Enable mDNS discovery                      |
| `--mdns-domain` | Custom mDNS domain name                    |
| `--cors`        | Additional browser origin(s) to allow CORS |

---

### plugin

Install a plugin and update your config.

```bash
opencode plugin <module>
```

Or use the alias.

```bash
opencode plug <module>
```

#### Flags

| Flag                                   | Short | Description                     |
| -------------------------------------- | ----- | ------------------------------- |
| `--global` | `-g`  | Install in global config        |
| `--force`  | `-f`  | Replace existing plugin version |

---

### pr

Fetch and checkout a GitHub PR branch, then run OpenCode.

```bash
opencode pr <number>
```

---

### db

Database tools.

```bash
opencode db [query]
```

#### Flags

| Flag                                   | Description                    |
| -------------------------------------- | ------------------------------ |
| `--format` | Output format: `json` or `tsv` |

---

#### path

Print the database path.

```bash
opencode db path
```

---

### debug

Debugging and troubleshooting tools.

```bash
opencode debug [command]
```

---

### uninstall

Uninstall OpenCode and remove all related files.

```bash
opencode uninstall
```

#### Flags

| Flag                                        | Short | Description                                 |
| ------------------------------------------- | ----- | ------------------------------------------- |
| `--keep-config` | `-c`  | Keep configuration files                    |
| `--keep-data`   | `-d`  | Keep session data and snapshots             |
| `--dry-run`     |       | Show what would be removed without removing |
| `--force`       | `-f`  | Skip confirmation prompts                   |

---

### upgrade

Updates opencode to the latest version or a specific version.

```bash
opencode upgrade [target]
```

To upgrade to the latest version.

```bash
opencode upgrade
```

To upgrade to a specific version.

```bash
opencode upgrade v0.1.48
```

#### Flags

| Flag                                   | Short | Description                                                       |
| -------------------------------------- | ----- | ----------------------------------------------------------------- |
| `--method` | `-m`  | The installation method that was used; curl, npm, pnpm, bun, brew |

---

## Global Flags

The opencode CLI takes the following global flags.

| Flag                                       | Short | Description                          |
| ------------------------------------------ | ----- | ------------------------------------ |
| `--help`       | `-h`  | Display help                         |
| `--version`    | `-v`  | Print version number                 |
| `--print-logs` |       | Print logs to stderr                 |
| `--log-level`  |       | Log level (DEBUG, INFO, WARN, ERROR) |
| `--pure`       |       | Run without external plugins         |

---

## Environment variables

OpenCode can be configured using environment variables.

| Variable                              | Type    | Description                                       |
| ------------------------------------- | ------- | ------------------------------------------------- |
| `OPENCODE_AUTO_SHARE`                 | boolean | Automatically share sessions                      |
| `OPENCODE_GIT_BASH_PATH`              | string  | Path to Git Bash executable on Windows            |
| `OPENCODE_CONFIG`                     | string  | Path to config file                               |
| `OPENCODE_TUI_CONFIG`                 | string  | Path to TUI config file                           |
| `OPENCODE_CONFIG_DIR`                 | string  | Path to config directory                          |
| `OPENCODE_CONFIG_CONTENT`             | string  | Inline json config content                        |
| `OPENCODE_DISABLE_AUTOUPDATE`         | boolean | Disable automatic update checks                   |
| `OPENCODE_DISABLE_PRUNE`              | boolean | Disable pruning of old data                       |
| `OPENCODE_DISABLE_TERMINAL_TITLE`     | boolean | Disable automatic terminal title updates          |
| `OPENCODE_PERMISSION`                 | string  | Inlined json permissions config                   |
| `OPENCODE_DISABLE_DEFAULT_PLUGINS`    | boolean | Disable default plugins                           |
| `OPENCODE_DISABLE_LSP_DOWNLOAD`       | boolean | Disable automatic LSP server downloads            |
| `OPENCODE_ENABLE_EXPERIMENTAL_MODELS` | boolean | Enable experimental models                        |
| `OPENCODE_DISABLE_AUTOCOMPACT`        | boolean | Disable automatic context compaction              |
| `OPENCODE_DISABLE_CLAUDE_CODE`        | boolean | Disable reading from `.claude` (prompt + skills)  |
| `OPENCODE_DISABLE_CLAUDE_CODE_PROMPT` | boolean | Disable reading `~/.claude/CLAUDE.md`             |
| `OPENCODE_DISABLE_CLAUDE_CODE_SKILLS` | boolean | Disable loading `.claude/skills`                  |
| `OPENCODE_DISABLE_MODELS_FETCH`       | boolean | Disable fetching models from remote sources       |
| `OPENCODE_DISABLE_MOUSE`              | boolean | Disable mouse capture in the TUI                  |
| `OPENCODE_FAKE_VCS`                   | string  | Fake VCS provider for testing purposes            |
| `OPENCODE_CLIENT`                     | string  | Client identifier (defaults to `cli`)             |
| `OPENCODE_ENABLE_EXA`                 | boolean | Enable Exa web search tools                       |
| `OPENCODE_ENABLE_PARALLEL`            | boolean | Enable Parallel web search tools                  |
| `OPENCODE_SERVER_PASSWORD`            | string  | Enable basic auth for `serve`/`web`               |
| `OPENCODE_SERVER_USERNAME`            | string  | Override basic auth username (default `opencode`) |
| `OPENCODE_MODELS_URL`                 | string  | Custom URL for fetching models configuration      |

---

### Experimental

These environment variables enable experimental features that may change or be removed.

| Variable                                        | Type    | Description                             |
| ----------------------------------------------- | ------- | --------------------------------------- |
| `OPENCODE_EXPERIMENTAL`                         | boolean | Enable the experimental umbrella flag   |
| `OPENCODE_EXPERIMENTAL_ICON_DISCOVERY`          | boolean | Enable icon discovery                   |
| `OPENCODE_EXPERIMENTAL_DISABLE_COPY_ON_SELECT`  | boolean | Disable copy on select in TUI           |
| `OPENCODE_EXPERIMENTAL_BASH_DEFAULT_TIMEOUT_MS` | number  | Default timeout for bash commands in ms |
| `OPENCODE_EXPERIMENTAL_OUTPUT_TOKEN_MAX`        | number  | Max output tokens for LLM responses     |
| `OPENCODE_EXPERIMENTAL_FILEWATCHER`             | boolean | Enable file watcher for entire dir      |
| `OPENCODE_EXPERIMENTAL_OXFMT`                   | boolean | Enable oxfmt formatter                  |
| `OPENCODE_EXPERIMENTAL_LSP_TOOL`                | boolean | Enable experimental LSP tool            |
| `OPENCODE_EXPERIMENTAL_DISABLE_FILEWATCHER`     | boolean | Disable file watcher                    |
| `OPENCODE_EXPERIMENTAL_EXA`                     | boolean | Enable experimental Exa features        |
| `OPENCODE_EXPERIMENTAL_LSP_TY`                  | boolean | Enable TY LSP for python files          |
| `OPENCODE_EXPERIMENTAL_PLAN_MODE`               | boolean | Enable plan mode                        |
| `OPENCODE_EXPERIMENTAL_BACKGROUND_SUBAGENTS`    | boolean | Enable background subagent tasks        |
| `OPENCODE_EXPERIMENTAL_EVENT_SYSTEM`            | boolean | Enable experimental event system        |
| `OPENCODE_EXPERIMENTAL_NATIVE_LLM`              | boolean | Enable native LLM request path          |
| `OPENCODE_EXPERIMENTAL_PARALLEL`                | boolean | Enable parallel web search execution    |
| `OPENCODE_EXPERIMENTAL_SCOUT`                   | boolean | Enable Scout subagent                   |
| `OPENCODE_EXPERIMENTAL_WORKSPACES`              | boolean | Enable workspace support                |


</details>
