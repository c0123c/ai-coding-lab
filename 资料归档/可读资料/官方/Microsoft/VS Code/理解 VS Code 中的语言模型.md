# 理解 VS Code 中的语言模型

讲解上下文窗口、推理、思考力度、自动选择、费用及自带密钥，说明模型可用性与账号和执行框架有关。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/language-models.md>)；源码提交（存档版）：[2026-10-08](<https://github.com/microsoft/vscode-docs/commit/a88484276b0f7e053f9145da14de6a98f32b61c5>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/concepts/language-models.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/理解 VS Code 中的语言模型.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: b2c3d4e5-6f7a-8b9c-0d1e-2f3a4b5c6d7e
DateApproved: 10/7/2026
MetaDescription: Understand language models in {% data variables.product.prodname_vscode_shortname %}, including model access, characteristics, context windows, and selection.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- copilot
- ai
- language model
- LLM
- context window
- nondeterministic
- model selection
- BYOK
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 了解语言模型

{% data variables.product.prodname_vscode %} 使用大型语言模型 (LLM) 来支持其 AI 功能。您可以灵活地选择使用哪些模型以及如何访问它们：

* **GitHub Copilot 计划中的型号**：从不同提供商提供的多个型号中进行选择，例如您的计划中包含的 Anthropic、Google 和 OpenAI。
* **其他账户**：使用 [所选线束支持的帐户](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>)，比如一个 ChatGPT 占 Codex。模型可用性、使用限制和计费取决于该帐户的计划和使用情况。
* **自带密钥 (BYOK)**：使用您自己的 API 密钥添加来自其他提供商的模型，或托管您自己的模型，包括离线运行的本地模型。通过BYOK，您可以在{% data variables.product.prodname_vscode_shortname %}中使用agents，而无需GitHub Copilot套餐。

本文解释了语言模型的工作原理、其特征以及如何考虑模型选择。

![语言模型编辑器的屏幕截图，显示可用模型的列表。](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/language-models/language-models-editor.png>)

## 语言模型如何工作

语言模型处理文本输入（“提示”）并生成文本输出。在 {% data variables.product.prodname_vscode_shortname %} 中，提示由多个来源组合而成：您的消息、对话历史记录、文件内容、工具输出和自定义指令。该模型生成的响应可以包括解释、代码编辑或调用 [ 工具 ](<https://github.com/docs/agents/concepts/tools.md>) 的请求。

语言模型不直接执行代码或访问文件。相反，它们生成 [agent 循环 ](<https://github.com/docs/agents/concepts/agents.md#agent-loop>) 解释为操作的文本。当模型请求工具调用时，{% data variables.product.prodname_vscode_shortname %} 执行该工具并将结果反馈给模型以进行下一次迭代。

语言模型的一些关键特征包括：

* **非确定性**：相同的提示每次都会产生不同的结果。这是设计使然，反映了模型如何从概率分布中进行采样。
* **上下文相关**：响应的质量取决于提示中提供的[上下文](<https://github.com/docs/agents/concepts/context.md>)的质量和相关性。
* **知识边界**：模型根据特定日期之前的数据进行训练，可能会针对超出其训练数据的主题生成过时或不正确的信息。 {% data variables.product.prodname_vscode_shortname %} 通过工具和工作区索引缓解了这一问题。

## 上下文窗口

上下文窗口是模型在单个请求中可以处理的信息总量。它包括一切：系统提示、自定义指令、对话历史记录、文件内容、工具输出和当前消息。不同的模型有不同的上下文窗口大小。

当上下文窗口填满时，{% data variables.product.prodname_vscode_shortname %} 会自动总结对话的旧部分以腾出空间。这意味着长时间对话初期的重要细节可能会被压缩或丢失。您还可以在聊天输入中键入 `/compact` 以随时手动触发压缩。 （可选）在命令后添加自定义指令以指导摘要，例如 `/compact focus on the API design decisions`。

详细了解 [{% data variables.product.prodname_vscode_shortname %} 如何组装上下文 ](<https://github.com/docs/agents/concepts/context.md>) 和 [ 上下文压缩 ](<https://github.com/docs/agents/run/sessions/manage-sessions.md#compact-conversation-context>)。

## 思考与推理

一些语言模型可以在产生响应之前执行扩展推理，也称为“思考”。推理模型不是立即生成答案，而是首先在内部解决问题，考虑多种方法，评估权衡，并建立逐步的思想链。这种内部推理发生在与最终输出分开的专用思维令牌中。

推理模型对于多步调试、架构规划、代码重构以及数学或科学分析等复杂任务特别有效。对于生成样板或回答基本问题等更简单的任务，额外的推理会增加延迟，但没有显着的好处。

### 思考努力

思考努力控制模型对每个请求应用的推理量。更高的努力水平会产生更彻底的内部推理，从而提高复杂问题的质量。较低的工作量水平通过限制或跳过思考步骤来减少延迟和令牌使用。

可用的工作量级别及其默认值因模型和提供商而异。一些模型还支持*自适应思维*，其中模型根据每个请求的复杂性动态决定是否推理以及推理多少，而不是始终使用固定的思维预算。

{% data variables.product.prodname_vscode_shortname %} 根据评估和在线绩效数据设置默认工作水平，并在支持的情况下启用自适应推理。对于大多数用例，默认值无需更改即可正常工作。

### 思考令牌

思考标记计入模型的上下文窗口，即使它们在响应中不可见。实际的思考输出通常以汇总形式返回，或者可以完全省略以降低延迟。请记住，较高的思考努力水平可以产生更多的思考标记，这会增加延迟。

了解如何在{% data variables.product.prodname_vscode_shortname %}中[配置思维努力级别](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>)。

## 选择合适的型号

每个模型都有不同的优势。有些针对速度进行了优化，非常适合快速编辑和简单的问题。其他人则拥有更大的上下文窗口或更好的推理能力，使它们成为复杂任务的理想选择。作为一般准则：

* **快速模型**最适合快速代码编辑、样板生成和简单的问题。
* **推理模型** 擅长复杂的重构、架构决策、多步骤调试以及需要分析权衡的任务。
* **大型上下文模型**非常适合保留更多信息的大型代码库或长时间对话。

您可以根据特定任务的需要随时切换模型。有关详细比较，请参阅 GitHub Copilot 文档中的 [为您的任务选择正确的 AI 模型 ](<https://docs.github.com/en/copilot/using-github-copilot/ai-models/choosing-the-right-ai-model-for-your-task>)。

### 自动选型

GitHub Copilot 中的自动模型选择结合了两个系统，将每个请求路由到最佳模型。一个系统跟踪实时模型的运行状况和可用性，而另一个系统则评估任务复杂性。它们共同将每个任务与能够最有效地解决它的模型相匹配，为需要它们的问题保留成本较高的推理模型，并将更简单的任务路由到更快的模型。

自动从多个模型中进行选择，并尊重您组织的 [ 模型访问设置 ](<https://docs.github.com/en/copilot/how-tos/use-ai-models/configure-access-to-ai-models>)。 Auto 不会选择管理员策略排除的模型或数据驻留策略限制的模型。

有关更多详细信息，请参阅GitHub文档中的[关于Copilot自动模型选择](<https://docs.github.com/en/copilot/concepts/auto-model-selection>)。

### AI 积分和模型成本

每个 Copilot 计划包含每月 [AI 积分 ](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>) 的津贴。根据模型和处理的代币数量，不同的模型以不同的速率消耗 AI 积分。功能更强大的型号每个代币的成本更高，而更轻的型号则可以进一步扩展您的使用范围。当您使用自动模型选择时，{% data variables.product.prodname_vscode_shortname %} 将每个请求路由到平衡质量和成本的高效模型。

其他因素也会影响信用消耗，例如[思考努力](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>)（更高的努力产生更多的思考标记）、上下文窗口大小和工具使用。提示缓存通过重用前一个请求的令牌来降低请求的有效成本。您可以使用 [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>) 检查缓存命中率。有关减少信用消耗的实用技巧，请参阅[优化AI信用使用](<https://github.com/docs/agents/guides/optimize-usage.md>)。

了解如何在{% data variables.product.prodname_vscode_shortname %}中[选择和配置语言模型](<https://github.com/docs/agent-customization/language-models.md>)。

## 带上你自己的语言模型密钥

如果内置模型不能满足您的需求，您可以携带自己的语言模型API密钥（BYOK）来使用其他提供商的模型或在本地运行模型。 BYOK 允许您连接到任何兼容的模型提供商，同时仍然使用 {% data variables.product.prodname_vscode_shortname %} 聊天和代理编码功能。 {% data variables.product.prodname_vscode_shortname %} 具有受支持的提供商的内置列表，或者您可以从 {% data variables.product.prodname_vs_marketplace %} 添加更多提供商。

您可以将 BYOK 用于 agent 体验和实用任务，例如提交消息生成。

### 为什么要带自己的钥匙

* **模型选择**：除了内置模型之外，还可以访问来自不同提供商的数百个模型。
* **实验**：尝试内置模型中尚未提供的新模型或功能。
* **本地计算**：将您自己的计算用于GitHub Copilot中已支持的模型之一或运行尚不可用的模型。
* **更好的控制**：绕过标准速率限制和对内置模型施加的限制。
* **离线和隔离环境**：与 Ollama 等本地模特使用 AI 聊天功能，无需 GitHub 帐户、Copilot 计划或互联网连接。

### 注意事项

* BYOK仅适用于聊天体验和实用任务。内联建议（代码完成）和依赖嵌入的功能（例如语义搜索）仍然需要 GitHub Copilot 计划。
* 功能取决于模型，可能与内置模型不同，例如，对工具调用、视觉或思维的支持。
* 使用 BYOK 时，无法保证对模型的输出应用负责任的 AI 过滤。

了解如何在 {% data variables.product.prodname_vscode_shortname %} 中 [ 添加您自己的语言模型密钥 ](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)。

## 相关资源

* [上下文](<https://github.com/docs/agents/concepts/context.md>)
* [AI {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agent-customization/language-models.md>) 中的语言模型


## 原文对照

<details>
<summary>展开完整原文</summary>

# Understand language models

{% data variables.product.prodname_vscode %} uses large language models (LLMs) to power its AI features. You have flexibility in which models you use and how you access them:

* **Models from your GitHub Copilot plan**: choose from multiple models by different providers, such as Anthropic, Google, and OpenAI, included with your plan.
* **Other accounts**: use an [account supported by the selected harness](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>), such as a ChatGPT account for Codex. Model availability, usage limits, and billing depend on that account's plan and the harness.
* **Bring your own key (BYOK)**: add models from other providers with your own API key, or host your own models, including local models that run offline. With BYOK, you can use agents in {% data variables.product.prodname_vscode_shortname %} without a GitHub Copilot plan.

This article explains how language models work, their characteristics, and how to think about model selection.

![Screenshot of the Language Models editor, showing the list of available models.](<https://raw.githubusercontent.com/microsoft/vscode-docs/7dde535199826fdb8e278bdf6f0c3bff0aae0138/docs/agents/images/language-models/language-models-editor.png>)

## How language models work

A language model processes text input (a "prompt") and generates text output. In {% data variables.product.prodname_vscode_shortname %}, the prompt is assembled from multiple sources: your message, conversation history, file contents, tool outputs, and custom instructions. The model generates responses that can include explanations, code edits, or requests to call [tools](<https://github.com/docs/agents/concepts/tools.md>).

Language models don't execute code or access files directly. Instead, they generate text that the [agent loop](<https://github.com/docs/agents/concepts/agents.md#agent-loop>) interprets as actions. When a model requests a tool call, {% data variables.product.prodname_vscode_shortname %} executes the tool and feeds the result back to the model for the next iteration.

Some key characteristics of language models include:

* **Nondeterministic**: the same prompt can produce different results each time. This is by design and reflects how the model samples from probability distributions.
* **Context-dependent**: the quality of the response depends on the quality and relevance of the [context](<https://github.com/docs/agents/concepts/context.md>) provided in the prompt.
* **Knowledge boundaries**: models are trained on data up to a certain date and might produce outdated or incorrect information for topics beyond their training data. {% data variables.product.prodname_vscode_shortname %} mitigates this with tools and workspace indexing.

## Context window

The context window is the total amount of information a model can process in a single request. It includes everything: the system prompt, custom instructions, conversation history, file contents, tool outputs, and your current message. Different models have different context window sizes.

When the context window fills up, {% data variables.product.prodname_vscode_shortname %} automatically summarizes older parts of the conversation to make room. This means important details from early in a long conversation might be compressed or lost. You can also type `/compact` in the chat input to manually trigger compaction at any time. Optionally, add custom instructions after the command to guide the summary, for example `/compact focus on the API design decisions`.

Learn more about [how {% data variables.product.prodname_vscode_shortname %} assembles context](<https://github.com/docs/agents/concepts/context.md>) and [context compaction](<https://github.com/docs/agents/run/sessions/manage-sessions.md#compact-conversation-context>).

## Thinking and reasoning

Some language models can perform extended reasoning, also called "thinking", before producing a response. Instead of generating an answer immediately, a reasoning model first works through the problem internally, considering multiple approaches, evaluating trade-offs, and building a step-by-step chain of thought. This internal reasoning happens in dedicated thinking tokens that are separate from the final output.

Reasoning models are especially effective for complex tasks like multi-step debugging, architectural planning, code refactoring, and mathematical or scientific analysis. For simpler tasks like generating boilerplate or answering basic questions, the extra reasoning adds latency without significant benefit.

### Thinking effort

Thinking effort controls how much reasoning a model applies to each request. Higher effort levels produce more thorough internal reasoning, which improves quality for complex problems. Lower effort levels reduce latency and token usage by limiting or skipping the thinking step.

The available effort levels and their default values vary by model and provider. Some models also support *adaptive thinking*, where the model dynamically decides whether and how much to reason based on the complexity of each request, rather than always using a fixed thinking budget.

{% data variables.product.prodname_vscode_shortname %} sets default effort levels based on evaluations and online performance data, and has adaptive reasoning enabled where supported. For most use cases, the defaults work well without changes.

### Thinking tokens

Thinking tokens count toward the model's context window, even though they are not visible in the response. The actual thinking output is typically returned in summarized form or can be omitted entirely for lower latency. Keep in mind that higher thinking effort levels can produce more thinking tokens, which can increase latency.

Learn how to [configure the thinking effort level](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>) in {% data variables.product.prodname_vscode_shortname %}.

## Choose the right model

Each model has different strengths. Some are optimized for speed and work well for quick edits and simple questions. Others have larger context windows or better reasoning capabilities, making them ideal for complex tasks. As a general guideline:

* **Fast models** are best for quick code edits, boilerplate generation, and straightforward questions.
* **Reasoning models** excel at complex refactoring, architectural decisions, multi-step debugging, and tasks that require analyzing trade-offs.
* **Large context models** work well for large codebases or long conversations where retaining more information matters.

You can switch models at any time, based on your needs for a particular task. For a detailed comparison, see [Choosing the right AI model for your task](<https://docs.github.com/en/copilot/using-github-copilot/ai-models/choosing-the-right-ai-model-for-your-task>) in the GitHub Copilot documentation.

### Auto model selection

Auto model selection in GitHub Copilot combines two systems to route each request to the optimal model. One system tracks real-time model health and availability, while the other evaluates task complexity. Together, they match each task to the model that can solve it most efficiently, reserving higher-cost reasoning models for problems that need them and routing simpler tasks to faster models.

Auto selects from multiple models and respects your organization's [model access settings](<https://docs.github.com/en/copilot/how-tos/use-ai-models/configure-access-to-ai-models>). Auto won't select models excluded by administrator policies or models restricted by data-residency policies.

For more details, see [About Copilot auto model selection](<https://docs.github.com/en/copilot/concepts/auto-model-selection>) in the GitHub documentation.

### AI credits and model costs

Each Copilot plan includes a monthly allowance of [AI credits](<https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-individuals>). Different models consume AI credits at different rates, based on the model and the number of tokens processed. More capable models cost more per token, while lighter models extend your usage further. When you use auto model selection, {% data variables.product.prodname_vscode_shortname %} routes each request to an efficient model that balances quality and cost.

Other factors also affect credit consumption, such as [thinking effort](<https://github.com/docs/agent-customization/language-models.md#configure-thinking-effort>) (higher effort produces more thinking tokens), context window size, and tool usage. Prompt caching lowers the effective cost of a request by reusing tokens from a previous one. You can inspect cache hit rates with the [Cache Explorer](<https://github.com/docs/agents/agent-troubleshooting/cache-explorer.md>). For practical tips on reducing credit consumption, see [optimize AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md>).

Learn how to [choose and configure language models](<https://github.com/docs/agent-customization/language-models.md>) in {% data variables.product.prodname_vscode_shortname %}.

## Bring your own language model key

If the built-in models don't meet your needs, you can bring your own language model API key (BYOK) to use models from other providers or to run models locally. BYOK lets you connect to any compatible model provider while still using the {% data variables.product.prodname_vscode_shortname %} chat and agentic coding features. {% data variables.product.prodname_vscode_shortname %} has a built-in list of supported providers or you can add more providers from the {% data variables.product.prodname_vs_marketplace %}.

You can use BYOK for both the agent experience and utility tasks, such as commit message generation.

### Why bring your own key

* **Model choice**: access hundreds of models from different providers, beyond the built-in models.
* **Experimentation**: try new models or features that are not yet available in the built-in models.
* **Local compute**: use your own compute for one of the models already supported in GitHub Copilot or to run models not yet available.
* **Greater control**: bypass the standard rate limits and restrictions imposed on the built-in models.
* **Offline and air-gapped environments**: use AI chat features with a local model like Ollama without a GitHub account, a Copilot plan, or an internet connection.

### Considerations

* BYOK only applies to the chat experience and utility tasks. Inline suggestions (code completions) and features that rely on embeddings, such as semantic search, still require a GitHub Copilot plan.
* Capabilities are model-dependent and might differ from the built-in models, for example, support for tool calling, vision, or thinking.
* There is no guarantee that responsible AI filtering is applied to the model's output when using BYOK.

Learn how to [add your own language model key](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>) in {% data variables.product.prodname_vscode_shortname %}.

## Related resources

* [Context](<https://github.com/docs/agents/concepts/context.md>)
* [AI language models in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/agent-customization/language-models.md>)


</details>
