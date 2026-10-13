# Copilot Chat 提示工程

介绍从总体到具体描述任务、提供示例、拆分复杂工作、消除歧义、指定代码上下文和迭代提示的方法。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：源码提交（存档版）：[2026-09-21](<https://github.com/github/docs/commit/e69e757062188deff7ecfc8091d9876189c0fac6>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/concepts/prompting/prompt-engineering.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Chat 提示工程.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Prompt engineering for GitHub Copilot Chat
shortTitle: Prompt engineering
intro: Follow these strategies to improve your {% data variables.product.prodname_copilot_short %} results.
versions:
  feature: copilot
redirect_from:
  - /copilot/using-github-copilot/prompt-engineering-for-github-copilot
  - /copilot/using-github-copilot/copilot-chat/prompt-engineering-for-copilot-chat
  - /copilot/concepts/prompt-engineering-for-copilot-chat
  - /copilot/concepts/prompt-engineering
  - /copilot/tutorials/copilot-cookbook/document-code
  - /copilot/example-prompts-for-github-copilot-chat/documenting-code
  - /copilot/copilot-chat-cookbook/documenting-code
  - /copilot/tutorials/copilot-chat-cookbook/documenting-code
  - /copilot/tutorials/copilot-chat-cookbook/document-code
  - /copilot/tutorials/copilot-cookbook/document-code/document-legacy-code
  - /copilot/example-prompts-for-github-copilot-chat/documenting-code/documenting-legacy-code
  - /copilot/copilot-chat-cookbook/documenting-code/documenting-legacy-code
  - /copilot/tutorials/copilot-chat-cookbook/documenting-code/documenting-legacy-code
  - /copilot/tutorials/copilot-chat-cookbook/documenting-code/document-legacy-code
  - /copilot/tutorials/copilot-chat-cookbook/document-code/document-legacy-code
  - /copilot/tutorials/copilot-cookbook/document-code/sync-documentation
  - /copilot/example-prompts-for-github-copilot-chat/documenting-code/syncing-documentation-with-code-changes
  - /copilot/copilot-chat-cookbook/documenting-code/syncing-documentation-with-code-changes
  - /copilot/tutorials/copilot-chat-cookbook/documenting-code/syncing-documentation-with-code-changes
  - /copilot/tutorials/copilot-chat-cookbook/documenting-code/sync-documentation
  - /copilot/tutorials/copilot-chat-cookbook/document-code/sync-documentation
contentType: concepts
category:
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

提示是您向 {% data variables.product.prodname_copilot %} 提出的请求。例如，您提出的问题 {% data variables.copilot.copilot_chat_short %}，或者您要求 {% data variables.product.prodname_copilot_short %} 完成的代码片段。除了提示之外，{% data variables.product.prodname_copilot_short %} 还使用其他上下文（例如当前文件中的代码和聊天历史记录）来生成响应。

按照本文中的提示编写提示，以便从 {% data variables.product.prodname_copilot_short %} 生成更好的响应。

## 从一般性开始，然后具体

在编写 {% data variables.product.prodname_copilot_short %} 提示时，首先对 {% data variables.product.prodname_copilot_short %} 目标或场景进行广泛描述。然后列出所有具体要求。

例如：

```copilot copy prompt
Write a JavaScript function that tells me if a number is prime

The function should take an integer and return true if the integer is prime

The function should error if the input is not a positive integer
```

## 举例说明

使用示例帮助 {% data variables.product.prodname_copilot_short %} 理解您想要什么。您可以提供示例输入数据、示例输出和示例实现。

例如：

```copilot copy prompt
Write a Go function that finds all dates in a string and returns them in an array. Dates can be formatted like:

* 05/02/24
* 05/02/2024
* 5/2/24
* 5/2/2024
* 05-02-24
* 05-02-2024
* 5-2-24
* 5-2-2024

Example:

findDates("I have a dentist appointment on 11/14/2023 and book club on 12-1-23")

Returns: ["11/14/2023", "12-1-23"]
```

单元测试也可以作为示例。在编写函数之前，您可以使用 {% data variables.product.prodname_copilot_short %} 为该函数编写单元测试。然后，您可以要求{% data variables.product.prodname_copilot_short %}编写这些单元测试描述的函数。

## 将复杂的任务分解为更简单的任务

如果您希望{% data variables.product.prodname_copilot_short %}完成一项复杂或大型任务，请将任务分解为多个简单的小任务。

例如，不要要求 {% data variables.product.prodname_copilot_short %} 生成单词搜索谜题，而是将过程分解为更小的任务，然后要求 {% data variables.product.prodname_copilot_short %} 一项一项地完成它们：

* 编写一个函数，通过 10 字母网格生成 10。
* 编写一个函数，在给定有效单词列表的情况下查找字母网格中的所有单词。
* 编写一个函数，使用前面的函数生成 10 由 10 字母组成的网格，其中至少包含 10 个单词。
* 更新之前的函数以打印字母网格和网格中的 10 随机单词。

## 避免歧义

避免含糊不清的术语。例如，如果“this”可能是当前文件、最后一个 {% data variables.product.prodname_copilot_short %} 响应或特定代码块，则不要问“这是做什么的”。相反，要具体一些：

* `createUser`功能有什么作用？
* 您上次回复中的代码有什么作用？

歧义也适用于图书馆：

* 如果您使用的是不常见的库，请描述该库的用途。
* 如果要使用特定库，请在文件顶部设置导入语句或指定要使用的库。

## 注明相关代码

如果您在编码时使用 IDE 中的 {% data variables.product.prodname_copilot_short %} 来获取建议，请打开所有相关文件并关闭不相关文件。 {% data variables.product.prodname_copilot_short %}将使用打开的文件来了解您的请求。

如果您在 IDE 中使用 {% data variables.copilot.copilot_chat_short %}，请打开文件或突出显示您想要 {% data variables.product.prodname_copilot_short %} 引用的代码。您还可以使用关键字手动向 {% data variables.copilot.copilot_chat_short %} 提供上下文。例如，您可以在 VS Code 中添加 `@workspace` 聊天参与者，或在 JetBrains IDE 中添加 `@project`。参见[AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/chat-cheat-sheet>)。

## 实验和迭代

如果您没有得到想要的结果，请迭代提示并重试。

如果您在编码时使用 {% data variables.product.prodname_copilot_short %} 获取建议，则可以完全删除该建议并重新开始。或者您可以保留建议并请求修改。

如果您使用的是 {% data variables.copilot.copilot_chat_short %}，您可以在下一个请求中引用之前的响应。或者，您可以删除之前的回复并重新开始。

## 保持历史相关性

{% data variables.copilot.copilot_chat_short %} 使用聊天历史记录来获取有关您的请求的上下文。仅提供 {% data variables.product.prodname_copilot_short %} 相关历史记录：

* 使用线程为新任务启动新对话
* 删除不再相关或未给您带来所需结果的请求

## 遵循良好的编码实践

如果您在代码库中向 {% data variables.product.prodname_copilot_short %} 寻求建议或解释时没有得到您想要的答复，请确保您现有的代码遵循最佳实践并且易于阅读。例如：

* 使用一致的代码风格和模式
* 对变量和函数使用描述性名称
* 评论你的代码
* 将您的代码构建为模块化的、有范围的组件
* 包括单元测试

>[！提示]
> 使用{% data variables.product.prodname_copilot_short %}帮助您的代码遵循最佳实践。例如，要求{% data variables.product.prodname_copilot_short %}添加注释或将大函数分解为更小的函数。

## 进一步阅读

* [如何使用GitHub Copilot：{% data variables.product.company_short %}博客中的提示、技巧和用例](<https://github.blog/2023-06-20-how-to-write-better-prompts-for-github-copilot/?ref_product=copilot&ref_type=engagement&ref_style=text>)
* [在 IDE 中使用 GitHub Copilot：{% data variables.product.company_short %} 博客中的提示、技巧和最佳实践](<https://github.blog/2024-03-25-how-to-use-github-copilot-in-your-ide-tips-tricks-and-best-practices/?ref_product=copilot&ref_type=engagement&ref_style=text>)
* [{% data variables.product.company_short %} 博客中的提示工程和 LLM 的开发人员指南 ](<https://github.blog/2023-07-17-prompt-engineering-guide-generative-ai-llms/>)
* [在{% data variables.product.company_short %}博客中提示GitHub Copilot聊天成为您的个人AI辅助功能](<https://github.blog/2023-10-09-prompting-github-copilot-chat-to-become-your-personal-ai-assistant-for-accessibility/?ref_product=copilot&ref_type=engagement&ref_style=text>)


## 原文对照

<details>
<summary>展开完整原文</summary>

A prompt is a request that you make to {% data variables.product.prodname_copilot %}. For example, a question that you ask {% data variables.copilot.copilot_chat_short %}, or a code snippet that you ask {% data variables.product.prodname_copilot_short %} to complete. In addition to your prompt, {% data variables.product.prodname_copilot_short %} uses additional context, like the code in your current file and the chat history, to generate a response.

Follow the tips in this article to write prompts that generate better responses from {% data variables.product.prodname_copilot_short %}.

## Start general, then get specific

When writing a prompt for {% data variables.product.prodname_copilot_short %}, first give {% data variables.product.prodname_copilot_short %} a broad description of the goal or scenario. Then list any specific requirements.

For example:

```copilot copy prompt
Write a JavaScript function that tells me if a number is prime

The function should take an integer and return true if the integer is prime

The function should error if the input is not a positive integer
```

## Give examples

Use examples to help {% data variables.product.prodname_copilot_short %} understand what you want. You can provide example input data, example outputs, and example implementations.

For example:

```copilot copy prompt
Write a Go function that finds all dates in a string and returns them in an array. Dates can be formatted like:

* 05/02/24
* 05/02/2024
* 5/2/24
* 5/2/2024
* 05-02-24
* 05-02-2024
* 5-2-24
* 5-2-2024

Example:

findDates("I have a dentist appointment on 11/14/2023 and book club on 12-1-23")

Returns: ["11/14/2023", "12-1-23"]
```

Unit tests can also serve as examples. Before writing your function, you can use {% data variables.product.prodname_copilot_short %} to write unit tests for the function. Then, you can ask {% data variables.product.prodname_copilot_short %} to write a function described by those unit tests.

## Break complex tasks into simpler tasks

If you want {% data variables.product.prodname_copilot_short %} to complete a complex or large task, break the task into multiple simple, small tasks.

For example, instead of asking {% data variables.product.prodname_copilot_short %} to generate a word search puzzle, break the process down into smaller tasks, and ask {% data variables.product.prodname_copilot_short %} to accomplish them one by one:

* Write a function to generate a 10 by 10 grid of letters.
* Write a function to find all words in a grid of letters, given a list of valid words.
* Write a function that uses the previous functions to generate a 10 by 10 grid of letters that contains at least 10 words.
* Update the previous function to print the grid of letters and 10 random words from the grid.

## Avoid ambiguity

Avoid ambiguous terms. For example, don’t ask "what does this do" if "this" could be the current file, the last {% data variables.product.prodname_copilot_short %} response, or a specific code block. Instead, be specific:

* What does the `createUser` function do?
* What does the code in your last response do?

Ambiguity can also apply to libraries:

* If you are using an uncommon library, describe what the library does.
* If you want to use a specific library, set the import statements at the top of the file or specify what library you want to use.

## Indicate relevant code

If you are using {% data variables.product.prodname_copilot_short %} in your IDE to get suggestions as you code, open any relevant files and close irrelevant files. {% data variables.product.prodname_copilot_short %} will use the open files to understand your request.

If you are using {% data variables.copilot.copilot_chat_short %} in your IDE, open the file or highlight the code that you want {% data variables.product.prodname_copilot_short %} to reference. You can also use keywords to manually supply context to {% data variables.copilot.copilot_chat_short %}. For example, you can add the `@workspace` chat participant in VS Code, or `@project` in JetBrains IDEs. See [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/chat-cheat-sheet>).

## Experiment and iterate

If you don’t get the result that you want, iterate on your prompt and try again.

If you are using {% data variables.product.prodname_copilot_short %} to get suggestions as you code, you can delete the suggestion entirely and start over. Or you can keep the suggestion and request modifications.

If you are using {% data variables.copilot.copilot_chat_short %}, you can reference the previous response in your next request. Or, you can delete the previous response and start over.

## Keep history relevant

{% data variables.copilot.copilot_chat_short %} uses the chat history to get context about your request. To give {% data variables.product.prodname_copilot_short %} only the relevant history:

* Use threads to start a new conversation for a new task
* Delete requests that are no longer relevant or that didn’t give you the desired result

## Follow good coding practices

If you aren't getting the responses you want when you ask {% data variables.product.prodname_copilot_short %} for suggestions or explanations in your codebase, make sure that your existing code follows best practices and is easy to read. For example:

* Use a consistent code style and patterns
* Use descriptive names for variables and functions
* Comment your code
* Structure your code into modular, scoped components
* Include unit tests

>[!Tip]
> Use {% data variables.product.prodname_copilot_short %} to help your code follow best practices. For example, ask {% data variables.product.prodname_copilot_short %} to add comments or to break a large function into smaller functions.

## Further reading

* [How to use GitHub Copilot: Prompts, tips, and use cases](<https://github.blog/2023-06-20-how-to-write-better-prompts-for-github-copilot/?ref_product=copilot&ref_type=engagement&ref_style=text>) in the {% data variables.product.company_short %} blog
* [Using GitHub Copilot in your IDE: Tips, tricks, and best practices](<https://github.blog/2024-03-25-how-to-use-github-copilot-in-your-ide-tips-tricks-and-best-practices/?ref_product=copilot&ref_type=engagement&ref_style=text>) in the {% data variables.product.company_short %} blog
* [A developer’s guide to prompt engineering and LLMs](<https://github.blog/2023-07-17-prompt-engineering-guide-generative-ai-llms/>) in the {% data variables.product.company_short %} blog
* [Prompting GitHub Copilot Chat to become your personal AI assistant for accessibility](<https://github.blog/2023-10-09-prompting-github-copilot-chat-to-become-your-personal-ai-assistant-for-accessibility/?ref_product=copilot&ref_type=engagement&ref_style=text>) in the {% data variables.product.company_short %} blog


</details>
