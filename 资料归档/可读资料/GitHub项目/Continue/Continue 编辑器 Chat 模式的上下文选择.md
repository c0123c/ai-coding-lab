# Continue 编辑器 Chat 模式的上下文选择

产品指南。介绍把选中代码、活动文件、指定文件、代码库、文档、终端和 Git Diff 加入对话，说明 VS Code 与 JetBrains 的操作方式。

- 来源：Continue
- 资料类型：产品指南
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2025-10-14](<https://github.com/continuedev/continue/commit/ef0b7f6c883ef43180da29a8994e5f766cdf03a7>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：针对编辑器 Chat 界面，部分上下文提供器可能属于存档版本行为。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/ide-extensions/chat/context-selection.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 编辑器 Chat 模式的上下文选择.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Chat Mode Context Selection"
sidebarTitle: "Context Selection"
description: "Learn how Continue selects relevant context for your chat requests, including text input, highlighted code, active files."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 如何使用文本输入

在输入框中键入问题或说明是唯一需要的上下文。

## 如何包含突出显示的代码

您通过按 cmd/ctrl + L (VS Code) 或 cmd/ctrl + J (JetBrains) 选择的突出显示代码将包含在提示中。

## 如何包含活动文件

您可以在发送请求时按 opt/alt + Enter 包含当前打开的文件作为上下文。

## 如何包含特定文件

您可以通过键入“@Files”并选择文件来将特定文件包含在当前工作区中。

## 代码库搜索

为了更好地了解代码库，请参阅我们的 [ 指南，让 agent 模式了解代码库和文档 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/ide-extensions/chat/Continue_让 Continue Agent 获取代码库和文档上下文.md>)。

## 如何包含文档站点

为了更好地了解文档，请参阅我们的 [ 指南，让 agent 模式了解代码库和文档 ](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/ide-extensions/chat/Continue_让 Continue Agent 获取代码库和文档上下文.md>)。

## 如何包含终端内容

您可以通过输入“@Terminal”将终端的内容包含在IDE中。

## 如何包含 Git Diff

您可以通过键入“@Git Diff”来包含对当前分支所做的所有更改。

## 如何使用其他上下文提供程序

您可以查看内置上下文提供程序的完整列表 [这里](<https://docs.continue.dev/customize/deep-dives/custom-providers>).


## 原文对照

<details>
<summary>展开完整原文</summary>

## How to Use Text Input

Typing a question or instructions into the input box is the only required context.

## How to Include Highlighted Code

The highlighted code you've selected by pressing cmd/ctrl + L (VS Code) or cmd/ctrl + J (JetBrains) will be included in your prompt.

## How to Include the Active File

You can include the currently open file as context by pressing opt/alt + enter when you send your request.

## How to Include a Specific File

You can include a specific file in your current workspace by typing '@Files' and selecting the file.

## Codebase Search

For better codebase awareness, see our [guide on making agent mode aware of codebases and documentation](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/ide-extensions/chat/Continue_让 Continue Agent 获取代码库和文档上下文.md>).

## How to Include Documentation Sites

For better documentation awareness, see our [guide on making agent mode aware of codebases and documentation](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/ide-extensions/chat/Continue_让 Continue Agent 获取代码库和文档上下文.md>).

## How to Include Terminal Contents

You can include the contents of the terminal in your IDE by typing '@Terminal'.

## How to Include Git Diff

You can include all of the changes you've made to your current branch by typing '@Git Diff'.

## How to Use Other Context Providers

You can see a full list of built-in context providers [here](<https://docs.continue.dev/customize/deep-dives/custom-providers>).


</details>
