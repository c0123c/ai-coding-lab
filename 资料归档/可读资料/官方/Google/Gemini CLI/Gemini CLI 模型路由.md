# Gemini CLI 模型路由

介绍模型失败时的回退机制、本地路由和选择优先级，说明自动路由的适用条件。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-04-29](<https://geminicli.com/docs/cli/model-routing/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/model-routing/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 模型路由.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 模型路由

Gemini CLI 包含模型路由功能，可在模型发生故障时自动切换到 
 后备模型。此功能默认启用
，并在主模型不可用时提供恢复能力。

## 它是如何运作的

[标题为“工作原理”的部分](<#它是如何运作的>)

模型路由由 `ModelAvailabilityService` 管理，它监视模型 
 运行状况并根据定义的 
 策略自动将请求路由到可用模型。

1. **模型失败：**如果当前选择的模型失败（例如，由于
配额或服务器错误），CLI 将启动回退过程。
2. **用户同意：**根据故障和模型的策略，CLI
可能会提示您切换到后备模型（默认情况下始终提示
您）。

某些内部实用程序调用（例如提示完成和分类）
 对 `gemini-2.5-flash-lite` 使用静默回退链，并将回退 
 到 `gemini-2.5-flash` 和 `gemini-2.5-pro`，而不提示或更改 
 配置的模型。
3. **模型切换：**如果获得批准，或者如果策略允许静默回退，
CLI 将在当前回合或会话的剩余时间
 中使用可用的后备模型。

### 本地模型路由（实验性）

[标题为“本地模型路由（实验）”的部分](<#本地模型路由实验性>)

Gemini CLI支持使用本地模型进行路由决策。配置后，
Gemini CLI 将使用本地运行的 **Gemma** 模型来制定路由决策 
（而不是将路由决策发送到托管模型）。此功能可以帮助
降低与托管模型使用相关的成本，同时提供类似的路由
决策延迟和质量。

最简单的设置方法是使用自动 `gemini gemma setup`
 命令。

有关如何配置本地模型路由的更多详细信息，请参阅
[`gemini gemma` — 本地模型路由设置](<https://geminicli.com/docs/core/gemma-setup>)。

### 型号选择优先级

[标题为“模型选择优先级”的部分](<#型号选择优先级>)

Gemini CLI 使用的模型由以下优先顺序确定：

1. **`--model` 命令行标志：** 使用 `--model` 标志指定的模型
启动时将始终使用 CLI。
2. **`GEMINI_MODEL`环境变量：**如果未使用`--model`标志，
CLI 将使用 `GEMINI_MODEL` 环境 
 变量中指定的模型。
3. `settings.json`中的**`model.name`:** 如果以上均未设置，则
将使用 `settings.json` 文件 
 的 `model.name` 属性中指定的模型。
4. **本地模型（实验）：**如果启用 Gemma 本地模型路由器
在您的 `settings.json` 文件中，CLI 将使用本地 Gemma 模型 
（而不是 Gemini 模型）将请求路由到适当的模型。
5. **默认型号：** 如果以上均未设置，则默认型号为
使用过。默认型号为`auto`

最后更新：四月 29、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Model routing

Gemini CLI includes a model routing feature that automatically switches to a
fallback model in case of a model failure. This feature is enabled by default
and provides resilience when the primary model is unavailable.

## How it works

[Section titled “How it works”](<#how-it-works>)

Model routing is managed by the `ModelAvailabilityService`, which monitors model
health and automatically routes requests to available models based on defined
policies.

1. **Model failure:** If the currently selected model fails (for example, due
   to quota or server errors), the CLI will initiate the fallback process.
2. **User consent:** Depending on the failure and the model’s policy, the CLI
   may prompt you to switch to a fallback model (by default always prompts
   you).

   Some internal utility calls (such as prompt completion and classification)
   use a silent fallback chain for `gemini-2.5-flash-lite` and will fall back
   to `gemini-2.5-flash` and `gemini-2.5-pro` without prompting or changing the
   configured model.
3. **Model switch:** If approved, or if the policy allows for silent fallback,
   the CLI will use an available fallback model for the current turn or the
   remainder of the session.

### Local Model Routing (Experimental)

[Section titled “Local Model Routing (Experimental)”](<#local-model-routing-experimental>)

Gemini CLI supports using a local model for routing decisions. When configured,
Gemini CLI will use a locally-running **Gemma** model to make routing decisions
(instead of sending routing decisions to a hosted model). This feature can help
reduce costs associated with hosted model usage while offering similar routing
decision latency and quality.

The easiest way to set this up is using the automated `gemini gemma setup`
command.

For more details on how to configure local model routing, see
[`gemini gemma` — Local Model Routing Setup](<https://geminicli.com/docs/core/gemma-setup>).

### Model selection precedence

[Section titled “Model selection precedence”](<#model-selection-precedence>)

The model used by Gemini CLI is determined by the following order of precedence:

1. **`--model` command-line flag:** A model specified with the `--model` flag
   when launching the CLI will always be used.
2. **`GEMINI_MODEL` environment variable:** If the `--model` flag is not used,
   the CLI will use the model specified in the `GEMINI_MODEL` environment
   variable.
3. **`model.name` in `settings.json`:** If neither of the above are set, the
   model specified in the `model.name` property of your `settings.json` file
   will be used.
4. **Local model (experimental):** If the Gemma local model router is enabled
   in your `settings.json` file, the CLI will use the local Gemma model
   (instead of Gemini models) to route the request to an appropriate model.
5. **Default model:** If none of the above are set, the default model will be
   used. The default model is `auto`

Last updated: Apr 29, 2026


</details>
