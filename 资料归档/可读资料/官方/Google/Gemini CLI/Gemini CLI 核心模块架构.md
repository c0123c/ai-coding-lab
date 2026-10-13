# Gemini CLI 核心模块架构

解释后端与 API、工具和 CLI 的职责关系，并讨论会话压缩、模型回退、文件和记忆发现及安全边界。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-29](<https://github.com/google-gemini/gemini-cli/commit/8cec56706476e73db5a12df6080d6e1b09fc3c56>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/core/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/core/Gemini CLI 核心模块架构.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI核心

Gemini CLI的核心包(`packages/core`)是Gemini
CLI的后端部分，处理与GeminiAPI的通信、管理工具以及处理从`packages/cli`发送的
请求。有关 Gemini CLI 的一般概述，请参阅 
[ 主文档页面 ](<Gemini CLI 文档入口（存档9b6e0265-575）.md>)。

## 浏览本节

- **[子agents](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/subagents.md>):**学习如何创建和使用专门的
用于复杂任务的子agents。
- **[核心工具参考](<Gemini CLI 工具参考.md>)：**有关如何使用工具的信息
由核心定义、注册和使用。
- **[内存导入处理器](<GEMINI.md 的记忆导入处理.md>)：**文档
使用 @file.md 语法的模块化 GEMINI.md 导入功能。
- **[策略引擎](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>):**使用策略引擎
对工具执行的细粒度控制。
- **[本地模型路由（实验）](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/gemma-setup.md>):** 了解如何
使用 
 自动设置命令启用本地 Gemma 模型进行模型路由决策。

## 核心的作用

虽然 Gemini CLI 的 `packages/cli` 部分提供用户界面，但 
`packages/core` 负责：

- **Gemini API 交互：** 与 Google Gemini API 安全通信，
发送用户提示并接收模型响应。
- **提示工程：**为Gemini模型构造有效的提示，
可能会合并对话历史记录、工具定义和 `GEMINI.md` 文件中的 
 教学上下文。
- **工具管理和编排：**
  - 注册可用的工具（例如，文件系统工具、shell 命令
执行）。
  - 解释来自 Gemini 模型的工具使用请求。
  - 使用提供的参数执行请求的工具。
  - 将工具执行结果返回到 Gemini 模型以进行进一步处理。
- **会话和状态管理：**跟踪会话状态，
包括历史和连贯互动所需的任何相关背景。
- **配置：**管理特定于内核的配置，例如API密钥
访问、模型选择和工具设置。

## 安全考虑

核心在安全方面起着至关重要的作用：

- **API密钥管理：**它处理`GEMINI_API_KEY`并确保它被使用
与 Gemini API 通信时安全。
- **工具执行：**当工具与本地系统交互时（例如，
`run_shell_command`），核心（及其底层工具实现）必须
谨慎行事，通常涉及沙箱机制以
防止意外修改。

## 聊天记录压缩

为了确保长时间对话不超过 Gemini
 模型的令牌限制，核心包含聊天历史记录压缩功能。

当对话接近配置模型的令牌限制时，
核心会在将对话历史记录发送到之前自动对其进行压缩
模型。这种压缩被设计为信息无损
传达，但它减少了使用的令牌总数。

您可以在
[Google AI文档](<https://ai.google.dev/gemini-api/docs/models>)中找到每个模型的令牌限制。

## 模型后备

Gemini CLI 包含模型回退机制，以确保即使默认的“pro”模型受到速率限制，您也可以继续
使用CLI。

如果您使用默认的“pro”模型，并且 CLI 检测到您受到 
 速率限制，它会自动切换到当前 
 会话的“flash”模型。这让您可以不间断地继续工作。

当配额耗尽时，使用 `gemini-2.5-flash-lite` 的内部实用程序调用（例如，提示 
 完成和分类）会静默回退到 `gemini-2.5-flash` 和 
`gemini-2.5-pro`，而不更改配置的模型。

## 文件发现服务

文件发现服务负责在项目中查找
与当前上下文相关的文件。它由`@`命令和其他需要访问文件的
工具使用。

## 内存发现服务

内存发现服务负责查找并加载为模型提供上下文的
`GEMINI.md` 文件。它以分层方式搜索这些文件
，从当前工作目录开始，向上移动
到项目根目录和用户的主目录。它还在
子目录中搜索。

这使您可以拥有全局、项目级和组件级上下文文件，
，这些文件全部组合起来为模型提供最相关的信息。

您可以使用[`/memory`命令](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>)将`show`、`add`、
和`refresh`加载的`GEMINI.md`文件的内容。

## 引文

当 Gemini 发现它正在背诵来源中的文本时，它会将引文附加到 
 输出中。默认情况下启用，但可以通过 
ui.showCitations 设置禁用。

- 当提出编辑建议时，在向用户提供选项之前会显示引文
接受。
- 引文始终显示在模型回合结束时。
- 我们删除重复的引文并按字母顺序显示它们。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI core

Gemini CLI's core package (`packages/core`) is the backend portion of Gemini
CLI, handling communication with the Gemini API, managing tools, and processing
requests sent from `packages/cli`. For a general overview of Gemini CLI, see the
[main documentation page](<Gemini CLI 文档入口（存档9b6e0265-575）.md>).

## Navigating this section

- **[Sub-agents](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/subagents.md>):** Learn how to create and use specialized
  sub-agents for complex tasks.
- **[Core tools reference](<Gemini CLI 工具参考.md>):** Information on how tools
  are defined, registered, and used by the core.
- **[Memory Import Processor](<GEMINI.md 的记忆导入处理.md>):** Documentation for the
  modular GEMINI.md import feature using @file.md syntax.
- **[Policy Engine](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/policy-engine.md>):** Use the Policy Engine for
  fine-grained control over tool execution.
- **[Local Model Routing (experimental)](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/core/gemma-setup.md>):** Learn how to
  enable use of a local Gemma model for model routing decisions using the
  automated setup command.

## Role of the core

While the `packages/cli` portion of Gemini CLI provides the user interface,
`packages/core` is responsible for:

- **Gemini API interaction:** Securely communicating with the Google Gemini API,
  sending user prompts, and receiving model responses.
- **Prompt engineering:** Constructing effective prompts for the Gemini model,
  potentially incorporating conversation history, tool definitions, and
  instructional context from `GEMINI.md` files.
- **Tool management & orchestration:**
  - Registering available tools (for example, file system tools, shell command
    execution).
  - Interpreting tool use requests from the Gemini model.
  - Executing the requested tools with the provided arguments.
  - Returning tool execution results to the Gemini model for further processing.
- **Session and state management:** Keeping track of the conversation state,
  including history and any relevant context required for coherent interactions.
- **Configuration:** Managing core-specific configurations, such as API key
  access, model selection, and tool settings.

## Security considerations

The core plays a vital role in security:

- **API key management:** It handles the `GEMINI_API_KEY` and ensures it's used
  securely when communicating with the Gemini API.
- **Tool execution:** When tools interact with the local system (for example,
  `run_shell_command`), the core (and its underlying tool implementations) must
  do so with appropriate caution, often involving sandboxing mechanisms to
  prevent unintended modifications.

## Chat history compression

To ensure that long conversations don't exceed the token limits of the Gemini
model, the core includes a chat history compression feature.

When a conversation approaches the token limit for the configured model, the
core automatically compresses the conversation history before sending it to the
model. This compression is designed to be lossless in terms of the information
conveyed, but it reduces the overall number of tokens used.

You can find the token limits for each model in the
[Google AI documentation](<https://ai.google.dev/gemini-api/docs/models>).

## Model fallback

Gemini CLI includes a model fallback mechanism to ensure that you can continue
to use the CLI even if the default "pro" model is rate-limited.

If you are using the default "pro" model and the CLI detects that you are being
rate-limited, it automatically switches to the "flash" model for the current
session. This lets you continue working without interruption.

Internal utility calls that use `gemini-2.5-flash-lite` (for example, prompt
completion and classification) silently fall back to `gemini-2.5-flash` and
`gemini-2.5-pro` when quota is exhausted, without changing the configured model.

## File discovery service

The file discovery service is responsible for finding files in the project that
are relevant to the current context. It is used by the `@` command and other
tools that need to access files.

## Memory discovery service

The memory discovery service is responsible for finding and loading the
`GEMINI.md` files that provide context to the model. It searches for these files
in a hierarchical manner, starting from the current working directory and moving
up to the project root and the user's home directory. It also searches in
subdirectories.

This lets you have global, project-level, and component-level context files,
which are all combined to provide the model with the most relevant information.

You can use the [`/memory` command](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/commands.md>) to `show`, `add`,
and `refresh` the content of loaded `GEMINI.md` files.

## Citations

When Gemini finds it is reciting text from a source it appends the citation to
the output. It is enabled by default but can be disabled with the
ui.showCitations setting.

- When proposing an edit the citations display before giving the user the option
  to accept.
- Citations are always shown at the end of the model’s turn.
- We deduplicate citations and display them in alphabetical order.


</details>
