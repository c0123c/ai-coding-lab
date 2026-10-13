# OpenAI Agents SDK 确定性测试工具

提供固定回复、工具调用、流式输出、故障注入和工作流变化检测，以及沙箱、实时会话与语音测试方法。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/openai/openai-agents-python/commit/9d2b318dd034e5c0333c88e60ed6c704cbbbd04b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/docs/testing.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/OpenAI Agents SDK 确定性测试工具.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 测试

SDK 为 Agent 工作流程、沙盒会话、实时会话和语音管道提供确定性、提供商中立的测试实用程序。这些实用程序在内存中运行，不发出模型、沙箱提供程序或实时 API 请求，并记录 SDK 拥有的规范化交互。下面的可运行配方禁用每次运行的跟踪，以便在配置 OpenAI API 键时默认跟踪处理器不会上传测试活动。

使用它们来测试您的应用程序和 SDK 拥有的编排：工具执行、切换、护栏、重试、流式传输、会话行为、沙箱功能、实时事件处理和语音管道组合。使用真实的提供程序适配器或集成环境来实现外部模型、网络协议、沙箱提供程序或音频系统所拥有的行为。

## 找到您需要的食谱

|我想...|使用|Go 至|
| --- | --- | --- |
|返回固定的最终答案|`ScriptedModel` 与 `assistant_message()`|[返回固定响应](<#返回固定响应>)|
|练习多转刀具循环|`function_call()` 随后是助理响应|[测试工具工作流程](<#测试工具工作流程>)|
|从请求中选择一个响应|`ModelStep.respond()` 或 `responder` 映射|[从请求中派生响应](<#从请求中获取响应>)|
|断言跑步者发送给模型的内容|`calls`、`first_call` 或 `last_call`|[检查模型调用](<#检查模型调用>)|
|测试流式运行|正常响应步骤，或 `ModelStep.stream()` 对于确切事件|[测试串流](<#测试串流>)|
|测试错误或重试决定| `ModelStep.raise_error()` |[注入模型失败](<#注入模型失败>)|
|检测意外的工作流程更改|精确的 FIFO 步骤加上 `assert_complete()`|[检测工作流程漂移](<#检测工作流程漂移>)|
|在不启动沙箱的情况下测试 `SandboxAgent`|`scripted_sandbox_session()` 加 `ScriptedModel`|[测试沙箱 Agent 工作流程](<#测试沙盒-agent-工作流程>)|
|匹配沙箱调用或得出其结果|沙盒步骤上的 `match` 或 `responder`|[配置沙箱步骤](<#配置沙盒步骤>)|
|在不打开连接的情况下测试实时会话|`ScriptedRealtimeModel` 和 `RealtimeStep`|[测试实时会话](<#测试实时会话>)|
|测试实时工具工作流程|发出 `RealtimeModelToolCallEvent` 并期望工具输出|[测试实时工具工作流程](<#测试实时工具工作流程>)|
|测试静态或流式语音管道|`ScriptedSTTModel`、`ScriptedTTSModel` 以及脚本化或真实工作流程|[测试语音管道](<#测试语音管道>)|
|测试提供程序序列化或线路有效负载|具有受控网络传输的真正提供商适配器|[选择正确的边界](<#选择正确的边界>)|

## 进口

测试 API 位于它们所替换的运行时边界附近：

|边界|导入路径|
| --- | --- |
|Agent 模型和沙箱工作流程| `agents.testing` |
|实时模型传输| `agents.realtime.testing` |
|语音 STT、TTS 和工作流程组件| `agents.voice.testing` |

测试符号被有意保留在顶级 `agents` 导入之外。

## Agent 工作流程配方

### 返回固定响应

为每个预期模型调用传递一个标准化输出项序列。输出序列速记接收一个确定性响应 ID 和一个请求的用法。

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.testing import ScriptedModel, assistant_message


@pytest.mark.asyncio
async def test_fixed_response() -> None:
    model = ScriptedModel([[assistant_message("Paris is the capital of France.")]])
    agent = Agent(name="Geography assistant", model=model)

    result = await Runner.run(
        agent,
        "What is the capital of France?",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "Paris is the capital of France."
    assert len(model.calls) == 1
    model.assert_complete()
```

使用 `model.assert_complete()` 完成确定性工作流程测试。它捕获工作流在消耗每个配置步骤之前停止的情况。

### 测试工具工作流程

编写一个调用该工具的模型响应和生成最终答案的第二个响应的脚本。真正的 SDK 工具管道在这些模型调用之间运行。

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.decorators import tool
from agents.testing import ScriptedModel, assistant_message, function_call


@tool
def get_weather(city: str) -> str:
    """Return the weather for a city."""
    return f"{city}: sunny"


@pytest.mark.asyncio
async def test_tool_workflow() -> None:
    model = ScriptedModel(
        [
            [function_call("get_weather", {"city": "Tokyo"}, call_id="call_1")],
            [assistant_message("It is sunny in Tokyo.")],
        ]
    )
    agent = Agent(name="Weather assistant", model=model, tools=[get_weather])

    result = await Runner.run(
        agent,
        "What is the weather in Tokyo?",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "It is sunny in Tokyo."
    assert len(model.calls) == 2
    assert model.last_call is not None
    assert any(item.get("type") == "function_call_output" for item in model.last_call.input)
    model.assert_complete()
```

该模式涵盖了工具输入验证、执行、结果转换、钩子、护栏和下一个模型转弯。直接调用 Python 函数将绕过这些 SDK 行为。

### 从请求中获取响应

当响应真正依赖于规范化模型调用或断言属于模型边界时，请使用 `ModelStep.respond()`。响应器可以是同步的或异步的，并且可以返回`ScriptedModel`接受的任何步骤形状。

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.testing import ModelCall, ModelStep, ScriptedModel, assistant_message


def respond(call: ModelCall):
    assert call.streamed is False
    assert call.input == [{"content": "Summarize this", "role": "user"}]
    return {"output": [assistant_message("Handled the normalized request.")]}


@pytest.mark.asyncio
async def test_request_aware_response() -> None:
    model = ScriptedModel([ModelStep.respond(respond)])
    agent = Agent(name="Assistant", model=model)

    result = await Runner.run(
        agent,
        "Summarize this",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "Handled the normalized request."
    model.assert_complete()
```

`ScriptedModel` 接受 `ModelStep`（等效字典形式）、`ModelResponse`（标准化输出项序列）或异常。当响应不依赖于调用时，首选固定输出序列，因为固定脚本使意外的转变更容易诊断。

### 检查模型调用

`ScriptedModel` 在解决或引发所选步骤之前记录每个调用。

|会员|包含|
| --- | --- |
| `calls` |调用顺序中的每个 `ModelCall`|
| `first_call` |第一次通话，或 `None`|
| `last_call` |最近的通话，或 `None`|
| `remaining_steps` |尚未消耗的已配置步骤数|

常见断言包括 `call.input`、`call.model_settings`、`call.tools`、`call.handoffs` 和 `call.streamed`。可变请求数据在调用边界进行快照，每个公共历史访问器返回分离的快照。工具、切换、输出模式和跟踪对象保持其运行时身份。

结构化 `call_index` 和 `input_index` 错误字段从零开始，因此它们直接索引 `calls[...]` 或提供的步骤序列。人类可读的错误消息显示基于 1 的调用或步骤编号。

当一项测试需要增量附加模型步骤时，请使用 `enqueue()` 或 `extend()`。新建一个`ScriptedModel`，用于独立场景；该实用程序不会重置消耗的步骤或调用历史记录。

### 测试串流

正常响应步骤支持 `Runner.run()` 和 `Runner.run_streamed()`。对于常见的辅助消息、推理项、函数调用和应用补丁调用，`ScriptedModel` 生成标准化的开始、增量、项完成和终端响应事件。终端响应携带完整的输出和用法。

仅当精确标准化的 `TResponseStreamEvent` 序列是被测行为的一部分时才使用 `ModelStep.stream()`：

```python
step = ModelStep.stream(
    events,
    output=[assistant_message("The terminal output used by the runner.")],
)
```

`events`可以是固定序列或接收记录的`ModelCall`的异步工厂。可选的 `output` 是在非流式调用中使用相同步骤时返回的响应。确切的流事件是SDK-标准化事件，而不是响应API或聊天完成线块。

自动流拒绝未实现增量生命周期的标准化输出项类型。对这些项目使用 `ModelStep.stream(...)`，而不是依赖部分事件序列。

### 注入模型失败

使用 `ModelStep.raise_error()` 会使一个模型调用失败。可选的重试建议属于该确切的脚本错误：

```python
from agents import ModelRetryAdvice
from agents.testing import ModelStep


step = ModelStep.raise_error(
    RuntimeError("temporary failure"),
    retry_advice=ModelRetryAdvice(suggested=True, replay_safety="safe"),
)
```

跑步者的重试策略决定建议是否会导致另一次尝试。每次重试都是另一个模型调用，并消耗下一个脚本步骤。 Python 助手接受固定的 `ModelRetryAdvice` 值；当重试建议本身必须根据尝试动态变化时，请使用自定义 `Model`。

### 检测工作流程漂移

将脚本化调用视为预期的工作流程形状。额外模型请求引发 `UnexpectedModelCall`；提前退出会留下需要 `assert_complete()` 报告的步骤。

当您的测试框架支持拆卸或终结器时，如果您还希望在另一个断言失败后报告未使用的步骤，请将 `assert_complete()` 放在那里。不要在正常回归测试中发现不匹配错误。

|错误|结构化字段|含义|
| --- | --- | --- |
| `InvalidModelStep` | `reason`, `input_index` |步骤格式错误，在进入队列之前被拒绝|
| `UnexpectedModelCall` | `call`, `call_index` |脚本结束后工作流进行了另一个模型调用|
| `UnconsumedModelSteps` | `remaining_steps` |工作流程在使用每个步骤之前结束|

## 沙盒 Agent 食谱

### 测试沙盒 Agent 工作流程

将 `ScriptedModel` 与 `scripted_sandbox_session()` 结合起来即可练习真正的 `SandboxAgent` 运行时，而无需创建本地容器或远程沙箱。模型脚本选择功能工具，而沙盒脚本定义相应的 `SandboxSession` 方法返回的内容。

```python
import pytest

from agents import RunConfig, Runner
from agents.sandbox import ExecResult, SandboxAgent
from agents.sandbox.capabilities import Shell
from agents.testing import (
    ScriptedModel,
    assistant_message,
    function_call,
    scripted_sandbox_session,
)


@pytest.mark.asyncio
async def test_sandbox_workflow() -> None:
    sandbox = scripted_sandbox_session(
        [
            {
                "method": "exec",
                "match": lambda call: call.args == ("pwd",),
                "result": ExecResult(
                    stdout=b"/workspace\n",
                    stderr=b"",
                    exit_code=0,
                ),
            }
        ]
    )
    model = ScriptedModel(
        [
            [function_call("exec_command", {"cmd": "pwd"}, call_id="call_1")],
            [assistant_message("The workspace is /workspace.")],
        ]
    )
    agent = SandboxAgent(
        name="Workspace assistant",
        model=model,
        capabilities=[Shell()],
    )

    async with sandbox:
        result = await Runner.run(
            agent,
            "Which directory are you in?",
            run_config=RunConfig(
                sandbox={"session": sandbox},
                tracing_disabled=True,
            ),
        )

    assert result.final_output == "The workspace is /workspace."
    assert [call.method for call in sandbox.calls] == ["exec"]
    sandbox.assert_complete()
    model.assert_complete()
```

此测试跨越两个标准化 SDK 边界。它涵盖工具参数验证、功能路由、沙箱会话调用、工具结果传递到下一个模型回合以及最终输出处理。它不会测试真实模型是否选择该命令或真实沙箱提供程序如何执行它。

### 配置沙盒步骤

每个匹配的沙箱调用都会消耗一个全局 FIFO 序列中的下一步。方法不匹配、匹配器拒绝或匹配器异常会使该步骤处于挂起状态。设置 `method`，仅选择一种结果，并仅在通话详细信息重要时添加 `match`。

|步员|当...时使用它|
| --- | --- |
| `result` |该方法应该返回一个固定类型的值|
| `responder` |结果取决于分离的`SandboxCall`|
| `error` |该方法应该引发特定的异常|
| `match` |除非匹配器返回 `False` 以外的值，否则应在生成结果之前拒绝调用|

支持的脚本方法名称为 `apply_patch`、`exec`、`ls`、`mkdir`、`pty_exec_start`、`pty_write_stdin`、`read`、`rm` 和`write`。仅公开配置的面向模型的功能。配置任一 PTY 方法时，这两个 PTY 方法会一起公开，因为它们形成一个交互式 shell 功能，但调用仍会使用全局 FIFO 脚本。

`sandbox.calls` 包含分离的 `SandboxCall` 快照，具有从零开始的 `call_index`、`method`、位置 `args` 和只读 `kwargs`。创建脚本时也会对静态结果进行快照。支持 `io.BytesIO` 和 `io.StringIO` 值；将自定义沙盒会话用于其他实时流对象或生命周期行为。

|错误|结构化字段|含义|
| --- | --- | --- |
| `InvalidSandboxStep` | `reason`, `input_index`, `method` |步骤格式错误或命名了不受支持的方法|
| `UnexpectedSandboxCall` | `call`, `call_index`, `actual_method`, `expected_method`, `remaining_steps` |工作流程调用了错误的方法或在脚本结束后继续|
| `SandboxCallMatcherError` | `call`, `call_index`, `method` |步骤匹配器返回 `False`|
| `UnconsumedSandboxSteps` | `remaining_steps`, `pending_methods` |工作流程在使用每个步骤之前结束|

返回的对象是会话本身。直接传给`RunConfig(sandbox={"session": sandbox})`；没有包装器 `.session` 属性。

## 实时食谱

### 测试实时会话

`ScriptedRealtimeModel` 实现 Python SDK 的归一化 `RealtimeModel` 边界。每个 `RealtimeStep` 匹配一个出站 `RealtimeModelSendEvent`，然后发出标准化的入站 `RealtimeModelEvent` 对象或引发注入错误。

```python
import pytest

from agents.realtime import (
    RealtimeAgent,
    RealtimeModelOutputTextDeltaEvent,
    RealtimeModelSendUserInput,
    RealtimeRawModelEvent,
    RealtimeRunner,
)
from agents.realtime.testing import RealtimeStep, ScriptedRealtimeModel


@pytest.mark.asyncio
async def test_realtime_message() -> None:
    reply = RealtimeModelOutputTextDeltaEvent(
        item_id="item_1",
        delta="Hello!",
        response_id="response_1",
    )
    model = ScriptedRealtimeModel(
        [
            RealtimeStep(
                expect=RealtimeModelSendUserInput(user_input="Hello"),
                emit=[reply],
            )
        ]
    )
    runner = RealtimeRunner(
        RealtimeAgent(name="Assistant"),
        model=model,
        config={"tracing_disabled": True},
    )

    observed_reply = False
    async with await runner.run() as session:
        await session.send_message("Hello")
        async for event in session:
            if isinstance(event, RealtimeRawModelEvent) and event.data == reply:
                observed_reply = True
                break

    assert observed_reply
    assert model.sent_events == (RealtimeModelSendUserInput(user_input="Hello"),)
    assert model.closed is True
    model.assert_complete()
```

期望可以是精确的事件值、与 `isinstance` 匹配的事件类，或者接收出站事件并返回匹配的 `True` 的可调用函数。默认情况下启用严格模式。使用`strict=False`，记录不相关的出站事件，但不消耗待处理步骤；当会话发出测试行为之外的偶然事件时，这非常有用。

使用 `connect_events` 在连接期间发出入站事件。对于生命周期故障，请使用 `connect_error` 或 `close_error`；对于与一个匹配的发送相关的故障，请使用 `RealtimeStep(error=...)`。一个步骤不能同时定义 `emit` 和 `error`。

### 测试实时工具工作流程

将真实函数工具附加到 `RealtimeAgent`，发出标准化工具调用，并期望 SDK 通过模型边界发送工具输出。将 `async_tool_calls` 设置为 `False` 可以使这个小示例在连接期间完成，而无需测试特定的等待机制。

```python
import pytest

from agents.decorators import tool
from agents.realtime import (
    RealtimeAgent,
    RealtimeModelSendToolOutput,
    RealtimeModelToolCallEvent,
    RealtimeRunner,
)
from agents.realtime.testing import RealtimeStep, ScriptedRealtimeModel


@tool
def lookup_order(order_id: str) -> str:
    """Look up an order by ID."""
    return f"Order {order_id} has shipped."


@pytest.mark.asyncio
async def test_realtime_tool_workflow() -> None:
    tool_call = RealtimeModelToolCallEvent(
        name="lookup_order",
        call_id="call_1",
        arguments='{"order_id":"order_123"}',
    )

    def matches_tool_output(event) -> bool:
        return (
            isinstance(event, RealtimeModelSendToolOutput)
            and event.tool_call.call_id == "call_1"
            and event.output == "Order order_123 has shipped."
        )

    model = ScriptedRealtimeModel(
        [RealtimeStep(expect=matches_tool_output)],
        connect_events=[tool_call],
    )
    agent = RealtimeAgent(
        name="Order assistant",
        tools=[lookup_order],
    )
    runner = RealtimeRunner(
        agent,
        model=model,
        config={"async_tool_calls": False, "tracing_disabled": True},
    )

    async with await runner.run():
        pass

    model.assert_complete()
```

这练习了真正的实时工具查找、参数验证、执行和输出路由。它并不能证明真实模型会选择该工具。

### 检查实时调用和生命周期

|会员|包含|
| --- | --- |
| `connect_calls` |无凭据、分离的连接快照|
| `sent_events` |按调用顺序分离出站事件快照|
| `remaining_steps` |剩余的预期出站发送|
| `listeners` |当前注册的监听器对象|
| `connected`, `closed`, `close_calls` |当前内存生命周期状态|

连接历史记录仅记录是否提供了API-密钥或标头字段；它从不存储它们的值。 URL 快照删除用户信息、查询参数和片段。可变事件数据和设置是分离的，而实时 SDK 对象（例如工具、切换和播放跟踪器）保留身份。

完成 `model.assert_complete()` 并让 `RealtimeSession` 异步上下文管理器关闭模型。 Python 实用程序故意不提供挂起的期望承诺、隐式超时或单独的 `assert_closed()` 帮助程序。

|错误|结构化字段|含义|
| --- | --- | --- |
| `UnexpectedRealtimeSend` | `actual`, `expected` |严格的出站发送与下一步不匹配，或者没有剩余步骤|
| `UnconsumedRealtimeSteps` | `remaining_steps` |会话在使用每个预期发送之前结束|
| `RealtimeScriptError` |无|该脚本在无效的生命周期状态下使用，例如在断开连接时发送|

## 语音管道配方

### 测试语音管道

将脚本化的 STT 和 TTS 模型与 `SingleAgentVoiceWorkflow` 和由 `ScriptedModel` 支持的 Agent 相结合，以测试完整的语音到文本 -> Agent -> 文本到语音管道，无需提供商请求。

```python
import numpy as np
import pytest

from agents import Agent
from agents.testing import ScriptedModel, assistant_message
from agents.voice import AudioInput, SingleAgentVoiceWorkflow, VoicePipeline
from agents.voice.testing import (
    ScriptedSTTModel,
    ScriptedTTSModel,
    TTSResult,
    pcm16_samples,
)


@pytest.mark.asyncio
async def test_voice_pipeline() -> None:
    model = ScriptedModel([[assistant_message("Hello there.")]])
    stt = ScriptedSTTModel("hello")
    pcm = pcm16_samples([0, 100, -100, 0])
    tts = ScriptedTTSModel([TTSResult([pcm])])
    pipeline = VoicePipeline(
        workflow=SingleAgentVoiceWorkflow(Agent(name="Voice assistant", model=model)),
        stt_model=stt,
        tts_model=tts,
        config={"tracing_disabled": True, "tts_settings": {"buffer_size": 1}},
    )

    result = await pipeline.run(AudioInput(np.zeros(2, dtype=np.int16)))
    events = [event async for event in result.stream()]

    assert events
    assert [call.text for call in tts.calls] == ["Hello there."]
    stt.assert_complete()
    tts.assert_complete()
    model.assert_complete()
```

当管道的 STT/TTS 生命周期正在测试但 Agent 编排未测试时，请使用 `ScriptedVoiceWorkflow`：

```python
from agents.voice.testing import ScriptedVoiceWorkflow


workflow = ScriptedVoiceWorkflow(
    turns=["Hello there."],
    start="Welcome.",
)
```

`start` 步骤被 `on_start()` 消耗。 `VoicePipeline`仅针对`StreamedAudioInput`调用`on_start()`；静态 `AudioInput` 运行不会消耗 `start`。每个正常回合都会记录其转录并消耗一个配置的结果。一个字符串就是一个片段；字符串序列控制文本分割之前的片段边界和TTS。

### 测试流转录

`ScriptedSTTModel` 接受静态 `transcriptions` 和独立脚本流 `sessions`。会话可以是 `ScriptedTranscriptionSession`、转录轮序列、异常或单个字符串：

```python
from agents.voice.testing import ScriptedSTTModel, ScriptedTranscriptionSession


session = ScriptedTranscriptionSession(["first turn", "second turn"])
stt = ScriptedSTTModel(sessions=[session])
```

关闭 `ScriptedTranscriptionSession` 将停止迭代并留下跳过的回合以供 `assert_complete()` 报告。 `ScriptedTTSModel` 类似地每次调用都会消耗一个 `TTSResult`、字节块序列或异常。

### 检查语音通话

|组件|记录历史|
| --- | --- |
| `ScriptedSTTModel` |`calls`、`session_calls` 和实时 `created_sessions` 身份|
| `ScriptedTTSModel` |`calls` 包含文本和独立设置|
| `ScriptedVoiceWorkflow` |`transcriptions`依次顺序|

静态音频缓冲区和可变设置在调用时进行快照。 `StreamedAudioInput` 和创建的转录会话对象保留其实时身份，因为管道继续使用它们。

|错误|结构化字段|含义|
| --- | --- | --- |
| `UnexpectedVoiceCall` | `operation` |静态转录、流式会话、TTS 调用、工作流启动或工作流轮转没有配置的步骤|
| `UnconsumedVoiceSteps` | `remaining_steps` |保留一个或多个已配置的语音步骤|

在测试配置的每个脚本语音组件上调用 `assert_complete()`。 `ScriptedSTTModel.assert_complete()` 还会检查其创建的转录会话中的轮次。

## 选择正确的边界

当测试应该执行 SDK 运行循环、工具、切换、护栏、会话、重试或规范化流而不依赖于模型提供程序时，请使用 `ScriptedModel`。

当测试应在不启动沙箱提供程序的情况下运用 `SandboxAgent` 功能和编排时，将 `scripted_sandbox_session()` 与 `ScriptedModel` 结合使用。在针对真实沙箱提供程序的集成测试中保持提供程序创建、流程执行、文件系统保真度、持久性、资源限制和隔离检查。

当测试应执行 `RealtimeSession` 行为或 `RealtimeAgent` 工具并切换编排而不打开 WebSocket 连接时，请使用 `ScriptedRealtimeModel`。在真实传输或集成环境中保留原始实时 client/server 事件、身份验证、网络恢复和音频传输行为。实时 API 会话在客户端发送输入和接收事件时保持连接打开，因此这些网络和协议问题属于规范化模型边界以下。有关生产连接架构，请参阅 [OpenAI 实时 API 指南 ](<https://developers.openai.com/api/docs/guides/realtime>)。

当测试需要执行 STT/TTS 排序、流式转录清理、工作流片段交付或在没有语音提供程序的情况下完成语音管道组合时，请使用语音测试组件。当转录质量、生成的语音、编码兼容性、延迟或播放是测试主题时，使用真实的音频模型和代表性音频。

请勿使用这些实用程序来测试响应 API 或聊天完成请求序列化、身份验证标头、提供程序默认值、HTTP 负载、提供程序流块、实时线框或提供程序特定的生命周期行为。保留真正的适配器并替换或控制其网络边界以进行这些测试。对于 `openai` v3、OpenAI 适配器测试应使用 `httpx2` 请求、响应、传输和异常类型；旧版 `httpx` 不是 Agents SDK 的核心依赖项。

## 最终清单

- 仅编写规范化模型、沙箱会话、实时模型或语音管道边界所拥有的交互的脚本。
- 断言重要的公共请求或调用字段而不是私有运行器状态。
- 喜欢固定的响应步骤；仅将响应程序用于依赖于请求的行为。
- 更喜欢自动模型流；仅当事件级行为很重要时才使用精确流。
- 使用其 `assert_complete()` 方法结束每个脚本组件测试。
- 当周围的测试拥有该生命周期时，使用异步上下文管理器进行实时和沙盒生命周期清理。
- 断言结构化错误字段而不是解析人类可读的消息。
- 通过受控网络传输对真实适配器进行提供商线路测试。

## 范围和当前限制

测试模块故意不提供：

- 每个标准化模型输出项的便利构建器。常见情况使用`assistant_message()`和`function_call()`，其他标准化项直接传递。
- 提供者协议模拟器。精确模型流使用规范化的 SDK 事件而不是响应 API 或聊天完成线块。
- 高级模拟实时服务器。测试显式匹配规范化的出站发送并发出场景所需的规范化的入站事件。
- 无序沙箱或实时期望。两个实用程序都消耗一个全局订单中的预期步骤。
- 测试运行程序特定的匹配器、固定装置、隐式超时或自动拆卸。
- 重置 API。 `ScriptedModel` 支持增量脚本的 `enqueue()` 和 `extend()`，但为独立场景创建新的脚本组件。

当测试需要格式错误的流、受控挂起或并发、精确取消或脚本化实用程序无法保留的生命周期边界时，请使用相应公共接口的自定义实现。记录测试中的专门边界。

## API参考

- [`agents.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/testing.md>)
- [`agents.realtime.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/realtime/testing.md>)
- [`agents.voice.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/voice/testing.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Testing

The SDK provides deterministic, provider-neutral testing utilities for Agent workflows, Sandbox sessions, Realtime sessions, and Voice pipelines. These utilities run in memory, make no model, sandbox-provider, or Realtime API requests, and record the normalized interactions that the SDK owns. The runnable recipes below disable tracing for each run so that the default trace processor does not upload test activity when an OpenAI API key is configured.

Use them to test orchestration owned by your application and the SDK: tool execution, handoffs, guardrails, retries, streaming, session behavior, Sandbox capabilities, Realtime event handling, and Voice pipeline composition. Use real provider adapters or integration environments for behavior owned by an external model, network protocol, sandbox provider, or audio system.

## Find the recipe you need

| I want to... | Use | Go to |
| --- | --- | --- |
| Return a fixed final answer | `ScriptedModel` with `assistant_message()` | [Return a fixed response](<#return-a-fixed-response>) |
| Exercise a multi-turn tool loop | `function_call()` followed by an assistant response | [Test a tool workflow](<#test-a-tool-workflow>) |
| Choose a response from the request | `ModelStep.respond()` or a `responder` mapping | [Derive a response from the request](<#derive-a-response-from-the-request>) |
| Assert what the runner sent to the model | `calls`, `first_call`, or `last_call` | [Inspect model calls](<#inspect-model-calls>) |
| Test a streamed run | A normal response step, or `ModelStep.stream()` for exact events | [Test streaming](<#test-streaming>) |
| Test an error or retry decision | `ModelStep.raise_error()` | [Inject model failures](<#inject-model-failures>) |
| Detect an accidental workflow change | Exact FIFO steps plus `assert_complete()` | [Detect workflow drift](<#detect-workflow-drift>) |
| Test a `SandboxAgent` without starting a sandbox | `scripted_sandbox_session()` plus `ScriptedModel` | [Test a Sandbox Agent workflow](<#test-a-sandbox-agent-workflow>) |
| Match Sandbox calls or derive their results | `match` or `responder` on a Sandbox step | [Configure Sandbox steps](<#configure-sandbox-steps>) |
| Test a Realtime session without opening a connection | `ScriptedRealtimeModel` and `RealtimeStep` | [Test a Realtime session](<#test-a-realtime-session>) |
| Test a Realtime tool workflow | Emit a `RealtimeModelToolCallEvent` and expect tool output | [Test a Realtime tool workflow](<#test-a-realtime-tool-workflow>) |
| Test a static or streamed Voice pipeline | `ScriptedSTTModel`, `ScriptedTTSModel`, and a scripted or real workflow | [Test a Voice pipeline](<#test-a-voice-pipeline>) |
| Test provider serialization or wire payloads | The real provider adapter with a controlled network transport | [Choose the correct boundary](<#choose-the-correct-boundary>) |

## Imports

The testing APIs live next to the runtime boundary they replace:

| Boundary | Import path |
| --- | --- |
| Agent model and Sandbox workflows | `agents.testing` |
| Realtime model transport | `agents.realtime.testing` |
| Voice STT, TTS, and workflow components | `agents.voice.testing` |

Testing symbols are intentionally kept out of the top-level `agents` import.

## Agent workflow recipes

### Return a fixed response

Pass one sequence of normalized output items for each expected model call. The output-sequence shorthand receives a deterministic response ID and usage for one request.

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.testing import ScriptedModel, assistant_message


@pytest.mark.asyncio
async def test_fixed_response() -> None:
    model = ScriptedModel([[assistant_message("Paris is the capital of France.")]])
    agent = Agent(name="Geography assistant", model=model)

    result = await Runner.run(
        agent,
        "What is the capital of France?",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "Paris is the capital of France."
    assert len(model.calls) == 1
    model.assert_complete()
```

Finish deterministic workflow tests with `model.assert_complete()`. It catches the case where the workflow stopped before consuming every configured step.

### Test a tool workflow

Script one model response that calls the tool and a second response that produces the final answer. The real SDK tool pipeline runs between those model calls.

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.decorators import tool
from agents.testing import ScriptedModel, assistant_message, function_call


@tool
def get_weather(city: str) -> str:
    """Return the weather for a city."""
    return f"{city}: sunny"


@pytest.mark.asyncio
async def test_tool_workflow() -> None:
    model = ScriptedModel(
        [
            [function_call("get_weather", {"city": "Tokyo"}, call_id="call_1")],
            [assistant_message("It is sunny in Tokyo.")],
        ]
    )
    agent = Agent(name="Weather assistant", model=model, tools=[get_weather])

    result = await Runner.run(
        agent,
        "What is the weather in Tokyo?",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "It is sunny in Tokyo."
    assert len(model.calls) == 2
    assert model.last_call is not None
    assert any(item.get("type") == "function_call_output" for item in model.last_call.input)
    model.assert_complete()
```

This pattern covers tool input validation, execution, result conversion, hooks, guardrails, and the next model turn. Calling the Python function directly would bypass those SDK behaviors.

### Derive a response from the request

Use `ModelStep.respond()` when a response genuinely depends on the normalized model call or when an assertion belongs at the model boundary. The responder may be synchronous or asynchronous and may return any step shape accepted by `ScriptedModel`.

```python
import pytest

from agents import Agent, RunConfig, Runner
from agents.testing import ModelCall, ModelStep, ScriptedModel, assistant_message


def respond(call: ModelCall):
    assert call.streamed is False
    assert call.input == [{"content": "Summarize this", "role": "user"}]
    return {"output": [assistant_message("Handled the normalized request.")]}


@pytest.mark.asyncio
async def test_request_aware_response() -> None:
    model = ScriptedModel([ModelStep.respond(respond)])
    agent = Agent(name="Assistant", model=model)

    result = await Runner.run(
        agent,
        "Summarize this",
        run_config=RunConfig(tracing_disabled=True),
    )

    assert result.final_output == "Handled the normalized request."
    model.assert_complete()
```

`ScriptedModel` accepts `ModelStep`, the equivalent dictionary form, `ModelResponse`, a normalized output-item sequence, or an exception. Prefer fixed output sequences when a response does not depend on the call because fixed scripts make unexpected turns easier to diagnose.

### Inspect model calls

`ScriptedModel` records each call before it resolves or raises the selected step.

| Member | Contains |
| --- | --- |
| `calls` | Every `ModelCall` in invocation order |
| `first_call` | The first call, or `None` |
| `last_call` | The most recent call, or `None` |
| `remaining_steps` | The number of configured steps not yet consumed |

Common assertions include `call.input`, `call.model_settings`, `call.tools`, `call.handoffs`, and `call.streamed`. Mutable request data is snapshotted at the invocation boundary, and each public history accessor returns detached snapshots. Tool, handoff, output-schema, and tracing objects keep their runtime identity.

Structured `call_index` and `input_index` error fields are zero-based so they directly index `calls[...]` or the supplied step sequence. Human-readable error messages display one-based call or step numbers.

Use `enqueue()` or `extend()` when one test needs to append model steps incrementally. Create a new `ScriptedModel` for an independent scenario; the utility does not reset consumed steps or call history.

### Test streaming

A normal response step supports both `Runner.run()` and `Runner.run_streamed()`. For common assistant messages, reasoning items, function calls, and apply-patch calls, `ScriptedModel` generates normalized start, delta, item-completion, and terminal response events. The terminal response carries the complete output and usage.

Use `ModelStep.stream()` only when the exact normalized `TResponseStreamEvent` sequence is part of the behavior under test:

```python
step = ModelStep.stream(
    events,
    output=[assistant_message("The terminal output used by the runner.")],
)
```

`events` may be a fixed sequence or an async factory that receives the recorded `ModelCall`. The optional `output` is the response returned if the same step is used in a non-streaming call. Exact stream events are SDK-normalized events, not Responses API or Chat Completions wire chunks.

Automatic streaming rejects normalized output-item kinds whose incremental lifecycle is not implemented. Use `ModelStep.stream(...)` for those items instead of relying on a partial event sequence.

### Inject model failures

Use `ModelStep.raise_error()` to fail one model call. Optional retry advice belongs to that exact scripted error:

```python
from agents import ModelRetryAdvice
from agents.testing import ModelStep


step = ModelStep.raise_error(
    RuntimeError("temporary failure"),
    retry_advice=ModelRetryAdvice(suggested=True, replay_safety="safe"),
)
```

The runner's retry policy decides whether advice causes another attempt. Each retry is another model call and consumes the next scripted step. The Python helper accepts a fixed `ModelRetryAdvice` value; use a custom `Model` when retry advice itself must vary dynamically by attempt.

### Detect workflow drift

Treat the scripted calls as the expected workflow shape. An extra model request raises `UnexpectedModelCall`; an early exit leaves steps for `assert_complete()` to report.

When your test framework supports teardown or finalizers, place `assert_complete()` there if you also want unconsumed steps reported after another assertion fails. Do not catch mismatch errors in a normal regression test.

| Error | Structured fields | Meaning |
| --- | --- | --- |
| `InvalidModelStep` | `reason`, `input_index` | A step is malformed and is rejected before entering the queue |
| `UnexpectedModelCall` | `call`, `call_index` | The workflow made another model call after the script ended |
| `UnconsumedModelSteps` | `remaining_steps` | The workflow ended before using every step |

## Sandbox Agent recipes

### Test a Sandbox Agent workflow

Combine `ScriptedModel` with `scripted_sandbox_session()` to exercise the real `SandboxAgent` runtime without creating a local container or remote sandbox. The model script chooses a capability tool, while the Sandbox script defines what the corresponding `SandboxSession` method returns.

```python
import pytest

from agents import RunConfig, Runner
from agents.sandbox import ExecResult, SandboxAgent
from agents.sandbox.capabilities import Shell
from agents.testing import (
    ScriptedModel,
    assistant_message,
    function_call,
    scripted_sandbox_session,
)


@pytest.mark.asyncio
async def test_sandbox_workflow() -> None:
    sandbox = scripted_sandbox_session(
        [
            {
                "method": "exec",
                "match": lambda call: call.args == ("pwd",),
                "result": ExecResult(
                    stdout=b"/workspace\n",
                    stderr=b"",
                    exit_code=0,
                ),
            }
        ]
    )
    model = ScriptedModel(
        [
            [function_call("exec_command", {"cmd": "pwd"}, call_id="call_1")],
            [assistant_message("The workspace is /workspace.")],
        ]
    )
    agent = SandboxAgent(
        name="Workspace assistant",
        model=model,
        capabilities=[Shell()],
    )

    async with sandbox:
        result = await Runner.run(
            agent,
            "Which directory are you in?",
            run_config=RunConfig(
                sandbox={"session": sandbox},
                tracing_disabled=True,
            ),
        )

    assert result.final_output == "The workspace is /workspace."
    assert [call.method for call in sandbox.calls] == ["exec"]
    sandbox.assert_complete()
    model.assert_complete()
```

This test crosses two normalized SDK boundaries. It covers tool argument validation, capability routing, Sandbox session invocation, delivery of the tool result to the next model turn, and final output handling. It does not test whether a real model chooses the command or how a real sandbox provider executes it.

### Configure Sandbox steps

Each matching Sandbox call consumes the next step in one global FIFO sequence. A method mismatch, matcher rejection, or matcher exception leaves that step pending. Set `method`, choose exactly one outcome, and add `match` only when the call details matter.

| Step member | Use it when... |
| --- | --- |
| `result` | The method should return a fixed typed value |
| `responder` | The result depends on the detached `SandboxCall` |
| `error` | The method should raise a specific exception |
| `match` | The call should be rejected before producing its outcome unless the matcher returns a value other than `False` |

The supported scripted method names are `apply_patch`, `exec`, `ls`, `mkdir`, `pty_exec_start`, `pty_write_stdin`, `read`, `rm`, and `write`. Only configured model-facing capabilities are exposed. The two PTY methods are exposed together when either PTY method is configured because they form one interactive-shell capability, but calls still consume the global FIFO script.

`sandbox.calls` contains detached `SandboxCall` snapshots with zero-based `call_index`, `method`, positional `args`, and read-only `kwargs`. Static results are also snapshotted when the script is created. `io.BytesIO` and `io.StringIO` values are supported; use a custom Sandbox session for other live stream objects or lifecycle behavior.

| Error | Structured fields | Meaning |
| --- | --- | --- |
| `InvalidSandboxStep` | `reason`, `input_index`, `method` | A step is malformed or names an unsupported method |
| `UnexpectedSandboxCall` | `call`, `call_index`, `actual_method`, `expected_method`, `remaining_steps` | The workflow called the wrong method or continued after the script ended |
| `SandboxCallMatcherError` | `call`, `call_index`, `method` | A step matcher returned `False` |
| `UnconsumedSandboxSteps` | `remaining_steps`, `pending_methods` | The workflow ended before using every step |

The returned object is the session itself. Pass it directly to `RunConfig(sandbox={"session": sandbox})`; there is no wrapper `.session` attribute.

## Realtime recipes

### Test a Realtime session

`ScriptedRealtimeModel` implements the Python SDK's normalized `RealtimeModel` boundary. Each `RealtimeStep` matches one outbound `RealtimeModelSendEvent` and then emits normalized inbound `RealtimeModelEvent` objects or raises an injected error.

```python
import pytest

from agents.realtime import (
    RealtimeAgent,
    RealtimeModelOutputTextDeltaEvent,
    RealtimeModelSendUserInput,
    RealtimeRawModelEvent,
    RealtimeRunner,
)
from agents.realtime.testing import RealtimeStep, ScriptedRealtimeModel


@pytest.mark.asyncio
async def test_realtime_message() -> None:
    reply = RealtimeModelOutputTextDeltaEvent(
        item_id="item_1",
        delta="Hello!",
        response_id="response_1",
    )
    model = ScriptedRealtimeModel(
        [
            RealtimeStep(
                expect=RealtimeModelSendUserInput(user_input="Hello"),
                emit=[reply],
            )
        ]
    )
    runner = RealtimeRunner(
        RealtimeAgent(name="Assistant"),
        model=model,
        config={"tracing_disabled": True},
    )

    observed_reply = False
    async with await runner.run() as session:
        await session.send_message("Hello")
        async for event in session:
            if isinstance(event, RealtimeRawModelEvent) and event.data == reply:
                observed_reply = True
                break

    assert observed_reply
    assert model.sent_events == (RealtimeModelSendUserInput(user_input="Hello"),)
    assert model.closed is True
    model.assert_complete()
```

An expectation may be an exact event value, an event class matched with `isinstance`, or a callable that receives the outbound event and returns `True` for a match. Strict mode is enabled by default. With `strict=False`, unrelated outbound events are recorded but do not consume a pending step; this is useful when a session emits incidental events that are outside the behavior under test.

Use `connect_events` to emit inbound events during connection. Use `connect_error` or `close_error` for lifecycle failures, and use `RealtimeStep(error=...)` for a failure tied to one matched send. A step cannot define both `emit` and `error`.

### Test a Realtime tool workflow

Attach a real function tool to `RealtimeAgent`, emit a normalized tool call, and expect the SDK to send the tool output through the model boundary. Setting `async_tool_calls` to `False` makes this small example complete during connection without test-specific waiting machinery.

```python
import pytest

from agents.decorators import tool
from agents.realtime import (
    RealtimeAgent,
    RealtimeModelSendToolOutput,
    RealtimeModelToolCallEvent,
    RealtimeRunner,
)
from agents.realtime.testing import RealtimeStep, ScriptedRealtimeModel


@tool
def lookup_order(order_id: str) -> str:
    """Look up an order by ID."""
    return f"Order {order_id} has shipped."


@pytest.mark.asyncio
async def test_realtime_tool_workflow() -> None:
    tool_call = RealtimeModelToolCallEvent(
        name="lookup_order",
        call_id="call_1",
        arguments='{"order_id":"order_123"}',
    )

    def matches_tool_output(event) -> bool:
        return (
            isinstance(event, RealtimeModelSendToolOutput)
            and event.tool_call.call_id == "call_1"
            and event.output == "Order order_123 has shipped."
        )

    model = ScriptedRealtimeModel(
        [RealtimeStep(expect=matches_tool_output)],
        connect_events=[tool_call],
    )
    agent = RealtimeAgent(
        name="Order assistant",
        tools=[lookup_order],
    )
    runner = RealtimeRunner(
        agent,
        model=model,
        config={"async_tool_calls": False, "tracing_disabled": True},
    )

    async with await runner.run():
        pass

    model.assert_complete()
```

This exercises the real Realtime tool lookup, argument validation, execution, and output routing. It does not prove that a real model will choose the tool.

### Inspect Realtime calls and lifecycle

| Member | Contains |
| --- | --- |
| `connect_calls` | Credential-free, detached connection snapshots |
| `sent_events` | Detached outbound event snapshots in invocation order |
| `remaining_steps` | Expected outbound sends that remain |
| `listeners` | Currently registered listener objects |
| `connected`, `closed`, `close_calls` | Current in-memory lifecycle state |

Connection history records only whether API-key or header fields were supplied; it never stores their values. URL snapshots remove user information, query parameters, and fragments. Mutable event data and settings are detached, while live SDK objects such as tools, handoffs, and playback trackers preserve identity.

Finish with `model.assert_complete()` and let the `RealtimeSession` async context manager close the model. The Python utility intentionally does not provide pending expectation promises, implicit timeouts, or a separate `assert_closed()` helper.

| Error | Structured fields | Meaning |
| --- | --- | --- |
| `UnexpectedRealtimeSend` | `actual`, `expected` | A strict outbound send did not match the next step, or no step remained |
| `UnconsumedRealtimeSteps` | `remaining_steps` | The session ended before using every expected send |
| `RealtimeScriptError` | none | The script was used in an invalid lifecycle state, such as sending while disconnected |

## Voice pipeline recipes

### Test a Voice pipeline

Combine scripted STT and TTS models with `SingleAgentVoiceWorkflow` and an Agent backed by `ScriptedModel` to test the full speech-to-text -> Agent -> text-to-speech pipeline without provider requests.

```python
import numpy as np
import pytest

from agents import Agent
from agents.testing import ScriptedModel, assistant_message
from agents.voice import AudioInput, SingleAgentVoiceWorkflow, VoicePipeline
from agents.voice.testing import (
    ScriptedSTTModel,
    ScriptedTTSModel,
    TTSResult,
    pcm16_samples,
)


@pytest.mark.asyncio
async def test_voice_pipeline() -> None:
    model = ScriptedModel([[assistant_message("Hello there.")]])
    stt = ScriptedSTTModel("hello")
    pcm = pcm16_samples([0, 100, -100, 0])
    tts = ScriptedTTSModel([TTSResult([pcm])])
    pipeline = VoicePipeline(
        workflow=SingleAgentVoiceWorkflow(Agent(name="Voice assistant", model=model)),
        stt_model=stt,
        tts_model=tts,
        config={"tracing_disabled": True, "tts_settings": {"buffer_size": 1}},
    )

    result = await pipeline.run(AudioInput(np.zeros(2, dtype=np.int16)))
    events = [event async for event in result.stream()]

    assert events
    assert [call.text for call in tts.calls] == ["Hello there."]
    stt.assert_complete()
    tts.assert_complete()
    model.assert_complete()
```

Use `ScriptedVoiceWorkflow` instead when the pipeline's STT/TTS lifecycle is under test but Agent orchestration is not:

```python
from agents.voice.testing import ScriptedVoiceWorkflow


workflow = ScriptedVoiceWorkflow(
    turns=["Hello there."],
    start="Welcome.",
)
```

The `start` step is consumed by `on_start()`. `VoicePipeline` calls `on_start()` only for `StreamedAudioInput`; a static `AudioInput` run does not consume `start`. Each normal turn records its transcription and consumes one configured result. A string is one fragment; a sequence of strings controls fragment boundaries before text splitting and TTS.

### Test streamed transcription

`ScriptedSTTModel` accepts static `transcriptions` and independently scripted streamed `sessions`. A session may be a `ScriptedTranscriptionSession`, a sequence of transcription turns, an exception, or a single string:

```python
from agents.voice.testing import ScriptedSTTModel, ScriptedTranscriptionSession


session = ScriptedTranscriptionSession(["first turn", "second turn"])
stt = ScriptedSTTModel(sessions=[session])
```

Closing `ScriptedTranscriptionSession` stops iteration and leaves skipped turns for `assert_complete()` to report. `ScriptedTTSModel` similarly consumes one `TTSResult`, byte-chunk sequence, or exception per call.

### Inspect Voice calls

| Component | Recorded history |
| --- | --- |
| `ScriptedSTTModel` | `calls`, `session_calls`, and live `created_sessions` identities |
| `ScriptedTTSModel` | `calls` containing text and detached settings |
| `ScriptedVoiceWorkflow` | `transcriptions` in turn order |

Static audio buffers and mutable settings are snapshotted at invocation time. A `StreamedAudioInput` and created transcription-session objects keep their live identity because the pipeline continues to use them.

| Error | Structured fields | Meaning |
| --- | --- | --- |
| `UnexpectedVoiceCall` | `operation` | A static transcription, streamed session, TTS call, workflow start, or workflow turn had no configured step |
| `UnconsumedVoiceSteps` | `remaining_steps` | One or more configured Voice steps remain |

Call `assert_complete()` on every scripted Voice component that the test configures. `ScriptedSTTModel.assert_complete()` also checks turns in the transcription sessions that it created.

## Choose the correct boundary

Use `ScriptedModel` when a test should exercise the SDK run loop, tools, handoffs, guardrails, sessions, retries, or normalized streaming without depending on a model provider.

Use `scripted_sandbox_session()` with `ScriptedModel` when a test should exercise `SandboxAgent` capabilities and orchestration without starting a sandbox provider. Keep provider creation, process execution, filesystem fidelity, persistence, resource limits, and isolation checks in integration tests against the real sandbox provider.

Use `ScriptedRealtimeModel` when a test should exercise `RealtimeSession` behavior or `RealtimeAgent` tool and handoff orchestration without opening a WebSocket connection. Keep raw Realtime client/server events, authentication, network recovery, and audio transport behavior on the real transport or in an integration environment. Realtime API sessions keep a connection open while the client sends input and receives events, so those network and protocol concerns belong below the normalized model boundary. See the [OpenAI Realtime API guide](<https://developers.openai.com/api/docs/guides/realtime>) for production connection architectures.

Use the Voice testing components when a test should exercise STT/TTS ordering, streamed transcription cleanup, workflow fragment delivery, or complete Voice pipeline composition without speech providers. Use real audio models and representative audio when transcription quality, generated speech, encoding compatibility, latency, or playback is the subject of the test.

Do not use these utilities to test Responses API or Chat Completions request serialization, authentication headers, provider defaults, HTTP payloads, provider stream chunks, Realtime wire frames, or provider-specific lifecycle behavior. Keep the real adapter and replace or control its network boundary for those tests. With `openai` v3, OpenAI adapter tests should use `httpx2` request, response, transport, and exception types; legacy `httpx` is not a core dependency of the Agents SDK.

## Final checklist

- Script only interactions owned by the normalized model, Sandbox session, Realtime model, or Voice pipeline boundary.
- Assert important public request or call fields instead of private runner state.
- Prefer fixed response steps; use responders only for request-dependent behavior.
- Prefer automatic model streaming; use exact streams only when event-level behavior matters.
- End each scripted component test with its `assert_complete()` method.
- Use async context managers for Realtime and Sandbox lifecycle cleanup when the surrounding test owns that lifecycle.
- Assert structured error fields instead of parsing human-readable messages.
- Keep provider wire tests on real adapters with controlled network transports.

## Scope and current limitations

The testing modules deliberately do not provide:

- Convenience builders for every normalized model output item. Use `assistant_message()` and `function_call()` for common cases, and pass other normalized items directly.
- A provider-protocol simulator. Exact model streams use normalized SDK events rather than Responses API or Chat Completions wire chunks.
- A high-level simulated Realtime server. Tests explicitly match normalized outbound sends and emit the normalized inbound events required by the scenario.
- Unordered Sandbox or Realtime expectations. Both utilities consume expected steps in one global order.
- Test-runner-specific matchers, fixtures, implicit timeouts, or automatic teardown.
- Reset APIs. `ScriptedModel` supports `enqueue()` and `extend()` for an incremental script, but create a new scripted component for an independent scenario.

Use a custom implementation of the corresponding public interface when a test requires malformed streams, controlled suspension or concurrency, exact cancellation, or a lifecycle boundary that the scripted utilities cannot preserve. Document that specialized boundary in the test.

## API reference

- [`agents.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/testing.md>)
- [`agents.realtime.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/realtime/testing.md>)
- [`agents.voice.testing`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/ref/voice/testing.md>)


</details>
