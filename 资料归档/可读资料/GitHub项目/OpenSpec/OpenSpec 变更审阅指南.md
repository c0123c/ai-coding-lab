# OpenSpec 变更审阅指南

说明规划与实施两个审阅时点，按 proposal、增量规格和实施成果检查问题、完成定义与行为场景。

- 来源：OpenSpec
- 资料类型：变更审阅指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/reviewing-changes.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/reviewing-changes.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 审查变更

OpenSpec 的全部承诺是，您和您的 AI ** 在编写任何代码之前就要构建的内容达成一致。** 仅当您真正阅读了 AI 起草的内容时，该协议才有意义。此页面涉及您执行此操作的两分钟 — 打开什么、按什么顺序以及要查找什么。

赌注很简单：在一段计划中走错弯几乎是免费的。在 300 行代码中捕获相同的错误转弯则不然。回顾是您收集该赌注的地方。

## 你回顾的两个瞬间

正好有两个：

```
/opsx:propose ──► REVIEW THE PLAN ──► /opsx:apply ──► REVIEW THE CODE ──► /opsx:archive
                  (before any code)                    (/opsx:verify)
```

1. **在`/opsx:propose`**（或`/opsx:ff`）之后，在`/opsx:apply`之前——阅读计划，同时它仍然只是文字。
2. **构建**后，使用`/opsx:verify` - 检查代码是否确实按照计划执行。

第一篇评论是最能帮助你的，也是人们会跳过的。该页面大部分时间都花在那里。

## 按此顺序阅读

变化是`openspec/changes/<name>/`中的纯Markdown文件夹。按顺序阅读文件，以便在出现问题时可以尽早退出：

```
openspec/changes/add-dark-mode/
├── proposal.md      1. the intent and scope   ← if this is wrong, stop here
├── specs/…/spec.md  2. the requirements       ← the heart of the review
├── design.md        (only for bigger changes) — the technical approach
└── tasks.md         3. the plan of work
```

您不需要阅读每一行。您需要回答三个问题，每个文件一个。

## 建议：这是正确的问题吗？

先打开【`proposal.md`】。它用一两段话来描述“为什么”和“什么”——意图、范围、方法。

**什么是好的：**一个明确的意图，一个你认可的范围，以及一个值得现在做的原因。

**危险信号：**

- 它稍微解决了 *不同的* 问题比你要求的问题还要多。
- 范围已经扩大 - 您要求主题切换，并且该提案还涉及“当我们在那里时”的验证。
- 这很模糊。 “完善设置页面”不是一个范围； “添加一个尊重 OS 首选项的暗模式切换”是。

**要回答的问题：** *这是否符合我实际要求的内容，是否有什么潜入的内容？*如果答案是否定的，请停止 - 不要进一步阅读，修复提案（请参阅[推动返回](<#反击很便宜>)）。

## 规格增量：“完成”定义正确吗？

这是审查的核心。 `specs/` 下的增量规范说明了变更发布时的 *true* — 作为要求和证明这些要求的场景：

```markdown
## ADDED Requirements

### Requirement: Dark Mode Toggle
The system SHALL let a user switch between light and dark themes.

#### Scenario: Respects the OS preference on first load
- GIVEN a user who has never set a theme
- WHEN they open the app on a device set to dark mode
- THEN the app renders in dark mode
```

**好的需求是什么样的：**一个可以交给测试人员的清晰的`SHALL`/`MUST`声明，以及至少一个GIVEN/WHEN/THEN实际执行该声明的场景。

**危险信号：**

- **模糊的要求。**“系统SHALL要快”无法构建或测试。什么是快？
- **没有场景的需求**，或者没有测试其所在需求的场景。
- **最有价值的收获：缺少什么。** AI忠实地写下你*所说的*。你的工作是注意你*忘记*说的话。如果您最关心 OS- 偏好案例并且没有场景提及它，那么这就是值得的评论。

阅读增量询问*如果系统完全做到这一点，并且只做到这一点，我会感到高兴吗？*这里还没有关于代码的内容，所以更改起来很便宜。

## 任务：工作计划是否合理？

最后打开`tasks.md`。这是 AI 将要完成的实施清单。

** 看起来不错：** 有序的步骤，每个步骤都可追溯到一个需求，没有什么神秘的。

**危险信号：**

- 没有匹配要求的任务（这是从哪里来的？）。
- 一项巨大的“实现功能”任务隐藏了所有真正的决策。
- 涉及超出您刚刚批准的范围的任务。

您在这里不是进行估计或微观管理 - 您是在检查该计划是否符合您已经接受的要求。

## 反击很便宜

如果这三个问题中有任何一个回答错误，请说出来。没有任何阶段，也没有什么是锁定的——你修复它并继续前进。两种方式，与[编辑更改](<OpenSpec 变更工件的编辑与迭代.md>)中完全相同：

- **自己编辑文件。**这是普通的Markdown；更改范围线、收紧要求、删除任务。
- **告诉AI出了什么问题**并让它修改：*“删除身份验证更改 - 超出范围，”* *“添加用户已经选择的场景主题，“* *”将任务 3 拆分为架构和 UI。“*

然后重新阅读您更改的部分。重新起草，直到成为您可以签名的计划。来回的*是*产品的工作。

## 代码后：验证

作品完成后，`/opsx:verify` 是您的第二次评论。它重新读取工件和代码并报告三个维度的不匹配情况：

|尺寸|它检查什么|
|-----------|----------------|
|**完整性**|每项任务完成，每项要求实现，涵盖的场景|
|**正确性**|实现符合规范的意图，处理了边缘情况|
|**相干性**|设计决策实际上显示在代码中|

```
You: /opsx:verify

AI:  Verifying add-dark-mode...

     COMPLETENESS
     ✓ All 8 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "Respects the OS preference on first load" has no test coverage
```

它将问题标记为 CRITICAL、WARNING 或 SUGGESTION，并且它会 ** 而不是 ** 阻止归档 — 它会显示差距并将呼叫留给您。这就是“AI 是否编写了代码”和“它是否构建了我们同意的内容”之间的区别。

`/opsx:verify` 位于扩展配置文件中。如果没有，请使用 `openspec config profile`（然后是 `openspec update`）将其打开，或者自己重新阅读更改和差异。

## 调整评论的大小

并非所有更改都能获得完整通行证。一个文件的拼写错误修复值得花二十秒浏览一下。涉及身份验证、付款或无法恢复的数据的更改值得回答上述每个问题。重点从来都不是仪式——而是把你的注意力花在错误会代价高昂的地方，而略过不会犯错误的地方。

## 两分钟清单

- [ ] 该提案的意图与我的要求相符。
- [ ] 没有任何额外的内容进入范围。
- [ ] 每个要求都足够具体以进行测试。
- [ ] 每个需求都有一个实际执行它的场景。
- [ ] 我最关心的案例被覆盖了。
- [ ] 任务映射到要求；没有什么是神秘的或超出范围的。
- [ ] 如果 AI 正是构建了这个，仅此而已，我会感到很舒服。

如果七个都通过了，请放心运行 `/opsx:apply`。如果有任何失败，那也不是挫折——而是两分钟的时间完成了它的工作。

## 下一步去哪里

- [编写好的规范](<编写清晰的 OpenSpec 行为规格.md>) — 另一方面：如何起草值得批准的需求和场景。
- [编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md>) — 开始后更改计划的机制。
- [工作流程](<OpenSpec 快速与扩展工作流.md>) — 审查适合更大的循环。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Reviewing a Change

OpenSpec's whole promise is that you and your AI **agree on what to build before any code is written.** That agreement only means something if you actually read what the AI drafted. This page is about the two minutes where you do that — what to open, in what order, and what to look for.

The bet is simple: catching a wrong turn in a one-paragraph plan is nearly free. Catching the same wrong turn in 300 lines of code is not. Review is where you collect on that bet.

## The two moments you review

There are exactly two:

```
/opsx:propose ──► REVIEW THE PLAN ──► /opsx:apply ──► REVIEW THE CODE ──► /opsx:archive
                  (before any code)                    (/opsx:verify)
```

1. **After `/opsx:propose`** (or `/opsx:ff`), before `/opsx:apply` — read the plan while it's still just words.
2. **After building**, with `/opsx:verify` — check that the code actually did what the plan said.

The first review is the one that saves you the most, and the one people skip. This page spends most of its time there.

## Read it in this order

A change is a folder of plain Markdown in `openspec/changes/<name>/`. Read the files in the order that lets you quit earliest if something's wrong:

```
openspec/changes/add-dark-mode/
├── proposal.md      1. the intent and scope   ← if this is wrong, stop here
├── specs/…/spec.md  2. the requirements       ← the heart of the review
├── design.md        (only for bigger changes) — the technical approach
└── tasks.md         3. the plan of work
```

You don't need to read every line. You need to answer three questions, one per file.

## The proposal: is this the right problem?

Open `proposal.md` first. It captures the "why" and "what" — the intent, the scope, the approach in a paragraph or two.

**What good looks like:** one clear intent, a scope you recognize, and a reason this is worth doing now.

**Red flags:**

- It solves a slightly *different* problem than the one you asked for.
- The scope has grown — you asked for a theme toggle and the proposal also touches auth "while we're in there."
- It's vague. "Improve the settings page" is not a scope; "add a dark-mode toggle that respects the OS preference" is.

**The question to answer:** *Does this match what I actually asked for, and is anything sneaking in?* If the answer is no, stop — don't read further, fix the proposal (see [Pushing back](<#pushing-back-is-cheap>)).

## The spec deltas: is "done" defined correctly?

This is the heart of the review. The delta specs under `specs/` say what will be *true* when the change ships — as requirements and the scenarios that prove them:

```markdown
## ADDED Requirements

### Requirement: Dark Mode Toggle
The system SHALL let a user switch between light and dark themes.

#### Scenario: Respects the OS preference on first load
- GIVEN a user who has never set a theme
- WHEN they open the app on a device set to dark mode
- THEN the app renders in dark mode
```

**What a good requirement looks like:** one clear `SHALL`/`MUST` statement you could hand to a tester, and at least one scenario whose GIVEN/WHEN/THEN actually exercises that statement.

**Red flags:**

- **A vague requirement.** "The system SHALL be fast" can't be built or tested. What's fast?
- **A requirement with no scenario**, or a scenario that doesn't test the requirement it sits under.
- **The most valuable catch of all: what's missing.** The AI faithfully writes down what you *said*. Your job is to notice what you *forgot* to say. If you cared most about the OS-preference case and no scenario mentions it, that's the review paying for itself.

Read the deltas asking *would I be happy if the system did exactly — and only — this?* Nothing here is about code yet, so it stays cheap to change.

## The tasks: is the plan of work sane?

Open `tasks.md` last. It's the implementation checklist the AI will work through.

**What good looks like:** ordered steps, each traceable to a requirement, nothing mysterious.

**Red flags:**

- A task with no matching requirement (where did that come from?).
- One giant "implement the feature" task that hides all the real decisions.
- A task that touches something outside the scope you just approved.

You're not estimating or micromanaging here — you're checking that the plan matches the requirements you already accepted.

## Pushing back is cheap

If any of the three questions came back wrong, say so. There are no phases and nothing is locked — you fix it and move on. Two ways, exactly as in [Editing a change](<OpenSpec 变更工件的编辑与迭代.md>):

- **Edit the file yourself.** It's plain Markdown; change the scope line, tighten a requirement, delete a task.
- **Tell the AI what's wrong** and let it revise: *"drop the auth changes — out of scope,"* *"add a scenario for when the user has already picked a theme,"* *"split task 3 into schema and UI."*

Then re-read the part you changed. Re-draft until it's a plan you'd sign your name to. That back-and-forth *is* the product working.

## After the code: verify

Once the work is built, `/opsx:verify` is your second review. It re-reads the artifacts and the code and reports mismatches across three dimensions:

| Dimension | What it checks |
|-----------|----------------|
| **Completeness** | Every task done, every requirement implemented, scenarios covered |
| **Correctness** | The implementation matches the spec's intent, edge cases handled |
| **Coherence** | Design decisions actually show up in the code |

```
You: /opsx:verify

AI:  Verifying add-dark-mode...

     COMPLETENESS
     ✓ All 8 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "Respects the OS preference on first load" has no test coverage
```

It flags issues as CRITICAL, WARNING, or SUGGESTION, and it does **not** block archiving — it surfaces the gaps and leaves the call to you. This is the difference between "did the AI write code" and "did it build what we agreed."

`/opsx:verify` is in the expanded profile. If you don't have it, turn it on with `openspec config profile` (then `openspec update`), or just re-read the change and the diff yourself.

## Right-size the review

Not every change earns the full pass. A one-file typo fix deserves a twenty-second skim. A change that touches auth, payments, or data you can't recover deserves every question above. The point was never ceremony — it's spending your attention where a mistake would be expensive, and skimming where it wouldn't.

## The two-minute checklist

- [ ] The proposal's intent matches what I asked for.
- [ ] Nothing extra has crept into the scope.
- [ ] Every requirement is specific enough to test.
- [ ] Every requirement has a scenario that actually exercises it.
- [ ] The case I care about most is covered.
- [ ] Tasks map to requirements; nothing is mysterious or out of scope.
- [ ] I'd be comfortable if the AI built exactly this and nothing more.

If all seven pass, run `/opsx:apply` with confidence. If any fail, that's not a setback — it's the two minutes doing its job.

## Where to go next

- [Writing Good Specs](<编写清晰的 OpenSpec 行为规格.md>) — the flip side: how to draft requirements and scenarios worth approving.
- [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md>) — the mechanics of changing a plan after you've started.
- [Workflows](<OpenSpec 快速与扩展工作流.md>) — where review fits in the larger loop.


</details>
