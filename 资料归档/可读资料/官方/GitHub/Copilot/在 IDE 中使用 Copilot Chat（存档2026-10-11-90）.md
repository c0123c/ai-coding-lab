# 在 IDE 中使用 Copilot Chat

介绍提问与 Agent 模式、提示输入、代码上下文、参与者和命令，支持解释代码、生成测试、修复问题和执行分步开发任务。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中使用 Copilot Chat（存档2026-10-11-90）.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在您的 IDE 中询问 GitHub Copilot 问题

使用编辑器中的 Copilot 聊天可为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

## 简介

本指南介绍了如何使用 Copilot 聊天和 agents 通过将编码任务分解为步骤、使用工具读取文件、编辑代码和运行命令以及在出现问题时进行自我纠正来自动执行编码任务。您还可以询问有关软件开发的一般问题，或有关项目中代码的具体问题。有关更多信息，请参阅 [GitHub Copilot 在 IDE 中](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides>).

要了解如何在桌面应用程序中将 Copilot 用于 agent 驱动的工作流程，请参阅 [GitHub Copilot 应用程序入门 ](<https://docs.github.com/en/copilot/get-started/quickstart-copilot-app>)。

<!-- --------------------- -->

<!-- VS Code -->

<!-- --------------------- -->

<div class="ghd-tool vscode">

## 先决条件

* **访问GitHub Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **Visual Studio Code**的最新版本。请参阅[Visual Studio Code下载页面](<https://code.visualstudio.com/Download?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。
* **登录Visual Studio Code**中的GitHub。如果您遇到身份验证问题，请参阅 [对 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio-code>) 的常见问题进行故障排除。

如果您可以通过您的组织访问 GitHub Copilot，并且您的组织所有者已禁用聊天功能，则您将无法使用 GitHub Copilot 聊天。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 聊天模式

您可以通过以下模式使用Copilot聊天：

* **提问模式**：获取编码问题的答案并让Copilot提供代码建议。
* **Agent模式**：让Copilot自主完成设定的任务。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)中使用agent模式。
* **计划模式**：让Copilot创建详细的实施计划，以确保满足所有要求。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。

要在模式之间切换，请使用聊天视图底部的 agents 下拉菜单。

> \[!NOTE]
> 如果您在模式选择器中没有看到 **Agent** 选项，则您的企业或组织管理员可能已为您的 IDE 禁用了 agent 模式。

### 使用询问模式

提问模式针对回答有关代码库、编码和一般技术概念的问题进行了优化。当您想要了解某事如何工作、探索想法或获得编码任务的帮助时，请使用询问模式。

要使用询问模式，请从聊天视图底部的 agents 下拉列表中选择 **询问 **，然后按如下所述提交提示。

## 提交提示

您可以向 agent 提供您想要构建的内容的高级描述，然后它就会开始工作。每个任务都在 agent 会话中运行，这是一个您可以跟踪、暂停、恢复或移交给另一个 agent 的持续对话。

1. 要打开聊天视图，请单击Visual Studio Code标题栏中的聊天图标。如果未显示聊天图标，请右键单击标题栏并确保选择**命令中心**。

![“Copilot聊天”按钮的屏幕截图，以深橙色轮廓突出显示。](<https://docs.github.com/assets/images/help/copilot/vsc-copilot-chat-icon.png>)

2. 在提示框中输入提示。有关可以使用的提示类型的介绍，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

3. 评估 Copilot 的响应，并在需要时提出后续请求。

响应可能包含文本、代码块、按钮、图像、URI 和文件树。响应通常包括交互元素。例如，响应可能包括用于插入代码块的菜单，或用于调用 Visual Studio Code 命令的按钮。

要查看 Copilot Chat 用于生成响应的文件，请选择响应顶部的 ** 使用的 *n* 引用 ** 下拉列表。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

## 在提示中使用关键字

您可以使用特殊关键字来帮助Copilot理解您的提示。例如，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

### 聊天参与者

聊天参与者就像领域专家，他们拥有可以为您提供帮助的专业知识。

Copilot 聊天可以根据您的自然语言提示推断相关的聊天参与者，从而改进高级功能的发现，而无需您在提示中明确指定要使用的参与者。

> \[！NOTE] 聊天参与者的自动推理目前处于公共预览版，可能会发生变化。

或者，您可以手动指定聊天参与者，将提示范围限定到特定域。为此，请在聊天提示框中键入 `@`，后跟聊天参与者姓名。

要获取可用聊天参与者的列表，请在聊天提示框中键入 `@`。另请参阅 Visual Studio Code 文档中的 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#chat-participants>) 或 [ 聊天参与者 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-participants>)。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参阅 Visual Studio Code 文档中的 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) 或 [ 斜线命令 ](<https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_slash-commands>)。

### 聊天变量

使用聊天变量在提示中包含特定上下文。要使用聊天变量，请在聊天提示框中键入 `#`，然后键入聊天变量。

要查看所有可用的聊天变量，请在聊天提示框中键入 `#`。另请参阅 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#chat-variables>)。

## 使用GitHub技能Copilot

Copilot的GitHub特定技能扩展了Copilot可以提供的信息类型。要在 Copilot 聊天中访问这些技能，请在您的问题中包含 `@github`。

当您将 `@github` 添加到问题时，Copilot 会根据您的问题内容动态选择适当的技能。您还可以明确要求 Copilot 聊天使用特定技能。您可以通过两种方式执行此操作：

* 使用自然语言询问Copilot聊天使用技能。例如，`@github Search the web to find the latest GPT model from OpenAI.`
* 要专门调用网络搜索，您可以在问题中包含 `#web` 变量。例如，`@github #web What is the latest LTS of Node.js?`

您可以通过询问 Copilot 生成当前可用技能的列表：`@github What skills are available?`

## 使用 Model Context Protocol (MCP) 服务器

您可以使用 MCP 通过将 Copilot Chat 与各种现有工具和服务集成来扩展其功能。有关更多信息，请参阅 [关于 Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>)。

## AI 模型 Copilot 聊天

您可以更改 Copilot 用于生成响应的模型。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您提出的问题类型。选项包括具有先进功能的高级型号。要更改 IDE 中的模型，请参阅 [更改 GitHub Copilot 聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>) 的 AI 模型。要更改或比较 GitHub 上的模型，请参阅 [在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>) 中询问 GitHub Copilot 问题。

## 访问 Copilot 聊天的其他方式

除了通过聊天视图提交提示外，您还可以通过其他方式提交提示：

* **快速聊天：** 要打开快速聊天下拉列表，请输入<kbd>Shift</kbd>+<kbd>选项</kbd>+<kbd>命令</kbd>+<kbd>L</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>L</kbd> （Windows/Linux）。
* **内联：** 要直接在编辑器或集成终端中启动内联聊天，请输入 <kbd>Command</kbd>+<kbd>i</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>i</kbd>（Windows/Linux）。
* **智能操作：** 要通过上下文菜单提交提示，请右键单击编辑器，在出现的菜单中选择 **Copilot**，然后选择其中一项操作。智能操作也可以通过闪烁图标来访问，当您选择一行代码时，有时会出现该图标。

有关更多详细信息，请参阅 Visual Studio Code 文档中的 [ 内联聊天 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_inline-chat>)、[ 快速聊天 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_quick-chat>) 和 [ 聊天智能操作 ](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-smart-actions>)。

## 在 Copilot 聊天中使用图像

使用支持图像输入的型号时，您可以将图像和 PDF 附加到提示中。

Copilot支持以下文件类型：

* JPEG (`.jpg`, `.jpeg`)
* PNG (`.png`)
* GIF (`.gif`)
* WEBP (`.webp`)
* PDF (`.pdf`)
* HEIC (`.heic`)
* HEIF (`.heif`)

例如，您可以附加：

* 代码片段的屏幕截图并要求Copilot解释该代码。
* 应用程序的用户界面模型，并要求 Copilot 生成代码。
* 流程图并要求 Copilot 描述图中所示的过程。
* 网页截图并要求Copilot生成类似页面的HTML。

图像和 PDF 附件在所有 Copilot 计划中均可用，并且默认启用，无需任何策略即可打开或关闭该功能。

### 将图像附加到聊天提示中

1. 执行以下操作之一：

   * 复制图像并将其粘贴到聊天视图中。
   * 将一个或多个图像文件从操作系统的文件资源管理器（或从 VS Code 中的资源管理器）拖放到聊天视图中。
   * 右键单击 VS Code 资源管理器中的图像文件，然后单击 **Copilot**，然后单击 ** 将文件添加到聊天 **。

2. 在聊天视图中输入您的提示以配合图像。例如，`explain this diagram`、`describe each of these images in detail`、`what does this error message mean`。

## 分享反馈

要指示响应是否有帮助，请使用响应旁边显示的“竖起大拇指”和“竖起大拇指”图标。

要留下有关 GitHub Copilot 聊天扩展的反馈，请在 [microsoft/vscode-copilot-release](<https://github.com/microsoft/vscode-copilot-release/issues>) 存储库中打开问题。

## 进一步阅读

* [GitHub Copilot聊天](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)的提示工程
* [使用 Copilot 在 Visual Studio Code 文档中的 VS Code](<https://code.visualstudio.com/docs/copilot/copilot-chat>) 中聊天
* [管理 agent 会话](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [在GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)中询问GitHub Copilot问题
* [申请卡：GitHub Copilot聊天](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub 其他产品和功能的条款](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- Visual Studio -->

<!-- --------------------- -->

<div class="ghd-tool visualstudio">

## 先决条件

* **访问GitHub Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **Visual Studio 2022 版本 17.8 或更高版本 **。请参阅 Visual Studio 文档中的[安装 Visual Studio](<https://learn.microsoft.com/visualstudio/install/install-visual-studio>)。

  * *对于 Visual Studio 17.8 和 17.9：*
    * **GitHub Copilot扩展名**。请参阅 Visual Studio 文档中的 [在 Visual Studio 中安装 GitHub Copilot](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。
    * **GitHub Copilot 聊天扩展**。请参阅 Visual Studio 文档中的 [在 Visual Studio 中安装 GitHub Copilot](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。

*Visual Studio 17.10 及更高版本内置了 GitHub Copilot 和 GitHub Copilot 聊天扩展。您无需单独安装它们。*
* **在 Visual Studio** 中登录 GitHub。如果您遇到身份验证问题，请参阅 [对 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio>) 的常见问题进行故障排除。

如果您可以通过您的组织访问 GitHub Copilot，并且您的组织所有者已禁用聊天功能，则您将无法使用 GitHub Copilot 聊天。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 聊天模式

Visual Studio 支持以下聊天模式：

* **询问模式**：无需更改代码即可获得答案和指导。
* **Agent 模式**：为Copilot 提供高级任务并允许其编辑代码、运行命令和迭代结果。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)中使用agent模式。

要在模式之间切换，请使用聊天面板底部的模式下拉菜单。聊天模式在 Visual Studio 17.14 及更高版本中可用。

### 使用询问模式

当您希望 Copilot 解释代码、回答问题或建议方法而不修改项目时，请使用询问模式。

要使用询问模式，请从聊天面板底部的模式下拉列表中选择 **询问**，然后按如下所述提交提示。

## 提交提示

您可以要求 Copilot Chat 为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 在 Visual Studio 菜单栏中，单击“**查看**”，然后单击“**GitHub Copilot聊天**”。
2. 在 Copilot 聊天窗口中，输入提示，然后按 **Enter**。有关提示示例，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。
3. 评估 Copilot 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制、插入或预览代码块结果的按钮。

要查看 Copilot Chat 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

## 在提示中使用关键字

您可以使用特殊关键字来帮助Copilot理解您的提示。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参阅 Visual Studio 文档中的 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) 或 [ 斜线命令 ](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#slash-commands>)。

### 参考文献

默认情况下，Copilot 聊天将引用您已打开的文件或您已选择的代码。您还可以使用 `#` 后跟文件名、文件名和行号，或使用 `solution` 来引用特定文件、行或解决方案。

另请参阅 Visual Studio 文档中的 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=visualstudio#references>) 或 [ 参考 ](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#reference>)。

## 将GitHub技能用于Copilot（预览）

> \[！NOTE]
> `@github` 聊天参与者当前处于预览状态，并且仅在 [Visual Studio 2022 预览版 2](<https://visualstudio.microsoft.com/vs/preview/>) 中可用。

Copilot的GitHub特定技能扩展了Copilot可以提供的信息类型。要在 Visual Studio 中的 Copilot 聊天中访问这些技能，请在问题中包含 `@github`。

当您将 `@github` 添加到问题时，Copilot 会根据您的问题内容动态选择适当的技能。您还可以明确要求 Copilot 聊天使用特定技能。例如，`@github Search the web to find the latest GPT4 model from OpenAI.`

您可以通过询问 Copilot 生成当前可用技能的列表：`@github What skills are available?`

## 使用 Model Context Protocol (MCP) 服务器

您可以使用 MCP 通过将 Copilot Chat 与各种现有工具和服务集成来扩展其功能。有关更多信息，请参阅 [关于 Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>)。

## AI 模型 Copilot 聊天

您可以更改 Copilot 用于生成响应的模型。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您提出的问题类型。选项包括具有先进功能的高级型号。要更改 IDE 中的模型，请参阅 [更改 GitHub Copilot 聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>) 的 AI 模型。要更改或比较 GitHub 上的模型，请参阅 [在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>) 中询问 GitHub Copilot 问题。

## 访问 Copilot 聊天的其他方式

除了通过聊天窗口提交提示之外，您还可以内联提交提示。要开始内联聊天，请右键单击编辑器窗口并选择 **询问 Copilot**。

有关更多详细信息，请参阅 Visual Studio 文档中的 [在内联聊天视图中提问 ](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat#ask-questions-in-the-inline-chat-view>)。

## 在 Copilot 聊天中使用图像

使用支持图像输入的型号时，您可以将图像和 PDF 附加到提示中。

Copilot支持以下文件类型：

* JPEG (`.jpg`, `.jpeg`)
* PNG (`.png`)
* GIF (`.gif`)
* WEBP (`.webp`)
* PDF (`.pdf`)
* HEIC (`.heic`)
* HEIF (`.heif`)

例如，您可以附加：

* 代码片段的屏幕截图并要求Copilot解释该代码。
* 应用程序的用户界面模型，并要求 Copilot 生成代码。
* 流程图并要求 Copilot 描述图中所示的过程。
* 网页截图并要求Copilot生成类似页面的HTML。

图像和 PDF 附件在所有 Copilot 计划中均可用，并且默认启用，无需任何策略即可打开或关闭该功能。

### 将图像附加到聊天提示中

1. 如果您在聊天视图右下角看到 AI 模型选择器，请选择支持向提示添加图像的模型之一：

2. 执行以下操作之一：

   * 复制图像并将其粘贴到聊天视图中。
   * 单击聊天视图右下角的回形针图标，单击**上传图片**，浏览到要附加的图片文件，选择它并单击**打开**。

如果需要，您可以添加多个图像。

3. 在聊天视图中输入您的提示以配合图像。例如，`explain this image` 或 `describe each of these images in detail`。

## 分享反馈

要分享有关 Copilot 聊天的反馈，您可以使用 Visual Studio 中的**发送反馈** 按钮。有关为 Visual Studio 提供反馈的详细信息，请参阅 [Visual Studio 反馈](<https://learn.microsoft.com/en-us/visualstudio/ide/how-to-report-a-problem-with-visual-studio?view=vs-2022>) 文档。

1. 在 Visual Studio 窗口的右上角，单击“**发送反馈**”按钮。

![Visual Studio 中共享反馈按钮的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/vs-share-feedback-button.png>)

2. 选择最能描述您的反馈的选项。
   * 要报告错误，请单击**报告问题**。
   * 要请求功能，请单击**建议功能**。

## 进一步阅读

* [GitHub Copilot聊天](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)的提示工程
* [在 Microsoft 学习文档 ](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat?view=vs-2022#use-copilot-chat-in-visual-studio>) 中使用 GitHub Copilot 在 Visual Studio 中聊天
* [改进 GitHub Copilot Microsoft Learn 文档中的聊天结果的提示 ](<https://learn.microsoft.com/en-us/visualstudio/ide/copilot-chat-context?view=vs-2022>)
* [在GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)中询问GitHub Copilot问题
* [申请卡：GitHub Copilot聊天](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub 其他产品和功能的条款](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- JetBrains -->

<!-- --------------------- -->

<div class="ghd-tool jetbrains">

## 先决条件

* **访问GitHub Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **兼容JetBrainsIDE**。 GitHub Copilot 与以下 IDE 兼容：

  * IntelliJ IDEA（终极版、社区版、教育版）
  * 安卓工作室
  * 克利翁
  * 与我一起编码嘉宾
  * DataGrip
  * DataSpell
  * GoLand
  * JetBrains 客户端
  * MPS
  * PhpStorm
  * PyCharm（专业、社区、教育）
  * 骑手
  * RubyMine
  * RustRover
  * WebStorm

请参阅 [JetBrains IDE](<https://www.jetbrains.com/products/?ref_product=copilot\&ref_type=engagement\&ref_style=button>) 工具查找器进行下载。
* **GitHub Copilot扩展**的最新版本。请参阅 JetBrains 市场中的 [GitHub Copilot 插件 ](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。有关安装说明，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>)。
* **在您的JetBrainsIDE**中登录GitHub。有关身份验证说明，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains#installing-the-github-copilot-plugin-in-your-jetbrains-ide>)。

如果您可以通过您的组织访问 GitHub Copilot，并且您的组织所有者已禁用聊天功能，则您将无法使用 GitHub Copilot 聊天。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 聊天模式

Copilot 聊天面板中的 agent 选择器可让您选择哪个 agent 驱动您的对话。要切换 agents，请使用聊天面板底部的 Agents 下拉菜单。

以下 agents 可用：

* **Agent 模式**（默认）：自主任务执行的完整代理体验。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)中使用agent模式。
* **询问模式**：无需更改代码即可快速获得答案和帮助。
* **计划模式**：在实施之前协作制定计划 - Copilot分析您的请求并构建结构化计划供您审核。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。
* **编辑模式**：对您单独查看和接受的多个文件进行受控编辑。
* **Copilot CLI**：通过 Copilot CLI 运行 Copilot，提供终端优先的代理体验，支持多种隔离模式、实时会话进度和工具调用可见性。有关详细信息，请参阅[GitHub Copilot CLI](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-cli>)。
* **自定义agents**：使用根据您的特定需求量身定制的个性化agents。请参阅[在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>)中使用自定义agents。

Copilot 编辑可让您使用编辑模式和 agent 模式直接从单个 Copilot 聊天提示对多个文件进行更改。

> \[!TIP]
> 您还可以访问 Copilot 来自 JetBrains AI 无需安装助手 Copilot 插件。有关更多信息，请参阅 [GitHub Copilot 在 IDE 中](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides>).

### 使用编辑模式

编辑模式仅在 Visual Studio Code 和 JetBrains IDE 中可用。

当您想要更精细地控制 Copilot 建议的编辑时，请使用编辑模式。在编辑模式下，您可以选择 Copilot 可以更改哪些文件，在每次迭代时为 Copilot 提供上下文，并决定是否在每次迭代后接受建议的编辑。

编辑模式最适合以下用例：

* 您想要对一组定义的文件进行快速、特定的更新。
* 您希望完全控制 LLM 请求 Copilot 使用的数量。

1. 要启动编辑会话，请单击菜单栏中的**<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> Copilot**，然后选择**打开GitHub Copilot聊天**。
2. 在聊天面板顶部，单击**Copilot编辑**。
3. 将相关文件添加到*工作集*中，以向GitHub Copilot指示您要处理哪些文件。您可以通过单击**添加所有打开的文件**来添加所有打开的文件或单独搜索单个文件。
4. 提交提示。为了响应您的提示，Copilot 编辑确定要更改的 * 工作集 * 中的哪些文件，并添加更改的简短描述。
5. 查看更改并**接受**或**放弃**对每个文件的编辑。

## 提交提示

您可以要求 Copilot Chat 为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 单击 JetBrains IDE 窗口右侧的 **GitHub Copilot 聊天 ** 图标，打开 Copilot 聊天窗口。

![活动栏中 GitHub Copilot 聊天图标的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

2. 在提示框中输入提示。有关提示示例，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

3. 评估 Copilot 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制或插入代码块的按钮。

要查看 Copilot Chat 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

## 补充您的提示

您可以使用斜杠命令和文件引用来帮助 Copilot 了解您要求它执行的操作。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。另请参阅 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=jetbrains#slash-commands-2>)。

### 文件参考

默认情况下，Copilot 聊天将引用您已打开的文件或您已选择的代码。您还可以通过将文件拖到聊天提示框中来告诉Copilot Chat要引用哪些文件。或者，您可以右键单击文件，选择**GitHub Copilot**，然后选择**聊天中的参考文件**。

## 使用GitHub技能Copilot

Copilot的GitHub特定技能扩展了Copilot可以提供的信息类型。要在 Copilot 聊天中访问这些技能，请在您的问题中包含 `@github`。

当您将 `@github` 添加到问题时，Copilot 会根据您的问题内容动态选择适当的技能。您还可以明确要求 Copilot 聊天使用特定技能。您可以通过两种方式执行此操作：

* 使用自然语言询问Copilot聊天使用技能。例如，`@github Search the web to find the latest GPT model from OpenAI.`
* 要专门调用网络搜索，您可以在问题中包含 `#web` 变量。例如，`@github #web What is the latest LTS of Node.js?`

您可以通过询问 Copilot 生成当前可用技能的列表：`@github What skills are available?`

## 使用 Model Context Protocol (MCP) 服务器

您可以使用 MCP 通过将 Copilot Chat 与各种现有工具和服务集成来扩展其功能。有关更多信息，请参阅 [关于 Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>)。

## AI 模型 Copilot 聊天

您可以更改 Copilot 用于生成响应的模型。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您提出的问题类型。选项包括具有先进功能的高级型号。要更改 IDE 中的模型，请参阅 [更改 GitHub Copilot 聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>) 的 AI 模型。要更改或比较 GitHub 上的模型，请参阅 [在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>) 中询问 GitHub Copilot 问题。

## 访问 Copilot 聊天的其他方式

* **内置请求**。除了通过聊天窗口提交提示之外，您还可以通过右键单击文件，选择 **GitHub Copilot**，然后选择选项之一来提交内置请求。
* **内联**。您可以内联提交聊天提示，并将其范围限制为突出显示的代码块或当前文件。
  * 要开始内联聊天，请右键单击代码块或当前文件中的任意位置，将鼠标悬停在 **GitHub Copilot** 上，然后选择 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="plus" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> Copilot：内联聊天**，或输入<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd>。

## 分享反馈

要分享有关 Copilot 聊天的反馈，您可以使用 JetBrains 中的 ** 分享反馈 ** 链接。

1. 在JetBrainsIDE窗口右侧，点击**Copilot聊天**图标，打开Copilot聊天窗口。

![活动栏中 Copilot 聊天图标的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

2. 在 Copilot 聊天窗口顶部，点击 ** 分享反馈 ** 链接。

![Copilot聊天窗口中分享反馈链接的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/jetbrains-share-feedback.png>)

## 进一步阅读

* [GitHub Copilot聊天](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)的提示工程
* [在GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)中询问GitHub Copilot问题
* [申请卡：GitHub Copilot聊天](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub 预发布许可条款](<https://docs.github.com/en/site-policy/github-terms/github-pre-release-license-terms>)
* [GitHub 其他产品和功能的条款](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- XCode -->

<!-- --------------------- -->

<div class="ghd-tool xcode">

## 先决条件

* **访问GitHub Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **GitHub Copilot扩展**的最新版本。有关安装说明，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>)。
* **在 Xcode** 中登录 GitHub。

如果您可以通过您的组织访问 GitHub Copilot，并且您的组织所有者已禁用聊天功能，则您将无法使用 GitHub Copilot 聊天。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 聊天模式

您可以在agent模式下使用Copilot聊天自主完成设定的任务，或者在计划模式下在进行任何代码更改之前起草实施计划。要在模式之间切换，请使用聊天窗口底部的 agents 下拉菜单。

* 对于agent模式，请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)中使用agent模式。
* 有关计划模式，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>) 中使用计划模式。

## 提交提示

您可以要求 Copilot Chat 为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 要打开聊天窗口，请单击菜单栏中的**编辑器**，然后单击**GitHub Copilot**，然后单击**打开聊天**。 Copilot 聊天在新窗口中打开。

2. 在提示框中输入提示。有关提示示例，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

3. 评估 Copilot 的回复，并在需要时提交后续提示。

响应通常包括交互元素。例如，响应可能包括用于复制或插入代码块的按钮。

要查看 Copilot Chat 用于生成响应的文件，请单击响应下方的 ** 参考文献 ** 链接。这些参考可能包括指向您的存储库的自定义说明文件的链接。此文件包含自动添加到您的所有聊天问题中的附加信息，以提高答复的质量。有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

## 使用 Model Context Protocol (MCP) 服务器

您可以使用 MCP 通过将 Copilot Chat 与各种现有工具和服务集成来扩展其功能。有关更多信息，请参阅 [关于 Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>)。

## AI 模型 Copilot 聊天

您可以更改 Copilot 用于生成响应的模型。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您提出的问题类型。选项包括具有先进功能的高级型号。要更改 IDE 中的模型，请参阅 [更改 GitHub Copilot 聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>) 的 AI 模型。要更改或比较 GitHub 上的模型，请参阅 [在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>) 中询问 GitHub Copilot 问题。

## 在提示中使用关键字

您可以使用特殊关键字来帮助Copilot理解您的提示。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。有关更多信息，请参阅 [GitHub Copilot 聊天备忘单 ](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=xcode#slash-commands>)。

## 文件参考

默认情况下，Copilot 聊天将引用您已打开的文件或您已选择的代码。要附加特定文件作为参考，请在聊天提示框中单击<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-paperclip" aria-label="Add attachments" role="img"><path d="M12.212 3.02a1.753 1.753 0 0 0-2.478.003l-5.83 5.83a3.007 3.007 0 0 0-.88 2.127c0 .795.315 1.551.88 2.116.567.567 1.333.89 2.126.89.79 0 1.548-.321 2.116-.89l5.48-5.48a.75.75 0 0 1 1.061 1.06l-5.48 5.48a4.492 4.492 0 0 1-3.177 1.33c-1.2 0-2.345-.487-3.187-1.33a4.483 4.483 0 0 1-1.32-3.177c0-1.195.475-2.341 1.32-3.186l5.83-5.83a3.25 3.25 0 0 1 5.553 2.297c0 .863-.343 1.691-.953 2.301L7.439 12.39c-.375.377-.884.59-1.416.593a1.998 1.998 0 0 1-1.412-.593 1.992 1.992 0 0 1 0-2.828l5.48-5.48a.751.751 0 0 1 1.042.018.751.751 0 0 1 .018 1.042l-5.48 5.48a.492.492 0 0 0 0 .707.499.499 0 0 0 .352.154.51.51 0 0 0 .356-.154l5.833-5.827a1.755 1.755 0 0 0 0-2.481Z"></path></svg>。

## 聊天管理

您可以为每个 Xcode IDE 打开一个对话线程，以在不同的上下文中组织讨论。您还可以通过聊天记录重新访问之前的对话并参考过去的建议。

## 分享反馈

要指示回复是否有帮助，请使用回复旁边显示的 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-thumbsup" aria-label="Thumbs up" role="img"><path d="M8.347.631A.75.75 0 0 1 9.123.26l.238.04a3.25 3.25 0 0 1 2.591 4.098L11.494 6h.665a3.25 3.25 0 0 1 3.118 4.167l-1.135 3.859A2.751 2.751 0 0 1 11.503 16H6.586a3.75 3.75 0 0 1-2.184-.702A1.75 1.75 0 0 1 3 16H1.75A1.75 1.75 0 0 1 0 14.25v-6.5C0 6.784.784 6 1.75 6h3.417a.25.25 0 0 0 .217-.127ZM4.75 13.649l.396.33c.404.337.914.521 1.44.521h4.917a1.25 1.25 0 0 0 1.2-.897l1.135-3.859A1.75 1.75 0 0 0 12.159 7.5H10.5a.75.75 0 0 1-.721-.956l.731-2.558a1.75 1.75 0 0 0-1.127-2.14L6.69 6.611a1.75 1.75 0 0 1-1.523.889H4.75ZM3.25 7.5h-1.5a.25.25 0 0 0-.25.25v6.5c0 .138.112.25.25.25H3a.25.25 0 0 0 .25-.25Z"></path></svg> 或 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-thumbsdown" aria-label="Thumbs down" role="img"><path d="M7.653 15.369a.75.75 0 0 1-.776.371l-.238-.04a3.25 3.25 0 0 1-2.591-4.099L4.506 10h-.665A3.25 3.25 0 0 1 .723 5.833l1.135-3.859A2.75 2.75 0 0 1 4.482 0H9.43c.78.003 1.538.25 2.168.702A1.752 1.752 0 0 1 12.989 0h1.272A1.75 1.75 0 0 1 16 1.75v6.5A1.75 1.75 0 0 1 14.25 10h-3.417a.25.25 0 0 0-.217.127ZM11.25 2.351l-.396-.33a2.248 2.248 0 0 0-1.44-.521H4.496a1.25 1.25 0 0 0-1.199.897L2.162 6.256A1.75 1.75 0 0 0 3.841 8.5H5.5a.75.75 0 0 1 .721.956l-.731 2.558a1.75 1.75 0 0 0 1.127 2.14L9.31 9.389a1.75 1.75 0 0 1 1.523-.889h.417Zm1.5 6.149h1.5a.25.25 0 0 0 .25-.25v-6.5a.25.25 0 0 0-.25-.25H13a.25.25 0 0 0-.25.25Z"></path></svg>。

## 进一步阅读

* [GitHub Copilot聊天](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)的提示工程
* [在GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)中询问GitHub Copilot问题
* [申请卡：GitHub Copilot聊天](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub 预发布许可条款](<https://docs.github.com/en/site-policy/github-terms/github-pre-release-license-terms>)
* [GitHub 其他产品和功能的条款](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- Eclipse -->

<!-- --------------------- -->

<div class="ghd-tool eclipse">

## 先决条件

* **访问Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **Eclipse 的兼容版本**。要使用 GitHub Copilot 扩展，您必须具有 Eclipse 版本 2024-09 或更高版本。请参阅 [Eclipse 下载页面 ](<https://www.eclipse.org/downloads/packages/>)。
* 如果您是拥有 Copilot 商业版或 Copilot 企业版计划的组织或企业的成员，则必须启用“Copilot 中的 MCP 服务器”策略，才能将 MCP 与 Copilot 结合使用。
* **GitHub Copilot扩展**的最新版本。从 [Eclipse Marketplace ](<https://aka.ms/copiloteclipse?ref_product=copilot\&ref_type=engagement\&ref_style=text>) 下载此文件。有关更多信息，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>)。
* **在 Eclipse** 中登录 GitHub。

如果您可以通过您的组织访问 GitHub Copilot，并且您的组织所有者已禁用聊天功能，则您将无法使用 GitHub Copilot 聊天。请参阅 [管理组织中 GitHub Copilot 的策略和功能 ](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>)。

## 聊天模式

您可以在agent模式下使用Copilot聊天自主完成设定的任务，或者在计划模式下在进行任何代码更改之前起草实施计划。要在模式之间切换，请使用聊天面板底部的 agents 下拉菜单。

* 对于agent模式，请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>)中使用agent模式。
* 有关计划模式，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>) 中使用计划模式。

## 提交提示

您可以要求 Copilot Chat 为您提供代码建议、解释代码、生成单元测试以及建议代码修复。

1. 要打开 Copilot 聊天面板，请单击 Eclipse 底部状态栏中的 Copilot 图标 (<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg>)，然后单击 **打开聊天**。

2. 在提示框中输入提示，然后按<kbd>输入</kbd>。

有关可以使用的提示类型的介绍，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

3. 评估 Copilot 的响应，并在需要时提出后续请求。

## 在提示中使用关键字

您可以使用特殊关键字来帮助Copilot理解您的提示。例如，请参阅 [开始使用 GitHub Copilot 在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>) 中聊天的提示。

### 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，然后键入命令。例如，使用`/explain`要求Copilot解释编辑器中当前显示的文件中的代码。

要查看所有可用的斜线命令，请在聊天提示框中键入 `/`。

## 使用 Model Context Protocol (MCP) 服务器

您可以使用 MCP 通过将 Copilot Chat 与各种现有工具和服务集成来扩展其功能。有关更多信息，请参阅 [关于 Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>)。

## AI 模型 Copilot 聊天

您可以更改 Copilot 用于生成响应的模型。您可能会发现不同的模型表现更好，或者提供更有用的响应，具体取决于您提出的问题类型。选项包括具有先进功能的高级型号。要更改 IDE 中的模型，请参阅 [更改 GitHub Copilot 聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>) 的 AI 模型。要更改或比较 GitHub 上的模型，请参阅 [在 GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>) 中询问 GitHub Copilot 问题。

## 进一步阅读

* [GitHub Copilot聊天](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)的提示工程
* [在GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)中询问GitHub Copilot问题
* [申请卡：GitHub Copilot聊天](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub 其他产品和功能的条款](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Asking GitHub Copilot questions in your IDE

Use Copilot Chat in your editor to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

## Introduction

This guide describes how to use Copilot Chat and agents to automate coding tasks by breaking them into steps, using tools to read files, edit code, and run commands, and self-correcting when something goes wrong. You can also ask general questions about software development, or specific questions about the code in your project. For more information, see [GitHub Copilot in IDEs](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides>).

To learn how to use Copilot for agent-driven workflows in a desktop app, see [Getting started with the GitHub Copilot app](<https://docs.github.com/en/copilot/get-started/quickstart-copilot-app>).

<!-- --------------------- -->

<!-- VS Code -->

<!-- --------------------- -->

<div class="ghd-tool vscode">

## Prerequisites

* **Access to GitHub Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **Latest version of Visual Studio Code**. See the [Visual Studio Code download page](<https://code.visualstudio.com/Download?ref_product=copilot\&ref_type=engagement\&ref_style=text>).
* **Sign in to GitHub in Visual Studio Code**. If you experience authentication issues, see [Troubleshooting common issues with GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio-code>).

If you have access to GitHub Copilot via your organization, you won't be able to use GitHub Copilot Chat if your organization owner has disabled chat. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Chat modes

You can use Copilot Chat in the following modes:

* **Ask mode**: to get answers to coding questions and get Copilot to provide code suggestions.
* **Agent mode**: to get Copilot to autonomously accomplish a set task. See [Using agent mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* **Plan mode**: to get Copilot to create detailed implementation plans to ensure all requirements are met. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

To switch between modes, use the agents dropdown at the bottom of the chat view.

> \[!NOTE]
> If you don’t see the **Agent** option in the mode selector, your enterprise or organization administrator may have disabled agent mode for your IDE.

### Using ask mode

Ask mode is optimized for answering questions about your codebase, coding, and general technology concepts. Use ask mode when you want to understand how something works, explore ideas, or get help with coding tasks.

To use ask mode, select **Ask** from the agents dropdown at the bottom of the chat view, then submit a prompt as described below.

## Submitting prompts

You can give the agent a high-level description of what you want to build and it gets to work. Each task runs inside an agent session, a persistent conversation you can track, pause, resume, or hand off to another agent.

1. To open the chat view, click the chat icon in the title bar of Visual Studio Code. If the chat icon is not displayed, right-click the title bar and make sure that **Command Center** is selected.

   ![Screenshot of the 'Copilot Chat' button, highlighted with a dark orange outline.](<https://docs.github.com/assets/images/help/copilot/vsc-copilot-chat-icon.png>)

2. Enter a prompt in the prompt box. For an introduction to the kinds of prompts you can use, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

3. Evaluate Copilot's response, and make a follow-up request if needed.

   The response may contain text, code blocks, buttons, images, URIs, and file trees. The response often includes interactive elements. For example, the response may include a menu to insert a code block, or a button to invoke a Visual Studio Code command.

   To see the files that Copilot Chat used to generate the response, select the **Used *n* references** dropdown at the top of the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using keywords in your prompt

You can use special keywords to help Copilot understand your prompt. For examples, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

### Chat participants

Chat participants are like domain experts who have a specialty that they can help you with.

Copilot Chat can infer relevant chat participants based on your natural language prompt, improving discovery of advanced capabilities without you having to explicitly specify the participant you want to use in your prompt.

> \[!NOTE] Automatic inference for chat participants is currently in public preview and is subject to change.

Alternatively, you can manually specify a chat participant to scope your prompt to a specific domain. To do this, type `@` in the chat prompt box, followed by a chat participant name.

For a list of available chat participants, type `@` in the chat prompt box. See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#chat-participants>) or [Chat participants](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-participants>) in the Visual Studio Code documentation.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) or [Slash commands](<https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_slash-commands>) in the Visual Studio Code documentation.

### Chat variables

Use chat variables to include specific context in your prompt. To use a chat variable, type `#` in the chat prompt box, followed by a chat variable.

To see all available chat variables, type `#` in the chat prompt box. See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#chat-variables>).

## Using GitHub skills for Copilot

Copilot's GitHub-specific skills expand the type of information Copilot can provide. To access these skills in Copilot Chat, include `@github` in your question.

When you add `@github` to a question, Copilot dynamically selects an appropriate skill, based on the content of your question. You can also explicitly ask Copilot Chat to use a particular skill. You can do this in two ways:

* Use natural language to ask Copilot Chat to use a skill. For example, `@github Search the web to find the latest GPT model from OpenAI.`
* To specifically invoke a web search you can include the `#web` variable in your question. For example, `@github #web What is the latest LTS of Node.js?`

You can generate a list of currently available skills by asking Copilot: `@github What skills are available?`

## Using Model Context Protocol (MCP) servers

You can use MCP to extend the capabilities of Copilot Chat by integrating it with a wide range of existing tools and services. For additional information, see [About Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>).

## AI models for Copilot Chat

You can change the model Copilot uses to generate responses. You may find that different models perform better, or provide more useful responses, depending on the type of questions you ask. Options include premium models with advanced capabilities. To change the model in your IDE, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>). To change or compare models on GitHub, see [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>).

## Additional ways to access Copilot Chat

In addition to submitting prompts through the chat view, you can submit prompts in other ways:

* **Quick chat:** To open the quick chat dropdown, enter <kbd>Shift</kbd>+<kbd>Option</kbd>+<kbd>Command</kbd>+<kbd>L</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>L</kbd> (Windows/Linux).
* **Inline:** To start an inline chat directly in the editor or integrated terminal, enter <kbd>Command</kbd>+<kbd>i</kbd> (Mac) / <kbd>Ctrl</kbd>+<kbd>i</kbd> (Windows/Linux).
* **Smart actions:** To submit prompts via the context menu, right click in your editor, select **Copilot** in the menu that appears, then select one of the actions. Smart actions can also be accessed via the sparkle icon that sometimes appears when you select a line of code.

See [inline chat](<https://code.visualstudio.com/docs/copilot/copilot-chat#_inline-chat>), [quick chat](<https://code.visualstudio.com/docs/copilot/copilot-chat#_quick-chat>), and [chat smart actions](<https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-smart-actions>) in the Visual Studio Code documentation for more details.

## Using images in Copilot Chat

You can attach images and PDFs to your prompts when using a model that supports image input.

Copilot supports the following file types:

* JPEG (`.jpg`, `.jpeg`)
* PNG (`.png`)
* GIF (`.gif`)
* WEBP (`.webp`)
* PDF (`.pdf`)
* HEIC (`.heic`)
* HEIF (`.heif`)

For example, you can attach:

* A screenshot of a code snippet and ask Copilot to explain the code.
* A mockup of the user interface for an application and ask Copilot to generate the code.
* A flowchart and ask Copilot to describe the processes shown in the image.
* A screenshot of a web page and ask Copilot to generate HTML for a similar page.

Image and PDF attachments are available on all Copilot plans and are enabled by default, with no policy required to turn the feature on or off.

### Attaching images to your chat prompt

1. Do one of the following:

   * Copy an image and paste it into the chat view.
   * Drag and drop one or more image file from your operating system's file explorer—or from the Explorer in VS Code—into the chat view.
   * Right-click an image file in the VS Code Explorer and click **Copilot** then **Add File to Chat**.

2. Type your prompt into the chat view to accompany the image. For example, `explain this diagram`, `describe each of these images in detail`, `what does this error message mean`.

## Sharing feedback

To indicate whether a response was helpful, use the thumbs up and thumbs down icons that appear next to the response.

To leave feedback about the GitHub Copilot Chat extension, open an issue in the [microsoft/vscode-copilot-release](<https://github.com/microsoft/vscode-copilot-release/issues>) repository.

## Further reading

* [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)
* [Using Copilot Chat in VS Code](<https://code.visualstudio.com/docs/copilot/copilot-chat>) in the Visual Studio Code documentation
* [Managing agent sessions](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)
* [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Application card: GitHub Copilot Chat](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub Terms for Additional Products and Features](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- Visual Studio -->

<!-- --------------------- -->

<div class="ghd-tool visualstudio">

## Prerequisites

* **Access to GitHub Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **Visual Studio 2022 version 17.8 or later**. See [Install Visual Studio](<https://learn.microsoft.com/visualstudio/install/install-visual-studio>) in the Visual Studio documentation.

  * *For Visual Studio 17.8 and 17.9:*
    * **GitHub Copilot extension**. See [Install GitHub Copilot in Visual Studio](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot\&ref_type=engagement\&ref_style=text>) in the Visual Studio documentation.
    * **GitHub Copilot Chat extension**. See [Install GitHub Copilot in Visual Studio](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-install-and-states?ref_product=copilot\&ref_type=engagement\&ref_style=text>) in the Visual Studio documentation.

  *Visual Studio 17.10 and later have the GitHub Copilot and GitHub Copilot Chat extensions built in. You don't need to install them separately.*
* **Sign in to GitHub in Visual Studio**. If you experience authentication issues, see [Troubleshooting common issues with GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/troubleshoot-copilot/troubleshoot-common-issues#authentication-problems-in-visual-studio>).

If you have access to GitHub Copilot via your organization, you won't be able to use GitHub Copilot Chat if your organization owner has disabled chat. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Chat modes

Visual Studio supports the following chat modes:

* **Ask mode**: Get answers and guidance without making changes to your code.
* **Agent mode**: Give Copilot a high-level task and allow it to edit code, run commands, and iterate on the results. See [Using agent mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).

To switch between modes, use the mode dropdown at the bottom of the chat panel. Chat modes are available in Visual Studio 17.14 and later.

### Using ask mode

Use ask mode when you want Copilot to explain code, answer a question, or suggest an approach without modifying your project.

To use ask mode, select **Ask** from the mode dropdown at the bottom of the chat panel, then submit a prompt as described below.

## Submitting prompts

You can ask Copilot Chat to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. In the Visual Studio menu bar, click **View**, then click **GitHub Copilot Chat**.
2. In the Copilot Chat window, enter a prompt, then press **Enter**. For example prompts, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).
3. Evaluate Copilot's response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy, insert, or preview the result of a code block.

   To see the files that Copilot Chat used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using keywords in your prompt

You can use special keywords to help Copilot understand your prompt.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands>) or [Slash commands](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#slash-commands>) in the Visual Studio documentation.

### References

By default, Copilot Chat will reference the file that you have open or the code that you have selected. You can also use `#` followed by a file name, file name and line numbers, or `solution` to reference a specific file, lines, or solution.

See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=visualstudio#references>) or [Reference](<https://learn.microsoft.com/visualstudio/ide/copilot-chat-context#reference>) in the Visual Studio documentation.

## Using GitHub skills for Copilot (preview)

> \[!NOTE]
> The `@github` chat participant is currently in preview, and only available in [Visual Studio 2022 Preview 2](<https://visualstudio.microsoft.com/vs/preview/>) onwards.

Copilot's GitHub-specific skills expand the type of information Copilot can provide. To access these skills in Copilot Chat in Visual Studio, include `@github` in your question.

When you add `@github` to a question, Copilot dynamically selects an appropriate skill, based on the content of your question. You can also explicitly ask Copilot Chat to use a particular skill. For example, `@github Search the web to find the latest GPT4 model from OpenAI.`

You can generate a list of currently available skills by asking Copilot: `@github What skills are available?`

## Using Model Context Protocol (MCP) servers

You can use MCP to extend the capabilities of Copilot Chat by integrating it with a wide range of existing tools and services. For additional information, see [About Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>).

## AI models for Copilot Chat

You can change the model Copilot uses to generate responses. You may find that different models perform better, or provide more useful responses, depending on the type of questions you ask. Options include premium models with advanced capabilities. To change the model in your IDE, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>). To change or compare models on GitHub, see [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>).

## Additional ways to access Copilot Chat

In addition to submitting prompts through the chat window, you can submit prompts inline. To start an inline chat, right click in your editor window and select **Ask Copilot**.

See [Ask questions in the inline chat view](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat#ask-questions-in-the-inline-chat-view>) in the Visual Studio documentation for more details.

## Using images in Copilot Chat

You can attach images and PDFs to your prompts when using a model that supports image input.

Copilot supports the following file types:

* JPEG (`.jpg`, `.jpeg`)
* PNG (`.png`)
* GIF (`.gif`)
* WEBP (`.webp`)
* PDF (`.pdf`)
* HEIC (`.heic`)
* HEIF (`.heif`)

For example, you can attach:

* A screenshot of a code snippet and ask Copilot to explain the code.
* A mockup of the user interface for an application and ask Copilot to generate the code.
* A flowchart and ask Copilot to describe the processes shown in the image.
* A screenshot of a web page and ask Copilot to generate HTML for a similar page.

Image and PDF attachments are available on all Copilot plans and are enabled by default, with no policy required to turn the feature on or off.

### Attaching images to your chat prompt

1. If you see the AI model picker at the bottom right of the chat view, select one of the models that supports adding images to prompts:

2. Do one of the following:

   * Copy an image and paste it into the chat view.
   * Click the paperclip icon at the bottom right of the chat view, click **Upload Image**, browse to the image file you want to attach, select it and click **Open**.

   You can add multiple images if required.

3. Type your prompt into the chat view to accompany the image. For example, `explain this image`, or `describe each of these images in detail`.

## Sharing feedback

To share feedback about Copilot Chat, you can use the **Send feedback** button in Visual Studio. For more information on providing feedback for Visual Studio, see the [Visual Studio Feedback](<https://learn.microsoft.com/en-us/visualstudio/ide/how-to-report-a-problem-with-visual-studio?view=vs-2022>) documentation.

1. In the top right corner of the Visual Studio window, click the **Send feedback** button.

   ![Screenshot of the share feedback button in Visual Studio.](<https://docs.github.com/assets/images/help/copilot/vs-share-feedback-button.png>)

2. Choose the option that best describes your feedback.
   * To report a bug, click **Report a problem**.
   * To request a feature, click **Suggest a feature**.

## Further reading

* [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)
* [Using GitHub Copilot Chat in Visual Studio in the Microsoft Learn documentation](<https://learn.microsoft.com/visualstudio/ide/visual-studio-github-copilot-chat?view=vs-2022#use-copilot-chat-in-visual-studio>)
* [Tips to improve GitHub Copilot Chat results in the Microsoft Learn documentation](<https://learn.microsoft.com/en-us/visualstudio/ide/copilot-chat-context?view=vs-2022>)
* [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Application card: GitHub Copilot Chat](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub Terms for Additional Products and Features](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- JetBrains -->

<!-- --------------------- -->

<div class="ghd-tool jetbrains">

## Prerequisites

* **Access to GitHub Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **Compatible JetBrains IDE**. GitHub Copilot is compatible with the following IDEs:

  * IntelliJ IDEA (Ultimate, Community, Educational)
  * Android Studio
  * CLion
  * Code With Me Guest
  * DataGrip
  * DataSpell
  * GoLand
  * JetBrains Client
  * MPS
  * PhpStorm
  * PyCharm (Professional, Community, Educational)
  * Rider
  * RubyMine
  * RustRover
  * WebStorm

  See the [JetBrains IDEs](<https://www.jetbrains.com/products/?ref_product=copilot\&ref_type=engagement\&ref_style=button>) tool finder to download.
* **Latest version of the GitHub Copilot extension**. See the [GitHub Copilot plugin](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot\&ref_type=engagement\&ref_style=text>) in the JetBrains Marketplace. For installation instructions, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>).
* **Sign in to GitHub in your JetBrains IDE**. For authentication instructions, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains#installing-the-github-copilot-plugin-in-your-jetbrains-ide>).

If you have access to GitHub Copilot via your organization, you won't be able to use GitHub Copilot Chat if your organization owner has disabled chat. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Chat modes

The agent picker in the Copilot Chat panel lets you choose which agent drives your conversation. To switch agents, use the Agents dropdown at the bottom of the chat panel.

The following agents are available:

* **Agent mode** (default): Full agentic experience with autonomous task execution. See [Using agent mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* **Ask mode**: Get quick answers and assistance without making code changes.
* **Plan mode**: Collaborate on planning before implementation—Copilot analyzes your request and builds a structured plan for your review. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).
* **Edit mode**: Make controlled edits across multiple files that you review and accept individually.
* **Copilot CLI**: Runs Copilot through Copilot CLI, providing a terminal-first agentic experience with support for multiple isolation modes, live session progress, and tool call visibility. For more information, see [GitHub Copilot CLI](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-cli>).
* **Custom agents**: Use personalized agents tailored to your specific needs. See [Using custom agents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

Copilot Edits lets you make changes across multiple files directly from a single Copilot Chat prompt, using edit mode and agent mode.

> \[!TIP]
> You can also access Copilot from JetBrains AI Assistant without installing the Copilot plugin. For more information, see [GitHub Copilot in IDEs](<https://docs.github.com/en/copilot/concepts/copilot-surfaces/copilot-in-ides>).

### Using edit mode

Edit mode is only available in Visual Studio Code and JetBrains IDEs.

Use edit mode when you want more granular control over the edits that Copilot proposes. In edit mode, you choose which files Copilot can make changes to, provide context to Copilot with each iteration, and decide whether or not to accept the suggested edits after each turn.

Edit mode is best suited to use cases where:

* You want to make a quick, specific update to a defined set of files.
* You want full control over the number of LLM requests Copilot uses.

1. To start an edit session, click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> Copilot** in the menu bar, then select **Open GitHub Copilot Chat**.
2. At the top of the chat panel, click **Copilot Edits**.
3. Add relevant files to the *working set* to indicate to GitHub Copilot which files you want to work on. You can add all open files by clicking **Add all open files** or individually search for single files.
4. Submit a prompt. In response to your prompt, Copilot Edits determines which files in your *working set* to change and adds a short description of the change.
5. Review the changes and **Accept** or **Discard** the edits for each file.

## Submitting prompts

You can ask Copilot Chat to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. Open the Copilot Chat window by clicking the **GitHub Copilot Chat** icon at the right side of the JetBrains IDE window.

   ![Screenshot of the GitHub Copilot Chat icon in the Activity Bar.](<https://docs.github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

2. Enter a prompt in the prompt box. For example prompts, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

3. Evaluate Copilot's response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy or insert a code block.

   To see the files that Copilot Chat used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Supplementing your prompt

You can use slash commands and file references to help Copilot understand your what you are asking it to do.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. See also [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=jetbrains#slash-commands-2>).

### File references

By default, Copilot Chat will reference the file that you have open or the code that you have selected. You can also tell Copilot Chat which files to reference by dragging a file into the chat prompt box. Alternatively, you can right click on a file, select **GitHub Copilot**, then select **Reference File in Chat**.

## Using GitHub skills for Copilot

Copilot's GitHub-specific skills expand the type of information Copilot can provide. To access these skills in Copilot Chat, include `@github` in your question.

When you add `@github` to a question, Copilot dynamically selects an appropriate skill, based on the content of your question. You can also explicitly ask Copilot Chat to use a particular skill. You can do this in two ways:

* Use natural language to ask Copilot Chat to use a skill. For example, `@github Search the web to find the latest GPT model from OpenAI.`
* To specifically invoke a web search you can include the `#web` variable in your question. For example, `@github #web What is the latest LTS of Node.js?`

You can generate a list of currently available skills by asking Copilot: `@github What skills are available?`

## Using Model Context Protocol (MCP) servers

You can use MCP to extend the capabilities of Copilot Chat by integrating it with a wide range of existing tools and services. For additional information, see [About Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>).

## AI models for Copilot Chat

You can change the model Copilot uses to generate responses. You may find that different models perform better, or provide more useful responses, depending on the type of questions you ask. Options include premium models with advanced capabilities. To change the model in your IDE, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>). To change or compare models on GitHub, see [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>).

## Additional ways to access Copilot Chat

* **Built-in requests**. In addition to submitting prompts through the chat window, you can submit built-in requests by right clicking in a file, selecting **GitHub Copilot**, then selecting one of the options.
* **Inline**. You can submit a chat prompt inline, and scope it to a highlighted code block or your current file.
  * To start an inline chat, right click on a code block or anywhere in your current file, hover over **GitHub Copilot**, then select **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="plus" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> Copilot: Inline Chat**, or enter <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>I</kbd>.

## Sharing feedback

To share feedback about Copilot Chat, you can use the **share feedback** link in JetBrains.

1. At the right side of the JetBrains IDE window, click the **Copilot Chat** icon to open the Copilot Chat window.

   ![Screenshot of the Copilot Chat icon in the Activity Bar.](<https://docs.github.com/assets/images/help/copilot/jetbrains-copilot-chat-icon.png>)

2. At the top of the Copilot Chat window, click the **share feedback** link.

   ![Screenshot of the share feedback link in the Copilot Chat window.](<https://docs.github.com/assets/images/help/copilot/jetbrains-share-feedback.png>)

## Further reading

* [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)
* [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Application card: GitHub Copilot Chat](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub Pre-release License Terms](<https://docs.github.com/en/site-policy/github-terms/github-pre-release-license-terms>)
* [GitHub Terms for Additional Products and Features](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- XCode -->

<!-- --------------------- -->

<div class="ghd-tool xcode">

## Prerequisites

* **Access to GitHub Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **Latest version of the GitHub Copilot extension**. For installation instructions, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension>).
* **Sign in to GitHub in Xcode**.

If you have access to GitHub Copilot via your organization, you won't be able to use GitHub Copilot Chat if your organization owner has disabled chat. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Chat modes

You can use Copilot Chat in agent mode to autonomously accomplish a set task, or in plan mode to draft an implementation plan before any code changes are made. To switch between modes, use the agents dropdown at the bottom of the chat window.

* For agent mode, see [Using agent mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* For plan mode, see [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Submitting prompts

You can ask Copilot Chat to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. To open the chat window, click **Editor** in the menu bar, then click **GitHub Copilot** then **Open Chat**. Copilot Chat opens in a new window.

2. Enter a prompt in the prompt box. For example prompts, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

3. Evaluate Copilot's response, and submit a follow up prompt if needed.

   The response often includes interactive elements. For example, the response may include buttons to copy or insert a code block.

   To see the files that Copilot Chat used to generate the response, click the **References** link below the response. The references may include a link to a custom instructions file for your repository. This file contains additional information that is automatically added to all of your chat questions to improve the quality of the responses. For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

## Using Model Context Protocol (MCP) servers

You can use MCP to extend the capabilities of Copilot Chat by integrating it with a wide range of existing tools and services. For additional information, see [About Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>).

## AI models for Copilot Chat

You can change the model Copilot uses to generate responses. You may find that different models perform better, or provide more useful responses, depending on the type of questions you ask. Options include premium models with advanced capabilities. To change the model in your IDE, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>). To change or compare models on GitHub, see [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>).

## Using keywords in your prompt

You can use special keywords to help Copilot understand your prompt.

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command.

To see all available slash commands, type `/` in the chat prompt box. For more information, see [GitHub Copilot Chat cheat sheet](<https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=xcode#slash-commands>).

## File references

By default, Copilot Chat will reference the file that you have open or the code that you have selected. To attach a specific file as reference, click <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-paperclip" aria-label="Add attachments" role="img"><path d="M12.212 3.02a1.753 1.753 0 0 0-2.478.003l-5.83 5.83a3.007 3.007 0 0 0-.88 2.127c0 .795.315 1.551.88 2.116.567.567 1.333.89 2.126.89.79 0 1.548-.321 2.116-.89l5.48-5.48a.75.75 0 0 1 1.061 1.06l-5.48 5.48a4.492 4.492 0 0 1-3.177 1.33c-1.2 0-2.345-.487-3.187-1.33a4.483 4.483 0 0 1-1.32-3.177c0-1.195.475-2.341 1.32-3.186l5.83-5.83a3.25 3.25 0 0 1 5.553 2.297c0 .863-.343 1.691-.953 2.301L7.439 12.39c-.375.377-.884.59-1.416.593a1.998 1.998 0 0 1-1.412-.593 1.992 1.992 0 0 1 0-2.828l5.48-5.48a.751.751 0 0 1 1.042.018.751.751 0 0 1 .018 1.042l-5.48 5.48a.492.492 0 0 0 0 .707.499.499 0 0 0 .352.154.51.51 0 0 0 .356-.154l5.833-5.827a1.755 1.755 0 0 0 0-2.481Z"></path></svg> in the chat prompt box.

## Chat management

You can open a conversation thread for each Xcode IDE to keep discussions organized across different contexts. You can also revisit previous conversations and reference past suggestions through the chat history.

## Sharing feedback

To indicate whether a response was helpful, use <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-thumbsup" aria-label="Thumbs up" role="img"><path d="M8.347.631A.75.75 0 0 1 9.123.26l.238.04a3.25 3.25 0 0 1 2.591 4.098L11.494 6h.665a3.25 3.25 0 0 1 3.118 4.167l-1.135 3.859A2.751 2.751 0 0 1 11.503 16H6.586a3.75 3.75 0 0 1-2.184-.702A1.75 1.75 0 0 1 3 16H1.75A1.75 1.75 0 0 1 0 14.25v-6.5C0 6.784.784 6 1.75 6h3.417a.25.25 0 0 0 .217-.127ZM4.75 13.649l.396.33c.404.337.914.521 1.44.521h4.917a1.25 1.25 0 0 0 1.2-.897l1.135-3.859A1.75 1.75 0 0 0 12.159 7.5H10.5a.75.75 0 0 1-.721-.956l.731-2.558a1.75 1.75 0 0 0-1.127-2.14L6.69 6.611a1.75 1.75 0 0 1-1.523.889H4.75ZM3.25 7.5h-1.5a.25.25 0 0 0-.25.25v6.5c0 .138.112.25.25.25H3a.25.25 0 0 0 .25-.25Z"></path></svg> or <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-thumbsdown" aria-label="Thumbs down" role="img"><path d="M7.653 15.369a.75.75 0 0 1-.776.371l-.238-.04a3.25 3.25 0 0 1-2.591-4.099L4.506 10h-.665A3.25 3.25 0 0 1 .723 5.833l1.135-3.859A2.75 2.75 0 0 1 4.482 0H9.43c.78.003 1.538.25 2.168.702A1.752 1.752 0 0 1 12.989 0h1.272A1.75 1.75 0 0 1 16 1.75v6.5A1.75 1.75 0 0 1 14.25 10h-3.417a.25.25 0 0 0-.217.127ZM11.25 2.351l-.396-.33a2.248 2.248 0 0 0-1.44-.521H4.496a1.25 1.25 0 0 0-1.199.897L2.162 6.256A1.75 1.75 0 0 0 3.841 8.5H5.5a.75.75 0 0 1 .721.956l-.731 2.558a1.75 1.75 0 0 0 1.127 2.14L9.31 9.389a1.75 1.75 0 0 1 1.523-.889h.417Zm1.5 6.149h1.5a.25.25 0 0 0 .25-.25v-6.5a.25.25 0 0 0-.25-.25H13a.25.25 0 0 0-.25.25Z"></path></svg> that appear next to the response.

## Further reading

* [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)
* [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Application card: GitHub Copilot Chat](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub Pre-release License Terms](<https://docs.github.com/en/site-policy/github-terms/github-pre-release-license-terms>)
* [GitHub Terms for Additional Products and Features](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>

<!-- --------------------- -->

<!-- Eclipse -->

<!-- --------------------- -->

<div class="ghd-tool eclipse">

## Prerequisites

* **Access to Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **Compatible version of Eclipse**. To use the GitHub Copilot extension, you must have Eclipse version 2024-09 or above. See the [Eclipse download page](<https://www.eclipse.org/downloads/packages/>).
* If you are a member of an organization or enterprise with a Copilot Business or Copilot Enterprise plan, the "MCP servers in Copilot" policy must be enabled in order to use MCP with Copilot.
* **Latest version of the GitHub Copilot extension**. Download this from the [Eclipse Marketplace](<https://aka.ms/copiloteclipse?ref_product=copilot\&ref_type=engagement\&ref_style=text>). For more information, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=eclipse>).
* **Sign in to GitHub in Eclipse**.

If you have access to GitHub Copilot via your organization, you won't be able to use GitHub Copilot Chat if your organization owner has disabled chat. See [Managing policies and features for GitHub Copilot in your organization](<https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/manage-policies>).

## Chat modes

You can use Copilot Chat in agent mode to autonomously accomplish a set task, or in plan mode to draft an implementation plan before any code changes are made. To switch between modes, use the agents dropdown at the bottom of the chat panel.

* For agent mode, see [Using agent mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode>).
* For plan mode, see [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Submitting prompts

You can ask Copilot Chat to give you code suggestions, explain code, generate unit tests, and suggest code fixes.

1. To open the Copilot Chat panel, click the Copilot icon (<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg>) in the status bar at the bottom of Eclipse, then click **Open Chat**.

2. Enter a prompt in the prompt box, then press <kbd>Enter</kbd>.

   For an introduction to the kinds of prompts you can use, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

3. Evaluate Copilot's response, and make a follow up request if needed.

## Using keywords in your prompt

You can use special keywords to help Copilot understand your prompt. For examples, see [Getting started with prompts for GitHub Copilot Chat in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide>).

### Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by a command. For example, use `/explain` to ask Copilot to explain the code in the file currently displayed in the editor.

To see all available slash commands, type `/` in the chat prompt box.

## Using Model Context Protocol (MCP) servers

You can use MCP to extend the capabilities of Copilot Chat by integrating it with a wide range of existing tools and services. For additional information, see [About Model Context Protocol (MCP)](<https://docs.github.com/en/copilot/concepts/context/mcp>).

## AI models for Copilot Chat

You can change the model Copilot uses to generate responses. You may find that different models perform better, or provide more useful responses, depending on the type of questions you ask. Options include premium models with advanced capabilities. To change the model in your IDE, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>). To change or compare models on GitHub, see [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github#changing-and-comparing-ai-models>).

## Further reading

* [Prompt engineering for GitHub Copilot Chat](<https://docs.github.com/en/copilot/concepts/prompting/prompt-engineering>)
* [Asking GitHub Copilot questions in GitHub](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Application card: GitHub Copilot Chat](<https://docs.github.com/en/copilot/responsible-use/chat>)
* [GitHub Terms for Additional Products and Features](<https://docs.github.com/en/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page>)
* [GitHub Copilot FAQ](<https://github.com/features/copilot#faq>)

</div>


</details>
