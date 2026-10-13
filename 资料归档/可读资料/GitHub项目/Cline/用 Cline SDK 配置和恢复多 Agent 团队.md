# 用 Cline SDK 配置和恢复多 Agent 团队

SDK 指南。介绍团队配置、协调者与成员工具、任务板和邮箱、团队持久化以及 CLI 恢复，比较团队和简单子任务的成本与适用情况。

- 来源：Cline
- 资料类型：SDK 指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/cline/cline/commit/db0be4c3fa8c19c201878e5dc4b3f4d74ffebdaf>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：团队增加执行开销，需要按任务规模判断。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/guides/multi-agent-teams.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/用 Cline SDK 配置和恢复多 Agent 团队.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Multi-Agent Teams"
sidebarTitle: "Multi-Agent Teams"
description: "Coordinate multiple agents working together on complex tasks with delegation, shared context, and result merging."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

多 agent 团队让您可以跨多个 agents 打破复杂的工作，并通过共享任务板进行协调。其中一个 agent 充当协调员，将子任务委派给专家 agents 并合并他们的结果。

## 赋能团队

团队是一项 ClineCore 功能。在会话配置中启用它们：

```typescript
import { ClineCore } from "@cline/sdk"

const cline = await ClineCore.create({ clientName: "team-app" })

const session = await cline.start({
  prompt: "Plan and implement a user authentication module with tests",
  config: {
    providerId: "anthropic",
    modelId: "claude-sonnet-4-6",
    apiKey: process.env.ANTHROPIC_API_KEY,
    systemPrompt: "You are a coordinator for a multi-agent coding team.",
    cwd: "/path/to/project",
    workspaceRoot: "/path/to/project",
    enableTools: true,
    enableSpawnAgent: true,
    enableAgentTeams: true,
    teamName: "auth-sprint",
  },
})
```

## 团队如何运作

启用团队后，协调员 agent 会获得以下工具：

|目的|工具|
|---------|-------|
|队友| `team_spawn_teammate`, `team_shutdown_teammate`, `team_status` |
|任务板|`team_task`（创建、列出、声明、完成和阻止任务）|
|运行任务| `team_run_task`, `team_list_runs`, `team_await_runs`, `team_cancel_run` |
|留言| `team_send_message`, `team_broadcast`, `team_read_mailbox`, `team_mission_log` |
|综合结果| `team_create_outcome`, `team_attach_outcome_fragment`, `team_review_outcome_fragment`, `team_finalize_outcome`, `team_list_outcomes` |
|清理| `team_cleanup` |

除`team_spawn_teammate`外，队友获得相同的工具，并且只有协调员可以关闭队友或清理团队。协调员决定如何拆分工作、创建哪些 agents 以及如何组合结果。

## 团队坚持

团队状态属于会话。 Cline将每个团队的任务板、邮箱、任务日志、结果和队友保存在会话的ID下的`~/.cline/data/db/teams.db`中。当会议恢复时，其团队和队友就会回来。

## 通过CLI

使用 `/team` 启动提示以启动团队，并使用 `cline --id <session-id>` 恢复会话以继续其工作。 [Agent 团队](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/guides/Cline_Cline CLI 多 Agent 团队与会话恢复.md>) 有详细信息。

```bash
cline "/team Plan and implement user auth with tests"
```

## 子Agents vs 团队

SDK 提供两个级别的多 agent 协调：

|特点|子Agents|团队|
|---------|-----------|-------|
|启用与| `enableSpawnAgent: true` | `enableAgentTeams: true` |
|坚持|仅在会话内|会话恢复时恢复|
|协调|亲子|点对点任务板|
|共享状态|无|任务板、邮箱、任务日志|
|最适合|一次性委托|随着时间的推移您恢复的复杂项目|

子agents重量较轻。父级 agent 生成一个子级，等待其结果，然后继续。没有持久状态，没有任务板。

```typescript
// Sub-agents: simple delegation within a single session
const session = await cline.start({
  config: {
    enableSpawnAgent: true,  // Agent can spawn sub-agents
    // ...
  },
  // ...
})
```

团队是为了你回来时付出更大的努力，agents需要异步协调。

## 何时使用团队

团队增加了开销。在以下情况下使用它们：

- 任务自然分解为独立的子任务
- 不同的子任务受益于不同的系统提示或专业化
- 您将在几天后恢复会话
- 您需要任务委派和完成的持久记录

对于更简单的情况，拥有良好工具的单个 agent 通常比团队更高效。


## 原文对照

<details>
<summary>展开完整原文</summary>

Multi-agent teams let you break complex work across multiple agents that coordinate through a shared task board. One agent acts as the coordinator, delegating subtasks to specialist agents and merging their results.

## Enabling Teams

Teams are a ClineCore feature. Enable them in the session config:

```typescript
import { ClineCore } from "@cline/sdk"

const cline = await ClineCore.create({ clientName: "team-app" })

const session = await cline.start({
  prompt: "Plan and implement a user authentication module with tests",
  config: {
    providerId: "anthropic",
    modelId: "claude-sonnet-4-6",
    apiKey: process.env.ANTHROPIC_API_KEY,
    systemPrompt: "You are a coordinator for a multi-agent coding team.",
    cwd: "/path/to/project",
    workspaceRoot: "/path/to/project",
    enableTools: true,
    enableSpawnAgent: true,
    enableAgentTeams: true,
    teamName: "auth-sprint",
  },
})
```

## How Teams Work

When teams are enabled, the coordinator agent gets these tools:

| Purpose | Tools |
|---------|-------|
| Teammates | `team_spawn_teammate`, `team_shutdown_teammate`, `team_status` |
| Task board | `team_task` (create, list, claim, complete and block tasks) |
| Running tasks | `team_run_task`, `team_list_runs`, `team_await_runs`, `team_cancel_run` |
| Messages | `team_send_message`, `team_broadcast`, `team_read_mailbox`, `team_mission_log` |
| Combined results | `team_create_outcome`, `team_attach_outcome_fragment`, `team_review_outcome_fragment`, `team_finalize_outcome`, `team_list_outcomes` |
| Clean-up | `team_cleanup` |

Teammates get the same tools except `team_spawn_teammate`, and only the coordinator can shut teammates down or clean up the team. The coordinator decides how to split work, which agents to create, and how to combine results.

## Team Persistence

Team state belongs to the session. Cline saves each team's task board, mailbox, mission log, results and teammates in `~/.cline/data/db/teams.db`, under the session's ID. When the session resumes, its team and teammates come back.

## Via CLI

Start a prompt with `/team` to start a team, and resume the session with `cline --id <session-id>` to continue its work. [Agent Teams](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/sdk/guides/Cline_Cline CLI 多 Agent 团队与会话恢复.md>) has the details.

```bash
cline "/team Plan and implement user auth with tests"
```

## Sub-Agents vs Teams

The SDK offers two levels of multi-agent coordination:

| Feature | Sub-Agents | Teams |
|---------|-----------|-------|
| Enable with | `enableSpawnAgent: true` | `enableAgentTeams: true` |
| Persistence | Within session only | Restored when the session resumes |
| Coordination | Parent-child | Peer-to-peer with task board |
| Shared state | None | Task board, mailbox, mission log |
| Best for | One-off delegation | Complex projects that you resume over time |

Sub-agents are lighter weight. The parent agent spawns a child, waits for its result, and continues. No persistent state, no task board.

```typescript
// Sub-agents: simple delegation within a single session
const session = await cline.start({
  config: {
    enableSpawnAgent: true,  // Agent can spawn sub-agents
    // ...
  },
  // ...
})
```

Teams are for bigger efforts that you come back to, where agents need to coordinate asynchronously.

## When to Use Teams

Teams add overhead. Use them when:

- The task naturally decomposes into independent subtasks
- Different subtasks benefit from different system prompts or specializations
- You will resume the session over several days
- You want a persistent record of task delegation and completion

For simpler cases, a single agent with good tools is usually more efficient than a team.


</details>
