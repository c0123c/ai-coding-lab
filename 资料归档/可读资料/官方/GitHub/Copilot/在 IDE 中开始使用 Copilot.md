# 在 IDE 中开始使用 Copilot

通过代码问答、交付一个任务和获得内联建议介绍基础操作，引导使用解释、修改和 Agent 验证能力。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/get-started/quickstart-for-using-github-copilot-in-your-ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中开始使用 Copilot.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Quickstart for using GitHub Copilot in your IDE
shortTitle: Copilot in your IDE
intro: 'Use {% data variables.product.prodname_copilot_short %} in your IDE to explain concepts, complete code, propose edits, and validate files with agent mode.'
allowTitleToDifferFromFilename: true
versions:
  feature: copilot
redirect_from:
  - /copilot/getting-started-with-github-copilot
  - /github/copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-visual-studio-code
  - /github/copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-a-jetbrains-ide
  - /github/copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-neovim
  - /github/copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-visual-studio
  - /copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-visual-studio-code
  - /copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-a-jetbrains-ide
  - /copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-neovim
  - /copilot/getting-started-with-github-copilot/getting-started-with-github-copilot-in-visual-studio
  - /copilot/using-github-copilot/getting-started-with-github-copilot
  - /copilot/quickstart
contentType: get-started
category:
  - Learn about Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.prodname_copilot %} 在支持的 IDE 中可用：{% data variables.product.prodname_vscode %}、{% data variables.product.prodname_vs %}、{% data variables.product.prodname_jetbrains_ides %}、Eclipse 和 XCode。它可以回答问题并解释概念，并在您工作时建议或完成代码。在支持agent模式的IDE中，它还可以建议编辑和验证文件。

本快速入门向您展示如何使用这些功能。大约需要十分钟。

## 先决条件

* **有效的 {% data variables.product.prodname_copilot_short %} 计划。** 请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>)。
* **IDE 的 {% data variables.product.prodname_copilot_short %} 分机。** 请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>)。
* **已在您的IDE中登录{% data variables.product.company_short %}。**如果您遇到身份验证问题，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues>)。

## 询问有关您的代码的问题

1. 在编辑器中打开现有的代码文件。
1. 打开{% data variables.copilot.copilot_chat_short %}窗口。

您执行此操作的方式取决于您的 IDE。
   * 在{% data variables.product.prodname_vscode %}中，按<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd>(Windows/Linux)或<kbd>控制</kbd>+<kbd>命令</kbd>+<kbd>I</kbd>(macOS)。
   * 在其他 IDE 中，在菜单栏或侧边栏中查找 {% data variables.copilot.copilot_chat_short %}。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)。

1. 输入`what does this file do`，然后按<kbd>输入</kbd>。

{% data variables.product.prodname_copilot_short %}的答案显示在您的问题下方。

1. 在编辑器中选择一行代码，然后在聊天窗口中键入 `explain this line`。

{% data variables.product.prodname_copilot_short %} 可以看到您打开的文件，因此您可以询问您的代码，而不是先描述它。

## 让 {% data variables.product.prodname_copilot_short %} 完成一项任务

大多数 IDE 支持 **agent 模式 **。在 agent 模式下，您描述所需内容，{% data variables.product.prodname_copilot_short %} 决定要更改哪些文件、进行编辑并在您批准的情况下运行命令。

检查您的【IDE】是否支持【agent】模式，请参见【[AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix?tool=ides>)】。

1. 在聊天窗口中，选择 **Agent**，然后描述任务。例如，`Create a task manager web app with the ability to add, delete, and mark tasks as completed`。
1. 按<kbd>输入</kbd>。

{% data variables.product.prodname_copilot_short %} 生成所需的文件和代码。

1. 检查更改，然后使用 IDE 的控件来接受它们。

准备好后，保存更改，并在一切看起来不错时提交。

## 获取您的第一个内嵌建议

{% data variables.product.prodname_copilot %} 可以在您工作时建议代码，并支持多种编程语言。此示例使用 JavaScript，但您可以使用您的首选语言进行操作。

1. 在您的 IDE 中，创建一个新的 JavaScript (_*.js_) 文件。
{% data reusables.copilot.type-function-header %}
 {% data variables.product.prodname_copilot_short %} 将自动以灰色文本建议整个函数体。确切的建议可能会有所不同。
1. {% data reusables.copilot.accept-suggestion %}

准备好后，保存更改，并在一切看起来不错时提交。

## 后续步骤

* 使用自定义指令在您的 IDE 中自定义 {% data variables.product.prodname_copilot_short %}。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide#further-reading>)。
* 尝试{% data variables.copilot.github_copilot_app %}。参见[AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.product.prodname_copilot %} is available in supported IDEs: {% data variables.product.prodname_vscode %}, {% data variables.product.prodname_vs %}, {% data variables.product.prodname_jetbrains_ides %}, Eclipse, and XCode. It can answer questions and explain concepts, and suggest or complete code as you work. In IDEs that support agent mode, it can also propose edits and validate files.

This quickstart shows you how to use these capabilities. It takes about ten minutes.

## Prerequisites

* **An active {% data variables.product.prodname_copilot_short %} plan.** See [AUTOTITLE](<https://github.com/copilot/get-started/about-github-copilot#get-access>).
* **The {% data variables.product.prodname_copilot_short %} extension for your IDE.** See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>).
* **Signed in to {% data variables.product.company_short %} in your IDE.** If you have authentication problems, see [AUTOTITLE](<https://github.com/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues>).

## Ask a question about your code

1. Open an existing code file in your editor.
1. Open the {% data variables.copilot.copilot_chat_short %} window.

   The way you do this depends on your IDE.
   * In {% data variables.product.prodname_vscode %}, press <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) or <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (macOS).
   * In other IDEs, look for {% data variables.copilot.copilot_chat_short %} in the menu bar or sidebar. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

1. Type `what does this file do`, then press <kbd>Enter</kbd>.

   {% data variables.product.prodname_copilot_short %}'s answer displays below your question.

1. Select a line of code in the editor, then type `explain this line` in the chat window.

{% data variables.product.prodname_copilot_short %} can see the file you have open, so you can ask about your code instead of describing it first.

## Let {% data variables.product.prodname_copilot_short %} work through a task

Most IDEs support **agent mode**. In agent mode, you describe what you want, and {% data variables.product.prodname_copilot_short %} decides which files to change, makes the edits, and runs commands with your approval.

To check whether your IDE supports agent mode, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix?tool=ides>).

1. In the chat window, select **Agent**, then describe a task. For example, `Create a task manager web app with the ability to add, delete, and mark tasks as completed`.
1. Press <kbd>Enter</kbd>.

   {% data variables.product.prodname_copilot_short %} generates the files and code needed.

1. Review the changes, then use your IDE's controls to accept them.

When you're ready, save your changes, and commit when everything looks good.

## Get your first inline suggestion

{% data variables.product.prodname_copilot %} can suggest code as you work and supports many programming languages. This example uses JavaScript, but you can follow along in your preferred language.

1. In your IDE, create a new JavaScript (_*.js_) file.
{% data reusables.copilot.type-function-header %}
   {% data variables.product.prodname_copilot_short %} will automatically suggest an entire function body in grayed text. The exact suggestion may vary.
1. {% data reusables.copilot.accept-suggestion %}

When you're ready, save your changes, and commit when everything looks good.

## Next steps

* Customize {% data variables.product.prodname_copilot_short %} in your IDE with custom instructions. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/customize-copilot/configure-custom-instructions/add-repository-instructions-in-your-ide#further-reading>).
* Try the {% data variables.copilot.github_copilot_app %}. See [AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>).


</details>
