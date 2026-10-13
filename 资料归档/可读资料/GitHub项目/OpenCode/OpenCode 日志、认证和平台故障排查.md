# OpenCode 日志、认证和平台故障排查

排查指南。介绍日志与存储、插件和配置诊断、服务连接、Windows/Linux、认证、模型、缓存和剪贴板问题；部分重置步骤会清理本地状态。

- 来源：OpenCode
- 资料类型：排查指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-06-09](<https://github.com/anomalyco/opencode/commit/671d1937867fc85c0d300a47faba371b5eab5e54>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：按具体故障选择步骤，涉及清理状态的操作需要理解影响。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/troubleshooting.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 日志、认证和平台故障排查.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Troubleshooting
description: Common issues and how to resolve them.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

要调试 OpenCode 的问题，请首先检查其存储在磁盘上的日志和本地数据。

---

## 日志

日志文件写入：

- **macOS/Linux**: `~/.local/share/opencode/log/`
- **Windows**：按`WIN+R`并粘贴`%USERPROFILE%\.local\share\opencode\log`

日志文件以时间戳命名（例如，`2025-01-09T123456.log`），并保留最新的10日志文件。

您可以使用`--log-level`命令行选项设置日志级别以获取更详细的调试信息。例如，`opencode --log-level DEBUG`。

---

## 存储

opencode 将会话数据和其他应用程序数据存储在磁盘上：

- **macOS/Linux**: `~/.local/share/opencode/`
- **Windows**：按`WIN+R`并粘贴`%USERPROFILE%\.local\share\opencode`

该目录包含：

- `auth.json` - 身份验证数据，例如 API 密钥、OAuth 令牌
- `log/` - 应用程序日志
- `project/` - 项目特定数据，例如会话和消息数据
  - 如果项目位于 Git 存储库中，则它存储在 `./<project-slug>/storage/` 中
  - 如果不是 Git 存储库，则存储在 `./global/storage/` 中

---

## 卸载

要卸载 OpenCode CLI 并删除其相关文件，请运行：

```bash
opencode uninstall
```

该命令显示将删除的内容并要求确认。请参阅 [CLI 参考资料 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#uninstall>)，了解保留配置或应用程序数据的选项。

要删除 OpenCode Desktop，请通过操作系统的应用程序管理工具卸载该应用程序。

---

## 桌面应用程序

OpenCode Desktop 在后台运行本地 OpenCode 服务器（`opencode-cli` sidecar）。大多数问题是由行为不当的插件、损坏的缓存或错误的服务器设置引起的。

### 快速检查

- 完全退出并重新启动应用程序。
- 如果应用程序显示错误屏幕，请单击**重新启动**并复制错误详细信息。
- 仅 macOS：`OpenCode` 菜单 -> **重新加载 Webview**（如果 UI 为 blank/frozen，则有帮助）。

---

### 禁用插件

如果桌面应用程序在启动时崩溃、挂起或行为异常，请首先禁用插件。

#### 检查全局配置

打开全局配置文件并查找 `plugin` 密钥。

- **macOS/Linux**：`~/.config/opencode/opencode.jsonc`（或`~/.config/opencode/opencode.json`）
- **macOS/Linux**（旧版安装）：`~/.local/share/opencode/opencode.jsonc`
- **Windows**：按`WIN+R`并粘贴`%USERPROFILE%\.config\opencode\opencode.jsonc`

如果您配置了插件，请通过删除密钥或将其设置为空数组来暂时禁用它们：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [],
}
```

#### 检查插件目录

OpenCode还可以从磁盘加载本地插件。暂时将它们移开（或重命名文件夹）并重新启动桌面应用程序：

- **全局插件**
  - **macOS/Linux**: `~/.config/opencode/plugins/`
  - **Windows**：按`WIN+R`并粘贴`%USERPROFILE%\.config\opencode\plugins`
- **项目插件**（仅当您使用每个项目配置时）
  - `<your-project>/.opencode/plugins/`

如果应用程序再次开始工作，请一次重新启用一个插件，以找出导致问题的插件。

---

### 清除缓存

如果禁用插件没有帮助（或者插件安装被卡住），请清除缓存，以便 OpenCode 可以重建它。

1. 完全退出OpenCode桌面。
2. 删除缓存目录：

- **macOS**：查找器 -> `Cmd+Shift+G` -> 粘贴 `~/.cache/opencode`
- **Linux**：删除`~/.cache/opencode`（或运行`rm -rf ~/.cache/opencode`）
- **Windows**：按`WIN+R`并粘贴`%USERPROFILE%\.cache\opencode`

3. 重新启动OpenCode桌面。

---

### 修复服务器连接问题

OpenCode Desktop 可以启动自己的本地服务器（默认）或连接到您配置的服务器 URL。

如果您看到 **“连接失败”** 对话框（或者应用程序永远无法通过启动屏幕），请检查自定义服务器 URL。

#### 清除桌面默认服务器URL

在主屏幕中，单击服务器名称（带有状态点）以打开服务器选择器。在**默认服务器**部分中，单击**清除**。

#### 从配置中删除 `server.port` / `server.hostname`

如果您的 `opencode.json(c)` 包含 `server` 部分，请将其暂时删除并重新启动桌面应用程序。

#### 检查环境变量

如果您在环境中设置了 `OPENCODE_PORT`，桌面应用程序将尝试为本地服务器使用该端口。

- 取消设置 `OPENCODE_PORT`（或选择一个空闲端口）并重新启动。

---

### Linux：Wayland / X11 问题

在 Linux 上，某些 Wayland 设置可能会导致空白窗口或合成器错误。

- 如果您在 Wayland 并且应用程序是 blank/crashing，请尝试使用 `OC_ALLOW_WAYLAND=1` 启动。
- 如果这让事情变得更糟，请将其删除并尝试在 X11 会话下启动。

---

### Windows：WebView2运行时

在 Windows 上，OpenCode 桌面需要 Microsoft Edge **WebView2 运行时 **。如果应用程序打开为空白窗口或无法启动，请install/update WebView2，然后重试。

---

### Windows：一般性能问题

如果您在 Windows 上遇到性能缓慢、文件访问问题或终端问题，请尝试使用 [WSL（适用于 Linux 的 Windows 子系统）](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>)。 WSL 提供了一个与 OpenCode 的功能更加无缝协作的 Linux 环境。

---

### 通知不显示

OpenCode 桌面仅在以下情况下显示系统通知：

- 在您的 OS 设置中为 OpenCode 启用通知，并且
- 应用程序窗口未聚焦。

---

### 重置桌面应用程序存储（最后的手段）

如果应用程序无法启动并且您无法从 UI 内部清除设置，请重置桌面应用程序的保存状态。

1. 退出 OpenCode 桌面。
2. 查找并删除这些文件（它们位于 OpenCode 桌面应用程序数据目录中）：

- `opencode.settings.dat`（桌面默认服务器URL）
- `opencode.global.dat` 和 `opencode.workspace.*.dat`（UI 状态类似于最近的 servers/projects）

快速找到目录：

- **macOS**：Finder -> `Cmd+Shift+G` -> `~/Library/Application Support`（然后搜索上面的文件名）
- **Linux**：在`~/.local/share`下搜索上述文件名
- **Windows**：按`WIN+R` -> `%APPDATA%`（然后搜索上面的文件名）

---

## 寻求帮助

如果您遇到 OpenCode 问题：

1. **报告 GitHub** 上的问题

报告错误或请求功能的最佳方式是通过我们的 GitHub 存储库：

   [**github.com/anomalyco/opencode/issues**](<https://github.com/anomalyco/opencode/issues>)

在创建新问题之前，请搜索现有问题以查看您的问题是否已被报告。

2. **加入我们的 Discord**

如需实时帮助和社区讨论，请加入我们的 Discord 服务器：

   [**opencode.ai/discord**](<https://opencode.ai/discord>)

---

## 常见问题

以下是一些常见问题以及解决方法。

---

### OpenCode 无法启动

1. 检查日志中是否有错误消息
2. 尝试使用 `--print-logs` 运行以查看终端中的输出
3. 确保您拥有最新版本 `opencode upgrade`

---

### 身份验证问题

1. 尝试使用 TUI 中的 `/connect` 命令重新进行身份验证
2. 检查您的 API 密钥是否有效
3. 确保您的网络允许连接到提供商的 API

---

### 型号不可用

1. 检查您是否已向提供商进行身份验证
2. 验证配置中的型号名称是否正确
3. 某些型号可能需要特定的访问权限或订阅

如果您遇到`ProviderModelNotFoundError`，您很可能错误地
在某处引用了模型。
模型应该像这样引用：`<providerId>/<modelId>`

示例：

- `openai/gpt-4.1`
- `openrouter/google/gemini-2.5-flash`
- `opencode/kimi-k2`

要了解您可以访问哪些模型，请运行 `opencode models`

---

### ProviderInitError

如果您遇到 ProviderInitError，则您的配置可能无效或损坏。

要解决这个问题：

1. 首先，按照 [ 提供商指南 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md>) 验证您的提供商是否已正确设置
2. 如果问题仍然存在，请尝试清除存储的配置：

```bash
rm -rf ~/.local/share/opencode
```

在 Windows 上，按 `WIN+R` 并删除：`%USERPROFILE%\.local\share\opencode`

3. 使用 TUI 中的 `/connect` 命令向您的提供商重新进行身份验证。

---

### AI_APICallError 和提供程序包问题

如果您遇到 API 调用错误，这可能是由于过时的提供程序包造成的。 opencode 根据需要动态安装提供程序包（OpenAI、Anthropic、Google 等）并在本地缓存。

要解决提供程序包问题：

1. 清除提供程序包缓存：

```bash
rm -rf ~/.cache/opencode
```

在 Windows 上，按 `WIN+R` 并删除：`%USERPROFILE%\.cache\opencode`

2. 重新启动opencode以重新安装最新的提供程序包

这将强制 opencode 下载最新版本的提供程序包，这通常可以解决模型参数和 API 更改的兼容性问题。

---

### Copy/paste 无法在 Linux 上运行

Linux 用户需要安装以下剪贴板实用程序之一才能使 copy/paste 功能正常工作：

**对于X11系统：**

```bash
apt install -y xclip
# or
apt install -y xsel
```

**对于 Wayland 系统：**

```bash
apt install -y wl-clipboard
```

**对于无头环境：**

```bash
apt install -y xvfb
# and run:
Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
export DISPLAY=:99.0
```

opencode 将检测您是否正在使用 Wayland 并且更喜欢 `wl-clipboard`，否则它将尝试按以下顺序查找剪贴板工具：`xclip` 和 `xsel`。


## 原文对照

<details>
<summary>展开完整原文</summary>

To debug issues with OpenCode, start by checking the logs and local data it stores on disk.

---

## Logs

Log files are written to:

- **macOS/Linux**: `~/.local/share/opencode/log/`
- **Windows**: Press `WIN+R` and paste `%USERPROFILE%\.local\share\opencode\log`

Log files are named with timestamps (e.g., `2025-01-09T123456.log`) and the most recent 10 log files are kept.

You can set the log level with the `--log-level` command-line option to get more detailed debug information. For example, `opencode --log-level DEBUG`.

---

## Storage

opencode stores session data and other application data on disk at:

- **macOS/Linux**: `~/.local/share/opencode/`
- **Windows**: Press `WIN+R` and paste `%USERPROFILE%\.local\share\opencode`

This directory contains:

- `auth.json` - Authentication data like API keys, OAuth tokens
- `log/` - Application logs
- `project/` - Project-specific data like session and message data
  - If the project is within a Git repo, it is stored in `./<project-slug>/storage/`
  - If it is not a Git repo, it is stored in `./global/storage/`

---

## Uninstall

To uninstall the OpenCode CLI and remove its related files, run:

```bash
opencode uninstall
```

The command shows what will be removed and asks for confirmation. See the [CLI reference](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode CLI 命令、参数与环境变量.md#uninstall>) for options to keep your configuration or application data.

To remove OpenCode Desktop, uninstall the application through your operating system's app management tools.

---

## Desktop app

OpenCode Desktop runs a local OpenCode server (the `opencode-cli` sidecar) in the background. Most issues are caused by a misbehaving plugin, a corrupted cache, or a bad server setting.

### Quick checks

- Fully quit and relaunch the app.
- If the app shows an error screen, click **Restart** and copy the error details.
- macOS only: `OpenCode` menu -> **Reload Webview** (helps if the UI is blank/frozen).

---

### Disable plugins

If the desktop app is crashing on launch, hanging, or behaving strangely, start by disabling plugins.

#### Check the global config

Open your global config file and look for a `plugin` key.

- **macOS/Linux**: `~/.config/opencode/opencode.jsonc` (or `~/.config/opencode/opencode.json`)
- **macOS/Linux** (older installs): `~/.local/share/opencode/opencode.jsonc`
- **Windows**: Press `WIN+R` and paste `%USERPROFILE%\.config\opencode\opencode.jsonc`

If you have plugins configured, temporarily disable them by removing the key or setting it to an empty array:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": [],
}
```

#### Check plugin directories

OpenCode can also load local plugins from disk. Temporarily move these out of the way (or rename the folder) and restart the desktop app:

- **Global plugins**
  - **macOS/Linux**: `~/.config/opencode/plugins/`
  - **Windows**: Press `WIN+R` and paste `%USERPROFILE%\.config\opencode\plugins`
- **Project plugins** (only if you use per-project config)
  - `<your-project>/.opencode/plugins/`

If the app starts working again, re-enable plugins one at a time to find which one is causing the issue.

---

### Clear the cache

If disabling plugins doesn't help (or a plugin install is stuck), clear the cache so OpenCode can rebuild it.

1. Quit OpenCode Desktop completely.
2. Delete the cache directory:

- **macOS**: Finder -> `Cmd+Shift+G` -> paste `~/.cache/opencode`
- **Linux**: delete `~/.cache/opencode` (or run `rm -rf ~/.cache/opencode`)
- **Windows**: Press `WIN+R` and paste `%USERPROFILE%\.cache\opencode`

3. Restart OpenCode Desktop.

---

### Fix server connection issues

OpenCode Desktop can either start its own local server (default) or connect to a server URL you configured.

If you see a **"Connection Failed"** dialog (or the app never gets past the splash screen), check for a custom server URL.

#### Clear the desktop default server URL

From the Home screen, click the server name (with the status dot) to open the Server picker. In the **Default server** section, click **Clear**.

#### Remove `server.port` / `server.hostname` from your config

If your `opencode.json(c)` contains a `server` section, temporarily remove it and restart the desktop app.

#### Check environment variables

If you have `OPENCODE_PORT` set in your environment, the desktop app will try to use that port for the local server.

- Unset `OPENCODE_PORT` (or pick a free port) and restart.

---

### Linux: Wayland / X11 issues

On Linux, some Wayland setups can cause blank windows or compositor errors.

- If you're on Wayland and the app is blank/crashing, try launching with `OC_ALLOW_WAYLAND=1`.
- If that makes things worse, remove it and try launching under an X11 session instead.

---

### Windows: WebView2 runtime

On Windows, OpenCode Desktop requires the Microsoft Edge **WebView2 Runtime**. If the app opens to a blank window or won't start, install/update WebView2 and try again.

---

### Windows: General performance issues

If you're experiencing slow performance, file access issues, or terminal problems on Windows, try using [WSL (Windows Subsystem for Linux)](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>). WSL provides a Linux environment that works more seamlessly with OpenCode's features.

---

### Notifications not showing

OpenCode Desktop only shows system notifications when:

- notifications are enabled for OpenCode in your OS settings, and
- the app window is not focused.

---

### Reset desktop app storage (last resort)

If the app won't start and you can't clear settings from inside the UI, reset the desktop app's saved state.

1. Quit OpenCode Desktop.
2. Find and delete these files (they live in the OpenCode Desktop app data directory):

- `opencode.settings.dat` (desktop default server URL)
- `opencode.global.dat` and `opencode.workspace.*.dat` (UI state like recent servers/projects)

To find the directory quickly:

- **macOS**: Finder -> `Cmd+Shift+G` -> `~/Library/Application Support` (then search for the filenames above)
- **Linux**: search under `~/.local/share` for the filenames above
- **Windows**: Press `WIN+R` -> `%APPDATA%` (then search for the filenames above)

---

## Getting help

If you're experiencing issues with OpenCode:

1. **Report issues on GitHub**

   The best way to report bugs or request features is through our GitHub repository:

   [**github.com/anomalyco/opencode/issues**](<https://github.com/anomalyco/opencode/issues>)

   Before creating a new issue, search existing issues to see if your problem has already been reported.

2. **Join our Discord**

   For real-time help and community discussion, join our Discord server:

   [**opencode.ai/discord**](<https://opencode.ai/discord>)

---

## Common issues

Here are some common issues and how to resolve them.

---

### OpenCode won't start

1. Check the logs for error messages
2. Try running with `--print-logs` to see output in the terminal
3. Ensure you have the latest version with `opencode upgrade`

---

### Authentication issues

1. Try re-authenticating with the `/connect` command in the TUI
2. Check that your API keys are valid
3. Ensure your network allows connections to the provider's API

---

### Model not available

1. Check that you've authenticated with the provider
2. Verify the model name in your config is correct
3. Some models may require specific access or subscriptions

If you encounter `ProviderModelNotFoundError` you are most likely incorrectly
referencing a model somewhere.
Models should be referenced like so: `<providerId>/<modelId>`

Examples:

- `openai/gpt-4.1`
- `openrouter/google/gemini-2.5-flash`
- `opencode/kimi-k2`

To figure out what models you have access to, run `opencode models`

---

### ProviderInitError

If you encounter a ProviderInitError, you likely have an invalid or corrupted configuration.

To resolve this:

1. First, verify your provider is set up correctly by following the [providers guide](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md>)
2. If the issue persists, try clearing your stored configuration:

```bash
rm -rf ~/.local/share/opencode
```

   On Windows, press `WIN+R` and delete: `%USERPROFILE%\.local\share\opencode`

3. Re-authenticate with your provider using the `/connect` command in the TUI.

---

### AI_APICallError and provider package issues

If you encounter API call errors, this may be due to outdated provider packages. opencode dynamically installs provider packages (OpenAI, Anthropic, Google, etc.) as needed and caches them locally.

To resolve provider package issues:

1. Clear the provider package cache:

```bash
rm -rf ~/.cache/opencode
```

   On Windows, press `WIN+R` and delete: `%USERPROFILE%\.cache\opencode`

2. Restart opencode to reinstall the latest provider packages

This will force opencode to download the most recent versions of provider packages, which often resolves compatibility issues with model parameters and API changes.

---

### Copy/paste not working on Linux

Linux users need to have one of the following clipboard utilities installed for copy/paste functionality to work:

**For X11 systems:**

```bash
apt install -y xclip
# or
apt install -y xsel
```

**For Wayland systems:**

```bash
apt install -y wl-clipboard
```

**For headless environments:**

```bash
apt install -y xvfb
# and run:
Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
export DISPLAY=:99.0
```

opencode will detect if you're using Wayland and prefer `wl-clipboard`, otherwise it will try to find clipboard tools in order of: `xclip` and `xsel`.


</details>
