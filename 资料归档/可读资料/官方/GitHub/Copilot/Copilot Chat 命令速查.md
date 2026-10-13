# Copilot Chat 命令速查

按使用界面列出引用、提及、斜杠命令、聊天变量和参与者，便于查询上下文与工具的调用方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/reference/chat-cheat-sheet.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Chat 命令速查.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: GitHub Copilot Chat cheat sheet
shortTitle: Chat cheat sheet
intro: 'Use this cheat sheet to quickly reference the most common commands and options for using {% data variables.copilot.copilot_chat %}.'
versions:
  feature: copilot
category:
  - Author and optimize with Copilot
redirect_from:
  - /copilot/reference/cheat-sheet
  - /copilot/using-github-copilot/github-copilot-chat-cheat-sheet
  - /copilot/using-github-copilot/copilot-chat/github-copilot-chat-cheat-sheet
  - /copilot/reference/github-copilot-chat-cheat-sheet
contentType: reference
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% webui %}

本文的这个版本适用于{% data variables.product.github %}网站上的{% data variables.product.prodname_copilot_short %}。对于本文的其他版本，请单击上面的选项卡。

{% data reusables.copilot.about-copilot-enhancements %}

有关如何开始使用{% data variables.product.github %}网站中的{% data variables.copilot.copilot_chat_short %}的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)。

## 提及

使用 `@` 提及将相关上下文直接附加到您的对话中。在聊天提示框中输入`@`以显示您可以附加的项目列表，例如：

* 讨论
* 扩展
* 文件
* 问题
* 拉取请求
* 存储库

## 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，后跟命令名称。

可用的斜杠命令可能会有所不同，具体取决于您的环境和聊天上下文。要查看当前可用的斜线命令列表，请在当前环境的聊天提示框中输入 `/`。以下是使用 {% data variables.copilot.copilot_chat_short %} 的一些最常见斜杠命令的列表。

|命令|描述|
| --- | --- |
| `/clear` |对话清晰。|
| `/delete` |删除对话。|
| `/new` |开始新的对话|
| `/rename` |重命名对话。|

## MCP技能

以下是 {% data variables.copilot.copilot_chat_dotcom_short %} 中当前可用的 MCP 技能的列表，以及您可以用来调用它们的示例提示。您不需要在提示中使用 MCP 技能名称；您可以简单地要求{% data variables.copilot.copilot_chat_short %}来执行任务。

|技能|提示示例|
| --- | --- |
| `create_branch`             | 创建一个名为的新分支 [BRANCH-NAME] 在存储库中 [USERNAME/REPO-NAME].                                                                 |
| `create_or_update_file`     | 添加一个名为 `hello-world.md` 给我的 [BRANCH-NAME] 的 [USERNAME/REPO-NAME] 内容为：“你好，世界！该文件是从 {% data variables.copilot.copilot_chat_dotcom_short %}!"         |
| `push_files`                | 推送文件 `test.md` 内容为“这是一个测试文件”并且 `test-again.md` 内容为“这是另一个测试文件” [BRANCH-NAME] 在 [USERNAME/REPO-NAME] |
| `update_pull_request_branch`| 更新拉取请求的分支 [PR-数量] 在 [USERNAME/REPO-NAME] 与基础分支的最新更改。                                          |
| `merge_pull_request`        |合并拉取请求 [PR-编号 ] 在 [USERNAME/REPO-NAME] 中|
| `get_me`                    |告诉我关于我自己的事。|
| `search_users`              |搜索名为“Mona Octocat”的用户|

有关在{% data variables.copilot.copilot_chat_short %}中使用MCP技能的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>)。

{% endwebui %}

{% vscode %}

本文的这个版本适用于{% data variables.product.prodname_vscode %}中的{% data variables.product.prodname_copilot_short %}。对于本文的其他版本，请单击上面的选项卡。

{% data reusables.copilot.about-copilot-enhancements %}

有关如何开始使用{% data variables.product.prodname_vscode %}中的{% data variables.copilot.copilot_chat_short %}的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)。

## 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，后跟命令名称。

可用的斜杠命令可能会有所不同，具体取决于您的环境和聊天上下文。要查看当前可用的斜线命令列表，请在当前环境的聊天提示框中输入 `/`。以下是使用 {% data variables.copilot.copilot_chat_short %} 的一些最常见斜杠命令的列表。

|命令|描述|
| --- | --- |
| `/clear` |开始新的聊天会话。|
| `/explain` |解释活动编辑器中的代码如何工作。|
| `/fix` |针对所选代码中的问题提出修复建议。|
| `/fixTestFailure` |查找并修复失败的测试。|
| `/help` |使用 {% data variables.product.prodname_copilot %} 的快速参考和基础知识。|
| `/new` |创建一个新项目。|
| `/tests` |为选定的代码生成单元测试。|

## 聊天变量

使用聊天变量在提示中包含特定上下文。要使用聊天变量，请在聊天提示框中键入 `#`，然后键入聊天变量。

|变量|描述|
| --- | --- |
| `#block` |在提示中包含当前代码块。|
| `#class` |在提示中包括当前班级。|
| `#comment` |在提示中包含当前评论。|
| `#file` |在提示中包括当前文件的内容。|
| `#function` |在提示中包含当前函数或方法。|
| `#line` |包括提示中的当前代码行。|
| `#path` |在提示中包含文件路径。|
| `#project` |在提示中包含项目上下文。|
| `#selection` |在提示中包含当前选定的文本。|
| `#sym` |在提示中包含当前符号。|

## 聊天参与者

聊天参与者就像领域专家，他们拥有可以为您提供帮助的专业知识。您可以通过在聊天提示框中输入`@`，然后输入聊天参与者名称来指定聊天参与者。要查看所有可用的聊天参与者，请在聊天提示框中键入 `@`。

以下是使用 {% data variables.copilot.copilot_chat_short %} 的一些最常见聊天参与者的列表。

|变量|描述|
|--------------|----------------------------------------------------------------------------------------------|
| `@azure`     |了解有关 Azure 服务以及如何使用、部署和管理它们的背景信息。当您需要有关 Azure 的帮助时，请使用 `@azure`。 `@azure` 聊天参与者当前位于 {% data variables.release-phases.public_preview %}，并且可能会发生变化。|
| `@github`    |允许你使用{% data variables.product.github %}专用的{% data variables.product.prodname_copilot_short %}技能。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide#using-github-skills-for-copilot>)。|
| `@terminal`  |具有有关 {% data variables.product.prodname_vscode %} 终端 shell 及其内容的上下文。当您需要创建或调试终端命令的帮助时，请使用 `@terminal`。|
| `@vscode`    |具有有关 {% data variables.product.prodname_vscode %} 命令和功能的上下文。当您需要 {% data variables.product.prodname_vscode %} 的帮助时，请使用 `@vscode`。|

{% endvscode %}

{% visualstudio %}

本文的这个版本适用于{% data variables.product.prodname_vs %}中的{% data variables.product.prodname_copilot_short %}。对于本文的其他版本，请单击上面的选项卡。

{% data reusables.copilot.about-copilot-enhancements %}

有关如何开始使用{% data variables.product.prodname_vs %}中的{% data variables.copilot.copilot_chat_short %}的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)。

## 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，后跟命令名称。

可用的斜杠命令可能会有所不同，具体取决于您的环境和聊天上下文。要查看当前可用的斜线命令列表，请在当前环境的聊天提示框中输入 `/`。以下是使用 {% data variables.copilot.copilot_chat_short %} 的一些最常见斜杠命令的列表。

|命令|描述|
| --- | --- |
| `/doc` |添加此符号的文档注释。|
| `/explain` |解释活动编辑器中的代码如何工作。|
| `/fix` |针对所选代码中的问题提出修复建议。|
| `/help` |使用 {% data variables.product.prodname_copilot %} 的快速参考和基础知识。|
| `/optimize` |分析并改进所选代码的运行时间。|
| `/tests` |为选定的代码生成单元测试。|

## 参考文献

默认情况下，{% data variables.copilot.copilot_chat_short %} 将引用您已打开的文件或您已选择的代码。您还可以使用 # 后跟文件名、文件名和行号或解决方案来引用特定文件、行或解决方案。

|示例|描述|
| --- | --- |
| `Where are the tests in #MyFile.cs?` |引用特定文件|
| `How are these files related #MyFile.cs #MyFile2.cs` |引用多个文件|
| `Explain this function #MyFile.cs: 66-72?` |引用文件中的特定行|
| `Is there a delete method in this #solution?` |引用当前文件|

{% endvisualstudio %}

{% jetbrains %}

本文的这个版本适用于JetBrains中的{% data variables.product.prodname_copilot_short %}。对于本文的其他版本，请单击上面的选项卡。

{% data reusables.copilot.about-copilot-enhancements %}

有关如何开始使用JetBrains中的{% data variables.copilot.copilot_chat_short %}的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)。

## 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，后跟命令名称。

可用的斜杠命令可能会有所不同，具体取决于您的环境和聊天上下文。要查看当前可用的斜线命令列表，请在当前环境的聊天提示框中输入 `/`。以下是使用 {% data variables.copilot.copilot_chat_short %} 的一些最常见斜杠命令的列表。

如果您在 JetBrains 中运行交互式 {% data variables.copilot.copilot_cli_short %} 会话，您还可以在该会话中使用 CLI- 特定的斜杠命令。

|命令|描述|
| --- | --- |
| `/chronicle` |查看并分析您的会话历史记录。可用子命令：`standup`、`tips`、`search` 和 `improve`。有关这些子命令的更多详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>)。|
| `/compact` |手动压缩当前的 {% data variables.copilot.copilot_cli_short %} 会话上下文以保持长时间运行的会话易于管理。|
| `/explain` |解释活动编辑器中的代码如何工作。|
| `/fix` |针对所选代码中的问题提出修复建议。|
| `/help` |使用 {% data variables.product.prodname_copilot %} 的快速参考和基础知识。|
| `/remote` |（仅限 {% data variables.copilot.copilot_cli_short %} 会话）启用或管理当前会话的远程控制，以便您可以从 {% data variables.product.prodname_dotcom_the_website %} 或 {% data variables.product.prodname_mobile %} 访问它。|
| `/tests` |为选定的代码生成单元测试。|

{% endjetbrains %}

{% xcode %}

本文的这个版本适用于 Xcode 中的 {% data variables.product.prodname_copilot_short %}。对于本文的其他版本，请单击上面的选项卡。

{% data reusables.copilot.about-copilot-enhancements %}

有关如何在 Xcode 中开始使用 {% data variables.copilot.copilot_chat_short %} 的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)。

## 斜杠命令

使用斜杠命令可以避免为常见场景编写复杂的提示。要使用斜杠命令，请在聊天提示框中键入 `/`，后跟命令名称。

可用的斜杠命令可能会有所不同，具体取决于您的环境和聊天上下文。要查看当前可用的斜线命令列表，请在当前环境的聊天提示框中输入 `/`。下面是使用 {% data variables.copilot.copilot_chat_short %} 的斜杠命令列表。

|命令|描述|
|-------------|----------------------------------------------------|
| `/doc`      |生成该符号的文档。|
| `/explain`  |为所选代码提供解释。|
| `/fix`      |建议修复代码错误和拼写错误。|
| `/simplify` |简化当前代码选择。|
| `/tests`    |为当前代码选择创建单元测试。|

{% endxcode %}


## 原文对照

<details>
<summary>展开完整原文</summary>

{% webui %}

This version of this article is for {% data variables.product.prodname_copilot_short %} on the {% data variables.product.github %} website. For other versions of this article, click the tabs above.

{% data reusables.copilot.about-copilot-enhancements %}

For information about how to get started with {% data variables.copilot.copilot_chat_short %} in the {% data variables.product.github %} website, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>).

## Mentions

Use `@` mentions in to attach relevant context directly to your conversations. Type `@` in the chat prompt box to display a list of items you can attach, such as:

* Discussions
* Extensions
* Files
* Issues
* Pull requests
* Repositories

## Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of some of the most common slash commands for using {% data variables.copilot.copilot_chat_short %}.

| Command | Description |
| --- | --- |
| `/clear` | Clear conversation. |
| `/delete` | Delete a conversation. |
| `/new` | Start a new conversation |
| `/rename` | Rename a conversation. |

## MCP skills

Below is a list of the MCP skills that are currently available in {% data variables.copilot.copilot_chat_dotcom_short %}, and example prompts you can use to invoke them. You do not need to use the MCP skill name in your prompt; you can simply ask {% data variables.copilot.copilot_chat_short %} to perform the task.

| Skill | Example prompt |
| --- | --- |
| `create_branch`             | Create a new branch called [BRANCH-NAME] in the repository [USERNAME/REPO-NAME].                                                                 |
| `create_or_update_file`     | Add a new file named `hello-world.md` to my [BRANCH-NAME] of [USERNAME/REPO-NAME] with the content: "Hello, world! This file was created from {% data variables.copilot.copilot_chat_dotcom_short %}!"         |
| `push_files`                | Push the files `test.md` with the content "This is a test file" and `test-again.md` with the content "This is another test file" to the [BRANCH-NAME] in [USERNAME/REPO-NAME] |
| `update_pull_request_branch`| Update the branch for pull request [PR-number] in [USERNAME/REPO-NAME] with the latest changes from the base branch.                                          |
| `merge_pull_request`        | Merge pull request [PR-Number] in [USERNAME/REPO-NAME]                                                                                                                      |
| `get_me`                    | Tell me about myself.                                                                                                                                 |
| `search_users`              | Search for users with the name "Mona Octocat"                                                                                       |

For more information about using MCP skills in {% data variables.copilot.copilot_chat_short %}, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

{% endwebui %}

{% vscode %}

This version of this article is for {% data variables.product.prodname_copilot_short %} in {% data variables.product.prodname_vscode %}. For other versions of this article, click the tabs above.

{% data reusables.copilot.about-copilot-enhancements %}

For information about how to get started with {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vscode %}, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

## Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of some of the most common slash commands for using {% data variables.copilot.copilot_chat_short %}.

| Command | Description |
| --- | --- |
| `/clear` | Start a new chat session. |
| `/explain` | Explain how the code in your active editor works. |
| `/fix` | Propose a fix for problems in the selected code. |
| `/fixTestFailure` | Find and fix a failing test. |
| `/help` | Quick reference and basics of using {% data variables.product.prodname_copilot %}. |
| `/new` | Create a new project. |
| `/tests` | Generate unit tests for the selected code. |

## Chat variables

Use chat variables to include specific context in your prompt. To use a chat variable, type `#` in the chat prompt box, followed by a chat variable.

| Variable | Description |
| --- | --- |
| `#block` | Includes the current block of code in the prompt. |
| `#class` | Includes the current class in the prompt. |
| `#comment` | Includes the current comment in the prompt. |
| `#file` | Includes the current file's content in the prompt. |
| `#function` | Includes the current function or method in the prompt. |
| `#line` | Includes the current line of code in the prompt. |
| `#path` | Includes the file path in the prompt. |
| `#project` | Includes the project context in the prompt. |
| `#selection` | Includes the currently selected text in the prompt. |
| `#sym` | Includes the current symbol in the prompt. |

## Chat participants

Chat participants are like domain experts who have a specialty that they can help you with. You can specify a chat participant by typing `@` in the chat prompt box, followed by a chat participant name. To see all available chat participants, type `@` in the chat prompt box.

Below is a list of some of the most common chat participants for using {% data variables.copilot.copilot_chat_short %}.

| Variable    | Description |
|--------------|----------------------------------------------------------------------------------------------|
| `@azure`     | Has context about Azure services and how to use, deploy and manage them. Use `@azure` when you want help with Azure. The `@azure` chat participant is currently in {% data variables.release-phases.public_preview %} and is subject to change. |
| `@github`    | Allows you to use {% data variables.product.github %}-specific {% data variables.product.prodname_copilot_short %} skills. See [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide#using-github-skills-for-copilot>). |
| `@terminal`  | Has context about the {% data variables.product.prodname_vscode %} terminal shell and its contents. Use `@terminal` when you want help creating or debugging terminal commands. |
| `@vscode`    | Has context about {% data variables.product.prodname_vscode %} commands and features. Use `@vscode` when you want help with {% data variables.product.prodname_vscode %}. |

{% endvscode %}

{% visualstudio %}

This version of this article is for {% data variables.product.prodname_copilot_short %} in {% data variables.product.prodname_vs %}. For other versions of this article, click the tabs above.

{% data reusables.copilot.about-copilot-enhancements %}

For information about how to get started with {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vs %}, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

## Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of some of the most common slash commands for using {% data variables.copilot.copilot_chat_short %}.

| Command | Description |
| --- | --- |
| `/doc` | Add documentation comment for this symbol. |
| `/explain` | Explain how the code in your active editor works. |
| `/fix` | Propose a fix for problems in the selected code. |
| `/help` | Quick reference and basics of using {% data variables.product.prodname_copilot %}. |
| `/optimize` | Analyze and improve running time of the selected code. |
| `/tests` | Generate unit tests for the selected code. |

## References

By default, {% data variables.copilot.copilot_chat_short %} will reference the file that you have open or the code that you have selected. You can also use # followed by a file name, file name and line numbers, or solution to reference a specific file, lines, or solution.

| Example | Description |
| --- | --- |
| `Where are the tests in #MyFile.cs?` | References a specific file |
| `How are these files related #MyFile.cs #MyFile2.cs` | References multiple files |
| `Explain this function #MyFile.cs: 66-72?` | References specific lines in a file |
| `Is there a delete method in this #solution?` | References the current file |

{% endvisualstudio %}

{% jetbrains %}

This version of this article is for {% data variables.product.prodname_copilot_short %} in JetBrains. For other versions of this article, click the tabs above.

{% data reusables.copilot.about-copilot-enhancements %}

For information about how to get started with {% data variables.copilot.copilot_chat_short %} in JetBrains, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

## Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of some of the most common slash commands for using {% data variables.copilot.copilot_chat_short %}.

If you run interactive {% data variables.copilot.copilot_cli_short %} sessions in JetBrains, you can also use CLI-specific slash commands from that session.

| Command | Description |
| --- | --- |
| `/chronicle` | Review and analyze your session history. Available subcommands: `standup`, `tips`, `search`, and `improve`. For more details on these subcommands, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/chronicle>). |
| `/compact` | Manually compress the current {% data variables.copilot.copilot_cli_short %} session context to keep a long-running session manageable. |
| `/explain` | Explain how the code in your active editor works. |
| `/fix` | Propose a fix for problems in the selected code. |
| `/help` | Quick reference and basics of using {% data variables.product.prodname_copilot %}. |
| `/remote` | ({% data variables.copilot.copilot_cli_short %} session only) Enable or manage remote control for the current session so you can access it from {% data variables.product.prodname_dotcom_the_website %} or {% data variables.product.prodname_mobile %}. |
| `/tests` | Generate unit tests for the selected code. |

{% endjetbrains %}

{% xcode %}

This version of this article is for {% data variables.product.prodname_copilot_short %} in Xcode. For other versions of this article, click the tabs above.

{% data reusables.copilot.about-copilot-enhancements %}

For information about how to get started with {% data variables.copilot.copilot_chat_short %} in Xcode, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

## Slash commands

Use slash commands to avoid writing complex prompts for common scenarios. To use a slash command, type `/` in the chat prompt box, followed by the command name.

Available slash commands may vary, depending on your environment and the context of your chat. To view a list of currently available slash commands, type `/` in the chat prompt box of your current environment. Below is a list of the slash commands for using {% data variables.copilot.copilot_chat_short %}.

| Command     | Description                                        |
|-------------|----------------------------------------------------|
| `/doc`      | Generate documentation for this symbol.            |
| `/explain`  | Provide an explanation for the selected code.      |
| `/fix`      | Suggest fixes for code errors and typos.           |
| `/simplify` | Simplify the current code selection.               |
| `/tests`    | Create a unit test for the current code selection. |

{% endxcode %}


</details>
