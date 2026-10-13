# 在 Windows 与 WSL 中运行 OpenCode

环境指南。展示 WSL 安装和 CLI、桌面或网页客户端连接，以及 Windows 文件访问与性能建议，帮助准备 Windows 开发环境。

- 来源：OpenCode
- 资料类型：环境指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-06](<https://github.com/anomalyco/opencode/commit/288a49165138cbe8071d7f65bdc37a1d627d4d2e>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：资料是厂商的环境建议，用户实际仓库位置不因此自动迁移。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/windows-wsl.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/在 Windows 与 WSL 中运行 OpenCode.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Windows (WSL)
description: Run OpenCode on Windows using WSL for the best experience.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

虽然 OpenCode 可以直接在 Windows 上运行，但我们建议使用 [Windows Subsystem for Linux (WSL)](<https://learn.microsoft.com/en-us/windows/wsl/install>) 以获得最佳体验。 WSL提供了一个与OpenCode的功能无缝协作的Linux环境。

**提示：为什么是WSL？：**

WSL提供更好的文件系统性能、完整的终端支持以及与OpenCode所依赖的开发工具的兼容性。

---

## 设置

1. **安装WSL**

如果您还没有安装，请使用官方 Microsoft 指南 [ 安装 WSL](<https://learn.microsoft.com/en-us/windows/wsl/install>)。

2. **在WSL**中安装OpenCode

设置 WSL 后，打开 WSL 终端并使用 [ 安装方法 ](<https://opencode.ai/docs/>) 之一安装 OpenCode。

```bash
curl -fsSL https://opencode.ai/install | bash
```

3. **使用WSL**中的OpenCode

导航到您的项目目录（通过 `/mnt/c/`、`/mnt/d/` 等访问 Windows 文件）并运行 OpenCode。

```bash
cd /mnt/c/Users/YourName/project
opencode
```

---

## 桌面应用程序 + WSL 服务器

如果您更喜欢使用 OpenCode 桌面应用程序，但想在 WSL 中运行服务器：

1. **用`--hostname 0.0.0.0`启动WSL**中的服务器以允许外部连接：

```bash
opencode serve --hostname 0.0.0.0 --port 4096
```

2. **将桌面应用程序**连接到`http://localhost:4096`

**注释：**

如果 `localhost` 在您的设置中不起作用，请改用 WSL IP 地址进行连接（来自 WSL：`hostname -I`）并使用 `http://<wsl-ip>:4096`。

**注意：**

使用`--hostname 0.0.0.0`时，设置`OPENCODE_SERVER_PASSWORD`以保护服务器。

```bash
OPENCODE_SERVER_PASSWORD=your-password opencode serve --hostname 0.0.0.0
```

---

## 网页客户端 + WSL

为了在 Windows 上获得最佳网络体验：

1. **在WSL终端**中运行`opencode web`而不是PowerShell：

```bash
opencode web --hostname 0.0.0.0
```

2. **从 Windows 浏览器访问 **，位于 `http://localhost:<port>`（OpenCode 打印 URL）

从 WSL 运行 `opencode web` 可确保正确的文件系统访问和终端集成，同时仍可从 Windows 浏览器进行访问。

---

## 访问 Windows 文件

WSL可以通过`/mnt/`目录访问您的所有Windows文件：

- `C:` 驱动器 → `/mnt/c/`
- `D:` 驱动器 → `/mnt/d/`
- 等等...

示例：

```bash
cd /mnt/c/Users/YourName/Documents/project
opencode
```

**提示：**

为了获得最流畅的体验，请考虑将您的存储库 cloning/copying 存储到 WSL 文件系统中（例如在 `~/code/` 下）并在那里运行 OpenCode。

---

## 温馨提示

- 对于存储在 Windows 驱动器上的项目，保持 OpenCode 在 WSL 中运行 - 文件访问是无缝的
- 将 VS Code 的 [WSL 扩展 ](<https://code.visualstudio.com/docs/remote/wsl>) 与 OpenCode 一起使用以实现集成开发工作流程
- 您的 OpenCode 配置和会话存储在 WSL 环境中的 `~/.local/share/opencode/`


## 原文对照

<details>
<summary>展开完整原文</summary>

While OpenCode can run directly on Windows, we recommend using [Windows Subsystem for Linux (WSL)](<https://learn.microsoft.com/en-us/windows/wsl/install>) for the best experience. WSL provides a Linux environment that works seamlessly with OpenCode's features.

**提示：Why WSL?：**

WSL offers better file system performance, full terminal support, and compatibility with development tools that OpenCode relies on.

---

## Setup

1. **Install WSL**

 If you haven't already, [install WSL](<https://learn.microsoft.com/en-us/windows/wsl/install>) using the official Microsoft guide.

2. **Install OpenCode in WSL**

 Once WSL is set up, open your WSL terminal and install OpenCode using one of the [installation methods](<https://opencode.ai/docs/>).

```bash
curl -fsSL https://opencode.ai/install | bash
```

3. **Use OpenCode from WSL**

 Navigate to your project directory (access Windows files via `/mnt/c/`, `/mnt/d/`, etc.) and run OpenCode.

```bash
cd /mnt/c/Users/YourName/project
opencode
```

---

## Desktop App + WSL Server

If you prefer using the OpenCode Desktop app but want to run the server in WSL:

1. **Start the server in WSL** with `--hostname 0.0.0.0` to allow external connections:

```bash
opencode serve --hostname 0.0.0.0 --port 4096
```

2. **Connect the Desktop app** to `http://localhost:4096`

**注释：**

If `localhost` does not work in your setup, connect using the WSL IP address instead (from WSL: `hostname -I`) and use `http://<wsl-ip>:4096`.

**注意：**

When using `--hostname 0.0.0.0`, set `OPENCODE_SERVER_PASSWORD` to secure the server.

```bash
OPENCODE_SERVER_PASSWORD=your-password opencode serve --hostname 0.0.0.0
```

---

## Web Client + WSL

For the best web experience on Windows:

1. **Run `opencode web` in the WSL terminal** rather than PowerShell:

```bash
opencode web --hostname 0.0.0.0
```

2. **Access from your Windows browser** at `http://localhost:<port>` (OpenCode prints the URL)

Running `opencode web` from WSL ensures proper file system access and terminal integration while still being accessible from your Windows browser.

---

## Accessing Windows Files

WSL can access all your Windows files through the `/mnt/` directory:

- `C:` drive → `/mnt/c/`
- `D:` drive → `/mnt/d/`
- And so on...

Example:

```bash
cd /mnt/c/Users/YourName/Documents/project
opencode
```

**提示：**

For the smoothest experience, consider cloning/copying your repo into the WSL filesystem (for example under `~/code/`) and running OpenCode there.

---

## Tips

- Keep OpenCode running in WSL for projects stored on Windows drives - file access is seamless
- Use VS Code's [WSL extension](<https://code.visualstudio.com/docs/remote/wsl>) alongside OpenCode for an integrated development workflow
- Your OpenCode config and sessions are stored within the WSL environment at `~/.local/share/opencode/`


</details>
