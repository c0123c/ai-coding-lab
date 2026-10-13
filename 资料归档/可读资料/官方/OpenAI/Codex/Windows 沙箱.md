# Windows 沙箱

介绍 Windows 下的沙箱配置、MXC 与旧方案的兼容和回退、管理员准备及权限行为。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/windows/windows-sandbox>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/windows/windows-sandbox.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Windows 沙箱.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Windows沙箱

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

在 Windows 上使用 Codex 与本机 [ChatGPT 桌面应用程序 ](<https://learn.chatgpt.com/docs/windows/windows-app>)、
[CLI](<https://learn.chatgpt.com/docs/codex/cli>) 或 [IDE 扩展 ](<https://learn.chatgpt.com/docs/codex/ide>)。

Windows 上的 ChatGPT 桌面应用程序支持核心工作流程，例如并行聊天、
工作树、计划任务、Git 功能、内置浏览器、文件预览、
插件和技能。

该应用程序可以使用 Windows 沙箱在 PowerShell 中本机运行，而不是需要 WSL 或虚拟机的 
。这使 Codex 保持在 Windows 原生 
 工作流程中，同时强制执行有限的文件系统和网络权限。

对于企业安装，请参阅[Windows 部署指南](<https://learn.chatgpt.com/docs/enterprise/windows-deployment>)。


  

> 插图：ChatGPT桌面应用程序 Windows 沙箱设置提示位于消息编辑器上方






  <CodexCallout
    href="/codex/windows/windows-app"
    title="Use the ChatGPT desktop app on Windows"
    description="Work across projects, run parallel chats, and review results in one place with the native Windows app."
    iconSrc="/images/codex/codex-banner-icon.webp"
  />



Codex支持三种Windows沙箱实现：

- `mxc`：兼容 Windows 设备上推荐的沙箱。使用本机进程隔离，无需管理员批准的设置、额外的 Windows 帐户或本地防火墙规则。
- `elevated`：MXC 不可用或禁用时的首选旧版后备。需要管理员批准的设置。沙箱中的命令无需管理员权限即可运行。
- `unelevated`：当提升的设置不可用并且组织策略允许时，传统的回退。网络隔离比 `elevated` 弱，并且不支持拒绝读取路径。





## 配置Windows沙箱

Windows 沙箱强制执行命令及其子进程的活动文件系统和网络权限。权限配置文件确定哪些路径可读或可写以及是否允许网络访问。批准策略单独控制 Codex 何时要求运行具有更多访问权限的命令。请参阅[沙箱和批准](<https://learn.chatgpt.com/docs/agent-approvals-security>)。





### 启用MXC

当设备支持时，桌面应用程序会自动优先选择 MXC 作为消费者帐户。对于企业部署或独立 CLI 使用，您可以通过以下配置启用相同的行为。

在`config.toml`中：

```toml
[features]
prefer_mxc = true
```

对于 `prefer_mxc = true`，当设备和策略支持时，Codex 将 MXC 用于 Windows 命令。
否则，它会使用现有的旧选择和设置流程。

对于设备支持或策略不同的企业，请将 `windows.sandbox` 设置为组织允许的旧实施、`elevated` 或 `unelevated`、[ 作为后备 ](<#配置旧后备>)。

管理员可以将此配置作为默认值分发，或通过 `requirements.toml` 强制执行 `features.prefer_mxc = true`。两者都允许遗留回退。请参阅[托管配置](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>)了解默认值和要求的差异。

### 保持 MXC 禁用

要防止在您的组织中使用 MXC，请将其添加到您的托管 
`requirements.toml` 中：

```toml
[windows]
allow_mxc = false
```

这会阻止自动 MXC 选择和显式 `windows.sandbox = "mxc"`。现有的旧沙箱设置和要求仍然适用。

### MXC 兼容性

主机必须已具备所需的 Microsoft 执行容器 (MXC) 功能，Microsoft 正在将其推广到 Windows 11 设备。 Microsoft 在 **Windows 11 24H2（内部版本 26100.9278）** 和 **25H2（内部版本）中引入了 MXC 进程隔离26200.9278)**。使用以下命令验证您的设备支持 MXC。

在 Codex CLI 0.162.0 及更高版本中，您可以测试 MXC 的一个命令，而无需更改已保存的沙箱选择。从您的项目目录中，运行：

```powershell
codex -c windows.sandbox=mxc sandbox --include-managed-config --permission-profile :workspace -- cmd.exe /d /c echo MXC_OK
$LASTEXITCODE
```

预期输出为 `MXC_OK` 和退出代码 `0`。这将检查命令启动与工作区权限配置文件和托管要求。

如果所需的本机功能不可用或策略阻止 MXC，则显式 `windows.sandbox = "mxc"` 选择将失败。

请注意这些兼容性限制：

- 托管网络需要有效的 `allow_local_binding = true`。 MXC 允许与主机环回上的服务建立连接。代理域规则仍然适用于代理流量，但代理的附加专用网络目标检查已被删除。
- 当前台命令退出时，剩余的子进程将停止。测试依赖于独立开发服务器的工作流程。

### 配置旧后备

在`config.toml`中选择后备实现：

```toml
[windows]
sandbox = "elevated" # or "unelevated"
```

`elevated` 是首选的旧后备方案。它使用专用的低权限沙箱用户、文件系统权限边界、防火墙规则以及沙箱中运行的命令所需的本地策略更改。

`unelevated` 是一个遗留后备。它使用源自当前用户的受限 Windows 令牌运行命令，应用基于 ACL- 的文件系统边界，并使用环境级脱机控制而不是专用脱机用户防火墙规则。它提供比 `elevated` 更弱的网络隔离，并且不支持拒绝的读取路径，但当管理员批准的设置被本地或企业策略阻止时仍然有用。

当设备和策略支持时使用MXC。否则，首选 `elevated`，并以 `unelevated` 作为辅助后备。

企业管理员可以限制哪些遗留沙箱实施
Codex 可以通过使用 [`requirements.toml`](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#admin-enforced-requirements-requirementstoml>):

```toml
[windows]
allowed_sandbox_implementations = ["elevated"]
```

此示例允许 `elevated` 并防止回退到 `unelevated`。当MXC可用时，它不限制`mxc`。其他托管权限和网络要求仍然适用。要允许任一遗留实现，请包含这两个值；当未选择模式时，Codex 优先选择 `elevated`。

有关支持的值，请参阅 [`requirements.toml` 参考 ](<https://learn.chatgpt.com/docs/config-file/config-reference#requirementstoml>)。要同时阻止 MXC，请使用单独的 [`windows.allow_mxc` 要求 ](<#保持-mxc-禁用>)。

默认情况下，两种传统沙箱模式还使用私有桌面来实现更强的 UI 隔离。

### IT-主导旧版提升沙箱的配置

对于没有本地管理员权限的员工，IT 可以在员工启动 Codex 之前安装 CLI 并配置提升的沙箱。从提升的 
 部署进程中，运行：

```powershell
codex sandbox setup --elevated --user 'DOMAIN\alice' --codex-home 'C:\Users\alice\.codex'
```

将身份和路径替换为员工的 Windows 身份和 `CODEX_HOME`。该命令读取用户的配置、配置沙箱并保存 `windows.sandbox = "elevated"`。然后，员工从普通终端运行 Codex。

### 沙箱权限

在完全访问模式下运行 Codex 意味着 Codex 不仅限于您的项目 
 目录，并且可能会执行无意的破坏性操作，从而导致 
 数据丢失。为了更安全地实现自动化，请保留沙箱边界并针对特定例外使用
 [规则](<https://learn.chatgpt.com/docs/agent-configuration/rules>)，或将
 [批准策略设置为
从不](<https://learn.chatgpt.com/docs/agent-approvals-security#run-without-approval-prompts>) 
 Codex 尝试在不请求升级权限的情况下解决问题，
 基于您的 [ 批准和安全设置 ](<https://learn.chatgpt.com/docs/agent-approvals-security>)。

### Windows 版本矩阵

|Windows版本|支持级别|注释|
| -------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|视窗 11|推荐|Windows 上 Codex 的最佳基准。如果您正在标准化企业部署，请使用此选项。|
|最近完全更新的 Windows 10|尽最大努力|可以工作，但不如 Windows 11 可靠。对于 Windows 10，Codex 取决于现代控制台支持，包括 ConPTY。实际上，需要 Windows 10 版本 1809 或更高版本。|
|较旧的 Windows 10 版本|不推荐|更有可能错过所需的控制台组件，例如 ConPTY，并且更有可能在企业设置中失败。|

其他环境假设：

- `winget` 应该可用。如果丢失，请更新 Windows 或安装
设置 Codex 之前的 Windows 包管理器。
- 旧版 `elevated` 沙箱取决于管理员批准的设置。
- 某些企业管理的设备会阻止所需的设置步骤，即使
OS版本本身是可以接受的。
- MXC 另外需要中描述的本机功能
[MXC 兼容性](<#mxc-兼容性>)；该矩阵并未在特定设备上建立 MXC
 可用性。

### 检查沙箱读取访问权限

当命令无法读取目录时，请检查活动权限配置文件、
托管要求和 Windows 文件权限。在CLI中，使用`/status`
和`/debug-config`检查活动会话和配置。请您的
管理员检查托管限制，而不是禁用沙箱。





默认情况下使用本机 Windows 沙箱。选择 [WSL](<https://learn.chatgpt.com/docs/windows/wsl>)
当您需要 Linux 原生工具时，您的工作流程已经存在 WSL2, 或
可用的本机 Windows 实现不能满足您的需求。

## 故障排除和 FAQ

如果您要对托管 Windows 计算机进行故障排除，请从本机 
 沙箱模式、Windows 版本以及 Codex 显示的任何策略错误开始。对于 MXC、
检查 [兼容性 ](<#mxc-兼容性>) 和有效的网络策略。
旧版沙箱问题可能来自设置、登录权限或文件系统权限。



### 我的旧版沙箱设置失败


如果 Codex 无法完成 `elevated` 沙箱设置，最常见的原因 
 是：

- Windows UAC 或管理员提示被拒绝，
- 计算机不允许创建本地用户或组，
- 机器不允许更改防火墙规则，
- 该机器阻止沙箱用户所需的登录权限，
- 或其他企业策略阻止部分设置流程。

尝试什么：

1. 再次尝试 `elevated` 沙箱设置并批准管理员提示
如果你的环境允许的话。
2. 如果您公司的笔记本电脑阻止了此操作，请询问您的 IT 团队该机器是否
允许管理员批准的本地 user/group 创建设置、防火墙 
 配置以及所需的沙箱用户登录权限。
3. 如果安装仍然失败并且托管策略允许，请使用 `unelevated`
该问题正在调查中。







### Codex 将我切换到未升高的沙箱


`unelevated` 实现可以在配置中选择，或者在 `elevated` 设置不可用时用作 
 后备。

- Codex仍然可以在沙盒模式下运行。
- 它仍然应用基于 ACL- 的文件系统边界，但它不使用
将沙箱用户边界与 `elevated` 分开，并具有较弱的网络 
 隔离。
- 这是一个有用的后备方案，但不是首选的长期企业
配置。

对于托管企业笔记本电脑，请首先检查 [MXC 兼容性 ](<#mxc-兼容性>)
。如果 MXC 不可用或被禁用，请要求您的 IT 团队配置 
`elevated`。







### 我看到 Windows 错误 1385


如果沙盒命令失败并出现错误 `1385`，则 Windows 拒绝沙盒用户启动该命令所需的登录类型 
。

实际上，这通常意味着 Codex 已成功创建沙盒用户，
 但 Windows 策略仍然阻止这些用户启动沙盒 
 命令。

该怎么做：

1. 询问您的 IT 团队设备策略是否授予所需的登录权限
给Codex创建的沙箱用户。
2. 如果问题仅影响某些部分，请比较组策略或 OU 差异
机器或团队。
3. 如果您需要立即继续工作，请使用 `unelevated` 沙箱，同时
政策问题正在调查。
4. 发送 `CODEX_HOME/.sandbox/sandbox.log` 以及您的 Windows 版本和
故障的简短描述。







### Codex 警告某些文件夹可供所有人写入


Codex 可能会警告某些文件夹可由 `Everyone` 写入。

如果您看到此警告，则这些文件夹的 Windows 权限过于广泛，
沙箱无法完全保护它们。

该怎么做：

1. 查看警告中列出的文件夹 Codex。
2. 如果适合的话，请从这些文件夹中删除 `Everyone` 写入权限
你的环境。
3. 重新启动 Codex 或在获得这些权限后重新运行沙箱设置
已更正。

如果您不确定如何更改这些权限，请向您的 IT 团队寻求帮助。







### 沙盒命令无法到达网络


某些 Codex 聊天是故意在没有出站网络访问权限的情况下运行的，
具体取决于所使用的权限模式。

如果任务因无法到达网络而失败：

1. 检查任务是否应该在禁用网络的情况下运行。
2. 如果您希望访问网络，请重新启动 Codex 并重试。
3. 如果问题持续发生，请收集沙盒日志，以便团队可以检查
机器是否处于部分或破碎的沙箱状态。







### 沙箱以前有效，然后停止了


这可能发生在以下时间之后：

- 移动存储库或工作区，
- 更改机器权限，
- 改变Windows政策，
- 或其他系统配置更改。

尝试什么：

1. 重新启动Codex。
2. 对于 MXC，重复 [ 兼容性探测 ](<#mxc-兼容性>) 并检查
有效的网络政策。对于旧版 `elevated` 实现，请再次尝试 
 沙箱设置。
3. 如果需要旧沙箱并且托管策略允许，请使用
`unelevated` 作为临时后备。
4. 收集沙箱日志以供审核。







### 我需要将诊断发送到 OpenAI


如果仍有问题，请发送：

- `CODEX_HOME/.sandbox/sandbox.log`

包括以下内容也很有帮助：

- 对您想要做的事情的简短描述，
- 所选实现：`mxc`、`elevated` 或 `unelevated`，
- 应用程序中显示的任何错误消息，
- 无论您是否看到 `1385` 或其他 Windows 或 PowerShell 错误，
- 您的 Windows 内部版本号，
- 以及您使用的是 Windows 11 还是 Windows 10。

请勿发送：

- `CODEX_HOME/.sandbox-secrets/`的内容







### IDE 扩展已安装但没有响应



您的系统可能缺少 C++ 开发工具，某些本机依赖项需要这些工具：

- Visual Studio 构建工具（C++ 工作负载）
- Microsoft Visual C++ 可再发行组件 (x64)
- 使用 `winget`，运行 `winget install --id Microsoft.VisualStudio.2022.BuildTools -e`

然后安装后完全重新启动VS Code。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Windows sandbox

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Use Codex on Windows with the native [ChatGPT desktop app](<https://learn.chatgpt.com/docs/windows/windows-app>), the
[CLI](<https://learn.chatgpt.com/docs/codex/cli>), or the [IDE extension](<https://learn.chatgpt.com/docs/codex/ide>).

The ChatGPT desktop app on Windows supports core workflows such as parallel chats,
worktrees, scheduled tasks, Git functionality, the built-in browser, file previews,
plugins, and skills.

The app can run natively in PowerShell with a Windows sandbox instead of
requiring WSL or a virtual machine. This keeps Codex in Windows-native
workflows while enforcing bounded filesystem and network permissions.

For enterprise installation, see the [Windows Deployment Guide](<https://learn.chatgpt.com/docs/enterprise/windows-deployment>).


  

> Illustration: ChatGPT desktop app Windows sandbox setup prompt above the message composer






  <CodexCallout
    href="/codex/windows/windows-app"
    title="Use the ChatGPT desktop app on Windows"
    description="Work across projects, run parallel chats, and review results in one place with the native Windows app."
    iconSrc="/images/codex/codex-banner-icon.webp"
  />



Codex supports three Windows sandbox implementations:

- `mxc`: The recommended sandbox on compatible Windows devices. Uses native process isolation without administrator-approved setup, additional Windows accounts, or local firewall rules.
- `elevated`: The preferred legacy fallback when MXC is unavailable or disabled. Requires administrator-approved setup. Commands in the sandbox run without administrator privileges.
- `unelevated`: A legacy fallback when elevated setup is unavailable and organizational policy permits it. Has weaker network isolation than `elevated` and doesn't support denied read paths.





## Configure the Windows sandbox

The Windows sandbox enforces the active filesystem and network permissions for commands and their child processes. The permission profile determines which paths are readable or writable and whether network access is allowed. Approval policy separately controls when Codex asks to run commands with more access. See [sandbox and approvals](<https://learn.chatgpt.com/docs/agent-approvals-security>).





### Enable MXC

The desktop app automatically prefers MXC for consumer accounts when the device supports it. For enterprise deployments or standalone CLI use, you can enable the same behavior with the configuration below.

In `config.toml`:

```toml
[features]
prefer_mxc = true
```

With `prefer_mxc = true`, Codex uses MXC for Windows commands when the device and policy support it.
Otherwise, it uses the existing legacy selection and setup flow.

For enterprises where device support or policy varies, keep `windows.sandbox` set to your organization's permitted legacy implementation, `elevated` or `unelevated`, [as a fallback](<#configure-a-legacy-fallback>).

Administrators can distribute this configuration as a default or enforce `features.prefer_mxc = true` through `requirements.toml`. Both permit legacy fallback. See [managed configuration](<https://learn.chatgpt.com/docs/enterprise/managed-configuration>) for how defaults and requirements differ.

### Keep MXC disabled

To prevent MXC use in your organization, add this to your managed
`requirements.toml`:

```toml
[windows]
allow_mxc = false
```

This blocks both automatic MXC selection and explicit `windows.sandbox = "mxc"`. Existing legacy sandbox settings and requirements still apply.

### MXC compatibility

The host must already have the required Microsoft Execution Containers (MXC) capabilities, which Microsoft is rolling out to Windows 11 devices. Microsoft introduced MXC process isolation in **Windows 11 24H2 (build 26100.9278)** and **25H2 (build 26200.9278)**. Use the command below to verify MXC support on your device.

In Codex CLI 0.162.0 and later, you can test MXC for one command without changing the saved sandbox selection. From your project directory, run:

```powershell
codex -c windows.sandbox=mxc sandbox --include-managed-config --permission-profile :workspace -- cmd.exe /d /c echo MXC_OK
$LASTEXITCODE
```

Expected output is `MXC_OK` and exit code `0`. This checks command startup with the workspace permission profile and managed requirements.

Explicit `windows.sandbox = "mxc"` selection fails if the required native capabilities are unavailable or policy blocks MXC.

Note these compatibility limits:

- Managed networking requires effective `allow_local_binding = true`. MXC permits connections to and from services on host loopback. Proxy domain rules still apply to proxied traffic, but the proxy's additional private-network destination checks are removed.
- Remaining child processes stop when the foreground command exits. Test workflows that rely on detached development servers.

### Configure a legacy fallback

Select the fallback implementation in `config.toml`:

```toml
[windows]
sandbox = "elevated" # or "unelevated"
```

`elevated` is the preferred legacy fallback. It uses dedicated lower-privilege sandbox users, filesystem permission boundaries, firewall rules, and local policy changes needed for commands that run in the sandbox.

`unelevated` is a legacy fallback. It runs commands with a restricted Windows token derived from your current user, applies ACL-based filesystem boundaries, and uses environment-level offline controls instead of the dedicated offline-user firewall rule. It provides weaker network isolation than `elevated` and doesn't support denied read paths, but is still useful when administrator-approved setup is blocked by local or enterprise policy.

Use MXC when the device and policy support it. Otherwise, prefer `elevated`, with `unelevated` as a secondary fallback.

Enterprise administrators can constrain which legacy sandbox implementations
Codex can use through [`requirements.toml`](<https://learn.chatgpt.com/docs/enterprise/managed-configuration#admin-enforced-requirements-requirementstoml>):

```toml
[windows]
allowed_sandbox_implementations = ["elevated"]
```

This example permits `elevated` and prevents fallback to `unelevated`. It does not restrict `mxc` when MXC is available. Other managed permission and network requirements still apply. To permit either legacy implementation, include both values; Codex prefers `elevated` when no mode is selected.

See the [`requirements.toml` reference](<https://learn.chatgpt.com/docs/config-file/config-reference#requirementstoml>) for the supported values. To block MXC as well, use the separate [`windows.allow_mxc` requirement](<#keep-mxc-disabled>).

By default, both legacy sandbox modes also use a private desktop for stronger UI isolation.

### IT-led provisioning for the legacy elevated sandbox

For employees without local administrator rights, IT can install the CLI and provision the elevated sandbox before the employee starts Codex. From an elevated
deployment process, run:

```powershell
codex sandbox setup --elevated --user 'DOMAIN\alice' --codex-home 'C:\Users\alice\.codex'
```

Replace the identity and path with the employee's Windows identity and `CODEX_HOME`. The command reads that user's configuration, provisions the sandbox, and saves `windows.sandbox = "elevated"`. The employee then runs Codex from a normal terminal.

### Sandbox permissions

Running Codex in full access mode means Codex is not limited to your project
  directory and might perform unintentional destructive actions that can lead to
  data loss. For safer automation, keep sandbox boundaries in place and use
  [rules](<https://learn.chatgpt.com/docs/agent-configuration/rules>) for specific exceptions, or set your
  [approval policy to
  never](<https://learn.chatgpt.com/docs/agent-approvals-security#run-without-approval-prompts>) to have
  Codex attempt to solve problems without asking for escalated permissions,
  based on your [approval and security setup](<https://learn.chatgpt.com/docs/agent-approvals-security>).

### Windows version matrix

| Windows version                  | Support level   | Notes                                                                                                                                                                                 |
| -------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Windows 11                       | Recommended     | Best baseline for Codex on Windows. Use this if you are standardizing an enterprise deployment.                                                                                       |
| Recent, fully updated Windows 10 | Best effort     | Can work, but is less reliable than Windows 11. For Windows 10, Codex depends on modern console support, including ConPTY. In practice, Windows 10 version 1809 or newer is required. |
| Older Windows 10 builds          | Not recommended | More likely to miss required console components such as ConPTY and more likely to fail in enterprise setups.                                                                          |

Additional environment assumptions:

- `winget` should be available. If it's missing, update Windows or install
  the Windows Package Manager before setting up Codex.
- The legacy `elevated` sandbox depends on administrator-approved setup.
- Some enterprise-managed devices block the required setup steps even when the
  OS version itself is acceptable.
- MXC additionally requires the native capabilities described in
  [MXC compatibility](<#mxc-compatibility>); this matrix doesn't establish MXC
  availability on a particular device.

### Check sandbox read access

When a command can't read a directory, check the active permission profile,
managed requirements, and Windows file permissions. In the CLI, use `/status`
and `/debug-config` to inspect the active session and configuration. Ask your
administrator to review a managed restriction rather than disabling the sandbox.





Use the native Windows sandbox by default. Choose [WSL](<https://learn.chatgpt.com/docs/windows/wsl>)
when you need Linux-native tooling, your workflow already lives in WSL2, or
the available native Windows implementations don't meet your needs.

## Troubleshooting and FAQ

If you are troubleshooting a managed Windows machine, start with the native
sandbox mode, Windows version, and any policy error shown by Codex. For MXC,
check [compatibility](<#mxc-compatibility>) and the effective network policy.
Legacy sandbox issues can come from setup, logon rights, or filesystem permissions.



### My legacy sandbox setup failed


If Codex cannot complete the `elevated` sandbox setup, the most common causes
are:

- the Windows UAC or administrator prompt was declined,
- the machine does not allow local user or group creation,
- the machine does not allow firewall rule changes,
- the machine blocks the logon rights needed by the sandbox users,
- or another enterprise policy blocks part of the setup flow.

What to try:

1. Try the `elevated` sandbox setup again and approve the administrator prompt
   if your environment allows it.
2. If your company laptop blocks this, ask your IT team whether the machine
   allows administrator-approved setup for local user/group creation, firewall
   configuration, and the required sandbox-user logon rights.
3. If setup still fails and managed policy permits it, use `unelevated` while
   the issue is investigated.







### Codex switched me to the unelevated sandbox


The `unelevated` implementation may be selected in configuration or used as a
fallback when `elevated` setup isn't available.

- Codex can still run in a sandboxed mode.
- It still applies ACL-based filesystem boundaries, but it does not use the
  separate sandbox-user boundary from `elevated` and has weaker network
  isolation.
- This is a useful fallback, but not the preferred long-term enterprise
  configuration.

For a managed enterprise laptop, check [MXC compatibility](<#mxc-compatibility>)
first. If MXC is unavailable or disabled, ask your IT team to provision
`elevated`.







### I see Windows error 1385


If sandboxed commands fail with error `1385`, Windows is denying the logon type
the sandbox user needs in order to start the command.

In practice, this usually means Codex created the sandbox users successfully,
but Windows policy is still preventing those users from launching sandboxed
commands.

What to do:

1. Ask your IT team whether the device policy grants the required logon rights
   to the Codex-created sandbox users.
2. Compare group policy or OU differences if the issue affects only some
   machines or teams.
3. If you need to keep working immediately, use the `unelevated` sandbox while
   the policy issue is investigated.
4. Send `CODEX_HOME/.sandbox/sandbox.log` along with your Windows version and a
   short description of the failure.







### Codex warns that some folders are writable by Everyone


Codex may warn that some folders are writable by `Everyone`.

If you see this warning, Windows permissions on those folders are too broad for
the sandbox to fully protect them.

What to do:

1. Review the folders Codex lists in the warning.
2. Remove `Everyone` write access from those folders if that is appropriate in
   your environment.
3. Restart Codex or re-run the sandbox setup after those permissions are
   corrected.

If you are not sure how to change those permissions, ask your IT team for help.







### Sandboxed commands cannot reach the network


Some Codex chats are intentionally run without outbound network access,
depending on the permissions mode in use.

If a task fails because it cannot reach the network:

1. Check whether the task was supposed to run with network disabled.
2. If you expected network access, restart Codex and try again.
3. If the issue keeps happening, collect the sandbox log so the team can check
   whether the machine is in a partial or broken sandbox state.







### Sandboxing worked before and then stopped


This can happen after:

- moving a repo or workspace,
- changing machine permissions,
- changing Windows policies,
- or other system configuration changes.

What to try:

1. Restart Codex.
2. For MXC, repeat the [compatibility probe](<#mxc-compatibility>) and check the
   effective network policy. For the legacy `elevated` implementation, try
   sandbox setup again.
3. If a legacy sandbox is needed and managed policy permits it, use
   `unelevated` as a temporary fallback.
4. Collect the sandbox log for review.







### I need to send diagnostics to OpenAI


If you still have problems, send:

- `CODEX_HOME/.sandbox/sandbox.log`

It is also helpful to include:

- a short description of what you were trying to do,
- the selected implementation: `mxc`, `elevated`, or `unelevated`,
- any error message shown in the app,
- whether you saw `1385` or another Windows or PowerShell error,
- your Windows build number,
- and whether you are on Windows 11 or Windows 10.

Do not send:

- the contents of `CODEX_HOME/.sandbox-secrets/`







### The IDE extension is installed but unresponsive



Your system may be missing C++ development tools, which some native dependencies require:

- Visual Studio Build Tools (C++ workload)
- Microsoft Visual C++ Redistributable (x64)
- With `winget`, run `winget install --id Microsoft.VisualStudio.2022.BuildTools -e`

Then fully restart VS Code after installation.


</details>
