# IDE 中的 Copilot 开发入口

概述代码建议、下一处编辑、聊天与 Agent 操作，帮助按探索、编写、审查和改进代码选择入口。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/copilot-surfaces/copilot-in-ides.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/IDE 中的 Copilot 开发入口.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: GitHub Copilot in IDEs
shortTitle: Copilot in IDEs
intro: 'Get contextual AI assistance from {% data variables.product.prodname_copilot %} throughout your IDE workflow, from exploring an approach to writing, reviewing, and improving code.'
versions:
  feature: copilot
defaultTool: vscode
contentType: concepts
category:
  - Learn about Copilot
redirect_from:
  - /copilot/concepts/completions/code-referencing
  - /copilot/concepts/completions/code-suggestions
  - /copilot/concepts/agents/copilot-in-jetbrains
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data variables.product.prodname_copilot %} 与支持的 IDE 集成，以帮助您完成整个开发过程。 {% data variables.product.prodname_copilot_short %}可以使用agent模式解释概念、完成代码、建议编辑和验证文件。

可用的功能和入口点因 IDE 的不同而异。详细比较请参见[AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix>)。

## {% data variables.product.prodname_copilot_short %} 如何帮助您 IDE

您可以通过三种主要方式使用 {% data variables.product.prodname_copilot_short %}：

* **代码建议**帮助您在不离开编辑器的情况下编写和编辑代码。
* **聊天**可让您提出问题、解释或重构代码、生成测试并探索可能的解决方案。
* **代理体验**可以通过在本地开发环境中读取文件、编辑代码和运行命令来规划和完成多步骤任务。

这些经验是相辅相成的。例如，您可以使用聊天来了解不熟悉的代码库，要求 agent 实现跨多个文件的更改，并使用内联建议来优化结果。

## 代码建议

{% data variables.product.prodname_copilot_short %} 可以在您工作时直接在编辑器中建议代码。根据您的 IDE，这些建议可以包括内联建议和 {% data variables.copilot.next_edit_suggestions %}。

### 内嵌建议

内联建议在您的 cursor 位置显示为灰色文本。 {% data variables.product.prodname_copilot_short %} 使用 cursor 周围的代码和其他可用上下文来预测您接下来可能要写的内容。

建议可以完成一行的一部分、整行或更大的代码块。您还可以编写自然语言注释来描述您想要的代码，然后让 {% data variables.product.prodname_copilot_short %} 建议实现。

您仍然有责任在使用建议的代码之前对其进行审查和测试。

### {% data variables.copilot.next_edit_suggestions_caps %}

{% data variables.copilot.next_edit_suggestions_caps %} 预测您可能在哪里进行下一次更改以及更改可能是什么。它们基于您当前正在进行的编辑，可以帮助您应用文件中其他位置的相关更改。

例如，在重命名方法或更改数据结构后，{% data variables.product.prodname_copilot_short %} 可能会建议更新受该更改影响的另一个位置。

可用性和配置因IDE而异。有关使用内联和下一个编辑建议的说明，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/get-code-suggestions/get-ide-code-suggestions>)。

{% data reusables.copilot.supported-languages %}

## 聊天和代理体验

{% data variables.copilot.copilot_chat %} 在您的 IDE 中提供对话界面。由于聊天可以使用项目中的上下文，因此您可以询问有关所选代码、打开的文件或更广泛的代码库的问题。

您可以使用聊天来：

* 解释一下不熟悉的代码。
* 建议修复错误。
* 重构或文档代码。
* 生成测试。
* 比较实施方法。
* 回答有关编程语言和开发工具的问题。

对于较大的任务，代理体验可以将您的请求分解为步骤并使用工具来完成工作。根据您的 IDE 和配置，agent 可以检查您的项目、编辑多个文件、运行终端命令以及响应工作时遇到的错误。

在您的本地开发环境中进行代理更改。在接受结果之前，请检查建议的更改和任何命令的输出。

有关使用聊天和 agents 的说明，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/chat-with-copilot/chat-in-ide>)。

## 选择切入点

对于大多数 IDE，**{% data variables.product.prodname_copilot %} 扩展或插件 ** 是主要入口点。它将 {% data variables.product.prodname_copilot_short %} 直接连接到您的编辑器，并提供 IDE 支持的功能。您可以按照[AUTOTITLE](<https://github.com/copilot/how-tos/set-up/install-copilot-extension>)安装适当的集成。

如果终端环境满足{% data variables.copilot.copilot_cli_short %}的要求，您也可以在IDE的集成终端中运行**{% data variables.copilot.copilot_cli %}**。 CLI 会话和 IDE 之间的集成有所不同。

### JetBrains IDE 中的入口点

在{% data variables.product.prodname_jetbrains_ides %}中，访问{% data variables.product.prodname_copilot_short %}的主要入口点可供选择：{% data variables.product.prodname_copilot %}插件、JetBrains、AI助手或集成终端中的{% data variables.copilot.copilot_cli %}。

|切入点|最适合|主要能力|
| --- | --- | --- |
|**{% data variables.product.prodname_copilot %} 插件**|完整的AI-辅助开发工作流程|内联和下一个编辑建议、聊天、代理功能、内联聊天、代码审查和提交消息生成|
|**{% data variables.product.prodname_copilot_short %} 在 JetBrains AI 助理**|使用聊天和 agent，无需安装单独的 {% data variables.product.prodname_copilot_short %} 插件|聊天、代理任务和模型选择|
| **{% data variables.copilot.copilot_cli_short %}** |终端优先的开发工作流程|综合码头代理协助及选型|

{% data variables.product.prodname_copilot %}插件还支持
多个agent线束和OpenTelemetry监控。企业管理员可以使用托管设置来控制插件市场、MCP 服务器、OpenTelemetry 配置以及用户是否可以绕过权限检查。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/opentelemetry>)和[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>)。

{% data variables.product.prodname_copilot %} 插件提供了与编辑器最广泛的集成，当您需要代码建议和全套 IDE 功能时，这是推荐的入口点。

{% data variables.product.prodname_copilot_short %} 还可通过 Agent 客户端协议 (ACP) 作为 JetBrains AI 助手中的 agent 提供。有关 ACP 的更多信息，请参阅 [ACP 文档 ](<https://agentclientprotocol.com/get-started/introduction>)。有关将 {% data variables.copilot.copilot_cli_short %} 作为 ACP 服务器运行的技术详细信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/acp-server>)。

## 引用匹配的公共代码

{% data variables.product.prodname_copilot %} 检查与 {% data variables.product.prodname_dotcom_the_website %} 上公共存储库中的代码匹配的建议。根据适用于您的帐户或组织的政策，可以阻止匹配建议或显示有关匹配代码的信息。

对于内联建议，代码引用会将潜在建议和周围代码的大约 150 个字符与公共存储库的索引进行比较。搜索中不包含来自私人存储库的代码和 {% data variables.product.prodname_dotcom %} 外部托管的代码。

当您接受与公共代码匹配的内联建议时，{% data variables.product.prodname_copilot_short %} 会记录详细信息，例如匹配的文件 URL 和任何检测到的许可证。仅以这种方式检查已接受的、未更改的建议。

当 {% data variables.copilot.copilot_chat_short %} 响应包含与公共存储库中的代码相匹配的代码时，该响应将包含指向有关匹配的信息的链接。参见[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github#references-to-matching-public-code>)。

通常，与公共代码的匹配发生在不到百分之一的 {% data variables.product.prodname_copilot_short %} 建议中，因此您不应期望看到许多建议的代码引用。

公共代码索引会定期刷新，因此它可能不包括最近添加的代码，并且可能包含对已移动或删除的代码的引用。

代码参考可帮助您查看匹配代码的来源和许可，以便您决定是否使用、归属或删除它。有关查看参考文献的说明，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/get-code-suggestions/find-matching-code>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data variables.product.prodname_copilot %} integrates with supported IDEs to help you throughout the development process. {% data variables.product.prodname_copilot_short %} can explain concepts, complete code, propose edits, and validate files with agent mode.

The available features and entry points vary by IDE. For a detailed comparison, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-feature-matrix>).

## How {% data variables.product.prodname_copilot_short %} helps in your IDE

You can work with {% data variables.product.prodname_copilot_short %} in three main ways:

* **Code suggestions** help you write and edit code without leaving the editor.
* **Chat** lets you ask questions, explain or refactor code, generate tests, and explore possible solutions.
* **Agentic experiences** can plan and complete multi-step tasks by reading files, editing code, and running commands in your local development environment.

These experiences complement each other. For example, you might use chat to understand an unfamiliar codebase, ask an agent to implement a change across several files, and use inline suggestions to refine the result.

## Code suggestions

{% data variables.product.prodname_copilot_short %} can suggest code directly in the editor as you work. Depending on your IDE, these suggestions can include inline suggestions and {% data variables.copilot.next_edit_suggestions %}.

### Inline suggestions

Inline suggestions appear as dimmed text at your cursor position. {% data variables.product.prodname_copilot_short %} uses the code around your cursor and other available context to predict what you are likely to write next.

Suggestions can complete part of a line, an entire line, or a larger block of code. You can also write a natural-language comment describing the code you want, then let {% data variables.product.prodname_copilot_short %} suggest an implementation.

You remain responsible for reviewing and testing suggested code before using it.

### {% data variables.copilot.next_edit_suggestions_caps %}

{% data variables.copilot.next_edit_suggestions_caps %} predict both where you are likely to make your next change and what that change could be. They are based on the edits you are currently making and can help you apply related changes elsewhere in a file.

For example, after you rename a method or change a data structure, {% data variables.product.prodname_copilot_short %} might suggest updating another location affected by that change.

Availability and configuration vary by IDE. For instructions on using inline and next edit suggestions, see [AUTOTITLE](<https://github.com/copilot/how-tos/get-code-suggestions/get-ide-code-suggestions>).

{% data reusables.copilot.supported-languages %}

## Chat and agentic experiences

{% data variables.copilot.copilot_chat %} provides a conversational interface in your IDE. Because chat can use context from your project, you can ask questions about selected code, open files, or your wider codebase.

You can use chat to:

* Explain unfamiliar code.
* Suggest fixes for bugs.
* Refactor or document code.
* Generate tests.
* Compare implementation approaches.
* Answer questions about programming languages and development tools.

For larger tasks, an agentic experience can break your request into steps and use tools to complete the work. Depending on your IDE and configuration, an agent can inspect your project, edit multiple files, run terminal commands, and respond to errors encountered while working.

Agentic changes are made in your local development environment. Review the proposed changes and the output of any commands before accepting the result.

For instructions on using chat and agents, see [AUTOTITLE](<https://github.com/copilot/how-tos/chat-with-copilot/chat-in-ide>).

## Choosing an entry point

For most IDEs, the **{% data variables.product.prodname_copilot %} extension or plugin** is the primary entry point. It connects {% data variables.product.prodname_copilot_short %} directly to your editor and provides the features supported by that IDE. You can install the appropriate integration by following [AUTOTITLE](<https://github.com/copilot/how-tos/set-up/install-copilot-extension>).

You can also run **{% data variables.copilot.copilot_cli %}** in an IDE's integrated terminal if the terminal environment meets the requirements for {% data variables.copilot.copilot_cli_short %}. Integration between CLI sessions and the IDE varies.

### Entry points in JetBrains IDEs

In {% data variables.product.prodname_jetbrains_ides %}, there are three primary entry points for accessing {% data variables.product.prodname_copilot_short %} to choose from: the {% data variables.product.prodname_copilot %} plugin, the JetBrains AI Assistant, or {% data variables.copilot.copilot_cli %} in the integrated terminal.

| Entry point | Best for | Main capabilities |
| --- | --- | --- |
| **{% data variables.product.prodname_copilot %} plugin** | A complete AI-assisted development workflow | Inline and next edit suggestions, chat, agentic features, inline chat, code review, and commit message generation |
| **{% data variables.product.prodname_copilot_short %} in JetBrains AI Assistant** | Using chat and an agent without installing a separate {% data variables.product.prodname_copilot_short %} plugin | Chat, agentic tasks, and model selection |
| **{% data variables.copilot.copilot_cli_short %}** | Terminal-first development workflows | Agentic assistance and model selection in the integrated terminal |

The {% data variables.product.prodname_copilot %} plugin also supports
multiple agent harnesses and OpenTelemetry monitoring. Enterprise administrators can use managed settings to control plugin marketplaces, MCP servers, OpenTelemetry configuration, and whether users can bypass permission checks. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/opentelemetry>) and [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/use-managed-settings/get-started>).

The {% data variables.product.prodname_copilot %} plugin provides the broadest integration with the editor and is the recommended entry point when you want code suggestions and the full set of IDE features.

{% data variables.product.prodname_copilot_short %} is also available as an agent in JetBrains AI Assistant through the Agent Client Protocol (ACP). For more information about ACP, see the [ACP documentation](<https://agentclientprotocol.com/get-started/introduction>). For technical details on running {% data variables.copilot.copilot_cli_short %} as an ACP server, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/acp-server>).

## References to matching public code

{% data variables.product.prodname_copilot %} checks suggestions for matches with code in public repositories on {% data variables.product.prodname_dotcom_the_website %}. Depending on the policy that applies to your account or organization, matching suggestions can be blocked or shown with information about the matching code.

For inline suggestions, code referencing compares a potential suggestion and approximately 150 characters of surrounding code with an index of public repositories. Code from private repositories and code hosted outside {% data variables.product.prodname_dotcom %} are not included in the search.

When you accept an inline suggestion that matches public code, {% data variables.product.prodname_copilot_short %} records details such as the matching file URLs and any detected licenses. Only accepted, unchanged suggestions are checked in this way.

When a {% data variables.copilot.copilot_chat_short %} response contains code that matches code in a public repository, the response includes a link to information about the match. See [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github#references-to-matching-public-code>).

Typically, matches to public code occur in less than one percent of {% data variables.product.prodname_copilot_short %} suggestions, so you should not expect to see code references for many suggestions.

The public-code index is refreshed periodically, so it may not include recently added code and may contain references to code that has since moved or been deleted.

Code references help you review the source and licensing of matching code so you can decide whether to use, attribute, or remove it. For instructions on viewing references, see [AUTOTITLE](<https://github.com/copilot/how-tos/get-code-suggestions/find-matching-code>).


</details>
