# 用 Copilot CLI 的 /fleet 并行执行任务

介绍拆分复杂请求并调度子任务的方法、适用场景、注意事项以及与自动驾驶模式的关系。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/github/docs/commit/c3a79a3e7b7397be3922268dc0a2f9719a88787c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/copilot-cli/fleet.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Copilot CLI 的 fleet 并行执行任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Running tasks in parallel with the `/fleet` command
shortTitle: Parallel task execution
allowTitleToDifferFromFilename: true
intro: 'The `/fleet` slash command lets {% data variables.copilot.copilot_cli_short %} break down a complex request into smaller tasks and run them in parallel, maximizing efficiency and throughput.'
versions:
  feature: copilot
contentType: concepts
category:
  - Learn about Copilot # Copilot discovery page
  - Learn about Copilot CLI # Copilot CLI bespoke page
docsTeamMetrics:
  - copilot-cli
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 简介

{% data variables.copilot.copilot_cli_short %} 中的 `/fleet` 斜线命令旨在获取实施计划并将其分解为可由子代理并行执行的较小的独立任务。这样可以更快地完成涉及多个步骤的复杂请求。

本文对 `/fleet` 斜杠命令进行概念性概述。

您还可以使用 `--fleet` 选项直接从命令行启动舰队模式。这是与 `/fleet` 斜杠命令等效的命令行。

有关使用 `/fleet` 斜线命令和 `--fleet` 命令行选项的信息，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/speed-up-task-completion>)。

## `/fleet` 如何运作

当您使用`/fleet`命令时，主{% data variables.product.prodname_copilot_short %}agent会分析提示并确定是否可以将其分为更小的子任务。它将根据子任务的性质及其依赖性来评估子代理是否可以有效地执行这些任务。如果它决定将部分或全部子任务分配给子代理，它将充当协调器，管理子任务之间的工作流程和依赖关系。在可能的情况下，编排器 agent 将并行运行子代理，从而更快地完成整个任务。

## 使用`/fleet`的好处

* **任务完成速度**：使用`/fleet`命令的主要好处是通过并行运行子任务可以更快地完成大型、多部分任务。大型任务的各个部分是否可以并行工作将取决于子任务之间的依赖关系。某些任务（例如为新功能创建一套测试）非常适合并行化，并且当您使用 `/fleet` 斜杠命令时通常会更快地完成。

* **专业化**：如果您已定义专门用于某些类型工作的自定义agents，则子代理可能会使用这些。这允许专业化，子代理使用最适合他们正在处理的特定子任务的自定义 agents。

默认情况下，子代理使用低成本 AI 模型。但是，您可以告诉{% data variables.product.prodname_copilot_short %}在部分工作中使用特定模型。例如，在较大的提示中，您可以指定 `... Use GPT-5.3-Codex, to create ... Use Claude Opus 5, to analyze ...`。如果子代理使用指定特定 AI 模型的自定义 agent 配置文件，则子代理将使用该模型。使用特定模型可以为特定类型的子任务产生更好质量的结果。

如果自定义agents可用，{% data variables.product.prodname_copilot_short %}将决定是否使用它来完成特定的子任务。但是，如果您知道特定的自定义 agent 非常适合特定的子任务，则可以使用 `@CUSTOM-AGENT-NAME` 在提示中指定这一点。例如，在较大的提示中：`... Use @test-writer to create comprehensive unit tests for ...`。

欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>)。

* **上下文窗口**：每个子代理都有自己的上下文窗口，与主agent和其他子代理分开。这使得每个子代理能够专注于其特定任务，而不会被更大任务的完整上下文淹没。

## 什么时候应该使用`/fleet`？

* **大型或复杂任务**：当您的请求涉及多个独立步骤时，例如重构多个文件、更新依赖项或跨模块运行测试。
* **可并行工作**：如果您的任务可以拆分为互不依赖的子任务。
* **自动化工作流程**：当您希望尽可能快地完成大型任务时 - 例如，当您使用自动驾驶模式来允许 {% data variables.product.prodname_copilot_short %} 自主工作时。

## 需要考虑的要点

* **{% data variables.product.prodname_ai_credits %}用法**：当您在CLI中提交提示并且{% data variables.product.prodname_copilot_short %}与选定的大语言模型（LLM）交互以生成响应时，这会消耗{% data variables.product.prodname_ai_credits %}。与 LLM 的交互越多，{% data variables.product.prodname_ai_credits %} 的消耗就越高。

每个子代理都可以独立于主 agent 与 LLM 交互，因此将工作拆分为由子代理运行的较小任务可能会导致比主 agent 处理工作更多的 LLM 交互。因此，在提示中使用 `/fleet` 可能会导致消耗更多的 {% data variables.product.prodname_ai_credits %}。

* **任务组合**：如果工作可以分解为独立的子任务，那么它最适合由多个子代理执行。如果您的请求本质上是顺序的，则使用 `/fleet` 斜线命令模式可能不会提供任何好处。

## 【`/fleet`】与自动驾驶模式的关系

`/fleet` 斜杠命令通常在自动驾驶模式下使用，但这些是可以独立使用的独特功能：

* **自动驾驶模式**允许{% data variables.product.prodname_copilot_short %}继续自主工作，直到任务完成，自动响应原本需要用户干预的请求。
* **`/fleet`** 是关于使用子代理并行执行任务，而主 agent 管理整个工作流程。您可以在独立于自动驾驶仪模式的交互式会话中使用 `/fleet` 斜杠命令。

在自动驾驶模式下使用 `/fleet` 的典型工作流程可能如下所示：

1. 按<kbd>Shift</kbd>+<kbd>Tab</kbd>切换到计划模式并与{% data variables.copilot.copilot_cli_short %}一起创建实施计划。
1. 认识到已完成的计划包含多个元素，并且看起来是 `/fleet` 的良好候选。
1. 选择 ** 接受计划并在 Autopilot 执行模式下继续 + 计划完成时显示的 /fleet** 选项。

有关自动驾驶模式的更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/autopilot>)。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/speed-up-task-completion>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

The `/fleet` slash command in {% data variables.copilot.copilot_cli_short %} is designed to take an implementation plan and break it down into smaller, independent tasks that can be executed in parallel by subagents. This allows for faster completion of complex requests that involve multiple steps.

This article gives a conceptual overview of the `/fleet` slash command.

You can also start fleet mode directly from the command line by using the `--fleet` option. This is the command-line equivalent of the `/fleet` slash command.

For information on using the `/fleet` slash command, and the `--fleet` command line option, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/speed-up-task-completion>).

## How `/fleet` works

When you use the `/fleet` command, the main {% data variables.product.prodname_copilot_short %} agent analyzes the prompt and determines whether it can be divided into smaller subtasks. It will assess, based on the nature of the subtasks and their dependencies, whether these can be efficiently executed by subagents. If it decides to assign some or all of the subtasks to subagents, it will act as orchestrator, managing the workflow and dependencies between the subtasks. Where possible, the orchestrator agent will run the subagents in parallel, allowing the whole task to be completed more quickly.

## Benefits of using `/fleet`

* **Speed of task completion**: The main benefit of using the `/fleet` command is that a large, multi-part task can be completed more quickly by running subtasks in parallel. Whether parts of a large task can be worked on in parallel will be determined by the dependencies between the subtasks. Some tasks, such as creating a suite of tests for a new feature, are well suited to parallelization and will typically complete faster when you use the `/fleet` slash command.

* **Specialization**: If you've defined custom agents that are specialized for certain types of work, these may be used by the subagents. This allows for specialization, with the subagents using the custom agents best suited to the specific subtask they are working on.

  By default, subagents use a low-cost AI model. However, you can tell {% data variables.product.prodname_copilot_short %} to use a specific model for part of the work. For example, within a larger prompt, you could specify `... Use GPT-5.3-Codex, to create ... Use Claude Opus 5, to analyze ...`. If a subagent uses a custom agent profile that specifies a particular AI model, then that model will be used by the subagent. Using a specific model may produce better quality results for particular types of subtask.

  If custom agents are available, {% data variables.product.prodname_copilot_short %} will decide whether to use one to complete a particular subtask. However, if you know that a specific custom agent is well-suited to a particular subtask, you can specify this in your prompt by using `@CUSTOM-AGENT-NAME`. For example, within a larger prompt: `... Use @test-writer to create comprehensive unit tests for ...`.

  For more information, see [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/customize-copilot/create-custom-agents-for-cli>).

* **Context window**: Each subagent has its own context window, separate from the main agent and other subagents. This allows each subagent to focus on its specific task without being overwhelmed by the full context of the larger task.

## When should you use `/fleet`?

* **Large or complex tasks**: When your request involves multiple independent steps, such as refactoring several files, updating dependencies, or running tests across modules.
* **Parallelizable work**: If your task can be split into subtasks that don’t depend on each other.
* **Automated workflows**: When you want the quickest possible completion of a large task—for example, when you're using autopilot mode to allow {% data variables.product.prodname_copilot_short %} to work autonomously.

## Points to consider

* **{% data variables.product.prodname_ai_credits %} usage**: When you submit a prompt in the CLI and {% data variables.product.prodname_copilot_short %} interacts with the selected large language model (LLM) to generate a response, this consumes {% data variables.product.prodname_ai_credits %}. More interactions with the LLM result in a higher consumption of {% data variables.product.prodname_ai_credits %}.

  Each subagent can interact with the LLM independently of the main agent, so splitting work up into smaller tasks that are run by subagents may result in more LLM interactions than if the work was handled by the main agent. Using `/fleet` in a prompt may therefore cause more {% data variables.product.prodname_ai_credits %} to be consumed.

* **Task composition**: Work is best suited to execution by multiple subagents if it can be decomposed into independent subtasks. If your request is inherently sequential, using the `/fleet` slash command mode may not provide any benefit.

## Relationship between `/fleet` and autopilot mode

The `/fleet` slash command is often used in autopilot mode, but these are distinct features that can be used independently:

* **Autopilot mode** allows {% data variables.product.prodname_copilot_short %} to continue working autonomously until a task is complete, auto-responding to requests that would otherwise require user intervention.
* **`/fleet`** is all about using subagents to execute tasks in parallel, while the main agent manages the overall workflow. You can use the `/fleet` slash command in interactive sessions independently of autopilot mode.

A typical workflow for using `/fleet` in autopilot mode might look like this:

1. Press <kbd>Shift</kbd>+<kbd>Tab</kbd> to switch into plan mode and work with {% data variables.copilot.copilot_cli_short %} to create an implementation plan.
1. Recognize that the completed plan contains multiple elements and looks like a good candidate for `/fleet`.
1. Select the **Accept plan and continue in Autopilot execution mode + /fleet** option that's displayed when the plan is complete.

For more information about autopilot mode, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/autopilot>).

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/speed-up-task-completion>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/use-copilot-cli/overview>)


</details>
