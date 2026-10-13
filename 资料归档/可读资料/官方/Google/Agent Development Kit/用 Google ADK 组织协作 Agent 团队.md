# 用 Google ADK 组织协作 Agent 团队

说明协作模式、角色切换、上下文隔离、运行注意事项和限制，供需要多角色协作的应用开发参考。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-07-08](<https://github.com/google/adk-docs/commit/a52a2d48a28bad34e85c083713865bbe65eb28b0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/workflows/collaboration.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/用 Google ADK 组织协作 Agent 团队.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 建立协作 agent 团队

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v2.0.0</span><span class="lst-go">Go v2.0.0</span>
</div> 支持

一些复杂的任务可能需要多个具有特定职责的 agents 
，并从结构化程度较低的过程中受益，特别是对于具有多个大量子任务的迭代 
 流程。在 
ADK 的协作 agent 团队中，协调员 agent 负责将任务委托给一个或多个子代理。
这种方法可以更轻松地构建复杂的、自我管理的 agent 系统，
 定义了要处理的子代理特定任务，完成任务后自动返回
父级。

当使用这种自我管理的agent团队方法时，子代理被分配一个
操作***模式***来管理他们的行为并限制他们的工作范围。
这些***模式***为子代理设置一般行为准则并创建更多
可预测且可靠的多agent工作流程。以下设置
可用于协作模式：

-   ***聊天***：完整的用户交互，手动返回父级agent
（默认，当前行为）
-   ***任务***：用于澄清的用户交互并自动返回
父级 agent
-   ***单转：***无需用户交互即可自动返回，并且可以
并行运行

本指南介绍了如何使用子代理的模式以及这些模式如何影响 
agent 行为。

！！！警告“已禁用：基于图形的工作流程中的任务模式”

协作模式 `task` 行为已禁用，无法在 ADK Python v2.0.0 中的 
 基于图形的工作流程中使用。此功能
预计将在未来版本中重新启用。

## 开始使用

以下代码示例显示如何为
一小组子代理设置操作模式并将它们分配给协调器agent：

=== "Python"

    ```python
    from google.adk import Agent

    weather_agent = Agent(
        name="weather_checker",
        mode="single_turn",         # no user interaction
        tools=[get_weather, user_info, geocode_address],
    )
    flight_agent = Agent(
        name="flight_booker",
        mode="task",                # can ask user questions
        input_schema=FlightInput,
        output_schema=FlightResult,
        tools=[search_flights, book_flight],
    )
    root = Agent(
        name="travel_planner",      # coordinator agent
        sub_agents=[weather_agent, flight_agent],
        # Auto-injects delegation tools named after each subagent:
        # weather_checker, flight_booker
    )
    ```

=== "Go"

在ADK Go v2.0.0中，`llmagent.Config`上的`Mode`字段接受与Python相同的
模式字符串：`"chat"`， `"task"`和`"single_turn"`。在协调器 agent 上声明 
 `SubAgents` 会导致 ADK 自动为每个子代理生成 
 一个委托工具，以子代理本身命名，
 与 Python 中的完全相同。

    ```go
    --8<-- "examples/go/snippets/workflows/collaboration/main.go:get-started"
    ```

当您运行此工作流程时，`travel_planner` 协调器 agent 自动
 识别任务并将其分配给子代理。当子代理完成 
 任务时，它会自动返回到协调器 agent。
有关使用 ***input_schema*** 和 
***output_schema*** 以及 agents、子代理和工作流节点构建数据的更多信息，请参阅
[agent工作流程的数据处理](<https://google.github.io/adk-docs/graphs/data-handling/>)。

## 模式配置和行为

每种协作模式都有与其相关的特定行为和限制
。下表比较了配置为
每种模式的子代理的属性：

！！！警告“警告：模式仅适用于子代理”

***模式***设置专门用于由协调器父级agent调用的子代理
。请勿使用模式 
 设置配置根 agent。

<table>
 <thead>
 <tr>
 <th><strong>主题\模式</strong></th>
 <th><code>chat</code>（默认）</th>
 <th><code>task</code></th>
 <th><code>single_turn</code></th>
 </tr>
 </thead>
 <tbody>
 <tr>
 <td><strong>人在循环中</strong></td>
 <td>完全互动</td>
 <td>对于仅澄清</td>
 <td>不允许</td>
 </tr>
 <tr>
 <td><strong>用户交互</strong></td>
 <td>用户与agent</td>
 <td>Agent 根据需要提出问题</td>
 <td>无用户交互</td>
 </tr>
 <tr>
 <td><strong>控制流</strong></td>
 <td>Agent 控制直至手动切换 </td>
 <td>Agent 控制直至任务完成 </td>
 <td>任务完成后立即返回 </td>
 </tr>
 <tr>
 <td><strong>并行执行</strong></td>
 <td>不支持</td>
 <td>不支持</td>
 <td>多个任务可以并行运行</td>
 </tr>
 <tr>
 <td><strong>返回父级</strong></td>
 <td>手动（通过传输）</td>
 <td>自动（通过<code>finish_task</code>)</td>
 <td>自动（有结果）</td>
 </tr>
 </tbody>
</table>

**表1。**ADK协作agent***模式***行为和
限制的比较。

## 操作注意事项

使用协作 agent 模式时，需要考虑一些控制转移和 
 上下文管理注意事项，如以下 
 部分所述。

### 工作流程节点和 Agent 传输

Agents配置为***任务***或***单轮***模式可以用作
工作流Agent图形节点，并与***LlmAgent***一起使用实例。然而，
 执行传输行为根据调用或父级 
agent 的不同而有所不同：

**作为工作流程图节点：**当任务或单轮agent放置在
工作流程图内时 - 例如***SequentialAgent***或***ParallelAgent***（Python
和Go 预构建 agents），或用 ADK Go
v2.0.0 图形引擎中的 `workflow.NewAgentNode` 包装 — agent 执行其任务。完成后，控件
根据工作流
agent图形的逻辑自动前进到下一个节点。

**作为 LlmAgent 的受让人：** 当父级 ***LlmAgent*** 通过以该子代理命名的委派工具将 
 控制权转移给任务 agent 时，
 任务agent 一直执行，直到调用 `finish_task`。此时，控制
自动返回到发起传输的原始agent。
此行为与默认的聊天***模式***agents不同，后者需要
显式`transfer_to_agent` 要求交回控制权。

<table>
 <thead>
 <tr>
 <th><strong>调用上下文</strong></th>
 <th><strong>任务完成后</strong></th>
 </tr>
 </thead>
 <tbody>
 <tr>
 <td>工作流节点</td>
 <td>前进到图中的下一个节点</td>
 </tr>
 <tr>
 <td>从 LlmAgent</td>
 转移 <td>将控制权返回给原始 agent</td>
 </tr>
 </tbody>
</table>

这种区别允许在两个上下文 
 中重复使用相同的任务 agent，而无需修改。运行时根据
如何调用agent来确定适当的控制流。

### Agent 上下文隔离

每个 *** 任务 *** 或 *** 单轮 *** 模式 agent 在其自己的隔离 
 会话分支中运行。当这些agents并行操作时，每个agent在为AI模型调用构建上下文时只能看到来自其自己分支的
事件，而无法
看到其对等agents正在做什么。一旦所有并行分支完成，
父级agent就会收到收集的结果并可以继续。

## 已知的限制

agent 协作模式存在一些已知的限制：

-   ***任务*模式agents**必须是叶agents并且不能有子代理。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Build collaborative agent teams

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v2.0.0</span><span class="lst-go">Go v2.0.0</span>
</div>

Some complex tasks may require multiple agents with specific responsibilities
and benefit from less structured procedures, particularly for iterative
processes with several, substantial sub-tasks. In a collaborative agent team in
ADK, a coordinator agent handles delegation of tasks to one or more subagents.
This approach makes it easier to build complex, self-managing agent systems,
with subagents defined to handle specific tasks, and automatic return to the
parent after completing a task.

When using this self-managed agent team approach, the subagents are assigned an
operating ***mode*** to manage their behavior and limit their scope of work.
These ***modes*** set general behavior guidelines for subagents and create more
predictable and reliable mulit-agent workflows. The following settings are
available for collaboration modes:

-   ***Chat***: Full user interaction, manual return to parent agent
    (default, current behavior)
-   ***Task***: User interaction for clarifications with automatic return to
    parent agent
-   ***Single-turn:*** No user interaction with automatic return and can be
    run in parallel

This guide covers how to use modes for your subagents and how these modes impact
agent behavior.

!!! warning "Disabled: Task mode in graph-based workflows"

    The collaborative mode `task` behavior is disabled for use in
    graph-based workflows in ADK Python v2.0.0. This feature
    is expected to be re-enabled in a future release.

## Get started

The following code example shows how to set operating modes for
a small team of subagents and assign them to a coordinator agent:

=== "Python"

    ```python
    from google.adk import Agent

    weather_agent = Agent(
        name="weather_checker",
        mode="single_turn",         # no user interaction
        tools=[get_weather, user_info, geocode_address],
    )
    flight_agent = Agent(
        name="flight_booker",
        mode="task",                # can ask user questions
        input_schema=FlightInput,
        output_schema=FlightResult,
        tools=[search_flights, book_flight],
    )
    root = Agent(
        name="travel_planner",      # coordinator agent
        sub_agents=[weather_agent, flight_agent],
        # Auto-injects delegation tools named after each subagent:
        # weather_checker, flight_booker
    )
    ```

=== "Go"

    In ADK Go v2.0.0, the `Mode` field on `llmagent.Config` accepts the same
    mode strings as Python: `"chat"`, `"task"`, and `"single_turn"`. Declaring
    `SubAgents` on the coordinator agent causes ADK to automatically generate
    a delegation tool for each subagent, named after the subagent itself,
    exactly as in Python.

    ```go
    --8<-- "examples/go/snippets/workflows/collaboration/main.go:get-started"
    ```

When you run this workflow, the `travel_planner` coordinator agent automatically
identifies and assigns tasks to the subagents. When a subagent completes
a task, it automatically returns to the coordinator agent.
For more information about structuring data using ***input_schema*** and
***output_schema*** with agents, subagents, and workflow nodes, see
[Data handling for agent workflows](<https://google.github.io/adk-docs/graphs/data-handling/>).

## Mode configuration and behaviors

Each collaboration mode has specific behaviors and limitations associated with
it. The following table compares the attributes of a subagent configured with
each mode:

!!! warning "Caution: Mode only for subagents"

    The ***mode*** setting is intended specifically for use with subagents invoked
    by a coordinator parent agent. Do not configure a root agent with the mode
    setting.

<table>
  <thead>
    <tr>
      <th><strong>Topic \ Mode</strong></th>
      <th><code>chat</code> (default)</th>
      <th><code>task</code></th>
      <th><code>single_turn</code></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Human in the Loop</strong></td>
      <td>Full interaction</td>
      <td>For clarification only</td>
      <td>Disallowed</td>
    </tr>
    <tr>
      <td><strong>User interaction</strong></td>
      <td>User chats freely with agent</td>
      <td>Agent asks questions as needed</td>
      <td>No user interaction</td>
    </tr>
    <tr>
      <td><strong>Control flow</strong></td>
      <td>Agent controls until manual handoff</td>
      <td>Agent controls until task complete</td>
      <td>Returns immediately after task</td>
    </tr>
    <tr>
      <td><strong>Parallel execution</strong></td>
      <td>Not supported</td>
      <td>Not supported</td>
      <td>Multiple tasks can run in parallel</td>
    </tr>
    <tr>
      <td><strong>Return to parent</strong></td>
      <td>Manual (via transfer)</td>
      <td>Automatic (via <code>finish_task</code>)</td>
      <td>Automatic (with result)</td>
    </tr>
  </tbody>
</table>

**Table 1.** Comparison of ADK Collaboration agent ***mode*** behavior and
limitations.

## Operating considerations

When using collaboration agent modes, there are a few control transfer and
context management considerations to consider, as described in the following
sections.

### Workflow Node and Agent transfers

Agents configured with ***task*** or ***single-turn*** modes can be used as
Workflow Agent graph nodes, and with ***LlmAgent*** instances. However the
execution transfer behavior is different depending on the calling, or parent,
agent:

**As a workflow graph node:** When a task or single-turn agent is placed within
a workflow graph — such as a ***SequentialAgent*** or ***ParallelAgent*** (Python
and Go prebuilt agents), or wrapped with `workflow.NewAgentNode` in the ADK Go
v2.0.0 graph engine — the agent executes its task. Upon completion, control
automatically advances to the next node based on the logic of the workflow
agent's graph.

**As a transferee from an LlmAgent:** When a parent ***LlmAgent*** transfers
control to a task agent via the delegation tool named after that subagent, the
task agent executes until it calls `finish_task`. At that point, control
automatically returns to the originating agent that initiated the transfer.
This behavior differs from default, chat ***mode*** agents, which require
explicit `transfer_to_agent` calls to hand back control.

<table>
  <thead>
    <tr>
      <th><strong>Invocation Context</strong></th>
      <th><strong>After Task Completion</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Workflow node</td>
      <td>Advances to next node in the graph</td>
    </tr>
    <tr>
      <td>Transfer from LlmAgent</td>
      <td>Returns control to the originating agent</td>
    </tr>
  </tbody>
</table>

This distinction allows the same task agent to be reused in both contexts
without modification. The runtime determines the appropriate control flow based
on how the agent was invoked.

### Agent context isolation

Each ***task*** or ***single-turn*** mode agent operates in its own isolated
session branch. When these agents operate in parallel, each agent only sees
events from its own branch when building context for AI model calls, and cannot
see what its peer agents are doing. Once all parallel branches complete, the
parent agent receives the collected results and can proceed.

## Known limitations

There are some known limitations with agent collaboration modes:

-   ***Task* mode agents** must be leaf agents and cannot have subagents.


</details>
