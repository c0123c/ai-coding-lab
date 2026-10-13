# Qwen Code 多 Agent 协调

介绍实验性团队运行时、任务清单、消息与角色隔离，以及调查角色只读和工作树写入的配置。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-12](<https://github.com/QwenLM/qwen-code/commit/8858d4340bbbb46f693dd09767aaaadc7ec7cc9b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/multi-agent-coordination.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 多 Agent 协调.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 多Agent协调

Qwen Code 可以通过实验性的 Agent 团队运行时协调多个队友。队友接收单独的任务、共享任务列表、交换消息并显示在现有的 Agent 视图选项卡中。 `/coordinate` 默认调查工作人员使用强制只读工具集，并且可以将一名编写者放置在​​领导者拥有的 Git 工作树中。

## 启用Agent团队

在Qwen Code设置中将`experimental.agentTeam`设置为`true`并重新启动，或使用`QWEN_CODE_ENABLE_AGENT_TEAM=1`启动Qwen Code。

## 运行协调任务

使用捆绑技能来实现目标：

```text
/coordinate investigate the authentication regression and propose the smallest fix
```

领导者创建一个团队，分配最多三个独立的工作流，并使用现有的团队工具来获取消息和任务状态。通过现有的 Agent 视图 UI，队友对话和批准仍然可见。只读队友无法执行shell命令或写入文件。如果需要实施，领导者可以创建一个 Git 工作树，并将一名编写团队成员固定在其中；领导者仍然是当前分支的唯一合并权限。

如果Agent团队被禁用，`/coordinate`仍然可以使用普通前台agents进行只读并行调查。这种后备措施是授权，而不是协作团队：工作人员只向领导者报告。

## 选择正确的多agent模式

|模式|用它来|通讯|工作区行为|
| ----------------------------- | --------------------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------- |
|`/coordinate` 与 Agent 团队|不同的工作流程促成同一个结果|共享任务和队友消息|强制只读工作者；可选的单个工作树编写器|
|子代理|小委托任务|工人向家长汇报|取决于所选的agent|
|竞技场|多个模型在同一任务上竞争|Agents 不合作|孤立的工作树；选出一名获胜者|
|赫尔德|协调不同的 CLI 产品或远程终端会话|外部端子级控制|外部管理 Qwen Code|

当前工作流程有意重用进程内 Agent 团队运行时和 Agent 视图 UI。队友通常会继承会话模型，尽管 agent 定义可以覆盖它。持久独立 PTY 会话、跨供应商工作人员和远程连接是单独的产品问题，并且不由 `/coordinate` 实现。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Multi-Agent Coordination

Qwen Code can coordinate several teammates with the experimental Agent Team runtime. Teammates receive separate tasks, share a task list, exchange messages, and appear in the existing Agent View tabs. `/coordinate` defaults investigation workers to an enforced read-only tool set and can place one writer in a leader-owned Git worktree.

## Enable Agent Team

Set `experimental.agentTeam` to `true` in Qwen Code settings and restart, or start Qwen Code with `QWEN_CODE_ENABLE_AGENT_TEAM=1`.

## Run a coordinated task

Use the bundled skill with a goal:

```text
/coordinate investigate the authentication regression and propose the smallest fix
```

The leader creates a team, assigns up to three independent workstreams, and uses the existing team tools for messages and task state. Teammate conversations and approvals remain visible through the existing Agent View UI. Read-only teammates cannot execute shell commands or write files. If implementation is needed, the leader can create one Git worktree and pin one writer teammate to it; the leader remains the only merge authority for the current branch.

If Agent Team is disabled, `/coordinate` can still use ordinary foreground agents for read-only parallel investigation. That fallback is delegation, not a collaborating team: the workers report only to the leader.

## Choosing the right multi-agent mode

| Mode                          | Use it for                                                      | Communication                      | Workspace behavior                                          |
| ----------------------------- | --------------------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------- |
| `/coordinate` with Agent Team | Different workstreams contributing to one result                | Shared tasks and teammate messages | Enforced read-only workers; optional single worktree writer |
| Subagents                     | Small delegated tasks                                           | Worker reports to parent           | Depends on the selected agent                               |
| Arena                         | Several models competing on the same task                       | Agents do not collaborate          | Isolated worktrees; one winner is selected                  |
| Herdr                         | Coordinating different CLI products or remote terminal sessions | External terminal-level control    | Managed outside Qwen Code                                   |

The current workflow deliberately reuses the in-process Agent Team runtime and Agent View UI. Teammates normally inherit the session model, although an agent definition can override it. Persistent independent PTY sessions, cross-vendor workers, and remote attach are separate product concerns and are not implemented by `/coordinate`.


</details>
