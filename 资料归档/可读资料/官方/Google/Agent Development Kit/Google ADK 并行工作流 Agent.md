# Google ADK 并行工作流 Agent

解释子任务并发与独立状态管理，以并行研究案例展示汇总方式；属于框架编排参考。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/google/adk-docs/commit/c75e61f5f3eb2c47ef56aa7aa52eadc91a59d4d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/agents/workflow-agents/parallel-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/agents/workflow-agents/parallel-agents.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 并行模板工作流程 agent

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div> 支持

的 ***ParallelAgent*** 类是一个 [模板工作流程](<https://google.github.io/adk-docs/agents/workflow-agents/>)
agent 执行其子agents 同时。这个执行策略可以
显着加快可以执行两项或多项任务的工作流程
独立。对于速度优先且涉及独立的场景，
资源密集型任务，这种模板化工作流程有助于并行
执行，这可以显着减少总体处理时间。使用时
这种工作流程类型，重要的是每个子agent 无需操作即可操作
取决于其他子agents。这种工作流程类型特别有益
对于多源数据检索或繁重计算等操作，其中
并行化可以带来显着的性能提升。

与其他模板化工作流程一样，***ParallelAgent***对象
的执行不受AI模型控制，并且其执行其
子agents的方式是确定的。并行执行集中指定的子 agents 可能会或可能 
 不会使用 AI 模型，但这些子 agents 的总体执行是 
 最终由您定义的 ***ParallelAgent*** 对象管理。

！！！注意“替代方案：基于图形的工作流程”

从 ADK 2.0 开始，Python 和 Go 的模板化工作流程已被取代

通过更灵活的工作流程结构，包括
 [基于图的工作流程](<https://google.github.io/adk-docs/graphs/>)和
 [动态工作流程](<https://google.github.io/adk-docs/graphs/dynamic/>)。

### 它是如何运作的

当调用`ParallelAgent`的`run_async()`方法时：

1. **并发执行：**它启动*每个*子agent存在于`sub_agents`列表中的`run_async()`方法*同时*。  这意味着所有 agents （大约）同时开始运行。
2. **独立分支：**每个子agent在其自己的执行分支中运行。  在执行期间，***没有*在这些分支**之间自动共享对话历史记录或状态。
3. **结果集合：** `ParallelAgent` 管理并行执行，并且通常提供一种在每个子 agent 完成后访问结果的方法（例如，通过结果或事件列表）。结果的顺序可能不确定。

### 独立执行和状态管理

*理解`ParallelAgent`内的子agents独立运行是至关重要的*。  如果您*需要*在这些agents之间进行通信或数据共享，则必须显式实现它。  可能的方法包括：

* **共享`InvocationContext`：**您可以将共享`InvocationContext`对象传递给每个子agent。  该对象可以充当共享数据存储。  但是，您需要仔细管理对此共享上下文的并发访问（例如，使用锁）以避免竞争条件。
* **外部状态管理：**使用外部数据库、消息队列或其他机制来管理共享状态并促进agents之间的通信。
* **后处理：**收集每个分支的结果，然后实现逻辑来协调数据。

![并行Agent](<https://google.github.io/adk-docs/assets/parallel-agent.png>){: width="600"}

### 完整示例：并行网络研究

想象一下同时研究多个主题：

1. **研究员Agent 1：** 研究“可再生能源”的`LlmAgent`。
2. **研究员Agent2：**研究“电动汽车技术”的`LlmAgent`。
3. **研究员Agent 3：** 研究“碳捕获方法”的`LlmAgent`。

    ```py
    ParallelAgent(sub_agents=[ResearcherAgent1, ResearcherAgent2, ResearcherAgent3])
    ```

这些研究任务是独立的。  使用 `ParallelAgent` 允许它们同时运行，与顺序运行相比，可能会显着减少总研究时间。每个agent的结果将在完成后单独收集。

???+“完整代码”

    === "Python"
        ```py
         --8<-- "examples/python/snippets/agents/workflow-agents/parallel_agent_web_research.py:init"
        ```

    === "TypeScript"
        ```typescript
         --8<-- "examples/typescript/snippets/agents/workflow-agents/parallel_agent_web_research.ts:init"
        ```

    === "Go"
        ```go
         --8<-- "examples/go/snippets/agents/workflow-agents/parallel/main.go:init"
        ```

    === "Java"
        ```java
         --8<-- "examples/java/snippets/src/main/java/agents/workflow/ParallelResearchPipeline.java:full_code"
        ```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Parallel template workflow agent

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div>

The ***ParallelAgent*** class is a [template workflow](<https://google.github.io/adk-docs/agents/workflow-agents/>)
agent that executes its sub-agents concurrently. This execution strategy can
dramatically speed up workflows where two or more tasks can be performed
independently. For scenarios prioritizing speed and involving independent,
resource-intensive tasks, this templated workflow facilitates parallel
execution, which can significantly reduce overall processing time. When using
this workflow type, it is important that each sub-agent can operate without
depending on the other sub-agents. This workflow type is particularly beneficial
for operations like multi-source data retrieval or heavy computations, where
parallelization yields substantial performance gains.

As with other templated workflows, the execution of a ***ParallelAgent*** object
is not controlled by an AI model, and is deterministic in how it executes its
sub-agents. The sub-agents specified in the parallel execution set may or may
not utilize AI models, but the overall execution of those sub-agents is
ultimately managed by the ***ParallelAgent*** object you define.

!!! note "Alternative: graph-based workflows"

    Starting in ADK 2.0 for Python and Go, templated workflows have been superseded

    by more flexible workflow structures, including
    [graph-based workflows](<https://google.github.io/adk-docs/graphs/>) and
    [dynamic workflows](<https://google.github.io/adk-docs/graphs/dynamic/>).

### How it works

When the `ParallelAgent`'s `run_async()` method is called:

1. **Concurrent Execution:** It initiates the `run_async()` method of *each* sub-agent present in the `sub_agents` list *concurrently*.  This means all the agents start running at (approximately) the same time.
2. **Independent Branches:**  Each sub-agent operates in its own execution branch.  There is ***no* automatic sharing of conversation history or state between these branches** during execution.
3. **Result Collection:** The `ParallelAgent` manages the parallel execution and, typically, provides a way to access the results from each sub-agent after they have completed (e.g., through a list of results or events). The order of results may not be deterministic.

### Independent Execution and State Management

It's *crucial* to understand that sub-agents within a `ParallelAgent` run independently.  If you *need* communication or data sharing between these agents, you must implement it explicitly.  Possible approaches include:

* **Shared `InvocationContext`:** You could pass a shared `InvocationContext` object to each sub-agent.  This object could act as a shared data store.  However, you'd need to manage concurrent access to this shared context carefully (e.g., using locks) to avoid race conditions.
* **External State Management:**  Use an external database, message queue, or other mechanism to manage shared state and facilitate communication between agents.
* **Post-Processing:** Collect results from each branch, and then implement logic to coordinate data afterwards.

![Parallel Agent](<https://google.github.io/adk-docs/assets/parallel-agent.png>){: width="600"}

### Full Example: Parallel Web Research

Imagine researching multiple topics simultaneously:

1. **Researcher Agent 1:**  An `LlmAgent` that researches "renewable energy sources."
2. **Researcher Agent 2:**  An `LlmAgent` that researches "electric vehicle technology."
3. **Researcher Agent 3:**  An `LlmAgent` that researches "carbon capture methods."

    ```py
    ParallelAgent(sub_agents=[ResearcherAgent1, ResearcherAgent2, ResearcherAgent3])
    ```

These research tasks are independent.  Using a `ParallelAgent` allows them to run concurrently, potentially reducing the total research time significantly compared to running them sequentially. The results from each agent would be collected separately after they finish.

???+ "Full Code"

    === "Python"
        ```py
         --8<-- "examples/python/snippets/agents/workflow-agents/parallel_agent_web_research.py:init"
        ```

    === "TypeScript"
        ```typescript
         --8<-- "examples/typescript/snippets/agents/workflow-agents/parallel_agent_web_research.ts:init"
        ```

    === "Go"
        ```go
         --8<-- "examples/go/snippets/agents/workflow-agents/parallel/main.go:init"
        ```

    === "Java"
        ```java
         --8<-- "examples/java/snippets/src/main/java/agents/workflow/ParallelResearchPipeline.java:full_code"
        ```


</details>
