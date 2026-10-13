# Antigravity 终端沙箱

介绍命令隔离、配置入口、非沙箱命令与权限的关系，并分别说明不同操作系统的支持行为。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/sandbox/>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.antigravity.google/docs/sandbox?tab=cli>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity 终端沙箱.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 终端沙箱 | Google 反重力文档

# 终端沙箱

终端沙箱将 OS- 级别容器 
 边界内的 agent shell 命令隔离，以保护您的工作站和敏感文件。

- 反重力2.0

- 反重力CLI

## 反重力2.0

注意事项

Antigravity 的更新权限系统目前可在 macOS 和 Linux 上使用，其中沙箱默认启用。在 Windows 上，Antigravity 继续使用之前的行为 - 请参阅下面的 Windows 部分。

### macOS 和 Linux

#### 概述

反重力包括一个终端沙箱，用于隔离 agents 执行的 shell 命令。沙盒命令可以写入您的项目文件夹、临时目录和公共构建缓存，并读取系统目录（例如 /usr 和 /etc），以便您的工具继续工作。 ~/.ssh 和 .env 等敏感文件会被阻止，任何未显式安装的内容在沙箱内都是不可见的，并且网络访问仅限于您已批准的域。

沙箱是基于本机操作系统原语构建的，因此无需管理虚拟机或 Docker 映像，也没有启动延迟：

操作系统技术详细信息 Linux 命名空间 内核命名空间隔离文件系统、隐藏主机进程并切断网络。 macOS sandbox-exec Seatbelt 配置文件 (SBPL) 限制文件系统访问和套接字连接。

#### 配置

您的权限预设控制是否使用沙箱。在“设置”>“常规”>“权限设置”下配置您的预设：

预设沙箱命令默认开启沙箱中允许；询问外部 请求审查 关闭 始终询问 Turbo 关闭 允许，不提示，无限制

您可以在“设置”>“项目”下覆盖单个项目中的全局预设。项目在创建时默认为“继承常规”，这遵循您的全局预设。

请参阅 Agent 权限了解完整的预设行为，包括文件访问、MCP 工具和网页读取。

#### 未沙盒的命令

有些命令无法在沙箱内运行，例如那些需要网络访问或与系统服务通信的命令。 agent 可以请求在沙箱外部运行此类命令，它们在您的主机上以完全权限运行。这些请求始终会暂停以供您批准，除非该命令已被命令允许或拒绝规则涵盖。

要让特定命令在没有提示的情况下运行（在沙箱内部或外部），请添加命令允许规则：

```
command(git push)
command(regex:docker compose .*)
```

#### 权限整合

沙箱从您的权限配置中派生其访问边界：

- 文件系统：您的项目文件夹以读写方式安装。在默认系统安装之上，read_file 下允许的路径以只读方式安装，write_file 下允许的路径以读写方式安装。其他一切都无法访问。

- 网络：默认情况下，沙盒命令无需网络访问即可运行。 read_url 下允许的域将添加到沙箱的出站白名单中。

### 窗户

注意事项

Windows 当前使用本节中描述的行为。未来版本会将 Windows 更新为统一权限系统。

#### 概述

反重力包括一个终端沙箱，用于隔离 agents 执行的 shell 命令。沙盒命令可以写入您的项目文件夹、临时目录和公共构建缓存，并读取系统目录，以便您的工具继续工作。 .env 等敏感文件会被阻止，任何未显式安装的内容在沙箱内都是不可见的，并且网络访问仅限于您已批准的域。

#### 配置

您可以全局或每个项目配置沙箱。

##### 全局设置

在“设置”>“常规”中的“Agent 设置”下，配置以下选项：

- 启用沙盒模式（预览）：在沙盒内运行 agent 终端命令。

- 终端命令自动执行：设置为在沙盒中继续，让沙盒命令无需批准即可运行；需要在沙箱外运行的命令仍然首先询问。其他选项是 Require Review 和 Always Proceed 。

##### 项目设置

在“设置”>“项目”下选择一个项目以覆盖该项目的这些设置。每个设置都会获得一个 Inherit General 选项，并且 Enable Sandbox Mode 变为下拉列表： Inherit General 、 Enabled 或 Disabled 。

#### 安全预设

同一设置部分中的安全预设下拉列表捆绑了终端和文件访问策略：

预设终端命令 自动执行 文件夹外文件访问 默认 要求审查 始终询问 全机要求审查 允许 Turbo 模式 始终继续 允许

所有预设均不会打开沙箱。启用“启用沙盒模式”会将预设切换为“自定义”，您可以在其中自行设置每个选项 - 常见的组合是沙盒与“在沙盒中继续”。

#### 未沙盒的命令

当命令需要在沙箱外运行时（因为它与 unsandboxed(...) 规则匹配、agent 请求它，或者沙箱被禁用），该命令会在您的主机上以完全权限运行。根据您的执行策略，agent 首先暂停以供您批准。

#### 权限整合

沙箱从您的权限配置中派生其访问边界：

- 文件系统：您的项目文件夹以读写方式安装。在默认系统安装之上，read_file 下允许的路径以只读方式安装，write_file 下允许的路径以读写方式安装。其他一切都无法访问。

- 网络 ：启用网络访问后，read_url 下允许的域将添加到沙箱的出站白名单中。

- 逃逸舱口 ( unsandboxed ) ：要让特定命令在沙箱外部运行而不完全禁用它，请添加一个 unsandboxed 允许规则： unsandboxed(git push) unsandboxed(docker compose .*)

```
unsandboxed(git push)
unsandboxed(docker compose .*)
```

## 反重力CLI

### 它是如何运作的

启用终端沙箱后，Antigravity CLI 在 OS- 级别隔离边界内执行命令。命令可以写入您的工作区、临时目录和公共构建缓存，并读取系统目录（例如 /usr 和 /etc），以便您的工具继续工作。 ~/.ssh 和 .env 等敏感文件会被阻止，任何未显式安装的内容在沙箱内都是不可见的，并且网络访问仅限于您已批准的域。

沙箱是基于本机操作系统原语构建的，因此无需管理虚拟机或 Docker 映像，也没有启动延迟：

操作系统技术详细信息 Linux 命名空间 内核命名空间隔离文件系统、隐藏主机进程并切断网络。 macOS sandbox-exec Seatbelt 配置文件 (SBPL) 限制文件系统访问和套接字连接。

### CLI配置

在 ~/.gemini/antigravity-cli/settings.json 中启用沙箱，或以交互方式使用 /config ：

```
{
"enableTerminalSandbox"
:
true
,
"toolPermission"
:
"proceed-in-sandbox"
}
```

该配置支持以下设置：

- enableTerminalSandbox （布尔值，默认值： false ）：在沙箱内运行 agent 命令。

- toolPermission（字符串，默认值：“request-review”）：将其设置为“proceed-in-sandbox”可以让沙箱命令自动运行，而需要在沙箱外运行的命令仍然提示审查。其他模式请参见设置。

#### CLI 标志

您还可以在启动CLI时控制沙箱：

```
# Force the sandbox on for this session
antigravity
--sandbox
```

CLI 支持以下沙箱标志：

- --sandbox ：为会话打开沙箱，覆盖 settings.json 。

### CLI权限集成

沙箱从您的权限配置中派生其访问边界：

- 文件系统：write_file 下允许的工作空间文件夹和路径以读写方式安装。 read_file 下允许的路径以只读方式安装在默认系统安装之上。被拒绝的路径被封锁，其他一切都无法访问。

- 网络：默认情况下，沙盒命令无需网络访问即可运行。 read_url 下允许的域将添加到沙箱的出站白名单中。

- 逃逸舱口（未沙盒）：与未沙盒允许规则相匹配的命令在沙盒外运行而不提示。这对于无法在隔离边界内工作的工具非常有用，例如 Docker 或与系统服务通信的命令。

agent 还可以请求在沙箱之外自行运行命令，例如，重试由于沙箱限制而失败的命令。这些请求始终需要您的批准，除非该命令与未沙盒化的允许规则匹配。

#### 示例

以下示例在 settings.json 中配置沙箱允许和拒绝规则：

```
{
"permissions"
: {
"allow"
: [
"command(npm test)"
,
"command(git diff)"
,
"unsandboxed(git push)"
,
"read_file(/var/log/app)"
,
"write_file(src/)"
],
"deny"
: [
"command(rm -rf /)"
,
"command(sudo *)"
,
"write_file(/home/user/.ssh)"
]
}
}
```

使用此配置：

- npm 测试和 git diff 在沙箱内运行。

- git Push 使用 unsandboxed(git Push) 在沙箱外运行。

- rm -rf / 和 sudo 始终被阻止。

### 互动提示

当命令需要审核时，您可以批准一次或将批准变成长期规则：

```
Do you want to proceed?
1. Yes
2. Yes, and always allow in this conversation for commands that start with 'npm test'
3. Yes, and always allow for commands that start with 'npm test' (Persist to settings.json)
4. No
```

当agent要求绕过沙箱时，提示会明确指出，以便您判断风险：

```
🔓 Allow sandbox bypass for command execution?
⚠️ Confirm the command is safe to run outside of the sandbox with full network and disk access.
```

此处批准“始终允许”会记录 unsandboxed(...) 规则，而不是普通的 command(...) 规则。

## 相关资源

探索相关文档和指南：

- Agent 权限：配置允许、拒绝和询问规则。

- Agent 设置：命令执行策略和文件访问控制。

- 设置：全局应用程序和 CLI 首选项。

- 项目：多文件夹配置和每个项目设置。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Terminal sandbox | Google Antigravity Docs

# Terminal sandbox

The terminal sandbox isolates agent shell commands inside OS-level container
boundaries to protect your workstation and sensitive files.

- Antigravity 2.0

- Antigravity CLI

## Antigravity 2.0

Note

Antigravity’s updated permission system is currently available on macOS and Linux , where the sandbox is enabled by default. On Windows , Antigravity continues to use the previous behavior—refer to the Windows section below.

### macOS and Linux

#### Overview

Antigravity includes a terminal sandbox that isolates shell commands executed by agents. Sandboxed commands can write to your project folders, temporary directories, and common build caches, and read system directories such as /usr and /etc so your tools keep working. Sensitive files such as ~/.ssh and .env are blocked, anything not explicitly mounted is invisible inside the sandbox, and network access is limited to domains you’ve approved.

The sandbox is built on native operating system primitives, so there are no virtual machines or Docker images to manage and no startup delay:

Operating System Technology Details Linux Namespaces Kernel namespaces isolate the filesystem, hide host processes, and cut off networking. macOS sandbox-exec Seatbelt profiles (SBPL) restrict filesystem access and socket connections.

#### Configuration

Your permission preset controls whether the sandbox is used. Configure your preset under Settings > General > Permission Settings :

Preset Sandbox Commands Default On Allowed in sandbox; ask outside Request Review Off Always ask Turbo Off Allowed without prompting, unrestricted

You can override the global preset in an individual project under Settings > Projects . Projects default to Inherit General upon creation, which follows your global preset.

Refer to Agent permissions for the full preset behavior, including file access, MCP tools, and web page reads.

#### Unsandboxed commands

Some commands cannot work inside the sandbox—for example, those needing network access or talking to system services. The agent can request to run such commands outside the sandbox, where they run on your host with full privileges. These requests always pause for your approval, unless the command is already covered by a command allow or deny rule.

To let a specific command run without prompting—inside or outside the sandbox—add a command allow rule:

```
command(git push)
command(regex:docker compose .*)
```

#### Permissions integration

The sandbox derives its access boundaries from your Permissions configuration:

- Filesystem : Your project folders are mounted read-write. Paths allowed under read_file are mounted read-only, and paths allowed under write_file are mounted read-write, on top of the default system mounts. Everything else is inaccessible.

- Network : Sandboxed commands run without network access by default. Domains allowed under read_url are added to the sandbox’s outbound allowlist.

### Windows

Note

Windows currently uses the behavior described in this section. A future release updates Windows to the unified permission system.

#### Overview

Antigravity includes a terminal sandbox that isolates shell commands executed by agents. Sandboxed commands can write to your project folders, temporary directories, and common build caches, and read system directories so your tools keep working. Sensitive files such as .env are blocked, anything not explicitly mounted is invisible inside the sandbox, and network access is limited to domains you’ve approved.

#### Configuration

You can configure the sandbox globally or per project.

##### Global settings

In Settings > General , under Agent Settings , configure the following options:

- Enable Sandbox Mode (Preview) : Runs agent terminal commands inside the sandbox.

- Terminal Command Auto Execution : Set to Proceed in Sandbox to let sandboxed commands run without approval; commands that need to run outside the sandbox still ask first. The other options are Require Review and Always Proceed .

##### Project settings

Select a project under Settings > Projects to override these settings for that project. Each setting gains an Inherit General option, and Enable Sandbox Mode becomes a dropdown: Inherit General , Enabled , or Disabled .

#### Security presets

The Security Preset dropdown in the same settings section bundles the terminal and file access policies:

Preset Terminal Command Auto Execution Outside-of-folders file access Default Require Review Always Ask Full machine Require Review Allow Turbo mode Always Proceed Allow

None of the presets turn the sandbox on. Enabling Enable Sandbox Mode switches the preset to Custom , where you set each option yourself—a common combination is the sandbox with Proceed in Sandbox .

#### Unsandboxed commands

When a command needs to run outside the sandbox—because it matches an unsandboxed(...) rule, the agent requested it, or the sandbox is disabled—the command runs on your host with full privileges. Depending on your execution policy, the agent pauses for your approval first.

#### Permissions integration

The sandbox derives its access boundaries from your Permissions configuration:

- Filesystem : Your project folders are mounted read-write. Paths allowed under read_file are mounted read-only, and paths allowed under write_file are mounted read-write, on top of the default system mounts. Everything else is inaccessible.

- Network : When network access is enabled, domains allowed under read_url are added to the sandbox’s outbound allowlist.

- Escape hatches ( unsandboxed ) : To let specific commands run outside the sandbox without disabling it entirely, add an unsandboxed allow rule: unsandboxed(git push) unsandboxed(docker compose .*)

```
unsandboxed(git push)
unsandboxed(docker compose .*)
```

## Antigravity CLI

### How it works

With the terminal sandbox enabled, Antigravity CLI executes commands inside an OS-level isolation boundary. Commands can write to your workspace, temporary directories, and common build caches, and read system directories such as /usr and /etc so your tools keep working. Sensitive files such as ~/.ssh and .env are blocked, anything not explicitly mounted is invisible inside the sandbox, and network access is limited to domains you’ve approved.

The sandbox is built on native operating system primitives, so there are no virtual machines or Docker images to manage and no startup delay:

Operating System Technology Details Linux Namespaces Kernel namespaces isolate the filesystem, hide host processes, and cut off networking. macOS sandbox-exec Seatbelt profiles (SBPL) restrict filesystem access and socket connections.

### CLI configuration

Enable the sandbox in ~/.gemini/antigravity-cli/settings.json , or interactively using /config :

```
{
"enableTerminalSandbox"
:
true
,
"toolPermission"
:
"proceed-in-sandbox"
}
```

The configuration supports the following settings:

- enableTerminalSandbox (boolean, default: false ): Runs agent commands inside the sandbox.

- toolPermission (string, default: "request-review" ): Setting this to "proceed-in-sandbox" lets sandboxed commands run automatically, while commands that need to run outside the sandbox still prompt for review. Refer to Settings for the other modes.

#### CLI flags

You can also control sandboxing when launching the CLI:

```
# Force the sandbox on for this session
antigravity
--sandbox
```

The CLI supports the following sandbox flag:

- --sandbox : Turns the sandbox on for the session, overriding settings.json .

### CLI permissions integration

The sandbox derives its access boundaries from your Permissions configuration:

- Filesystem : Workspace folders and paths allowed under write_file are mounted read-write. Paths allowed under read_file are mounted read-only, on top of the default system mounts. Denied paths are blocked, and everything else is inaccessible.

- Network : Sandboxed commands run without network access by default. Domains allowed under read_url are added to the sandbox’s outbound allowlist.

- Escape hatches ( unsandboxed ) : Commands matching an unsandboxed allow rule run outside the sandbox without prompting. This is useful for tools that can’t work inside the isolation boundary, such as Docker or commands that talk to system services.

The agent can also request to run a command outside the sandbox on its own—for example, to retry a command that failed due to sandbox restrictions. These requests always require your approval unless the command matches an unsandboxed allow rule.

#### Example

The following example configures sandbox allow and deny rules in settings.json :

```
{
"permissions"
: {
"allow"
: [
"command(npm test)"
,
"command(git diff)"
,
"unsandboxed(git push)"
,
"read_file(/var/log/app)"
,
"write_file(src/)"
],
"deny"
: [
"command(rm -rf /)"
,
"command(sudo *)"
,
"write_file(/home/user/.ssh)"
]
}
}
```

With this configuration:

- npm test and git diff run inside the sandbox.

- git push runs outside the sandbox using unsandboxed(git push) .

- rm -rf / and sudo are always blocked.

### Interactive prompts

When a command needs review, you can approve it once or turn the approval into a standing rule:

```
Do you want to proceed?
1. Yes
2. Yes, and always allow in this conversation for commands that start with 'npm test'
3. Yes, and always allow for commands that start with 'npm test' (Persist to settings.json)
4. No
```

When the agent asks to bypass the sandbox, the prompt calls it out explicitly so you can judge the risk:

```
🔓 Allow sandbox bypass for command execution?
⚠️ Confirm the command is safe to run outside of the sandbox with full network and disk access.
```

Approving “always allow” here records an unsandboxed(...) rule instead of a plain command(...) rule.

## Related resources

Explore related documentation and guides:

- Agent permissions : Configure allow, deny, and ask rules.

- Agent settings : Command execution policies and file access controls.

- Settings : Global application and CLI preferences.

- Projects : Multi-folder configuration and per-project settings.


</details>
