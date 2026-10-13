# 推动团队持续使用 Copilot Agent

按启动前准备、两周实践和后续评估组织团队采用，涵盖目标、工作流、控制措施及业务结果指标。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-01](<https://github.com/github/docs/commit/afc156dff3bc78432d4afa66b01689f3dffc96fd>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/enable-developers/drive-team-agentic-adoption.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/推动团队持续使用 Copilot Agent.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Driving team adoption of agentic GitHub Copilot
shortTitle: Drive agentic adoption
allowTitleToDifferFromFilename: true
intro: 'Help established teams build sustained habits with {% data variables.copilot.github_copilot_app %} and {% data variables.copilot.copilot_cli_short %}, then measure adoption and business impact.'
versions:
  feature: copilot
contentType: tutorials
category:
  - Roll Copilot out at scale
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

After developers are using {% data variables.product.prodname_copilot_short %}, a focused rollout can help established teams add {% data variables.copilot.github_copilot_app %} and {% data variables.copilot.copilot_cli_short %} to their daily work. These tools complement IDE chat and agent mode. The goal is to help teams build repeatable agentic workflows with the tool that best fits each task.

本手册适用于协调已建立团队的 {% data variables.product.prodname_copilot_short %} 采用的人员。它会指导您完成为期两周的入职冲刺，以测试工作流程的适合性并建立使用习惯。 A two-week sprint provides an early signal of adoption, not enough evidence to prove long-term productivity or cost impact.

## 冲刺之前

确定部署协调员以及负责政策、许可证、指标、安全、支持和支出的人员。 Agree on who can pause the sprint or disable access if you find an unexpected policy, security, or cost issue.

### 选择并装备初始团队

Start with one team or a small group of established teams. Participants are more likely to learn from each other, and you can distinguish team practices from individual preferences.

选择具有以下特征的团队：

* 经理或团队负责人将模拟使用情况并在团队会议中进行讨论
* 一名或多名经验丰富的个人贡献者，可以测试高级工作流程、支持同行并将成功的实验转化为团队指导
* 适合代理工具的工作，例如调查代码库、跨文件进行协调更改、运行测试或准备拉取请求
* Stable delivery metrics that you can use as a baseline

避免只选择爱好者。 Include a representative mix of experience levels so that the sprint reveals common setup, trust, and workflow barriers.

Select a real project with enough suitable work for participants to use agentic tools several times during the sprint.然后准备访问：

1. Confirm that each participant receives their {% data variables.product.prodname_copilot_short %} license through the organization whose policies will govern the sprint.
1. Work with the appropriate enterprise and organization owners to enable the separate policies for {% data variables.copilot.github_copilot_app %} and {% data variables.copilot.copilot_cli_short %}.
1. 在启用策略之前确认范围。 Organization policies apply to everyone who receives their license through that organization, not only the selected team. Enterprise policies can also restrict which settings organization owners can change.欲了解更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/enterprise/policies>)。
1. Ask participants to install and sign in to both tools before the kickoff.参见[AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>)和[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli>)。
1. Confirm that each participant can access the project and use the tools under the intended policies and managed settings.
1. Give the person measuring the sprint access to {% data variables.product.prodname_copilot_short %} usage metrics.对于所需的角色，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/copilot-usage-metrics/copilot-metrics>)。

如果您是第一次启用这些工具，请将冲刺视为更广泛的功能试点的一部分。在开始之前，估计基于使用的成本，根据预期的停止行为设置适当的预算，并计划在冲刺期间监控支出。完整流程请参见[AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/govern-at-scale/pilot-a-feature-or-model>)。

### 定义激活和结果目标

为入门冲刺设置 ** 激活 ** 目标。例如，为每个参与者设置一个目标，使其在冲刺的每周至少三天内使用 {% data variables.copilot.github_copilot_app_short %} 或 {% data variables.copilot.copilot_cli_short %}。 Repeated use gives participants time to try the tools on different tasks and decide where they fit into daily work.

Pair the activation target with one or two **outcomes** that matter to the team.例如：

* 减少从开始工作到打开拉取请求的时间。
* Complete more maintenance tasks without delaying planned feature work.
* 减少调查不熟悉的代码所花费的时间。
* Maintain or improve review quality while increasing pull request throughput.

Record a baseline for the outcomes before the sprint.

Do not use the amount of generated code as a success target. More generated code does not necessarily create more value and can increase review or maintenance work.

### 准备工作流程、跟踪和保障措施

不要要求参与者“更多地使用 AI”，而是为团队提供两到三个 ** 批准的与其当前工作相匹配的工作流程 **。例如：

* Use {% data variables.copilot.copilot_cli_short %} to investigate a failure, plan a multi-file change, run tests, or review local changes.
* Use {% data variables.copilot.github_copilot_app_short %} to work from an issue, make changes in an agent session, and open a pull request.
* Move between the tools when useful, for example by opening {% data variables.copilot.github_copilot_app_short %} from {% data variables.copilot.copilot_cli_short %} with `copilot app`.

For each workflow, document the required human review, testing, and approval steps.如果您的企业使用自定义指令、agents、托管设置或批准的 Model Context Protocol (MCP) 服务器，请在冲刺之前配置它们。

Decide how the team will share examples and track progress.您可以：

* Add a temporary pull request label, such as `copilot-pilot`, so participants can find and discuss relevant examples.
* Add a consistent marker to relevant commit messages, such as `Copilot-Assisted: true`, if your organization already analyzes commit metadata.
* Prepare a team dashboard that shows the share of participants active on either tool on three or more days per week.
* 选择现有的团队会议或频道，参与者可以在其中共享任务、工作流和结果，而无需共享可能包含敏感信息的提示。

Labels and commit message markers provide self-reported context, not authoritative usage data. Use them to find examples and start conversations. Use {% data variables.product.prodname_copilot_short %} usage metrics to measure activity.

Before kickoff, document:

* Who will review access, policy behavior, agent activity, support issues, and spending during the sprint
* How participants will report unexpected or unsafe behavior
* 暂停或停止冲刺的条件，例如意外的政策范围、重复的不安全结果或支出接近商定的限额
* How the responsible owner will disable the tools or remove sprint-specific configuration

## 冲刺期间

In the first week, remove setup barriers and create shared experiences. In the second week, reinforce successful workflows and address barriers.

### Week one: Launch and complete the first workflows

1. **与整个团队一起启动。**解释您想要改进的结果、激活目标以及参与者必须遵循的护栏。
1. **演示实际工作。** 要求经理、团队领导或经验丰富的贡献者完成团队代码库中选定的工作流程之一。 Show where human review and approval remain required.
1. **一起完成第一个任务。** 将参与者配对，以便每个人都在 {% data variables.copilot.github_copilot_app_short %} 或 {% data variables.copilot.copilot_cli_short %} 中开始会话并完成一项小型、低风险的任务。
1. **在正常工作期间使用这些工具。**要求参与者在激活目标的天数内至少使用一个选定的工作流程。 Avoid creating artificial exercises after the first task.
1. **检查操作。**确认策略和托管设置按预期运行。查看支持和安全报告，并根据商定的限额检查基于使用情况的支出。

### 第二周：强化有用的工作流程

1. **讨论现有会议中的使用情况。**询问当参与者选择另一个{% data variables.product.prodname_copilot_short %}工具或功能时，这些工具有什么帮助，以及是什么阻碍了他们。
1. **响应工作流程障碍。**使用以下操作：
   * **另一个工具更适合该任务**：保留该任务的现有工作流程，并将冲刺重点放在从所选工具中受益的工作上。
   * **参与者不知道如何启动**：为选定的工作流程之一提供经过测试的启动提示。
   * **agent 缺乏上下文或产生较差的结果**：改进存储库说明、任务描述或可用工具，然后再次测试工作流程。
   * **参与者不信任结果**：演示如何查看差异、限制权限、运行测试和丢弃不需要的更改。
   * **该工具不可用或被阻止**：验证许可证分配、策略、安装、身份验证、网络访问和批准的工具配置。
1. **调整工作流程指南。**删除无帮助的工作流程，修复访问或配置问题，并分享成功会话中的实践。
1. **再次检查操作。**审查安全报告、agent活动、政策行为、支持需求和支出。如果冲刺满足停止条件之一，则暂停冲刺。
1. **Review preliminary results.** At the end of week two, compare activation and outcome signals with the baseline.

不要阻止正常的 IDE 使用。目标是帮助团队在减少工作量或扩大可以完成的任务时选择代理工具。

## After the sprint

衡量结果，记录您学到的知识，并决定是否重复、扩展、重新设计、暂停或停止推出。

### Measure sustained usage

Use daily reports for each user from the {% data variables.product.prodname_copilot_short %} usage metrics API:

* `used_cli` 和 `used_copilot_app` 显示一个人在某一天是否使用过每种工具。
* `totals_by_cli` 和 `totals_by_copilot_app` 提供会话、请求和提示计数以进行更深入的分析。

检查激活目标：

1. 将每日行过滤到冲刺日期。
1. Keep rows where `used_cli` or `used_copilot_app` is `true`.
1. 按用户和周对行进行分组。
1. Count the distinct `day` values in each group.

A participant meets a three-day target when the count is at least three in each week.

To view these fields by team, join the user-teams report with the per-user usage report.参见[AUTOTITLE](<https://github.com/copilot/reference/copilot-usage-metrics/team-level-metrics>)。

使用影响仪表板作为整体 agent 采用的定向、长期视图。采用队列使用跟踪 28 天窗口，可以反映本次冲刺中两个工具之外的代理功能的活动。不要使用同期群变更来归因此冲刺的影响。请参阅[AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/view-impact-dashboard>)。

### Measure team outcomes

Compare the sprint period with your baseline, using measures such as:

* 每个开发人员合并拉取请求
* 合并拉取请求的中位时间
* 维护或积压工作的完成率
* 缺陷、返工或审查结果
* Participant confidence and satisfaction from a short survey

Review quantitative results alongside team feedback. Differences in project complexity, staffing, and work type can affect delivery metrics, so do not attribute every change to {% data variables.product.prodname_copilot_short %}.

### Decide how to scale

总结一下：

* The percentage of participants who reached the activation target
* Which workflows participants repeated without prompting
* The most common setup, governance, or trust barriers
* 所选团队成果的变化
* Usage-based costs and any operational work required to support the rollout
* 任何安全问题以及您如何解决这些问题

当初始团队展示出可重复的工作流程以及可接受的质量、安全性、支持需求和成本时，扩展到另一个已建立的团队。如果激活率较低，请识别并解决该团队的特定障碍，例如设置问题或不合适的工作流程。不要更改已成功采用这些工具的团队的部署。

如果您专门为此冲刺启用了工具，请勿使用两周的数据作为长期成本影响的证据。 Continue 进行更广泛的试点，直到您拥有足够的计费周期数据来做出合理的扩展决策。

当您扩展时，请保留相同的激活和结果定义，以便您可以比较推出组。在有足够的内部证据来定义对企业的持续、有价值的用途后，重新审视目标。


## 原文对照

<details>
<summary>展开完整原文</summary>

After developers are using {% data variables.product.prodname_copilot_short %}, a focused rollout can help established teams add {% data variables.copilot.github_copilot_app %} and {% data variables.copilot.copilot_cli_short %} to their daily work. These tools complement IDE chat and agent mode. The goal is to help teams build repeatable agentic workflows with the tool that best fits each task.

This playbook is for people who coordinate {% data variables.product.prodname_copilot_short %} adoption for established teams. It guides you through a two-week onboarding sprint to test workflow fit and establish usage habits. A two-week sprint provides an early signal of adoption, not enough evidence to prove long-term productivity or cost impact.

## Before the sprint

Identify a rollout coordinator and the people responsible for policies, licenses, metrics, security, support, and spending. Agree on who can pause the sprint or disable access if you find an unexpected policy, security, or cost issue.

### Select and equip an initial team

Start with one team or a small group of established teams. Participants are more likely to learn from each other, and you can distinguish team practices from individual preferences.

Choose teams that have:

* A manager or team lead who will model usage and discuss it in team meetings
* One or more experienced individual contributors who can test advanced workflows, support peers, and turn successful experiments into team guidance
* Work that suits agentic tools, such as investigating a codebase, making coordinated changes across files, running tests, or preparing pull requests
* Stable delivery metrics that you can use as a baseline

Avoid selecting only enthusiasts. Include a representative mix of experience levels so that the sprint reveals common setup, trust, and workflow barriers.

Select a real project with enough suitable work for participants to use agentic tools several times during the sprint. Then prepare access:

1. Confirm that each participant receives their {% data variables.product.prodname_copilot_short %} license through the organization whose policies will govern the sprint.
1. Work with the appropriate enterprise and organization owners to enable the separate policies for {% data variables.copilot.github_copilot_app %} and {% data variables.copilot.copilot_cli_short %}.
1. Confirm the scope before enabling a policy. Organization policies apply to everyone who receives their license through that organization, not only the selected team. Enterprise policies can also restrict which settings organization owners can change. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/enterprise/policies>).
1. Ask participants to install and sign in to both tools before the kickoff. See [AUTOTITLE](<https://github.com/copilot/get-started/quickstart-copilot-app>) and [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-cli/set-up-copilot-cli>).
1. Confirm that each participant can access the project and use the tools under the intended policies and managed settings.
1. Give the person measuring the sprint access to {% data variables.product.prodname_copilot_short %} usage metrics. For the required role, see [AUTOTITLE](<https://github.com/copilot/concepts/copilot-usage-metrics/copilot-metrics>).

If you are enabling these tools for the first time, treat the sprint as part of a broader feature pilot. Before kickoff, estimate usage-based costs, set an appropriate budget with the intended stop behavior, and plan to monitor spending during the sprint. For a complete process, see [AUTOTITLE](<https://github.com/copilot/tutorials/roll-out-at-scale/govern-at-scale/pilot-a-feature-or-model>).

### Define activation and outcome targets

Set an **activation** target for the onboarding sprint. For example, set a target for each participant to use either {% data variables.copilot.github_copilot_app_short %} or {% data variables.copilot.copilot_cli_short %} on at least three separate days in each week of the sprint. Repeated use gives participants time to try the tools on different tasks and decide where they fit into daily work.

Pair the activation target with one or two **outcomes** that matter to the team. For example:

* Reduce the time from starting work to opening a pull request.
* Complete more maintenance tasks without delaying planned feature work.
* Reduce time spent investigating unfamiliar code.
* Maintain or improve review quality while increasing pull request throughput.

Record a baseline for the outcomes before the sprint.

Do not use the amount of generated code as a success target. More generated code does not necessarily create more value and can increase review or maintenance work.

### Prepare workflows, tracking, and safeguards

Instead of asking participants to "use AI more," give the team two or three **approved workflows** that match their current work. For example:

* Use {% data variables.copilot.copilot_cli_short %} to investigate a failure, plan a multi-file change, run tests, or review local changes.
* Use {% data variables.copilot.github_copilot_app_short %} to work from an issue, make changes in an agent session, and open a pull request.
* Move between the tools when useful, for example by opening {% data variables.copilot.github_copilot_app_short %} from {% data variables.copilot.copilot_cli_short %} with `copilot app`.

For each workflow, document the required human review, testing, and approval steps. If your enterprise uses custom instructions, agents, managed settings, or approved Model Context Protocol (MCP) servers, configure them before the sprint.

Decide how the team will share examples and track progress. You can:

* Add a temporary pull request label, such as `copilot-pilot`, so participants can find and discuss relevant examples.
* Add a consistent marker to relevant commit messages, such as `Copilot-Assisted: true`, if your organization already analyzes commit metadata.
* Prepare a team dashboard that shows the share of participants active on either tool on three or more days per week.
* Choose an existing team meeting or channel where participants can share the task, workflow, and result without sharing prompts that may contain sensitive information.

Labels and commit message markers provide self-reported context, not authoritative usage data. Use them to find examples and start conversations. Use {% data variables.product.prodname_copilot_short %} usage metrics to measure activity.

Before kickoff, document:

* Who will review access, policy behavior, agent activity, support issues, and spending during the sprint
* How participants will report unexpected or unsafe behavior
* Conditions that will pause or stop the sprint, such as an unintended policy scope, repeated unsafe results, or spending approaching the agreed limit
* How the responsible owner will disable the tools or remove sprint-specific configuration

## During the sprint

In the first week, remove setup barriers and create shared experiences. In the second week, reinforce successful workflows and address barriers.

### Week one: Launch and complete the first workflows

1. **Launch with the whole team.** Explain the outcomes you want to improve, the activation target, and the guardrails participants must follow.
1. **Demonstrate real work.** Ask the manager, team lead, or experienced contributor to complete one of the selected workflows in the team's codebase. Show where human review and approval remain required.
1. **Complete a first task together.** Pair participants so that everyone starts a session in {% data variables.copilot.github_copilot_app_short %} or {% data variables.copilot.copilot_cli_short %} and completes a small, low-risk task.
1. **Use the tools during normal work.** Ask participants to use at least one selected workflow on the number of days in your activation target. Avoid creating artificial exercises after the first task.
1. **Check operations.** Confirm that policies and managed settings behave as intended. Review support and safety reports, and check usage-based spending against the agreed limit.

### Week two: Reinforce useful workflows

1. **Discuss usage in existing meetings.** Ask what the tools helped with, when participants chose another {% data variables.product.prodname_copilot_short %} tool or feature, and what blocked them.
1. **Respond to workflow barriers.** Use the following actions:
   * **Another tool fit the task better**: Keep the existing workflow for that task and focus the sprint on work that benefits from the selected tools.
   * **The participant did not know how to start**: Provide a tested starter prompt for one of the selected workflows.
   * **The agent lacked context or produced poor results**: Improve repository instructions, task descriptions, or available tools, then test the workflow again.
   * **The participant did not trust the result**: Demonstrate how to review diffs, restrict permissions, run tests, and discard unwanted changes.
   * **The tool was unavailable or blocked**: Verify license assignment, policies, installation, authentication, network access, and approved tool configuration.
1. **Adjust the workflow guidance.** Remove workflows that did not help, fix access or configuration problems, and share practices from successful sessions.
1. **Check operations again.** Review safety reports, agent activity, policy behavior, support needs, and spending. Pause the sprint if it meets one of your stop conditions.
1. **Review preliminary results.** At the end of week two, compare activation and outcome signals with the baseline.

Do not discourage normal IDE use. The goal is to help the team choose an agentic tool when it reduces effort or expands what they can accomplish.

## After the sprint

Measure the results, document what you learned, and decide whether to repeat, expand, redesign, pause, or stop the rollout.

### Measure sustained usage

Use daily reports for each user from the {% data variables.product.prodname_copilot_short %} usage metrics API:

* `used_cli` and `used_copilot_app` show whether a person used each tool on a given day.
* `totals_by_cli` and `totals_by_copilot_app` provide session, request, and prompt counts for deeper analysis.

To check the activation target:

1. Filter the daily rows to the sprint dates.
1. Keep rows where `used_cli` or `used_copilot_app` is `true`.
1. Group the rows by user and week.
1. Count the distinct `day` values in each group.

A participant meets a three-day target when the count is at least three in each week.

To view these fields by team, join the user-teams report with the per-user usage report. See [AUTOTITLE](<https://github.com/copilot/reference/copilot-usage-metrics/team-level-metrics>).

Use the impact dashboard as a directional, longer-term view of overall agent adoption. Adoption cohorts use a trailing 28-day window and can reflect activity from agentic features beyond the two tools in this sprint. Do not use a cohort change to attribute impact to this sprint. See [AUTOTITLE](<https://github.com/copilot/how-tos/administer-copilot/view-impact-dashboard>).

### Measure team outcomes

Compare the sprint period with your baseline, using measures such as:

* Pull requests merged per developer
* Median time to merge pull requests
* Completion rate for maintenance or backlog work
* Defects, rework, or review findings
* Participant confidence and satisfaction from a short survey

Review quantitative results alongside team feedback. Differences in project complexity, staffing, and work type can affect delivery metrics, so do not attribute every change to {% data variables.product.prodname_copilot_short %}.

### Decide how to scale

Summarize:

* The percentage of participants who reached the activation target
* Which workflows participants repeated without prompting
* The most common setup, governance, or trust barriers
* Changes in the selected team outcomes
* Usage-based costs and any operational work required to support the rollout
* Any safety issues and how you resolved them

Expand to another established team when the initial team demonstrates repeatable workflows and acceptable quality, safety, support needs, and cost. If activation is low, identify and address that team's specific barriers, such as setup problems or unsuitable workflows. Do not change the rollout for teams that have already adopted the tools successfully.

If you enabled the tools specifically for this sprint, do not use two weeks of data as proof of long-term cost impact. Continue the broader pilot until you have enough billing-cycle data to make a defensible expansion decision.

As you expand, keep the same activation and outcome definitions so that you can compare rollout groups. Revisit the targets after you have enough internal evidence to define sustained, valuable use for your enterprise.


</details>
