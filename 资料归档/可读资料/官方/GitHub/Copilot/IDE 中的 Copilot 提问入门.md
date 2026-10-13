# IDE 中的 Copilot 提问入门

提供理解项目、生成代码、修复重构、编写测试和解释命令的提问示例，按编辑器介绍使用场景。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/get-started-with-chat-in-your-ide.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/IDE 中的 Copilot 提问入门.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Getting started with prompts for GitHub Copilot Chat in your IDE
intro: Get an overview of ways to use {% data variables.copilot.copilot_chat_short %} in your IDE.
defaultTool: vscode
versions:
  feature: copilot
redirect_from:
  - /copilot/using-github-copilot/example-use-cases/example-prompts-for-copilot-chat
  - /copilot/using-github-copilot/guides-on-using-github-copilot/getting-started-with-prompts-for-copilot-chat
  - /copilot/using-github-copilot/copilot-chat/getting-started-with-prompts-for-copilot-chat
  - /copilot/get-started/getting-started-with-prompts-for-copilot-chat
  - /copilot/how-tos/chat/getting-started-with-prompts-for-copilot-chat
  - /copilot/how-tos/chat/get-started-with-chat
  - /copilot/how-tos/use-chat/get-started-with-chat
  - /copilot/how-tos/chat-with-copilot/get-started-with-chat
  - /copilot/how-tos/chat-with-copilot/get-started-with-chat-in-your-ide
  - /copilot/tutorials/copilot-cookbook/refactor-code/fix-lint-errors
  - /copilot/example-prompts-for-github-copilot-chat/refactoring-code/fixing-lint-errors
  - /copilot/copilot-chat-cookbook/refactoring-code/fixing-lint-errors
  - /copilot/tutorials/copilot-chat-cookbook/refactoring-code/fixing-lint-errors
  - /copilot/tutorials/copilot-chat-cookbook/refactoring-code/fix-lint-errors
  - /copilot/tutorials/copilot-chat-cookbook/refactor-code/fix-lint-errors
shortTitle: Get started with Chat in your IDE
contentType: how-tos
category:
  - Author and optimize with Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

您可以询问 {% data variables.copilot.copilot_chat_short %} 有关您的项目的具体问题或一般软件问题。您还可以要求 {% data variables.copilot.copilot_chat_short %} 编写代码、修复错误、编写测试和记录代码。

使用上面的选项卡选择您正在使用 {% data variables.copilot.copilot_chat_short %} 的环境。

{% vscode %}

<a href="vscode://GitHub.Copilot-Chat?ref_product=copilot&ref_type=engagement&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 mb-3 no-underline">
 <span>在{% data variables.product.prodname_vscode %}</span> {% octicon "link-external" height:16 aria-label="link-external" %}
</a>中打开{% data variables.copilot.copilot_chat_short %}

以下一些示例提示使用聊天参与者（前面为 `@`）、斜杠命令（前面为 `/`）或聊天变量（前面为 `#`）。有关提示中关键字的更多信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide#using-keywords-in-your-prompt>)。

## 询问一般软件问题

您可以询问{% data variables.copilot.copilot_chat_short %}一般软件问题。例如：

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `@terminal how to update an npm package`

## 询问有关您的项目的问题

您可以询问 {% data variables.copilot.copilot_chat_short %} 有关您的项目的问题。

* `what sorting algorithm does this function use`
* `@workspace how are notifications scheduled`
* `#file:gameReducer.js #file:gameInit.js how are these files related`

要为 {% data variables.product.prodname_copilot_short %} 提供正确的上下文，请尝试以下一些策略：

* 突出显示相关代码行。
* 使用聊天变量，如 `#selection`、`#file`、`#editor`、`#codebase` 或 `#git`。
* 使用 `@workspace` 聊天参与者。

## 编写代码

您可以要求{% data variables.product.prodname_copilot_short %}为您编写代码。例如：

* `write a function to sum all numbers in a list`
* `add error handling to this function`
* `@workspace add form validation, similar to the newsletter page`

当 {% data variables.product.prodname_copilot_short %} 返回代码块时，响应包括复制代码或将代码插入到 cursor、新文件或终端的选项。

## 询问有关 {% data variables.product.prodname_GHAS %} 功能警报的问题

您可以通过 {% data variables.product.prodname_GHAS %} 功能（{% data variables.product.prodname_code_scanning %}、{% data variables.product.prodname_secret_scanning %} 和 {% data variables.product.prodname_dependabot_alerts %}）向 {% data variables.product.prodname_copilot_short %} 询问组织中存储库中的安全警报。例如：

* `How would I fix this alert?`
* `How many alerts do I have on this pull request?`
* `Which line of code is this {% data variables.product.prodname_code_scanning %} alert referencing?`
* `What library is affected by this {% data variables.product.prodname_dependabot %} alert?`

## 建立一个新项目

使用 `/new` 斜杠命令设置新项目。例如：

* `/new react app with typescript`
* `/new python django web application`
* `/new node.js express server`

Copilot 将建议目录结构并提供一个按钮来创建建议的文件和内容。要预览建议的文件，请在建议的目录结构中选择文件名。

使用 `/newNotebook` 斜杠命令设置新的 Jupyter 笔记本。例如：

* `/newNotebook retrieve the titanic dataset and use Seaborn to plot the data`

## 修复、改进和重构代码

如果您的活动文件包含错误，请使用 `/fix` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 修复错误。

您还可以提出改进或重构代码的一般请求。

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## 编写测试

使用 `/tests` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 为活动文件或所选代码编写测试。例如：

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

`/tests` 斜杠命令为现有代码编写测试。如果您喜欢在编写代码之前编写测试（测试驱动开发），请省略 `/tests` 命令。例如：

* `Add tests for a JavaScript function that should sum a list of integers`

## 询问有关 {% data variables.product.prodname_vscode %} 的问题

使用 `@vscode` 聊天参与者询问有关 {% data variables.product.prodname_vscode %} 的具体问题。例如：

* `@vscode tell me how to debug a node.js app`
* `@vscode how do I change my {% data variables.product.prodname_vscode %} colors`
* `@vscode how can I change key bindings`

## 询问有关命令行的问题

使用 `@terminal` 聊天参与者询问有关命令行的具体问题。例如：

* `@terminal find the largest file in the src directory`
* `@terminal #terminalLastCommand` 解释最后一个命令和任何错误

{% endvscode %}

{% visualstudio %}

## 询问一般软件问题

您可以询问{% data variables.copilot.copilot_chat_short %}一般软件问题。例如：

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## 询问有关您的项目的问题

您可以询问 {% data variables.copilot.copilot_chat_short %} 有关您的项目的问题。要为 {% data variables.product.prodname_copilot_short %} 提供正确的上下文，请尝试以下一些策略：

* 突出显示相关代码行。
* 打开相关文件。
* 使用`#file`告诉{% data variables.product.prodname_copilot_short %}引用特定文件。
* 使用`#solution`告诉{% data variables.product.prodname_copilot_short %}引用活动文件。

例如：

* `what sorting algorithm does this function use`
* `#file:gameReducer.js what happens when a new game is requested`

## 编写代码

您可以要求{% data variables.product.prodname_copilot_short %}为您编写代码。例如：

* `write a function to sum all numbers in a list`
* `add error handling to this function`

当 {% data variables.product.prodname_copilot_short %} 返回代码块时，响应包括复制代码、将代码插入新文件或预览代码输出的选项。

## 询问有关 {% data variables.product.prodname_GHAS %} 功能警报的问题

您可以通过 {% data variables.product.prodname_GHAS %} 功能（{% data variables.product.prodname_code_scanning %}、{% data variables.product.prodname_secret_scanning %} 和 {% data variables.product.prodname_dependabot_alerts %}）向 {% data variables.product.prodname_copilot_short %} 询问组织中存储库中的安全警报。例如：

* `How would I fix this alert?`
* `How many alerts do I have on this pull request?`
* `Which line of code is this {% data variables.product.prodname_code_scanning %} alert referencing?`
* `What library is affected by this {% data variables.product.prodname_dependabot %} alert?`

## 修复、改进和重构代码

如果您的活动文件包含错误，请使用 `/fix` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 修复错误。

您还可以提出改进或重构代码的一般请求。

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## 编写测试

使用 `/tests` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 为活动文件或所选代码编写测试。例如：

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

`/tests` 斜杠命令为现有代码编写测试。如果您喜欢在编写代码之前编写测试（测试驱动开发），请省略 `/tests` 命令。例如：

* `Add tests for a JavaScript function that should sum a list of integers`

{% endvisualstudio %}

{% jetbrains %}

## 询问一般软件问题

您可以询问{% data variables.copilot.copilot_chat_short %}一般软件问题。例如：

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## 询问有关您的项目的问题

您可以询问 {% data variables.copilot.copilot_chat_short %} 有关您的项目的问题。要为 {% data variables.product.prodname_copilot_short %} 提供正确的上下文，请尝试以下一些策略：

* 突出显示相关代码行。
* 打开相关文件。
* 添加文件作为参考。有关如何使用文件引用的信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide?tool=jetbrains#file-references>)。
* 使用 `@project` 聊天参与者。

例如：

* `what sorting algorithm does this function use`
* `how are these files related`（参考有问题的文件）
* `@project how are notifications scheduled`

## 编写代码

您可以要求{% data variables.product.prodname_copilot_short %}为您编写代码。例如：

* `write a function to sum all numbers in a list`
* `add error handling to this function`

当 {% data variables.product.prodname_copilot_short %} 返回代码块时，响应包括复制代码或在 cursor 处插入代码的选项。

## 修复、改进和重构代码

如果您的活动文件包含错误，请使用 `/fix` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 修复错误。

您还可以提出改进或重构代码的一般请求。

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## 编写测试

使用 `/tests` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 为活动文件或所选代码编写测试。例如：

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

`/tests` 斜杠命令为现有代码编写测试。如果您喜欢在编写代码之前编写测试（测试驱动开发），请省略 `/tests` 命令。例如：

* `Add tests for a JavaScript function that should sum a list of integers`

{% endjetbrains %}

{% eclipse %}

## 询问一般软件问题

您可以询问{% data variables.copilot.copilot_chat_short %}一般软件问题。例如：

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## 询问有关项目文件的问题

您可以询问 {% data variables.copilot.copilot_chat_short %} 有关编辑器中当前显示的文件的问题，或者有关您在 {% data variables.copilot.copilot_chat_short %} 面板中附加到对话的文件的问题。为 {% data variables.product.prodname_copilot_short %} 提供正确的上下文：

* 在编辑器中打开相关文件。
* 单击 {% data variables.copilot.copilot_chat_short %} 面板中的回形针图标，然后搜索并选择要附加到对话的文件。

例如：

* `how can I make this file run faster`
* `how are these files related`（有两个或更多附件）
* `explain the getSearchReplaceRules function`

## 编写代码

您可以要求{% data variables.product.prodname_copilot_short %}为您编写代码。例如：

* `write a TypeScript function to sum all numbers in a list`
* `using the comments in this file, create appropriate Node JavaScript`

当 {% data variables.product.prodname_copilot_short %} 返回代码块时，响应包含复制代码的选项。

## 修复、改进和重构代码

如果您的活动文件包含错误，请使用 `/fix` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 修复错误。

您还可以提出改进或重构代码的一般请求。

* `how would you improve the code in this file`
* `translate this code to C#`
* `add error handling to the main function`

## 编写测试

使用 `/tests` 斜杠命令要求 {% data variables.product.prodname_copilot_short %} 为活动文件或所选代码编写测试。例如：

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

`/tests` 斜杠命令为现有代码编写测试。如果您喜欢在编写代码之前编写测试（测试驱动开发），请省略 `/tests` 命令。例如：

* `Add tests for a JavaScript function that should sum a list of integers`

{% endeclipse %}


## 原文对照

<details>
<summary>展开完整原文</summary>

You can ask {% data variables.copilot.copilot_chat_short %} specific questions about your project or general software questions. You can also ask {% data variables.copilot.copilot_chat_short %} to write code, fix errors, write tests, and document code.

Use the tabs above to select the environment where you are using {% data variables.copilot.copilot_chat_short %}.

{% vscode %}

<a href="vscode://GitHub.Copilot-Chat?ref_product=copilot&ref_type=engagement&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 mb-3 no-underline">
    <span>Open {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vscode %}</span> {% octicon "link-external" height:16 aria-label="link-external" %}
</a>

Some of the following example prompts use chat participants (preceded by `@`), slash commands (preceded by `/`), or chat variables (preceded by `#`). For more information on keywords in prompts, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide#using-keywords-in-your-prompt>).

## Ask general software questions

You can ask {% data variables.copilot.copilot_chat_short %} general software questions. For example:

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `@terminal how to update an npm package`

## Ask questions about your project

You can ask {% data variables.copilot.copilot_chat_short %} questions about your project.

* `what sorting algorithm does this function use`
* `@workspace how are notifications scheduled`
* `#file:gameReducer.js #file:gameInit.js how are these files related`

To give {% data variables.product.prodname_copilot_short %} the correct context, try some of these strategies:

* Highlight relevant lines of code.
* Use chat variables like `#selection`, `#file`, `#editor`, `#codebase`, or `#git`.
* Use the `@workspace` chat participant.

## Write code

You can ask {% data variables.product.prodname_copilot_short %} to write code for you. For example:

* `write a function to sum all numbers in a list`
* `add error handling to this function`
* `@workspace add form validation, similar to the newsletter page`

When {% data variables.product.prodname_copilot_short %} returns a code block, the response includes options to copy the code, or to insert the code at your cursor, into a new file, or into the terminal.

## Ask questions about alerts from {% data variables.product.prodname_GHAS %} features

You can ask {% data variables.product.prodname_copilot_short %} about security alerts in repositories in your organization from {% data variables.product.prodname_GHAS %} features ({% data variables.product.prodname_code_scanning %}, {% data variables.product.prodname_secret_scanning %}, and {% data variables.product.prodname_dependabot_alerts %}). For example:

* `How would I fix this alert?`
* `How many alerts do I have on this pull request?`
* `Which line of code is this {% data variables.product.prodname_code_scanning %} alert referencing?`
* `What library is affected by this {% data variables.product.prodname_dependabot %} alert?`

## Set up a new project

Use the `/new` slash command to set up a new project. For example:

* `/new react app with typescript`
* `/new python django web application`
* `/new node.js express server`

Copilot will suggest a directory structure and provide a button to create the suggested files and contents. To preview a suggested file, select the file name in the suggested directory structure.

Use the `/newNotebook` slash command to set up a new Jupyter notebook. For example:

* `/newNotebook retrieve the titanic dataset and use Seaborn to plot the data`

## Fix, improve, and refactor code

If your active file contains an error, use the `/fix` slash command to ask {% data variables.product.prodname_copilot_short %} to fix the error.

You can also make general requests to improve or refactor your code.

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## Write tests

Use the `/tests` slash command to ask {% data variables.product.prodname_copilot_short %} to write tests for the active file or selected code. For example:

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

The `/tests` slash command writes tests for existing code. If you prefer to write tests before writing code (test driven development), omit the `/tests` command. For example:

* `Add tests for a JavaScript function that should sum a list of integers`

## Ask questions about {% data variables.product.prodname_vscode %}

Use the `@vscode` chat participant to ask specific questions about {% data variables.product.prodname_vscode %}. For example:

* `@vscode tell me how to debug a node.js app`
* `@vscode how do I change my {% data variables.product.prodname_vscode %} colors`
* `@vscode how can I change key bindings`

## Ask questions about the command line

Use the `@terminal` chat participant to ask specific questions about the command line. For example:

* `@terminal find the largest file in the src directory`
* `@terminal #terminalLastCommand` to explain the last command and any errors

{% endvscode %}

{% visualstudio %}

## Ask general software questions

You can ask {% data variables.copilot.copilot_chat_short %} general software questions. For example:

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## Ask questions about your project

You can ask {% data variables.copilot.copilot_chat_short %} questions about your project. To give {% data variables.product.prodname_copilot_short %} the correct context, try some of these strategies:

* Highlight relevant lines of code.
* Open the relevant file.
* Use `#file` to tell {% data variables.product.prodname_copilot_short %} to reference specific files.
* Use `#solution` to tell {% data variables.product.prodname_copilot_short %} to reference the active file.

For example:

* `what sorting algorithm does this function use`
* `#file:gameReducer.js what happens when a new game is requested`

## Write code

You can ask {% data variables.product.prodname_copilot_short %} to write code for you. For example:

* `write a function to sum all numbers in a list`
* `add error handling to this function`

When {% data variables.product.prodname_copilot_short %} returns a code block, the response includes options to copy the code, insert the code into a new file, or preview the code output.

## Ask questions about alerts from {% data variables.product.prodname_GHAS %} features

You can ask {% data variables.product.prodname_copilot_short %} about security alerts in repositories in your organization from {% data variables.product.prodname_GHAS %} features ({% data variables.product.prodname_code_scanning %}, {% data variables.product.prodname_secret_scanning %}, and {% data variables.product.prodname_dependabot_alerts %}). For example:

* `How would I fix this alert?`
* `How many alerts do I have on this pull request?`
* `Which line of code is this {% data variables.product.prodname_code_scanning %} alert referencing?`
* `What library is affected by this {% data variables.product.prodname_dependabot %} alert?`

## Fix, improve, and refactor code

If your active file contains an error, use the `/fix` slash command to ask {% data variables.product.prodname_copilot_short %} to fix the error.

You can also make general requests to improve or refactor your code.

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## Write tests

Use the `/tests` slash command to ask {% data variables.product.prodname_copilot_short %} to write tests for the active file or selected code. For example:

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

The `/tests` slash command writes tests for existing code. If you prefer to write tests before writing code (test driven development), omit the `/tests` command. For example:

* `Add tests for a JavaScript function that should sum a list of integers`

{% endvisualstudio %}

{% jetbrains %}

## Ask general software questions

You can ask {% data variables.copilot.copilot_chat_short %} general software questions. For example:

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## Ask questions about your project

You can ask {% data variables.copilot.copilot_chat_short %} questions about your project. To give {% data variables.product.prodname_copilot_short %} the correct context, try some of these strategies:

* Highlight relevant lines of code.
* Open the relevant file.
* Add the file as a reference. For information about how to use file references, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide?tool=jetbrains#file-references>).
* Use the `@project` chat participant.

For example:

* `what sorting algorithm does this function use`
* `how are these files related` (with references to the files in question)
* `@project how are notifications scheduled`

## Write code

You can ask {% data variables.product.prodname_copilot_short %} to write code for you. For example:

* `write a function to sum all numbers in a list`
* `add error handling to this function`

When {% data variables.product.prodname_copilot_short %} returns a code block, the response includes options to copy the code or to insert the code at your cursor.

## Fix, improve, and refactor code

If your active file contains an error, use the `/fix` slash command to ask {% data variables.product.prodname_copilot_short %} to fix the error.

You can also make general requests to improve or refactor your code.

* `how would you improve this code?`
* `translate this code to C#`
* `add error handling to this function`

## Write tests

Use the `/tests` slash command to ask {% data variables.product.prodname_copilot_short %} to write tests for the active file or selected code. For example:

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

The `/tests` slash command writes tests for existing code. If you prefer to write tests before writing code (test driven development), omit the `/tests` command. For example:

* `Add tests for a JavaScript function that should sum a list of integers`

{% endjetbrains %}

{% eclipse %}

## Ask general software questions

You can ask {% data variables.copilot.copilot_chat_short %} general software questions. For example:

* `tell me about nodejs web server frameworks`
* `how can I create an Express app`
* `what's the process for updating an npm package`

## Ask questions about files your project

You can ask {% data variables.copilot.copilot_chat_short %} questions about the file that's currently displayed in the editor, or about files you have attached to your conversation in the {% data variables.copilot.copilot_chat_short %} panel. To give {% data variables.product.prodname_copilot_short %} the correct context:

* Open the relevant file in the editor.
* Click the paperclip icon in the {% data variables.copilot.copilot_chat_short %} panel, then search for and select files you want to attach to the conversation.

For example:

* `how can I make this file run faster`
* `how are these files related` (with two or more attached files)
* `explain the getSearchReplaceRules function`

## Write code

You can ask {% data variables.product.prodname_copilot_short %} to write code for you. For example:

* `write a TypeScript function to sum all numbers in a list`
* `using the comments in this file, create appropriate Node JavaScript`

When {% data variables.product.prodname_copilot_short %} returns a code block, the response includes options to copy the code.

## Fix, improve, and refactor code

If your active file contains an error, use the `/fix` slash command to ask {% data variables.product.prodname_copilot_short %} to fix the error.

You can also make general requests to improve or refactor your code.

* `how would you improve the code in this file`
* `translate this code to C#`
* `add error handling to the main function`

## Write tests

Use the `/tests` slash command to ask {% data variables.product.prodname_copilot_short %} to write tests for the active file or selected code. For example:

* `/tests`
* `/tests using the Jest framework`
* `/tests ensure the function rejects an empty list`

The `/tests` slash command writes tests for existing code. If you prefer to write tests before writing code (test driven development), omit the `/tests` command. For example:

* `Add tests for a JavaScript function that should sum a list of integers`

{% endeclipse %}


</details>
