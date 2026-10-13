# 在 IDE 中使用 Copilot Chat

介绍提问与 Agent 模式、提示输入、代码上下文、参与者和命令，支持解释代码、生成测试、修复问题和执行分步开发任务。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中使用 Copilot Chat（存档be38ec5d-481）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Asking GitHub Copilot questions in your IDE
intro: Use {% data variables.copilot.copilot_chat_short %} in your editor to give you code suggestions, explain code, generate unit tests, and suggest code fixes.
redirect_from:
  - /copilot/github-copilot-chat/using-github-copilot-chat
  - /copilot/github-copilot-chat/using-github-copilot-chat-in-your-ide
  - /copilot/github-copilot-chat/copilot-chat-in-ides/using-github-copilot-chat-in-your-ide
  - /copilot/github-copilot-chat/copilot-chat-in-ides
  - /copilot/using-github-copilot/asking-github-copilot-questions-in-your-ide
  - /copilot/using-github-copilot/copilot-chat/asking-github-copilot-questions-in-your-ide
  - /copilot/how-tos/chat/asking-github-copilot-questions-in-your-ide
  - /copilot/how-tos/chat/use-chat-in-ide
  - /copilot/how-tos/use-chat/use-chat-in-ide
  - /copilot/how-tos/chat-with-copilot/use-chat-in-ide
  - /copilot/how-tos/chat-with-copilot/chat-in-ide
  - /copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-modes
defaultTool: vscode
versions:
  feature: copilot
shortTitle: Chat in IDE
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

本指南介绍如何使用 {% data variables.copilot.copilot_chat_short %} 和 agents 将编码任务分解为步骤，使用工具读取文件、编辑代码和运行命令，以及在出现问题时进行自我纠正，从而自动执行编码任务。您还可以询问有关软件开发的一般问题，或有关项目中代码的具体问题。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>)。

要了解如何在桌面应用程序中将 {% data variables.product.prodname_copilot_short %} 用于 agent 驱动的工作流程，请参阅 [AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>)。

<!-- --------------------- -->
<!-- VS Code -->
<!-- --------------------- -->

{% vscode %}

## 先决条件

* **访问{% data variables.product.prodname_copilot %}**。 {% data reusables.copilot.subscription-prerequisite %}
{% data reusables.copilot.vscode-prerequisites %}

{% data reusables.copilot.chat-access-denied %}

## 聊天模式

您可以在以下模式下使用{% data variables.copilot.copilot_chat_short %}：

* **提问模式**：获取编码问题的答案并让{% data variables.product.prodname_copilot_short %}提供代码建议。
* **Agent模式**：让{% data variables.product.prodname_copilot_short %}自主完成设定的任务。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。
* **计划模式**：让{% data variables.product.prodname_copilot_short %}创建详细的实施计划，以确保满足所有要求。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)。

要在模式之间切换，请使用聊天视图底部的 agents 下拉菜单。

> [!NOTE]
> 如果您在模式选择器中没有看到 **Agent** 选项，则您的企业或组织管理员可能已为您的 IDE 禁用了 agent 模式。

### 使用询问模式

提问模式针对回答有关代码库、编码和一般技术概念的问题进行了优化。当您想要了解某事如何工作、探索想法或获得编码任务的帮助时，请使用询问模式。

要使用询问模式，请从聊天视图底部的 agents 下拉列表中选择 **询问 **，然后按如下所述提交提示。

## 提交提示

您可以向 agent 提供您想要构建的内容的高级描述，然后它就会开始工作。每个任务都在 agent 会话中运行，这是一个您可以跟踪、暂停、恢复或移交给另一个 agent 的持续对话。

1. 要打开聊天视图，请单击{% data variables.product.prodname_vscode %}标题栏中的聊天图标。如果未显示聊天图标，请右键单击标题栏并确保选择**命令中心**。

![“{% data variables.copilot.copilot_chat_short %}”按钮的屏幕截图，以深橙色轮廓突出显示。](<https://github.com/assets/images/help/copilot/vsc-copilot-chat-icon.png>)

1. 在提示框中输入提示。有关可以使用的提示类型的介绍，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

1. 评估 {% data variables.product.prodname_copilot_short %} 的响应，并在需要时提出后续请求。

响应可能包含文本、代码块、按钮、图像、URI 和文件树。响应通常包括交互元素。例如，响应可能包括用于插入代码块的菜单，或用于调用 {% data variables.product.prodname_vscode %} 命令的按钮。

要查看 {% data variables.copilot.copilot_chat_short %} 用于生成响应的文件，请选择响应顶部的 ** 使用的 _n_ 引用 ** 下拉列表。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)。

## 在提示中使用关键字

您可以使用特殊关键字来帮助{% data variables.product.prodname_copilot_short %}理解您的提示。例如，请参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

### 聊天参与者

聊天参与者就像领域专家，他们拥有可以为您提供帮助的专业知识。

{% data variables.copilot.copilot_chat_short %} 可以根据您的自然语言提示推断相关的聊天参与者，从而改进高级功能的发现，而无需您在提示中明确指定要使用的参与者。

> [！NOTE] 聊天参与者的自动推理目前位于 {% data variables.release-phases.public_preview %} 中，并且可能会发生变化。

或者，您可以手动指定聊天参与者，将提示范围限定到特定域。为此，请在聊天提示框中键入 `@`，后跟聊天参与者姓名。

要获取可用聊天参与者的列表，请在聊天提示框中键入 `@`。另请参阅 {% data variables.product.prodname_vscode %} 文档中的 [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#chat-participants>) 或 [聊天参与者 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-participants>)。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参阅 {% data variables.product.prodname_vscode %} 文档中的 [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) 或 [ 斜杠命令 ](<https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_slash-commands>)。

### 聊天变量

使用聊天变量在提示中包含特定上下文。要使用聊天变量，请在聊天提示框中键入 `#`，然后键入聊天变量。

要查看所有可用的聊天变量，请在聊天提示框中键入 `#`。另请参见[AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#chat-variables>)。

## 使用{% data variables.product.prodname_dotcom %}技能{% data variables.product.prodname_copilot_short %}

{% data reusables.copilot.using-skills %}

## 使用 Model Context Protocol (MCP) 服务器

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI {% data variables.copilot.copilot_chat_short %} 的型号

{% data reusables.copilot.change-the-ai-model %}

## 访问 {% data variables.copilot.copilot_chat_short %} 的其他方式

除了通过聊天视图提交提示外，您还可以通过其他方式提交提示：

* **快速聊天：** 要打开快速聊天下拉列表，请输入<kbd>Shift</kbd>+<kbd>选项</kbd>+<kbd>命令</kbd>+<kbd>L</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>L</kbd> （Windows/Linux）。
* **内联：** 要直接在编辑器或集成终端中启动内联聊天，请输入 <kbd>Command</kbd>+<kbd>i</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>i</kbd>（Windows/Linux）。
* **智能操作：** 要通过上下文菜单提交提示，请右键单击编辑器，在出现的菜单中选择 **{% data variables.product.prodname_copilot_short %}**，然后选择其中一项操作。智能操作也可以通过闪烁图标来访问，当您选择一行代码时，有时会出现该图标。

有关更多详细信息，请参阅 {% data variables.product.prodname_vscode %} 文档中的 [ 内联聊天 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_inline-chat>)、[ 快速聊天 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_quick-chat>) 和 [ 聊天智能操作 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-smart-actions>)。

## 使用{% data variables.copilot.copilot_chat_short %}中的图像

{% data reusables.copilot.using-images-in-chat %}

### 将图像附加到聊天提示中

1. 执行以下操作之一：

   * 复制图像并将其粘贴到聊天视图中。
   * 将一个或多个图像文件从操作系统的文件资源管理器（或从 {% data variables.product.prodname_vscode_shortname %} 中的资源管理器）拖放到聊天视图中。
   * 右键单击 {% data variables.product.prodname_vscode_shortname %} 资源管理器中的图像文件，然后单击 **{% data variables.product.prodname_copilot_short %}**，然后单击 ** 将文件添加到聊天 **。

{% data reusables.copilot.type-prompt-for-image %}

## 分享反馈

要指示响应是否有帮助，请使用响应旁边显示的“竖起大拇指”和“竖起大拇指”图标。

要留下有关 {% data variables.copilot.copilot_chat %} 扩展的反馈，请在 [microsoft/vscode-copilot-release](<https://github.com/microsoft/vscode-copilot-release/issues>) 存储库中打开问题。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [在{% data variables.product.prodname_vscode %}文档的{% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/copilot-chat>)中使用{% data variables.copilot.copilot_chat_short %}
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} 信任中心](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endvscode %}

<!-- --------------------- -->
<!-- Visual Studio -->
<!-- --------------------- -->

{% visualstudio %}

## 先决条件

* **访问{% data variables.product.prodname_copilot %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vs %} 2022 版本 17.8 或更高版本 **。请参阅{% data variables.product.prodname_vs %}文档中的[安装{% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/install/install-visual-studio>)。
  * _对于{% data variables.product.prodname_vs %} 17.8和17.9：_
    * **{% data variables.product.prodname_copilot %}扩展名**。请参阅[在{% data variables.product.prodname_vs %}文档中的{% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>)中安装{% data variables.product.prodname_copilot %}。
    * **{% data variables.copilot.copilot_chat %}扩展名**。请参阅[在{% data variables.product.prodname_vs %}文档中的{% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>)中安装{% data variables.product.prodname_copilot %}。

_{% data variables.product.prodname_vs %} 17.10 及更高版本内置了 {% data variables.product.prodname_copilot %} 和 {% data variables.copilot.copilot_chat %} 扩展。您无需单独安装它们。_
* **登录{% data variables.product.prodname_vs %}**中的{% data variables.product.company_short %}。如果您遇到身份验证问题，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio>)。

{% data reusables.copilot.chat-access-denied %}

## 聊天模式

{% data variables.product.prodname_vs %}支持以下聊天模式：

* **询问模式**：无需更改代码即可获得答案和指导。
* **Agent 模式**：为{% data variables.product.prodname_copilot_short %} 提供高级任务并允许其编辑代码、运行命令和迭代结果。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。

要在模式之间切换，请使用聊天面板底部的模式下拉菜单。聊天模式在 {% data variables.product.prodname_vs %} 17.14 及更高版本中可用。

### 使用询问模式

当您希望 {% data variables.product.prodname_copilot_short %} 解释代码、回答问题或建议方法而不修改项目时，请使用询问模式。

要使用询问模式，请从聊天面板底部的模式下拉列表中选择 **询问**，然后按如下所述提交提示。

## 提交提示

您可以要求{% data variables.copilot.copilot_chat_short %}为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 在{% data variables.product.prodname_vs %}菜单栏中，单击**查看**，然后单击**{% data variables.copilot.copilot_chat %}**。
1. 在{% data variables.copilot.copilot_chat_short %}窗口中，输入提示，然后按**输入**。有关提示示例，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。
1. 评估 {% data variables.product.prodname_copilot_short %} 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制、插入或预览代码块结果的按钮。

要查看 {% data variables.copilot.copilot_chat_short %} 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)。

## 在提示中使用关键字

您可以使用特殊关键字来帮助{% data variables.product.prodname_copilot_short %}理解您的提示。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参阅 {% data variables.product.prodname_vs %} 文档中的 [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) 或 [ 斜杠命令 ](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#slash-commands>)。

### 参考文献

默认情况下，{% data variables.copilot.copilot_chat_short %} 将引用您已打开的文件或您已选择的代码。您还可以使用 `#` 后跟文件名、文件名和行号，或使用 `solution` 来引用特定文件、行或解决方案。

另请参阅 {% data variables.product.prodname_vs %} 文档中的 [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=visualstudio#references>) 或 [ 参考 ](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#reference>)。

## 将{% data variables.product.prodname_dotcom %}技能用于{% data variables.product.prodname_copilot_short %}（预览）

> [！NOTE]
> `@github` 聊天参与者当前处于预览状态，并且仅在 [{% data variables.product.prodname_vs %} 2022 预览版 2](<https://visualstudio.microsoft.com/vs/preview/>) 中可用。

{% data variables.product.prodname_copilot_short %}的{% data variables.product.prodname_dotcom %}特定技能扩展了{% data variables.product.prodname_copilot_short %}可以提供的信息类型。要访问 {% data variables.product.prodname_vs %} 中的 {% data variables.copilot.copilot_chat_short %} 中的这些技能，请在您的问题中包含 `@github`。

当您将 `@github` 添加到问题时，{% data variables.product.prodname_copilot_short %} 会根据您的问题内容动态选择适当的技能。您还可以明确要求{% data variables.copilot.copilot_chat_short %}使用特定技能。例如，`@github Search the web to find the latest GPT4 model from OpenAI.`

您可以通过询问 {% data variables.product.prodname_copilot_short %} 生成当前可用技能的列表：`@github What skills are available?`

## 使用 Model Context Protocol (MCP) 服务器

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI {% data variables.copilot.copilot_chat_short %} 的型号

{% data reusables.copilot.change-the-ai-model %}

## 访问 {% data variables.copilot.copilot_chat_short %} 的其他方式

除了通过聊天窗口提交提示之外，您还可以内联提交提示。要开始内联聊天，请右键单击编辑器窗口并选择 **询问 {% data variables.product.prodname_copilot_short %}**。

有关更多详细信息，请参阅 {% data variables.product.prodname_vs %} 文档中的 [在内联聊天视图中提问 ](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat#ask-questions-in-the-inline-chat-view>)。

## 使用{% data variables.copilot.copilot_chat_short %}中的图像

{% data reusables.copilot.using-images-in-chat %}

### 将图像附加到聊天提示中

1. 如果您在聊天视图右下角看到 AI 模型选择器，请选择支持向提示添加图像的模型之一：

1. 执行以下操作之一：

   * 复制图像并将其粘贴到聊天视图中。
   * 单击聊天视图右下角的回形针图标，单击**上传图片**，浏览到要附加的图片文件，选择它并单击**打开**。

如果需要，您可以添加多个图像。

1. 在聊天视图中输入您的提示以配合图像。例如，`explain this image` 或 `describe each of these images in detail`。

## 分享反馈

要分享有关{% data variables.copilot.copilot_chat_short %}的反馈，您可以使用{% data variables.product.prodname_vs %}中的**发送反馈**按钮。有关为 {% data variables.product.prodname_vs %} 提供反馈的更多信息，请参阅 [{% data variables.product.prodname_vs %} 反馈 ](<https://learn.microsoft.com/en-us/visualstudio/ide/how-to-report-a-problem-with-visual-studio?view=vs-2022>) 文档。

1. 在【{% data variables.product.prodname_vs %}】窗口右上角，点击【**】发送反馈【**】按钮。

![{% data variables.product.prodname_vs %}中分享反馈按钮的截图。](<https://github.com/assets/images/help/copilot/vs-share-feedback-button.png>)

1. 选择最能描述您的反馈的选项。
    * 要报告错误，请单击**报告问题**。
    * 要请求功能，请单击**建议功能**。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [在Microsoft学习文档](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat?view=vs-2022#use-copilot-chat-in-visual-studio>)的{% data variables.product.prodname_vs %}中使用{% data variables.copilot.copilot_chat %}
* [Microsoft学习文档](<https://learn.microsoft.com/en-us/visualstudio/ide/copilot-chat-context?view=vs-2022>)中改进{% data variables.copilot.copilot_chat %}结果的提示
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} 信任中心](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endvisualstudio %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

## 先决条件

* **访问{% data variables.product.prodname_copilot %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **兼容JetBrainsIDE**。 {% data variables.product.prodname_copilot %} 与以下 IDE 兼容：

  {% data reusables.copilot.jetbrains-compatible-ides %}
{% data reusables.copilot.jetbrains-plugin-prerequisites %}

{% data reusables.copilot.chat-access-denied %}

## 聊天模式

{% data variables.copilot.copilot_chat_short %} 面板中的 agent 选择器可让您选择哪个 agent 驱动您的对话。要切换 agents，请使用聊天面板底部的 Agents 下拉菜单。

以下 agents 可用：

* **Agent 模式**（默认）：自主任务执行的完整代理体验。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。
* **询问模式**：无需更改代码即可快速获得答案和帮助。
* **计划模式**：在实施之前协作制定计划 - {% data variables.product.prodname_copilot_short %}分析您的请求并构建结构化计划供您审核。参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)。
* **编辑模式**：对您单独查看和接受的多个文件进行受控编辑。
* **{% data variables.copilot.copilot_cli_short %}**：通过{% data variables.copilot.copilot_cli_short %}运行{% data variables.product.prodname_copilot_short %}，提供终端优先的代理体验，支持多种隔离模式、实时会话进度和工具调用可见性。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli>)。
* **{% data variables.copilot.custom_agents_caps_short %}**：使用根据您的具体需求量身定制的个性化agents。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>)。

{% data variables.copilot.copilot_edits_short %} 允许您使用编辑模式和 agent 模式直接从单个 {% data variables.copilot.copilot_chat_short %} 提示符对多个文件进行更改。

> [!TIP]
> 您也可以在不安装{% data variables.product.prodname_copilot_short %}插件的情况下从JetBrainsAI助手访问{% data variables.product.prodname_copilot_short %}。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>)。

### 使用编辑模式

{% data reusables.copilot.copilot-edits.edit-mode-description %}

1. 要启动编辑会话，请单击菜单栏中的 **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}**，然后选择 ** 打开 {% data variables.copilot.copilot_chat %}**。
1. 在聊天面板顶部，单击**{% data variables.copilot.copilot_edits_short %}**。
1. 将相关文件添加到_工作集_中，以向{% data variables.product.prodname_copilot %}指示您要处理哪些文件。您可以通过单击**添加所有打开的文件**来添加所有打开的文件或单独搜索单个文件。
1. 提交提示。为了响应您的提示，{% data variables.copilot.copilot_edits_short %} 确定要更改的 _ 工作集 _ 中的哪些文件，并添加更改的简短描述。
1. 查看更改并**接受**或**放弃**对每个文件的编辑。

## 提交提示

您可以要求{% data variables.copilot.copilot_chat_short %}为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 单击JetBrainsIDE窗口右侧的**{% data variables.copilot.copilot_chat %}**图标，打开{% data variables.copilot.copilot_chat_short %}窗口。

![活动栏中{% data variables.copilot.copilot_chat %}图标的屏幕截图。](<https://github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

1. 在提示框中输入提示。有关提示示例，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

1. 评估 {% data variables.product.prodname_copilot_short %} 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制或插入代码块的按钮。

要查看 {% data variables.copilot.copilot_chat_short %} 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)。

## 补充您的提示

您可以使用斜杠命令和文件引用来帮助 {% data variables.product.prodname_copilot_short %} 了解您要求它执行的操作。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参见[AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=jetbrains#slash-commands-2>)。

### 文件参考

默认情况下，{% data variables.copilot.copilot_chat_short %} 将引用您已打开的文件或您已选择的代码。您还可以通过将文件拖到聊天提示框中来告诉{% data variables.copilot.copilot_chat_short %}要引用哪些文件。或者，您可以右键单击文件，选择**GitHub Copilot**，然后选择**聊天中的参考文件**。

## 使用{% data variables.product.prodname_dotcom %}技能{% data variables.product.prodname_copilot_short %}

{% data reusables.copilot.using-skills %}

## 使用 Model Context Protocol (MCP) 服务器

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI {% data variables.copilot.copilot_chat_short %} 的型号

{% data reusables.copilot.change-the-ai-model %}

## 访问 {% data variables.copilot.copilot_chat_short %} 的其他方式

* **内置请求**。除了通过聊天窗口提交提示之外，您还可以通过右键单击文件，选择 **{% data variables.product.prodname_copilot %}**，然后选择选项之一来提交内置请求。
* **内联**。您可以内联提交聊天提示，并将其范围限制为突出显示的代码块或当前文件。
   * 要开始内联聊天，请右键单击代码块或当前文件中的任意位置，将鼠标悬停在 **{% data variables.product.prodname_copilot %}** 上，然后选择 **{% octicon "plus" aria-hidden="true" aria-label="plus" %} {% data variables.product.prodname_copilot_short %}：内联聊天**，或输入<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd>。

## 分享反馈

要分享有关{% data variables.copilot.copilot_chat_short %}的反馈，您可以使用JetBrains中的**分享反馈**链接。

1. 在JetBrainsIDE窗口右侧，单击**{% data variables.copilot.copilot_chat_short %}**图标，打开{% data variables.copilot.copilot_chat_short %}窗口。

![活动栏中{% data variables.copilot.copilot_chat_short %}图标的屏幕截图。](<https://github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

1. 在{% data variables.copilot.copilot_chat_short %}窗口顶部，点击**分享反馈**链接。

![{% data variables.copilot.copilot_chat_short %}窗口中分享反馈链接的屏幕截图。](<https://github.com/assets/images/help/copilot/jetbrains-share-feedback.png>)

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} 信任中心](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endjetbrains %}

<!-- --------------------- -->
<!-- XCode -->
<!-- --------------------- -->

{% xcode %}

## 先决条件

* **访问{% data variables.product.prodname_copilot %}**。 {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_copilot %}扩展**的最新版本。有关安装说明，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>)。
* **在 Xcode** 中登录 {% data variables.product.company_short %}。

{% data reusables.copilot.chat-access-denied %}

## 聊天模式

您可以在agent模式下使用{% data variables.copilot.copilot_chat_short %}自主完成设定的任务，或者在计划模式下在进行任何代码更改之前起草实施计划。要在模式之间切换，请使用聊天窗口底部的 agents 下拉菜单。

* 对于agent模式，请参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。
* 对于计划模式，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)。

## 提交提示

您可以要求{% data variables.copilot.copilot_chat_short %}为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 要打开聊天窗口，请单击菜单栏中的**编辑器**，然后单击**{% data variables.product.prodname_copilot %}**，然后单击**打开聊天**。 {% data variables.copilot.copilot_chat_short %} 在新窗口中打开。

1. 在提示框中输入提示。有关提示示例，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

1. 评估 {% data variables.product.prodname_copilot_short %} 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制或插入代码块的按钮。

要查看 {% data variables.copilot.copilot_chat_short %} 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>)。

## 使用 Model Context Protocol (MCP) 服务器

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI {% data variables.copilot.copilot_chat_short %} 的型号

{% data reusables.copilot.change-the-ai-model %}

## 在提示中使用关键字

您可以使用特殊关键字来帮助{% data variables.product.prodname_copilot_short %}理解您的提示。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=xcode#slash-commands>)。

## 文件参考

默认情况下，{% data variables.copilot.copilot_chat_short %} 将引用您已打开的文件或您已选择的代码。要附加特定文件作为参考，请在聊天提示框中单击{% octicon "paperclip" aria-label="Add attachments" %}。

## 聊天管理

您可以为每个 Xcode IDE 打开一个对话线程，以在不同的上下文中组织讨论。您还可以通过聊天记录重新访问之前的对话并参考过去的建议。

## 分享反馈

要指示回复是否有帮助，请使用回复旁边显示的 {% octicon "thumbsup" aria-label="Thumbs up" %} 或 {% octicon "thumbsdown" aria-label="Thumbs down" %}。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} 信任中心](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endxcode %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

## 先决条件

{% data reusables.copilot.eclipse-prerequisites %}
* **{% data variables.product.prodname_copilot %}扩展**的最新版本。从 [Eclipse Marketplace ](<https://aka.ms/copiloteclipse?ref_product=copilot&ref_type=engagement&ref_style=text>) 下载此文件。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>)。
* **在 Eclipse** 中登录 {% data variables.product.company_short %}。

{% data reusables.copilot.chat-access-denied %}

## 聊天模式

您可以在agent模式下使用{% data variables.copilot.copilot_chat_short %}自主完成设定的任务，或者在计划模式下在进行任何代码更改之前起草实施计划。要在模式之间切换，请使用聊天面板底部的 agents 下拉菜单。

* 对于agent模式，请参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)。
* 对于计划模式，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)。

## 提交提示

您可以要求{% data variables.copilot.copilot_chat_short %}为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 要打开 {% data variables.copilot.copilot_chat_short %} 面板，请单击 Eclipse 底部状态栏中的 {% data variables.product.prodname_copilot_short %} 图标 ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %})，然后单击 ** 打开聊天 **。

1. 在提示框中输入提示，然后按<kbd>输入</kbd>。

有关可以使用的提示类型的介绍，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

1. 评估 {% data variables.product.prodname_copilot_short %} 的响应，并在需要时提出后续请求。

## 在提示中使用关键字

您可以使用特殊关键字来帮助{% data variables.product.prodname_copilot_short %}理解您的提示。例如，请参见[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>)。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。例如，使用`/explain`要求{% data variables.product.prodname_copilot_short %}解释编辑器中当前显示的文件中的代码。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。

## 使用 Model Context Protocol (MCP) 服务器

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI {% data variables.copilot.copilot_chat_short %} 的型号

{% data reusables.copilot.change-the-ai-model %}

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} 信任中心](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endeclipse %}


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

This guide describes how to use {% data variables.copilot.copilot_chat_short %} and agents to automate coding tasks by breaking them into steps, using tools to read files, edit code, and run commands, and self-correcting when something goes wrong. You can also ask general questions about software development, or specific questions about the code in your project. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>).

To learn how to use {% data variables.product.prodname_copilot_short %} for agent-driven workflows in a desktop app, see [AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>).

<!-- --------------------- -->
<!-- VS Code -->
<!-- --------------------- -->

{% vscode %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot %}**. {% data reusables.copilot.subscription-prerequisite %}
{% data reusables.copilot.vscode-prerequisites %}

{% data reusables.copilot.chat-access-denied %}

## Chat modes

You can use {% data variables.copilot.copilot_chat_short %} in the following modes:

* **Ask mode**: to get answers to coding questions and get {% data variables.product.prodname_copilot_short %} to provide code suggestions.
* **Agent mode**: to get {% data variables.product.prodname_copilot_short %} to autonomously accomplish a set task. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* **Plan mode**: to get {% data variables.product.prodname_copilot_short %} to create detailed implementation plans to ensure all requirements are met. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

To switch between modes, use the agents dropdown at the bottom of the chat view.

> [!NOTE]
> If you don’t see the **Agent** option in the mode selector, your enterprise or organization administrator may have disabled agent mode for your IDE.

### Using ask mode

Ask mode is optimized for answering questions about your codebase, coding, and general technology concepts. Use ask mode when you want to understand how something works, explore ideas, or get help with coding tasks.

To use ask mode, select **Ask** from the agents dropdown at the bottom of the chat view, then submit a prompt as described below.

## Submitting prompts

You can give the agent a high-level description of what you want to build and it gets to work. Each task runs inside an agent session, a persistent conversation you can track, pause, resume, or hand off to another agent.

1. To open the chat view, click the chat icon in the title bar of {% data variables.product.prodname_vscode %}. If the chat icon is not displayed, right-click the title bar and make sure that **Command Center** is selected.

   ![Screenshot of the '{% data variables.copilot.copilot_chat_short %}' button, highlighted with a dark orange outline.](<https://github.com/assets/images/help/copilot/vsc-copilot-chat-icon.png>)

1. Enter a prompt in the prompt box. For an introduction to the kinds of prompts you can use, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

1. Evaluate {% data variables.product.prodname_copilot_short %}'s response, and make a follow-up request if needed.

   The response may contain text, code blocks, buttons, images, URIs, and file trees. The response often includes interactive elements. For example, the response may include a menu to insert a code block, or a button to invoke a {% data variables.product.prodname_vscode %} command.

   To see the files that {% data variables.copilot.copilot_chat_short %} used to generate the response, select the **Used _n_ references** dropdown at the top of the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using keywords in your prompt

You can use special keywords to help {% data variables.product.prodname_copilot_short %} understand your prompt. For examples, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

### Chat participants

Chat participants are like domain experts who have a specialty that they can help you with.

{% data variables.copilot.copilot_chat_short %} can infer relevant chat participants based on your natural language prompt, improving discovery of advanced capabilities without you having to explicitly specify the participant you want to use in your prompt.

> [!NOTE] Automatic inference for chat participants is currently in {% data variables.release-phases.public_preview %} and is subject to change.

Alternatively, you can manually specify a chat participant to scope your prompt to a specific domain. To do this, type `@` in the chat prompt box, followed by a chat participant name.

For a list of available chat participants, type `@` in the chat prompt box. See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#chat-participants>) or [Chat participants](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-participants>) in the {% data variables.product.prodname_vscode %} documentation.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) or [Slash commands](<https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_slash-commands>) in the {% data variables.product.prodname_vscode %} documentation.

### Chat variables

Use chat variables to include specific context in your prompt. To use a chat variable, type `#` in the chat prompt box, followed by a chat variable.

To see all available chat variables, type `#` in the chat prompt box. See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#chat-variables>).

## Using {% data variables.product.prodname_dotcom %} skills for {% data variables.product.prodname_copilot_short %}

{% data reusables.copilot.using-skills %}

## Using Model Context Protocol (MCP) servers

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI models for {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.change-the-ai-model %}

## Additional ways to access {% data variables.copilot.copilot_chat_short %}

In addition to submitting prompts through the chat view, you can submit prompts in other ways:

* **Quick chat:** To open the quick chat dropdown, enter <kbd>Shift</kbd>+<kbd>Option</kbd>+<kbd>Command</kbd>+<kbd>L</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>L</kbd> (Windows/Linux).
* **Inline:** To start an inline chat directly in the editor or integrated terminal, enter <kbd>Command</kbd>+<kbd>i</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>i</kbd> (Windows/Linux).
* **Smart actions:** To submit prompts via the context menu, right click in your editor, select **{% data variables.product.prodname_copilot_short %}** in the menu that appears, then select one of the actions. Smart actions can also be accessed via the sparkle icon that sometimes appears when you select a line of code.

See [inline chat](<https://code.visualstudio.com/docs/copilot/copilot-chat#_inline-chat>), [quick chat](<https://code.visualstudio.com/docs/copilot/copilot-chat#_quick-chat>), and [chat smart actions](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-smart-actions>) in the {% data variables.product.prodname_vscode %} documentation for more details.

## Using images in {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.using-images-in-chat %}

### Attaching images to your chat prompt

1. Do one of the following:

   * Copy an image and paste it into the chat view.
   * Drag and drop one or more image file from your operating system's file explorer—or from the Explorer in {% data variables.product.prodname_vscode_shortname %}—into the chat view.
   * Right-click an image file in the {% data variables.product.prodname_vscode_shortname %} Explorer and click **{% data variables.product.prodname_copilot_short %}** then **Add File to Chat**.

{% data reusables.copilot.type-prompt-for-image %}

## Sharing feedback

To indicate whether a response was helpful, use the thumbs up and thumbs down icons that appear next to the response.

To leave feedback about the {% data variables.copilot.copilot_chat %} extension, open an issue in the [microsoft/vscode-copilot-release](<https://github.com/microsoft/vscode-copilot-release/issues>) repository.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [Using {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vscode_shortname %}](<https://code.visualstudio.com/docs/copilot/copilot-chat>) in the {% data variables.product.prodname_vscode %} documentation
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} Trust Center](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endvscode %}

<!-- --------------------- -->
<!-- Visual Studio -->
<!-- --------------------- -->

{% visualstudio %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot %}**. {% data reusables.copilot.subscription-prerequisite %}
* **{% data variables.product.prodname_vs %} 2022 version 17.8 or later**. See [Install {% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/install/install-visual-studio>) in the {% data variables.product.prodname_vs %} documentation.
  * _For {% data variables.product.prodname_vs %} 17.8 and 17.9:_
    * **{% data variables.product.prodname_copilot %} extension**. See [Install {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>) in the {% data variables.product.prodname_vs %} documentation.
    * **{% data variables.copilot.copilot_chat %} extension**. See [Install {% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vs %}](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot&ref_type=engagement&ref_style=text>) in the {% data variables.product.prodname_vs %} documentation.

   _{% data variables.product.prodname_vs %} 17.10 and later have the {% data variables.product.prodname_copilot %} and {% data variables.copilot.copilot_chat %} extensions built in. You don't need to install them separately._
* **Sign in to {% data variables.product.company_short %} in {% data variables.product.prodname_vs %}**. If you experience authentication issues, see [AUTOTITLE](<https://github.com/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio>).

{% data reusables.copilot.chat-access-denied %}

## Chat modes

{% data variables.product.prodname_vs %} supports the following chat modes:

* **Ask mode**: Get answers and guidance without making changes to your code.
* **Agent mode**: Give {% data variables.product.prodname_copilot_short %} a high-level task and allow it to edit code, run commands, and iterate on the results. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).

To switch between modes, use the mode dropdown at the bottom of the chat panel. Chat modes are available in {% data variables.product.prodname_vs %} 17.14 and later.

### Using ask mode

Use ask mode when you want {% data variables.product.prodname_copilot_short %} to explain code, answer a question, or suggest an approach without modifying your project.

To use ask mode, select **Ask** from the mode dropdown at the bottom of the chat panel, then submit a prompt as described below.

## Submitting prompts

You can ask {% data variables.copilot.copilot_chat_short %} to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. In the {% data variables.product.prodname_vs %} menu bar, click **View**, then click **{% data variables.copilot.copilot_chat %}**.
1. In the {% data variables.copilot.copilot_chat_short %} window, enter a prompt, then press **Enter**. For example prompts, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).
1. Evaluate {% data variables.product.prodname_copilot_short %}'s response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy, insert, or preview the result of a code block.

   To see the files that {% data variables.copilot.copilot_chat_short %} used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using keywords in your prompt

You can use special keywords to help {% data variables.product.prodname_copilot_short %} understand your prompt.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) or [Slash commands](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#slash-commands>) in the {% data variables.product.prodname_vs %} documentation.

### References

By default, {% data variables.copilot.copilot_chat_short %} will reference the file that you have open or the code that you have selected. You can also use `#` followed by a file name, file name and line numbers, or `solution` to reference a specific file, lines, or solution.

See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=visualstudio#references>) or [Reference](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#reference>) in the {% data variables.product.prodname_vs %} documentation.

## Using {% data variables.product.prodname_dotcom %} skills for {% data variables.product.prodname_copilot_short %} (preview)

> [!NOTE]
> The `@github` chat participant is currently in preview, and only available in [{% data variables.product.prodname_vs %} 2022 Preview 2](<https://visualstudio.microsoft.com/vs/preview/>) onwards.

{% data variables.product.prodname_copilot_short %}'s {% data variables.product.prodname_dotcom %}-specific skills expand the type of information {% data variables.product.prodname_copilot_short %} can provide. To access these skills in {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vs %}, include `@github` in your question.

When you add `@github` to a question, {% data variables.product.prodname_copilot_short %} dynamically selects an appropriate skill, based on the content of your question. You can also explicitly ask {% data variables.copilot.copilot_chat_short %} to use a particular skill. For example, `@github Search the web to find the latest GPT4 model from OpenAI.`

You can generate a list of currently available skills by asking {% data variables.product.prodname_copilot_short %}: `@github What skills are available?`

## Using Model Context Protocol (MCP) servers

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI models for {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.change-the-ai-model %}

## Additional ways to access {% data variables.copilot.copilot_chat_short %}

In addition to submitting prompts through the chat window, you can submit prompts inline. To start an inline chat, right click in your editor window and select **Ask {% data variables.product.prodname_copilot_short %}**.

See [Ask questions in the inline chat view](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat#ask-questions-in-the-inline-chat-view>) in the {% data variables.product.prodname_vs %} documentation for more details.

## Using images in {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.using-images-in-chat %}

### Attaching images to your chat prompt

1. If you see the AI model picker at the bottom right of the chat view, select one of the models that supports adding images to prompts:

1. Do one of the following:

   * Copy an image and paste it into the chat view.
   * Click the paperclip icon at the bottom right of the chat view, click **Upload Image**, browse to the image file you want to attach, select it and click **Open**.

   You can add multiple images if required.

1. Type your prompt into the chat view to accompany the image. For example, `explain this image`, or `describe each of these images in detail`.

## Sharing feedback

To share feedback about {% data variables.copilot.copilot_chat_short %}, you can use the **Send feedback** button in {% data variables.product.prodname_vs %}. For more information on providing feedback for {% data variables.product.prodname_vs %}, see the [{% data variables.product.prodname_vs %} Feedback](<https://learn.microsoft.com/en-us/visualstudio/ide/how-to-report-a-problem-with-visual-studio?view=vs-2022>) documentation.

1. In the top right corner of the {% data variables.product.prodname_vs %} window, click the **Send feedback** button.

    ![Screenshot of the share feedback button in {% data variables.product.prodname_vs %}.](<https://github.com/assets/images/help/copilot/vs-share-feedback-button.png>)

1. Choose the option that best describes your feedback.
    * To report a bug, click **Report a problem**.
    * To request a feature, click **Suggest a feature**.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [Using {% data variables.copilot.copilot_chat %} in {% data variables.product.prodname_vs %} in the Microsoft Learn documentation](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat?view=vs-2022#use-copilot-chat-in-visual-studio>)
* [Tips to improve {% data variables.copilot.copilot_chat %} results in the Microsoft Learn documentation](<https://learn.microsoft.com/en-us/visualstudio/ide/copilot-chat-context?view=vs-2022>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} Trust Center](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endvisualstudio %}

<!-- --------------------- -->
<!-- JetBrains -->
<!-- --------------------- -->

{% jetbrains %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot %}**. {% data reusables.copilot.subscription-prerequisite %}
* **Compatible JetBrains IDE**. {% data variables.product.prodname_copilot %} is compatible with the following IDEs:

  {% data reusables.copilot.jetbrains-compatible-ides %}
{% data reusables.copilot.jetbrains-plugin-prerequisites %}

{% data reusables.copilot.chat-access-denied %}

## Chat modes

The agent picker in the {% data variables.copilot.copilot_chat_short %} panel lets you choose which agent drives your conversation. To switch agents, use the Agents dropdown at the bottom of the chat panel.

The following agents are available:

* **Agent mode** (default): Full agentic experience with autonomous task execution. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* **Ask mode**: Get quick answers and assistance without making code changes.
* **Plan mode**: Collaborate on planning before implementation—{% data variables.product.prodname_copilot_short %} analyzes your request and builds a structured plan for your review. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).
* **Edit mode**: Make controlled edits across multiple files that you review and accept individually.
* **{% data variables.copilot.copilot_cli_short %}**: Runs {% data variables.product.prodname_copilot_short %} through {% data variables.copilot.copilot_cli_short %}, providing a terminal-first agentic experience with support for multiple isolation modes, live session progress, and tool call visibility. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli>).
* **{% data variables.copilot.custom_agents_caps_short %}**: Use personalized agents tailored to your specific needs. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

{% data variables.copilot.copilot_edits_short %} lets you make changes across multiple files directly from a single {% data variables.copilot.copilot_chat_short %} prompt, using edit mode and agent mode.

> [!TIP]
> You can also access {% data variables.product.prodname_copilot_short %} from JetBrains AI Assistant without installing the {% data variables.product.prodname_copilot_short %} plugin. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-in-ides>).

### Using edit mode

{% data reusables.copilot.copilot-edits.edit-mode-description %}

1. To start an edit session, click **{% octicon "copilot" aria-hidden="true" aria-label="copilot" %} {% data variables.product.prodname_copilot_short %}** in the menu bar, then select **Open {% data variables.copilot.copilot_chat %}**.
1. At the top of the chat panel, click **{% data variables.copilot.copilot_edits_short %}**.
1. Add relevant files to the _working set_ to indicate to {% data variables.product.prodname_copilot %} which files you want to work on. You can add all open files by clicking **Add all open files** or individually search for single files.
1. Submit a prompt. In response to your prompt, {% data variables.copilot.copilot_edits_short %} determines which files in your _working set_ to change and adds a short description of the change.
1. Review the changes and **Accept** or **Discard** the edits for each file.

## Submitting prompts

You can ask {% data variables.copilot.copilot_chat_short %} to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. Open the {% data variables.copilot.copilot_chat_short %} window by clicking the **{% data variables.copilot.copilot_chat %}** icon at the right side of the JetBrains IDE window.

   ![Screenshot of the {% data variables.copilot.copilot_chat %} icon in the Activity Bar.](<https://github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

1. Enter a prompt in the prompt box. For example prompts, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

1. Evaluate {% data variables.product.prodname_copilot_short %}'s response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy or insert a code block.

   To see the files that {% data variables.copilot.copilot_chat_short %} used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Supplementing your prompt

You can use slash commands and file references to help {% data variables.product.prodname_copilot_short %} understand your what you are asking it to do.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=jetbrains#slash-commands-2>).

### File references

By default, {% data variables.copilot.copilot_chat_short %} will reference the file that you have open or the code that you have selected. You can also tell {% data variables.copilot.copilot_chat_short %} which files to reference by dragging a file into the chat prompt box. Alternatively, you can right click on a file, select **GitHub Copilot**, then select **Reference File in Chat**.

## Using {% data variables.product.prodname_dotcom %} skills for {% data variables.product.prodname_copilot_short %}

{% data reusables.copilot.using-skills %}

## Using Model Context Protocol (MCP) servers

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI models for {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.change-the-ai-model %}

## Additional ways to access {% data variables.copilot.copilot_chat_short %}

* **Built-in requests**. In addition to submitting prompts through the chat window, you can submit built-in requests by right clicking in a file, selecting **{% data variables.product.prodname_copilot %}**, then selecting one of the options.
* **Inline**. You can submit a chat prompt inline, and scope it to a highlighted code block or your current file.
   * To start an inline chat, right click on a code block or anywhere in your current file, hover over **{% data variables.product.prodname_copilot %}**, then select **{% octicon "plus" aria-hidden="true" aria-label="plus" %} {% data variables.product.prodname_copilot_short %}: Inline Chat**, or enter <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd>.

## Sharing feedback

To share feedback about {% data variables.copilot.copilot_chat_short %}, you can use the **share feedback** link in JetBrains.

1. At the right side of the JetBrains IDE window, click the **{% data variables.copilot.copilot_chat_short %}** icon to open the {% data variables.copilot.copilot_chat_short %} window.

    ![Screenshot of the {% data variables.copilot.copilot_chat_short %} icon in the Activity Bar.](<https://github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

1. At the top of the {% data variables.copilot.copilot_chat_short %} window, click the **share feedback** link.

    ![Screenshot of the share feedback link in the {% data variables.copilot.copilot_chat_short %} window.](<https://github.com/assets/images/help/copilot/jetbrains-share-feedback.png>)

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} Trust Center](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endjetbrains %}

<!-- --------------------- -->
<!-- XCode -->
<!-- --------------------- -->

{% xcode %}

## Prerequisites

* **Access to {% data variables.product.prodname_copilot %}**. {% data reusables.copilot.subscription-prerequisite %}
* **Latest version of the {% data variables.product.prodname_copilot %} extension**. For installation instructions, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>).
* **Sign in to {% data variables.product.company_short %} in Xcode**.

{% data reusables.copilot.chat-access-denied %}

## Chat modes

You can use {% data variables.copilot.copilot_chat_short %} in agent mode to autonomously accomplish a set task, or in plan mode to draft an implementation plan before any code changes are made. To switch between modes, use the agents dropdown at the bottom of the chat window.

* For agent mode, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* For plan mode, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Submitting prompts

You can ask {% data variables.copilot.copilot_chat_short %} to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. To open the chat window, click **Editor** in the menu bar, then click **{% data variables.product.prodname_copilot %}** then **Open Chat**. {% data variables.copilot.copilot_chat_short %} opens in a new window.

1. Enter a prompt in the prompt box. For example prompts, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

1. Evaluate {% data variables.product.prodname_copilot_short %}'s response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy or insert a code block.

   To see the files that {% data variables.copilot.copilot_chat_short %} used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using Model Context Protocol (MCP) servers

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI models for {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.change-the-ai-model %}

## Using keywords in your prompt

You can use special keywords to help {% data variables.product.prodname_copilot_short %} understand your prompt.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. For more information, see [AUTOTITLE](<https://github.com/copilot/reference/chat-cheat-sheet?tool=xcode#slash-commands>).

## File references

By default, {% data variables.copilot.copilot_chat_short %} will reference the file that you have open or the code that you have selected. To attach a specific file as reference, click {% octicon "paperclip" aria-label="Add attachments" %} in the chat prompt box.

## Chat management

You can open a conversation thread for each Xcode IDE to keep discussions organized across different contexts. You can also revisit previous conversations and reference past suggestions through the chat history.

## Sharing feedback

To indicate whether a response was helpful, use {% octicon "thumbsup" aria-label="Thumbs up" %} or {% octicon "thumbsdown" aria-label="Thumbs down" %} that appear next to the response.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} Trust Center](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endxcode %}

<!-- --------------------- -->
<!-- Eclipse -->
<!-- --------------------- -->

{% eclipse %}

## Prerequisites

{% data reusables.copilot.eclipse-prerequisites %}
* **Latest version of the {% data variables.product.prodname_copilot %} extension**. Download this from the [Eclipse Marketplace](<https://aka.ms/copiloteclipse?ref_product=copilot&ref_type=engagement&ref_style=text>). For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>).
* **Sign in to {% data variables.product.company_short %} in Eclipse**.

{% data reusables.copilot.chat-access-denied %}

## Chat modes

You can use {% data variables.copilot.copilot_chat_short %} in agent mode to autonomously accomplish a set task, or in plan mode to draft an implementation plan before any code changes are made. To switch between modes, use the agents dropdown at the bottom of the chat panel.

* For agent mode, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* For plan mode, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Submitting prompts

You can ask {% data variables.copilot.copilot_chat_short %} to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. To open the {% data variables.copilot.copilot_chat_short %} panel, click the {% data variables.product.prodname_copilot_short %} icon ({% octicon "copilot" aria-hidden="true" aria-label="copilot" %}) in the status bar at the bottom of Eclipse, then click **Open Chat**.

1. Enter a prompt in the prompt box, then press <kbd>Enter</kbd>.

   For an introduction to the kinds of prompts you can use, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

1. Evaluate {% data variables.product.prodname_copilot_short %}'s response, and make a follow up request if needed.

## Using keywords in your prompt

You can use special keywords to help {% data variables.product.prodname_copilot_short %} understand your prompt. For examples, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command. For example, use `/explain` to ask {% data variables.product.prodname_copilot_short %} to explain the code in the file currently displayed in the editor.

To see all available slash commands, type `/` in the chat prompt box.

## Using Model Context Protocol (MCP) servers

{% data reusables.copilot.mcp.mcp-chat-in-ide %}

## AI models for {% data variables.copilot.copilot_chat_short %}

{% data reusables.copilot.change-the-ai-model %}

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/prompting/prompt-engineering>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [AUTOTITLE](<https://github.com/copilot/responsible-use/chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [{% data variables.product.prodname_copilot %} Trust Center](<https://copilot.github.trust.page>)
* [{% data variables.product.prodname_copilot %} FAQ](<https://github.com/features/copilot#faq>)

{% endeclipse %}


</details>
