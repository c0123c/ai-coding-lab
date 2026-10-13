# Qwen Code 沙箱配置

比较 Linux 工具级限制、macOS 与容器方案，介绍前提、迁移、文件网络设置、镜像、代理及常见问题。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-03](<https://github.com/QwenLM/qwen-code/commit/09411aa0a9193b2e1d86f1a8de7c65e513757a52>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/sandbox.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/sandbox.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 沙盒

本文档解释了 Linux 上的工具执行限制和现有的整体 CLI 沙箱方法。

## Linux工具执行沙箱

在 Linux 上，在 `/usr/bin/bwrap`（受支持的系统路径）处安装 `bwrap` (Bubblewrap)，以获得最强大的受支持边界，然后在 **用户 ** 设置（`~/.qwen/settings.json` 或 `QWEN_HOME` 选择的目录）或管理员中启用工具执行沙箱**系统**设置：

```json
{
  "tools": {
    "executionSandbox": {
      "backend": "auto",
      "filesystem": "workspace-write",
      "network": "closed"
    }
  }
}
```

需要 `filesystem` 和 `network`。 `backend` 默认为 `auto`，它首先尝试 bwrap。当 bwrap 不可用且 `network` 为 `open` 时，`auto` 可以在具有 Landlock ABI 3 或更新版本的内核上使用捆绑的 Landlock 帮助程序。将 `backend` 设置为 `bwrap` 或 `landlock` 以要求确切的实现。 Landlock 无法强制执行 `network: closed` 或隐藏显式屏蔽路径。在任何命令开始之前，任一要求都会拒绝 Landlock；自动回退永远不会丢弃剩余的路径掩码。使用 `read-only` 拒绝工作区写入，或使用 `workspace-write` 允许在当前规范工作区内进行写入。每个命令还获得私有暂存空间。普通 `includeDirectories` 设置不授予写入权限。将工作区与 Qwen 安装、用户配置和运行时状态目录分开。

CLI、模型传输、身份验证和会话存储保留在主机上。 Shell、终端 `!`、提示 shell 插值、监视器、Read/Write/Edit 和支持的嵌套进程内 Agent/Code 模式调用使用相同的运行时策略。使用 bwrap，`network: closed` 会阻止来自命令的普通 host/external IP 连接，而 `open` 共享主机网络。读取范围仍然广泛：此模式不会隐藏主机秘密或承诺隔离路径名 Unix 套接字和主机服务。批准和 YOLO 不会扩展文件系统或网络策略。标准流是调用者提供的显式功能：已连接的套接字、TTY 或字符设备即使使用 `network: closed` 仍然可用。封闭网络不会撤销那些现有的描述符；当不需要访问时，避免将主机服务连接作为标准输入提供。主机支持的文件和名为 FIFO 标准输入需要捆绑的 Linux x64/arm64 输入帮助程序，并通过私有 Unix 流套接字复制，其写半关闭；常规文件偏移量仅提前从该套接字消耗的字节，并且空闲的 FIFO 编写器不会延迟命令完成。直接读取fd 0：该输入是一个套接字而不是FIFO，并且不支持通过`/dev/stdin`或`/proc/self/fd/0`重新打开它。 SIGINT/SIGTERM取消命令，但仍然耗尽接受的stdout/stderr字节；关闭的输出通过退出 141 停止该流，而其他有效输出则耗尽。

Landlock 报告 `partial` 强制执行，因为它限制路径名读取、写入和目录突变，但不会创建 PID 或网络命名空间，并且当前无法限制每个元数据操作，包括 `chmod`、`chown`、扩展属性和时间戳。 Landlock 命令可以查看主机进程并使用主机网络。分离的后代保留 Landlock 文件系统限制，但不会获得 bwrap 的 PID- 命名空间生命周期边界。打开的文件描述符保留其现有的访问权限。 Qwen Code 通过私有只读套接字流式传输主机支持的文件和名为 FIFO stdin，而不是将这些描述符传递给有效负载；套接字、字符设备和匿名管道可以共享。输出标准流仍然是调用者的显式目的地。执行状态描述符在有效负载执行时关闭。

Landlock 还有一个较窄的可写设备表面：它允许写入 `/dev/null`，但不会创建 bwrap 的私有 `/dev` 和 `/proc` 挂载。打开`/dev/full`或`/dev/tty`以在`/dev/shm`中写入和创建文件被拒绝，因此需要这些操作的程序可能会失败。使用通过 `TMPDIR`、`TMP` 和 `TEMP` 公开的私有临时目录作为临时文件。已经开放的、调用者提供的标准流保留其现有的访问权限。 Startup 的 Landlock 策略探测需要 `/usr/bin/true`；缺少它的主机无法探测，即使其内核支持 Landlock。

工作区设置无法启用、禁用或修改此策略，即使在受信任的项目中也是如此。有效优先级是系统优先于用户优先于 SystemDefaults，选择完整的策略对象。值必须是文字；环境替换、未知字段和不完整的对象都会被拒绝。 `--bare` 和 `--safe-mode` 保留操作员限制。更改需要新的运行时。格式错误或不可读的 SystemDefaults、用户或系统设置文件现在会阻止启动（配置退出代码 52），而不是默默地将未知策略重置为空设置。错误命名文件和修复操作。修复其 JSON 对象或恢复读取访问权限，然后重新启动。用户 `.corrupted` 副本仅供参考；原始操作员文件永远不会被清除。仅工作区恢复仍然可用，无需操作员限制。格式错误的链接工作区设置（包括链接的 `.qwen` 目录或硬链接文件）需要手动修复；在更改原始或以前的损坏副本之前，自动恢复会被拒绝。有效链接的 JSON 仍然可读。

此初始公共模式支持普通无头 CLI 和两个终端 UI。 ACP、`qwen serve` 和网络终端明确拒绝它。 MCP/LSP、可执行文件 hooks/extensions、worktree/arena 管理、外部 agents、推测执行、文件检查点、自动 skill/memory 维护以及未移植的进程或突变工具被禁用或拒绝。普通同工作空间进程内agents无法添加钩子、MCP服务器、外部执行器或工作树隔离。禁用自定义状态命令、外部 Markdown/image 渲染器、IDE 检测和连接、文件恢复以及主机 Git diff 预览。仅对话倒带仍然可用。

显式操作员诊断（例如 `/doctor`、通过 `/memory` 打开或编辑文件和文件夹以及通过 `/skills` 更改设置）仍然是受信任的主机操作。这些管理命令不公开为模型可调用命令。选择一个技能填充输入字段；目前，在准备参数或更新使用记录之前，提交 `/skill-name` 会报告不支持模式错误。技能命令执行和模型技能工具在其准备写入受到限制之前保持不可用。即使您更改了保存的设置，自动内存和技能维护在此运行时仍处于禁用状态。

在运行任务之前检查并验证活动边界：

```bash
qwen sandbox
qwen sandbox --verify
qwen sandbox -- sh -c 'printf "confined command\n"'
qwen -y -p "Update the project and run its tests"
```

链接的 Git 工作树仅接收对承认的工作区的写访问权限。该工作区之外的 Git 公共目录和工作树元数据保持只读；此模式不会隐式授权更改另一个签出的存储库元数据。需要这些写入的操作可以被拒绝。

该报告命名了工具边界、requested/effective后端、实施级别、锁ABI（如果适用）、工作区、文件系统和命令网络策略。 `--verify` 检查工作区写入、拒绝已知在主机上可写的文件、预期的私有或共享 PID 命名空间标识以及选定的网络命名空间。通过的后端探测在所有继承的环境设置下建立准入，而不是经过身份验证的完整会话可行性。随后运行普通任务来测试其所需的工具和服务。如果验证无法创建其主机端临时fixture，请检查`TMPDIR`和目录权限；这与边界拒绝是分开的。后端设置错误在有效负载执行之前失败，并且永远不会在主机上重新运行该命令。单命令子命令转发文字参数并重定向 stdin，同时逐字节保留 stdout 和 stderr；使用终端 `!` 执行交互式 PTY 命令。

### 从整个CLI bwrap 迁移

替换`--sandbox bwrap`并删除`tools.sandbox: "bwrap"`、`QWEN_SANDBOX=bwrap`、`QWEN_SANDBOX_NET`和`QWEN_SANDBOX_PROXY_COMMAND`，然后按上述配置`tools.executionSandbox`。从旧沙箱外部的 shell 重新启动；继承的`SANDBOX=bwrap`被拒绝。 `proxied` 不是受支持的网络策略。请勿将此模式与 Docker、Podman、Seatbelt 或继承的整体 CLI 沙箱标记结合使用。旧的 bwrap 设置会产生显式迁移错误。

下面的 Docker、Podman 和 macOS Seatbelt 方法仍然在其现有环境中运行整个 CLI。

## 先决条件

使用沙箱之前，需要安装并设置【Qwen Code】：

```bash
npm install -g @qwen-code/qwen-code
```

验证安装

```bash
qwen --version
```

## 沙盒概述

沙箱将潜在危险的操作（例如 shell 命令或文件修改）与您的主机系统隔离，在 CLI 和您的环境之间提供安全屏障。

沙箱的好处包括：

- **安全**：防止意外的系统损坏或数据丢失。
- **隔离**：限制文件系统对项目目录的访问。
- **一致性**：确保不同系统之间的可重现环境。
- **安全**：降低使用不受信任的代码或实验命令时的风险。

> [!note]
>
> **命名说明：** 一些与沙箱相关的环境变量可能曾经使用过 `GEMINI_*` 前缀。所有新环境变量都使用 `QWEN_*` 前缀。

## 沙箱方法

您理想的沙盒方法可能会有所不同，具体取决于您的平台和首选容器解决方案。

### 1。 macOS安全带（仅限macOS）

使用 `sandbox-exec` 的轻量级内置沙箱。

**默认配置文件**：`permissive-open` - 限制项目目录外部的写入，但允许大多数其他操作和出站网络访问。

**最适合**：快速，不需要 Docker，文件写入的强大护栏。

### 2。基于容器（Docker/Podman）

具有完全进程隔离的跨平台沙箱。

默认情况下，Qwen Code 使用已发布的沙箱映像（在 CLI 包中配置）并根据需要拉取它。

容器沙箱将您的工作区和 `~/.qwen` 目录装载到容器中，以便身份验证和设置在运行之间保持不变。

**最适合**：对任何OS进行强隔离，在已知图像内使用一致的工具。

### 选择方法

- **在macOS**上：
  - 当您需要轻量级沙箱时，请使用 Seatbelt（推荐大多数用户）。
  - 当您需要完整的 Linux 用户空间（例如，需要 Linux 二进制文件的工具）时，请使用 Docker/Podman。
- **在Linux/Windows**上：
  - 使用 Docker 或 Podman。

## 快速入门

```bash
# Enable sandboxing with command flag
qwen -s -p "analyze the code structure"

# Or enable sandboxing for your shell session (recommended for CI / scripts)
export QWEN_SANDBOX=true   # true auto-picks a provider (see notes below)
qwen -p "run the test suite"

# Configure in settings.json
{
  "tools": {
    "sandbox": true
  }
}
```

> [!提示]
>
> **提供商选择说明：**
>
> - 在 **macOS** 上，`QWEN_SANDBOX=true` 通常选择 `sandbox-exec`（安全带）（如果可用）。
> - 在**Linux/Windows**上，`QWEN_SANDBOX=true`需要安装`docker`或`podman`。
> - 要强制提供程序，请设置 `QWEN_SANDBOX=docker|podman|sandbox-exec`。

## 配置

### 启用沙箱（按优先顺序）

1. **环境变量**：`QWEN_SANDBOX=true|false|docker|podman|sandbox-exec`
2. **命令标志/参数**：`-s`、`--sandbox`或`--sandbox=<provider>`
3. **设置文件**：`settings.json`中的`tools.sandbox`（例如，`{"tools": {"sandbox": true}}`）。

> [！重要]
>
> 如果设置了`QWEN_SANDBOX`，则**将覆盖**、CLI标志和`settings.json`。

`--sandbox` 和 `-s` 接受可选值：`true`、`false`、`docker`、`podman` 或 `sandbox-exec`。对于带提示的自动选择，请使用 `qwen --sandbox=true "query"` 或 `qwen --sandbox -p "query"`。与旧的纯布尔解析器不同，`qwen --sandbox "query"` 现在将 `query` 视为后端值并拒绝未知后端。 `--` 之前的选项即使在位置词之后也使用相同的语法；使用 `-p "..."` 作为提示中的文字选项文本。 `--` 停止选项解析，但其尾随标记不会作为默认命令提示符转发。管理子命令不接受会话沙箱标志，其 `-s` 别名保留其自己的含义。

### 配置沙箱镜像（Docker/Podman）

- **CLI 标志**: `--sandbox-image <image>`
- **环境变量**：`QWEN_SANDBOX_IMAGE=<image>`
- **设置文件**：`settings.json`中的`tools.sandboxImage`（例如，`{"tools": {"sandboxImage": "ghcr.io/qwenlm/qwen-code:0.14.1"}}`）

优先级顺序（从高到低）：

1. `--sandbox-image`
2. `QWEN_SANDBOX_IMAGE`
3. `tools.sandboxImage`
4. 来自 CLI 包的内置默认图像（例如 `ghcr.io/qwenlm/qwen-code:<version>`）

`settings.env.QWEN_SANDBOX_IMAGE` 也可以用作通用环境注入机制，但 `tools.sandboxImage` 是首选的持久设置。

自定义图像由用户管理。使用最新的 Qwen Code 安装重建它们以接收安全更新切换；较旧的图像可能仍使用其原始的进程内更新程序。

### macOS 安全带型材

内置配置文件（通过 `SEATBELT_PROFILE` env var 设置）：

- `permissive-open`（默认）：写入限制，网络允许
- `permissive-closed`：写入限制，无网络
- `permissive-proxied`：写入限制，通过代理网络
- `restrictive-open`：严格限制，网络允许
- `restrictive-closed`：最大限制
- `restrictive-proxied`：严格限制，通过代理网络

> [!提示]
>
> 从 `permissive-open` 开始，如果您的工作流程仍然有效，然后收紧到 `restrictive-closed`。

### 定制安全带型材 (macOS)

要使用自定义安全带配置文件：

1. 在您的项目中创建一个名为 `.qwen/sandbox-macos-<profile_name>.sb` 的文件。
2. 设置`SEATBELT_PROFILE=<profile_name>`。

### 自定义沙箱标志

对于基于容器的沙箱，您可以使用 `SANDBOX_FLAGS` 环境变量将自定义标志注入到 `docker` 或 `podman` 命令中。这对于高级配置非常有用，例如禁用特定用例的安全功能。

**示例（Podman）**：

要禁用卷挂载的 SELinux 标签，您可以设置以下内容：

```bash
export SANDBOX_FLAGS="--security-opt label=disable"
```

多个标志可以作为空格分隔的字符串提供：

```bash
export SANDBOX_FLAGS="--flag1 --flag2=value"
```

### 网络代理（所有沙箱方法）

如果您想限制对白名单的出站网络访问，您可以在沙箱旁边运行本地代理：

- 设置`QWEN_SANDBOX_PROXY_COMMAND=<command>`
- 该命令必须启动侦听 `:::8877` 的代理服务器

这对于 `*-proxied` 安全带型材特别有用。

有关有效的白名单样式代理示例，请参阅：[代理脚本示例](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/proxy-script.md>)。

## Linux UID/GID 处理

在 Linux 上，Qwen Code 默认启用 UID/GID 映射，以便沙箱以您的用户身份运行（并重用已安装的 `~/.qwen`）。覆盖：

```bash
export SANDBOX_SET_UID_GID=true   # Force host UID/GID
export SANDBOX_SET_UID_GID=false  # Disable UID/GID mapping
```

## 故障排除

### 常见问题

**“不允许操作”**

- 操作需要访问沙箱外部。
- 关于 macOS 安全带：尝试更宽松的 `SEATBELT_PROFILE`。
- 在 Docker/Podman 上：验证工作区已安装，并且您的命令不需要在项目目录之外进行访问。

**缺少命令**

- 容器沙箱：通过`.qwen/sandbox.Dockerfile`或`.qwen/sandbox.bashrc`添加。
- 安全带：使用了您的主机二进制文件，但沙箱可能会限制对某些路径的访问。

**Java 在 Docker 沙箱中不可用 **

官方 Qwen Code Docker 镜像有意最小化，以保持镜像小、安全且快速拉取。不同的用户需要不同的语言运行时（Java、Python、Node.js等），将所有环境捆绑到单个映像中是不切实际的。因此，Java是**默认不包含在Docker沙箱中的**。

如果您的工作流程需要 Java，您可以通过在项目中创建 `.qwen/sandbox.Dockerfile` 来扩展基础映像：

```dockerfile
FROM ghcr.io/qwenlm/qwen-code:latest

RUN apt-get update && \
    apt-get install -y openjdk-17-jre && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

然后重建沙箱镜像：

```bash
QWEN_SANDBOX=docker BUILD_SANDBOX=1 qwen -s
```

有关自定义沙箱的更多详细信息，请参阅[自定义沙箱环境](<定制 Qwen Code 容器沙箱.md>)。

**网络问题**

- 检查沙箱配置文件是否允许网络。
- 验证代理配置。

### 调试模式

```bash
DEBUG=1 qwen -s -p "debug command"
```

**注意：**如果项目的`.env`文件中有`DEBUG=true`，由于自动排除，它不会影响CLI。使用 `.qwen/.env` 文件进行 Qwen Code 特定的调试设置。

### 检查沙箱

```bash
# Check environment
qwen -s -p "run shell command: env | grep SANDBOX"

# List mounts
qwen -s -p "run shell command: mount | grep workspace"
```

## 安全说明

- 沙盒可以减少但不能消除所有风险。
- 使用允许您工作的最严格的配置文件。
- 第一个 pull/build. 之后容器开销最小
- GUI 应用程序可能无法在沙箱中运行。

## 相关文档

- [配置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>)：完整配置选项。
- [命令](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>)：可用命令。
- [故障排除](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>)：一般故障排除。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sandbox

This document explains tool execution confinement on Linux and the existing whole-CLI sandbox methods.

## Linux tool execution sandbox

On Linux, install `bwrap` (Bubblewrap) at `/usr/bin/bwrap` (the supported system path) for the strongest supported boundary, then enable the tool execution sandbox in your **User** settings (`~/.qwen/settings.json`, or the directory selected by `QWEN_HOME`) or administrator **System** settings:

```json
{
  "tools": {
    "executionSandbox": {
      "backend": "auto",
      "filesystem": "workspace-write",
      "network": "closed"
    }
  }
}
```

`filesystem` and `network` are required. `backend` defaults to `auto`, which tries bwrap first. When bwrap is unavailable and `network` is `open`, `auto` can use the bundled Landlock helper on kernels with Landlock ABI 3 or newer. Set `backend` to `bwrap` or `landlock` to require that exact implementation. Landlock cannot enforce `network: closed` or hide explicit masked paths. Either requirement rejects Landlock before any command starts; automatic fallback never discards a remaining path mask. Use `read-only` to deny workspace writes, or `workspace-write` to permit writes inside the current canonical workspace. Each command also gets private scratch space. Ordinary `includeDirectories` settings do not grant write access. Keep the workspace separate from the Qwen installation, user configuration and runtime state directories.

The CLI, model transport, authentication and session storage remain on the host. Shell, terminal `!`, prompt shell interpolation, Monitor, Read/Write/Edit and supported nested in-process Agent/Code Mode calls use the same runtime policy. With bwrap, `network: closed` blocks ordinary host/external IP connections from commands, while `open` shares the host network. Reads remain broad: this mode does not hide host secrets or promise to isolate pathname Unix sockets and host services. Approval and YOLO do not expand the filesystem or network policy. Standard streams are explicit caller-provided capabilities: an already-connected socket, TTY or character device remains usable even with `network: closed`. Closed networking does not revoke those existing descriptors; avoid supplying a host-service connection as stdin when that access is unwanted. Host-backed file and named-FIFO stdin require the bundled Linux x64/arm64 input helper and are copied through a private Unix stream socket with its write half shut; regular-file offsets advance only by bytes consumed from that socket, and an idle FIFO writer does not delay command completion. Read fd 0 directly: this input is a socket rather than a FIFO, and reopening it through `/dev/stdin` or `/proc/self/fd/0` is unsupported. SIGINT/SIGTERM cancel the command but still drain accepted stdout/stderr bytes; a closed output stops that stream with exit 141 while the other valid output drains.

Landlock reports `partial` enforcement because it restricts pathname reads, writes and directory mutations but does not create PID or network namespaces and cannot currently restrict every metadata operation, including `chmod`, `chown`, extended attributes and timestamps. A Landlock command can see host processes and uses host networking. Detached descendants retain the Landlock filesystem restrictions but do not get bwrap's PID-namespace lifetime boundary. Open file descriptors retain their existing access. Qwen Code streams host-backed file and named-FIFO stdin through a private read-only socket instead of passing those descriptors to the payload; sockets, character devices and anonymous pipes can be shared. Output standard streams remain the caller's explicit destinations. The execution-status descriptor closes on payload exec.

Landlock also has a narrower writable device surface: it grants writes to `/dev/null`, but does not create bwrap's private `/dev` and `/proc` mounts. Opening `/dev/full` or `/dev/tty` for writing and creating files in `/dev/shm` are denied, so programs requiring these operations may fail. Use the private scratch directory exposed through `TMPDIR`, `TMP`, and `TEMP` for temporary files. Already-open, caller-provided standard streams keep their existing access. Startup's Landlock policy probe requires `/usr/bin/true`; a host missing it fails the probe even if its kernel supports Landlock.

Workspace settings cannot enable, disable or modify this policy, even in a trusted project. Effective precedence is System over User over SystemDefaults, selecting a complete policy object. Values must be literals; environment substitutions, unknown fields and incomplete objects are rejected. `--bare` and `--safe-mode` retain operator confinement. Changes require a new runtime. A malformed or unreadable SystemDefaults, User or System settings file now blocks startup (configuration exit code 52), instead of silently resetting unknown policy to empty settings. The error names the file and repair action. Repair its JSON object or restore read access, then restart. A User `.corrupted` copy is only a reference; the original operator file is never cleared. Workspace-only recovery remains available without operator confinement. Malformed linked Workspace settings (including a linked `.qwen` directory or a hard-linked file) require manual repair; automatic recovery refuses before changing the original or a previous corruption copy. Valid linked JSON remains readable.

This initial public mode supports the ordinary headless CLI and both terminal UIs. ACP, `qwen serve` and web terminals explicitly reject it. MCP/LSP, executable hooks/extensions, worktree/arena management, external agents, speculative execution, file checkpoints, automatic skill/memory maintenance, and unported process or mutation tools are disabled or rejected. Ordinary same-workspace in-process agents cannot add hooks, MCP servers, external executors or worktree isolation. Custom status commands, external Markdown/image renderers, IDE detection and connection, file restore, and host Git diff previews are disabled. Conversation-only rewind remains available.

Explicit operator diagnostics such as `/doctor`, opening or editing files and folders through `/memory`, and changing settings through `/skills` remain trusted host actions. These management commands are not exposed as model-invocable commands. Picking a skill fills the input field; submitting `/skill-name` currently reports an unsupported-mode error before preparing arguments or updating usage records. Skill command execution and the model Skill tool remain unavailable until their preparation writes are confined. Automatic memory and skill maintenance remain disabled in this runtime even if you change their saved settings.

Inspect and verify the active boundary before running a task:

```bash
qwen sandbox
qwen sandbox --verify
qwen sandbox -- sh -c 'printf "confined command\n"'
qwen -y -p "Update the project and run its tests"
```

Linked Git worktrees receive write access to the admitted workspace only. Git common-directory and worktree metadata outside that workspace stay read-only; this mode does not implicitly authorize changing another checkout's repository metadata. Operations requiring those writes can be refused.

The report names the tool boundary, requested/effective backend, enforcement level, Landlock ABI when applicable, workspace, filesystem and command network policy. `--verify` checks workspace writes, denial against a file known to be writable on the host, the expected private or shared PID namespace identity, and the selected network namespace. A passed backend probe establishes admission, not authenticated full-session viability under all inherited environment settings. Run an ordinary task afterwards to test its required tools and services. If verification cannot create its host-side temporary fixture, check `TMPDIR` and directory permissions; this is separate from a boundary refusal. Backend setup errors fail before payload execution and never rerun the command on the host. The one-command subcommand forwards literal arguments and redirected stdin while preserving stdout and stderr byte-for-byte; use terminal `!` for interactive PTY commands.

### Migrating from whole-CLI bwrap

Replace `--sandbox bwrap` and remove `tools.sandbox: "bwrap"`, `QWEN_SANDBOX=bwrap`, `QWEN_SANDBOX_NET` and `QWEN_SANDBOX_PROXY_COMMAND`, then configure `tools.executionSandbox` as above. Restart from a shell outside the old sandbox; inherited `SANDBOX=bwrap` is rejected. `proxied` is not a supported network policy. Do not combine this mode with Docker, Podman, Seatbelt or an inherited whole-CLI sandbox marker. Old bwrap settings produce an explicit migration error.

The Docker, Podman and macOS Seatbelt methods below still run the whole CLI in their existing environment.

## Prerequisites

Before using sandboxing, you need to install and set up Qwen Code:

```bash
npm install -g @qwen-code/qwen-code
```

To verify the installation

```bash
qwen --version
```

## Overview of sandboxing

Sandboxing isolates potentially dangerous operations (such as shell commands or file modifications) from your host system, providing a security barrier between the CLI and your environment.

The benefits of sandboxing include:

- **Security**: Prevent accidental system damage or data loss.
- **Isolation**: Limit file system access to project directory.
- **Consistency**: Ensure reproducible environments across different systems.
- **Safety**: Reduce risk when working with untrusted code or experimental commands.

> [!note]
>
> **Naming note:** Some sandbox-related environment variables may have used the `GEMINI_*` prefix historically. All new environment variables use the `QWEN_*` prefix.

## Sandboxing methods

Your ideal method of sandboxing may differ depending on your platform and your preferred container solution.

### 1. macOS Seatbelt (macOS only)

Lightweight, built-in sandboxing using `sandbox-exec`.

**Default profile**: `permissive-open` - restricts writes outside the project directory, but allows most other operations and outbound network access.

**Best for**: Fast, no Docker required, strong guardrails for file writes.

### 2. Container-based (Docker/Podman)

Cross-platform sandboxing with complete process isolation.

By default, Qwen Code uses a published sandbox image (configured in the CLI package) and will pull it as needed.

The container sandbox mounts your workspace and your `~/.qwen` directory into the container so auth and settings persist between runs.

**Best for**: Strong isolation on any OS, consistent tooling inside a known image.

### Choosing a method

- **On macOS**:
  - Use Seatbelt when you want lightweight sandboxing (recommended for most users).
  - Use Docker/Podman when you need a full Linux userland (e.g., tools that require Linux binaries).
- **On Linux/Windows**:
  - Use Docker or Podman.

## Quickstart

```bash
# Enable sandboxing with command flag
qwen -s -p "analyze the code structure"

# Or enable sandboxing for your shell session (recommended for CI / scripts)
export QWEN_SANDBOX=true   # true auto-picks a provider (see notes below)
qwen -p "run the test suite"

# Configure in settings.json
{
  "tools": {
    "sandbox": true
  }
}
```

> [!tip]
>
> **Provider selection notes:**
>
> - On **macOS**, `QWEN_SANDBOX=true` typically selects `sandbox-exec` (Seatbelt) if available.
> - On **Linux/Windows**, `QWEN_SANDBOX=true` requires `docker` or `podman` to be installed.
> - To force a provider, set `QWEN_SANDBOX=docker|podman|sandbox-exec`.

## Configuration

### Enable sandboxing (in order of precedence)

1. **Environment variable**: `QWEN_SANDBOX=true|false|docker|podman|sandbox-exec`
2. **Command flag / argument**: `-s`, `--sandbox`, or `--sandbox=<provider>`
3. **Settings file**: `tools.sandbox` in your `settings.json` (e.g., `{"tools": {"sandbox": true}}`).

> [!important]
>
> If `QWEN_SANDBOX` is set, it **overrides** the CLI flag and `settings.json`.

`--sandbox` and `-s` accept an optional value: `true`, `false`, `docker`, `podman` or `sandbox-exec`. For automatic selection with a prompt, use `qwen --sandbox=true "query"` or `qwen --sandbox -p "query"`. Unlike the old boolean-only parser, `qwen --sandbox "query"` now treats `query` as a backend value and rejects an unknown backend. Options before `--` use the same grammar even after positional words; use `-p "..."` for literal option text within a prompt. `--` stops option parsing, but its trailing tokens are not forwarded as the default-command prompt. Session sandbox flags are not accepted on management subcommands, whose `-s` aliases retain their own meaning.

### Configure the sandbox image (Docker/Podman)

- **CLI flag**: `--sandbox-image <image>`
- **Environment variable**: `QWEN_SANDBOX_IMAGE=<image>`
- **Settings file**: `tools.sandboxImage` in your `settings.json` (e.g., `{"tools": {"sandboxImage": "ghcr.io/qwenlm/qwen-code:0.14.1"}}`)

Priority order (highest to lowest):

1. `--sandbox-image`
2. `QWEN_SANDBOX_IMAGE`
3. `tools.sandboxImage`
4. Built-in default image from the CLI package (for example `ghcr.io/qwenlm/qwen-code:<version>`)

`settings.env.QWEN_SANDBOX_IMAGE` also works as a generic env injection mechanism, but `tools.sandboxImage` is the preferred persistent setting.

Custom images are user-managed. Rebuild them with an up-to-date Qwen Code installation to receive the safe update handoff; older images may still use their original in-process updater.

### macOS Seatbelt profiles

Built-in profiles (set via `SEATBELT_PROFILE` env var):

- `permissive-open` (default): Write restrictions, network allowed
- `permissive-closed`: Write restrictions, no network
- `permissive-proxied`: Write restrictions, network via proxy
- `restrictive-open`: Strict restrictions, network allowed
- `restrictive-closed`: Maximum restrictions
- `restrictive-proxied`: Strict restrictions, network via proxy

> [!tip]
>
> Start with `permissive-open`, then tighten to `restrictive-closed` if your workflow still works.

### Custom Seatbelt profiles (macOS)

To use a custom Seatbelt profile:

1. Create a file named `.qwen/sandbox-macos-<profile_name>.sb` in your project.
2. Set `SEATBELT_PROFILE=<profile_name>`.

### Custom Sandbox Flags

For container-based sandboxing, you can inject custom flags into the `docker` or `podman` command using the `SANDBOX_FLAGS` environment variable. This is useful for advanced configurations, such as disabling security features for specific use cases.

**Example (Podman)**:

To disable SELinux labeling for volume mounts, you can set the following:

```bash
export SANDBOX_FLAGS="--security-opt label=disable"
```

Multiple flags can be provided as a space-separated string:

```bash
export SANDBOX_FLAGS="--flag1 --flag2=value"
```

### Network proxying (all sandbox methods)

If you want to restrict outbound network access to an allowlist, you can run a local proxy alongside the sandbox:

- Set `QWEN_SANDBOX_PROXY_COMMAND=<command>`
- The command must start a proxy server that listens on `:::8877`

This is especially useful with `*-proxied` Seatbelt profiles.

For a working allowlist-style proxy example, see: [Example Proxy Script](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/developers/examples/proxy-script.md>).

## Linux UID/GID handling

On Linux, Qwen Code defaults to enabling UID/GID mapping so the sandbox runs as your user (and reuses the mounted `~/.qwen`). Override with:

```bash
export SANDBOX_SET_UID_GID=true   # Force host UID/GID
export SANDBOX_SET_UID_GID=false  # Disable UID/GID mapping
```

## Troubleshooting

### Common issues

**"Operation not permitted"**

- Operation requires access outside sandbox.
- On macOS Seatbelt: try a more permissive `SEATBELT_PROFILE`.
- On Docker/Podman: verify the workspace is mounted and your command doesn’t require access outside the project directory.

**Missing commands**

- Container sandbox: add them via `.qwen/sandbox.Dockerfile` or `.qwen/sandbox.bashrc`.
- Seatbelt: your host binaries are used, but the sandbox may restrict access to some paths.

**Java not available in Docker sandbox**

The official Qwen Code Docker image is intentionally minimal to keep the image small, secure, and fast to pull. Different users require different language runtimes (Java, Python, Node.js, etc.), and bundling all environments into a single image is not practical. Therefore, Java is **not included by default** in the Docker sandbox.

If your workflow requires Java, you can extend the base image by creating a `.qwen/sandbox.Dockerfile` in your project:

```dockerfile
FROM ghcr.io/qwenlm/qwen-code:latest

RUN apt-get update && \
    apt-get install -y openjdk-17-jre && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

Then rebuild the sandbox image:

```bash
QWEN_SANDBOX=docker BUILD_SANDBOX=1 qwen -s
```

For more details on customizing the sandbox, see [Customizing the sandbox environment](<定制 Qwen Code 容器沙箱.md>).

**Network issues**

- Check sandbox profile allows network.
- Verify proxy configuration.

### Debug mode

```bash
DEBUG=1 qwen -s -p "debug command"
```

**Note:** If you have `DEBUG=true` in a project's `.env` file, it won't affect the CLI due to automatic exclusion. Use `.qwen/.env` files for Qwen Code-specific debug settings.

### Inspect sandbox

```bash
# Check environment
qwen -s -p "run shell command: env | grep SANDBOX"

# List mounts
qwen -s -p "run shell command: mount | grep workspace"
```

## Security notes

- Sandboxing reduces but doesn't eliminate all risks.
- Use the most restrictive profile that allows your work.
- Container overhead is minimal after the first pull/build.
- GUI applications may not work in sandboxes.

## Related documentation

- [Configuration](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>): Full configuration options.
- [Commands](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>): Available commands.
- [Troubleshooting](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>): General troubleshooting.


</details>
