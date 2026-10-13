# Google ADK 循环工作流 Agent

介绍重复执行子 Agent、迭代次数和停止条件，以文档改进案例说明实现；属于循环编排样例。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/google/adk-docs/commit/c75e61f5f3eb2c47ef56aa7aa52eadc91a59d4d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/agents/workflow-agents/loop-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/agents/workflow-agents/loop-agents.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 循环模板工作流程 agent

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div> 支持

***LoopAgent*** 类是一个 [ 模板工作流程 ](<https://google.github.io/adk-docs/agents/workflow-agents/>) agent
，它在循环中执行其子 agents 指定的迭代次数或 
 直到满足终止条件。当您的工作流程 
 涉及重复或迭代细化（例如修改代码或 
 文档）时，请使用 ***LoopAgent***。与其他模板化工作流程一样，***LoopAgent***
对象的执行不受AI模型控制，并且其执行
其子agents的方式是确定的。定义的循环中的子agents可能会也可能不会使用AI
模型，但这些子agents的整体执行最终由
您定义的***LoopAgent***对象管理。

！！！注意“替代方案：基于图形的工作流程”

从 ADK 2.0 开始，Python 和 Go 的模板化工作流程已被取代

通过更灵活的工作流程结构，包括
 [基于图的工作流程](<https://google.github.io/adk-docs/graphs/>)和
 [动态工作流程](<https://google.github.io/adk-docs/graphs/dynamic/>)。

### 示例场景

您想要构建一个可以生成食物图像的 agent，但有时当 
 您想要生成特定数量的项目（例如香蕉）时，agent
 会在图像中生成不同数量的这些项目，例如 7
 香蕉的图像。您有两个工具：`Generate Image`、`Count Food Items`。如果您的目标 
 是继续生成图像，直到正确生成指定的 
 数量的项目，或者经过一定次数的迭代后，您可以使用 ***LoopAgent*** 工作流程构建您的 
agent。

### 它是如何运作的

当调用`LoopAgent`的`Run Async`方法时，它会执行以下操作：

1. **子Agent执行：**它按顺序_迭代子Agents列表_。对于_每个_子agent，它调用agent的`Run Async`方法。
2. **终止检查：**

_至关重要的是_，`LoopAgent`本身_而不是_固有地决定何时停止循环。您_必须_实现终止机制以防止无限循环。  常见策略包括：

    * **最大迭代次数**：设置`LoopAgent`中的最大迭代次数。 **循环将在多次迭代后终止**。
    * **从子agent**升级：设计一个或多个子agents来评估条件（例如，“文档质量是否足够好？”，“是否已达成共识？”）。  如果满足条件，子agent可以发出终止信号（例如，通过引发自定义事件、在共享上下文中设置标志或返回特定值）。

![循环Agent](<https://google.github.io/adk-docs/assets/loop-agent.png>)

### 完整示例：迭代文档改进

想象一下您想要迭代改进文档的场景：

* **编写者 Agent：** 生成或完善主题草稿的 `LlmAgent`。
* **批评者Agent：**批评草案的`LlmAgent`，确定需要改进的领域。

    ```py
    LoopAgent(sub_agents=[WriterAgent, CriticAgent], max_iterations=5)
    ```

在此设置中，`LoopAgent` 将管理迭代过程。  `CriticAgent` 可以 ** 设计为当文档达到令人满意的质量水平 ** 时返回“STOP”信号，从而防止进一步迭代。或者，`max iterations` 参数可用于将过程限制为固定数量的周期，或者可以实现外部逻辑来做出停止决策。 **循环最多运行五次**，确保迭代细化不会无限期地继续。

???+“完整代码”

    === "Python"
        ```py
        --8<-- "examples/python/snippets/agents/workflow-agents/loop_agent_doc_improv_agent.py:init"
        ```

    === "TypeScript"
        ```typescript
        --8<-- "examples/typescript/snippets/agents/workflow-agents/loop_agent_doc_improv_agent.ts:init"
        ```

    === "Go"
        ```go
        --8<-- "examples/go/snippets/agents/workflow-agents/loop/main.go:init"
        ```

    === "Java"
        ```java
        --8<-- "examples/java/snippets/src/main/java/agents/workflow/LoopAgentExample.java:init"
        ```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Loop template workflow agent

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.2.0</span>
</div>

The ***LoopAgent*** class is a [template workflow](<https://google.github.io/adk-docs/agents/workflow-agents/>) agent
that executes its sub-agents in a loop for a specified number of iterations or
until a termination condition is met. Use the ***LoopAgent*** when your workflow
involves repetition or iterative refinement, such as revising code or a
document. As with other templated workflows, the execution of a ***LoopAgent***
object is not controlled by an AI model, and is deterministic in how it executes
its sub-agents. The sub-agents within the defined loop may or may not utilize AI
models, but the overall execution of those sub-agents is ultimately managed by
the ***LoopAgent*** object you define.

!!! note "Alternative: graph-based workflows"

    Starting in ADK 2.0 for Python and Go, templated workflows have been superseded

    by more flexible workflow structures, including
    [graph-based workflows](<https://google.github.io/adk-docs/graphs/>) and
    [dynamic workflows](<https://google.github.io/adk-docs/graphs/dynamic/>).

### Example scenario

You want to build an agent that can generate images of food, but sometimes when
you want to generate a specific number of items, such as bananas, the agent
generates a different number of those items in the image, such as an image of 7
bananas. You have two tools: `Generate Image`, `Count Food Items`. If your goal
is to keep generating images until it either correctly generates the specified
number of items, or after a certain number of iterations, you can build your
agent using a ***LoopAgent*** workflow.

### How it Works

When the `LoopAgent`'s `Run Async` method is called, it performs the following actions:

1. **Sub-Agent Execution:**  It iterates through the Sub Agents list _in order_. For _each_ sub-agent, it calls the agent's `Run Async` method.
2. **Termination Check:**

    _Crucially_, the `LoopAgent` itself does _not_ inherently decide when to stop looping. You _must_ implement a termination mechanism to prevent infinite loops.  Common strategies include:

    * **Max Iterations**: Set a maximum number of iterations in the `LoopAgent`. **The loop will terminate after that many iterations**.
    * **Escalation from sub-agent**: Design one or more sub-agents to evaluate a condition (e.g., "Is the document quality good enough?", "Has a consensus been reached?").  If the condition is met, the sub-agent can signal termination (e.g., by raising a custom event, setting a flag in a shared context, or returning a specific value).

![Loop Agent](<https://google.github.io/adk-docs/assets/loop-agent.png>)

### Full Example: Iterative Document Improvement

Imagine a scenario where you want to iteratively improve a document:

* **Writer Agent:** An `LlmAgent` that generates or refines a draft on a topic.
* **Critic Agent:** An `LlmAgent` that critiques the draft, identifying areas for improvement.

    ```py
    LoopAgent(sub_agents=[WriterAgent, CriticAgent], max_iterations=5)
    ```

In this setup, the `LoopAgent` would manage the iterative process.  The `CriticAgent` could be **designed to return a "STOP" signal when the document reaches a satisfactory quality level**, preventing further iterations. Alternatively, the `max iterations` parameter could be used to limit the process to a fixed number of cycles, or external logic could be implemented to make stop decisions. The **loop would run at most five times**, ensuring the iterative refinement doesn't continue indefinitely.

???+ "Full Code"

    === "Python"
        ```py
        --8<-- "examples/python/snippets/agents/workflow-agents/loop_agent_doc_improv_agent.py:init"
        ```

    === "TypeScript"
        ```typescript
        --8<-- "examples/typescript/snippets/agents/workflow-agents/loop_agent_doc_improv_agent.ts:init"
        ```

    === "Go"
        ```go
        --8<-- "examples/go/snippets/agents/workflow-agents/loop/main.go:init"
        ```

    === "Java"
        ```java
        --8<-- "examples/java/snippets/src/main/java/agents/workflow/LoopAgentExample.java:init"
        ```


</details>
