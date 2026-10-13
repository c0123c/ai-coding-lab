# OpenCode Zen 模型服务、接入与团队配置

服务参考。介绍可选模型服务的接入、端点、模型、费用、限额、隐私和团队角色等，帮助模型服务选择；价格与可用模型仅代表存档说明。

- 来源：OpenCode
- 资料类型：服务参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/anomalyco/opencode/commit/388406238bd5ca15564a762840a2362c3a45bd9c>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：具有时效性的模型列表、费用和隐私条件应核对当前官方服务。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/zen.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode Zen 模型服务、接入与团队配置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Zen
description: Curated list of models provided by OpenCode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode Zen 是 OpenCode 团队提供的经过测试和验证的模型列表。

Zen 的工作方式与 OpenCode 中的任何其他提供程序一样。您登录 OpenCode Zen 并获取 
 您的 API 密钥。它是**完全可选的**，您不需要使用它来使用
OpenCode。

---

## 背景

那里有大量模型，但只有少数 
 这些模型可以很好地编码 agents。此外，大多数提供商的
配置非常不同；所以你会得到非常不同的性能和质量。

**提示：**

我们测试了一组与 OpenCode 配合良好的模型和提供程序。

因此，如果您通过 OpenRouter 之类的方式使用模型，您永远无法
 确定您是否获得了所需模型的最佳版本。

为了解决这个问题，我们做了几件事：

1. 我们测试了一组选定的模型，并与他们的团队讨论了如何
最好运行它们。
2. 然后我们与一些提供商合作以确保这些服务得到服务
正确。
3. 最后，我们对 model/provider 的组合进行了基准测试，得出了
并附上一份我们觉得不错的推荐清单。

OpenCode Zen 是一个 AI 网关，可让您访问这些模型。

---

## 它是如何运作的

OpenCode Zen 的工作方式与 OpenCode 中的任何其他提供程序一样。

1. 您登录 **[OpenCode Zen](<https://opencode.ai/auth>)**，添加您的账单
详细信息，然后复制您的 API 密钥。
2. 您在 TUI 中运行 `/connect` 命令，选择 OpenCode Zen，然后粘贴 API 密钥。
3. 运行【TUI】中的【`/models`】即可看到我们推荐的型号列表。

您需要按请求付费，并且您可以将积分添加到您的帐户中。

---

## 端点

您还可以通过以下 API 端点访问我们的模型。

|型号|型号【ID】|端点|AI SDK 封装|
| ------------------------------- | ------------------------------- | --------------------------------------------------------- | --------------------------- |
|GPT 6 阿斯特拉|gpt-6-astra| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 6 索尔|gpt-6-sol| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 6.1 索尔|gpt-6.1-sol| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 6 露娜|gpt-6-luna| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.6 索尔|gpt-5.6-sol| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.6 泰拉|gpt-5.6-terra| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.6 露娜|gpt-5.6-luna| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.5                         |gpt-5.5| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.5 专业版|gpt-5.5-pro| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.4                         |gpt-5.4| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.4 专业版|gpt-5.4-pro| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.4 迷你|gpt-5.4-迷你| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.4 纳米|gpt-5.4-纳米| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.3 Codex                   |gpt-5.3-codex| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.3 Codex 火花|gpt-5.3-codex-spark| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.2                         |gpt-5.2| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.2 Codex                   |gpt-5.2-codex| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1                         |gpt-5.1| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1 Codex                   |gpt-5.1-codex| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.1 Codex 最大|gpt-5.1-codex-max| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5.1 Codex 迷你|gpt-5.1-codex-mini| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5                           |gpt-5| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5 Codex                     |gpt-5-codex| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|GPT 5 纳米|gpt-5-纳米| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|Claude 寓言 5.1|克劳德寓言-5-1| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 寓言 5|克劳德寓言-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 5.5|克劳德作品-5-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 5|克劳德作品-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 4.8|克劳德作品-4-8| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 4.7|克劳德作品-4-7| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 4.6|克劳德作品-4-6| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 作品 4.5|克劳德作品-4-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 十四行诗 5.5|克劳德十四行诗-5-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 十四行诗 5|克劳德十四行诗-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 十四行诗 4.6|克劳德十四行诗-4-6| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 十四行诗 4.5|克劳德十四行诗-4-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 俳句 5.5|克劳德俳句-5-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Claude 俳句 4.5|克劳德俳句-4-5| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Gemini 3.8 闪光灯|双子座-3.8-闪光| `https://opencode.ai/zen/v1/models/gemini-3.8-flash`      | `@ai-sdk/google`            |
|Gemini 3.7 闪光灯|双子座-3.7-闪光| `https://opencode.ai/zen/v1/models/gemini-3.7-flash`      | `@ai-sdk/google`            |
|Gemini 3.6 闪光灯|双子座-3.6-闪光| `https://opencode.ai/zen/v1/models/gemini-3.6-flash`      | `@ai-sdk/google`            |
|Gemini 3.5 闪光灯|双子座-3.5-闪光| `https://opencode.ai/zen/v1/models/gemini-3.5-flash`      | `@ai-sdk/google`            |
|Gemini 3.5 Flash Lite|双子座-3.5-flash-lite| `https://opencode.ai/zen/v1/models/gemini-3.5-flash-lite` | `@ai-sdk/google`            |
|Gemini 3.1 专业版|双子座-3.1-pro| `https://opencode.ai/zen/v1/models/gemini-3.1-pro`        | `@ai-sdk/google`            |
|Gemini 3 闪光灯|双子座-3-闪光| `https://opencode.ai/zen/v1/models/gemini-3-flash`        | `@ai-sdk/google`            |
|格洛克 4.7|grok-4.7| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|格洛克 4.6|grok-4.6| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|格洛克 4.5|grok-4.5| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|Grok 构建 0.1|grok-build-0.1| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|缪斯火花 1.3|缪斯-火花-1.3| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|缪斯火花 1.2|缪斯-火花-1.2| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
|Qwen3.8 最大|qwen3.8-最大| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Qwen3.8 闪光|qwen3.8-flash| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Qwen3.7 最大|qwen3.7-最大| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Qwen3.7加|qwen3.7-plus| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Qwen3.6加|qwen3.6-plus| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|Qwen3.5加|qwen3.5-plus| `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
|DeepSeek V4.1 闪存|deepseek-v4.1-flash| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|DeepSeek V4 专业版|deepseek-v4-pro| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|DeepSeek V4 闪光灯|deepseek-v4-flash| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|DeepSeek V4 Flash 视觉实验|deepseek-v4-flash-vision-exp| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M3                      |极小极大-m3| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M2.7                    |极小极大-m2。7| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M2.5                    |极小极大-m2。5| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|GLM 5.3 闪光灯|glm-5.3-flash| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.3                         |glm-5.3| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.2                         |glm-5.2| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.1                         |glm-5.1| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5                           |glm-5| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|基米K2。5|kimi-k2.5| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|基米K2。6|kimi-k2.6| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|基米K2.7代码|kimi-k2.7-代码| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|基米K3|基米-k3| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Mistral 大号 4|米斯特拉尔-大-4| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|杰夫 1.13|jev-1.13| `https://opencode.ai/zen/v1/systemone`                    | -                           |
|杰夫 1.13 免费|jev-1.13-免费| `https://opencode.ai/zen/v1/systemone`                    | -                           |
|大泡菜|大泡菜| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|太空兔子免费|无太空兔子| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|LongCat 2.5 免费预览|longcat-2.5-免预览| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|步骤 5 免费预览|步骤-5-免预览| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Exo免费|无外切酶| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|MiMo-V2.6-免Flash|mimo-v2.6-无闪存| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|MiMo-V2.5 免费|mimo-v2.5-免费| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|灵 3.1 免闪光|ling-3.1-无闪| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Ling 3.0 闪鳍免费|ling-3.0-无闪鳍| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Nemotron 3 超免费|nemotron-3-超自由| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Nemotron 3.5 无闪电|nemotron-3.5-无雷| `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
|Muse Spark 1.3 贡献者免费|muse-spark-1.3-无贡献者| `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |

的 [型号编号](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>) 在你的 OpenCode 配置
使用格式 `opencode/<model-id>`。例如，对于 GPT 5.5，你会
使用 `opencode/gpt-5.5` 在你的配置中。

---

### 型号

您可以从以下位置获取可用模型及其元数据的完整列表：

```
https://opencode.ai/zen/v1/models
```

---

### 杰夫

Jev 是 TypeSafe AI 的 System One 模型，用于快速、结构化决策。它不是生成文本，而是针对键入的问题评估 `state`，并返回代码可以直接使用的值和概率。它支持 yes/no (`noul`)、多项选择 (`choice`) 和基于评分标准 (`score`) 的问题。

将 OpenCode Zen API 密钥与 `https://opencode.ai/zen/v1/systemone` 端点结合使用。此示例检查支持请求是否紧急：

```bash
curl https://opencode.ai/zen/v1/systemone \
  -H "Authorization: Bearer $OPENCODE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jev-1.13",
    "state": "My payments have failed for three days and I am losing sales. Please help now.",
    "questions": {
      "is_urgent": {
        "type": "noul",
        "instructions": "Does this request require urgent attention?"
      }
    }
  }'
```

您可以在一项请求中提出多个问题。 Jev 并行评估它们并返回匹配问题 ID 下的每个答案：

```bash
curl https://opencode.ai/zen/v1/systemone \
  -H "Authorization: Bearer $OPENCODE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jev-1.13",
    "state": "My order arrived late and the item is damaged. I want a refund.",
    "questions": {
      "department": {
        "type": "choice",
        "instructions": "Which team should handle this request?",
        "criteria": {
          "returns": "Refunds, exchanges, or damaged items",
          "shipping": "Delivery delays or lost packages",
          "billing": "Charges, invoices, or payment problems"
        }
      },
      "frustration": {
        "type": "score",
        "instructions": "How frustrated is the customer?",
        "criteria": ["Calm", "Frustrated", "Very angry"]
      }
    }
  }'
```

使用`jev-1.13-free`而不是`jev-1.13`来使用限时免费模型。有关问题类型和回答字段的详细信息，请参阅 [TypeSafe AI 文档 ](<https://docs.typesafe.ai/>)。

---

## 定价

我们支持即用即付模式。以下是每 100 万代币 ** 的价格 **。

|型号|输入|输出|缓存读取|缓存写入|
| --------------------------------- | --------------- | --------------- | --------------- | ------------ |
|大泡菜|免费|免费|免费| -            |
|太空兔子免费|免费|免费|免费| -            |
|LongCat 2.5 免费预览|免费|免费|免费| -            |
|步骤 5 免费预览|免费|免费|免费| -            |
|Exo免费|免费|免费|免费| -            |
|MiMo-V2.6-免Flash|免费|免费|免费| -            |
|MiMo-V2.5 免费|免费|免费|免费| -            |
|灵 3.1 免闪光|免费|免费|免费| -            |
|Ling 3.0 闪鳍免费|免费|免费|免费| -            |
|Nemotron 3 超免费|免费|免费|免费| -            |
|Nemotron 3.5 无闪电|免费|免费|免费| -            |
|Muse Spark 1.3 贡献者免费|免费|免费|免费| -            |
|杰夫 1.13 免费|免费|免费| -               | -            |
|杰夫 1.13| $0.042          |免费| -               | -            |
| MiniMax M3                        | $0.30           | $1.20           | $0.06           | -            |
| MiniMax M2.7                      | $0.30           | $1.20           | $0.06           | -            |
| MiniMax M2.5                      | $0.30           | $1.20           | $0.06           | -            |
|GLM 5.3 闪光灯| $0.15           | $0.50           | $0.03           | -            |
| GLM 5.3                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5.2                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5.1                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5                             | $1.00           | $3.20           | $0.20           | -            |
|基米K2.7代码| $0.95           | $4.00           | $0.19           | -            |
|基米K3| $3.00           | $15.00          | $0.30           | -            |
|基米K2。6| $0.95           | $4.00           | $0.16           | -            |
|基米K2。5| $0.60           | $3.00           | $0.10           | -            |
|Mistral 大号 4（50% 关闭）| ~~$1.36~~ $0.68 | ~~$4.18~~ $2.09 | ~~$0.14~~ $0.07 | -            |
|Qwen3.8 最大| $2.00           | $6.00           | $0.25           | $2.50        |
|Qwen3.8 闪光| $0.15           | $0.47           | $0.016          | $0.20        |
|Qwen3.7 最大| $2.50           | $7.50           | $0.50           | $3.125       |
|Qwen3.7加| $0.40           | $1.60           | $0.04           | $0.50        |
|Qwen3.6加| $0.50           | $3.00           | $0.05           | $0.625       |
|Qwen3.5加| $0.20           | $1.20           | $0.02           | $0.25        |
|DeepSeek V4.1 闪存| $0.30           | $1.20           | $0.006          | -            |
|DeepSeek V4 专业版| $1.74           | $3.48           | $0.145          | -            |
|DeepSeek V4 闪光灯| $0.14           | $0.28           | $0.028          | -            |
|DeepSeek V4 Flash 视觉实验| $0.14           | $0.28           | $0.028          | -            |
|Claude 寓言 5.1| $10.00          | $50.00          | $0.25           | $12.50       |
|Claude 寓言 5| $10.00          | $50.00          | $1.00           | $12.50       |
|Claude 作品 5.5| $4.00           | $20.00          | $0.20           | $5.00        |
|Claude 作品 5| $5.00           | $25.00          | $0.50           | $6.25        |
|Claude 作品 4.8| $5.00           | $25.00          | $0.50           | $6.25        |
|Claude 作品 4.7| $5.00           | $25.00          | $0.50           | $6.25        |
|Claude 作品 4.6| $5.00           | $25.00          | $0.50           | $6.25        |
|Claude 作品 4.5| $5.00           | $25.00          | $0.50           | $6.25        |
|Claude 十四行诗 5.5| $2.00           | $10.00          | $0.20           | $2.50        |
|Claude 十四行诗 5| $2.00           | $10.00          | $0.20           | $2.50        |
|Claude 十四行诗 4.6| $3.00           | $15.00          | $0.30           | $3.75        |
|Claude Sonnet 4.5（≤ 200K 令牌）| $3.00           | $15.00          | $0.30           | $3.75        |
|Claude Sonnet 4.5（> 200K 代币）| $6.00           | $22.50          | $0.60           | $7.50        |
|Claude Haiku 5.5（≤ 100K 代币）| $0.10           | $0.50           | $0.01           | $0.125       |
|Claude Haiku 5.5（> 100K 代币）| $0.50           | $2.50           | $0.05           | $0.625       |
|Claude 俳句 4.5| $1.00           | $5.00           | $0.10           | $1.25        |
|Gemini 3.8 闪光灯| $1.50           | $7.50           | $0.15           | -            |
|Gemini 3.7 闪光灯| $1.50           | $7.50           | $0.15           | -            |
|Gemini 3.6 闪光灯| $1.50           | $7.50           | $0.15           | -            |
|Gemini 3.5 闪光灯| $1.50           | $9.00           | $0.15           | -            |
|Gemini 3.5 Flash Lite| $0.30           | $2.50           | $0.03           | -            |
|Gemini 3.1 Pro（≤ 200K 代币）| $2.00           | $12.00          | $0.20           | -            |
|Gemini 3.1 Pro（> 200K 代币）| $4.00           | $18.00          | $0.40           | -            |
|Gemini 3 闪光灯| $0.50           | $3.00           | $0.05           | -            |
|Grok 4.7（≤ 200K 代币）| $2.00           | $6.00           | $0.50           | -            |
|Grok 4.7（> 200K 代币）| $4.00           | $12.00          | $1.00           | -            |
|Grok 4.6（≤ 200K 代币）| $2.00           | $6.00           | $0.50           | -            |
|Grok 4.6（> 200K 代币）| $4.00           | $12.00          | $1.00           | -            |
|Grok 4.5（≤ 200K 代币）| $2.00           | $6.00           | $0.30           | -            |
|Grok 4.5（> 200K 代币）| $4.00           | $12.00          | $0.60           | -            |
|Grok 构建 0.1| $1.00           | $2.00           | $0.20           | -            |
|缪斯火花 1.3| $1.25           | $4.25           | $0.15           | -            |
|缪斯火花 1.2| $1.25           | $4.25           | $0.15           | -            |
|GPT 6 Astra（≤ 272K 代币）| $10.00          | $50.00          | $1.00           | $12.50       |
|GPT 6 Astra（> 272K 代币）| $20.00          | $75.00          | $2.00           | $25.00       |
|GPT 6 Sol（≤ 272K 代币）| $2.00           | $10.00          | $0.20           | $2.50        |
|GPT 6 Sol（> 272K 代币）| $4.00           | $15.00          | $0.40           | $5.00        |
|GPT 6.1 Sol（≤ 272K 代币）| $2.00           | $10.00          | $0.10           | $2.50        |
|GPT 6.1 Sol（> 272K 代币）| $4.00           | $15.00          | $0.20           | $5.00        |
|GPT 6 Luna（≤ 272K 代币）| $0.10           | $0.50           | $0.01           | $0.125       |
|GPT 6 Luna（> 272K 代币）| $0.20           | $0.75           | $0.02           | $0.25        |
|GPT 5.6 Sol（≤ 272K 代币）| $4.00           | $20.00          | $0.40           | $5.00        |
|GPT 5.6 Sol（> 272K 代币）| $8.00           | $30.00          | $0.80           | $10.00       |
|GPT 5.6 Terra（≤ 272K 代币）| $2.00           | $12.00          | $0.20           | $2.50        |
|GPT 5.6 Terra（> 272K 代币）| $4.00           | $18.00          | $0.40           | $5.00        |
|GPT 5.6 Luna（≤ 272K 代币）| $0.20           | $1.20           | $0.02           | $0.25        |
|GPT 5.6 Luna（> 272K 代币）| $0.40           | $1.80           | $0.04           | $0.50        |
|GPT 5.5（≤ 272K 代币）| $5.00           | $30.00          | $0.50           | -            |
|GPT 5.5（> 272K 代币）| $10.00          | $45.00          | $1.00           | -            |
|GPT 5.5 专业版| $30.00          | $180.00         | $30.00          | -            |
|GPT 5.4（≤ 272K 代币）| $2.50           | $15.00          | $0.25           | -            |
|GPT 5.4（> 272K 代币）| $5.00           | $22.50          | $0.50           | -            |
|GPT 5.4 专业版| $30.00          | $180.00         | $30.00          | -            |
|GPT 5.4 迷你| $0.75           | $4.50           | $0.075          | -            |
|GPT 5.4 纳米| $0.20           | $1.25           | $0.02           | -            |
|GPT 5.3 Codex 火花| $1.75           | $14.00          | $0.175          | -            |
| GPT 5.3 Codex                     | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.2                           | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.2 Codex                     | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.1                           | $1.07           | $8.50           | $0.107          | -            |
| GPT 5.1 Codex                     | $1.07           | $8.50           | $0.107          | -            |
|GPT 5.1 Codex 最大| $1.25           | $10.00          | $0.125          | -            |
|GPT 5.1 Codex 迷你| $0.25           | $2.00           | $0.025          | -            |
| GPT 5                             | $1.07           | $8.50           | $0.107          | -            |
| GPT 5 Codex                       | $1.07           | $8.50           | $0.107          | -            |
|GPT 5 纳米| $0.05           | $0.40           | $0.005          | -            |

**DeepSeek V4 Flash Vision Exp:** 图像根据其尺寸转换为令牌，并与文本令牌一起作为输入令牌计费。 [了解更多](<https://api-docs.deepseek.com/quick_start/pricing/>)。

您可能会在使用历史记录中注意到[低成本型号](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>)，例如Haiku、Nano或Flash。 OpenCode 使用这些模型来生成会话标题。

**注释：**

信用卡费用按成本转嫁（4.4% + $0.30 每笔交易）；除此之外我们不收取任何费用。

免费模型：

- Exo Free 已在 OpenCode 上限时发售。团队正在利用这段时间收集反馈并改进模型。
- MiMo-V2。6-OpenCode 限时提供 Flash Free。团队正在利用这段时间收集反馈并改进模型。
- MiMo-V2.5 OpenCode限时免费。团队正在利用这段时间收集反馈并改进模型。
- Ling 3.1 Flash Free 已在 OpenCode 上限时推出。团队正在利用这段时间收集反馈并改进模型。
- Ling 3.0 Flash Fin Free 已在 OpenCode 上限时发售。团队正在利用这段时间收集反馈并改进模型。
- Nemotron 3 Ultra Free 在 OpenCode 上限时提供。团队正在利用这段时间收集反馈并改进模型。
- Nemotron 3.5 Lightning Free 已在 OpenCode 上限时发售。团队正在利用这段时间收集反馈并改进模型。
- Big Pickle 是一个隐形模型，在 OpenCode 上限时免费。团队正在利用这段时间收集反馈并改进模型。
- Space Bunny Free 是一款隐形模型，在 OpenCode 上限时免费。其提供商遵循零保留政策，不会将您的数据用于模型训练。
- LongCat 2.5 免费预览在 OpenCode 上限时免费。其提供商遵循零保留政策，不会将您的数据用于模型训练。
- 步骤 5 免费预览在 OpenCode 上限时免费。其提供商遵循零保留政策，不会将您的数据用于模型训练。
- Muse Spark 1.3 Contributor Free 在 OpenCode 上限时提供。团队正在利用这段时间收集反馈并改进模型。
- Jev 1.13 免费在 OpenCode 上限时提供。

[如有任何疑问请联系我们](<mailto:help@anoma.ly>)。

---

### 自动重新加载

如果您的余额低于 $5，Zen 将自动重新充值 $20。

您可以更改自动充值金额。您还可以完全禁用自动重新加载。

---

### 每月限额

您还可以为整个工作区和团队中的每个 
 成员设置每月使用限制。

例如，假设您将每月使用限额设置为 $20,禅宗不会用
超过$20 一个月内。但如果你启用了自动重新加载，Zen 可能会结束
向您收取超过 $ 的费用20 如果您的余额低于 $5.

---

### 已弃用的型号

|型号|弃用日期|
| ------------------ | ----------------- |
| GPT 5.2 Codex      |七月 23、2026|
| GPT 5.1 Codex      |七月 23、2026|
|GPT 5.1 Codex 最大|七月 23、2026|
|GPT 5.1 Codex 迷你|七月 23、2026|
| GPT 5 Codex        |七月 23、2026|
|Claude 作品 4.1|八月 5、2026|
|Claude 十四行诗 4|六月 15、2026|
|Claude 俳句 3.5|二月 16、2026|
|Gemini 3 专业版|三月 9、2026|
| MiniMax M2.5       |八月 5、2026|
| MiniMax M2.1       |三月 15、2026|
| GLM 5              |5月14、2026|
| GLM 4.7            |三月 15、2026|
| GLM 4.6            |三月 15、2026|
|基米K2。5|八月 5、2026|
|Kimi K2 思考|三月 6、2026|
|基米K2|三月 6、2026|
|Qwen3编码器480B|二月 6、2026|

---

## 隐私

我们所有的模型都托管在 US 和 EU 中。我们的提供商遵循零保留政策，不会将您的数据用于模型训练，但以下情况除外：

- Big Pickle：在免费期间，收集的数据可用于改进模型。
- Exo Free：在免费期间，收集的数据可用于改进模型。
- MiMo-V2.6-Flash Free：在免费期间，收集的数据可用于改进模型。
- MiMo-V2.5 免费：在免费期间，收集的数据可用于改进模型。
- Ling 3.1 Flash 免费：在免费期间，收集的数据可用于改进模型。
- Ling 3.0 Flash Fin Free：免费期间，收集的数据可用于改进模型。
- Nemotron 3 Ultra Free（NVIDIA 免费端点）：仅供试用 - 请勿提交个人或机密数据。出于安全目的并改进 NVIDIA 产品和服务，我们会记录您的使用情况。出于改进目的而记录的会话数据不会与您的身份或任何持久标识符相关联。有关我们的数据处理实践的更多信息，请参阅我们的[隐私政策](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>)。通过与此端点交互，您同意我们收集、记录和使用此类信息以及[NVIDIA API试用服务条款](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>)。
- Nemotron 3.5 Lightning Free（NVIDIA 免费端点）：仅供试用 - 请勿提交个人或机密数据。出于安全目的并改进 NVIDIA 产品和服务，我们会记录您的使用情况。出于改进目的而记录的会话数据不会与您的身份或任何持久标识符相关联。有关我们的数据处理实践的更多信息，请参阅我们的[隐私政策](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>)。通过与此端点交互，您同意我们收集、记录和使用此类信息以及[NVIDIA API试用服务条款](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>)。
- OpenAI API：根据 [OpenAI 的数据政策 ](<https://platform.openai.com/docs/guides/your-data>)，请求将保留 30 天。
- Anthropic API：根据 [Anthropic 的数据政策 ](<https://docs.anthropic.com/en/docs/claude-code/data-usage>)，请求将保留 30 天。
- Mistral API：提示和其他输入不用于训练。数据根据[Mistral的数据保留政策](<https://docs.mistral.ai/admin/monitor-comply/zero-data-retention>)保留。
- Jev API：提示和其他输入不用于训练。数据根据[TypeSafeAI的隐私政策](<https://typesafe.ai/legal/privacy-policy>)保留。
- Muse Spark 1.3 免费贡献者：大幅折扣代币定价，以换取使用您的提示和完成来训练未来元模型的许可。 [了解更多](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>)。

---

## 对于团队

Zen 对团队也很有效。您可以邀请队友、分配角色、策划
您的团队使用的模型等等。

**注释：**

作为测试版的一部分，工作空间目前对团队免费。

作为测试版的一部分，管理工作空间目前对团队免费。我们将很快
分享有关定价的更多详细信息。

---

### 角色

您可以邀请团队成员到您的工作区并分配角色：

- **管理员**：管理模型、成员、API密钥和计费
- **成员**：仅管理自己的API密钥

管理员还可以为每个成员设置每月支出限额，以控制成本。

---

### 模型访问

管理员可以启用或禁用工作区的特定模型。对禁用模型发出的请求将返回错误。

这对于您想要禁用 
 收集数据的模型的使用非常有用。

---

### 带上你自己的钥匙

您可以使用自己的 OpenAI 或 Anthropic API 键，同时仍然访问 Zen 中的其他模型。

当您使用自己的密钥时，令牌将由提供商直接计费，而不是由 Zen。

例如，您的组织可能已经拥有 OpenAI 或 Anthropic
 的密钥，并且您希望使用该密钥而不是 Zen 提供的密钥。

---

## 目标

我们创建 OpenCode Zen 是为了：

1. **基准**编码agents的最佳models/providers。
2. 可以使用 ** 最高质量 ** 选项，而不会降低性能或转向更便宜的提供商。
3. 通过按成本价出售来传递任何**降价**；所以唯一的加价就是支付我们的加工费。
4. 允许您将其与任何其他编码 agent 一起使用，从而**无锁定**。并且始终允许您将任何其他提供商与 OpenCode 一起使用。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode Zen is a list of tested and verified models provided by the OpenCode team.

Zen works like any other provider in OpenCode. You login to OpenCode Zen and get
your API key. It's **completely optional** and you don't need to use it to use
OpenCode.

---

## Background

There are a large number of models out there but only a few of
these models work well as coding agents. Additionally, most providers are
configured very differently; so you get very different performance and quality.

**提示：**

We tested a select group of models and providers that work well with OpenCode.

So if you are using a model through something like OpenRouter, you can never be
sure if you are getting the best version of the model you want.

To fix this, we did a couple of things:

1. We tested a select group of models and talked to their teams about how to
   best run them.
2. We then worked with a few providers to make sure these were being served
   correctly.
3. Finally, we benchmarked the combination of the model/provider and came up
   with a list that we feel good recommending.

OpenCode Zen is an AI gateway that gives you access to these models.

---

## How it works

OpenCode Zen works like any other provider in OpenCode.

1. You sign in to **[OpenCode Zen](<https://opencode.ai/auth>)**, add your billing
   details, and copy your API key.
2. You run the `/connect` command in the TUI, select OpenCode Zen, and paste your API key.
3. Run `/models` in the TUI to see the list of models we recommend.

You are charged per request and you can add credits to your account.

---

## Endpoints

You can also access our models through the following API endpoints.

| Model                           | Model ID                        | Endpoint                                                  | AI SDK Package              |
| ------------------------------- | ------------------------------- | --------------------------------------------------------- | --------------------------- |
| GPT 6 Astra                     | gpt-6-astra                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 6 Sol                       | gpt-6-sol                       | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 6.1 Sol                     | gpt-6.1-sol                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 6 Luna                      | gpt-6-luna                      | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.6 Sol                     | gpt-5.6-sol                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.6 Terra                   | gpt-5.6-terra                   | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.6 Luna                    | gpt-5.6-luna                    | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.5                         | gpt-5.5                         | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.5 Pro                     | gpt-5.5-pro                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.4                         | gpt-5.4                         | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.4 Pro                     | gpt-5.4-pro                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.4 Mini                    | gpt-5.4-mini                    | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.4 Nano                    | gpt-5.4-nano                    | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.3 Codex                   | gpt-5.3-codex                   | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.3 Codex Spark             | gpt-5.3-codex-spark             | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.2                         | gpt-5.2                         | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.2 Codex                   | gpt-5.2-codex                   | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1                         | gpt-5.1                         | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1 Codex                   | gpt-5.1-codex                   | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1 Codex Max               | gpt-5.1-codex-max               | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5.1 Codex Mini              | gpt-5.1-codex-mini              | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5                           | gpt-5                           | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5 Codex                     | gpt-5-codex                     | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| GPT 5 Nano                      | gpt-5-nano                      | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Claude Fable 5.1                | claude-fable-5-1                | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Fable 5                  | claude-fable-5                  | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 5.5                 | claude-opus-5-5                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 5                   | claude-opus-5                   | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 4.8                 | claude-opus-4-8                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 4.7                 | claude-opus-4-7                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 4.6                 | claude-opus-4-6                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Opus 4.5                 | claude-opus-4-5                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Sonnet 5.5               | claude-sonnet-5-5               | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Sonnet 5                 | claude-sonnet-5                 | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Sonnet 4.6               | claude-sonnet-4-6               | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Sonnet 4.5               | claude-sonnet-4-5               | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Haiku 5.5                | claude-haiku-5-5                | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Claude Haiku 4.5                | claude-haiku-4-5                | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Gemini 3.8 Flash                | gemini-3.8-flash                | `https://opencode.ai/zen/v1/models/gemini-3.8-flash`      | `@ai-sdk/google`            |
| Gemini 3.7 Flash                | gemini-3.7-flash                | `https://opencode.ai/zen/v1/models/gemini-3.7-flash`      | `@ai-sdk/google`            |
| Gemini 3.6 Flash                | gemini-3.6-flash                | `https://opencode.ai/zen/v1/models/gemini-3.6-flash`      | `@ai-sdk/google`            |
| Gemini 3.5 Flash                | gemini-3.5-flash                | `https://opencode.ai/zen/v1/models/gemini-3.5-flash`      | `@ai-sdk/google`            |
| Gemini 3.5 Flash Lite           | gemini-3.5-flash-lite           | `https://opencode.ai/zen/v1/models/gemini-3.5-flash-lite` | `@ai-sdk/google`            |
| Gemini 3.1 Pro                  | gemini-3.1-pro                  | `https://opencode.ai/zen/v1/models/gemini-3.1-pro`        | `@ai-sdk/google`            |
| Gemini 3 Flash                  | gemini-3-flash                  | `https://opencode.ai/zen/v1/models/gemini-3-flash`        | `@ai-sdk/google`            |
| Grok 4.7                        | grok-4.7                        | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Grok 4.6                        | grok-4.6                        | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Grok 4.5                        | grok-4.5                        | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Grok Build 0.1                  | grok-build-0.1                  | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Muse Spark 1.3                  | muse-spark-1.3                  | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Muse Spark 1.2                  | muse-spark-1.2                  | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |
| Qwen3.8 Max                     | qwen3.8-max                     | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Qwen3.8 Flash                   | qwen3.8-flash                   | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Qwen3.7 Max                     | qwen3.7-max                     | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Qwen3.7 Plus                    | qwen3.7-plus                    | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Qwen3.6 Plus                    | qwen3.6-plus                    | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| Qwen3.5 Plus                    | qwen3.5-plus                    | `https://opencode.ai/zen/v1/messages`                     | `@ai-sdk/anthropic`         |
| DeepSeek V4.1 Flash             | deepseek-v4.1-flash             | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Pro                 | deepseek-v4-pro                 | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Flash               | deepseek-v4-flash               | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Flash Vision Exp    | deepseek-v4-flash-vision-exp    | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M3                      | minimax-m3                      | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M2.7                    | minimax-m2.7                    | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiniMax M2.5                    | minimax-m2.5                    | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.3 Flash                   | glm-5.3-flash                   | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.3                         | glm-5.3                         | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.2                         | glm-5.2                         | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5.1                         | glm-5.1                         | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| GLM 5                           | glm-5                           | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Kimi K2.5                       | kimi-k2.5                       | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Kimi K2.6                       | kimi-k2.6                       | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Kimi K2.7 Code                  | kimi-k2.7-code                  | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Kimi K3                         | kimi-k3                         | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Mistral Large 4                 | mistral-large-4                 | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Jev 1.13                        | jev-1.13                        | `https://opencode.ai/zen/v1/systemone`                    | -                           |
| Jev 1.13 Free                   | jev-1.13-free                   | `https://opencode.ai/zen/v1/systemone`                    | -                           |
| Big Pickle                      | big-pickle                      | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Space Bunny Free                | space-bunny-free                | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| LongCat 2.5 Preview Free        | longcat-2.5-preview-free        | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Step 5 Preview Free             | step-5-preview-free             | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Exo Free                        | exo-free                        | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiMo-V2.6-Flash Free            | mimo-v2.6-flash-free            | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| MiMo-V2.5 Free                  | mimo-v2.5-free                  | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Ling 3.1 Flash Free             | ling-3.1-flash-free             | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Ling 3.0 Flash Fin Free         | ling-3.0-flash-fin-free         | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Nemotron 3 Ultra Free           | nemotron-3-ultra-free           | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Nemotron 3.5 Lightning Free     | nemotron-3.5-lightning-free     | `https://opencode.ai/zen/v1/chat/completions`             | `@ai-sdk/openai-compatible` |
| Muse Spark 1.3 Contributor Free | muse-spark-1.3-contributor-free | `https://opencode.ai/zen/v1/responses`                    | `@ai-sdk/openai`            |

The [model id](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>) in your OpenCode config
uses the format `opencode/<model-id>`. For example, for GPT 5.5, you would
use `opencode/gpt-5.5` in your config.

---

### Models

You can fetch the full list of available models and their metadata from:

```
https://opencode.ai/zen/v1/models
```

---

### Jev

Jev is a System One model from TypeSafe AI for fast, structured decisions. Instead of generating text, it evaluates a `state` against typed questions and returns values and probabilities that your code can use directly. It supports yes/no (`noul`), multiple-choice (`choice`), and rubric-based (`score`) questions.

Use your OpenCode Zen API key with the `https://opencode.ai/zen/v1/systemone` endpoint. This example checks whether a support request is urgent:

```bash
curl https://opencode.ai/zen/v1/systemone \
  -H "Authorization: Bearer $OPENCODE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jev-1.13",
    "state": "My payments have failed for three days and I am losing sales. Please help now.",
    "questions": {
      "is_urgent": {
        "type": "noul",
        "instructions": "Does this request require urgent attention?"
      }
    }
  }'
```

You can ask multiple questions in one request. Jev evaluates them in parallel and returns each answer under the matching question ID:

```bash
curl https://opencode.ai/zen/v1/systemone \
  -H "Authorization: Bearer $OPENCODE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "jev-1.13",
    "state": "My order arrived late and the item is damaged. I want a refund.",
    "questions": {
      "department": {
        "type": "choice",
        "instructions": "Which team should handle this request?",
        "criteria": {
          "returns": "Refunds, exchanges, or damaged items",
          "shipping": "Delivery delays or lost packages",
          "billing": "Charges, invoices, or payment problems"
        }
      },
      "frustration": {
        "type": "score",
        "instructions": "How frustrated is the customer?",
        "criteria": ["Calm", "Frustrated", "Very angry"]
      }
    }
  }'
```

Use `jev-1.13-free` instead of `jev-1.13` to use the limited-time free model. See the [TypeSafe AI documentation](<https://docs.typesafe.ai/>) for details about question types and response fields.

---

## Pricing

We support a pay-as-you-go model. Below are the prices **per 1M tokens**.

| Model                             | Input           | Output          | Cached Read     | Cached Write |
| --------------------------------- | --------------- | --------------- | --------------- | ------------ |
| Big Pickle                        | Free            | Free            | Free            | -            |
| Space Bunny Free                  | Free            | Free            | Free            | -            |
| LongCat 2.5 Preview Free          | Free            | Free            | Free            | -            |
| Step 5 Preview Free               | Free            | Free            | Free            | -            |
| Exo Free                          | Free            | Free            | Free            | -            |
| MiMo-V2.6-Flash Free              | Free            | Free            | Free            | -            |
| MiMo-V2.5 Free                    | Free            | Free            | Free            | -            |
| Ling 3.1 Flash Free               | Free            | Free            | Free            | -            |
| Ling 3.0 Flash Fin Free           | Free            | Free            | Free            | -            |
| Nemotron 3 Ultra Free             | Free            | Free            | Free            | -            |
| Nemotron 3.5 Lightning Free       | Free            | Free            | Free            | -            |
| Muse Spark 1.3 Contributor Free   | Free            | Free            | Free            | -            |
| Jev 1.13 Free                     | Free            | Free            | -               | -            |
| Jev 1.13                          | $0.042          | Free            | -               | -            |
| MiniMax M3                        | $0.30           | $1.20           | $0.06           | -            |
| MiniMax M2.7                      | $0.30           | $1.20           | $0.06           | -            |
| MiniMax M2.5                      | $0.30           | $1.20           | $0.06           | -            |
| GLM 5.3 Flash                     | $0.15           | $0.50           | $0.03           | -            |
| GLM 5.3                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5.2                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5.1                           | $1.40           | $4.40           | $0.26           | -            |
| GLM 5                             | $1.00           | $3.20           | $0.20           | -            |
| Kimi K2.7 Code                    | $0.95           | $4.00           | $0.19           | -            |
| Kimi K3                           | $3.00           | $15.00          | $0.30           | -            |
| Kimi K2.6                         | $0.95           | $4.00           | $0.16           | -            |
| Kimi K2.5                         | $0.60           | $3.00           | $0.10           | -            |
| Mistral Large 4 (50% off)         | ~~$1.36~~ $0.68 | ~~$4.18~~ $2.09 | ~~$0.14~~ $0.07 | -            |
| Qwen3.8 Max                       | $2.00           | $6.00           | $0.25           | $2.50        |
| Qwen3.8 Flash                     | $0.15           | $0.47           | $0.016          | $0.20        |
| Qwen3.7 Max                       | $2.50           | $7.50           | $0.50           | $3.125       |
| Qwen3.7 Plus                      | $0.40           | $1.60           | $0.04           | $0.50        |
| Qwen3.6 Plus                      | $0.50           | $3.00           | $0.05           | $0.625       |
| Qwen3.5 Plus                      | $0.20           | $1.20           | $0.02           | $0.25        |
| DeepSeek V4.1 Flash               | $0.30           | $1.20           | $0.006          | -            |
| DeepSeek V4 Pro                   | $1.74           | $3.48           | $0.145          | -            |
| DeepSeek V4 Flash                 | $0.14           | $0.28           | $0.028          | -            |
| DeepSeek V4 Flash Vision Exp      | $0.14           | $0.28           | $0.028          | -            |
| Claude Fable 5.1                  | $10.00          | $50.00          | $0.25           | $12.50       |
| Claude Fable 5                    | $10.00          | $50.00          | $1.00           | $12.50       |
| Claude Opus 5.5                   | $4.00           | $20.00          | $0.20           | $5.00        |
| Claude Opus 5                     | $5.00           | $25.00          | $0.50           | $6.25        |
| Claude Opus 4.8                   | $5.00           | $25.00          | $0.50           | $6.25        |
| Claude Opus 4.7                   | $5.00           | $25.00          | $0.50           | $6.25        |
| Claude Opus 4.6                   | $5.00           | $25.00          | $0.50           | $6.25        |
| Claude Opus 4.5                   | $5.00           | $25.00          | $0.50           | $6.25        |
| Claude Sonnet 5.5                 | $2.00           | $10.00          | $0.20           | $2.50        |
| Claude Sonnet 5                   | $2.00           | $10.00          | $0.20           | $2.50        |
| Claude Sonnet 4.6                 | $3.00           | $15.00          | $0.30           | $3.75        |
| Claude Sonnet 4.5 (≤ 200K tokens) | $3.00           | $15.00          | $0.30           | $3.75        |
| Claude Sonnet 4.5 (> 200K tokens) | $6.00           | $22.50          | $0.60           | $7.50        |
| Claude Haiku 5.5 (≤ 100K tokens)  | $0.10           | $0.50           | $0.01           | $0.125       |
| Claude Haiku 5.5 (> 100K tokens)  | $0.50           | $2.50           | $0.05           | $0.625       |
| Claude Haiku 4.5                  | $1.00           | $5.00           | $0.10           | $1.25        |
| Gemini 3.8 Flash                  | $1.50           | $7.50           | $0.15           | -            |
| Gemini 3.7 Flash                  | $1.50           | $7.50           | $0.15           | -            |
| Gemini 3.6 Flash                  | $1.50           | $7.50           | $0.15           | -            |
| Gemini 3.5 Flash                  | $1.50           | $9.00           | $0.15           | -            |
| Gemini 3.5 Flash Lite             | $0.30           | $2.50           | $0.03           | -            |
| Gemini 3.1 Pro (≤ 200K tokens)    | $2.00           | $12.00          | $0.20           | -            |
| Gemini 3.1 Pro (> 200K tokens)    | $4.00           | $18.00          | $0.40           | -            |
| Gemini 3 Flash                    | $0.50           | $3.00           | $0.05           | -            |
| Grok 4.7 (≤ 200K tokens)          | $2.00           | $6.00           | $0.50           | -            |
| Grok 4.7 (> 200K tokens)          | $4.00           | $12.00          | $1.00           | -            |
| Grok 4.6 (≤ 200K tokens)          | $2.00           | $6.00           | $0.50           | -            |
| Grok 4.6 (> 200K tokens)          | $4.00           | $12.00          | $1.00           | -            |
| Grok 4.5 (≤ 200K tokens)          | $2.00           | $6.00           | $0.30           | -            |
| Grok 4.5 (> 200K tokens)          | $4.00           | $12.00          | $0.60           | -            |
| Grok Build 0.1                    | $1.00           | $2.00           | $0.20           | -            |
| Muse Spark 1.3                    | $1.25           | $4.25           | $0.15           | -            |
| Muse Spark 1.2                    | $1.25           | $4.25           | $0.15           | -            |
| GPT 6 Astra (≤ 272K tokens)       | $10.00          | $50.00          | $1.00           | $12.50       |
| GPT 6 Astra (> 272K tokens)       | $20.00          | $75.00          | $2.00           | $25.00       |
| GPT 6 Sol (≤ 272K tokens)         | $2.00           | $10.00          | $0.20           | $2.50        |
| GPT 6 Sol (> 272K tokens)         | $4.00           | $15.00          | $0.40           | $5.00        |
| GPT 6.1 Sol (≤ 272K tokens)       | $2.00           | $10.00          | $0.10           | $2.50        |
| GPT 6.1 Sol (> 272K tokens)       | $4.00           | $15.00          | $0.20           | $5.00        |
| GPT 6 Luna (≤ 272K tokens)        | $0.10           | $0.50           | $0.01           | $0.125       |
| GPT 6 Luna (> 272K tokens)        | $0.20           | $0.75           | $0.02           | $0.25        |
| GPT 5.6 Sol (≤ 272K tokens)       | $4.00           | $20.00          | $0.40           | $5.00        |
| GPT 5.6 Sol (> 272K tokens)       | $8.00           | $30.00          | $0.80           | $10.00       |
| GPT 5.6 Terra (≤ 272K tokens)     | $2.00           | $12.00          | $0.20           | $2.50        |
| GPT 5.6 Terra (> 272K tokens)     | $4.00           | $18.00          | $0.40           | $5.00        |
| GPT 5.6 Luna (≤ 272K tokens)      | $0.20           | $1.20           | $0.02           | $0.25        |
| GPT 5.6 Luna (> 272K tokens)      | $0.40           | $1.80           | $0.04           | $0.50        |
| GPT 5.5 (≤ 272K tokens)           | $5.00           | $30.00          | $0.50           | -            |
| GPT 5.5 (> 272K tokens)           | $10.00          | $45.00          | $1.00           | -            |
| GPT 5.5 Pro                       | $30.00          | $180.00         | $30.00          | -            |
| GPT 5.4 (≤ 272K tokens)           | $2.50           | $15.00          | $0.25           | -            |
| GPT 5.4 (> 272K tokens)           | $5.00           | $22.50          | $0.50           | -            |
| GPT 5.4 Pro                       | $30.00          | $180.00         | $30.00          | -            |
| GPT 5.4 Mini                      | $0.75           | $4.50           | $0.075          | -            |
| GPT 5.4 Nano                      | $0.20           | $1.25           | $0.02           | -            |
| GPT 5.3 Codex Spark               | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.3 Codex                     | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.2                           | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.2 Codex                     | $1.75           | $14.00          | $0.175          | -            |
| GPT 5.1                           | $1.07           | $8.50           | $0.107          | -            |
| GPT 5.1 Codex                     | $1.07           | $8.50           | $0.107          | -            |
| GPT 5.1 Codex Max                 | $1.25           | $10.00          | $0.125          | -            |
| GPT 5.1 Codex Mini                | $0.25           | $2.00           | $0.025          | -            |
| GPT 5                             | $1.07           | $8.50           | $0.107          | -            |
| GPT 5 Codex                       | $1.07           | $8.50           | $0.107          | -            |
| GPT 5 Nano                        | $0.05           | $0.40           | $0.005          | -            |

**DeepSeek V4 Flash Vision Exp:** Images are converted into tokens based on their dimensions and billed as input tokens alongside text tokens. [Learn more](<https://api-docs.deepseek.com/quick_start/pricing/>).

You may notice [low-cost models](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>), such as Haiku, Nano, or Flash, in your usage history. OpenCode uses these models to generate session titles.

**注释：**

Credit card fees are passed along at cost (4.4% + $0.30 per transaction); we don't charge anything beyond that.

The free models:

- Exo Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- MiMo-V2.6-Flash Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- MiMo-V2.5 Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Ling 3.1 Flash Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Ling 3.0 Flash Fin Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Nemotron 3 Ultra Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Nemotron 3.5 Lightning Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Big Pickle is a stealth model that's free on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Space Bunny Free is a stealth model that's free on OpenCode for a limited time. Its provider follows a zero-retention policy and does not use your data for model training.
- LongCat 2.5 Preview Free is free on OpenCode for a limited time. Its provider follows a zero-retention policy and does not use your data for model training.
- Step 5 Preview Free is free on OpenCode for a limited time. Its provider follows a zero-retention policy and does not use your data for model training.
- Muse Spark 1.3 Contributor Free is available on OpenCode for a limited time. The team is using this time to collect feedback and improve the model.
- Jev 1.13 Free is available on OpenCode for a limited time.

[Contact us](<mailto:help@anoma.ly>) if you have any questions.

---

### Auto-reload

If your balance goes below $5, Zen will automatically reload $20.

You can change the auto-reload amount. You can also disable auto-reload entirely.

---

### Monthly limits

You can also set a monthly usage limit for the entire workspace and for each
member of your team.

For example, let's say you set a monthly usage limit to $20, Zen will not use
more than $20 in a month. But if you have auto-reload enabled, Zen might end up
charging you more than $20 if your balance goes below $5.

---

### Deprecated models

| Model              | Deprecation date  |
| ------------------ | ----------------- |
| GPT 5.2 Codex      | July 23, 2026     |
| GPT 5.1 Codex      | July 23, 2026     |
| GPT 5.1 Codex Max  | July 23, 2026     |
| GPT 5.1 Codex Mini | July 23, 2026     |
| GPT 5 Codex        | July 23, 2026     |
| Claude Opus 4.1    | August 5, 2026    |
| Claude Sonnet 4    | June 15, 2026     |
| Claude Haiku 3.5   | February 16, 2026 |
| Gemini 3 Pro       | March 9, 2026     |
| MiniMax M2.5       | August 5, 2026    |
| MiniMax M2.1       | March 15, 2026    |
| GLM 5              | May 14, 2026      |
| GLM 4.7            | March 15, 2026    |
| GLM 4.6            | March 15, 2026    |
| Kimi K2.5          | August 5, 2026    |
| Kimi K2 Thinking   | March 6, 2026     |
| Kimi K2            | March 6, 2026     |
| Qwen3 Coder 480B   | February 6, 2026  |

---

## Privacy

All our models are hosted in the US and EU. Our providers follow a zero-retention policy and do not use your data for model training, with the following exceptions:

- Big Pickle: During its free period, collected data may be used to improve the model.
- Exo Free: During its free period, collected data may be used to improve the model.
- MiMo-V2.6-Flash Free: During its free period, collected data may be used to improve the model.
- MiMo-V2.5 Free: During its free period, collected data may be used to improve the model.
- Ling 3.1 Flash Free: During its free period, collected data may be used to improve the model.
- Ling 3.0 Flash Fin Free: During its free period, collected data may be used to improve the model.
- Nemotron 3 Ultra Free (NVIDIA free endpoints): Trial use only — do not submit personal or confidential data. Your use is logged for security purposes and to improve NVIDIA products and services. The logged session data for improvement purposes is not linked to your identity or any persistent identifier. For more information about our data processing practices, see our [Privacy Policy](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>). By interacting with this endpoint, you consent to our collection, recording, and use of such information and the [NVIDIA API Trial Terms of Service](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>).
- Nemotron 3.5 Lightning Free (NVIDIA free endpoints): Trial use only — do not submit personal or confidential data. Your use is logged for security purposes and to improve NVIDIA products and services. The logged session data for improvement purposes is not linked to your identity or any persistent identifier. For more information about our data processing practices, see our [Privacy Policy](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>). By interacting with this endpoint, you consent to our collection, recording, and use of such information and the [NVIDIA API Trial Terms of Service](<https://assets.ngc.nvidia.com/products/api-catalog/legal/NVIDIA%20API%20Trial%20Terms%20of%20Service.pdf>).
- OpenAI APIs: Requests are retained for 30 days in accordance with [OpenAI's Data Policies](<https://platform.openai.com/docs/guides/your-data>).
- Anthropic APIs: Requests are retained for 30 days in accordance with [Anthropic's Data Policies](<https://docs.anthropic.com/en/docs/claude-code/data-usage>).
- Mistral APIs: Prompts and other inputs are not used for training. Data is retained in accordance with [Mistral's Data Retention Policy](<https://docs.mistral.ai/admin/monitor-comply/zero-data-retention>).
- Jev APIs: Prompts and other inputs are not used for training. Data is retained in accordance with [TypeSafe AI's Privacy Policy](<https://typesafe.ai/legal/privacy-policy>).
- Muse Spark 1.3 Contributor Free: Heavily discounted token pricing in exchange for permission to use your prompts and completions to train future Meta models. [Learn more](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>).

---

## For Teams

Zen also works great for teams. You can invite teammates, assign roles, curate
the models your team uses, and more.

**注释：**

Workspaces are currently free for teams as a part of the beta.

Managing your workspace is currently free for teams as a part of the beta. We'll be
sharing more details on the pricing soon.

---

### Roles

You can invite teammates to your workspace and assign roles:

- **Admin**: Manage models, members, API keys, and billing
- **Member**: Manage only their own API keys

Admins can also set monthly spending limits for each member to keep costs under control.

---

### Model access

Admins can enable or disable specific models for the workspace. Requests made to a disabled model will return an error.

This is useful for cases where you want to disable the use of a model that
collects data.

---

### Bring your own key

You can use your own OpenAI or Anthropic API keys while still accessing other models in Zen.

When you use your own keys, tokens are billed directly by the provider, not by Zen.

For example, your organization might already have a key for OpenAI or Anthropic
and you want to use that instead of the one that Zen provides.

---

## Goals

We created OpenCode Zen to:

1. **Benchmark** the best models/providers for coding agents.
2. Have access to the **highest quality** options and not downgrade performance or route to cheaper providers.
3. Pass along any **price drops** by selling at cost; so the only markup is to cover our processing fees.
4. Have **no lock-in** by allowing you to use it with any other coding agent. And always let you use any other provider with OpenCode as well.


</details>
