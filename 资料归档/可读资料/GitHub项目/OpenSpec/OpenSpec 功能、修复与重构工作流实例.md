# OpenSpec 功能、修复与重构工作流实例

通过小功能、缺陷修复、预先探索、并行变更、重构和分步控制等完整示例展示命令与工件如何配合。

- 来源：OpenSpec
- 资料类型：工作流示例与操作配方
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/examples.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/examples.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 示例和食谱

真正的改变，从开始到结束。每个配方都会显示您要输入的命令以及您会看到的内容，因此您可以将您的情况与模式相匹配并复制它。这些使用默认的 **core** 命令（`propose`、`explore`、`apply`、`update`、`sync`、`archive`）；值得注意的是，扩展集有帮助。

开始前提醒：像 `/opsx:propose` 这样的斜杠命令会进入你的 **AI 助手的聊天室 **，而 `openspec` 命令会进入你的 ** 终端 **。如果这是新内容，请先阅读 [命令如何工作](<OpenSpec 两类命令如何协作.md>)。在下面的文字记录中，`You:` 和 `AI:` 是聊天，以 `$` 开头的行是终端。

> **还不确定你在构建什么？**如果你首先从`/opsx:explore`开始思考，大多数这些食谱都会更清晰。 [食谱3](<#食谱3在提交之前探索>)展示了它的实际效果，[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)指南提供了完整的案例。

## 配方1：一个小功能，快速路径

**何时使用它：**你知道你想要什么，而且它是一个包含的作品。这是最常见的食谱。

整个事情就是三个命令。提出、构建、归档。

```text
You: /opsx:propose add-logout-button

AI:  Created openspec/changes/add-logout-button/
     ✓ proposal.md — why and what
     ✓ specs/      — the new requirement and its scenarios
     ✓ design.md   — technical approach
     ✓ tasks.md    — implementation checklist
     Ready for implementation. Run /opsx:apply.
```

现在阅读该计划。打开提案和增量规范。这就是 OpenSpec 的构建目的：在它仍然是一个段落而不是 400 行代码时捕获错误的假设。如果出现问题，请直接编辑任何工件，然后继续。

```text
You: /opsx:apply

AI:  Working through tasks...
     ✓ 1.1 Add logout button to header
     ✓ 1.2 Clear session on click
     ✓ 1.3 Add confirmation dialog
     All tasks complete!

You: /opsx:archive

AI:  Archiving add-logout-button...
     ✓ Merged specs into openspec/specs/auth/spec.md
     ✓ Moved to openspec/changes/archive/2026-06-22-add-logout-button/
     Done. Ready for the next change.
```

就是这样。注销行为现在是您规范的一部分，并且更改将与其完整上下文一起归档。

## 配方2：错误修复

**何时使用它：**某些东西被破坏了，并且您希望将修复记录为有意的行为更改，而不是神秘的提交。

错误修复的工作方式与功能完全相同。不同之处在于如何构建提案：描述*正确的*行为，而不仅仅是“修复错误”。

```text
You: /opsx:propose fix-login-redirect-loop

AI:  Created openspec/changes/fix-login-redirect-loop/
     The proposal captures the current broken behavior and the
     intended behavior. The delta spec MODIFIES the redirect
     requirement so it's now precise and testable.
     Ready for implementation.
```

由于修复是在新场景中作为 `MODIFIED` 要求实现的，因此下一个人（或下一个 AI 会话）不仅会看到您修复了它，还会看到“正确”的含义。然后像往常一样`/opsx:apply`和`/opsx:archive`。

提示：对于修复，一个好的方案是散文中的回归测试。 “GIVEN 已注销的用户，WHEN 他们提交了有效凭据，THEN 他们登陆仪表板并且不会再次重定向。”这么写，实施就有了明确的目标。

## 食谱3：在提交之前探索

**何时使用它：**您有问题但尚未制定计划。您不确定要构建什么，或者哪种方法是正确的。

以 `/opsx:explore` 开头。这是一个没有结构的思考伙伴。它从不编写代码，也不会编写任何其他内容，除非您要求它捕获您的决定，或者在它提供时说“是”。它会读取您的代码库并帮助您做出决定。

```text
You: /opsx:explore

AI:  What would you like to explore?

You: Page loads feel slow but I don't know where the time goes.

AI:  Let me look... I found three likely culprits:
     1. Large unoptimized images on the product list
     2. Synchronous data fetching that blocks render
     3. Re-renders triggered by context changes
     Which should we tackle first?

You: The data fetching one.

You: /opsx:propose optimize-product-list-fetching

AI:  Created the change, with a proposal informed by what we just
     found. Ready for implementation.
```

在你做出改变之前*，探索会澄清你的想法*。当洞察力具体化时，提出建议，AI 就会将上下文向前推进。

## 配方4：同时兼顾两个变化

**何时使用它：**您处于中等功能，紧急修复需要插队。

更改是独立的文件夹，因此并行工作不会发生冲突。开始修复、发布它，然后返回到您上次中断的位置。

```text
You: I'm partway through add-dark-mode, but I need to fix a login bug now.

You: /opsx:propose fix-login-redirect

AI:  Created openspec/changes/fix-login-redirect/. Ready for implementation.

You: /opsx:apply

AI:  Implemented the fix. All tasks complete.

You: /opsx:archive

AI:  ✓ Archived fix-login-redirect.

You: Back to dark mode.

You: /opsx:apply add-dark-mode

AI:  Resuming add-dark-mode. Picking up at task 2.3: Update Header...
```

在 `/opsx:apply add-dark-mode` 中命名更改是当多个更改处于活动状态时将 AI 指向特定更改的方式。由于任务在 `tasks.md` 中跟踪完成情况，因此 AI 确切地知道您停止的位置。

当同时完成多项更改时，扩展的 `/opsx:bulk-archive` 将它们归档在一起，并通过检查实际实现的内容来解决规范冲突。请参阅[工作流程](<OpenSpec 快速与扩展工作流.md#并行变化>)。

## 配方5：没有行为改变的重构

**何时使用它：**您正在重组代码，外部可见的行为应该保持相同。

这是一个有趣的情况，因为纯粹的重构*没有任何东西可以添加到您的规范中*。行为契约不变；只有实现才可以。因此，工作存在于设计和任务中，规格增量是空的或不存在的。

```text
You: /opsx:propose refactor-payment-module

AI:  Created the change. The proposal states the goal (split the
     payment module, no behavior change) and the design captures
     the new structure. No spec changes, since behavior is identical.
     Ready for implementation.
```

通过在更改的 `.openspec.yaml` 中设置 `skip_specs: true` 显式声明空增量：

```yaml
schema: spec-driven
skip_specs: true
```

如果没有标记，`openspec validate` 会拒绝零增量的更改（因此仍然会捕获被遗忘的规格阶段）；有了它，验证就会通过，并且 `openspec status` 将规范阶段显示为显式跳过而不是待定。如果重构最终改变了行为，请从 `.openspec.yaml` 中删除 `skip_specs` 并写入增量规范 - 验证将标记与规范文件视为冲突，因此过时的标记不能默默地徘徊。

归档标记的更改不需要额外的标志（没有要合并的增量）。独立地，`--skip-specs` 标志告诉终端命令显式跳过规范步骤：

```bash
$ openspec archive refactor-payment-module --skip-specs
```

相同的标志对于工具、CI 和仅文档更改很方便。原则：规范描述行为，因此如果行为没有改变，规范也不应该改变。请参阅[概念](<OpenSpec 核心概念与规格结构.md#规格是什么和不是什么>)。

## 配方6：逐步控制（扩展命令）

**何时使用它：**复杂或有风险的更改，您需要在继续之前检查每个工件。

核心`/opsx:propose`一次性起草所有内容。当您希望一次执行一步时，请打开扩展命令：

```bash
$ openspec config profile      # select the expanded workflows
$ openspec update              # apply them to this project
```

现在您可以逐步构建和构建：

```text
You: /opsx:new add-2fa

AI:  Created openspec/changes/add-2fa/. Ready to create: proposal.

You: /opsx:continue

AI:  Created proposal.md. Now available: specs, design.

You: /opsx:continue

AI:  Created specs/auth/spec.md. Now available: design.
```

审查每个工件，自由编辑，并在您满意时继续。当您希望一次性起草其余内容时，`/opsx:ff` 会快进剩余的规划工件。在归档之前，`/opsx:verify` 检查实现是否确实符合规范。请参阅[工作流程](<OpenSpec 快速与扩展工作流.md#opsxff-与-opsxcontinue>)。

## 食谱7：动手学习整个循环

**何时使用它：**您已经安装了OpenSpec并且想要*感受*您自己的代码的工作流程，而不是玩具示例。

打开扩展命令（参见配方6），然后：

```text
You: /opsx:onboard

AI:  Welcome to OpenSpec! I'll walk you through a complete change
     using your actual codebase. Let me scan for a small, safe
     improvement we can make together...
```

`/opsx:onboard` 找到一个真正的（小）改进，为其创建更改，实施它，并将其存档，叙述每一步。需要 15 到 30 分钟，您就可以保留或放弃真正的零钱。这是最温和的学习方式。请参阅[命令](<OpenSpec AI 聊天命令参考.md#opsxonboard>)。

## 从终端检查您的工作

您可以随时从终端检查事物的状态：

```bash
$ openspec list                      # active changes
$ openspec show add-dark-mode        # one change in detail
$ openspec validate add-dark-mode    # check structure
$ openspec view                      # interactive dashboard
```

这些是读取和检查工具。提议和构建仍然通过聊天中的斜线命令进行。完整详细信息请参见 [CLI 参考文献 ](<OpenSpec 命令行参考.md>)。

## 下一步去哪里

- [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)：当您不确定时推荐的开始方式
- [工作流程](<OpenSpec 快速与扩展工作流.md>)：上述模式，以及何时使用每种模式的决策指导
- [命令](<OpenSpec AI 聊天命令参考.md>)：每个斜杠命令的详细信息
- [入门](<OpenSpec 入门与首个变更.md>)：规范的首次更改演练
- [概念](<OpenSpec 核心概念与规格结构.md>)：为什么各个部分以它们的方式组合在一起


## 原文对照

<details>
<summary>展开完整原文</summary>

# Examples & Recipes

Real changes, start to finish. Each recipe shows the commands you'd type and what you'd see back, so you can match your situation to a pattern and copy it. These use the default **core** commands (`propose`, `explore`, `apply`, `update`, `sync`, `archive`); where the expanded set helps, it's noted.

A reminder before you start: slash commands like `/opsx:propose` go in your **AI assistant's chat**, and `openspec` commands go in your **terminal**. If that's new, read [How Commands Work](<OpenSpec 两类命令如何协作.md>) first. In the transcripts below, `You:` and `AI:` are the chat, and lines starting with `$` are the terminal.

> **Not sure what you're building yet?** Most of these recipes are sharper if you start with `/opsx:explore` to think it through first. [Recipe 3](<#recipe-3-exploring-before-you-commit>) shows it in action, and the [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) guide makes the full case.

## Recipe 1: A small feature, the fast path

**When to use it:** you know what you want, and it's a contained piece of work. This is the most common recipe.

The whole thing is three commands. Propose, build, archive.

```text
You: /opsx:propose add-logout-button

AI:  Created openspec/changes/add-logout-button/
     ✓ proposal.md — why and what
     ✓ specs/      — the new requirement and its scenarios
     ✓ design.md   — technical approach
     ✓ tasks.md    — implementation checklist
     Ready for implementation. Run /opsx:apply.
```

Now read the plan. Open the proposal and the delta spec. This is the moment OpenSpec is built for: catching a wrong assumption while it's still one paragraph, not 400 lines of code. Edit any artifact directly if something's off, then continue.

```text
You: /opsx:apply

AI:  Working through tasks...
     ✓ 1.1 Add logout button to header
     ✓ 1.2 Clear session on click
     ✓ 1.3 Add confirmation dialog
     All tasks complete!

You: /opsx:archive

AI:  Archiving add-logout-button...
     ✓ Merged specs into openspec/specs/auth/spec.md
     ✓ Moved to openspec/changes/archive/2026-06-22-add-logout-button/
     Done. Ready for the next change.
```

That's it. The logout behavior is now part of your specs, and the change is filed away with its full context.

## Recipe 2: A bug fix

**When to use it:** something is broken and you want the fix recorded as a deliberate change to behavior, not a mystery commit.

Bug fixes work exactly like features. The difference is in how you frame the proposal: describe the *correct* behavior, not just "fix the bug."

```text
You: /opsx:propose fix-login-redirect-loop

AI:  Created openspec/changes/fix-login-redirect-loop/
     The proposal captures the current broken behavior and the
     intended behavior. The delta spec MODIFIES the redirect
     requirement so it's now precise and testable.
     Ready for implementation.
```

Because the fix lands as a `MODIFIED` requirement with a fresh scenario, the next person (or the next AI session) sees not just that you fixed it, but what "correct" means. Then `/opsx:apply` and `/opsx:archive` as usual.

Tip: for a fix, a good scenario is the regression test in prose. "GIVEN a logged-out user, WHEN they submit valid credentials, THEN they land on the dashboard and are not redirected again." Write that, and the implementation has a clear target.

## Recipe 3: Exploring before you commit

**When to use it:** you have a problem but not yet a plan. You're not sure what to build, or which approach is right.

Start with `/opsx:explore`. It's a thinking partner with no structure. It never writes code, and writes nothing else unless you ask it to capture what you decided, or say yes when it offers. It reads your codebase and helps you decide.

```text
You: /opsx:explore

AI:  What would you like to explore?

You: Page loads feel slow but I don't know where the time goes.

AI:  Let me look... I found three likely culprits:
     1. Large unoptimized images on the product list
     2. Synchronous data fetching that blocks render
     3. Re-renders triggered by context changes
     Which should we tackle first?

You: The data fetching one.

You: /opsx:propose optimize-product-list-fetching

AI:  Created the change, with a proposal informed by what we just
     found. Ready for implementation.
```

Exploration clarifies your thinking *before* you spend a change on it. When the insight crystallizes, propose, and the AI carries the context forward.

## Recipe 4: Juggling two changes at once

**When to use it:** you're mid-feature and an urgent fix jumps the queue.

Changes are independent folders, so parallel work doesn't conflict. Start the fix, ship it, then return to the feature right where you left off.

```text
You: I'm partway through add-dark-mode, but I need to fix a login bug now.

You: /opsx:propose fix-login-redirect

AI:  Created openspec/changes/fix-login-redirect/. Ready for implementation.

You: /opsx:apply

AI:  Implemented the fix. All tasks complete.

You: /opsx:archive

AI:  ✓ Archived fix-login-redirect.

You: Back to dark mode.

You: /opsx:apply add-dark-mode

AI:  Resuming add-dark-mode. Picking up at task 2.3: Update Header...
```

Naming the change in `/opsx:apply add-dark-mode` is how you point the AI at a specific change when more than one is active. Because tasks track completion in `tasks.md`, the AI knows exactly where you stopped.

When several changes are done at once, the expanded `/opsx:bulk-archive` files them together and resolves spec conflicts by checking what's actually implemented. See [Workflows](<OpenSpec 快速与扩展工作流.md#parallel-changes>).

## Recipe 5: A refactor with no behavior change

**When to use it:** you're restructuring code, and externally visible behavior should stay identical.

This is the interesting case, because a pure refactor has *nothing to add to your specs*. The behavior contract doesn't change; only the implementation does. So the work lives in the design and tasks, and the spec delta is empty or absent.

```text
You: /opsx:propose refactor-payment-module

AI:  Created the change. The proposal states the goal (split the
     payment module, no behavior change) and the design captures
     the new structure. No spec changes, since behavior is identical.
     Ready for implementation.
```

Declare the empty delta explicitly by setting `skip_specs: true` in the change's `.openspec.yaml`:

```yaml
schema: spec-driven
skip_specs: true
```

Without the marker, `openspec validate` rejects a change with zero deltas (so a forgotten specs phase still gets caught); with it, validation passes and `openspec status` shows the specs stage as explicitly skipped rather than pending. If the refactor turns out to change behavior after all, remove `skip_specs` from `.openspec.yaml` and write the delta specs — validate treats the marker plus spec files as a conflict, so the stale marker can't linger silently.

Archiving a marked change needs no extra flags (there are no deltas to merge). Independently, the `--skip-specs` flag tells the terminal command to skip the spec step explicitly:

```bash
$ openspec archive refactor-payment-module --skip-specs
```

The same flag is handy for tooling, CI, and docs-only changes. The principle: specs describe behavior, so if behavior didn't change, the spec shouldn't either. See [Concepts](<OpenSpec 核心概念与规格结构.md#what-a-spec-is-and-is-not>).

## Recipe 6: Step-by-step control (expanded commands)

**When to use it:** a complex or risky change where you want to review each artifact before moving on.

The core `/opsx:propose` drafts everything at once. When you'd rather go one step at a time, turn on the expanded commands:

```bash
$ openspec config profile      # select the expanded workflows
$ openspec update              # apply them to this project
```

Now you can scaffold and build incrementally:

```text
You: /opsx:new add-2fa

AI:  Created openspec/changes/add-2fa/. Ready to create: proposal.

You: /opsx:continue

AI:  Created proposal.md. Now available: specs, design.

You: /opsx:continue

AI:  Created specs/auth/spec.md. Now available: design.
```

Review each artifact as it lands, edit freely, and continue when you're happy. When you want the rest drafted in one go, `/opsx:ff` fast-forwards through the remaining planning artifacts. Before archiving, `/opsx:verify` checks that the implementation actually matches the specs. See [Workflows](<OpenSpec 快速与扩展工作流.md#opsxff-vs-opsxcontinue>).

## Recipe 7: Learning the whole loop hands-on

**When to use it:** you've installed OpenSpec and want to *feel* the workflow on your own code, not a toy example.

Turn on the expanded commands (see Recipe 6), then:

```text
You: /opsx:onboard

AI:  Welcome to OpenSpec! I'll walk you through a complete change
     using your actual codebase. Let me scan for a small, safe
     improvement we can make together...
```

`/opsx:onboard` finds a real (small) improvement, creates a change for it, implements it, and archives it, narrating every step. It takes 15 to 30 minutes and leaves you with a real change you can keep or discard. It's the gentlest way to learn. See [Commands](<OpenSpec AI 聊天命令参考.md#opsxonboard>).

## Checking your work from the terminal

Any time, from your terminal, you can inspect the state of things:

```bash
$ openspec list                      # active changes
$ openspec show add-dark-mode        # one change in detail
$ openspec validate add-dark-mode    # check structure
$ openspec view                      # interactive dashboard
```

These are read-and-inspect tools. The proposing and building still happen through slash commands in chat. Full details in the [CLI reference](<OpenSpec 命令行参考.md>).

## Where to go next

- [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>): the recommended way to start when you're unsure
- [Workflows](<OpenSpec 快速与扩展工作流.md>): the patterns above, with decision guidance on when to use each
- [Commands](<OpenSpec AI 聊天命令参考.md>): every slash command in detail
- [Getting Started](<OpenSpec 入门与首个变更.md>): the canonical first-change walkthrough
- [Concepts](<OpenSpec 核心概念与规格结构.md>): why the pieces fit together the way they do


</details>
