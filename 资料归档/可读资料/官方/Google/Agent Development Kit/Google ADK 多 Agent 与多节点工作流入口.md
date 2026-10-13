# Google ADK 多 Agent 与多节点工作流入口

概述复杂应用的工作流组织方式，提供后续编排资料入口；这是概览资料。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/google/adk-docs/commit/c75e61f5f3eb2c47ef56aa7aa52eadc91a59d4d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/workflows/index.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/docs/workflows/index.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 工作流程：多agent、多节点应用

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span>
</div> 支持

随着代理应用程序变得越来越复杂，将它们构建为一个单一的
整体agent可能会成为开发、评估和维护的挑战。
Agent开发套件(ADK)支持通过组合多个来构建复杂的agent应用程序
 agents 和可执行节点进入 *agent
 工作流程 *。随着您的 agent 应用程序变得更加复杂和完善，使用多个元素构建 agents 可以提供许多 
 的好处：

* **可预测性：**使用模板创建更多受控的任务执行流程
基于逻辑或图形的执行机制。
* **可靠性：**确保任务按所需的顺序或模式运行
始终如一。
* **结构：**通过组合agents更易于管理地构建复杂流程
元素，分离任务职责，并限制
给定任务的数据上下文。

可以使用多种结构和架构来构建工作流程，如下图
所示：



![ADK](<https://google.github.io/adk-docs/assets/workflows_overview.svg>) 中的工作流程类型


**图1。** ADK工作流程可以具有灵活的执行路径，或遵循
特定的模板化执行模式。

以下是使用 ADK 为您的 agent 应用程序构建工作流程 
 的多种方法的快速指南：

*   [**基于图形的工作流程：**](<https://google.github.io/adk-docs/graphs/>)（ADK 2.0 及更高版本）
此工作流类型允许您将 AI- 驱动的 agents 和确定性 
 执行节点组成一个灵活的执行图，其中可以包括决策分支。

*   [**动态工作流程：**](<https://google.github.io/adk-docs/graphs/dynamic/>)（ADK 2.0 及更高版本）
此工作流程类型允许您使用完整的编程代码逻辑组成 AI- 驱动的 agents 和确定性 
 执行节点。

*   [**协作工作流程：**](<https://google.github.io/adk-docs/workflows/collaboration/>)（ADK 2.0 及更高版本）
此工作流程类型允许单个 agent 充当动态协调员角色 
，以通过一组指定的子 agents 完成任务。

*   [**模板工作流程：**](<https://google.github.io/adk-docs/agents/workflow-agents/>)
这些预构建的工作流程从***BaseAgent***扩展而来，并提供固定的
执行逻辑结构，包括序列、循环和并行执行。

请点击上面提供的链接，了解有关每种类型的 ADK 工作流程 
 架构的更多信息。

！！！例如“实验：Agent 路由”

Agent 路由是一项实验性功能，允许您在运行时使用路由器功能在 
 多个 agents 之间进行选择，以进行回退、
 A/B 测试和自动路由。有关详细信息，请参阅
 [Agent 路由](<https://google.github.io/adk-docs/agents/routing/>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Workflows: multi-agent, multi-node applications

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v0.1.0</span><span class="lst-typescript">TypeScript v0.2.0</span><span class="lst-go">Go v0.1.0</span><span class="lst-java">Java v0.1.0</span>
</div>

As agentic applications grow in complexity, structuring them as a single,
monolithic agent can become challenging to develop, evaluate, and maintain.
Agent Development Kit (ADK) supports building sophisticated agent applications
by composing multiple agents and executable nodes into *agent
workflows*. Structuring agents using multiple elements can provide a number
of benefits as your agent applications grow more complex and sophisticated:

* **Predictability:** Create more controlled task execution flow using templated
  logic or graph-based execution mechanisms.
* **Reliability:** Ensure tasks run in the required order or pattern
  consistently.
* **Structure:** Build complex processes more manageably by composing agents
  elements, separating task responsibilities, and limiting data contexts for
  given tasks.

Workflows can be built using several structures and architectures, as
illustrated in the following diagram:



![Workflow types in ADK](<https://google.github.io/adk-docs/assets/workflows_overview.svg>)


**Figure 1.** ADK workflows can have flexible execution paths, or follow
specific, templated execution patterns.

The following is a quick guide to the multiple methods for building workflows
for your agent application with ADK:

*   [**Graph-based workflows:**](<https://google.github.io/adk-docs/graphs/>) (ADK 2.0 and higher)
    This workflow type allows you to compose both AI-powered agents and deterministic
    execution nodes into a flexible execution graph that can include decision branching.

*   [**Dynamic workflows:**](<https://google.github.io/adk-docs/graphs/dynamic/>) (ADK 2.0 and higher)
    This workflow type allows you to compose AI-powered agents and deterministic
    execution nodes using full programmatic code logic.

*   [**Collaborative workflows:**](<https://google.github.io/adk-docs/workflows/collaboration/>) (ADK 2.0 and higher)
    This workflow type allows a single agent to act in a dynamic coordinator role
    to accomplish tasks with a set of specified sub-agents.

*   [**Template workflows:**](<https://google.github.io/adk-docs/agents/workflow-agents/>)
    These pre-built workflows are extended from ***BaseAgent*** and provide fixed
    execution logic structures including sequences, loops, and parallel execution.

Follow the links provide above for more information about each type of ADK workflow
architecture.

!!! example "Experimental: Agent Routing"

    Agent Routing is an experimental feature that allows you to select between
    multiple agents at runtime using router functions for fallback,
    A/B testing, and auto-routing. For more information, see
    [Agent Routing](<https://google.github.io/adk-docs/agents/routing/>).


</details>
