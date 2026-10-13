# 用 /fleet 并行运行多个 Agent

介绍任务拆分、明确文件边界和依赖、选择专门角色及验证子任务执行的方法，并分析并行协作常见问题。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-04-01](<https://github.blog/ai-and-ml/github-copilot/run-multiple-agents-at-once-with-fleet-in-copilot-cli/>)；更新：[2026-04-14](<https://github.blog/ai-and-ml/github-copilot/run-multiple-agents-at-once-with-fleet-in-copilot-cli/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/run-multiple-agents-at-once-with-fleet-in-copilot-cli/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 fleet 并行运行多个 Agent.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Copilot CLI 中的 /fleet 一次运行多个 agents

如果 [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) 可以同时处理五个文件而不是一个怎么办？这就是 `/fleet` 的用武之地。

`/fleet` 是 Copilot CLI 中的斜杠命令，使 Copilot 能够同时与多个子代理并行工作。 Copilot 现在不再是按顺序完成任务，而是有一个幕后协调器，可以规划您的目标并将其分解为独立的工作项，并分派多个 agents 同时执行它们。在不同的文件、代码库的不同部分，一次性完成。

想了解更多关于`/fleet`的工作原理，更重要的是，如何最有效地使用它？让我们开始吧。

## 它是如何运作的

当您在提示下运行 `/fleet` 时，幕后编排器：

1. 将您的任务分解为具有依赖性的离散工作项。
2. 确定哪些项目可以并行运行，哪些项目必须等待。
3. 同时发送独立项目作为背景子agents。
4. 轮询完成，然后调度下一波。
5. 验证输出并合成任何最终工件。

每个子agent都有自己的上下文窗口，但共享相同的文件系统。他们不能直接交谈；仅协调器坐标。

将其视为项目负责人，将工作分配给团队、检查进度并组装最终可交付成果。

## 开始使用

通过发送 `/fleet <YOUR OBJECTIVE PROMPT>` 启动舰队模式。例如：

```
/fleet Refactor the auth module, update tests, and fix the related docs in the folder docs/auth/
```

就是这样。编排器获取您的目标，找出可以并行化的内容，然后开始调度。

您还可以在终端中以非交互方式运行它：

```
copilot -p "/fleet <YOUR TASK>" --no-ask-user
```

非交互模式需要 `--no-ask-user` 标志，因为无法响应提示。现在让我们看看什么是好的提示。

## 编写并行性良好的提示

`/fleet` 提示的质量决定了工作分配的效率。关键是为协调器提供足够的结构来干净地分解您的任务。

一个好方法是具体说明可交付成果。将每个工作项映射到具体的工件，例如文件、测试套件或文档的一部分。模糊的提示会导致顺序执行，因为协调器无法识别独立的部分。

例如，您可以尝试：而不是：`/fleet Build the documentation`：

```
/fleet Create docs for the API module: 

- docs/authentication.md covering token flow and examples 

- docs/endpoints.md with request/response schemas for all REST endpoints 

- docs/errors.md with error codes and troubleshooting steps 

- docs/index.md linking to all three pages (depends on the others finishing first)
```

第二个提示为协调器提供了四种不同的可交付成果，其中三种可以并行运行，另一种则依赖于它们。

## 设定明确的界限

当子agents确切地知道他们的范围开始和结束的位置时，他们的工作效果最好。编写提示时包括：

* 文件或模块边界：每个轨道拥有哪些目录或文件
* 约束：不要碰什么（例如，没有测试更改，没有依赖项升级）
* 验证标准：Lint、类型检查、必须通过的测试

这是展示这些边界的提示：

```
/fleet Implement feature flags in three tracks: 

1. API layer: add flag evaluation to src/api/middleware/ and include unit tests that look for successful flag evaluation and tests API endpoints  

2. UI: wire toggle components in src/components/flags/ and introduce no new dependencies 

3. Config: add flag definitions to config/features.yaml  and validate against schema 

Run independent tracks in parallel. No changes outside assigned directories.
```

## 当依赖项存在时声明它们

如果一项工作依赖于另一项工作，请说出来。编排器将序列化这些项目并并行化其余项目。例如：

```
/fleet Migrate the database layer: 

1. Write new schema in migrations/005_users.sql 

2. Update the ORM models in src/models/user.ts (depends on 1) 

3. Update API handlers in src/api/users.ts (depends on 2) 

4. Write integration tests in tests/users.test.ts (depends on 2) 

 Items 3 and 4 can run in parallel after item 2 completes.
```

## 针对不同的作业使用自定义 agents

您可以在 `.github/agents/` 中定义专门的 agents 并在 `/fleet` 提示符中引用它们。每个agent可以指定自己的模型、工具和指令。请注意，如果您不指定使用哪个模型，agents将使用当前的默认模型。

```
# .github/agents/technical-writer.md 

--- 

name: technical-writer 

description: Documentation specialist 

model: claude-sonnet-4 

tools: ["bash", "create", "edit", "view"] 

--- 

You write clear, concise technical documentation. Follow the project style guide in /docs/styleguide.md.
```

然后在提示中引用自定义 agent：

```
/fleet Use @technical-writer.md as the agent for all docs tasks and the default agent for code changes.
```

当不同的轨道需要不同的强度时，例如使用较重的模型来处理复杂的逻辑，使用较轻的模型来处理样板文档，这非常有用。

## 如何验证子代理是否正在部署

观看编排器如何部署子代理，这是学习如何编写并行性良好的提示的最快方法。

使用这个快速清单：

* 分解出现：在开始工作之前，查看与您分享的计划Copilot，看看它是否将工作分解为多个轨道，而不是一个长的线性计划。
* 后台任务 UI 确认活动：一旦开始工作，运行 `/tasks` 打开任务对话框并检查正在运行的后台任务。
* 出现并行进度：更新同时移动的参考单独轨道。

如果队列似乎没有并行化，请尝试停止 Copilot 的工作并要求进行显式分解：

```
Decompose this into independent tracks first, then execute tracks in parallel. Report each track separately with status and blockers.
```

## 避免常见陷阱

Fleet 很强大，但有一些问题值得提前了解。

### 对文件进行分区

子agents共享一个没有文件锁定的文件系统。如果两个 agents 写入同一个文件，则最后一个完成的获胜 - 静默。没有错误，没有合并，只是覆盖。

解决方法是在提示中分配每个 agent 不同的文件。如果多个 agents 需要贡献到单个文件，请考虑将每个写入临时路径，并让协调器在最后合并它们。或者为 agents 设置明确的顺序。

### 保持提示独立

子agents无法看到协调器的对话历史记录。当协调器调度子agent时，它会传递一个提示，但该提示需要包含子agent所需的所有内容。如果您已经在会话早期收集了有用的上下文，请确保您的 `/fleet` 提示包含它（或引用子 agents 可以读取的文件）。

### 指挥正在进行中的车队

调度完成后，您可以发送后续提示来指导编排器：

* `Prioritize failing tests first, then complete remaining tasks.`
* `List active sub-agents and what each is currently doing.`
* `Mark done only when lint, type check, and all tests pass.`

## 何时使用 `/fleet`（何时不使用）

当您的任务具有自然并行性（多个文件、独立模块或可分离关注点）时，`/fleet` 就会大放异彩。它对于以下方面特别有效：

* 同时重构多个文件。
* 一次生成多个组件的文档。
* 实现涵盖 API、UI 的功能并进行测试。
* 运行不共享状态的独立代码修改。

对于严格线性的单文件工作，常规的 Copilot CLI 提示更简单且同样快速。舰队增加了协调开销，因此当有实际工作需要分配时，它会带来回报。

当你把它当作一个团队而不是一个魔术来对待时，`/fleet`是最有用的。从小处开始。选择一个具有清晰输出、干净文件边界和明显并行性的任务。了解协调器如何分解工作、它在哪里有帮助、在哪里有阻碍。当您变得更加熟悉时，可以通过更大的重构、多轨功能或并行的文档和测试来进一步推动它。了解 `/fleet` 何时获得回报的最快方法是在实际工作中尝试并根据您所看到的内容调整提示。

[开始使用 GitHub Copilot CLI >](<https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## 撰写者

![马特·奈伊](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [马特·奈伊](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

项目经理总监，我领导 GitHub 的 AI forEveryone 项目。

![布莱恩LaFlamme](<https://avatars.githubusercontent.com/u/18745491?v=4&s=200>)

### [布莱恩 LaFlamme](<https://github.blog/author/briancl2/>)

[@briancl2](<https://github.com/briancl2>)

高级企业客户经理


## 原文对照

<details>
<summary>展开完整原文</summary>

# Run multiple agents at once with /fleet in Copilot CLI

What if [GitHub Copilot CLI](<https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>) could work on five files at the same time instead of one? That’s where `/fleet` comes in.

`/fleet` is a slash command in Copilot CLI that enables Copilot to simultaneously work with multiple subagents in parallel. Instead of working through tasks sequentially, Copilot now has a behind the scenes orchestrator that plans and breaks your objective into independent work items and dispatches multiple agents to execute them simultaneously. On different files, in different parts of your codebase, all at once.

Want to learn more about how `/fleet` works, and more importantly, how to use it most effectively? Let’s jump in.

## How it works

When you run `/fleet` with a prompt, the behind-the-scenes orchestrator:

1. Decomposes your task into discrete work items with dependencies.
2. Identifies which items can run in parallel versus which must wait.
3. Dispatches independent items as background sub-agents simultaneously.
4. Polls for completion, then dispatches the next wave.
5. Verifies outputs and synthesizes any final artifacts.

Each sub-agent gets its own context window but shares the same filesystem. They can’t talk to each other directly; only the orchestrator coordinates.

Think of it as a project lead who assigns work to a team, checks in on progress, and assembles the final deliverable.

## Getting started

Start fleet mode by sending `/fleet <YOUR OBJECTIVE PROMPT>`. For example:

```
/fleet Refactor the auth module, update tests, and fix the related docs in the folder docs/auth/
```

That’s it. The orchestrator takes your objective, figures out what can be parallelized, and starts dispatching.

You can also run it non-interactively in your terminal:

```
copilot -p "/fleet <YOUR TASK>" --no-ask-user
```

The `--no-ask-user` flag is required for non-interactive mode, since there’s no way to respond to prompts. Now let’s look at what makes a good prompt.

## Write prompts that parallelize well

The quality of your `/fleet` prompt determines how effectively work gets distributed. The key is giving the orchestrator enough structure to cleanly break down your task.

A good way to do that is being specific about deliverables. Map every work item to a concrete artifact like a file, a test suite, or a section of documentation. Vague prompts lead to sequential execution because the orchestrator can’t identify independent pieces.

For example, instead of: `/fleet Build the documentation`, you could try:

```
/fleet Create docs for the API module: 

- docs/authentication.md covering token flow and examples 

- docs/endpoints.md with request/response schemas for all REST endpoints 

- docs/errors.md with error codes and troubleshooting steps 

- docs/index.md linking to all three pages (depends on the others finishing first)
```

The second prompt gives the orchestrator four distinct deliverables, three of which can run in parallel, and one that depends on them.

## Set explicit boundaries

Sub-agents work best when they know exactly where their scope starts and ends. When writing your prompt include:

* File or module boundaries: Which directories or files each track owns
* Constraints: What not to touch (e.g., no test changes, no dependency upgrades)
* Validation criteria: Lint, type checks, tests that must pass

Here’s a prompt that showcases these boundaries:

```
/fleet Implement feature flags in three tracks: 

1. API layer: add flag evaluation to src/api/middleware/ and include unit tests that look for successful flag evaluation and tests API endpoints  

2. UI: wire toggle components in src/components/flags/ and introduce no new dependencies 

3. Config: add flag definitions to config/features.yaml  and validate against schema 

Run independent tracks in parallel. No changes outside assigned directories.
```

## Declare dependencies when they exist

If one piece of work depends on another, say so. The orchestrator will serialize those items and parallelize the rest. For example:

```
/fleet Migrate the database layer: 

1. Write new schema in migrations/005_users.sql 

2. Update the ORM models in src/models/user.ts (depends on 1) 

3. Update API handlers in src/api/users.ts (depends on 2) 

4. Write integration tests in tests/users.test.ts (depends on 2) 

 Items 3 and 4 can run in parallel after item 2 completes.
```

## Use custom agents for different jobs

You can define specialized agents in `.github/agents/` and reference them in your `/fleet` prompt. Each agent can specify its own model, tools, and instructions. Be aware that if you don’t specify which model to use, agents will use the current default model.

```
# .github/agents/technical-writer.md 

--- 

name: technical-writer 

description: Documentation specialist 

model: claude-sonnet-4 

tools: ["bash", "create", "edit", "view"] 

--- 

You write clear, concise technical documentation. Follow the project style guide in /docs/styleguide.md.
```

Then reference the custom agent in your prompt:

```
/fleet Use @technical-writer.md as the agent for all docs tasks and the default agent for code changes.
```

This is useful when different tracks need different strengths such as using a heavier model for complex logic and a lighter one for boilerplate documentation.

## How to verify subagents are deploying

Watch how the orchestrator deploys subagents, it’s the fastest way to learn how to write prompts that parallelize well.

Use this quick checklist:

* Decomposition appears: Before it starts working, review the plan Copilot shares with you to see if it breaks work into multiple tracks, instead of one long linear plan.
* Background task UI confirms activity: Once it begins working, run `/tasks` to open the tasks dialog and inspect running background tasks.
* Parallel progress appears: Updates reference separate tracks moving at the same time.

If the fleet doesn’t seem to be parallelizing, try stopping Copilot’s work and asking for an explicit decomposition:

```
Decompose this into independent tracks first, then execute tracks in parallel. Report each track separately with status and blockers.
```

## Avoiding common pitfalls

Fleet is powerful, but a few gotchas are worth knowing upfront.

### Partition your files

Sub-agents share a filesystem with no file locking. If two agents write to the same file, the last one to finish wins—silently. No error, no merge, just an overwrite.

The fix is to assign each agent distinct files in your prompt. If multiple agents need to contribute to a single file, consider having each write to a temporary path and let the orchestrator merge them at the end. Or set an explicit order for the agents to follow.

### Keep prompts self-contained

Sub-agents can’t see the orchestrator’s conversation history. When the orchestrator dispatches a sub-agent, it passes along a prompt, but that prompt needs to include everything the sub-agent needs. If you’ve already gathered useful context earlier in the session, make sure your `/fleet` prompt includes it (or references files the sub-agents can read).

### Steering a fleet in progress

After dispatching, you can send follow-up prompts to guide the orchestrator:

* `Prioritize failing tests first, then complete remaining tasks.`
* `List active sub-agents and what each is currently doing.`
* `Mark done only when lint, type check, and all tests pass.`

## When to use `/fleet` (and when not to)

`/fleet` shines when your task has natural parallelism—multiple files, independent modules, or separable concerns. It’s particularly effective for:

* Refactoring across multiple files simultaneously.
* Generating documentation for several components at once.
* Implementing a feature that spans API, UI, and tests.
* Running independent code modifications that don’t share state.

For strictly linear, single-file work, regular Copilot CLI prompts are simpler and just as fast. Fleet adds coordination overhead, so it pays off when there’s real work to distribute.

`/fleet` is most useful when you treat it like a team, not a magic trick. Start small. Pick a task with clear outputs, clean file boundaries, and obvious parallelism. See how the orchestrator decomposes the work, where it helps, and where it gets in the way. As you get more comfortable, push it further with larger refactors, multi‑track features, or docs and tests in parallel. The fastest way to learn when `/fleet` pays off is to try it on real work and adjust your prompts based on what you see.

[Get started with GitHub Copilot CLI >](<https://github.com/features/copilot/cli?utm_source=blog-cli-fleet-agents-cta&utm_medium=blog&utm_campaign=dev-pod-copilot-cli-2026>)

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## Written by

![Matt Nigh](<https://avatars.githubusercontent.com/u/5862416?v=4&s=200>)

### [Matt Nigh](<https://github.blog/author/mattnigh/>)

[@mattnigh](<https://github.com/mattnigh>)

Program Manager Director, I lead the AI for Everyone program at GitHub.

![Brian LaFlamme](<https://avatars.githubusercontent.com/u/18745491?v=4&s=200>)

### [Brian LaFlamme](<https://github.blog/author/briancl2/>)

[@briancl2](<https://github.com/briancl2>)

Senior Enterprise Account Executive


</details>
