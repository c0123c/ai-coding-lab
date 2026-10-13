# Google ADK 模板工作流入口

简要介绍控制子 Agent 执行顺序的工作流模板，主要作为相关说明的导航入口。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/google/adk-docs/commit/c75e61f5f3eb2c47ef56aa7aa52eadc91a59d4d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/agents/workflow-agents/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/agents/workflow-agents/Google ADK 模板工作流入口.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 模板 agent 工作流程

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span>
</div> 支持

本节介绍*模板工作流程*，也称为*工作流程agents*、
，它们是专门控制一个或多个的执行流程的agents更多
子agents。模板工作流程agents是专门为
编排子agents的执行流程而设计的组件。它们的主要作用是管理
其他agents如何以及何时运行，定义进程的控制流。

！！！注意“替代方案：基于图形的工作流程”

从 ADK 2.0 开始，Python 和 Go 的模板工作流程已被取代

通过更灵活的工作流程结构，包括
 [基于图形的工作流程](<https://google.github.io/adk-docs/graphs/>)和
 [动态工作流程](<https://google.github.io/adk-docs/graphs/dynamic/>)。
这些工作流程架构提供了更多控制，灵活性 
 以及随着时间的推移改进您的 agent 工作流程的能力。



![ADK](<https://google.github.io/adk-docs/assets/template_workflows.svg>) 中的模板 agent 工作流程


**图1。**ADK中模板工作流的执行模式

模板工作流程 agents 根据预定义逻辑进行操作。他们根据其类型（例如顺序、并行或
循环）确定
执行顺序，而无需咨询AI模型来协助编排。这种
方法会产生确定性和可预测的执行模式。模板
工作流包括以下任务执行结构，每个结构都实现
不同的任务完成模式：

<div class="grid cards" markdown>

- :material-console-line: **顺序 Agent 工作流程**

    ---

按顺序依次执行子agents。

[：octicons-箭头-右-24：了解更多](<Google ADK 顺序工作流 Agent.md>)

- :material-console-line: **循环 Agent 工作流程**

    ---

重复执行其子agents，直到满足特定的终止条件。

[：octicons-箭头-右-24：了解更多](<Google ADK 循环工作流 Agent.md>)

- :material-console-line: **并行 Agent 工作流程**

    ---

并行执行多个子agents。

[：octicons-箭头-右-24：了解更多](<Google ADK 并行工作流 Agent.md>)

</div>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Template agent workflows

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span>
</div>

This section introduces *template workflows*, also known as *workflow agents*,
which are specialized agents that control the execution flow of one or more
sub-agents. Template workflow agents are specialized components designed for
orchestrating the execution flow of sub-agents. Their primary role is to manage
how and when other agents run, defining the control flow of a process.

!!! note "Alternative: graph-based workflows"

    Starting in ADK 2.0 for Python and Go, template workflows have been superseded

    by more flexible workflow structures, including
    [graph-based workflows](<https://google.github.io/adk-docs/graphs/>) and
    [dynamic workflows](<https://google.github.io/adk-docs/graphs/dynamic/>).
    These workflow architectures provide more control, flexibility
    and capability to evolve your agent workflows over time.



![Template agent workflows in ADK](<https://google.github.io/adk-docs/assets/template_workflows.svg>)


**Figure 1.** Execution patterns of template workflows in ADK

Template workflow agents operate based on predefined logic. They determine the
execution sequence according to their type, such as sequential, parallel, or
loop, without consulting an AI model for assistance with the orchestration. This
approach results in deterministic and predictable execution patterns. Template
workflows include the following task execution structures, which each implement
a distinct task completion pattern:

<div class="grid cards" markdown>

- :material-console-line: **Sequential Agent workflow**

    ---

    Executes sub-agents one after another, in sequence.

    [:octicons-arrow-right-24: Learn more](<Google ADK 顺序工作流 Agent.md>)

- :material-console-line: **Loop Agent workflow**

    ---

    Repeatedly executes its sub-agents until a specific termination condition is met.

    [:octicons-arrow-right-24: Learn more](<Google ADK 循环工作流 Agent.md>)

- :material-console-line: **Parallel Agent workflow**

    ---

    Executes multiple sub-agents in parallel.

    [:octicons-arrow-right-24: Learn more](<Google ADK 并行工作流 Agent.md>)

</div>


</details>
