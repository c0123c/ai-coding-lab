# 在 IDE 中使用 Agent 模式

说明让 Copilot 跨文件修改并运行命令的方法，介绍模型或自定义 Agent 选择、执行过程中的引导及工具扩展。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-agent-mode.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 IDE 中使用 Agent 模式.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在 IDE 中使用 agent 模式

给 Copilot 一个任务，让它在编辑器中自主工作，编辑项目中的文件并运行命令，直到任务完成。

## 简介

在 agent 模式下，Copilot 执行高级任务，决定要更改哪些文件，进行编辑，并根据需要运行命令，迭代直到任务完成。您保持控制：您查看更改，并且默认情况下您在命令运行之前批准它们。您的管理员或您自己的编辑器设置可能允许某些命令自动运行。

Agent 模式可在 Visual Studio Code、Visual Studio、JetBrains IDE、Xcode 和 Eclipse 中使用。这些步骤因编辑器而异，因此请单击上面的选项卡以获取适用于您的 IDE 的说明。

有关如何打开 Copilot 聊天并在可用模式之间进行选择，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>) 中询问 GitHub Copilot 问题。

<!-- --------------------- -->

<!-- VS Code -->

<!-- --------------------- -->

<div class="ghd-tool vscode">

当您有特定任务并希望启用 Copilot 来自主编辑代码时，请使用 agent 模式。在 agent 模式下，Copilot 确定要更改哪些文件，提供代码更改和终端命令来完成任务，并迭代修复问题，直到原始任务完成。

Agent 模式最适合以下用例：

* 您的任务很复杂，涉及多个步骤、迭代和错误处理。
* 您希望 Copilot 确定完成任务所需采取的步骤。
* 该任务需要Copilot与外部应用程序集成，例如MCP服务器。

## 使用agents

1. 如果尚未显示聊天视图，请从 Copilot 聊天菜单中选择 ** 打开聊天 **。
2. 在聊天视图的底部，确保从 agents 下拉列表中选择 **Agent**。
3. 提交提示。为了响应您的提示，Copilot 在编辑器中流式传输编辑内容、更新工作集并在必要时运行终端命令。
4. 检查并迭代更改或运行代码审查。

您也可以[点击此链接](<vscode://GitHub.Copilot-Chat/chat?mode=agent\&ref_product=copilot\&ref_type=engagement\&ref_style=text>)直接进入VS Code中的agent模式。 <!-- markdownlint-disable-line GHD003 -->

> \[!NOTE]
> 如果您在模式选择器中没有看到 **Agent** 选项，则您的企业或组织管理员可能已为您的 IDE 禁用了 agent 模式。

有关更多信息，请参阅 Visual Studio Code 文档中的 [聊天概述 ](<https://aka.ms/vscode-copilot-agent>)。

当您使用agent模式时，您输入的每个提示都会消耗GitHub AI积分。

## 在 agent 工作时对其进行转向

Agent模式是交互式的。当Copilot工作时，您可以：

* 在完成之前提交后续提示以重定向 agent。
* 确认或拒绝 agent 建议的每个终端命令，除非它已配置为自动运行。
* 查看出现的流式编辑并撤消任何您不需要的内容。

如果任务很大或不明确，请考虑首先起草实施计划。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。

## 选择型号或定制 agent

在提交任务之前，您可以更改 agent 使用的 AI 模型，或选择针对特定工作类型定制的自定义 agent。

* 要更改模型，请参阅[更改GitHub Copilot聊天](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)的AI模型。
* 要选择自定义 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>) 中使用自定义 agents。

## 使用工具扩展agent模式

agent 模式的大部分功能来自它可以调用的工具。您可以使用 Model Context Protocol (MCP) 服务器扩展 agent，这会添加用于与外部系统和 GitHub 本身配合使用的工具。

* 要在 IDE 中设置 MCP 服务器，请参阅 [扩展 GitHub Copilot 与 Model Context Protocol (MCP) 服务器聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>)。
* 要通过编辑器使用 GitHub，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>) 中使用 GitHub MCP 服务器。
* 有关有效示例，请参阅 [使用 MCP](<https://docs.github.com/en/copilot/tutorials/enhance-agent-mode-with-mcp>) 增强 GitHub Copilot agent 模式。

要将独立的子任务交给具有自己上下文的单独的 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) 中使用子代理。

## 进一步阅读

* [在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)中询问GitHub Copilot问题
* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Visual Studio -->

<!-- --------------------- -->

<div class="ghd-tool visualstudio">

当您有特定任务并希望启用 Copilot 来自主编辑代码时，请使用 agent 模式。在 agent 模式下，Copilot 确定要更改哪些文件，提供代码更改和终端命令来完成任务，并迭代修复问题，直到原始任务完成。

Agent 模式在 Visual Studio 17.14 及更高版本中可用。

## 使用agent模式

1. 在 Visual Studio 菜单栏中，单击“**查看**”，然后单击“**GitHub Copilot聊天**”。
2. 在聊天面板底部，从模式下拉列表中选择 **Agent**。
3. 提交提示。为了响应您的提示，Copilot 在编辑器中流式传输编辑内容、更新工作集，并在必要时建议要运行的终端命令。
4. 查看更改。如果Copilot建议终端命令，请确认Copilot是否可以运行它们。作为响应，Copilot 迭代并执行其他操作以完成原始提示中的任务。

当您使用Copilotagent模式时，您输入的每个提示都会消耗GitHubAI积分。

## 选择型号

在提交任务之前，您可以更改agent使用的AI模型。请参阅[更改GitHub Copilot聊天](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)的AI模型。

## 使用工具扩展agent模式

您可以使用 Model Context Protocol (MCP) 服务器扩展 agent，这些服务器添加了用于与外部系统和 GitHub 本身配合使用的工具。

* 要在 IDE 中设置 MCP 服务器，请参阅 [扩展 GitHub Copilot 与 Model Context Protocol (MCP) 服务器聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>)。
* 要通过编辑器使用 GitHub，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>) 中使用 GitHub MCP 服务器。

## 进一步阅读

* [在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)中询问GitHub Copilot问题
* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- JetBrains -->

<!-- --------------------- -->

<div class="ghd-tool jetbrains">

当您有特定任务并希望启用 Copilot 来自主编辑代码时，请使用 agent 模式。在 agent 模式下，Copilot 确定要更改哪些文件，提供代码更改和终端命令来完成任务，并迭代修复问题，直到原始任务完成。

Agent 模式最适合以下用例：

* 您的任务很复杂，涉及多个步骤、迭代和错误处理。
* 您希望 Copilot 确定完成任务所需采取的步骤。
* 该任务需要Copilot与外部应用程序集成，例如MCP服务器。

## 使用agent模式

1. 要使用 agent 模式启动编辑会话，请单击菜单栏中的 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> Copilot**，然后选择 **打开 GitHub Copilot 聊天 **。
2. 在聊天面板顶部，单击 **Agent** 选项卡。
3. 提交提示。为了响应您的提示，Copilot 在编辑器中流式传输编辑内容、更新工作集，并在必要时建议要运行的终端命令。
4. 查看更改。如果Copilot建议终端命令，请确认Copilot是否可以运行它们。作为响应，Copilot 迭代并执行其他操作以完成原始提示中的任务。

当您使用agent模式时，您输入的每个提示都会消耗GitHub AI积分。

## 在 agent 工作时对其进行转向

Agent模式是交互式的。当Copilot工作时，您可以提交后续提示来重定向agent，并确认或拒绝它建议的每个终端命令。

如果任务很大或不明确，请考虑首先起草实施计划。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。

## 选择型号或定制 agent

在提交任务之前，您可以更改 agent 使用的 AI 模型，或选择针对特定工作类型定制的自定义 agent。

* 要更改模型，请参阅[更改GitHub Copilot聊天](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)的AI模型。
* 要选择自定义 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>) 中使用自定义 agents。

## 使用工具扩展agent模式

您可以使用 Model Context Protocol (MCP) 服务器扩展 agent，这些服务器添加了用于与外部系统和 GitHub 本身配合使用的工具。

* 要在 IDE 中设置 MCP 服务器，请参阅 [扩展 GitHub Copilot 与 Model Context Protocol (MCP) 服务器聊天 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>)。
* 要通过编辑器使用 GitHub，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>) 中使用 GitHub MCP 服务器。

要将独立的子任务交给具有自己上下文的单独的 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) 中使用子代理。

## 进一步阅读

* [在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)中询问GitHub Copilot问题
* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Xcode -->

<!-- --------------------- -->

<div class="ghd-tool xcode">

当您有特定任务并希望启用 Copilot 来自主编辑代码时，请使用 agent 模式。在 agent 模式下，Copilot 确定要更改哪些文件，提供代码更改和终端命令来完成任务，并迭代修复问题，直到原始任务完成。

Agent 模式最适合以下用例：

* 您的任务很复杂，涉及多个步骤、迭代和错误处理。
* 您希望 Copilot 确定完成任务所需采取的步骤。
* 该任务需要Copilot与外部应用程序集成，例如MCP服务器。

## 使用agent模式

1. 如果尚未显示，请单击菜单栏中的**编辑器**，然后单击**GitHub Copilot**，然后单击**打开聊天**，打开Copilot聊天窗口。
2. 在聊天面板底部，从 agents 下拉列表中选择 **Agent**。
3. 或者，将相关文件添加到 * 工作集 * 视图，以向 Copilot 指示您要处理哪些文件。
4. 提交提示。为了响应您的提示，Copilot 在编辑器中流式传输编辑内容、更新工作集，并在必要时建议要运行的终端命令。
5. 查看更改。如果Copilot建议终端命令，请确认Copilot是否可以运行它们。作为响应，Copilot 迭代并执行其他操作以完成原始提示中的任务。

当您使用agent模式时，您输入的每个提示都会消耗GitHub AI积分。

## 在 agent 工作时对其进行转向

Agent模式是交互式的。当Copilot工作时，您可以提交后续提示来重定向agent，并确认或拒绝它建议的每个终端命令。

如果任务很大或不明确，请考虑首先起草实施计划。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。

## 选择型号

在提交任务之前，您可以更改agent使用的AI模型。请参阅[更改GitHub Copilot聊天](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)的AI模型。

要将独立的子任务交给具有自己上下文的单独的 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) 中使用子代理。

## 进一步阅读

* [在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)中询问GitHub Copilot问题
* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Eclipse -->

<!-- --------------------- -->

<div class="ghd-tool eclipse">

当您有特定任务并希望启用 Copilot 来自主编辑代码时，请使用 agent 模式。在 agent 模式下，Copilot 确定要更改哪些文件，提供代码更改和终端命令来完成任务，并迭代修复问题，直到原始任务完成。

Agent 模式最适合以下用例：

* 您的任务很复杂，涉及多个步骤、迭代和错误处理。
* 您希望 Copilot 确定完成任务所需采取的步骤。
* 该任务需要Copilot与外部应用程序集成，例如MCP服务器。

## 使用agent模式

1. 单击 Eclipse 底部状态栏中的 Copilot 图标 (<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg>)，然后单击 ** 打开聊天 **，打开 Copilot 聊天面板。
2. 在聊天面板底部，从 agents 下拉列表中选择 **Agent**。
3. 提交提示。为了响应您的提示，Copilot 在编辑器中流式传输编辑内容、更新工作集，并在必要时建议要运行的终端命令。
4. 查看更改。如果Copilot建议终端命令，请确认Copilot是否可以运行它们。作为响应，Copilot 迭代并执行其他操作以完成原始提示中的任务。

当您使用agent模式时，您输入的每个提示都会消耗GitHub AI积分。

## 在 agent 工作时对其进行转向

Agent模式是交互式的。当Copilot工作时，您可以提交后续提示来重定向agent，并确认或拒绝它建议的每个终端命令。

如果任务很大或不明确，请考虑首先起草实施计划。请参阅[在IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>)中使用计划模式。

## 选择型号

在提交任务之前，您可以更改agent使用的AI模型。请参阅[更改GitHub Copilot聊天](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>)的AI模型。

要将独立的子任务交给具有自己上下文的单独的 agent，请参阅 [在 IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>) 中使用子代理。

## 进一步阅读

* [在您的IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)中询问GitHub Copilot问题
* [GitHub Copilot 在 GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Using agent mode in your IDE

Give Copilot a task and let it work autonomously in your editor, editing files across your project and running commands until the task is complete.

## Introduction

In agent mode, Copilot takes a high-level task, decides which files to change, makes the edits, and runs commands as needed, iterating until the task is done. You stay in control: you review the changes, and by default you approve commands before they run. Your administrator, or your own editor settings, may allow some commands to run automatically.

Agent mode is available in Visual Studio Code, Visual Studio, JetBrains IDEs, Xcode, and Eclipse. The steps differ by editor, so click the tabs above for instructions for your IDE.

For how to open Copilot Chat and choose between the available modes, see [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>).

<!-- --------------------- -->

<!-- VS Code -->

<!-- --------------------- -->

<div class="ghd-tool vscode">

Use agent mode when you have a specific task in mind and want to enable Copilot to autonomously edit your code. In agent mode, Copilot determines which files to make changes to, offers code changes and terminal commands to complete the task, and iterates to remediate issues until the original task is complete.

Agent mode is best suited to use cases where:

* Your task is complex, and involves multiple steps, iterations, and error handling.
* You want Copilot to determine the necessary steps to take to complete the task.
* The task requires Copilot to integrate with external applications, such as an MCP server.

## Using agents

1. If the chat view is not already displayed, select **Open Chat** from the Copilot Chat menu.
2. At the bottom of the chat view, ensure **Agent** is selected from the agents dropdown.
3. Submit a prompt. In response to your prompt, Copilot streams the edits in the editor, updates the working set, and runs terminal commands if necessary.
4. Review and iterate on changes or run a code review.

You can also [click this link](<vscode://GitHub.Copilot-Chat/chat?mode=agent\&ref_product=copilot\&ref_type=engagement\&ref_style=text>) to go directly to agent mode in VS Code. <!-- markdownlint-disable-line GHD003 -->

> \[!NOTE]
> If you don’t see the **Agent** option in the mode selector, your enterprise or organization administrator may have disabled agent mode for your IDE.

For more information, see [Chat overview](<https://aka.ms/vscode-copilot-agent>) in the Visual Studio Code documentation.

When you use agent mode, each prompt you enter consumes GitHub AI Credits.

## Steering an agent while it works

Agent mode is interactive. While Copilot is working, you can:

* Submit a follow-up prompt to redirect the agent before it finishes.
* Confirm or reject each terminal command the agent proposes, unless it has been configured to run automatically.
* Review streamed edits as they appear and undo any you do not want.

If a task is large or ambiguous, consider drafting an implementation plan first. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Choosing a model or a custom agent

Before you submit a task, you can change which AI model the agent uses, or select a custom agent tailored to a specific kind of work.

* To change the model, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>).
* To select a custom agent, see [Using custom agents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

## Extending agent mode with tools

Much of agent mode's capability comes from the tools it can call. You can extend the agent with Model Context Protocol (MCP) servers, which add tools for working with external systems and with GitHub itself.

* To set up MCP servers in your IDE, see [Extending GitHub Copilot Chat with Model Context Protocol (MCP) servers](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>).
* To work with GitHub from your editor, see [Using the GitHub MCP Server in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).
* For a worked example, see [Enhancing GitHub Copilot agent mode with MCP](<https://docs.github.com/en/copilot/tutorials/enhance-agent-mode-with-mcp>).

To hand a self-contained subtask to a separate agent with its own context, see [Using subagents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>).

## Further reading

* [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Visual Studio -->

<!-- --------------------- -->

<div class="ghd-tool visualstudio">

Use agent mode when you have a specific task in mind and want to enable Copilot to autonomously edit your code. In agent mode, Copilot determines which files to make changes to, offers code changes and terminal commands to complete the task, and iterates to remediate issues until the original task is complete.

Agent mode is available in Visual Studio 17.14 and later.

## Using agent mode

1. In the Visual Studio menu bar, click **View**, then click **GitHub Copilot Chat**.
2. At the bottom of the chat panel, select **Agent** from the mode dropdown.
3. Submit a prompt. In response to your prompt, Copilot streams the edits in the editor, updates the working set, and if necessary, suggests terminal commands to run.
4. Review the changes. If Copilot suggested terminal commands, confirm whether or not Copilot can run them. In response, Copilot iterates and performs additional actions to complete the task in your original prompt.

When you use Copilot agent mode, each prompt you enter consumes GitHub AI Credits.

## Choosing a model

Before you submit a task, you can change which AI model the agent uses. See [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>).

## Extending agent mode with tools

You can extend the agent with Model Context Protocol (MCP) servers, which add tools for working with external systems and with GitHub itself.

* To set up MCP servers in your IDE, see [Extending GitHub Copilot Chat with Model Context Protocol (MCP) servers](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>).
* To work with GitHub from your editor, see [Using the GitHub MCP Server in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

## Further reading

* [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- JetBrains -->

<!-- --------------------- -->

<div class="ghd-tool jetbrains">

Use agent mode when you have a specific task in mind and want to enable Copilot to autonomously edit your code. In agent mode, Copilot determines which files to make changes to, offers code changes and terminal commands to complete the task, and iterates to remediate issues until the original task is complete.

Agent mode is best suited to use cases where:

* Your task is complex, and involves multiple steps, iterations, and error handling.
* You want Copilot to determine the necessary steps to take to complete the task.
* The task requires Copilot to integrate with external applications, such as an MCP server.

## Using agent mode

1. To start an edit session using agent mode, click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> Copilot** in the menu bar, then select **Open GitHub Copilot Chat**.
2. At the top of the chat panel, click the **Agent** tab.
3. Submit a prompt. In response to your prompt, Copilot streams the edits in the editor, updates the working set, and if necessary, suggests terminal commands to run.
4. Review the changes. If Copilot suggested terminal commands, confirm whether or not Copilot can run them. In response, Copilot iterates and performs additional actions to complete the task in your original prompt.

When you use agent mode, each prompt you enter consumes GitHub AI Credits.

## Steering an agent while it works

Agent mode is interactive. While Copilot is working, you can submit a follow-up prompt to redirect the agent, and confirm or reject each terminal command it proposes.

If a task is large or ambiguous, consider drafting an implementation plan first. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Choosing a model or a custom agent

Before you submit a task, you can change which AI model the agent uses, or select a custom agent tailored to a specific kind of work.

* To change the model, see [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>).
* To select a custom agent, see [Using custom agents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents>).

## Extending agent mode with tools

You can extend the agent with Model Context Protocol (MCP) servers, which add tools for working with external systems and with GitHub itself.

* To set up MCP servers in your IDE, see [Extending GitHub Copilot Chat with Model Context Protocol (MCP) servers](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/customize-copilot/extend-copilot-with-tools-and-context/extend-copilot-chat-with-mcp>).
* To work with GitHub from your editor, see [Using the GitHub MCP Server in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/use-the-github-mcp-server>).

To hand a self-contained subtask to a separate agent with its own context, see [Using subagents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>).

## Further reading

* [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Xcode -->

<!-- --------------------- -->

<div class="ghd-tool xcode">

Use agent mode when you have a specific task in mind and want to enable Copilot to autonomously edit your code. In agent mode, Copilot determines which files to make changes to, offers code changes and terminal commands to complete the task, and iterates to remediate issues until the original task is complete.

Agent mode is best suited to use cases where:

* Your task is complex, and involves multiple steps, iterations, and error handling.
* You want Copilot to determine the necessary steps to take to complete the task.
* The task requires Copilot to integrate with external applications, such as an MCP server.

## Using agent mode

1. If it is not already displayed, open the Copilot Chat window by clicking **Editor** in the menu bar, then clicking **GitHub Copilot** then **Open Chat**.
2. At the bottom of the chat panel, select **Agent** from the agents dropdown.
3. Optionally, add relevant files to the *working set* view to indicate to Copilot which files you want to work on.
4. Submit a prompt. In response to your prompt, Copilot streams the edits in the editor, updates the working set, and if necessary, suggests terminal commands to run.
5. Review the changes. If Copilot suggested terminal commands, confirm whether or not Copilot can run them. In response, Copilot iterates and performs additional actions to complete the task in your original prompt.

When you use agent mode, each prompt you enter consumes GitHub AI Credits.

## Steering an agent while it works

Agent mode is interactive. While Copilot is working, you can submit a follow-up prompt to redirect the agent, and confirm or reject each terminal command it proposes.

If a task is large or ambiguous, consider drafting an implementation plan first. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Choosing a model

Before you submit a task, you can change which AI model the agent uses. See [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>).

To hand a self-contained subtask to a separate agent with its own context, see [Using subagents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>).

## Further reading

* [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>

<!-- --------------------- -->

<!-- Eclipse -->

<!-- --------------------- -->

<div class="ghd-tool eclipse">

Use agent mode when you have a specific task in mind and want to enable Copilot to autonomously edit your code. In agent mode, Copilot determines which files to make changes to, offers code changes and terminal commands to complete the task, and iterates to remediate issues until the original task is complete.

Agent mode is best suited to use cases where:

* Your task is complex, and involves multiple steps, iterations, and error handling.
* You want Copilot to determine the necessary steps to take to complete the task.
* The task requires Copilot to integrate with external applications, such as an MCP server.

## Using agent mode

1. Open the Copilot Chat panel by clicking the Copilot icon (<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg>) in the status bar at the bottom of Eclipse, then clicking **Open Chat**.
2. At the bottom of the chat panel, select **Agent** from the agents dropdown.
3. Submit a prompt. In response to your prompt, Copilot streams the edits in the editor, updates the working set, and if necessary, suggests terminal commands to run.
4. Review the changes. If Copilot suggested terminal commands, confirm whether or not Copilot can run them. In response, Copilot iterates and performs additional actions to complete the task in your original prompt.

When you use agent mode, each prompt you enter consumes GitHub AI Credits.

## Steering an agent while it works

Agent mode is interactive. While Copilot is working, you can submit a follow-up prompt to redirect the agent, and confirm or reject each terminal command it proposes.

If a task is large or ambiguous, consider drafting an implementation plan first. See [Using plan mode in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-plan-mode>).

## Choosing a model

Before you submit a task, you can change which AI model the agent uses. See [Changing the AI model for GitHub Copilot Chat](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/change-the-chat-model>).

To hand a self-contained subtask to a separate agent with its own context, see [Using subagents in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents>).

## Further reading

* [Asking GitHub Copilot questions in your IDE](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [GitHub Copilot on GitHub.com](<https://docs.github.com/en/copilot/concepts/chat>)

</div>


</details>
