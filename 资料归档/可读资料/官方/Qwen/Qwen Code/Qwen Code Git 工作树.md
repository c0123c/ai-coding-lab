# Qwen Code Git 工作树

讲解会话启动或中途创建隔离目录、子 Agent 隔离、退出保留删除、恢复与自动清理，帮助管理并行代码修改。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-28](<https://github.com/QwenLM/qwen-code/commit/1b46d63fb7a0c3f2edde9ab6803bb48ed64eced6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/worktree.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code Git 工作树.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 工作树

> 在临时 [git worktree](<https://git-scm.com/docs/git-worktree>) 中隔离实验工作，而无需离开当前会话。当模型即将进行广泛的编辑（您希望与主结帐分开）时，或者当您希望子代理在其自己的沙箱中工作时，此功能非常有用。

## 快速入门

### 在工作树内启动会话（`--worktree` 标志）

如果您预先知道整个会话应在工作树内运行，请在启动时传递 `--worktree`：

```bash
# Auto-generated slug (e.g. tender-jemison-037f0a)
qwen --worktree

# Explicit name
qwen --worktree my-feature

# `=` form (recommended when also passing a positional prompt — see tip below)
qwen --worktree=my-feature

# PR reference — fetches refs/pull/<N>/head from `origin`
qwen --worktree=#4174
qwen --worktree https://github.com/QwenLM/qwen-code/pull/4174

# Continue a previous --worktree session — re-attaches to the existing dir
qwen --resume <session-id> --worktree=my-feature
```

> **提示 - 裸露的 `--worktree` 后跟位置提示不明确。** 因为 `--worktree` 采用可选值，所以 `qwen --worktree "say hi"` 使 yargs 将 `"say hi"` 作为 slug 消耗（并因空格而拒绝它）。使用以下之一：
>
> - `qwen --worktree=my-feature "say hi"`（始终有效 - 通过 `=` 显式 slug）
> - `qwen "say hi" --worktree`（位置在前，标志在末尾 → 自动 slug）
> - `qwen --worktree --approval-mode yolo "say hi"`（它们之间的任何标志都锚定裸形式）

> **提示 - `qwen --resume --worktree foo`（无会话 ID）在首次使用时显示空选择器。** 选择器范围为所选工作树的会话存储；不列出在该工作树之外启动的会话。要恢复在 `foo` 内启动的会话，请直接使用 `qwen --resume <id> --worktree foo` — CLI 重新附加到现有的 `foo/` 目录，而不是重新创建它。

`process.cwd()` 和模型的工作区在第一轮运行之前切换到工作树。使用`Ctrl+C`退出两次，[退出对话框](<#退出对话框-ctrlc--ctrld>)提示保留或删除工作树。

`--worktree` 标志不能与 `--acp`/`--experimental-acp` 组合 — 对于 ACP 主机（如 Zed），请将工作树路径作为 `loadSession`/`newSession` 请求的 `cwd` 传递。

### 或者在会议中询问

或者，用简单的语言要求 Qwen Code 从现有会话中创建工作树：

```text
> start a worktree called experiment-a
Worktree experiment-a created on branch worktree-experiment-a
.qwen/worktrees/experiment-a
```

从此时起，模型通过 `.qwen/worktrees/experiment-a/` 路由每个文件编辑和 shell 命令。您的原始工作目录未受影响。

完成后：

```text
> exit the worktree and remove it
Removed worktree experiment-a (branch worktree-experiment-a)
```

如果您想稍后返回，请要求退出并将工作树保留在磁盘上：

```text
> exit the worktree but keep it
Kept worktree experiment-a at .qwen/worktrees/experiment-a
```

## 何时使用工作树

工作树在四个独立路径中激活：

|触发|会发生什么|
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
|您使用 `--worktree` 启动|CLI 在任何模型轮流运行之前创建工作树，并将会话 chdirs 到其中。首先获取 PR 表单（`#N`、完整 URL）。|
|您在会话中明确要求工作树|型号为`enter_worktree`；后续的文件编辑都在其中。|
|你明确要求离开|模型用 `keep` 或 `remove` 调用 `exit_worktree`。|
|模型生成一个启用隔离的子agent|如果 agent 没有差异，则会自动创建并清理一次性工作树 (`agent-<hex>`)。|

这两个会话中工具（`enter_worktree` / `exit_worktree`）被故意限制在明确的措辞后面——说“修复这个错误”或“创建一个分支”将**不会**触发它们。您必须说“使用工作树”、“启动工作树”或“在工作树中”之类的内容。 `--worktree` CLI 标志没有这样的保护；它总是在存在时创建一个。

## 创建了什么

每个 Qwen 管理的工作树都放置在项目的 `.qwen` 目录下：

```
<repoRoot>/.qwen/worktrees/<slug>/         # Working directory
                          ↳ branch worktree-<slug>   # Created off your current branch
```

- **Slug** — 字母、数字、点、下划线、连字符；最大 64 个字符。如果您不指定名称，则会自动生成 `<adjective>-<noun>-<6hex>` 段（例如 `tender-jemison-037f0a`）。 PR 参考文献产生 `pr-<N>`。
- **分支** - 总是`worktree-<slug>`，从您在请求工作树时签出的任何分支分支（不一定是主工作树的`HEAD`）。对于 PR 工作树，分支是 `worktree-pr-<N>`，并且基于 `FETCH_HEAD`（PR 的尖端位于 GitHub 侧）而不是您的本地分支。
- **Hooks** - 工作树的 `core.hooksPath` 自动指向主存储库的 `.husky/`（首选）或 `.git/hooks/`，因此工作树内的提交仍然会触发现有的预提交/提交消息挂钩。
- **可选符号链接** - `worktree.symlinkDirectories`中列出的目录（请参阅[设置](<#设置>)）从主存储库符号链接到新工作树中，因此可以重用像`node_modules`这样的重型目录，而无需重新安装。

通用工作树路径是**不可配置的** - 它必须位于`<repoRoot>/.qwen/worktrees/`下，以便CLI可以在重新启动和陈旧清理扫描时找到它。 （不相关的 `agents.arena.worktreeBaseDir` 设置仅控制 [Agent Arena](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/arena.md>) 工作树，它们在 `~/.qwen/arena/` 下使用单独的路径树。）

## 页脚和状态行

当工作树处于活动状态时，页脚在其自己的行上显示一个暗淡的指示器：

```
⎇ worktree-experiment-a (experiment-a)
```

如果您使用 [ 自定义状态行脚本 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/status-line.md>)，它还会在通过管道传输到 stdin 的 JSON 有效负载中接收 `worktree` 对象：

```json
{
  "worktree": {
    "name": "experiment-a",
    "path": "/path/to/repo/.qwen/worktrees/experiment-a",
    "branch": "worktree-experiment-a",
    "original_cwd": "/path/to/repo",
    "original_branch": "main"
  }
}
```

当工作树处于活动状态时，有效负载字段仅**存在**，因此`null`检查（`input.worktree?.name`）就足够了。

如果您的自定义状态行已呈现工作树信息，您可以隐藏内置的页脚行以避免重复 - 请参阅下面的 [ 设置 ](<#设置>)。

## 退出对话框 (Ctrl+C / Ctrl+D)

当工作树处于活动状态时按退出快捷键两次将打开 ** 工作树退出对话框 **，而不是关闭 CLI：

```
⎇ Active worktree: "experiment-a" (worktree-experiment-a)

  • 2 new commit(s) on worktree-experiment-a
  • 3 uncommitted file(s)
  Removing the worktree will discard everything above.

What would you like to do?
  ○ Keep worktree (exit without deleting)
  ○ Remove worktree and branch (discards 2 commit(s), 3 file(s))
  ○ Cancel (stay in session)
```

该对话框检查打开时的工作树（`git status --porcelain` + `git rev-list <baseHEAD>..HEAD`）并显示两个计数，以便您确切地知道要丢弃的内容。 `ESC` 取消。

如果 `git status` 本身失败（例如索引损坏，工作树目录在 CLI 下被删除），对话框会显示 `⚠ Could not measure worktree state` 警告，并且计数可能不可靠 — 选择 ** 保留 ** 或**取消**，直到您诊断出潜在的回购问题。

## `--resume`恢复

活动工作树绑定与会话记录一起保存到 sidecar 文件中：

```
<chatsDir>/<sessionId>.worktree.json
```

当您启动 CLI 与 `--resume <sessionId>` （或从以下位置选择会话 `/resume`），三件事在整个过程中一致发生 **互动式 TUI**, **无头的 `-p`**, 和 **ACP/Zed** 模式：

1. sidecar 已加载，并且工作树目录已验证是否仍然存在于磁盘上。
2. 如果处于活动状态，模型会在下一个提示时收到一次性提醒：
   ```
   [Resumed] Active worktree: "<slug>" at <path> (branch: <branch>). Continue using this path for all file operations.
   ```
3. 如果工作树目录在会话之间被删除，过时的 sidecar 会自动清理 - 没有错误，恢复将在没有工作树上下文的情况下继续。

每种模式都选择自己的注入机制，但用户可见的行为是相同的：

|模式|机制|
| ----------------- | ------------------------------------------------------------------------------------------------------ |
|互动（TUI）|`INFO` 历史记录项 + 下一个用户提示上的系统提醒前缀。|
|无头 (`-p`)|提示上的 `<system-reminder>` 前缀 + 输出流中的 `worktree_restored` JSON 系统事件。|
|ACP（例如 Zed）|待处理通知附在下一个 `prompt()` 通话中。|

该模型是**而不是**自动`chdir`进入工作树 - 提醒是让它通过工作树路径路由编辑的原因。

## 子Agent隔离

`agent` 工具接受可选的 `isolation: "worktree"` 参数。设置后，Qwen Code 在子 agent 启动之前在 `<repoRoot>/.qwen/worktrees/agent-<7hex>/` 处创建一个临时工作树，并且：

- **无更改** → 当agent完成时，工作树将自动删除。
- **有更改** → 工作树被保留；它的路径和分支被附加到 agent 的结果中，例如
  ```
  …agent output…
  [worktree preserved: /path/to/.qwen/worktrees/agent-3f2a1b9 (branch worktree-agent-3f2a1b9)]
  ```
查看差异并手动合并或删除它。

两个限制：

- `isolation: "worktree"` 需要一个非分叉 `subagent_type` - 分叉子 agents (`subagent_type: "fork"`) 重用父级的完整对话上下文，因此隔离它们会从工作树中分离意图。
- Agents使用`isolation: "worktree"`遵循默认后台行为；当 agent 报告完成时，清理运行。设置 `run_in_background: false` 以获得内联结果。未命名的调用者拥有的 `working_dir` 启动在前台运行；显式后台执行被拒绝，而配置的后台执行（子代理定义中的`background: true`）在顶层被拒绝，并在嵌套时降级到前台运行，因为它们的生命周期是在外部管理的。

### 自动陈旧清理

每次 CLI 启动时都会收获在崩溃或 `--no-cleanup` 关闭中幸存下来的短暂 agent 工作树，并采用保守的故障关闭规则：

|守卫|行为|
| --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|Slug 必须匹配 `agent-<7hex>` 模式|其他名字从未被触及。您准确命名为 `agent-<7hex>` 的工作树与临时 agent 工作树没有区别，并且 ** 是 ** 扫描候选者。|
|目录 `mtime` > 30 天|较新的条目将被跳过。|
|任何未提交的工作——已跟踪、未跟踪或 git 忽略|跳过该条目（不要删除）。只有一次性构建输出、符号链接和会话标记除外 - 请参阅下面的注释。|
|任何无法从远程访问的提交|跳过该条目（不要删除）。|
|读取 git state 时出现任何错误|跳过该条目（不要删除）。|

命名用户工作树（`enter_worktree` slugs）永远不会被自动清理，但有一个例外：保留的 `agent-<7hex>` 形状，扫描无法将其与临时 agent 工作树区分开来，一旦它不包含未提交的内容和未合并的提交，就会在 30 天后收获。为您想要保留的作品选择任何其他名称。

未提交的内容防护读取 `git status --porcelain --untracked-files=normal --ignored=matching`，因此即使您的忽略规则隐藏的内容 - `.env`、`.qwen/pr-drafts/` - 也会保留旧的 `agent-<7hex>` 工作树。唯一的豁免是：

- **一次性构建输出** — 一个 _被忽略_ 条目其 _第一_ 路径段是 `node_modules`, `dist` 或 `coverage`，以便输出和 git 在其下列出的任何内容，包括一个被忽略的文件（`!! dist/archive.zip` 在一个 `*.zip` 规则）。也是一个被忽视的 _目录_ 嵌套在结帐内，其自己的名称是这三个之一（单一存储库的 `packages/app/node_modules/`) — 但仅当 git 折叠条目时 _在_ 该目录，需要一个与目录本身匹配的忽略规则（`node_modules/`，或者裸露的 `node_modules`）。与目录匹配的规则 _内容_ 相反（`**/node_modules/*`，或本地包 `node_modules/**`）使 git 单独列出文件（`!! packages/app/node_modules/x/`），这不是豁免并保留结帐。嵌套被忽略 _文件_ 使用这些名称之一不是构建输出，并且仍然保留工作树，所以 `go test -coverprofile=coverage` 写作 `packages/app/coverage` 保留结帐 - 但根目录下没有存储任何内容 `dist/`, `coverage/` 或 `node_modules/` 被保留。同一棵树离开了 _未追踪的_，所以 git 说 `?? dist/`，不免除并保留结帐。请注意 `??` 描述了 _渲染条目_，而不是缺少规则：git 折叠了未跟踪的父级（`?? packages/`）每当它保存任何非忽略的未跟踪文件时，并将其中的忽略文件分别列出为 `!!` 同时。条目呈现的常见原因 `??` 没有忽略规则到达它 - 包括拼写未到达该路径的规则，因为根 `node_modules/**` 仅覆盖根级树 - 或者规则是在工作树分支被切断后提交的。
- **符号链接** - 删除结帐会取消它们的链接，并且永远不会触及它们指向的内容，这就是保持`worktree.symlinkDirectories`结帐可收获的原因。嵌套值 (`tools/cache`) 使 git 将子树折叠为单个 `?? tools/` 条目，因此折叠目录仅当它至少包含一个条目并且其中的所有内容本身都是符号链接时才符合资格；链接旁边的一个真实文件（或一个空目录，其中不包含可言的链接）保留了工作树。
- **守护进程的`.qwen-session`标记**，只要git将其列为未跟踪或忽略即可。存储库实际跟踪的标记也不例外：对跟踪路径的编辑与用户工作没有区别，并且 `writeWorktreeSessionMarker` 无条件覆盖跟踪标记，因此守护进程本身可以是该编辑的作者 - 然后保留保留，直到标记被提交或恢复。

由于包含特殊字符而被 git 引用的路径不会被解析并计为工作。

## `exit_worktree action="remove"` 上的安全防护装置

删除目录和分支之前会触发三个独立的守卫：

1. **会话所有权** - 每个工作树都带有一个 sidecar 标记以及创建它的会话 ID。尝试删除它的另一个会话被拒绝，并出现一个明确的错误，指向手动逃生舱口的 `git worktree remove`。
2. **脏工作树** - 未提交的跟踪或未跟踪的更改阻止删除。通过 `discard_changes: true` 进行覆盖。 （绕过需要明确的用户确认 - `action: "remove"` 在 AUTO_EDIT 模式下永远不会自动批准。）
3. **未合并提交** — 在`worktree-<slug>`上提交，没有其他本地分支或远程引用无条件地指向块删除；没有“放弃提交”标志，因为丢失已提交的工作很少是用户的意思。首先在其他地方合并、推送或重命名分支。

相同的三个防护装置也适用于 `WorktreeExitDialog → Remove` 按钮。

## 设置

两种设置塑造了通用工作树体验：

|钥匙|类型|默认|效果|
| --------------------------------- | ---------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ui.hideBuiltinWorktreeIndicator` |布尔值| `false`     |隐藏内置的 `⎇ worktree-… (…)` 页脚行。 `worktree` 字段仍传递到自定义状态行脚本。仅当您的状态行已呈现工作树时才设置为 `true` — 否则您将失去所有 UI 可供性。|
| `worktree.symlinkDirectories`     | `string[]` | `undefined` |主存储库下的目录，用于在创建时符号链接到每个通用工作树。路径是相对于仓库根的；绝对路径和任何包含 `..` 的条目都会被拒绝。丢失的源和现有目标将被静默跳过（不覆盖）。|

示例：

```jsonc
// ~/.qwen/settings.json or <repo>/.qwen/settings.json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".turbo", "dist"],
  },
}
```

适用于 ALL 工作树创建路径：`--worktree` 标志、`enter_worktree` 工具和 `agent isolation: "worktree"`。

与一般工作树无关但值得了解的设置：

- `agents.arena.worktreeBaseDir` — 控制 **Agent 竞技场 ** 工作树放置（默认 `~/.qwen/arena`）。不影响始终位于 `<repoRoot>/.qwen/worktrees/` 下的通用工作树。

`worktree.sparsePaths` 还没有架构 - 这是一个路线图项目（请参阅 [ 限制 ](<#局限性>)）。

## 工具参考

### `enter_worktree`

```json
{ "name": "experiment-a" }
```

|领域|类型|必填|注释|
| ------ | ------ | -------- | ------------------------------------------------------------------------------------------ |
| `name` |字符串|不|蛞蝓。字母、数字、点、下划线、连字符；最大 64 个字符。省略时自动生成。|

在以下情况下拒绝运行：

- CLI 不在 git 存储库中。
- 当前工作目录已经在`.qwen/worktrees/`内（无嵌套工作树）。

### `exit_worktree`

```json
{ "name": "experiment-a", "action": "remove", "discard_changes": false }
```

|领域|类型|必填|注释|
| ----------------- | ---------------------- | ------------------------------------- | ------------------------------------------------------------------ |
| `name`            |字符串|是的|必须与 `enter_worktree` 中使用的段匹配。|
| `action`          | `"keep"` \| `"remove"` |是的|`keep` 保留 dir + 分支； `remove` 删除两者。|
| `discard_changes` |布尔值|仅当 `action="remove"` 且脏时|覆盖脏树守卫。对`action="keep"`没有影响。|

`action: "remove"` 始终提示确认，包括在 `AUTO_EDIT` 批准模式下 - 它被视为破坏性 shell 操作，而不是仅提供信息的工具。

### `agent` — `isolation` 参数

```json
{
  "subagent_type": "my-agent",
  "description": "…",
  "prompt": "…",
  "isolation": "worktree"
}
```

|领域|类型|必填|注释|
| ----------- | ------------ | -------- | ------------------------------------------------------------------------------------------------- |
| `isolation` | `"worktree"` |不|在新的 `agent-<7hex>` 工作树中运行 agent。需要设置 `subagent_type`（无叉）。|

有关 agent 工具参考的其余部分，请参阅 [Sub-Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents.md>)。

## CLI 参考

### `--worktree [name | #N | url]`

```bash
qwen --worktree                                               # auto-generate slug
qwen --worktree my-feature                                    # explicit slug
qwen --worktree=my-feature                                    # = form
qwen --worktree=#123                                          # PR reference
qwen --worktree https://github.com/owner/repo/pull/123        # PR URL
```

|输入|结果|
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------- |
|裸旗（无值）|自动段 `<adjective>-<noun>-<6hex>`，分支 `worktree-<slug>`，基础 = 当前分支。|
|普通蛞蝓|分支 `worktree-<slug>`，基数 = 当前分支。 Slug 验证：letters/digits/dot/underscore/hyphen，最大 64 个字符。|
|`#N` 或 `<github-url>/pull/N`|Slug `pr-<N>`，分支 `worktree-pr-<N>`，在 `git fetch origin pull/<N>/head` 后基 = `FETCH_HEAD`（30 秒超时）。|

`--worktree` 不能与 `--acp` / `--experimental-acp` 组合。

当 `--worktree` 与 `--resume <session-id>` 组合时，工作树获胜：恢复会话的已保存工作树（如果有）被覆盖，并且 stderr 行 + 第一个提示提醒报告覆盖情况。

对于交互式 (TUI) 和无头 (`-p`) 模式，会自动创建工作树，并在第一次转动之前将会话 chdirs 添加到其中。

PR-获取失败模式（退出代码！= 0，未创建工作树）：

|原因|留言摘录|
| ----------------------------- | ---------------------------------------------------------- |
|缺少 `origin` 遥控器| `requires an "origin" remote that points at GitHub`        |
|原点上不存在 PR| `Failed to fetch PR #<N>: the PR does not exist on origin` |
|30秒网络超时| `Failed to fetch PR #<N>: timed out after 30s`             |
|PR 数字超出范围/零| `Invalid PR number`                                        |

## 局限性

当前阶段有意不实施以下项目：

- **没有稀疏结帐。**大型 monorepos 检查完整的树。 （`worktree.sparsePaths` 是路线图项目。）
- **没有 tmux 集成。** CLI 不会在新的 tmux 窗口中生成工作树会话。
- **工作树是用于会话存储的单独“项目”。**以`--worktree foo`开始的会话保存在该工作树的聊天目录下；要稍后恢复它们，您必须再次通过 `--worktree foo`。没有 `--worktree` 启动的会话将保存在主结帐下，并且不会出现在工作树的简历选择器中。
- **没有跨段会话覆盖。** `qwen --resume <sid> --worktree second`，其中`<sid>`是用`--worktree first`创建的，将无法找到会话 - 会话和工作树由`projectHash(cwd)`紧密绑定。要在现有会话上切换工作树，您必须退出，然后使用新的 `--worktree` 和新的提示重新启动。未来的架构更改（将存储锚定在存储库根而不是 `cwd`）将解除此限制。
- ** 会话中 `enter_worktree` 确实 NOT 切换 `process.cwd()` 或 `Config.targetDir`。** 该工具使用仅模型上下文约定（请参阅[子Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents.md>)）。只有启动`--worktree`标志实际上切换进程工作目录。
- **其他参数字段中的相对路径被解析BEFORE工作树chdir。**路径选择标志（`--mcp-config`，`--openai-logging-dir`，`--json-file`，`--input-file`，当设置 `--worktree` 时，`--telemetry-outfile`、`--include-directories`）将针对启动 cwd 标准化为绝对路径。不在此列表中的其他路径形状的 argv 字段仍然根据工作树 cwd 进行解析 - 使用绝对路径是安全的。

跟踪 `docs/design/worktree.md` 中的路线图。

## 故障排除

**页脚没有显示工作树指示器，即使我刚刚创建了一个。**
检查`ui.hideBuiltinWorktreeIndicator`是否未设置为`true`。还要确认该工具的成功消息中的 slug 非空。

**`--resume` 无法恢复我的工作树。**
检查 `<chatsDir>/<sessionId>.worktree.json` 是否存在。当工作树目录消失时，CLI 会自动删除 sidecar，因此丢失的 sidecar 加上丢失的目录是正常的“没有工作树可恢复”状态 - 不是错误。运行 `--debug` 并 grep for `restoreWorktreeContext` 查看原因。

**`exit_worktree` 表示“由不同的会话创建”。**
这是会话所有权保护。恢复原始会话并从那里退出，或手动运行建议的 `git worktree remove …` 命令。

**过时的`agent-<hex>`工作树不断堆积。**
30天的截止时间是保守的；使用 `git worktree list && git worktree remove <path>` 手动扫描，或者等待 — 在 30 天标记之后的下一个 CLI 启动将收获它们，只要它们没有未提交的工作（跟踪、未跟踪或超出一次性构建输出和符号链接的 git 忽略）并且没有未合并的提交。使用 `--debug` 和 grep for `cleanupStaleAgentWorktrees: keeping` 来查看扫描拒绝了哪个条目以及原因，或者使用 `worktreeHasWork: waiving` 来查看哪些条目授权删除以及在哪些豁免下。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Worktrees

> Isolate experimental work in a temporary [git worktree](<https://git-scm.com/docs/git-worktree>) without leaving your current session. Useful when the model is about to make wide-ranging edits you want to keep separate from your main checkout, or when you want a subagent to work in a sandbox of its own.

## Quick Start

### Start the session inside a worktree (`--worktree` flag)

If you know up front that the entire session should run inside a worktree, pass `--worktree` at launch:

```bash
# Auto-generated slug (e.g. tender-jemison-037f0a)
qwen --worktree

# Explicit name
qwen --worktree my-feature

# `=` form (recommended when also passing a positional prompt — see tip below)
qwen --worktree=my-feature

# PR reference — fetches refs/pull/<N>/head from `origin`
qwen --worktree=#4174
qwen --worktree https://github.com/QwenLM/qwen-code/pull/4174

# Continue a previous --worktree session — re-attaches to the existing dir
qwen --resume <session-id> --worktree=my-feature
```

> **Tip — bare `--worktree` followed by a positional prompt is ambiguous.** Because `--worktree` takes an optional value, `qwen --worktree "say hi"` makes yargs consume `"say hi"` as the slug (and reject it because of the space). Use one of:
>
> - `qwen --worktree=my-feature "say hi"` (always works — explicit slug via `=`)
> - `qwen "say hi" --worktree` (positional first, flag at the end → auto slug)
> - `qwen --worktree --approval-mode yolo "say hi"` (any flag between them anchors the bare form)

> **Tip — `qwen --resume --worktree foo` (no session ID) shows an empty picker on first use.** The picker scopes to the chosen worktree's session storage; sessions started outside that worktree are not listed. To resume a session that was started inside `foo`, use `qwen --resume <id> --worktree foo` directly — the CLI re-attaches to the existing `foo/` directory rather than re-creating it.

`process.cwd()` and the model's workspace are switched to the worktree before the first turn runs. Exit with `Ctrl+C` twice and the [Exit Dialog](<#exit-dialog-ctrlc--ctrld>) prompts to keep or remove the worktree.

The `--worktree` flag cannot be combined with `--acp`/`--experimental-acp` — for ACP hosts (like Zed), pass the worktree path as the `cwd` of the `loadSession`/`newSession` request instead.

### Or ask mid-session

Alternatively, ask Qwen Code in plain language to create a worktree from inside an existing session:

```text
> start a worktree called experiment-a
Worktree experiment-a created on branch worktree-experiment-a
.qwen/worktrees/experiment-a
```

From this point on, the model routes every file edit and shell command through `.qwen/worktrees/experiment-a/`. Your original working directory is untouched.

When you are done:

```text
> exit the worktree and remove it
Removed worktree experiment-a (branch worktree-experiment-a)
```

If you want to come back later, ask to exit with the worktree kept on disk instead:

```text
> exit the worktree but keep it
Kept worktree experiment-a at .qwen/worktrees/experiment-a
```

## When Worktrees Are Used

Worktrees are activated in four independent paths:

| Trigger                                         | What happens                                                                                                                   |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| You launch with `--worktree`                    | The CLI creates the worktree before any model turn runs and chdirs the session into it. PR forms (`#N`, full URL) fetch first. |
| You explicitly ask for a worktree mid-session   | Model calls `enter_worktree`; subsequent file edits go inside it.                                                              |
| You explicitly ask to leave                     | Model calls `exit_worktree` with `keep` or `remove`.                                                                           |
| Model spawns a sub-agent with isolation enabled | A throwaway worktree (`agent-<hex>`) is created automatically and cleaned up if the agent has no diffs.                        |

The two mid-session tools (`enter_worktree` / `exit_worktree`) are deliberately gated behind explicit phrasing — saying "fix this bug" or "create a branch" will **not** trigger them. You must say something like "use a worktree", "start a worktree", or "in a worktree". The `--worktree` CLI flag has no such guard; it always creates one when present.

## What Gets Created

Every Qwen-managed worktree is placed under your project's `.qwen` directory:

```
<repoRoot>/.qwen/worktrees/<slug>/         # Working directory
                          ↳ branch worktree-<slug>   # Created off your current branch
```

- **Slug** — letters, digits, dot, underscore, hyphen; max 64 chars. If you don't specify a name, an `<adjective>-<noun>-<6hex>` slug is auto-generated (e.g. `tender-jemison-037f0a`). PR references produce `pr-<N>`.
- **Branch** — always `worktree-<slug>`, branched from whichever branch you have checked out when you ask for the worktree (not necessarily the main working tree's `HEAD`). For PR worktrees the branch is `worktree-pr-<N>` and is based on `FETCH_HEAD` (the PR's tip on the GitHub side) rather than your local branch.
- **Hooks** — the worktree's `core.hooksPath` is automatically pointed at the main repo's `.husky/` (preferred) or `.git/hooks/` so commits inside the worktree still trigger your existing pre-commit / commit-msg hooks.
- **Optional symlinks** — directories listed in `worktree.symlinkDirectories` (see [Settings](<#settings>)) are symlinked from the main repo into the new worktree so heavy dirs like `node_modules` can be reused without reinstalling.

The general-purpose worktree path is **not configurable** — it must live under `<repoRoot>/.qwen/worktrees/` so the CLI can find it on restart and on stale-cleanup sweeps. (The unrelated `agents.arena.worktreeBaseDir` setting controls only [Agent Arena](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/arena.md>) worktrees, which use a separate path tree under `~/.qwen/arena/`.)

## Footer and Status Line

When a worktree is active, the Footer shows a dim indicator on its own row:

```
⎇ worktree-experiment-a (experiment-a)
```

If you use a [custom status line script](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/status-line.md>), it also receives a `worktree` object in the JSON payload piped to stdin:

```json
{
  "worktree": {
    "name": "experiment-a",
    "path": "/path/to/repo/.qwen/worktrees/experiment-a",
    "branch": "worktree-experiment-a",
    "original_cwd": "/path/to/repo",
    "original_branch": "main"
  }
}
```

The payload field is present **only** when a worktree is active, so a `null`-check (`input.worktree?.name`) is enough.

If your custom status line already renders worktree info, you can hide the built-in Footer row to avoid duplication — see [Settings](<#settings>) below.

## Exit Dialog (Ctrl+C / Ctrl+D)

Pressing the quit shortcut twice while a worktree is active opens the **Worktree Exit Dialog** instead of closing the CLI:

```
⎇ Active worktree: "experiment-a" (worktree-experiment-a)

  • 2 new commit(s) on worktree-experiment-a
  • 3 uncommitted file(s)
  Removing the worktree will discard everything above.

What would you like to do?
  ○ Keep worktree (exit without deleting)
  ○ Remove worktree and branch (discards 2 commit(s), 3 file(s))
  ○ Cancel (stay in session)
```

The dialog inspects the worktree on open (`git status --porcelain` + `git rev-list <baseHEAD>..HEAD`) and surfaces both counts so you know exactly what you'd be discarding. `ESC` cancels.

If `git status` itself fails (e.g. corrupt index, worktree directory was removed under the CLI), the dialog shows a `⚠ Could not measure worktree state` warning and the counts may be unreliable — choose **Keep** or **Cancel** until you've diagnosed the underlying repo problem.

## `--resume` Restore

The active worktree binding is persisted to a sidecar file alongside your session transcript:

```
<chatsDir>/<sessionId>.worktree.json
```

When you launch the CLI with `--resume <sessionId>` (or pick the session from `/resume`), three things happen consistently across **interactive TUI**, **headless `-p`**, and **ACP/Zed** modes:

1. The sidecar is loaded and the worktree directory is verified to still exist on disk.
2. If alive, the model receives a one-shot reminder on its very next prompt:
   ```
   [Resumed] Active worktree: "<slug>" at <path> (branch: <branch>). Continue using this path for all file operations.
   ```
3. If the worktree directory was deleted between sessions, the stale sidecar is cleaned up automatically — no error, the resume just continues without worktree context.

Each mode chooses its own injection mechanism, but the user-visible behavior is identical:

| Mode              | Mechanism                                                                                              |
| ----------------- | ------------------------------------------------------------------------------------------------------ |
| Interactive (TUI) | `INFO` history item + system-reminder prefix on the next user prompt.                                  |
| Headless (`-p`)   | `<system-reminder>` prefix on the prompt + `worktree_restored` JSON system event in the output stream. |
| ACP (e.g. Zed)    | Pending notice attached to the next `prompt()` call.                                                   |

The model is **not** automatically `chdir`'d into the worktree — the reminder is what keeps it routing edits through the worktree path.

## Sub-Agent Isolation

The `agent` tool accepts an optional `isolation: "worktree"` parameter. When set, Qwen Code creates an ephemeral worktree at `<repoRoot>/.qwen/worktrees/agent-<7hex>/` before the sub-agent starts, and:

- **No changes** → the worktree is automatically removed when the agent finishes.
- **Has changes** → the worktree is preserved; its path and branch are appended to the agent's result, e.g.
  ```
  …agent output…
  [worktree preserved: /path/to/.qwen/worktrees/agent-3f2a1b9 (branch worktree-agent-3f2a1b9)]
  ```
  Review the diff and merge or delete it manually.

Two constraints:

- `isolation: "worktree"` requires a non-fork `subagent_type` — forked sub-agents (`subagent_type: "fork"`) reuse the parent's full conversation context, so isolating them would split intent from working tree.
- Agents using `isolation: "worktree"` follow the default background behavior; the cleanup runs when the agent reports completion. Set `run_in_background: false` for an inline result. Unnamed caller-owned `working_dir` launches run in the foreground; explicit background execution is rejected, while configured background execution (`background: true` in a subagent definition) is rejected at the top level and downgraded to a foreground run when nested because their lifecycle is managed externally.

### Automatic Stale Cleanup

Ephemeral agent worktrees that survived a crash or `--no-cleanup` shutdown are reaped on every CLI startup, with conservative fail-closed rules:

| Guard                                                     | Behavior                                                                                                                                                       |
| --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Slug must match `agent-<7hex>` pattern                    | Other names are never touched. A worktree you named exactly `agent-<7hex>` is indistinguishable from an ephemeral agent worktree and **is** a sweep candidate. |
| Directory `mtime` > 30 days                               | Newer entries are skipped.                                                                                                                                     |
| Any uncommitted work — tracked, untracked, or git-ignored | Skip the entry (don't delete). Only disposable build output, symlinks and the session marker are exempt — see the note below.                                  |
| Any commit not reachable from a remote                    | Skip the entry (don't delete).                                                                                                                                 |
| Any error reading git state                               | Skip the entry (don't delete).                                                                                                                                 |

Named user worktrees (`enter_worktree` slugs) are never auto-cleaned, with one exception: the reserved `agent-<7hex>` shape, which the sweep cannot tell apart from an ephemeral agent worktree, is reaped after 30 days once it holds no uncommitted content and no unmerged commits. Pick any other name for work you want kept.

The uncommitted-content guard reads `git status --porcelain --untracked-files=normal --ignored=matching`, so even content your ignore rules hide — a `.env`, `.qwen/pr-drafts/` — preserves an aged `agent-<7hex>` worktree. The only exemptions are:

- **Disposable build output** — an _ignored_ entry whose _first_ path segment is `node_modules`, `dist` or `coverage`, so that output and anything git lists under it, a single ignored file included (`!! dist/archive.zip` under a `*.zip` rule). Also an ignored _directory_ nested inside the checkout whose own name is one of those three (a monorepo's `packages/app/node_modules/`) — but only when git collapses the entry _at_ that directory, which needs an ignore rule matching the directory itself (`node_modules/`, or a bare `node_modules`). A rule matching the directory's _contents_ instead (`**/node_modules/*`, or a package-local `node_modules/**`) makes git list the files individually (`!! packages/app/node_modules/x/`), which is not exempt and preserves the checkout. A nested ignored _file_ with one of those names is not build output and still preserves the worktree, so `go test -coverprofile=coverage` writing `packages/app/coverage` keeps the checkout — but nothing stored under a root `dist/`, `coverage/` or `node_modules/` is kept. The same tree left _untracked_, so git says `?? dist/`, is not exempt and preserves the checkout. Note that `??` describes the _rendered entry_, not the absence of a rule: git collapses an untracked parent (`?? packages/`) whenever it holds any non-ignored untracked file, and lists ignored files inside it separately as `!!` at the same time. The usual reasons an entry renders `??` are that no ignore rule reaches it — including a rule whose spelling does not reach that path, since a root `node_modules/**` covers only the root-level tree — or that the rule was committed after the worktree branch was cut.
- **Symlinks** — removing the checkout unlinks them and never touches what they point at, which is what keeps `worktree.symlinkDirectories` checkouts reapable. A nested value (`tools/cache`) makes git collapse the subtree to a single `?? tools/` entry, so a collapsed directory qualifies only when it holds at least one entry and everything inside it is itself a symlink; one real file beside the links — or an empty directory, which holds no link to speak of — preserves the worktree.
- **The daemon's `.qwen-session` marker**, as long as git lists it as untracked or ignored. A marker the repository actually tracks is not exempt: an edit to a tracked path is indistinguishable from user work, and `writeWorktreeSessionMarker` overwrites a tracked marker unconditionally, so the daemon itself can be the author of that edit — the preserve then holds until the marker is committed or reverted.

A path git quotes because it contains special characters is not resolved and counts as work.

## Safety Guards on `exit_worktree action="remove"`

Three independent guards trigger before the directory and branch are deleted:

1. **Session ownership** — each worktree carries a sidecar marker with the session ID that created it. A different session trying to remove it is refused with a clear error pointing at `git worktree remove` for the manual escape hatch.
2. **Dirty working tree** — uncommitted tracked or untracked changes block removal. Pass `discard_changes: true` to override. (Bypass requires explicit user confirmation — `action: "remove"` is never auto-approved in AUTO_EDIT mode.)
3. **Unmerged commits** — commits on `worktree-<slug>` that no other local branch or remote ref points at block removal unconditionally; there is no "discard commits" flag because losing committed work is rarely what users mean. Merge, push, or rename the branch elsewhere first.

The same three guards apply to the `WorktreeExitDialog → Remove` button.

## Settings

Two settings shape the general-purpose worktree experience:

| Key                               | Type       | Default     | Effect                                                                                                                                                                                                                                                                     |
| --------------------------------- | ---------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ui.hideBuiltinWorktreeIndicator` | boolean    | `false`     | Hides the built-in `⎇ worktree-… (…)` Footer row. The `worktree` field is still delivered to custom status line scripts. Set to `true` only if your status line already renders the worktree — otherwise you lose all UI affordance.                                       |
| `worktree.symlinkDirectories`     | `string[]` | `undefined` | Directories under the main repo to symlink into every general-purpose worktree on creation. Paths are relative to the repo root; absolute paths and any entry containing `..` are rejected. Missing sources and existing destinations are silently skipped (no overwrite). |

Example:

```jsonc
// ~/.qwen/settings.json or <repo>/.qwen/settings.json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".turbo", "dist"],
  },
}
```

Applies to ALL worktree-creation paths: `--worktree` flag, `enter_worktree` tool, and `agent isolation: "worktree"`.

Settings unrelated to general worktrees but worth knowing about:

- `agents.arena.worktreeBaseDir` — controls **Agent Arena** worktree placement (default `~/.qwen/arena`). Does not affect general-purpose worktrees, which always live under `<repoRoot>/.qwen/worktrees/`.

There is no schema for `worktree.sparsePaths` yet — that's a roadmap item (see [Limitations](<#limitations>)).

## Tool Reference

### `enter_worktree`

```json
{ "name": "experiment-a" }
```

| Field  | Type   | Required | Notes                                                                                      |
| ------ | ------ | -------- | ------------------------------------------------------------------------------------------ |
| `name` | string | no       | Slug. Letters, digits, dot, underscore, hyphen; max 64 chars. Auto-generated when omitted. |

Refuses to run when:

- The CLI is not in a git repository.
- The current working directory is already inside `.qwen/worktrees/` (no nested worktrees).

### `exit_worktree`

```json
{ "name": "experiment-a", "action": "remove", "discard_changes": false }
```

| Field             | Type                   | Required                              | Notes                                                              |
| ----------------- | ---------------------- | ------------------------------------- | ------------------------------------------------------------------ |
| `name`            | string                 | yes                                   | Must match the slug used in `enter_worktree`.                      |
| `action`          | `"keep"` \| `"remove"` | yes                                   | `keep` preserves dir + branch; `remove` deletes both.              |
| `discard_changes` | boolean                | only when `action="remove"` and dirty | Overrides the dirty-tree guard. Has no effect for `action="keep"`. |

`action: "remove"` always prompts for confirmation, including under `AUTO_EDIT` approval mode — it is treated as a destructive shell operation, not an info-only tool.

### `agent` — `isolation` parameter

```json
{
  "subagent_type": "my-agent",
  "description": "…",
  "prompt": "…",
  "isolation": "worktree"
}
```

| Field       | Type         | Required | Notes                                                                                             |
| ----------- | ------------ | -------- | ------------------------------------------------------------------------------------------------- |
| `isolation` | `"worktree"` | no       | Runs the agent in a fresh `agent-<7hex>` worktree. Requires `subagent_type` to be set (no forks). |

See [Sub-Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents.md>) for the rest of the agent tool reference.

## CLI Reference

### `--worktree [name | #N | url]`

```bash
qwen --worktree                                               # auto-generate slug
qwen --worktree my-feature                                    # explicit slug
qwen --worktree=my-feature                                    # = form
qwen --worktree=#123                                          # PR reference
qwen --worktree https://github.com/owner/repo/pull/123        # PR URL
```

| Input                         | Result                                                                                                                |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| Bare flag (no value)          | Auto slug `<adjective>-<noun>-<6hex>`, branch `worktree-<slug>`, base = current branch.                               |
| Plain slug                    | Branch `worktree-<slug>`, base = current branch. Slug validation: letters/digits/dot/underscore/hyphen, max 64 chars. |
| `#N` or `<github-url>/pull/N` | Slug `pr-<N>`, branch `worktree-pr-<N>`, base = `FETCH_HEAD` after `git fetch origin pull/<N>/head` (30s timeout).    |

`--worktree` cannot be combined with `--acp` / `--experimental-acp`.

When `--worktree` is combined with `--resume <session-id>`, the worktree wins: the resumed session's saved worktree (if any) is overridden and a stderr line + first-prompt reminder report the override.

For interactive (TUI) and headless (`-p`) modes the worktree is automatically created and the session chdirs into it before the first turn.

PR-fetch failure modes (exit code != 0, no worktree created):

| Cause                         | Message excerpt                                            |
| ----------------------------- | ---------------------------------------------------------- |
| Missing `origin` remote       | `requires an "origin" remote that points at GitHub`        |
| PR doesn't exist on origin    | `Failed to fetch PR #<N>: the PR does not exist on origin` |
| 30s network timeout           | `Failed to fetch PR #<N>: timed out after 30s`             |
| PR number out of range / zero | `Invalid PR number`                                        |

## Limitations

The following items are intentionally not implemented in the current phase:

- **No sparse checkout.** Large monorepos check out the full tree. (`worktree.sparsePaths` is a roadmap item.)
- **No tmux integration.** The CLI does not spawn worktree sessions in new tmux windows.
- **Worktrees are separate "projects" for session storage.** Sessions started with `--worktree foo` are saved under that worktree's chats dir; to resume them later you must pass `--worktree foo` again. Sessions started without `--worktree` are saved under the main checkout and won't appear in the worktree's resume picker.
- **No cross-slug session override.** `qwen --resume <sid> --worktree second` where `<sid>` was created with `--worktree first` will fail to find the session — sessions and worktrees are tightly bound by `projectHash(cwd)`. To switch worktrees on an existing session you must exit, then re-launch with the new `--worktree` and a fresh prompt. A future architectural change (anchoring storage at the repo root instead of `cwd`) would lift this constraint.
- **Mid-session `enter_worktree` does NOT switch `process.cwd()` or `Config.targetDir`.** That tool uses the model-context-only convention (see [Sub-Agents](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/sub-agents.md>)). Only the startup `--worktree` flag actually switches the process working directory.
- **Relative paths in other arg fields are resolved BEFORE the worktree chdir.** Path-taking flags (`--mcp-config`, `--openai-logging-dir`, `--json-file`, `--input-file`, `--telemetry-outfile`, `--include-directories`) are normalized to absolute paths against the launch cwd when `--worktree` is set. Other path-shaped argv fields not in this list still resolve against the worktree cwd — use absolute paths to be safe.

Track the roadmap in `docs/design/worktree.md`.

## Troubleshooting

**The Footer shows no worktree indicator even though I just created one.**
Check that `ui.hideBuiltinWorktreeIndicator` is not set to `true`. Also confirm the slug is non-empty in the tool's success message.

**`--resume` does not restore my worktree.**
Check `<chatsDir>/<sessionId>.worktree.json` exists. The CLI deletes the sidecar automatically when the worktree directory is gone, so a missing sidecar plus a missing directory is the normal "no worktree to restore" state — not a bug. Run with `--debug` and grep for `restoreWorktreeContext` to see the reason.

**`exit_worktree` says "created by a different session".**
This is the session-ownership guard. Resume the original session and exit from there, or run the suggested `git worktree remove …` command manually.

**Stale `agent-<hex>` worktrees keep piling up.**
The 30-day cutoff is conservative; sweep manually with `git worktree list && git worktree remove <path>`, or wait — the next CLI startup after the 30-day mark will reap them as long as they hold no uncommitted work (tracked, untracked, or git-ignored beyond disposable build output and symlinks) and no unmerged commits. Run with `--debug` and grep for `cleanupStaleAgentWorktrees: keeping` to see which entry the sweep refused and why, or `worktreeHasWork: waiving` to see which entries authorized a removal and under which exemption.


</details>
