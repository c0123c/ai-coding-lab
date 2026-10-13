# Copilot Chat 应用能力与限制说明

介绍聊天能力、不同使用界面、模型、评估与限制，以及部署和使用建议；用于了解产品边界。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/github/docs/commit/d9fd377ea956f59385ac8cb2034ba5c613b6fd85>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/responsible-use/chat.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot Chat 应用能力与限制说明.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: 'Application card: GitHub Copilot Chat'
shortTitle: Chat
intro: 'Learn how to use GitHub Copilot Chat responsibly by understanding its purposes, capabilities, and limitations.'
versions:
  feature: copilot
redirect_from:
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-chat-in-githubcom
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-chat-in-github
  - /copilot/responsible-use-of-github-copilot-features/chat-in-github
  - /copilot/responsible-use/chat-in-github
  - /early-access/copilot/github-copilot-chat-transparency-note
  - /early-access/copilot/github-copilot-chat-technical-preview-license-terms
  - /copilot/github-copilot-chat/about-github-copilot-chat
  - /copilot/github-copilot-chat/copilot-chat-in-ides/about-github-copilot-chat-in-your-ide
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-chat-in-your-ide
  - /copilot/responsible-use-of-github-copilot-features/chat-in-your-ide
  - /copilot/responsible-use/chat-in-your-ide
  - /early-access/copilot/about-github-copilot-chat-in-github-mobile
  - /copilot/github-copilot-chat/about-github-copilot-chat-in-github-mobile
  - /copilot/github-copilot-chat/copilot-chat-in-github-mobile/about-github-copilot-chat-in-github-mobile
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-chat-in-github-mobile
  - /copilot/responsible-use-of-github-copilot-features/chat-in-github-mobile
  - /copilot/responsible-use/chat-in-github-mobile
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-in-windows-terminal
  - /copilot/responsible-use-of-github-copilot-features/copilot-in-windows-terminal
  - /copilot/responsible-use/copilot-in-windows-terminal
  - /copilot/responsible-use/copilot-spaces
  - /copilot/github-copilot-enterprise/copilot-pull-request-summaries/about-copilot-pull-request-summaries
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-pull-request-summaries
  - /copilot/responsible-use-of-github-copilot-features/pull-request-summaries
  - /copilot/responsible-use/pull-request-summaries
  - /copilot/responsible-use-of-github-copilot-features/copilot-commit-message-generation
  - /copilot/responsible-use/copilot-commit-message-generation
  - /copilot/responsible-use-of-github-copilot-features/responsible-use-of-github-copilot-in-github-desktop
  - /copilot/responsible-use-of-github-copilot-features/copilot-in-github-desktop
  - /copilot/responsible-use/copilot-in-github-desktop
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

GitHub Copilot 聊天是一个聊天界面，可让您与 GitHub Copilot 互动，询问并接收与编码相关的问题的答案。 GitHub Copilot 聊天可在 GitHub.com、受支持的 IDE（VS Code、Visual Studio、JetBrains 和 Eclipse）、GitHub Mobile 和 Windows 终端中使用。在 GitHub.com 和 GitHub 桌面上，Copilot 还可以生成拉取请求摘要和提交消息 - AI- 支持拉取请求或提交中所做更改的概述。

GitHub Copilot Chat 可以回答各种与编码相关的问题，主题包括语法、编程概念、测试用例、调试等。 GitHub Copilot 聊天并非旨在回答非编码问题或提供有关编码之外主题的一般信息。

GitHub Copilot 聊天支持的主要语言是英语。

## 2。关键术语

以下列表提供了与 GitHub Copilot 聊天相关的关键术语词汇表：

* **内容过滤**：一种安全系统，可扫描提示和响应，以在有害内容显示给用户之前检测和阻止有害内容。
* **幻觉**：语言模型生成的输出听起来似乎合理但实际上不正确、不受所提供的上下文支持或完全捏造的现象。幻觉是大型语言模型的一种已知风险，也是人类对 AI- 生成的输出进行审核非常重要的原因。
* **大型语言模型 (LLM)**：一种在大量文本数据上训练的神经网络，可以生成、分析和转换自然语言和代码。 GitHub Copilot Chat 使用一个或多个 LLM 来处理提示并生成响应。
* **提示**：提供给GitHub Copilot聊天的输入。系统将提示与其他上下文（例如，打开的文件或存储库数据）结合起来，然后将其发送到语言模型。
* **公共代码匹配**：检查Copilot的建议是否与公开可用代码匹配的功能。根据您的设置，匹配建议可能会关闭或打开；如果打开，它可以阻止或注释源存储库和任何许可证信息的引用。
* **拉取请求摘要**：AI-生成的拉取请求中更改的概述，由散文段落和链接到受影响文件的关键更改的项目符号列表组成。摘要是在 GitHub.com 上按需生成的。
* **红队**：一种结构化测试实践，测试人员故意尝试从AI系统中引发不安全、有害或意外的行为。红队有助于在发布前后识别漏洞并改进安全缓解措施。
* **训练数据**：用于训练GitHub Copilot聊天背后的基础模型的大量公开文本和代码。训练数据的组成会影响不同编程语言、框架和主题的模型建议的质量和覆盖范围。

## 3。主要特性或功能

此处概述的主要特性和功能描述了 GitHub Copilot Chat 的设计用途以及它如何在支持的任务中执行。

* **对话式编码帮助**：GitHub Copilot聊天提供了一个自然语言界面，用于提出与编码相关的问题并以代码、解释或分步指导的形式接收答案。用户可以提出后续问题来完善响应，并且对话历史记录会在会话中维护。
* **上下文感知响应**：GitHub Copilot聊天使用上下文信息（例如打开的文件、活动存储库、聊天历史记录和（启用时）Web 搜索结果）来生成与用户当前工作相关的响应。可用的具体上下文取决于平台（GitHub.com、IDE、移动设备、Windows 终端或 GitHub 桌面）。
* **多平台可用性**：GitHub Copilot可在多个界面上进行聊天，包括GitHub.com、支持的IDE（VS Code、Visual Studio、JetBrains和Eclipse）、GitHub移动、 Windows 终端和 GitHub 桌面。每个平台都提供针对其环境进行优化的定制体验。
* **Agent 模式**：在支持的 IDE 中，GitHub Copilot Chat 可以在 agent 模式下运行，其中模型自主规划多步骤任务、调用工具（例如运行终端命令或编辑文件）并迭代结果。这将聊天体验扩展到单轮问答交互之外。
* **Copilot 空间**：空间允许用户将存储库、文件、问题和其他材料组织到精选的上下文集合中。当在空间内提出问题时，Copilot 使用包含的上下文来生成更有针对性和相关的响应。还可以通过远程 GitHub MCP 服务器从 IDE 访问空间。
* **拉取请求摘要**：在GitHub.com上，Copilot可以生成拉取请求中更改的AI-强力摘要。输出包含散文概述，后面是链接到受影响文件的关键更改的项目符号列表。摘要是根据需要生成的，旨在帮助审阅者快速了解更改的内容。
* **提交消息生成**：在GitHub.com和GitHub桌面中，Copilot可以根据您选择的代码更改生成建议的提交摘要（标题）和描述。在 GitHub 桌面中，用户可以选择特定的代码行或文件以提高上下文和准确性。生成的消息可以在提交之前进行审查和编辑。
* **自带密钥 (BYOK)**：组织可以通过提供自己的 API 密钥，而不是使用默认的 GitHub 托管模型，将 GitHub Copilot Chat 连接到受支持的第三方提供商的大型语言模型。
* **内容过滤**：有时，GitHub Copilot聊天可能包含内容过滤系统，该系统扫描提示和响应以检测和阻止有害内容。公共代码匹配功能会检查建议是否与公开可用的代码匹配，并根据设置阻止或注释那些与源和许可证信息匹配的内容。

## 4。预期用途

GitHub Copilot 聊天可用于多种行业的多种场景。用例的一些示例包括：

* **回答编码问题**：向GitHub Copilot询问特定编码问题的帮助或澄清，并以自然语言或代码片段格式接收回复。响应可以利用模型的训练数据、存储库上下文和网络搜索结果（启用时）。
* **解释代码并提出改进建议**：生成函数用途、输入、输出和依赖项的自然语言描述。 GitHub Copilot 聊天还可以提出改进建议，例如更好的错误处理或更可读的控制流。请注意，生成的解释可能并不总是准确或完整，应进行审查。
* **生成单元测试用例**：GitHub Copilot聊天可以通过基于编辑器中打开的代码或突出显示的代码片段生成代码片段来帮助编写单元测试用例。它可以根据函数的签名和主体建议可能的输入参数、预期输出值和断言。 GitHub Copilot Chat 还可以针对可能难以手动识别的边缘情况和边界条件（例如错误处理、空值或意外输入类型）建议测试用例。生成的测试用例可能无法涵盖所有​​可能的场景；手动测试和代码审查仍然是必要的。
* **提出代码修复建议**：根据错误消息、代码语法和周围上下文获取错误修复建议。 GitHub Copilot 聊天可以建议对变量、控制结构或函数调用进行更改，以解决问题。请注意，建议的修复可能并不总是最佳或完整的。
* **规划编码任务**：阅读并总结GitHub问题，回答有关问题，或提出后续步骤。
* **了解发布、讨论和提交**：总结发布中的更改、讨论的要点或特定提交中的更改。
* **总结拉取请求更改 (GitHub.com)**：生成拉取请求中更改的 AI- 支持的摘要（包括散文概述和链接到受影响文件的关键更改的项目符号列表），以帮助审阅者快速了解更改的内容以及审查的重点。
* **生成提交消息（GitHub.com 和 GitHub 桌面）**：根据您选择提交的代码更改生成提交消息摘要（标题）和描述，帮助您节省时间并维护清晰的提交历史记录。在 GitHub 桌面中，您可以选择特定的代码行或文件以更好地理解上下文，从而提高准确性。您可以在提交之前查看并编辑建议的标题和描述。
* **查找正确的命令（Windows 终端）**：要求 Copilot 建议可帮助您在命令行中执行任务的命令。您可以修改您的问题，直到返回的命令满足您的期望，然后将其插入到命令行中运行它。
* **解释不熟悉的命令（Windows 终端）**：要求Copilot生成命令功能的自然语言描述，包括其输入和输出参数以及使用示例。请注意，生成的解释可能并不总是准确或完整。
* **开发新功能（空间）**：将相关代码、产品规格和设计说明捆绑在一个空间中，以便 Copilot 可以解释当前的实现、突出显示差距并起草新代码或后续步骤。
* **定义重复任务的逻辑（空格）**：使用流程图、示例或架构记录一次流程，并在整个团队中重复使用它，以获得一致的模式和可重复使用的模板。
* **与队友分享知识（空间）**：将最新的代码和文档收集在一处，以便Copilot可以解释系统、回答问题和加入队友。

## 5。模型和训练数据

GitHub Copilot 聊天利用各种 AI 模型来增强用户所看到的体验。有关 Copilot 可用型号的比较，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>)。有关受支持型号的完整列表，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>)。有关模型托管位置的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>)。要详细了解用于训练 GitHub Copilot 聊天背后的基础模型的数据，请参阅上面链接的 AI 模型比较和 [GitHub Copilot 训练过哪些数据？](<https://github.com/features/copilot#faq>) 在 GitHub Copilot FAQ 中。

### 使用自带密钥 (BYOK)

当您将自带密钥与 GitHub Copilot 聊天结合使用时，您可以将聊天体验连接到除默认 Copilot 模型之外的受支持提供商提供的大型语言模型。支持的提供程序示例包括 Anthropic、AWS Bedrock、Google AI Studio、Microsoft Foundry、OpenAI、OpenAI 兼容提供程序以及xAI。您可以直接在 Copilot 设置中添加所选提供商的 API 密钥。

当BYOK激活时：

* **功能范围**：您选择的模型在GitHub Copilot聊天中使用。在 Agent 模式下，BYOK 为主要对话提供支持，但某些操作（例如代码应用程序或其他工具调用）可能仍然使用针对这些任务优化的 Copilot 集成模型。这些内置模型不通过您的 BYOK 提供商运行。
* **内容过滤**：无论哪个提供商处于活动状态，响应仍会通过GitHub的API，并且可能会在向您显示结果之前进行内容过滤。
* **质量注意事项**：建议可能会有所不同，具体取决于您选择的提供商的优势和培训范围。
* **数据处理**：使用BYOK时，您的提示和响应将传输到您选择的提供商，并可能受到该提供商的数据保留和隐私政策的约束。
* **您的责任**：您负责以下事项：
  * 提供商 API 密钥安全
  * 使用成本或配额
  * 输出验证
  * 评估您选择的型号是否满足您的安全和质量要求
  * 遵守您选择的提供商的条款
  * 确定您选择的模型是否符合适用法律
  * 确保人们在使用任何输出做出影响人们的决策之前对其进行审查
* **出口限制**：某些AI型号可能受到出口管制。验证您选择的提供商和型号是否已获授权在您所在的司法管辖区使用。

BYOK 使您的组织能够选择最适合您需求的语言模型。请注意，模型性能和安全特性取决于提供商。

## 6。性能

GitHub Copilot 聊天结合使用自然语言处理和机器学习来理解您的问题并为您提供答案。这个过程涉及：

1. **输入处理**：用户的提示被系统预处理，结合上下文信息（例如，当前存储库、打开的文件或聊天记录），并发送到大语言模型。用户输入可以采用代码片段或简单语言的形式。
1. **语言模型分析**：提示是通过语言模型传递的，语言模型是在大量文本数据上训练的神经网络。语言模型分析输入提示。
1. **响应生成**：模型根据对输入提示和提供给它的上下文的分析生成响应。此响应可以采用生成代码、代码建议或现有代码解释的形式。
1. **输出格式**：使用语法突出显示、缩进和其他功能来格式化响应以增加清晰度。根据问题的类型，还可以提供模型所使用的上下文的链接（例如源代码文件、问题或文档）。

### 经验差异

* **在 GitHub.com** 上：该模型可以从存储在 GitHub 上的存储库数据和 Web 搜索结果（当管理员启用时）收集其他上下文。响应可能包括源代码文件、问题或文档的链接。
* **拉取请求摘要 (GitHub.com)**：当用户请求摘要时，工作流使用文件中的代码差异来构建提示，并请求 Copilot 生成总体摘要。输出由一个散文段落组成，概述了更改，后面是链接到相应代码行的关键更改的项目符号列表。您可以在新的或现有拉取请求的描述中或在拉取请求时间线的评论中生成摘要。较大的拉取请求可能需要几分钟的时间来处理。拉取请求摘要唯一支持的语言是英语。
* **提交消息生成（GitHub.com）**：当您单击GitHub.com上的**提交更改**按钮时，Copilot根据所选文件中的代码更改。文本将插入摘要和描述字段中，供您在提交之前查看和编辑。提交消息生成唯一支持的语言是英语。
* **提交消息生成（GitHub桌面）**：当您单击GitHub桌面中的Copilot按钮时，Copilot会根据您选择的代码更改生成建议的摘要（标题）和说明。您可以选择特定的代码行或文件来提高上下文和准确性，并且可以在最终确定之前重新生成建议。唯一支持的语言是英语。
* **在您的IDE**中：系统将用户输入与上下文信息（例如存储库名称和用户打开的文件）结合起来。在 VS Code 和 Visual Studio 中，可以从可选的 `.github/copilot-instructions.md` 文件自动提供附加上下文（用户可以在设置中禁用此功能）。在 VS Code 或 Visual Studio 中使用 `@github` 聊天参与者时，GitHub Copilot 聊天还可以从 GitHub 上存储的代码和 Bing 搜索结果（如果管理员启用）收集上下文。
* **在GitHub移动**上：输入提示被预处理并发送到语言模型。该系统仅用于回答与编码相关的问题。可用选项因 Copilot 计划而异：拥有 Copilot Enterprise 订阅的用户可以使用私有索引存储库中的数据进行对话，并且如果启用了 Web 搜索集成，则可以接收由 Web 搜索结果通知的响应。订阅了 Copilot Pro 的用户可以讨论最热门的公共存储库。
* **在 Windows 终端中**：用户的提示与上下文信息（活动 shell 的名称和聊天历史记录）相结合并发送到语言模型。响应采用建议命令或命令解释的形式。建议的命令不会自动运行 - 您必须单击命令将其插入命令行中，然后手动运行它。
* **在Copilot空间中**：空间可让您组织GitHub Copilot聊天用来回答您的问题的上下文。空间可以包含存储库、代码、拉取请求、问题、自由文本内容（如文字记录或注释）、图像和文件上传。当您在空间中提交问题时，Copilot 会使用该空间中的相关上下文来增强您的请求。还可以通过远程 GitHub MCP 服务器从 IDE 访问空间。并非空间中的所有内容都会在每个响应中使用 - Copilot 处理所包含内容的一部分，因此有选择性地添加内容有助于确保相关性。

GitHub Copilot 聊天旨在为您提供与您的问题最相关的答案。然而，它可能并不总能提供您正在寻找的答案。 GitHub Copilot 聊天的用户有责任审查和验证系统生成的响应，以确保其准确且适当。

## 7。局限性

了解 GitHub Copilot 聊天的局限性对于确定它是否在安全有效的范围内使用至关重要。虽然我们鼓励客户在其创新解决方案或应用程序中利用 GitHub Copilot Chat，但需要注意的是，GitHub Copilot Chat 并非针对所有可能的场景而设计。我们鼓励用户在选择用例时参考[AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms>)以及以下注意事项：

* **范围有限**：GitHub Copilot Chat 已接受过大量代码训练，但可能无法处理更复杂的代码结构或晦涩的编程语言。对于每种语言，建议的质量取决于训练数据的数量和多样性。例如，JavaScript 具有良好的代表性和良好的支持，而不太常见的语言可能会产生较低质量的结果。
* **潜在偏差**：从现有代码存储库中提取的训练数据可能包含该工具可能永久存在的偏差和错误。 GitHub Copilot 聊天也可能偏向于某些编程语言或编码风格，这可能导致代码建议不理想或不完整。
* **安全风险**：如果不仔细检查，生成的代码可能会暴露敏感信息或漏洞。始终彻底检查和测试生成的代码，尤其是对于安全敏感的应用程序。
* **与公共代码匹配**：虽然概率较低，但GitHub Copilot聊天可能会生成与训练集中的代码匹配的代码。您应该采取与处理任何使用非您独立创作的材料的代码相同的预防措施，包括严格测试、IP 扫描和检查安全漏洞。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>)。
* **不准确的代码**：GitHub Copilot聊天可能会生成看似有效但在语义或语法上不正确的代码，或者不能准确反映开发人员的意图。仔细检查和测试生成的代码，特别是对于关键或敏感应用程序。
* **对非编码主题的不准确响应**：GitHub Copilot聊天并非旨在回答非编码问题，并且其在这些上下文中的响应可能是不相关或无意义的。
* **破坏性命令的风险（Windows 终端）**：Copilot 可能会建议可能具有破坏性的命令（例如删除内容或格式化硬盘），这些命令在某些情况下可能是必要的，但如果使用不当可能会导致问题。您最终对您选择执行的任何命令负责。尽管存在故障保护和安全机制，但执行命令仍存在固有风险。
* **行更改了 PR 摘要的限制 (GitHub.com)**：添加和删除次数超过 400 的文件将从摘要中排除。
* **PR 摘要不会自动更新 (GitHub.com)**：仅当用户手动请求时才会创建拉取请求摘要。当对拉取请求进行更新或更改时，摘要不会自动刷新。用户可以请求新的摘要，但应仔细检查更新的输出 - 它与原始输出具有相同的不准确风险。
* **复制PR内容(GitHub.com)**：由于摘要概述了拉取请求中的更改，因此如果拉取请求内容中出现有害或冒犯性术语，则摘要有可能复制这些术语。用户应该期望其 PR 中使用的术语出现在 AI- 生成的摘要中。
* **提交消息的范围有限（GitHub.com 和 GitHub 桌面）**：提交消息生成可能会遇到复杂的代码更改、较短的差异窗口或最近开发的编程语言。建议的质量取决于所涉及语言的培训数据的可用性和多样性。
* **复制提交内容（GitHub.com 和 GitHub 桌面）**：由于提交消息总结了提交中的更改，因此如果代码更改中出现有害或冒犯性术语，则生成的消息有可能包含这些术语。
* **提交消息不会自动更新（GitHub.com 和 GitHub 桌面版）**：提交消息仅在您请求时生成。当您进行其他更改时，提交消息不会自动刷新。您可以重新生成新建议，但应仔细检查更新后的输出 - 它与原始建议具有相同的不准确风险。
* **用户意图的解释（空格）**：空格有助于在策划的上下文中建立GitHub Copilot聊天的响应，但系统仍然可能会误解您的意图。始终检查输出以确认它反映了您的目标。
* **上下文限制（空间）**：空间已定义大小限制，并且 GitHub Copilot 聊天仅处理您包含的部分内容。并非空间中的每个文件、文档或注释都会用于响应。有选择性地添加内容有助于确保使用最相关的上下文。
* **基于自然语言的不同性能**：GitHub Copilot聊天主要针对英语进行了优化。您可能会注意到其他语言的提示的性能有所不同。
* **利用网络搜索**：在 GitHub.com 上，当在受支持的 IDE 中使用 GitHub 聊天参与者时，GitHub Copilot Chat 可以选择使用 Bing 搜索来帮助回答您的问题，例如，有关最近事件、新技术或高度具体主题的查询。您的企业管理员可以为您的组织启用或禁用 Bing。使用网络搜索时，提示的内容以及其他上下文将用于代表您生成 Bing 搜索查询。发送到 Bing 的搜索查询受 [Microsoft 的隐私声明 ](<https://privacy.microsoft.com/en-us/privacystatement>) 管辖。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>)。

## 8。评价

{% data reusables.rai.copilot.application-card-evaluations %}

### 性能和质量评估

GitHub Copilot 聊天 AI 功能是使用行业标准基准（例如 SWE-Bench）和内部开发的评估套件相结合进行评估的。基准任务来自公共开源存储库和合成场景；不使用真实的用户查询或客户代码。每次评估都包括多次独立运行，以解释模型输出中的不确定性。关键指标包括解决率（成功完成任务的百分比）、令牌效率、延迟和工具调用可靠性。当进行更新时，模型会被重新评估，并通过错误率、响应延迟和聚合使用模式在生产中持续监控。

### 性能和质量评价方法

GitHub Copilot Chat 的新模型在部署之前会经过分阶段评估过程。集成商团队运行特定于其表面的基准测试套件，在代表性编码任务（例如错误修复、代码生成和多文件重构）上测试模型。根据既定基线和现有生产模型对结果进行审查。在进入下一阶段之前，模型必须在解决率、代币效率和延迟等关键指标上达到或超过基准性能。在任何模型被批准用于面向用户的部署之前，跨职能审查委员会会做出正式的 go/no-go 决定。

### 风险和安全评估

{% data reusables.rai.copilot.application-card-risk-and-safety-evaluations %}

### 质量安全评价数据

{% data reusables.rai.copilot.application-card-evaluation-data-for-quality-and-safety %}

### 定制评估

作为我们产品开发流程的一部分，我们进行红队合作以了解和提高 GitHub Copilot 聊天的安全性。启用后，输入提示和输出完成将通过内容过滤器运行。

## 9。安全组件和缓解措施

* **内容过滤**：启用后，内容过滤系统会检测并阻止有害内容的输出。如果您遇到攻击性内容，请向`copilot-safety@github.com`举报。
* **公共代码匹配**：根据您的设置，GitHub Copilot聊天可能会打开或关闭重复检测过滤器。如果打开，GitHub Copilot Chat 会阻止与公共代码匹配的建议，或者使用指向源存储库和任何许可证详细信息的链接对它们进行注释。在 VS Code 中，如果您启用了与公共代码匹配的建议，GitHub Copilot Chat 会显示一条消息，其中包含一个链接，用于在编辑器中显示匹配的代码和任何许可证详细信息。在 Visual Studio、JetBrains、Eclipse 和 GitHub Mobile 中，GitHub Copilot Chat 使用过滤器来阻止与公共代码的匹配。无论设置如何，您都应该采取与使用非您独立创作的材料的任何代码相同的预防措施，包括严格测试、IP 扫描和检查安全漏洞。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>)。

## 10。部署和采用 GitHub Copilot 聊天的最佳实践

负责任的AI是GitHub与其客户之间的共同承诺。虽然 GitHub 构建的 AI 应用程序以安全、公平和透明为核心，但客户在自己的环境中负责任地部署和使用这些技术方面发挥着关键作用。为了支持这种合作伙伴关系，我们为部署人员和最终用户提供以下最佳实践，以帮助客户有效地实施负责任的 AI。

* **使用 GitHub Copilot 聊天进行后续决策或在敏感领域中时请谨慎行事并评估结果**：{% data reusables.rai.copilot.application-card-consequential-decisions %}
* **评估法律和监管考虑因素**：{% data reusables.rai.copilot.application-card-evaluate-legal-regulatory %}

* **在适当的时候进行人工监督**：在与AI应用程序交互时，人工监督是一个重要的保障。虽然我们不断改进我们的 AI 应用程序，AI 仍然可能会出错。生成的输出可能不准确、不完整、有偏见、不一致或与您的预期目标无关。发生这种情况的原因有多种，例如输入的模糊性或基础模型的限制。因此，用户应查看 GitHub Copilot 聊天生成的响应并验证它们是否符合他们的期望和要求。
* **注意过度依赖的风险**：{% data reusables.rai.copilot.application-card-overreliance %}
* **在敏感域中设计代理AI时要小心谨慎**：{% data reusables.rai.copilot.application-card-agentic-ai-caution %}
* **使用GitHub Copilot聊天作为工具，而不是替代品**：虽然GitHub Copilot聊天可以是生成代码的强大工具，但重要的是将其用作人类编程的工具而不是替代品。请始终检查和测试 GitHub Copilot Chat 生成的代码，以确保其满足您的要求并且没有错误或安全问题。
* **保留主题提示**：GitHub Copilot聊天旨在解决与编码相关的查询。将提示限制为编码问题或任务可以提高模型的输出质量。
* **使用安全编码和代码审查实践**：遵循安全编码的最佳实践（例如避免硬编码密码或SQL注入漏洞）并审查所有生成的代码。
* **对上下文（空间）进行选择性**：仅将最相关的文件、存储库和注释添加到空间有助于Copilot保持专注。空间中过多的不必要的内容会降低响应质量。
* **保持上下文更新（空间）**：随着项目的发展，刷新空间中的文件、问题或文档。过时的上下文可能会导致 Copilot 生成不准确或不完整的答案。
* **在来源（空格）旁边使用说明**：将自然语言说明与精选来源相结合有助于Copilot更好地理解您的意图。
* **在空间中锚定聊天 (Spaces)**：从空间内开始对话可确保连续性和相关性，使响应与您设置的上下文保持一致。
* **发布前查看 PR 摘要 (GitHub.com)**：拉取请求摘要旨在补充（而不是取代）您自己的有关更改的上下文。在保存或发布拉取请求之前，请务必检查并评估生成的摘要的准确性。
* **提交前查看提交消息（GitHub.com 和 GitHub 桌面）**：提交消息生成旨在补充（而不是取代）您自己的更改描述。在提交之前，请务必查看并编辑建议的标题和描述。在GitHub桌面中，选择特定的代码行或文件可以提高准确性。您可以通过 GitHub.com 上的 Copilot [ 设置页面 ](<https://github.com/settings/copilot/features>) 选择退出提交消息生成。
* **提供反馈**：如果您遇到任何问题或限制，请通过每个聊天响应下方的拇指按钮up/down提供反馈。提供有关拉取请求摘要的反馈的能力取决于您的企业设置。欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>)。对于提交消息生成，您可以通过 [ 社区讨论 ](<https://github.com/orgs/community/discussions/categories/copilot-news-and-announcements>) 或 [ 在 GitHub 桌面存储库 ](<https://github.com/desktop/desktop/issues>) 中打开问题来分享反馈。这有助于改进该工具并解决问题。
* **保持最新状态**：GitHub Copilot聊天正在不断发展。及时了解更新、新的安全风险和最佳实践。

## 11。了解更多有关 GitHub Copilot 聊天的信息

有关负责任地使用 GitHub Copilot 聊天的更多指导，我们建议查看以下文档：

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/create-a-pr-summary>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [终端聊天](<https://learn.microsoft.com/windows/terminal/terminal-chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot 信任中心](<https://copilot.github.trust.page/>)

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

GitHub Copilot Chat is a chat interface that lets you interact with GitHub Copilot to ask and receive answers to coding-related questions. GitHub Copilot Chat is available on GitHub.com, in supported IDEs (VS Code, Visual Studio, JetBrains, and Eclipse), on GitHub Mobile, and in Windows Terminal. On GitHub.com and in GitHub Desktop, Copilot can also generate pull request summaries and commit messages—AI-powered overviews of changes made in a pull request or commit.

GitHub Copilot Chat can answer a wide range of coding-related questions on topics including syntax, programming concepts, test cases, debugging, and more. GitHub Copilot Chat is not designed to answer non-coding questions or provide general information on topics outside of coding.

The primary supported language for GitHub Copilot Chat is English.

## 2. Key terms

The following list provides a glossary of key terms related to GitHub Copilot Chat:

* **Content filtering**: A safety system that scans prompts and responses to detect and block harmful content before it is shown to the user.
* **Hallucination**: A phenomenon where a language model generates output that sounds plausible but is factually incorrect, unsupported by the provided context, or entirely fabricated. Hallucinations are a known risk of large language models and are reason that human review of AI-generated output is important.
* **Large language model (LLM)**: A type of neural network trained on a large body of text data that can generate, analyze, and transform natural language and code. GitHub Copilot Chat uses one or more LLMs to process prompts and produce responses.
* **Prompt**: The input that is provided to GitHub Copilot Chat. The system combines the prompt with additional context (for example, open files or repository data) before sending it to the language model.
* **Public code matching**: A feature that checks whether Copilot's suggestions match publicly available code. Depending on your settings, matching suggestions may be turned off or on; if turned on, it can either block or annotate with a reference to the source repository and any license information.
* **Pull request summary**: An AI-generated overview of the changes in a pull request, consisting of a prose paragraph and a bulleted list of key changes linked to the affected files. Summaries are generated on demand on GitHub.com.
* **Red teaming**: A structured testing practice in which testers deliberately attempt to provoke unsafe, harmful, or unintended behavior from an AI system. Red teaming helps identify vulnerabilities and improve safety mitigations before and after release.
* **Training data**: The large body of publicly available text and code that was used to train the foundation models behind GitHub Copilot Chat. The composition of the training data influences the quality and coverage of the model's suggestions across different programming languages, frameworks, and topics.

## 3. Key features or capabilities

The key features and capabilities outlined here describe what GitHub Copilot Chat is designed to do and how it performs across supported tasks.

* **Conversational coding assistance**: GitHub Copilot Chat provides a natural language interface for asking coding-related questions and receiving answers in the form of code, explanations, or step-by-step guidance. Users can ask follow-up questions to refine responses, and the conversation history is maintained within a session.
* **Context-aware responses**: GitHub Copilot Chat uses contextual information—such as open files, the active repository, chat history, and (when enabled) web search results—to generate responses that are relevant to the user's current work. The specific context available depends on the platform (GitHub.com, IDE, Mobile, Windows Terminal, or GitHub Desktop).
* **Multi-platform availability**: GitHub Copilot Chat is available across multiple surfaces, including GitHub.com, supported IDEs (VS Code, Visual Studio, JetBrains, and Eclipse), GitHub Mobile, Windows Terminal, and GitHub Desktop. Each platform offers a tailored experience optimized for its environment.
* **Agent mode**: In supported IDEs, GitHub Copilot Chat can operate in agent mode, where the model autonomously plans multi-step tasks, invokes tools (such as running terminal commands or editing files), and iterates on results. This extends the chat experience beyond single-turn question-and-answer interactions.
* **Copilot Spaces**: Spaces let users organize repositories, files, issues, and other materials into a curated collection of context. When a question is asked inside a space, Copilot uses the included context to produce more targeted and relevant responses. Spaces can also be accessed from the IDE via the remote GitHub MCP server.
* **Pull request summaries**: On GitHub.com, Copilot can generate an AI-powered summary of the changes in a pull request. The output consists of a prose overview followed by a bulleted list of key changes linked to the affected files. Summaries are generated on demand and are intended to help reviewers quickly understand what changed.
* **Commit message generation**: On GitHub.com and in GitHub Desktop, Copilot can generate a suggested commit summary (title) and description based on the code changes you have selected. In GitHub Desktop, users can select specific lines of code or files to improve context and accuracy. Generated messages can be reviewed and edited before committing.
* **Bring Your Own Key (BYOK)**: Organizations can connect GitHub Copilot Chat to large language models from supported third-party providers by supplying their own API key, rather than using the default GitHub-hosted model.
* **Content filtering**: At times, GitHub Copilot Chat may include a content filtering system that scans prompts and responses to detect and block harmful content. A public code matching feature checks whether suggestions match publicly available code and, depending on settings, blocks or annotates those matches with source and license information.

## 4. Intended uses

GitHub Copilot Chat can be used in multiple scenarios across a variety of industries. Some examples of use cases include:

* **Answering coding questions**: Ask GitHub Copilot Chat for help or clarification on specific coding problems and receive responses in natural language or code snippet format. The response may draw on the model's training data, repository context, and web search results (when enabled).
* **Explaining code and suggesting improvements**: Generate natural language descriptions of a function's purpose, inputs, outputs, and dependencies. GitHub Copilot Chat can also suggest improvements such as better error handling or more readable control flow. Note that generated explanations may not always be accurate or complete and should be reviewed.
* **Generating unit test cases**: GitHub Copilot Chat can help write unit test cases by generating code snippets based on the code open in the editor or a highlighted code snippet. It can suggest possible input parameters, expected output values, and assertions based on the function's signature and body. GitHub Copilot Chat can also suggest test cases for edge cases and boundary conditions—such as error handling, null values, or unexpected input types—that might be difficult to identify manually. Generated test cases may not cover all possible scenarios; manual testing and code review are still necessary.
* **Proposing code fixes**: Get suggested fixes for bugs based on the error message, code syntax, and surrounding context. GitHub Copilot Chat can suggest changes to variables, control structures, or function calls that might resolve the issue. Note that suggested fixes may not always be optimal or complete.
* **Planning coding tasks**: Read and summarize GitHub issues, answer questions about them, or propose next steps.
* **Learning about releases, discussions, and commits**: Summarize what changed in a release, the gist of a discussion, or the changes in a specific commit.
* **Summarizing pull request changes (GitHub.com)**: Generate an AI-powered summary of the changes in a pull request—including a prose overview and a bulleted list of key changes linked to the affected files—to help reviewers quickly understand what changed and where to focus their review.
* **Generating commit messages (GitHub.com and GitHub Desktop)**: Generate a commit message summary (title) and description based on the code changes you've selected to commit, helping you save time and maintain clear commit histories. In GitHub Desktop, you can select specific lines of code or files for better context understanding to increase accuracy. You can review and edit the suggested title and description before committing.
* **Finding the right command (Windows Terminal)**: Ask Copilot to suggest commands that help you perform tasks in the command line. You can revise your question until the returned command meets your expectations, then insert it into your command line to run it.
* **Explaining an unfamiliar command (Windows Terminal)**: Ask Copilot to generate a natural language description of a command's functionality, including its input and output parameters and usage examples. Note that generated explanations may not always be accurate or complete.
* **Developing a new feature (Spaces)**: Bundle relevant code, product specs, and design notes in a space so Copilot can explain the current implementation, highlight gaps, and draft new code or next steps.
* **Defining logic for repeated tasks (Spaces)**: Document a process once—with flowcharts, examples, or schemas—and reuse it across your team for consistent patterns and reusable templates.
* **Sharing knowledge with teammates (Spaces)**: Collect the latest code and documentation in one place so Copilot can explain systems, answer questions, and onboard teammates.

## 5. Models and training data

GitHub Copilot Chat leverages a variety of AI models to power the experience that users see. For a comparison of the models available for Copilot, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-comparison>). For the full list of supported models, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/supported-models>). For information on where models are hosted, see [AUTOTITLE](<https://github.com/copilot/reference/ai-models/model-hosting>). To learn more about the data used to train the foundation models behind GitHub Copilot Chat, refer to the linked AI model comparison above and [What data has GitHub Copilot been trained on?](<https://github.com/features/copilot#faq>) in the GitHub Copilot FAQ.

### Using Bring Your Own Key (BYOK)

When you use Bring Your Own Key with GitHub Copilot Chat, you can connect the chat experience to large language models from supported providers beyond the default Copilot model. Examples of supported providers include Anthropic, AWS Bedrock, Google AI Studio, Microsoft Foundry, OpenAI, OpenAI-compatible providers, and xAI. You add your API key for the chosen provider directly in your Copilot settings.

When BYOK is active:

* **Feature scope**: Your chosen model is used within GitHub Copilot Chat. In Agent mode, BYOK powers the main conversation, but certain actions such as code application or other tool calls may still use Copilot-integrated models optimized for those tasks. These built-in models do not run through your BYOK provider.
* **Content filtering**: Regardless of which provider is active, responses still pass through GitHub's API and may have content filtering before results are shown to you.
* **Quality considerations**: Suggestions may vary depending on the strengths and training coverage of your chosen provider.
* **Data handling**: When using BYOK, your prompts and responses are transmitted to your selected provider and may be subject to that provider's data retention and privacy policies.
* **Your responsibilities**: You are responsible for the following:
  * Provider API key security
  * Usage costs or quotas
  * Output validation
  * Evaluating whether your chosen model meets your safety and quality requirements
  * Compliance with your selected provider's terms
  * Determining whether your chosen model complies with applicable laws
  * Ensuring that a human reviews any output before using it to make decisions that affect people
* **Export restrictions**: Certain AI models may be subject to export controls. Verify your selected provider and model are authorized for use in your jurisdiction.

BYOK empowers your organization to choose the language model that best fits your needs. Note that model performance and safety characteristics are provider-dependent.

## 6. Performance

GitHub Copilot Chat works by using a combination of natural language processing and machine learning to understand your question and provide you with an answer. This process involves:

1. **Input processing**: The user's prompt is pre-processed by the system, combined with contextual information (for example, the current repository, open files, or chat history), and sent to a large language model. User input can take the form of code snippets or plain language.
1. **Language model analysis**: The prompt is passed through the language model, which is a neural network trained on a large body of text data. The language model analyzes the input prompt.
1. **Response generation**: The model generates a response based on its analysis of the input prompt and the context provided to it. This response can take the form of generated code, code suggestions, or explanations of existing code.
1. **Output formatting**: The response is formatted with syntax highlighting, indentation, and other features to add clarity. Depending on the type of question, links to context that the model used—such as source code files, issues, or documentation—may also be provided.

### Differences by experience

* **On GitHub.com**: The model can gather additional context from repository data stored on GitHub and web search results (when enabled by your administrator). Responses may include links to source code files, issues, or documentation.
* **Pull request summaries (GitHub.com)**: When a user requests a summary, a workflow uses the code diffs from files to build a prompt and requests Copilot to generate an overall summary. The output consists of a prose paragraph giving an overview of the changes, followed by a bulleted list of key changes linked to the respective lines of code. You can generate a summary in the description of a new or existing pull request, or in a comment on the pull request timeline. Larger pull requests may take up to a couple of minutes to process. The only supported language for pull request summaries is English.
* **Commit message generation (GitHub.com)**: When you click the **Commit changes** button on GitHub.com, Copilot generates a suggested summary (title) and description based on the code changes in the selected files. The text is inserted into the summary and description fields for you to review and edit before committing. The only supported language for commit message generation is English.
* **Commit message generation (GitHub Desktop)**: When you click the Copilot button in GitHub Desktop, Copilot generates a suggested summary (title) and description based on the code changes you've selected. You can select specific lines of code or files to improve context and accuracy, and you can regenerate suggestions before finalizing. The only supported language is English.
* **In your IDE**: The system combines user input with contextual information such as the name of the repository and the files the user has open. In VS Code and Visual Studio, additional context can be provided automatically from an optional `.github/copilot-instructions.md` file (the user can disable this in settings). When using the `@github` chat participant in VS Code or Visual Studio, GitHub Copilot Chat can also gather context from code stored on GitHub and from Bing search results (if enabled by your administrator).
* **On GitHub Mobile**: The input prompt is pre-processed and sent to the language model. The system is only intended to respond to coding-related questions. The options available vary by Copilot plan: users with a Copilot Enterprise subscription can have conversations using data from private indexed repositories and, if the web search integration is enabled, receive responses informed by web search results. Users with a Copilot Pro subscription can discuss top popular public repositories.
* **In Windows Terminal**: The user's prompt is combined with contextual information (the name of the active shell and the chat history) and sent to the language model. Responses take the form of a suggested command or an explanation of a command. Suggested commands are not run automatically—you must click on a command to insert it into your command line and then manually run it.
* **In Copilot Spaces**: Spaces let you organize the context that GitHub Copilot Chat uses to answer your questions. A space can include repositories, code, pull requests, issues, free-text content like transcripts or notes, images, and file uploads. When you submit a question in a space, Copilot augments your request with relevant context from that space. Spaces can also be accessed from the IDE via the remote GitHub MCP server. Not all content in a space is used in every response—Copilot processes a portion of the included content, so being selective about what you add helps ensure relevance.

GitHub Copilot Chat is intended to provide you with the most relevant answer to your question. However, it may not always provide the answer you are looking for. Users of GitHub Copilot Chat are responsible for reviewing and validating responses generated by the system to ensure they are accurate and appropriate.

## 7. Limitations

Understanding GitHub Copilot Chat's limitations is crucial to determine if it is used within safe and effective boundaries. While we encourage customers to leverage GitHub Copilot Chat in their innovative solutions or applications, it's important to note that GitHub Copilot Chat was not designed for every possible scenario. We encourage users to refer to [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms>) as well as the following considerations when choosing a use case:

* **Limited scope**: GitHub Copilot Chat has been trained on a large body of code but may not be able to handle more complex code structures or obscure programming languages. For each language, the quality of suggestions depends on the volume and diversity of training data. For example, JavaScript is well-represented and well-supported, while less common languages may yield lower-quality results.
* **Potential biases**: Training data drawn from existing code repositories may contain biases and errors that can be perpetuated by the tool. GitHub Copilot Chat may also be biased towards certain programming languages or coding styles, which can lead to suboptimal or incomplete code suggestions.
* **Security risks**: Generated code can potentially expose sensitive information or vulnerabilities if not reviewed carefully. Always review and test generated code thoroughly, especially for security-sensitive applications.
* **Matches with public code**: While the probability is low, GitHub Copilot Chat may produce code that matches code in the training set. You should take the same precautions as you would with any code that uses material you did not independently originate including rigorous testing, IP scanning, and checking for security vulnerabilities. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>).
* **Inaccurate code**: GitHub Copilot Chat may generate code that appears valid but is not semantically or syntactically correct, or does not accurately reflect the intent of the developer. Carefully review and test generated code, particularly for critical or sensitive applications.
* **Inaccurate responses to non-coding topics**: GitHub Copilot Chat is not designed to answer non-coding questions, and its responses in those contexts may be irrelevant or nonsensical.
* **Risk of destructive commands (Windows Terminal)**: Copilot may suggest commands that could be destructive—such as deleting content or formatting a hard drive—that may be necessary in certain scenarios but can cause problems if used incorrectly. You are ultimately responsible for any commands you choose to execute. Despite the presence of fail-safes and safety mechanisms, executing commands carries inherent risks.
* **Lines changed limits for PR summaries (GitHub.com)**: Files with more than 400 combined additions and deletions are excluded from summarization.
* **PR summaries are not auto-updated (GitHub.com)**: Pull request summaries are only created when users request them manually. When updates or changes are made to the pull request, the summary is not automatically refreshed. Users can request a new summary, but should carefully review the updated output—it carries the same risks of inaccuracy as the original.
* **Replication of PR content (GitHub.com)**: Because a summary outlines the changes in a pull request, if harmful or offensive terms appear in the pull request content, there is potential for the summary to replicate those terms. Users should expect terms used in their PR to appear in the AI-generated summary.
* **Limited scope for commit messages (GitHub.com and GitHub Desktop)**: Commit message generation may struggle with intricate code changes, short diff windows, or recently developed programming languages. The quality of suggestions depends on the availability and diversity of training data for the languages involved.
* **Replication of commit content (GitHub.com and GitHub Desktop)**: Because a commit message summarizes the changes in a commit, if harmful or offensive terms appear in the code changes, there is potential for the generated message to include those terms.
* **Commit messages are not auto-updated (GitHub.com and GitHub Desktop)**: Commit messages are only generated when you request them. When you make additional changes, the commit message is not automatically refreshed. You can regenerate a new suggestion, but should carefully review the updated output—it carries the same risks of inaccuracy as the original.
* **Interpretation of user intent (Spaces)**: Spaces help ground GitHub Copilot Chat's responses in curated context, but the system may still misunderstand your intent. Always review output to confirm it reflects your goals.
* **Context limits (Spaces)**: Spaces have defined size limits, and GitHub Copilot Chat only processes a portion of the content you include. Not every file, document, or note in a space will be used in a response. Being selective about what you add helps ensure the most relevant context is used.
* **Differing performance based on natural language**: GitHub Copilot Chat has been optimized primarily for English. You may notice differing performance with prompts in other languages.
* **Leveraging web search**: On GitHub.com and when using the GitHub chat participant in supported IDEs, GitHub Copilot Chat can optionally use a Bing search to help answer your question—for example, queries about recent events, new technologies, or highly specific subjects. Your enterprise administrator can enable or disable Bing for your organization. When web search is used, the content of your prompt, along with additional context, is used to generate a Bing search query on your behalf. The search query sent to Bing is governed by [Microsoft's Privacy Statement](<https://privacy.microsoft.com/en-us/privacystatement>). For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>).

## 8. Evaluations

{% data reusables.rai.copilot.application-card-evaluations %}

### Performance and quality evaluations

GitHub Copilot Chat AI features are evaluated using a combination of industry-standard benchmarks (e.g., SWE-Bench) and internally developed evaluation suites. Benchmark tasks are sourced from public open-source repositories and synthetic scenarios; no real user queries or customer code are used. Each evaluation includes multiple independent runs to account for nondeterminism in model outputs. Key metrics include resolution rate (percentage of tasks successfully completed), token efficiency, latency, and tool call reliability. Models are re-evaluated when updates are made and monitored continuously in production via error rates, response latency, and aggregate usage patterns.

### Performance and quality evaluation methods

New models for GitHub Copilot Chat undergo a staged evaluation process before deployment. Integrator teams run benchmark suites specific to their surface, testing the model on representative coding tasks such as bug fixes, code generation, and multi-file refactoring. Results are reviewed against established baselines and existing production models. Models must meet or exceed baseline performance across key metrics like resolution rate, token efficiency, and latency, before advancing to the next stage. A cross-functional review board makes a formal go/no-go decision before any model is approved for user-facing deployment.

### Risk and safety evaluations

{% data reusables.rai.copilot.application-card-risk-and-safety-evaluations %}

### Evaluation data for quality and safety

{% data reusables.rai.copilot.application-card-evaluation-data-for-quality-and-safety %}

### Custom evaluations

As part of our product development process, we undertake red teaming to understand and improve the safety of GitHub Copilot Chat. When enabled, input prompts and output completions are run through content filters.

## 9. Safety components and mitigations

* **Content filtering**: When enabled, the content filtering system detects and prevents the output of harmful content. If you encounter offensive content, report it to `copilot-safety@github.com`.
* **Public code matching**: Depending on your settings, GitHub Copilot Chat may have the duplicate detection filter turned on or off. If on, GitHub Copilot Chat either blocks suggestions that match public code or annotates them with links to the source repositories and any license details. In VS Code, if you have enabled suggestions that match public code, GitHub Copilot Chat displays a message with a link to show the matched code and any license details in the editor. In Visual Studio, JetBrains, Eclipse, and GitHub Mobile, GitHub Copilot Chat uses filters that block matches with public code. Regardless of settings, you should take the same precautions as with any code that uses material you did not independently originate—including rigorous testing, IP scanning, and checking for security vulnerabilities. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/copilot-for-common-tasks/find-matching-code>).

## 10. Best practices for deploying and adopting GitHub Copilot Chat

Responsible AI is a shared commitment between GitHub and its customers. While GitHub builds AI applications with safety, fairness, and transparency at the core, customers play a critical role in deploying and using these technologies responsibly within their own contexts. To support this partnership, we offer the following best practices for deployers and end users to help customers implement responsible AI effectively.

* **Exercise caution and evaluate outcomes when using GitHub Copilot Chat for consequential decisions or in sensitive domains**: {% data reusables.rai.copilot.application-card-consequential-decisions %}
* **Evaluate legal and regulatory considerations**: {% data reusables.rai.copilot.application-card-evaluate-legal-regulatory %}

* **Exercise human oversight when appropriate**: Human oversight is an important safeguard when interacting with AI applications. While we continuously improve our AI applications, AI might still make mistakes. The outputs generated may be inaccurate, incomplete, biased, misaligned, or irrelevant to your intended goals. This could happen due to various reasons, such as ambiguity in the inputs or limitations of the underlying models. As such, users should review the responses generated by GitHub Copilot Chat and verify that they match their expectations and requirements.
* **Be aware of the risk of overreliance**: {% data reusables.rai.copilot.application-card-overreliance %}
* **Exercise caution when designing agentic AI in sensitive domains**: {% data reusables.rai.copilot.application-card-agentic-ai-caution %}
* **Use GitHub Copilot Chat as a tool, not a replacement**: While GitHub Copilot Chat can be a powerful tool for generating code, it is important to use it as a tool rather than a replacement for human programming. Always review and test code generated by GitHub Copilot Chat to ensure it meets your requirements and is free of errors or security concerns.
* **Keep prompts on topic**: GitHub Copilot Chat is intended to address coding-related queries. Limiting prompts to coding questions or tasks enhances the model's output quality.
* **Use secure coding and code review practices**: Follow best practices for secure coding—such as avoiding hard-coded passwords or SQL injection vulnerabilities—and review all generated code.
* **Be selective with context (Spaces)**: Adding only the most relevant files, repositories, and notes to a space helps Copilot stay focused. Overloading a space with unnecessary content can dilute response quality.
* **Keep context updated (Spaces)**: As your project evolves, refresh the files, issues, or documentation in your space. Outdated context may cause Copilot to generate inaccurate or incomplete answers.
* **Use instructions alongside sources (Spaces)**: Combining natural language instructions with curated sources helps Copilot better understand your intent.
* **Anchor chat in a space (Spaces)**: Starting conversations from within a space ensures continuity and relevance, keeping responses aligned with the context you've set up.
* **Review PR summaries before publishing (GitHub.com)**: Pull request summaries are intended to supplement—not replace—your own context about the changes. Always review and assess the accuracy of a generated summary before saving or publishing your pull request.
* **Review commit messages before committing (GitHub.com and GitHub Desktop)**: Commit message generation is intended to supplement—not replace—your own descriptions of the changes. Always review and edit the suggested title and description before committing. In GitHub Desktop, selecting specific lines of code or files can improve accuracy. You can opt out of commit message generation via the Copilot [settings page](<https://github.com/settings/copilot/features>) on GitHub.com.
* **Provide feedback**: If you encounter any issues or limitations, provide feedback through the thumbs up/down buttons below each chat response. The ability to provide feedback about pull request summaries is dependent on your enterprise settings. For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-enterprise-policies>). For commit message generation, you can share feedback via the [community discussion](<https://github.com/orgs/community/discussions/categories/copilot-news-and-announcements>) or by [opening an issue in the GitHub Desktop repository](<https://github.com/desktop/desktop/issues>). This helps improve the tool and address concerns.
* **Stay up to date**: GitHub Copilot Chat is evolving. Stay current with updates, new security risks, and best practices.

## 11. Learn more about GitHub Copilot Chat

For additional guidance on the responsible use of GitHub Copilot Chat, we recommend reviewing the following documentation:

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-in-your-ide/chat-with-copilot/chat-in-ide>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/copilot-for-github-tasks/create-a-pr-summary>)
* [AUTOTITLE](<https://github.com/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-github>)
* [Terminal Chat](<https://learn.microsoft.com/windows/terminal/terminal-chat>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-pre-release-license-terms>)
* [AUTOTITLE](<https://github.com/free-pro-team@latest/site-policy/github-terms/github-terms-for-additional-products-and-features#github-copilot>)
* [GitHub Copilot Trust Center](<https://copilot.github.trust.page/>)

### Learn more about responsible AI

* [Microsoft AI principles](<https://www.microsoft.com/en-us/ai/responsible-ai>)
* [Microsoft responsible AI resources](<https://www.microsoft.com/en-us/ai/responsible-ai-resources>)
* [Microsoft Azure Learning courses on responsible AI](<https://docs.microsoft.com/en-us/learn/paths/responsible-ai-business-principles/>)


</details>
