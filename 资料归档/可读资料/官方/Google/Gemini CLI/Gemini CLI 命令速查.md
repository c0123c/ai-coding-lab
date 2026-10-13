# Gemini CLI 命令速查

汇总启动参数、交互命令、模型、扩展和 MCP 管理选项，便于查找具体终端操作。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-05-01](<https://geminicli.com/docs/cli/cli-reference/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/cli-reference/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 命令速查.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI 备忘单

本页提供常用的Gemini CLI命令、选项、
和参数的参考。

## CLI命令

[标题为“CLI命令”的部分](<#cli命令>)

|命令|描述|示例|
| --- | --- | --- |
| `gemini` |开始互动【REPL】| `gemini` |
| `gemini -p "query"` |非交互式查询| `gemini -p "summarize README.md"` |
|双子座“询问”|交互式查询并继续|双子座“解释一下这个项目”|
| `cat file | gemini` |处理管道内容| `cat logs.txt | gemini` `Get-Content logs.txt | gemini` |
| `gemini -i "query"` |交互执行并继续| `gemini -i "What is the purpose of this project?"` |
| `gemini -r "latest"` |Continue 最近的会话| `gemini -r "latest"` |
| `gemini -r "latest" "query"` |带有新提示的 Continue 会话| `gemini -r "latest" "Check for type errors"` |
| `gemini -r "<session-id>" "query"` |由 ID 恢复会话| `gemini -r "abc123" "Finish this PR"` |
| `gemini update` |更新到最新版本| `gemini update` |
| `gemini extensions` |管理扩展|请参阅[扩展管理](<#扩展管理>)|
| `gemini mcp` |配置MCP服务器|参见[MCP服务器管理](<#mcp服务器管理>)|

### 位置参数

[标题为“位置参数”的部分](<#位置参数>)

|论证|类型|描述|
| --- | --- | --- |
| `query` |字符串（可变参数）|位置提示。默认为 TTY 中的交互模式。使用 `-p/--prompt` 进行非交互式执行。|

## 交互式命令

[标题为“交互式命令”的部分](<#交互式命令>)

这些命令可在交互式 REPL 中使用。

|命令|描述|
| --- | --- |
| `/skills reload` |从磁盘重新加载发现的技能|
| `/agents reload` |重新加载agent注册表|
| `/commands list` |列出可用的自定义斜杠命令|
| `/commands reload` |重新加载自定义斜杠命令|
| `/memory reload` |重新加载上下文文件（例如，`GEMINI.md`）|
| `/mcp reload` |重新启动并重新加载MCP服务器|
| `/extensions reload` |重新加载所有活动扩展|
| `/help` |显示所有命令的帮助|
| `/quit` |退出交互会话|

## CLI 选项

[标题为“CLI选项”的部分](<#cli-选项>)

|选项|别名|类型|默认|描述|
| --- | --- | --- | --- | --- |
| `--debug` | `-d` |布尔值| `false` |在带有详细日志记录的调试模式下运行|
| `--version` | `-v` | - | - |显示CLI版本号并退出|
| `--help` | `-h` | - | - |显示帮助信息|
| `--model` | `-m` |字符串| `auto` |使用的模型。有关可用值，请参阅[型号选择](<#选型>)。|
| `--prompt` | `-p` |字符串| - |提示文字。附加到标准输入（如果提供）。强制非交互模式。|
| `--prompt-interactive` | `-i` |字符串| - |执行提示符并以交互模式继续|
| `--worktree` | `-w` |字符串| - |在新的 git 工作树中启动 Gemini。如果未提供名称，则会自动生成一个名称。设置中需要`experimental.worktrees: true`。|
| `--sandbox` | `-s` |布尔值| `false` |在沙盒环境中运行以更安全地执行|
| `--skip-trust` | - |布尔值| `false` |信任此会话的当前工作区，跳过文件夹信任检查。|
| `--approval-mode` | - |字符串| `default` |工具执行的批准模式。选项：`default`、`auto_edit`、`yolo`、`plan`|
| `--yolo` | `-y` |布尔值| `false` |**已弃用。** 自动批准所有操作。请改用 `--approval-mode=yolo`。|
| `--experimental-acp` | - |布尔值| - |以 ACP（Agent 代码引导）模式启动。 **实验功能。**|
| `--experimental-zed-integration` | - |布尔值| - |在 Zed 编辑器集成模式下运行。 **实验功能。**|
| `--allowed-mcp-server-names` | - |数组| - |允许的 MCP 服务器名称（逗号分隔或多个标志）|
| `--allowed-tools` | - |数组| - |**已弃用。** 请改用[策略引擎](<https://geminicli.com/docs/reference/policy-engine>)。无需确认即可运行的工具（逗号分隔或多个标志）|
| `--extensions` | `-e` |数组| - |要使用的扩展列表。如果未提供，则启用所有扩展（逗号分隔或多个标志）|
| `--list-extensions` | `-l` |布尔值| - |列出所有可用的扩展并退出|
| `--resume` | `-r` |字符串| - |恢复之前的会话。使用 `"latest"` 作为最新编号或索引号（例如 `--resume 5`）|
| `--list-sessions` | - |布尔值| - |列出当前项目的可用会话并退出|
| `--delete-session` | - |字符串| - |按索引号删除会话（使用`--list-sessions`查看可用会话）|
| `--include-directories` | - |数组| - |要包含在工作区中的其他目录（逗号分隔或多个标志）|
| `--screen-reader` | - |布尔值| - |启用屏幕阅读器模式以实现辅助功能|
| `--output-format` | `-o` |字符串| `text` |CLI 输出的格式。选项：`text`、`json`、`stream-json`|

## 选型

[标题为“模型选择”的部分](<#选型>)

`--model`（或 `-m`）标志可让您指定要使用的 Gemini 模型。您可以
使用模型别名（用户友好的名称）或具体模型名称。

### 模型别名

[标题为“模型别名”的部分](<#模型别名>)

这些是映射到特定模型的便捷快捷方式：

|别名|决心|描述|
| --- | --- | --- |
| `auto` |`gemini-2.5-pro` 或 `gemini-3-pro-preview`|**默认。** 如果启用预览功能，则解析为预览模型，否则解析为标准专业模型。|
| `pro` |`gemini-2.5-pro` 或 `gemini-3-pro-preview`|用于复杂的推理任务。如果启用，则使用预览模型。|
| `flash` | `gemini-2.5-flash` |适用于大多数任务的快速、平衡的模型。|
| `flash-lite` | `gemini-2.5-flash-lite` |适用于简单任务的最快模型。|

## 扩展管理

[标题为“扩展管理”的部分](<#扩展管理>)

|命令|描述|示例|
| --- | --- | --- |
| `gemini extensions install <source>` |从 Git URL 或本地路径安装扩展| `gemini extensions install https://github.com/user/my-extension` |
| `gemini extensions install <source> --ref <ref>` |从特定的 branch/tag/commit 安装| `gemini extensions install https://github.com/user/my-extension --ref develop` |
| `gemini extensions install <source> --auto-update` |安装并启用自动更新| `gemini extensions install https://github.com/user/my-extension --auto-update` |
| `gemini extensions uninstall <name>` |卸载一个或多个扩展| `gemini extensions uninstall my-extension` |
| `gemini extensions list` |列出所有已安装的扩展| `gemini extensions list` |
| `gemini extensions update <name>` |更新特定扩展| `gemini extensions update my-extension` |
| `gemini extensions update --all` |更新所有扩展| `gemini extensions update --all` |
| `gemini extensions enable <name>` |启用扩展| `gemini extensions enable my-extension` |
| `gemini extensions disable <name>` |禁用扩展| `gemini extensions disable my-extension` |
| `gemini extensions link <path>` |链接本地扩展进行开发| `gemini extensions link /path/to/extension` |
| `gemini extensions new <path>` |从模板创建新扩展| `gemini extensions new ./my-extension` |
| `gemini extensions validate <path>` |验证扩展结构| `gemini extensions validate ./my-extension` |

有关更多详细信息，请参阅[扩展文档](<https://geminicli.com/docs/extensions>)。

## MCP服务器管理

[标题为“MCP服务器管理”的部分](<#mcp服务器管理>)

|命令|描述|示例|
| --- | --- | --- |
| `gemini mcp add <name> <command>` |添加基于 stdio 的 MCP 服务器| `gemini mcp add github npx -y @modelcontextprotocol/server-github` |
| `gemini mcp add <name> <url> --transport http` |添加基于HTTP-的MCP服务器| `gemini mcp add api-server http://localhost:3000 --transport http` |
| `gemini mcp add <name> <command> --env KEY=value` |添加环境变量| `gemini mcp add slack node server.js --env SLACK_TOKEN=xoxb-xxx` |
| `gemini mcp add <name> <command> --scope user` |添加用户范围| `gemini mcp add db node db-server.js --scope user` |
| `gemini mcp add <name> <command> --include-tools tool1,tool2` |使用特定工具添加| `gemini mcp add github npx -y @modelcontextprotocol/server-github --include-tools list_repos,get_pr` |
| `gemini mcp remove <name>` |删除 MCP 服务器| `gemini mcp remove github` |
| `gemini mcp list` |列出所有已配置的MCP服务器| `gemini mcp list` |

有关更多详细信息，请参阅[MCP服务器集成](<https://geminicli.com/docs/tools/mcp-server>)。

## 技能管理

[标题为“技能管理”的部分](<#技能管理>)

|命令|描述|示例|
| --- | --- | --- |
| `gemini skills list` |列出所有发现的 agent skills| `gemini skills list` |
| `gemini skills install <source>` |从 Git、路径或文件安装技能| `gemini skills install https://github.com/u/repo` |
| `gemini skills link <path>` |通过符号链接链接本地 agent skills| `gemini skills link /path/to/my-skills` |
| `gemini skills uninstall <name>` |卸载 agent 技能| `gemini skills uninstall my-skill` |
| `gemini skills enable <name>` |启用 agent 技能| `gemini skills enable my-skill` |
| `gemini skills disable <name>` |禁用agent技能| `gemini skills disable my-skill` |
| `gemini skills enable --all` |启用所有技能| `gemini skills enable --all` |
| `gemini skills disable --all` |禁用所有技能| `gemini skills disable --all` |

有关更多详细信息，请参阅[Agent Skills文档](<https://geminicli.com/docs/cli/skills>)。

最后更新：五月 1、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI cheatsheet

This page provides a reference for commonly used Gemini CLI commands, options,
and parameters.

## CLI commands

[Section titled “CLI commands”](<#cli-commands>)

| Command | Description | Example |
| --- | --- | --- |
| `gemini` | Start interactive REPL | `gemini` |
| `gemini -p "query"` | Query non-interactively | `gemini -p "summarize README.md"` |
| gemini “query” | Query and continue interactively | gemini “explain this project” |
| `cat file | gemini` | Process piped content | `cat logs.txt | gemini` `Get-Content logs.txt | gemini` |
| `gemini -i "query"` | Execute and continue interactively | `gemini -i "What is the purpose of this project?"` |
| `gemini -r "latest"` | Continue most recent session | `gemini -r "latest"` |
| `gemini -r "latest" "query"` | Continue session with a new prompt | `gemini -r "latest" "Check for type errors"` |
| `gemini -r "<session-id>" "query"` | Resume session by ID | `gemini -r "abc123" "Finish this PR"` |
| `gemini update` | Update to latest version | `gemini update` |
| `gemini extensions` | Manage extensions | See [Extensions Management](<#extensions-management>) |
| `gemini mcp` | Configure MCP servers | See [MCP Server Management](<#mcp-server-management>) |

### Positional arguments

[Section titled “Positional arguments”](<#positional-arguments>)

| Argument | Type | Description |
| --- | --- | --- |
| `query` | string (variadic) | Positional prompt. Defaults to interactive mode in a TTY. Use `-p/--prompt` for non-interactive execution. |

## Interactive commands

[Section titled “Interactive commands”](<#interactive-commands>)

These commands are available within the interactive REPL.

| Command | Description |
| --- | --- |
| `/skills reload` | Reload discovered skills from disk |
| `/agents reload` | Reload the agent registry |
| `/commands list` | List available custom slash commands |
| `/commands reload` | Reload custom slash commands |
| `/memory reload` | Reload context files (for example, `GEMINI.md`) |
| `/mcp reload` | Restart and reload MCP servers |
| `/extensions reload` | Reload all active extensions |
| `/help` | Show help for all commands |
| `/quit` | Exit the interactive session |

## CLI Options

[Section titled “CLI Options”](<#cli-options>)

| Option | Alias | Type | Default | Description |
| --- | --- | --- | --- | --- |
| `--debug` | `-d` | boolean | `false` | Run in debug mode with verbose logging |
| `--version` | `-v` | - | - | Show CLI version number and exit |
| `--help` | `-h` | - | - | Show help information |
| `--model` | `-m` | string | `auto` | Model to use. See [Model Selection](<#model-selection>) for available values. |
| `--prompt` | `-p` | string | - | Prompt text. Appended to stdin input if provided. Forces non-interactive mode. |
| `--prompt-interactive` | `-i` | string | - | Execute prompt and continue in interactive mode |
| `--worktree` | `-w` | string | - | Start Gemini in a new git worktree. If no name is provided, one is generated automatically. Requires `experimental.worktrees: true` in settings. |
| `--sandbox` | `-s` | boolean | `false` | Run in a sandboxed environment for safer execution |
| `--skip-trust` | - | boolean | `false` | Trust the current workspace for this session, skipping the folder trust check. |
| `--approval-mode` | - | string | `default` | Approval mode for tool execution. Choices: `default`, `auto_edit`, `yolo`, `plan` |
| `--yolo` | `-y` | boolean | `false` | **Deprecated.** Auto-approve all actions. Use `--approval-mode=yolo` instead. |
| `--experimental-acp` | - | boolean | - | Start in ACP (Agent Code Pilot) mode. **Experimental feature.** |
| `--experimental-zed-integration` | - | boolean | - | Run in Zed editor integration mode. **Experimental feature.** |
| `--allowed-mcp-server-names` | - | array | - | Allowed MCP server names (comma-separated or multiple flags) |
| `--allowed-tools` | - | array | - | **Deprecated.** Use the [Policy Engine](<https://geminicli.com/docs/reference/policy-engine>) instead. Tools that are allowed to run without confirmation (comma-separated or multiple flags) |
| `--extensions` | `-e` | array | - | List of extensions to use. If not provided, all extensions are enabled (comma-separated or multiple flags) |
| `--list-extensions` | `-l` | boolean | - | List all available extensions and exit |
| `--resume` | `-r` | string | - | Resume a previous session. Use `"latest"` for most recent or index number (for example `--resume 5`) |
| `--list-sessions` | - | boolean | - | List available sessions for the current project and exit |
| `--delete-session` | - | string | - | Delete a session by index number (use `--list-sessions` to see available sessions) |
| `--include-directories` | - | array | - | Additional directories to include in the workspace (comma-separated or multiple flags) |
| `--screen-reader` | - | boolean | - | Enable screen reader mode for accessibility |
| `--output-format` | `-o` | string | `text` | The format of the CLI output. Choices: `text`, `json`, `stream-json` |

## Model selection

[Section titled “Model selection”](<#model-selection>)

The `--model` (or `-m`) flag lets you specify which Gemini model to use. You can
use either model aliases (user-friendly names) or concrete model names.

### Model aliases

[Section titled “Model aliases”](<#model-aliases>)

These are convenient shortcuts that map to specific models:

| Alias | Resolves To | Description |
| --- | --- | --- |
| `auto` | `gemini-2.5-pro` or `gemini-3-pro-preview` | **Default.** Resolves to the preview model if preview features are enabled, otherwise resolves to the standard pro model. |
| `pro` | `gemini-2.5-pro` or `gemini-3-pro-preview` | For complex reasoning tasks. Uses preview model if enabled. |
| `flash` | `gemini-2.5-flash` | Fast, balanced model for most tasks. |
| `flash-lite` | `gemini-2.5-flash-lite` | Fastest model for simple tasks. |

## Extensions management

[Section titled “Extensions management”](<#extensions-management>)

| Command | Description | Example |
| --- | --- | --- |
| `gemini extensions install <source>` | Install extension from Git URL or local path | `gemini extensions install https://github.com/user/my-extension` |
| `gemini extensions install <source> --ref <ref>` | Install from specific branch/tag/commit | `gemini extensions install https://github.com/user/my-extension --ref develop` |
| `gemini extensions install <source> --auto-update` | Install with auto-update enabled | `gemini extensions install https://github.com/user/my-extension --auto-update` |
| `gemini extensions uninstall <name>` | Uninstall one or more extensions | `gemini extensions uninstall my-extension` |
| `gemini extensions list` | List all installed extensions | `gemini extensions list` |
| `gemini extensions update <name>` | Update a specific extension | `gemini extensions update my-extension` |
| `gemini extensions update --all` | Update all extensions | `gemini extensions update --all` |
| `gemini extensions enable <name>` | Enable an extension | `gemini extensions enable my-extension` |
| `gemini extensions disable <name>` | Disable an extension | `gemini extensions disable my-extension` |
| `gemini extensions link <path>` | Link local extension for development | `gemini extensions link /path/to/extension` |
| `gemini extensions new <path>` | Create new extension from template | `gemini extensions new ./my-extension` |
| `gemini extensions validate <path>` | Validate extension structure | `gemini extensions validate ./my-extension` |

See [Extensions Documentation](<https://geminicli.com/docs/extensions>) for more details.

## MCP server management

[Section titled “MCP server management”](<#mcp-server-management>)

| Command | Description | Example |
| --- | --- | --- |
| `gemini mcp add <name> <command>` | Add stdio-based MCP server | `gemini mcp add github npx -y @modelcontextprotocol/server-github` |
| `gemini mcp add <name> <url> --transport http` | Add HTTP-based MCP server | `gemini mcp add api-server http://localhost:3000 --transport http` |
| `gemini mcp add <name> <command> --env KEY=value` | Add with environment variables | `gemini mcp add slack node server.js --env SLACK_TOKEN=xoxb-xxx` |
| `gemini mcp add <name> <command> --scope user` | Add with user scope | `gemini mcp add db node db-server.js --scope user` |
| `gemini mcp add <name> <command> --include-tools tool1,tool2` | Add with specific tools | `gemini mcp add github npx -y @modelcontextprotocol/server-github --include-tools list_repos,get_pr` |
| `gemini mcp remove <name>` | Remove an MCP server | `gemini mcp remove github` |
| `gemini mcp list` | List all configured MCP servers | `gemini mcp list` |

See [MCP Server Integration](<https://geminicli.com/docs/tools/mcp-server>) for more details.

## Skills management

[Section titled “Skills management”](<#skills-management>)

| Command | Description | Example |
| --- | --- | --- |
| `gemini skills list` | List all discovered agent skills | `gemini skills list` |
| `gemini skills install <source>` | Install skill from Git, path, or file | `gemini skills install https://github.com/u/repo` |
| `gemini skills link <path>` | Link local agent skills via symlink | `gemini skills link /path/to/my-skills` |
| `gemini skills uninstall <name>` | Uninstall an agent skill | `gemini skills uninstall my-skill` |
| `gemini skills enable <name>` | Enable an agent skill | `gemini skills enable my-skill` |
| `gemini skills disable <name>` | Disable an agent skill | `gemini skills disable my-skill` |
| `gemini skills enable --all` | Enable all skills | `gemini skills enable --all` |
| `gemini skills disable --all` | Disable all skills | `gemini skills disable --all` |

See [Agent Skills Documentation](<https://geminicli.com/docs/cli/skills>) for more details.

Last updated: May 1, 2026


</details>
