# 在 WSL 中使用 Codex

说明从 WSL 启动编辑器、确认连接、运行 CLI 和选择项目位置，并提供常见环境问题的排查路径。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/windows/wsl>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/windows/wsl.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/在 WSL 中使用 Codex.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# WSL

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

当您使用 WSL2 时，Codex 在 Linux 环境中运行，而不是使用 
 本机 [Windows 沙箱 ](<https://learn.chatgpt.com/docs/windows/windows-sandbox>)。当您需要 Linux 原生 
 工具，您的存储库和开发人员工作流程已存在于 WSL2 中，或者 
 可用的 Windows 沙箱实现无法满足您的需求时，请选择 WSL2。

WSL1 通过 Codex `0.114` 支持。从 Codex `0.115` 开始，Linux
 沙箱移至 `bubblewrap`，因此不再支持 WSL1。

## 从WSL内部发射VS Code

有关分步说明，请参阅[官方VS CodeWSL教程](<https://code.visualstudio.com/docs/remote/wsl-tutorial>)。

### 先决条件

- 安装了 WSL 的 Windows。要安装WSL，请以管理员身份打开PowerShell，然后运行`wsl --install`（Ubuntu是常见选择）。
- 安装了 [WSL 扩展 ](<https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl>) 的 VS Code。

### 从 WSL 终端打开 VS Code

```bash
# From your WSL shell
cd ~/code/your-project
code .
```

这将打开 WSL 远程窗口，根据需要安装 VS Code 服务器，并确保集成终端在 Linux 中运行。

### 确认您已连接到 WSL

- 查找显示 `WSL: <distro>` 的绿色状态栏。
- 集成终端应显示 Linux 路径（例如 `/home/...`）而不是 `C:\`。
- 您可以通过以下方式验证：

```bash
  echo $WSL_DISTRO_NAME
```

这将打印您的发行版名称。

如果状态栏中没有看到“WSL：...”，请按`Ctrl+Shift+P`，选择“WSL：
重新打开WSL`, and keep your repository under `/home/...` (not `C：\`）
中的文件夹以获得最佳性能。

如果 Windows 应用程序或项目选择器未显示您的 WSL 存储库，请在文件选择器或资源管理器中键入 
 `\\wsl$`，然后导航到 
 发行版的主目录。

## 将 Codex CLI 与 WSL 一起使用

从提升的 PowerShell 或 Windows 终端运行这些命令：

```powershell
# Install default Linux distribution (like Ubuntu)
wsl --install

# Start a shell inside Windows Subsystem for Linux
wsl
```

然后从 WSL shell 运行这些命令：

```bash
# Install and run Codex in WSL
curl -fsSL https://chatgpt.com/codex/install.sh | sh
codex
```

## 处理 WSL 内的代码

- 在 Windows 安装路径（如 `/mnt/c/...`）中工作可能比在 Windows 本机路径中工作慢。将存储库保存在 Linux 主目录下（例如 `~/code/my-app`），以获得更快的 I/O 并减少符号链接和权限问题：
```bash
  mkdir -p ~/code && cd ~/code
  git clone https://github.com/your/repo.git
  cd repo
```
- 如果您需要 Windows 访问文件，它们位于资源管理器中的 `\\wsl$\Ubuntu\home\&lt;user&gt;` 下。

## 故障排除和 FAQ



### 大型存储库在 WSL 中感觉很慢



- 确保您不在 `/mnt/c` 下工作。将存储库移至 WSL（例如，`~/code/...`）。
- 如果需要，增加内存和WSL的CPU；将WSL更新到最新版本：
```powershell
  wsl --update
  wsl --shutdown
```







### WSL 中的 VS Code 找不到法典



验证二进制文件是否存在并且位于 WSL 内的 `PATH` 上：

```bash
which codex || echo "codex not found"
```

如果未找到二进制文件，请按照 [Codex CLI 设置说明 ](<#将-codex-cli-与-wsl-一起使用>) 进行操作。


## 原文对照

<details>
<summary>展开完整原文</summary>

# WSL

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

When you use WSL2, Codex runs inside the Linux environment instead of using the
native [Windows sandbox](<https://learn.chatgpt.com/docs/windows/windows-sandbox>). Choose WSL2 when you need Linux-native
tooling, your repositories and developer workflow already live in WSL2, or
the available Windows sandbox implementations don't meet your needs.

WSL1 was supported through Codex `0.114`. Starting in Codex `0.115`, the Linux
sandbox moved to `bubblewrap`, so WSL1 is no longer supported.

## Launch VS Code from inside WSL

For step-by-step instructions, see the [official VS Code WSL tutorial](<https://code.visualstudio.com/docs/remote/wsl-tutorial>).

### Prerequisites

- Windows with WSL installed. To install WSL, open PowerShell as an administrator, then run `wsl --install` (Ubuntu is a common choice).
- VS Code with the [WSL extension](<https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl>) installed.

### Open VS Code from a WSL terminal

```bash
# From your WSL shell
cd ~/code/your-project
code .
```

This opens a WSL remote window, installs the VS Code Server if needed, and ensures integrated terminals run in Linux.

### Confirm you're connected to WSL

- Look for the green status bar that shows `WSL: <distro>`.
- Integrated terminals should display Linux paths (such as `/home/...`) instead of `C:\`.
- You can verify with:

```bash
  echo $WSL_DISTRO_NAME
```

  This prints your distribution name.

If you don't see "WSL: …" in the status bar, press `Ctrl+Shift+P`, pick `WSL:
  Reopen Folder in WSL`, and keep your repository under `/home/...` (not `C:\`)
  for best performance.

If the Windows app or project picker does not show your WSL repository, type
  `\\wsl$` into the file picker or Explorer, then navigate to your
  distribution's home directory.

## Use Codex CLI with WSL

Run these commands from an elevated PowerShell or Windows Terminal:

```powershell
# Install default Linux distribution (like Ubuntu)
wsl --install

# Start a shell inside Windows Subsystem for Linux
wsl
```

Then run these commands from your WSL shell:

```bash
# Install and run Codex in WSL
curl -fsSL https://chatgpt.com/codex/install.sh | sh
codex
```

## Work on code inside WSL

- Working in Windows-mounted paths like `/mnt/c/...` can be slower than working in Windows-native paths. Keep your repositories under your Linux home directory (like `~/code/my-app`) for faster I/O and fewer symlink and permission issues:
```bash
  mkdir -p ~/code && cd ~/code
  git clone https://github.com/your/repo.git
  cd repo
```
- If you need Windows access to files, they're under `\\wsl$\Ubuntu\home\&lt;user&gt;` in Explorer.

## Troubleshooting and FAQ



### Large repositories feel slow in WSL



- Make sure you're not working under `/mnt/c`. Move the repository to WSL (for example, `~/code/...`).
- Increase memory and CPU for WSL if needed; update WSL to the latest version:
```powershell
  wsl --update
  wsl --shutdown
```







### VS Code in WSL cannot find codex



Verify the binary exists and is on `PATH` inside WSL:

```bash
which codex || echo "codex not found"
```

If the binary isn't found, follow the [Codex CLI setup instructions](<#use-codex-cli-with-wsl>).


</details>
