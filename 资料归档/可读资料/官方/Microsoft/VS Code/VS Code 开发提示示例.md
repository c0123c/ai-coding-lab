# VS Code 开发提示示例

覆盖理解仓库、生成修改、测试、调试、Git、外部工具、终端和 Notebook，给出多轮协作提示参考。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/prompt-examples.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/prompt-examples.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code 开发提示示例.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 9d8f3a2b-5c6e-4f7a-8b9c-1d2e3f4a5b6c
DateApproved: 10/7/2026
MetaDescription: Use chat prompts in {% data variables.product.prodname_vscode_shortname %} to explore code, build features, refactor, debug, and test.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 提示示例

本文提供了跨不同场景和 agents 在 {% data variables.product.prodname_vscode %} 中聊天的示例提示。使用这些示例作为灵感，为您自己的开发任务制作有效的提示。

如果您不熟悉在 {% data variables.product.prodname_vscode_shortname %} 中使用聊天，请详细了解 [聊天入门 ](<https://github.com/docs/chat/chat-overview.md>) 或查看 [提示制作的最佳实践 ](<https://github.com/docs/agents/best-practices.md>)。

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
按照实践教程使用AI agents在{% data variables.product.prodname_vscode_shortname %}中构建应用程序。

* [开始agentic编码教程](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## 一般编码和技术问题

使用 **Ask** agent 获得有关编码概念、技术主题和一般编程问题的快速解答。

```prompt-ask
What is a linked list?
```

```prompt-ask
Provide 3 ways to implement a search feature in React.
```

```prompt-ask
Explain the difference between async/await and promises.
```

## 了解和探索您的代码库

使用 ** 询问 ** agent 和 `#codebase` 来了解您的项目如何工作、找到特定功能或探索代码关系。

有关源参考和验证步骤的指导调查，请参阅 [使用 agent](<https://github.com/docs/agents/guides/explore-a-codebase.md>) 探索代码库。

```prompt-ask
Explain how authentication works in #codebase
```

```prompt-ask
Where is the database connection string configured? #codebase
```

```prompt-ask
How do I build this #codebase?
```

```prompt-ask
Which testing framework is used for #calculator.test.js?
```

## 代码生成和编辑

使用 **Agent** 进行多文件创建，使用 ** 内联聊天 ** (`kb(inlinechat.start)`) 进行有针对性的就地编辑。

有关现有项目中的完整工作流程，请参阅[添加功能](<https://github.com/docs/agents/guides/add-a-feature.md>)和[重构而不更改行为](<https://github.com/docs/agents/guides/refactor-safely.md>)。

```prompt
Add a login button and style it based on #styles.css
```

```prompt
Create a meal-planning web app using React and Node.js
```

```prompt
Refactor this code to use async/await
```

## 测试和质量保证

使用 **Agent** 生成测试或修复失败的测试。

```prompt
Add unit tests for the user service.
```

```prompt
Fix the failing tests #testFailure
```

## 调试和修复问题

使用 **Agent** 修复跨文件的问题，或 ** 询问 ** 首先了解根本原因。

```prompt
Fix the issues in #problems
```

```prompt
Why is this function returning undefined?
```

## 使用源代码管理

使用聊天来处理待处理的更改并生成发布文档。

```prompt
Summarize the #changes
```

```prompt
Generate release notes based on the #changes
```

## 使用外部资源

使用 `#fetch` 引用网络中的内容或使用 GitHub MCP 服务器在公共 GitHub 存储库中搜索代码示例。

```prompt
How do I use the 'useState' hook in react 18? #fetch https://18.react.dev/reference/react/useState#usage
```

```prompt
Build an API endpoint to fetch address info, use the template from GitHub repo contoso/api-templates
```

```prompt
What are the top #extensions for this workspace?
```

## 终端和命令行任务

使用 [ 终端内联聊天 ](<https://github.com/docs/chat/inline-chat.md#use-terminal-inline-chat>) 获取有关 shell 命令和终端操作的帮助。

```prompt
How do I install npm packages?
```

```prompt
List the top 5 largest files in the src directory
```

```prompt
undo the last git commit
```

## 使用 Jupyter 笔记本

使用 **Agent** 创建、编辑和使用 Jupyter 笔记本。

```prompt
/newNotebook use pandas and seaborn to read and visualize the titanic dataset. Show key information from the dataset.
```

```prompt
Create a notebook to read data from #housing.csv and plot the distribution of prices
```

```prompt
Make sure the data is cleaned before visualizing and processing it
```

```prompt
Show the correlation between different features in the dataset
```

## 多轮对话示例

聊天支持同一会话中的后续提示。使用多轮对话迭代结果并优化 AI 的输出。

**第一次提示：**

```prompt
Create a REST API with Express.js that has endpoints for users and products
```

**后续提示：**

```prompt
Add input validation and error handling to both endpoints
```

```prompt
Now add unit tests for the validation logic
```

通过建立在早期响应的基础上，AI 维护了先前步骤的上下文并生成更连贯的代码。

## 制作有效提示的技巧

* **具体**：包括有关您想要完成的任务、要使用的技术以及预期输出格式的详细信息。
* **添加上下文**：使用 # 提及来引用文件、符号或上下文变量，例如 `#codebase`、`#changes` 或 `#problems`。
* **迭代**：从一个简单的提示开始，并根据响应进行完善。提出后续问题以改善结果。
* **分解复杂的任务**：不要一次性要求所有事情，而是将大任务分解为更小的、可管理的步骤。

详细了解[制作提示的最佳实践](<https://github.com/docs/agents/best-practices.md>)和[向提示添加上下文](<https://github.com/docs/chat/copilot-chat-context.md>)。

## 相关资源

* [聊天概述](<https://github.com/docs/chat/chat-overview.md>)
* [向聊天提示添加上下文](<https://github.com/docs/chat/copilot-chat-context.md>)
* [内嵌聊天](<https://github.com/docs/chat/inline-chat.md>)
* [Copilot GitHub 文档中的聊天手册 ](<https://docs.github.com/en/copilot/example-prompts-for-github-copilot-chat>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Prompt examples

This article provides example prompts for chat in {% data variables.product.prodname_vscode %} across different scenarios and agents. Use these examples as inspiration to craft effective prompts for your own development tasks.

If you are new to using chat in {% data variables.product.prodname_vscode_shortname %}, learn more about [getting started with chat](<https://github.com/docs/chat/chat-overview.md>) or review [best practices for prompt crafting](<https://github.com/docs/agents/best-practices.md>).

<div class="docs-action" data-show-in-doc="false" data-show-in-sidebar="true" title="Get started with agents">
Follow a hands-on tutorial to build an app with AI agents in {% data variables.product.prodname_vscode_shortname %}.

* [Start agentic coding tutorial](<https://github.com/docs/agents/agents-tutorial.md>)

</div>

## General coding and technology questions

Use the **Ask** agent to get quick answers about coding concepts, technology topics, and general programming questions.

```prompt-ask
What is a linked list?
```

```prompt-ask
Provide 3 ways to implement a search feature in React.
```

```prompt-ask
Explain the difference between async/await and promises.
```

## Understanding and exploring your codebase

Use the **Ask** agent with `#codebase` to understand how your project works, locate specific functionality, or explore code relationships.

For a guided investigation with source references and verification steps, see [Explore a codebase with an agent](<https://github.com/docs/agents/guides/explore-a-codebase.md>).

```prompt-ask
Explain how authentication works in #codebase
```

```prompt-ask
Where is the database connection string configured? #codebase
```

```prompt-ask
How do I build this #codebase?
```

```prompt-ask
Which testing framework is used for #calculator.test.js?
```

## Code generation and editing

Use **Agent** for multi-file creation and **inline chat** (`kb(inlinechat.start)`) for targeted, in-place edits.

For complete workflows in an existing project, see [Add a feature](<https://github.com/docs/agents/guides/add-a-feature.md>) and [Refactor without changing behavior](<https://github.com/docs/agents/guides/refactor-safely.md>).

```prompt
Add a login button and style it based on #styles.css
```

```prompt
Create a meal-planning web app using React and Node.js
```

```prompt
Refactor this code to use async/await
```

## Testing and quality assurance

Use **Agent** to generate tests or fix failing tests.

```prompt
Add unit tests for the user service.
```

```prompt
Fix the failing tests #testFailure
```

## Debugging and fixing issues

Use **Agent** for fixing issues across files, or **Ask** to understand the root cause first.

```prompt
Fix the issues in #problems
```

```prompt
Why is this function returning undefined?
```

## Working with source control

Use chat to work with your pending changes and generate release documentation.

```prompt
Summarize the #changes
```

```prompt
Generate release notes based on the #changes
```

## Working with external resources

Use `#fetch` to reference content from the web or use the GitHub MCP server to search for code examples in public GitHub repositories.

```prompt
How do I use the 'useState' hook in react 18? #fetch https://18.react.dev/reference/react/useState#usage
```

```prompt
Build an API endpoint to fetch address info, use the template from GitHub repo contoso/api-templates
```

```prompt
What are the top #extensions for this workspace?
```

## Terminal and command-line tasks

Use [terminal inline chat](<https://github.com/docs/chat/inline-chat.md#use-terminal-inline-chat>) to get help with shell commands and terminal operations.

```prompt
How do I install npm packages?
```

```prompt
List the top 5 largest files in the src directory
```

```prompt
undo the last git commit
```

## Working with Jupyter notebooks

Use **Agent** to create, edit, and work with Jupyter notebooks.

```prompt
/newNotebook use pandas and seaborn to read and visualize the titanic dataset. Show key information from the dataset.
```

```prompt
Create a notebook to read data from #housing.csv and plot the distribution of prices
```

```prompt
Make sure the data is cleaned before visualizing and processing it
```

```prompt
Show the correlation between different features in the dataset
```

## Multi-turn conversation examples

Chat supports follow-up prompts within the same session. Use multi-turn conversations to iterate on results and refine the AI's output.

**First prompt:**

```prompt
Create a REST API with Express.js that has endpoints for users and products
```

**Follow-up prompts:**

```prompt
Add input validation and error handling to both endpoints
```

```prompt
Now add unit tests for the validation logic
```

By building on earlier responses, the AI maintains context from the previous steps and generates more coherent code.

## Tips for crafting effective prompts

* **Be specific**: Include details about what you want to accomplish, the technologies to use, and the expected output format.
* **Add context**: Use #-mentions to reference files, symbols, or context variables like `#codebase`, `#changes`, or `#problems`.
* **Iterate**: Start with a simple prompt and refine it based on the response. Ask follow-up questions to improve the results.
* **Break down complex tasks**: Instead of asking for everything at once, break large tasks into smaller, manageable steps.

Learn more about [best practices for crafting prompts](<https://github.com/docs/agents/best-practices.md>) and [adding context to your prompts](<https://github.com/docs/chat/copilot-chat-context.md>).

## Related resources

* [Chat overview](<https://github.com/docs/chat/chat-overview.md>)
* [Add context to your chat prompt](<https://github.com/docs/chat/copilot-chat-context.md>)
* [Inline chat](<https://github.com/docs/chat/inline-chat.md>)
* [Copilot Chat Cookbook](<https://docs.github.com/en/copilot/example-prompts-for-github-copilot-chat>) in the GitHub documentation


</details>
