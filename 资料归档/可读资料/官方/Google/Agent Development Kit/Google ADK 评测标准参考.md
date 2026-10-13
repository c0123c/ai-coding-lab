# Google ADK 评测标准参考

比较工具调用轨迹、回复质量、安全性及模型评分标准，说明适用条件、配置和结果解读。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-10-01](<https://github.com/google/adk-docs/commit/f82eee54c5527c63dcb9e38fe760a508c56812ae>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/evaluate/criteria.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/evaluate/criteria.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 评价标准

<div class="language-support-tag">
 <span class="lst-supported">受 ADK</span><span class="lst-python">Python</span>
</div> 支持

本页概述了 ADK 提供的用于评估 agent
 性能的评估标准，包括工具使用轨迹、响应质量和安全性。

标准 | 描述 | 基于参考 | 需要评分标准 | LLM- 作为法官 | 支持 [ 用户模拟](<Google ADK 评测的用户模拟.md>)
:---------------------------------------------------- | :-------------------------------------------------------------------- | :-------------- | :---------------------------- | :------------------------ | :----------------------------------------------------------------
`tool_trajectory_avg_score` | 刀具调用轨迹精确匹配 | 是 | 否 | 否 | 否
`response_match_score` | ROUGE-1 与参考响应的相似性 | 是 | 否 | 否 | 否 
`response_evaluation_score` | 顶点 AI 的一致性得分agent 响应 | 是 | 否 | 是 | 否 
`final_response_match_v2` | LLM- 判断与参考响应的语义匹配| 是 | 否 | 是 | 否 
`rubric_based_final_response_quality_v1` | LLM-根据自定义评分标准判断最终响应质量 | 否| 是 | 是 | 是
`rubric_based_tool_use_quality_v1` | LLM-根据自定义标准判断工具使用质量 | 否 | 是| 是 | 是
`rubric_based_multi_turn_trajectory_quality_v1` | LLM-根据自定义标准判断多转弯轨迹质量 | 否 | 是 | 是| 是 
`hallucinations_v1` | LLM-根据上下文判断 agent 响应的接地性 | 否 | 否 | 是| 是
`safety_v1` | Safety/harmlessness 的 agent 响应 | 否 | 否 | 是 |是
`per_turn_user_simulator_quality_v1` | LLM-判断用户模拟器质量| 否| 否| 是| 是
`multi_turn_task_success_v1` | 评估 agent 是否实现对话目标 | 否 | 否 | 是 | 是 
`multi_turn_trajectory_quality_v1` | 评估总体轨迹对话的 | 否 | 否 | 是 | 是 
`multi_turn_tool_use_quality_v1` | 评估对话期间进行的函数调用 | 否 | 否| 是 | 是

## 效率标准

以下四个标准衡量 agent 的运行成本。它们 
 在几个方面与其他标准不同。它们是*信息性*，因为
它们报告一个值，但从未通过或失败评估案例，并且它们的状态
始终为`INFORMATIONAL`。它们也 * 始终打开 *，因为每次评估都会报告它们 
，但不会出现在 `EvalConfig` 中，并且无法关闭 
。它们无需配置任何内容，并且在其中设置的阈值会被
拒绝而不是忽略，因此配置永远不会带有死设置。

标准 | 报告内容 | 单位 
:------------------------------------ | :-------------------------------------------------------- | :------
`tool_call_count_v1` | 工具调用次数agent 进行 | 计数
`inference_call_count_v1` | agent 进行的模型调用次数 | 计数
`token_usage_v1` | 模型在调用中消耗的令牌| 令牌
`invocation_duration_v1` | 调用花费的挂钟时间 | 秒

下面，在质量标准之后，分别记录这四个内容。

## tool_trajectory_avg_score

此标准将 agent 调用的工具序列与预期调用列表 
 进行比较，并根据匹配类型之一计算平均分数：
`EXACT`、`IN_ORDER` 或 `ANY_ORDER`。

#### 何时使用此标准？

此标准非常适合 agent 正确性取决于工具 
 调用的场景。根据需要遵循的工具调用的严格程度，您可以从以下三种匹配类型之一中选择 
：`EXACT`、`IN_ORDER` 和 `ANY_ORDER`。

该指标对于以下方面特别有价值：

*   **回归测试：**确保agent更新不会无意中
改变已建立的测试用例的工具调用行为。
*   **工作流程验证：**验证agents是否正确遵循预定义
需要按特定顺序调用特定 API 的工作流程。
*   **高精度任务：**评估工具存在轻微偏差的任务
参数或调用顺序可能会导致明显不同或不正确的
结果。

当您需要强制执行特定的工具执行路径时，请使用 `EXACT` 匹配，并且 
 将任何偏差（无论是工具名称、参数还是顺序）视为失败。

当您想要确保某些关键工具调用以 
 特定顺序发生，但允许其他工具调用发生在其间时，请使用 `IN_ORDER` 匹配。此选项
有助于确保某些关键操作或工具调用是否按特定顺序发生，
也为其他工具调用的发生留下了一定的范围。

当您想要确保某些关键工具调用发生时，请使用 `ANY_ORDER` 匹配，但 
 不关心它们的顺序，并允许其他工具调用发生在 
 之间。此条件对于出现有关 
 同一概念的多个工具调用的情况非常有用，例如您的 agent 问题 5 搜索查询。在搜索查询发生之前，您并不真正
关心它们发出的顺序。

#### 详情

对于正在评估的每个调用，此标准将 agent 生成的 
 工具调用列表与使用三种匹配类型之一的 
 的预期工具调用列表进行比较。如果工具根据所选匹配 
 类型调用匹配，则该调用将获得 1.0 分数，否则分数为 0.0。
最终值是 
 评估案例中所有调用的这些分数的平均值。

可以使用以下匹配类型之一来完成比较：

*   **`EXACT`**：需要实际工具与预期工具完美匹配
调用，没有额外或缺失的工具调用。
*   **`IN_ORDER`**：要求存在预期列表中的所有工具调用
在实际列表中，顺序相同，但允许其他工具调用 
 出现在两者之间。
*   **`ANY_ORDER`**：要求预期列表中的所有工具调用
以任意顺序出现在实际列表中，并允许其他工具调用 
 出现在两者之间。

#### 如何使用这个标准？

默认情况下， `tool_trajectory_avg_score` 用途 `EXACT` 匹配类型。您可以指定
只是这个标准的一个阈值 `EvalConfig` 根据 `criteria`
字典为 `EXACT` 匹配类型。该值应该是一个介于 0.0 和
1.0，它表示评估案例通过的最低可接受分数。如果
您希望工具轨迹在所有调用中完全匹配，您应该设置
的阈值 1.0.

`EXACT` 匹配的 `EvalConfig` 条目示例：

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0
  }
}
```

或者您可以显式指定 `match_type`：

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "EXACT"
    }
  }
}
```


如果您想使用 `IN_ORDER` 或 `ANY_ORDER` 匹配类型，您可以通过 
`match_type` 字段以及阈值指定它。

`IN_ORDER` 匹配的 `EvalConfig` 条目示例：

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "IN_ORDER"
    }
  }
}
```

`ANY_ORDER` 匹配的 `EvalConfig` 条目示例：

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "ANY_ORDER"
    }
  }
}
```

#### 输出以及如何解释

输出是 0.0 和 1.0 之间的分数，其中 1.0 表示所有调用的实际工具轨迹与预期工具轨迹完美匹配 
，而 0.0
 表示所有调用完全不匹配。分数越高越好。 
 分数低于 1.0 意味着对于至少一次调用，agent 的工具调用 
 轨迹偏离预期轨迹。

## response_match_score

此标准评估 agent 的最终响应是否与使用 Rouge-1 的 golden/expected
 最终响应匹配。

### 何时使用此标准？

当您需要定量测量 
agent 的输出与内容重叠方面的预期输出的匹配程度时，请使用此标准。

### 详情

ROUGE-1专门测量
系统生成的文本（候选摘要）和参考文本之间的一元组（单个单词）的重叠。它
本质上检查候选文本中存在参考文本中的多少个单词
。要了解更多信息，请参阅
[ROUGE-1](<https://github.com/google-research/google-research/tree/master/rouge>)的详细信息。

### 如何使用这个标准？

您可以在 
`criteria` 字典下的 `EvalConfig` 中指定此条件的阈值。该值应该是 0.0 和 1.0 之间的浮点数，其中 
 表示评估案例通过的最低可接受分数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "response_match_score": 0.8
  }
}
```

### 输出以及如何解释

该标准的值范围是[0,1]，值越接近1越可取。

## final_response_match_v2

该标准使用 LLM 作为判断来评估 agent 的最终响应是否与 golden/expected
 最终响应匹配。

### 何时使用此标准？

当您需要根据参考评估 agent 的最终 
 响应的正确性，但需要灵活地显示答案 
 时，请使用此标准。它适用于
可以接受不同措辞或格式的情况，只要核心含义和信息与参考文献相匹配。
此标准是评估问答、
摘要或其他生成任务的不错选择，其中语义等价比精确词汇更重要
重叠，使其成为 
 到 `response_match_score` 的更复杂的替代品。

### 详情

该标准使用大型语言模型（LLM）作为判断，以确定
agent的最终响应在语义上是否等同于提供的参考
响应。它的设计比词汇匹配指标（如
`response_match_score`）更灵活，因为它重点关注agent的响应是否包含
正确的信息，同时容忍格式、措辞、
或包含其他正确细节的差异。

对于每次调用，该标准都会提示法官 LLM 与参考相比，将 agent 的 
 响应评级为“有效”或“无效”。为了鲁棒性，此操作会重复
多次（可通过`num_samples`配置），并且多数
投票决定调用是否收到1.0（有效）或0.0
（无效）的分数。最终标准分数是整个评估案例中被视为有效的调用的比例
。

### 如何使用这个标准？

该标准使用`LlmAsAJudgeCriterion`，允许您配置
评估阈值、判断模型和每次调用的样本数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "final_response_match_v2": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      }
    }
  }
}
```

### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。分数 1.0 表示 LLM
 法官认为 agent 的最终响应对所有调用都有效，
，而接近 0.0 的分数表示与参考响应相比，许多响应被判定为无效 
。值越高越好。

## rubric_based_final_response_quality_v1

该标准使用 LLM 作为判断者，根据 
 用户定义的一组评分标准来评估 agent 最终响应的质量。

### 何时使用此标准？

当您需要评估响应质量的各个方面时，请使用此标准
超越简单的正确性或与引用的语义等同性。这是理想的
用于评估细微的属性，例如语气、风格、有用性或遵守
您的规则中定义的具体对话准则。这个标准是
当不存在单一参考响应或质量
取决于多种主观因素。

### 详情

此标准提供了一种灵活的方法来根据您定义为评分标准的 
 特定标准来评估响应质量。例如，您可以定义 
 标题来检查响应是否简洁，是否正确推断用户意图，
 或者是否避免行话。

该标准使用 LLM- 作为法官来评估 agent 针对每个评分标准的最终响应 
，产生 `yes` (1.0) 或 `no` (0.0) 判决
与其他基于 LLM- 的指标一样，它在每次 
 调用中对判断模型进行多次采样，并使用多数投票来确定 
 该调用中每个评分标准的分数。调用的总分是其 
 评分标准的平均值。评估案例的最终标准分数是所有调用的
这些总体分数的平均值。

### 如何使用这个标准？

该标准使用`RubricsBasedCriterion`，这需要在`EvalConfig`中提供
的量规列表。每个标题应定义一个唯一的 ID
 及其内容。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "rubric_based_final_response_quality_v1": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "conciseness",
          "rubric_content": {
            "text_property": "The agent's response is direct and to the point."
          }
        },
        {
          "rubric_id": "intent_inference",
          "rubric_content": {
            "text_property": "The agent's response accurately infers the user's underlying goal from ambiguous queries."
          }
        }
      ]
    }
  }
}
```

还可以通过 `EvalCase.rubrics` 附加每个案例的评分细则。与 
 标准级别的评分标准不同，这些评分标准是通过 `type` 过滤的。只有 
`type` 与此条件的预期值 (`"FINAL_RESPONSE_QUALITY"`)
 匹配的条目才会合并到有效的 rubric 集中：

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "no_speculative_pricing",
      "rubric_content": {
        "text_property": "The agent's final response does not fabricate prices for products it did not look up."
      },
      "type": "FINAL_RESPONSE_QUALITY"
    }
  ]
}
```

传递给法官的合并标题列表是上面的标准级别列表和来自 `EvalCase.rubrics` 的任何类型匹配条目的并集。

#### 量规注释

- 有效的标题列表 ** 必须是非空的 **，否则 `RubricBasedEvaluator` 在评估时会引发 `ValueError`。只要评估案例提供类型匹配的规则，`EvalConfig.criteria["rubric_based_final_response_quality_v1"].rubrics` 上的标准级别列表就可以留空。
- `EvalCase.rubrics` 上的评分标准是标准级别列表顶部的 * 附加 *，而不是替换。传递给法官的有效评分标准集是两者的并集。
- 通过 `EvalCase.rubrics` 按案例提供的量规由 `type` 过滤：仅合并 `type` 为 `"FINAL_RESPONSE_QUALITY"` 的量规。 `EvalConfig` 中的标准级量规为 ** 而非 ** 过滤作者：`type`。

### 输出以及如何解释

该标准输出 0.0 和 1.0 之间的总分，其中 1.0 指示 
 agent 的响应满足所有调用中的所有标准，而 0.0
 指示没有满足任何标准。结果还包括每次调用的详细
每个标题的分数。值越高越好。

## rubric_based_tool_use_quality_v1

该标准使用 LLM 作为判断者，根据 
 用户定义的一组评分标准来评估 agent 工具使用的质量。

### 何时使用此标准？

当您需要评估**和agent如何使用工具时，而不是
而只是*（如果*最终响应正确）时，请使用此标准。它非常适合评估
agent是否选择了正确的工具、使用了正确的参数，或者遵循了
特定的工具调用顺序。这对于验证 agent 推理 
 流程、调试工具使用错误以及确保遵守规定的 
 工作流程非常有用，特别是在多个工具使用路径可能导致 
 类似的最终答案但只有一条路径被认为是正确的情况下。

### 详情

此标准提供了一种灵活的方法来根据您定义为评分标准的特定 
 规则来评估工具使用情况。例如，您可以定义规则来检查
是否调用了特定工具，其参数是否正确，或者是否以特定顺序调用了工具
。

该标准使用 LLM- 作为法官来评估 agent 的工具调用和 
 对每个评分标准的响应，生成 `yes` (1.0) 或 `no` (0.0)对
每个的判决。与其他基于 LLM- 的指标一样，它在每次调用时对判断模型进行多次采样 
，并使用多数投票来确定该调用中每个评分标准 
 的分数。调用的总分是其 
 评分标准的平均值。评估案例的最终标准分数是所有调用的
这些总体分数的平均值。

### 如何使用这个标准？

该标准使用`RubricsBasedCriterion`，这需要在`EvalConfig`中提供
的量规列表。每个标题都应定义一个唯一的 ID
 及其内容，描述要评估的工具使用的特定方面。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "rubric_based_tool_use_quality_v1": {
      "threshold": 1.0,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "geocoding_called",
          "rubric_content": {
            "text_property": "The agent calls the GeoCoding tool before calling the GetWeather tool."
          }
        },
        {
          "rubric_id": "getweather_called",
          "rubric_content": {
            "text_property": "The agent calls the GetWeather tool with coordinates derived from the user's location."
          }
        }
      ]
    }
  }
}
```

还可以通过 `EvalCase.rubrics` 附加每个案例的评分细则。与 
 标准级别的评分标准不同，这些评分标准是通过 `type` 过滤的。只有 
`type` 与此条件的预期值 (`"TOOL_USE_QUALITY"`) 匹配的条目才会 
 合并到有效的 rubric 集中：

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "no_pricing_tool_when_not_asked",
      "rubric_content": {
        "text_property": "The agent does not call the pricing tool in this case, since the user only asked about availability."
      },
      "type": "TOOL_USE_QUALITY"
    }
  ]
}
```

传递给法官的合并标题列表是上面的标准级别列表和来自 `EvalCase.rubrics` 的任何类型匹配条目的并集。

#### 量规注释

- 有效的标题列表 ** 必须是非空的 **，否则 `RubricBasedEvaluator` 在评估时会引发 `ValueError`。只要评估案例提供类型匹配的规则，`EvalConfig.criteria["rubric_based_tool_use_quality_v1"].rubrics` 上的标准级别列表就可以留空。
- `EvalCase.rubrics` 上的评分标准是标准级别列表顶部的 * 附加 *，而不是替换。传递给法官的有效评分标准集是两者的并集。
- 通过 `EvalCase.rubrics` 按案例提供的量规由 `type` 过滤：仅合并 `type` 为 `"TOOL_USE_QUALITY"` 的量规。 `EvalConfig` 中的标准级量规为 ** 而非 ** 过滤作者：`type`。

### 输出以及如何解释

该标准输出 0.0 和 1.0 之间的总分，其中 1.0 表示 
 agent 的工具使用满足所有调用中的所有标准，而 
0.0 表示没有满足任何标准。结果还包括每次调用的详细
每个标题的分数。值越高越好。

## rubric_based_multi_turn_trajectory_quality_v1

该标准使用 LLM 作为 
 法官，根据用户定义的一组规则来评估 agent 在整个 
 多轮对话中的行为质量。

### 何时使用此标准？

当您需要评估某个项目的各个方面时，请使用此标准 agent's *轨迹*
跨越多轮对话——例如 agent 纠正航向
在晚点披露背景信息后，平衡有用性与安全性，或者遵循
特定领域的对话指南——而不仅仅是单轮对话
最终回应。不像 `multi_turn_trajectory_quality_v1`，它委托给
的 Agent 平台评估 SDK 并评估沿通用轴的轨迹，这
标准允许您指定自定义 yes/no 根据您的领域量身定制的标准。

### 详情

此标准累积整个对话 
 的完整对话历史记录（用户轮次、agent 轮次和工具交互），并针对您提供的每个标题执行基于单个 
LLM- 的评估。对于每个评分细则，
裁判会产生一个`yes`（1.0）或`no`（0.0）判决，反映agent在所有回合中的
累积行为。评估案例的第一个 N-1 回合被 
 标记为 `NOT_EVALUATED`，最后一个回合包含总分。与
其他基于LLM-的指标一样，每次调用
都会对法官进行多次采样，并使用多数投票进行聚合。

### 如何使用这个标准？

该标准使用`RubricsBasedCriterion`。在 
`EvalConfig` 条目中提供您的评分标准；各个 `EvalCase` 条目上携带的评分标准将添加 
 在标准级别列表的顶部，并按 `type` 进行过滤（请参阅下面的注释）。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "rubric_based_multi_turn_trajectory_quality_v1": {
      "threshold": 0.7,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "elicits_individual_factors",
          "rubric_content": {
            "text_property": "The agent asks about individual factors (age, prior conditions, current medication) before giving any individualized advice."
          }
        },
        {
          "rubric_id": "corrects_after_late_disclosure",
          "rubric_content": {
            "text_property": "When the user discloses risk-relevant information late in the conversation, the agent revisits and corrects earlier advice rather than leaving it standing."
          }
        }
      ]
    }
  }
}
```

还可以通过 `EvalCase.rubrics` 附加每个案例的评分细则。与 
 标准级别的评分标准不同，这些评分标准是通过 `type` 过滤的。只有 
`type` 与此条件的预期值 (`"TRAJECTORY_QUALITY"`) 匹配的条目才会 
 合并到有效的 rubric 集中：

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "checks_interactions_before_recommending",
      "rubric_content": {
        "text_property": "Given this case's disclosed medication history, the agent checks for drug interactions before finalizing any recommendation."
      },
      "type": "TRAJECTORY_QUALITY"
    }
  ]
}
```

传递给法官的合并标题列表是上面的标准级别列表和来自 `EvalCase.rubrics` 的任何类型匹配条目的并集。

#### 量规注释

- 有效的标题列表 ** 必须是非空的 **，否则 `RubricBasedEvaluator` 在评估时会引发 `ValueError`。只要评估案例提供类型匹配的规则，`EvalConfig.criteria["rubric_based_multi_turn_trajectory_quality_v1"].rubrics` 上的标准级别列表就可以留空。
- `EvalCase.rubrics` 上的评分标准是标准级别列表顶部的 * 附加 *，而不是替换。传递给法官的有效评分标准集是两者的并集。
- 通过 `EvalCase.rubrics` 按案例提供的量规由 `type` 过滤：仅合并 `type` 为 `"TRAJECTORY_QUALITY"` 的量规。 `EvalConfig` 中的标准级量规为 ** 而非 ** 过滤作者：`type`。

### 输出以及如何解释

该标准输出 0.0 和 1.0 之间的总分，其中 1.0 表示 
 agent 的轨迹满足所有评分标准，0.0 表示不满足 
 评分标准。结果还包括详细的每个评分标准的分数。
值越高越好。

## hallucinations_v1

此标准评估模型响应是否包含任何虚假、
矛盾或不受支持的主张。

### 何时使用此标准？

使用此标准可确保 agent 的响应基于提供的 
 上下文（例如工具输出、用户查询、指令），并且不包含 
 幻觉。

### 详情

此标准根据上下文（包括开发人员
指令、用户提示、工具定义和工具调用及其
结果）评估模型响应是否包含任何错误、
矛盾或不受支持的声明。它使用 LLM- 作为法官并遵循两个步骤：

1.  **分段器**：将agent响应分段为单个句子。
2.  **句子验证器**：根据
提供了接地的背景。每个句子都标记为 `supported`、
、`unsupported`、`contradictory`、`disputed` 或 `not_applicable`。

该指标计算准确度分数：
`supported` 或 `not_applicable` 的句子的百分比。默认情况下，仅评估最终响应
。如果 `evaluate_intermediate_nl_responses` 在 
 标准中设置为 true，则还会 
 评估来自 agents 的中间自然语言响应。

### 如何使用这个标准？

该标准使用`HallucinationsCriterion`，允许您配置
评估阈值、判断模型、每次调用的样本数以及
是否评估中间自然语言响应。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "hallucinations_v1": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest"
      },
      "evaluate_intermediate_nl_responses": true
    }
  }
}

```

### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。 1.0 的分数表示 agent 的响应中的所有 
 句子均以上下文为基础，而 
 接近 0.0 的分数表示许多句子是错误的、矛盾的或不受支持的。
较高的值是更好。

## safety_v1

该标准评估 Agent 响应的安全性（无害性）。

### 何时使用此标准？

当您需要确保 agent 响应 
 符合安全准则并且不会产生有害或不当的 
 内容时，应使用此标准。对于面向用户的应用程序或任何以 
 响应安全为优先考虑的系统来说，它至关重要。

### 详情

此标准评估 agent 的回复是否包含任何有害的 
 内容，例如仇恨言论、骚扰或危险信息。与 ADK 内本地实现的其他 
 指标不同，`safety_v1` 将评估委托给 
 Agent 平台评估 SDK。

### 如何使用这个标准？

使用此标准需要 Google 云项目。您必须设置
`GOOGLE_CLOUD_PROJECT`和`GOOGLE_CLOUD_LOCATION`环境变量，
通常位于agent目录中的`.env`文件中，以便Agent平台
SDK正常运行。有关从ADK agents连接到Google云
的更多信息，请参阅
[连接到Google云和Agent平台](<https://google.github.io/adk-docs/get-started/google-cloud/>)。

您可以在 
`criteria` 字典下的 `EvalConfig` 中指定此条件的阈值。该值应该是 0.0 和 1.0 之间的浮点数，
 表示响应被视为通过的最低安全分数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "safety_v1": 0.8
  }
}
```

### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。接近 1.0 的分数表明 
 该响应是安全的，而接近 0.0 的分数表明存在潜在的安全性 
 问题。

## per_turn_user_simulator_quality_v1

该标准评估用户模拟器是否忠实于对话
计划和角色。

#### 何时使用此标准？

当您需要在多轮
对话中评估用户模拟器时，请使用此标准。它旨在评估模拟器是否遵循`ConversationScenario`中定义的
对话计划和角色。

#### 详情

该标准确定用户模拟器在多轮对话中是否遵循定义的
`ConversationScenario`。

对于第一轮，此标准检查用户模拟器响应是否与 `ConversationScenario` 中的 
`starting_prompt` 匹配。对于后续回合，它使用
LLM-作为判断来评估用户响应是否遵循`ConversationScenario`中的`conversation_plan`
和`user_persona`。为了检查对 
 角色的遵守情况，我们使用 `UserPersona` 中指定的 `violation_rubrics`。

#### 如何使用这个标准？

该标准允许您配置评估阈值、判断模型
和每次调用的样本数。该标准还允许您指定一个
`stop_signal`，它向LLM发出信号，判断对话已完成。
为了获得最佳结果，请在`LlmBackedUserSimulator`中使用停止信号。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "per_turn_user_simulator_quality_v1": {
      "threshold": 1.0,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "stop_signal": "</finished>"
    }
  }
}
```

#### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数，表示根据 
 对话场景判断用户模拟器的响应有效的 
 回合的分数。分数 1.0 表示模拟器在所有回合中的表现均符合预期 
，而接近 0.0 的分数表示 
 模拟器在许多回合中出现偏差。值越高越好。

### multi_turn_task_success_v1

此标准评估 agent 是否实现了 
 对话的一个或多个目标。

#### 何时使用此标准？

当您想要衡量多轮 
 对话在实现其预期目标方面的总体成功程度时，请使用此标准。它重点关注最终的
结果，而不是实现该结果所采取的具体步骤。

#### 详情

该标准考虑多轮对话
的所有轮次来确定任务是否成功完成。它将评估
委托给Agent平台评估SDK。

#### 如何使用这个标准？

使用此标准需要 Google 云项目。您必须为 Agent 平台 SDK 设置 
`GOOGLE_CLOUD_PROJECT` 和 `GOOGLE_CLOUD_LOCATION` 环境变量，
通常位于 agent 目录中的 `.env` 文件中使
功能正常。有关从 ADK agents 连接到 Google 云的更多信息，请参阅
[连接到 Google 云和 Agent 平台](<https://google.github.io/adk-docs/get-started/google-cloud/>)。

您可以在 
`criteria` 字典下的 `EvalConfig` 中指定此条件的阈值。该值应该是 0.0 和 1.0 之间的浮点数，
 表示对话被视为成功的最低分数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "multi_turn_task_success_v1": 0.8
  }
}
```

#### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。分数接近 1.0 表明 
 任务已成功实现，而分数接近 0.0 表明 
 未能实现目标。

### multi_turn_trajectory_quality_v1

该标准评估对话的整体轨迹。

#### 何时使用此标准？

该指标与 `multi_turn_task_success_v1` 不同，因为 
 任务成功仅与目标是否实现有关。如何
实现这一点并不是它所关心的。另一方面，该指标评估 
 agent 为实现目标而采取的路径或轨迹。当您关心对话期间采取的 
 步骤的效率、有效性和逻辑时，请使用此 
 标准。

#### 详情

该标准是一个无参考指标，用于评估跨多个回合的 
 交互轨迹的质量。它将评估委托给
Agent平台评估SDK。

#### 如何使用这个标准？

使用此标准需要 Google 云项目。您必须为 Agent 平台 SDK 设置 
`GOOGLE_CLOUD_PROJECT` 和 `GOOGLE_CLOUD_LOCATION` 环境变量，
通常位于 agent 目录中的 `.env` 文件中使
功能正常。有关从 ADK agents 连接到 Google 云的更多信息，请参阅
[连接到 Google 云和 Agent 平台](<https://google.github.io/adk-docs/get-started/google-cloud/>)。

您可以在 
`criteria` 字典下的 `EvalConfig` 中指定此条件的阈值。该值应该是 0.0 和 1.0 之间的浮点数，
 表示被视为通过的最低轨迹质量分数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "multi_turn_trajectory_quality_v1": 0.8
  }
}
```

#### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。接近 1.0 的分数表示 
 是高质量的轨迹，而接近 0.0 的分数表示较差或 
 低效的轨迹。

### multi_turn_tool_use_quality_v1

此标准评估多轮
对话期间进行的函数调用。

#### 何时使用此标准？

使用此标准专门评估 agent 在多轮 
 对话中进行的工具或函数调用的质量、相关性和 
 正确性。它对于调试 agent 功能非常有用，例如 
agent 是否知道何时以及如何在复杂的多步骤 
 工作流程中选择正确的工具。

#### 详情

该指标是无参考的，可以评估函数调用行为
，而不需要黄金轨迹。它将评估委托给 Vertex
AI General AI Eval SDK。

#### 如何使用这个标准？

使用此标准需要 Google 云项目。您必须为 Agent 平台 SDK 设置 
`GOOGLE_CLOUD_PROJECT` 和 `GOOGLE_CLOUD_LOCATION` 环境变量，
通常位于 agent 目录中的 `.env` 文件中使
功能正常。有关从 ADK agents 连接到 Google 云的更多信息，请参阅
[连接到 Google 云和 Agent 平台](<https://google.github.io/adk-docs/get-started/google-cloud/>)。

您可以在 
`criteria` 字典下的 `EvalConfig` 中指定此条件的阈值。该值应该是 0.0 和 1.0 之间的浮点数，
 表示被视为通过的最低工具使用质量分数。

示例 `EvalConfig` 条目：

```json
{
  "criteria": {
    "multi_turn_tool_use_quality_v1": 0.8
  }
}
```

#### 输出以及如何解释

该标准返回 0.0 和 1.0 之间的分数。接近 1.0 的分数表明 
 在整个对话过程中工具使用情况良好，而接近 0.0
 的分数表明工具使用情况较差。

## tool_call_count_v1

此标准报告 agent 进行的工具（函数）调用的数量。

### 何时使用此标准？

您不需要启用它。使用它来查看更改是否使 agent
 调用比以前更多的工具 - 例如在编辑指令或 
 交换模型之后。

### 详情

该计数是根据 agent 的工具调用轨迹每次调用得出的。为 eval 案例报告的 
 值是其调用的平均值，并且 
 每次调用计数与其一起报告。

### 如何使用这个标准？

没有什么可配置的。提供阈值是一个错误而不是无操作。

### 输出以及如何解释

非负数；更低意味着更少的工具调用。它永远不会通过或失败，
因此它不会影响评估案例的状态。

## inference_call_count_v1

此标准报告 agent 进行的推理（模型）调用的数量。

### 何时使用此标准？

您不需要启用它。使用它作为推理步骤
或重试一轮的代理；跳转通常意味着agent正在循环或
重新规划。与 `token_usage_v1` 一起阅读，它区分了回合 
 变得昂贵的两种方式：更多调用，或每次调用更大的上下文。

### 详情

为调用记录的每个模型响应都算作一次调用，包括生成最终响应的 
。 eval
 情况报告的值是其调用的平均值。

评估调用跨越整个回合，并且在该回合 
 期间运行的每个子 agent 共享其调用 ID，因此计数涵盖整个回合，而不是 
，而不是任何一个 agent。

### 如何使用这个标准？

没有什么可配置的。提供阈值是一个错误而不是无操作。

### 输出以及如何解释

非负数；较低意味着模型调用较少。当运行未捕获模型调用数据时，报告无值 (n/a)
。

## token_usage_v1

此标准报告模型在调用过程中消耗的令牌。

### 何时使用此标准？

您不需要启用它。用它来捕捉在移动代币支出的同时使质量
保持不变的变化——更长的指令、模型交换或
思考预算变化。

### 详情

每个模型调用报告的令牌计数是每次调用的总和，
 为 eval 案例报告的值是其调用的平均值。

分数就是总分。每个令牌计数都作为
细分报告，并且计数嵌套，每个计数都包含其下的计数：

```
total_tokens
  input_tokens
    prompt_tokens
      cached_tokens
    tool_use_tokens
  output_tokens
    candidates_tokens
    reasoning_tokens
```

`total_tokens` 值源自 `input_tokens` 加 `output_tokens`，而不是 
，而不是从后端自己报告的总数中获取，因此它始终与 
 细分一致。

当后端未报告时，计数读取为 n/a，而不是 0。

### 如何使用这个标准？

没有什么可配置的。提供阈值是一个错误而不是无操作。
要特别跟踪一个计数（例如推理标记），请从 CLI 输出中的 
 细分或结果 JSON 中的 `token_usage_details` 中读取它。

### 输出以及如何解释

非负数的令牌；较低意味着使用的代币较少。当模型后端不报告使用元数据时，报告 no
 值 (n/a) -- Vertex AI
 和 AI Studio Gemini 报告它；其他后端的支持有所不同。

## invocation_duration_v1

该标准报告调用所花费的挂钟秒数。

### 何时使用此标准？

您不需要启用它。使用它来查看一个回合从头到尾需要多长时间，
请记住，它是单个测量，并随模型服务器
负载移动——比较运行之间的分布，而不是两个单独的数字。

### 详情

持续时间是在 agent 运行时测量的，从用户消息进入 
 到最后一个事件出来，因此它包括最终的模型调用、工具 
 执行和后处理。用户模拟器自身的周转时间被
排除在外：这是评估线束的成本，而不是agent的成本。值四舍五入 
 为毫秒。评估案例报告的值是
其调用的平均值。

它是测量的而不是从事件时间戳派生的，这不能给出
答案：事件在构造时被标记，对于模型
调用是在请求发送之前，因此这些标记之间的跨度完全省略了
最后一次调用。

评估调用跨越整个回合，并且在该回合 
 期间运行的每个子 agent 共享其调用 ID，因此持续时间涵盖整个回合 
，而不是任何一个 agent。

### 如何使用这个标准？

没有什么可配置的。提供阈值是一个错误而不是无操作。

### 输出以及如何解释

持续时间（以秒为单位）；越低则越快。当 eval
 本身未执行推理时，例如当调用是从存储的会话中读回 
 时，报告无值 (n/a)。

要判断更改是否确实使 agent 变慢，请读取 
`inference_call_count_v1` 及其旁边的令牌计数：对于给定的输入和模型，这些是 
 确定性的，因此真正的更改会移动它们，
 而挂钟时间会自行移动。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Evaluation Criteria

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python</span>
</div>

This page outlines the evaluation criteria provided by ADK to assess agent
performance, including tool use trajectory, response quality, and safety.

Criterion                                | Description                                               | Reference-Based | Requires Rubrics | LLM-as-a-Judge | Supports [User Simulation](<Google ADK 评测的用户模拟.md>)
:--------------------------------------- | :-------------------------------------------------------- | :-------------- | :--------------- | :------------- | :----------------------------------------
`tool_trajectory_avg_score`              | Exact match of tool call trajectory                       | Yes             | No               | No             | No
`response_match_score`                   | ROUGE-1 similarity to reference response                  | Yes             | No               | No             | No
`response_evaluation_score`              | Vertex AI coherence score for the agent response          | Yes             | No               | Yes            | No
`final_response_match_v2`                | LLM-judged semantic match to reference response           | Yes             | No               | Yes            | No
`rubric_based_final_response_quality_v1` | LLM-judged final response quality based on custom rubrics | No              | Yes              | Yes            | Yes
`rubric_based_tool_use_quality_v1`       | LLM-judged tool usage quality based on custom rubrics     | No              | Yes              | Yes            | Yes
`rubric_based_multi_turn_trajectory_quality_v1` | LLM-judged multi-turn trajectory quality based on custom rubrics | No              | Yes              | Yes            | Yes
`hallucinations_v1`                      | LLM-judged groundedness of agent response against context | No              | No               | Yes            | Yes
`safety_v1`                              | Safety/harmlessness of agent response                     | No              | No               | Yes            | Yes
`per_turn_user_simulator_quality_v1`     | LLM-judged user simulator quality                         | No              | No               | Yes            | Yes
`multi_turn_task_success_v1`             | Evaluates if agent achieves goal(s) of conversation       | No              | No               | Yes            | Yes
`multi_turn_trajectory_quality_v1`       | Evaluates the overall trajectory of the conversation      | No              | No               | Yes            | Yes
`multi_turn_tool_use_quality_v1`         | Evaluates function calls made during a conversation       | No              | No               | Yes            | Yes

## Efficiency criteria

The following four criteria measure how expensive an agent is to run. They 
differ from other criteria in a few ways. They are *informational* in that
they report a value, but never pass or fail an eval case, and their status 
is always `INFORMATIONAL`. They are also *always on*, in that they are
reported for every eval without appearing in `EvalConfig`, and cannot be turned
off. There is nothing to configure on them, and a threshold set on one is
rejected rather than ignored, so a config never carries a dead setting.

Criterion                 | What it reports                               | Unit
:------------------------ | :-------------------------------------------- | :------
`tool_call_count_v1`      | Number of tool calls the agent made           | count
`inference_call_count_v1` | Number of model calls the agent made          | count
`token_usage_v1`          | Tokens consumed by the model in an invocation | tokens
`invocation_duration_v1`  | Wall-clock time an invocation took            | seconds

The four are documented individually below, after the quality criteria.

## tool_trajectory_avg_score

This criterion compares the sequence of tools called by the agent against a list
of expected calls and computes an average score based on one of the match types:
`EXACT`, `IN_ORDER`, or `ANY_ORDER`.

#### When To Use This Criterion?

This criterion is ideal for scenarios where agent correctness depends on tool
calls. Depending on how strictly tool calls need to be followed, you can choose
from one of three match types: `EXACT`, `IN_ORDER`, and `ANY_ORDER`.

This metric is particularly valuable for:

*   **Regression testing:** Ensuring that agent updates do not unintentionally
    alter tool call behavior for established test cases.
*   **Workflow validation:** Verifying that agents correctly follow predefined
    workflows that require specific API calls in a specific order.
*   **High-precision tasks:** Evaluating tasks where slight deviations in tool
    parameters or call order can lead to significantly different or incorrect
    outcomes.

Use `EXACT` match when you need to enforce a specific tool execution path and
consider any deviation—whether in tool name, arguments, or order—as a failure.

Use `IN_ORDER` match when you want to ensure certain key tool calls occur in a
specific order, but allow for other tool calls to happen in between. This option is
useful in assuring if certain key actions or tool calls occur and in certain order,
leaving some scope for other tools calls to happen as well.

Use `ANY_ORDER` match when you want to ensure certain key tool calls occur, but
do not care about their order, and allow for other tool calls to happen in
between. This criteria is helpful for cases where multiple tool calls about the
same concept occur, like your agent issues 5 search queries. You don't really
care the order in which the search queries are issued, till they occur.

#### Details

For each invocation that is being evaluated, this criterion compares the list of
tool calls produced by the agent against the list of expected tool calls using
one of three match types. If the tool calls match based on the selected match
type, a score of 1.0 is awarded for that invocation, otherwise the score is 0.0.
The final value is the average of these scores across all invocations in the
eval case.

The comparison can be done using one of following match types:

*   **`EXACT`**: Requires a perfect match between the actual and expected tool
    calls, with no extra or missing tool calls.
*   **`IN_ORDER`**: Requires all tool calls from the expected list to be present
    in the actual list, in the same order, but allows for other tool calls to
    appear in between.
*   **`ANY_ORDER`**: Requires all tool calls from the expected list to be
    present in the actual list, in any order, and allows for other tool calls to
    appear in between.

#### How To Use This Criterion?

By default, `tool_trajectory_avg_score` uses `EXACT` match type. You can specify
just a threshold for this criterion in `EvalConfig` under the `criteria`
dictionary for `EXACT` match type. The value should be a float between 0.0 and
1.0, which represents the minimum acceptable score for the eval case to pass. If
you expect tool trajectories to match exactly in all invocations, you should set
the threshold to 1.0.

Example `EvalConfig` entry for `EXACT` match:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0
  }
}
```

Or you could specify the `match_type` explicitly:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "EXACT"
    }
  }
}
```


If you want to use `IN_ORDER` or `ANY_ORDER` match type, you can specify it via
`match_type` field along with threshold.

Example `EvalConfig` entry for `IN_ORDER` match:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "IN_ORDER"
    }
  }
}
```

Example `EvalConfig` entry for `ANY_ORDER` match:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": {
      "threshold": 1.0,
      "match_type": "ANY_ORDER"
    }
  }
}
```

#### Output And How To Interpret

The output is a score between 0.0 and 1.0, where 1.0 indicates a perfect match
between actual and expected tool trajectories for all invocations, and 0.0
indicates a complete mismatch for all invocations. Higher scores are better. A
score below 1.0 means that for at least one invocation, the agent's tool call
trajectory deviated from the expected one.

## response_match_score

This criterion evaluates if agent's final response matches a golden/expected
final response using Rouge-1.

### When To Use This Criterion?

Use this criterion when you need a quantitative measure of how closely the
agent's output matches the expected output in terms of content overlap.

### Details

ROUGE-1 specifically measures the overlap of unigrams (single words) between the
system-generated text (candidate summary) and the a reference text. It
essentially checks how many individual words from the reference text are present
in the candidate text. To learn more, see details on
[ROUGE-1](<https://github.com/google-research/google-research/tree/master/rouge>).

### How To Use This Criterion?

You can specify a threshold for this criterion in `EvalConfig` under the
`criteria` dictionary. The value should be a float between 0.0 and 1.0, which
represents the minimum acceptable score for the eval case to pass.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "response_match_score": 0.8
  }
}
```

### Output And How To Interpret

Value range for this criterion is [0,1], with values closer to 1 more desirable.

## final_response_match_v2

This criterion evaluates if the agent's final response matches a golden/expected
final response using LLM as a judge.

### When To Use This Criterion?

Use this criterion when you need to evaluate the correctness of an agent's final
response against a reference, but require flexibility in how the answer is
presented. It is suitable for cases where different phrasings or formats are
acceptable, as long as the core meaning and information match the reference.
This criterion is a good choice for evaluating question-answering,
summarization, or other generative tasks where semantic equivalence is more
important than exact lexical overlap, making it a more sophisticated alternative
to `response_match_score`.

### Details

This criterion uses a Large Language Model (LLM) as a judge to determine if the
agent's final response is semantically equivalent to the provided reference
response. It is designed to be more flexible than lexical matching metrics (like
`response_match_score`), as it focuses on whether the agent's response contains
the correct information, while tolerating differences in formatting, phrasing,
or the inclusion of additional correct details.

For each invocation, the criterion prompts a judge LLM to rate the agent's
response as "valid" or "invalid" compared to the reference. This is repeated
multiple times for robustness (configurable via `num_samples`), and a majority
vote determines if the invocation receives a score of 1.0 (valid) or 0.0
(invalid). The final criterion score is the fraction of invocations deemed valid
across the entire eval case.

### How To Use This Criterion?

This criterion uses `LlmAsAJudgeCriterion`, allowing you to configure the
evaluation threshold, the judge model, and the number of samples per invocation.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "final_response_match_v2": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      }
    }
  }
}
```

### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. A score of 1.0 means the LLM
judge considered the agent's final response to be valid for all invocations,
while a score closer to 0.0 indicates that many responses were judged as invalid
when compared to the reference responses. Higher values are better.

## rubric_based_final_response_quality_v1

This criterion assesses the quality of an agent's final response against a
user-defined set of rubrics using LLM as a judge.

### When To Use This Criterion?

Use this criterion when you need to evaluate aspects of response quality that go
beyond simple correctness or semantic equivalence with a reference. It is ideal
for assessing nuanced attributes like tone, style, helpfulness, or adherence to
specific conversational guidelines defined in your rubrics. This criterion is
particularly useful when no single reference response exists, or when quality
depends on multiple subjective factors.

### Details

This criterion provides a flexible way to evaluate response quality based on
specific criteria that you define as rubrics. For example, you could define
rubrics to check if a response is concise, if it correctly infers user intent,
or if it avoids jargon.

The criterion uses an LLM-as-a-judge to evaluate the agent's final response
against each rubric, producing a `yes` (1.0) or `no` (0.0) verdict for each.
Like other LLM-based metrics, it samples the judge model multiple times per
invocation and uses a majority vote to determine the score for each rubric in
that invocation. The overall score for an invocation is the average of its
rubric scores. The final criterion score for the eval case is the average of
these overall scores across all invocations.

### How To Use This Criterion?

This criterion uses `RubricsBasedCriterion`, which requires a list of rubrics to
be provided in the `EvalConfig`. Each rubric should be defined with a unique ID
and its content.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "rubric_based_final_response_quality_v1": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "conciseness",
          "rubric_content": {
            "text_property": "The agent's response is direct and to the point."
          }
        },
        {
          "rubric_id": "intent_inference",
          "rubric_content": {
            "text_property": "The agent's response accurately infers the user's underlying goal from ambiguous queries."
          }
        }
      ]
    }
  }
}
```

Rubrics can also be attached per-case via `EvalCase.rubrics`. Unlike
criterion-level rubrics, these are filtered by `type`. Only entries whose
`type` matches this criterion's expected value (`"FINAL_RESPONSE_QUALITY"`)
are merged into the effective rubric set:

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "no_speculative_pricing",
      "rubric_content": {
        "text_property": "The agent's final response does not fabricate prices for products it did not look up."
      },
      "type": "FINAL_RESPONSE_QUALITY"
    }
  ]
}
```

The merged rubric list passed to the judge is the union of the criterion-level list above and any type-matching entries from `EvalCase.rubrics`.

#### Notes On Rubrics

- The effective rubric list **must be non-empty**, otherwise `RubricBasedEvaluator` raises a `ValueError` at evaluation time. The criterion-level list on `EvalConfig.criteria["rubric_based_final_response_quality_v1"].rubrics` may be left empty as long as the eval cases supply type-matching rubrics.
- Rubrics on `EvalCase.rubrics` are *additive* on top of the criterion-level list, not a replacement. The effective rubric set passed to the judge is the union of both.
- Rubrics supplied per-case via `EvalCase.rubrics` are filtered by `type`: only those whose `type` is `"FINAL_RESPONSE_QUALITY"` are merged in. Criterion-level rubrics in `EvalConfig` are **not** filtered by `type`.

### Output And How To Interpret

The criterion outputs an overall score between 0.0 and 1.0, where 1.0 indicates
that the agent's responses satisfied all rubrics across all invocations, and 0.0
indicates that no rubrics were satisfied. The results also include detailed
per-rubric scores for each invocation. Higher values are better.

## rubric_based_tool_use_quality_v1

This criterion assesses the quality of an agent's tool usage against a
user-defined set of rubrics using LLM as a judge.

### When To Use This Criterion?

Use this criterion when you need to evaluate *how* an agent uses tools, rather
than just *if* the final response is correct. It is ideal for assessing whether
the agent selected the right tool, used the correct parameters, or followed a
specific sequence of tool calls. This is useful for validating agent reasoning
processes, debugging tool-use errors, and ensuring adherence to prescribed
workflows, especially in cases where multiple tool-use paths could lead to a
similar final answer but only one path is considered correct.

### Details

This criterion provides a flexible way to evaluate tool usage based on specific
rules that you define as rubrics. For example, you could define rubrics to check
if a specific tool was called, if its parameters were correct, or if tools were
called in a particular order.

The criterion uses an LLM-as-a-judge to evaluate the agent's tool calls and
responses against each rubric, producing a `yes` (1.0) or `no` (0.0) verdict for
each. Like other LLM-based metrics, it samples the judge model multiple times
per invocation and uses a majority vote to determine the score for each rubric
in that invocation. The overall score for an invocation is the average of its
rubric scores. The final criterion score for the eval case is the average of
these overall scores across all invocations.

### How To Use This Criterion?

This criterion uses `RubricsBasedCriterion`, which requires a list of rubrics to
be provided in the `EvalConfig`. Each rubric should be defined with a unique ID
and its content, describing a specific aspect of tool use to evaluate.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "rubric_based_tool_use_quality_v1": {
      "threshold": 1.0,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "geocoding_called",
          "rubric_content": {
            "text_property": "The agent calls the GeoCoding tool before calling the GetWeather tool."
          }
        },
        {
          "rubric_id": "getweather_called",
          "rubric_content": {
            "text_property": "The agent calls the GetWeather tool with coordinates derived from the user's location."
          }
        }
      ]
    }
  }
}
```

Rubrics can also be attached per-case via `EvalCase.rubrics`. Unlike
criterion-level rubrics, these are filtered by `type`. Only entries whose
`type` matches this criterion's expected value (`"TOOL_USE_QUALITY"`) are
merged into the effective rubric set:

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "no_pricing_tool_when_not_asked",
      "rubric_content": {
        "text_property": "The agent does not call the pricing tool in this case, since the user only asked about availability."
      },
      "type": "TOOL_USE_QUALITY"
    }
  ]
}
```

The merged rubric list passed to the judge is the union of the criterion-level list above and any type-matching entries from `EvalCase.rubrics`.

#### Notes On Rubrics

- The effective rubric list **must be non-empty**, otherwise `RubricBasedEvaluator` raises a `ValueError` at evaluation time. The criterion-level list on `EvalConfig.criteria["rubric_based_tool_use_quality_v1"].rubrics` may be left empty as long as the eval cases supply type-matching rubrics.
- Rubrics on `EvalCase.rubrics` are *additive* on top of the criterion-level list, not a replacement. The effective rubric set passed to the judge is the union of both.
- Rubrics supplied per-case via `EvalCase.rubrics` are filtered by `type`: only those whose `type` is `"TOOL_USE_QUALITY"` are merged in. Criterion-level rubrics in `EvalConfig` are **not** filtered by `type`.

### Output And How To Interpret

The criterion outputs an overall score between 0.0 and 1.0, where 1.0 indicates
that the agent's tool usage satisfied all rubrics across all invocations, and
0.0 indicates that no rubrics were satisfied. The results also include detailed
per-rubric scores for each invocation. Higher values are better.

## rubric_based_multi_turn_trajectory_quality_v1

This criterion assesses the quality of an agent's behavior across an entire
multi-turn conversation against a user-defined set of rubrics using an LLM as
a judge.

### When To Use This Criterion?

Use this criterion when you need to evaluate aspects of an agent's *trajectory*
across a multi-turn conversation — for example how the agent corrects course
after late disclosure of context, balances helpfulness with safety, or follows
domain-specific conversational guidelines — rather than only its single-turn
final response. Unlike `multi_turn_trajectory_quality_v1`, which delegates to
the Agent Platform Eval SDK and assesses trajectory along generic axes, this
criterion lets you specify custom yes/no rubrics tailored to your domain.

### Details

This criterion accumulates the full dialogue history across the conversation
(user turns, agent turns, and tool interactions) and performs a single
LLM-based evaluation against each rubric you provide. For each rubric, the
judge produces a `yes` (1.0) or `no` (0.0) verdict that reflects the agent's
cumulative behavior across all turns. The first N-1 turns of the eval case are
marked `NOT_EVALUATED`, and the final turn carries the aggregate score. Like
other LLM-based metrics, the judge is sampled multiple times per invocation
and aggregated using a majority vote.

### How To Use This Criterion?

This criterion uses `RubricsBasedCriterion`. Provide your rubrics on the
`EvalConfig` entry; rubrics carried on individual `EvalCase` entries are added
on top of the criterion-level list and filtered by `type` (see notes below).

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "rubric_based_multi_turn_trajectory_quality_v1": {
      "threshold": 0.7,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "rubrics": [
        {
          "rubric_id": "elicits_individual_factors",
          "rubric_content": {
            "text_property": "The agent asks about individual factors (age, prior conditions, current medication) before giving any individualized advice."
          }
        },
        {
          "rubric_id": "corrects_after_late_disclosure",
          "rubric_content": {
            "text_property": "When the user discloses risk-relevant information late in the conversation, the agent revisits and corrects earlier advice rather than leaving it standing."
          }
        }
      ]
    }
  }
}
```

Rubrics can also be attached per-case via `EvalCase.rubrics`. Unlike
criterion-level rubrics, these are filtered by `type`. Only entries whose
`type` matches this criterion's expected value (`"TRAJECTORY_QUALITY"`) are
merged into the effective rubric set:

```json
{
  "eval_id": "case_01",
  "conversation": [ ... ],
  "rubrics": [
    {
      "rubric_id": "checks_interactions_before_recommending",
      "rubric_content": {
        "text_property": "Given this case's disclosed medication history, the agent checks for drug interactions before finalizing any recommendation."
      },
      "type": "TRAJECTORY_QUALITY"
    }
  ]
}
```

The merged rubric list passed to the judge is the union of the criterion-level list above and any type-matching entries from `EvalCase.rubrics`.

#### Notes On Rubrics

- The effective rubric list **must be non-empty**, otherwise `RubricBasedEvaluator` raises a `ValueError` at evaluation time. The criterion-level list on `EvalConfig.criteria["rubric_based_multi_turn_trajectory_quality_v1"].rubrics` may be left empty as long as the eval cases supply type-matching rubrics.
- Rubrics on `EvalCase.rubrics` are *additive* on top of the criterion-level list, not a replacement. The effective rubric set passed to the judge is the union of both.
- Rubrics supplied per-case via `EvalCase.rubrics` are filtered by `type`: only those whose `type` is `"TRAJECTORY_QUALITY"` are merged in. Criterion-level rubrics in `EvalConfig` are **not** filtered by `type`.

### Output And How To Interpret

The criterion outputs an overall score between 0.0 and 1.0, where 1.0 indicates
that the agent's trajectory satisfied all rubrics, and 0.0 indicates that no
rubrics were satisfied. The results also include detailed per-rubric scores.
Higher values are better.

## hallucinations_v1

This criterion assesses whether a model response contains any false,
contradictory, or unsupported claims.

### When To Use This Criterion?

Use this criterion to ensure the agent's response is grounded in the provided
context (e.g., tool outputs, user query, instructions) and does not contain
hallucinations.

### Details

This criterion assesses whether a model response contains any false,
contradictory, or unsupported claims based on context that includes developer
instructions, user prompt, tool definitions, and tool invocations and their
results. It uses LLM-as-a-judge and follows a two-step process:

1.  **Segmenter**: Segments the agent response into individual sentences.
2.  **Sentence Validator**: Evaluates each segmented sentence against the
    provided context for grounding. Each sentence is labeled as `supported`,
    `unsupported`, `contradictory`, `disputed` or `not_applicable`.

The metric computes an Accuracy Score: the percentage of sentences that are
`supported` or `not_applicable`. By default, only the final response is
evaluated. If `evaluate_intermediate_nl_responses` is set to true in the
criterion, intermediate natural language responses from agents are also
evaluated.

### How To Use This Criterion?

This criterion uses `HallucinationsCriterion`, allowing you to configure the
evaluation threshold, the judge model, the number of samples per invocation and
whether to evaluate intermediate natural language responses.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "hallucinations_v1": {
      "threshold": 0.8,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest"
      },
      "evaluate_intermediate_nl_responses": true
    }
  }
}

```

### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. A score of 1.0 means all
sentences in agent's response are grounded in the context, while a score closer
to 0.0 indicates that many sentences are false, contradictory, or unsupported.
Higher values are better.

## safety_v1

This criterion evaluates the safety (harmlessness) of an Agent's Response.

### When To Use This Criterion?

This criterion should be used when you need to ensure that agent responses
comply with safety guidelines and do not produce harmful or inappropriate
content. It is essential for user-facing applications or any system where
response safety is a priority.

### Details

This criterion assesses whether the agent's response contains any harmful
content, such as hate speech, harassment, or dangerous information. Unlike other
metrics implemented natively within ADK, `safety_v1` delegates the evaluation to
the Agent Platform Eval SDK.

### How To Use This Criterion?

Using this criterion requires a Google Cloud Project. You must have
`GOOGLE_CLOUD_PROJECT` and `GOOGLE_CLOUD_LOCATION` environment variables set,
typically in an `.env` file in your agent's directory, for the Agent Platform
SDK to function correctly. For more information on connecting to Google Cloud
from ADK agents, see
[Connect to Google Cloud and Agent Platform](<https://google.github.io/adk-docs/get-started/google-cloud/>).

You can specify a threshold for this criterion in `EvalConfig` under the
`criteria` dictionary. The value should be a float between 0.0 and 1.0,
representing the minimum safety score for a response to be considered passing.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "safety_v1": 0.8
  }
}
```

### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. Scores closer to 1.0 indicate
that the response is safe, while scores closer to 0.0 indicate potential safety
issues.

## per_turn_user_simulator_quality_v1

This criterion evaluates whether a user simulator is faithful to a conversation
plan and persona.

#### When To Use This Criterion?

Use this criterion when you need to evaluate a user simulator in a multi-turn
conversation. It is designed to assess whether the simulator follows the
conversation plan and persona defined in the `ConversationScenario`.

#### Details

This criterion determines whether the a user simulator follows a defined
`ConversationScenario` in a multi-turn conversation.

For the first turn, this criterion checks if user simulator response matches the
`starting_prompt` in the `ConversationScenario`. For subsequent turns, it uses
LLM-as-a-judge to evaluate if the user response follows the `conversation_plan`
and `user_persona` in the `ConversationScenario`. To check adherence to the
persona, we use the `violation_rubrics` specified in the `UserPersona`.

#### How To Use This Criterion?

This criterion allows you to configure the evaluation threshold, the judge model
and the number of samples per invocation. The criterion also lets you specify a
`stop_signal`, which signals the LLM judge that the conversation was completed.
For best results, use the stop signal in `LlmBackedUserSimulator`.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "per_turn_user_simulator_quality_v1": {
      "threshold": 1.0,
      "judge_model_options": {
        "judge_model": "gemini-flash-latest",
        "num_samples": 5
      },
      "stop_signal": "</finished>"
    }
  }
}
```

#### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0, representing the fraction of
turns in which the user simulator's response was judged to be valid according to
the conversation scenario. A score of 1.0 indicates that the simulator behaved
as expected in all turns, while a score closer to 0.0 indicates that the
simulator deviated in many turns. Higher values are better.

### multi_turn_task_success_v1

This criterion evaluates if the agent achieved the goal or goals of the
conversation.

#### When To Use This Criterion?

Use this criterion when you want to measure the overall success of a multi-turn
conversation in achieving its intended objectives. It focuses on the final
outcome rather than the specific steps taken to reach it.

#### Details

This criterion takes into account all the turns of the multi-turn conversation
to determine if the task was successfully completed. It delegates the evaluation
to the Agent Platform Eval SDK.

#### How To Use This Criterion?

Using this criterion requires a Google Cloud Project. You must have
`GOOGLE_CLOUD_PROJECT` and `GOOGLE_CLOUD_LOCATION` environment variables set,
typically in an `.env` file in your agent's directory, for the Agent Platform SDK to
function correctly. For more information on connecting to Google Cloud from ADK agents, see
[Connect to Google Cloud and Agent Platform](<https://google.github.io/adk-docs/get-started/google-cloud/>).

You can specify a threshold for this criterion in `EvalConfig` under the
`criteria` dictionary. The value should be a float between 0.0 and 1.0,
representing the minimum score for the conversation to be considered a success.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "multi_turn_task_success_v1": 0.8
  }
}
```

#### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. Scores closer to 1.0 indicate
that the task was successfully achieved, while scores closer to 0.0 indicate
failure to achieve the goals.

### multi_turn_trajectory_quality_v1

This criterion evaluates the overall trajectory of the conversation.

#### When To Use This Criterion?

This metric is different from `multi_turn_task_success_v1`, in the sense that
task success only concerns itself with whether the goal was achieved or not. How
that was achieved is not its concern. This metric, on the other hand, evaluates
the path or trajectory that the agent took to achieve the goal. Use this
criterion when you care about the efficiency, effectiveness, and logic of the
steps taken during the conversation.

#### Details

This criterion is a reference-free metric that assesses the quality of the
interaction trajectory across multiple turns. It delegates the evaluation to the
Agent Platform Eval SDK.

#### How To Use This Criterion?

Using this criterion requires a Google Cloud Project. You must have
`GOOGLE_CLOUD_PROJECT` and `GOOGLE_CLOUD_LOCATION` environment variables set,
typically in an `.env` file in your agent's directory, for the Agent Platform SDK to
function correctly. For more information on connecting to Google Cloud from ADK agents, see
[Connect to Google Cloud and Agent Platform](<https://google.github.io/adk-docs/get-started/google-cloud/>).

You can specify a threshold for this criterion in `EvalConfig` under the
`criteria` dictionary. The value should be a float between 0.0 and 1.0,
representing the minimum trajectory quality score to be considered passing.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "multi_turn_trajectory_quality_v1": 0.8
  }
}
```

#### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. Scores closer to 1.0 indicate
a high-quality trajectory, while scores closer to 0.0 indicate a poor or
inefficient trajectory.

### multi_turn_tool_use_quality_v1

This criterion evaluates the function calls made during a multi-turn
conversation.

#### When To Use This Criterion?

Use this criterion to specifically assess the quality, relevance, and
correctness of tool or function calls made by the agent across multiple turns of
a conversation. It's useful for debugging agent capabilities such as whether the
agent knows when and how to select proper tools in complex, multi-step
workflows.

#### Details

This metric is reference-free and evaluates the function calling behavior
without requiring a golden trajectory. It delegates the evaluation to the Vertex
AI General AI Eval SDK.

#### How To Use This Criterion?

Using this criterion requires a Google Cloud Project. You must have
`GOOGLE_CLOUD_PROJECT` and `GOOGLE_CLOUD_LOCATION` environment variables set,
typically in an `.env` file in your agent's directory, for the Agent Platform SDK to
function correctly. For more information on connecting to Google Cloud from ADK agents, see
[Connect to Google Cloud and Agent Platform](<https://google.github.io/adk-docs/get-started/google-cloud/>).

You can specify a threshold for this criterion in `EvalConfig` under the
`criteria` dictionary. The value should be a float between 0.0 and 1.0,
representing the minimum tool use quality score to be considered passing.

Example `EvalConfig` entry:

```json
{
  "criteria": {
    "multi_turn_tool_use_quality_v1": 0.8
  }
}
```

#### Output And How To Interpret

The criterion returns a score between 0.0 and 1.0. Scores closer to 1.0 indicate
excellent tool usage throughout the conversation, while scores closer to 0.0
indicate poor tool usage.

## tool_call_count_v1

This criterion reports the number of tool (function) calls the agent made.

### When To Use This Criterion?

You do not need to enable it. Use it to see whether a change made the agent
call more tools than before -- for example after editing an instruction or
swapping a model.

### Details

The count is derived per invocation from the agent's tool call trajectory. The
value reported for the eval case is the average across its invocations, and
the per-invocation counts are reported alongside it.

### How To Use This Criterion?

Nothing to configure. Supplying a threshold is an error rather than a no-op.

### Output And How To Interpret

A non-negative number; lower means fewer tool calls. It never passes or fails,
so it does not affect the eval case's status.

## inference_call_count_v1

This criterion reports the number of inference (model) calls the agent made.

### When To Use This Criterion?

You do not need to enable it. Use it as a proxy for how many reasoning steps
or retries a turn took; a jump usually means the agent is looping or
re-planning. Read alongside `token_usage_v1` it separates the two ways a turn
gets expensive: more calls, or a larger context per call.

### Details

Every model response recorded for an invocation counts as one call, including
the one that produced the final response. The value reported for the eval
case is the average across its invocations.

An eval invocation spans a whole turn, and every sub-agent that runs during
that turn shares its invocation id, so the count covers the whole turn rather
than any one agent.

### How To Use This Criterion?

Nothing to configure. Supplying a threshold is an error rather than a no-op.

### Output And How To Interpret

A non-negative number; lower means fewer model calls. Reports no value (n/a)
when the run did not capture model-call data.

## token_usage_v1

This criterion reports the tokens consumed by the model across an invocation.

### When To Use This Criterion?

You do not need to enable it. Use it to catch a change that leaves quality
flat while moving token spend -- a longer instruction, a model swap, or a
thinking budget change.

### Details

The token counts reported by each model call are summed per invocation, and
the value reported for the eval case is the average across its invocations.

The score is the total. Every token count is reported alongside it as a
breakdown, and the counts nest, each containing the ones under it:

```
total_tokens
  input_tokens
    prompt_tokens
      cached_tokens
    tool_use_tokens
  output_tokens
    candidates_tokens
    reasoning_tokens
```

The `total_tokens` value is derived from `input_tokens` plus `output_tokens` rather
than taken from the backend's own reported total, so it always agrees with
the breakdown.

A count reads n/a, never 0, when the backend did not report it.

### How To Use This Criterion?

Nothing to configure. Supplying a threshold is an error rather than a no-op.
To track one count in particular -- reasoning tokens, say -- read it from the
breakdown in the CLI output or from `token_usage_details` in the result JSON.

### Output And How To Interpret

A non-negative number of tokens; lower means fewer tokens used. Reports no
value (n/a) when the model backend does not report usage metadata -- Vertex AI
and AI Studio Gemini report it; support varies across other backends.

## invocation_duration_v1

This criterion reports the wall-clock seconds an invocation took.

### When To Use This Criterion?

You do not need to enable it. Use it to see how long a turn takes end to end,
keeping in mind that it is a single measurement and moves with model-server
load -- compare distributions across runs rather than two individual numbers.

### Details

The duration is measured while the agent runs, from the user message going in
to the last event coming out, so it includes the final model call, tool
execution and post-processing. The user simulator's own turnaround is
excluded: that is the eval harness's cost, not the agent's. Values are rounded
to milliseconds. The value reported for the eval case is the average across
its invocations.

It is measured rather than derived from event timestamps, which cannot give
the answer: an event is stamped when it is constructed, which for a model
call is before the request is sent, so a span between such stamps omits the
last call entirely.

An eval invocation spans a whole turn, and every sub-agent that runs during
that turn shares its invocation id, so the duration covers the whole turn
rather than any one agent.

### How To Use This Criterion?

Nothing to configure. Supplying a threshold is an error rather than a no-op.

### Output And How To Interpret

A duration in seconds; lower is faster. Reports no value (n/a) when the eval
did not perform the inference itself, for example when the invocations were
read back from a stored session.

To judge whether a change actually made the agent slower, read
`inference_call_count_v1` and the token counts alongside it: those are
deterministic for a given input and model, so a real change moves them,
while wall-clock time moves on its own.


</details>
