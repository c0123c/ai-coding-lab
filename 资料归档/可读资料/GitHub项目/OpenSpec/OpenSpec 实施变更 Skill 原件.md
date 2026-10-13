# OpenSpec 实施变更 Skill 原件

定义变更选择、状态与上下文读取、逐项实施任务和完成或暂停时报告进度的代理指令。

- 来源：OpenSpec
- 资料类型：SKILL.md：实施工作流代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-apply-change/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-apply-change/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-apply-change
description: Implement tasks from an OpenSpec change. Use when the user wants to start implementing, continue implementation, or work through tasks. Also use when the user says "openspec apply", "opsx apply", or "openspec implement".
allowed-tools: Bash(openspec:*)
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

实施 OpenSpec 变更中的任务。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

**输入**：可以选择指定更改名称（例如，`/openspec-apply-change add-auth`）。如果省略，请检查是否可以从对话上下文中推断出来。如果含糊或不明确，您 MUST 会提示可用的更改。

**步骤**

1. **选择更改**

如果提供了名称，请使用它。否则：
   - 从对话上下文中推断用户是否提到了更改
   - 如果仅存在一项活动更改，则自动选择
   - 如果不明确，请运行 `openspec list --json` 以获取可用更改并要求用户选择一项

始终宣布：“使用更改：<name>”以及如何覆盖（例如，`/openspec-apply-change <other>`）。

2. **检查状态以了解架构**
   ```bash
   openspec status --change "<name>" --json
   ```
解析JSON即可理解：
   - `schemaName`：正在使用的工作流程（例如“规范驱动”）
   - `planningHome`、`changeRoot` 和 `actionContext`：规划范围和编辑约束
   - 哪个工件包含任务（通常是规范驱动的“任务”，检查其他任务的状态）

3. **获取使用说明**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

这将返回：
   - `contextFiles`：工件 ID -> 具体文件路径数组（因架构而异 - 可能是 proposal/specs/design/tasks 或 spec/tests/implementation/docs）
   - 进度（总计、完成、剩余）
   - 包含状态、源路径和源行的任务列表
   - 基于当前状态的动态指令
   - 可选【`context`】：当前需要的工程指令从所选根输入
   - 可选 `operationGuidance`：当前申请咨询指南
   - `missingArtifacts`（如果存在）：所需的工件 ID，无输出

**手柄状态：**
   - 如果`state: "blocked"`：显示消息并暂停执行。
     - 如果`missingArtifacts`非空：建议使用`/openspec-continue-change`创建它们。
     - 否则，请按照 CLI 说明从现有计划工件创建或修复架构配置的跟踪文件。不要假设另一个工件已准备好或在受阻时开始实施。
   - 如果 `state: "all_done"`：报告所有跟踪的任务均已完成，并建议在归档前进行适当的审查或验证
   - 否则：继续执行

将 `context` 视为必需的提示级输入。阅读并考虑它，并
在实施时应用相关的项目事实、约定和约束。
将`operationGuidance`视为可选的附加建议。阅读并考虑每个 
 条目，并遵循适用且与内置 
 工作流程兼容的条目。

将这两个字段与 CLI- 返回的状态、缺失的工件、任务、
 进度、`contextFiles` 和内置 `instruction` 分开。它们不是
任务完成的证据，不会替换内置指令，并且
不允许绕过阻塞状态。如果上下文与内置 
 指令、显式用户选择或 CLI- 控制值冲突，则报告 
 冲突并保留控制值。如果指导不适用或
与控制输入冲突，请勿遵循并解释原因。
这些是提示级行为契约，不是可执行的检查。

4. **读取上下文文件**

从应用指令输出中读取 `contextFiles` 下列出的每个文件路径。
 这些文件取决于所使用的架构：
   - **规范驱动**：提案、规范、设计、任务
   - 其他模式：遵循 CLI 输出中的 contextFiles

请勿将 `context` 或 `operationGuidance` 逐字复制到实现 
 文件或规划工件中，除非用户单独要求提供该内容。

5. **显示当前进度**

显示：
   - 正在使用的架构
   - 进度：“N/M任务完成”
   - 剩余任务概述
   - 来自CLI的动态指令

6. **执行任务（循环直到完成或阻塞）**

对于每个待处理的任务：
   - 显示正在处理哪个任务
   - 进行所需的代码更改
   - 保持变更最小化并集中精力
   - 编辑前，确认返回的`sourcePath`处的复选框和`line`仍然与任务描述相符；如果没有，请重新运行应用指令并使用刷新的位置
   - 在返回的 `sourcePath` 和 `line` 处标记任务已完成： `- [ ]` → `- [x]`
   - 重新运行应用指令并确认任务现已完成并且进度已更改
   - Continue 到下一个任务

**暂停如果：**
   - 任务不清楚→要求澄清
   - 实施揭示了一个设计问题→建议更新工件
   - 一项任务需要超出规范和任务所描述的范围，或者您可能会想要放弃、缩小、推迟或接受指定行为的例外情况，以使其适合 → 提出增加的范围并提出要求；不要默默吸收
   - 遇到错误或阻塞→报告并等待指导
   - 用户中断

7. **完成或暂停时，显示状态**

显示：
   - 本次会议完成的任务
   - 总体进度：“N/M任务完成”
   - 如果全部完成：报告跟踪的任务已完成，并建议在存档之前进行适当的审查或验证
   - 如果暂停：解释原因并等待指导

**实施期间的输出**

```
## Implementing: <change-name> (schema: <schema-name>)

Working on task 3/7: <task description>
[...implementation happening...]
✓ Task complete

Working on task 4/7: <task description>
[...implementation happening...]
✓ Task complete
```

**完成时输出**

```
## Implementation Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 7/7 tasks complete ✓

### Completed This Session
- [x] Task 1
- [x] Task 2
...

All tracked tasks are complete. Review or verify the change as appropriate
before archiving. You can archive this change with `/openspec-archive-change`.
```

**暂停时输出（遇到问题）**

```
## Implementation Paused

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 4/7 tasks complete

### Issue Encountered
<description of the issue>

**Options:**
1. <option 1>
2. <option 2>
3. Other approach

What would you like to do?
```

**护栏**
- 继续执行任务，直到完成或被阻止
- 在开始之前始终读取上下文文件（来自应用指令输出）
- 如果任务不明确，请在实施之前暂停并询问
- 如果实施过程中发现问题，请暂停并建议工件更新
- 尽量减少代码更改并限制每个任务的范围
- 完成每项任务后立即更新任务复选框
- 因错误、阻碍或不明确的需求而暂停 - 不要猜测
- 当任务需要超出规范描述的范围时，应显示增加的范围并暂停 - 切勿默默地缩小、推迟或简化指定的行为
- 只标记一个任务 `- [x]` 当其指定的行为完全实现时，而不是部分完成或推迟时
- 使用 CLI 输出中的 contextFiles，不要假设特定的文件名
- 使用每个任务的 sourcePath 和行来更新其确切的复选框
- 不要使用上下文或操作指南作为任务完成的证据
- 应用相关项目背景；报告与控制工作流程输入冲突
- 考虑每一个指导条目；解释任何不适用或相互矛盾的建议
- 不要将运行时上下文或操作指南复制到实施文件或规划工件中
- 保留 CLI- 控制的 blocked/ready/all-done 行为和完成标准

**流体工作流程集成**

该技能支持“针对变更采取行动”模型：

- **可以随时调用**：在所有工件完成之前（如果任务存在），部分实现之后，与其他操作交错
- **允许工件更新**：如果实现揭示设计问题，建议更新工件 - 非锁相，工作流畅


## 原文对照

<details>
<summary>展开完整原文</summary>

Implement tasks from an OpenSpec change.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

**Input**: Optionally specify a change name (e.g., `/openspec-apply-change add-auth`). If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **Select the change**

   If a name is provided, use it. Otherwise:
   - Infer from conversation context if the user mentioned a change
   - Auto-select if only one active change exists
   - If ambiguous, run `openspec list --json` to get available changes and ask the user to select one

   Always announce: "Using change: <name>" and how to override (e.g., `/openspec-apply-change <other>`).

2. **Check status to understand the schema**
   ```bash
   openspec status --change "<name>" --json
   ```
   Parse the JSON to understand:
   - `schemaName`: The workflow being used (e.g., "spec-driven")
   - `planningHome`, `changeRoot`, and `actionContext`: planning scope and edit constraints
   - Which artifact contains the tasks (typically "tasks" for spec-driven, check status for others)

3. **Get apply instructions**

   ```bash
   openspec instructions apply --change "<name>" --json
   ```

   This returns:
   - `contextFiles`: artifact ID -> array of concrete file paths (varies by schema - could be proposal/specs/design/tasks or spec/tests/implementation/docs)
   - Progress (total, complete, remaining)
   - Task list with status, source path, and source line
   - Dynamic instruction based on current state
   - Optional `context`: current required project instruction input from the selected root
   - Optional `operationGuidance`: current advisory guidance for apply
   - `missingArtifacts` (when present): required artifact ids with no output

   **Handle states:**
   - If `state: "blocked"`: show the message and pause implementation.
     - If `missingArtifacts` is non-empty: suggest using `/openspec-continue-change` to create them.
     - Otherwise, follow the CLI instruction to create or repair the schema-configured tracking file from existing planning artifacts. Do not assume another artifact is ready or start implementation while blocked.
   - If `state: "all_done"`: report that all tracked tasks are complete and suggest review or verification as appropriate before archiving
   - Otherwise: proceed to implementation

   Treat `context` as a required prompt-level input. Read and consider it, and
   apply relevant project facts, conventions, and constraints while implementing.
   Treat `operationGuidance` as optional additive advice. Read and consider every
   entry, and follow entries that are applicable and compatible with the built-in
   workflow.

   Keep both fields separate from CLI-returned state, missing artifacts, tasks,
   progress, `contextFiles`, and the built-in `instruction`. They are not
   evidence of task completion, do not replace the built-in instruction, and do
   not permit bypassing a blocked state. If context conflicts with the built-in
   instruction, an explicit user choice, or a CLI-controlled value, report the
   conflict and preserve the controlling value. If guidance is inapplicable or
   conflicts with those controlling inputs, do not follow it and explain why.
   These are prompt-level behavior contracts, not enforceable checks.

4. **Read context files**

   Read every file path listed under `contextFiles` from the apply instructions output.
   The files depend on the schema being used:
   - **spec-driven**: proposal, specs, design, tasks
   - Other schemas: follow the contextFiles from CLI output

   Do not copy `context` or `operationGuidance` verbatim into implementation
   files or planning artifacts unless the user separately asks for that content.

5. **Show current progress**

   Display:
   - Schema being used
   - Progress: "N/M tasks complete"
   - Remaining tasks overview
   - Dynamic instruction from CLI

6. **Implement tasks (loop until done or blocked)**

   For each pending task:
   - Show which task is being worked on
   - Make the code changes required
   - Keep changes minimal and focused
   - Before editing, confirm the checkbox at the returned `sourcePath` and `line` still matches the task description; if it does not, rerun the apply instructions and use the refreshed location
   - Mark the task complete at its returned `sourcePath` and `line`: `- [ ]` → `- [x]`
   - Rerun the apply instructions and confirm that task is now done and progress changed
   - Continue to next task

   **Pause if:**
   - Task is unclear → ask for clarification
   - Implementation reveals a design issue → suggest updating artifacts
   - A task needs work beyond what the spec and tasks describe, or you are tempted to drop, narrow, defer, or accept exceptions to specified behavior to make it fit → surface the added scope and ask; do not absorb it silently
   - Error or blocker encountered → report and wait for guidance
   - User interrupts

7. **On completion or pause, show status**

   Display:
   - Tasks completed this session
   - Overall progress: "N/M tasks complete"
   - If all done: report that tracked tasks are complete and suggest review or verification as appropriate before archiving
   - If paused: explain why and wait for guidance

**Output During Implementation**

```
## Implementing: <change-name> (schema: <schema-name>)

Working on task 3/7: <task description>
[...implementation happening...]
✓ Task complete

Working on task 4/7: <task description>
[...implementation happening...]
✓ Task complete
```

**Output On Completion**

```
## Implementation Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 7/7 tasks complete ✓

### Completed This Session
- [x] Task 1
- [x] Task 2
...

All tracked tasks are complete. Review or verify the change as appropriate
before archiving. You can archive this change with `/openspec-archive-change`.
```

**Output On Pause (Issue Encountered)**

```
## Implementation Paused

**Change:** <change-name>
**Schema:** <schema-name>
**Progress:** 4/7 tasks complete

### Issue Encountered
<description of the issue>

**Options:**
1. <option 1>
2. <option 2>
3. Other approach

What would you like to do?
```

**Guardrails**
- Keep going through tasks until done or blocked
- Always read context files before starting (from the apply instructions output)
- If task is ambiguous, pause and ask before implementing
- If implementation reveals issues, pause and suggest artifact updates
- Keep code changes minimal and scoped to each task
- Update task checkbox immediately after completing each task
- Pause on errors, blockers, or unclear requirements - don't guess
- When a task needs work beyond what the spec describes, surface the added scope and pause - never silently narrow, defer, or simplify away specified behavior
- Only mark a task `- [x]` when its specified behavior is fully implemented, not when it is partially done or deferred
- Use contextFiles from CLI output, don't assume specific file names
- Use each task's sourcePath and line to update its exact checkbox
- Do not use context or operation guidance as proof that a task is complete
- Apply relevant project context; report conflicts with controlling workflow inputs
- Consider every guidance entry; explain any inapplicable or conflicting advice
- Do not copy runtime context or operation guidance into implementation files or planning artifacts
- Preserve CLI-controlled blocked/ready/all-done behavior and completion criteria

**Fluid Workflow Integration**

This skill supports the "actions on a change" model:

- **Can be invoked anytime**: Before all artifacts are done (if tasks exist), after partial implementation, interleaved with other actions
- **Allows artifact updates**: If implementation reveals design issues, suggest updating artifacts - not phase-locked, work fluidly


</details>
