# Qwen Code 项目指令与记忆

比较 QWEN.md 与自动记忆，介绍位置、引用、置顶、清理、团队共享和可选 Git 同步，用于跨会话保留知识。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/QwenLM/qwen-code/commit/864850816e92c6bb3e99dd5ba0b5370885ada120>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/memory.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/memory.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 内存

每个 Qwen Code 会话都以新的上下文窗口开始。两种机制可以跨会话传递知识，因此您不必每次都重新解释自己：

- **QWEN.md** — 指令 _ 你 _ 写入一次，Qwen 读取每个会话
- **自动记忆** — 笔记Qwen根据从你那里学到的东西来编写自己

---

## QWEN.md：您对Qwen的指示

QWEN.md 是一个纯文本文件，您可以在其中编写 Qwen 应该始终了解您的项目或偏好的内容。将其视为在每次对话开始时加载的永久简报。

### QWEN.md 中要放入什么

添加您必须在每次会话中重复的内容：

- 构建和测试命令（`npm run test`、`make build`）
- 您的团队遵循的编码约定（“所有新文件必须有 JSDoc 注释”）
- 架构决策（“我们使用存储库模式，从不直接从控制器调用数据库”）
- 个人喜好（“始终使用 pnpm，而不是 npm”）
- 高风险工作的验证策略 - 例如“在得出结论之前根据数据库进行验证”（有关现成的模板，请参阅 [ 执行基于证据的结论 ](<Qwen Code 常见开发工作流.md#执行基于证据的结论>)）

不要包含 Qwen 可以通过阅读代码找出的内容。 QWEN.md 在简短且具体的情况下效果最好——它越长，Qwen 遵循它的可靠性就越低。

### 在哪里创建QWEN.md

|文件|它适用于谁|
| ----------------------------- | ------------------------------------------------ |
| `~/.qwen/QWEN.md`             |您，在您的所有项目中|
|`QWEN.md` 在项目根目录中|您的整个团队（将其提交给源代码控制）|
| `.qwen/QWEN.local.md`         |只有你，只有在这个项目中（远离git）|

您可以将这些任意组合。 Qwen 在您启动会话时加载所有这些。

如果您的存储库已有其他 AI 工具的 `AGENTS.md` 文件，Qwen 也会读取该文件。无需重复说明。

#### 何时使用`.qwen/QWEN.local.md`

将其用于 ** 项目特定但个人的 ** 说明 - 属于该项目但不应与团队共享的内容：

- 您自己的集群 ID、容器注册表命名空间或云帐户
- 对本地环境进行硬编码的个人调试命令
- 您希望 Qwen 了解您正在进行的工作但不提交的注释

它在**共享项目`QWEN.md`之后加载**，因此您的本地指令可以补充或覆盖团队的指令。

**你必须自己gitignore它。**虽然`.qwen/`通常被视为本地目录，但qwen-code不会为你生成`.gitignore`，并且某些项目会提交`.qwen/settings.json`。将此行添加到您的 `.gitignore` （或您的全局 git 忽略）：

```
.qwen/QWEN.local.md
```

### 使用 `/init` 自动生成一个

运行 `/init` 和 Qwen 将分析您的代码库以创建一个入门 QWEN.md，其中包含构建命令、测试指令和它找到的约定。如果已经存在，它会建议添加而不是覆盖。

### 参考其他文件

您可以将 QWEN.md 指向其他文件，以便 Qwen 也读取它们：

```markdown
See @README.md for project overview.

# Conventions

- Git workflow: @docs/git-workflow.md
```

在 QWEN.md 中的任何位置使用 `@path/to/file`。相对路径从 QWEN.md 文件本身解析。

---

## 自动记忆：Qwen 了解您的信息

自动记忆在后台运行。在您的每次对话之后，Qwen 都会悄悄保存它学到的有用信息 - 您的偏好、您提供的反馈、项目背景 - 以便它可以在未来的会话中使用它们，而无需您重复自己。

这与 QWEN.md 不同：你不写它，Qwen 写。

### Qwen 节省了什么

Qwen 寻找四种值得记住的事情：

|什么|示例|
| ----------------------- | -------------------------------------------------------- |
|**关于你**|您的角色、背景、您喜欢的工作方式|
|**您的反馈**|您所做的更正、您确认的方法|
|**项目背景**|正在进行的工作、决策、目标从代码中不明显|
|**外部参考**|您提到的仪表板、票务跟踪器、文档链接|

Qwen 不会保存所有内容 - 只会保存下次真正有用的内容。

### 存储在哪里

自动存储文件位于 `~/.qwen/projects/<project>/memory/`。同一结帐的所有分支共享相同的内存文件夹，因此 Qwen 在一个分支中学到的内容在其他分支中也可用。每个链接的 git 工作树都有自己的内存文件夹，与聊天和其他会话状态的每个工作树隔离相匹配 - 您希望每个工作树中的存储库范围的约定属于 [ 团队内存 ](<#团队记忆与协作者共享>)。

保存的所有内容都是纯 Markdown — 您可以随时打开、编辑或删除任何文件。

`MEMORY.md` 是一个紧凑索引，而不是完整的内存存储。它生成的主体和加载到提示中的部分共享 200 行和 25,000 UTF-16 代码单元的限制；截断通知单独添加。在及时制定预算之前，行尾会被标准化。在大小压力下，完整的普通条目（最多 150 代码单元）在较长的条目之前获得空间，并且保留的条目保持其原始顺序。无法容纳的条目将被整个跳过，因此其 Markdown 链接永远不会在中间被切断。在链接的主题文件中保留详细信息。

#### 固定内存

将自动内存维护应保留的手动文档
放在托管内存目录中的`pinned/`下，例如
`~/.qwen/projects/<project>/memory/pinned/architecture.md`或
`~/.qwen/memories/pinned/preferences.md`。使用与其他
内存文档相同的frontmatter。有效的固定文件可由 Qwen 读取，并在下次重建 `MEMORY.md` 时包含在 
 中，其大小和文件计数限制与 
 其他内存文档相同。

只有直接位于托管内存根内部的顶级 `pinned/` 目录受到 
 保护；诸如`memory/project/pinned/`之类的嵌套目录是普通的
可写内存。自动提取和 Dreamworks 不区分大小写地匹配保留的 
 目录名称。

自动提取被指示保持固定记录及其有效的 
 索引条目不变，而 Dream 被指示在 
 合并期间跳过 `pinned/`。自动提取和分叉 Dream Worker，包括 
 后台清理，都会在其写入和编辑 
 工具上强制执行固定文件边界，包括通过符号链接解析为 `pinned/` 的路径。
 命令行删除也被关闭：只有 Dream Worker 持有的项目一个
shell，其策略是只读的，而自动提取和
用户内存Dream Worker根本不持有任何shell。您仍然可以直接控制
这些文件，并且可以通过明确的`/forget`请求删除它们。

> **注意：**可见的`/dream`斜杠命令在主Agent上运行。它
> 接收相同的跳跃指令，但尚未接收分叉的
> 工人的确定性每转工具门。

### 定期清理

Qwen 定期检查其保存的内存以删除重复项并清理过时的条目。累积足够的会话后，该操作每天在后台自动运行一次。如果您希望它立即运行，您可以使用 `/dream` 手动触发它。

当清理在后台运行时，您的会话将正常继续。

### 打开或关闭它

自动记忆功能默认开启。要切换它，请打开 `/memory` 并使用顶部的开关。您可以仅关闭自动保存、定期清理或两者都关闭。

您还可以在`~/.qwen/settings.json`（适用于所有项目）或`.qwen/settings.json`（仅此项目）中设置它们：

```json
{
  "memory": {
    "enableManagedAutoMemory": true,
    "enableManagedAutoDream": true
  }
}
```

### 结构化召回（选择加入）

默认情况下，Qwen 将内存读取为平面 `MEMORY.md` 索引。结构化协议用分层内存树取代了它，仅注入与当前请求相关的子树，并为 Qwen 提供了 `search_memory` 工具来按需提取完整条目 - 一旦内存集合增长到超过少数文件，这会花费更少的令牌。

默认情况下它是关闭的。在`settings.json`中将其打开（需要重新启动）：

```json
{
  "memory": {
    "enableStructuredRecall": true
  }
}
```

或使用 `QWEN_CODE_MEMORY_STRUCTURED_RECALL=1` 进行单次运行。

打开它会启动后台元数据迁移：每个内存文件都会获得回忆所需的前言（类别、关键字、使用场景）。它每轮最多运行 10 个文件的小批量，并且只重写前文，从不重写笔记的正文。当设置关闭时，永远不会计划迁移，因此不会重写任何内容，也不会进行后台模型调用。

### 团队记忆（与协作者共享）

默认情况下，自动内存是**您私有的** - 它位于您的主目录下并且永远不会共享。团队记忆是整个团队通过 git** 共享 ** 的选择加入层。

启用后，Qwen 在存储库 ** 内的 `.qwen/team-memory/` ** 处获得第三个内存目录。它使用与私有层相同的每内存一个文件布局和 `MEMORY.md` 索引。因为它致力于存储库，所以它会以正常方式与每个协作者共享：您 `git pull` 接收队友的记忆，commit/push 分享您的记忆。 Qwen 在这里路由持久的、项目范围的知识——每个贡献者必须遵循的约定，共享参考指针（跟踪器、仪表板）——而个人和快速衰减的笔记则保持私密。

在 `settings.json` 中为每个项目（或全局）启用它：

```json
{
  "memory": {
    "enableTeamMemory": true
  }
}
```

默认情况下**关闭**。请记住以下注意事项：

- **它是源代码控制的，并且对具有存储库访问权限的每个人都可见。** 像提交存储库一样对待团队内存。
- **秘密被阻止。** 扫描对 `.qwen/team-memory/` 的写入以获取凭据（API 密钥、令牌、私钥）；检测到的秘密会被拒绝，永远不会被写入。扫描只是一种保障，而不是一种保证——不要将敏感数据放在那里。
- **更改是可审查的。** 团队内存写入像任何其他文件一样出现在 `git status` / PR diff 中，因此可以在提交之前对其进行审查。在默认批准模式下Qwen在每个团队写入之前也会询问；在 `AUTO_EDIT`/YOLO 模式（您选择自动批准）中，它们会在没有提示的情况下应用，但仍然会出现在差异中。
- **该目录必须是 git-tracked。** 如果您的项目的 `.gitignore` 不包括 `.qwen/*`，请重新包含路径以便可以共享：

  ```gitignore
  !.qwen/team-memory/
  !.qwen/team-memory/**
  ```

注意：使用文件全局忽略形式（`.qwen/*`），而不是带有尾部斜杠的目录形式（`.qwen/`）。目录形式的忽略使 git 完全跳过该文件夹，因此它下面的 `!`-reinclude 是一个无操作，并且团队层在 git 中默默地保持为空。当层已启用但其目录被 git 忽略或位于任何 git 存储库之外时，Qwen 在启动时发出一次警告，因此这种错误配置不会被忽视。

`QWEN_CODE_MEMORY_TEAM=1` / `=0` 覆盖单次运行的设置。

### 自动 git 同步（可选）

默认情况下，您与正常的 git 工作流程共享团队内存（`pull` 接收，`commit`/`push` 共享）。要让 Qwen 为您执行此操作，请启用同步：

```json
{
  "memory": {
    "enableTeamMemory": true,
    "enableTeamMemorySync": true
  }
}
```

启用后，在会话开始时 Qwen 尽力同步 `.qwen/team-memory/` 目录：它会重建共享 `MEMORY.md` 索引，快进拉取协作者的更新 ** 首先 **，然后在顶部提交团队内存更改，并推送**仅同步提交**（通过显式单分支引用规范） - 因此您加载的索引反映了最新的。它仅 ** 暂存 ** 团队目录（您的其他工作更改永远不会提交），并且永远不会因 git 故障而阻塞会话。默认关闭。 `QWEN_CODE_MEMORY_TEAM_SYNC=1` / `=0` 覆盖单次运行的设置。

启用之前需要了解两件事：

- **快进拉动作用于整个当前分支，而不仅仅是`.qwen/team-memory/`**（git没有路径范围的拉动）。因此，同步会将您的分支快进到远程提示。相比之下，推送是有范围的：它只发布**此同步刚刚创建的提交**，因此它永远不会推送您拥有的其他未推送的提交 - 如果您的分支已经领先于上游，则同步会在本地提交并跳过推送。在快进拉动良好的分支上启用它，或者在专用结帐上运行它。
- **分叉分支保持不变**（`--ff-only`从不合并）。当发生这种情况时，同步根本不会执行该会话；解决分歧（`git pull`）并恢复。没有上游（没有跟踪配置）的分支仍然会在本地提交，但会跳过推送——没有地方可以推送。

---

## 命令

### `/memory`

打开内存面板。从这里您可以：

- 打开或关闭自动内存保存
- 打开或关闭定期清理（梦想）
- 打开您的个人QWEN.md（`~/.qwen/QWEN.md`）
- 打开项目QWEN.md
- 浏览自动记忆文件夹

### `/memory migrate-team`

将**团队记忆**层（`.qwen/team-memory/`）迁移到结构化元数据格式：每个文件获得记忆回忆所需的前言（名称、描述、类别、关键字、使用场景）。一旦启用[结构化调用](<#结构化召回选择加入>)，个人和项目内存将在后台自动迁移；团队内存通过 git 共享，因此重写仅在明确请求时运行，并显示为可审查的提交。在启用团队内存的受信任工作区中，每个存储库运行一次。

### `/init`

为您的项目生成启动器 QWEN.md。 Qwen 读取您的代码库并填写它发现的构建命令、测试指令和约定。

### `/remember <text>`

立即将某些内容保存到自动内存中，而无需等待 Qwen 自动拾取它：

```
/remember always use snake_case for Python variable names
/remember the staging environment is at staging.example.com
```

### `/forget <text>`

删除与您的描述相匹配的自动记忆条目：

```
/forget old workaround for the login bug
```

### `/dream`

立即运行内存清理，而不是等待自动计划：

```
/dream
```

---

## 故障排除

### Qwen 没有关注我的 QWEN.md

打开【`/memory`】查看加载了哪些文件。如果您的文件未列出，Qwen 将看不到它 — 确保它位于项目根目录或 `~/.qwen/` 中。

当说明具体时，效果会更好：

- ✓ `Use 2-space indentation for TypeScript files`
- ✗ `Format code nicely`

如果您有多个 QWEN.md 文件且指令相互冲突，则 Qwen 的行为可能会不一致。审查它们并消除任何矛盾。

### 我想看看 Qwen 保存了什么

运行`/memory`并选择**打开自动存储文件夹**。所有保存的记忆都是可读的 Markdown 文件，您可以浏览、编辑或删除。

### Qwen 总是忘记事情

如果自动记忆已打开，但 Qwen 似乎无法记住跨会话的内容，请尝试运行 `/dream` 强制执行清理过程。另请检查 `/memory` 以确认两个切换均已启用。

对于您总是希望 Qwen 记住的事情，请将它们添加到 QWEN.md 中 - 自动记忆是尽力而为，QWEN.md 是有保证的。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Memory

Every Qwen Code session starts with a fresh context window. Two mechanisms carry knowledge across sessions so you don't have to re-explain yourself every time:

- **QWEN.md** — instructions _you_ write once and Qwen reads every session
- **Auto-memory** — notes Qwen writes itself based on what it learns from you

---

## QWEN.md: your instructions to Qwen

QWEN.md is a plain text file where you write things Qwen should always know about your project or your preferences. Think of it as a permanent briefing that loads at the start of every conversation.

### What to put in QWEN.md

Add things you'd otherwise have to repeat every session:

- Build and test commands (`npm run test`, `make build`)
- Coding conventions your team follows ("all new files must have JSDoc comments")
- Architectural decisions ("we use the repository pattern, never call the database directly from controllers")
- Personal preferences ("always use pnpm, not npm")
- A verification policy for high-stakes work — for example "verify against the database before concluding" (see [Enforce evidence-based conclusions](<Qwen Code 常见开发工作流.md#enforce-evidence-based-conclusions>) for a ready-made template)

Don't include things Qwen can figure out by reading your code. QWEN.md works best when it's short and specific — the longer it gets, the less reliably Qwen follows it.

### Where to create QWEN.md

| File                          | Who it applies to                                |
| ----------------------------- | ------------------------------------------------ |
| `~/.qwen/QWEN.md`             | You, across all your projects                    |
| `QWEN.md` in the project root | Your whole team (commit it to source control)    |
| `.qwen/QWEN.local.md`         | Only you, only in this project (keep out of git) |

You can have any combination of these. Qwen loads all of them when you start a session.

If your repository already has an `AGENTS.md` file for other AI tools, Qwen reads that too. No need to duplicate instructions.

#### When to use `.qwen/QWEN.local.md`

Use it for **project-specific but personal** instructions — things that belong to this project but shouldn't be shared with the team:

- Your own cluster ID, container registry namespace, or cloud account
- A personal debug command that hardcodes your local environment
- Notes you want Qwen to know about your work-in-progress, but not commit

It loads **after** the shared project `QWEN.md`, so your local instructions can supplement or override the team's.

**You must gitignore it yourself.** Although `.qwen/` is often treated as a local directory, qwen-code does not generate a `.gitignore` for you, and some projects commit `.qwen/settings.json`. Add this line to your `.gitignore` (or to your global git ignore):

```
.qwen/QWEN.local.md
```

### Generate one automatically with `/init`

Run `/init` and Qwen will analyze your codebase to create a starter QWEN.md with build commands, test instructions, and conventions it finds. If one already exists, it suggests additions instead of overwriting.

### Reference other files

You can point QWEN.md at other files so Qwen reads them too:

```markdown
See @README.md for project overview.

# Conventions

- Git workflow: @docs/git-workflow.md
```

Use `@path/to/file` anywhere in QWEN.md. Relative paths resolve from the QWEN.md file itself.

---

## Auto-memory: what Qwen learns about you

Auto-memory runs in the background. After each of your conversations, Qwen quietly saves useful things it learned — your preferences, feedback you gave, project context — so it can use them in future sessions without you repeating yourself.

This is different from QWEN.md: you don't write it, Qwen does.

### What Qwen saves

Qwen looks for four kinds of things worth remembering:

| What                    | Examples                                                 |
| ----------------------- | -------------------------------------------------------- |
| **About you**           | Your role, background, how you like to work              |
| **Your feedback**       | Corrections you made, approaches you confirmed           |
| **Project context**     | Ongoing work, decisions, goals not obvious from the code |
| **External references** | Dashboards, ticket trackers, docs links you mentioned    |

Qwen doesn't save everything — only things that would actually be useful next time.

### Where it's stored

Auto-memory files live at `~/.qwen/projects/<project>/memory/`. All branches of the same checkout share the same memory folder, so what Qwen learns in one branch is available in others. Each linked git worktree gets its own memory folder, matching the per-worktree isolation of chats and other session state — repository-wide conventions you want in every worktree belong in [team memory](<#team-memory-shared-with-collaborators>).

Everything saved is plain markdown — you can open, edit, or delete any file at any time.

`MEMORY.md` is a compact index, rather than the full memory store. Its generated body and the portion loaded into prompts share a limit of 200 lines and 25,000 UTF-16 code units; truncation notices are added separately. Line endings are normalized before prompt budgeting. Under size pressure, complete ordinary entries (up to 150 code units) get space before longer entries, and retained entries keep their original order. An entry that cannot fit is skipped whole, so its Markdown link is never cut in the middle. Keep detail in the linked topic files.

#### Pinned memory

Put hand-curated documents that automatic memory maintenance should preserve
under `pinned/` in a managed-memory directory, for example
`~/.qwen/projects/<project>/memory/pinned/architecture.md` or
`~/.qwen/memories/pinned/preferences.md`. Use the same frontmatter as other
memory documents. Valid pinned files are readable by Qwen and are included the
next time `MEMORY.md` is rebuilt, under the same size and file-count limits as
other memory documents.

Only the top-level `pinned/` directory directly inside a managed-memory root is
protected; nested directories such as `memory/project/pinned/` are ordinary
writable memory. Automatic extraction and Dream workers match the reserved
directory name case-insensitively.

Automatic extraction is instructed to leave pinned records and their valid
index entries unchanged, while Dream is instructed to skip `pinned/` during
consolidation. Both automatic extraction and forked Dream workers, including
background cleanup, enforce the pinned-file boundary on their write and edit
tools, including paths that resolve through a symlink into `pinned/`.
Command-line deletion is closed off too: only the project Dream worker holds a
shell, and its policy is read-only, while automatic extraction and the
user-memory Dream worker hold no shell at all. You still control
these files directly and can remove them with an explicit `/forget` request.

> **Note:** The visible `/dream` slash command runs on the main Agent. It
> receives the same skip instruction, but does not yet receive the forked
> worker's deterministic per-turn tool gate.

### Periodic cleanup

Qwen periodically goes through its saved memories to remove duplicates and clean up outdated entries. This runs automatically in the background once a day after enough sessions have accumulated. You can trigger it manually with `/dream` if you want it to run now.

Your session continues normally while cleanup runs in the background.

### Turning it on or off

Auto-memory is on by default. To toggle it, open `/memory` and use the switches at the top. You can turn off just the automatic saving, just the periodic cleanup, or both.

You can also set them in `~/.qwen/settings.json` (applies to all projects) or `.qwen/settings.json` (this project only):

```json
{
  "memory": {
    "enableManagedAutoMemory": true,
    "enableManagedAutoDream": true
  }
}
```

### Structured recall (opt-in)

By default Qwen reads memory as a flat `MEMORY.md` index. The structured protocol replaces that with a hierarchical memory tree, injects only the subtree relevant to your current request, and gives Qwen a `search_memory` tool to pull full entries on demand — which costs fewer tokens once a memory collection grows past a handful of files.

It is off by default. Turn it on in `settings.json` (restart required):

```json
{
  "memory": {
    "enableStructuredRecall": true
  }
}
```

or for a single run with `QWEN_CODE_MEMORY_STRUCTURED_RECALL=1`.

Turning it on starts a background metadata migration: each memory file gains the frontmatter (category, keywords, usage scenarios) that recall needs. It runs in small batches of at most 10 files per turn and only rewrites frontmatter, never the body of a note. While the setting is off the migration is never scheduled, so nothing is rewritten and no background model calls are made.

### Team memory (shared with collaborators)

By default, auto-memory is **private to you** — it lives under your home directory and is never shared. Team memory is an opt-in tier that the whole team shares **through git**.

When enabled, Qwen gains a third memory directory at `.qwen/team-memory/` **inside the repository**. It uses the same one-file-per-memory layout and `MEMORY.md` index as the private tiers. Because it is committed to the repo, it is shared with every collaborator the normal way: you `git pull` to receive teammates' memories and commit/push to share yours. Qwen routes durable, project-wide knowledge here — conventions every contributor must follow, shared reference pointers (trackers, dashboards) — while personal and fast-decaying notes stay private.

Enable it per project (or globally) in `settings.json`:

```json
{
  "memory": {
    "enableTeamMemory": true
  }
}
```

It is **off by default**. Keep these caveats in mind:

- **It is source-controlled and visible to everyone with repo access.** Treat a team memory like committing to the repo.
- **Secrets are blocked.** Writes to `.qwen/team-memory/` are scanned for credentials (API keys, tokens, private keys); a detected secret is rejected, never written. The scan is a backstop, not a guarantee — don't put sensitive data there.
- **Changes are reviewable.** Team memory writes appear in `git status` / the PR diff like any other file, so they can be reviewed before they're committed. In the default approval mode Qwen also asks before each team write; in `AUTO_EDIT`/YOLO mode (where you've opted into auto-approval) they are applied without a prompt but still surface in the diff.
- **The directory must be git-tracked.** If your project's `.gitignore` excludes `.qwen/*`, re-include the path so it can be shared:

  ```gitignore
  !.qwen/team-memory/
  !.qwen/team-memory/**
  ```

  Caveat: use the file-glob ignore form (`.qwen/*`), not a directory form with a trailing slash (`.qwen/`). A directory-form ignore makes git skip the folder entirely, so a `!`-reinclude below it is a no-op and the team tier stays silently empty in git. Qwen warns once at startup when the tier is enabled but its directory is git-ignored or outside any git repository, so this misconfiguration does not pass unnoticed.

`QWEN_CODE_MEMORY_TEAM=1` / `=0` overrides the setting for a single run.

### Automatic git sync (optional)

By default you share team memory with the normal git workflow (`pull` to receive, `commit`/`push` to share). To have Qwen do it for you, enable sync:

```json
{
  "memory": {
    "enableTeamMemory": true,
    "enableTeamMemorySync": true
  }
}
```

When on, at session start Qwen best-effort syncs the `.qwen/team-memory/` directory: it rebuilds the shared `MEMORY.md` index, fast-forward-pulls collaborators' updates **first**, then commits your team-memory changes on top, and pushes **only that sync commit** (via an explicit single-branch refspec) — so the index you load reflects the latest. It only **stages** the team directory (your other working changes are never committed), and never blocks the session on a git failure. Off by default. `QWEN_CODE_MEMORY_TEAM_SYNC=1` / `=0` overrides the setting for a single run.

Two things to know before enabling it:

- **The fast-forward pull acts on your whole current branch, not just `.qwen/team-memory/`** (git has no path-scoped pull). So sync will fast-forward your branch to the remote tip. The push, by contrast, is scoped: it publishes **only the commit this sync just created**, so it never pushes other unpushed commits you have — if your branch is already ahead of upstream, sync commits locally and skips the push. Enable it on branches where the fast-forward pull is fine — or run it on a dedicated checkout.
- **A diverged branch is left untouched** (`--ff-only` never merges). When that happens sync simply does nothing that session; resolve the divergence (`git pull`) and it resumes. A branch with no upstream (no tracking configuration) still commits locally but skips the push — there is nowhere to push to.

---

## Commands

### `/memory`

Opens the Memory panel. From here you can:

- Turn auto-memory saving on or off
- Turn periodic cleanup (dream) on or off
- Open your personal QWEN.md (`~/.qwen/QWEN.md`)
- Open the project QWEN.md
- Browse the auto-memory folder

### `/memory migrate-team`

Migrates the **team memory** tier (`.qwen/team-memory/`) to the structured metadata format: each file gains the frontmatter (name, description, category, keywords, usage scenarios) that memory recall needs. Personal and project memory are migrated automatically in the background once [structured recall](<#structured-recall-opt-in>) is enabled; team memory is shared through git, so the rewrite runs only on explicit request and shows up as a reviewable commit. Run it once per repository in a trusted workspace with team memory enabled.

### `/init`

Generates a starter QWEN.md for your project. Qwen reads your codebase and fills in build commands, test instructions, and conventions it discovers.

### `/remember <text>`

Immediately saves something to auto-memory without waiting for Qwen to pick it up automatically:

```
/remember always use snake_case for Python variable names
/remember the staging environment is at staging.example.com
```

### `/forget <text>`

Removes auto-memory entries that match your description:

```
/forget old workaround for the login bug
```

### `/dream`

Runs the memory cleanup now instead of waiting for the automatic schedule:

```
/dream
```

---

## Troubleshooting

### Qwen isn't following my QWEN.md

Open `/memory` to see which files are loaded. If your file isn't listed, Qwen can't see it — make sure it's in the project root or `~/.qwen/`.

Instructions work better when they're specific:

- ✓ `Use 2-space indentation for TypeScript files`
- ✗ `Format code nicely`

If you have multiple QWEN.md files with conflicting instructions, Qwen may behave inconsistently. Review them and remove any contradictions.

### I want to see what Qwen has saved

Run `/memory` and select **Open auto-memory folder**. All saved memories are readable markdown files you can browse, edit, or delete.

### Qwen keeps forgetting things

If auto-memory is on but Qwen doesn't seem to remember things across sessions, try running `/dream` to force a cleanup pass. Also check `/memory` to confirm both toggles are enabled.

For things you always want Qwen to remember, add them to QWEN.md instead — auto-memory is best-effort, QWEN.md is guaranteed.


</details>
