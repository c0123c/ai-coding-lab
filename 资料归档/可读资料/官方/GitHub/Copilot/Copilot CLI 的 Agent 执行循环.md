# Copilot CLI 的 Agent 执行循环

解释提示到工具调用和事件输出的过程，区分会话空闲与任务完成事件，帮助理解 SDK 状态与自动执行。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-05-28](<https://github.com/github/docs/commit/e9226542b7c4f042874679e11eb6bbebb7b0033a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/features/agent-loop.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot CLI 的 Agent 执行循环.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: The agent loop
shortTitle: Agent Loop
intro: >-
  How the Copilot CLI processes a user message end-to-end: from prompt to
  `session.idle`.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-copilot-sdk/agent-loop
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## 建筑

![图表：显示所描述过程的图表。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-0.png>)

**SDK** 是一个传输层 - 它通过 JSON-RPC 将您的提示发送到 **Copilot CLI**，并将事件表面返回到您的应用程序。 **CLI** 是运行代理工具使用循环的协调器，发出一个或多个 LLM API 调用，直到任务完成。

## 工具使用循环

当调用`session.send({ prompt })`时，CLI进入循环：

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-1.png>)

该模型可以看到每次调用的 ** 完整对话历史记录 ** — 系统提示、用户消息以及所有先前的工具调用和结果。

**关键见解：** 此循环的每次迭代恰好是一次 LLM API 调用，在事件日志中显示为一对 `assistant.turn_start` / `assistant.turn_end`。没有隐藏呼叫。

## 转弯——它们是什么

**turn** 是单个 LLM API 调用及其后果：

1. CLI将会话历史记录发送到LLM
1. LLM 响应（可能带有工具请求）
1. 如果请求工具，CLI 会执行它们
1. `assistant.turn_end` 已发出

单个用户消息通常会导致 ** 多次转向 **。例如，诸如“X 在此代码库中如何工作？”之类的问题。可能会产生：

|转|模型的作用| toolRequests? |
|------|-------------------|---------------|
| 1 |调用 `grep` 和 `glob` 搜索代码库|✅ 是的|
| 2 |根据搜索结果读取特定文件|✅ 是的|
| 3 |阅读更多文件以获得更深入的上下文|✅ 是的|
| 4 |产生最终的文本答案|❌ 否 → 循环结束|

该模型在每一回合都会决定是否请求更多工具或产生最终答案。每次调用都会看到**完整累积的上下文**（所有先前的工具调用和结果），因此它可以就是否拥有足够的信息做出明智的决定。

## 多轮交互的事件流

![图表：显示所描述过程的流程图。](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-2.png>)

## 每个回合谁触发？

|演员|责任|
|-------|---------------|
|**您的应用**|通过 `session.send()` 发送初始提示|
| **Copilot CLI** |运行工具使用循环 — 执行工具并将结果反馈到 LLM 以进行下一回合|
| **LLM** |决定是请求工具（继续循环）还是生成最终响应（停止）|
| **SDK** |传递事件；不控制循环|

CLI 是纯粹机械的：“模型要求工具→执行→再次调用模型。” **模型**是何时停止的决策者。

## `session.idle` 与 `session.task_complete`

这是两个不同的完成信号，具有截然不同的保证：

### `session.idle`

* **工具使用循环结束时始终发出**
* **临时**：未持久保存到磁盘，不在会话恢复时重播
* 意思是：“agent已停止处理并准备好接收下一条消息”
* **使用此**作为可靠的“完成”信号

SDK 的 `sendAndWait()` 方法等待此事件：

```typescript
// Blocks until session.idle fires
const response = await session.sendAndWait({ prompt: "Fix the bug" });
```

### `session.task_complete`

* **可选地发出**：要求模型明确地发出信号
* **Persisted**：保存到磁盘上的会话事件日志
* 意思是：“agent认为总体任务已完成”
* 带有可选的 `summary` 字段

```typescript
session.on("session.task_complete", (event) => {
    console.log("Task done:", event.data.summary);
});
```

### 自动驾驶模式：CLI 轻推 `task_complete`

在**自动驾驶模式**（headless/autonomous操作）下，CLI主动跟踪模型是否调用了`task_complete`。如果工具使用循环在没有它的情况下结束，CLI 会注入一条合成用户消息来推动模型：

> *“您尚未使用 task_complete 工具将任务标记为完成。如果您正在计划，请停止计划并开始实施。直到完全完成该任务，您才算完成。”*

这有效地重新启动了工具使用循环——模型将微移视为新的用户消息并继续工作。微移还指示模型 ** 而不是 ** 过早调用 `task_complete`：

* 如果您有悬而未决的问题，请不要调用它——做出决定并继续工作
* 如果遇到错误，请不要调用它 - 尝试解决它
* 如果还有剩余步骤，请不要调用它 - 首先完成它们

这会在自动驾驶仪中创建一个**两级完成机制**：
1. 模型调用 `task_complete` 并提供摘要 → CLI 发出 `session.task_complete` → 完成
1. 模型停止而不调用它 → CLI 轻推 → 模型继续或调用 `task_complete`

### 为什么 `task_complete` 可能不会出现

在**交互模式**（正常聊天）下，CLI不会轻推`task_complete`。该模型可能会完全跳过它。常见原因：

* **对话式问答**：模型回答了一个问题，然后就停止了——没有需要完成的离散“任务”
* **模型自行判断**：模型在不调用任务完成信号的情况下生成最终文本响应
* **中断的会话**：会话在模型到达完成点之前结束

不管怎样，CLI 都会发出 `session.idle`，因为它是一种机械信号（循环结束），而不是语义信号（模型认为它已经完成）。

### 你应该使用哪个？

|使用案例|信号|
|----------|--------|
|“等待agent处理完成”| `session.idle` ✅ |
|“知道编码任务何时完成”|`session.task_complete`（尽力而为）|
|“Timeout/error处理”| `session.idle` + `session.error` ✅ |

## 计算 LLM 通话次数

事件日志中的 `assistant.turn_start` / `assistant.turn_end` 对的数量等于进行的 LLM API 调用的总数。没有隐藏的计划、评估或完成检查的要求。

要检查会话的轮数：

```bash
# Count turns in a session's event log
grep -c "assistant.turn_start" ~/.copilot/session-state/<sessionId>/events.jsonl
```

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>)：每种事件类型的完整字段级参考
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/session-persistence>)：会话如何保存和恢复
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/hooks>)：循环中拦截事件（权限、工具）


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

## Architecture

![Diagram: Graph diagram showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-0.png>)

The **SDK** is a transport layer—it sends your prompt to the **Copilot CLI** over JSON-RPC and surfaces events back to your app. The **CLI** is the orchestrator that runs the agentic tool-use loop, making one or more LLM API calls until the task is done.

## The tool-use loop

When you call `session.send({ prompt })`, the CLI enters a loop:

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-1.png>)

The model sees the **full conversation history** on each call—system prompt, user message, and all prior tool calls and results.

**Key insight:** Each iteration of this loop is exactly one LLM API call, visible as one `assistant.turn_start` / `assistant.turn_end` pair in the event log. There are no hidden calls.

## Turns—what they are

A **turn** is a single LLM API call and its consequences:

1. The CLI sends the conversation history to the LLM
1. The LLM responds (possibly with tool requests)
1. If tools were requested, the CLI executes them
1. `assistant.turn_end` is emitted

A single user message typically results in **multiple turns**. For example, a question like "how does X work in this codebase?" might produce:

| Turn | What the model does | toolRequests? |
|------|-------------------|---------------|
| 1 | Calls `grep` and `glob` to search the codebase | ✅ Yes |
| 2 | Reads specific files based on search results | ✅ Yes |
| 3 | Reads more files for deeper context | ✅ Yes |
| 4 | Produces the final text answer | ❌ No → loop ends |

The model decides on each turn whether to request more tools or produce a final answer. Each call sees the **full accumulated context** (all prior tool calls and results), so it can make an informed decision about whether it has enough information.

## Event flow for a multi-turn interaction

![Diagram: Flowchart showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/features-agent-loop-diagram-2.png>)

## Who triggers each turn?

| Actor | Responsibility |
|-------|---------------|
| **Your app** | Sends the initial prompt via `session.send()` |
| **Copilot CLI** | Runs the tool-use loop—executes tools and feeds results back to the LLM for the next turn |
| **LLM** | Decides whether to request tools (continue looping) or produce a final response (stop) |
| **SDK** | Passes events through; does not control the loop |

The CLI is purely mechanical: "model asked for tools → execute → call model again." The **model** is the decision-maker for when to stop.

## `session.idle` vs `session.task_complete`

These are two different completion signals with very different guarantees:

### `session.idle`

* **Always emitted** when the tool-use loop ends
* **Ephemeral**: not persisted to disk, not replayed on session resume
* Means: "the agent has stopped processing and is ready for the next message"
* **Use this** as your reliable "done" signal

The SDK's `sendAndWait()` method waits for this event:

```typescript
// Blocks until session.idle fires
const response = await session.sendAndWait({ prompt: "Fix the bug" });
```

### `session.task_complete`

* **Optionally emitted**: requires the model to explicitly signal it
* **Persisted**: saved to the session event log on disk
* Means: "the agent considers the overall task fulfilled"
* Carries an optional `summary` field

```typescript
session.on("session.task_complete", (event) => {
    console.log("Task done:", event.data.summary);
});
```

### Autopilot mode: the CLI nudges for `task_complete`

In **autopilot mode** (headless/autonomous operation), the CLI actively tracks whether the model has called `task_complete`. If the tool-use loop ends without it, the CLI injects a synthetic user message nudging the model:

> *"You have not yet marked the task as complete using the task_complete tool. If you were planning, stop planning and start implementing. You aren't done until you have fully completed the task."*

This effectively restarts the tool-use loop—the model sees the nudge as a new user message and continues working. The nudge also instructs the model **not** to call `task_complete` prematurely:

* Don't call it if you have open questions—make decisions and keep working
* Don't call it if you hit an error—try to resolve it
* Don't call it if there are remaining steps—complete them first

This creates a **two-level completion mechanism** in autopilot:
1. The model calls `task_complete` with a summary → CLI emits `session.task_complete` → done
1. The model stops without calling it → CLI nudges → model continues or calls `task_complete`

### Why `task_complete` might not appear

In **interactive mode** (normal chat), the CLI does not nudge for `task_complete`. The model may skip it entirely. Common reasons:

* **Conversational Q&A**: The model answers a question and simply stops—there's no discrete "task" to complete
* **Model discretion**: The model produces a final text response without calling the task-complete signal
* **Interrupted sessions**: The session ends before the model reaches a completion point

The CLI emits `session.idle` regardless, because it's a mechanical signal (the loop ended), not a semantic one (the model thinks it's done).

### Which should you use?

| Use case | Signal |
|----------|--------|
| "Wait for the agent to finish processing" | `session.idle` ✅ |
| "Know when a coding task is done" | `session.task_complete` (best-effort) |
| "Timeout/error handling" | `session.idle` + `session.error` ✅ |

## Counting LLM calls

The number of `assistant.turn_start` / `assistant.turn_end` pairs in the event log equals the total number of LLM API calls made. There are no hidden calls for planning, evaluation, or completion checking.

To inspect turn count for a session:

```bash
# Count turns in a session's event log
grep -c "assistant.turn_start" ~/.copilot/session-state/<sessionId>/events.jsonl
```

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/streaming-events>): Full field-level reference for every event type
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/session-persistence>): How sessions are saved and resumed
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/features/hooks>): Intercepting events in the loop (permissions, tools)


</details>
