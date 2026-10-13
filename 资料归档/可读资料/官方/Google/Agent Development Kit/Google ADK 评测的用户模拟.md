# Google ADK 评测的用户模拟

说明用用户画像、模拟器配置和生成测试场景评估多轮对话，并包含实时语音场景；属于应用评测参考。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-10-02](<https://github.com/google/adk-docs/commit/ba962ed9cc4e13132d90ce2743f3807c30d4d83a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/evaluate/user-sim.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/evaluate/user-sim.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 用户模拟

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v1.18.0</span>
</div> 支持

在评估会话 agents 时，使用固定的 
 组用户提示并不总是可行，因为会话可能会以意想不到的方式进行。
例如，如果 agent 需要用户提供两个值来执行任务，
它可能会要求一次提供一个值，或者
为了解决此问题，ADK 可以使用 
 生成 AI 模型动态生成用户提示。

要使用此功能，您必须指定 [`ConversationScenario`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/conversation_scenarios.py>)，它指示用户在与 agent 对话中的目标。您还可以指定您希望用户遵守的用户角色。

一个`ConversationScenario`由以下组件组成：

*   `starting_prompt`：用户应该使用的固定初始提示来启动
与agent的对话。
*   `conversation_plan`：用户必须实现的目标的高级指南
达到。
*   `user_persona`：用户特征的定义，例如技术
专业知识或语言风格。


[`hello_world`](<https://github.com/google/adk-python/tree/main/contributing/samples/core/hello_world>)
agent 的示例对话场景如下所示：

```json
{
  "starting_prompt": "What can you do for me?",
  "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime."
}
```
LLM使用`conversation_plan`以及对话历史记录来
动态生成用户提示。

您还可以通过以下方式指定预构建的`user_persona`：

```json
{
  "starting_prompt": "What can you do for me?",
  "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime.",
  "user_persona": "NOVICE"
}
```

虽然对话计划规定了必须完成的任务，但角色规定了模型如何表达其查询并对 agent 的响应做出反应。

## 用户角色

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v1.26.0</span>
</div> 支持

用户角色是模拟用户在对话期间采用的角色。
它由一组 ** 行为 ** 定义，这些行为决定用户如何与 
 agent 交互，例如他们的通信风格、他们如何提供信息、以及
他们如何对错误做出反应。

`UserPersona`由以下字段组成：

*   `id`：角色的唯一标识符。
*   `description`：关于用户是谁以及他们如何进行的高级描述
与 agent 互动。
*   `behaviors`：定义特定特征的`UserBehavior`对象列表。

每个 `UserBehavior` 包括：

*   `name`：行为的名称。
*   `description`：预期行为的摘要。
*   `behavior_instructions`：给模拟用户的具体说明
（LLM）如何行动。
*   `violation_rubrics`：评估者用来判断用户是否
遵循这种行为。如果**其中任何**满足****，则
评估者应确定该行为是**而不是**遵循的。

## 预建角色

ADK 提供了一组由常见行为组成的预构建角色。下表
总结了每个角色的行为：

|行为|**EXPERT** 角色|**NOVICE** 角色|**EVALUATOR** 角色|
| :--- | :--- | :--- | :--- |
|**前进**|注重细节（主动提供细节）|以目标为导向（等待询问细节）|注重细节|
|**回答**|仅相关问题|回答所有问题|仅相关问题|
|**更正Agent不准确的地方**|是的|否|否|
|**排查 Agent 错误**|曾经|从来没有|从来没有|
|**音调**|专业|会话型|会话型|

## 示例：使用对话场景评估 [`hello_world`](<https://github.com/google/adk-python/tree/main/contributing/samples/core/hello_world>) agent

要将包含对话场景的评估案例添加到新的或现有的 
[`EvalSet`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>)、
，您需要首先创建一个对话场景列表来测试 agent。

尝试将以下内容保存到
`contributing/samples/core/hello_world/conversation_scenarios.json`：

```json
{
  "scenarios": [
    {
      "starting_prompt": "What can you do for me?",
      "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime.",
      "user_persona": "NOVICE"
    },
    {
      "starting_prompt": "Hi, I'm running a tabletop RPG in which prime numbers are bad!",
      "conversation_plan": "Say that you don't care about the value; you just want the agent to tell you if a roll is good or bad. Once the agent agrees, ask it to roll a 6-sided die. Finally, ask the agent to do the same with 2 20-sided dice.",
      "user_persona": "EXPERT"
    }
  ]
}
```

您还需要一个会话输入文件，其中包含
评估期间使用的信息。
尝试将以下内容保存到
`contributing/samples/core/hello_world/session_input.json`：

```json
{
  "app_name": "hello_world",
  "user_id": "user"
}
```

然后，您可以将对话场景添加到`EvalSet`：

```bash
# (optional) create a new EvalSet
adk eval_set create \
  contributing/samples/core/hello_world \
  eval_set_with_scenarios

# add conversation scenarios to the EvalSet as new eval cases
adk eval_set add_eval_case \
  contributing/samples/core/hello_world \
  eval_set_with_scenarios \
  --scenarios_file contributing/samples/core/hello_world/conversation_scenarios.json \
  --session_input_file contributing/samples/core/hello_world/session_input.json
```

默认情况下，ADK 使用需要指定 agent 预期 
 响应的指标来运行评估。
由于动态对话场景并非如此，我们将使用 
[`EvalConfig`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_config.py>)
 和一些备用支持指标。

尝试将以下内容保存到
`contributing/samples/core/hello_world/eval_config.json`：

```json
{
  "criteria": {
    "hallucinations_v1": {
      "threshold": 0.5,
      "evaluate_intermediate_nl_responses": true
    },
    "safety_v1": {
      "threshold": 0.8
    }
  }
}
```

最后，您可以使用`adk eval`命令来运行评估：

```bash
adk eval \
    contributing/samples/core/hello_world \
    --config_file_path contributing/samples/core/hello_world/eval_config.json \
    eval_set_with_scenarios \
    --print_detailed_results
```

## 用户模拟器配置

您可以覆盖默认的用户模拟器配置来更改模型、
内部模型行为以及用户与agent交互的最大数量。
下面的`EvalConfig`显示了默认的用户模拟器配置：

```json
{
  "criteria": {
    # same as before
  },
  "user_simulator_config": {
    "model": "gemini-flash-latest",
    "model_configuration": {
      "thinking_config": {
        "include_thoughts": true,
        "thinking_budget": 10240
      }
    },
    "max_allowed_invocations": 20,
    "include_function_calls": false
  }
}
```

*   `model`：支持用户模拟器的模型。
*   `model_configuration`: A
[`GenerateContentConfig`](<https://github.com/googleapis/python-genai/blob/6196b1b4251007e33661bb5d7dc27bafee3feefe/google/genai/types.py#L4295>)
 控制模型行为。
*   `max_allowed_invocations`：允许的最大用户-agent交互
在对话被强行终止之前。这应该设置为
，大于
 `EvalSet`中最长的合理用户-agent交互。初始固定提示算作一次调用。将此
值设置为`-1`会删除创新限制，但不建议这样做。
*   `include_function_calls`：可选。是否包含函数调用和
向用户模拟器提供的对话历史记录提示中的响应。
 默认为 `false`。
*   `custom_instructions`：可选。覆盖默认指令
用户模拟器。指令字符串必须包含以下格式的
占位符，使用
 [Jinja](<https://jinja.palletsprojects.com/en/stable/templates/#>)语法（*do
不要提前替换值！*）：
    *   `{{ stop_signal }}`：用户模拟器时生成的文本
决定谈话结束。
    *   `{{ conversation_plan }}`：谈话的总体计划
用户模拟器必须遵循。
    *   `{{ conversation_history }}` ：用户与用户之间的对话
agent 到目前为止。
    *   您还可以通过`{{ persona }}`访问`UserPersona`对象
占位符。

## 自定义角色

您可以通过在 
 和 `ConversationScenario` 中提供 `UserPersona` 对象来定义您自己的自定义角色。

自定义角色定义示例：

```json
{
  "starting_prompt": "I need help with my account.",
  "conversation_plan": "Ask the agent to reset your password.",
  "user_persona": {
    "id": "IMPATIENT_USER",
    "description": "A user who is in a rush and gets easily frustrated.",
    "behaviors": [
      {
        "name": "Short responses",
        "description": "The user should provide very short, sometimes incomplete responses.",
        "behavior_instructions": [
            "Keep your responses under 10 words.",
            "Omit polite phrases."
        ],
        "violation_rubrics": [
            "The user response is over 10 words.",
            "The user response is overly polite."
        ]
      }
    ]
  }
}
```

## 通过用户模拟生成评估案例

手动编写评估案例可能非常耗时，并且可能无法涵盖所有潜在的故障模式。 ADK 提供了一个命令，可使用 Agent 平台评估 SDK 根据您的 agent 的定义自动生成多样化且真实的对话场景。

！！！警告“先决条件：Agent 平台凭证”
 生成评估案例使用 [Vertex Gen AI 评估服务 API](<https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/evaluation-overview>)。您必须拥有启用了 Agent 平台 API 的 Google 云项目，并在您的环境中配置了有效的应用程序默认凭据 (ADC)。


### 命令语法

```bash
adk eval_set generate_eval_cases \
    <AGENT_MODULE_FILE_PATH> \
    <EVAL_SET_ID> \
    --user_simulation_config_file=<PATH_TO_CONFIG_FILE>
```

### 配置文件格式

`--user_simulation_config_file` 需要一个与 `ConversationGenerationConfig` 架构匹配的 JSON 文件：

```json
{
  "count": 5,
  "generation_instruction": "Generate scenarios where the user asks to control home devices under different conditions.",
  "environment_context": "Available devices: device_1 (Light), device_2 (Thermostat).",
  "model_name": "gemini-flash-latest"
}
```

### 配置字段

*   **`count`**（必填）：要生成的对话场景的数量。
*   **`generation_instruction`**（可选）：自然语言提示，指导您要测试的特定类型的场景或目标。
*   **`environment_context`**（可选）：描述 agent 工具可访问的后端数据或状态的上下文。这有助于生成器创建基于实际数据（例如有效的设备 ID）的查询。
*   **`model_name`**（必需）：用于生成的 Gemini 模型（例如，`gemini-flash-latest`）。

## 现场音频用户模拟 agents

用户模拟器与被测试的 agent 是否是实时 
（语音）agent 无关，因此相同的 `ConversationScenario`（或固定对话）可以 
 驱动文本和实时评估。对于直播 agents，模拟用户的回合可以 
 合成为 ** 音频 ** 并流式传输到 agent。

这是通过评估配置 
(`test_config.json`) 中的 `llm_audio` 用户模拟器进行配置的。它包装了标准文本模拟器，并使用文本转语音模型将每个 
 生成的用户声音转换为音频。默认使用
Google云文本转语音(`cloud_tts`)；可以使用 Gemini TTS 型号名称 
 代替。

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.5
  },
  "live_model_config": {
    "timeout_seconds": 300
  },
  "user_simulator_config": {
    "type": "llm_audio",
    "model": "gemini-2.5-flash",
    "audio_model": "cloud_tts",
    "audio_model_configuration": {
      "speech_config": {
        "voice_config": {
          "prebuilt_voice_config": { "voice_name": "en-US-Studio-O" }
        },
        "language_code": "en-US"
      }
    },
    "include_text_with_audio": true
  }
}
```

关键领域：

*   `type`：`"llm_audio"`选择音频用户模拟器。
*   `audio_model`：`"cloud_tts"` 用于 Google 云文本转语音，或 Gemini
TTS 型号名称（例如 `"gemini-2.5-flash-preview-tts"`）。
*   `audio_model_configuration.speech_config`：选择语音和语言。
*   `include_text_with_audio`：用户转动是否还携带文本部分
与生成的音频一起。

！！！注意“实时模型需要实时推理”

评估实时 agent 需要实时（双向流）推理，
 是 ** 而不是 ** 默认值。通过将 `live_model_config`
 块添加到您的配置文件来启用它。实时 API 模型（例如 `gemini-*-live-*`）不是通过非实时评估使用的一元 `generateContent` 端点提供的 
，因此 
 在没有实时模式的情况下运行它们会失败。

`use_live`是从`live_model_config`设置的内部字段；将其放入
配置文件中没有效果。

使用 `cloud_tts` 需要 `google-cloud-texttospeech` 软件包（包含在 `google-adk[eval]` 额外内容中的 
）并访问云文本转语音 API。

请参阅 
[`contributing/samples/live/non_blocking_tool_agent`](<https://github.com/google/adk-python/tree/main/contributing/samples/live/non_blocking_tool_agent>)
 中的示例，了解完整的、可运行的实时评估配置。


## 原文对照

<details>
<summary>展开完整原文</summary>

# User simulation

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v1.18.0</span>
</div>

When evaluating conversational agents, it is not always practical to use a fixed
set of user prompts, as the conversation can proceed in unexpected ways.
For example, if the agent needs the user to supply two values to perform a task,
it may ask for those values one at a time or both at once.
To resolve this issue, ADK can dynamically generate user prompts using a
generative AI model.

To use this feature, you must specify a [`ConversationScenario`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/conversation_scenarios.py>) which dictates the user's goals in their conversation with the agent. You may also specify a user persona that you expect the user to adhere to.

A `ConversationScenario` consists of the following components:

*   `starting_prompt`: A fixed initial prompt that the user should use to start
    the conversation with the agent.
*   `conversation_plan`: A high-level guideline for the goals the user must
    achieve.
*   `user_persona`: A definition of the user's traits, such as technical
    expertise or linguistic style.

A sample conversation scenario for the
[`hello_world`](<https://github.com/google/adk-python/tree/main/contributing/samples/core/hello_world>)
agent is shown below:

```json
{
  "starting_prompt": "What can you do for me?",
  "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime."
}
```
The LLM uses the `conversation_plan`, along with the conversation history, to
dynamically generate user prompts.

You can also specify a pre-built `user_persona` in the following manner:

```json
{
  "starting_prompt": "What can you do for me?",
  "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime.",
  "user_persona": "NOVICE"
}
```

While the conversation plan dictates what must be accomplished, the persona dictates how the model phrases its queries and reacts to the agent's responses.

## User personas

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v1.26.0</span>
</div>

A User Persona is a role that the simulated user adopts during the conversation.
It is defined by a set of **behaviors** that dictate how the user interacts with
the agent, such as their communication style, how they provide information, and
how they react to errors.

A `UserPersona` consists of the following fields:

*   `id`: A unique identifier for the persona.
*   `description`: A high-level description of who the user is and how they
    interact with the agent.
*   `behaviors`: A list of `UserBehavior` objects that define specific traits.

Each `UserBehavior` includes:

*   `name`: The name of the behavior.
*   `description`: A summary of the expected behavior.
*   `behavior_instructions`: Specific instructions given to the simulated user
    (LLM) on how to act.
*   `violation_rubrics`: Used by evaluators to determine whether the user is
    following this behavior. If **any** of these rubrics are **satisfied**, the
    evaluator should determine the behavior was **not** followed.

## Pre-built Personas

ADK provides a set of pre-built personas composed of common behaviors. The table
below summarizes the behaviors for each persona:

| Behavior | **EXPERT** persona | **NOVICE** persona | **EVALUATOR** persona |
| :--- | :--- | :--- | :--- |
| **Advance** | Detail oriented (proactively provides details) | Goal oriented (waits to be asked for details) | Detail oriented |
| **Answer** | Relevant questions only | Answer all questions | Relevant questions only |
| **Correct Agent Inaccuracies** | Yes | No | No |
| **Troubleshoot Agent Errors** | Once | Never | Never |
| **Tone** | Professional | Conversational | Conversational |

## Example: Evaluate the [`hello_world`](<https://github.com/google/adk-python/tree/main/contributing/samples/core/hello_world>) agent with conversation scenarios

To add evaluation cases containing conversation scenarios to a new or existing
[`EvalSet`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_set.py>),
you need to first create a list of conversation scenarios to test the agent in.

Try saving the following to
`contributing/samples/core/hello_world/conversation_scenarios.json`:

```json
{
  "scenarios": [
    {
      "starting_prompt": "What can you do for me?",
      "conversation_plan": "Ask the agent to roll a 20-sided die. After you get the result, ask the agent to check if it is prime.",
      "user_persona": "NOVICE"
    },
    {
      "starting_prompt": "Hi, I'm running a tabletop RPG in which prime numbers are bad!",
      "conversation_plan": "Say that you don't care about the value; you just want the agent to tell you if a roll is good or bad. Once the agent agrees, ask it to roll a 6-sided die. Finally, ask the agent to do the same with 2 20-sided dice.",
      "user_persona": "EXPERT"
    }
  ]
}
```

You will also need a session input file containing information used during
evaluation.
Try saving the following to
`contributing/samples/core/hello_world/session_input.json`:

```json
{
  "app_name": "hello_world",
  "user_id": "user"
}
```

Then, you can add the conversation scenarios to an `EvalSet`:

```bash
# (optional) create a new EvalSet
adk eval_set create \
  contributing/samples/core/hello_world \
  eval_set_with_scenarios

# add conversation scenarios to the EvalSet as new eval cases
adk eval_set add_eval_case \
  contributing/samples/core/hello_world \
  eval_set_with_scenarios \
  --scenarios_file contributing/samples/core/hello_world/conversation_scenarios.json \
  --session_input_file contributing/samples/core/hello_world/session_input.json
```

By default, ADK runs evaluations with metrics that require the agent's expected
response to be specified.
Since that is not the case for a dynamic conversation scenario, we will use an
[`EvalConfig`](<https://github.com/google/adk-python/blob/main/src/google/adk/evaluation/eval_config.py>)
with some alternate supported metrics.

Try saving the following to
`contributing/samples/core/hello_world/eval_config.json`:

```json
{
  "criteria": {
    "hallucinations_v1": {
      "threshold": 0.5,
      "evaluate_intermediate_nl_responses": true
    },
    "safety_v1": {
      "threshold": 0.8
    }
  }
}
```

Finally, you can use the `adk eval` command to run the evaluation:

```bash
adk eval \
    contributing/samples/core/hello_world \
    --config_file_path contributing/samples/core/hello_world/eval_config.json \
    eval_set_with_scenarios \
    --print_detailed_results
```

## User simulator configuration

You can override the default user simulator configuration to change the model,
internal model behavior, and the maximum number of user-agent interactions.
The below `EvalConfig` shows the default user simulator configuration:

```json
{
  "criteria": {
    # same as before
  },
  "user_simulator_config": {
    "model": "gemini-flash-latest",
    "model_configuration": {
      "thinking_config": {
        "include_thoughts": true,
        "thinking_budget": 10240
      }
    },
    "max_allowed_invocations": 20,
    "include_function_calls": false
  }
}
```

*   `model`: The model backing the user simulator.
*   `model_configuration`: A
    [`GenerateContentConfig`](<https://github.com/googleapis/python-genai/blob/6196b1b4251007e33661bb5d7dc27bafee3feefe/google/genai/types.py#L4295>)
    which controls the model behavior.
*   `max_allowed_invocations`: The maximum user-agent interactions allowed
    before the conversation is forcefully terminated. This should be set to be
    greater than the longest reasonable user-agent interaction in your
    `EvalSet`. The initial fixed prompt counts as an invocation. Setting this
    value to `-1` removes the innovation limit, which is not recommended.
*   `include_function_calls`: Optional. Whether to include function calls and
    responses in the conversation history prompt given to the user simulator.
    Defaults to `false`.
*   `custom_instructions`: Optional. Overrides the default instructions for the
    user simulator. The instruction string must contain the following formatting
    placeholders using
    [Jinja](<https://jinja.palletsprojects.com/en/stable/templates/#>) syntax (*do
    not substitute values in advance!*):
    *   `{{ stop_signal }}` : text to be generated when the user simulator
        decides that the conversation is over.
    *   `{{ conversation_plan }}` : the overall plan for the conversation that
        the user simulator must follow.
    *   `{{ conversation_history }}` : the conversation between the user and the
        agent so far.
    *   You can also access the `UserPersona` object through the `{{ persona }}`
        placeholder.

## Custom personas

You can define your own custom persona by providing a `UserPersona` object in
the `ConversationScenario`.

Example of a custom persona definition:

```json
{
  "starting_prompt": "I need help with my account.",
  "conversation_plan": "Ask the agent to reset your password.",
  "user_persona": {
    "id": "IMPATIENT_USER",
    "description": "A user who is in a rush and gets easily frustrated.",
    "behaviors": [
      {
        "name": "Short responses",
        "description": "The user should provide very short, sometimes incomplete responses.",
        "behavior_instructions": [
            "Keep your responses under 10 words.",
            "Omit polite phrases."
        ],
        "violation_rubrics": [
            "The user response is over 10 words.",
            "The user response is overly polite."
        ]
      }
    ]
  }
}
```

## Generate evaluation cases via user simulation

Writing evaluation cases manually can be time-consuming and may not cover all potential failure modes. ADK provides a command to automatically generate diverse and realistic conversation scenarios based on your agent's definition using the Agent Platform Eval SDK.

!!! warning "Prerequisites: Agent Platform Credentials"
    Generating evaluation cases uses the [Vertex Gen AI Evaluation Service API](<https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/evaluation-overview>). You must have a Google Cloud project with the Agent Platform API enabled and valid Application Default Credentials (ADC) configured in your environment.


### Command Syntax

```bash
adk eval_set generate_eval_cases \
    <AGENT_MODULE_FILE_PATH> \
    <EVAL_SET_ID> \
    --user_simulation_config_file=<PATH_TO_CONFIG_FILE>
```

### Configuration File Format

The `--user_simulation_config_file` expects a JSON file matching the `ConversationGenerationConfig` schema:

```json
{
  "count": 5,
  "generation_instruction": "Generate scenarios where the user asks to control home devices under different conditions.",
  "environment_context": "Available devices: device_1 (Light), device_2 (Thermostat).",
  "model_name": "gemini-flash-latest"
}
```

### Configuration Fields

*   **`count`** (required): The number of conversation scenarios to generate.
*   **`generation_instruction`** (optional): A natural language prompt guiding the specific types of scenarios or goals you want to test.
*   **`environment_context`** (optional): Context describing the backend data or state accessible to the agent's tools. This helps the generator create queries that are grounded in realistic data (e.g., valid device IDs).
*   **`model_name`** (required): The Gemini model used for generation (e.g., `gemini-flash-latest`).

## Audio user simulation for live agents

The user simulator is independent of whether the agent under test is a live
(voice) agent, so the same `ConversationScenario` (or a fixed conversation) can
drive both text and live evals. For live agents, the simulated user's turns can
be synthesized to **audio** and streamed to the agent.

This is configured with the `llm_audio` user simulator in your eval config
(`test_config.json`). It wraps the standard text simulator and converts each
generated user turn to audio using a text-to-speech model. By default it uses
Google Cloud Text-to-Speech (`cloud_tts`); a Gemini TTS model name may be used
instead.

```json
{
  "criteria": {
    "tool_trajectory_avg_score": 1.0,
    "response_match_score": 0.5
  },
  "live_model_config": {
    "timeout_seconds": 300
  },
  "user_simulator_config": {
    "type": "llm_audio",
    "model": "gemini-2.5-flash",
    "audio_model": "cloud_tts",
    "audio_model_configuration": {
      "speech_config": {
        "voice_config": {
          "prebuilt_voice_config": { "voice_name": "en-US-Studio-O" }
        },
        "language_code": "en-US"
      }
    },
    "include_text_with_audio": true
  }
}
```

Key fields:

*   `type`: `"llm_audio"` selects the audio user simulator.
*   `audio_model`: `"cloud_tts"` for Google Cloud Text-to-Speech, or a Gemini
    TTS model name (e.g. `"gemini-2.5-flash-preview-tts"`).
*   `audio_model_configuration.speech_config`: Selects the voice and language.
*   `include_text_with_audio`: Whether the user turn also carries the text part
    alongside the generated audio.

!!! note "Live models require live inference"

    Evaluating a live agent requires live (bidirectional streaming) inference,
    which is **not** the default. Enable it by adding a `live_model_config`
    block to your config file. Live API models (e.g. `gemini-*-live-*`) are not
    served over the unary `generateContent` endpoint that non-live eval uses, so
    running them without live mode fails.

    `use_live` is an internal field set from `live_model_config`; putting it in
    a config file has no effect.

    Using `cloud_tts` requires the `google-cloud-texttospeech` package (included
    in the `google-adk[eval]` extra) and access to the Cloud Text-to-Speech API.

See the sample at
[`contributing/samples/live/non_blocking_tool_agent`](<https://github.com/google/adk-python/tree/main/contributing/samples/live/non_blocking_tool_agent>)
for a complete, runnable live eval configuration.


</details>
