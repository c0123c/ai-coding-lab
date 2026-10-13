# Google ADK 顺序工作流 Agent

介绍固定顺序执行子 Agent 和共享状态，以代码开发流水线为例串联生成、审查及改进。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/google/adk-docs/commit/1ab7227d6a764c245743a9915c819554d48ffdf1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/agents/workflow-agents/sequential-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/agents/workflow-agents/sequential-agents.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 顺序模板工作流程 agent

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div> 支持

***SequentialAgent*** 类是一个 [ 模板工作流程 ](<https://google.github.io/adk-docs/agents/workflow-agents/>)
agent，它按照列表中指定的顺序执行其子 agents。
 当您希望以固定的方式执行时，请使用 ***SequentialAgent***，严格
顺序。与其他模板化工作流程一样，
***SequentialAgent*** 对象的执行不受 AI 模型控制，并且 
 确定如何执行其子 agents。 
顺序执行集中指定的子agents可能会也可能不会使用AI模型，但这些子agents的总体
执行最终由您定义的***SequentialAgent***
对象管理。

！！！注意“替代方案：基于图形的工作流程”

从 ADK 2.0 开始，Python 和 Go 的模板化工作流程已被取代

通过更灵活的工作流程结构，包括
 [基于图的工作流程](<https://google.github.io/adk-docs/graphs/>)和
 [动态工作流程](<https://google.github.io/adk-docs/graphs/dynamic/>)。

### 示例场景

你想建立一个 agent 可以使用两个工具总结任何网页：
**获取页面内容** 和 **摘要页面**。自从 agent 必须始终打电话
**获取页面内容** 打电话之前 **摘要页面**，你可以建立你的
agent 使用 ***SequentialAgent*** 类。

### 它是如何运作的

当调用`SequentialAgent`的`Run Async`方法时，它会执行以下操作：

1. **迭代：** 它按照提供的顺序迭代子 agents 列表。
2. **子Agent执行：**对于列表中的每个子agent，它调用子agent的`Run Async`方法。

![顺序 Agent](<https://google.github.io/adk-docs/assets/sequential-agent.png>){: width="600"}

！！！注意“共享调用上下文”
 `SequentialAgent` 将相同的 `InvocationContext` 传递给其每个 
 子 agents。这意味着它们都共享相同的会话状态，包括 
 临时 (`temp:`) 命名空间，从而可以轻松地在 
 单轮内的步骤之间传递数据。

### 完整示例：代码开发管道

考虑一个简化的代码开发管道：

* **代码编写器 Agent:** 根据规范生成初始代码的 LLM Agent。
* **代码审查者 Agent：** 一个 LLM Agent，负责审查生成的代码是否有错误、样式问题以及是否遵守最佳实践。  它接收代码编写器 Agent 的输出。
* **代码重构器 Agent：** 一个 LLM Agent，它接受审阅的代码和审阅者的评论，并重构它以提高质量和解决问题。

使用 `SequentialAgent` 可以轻松定义此执行流程，如以下代码片段中的 
 所示：

```py
SequentialAgent(sub_agents=[CodeWriterAgent, CodeReviewerAgent, CodeRefactorerAgent])
```

这确保了代码按照严格、可靠的顺序编写，*然后*审查，*最后*重构。 **每个子agent的输出通过[输出密钥](<https://google.github.io/adk-docs/agents/llm-agents/##data-handling>)**将它们存储在状态中传递到下一个。

???+“代码”

    === "Python"
        ```py
        --8<-- "examples/python/snippets/agents/workflow-agents/sequential_agent_code_development_agent.py:init"
        ```

    === "TypeScript"
        ```typescript
        --8<-- "examples/typescript/snippets/agents/workflow-agents/sequential_agent_code_development_agent.ts:init"
        ```

    === "Go"
        ```go
        --8<-- "examples/go/snippets/agents/workflow-agents/sequential/main.go:init"
        ```

    === "Java"
        ```java
        --8<-- "examples/java/snippets/src/main/java/agents/workflow/SequentialAgentExample.java:init"
        ```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sequential template workflow agent

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div>

The ***SequentialAgent*** class is a [template workflow](<https://google.github.io/adk-docs/agents/workflow-agents/>)
agent that executes its sub-agents in the order they are specified in a list.
Use ***SequentialAgent*** when you want execution to occur in a fixed, strict
order. As with other templated workflows, the execution of a
***SequentialAgent*** object is not controlled by an AI model, and is
deterministic in how it executes its sub-agents. The sub-agents specified in the
sequential execution set may or may not utilize AI models, but the overall
execution of those sub-agents is ultimately managed by the ***SequentialAgent***
object you define.

!!! note "Alternative: graph-based workflows"

    Starting in ADK 2.0 for Python and Go, templated workflows have been superseded

    by more flexible workflow structures, including
    [graph-based workflows](<https://google.github.io/adk-docs/graphs/>) and
    [dynamic workflows](<https://google.github.io/adk-docs/graphs/dynamic/>).

### Example scenario

You want to build an agent that can summarize any webpage, using two tools:
**Get Page Contents** and **Summarize Page**. Since the agent must always call
**Get Page Contents** before calling **Summarize Page**, you can build your
agent using the ***SequentialAgent*** class.

### How it works

When the `SequentialAgent`'s `Run Async` method is called, it performs the following actions:

1. **Iteration:** It iterates through the sub agents list in the order they were provided.
2. **Sub-Agent Execution:** For each sub-agent in the list, it calls the sub-agent's `Run Async` method.

![Sequential Agent](<https://google.github.io/adk-docs/assets/sequential-agent.png>){: width="600"}

!!! note "Shared Invocation Context"
    The `SequentialAgent` passes the same `InvocationContext` to each of its
    sub-agents. This means they all share the same session state, including the
    temporary (`temp:`) namespace, making it easy to pass data between steps within
    a single turn.

### Full Example: Code Development Pipeline

Consider a simplified code development pipeline:

* **Code Writer Agent:**  An LLM Agent that generates initial code based on a specification.
* **Code Reviewer Agent:**  An LLM Agent that reviews the generated code for errors, style issues, and adherence to best practices.  It receives the output of the Code Writer Agent.
* **Code Refactorer Agent:** An LLM Agent that takes the reviewed code, and the reviewer's comments, and refactors it to improve quality and address issues.

Using a `SequentialAgent` makes it simple to define this execution flow, as shown
in the following code snippet:

```py
SequentialAgent(sub_agents=[CodeWriterAgent, CodeReviewerAgent, CodeRefactorerAgent])
```

This ensures the code is written, *then* reviewed, and *finally* refactored, in a strict, dependable order. **The output from each sub-agent is passed to the next by storing them in state via [Output Key](<https://google.github.io/adk-docs/agents/llm-agents/##data-handling>)**.

???+ "Code"

    === "Python"
        ```py
        --8<-- "examples/python/snippets/agents/workflow-agents/sequential_agent_code_development_agent.py:init"
        ```

    === "TypeScript"
        ```typescript
        --8<-- "examples/typescript/snippets/agents/workflow-agents/sequential_agent_code_development_agent.ts:init"
        ```

    === "Go"
        ```go
        --8<-- "examples/go/snippets/agents/workflow-agents/sequential/main.go:init"
        ```

    === "Java"
        ```java
        --8<-- "examples/java/snippets/src/main/java/agents/workflow/SequentialAgentExample.java:init"
        ```


</details>
