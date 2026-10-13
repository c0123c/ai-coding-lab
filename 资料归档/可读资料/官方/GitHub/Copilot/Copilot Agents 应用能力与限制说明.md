# Copilot Agents 应用能力与限制说明

系统记录云端 Agent、代码审查、CLI 与 SDK 的用途、能力、评估、局限和风险控制，帮助判断适用条件。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/responsible-use/agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Agents 应用能力与限制说明.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: 'Application card: GitHub Copilot Agents'
shortTitle: Agents
intro: 'Learn how to use GitHub Copilot agentic features responsibly by understanding their purposes, capabilities, and limitations.'
versions:
  feature: copilot
redirect_from:
  - /early-access/copilot/code-review/responsible-use-of-copilot-code-review
  - /early-access/copilot/code-reviews/responsible-use-of-copilot-code-review
  - /early-access/copilot/code-reviews/responsible-use-of-copilot-code-reviews
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-code-review
  - /copilot/responsible-use-of-github-copilot-features/code-review
  - /copilot/responsible-use/code-review
  - /early-access/copilot/coding-agent/responsible-use-of-copilot-coding-agent
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-copilot-coding-agent-on-githubcom
  - /copilot/responsible-use-of-github-copilot-features/copilot-coding-agent
  - /copilot/responsible-use/copilot-coding-agent
  - /copilot/responsible-use/copilot-cloud-agent
  - /copilot/github-copilot-in-the-cli/about-github-copilot-in-the-cli
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-in-the-cli
  - /copilot/responsible-use-of-github-copilot-features/copilot-in-the-cli
  - /copilot/responsible-use/copilot-in-the-cli
  - /copilot/responsible-use/copilot-cli
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-spark
  - /copilot/responsible-use-of-github-copilot-features/spark
  - /copilot/responsible-use/spark
contentType: rai
category:
  - Responsible use
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 什么是申请卡？

{% data reusables.rai.copilot.application-card-intro %}

## 1。概述

GitHub Copilot 包括几个超越建议和对话的代理功能 - 他们可以审查代码、代表您采取行动以及构建应用程序。这张卡涵盖了以下经历：

* **Copilot 代码审查**：审查 GitHub.com 上的拉取请求差异和元数据，生成反馈意见和建议的更改。
* **Copilot 云 agent**：GitHub.com 上的异步 agent，可以创建分支、编写代码并打开拉取请求以响应分配的问题。云 agent 在带有自动安全扫描的临时防火墙环境中运行。
* **Copilot CLI**：一个命令行工具，可以创建和修改文件、执行命令以及执行多步骤任务。默认情况下，文件系统访问范围仅限于启动 CLI 的目录。权限提示取决于活动的权限模式。启用计算机使用后，Copilot CLI 还可以与该目录范围之外的桌面应用程序交互。
* **Copilot SDK**：一个编程库，允许开发人员使用 Copilot 构建自定义 AI- 支持的应用程序。 SDK 通过 JSON-RPC 与 Copilot CLI 通信，并支持自定义 agents、MCP 服务器集成、生命周期挂钩和会话管理。
* **{% data variables.copilot.github_copilot_app %}**：用于跨本地存储库、git 工作树和云沙箱引导 agent 会话的桌面应用程序。该应用程序使用 {% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.copilot_sdk %} 作为基础，并添加了用于并行会话、快速聊天、{% data variables.product.github %} 问题和拉取请求工作流程、自动化和画布的用户界面。当启用计算机使用时，该应用程序还可以与桌面应用程序交互。

这些功能共享共同的原则——人工监督、输出审查和负责任的使用——但在执行环境、权限和数据流方面有所不同。以下部分在上下文中描述了每个部分。

## 2。关键术语

以下列表提供了与 GitHub Copilot Agents 相关的关键术语词汇表：

* **代码建议**：Copilot代码审查提出的特定代码更改，作为拉取请求反馈的一部分。代码建议以建议更改的形式呈现，只需单击几下即可应用。
* **内容过滤**：一种安全系统，可扫描提示和响应，以在向用户显示有害、攻击性或不安全的内容之前检测并阻止这些内容。
* **计算机使用**：{% data variables.copilot.copilot_cli %}和{% data variables.copilot.github_copilot_app %}中的可选功能，允许agent与桌面应用程序交互。其工具可以在需要视觉上下文时通过操作系统的辅助功能树或屏幕截图读取可访问的应用程序内容和视觉上下文，单击控件，输入和编辑文本，按键，滚动，拖动以及跨应用程序导航工作流程。
* **自定义说明**：代码风格和最佳实践的自然语言描述，存储库维护人员可以配置它们来指导Copilot代码审查的反馈。自定义指令有助于 Copilot 了解特定代码库的约定和标准。
* **幻觉**：语言模型生成的输出听起来似乎合理但实际上不正确、不受所提供的上下文支持或完全捏造的现象。在代码审查中，幻觉可以表现为反馈突出显示不存在的问题或基于对代码的误解。
* **大型语言模型 (LLM)**：一种在大量文本数据上训练的神经网络，可以生成、分析和转换自然语言和代码。 Copilot Agents 使用一个或多个 LLM 来处理上下文并生成响应。
* **临时开发环境**：为每个云agent会话创建的临时、隔离的计算环境。会话结束后环境将被破坏，确保运行之间不会保留任何状态。
* **防火墙**：默认为云agent启用的网络级控制，可防止与未经授权的主机的出站连接，防止代码或数据的意外或恶意泄露。
* **AI信用**：Copilot功能的消耗单位。每次使用 Copilot 代码审查都会消耗 AI 积分。
* **权限提示**：CopilotCLI中的交互式确认步骤，要求用户在agent继续之前批准某个操作，例如修改文件、执行命令或访问当前目录之外的文件。权限提示是本地代理执行的关键安全机制。
* **Copilot SDK**：一个编程库 (`@github/copilot-sdk`)，允许开发人员通过创建会话、发送提示和接收来自 Copilot 的流响应来构建自定义 AI- 支持的应用程序。 SDK通过JSON-RPC与CopilotCLI通信。
* **JSON-RPC**：CopilotSDK和CopilotCLI之间使用的通信协议。 SDK 向 CLI 进程发送结构化请求，该进程处理模型交互和工具执行。
* **自定义 agent (SDK)**：SDK 中的命名 agent 配置，具有自己的系统提示符、作用域工具和可选的 MCP 服务器。 SDK运行时可以根据用户意图自动委托给子agents。
* **Hooks (SDK)**：Copilot SDK 中的生命周期回调，允许开发人员在会话期间的特定点注入自定义逻辑，例如工具使用之前或之后、会话开始或结束时以及出错时。

## 3。主要特性或功能

此处概述的主要特性和功能描述了 GitHub Copilot Agents 的设计用途以及它们如何在支持的任务中执行。

* **自动代码审查反馈**：当用户请求Copilot进行审查时，它会扫描代码更改以及其他相关上下文，并提供有关代码的反馈。反馈可能包括自然语言注释和链接到特定行和文件的特定代码建议。
* **可定制的审查指南**：Copilot可以使用自定义指令（编码风格和最佳实践的自然语言描述）来定制代码审查，以便反馈反映存储库的约定和标准。
* **自主拉取请求创建**：Copilot云agent可以从问题、拉取请求评论或Copilot聊天中选取任务，创建分支，生成定制的代码更改，然后打开拉取请求。创建初始拉取请求后，agent 可以根据您的反馈和评论进行迭代。
* **短暂的、受防火墙保护的执行**：在处理任务时，云agent可以访问自己的短暂开发环境，在其中可以更改代码、执行自动化测试和运行 linters。默认情况下启用防火墙以防止数据泄露。
* **自动安全扫描**：在代码生成过程中，云agent自动使用CodeQL、秘密扫描和依赖关系分析来分析新生成的代码是否存在安全漏洞，并尝试在问题出现之前解决它们。
* **外部集成**：云agent可以从MCP（如workIQ和Microsoft 365）以及外部应用程序（如Microsoft）接收信息和上下文Teams、Linear、Slack 和 Jira，使团队能够直接在现有工作流程中分配任务并跟踪进度。
* **本地代理执行（Copilot CLI）**：Copilot CLI在终端中提供类似聊天的界面，可以自主创建和修改文件、执行命令以及执行多步骤任务。默认情况下，文件系统访问范围仅限于启动 CLI 的目录。主动权限模式决定了修改文件或运行命令前是否出现CLI提示。
* **桌面应用程序交互（计算机使用）**：在本地 {% data variables.copilot.copilot_cli_short %} 和 {% data variables.copilot.github_copilot_app_short %} 会话中，计算机用户可以在需要视觉上下文时通过操作系统的辅助功能树或屏幕截图读取可访问的应用程序内容和视觉上下文，单击控件、输入和编辑文本、按键、滚动、拖动以及跨应用程序导航工作流。

## 4。预期用途

GitHub Copilot Agents可应用于各行业多种场景。用例的一些示例包括：

* **补充人工代码审查**：Copilot代码审查旨在快速提供有关开发人员代码的反馈，使开发人员能够更快地准备好代码合并并提高整体代码质量。
* **代码库维护**：云agent可以处理与安全相关的修复、依赖项升级和有针对性的重构。
* **功能开发**：云agent可以实现增量功能请求、开发额外的测试套件以及创建或更新文档。
* **制作新项目原型**：云agent和CopilotCLI可以开发新概念，帮助开发人员快速探索想法。
* **设置环境 (CLI)**: Copilot CLI 可以在终端中运行命令来设置本地环境以处理现有项目。
* **查找正确的命令 (CLI)**: Copilot CLI 可以建议命令来执行您尝试完成的任务，并用自然语言解释不熟悉的命令。
* **使用旧版应用程序和仅 GUI-的应用程序（计算机使用）**：计算机使用有助于处理不提供 API、命令行界面或 MCP 集成的本地桌面应用程序中的工作流程。用户应该描述结果、应用程序和重要约束，然后审查操作和结果。
* **构建自定义 AI 应用程序 (SDK)**：Copilot SDK 使开发人员能够构建利用 Copilot 在自己的产品中进行代码生成、自然语言交互和任务自动化的应用程序工作流程。
* **多agent编排（SDK）**：使用自定义agents和子agents，开发人员可以构建复杂的工作流程，其中多个专业agents协作处理复杂任务，并根据用户自动委派意图。
* **使用外部工具扩展应用程序 (SDK)**：SDK 的 MCP 服务器支持允许开发人员将其应用程序连接到外部数据源和服务，从而扩展其 agents 可以执行的任务范围。

## 5。模型和训练数据

GitHub Copilot Agents 利用各种 AI 模型来增强用户所看到的体验。有关 Copilot 可用型号的比较，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>)。有关受支持型号的完整列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>)。有关模型托管位置的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>)。要了解有关 GitHub Copilot Agents 背后用于训练基础模型的数据的更多信息，请参阅上面链接的 AI 模型比较以及 GitHub Copilot 中的 [GitHub Copilot 训练过哪些数据？](<https://github.com/features/copilot#faq>) FAQ。

Copilot 代码审查是一款专门构建的产品，它使用精心调整的模型、提示和系统行为组合，在各种代码库中提供一致、高质量的反馈。不支持模型切换，因为更改模型可能会影响可靠性、用户体验和评论质量。 Copilot 代码审查可能会使用您组织的“模型”设置页面上未启用的模型。

Copilot 云 agent 使用大型语言模型来推理任务、生成代码并在其临时开发环境中利用工具。 agent 已通过多种编程语言进行了评估。英语是提示和响应的主要支持语言。

Copilot CLI 使用大型语言模型来推理任务、生成代码、修改文件以及在本地终端环境中执行命令。 agent 已通过多种编程语言进行了评估。英语是提示和响应的主要支持语言。

Copilot SDK 使用相同的底层模型和功能通过 JSON-RPC 与 Copilot CLI 进行通信。使用 SDK 构建的应用程序使用与经过身份验证的 Copilot 用户或组织可用的相同模型。开发人员还可以携带自己的 API 密钥 (BYOK) 来使用自定义模型提供程序。

## 6。性能

### 经验差异

#### Copilot云agent

Copilot 云 agent 通过结合使用自然语言处理和机器学习来理解您的任务并在代码库中进行更改。这个过程可以分为多个步骤：

1. **提示处理**：通过问题、拉取请求评论或Copilot聊天消息提供的任务与其他相关的上下文信息相结合以形成提示。该提示被发送到大型语言模型进行处理。输入可以采用简单的自然语言、代码片段或图像的形式。
1. **语言模型分析**：提示通过一个大型语言模型，这是一个经过大量数据训练的神经网络。语言模型分析输入提示，以帮助 agent 推理任务并利用必要的工具。
1. **响应生成**：语言模型根据对提示的分析生成响应。该响应可以采取自然语言建议和代码建议的形式。
1. **输出格式**：agent完成第一次运行后，它会使用所做的更改更新拉取请求描述。 agent 可能包含有关其无法访问的资源的补充信息，并提供有关解决步骤的建议。您可以通过在拉取请求中发表评论或明确提及 agent (`@copilot`) 来提供反馈。然后，agent 将重新提交该反馈以进行进一步分析，并以更新的更改进行响应。

Copilot云agent旨在为任务解决提供最相关的解决方案。然而，它可能并不总能提供您正在寻找的答案。您有责任审核和验证 Copilot 云 agent 生成的回复，以确保它们准确且适当。

#### Copilot 代码审查

Copilot 代码审查结合自然语言处理和机器学习来检查您的代码并提供反馈。这个过程可以分为多个步骤：

1. **输入处理**：代码更改与其他相关上下文信息（例如拉取请求的标题和正文）以及已定义的任何自定义指令相结合，形成提示。该提示被发送到大型语言模型。
1. **语言模型分析**：提示通过Copilot语言模型传递，该模型是经过大量文本数据训练的神经网络。语言模型分析输入提示。
1. **响应生成**：语言模型根据对输入提示的分析生成响应。该响应可以采取自然语言建议和代码建议的形式。
1. **输出格式**：响应直接在支持的编辑器中呈现给用户，或者作为 GitHub.com 上的拉取请求审查呈现给用户，提供链接到特定文件的特定行的代码反馈。如果 Copilot 提供了代码建议，则该建议将作为建议更改呈现，只需单击几下即可应用。

#### Copilot CLI

Copilot CLI 通过结合使用自然语言处理和机器学习来理解您的任务并在代码库中进行更改。这个过程可以分为多个步骤：

1. **输入处理**：您的输入与相关上下文信息结合形成提示。该提示被发送到大型语言模型进行处理。输入可以采用简单的自然语言、代码片段或终端中文件引用的形式。
1. **语言模型分析**：提示通过一个大型语言模型，这是一个经过大量数据训练的神经网络。语言模型分析输入提示，以帮助 agent 推理任务并使用必要的工具。
1. **响应生成**：语言模型根据对提示的分析生成响应。此响应可以采取自然语言建议、代码建议、文件修改和命令执行的形式。
1. **输出格式**：使用语法突出显示、缩进和其他格式功能来格式化和呈现响应。 agent 还可以在您的本地环境中执行命令并在您的文件系统中创建、编辑或删除文件以完成任务。所有此类操作都需要明确的权限提示。

在互动聊天窗口中返回响应后，您可以向agent提供反馈。 agent 会将反馈重新提交给语言模型以进行进一步分析并返回附加响应。

#### Copilot SDK

Copilot SDK 为 Copilot 的代理功能提供编程接口。使用 SDK 构建的应用程序遵循以下过程：

1. **会话创建**：应用程序创建与SDK的会话，指定模型、系统提示符、可用工具、自定义agents、MCP服务器和挂钩。 SDK 与 Copilot CLI 建立 JSON-RPC 连接。
1. **提示提交**：用户输入被提交到会话。 SDK 将提示（以及会话上下文）路由到 CLI，后者将其转发到语言模型。
1. **Agent执行**：语言模型对任务进行推理，并可能调用工具、委托给子agents或连接到MCP服务器。生命周期钩子在每个阶段触发，允许应用程序注入自定义逻辑。
1. **响应流**：响应被流式传输回应用程序，应用程序可以以适合其界面的任何格式呈现它们。 SDK 提供文本、工具调用、错误和完成信号的结构化事件。

## 7。局限性

了解 GitHub Copilot 代理功能的局限性对于确定它们在安全有效的范围内使用至关重要。虽然我们鼓励客户在其创新解决方案或应用程序中利用这些功能，但值得注意的是，它们并非针对所有可能的场景而设计。我们鼓励用户在选择用例时参考[AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms>)以及以下注意事项：

* **遗漏的代码质量问题**：Copilot可能无法识别代码中存在的所有问题，特别是在更改较大或复杂的情况下。为了确保识别并纠正所有相关问题，Copilot 代码审查应辅之以仔细的人工代码审查。
* **误报**：Copilot代码审查存在产生幻觉的风险——它可能会突出显示审查代码中不存在的问题或基于对代码的误解的问题。在采取行动和进行更改之前，应仔细审查和考虑 Copilot 代码审查生成的注释。
* **不准确或不安全的代码建议**：作为其评论的一部分，Copilot代码审查可能会提供具体的代码建议。生成的代码可能看起来有效，但实际上在语义或语法上可能不正确，或者可能无法正确解决注释中指出的问题。此外，Copilot生成的代码可能包含安全漏洞或其他问题。您应该始终仔细检查和测试 Copilot 生成的代码。
* **潜在偏差**：Copilot的训练数据取自现有代码存储库，其中可能包含该工具可能永久存在的偏差和错误。此外，Copilot 代码审查可能会偏向某些编程语言或编码风格，这可能会导致反馈不理想或不完整。
* **范围有限（云agent）**：云agent使用的语言模型已经在大量代码上进行了训练，但范围仍然有限，可能无法处理某些代码结构或晦涩的编程语言。对于每种语言，建议的质量取决于该语言的训练数据的数量和多样性。
* **不准确的代码（云agent）**：云agent可能会生成看似有效但实际上在语义或语法上可能不正确的代码，或者可能无法准确反映开发人员的意图。您应该仔细检查和测试生成的代码，特别是在处理关键或敏感应用程序时。
* **安全风险（云agent）**：云agent根据存储库中问题或评论的上下文生成代码和自然语言，如果不小心使用，可能会暴露敏感信息或漏洞。您应该在合并之前彻底检查 agent 生成的所有输出。
* **公共代码匹配（云 agent）**：即使“与公共代码匹配的建议”策略设置为“阻止”，云 agent 也可能生成与公开可用代码匹配或接近匹配的代码。如果发生这种情况，Copilot 将在 agent 会话日志中显示匹配项，并提供一个链接来显示匹配代码的详细信息。
* **范围有限（CLI）**：Copilot CLI使用的语言模型已经在大量代码上进行了训练，但范围仍然有限，可能无法处理某些代码结构或晦涩的编程语言。对于每种语言，建议的质量取决于该语言的训练数据的数量和多样性。
* **不准确的代码 (CLI)**: Copilot CLI 可能会生成看似有效但实际上在语义或语法上可能不正确的代码，或者可能无法准确反映开发人员的意图。您应该仔细检查和测试生成的代码，特别是在处理关键或敏感应用程序时。
* **安全风险 (CLI)**：Copilot CLI 根据本地环境上下文生成代码和自然语言，如果使用不小心，可能会暴露敏感信息或漏洞。您应该彻底检查 agent 生成的所有输出。
* **公共代码匹配 (CLI)**：Copilot CLI 可能会生成与公开可用代码匹配或接近匹配的代码，即使“与公共代码匹配的建议”策略设置为“阻止”也是如此。
* **命令执行风险（CLI）**：要求或允许Copilot CLI执行命令时需要额外小心，特别是关于某些建议命令的潜在破坏性。您可能会遇到文件删除或硬盘格式化的命令，如果使用不当可能会导致问题。您最终对Copilot CLI执行的命令负责。
* **界面解释（计算机使用）**：计算机使用可能会选择错误的控件，在错误的位置输入文本，或者难以处理非标准或动态控件和复杂的工作流程。时间或窗口状态的变化可能会产生不同的结果，导致计算机使用重复操作，或阻止其继续。
* **意外操作（计算机使用）**：不明确的指令或意外的屏幕内容可能会导致计算机使用执行意外操作。这些操作可能会修改或暴露数据或影响连接的帐户。用户应该缩小任务范围，指定重要的约束，并监视活动的应用程序。
* **屏幕信息（计算机使用）**：应用程序窗口可能会显示敏感信息或有关其他人的信息。用户应仅允许计算机使用其可见内容适合作为上下文提供的应用程序和任务。
* **本地和平台限制（计算机使用）**：计算机使用需要本地会话，并且可在 macOS 和 Windows 上使用。在 macOS 上，需要辅助功能和屏幕录制权限。如果捆绑的插件、帮助程序或 MCP 服务器不可用或已断开连接，则计算机使用操作无法运行。
* **始终允许的应用程序（计算机使用）**：用户选择始终允许的应用程序可以在以后的会话中进行控制，而无需其他应用程序级提示。 **始终允许**并不适合每个应用程序，特别是包含敏感信息或支持高影响力操作的应用程序。
* **继承的限制（SDK）**：由于Copilot SDK与Copilot CLI通信，因此使用SDK构建的应用程序继承了相同的限制模型限制，包括某些编程语言的有限范围以及生成不准确或不安全的代码的可能性。
* **自定义 agent 复杂性 (SDK)**：配置不正确的自定义 agents、工具或挂钩可能会产生意外行为。开发人员负责测试和验证其自定义 agent 配置的行为。
* **MCP服务器信任（SDK）**：通过SDK连接的MCP服务器可以公开来自外部源的工具和数据。开发人员必须确保连接的 MCP 服务器是值得信赖的，因为恶意或配置错误的服务器可能会引入有害行为或暴露敏感数据。
* **BYOK 模型差异 (SDK)**：与第三方模型提供商一起使用自带密钥配置时，行为可能与 GitHub 托管模型不同。开发人员负责评估其所选提供商的响应的安全性和质量。

## 8。评价

{% data reusables.rai.copilot.application-card-evaluations %}

### 性能和质量评估

GitHub Copilot Agents 使用行业标准基准（例如 SWE-Bench）和内部开发的评估套件的组合在其支持的表面上进行评估。基准任务来自公共开源存储库和合成场景；不使用真实的用户查询或客户代码。每次评估都包括多次独立运行，以解释模型输出中的不确定性。关键指标包括解决率（成功完成任务的百分比）、令牌效率、延迟和工具调用可靠性。当进行更新时，模型会被重新评估，并通过错误率、响应延迟和聚合使用模式在生产中持续监控。

### 性能和质量评价方法

GitHub Copilot Agents 的新型号在部署前经过阶段性评估过程。集成商团队运行特定于其表面的基准测试套件，在代表性编码任务（例如错误修复、代码生成和多文件重构）上测试模型。根据既定基线和现有生产模型对结果进行审查。在进入下一阶段之前，模型必须在解决率、代币效率和延迟等关键指标上达到或超过基准性能。在任何模型被批准用于面向用户的部署之前，跨职能审查委员会会做出正式的 go/no-go 决定。

### 风险和安全评估

{% data reusables.rai.copilot.application-card-risk-and-safety-evaluations %}

### 质量安全评价数据

{% data reusables.rai.copilot.application-card-evaluation-data-for-quality-and-safety %}

### 定制评估

Copilot 代理功能已受到 RAI 红队的约束，以识别和解决潜在风险。随着时间的推移，我们将继续监控这些功能的有效性和安全性。有关更多信息，请参阅 Microsoft 安全博客上的 [Microsoft AI 红队构建更安全的未来 AI](<https://www.microsoft.com/en-us/security/blog/2023/08/07/microsoft-ai-red-team-building-future-of-safer-ai/>)。

## 9。安全组件和缓解措施

### Copilot云agent

* **权限升级控制**：云agent仅响应具有存储库写入访问权限的用户的交互。由 agent 提出的拉取请求触发的操作工作流需要获得具有写入权限的用户的批准才能运行。 agent 过滤隐藏字符，这些字符可能允许用户隐藏评论或问题内容中的有害指令。
* **受限权限**：云agent只能访问正在创建拉取请求的存储库，无法访问其他存储库。它只能推送到单个分支：通过 `@copilot` 触发时的现有拉取请求分支，或者以其他方式推送到新的 `copilot/` 分支。这意味着 Copilot 无法直接推送到您的默认分支（例如 `main`）。 agent 无权访问操作组织或存储库机密 — 只有专门添加到 `copilot` 环境的机密和变量才会传递到 agent。
* **确保可追溯性**：云agent的提交由Copilot创作，启动任务的人被标记为共同作者。这样可以更轻松地识别 agent 生成的代码以及发起任务的人。云 agent 的提交已签名，因此它们在 GitHub 上显示为“已验证”。这让我们确信提交是由 GitHub Copilot 云 agent 做出的并且未被更改。每条提交消息都包含指向 agent 会话日志的链接。这为您提供了从任何 agent 创作的提交到完整会话日志的永久链接，因此您可以了解为什么 Copilot 在代码审查期间进行更改，或稍后出于审计目的进行跟踪。
* **用于防止数据泄露的防火墙**：默认情况下，云agent启用了防火墙，以防止意外或由于恶意用户输入而导致代码或其他敏感数据的泄露。
* **自动安全漏洞检测**：在代码生成过程中，云agent自动分析新生成的代码是否存在安全漏洞并尝试解决它们。使用 CodeQL（以识别潜在的漏洞和错误）、秘密扫描（以确保不引入秘密）和依赖性分析（以检查引用的依赖性中的已知漏洞）来执行分析。

### Copilot CLI

* **目录范围的访问**：默认情况下，Copilot CLI 只能访问调用它的目录中及其下的文件和文件夹。如果agent希望访问当前目录之外的文件，它会请求许可。
* **文件修改权限提示**：CopilotCLI修改文件之前请求权限。您应该在授予权限之前确保它正在修改正确的文件。
* **命令执行的权限提示**：CopilotCLI在执行可能危险的命令之前请求权限。在授予运行权限之前，您应该仔细查看这些命令。
* **可配置权限**：您可以使用各种命令行选项授予Copilot CLI特定权限或所有权限：例如，`--allow-tool=[TOOLS...]`、`--allow-all-tools`、`--allow-all`（或其斜杠命令）等效于在交互式会话中使用的 `/allow-all`）。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#command-line-options>)。通常，当您在自动驾驶模式下使用 Copilot CLI 时，您将授予其完全权限，以允许其自主完成任务，而无需您在其执行任务时批准活动。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/autopilot>)。
* **安全注意事项**：有关使用Copilot CLI时的安全实践的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>)。

### {% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.github_copilot_app %} 中的计算机使用

计算机使用是一项可选的本地功能，允许 agent 代表用户与桌面应用程序进行交互。与其他代理功能一样，计算机的使用也有局限性。由于它解释视觉界面，因此更改界面或意外的屏幕内容可能会导致结果不一致、工作流程停滞或意外操作。以下控件可帮助用户限制和中断其访问。

* **默认关闭激活**：默认情况下禁用捆绑的计算机使用插件。用户必须先启用它，然后 agent 才能使用其工具。
* **托管策略**：企业管理员可以通过托管设置禁用计算机的使用。用户无法通过本地设置覆盖企业策略。详细配置请参见【[AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#featurescomputeruse>)】。
* **工具权限控制**：计算机使用的应用程序请求使用主机权限系统。根据激活的权限模式，主机会显示批准提示或自动批准请求。显式拒绝规则优先，如果没有权限处理程序接受请求，则请求将无法关闭。
* **操作系统权限**：在macOS上，计算机使用引导用户授予与控件交互的辅助功能权限和在需要视觉上下文时检查窗口的屏幕录制权限。
* **申请审批管理**：在{% data variables.copilot.github_copilot_app %}中，用户可以查看和删除已保存的申请审批。删除应用程序会删除其保存的对未来会话的批准，但不会撤销正在运行的会话中已授予的访问权限。主持人的权限设置决定以后的请求是否显示提示或自动批准。
* **会话中断**：如果计算机使用意外开始运行，用户可以中断活动的本地操作。在{% data variables.copilot.copilot_cli %}中，按<kbd>Esc</kbd>两次会中断当前操作。在{% data variables.copilot.github_copilot_app %}中，单击**停止**或按<kbd>Esc</kbd>会中断它。

### Copilot SDK

* **继承CLI安全控制**：CopilotSDK与CopilotCLI通信，继承其权限模型和安全控制。工具执行和文件修改仍然需要适当的权限。
* **基于钩子的监督**：SDK的生命周期钩子（例如`onPreToolUse`和`onPostToolUse`）允许开发人员在工具执行之前和之后实施自定义安全检查、审核日志记录和审批工作流程。
* **MCP 服务器隔离**：MCP 服务器作为单独的进程运行。开发人员可以控制每个会话哪些服务器可用，从而限制外部工具访问的范围。
* **会话范围**：每个SDK会话都与其自己的上下文、工具和权限隔离。开发人员可以控制每个会话中可用的数据和功能。
* **BYOK 责任**：使用自带密钥配置时，提示和响应将直接发送到配置的提供程序。开发人员有责任审查其选择的提供商的数据处理政策。

### {% data variables.copilot.github_copilot_app %}

{% data variables.copilot.github_copilot_app %} 是一个桌面应用程序，用于跨本地存储库、git 工作树和云沙箱引导 agent 会话。该应用程序使用 {% data variables.copilot.copilot_cli %} 和 {% data variables.copilot.copilot_sdk %} 作为基础，并添加了用于并行会话、快速聊天、{% data variables.product.github %} 问题和拉取请求工作流程、自动化和画布的用户界面。

* **工作空间隔离**：应用程序会话可以在单独的 git 工作树或云沙箱中运行，有助于将并发任务彼此隔离以及与默认分支隔离。
* **继承了CLI和SDK安全控制**：应用程序会话使用{% data variables.copilot.copilot_cli %}和{% data variables.copilot.copilot_sdk %}，因此它们继承了文件访问、文件修改、命令执行、会话范围和连接工具的相同模型限制和安全控制。
* **合并前的人工审核**：应用程序显示生成的更改、拉取请求状态、CI结果、审核活动和会话历史记录，以便用户可以在提交评论、启用agent合并或合并拉取请求之前检查工作。
* **可配置的集成和自动化**：用户控制应用程序中可用的MCP服务器、技能、画布扩展和自动化。这些配置应在使用前进行审查，因为它们可能会影响会话可用的工具、数据和后台操作。

### 使用您自己的模型提供程序时的数据处理（CLI）

当您将 Copilot CLI 配置为使用您自己的模型提供程序时，您的提示、代码上下文和生成的响应将直接发送到您配置的提供程序。它们不通过 GitHub 路由。您有责任查看并遵守所选提供商的服务条款和数据处理政策。

#### 遥测

当您在没有离线模式的情况下使用自己的模型提供程序时，Copilot CLI 会像往常一样继续向 GitHub 发送遥测数据。此遥测不包含您的提示或代码，但包含使用情况元数据。

如果通过将 `COPILOT_OFFLINE` 环境变量设置为 `true` 来启用离线模式，则所有遥测都会被禁用。在离线模式下，Copilot CLI 仅向您配置的模型提供程序发出网络请求。

#### 身份验证和功能可用性

GitHub 使用您自己的模型提供程序 (BYOK) 时不需要身份验证。如果没有GitHub身份验证，以下功能将不可用：

* `/delegate`，将会话移交给GitHub的服务器端Copilot
* GitHub MCP 服务器
* GitHub 代码搜索

在离线模式下，基于 Web 的工具（例如 `web_fetch` 和 GitHub 代码搜索）也会被禁用。

#### 无法回退到 GitHub 托管模型

如果您的模型提供程序配置无效，Copilot CLI 将退出并出现错误。它不会回退到 GitHub 托管模型。常见故障（例如连接被拒绝、身份验证错误、未找到模型和超时）会生成用户友好的消息以及可操作的指导。

## 10。部署和采用 GitHub Copilot 代理功能的最佳实践

负责任的AI是GitHub与其客户之间的共同承诺。虽然 GitHub 构建的 AI 应用程序以安全、公平和透明为核心，但客户在自己的环境中负责任地部署和使用这些技术方面发挥着关键作用。为了支持这种合作伙伴关系，我们为部署人员和最终用户提供以下最佳实践，以帮助客户有效地实施负责任的 AI。

* **在使用 Copilot 代理特征进行后续决策或敏感领域时请谨慎行事并评估结果 **：{% data reusables.rai.copilot.application-card-consequential-decisions %}
* **评估法律和监管考虑因素**：{% data reusables.rai.copilot.application-card-evaluate-legal-regulatory %}
* **在适当的时候进行人工监督**：在与AI应用程序交互时，人工监督是一个重要的保障。虽然我们不断改进我们的 AI 应用程序，AI 仍然可能会出错。生成的输出可能不准确、不完整、有偏见、不一致或与您的预期目标无关。发生这种情况的原因有多种，例如输入的模糊性或基础模型的限制。因此，用户应该查看 Copilot 代理功能生成的响应，并验证它们是否符合他们的期望和要求。
* **注意过度依赖的风险**：{% data reusables.rai.copilot.application-card-overreliance %}
* **在敏感域中设计代理AI时要小心谨慎**：{% data reusables.rai.copilot.application-card-agentic-ai-caution %}
* **使用Copilot代码审查来补充人工审查，而不是取代它们**：虽然Copilot代码审查可以成为提高代码质量的强大工具，但重要的是将其用作工具，而不是取代人工审查。您应该始终查看和验证 Copilot 代码审核生成的反馈，并通过仔细的人工审核来补充 Copilot 的反馈，以确保您的代码满足您的要求。
* **提供反馈**：如果您在Copilot代码审查中遇到任何问题或限制，我们建议您使用Copilot评论上的“赞成”和“反对”按钮提供反馈。这可以帮助 GitHub 改进该工具并解决任何问题或限制。
* **配置自定义指令**：您可以配置自定义指令来帮助Copilot了解您的编码风格和最佳实践，从而提高审核反馈的相关性和质量。
* **确保云 agent 任务范围明确 **：分配给云 agent 的提示越清晰且范围明确，结果就越好。理想的问题包括对问题的清晰描述、完整的验收标准以及需要更改哪些文件的提示。
* **定制云 agent 有额外的背景**: 云 agent 可以访问语义代码搜索，这有助于它根据含义找到相关代码，而不仅仅是精确的文本匹配，从而使其能够更快地完成任务。为了进一步提高性能，实施定制 Copilot 指示以帮助 agent 更好地了解您的项目以及如何构建、测试和验证其更改。
* **使用云 agent 作为工具，而不是替代品**：您应该始终检查和测试云 agent 生成的内容，以确保它满足您的要求，并且在合并之前没有错误或安全问题。
* **通过云使用安全编码和代码审查实践agent**：尽管云agent可以生成语法正确的代码，但它可能并不总是安全的。遵循安全编码和代码审查的最佳实践。对使用非您独立创作的材料的任何代码采取相同的预防措施，包括严格测试、IP 扫描和检查安全漏洞。
* **保持最新状态**：云agent是一项不断发展的技术。及时了解可能出现的任何新的安全风险或最佳实践。
* **使用Copilot CLI作为工具，而不是替代品**：您应该始终检查和验证Copilot CLI生成的命令和代码，以确保它们满足您的要求并且没有错误或安全问题。
* **执行前检查命令（CLI）**：当Copilot CLI建议执行命令时要特别小心，尤其是那些修改或删除文件的命令。您最终对允许 agent 运行的命令负责。
* **保持CLI任务范围明确**：您提供的提示越清晰且范围明确，结果就越好。包括对问题的清晰描述、验收标准以及需要更改哪些文件的提示。
* **提供反馈 (CLI)**：如果您在使用 Copilot CLI 时遇到任何问题或限制，请使用 `/feedback` 命令提供反馈。
* **在可用时使用直接工具（计算机使用）**：当 API、MCP 服务器、终端命令、文件系统工具或专用浏览器工具可以完成任务时，使用该工具获取更结构化的信息和可预测的结果。
* **保持计算机使用任务的范围窄且可观察**：描述您想要的结果、涉及的应用程序以及任何重要的限制。查看有效的应用程序和结果。
* **限制始终允许的应用程序**：对于包含敏感信息或支持高影响力操作的应用程序，避免选择**始终允许**。当不再需要时查看并删除已保存的批准。
* **验证自定义 agent 行为 (SDK)**：在将使用 SDK 构建的应用程序部署到生产环境之前，彻底测试自定义 agents、工具和挂钩。确保工具配置和系统提示产生安全、预期的行为。
* **审核MCP服务器连接(SDK)**：仅连接到您信任的MCP服务器。检查每个服务器公开的工具和数据，并确保它们符合您的应用程序的安全要求。
* **实施安全挂钩 (SDK)**：使用 SDK 的生命周期挂钩在应用程序中实施内容过滤、审核日志记录和工具审批工作流程等防护措施。
* **适当范围会话 (SDK)**：仅使用当前任务所需的工具、agents 和权限配置每个 SDK 会话。当范围狭窄就足够时，避免授予广泛的访问权限。
* **查看 BYOK 提供商策略 (SDK)**：如果使用自带密钥配置，请确保您选择的模型提供商的服务条款和数据处理策略满足您的组织的要求。

## 11。了解有关 GitHub Copilot 代理功能的更多信息

有关负责任地使用 Copilot 代理功能的其他指导，我们建议查看以下文档：

* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/github-copilot-app>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/computer-use>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [Copilot 信任中心](<https://copilot.github.trust.page/>)

### 了解有关负责任的更多信息 AI

* [Microsoft AI 原理](<https://www.microsoft.com/en-us/ai/responsible-ai>)
* [Microsoft负责AI资源](<https://www.microsoft.com/en-us/ai/responsible-ai-resources>)
* [Microsoft 负责任的 Azure 学习课程 AI](<https://docs.microsoft.com/en-us/learn/paths/responsible-ai-business-principles/>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## What is an Application Card?

{% data reusables.rai.copilot.application-card-intro %}

## 1. Overview

GitHub Copilot includes several agentic features that go beyond suggestion and conversation—they can review code, take action on your behalf, and build applications. This card covers the following experiences:

* **Copilot code review**: Reviews pull request diffs and metadata on GitHub.com, producing feedback comments and suggested changes.
* **Copilot cloud agent**: An asynchronous agent on GitHub.com that can create branches, write code, and open pull requests in response to assigned issues. The cloud agent runs in an ephemeral, firewalled environment with automated security scanning.
* **Copilot CLI**: A command-line tool that can create and modify files, execute commands, and perform multi-step tasks. By default, filesystem access is scoped to the directory where the CLI was started. Permission prompts depend on the active permission mode. When computer use is enabled, Copilot CLI can also interact with desktop applications outside that directory scope.
* **Copilot SDK**: A programmatic library that allows developers to build custom AI-powered applications using Copilot. The SDK communicates with Copilot CLI over JSON-RPC and supports custom agents, MCP server integrations, lifecycle hooks, and session management.
* **{% data variables.copilot.github_copilot_app %}**: A desktop application for directing agent sessions across local repositories, git worktrees, and cloud sandboxes. The app uses {% data variables.copilot.copilot_cli %} and {% data variables.copilot.copilot_sdk %} as its foundation and adds a user interface for parallel sessions, quick chats, {% data variables.product.github %} issue and pull request workflows, automations, and canvases. When computer use is enabled, the app can also interact with desktop applications.

These features share common principles—human oversight, review of outputs, and responsible use—but differ in their execution environments, permissions, and data flows. The sections below describe each in context.

## 2. Key terms

The following list provides a glossary of key terms related to GitHub Copilot Agents:

* **Code suggestion**: A specific code change proposed by Copilot code review as part of its feedback on a pull request. Code suggestions are presented as suggested changes that can be applied with a couple of clicks.
* **Content filtering**: A safety system that scans prompts and responses to detect and block harmful, offensive, or insecure content before it is shown to the user.
* **Computer use**: An optional capability in {% data variables.copilot.copilot_cli %} and {% data variables.copilot.github_copilot_app %} that allows an agent to interact with desktop applications. Its tools can read accessible application content and visual context through the operating system's accessibility tree or screenshots when visual context is needed, click controls, enter and edit text, press keys, scroll, drag, and navigate workflows across applications.
* **Custom instructions**: Natural language descriptions of coding style and best practices that a repository maintainer can configure to guide Copilot code review's feedback. Custom instructions help Copilot understand the conventions and standards of a specific codebase.
* **Hallucination**: A phenomenon where a language model generates output that sounds plausible but is factually incorrect, unsupported by the provided context, or entirely fabricated. In code review, hallucination can manifest as feedback that highlights problems that do not exist or are based on misunderstandings of the code.
* **Large language model (LLM)**: A type of neural network trained on a large body of text data that can generate, analyze, and transform natural language and code. Copilot Agents use one or more LLMs to process context and produce responses.
* **Ephemeral development environment**: A temporary, isolated compute environment created for each cloud agent session. The environment is destroyed after the session ends, ensuring that no state persists between runs.
* **Firewall**: A network-level control enabled by default for the cloud agent that prevents outbound connections to unauthorized hosts, protecting against accidental or malicious exfiltration of code or data.
* **AI credit**: A unit of consumption for Copilot features. Each use of Copilot code review consumes AI credits.
* **Permission prompt**: An interactive confirmation step in Copilot CLI that asks the user to approve an action—such as modifying a file, executing a command, or accessing files outside the current directory—before the agent proceeds. Permission prompts are a key safety mechanism for local agentic execution.
* **Copilot SDK**: A programmatic library (`@github/copilot-sdk`) that lets developers build custom AI-powered applications by creating sessions, sending prompts, and receiving streaming responses from Copilot. The SDK communicates with Copilot CLI over JSON-RPC.
* **JSON-RPC**: The communication protocol used between the Copilot SDK and Copilot CLI. The SDK sends structured requests to the CLI process, which handles model interaction and tool execution.
* **Custom agent (SDK)**: A named agent configuration within the SDK that has its own system prompt, scoped tools, and optional MCP servers. The SDK runtime can automatically delegate to sub-agents based on user intent.
* **Hooks (SDK)**: Lifecycle callbacks in the Copilot SDK that let developers inject custom logic at specific points during a session, such as before or after tool use, on session start or end, and on error.

## 3. Key features or capabilities

The key features and capabilities outlined here describe what GitHub Copilot Agents are designed to do and how they perform across supported tasks.

* **Automated code review feedback**: When a user requests a review from Copilot, it scans the code changes plus additional relevant context and provides feedback on the code. Feedback may include natural language comments and specific code suggestions linked to particular lines and files.
* **Customizable review guidance**: Copilot code review can be customized with custom instructions—natural language descriptions of coding style and best practices—so that feedback reflects a repository's conventions and standards.
* **Autonomous pull request creation**: The Copilot cloud agent can pick up a task from an issue, a pull request comment, or Copilot Chat, create a branch, generate tailored code changes, and open a pull request. After the initial pull request is created, the agent can iterate based on your feedback and reviews.
* **Ephemeral, firewalled execution**: While working on a task, the cloud agent has access to its own ephemeral development environment where it can make changes to code, execute automated tests, and run linters. A firewall is enabled by default to prevent data exfiltration.
* **Automated security scanning**: During code generation, the cloud agent automatically analyzes newly generated code for security vulnerabilities using CodeQL, secret scanning, and dependency analysis, and attempts to resolve any issues before they are introduced.
* **External integrations**: The cloud agent can receive information and context from MCP like workIQ and Microsoft 365, and external applications like Microsoft Teams, Linear, Slack, and Jira, enabling teams to assign tasks and track progress directly within their existing workflows.
* **Local agentic execution (Copilot CLI)**: Copilot CLI provides a chat-like interface in the terminal that can autonomously create and modify files, execute commands, and perform multi-step tasks. By default, filesystem access is scoped to the directory where the CLI was started. The active permission mode determines whether the CLI prompts before modifying files or running commands.
* **Desktop application interaction (computer use)**: In local {% data variables.copilot.copilot_cli_short %} and {% data variables.copilot.github_copilot_app_short %} sessions, computer use can read accessible application content and visual context through the operating system's accessibility tree or screenshots when visual context is needed, click controls, enter and edit text, press keys, scroll, drag, and navigate workflows across applications.

## 4. Intended uses

GitHub Copilot Agents can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

* **Supplementing human code review**: Copilot code review is intended to quickly provide feedback on a developer's code, enabling developers to get code ready to merge more quickly and increasing overall code quality.
* **Codebase maintenance**: The cloud agent can tackle security-related fixes, dependency upgrades, and targeted refactoring.
* **Feature development**: The cloud agent can implement incremental feature requests, develop additional test suites, and create or update documentation.
* **Prototyping new projects**: The cloud agent and Copilot CLI can greenfield new concepts, helping developers explore ideas quickly.
* **Setting up your environment (CLI)**: Copilot CLI can run commands in your terminal to set up your local environment to work on existing projects.
* **Finding the right command (CLI)**: Copilot CLI can suggest commands to perform tasks you're trying to complete, and explain unfamiliar commands in natural language.
* **Working with legacy and GUI-only applications (computer use)**: Computer use can help with workflows in local desktop applications that do not provide an API, command-line interface, or MCP integration. Users should describe the outcome, applications, and important constraints, then review the actions and result.
* **Building custom AI applications (SDK)**: The Copilot SDK enables developers to build applications that leverage Copilot for code generation, natural language interaction, and task automation in their own products and workflows.
* **Multi-agent orchestration (SDK)**: Using custom agents and sub-agents, developers can build sophisticated workflows where multiple specialized agents collaborate on complex tasks, with automatic delegation based on user intent.
* **Extending applications with external tools (SDK)**: The SDK's MCP server support allows developers to connect their applications to external data sources and services, expanding the range of tasks their agents can perform.

## 5. Models and training data

GitHub Copilot Agents leverage a variety of AI models to power the experience that users see. For a comparison of the models available for Copilot, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>). For the full list of supported models, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>). For information on where models are hosted, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>). To learn more about the data used to train the foundation models behind GitHub Copilot Agents, refer to the linked AI model comparison above and [What data has GitHub Copilot been trained on?](<https://github.com/features/copilot#faq>) in the GitHub Copilot FAQ.

Copilot code review is a purpose-built product that uses a carefully tuned mix of models, prompts, and system behaviors to deliver consistent, high-quality feedback across a wide range of codebases. Model switching is not supported, as changing the model is likely to compromise reliability, user experience, and the quality of review comments. Copilot code review may use models that are not enabled on your organization's "Models" settings page.

The Copilot cloud agent uses a large language model to reason about tasks, generate code, and leverage tools within its ephemeral development environment. The agent has been evaluated across a variety of programming languages. English is the primary supported language for prompts and responses.

Copilot CLI uses a large language model to reason about tasks, generate code, modify files, and execute commands in your local terminal environment. The agent has been evaluated across a variety of programming languages. English is the primary supported language for prompts and responses.

The Copilot SDK communicates with Copilot CLI over JSON-RPC, using the same underlying models and capabilities. Applications built with the SDK use the same models available to the authenticated Copilot user or organization. Developers can also bring their own API keys (BYOK) to use custom model providers.

## 6. Performance

### Differences by experience

#### Copilot cloud agent

The Copilot cloud agent works by using a combination of natural language processing and machine learning to understand your task and make changes in a codebase. This process can be broken down into a number of steps:

1. **Prompt processing**: The task provided through an issue, pull request comment, or Copilot Chat message is combined with other relevant, contextual information to form a prompt. That prompt is sent to a large language model for processing. Inputs can take the form of plain natural language, code snippets, or images.
1. **Language model analysis**: The prompt is passed through a large language model, which is a neural network that has been trained on a large body of data. The language model analyzes the input prompt to help the agent reason about the task and leverage necessary tools.
1. **Response generation**: The language model generates a response based on its analysis of the prompt. This response can take the form of natural language suggestions and code suggestions.
1. **Output formatting**: Once the agent completes its first run, it updates the pull request description with the changes it made. The agent may include supplemental information about resources it could not access and provide suggestions on steps to resolve. You may provide feedback by commenting within the pull request or explicitly mentioning the agent (`@copilot`). The agent will then resubmit that feedback for further analysis and respond with updated changes.

Copilot cloud agent is intended to provide the most relevant solution for task resolution. However, it may not always provide the answer you are looking for. You are responsible for reviewing and validating responses generated by Copilot cloud agent to ensure they are accurate and appropriate.

#### Copilot code review

Copilot code review inspects your code and provides feedback using a combination of natural language processing and machine learning. This process can be broken down into a number of steps:

1. **Input processing**: The code changes are combined with other relevant, contextual information (for example, the pull request's title and body), and any custom instructions that have been defined, to form a prompt. That prompt is sent to a large language model.
1. **Language model analysis**: The prompt is passed through the Copilot language model, which is a neural network that has been trained on a large body of text data. The language model analyzes the input prompt.
1. **Response generation**: The language model generates a response based on its analysis of the input prompt. This response can take the form of natural language suggestions and code suggestions.
1. **Output formatting**: The response is presented to the user either directly in a supported editor or as a pull request review on GitHub.com, providing code feedback linked to specific lines of specific files. Where Copilot has provided a code suggestion, the suggestion is presented as a suggested change, which can be applied with a couple of clicks.

#### Copilot CLI

Copilot CLI works by using a combination of natural language processing and machine learning to understand your task and make changes in a codebase. This process can be broken down into a number of steps:

1. **Input processing**: Your input is combined with relevant contextual information to form a prompt. That prompt is sent to a large language model for processing. Inputs can take the form of plain natural language, code snippets, or references to files in your terminal.
1. **Language model analysis**: The prompt is passed through a large language model, which is a neural network that has been trained on a large body of data. The language model analyzes the input prompt to help the agent reason about the task and use the necessary tools.
1. **Response generation**: The language model generates a response based on its analysis of the prompt. This response can take the form of natural language suggestions, code suggestions, file modifications, and command executions.
1. **Output formatting**: The response is formatted and presented using syntax highlighting, indentation, and other formatting features. The agent may also execute commands in your local environment and create, edit, or delete files in your file system in order to complete the task. All such actions require explicit permission prompts.

You may provide feedback to the agent after it returns a response in the interactive chat window. The agent will resubmit that feedback to the language model for further analysis and return an additional response.

#### Copilot SDK

The Copilot SDK provides a programmatic interface to Copilot's agentic capabilities. Applications built with the SDK follow this process:

1. **Session creation**: The application creates a session with the SDK, specifying the model, system prompt, available tools, custom agents, MCP servers, and hooks. The SDK establishes a JSON-RPC connection to Copilot CLI.
1. **Prompt submission**: User input is submitted to the session. The SDK routes the prompt (along with session context) to the CLI, which forwards it to the language model.
1. **Agent execution**: The language model reasons about the task and may invoke tools, delegate to sub-agents, or connect to MCP servers. Lifecycle hooks fire at each stage, allowing the application to inject custom logic.
1. **Response streaming**: Responses are streamed back to the application, which can present them in any format appropriate for its interface. The SDK provides structured events for text, tool calls, errors, and completion signals.

## 7. Limitations

Understanding GitHub Copilot agentic features' limitations is crucial to determine they are used within safe and effective boundaries. While we encourage customers to leverage these features in their innovative solutions or applications, it's important to note that they were not designed for every possible scenario. We encourage users to refer to [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms>) as well as the following considerations when choosing a use case:

* **Missed code quality problems**: Copilot may not identify all of the problems that are present in code, especially where changes are large or complex. To ensure that all relevant problems are identified and corrected, Copilot code review should be supplemented with careful human code review.
* **False positives**: Copilot code review has a risk of hallucination—it may highlight problems in reviewed code that do not exist or are based on misunderstandings of the code. Comments generated by Copilot code review should be carefully reviewed and considered before taking action and making changes.
* **Inaccurate or insecure code suggestions**: As part of its comments, Copilot code review may provide specific code suggestions. The code generated may appear to be valid but may not actually be semantically or syntactically correct, or may not correctly resolve the problem identified in the comment. In addition, code generated by Copilot may contain security vulnerabilities or other issues. You should always carefully review and test code generated by Copilot.
* **Potential biases**: Copilot's training data is drawn from existing code repositories, which may contain biases and errors that can be perpetuated by the tool. Additionally, Copilot code review may be biased toward certain programming languages or coding styles, which can lead to suboptimal or incomplete feedback.
* **Limited scope (cloud agent)**: The language model used by the cloud agent has been trained on a large body of code but still has a limited scope and may not be able to handle certain code structures or obscure programming languages. For each language, the quality of suggestions depends on the volume and diversity of training data for that language.
* **Inaccurate code (cloud agent)**: The cloud agent may generate code that appears to be valid but may not actually be semantically or syntactically correct, or may not accurately reflect the intent of the developer. You should carefully review and test generated code, particularly when dealing with critical or sensitive applications.
* **Security risks (cloud agent)**: The cloud agent generates code and natural language based on the context of an issue or comment within a repository, which can potentially expose sensitive information or vulnerabilities if not used carefully. You should review all outputs generated by the agent thoroughly prior to merging.
* **Public code matches (cloud agent)**: The cloud agent may generate code that is a match or near match of publicly available code, even if the "Suggestions matching public code" policy is set to "Block." If this happens, Copilot will show matches in the agent session logs with a link to display details of the matched code.
* **Limited scope (CLI)**: The language model used by Copilot CLI has been trained on a large body of code but still has a limited scope and may not be able to handle certain code structures or obscure programming languages. For each language, the quality of suggestions depends on the volume and diversity of training data for that language.
* **Inaccurate code (CLI)**: Copilot CLI may generate code that appears to be valid but may not actually be semantically or syntactically correct, or may not accurately reflect the intent of the developer. You should carefully review and test generated code, particularly when dealing with critical or sensitive applications.
* **Security risks (CLI)**: Copilot CLI generates code and natural language based on the context of your local environment, which can potentially expose sensitive information or vulnerabilities if not used carefully. You should review all outputs generated by the agent thoroughly.
* **Public code matches (CLI)**: Copilot CLI may generate code that is a match or near match of publicly available code, even if the "Suggestions matching public code" policy is set to "Block."
* **Command execution risks (CLI)**: Additional caution is required when asking or allowing Copilot CLI to execute a command, particularly regarding the potential destructiveness of some suggested commands. You may encounter commands for file deletion or hard drive formatting, which can cause problems if used incorrectly. You are ultimately responsible for the commands executed by Copilot CLI.
* **Interface interpretation (computer use)**: Computer use may select the wrong control, enter text in the wrong location, or have difficulty with non-standard or dynamic controls and complex workflows. Changes in timing or window state can produce different results, cause computer use to repeat an action, or prevent it from continuing.
* **Unintended actions (computer use)**: Ambiguous instructions or unexpected on-screen content can cause computer use to take unintended actions. These actions may modify or expose data or affect connected accounts. Users should keep tasks narrow, specify important constraints, and monitor the active application.
* **On-screen information (computer use)**: Application windows may display sensitive information or information about other people. Users should only enable computer use for applications and tasks whose visible content is appropriate to provide as context.
* **Local and platform constraints (computer use)**: Computer use requires a local session and is available on macOS and Windows. On macOS, Accessibility and Screen Recording permissions are required. If the bundled plugin, helper, or MCP server is unavailable or disconnected, computer-use actions cannot run.
* **Always allowed applications (computer use)**: An application that a user chooses to always allow can be controlled in later sessions without another application-level prompt. **Always allow** is not appropriate for every application, particularly applications that contain sensitive information or support high-impact actions.
* **Inherited limitations (SDK)**: Because the Copilot SDK communicates with Copilot CLI, applications built with the SDK inherit the same model limitations, including limited scope for certain programming languages and the potential for inaccurate or insecure code generation.
* **Custom agent complexity (SDK)**: Incorrectly configured custom agents, tools, or hooks may produce unexpected behavior. Developers are responsible for testing and validating the behavior of their custom agent configurations.
* **MCP server trust (SDK)**: MCP servers connected through the SDK can expose tools and data from external sources. Developers must ensure that connected MCP servers are trustworthy, as malicious or misconfigured servers could introduce harmful behavior or expose sensitive data.
* **BYOK model variance (SDK)**: When using bring-your-own-key configurations with third-party model providers, behavior may differ from GitHub-hosted models. Developers are responsible for evaluating the safety and quality of responses from their chosen provider.

## 8. Evaluations

{% data reusables.rai.copilot.application-card-evaluations %}

### Performance and quality evaluations

GitHub Copilot Agents are evaluated across its supported surfaces using a combination of industry-standard benchmarks (e.g., SWE-Bench) and internally developed evaluation suites. Benchmark tasks are sourced from public open-source repositories and synthetic scenarios; no real user queries or customer code are used. Each evaluation includes multiple independent runs to account for nondeterminism in model outputs. Key metrics include resolution rate (percentage of tasks successfully completed), token efficiency, latency, and tool call reliability. Models are re-evaluated when updates are made and monitored continuously in production via error rates, response latency, and aggregate usage patterns. 

### Performance and quality evaluation methods

New models for GitHub Copilot Agents undergo a staged evaluation process before deployment. Integrator teams run benchmark suites specific to their surface, testing the model on representative coding tasks such as bug fixes, code generation, and multi-file refactoring. Results are reviewed against established baselines and existing production models. Models must meet or exceed baseline performance across key metrics like resolution rate, token efficiency, and latency, before advancing to the next stage. A cross-functional review board makes a formal go/no-go decision before any model is approved for user-facing deployment. 

### Risk and safety evaluations

{% data reusables.rai.copilot.application-card-risk-and-safety-evaluations %}

### Evaluation data for quality and safety

{% data reusables.rai.copilot.application-card-evaluation-data-for-quality-and-safety %}

### Custom evaluations

Copilot agentic features have been subject to RAI red teaming to identify and address potential risks. We continue to monitor the efficacy and safety of these features over time. For more information, see [Microsoft AI Red Team building future of safer AI](<https://www.microsoft.com/en-us/security/blog/2023/08/07/microsoft-ai-red-team-building-future-of-safer-ai/>) on the Microsoft security blog.

## 9. Safety components and mitigations

### Copilot cloud agent

* **Privilege escalation controls**: The cloud agent only responds to interactions from users with repository write access. Actions workflows triggered by pull requests raised by the agent require approval from a user with write access before they will run. The agent filters hidden characters that might allow users to hide harmful instructions in comments or issue contents.
* **Constrained permissions**: The cloud agent only has access to the repository where it is creating a pull request and cannot access other repositories. It can only push to a single branch: the existing pull request branch when triggered via `@copilot`, or otherwise to a new `copilot/` branch. This means that Copilot cannot push directly to your default branch (for example, `main`). The agent does not have access to Actions organization or repository secrets—only secrets and variables specifically added to the `copilot` environment are passed to the agent.
* **Ensuring traceability**: The cloud agent's commits are authored by Copilot, with the human who started the task marked as the co-author. This makes it easier to identify code generated by the agent and who initiated the task. The cloud agent's commits are signed, so they appear as "Verified" on GitHub. This provides confidence that the commits were made by GitHub Copilot cloud agent and have not been altered. Each commit message includes a link to the agent session logs. This gives you a permanent link from any agent-authored commit to the full session logs, so you can understand why Copilot made a change during code review or trace it later for auditing purposes.
* **Firewall for data exfiltration prevention**: By default, the cloud agent has a firewall enabled to prevent exfiltration of code or other sensitive data, either accidentally or due to malicious user input.
* **Automated security vulnerability detection**: During code generation, the cloud agent automatically analyzes newly generated code for security vulnerabilities and attempts to resolve them. Analysis is performed using CodeQL (to identify potential vulnerabilities and errors), secret scanning (to ensure secrets aren't introduced), and dependency analysis (to check for known vulnerabilities in referenced dependencies).

### Copilot CLI

* **Directory-scoped access**: By default, Copilot CLI only has access to files and folders in, and below, the directory from which it was invoked. If the agent wishes to access files outside the current directory, it will ask for permission.
* **Permission prompts for file modifications**: Copilot CLI asks for permission before modifying files. You should ensure it is modifying the correct files before granting permission.
* **Permission prompts for command execution**: Copilot CLI asks for permission before executing commands that may be dangerous. You should review these commands carefully before giving permission to run.
* **Configurable permissions**: You can grant Copilot CLI specific permissions, or all permissions, by using the various command line options: for example, `--allow-tool=[TOOLS...]`, `--allow-all-tools`, `--allow-all` (or its slash command equivalent `/allow-all` for use in an interactive session). For more information, see [AUTOTITLE](<https://github.com/copilot/reference/copilot-cli-reference/cli-command-reference#command-line-options>). Typically, when you use Copilot CLI in autopilot mode, you will grant it full permissions to allow it to complete a task autonomously, without requiring you to approve activity as it works on the task. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/autopilot>).
* **Security considerations**: For more information about security practices while using Copilot CLI, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli#security-considerations>).

### Computer use in {% data variables.copilot.copilot_cli %} and {% data variables.copilot.github_copilot_app %}

Computer use is an optional local capability that allows an agent to interact with desktop applications on a user's behalf. Like other agentic features, computer use has limitations. Because it interprets visual interfaces, changing interfaces or unexpected on-screen content can lead to inconsistent results, stalled workflows, or unintended actions. The following controls help users limit and interrupt its access.

* **Default-off activation**: The bundled computer-use plugin is disabled by default. Users must enable it before its tools are available to an agent.
* **Managed policy**: Enterprise administrators can disable computer use through managed settings. Users cannot override the enterprise policy through local settings. For configuration details, see [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings#featurescomputeruse>).
* **Tool permission controls**: Computer-use application requests use the host permission system. Depending on the active permission mode, the host displays an approval prompt or approves the request automatically. Explicit deny rules take precedence, and a request fails closed if no permission handler accepts it.
* **Operating system permissions**: On macOS, computer use guides users through granting Accessibility permission to interact with controls and Screen Recording permission to inspect windows when visual context is needed.
* **Application approval management**: In {% data variables.copilot.github_copilot_app %}, users can review and remove saved application approvals. Removing an application deletes its saved approval for future sessions, but does not revoke access already granted in a running session. The host's permission settings determine whether later requests display a prompt or are approved automatically.
* **Session interruption**: Users can interrupt an active local operation if computer use starts acting unexpectedly. In {% data variables.copilot.copilot_cli %}, pressing <kbd>Esc</kbd> twice interrupts the current operation. In {% data variables.copilot.github_copilot_app %}, clicking **Stop** or pressing <kbd>Esc</kbd> interrupts it.

### Copilot SDK

* **Inherited CLI safety controls**: The Copilot SDK communicates with Copilot CLI, inheriting its permission model and safety controls. Tool executions and file modifications still require appropriate permissions.
* **Hook-based oversight**: The SDK's lifecycle hooks (such as `onPreToolUse` and `onPostToolUse`) allow developers to implement custom safety checks, audit logging, and approval workflows before and after tool execution.
* **MCP server isolation**: MCP servers run as separate processes. Developers can control which servers are available per session, limiting the scope of external tool access.
* **Session scoping**: Each SDK session is isolated with its own context, tools, and permissions. Developers can control what data and capabilities are available within each session.
* **BYOK responsibility**: When using bring-your-own-key configurations, prompts and responses are sent directly to the configured provider. Developers are responsible for reviewing the data handling policies of their chosen provider.

### {% data variables.copilot.github_copilot_app %}

The {% data variables.copilot.github_copilot_app %} is a desktop application for directing agent sessions across local repositories, git worktrees, and cloud sandboxes. The app uses {% data variables.copilot.copilot_cli %} and {% data variables.copilot.copilot_sdk %} as its foundation and adds a user interface for parallel sessions, quick chats, {% data variables.product.github %} issue and pull request workflows, automations, and canvases.

* **Workspace isolation**: App sessions can run in separate git worktrees or cloud sandboxes, helping isolate concurrent tasks from each other and from the default branch.
* **Inherited CLI and SDK safety controls**: App sessions use {% data variables.copilot.copilot_cli %} and {% data variables.copilot.copilot_sdk %}, so they inherit the same model limitations and safety controls for file access, file modification, command execution, session scoping, and connected tools.
* **Human review before merge**: The app surfaces generated changes, pull request status, CI results, review activity, and session history so users can inspect work before submitting reviews, enabling agent merge, or merging pull requests.
* **Configurable integrations and automations**: Users control which MCP servers, skills, canvas extensions, and automations are available in the app. These configurations should be reviewed before use because they can affect the tools, data, and background actions available to sessions.

### Data handling when using your own model provider (CLI)

When you configure Copilot CLI to use your own model provider, your prompts, code context, and generated responses are sent directly to the provider you configure. They are not routed through GitHub. You are responsible for reviewing and complying with the terms of service and data handling policies of your chosen provider.

#### Telemetry

When you use your own model provider without offline mode, Copilot CLI continues to send telemetry to GitHub as usual. This telemetry does not include your prompts or code, but it does include usage metadata.

If you enable offline mode by setting the `COPILOT_OFFLINE` environment variable to `true`, all telemetry is disabled. In offline mode, Copilot CLI only makes network requests to your configured model provider.

#### Authentication and feature availability

GitHub authentication is not required when using your own model provider (BYOK). Without GitHub authentication, the following features are unavailable:

* `/delegate`, which hands off the session to GitHub's server-side Copilot
* The GitHub MCP server
* GitHub Code Search

In offline mode, web-based tools such as `web_fetch` and GitHub Code Search are also disabled.

#### No fallback to GitHub-hosted models

If your model provider configuration is invalid, Copilot CLI exits with an error. It does not fall back to GitHub-hosted models. Common failures, such as connection refused, authentication errors, model not found, and timeouts, produce user-friendly messages with actionable guidance.

## 10. Best practices for deploying and adopting GitHub Copilot agentic features

Responsible AI is a shared commitment between GitHub and its customers. While GitHub builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

* **Exercise caution and evaluate outcomes when using Copilot agentic features for consequential decisions or in sensitive domains**: {% data reusables.rai.copilot.application-card-consequential-decisions %}
* **Evaluate legal and regulatory considerations**: {% data reusables.rai.copilot.application-card-evaluate-legal-regulatory %}
* **Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by Copilot agentic features and verify that they match their expectations and requirements.
* **Be aware of the risk of overreliance**: {% data reusables.rai.copilot.application-card-overreliance %}
* **Exercise caution when designing agentic AI in sensitive domains**: {% data reusables.rai.copilot.application-card-agentic-ai-caution %}
* **Use Copilot code review to supplement human reviews, not to replace them**: While Copilot code review can be a powerful tool for improving code quality, it is important to use it as a tool, rather than to replace human reviews. You should always review and verify the feedback generated by Copilot code review, and supplement Copilot's feedback with careful human review to ensure your code meets your requirements.
* **Provide feedback**: If you encounter any issues or limitations with Copilot code review, we recommend that you provide feedback by using the thumbs up and thumbs down buttons on Copilot's comments. This can help GitHub improve the tool and address any concerns or limitations.
* **Configure custom instructions**: You can configure custom instructions to help Copilot understand your coding style and best practices, improving the relevance and quality of review feedback.
* **Ensure cloud agent tasks are well-scoped**: The more clear and well-scoped the prompt you assign to the cloud agent, the better the results. An ideal issue includes a clear description of the problem, complete acceptance criteria, and hints on what files need to be changed.
* **Customize the cloud agent with additional context**: The cloud agent has access to semantic code search, which helps it find relevant code based on meaning rather than just exact text matches, allowing it to complete tasks faster. To further enhance performance, implement custom Copilot instructions to help the agent better understand your project and how to build, test, and validate its changes.
* **Use the cloud agent as a tool, not a replacement**: You should always review and test the content generated by the cloud agent to ensure that it meets your requirements and is free of errors or security concerns prior to merging.
* **Use secure coding and code review practices with the cloud agent**: Although the cloud agent can generate syntactically correct code, it may not always be secure. Follow best practices for secure coding and code review. Take the same precautions as you would with any code that uses material you did not independently originate, including rigorous testing, IP scanning, and checking for security vulnerabilities.
* **Stay up to date**: The cloud agent is an evolving technology. Stay up to date with any new security risks or best practices that may emerge.
* **Use Copilot CLI as a tool, not a replacement**: You should always review and verify commands and code generated by Copilot CLI to ensure they meet your requirements and are free of errors or security concerns.
* **Review commands before execution (CLI)**: Exercise particular caution when Copilot CLI suggests executing commands, especially those that modify or delete files. You are ultimately responsible for the commands you allow the agent to run.
* **Keep CLI tasks well-scoped**: The more clear and well-scoped the prompt you provide, the better the results. Include a clear description of the problem, acceptance criteria, and hints on what files need to be changed.
* **Provide feedback (CLI)**: If you encounter any issues or limitations with Copilot CLI, provide feedback using the `/feedback` command.
* **Use direct tools when available (computer use)**: When an API, MCP server, terminal command, filesystem tool, or dedicated browser tool can complete a task, use that tool for more structured information and predictable results.
* **Keep computer-use tasks narrow and observable**: Describe the outcome you want, the applications involved, and any important constraints. Review the active application and outcome.
* **Limit always allowed applications**: Avoid choosing **Always allow** for applications that contain sensitive information or support high-impact actions. Review and remove saved approvals when they are no longer needed.
* **Validate custom agent behavior (SDK)**: Thoroughly test custom agents, tools, and hooks before deploying applications built with the SDK to production. Ensure that tool configurations and system prompts produce safe, expected behavior.
* **Audit MCP server connections (SDK)**: Only connect to MCP servers that you trust. Review the tools and data that each server exposes and ensure they align with your application's security requirements.
* **Implement safety hooks (SDK)**: Use the SDK's lifecycle hooks to implement guardrails such as content filtering, audit logging, and tool approval workflows in your applications.
* **Scope sessions appropriately (SDK)**: Configure each SDK session with only the tools, agents, and permissions required for the task at hand. Avoid granting broad access when narrow scoping is sufficient.
* **Review BYOK provider policies (SDK)**: If using bring-your-own-key configurations, ensure your chosen model provider's terms of service and data handling policies meet your organization's requirements.

## 11. Learn more about GitHub Copilot agentic features

For additional guidance on the responsible use of Copilot agentic features, we recommend reviewing the following documentation:

* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/request-a-code-review/use-code-review>)
* [AUTOTITLE](<https://github.com/copilot/tutorials/cloud-agent/get-the-best-results>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)
* [AUTOTITLE](<https://github.com/copilot/get-started/sdk-quickstart>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/github-copilot-app>)
* [AUTOTITLE](<https://github.com/copilot/concepts/agents/computer-use>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [Copilot Trust Center](<https://copilot.github.trust.page/>)

### Learn more about responsible AI

* [Microsoft AI principles](<https://www.microsoft.com/en-us/ai/responsible-ai>)
* [Microsoft responsible AI resources](<https://www.microsoft.com/en-us/ai/responsible-ai-resources>)
* [Microsoft Azure Learning courses on responsible AI](<https://docs.microsoft.com/en-us/learn/paths/responsible-ai-business-principles/>)


</details>
