# 让 CLI 操作电脑界面

说明在支持的平台启用电脑操作，让 Claude 查看屏幕、点击和输入，以测试本地应用及操作仅有图形界面的工具；涵盖授权和会话限制。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-03](<https://code.claude.com/docs/en/computer-use>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/computer-use.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/让 CLI 操作电脑界面.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 让Claude从CLI使用您的计算机

> 启用 Claude Code CLI 中的计算机使用，以便 Claude 可以打开应用程序、单击、键入并查看 macOS 上的屏幕。无需离开终端即可测试本机应用程序、调试视觉问题并自动化 GUI- 专用工具。



**注释：**

计算机使用是 macOS 的研究预览，需要 Pro 或 Max 计划。它不适用于团队或企业计划。它需要交互式会话，因此在带有 `-p` 标志的非交互模式下不可用。


使用计算机可以让 Claude 打开应用程序、控制屏幕并以您喜欢的方式在计算机上工作。从 CLI 中，Claude 可以编译 Swift 应用程序，启动它，单击每个按钮，并对结果进行屏幕截图，所有这些都在编写代码的同一个对话中进行。

本页介绍了 CLI 中计算机的使用方式。对于 macOS 或 Windows 上的桌面应用程序，请参阅 [桌面中的计算机使用 ](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>)。

## 使用电脑可以做什么

使用计算机处理需要 GUI 的任务：通常需要离开终端并手动完成的任何任务。

* **构建并验证本机应用程序**：要求Claude构建macOS菜单栏应用程序。 Claude 编写 Swift、编译它、启动它，并在打开它之前单击每个控件以验证它是否正常工作。
* **端到端UI测试**：将Claude指向本地Electron应用程序并说“测试入门流程”。 Claude 打开应用程序，点击注册，并对每个步骤进行屏幕截图。没有 Playwright 配置，没有测试工具。
* **调试视觉和布局问题**：告诉Claude“模态在小窗口上被剪裁。” Claude 调整窗口大小，重现错误，截屏，修补 CSS，并验证修复情况。 Claude所见即所得。
* **仅驱动GUI-工具**：与设计工具、硬件控制面板、iOS模拟器或没有CLI或API的专有应用程序交互。

## 当使用计算机时

Claude 有多种与应用程序或服务交互的方式。计算机的使用最广泛，速度也最慢，因此Claude首先尝试最精确的工具：

* 如果您有用于该服务的 [MCP 服务器 ](<https://code.claude.com/docs/en/mcp>)，Claude 将使用该服务器。
* 如果任务是 shell 命令，Claude 使用 Bash。
* 如果任务是浏览器工作并且您有 [Claude 在 Chrome 中](<https://code.claude.com/docs/en/chrome>) 设立, Claude 使用那个。
* 如果这些都不适用，Claude 使用计算机。

屏幕控制保留给其他任何东西都无法到达的东西：本机应用程序、模拟器（例如 iOS 模拟器）以及没有 API 的工具。

## 启用计算机使用

计算机可作为名为 `computer-use` 的内置 MCP 服务器使用。它默认处于关闭状态，直到您启用它。



  

#### 打开MCP菜单

在交互式 Claude Code 会话中，运行：

    ```text theme={null}
    /mcp
    ```

在服务器列表中找到`computer-use`。它显示为已禁用。
  


  

#### 启用服务器

选择`computer-use`并选择**启用**。每个项目都会保留该设置，因此您只需为要使用计算机的每个项目执行一次此操作。
  


  

#### 授予macOS权限

第一次 Claude 尝试使用您的计算机时，您会看到授予两个 macOS 权限的提示：

    * **辅助功能**：让Claude单击、键入和滚动
    * **屏幕录制**：让Claude查看屏幕上的内容

该提示包含用于打开相关系统设置窗格的链接。授予两者，然后在提示中选择 ** 重试 **。 macOS 可能会要求您在授予屏幕录制权限后重新启动 Claude Code。
  


设置完成后，让Claude做一些需要GUI的事情：

```text theme={null}
Build the app target, launch it, and click through each tab to make
sure nothing crashes. Screenshot any error states you find.
```

## 每个会话批准应用程序

启用 `computer-use` 服务器不会授予 Claude 对计算机上每个应用程序的访问权限。第一次 Claude 在会话中需要特定应用程序时，终端中会出现一条提示，显示：

* Claude 想要控制哪些应用程序
* 请求的任何额外权限，例如剪贴板访问
* Claude 工作期间将隐藏多少其他应用程序

选择**允许此会话**或**拒绝**。批准持续到当前会议。当 Claude 一起请求多个应用程序时，您可以一次批准多个应用程序。

具有广泛覆盖范围的应用程序会在提示中显示额外警告，以便您知道批准它们会授予什么：

|警告|适用于|
| :- | :- |
|相当于shell访问|终端、iTerm、VS Code、Warp 以及其他终端和 IDE|
|可以读取或写入任何文件|查找器|
|可以更改系统设置|系统设置|

这些应用程序未被阻止。该警告可让您决定该任务是否需要该级别的访问权限。

批准 Finder 让 Claude 单击桌面、Dock 或 Finder 窗口。

Claude的控制级别也因应用程序类别而异：浏览器和交易平台只能查看，终端和 IDE 只能点击，其他一切都可以完全控制。参见 [桌面中的应用程序权限](<https://code.claude.com/docs/en/desktop#app-permissions>) 以获得完整的层级细分。

## Claude 如何在您的屏幕上工作

了解流程有助于您预测 Claude 将做什么以及如何干预。

### 一次一个会话

一次只能有一个会话可以使用您的计算机。会话在其第一次计算机使用操作时获取锁定，并在会话退出时（而不是任务完成时）释放它。第二个会话的计算机使用失败，并出现命名持有锁的会话错误。首先退出该会话。

### Claude 工作时应用程序被隐藏

当 Claude 开始控制您的屏幕时，其他可见的应用程序将被隐藏，因此 Claude 仅与批准的应用程序交互。您的终端窗口保持可见，并且不包含在屏幕截图中，因此您可以观看会话，并且 Claude 永远看不到它自己的输出。

当Claude完成回合后，隐藏的应用程序将自动恢复。

### 屏幕截图会自动缩小

Claude Code 在将每个屏幕截图发送给模型之前缩小其尺寸。您无需在 Retina 或其他高分辨率显示器上降低显示分辨率或调整窗口大小。 16英寸MacBook Pro在原始视网膜分辨率下以3456×2234捕获并缩小到大约1372×887，保持宽高比。

没有可以更改目标大小的设置。如果屏幕上的文本或控件太小，Claude 在缩小尺寸后无法阅读，请在应用程序中增加其大小，而不是更改显示分辨率。

### 随时停止

Claude 每次第一次使用您的计算机时，都会出现 macOS 通知：“Claude 正在使用您的计算机·按 Esc 停止。”在任意位置按`Esc`可立即中止当前操作，或在终端中按`Ctrl+C`。无论哪种方式，Claude 都会停止，取消隐藏您的应用程序，并将控制权返回给您。该会话将保持 [ 计算机使用锁 ](<#一次一个会话>)，直到其退出。

Claude 完成后，会出现第二条通知。

## 安全与信任边界



**注意：**

与 [ 沙盒 Bash 工具 ](<https://code.claude.com/docs/en/sandboxing>) 不同，计算机使用在您的实际桌面上运行，可以访问您批准的应用程序。 Claude 检查每个操作并标记来自屏幕内容的潜在提示注入，但信任边界不同。请参阅 [计算机使用安全指南 ](<https://support.claude.com/en/articles/14128542>) 了解最佳实践。


内置护栏可降低风险，无需配置：

* **按应用批准**：Claude只能控制您在当前会话中批准的应用。
* **哨兵警告**：授予 shell、文件系统或系统设置访问权限的应用程序在您批准之前会被标记。
* **从屏幕截图中排除的终端**：Claude永远不会看到您的终端窗口，因此会话中的屏幕提示无法反馈到模型中。
* **全局转义**：`Esc`键可从任何地方中止计算机使用，并且按键会被消耗，因此提示注入无法使用它来关闭对话框。
* **锁定文件**：一次只有一个会话可以控制您的机器。

## 工作流程示例

这些示例展示了将计算机使用与编码任务结合起来的常见方法。

### 验证本机构建

对 macOS 或 iOS 应用程序进行更改后，让 Claude 一次性编译和验证：

```text theme={null}
Build the MenuBarStats target, launch it, open the preferences window,
and verify the interval slider updates the label. Screenshot the
preferences window when you're done.
```

Claude 运行 `xcodebuild`，启动应用程序，与 UI 交互，并报告它发现的内容。

### 重现布局错误

当视觉错误仅出现在某些窗口大小时，让 Claude 找到它：

```text theme={null}
The settings modal clips its footer on narrow windows. Resize the app
window down until you can reproduce it, screenshot the clipped state,
then check the CSS for the modal container.
```

Claude 调整窗口大小、捕获损坏状态并读取相关样式表。

### 测试模拟器流程

无需编写XCTest即可驱动iOS模拟器：

```text theme={null}
Open the iOS Simulator, launch the app, tap through the onboarding
screens, and tell me if any screen takes more than a second to load.
```

Claude 控制模拟器的方式与使用鼠标的方式相同。此流程适用于【CLI】；在桌面应用程序中，相同的请求将打开 [iOS 模拟器窗格 ](<https://code.claude.com/docs/en/desktop-ios-simulator>) 而不是屏幕控制。

## 与桌面应用程序的差异

CLI 和桌面表面共享相同的计算机使用引擎，但有一些差异：

|特点|桌面| CLI |
| :- | :- | :- |
|平台|macOS 和 Windows|仅macOS|
|启用|切换**设置>此计算机>系统**|启用`/mcp`中的`computer-use`|
|拒绝的应用程序列表|可在设置中配置|尚不可用|
|自动取消隐藏切换|可选|始终开启|
|调度一体化|调度生成的会话可以使用计算机使用|不适用|

## 故障排除

### “计算机正在被另一个 Claude 会话使用”

另一个 Claude Code 会话持有该锁，并一直保持到退出为止。退出该会话。如果其他会话崩溃，当 Claude 检测到进程不再运行时，锁会自动释放。

### macOS权限提示不断重复出现

macOS 有时需要在您授予屏幕录制权限后重新启动请求进程。完全退出 Claude Code 并开始新的会话。如果提示仍然存在，请打开 **系统设置 > 隐私和安全 > 屏幕录制 ** 并确认您的终端应用程序已列出并启用。

### `computer-use` 未出现在 `/mcp` 中

服务器仅出现在符合条件的设置中。检查：

* 您正在使用 macOS。 CLI 中的计算机在 Linux 或 Windows 上不可用。在 Windows 上，请改用 [ 桌面中的计算机使用 ](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>)。
* 您使用的是 Pro 或 Max 套餐。运行 `/status` 确认您的订阅。
* 您已通过 claude.ai 进行身份验证。 Amazon Bedrock、Google Cloud 的 Agent Platform 或 Microsoft Foundry 等第三方提供商无法使用计算机。如果您仅通过第三方提供商访问 Claude，则需要单独的 claude.ai 帐户才能使用此功能。
* 您正处于交互式会话中。计算机在带有 `-p` 标志的非交互模式下不可用。

## 另请参阅

* [计算机在桌面上的使用](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>)：与图形设置页面具有相同的功能
* Chrome 中的 [Claude ](<https://code.claude.com/docs/en/chrome>)：基于 Web 的任务的浏览器自动化
* [MCP](<https://code.claude.com/docs/en/mcp>)：将Claude连接到结构化工具和API
* [沙箱](<https://code.claude.com/docs/en/sandboxing>)：Claude的Bash工具如何隔离文件系统和网络访问
* [计算机使用安全指南](<https://support.claude.com/en/articles/14128542>)：安全使用计算机的最佳实践


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Let Claude use your computer from the CLI

> Enable computer use in the Claude Code CLI so Claude can open apps, click, type, and see your screen on macOS. Test native apps, debug visual issues, and automate GUI-only tools without leaving your terminal.



**注释：**

  Computer use is a research preview on macOS that requires a Pro or Max plan. It is not available on Team or Enterprise plans. It requires an interactive session, so it is not available in non-interactive mode with the `-p` flag.


Computer use lets Claude open apps, control your screen, and work on your machine the way you would. From the CLI, Claude can compile a Swift app, launch it, click through every button, and screenshot the result, all in the same conversation where it wrote the code.

This page covers how computer use works in the CLI. For the Desktop app on macOS or Windows, see [computer use in Desktop](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>).

## What you can do with computer use

Computer use handles tasks that require a GUI: anything you'd normally have to leave the terminal and do by hand.

* **Build and validate native apps**: ask Claude to build a macOS menu bar app. Claude writes the Swift, compiles it, launches it, and clicks through every control to verify it works before you ever open it.
* **End-to-end UI testing**: point Claude at a local Electron app and say "test the onboarding flow." Claude opens the app, clicks through signup, and screenshots each step. No Playwright config, no test harness.
* **Debug visual and layout issues**: tell Claude "the modal is clipping on small windows." Claude resizes the window, reproduces the bug, screenshots it, patches the CSS, and verifies the fix. Claude sees what you see.
* **Drive GUI-only tools**: interact with design tools, hardware control panels, the iOS Simulator, or proprietary apps that have no CLI or API.

## When computer use applies

Claude has several ways to interact with an app or service. Computer use is the broadest and slowest, so Claude tries the most precise tool first:

* If you have an [MCP server](<https://code.claude.com/docs/en/mcp>) for the service, Claude uses that.
* If the task is a shell command, Claude uses Bash.
* If the task is browser work and you have [Claude in Chrome](<https://code.claude.com/docs/en/chrome>) set up, Claude uses that.
* If none of those apply, Claude uses computer use.

Screen control is reserved for things nothing else can reach: native apps, simulators such as the iOS Simulator, and tools without an API.

## Enable computer use

Computer use is available as a built-in MCP server called `computer-use`. It's off by default until you enable it.



  

#### Open the MCP menu

    In an interactive Claude Code session, run:

    ```text theme={null}
    /mcp
    ```

    Find `computer-use` in the server list. It shows as disabled.
  


  

#### Enable the server

    Select `computer-use` and choose **Enable**. The setting persists per project, so you only do this once for each project where you want computer use.
  


  

#### Grant macOS permissions

    The first time Claude tries to use your computer, you'll see a prompt to grant two macOS permissions:

    * **Accessibility**: lets Claude click, type, and scroll
    * **Screen Recording**: lets Claude see what's on your screen

    The prompt includes links to open the relevant System Settings pane. Grant both, then select **Try again** in the prompt. macOS may require you to restart Claude Code after granting Screen Recording.
  


After setup, ask Claude to do something that needs the GUI:

```text theme={null}
Build the app target, launch it, and click through each tab to make
sure nothing crashes. Screenshot any error states you find.
```

## Approve apps per session

Enabling the `computer-use` server doesn't grant Claude access to every app on your machine. The first time Claude needs a specific app in a session, a prompt appears in your terminal showing:

* Which apps Claude wants to control
* Any extra permissions requested, such as clipboard access
* How many other apps will be hidden while Claude works

Choose **Allow for this session** or **Deny**. Approvals last for the current session. You can approve multiple apps at once when Claude requests them together.

Apps with broad reach show an extra warning in the prompt so you know what approving them grants:

| Warning | Applies to |
| :- | :- |
| Equivalent to shell access | Terminal, iTerm, VS Code, Warp, and other terminals and IDEs |
| Can read or write any file | Finder |
| Can change system settings | System Settings |

These apps aren't blocked. The warning lets you decide whether the task warrants that level of access.

Approve Finder to let Claude click the desktop, the Dock, or a Finder window.

Claude's level of control also varies by app category: browsers and trading platforms are view-only, terminals and IDEs are click-only, and everything else gets full control. See [app permissions in Desktop](<https://code.claude.com/docs/en/desktop#app-permissions>) for the complete tier breakdown.

## How Claude works on your screen

Understanding the flow helps you anticipate what Claude will do and how to intervene.

### One session at a time

Only one session at a time can use your computer. A session takes a lock at its first computer use action and releases it when the session exits, not when the task finishes. A second session's computer use fails with an error naming the session that holds the lock. Exit that session first.

### Apps are hidden while Claude works

When Claude starts controlling your screen, other visible apps are hidden so Claude interacts with only the approved apps. Your terminal window stays visible and is excluded from screenshots, so you can watch the session and Claude never sees its own output.

When Claude finishes the turn, hidden apps are restored automatically.

### Screenshots are downscaled automatically

Claude Code downscales every screenshot before sending it to the model. You don't need to lower your display resolution or resize windows on Retina or other high-resolution displays. A 16-inch MacBook Pro at native Retina resolution captures at 3456×2234 and downscales to roughly 1372×887, preserving aspect ratio.

There is no setting to change the target size. If on-screen text or controls are too small for Claude to read after downscaling, increase their size in the app rather than changing your display resolution.

### Stop at any time

The first time Claude uses your computer in each turn, a macOS notification appears: "Claude is using your computer · press Esc to stop." Press `Esc` anywhere to abort the current action immediately, or press `Ctrl+C` in the terminal. Either way, Claude stops, unhides your apps, and returns control to you. The session keeps the [computer use lock](<#one-session-at-a-time>) until it exits.

A second notification appears when Claude is done.

## Safety and the trust boundary



**注意：**

  Unlike the [sandboxed Bash tool](<https://code.claude.com/docs/en/sandboxing>), computer use runs on your actual desktop with access to the apps you approve. Claude checks each action and flags potential prompt injection from on-screen content, but the trust boundary is different. See the [computer use safety guide](<https://support.claude.com/en/articles/14128542>) for best practices.


The built-in guardrails reduce risk without requiring configuration:

* **Per-app approval**: Claude can only control apps you've approved in the current session.
* **Sentinel warnings**: apps that grant shell, filesystem, or system settings access are flagged before you approve.
* **Terminal excluded from screenshots**: Claude never sees your terminal window, so on-screen prompts in your session can't feed back into the model.
* **Global escape**: the `Esc` key aborts computer use from anywhere, and the key press is consumed so prompt injection can't use it to dismiss dialogs.
* **Lock file**: only one session can control your machine at a time.

## Example workflows

These examples show common ways to combine computer use with coding tasks.

### Validate a native build

After making changes to a macOS or iOS app, have Claude compile and verify in one pass:

```text theme={null}
Build the MenuBarStats target, launch it, open the preferences window,
and verify the interval slider updates the label. Screenshot the
preferences window when you're done.
```

Claude runs `xcodebuild`, launches the app, interacts with the UI, and reports what it finds.

### Reproduce a layout bug

When a visual bug only appears at certain window sizes, let Claude find it:

```text theme={null}
The settings modal clips its footer on narrow windows. Resize the app
window down until you can reproduce it, screenshot the clipped state,
then check the CSS for the modal container.
```

Claude resizes the window, captures the broken state, and reads the relevant stylesheets.

### Test a simulator flow

Drive the iOS Simulator without writing XCTest:

```text theme={null}
Open the iOS Simulator, launch the app, tap through the onboarding
screens, and tell me if any screen takes more than a second to load.
```

Claude controls the simulator the same way you would with a mouse. This flow applies to the CLI; in the Desktop app, the same request opens the [iOS Simulator pane](<https://code.claude.com/docs/en/desktop-ios-simulator>) instead of screen control.

## Differences from the Desktop app

The CLI and Desktop surfaces share the same computer use engine, with a few differences:

| Feature | Desktop | CLI |
| :- | :- | :- |
| Platforms | macOS and Windows | macOS only |
| Enable | Toggle in **Settings > This computer > System** | Enable `computer-use` in `/mcp` |
| Denied apps list | Configurable in Settings | Not yet available |
| Auto-unhide toggle | Optional | Always on |
| Dispatch integration | Dispatch-spawned sessions can use computer use | Not applicable |

## Troubleshooting

### "Computer use is in use by another Claude session"

Another Claude Code session holds the lock, which it keeps until it exits. Exit that session. If the other session crashed, the lock is released automatically when Claude detects the process is no longer running.

### macOS permissions prompt keeps reappearing

macOS sometimes requires a restart of the requesting process after you grant Screen Recording. Quit Claude Code completely and start a new session. If the prompt persists, open **System Settings > Privacy & Security > Screen Recording** and confirm your terminal app is listed and enabled.

### `computer-use` doesn't appear in `/mcp`

The server only appears on eligible setups. Check that:

* You're on macOS. Computer use in the CLI is not available on Linux or Windows. On Windows, use [computer use in Desktop](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>) instead.
* You're on a Pro or Max plan. Run `/status` to confirm your subscription.
* You're authenticated through claude.ai. Computer use is not available with third-party providers like Amazon Bedrock, Google Cloud's Agent Platform, or Microsoft Foundry. If you access Claude exclusively through a third-party provider, you need a separate claude.ai account to use this feature.
* You're in an interactive session. Computer use is not available in non-interactive mode with the `-p` flag.

## See also

* [Computer use in Desktop](<https://code.claude.com/docs/en/desktop#let-claude-use-your-computer>): the same capability with a graphical settings page
* [Claude in Chrome](<https://code.claude.com/docs/en/chrome>): browser automation for web-based tasks
* [MCP](<https://code.claude.com/docs/en/mcp>): connect Claude to structured tools and APIs
* [Sandboxing](<https://code.claude.com/docs/en/sandboxing>): how Claude's Bash tool isolates filesystem and network access
* [Computer use safety guide](<https://support.claude.com/en/articles/14128542>): best practices for safe computer use


</details>
