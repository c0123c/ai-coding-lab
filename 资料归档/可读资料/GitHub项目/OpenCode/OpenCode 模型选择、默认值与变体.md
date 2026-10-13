# OpenCode 模型选择、默认值与变体

产品指南。介绍供应商连接、模型选择和加载顺序、默认模型、自定义配置及推理变体，帮助配置编码任务的模型；推荐列表具有存档时效。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-01-12](<https://github.com/anomalyco/opencode/commit/2e9c22d9114c289f30397d4888bdf523f8d816b1>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：模型推荐和可用性不是永久结论。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/models.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 模型选择、默认值与变体.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Models
description: Configuring an LLM provider and model.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 使用 [AI SDK](<https://ai-sdk.dev/>) 和 [Models.dev](<https://models.dev>) 来支持 **75+ LLM 提供程序**，并且支持运行本地模型。

---

## 供应商

默认情况下会预加载大多数流行的提供程序。如果您通过 `/connect` 命令添加了提供程序的凭据，那么当您启动 OpenCode 时，它们将可用。

了解有关 [ 提供商 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md>) 的更多信息。

---

## 选择型号

配置完提供商后，您可以通过输入以下内容来选择您想要的型号：

```bash
/models
```

---

## 推荐型号

那里有很多型号，每周都会有新型号问世。

**提示：**

考虑使用我们推荐的模型之一。

然而，既擅长生成代码又擅长工具调用的只有少数。

以下是与 OpenCode 配合良好的几种型号，排名不分先后。 （这不是详尽的列表，也不一定是最新的）：

- GPT 5.2
- GPT 5.1 Codex
- Claude 作品 4.5
- Claude 十四行诗 4.5
- 极小极大 M2.1
- Gemini 3 专业版

---

## 设置默认值

要将其中之一设置为默认模型，您可以在 
OpenCode 配置中设置 `model` 键。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "lmstudio/google/gemma-3n-e4b"
}
```

这里完整的 ID 是 `provider_id/model_id`。例如，如果您使用 [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>)，则可以使用 `opencode/gpt-5.1-codex` 代替 GPT 5.1 Codex。

如果您配置了 [ 自定义提供程序 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#custom>)，则 `provider_id` 是配置的 `provider` 部分中的密钥，`model_id` 是 `provider.models` 中的密钥。

---

## 配置模型

您可以通过 config.json 全局配置模型的选项。

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openai": {
      "models": {
        "gpt-5": {
          "options": {
            "reasoningEffort": "high",
            "textVerbosity": "low",
            "reasoningSummary": "auto",
            "include": ["reasoning.encrypted_content"],
          },
        },
      },
    },
    "anthropic": {
      "models": {
        "claude-sonnet-4-5-20250929": {
          "options": {
            "thinking": {
              "type": "enabled",
              "budgetTokens": 16000,
            },
          },
        },
      },
    },
  },
}
```

在这里，我们为两个内置模型配置全局设置：通过 `openai` 提供程序访问时为 `gpt-5`，通过 `anthropic` 提供程序访问时为 `claude-sonnet-4-20250514`。
内置提供程序和模型名称可以在[Models.dev](<https://models.dev>)。

您还可以为您正在使用的任何 agents 配置这些选项。 agent 配置会覆盖此处的所有全局选项。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#additional>)。

您还可以定义扩展内置变体的自定义变体。变体允许您为同一模型配置不同的设置，而无需创建重复的条目：

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "opencode": {
      "models": {
        "gpt-5": {
          "variants": {
            "high": {
              "reasoningEffort": "high",
              "textVerbosity": "low",
              "reasoningSummary": "auto",
            },
            "low": {
              "reasoningEffort": "low",
              "textVerbosity": "low",
              "reasoningSummary": "auto",
            },
          },
        },
      },
    },
  },
}
```

---

## 变体

许多型号支持具有不同配置的多种变体。 OpenCode 附带流行提供商的内置默认变体。

### 内置变体

OpenCode 附带许多提供商的默认变体：

**Anthropic**:

- `high` - 高思维预算（默认）
- `max` - 最大思考预算

**OpenAI**:

因型号而异，但大致如下：

- `none` - 没有推理
- `minimal` - 最少的推理工作
- `low` - 推理工作量低
- `medium` - 中等推理努力
- `high` - 高推理能力
- `xhigh` - 超高的推理能力

**Google**:

- `low` - 降低 effort/token 预算
- `high` - 更高的 effort/token 预算

**提示：**

该列表并不全面。许多其他提供商也有内置的默认值。

### 定制变体

您可以覆盖现有变体或添加您自己的变体：

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openai": {
      "models": {
        "gpt-5": {
          "variants": {
            "thinking": {
              "reasoningEffort": "high",
              "textVerbosity": "low",
            },
            "fast": {
              "disabled": true,
            },
          },
        },
      },
    },
  },
}
```

### 循环变体

使用按键绑定 `variant_cycle` 在变体之间快速切换。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>)。

---

## 加载模型

当OpenCode启动时，它会按以下优先顺序检查模型：

1. `--model` 或 `-m` 命令行标志。格式与配置文件中的相同：`provider_id/model_id`。

2. OpenCode配置中的型号列表。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514"
}
```

这里的格式是`provider/model`。

3. 最后使用的型号。

4. 第一个模型使用内部优先级。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode uses the [AI SDK](<https://ai-sdk.dev/>) and [Models.dev](<https://models.dev>) to support **75+ LLM providers** and it supports running local models.

---

## Providers

Most popular providers are preloaded by default. If you've added the credentials for a provider through the `/connect` command, they'll be available when you start OpenCode.

Learn more about [providers](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md>).

---

## Select a model

Once you've configured your provider you can select the model you want by typing in:

```bash
/models
```

---

## Recommended models

There are a lot of models out there, with new models coming out every week.

**提示：**

Consider using one of the models we recommend.

However, there are only a few of them that are good at both generating code and tool calling.

Here are several models that work well with OpenCode, in no particular order. (This is not an exhaustive list nor is it necessarily up to date):

- GPT 5.2
- GPT 5.1 Codex
- Claude Opus 4.5
- Claude Sonnet 4.5
- Minimax M2.1
- Gemini 3 Pro

---

## Set a default

To set one of these as the default model, you can set the `model` key in your
OpenCode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "lmstudio/google/gemma-3n-e4b"
}
```

Here the full ID is `provider_id/model_id`. For example, if you're using [OpenCode Zen](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>), you would use `opencode/gpt-5.1-codex` for GPT 5.1 Codex.

If you've configured a [custom provider](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 云端与本地模型服务接入.md#custom>), the `provider_id` is key from the `provider` part of your config, and the `model_id` is the key from `provider.models`.

---

## Configure models

You can globally configure a model's options through the config.

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openai": {
      "models": {
        "gpt-5": {
          "options": {
            "reasoningEffort": "high",
            "textVerbosity": "low",
            "reasoningSummary": "auto",
            "include": ["reasoning.encrypted_content"],
          },
        },
      },
    },
    "anthropic": {
      "models": {
        "claude-sonnet-4-5-20250929": {
          "options": {
            "thinking": {
              "type": "enabled",
              "budgetTokens": 16000,
            },
          },
        },
      },
    },
  },
}
```

Here we're configuring global settings for two built-in models: `gpt-5` when accessed via the `openai` provider, and `claude-sonnet-4-20250514` when accessed via the `anthropic` provider.
The built-in provider and model names can be found on [Models.dev](<https://models.dev>).

You can also configure these options for any agents that you are using. The agent config overrides any global options here. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 主 Agent、子 Agent 与配置.md#additional>).

You can also define custom variants that extend built-in ones. Variants let you configure different settings for the same model without creating duplicate entries:

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "opencode": {
      "models": {
        "gpt-5": {
          "variants": {
            "high": {
              "reasoningEffort": "high",
              "textVerbosity": "low",
              "reasoningSummary": "auto",
            },
            "low": {
              "reasoningEffort": "low",
              "textVerbosity": "low",
              "reasoningSummary": "auto",
            },
          },
        },
      },
    },
  },
}
```

---

## Variants

Many models support multiple variants with different configurations. OpenCode ships with built-in default variants for popular providers.

### Built-in variants

OpenCode ships with default variants for many providers:

**Anthropic**:

- `high` - High thinking budget (default)
- `max` - Maximum thinking budget

**OpenAI**:

Varies by model but roughly:

- `none` - No reasoning
- `minimal` - Minimal reasoning effort
- `low` - Low reasoning effort
- `medium` - Medium reasoning effort
- `high` - High reasoning effort
- `xhigh` - Extra high reasoning effort

**Google**:

- `low` - Lower effort/token budget
- `high` - Higher effort/token budget

**提示：**

This list is not comprehensive. Many other providers have built-in defaults too.

### Custom variants

You can override existing variants or add your own:

**代码示例：** `opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openai": {
      "models": {
        "gpt-5": {
          "variants": {
            "thinking": {
              "reasoningEffort": "high",
              "textVerbosity": "low",
            },
            "fast": {
              "disabled": true,
            },
          },
        },
      },
    },
  },
}
```

### Cycle variants

Use the keybind `variant_cycle` to quickly switch between variants. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 快捷键与终端输入设置.md>).

---

## Loading models

When OpenCode starts up, it checks for models in the following priority order:

1. The `--model` or `-m` command line flag. The format is the same as in the config file: `provider_id/model_id`.

2. The model list in the OpenCode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514"
}
```

   The format here is `provider/model`.

3. The last used model.

4. The first model using an internal priority.


</details>
