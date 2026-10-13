# Qwen Code 浏览器操作

说明通过已有 Chrome 页面和登录状态开展网页操作，以及启用、停用和版本前提；适用系统以正文为准。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-23](<https://github.com/QwenLM/qwen-code/commit/afd7236298b7bbdd578307b58283f96124f8371e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/browser-use.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 浏览器操作.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 浏览器使用

浏览器使用允许 Qwen Code 使用您的 
 现有选项卡和登录会话来处理 Chrome 浏览器中的页面。

## 使用

使用 macOS 或带有 Chrome 的 Linux 125 或稍后和 Qwen Code 0.24.2 或稍后
（检查与 `qwen --version`). **安装
[Qwen Code 来自 Chrome 网上应用店的扩展程序](<https://chromewebstore.google.com/detail/qwen-code/hdhmmjclhibojdddmancfgbkleahfaph>)
在您要使用的 Chrome 配置文件中。** 扩展名是必需的，但不是
由安装 Qwen Code 包。 Chrome 在安装后会保持最新状态。

**每个配置文件使用一份扩展副本。**如果您之前已解压加载
，请在使用
商店版本之前删除或禁用`chrome://extensions`中的该副本。启用两者后，Qwen 会看到同一配置文件 
 的两个浏览器，并且可以连接到其中一个。

如果 Chrome 网上应用店表示该扩展程序在您所在的地区不可用，请
从源代码构建它：按照 Qwen Code 存储库中 `packages/chrome-extension` 目录的 
[README](<https://github.com/QwenLM/qwen-code/tree/main/packages/chrome-extension#readme>)
，然后 
 打开 `chrome://extensions`，启用开发人员模式，选择**加载解压后的**，
选择构建的`dist/extension`目录。

直接描述您的浏览器任务，例如：

> 阅读我打开的仪表板并总结今天的订单。

Qwen 在适当的时候选择浏览器使用技能。第一个浏览器任务
自动在您的用户目录中注册一个小的本地连接程序；
以后的任务重用它。 Qwen在
操作页面之前确认与分机的连接。如果无法连接，请打开 Chrome 并检查 Qwen Code 是否为 
0.24.2 或更高版本，并且该扩展程序已在预期配置文件中启用，然后 
 重试。如果运行时依赖项需要在首次使用时进行配置，Qwen将指导
您并可能要求您重新启动。不需要单独的浏览器使用 Qwen 扩展或 
`qwen serve` 过程。

## 禁用

使用`/skills`禁用**浏览器使用**。这会隐藏模型的技能
，但不会断开现有浏览器会话或删除对话中已加载的指令
。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Browser Use

Browser Use lets Qwen Code work with pages in your Chrome browser, using your
existing tabs and signed-in sessions.

## Use

Use macOS or Linux with Chrome 125 or later and Qwen Code 0.24.2 or later
(check with `qwen --version`). **Install the
[Qwen Code extension from the Chrome Web Store](<https://chromewebstore.google.com/detail/qwen-code/hdhmmjclhibojdddmancfgbkleahfaph>)
in the Chrome profile you want to use.** The extension is required and is not
installed by the Qwen Code package. Chrome keeps it up to date once installed.

**Use one copy of the extension per profile.** If you previously loaded it
unpacked, remove or disable that copy in `chrome://extensions` before using the
store version. With both enabled, Qwen sees two browsers for the same profile
and may connect to either.

If the Chrome Web Store says the extension is not available in your region,
build it from source instead: follow the
[README](<https://github.com/QwenLM/qwen-code/tree/main/packages/chrome-extension#readme>)
of the `packages/chrome-extension` directory in the Qwen Code repository, then
open `chrome://extensions`, enable Developer mode, choose **Load unpacked**, and
pick the built `dist/extension` directory.

Describe your browser task directly, for example:

> Read my open dashboard and summarize today's orders.

Qwen selects the Browser Use skill when appropriate. The first browser task
automatically registers a small local connection program in your user directory;
later tasks reuse it. Qwen confirms the connection with the extension before
operating pages. If it cannot connect, open Chrome and check that Qwen Code is
0.24.2 or later and that the extension is enabled in the intended profile, then
retry. If a runtime dependency needs configuration on first use, Qwen will guide
you and may ask you to restart. No separate Browser Use Qwen extension or
`qwen serve` process is needed.

## Disable

Use `/skills` to disable **browser-use**. This hides the skill from the model
but does not disconnect an existing browser session or remove instructions
already loaded in a conversation.


</details>
