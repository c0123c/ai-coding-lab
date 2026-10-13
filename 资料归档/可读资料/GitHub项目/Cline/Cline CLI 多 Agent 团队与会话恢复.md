# Cline CLI 多 Agent 团队与会话恢复

CLI 使用指南。说明以 /team 建立协调者和成员、共享任务板、查看团队状态以及按会话恢复工作；正文限定 SDK、CLI 和 Kanban，暂不适用于 VS Code 或 JetBrains 扩展。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/cline/cline/commit/db0be4c3fa8c19c201878e5dc4b3f4d74ffebdaf>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：存档正文明确限定 SDK、CLI、Kanban 界面。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/agent-teams.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline CLI 多 Agent 团队与会话恢复.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Agent Teams"
sidebarTitle: "Agent Teams"
description: "Coordinate multiple agents working together on complex tasks from the CLI."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

**注意：**

此功能目前仅适用于 Cline、SDK、CLI 和看板。此功能目前不适用于 VSCode 和 JetBrains 扩展。

Agent 团队让您可以跨多个 agents 打破复杂的工作，并通过共享任务板进行协调。其中一个 agent 充当协调员，将子任务委派给专家 agents。

## 组建团队

在交互式会话或命令行中使用 `/team` 启动提示：

```bash
cline "/team Plan and implement user authentication with tests"
```

协调员获得工具来添加队友、向他们分配任务并收集他们的结果。如果没有`/team`，Cline在确定任务需要团队时仍然可以组建团队。

## 恢复团队工作

该团队属于会话。要从中断处继续，请使用 `cline history` 找到会话的 ID，然后恢复会话：

```bash
cline --id <session-id>
```

会议与其团队一起开始，您可以告诉协调员下一步该做什么。

## 团队状态

每个团队都有一个任务板、agents之间的消息邮箱和任务日志。 Cline 将它们保留在 `~/.cline/data/db/teams.db` 中。

## 关闭团队

团队可以在“行动”和“计划”模式下使用，但不能在 `--yolo` 下使用。要关闭它们，请运行`cline`，输入`/settings`，然后关闭**工具**选项卡上的`teams`工具。关闭`spawn_agent`也可以停止[子agents](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_Cline 子 Agent 的任务、上下文与审批.md>)。

## 子Agents

对于单个会话中更简单的委派（无持久状态），请使用 [sub-agents](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_Cline 子 Agent 的任务、上下文与审批.md>)。子agents并行运行并将重点报告返回给主agent。

有关程序化 API，请参阅 [SDK 多 Agent 团队指南 ](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_用 Cline SDK 配置和恢复多 Agent 团队.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

**注意：**

This feature currently only applies to Cline SDK, CLI, and Kanban. This feature is not applicable on VSCode and JetBrains Extension for now.

Agent teams let you break complex work across multiple agents that coordinate through a shared task board. One agent acts as the coordinator, delegating subtasks to specialist agents.

## Starting a Team

Start your prompt with `/team`, in an interactive session or on the command line:

```bash
cline "/team Plan and implement user authentication with tests"
```

The coordinator gets tools to add teammates, hand them tasks and collect their results. Without `/team`, Cline can still form a team when it decides a task needs one.

## Resuming Team Work

The team belongs to the session. To pick up where it left off, find the session's ID with `cline history`, then resume the session:

```bash
cline --id <session-id>
```

The session opens with its team, and you can tell the coordinator what to do next.

## Team State

Each team has a task board, a mailbox for messages between agents, and a mission log. Cline keeps them in `~/.cline/data/db/teams.db`.

## Turning Teams Off

Teams are available in Act and Plan modes, and not with `--yolo`. To turn them off, run `cline`, type `/settings`, and turn off the `teams` tool on the **Tools** tab. Turn off `spawn_agent` there to stop [sub-agents](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_Cline 子 Agent 的任务、上下文与审批.md>) too.

## Sub-Agents

For simpler delegation within a single session (no persistent state), use [sub-agents](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_Cline 子 Agent 的任务、上下文与审批.md>). Sub-agents run in parallel and return focused reports to the main agent.

See the [SDK Multi-Agent Teams guide](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/cli/Cline_用 Cline SDK 配置和恢复多 Agent 团队.md>) for the programmatic API.


</details>
