# 配置 Copilot CLI 的信任与权限

详细说明可信目录、工具批准、Shell 命令、文件路径及 URL 权限，帮助控制 Agent 可以访问和执行的范围。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-cli/set-up-copilot-cli/configure-copilot-cli.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/配置 Copilot CLI 的信任与权限.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Configuring GitHub Copilot CLI
shortTitle: Configure Copilot CLI
intro: Configure trusted directories, tool access, and path and URL permissions for {% data variables.copilot.copilot_cli_short %}
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot # Copilot discovery page
  - Configure Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

{% data variables.copilot.copilot_cli_short %} 有多个配置选项，可以控制它可以代表您访问和执行的操作。

本文向您展示如何设置受信任目录、配置工具的访问权限以及授予对文件路径和 URL 的权限。

### 先决条件

* 安装{% data variables.copilot.copilot_cli_short %}。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>)。

## 设置受信任目录

受信任的目录控制 {% data variables.copilot.copilot_cli_short %} 可以读取、修改和执行文件的位置。信任目录具有安全隐患，请参阅[安全注意事项](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>)。

### 选择信任目录

当您启动 {% data variables.copilot.copilot_cli %} 会话时，系统会要求您确认您信任启动 CLI 的目录中及其下面的文件。

您可以选择信任当前目录：

* 仅当前正在运行的会话
* 本次会议和未来的会议

如果您选择为将来的会话信任该目录，则不会再次显示受信任目录提示。仅当您确定此位置始终是 {% data variables.product.prodname_copilot_short %} 操作的安全场所时，您才应选择第二个选项。

### 编辑受信任目录

您可以编辑永久信任目录的列表。可信目录存储在自动管理的`config.json`文件中。

1. 打开CLI的`config.json`文件。默认情况下，它存储在主目录下的 `.copilot` 文件夹中：

   * **macOS/Linux**: `~/.copilot/config.json`
   * **Windows**：`$HOME\.copilot\config.json`

您可以通过设置 `COPILOT_HOME` 环境变量来更改配置位置。这将更改上述路径中的 `~/.copilot/` 和 `$HOME\.copilot\`。

1. 编辑 `trustedFolders` 数组的内容。

## 设置允许的工具

您可以控制 {% data variables.copilot.copilot_cli_short %} 可以使用哪些工具，方法是在 {% data variables.product.prodname_copilot_short %} 尝试使用工具时响应批准提示，或者通过命令行标志指定权限。

请注意，允许工具访问会产生安全隐患，请参阅[安全注意事项](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>)。

在本节中，您可以学习如何：

* [首次允许工具](<#首次允许使用工具>)
* [允许在未经手动批准的情况下使用工具](<#允许无需手动批准即可使用工具>)
* [指定您要允许或拒绝哪个工具](<#指定您要允许或拒绝的工具>)
* [允许某些工具，同时拒绝其他工具](<#允许某些工具同时拒绝其他工具>)
* [限制可用工具](<#限制可用工具>)

### 首次允许使用工具

{% data variables.product.prodname_copilot_short %}第一次需要使用可能需要批准的工具-{% data reusables.cli.tools-needing-approval %}-它会询问您是否允许它运行。是否提示您可能取决于该工具及其使用方式（例如提供的参数或该工具之前是否已获得批准）。

1. 提示{% data variables.product.prodname_copilot_short %}执行需要工具的任务。例如：

   ```shell
   copilot -p "Create a new file called README.md with a project description"
   ```

1. 从以下三个选项之一中选择：

   * `1. Yes`

选择此选项可允许 {% data variables.product.prodname_copilot_short %} 运行此特定命令，仅限这一次。下次需要使用此工具时，它会再次询问您。

   * `2. Yes, and approve TOOL for the rest of the running session`

选择此选项可允许 {% data variables.product.prodname_copilot_short %} 在当前运行的会话期间使用此工具。它会在新的会话中再次请求您的批准，或者您将来是否恢复当前会话。如果您选择此选项，则表示您允许 {% data variables.product.prodname_copilot_short %} 以它认为合适的任何方式使用此工具。

例如，如果 {% data variables.product.prodname_copilot_short %} 要求您允许它运行命令 `rm ./this-file.txt`，并且您选择选项 2，则 {% data variables.product.prodname_copilot_short %} 可以在此会话的当前运行期间运行任何 `rm` 命令（例如 `rm -rf ./*`），而无需请求您的批准。

   * `3. No, and tell Copilot what to do differently (Esc)`

选择此选项可取消建议的命令并指示 {% data variables.product.prodname_copilot_short %} 尝试不同的方法。

### 允许无需手动批准即可使用工具

您可以使用命令行标志来指定 {% data variables.product.prodname_copilot_short %} 可以使用的工具，而无需征求您的批准。

#### 允许所有工具

使用`--allow-all-tools`允许{% data variables.product.prodname_copilot_short %}使用任何工具而无需征求您的批准。

* 例如：

  ```shell
  copilot -p "Revert the last commit" --allow-all-tools
  ```

#### 拒绝工具

使用`--deny-tool`可以防止{% data variables.product.prodname_copilot_short %}使用特定工具。

* 例如：

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

该选项优先于 `--allow-all-tools` 和 `--allow-tool` 选项。

#### 允许使用工具

使用`--allow-tool`允许{% data variables.product.prodname_copilot_short %}使用特定工具，而无需征求您的批准。

* 例如：

  ```shell
  copilot --allow-tool='shell'
  ```

### 指定您要允许或拒绝的工具

要使用 `--deny-tool` 和 `--allow-tool` 选项，您必须指定要允许或拒绝的工具类型：

* [Shell 命令](<#允许或拒绝-shell-命令>)
* [“写入”工具](<#允许或拒绝-write-工具>)
* [MCP 服务器工具](<#允许或拒绝-mcp-服务器工具>)

#### 允许或拒绝 shell 命令

使用 `shell(COMMAND)` 允许或拒绝特定的 shell 命令。

* 例如，要防止 {% data variables.product.prodname_copilot_short %} 使用任何 `rm` 命令，请使用：

  ```shell
  copilot --deny-tool='shell(rm)'
  ```

对于 `git` 和 `gh` 命令，指定要允许或拒绝的特定第一级子命令。

* 例如，要防止 {% data variables.product.prodname_copilot_short %} 使用 `git push`，请使用：

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

工具规格是可选的。例如，`copilot --allow-tool='shell'`允许{% data variables.product.prodname_copilot_short %}使用任何shell命令，无需单独批准。

#### 允许或拒绝 `'write'` 工具

使用 `'write'` 允许或拒绝工具（shell 命令除外）修改文件的权限。

* 例如，要允许 {% data variables.product.prodname_copilot_short %} 在未经您个人批准的情况下编辑文件，请使用：

  ```shell
  copilot --allow-tool='write'
  ```

#### 允许或拒绝 MCP 服务器工具

使用 `'MCP_SERVER_NAME'` 允许或拒绝来自指定 MCP 服务器的特定工具。

* 例如，要防止 {% data variables.product.prodname_copilot_short %} 使用名为 `My-MCP-Server` 的 MCP 服务器中名为 `tool_name` 的工具，请使用：

  ```shell
  copilot --deny-tool='My-MCP-Server(tool_name)'
  ```

`MCP_SERVER_NAME` 是您已配置的MCP 服务器的名称。

来自服务器的工具在括号中指定，使用在 MCP 服务器中注册的工具名称。

使用服务器名称而不指定工具将允许或拒绝来自该服务器的所有工具。

您可以通过在{% data variables.copilot.copilot_cli_short %}的交互模式中输入`/mcp`并从显示的列表中选择服务器来查找MCP服务器的名称。

### 允许某些工具，同时拒绝其他工具

要准确确定 {% data variables.product.prodname_copilot_short %} 可以使用哪些工具而不征求您的批准，您可以使用批准选项的组合。例如：

* 要防止 {% data variables.product.prodname_copilot_short %} 使用 `rm` 和 `git push` 命令，但自动允许所有其他工具，请使用：

  ```shell
  copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'
  ```

* 要防止 {% data variables.product.prodname_copilot_short %} 使用名为 `My-MCP-Server` 的 MCP 服务器中的工具 `tool_name`，但允许在未经个人批准的情况下使用该服务器中的所有其他工具，请使用：

  ```shell
  copilot --allow-tool='My-MCP-Server' --deny-tool='My-MCP-Server(tool_name)'
  ```

### 限制可用工具

要将 {% data variables.product.prodname_copilot_short %} 限制为一组特定的工具，请使用 `--available-tools`。

{% data variables.product.prodname_copilot_short %} 无法使用未包含在此列表中的工具。

## 设置路径权限

路径权限控制{% data variables.product.prodname_copilot_short %}可以访问哪些目录和文件。

默认情况下，{% data variables.copilot.copilot_cli_short %}可以访问当前工作目录、其子目录和系统临时目录。

路径权限适用于 shell 命令、文件操作（创建、编辑、查看）和搜索工具（例如 `grep` 和 glob 模式）。对于 shell 命令，通过对命令文本进行标记并识别看起来像路径的标记来启发式提取路径。

> [!WARNING]
> shell 命令的路径检测有限制：
>
> * 可能无法检测到嵌入复杂 shell 结构中的路径。
> * 仅扩展一组特定的环境变量（`HOME`、`TMPDIR`、`PWD` 等）。诸如 `$MY_PROJECT_DIR` 之类的自定义变量不会扩展，并且可能无法正确验证。
> * 符号链接会针对现有文件进行解析，但不会针对正在创建的文件进行解析。

### 允许访问所有路径

要禁用路径验证并允许访问任何路径，请在启动 {% data variables.copilot.copilot_cli_short %} 时使用 `--allow-all-paths` 标志。

### 禁止访问临时目录

要禁止访问临时目录，请使用 `--disallow-temp-dir`。

## 设置 URL 权限

URL 权限控制哪些外部 URL {% data variables.product.prodname_copilot_short %} 可以访问。默认情况下，所有 URL 都需要经过批准才能授予访问权限。

URL 权限适用于 `web_fetch` 工具和访问网络的 shell 命令的精选列表（例如 `curl`、`wget` 和 `fetch`）。对于 shell 命令，使用正则表达式模式提取 URL。

> [!WARNING]
> URL shell 命令的检测有限制：
>
> * 不会检测命令读取的文件内容、配置文件或环境变量中的 URL。
> * 可能无法检测到混淆的 URL（例如拆分字符串或转义序列）。
> * HTTP 和 HTTPS 被视为不同的协议，需要单独批准。

URL 权限可以在会话中保留或永久保留。

### 禁用 URL 验证

要禁用 URL 验证，请使用 `--allow-all-urls` 标志。

### 预先批准特定域

要预先批准特定域，请使用 `--allow-url=DOMAIN`。

* 例如，`--allow-url=github.com`。

### 拒绝特定域

要拒绝特定域，请使用 `--deny-url=DOMAIN`。

* 例如，`--deny-url=github.com`。

## 允许所有工具、路径和 URL

要允许所有工具、路径和 URL，请使用 `--allow-all` 或其别名 `--yolo`。

该标志结合了：
* `--allow-all-tools`（跳过工具审批）。
* `--allow-all-paths`（禁用路径验证）。
* `--allow-all-urls`（禁用URL验证）。

在交互式会话期间，您还可以使用 `/allow-all` 或 `/yolo` 斜杠命令启用所有权限。

> [!NOTE] {% data reusables.copilot.disable-bypass %}

## 限制对文件的访问

各种 `--allow-all...` 选项使您不必决定是否允许 {% data variables.product.prodname_copilot_short %} 访问单个工具、文件和 URL。然而，使用它们扩大了 {% data variables.product.prodname_copilot_short %} 执行您可能不希望它执行的操作的范围，例如，更改您正在使用的存储库之外的文件。

为了减轻这种风险，您可以指示 CLI 将其命令沙箱（本地沙箱），或者您可以在远程沙箱环境（云沙箱）中运行整个 {% data variables.copilot.copilot_cli_short %} 会话。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot>)
* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-config-dir-reference>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

{% data variables.copilot.copilot_cli_short %} has several configuration options that control what it can access and do on your behalf.

This article shows you how to set trusted directories, configure access for tools, and grant permissions to file paths and URLs.

### Prerequisites

* Install the {% data variables.copilot.copilot_cli_short %}. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli>).

## Setting trusted directories

Trusted directories control where {% data variables.copilot.copilot_cli_short %} can read, modify, and execute files. Trusting a directory has security implications, see [Security considerations](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>).

### Choosing to trust a directory

When you start a {% data variables.copilot.copilot_cli %} session, you'll be asked to confirm that you trust the files in, and below, the directory from which you launched the CLI.

You can choose to trust the current directory for:

* The currently running session only
* This and future sessions

If you choose to trust the directory for future sessions, the trusted directory prompt will not be displayed again. You should only choose this second option if you are sure that this location will always be a safe place for {% data variables.product.prodname_copilot_short %} to operate.

### Editing trusted directories

You can edit the list of permanently trusted directories. Trusted directories are stored in the automatically managed `config.json` file.

1. Open the CLI's `config.json` file. By default, it's stored in a `.copilot` folder under your home directory:

   * **macOS/Linux**: `~/.copilot/config.json`
   * **Windows**: `$HOME\.copilot\config.json`

  You can change the config location by setting the `COPILOT_HOME` environment variable. This would change `~/.copilot/` and `$HOME\.copilot\` in the above paths.

1. Edit the contents of the `trustedFolders` array.

## Setting allowed tools

You can control which tools {% data variables.copilot.copilot_cli_short %} can use, either by responding to approval prompts when {% data variables.product.prodname_copilot_short %} attempts to use a tool, or by specifying permissions via command-line flags.

Be aware that allowing tool access has security implications, see [Security considerations](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>).

In this section, you can learn how to:

* [Allow a tool for the first time](<#allowing-a-tool-for-the-first-time>)
* [Allow tools to be used without manual approval](<#allowing-tools-to-be-used-without-manual-approval>)
* [Specify which tool you want to allow or deny](<#specifying-which-tool-you-want-to-allow-or-deny>)
* [Allow some tools while denying others](<#allowing-some-tools-while-denying-others>)
* [Limit available tools](<#limiting-available-tools>)

### Allowing a tool for the first time

The first time that {% data variables.product.prodname_copilot_short %} needs to use a tool that may require approval—{% data reusables.cli.tools-needing-approval %}—it will ask you whether you want to allow it to run. Whether you’re prompted can depend on the tool and how it’s being used (such as the arguments provided or whether the tool has been previously approved).

1. Prompt {% data variables.product.prodname_copilot_short %} to perform a task that requires a tool. For example:

   ```shell
   copilot -p "Create a new file called README.md with a project description"
   ```

1. Choose from one of the three options:

   * `1. Yes`

      Choose this option to allow {% data variables.product.prodname_copilot_short %} to run this particular command, this time only. The next time it needs to use this tool, it will ask you again.

   * `2. Yes, and approve TOOL for the rest of the running session`

      Choose this option to allow {% data variables.product.prodname_copilot_short %} to use this tool for the duration of the currently running session. It will ask for your approval again in new sessions, or if you resume the current session in the future. If you choose this option, you are allowing {% data variables.product.prodname_copilot_short %} to use this tool in any way it thinks is appropriate.

      For example, if {% data variables.product.prodname_copilot_short %} asks you to allow it to run the command `rm ./this-file.txt`, and you choose option 2, then {% data variables.product.prodname_copilot_short %} can run any `rm` command (for example, `rm -rf ./*`) during the current run of this session, without asking for your approval.

   * `3. No, and tell Copilot what to do differently (Esc)`

      Choose this option to cancel the proposed command and instruct {% data variables.product.prodname_copilot_short %} to try a different approach.

### Allowing tools to be used without manual approval

You can use command-line flags to designate tools that {% data variables.product.prodname_copilot_short %} can use without asking for your approval.

#### Allowing all tools

Use the `--allow-all-tools` to allow {% data variables.product.prodname_copilot_short %} to use any tool without asking for your approval.

* For example:

  ```shell
  copilot -p "Revert the last commit" --allow-all-tools
  ```

#### Denying a tool

Use `--deny-tool` to prevent {% data variables.product.prodname_copilot_short %} from using a specific tool.

* For example:

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

This option takes precedence over the `--allow-all-tools` and `--allow-tool` options.

#### Allowing a tool

Use `--allow-tool` to allow {% data variables.product.prodname_copilot_short %} to use a specific tool without asking for your approval.

* For example:

  ```shell
  copilot --allow-tool='shell'
  ```

### Specifying which tool you want to allow or deny

To use the `--deny-tool` and `--allow-tool` options, you must specify what type of tool you want to allow or deny:

* [Shell commands](<#allowing-or-denying-shell-commands>)
* ['Write' tools](<#allowing-or-denying-write-tools>)
* [MCP server tools](<#allowing-or-denying-mcp-server-tools>)

#### Allowing or denying shell commands

Use `shell(COMMAND)` to allow or deny a specific shell command.

* For example, to prevent {% data variables.product.prodname_copilot_short %} from using any `rm` command, use:

  ```shell
  copilot --deny-tool='shell(rm)'
  ```

For `git` and `gh` commands, specify a particular first-level subcommand to allow or deny.

* For example, to prevent {% data variables.product.prodname_copilot_short %} from using `git push`, use:

  ```shell
  copilot --deny-tool='shell(git push)'
  ```

The tool specification is optional. For example, `copilot --allow-tool='shell'` allows {% data variables.product.prodname_copilot_short %} to use any shell command without individual approval.

#### Allowing or denying `'write'` tools

Use `'write'` to allow or deny tools—other than shell commands—permission to modify files.

* For example, to allow {% data variables.product.prodname_copilot_short %} to edit files without your individual approval, use:

  ```shell
  copilot --allow-tool='write'
  ```

#### Allowing or denying MCP server tools

Use `'MCP_SERVER_NAME'` to allow or deny a specific tool from the specified MCP server.

* For example, to prevent {% data variables.product.prodname_copilot_short %} from using the tool called `tool_name` from the MCP server called `My-MCP-Server`, use:

  ```shell
  copilot --deny-tool='My-MCP-Server(tool_name)'
  ```

`MCP_SERVER_NAME` is the name of an MCP server that you have configured.

Tools from the server are specified in parentheses, using the tool name that is registered with the MCP server.

Using the server name without specifying a tool allows or denies all tools from that server.

You can find an MCP server's name by entering `/mcp` in the interactive mode of {% data variables.copilot.copilot_cli_short %} and selecting the server from the list that's displayed.

### Allowing some tools while denying others

To determine exactly which tools {% data variables.product.prodname_copilot_short %} can use without asking for your approval, you can use a combination of approval options. For example:

* To prevent {% data variables.product.prodname_copilot_short %} from using the `rm` and `git push` commands, but automatically allow all other tools, use:

  ```shell
  copilot --allow-all-tools --deny-tool='shell(rm)' --deny-tool='shell(git push)'
  ```

* To prevent {% data variables.product.prodname_copilot_short %} from using the tool `tool_name` from the MCP server named `My-MCP-Server`, but allow all other tools from that server to be used without individual approval, use:

  ```shell
  copilot --allow-tool='My-MCP-Server' --deny-tool='My-MCP-Server(tool_name)'
  ```

### Limiting available tools

To restrict {% data variables.product.prodname_copilot_short %} to a specific set of tools, use `--available-tools`.

Tools not included in this list will not be available to {% data variables.product.prodname_copilot_short %}.

## Setting path permissions

Path permissions control which directories and files {% data variables.product.prodname_copilot_short %} can access.

By default, {% data variables.copilot.copilot_cli_short %} can access the current working directory, its subdirectories, and the system temp directory.

Path permissions apply to shell commands, file operations (create, edit, view), and search tools (such as `grep` and glob patterns). For shell commands, paths are heuristically extracted by tokenizing command text and identifying tokens that look like paths.

> [!WARNING]
> Path detection for shell commands has limitations:
>
> * Paths embedded in complex shell constructs may not be detected.
> * Only a specific set of environment variables are expanded (`HOME`, `TMPDIR`, `PWD`, and similar). Custom variables like `$MY_PROJECT_DIR` are not expanded and may not be validated correctly.
> * Symlinks are resolved for existing files, but not for files being created.

### Allowing access to all paths

To disable path verification and allow access to any path, use the `--allow-all-paths` flag when starting {% data variables.copilot.copilot_cli_short %}.

### Disallowing access to the temp directory

To disallow access to the temp directory, use `--disallow-temp-dir`.

## Setting URL permissions

URL permissions control which external URLs {% data variables.product.prodname_copilot_short %} can access. By default, all URLs require approval before access is granted.

URL permissions apply to the `web_fetch` tool and a curated list of shell commands that access the network (such as `curl`, `wget`, and `fetch`). For shell commands, URLs are extracted using regex patterns.

> [!WARNING]
> URL detection for shell commands has limitations:
>
> * URLs in file contents, config files, or environment variables read by commands are not detected.
> * Obfuscated URLs (such as split strings or escape sequences) may not be detected.
> * HTTP and HTTPS are treated as different protocols and require separate approval.

URL permissions can be persisted for the session or permanently.

### Disabling URL verification

To disable URL verification, use the `--allow-all-urls` flag.

### Pre-approving specific domains

To pre-approve specific domains, use `--allow-url=DOMAIN`.

* For example, `--allow-url=github.com`.

### Denying specific domains

To deny specific domains, use `--deny-url=DOMAIN`.

* For example, `--deny-url=github.com`.

## Allowing all tools, paths, and URLs

To allow all tools, paths and URLs, use `--allow-all`, or its alias, `--yolo`.

This flag combines:
* `--allow-all-tools` (skip tool approval).
* `--allow-all-paths` (disable path verification).
* `--allow-all-urls` (disables URL verification).

During an interactive session, you can also enable all permissions with the `/allow-all` or `/yolo` slash commands.

> [!NOTE] {% data reusables.copilot.disable-bypass %}

## Restrict access to files

The various `--allow-all...` options save you from having to decide whether to allow {% data variables.product.prodname_copilot_short %} to access individual tools, files, and URLs. However, using them widens the scope for {% data variables.product.prodname_copilot_short %} to perform actions that you might not want it to—for example, altering files outside of the repository you are working in.

To mitigate this risk, you can instruct the CLI to sandbox its commands (local sandboxing), or you can run the entire {% data variables.copilot.copilot_cli_short %} session within a remote, sandboxed environment (cloud sandboxing). For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/about-cloud-and-local-sandboxes>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot>)
* [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-config-dir-reference>)


</details>
