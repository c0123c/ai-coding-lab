# Qwen Code 桌面操作技能

说明额外 MCP 和驱动的安装、权限、远端会话与排查，用于让 Agent 操作桌面应用。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-28](<https://github.com/QwenLM/qwen-code/commit/2cfd3906507b991fbbf4777b9390c437a859cb56>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/computer-use.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/Qwen Code 桌面操作技能.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 电脑使用

Qwen Code 包含一项 `computer-use` 技能，可教授模型如何通过两个单独安装的软件包
操作桌面应用程序：

```text
bundled computer-use skill
  -> @qwen-code/node-repl-mcp
  -> @qwen-code/cua-sdk/computer-use
  -> native cua-driver accessibility backend
```

Qwen Code 不捆绑 MCP 服务器、SDK 或本机驱动程序。技能
会在外部包丢失时自动安装。

> [！警告]
>
> 计算机使用可以读取应用程序UI并控制鼠标和键盘输入。
> 仅在受信任的环境中使用它并仔细审查MCP批准。

## 自动设置

需要 Node.js 22 或更高版本以及 npm。

首次使用时，该技能会自行运行这些命令：

```bash
qwen mcp add --scope user node-repl npx -y @qwen-code/node-repl-mcp@0.1.7
npm install --no-save --package-lock=false @qwen-code/cua-sdk@0.20.11
```

首次添加MCP服务器后，重新启动Qwen Code。然后技能通过`node_repl`恢复
桌面任务。

SDK 安装保持 `package.json` 和锁定文件不变，但它 
 确实写入工作区的 `node_modules`。它的安装后下载并
验证当前平台的本机有效负载。

删除MCP配置或工作区SDK安装会禁用
执行路径；没有遗留后备方案。

## 使用

要求 Qwen Code 使用 `$computer-use` 执行桌面任务。引导后，它
使用macOS上的应用程序工作流程：

1. 将应用程序与 `computer.getApp(nameOrIdentifierOrPath)` 绑定；
2. 读取 `app.getState()` 以获得紧凑的辅助功能文本，后跟自动
增量更新；
3. 使用该文本中的短元素 ID 执行一项或多项操作；
4. 在决定下一步做什么之前获取最新状态；和
5. 仅当没有其他持久性时，关闭SDK客户端并重置REPL
需要状态。

驱动程序是计算观察差异的唯一组件。模型代码
使用类型化的SDK方法，并且不调度任意驱动程序工具名称。
应用程序句柄跟踪当前窗口和对话框，在内部保留本机元素
身份，并将输入委托给本机驱动程序。型号代码
不选择foreground/background模式。未确认的操作不会重播。
`getState()`可以打开发现的已停止的应用程序；操作永远不会重新启动它。
现有的精确窗口 API 在 Windows 和 Linux 上仍然可用。

```js
const app = await computer.getApp('Microsoft Excel');
nodeRepl.write((await app.getState()).text);
// Use an element ID from the returned state.
await app.click(37);
await app.typeText('hello');
nodeRepl.write((await app.getState()).text);
```

打开或关闭对话框后刷新状态，然后重用元素 ID。
每次应用程序状态刷新都会在内部捕获当前屏幕截图。默认的
返回将其隐藏；当模型需要图像时，使用 
`app.getState({ includeScreenshot: true })` 显式请求它。

## 权限

节点 REPL 是一个 MCP 服务器，它使用 
 普通 Node.js 权限执行模型编写的 JavaScript。其调用遵循Qwen Code的正常
[MCP批准流程](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode.md>)。 SDK 还强制执行本机 
 授权。

在 macOS 上，辅助功能观察和输入需要辅助功能权限。
屏幕截图还需要屏幕录制权限。 macOS可以
将授权归因于启动Qwen Code的终端或IDE。 Windows 和
Linux 使用其平台可访问性和输入设施。

## 从远程会话中使用您面前的计算机

当 Qwen Code 在无头计算机（开发盒、服务器）上运行时，该技能仍然可以 
 驱动您所在的桌面：该计算机将其自己的 
`node_repl` 借给一个远程会话。今天它在 macOS 上运行。

在您的计算机上设置一次：

```bash
npx -y @qwen-code/node-repl-mcp@0.1.7 desktop-relay install
```

这将在 `~/.qwen/desktop-relay` 下安装 `node_repl` 和 SDK，并在 `127.0.0.1:47821` 上注册 
 启动套接字。后台没有任何运行； launchd
仅在连接时启动一个短暂的进程。

- **从 Web Shell。** 远程守护程序必须运行
`QWEN_SERVE_CLIENT_MCP_OVER_WS=1`，并且 Web Shell 必须是安全页面 
（https 或通过 SSH 隧道的 `http://localhost`）。在会话中，在侧边栏页脚中选择
 **使用此计算机**，然后选择**连接此计算机**。

计算机上的对话框要求您允许每个连接。允许的会话
可以在您的权限下在您的计算机上运行代码，并查看和控制其
屏幕，就像本地计算机使用一样，直到您断开连接或它结束。 macOS
首次要求在“辅助功能”和“屏幕录制”下允许`node`。

## 故障排除

- 如果自动设置后`node_repl`仍然不可用，请重新启动Qwen Code
并使用`qwen mcp list`验证服务器。
- 如果自动设置后SDK导入仍然失败，请确认Qwen Code已导入
从安装包的工作区运行。
- 超时、取消、重置或内核崩溃后，引导 SDK
再次客户端并请求新鲜状态。

## 另请参阅

- [技能](<在 Qwen Code 中创建与管理 Skills.md>)
- [MCP 服务器](<通过 MCP 为 Qwen Code 接入工具.md>)
- [审批方式](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode.md>)
- [沙箱](<Qwen Code 沙箱配置.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Computer Use

Qwen Code includes a `computer-use` skill that teaches the model how to
operate desktop applications through two separately installed packages:

```text
bundled computer-use skill
  -> @qwen-code/node-repl-mcp
  -> @qwen-code/cua-sdk/computer-use
  -> native cua-driver accessibility backend
```

Qwen Code does not bundle the MCP server, SDK, or native driver. The skill
installs the external packages automatically when they are missing.

> [!warning]
>
> Computer Use can read application UI and control mouse and keyboard input.
> Use it only in trusted environments and review MCP approvals carefully.

## Automatic setup

Node.js 22 or later and npm are required.

When first used, the skill runs these commands itself:

```bash
qwen mcp add --scope user node-repl npx -y @qwen-code/node-repl-mcp@0.1.7
npm install --no-save --package-lock=false @qwen-code/cua-sdk@0.20.11
```

Restart Qwen Code after the MCP server is first added. The skill then resumes
the desktop task through `node_repl`.

The SDK installation leaves `package.json` and the lockfile unchanged, but it
does write to the workspace's `node_modules`. Its postinstall downloads and
verifies the native payload for the current platform.

Removing the MCP configuration or workspace SDK installation disables the
execution path; there is no legacy fallback.

## Use

Ask Qwen Code to use `$computer-use` for the desktop task. After bootstrap, it
uses the app workflow on macOS:

1. binds the application with `computer.getApp(nameOrIdentifierOrPath)`;
2. reads `app.getState()` for compact accessibility text, followed by automatic
   incremental updates;
3. performs one or more actions using the short element IDs in that text;
4. fetches the latest state before deciding what to do next; and
5. closes the SDK client and resets the REPL only when no other persistent
   state is needed.

The driver is the only component that computes observation diffs. Model code
uses the typed SDK methods and does not dispatch arbitrary driver tool names.
The app handle tracks the current window and dialogs, keeps native element
identity internally, and delegates input to the native driver. Model code does
not choose foreground/background modes. Unconfirmed actions are not replayed.
`getState()` can open a discovered stopped app; actions never restart it.
Existing exact-window APIs remain available on Windows and Linux.

```js
const app = await computer.getApp('Microsoft Excel');
nodeRepl.write((await app.getState()).text);
// Use an element ID from the returned state.
await app.click(37);
await app.typeText('hello');
nodeRepl.write((await app.getState()).text);
```

Refresh state after opening or closing a dialog before reusing element IDs.
Each App state refresh captures the current screenshot internally. The default
return keeps it hidden; request it explicitly with
`app.getState({ includeScreenshot: true })` when the model needs the image.

## Permissions

The Node REPL is an MCP server that executes model-authored JavaScript with
ordinary Node.js authority. Its calls follow Qwen Code's normal
[MCP approval flow](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode.md>). The SDK also enforces native
authorization.

On macOS, accessibility observation and input require Accessibility permission.
Screenshots additionally require Screen Recording permission. macOS may
attribute the grant to the terminal or IDE that launched Qwen Code. Windows and
Linux use their platform accessibility and input facilities.

## Use the computer in front of you from a remote session

When Qwen Code runs on a headless machine (a dev box, a server), the skill can
still drive the desktop you are sitting at: that computer lends its own
`node_repl` to one remote session. It works on macOS today.

Set it up once on your computer:

```bash
npx -y @qwen-code/node-repl-mcp@0.1.7 desktop-relay install
```

This installs `node_repl` and the SDK under `~/.qwen/desktop-relay` and registers
a launchd socket on `127.0.0.1:47821`. Nothing runs in the background; launchd
starts a short-lived process only when something connects.

- **From the Web Shell.** The remote daemon must run with
  `QWEN_SERVE_CLIENT_MCP_OVER_WS=1`, and the Web Shell must be a secure page
  (https, or `http://localhost` through an SSH tunnel). In a session, choose
  **Use this computer** in the sidebar footer, then **Connect this computer**.

A dialog on your computer asks you to allow every connection. An allowed session
can run code on your computer with your permissions and see and control its
screen, just like local Computer Use, until you disconnect it or it ends. macOS
asks to allow `node` under Accessibility and Screen Recording the first time.

## Troubleshooting

- If `node_repl` is still unavailable after automatic setup, restart Qwen Code
  and verify the server with `qwen mcp list`.
- If the SDK import still fails after automatic setup, confirm Qwen Code is
  running from the workspace where the package was installed.
- After a timeout, cancellation, reset, or kernel crash, bootstrap the SDK
  client again and request fresh state.

## See also

- [Skills](<在 Qwen Code 中创建与管理 Skills.md>)
- [MCP servers](<通过 MCP 为 Qwen Code 接入工具.md>)
- [Approval Mode](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/approval-mode.md>)
- [Sandboxing](<Qwen Code 沙箱配置.md>)


</details>
