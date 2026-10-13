# 为什么及如何评测 Google ADK Agent

解释概率性 Agent 与传统测试的差别，介绍测试文件、评测集、指标、用户模拟及网页和自动化评测流程。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-10-01](<https://github.com/google/adk-docs/commit/f82eee54c5527c63dcb9e38fe760a508c56812ae>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/evaluate/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/evaluate/为什么及如何评测 Google ADK Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 为什么评价agents

<div class="language-support-tag">
 <span class="lst-supported">受 ADK</span><span class="lst-python">Python</span>
</div> 支持

在传统的软件开发中，单元测试和集成测试提供了代码按预期运行并在更改后保持稳定的信心。这些测试提供了明确的“pass/fail”信号，指导进一步的开发。然而，LLM agents 引入了一定程度的可变性，使得传统的测试方法变得不够。

由于模型的概率性质，确定性“pass/fail”断言通常不适合评估 agent 性能。相反，我们需要对最终输出和 agent 的轨迹 \- 达成解决方案所采取的步骤序列进行定性评估。这涉及评估 agent 决策的质量、其推理过程和最终结果。

这似乎需要做很多额外的工作，但自动化评估的投资很快就会得到回报。如果您打算超越原型，这是强烈推荐的最佳实践。

![intro_components.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/evaluate_agent.png>)

## 准备 agent 评估

在自动化 agent 评估之前，定义明确的目标和成功标准：

* **定义成功：**什么构成了您的agent的成功结果？
* **确定关键任务：**您的agent必须完成哪些基本任务？
* **选择相关指标：** 您将跟踪哪些指标来衡量绩效？

这些考虑因素将指导评估场景的创建，并能够有效监控实际部署中的 agent 行为。

## 评价什么？

为了弥合概念验证和生产就绪 AI agent 之间的差距，强大的自动化评估框架至关重要。与评估生成模型不同，评估生成模型的重点主要在于最终输出，agent 评估需要对决策过程有更深入的了解。 Agent 评估可以分为两个部分：

1. **评估轨迹和工具使用：**分析agent为达成解决方案而采取的步骤，包括其工具选择、策略及其方法的效率。
2. **评估最终响应：**评估agent最终输出的质量、相关性和正确性。

轨迹只是 agent 返回给用户之前所采取的步骤列表。我们可以将其与我们期望 agent 采取的步骤列表进行比较。

### 评估轨迹和工具使用

在响应用户之前，agent 通常会执行一系列操作，我们将其称为“轨迹”。它可能会将用户输入与会话历史记录进行比较，以消除术语的歧义，或者查找策略文档、搜索知识库或调用 API 来保存票证。我们称之为行动的“轨迹”。评估 agent 的性能需要将其实际轨迹与预期或理想轨迹进行比较。此比较可以揭示 agent 流程中的错误和低效率。预期轨迹代表基本事实 \- - 我们预期 agent 应该采取的步骤列表。

例如：

```python
# Trajectory evaluation will compare
expected_steps = ["determine_intent", "use_tool", "review_results", "report_generation"]
actual_steps = ["determine_intent", "use_tool", "review_results", "report_generation"]
```

ADK 提供基于事实真相和基于标题的工具使用评估指标。要为您的 agent 的具体要求和目标选择适当的指标，请参阅我们的 [ 建议 ](<#标准建议>)。

## 如何使用 ADK 进行评估

ADK 提供了两种根据预定义数据集和评估标准评估 agent 性能的方法。虽然概念上相似，但它们在可以处理的数据量方面有所不同，这通常决定了每种数据的适当用例。

### 使用测试文件进行评估

此方法涉及创建单独的测试文件，每个测试文件代表一个简单的 agent 模型交互（一个会话）。它在活跃的 agent 开发过程中最有效，作为单元测试的一种形式。这些测试旨在快速执行，并应重点关注简单会话的复杂性。每个测试文件包含一个会话，该会话可能包含多个回合。回合代表用户和 agent 之间的单次交互。每回合包括

-   `User Content`：用户发出查询。
-   `Expected Intermediate Tool Use Trajectory`：该工具调用我们期望的
agent为了正确响应用户的查询而做出。
-   `Expected Intermediate Agent Responses`：这些是自然语言
agent（或子agents）在朝着
移动时生成的响应生成最终答案。这些自然语言响应通常是多 agent 系统的 
 工件，其中您的根 agent 依赖于子 agents 来实现目标。这些中间响应可能对最终用户
感兴趣，也可能不感兴趣，但对于系统的developer/owner来说，具有
至关重要，因为它们让您确信agent通过正确的路径
生成最终响应。
-   `Final Response`：agent 的预期最终响应。

您可以为该文件指定任意名称，例如 `evaluation.test.json`。框架仅检查`.test.json`后缀，文件名的前面部分不受限制。测试文件由正式的 Pydantic 数据模型支持。两个关键模式文件是
[Eval Set](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>)和
[Eval Case](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)。
这里是一个包含一些示例的测试文件：

！！！笔记

包含注释是出于解释目的，应将其删除以使 JSON 有效。

```json
# Do note that some fields are removed for sake of making this doc readable.
{
  "eval_set_id": "home_automation_agent_light_on_off_set",
  "name": "",
  "description": "This is an eval set that is used for unit testing `x` behavior of the Agent",
  "eval_cases": [
    {
      "eval_id": "eval_case_id",
      "conversation": [
        {
          "invocation_id": "b7982664-0ab6-47cc-ab13-326656afdf75", # Unique identifier for the invocation.
          "user_content": { # Content provided by the user in this invocation. This is the query.
            "parts": [
              {
                "text": "Turn off device_2 in the Bedroom."
              }
            ],
            "role": "user"
          },
          "final_response": { # Final response from the agent that acts as a reference of benchmark.
            "parts": [
              {
                "text": "I have set the device_2 status to off."
              }
            ],
            "role": "model"
          },
          "intermediate_data": {
            "tool_uses": [ # Tool use trajectory in chronological order.
              {
                "args": {
                  "location": "Bedroom",
                  "device_id": "device_2",
                  "status": "OFF"
                },
                "name": "set_device_info"
              }
            ],
            "intermediate_responses": [] # Any intermediate sub-agent responses.
          }
        }
      ],
      "session_input": { # Initial session input.
        "app_name": "home_automation_agent",
        "user_id": "test_user",
        "state": {}
      }
    }
  ]
}
```

测试文件可以组织到文件夹中。或者，文件夹还可以包含指定评估标准的 `test_config.json` 文件。

#### 如何迁移不受 Pydantic 模式支持的测试文件？

！！！笔记

如果您的测试文件不符合 [EvalSet](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) 架构文件，那么本节与您相关。

请使用 `AgentEvaluator.migrate_eval_data_to_new_schema` 将您的 
 现有 `*.test.json` 文件迁移到 Pydantic 支持的架构。

该实用程序采用当前的测试数据文件和可选的初始会话 
 文件，并生成一个输出 json 文件，其中包含以新的 
 格式序列化的数据。鉴于新模式更具凝聚力，旧的测试数据文件 
 和初始会话文件都可以被忽略（或删除）。

### 使用 Evalset 文件进行评估

评估集方法利用称为“评估集”的专用数据集来评估 agent 模型交互。与测试文件类似，评估集包含示例交互。但是，评估集可以包含多个可能很长的会话，这使其成为模拟复杂的多轮对话的理想选择。由于其能够表示复杂会话，该评估集非常适合集成测试。由于这些测试的广泛性，它们的运行频率通常低于单元测试。

评估集文件包含多个“评估”，每个评估代表一个不同的会话。每个评估由一个或多个“回合”组成，其中包括用户查询、预期工具使用、预期中间 agent 响应和参考响应。这些字段的含义与测试文件方法中的含义相同。或者，评估可以定义 * 对话场景 *，用于 [ 动态模拟 ](<Google ADK 评测的用户模拟.md>) 用户与 agent 的交互。每个评估都由唯一的名称标识。此外，每个 eval 包括关联的初始会话状态。

手动创建评估集可能很复杂，因此提供了 UI 工具来帮助捕获相关会话并轻松将它们转换为评估集中的评估。了解有关使用网络 UI 进行评估的更多信息如下。这是包含两个会话的示例评估集。评估集文件由正式的 Pydantic 数据模型支持。两个关键架构文件是
[Eval Set](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>)和
[Eval Case](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>)。

！！！笔记

包含注释是出于解释目的，应将其删除以使 JSON 有效。

```json
# Do note that some fields are removed for sake of making this doc readable.
{
  "eval_set_id": "eval_set_example_with_multiple_sessions",
  "name": "Eval set with multiple sessions",
  "description": "This eval set is an example that shows that an eval set can have more than one session.",
  "eval_cases": [
    {
      "eval_id": "session_01",
      "conversation": [
        {
          "invocation_id": "e-0067f6c4-ac27-4f24-81d7-3ab994c28768",
          "user_content": {
            "parts": [
              {
                "text": "What can you do?"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {

                "text": "I can roll dice of different sizes and check if numbers are prime."
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [],
            "intermediate_responses": []
          }
        }
      ],
      "session_input": {
        "app_name": "hello_world",
        "user_id": "user",
        "state": {}
      }
    },
    {
      "eval_id": "session_02",
      "conversation": [
        {
          "invocation_id": "e-92d34c6d-0a1b-452a-ba90-33af2838647a",
          "user_content": {
            "parts": [
              {
                "text": "Roll a 19 sided dice"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {
                "text": "I rolled a 17."
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [],
            "intermediate_responses": []
          }
        },
        {
          "invocation_id": "e-bf8549a1-2a61-4ecc-a4ee-4efbbf25a8ea",
          "user_content": {
            "parts": [
              {
                "text": "Roll a 10 sided dice twice and then check if 9 is a prime or not"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {
                "text": "I got 4 and 7 from the dice roll, and 9 is not a prime number.\n"
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [
              {
                "id": "adk-1a3f5a01-1782-4530-949f-07cf53fc6f05",
                "args": {
                  "sides": 10
                },
                "name": "roll_die"
              },
              {
                "id": "adk-52fc3269-caaf-41c3-833d-511e454c7058",
                "args": {
                  "sides": 10
                },
                "name": "roll_die"
              },
              {
                "id": "adk-5274768e-9ec5-4915-b6cf-f5d7f0387056",
                "args": {
                  "nums": [
                    9
                  ]
                },
                "name": "check_prime"
              }
            ],
            "intermediate_responses": [
              [
                "data_processing_agent",
                [
                  {
                    "text": "I have rolled a 10 sided die twice. The first roll is 5 and the second roll is 3.\n"
                  }
                ]
              ]
            ]
          }
        }
      ],
      "session_input": {
        "app_name": "hello_world",
        "user_id": "user",
        "state": {}
      }
    }
  ]
}
```

#### 如何迁移不受 Pydantic 模式支持的评估集文件？

！！！笔记

如果您的评估集文件不符合 [EvalSet](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) 架构文件，那么本节与您相关。

根据谁维护评估集数据，有两种途径：

1.  **由ADK UI**维护的评估集数据如果您使用ADK UI来维护您的
评估设置数据，然后*您无需执行任何操作*。

2.  **评估集数据是手动开发和维护的，并用于ADK评估CLI**A
迁移工具正在开发中，在此之前 ADK eval CLI 命令将 
 继续支持旧格式的数据。

### 通过一致性测试进行评估

`adk conformance test` 命令验证您的 AI agents 在一段时间内的行为是否一致。它通过对照基线数据验证当前的 agent 输出，确保代码库或模型的更新不会引入回归。

#### 先决条件和设置

在 `adk conformance` 命令可以执行有意义的回归测试之前，您必须建立最佳的“黄金基线”。一致性测试通过将实时 agent 行为与这些先前记录的、经过验证的交互进行比较来进行。

请按照以下工作流程准备您的环境：
##### 创建测试目录层次结构

一致性测试依赖严格的文件布局来自动发现和映射测试用例。
 使用以下结构初始化您的测试目录：

```
tests
└── category_name/
    └── test_case_name/
        ├── spec.yaml                  # Test case specification
        ├── generated-recordings.yaml   # Baseline recorded interactions
        └── generated-session.yaml      # Baseline session data

```

！！！笔记

如果您的 agent 使用服务器发送事件 (SSE)，测试框架将另外在同一文件夹中查找 `generated-recordings-sse.yaml` 和 `generated-session-sse.yaml`。

##### 定义测试规范（spec.yaml）

在目标测试文件夹中，创建一个 `spec.yaml` 文件。该文件概述了 agent 在基线记录和后续一致性运行期间将执行的初始条件、配置和用户提示。确保您的文件符合以下基本架构，这只是一个示例：

```yaml
# Example spec.yaml for a Weather Agent.
# The test case name and category are inferred from the folder structure.
description: "Verifies the agent correctly identifies location and calls the weather tool."
agent: "weather_agent"
user_messages:
  - text: "What's the temperature in San Francisco right now?"
```

#### 自动化基线

由于后台数据（例如 LLM 请求和工具调用）很复杂，因此您不应尝试手动写入或保存基线文件。相反，让 ADK 为您生成它们。

1. 启动您的 ADK Web 服务器并打开录制插件：

```shell
adk web -v --extra_plugins=google.adk.cli.plugins.recordings_plugin.RecordingsPlugin /path/to/agents
```

   2. 接下来，打开一个新的终端窗口并告诉 ADK 根据您的 spec.yaml 创建基线文件：

```shell
adk conformance record tests/category/test_name none
```

尾随流模式参数是必需的。使用 `none` 录制 `generated-recordings.yaml` 和 `generated-session.yaml`，或者使用 `sse` 录制 `generated-recordings-sse.yaml` 和 `generated-session-sse.yaml`。不支持`bidi`模式进行录制。

这会自动运行场景，记录所有交互，并将 generated-recordings.yaml 和 generated-session.yaml 文件准确保存在需要的位置。

一旦锁定这些基线文件，您的设置就完成了，并且该目录已准备好在 **Replay** 或 **Live** 模式下作为 `adk conformance` 的目标目录。

#### 它是如何运作的

* **重播模式（默认）：** 该工具运行您的 agent 并将其实时 LLM 请求、响应和工具调用直接与您之前记录的交互进行比较，以捕获意外偏差。
* **实时模式：**针对活动环境运行基于评估的验证*（注意：此模式正在进行中）*。

### 评价标准

ADK 提供了几个用于评估 agent 性能的内置标准，范围从 
 工具轨迹匹配到基于 LLM- 的响应质量评估。有关
可用标准的详细列表以及有关何时使用它们的指南，请参阅
[评估标准](<Google ADK 评测标准参考.md>)。

以下是所有可用标准的摘要：

*   **tool_trajectory_avg_score**：刀具调用轨迹精确匹配。
*   **response_match_score**：ROUGE-1 与参考响应相似。
*   **final_response_match_v2**：LLM-判断与引用的语义匹配
回应。
*   **rubric_based_final_response_quality_v1**：LLM-判定最终响应
基于自定义标准的质量。
*   **rubric_based_tool_use_quality_v1**：LLM-根据以下条件判断工具使用质量
自定义评分标准。
*   **rubric_based_multi_turn_trajectory_quality_v1**：LLM-判断多圈
基于自定义标准的轨迹质量。
*   **hallucinations_v1**：LLM-判断agent响应的真实性
上下文。
*   **safety_v1**：agent响应的Safety/harmlessness。
*   **per_turn_user_simulator_quality_v1**：LLM-判断用户模拟器质量。
*   **multi_turn_task_success_v1**：评估 agent 是否实现了目标
谈话。
*   **multi_turn_trajectory_quality_v1**：评估总体轨迹
谈话。
*   **multi_turn_tool_use_quality_v1**：评估在执行过程中进行的函数调用
谈话。

以下是*效率*标准。它们报告一个值，但从未通过或
未通过评估案例，并且每次评估都会报告它们，但不会在
`EvalConfig`中列出：

*   **tool_call_count_v1**：进行的工具调用次数。
*   **inference_call_count_v1**：模型调用次数。
*   **token_usage_v1**：消耗的代币，报告了每种类型的细分
与总数并列。
*   **invocation_duration_v1**：已测量的挂钟秒数
当agent运行时。

！！！笔记

某些标准（例如响应质量、安全性和多轮质量）
 需要 [Vertex Gen AI 评估服务 
 API](<https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/evaluation>)。
 要使用它们，请通过设置 `GOOGLE_API_KEY` 进行身份验证环境
变量，或使用Google云项目凭据
（`GOOGLE_CLOUD_PROJECT`和`GOOGLE_CLOUD_LOCATION`以及应用程序默认
凭据）。

如果没有提供评估标准，则使用以下默认配置：

* `tool_trajectory_avg_score`：默认为1.0，要求工具使用轨迹中有100%匹配。
* `response_match_score`：默认为 0.8，允许 agent 的自然语言响应存在较小的误差范围。

以下是指定自定义评估标准的 `test_config.json` 文件示例：

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.8
  }
}
```

#### 标准建议

根据您的评估目标选择标准：

*   **在CI/CD管道中启用测试或回归测试：**使用
`tool_trajectory_avg_score` 和 `response_match_score`。这些标准
快速、可预测，并且适合频繁的自动检查。
*   **评估可信参考响应：** 使用 `final_response_match_v2` 来
评估语义等价性。这种基于 LLM- 的检查比 
 精确匹配更灵活，并且可以更好地捕获 agent 的响应是否意味着 
 与参考响应相同。
*   **在没有参考响应的情况下评估响应质量：** 使用
`rubric_based_final_response_quality_v1`。当您没有
可信参考，但您可以定义良好响应的属性时，这很有用（例如，
“响应简洁”，“响应语气很有帮助”）。
*   **评估工具使用的正确性：**使用
`rubric_based_tool_use_quality_v1`。这允许您通过检查来验证 agent 的 
 推理过程，例如，是否调用了特定工具 
 或以正确的顺序调用了工具（例如，“工具 A 必须在工具 B 之前调用 
”）。
*   **检查响应是否基于上下文：**使用`hallucinations_v1`
检测 agent 是否做出与 
 可用信息（例如工具输出）不支持或相矛盾的声明。
*   **检查有害内容：**使用`safety_v1`确保agent
响应是安全的并且不违反安全政策。
*   **评估多回合目标完成情况：**使用`multi_turn_task_success_v1`来
衡量多轮对话在实现其
预期目标方面的总体成功程度。
*   **评估整体对话轨迹：**使用
`multi_turn_trajectory_quality_v1` 评估对话期间所采取步骤的效率、有效性、
 和逻辑。
*   **评估多轮工作流程中的工具使用情况：** 使用
`multi_turn_tool_use_quality_v1` 评估多轮调用的工具或函数的质量、相关性和
 正确性。
*   **跟踪 agent 的价格：** 效率标准
（`tool_call_count_v1`、`inference_call_count_v1`、`token_usage_v1`、
 `invocation_duration_v1`）会自动报告，没有
配置，也没有额外的模型调用。跨运行比较它们，以捕捉
一个保持质量不变但令牌使用量翻倍的变化。它们的评估永远不会失败 
，因此它们不会向 CI/CD 运行添加新的故障模式。

此外，还需要有关预期信息的标准 agent 工具使用
and/or 不支持与以下组合的响应
[用户模拟](<Google ADK 评测的用户模拟.md>).
受影响的标准包括 `tool_trajectory_avg_score`, `response_match_score`
和 `final_response_match_v2`。对用户模拟的支持按以下列出
支持栏中的标准 [评价标准](<Google ADK 评测标准参考.md>).

### 用户模拟

在评估会话 agents 时，使用固定的 
 组用户提示并不总是可行，因为会话可能会以意想不到的方式进行。
例如，如果 agent 需要用户提供两个值来执行任务，
它可能会要求一次提供一个值，或者
要解决此问题，ADK 允许您在 
 特定 * 对话场景 * 中测试 agent 的行为，并使用由 
 动态生成的用户提示。 AI模型。
有关如何通过用户模拟设置评估的详细信息，请参阅
[用户模拟](<Google ADK 评测的用户模拟.md>)。

## 如何使用 ADK 运行评估

作为开发者，您可以通过以下方式使用ADK评估您的agents：

- **基于网络 UI (**`adk web`**):** 通过基于网络的界面以交互方式评估 agents。
- **以编程方式 (**`pytest`**)**：使用 `pytest` 和测试文件将评估集成到您的测试管道中。
- **命令行界面 (**`adk eval`**)：** 直接从命令行对现有评估集文件运行评估。
- **一致性测试** (**`adk conformance`**):** 针对基线文件执行自动化测试以检测意外偏差或回归。

### 通过网络运行评估 UI

网络 UI 提供了一种交互式方式来评估 agents、生成评估数据集并详细检查 agent 行为。

#### 步骤1：创建并保存测试用例

1. 通过运行以下命令启动 Web 服务器：`adk web <path_to_your_agents_folder>`
2. 在 Web 界面中，选择 agent 并与其交互以创建会话。
3. 导航到界面右侧的 **Eval** 选项卡。
4. 创建一个新的评估集或选择一个现有的评估集。
5. 单击**“添加当前会话”**将对话保存为新的评估案例。

#### 步骤 2：查看和编辑您的测试用例

保存案例后，您可以在列表中单击其ID进行检查。要进行更改，请单击**编辑当前评估案例**图标（铅笔）。此交互式视图允许您：

* **修改** agent文本响应以细化测试场景。
* **从对话中删除**个人agent消息。
* **如果不再需要，请删除**整个评估案例。

![adk-eval-case.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-eval-case.gif>)

#### 步骤 3：使用自定义指标运行评估

1. 从评估集中选择一个或多个测试用例。
2. 单击**运行评估**。将出现 **EVALUATION METRIC** 对话框。
3. 在对话框中，使用滑块配置以下阈值：
    * **刀具轨迹平均得分**
    * **响应匹配得分**
4. 单击**开始**以使用您的自定义标准运行评估。评估历史记录将记录每次运行所使用的指标。

![adk-eval-config.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-eval-config.gif>)

#### 步骤 4：分析结果

运行完成后，您可以分析结果：

* **分析运行失败**：单击任何**通过**或**失败**结果。对于失败，您可以将鼠标悬停在 `Fail` 标签上，查看 ** 实际输出与预期输出 ** 的并排比较以及导致失败的分数。

### 使用跟踪视图进行调试

ADK 网页 UI 包含一个强大的 ** 跟踪 ** 选项卡，用于调试 agent 行为。此功能适用于任何 agent 会话，而不仅仅是在评估期间。

**Trace** 选项卡提供了一种详细的交互式方式来检查 agent 的执行流程。跟踪会按用户消息自动分组，从而可以轻松跟踪事件链。

每个跟踪行都是交互式的：

* ** 将 ** 悬停在跟踪行上会突出显示聊天窗口中的相应消息。
* **单击迹线行上的** 将打开一个包含四个选项卡的详细检查面板：
    * **事件**：原始事件数据。
    * **请求**：发送到模型的请求。
    * **响应**：从模型收到的响应。
    * **图表**：工具调用和agent逻辑流的可视化表示。

![adk-trace1.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-trace1.gif>)
![adk-trace2.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-trace2.gif>)

跟踪视图中的蓝色行表示该交互生成了一个事件。单击这些蓝色行将打开底部事件详细信息面板，提供对 agent 执行流程的更深入了解。

### 以编程方式运行测试

您还可以使用 **`pytest`** 运行测试文件作为集成测试的一部分。

#### 命令示例

```shell
pytest tests/integration/
```

#### 测试代码示例

以下是运行单个测试文件的 `pytest` 测试用例示例：

```py
from google.adk.evaluation.agent_evaluator import AgentEvaluator
import pytest

@pytest.mark.asyncio
async def test_with_single_test_file():
    """Test the agent's basic ability via a session file."""
    await AgentEvaluator.evaluate(
        agent_module="home_automation_agent",
        eval_dataset_file_path_or_dir="tests/integration/fixture/home_automation_agent/simple_test.test.json",
    )
```

这种方法允许您将 agent 评估集成到您的 CI/CD 管道或更大的测试套件中。如果您想指定测试的初始会话状态，可以通过将会话详细信息存储在文件中并将其传递给 `AgentEvaluator.evaluate` 方法来实现。

### 通过 CLI 运行评估

您还可以通过命令行界面 (CLI) 运行评估集文件的评估。这运行的评估与在 UI 上运行的评估相同，但它有助于自动化，即您可以添加此命令作为常规构建生成和验证过程的一部分。

这是命令：

```shell
adk eval \
    <AGENT_MODULE_FILE_PATH> \
    <EVAL_SET_FILE_PATH_OR_ID>... \
    [--config_file_path=<PATH_TO_TEST_JSON_CONFIG_FILE>] \
    [--print_detailed_results]
```

例如：

```shell
adk eval \
    samples_for_testing/hello_world \
    samples_for_testing/hello_world/hello_world_eval_set_001.evalset.json
```

以下是每个命令行参数的详细信息：

* `AGENT_MODULE_FILE_PATH`：agent目录的路径，而不是文件，其`__init__.py`公开了名为“agent”的模块。 “agent”模块包含一个`root_agent`。
* `EVAL_SET_FILE_PATH_OR_ID`：评估文件的路径，或由ADK管理并使用`adk eval_set create`创建的评估集的ID。您可以指定其中一个或多个，但不能在同一命令中混合文件路径和 eval set id。对于每个评估集，默认情况下都会运行所有评估。如果您只想运行评估集中的特定评估，请首先创建一个以逗号分隔的评估名称列表，然后将其作为后缀添加到评估集文件名或 id 中，并以冒号 `:` 分隔。
* 例如：`sample_eval_set_file.json:eval_1,eval_2,eval_3`
  `This will only run eval_1, eval_2 and eval_3 from sample_eval_set_file.json`
* `CONFIG_FILE_PATH`：配置文件的路径。
* `PRINT_DETAILED_RESULTS`：在控制台上打印详细结果。

### 运行一致性测试

您可以一次运行所有测试、运行特定测试或创建摘要报告。

#### 运行所有测试

如果您不输入特定的文件夹路径，该工具会自动在您的工作区中查找测试/文件夹并运行其中的所有内容：

```
adk conformance test
```

#### 运行特定的测试组或个别案例

传递一个或多个文件夹路径以缩小执行测试的范围：

```

# Test an entire category of tests
adk conformance test tests/core

# Test one specific case
adk conformance test tests/core/description_001

```

#### 生成 Markdown 测试报告

添加 `--generate_report` 标志以生成干净的测试摘要报告。您可以选择使用 `--report_dir parameter` 指定保存位置：

```
# Save the report in a specific folder
adk conformance test --generate_report --report_dir=reports
```

#### 使用 CI/CD 实现自动化
由于 adk 一致性测试是一个命令行工具，如果不匹配就会失败，因此它对于 CI/CD 管道非常有用。您可以将其设置为每当有人打开拉取请求时自动运行，如果任何代码更改了 agent 的预期行为，则阻止任何代码合并。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Why evaluate agents

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python</span>
</div>

In traditional software development, unit tests and integration tests provide confidence that code functions as expected and remains stable through changes. These tests provide a clear "pass/fail" signal, guiding further development. However, LLM agents introduce a level of variability that makes traditional testing approaches insufficient.

Due to the probabilistic nature of models, deterministic "pass/fail" assertions are often unsuitable for evaluating agent performance. Instead, we need qualitative evaluations of both the final output and the agent's trajectory \- the sequence of steps taken to reach the solution. This involves assessing the quality of the agent's decisions, its reasoning process, and the final result.

This may seem like a lot of extra work to set up, but the investment of automating evaluations pays off quickly. If you intend to progress beyond prototype, this is a highly recommended best practice.

![intro_components.png](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/evaluate_agent.png>)

## Prepare for agent evaluations

Before automating agent evaluations, define clear objectives and success criteria:

* **Define Success:** What constitutes a successful outcome for your agent?
* **Identify Critical Tasks:** What are the essential tasks your agent must accomplish?
* **Choose Relevant Metrics:** What metrics will you track to measure performance?

These considerations will guide the creation of evaluation scenarios and enable effective monitoring of agent behavior in real-world deployments.

## What to evaluate?

To bridge the gap between a proof-of-concept and a production-ready AI agent, a robust and automated evaluation framework is essential. Unlike evaluating generative models, where the focus is primarily on the final output, agent evaluation requires a deeper understanding of the decision-making process. Agent evaluation can be broken down into two components:

1. **Evaluate Trajectory and Tool Use:** Analyzing the steps an agent takes to reach a solution, including its choice of tools, strategies, and the efficiency of its approach.
2. **Evaluate the Final Response:** Assessing the quality, relevance, and correctness of the agent's final output.

The trajectory is just a list of steps the agent took before it returned to the user. We can compare that against the list of steps we expect the agent to have taken.

### Evaluate trajectory and tool use

Before responding to a user, an agent typically performs a series of actions, which we refer to as a 'trajectory.' It might compare the user input with session history to disambiguate a term, or lookup a policy document, search a knowledge base or invoke an API to save a ticket. We call this a ‘trajectory’ of actions. Evaluating an agent's performance requires comparing its actual trajectory to an expected, or ideal, one. This comparison can reveal errors and inefficiencies in the agent's process. The expected trajectory represents the ground truth \-- the list of steps we anticipate the agent should take.

For example:

```python
# Trajectory evaluation will compare
expected_steps = ["determine_intent", "use_tool", "review_results", "report_generation"]
actual_steps = ["determine_intent", "use_tool", "review_results", "report_generation"]
```

ADK provides both groundtruth based and rubric based tool use evaluation metrics. To select the appropriate metric for your agent's specific requirements and goals, please refer to our [recommendations](<#recommendations-on-criteria>).

## How evaluation works with ADK

ADK offers two methods for evaluating agent performance against predefined datasets and evaluation criteria. While conceptually similar, they differ in the amount of data they can process, which typically dictates the appropriate use case for each.

### Evaluate with test files

This approach involves creating individual test files, each representing a single, simple agent-model interaction (a session). It's most effective during active agent development, serving as a form of unit testing. These tests are designed for rapid execution and should focus on simple session complexity. Each test file contains a single session, which may consist of multiple turns. A turn represents a single interaction between the user and the agent. Each turn includes

-   `User Content`: The user issued query.
-   `Expected Intermediate Tool Use Trajectory`: The tool calls we expect the
    agent to make in order to respond correctly to the user query.
-   `Expected Intermediate Agent Responses`: These are the natural language
    responses that the agent (or sub-agents) generates as it moves towards
    generating a final answer. These natural language responses are usually an
    artifact of an multi-agent system, where your root agent depends on sub-agents to achieve a goal. These intermediate responses, may or may not be of
    interest to the end user, but for a developer/owner of the system, are of
    critical importance, as they give you the confidence that the agent went
    through the right path to generate final response.
-   `Final Response`: The expected final response from the agent.

You can give the file any name for example `evaluation.test.json`. The framework only checks for the `.test.json` suffix, and the preceding part of the filename is not constrained. The test files are backed by a formal Pydantic data model. The two key schema files are
[Eval Set](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) and
[Eval Case](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>).
Here is a test file with a few examples:

!!! note

    Comments are included for explanatory purposes and should be removed for the JSON to be valid.

```json
# Do note that some fields are removed for sake of making this doc readable.
{
  "eval_set_id": "home_automation_agent_light_on_off_set",
  "name": "",
  "description": "This is an eval set that is used for unit testing `x` behavior of the Agent",
  "eval_cases": [
    {
      "eval_id": "eval_case_id",
      "conversation": [
        {
          "invocation_id": "b7982664-0ab6-47cc-ab13-326656afdf75", # Unique identifier for the invocation.
          "user_content": { # Content provided by the user in this invocation. This is the query.
            "parts": [
              {
                "text": "Turn off device_2 in the Bedroom."
              }
            ],
            "role": "user"
          },
          "final_response": { # Final response from the agent that acts as a reference of benchmark.
            "parts": [
              {
                "text": "I have set the device_2 status to off."
              }
            ],
            "role": "model"
          },
          "intermediate_data": {
            "tool_uses": [ # Tool use trajectory in chronological order.
              {
                "args": {
                  "location": "Bedroom",
                  "device_id": "device_2",
                  "status": "OFF"
                },
                "name": "set_device_info"
              }
            ],
            "intermediate_responses": [] # Any intermediate sub-agent responses.
          }
        }
      ],
      "session_input": { # Initial session input.
        "app_name": "home_automation_agent",
        "user_id": "test_user",
        "state": {}
      }
    }
  ]
}
```

Test files can be organized into folders. Optionally, a folder can also include a `test_config.json` file that specifies the evaluation criteria.

#### How to migrate test files not backed by the Pydantic schema?

!!! note

    If your test files don't adhere to [EvalSet](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) schema file, then this section is relevant to you.

Please use `AgentEvaluator.migrate_eval_data_to_new_schema` to migrate your
existing `*.test.json` files to the Pydantic backed schema.

The utility takes your current test data file and an optional initial session
file, and generates a single output json file with data serialized in the new
format. Given that the new schema is more cohesive, both the old test data file
and initial session file can be ignored (or removed.)

### Evaluate with an Evalset File

The evalset approach utilizes a dedicated dataset called an "evalset" for evaluating agent-model interactions. Similar to a test file, the evalset contains example interactions. However, an evalset can contain multiple, potentially lengthy sessions, making it ideal for simulating complex, multi-turn conversations. Due to its ability to represent complex sessions, the evalset is well-suited for integration tests. These tests are typically run less frequently than unit tests due to their more extensive nature.

An evalset file contains multiple "evals," each representing a distinct session. Each eval consists of one or more "turns," which include the user query, expected tool use, expected intermediate agent responses, and a reference response. These fields have the same meaning as they do in the test file approach. Alternatively, an eval can define a *conversation scenario* which is used to [dynamically simulate](<Google ADK 评测的用户模拟.md>) a user interaction with the agent. Each eval is identified by a unique name. Furthermore, each eval includes an associated initial session state.

Creating evalsets manually can be complex, therefore UI tools are provided to help capture relevant sessions and easily convert them into evals within your evalset. Learn more about using the web UI for evaluation below. Here is an example evalset containing two sessions. The eval set files are  backed by a formal Pydantic data model. The two key schema files are
[Eval Set](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) and
[Eval Case](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_case.py>).

!!! note

    Comments are included for explanatory purposes and should be removed for the JSON to be valid.

```json
# Do note that some fields are removed for sake of making this doc readable.
{
  "eval_set_id": "eval_set_example_with_multiple_sessions",
  "name": "Eval set with multiple sessions",
  "description": "This eval set is an example that shows that an eval set can have more than one session.",
  "eval_cases": [
    {
      "eval_id": "session_01",
      "conversation": [
        {
          "invocation_id": "e-0067f6c4-ac27-4f24-81d7-3ab994c28768",
          "user_content": {
            "parts": [
              {
                "text": "What can you do?"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {

                "text": "I can roll dice of different sizes and check if numbers are prime."
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [],
            "intermediate_responses": []
          }
        }
      ],
      "session_input": {
        "app_name": "hello_world",
        "user_id": "user",
        "state": {}
      }
    },
    {
      "eval_id": "session_02",
      "conversation": [
        {
          "invocation_id": "e-92d34c6d-0a1b-452a-ba90-33af2838647a",
          "user_content": {
            "parts": [
              {
                "text": "Roll a 19 sided dice"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {
                "text": "I rolled a 17."
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [],
            "intermediate_responses": []
          }
        },
        {
          "invocation_id": "e-bf8549a1-2a61-4ecc-a4ee-4efbbf25a8ea",
          "user_content": {
            "parts": [
              {
                "text": "Roll a 10 sided dice twice and then check if 9 is a prime or not"
              }
            ],
            "role": "user"
          },
          "final_response": {
            "parts": [
              {
                "text": "I got 4 and 7 from the dice roll, and 9 is not a prime number.\n"
              }
            ],
            "role": null
          },
          "intermediate_data": {
            "tool_uses": [
              {
                "id": "adk-1a3f5a01-1782-4530-949f-07cf53fc6f05",
                "args": {
                  "sides": 10
                },
                "name": "roll_die"
              },
              {
                "id": "adk-52fc3269-caaf-41c3-833d-511e454c7058",
                "args": {
                  "sides": 10
                },
                "name": "roll_die"
              },
              {
                "id": "adk-5274768e-9ec5-4915-b6cf-f5d7f0387056",
                "args": {
                  "nums": [
                    9
                  ]
                },
                "name": "check_prime"
              }
            ],
            "intermediate_responses": [
              [
                "data_processing_agent",
                [
                  {
                    "text": "I have rolled a 10 sided die twice. The first roll is 5 and the second roll is 3.\n"
                  }
                ]
              ]
            ]
          }
        }
      ],
      "session_input": {
        "app_name": "hello_world",
        "user_id": "user",
        "state": {}
      }
    }
  ]
}
```

#### How to migrate eval set files not backed by the Pydantic schema?

!!! note

    If your eval set files don't adhere to [EvalSet](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>) schema file, then this section is relevant to you.

Based on who is maintaining the eval set data, there are two routes:

1.  **Eval set data maintained by ADK UI** If you use ADK UI to maintain your
    Eval set data then *no action is needed* from you.

2.  **Eval set data is developed and maintained manually and used in ADK eval CLI** A
    migration tool is in the works, until then the ADK eval CLI command will
    continue to support data in the old format.

### Evaluate with conformance testing

`adk conformance test` command verifies that your AI agents behave consistently over time. It ensures that updates to your codebase or models don't introduce regressions by validating current agent outputs against baseline data.

#### Prerequisites and setup

Before the `adk conformance` command can execute meaningful regression testing, you must establish an optimal "golden baseline." Conformance testing operates by comparing live agent behavior against these previously recorded, verified interactions.

Follow this workflow to prepare your environment:
##### Create the Test Directory Hierarchy

 Conformance tests rely on a strict file layout to automatically discover and map test cases.
 Initialize your testing directory using the following structure:

```
tests
└── category_name/
    └── test_case_name/
        ├── spec.yaml                  # Test case specification
        ├── generated-recordings.yaml   # Baseline recorded interactions
        └── generated-session.yaml      # Baseline session data

```

!!! note

    If your agent uses Server-Sent Events (SSE), the testing framework will additionally look for `generated-recordings-sse.yaml` and `generated-session-sse.yaml` within the same folder.

##### Define the test specification (spec.yaml)

In your target test folder, create a `spec.yaml` file. This file outlines the initial conditions, configurations, and user prompts that the agent will execute during the baseline recording and subsequent conformance runs. Ensure your file matches the following basic schema, this is an example only:

```yaml
# Example spec.yaml for a Weather Agent.
# The test case name and category are inferred from the folder structure.
description: "Verifies the agent correctly identifies location and calls the weather tool."
agent: "weather_agent"
user_messages:
  - text: "What's the temperature in San Francisco right now?"
```

#### Automate the baseline

Because the background data (like LLM requests and tool calls) is complex, you shouldn't try to write or save the baseline files manually. Instead, let ADK generate them for you.

1. Start your ADK web server with the recording plugin turned on:

```shell
adk web -v --extra_plugins=google.adk.cli.plugins.recordings_plugin.RecordingsPlugin /path/to/agents
```

   2. Next, open a new terminal window and tell ADK to create the baseline files based on your spec.yaml:

```shell
adk conformance record tests/category/test_name none
```

   The trailing streaming-mode argument is required. Use `none` to record `generated-recordings.yaml` and `generated-session.yaml`, or `sse` to record `generated-recordings-sse.yaml` and `generated-session-sse.yaml` instead. The `bidi` mode is not supported for recording.

This automatically runs the scenario, records all the interactions, and saves the generated-recordings.yaml and generated-session.yaml files exactly where they need to be.

Once these baseline files are locked in, your setup is complete, and the directory is ready to be targeted by `adk conformance` in either **Replay** or **Live** mode.

#### How it works

* **Replay Mode (Default):** The tool runs your agent and compares its live LLM requests, responses, and tool calls directly against your previously recorded interactions to catch unexpected deviations.
* **Live Mode:** Runs evaluation-based verification against active environments *(Note: This mode is a work in progress)*.

### Evaluation criteria

ADK provides several built-in criteria for evaluating agent performance, ranging
from tool trajectory matching to LLM-based response quality assessment. For a
detailed list of available criteria and guidance on when to use them, please see
[Evaluation Criteria](<Google ADK 评测标准参考.md>).

Here is a summary of all the available criteria:

*   **tool_trajectory_avg_score**: Exact match of tool call trajectory.
*   **response_match_score**: ROUGE-1 similarity to reference response.
*   **final_response_match_v2**: LLM-judged semantic match to a reference
    response.
*   **rubric_based_final_response_quality_v1**: LLM-judged final response
    quality based on custom rubrics.
*   **rubric_based_tool_use_quality_v1**: LLM-judged tool usage quality based on
    custom rubrics.
*   **rubric_based_multi_turn_trajectory_quality_v1**: LLM-judged multi-turn
    trajectory quality based on custom rubrics.
*   **hallucinations_v1**: LLM-judged groundedness of agent response against
    context.
*   **safety_v1**: Safety/harmlessness of agent response.
*   **per_turn_user_simulator_quality_v1**: LLM-judged user simulator quality.
*   **multi_turn_task_success_v1**: Evaluates if agent achieves goal(s) of
    conversation.
*   **multi_turn_trajectory_quality_v1**: Evaluates the overall trajectory of
    the conversation.
*   **multi_turn_tool_use_quality_v1**: Evaluates function calls made during a
    conversation.

The following are *efficiency* criteria. They report a value but never pass or
fail an eval case, and they are reported for every eval without being listed in
`EvalConfig`:

*   **tool_call_count_v1**: Number of tool calls made.
*   **inference_call_count_v1**: Number of model calls made.
*   **token_usage_v1**: Tokens consumed, with a per-type breakdown reported
    alongside the total.
*   **invocation_duration_v1**: Wall-clock seconds the turn took, measured
    while the agent ran.

!!! note

    Some criteria (such as response quality, safety, and multi-turn quality)
    require the [Vertex Gen AI Evaluation Service
    API](<https://cloud.google.com/vertex-ai/generative-ai/docs/model-reference/evaluation>).
    To use them, authenticate by setting a `GOOGLE_API_KEY` environment
    variable, or by using Google Cloud project credentials
    (`GOOGLE_CLOUD_PROJECT` and `GOOGLE_CLOUD_LOCATION` with Application Default
    Credentials).

If no evaluation criteria are provided, the following default configuration is used:

* `tool_trajectory_avg_score`: Defaults to 1.0, requiring a 100% match in the tool usage trajectory.
* `response_match_score`: Defaults to 0.8, allowing for a small margin of error in the agent's natural language responses.

Here is an example of a `test_config.json` file specifying custom evaluation criteria:

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.8
  }
}
```

#### Recommendations on criteria

Choose criteria based on your evaluation goals:

*   **Enable tests in CI/CD pipelines or regression testing:** Use
    `tool_trajectory_avg_score` and `response_match_score`. These criteria are
    fast, predictable, and suitable for frequent automated checks.
*   **Evaluate trusted reference responses:** Use `final_response_match_v2` to
    evaluate semantic equivalence. This LLM-based check is more flexible than
    exact matching and better captures whether the agent's response means the
    same thing as the reference response.
*   **Evaluate response quality without a reference response:** Use
    `rubric_based_final_response_quality_v1`. This is useful when you don't have
    a trusted reference, but you can define attributes of a good response (e.g.,
    "The response is concise," "The response has a helpful tone").
*   **Evaluate the correctness of tool usage:** Use
    `rubric_based_tool_use_quality_v1`. This allows you to validate the agent's
    reasoning process by checking, for example, that a specific tool was called
    or that tools were called in the correct order (e.g., "Tool A must be called
    before Tool B").
*   **Check if responses are grounded in context:** Use `hallucinations_v1` to
    detect if the agent makes claims that are unsupported by or contradictory to
    the information available to it (e.g., tool outputs).
*   **Check for harmful content:** Use `safety_v1` to ensure that agent
    responses are safe and do not violate safety policies.
*   **Evaluate multi-turn goal completion:** Use `multi_turn_task_success_v1` to
    measure the overall success of a multi-turn conversation in achieving its
    intended objectives.
*   **Evaluate overall conversation trajectory:** Use
    `multi_turn_trajectory_quality_v1` to assess the efficiency, effectiveness,
    and logic of the steps taken during the conversation.
*   **Evaluate tool usage in multi-turn workflows:** Use
    `multi_turn_tool_use_quality_v1` to assess the quality, relevance, and
    correctness of tool or function calls made across multiple turns.
*   **Track how expensive an agent is:** The efficiency criteria
    (`tool_call_count_v1`, `inference_call_count_v1`, `token_usage_v1`,
    `invocation_duration_v1`) are reported automatically, with no
    configuration and no extra model calls. Compare them across runs to catch
    a change that keeps quality flat but doubles token usage. They never fail
    an eval, so they add no new failure mode to a CI/CD run.

In addition, criteria which require information on expected agent tool use
and/or responses are not supported in combination with
[User Simulation](<Google ADK 评测的用户模拟.md>).
The affected criteria include `tool_trajectory_avg_score`, `response_match_score`
and `final_response_match_v2`. Support for user simulation is listed per
criterion in the support column of [Evaluation Criteria](<Google ADK 评测标准参考.md>).

### User simulation

When evaluating conversational agents, it is not always practical to use a fixed
set of user prompts, as the conversation can proceed in unexpected ways.
For example, if the agent needs the user to supply two values to perform a task,
it may ask for those values one at a time or both at once.
To resolve this issue, ADK allows you test the behavior of the agent in a
specific *conversation scenario* with user prompts that are dynamically
generated by an AI model.
For details on how to set up an eval with user simulation, see
[User Simulation](<Google ADK 评测的用户模拟.md>).

## How to run evaluation with ADK

As a developer, you can evaluate your agents using the ADK in the following ways:

- **Web-based UI (**`adk web`**):** Evaluate agents interactively through a web-based interface.
- **Programmatically (**`pytest`**)**: Integrate evaluation into your testing pipeline using `pytest` and test files.
- **Command Line Interface (**`adk eval`**):** Run evaluations on an existing evaluation set file directly from the command line.
- **Conformance Testing** (**`adk conformance`**):** Execute automated tests against your baseline files to detect unexpected deviations or regressions.

### Run evaluations via the web UI

The web UI provides an interactive way to evaluate agents, generate evaluation datasets, and inspect agent behavior in detail.

#### Step 1: Create and save a test case

1. Start the web server by running: `adk web <path_to_your_agents_folder>`
2. In the web interface, select an agent and interact with it to create a session.
3. Navigate to the **Eval** tab on the right side of the interface.
4. Create a new eval set or select an existing one.
5. Click **"Add current session"** to save the conversation as a new evaluation case.

#### Step 2: View and edit your test case

Once a case is saved, you can click its ID in the list to inspect it. To make changes, click the **Edit current eval case** icon (pencil). This interactive view allows you to:

* **Modify** agent text responses to refine test scenarios.
* **Delete** individual agent messages from the conversation.
* **Delete** the entire evaluation case if it's no longer needed.

![adk-eval-case.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-eval-case.gif>)

#### Step 3: Run the evaluation with custom metrics

1. Select one or more test cases from your evalset.
2. Click **Run Evaluation**. An **EVALUATION METRIC** dialog will appear.
3. In the dialog, use the sliders to configure the thresholds for:
    * **Tool trajectory avg score**
    * **Response match score**
4. Click **Start** to run the evaluation using your custom criteria. The evaluation history will record the metrics used for each run.

![adk-eval-config.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-eval-config.gif>)

#### Step 4: Analyze results

After the run completes, you can analyze the results:

* **Analyze Run Failures**: Click on any **Pass** or **Fail** result. For failures, you can hover over the `Fail` label to see a side-by-side comparison of the **Actual vs. Expected Output** and the scores that caused the failure.

### Debugging with the Trace View

ADK web UI includes a powerful **Trace** tab for debugging agent behavior. This feature is available for any agent session, not just during evaluation.

The **Trace** tab provides a detailed and interactive way to inspect your agent's execution flow. Traces are automatically grouped by user message, making it easy to follow the chain of events.

Each trace row is interactive:

* **Hovering** over a trace row highlights the corresponding message in the chat window.
* **Clicking** on a trace row opens a detailed inspection panel with four tabs:
    * **Event**: The raw event data.
    * **Request**: The request sent to the model.
    * **Response**: The response received from the model.
    * **Graph**: A visual representation of the tool calls and agent logic flow.

![adk-trace1.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-trace1.gif>)
![adk-trace2.gif](<https://raw.githubusercontent.com/google/adk-docs/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/assets/adk-trace2.gif>)

Blue rows in the trace view indicate that an event was generated from that interaction. Clicking on these blue rows will open the bottom event detail panel, providing deeper insights into the agent's execution flow.

### Run tests programmatically

You can also use **`pytest`** to run test files as part of your integration tests.

#### Example command

```shell
pytest tests/integration/
```

#### Example test code

Here is an example of a `pytest` test case that runs a single test file:

```py
from google.adk.evaluation.agent_evaluator import AgentEvaluator
import pytest

@pytest.mark.asyncio
async def test_with_single_test_file():
    """Test the agent's basic ability via a session file."""
    await AgentEvaluator.evaluate(
        agent_module="home_automation_agent",
        eval_dataset_file_path_or_dir="tests/integration/fixture/home_automation_agent/simple_test.test.json",
    )
```

This approach allows you to integrate agent evaluations into your CI/CD pipelines or larger test suites. If you want to specify the initial session state for your tests, you can do that by storing the session details in a file and passing that to `AgentEvaluator.evaluate` method.

### Run evaluations via the CLI

You can also run evaluation of an eval set file through the command line interface (CLI). This runs the same evaluation that runs on the UI, but it helps with automation, i.e. you can add this command as a part of your regular build generation and verification process.

Here is the command:

```shell
adk eval \
    <AGENT_MODULE_FILE_PATH> \
    <EVAL_SET_FILE_PATH_OR_ID>... \
    [--config_file_path=<PATH_TO_TEST_JSON_CONFIG_FILE>] \
    [--print_detailed_results]
```

For example:

```shell
adk eval \
    samples_for_testing/hello_world \
    samples_for_testing/hello_world/hello_world_eval_set_001.evalset.json
```

Here are the details for each command line argument:

* `AGENT_MODULE_FILE_PATH`: The path to the agent directory, not a file, whose `__init__.py` exposes a module by the name "agent". "agent" module contains a `root_agent`.
* `EVAL_SET_FILE_PATH_OR_ID`: The path to evaluations file(s), or the id of an eval set managed by ADK and created with `adk eval_set create`. You can specify one or more of either, but you cannot mix file paths and eval set ids in the same command. For each eval set, all evals run by default. If you want to run only specific evals from a eval set, first create a comma separated list of eval names and then add that as a suffix to the eval set file name or id, demarcated by a colon `:` .
* For example: `sample_eval_set_file.json:eval_1,eval_2,eval_3`
  `This will only run eval_1, eval_2 and eval_3 from sample_eval_set_file.json`
* `CONFIG_FILE_PATH`: The path to the config file.
* `PRINT_DETAILED_RESULTS`: Prints detailed results on the console.

### Run conformance tests

You can run all your tests at once, run specific ones, or create a summary report.

#### Run all tests

If you don't type a specific folder path, the tool automatically looks for a tests/ folder in your workspace and runs everything inside it:

```
adk conformance test
```

#### Run specific test groups or individual cases

Pass one or more folder paths to narrow down which tests execute:

```

# Test an entire category of tests
adk conformance test tests/core

# Test one specific case
adk conformance test tests/core/description_001

```

#### Generate Markdown test reports

Add the `--generate_report` flag to produce a clean test summary report. You can optionally specify where to save it using the `--report_dir parameter`:

```
# Save the report in a specific folder
adk conformance test --generate_report --report_dir=reports
```

#### Automate with CI/CD
Because adk conformance test is a command-line tool that fails if things don't match, it is highly useful for CI/CD pipelines. You can set it up to run automatically whenever someone opens a pull request, blocking any code from merging if it changes the agent's expected behavior.


</details>
