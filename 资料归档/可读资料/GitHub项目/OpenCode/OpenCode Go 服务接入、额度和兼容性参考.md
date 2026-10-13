# OpenCode Go 服务接入、额度和兼容性参考

服务参考。介绍 Go 套餐、API 接入、验证过的客户端、额度、超限和隐私条件，帮助理解模型服务选型；价格与限制只代表存档时的说明。

- 来源：OpenCode
- 资料类型：服务参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/anomalyco/opencode/commit/03146dab56b2c0cec7b6fafd1d8fad2750627256>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：具有时效性的套餐与额度信息不作为当前购买依据。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/go.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode Go 服务接入、额度和兼容性参考.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Go
description: Reliable access to open coding models with two usage tiers.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode Go 让您可以可靠地访问流行的开放编码模型，并提供两个月度计划：

|计划|价格|包含的用途|
| ----------- | ------------- | ------------------------------------------- |
| **Go**      |**$10/月**|以较低成本访问以下型号|
|**Go 加**|**$40/月**|以下型号的使用限制更高|

Go 与 OpenCode 中的任何其他提供商一样工作。您订阅 OpenCode Go 并 
 获取您的 API 密钥。它是**完全可选的**，您不需要使用它来
使用OpenCode。

该服务主要面向国际用户，提供稳定的全球访问。

---

## 背景

开放模型已经变得非常好。它们现在的性能接近于编码任务的 
 专有模型。而且由于许多提供商可以提供有竞争力的服务
，因此它们通常要便宜得多。

然而，获得可靠、低延迟的访问它们可能很困难。提供商
的质量和可用性各不相同。

**提示：**

我们测试了一组与 OpenCode 配合良好的模型和提供程序。

为了解决这个问题，我们做了几件事：

1. 我们测试了一组选定的开放模型，并与他们的团队讨论了如何
最好运行它们。
2. 然后我们与一些提供商合作以确保这些服务得到服务
正确。
3. 最后，我们对 model/provider 的组合进行了基准测试，得出了
并附上一份我们觉得不错的推荐清单。

Go 和 Go Plus 都提供对这些模型的访问；选择适合
您使用量的计划。

---

## 它是如何运作的

OpenCode Go 与 OpenCode 中的任何其他提供商一样工作。

1. 您登录**[OpenCode控制台](<https://opencode.ai/auth>)**，订阅Go或Go Plus，并且
复制您的 API 密钥。
2. 您在TUI中运行`/connect`命令，选择`OpenCode Go`，然后粘贴
你的 API 键。
3. Run `/models` in the TUI to see the list of models available through Go.

**注释：**

每个工作区只有一名成员可以订阅 OpenCode Go 或 Go Plus。

目前的型号列表包括：

- **神乐 4.7**
- **神乐 4.6**
- **GLM-5.3-闪光**
- **GLM-5.3**
- **GLM-5.2**
- **GPT 6 露娜**
- **GPT 5.6 露娜**
- **Claude 俳句 5.5**
- **基米 K3**
- **基米K2.7代码**
- **基米K2。6**
- **LongCat-2.0**
- **LongCat 2.5 免费预览**（限时）
- **步骤5免费预览**（限时）
- **MiMo-V2.6-闪存**
- **MiMo-V2.6-Pro**
- **MiMo-V2.5**
- **MiMo-V2.5-Pro**
- **MiniMax M3**
- **MiniMax M2.7**
- **Muse Spark 1.3 贡献者**（[有限区域](<https://ai.developer.meta.com/legal/geographic-use-policy>)）
- **Muse Spark 1.2 贡献者**（[有限区域](<https://ai.developer.meta.com/legal/geographic-use-policy>)）
- **Qwen3.8 最大**
- **Qwen3.8 闪光**
- **Qwen3.7加**
- **DeepSeek V4.1 闪存**
- **DeepSeek V4 专业版**
- **DeepSeek V4 闪光灯**
- **DeepSeek V4 闪光视觉实验**
- **Hy4预览**
- **Hy3**
- **太空兔子**

当我们测试和添加新模型时，模型列表可能会发生变化。

---

## 我可以在哪里使用它？

OpenCode Go 是为 [OpenCode](<https://opencode.ai>) 和其他产生类似请求类型的编码 agents
 设计的。监控流量是否存在滥用情况，
会降低其他用户的体验。

您的客户应该：

1. 发送典型编码agent流量
2. 用自己的用户agent来标识自己，例如`my-coding-agent/1.0`，而不是
比通用的 SDK 或 HTTP- 库名称。
3. 为每个对话在 `x-opencode-session` 中发送稳定会话 ID，以便我们可以优化路由和
提示缓存。

### 经过验证的客户

Besides OpenCode, the following clients have been validated to work properly
with OpenCode Go.虽然我们不保证他们将来会继续工作。

|客户|会话支持|
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|**爱马仕**|包含 [PR #101864](<https://github.com/NousResearch/hermes-agent/pull/101864>) 的构建在主和辅助 OpenCode 请求上发送标头。该修复已在 v0.21.0 之后合并；仅该版本不包括它。|
| **Claude Code**   |Go recognizes its native session header.不需要自定义标头包装器。|
| **Codex**         |Go 识别其本机会话标头。某些版本和代理设置仍然忽略它；转发请求时保留会话标头。|
|**Z代码**| Go 识别其本机会话标头。我们的 [要求 `x-opencode-session`](<https://github.com/zai-org/feedback/issues/492>) 保持打开状态，但不再需要发送该特定标头。                                   |
|**Pi**|当前版本发送 OpenCode 的会话信息。更新旧安装。|
|**jcode**|更新到 **v0.81.6 或更高版本 **，其中包括 [ 会话标头修复 ](<https://github.com/1jehuang/jcode/issues/1167>)。|
|**公斤代码 CLI**|包含 [PR #13752](<https://github.com/Kilo-Org/kilocode/pull/13752>) 的构建恢复 OpenCode 会话标头。此修复涵盖 CLI，而不是 VS Code 扩展。请参阅[问题#13723](<https://github.com/Kilo-Org/kilocode/issues/13723>)。|

### 已知有问题的客户

这些客户端在我们
调查的版本中缺少或不完整的会话支持。链接的报告跟踪修复和解决方法。

|客户|状态和跟踪|
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|**DeepSeek 安全带**|会话信息到达某些模型路径，但在其他路径上丢失。我们识别出它的原生标头；剩下的工作是将其发送到所有适配器。 [讨论#5495](<https://github.com/deepseek-ai/deepseek-harness/discussions/5495>)。|
|**GitHub Copilot 聊天**|[VS Code 问题 #334186](<https://github.com/microsoft/vscode/issues/334186>) 中请求自动会话标头支持。|
|**基米码**|[问题#3506](<https://github.com/MoonshotAI/kimi-code/issues/3506>)中请求自动会话标头支持。|
|**MiMo 代码**|[问题#2317](<https://github.com/XiaomiMiMo/MiMo-Code/issues/2317>)在[PR#2327](<https://github.com/XiaomiMiMo/MiMo-Code/pull/2327>)中提出了修复方案，但尚未合并。|

## 使用限制

使用限制定义为每月的美元金额。下表显示了每个计划的
每月限额以及每个模型的代币成本。 Go 和 Go Plus 的代币定价为 
 相同。

每个型号都有以下使用限制：5小时 — 20% 每月限制；
每周 — 50%；以及每月 — 100%。

下面每个型号的每月限额决定了其使用量如何计入这些限额。

代币价格为每 100 万个代币。

#### Go

|型号|输入|输出|缓存读取|缓存写入|每月限额|
| --------------------------------------- | ------ | ------ | ----------- | ------------ | ---------------------------------------------- |
|GLM-5.3-闪存| $0.15  | $0.50  | $0.03       | -            | **$60**                                        |
| GLM-5.3                                 | $1.40  | $4.40  | $0.26       | -            | **$15**                                        |
| GLM-5.2                                 | $1.40  | $4.40  | $0.26       | -            | **$60**                                        |
|基米K3| $3.00  | $15.00 | $0.30       | -            | **$15**                                        |
|基米K2.7代码| $0.95  | $4.00  | $0.19       | -            | **$60**                                        |
|基米K2。6| $0.95  | $4.00  | $0.16       | -            | **$60**                                        |
| LongCat-2.0                             | $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
|LongCat 2.5 免费预览|免费|免费|免费| -            |**无限制**<br>限时|
|步骤 5 免费预览|免费|免费|免费| -            |**无限制**<br>限时|
|MiMo-V2.6-闪存| $0.14  | $0.28  | $0.0028     | -            | **$60**                                        |
|MiMo-V2.6-Pro| $0.435 | $0.87  | $0.003625   | -            | **$15**                                        |
| MiMo-V2.5                               | $0.14  | $0.28  | $0.0028     | -            | **$60**                                        |
|MiMo-V2.5-Pro| $0.435 | $0.87  | $0.003625   | -            | **$15**                                        |
| MiniMax M3                              | $0.30  | $1.20  | $0.06       | -            | **$60**                                        |
| MiniMax M2.7                            | $0.30  | $1.20  | $0.06       | $0.375       | **$60**                                        |
|Muse Spark 1.3 贡献者| $0.10  | $0.20  | $0.002      | -            | **$60**                                        |
|Muse Spark 1.2 贡献者| $0.10  | $0.20  | $0.002      | -            | **$60**                                        |
|Qwen3.8 最大| $2.00  | $6.00  | $0.25       | $2.50        | **$15**                                        |
|Qwen3.8 闪光| $0.15  | $0.47  | $0.016      | $0.20        | **$30**                                        |
|Qwen3.7 Plus（≤ 256K 代币）| $0.40  | $1.60  | $0.04       | $0.50        | **$60**                                        |
|Qwen3.7 Plus（> 256K 代币）| $1.20  | $4.80  | $0.12       | $1.50        | **$60**                                        |
|DeepSeek V4.1 闪光灯（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$60**                                        |
|DeepSeek V4.1 闪光（峰值）| $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
|DeepSeek V4 Pro（非高峰期）| $0.66  | $1.98  | $0.022      | -            | **$15**                                        |
|DeepSeek V4 Pro（峰值）| $1.32  | $3.96  | $0.044      | -            | **$15**                                        |
|DeepSeek V4 闪光灯（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$30**                                        |
|DeepSeek V4 闪光（峰值）| $0.30  | $1.20  | $0.006      | -            | **$30**                                        |
|DeepSeek V4 Flash Vision Exp（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$15**                                        |
|DeepSeek V4 Flash Vision Exp（峰值）| $0.30  | $1.20  | $0.006      | -            | **$15**                                        |
|Hy4 预览| $0.834 | $2.501 | $0.042      | -            | **$30**                                        |
|HY3| $0.14  | $0.58  | $0.035      | -            | **$60**                                        |
|太空兔| $0.15  | $0.60  | $0.03       | -            | **$30**                                        |
|Grok 4.7（≤ 200K 代币）| $2.00  | $6.00  | $0.50       | -            | **$15**                                        |
|Grok 4.7（> 200K 代币）| $4.00  | $12.00 | $1.00       | -            | **$15**                                        |
|Grok 4.6（≤ 200K 代币）| $2.00  | $6.00  | $0.50       | -            | **$15**                                        |
|Grok 4.6（> 200K 代币）| $4.00  | $12.00 | $1.00       | -            | **$15**                                        |
|GPT 6 Luna（≤ 272K 代币）| $0.10  | $0.50  | $0.01       | $0.125       | **$15**                                        |
|GPT 6 Luna（> 272K 代币）| $0.20  | $0.75  | $0.02       | $0.25        | **$15**                                        |
|GPT 5.6 Luna（≤ 272K 代币）| $0.20  | $1.20  | $0.02       | $0.25        | **$15**                                        |
|GPT 5.6 Luna（> 272K 代币）| $0.40  | $1.80  | $0.04       | $0.50        | **$15**                                        |
|Claude Haiku 5.5（≤ 100K 代币）| $0.10  | $0.50  | $0.01       | $0.125       | **$15**                                        |
|Claude Haiku 5.5（> 100K 代币）| $0.50  | $2.50  | $0.05       | $0.625       | **$15**                                        |

#### Go 加

|型号|输入|输出|缓存读取|缓存写入|每月限额|
| --------------------------------------- | ------ | ------ | ----------- | ------------ | ---------------------------------------------- |
|GLM-5.3-闪存| $0.15  | $0.50  | $0.03       | -            | **$180**                                       |
| GLM-5.3                                 | $1.40  | $4.40  | $0.26       | -            | **$120**                                       |
| GLM-5.2                                 | $1.40  | $4.40  | $0.26       | -            | **$180**                                       |
|基米K3| $3.00  | $15.00 | $0.30       | -            | **$60**                                        |
|基米K2.7代码| $0.95  | $4.00  | $0.19       | -            | **$180**                                       |
|基米K2。6| $0.95  | $4.00  | $0.16       | -            | **$240**                                       |
| LongCat-2.0                             | $0.30  | $1.20  | $0.006      | -            | **$240**                                       |
|LongCat 2.5 免费预览|免费|免费|免费| -            |**无限制**<br>限时|
|步骤 5 免费预览|免费|免费|免费| -            |**无限制**<br>限时|
|MiMo-V2.6-闪存| $0.14  | $0.28  | $0.0028     | -            | **$120**                                       |
|MiMo-V2.6-Pro| $0.435 | $0.87  | $0.003625   | -            | **$60**                                        |
| MiMo-V2.5                               | $0.14  | $0.28  | $0.0028     | -            | **$120**                                       |
|MiMo-V2.5-Pro| $0.435 | $0.87  | $0.003625   | -            | **$60**                                        |
| MiniMax M3                              | $0.30  | $1.20  | $0.06       | -            | **$180**                                       |
| MiniMax M2.7                            | $0.30  | $1.20  | $0.06       | $0.375       | **$240**                                       |
|Muse Spark 1.3 贡献者| $0.10  | $0.20  | $0.002      | -            | **$120**                                       |
|Muse Spark 1.2 贡献者| $0.10  | $0.20  | $0.002      | -            | **$120**                                       |
|Qwen3.8 最大| $2.00  | $6.00  | $0.25       | $2.50        | **$60**                                        |
|Qwen3.8 闪光| $0.15  | $0.47  | $0.016      | $0.20        | **$90**                                        |
|Qwen3.7 Plus（≤ 256K 代币）| $0.40  | $1.60  | $0.04       | $0.50        | **$180**                                       |
|Qwen3.7 Plus（> 256K 代币）| $1.20  | $4.80  | $0.12       | $1.50        | **$180**                                       |
|DeepSeek V4.1 闪光灯（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$120**                                       |
|DeepSeek V4.1 闪光（峰值）| $0.30  | $1.20  | $0.006      | -            | **$120**                                       |
|DeepSeek V4 Pro（非高峰期）| $0.66  | $1.98  | $0.022      | -            | **$60**                                        |
|DeepSeek V4 Pro（峰值）| $1.32  | $3.96  | $0.044      | -            | **$60**                                        |
|DeepSeek V4 闪光灯（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$120**                                       |
|DeepSeek V4 闪光（峰值）| $0.30  | $1.20  | $0.006      | -            | **$120**                                       |
|DeepSeek V4 Flash Vision Exp（非高峰）| $0.15  | $0.60  | $0.003      | -            | **$60**                                        |
|DeepSeek V4 Flash Vision Exp（峰值）| $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
|Hy4 预览| $0.834 | $2.501 | $0.042      | -            | **$120**                                       |
|HY3| $0.14  | $0.58  | $0.035      | -            | **$240**                                       |
|太空兔| $0.15  | $0.60  | $0.03       | -            | **$120**                                       |
|Grok 4.7（≤ 200K 代币）| $2.00  | $6.00  | $0.50       | -            | **$60**                                        |
|Grok 4.7（> 200K 代币）| $4.00  | $12.00 | $1.00       | -            | **$60**                                        |
|Grok 4.6（≤ 200K 代币）| $2.00  | $6.00  | $0.50       | -            | **$60**                                        |
|Grok 4.6（> 200K 代币）| $4.00  | $12.00 | $1.00       | -            | **$60**                                        |
|GPT 6 Luna（≤ 272K 代币）| $0.10  | $0.50  | $0.01       | $0.125       | **$60**                                        |
|GPT 6 Luna（> 272K 代币）| $0.20  | $0.75  | $0.02       | $0.25        | **$60**                                        |
|GPT 5.6 Luna（≤ 272K 代币）| $0.20  | $1.20  | $0.02       | $0.25        | **$60**                                        |
|GPT 5.6 Luna（> 272K 代币）| $0.40  | $1.80  | $0.04       | $0.50        | **$60**                                        |
|Claude Haiku 5.5（≤ 100K 代币）| $0.10  | $0.50  | $0.01       | $0.125       | **$60**                                        |
|Claude Haiku 5.5（> 100K 代币）| $0.50  | $2.50  | $0.05       | $0.625       | **$60**                                        |

**LongCat 2.5 免费预览：** 限时免费。

**步骤5免费预览：**限时免费。

**DeepSeek V4.1 Flash / V4 Pro / V4 Flash / V4 Flash Vision Exp:** 高峰时段为01:00-04:00 和 06:00-10:00 UTC，星期一至星期五；所有其他时间（包括周末）均为非高峰时段。 [了解更多](<https://api-docs.deepseek.com/quick_start/pricing/>)。

**DeepSeek V4 Flash Vision Exp:** 图像根据其尺寸转换为令牌，并与文本令牌一起作为输入令牌计费。 [了解更多](<https://api-docs.deepseek.com/quick_start/pricing/>)。

### 预计请求数

The tables below estimate request counts based on typical Go usage patterns. Go
加上每个型号的较高使用限制的估计比例。

#### Go

|型号|每 5 小时的请求|每周请求数|每月请求数|
| ---------------------------- | -------------------- | ----------------- | ------------------ |
|GLM-5.3-闪存| 6,320                | 15,790            | 31,580             |
| GLM-5.3                      | 220                  | 540               | 1,080              |
| GLM-5.2                      | 880                  | 2,150             | 4,300              |
|基米K3| 110                  | 250               | 490                |
|基米K2.7代码| 1,350                | 3,380             | 6,750              |
|基米K2。6| 1,150                | 2,880             | 5,750              |
| LongCat-2.0                  | 11,400               | 28,600            | 57,200             |
|LongCat 2.5 免费预览|无限|无限|无限|
|步骤 5 免费预览|无限|无限|无限|
|MiMo-V2.6-闪存| 30,100               | 75,200            | 150,400            |
|MiMo-V2.6-Pro| 3,250                | 8,150             | 16,300             |
| MiMo-V2.5                    | 30,100               | 75,200            | 150,400            |
|MiMo-V2.5-Pro| 3,250                | 8,150             | 16,300             |
| MiniMax M3                   | 3,200                | 8,000             | 16,000             |
| MiniMax M2.7                 | 3,400                | 8,500             | 17,000             |
|Muse Spark 1.3 贡献者| 45,300               | 113,300           | 226,600            |
|Muse Spark 1.2 贡献者| 45,300               | 113,300           | 226,600            |
|Qwen3.8 最大| 160                  | 400               | 810                |
|Qwen3.8 闪光| 5,400                | 13,500            | 27,000             |
|Qwen3.7加| 4,300                | 10,800            | 21,600             |
|DeepSeek V4.1 闪存| 26,000               | 65,000            | 130,000            |
|DeepSeek V4 专业版| 1,050                | 2,600             | 5,200              |
|DeepSeek V4 闪光灯| 13,000               | 32,500            | 65,000             |
|DeepSeek V4 Flash 视觉实验| 6,500                | 16,250            | 32,500             |
|Hy4 预览| 1,350                | 3,380             | 6,770              |
|HY3| 4,300                | 10,750            | 21,500             |
|太空兔| 3,130                | 7,810             | 15,630             |
|格洛克 4.7| 169                  | 423               | 845                |
|格洛克 4.6| 169                  | 423               | 845                |
|GPT 6 露娜| 4,230                | 10,560            | 21,130             |
|GPT 5.6 露娜| 2,050                | 5,100             | 10,250             |
|Claude 俳句 5.5| 3,850                | 9,620             | 19,230             |

#### Go 加

|型号|每 5 小时的请求|每周请求数|每月请求数|
| ---------------------------- | -------------------- | ----------------- | ------------------ |
|GLM-5.3-闪存| 18,960               | 47,370            | 94,740             |
| GLM-5.3                      | 1,760                | 4,320             | 8,640              |
| GLM-5.2                      | 2,640                | 6,450             | 12,900             |
|基米K3| 440                  | 1,000             | 1,960              |
|基米K2.7代码| 4,050                | 10,140            | 20,250             |
|基米K2。6| 4,600                | 11,520            | 23,000             |
| LongCat-2.0                  | 45,600               | 114,400           | 228,800            |
|LongCat 2.5 免费预览|无限|无限|无限|
|步骤 5 免费预览|无限|无限|无限|
|MiMo-V2.6-闪存| 60,200               | 150,400           | 300,800            |
|MiMo-V2.6-Pro| 13,000               | 32,600            | 65,200             |
| MiMo-V2.5                    | 60,200               | 150,400           | 300,800            |
|MiMo-V2.5-Pro| 13,000               | 32,600            | 65,200             |
| MiniMax M3                   | 9,600                | 24,000            | 48,000             |
| MiniMax M2.7                 | 13,600               | 34,000            | 68,000             |
|Muse Spark 1.3 贡献者| 90,600               | 226,600           | 453,200            |
|Muse Spark 1.2 贡献者| 90,600               | 226,600           | 453,200            |
|Qwen3.8 最大| 640                  | 1,600             | 3,240              |
|Qwen3.8 闪光| 16,200               | 40,500            | 81,000             |
|Qwen3.7加| 12,900               | 32,400            | 64,800             |
|DeepSeek V4.1 闪存| 52,000               | 130,000           | 260,000            |
|DeepSeek V4 专业版| 4,200                | 10,400            | 20,800             |
|DeepSeek V4 闪光灯| 52,000               | 130,000           | 260,000            |
|DeepSeek V4 Flash 视觉实验| 26,000               | 65,000            | 130,000            |
|Hy4 预览| 5,400                | 13,520            | 27,080             |
|HY3| 17,200               | 43,000            | 86,000             |
|太空兔| 12,500               | 31,250            | 62,500             |
|格洛克 4.7| 676                  | 1,692             | 3,380              |
|格洛克 4.6| 676                  | 1,692             | 3,380              |
|GPT 6 露娜| 16,920               | 42,240            | 84,520             |
|GPT 5.6 露娜| 8,200                | 20,400            | 41,000             |
|Claude 俳句 5.5| 15,380               | 38,460            | 76,920             |

该估计使用每个请求的以下令牌计数；实际使用情况有所不同。

- Grok 4.7/4.6 — 390 输入，32,500 缓存，120 每个请求的输出令牌
- GLM-5.3-Flash — 1,000 输入，55,000 缓存，200 每个请求的输出令牌
- GLM-5.3/5.2 - 700 输入，52,000 缓存，150 每个请求的输出令牌
- GPT 6 Luna — 1,000 输入，50,000 缓存，220 每个请求的输出令牌
- GPT 5.6 Luna — 1,000 输入，50,000 缓存，220 每个请求的输出令牌
- Claude Haiku 5.5 — 1,100 输入，55,000 缓存，240 每个请求的输出令牌
- Kimi K3 - 1,050 输入，76,500 缓存，300 每个请求的输出令牌
- Kimi K2.7/K2.6 - 870 输入，55,000 缓存，200 每个请求的输出令牌
- LongCat-2.0 — 920 输入，88,900 缓存，200 每个请求的输出令牌
- DeepSeek V4.1 闪存 - 每个请求的 410 输入、71,300 缓存、310 输出令牌
- DeepSeek V4 Pro — 750 输入，82,000 缓存，290 每个请求的输出令牌
- DeepSeek V4 闪存 — 410 输入，71,300 缓存，310 每个请求的输出令牌
- DeepSeek V4 Flash Vision Exp — 每个请求的 410 输入、71,300 缓存、310 输出令牌
- MiniMax M3 — 510 输入，56,000 缓存，190 每个请求的输出令牌
- MiniMax M2.7 — 300 输入，55,000 缓存，125 每个请求的输出令牌
- Muse Spark 1.3 贡献者 — 620 输入，71,400 缓存，300 每个请求的输出令牌
- Muse Spark 1.2 贡献者 — 620 输入，71,400 缓存，300 每个请求的输出令牌
- MiMo-V2.6-Flash — 830 输入，71,500 缓存，295 每个请求的输出令牌
- MiMo-V2.6-Pro — 每个请求的 790 输入、86,000 缓存、305 输出令牌
- MiMo-V2.5 — 830 输入，71,500 缓存，295 每个请求的输出令牌
- MiMo-V2.5-Pro — 每个请求的 790 输入、86,000 缓存、305 输出令牌
- Qwen3.8 Max — 每个请求的 420 输入、66,000 缓存、200 输出令牌
- Qwen3.8 闪存 — 600 输入，58,000 缓存，200 每个请求的输出令牌
- Qwen3.7 Plus — 500 输入，57,000 缓存，190 每个请求的输出令牌
- Hy4 预览 — 830 输入，71,500 缓存，295 每个请求输出令牌
- Hy3 — 830 输入，71,500 缓存，295 每个请求的输出令牌
- Space Bunny — 1,000 输入，55,000 缓存，200 每个请求输出令牌

您可以在**[控制台](<https://opencode.ai/auth>)**中跟踪您当前的使用情况。

**提示：**

如果达到使用限制，您可以继续使用免费模型。

随着我们从早期使用和反馈中了解，使用限制可能会发生变化。

---

### 使用超出限制

如果您的 Zen 余额上还有积分，您可以在控制台中启用 **使用余额 **
 选项。启用后，Go 在您达到使用限制后将回退到您的 Zen 余额 
，而不是阻止请求。

---

### 为什么有些型号的使用率较低

对于Go，包含的每月使用量因型号而异。

对于大多数型号，我们通过批量折扣和预留 GPU 容量来实现此目的。然后，我们会将这些节省的费用作为更高的每月使用量转嫁给您。

对于某些型号，我们没有机会协商折扣或以较低的成本托管它们，要么是因为型号是新的，要么是因为它们的公开定价已经打折。

对于这些模型，您仍然比直接向模型提供商付款要多一点；这就是为什么它们包含的每月使用量较低的原因。

---

## 端点

您还可以通过以下 API 端点访问 Go 模型。

|型号|型号【ID】|端点|AI SDK 封装|
| ---------------------------- | ---------------------------- | ------------------------------------------------ | --------------------------- |
|格洛克 4.7|grok-4.7| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|格洛克 4.6|grok-4.6| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|GPT 6 露娜|gpt-6-luna| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|GPT 5.6 露娜|gpt-5.6-luna| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|Claude 俳句 5.5|克劳德俳句-5-5| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
|GLM-5.3-闪存|glm-5.3-flash| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| GLM-5.3                      |glm-5.3| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| GLM-5.2                      |glm-5.2| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|基米K3|基米-k3| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|基米K2.7代码|kimi-k2.7-代码| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|基米K2。6|kimi-k2.6| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| LongCat-2.0                  |长猫-2.0| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|LongCat 2.5 免费预览|longcat-2.5-免预览| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|步骤 5 免费预览|步骤-5-免预览| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|DeepSeek V4.1 闪存|deepseek-v4.1-flash| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|DeepSeek V4 专业版|deepseek-v4-pro| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|DeepSeek V4 闪光灯|deepseek-v4-flash| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|DeepSeek V4 Flash 视觉实验|deepseek-v4-flash-vision-exp| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|MiMo-V2.6-闪存|mimo-v2.6-闪存| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|MiMo-V2.6-Pro|mimo-v2.6-pro| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiMo-V2.5                    |mimo-v2.5| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|MiMo-V2.5-Pro|mimo-v2.5-pro| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiniMax M3                   |极小极大-m3| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| MiniMax M2.7                 |极小极大-m2。7| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
|Muse Spark 1.3 贡献者|muse-spark-1.3-贡献者| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|Muse Spark 1.2 贡献者|muse-spark-1.2-贡献者| `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
|Qwen3.8 最大|qwen3.8-最大| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
|Qwen3.8 闪光|qwen3.8-flash| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
|Qwen3.7加|qwen3.7-plus| `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
|Hy4 预览|hy4-预览| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|HY3|hy3| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
|太空兔|太空兔子| `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |

的 [型号编号](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>) 在你的 OpenCode 配置
使用格式 `opencode-go/<model-id>`。例如，对于基米 K3，你会
使用 `opencode-go/kimi-k3` 在你的配置中。

---

### 型号

您可以从以下位置获取可用模型及其元数据的完整列表：

```
https://opencode.ai/zen/go/v1/models
```

---

## 隐私

|型号|模型训练|数据保留|
| ---------------------------- | -------------- | -------------- |
|格洛克 4.7|未使用|30 天|
|格洛克 4.6|未使用|30 天|
|GPT 6 露娜|未使用|30 天|
|GPT 5.6 露娜|未使用|30 天|
|Claude 俳句 5.5|未使用|30 天|
|GLM-5.3-闪存|未使用|0 天|
| GLM-5.3                      |未使用|0 天|
| GLM-5.2                      |未使用|0 天|
|基米K3|未使用|0 天|
|基米K2.7代码|未使用|0 天|
|基米K2。6|未使用|0 天|
| LongCat-2.0                  |未使用|0 天|
|MiMo-V2.6-Pro|未使用|0 天|
|MiMo-V2.6-闪存|未使用|0 天|
|MiMo-V2.5-Pro|未使用|0 天|
| MiMo-V2.5                    |未使用|0 天|
|Qwen3.8 最大|未使用|0 天|
|Qwen3.8 闪光|未使用|0 天|
|Qwen3.7加|未使用|0 天|
| MiniMax M3                   |未使用|0 天|
| MiniMax M2.7                 |未使用|0 天|
|Muse Spark 1.3 贡献者|是的|不是 ZDR|
|Muse Spark 1.2 贡献者|是的|不是 ZDR|
|DeepSeek V4.1 闪存|未使用|0 天\*|
|DeepSeek V4 专业版|未使用|0 天\*|
|DeepSeek V4 闪光灯|未使用|0 天\*|
|DeepSeek V4 Flash 视觉实验|未使用|0 天\*|
|Hy4 预览|未使用|0 天|
|HY3|未使用|0 天|
|太空兔|未使用|0 天|
|LongCat 2.5 免费预览|未使用|0 天|
|步骤 5 免费预览|未使用|0 天|

- **Grok 4.7/4.6:** ZDR 禁用依赖于存储数据的重要 API 功能，包括有状态响应 API、文件和集合以及批处理API。 [了解更多](<https://docs.x.ai/developers/faq/security#what-is-zero-data-retention-zdr>)。
- **GPT 6 Luna / GPT 5.6 Luna:** 为所有 API 功能使用情况生成滥用监控日志，并保留最多 30 天。 [了解更多](<https://developers.openai.com/api/docs/guides/your-data#data-retention-controls-for-abuse-monitoring>)。
- **Claude Haiku 5.5：** 根据 [Anthropic 的数据政策 ](<https://docs.anthropic.com/en/docs/claude-code/data-usage>)，请求将保留 30 天。
- **Muse Spark 1.3 贡献者：** 代币定价大幅折扣，以换取使用您的提示和完成来训练未来元模型的许可。可用性仅限于 Meta 的 [地理使用政策 ](<https://ai.developer.meta.com/legal/geographic-use-policy>) 允许的区域。 [了解更多](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>)。
- **Muse Spark 1.2 贡献者：** 代币定价大幅折扣，以换取使用您的提示和完成来训练未来元模型的许可。可用性仅限于 Meta 的 [地理使用政策 ](<https://ai.developer.meta.com/legal/geographic-use-policy>) 允许的区域。 [了解更多](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>)。
- **DeepSeek：** ZDR 协议每月续订一次。当前协议有效期至 10 月 31、2026。

---

## 目标

我们创建 OpenCode Go 是为了：

1. 让更多人以低成本订阅 AI 编码 ** 可以访问 **。
2. 提供**可靠**访问最佳开放编码模型的机会。
3. 策划经过 ** 测试和基准测试 ** 的模型，以供编码 agent 使用。
4. 通过允许您将任何其他提供商与 OpenCode 一起使用，**不会锁定**。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode Go gives you reliable access to popular open coding models, with two monthly plans:

| Plan        | Price         | Included usage                              |
| ----------- | ------------- | ------------------------------------------- |
| **Go**      | **$10/month** | Lower-cost access to the models below       |
| **Go Plus** | **$40/month** | Higher usage limits across the models below |

Go works like any other provider in OpenCode. You subscribe to OpenCode Go and
get your API key. It's **completely optional** and you don't need to use it to
use OpenCode.

The service is designed primarily for international users and provides stable global access.

---

## Background

Open models have gotten really good. They now reach performance close to
proprietary models for coding tasks. And because many providers can serve them
competitively, they are usually far cheaper.

However, getting reliable, low latency access to them can be difficult. Providers
vary in quality and availability.

**提示：**

We tested a select group of models and providers that work well with OpenCode.

To fix this, we did a couple of things:

1. We tested a select group of open models and talked to their teams about how to
   best run them.
2. We then worked with a few providers to make sure these were being served
   correctly.
3. Finally, we benchmarked the combination of the model/provider and came up
   with a list that we feel good recommending.

Both Go and Go Plus provide access to these models; choose the plan that fits
how much you use them.

---

## How it works

OpenCode Go works like any other provider in OpenCode.

1. You sign in to **[OpenCode Console](<https://opencode.ai/auth>)**, subscribe to Go or Go Plus, and
   copy your API key.
2. You run the `/connect` command in the TUI, select `OpenCode Go`, and paste
   your API key.
3. Run `/models` in the TUI to see the list of models available through Go.

**注释：**

Only one member per workspace can subscribe to OpenCode Go or Go Plus.

The current list of models includes:

- **Grok 4.7**
- **Grok 4.6**
- **GLM-5.3-Flash**
- **GLM-5.3**
- **GLM-5.2**
- **GPT 6 Luna**
- **GPT 5.6 Luna**
- **Claude Haiku 5.5**
- **Kimi K3**
- **Kimi K2.7 Code**
- **Kimi K2.6**
- **LongCat-2.0**
- **LongCat 2.5 Preview Free** (limited time)
- **Step 5 Preview Free** (limited time)
- **MiMo-V2.6-Flash**
- **MiMo-V2.6-Pro**
- **MiMo-V2.5**
- **MiMo-V2.5-Pro**
- **MiniMax M3**
- **MiniMax M2.7**
- **Muse Spark 1.3 Contributor** ([limited regions](<https://ai.developer.meta.com/legal/geographic-use-policy>))
- **Muse Spark 1.2 Contributor** ([limited regions](<https://ai.developer.meta.com/legal/geographic-use-policy>))
- **Qwen3.8 Max**
- **Qwen3.8 Flash**
- **Qwen3.7 Plus**
- **DeepSeek V4.1 Flash**
- **DeepSeek V4 Pro**
- **DeepSeek V4 Flash**
- **DeepSeek V4 Flash Vision Exp**
- **Hy4 preview**
- **Hy3**
- **Space Bunny**

The list of models may change as we test and add new ones.

---

## Where can I use it?

OpenCode Go is designed for [OpenCode](<https://opencode.ai>) and other coding agents
that produce similar types of requests. Traffic is monitored for abuse that
degrades the experience for other users.

Your client should:

1. Send typical coding agent traffic
2. Identify itself with its own user agent, such as `my-coding-agent/1.0`, rather
   than a generic SDK or HTTP-library name.
3. Send a stable session ID in `x-opencode-session` for each conversation so we can optimize routing and
   prompt caching.

### Validated Clients

Besides OpenCode, the following clients have been validated to work properly
with OpenCode Go. Although we do not guarantee that they will continue to work in the future.

| Client            | Session support                                                                                                                                                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Hermes**        | Builds containing [PR #101864](<https://github.com/NousResearch/hermes-agent/pull/101864>) send the header on main and auxiliary OpenCode requests. The fix was merged after v0.21.0; that release alone does not include it.                 |
| **Claude Code**   | Go recognizes its native session header. No custom-header wrapper is needed.                                                                                                                                                                |
| **Codex**         | Go recognizes its native session header. Some versions and proxy setups still omit it; preserve the session header when forwarding requests.                                                                                                |
| **ZCode**         | Go recognizes its native session header. Our [request for `x-opencode-session`](<https://github.com/zai-org/feedback/issues/492>) remains open, but it is no longer necessary to send that specific header.                                   |
| **Pi**            | Current builds send session information for OpenCode. Update older installations.                                                                                                                                                           |
| **jcode**         | Update to **v0.81.6 or later**, which includes the [session-header fix](<https://github.com/1jehuang/jcode/issues/1167>).                                                                                                                     |
| **Kilo Code CLI** | Builds containing [PR #13752](<https://github.com/Kilo-Org/kilocode/pull/13752>) restore OpenCode session headers. This fix covers the CLI, not the VS Code extension. See [issue #13723](<https://github.com/Kilo-Org/kilocode/issues/13723>). |

### Known Problematic Clients

These clients have missing or incomplete session support in the versions we
investigated. The linked reports track fixes and workarounds.

| Client                  | Status and tracking                                                                                                                                                                                                                                    |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **DeepSeek Harness**    | Session information arrives on some model paths, but is missing on others. We recognize its native header; the remaining work is to send it across all adapters. [Discussion #5495](<https://github.com/deepseek-ai/deepseek-harness/discussions/5495>). |
| **GitHub Copilot Chat** | Automatic session-header support is requested in [VS Code issue #334186](<https://github.com/microsoft/vscode/issues/334186>).                                                                                                                           |
| **Kimi Code**           | Automatic session-header support is requested in [issue #3506](<https://github.com/MoonshotAI/kimi-code/issues/3506>).                                                                                                                                   |
| **MiMo Code**           | [Issue #2317](<https://github.com/XiaomiMiMo/MiMo-Code/issues/2317>) has a proposed fix in [PR #2327](<https://github.com/XiaomiMiMo/MiMo-Code/pull/2327>), which has not yet merged.                                                                      |

## Usage limits

Usage limits are defined as monthly dollar amounts. The table below shows the
monthly limit for each plan and the token costs for each model. Token pricing is
the same for Go and Go Plus.

Each model has the following usage limits: 5-hour — 20% of the monthly limit;
weekly — 50%; and monthly — 100%.

Each model's monthly limit below determines how its usage counts toward those allowances.

Token prices are per 1M tokens.

#### Go

| Model                                   | Input  | Output | Cached Read | Cached Write | Monthly limit                                  |
| --------------------------------------- | ------ | ------ | ----------- | ------------ | ---------------------------------------------- |
| GLM-5.3-Flash                           | $0.15  | $0.50  | $0.03       | -            | **$60**                                        |
| GLM-5.3                                 | $1.40  | $4.40  | $0.26       | -            | **$15**                                        |
| GLM-5.2                                 | $1.40  | $4.40  | $0.26       | -            | **$60**                                        |
| Kimi K3                                 | $3.00  | $15.00 | $0.30       | -            | **$15**                                        |
| Kimi K2.7 Code                          | $0.95  | $4.00  | $0.19       | -            | **$60**                                        |
| Kimi K2.6                               | $0.95  | $4.00  | $0.16       | -            | **$60**                                        |
| LongCat-2.0                             | $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
| LongCat 2.5 Preview Free                | Free   | Free   | Free        | -            | **Unlimited**<br>limited time |
| Step 5 Preview Free                     | Free   | Free   | Free        | -            | **Unlimited**<br>limited time |
| MiMo-V2.6-Flash                         | $0.14  | $0.28  | $0.0028     | -            | **$60**                                        |
| MiMo-V2.6-Pro                           | $0.435 | $0.87  | $0.003625   | -            | **$15**                                        |
| MiMo-V2.5                               | $0.14  | $0.28  | $0.0028     | -            | **$60**                                        |
| MiMo-V2.5-Pro                           | $0.435 | $0.87  | $0.003625   | -            | **$15**                                        |
| MiniMax M3                              | $0.30  | $1.20  | $0.06       | -            | **$60**                                        |
| MiniMax M2.7                            | $0.30  | $1.20  | $0.06       | $0.375       | **$60**                                        |
| Muse Spark 1.3 Contributor              | $0.10  | $0.20  | $0.002      | -            | **$60**                                        |
| Muse Spark 1.2 Contributor              | $0.10  | $0.20  | $0.002      | -            | **$60**                                        |
| Qwen3.8 Max                             | $2.00  | $6.00  | $0.25       | $2.50        | **$15**                                        |
| Qwen3.8 Flash                           | $0.15  | $0.47  | $0.016      | $0.20        | **$30**                                        |
| Qwen3.7 Plus (≤ 256K tokens)            | $0.40  | $1.60  | $0.04       | $0.50        | **$60**                                        |
| Qwen3.7 Plus (> 256K tokens)            | $1.20  | $4.80  | $0.12       | $1.50        | **$60**                                        |
| DeepSeek V4.1 Flash (Off-Peak)          | $0.15  | $0.60  | $0.003      | -            | **$60**                                        |
| DeepSeek V4.1 Flash (Peak)              | $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
| DeepSeek V4 Pro (Off-Peak)              | $0.66  | $1.98  | $0.022      | -            | **$15**                                        |
| DeepSeek V4 Pro (Peak)                  | $1.32  | $3.96  | $0.044      | -            | **$15**                                        |
| DeepSeek V4 Flash (Off-Peak)            | $0.15  | $0.60  | $0.003      | -            | **$30**                                        |
| DeepSeek V4 Flash (Peak)                | $0.30  | $1.20  | $0.006      | -            | **$30**                                        |
| DeepSeek V4 Flash Vision Exp (Off-Peak) | $0.15  | $0.60  | $0.003      | -            | **$15**                                        |
| DeepSeek V4 Flash Vision Exp (Peak)     | $0.30  | $1.20  | $0.006      | -            | **$15**                                        |
| Hy4 preview                             | $0.834 | $2.501 | $0.042      | -            | **$30**                                        |
| Hy3                                     | $0.14  | $0.58  | $0.035      | -            | **$60**                                        |
| Space Bunny                             | $0.15  | $0.60  | $0.03       | -            | **$30**                                        |
| Grok 4.7 (≤ 200K tokens)                | $2.00  | $6.00  | $0.50       | -            | **$15**                                        |
| Grok 4.7 (> 200K tokens)                | $4.00  | $12.00 | $1.00       | -            | **$15**                                        |
| Grok 4.6 (≤ 200K tokens)                | $2.00  | $6.00  | $0.50       | -            | **$15**                                        |
| Grok 4.6 (> 200K tokens)                | $4.00  | $12.00 | $1.00       | -            | **$15**                                        |
| GPT 6 Luna (≤ 272K tokens)              | $0.10  | $0.50  | $0.01       | $0.125       | **$15**                                        |
| GPT 6 Luna (> 272K tokens)              | $0.20  | $0.75  | $0.02       | $0.25        | **$15**                                        |
| GPT 5.6 Luna (≤ 272K tokens)            | $0.20  | $1.20  | $0.02       | $0.25        | **$15**                                        |
| GPT 5.6 Luna (> 272K tokens)            | $0.40  | $1.80  | $0.04       | $0.50        | **$15**                                        |
| Claude Haiku 5.5 (≤ 100K tokens)        | $0.10  | $0.50  | $0.01       | $0.125       | **$15**                                        |
| Claude Haiku 5.5 (> 100K tokens)        | $0.50  | $2.50  | $0.05       | $0.625       | **$15**                                        |

#### Go Plus

| Model                                   | Input  | Output | Cached Read | Cached Write | Monthly limit                                  |
| --------------------------------------- | ------ | ------ | ----------- | ------------ | ---------------------------------------------- |
| GLM-5.3-Flash                           | $0.15  | $0.50  | $0.03       | -            | **$180**                                       |
| GLM-5.3                                 | $1.40  | $4.40  | $0.26       | -            | **$120**                                       |
| GLM-5.2                                 | $1.40  | $4.40  | $0.26       | -            | **$180**                                       |
| Kimi K3                                 | $3.00  | $15.00 | $0.30       | -            | **$60**                                        |
| Kimi K2.7 Code                          | $0.95  | $4.00  | $0.19       | -            | **$180**                                       |
| Kimi K2.6                               | $0.95  | $4.00  | $0.16       | -            | **$240**                                       |
| LongCat-2.0                             | $0.30  | $1.20  | $0.006      | -            | **$240**                                       |
| LongCat 2.5 Preview Free                | Free   | Free   | Free        | -            | **Unlimited**<br>limited time |
| Step 5 Preview Free                     | Free   | Free   | Free        | -            | **Unlimited**<br>limited time |
| MiMo-V2.6-Flash                         | $0.14  | $0.28  | $0.0028     | -            | **$120**                                       |
| MiMo-V2.6-Pro                           | $0.435 | $0.87  | $0.003625   | -            | **$60**                                        |
| MiMo-V2.5                               | $0.14  | $0.28  | $0.0028     | -            | **$120**                                       |
| MiMo-V2.5-Pro                           | $0.435 | $0.87  | $0.003625   | -            | **$60**                                        |
| MiniMax M3                              | $0.30  | $1.20  | $0.06       | -            | **$180**                                       |
| MiniMax M2.7                            | $0.30  | $1.20  | $0.06       | $0.375       | **$240**                                       |
| Muse Spark 1.3 Contributor              | $0.10  | $0.20  | $0.002      | -            | **$120**                                       |
| Muse Spark 1.2 Contributor              | $0.10  | $0.20  | $0.002      | -            | **$120**                                       |
| Qwen3.8 Max                             | $2.00  | $6.00  | $0.25       | $2.50        | **$60**                                        |
| Qwen3.8 Flash                           | $0.15  | $0.47  | $0.016      | $0.20        | **$90**                                        |
| Qwen3.7 Plus (≤ 256K tokens)            | $0.40  | $1.60  | $0.04       | $0.50        | **$180**                                       |
| Qwen3.7 Plus (> 256K tokens)            | $1.20  | $4.80  | $0.12       | $1.50        | **$180**                                       |
| DeepSeek V4.1 Flash (Off-Peak)          | $0.15  | $0.60  | $0.003      | -            | **$120**                                       |
| DeepSeek V4.1 Flash (Peak)              | $0.30  | $1.20  | $0.006      | -            | **$120**                                       |
| DeepSeek V4 Pro (Off-Peak)              | $0.66  | $1.98  | $0.022      | -            | **$60**                                        |
| DeepSeek V4 Pro (Peak)                  | $1.32  | $3.96  | $0.044      | -            | **$60**                                        |
| DeepSeek V4 Flash (Off-Peak)            | $0.15  | $0.60  | $0.003      | -            | **$120**                                       |
| DeepSeek V4 Flash (Peak)                | $0.30  | $1.20  | $0.006      | -            | **$120**                                       |
| DeepSeek V4 Flash Vision Exp (Off-Peak) | $0.15  | $0.60  | $0.003      | -            | **$60**                                        |
| DeepSeek V4 Flash Vision Exp (Peak)     | $0.30  | $1.20  | $0.006      | -            | **$60**                                        |
| Hy4 preview                             | $0.834 | $2.501 | $0.042      | -            | **$120**                                       |
| Hy3                                     | $0.14  | $0.58  | $0.035      | -            | **$240**                                       |
| Space Bunny                             | $0.15  | $0.60  | $0.03       | -            | **$120**                                       |
| Grok 4.7 (≤ 200K tokens)                | $2.00  | $6.00  | $0.50       | -            | **$60**                                        |
| Grok 4.7 (> 200K tokens)                | $4.00  | $12.00 | $1.00       | -            | **$60**                                        |
| Grok 4.6 (≤ 200K tokens)                | $2.00  | $6.00  | $0.50       | -            | **$60**                                        |
| Grok 4.6 (> 200K tokens)                | $4.00  | $12.00 | $1.00       | -            | **$60**                                        |
| GPT 6 Luna (≤ 272K tokens)              | $0.10  | $0.50  | $0.01       | $0.125       | **$60**                                        |
| GPT 6 Luna (> 272K tokens)              | $0.20  | $0.75  | $0.02       | $0.25        | **$60**                                        |
| GPT 5.6 Luna (≤ 272K tokens)            | $0.20  | $1.20  | $0.02       | $0.25        | **$60**                                        |
| GPT 5.6 Luna (> 272K tokens)            | $0.40  | $1.80  | $0.04       | $0.50        | **$60**                                        |
| Claude Haiku 5.5 (≤ 100K tokens)        | $0.10  | $0.50  | $0.01       | $0.125       | **$60**                                        |
| Claude Haiku 5.5 (> 100K tokens)        | $0.50  | $2.50  | $0.05       | $0.625       | **$60**                                        |

**LongCat 2.5 Preview Free:** Free for a limited time.

**Step 5 Preview Free:** Free for a limited time.

**DeepSeek V4.1 Flash / V4 Pro / V4 Flash / V4 Flash Vision Exp:** Peak hours are 01:00-04:00 and 06:00-10:00 UTC, Monday through Friday; all other hours, including weekends, are Off-Peak. [Learn more](<https://api-docs.deepseek.com/quick_start/pricing/>).

**DeepSeek V4 Flash Vision Exp:** Images are converted into tokens based on their dimensions and billed as input tokens alongside text tokens. [Learn more](<https://api-docs.deepseek.com/quick_start/pricing/>).

### Estimated requests

The tables below estimate request counts based on typical Go usage patterns. Go
Plus estimates scale with each model's higher usage limit.

#### Go

| Model                        | Requests per 5 hours | Requests per week | Requests per month |
| ---------------------------- | -------------------- | ----------------- | ------------------ |
| GLM-5.3-Flash                | 6,320                | 15,790            | 31,580             |
| GLM-5.3                      | 220                  | 540               | 1,080              |
| GLM-5.2                      | 880                  | 2,150             | 4,300              |
| Kimi K3                      | 110                  | 250               | 490                |
| Kimi K2.7 Code               | 1,350                | 3,380             | 6,750              |
| Kimi K2.6                    | 1,150                | 2,880             | 5,750              |
| LongCat-2.0                  | 11,400               | 28,600            | 57,200             |
| LongCat 2.5 Preview Free     | Unlimited            | Unlimited         | Unlimited          |
| Step 5 Preview Free          | Unlimited            | Unlimited         | Unlimited          |
| MiMo-V2.6-Flash              | 30,100               | 75,200            | 150,400            |
| MiMo-V2.6-Pro                | 3,250                | 8,150             | 16,300             |
| MiMo-V2.5                    | 30,100               | 75,200            | 150,400            |
| MiMo-V2.5-Pro                | 3,250                | 8,150             | 16,300             |
| MiniMax M3                   | 3,200                | 8,000             | 16,000             |
| MiniMax M2.7                 | 3,400                | 8,500             | 17,000             |
| Muse Spark 1.3 Contributor   | 45,300               | 113,300           | 226,600            |
| Muse Spark 1.2 Contributor   | 45,300               | 113,300           | 226,600            |
| Qwen3.8 Max                  | 160                  | 400               | 810                |
| Qwen3.8 Flash                | 5,400                | 13,500            | 27,000             |
| Qwen3.7 Plus                 | 4,300                | 10,800            | 21,600             |
| DeepSeek V4.1 Flash          | 26,000               | 65,000            | 130,000            |
| DeepSeek V4 Pro              | 1,050                | 2,600             | 5,200              |
| DeepSeek V4 Flash            | 13,000               | 32,500            | 65,000             |
| DeepSeek V4 Flash Vision Exp | 6,500                | 16,250            | 32,500             |
| Hy4 preview                  | 1,350                | 3,380             | 6,770              |
| Hy3                          | 4,300                | 10,750            | 21,500             |
| Space Bunny                  | 3,130                | 7,810             | 15,630             |
| Grok 4.7                     | 169                  | 423               | 845                |
| Grok 4.6                     | 169                  | 423               | 845                |
| GPT 6 Luna                   | 4,230                | 10,560            | 21,130             |
| GPT 5.6 Luna                 | 2,050                | 5,100             | 10,250             |
| Claude Haiku 5.5             | 3,850                | 9,620             | 19,230             |

#### Go Plus

| Model                        | Requests per 5 hours | Requests per week | Requests per month |
| ---------------------------- | -------------------- | ----------------- | ------------------ |
| GLM-5.3-Flash                | 18,960               | 47,370            | 94,740             |
| GLM-5.3                      | 1,760                | 4,320             | 8,640              |
| GLM-5.2                      | 2,640                | 6,450             | 12,900             |
| Kimi K3                      | 440                  | 1,000             | 1,960              |
| Kimi K2.7 Code               | 4,050                | 10,140            | 20,250             |
| Kimi K2.6                    | 4,600                | 11,520            | 23,000             |
| LongCat-2.0                  | 45,600               | 114,400           | 228,800            |
| LongCat 2.5 Preview Free     | Unlimited            | Unlimited         | Unlimited          |
| Step 5 Preview Free          | Unlimited            | Unlimited         | Unlimited          |
| MiMo-V2.6-Flash              | 60,200               | 150,400           | 300,800            |
| MiMo-V2.6-Pro                | 13,000               | 32,600            | 65,200             |
| MiMo-V2.5                    | 60,200               | 150,400           | 300,800            |
| MiMo-V2.5-Pro                | 13,000               | 32,600            | 65,200             |
| MiniMax M3                   | 9,600                | 24,000            | 48,000             |
| MiniMax M2.7                 | 13,600               | 34,000            | 68,000             |
| Muse Spark 1.3 Contributor   | 90,600               | 226,600           | 453,200            |
| Muse Spark 1.2 Contributor   | 90,600               | 226,600           | 453,200            |
| Qwen3.8 Max                  | 640                  | 1,600             | 3,240              |
| Qwen3.8 Flash                | 16,200               | 40,500            | 81,000             |
| Qwen3.7 Plus                 | 12,900               | 32,400            | 64,800             |
| DeepSeek V4.1 Flash          | 52,000               | 130,000           | 260,000            |
| DeepSeek V4 Pro              | 4,200                | 10,400            | 20,800             |
| DeepSeek V4 Flash            | 52,000               | 130,000           | 260,000            |
| DeepSeek V4 Flash Vision Exp | 26,000               | 65,000            | 130,000            |
| Hy4 preview                  | 5,400                | 13,520            | 27,080             |
| Hy3                          | 17,200               | 43,000            | 86,000             |
| Space Bunny                  | 12,500               | 31,250            | 62,500             |
| Grok 4.7                     | 676                  | 1,692             | 3,380              |
| Grok 4.6                     | 676                  | 1,692             | 3,380              |
| GPT 6 Luna                   | 16,920               | 42,240            | 84,520             |
| GPT 5.6 Luna                 | 8,200                | 20,400            | 41,000             |
| Claude Haiku 5.5             | 15,380               | 38,460            | 76,920             |

The estimates use the following token counts per request; actual usage varies.

- Grok 4.7/4.6 — 390 input, 32,500 cached, 120 output tokens per request
- GLM-5.3-Flash — 1,000 input, 55,000 cached, 200 output tokens per request
- GLM-5.3/5.2 — 700 input, 52,000 cached, 150 output tokens per request
- GPT 6 Luna — 1,000 input, 50,000 cached, 220 output tokens per request
- GPT 5.6 Luna — 1,000 input, 50,000 cached, 220 output tokens per request
- Claude Haiku 5.5 — 1,100 input, 55,000 cached, 240 output tokens per request
- Kimi K3 — 1,050 input, 76,500 cached, 300 output tokens per request
- Kimi K2.7/K2.6 — 870 input, 55,000 cached, 200 output tokens per request
- LongCat-2.0 — 920 input, 88,900 cached, 200 output tokens per request
- DeepSeek V4.1 Flash — 410 input, 71,300 cached, 310 output tokens per request
- DeepSeek V4 Pro — 750 input, 82,000 cached, 290 output tokens per request
- DeepSeek V4 Flash — 410 input, 71,300 cached, 310 output tokens per request
- DeepSeek V4 Flash Vision Exp — 410 input, 71,300 cached, 310 output tokens per request
- MiniMax M3 — 510 input, 56,000 cached, 190 output tokens per request
- MiniMax M2.7 — 300 input, 55,000 cached, 125 output tokens per request
- Muse Spark 1.3 Contributor — 620 input, 71,400 cached, 300 output tokens per request
- Muse Spark 1.2 Contributor — 620 input, 71,400 cached, 300 output tokens per request
- MiMo-V2.6-Flash — 830 input, 71,500 cached, 295 output tokens per request
- MiMo-V2.6-Pro — 790 input, 86,000 cached, 305 output tokens per request
- MiMo-V2.5 — 830 input, 71,500 cached, 295 output tokens per request
- MiMo-V2.5-Pro — 790 input, 86,000 cached, 305 output tokens per request
- Qwen3.8 Max — 420 input, 66,000 cached, 200 output tokens per request
- Qwen3.8 Flash — 600 input, 58,000 cached, 200 output tokens per request
- Qwen3.7 Plus — 500 input, 57,000 cached, 190 output tokens per request
- Hy4 preview — 830 input, 71,500 cached, 295 output tokens per request
- Hy3 — 830 input, 71,500 cached, 295 output tokens per request
- Space Bunny — 1,000 input, 55,000 cached, 200 output tokens per request

You can track your current usage in the **[console](<https://opencode.ai/auth>)**.

**提示：**

If you reach the usage limit, you can continue using the free models.

Usage limits may change as we learn from early usage and feedback.

---

### Usage beyond limits

If you also have credits on your Zen balance, you can enable the **Use balance**
option in the console. When enabled, Go will fall back to your Zen balance
after you've reached your usage limits instead of blocking requests.

---

### Why some models have lower usage

With Go, the included monthly usage varies by model.

For most models, we make this work through bulk discounts and reserved GPU capacity. We then pass those savings on to you as higher monthly usage.

For some models, we haven't had the opportunity to negotiate a discount or host them at a lower cost, either because the model is new or because their public pricing is already discounted.

For these models, you still get a little more than if you paid the model providers directly; this is why their included monthly usage is lower.

---

## Endpoints

You can also access Go models through the following API endpoints.

| Model                        | Model ID                     | Endpoint                                         | AI SDK Package              |
| ---------------------------- | ---------------------------- | ------------------------------------------------ | --------------------------- |
| Grok 4.7                     | grok-4.7                     | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| Grok 4.6                     | grok-4.6                     | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| GPT 6 Luna                   | gpt-6-luna                   | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| GPT 5.6 Luna                 | gpt-5.6-luna                 | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| Claude Haiku 5.5             | claude-haiku-5-5             | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| GLM-5.3-Flash                | glm-5.3-flash                | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| GLM-5.3                      | glm-5.3                      | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| GLM-5.2                      | glm-5.2                      | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Kimi K3                      | kimi-k3                      | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Kimi K2.7 Code               | kimi-k2.7-code               | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Kimi K2.6                    | kimi-k2.6                    | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| LongCat-2.0                  | longcat-2.0                  | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| LongCat 2.5 Preview Free     | longcat-2.5-preview-free     | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Step 5 Preview Free          | step-5-preview-free          | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| DeepSeek V4.1 Flash          | deepseek-v4.1-flash          | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Pro              | deepseek-v4-pro              | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Flash            | deepseek-v4-flash            | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| DeepSeek V4 Flash Vision Exp | deepseek-v4-flash-vision-exp | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiMo-V2.6-Flash              | mimo-v2.6-flash              | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiMo-V2.6-Pro                | mimo-v2.6-pro                | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiMo-V2.5                    | mimo-v2.5                    | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiMo-V2.5-Pro                | mimo-v2.5-pro                | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| MiniMax M3                   | minimax-m3                   | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| MiniMax M2.7                 | minimax-m2.7                 | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| Muse Spark 1.3 Contributor   | muse-spark-1.3-contributor   | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| Muse Spark 1.2 Contributor   | muse-spark-1.2-contributor   | `https://opencode.ai/zen/go/v1/responses`        | `@ai-sdk/openai`            |
| Qwen3.8 Max                  | qwen3.8-max                  | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| Qwen3.8 Flash                | qwen3.8-flash                | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| Qwen3.7 Plus                 | qwen3.7-plus                 | `https://opencode.ai/zen/go/v1/messages`         | `@ai-sdk/anthropic`         |
| Hy4 preview                  | hy4-preview                  | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Hy3                          | hy3                          | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |
| Space Bunny                  | space-bunny                  | `https://opencode.ai/zen/go/v1/chat/completions` | `@ai-sdk/openai-compatible` |

The [model id](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#models>) in your OpenCode config
uses the format `opencode-go/<model-id>`. For example, for Kimi K3, you would
use `opencode-go/kimi-k3` in your config.

---

### Models

You can fetch the full list of available models and their metadata from:

```
https://opencode.ai/zen/go/v1/models
```

---

## Privacy

| Model                        | Model training | Data retention |
| ---------------------------- | -------------- | -------------- |
| Grok 4.7                     | Not used       | 30 days        |
| Grok 4.6                     | Not used       | 30 days        |
| GPT 6 Luna                   | Not used       | 30 days        |
| GPT 5.6 Luna                 | Not used       | 30 days        |
| Claude Haiku 5.5             | Not used       | 30 days        |
| GLM-5.3-Flash                | Not used       | 0 days         |
| GLM-5.3                      | Not used       | 0 days         |
| GLM-5.2                      | Not used       | 0 days         |
| Kimi K3                      | Not used       | 0 days         |
| Kimi K2.7 Code               | Not used       | 0 days         |
| Kimi K2.6                    | Not used       | 0 days         |
| LongCat-2.0                  | Not used       | 0 days         |
| MiMo-V2.6-Pro                | Not used       | 0 days         |
| MiMo-V2.6-Flash              | Not used       | 0 days         |
| MiMo-V2.5-Pro                | Not used       | 0 days         |
| MiMo-V2.5                    | Not used       | 0 days         |
| Qwen3.8 Max                  | Not used       | 0 days         |
| Qwen3.8 Flash                | Not used       | 0 days         |
| Qwen3.7 Plus                 | Not used       | 0 days         |
| MiniMax M3                   | Not used       | 0 days         |
| MiniMax M2.7                 | Not used       | 0 days         |
| Muse Spark 1.3 Contributor   | Yes            | Not ZDR        |
| Muse Spark 1.2 Contributor   | Yes            | Not ZDR        |
| DeepSeek V4.1 Flash          | Not used       | 0 days\*       |
| DeepSeek V4 Pro              | Not used       | 0 days\*       |
| DeepSeek V4 Flash            | Not used       | 0 days\*       |
| DeepSeek V4 Flash Vision Exp | Not used       | 0 days\*       |
| Hy4 preview                  | Not used       | 0 days         |
| Hy3                          | Not used       | 0 days         |
| Space Bunny                  | Not used       | 0 days         |
| LongCat 2.5 Preview Free     | Not used       | 0 days         |
| Step 5 Preview Free          | Not used       | 0 days         |

- **Grok 4.7/4.6:** ZDR disables important API features that depend on stored data, including the stateful Responses API, Files and Collections, and the Batch API. [Learn more](<https://docs.x.ai/developers/faq/security#what-is-zero-data-retention-zdr>).
- **GPT 6 Luna / GPT 5.6 Luna:** Abuse monitoring logs are generated for all API feature usage and retained for up to 30 days. [Learn more](<https://developers.openai.com/api/docs/guides/your-data#data-retention-controls-for-abuse-monitoring>).
- **Claude Haiku 5.5:** Requests are retained for 30 days in accordance with [Anthropic's Data Policies](<https://docs.anthropic.com/en/docs/claude-code/data-usage>).
- **Muse Spark 1.3 Contributor:** Heavily discounted token pricing in exchange for permission to use your prompts and completions to train future Meta models. Availability is limited to regions permitted by Meta's [Geographic Use Policy](<https://ai.developer.meta.com/legal/geographic-use-policy>). [Learn more](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>).
- **Muse Spark 1.2 Contributor:** Heavily discounted token pricing in exchange for permission to use your prompts and completions to train future Meta models. Availability is limited to regions permitted by Meta's [Geographic Use Policy](<https://ai.developer.meta.com/legal/geographic-use-policy>). [Learn more](<https://dev.meta.ai/docs/pricing-rate-limits#contributor-tier>).
- **DeepSeek:** ZDR agreement is renewed monthly. The current agreement is valid through October 31, 2026.

---

## Goals

We created OpenCode Go to:

1. Make AI coding **accessible** to more people with a low cost subscription.
2. Provide **reliable** access to the best open coding models.
3. Curate models that are **tested and benchmarked** for coding agent use.
4. Have **no lock-in** by allowing you to use any other provider with OpenCode as well.


</details>
