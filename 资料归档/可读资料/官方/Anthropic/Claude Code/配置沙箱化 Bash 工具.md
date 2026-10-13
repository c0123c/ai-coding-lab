# 配置沙箱化 Bash 工具

说明如何限制命令可访问的文件与网络，启用并验证沙箱、配置边界、处理依赖和故障，同时列出未受沙箱保护的行为。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://code.claude.com/docs/en/sandboxing>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/sandboxing.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/配置沙箱化 Bash 工具.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 配置沙盒 Bash 工具

> 限制文件和网络主机Claude Code的shell命令可以通过内置沙箱到达。打开它，设置边界，并修复它破坏的部分。

Bash 沙箱是操作系统围绕在您的计算机上运行的 shell 命令 Claude 强制实施的边界。您可以设置这些命令可以访问哪些文件和网络域，并且这些限制适用于 Bash、PowerShell 和 Monitor 命令及其启动的进程。由于操作系统在命令运行时应用限制，因此 Claude Code 可以 [ 运行沙盒命令，而无需要求您 ](<#沙盒模式>) 批准每个命令。

沙箱仅涵盖 shell 命令。 Claude 的文件工具、MCP 服务器和挂钩 [ 在其外部运行 ](<#什么在沙箱之外运行>)。

沙箱在 macOS、Linux 和 WSL2 上运行。在本机 Windows 上，Claude Code 运行非沙盒命令。要在 Windows 计算机上使用沙箱，请在 WSL2 发行版中运行 Claude Code。



**注释：**

本页介绍了您自己的计算机上围绕 shell 命令的沙箱。其他页面涵盖相关问题：

  * 云会话隔离方式请参见[安全与隔离](<https://code.claude.com/docs/en/claude-code-on-the-web#security-and-isolation>)
  * 要比较其他隔离方法（例如开发容器、自定义容器和虚拟机），请参阅[沙箱环境](<https://code.claude.com/docs/en/sandbox-environments>)
  * 要减少 Bash 以外的工具的权限提示，请参阅[权限模式](<https://code.claude.com/docs/en/permission-modes>)



## 沙箱限制什么

当沙箱打开时，shell 命令 Claude 运行在其边界内启动，它们启动的进程也是如此。沙箱默认处于关闭状态。要打开它，请运行 `/sandbox` 在一个会话中，如 [开始使用](<#开始使用>) 显示或设置 [`sandbox.enabled`](<https://code.claude.com/docs/en/settings-reference#sandbox-enabled>) 到 `true` 在一个 [设置文件](<https://code.claude.com/docs/en/settings>) 比如 `~/.claude/settings.json`.

该表显示了沙盒命令默认情况下可以达到的范围以及更改每个默认值的设置。

|访问|默认|改变它与|
| :- | :- | :- |
|写|工作目录、每用户临时目录以及您添加的 [ 目录 ](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>)。 [受保护路径](<#受保护的路径>)保持拒绝写入状态| [`filesystem.allowWrite`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-allowwrite>), [`filesystem.denyWrite`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-denywrite>) |
|读|大多数机器，包括凭证文件，例如 `~/.ssh` 和 `~/.aws/credentials`| [`filesystem.denyRead`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-denyread>), [`credentials`](<#保护凭证>) |
|网络|没有直接出去的路线。连接通过您计算机上的代理，该代理根据您允许的域检查每个主机，这些域从空开始。你的权限模式决定[其他主机会发生什么](<#主机位于您允许的域之外>)| [`network.allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>), [`network.deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) |
|环境变量|继承自Claude Code，包括其环境中的任何秘密| [`credentials`](<#保护凭证>), [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>) |

Claude Code 在开源 [`@anthropic-ai/sandbox-runtime`](<https://github.com/anthropics/sandbox-runtime>) 包上构建沙箱。

### 什么在沙箱之外运行

沙箱包装 shell 命令。这些工具和流程在其外部运行：

* **内置文件和网页工具**：读取、编辑、写入、WebFetch和WebSearch等工具改为遵循[权限规则](<https://code.claude.com/docs/en/permissions>)。 `denyRead` 条目不会停止读取工具，并且 `allowedDomains` 不会限制 WebFetch
* **其他进程Claude Code启动**：命令[挂钩](<https://code.claude.com/docs/en/hooks>)，本地[MCP服务器](<https://code.claude.com/docs/en/mcp>)，[插件监视器](<https://code.claude.com/docs/en/plugins/components#monitors>)、[LSP服务器](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>)和辅助命令，例如[状态行](<https://code.claude.com/docs/en/statusline>)命令和`apiKeyHelper`以完全访问权限运行

一些 shell 命令也会在沙箱之外运行，具体取决于您的设置：

* **您自己键入的命令**：您在[`!` shell 模式提示符下输入的命令](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) 在大多数会话中以非沙箱方式运行。 [严格沙盒模式](<#用严格沙箱模式关闭重试>)列出了您键入的命令在沙盒中运行的会话
* **排除的命令**：与[`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>)匹配的命令在未沙盒的情况下运行
* **未沙盒重试**：Claude可以[要求运行未沙盒的命令](<#非沙盒重试逃生舱口>)，通常是在沙盒中失败之后

要将本节中的工具、进程和命令置于一个边界之后，请在 [ 容器、虚拟机或沙箱运行时 ](<https://code.claude.com/docs/en/sandbox-environments>) 中运行 Claude Code 进程本身。

## 开始使用

沙箱内置于Claude Code中。您安装的内容取决于您的平台：

* **macOS**：沙盒使用内置的Seatbelt框架，因此您可以直接进入步骤
* **Linux 和 WSL2**：沙箱依赖于 `bubblewrap` 和 `socat`，在 [设置 Linux 和 WSL2](<#设置-linux-和-wsl2>) 中介绍。即使你还没有安装它们，你也可以从`/sandbox`开始，因为它的面板会显示是否缺少任何东西



  

#### 运行/sandbox

启动 Claude Code 会话并运行 `/sandbox` 命令：

    ```text theme={null}
    /sandbox
    ```

这将打开带有三个选项卡的沙箱面板，当缺少可选的 seccomp 过滤器时，还会在 Linux 上打开一个“依赖项”选项卡：

    * **模式**：选择如何批准沙盒命令，将在下一步中介绍
    * **覆盖**：选择在沙箱下失败的命令是否可以回退到未沙箱运行。这是[`allowUnsandboxedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowunsandboxedcommands>)设置
    * **Config**：查看解析的沙箱设置

如果面板仅显示“依赖关系”选项卡，则说明缺少所需的包。按照[设置Linux和WSL2](<#设置-linux-和-wsl2>)中的说明进行安装，重新启动Claude Code，然后再次运行`/sandbox`。
  


  

#### 选择一种模式

在模式选项卡上，选择自动允许或常规权限。自动允许在不提示的情况下运行沙盒命令，即使命令被沙盒化，常规权限也会保留常规权限提示。请参阅 [ 沙箱模式 ](<#沙盒模式>)，了解哪些命令在自动允许模式下仍会提示。
  


  

#### 运行 Bash 命令

要求 Claude 运行命令，例如构建或测试套件。默认情况下，沙箱内的命令可以写入工作目录、[每用户临时目录](<https://code.claude.com/docs/en/env-vars>)以及您使用`--add-dir`、`/add-dir`添加的任何[目录](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>)，或`permissions.additionalDirectories`。

    命令第一次需要新的网络域时， Claude Code 提示批准；在 [自动模式](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), Claude 而是命名命令所需的主机 [在命令本身上](<#自动模式下每个命令允许的域>) 以便分类器用它来审查。

要扩大或缩小沙箱允许的范围，请参阅[配置沙箱](<#配置沙箱>)。

如果沙盒命令在容器内失败并显示 `Operation not permitted`，请参阅 [Bubblewrap 无法在容器内启动 ](<#bubblewrap-无法在容器内启动>)。
  


当您在面板中选择模式时，Claude Code 会将其保存到项目的本地设置 `.claude/settings.local.json` 中，该设置适用于当前项目。 Claude Code 在保存设置时将该文件添加到全局 gitignore 中。要在所有项目中启用沙箱，请在 `~/.claude/settings.json` 的用户设置中将 [`sandbox.enabled`](<https://code.claude.com/docs/en/settings-reference#sandbox-enabled>) 设置为 `true`。要为组织中的每个开发人员强制实施沙箱，请使用[托管设置](<#使用托管设置强制执行沙箱>)。

要更改一个会话的沙箱而不写入设置文件，请使用 [`--settings`](<https://code.claude.com/docs/en/settings#change-a-setting-for-one-session>) 启动 Claude Code。例如，此命令启动一个沙盒会话，其中 Claude 无法在沙盒外重试被阻止的命令：

```bash theme={null}
claude --settings '{"sandbox": {"enabled": true, "allowUnsandboxedCommands": false}}'
```



**注意：**

默认情况下，如果沙箱由于缺少依赖项或平台不受支持而无法启动，Claude Code 会在没有沙箱的情况下运行命令。要使 Claude Code 在启动时退出，请将 [`sandbox.failIfUnavailable`](<https://code.claude.com/docs/en/settings-reference#sandbox-failifunavailable>) 设置为 `true`。需要沙箱作为安全门的托管部署可以使用此设置。


### 确认命令在沙箱内运行

要检查沙箱是否正常工作，请要求 Claude 运行表中的每一行。您在 [`!` 提示符 ](<#什么在沙箱之外运行>) 中键入的内容通常在沙箱之外运行，因此您自己键入一行并不会对其进行测试。

|命令|沙箱内的结果|
| :- | :- |
| `touch ~/sandbox-probe` |失败，macOS 上为 `Operation not permitted`，或 Linux 上为 `Read-only file system` 和 WSL2|
| `curl --noproxy '*' https://example.com` |失败并显示 `Could not resolve host`，因为该命令没有绕过沙箱代理的路由|

如果 Claude 要求在沙箱外重试失败的命令，请拒绝重试。如果 `touch` 成功并且您的主目录不是沙箱允许命令写入的目录之一，请删除 `~/sandbox-probe`。然后运行`/sandbox`来检查沙箱是否已打开并且其依赖项是否已安装。

### 设置 Linux 和 WSL2

在 Linux 和 WSL2 上，沙箱依赖于这些软件包：

* [`bubblewrap`](<https://github.com/containers/bubblewrap>)：强制文件系统隔离的非特权沙箱工具
* [`socat`](<http://www.dest-unreach.org/socat/>)：用于通过沙箱代理路由网络流量的中继

使用您的发行版的包管理器安装它们：



  

#### Ubuntu/Debian

    ```bash theme={null}
    sudo apt-get install bubblewrap socat
    ```
  


  

#### 软呢帽

    ```bash theme={null}
    sudo dnf install bubblewrap socat
    ```
  


当缺少依赖项时，`/sandbox` 中的“依赖项”选项卡会列出您的平台缺少的 `ripgrep`、`bubblewrap`、`socat` 和 seccomp 过滤器。如果在安装并重新启动 Claude Code 后没有看到该选项卡，则所有依赖项都存在。

Ripgrep 与本机 Claude Code 二进制文件捆绑在一起。 seccomp 过滤器是可选的，它添加了 Unix 域套接字阻塞。如果缺少，请使用 `npm install -g @anthropic-ai/sandbox-runtime` 安装。

当缺少所需的依赖项时，“依赖项”选项卡是唯一显示的选项卡，直到您安装它。当仅缺少可选的 seccomp 过滤器时，“依赖关系”选项卡将与其他选项卡一起显示。依赖性检查在启动时运行，因此在安装 `/sandbox` 的软件包后重新启动 Claude Code 以检测它们。



  

#### Ubuntu 24.04 及更高版本：允许 bubblewrap 创建用户命名空间

在 Ubuntu 24.04 及更高版本上，默认 AppArmor 策略会阻止 bubblewrap 创建隔离所需的用户命名空间。

要检查您的环境（包括 WSL2 内部）是否强制执行此限制，请运行 `sysctl kernel.apparmor_restrict_unprivileged_userns`。如果命令返回`0`，则跳过此步骤。如果打印 `No such file or directory` 错误，则该密钥不存在，您可以跳过此步骤。如果它返回 `1`，请添加授予 `bwrap` 此功能的 AppArmor 配置文件：

    ```bash theme={null}
    sudo tee /etc/apparmor.d/bwrap > /dev/null <<'EOF'
    abi <abi/4.0>,
    include <tunables/global>

    profile bwrap /usr/bin/bwrap flags=(unconfined) {
      userns,
      include if exists <local/bwrap>
    }
    EOF
    ```

该配置文件仅适用于 `bwrap` 本身，不适用于它在沙箱内运行的命令。重新加载 AppArmor 以应用它：

    ```bash theme={null}
    sudo systemctl reload apparmor
    ```
  


  

#### WSL2 注释

使用 PowerShell 中的 `wsl -l -v` 检查您的 WSL 版本。如果您看到 `Sandboxing requires WSL2`，则您的发行版正在运行 WSL1。将其升级到WSL2或在没有沙箱的情况下运行Claude Code。

在 WSL2 上，WSL 通过 Unix 套接字将 Windows 二进制文件（例如 `cmd.exe`、`powershell.exe` 或 `/mnt/c/` 下的任何内容）的启动交给 Windows 主机，因此沙盒命令是否可以启动一个遵循沙盒的命令[Unix套接字设置](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowunixsockets>)：必须安装可选的seccomp过滤器以首先阻止套接字。要允许这些启动，请设置 `allowAllUnixSockets`，这将向沙盒命令打开每个 Unix 套接字。
  


### 沙盒模式

Claude Code提供两种沙箱模式。在这两种情况下，沙箱都强制执行相同的文件系统和网络限制；区别仅在于沙盒命令是自动批准还是需要显式许可。

#### 自动允许模式

当命令在沙箱内运行时，Claude Code 会自动批准命令，无需提示。当命令在沙箱外部运行时，它会通过常规 [ 权限流 ](<https://code.claude.com/docs/en/permissions>)，因为它与 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>) 匹配，或者因为 Claude [ 重试取消沙箱操作 ](<#非沙盒重试逃生舱口>)。

连接到您不允许的主机的沙盒命令会保留在沙盒中。 [您允许的域之外的主机](<#主机位于您允许的域之外>)涵盖由谁决定连接是否通过。

即使在自动允许模式下，以下规则仍然适用：

* 始终遵守显式 [ 拒绝规则 ](<https://code.claude.com/docs/en/permissions>)
* 针对 [ 关键路径 ](<https://code.claude.com/docs/en/permission-modes#critical-paths>) 的 `rm` 或 `rmdir` 命令仍会经过常规权限流程
* 内容范围的 [ 询问规则 ](<https://code.claude.com/docs/en/permissions>) 像 `Bash(git push *)` 仍然强制提示，即使对于沙盒命令也是如此
* 对于运行沙盒的命令，会跳过裸露的 `Bash` 询问规则或等效的 `Bash(*)` 形式；它仍然适用于回退到常规权限流程的命令。在 [ 计划模式 ](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) 中，不会跳过该规则：它也会提示输入沙盒命令，包括只读命令



**说明：**

自动允许模式独立于您的权限模式设置工作，但有以下三个例外：[计划模式](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>)，这是一个自动模式命令，其中包含[每个命令允许的域](<#自动模式下每个命令允许的域>)，以及沙盒的[服务器端分类器审查](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>)自动模式下的命令。即使您未处于“接受编辑”模式，沙盒中的 Bash 命令也会在启用自动允许时自动运行。这意味着修改沙箱边界内的文件的 Bash 命令会在没有提示的情况下执行，即使在文件编辑工具会提示的手动模式下也是如此。

在计划模式下，自动允许不会扩大批准范围；请参阅 [ 计划模式 ](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) 了解 Claude Code 在计划时如何控制命令。


#### 常规权限模式

所有 Bash 命令都会经过常规权限流程，即使是在沙盒中也是如此。这提供了更多控制，但需要更多批准。

#### 非沙盒重试逃生舱口

非沙盒重试是沙盒内失败的命令（例如与其不兼容的工具）的逃生口。当沙箱阻止网络连接时，Claude Code 在命令结果中命名被拒绝的主机，因此 Claude 可以看到被阻止的内容。 Claude 分析失败并可以使用 `dangerouslyDisableSandbox` 参数重试该命令。

重试的命令在未沙盒的情况下运行。在交互式终端会话中，谁批准取决于您的权限模式：

* **`bypassPermissions`模式**：重试运行而不提示
* **手动模式和`acceptEdits`模式**：您会收到标题为“Bash command (unsandboxed)”的提示
* **[自动模式](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)**：单独的分类器模型评估底层命令
* **`dontAsk`模式**：Claude Code拒绝重试
* **计划模式**：请参阅[在计划时Claude Code如何控制命令](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>)

这些规则和设置更改批准重试的人员：

* **匹配的允许规则**：如果诸如`Bash(curl *)`之类的允许规则与该命令匹配，它也会批准重试，因此该命令在沙箱外运行且没有提示
* **参数**的询问规则：添加[询问规则](<https://code.claude.com/docs/en/permissions#match-by-input-parameter>)，以便在Bash重试时提示`Bash(dangerouslyDisableSandbox:true)`。您也会在自动模式和 `bypassPermissions` 模式下收到提示，并且该规则优先于匹配的允许规则
* **[`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>)**：[无模式自动批准的操作](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>)涵盖打开时提示的重试

#### 用严格沙箱模式关闭重试

您可以通过在[沙箱设置](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>)中设置`"allowUnsandboxedCommands": false`来禁用非沙箱重试。禁用重试后，Claude Code 将忽略 `dangerouslyDisableSandbox` 参数。当沙箱运行时，命令 Claude 运行将被沙箱化，除非它们与 `excludedCommands` 条目匹配。要防止 Claude Code 在沙箱无法启动时运行未沙箱化的命令，还需设置 [`failIfUnavailable`](<https://code.claude.com/docs/en/settings-reference#sandbox-failifunavailable>)。 `/sandbox` **覆盖**选项卡将此设置显示为**严格沙箱模式**。

即使项目设置设置为 `true`，用户设置、`--settings` 或托管设置中的 `false` 也会保留。用户设置中的 `false` 不会使沙箱成为管理员必需的，因此项目的其他沙箱设置仍然适用。在 v2.1.285 之前，项目的 `true` 会覆盖用户设置中的 `false`。

如果您或您的管理员在托管设置中或使用 `--settings` 标志禁用重试，则沙箱将成为管理员必需的。 Claude Code 然后忽略存储库文件中放松沙箱的设置，包括 `excludedCommands` 条目。 [管理员所需的沙箱下的存储库设置](<#管理员所需的沙箱下的存储库设置>)列出了它们。

严格沙箱模式适用于Claude运行的命令。您在 [`!` shell 模式提示符 ](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) 处键入的命令在沙箱外部运行，除非会话是以下之一：

* **A [后台会话](<https://code.claude.com/docs/en/agent-view>)**：严格的沙箱模式也涵盖 shell 模式命令
* **具有[`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars#variables>)集**的Linux会话：每个命令都运行沙盒，包括shell模式命令

在 v2.1.260 之前，严格的沙箱模式在每个会话中沙箱 shell 模式命令。

#### 临时目录

默认情况下，每个用户的临时目录与工作目录一起在沙箱内是可写的。除非您[禁用文件系统隔离](<#禁用文件系统隔离>)，Claude Code将`$TMPDIR`设置为此目录以用于沙盒命令，因此写入临时文件的工具无需额外配置即可工作。

设置未沙盒的命令会继承 shell 的 `$TMPDIR`，因此当文件系统隔离打开时，沙盒和未沙盒的命令会将 `$TMPDIR` 解析为不同的目录。如果您的 shell 未设置 `$TMPDIR` 或为空，则引用 `$TMPDIR` 的未沙盒命令会收到您的 [`CLAUDE_CODE_TMPDIR`](<https://code.claude.com/docs/en/env-vars>) 覆盖，或者当您未设置或覆盖是长路径时操作系统的临时目录，因此该变量不会扩展为空字符串。要在两者之间传递临时文件，请将它们写入工作目录下。

## 配置沙箱

通过您的 `settings.json` 文件自定义沙箱行为。请参阅[设置](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>)以获取完整的配置参考。

默认情况下，沙盒命令可以写入当前工作目录、每个用户的临时目录以及任何 [您添加的目录](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>) 与 `--add-dir`, `/add-dir`, 或 `permissions.additionalDirectories`。如果子进程命令像 `kubectl`, `terraform`, 或 `npm` 需要在这些目录之外写入，请使用 `sandbox.filesystem.allowWrite` 授予对特定路径的访问权限：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "allowWrite": ["~/.kube", "/tmp/build"]
    }
  }
}
```

这些路径是在 OS 级别强制执行的，因此沙箱内运行的所有命令（包括其子进程）都会遵守它们。当工具需要对特定位置进行写访问时，这是推荐的方法，而不是使用 `excludedCommands` 将工具完全从沙箱中排除。

当您在多个 [ 设置范围 ](<https://code.claude.com/docs/en/settings#settings-precedence>) 中定义相同的文件系统数组时，Claude Code 会合并它们，合并路径，而不是用一个范围的数组替换另一个范围的数组。当 [ 阻止开发人员扩大策略 ](<#阻止开发商扩大政策范围>) 下的锁覆盖它时，Claude Code 会在合并中留下一个条目。

如果您排除来源 [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference>) 于 CLI 或 [`settingSources`](<https://code.claude.com/docs/en/agent-sdk/claude-code-features#control-filesystem-settings-with-settingsources>) 在 Agent SDK, Claude Code 忽略它的 `sandbox.filesystem` 条目，其 `Edit` 权限规则及其 `Read` 构建沙箱配置时拒绝规则。需要 Claude Code v2.1.246 或稍后。

当您在会话期间编辑这些文件系统列表时，Claude Code [ 会将更改应用到正在运行的会话 ](<https://code.claude.com/docs/en/settings#when-edits-take-effect>)，以便下一个沙盒命令在新路径下运行。

沙盒文件系统路径使用标准约定：`/tmp/build` 是绝对路径，`~/.kube` 是相对于您的主目录的路径。这与 [读取和编辑权限规则 ](<https://code.claude.com/docs/en/permissions#read-and-edit>) 不同，后者使用 `//path` 表示绝对权限，使用 `/path` 表示项目相对权限。有关相对路径、尾部斜杠和通配符，请参阅 [沙箱路径前缀 ](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>)。

您还可以使用 `sandbox.filesystem.denyWrite` 和 `sandbox.filesystem.denyRead` 拒绝写入或读取访问，并使用 `sandbox.filesystem.allowRead` 重新允许被拒绝区域内的特定路径。当读取规则重叠时，应用路径较窄的规则：

|规则示例|结果|
| :- | :- |
|`"denyRead": ["~/"]` 与 `"allowRead": ["~/projects"]`|`~/projects` 可读，主目录的其余部分保持阻止状态。较窄的允许重新开放被拒绝区域的那部分|
|`"allowRead": ["~/"]` 与 `"denyRead": ["~/.env"]`|`~/.env` 保持阻塞状态，主目录的其余部分可读。拒绝包含在更广泛的允许中，因此广泛的允许不能默默地重新暴露秘密|
|`"allowRead": ["~/"]` 与 `"denyRead": ["~/**/.env"]`|主目录下的每个 `.env` 保持阻塞状态，其余部分可读。 [通配符拒绝](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>)以与精确路径相同的方式保留在更宽的允许范围内|

下面的示例阻止从整个主目录读取，但仍然允许从当前项目读取。将其放置在项目的 `.claude/settings.json` 中，因为当配置位于项目设置中时，相对路径 `.` 解析为项目根目录：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "denyRead": ["~/"],
      "allowRead": ["."]
    }
  }
}
```

如果您在 `~/.claude/settings.json` 中放置相同的配置，`.` 将解析为 `~/.claude`，并且项目文件将继续受到 `denyRead` 规则的阻止。

要拒绝沙盒命令对主目录和已安装卷的读取访问权限，同时保持工作目录可读，请设置 [`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>) 而不是写入路径规则。

### 使用 `excludedCommands` 在沙箱外运行命令

在 [`sandbox.excludedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>) 中列出命令模式以在沙箱外运行匹配的命令，这意味着没有文件系统限制，也没有网络代理。将其用于无法在沙箱内工作且您信任且具有完全访问权限的工具。需要多个目录或多台主机的工具可以与 `allowWrite` 或 `allowedDomains` 一起使用，这会使命令保持沙箱状态。

此示例从沙箱中取出 `docker compose` 命令。将其保存在 `~/.claude/settings.json` 中以将其应用到您的所有项目：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "excludedCommands": ["docker compose *"]
  }
}
```

Claude Code 根据每个 Bash 和 Monitor 调用检查您的条目。调用是发送整个命令行 Claude，它可以链接多个命令。以下规则决定调用是否离开沙箱：

* **以 ` *`** 结束模式：条目使用与 `Bash(...)` [ 权限规则 ](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>) 相同的语法，其中没有通配符的模式是完全匹配的。 `docker` 仅匹配不带参数的 `docker`。 `docker *` 与带或不带参数的 `docker` 匹配
* **调用中的每个命令都必须匹配**：`npm ci && docker compose build`保持沙箱状态，除非另一个条目覆盖`npm ci`
* **Claude Code 与调用的文本** 匹配：内部调用`docker` 的脚本或`make` 目标不匹配，`/usr/local/bin/docker` 也不匹配
* **某些调用保持沙箱状态**：重定向到文件、`cd`或命令替换（例如`$(...)`）使整个调用保持沙箱状态。 [参考条目](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>)列出了更多保持沙盒状态的调用
* **保存条目的位置可能很重要**：虽然沙箱是[需要管理员](<#管理员所需的沙箱下的存储库设置>)，但Claude Code会忽略`.claude/settings.json`和`.claude/settings.local.json`中的条目

排除的命令会经历常规权限流程：

* [只读命令](<https://code.claude.com/docs/en/permissions#read-only-commands>)和允许规则覆盖的命令在没有提示的情况下运行
* 在自动模式下，分类器会检查其他排除的命令
* 在 `bypassPermissions` 模式下，排除的命令在没有提示的情况下运行，除非询问规则与其匹配

要确认条目匹配，请切换到手动模式并要求 Claude 运行更改某些内容的匹配命令，例如 `docker compose up -d`。权限提示的标题是“Bash 命令（未沙盒化）”。



**注意：**

排除的命令以您的完全访问权限运行。诸如 `docker *` 之类的广泛条目涵盖了该工具可以执行的所有操作。如果您编写一个涵盖解释器、工作目录中的脚本或作用于其中文件的工具的模式，就像 `docker compose` 对其撰写文件所做的那样，Claude 可以写入该文件，然后在沙箱外运行它。较窄的模式使得 Claude 可以在沙箱外运行的数量更少。


### 禁用文件系统隔离

将 `sandbox.filesystem.disabled` 设置为 `true` 以跳过文件系统隔离，同时保持网络隔离。下面的示例关闭文件系统隔离，同时保留网络域的白名单：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "disabled": true
    },
    "network": {
      "allowedDomains": ["github.com", "*.npmjs.org"]
    }
  }
}
```

沙箱有两个独立的层：[文件系统隔离](<#文件系统隔离>)控制沙箱命令可以读取和写入的路径，[网络隔离](<#网络隔离>)控制它们可以到达哪些域。随着文件系统层的关闭，沙盒命令可以获得对主机文件系统的不受限制的读写访问，而它们的网络出口仍局限于您允许的域。当您使用沙箱来控制命令连接的位置而不是它们写入的内容时，请关闭该层。

`sandbox.filesystem.disabled` 默认为 `false`。需要 Claude Code v2.1.216 或更高版本。



**注意：**

通过关闭文件系统隔离和自动允许命令，沙盒命令可以写入稍后命令运行或读取的文件，例如 shell 启动文件、`$PATH` 或 `~/.claude/settings.json` 上的可执行文件，并使用它们在下次运行时扩大其自身的访问权限。仅针对您信任不会升级其自身访问权限的工作负载，将 `filesystem.disabled` 设置为 `true`。使用 [`allowManagedDomainsOnly`](<#阻止开发商扩大政策范围>) 锁定网络域可以缩小风险，但不能消除风险，因为该锁定仅适用于沙箱内运行的命令。


#### 哪些设置可以禁用它

由于关闭文件系统隔离会扩大沙盒命令的功能，因此 Claude Code 仅遵循以下设置源中的 `filesystem.disabled`：

* 用户设置、托管设置和`--settings`CLI标志都可以设置它。 `.claude/settings.json` 和 `.claude/settings.local.json` 中的项目设置不能，因此签出的项目无法关闭文件系统隔离。
* 当托管设置配置 `sandbox.filesystem` 时，或者使用 `"mode": "deny"` 列出任何 `sandbox.credentials.files` 条目时，只有托管设置可以设置密钥。这使管理员部署的文件系统限制保持有效；要放宽此类部署，请在托管设置中设置 `"disabled": true`。
* 设置 [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>) 时，Claude Code 会忽略每个源（包括托管设置）中的 `filesystem.disabled`，并保持文件系统隔离打开。

[有效的](<https://code.claude.com/docs/en/settings-reference#invalid-credential-entries-in-managed-settings>) `mask`条目不会锁定密钥，即使Claude Code [在启动时回退到`deny`](<#掩码凭证文件>)也是如此。将无法屏蔽的路径（例如凭据目录）列为托管设置中的显式 `deny` 条目，这会锁定密钥。

#### 文件系统隔离关闭时会发生什么变化

设置 `filesystem.disabled` 会解除文件系统层本身强制执行的保护。其他层强制执行的保护继续应用：

|保护|文件系统隔离关闭|
| - | - |
|`filesystem.denyRead` 和 [`credentials.files`](<#保护凭证>) `deny` 读取块|不强制执行。文件系统层同时应用|
|`credentials.envVars` `deny` 和 `mask` 条目|强制执行。环境变量清理独立于文件系统层|
|[`credentials.files` `mask` 条目](<#掩码凭证文件>) 用作掩码|强制：屏蔽独立于文件系统层。与任何 `deny` 条目一样，不强制执行 [ 回落到 `deny`](<#掩码凭证文件>) 的条目|

另外还有两件事发生了变化：

* 沙盒命令继承 shell 的 `$TMPDIR` 而不是每用户临时目录，因为每个临时目录都是可写的，并且 Claude Code 不再将命令重定向到每用户临时目录。

在 Linux 上，该变量通常在父 shell 中取消设置。 Bash 工具指南告诉 Claude 使用 `mktemp -d` 创建临时目录，而不是依赖 `$TMPDIR`。
* [`autoAllowBashIfSandboxed`](<https://code.claude.com/docs/en/settings-reference#sandbox-autoallowbashifsandboxed>) 仍默认为 `true`，因此沙盒命令会在没有提示的情况下继续运行。将其设置为 `false` 以提示输入沙盒命令。

### 保护凭证

`sandbox.credentials` 设置声明凭证文件和环境变量以防止沙盒命令的影响。每个条目命名一个文件路径或一个环境变量和一个 `mode`。专用的 `credentials` 块将凭证规则分组在一起并与一般文件系统规则分开。

对于带有 `"mode": "deny"` 的条目，沙箱内的读取将被拒绝，与 `filesystem.denyRead` 的限制相同，并且在每个沙箱命令运行之前都会取消设置环境变量。文件保护是文件系统层的一部分，因此如果您[禁用文件系统隔离](<#禁用文件系统隔离>)，则它不适用；环境变量保护仍然有效。

以下示例阻止读取 AWS 凭证文件和 SSH 目录，并从沙盒命令环境中删除 `GITHUB_TOKEN` 和 `NPM_TOKEN`：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "credentials": {
      "files": [
        { "path": "~/.aws/credentials", "mode": "deny" },
        { "path": "~/.ssh", "mode": "deny" }
      ],
      "envVars": [
        { "name": "GITHUB_TOKEN", "mode": "deny" },
        { "name": "NPM_TOKEN", "mode": "deny" }
      ]
    }
  }
}
```

环境变量条目和文件条目也接受 `"mode": "mask"`，如 [掩码凭据 ](<#掩码凭证>) 中所述。

文件路径遵循与 `sandbox.filesystem.*` 设置相同的 [ 前缀规则 ](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>)。

Claude Code 合并会话加载的每个 [ 设置范围 ](<https://code.claude.com/docs/en/settings#settings-precedence>) 中的 `deny` 条目。 `deny` 条目只会缩小访问范围，因此任何范围都可以添加一个条目，但任何范围都不能删除另一个范围添加的条目。

当您[排除设置源](<#配置沙箱>)时：

* **项目或本地设置**：Claude Code不应用任何`credentials`条目。需要 Claude Code v2.1.246 或更高版本。
* **用户设置**：Claude Code仍应用`~/.claude/settings.json`中的`deny`条目，并保留其[文件`mask`条目](<#掩码凭证文件>)作为限制，不再授权代理来替换实际值，但删除其 [ 环境变量 `mask` 条目 ](<#屏蔽环境变量>)。

没有内置的凭据拒绝列表，因此只有您列出的文件和变量受到限制。

`sandbox.credentials` 仅影响沙盒 Bash 命令。要从所有子进程中删除凭据（无论沙箱如何），请设置 [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>)。

### 掩码凭证

当您屏蔽凭据时，Claude Code 会显示沙盒命令（称为哨兵的每个会话占位符），并且 [ 沙盒代理 ](<#网络隔离>) 会替换您允许的主机的出站请求的实际值。 [保护凭证](<#保护凭证>)下的`deny`条目会阻止凭证。对于 macOS 上的文件，Claude Code [ 会阻止该文件，而不是 ](<#掩码凭证文件>) 屏蔽它。 [`sandbox.credentials`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials>) 参考文献列出了每个字段。

屏蔽需要满足以下条件：

* **TLS终止**：代理替换请求内容中的真实值，因此它必须看到它们。设置 [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) 以便代理终止 TLS 本身。如果没有它，屏蔽就会失败而不会暴露任何内容：该命令仍然只能看到哨兵，但哨兵到达服务器时不会发生变化，并且身份验证失败。要检查是否存在此错误配置，请在终端中运行 `claude doctor` 并查找 `TLS termination is unavailable` 警告。
* **允许的目的地**：每个`mask`条目可以列出`injectHosts`，真实值允许到达的主机。代理仅在 [ 域白名单 ](<#网络隔离>) 承认的连接上注入，因此每个 `injectHosts` 主机也必须可通过 `network.allowedDomains` 访问。对于没有 `injectHosts` 的 `mask` 条目，代理将替换 `network.allowedDomains` 中每个主机的请求的实际值。
* **可信设置范围**：屏蔽授权代理将您的真实凭证发送到某处，因此Claude Code尊重`mask`条目、`network.tlsTerminate`、[`credentials.allowPlaintextInject`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-allowplaintextinject>)、`awsPairs`和`sigv4` 仅来自用户设置、托管设置和 `--settings` 标志。它会忽略存储库的 `.claude/settings.json` 或 `.claude/settings.local.json` 中的它们。当您的管理员通过服务器管理的设置传送 `mask` 条目、`network.tlsTerminate` 或 `credentials.allowPlaintextInject` 时，它们将计为需要批准的 [ 设置 ](<https://code.claude.com/docs/en/server-managed-settings#security-approval-dialogs>)。

#### 屏蔽环境变量

要屏蔽环境变量，请在其 `credentials.envVars` 条目上设置 `"mode": "mask"`。该命令及其记录的任何内容都不会保存真实的凭据，但其请求仍然可以进行身份​​验证。当在任何范围内使用 `deny` 列出相同变量时，`deny` 优先。

此示例屏蔽了两个标记。 `GH_TOKEN` 仅在对 `api.github.com` 的请求中被替换，而 `NPM_TOKEN` 没有 `injectHosts`，并且在对 `network.allowedDomains` 中的每个主机的请求中被替换：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "network": {
      "tlsTerminate": {},
      "allowedDomains": ["*.github.com", "registry.npmjs.org"]
    },
    "credentials": {
      "envVars": [
        { "name": "GH_TOKEN", "mode": "mask", "injectHosts": ["api.github.com"] },
        { "name": "NPM_TOKEN", "mode": "mask" }
      ]
    }
  }
}
```

默认情况下，屏蔽会替换整个值。对于具有结构的值，例如 `DATABASE_URL` 连接字符串或 JWT，请使用 [`extract`、`decode`、`maskClaims` 和 `onExtractNoMatch` 字段](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-envvars>)，以便解析该值的工具继续工作。

<span id="ipv6-destinations-in-injecthosts" />对于 IPv6 目标，在两个列表中以不同的方式拼写地址：

* **`network.allowedDomains`**：括号形式，如`"[::1]"`
* **`injectHosts`**：规范压缩形式的裸地址，例如`"::1"`

代理将每个 `injectHosts` 条目与连接的裸目标地址进行匹配，忽略端口，因此括号中的区域 ID 或不同压缩的拼写永远不会匹配。 `claude doctor` 标记永远无法与警告 `Sandbox credential injectHosts entries can never match their destination` 匹配的条目。此检查需要 Claude Code v2.1.229 或更高版本。

#### 重新签署 AWS 请求

AWS 请求在请求内容上携带 SigV4 签名，因此将 `AWS_ACCESS_KEY_ID` 和 `AWS_SECRET_ACCESS_KEY` 一起屏蔽。代理通过访问密钥的 [sentinel](<#掩码凭证>) 检测到 SigV4 请求，并使用真实值重新签名该请求，这需要 Claude Code v2.1.221 或更高版本。如果您仅掩盖秘密，请求将使用代理无法检测到的占位符进行签名，因此它们会在 AWS 处失败。

当您屏蔽传统的 `AWS_ACCESS_KEY_ID`、`AWS_SECRET_ACCESS_KEY` 和 `AWS_SESSION_TOKEN` 变量的整个值时，Claude Code 会自动将它们链接到一个凭证中。如果您的 AWS 凭证位于具有其他名称的变量中，请将它们与 [`credentials.awsPairs`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-awspairs>) 分组，这需要 Claude Code v2.1.224 或更高版本。

流式上传、预签名 URL 和 SigV4A 请求携带代理无法重新计算的签名。当其中一个请求使用屏蔽对的占位符进行签名时，代理会使其失败，而不是转发损坏的签名。使用未屏蔽的凭据签名的请求不受影响。使用 [`credentials.sigv4`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-sigv4>)（需要 Claude Code v2.1.224 或更高版本）来转发这些请求表单之一。 AWS仍然拒绝请求，因此调用工具收到AWS自己的拒绝响应而不是代理错误。

#### 掩码凭证文件

要屏蔽凭证文件，请在其 `credentials.files` 条目上设置 `"mode": "mask"`。屏蔽文件需要 Claude Code v2.1.221 或更高版本。沙盒命令看到的内容取决于平台：

* **Linux 和 WSL2**：沙盒命令读取文件的 [sentinel](<#掩码凭证>) 副本，并且代理替换出站请求的实际值。
* **macOS**：沙盒命令根本无法读取该文件。 Claude Code 不构建哨兵副本，因此使用文件进行身份验证的工具无法在沙箱内工作，与 `deny` 效果相同。即使您[禁用文件系统隔离](<#禁用文件系统隔离>)，读取块也会保留。

此示例屏蔽了存储在 `~/.config/gh/hosts.yml` 中的 GitHub 令牌。 `extract` 模式标记文件的哪一部分是秘密，因此在 Linux 上和 WSL2 `gh` 仍然解析其配置的其余部分：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "network": {
      "tlsTerminate": {},
      "allowedDomains": ["*.github.com"]
    },
    "credentials": {
      "files": [
        {
          "path": "~/.config/gh/hosts.yml",
          "mode": "mask",
          "extract": "oauth_token:\\s*(\\S+)",
          "injectHosts": ["api.github.com"]
        }
      ]
    }
  }
}
```

要确认掩码处于活动状态，请要求 Claude 在沙盒命令中运行 `cat ~/.config/gh/hosts.yml`。在 Linux 和 WSL2 上，输出显示标记代替令牌，而在 macOS 上读取失败。

如果没有 `extract` 或 `decode`，Claude Code 会用一个哨兵替换整个文件，这适合保存单个秘密的文件。使用 [`extract`、`decode`、`maskClaims`、`onExtractNoMatch` 和 `maskDuplicates` 字段](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-files>) 来控制部分屏蔽以及当模式不匹配时会发生什么。



**注意：**

当匹配没有找到任何要屏蔽的内容时，默认的 `onExtractNoMatch` 值 `warn` 会跳过该条目，因此沙盒命令可以读取未屏蔽的真实文件。在 macOS 上，Claude Code 在文件系统隔离打开时运行模式之前，将 `mask` 条目应用为 `deny`，因此仅当 [ 文件系统隔离关闭 ](<#禁用文件系统隔离>) 时，不匹配结果才会生效。默认值适合可能合法不存在的凭据。如果秘密可能存在但模式可能会丢失，请使用 [`deny`](<https://code.claude.com/docs/en/settings-reference#mask-fields-for-files>)。


`mask` 适用于单个文件，因此单独列出每个凭证文件。对于无法安全屏蔽的 `mask` 条目，Claude Code 会回退到 `deny`：目录路径、glob 模式、大于 8 MiB 的文件或不是 UTF-8 文本的文件。

## 沙箱如何工作

### 文件系统隔离

沙盒 Bash 工具限制文件系统对特定目录的访问：

* **默认写入行为**：对当前工作目录及其子目录、使用`--add-dir`、`/add-dir`或[`permissions.additionalDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-additionaldirectories>)添加的任何目录以及`$TMPDIR`指向的每用户临时目录的读写访问权限
* **默认读取行为**：对整个计算机的读取访问权限，某些被拒绝的目录除外。此默认值仍然允许读取凭据文件，因此[保护凭据](<#保护凭证>)您不希望命令读取。
* **读取块**: 与 [`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>) 打开时，沙盒命令也会失去对主目录和保存用户文件的其他目录的读取访问权限，除了以下路径： [块下的沙盒命令](<https://code.claude.com/docs/en/settings-reference#sandboxed-commands-under-the-block>) 列表。该部分还说明了该块的这一部分何时不适用。
* **Git worktrees**：当工作目录是[链接的git worktree](<https://code.claude.com/docs/en/worktrees>)时，沙箱还允许写入主存储库的共享`.git`目录，因此诸如`git commit`之类的命令可以更新引用和索引。对该目录内的 `hooks/` 和 `config` 的写入仍然被拒绝。

要完全跳过文件系统隔离，同时保持网络隔离，请设置 [`sandbox.filesystem.disabled`](<#禁用文件系统隔离>)。

### 受保护的路径

在沙盒命令可以写入的目录内，沙盒仍然拒绝写入文件 Claude Code 从中加载配置和代码。可以编辑这些文件的命令可以授予自身权限，或者添加 Claude Code 在沙箱外部运行的挂钩或 MCP 服务器。权限系统有自己的[保护路径](<https://code.claude.com/docs/en/permission-modes#protected-paths>)，控制工具运行之前Claude Code批准的内容；沙箱的列表适用于已经运行的命令。它涵盖四组路径：

* **在您的工作目录及其上方的目录中**：`.claude`设置文件、`.claude/skills`、`.claude/agents`、`.claude/commands`和`.claude/hooks`目录， `.mcp.json`，文件Claude Code单独运行，例如`.claude/workflows`和`.claude/scheduled_tasks.json`
* **仅在您的工作目录中**：shell启动文件，例如`.bashrc`和`.zshrc`、`.gitconfig`、`.vscode`和`.idea`目录以及`hooks`和`config`里面`.git`
* **将你的工作目录变成裸git存储库的文件**：顶层的`HEAD`、`objects`和`refs`，加上现有的`config`和`hooks`条目`HEAD`坐在他们旁边。即使没有 `HEAD`，名为 `config` 的文件也会被拒绝。在 Linux 和 WSL2 上，沙盒会删除沙盒命令运行时出现的顶级 `HEAD` 文件或 `objects` 或 `refs` 目录
* **在`~/.claude`中，或目录`CLAUDE_CONFIG_DIR`指向**：其大部分内容，加上`~/.claude.json`和`.credentials.json`凭证存储

如果在会话期间受保护的设置文件的路径中出现符号链接，则沙箱也会拒绝写入它指向的文件，从下一个命令开始。

无法免除这些路径之一：覆盖该路径的 `allowWrite` 条目或 `Edit` 允许规则不会解除保护。关闭保护的唯一方法是 [`filesystem.disabled`](<#禁用文件系统隔离>)，它会关闭每个路径的文件系统隔离。要查看为您的计算机解析的大多数路径，请运行 `/sandbox` 并打开 **Config** 选项卡，该选项卡在 **Denied inside allowed** 下列出它们，并与您自己的 `denyWrite` 条目混合在一起。

如果 `git merge` 或 `git checkout` 在这些路径之一上失败并显示 `unable to unlink old`，请参阅 [git 命令失败并显示 `unable to unlink old`](<#git-命令失败并显示-unable-to-unlink-old>)。

### 网络隔离

沙盒命令没有到网络的直接路由：

* **Linux 和 WSL2**：该命令在与您的网络没有连接的单独网络命名空间中运行
* **macOS**：Seatbelt 沙箱框架默认阻止除沙箱代理之外的连接

Claude Code 在您的计算机上、沙箱外部运行沙箱代理，并使用 `HTTP_PROXY`、`HTTPS_PROXY`、`ALL_PROXY` 和相关环境变量将命令定向到它。代理根据允许和拒绝的域检查每个连接的主机名。

一个工具能达到什么程度取决于它是否使用代理：

* **读取代理变量的工具**：`curl`、`npm`、`git` over HTTPS，以及类似的工具一旦允许其主机即可连接。没有端口的 `allowedDomains` 条目允许该主机上的每个端口
* **忽略代理变量的工具**：普通`ssh`，大多数数据库驱动程序和类似工具无法连接，甚至无法连接到允许的主机。请参阅[数据库客户端或其他非HTTP工具无法到达允许的主机](<#数据库客户端或其他非-http-工具无法到达允许的主机>)
* **任何不属于TCP**的东西：UDP、HTTP/3超过QUIC，以及ICMP工具（例如`ping`）不能离开沙箱

以下设置和行为控制代理允许哪些主机：

* **域限制**：您允许的域从空开始。 [允许的域之外的主机](<#主机位于您允许的域之外>)涵盖了命令第一次需要新域时发生的情况。
* **批准选项**：如果在提示时选择“是”，Claude Code将允许主持人进行当前会话的剩余时间。如果您选择“是，不再询问”，Claude Code 会将 `WebFetch(domain:...)` 允许规则保存到您的 [ 本地设置 ](<https://code.claude.com/docs/en/permissions#permission-system>)，以便主持人在以后的会话中保持允许状态。虽然沙箱需要 [ 管理员 ](<#管理员所需的沙箱下的存储库设置>)，但 Claude Code 会将规则保存到您的用户设置中，并应用于每个项目。
* **预先允许的域**：预先允许带有[`allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>)的域，以完全避免提示。 Claude Code 还预先允许来自 `WebFetch(domain:...)` 允许规则的域，如 [ 权限规则 ](<#权限规则>) 中所述。
* **严格白名单**：如果您在用户、托管或CLI `--settings`设置中将[`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>)设置为`true`，则Claude Code会拒绝沙盒命令访问白名单之外的任何主机的提示。允许列表是 `allowedDomains` 加上 `WebFetch(domain:...)` 允许规则中的域，或者仅设置 `allowManagedDomainsOnly` 时的托管设置条目。 [无需管理员所需的沙箱即可应用的锁](<#无需管理员所需的沙箱即可应用的锁>)涵盖存储库的条目。 Claude Code 仅对沙盒命令强制执行此操作；诸如`WebFetch`之类的进程内工具仍然遵循其[权限规则](<#权限规则>)。在存储库的 `.claude/settings.json` 或 `.claude/settings.local.json` 中设置它没有任何效果。需要 Claude Code v2.1.219 或更高版本。
* **托管锁定**：如果在托管设置中设置了[`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>)，则将自动阻止非允许的域而不是提示，并且仅遵守托管设置中的`allowedDomains`和`WebFetch(domain:...)`允许规则。
* **企业代理**：当您的网络需要出站流量通过企业代理时，请按照[代理配置](<https://code.claude.com/docs/en/network-config#proxy-configuration>)中的描述设置`HTTPS_PROXY`、`HTTP_PROXY`和`NO_PROXY` `env` 阻止您的设置，以便 [ 背景 agents](<https://code.claude.com/docs/en/network-config#set-network-variables-in-settings-not-the-shell>) 也获取它们，或者在您启动 Claude Code 的环境中。 Claude Code 强制执行域白名单，然后通过该上游代理建立允许的连接隧道。 `http://` 和 `https://` 代理 URL 可以工作，如果需要，可以在 URL 中进行基本身份验证。

在 `WebFetch(domain:...)` 规则中，沙箱支持两种通配符形式：前导 `*.`，例如 `*.example.com` 和裸 `*`。裸 `*` 表单需要 Claude Code v2.1.186 或更高版本。任何其他位置的通配符（例如 `WebFetch(domain:example.*)`）仍然匹配提取，但对沙盒命令没有影响。



**注释：**

内置代理根据请求的主机名强制执行允许列表，并且默认情况下不会终止或检查 TLS 流量。实验性的 [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) 设置使内置代理终止 TLS 本身，这是 [`mask` 凭据条目 ](<#掩码凭证>) 所需要的。请参阅 [ 安全限制 ](<#安全限制>) 了解默认值的影响，如果您的威胁模型需要 TLS 检查，请参阅 [自定义代理配置 ](<#自定义代理配置>)。


#### 主机位于您允许的域之外

当沙盒命令连接到不在您允许的域中的主机时，该命令将保留在沙盒中并等待决定。在交互式终端会话中，决定取决于您的权限模式：

|权限模式|连接会发生什么情况|
| :- | :- |
|`bypassPermissions` 模式，以及具有 [ 绕过权限的计划模式 ](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>)|无需提示即可允许|
|手动模式、`acceptEdits`模式和计划模式|您会收到提示|
|自动模式|除非命令 [ 列出了主机 ](<#自动模式下每个命令允许的域>) 并且分类器批准了该列表，否则会被拒绝|
|`dontAsk`模式|拒绝|

当[`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>)或[`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>)打开时，内置沙箱代理在每种权限模式下都会拒绝连接。在 `bypassPermissions` 模式下，允许允许的域之外的主机，除非其中之一处于打开状态。 [非沙箱重试逃生舱口](<#非沙盒重试逃生舱口>)涵盖命令何时可以将沙箱留在该模式。在每种权限模式下，与 [`deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) 中的主机的连接也会被拒绝。

#### 解析为本地地址的主机名

主机名通过白名单后，沙箱代理会对其进行解析，并在该名称仅解析为本地地址时拒绝连接。本地地址包括环回地址（例如 `127.0.0.1`）、链路本地地址（例如 `169.254.169.254` 云元数据端点）以及分配给您自己的计算机的地址。名称 `localhost` 和 `*.localhost` 允许解析为环回。

允许解析为专用范围（例如 `10.0.0.0/8`）的 Intranet 主机名进行连接。要让名称解析为被拒绝的地址，请将该 IP 地址添加到 `allowedDomains`，例如 `"127.0.0.1:8080"`。

该检查适用于主机名。您允许的域和权限模式决定与 IP 地址的连接。代理还会跳过对通过上游公司代理发送的连接的检查，因为该代理会解析名称。

#### 自动模式下每个命令允许的域

在启用沙箱的 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 下，Claude 在命令本身上命名命令所需的主机，而不是为每个连接触发网络批准。在沙箱中运行的每个 Bash、PowerShell 或 [Monitor](<https://code.claude.com/docs/en/tools-reference#monitor-tool>) 命令都可以携带超出沙箱白名单的主机列表：诸如 `registry.npmjs.org` 的域、诸如 `*.pythonhosted.org` 的通配符或 IP地址，每个都有一个可选的`:port`。分类器与命令一起审查主机。需要 Claude Code v2.1.271 或更高版本。

只要该命令运行，批准的列表就会仅针对该命令打开这些主机。不会向您的会话允许的主机或您的设置添加任何内容；下一个命令命名它自己的主机。

携带主机的命令会进入分类器，而不是由权限规则或沙箱的 [ 自动允许模式 ](<#沙盒模式>) 批准。如果 [ 询问规则 ](<https://code.claude.com/docs/en/permissions#manage-permissions>) 强制提示输入命令，终端中的权限对话框会列出其旁边的主机，并且批准会涵盖这两个主机。

每个命令列表仅扩大沙箱默认拒绝的内容。 [`deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) 条目仍然被阻止。当 [`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>) 或 [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) 锁定白名单时，Claude Code 拒绝每个命令列表。

虽然每个命令列表适用，但 Claude Code 拒绝连接到未列出批准命令的主机，而无需提示或分类器检查。拒绝在命令结果中命名主机，并且 Claude 重新运行添加了主机的命令。

#### 域列表中的 IPv6 地址

要匹配 `allowedDomains`、`deniedDomains` 或 `WebFetch(domain:...)` 规则中的 IPv6 地址，请将地址写在括号中：`"[::1]"` 匹配每个端口上的该地址，而 `"[::1]:443"` 仅在端口 443 上匹配该地址。括号形式需要 Claude Code v2.1.229 或更高版本。

不带括号的条目（例如 `::1:443`）在地址和带端口的地址之间是不明确的：

* **拒绝列表**：Claude Code拒绝条目解析为的每个读取，因此您想要的任何读取都会被阻止。对于没有可解析读取的条目，Claude Code 不会阻止任何内容
* **允许列表**：Claude Code永远不会允许超过您所写的内容。当读取解析干净时，它会重写其主机和端口读取的不明确条目，并且可能会完全删除该条目而不是扩大白名单

要查找不明确的条目，请在终端中运行 `claude doctor` 并查找 `Sandbox network domain entries have unreliable spellings` 警告。以括号形式重写每个不明确的条目。

### OS-级别执行

沙盒 Bash 工具使用操作系统安全原语：

* **macOS**：使用 Seatbelt 进行沙箱强制执行
* **Linux**：使用 [bubbblewrap](<https://github.com/containers/bubblewrap>) 进行隔离
* **WSL2**：使用 bubblewrap，与 Linux 相同

您还可以单独运行 [`@anthropic-ai/sandbox-runtime`](<https://github.com/anthropics/sandbox-runtime>) 包来包装 Claude Code 进程。请参阅[沙箱运行时](<https://code.claude.com/docs/en/sandbox-environments#sandbox-runtime>)。

## 沙盒与权限和权限模式有何关系

沙箱、[权限规则](<https://code.claude.com/docs/en/permissions>)和[权限模式](<https://code.claude.com/docs/en/permission-modes>)是互补层。以下部分介绍了沙箱如何与每个部分交互。

### 权限规则

权限规则和沙箱控制不同的东西：

* **权限规则**控制哪些工具Claude Code可以使用并在任何工具运行之前进行评估。它们适用于所有工具：Bash、Read、Edit、WebFetch、MCP 等，但拒绝或询问规则不能在任何其他工具保留时阻止 [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>)。
* **沙箱**提供OS-级别的强制执行，限制shell命令可以在文件系统和网络级别访问的内容。它适用于 Bash、PowerShell 和 [Monitor](<https://code.claude.com/docs/en/tools-reference#monitor-tool>) 工具命令及其子进程。

这两层的执行方式也有所不同。 Claude Code 在命令运行之前根据命令字符串评估权限决策，并且在自动模式下，根据单独的分类器对命令是否安全的判断。操作系统在运行的进程上强制执行沙箱边界，因此无论模型选择运行什么，即使允许的命令执行的操作超出其名称所暗示的范围，它都会保持不变。

文件系统和网络限制是通过沙箱设置和权限规则配置的：

|设置或规则|它的作用|
| :- | :- |
| `sandbox.filesystem.allowWrite` |授予子进程对工作目录之外的路径的写访问权限|
|`sandbox.filesystem.denyWrite` 和 `sandbox.filesystem.denyRead`|阻止子进程访问特定路径|
| `sandbox.filesystem.allowRead` |重新允许读取 `denyRead` 区域内的特定路径|
| [`sandbox.filesystem.disabled`](<#禁用文件系统隔离>) |完全关闭文件系统层，同时保持网络隔离|
|`Edit`允许规则|授予对特定路径的写访问权限，与 `sandbox.filesystem.allowWrite` 的方式相同|
|`Read` 和 `Edit` 拒绝规则|阻止对特定文件或目录的访问|
|`WebFetch(domain:...)` 允许和拒绝规则|控制域访问|
|沙盒`allowedDomains`|控制 Bash 命令可以到达的域|
|沙盒`deniedDomains`|即使更广泛的 `allowedDomains` 通配符允许特定域，也会阻止它们|

沙箱设置和权限规则中的路径和域将合并到最终的沙箱配置中。

[claude-code 存储库的示例目录 ](<https://github.com/anthropics/claude-code/tree/main/examples/settings>) 包含常见部署场景的入门设置配置，包括特定于沙箱的示例。使用这些作为起点并调整它们以满足您的需求。

### 权限模式

`/sandbox`不是[权限模式](<https://code.claude.com/docs/en/permission-modes>)。权限模式决定工具调用是否运行以及是否首先提示您，而沙箱则限制 Bash 命令运行后可以访问的内容。它们的不同之处在于控制的内容以及替换每个操作提示的内容：

| |它控制什么|什么取代了提示|
| :- | :- | :- |
| `/sandbox` |Bash 命令运行后可以访问什么|沙箱边界本身，处于 [ 自动允许模式 ](<#沙盒模式>)|
|[自动模式](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)|每个工具调用是否运行|审查动作的分类器|
| `--dangerously-skip-permissions` |每个工具调用是否运行|什么也没有。 [受保护路径](<https://code.claude.com/docs/en/permission-modes#protected-paths>)检查也被跳过； [操作无模式自动批准](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>)仍然适用|

沙箱的 [ 自动允许模式 ](<#沙盒模式>) 与 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 是分开的：自动允许批准 Bash 命令，因为沙箱边界包含它们，而自动模式使用分类器来审查操作。两者独立工作，也可以组合使用，但[沙盒模式](<#沙盒模式>)下列出的例外情况。要为无人值守运行选择隔离边界，请参阅[沙盒环境](<https://code.claude.com/docs/en/sandbox-environments#how-isolation-relates-to-permission-modes>)。有关常见权限模式和沙箱配对的表格以及启动每个模式的标志，请参阅[常见设置](<https://code.claude.com/docs/en/permission-modes#common-setups>)。

## 为您的组织配置沙箱

管理员可以要求每个用户都使用沙箱，阻止开发人员扩大策略，并通过公司代理路由沙箱流量。

### 使用托管设置强制执行沙箱

要为每个开发人员提供沙箱，请通过 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>) 交付 `sandbox` 密钥，无论是作为由 MDM 管理的文件，还是通过 claude.ai 上的 [ 服务器管理设置 ](<https://code.claude.com/docs/en/server-managed-settings>) 交付。

以下托管设置配置启用沙箱，在平台不受支持或缺少依赖项时拒绝启动 Claude Code，并防止模型在沙箱之外重试命令：

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "failIfUnavailable": true,
    "allowUnsandboxedCommands": false
  }
}
```

`enabled` 之外的两个键控制沙箱无法运行命令时发生的情况：

* **`failIfUnavailable`**：缺少依赖项（例如 Linux 上的 bubblewrap）会阻止 Claude Code 启动而不是退回到非沙盒执行
* **`allowUnsandboxedCommands: false`**：Claude Code 忽略 `dangerouslyDisableSandbox` 逃生舱口，因此当命令在沙箱下失败时，Claude 无法在未沙箱的情况下重试

考虑这些补充：

* 为必须在没有隔离的情况下运行的任何组织批准的工具添加 `excludedCommands`，因为此配置 [ 会阻止存储库的设置从沙箱中获取命令 ](<#管理员所需的沙箱下的存储库设置>)
* 为凭据目录（例如 `~/.aws` 和 `~/.ssh`）以及秘密环境变量添加 [`sandbox.credentials`](<#保护凭证>) 条目，因为默认读取策略仍然允许它们

此配置沙箱运行命令 Claude。开发人员仍然可以在 [`!` shell 模式提示符 ](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) 下键入命令并在沙箱外部运行它，其访问权限与他们在 Claude Code 之外的任何终端中已经拥有的访问权限相同。请参阅 [ 严格沙盒模式 ](<#用严格沙箱模式关闭重试>) 了解键入的命令在沙盒中运行的会话。

沙箱不在本机 Windows 上运行，因此设置 `failIfUnavailable` 后，Claude Code 在这些计算机上启动时退出。如果您的机群包括 Windows 主机，您可以：

* **按操作系统交付配置**：通过您的MDM或仅在macOS和Linux计算机上将其部署为[托管设置文件](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>)。 [服务器管理的设置](<https://code.claude.com/docs/en/server-managed-settings#current-limitations>) 适用于组织中的所有用户
* **将 Windows 用户移至受支持的环境中**：让他们在 WSL2 或容器内运行 Claude Code

### 阻止开发商扩大政策范围

当托管设置设置布尔键（例如 `enabled` 或 `failIfUnavailable`）时，Claude Code 使用托管值并忽略开发人员在本地设置的任何内容。对于诸如 `allowRead` 之类的数组键，Claude Code 会合并会话加载范围中的条目，因此开发人员可以附加扩大策略的条目，除非锁覆盖了该键。

除非托管设置设置了它们，否则开发人员的用户设置或 `--settings` 可以打开以下键。存储库的 `.claude/settings.json` 也可以，除非沙箱是 [ 需要管理员 ](<#管理员所需的沙箱下的存储库设置>)。每一个都会削弱沙箱，因此如果您不希望使用它，请在托管设置中将其设置为 `false`：

* [`enableWeakerNestedSandbox`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernestedsandbox>)
* [`enableWeakerNetworkIsolation`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernetworkisolation>)
* [`network.allowAllUnixSockets`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowallunixsockets>)
* [`network.allowLocalBinding`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowlocalbinding>)
* [`allowAppleEvents`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowappleevents>)，存储库无法打开

在托管设置中将 `allowManagedReadPathsOnly` 设置为 `true`，以便仅接受托管设置中的 `allowRead` 条目。这可以防止开发人员将读取访问范围扩大到组织批准的路径之外。

要将网络域锁定为托管值，请设置 [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>)。锁定后，只有托管设置可以设置 [ 代理端口 ](<#自定义代理配置>)。

当托管设置配置 `sandbox.filesystem` 或使用 `"mode": "deny"` 列出任何 `sandbox.credentials.files` 条目时，只有托管设置可以设置 [`filesystem.disabled`](<#禁用文件系统隔离>)，因此开发人员无法关闭管理员部署的文件系统限制。 [有效的](<https://code.claude.com/docs/en/settings-reference#invalid-credential-entries-in-managed-settings>) `mask`条目不会锁定密钥。请参阅[哪些设置可以禁用它](<#哪些设置可以禁用它>)。

#### 管理员所需的沙箱下的存储库设置

当以下设置之一生效时，沙箱需要管理员：

* [`allowUnsandboxedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowunsandboxedcommands>) 在托管设置中设置为 `false`，或使用 `--settings` 标志，除非托管设置将其设置为 `true`
* 在托管设置中将 [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) 设置为 `true`

这些设置不会打开沙箱，因此还要设置 `enabled`。

虽然沙箱需要管理员管理，但 Claude Code 采用仅从托管设置、`--settings` 标志和每个开发人员的 `~/.claude/settings.json` 中放松沙箱的设置。它忽略存储库的 `.claude/settings.json` 和 `.claude/settings.local.json` 中的这些设置：

|存储库设置|Claude Code 忽略了什么|
| :- | :- |
| `excludedCommands`, `ignoreViolations`, `network.allowedDomains`, `network.allowUnixSockets`, `network.allowMachLookup`, `network.httpProxyPort`, `network.socksProxyPort` |每个条目|
|`filesystem.allowWrite`、`Edit(...)` 允许规则、`permissions.additionalDirectories`|每个条目的写访问都提供沙盒命令。 Claude的文件工具仍然遵循`Edit(...)`规则和附加目录|
|`WebFetch(domain:...)`允许规则|每个规则添加到沙箱允许列表中的主机。 WebFetch工具仍然遵循规则|
| `enableWeakerNestedSandbox`, `enableWeakerNetworkIsolation`, `network.allowAllUnixSockets`, `network.allowLocalBinding` |`true`。 `false` 仍然适用|
| `enabled`, `failIfUnavailable` |`false`，当开发者的`~/.claude/settings.json`设置为`true`时|
| `filesystem.allowRead` |管理设置 `--settings` 或用户设置拒绝读取的路径处或路径下的条目，或可以匹配的 glob|

当沙箱需要管理员时，这些设置仍然适用：

* **在存储库的文件中**：拒绝条目和`autoAllowBashIfSandboxed`值。在托管设置中设置密钥以防止存储库更改它
* **在开发人员自己的设置中**：表中的设置仍然适用于`~/.claude/settings.json`或`--settings`，除非诸如`allowManagedDomainsOnly`之类的仅托管锁覆盖它们。其中大多数，例如`excludedCommands`和`filesystem.allowWrite`，没有仅托管锁

[使用托管设置强制沙箱](<#使用托管设置强制执行沙箱>)下的配置使沙箱需要管理员权限。将批准的工具所需的 `excludedCommands`、`allowWrite` 和套接字条目添加到托管设置中，因为存储库无法提供它们。

需要 Claude Code v2.1.285 或更高版本。从 v2.1.282 到 v2.1.284，相同的设置使 Claude Code 忽略存储库的 `excludedCommands` 条目。

#### 无需管理员所需的沙箱即可应用的锁

某些设置使 Claude Code 忽略直接覆盖一项限制的存储库密钥，即使沙箱不需要管理员也是如此。仅当您在文件中设置其行名称时，每个沙箱才会产生此效果，并且存储库的其他沙箱设置仍然适用。需要 Claude Code v2.1.285 或更高版本。

|设置|你设置的地方|Claude Code 在存储库的设置中忽略了什么|
| :- | :- | :- |
|`network.deniedDomains` 或 `WebFetch(domain:...)` 拒绝规则|托管设置，`--settings`|`httpProxyPort` 和 `socksProxyPort`|
| `network.strictAllowlist` |托管设置，`--settings`，用户设置|代理端口 `allowedDomains` 和 `WebFetch(domain:...)` 允许规则|
|`filesystem.denyRead`、`Read(...)` 拒绝规则或 `credentials.files` 条目|托管设置，`--settings`|托管设置、`--settings` 或用户设置拒绝读取的路径上或下的 `allowRead`、`allowWrite`、`Edit(...)` 允许或 `additionalDirectories` 条目，或可以匹配的 glob|

这些锁改变了沙盒命令可以到达的范围。 WebFetch 工具和 Claude 的文件工具仍然遵循存储库的规则和附加目录。

### 自定义代理配置

要使用您自己的工具检查、过滤或记录沙箱流量，请将内置沙箱代理替换为您在同一台计算机上运行的代理。

要通过网络上其他地方的公司代理路由沙箱流量，请设置 `HTTPS_PROXY` 相反，作为 **企业代理** 条目下 [网络隔离](<#网络隔离>) 描述。这样， Claude Code的许可名单仍然适用。

要将沙盒命令定向到您的代理，请在 [ 沙盒设置 ](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>) 中设置它侦听的本地主机端口：

```json theme={null}
{
  "sandbox": {
    "network": {
      "httpProxyPort": 8080,
      "socksProxyPort": 8081
    }
  }
}
```

如果您设置端口并设置 `HTTPS_PROXY` 或 `HTTP_PROXY`，则 Claude Code 不会将发送到代理的沙盒命令转发到代理这些变量名称。要访问公司代理，请配置您自己的代理以转发到该代理。

哪些文件可以设置端口取决于您的其他沙箱设置。第一个匹配的情况适用：

* **`allowManagedDomainsOnly` 已打开 **：仅限托管设置
* **沙箱需要[管理员](<#管理员所需的沙箱下的存储库设置>)，或[较窄的网络锁定](<#无需管理员所需的沙箱即可应用的锁>)适用**：托管设置、`--settings`和用户设置
* **否则**：任何设置文件

Claude Code 忽略其他地方设置的端口。在v2.1.285之前，任何设置文件都可以设置端口。



**注意：**

一旦任一端口适用，您的代理就负责过滤发送给它的所有内容。 Claude Code自己的网络控制，例如`allowedDomains`、`deniedDomains`、`strictAllowlist`、批准提示和[本地地址检查](<#解析为本地地址的主机名>)，停止应用于该流量。沙盒命令可以连接到任一代理，因此，如果您仅设置一个端口，则另一个代理上的 Claude Code 的域列表不会限制该命令通过您的端口到达的内容。


## 故障排除

有些命令即使在沙箱外也能工作，但在沙箱内会失败。查找与您的症状或错误消息匹配的标题。

如果您组织的沙箱是 [ 需要管理员 ](<#管理员所需的沙箱下的存储库设置>)，则 Claude Code 会忽略项目设置文件中这些修复名称的设置，因此将它们保存在 `~/.claude/settings.json` 中，并在每个项目中应用。如果修复仍然没有效果，您组织的托管设置可能会设置该键。

添加 `excludedCommands` 模式的修复会从模式匹配的命令中删除沙箱。请参阅[排除的命令可以做什么](<#使用-excludedcommands-在沙箱外运行命令>)。

### 命令因主机不允许错误而失败

许多 CLI 工具需要到达特定主机。出现提示时批准主机，或将其添加到 [`allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>)。如果您的组织使用 `allowManagedDomainsOnly` 锁定白名单，则不会出现提示，因此请要求管理员添加主机。

### `jest` 挂起或失败

`watchman` 与沙箱不兼容。改为运行 `jest --no-watchman`。

### 基于 Go 的 CLI 无法对 macOS 进行 TLS 验证

`gh`、`gcloud`和`terraform`等工具可能会在[安全带](<#os-级别执行>)下无法通过TLS验证。要在沙箱外运行这些工具，请为每个工具添加一个模式，例如 `gh *` 到 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>)。然后，该工具将使用您的完全访问权限及其存储的凭据运行。如果您将 `httpProxyPort` 与 MITM 代理和自定义 CA 一起使用，请将 [`enableWeakerNetworkIsolation`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernetworkisolation>) 设置为 `true`。

### `open`、`osascript` 或基于浏览器的身份验证流程失败，macOS 上出现错误 `-600`

默认情况下，沙盒会阻止 Apple 事件。在您的用户、托管或 CLI 设置中将 [`allowAppleEvents`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowappleevents>) 设置为 `true` 以允许它们。 Claude Code 在项目设置中忽略此键。

启用 `allowAppleEvents` 会删除代码执行隔离，因为沙盒命令可以在没有用户提示的情况下启动其他未沙盒的应用程序，并且可以根据 macOS 自动化同意提示 (TCC) 向正在运行的应用程序发送 AppleScript 命令。或者，将 `open *` 等模式添加到 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>)。然后，每个 `open` 调用都会经过权限流程，并且 `open` 可以启动任何文件或应用程序，包括 Claude 编写的文件或应用程序。

### `docker` 命令失败

`docker` 与沙箱不兼容。使用 `excludedCommands` 模式（例如 `docker compose *`）从沙箱中取出您需要的 `docker` 命令。 [使用 `excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>) 在沙箱外部运行命令解释了排除的 `docker` 命令可以达到的范围。较窄的模式从沙箱中取出的命令较少。

### `pbcopy`、`xclip` 或 `wl-copy` 不更新剪贴板

`pbcopy`、`xclip` 和 `wl-copy` 剪贴板实用程序可能无法从沙箱内部到达系统剪贴板，在这种情况下，通过管道传输到它们的文本不会到达。

要将 Claude 的输出放在剪贴板上，请要求 Claude 在其响应中打印它，然后运行 [`/copy`](<https://code.claude.com/docs/en/commands>)。 `/copy` 从 Claude Code 进程而不是从沙盒命令写入剪贴板。

当 Claude 通过管道将文本传输到这些工具之一时，将该工具添加到 [`excludedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>) 不会自行将该调用从沙箱中取出。

### git 命令失败并显示 `unable to unlink old`

当需要替换沙箱拒绝写入的文件时，`git merge`、`git checkout` 和类似命令会失败并显示 `unable to unlink old`。在 Linux 和 WSL2 上，错误以 `Read-only file system` 结尾。该文件可以位于以下位置之一：

* 在 [ 受保护路径 ](<#受保护的路径>) 下，例如 `.claude/skills`
* 在您的 `denyWrite` 条目之一下
* 在目录之外，沙箱允许命令写入

失败后，Claude可能[提议在沙箱](<#非沙盒重试逃生舱口>)之外重新运行命令。批准重试，或在另一个终端中自行运行 git 命令。如果您将`allowUnsandboxedCommands`设置为`false`，Claude无法提供重试，因此请自行运行该命令。

### Bubblewrap 无法在容器内启动

在非特权容器中，[bubblewrap](<#os-级别执行>) 无法挂载新的 `/proc` 文件系统，因此沙盒命令会失败并出现 `bwrap` 错误，例如 `Can't mount proc on /newroot/proc: Operation not permitted`。将 [`enableWeakerNestedSandbox`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernestedsandbox>) 设置为 `true`，以便沙箱绑定挂载容器的现有 `/proc`。仅当外部容器已提供所需的隔离边界时才使用此设置，因为该设置将进程信息公开给沙盒命令，而新的 `/proc` 挂载将隐藏这些信息。

### 0字节只读文件出现在`.claude`设置路径中，并且“是，不再询问”不保存

在 Linux 和 WSL2 上，沙盒通过在沙盒命令运行时创建 0 字节只读占位符来对尚不存在的文件进行写入拒绝。沙箱随后会删除占位符。如果会话在清理运行之前被终止（例如通过 SIGKILL），则占位符会保留下来。以后的会话在每次启动时都会再次将占位符绑定为只读，因此保存权限选择等设置写入会在占位符保留的路径处失败。

在终端中运行 `claude doctor` 以列出剩余的占位符文件。 [`Stale sandbox mask files left by a killed session`](<https://code.claude.com/docs/en/errors#stale-sandbox-mask-files-left-by-a-killed-session>) 警告命名其中一些并计算其余的。删除带有 `rm` 的每个文件，同时该项目中没有运行其他 Claude Code 会话。在 v2.1.257 之前，Claude Code 留下了相同的占位符而没有标记它们。

### SSH 上的 `git` 失败，沙箱开启

在 macOS、`git fetch`、`git pull` 和 `git push` 上，即使主机被允许，沙箱内的 SSH 远程也会失败。在 Linux 和 WSL2 上，一旦主机被允许，它们就可以工作。 Claude Code 通过 [ 沙盒代理 ](<#网络隔离>) 建立 git 的 SSH 连接隧道，并且 macOS 隧道无法对该代理进行身份验证。

在 Linux 和 WSL2 上，如果连接仍然失败，请检查以下内容：

* **端口 22** 上允许主机：没有端口的 `allowedDomains` 条目（例如 `"git.example.com"`）将其覆盖
* **您的公司代理允许端口 22**：如果您的网络需要上游代理，隧道也会通过它
* **密钥可作为文件读取**：沙箱可以阻止`ssh-agent`套接字，并且`~/.ssh`的`denyRead`或`credentials`条目隐藏您的密钥文件

在 macOS 上，将遥控器切换到 HTTPS，这需要 HTTPS 凭据，例如个人访问令牌：

```bash theme={null}
git remote set-url origin https://git.example.com/example-org/example-repo.git
```

如果你必须保持 SSH 远程，请使用 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>) 将 git 的网络命令从沙箱中取出：

```json theme={null}
{
  "sandbox": {
    "excludedCommands": ["git fetch *", "git pull *", "git push *"]
  }
}
```

这些条目与 `git push origin main` 匹配。添加 `cd`、使用 `git -C` 或包含命令替换的调用将保持沙箱状态。排除的 git 命令可以到达任何主机，而不仅仅是 `allowedDomains` 中的主机。

由于 [ 数据库客户端条目 ](<#数据库客户端或其他非-http-工具无法到达允许的主机>) 给出的原因，SSH 上的普通 `ssh`、`scp` 和 `rsync` 失败。

### 数据库客户端或其他非 HTTP 工具无法到达允许的主机

忽略代理环境变量的工具无法从沙箱内部连接，甚至无法连接到 `allowedDomains` 中的主机。沙盒命令[没有到网络的直接路由](<#网络隔离>)，因此打开自己的连接的工具会失败。大多数数据库驱动程序、普通的 `ssh` 和使用 UDP 的工具都以这种方式运行。

该故障看起来像是网络或名称解析错误：

* **macOS**：`Operation not permitted`，或名称解析错误，例如`Could not resolve host`
* **Linux 和 WSL2**：`Network is unreachable`，或名称解析错误，例如 `Temporary failure in name resolution`

当其主机不被允许时，使用代理的工具会以不同的方式失败。您收到网络提示，或者该工具从代理收到 `403` 响应。

要让该工具进行连接，请使用 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>) 在沙箱外部运行需要该工具的命令。此示例排除一个脚本并添加一个 [ 询问规则 ](<https://code.claude.com/docs/en/permissions>) 以便您批准每次运行：

```json theme={null}
{
  "sandbox": {
    "excludedCommands": ["python scripts/load_orders.py *"]
  },
  "permissions": {
    "ask": ["Bash(python scripts/load_orders.py *)"]
  }
}
```

该脚本以您的完全访问权限运行，并且 Claude 可以编辑您工作目录中的脚本，因此请在出现提示时查看它。

### 命令无法到达本地主机上的服务器

默认情况下，沙盒命令无法直接连接到沙盒之外的计算机上运行的服务器，例如开发服务器或容器中的数据库。您可以更改的内容取决于您的平台：

* **macOS**：将[`network.allowLocalBinding`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowlocalbinding>)设置为`true`。然后，沙盒命令可以侦听网络端口并连接到本地主机上的任何端口，其中包括侦听在那里的所有其他服务。不需要身份验证的本地主机服务（例如调试器）可以执行沙箱外部的命令，并且侦听非环回地址的命令接受来自其他计算机的连接
* **Linux 和 WSL2**：沙盒命令的 `localhost` 是该命令专用的。该命令可以侦听端口并到达它自己启动的服务器。与 `localhost` 或 `127.0.0.1` 的直接连接不会到达主机上的服务器，并且 `allowLocalBinding` 不起作用。运行需要主机服务器位于沙箱之外的命令 [`excludedCommands`](<#使用-excludedcommands-在沙箱外运行命令>)，其中没有文件系统或网络限制。对于通过沙箱代理的连接，请参阅[解析为本地地址的主机名](<#解析为本地地址的主机名>)

此示例打开 macOS 的设置：

```json theme={null}
{
  "sandbox": {
    "network": {
      "allowLocalBinding": true
    }
  }
}
```

`localhost` 的 `allowedDomains` 条目适用于通过代理的连接，因此它不会更改直接连接。 Claude Code 为沙盒命令设置 `NO_PROXY`，以便它们直接连接到 `localhost`，而不是通过代理。该条目还将计算机本地主机上的每个端口公开给使用代理的命令。对于指向 `127.0.0.1` 的开发主机名，请参阅 [允许的主机名被 `resolved to a loopback address`](<#允许的主机名被拒绝并显示-resolved-to-a-loopback-address>) 拒绝。

### 允许的主机名被拒绝并显示 `resolved to a loopback address`

沙箱代理拒绝 [ 解析为本地地址 ](<#解析为本地地址的主机名>) 的允许主机名，这会影响指向 `127.0.0.1` 的开发名称，例如 `myapp.test`。该命令看到一个 `403` 响应，其正文命名了地址类型，例如 `Connection to myapp.test blocked: resolved to a loopback address`。

将名称解析为的 IP 地址与 `allowedDomains` 中的主机名一起添加，每个地址都包含您的服务器侦听的端口：

```json theme={null}
{
  "sandbox": {
    "network": {
      "allowedDomains": ["myapp.test:3000", "127.0.0.1:3000"]
    }
  }
}
```

没有端口的 IP 地址条目可让沙盒命令到达侦听该地址的每个服务。

在 v2.1.284 之前，代理连接到允许的主机名解析到的任何地址。

### `/sandbox` 失败并显示 `Sandbox settings are overridden by a higher-priority configuration`

当较高的 [ 设置级别 ](<https://code.claude.com/docs/en/settings#settings-precedence>) 设置 `sandbox.enabled`、`sandbox.autoAllowBashIfSandboxed` 或 `sandbox.allowUnsandboxedCommands` 时，`/sandbox` 打印 `Error: Sandbox settings are overridden by a higher-priority configuration and cannot be changed locally.`，而不是打开其面板。面板将您的选择保存到 `.claude/settings.local.json`，并且保存在那里的值不能覆盖这些级别。

托管设置和 `--settings` 的排名高于本地设置。要查看此会话加载了其中哪些，请运行 `/status` 并读取 `Setting sources` 行：

* **`Command line arguments`**：如果您使用[`--settings`](<https://code.claude.com/docs/en/settings#change-a-setting-for-one-session>)启动Claude Code，请检查您传递的文件或JSON是否设置了这些键之一。如果是，请更改此处的值，或在不使用这些键的情况下再次启动 Claude Code。
* **`Enterprise managed settings`**：已加载您组织的托管设置。如果他们设置了这些密钥之一，您将无法从 `/sandbox` 或您控制的任何设置文件更改该密钥，因此请询问您的管理员。

## 局限性

沙箱可以降低风险，但并不是完全的隔离边界。在将其作为硬安全控制之前，请先查看以下限制。

### 安全限制

* **网络过滤**：沙箱限制进程可以连接到哪些域。默认情况下，内置代理不会终止或检查出站流量的 TLS，因此不会检查加密连接的内容。实验性 [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) 设置在 [`mask` 凭证替换 ](<#掩码凭证>) 的代理处终止 TLS，但不添加内容过滤。您有责任确保您的策略中仅允许受信任的域。



**注意：**

允许广泛的域（例如 `github.com`）可以创建数据泄露的路径。由于代理根据客户端提供的主机名做出允许决定，而不检查 TLS，因此沙箱内运行的代码可能会使用 [ 域前端 ](<https://en.wikipedia.org/wiki/Domain_fronting>) 或类似技术来访问允许列表之外的主机。如果您的威胁模型需要更强的保证，请配置终止 TLS 并检查流量的 [ 自定义代理 ](<#自定义代理配置>)，并在沙箱内安装其 CA 证书。更强的TLS-感知网络隔离是一个活跃的开发领域。


* **通过 Unix 套接字进行权限升级**：`allowUnixSockets` 配置可能会无意中授予对系统服务的访问权限，从而导致沙箱绕过。例如，允许访问 `/var/run/docker.sock` 可以有效地授予通过 Docker 套接字访问主机系统的权限。仔细考虑允许通过沙箱的任何 Unix 套接字。
* **文件系统权限提升**：过于广泛的文件系统写入权限可能会导致权限提升攻击。允许写入 `$PATH` 中包含可执行文件的目录、系统配置目录或用户 shell 配置文件（例如 `.bashrc` 或 `.zshrc`）可能会导致当其他用户或系统进程访问这些文件时在不同的安全上下文中执行代码。
* **Linux 沙箱强度**：Linux 实现提供强大的文件系统和网络隔离，但包含 `enableWeakerNestedSandbox` 模式，使其能够在没有特权命名空间的 Docker 环境中工作。此选项会大大削弱安全性，并且仅应在以其他方式强制实施额外隔离时使用。
* **macOS** 上的 Apple 事件：macOS 沙箱默认阻止 Apple 事件。 `allowAppleEvents` 设置解除了此限制，因此 `open` 和 `osascript` 等工具可以工作，但它消除了代码执行隔离：沙盒命令可以在没有用户提示的情况下启动未沙盒的其他应用程序，并且可以将 AppleScript 命令发送到正在运行的应用程序，具体取决于每个应用程序 macOS自动化同意提示（TCC）。它仅在用户、托管或 CLI 设置中受到尊重。项目设置无法启用它。

### 适用范围

沙箱隔离 shell 命令及其子进程。 [沙箱外部运行的内容](<#什么在沙箱之外运行>)列出了它未涵盖的工具和帮助程序进程。与沙箱相关的计算机使用和子代理如下：

* **计算机使用**：当Claude打开应用程序并控制您的屏幕时，它会在您的实际桌面上运行，而不是在隔离环境中运行。每个应用程序的权限提示会控制每个应用程序。请参阅[计算机在CLI](<https://code.claude.com/docs/en/computer-use>)中的使用或[计算机在桌面](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>)中的使用。
* **子代理**：[子代理](<https://code.claude.com/docs/en/sub-agents>)在与父会话相同的进程中运行，并使用相同的沙箱配置。当在父会话中启用沙箱时，子代理内的 Bash 命令将被沙箱化。
* **后台会话**：[后台会话](<https://code.claude.com/docs/en/agent-view>)在其自己的进程中运行，当[其设置](<https://code.claude.com/docs/en/agent-view#settings-and-provider>)启用沙箱时，其Bash命令将被沙箱化。
* **围绕整个进程的边界**：将进程放在[沙箱外部运行的进程](<#什么在沙箱之外运行>)也位于边界后面，在[沙箱内运行Claude Code（本地模式）运行时](<https://code.claude.com/docs/en/sandbox-environments#sandbox-runtime>)，其网络白名单仅限于您批准的主机，或者在[开发容器](<https://code.claude.com/docs/en/devcontainer>)及其防火墙脚本中。有关后台服务及其托管的会话，请参阅 [在公司启动器后面运行 Claude Code ](<https://code.claude.com/docs/en/corporate-launcher>)。
* **Mods**：[mod](<https://code.claude.com/docs/en/plugins/mods/overview>)是一个在Claude Code内部运行自己的代码的插件，并且mod启动的进程在沙箱外部运行。请参阅[mod 可以达到什么水平](<https://code.claude.com/docs/en/plugins/mods/overview#what-a-mod-can-reach>)。



**注意：**

有效的沙箱需要文件系统和网络隔离。如果没有网络隔离，受损的 agent 可能会泄露敏感文件，例如 SSH 密钥。如果没有文件系统隔离，无论是通过许可策略还是通过 [ 禁用文件系统层 ](<#禁用文件系统隔离>)，受损的 agent 都可以为系统资源设置后门以获得网络访问权限。当您扩大默认值时，请检查 `allowWrite` 路径、广泛的 `allowedDomains` 条目或 `excludedCommands` 例外是否不会撤消另一侧的限制。


## 另请参阅

* [沙箱环境](<https://code.claude.com/docs/en/sandbox-environments>)：将内置沙箱与开发容器、容器和虚拟机进行比较
* [安全](<https://code.claude.com/docs/en/security>)：安全功能和最佳实践
* [权限](<https://code.claude.com/docs/en/permissions>)：权限配置和访问控制
* [所有设置](<https://code.claude.com/docs/en/settings-reference>)：各设置键
* [CLI参考](<https://code.claude.com/docs/en/cli-reference>)：命令行选项


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Configure the sandboxed Bash tool

> Restrict the files and network hosts Claude Code's shell commands can reach with the built-in sandbox. Turn it on, set the boundary, and fix what it breaks.

The Bash sandbox is a boundary that the operating system enforces around the shell commands Claude runs on your machine. You set which files and network domains those commands can reach, and the limits apply to Bash, PowerShell, and Monitor commands and the processes they start. Because the operating system applies the limits while a command runs, Claude Code can [run sandboxed commands without asking you](<#sandbox-modes>) to approve each one.

The sandbox covers shell commands only. Claude's file tools, MCP servers, and hooks [run outside it](<#what-runs-outside-the-sandbox>).

The sandbox runs on macOS, Linux, and WSL2. On native Windows, Claude Code runs commands unsandboxed. To use the sandbox on a Windows machine, run Claude Code inside a WSL2 distribution.



**注释：**

  This page covers the sandbox around shell commands on your own machine. Other pages cover related questions:

  * For how a cloud session is isolated, see [Security and isolation](<https://code.claude.com/docs/en/claude-code-on-the-web#security-and-isolation>)
  * To compare other isolation approaches such as dev containers, custom containers, and virtual machines, see [Sandbox environments](<https://code.claude.com/docs/en/sandbox-environments>)
  * To reduce permission prompts for tools other than Bash, see [permission modes](<https://code.claude.com/docs/en/permission-modes>)



## What the sandbox restricts

While the sandbox is on, the shell commands Claude runs start inside its boundary, and so do the processes they start. The sandbox is off by default. To turn it on, run `/sandbox` in a session, as [Get started](<#get-started>) shows, or set [`sandbox.enabled`](<https://code.claude.com/docs/en/settings-reference#sandbox-enabled>) to `true` in a [settings file](<https://code.claude.com/docs/en/settings>) such as `~/.claude/settings.json`.

The table shows what a sandboxed command can reach by default and the settings that change each default.

| Access | Default | Change it with |
| :- | :- | :- |
| Writes | The working directory, a per-user temp directory, and [directories you've added](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>). [Protected paths](<#protected-paths>) stay write-denied | [`filesystem.allowWrite`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-allowwrite>), [`filesystem.denyWrite`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-denywrite>) |
| Reads | Most of the machine, including credential files such as `~/.ssh` and `~/.aws/credentials` | [`filesystem.denyRead`](<https://code.claude.com/docs/en/settings-reference#sandbox-filesystem-denyread>), [`credentials`](<#protect-credentials>) |
| Network | No direct route out. Connections go through a proxy on your machine that checks each host against your allowed domains, which start empty. Your permission mode decides [what happens to other hosts](<#hosts-outside-your-allowed-domains>) | [`network.allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>), [`network.deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) |
| Environment variables | Inherited from Claude Code, including any secrets in its environment | [`credentials`](<#protect-credentials>), [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>) |

Claude Code builds the sandbox on the open source [`@anthropic-ai/sandbox-runtime`](<https://github.com/anthropics/sandbox-runtime>) package.

### What runs outside the sandbox

The sandbox wraps shell commands. These tools and processes run outside it:

* **Built-in file and web tools**: tools such as Read, Edit, Write, WebFetch, and WebSearch follow [permission rules](<https://code.claude.com/docs/en/permissions>) instead. A `denyRead` entry doesn't stop the Read tool, and `allowedDomains` doesn't limit WebFetch
* **Other processes Claude Code starts**: command [hooks](<https://code.claude.com/docs/en/hooks>), local [MCP servers](<https://code.claude.com/docs/en/mcp>), [plugin monitors](<https://code.claude.com/docs/en/plugins/components#monitors>), [LSP servers](<https://code.claude.com/docs/en/tools-reference#lsp-tool-behavior>), and helper commands such as your [status line](<https://code.claude.com/docs/en/statusline>) command and `apiKeyHelper` run with your full access

Some shell commands also run outside the sandbox, depending on your settings:

* **Commands you type yourself**: a command you enter at the [`!` shell-mode prompt](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) runs unsandboxed in most sessions. [Strict sandbox mode](<#turn-off-the-retry-with-strict-sandbox-mode>) lists the sessions where a command you type runs sandboxed
* **Excluded commands**: commands that match [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>) run unsandboxed
* **Unsandboxed retries**: Claude can [ask to run a command unsandboxed](<#the-unsandboxed-retry-escape-hatch>), usually after it fails in the sandbox

To put the tools, processes, and commands in this section behind one boundary, run the Claude Code process itself in a [container, virtual machine, or the sandbox runtime](<https://code.claude.com/docs/en/sandbox-environments>).

## Get started

The sandbox is built into Claude Code. What you install depends on your platform:

* **macOS**: sandboxing uses the built-in Seatbelt framework, so you can go straight to the steps
* **Linux and WSL2**: the sandbox relies on `bubblewrap` and `socat`, covered in [Set up Linux and WSL2](<#set-up-linux-and-wsl2>). Even if you haven't installed them yet, you can start with `/sandbox`, because its panel shows whether anything is missing



  

#### Run /sandbox

    Start a Claude Code session and run the `/sandbox` command:

    ```text theme={null}
    /sandbox
    ```

    This opens the sandbox panel with three tabs, plus a Dependencies tab on Linux when the optional seccomp filter is missing:

    * **Mode**: choose how sandboxed commands are approved, covered in the next step
    * **Overrides**: choose whether commands that fail under the sandbox can fall back to running unsandboxed. This is the [`allowUnsandboxedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowunsandboxedcommands>) setting
    * **Config**: view the resolved sandbox settings

    If the panel shows only a Dependencies tab, a required package is missing. Install it as described in [Set up Linux and WSL2](<#set-up-linux-and-wsl2>), restart Claude Code, and run `/sandbox` again.
  


  

#### Choose a mode

    On the Mode tab, select auto-allow or regular permissions. Auto-allow runs sandboxed commands without prompting, and regular permissions keeps the regular permission prompts even when commands are sandboxed. See [Sandbox modes](<#sandbox-modes>) for which commands still prompt in auto-allow mode.
  


  

#### Run a Bash command

    Ask Claude to run a command, such as a build or a test suite. By default, commands inside the sandbox can write to the working directory, a [per-user temp directory](<https://code.claude.com/docs/en/env-vars>), and any [directories you've added](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>) with `--add-dir`, `/add-dir`, or `permissions.additionalDirectories`.

    The first time a command needs a new network domain, Claude Code prompts for approval; in [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), Claude instead names the hosts a command needs [on the command itself](<#per-command-allowed-domains-in-auto-mode>) for the classifier to review with it.

    To widen or narrow what the sandbox allows, see [Configure sandboxing](<#configure-sandboxing>).

    If sandboxed commands fail with `Operation not permitted` inside a container, see [Bubblewrap fails to start inside a container](<#bubblewrap-fails-to-start-inside-a-container>).
  


When you select a mode in the panel, Claude Code saves it to your project's local settings at `.claude/settings.local.json`, which apply to the current project. Claude Code adds that file to your global gitignore when it saves a setting there. To enable the sandbox across all of your projects, set [`sandbox.enabled`](<https://code.claude.com/docs/en/settings-reference#sandbox-enabled>) to `true` in your user settings at `~/.claude/settings.json`. To enforce sandboxing for every developer in an organization, use [managed settings](<#enforce-sandboxing-with-managed-settings>).

To change the sandbox for one session without writing to a settings file, start Claude Code with [`--settings`](<https://code.claude.com/docs/en/settings#change-a-setting-for-one-session>). For example, this command starts a sandboxed session in which Claude can't retry a blocked command outside the sandbox:

```bash theme={null}
claude --settings '{"sandbox": {"enabled": true, "allowUnsandboxedCommands": false}}'
```



**注意：**

  By default, if the sandbox can't start because a dependency is missing or the platform is unsupported, Claude Code runs commands without sandboxing. To make Claude Code exit at startup instead, set [`sandbox.failIfUnavailable`](<https://code.claude.com/docs/en/settings-reference#sandbox-failifunavailable>) to `true`. Managed deployments that require sandboxing as a security gate can use this setting.


### Confirm commands run inside the sandbox

To check that the sandbox is working, ask Claude to run each line in the table. What you type at the [`!` prompt](<#what-runs-outside-the-sandbox>) usually runs outside the sandbox, so typing a line yourself doesn't test it.

| Command | Result inside the sandbox |
| :- | :- |
| `touch ~/sandbox-probe` | Fails with `Operation not permitted` on macOS, or `Read-only file system` on Linux and WSL2 |
| `curl --noproxy '*' https://example.com` | Fails with `Could not resolve host`, because the command has no route around the sandbox proxy |

If Claude asks to retry a failed command outside the sandbox, decline the retry. If `touch` succeeds and your home directory isn't one of the directories the sandbox lets commands write to, delete `~/sandbox-probe`. Then run `/sandbox` to check that the sandbox is on and its dependencies are installed.

### Set up Linux and WSL2

On Linux and WSL2, the sandbox relies on these packages:

* [`bubblewrap`](<https://github.com/containers/bubblewrap>): the unprivileged sandboxing tool that enforces filesystem isolation
* [`socat`](<http://www.dest-unreach.org/socat/>): the relay used to route network traffic through the sandbox proxy

Install them with your distribution's package manager:



  

#### Ubuntu/Debian

    ```bash theme={null}
    sudo apt-get install bubblewrap socat
    ```
  


  

#### Fedora

    ```bash theme={null}
    sudo dnf install bubblewrap socat
    ```
  


When a dependency is missing, the Dependencies tab in `/sandbox` lists which of `ripgrep`, `bubblewrap`, `socat`, and the seccomp filter your platform lacks. If you don't see the tab after installing and restarting Claude Code, all dependencies are present.

Ripgrep is bundled with the native Claude Code binary. The seccomp filter is optional and adds Unix domain socket blocking. Install it with `npm install -g @anthropic-ai/sandbox-runtime` if it is missing.

When a required dependency is missing, the Dependencies tab is the only tab shown until you install it. When only the optional seccomp filter is missing, the Dependencies tab appears alongside the other tabs. The dependency check runs at startup, so restart Claude Code after installing packages for `/sandbox` to detect them.



  

#### Ubuntu 24.04 and later: allow bubblewrap to create user namespaces

    On Ubuntu 24.04 and later, the default AppArmor policy prevents bubblewrap from creating the user namespaces it needs for isolation.

    To check whether your environment enforces this restriction, including inside WSL2, run `sysctl kernel.apparmor_restrict_unprivileged_userns`. If the command returns `0`, skip this step. If it prints a `No such file or directory` error, the key doesn't exist and you can skip this step. If it returns `1`, add an AppArmor profile that grants `bwrap` this capability:

    ```bash theme={null}
    sudo tee /etc/apparmor.d/bwrap > /dev/null <<'EOF'
    abi <abi/4.0>,
    include <tunables/global>

    profile bwrap /usr/bin/bwrap flags=(unconfined) {
      userns,
      include if exists <local/bwrap>
    }
    EOF
    ```

    The profile applies only to `bwrap` itself, not to the commands it runs inside the sandbox. Reload AppArmor to apply it:

    ```bash theme={null}
    sudo systemctl reload apparmor
    ```
  


  

#### WSL2 notes

    Check your WSL version with `wsl -l -v` from PowerShell. If you see `Sandboxing requires WSL2`, your distribution is running WSL1. Upgrade it to WSL2 or run Claude Code without sandboxing.

    On WSL2, WSL hands a launch of a Windows binary such as `cmd.exe`, `powershell.exe`, or anything under `/mnt/c/` to the Windows host over a Unix socket, so whether a sandboxed command can launch one follows the sandbox's [Unix-socket settings](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowunixsockets>): the optional seccomp filter has to be installed to block the socket in the first place. To allow these launches, set `allowAllUnixSockets`, which opens every Unix socket to sandboxed commands.
  


### Sandbox modes

Claude Code offers two sandbox modes. In both, the sandbox enforces the same filesystem and network restrictions; the difference is only in whether sandboxed commands are auto-approved or require explicit permission.

#### Auto-allow mode

Claude Code approves a command automatically, with no prompt, when the command runs inside the sandbox. A command goes through the regular [permission flow](<https://code.claude.com/docs/en/permissions>) when it runs outside the sandbox because it matches [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>) or because Claude [retries it unsandboxed](<#the-unsandboxed-retry-escape-hatch>).

A sandboxed command that connects to a host you haven't allowed stays in the sandbox. [Hosts outside your allowed domains](<#hosts-outside-your-allowed-domains>) covers who decides whether the connection goes through.

Even in auto-allow mode, the following still apply:

* Explicit [deny rules](<https://code.claude.com/docs/en/permissions>) are always respected
* `rm` or `rmdir` commands that target a [critical path](<https://code.claude.com/docs/en/permission-modes#critical-paths>) still go through the regular permission flow
* Content-scoped [ask rules](<https://code.claude.com/docs/en/permissions>) like `Bash(git push *)` still force a prompt even for sandboxed commands
* A bare `Bash` ask rule, or the equivalent `Bash(*)` form, is skipped for commands that run sandboxed; it still applies to commands that fall back to the regular permission flow. In [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>), the rule isn't skipped: it prompts for sandboxed commands too, including read-only ones



**说明：**

  Auto-allow mode works independently of your permission mode setting, with three exceptions: [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>), an auto mode command that carries [per-command allowed domains](<#per-command-allowed-domains-in-auto-mode>), and [server-side classifier review](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>) of sandboxed commands in auto mode. Even if you're not in "accept edits" mode, sandboxed Bash commands run automatically when auto-allow is enabled. This means Bash commands that modify files within the sandbox boundaries execute without prompting, even in Manual mode, where the file edit tools would prompt.

  In plan mode, auto-allow doesn't widen approvals; see [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) for how Claude Code gates commands while you plan.


#### Regular permissions mode

All Bash commands go through the regular permission flow, even when sandboxed. This provides more control but requires more approvals.

#### The unsandboxed retry escape hatch

The unsandboxed retry is an escape hatch for commands that fail inside the sandbox, such as tools that are incompatible with it. When the sandbox blocks a network connection, Claude Code names the denied host in the command's result, so Claude sees what was blocked. Claude analyzes the failure and may retry the command with the `dangerouslyDisableSandbox` parameter.

The retried command runs unsandboxed. In an interactive terminal session, who approves it depends on your permission mode:

* **`bypassPermissions` mode**: the retry runs without a prompt
* **Manual mode and `acceptEdits` mode**: you get a prompt titled "Bash command (unsandboxed)"
* **[Auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)**: a separate classifier model evaluates the underlying command
* **`dontAsk` mode**: Claude Code denies the retry
* **Plan mode**: see [how Claude Code gates commands while you plan](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>)

These rules and settings change who approves the retry:

* **A matching allow rule**: if an allow rule such as `Bash(curl *)` matches the command, it also approves the retry, so the command runs outside the sandbox with no prompt
* **An ask rule for the parameter**: add an [ask rule](<https://code.claude.com/docs/en/permissions#match-by-input-parameter>) for `Bash(dangerouslyDisableSandbox:true)` to be prompted on Bash retries. You get the prompt in auto mode and `bypassPermissions` mode too, and the rule takes precedence over a matching allow rule
* **[`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>)**: [Actions no mode auto-approves](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>) covers the retries that prompt while it's on

#### Turn off the retry with strict sandbox mode

You can disable the unsandboxed retry by setting `"allowUnsandboxedCommands": false` in your [sandbox settings](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>). With the retry disabled, Claude Code ignores the `dangerouslyDisableSandbox` parameter. While the sandbox is running, commands Claude runs are then sandboxed unless they match an `excludedCommands` entry. To keep Claude Code from running commands unsandboxed when the sandbox can't start, also set [`failIfUnavailable`](<https://code.claude.com/docs/en/settings-reference#sandbox-failifunavailable>). The `/sandbox` **Overrides** tab shows this setting as **Strict sandbox mode**.

A `false` in your user settings, `--settings`, or managed settings holds even when a project's settings set `true`. A `false` in your user settings doesn't make the sandbox admin-required, so a project's other sandbox settings still apply. Before v2.1.285, a project's `true` overrode a `false` in your user settings.

If you or your administrator disable the retry in managed settings or with the `--settings` flag, the sandbox becomes admin-required. Claude Code then ignores the settings in a repository's files that loosen the sandbox, `excludedCommands` entries included. [Repository settings under an admin-required sandbox](<#repository-settings-under-an-admin-required-sandbox>) lists them.

Strict sandbox mode applies to the commands Claude runs. Commands you type yourself at the [`!` shell-mode prompt](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) run outside the sandbox unless the session is one of these:

* **A [background session](<https://code.claude.com/docs/en/agent-view>)**: strict sandbox mode covers shell-mode commands too
* **A Linux session with [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars#variables>) set**: every command runs sandboxed, shell-mode commands included

Before v2.1.260, strict sandbox mode sandboxed shell-mode commands in every session.

#### Temporary directories

A per-user temp directory is writable inside the sandbox by default, alongside the working directory. Unless you [disable filesystem isolation](<#disable-filesystem-isolation>), Claude Code sets `$TMPDIR` to this directory for sandboxed commands, so tools that write temporary files work without extra configuration.

Unsandboxed commands inherit your shell's `$TMPDIR` when it is set, so while filesystem isolation is on, sandboxed and unsandboxed commands resolve `$TMPDIR` to different directories. If your shell leaves `$TMPDIR` unset or empty, an unsandboxed command that references `$TMPDIR` receives your [`CLAUDE_CODE_TMPDIR`](<https://code.claude.com/docs/en/env-vars>) override, or the operating system's temp directory when you haven't set one or the override is a long path, so the variable doesn't expand to an empty string. To pass temporary files between the two, write them under the working directory instead.

## Configure sandboxing

Customize sandbox behavior through your `settings.json` file. See [Settings](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>) for the complete configuration reference.

By default, sandboxed commands can write to the current working directory, the per-user temp directory, and any [directories you've added](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>) with `--add-dir`, `/add-dir`, or `permissions.additionalDirectories`. If subprocess commands like `kubectl`, `terraform`, or `npm` need to write outside those directories, use `sandbox.filesystem.allowWrite` to grant access to specific paths:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "allowWrite": ["~/.kube", "/tmp/build"]
    }
  }
}
```

These paths are enforced at the OS level, so all commands running inside the sandbox, including their child processes, respect them. This is the recommended approach when a tool needs write access to a specific location, rather than excluding the tool from the sandbox entirely with `excludedCommands`.

When you define the same filesystem array in multiple [settings scopes](<https://code.claude.com/docs/en/settings#settings-precedence>), Claude Code merges them, combining the paths rather than replacing one scope's array with another's. Claude Code leaves an entry out of the merge when a lock under [Keep developers from widening the policy](<#keep-developers-from-widening-the-policy>) covers it.

If you exclude a source with [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference>) on the CLI or [`settingSources`](<https://code.claude.com/docs/en/agent-sdk/claude-code-features#control-filesystem-settings-with-settingsources>) in the Agent SDK, Claude Code ignores its `sandbox.filesystem` entries, its `Edit` permission rules, and its `Read` deny rules when building the sandbox configuration. Requires Claude Code v2.1.246 or later.

When you edit these filesystem lists during a session, Claude Code [applies the change to the running session](<https://code.claude.com/docs/en/settings#when-edits-take-effect>), so the next sandboxed command runs under the new paths.

Sandbox filesystem paths use standard conventions: `/tmp/build` is absolute and `~/.kube` is relative to your home directory. This differs from [Read and Edit permission rules](<https://code.claude.com/docs/en/permissions#read-and-edit>), which use `//path` for absolute and `/path` for project-relative. For relative paths, trailing slashes, and wildcards, see [Sandbox path prefixes](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>).

You can also deny write or read access using `sandbox.filesystem.denyWrite` and `sandbox.filesystem.denyRead`, and re-allow specific paths within a denied region using `sandbox.filesystem.allowRead`. When read rules overlap, the rule with the narrower path applies:

| Example rules | Result |
| :- | :- |
| `"denyRead": ["~/"]` with `"allowRead": ["~/projects"]` | `~/projects` is readable and the rest of the home directory stays blocked. The narrower allow re-opens that part of the denied region |
| `"allowRead": ["~/"]` with `"denyRead": ["~/.env"]` | `~/.env` stays blocked and the rest of the home directory is readable. The deny holds inside a wider allow, so a broad allow can't silently re-expose a secret |
| `"allowRead": ["~/"]` with `"denyRead": ["~/**/.env"]` | Every `.env` under the home directory stays blocked and the rest is readable. A [wildcard deny](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>) holds inside a wider allow the same way an exact path does |

The example below blocks reading from the entire home directory while still allowing reads from the current project. Place it in your project's `.claude/settings.json`, because the relative path `.` resolves to the project root when the configuration lives in project settings:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "denyRead": ["~/"],
      "allowRead": ["."]
    }
  }
}
```

If you placed the same configuration in `~/.claude/settings.json`, `.` would resolve to `~/.claude` instead, and project files would remain blocked by the `denyRead` rule.

To deny sandboxed commands read access to home directories and mounted volumes while keeping the working directories readable, set [`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>) instead of writing path rules.

### Run commands outside the sandbox with `excludedCommands`

List a command pattern in [`sandbox.excludedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>) to run matching commands outside the sandbox, which means no filesystem restrictions and no network proxy. Use it for a tool that can't work inside the sandbox and that you trust with your full access. A tool that needs one more directory or one more host may work with `allowWrite` or `allowedDomains`, which keep the command sandboxed.

This example takes `docker compose` commands out of the sandbox. Save it in `~/.claude/settings.json` to apply it to all of your projects:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "excludedCommands": ["docker compose *"]
  }
}
```

Claude Code checks your entries against each Bash and Monitor call. A call is the whole command line Claude sends, which can chain several commands. The following rules decide whether a call leaves the sandbox:

* **End the pattern with ` *`**: entries use the same syntax as a `Bash(...)` [permission rule](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>), where a pattern with no wildcard is an exact match. `docker` matches only `docker` with no arguments. `docker *` matches `docker` with or without arguments
* **Every command in the call has to match**: `npm ci && docker compose build` stays sandboxed unless another entry covers `npm ci`
* **Claude Code matches the text of the call**: a script or `make` target that calls `docker` internally doesn't match, and neither does `/usr/local/bin/docker`
* **Some calls stay sandboxed**: a redirect to a file, a `cd`, or a command substitution such as `$(...)` keeps the whole call sandboxed. The [reference entry](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>) lists more calls that stay sandboxed
* **Where you save the entry can matter**: while the sandbox is [admin-required](<#repository-settings-under-an-admin-required-sandbox>), Claude Code ignores entries in `.claude/settings.json` and `.claude/settings.local.json`

An excluded command goes through the regular permission flow:

* [Read-only commands](<https://code.claude.com/docs/en/permissions#read-only-commands>) and commands your allow rules cover run without a prompt
* In auto mode, the classifier reviews other excluded commands
* In `bypassPermissions` mode, an excluded command runs without a prompt unless an ask rule matches it

To confirm an entry matches, switch to Manual mode and ask Claude to run a matching command that changes something, such as `docker compose up -d`. The permission prompt is titled "Bash command (unsandboxed)".



**注意：**

  An excluded command runs with your full access. A broad entry such as `docker *` covers everything that tool can do. If you write a pattern that covers an interpreter, a script inside your working directory, or a tool that acts on a file there, as `docker compose` does with its compose file, Claude can write that file and then run it outside the sandbox. A narrower pattern leaves less that Claude can run outside the sandbox.


### Disable filesystem isolation

Set `sandbox.filesystem.disabled` to `true` to skip filesystem isolation while keeping network isolation. The example below turns off filesystem isolation while keeping an allowlist of network domains:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "filesystem": {
      "disabled": true
    },
    "network": {
      "allowedDomains": ["github.com", "*.npmjs.org"]
    }
  }
}
```

The sandbox has two independent layers: [filesystem isolation](<#filesystem-isolation>) controls which paths sandboxed commands can read and write, and [network isolation](<#network-isolation>) controls which domains they can reach. With the filesystem layer off, sandboxed commands get unrestricted read and write access to the host filesystem, while their network egress stays confined to your allowed domains. Turn the layer off when you sandbox to control where commands connect rather than what they write.

`sandbox.filesystem.disabled` defaults to `false`. Requires Claude Code v2.1.216 or later.



**注意：**

  With filesystem isolation off and commands auto-allowed, a sandboxed command can write files that later commands run or read, such as shell startup files, executables on `$PATH`, or `~/.claude/settings.json`, and use them to widen its own access on the next run. Set `filesystem.disabled` to `true` only for workloads you trust not to escalate their own access. Locking network domains with [`allowManagedDomainsOnly`](<#keep-developers-from-widening-the-policy>) narrows the risk but doesn't remove it, since that lock applies only to commands running inside the sandbox.


#### Which settings can disable it

Because turning filesystem isolation off widens what sandboxed commands can do, Claude Code honors `filesystem.disabled` from these settings sources only:

* User settings, managed settings, and the `--settings` CLI flag can set it. Project settings in `.claude/settings.json` and `.claude/settings.local.json` can't, so a checked-out project can't switch filesystem isolation off.
* When managed settings configure `sandbox.filesystem` at all, or list any `sandbox.credentials.files` entry with `"mode": "deny"`, only managed settings can set the key. This keeps administrator-deployed filesystem restrictions in force; to relax such a deployment, set `"disabled": true` in managed settings.
* When [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>) is set, Claude Code ignores `filesystem.disabled` from every source, including managed settings, and keeps filesystem isolation on.

A [valid](<https://code.claude.com/docs/en/settings-reference#invalid-credential-entries-in-managed-settings>) `mask` entry doesn't lock the key, even when Claude Code [falls back to `deny`](<#mask-credential-files>) for it at startup. List a path that can't be masked, such as a credential directory, as an explicit `deny` entry in managed settings, which locks the key.

#### What changes when filesystem isolation is off

Setting `filesystem.disabled` lifts the protections the filesystem layer itself enforces. Protections that other layers enforce keep applying:

| Protection | With filesystem isolation off |
| - | - |
| `filesystem.denyRead` and [`credentials.files`](<#protect-credentials>) `deny` read blocks | Not enforced. The filesystem layer applies both |
| `credentials.envVars` `deny` and `mask` entries | Enforced. Environment variable scrubbing is independent of the filesystem layer |
| [`credentials.files` `mask` entries](<#mask-credential-files>) applied as masks | Enforced: masking is independent of the filesystem layer. An entry that [fell back to `deny`](<#mask-credential-files>) is not enforced, like any `deny` entry |

Two other things change:

* Sandboxed commands inherit your shell's `$TMPDIR` instead of the per-user temp directory, because every temp directory is writable and Claude Code no longer redirects commands to the per-user one.

  On Linux the variable is often unset in the parent shell. The Bash tool guidance tells Claude to create scratch directories with `mktemp -d` instead of relying on `$TMPDIR`.
* [`autoAllowBashIfSandboxed`](<https://code.claude.com/docs/en/settings-reference#sandbox-autoallowbashifsandboxed>) still defaults to `true`, so sandboxed commands keep running without prompts. Set it to `false` to prompt for sandboxed commands.

### Protect credentials

The `sandbox.credentials` setting declares credential files and environment variables to protect from sandboxed commands. Each entry names a file path or an environment variable and a `mode`. The dedicated `credentials` block keeps credential rules grouped together and separate from general filesystem rules.

For entries with `"mode": "deny"`, file paths are denied for reads inside the sandbox, the same restriction that `filesystem.denyRead` applies, and environment variables are unset before each sandboxed command runs. The file protection is part of the filesystem layer, so it doesn't apply if you [disable filesystem isolation](<#disable-filesystem-isolation>); the environment variable protection still does.

The example below blocks reads of the AWS credentials file and the SSH directory and removes `GITHUB_TOKEN` and `NPM_TOKEN` from the environment of sandboxed commands:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "credentials": {
      "files": [
        { "path": "~/.aws/credentials", "mode": "deny" },
        { "path": "~/.ssh", "mode": "deny" }
      ],
      "envVars": [
        { "name": "GITHUB_TOKEN", "mode": "deny" },
        { "name": "NPM_TOKEN", "mode": "deny" }
      ]
    }
  }
}
```

Environment variable entries and file entries also accept `"mode": "mask"`, described under [Mask credentials](<#mask-credentials>).

File paths follow the same [prefix rules](<https://code.claude.com/docs/en/settings-reference#sandbox-path-prefixes>) as `sandbox.filesystem.*` settings.

Claude Code merges the `deny` entries from every [settings scope](<https://code.claude.com/docs/en/settings#settings-precedence>) the session loads. A `deny` entry only ever narrows access, so any scope can add one, but no scope can remove one that another scope added.

When you [exclude a settings source](<#configure-sandboxing>):

* **Project or local settings**: Claude Code applies none of their `credentials` entries. Requires Claude Code v2.1.246 or later.
* **User settings**: Claude Code still applies the `deny` entries in `~/.claude/settings.json` and keeps its [file `mask` entries](<#mask-credential-files>) as restrictions that no longer authorize the proxy to substitute the real value, but drops its [environment variable `mask` entries](<#mask-environment-variables>).

There is no built-in credential deny list, so only the files and variables you list are restricted.

`sandbox.credentials` affects sandboxed Bash commands only. To strip credentials from all subprocesses regardless of sandboxing, set [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars>).

### Mask credentials

When you mask a credential, Claude Code shows sandboxed commands a per-session placeholder called the sentinel, and the [sandbox proxy](<#network-isolation>) substitutes the real value on outbound requests to hosts you allow. A `deny` entry under [Protect credentials](<#protect-credentials>) blocks the credential instead. For files on macOS, Claude Code [blocks the file instead](<#mask-credential-files>) of masking it. The [`sandbox.credentials`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials>) reference lists every field.

Masking requires the following:

* **TLS termination**: the proxy substitutes the real value inside request contents, so it has to see them. Set [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) so the proxy terminates TLS itself. Without it, masking fails without exposing anything: the command still sees only the sentinel, but the sentinel reaches the server unchanged and authentication fails. To check for this misconfiguration, run `claude doctor` in your terminal and look for the `TLS termination is unavailable` warning.
* **An allowed destination**: each `mask` entry can list `injectHosts`, the hosts the real value is allowed to reach. The proxy injects only on connections the [domain allowlist](<#network-isolation>) admits, so each `injectHosts` host must also be reachable through `network.allowedDomains`. For a `mask` entry with no `injectHosts`, the proxy substitutes the real value on requests to every host in `network.allowedDomains`.
* **A trusted settings scope**: masking authorizes the proxy to send your real credential somewhere, so Claude Code honors `mask` entries, `network.tlsTerminate`, [`credentials.allowPlaintextInject`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-allowplaintextinject>), `awsPairs`, and `sigv4` only from user settings, managed settings, and the `--settings` flag. It ignores them in a repository's `.claude/settings.json` or `.claude/settings.local.json`. When your administrator delivers `mask` entries, `network.tlsTerminate`, or `credentials.allowPlaintextInject` through server-managed settings, they count as [settings that need approval](<https://code.claude.com/docs/en/server-managed-settings#security-approval-dialogs>).

#### Mask environment variables

To mask an environment variable, set `"mode": "mask"` on its `credentials.envVars` entry. The command and anything it logs never hold the real credential, but its requests still authenticate. When the same variable is listed with `deny` in any scope, `deny` takes precedence.

This example masks two tokens. `GH_TOKEN` is substituted only on requests to `api.github.com`, while `NPM_TOKEN` has no `injectHosts` and is substituted on requests to every host in `network.allowedDomains`:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "network": {
      "tlsTerminate": {},
      "allowedDomains": ["*.github.com", "registry.npmjs.org"]
    },
    "credentials": {
      "envVars": [
        { "name": "GH_TOKEN", "mode": "mask", "injectHosts": ["api.github.com"] },
        { "name": "NPM_TOKEN", "mode": "mask" }
      ]
    }
  }
}
```

Masking replaces the whole value by default. For a value with structure, such as a `DATABASE_URL` connection string or a JWT, use the [`extract`, `decode`, `maskClaims`, and `onExtractNoMatch` fields](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-envvars>) so tools that parse the value keep working.

<span id="ipv6-destinations-in-injecthosts" />For an IPv6 destination, spell the address differently in the two lists:

* **`network.allowedDomains`**: the bracketed form, such as `"[::1]"`
* **`injectHosts`**: the bare address in its canonical compressed form, such as `"::1"`

The proxy matches each `injectHosts` entry against the connection's bare destination address, ignoring ports, so a bracketed, zone-ID, or differently compressed spelling never matches. `claude doctor` flags entries that can never match with the warning `Sandbox credential injectHosts entries can never match their destination`. This check requires Claude Code v2.1.229 or later.

#### Re-sign AWS requests

AWS requests carry SigV4 signatures over the request contents, so mask `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` together. The proxy detects a SigV4 request by the access key's [sentinel](<#mask-credentials>) and re-signs the request with the real values, which requires Claude Code v2.1.221 or later. If you mask only the secret, requests are signed with a placeholder the proxy can't detect, so they fail at AWS.

Claude Code links the conventional `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_SESSION_TOKEN` variables into one credential automatically when you mask their whole values. If your AWS credential is in variables with other names, group them with [`credentials.awsPairs`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-awspairs>), which requires Claude Code v2.1.224 or later.

Streaming uploads, presigned URLs, and SigV4A requests carry signatures the proxy can't recompute. When one of these requests is signed with a masked pair's placeholder, the proxy fails it rather than forward a broken signature. Requests signed with unmasked credentials aren't affected. Use [`credentials.sigv4`](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-sigv4>), which requires Claude Code v2.1.224 or later, to forward one of these request forms instead. AWS still rejects the request, so the calling tool receives AWS's own rejection response instead of a proxy error.

#### Mask credential files

To mask a credential file, set `"mode": "mask"` on its `credentials.files` entry. Masking files requires Claude Code v2.1.221 or later. What a sandboxed command sees depends on the platform:

* **Linux and WSL2**: sandboxed commands read a [sentinel](<#mask-credentials>) copy of the file, and the proxy substitutes the real value on outbound requests.
* **macOS**: sandboxed commands can't read the file at all. Claude Code builds no sentinel copy, so tools that authenticate with the file don't work inside the sandbox, the same effect as `deny`. The read block holds even when you [disable filesystem isolation](<#disable-filesystem-isolation>).

This example masks a GitHub token stored in `~/.config/gh/hosts.yml`. The `extract` pattern marks which part of the file is the secret, so on Linux and WSL2 `gh` still parses the rest of its config:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "network": {
      "tlsTerminate": {},
      "allowedDomains": ["*.github.com"]
    },
    "credentials": {
      "files": [
        {
          "path": "~/.config/gh/hosts.yml",
          "mode": "mask",
          "extract": "oauth_token:\\s*(\\S+)",
          "injectHosts": ["api.github.com"]
        }
      ]
    }
  }
}
```

To confirm the mask is active, ask Claude to run `cat ~/.config/gh/hosts.yml` in a sandboxed command. On Linux and WSL2 the output shows a sentinel in place of the token, and on macOS the read fails.

Without `extract` or `decode`, Claude Code replaces the entire file with one sentinel, which suits a file holding a single bare secret. Use the [`extract`, `decode`, `maskClaims`, `onExtractNoMatch`, and `maskDuplicates` fields](<https://code.claude.com/docs/en/settings-reference#sandbox-credentials-files>) to control partial masking and what happens when the pattern matches nothing.



**注意：**

  When matching finds nothing to mask, the default `onExtractNoMatch` value, `warn`, skips the entry, so sandboxed commands can read the real file unmasked. On macOS, Claude Code applies `mask` entries as `deny` before the pattern runs whenever filesystem isolation is on, so the no-match outcomes take effect there only when [filesystem isolation is off](<#disable-filesystem-isolation>). The default suits credentials that may be legitimately absent. If the secret might be present but the pattern might miss it, use [`deny`](<https://code.claude.com/docs/en/settings-reference#mask-fields-for-files>).


`mask` applies to a single file, so list each credential file individually. Claude Code falls back to `deny` for a `mask` entry it can't mask safely: a directory path, a glob pattern, a file larger than 8 MiB, or a file that isn't UTF-8 text.

## How sandboxing works

### Filesystem isolation

The sandboxed Bash tool restricts file system access to specific directories:

* **Default write behavior**: read and write access to the current working directory and its subdirectories, any directories you've added with `--add-dir`, `/add-dir`, or [`permissions.additionalDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-additionaldirectories>), plus the per-user temp directory that `$TMPDIR` points to
* **Default read behavior**: read access to the entire computer, except certain denied directories. This default still allows reading credential files, so [protect credentials](<#protect-credentials>) you don't want commands to read.
* **Read block**: with [`permissions.blockReadsOutsideWorkingDirectories`](<https://code.claude.com/docs/en/settings-reference#permissions-blockreadsoutsideworkingdirectories>) on, sandboxed commands also lose read access to your home directory and the other directories that hold user files, apart from the paths that [Sandboxed commands under the block](<https://code.claude.com/docs/en/settings-reference#sandboxed-commands-under-the-block>) lists. That section also says when this part of the block doesn't apply.
* **Git worktrees**: when the working directory is a [linked git worktree](<https://code.claude.com/docs/en/worktrees>), the sandbox also allows writes to the main repository's shared `.git` directory so commands such as `git commit` can update refs and the index. Writes to `hooks/` and `config` inside that directory remain denied.

To skip filesystem isolation entirely while keeping network isolation, set [`sandbox.filesystem.disabled`](<#disable-filesystem-isolation>).

### Protected paths

Inside the directories that sandboxed commands can write to, the sandbox still denies writes to the files Claude Code loads configuration and code from. A command that could edit those files could grant itself permissions, or add a hook or MCP server that Claude Code runs outside the sandbox. The permission system has its own [protected paths](<https://code.claude.com/docs/en/permission-modes#protected-paths>), which control what Claude Code approves before a tool runs; the sandbox's list applies to a command that is already running. It covers four groups of paths:

* **In your working directory and the directories above it**: the `.claude` settings files, the `.claude/skills`, `.claude/agents`, `.claude/commands`, and `.claude/hooks` directories, `.mcp.json`, and the files Claude Code runs on its own, such as `.claude/workflows` and `.claude/scheduled_tasks.json`
* **In your working directory only**: shell startup files such as `.bashrc` and `.zshrc`, `.gitconfig`, the `.vscode` and `.idea` directories, and `hooks` and `config` inside `.git`
* **Files that would turn your working directory into a bare git repository**: `HEAD`, `objects`, and `refs` at the top level, plus existing `config` and `hooks` entries there when a `HEAD` sits beside them. A file named `config` is denied even with no `HEAD`. On Linux and WSL2, the sandbox deletes a top-level `HEAD` file or `objects` or `refs` directory that appears while a sandboxed command is running
* **In `~/.claude`, or the directory `CLAUDE_CONFIG_DIR` points to**: most of its contents, plus `~/.claude.json` and the `.credentials.json` credential store

If a symlink appears at a protected settings file's path during the session, the sandbox also denies writes to the file it points to, starting with the next command.

There is no way to exempt one of these paths: an `allowWrite` entry or an `Edit` allow rule that covers the path doesn't lift the protection. The only way to turn the protection off is [`filesystem.disabled`](<#disable-filesystem-isolation>), which turns off filesystem isolation for every path. To see most of these paths resolved for your machine, run `/sandbox` and open the **Config** tab, which lists them under **Denied within allowed**, mixed in with your own `denyWrite` entries.

If `git merge` or `git checkout` fails with `unable to unlink old` on one of these paths, see [A git command fails with `unable to unlink old`](<#a-git-command-fails-with-unable-to-unlink-old>).

### Network isolation

A sandboxed command has no direct route to the network:

* **Linux and WSL2**: the command runs in a separate network namespace that has no connection to your network
* **macOS**: the Seatbelt sandbox framework by default blocks connections other than the one to the sandbox proxy

Claude Code runs the sandbox proxy on your machine, outside the sandbox, and directs commands to it with `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`, and related environment variables. The proxy checks the hostname of each connection against your allowed and denied domains.

What a tool can reach depends on whether it uses the proxy:

* **Tools that read the proxy variables**: `curl`, `npm`, `git` over HTTPS, and similar tools connect once their host is allowed. An `allowedDomains` entry with no port allows every port on that host
* **Tools that ignore the proxy variables**: plain `ssh`, most database drivers, and similar tools can't connect, even to an allowed host. See [A database client or other non-HTTP tool fails to reach an allowed host](<#a-database-client-or-other-non-http-tool-fails-to-reach-an-allowed-host>)
* **Anything that isn't TCP**: UDP, HTTP/3 over QUIC, and ICMP tools such as `ping` can't leave the sandbox

The following settings and behaviors control which hosts the proxy allows:

* **Domain restrictions**: your allowed domains start empty. [Hosts outside your allowed domains](<#hosts-outside-your-allowed-domains>) covers what happens the first time a command needs a new domain.
* **Approval choices**: if you choose Yes when prompted, Claude Code allows the host for the rest of the current session. If you choose "Yes, and don't ask again", Claude Code saves a `WebFetch(domain:...)` allow rule to your [local settings](<https://code.claude.com/docs/en/permissions#permission-system>), so the host stays allowed in future sessions. While the sandbox is [admin-required](<#repository-settings-under-an-admin-required-sandbox>), Claude Code saves the rule to your user settings, where it applies in every project.
* **Pre-allowed domains**: pre-allow domains with [`allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>) to avoid the prompt entirely. Claude Code also pre-allows domains from `WebFetch(domain:...)` allow rules, as described in [Permission rules](<#permission-rules>).
* **Strict allowlist**: if you set [`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>) to `true` in user, managed, or CLI `--settings` settings, Claude Code denies sandboxed commands access to any host outside the allowlist instead of prompting. The allowlist is `allowedDomains` plus domains from `WebFetch(domain:...)` allow rules, or only the managed settings entries when `allowManagedDomainsOnly` is set. [Locks that apply without an admin-required sandbox](<#locks-that-apply-without-an-admin-required-sandbox>) covers a repository's entries. Claude Code enforces this for sandboxed commands only; in-process tools such as `WebFetch` still follow their [permission rules](<#permission-rules>). Setting it in a repository's `.claude/settings.json` or `.claude/settings.local.json` has no effect. Requires Claude Code v2.1.219 or later.
* **Managed lockdown**: if [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) is set in managed settings, non-allowed domains are blocked automatically instead of prompting, and only `allowedDomains` and `WebFetch(domain:...)` allow rules from managed settings are honored.
* **Corporate proxy**: when your network requires outbound traffic to go through a corporate proxy, set `HTTPS_PROXY`, `HTTP_PROXY`, and `NO_PROXY` as [proxy configuration](<https://code.claude.com/docs/en/network-config#proxy-configuration>) describes, in the `env` block of your settings so that [background agents](<https://code.claude.com/docs/en/network-config#set-network-variables-in-settings-not-the-shell>) get them too, or in the environment you launch Claude Code from. Claude Code enforces the domain allowlist and then tunnels allowed connections through that upstream proxy. `http://` and `https://` proxy URLs work, with basic authentication in the URL if you need it.

In a `WebFetch(domain:...)` rule, the sandbox honors two wildcard forms: a leading `*.`, such as `*.example.com`, and a bare `*`. The bare `*` form requires Claude Code v2.1.186 or later. A wildcard in any other position, such as `WebFetch(domain:example.*)`, still matches fetches but has no effect on sandboxed commands.



**注释：**

  The built-in proxy enforces the allowlist based on the requested hostname and, by default, does not terminate or inspect TLS traffic. The experimental [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) setting makes the built-in proxy terminate TLS itself, which [`mask` credential entries](<#mask-credentials>) require. See [Security limitations](<#security-limitations>) for the implications of the default, and [Custom proxy configuration](<#custom-proxy-configuration>) if your threat model requires TLS inspection.


#### Hosts outside your allowed domains

When a sandboxed command connects to a host that isn't in your allowed domains, the command stays in the sandbox and waits for a decision. In an interactive terminal session, the decision depends on your permission mode:

| Permission mode | What happens to the connection |
| :- | :- |
| `bypassPermissions` mode, and plan mode with [bypass permissions available](<https://code.claude.com/docs/en/permission-modes#skip-all-checks-with-bypasspermissions-mode>) | Allowed without a prompt |
| Manual mode, `acceptEdits` mode, and plan mode otherwise | You get a prompt |
| Auto mode | Refused unless the command [listed the host](<#per-command-allowed-domains-in-auto-mode>) and the classifier approved the list |
| `dontAsk` mode | Refused |

With [`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>) or [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) on, the built-in sandbox proxy refuses the connection in every permission mode. In `bypassPermissions` mode, hosts outside your allowed domains are allowed unless one of them is on. [The unsandboxed retry escape hatch](<#the-unsandboxed-retry-escape-hatch>) covers when a command can leave the sandbox in that mode. A connection to a host in [`deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) is refused in every permission mode too.

#### Hostnames that resolve to local addresses

After a hostname passes the allowlist, the sandbox proxy resolves it and refuses the connection when the name resolves only to local addresses. Local addresses include loopback addresses such as `127.0.0.1`, link-local addresses such as the `169.254.169.254` cloud metadata endpoint, and addresses assigned to your own machine. The names `localhost` and `*.localhost` are allowed to resolve to loopback.

An allowed intranet hostname that resolves to a private range such as `10.0.0.0/8` connects. To let a name resolve to a refused address, add that IP address to `allowedDomains`, such as `"127.0.0.1:8080"`.

The check applies to hostnames. Your allowed domains and permission mode decide a connection to an IP address. The proxy also skips the check for connections it sends through an upstream corporate proxy, because that proxy resolves the name.

#### Per-command allowed domains in auto mode

In [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) with sandboxing on, Claude names the hosts a command needs on the command itself instead of triggering a network approval for each connection. Each Bash, PowerShell, or [Monitor](<https://code.claude.com/docs/en/tools-reference#monitor-tool>) command that runs in the sandbox can carry a list of hosts beyond the sandbox's allowlist: a domain such as `registry.npmjs.org`, a wildcard such as `*.pythonhosted.org`, or an IP address, each with an optional `:port`. The classifier reviews the hosts together with the command. Requires Claude Code v2.1.271 or later.

An approved list opens those hosts for that one command alone, for as long as it runs. Nothing is added to your session's allowed hosts or your settings; the next command names its own hosts.

A command that carries hosts goes to the classifier instead of being approved by a permission rule or the sandbox's [auto-allow mode](<#sandbox-modes>). If an [ask rule](<https://code.claude.com/docs/en/permissions#manage-permissions>) forces a prompt for the command, the permission dialog in your terminal lists the hosts beside it, and approving there covers both.

A per-command list widens only what the sandbox denies by default. [`deniedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-denieddomains>) entries still block. When [`strictAllowlist`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-strictallowlist>) or [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) locks the allowlist, Claude Code refuses per-command lists.

While per-command lists apply, Claude Code refuses a connection to a host that no approved command listed, without a prompt or a classifier check. The refusal names the host in the command's result, and Claude re-runs the command with the host added.

#### IPv6 addresses in domain lists

To match an IPv6 address in `allowedDomains`, `deniedDomains`, or a `WebFetch(domain:...)` rule, write the address in brackets: `"[::1]"` matches that address on every port, and `"[::1]:443"` matches it on port 443 only. The bracketed form requires Claude Code v2.1.229 or later.

An unbracketed entry such as `::1:443` is ambiguous between an address and an address with a port:

* **Deny lists**: Claude Code denies every reading the entry parses as, so whichever reading you meant is blocked. For an entry with no parseable reading, Claude Code blocks nothing
* **Allow lists**: Claude Code never allows more than you wrote. It rewrites an ambiguous entry to its host-and-port reading when that reading parses cleanly, and may drop the entry entirely rather than widen the allowlist

To find ambiguous entries, run `claude doctor` in your terminal and look for the `Sandbox network domain entries have unreliable spellings` warning. Rewrite each ambiguous entry in the bracketed form.

### OS-level enforcement

The sandboxed Bash tool uses operating system security primitives:

* **macOS**: uses Seatbelt for sandbox enforcement
* **Linux**: uses [bubblewrap](<https://github.com/containers/bubblewrap>) for isolation
* **WSL2**: uses bubblewrap, same as Linux

You can also run the [`@anthropic-ai/sandbox-runtime`](<https://github.com/anthropics/sandbox-runtime>) package on its own to wrap the Claude Code process. See [Sandbox runtime](<https://code.claude.com/docs/en/sandbox-environments#sandbox-runtime>).

## How sandboxing relates to permissions and permission modes

Sandboxing, [permission rules](<https://code.claude.com/docs/en/permissions>), and [permission modes](<https://code.claude.com/docs/en/permission-modes>) are complementary layers. The sections below cover how the sandbox interacts with each.

### Permission rules

Permission rules and sandboxing control different things:

* **Permission rules** control which tools Claude Code can use and are evaluated before any tool runs. They apply to every tool: Bash, Read, Edit, WebFetch, MCP, and others, except that a deny or ask rule can't block [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) while any other tool remains.
* **Sandboxing** provides OS-level enforcement that restricts what shell commands can access at the filesystem and network level. It applies to Bash, PowerShell, and [Monitor](<https://code.claude.com/docs/en/tools-reference#monitor-tool>) tool commands and their child processes.

The two layers also differ in how they are enforced. Claude Code evaluates permission decisions before a command runs, based on the command string and, in auto mode, a separate classifier's judgment about whether the command is safe. The operating system enforces the sandbox boundary on the running process, so it holds regardless of what the model chose to run and even if an allowed command does more than its name suggests.

Filesystem and network restrictions are configured through both sandbox settings and permission rules:

| Setting or rule | What it does |
| :- | :- |
| `sandbox.filesystem.allowWrite` | Grants subprocess write access to paths outside the working directory |
| `sandbox.filesystem.denyWrite` and `sandbox.filesystem.denyRead` | Block subprocess access to specific paths |
| `sandbox.filesystem.allowRead` | Re-allows reading specific paths within a `denyRead` region |
| [`sandbox.filesystem.disabled`](<#disable-filesystem-isolation>) | Turns the filesystem layer off entirely while keeping network isolation |
| `Edit` allow rules | Grant write access to specific paths, the same way `sandbox.filesystem.allowWrite` does |
| `Read` and `Edit` deny rules | Block access to specific files or directories |
| `WebFetch(domain:...)` allow and deny rules | Control domain access |
| Sandbox `allowedDomains` | Controls which domains Bash commands can reach |
| Sandbox `deniedDomains` | Blocks specific domains even when a broader `allowedDomains` wildcard would otherwise permit them |

Paths and domains from both sandbox settings and permission rules are merged into the final sandbox configuration.

The [claude-code repository's examples directory](<https://github.com/anthropics/claude-code/tree/main/examples/settings>) includes starter settings configurations for common deployment scenarios, including sandbox-specific examples. Use these as starting points and adjust them to fit your needs.

### Permission modes

`/sandbox` is not a [permission mode](<https://code.claude.com/docs/en/permission-modes>). Permission modes decide whether a tool call runs and whether you are prompted first, while the sandbox restricts what a Bash command can access once it runs. They differ in what they control and what replaces the per-action prompt:

| | What it controls | What replaces the prompt |
| :- | :- | :- |
| `/sandbox` | What a Bash command can access once it runs | The sandbox boundary itself, in [auto-allow mode](<#sandbox-modes>) |
| [Auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) | Whether each tool call runs | A classifier that reviews actions |
| `--dangerously-skip-permissions` | Whether each tool call runs | Nothing. [Protected path](<https://code.claude.com/docs/en/permission-modes#protected-paths>) checks are also skipped; the [actions no mode auto-approves](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>) still apply |

The sandbox's [auto-allow mode](<#sandbox-modes>) is separate from [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>): auto-allow approves Bash commands because the sandbox boundary contains them, while auto mode uses a classifier to review actions. The two work independently and can be combined, with the exceptions listed under [Sandbox modes](<#sandbox-modes>). To choose an isolation boundary for unattended runs, see [Sandbox environments](<https://code.claude.com/docs/en/sandbox-environments#how-isolation-relates-to-permission-modes>). For a table of common permission mode and sandbox pairings with the flags that start each one, see [Common setups](<https://code.claude.com/docs/en/permission-modes#common-setups>).

## Configure the sandbox for your organization

Administrators can require sandboxing for every user, keep developers from widening the policy, and route sandbox traffic through a corporate proxy.

### Enforce sandboxing with managed settings

To require the sandbox for every developer, deliver the `sandbox` keys through [managed settings](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>), either as a file managed by your MDM or through [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>) on claude.ai.

The following managed settings configuration enables the sandbox, refuses to start Claude Code when the platform is unsupported or a dependency is missing, and prevents the model from retrying commands outside the sandbox:

```json theme={null}
{
  "sandbox": {
    "enabled": true,
    "failIfUnavailable": true,
    "allowUnsandboxedCommands": false
  }
}
```

The two keys beyond `enabled` control what happens when the sandbox cannot run a command:

* **`failIfUnavailable`**: a missing dependency such as bubblewrap on Linux blocks Claude Code from starting rather than falling back to unsandboxed execution
* **`allowUnsandboxedCommands: false`**: Claude Code ignores the `dangerouslyDisableSandbox` escape hatch, so when a command fails under the sandbox, Claude can't retry it unsandboxed

Consider these additions alongside them:

* Add `excludedCommands` for any organization-approved tools that must run without isolation, because this configuration [stops a repository's settings from taking commands out of the sandbox](<#repository-settings-under-an-admin-required-sandbox>)
* Add [`sandbox.credentials`](<#protect-credentials>) entries for credential directories such as `~/.aws` and `~/.ssh` and for secret environment variables, since the default read policy still allows them

This configuration sandboxes the commands Claude runs. A developer can still type a command at the [`!` shell-mode prompt](<https://code.claude.com/docs/en/interactive-mode#shell-mode-with-prefix>) and run it outside the sandbox, with the same access they already have in any terminal outside Claude Code. See [strict sandbox mode](<#turn-off-the-retry-with-strict-sandbox-mode>) for the sessions where typed commands run sandboxed.

The sandbox doesn't run on native Windows, so with `failIfUnavailable` set, Claude Code exits at startup on those machines. If your fleet includes Windows hosts, you can:

* **Deliver the configuration by operating system**: deploy it through your MDM or as a [managed settings file](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>) on macOS and Linux machines only. [Server-managed settings](<https://code.claude.com/docs/en/server-managed-settings#current-limitations>) apply to all users in the organization
* **Move Windows users into a supported environment**: have them run Claude Code inside WSL2 or a container

### Keep developers from widening the policy

When managed settings set a Boolean key such as `enabled` or `failIfUnavailable`, Claude Code uses the managed value and ignores anything a developer sets locally. For array keys such as `allowRead`, Claude Code merges entries from the scopes the session loads, so a developer can append entries that widen the policy unless a lock covers that key.

Unless managed settings set them, a developer's user settings or `--settings` can turn on the following keys. A repository's `.claude/settings.json` can too, unless the sandbox is [admin-required](<#repository-settings-under-an-admin-required-sandbox>). Each one weakens the sandbox, so set it to `false` in managed settings if you don't want it used:

* [`enableWeakerNestedSandbox`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernestedsandbox>)
* [`enableWeakerNetworkIsolation`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernetworkisolation>)
* [`network.allowAllUnixSockets`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowallunixsockets>)
* [`network.allowLocalBinding`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowlocalbinding>)
* [`allowAppleEvents`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowappleevents>), which a repository can't turn on

Set `allowManagedReadPathsOnly` to `true` in managed settings so that only `allowRead` entries from managed settings are honored. This prevents developers from widening read access beyond the organization-approved paths.

To lock network domains to the managed values the same way, set [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>). With the lock on, only managed settings can set a [proxy port](<#custom-proxy-configuration>).

When managed settings configure `sandbox.filesystem` or list any `sandbox.credentials.files` entry with `"mode": "deny"`, only managed settings can set [`filesystem.disabled`](<#disable-filesystem-isolation>), so developers can't switch off administrator-deployed filesystem restrictions. A [valid](<https://code.claude.com/docs/en/settings-reference#invalid-credential-entries-in-managed-settings>) `mask` entry doesn't lock the key. See [Which settings can disable it](<#which-settings-can-disable-it>).

#### Repository settings under an admin-required sandbox

The sandbox is admin-required while one of these settings is in effect:

* [`allowUnsandboxedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowunsandboxedcommands>) set to `false` in managed settings, or with the `--settings` flag unless managed settings set it to `true`
* [`allowManagedDomainsOnly`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowmanageddomainsonly>) set to `true` in managed settings

These settings don't turn the sandbox on, so set `enabled` as well.

While the sandbox is admin-required, Claude Code takes the settings that loosen it only from managed settings, the `--settings` flag, and each developer's `~/.claude/settings.json`. It ignores these settings in a repository's `.claude/settings.json` and `.claude/settings.local.json`:

| Repository setting | What Claude Code ignores |
| :- | :- |
| `excludedCommands`, `ignoreViolations`, `network.allowedDomains`, `network.allowUnixSockets`, `network.allowMachLookup`, `network.httpProxyPort`, `network.socksProxyPort` | Every entry |
| `filesystem.allowWrite`, `Edit(...)` allow rules, `permissions.additionalDirectories` | The write access each entry gives sandboxed commands. Claude's file tools still follow the `Edit(...)` rules and additional directories |
| `WebFetch(domain:...)` allow rules | The host each rule adds to the sandbox allowlist. The WebFetch tool still follows the rule |
| `enableWeakerNestedSandbox`, `enableWeakerNetworkIsolation`, `network.allowAllUnixSockets`, `network.allowLocalBinding` | `true`. A `false` still applies |
| `enabled`, `failIfUnavailable` | `false`, when the developer's `~/.claude/settings.json` sets `true` |
| `filesystem.allowRead` | An entry at or under a path that managed settings, `--settings`, or user settings deny reading, or a glob that could match one |

These settings still apply while the sandbox is admin-required:

* **In a repository's files**: deny entries and the `autoAllowBashIfSandboxed` value. Set the key in managed settings to keep a repository from changing it
* **In a developer's own settings**: the settings in the table still apply from `~/.claude/settings.json` or `--settings`, unless a managed-only lock such as `allowManagedDomainsOnly` covers them. Most of them, such as `excludedCommands` and `filesystem.allowWrite`, have no managed-only lock

The configuration under [Enforce sandboxing with managed settings](<#enforce-sandboxing-with-managed-settings>) makes the sandbox admin-required. Add the `excludedCommands`, `allowWrite`, and socket entries that your approved tools need to managed settings, because a repository can't supply them.

Requires Claude Code v2.1.285 or later. From v2.1.282 to v2.1.284, the same settings made Claude Code ignore a repository's `excludedCommands` entries.

#### Locks that apply without an admin-required sandbox

Some settings make Claude Code ignore the repository keys that directly override one restriction, even when the sandbox isn't admin-required. Each one has this effect only when you set it in a file its row names, and the repository's other sandbox settings still apply. Requires Claude Code v2.1.285 or later.

| Setting | Where you set it | What Claude Code ignores in a repository's settings |
| :- | :- | :- |
| `network.deniedDomains` or a `WebFetch(domain:...)` deny rule | Managed settings, `--settings` | `httpProxyPort` and `socksProxyPort` |
| `network.strictAllowlist` | Managed settings, `--settings`, user settings | The proxy ports, `allowedDomains`, and `WebFetch(domain:...)` allow rules |
| `filesystem.denyRead`, a `Read(...)` deny rule, or a `credentials.files` entry | Managed settings, `--settings` | An `allowRead`, `allowWrite`, `Edit(...)` allow, or `additionalDirectories` entry at or under a path that managed settings, `--settings`, or user settings deny reading, or a glob that could match one |

These locks change what sandboxed commands can reach. The WebFetch tool and Claude's file tools still follow a repository's rules and additional directories.

### Custom proxy configuration

To inspect, filter, or log sandbox traffic with your own tooling, replace the built-in sandbox proxy with a proxy you run on the same machine.

To route sandbox traffic through a corporate proxy elsewhere on your network, set `HTTPS_PROXY` instead, as the **Corporate proxy** entry under [Network isolation](<#network-isolation>) describes. That way, Claude Code's allowlist still applies.

To direct sandboxed commands to your proxy, set the localhost ports it listens on in [sandbox settings](<https://code.claude.com/docs/en/settings-reference#sandbox-settings>):

```json theme={null}
{
  "sandbox": {
    "network": {
      "httpProxyPort": 8080,
      "socksProxyPort": 8081
    }
  }
}
```

If you set a port and also set `HTTPS_PROXY` or `HTTP_PROXY`, Claude Code doesn't forward what sandboxed commands send to your proxy on to the proxy those variables name. To reach a corporate proxy, configure your own proxy to forward to it.

Which files can set a port depends on your other sandbox settings. The first case that matches applies:

* **`allowManagedDomainsOnly` is on**: managed settings only
* **The sandbox is [admin-required](<#repository-settings-under-an-admin-required-sandbox>), or a [narrower network lock](<#locks-that-apply-without-an-admin-required-sandbox>) applies**: managed settings, `--settings`, and user settings
* **Otherwise**: any settings file

Claude Code ignores a port set anywhere else. Before v2.1.285, any settings file could set a port.



**注意：**

  Once either port applies, your proxy is responsible for filtering everything sent to it. Claude Code's own network controls, such as `allowedDomains`, `deniedDomains`, `strictAllowlist`, approval prompts, and the [local-address check](<#hostnames-that-resolve-to-local-addresses>), stop applying to that traffic. A sandboxed command can connect to either proxy, so if you set only one port, Claude Code's domain lists on the other proxy don't limit what the command reaches through yours.


## Troubleshooting

Some commands fail inside the sandbox even though they work outside it. Find the heading that matches your symptom or error message.

If your organization's sandbox is [admin-required](<#repository-settings-under-an-admin-required-sandbox>), Claude Code ignores the settings these fixes name in a project's settings files, so save them in `~/.claude/settings.json`, where they apply in every project. If a fix still has no effect, your organization's managed settings may set that key.

A fix that adds an `excludedCommands` pattern removes the sandbox from the commands the pattern matches. See [what an excluded command can do](<#run-commands-outside-the-sandbox-with-excludedcommands>).

### Commands fail with a host-not-allowed error

Many CLI tools need to reach specific hosts. Approve the host when prompted, or add it to [`allowedDomains`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-alloweddomains>). If your organization locks the allowlist with `allowManagedDomainsOnly`, there's no prompt, so ask your administrator to add the host.

### `jest` hangs or fails

`watchman` is incompatible with the sandbox. Run `jest --no-watchman` instead.

### Go-based CLIs fail TLS verification on macOS

Tools such as `gh`, `gcloud`, and `terraform` may fail TLS verification under [Seatbelt](<#os-level-enforcement>). To run these tools outside the sandbox, add a pattern for each tool, such as `gh *`, to [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>). The tool then runs with your full access and its stored credentials. If you are using `httpProxyPort` with a MITM proxy and custom CA, set [`enableWeakerNetworkIsolation`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernetworkisolation>) to `true` instead.

### `open`, `osascript`, or browser-based auth flows fail with error `-600` on macOS

The sandbox blocks Apple Events by default. Set [`allowAppleEvents`](<https://code.claude.com/docs/en/settings-reference#sandbox-allowappleevents>) to `true` in your user, managed, or CLI settings to allow them. Claude Code ignores this key in project settings.

Enabling `allowAppleEvents` removes code-execution isolation, since sandboxed commands can then launch other applications unsandboxed with no user prompt, and can send AppleScript commands to running applications, subject to the macOS automation-consent prompt (TCC). Alternatively, add a pattern such as `open *` to [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>). Each `open` call then goes through the permission flow, and `open` can launch any file or app, including one Claude wrote.

### `docker` commands fail

`docker` is incompatible with the sandbox. Take the `docker` commands you need out of the sandbox with an `excludedCommands` pattern such as `docker compose *`. [Run commands outside the sandbox with `excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>) explains what an excluded `docker` command can reach. A narrower pattern takes fewer commands out of the sandbox.

### `pbcopy`, `xclip`, or `wl-copy` doesn't update the clipboard

The `pbcopy`, `xclip`, and `wl-copy` clipboard utilities can fail to reach the system clipboard from inside the sandbox, in which case the text piped to them doesn't arrive.

To put Claude's output on your clipboard, ask Claude to print it in its response, then run [`/copy`](<https://code.claude.com/docs/en/commands>). `/copy` writes to the clipboard from the Claude Code process rather than from a sandboxed command.

When Claude pipes text to one of these tools, adding the tool to [`excludedCommands`](<https://code.claude.com/docs/en/settings-reference#sandbox-excludedcommands>) doesn't take that call out of the sandbox on its own.

### A git command fails with `unable to unlink old`

`git merge`, `git checkout`, and similar commands fail with `unable to unlink old` when they need to replace a file the sandbox denies writes to. On Linux and WSL2 the error ends with `Read-only file system`. The file can be in one of these places:

* Under a [protected path](<#protected-paths>) such as `.claude/skills`
* Under one of your `denyWrite` entries
* Outside the directories the sandbox lets commands write to at all

After the failure, Claude may [offer to rerun the command outside the sandbox](<#the-unsandboxed-retry-escape-hatch>). Approve that retry, or run the git command yourself in another terminal. If you've set `allowUnsandboxedCommands` to `false`, Claude can't offer the retry, so run the command yourself.

### Bubblewrap fails to start inside a container

In an unprivileged container, [bubblewrap](<#os-level-enforcement>) can't mount a fresh `/proc` filesystem, so sandboxed commands fail with a `bwrap` error such as `Can't mount proc on /newroot/proc: Operation not permitted`. Set [`enableWeakerNestedSandbox`](<https://code.claude.com/docs/en/settings-reference#sandbox-enableweakernestedsandbox>) to `true` so the sandbox bind-mounts the container's existing `/proc` instead. Only use this setting when the outer container already provides the isolation boundary you need, since the setting exposes process information to sandboxed commands that a fresh `/proc` mount would hide.

### 0-byte read-only files appear at `.claude` settings paths, and "Yes, and don't ask again" doesn't save

On Linux and WSL2, the sandbox holds a write denial on a file that doesn't exist yet by creating a 0-byte read-only placeholder there while a sandboxed command runs. The sandbox removes the placeholder afterward. If a session is killed before that cleanup runs, for example by SIGKILL, the placeholders stay behind. Later sessions bind the placeholders read-only again on every start, so a settings write such as saving a permission choice fails at a path where a placeholder remains.

Run `claude doctor` in your terminal to list the leftover placeholder files. The [`Stale sandbox mask files left by a killed session`](<https://code.claude.com/docs/en/errors#stale-sandbox-mask-files-left-by-a-killed-session>) warning names some of them and counts the rest. Delete each file with `rm` while no other Claude Code session is running in that project. Before v2.1.257, Claude Code left the same placeholders behind without flagging them.

### `git` over SSH fails with the sandbox on

On macOS, `git fetch`, `git pull`, and `git push` against an SSH remote fail inside the sandbox even when the host is allowed. On Linux and WSL2, they work once the host is allowed. Claude Code tunnels git's SSH connection through the [sandbox proxy](<#network-isolation>), and the macOS tunnel can't authenticate to that proxy.

On Linux and WSL2, check these if the connection still fails:

* **The host is allowed on port 22**: an `allowedDomains` entry with no port, such as `"git.example.com"`, covers it
* **Your corporate proxy permits port 22**: if your network requires an upstream proxy, the tunnel goes through it too
* **The key is readable as a file**: the sandbox can block the `ssh-agent` socket, and a `denyRead` or `credentials` entry for `~/.ssh` hides your key files

On macOS, switch the remote to HTTPS, which needs HTTPS credentials such as a personal access token:

```bash theme={null}
git remote set-url origin https://git.example.com/example-org/example-repo.git
```

If you have to keep the SSH remote, take git's network commands out of the sandbox with [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>):

```json theme={null}
{
  "sandbox": {
    "excludedCommands": ["git fetch *", "git pull *", "git push *"]
  }
}
```

These entries match `git push origin main`. A call that adds a `cd`, uses `git -C`, or contains a command substitution stays sandboxed. The excluded git commands can reach any host, not only the ones in `allowedDomains`.

Plain `ssh`, `scp`, and `rsync` over SSH fail for the reason [the database client entry](<#a-database-client-or-other-non-http-tool-fails-to-reach-an-allowed-host>) gives.

### A database client or other non-HTTP tool fails to reach an allowed host

A tool that ignores the proxy environment variables can't connect from inside the sandbox, even to a host in `allowedDomains`. A sandboxed command has [no direct route to the network](<#network-isolation>), so a tool that opens its own connection fails. Most database drivers, plain `ssh`, and tools that use UDP behave this way.

The failure looks like a network or name resolution error:

* **macOS**: `Operation not permitted`, or a name resolution error such as `Could not resolve host`
* **Linux and WSL2**: `Network is unreachable`, or a name resolution error such as `Temporary failure in name resolution`

A tool that uses the proxy fails differently when its host isn't allowed. You get a network prompt, or the tool receives a `403` response from the proxy.

To let the tool connect, run the command that needs it outside the sandbox with [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>). This example excludes one script and adds an [ask rule](<https://code.claude.com/docs/en/permissions>) so that you approve each run:

```json theme={null}
{
  "sandbox": {
    "excludedCommands": ["python scripts/load_orders.py *"]
  },
  "permissions": {
    "ask": ["Bash(python scripts/load_orders.py *)"]
  }
}
```

The script runs with your full access, and Claude can edit a script that's inside your working directory, so review it when the prompt appears.

### A command fails to reach a server on localhost

By default, a sandboxed command can't connect directly to a server that's running on your machine outside the sandbox, such as a dev server or a database in a container. What you can change depends on your platform:

* **macOS**: set [`network.allowLocalBinding`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-allowlocalbinding>) to `true`. Sandboxed commands can then listen on network ports and connect to any port on localhost, which includes every other service listening there. A localhost service that doesn't require authentication, such as a debugger, can then act for the command outside the sandbox, and a command that listens on a non-loopback address accepts connections from other machines
* **Linux and WSL2**: a sandboxed command's `localhost` is private to that command. The command can listen on a port and reach servers it started itself. A direct connection to `localhost` or `127.0.0.1` doesn't reach servers on the host, and `allowLocalBinding` has no effect. Run the command that needs the host's server outside the sandbox with [`excludedCommands`](<#run-commands-outside-the-sandbox-with-excludedcommands>), where it has no filesystem or network limits. For connections that go through the sandbox proxy, see [Hostnames that resolve to local addresses](<#hostnames-that-resolve-to-local-addresses>)

This example turns the setting on for macOS:

```json theme={null}
{
  "sandbox": {
    "network": {
      "allowLocalBinding": true
    }
  }
}
```

An `allowedDomains` entry for `localhost` applies to connections that go through the proxy, so it doesn't change a direct connection. Claude Code sets `NO_PROXY` for sandboxed commands so that they connect to `localhost` directly instead of through the proxy. The entry also exposes every port on your machine's localhost to a command that does use the proxy. For a development hostname that points at `127.0.0.1`, see [An allowed hostname is refused with `resolved to a loopback address`](<#an-allowed-hostname-is-refused-with-resolved-to-a-loopback-address>).

### An allowed hostname is refused with `resolved to a loopback address`

The sandbox proxy refuses an allowed hostname that [resolves to a local address](<#hostnames-that-resolve-to-local-addresses>), which affects development names such as `myapp.test` that point at `127.0.0.1`. The command sees a `403` response whose body names the kind of address, such as `Connection to myapp.test blocked: resolved to a loopback address`.

Add the IP address the name resolves to alongside the hostname in `allowedDomains`, each with the port your server listens on:

```json theme={null}
{
  "sandbox": {
    "network": {
      "allowedDomains": ["myapp.test:3000", "127.0.0.1:3000"]
    }
  }
}
```

An IP address entry with no port lets sandboxed commands reach every service listening on that address.

Before v2.1.284, the proxy connected to whatever address an allowed hostname resolved to.

### `/sandbox` fails with `Sandbox settings are overridden by a higher-priority configuration`

`/sandbox` prints `Error: Sandbox settings are overridden by a higher-priority configuration and cannot be changed locally.` instead of opening its panel when a higher [settings level](<https://code.claude.com/docs/en/settings#settings-precedence>) sets `sandbox.enabled`, `sandbox.autoAllowBashIfSandboxed`, or `sandbox.allowUnsandboxedCommands`. The panel saves your choices to `.claude/settings.local.json`, and a value saved there can't override those levels.

Managed settings and `--settings` rank above local settings. To see which of them this session loaded, run `/status` and read the `Setting sources` line:

* **`Command line arguments`**: if you started Claude Code with [`--settings`](<https://code.claude.com/docs/en/settings#change-a-setting-for-one-session>), check whether the file or JSON you passed sets one of those keys. If it does, change the value there, or start Claude Code again without those keys.
* **`Enterprise managed settings`**: your organization's managed settings are loaded. If they set one of those keys, you can't change that key from `/sandbox` or from any settings file you control, so ask your administrator.

## Limitations

Sandboxing reduces risk but is not a complete isolation boundary. Review the limitations below before relying on it as a hard security control.

### Security limitations

* **Network filtering**: the sandbox restricts which domains processes can connect to. By default the built-in proxy does not terminate or inspect TLS on outbound traffic, so the contents of encrypted connections are not examined. The experimental [`network.tlsTerminate`](<https://code.claude.com/docs/en/settings-reference#sandbox-network-tlsterminate>) setting terminates TLS at the proxy for [`mask` credential substitution](<#mask-credentials>) but does not add content filtering. You are responsible for ensuring that only trusted domains are allowed in your policy.



**注意：**

  Allowing broad domains such as `github.com` can create paths for data exfiltration. Because the proxy makes its allow decision from the client-supplied hostname without inspecting TLS, code running inside the sandbox can potentially use [domain fronting](<https://en.wikipedia.org/wiki/Domain_fronting>) or similar techniques to reach hosts outside the allowlist. If your threat model requires stronger guarantees, configure a [custom proxy](<#custom-proxy-configuration>) that terminates TLS and inspects traffic, and install its CA certificate inside the sandbox. Stronger TLS-aware network isolation is an active area of development.


* **Privilege escalation via Unix sockets**: the `allowUnixSockets` configuration can inadvertently grant access to system services that could lead to sandbox bypasses. For example, allowing access to `/var/run/docker.sock` effectively grants access to the host system through the Docker socket. Consider carefully any Unix sockets that you allow through the sandbox.
* **Filesystem permission escalation**: overly broad filesystem write permissions can enable privilege escalation attacks. Allowing writes to directories containing executables in `$PATH`, system configuration directories, or user shell configuration files such as `.bashrc` or `.zshrc` can lead to code execution in different security contexts when other users or system processes access these files.
* **Linux sandbox strength**: the Linux implementation provides strong filesystem and network isolation but includes an `enableWeakerNestedSandbox` mode that enables it to work inside Docker environments without privileged namespaces. This option considerably weakens security and should only be used when additional isolation is otherwise enforced.
* **Apple Events on macOS**: the macOS sandbox blocks Apple Events by default. The `allowAppleEvents` setting lifts this restriction so tools such as `open` and `osascript` work, but it removes code-execution isolation: sandboxed commands can launch other applications unsandboxed with no user prompt, and can send AppleScript commands to running applications, subject to the per-app macOS automation-consent prompt (TCC). It is only honored from user, managed, or CLI settings. Project settings cannot enable it.

### Scope

The sandbox isolates shell commands and their child processes. [What runs outside the sandbox](<#what-runs-outside-the-sandbox>) lists the tools and helper processes it doesn't cover. Computer use and subagents relate to the sandbox as follows:

* **Computer use**: when Claude opens apps and controls your screen, it runs on your actual desktop rather than in an isolated environment. Per-app permission prompts gate each application. See [computer use in the CLI](<https://code.claude.com/docs/en/computer-use>) or [computer use in Desktop](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>).
* **Subagents**: [subagents](<https://code.claude.com/docs/en/sub-agents>) run in the same process as the parent session and use the same sandbox configuration. Bash commands inside a subagent are sandboxed when sandboxing is enabled in the parent session.
* **Background sessions**: a [background session](<https://code.claude.com/docs/en/agent-view>) runs in its own process, and its Bash commands are sandboxed when [its settings](<https://code.claude.com/docs/en/agent-view#settings-and-provider>) enable sandboxing.
* **A boundary around the whole process**: to put the processes in [What runs outside the sandbox](<#what-runs-outside-the-sandbox>) behind a boundary too, run Claude Code (local mode) inside the [sandbox runtime](<https://code.claude.com/docs/en/sandbox-environments#sandbox-runtime>) with a network allowlist limited to the hosts you approve, or in the [dev container](<https://code.claude.com/docs/en/devcontainer>) with its firewall script. For the background service and the sessions it hosts, see [Run Claude Code behind a corporate launcher](<https://code.claude.com/docs/en/corporate-launcher>).
* **Mods**: a [mod](<https://code.claude.com/docs/en/plugins/mods/overview>) is a plugin that runs its own code inside Claude Code, and a process that a mod starts runs outside the sandbox. See [What a mod can reach](<https://code.claude.com/docs/en/plugins/mods/overview#what-a-mod-can-reach>).



**注意：**

  Effective sandboxing requires both filesystem and network isolation. Without network isolation, a compromised agent could exfiltrate sensitive files like SSH keys. Without filesystem isolation, whether from a permissive policy or from [disabling the filesystem layer](<#disable-filesystem-isolation>), a compromised agent could backdoor system resources to gain network access. When you widen the defaults, check that an `allowWrite` path, a broad `allowedDomains` entry, or an `excludedCommands` exception does not undo a restriction on the other side.


## See also

* [Sandbox environments](<https://code.claude.com/docs/en/sandbox-environments>): compare the built-in sandbox with dev containers, containers, and VMs
* [Security](<https://code.claude.com/docs/en/security>): security features and best practices
* [Permissions](<https://code.claude.com/docs/en/permissions>): permission configuration and access control
* [All settings](<https://code.claude.com/docs/en/settings-reference>): every settings key
* [CLI reference](<https://code.claude.com/docs/en/cli-reference>): command-line options


</details>
