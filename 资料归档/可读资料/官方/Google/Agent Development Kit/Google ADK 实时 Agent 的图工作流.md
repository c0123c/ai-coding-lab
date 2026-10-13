# Google ADK 实时 Agent 的图工作流

说明将实时对话 Agent 放入节点与边组成的流程、统一事件流和对话中途交接；属于应用编排参考。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-10-02](<https://github.com/google/adk-docs/commit/ba962ed9cc4e13132d90ce2743f3807c30d4d83a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/live/workflows.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/live/Google ADK 实时 Agent 的图工作流.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 实时图形工作流程 agents

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v2.0.0</span>
</div> 支持

实时 agents 与任何其他 ADK agent 组成相同的图形工作流程。定义节点和 
 边、路由和状态在 [ 图工作流程 ](<https://github.com/google/adk-docs/blob/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/graphs/index.md>) 中介绍，
 更广泛的多 agent 图片在 [ 工作流程 ](<Google ADK 多 Agent 与多节点工作流入口.md>) 中介绍。在实时
连接下发生的变化是执行模型。

下 `run_live()`，整个管道 agents 运行 *在一个打开的连接和一个事件内
循环*，因此呼叫者会听到一个连续的对话。他们一边控制一边继续说话
从一开始移动 agent 到下一个，并且永远不会听到切换。

这也塑造了你的代码。使用 request/response agent，每个 agent 转换都是一个新的 
 调用您控制；这里，整个工作流程都是一个循环和一个队列，无论它跨越
多少agents。

## 在图表中运行 agents

图表 [`Workflow`](<https://github.com/google/adk-docs/blob/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/graphs/index.md>) 是您如何对 ADK 2.0 中的实时 agents 进行排序。您
将agents定义为节点并将它们与边连接，并且跑步者在
单个实时会话中遍历图形：

```python
from google.adk.agents.llm_agent import Agent
from google.adk.workflow import START, Workflow

LIVE_MODEL = 'gemini-live-2.5-flash-native-audio'

greeter = Agent(
    model=LIVE_MODEL,
    name='greeter',
    mode='task',  # required for the node to use the live connection
    instruction='Greet the caller and confirm you are speaking with John Doe. '
    'Ask one question per turn. Complete your task once the name is confirmed.',
)

verifier = Agent(
    model=LIVE_MODEL,
    name='verifier',
    mode='task',
    instruction='Verify the caller by date of birth, then complete your task.',
)

root_agent = Workflow(
    name='intake',
    edges=[
        (START, greeter),
        (greeter, verifier),
    ],
)
```

使用 `adk web` 提供此服务并启动实时会话，或将其传递给 `Runner.run_live()`。
 运行程序检测到 `Workflow` 根并通过实时连接驱动它；您在所有节点上使用
一个事件流。请参阅可运行的 
[`workflow` 示例 ](<https://github.com/google/adk-python/tree/main/contributing/samples/live/workflow>)
，了解具有类型切换和实时评估集的三阶段语音输入流程。

**每个说话的agent都需要`mode='task'`或`mode='chat'`。**作为工作流程中的节点，没有`mode`的
`LlmAgent`回退到`single_turn`，它在实时连接之外运行
并完全忽略音频队列，因此呼叫者听不到任何声音。在每个进行通信的节点上显式设置模式
。

每个节点在该节点的持续时间内打开自己的实时 API 会话，并且工作流的 
`LiveRequestQueue` 按顺序在节点之间共享。单个队列无法同时提供两个活动 
 节点，因此请将活动节点保持在一条路径上，而不是散开。

## 读取一个事件流

流在节点转换中是连续的。使用一个循环和一个队列 
 来使用它，并读取 `event.author` 来判断哪个 agent 在说话。

```python
queue = LiveRequestQueue()

async for event in runner.run_live(
    user_id='user_123',
    session_id='session_456',
    live_request_queue=queue,
):
    if event.content and event.content.parts:
        for part in event.content.parts:
            if part.inline_data and part.inline_data.mime_type.startswith('audio/'):
                await play_audio(part.inline_data.data)
            elif part.text:
                await display_text(f'[{event.author}] {part.text}')
```

不要为每个 agent 打开新的 `run_live()` 循环或新的 `LiveRequestQueue`。一个循环和
一个队列服务于整个工作流程；用户输入流向当前处于活动状态的节点。

## 谈话中途移交

协调员 agent 可以将对话传递给与 
`transfer_to_agent` 进行会议的专家。切换发生在同一个 `run_live()` 循环内：ADK 关闭 
 协调员的实时连接，为专家打开一个新的连接，并且用户保持 
 通话。

```text
User: "I need help with billing"
Event: author="coordinator", function_call: transfer_to_agent(agent_name="billing")
Event: author="billing", text="I can help with your billing question..."
```

传输会为目标 agent 启动一个新的实时 API 会话，因此来自协调器的会话恢复句柄 
 不会延续。为了保持协调者自己团队的转账，
在子agents上设置`disallow_transfer_to_peers`；不允许的兄弟转移会引发
`ValueError`。

## 旧工作流程 agents

使用图表 `Workflow` 作为新代码。 `SequentialAgent`、`LoopAgent` 和 `ParallelAgent` 已被 
** 弃用，取而代之的是 `Workflow`**，并将在未来版本中删除。 `LoopAgent`
 和 `ParallelAgent` 在 `run_live()` 下提升 `NotImplementedError` 并将导致实时 
 会话崩溃，因此请保持两者远离任何实时路径。

`SequentialAgent` 仍以实时模式运行。当它执行时，ADK 会向每个直接 `LlmAgent` 子 agent 添加一个 `task_completed`
 工具，并附加一条指令，告诉模型在任务完成时 
 调用它。调用 `task_completed` 会结束子 agent 的实时 
 连接，并前进到序列中的下一个 agent。

```python
# ADK injects this into each LlmAgent sub-agent at live-run time.
def task_completed():
    """Signals that the agent has completed the user's task."""
    return 'Task completion signaled.'
```

事件流看起来像任何实时工作流程：每个 agent 运行一系列事件，然后是 
`task_completed` 函数响应，然后下一个 agent 开始：

```text
Event: author="researcher", function_call: task_completed()
Event: author="writer", text="Based on the research..."
```

`task_completed` 和 `transfer_to_agent` 由于不同的原因结束了 agent 的回合：

|功能|图案|效果|
|----------|---------|--------|
| `task_completed` |固定顺序|结束当前agent；序列中的下一个 agent 开始|
| `transfer_to_agent` |动态路由|关闭当前直播会话；为目标 agent 打开一个新会话|


## 原文对照

<details>
<summary>展开完整原文</summary>

# Graph workflows for live agents

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v2.0.0</span>
</div>

Live agents compose into the same graph workflows as any other ADK agent. Defining nodes and
edges, routing, and state are covered in [Graph workflows](<https://github.com/google/adk-docs/blob/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/graphs/index.md>), and the
broader multi-agent picture in [Workflows](<Google ADK 多 Agent 与多节点工作流入口.md>). What changes under a live
connection is the execution model.

Under `run_live()`, a whole pipeline of agents runs *inside one open connection and one event
loop*, so the caller hears a single continuous conversation. They keep talking while control
moves from one agent to the next, and never hear the handoff.

That shapes your code too. With a request/response agent, each agent transition is a fresh
call you control; here it is one loop and one queue for the entire workflow, no matter how
many agents it spans.

## Run agents in a graph

A graph [`Workflow`](<https://github.com/google/adk-docs/blob/12712d00d48712998f15a2a9d8bd26d708b727a3/docs/graphs/index.md>) is how you sequence live agents in ADK 2.0. You
define the agents as nodes and connect them with edges, and the runner walks the graph over a
single live session:

```python
from google.adk.agents.llm_agent import Agent
from google.adk.workflow import START, Workflow

LIVE_MODEL = 'gemini-live-2.5-flash-native-audio'

greeter = Agent(
    model=LIVE_MODEL,
    name='greeter',
    mode='task',  # required for the node to use the live connection
    instruction='Greet the caller and confirm you are speaking with John Doe. '
    'Ask one question per turn. Complete your task once the name is confirmed.',
)

verifier = Agent(
    model=LIVE_MODEL,
    name='verifier',
    mode='task',
    instruction='Verify the caller by date of birth, then complete your task.',
)

root_agent = Workflow(
    name='intake',
    edges=[
        (START, greeter),
        (greeter, verifier),
    ],
)
```

Serve this with `adk web` and start a live session, or pass it to `Runner.run_live()`.
The runner detects a `Workflow` root and drives it over the live connection; you consume
one event stream across all nodes. See the runnable
[`workflow` sample](<https://github.com/google/adk-python/tree/main/contributing/samples/live/workflow>)
for a three-stage voice intake flow with typed handoffs and a live eval set.

**Every agent that speaks needs `mode='task'` or `mode='chat'`.** As a node in a workflow, an
`LlmAgent` with no `mode` falls back to `single_turn`, which runs outside the live connection
and ignores the audio queue entirely, so the caller hears nothing from it. Set the mode
explicitly on every node that talks.

Each node opens its own Live API session for the duration of that node, and the workflow's
`LiveRequestQueue` is shared across nodes in sequence. A single queue cannot feed two live
nodes at once, so keep live nodes on one path rather than fanning out.

## Read one event stream

The stream is continuous across node transitions. Consume it with one loop and one queue,
and read `event.author` to tell which agent is speaking.

```python
queue = LiveRequestQueue()

async for event in runner.run_live(
    user_id='user_123',
    session_id='session_456',
    live_request_queue=queue,
):
    if event.content and event.content.parts:
        for part in event.content.parts:
            if part.inline_data and part.inline_data.mime_type.startswith('audio/'):
                await play_audio(part.inline_data.data)
            elif part.text:
                await display_text(f'[{event.author}] {part.text}')
```

Do not open a new `run_live()` loop or a new `LiveRequestQueue` per agent. One loop and
one queue serve the whole workflow; user input flows to whichever node is currently active.

## Hand off mid-conversation

A coordinator agent can pass the conversation to a specialist mid-session with
`transfer_to_agent`. The handoff happens inside the same `run_live()` loop: ADK closes the
coordinator's live connection, opens a fresh one for the specialist, and the user keeps
talking.

```text
User: "I need help with billing"
Event: author="coordinator", function_call: transfer_to_agent(agent_name="billing")
Event: author="billing", text="I can help with your billing question..."
```

Transfers start a new Live API session for the target agent, so session-resumption handles
from the coordinator do not carry over. To keep transfers on the coordinator's own team,
set `disallow_transfer_to_peers` on the sub-agents; a disallowed sibling transfer raises a
`ValueError`.

## Legacy workflow agents

Use a graph `Workflow` for new code. `SequentialAgent`, `LoopAgent`, and `ParallelAgent` are
**deprecated in favor of `Workflow`** and will be removed in a future release. `LoopAgent`
and `ParallelAgent` raise `NotImplementedError` under `run_live()` and will crash a live
session, so keep both off any live path.

`SequentialAgent` still runs in live mode. When it does, ADK adds a `task_completed`
tool to each direct `LlmAgent` sub-agent and appends an instruction telling the model to
call it when the task is done. Calling `task_completed` ends that sub-agent's live
connection and advances to the next agent in the sequence.

```python
# ADK injects this into each LlmAgent sub-agent at live-run time.
def task_completed():
    """Signals that the agent has completed the user's task."""
    return 'Task completion signaled.'
```

The event stream looks like any live workflow: a run of events per agent, then a
`task_completed` function response, then the next agent begins:

```text
Event: author="researcher", function_call: task_completed()
Event: author="writer", text="Based on the research..."
```

`task_completed` and `transfer_to_agent` end an agent's turn for different reasons:

| Function | Pattern | Effect |
|----------|---------|--------|
| `task_completed` | Fixed sequence | Ends the current agent; the next agent in the sequence begins |
| `transfer_to_agent` | Dynamic routing | Closes the current live session; a new session opens for the target agent |


</details>
