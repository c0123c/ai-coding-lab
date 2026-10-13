# OpenSpec 完整工作循环教学 Skill 原件

定义前置检查、从代码库选择小任务、讲解探索与工件生成、实施验证和归档的引导式学习过程。

- 来源：OpenSpec
- 资料类型：SKILL.md：引导式教学代理指令原件
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/skills/openspec-onboard/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/skills/openspec-onboard/SKILL.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: openspec-onboard
description: Guided onboarding for OpenSpec - walk through a complete workflow cycle with narration and real codebase work. Also use when the user says "openspec onboard" or "opsx onboard".
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

指导用户完成第一个完整的 OpenSpec 工作流程周期。这是一种教学体验 - 您将在解释每个步骤的同时在他们的代码库中进行实际工作。

**商店选择：** 如果用户命名商店（商店是在本机上注册的独立 OpenSpec 存储库）或工作位于其中，请运行 `openspec store list --json` 以发现注册的商店 ID，然后在读取或写入规格和更改的命令上传递 `--store <id>` (`new change`、`status`、`instructions`、`list`、`show`、`validate`、`archive`、`doctor`、`context`、 `schemas`、`view`）。选择后，将 `--store <id>` 视为工作流程其余部分的粘性。下面这些命令的每个未指定范围的示例都是简写形式：在运行之前，附加标志。例如，运行 `openspec status --change "<name>" --json --store "<id>"`，而不是下面显示的无作用域形式。其他命令不采用该标志。命令打印的提示已经带有该标志；继续跟进。如果没有存储，命令将作用于最近的本地 `openspec/` 根。

**项目检查：** 这些步骤需要已使用 OpenSpec 的项目。在写入任何内容（`new change`、`archive`、`sync specs`或创作工件文件）的第一步之前，请确认项目具有根：运行`openspec list --json`（选择商店时使用`--store <id>`，因为该商店是根）并读取`root`。根对象意味着项目已设置。 `"root": null` 意味着它不是 - 这里没有 `openspec/` 目录，并且诸如 `openspec new change` 之类的写入会创建一个作为副作用。该命令也以非零值退出，这是答案而不是损坏的 CLI，因此请阅读 JSON 而不是重试或解决它。

其中一个 `"root": null` 与设置无关：当 `status` 错误消息以 `Declared in` 或 `Invalid store declaration in` 开头并命名该项目的 `openspec/config.yaml`（或 `config.yml`）时，该项目确实通过存储使用 OpenSpec声明，本机无法解析（商店未注册，或 `store:` 行格式错误）。不要将其视为未初始化并跳过以下分支：在写入之前停止并向用户显示错误为 `message` 和 `fix`。

否则，在没有 root 的情况下，接下来发生的情况取决于如何实现此工作流程：

- **自动选择**：您自己选择此工作流程，无需用户命名OpenSpec、命名此技能或运行其斜线命令。停止使用 OpenSpec 并正常应答请求，就像未安装 OpenSpec 时一样。不要要求他们设置任何内容，也不要提及 OpenSpec 设置。
- **显式OpenSpec请求**：名为OpenSpec的用户，命名了该技能，或运行了其斜线命令。在写作之前停下来，询问如何继续：设置此项目 (`openspec init`)，定位他们已有的商店 (`--store <id>`)，或者在没有 OpenSpec 的情况下继续此请求。等待他们的答复。

在这两个分支中，永远不要创建根作为副作用：在用户要求之前不要运行 `openspec init`，不要手动创建 `openspec/` 文件，并且不要让命令创建它。

---

## 预检

开始之前，检查是否安装了OpenSpecCLI：

```bash
# Unix/macOS
openspec --version 2>&1 || echo "CLI_NOT_INSTALLED"
# Windows (PowerShell)
# if (Get-Command openspec -ErrorAction SilentlyContinue) { openspec --version } else { echo "CLI_NOT_INSTALLED" }
```

**如果未安装CLI：**
> OpenSpecCLI未安装。先安装，然后再回到`/openspec-onboard`。

如果未安装，请在此停止。

---

## 阶段1：欢迎

显示：

```
## Welcome to OpenSpec!

I'll walk you through a complete change cycle—from idea to implementation—using a real task in your codebase. Along the way, you'll learn the workflow by doing it.

**What we'll do:**
1. Pick a small, real task in your codebase
2. Explore the problem briefly
3. Create a change (the container for our work)
4. Build the artifacts: proposal → specs → design → tasks
5. Implement the tasks
6. Archive the completed change

**Time:** ~15-20 minutes

Let's start by finding something to work on.
```

---

## 阶段2：任务选择

### 代码库分析

扫描代码库以寻找小的改进机会。寻找：

1. **TODO/FIXME 注释** - 在代码文件中搜索 `TODO`、`FIXME`、`HACK`、`XXX`
2. **缺少错误处理** - `catch` 吞没错误的块，没有 try-catch 的危险操作
3. **没有测试的函数** - 与测试目录的交叉引用`src/`
4. **类型问题** - TypeScript 文件中的 `any` 类型（`: any`、`as any`）
5. **调试工件** - 非调试代码中的`console.log`、`console.debug`、`debugger` 语句
6. **缺少验证** - 未经验证的用户输入处理程序

另请检查最近的 git 活动：
```bash
# Unix/macOS
git log --oneline -10 2>/dev/null || echo "No git history"
# Windows (PowerShell)
# git log --oneline -10 2>$null; if ($LASTEXITCODE -ne 0) { echo "No git history" }
```

### 提出建议

根据您的分析，提出3-4具体建议：

```
## Task Suggestions

Based on scanning your codebase, here are some good starter tasks:

**1. [Most promising task]**
   Location: `src/path/to/file.ts:42`
   Scope: ~1-2 files, ~20-30 lines
   Why it's good: [brief reason]

**2. [Second task]**
   Location: `src/another/file.ts`
   Scope: ~1 file, ~15 lines
   Why it's good: [brief reason]

**3. [Third task]**
   Location: [location]
   Scope: [estimate]
   Why it's good: [brief reason]

**4. Something else?**
   Tell me what you'd like to work on.

Which task interests you? (Pick a number or describe your own)
```

**如果没有找到：**回过头来询问用户想要构建什么：
> 我在您的代码库中没有发现明显的快速胜利。您一直想添加或修复什么小东西？

### 适用范围护栏

如果用户选择或描述的内容太大（主要功能、多天工作）：

```
That's a valuable task, but it's probably larger than ideal for your first OpenSpec run-through.

For learning the workflow, smaller is better—it lets you see the full cycle without getting stuck in implementation details.

**Options:**
1. **Slice it smaller** - What's the smallest useful piece of [their task]? Maybe just [specific slice]?
2. **Pick something else** - One of the other suggestions, or a different small task?
3. **Do it anyway** - If you really want to tackle this, we can. Just know it'll take longer.

What would you prefer?
```

如果用户坚持的话，就让他们覆盖——这是一个软护栏。

---

## 阶段 3：探索演示

选择任务后，简要演示探索模式：

```
Before we create a change, let me quickly show you **explore mode**—it's how you think through problems before committing to a direction.
```

花费1-2分钟调查相关代码：
- 读取涉及的文件
- 如果有帮助的话，快速绘制一个 ASCII 图表
- 注意任何注意事项

```
## Quick Exploration

[Your brief analysis—what you found, any considerations]

┌─────────────────────────────────────────┐
│   [Optional: ASCII diagram if helpful]  │
└─────────────────────────────────────────┘

Explore mode (`/openspec-explore`) is for this kind of thinking—investigating before implementing. You can use it anytime you need to think through a problem.

Now let's create a change to hold our work.
```

**PAUSE** - 等待用户确认后再继续。

---

## 阶段 4：创造改变

**EXPLAIN:**
```
## Creating a Change

A "change" in OpenSpec is a container for all the thinking and planning around a piece of work. It lives at the `changeRoot` reported by `openspec status --change "<name>" --json` and holds your artifacts—proposal, specs, design, tasks.

Let me create one for our task.
```

**DO:** 使用派生的短横线大小写名称创建更改：
```bash
openspec new change "<derived-name>"
```

**SHOW:**
```
Created: <changeRoot from status JSON>

The folder structure:
```
<changeRoot>/
├── proposal.md ← 我们为什么要这样做（空，我们会填满它）
├── design.md ← 我们将如何构建它（空）
├──规格/ ← 详细要求（空）
└── tasks.md ← 实施清单（空）
```

Now let's fill in the first artifact—the proposal.
```

---

## 阶段5：提案

**EXPLAIN:**
```
## The Proposal

The proposal captures **why** we're making this change and **what** it involves at a high level. It's the "elevator pitch" for the work.

I'll draft one based on our task.
```

**DO:** 起草提案内容（暂不保存）：

`<capability-path>` 是相对于 `specs/` 的规范目录（例如，
`user-auth` 或 `identity/user-auth`）。使用修改后的
功能的确切现有路径。对于新功能，请遵循项目的既定规范
组织。

```
Here's a draft proposal:

---

# Proposal

## Why

[1-2 sentences explaining the problem/opportunity]

## What Changes

[Bullet points of what will be different]

## Capabilities

### New Capabilities
- `<capability-path>`: [brief description]

### Modified Capabilities
<!-- If modifying existing behavior -->
- `<existing-capability-path>`: [brief description]

## Impact

- `src/path/to/file.ts`: [what changes]
- [other files if applicable]

---

Does this capture the intent? I can adjust before we save it.
```

**PAUSE** - 等待用户 approval/feedback.

批准后，保存提案：
```bash
openspec instructions proposal --change "<name>" --json
```
然后将`openspec instructions proposal --change "<name>" --json`中的内容写入到`resolvedOutputPath`中。

```
Proposal saved. This is your "why" document—you can always come back and refine it as understanding evolves.

Next up: specs.
```

---

## 相位 6：规格

**EXPLAIN:**
```
## Specs

Specs define **what** we're building in precise, testable terms. They use a requirement/scenario format that makes expected behavior crystal clear.

For a small task like this, we might only need one spec file.
```

**DO:** 解析应在何处创建规范文件：
```bash
openspec instructions specs --change "<name>" --json
# Use resolvedOutputPath from the JSON. If it is a glob, choose the concrete file path using the schema instruction and the change's context.
```

起草规范内容：

```
Here's the spec:

---

# Spec Delta

## ADDED Requirements

### Requirement: <Name>

<Description of what the system should do>

#### Scenario: <Scenario name>

- **WHEN** <trigger condition>
- **THEN** <expected outcome>
- **AND** <additional outcome if needed>

---

This format—WHEN/THEN/AND—makes requirements testable. You can literally read them as test cases.
```

保存到`resolvedOutputPath`中选择的具体文件路径。

---

## 阶段7：设计

**EXPLAIN:**
```
## Design

The design captures **how** we'll build it—technical decisions, tradeoffs, approach.

For small changes, this might be brief. That's fine—not every change needs deep design discussion.
```

**DO：**草稿design.md：

```
Here's the design:

---

# Design

## Context

[Brief context about the current state]

## Goals / Non-Goals

**Goals:**
- [What we're trying to achieve]

**Non-Goals:**
- [What's explicitly out of scope]

## Decisions

### Decision 1: [Key decision]

[Explanation of approach and rationale]

---

For a small task, this captures the key decisions without over-engineering.
```

从`openspec instructions design --change "<name>" --json`保存到`resolvedOutputPath`。

---

## 阶段 8：任务

**EXPLAIN:**
```
## Tasks

Finally, we break the work into implementation tasks—checkboxes that drive the apply phase.

These should be small, clear, and in logical order. Each group carries the tests and documentation for its own work - the last group is only for integration checks.
```

**DO:** 根据规格和设计生成任务：

```
Here are the implementation tasks:

---

# Tasks

## 1. [Category or file]

- [ ] 1.1 [Specific task] — verify: [test, command, observable behavior, or delivered artifact]
- [ ] 1.2 [Specific task] — verify: [test, command, observable behavior, or delivered artifact]

## 2. Integration Verification

- [ ] 2.1 Verify [broader integration or system behavior] with [end-to-end test or observable result]

---

Each checkbox becomes a unit of work in the apply phase. Does this task breakdown look right?
```

**PAUSE** - 等待用户 approval/feedback.

审核通过后，从`openspec instructions tasks --change "<name>" --json`保存到`resolvedOutputPath`。

然后问：
> “任务已保存，准备实施吗？”

**PAUSE** - 等待用户确认后再执行。

---

## 阶段9：申请（实施）

**EXPLAIN:**
```
## Implementation

Now we implement each task, checking them off as we go. I'll announce each one and occasionally note how the specs/design informed the approach.
```

**DO:** 对于每个任务：

1. 公告：“正在处理任务 N：[描述]”
2. 实施代码库中的更改
3. 自然地参考specs/design：“规范说X，所以我做Y”
4. 在 tasks.md 中标记完成：`- [ ]` → `- [x]`
5. 简要状态：“✓ 任务 N 完成”

保持叙述轻松——不要过度解释每一行代码。

完成所有任务后：

```
## Implementation Complete

All tasks done:
- [x] Task 1
- [x] Task 2
- [x] ...

The change is implemented! One more step—let's archive it.
```

---

## 阶段 10：存档

**EXPLAIN:**
```
## Archiving

When a change is complete, we archive it. The archive path is derived from `planningHome.changesDir` and the date.

Archived changes become your project's decision history—you can always find them later to understand why something was built a certain way.
```

**DO:** 存档更改（`--yes` 回答确认提示，您无法从工具调用中回答）：
```bash
openspec archive "<name>" --yes
```

**SHOW:**
```
Archived to: `<planningHome.changesDir>/archive/<target-name>/` (the target name prepends today's date, unless the name already starts with a `YYYY-MM-DD-` prefix — then it is kept as-is, no second date)

The change is now part of your project's history. The code is in your codebase, the decision record is preserved.
```

---

## 阶段 11：回顾和后续步骤

```
## Congratulations!

You just completed a full OpenSpec cycle:

1. **Explore** - Thought through the problem
2. **New** - Created a change container
3. **Proposal** - Captured WHY
4. **Specs** - Defined WHAT in detail
5. **Design** - Decided HOW
6. **Tasks** - Broke it into steps
7. **Apply** - Implemented the work
8. **Archive** - Preserved the record

This same rhythm works for any size change—a small fix or a major feature.

---

## Command Reference

**The commands you have installed:**

 | Command          | What it does                               |
 |------------------|--------------------------------------------|
 | `/openspec-propose` | Create a change and generate all artifacts |
 | `/openspec-explore` | Think through problems before/during work  |
 | `/openspec-apply-change`   | Implement tasks from a change              |
 | `/openspec-archive-change` | Archive a completed change                 |
 | `/openspec-new-change`     | Start a new change, one artifact at a time |
 | `/openspec-continue-change` | Continue working on an existing change    |
 | `/openspec-ff-change`      | Fast-forward: create all artifacts at once |
 | `/openspec-verify-change`  | Verify implementation matches artifacts    |

---

## What's Next?

Try `/openspec-propose` on something you actually want to build. You've got the rhythm now!
```

---

## 优雅的退出处理

### 用户想要中途停止

如果用户说他们需要停止、想要暂停或看起来心不在焉：

```
No problem! Your change is saved at the `changeRoot` reported by `openspec status --change "<name>" --json`.

To pick up where we left off later, `openspec status --change "<name>" --json` shows exactly where the change stands.
- `/openspec-continue-change <name>` - Resume artifact creation
- `/openspec-apply-change <name>` - Jump to implementation (if tasks exist)

The work won't be lost. Come back whenever you're ready.
```

优雅退出，毫无压力。

### 用户只需要命令参考

如果用户说他们只想查看命令或跳过教程：

```
## OpenSpec Quick Reference

**The commands you have installed:**

 | Command                  | What it does                               |
 |--------------------------|--------------------------------------------|
 | `/openspec-propose <name>`  | Create a change and generate all artifacts |
 | `/openspec-explore`         | Think through problems (no code changes)   |
 | `/openspec-apply-change <name>`    | Implement tasks                            |
 | `/openspec-archive-change <name>`  | Archive when done                          |
 | `/openspec-new-change <name>`      | Start a new change, step by step           |
 | `/openspec-continue-change <name>` | Continue an existing change                |
 | `/openspec-ff-change <name>`       | Fast-forward: all artifacts at once        |
 | `/openspec-verify-change <name>`   | Verify implementation                      |

Try `/openspec-propose` to start your first change.
```

优雅地退出。

---

## 护栏

- **在关键转换处遵循 EXPLAIN → DO → SHOW → PAUSE 模式 **（探索后、提案草稿后、任务后、存档后）
- **实施过程中保持旁白轻松**——有教无类
- **即使变化很小，也不要跳过阶段**——目标是教授工作流程
- **在标记点暂停以进行确认**，但不要过度暂停
- **句柄优雅退出**—永远不要强迫用户继续
- **使用真实的代码库任务** - 不要模拟或使用虚假示例
- **轻轻调整范围**—指导较小的任务但尊重用户的选择


## 原文对照

<details>
<summary>展开完整原文</summary>

Guide the user through their first complete OpenSpec workflow cycle. This is a teaching experience—you'll do real work in their codebase while explaining each step.

**Store selection:** If the user names a store (a store is a standalone OpenSpec repo registered on this machine) or the work lives in one, run `openspec store list --json` to discover registered store ids, then pass `--store <id>` on the commands that read or write specs and changes (`new change`, `status`, `instructions`, `list`, `show`, `validate`, `archive`, `doctor`, `context`, `schemas`, `view`). Once selected, treat `--store <id>` as sticky for the rest of the workflow. Every unscoped example of those commands below is shorthand: before running it, append the flag. For example, run `openspec status --change "<name>" --json --store "<id>"`, not the unscoped form shown below. Other commands do not take the flag. Hints printed by commands already carry the flag; keep it on follow-ups. Without a store, commands act on the nearest local `openspec/` root.

**Project check:** These steps expect a project that already uses OpenSpec. Before the first step that writes anything (`new change`, `archive`, `sync specs`, or authoring an artifact file), confirm the project has a root: run `openspec list --json` (with `--store <id>` when a store is selected, since the store is then the root) and read `root`. A root object means the project is set up. `"root": null` means it is not - there is no `openspec/` directory here, and a write such as `openspec new change` would create one as a side effect. The command also exits non-zero, which is that answer rather than a broken CLI, so read the JSON instead of retrying or working around it.

One `"root": null` is not about setup: when a `status` error message starts with `Declared in` or `Invalid store declaration in` and names this project's `openspec/config.yaml` (or `config.yml`), the project does use OpenSpec through a store it declares, which this machine cannot resolve (the store is not registered, or the `store:` line is malformed). Do not treat it as uninitialized and skip the branches below: stop before writing and show the user that error's `message` and `fix`.

Otherwise, with no root, what happens next depends on how this workflow was reached:

- **Auto-selected**: you chose this workflow yourself, without the user naming OpenSpec, naming this skill, or running its slash command. Stop using OpenSpec and answer the request normally, as you would with no OpenSpec installed. Do not ask them to set anything up and do not mention OpenSpec setup.
- **Explicit OpenSpec request**: the user named OpenSpec, named this skill, or ran its slash command. Stop before writing and ask how to proceed: set this project up (`openspec init`), target a store they already have (`--store <id>`), or continue without OpenSpec for this request. Wait for their answer.

In both branches, never create the root as a side effect: do not run `openspec init` until the user asks for it, do not hand-create `openspec/` files, and do not let a command create it.

---

## Preflight

Before starting, check if the OpenSpec CLI is installed:

```bash
# Unix/macOS
openspec --version 2>&1 || echo "CLI_NOT_INSTALLED"
# Windows (PowerShell)
# if (Get-Command openspec -ErrorAction SilentlyContinue) { openspec --version } else { echo "CLI_NOT_INSTALLED" }
```

**If CLI not installed:**
> OpenSpec CLI is not installed. Install it first, then come back to `/openspec-onboard`.

Stop here if not installed.

---

## Phase 1: Welcome

Display:

```
## Welcome to OpenSpec!

I'll walk you through a complete change cycle—from idea to implementation—using a real task in your codebase. Along the way, you'll learn the workflow by doing it.

**What we'll do:**
1. Pick a small, real task in your codebase
2. Explore the problem briefly
3. Create a change (the container for our work)
4. Build the artifacts: proposal → specs → design → tasks
5. Implement the tasks
6. Archive the completed change

**Time:** ~15-20 minutes

Let's start by finding something to work on.
```

---

## Phase 2: Task Selection

### Codebase Analysis

Scan the codebase for small improvement opportunities. Look for:

1. **TODO/FIXME comments** - Search for `TODO`, `FIXME`, `HACK`, `XXX` in code files
2. **Missing error handling** - `catch` blocks that swallow errors, risky operations without try-catch
3. **Functions without tests** - Cross-reference `src/` with test directories
4. **Type issues** - `any` types in TypeScript files (`: any`, `as any`)
5. **Debug artifacts** - `console.log`, `console.debug`, `debugger` statements in non-debug code
6. **Missing validation** - User input handlers without validation

Also check recent git activity:
```bash
# Unix/macOS
git log --oneline -10 2>/dev/null || echo "No git history"
# Windows (PowerShell)
# git log --oneline -10 2>$null; if ($LASTEXITCODE -ne 0) { echo "No git history" }
```

### Present Suggestions

From your analysis, present 3-4 specific suggestions:

```
## Task Suggestions

Based on scanning your codebase, here are some good starter tasks:

**1. [Most promising task]**
   Location: `src/path/to/file.ts:42`
   Scope: ~1-2 files, ~20-30 lines
   Why it's good: [brief reason]

**2. [Second task]**
   Location: `src/another/file.ts`
   Scope: ~1 file, ~15 lines
   Why it's good: [brief reason]

**3. [Third task]**
   Location: [location]
   Scope: [estimate]
   Why it's good: [brief reason]

**4. Something else?**
   Tell me what you'd like to work on.

Which task interests you? (Pick a number or describe your own)
```

**If nothing found:** Fall back to asking what the user wants to build:
> I didn't find obvious quick wins in your codebase. What's something small you've been meaning to add or fix?

### Scope Guardrail

If the user picks or describes something too large (major feature, multi-day work):

```
That's a valuable task, but it's probably larger than ideal for your first OpenSpec run-through.

For learning the workflow, smaller is better—it lets you see the full cycle without getting stuck in implementation details.

**Options:**
1. **Slice it smaller** - What's the smallest useful piece of [their task]? Maybe just [specific slice]?
2. **Pick something else** - One of the other suggestions, or a different small task?
3. **Do it anyway** - If you really want to tackle this, we can. Just know it'll take longer.

What would you prefer?
```

Let the user override if they insist—this is a soft guardrail.

---

## Phase 3: Explore Demo

Once a task is selected, briefly demonstrate explore mode:

```
Before we create a change, let me quickly show you **explore mode**—it's how you think through problems before committing to a direction.
```

Spend 1-2 minutes investigating the relevant code:
- Read the file(s) involved
- Draw a quick ASCII diagram if it helps
- Note any considerations

```
## Quick Exploration

[Your brief analysis—what you found, any considerations]

┌─────────────────────────────────────────┐
│   [Optional: ASCII diagram if helpful]  │
└─────────────────────────────────────────┘

Explore mode (`/openspec-explore`) is for this kind of thinking—investigating before implementing. You can use it anytime you need to think through a problem.

Now let's create a change to hold our work.
```

**PAUSE** - Wait for user acknowledgment before proceeding.

---

## Phase 4: Create the Change

**EXPLAIN:**
```
## Creating a Change

A "change" in OpenSpec is a container for all the thinking and planning around a piece of work. It lives at the `changeRoot` reported by `openspec status --change "<name>" --json` and holds your artifacts—proposal, specs, design, tasks.

Let me create one for our task.
```

**DO:** Create the change with a derived kebab-case name:
```bash
openspec new change "<derived-name>"
```

**SHOW:**
```
Created: <changeRoot from status JSON>

The folder structure:
```
<changeRoot>/
├── proposal.md    ← Why we're doing this (empty, we'll fill it)
├── design.md      ← How we'll build it (empty)
├── specs/         ← Detailed requirements (empty)
└── tasks.md       ← Implementation checklist (empty)
```

Now let's fill in the first artifact—the proposal.
```

---

## Phase 5: Proposal

**EXPLAIN:**
```
## The Proposal

The proposal captures **why** we're making this change and **what** it involves at a high level. It's the "elevator pitch" for the work.

I'll draft one based on our task.
```

**DO:** Draft the proposal content (don't save yet):

`<capability-path>` is the spec directory relative to `specs/` (for example,
`user-auth` or `identity/user-auth`). Use the exact existing path for modified
capabilities. For new capabilities, follow the project's established spec
organization.

```
Here's a draft proposal:

---

# Proposal

## Why

[1-2 sentences explaining the problem/opportunity]

## What Changes

[Bullet points of what will be different]

## Capabilities

### New Capabilities
- `<capability-path>`: [brief description]

### Modified Capabilities
<!-- If modifying existing behavior -->
- `<existing-capability-path>`: [brief description]

## Impact

- `src/path/to/file.ts`: [what changes]
- [other files if applicable]

---

Does this capture the intent? I can adjust before we save it.
```

**PAUSE** - Wait for user approval/feedback.

After approval, save the proposal:
```bash
openspec instructions proposal --change "<name>" --json
```
Then write the content to the `resolvedOutputPath` from `openspec instructions proposal --change "<name>" --json`.

```
Proposal saved. This is your "why" document—you can always come back and refine it as understanding evolves.

Next up: specs.
```

---

## Phase 6: Specs

**EXPLAIN:**
```
## Specs

Specs define **what** we're building in precise, testable terms. They use a requirement/scenario format that makes expected behavior crystal clear.

For a small task like this, we might only need one spec file.
```

**DO:** Resolve where the spec file should be created:
```bash
openspec instructions specs --change "<name>" --json
# Use resolvedOutputPath from the JSON. If it is a glob, choose the concrete file path using the schema instruction and the change's context.
```

Draft the spec content:

```
Here's the spec:

---

# Spec Delta

## ADDED Requirements

### Requirement: <Name>

<Description of what the system should do>

#### Scenario: <Scenario name>

- **WHEN** <trigger condition>
- **THEN** <expected outcome>
- **AND** <additional outcome if needed>

---

This format—WHEN/THEN/AND—makes requirements testable. You can literally read them as test cases.
```

Save to the concrete file path chosen from `resolvedOutputPath`.

---

## Phase 7: Design

**EXPLAIN:**
```
## Design

The design captures **how** we'll build it—technical decisions, tradeoffs, approach.

For small changes, this might be brief. That's fine—not every change needs deep design discussion.
```

**DO:** Draft design.md:

```
Here's the design:

---

# Design

## Context

[Brief context about the current state]

## Goals / Non-Goals

**Goals:**
- [What we're trying to achieve]

**Non-Goals:**
- [What's explicitly out of scope]

## Decisions

### Decision 1: [Key decision]

[Explanation of approach and rationale]

---

For a small task, this captures the key decisions without over-engineering.
```

Save to the `resolvedOutputPath` from `openspec instructions design --change "<name>" --json`.

---

## Phase 8: Tasks

**EXPLAIN:**
```
## Tasks

Finally, we break the work into implementation tasks—checkboxes that drive the apply phase.

These should be small, clear, and in logical order. Each group carries the tests and documentation for its own work - the last group is only for integration checks.
```

**DO:** Generate tasks based on specs and design:

```
Here are the implementation tasks:

---

# Tasks

## 1. [Category or file]

- [ ] 1.1 [Specific task] — verify: [test, command, observable behavior, or delivered artifact]
- [ ] 1.2 [Specific task] — verify: [test, command, observable behavior, or delivered artifact]

## 2. Integration Verification

- [ ] 2.1 Verify [broader integration or system behavior] with [end-to-end test or observable result]

---

Each checkbox becomes a unit of work in the apply phase. Does this task breakdown look right?
```

**PAUSE** - Wait for user approval/feedback.

After approval, save to the `resolvedOutputPath` from `openspec instructions tasks --change "<name>" --json`.

Then ask:
> "Tasks are saved. Ready to implement?"

**PAUSE** - Wait for user to confirm before implementation.

---

## Phase 9: Apply (Implementation)

**EXPLAIN:**
```
## Implementation

Now we implement each task, checking them off as we go. I'll announce each one and occasionally note how the specs/design informed the approach.
```

**DO:** For each task:

1. Announce: "Working on task N: [description]"
2. Implement the change in the codebase
3. Reference specs/design naturally: "The spec says X, so I'm doing Y"
4. Mark complete in tasks.md: `- [ ]` → `- [x]`
5. Brief status: "✓ Task N complete"

Keep narration light—don't over-explain every line of code.

After all tasks:

```
## Implementation Complete

All tasks done:
- [x] Task 1
- [x] Task 2
- [x] ...

The change is implemented! One more step—let's archive it.
```

---

## Phase 10: Archive

**EXPLAIN:**
```
## Archiving

When a change is complete, we archive it. The archive path is derived from `planningHome.changesDir` and the date.

Archived changes become your project's decision history—you can always find them later to understand why something was built a certain way.
```

**DO:** Archive the change (`--yes` answers the confirmation prompts, which you cannot answer from a tool call):
```bash
openspec archive "<name>" --yes
```

**SHOW:**
```
Archived to: `<planningHome.changesDir>/archive/<target-name>/` (the target name prepends today's date, unless the name already starts with a `YYYY-MM-DD-` prefix — then it is kept as-is, no second date)

The change is now part of your project's history. The code is in your codebase, the decision record is preserved.
```

---

## Phase 11: Recap & Next Steps

```
## Congratulations!

You just completed a full OpenSpec cycle:

1. **Explore** - Thought through the problem
2. **New** - Created a change container
3. **Proposal** - Captured WHY
4. **Specs** - Defined WHAT in detail
5. **Design** - Decided HOW
6. **Tasks** - Broke it into steps
7. **Apply** - Implemented the work
8. **Archive** - Preserved the record

This same rhythm works for any size change—a small fix or a major feature.

---

## Command Reference

**The commands you have installed:**

 | Command          | What it does                               |
 |------------------|--------------------------------------------|
 | `/openspec-propose` | Create a change and generate all artifacts |
 | `/openspec-explore` | Think through problems before/during work  |
 | `/openspec-apply-change`   | Implement tasks from a change              |
 | `/openspec-archive-change` | Archive a completed change                 |
 | `/openspec-new-change`     | Start a new change, one artifact at a time |
 | `/openspec-continue-change` | Continue working on an existing change    |
 | `/openspec-ff-change`      | Fast-forward: create all artifacts at once |
 | `/openspec-verify-change`  | Verify implementation matches artifacts    |

---

## What's Next?

Try `/openspec-propose` on something you actually want to build. You've got the rhythm now!
```

---

## Graceful Exit Handling

### User wants to stop mid-way

If the user says they need to stop, want to pause, or seem disengaged:

```
No problem! Your change is saved at the `changeRoot` reported by `openspec status --change "<name>" --json`.

To pick up where we left off later, `openspec status --change "<name>" --json` shows exactly where the change stands.
- `/openspec-continue-change <name>` - Resume artifact creation
- `/openspec-apply-change <name>` - Jump to implementation (if tasks exist)

The work won't be lost. Come back whenever you're ready.
```

Exit gracefully without pressure.

### User just wants command reference

If the user says they just want to see the commands or skip the tutorial:

```
## OpenSpec Quick Reference

**The commands you have installed:**

 | Command                  | What it does                               |
 |--------------------------|--------------------------------------------|
 | `/openspec-propose <name>`  | Create a change and generate all artifacts |
 | `/openspec-explore`         | Think through problems (no code changes)   |
 | `/openspec-apply-change <name>`    | Implement tasks                            |
 | `/openspec-archive-change <name>`  | Archive when done                          |
 | `/openspec-new-change <name>`      | Start a new change, step by step           |
 | `/openspec-continue-change <name>` | Continue an existing change                |
 | `/openspec-ff-change <name>`       | Fast-forward: all artifacts at once        |
 | `/openspec-verify-change <name>`   | Verify implementation                      |

Try `/openspec-propose` to start your first change.
```

Exit gracefully.

---

## Guardrails

- **Follow the EXPLAIN → DO → SHOW → PAUSE pattern** at key transitions (after explore, after proposal draft, after tasks, after archive)
- **Keep narration light** during implementation—teach without lecturing
- **Don't skip phases** even if the change is small—the goal is teaching the workflow
- **Pause for acknowledgment** at marked points, but don't over-pause
- **Handle exits gracefully**—never pressure the user to continue
- **Use real codebase tasks**—don't simulate or use fake examples
- **Adjust scope gently**—guide toward smaller tasks but respect user choice


</details>
