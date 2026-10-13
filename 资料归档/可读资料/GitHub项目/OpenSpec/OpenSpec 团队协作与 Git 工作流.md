# OpenSpec 团队协作与 Git 工作流

说明团队如何在日常开发和拉取请求中审阅规格、选择归档时机、并行处理变更，以及让规划跨越多个仓库。

- 来源：OpenSpec
- 资料类型：团队协作指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/team-workflow.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/team-workflow.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OpenSpec 在团队中

无论您是独自一人还是在二十人的团队中，其他指南中的所有内容都相同。团队的变化是围绕边缘的问题：规范在哪里，团队成员如何审查计划，以及这些如何适应我们已有的拉取请求流程？

简短的回答：更改只是文件，并且 OpenSpec 永远不会触及 git。因此它适合您现有的工作流程，而不是取代它。本页详细说明了行之有效的约定。

## 一条规则：OpenSpec 不接触 git

OpenSpec 在 `openspec/` 下读取和写入纯 Markdown。它永远不会提交、分支、推送或拉取您的项目 - 并且它永远不会自行克隆或同步 [ 存储 ](<OpenSpec Store 跨仓库规划指南（Beta）.md>)。这意味着：

- **您像任何源一样提交`openspec/`。**规格、活动更改和存档是项目历史记录的一部分。 （是的，提交整个文件夹 - 请参阅 [FAQ](<OpenSpec 常见问题.md#我应该将-openspec-文件夹提交到-git-吗>)。）
- **更改是您像代码一样版本的文件夹。** `openspec/changes/add-dark-mode/` 只是分支上的文件。
- **下面的一切都是约定，而不是强制执行。** OpenSpec不会让你这样做；它非常适合。

## 日常循环

运行良好的工作流程将更改映射到分支和拉取请求：

```
git switch -c add-dark-mode        start a branch, as usual
   │
/opsx:propose add-dark-mode        draft the plan (proposal + specs + tasks)
   │
REVIEW THE PLAN                    you read it before any code — see Reviewing a Change
   │
/opsx:apply                        build it; artifacts + code change together
   │
git commit && open a PR            the PR contains the spec delta AND the code
   │
teammate reviews, merges
   │
/opsx:archive                      fold the delta into specs/, move the change to archive/
```

该计划和代码并存于同一个分支中，因此您的队友会一起审查两者，六个月后，存档的规范仍然解释了为什么代码看起来像它的样子。

## 检查拉取请求中的规格

这是团队感受到回报的地方。当 PR 包含更改的增量规范时，审阅者在阅读一行代码之前会得到原始差异从未给他们的东西：**此更改应该执行的操作的简单语言声明**。

给审稿人一个好的审稿顺序：

1. **阅读 `proposal.md`** — 这是正确的问题和范围吗？
2. **读取`specs/`**下的增量——“完成”定义正确吗？ （这是[审查更改](<OpenSpec 变更审阅指南.md>)两分钟的通行证，现在发生在PR中。）
3. **然后阅读代码差异** - 它是否完全满足了这些要求？

不同意 * 方法 * 的审阅者可以廉价地反对该提案，而不是在 300 行代码中重新争论它。将增量规范放在 PR 描述的顶部附近，或将审阅者指向更改文件夹，以便他们从那里开始。

## 何时归档

存档会将更改的增量折叠到主 `openspec/specs/` 中，并将更改文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`。因为 `specs/` 是 ** 共享事实来源 **，所以时机对团队来说很重要。两个可行的约定：

- **PR合并后存档（推荐）。**分支携带活动更改；一旦它合并到你的主分支，就在那里存档（通常是一个小的后续提交或计划的清理）。这使得共享的 `specs/` 仅与实际交付的工作一起向前推进。
- **存档在PR内。**对于小型团队来说更简单：添加代码的相同PR也可以同步和存档。权衡是你的 `specs/` diff 和代码 diff 放在一起，这会使 PR 噪音更大。

选择一个并保持一致。无论哪种方式，`/opsx:archive` 都会检查任务是否完成并首先提供同步，因此不会意外合并半完成的情况。

## 两个人，平行变化

因为更改是单独的文件夹，所以它们不会冲突：

- **不同的更改，不同的人 - 没问题。** `add-dark-mode` 和 `rate-limit-login` 是不同分支上的不同文件夹；他们从不接触对方，直到他们都存档为止。
- **一个更改，一个所有者。** 两个人编辑同一更改文件夹会发生冲突，就像两个人编辑同一文件一样。将更改保留给单个作者，或将其拆分为两个更改（[正确大小](<编写清晰的 OpenSpec 行为规格.md#调整变更大小>)的另一个原因）。
- **出现冲突的地方是`specs/`。**如果两个更改都修改了*相同的*要求，则归档第二个更改将在`openspec/specs/…/spec.md`中发生冲突 - 像任何合并冲突一样解决它，保留反映现实的要求。这种情况很少见，但它是一个功能：它是 git 告诉你两个关于系统应该如何行为的更改不一致的。

## 当计划超出一个存储库时

上述所有内容都假设该计划位于代码存储库自己的 `openspec/` 文件夹中，这是正确的默认值。当您的规划真正跨越多个存储库或团队时（一项功能涉及三个服务，或者一个团队拥有而其他团队使用的需求），这就是测试版 **stores** 功能的用途：规划拥有自己的存储库，任何代码存储库都可以指向该存储库。从 [商店用户指南 ](<OpenSpec Store 跨仓库规划指南（Beta）.md>) 开始。

## 下一步去哪里

- [审查更改](<OpenSpec 变更审阅指南.md>) - 审查通过，现在位于您的PR中。
- [编写良好的规范](<编写清晰的 OpenSpec 行为规格.md>) - 包括如何调整更改大小以使其适合一个分支。
- [商店用户指南](<OpenSpec Store 跨仓库规划指南（Beta）.md>) - 跨越存储库和团队的规划。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OpenSpec on a Team

Everything in the other guides works the same whether you're solo or on a team of twenty. What changes on a team is the questions around the edges: where do the specs live, how do teammates review a plan, and how does any of this fit the pull-request flow we already have?

The short answer: a change is just files, and OpenSpec never touches git. So it fits your existing workflow instead of replacing it. This page spells out the conventions that work well.

## One rule: OpenSpec doesn't touch git

OpenSpec reads and writes plain Markdown under `openspec/`. It never commits, branches, pushes, or pulls in your project — and it never clones or syncs a [store](<OpenSpec Store 跨仓库规划指南（Beta）.md>) on its own. That means:

- **You commit `openspec/` like any source.** Specs, active changes, and the archive are part of your project's history. (Yes, commit the whole folder — see the [FAQ](<OpenSpec 常见问题.md#should-i-commit-the-openspec-folder-to-git>).)
- **A change is a folder you version like code.** `openspec/changes/add-dark-mode/` is just files on a branch.
- **Everything below is convention, not enforcement.** OpenSpec won't make you do it this way; it just fits cleanly.

## The everyday loop

The workflow that works well maps a change onto a branch and a pull request:

```
git switch -c add-dark-mode        start a branch, as usual
   │
/opsx:propose add-dark-mode        draft the plan (proposal + specs + tasks)
   │
REVIEW THE PLAN                    you read it before any code — see Reviewing a Change
   │
/opsx:apply                        build it; artifacts + code change together
   │
git commit && open a PR            the PR contains the spec delta AND the code
   │
teammate reviews, merges
   │
/opsx:archive                      fold the delta into specs/, move the change to archive/
```

The plan and the code live side by side in the same branch, so your teammates review both together, and six months later the archived spec still explains why the code looks the way it does.

## Reviewing specs in a pull request

This is where a team feels the payoff. When a PR includes the change's delta spec, the reviewer gets something a raw diff never gives them: **a plain-language statement of what this change is supposed to do**, before they read a single line of code.

A good review order for the reviewer:

1. **Read `proposal.md`** — is this the right problem and scope?
2. **Read the delta under `specs/`** — is "done" defined correctly? (This is the [Reviewing a Change](<OpenSpec 变更审阅指南.md>) two-minute pass, now happening in the PR.)
3. **Then read the code diff** — does it deliver exactly those requirements?

A reviewer who disagrees with the *approach* can say so against the proposal, cheaply, instead of relitigating it across 300 lines of code. Put the delta spec near the top of the PR description, or point reviewers at the change folder, so they start there.

## When to archive

Archiving folds a change's deltas into your main `openspec/specs/` and moves the change folder to `openspec/changes/archive/YYYY-MM-DD-<name>/`. Because `specs/` is the **shared source of truth**, the timing matters on a team. Two workable conventions:

- **Archive after the PR merges (recommended).** The branch carries the active change; once it's merged to your main branch, archive there (often a tiny follow-up commit or a scheduled cleanup). This keeps the shared `specs/` moving forward only with work that actually shipped.
- **Archive inside the PR.** Simpler for small teams: the same PR that adds the code also syncs and archives. The tradeoff is that your `specs/` diff and your code diff land together, which can make the PR noisier.

Pick one and be consistent. Either way, `/opsx:archive` checks that tasks are complete and offers to sync first, so nothing merges half-finished by accident.

## Two people, parallel changes

Because changes are separate folders, they don't collide:

- **Different changes, different people — no problem.** `add-dark-mode` and `rate-limit-login` are different folders on different branches; they never touch each other until they both archive.
- **One change, one owner.** Two people editing the same change folder conflict exactly like two people editing the same file. Keep a change to a single author, or split it into two changes (another reason to [right-size](<编写清晰的 OpenSpec 行为规格.md#right-size-the-change>)).
- **The one place conflicts show up is `specs/`.** If two changes both modify the *same* requirement, archiving the second one will conflict in `openspec/specs/…/spec.md` — resolve it like any merge conflict, keeping the requirement that reflects reality. This is rare, and it's a feature: it's git telling you two changes disagreed about how the system should behave.

## When planning outgrows one repo

Everything above assumes the plan lives in the code repo's own `openspec/` folder, which is the right default. When your planning genuinely spans several repos or teams — one feature touching three services, or requirements one team owns and others consume — that's what the beta **stores** feature is for: planning gets its own repo that any code repo can point at. Start with the [Stores User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>).

## Where to go next

- [Reviewing a Change](<OpenSpec 变更审阅指南.md>) — the review pass, now inside your PR.
- [Writing Good Specs](<编写清晰的 OpenSpec 行为规格.md>) — including how to right-size a change so it fits one branch.
- [Stores User Guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>) — planning that spans repos and teams.


</details>
