# OpenCode 入门、项目初始化与首次开发操作

入门指南。包含安装、模型连接、初始化 AGENTS.md、代码提问、增加功能、修改和撤销等步骤；文件名虽为 index，正文包含实际操作内容。

- 来源：OpenCode
- 资料类型：入门指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-13](<https://github.com/anomalyco/opencode/commit/88e2eb5416043378f96720db83920f28e0250245>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：入门样例需要在自己的项目中验证，文件名不能代替正文判断。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/index.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 入门、项目初始化与首次开发操作.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Intro
description: Get started with OpenCode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

[**OpenCode**](<https://opencode.ai/>)是一个开源的AI编码agent。它可用作基于终端的界面、桌面应用程序或 IDE 扩展。

![OpenCode TUI 与 opencode 主题](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/lander/screenshot.png>)

让我们开始吧。

---

#### 先决条件

要在终端中使用 OpenCode，您需要：

1. 现代终端模拟器，例如：
   - [WezTerm](<https://wezterm.org>)，跨平台
   - [Alacritty](<https://alacritty.org>)，跨平台
   - [Ghostty](<https://ghostty.org>)、Linux 和 macOS
   - [Kitty](<https://sw.kovidgoyal.net/kitty/>)、Linux 和 macOS

2. 您要使用的 LLM 提供商的 API 键。

---

## 安装

安装 OpenCode 最简单的方法是通过安装脚本。

```bash
curl -fsSL https://opencode.ai/install | bash
```

您还可以使用以下命令安装它：

- **使用Node.js**

#### npm

```bash
npm install -g opencode-ai
```

#### Bun

```bash
bun install -g opencode-ai
```

#### pnpm

```bash
pnpm install -g opencode-ai
```

#### 纱线

```bash
yarn global add opencode-ai
```

- **在 macOS 和 Linux** 上使用 Homebrew

```bash
brew install anomalyco/tap/opencode
```

  > 我们建议使用 OpenCode 点击获取最新版本。官方 `brew install opencode` 公式由 Homebrew 团队维护，更新频率较低。

- **在 Arch Linux 上安装**

```bash
sudo pacman -S opencode           # Arch Linux (Stable)
paru -S opencode-bin              # Arch Linux (Latest from AUR)
```

#### 窗户

**提示：推荐：使用WSL：**

为了在 Windows 上获得最佳体验，我们建议使用 [适用于 Linux 的 Windows 子系统 (WSL)](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>)。它提供了更好的性能并与OpenCode的功能完全兼容。

- **使用巧克力**

```bash
choco install opencode
```

- **使用勺子**

```bash
scoop install opencode
```

- **使用NPM**

```bash
npm install -g opencode-ai
```

- **使用Mise**

```bash
mise use -g github:anomalyco/opencode
```

- **使用Docker**

```bash
docker run -it --rm ghcr.io/anomalyco/opencode
```

目前正在支持使用 Bun 在 Windows 上安装 OpenCode。

您还可以从 [Releases](<https://github.com/anomalyco/opencode/releases>) 获取二进制文件。

---

## 配置

使用 OpenCode，您可以通过配置 API 密钥来使用任何 LLM 提供商。

如果您不熟悉使用 LLM 提供程序，我们建议使用 [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>)。
这是经过 OpenCode
 团队测试和验证的模型精选列表。

1. 在TUI中运行`/connect`命令，选择opencode，然后前往[opencode.ai/auth](<https://opencode.ai/auth>)。

```txt
/connect
```

2. 登录，添加您的账单详细信息，然后复制您的 API 密钥。

3. 粘贴您的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

或者，您可以选择其他提供商之一。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#directory>)。

---

## 初始化

现在您已经配置了提供程序，您可以导航到您想要处理的项目
。

```bash
cd /path/to/project
```

并运行OpenCode。

```bash
opencode
```

接下来，通过运行以下命令初始化项目的 OpenCode。

```bash
/init
```

这将使 OpenCode 分析您的项目并在 
 项目根目录中创建一个 `AGENTS.md` 文件。

**提示：**

您应该将项目的 `AGENTS.md` 文件提交到 Git。

这有助于OpenCode理解项目结构和使用的编码模式
。

---

## 用途

您现在可以使用 OpenCode 来处理您的项目。有什么问题请尽管问
！

如果您不熟悉使用 AI 编码 agent，这里的一些示例可能 
 有帮助。

---

### 提出问题

您可以要求OpenCode向您解释代码库。

**提示：**

使用`@`键可以模糊搜索工程中的文件。

```txt
How is authentication handled in @packages/functions/src/api/index.ts
```

如果您没有处理代码库的一部分，这会很有帮助。

---

### 添加功能

您可以要求OpenCode为您的项目添加新功能。不过我们首先建议要求它制定一个计划。

1. **创建计划**

OpenCode 有一个 _ 计划模式 _，该模式禁用其进行更改的能力，并且 
 相反建议 _ 如何 _ 实现该功能。

使用 **Tab** 键切换到它。您会在右下角看到一个指示符。

**代码示例：** `Switch to Plan mode`

```bash
<TAB>
```

现在让我们描述一下我们想要它做什么。

```txt
When a user deletes a note, we'd like to flag it as deleted in the database.
Then create a screen that shows all the recently deleted notes.
From this screen, the user can undelete a note or permanently delete it.
```

您想要提供 OpenCode 足够的详细信息以了解您想要的内容。它有助于
 与它交谈，就像你正在与团队中的初级开发人员交谈一样。

**提示：**

为 OpenCode 提供大量上下文和示例，以帮助其理解您 
 想要的内容。

2. **迭代计划**

一旦它为您提供了计划，您就可以提供反馈或添加更多详细信息。

```txt
We'd like to design this new screen using a design I've used before.
[Image #1] Take a look at this image and use it as a reference.
```

**提示：**

将图像拖放到终端中以将其添加到提示中。

OpenCode 可以扫描您提供的任何图像并将其添加到提示中。您可以
通过将图像拖放到终端来执行此操作。

3. **构建功能**

   一旦您对计划感到满意，请切换回 _构建模式_ 通过
   击中 **选项卡** 再次按键。

```bash
<TAB>
```

并要求其做出改变。

```bash
Sounds good! Go ahead and make the changes.
```

---

### 做出改变

对于更直接的更改，您可以要求OpenCode直接构建它
，而无需先查看计划。

```txt
We need to add authentication to the /settings route. Take a look at how this is
handled in the /notes route in @packages/functions/src/notes.ts and implement
the same logic in @packages/functions/src/settings.ts
```

您需要确保提供大量详细信息，以便 OpenCode 做出正确的 
 更改。

---

### 撤消更改

假设您要求 OpenCode 进行一些更改。

```txt
Can you refactor the function in @packages/functions/src/api/index.ts?
```

但你意识到这不是你想要的。您**可以使用`/undo`命令撤消**更改
。

```bash
/undo
```

OpenCode 现在将恢复您所做的更改并再次显示您的原始消息
。

```txt
Can you refactor the function in @packages/functions/src/api/index.ts?
```

从这里您可以调整提示并要求 OpenCode 重试。

**提示：**

您可以多次运行 `/undo` 以撤消多项更改。

或者您**可以使用`/redo`命令重做**更改。

```bash
/redo
```

---

## 分享

您与 OpenCode 的对话可以 [ 与您的 
 团队 ](<https://github.com/docs/share>) 共享。

```bash
/share
```

这将创建当前对话的链接并将其复制到剪贴板。

**注释：**

默认情况下不共享对话。

这是 [ 与 OpenCode 的示例对话 ](<https://opencode.ai/s/4XP1fce5>)。

---

## 定制

就是这样！您现在是使用 OpenCode 的专家。

要使其成为您自己的，我们建议[选择主题](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>)，[自定义按键绑定](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>)，[配置代码格式化程序](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 格式化器启用与定制.md>)，[创建自定义命令](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 自定义命令和提示模板.md>)，或使用[OpenCode配置](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

[**OpenCode**](<https://opencode.ai/>) is an open source AI coding agent. It's available as a terminal-based interface, desktop app, or IDE extension.

![OpenCode TUI with the opencode theme](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/lander/screenshot.png>)

Let's get started.

---

#### Prerequisites

To use OpenCode in your terminal, you'll need:

1. A modern terminal emulator like:
   - [WezTerm](<https://wezterm.org>), cross-platform
   - [Alacritty](<https://alacritty.org>), cross-platform
   - [Ghostty](<https://ghostty.org>), Linux and macOS
   - [Kitty](<https://sw.kovidgoyal.net/kitty/>), Linux and macOS

2. API keys for the LLM providers you want to use.

---

## Install

The easiest way to install OpenCode is through the install script.

```bash
curl -fsSL https://opencode.ai/install | bash
```

You can also install it with the following commands:

- **Using Node.js**

#### npm

```bash
npm install -g opencode-ai
```

#### Bun

```bash
bun install -g opencode-ai
```

#### pnpm

```bash
pnpm install -g opencode-ai
```

#### Yarn

```bash
yarn global add opencode-ai
```

- **Using Homebrew on macOS and Linux**

```bash
brew install anomalyco/tap/opencode
```

  > We recommend using the OpenCode tap for the most up to date releases. The official `brew install opencode` formula is maintained by the Homebrew team and is updated less frequently.

- **Installing on Arch Linux**

```bash
sudo pacman -S opencode           # Arch Linux (Stable)
paru -S opencode-bin              # Arch Linux (Latest from AUR)
```

#### Windows

**提示：Recommended: Use WSL：**

For the best experience on Windows, we recommend using [Windows Subsystem for Linux (WSL)](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>). It provides better performance and full compatibility with OpenCode's features.

- **Using Chocolatey**

```bash
choco install opencode
```

- **Using Scoop**

```bash
scoop install opencode
```

- **Using NPM**

```bash
npm install -g opencode-ai
```

- **Using Mise**

```bash
mise use -g github:anomalyco/opencode
```

- **Using Docker**

```bash
docker run -it --rm ghcr.io/anomalyco/opencode
```

Support for installing OpenCode on Windows using Bun is currently in progress.

You can also grab the binary from the [Releases](<https://github.com/anomalyco/opencode/releases>).

---

## Configure

With OpenCode you can use any LLM provider by configuring their API keys.

If you are new to using LLM providers, we recommend using [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>).
It's a curated list of models that have been tested and verified by the OpenCode
team.

1. Run the `/connect` command in the TUI, select opencode, and head to [opencode.ai/auth](<https://opencode.ai/auth>).

```txt
/connect
```

2. Sign in, add your billing details, and copy your API key.

3. Paste your API key.

```txt
┌ API key
│
│
└ enter
```

Alternatively, you can select one of the other providers. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#directory>).

---

## Initialize

Now that you've configured a provider, you can navigate to a project that
you want to work on.

```bash
cd /path/to/project
```

And run OpenCode.

```bash
opencode
```

Next, initialize OpenCode for the project by running the following command.

```bash
/init
```

This will get OpenCode to analyze your project and create an `AGENTS.md` file in
the project root.

**提示：**

You should commit your project's `AGENTS.md` file to Git.

This helps OpenCode understand the project structure and the coding patterns
used.

---

## Usage

You are now ready to use OpenCode to work on your project. Feel free to ask it
anything!

If you are new to using an AI coding agent, here are some examples that might
help.

---

### Ask questions

You can ask OpenCode to explain the codebase to you.

**提示：**

Use the `@` key to fuzzy search for files in the project.

```txt
How is authentication handled in @packages/functions/src/api/index.ts
```

This is helpful if there's a part of the codebase that you didn't work on.

---

### Add features

You can ask OpenCode to add new features to your project. Though we first recommend asking it to create a plan.

1. **Create a plan**

   OpenCode has a _Plan mode_ that disables its ability to make changes and
   instead suggest _how_ it'll implement the feature.

   Switch to it using the **Tab** key. You'll see an indicator for this in the lower right corner.

**代码示例：** `Switch to Plan mode`

```bash
<TAB>
```

   Now let's describe what we want it to do.

```txt
When a user deletes a note, we'd like to flag it as deleted in the database.
Then create a screen that shows all the recently deleted notes.
From this screen, the user can undelete a note or permanently delete it.
```

   You want to give OpenCode enough details to understand what you want. It helps
   to talk to it like you are talking to a junior developer on your team.

**提示：**

   Give OpenCode plenty of context and examples to help it understand what you
   want.

2. **Iterate on the plan**

   Once it gives you a plan, you can give it feedback or add more details.

```txt
We'd like to design this new screen using a design I've used before.
[Image #1] Take a look at this image and use it as a reference.
```

**提示：**

   Drag and drop images into the terminal to add them to the prompt.

   OpenCode can scan any images you give it and add them to the prompt. You can
   do this by dragging and dropping an image into the terminal.

3. **Build the feature**

   Once you feel comfortable with the plan, switch back to _Build mode_ by
   hitting the **Tab** key again.

```bash
<TAB>
```

   And asking it to make the changes.

```bash
Sounds good! Go ahead and make the changes.
```

---

### Make changes

For more straightforward changes, you can ask OpenCode to directly build it
without having to review the plan first.

```txt
We need to add authentication to the /settings route. Take a look at how this is
handled in the /notes route in @packages/functions/src/notes.ts and implement
the same logic in @packages/functions/src/settings.ts
```

You want to make sure you provide a good amount of detail so OpenCode makes the right
changes.

---

### Undo changes

Let's say you ask OpenCode to make some changes.

```txt
Can you refactor the function in @packages/functions/src/api/index.ts?
```

But you realize that it is not what you wanted. You **can undo** the changes
using the `/undo` command.

```bash
/undo
```

OpenCode will now revert the changes you made and show your original message
again.

```txt
Can you refactor the function in @packages/functions/src/api/index.ts?
```

From here you can tweak the prompt and ask OpenCode to try again.

**提示：**

You can run `/undo` multiple times to undo multiple changes.

Or you **can redo** the changes using the `/redo` command.

```bash
/redo
```

---

## Share

The conversations that you have with OpenCode can be [shared with your
team](<https://github.com/docs/share>).

```bash
/share
```

This will create a link to the current conversation and copy it to your clipboard.

**注释：**

Conversations are not shared by default.

Here's an [example conversation](<https://opencode.ai/s/4XP1fce5>) with OpenCode.

---

## Customize

And that's it! You are now a pro at using OpenCode.

To make it your own, we recommend [picking a theme](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 终端主题与显示配置.md>), [customizing the keybinds](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>), [configuring code formatters](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 格式化器启用与定制.md>), [creating custom commands](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 自定义命令和提示模板.md>), or playing around with the [OpenCode config](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>).


</details>
