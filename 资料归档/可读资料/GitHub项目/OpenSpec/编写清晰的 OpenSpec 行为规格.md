# 编写清晰的 OpenSpec 行为规格

说明如何把行为、需求与场景写成可检查的规格，选择适当增量类型并控制变更范围，附会话超时等示例。

- 来源：OpenSpec
- 资料类型：规格编写指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/writing-specs.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/writing-specs.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 编写好的规格

您很少从空白页编写规范。你用简单的语言描述一个变化，`/opsx:propose`起草需求和场景，然后你把它们做好。本页介绍的是最后一部分——“好”是什么样子，以及如何引导 AI 走向它。

它是[审查变更](<OpenSpec 变更审阅指南.md>)的姊妹篇：审查是抓住草稿中的弱点，写作是知道强项是由什么构成的。

## 规范是行为，而不是代码

规范以任何人都可以检查的方式说明了您的系统*做什么*，而不是它是如何构建的。它由**需求**（行为陈述）和**场景**（证明它们的具体示例）组成。

```markdown
### Requirement: Session Timeout
The system SHALL expire a session after 30 minutes of inactivity.

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass with no activity
- THEN the session is invalidated and the user must re-authenticate
```

将 *how* — 队列、库、表模式 — 保留在 `design.md` 或代码中。当行为和实现混合到一个需求中时，该需求就不再可测试，并且在代码更改的那一刻就开始变得陈旧。

## 什么才是好的需求

好的需求是一种行为，表述得如此简单，您可以将其交给其他人来测试。

- **一个语句，一个`SHALL`/`MUST`。**如果一个需求有三个“and is”子句，那么它实际上是三个需求。把他们分开。
- **Observable。**代码之外的人应该能够判断它是否成立。可以观察到“当上传超过 10 MB 时，系统 SHALL 显示错误横幅”。 “系统 SHALL 可以优雅地处理大量上传”。
- **正确的强度。** OpenSpec 使用 RFC 2119 关键字，它们的含义不同：

  |关键词|含义|
  |---------|---------|
  | `MUST` / `SHALL` |一个硬性的要求。没有商量的余地。|
  | `SHOULD` |强烈建议，并有合理例外的空间。|
  | `MAY` |真正可选。|

默认情况下到达 `MUST`/`SHALL`。仅当您真正的意思是“除非有充分的理由不这样做”时，才使用 `SHOULD`。

某个需求的测试：*从未见过代码的测试人员能否判断是否通过？*如果没有，则需要磨砺。

## 什么才是好的场景

场景是需求发挥作用的地方。每个都是一个具体的 GIVEN / WHEN / THEN，可以成为自动化测试。

- **它执行其要求。**仅重述要求的场景，换句话说，不测试任何内容。使其成为具有特定结果的特定情况。
- **涵盖重要的情况，而不仅仅是快乐的道路。**有效登录很容易。空的输入、过期的令牌、第二次点击、出错的地方——这些都是错误存在的地方，也是场景最有价值的地方。
- **在标题中列出案例。**“场景：拒绝过期令牌”让审阅者一目了然； “场景：测试 2”没有。

一个有用的习惯：在批准之前，询问*如果看到损坏我会感到不安的情况是什么？* - 并确保有一个场景命名它。

## 选择正确的三角洲类型

更改描述了对具有三种部分类型的规范的编辑。使用正确的规格可以让您存档的规格保持诚实：

- **`## ADDED Requirements`** — 以前不存在的全新行为。
- **`## MODIFIED Requirements`** — 已经存在且正在改变的行为。包括完整的新版本；关于更改内容的简短说明可以帮助审阅者。
- **`## REMOVED Requirements`** — 行为消失，并说明原因。

在存档中，ADDED 被附加到主规范中，MODIFIED 替换旧版本，并且 REMOVED 被删除。删除功能的最后一个要求，然后将其废弃：存档会删除`openspec/specs/<capability>/spec.md`，而不是保留其中没有任何内容的规范。因为这是删除文件的一个存档步骤，所以必须要求将 `retire_capabilities: true` 添加到更改的 `.openspec.yaml`，以及该文件已需要的 `schema:`。如果没有它，存档就会中止并告诉您这一点。停用会删除整个文件，因此当规范包含其标题 `## Purpose` 及其要求块之外的任何内容（`## Notes` 部分，要求下的注释）时，该文件也会被拒绝。中止为这些行命名；将它们移至 `## Purpose` 或需求中，或手动删除规范。对于调用者结帐中的规范，存档输出还命名恢复已提交文件的 `git checkout`；选定的商店会收到结账范围内的恢复指导。如果您将真正的更改标记为 ADDED，您最终会遇到两个相互竞争的需求；如果您将新行为描述为 MODIFIED，则没有任何内容可以替换。如有疑问，请打开当前规范并查看是否已存在该要求。

还有一个部分值得了解。当您的增量创建尚不存在的功能时，请使用 `## Purpose` 打开它 - 一两句话说明该功能的用途。 Archive 将其用作其创建的主要规范的目的；跳过它，您会得到一个需要手动填写的 `TBD` 占位符。现有规范已经有一个目的，因此增量值将被忽略 - 直接编辑 `openspec/specs/<capability-path>/spec.md` 来更改它。这里，`<capability-path>`是相对于`specs/`的目录，例如平面项目中的`user-auth`或按域组织的项目中的`identity/user-auth`。

## 调整变更大小

最常见的创作错误并不是措辞糟糕的要求，而是试图进行三项更改。

**一个好的改变有一个意图，你可以用一句话来表达。**“添加暗模式切换。” “对登录端点进行速率限制。” “从 cookie 中迁移会话。”如果描述变化需要很多“而且”，那就是分裂它的信号。

迹象表明变化太大：

- 该提案的范围读起来就像是不相关功能的列表。
- 审查它需要一个下午，所以没有人会。
- 两个人在工作时不可能不发生碰撞。
- 一半的任务可以自行交付。

较小的更改更容易审查，更容易在一次集中会议中构建，并且在六个月后（当存档只剩下所有内容时）更容易推理。您始终可以并行运行多个更改 - 请参阅[编辑和迭代](<OpenSpec 变更工件的编辑与迭代.md>)和[工作流程](<OpenSpec 快速与扩展工作流.md>)。

相反的情况也会发生：一行拼写错误修复不需要三个要求和一个设计文档。将仪式与赌注相匹配。

## 如何引导 AI 获得良好的吃水

因为 `/opsx:propose` 进行了初稿，所以您得到的结果的质量会跟踪您所提供的内容的质量。您不必手动编写需求 - 您必须瞄准 AI：

- **说明意图和边界。** *“添加一个在首次加载时遵循 OS 设置的暗模式切换 - 不要触摸现有主题 API。”* 超出范围的一半与范围内的一半。
- **说出您关心的案例。** *“确保有一个针对已手动选择主题的用户的方案。”* AI涵盖了您所指出的内容。
- **然后编辑。** 这是简单的 Markdown。收紧模糊的`SHALL`，删除没有测试任何内容的场景，添加它错过的情况 - 或者询问AI：*“超时要求模糊，将其固定为30分钟。”*

草稿、锐化、重复。几轮之后就会产生一个值得您信任的规范，这就是重点。

## 快速清单

- [ ] 每个要求都是具有 `SHALL`/`MUST` 的可观察行为。
- [ ] 需求中没有包含任何实现细节。
- [ ] 每个需求至少有一个实际执行它的场景。
- [ ] 重要的边缘和错误情况有场景，而不仅仅是快乐路径。
- [ ] Delta 根据当前规格正确使用 ADDED / MODIFIED / REMOVED。
- [ ] 整个变化有一个意图，您可以用一句话来说明。

## 下一步去哪里

- [回顾变更](<OpenSpec 变更审阅指南.md>)——两分钟的传球，捕捉漏掉的东西。
- [概念](<OpenSpec 核心概念与规格结构.md>) — 规格、变更和增量背后的更深层次模型。
- [示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md>) - 从开始到结束的真正变化。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Writing Good Specs

You rarely write a spec from a blank page. You describe a change in plain language, `/opsx:propose` drafts the requirements and scenarios, and then you make them good. This page is about that last part — what "good" looks like, and how to steer the AI toward it.

It's the companion to [Reviewing a Change](<OpenSpec 变更审阅指南.md>): reviewing is catching the weak spots in a draft, writing is knowing what a strong one is made of.

## A spec is behavior, not code

A spec says what your system *does*, in terms anyone could check — not how it's built. It's made of **requirements** (statements of behavior) and **scenarios** (concrete examples that prove them).

```markdown
### Requirement: Session Timeout
The system SHALL expire a session after 30 minutes of inactivity.

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass with no activity
- THEN the session is invalidated and the user must re-authenticate
```

Keep the *how* — the queue, the library, the table schema — in `design.md` or the code. When behavior and implementation get mixed into one requirement, the requirement stops being testable and starts going stale the moment the code changes.

## What makes a good requirement

A good requirement is one behavior, stated so plainly you could hand it to someone else to test.

- **One statement, one `SHALL`/`MUST`.** If a requirement has three "and also" clauses, it's really three requirements. Split them.
- **Observable.** Someone outside the code should be able to tell whether it holds. "The system SHALL show an error banner when the upload exceeds 10 MB" is observable. "The system SHALL handle large uploads gracefully" is not.
- **The right strength.** OpenSpec uses the RFC 2119 keywords, and they mean different things:

  | Keyword | Meaning |
  |---------|---------|
  | `MUST` / `SHALL` | A hard requirement. Non-negotiable. |
  | `SHOULD` | A strong recommendation, with room for a justified exception. |
  | `MAY` | Genuinely optional. |

  Reach for `MUST`/`SHALL` by default. Use `SHOULD` only when you truly mean "unless there's a good reason not to."

The test for a requirement: *could a tester who's never seen the code tell whether it passed?* If not, it needs sharpening.

## What makes a good scenario

Scenarios are where a requirement earns its keep. Each one is a concrete GIVEN / WHEN / THEN that could become an automated test.

- **It exercises its requirement.** A scenario that just restates the requirement in other words tests nothing. Make it a specific situation with a specific outcome.
- **Cover the cases that matter, not just the happy path.** The valid login is easy. The empty input, the expired token, the second click, the thing that goes wrong — those are where bugs live, and where a scenario is worth the most.
- **Name the case in the title.** "Scenario: Rejects an expired token" tells a reviewer what's covered at a glance; "Scenario: Test 2" doesn't.

A useful habit: before approving, ask *what's the one case I'd be upset to see broken?* — and make sure a scenario names it.

## Pick the right kind of delta

A change describes its edits to the specs with three section types. Using the right one keeps your archived specs honest:

- **`## ADDED Requirements`** — brand-new behavior that didn't exist before.
- **`## MODIFIED Requirements`** — behavior that already existed and is changing. Include the full new version; a short note on what changed helps a reviewer.
- **`## REMOVED Requirements`** — behavior going away, with a line on why.

On archive, ADDED gets appended to the main spec, MODIFIED replaces the old version, and REMOVED is dropped from it. Remove the last requirement a capability has and you retire it: rather than leave a spec with nothing in it, archive deletes `openspec/specs/<capability>/spec.md`. Because that is the one archive step that removes a file, it has to be asked for — add `retire_capabilities: true` to the change's `.openspec.yaml`, alongside the `schema:` that file already needs. Without it the archive aborts and tells you so. Retirement deletes the whole file, so it is also refused while the spec holds anything outside its title, `## Purpose`, and its requirement blocks — a `## Notes` section, a comment under a requirement. The abort names those lines; move them into `## Purpose` or a requirement, or delete the spec by hand. For a spec in the caller's checkout, the archive output also names the `git checkout` that restores a committed file; selected stores receive checkout-scoped recovery guidance instead. If you mark a real change as ADDED, you end up with two competing requirements; if you describe new behavior as MODIFIED, there's nothing to replace. When in doubt, open the current spec and see whether the requirement is already there.

One more section is worth knowing about. When your delta creates a capability that doesn't exist yet, open it with `## Purpose` — a sentence or two on what the capability is for. Archive uses it as the Purpose of the main spec it creates; skip it and you get a `TBD` placeholder to fill in by hand. An existing spec already has a Purpose, so a delta's is ignored there — edit `openspec/specs/<capability-path>/spec.md` directly to change one. Here, `<capability-path>` is the directory relative to `specs/`, such as `user-auth` in a flat project or `identity/user-auth` in a project organized by domain.

## Right-size the change

The single most common authoring mistake isn't a badly worded requirement — it's a change that's trying to be three changes.

**A good change has one intent you can say in a sentence.** "Add a dark-mode toggle." "Rate-limit the login endpoint." "Migrate sessions off cookies." If describing the change needs a lot of "and also," that's the signal to split it.

Signs a change is too big:

- The proposal's scope reads like a list of unrelated features.
- Reviewing it would take an afternoon, so nobody will.
- Two people couldn't work on it without colliding.
- Half the tasks could ship on their own.

Smaller changes are easier to review, easier to build in one focused session, and easier to reason about six months later when the archive is all that's left. You can always run several changes in parallel — see [Editing & iterating](<OpenSpec 变更工件的编辑与迭代.md>) and [Workflows](<OpenSpec 快速与扩展工作流.md>).

The opposite also happens: a one-line typo fix doesn't need three requirements and a design doc. Match the ceremony to the stakes.

## How to steer the AI toward a good draft

Because `/opsx:propose` does the first draft, the quality of what you get back tracks the quality of what you give it. You don't have to write requirements by hand — you have to aim the AI well:

- **State the intent and the boundary.** *"Add a dark-mode toggle that follows the OS setting on first load — don't touch the existing theme API."* The out-of-scope half matters as much as the in-scope half.
- **Name the cases you care about.** *"Make sure there's a scenario for a user who already picked a theme manually."* The AI covers what you point at.
- **Then edit.** It's plain Markdown. Tighten a vague `SHALL`, delete a scenario that tests nothing, add the case it missed — or ask the AI to: *"the timeout requirement is vague, pin it to 30 minutes."*

Draft, sharpen, repeat. A few rounds of that produces a spec you'd trust, which is the whole point.

## A quick checklist

- [ ] Each requirement is one observable behavior with a `SHALL`/`MUST`.
- [ ] No implementation details are baked into the requirements.
- [ ] Every requirement has at least one scenario that actually exercises it.
- [ ] The important edge and error cases have scenarios, not just the happy path.
- [ ] Deltas use ADDED / MODIFIED / REMOVED correctly against the current spec.
- [ ] The whole change has one intent you can state in a sentence.

## Where to go next

- [Reviewing a Change](<OpenSpec 变更审阅指南.md>) — the two-minute pass that catches what slipped through.
- [Concepts](<OpenSpec 核心概念与规格结构.md>) — the deeper model behind specs, changes, and deltas.
- [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>) — real changes from start to finish.


</details>
