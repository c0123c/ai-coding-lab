# Codex CLI 命令与参数参考

逐项列出 CLI 子命令、全局参数、配置覆盖和成熟度，包含交互运行、服务、远程控制和调试等入口。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/developer-commands>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/developer-commands.md?surface=cli>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex CLI 命令与参数参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开发者命令

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

## 如何阅读本参考资料

此页面列出了每个记录的 Codex CLI 命令和标志。使用交互式表格按关键字或描述进行搜索。每个部分都显示选项的成熟度并标记已弃用的选项和有风险的组合。

的 CLI 继承大部分默认值 `~/.codex/config.toml`。任意
  `-c key=value` 覆盖您在命令行中传递的内容
  该调用的优先级。参见 [配置
  基础知识](<https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence>) 了解更多
  信息。

## 全球旗帜

<ConfigTable client:load options={globalFlagOptions} />

这些选项适用于基本 `codex` 命令。大多数传播到命令；
请参阅上面的注释或相关命令帮助以了解例外情况。对于传播的
标志，请按照相关命令帮助进行操作。例如，`codex exec --oss ...`
 将 `--oss` 应用到 `exec`。

## 命令概述

成熟度列使用功能成熟度标签，例如实验性、测试版、
 稳定和已弃用。请参阅 [ 功能成熟度 ](<https://learn.chatgpt.com/docs/feature-maturity>) 了解 
 如何解释这些标签。

<ConfigTable
  client:load
  options={commandOverview}
  secondColumnTitle="Maturity"
  secondColumnVariant="maturity"
/>

## 命令详情

### `codex`（互动）

运行不带子命令的 `codex` 将启动交互式终端 UI (TUI)。 agent 接受上面的全局标志以及图像附件。 Web搜索默认为缓存模式；使用`--search`切换到实时浏览。对于低摩擦局部工作，请使用 `--sandbox workspace-write --ask-for-approval on-request`。

使用`--remote ws://host:port`或`--remote wss://host:port`将TUI连接到以`codex app-server --listen ws://IP:PORT`启动的应用服务器。对于本地 Unix 套接字，请使用 `--remote unix://` 作为默认套接字，或使用 `--remote unix://PATH` 作为显式路径。当服务器需要承载令牌进行 WebSocket 身份验证时，添加 `--remote-auth-token-env <ENV_VAR>`。

### `codex app-server`

在本地启动 Codex 应用程序服务器。这主要用于开发和调试，可能会更改，恕不另行通知。

<ConfigTable client:load options={appServerOptions} />

`codex app-server --listen stdio://` 保留默认的 JSONL-over-stdio 行为，并且 `codex app-server --stdio` 是该传输的别名。 `--listen ws://IP:PORT` 为应用服务器客户端启用 WebSocket 传输。服务器接受 `ws://` 监听 URL；当客户端与 `wss://` 连接时，使用 TLS 终止或安全代理。使用 `--listen unix://` 在 Codex 的默认 Unix 套接字上接受 WebSocket 握手，或使用 `--listen unix:///absolute/path.sock` 选择套接字路径。如果您为客户端绑定生成架构，请添加 `--experimental` 以包含门控字段和方法。

添加 `--code-mode-host wss://code-mode.example.com/host` 将应用程序服务器连接到 
 远程代码模式主机，而不是启动本地主机。此出站 
 连接与 `--listen` 分开，并由 
 应用程序服务器进程中的每个线程共享。仅将 `ws://` 用于本地主机或 SSH- 转发的主机。

### `codex remote-control`

运行【`codex remote-control`】，在前台启动远程控制。使用
`codex remote-control start`启动本地应用程序服务器守护进程并启用远程
控制，并使用`codex remote-control stop`停止它。托管
远程控制客户端和SSH远程工作流程使用这些命令；当您构建本地 
 协议客户端时，它们不是 
 的 `codex app-server --listen` 替代品。

守护进程运行后，使用`codex remote-control pair`创建并
打印短暂的手动配对代码。将 `--json` 添加到任何远程控制 
 命令中，以获得机器可读的输出。对于 `pair`，JSON 响应包括 
`pairingCode`、`manualPairingCode`、`environmentId` 和 `expiresAt`。

### `codex app`

从 macOS 或 Windows 上的终端启动 ChatGPT 桌面应用程序。在macOS、
Codex上可以打开特定的工作空间路径；在 Windows 上，Codex 打印 
open 的路径。

<ConfigTable client:load options={appOptions} />

`codex app` 打开已安装的 ChatGPT 桌面应用程序，或者在 
 应用程序丢失时启动安装程序。在macOS上，Codex打开提供的工作空间路径；在
Windows上，它会打印安装后打开的路径。

### `codex debug app-server send-message-v2`

使用内置应用程序服务器测试客户端通过应用程序服务器的 V2 thread/turn 流发送一条消息。

<ConfigTable client:load options={debugAppServerSendMessageV2Options} />

此调试流程使用 `experimentalApi: true` 进行初始化，启动线程，发送轮次，并流式传输服务器通知。使用它在本地重现和检查应用程序服务器协议行为。

### `codex debug models`

打印原始模型目录Codex见JSON。

<ConfigTable client:load options={debugModelsOptions} />

当您只想检查与当前二进制文件捆绑的目录而不从远程模型端点刷新时，请使用 `--bundled`。

### `codex debug prompt-input`

将精确的模型可见提示输入列表渲染为 JSON。在
调试指令发现、会话上下文或提示构造时使用此选项。

<ConfigTable client:load options={debugPromptInputOptions} />

### `codex apply`

将 Codex 云聊天中的最新差异应用到本地存储库。您必须进行身份验证并有权访问聊天。

<ConfigTable client:load options={applyOptions} />

如果 `git apply` 失败（例如，由于冲突），Codex 打印修补的文件并以非零值退出。

### `codex review`

以非交互方式运行代码审查。仅选择一个审核目标，或通过
自定义审核说明作为提示。

<ConfigTable client:load options={reviewOptions} />

`--uncommitted`、`--base`、`--commit` 和自定义 `PROMPT` 与另一个 
 冲突。 `--title` 只能与 `--commit` 一起使用。

### `codex archive` 和 `codex unarchive`

按会话 ID 或会话名称存档或恢复已保存的交互式会话。
当您想要清理会话选取器而不删除 
 记录时，请使用这些命令。会话 ID 优先于会话名称。

```bash
codex archive <SESSION>
codex unarchive <SESSION>
```

<ConfigTable client:load options={archiveOptions} />

### `codex delete`

按会话 ID 或会话名称永久删除已保存的交互式会话。
仅当您想要删除脚本而不是将其从 
 活动会话列表中隐藏时才使用此选项。

```bash
codex delete <SESSION>
codex delete <SESSION_UUID> --force
```

<ConfigTable client:load options={deleteOptions} />

仅将 `--force` 与会话 UUID 结合使用。命名会话仍需要 
 确认，因此 Codex 不会在没有提示的情况下删除重复或不明确的名称。

### `codex cloud`

从终端与Codex云聊天互动。默认命令打开一个交互式选择器； `codex cloud exec`直接提交任务，`codex cloud list`返回最近的聊天记录以供脚本编写或快速检查。

<ConfigTable client:load options={cloudExecOptions} />

身份验证遵循与主 CLI 相同的凭据。如果任务提交失败，Codex 将以非零值退出。

#### `codex cloud list`

列出最近的云聊天，并提供可选的过滤和分页功能。

<ConfigTable client:load options={cloudListOptions} />

纯文本输出打印任务 URL，后跟状态详细信息。使用 `--json` 进行自动化。 JSON 有效负载包含一个 `tasks` 数组以及一个可选的 `cursor` 值。每个任务包括`id`、`url`、`title`、`status`、`updated_at`、`environment_id`、`environment_label`、`summary`、 `is_review`和`attempt_total`。

### `codex completion`

生成 shell 完成脚本并将输出重定向到适当的位置，例如 `codex completion zsh > "${fpath[1]}/_codex"`。

<ConfigTable client:load options={completionOptions} />

### `codex doctor`

在提交支持问题或
之前生成本地诊断报告，同时调查损坏的Codex安装。该报告检查安装、
配置、身份验证、运行时、Git、终端、应用程序服务器和线程
库存运行状况。

<ConfigTable client:load options={doctorOptions} />

### `codex features`

管理存储在`$CODEX_HOME/config.toml`中的功能标志。 `enable` 和 
`disable` 命令保留更改，以便它们应用于将来的会话。 
`features` 子命令不接受 `--profile`。

<ConfigTable client:load options={featuresOptions} />

### `codex exec`

使用 `codex exec`（或缩写形式 `codex e`）进行脚本化或 CI- 样式的运行，无需人工交互即可完成。

<ConfigTable client:load options={execOptions} />

Codex 默认写入格式化输出。添加 `--json` 以接收换行符分隔的 JSON 事件（每个状态更改一个）。可选的 `resume` 子命令允许您继续非交互式任务。使用 `--last` 从当前工作目录中选择最近的会话，或添加 `--all` 以搜索所有会话：

<ConfigTable client:load options={execResumeOptions} />

### `codex execpolicy`

保存前检查 `execpolicy` 规则文件。 `codex execpolicy check` 接受一个或多个 `--rules` 标志（例如，`~/.codex/rules` 下的文件）并发出 JSON，显示最严格的决策和任何匹配规则。添加 `--pretty` 以格式化输出。 `execpolicy` 命令当前处于预览状态。

<ConfigTable client:load options={execpolicyOptions} />

### `codex login`

使用 ChatGPT 帐户、API 密钥或访问令牌对 CLI 进行身份验证。如果没有标志，Codex 将打开 ChatGPT OAuth 流程的浏览器。

<ConfigTable client:load options={loginOptions} />

当凭据存在时，`codex login status` 会以 `0` 退出，这在自动化脚本中很有帮助。

### `codex logout`

删除 API 密钥和 ChatGPT 身份验证的已保存凭据。该命令没有标志。

### `codex mcp`

管理存储在`~/.codex/config.toml`中的Model Context Protocol服务器条目。

<ConfigTable client:load options={mcpCommands} />

`add` 子命令支持 stdio 和流式 HTTP 传输：

<ConfigTable client:load options={mcpAddOptions} />

OAuth 操作（`login`、`logout`）仅适用于可流式传输的 HTTP 服务器（并且仅当服务器支持 OAuth 时）。

### `codex plugin`

从配置的市场安装、列出和删除插件。

<ConfigTable client:load options={pluginCommands} />

`codex plugin add --json` 打印 `pluginId`、`name`、`marketplaceName`、
`version`、`installedPath` 和 `authPolicy`。 `codex plugin list --json` 打印 
`installed` 和 `available` 数组。条目包括 `pluginId`、`name`、
`marketplaceName`、`version`、`installed`、`enabled`、`source`、 `installPolicy`、
`authPolicy` 以及（如果可用）`marketplaceSource` 以及已配置的 
 市场源类型和值。 `codex plugin remove --json` 打印 
`pluginId`、`name` 和 `marketplaceName`。

### `codex plugin marketplace`

管理 Codex 可以浏览和安装的插件市场源。

<ConfigTable client:load options={marketplaceCommands} />

`codex plugin marketplace add` 接受 GitHub 简写，例如 `owner/repo` 或 
`owner/repo@ref`、HTTP 或 HTTPS Git URL、SSH Git URL 和本地市场 
root目录。使用 `--ref` 固定 Git 引用，然后重复 `--sparse PATH` 到 
 对 Git 支持的市场存储库使用稀疏签出。

`codex plugin marketplace list` 打印范围内的市场名称和根，
 包括隐式发现的默认市场和配置的市场 
 快照。

将 `--json` 添加到市场添加、列出、升级或删除命令，以实现 
 自动化友好的输出。商城新增【JSON】包括【`marketplaceName`】、【
`installedRoot`】、【`alreadyAdded`】；列表 JSON 包含一个 `marketplaces` 数组 
，其中包含 `name`、`root` 和可选的 `marketplaceSource`；升级【JSON】包括【
`selectedMarketplaces`】、【`upgradedRoots`】、【`errors`】；删除JSON包括
`marketplaceName`和`installedRoot`。

### `codex mcp-server`

`codex mcp-server` 命令和独立的 `codex-mcp-server` 二进制文件已 
 被删除。请改用 [Codex 应用服务器 ](<https://learn.chatgpt.com/docs/app-server>)。

### `codex resume`

Continue ID 的交互式会话或恢复最近的聊天。 `codex resume` 将 `--last` 范围限定为当前工作目录，除非您传递 `--all`。它接受与 `codex` 相同的全局标志，包括模型和沙箱覆盖。

如果当前工作目录与会话的保存目录不同，
Codex 会询问使用哪个目录。将 
[`tui.resume_cwd`](<https://learn.chatgpt.com/docs/config-file/config-reference>) 设置为 `"current"` 或 
`"session"` 以在没有提示的情况下重复使用该选择。显式 `--cd` (`-C`)
 覆盖优先于 `tui.resume_cwd`。

<ConfigTable client:load options={resumeOptions} />

### `codex fork`

将先前的交互式会话分叉为新的聊天。默认情况下，`codex fork` 打开会话选择器；添加 `--last` 来分叉您最近的会话。

当当前和保存的会话目录不同时，`codex fork` 使用 
 与 `codex resume` 相同的工作目录提示和 `tui.resume_cwd` 设置。

<ConfigTable client:load options={forkOptions} />

### `codex sandbox`

使用沙箱帮助程序在 Codex 内部使用的相同策略下运行命令。

#### macOS安全带

<ConfigTable client:load options={sandboxMacOptions} />

#### Linux 内锁

<ConfigTable client:load options={sandboxLinuxOptions} />

#### 窗户

<ConfigTable client:load options={sandboxWindowsOptions} />

### `codex update`

当安装的版本支持自我更新时，检查并应用 Codex CLI 更新。调试版本会打印一条消息，告诉您安装发布版本。

## 标志组合和安全提示

- 使用 `--sandbox workspace-write` 进行可以留在工作空间内的无人值守本地工作，并避免使用 `--dangerously-bypass-approvals-and-sandbox`，除非您位于专用沙箱 VM 内。
- 当您需要授予 Codex 对更多目录的写访问权限时，优先选择 `--add-dir` 而不是强制 `--sandbox danger-full-access`。
- 将 `--json` 与 CI 中的 `--output-last-message` 配对，以捕获机器可读的进度和最终的自然语言摘要。

## 交互式快捷键

- 输入 `@` 在工作区中搜索文件并将其路径添加到提示符中。
- 按<kbd>向上</kbd>或<kbd>向下</kbd>恢复草稿历史记录。
- 按 <kbd>Ctrl</kbd>+<kbd>R</kbd> 搜索提示历史记录，然后按 <kbd>Enter</kbd> 使用匹配或按 <kbd>Escape</kbd>取消。
- 按 <kbd>Ctrl</kbd>+<kbd>O</kbd> 或运行 `/copy` 复制最新完成的 Codex 输出。
- 在行中添加 `!` 前缀可在当前批准和沙箱设置下运行本地 shell 命令。
- 当 Codex 正在工作时按 <kbd>Tab </kbd> 将后续提示、斜杠命令或 shell 命令排队到下一回合。
- 当Codex正在向当前回合注入新指令时，按<kbd>输入</kbd>。
- 使用空编辑器按 <kbd>Escape</kbd> 两次以编辑上一条用户消息并从该点分叉聊天。
- 按 <kbd>Ctrl</kbd>+<kbd>C</kbd> 或运行 `/exit` 关闭会话。

## 相关资源

- [Codex CLI 概述](<https://learn.chatgpt.com/docs/codex/cli>)：安装、升级和快速提示。
- [配置基础知识](<https://learn.chatgpt.com/docs/config-file/config-basic>)：保留模型和提供程序等默认值。
- [高级配置](<https://learn.chatgpt.com/docs/config-file/config-advanced>)：配置文件、提供程序、沙箱调整和集成。
- [AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>)：Codex agent 功能和最佳实践的概念概述。

斜杠命令让您可以通过键盘快速控制 Codex。在
编辑器中输入`/`以打开斜杠弹出窗口，选择一个命令，然后Codex将执行
操作，例如切换模型，调整权限或总结长
聊天，而无需离开终端。

本指南向您展示如何：

- 为任务找到正确的内置斜线命令
- 使用 `/model`、`/fast`、
`/personality`、`/permissions`、`/approve`、`/raw`、`/agent` 和 `/status`

## 内置斜杠命令

Codex 附带以下命令。打开斜杠弹出窗口并开始输入 
 命令名称来过滤列表。

当聊天已经在运行时，您可以输入斜杠命令并按`Tab`将其
排队到下一回合。 Codex 在运行时解析排队的斜线命令，因此 
 命令菜单和错误会在当前回合结束后出现。在将命令排队之前，斜线
补全仍然有效。

|命令|目的|何时使用它|
| ------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [`/permissions`](<#使用-permissions-更新权限>)                                      |设置 Codex 无需先询问即可执行的操作。|在会话中放松或收紧批准要求，例如在自动和只读之间切换。|
| [`/ide`](<#将-ide-上下文包含在-ide-中>)                                                     |包括打开的文件、当前选择和其他 IDE 上下文。|将编辑器上下文拉入下一个提示，而无需重新解释 IDE 中打开的内容。|
| [`/keymap`](<#使用-keymap-重新映射-tui-快捷方式>)                                               |重新映射 TUI 键盘快捷键。|检查并保留 `config.toml` 中的自定义快捷方式绑定。|
| [`/vim`](<#使用-vim-切换-vim-模式>)                                                         |切换作曲家的 Vim 模式。|在 Vim normal/insert 行为和默认编辑器编辑模式之间切换。|
| [`/setup-default-sandbox`](<#使用-setup-default-sandbox-设置提升的-windows-沙箱>) |设置提升的 agent 沙箱（仅限 Windows）。|在 Codex 提供提升的设置后替换降级的 Windows 沙箱。|
| [`/sandbox-add-read-dir`](<#使用-sandbox-add-read-dir-授予沙箱读取访问权限>)             |授予沙盒对额外目录的读取权限（仅限 Windows）。|取消阻止需要读取当前可读根目录之外的绝对目录路径的命令。|
| [`/agent`, `/subagents`](<#将-agent-线程切换为-agent>)                                  |切换活动的 agent 线程。|检查或继续在生成的子代理线程中工作。|
| [`/apps`](<#使用-apps-浏览应用程序>)                                                           |浏览应用程序（连接器）并将它们插入到您的提示中。|在要求 Codex 使用应用程序之前，将其附加为 `$app-slug`。|
| [`/plugins`](<#使用-plugins-浏览插件>)                                                  |浏览已安装和可发现的插件。|检查插件工具、安装建议的插件或管理插件可用性。|
| [`/hooks`](<#使用-hooks-查看和管理生命周期挂钩>)                                     |查看和管理生命周期挂钩。|检查已配置的挂钩、信任新的或更改的挂钩，或者在运行之前禁用非托管挂钩。|
| [`/clear`](<#清除终端并与-clear-开始新的聊天>)                             |清除终端并开始新的聊天。|当您想要重新开始时，请重置可见的 UI 和聊天上下文。|
| [`/rename`](<#将当前聊天重命名为-rename>)                                           |重命名当前聊天。|为保存的会话指定一个可识别的名称，而无需保留 TUI。|
| [`/archive`](<#使用-archive-存档当前会话>)                                     |存档当前会话并退出 Codex。|从活动会话列表中删除当前会话，而不删除其记录。|
| [`/delete`](<#使用-delete-删除当前会话>)                                        |永久删除当前会话并退出Codex。|当存档不够时，删除转录本和后代会话。|
| [`/compact`](<#使用-compact-保持成绩单精简>)                                           |将可见聊天总结为免费代币。|在长时间运行后使用，以便 Codex 保留关键点，而不会破坏上下文窗口。|
| [`/copy`](<#复制最新回复-copy>)                                              |复制最新完成的 Codex 输出。|获取最新完成的响应或计划文本，无需手动选择。您也可以按`Ctrl+O`。|
| [`/diff`](<#使用-diff-查看更改>)                                                        |显示 Git 差异，包括 Git 尚未跟踪的文件。|在提交或运行测试之前查看 Codex 的编辑。|
| [`/exit`](<#使用-quit-或-exit-退出-cli>)                                                  |退出CLI（与`/quit`相同）。|替代拼写；这两个命令都退出会话。|
| [`/experimental`](<#使用-experimental-切换实验功能>)                          |切换实验性功能。|启用网络代理或防止运行时睡眠等选项。|
| [`/approve`](<#使用-approve-批准自动审核拒绝>)                                   |批准最近一次自动审核拒绝的重试。|重试自动审阅器拒绝的命令或操作。|
| [`/memories`](<#使用-memories-配置存储器>)                                            |配置内存使用和生成。|无需离开TUI即可打开或关闭内存注入或内存生成。|
| [`/skills`](<#使用skills技能>)                                                        |浏览和使用技能。|通过选择相关的本地技能来改善特定任务的行为。|
| [`/import`](<#使用-import-导入-claude-code-或-cursor-设置>)                                |导入 Claude Code 或 Cursor 设置、项目和聊天。|将支持的外部 agent 工件迁移到 Codex 配置和本地文件中。|
| [`/feedback`](<#通过-feedback-发送反馈>)                                                 |将日志发送给 Codex 维护者。|报告问题或与支持人员分享诊断结果。|
| [`/init`](<#使用-init-生成-agentsmd>)                                                     |在当前目录生成一个`AGENTS.md`脚手架。|捕获您正在使用的存储库或子目录的持久指令。|
| [`/logout`](<#使用-logout-注销>)                                                          |退出 Codex。|使用共享计算机时清除本地凭据。|
| [`/mcp`](<#列出带有-mcp-的-mcp-工具>)                                                          |列出已配置的 Model Context Protocol (MCP) 工具。|检查会话期间可以调用哪些外部工具Codex；添加 `verbose` 以获取服务器详细信息。|
| [`/mention`](<#使用-mention-突出显示文件>)                                                 |将文件附加到聊天中。|将 Codex 指向您希望接下来检查的特定文件或文件夹。|
| [`/model`](<#使用-model-设置活动模型>)                                                |选择活动模型（以及推理工作，如果可用）。|如果显示，请使用 `gpt-6-luna` 或 `gpt-6.1-sol`。|
| [`/fast`](<#使用-fast-切换快速模式>)                                                      |当模型目录公开快速服务层时，切换快速服务层。|打开或关闭当前模型的快速层并保留选择。|
| [`/plan`](<#使用-plan-切换到计划模式>)                                                   |切换到计划模式并可选择发送提示。|在实施工作开始之前，请Codex提出执行计划。|
| [`/goal`](<#使用-goal-设置或查看任务目标>)                                               |设置、编辑、暂停、恢复、查看或清除任务目标。|为 Codex 指定一个持久目标，以便在较大的任务运行时进行跟踪。|
| [`/personality`](<#使用-personality-设置沟通方式>)                               |选择响应的沟通方式。|使 Codex 更简洁、更具解释性或更具协作性，而无需更改您的说明。|
| [`/ps`](<#使用ps检查后台终端>)                                                |显示后台终端及其最近的输出。|检查长时间运行的命令而不留下主要记录。|
| [`/stop`](<#使用-stop-停止后台终端>)                                             |停止所有后台终端。|取消当前会话启动的后台终端工作。|
| [`/fork`](<#与-fork-分叉当前聊天>)                                                 |将当前聊天分叉为新聊天。|分支活动会话以探索新方法，而不会丢失当前的记录。|
| [`/app`](<#continue-在桌面应用程序中带有-app>)                                             |Continue ChatGPT 桌面应用程序中的当前会话。|从 TUI 移至 macOS 或 Windows 上的桌面应用程序。|
| [`/side`, `/btw`](<#与-side-开始侧聊>)                                             |开始短暂的侧聊。|在不中断主要聊天记录的情况下询问有针对性的后续行动。|
| [`/raw`](<#使用-raw-切换原始回滚>)                                                   |切换原始回滚模式。|在查看长输出时，减少终端选择和复制的格式。|
| [`/resume`](<#恢复与-resume-的已保存聊天>)                                               |从会话列表中恢复保存的聊天。|Continue 从上一个 CLI 会话开始工作，无需重新开始。|
| [`/new`](<#与-new-开始新的聊天>)                                                        |在同一 CLI 会话中开始新的聊天。|当您想在同一存储库中获得新的提示时，请重置聊天上下文而不离开 CLI。|
| [`/quit`](<#使用-quit-或-exit-退出-cli>)                                                  |退出CLI。|立即离开会议。|
| [`/review`](<#请求使用-review-进行工作树审查>)                                     |请 Codex 检查您的工作树。|在 Codex 完成工作后或当您想要第二次关注本地更改时运行。|
| [`/status`](<#使用-status-检查会话>)                                               |显示会话配置和令牌使用情况。|确认活动模型、审批策略、可写根和剩余上下文容量。|
| [`/usage`](<#使用-usage-查看帐户使用情况>)                                                  |查看帐户令牌使用情况或使用速率限制重置。|从 TUI 内部检查每日、每周或累积 ChatGPT 代币活动。|
| [`/debug-config`](<#使用-debug-config-检查配置层>)                                 |打印配置层和需求诊断。|调试优先级和策略要求，包括实验网络限制。|
| [`/statusline`](<#使用-statusline-配置页脚项目>)                                    |以交互方式配置 TUI 状态行字段。|选择并重新排序页脚项目 (model/context/limits/git/tokens/session) 并保留在 config.toml 中。|
| [`/title`](<#配置终端标题项title>)                                      |以交互方式配置终端窗口或选项卡标题字段。|选择并重新排序标题项，例如项目、状态、线程、分支、模型和任务进度。|
| [`/theme`](<#使用-theme-选择语法主题>)                                               |选择语法突出显示主题。|预览并保留终端语法突出显示主题。|
| [`/pets`, `/pet`](<#选择带有pets的末期宠物>)                                         |选择或隐藏末期宠物。|使用内置或自定义环境宠物来个性化 TUI。|

`/quit`和`/exit`均退出CLI。仅在您保存或
提交任何重要工作后才使用它们。

使用 `/permissions` 调整什么 Codex 无需先询问就可以做。使用
`/approve` 仅当您需要重试最近的操作时，自动审核
否认。

## 使用斜杠命令控制您的会话

以下工作流程可让您的会话保持在正轨上，而无需重新启动 Codex。

### 使用 `/model` 设置活动模型

选择器仅显示您的帐户和工作区可用的模型。如果未列出 GPT-6.1
Sol，请选择其他型号。请参阅 [GPT-6.1 Sol 可用性（按计划）](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>)。

1. 启动Codex并打开作曲家。
2. 输入 `/model` 并按 Enter。
3. 从弹出窗口中选择一个型号，例如 `gpt-6-luna` 或 `gpt-6.1-sol`。

预期：Codex 确认记录中的新模型。运行 `/status` 以验证更改。

### 使用 `/fast` 切换快速模式

1. 输入 `/fast` 打开当前型号的快速服务层。
2. 再次输入 `/fast` 将其关闭。

预期：Codex 切换层并保存选择。在 TUI 页脚中，
 您还可以使用 `/statusline` 显示快速模式状态行项目。

快速层命令是目录驱动的。如果当前型号未宣传 
 快速层，则 Codex 将不会显示 `/fast`。

### 使用 `/personality` 设置沟通方式

使用 `/personality` 更改 Codex 的通信方式，而无需重写提示。

1. 在活动聊天中，输入 `/personality` 并按 Enter。
2. 从弹出窗口中选择一种样式。

预期：Codex 确认记录中的新样式并将其用于稍后的聊天中的 
 响应。

Codex支持`friendly`、`pragmatic`和`none`个性。使用`none`
禁用个性指令。

如果活动模型不支持个性特定指令，Codex 会隐藏此命令。

### 使用 `/plan` 切换到计划模式

1. 输入 `/plan` 并按 Enter 将活动聊天切换到计划
模式。
2. 可选：提供内联提示文本（例如，`/plan Propose a
此服务的迁移计划`）。
3. 您可以在使用内联 `/plan` 参数时粘贴内容或附加图像。

预期：Codex 进入计划模式并使用可选的内联提示作为第一个计划请求。

虽然Codex已经可以使用，但`/plan`暂时不可用。

### 使用 `/goal` 设置或查看任务目标

1. 输入 `/goal <objective>` 设置目标，例如 `/goal Finish the migration and keep tests green`。
2. 输入 `/goal` 查看当前目标。
3. 使用`/goal edit`修改目标。使用`/goal pause`、`/goal resume`或`/goal clear`暂停、恢复或删除它。

预期：Codex 在工作继续的同时将目标附加到活动聊天中。

目标必须非空且最多 4,000 个字符。对于较长的
指令，请将详细信息放入文件中并将目标指向该文件。

### 使用 `/experimental` 切换实验功能

1. 输入 `/experimental` 并按 Enter。
2. 切换您想要的功能（例如，网络代理或防止运行时睡眠），然后如果提示要求您重新启动 Codex。

预期：Codex 将您的功能选择保存到配置并在重新启动时应用它们。

### 使用 `/approve` 批准自动审核拒绝

当自动审阅者拒绝最近的操作并且您希望 
Codex 重试一次时，请使用 `/approve`。

1. 输入 `/approve`。
2. 当Codex显示相关拒绝操作时确认重试。

预期：Codex 在当前会话 
 策略下重试一次拒绝的操作。

### 使用 `/memories` 配置存储器

1. 输入 `/memories`。
2. 选择 Codex 是否应使用现有记忆、生成新记忆或
保持记忆行为禁用。

预期：Codex 更新未来会话的相关内存设置。

### 使用`/skills`技能

1. 输入 `/skills`。
2. 选择您想要Codex应用的技能。

预期：Codex 插入选定的技能上下文，以便下一个请求遵循 
 该技能的说明。

<a id="import-claude-code-configuration-with-import"></a>
<a id="import-claude-code-setup-with-import"></a>
<a id="cli-import-claude-code-setup-with-import"></a>

### 使用 `/import` 导入 Claude Code 或 Cursor 设置

1. 输入 `/import`。
2. 选择**Claude Code**或**Cursor**。
3. 选择要迁移的设置、项目文件或最近的聊天记录。

预期：Codex 打开外部 agent 导入选择器，并将选定的 
 支持的工件导入到 Codex 配置和本地文件中。会话发现 
 包括最近 30 天的最多 50 条聊天记录。

从本地 TUI 会话运行 `/import`。当任务正在运行时、
在远程会话中以及连接到本地应用程序服务器守护程序时，它不可用。

有关桌面应用程序工作流程和支持的工件类型，请参阅 [从 
 另一个 agent](<https://learn.chatgpt.com/docs/import>) 导入。

<a id="clear-the-terminal-and-start-a-new-chat-with-clear"></a>
<a id="clear-the-terminal-and-start-a-new-task-with-clear"></a>

### 清除终端并与 `/clear` 开始新的聊天

1. 输入 `/clear` 并按 Enter。

预期：Codex 清除终端，重置可见记录，并在同一 CLI 会话中开始 
 新聊天。

要在创建新聊天时为其命名，请运行 `/clear release prep`。

与 <kbd>Ctrl</kbd>+<kbd>L</kbd> 不同，`/clear` 开始新的聊天。

<kbd>Ctrl</kbd>+<kbd>L</kbd>仅清除终端视图并保留当前
聊天。 Codex 在任务正在进行时禁用这两个操作。

### 使用 `/archive` 存档当前会话

1. 输入 `/archive` 并按 Enter。
2. 确认您要存档当前会话并退出 Codex。

预期：Codex 存档当前会话并关闭交互 TUI。
Codex 保留本地存储的会话记录；稍后用
`codex unarchive <SESSION>`恢复它。

任务正在运行时，`/archive` 不可用。

### 使用 `/delete` 删除当前会话

1. 输入 `/delete` 并按 Enter。
2. 确认您要删除当前会话并退出Codex。

预期：Codex 删除当前会话记录并关闭 
 交互式 TUI。删除是永久性的，并且还会删除生成的后代 
 会话。

当聊天正在运行或处于侧聊状态时，`/delete` 不可用。

### 使用 `/permissions` 更新权限

1. 输入 `/permissions` 并按 Enter。
2. 例如，选择与您的舒适度相匹配的批准预设
`Auto` 用于自动运行，或 `Read Only` 用于审查编辑。当命名的
权限配置文件处于活动状态时，选择器还会显示配置的自定义
配置文件及其描述。

预期：Codex 宣布更新的政策。未来的操作将遵循 
 更新的批准模式，直到您再次更改它。

### 将 IDE 上下文包含在 `/ide` 中

1. 输入 `/ide`。
2. 如果您想解释 Codex 应该如何处理，请添加可选的内联文本
当前IDE选择或打开的文件。

预期：Codex 在下一个提示中包含可用的 IDE 上下文。

### 使用 `/vim` 切换 Vim 模式

1. 输入 `/vim`。
2. Continue 在作曲家中编辑。

预期：Codex 切换当前会话的 Composer Vim 模式。要使 Vim
 模式成为新会话的默认模式，请在 
`config.toml` 中设置 `tui.vim_mode_default = true`。

### 使用 `/setup-default-sandbox` 设置提升的 Windows 沙箱

当 Codex 使用降级的 
 限制令牌沙箱时，此命令仅出现在 Windows 上。

1. 输入 `/setup-default-sandbox`。
2. 按照管理员设置流程进行操作。

预期：Codex 配置提升的 Windows 沙箱并选择 
 相应的自动批准预设。

### 复制最新回复 `/copy`

1. 输入 `/copy` 并按 Enter。

预期：Codex 将最新完成的 Codex 输出复制到剪贴板。

如果回合仍在运行，`/copy` 使用最新完成的输出，而不是 
 正在进行的响应。该命令在第一次完成的 
Codex 输出之前和回滚之后立即不可用。

您也可以在主TUI中按<kbd>Ctrl</kbd>+<kbd>O</kbd>来复制
最新完成的响应，而无需打开斜杠命令菜单。

### 使用 `/raw` 切换原始回滚

1. 键入 `/raw`、`/raw on` 或 `/raw off`。

预期：Codex 切换原始回滚模式，这使得终端选择和 
 复制更加直接。您还可以使用默认的 <kbd>Alt</kbd>+<kbd>R</kbd>
 绑定或使用 `tui.raw_output_mode = true` 保留默认值。

### 使用 `/sandbox-add-read-dir` 授予沙箱读取访问权限

仅当在 Windows 上本机运行 CLI 时，此命令才可用。

1. 输入 `/sandbox-add-read-dir C:\absolute\directory\path` 并按 Enter。
2. 确认路径是现有的绝对目录。

预期：Codex 刷新 Windows 沙箱策略并授予对 
 该目录的读取权限，以便以后在沙箱中运行命令。

### 使用 `/status` 检查会话

1. 在任何聊天中，输入 `/status`。
2. 查看活动模型、批准策略、可写根和的输出
当前令牌使用情况。当TUI远程连接时，输出还
显示远程地址和服务器版本。

预期：Codex 打印摘要，确认其正在按照您
 的预期运行。

### 使用 `/usage` 查看帐户使用情况

1. 输入 `/usage` 打开使用菜单。
2. 选择是否显示代币活动或兑换可用的赚取重置。
3. 要直接打开代币活动，请输入 `/usage daily`、`/usage weekly` 或 `/usage cumulative`。

预期：Codex 打开 
 所选视图的使用操作或显示帐户令牌活动。如果会话没有 Codex 服务帐户身份验证，Codex
 将显示登录要求。

### 使用 `/debug-config` 检查配置层

1. 输入 `/debug-config`。
2. 查看配置层顺序的输出（优先级最低的在前），on/off
国家和政策来源。

预期：Codex 在配置时打印层诊断以及策略详细信息，例如 
`allowed_approval_policies`、`allowed_sandbox_modes`、`mcp_servers`、`rules`、
 和 `experimental_network`。

使用此输出来调试有效设置与 `config.toml` 不同的原因。

### 使用 `/statusline` 配置页脚项目

1. 输入 `/statusline`。
2. 使用选择器切换和重新排序项目，然后确认。

预期：页脚状态行立即更新并持续显示为 `config.toml` 中的 
`tui.status_line`。

可用的状态行项目包括模型、模型+推理、上下文统计、速率
限制、git分支、令牌计数器、会话ID、当前directory/project根、
和Codex版本。

### 配置终端标题项`/title`

1. 输入 `/title`。
2. 使用选择器切换和重新排序项目，然后确认。

预期：终端窗口或选项卡标题立即更新并持续显示为 `config.toml` 中的 
`tui.terminal_title`。

可用的标题项包括应用程序名称、项目、微调器、状态、线程、git
分支、模型和任务进度。

### 使用 `/theme` 选择语法主题

1. 输入 `/theme`。
2. 从选择器中预览主题，然后确认。

预期：Codex 更新语法突出显示并将选择保留为 `config.toml` 中的 
`tui.theme`。

### 选择带有`/pets`的末期宠物

1. 输入`/pets`（或`/pet`）打开宠物选择器。
2. 选择内置或自定义宠物，或关闭宠物。

预期：Codex 在支持的终端中显示选定的环境宠物，并且 
 保留该选择。您还可以输入 `/pets off` 将其隐藏。

### 使用 `/keymap` 重新映射 TUI 快捷方式

使用 `/keymap` 检查、更新和保留 TUI 的键盘快捷键绑定。

1. 输入 `/keymap`。
2. 选择要更改的快捷方式上下文和操作。
3. 输入新绑定或删除现有绑定。

预期：Codex 更新活动键盘映射并将自定义绑定写入 `config.toml` 中的 `tui.keymap`。

键绑定使用 `ctrl-a`、`shift-enter` 和 `page-down` 等名称。上下文特定的绑定覆盖`tui.keymap.global`；空的绑定列表会解除该操作的绑定。

### 使用`/ps`检查后台终端

1. 输入 `/ps`。
2. 查看后台终端列表及其状态。

预期：Codex 显示每个后台终端的命令以及最多三个 
 最近的非空输出行，以便您可以一目了然地衡量进度。

使用【`unified_exec`】时出现后台终端；否则，列表可能为空。

### 使用 `/stop` 停止后台终端

1. 输入 `/stop`。
2. 在停止列出的终端之前确认 Codex 是否询问。

预期：Codex 停止当前会话的所有后台终端。 `/clean`
 仍可用作 `/stop` 的别名。

### 使用 `/compact` 保持成绩单精简

1. 经过长时间的交流后，输入`/compact`。
2. 确认 Codex 何时提出总结迄今为止的聊天内容。

预期：Codex 用简洁的摘要替换先前的回合，释放上下文
，同时保留关键细节。

### 使用 `/diff` 查看更改

1. 输入 `/diff` 以检查 Git 差异。
2. 滚动浏览 CLI 内的输出以查看编辑和添加的文件。

预期：Codex 显示您已暂存的更改、尚未暂存的更改，
 以及 Git 尚未开始跟踪的文件，因此您可以决定保留哪些内容。

### 使用 `/mention` 突出显示文件

1. 键入 `/mention`，后跟路径，例如 `/mention src/lib/api.ts`。
2. 从弹出窗口中选择匹配结果。

预期：Codex 将文件添加到聊天中，确保后续回合直接引用它。

<a id="start-a-new-conversation-with-new"></a>

### 与 `/new` 开始新的聊天

1. 输入 `/new` 并按 Enter。

预期：Codex 在同一 CLI 会话中开始新的聊天，因此您
可以在不离开终端的情况下切换聊天。

要在创建新聊天时为其命名，请运行 `/new bug bash`。

与`/clear`不同，`/new`不会首先清除当前的终端视图。

<a id="rename-the-current-chat-with-rename"></a>
<a id="rename-the-current-task-with-rename"></a>

### 将当前聊天重命名为 `/rename`

1. 键入 `/rename <name>`，或键入 `/rename` 打开命名提示。
2. 输入一个简短的名称，以帮助您稍后找到聊天。

预期：Codex 更新已保存的聊天名称而不更改其记录。

<a id="resume-a-saved-conversation-with-resume"></a>

### 恢复与 `/resume` 的已保存聊天

1. 输入 `/resume` 并按 Enter。
2. 从保存的会话选择器中选择您想要的会话。

预期：Codex 重新加载选定的聊天记录，以便您可以从上次中断的位置继续选择 
，保持原始历史记录完好无损。

<a id="fork-the-current-conversation-with-fork"></a>

### 与 `/fork` 分叉当前聊天

1. 输入 `/fork` 并按 Enter。

预期：Codex 将当前聊天克隆为具有新的 
ID 的新聊天，保持原始记录不变，以便您可以并行探索替代的 
 方法。

如果您需要分叉已保存的会话而不是当前会话，请在终端中运行 
`codex fork` 以打开会话选择器。

### Continue 在桌面应用程序中带有 `/app`

在 macOS 和 Windows 上，键入 `/app` 以在 ChatGPT
 桌面应用程序中打开当前会话。如果应用程序未安装或运行，Codex 会显示错误，要求
您安装或启动它。

预期：桌面应用程序会打开相同的已保存聊天记录，以便您可以继续。

<a id="start-a-side-conversation-with-side"></a>

### 与 `/side` 开始侧聊

使用 `/side` 从当前聊天启动临时分支，而无需离开主聊天。

1. 输入 `/side` 打开侧聊。
2. 可以选择添加内联文本，例如 `/side Check whether this plan has an obvious risk`。
3. 重点绕行结束后返回父聊天室。

预期：Codex 打开一个侧聊天，其记录与 
 父聊天是分开的。当您处于侧面模式时，TUI 会继续显示 
 父聊天的状态，以便您可以查看主聊天是否仍在运行。

`/side` 在另一侧聊天中和审阅模式下不可用。

### 使用 `/init` 生成 `AGENTS.md`

1. 在您希望 Codex 查找持久指令的目录中运行 `/init`。
2. 查看生成的 `AGENTS.md`，然后对其进行编辑以匹配您的存储库约定。

预期：Codex 创建一个 `AGENTS.md` 支架，您可以针对 
 未来的会话进行完善和提交。

### 请求使用 `/review` 进行工作树审查

1. 输入 `/review`。
2. 如果您想检查确切的文件更改，请按照 `/diff` 进行操作。

预期：Codex 总结了在您的工作树中发现的问题，重点关注 
 行为更改和缺少的测试。它使用当前会话模型，除非
您在`config.toml`中设置了`review_model`。

### 列出带有 `/mcp` 的 MCP 工具

1. 输入 `/mcp`。
2. 查看列表以确认哪些 MCP 服务器和工具可用。

预期：您会看到已配置的 Model Context Protocol (MCP) 工具 Codex 可以在此会话中调用。

使用 `/mcp verbose` 包含详细的服务器诊断信息。如果您传递除 `verbose` 之外的任何内容，Codex 将显示命令用法。

### 使用 `/apps` 浏览应用程序

1. 输入 `/apps`。
2. 从列表中选择一个应用程序。

预期：Codex 将应用提及作为 `$app-slug` 插入到编写器中，因此 
 您可以立即要求 Codex 使用它。

### 使用 `/plugins` 浏览插件

1. 输入 `/plugins`。
2. 选择一个市场选项卡，然后选择一个插件来检查其功能或可用操作。

预期：Codex 打开插件浏览器，以便您可以查看已安装的插件、
您的配置允许的可发现插件以及已安装的插件状态。
在已安装的插件上按 <kbd>空格键</kbd> 可切换其启用状态。

### 使用 `/hooks` 查看和管理生命周期挂钩

1. 输入 `/hooks`。
2. 选择一个挂钩事件来检查匹配的处理程序。
3. 根据需要信任、禁用或重新启用非托管挂钩。

预期：Codex 打开挂钩浏览器，以便您可以查看配置的生命周期 
 挂钩。托管挂钩显示为托管，无法从用户挂钩
浏览器禁用。

### 将 agent 线程切换为 `/agent`

1. 输入 `/agent` 或 `/subagents` 并按 Enter。
2. 从选择器中选择您想要的线程。

预期：Codex 切换活动线程，以便您可以检查或继续 
agent 的工作。

### 通过 `/feedback` 发送反馈

1. 输入 `/feedback` 并按 Enter。
2. 按照提示添加日志或诊断信息。

预期：Codex 收集请求的诊断并将其提交给 
 维护者。

### 使用 `/logout` 注销

1. 输入 `/logout` 并按 Enter。

预期：Codex 清除当前用户会话的本地凭据。

### 使用 `/quit` 或 `/exit` 退出 CLI

1. 输入 `/quit`（或 `/exit`）并按 Enter。

预期：Codex 立即退出。首先保存或提交任何重要的工作。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Developer commands

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

## How to read this reference

This page catalogs every documented Codex CLI command and flag. Use the interactive tables to search by key or description. Each section shows the option's maturity and flags deprecated options and risky combinations.

The CLI inherits most defaults from `~/.codex/config.toml`. Any
  `-c key=value` overrides you pass at the command line take
  precedence for that invocation. See [Config
  basics](<https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence>) for more
  information.

## Global flags

<ConfigTable client:load options={globalFlagOptions} />

These options apply to the base `codex` command. Most propagate to commands;
see the notes above or the relevant command help for exceptions. For propagated
flags, follow the relevant command help. For example, `codex exec --oss ...`
applies `--oss` to `exec`.

## Command overview

The Maturity column uses feature maturity labels such as Experimental, Beta,
  Stable, and Deprecated. See [Feature Maturity](<https://learn.chatgpt.com/docs/feature-maturity>) for
  how to interpret these labels.

<ConfigTable
  client:load
  options={commandOverview}
  secondColumnTitle="Maturity"
  secondColumnVariant="maturity"
/>

## Command details

### `codex` (interactive)

Running `codex` with no subcommand launches the interactive terminal UI (TUI). The agent accepts the global flags above plus image attachments. Web search defaults to cached mode; use `--search` to switch to live browsing. For low-friction local work, use `--sandbox workspace-write --ask-for-approval on-request`.

Use `--remote ws://host:port` or `--remote wss://host:port` to connect the TUI to an app server started with `codex app-server --listen ws://IP:PORT`. For a local Unix socket, use `--remote unix://` for the default socket or `--remote unix://PATH` for an explicit path. Add `--remote-auth-token-env <ENV_VAR>` when the server requires a bearer token for WebSocket authentication.

### `codex app-server`

Launch the Codex app server locally. This is primarily for development and debugging and may change without notice.

<ConfigTable client:load options={appServerOptions} />

`codex app-server --listen stdio://` keeps the default JSONL-over-stdio behavior, and `codex app-server --stdio` is an alias for that transport. `--listen ws://IP:PORT` enables WebSocket transport for app-server clients. The server accepts `ws://` listen URLs; use TLS termination or a secure proxy when clients connect with `wss://`. Use `--listen unix://` to accept WebSocket handshakes on Codex's default Unix socket, or `--listen unix:///absolute/path.sock` to choose a socket path. If you generate schemas for client bindings, add `--experimental` to include gated fields and methods.

Add `--code-mode-host wss://code-mode.example.com/host` to connect app-server to
a remote Code Mode host instead of starting a local host. This outbound
connection is separate from `--listen` and shared by every thread in the
app-server process. Use `ws://` only for a localhost or SSH-forwarded host.

### `codex remote-control`

Run `codex remote-control` to start remote control in the foreground. Use
`codex remote-control start` to start the local app-server daemon with remote
control enabled, and `codex remote-control stop` to stop it. Managed
remote-control clients and SSH remote workflows use these commands; they aren't
a replacement for `codex app-server --listen` when you're building a local
protocol client.

After the daemon is running, use `codex remote-control pair` to create and
print a short-lived manual pairing code. Add `--json` to any remote-control
command for machine-readable output. For `pair`, the JSON response includes
`pairingCode`, `manualPairingCode`, `environmentId`, and `expiresAt`.

### `codex app`

Launch the ChatGPT desktop app from the terminal on macOS or Windows. On macOS,
Codex can open a specific workspace path; on Windows, Codex prints the path to
open.

<ConfigTable client:load options={appOptions} />

`codex app` opens an installed ChatGPT desktop app, or starts the installer when
the app is missing. On macOS, Codex opens the provided workspace path; on
Windows, it prints the path to open after installation.

### `codex debug app-server send-message-v2`

Send one message through app-server's V2 thread/turn flow using the built-in app-server test client.

<ConfigTable client:load options={debugAppServerSendMessageV2Options} />

This debug flow initializes with `experimentalApi: true`, starts a thread, sends a turn, and streams server notifications. Use it to reproduce and inspect app-server protocol behavior locally.

### `codex debug models`

Print the raw model catalog Codex sees as JSON.

<ConfigTable client:load options={debugModelsOptions} />

Use `--bundled` when you want to inspect only the catalog bundled with the current binary, without refreshing from the remote models endpoint.

### `codex debug prompt-input`

Render the exact model-visible prompt input list as JSON. Use this when
debugging instruction discovery, session context, or prompt construction.

<ConfigTable client:load options={debugPromptInputOptions} />

### `codex apply`

Apply the most recent diff from a Codex cloud chat to your local repository. You must authenticate and have access to the chat.

<ConfigTable client:load options={applyOptions} />

Codex prints the patched files and exits non-zero if `git apply` fails (for example, due to conflicts).

### `codex review`

Run a code review non-interactively. Choose exactly one review target, or pass
custom review instructions as a prompt.

<ConfigTable client:load options={reviewOptions} />

`--uncommitted`, `--base`, `--commit`, and a custom `PROMPT` conflict with one
another. Use `--title` only with `--commit`.

### `codex archive` and `codex unarchive`

Archive or restore a saved interactive session by session ID or session name.
Use these commands when you want to clean up the session picker without deleting
the transcript. Session IDs take precedence over session names.

```bash
codex archive <SESSION>
codex unarchive <SESSION>
```

<ConfigTable client:load options={archiveOptions} />

### `codex delete`

Permanently delete a saved interactive session by session ID or session name.
Use this only when you want to remove the transcript instead of hiding it from
active session lists.

```bash
codex delete <SESSION>
codex delete <SESSION_UUID> --force
```

<ConfigTable client:load options={deleteOptions} />

Use `--force` only with a session UUID. Named sessions still require
confirmation so Codex doesn't delete a repeated or ambiguous name without a prompt.

### `codex cloud`

Interact with Codex cloud chats from the terminal. The default command opens an interactive picker; `codex cloud exec` submits a task directly, and `codex cloud list` returns recent chats for scripting or quick inspection.

<ConfigTable client:load options={cloudExecOptions} />

Authentication follows the same credentials as the main CLI. Codex exits non-zero if the task submission fails.

#### `codex cloud list`

List recent cloud chats with optional filtering and pagination.

<ConfigTable client:load options={cloudListOptions} />

Plain-text output prints a task URL followed by status details. Use `--json` for automation. The JSON payload contains a `tasks` array plus an optional `cursor` value. Each task includes `id`, `url`, `title`, `status`, `updated_at`, `environment_id`, `environment_label`, `summary`, `is_review`, and `attempt_total`.

### `codex completion`

Generate shell completion scripts and redirect the output to the appropriate location, for example `codex completion zsh > "${fpath[1]}/_codex"`.

<ConfigTable client:load options={completionOptions} />

### `codex doctor`

Generate a local diagnostic report before filing a support issue or
while investigating a broken Codex installation. The report checks installation,
configuration, authentication, runtime, Git, terminal, app-server, and thread
inventory health.

<ConfigTable client:load options={doctorOptions} />

### `codex features`

Manage feature flags stored in `$CODEX_HOME/config.toml`. The `enable` and
`disable` commands persist changes so they apply to future sessions. The
`features` subcommand doesn't accept `--profile`.

<ConfigTable client:load options={featuresOptions} />

### `codex exec`

Use `codex exec` (or the short form `codex e`) for scripted or CI-style runs that should finish without human interaction.

<ConfigTable client:load options={execOptions} />

Codex writes formatted output by default. Add `--json` to receive newline-delimited JSON events (one per state change). The optional `resume` subcommand lets you continue non-interactive tasks. Use `--last` to pick the most recent session from the current working directory, or add `--all` to search across all sessions:

<ConfigTable client:load options={execResumeOptions} />

### `codex execpolicy`

Check `execpolicy` rule files before you save them. `codex execpolicy check` accepts one or more `--rules` flags (for example, files under `~/.codex/rules`) and emits JSON showing the strictest decision and any matching rules. Add `--pretty` to format the output. The `execpolicy` command is currently in preview.

<ConfigTable client:load options={execpolicyOptions} />

### `codex login`

Authenticate the CLI with a ChatGPT account, API key, or access token. With no flags, Codex opens a browser for the ChatGPT OAuth flow.

<ConfigTable client:load options={loginOptions} />

`codex login status` exits with `0` when credentials are present, which is helpful in automation scripts.

### `codex logout`

Remove saved credentials for both API key and ChatGPT authentication. This command has no flags.

### `codex mcp`

Manage Model Context Protocol server entries stored in `~/.codex/config.toml`.

<ConfigTable client:load options={mcpCommands} />

The `add` subcommand supports both stdio and streamable HTTP transports:

<ConfigTable client:load options={mcpAddOptions} />

OAuth actions (`login`, `logout`) only work with streamable HTTP servers (and only when the server supports OAuth).

### `codex plugin`

Install, list, and remove plugins from configured marketplaces.

<ConfigTable client:load options={pluginCommands} />

`codex plugin add --json` prints `pluginId`, `name`, `marketplaceName`,
`version`, `installedPath`, and `authPolicy`. `codex plugin list --json` prints
`installed` and `available` arrays. Entries include `pluginId`, `name`,
`marketplaceName`, `version`, `installed`, `enabled`, `source`, `installPolicy`,
`authPolicy`, and, when available, `marketplaceSource` with the configured
marketplace source type and value. `codex plugin remove --json` prints
`pluginId`, `name`, and `marketplaceName`.

### `codex plugin marketplace`

Manage plugin marketplace sources that Codex can browse and install from.

<ConfigTable client:load options={marketplaceCommands} />

`codex plugin marketplace add` accepts GitHub shorthand such as `owner/repo` or
`owner/repo@ref`, HTTP or HTTPS Git URLs, SSH Git URLs, and local marketplace
root directories. Use `--ref` to pin a Git ref, and repeat `--sparse PATH` to
use a sparse checkout for Git-backed marketplace repositories.

`codex plugin marketplace list` prints in-scope marketplace names and roots,
including implicitly discovered default marketplaces and configured marketplace
snapshots.

Add `--json` to marketplace add, list, upgrade, or remove commands for
automation-friendly output. Marketplace add JSON includes `marketplaceName`,
`installedRoot`, and `alreadyAdded`; list JSON includes a `marketplaces` array
with `name`, `root`, and optional `marketplaceSource`; upgrade JSON includes
`selectedMarketplaces`, `upgradedRoots`, and `errors`; remove JSON includes
`marketplaceName` and `installedRoot`.

### `codex mcp-server`

The `codex mcp-server` command and standalone `codex-mcp-server` binary have
  been removed. Use the [Codex app server](<https://learn.chatgpt.com/docs/app-server>) instead.

### `codex resume`

Continue an interactive session by ID or resume the most recent chat. `codex resume` scopes `--last` to the current working directory unless you pass `--all`. It accepts the same global flags as `codex`, including model and sandbox overrides.

If the current working directory differs from the session's saved directory,
Codex asks which directory to use. Set
[`tui.resume_cwd`](<https://learn.chatgpt.com/docs/config-file/config-reference>) to `"current"` or
`"session"` to reuse that choice without a prompt. An explicit `--cd` (`-C`)
override takes precedence over `tui.resume_cwd`.

<ConfigTable client:load options={resumeOptions} />

### `codex fork`

Fork a previous interactive session into a new chat. By default, `codex fork` opens the session picker; add `--last` to fork your most recent session instead.

When the current and saved session directories differ, `codex fork` uses the
same working-directory prompt and `tui.resume_cwd` setting as `codex resume`.

<ConfigTable client:load options={forkOptions} />

### `codex sandbox`

Use the sandbox helper to run a command under the same policies Codex uses internally.

#### macOS seatbelt

<ConfigTable client:load options={sandboxMacOptions} />

#### Linux Landlock

<ConfigTable client:load options={sandboxLinuxOptions} />

#### Windows

<ConfigTable client:load options={sandboxWindowsOptions} />

### `codex update`

Check for and apply a Codex CLI update when the installed release supports self-update. Debug builds print a message telling you to install a release build instead.

## Flag combinations and safety tips

- Use `--sandbox workspace-write` for unattended local work that can stay inside the workspace, and avoid `--dangerously-bypass-approvals-and-sandbox` unless you are inside a dedicated sandbox VM.
- When you need to grant Codex write access to more directories, prefer `--add-dir` rather than forcing `--sandbox danger-full-access`.
- Pair `--json` with `--output-last-message` in CI to capture machine-readable progress and a final natural-language summary.

## Interactive shortcuts

- Type `@` to search for a file in the workspace and add its path to the prompt.
- Press <kbd>Up</kbd> or <kbd>Down</kbd> to restore draft history.
- Press <kbd>Ctrl</kbd>+<kbd>R</kbd> to search prompt history, then press <kbd>Enter</kbd> to use a match or <kbd>Escape</kbd> to cancel.
- Press <kbd>Ctrl</kbd>+<kbd>O</kbd> or run `/copy` to copy the latest completed Codex output.
- Prefix a line with `!` to run a local shell command under the current approval and sandbox settings.
- Press <kbd>Tab</kbd> while Codex is working to queue a follow-up prompt, slash command, or shell command for the next turn.
- Press <kbd>Enter</kbd> while Codex is working to inject new instructions into the current turn.
- Press <kbd>Escape</kbd> twice with an empty composer to edit the previous user message and fork the chat from that point.
- Press <kbd>Ctrl</kbd>+<kbd>C</kbd> or run `/exit` to close the session.

## Related resources

- [Codex CLI overview](<https://learn.chatgpt.com/docs/codex/cli>): installation, upgrades, and quick tips.
- [Config basics](<https://learn.chatgpt.com/docs/config-file/config-basic>): persist defaults like the model and provider.
- [Advanced Config](<https://learn.chatgpt.com/docs/config-file/config-advanced>): profiles, providers, sandbox tuning, and integrations.
- [AGENTS.md](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>): conceptual overview of Codex agent capabilities and best practices.

Slash commands give you fast, keyboard-first control over Codex. Type `/` in
the composer to open the slash popup, choose a command, and Codex will perform
actions such as switching models, adjusting permissions, or summarizing long
chats without leaving the terminal.

This guide shows you how to:

- Find the right built-in slash command for a task
- Steer an active session with commands like `/model`, `/fast`,
  `/personality`, `/permissions`, `/approve`, `/raw`, `/agent`, and `/status`

## Built-in slash commands

Codex ships with the following commands. Open the slash popup and start typing
the command name to filter the list.

When a chat is already running, you can type a slash command and press `Tab` to
queue it for the next turn. Codex parses queued slash commands when they run, so
command menus and errors appear after the current turn finishes. Slash
completion still works before you queue the command.

| Command                                                                                     | Purpose                                                         | When to use it                                                                                             |
| ------------------------------------------------------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [`/permissions`](<#update-permissions-with-permissions>)                                      | Set what Codex can do without asking first.                     | Relax or tighten approval requirements mid-session, such as switching between Auto and Read Only.          |
| [`/ide`](<#include-ide-context-with-ide>)                                                     | Include open files, current selection, and other IDE context.   | Pull editor context into the next prompt without re-explaining what's open in your IDE.                    |
| [`/keymap`](<#remap-tui-shortcuts-with-keymap>)                                               | Remap TUI keyboard shortcuts.                                   | Inspect and persist custom shortcut bindings in `config.toml`.                                             |
| [`/vim`](<#toggle-vim-mode-with-vim>)                                                         | Toggle Vim mode for the composer.                               | Switch between Vim normal/insert behavior and the default composer editing mode.                           |
| [`/setup-default-sandbox`](<#set-up-the-elevated-windows-sandbox-with-setup-default-sandbox>) | Set up the elevated agent sandbox (Windows only).               | Replace the degraded Windows sandbox after Codex offers the elevated setup.                                |
| [`/sandbox-add-read-dir`](<#grant-sandbox-read-access-with-sandbox-add-read-dir>)             | Grant sandbox read access to an extra directory (Windows only). | Unblock commands that need to read an absolute directory path outside the current readable roots.          |
| [`/agent`, `/subagents`](<#switch-agent-threads-with-agent>)                                  | Switch the active agent thread.                                 | Inspect or continue work in a spawned subagent thread.                                                     |
| [`/apps`](<#browse-apps-with-apps>)                                                           | Browse apps (connectors) and insert them into your prompt.      | Attach an app as `$app-slug` before asking Codex to use it.                                                |
| [`/plugins`](<#browse-plugins-with-plugins>)                                                  | Browse installed and discoverable plugins.                      | Inspect plugin tools, install suggested plugins, or manage plugin availability.                            |
| [`/hooks`](<#view-and-manage-lifecycle-hooks-with-hooks>)                                     | View and manage lifecycle hooks.                                | Inspect configured hooks, trust new or changed hooks, or disable non-managed hooks before they run.        |
| [`/clear`](<#clear-the-terminal-and-start-a-new-chat-with-clear>)                             | Clear the terminal and start a fresh chat.                      | Reset the visible UI and chat context together when you want a fresh start.                                |
| [`/rename`](<#rename-the-current-chat-with-rename>)                                           | Rename the current chat.                                        | Give a saved session a recognizable name without leaving the TUI.                                          |
| [`/archive`](<#archive-the-current-session-with-archive>)                                     | Archive the current session and exit Codex.                     | Remove the current session from active session lists without deleting its transcript.                      |
| [`/delete`](<#delete-the-current-session-with-delete>)                                        | Permanently delete the current session and exit Codex.          | Remove the transcript and descendant sessions when archiving isn't enough.                                 |
| [`/compact`](<#keep-transcripts-lean-with-compact>)                                           | Summarize the visible chat to free tokens.                      | Use after long runs so Codex retains key points without blowing the context window.                        |
| [`/copy`](<#copy-the-latest-response-with-copy>)                                              | Copy the latest completed Codex output.                         | Grab the latest finished response or plan text without manually selecting it. You can also press `Ctrl+O`. |
| [`/diff`](<#review-changes-with-diff>)                                                        | Show the Git diff, including files Git isn't tracking yet.      | Review Codex's edits before you commit or run tests.                                                       |
| [`/exit`](<#exit-the-cli-with-quit-or-exit>)                                                  | Exit the CLI (same as `/quit`).                                 | Alternative spelling; both commands exit the session.                                                      |
| [`/experimental`](<#toggle-experimental-features-with-experimental>)                          | Toggle experimental features.                                   | Enable options such as Network proxy or Prevent sleep while running.                                       |
| [`/approve`](<#approve-an-auto-review-denial-with-approve>)                                   | Approve one retry of a recent auto review denial.               | Retry a command or action that the auto reviewer denied.                                                   |
| [`/memories`](<#configure-memories-with-memories>)                                            | Configure memory use and generation.                            | Turn memory injection or memory generation on or off without leaving the TUI.                              |
| [`/skills`](<#use-skills-with-skills>)                                                        | Browse and use skills.                                          | Improve task-specific behavior by selecting a relevant local skill.                                        |
| [`/import`](<#import-claude-code-or-cursor-setup-with-import>)                                | Import Claude Code or Cursor setup, projects, and chats.        | Migrate supported external-agent artifacts into Codex configuration and local files.                       |
| [`/feedback`](<#send-feedback-with-feedback>)                                                 | Send logs to the Codex maintainers.                             | Report issues or share diagnostics with support.                                                           |
| [`/init`](<#generate-agentsmd-with-init>)                                                     | Generate an `AGENTS.md` scaffold in the current directory.      | Capture persistent instructions for the repository or subdirectory you're working in.                      |
| [`/logout`](<#sign-out-with-logout>)                                                          | Sign out of Codex.                                              | Clear local credentials when using a shared machine.                                                       |
| [`/mcp`](<#list-mcp-tools-with-mcp>)                                                          | List configured Model Context Protocol (MCP) tools.             | Check which external tools Codex can call during the session; add `verbose` for server details.            |
| [`/mention`](<#highlight-files-with-mention>)                                                 | Attach a file to the chat.                                      | Point Codex at specific files or folders you want it to inspect next.                                      |
| [`/model`](<#set-the-active-model-with-model>)                                                | Choose the active model (and reasoning effort, when available). | Use `gpt-6-luna` or `gpt-6.1-sol` if shown.            |
| [`/fast`](<#toggle-fast-mode-with-fast>)                                                      | Toggle a Fast service tier when the model catalog exposes one.  | Turn the current model's Fast tier on or off and persist the selection.                                    |
| [`/plan`](<#switch-to-plan-mode-with-plan>)                                                   | Switch to plan mode and optionally send a prompt.               | Ask Codex to propose an execution plan before implementation work starts.                                  |
| [`/goal`](<#set-or-view-a-task-goal-with-goal>)                                               | Set, edit, pause, resume, view, or clear a task goal.           | Give Codex a persistent target to track while a larger task runs.                                          |
| [`/personality`](<#set-a-communication-style-with-personality>)                               | Choose a communication style for responses.                     | Make Codex more concise, more explanatory, or more collaborative without changing your instructions.       |
| [`/ps`](<#check-background-terminals-with-ps>)                                                | Show background terminals and their recent output.              | Check long-running commands without leaving the main transcript.                                           |
| [`/stop`](<#stop-background-terminals-with-stop>)                                             | Stop all background terminals.                                  | Cancel background terminal work started by the current session.                                            |
| [`/fork`](<#fork-the-current-chat-with-fork>)                                                 | Fork the current chat into a new chat.                          | Branch the active session to explore a new approach without losing the current transcript.                 |
| [`/app`](<#continue-in-the-desktop-app-with-app>)                                             | Continue the current session in the ChatGPT desktop app.        | Move from the TUI to the desktop app on macOS or Windows.                                                  |
| [`/side`, `/btw`](<#start-a-side-chat-with-side>)                                             | Start an ephemeral side chat.                                   | Ask a focused follow-up without disrupting the main chat's transcript.                                     |
| [`/raw`](<#toggle-raw-scrollback-with-raw>)                                                   | Toggle raw scrollback mode.                                     | Make terminal selection and copying less formatted while reviewing long output.                            |
| [`/resume`](<#resume-a-saved-chat-with-resume>)                                               | Resume a saved chat from your session list.                     | Continue work from a previous CLI session without starting over.                                           |
| [`/new`](<#start-a-new-chat-with-new>)                                                        | Start a new chat inside the same CLI session.                   | Reset the chat context without leaving the CLI when you want a fresh prompt in the same repo.              |
| [`/quit`](<#exit-the-cli-with-quit-or-exit>)                                                  | Exit the CLI.                                                   | Leave the session immediately.                                                                             |
| [`/review`](<#ask-for-a-working-tree-review-with-review>)                                     | Ask Codex to review your working tree.                          | Run after Codex completes work or when you want a second set of eyes on local changes.                     |
| [`/status`](<#inspect-the-session-with-status>)                                               | Display session configuration and token usage.                  | Confirm the active model, approval policy, writable roots, and remaining context capacity.                 |
| [`/usage`](<#view-account-usage-with-usage>)                                                  | View account token usage or use a rate-limit reset.             | Inspect daily, weekly, or cumulative ChatGPT token activity from inside the TUI.                           |
| [`/debug-config`](<#inspect-config-layers-with-debug-config>)                                 | Print config layer and requirements diagnostics.                | Debug precedence and policy requirements, including experimental network constraints.                      |
| [`/statusline`](<#configure-footer-items-with-statusline>)                                    | Configure TUI status-line fields interactively.                 | Pick and reorder footer items (model/context/limits/git/tokens/session) and persist in config.toml.        |
| [`/title`](<#configure-terminal-title-items-with-title>)                                      | Configure terminal window or tab title fields interactively.    | Pick and reorder title items such as project, status, thread, branch, model, and task progress.            |
| [`/theme`](<#choose-a-syntax-theme-with-theme>)                                               | Choose a syntax-highlighting theme.                             | Preview and persist a terminal syntax-highlighting theme.                                                  |
| [`/pets`, `/pet`](<#choose-a-terminal-pet-with-pets>)                                         | Choose or hide a terminal pet.                                  | Personalize the TUI with a built-in or custom ambient pet.                                                 |

`/quit` and `/exit` both exit the CLI. Use them only after you have saved or
committed any important work.

Use `/permissions` to adjust what Codex can do without asking first. Use
`/approve` only when you need to retry a recent action that automatic review
denied.

## Control your session with slash commands

The following workflows keep your session on track without restarting Codex.

### Set the active model with `/model`

The picker only shows models available to your account and workspace. If GPT-6.1
Sol isn't listed, choose another model. See [GPT-6.1 Sol availability by plan](<https://learn.chatgpt.com/docs/models#gpt-6.1-sol>).

1. Start Codex and open the composer.
2. Type `/model` and press Enter.
3. Choose a model such as `gpt-6-luna` or `gpt-6.1-sol` from the popup.

Expected: Codex confirms the new model in the transcript. Run `/status` to verify the change.

### Toggle Fast mode with `/fast`

1. Type `/fast` to turn the current model's Fast service tier on.
2. Type `/fast` again to turn it off.

Expected: Codex toggles the tier and saves the selection. In the TUI footer,
you can also show a Fast mode status-line item with `/statusline`.

Fast tier commands are catalog-driven. If the current model doesn't advertise a
Fast tier, Codex won't show `/fast`.

### Set a communication style with `/personality`

Use `/personality` to change how Codex communicates without rewriting your prompt.

1. In an active chat, type `/personality` and press Enter.
2. Choose a style from the popup.

Expected: Codex confirms the new style in the transcript and uses it for later
responses in the chat.

Codex supports `friendly`, `pragmatic`, and `none` personalities. Use `none`
to disable personality instructions.

If the active model doesn't support personality-specific instructions, Codex hides this command.

### Switch to plan mode with `/plan`

1. Type `/plan` and press Enter to switch the active chat into plan
   mode.
2. Optional: provide inline prompt text (for example, `/plan Propose a
migration plan for this service`).
3. You can paste content or attach images while using inline `/plan` arguments.

Expected: Codex enters plan mode and uses your optional inline prompt as the first planning request.

While Codex is already working, `/plan` is temporarily unavailable.

### Set or view a task goal with `/goal`

1. Type `/goal <objective>` to set the goal, for example `/goal Finish the migration and keep tests green`.
2. Type `/goal` to view the current goal.
3. Use `/goal edit` to revise the objective. Use `/goal pause`, `/goal resume`, or `/goal clear` to pause, resume, or remove it.

Expected: Codex keeps the goal attached to the active chat while work continues.

Goal objectives must be non-empty and at most 4,000 characters. For longer
instructions, put the details in a file and point the goal at that file.

### Toggle experimental features with `/experimental`

1. Type `/experimental` and press Enter.
2. Toggle the features you want (for example, Network proxy or Prevent sleep while running), then restart Codex if the prompt asks you to.

Expected: Codex saves your feature choices to config and applies them on restart.

### Approve an auto review denial with `/approve`

Use `/approve` when the automatic reviewer denied a recent action and you want
Codex to retry it once.

1. Type `/approve`.
2. Confirm the retry when Codex shows the relevant denied action.

Expected: Codex retries that denied action once under the current session
policy.

### Configure memories with `/memories`

1. Type `/memories`.
2. Choose whether Codex should use existing memories, generate new memories, or
   keep memory behavior disabled.

Expected: Codex updates the relevant memory settings for future sessions.

### Use skills with `/skills`

1. Type `/skills`.
2. Pick the skill you want Codex to apply.

Expected: Codex inserts the selected skill context so the next request follows
that skill's instructions.

<a id="import-claude-code-configuration-with-import"></a>
<a id="import-claude-code-setup-with-import"></a>
<a id="cli-import-claude-code-setup-with-import"></a>

### Import Claude Code or Cursor setup with `/import`

1. Type `/import`.
2. Choose **Claude Code** or **Cursor**.
3. Select the setup, project files, or recent chats you want to migrate.

Expected: Codex opens the external-agent import picker and imports the selected
supported artifacts into Codex configuration and local files. Session discovery
includes up to 50 chats from the last 30 days.

Run `/import` from a local TUI session. It's unavailable while a task is running,
in remote sessions, and while connected to the local app-server daemon.

For the desktop app workflow and supported artifact types, see [Import from
another agent](<https://learn.chatgpt.com/docs/import>).

<a id="clear-the-terminal-and-start-a-new-chat-with-clear"></a>
<a id="clear-the-terminal-and-start-a-new-task-with-clear"></a>

### Clear the terminal and start a new chat with `/clear`

1. Type `/clear` and press Enter.

Expected: Codex clears the terminal, resets the visible transcript, and starts
a fresh chat in the same CLI session.

To name the new chat as you create it, run `/clear release prep`.

Unlike <kbd>Ctrl</kbd>+<kbd>L</kbd>, `/clear` starts a new chat.

<kbd>Ctrl</kbd>+<kbd>L</kbd> only clears the terminal view and keeps the current
chat. Codex disables both actions while a task is in progress.

### Archive the current session with `/archive`

1. Type `/archive` and press Enter.
2. Confirm that you want to archive the current session and exit Codex.

Expected: Codex archives the current session and closes the interactive TUI.
Codex keeps the session transcript stored locally; restore it later with
`codex unarchive <SESSION>`.

`/archive` is unavailable while a task is running.

### Delete the current session with `/delete`

1. Type `/delete` and press Enter.
2. Confirm that you want to delete the current session and exit Codex.

Expected: Codex deletes the current session transcript and closes the
interactive TUI. Deletion is permanent and also removes spawned descendant
sessions.

`/delete` is unavailable while a chat is running or in a side chat.

### Update permissions with `/permissions`

1. Type `/permissions` and press Enter.
2. Select the approval preset that matches your comfort level, for example
   `Auto` for hands-off runs or `Read Only` to review edits. When named
   permission profiles are active, the picker also shows configured custom
   profiles and their descriptions.

Expected: Codex announces the updated policy. Future actions respect the
updated approval mode until you change it again.

### Include IDE context with `/ide`

1. Type `/ide`.
2. Add optional inline text if you want to explain what Codex should do with the
   current IDE selection or open files.

Expected: Codex includes available IDE context in the next prompt.

### Toggle Vim mode with `/vim`

1. Type `/vim`.
2. Continue editing in the composer.

Expected: Codex toggles composer Vim mode for the current session. To make Vim
mode the default for new sessions, set `tui.vim_mode_default = true` in
`config.toml`.

### Set up the elevated Windows sandbox with `/setup-default-sandbox`

This command appears only on Windows when Codex is using the degraded
restricted-token sandbox.

1. Type `/setup-default-sandbox`.
2. Follow the administrator setup flow.

Expected: Codex configures the elevated Windows sandbox and selects the
corresponding automatic approval preset.

### Copy the latest response with `/copy`

1. Type `/copy` and press Enter.

Expected: Codex copies the latest completed Codex output to your clipboard.

If a turn is still running, `/copy` uses the latest completed output instead of
the in-progress response. The command is unavailable before the first completed
Codex output and immediately after a rollback.

You can also press <kbd>Ctrl</kbd>+<kbd>O</kbd> from the main TUI to copy the
latest completed response without opening the slash command menu.

### Toggle raw scrollback with `/raw`

1. Type `/raw`, `/raw on`, or `/raw off`.

Expected: Codex toggles raw scrollback mode, which makes terminal selection and
copying more direct. You can also use the default <kbd>Alt</kbd>+<kbd>R</kbd>
binding or persist the default with `tui.raw_output_mode = true`.

### Grant sandbox read access with `/sandbox-add-read-dir`

This command is available only when running the CLI natively on Windows.

1. Type `/sandbox-add-read-dir C:\absolute\directory\path` and press Enter.
2. Confirm the path is an existing absolute directory.

Expected: Codex refreshes the Windows sandbox policy and grants read access to
that directory for later commands that run in the sandbox.

### Inspect the session with `/status`

1. In any chat, type `/status`.
2. Review the output for the active model, approval policy, writable roots, and
   current token usage. When the TUI connects remotely, the output also
   shows the remote address and the server version.

Expected: Codex prints a summary confirming that it's operating where you
expect.

### View account usage with `/usage`

1. Type `/usage` to open the usage menu.
2. Choose whether to show token activity or redeem an available earned reset.
3. To open token activity directly, type `/usage daily`, `/usage weekly`, or `/usage cumulative`.

Expected: Codex opens usage actions or shows account token activity for the
selected view. If the session doesn't have Codex service account auth, Codex
shows a sign-in requirement.

### Inspect config layers with `/debug-config`

1. Type `/debug-config`.
2. Review the output for config layer order (lowest precedence first), on/off
   state, and policy sources.

Expected: Codex prints layer diagnostics plus policy details such as
`allowed_approval_policies`, `allowed_sandbox_modes`, `mcp_servers`, `rules`,
and `experimental_network` when configured.

Use this output to debug why an effective setting differs from `config.toml`.

### Configure footer items with `/statusline`

1. Type `/statusline`.
2. Use the picker to toggle and reorder items, then confirm.

Expected: The footer status line updates immediately and persists to
`tui.status_line` in `config.toml`.

Available status-line items include model, model+reasoning, context stats, rate
limits, git branch, token counters, session id, current directory/project root,
and Codex version.

### Configure terminal title items with `/title`

1. Type `/title`.
2. Use the picker to toggle and reorder items, then confirm.

Expected: The terminal window or tab title updates immediately and persists to
`tui.terminal_title` in `config.toml`.

Available title items include app name, project, spinner, status, thread, git
branch, model, and task progress.

### Choose a syntax theme with `/theme`

1. Type `/theme`.
2. Preview a theme from the picker, then confirm.

Expected: Codex updates syntax highlighting and persists the choice to
`tui.theme` in `config.toml`.

### Choose a terminal pet with `/pets`

1. Type `/pets` (or `/pet`) to open the pet picker.
2. Choose a built-in or custom pet, or turn pets off.

Expected: Codex displays the selected ambient pet in supported terminals and
persists the selection. You can also type `/pets off` to hide it.

### Remap TUI shortcuts with `/keymap`

Use `/keymap` to inspect, update, and persist keyboard shortcut bindings for the TUI.

1. Type `/keymap`.
2. Pick the shortcut context and action you want to change.
3. Enter the new binding or remove the existing one.

Expected: Codex updates the active keymap and writes the custom binding to `tui.keymap` in `config.toml`.

Key bindings use names such as `ctrl-a`, `shift-enter`, and `page-down`. Context-specific bindings override `tui.keymap.global`; an empty binding list unbinds the action.

### Check background terminals with `/ps`

1. Type `/ps`.
2. Review the list of background terminals and their status.

Expected: Codex shows each background terminal's command plus up to three
recent, non-empty output lines so you can gauge progress at a glance.

Background terminals appear when `unified_exec` is in use; otherwise, the list may be empty.

### Stop background terminals with `/stop`

1. Type `/stop`.
2. Confirm if Codex asks before stopping the listed terminals.

Expected: Codex stops all background terminals for the current session. `/clean`
is still available as an alias for `/stop`.

### Keep transcripts lean with `/compact`

1. After a long exchange, type `/compact`.
2. Confirm when Codex offers to summarize the chat so far.

Expected: Codex replaces earlier turns with a concise summary, freeing context
while keeping critical details.

### Review changes with `/diff`

1. Type `/diff` to inspect the Git diff.
2. Scroll through the output inside the CLI to review edits and added files.

Expected: Codex shows changes you've staged, changes you haven't staged yet,
and files Git hasn't started tracking, so you can decide what to keep.

### Highlight files with `/mention`

1. Type `/mention` followed by a path, for example `/mention src/lib/api.ts`.
2. Select the matching result from the popup.

Expected: Codex adds the file to the chat, ensuring follow-up turns reference it directly.

<a id="start-a-new-conversation-with-new"></a>

### Start a new chat with `/new`

1. Type `/new` and press Enter.

Expected: Codex starts a fresh chat in the same CLI session, so you
can switch chats without leaving your terminal.

To name the new chat as you create it, run `/new bug bash`.

Unlike `/clear`, `/new` doesn't clear the current terminal view first.

<a id="rename-the-current-chat-with-rename"></a>
<a id="rename-the-current-task-with-rename"></a>

### Rename the current chat with `/rename`

1. Type `/rename <name>`, or type `/rename` to open the naming prompt.
2. Enter a short name that will help you find the chat later.

Expected: Codex updates the saved chat name without changing its transcript.

<a id="resume-a-saved-conversation-with-resume"></a>

### Resume a saved chat with `/resume`

1. Type `/resume` and press Enter.
2. Choose the session you want from the saved-session picker.

Expected: Codex reloads the selected chat's transcript so you can pick
up where you left off, keeping the original history intact.

<a id="fork-the-current-conversation-with-fork"></a>

### Fork the current chat with `/fork`

1. Type `/fork` and press Enter.

Expected: Codex clones the current chat into a new chat with a fresh
ID, leaving the original transcript untouched so you can explore an alternative
approach in parallel.

If you need to fork a saved session instead of the current one, run
`codex fork` in your terminal to open the session picker.

### Continue in the desktop app with `/app`

On macOS and Windows, type `/app` to open the current session in the ChatGPT
desktop app. If the app isn't installed or running, Codex shows an error asking
you to install or launch it.

Expected: The desktop app opens the same saved chat so you can continue there.

<a id="start-a-side-conversation-with-side"></a>

### Start a side chat with `/side`

Use `/side` to start an ephemeral fork from the current chat without switching away from the main chat.

1. Type `/side` to open a side chat.
2. Optionally add inline text, for example `/side Check whether this plan has an obvious risk`.
3. Return to the parent chat after the focused detour finishes.

Expected: Codex opens a side chat whose transcript is separate from
the parent chat. While you are in side mode, the TUI continues to show the
parent chat's status so you can see whether the main chat is still running.

`/side` is unavailable inside another side chat and during review mode.

### Generate `AGENTS.md` with `/init`

1. Run `/init` in the directory where you want Codex to look for persistent instructions.
2. Review the generated `AGENTS.md`, then edit it to match your repository conventions.

Expected: Codex creates an `AGENTS.md` scaffold you can refine and commit for
future sessions.

### Ask for a working tree review with `/review`

1. Type `/review`.
2. Follow up with `/diff` if you want to inspect the exact file changes.

Expected: Codex summarizes issues it finds in your working tree, focusing on
behavior changes and missing tests. It uses the current session model unless
you set `review_model` in `config.toml`.

### List MCP tools with `/mcp`

1. Type `/mcp`.
2. Review the list to confirm which MCP servers and tools are available.

Expected: You see the configured Model Context Protocol (MCP) tools Codex can call in this session.

Use `/mcp verbose` to include detailed server diagnostics. If you pass anything other than `verbose`, Codex shows the command usage.

### Browse apps with `/apps`

1. Type `/apps`.
2. Pick an app from the list.

Expected: Codex inserts the app mention into the composer as `$app-slug`, so
you can immediately ask Codex to use it.

### Browse plugins with `/plugins`

1. Type `/plugins`.
2. Choose a marketplace tab, then pick a plugin to inspect its capabilities or available actions.

Expected: Codex opens the plugin browser so you can review installed plugins,
discoverable plugins that your configuration allows, and installed plugin state.
Press <kbd>Space</kbd> on an installed plugin to toggle its enabled state.

### View and manage lifecycle hooks with `/hooks`

1. Type `/hooks`.
2. Choose a hook event to inspect the matching handlers.
3. Trust, disable, or re-enable non-managed hooks as needed.

Expected: Codex opens the hook browser so you can review configured lifecycle
hooks. Managed hooks appear as managed and can't be disabled from the user hook
browser.

### Switch agent threads with `/agent`

1. Type `/agent` or `/subagents` and press Enter.
2. Select the thread you want from the picker.

Expected: Codex switches the active thread so you can inspect or continue that
agent's work.

### Send feedback with `/feedback`

1. Type `/feedback` and press Enter.
2. Follow the prompts to include logs or diagnostics.

Expected: Codex collects the requested diagnostics and submits them to the
maintainers.

### Sign out with `/logout`

1. Type `/logout` and press Enter.

Expected: Codex clears local credentials for the current user session.

### Exit the CLI with `/quit` or `/exit`

1. Type `/quit` (or `/exit`) and press Enter.

Expected: Codex exits immediately. Save or commit any important work first.


</details>
