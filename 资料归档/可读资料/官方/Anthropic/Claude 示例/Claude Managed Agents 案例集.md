# Claude Managed Agents 案例集

介绍持久会话、工具与沙箱环境的托管运行方式，提供应用、教程和可运行案例入口。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-09-21](<https://github.com/anthropics/claude-cookbooks/commit/daac2acb91544767804e42cf720df07c232fd5b6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/managed_agents/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/Claude Managed Agents 案例集.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Claude 管理 Agents 食谱

Claude 托管 Agents 是 Anthropic 的托管运行时，用于有状态的工具使用 
agents。您定义一次 agent 和沙盒环境，然后在会话中运行 
 它们，以持续保存文件、工具状态和对话 
。这些教程从头到尾地展示了它。

## MongoDB 托管 Agents

想要给 agent 一个真正的数据库 - 用于检索、图形遍历以及作为 
 的记录系统？
**[`CMA_with_mongodb_atlas.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_with_mongodb_atlas.ipynb>)**
 是综合食谱：将 MongoDB 连接到 CMA agent 的三种方法（以及
凭证位于每条路径上的位置），四种检索模式
（矢量/全文/混合/图形）作为可提升的构建块，以及以Atlas为系统的
端到端人机循环欺诈审查agent 
记录和审核骨干。

## 应用食谱

- **[data_analyst_agent.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/data_analyst_agent.ipynb>)** 构建一个
分析师使用 pandas
 和情节将 CSV 转变为叙述性 HTML 报告。您将配置环境和 agent、装载 
 数据集、流式传输运行并检索生成的工件。
- **[slack_data_bot.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/slack_data_bot.ipynb>)** 将 agent 包装在
Slack 机器人。使用 CSV 提及它即可在线程中获取报告；
 回复将继续同一会话。
- **[sre_incident_responder.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/sre_incident_responder.ipynb>)** 放置
在待命路径上管理 Agents：寻呼机警报启动会话，
 agent 进行调查并打开 PR，然后在合并之前暂停以供人工 
 批准。您将连接警报 Webhook，附加 
 技能和自定义工具，并在控制台中查看完整运行情况。

## 指导教程

通过真实工作流程教授托管 Agents API 表面 
 的端到端教程。没有严格的阅读顺序，
但是`CMA_iterate_fix_failing_tests.ipynb`是一个很好的切入点，
它介绍了其他构建的每个API形状。

|笔记本|它教什么|
| --- | --- |
| [`CMA_iterate_fix_failing_tests.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_iterate_fix_failing_tests.ipynb>) |执行→观察→修复失败的测试套件上的循环。入门笔记本：介绍了 agent/环境/会话、文件挂载以及通过将有缺陷的包变为绿色的流式事件循环。|
| [`CMA_orchestrate_issue_to_pr.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_orchestrate_issue_to_pr.ipynb>) |问题 → 修复 → PR → CI → 审查 → 通过模拟合并 `gh` CLI。多回转转向、从 CI 故障中恢复的中链以及评论意见。侧边栏展示了如何将 `github_repository` 资源的文件挂载与真实存储库交换。|
| [`CMA_explore_unfamiliar_codebase.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_explore_unfamiliar_codebase.ipynb>) |扎根于不熟悉的代码库，并植入陈旧的文档陷阱。侧边栏显示如何通过 `sessions.resources.add` 将资源添加到正在运行的会话。|
| [`CMA_gate_human_in_the_loop.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_gate_human_in_the_loop.ipynb>) |通过自定义工具 `decide()` / `escalate()` 进行人机交互费用审批。涵盖自定义工具往返模式、`requires_action` 空闲反弹和并行工具调用重复数据删除。|
| [`CMA_with_mongodb_atlas.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_with_mongodb_atlas.ipynb>) |**MongoDB Atlas**端到端：三种连接路径（主机端自定义工具、自托管沙箱、自托管MCP）、四种检索模式（矢量/全文/RRF混合/`$graphLookup`）和人在循环欺诈审查agent 在同一集群中记录决策和仅附加审计跟踪。通过 `requires_action` 门暂停危险案件；显示了 CI 的自动解析门和用于生产的 webhook 切换。|
| [`CMA_prompt_versioning_and_rollback.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_prompt_versioning_and_rollback.ipynb>) |服务器端提示版本控制：创建 v1，根据标记的测试集进行评估，交付 v2，检测回归，通过将会话固定到版本 1 进行回滚。涵盖 `agents.update`、固定在 `sessions.create` 上的版本，以及当提示不是代码时审查门移动的位置。|
| [`CMA_operate_in_production.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_operate_in_production.ipynb>) |生产设置：MCP 工具集、每个最终用户凭证的保管库、HITL 的 `session.status_idled` 和 `session.budget_reached` Webhook 模式以及无长期连接的成本监控、`inference_geo` 驻留固定以及资源生命周期 CRUD动词。|
| [`CMA_remember_user_preferences.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_remember_user_preferences.ipynb>) |记忆存储：一种购物 agent，可在一次会话中了解顾客的偏好，并在下一次会话中回忆起这些偏好。涵盖 `memory_stores.create`、`resources` 附件以及每个附件 `instructions`，从您自己的应用程序检查和播种内存，并将每个客户的读写存储与品牌范围的只读存储相结合。|
| [`CMA_coordinate_specialist_team.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_coordinate_specialist_team.ipynb>) |通过 `multiagent` 协调员配置的异构团队：协调员运行三名专家（网络搜索研究员、文件阅读图书管理员、基于规则的定价员），并使用范围内的工具集来组装销售提案。涵盖 `multiagent` 字段、`thread_created` / `thread_message_received` 事件类型、为什么每个角色工具范围很重要，以及协调员在编写之前查阅的 `advisor` 名册条目。|
| [`CMA_watch_subagents_live.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_watch_subagents_live.ipynb>) |您可以实时观看的课程规划团队。涵盖每个线程 `event_deltas`，因此子代理文本实时流，会话创建时的 `initial_events`，每个 agent 模型 `effort` 作为成本杠杆，以及无版本 `agents.update`。建立在协调专家团队形状的基础上。|
| [`CMA_plan_big_execute_small.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_plan_big_execute_small.ipynb>) |协调器模式经济学：前沿协调器将大量代币的网络读取委托给廉价的并行工作人员，根据严格匹配的单独前沿控制进行衡量，每个线程 `usage.list_cost` 计量和会话 `budget` 作为扇出护栏。|
| [`CMA_verify_with_outcome_grader.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_verify_with_outcome_grader.ipynb>) |使用结果构建评分和修改循环：作者起草引用的研究摘要，无状态评分者获取每个 URL 并对照标题检查每个引用，反馈推动修改，直到摘要通过。涵盖 `user.define_outcome`、`span.outcome_evaluation_*` 事件，以及如何编写评分者可以采取行动的标题。|
| [`CMA_consult_an_advisor.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_consult_an_advisor.ipynb>) |中层工作 agent，通过 `advisor` 名册条目在中途咨询更强大的模型。涵盖 `multiagent.agents` 中的 `{"type": "advisor"}` 条目、主流上的 `anthropic.advisor` 线程生命周期、顾问线程 `usage` 的每次咨询成本以及经过编辑的交付臂。|
| [`CMA_cap_session_spend.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_cap_session_spend.ipynb>) |强制执行一次会话的支出上限。涵盖 `sessions.create` 上的 `budget` 字段、`session.usage` 快照事件和 `usage.list_cost`、`budget_reached` 暂停以及使用 `sessions.update` 升高、降低和移除上限。|
| [`CMA_use_skills_from_a_repo.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_use_skills_from_a_repo.ipynb>) |在会话开始时从已安装存储库的根 `.claude/skills/` 发现的技能，agent 上没有 `skills` 字段。涵盖扫描仪期望的布局、读取然后遵循协议和生命周期规则（技能上限、单次扫描、惰性嵌套发现）。|
| [`CMA_pin_inference_geo.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_pin_inference_geo.ipynb>) |agent 定义上的数据驻留：`model.inference_geo` 固定（强制执行该固定）、其与工作区 `allowed_inference_geos` 策略的相互作用，以及使用 `agent_with_overrides` 的单会话地理覆盖。|

流事件循环在 
 迭代笔记本中逐行遍历，然后分解到 
`utilities.stream_until_end_turn` 中，以便其他笔记本可以 
 导入它，而不是重复 `match ev.type:` 块。 
gate 笔记本和 MongoDB 食谱是例外：它们保持 
 内联循环，因为自定义工具 agents 除了 `end_turn` 之外还需要处理 
`requires_action` 空闲反弹，其中
助手没有涵盖。

## 开始使用

在您的环境中设置 `ANTHROPIC_API_KEY`，然后在 Jupyter 中打开 
`data_analyst_agent.ipynb` 并运行顶部到 
 底部的单元格。每个笔记本都会安装自己的依赖项，并提示 
 输入所需的任何凭据。 
`CMA_orchestrate_issue_to_pr.ipynb` 中的编排到 PR 侧边栏、`CMA_operate_in_production.ipynb` 中的库支持的 MCP 示例 
 和 
`CMA_use_skills_from_a_repo.ipynb` 还需要 `GITHUB_TOKEN`
 集（细粒度PAT 阅读公共仓库就足够了）。 `CMA_with_mongodb_atlas.ipynb` 中的 
MongoDB 食谱另外 
 需要将 `MONGO_URI` 设置为 Atlas SRV 连接字符串（M0
 免费集群有效）；提供者密钥 - `MDB_ATLAS_API_KEY` 或 
`VOYAGE_API_KEY` - 是可选的，仅启用 
 实时嵌入和重新排序路径。

所有食谱固定数据 - 输入 CSV 和支持资产
应用的食谱，加上引导的陷阱固定装置
教程阅读自——生活在 `example_data/`。参见
[`example_data/OVERVIEW.md`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/example_data/OVERVIEW.md>) 为
目录图。

## 可运行的应用程序

该目录包含笔记本。基于托管 Agents 构建的完整应用程序，例如 
Slack 和 Linear 机器人、Sentry 分类 agent、公路旅行规划器 
 以及曾经住在这里的 MCP 服务器都在 
[claude-quickstarts/managed-agents](<https://github.com/anthropics/claude-quickstarts/tree/main/managed-agents>) 中。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Claude Managed Agents cookbooks

Claude Managed Agents is Anthropic's hosted runtime for stateful, tool-using
agents. You define an agent and a sandboxed environment once, then run
them in sessions that persist files, tool state, and conversation
across turns. These tutorials show it end to end.

## MongoDB on Managed Agents

Want to give an agent a real database — for retrieval, graph traversal, and as
its system of record?
**[`CMA_with_mongodb_atlas.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_with_mongodb_atlas.ipynb>)**
is the consolidated cookbook: three ways to connect MongoDB to a CMA agent (and
where the credential lives on each path), the four retrieval patterns
(vector / full-text / hybrid / graph) as liftable building blocks, and an
end-to-end human-in-the-loop fraud-review agent with Atlas as the system of
record and audit backbone.

## Applied cookbooks

- **[data_analyst_agent.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/data_analyst_agent.ipynb>)** builds an
  analyst that turns a CSV into a narrative HTML report using pandas
  and plotly. You'll configure an environment and agent, mount a
  dataset, stream the run, and retrieve the generated artifacts.
- **[slack_data_bot.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/slack_data_bot.ipynb>)** wraps that agent in
  a Slack bot. Mention it with a CSV to get the report in-thread;
  replies continue the same session.
- **[sre_incident_responder.ipynb](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/sre_incident_responder.ipynb>)** puts
  Managed Agents on the on-call path: a pager alert starts a session,
  the agent investigates and opens a PR, then pauses for human
  approval before merging. You'll wire the alert webhook, attach a
  Skill and custom tools, and review the full run in the Console.

## Guided tutorials

End-to-end tutorials that teach the Managed Agents API surface
through realistic workflows. There's no strict reading order,
but `CMA_iterate_fix_failing_tests.ipynb` is a good entry point,
it introduces every API shape the others build on.

| Notebook | What it teaches |
| --- | --- |
| [`CMA_iterate_fix_failing_tests.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_iterate_fix_failing_tests.ipynb>) | Do → observe → fix loop on a failing test suite. The entry-point notebook: introduces agent / environment / session, file mounts, and the streaming event loop through the lens of getting a buggy package to green. |
| [`CMA_orchestrate_issue_to_pr.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_orchestrate_issue_to_pr.ipynb>) | Issue → fix → PR → CI → review → merge through a mock `gh` CLI. Multi-turn steering, mid-chain recovery from a CI failure and a review comment. Sidebar shows how to swap the file mount for a `github_repository` resource against a real repo. |
| [`CMA_explore_unfamiliar_codebase.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_explore_unfamiliar_codebase.ipynb>) | Grounding in an unfamiliar codebase, with a planted stale-doc trap. Sidebar shows how to add resources to a running session via `sessions.resources.add`. |
| [`CMA_gate_human_in_the_loop.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_gate_human_in_the_loop.ipynb>) | Human-in-the-loop expense approval via custom-tool `decide()` / `escalate()`. Covers the custom-tool round-trip pattern, the `requires_action` idle bounce, and parallel-tool-call dedupe. |
| [`CMA_with_mongodb_atlas.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_with_mongodb_atlas.ipynb>) | **MongoDB Atlas** end to end: three connection paths (host-side custom tool, self-hosted sandbox, self-hosted MCP), the four retrieval patterns (vector / full-text / RRF hybrid / `$graphLookup`), and a human-in-the-loop fraud-review agent that records decisions and an append-only audit trail in the same cluster. Pauses on risky cases via the `requires_action` gate; shows an auto-resolving gate for CI and the webhook handoff for production. |
| [`CMA_prompt_versioning_and_rollback.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_prompt_versioning_and_rollback.ipynb>) | Server-side prompt versioning: create v1, evaluate against a labelled test set, ship v2, detect a regression, roll back by pinning sessions to version 1. Covers `agents.update`, version pinning on `sessions.create`, and where the review gate moves when prompts are not code. |
| [`CMA_operate_in_production.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_operate_in_production.ipynb>) | Production setup: MCP toolsets, vaults for per-end-user credentials, the `session.status_idled` and `session.budget_reached` webhook patterns for HITL and cost supervision without long-lived connections, `inference_geo` pinning for residency, and the resource lifecycle CRUD verbs. |
| [`CMA_remember_user_preferences.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_remember_user_preferences.ipynb>) | Memory stores: a shopping agent that learns a customer's preferences in one session and recalls them in the next. Covers `memory_stores.create`, the `resources` attachment with per-attachment `instructions`, inspecting and seeding memories from your own application, and combining a per-customer read-write store with a brand-wide read-only store. |
| [`CMA_coordinate_specialist_team.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_coordinate_specialist_team.ipynb>) | Heterogeneous team via the `multiagent` coordinator config: a coordinator runs three specialists (web-search researcher, file-reading librarian, rules-based pricer) with scoped toolsets to assemble a sales proposal. Covers the `multiagent` field, the `thread_created` / `thread_message_received` event types, why per-role tool scoping matters, and an `advisor` roster entry the coordinator consults before writing. |
| [`CMA_watch_subagents_live.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_watch_subagents_live.ipynb>) | A curriculum-planning team you can watch in real time. Covers per-thread `event_deltas` so subagent text streams live, `initial_events` on session create, per-agent model `effort` as a cost lever, and versionless `agents.update`. Builds on the coordinate-specialist-team shapes. |
| [`CMA_plan_big_execute_small.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_plan_big_execute_small.ipynb>) | Coordinator-pattern economics: a frontier coordinator delegates the token-heavy web reading to cheap parallel workers, measured against a rigor-matched solo-frontier control with per-thread `usage.list_cost` metering and a session `budget` as the fan-out guardrail. |
| [`CMA_verify_with_outcome_grader.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_verify_with_outcome_grader.ipynb>) | Build a grade-and-revise loop with Outcomes: a writer drafts a cited research brief, a stateless grader fetches every URL and checks every quote against a rubric, and feedback drives revisions until the brief passes. Covers `user.define_outcome`, the `span.outcome_evaluation_*` events, and how to write a rubric the grader can act on. |
| [`CMA_consult_an_advisor.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_consult_an_advisor.ipynb>) | A mid-tier working agent that consults a stronger model mid-turn via an `advisor` roster entry. Covers the `{"type": "advisor"}` entry in `multiagent.agents`, the `anthropic.advisor` thread lifecycle on the primary stream, per-consultation cost from the advisor thread's `usage`, and the redacted delivery arm. |
| [`CMA_cap_session_spend.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_cap_session_spend.ipynb>) | Enforced spend ceiling on one session. Covers the `budget` field on `sessions.create`, the `session.usage` snapshot events and `usage.list_cost`, the `budget_reached` pause, and raising, lowering, and removing the cap with `sessions.update`. |
| [`CMA_use_skills_from_a_repo.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_use_skills_from_a_repo.ipynb>) | Skills discovered from a mounted repository's root `.claude/skills/` at session start, with no `skills` field on the agent. Covers the layout the scanner expects, the read-then-follow protocol, and the lifecycle rules (skill cap, single scan, lazy nested discovery). |
| [`CMA_pin_inference_geo.ipynb`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/CMA_pin_inference_geo.ipynb>) | Data residency on the agent definition: `model.inference_geo` pinning, where the pin is enforced, its interplay with the workspace `allowed_inference_geos` policy, and a one-session geography override with `agent_with_overrides`. |

The streaming event loop is walked through line by line in the
iterate notebook and then factored into
`utilities.stream_until_end_turn` so the other notebooks can
import it instead of repeating the `match ev.type:` block. The
gate notebook and the MongoDB cookbook are the exceptions: they keep
the loop inline because custom-tool agents need to handle
`requires_action` idle bounces in addition to `end_turn`, which
the helper doesn't cover.

## Getting started

Set `ANTHROPIC_API_KEY` in your environment, then open
`data_analyst_agent.ipynb` in Jupyter and run the cells top to
bottom. Each notebook installs its own dependencies and prompts
for any credentials it needs. The orchestrate-to-PR sidebar in
`CMA_orchestrate_issue_to_pr.ipynb`, the vault-backed MCP example
in `CMA_operate_in_production.ipynb`, and
`CMA_use_skills_from_a_repo.ipynb` additionally need `GITHUB_TOKEN`
set (a fine-grained PAT with public-repo read is enough). The
MongoDB cookbook in `CMA_with_mongodb_atlas.ipynb` additionally
needs `MONGO_URI` set to an Atlas SRV connection string (an M0
free cluster works); a provider key — `MDB_ATLAS_API_KEY` or
`VOYAGE_API_KEY` — is optional and only enables the
live-embedding and reranker paths.

All cookbook fixture data — input CSVs and supporting assets for
the applied cookbooks, plus the planted-trap fixtures the guided
tutorials read from — lives under `example_data/`. See
[`example_data/OVERVIEW.md`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/managed_agents/example_data/OVERVIEW.md>) for the
directory map.

## Runnable apps

This directory holds notebooks. Full apps built on Managed Agents, such as
the Slack and Linear bots, the Sentry triage agent, the road trip planner,
and the MCP server that used to live here, are in
[claude-quickstarts/managed-agents](<https://github.com/anthropics/claude-quickstarts/tree/main/managed-agents>).


</details>
