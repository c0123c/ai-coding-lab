# Copilot 橡皮鸭评议 Agent

介绍由另一模型对计划、代码和测试提供第二意见的机制，以及手动与自动触发方法，用于交叉检查开发结果。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/github/docs/commit/c3a79a3e7b7397be3922268dc0a2f9719a88787c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/agents/copilot-cli/rubber-duck.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 橡皮鸭评议 Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: About the rubber duck agent
shortTitle: About rubber duck
allowTitleToDifferFromFilename: true
intro: 'The rubber duck agent is a built-in critic that gives {% data variables.product.prodname_copilot_short %} a constructive second opinion on its own plans, code, and tests—using a different AI model from the one driving your session.'
product: '{% data reusables.gated-features.copilot-cli %}'
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

橡皮鸭是 {% data variables.copilot.copilot_cli %} 中的内置 agent，充当建设性评论家。在处理任务时，会话的主要 CLI agent 可以将其当前计划、设计、实施或测试传递给橡皮鸭 agent 进行审查。橡皮鸭 agent 寻找盲点、设计缺陷和实质性问题，并提供具体的、可操作的反馈。 {% data variables.product.prodname_copilot_short %} 然后在继续之前考虑该批评。

橡皮鸭 agent 旨在审查建议的更改，而不是进行文件更改本身。会话的主要 agent 决定如何处理反馈。

## 为什么叫“橡皮鸭”？

该名称源自软件工程中一种长期存在的技术，称为 ** 橡皮鸭 **，在该技术中，您可以向无生命的对象（传统上是橡皮鸭）解释代码或建议的解决方案。这个想法是，通过阐明你的想法，你经常会发现错误、误解或逻辑缺陷。

橡皮鸭agent应用了与{% data variables.product.prodname_copilot_short %}类似的想法。在进行重大更改之前，{% data variables.product.prodname_copilot_short %} 可以停下来，阐明其当前的想法，并由独立审查员对其进行审查。与真正的橡皮鸭不同，这只鸭子会回嘴：它返回一个结构化的批评，{% data variables.product.prodname_copilot_short %} 可以采取行动。

## 来自另一个模型的第二意见

橡皮鸭 agent 的一个关键设计特征是，它故意在 ** 与驾驶您的会话的模型 ** 不同的 AI 模型上运行。 {% data variables.copilot.copilot_cli %} 选择与当前会话模型形成对比的批评者模型。例如，如果您选择在会话中使用 Claude 模型，则橡皮鸭 agent 可能会使用 GPT 模型作为批评者。 {% data variables.copilot.copilot_cli_short %} 仅在有合适的模型可提供有用的批评时才使用橡皮鸭 agent。

使用不同模型的好处是批评者不太可能与产生作品的模型有相同的盲点、偏见或失败模式。您可以有效地获得对同一问题的两种独立观点。

每次调用橡皮鸭 agent 时，都会根据您当前的会话模型自动选择适当的批评家模型。如果您在会话中切换会话模型（例如，使用 `/model` 命令），则下次调用橡皮鸭 agent 将为新会话模型选择适当的批评家。

## 橡皮鸭 agent 的作用

当橡皮鸭agent被咨询时，它：

1. **在上下文中阅读工作。**它理解代码、设计或提案试图完成什么，它如何与系统的其余部分集成，以及存在什么假设。
1. **识别真正的问题。**它查找错误、逻辑错误、安全漏洞、设计缺陷、反模式、性能瓶颈以及其他对任务成功真正重要的问题。
1. **建议具体修复。** 对于发现的每个问题，它都会说明问题、其影响以及具体建议的更改。
1. **按严重性对其反馈**进行分类：
   * **阻塞问题** — 必须修复才能使工作成功。
   * **非阻塞问题**—应该修复以提高质量，但不会妨碍成功。
   * **建议**——优先级较低的改进，但仍对结果产生实际影响。
1. **仅报告重要的发现。**如果没有发现问题，则会明确说明。橡皮鸭 agent 配置为不评论样式、格式、命名约定、注释中的语法、次要重构或不能防止实际问题的最佳实践。

橡皮鸭 agent 可以通过标准探索工具对您的代码库进行只读访问。它无法编辑文件或运行更改环境的命令。

## 手动调用橡皮鸭agent

默认情况下，不会自动调用橡皮鸭 agent。每次你想要批评时，你都需要明确地询问{% data variables.product.prodname_copilot_short %}。例如，在要求 {% data variables.product.prodname_copilot_short %} 制定工作计划后，您可以输入如下提示：

```copilot
Rubber duck your plan.
```

或者在一系列更改的过程中，您可以提示：

```copilot
Get a critique of the changes you've made so far.
```

您还可以使用斜线命令调用橡皮鸭 agent：

```copilot
/rubber-duck What edge cases are missing?
```

## 自动调用橡皮鸭agent

如果您希望{% data variables.product.prodname_copilot_short %}主动咨询橡皮鸭agent而不被询问，您可以从`/subagents`选择器中打开agent的自动调用。

1. 在交互式 {% data variables.copilot.copilot_cli_short %} 会话中，输入 `/subagents`。
1. 使用键盘方向键选择橡皮鸭agent，然后按<kbd>输入</kbd>。
1. 选择**主动调用**，然后按<kbd>输入</kbd>以在关闭和打开之间切换设置。
1. 使用退出键退出 `/subagents` 选择器。

以这种方式打开橡皮鸭 agent 的自动调用会更新您的 {% data variables.product.prodname_copilot_short %} 设置文件（通常为 `~/.copilot/settings.json`），以便您选择的设置在将来的会话中保持不变：

```json
{
  "subagents": {
    "agents": {
      "rubber-duck": {
        "autoInvoke": true
      }
    }
  }
}
```

当启用自动调用时，{% data variables.product.prodname_copilot_short %}会被指示在高杠杆时刻而不是仅在卡住时咨询橡皮鸭agent。典型情况包括：

* **在计划一项重要的更改之后，但在实施之前。**这是发现设计缺陷的最高杠杆时刻，而路线修正仍然很便宜。
* **实施中期，**检查复杂工作中的盲点。
* **编写测试后，**验证测试覆盖范围是否全面，并且行为实际上满足您的原始请求。
* **反应性地，当{% data variables.product.prodname_copilot_short %}遇到重复故障或意外结果时，**对问题进行独立分析，而不是重试相同的方法。

对于小的、易于理解的更改 {% data variables.product.prodname_copilot_short %} 通常会跳过橡皮鸭 agent。

当 {% data variables.product.prodname_copilot_short %} 咨询橡皮鸭 agent 时，它会在时间线输出中为您总结由此产生的批评，而不是逐字重复 - 例如，“批评指出了我围绕 X 的计划中的盲点，因此我更新了计划来解决这个问题。”

## 使用橡皮鸭的好处agent

* **尽早发现问题。**大多数失败的重要任务都存在批评可能在计划阶段发现的问题。在编写代码之前获得反馈比稍后解决问题更好。

  > [！NOTE]
  > 咨询橡皮鸭agent在单独的模型上运行额外的推理过程，因此它增加了一些延迟并涉及额外的模型使用。好处是，预先花费更多的时间和资源可以通过及早发现问题并减少完成任务的失败尝试次数来节省时间和整体模型使用率。

* **减少单模型盲点。**由于agent使用来自不同系列的模型，因此它带来了真正不同的视角，而不是重新运行产生原始作品的相同推理。
* **提高复杂变更的质量。**在{% data variables.product.prodname_copilot_short %}承诺某种方法之前，架构决策、多文件变更和不熟悉的代码库都受益于第二意见。
* ** 不妨碍简单的任务。** {% data variables.product.prodname_copilot_short %} 仅在工作不平凡时才咨询橡皮鸭 agent，因此它不会减慢快速编辑和明显修复的速度。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-custom-agents#built-in-agents>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## Introduction

Rubber duck is a built-in agent in {% data variables.copilot.copilot_cli %} that acts as a constructive critic. While working on a task, the main CLI agent for a session can pass its current plan, design, implementation, or tests over to the rubber duck agent for review. The rubber duck agent looks for blind spots, design flaws, and substantive issues, and reports back with concrete, actionable feedback. {% data variables.product.prodname_copilot_short %} then takes that critique into account before continuing.

The rubber duck agent is designed to review proposed changes, not to make file changes itself. The main agent for the session decides what to do with the feedback.

## Why "rubber duck"?

The name comes from a long-standing technique in software engineering called **rubber ducking** in which you explain your code, or proposed solution, to an inanimate object—traditionally a rubber duck. The idea is that by articulating your thinking, you often uncover mistakes, misunderstandings, or logical flaws.

The rubber duck agent applies a similar idea to {% data variables.product.prodname_copilot_short %}. Before moving ahead with a non-trivial change, {% data variables.product.prodname_copilot_short %} can stop, articulate its current thinking, and have it scrutinized by an independent reviewer. Unlike a real rubber duck, this one talks back: it returns a structured critique that {% data variables.product.prodname_copilot_short %} can act on.

## Second opinion from another model

A key design feature of the rubber duck agent is that it deliberately runs on a **different AI model from the one driving your session**. {% data variables.copilot.copilot_cli %} picks a critic model that contrasts with the current session model. For example, if you have chosen to use a Claude model for your session, the rubber duck agent may use a GPT model as the critic. {% data variables.copilot.copilot_cli_short %} only uses the rubber duck agent if a suitable model is available to provide a useful critique.

The benefit of using a different model is that the critic is less likely to share the same blind spots, biases, or failure modes as the model that produced the work. You effectively get two independent perspectives on the same problem.

The appropriate critic model is selected automatically each time the rubber duck agent is invoked, based on your current session model. If you switch session models mid-session (for example, with the `/model` command), the next invocation of the rubber duck agent picks the appropriate critic for the new session model.

## What the rubber duck agent does

When the rubber duck agent is consulted, it:

1. **Reads the work in context.** It understands what the code, design, or proposal is trying to accomplish, how it integrates with the rest of the system, and what assumptions exist.
1. **Identifies real issues.** It looks for bugs, logic errors, security vulnerabilities, design flaws, anti-patterns, performance bottlenecks, and other problems that genuinely matter to the success of the task.
1. **Recommends specific fixes.** For each issue it finds, it states the issue, its impact, and a concrete suggested change.
1. **Categorizes its feedback** by severity:
   * **Blocking issues**—must be fixed for the work to succeed.
   * **Non-blocking issues**—should be fixed to improve quality, but won't prevent success.
   * **Suggestions**—lower-priority improvements that still have a real impact on the outcome.
1. **Only reports findings that matter.** If it finds no issues, it says so explicitly. The rubber duck agent is configured not to comment on style, formatting, naming conventions, grammar in comments, minor refactors, or best practices that don't prevent actual problems.

The rubber duck agent has read-only access to your codebase via the standard exploration tools. It cannot edit files or run commands that change your environment.

## Manually invoking the rubber duck agent

By default, the rubber duck agent is not automatically invoked. You need to explicitly ask {% data variables.product.prodname_copilot_short %} for a critique each time you want one. For example, after asking {% data variables.product.prodname_copilot_short %} to produce a plan of work, you could enter a prompt such as:

```copilot
Rubber duck your plan.
```

Or part way through a series of changes, you could prompt:

```copilot
Get a critique of the changes you've made so far.
```

You can also invoke the rubber duck agent with a slash command:

```copilot
/rubber-duck What edge cases are missing?
```

## Auto-invoking the rubber duck agent

If you want {% data variables.product.prodname_copilot_short %} to proactively consult the rubber duck agent without being asked, you can turn on auto-invocation for the agent from the `/subagents` picker.

1. In an interactive {% data variables.copilot.copilot_cli_short %} session, enter `/subagents`.
1. Use the keyboard arrow keys to select the rubber duck agent, then press <kbd>Enter</kbd>.
1. Select **Proactive invocation** then press <kbd>Enter</kbd> to toggle the setting between off and on.
1. Use the escape key to exit the `/subagents` picker.

Turning on auto-invocation for the rubber duck agent in this way updates your {% data variables.product.prodname_copilot_short %} settings file (typically `~/.copilot/settings.json`), so that your choice of setting persists for future sessions:

```json
{
  "subagents": {
    "agents": {
      "rubber-duck": {
        "autoInvoke": true
      }
    }
  }
}
```

When automatic invocation is enabled, {% data variables.product.prodname_copilot_short %} is instructed to consult the rubber duck agent at high-leverage moments rather than only when stuck. Typical situations include:

* **After planning a non-trivial change, but before implementing it.** This is the highest-leverage moment to catch design flaws, while course corrections are still cheap.
* **Mid-implementation,** to check for blind spots in a complex piece of work.
* **After writing tests,** to validate that test coverage is comprehensive and that the behavior actually satisfies your original request.
* **Reactively, when {% data variables.product.prodname_copilot_short %} hits repeated failures or unexpected results,** to get an independent analysis of the problem rather than retrying the same approach.

For small, well-understood changes {% data variables.product.prodname_copilot_short %} typically skips the rubber duck agent.

When {% data variables.product.prodname_copilot_short %} consults the rubber duck agent, it summarizes the resulting critique for you in the timeline output rather than repeating it verbatim—for example, "The critique pointed out a blind spot in my plan around X, so I updated my plan to address that."

## Benefits of using the rubber duck agent

* **Catches issues early.** Most non-trivial tasks that fail have problems that a critique could have caught at the planning stage. Getting feedback before code is written is preferable to fixing problems later in the process.

  > [!NOTE]
  > Consulting the rubber duck agent runs an additional reasoning pass on a separate model, so it adds some latency and involves additional model usage. The upside is that spending a little more time and resources up front can save you time and model usage overall by catching issues early and by reducing the number of failed attempts to complete a task.

* **Reduces single-model blind spots.** Because the agent uses a model from a different family, it brings a genuinely different perspective rather than re-running the same reasoning that produced the original work.
* **Improves quality of complex changes.** Architectural decisions, multi-file changes, and unfamiliar codebases all benefit from a second opinion before {% data variables.product.prodname_copilot_short %} commits to an approach.
* **Stays out of the way for simple tasks.** {% data variables.product.prodname_copilot_short %} only consults the rubber duck agent when the work is non-trivial, so it doesn't slow down quick edits and obvious fixes.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-custom-agents#built-in-agents>)


</details>
