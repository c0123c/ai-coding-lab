# Google ADK 自定义评测指标

介绍同步和异步指标的定义、配置与使用示例，帮助为特定 Agent 应用设计评价标准。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-07](<https://github.com/google/adk-docs/commit/839e898fcf0176c8c323aeb4cf806e2fc1b14c15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/evaluate/custom_metrics.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/Google ADK 自定义评测指标.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

## Agent 评估的自定义指标

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v1.18.0</span>
</div> 支持

如果您需要针对您的特定用例或内置选项未涵盖的
域定制的专门指标，您可以定义自己的
自定义指标。

## 定义自定义指标

自定义指标是 Python 评估一个函数 agent的表现
给定评估案例并返回
[`EvaluationResult`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/evaluator.py>).
该函数接收 [`EvalMetric`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_metrics.py>),
的清单 [`Invocation`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)
所产生的物体 agent 在评估运行期间，以及可选的列表
预期的调用或
[`ConversationScenario`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)
如 eval 情况中所定义。

每个 `Invocation` 对象代表 
 用户和 agent 之间的单轮交互，并包含工具轨迹、
 中间响应和该轮最终响应等信息。

您的自定义指标函数必须具有以下签名：

```python
from typing import Optional
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.evaluator import EvaluationResult

def my_custom_metric_function(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  ...
```

该函数应返回一个 `EvaluationResult` 对象，其中填充了 
`overall_score`、`overall_eval_status` 和 `per_invocation_results` 字段 
。

### 示例

下面是一个自定义指标的简单示例，用于检查 agent 每轮的最终 
 响应是否与预期的最终响应完全匹配。

```python
import statistics
from typing import Optional

from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.eval_metrics import EvalStatus
from google.adk.evaluation.evaluator import EvaluationResult, PerInvocationResult

def check_final_response_exact_match(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  """Checks if the final response of the first turn matches the expected
  response."""
  if not expected_invocations:
    return EvaluationResult(overall_score=0.0, overall_eval_status=EvalStatus.NOT_EVALUATED)

  per_invocation_results = []

  for actual, expected in zip(actual_invocations, expected_invocations):
    actual_final_response = "".join([part.text for part in actual.final_response.parts])
    expected_final_response = "".join([part.text for part in expected.final_response.parts])
    score = 1.0 if actual_final_response == expected_final_response else 0.0
    eval_status = EvalStatus.PASSED if score else EvalStatus.FAILED
    invocation_result = PerInvocationResult(
        actual_invocation=actual,
        expected_invocation=expected,
        score=score,
        eval_status=eval_status
    )
    per_invocation_results.append(invocation_result)

  average_score = statistics.mean(result.score for result in per_invocation_results)

  threshold = eval_metric.criterion.threshold
  overall_eval_status = (
    EvalStatus.PASSED if average_score >= threshold else EvalStatus.FAILED
  )
  return EvaluationResult(
      overall_score=average_score,
      overall_eval_status=overall_eval_status,
      per_invocation_results=per_invocation_results,
  )
```

#### 异步指标

如果您的自定义指标需要进行异步调用，例如调用 API、
，您可以将其定义为 `async` 函数。

以下是自定义指标函数的示例，该函数使用伪造的异步 
 脏话检查器 API 来检查 agent 响应是否包含脏话。

```python
import asyncio
import statistics
from typing import Optional

from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.eval_metrics import EvalStatus
from google.adk.evaluation.evaluator import EvaluationResult, PerInvocationResult

class ProfanityChecker:
  """A fake profanity checker that mimics an async API."""

  async def check(self, text: str) -> bool:
    """Returns True if profanity is detected, False otherwise."""
    await asyncio.sleep(0.01)
    return "profanity" in text.lower()

profanity_checker = ProfanityChecker()

async def check_for_profanity(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  """Checks if the agent response contains profanity using a fake async API."""
  per_invocation_results = []

  for invocation in actual_invocations:
    agent_response = "".join(part.text for part in invocation.final_response.parts)
    has_profanity = await profanity_checker.check(agent_response)
    score = 0.0 if has_profanity else 1.0
    eval_status = EvalStatus.FAILED if has_profanity else EvalStatus.PASSED

    invocation_result = PerInvocationResult(
        actual_invocation=invocation,
        score=score,
        eval_status=eval_status
    )
    per_invocation_results.append(invocation_result)

  scores = [
      result.score
      for result in per_invocation_results
      if result.eval_status != EvalStatus.NOT_EVALUATED
  ]

  average_score = statistics.mean(scores)

  threshold = eval_metric.criterion.threshold
  overall_eval_status = (
      EvalStatus.PASSED if average_score >= threshold else EvalStatus.FAILED
  )
  return EvaluationResult(
      overall_score=average_score,
      overall_eval_status=overall_eval_status,
      per_invocation_results=per_invocation_results,
  )
```

## 使用自定义指标

要在 `adk eval` 的评估运行中使用自定义指标，您需要 
 在 `EvalConfig` JSON 文件中指定它。

1.  将您的自定义指标添加为评估 `criteria` 之一。关键是你的指标
名称，值为通过阈值。
2.  将 `custom_metrics` 对象添加到 `EvalConfig`。在这个对象内部，添加一个
每个自定义指标的条目，其中键是指标名称（与 `criteria` 中的 
 匹配），值是包含 `code_config` 的对象。
3.  `code_config` 对象应包含一个带有字符串的 `name` 字段
表示自定义指标函数的 Python 导入路径，采用 
 格式 `my.module.my_function`。

### 示例 `EvalConfig`

假设您的 `check_final_response_exact_match` 函数在 
`my_agent.metrics.py` 中定义，您的 `EvalConfig` 可能如下所示：

```json
{
  "criteria": {
    "my_check_final_response_exact_match": {
      "threshold": 0.8
    },
    "tool_trajectory_avg_score": {
      "threshold": 1.0
    }
  },
  "custom_metrics": {
    "my_check_final_response_exact_match": {
      "code_config": {
        "name": "my_agent.metrics.check_final_response_exact_match"
      }
    }
  }
}
```

使用此配置，当您运行
`adk eval --config_file_path=<path_to_this_config>`时，ADK将为每个eval案例执行
`check_final_response_exact_match`，并检查返回的
分数是否> = 0.8以将`my_check_final_response_exact_match`标准标记为
已通过或失败。

### 提供指标信息

您可以选择通过在 `EvalConfig` 的 
 自定义指标定义中添加 [`MetricInfo`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_metrics.py#L369>) 对象来提供有关自定义指标的元数据，例如其 
 描述和值范围。如果未提供`metric_info`，
ADK将使用默认值（`min_value`=0.0，`max_value`=1.0）。

ADK 工具可以使用此信息来显示和结果聚合 
 目的。

以下是为自定义指标提供 `metric_info` 的示例，该指标返回 
 -1.0 和 1.0 之间的分数：

```json
{
  "criteria": {
    "my_metric": {
      "threshold": 0.5
    }
  },
  "custom_metrics": {
    "my_metric": {
      "code_config": {
        "name": "my_agent.metrics.my_metric_function"
      },
      "metric_info": {
        "metric_name": "my_metric",
        "description": "This metric evaluates XYZ and returns a score between -1.0 and 1.0.",
        "metric_value_info": {
          "interval": {
            "min_value": -1.0,
            "max_value": 1.0
          }
        }
      }
    }
  }
}
```


## 原文对照

<details>
<summary>展开完整原文</summary>

## Custom Metrics for Agent Evaluation

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v1.18.0</span>
</div>

If you require specialized metrics tailored to your specific use cases or
domains that are not covered by built-in options, you can define your own
custom metrics.

## Define a Custom Metric

A custom metric is a Python function that evaluates an agent's performance on a
given evaluation case and returns an
[`EvaluationResult`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/evaluator.py>).
The function receives the [`EvalMetric`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_metrics.py>),
the list of [`Invocation`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)
objects produced by the agent during the evaluation run, and optionally, a list
of expected invocations or a
[`ConversationScenario`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)
as defined in the eval case.

Each `Invocation` object represents a single turn of interaction between the
user and the agent, and contains information such as tool trajectory,
intermediate responses, and final response for that turn.

Your custom metric function must have the following signature:

```python
from typing import Optional
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.evaluator import EvaluationResult

def my_custom_metric_function(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  ...
```

The function should return an `EvaluationResult` object with the
`overall_score`, `overall_eval_status`, and `per_invocation_results` fields
populated.

### Example

Here is a simple example of a custom metric that checks if the agent's final
response in each turn matches the expected final response exactly.

```python
import statistics
from typing import Optional

from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.eval_metrics import EvalStatus
from google.adk.evaluation.evaluator import EvaluationResult, PerInvocationResult

def check_final_response_exact_match(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  """Checks if the final response of the first turn matches the expected
  response."""
  if not expected_invocations:
    return EvaluationResult(overall_score=0.0, overall_eval_status=EvalStatus.NOT_EVALUATED)

  per_invocation_results = []

  for actual, expected in zip(actual_invocations, expected_invocations):
    actual_final_response = "".join([part.text for part in actual.final_response.parts])
    expected_final_response = "".join([part.text for part in expected.final_response.parts])
    score = 1.0 if actual_final_response == expected_final_response else 0.0
    eval_status = EvalStatus.PASSED if score else EvalStatus.FAILED
    invocation_result = PerInvocationResult(
        actual_invocation=actual,
        expected_invocation=expected,
        score=score,
        eval_status=eval_status
    )
    per_invocation_results.append(invocation_result)

  average_score = statistics.mean(result.score for result in per_invocation_results)

  threshold = eval_metric.criterion.threshold
  overall_eval_status = (
    EvalStatus.PASSED if average_score >= threshold else EvalStatus.FAILED
  )
  return EvaluationResult(
      overall_score=average_score,
      overall_eval_status=overall_eval_status,
      per_invocation_results=per_invocation_results,
  )
```

#### Async Metric

If your custom metric needs to make asynchronous calls, such as calling an API,
you can define it as an `async` function.

The following is an example of a custom metric function that uses a fake async
profanity checker API to check if the agent response contains profanity.

```python
import asyncio
import statistics
from typing import Optional

from google.adk.evaluation.conversation_scenarios import ConversationScenario
from google.adk.evaluation.eval_case import Invocation
from google.adk.evaluation.eval_metrics import EvalMetric
from google.adk.evaluation.eval_metrics import EvalStatus
from google.adk.evaluation.evaluator import EvaluationResult, PerInvocationResult

class ProfanityChecker:
  """A fake profanity checker that mimics an async API."""

  async def check(self, text: str) -> bool:
    """Returns True if profanity is detected, False otherwise."""
    await asyncio.sleep(0.01)
    return "profanity" in text.lower()

profanity_checker = ProfanityChecker()

async def check_for_profanity(
    eval_metric: EvalMetric,
    actual_invocations: list[Invocation],
    expected_invocations: Optional[list[Invocation]],
    conversation_scenario: Optional[ConversationScenario],
) -> EvaluationResult:
  """Checks if the agent response contains profanity using a fake async API."""
  per_invocation_results = []

  for invocation in actual_invocations:
    agent_response = "".join(part.text for part in invocation.final_response.parts)
    has_profanity = await profanity_checker.check(agent_response)
    score = 0.0 if has_profanity else 1.0
    eval_status = EvalStatus.FAILED if has_profanity else EvalStatus.PASSED

    invocation_result = PerInvocationResult(
        actual_invocation=invocation,
        score=score,
        eval_status=eval_status
    )
    per_invocation_results.append(invocation_result)

  scores = [
      result.score
      for result in per_invocation_results
      if result.eval_status != EvalStatus.NOT_EVALUATED
  ]

  average_score = statistics.mean(scores)

  threshold = eval_metric.criterion.threshold
  overall_eval_status = (
      EvalStatus.PASSED if average_score >= threshold else EvalStatus.FAILED
  )
  return EvaluationResult(
      overall_score=average_score,
      overall_eval_status=overall_eval_status,
      per_invocation_results=per_invocation_results,
  )
```

## Use a Custom Metric

To use your custom metric in an evaluation run with `adk eval`, you need to
specify it in your `EvalConfig` JSON file.

1.  Add your custom metric as one of the eval `criteria`. The key is your metric
    name, and the value is the passing threshold.
2.  Add a `custom_metrics` object to `EvalConfig`. Inside this object, add an
    entry for each custom metric, where the key is the metric name (matching the
    one in `criteria`) and the value is an object containing `code_config`.
3.  The `code_config` object should contain a `name` field with a string
    representing the Python import path to your custom metric function, in the
    format `my.module.my_function`.

### Example `EvalConfig`

Assuming your `check_final_response_exact_match` function is defined in
`my_agent.metrics.py`, your `EvalConfig` might look like this:

```json
{
  "criteria": {
    "my_check_final_response_exact_match": {
      "threshold": 0.8
    },
    "tool_trajectory_avg_score": {
      "threshold": 1.0
    }
  },
  "custom_metrics": {
    "my_check_final_response_exact_match": {
      "code_config": {
        "name": "my_agent.metrics.check_final_response_exact_match"
      }
    }
  }
}
```

With this configuration, when you run
`adk eval --config_file_path=<path_to_this_config>`, ADK will execute
`check_final_response_exact_match` for each eval case, and check if the returned
score is >= 0.8 to mark the `my_check_final_response_exact_match` criterion as
passed or failed.

### Providing Metric Information

You can optionally provide metadata about your custom metric, such as its
description and value range, by adding a [`MetricInfo`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_metrics.py#L369>) object within your
custom metric definition in `EvalConfig`. If `metric_info` is not provided,
ADK will use default values (`min_value`=0.0, `max_value`=1.0).

This information can be used by ADK tools for display and result aggregation
purposes.

Here is an example of providing `metric_info` for a custom metric that returns
a score between -1.0 and 1.0:

```json
{
  "criteria": {
    "my_metric": {
      "threshold": 0.5
    }
  },
  "custom_metrics": {
    "my_metric": {
      "code_config": {
        "name": "my_agent.metrics.my_metric_function"
      },
      "metric_info": {
        "metric_name": "my_metric",
        "description": "This metric evaluates XYZ and returns a score between -1.0 and 1.0.",
        "metric_value_info": {
          "interval": {
            "min_value": -1.0,
            "max_value": 1.0
          }
        }
      }
    }
  }
}
```


</details>
