# Mistral Vibe Git 工作树技能

说明创建、复用、保留、快照和清理工作树的流程与注意事项；适用于该工具的隔离开发管理。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-09-11](<https://github.com/mistralai/mistral-vibe/commit/b166c3a39eaac6ece96ca31840a3278c636616b5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/vibe/plugins/builtins/vibe/skills/worktree/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/Mistral Vibe Git 工作树技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: worktree
description: Create, reuse, and clean up git worktrees under $VIBE_HOME/worktrees. Use when the user wants to work in an isolated git worktree, create a worktree for a task, or list/remove existing worktrees.
user-invocable: true
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 工作树管理

Git 工作树位于 `$VIBE_HOME/worktrees/`（默认为 `~/.vibe/worktrees/`）、
 下，每个工作树位于名为 `<repo_root.name>-<sha256(common_git_dir)[:12]>`
 的每个存储库存储桶中，因此来自不同存储库的工作树永远不会发生冲突。

```
$VIBE_HOME/worktrees/
  .claims/<bucket>/<name>/          # claim records and holder markers
    record.json                     # metadata: branch, base_commit, branch_created, claimed_at
    recovery.json                   # snapshot metadata for a retained chat
    holders/                        # one empty file per live session
  <bucket>/<name>/                  # the actual git worktree checkout
```

## 创建工作树

1. 从存储库根和公共 git 目录计算存储桶。
2. 验证名称（单个可移植路径段；没有 `<>:"/\|?*`，没有
尾随空格或点，没有 Windows 保留名称，例如 `CON`/`PRN`/`NUL`/
 `COM1`-`COM9`/`LPT1`-`LPT9`， `.` 或 `..`）。
3. 使用 `mkdir` 保留目录（原子声明 - 如果采用则失败）。
4. 对于新分支，从远程的默认分支开始（首先获取，
尽力而为）。对于现有分支，将其签入工作树。
5. `git worktree add -b <name> <path> <start_point>`（或不带`-b`以供重复使用）。
6. 将工作树自己的 HEAD 记录为 `base_commit` — 这是基线
清理，而不是调用结账的 HEAD。
7. 在`.claims/<bucket>/<name>/`下写入`record.json`。

如果没有给出名称，则通过提示提示来派生：NFKD-标准化，
小写，将非`[a-z0-9]+`替换为`-`，取第一个6单词，修剪为
40字符，删除追踪止损的话。分支获得 `vibe/` 前缀。在碰撞时附加 
`-2`、`-3` 等（最多 100）。如果为空，则回落为随机弹头。

仅在验证后重用现有工作树：路径中没有符号链接，
`.git` 是一个文件（不是目录），相同的公共 git 目录，匹配分支。

## 控股

使用工作树的会话必须注册为持有者，以防止 
 后台清理将其删除。持有者文件在会话的生命周期
内被锁定；崩溃进程留下的解锁标记已过时，可能
被删除。临时持有者在会话附加时保护工作树。

## 清理

删除之前，检查工作树状态：
- 未提交的更改或未跟踪的文件：`git -c core.fsmonitor= status --porcelain --untracked-files=all`
- 自开始以来的新提交：`git rev-list --count <base_commit>..HEAD`

仅当这三个都不存在时，工作树才是干净的。如果脏了，请在继续之前与 
 用户确认 — 删除会丢弃未提交的工作、未跟踪的 
 文件和新提交。

如果分支已存在（不是由该会话创建），请在删除之前单独确认
。重新检查持有者 - 如果另一个会话仍然持有 
 工作树，则保留它。 `cd` 在删除之前退出工作树（Windows 拒绝 
 删除进程的 cwd）。

删除：`git worktree remove --force <path>`，然后是 `git branch -D <name>`（如果 
 分支是在本次会话中创建的或用户确认的）。删除索赔
记录和`rmdir`空目录（永远不要`rmtree` - 幸存的持有者
不得丢失）。

## 快照

在保留删除不活动的工作树之前，将其状态保存到参考：stage
所有文件（包括未跟踪的，排除忽略的）并提交到
`refs/vibe/reaped/<name>`。使用单独的索引文件，以便工作树自己的
索引不受影响。删除有效声明后保留 `recovery.json`，以便 
 重新打开聊天可以自动重新创建结账。

## 保留率

跨存储库保留最新配置数量的托管工作树。
跳过活动工作树。在删除之前对每个不活动的多余工作树进行快照；
如果快照失败，则保留它。仅当空且没有创建进程仍保留其标记时，没有 `base_commit` 的预留才会被 
 丢弃。

创建、附加和保留转换使用跨进程锁，因此当另一个进程声明或附加
工作树时，无法删除它。

## 陷阱

- 读取别人的工作树时禁用 `core.fsmonitor`：
  `git -c core.fsmonitor= status`.
- 新分支从远程默认分支开始，而不是当前分支
结帐的HEAD。
- 符号链接路径被拒绝——不是稳定的工作树路径。
- `git worktree add` 无法区分采用的路径和无效的引用
（都退出128），所以`mkdir`首先保留路径。
- 工作树仅对于会话隐式信任，而不是持久的。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Worktree Management

Git worktrees live under `$VIBE_HOME/worktrees/` (default `~/.vibe/worktrees/`),
each in a per-repository bucket named `<repo_root.name>-<sha256(common_git_dir)[:12]>`
so worktrees from different repos never collide.

```
$VIBE_HOME/worktrees/
  .claims/<bucket>/<name>/          # claim records and holder markers
    record.json                     # metadata: branch, base_commit, branch_created, claimed_at
    recovery.json                   # snapshot metadata for a retained chat
    holders/                        # one empty file per live session
  <bucket>/<name>/                  # the actual git worktree checkout
```

## Creating a worktree

1. Compute the bucket from the repo root and common git dir.
2. Validate the name (single portable path segment; no `<>:"/\|?*`, no
   trailing space or dot, no Windows reserved names like `CON`/`PRN`/`NUL`/
   `COM1`-`COM9`/`LPT1`-`LPT9`, not `.` or `..`).
3. Reserve the directory with `mkdir` (atomic claim — fails if taken).
4. For a new branch, start from the remote's default branch (fetch first,
   best-effort). For an existing branch, check it out into the worktree.
5. `git worktree add -b <name> <path> <start_point>` (or without `-b` for reuse).
6. Record the worktree's own HEAD as `base_commit` — this is the baseline for
   cleanup, not the invoking checkout's HEAD.
7. Write `record.json` under `.claims/<bucket>/<name>/`.

When no name is given, derive one by slugifying the prompt: NFKD-normalize,
lowercase, replace non-`[a-z0-9]+` with `-`, take the first 6 words, trim to
40 chars, drop trailing stop words. Branch gets a `vibe/` prefix. Append
`-2`, `-3`, etc. on collision (up to 100). Fall back to a random slug if empty.

Reuse an existing worktree only after validating: no symlinks in the path,
`.git` is a file (not a directory), same common git dir, matching branch.

## Holding

A session using a worktree must register as a holder to prevent the
background cleanup from removing it. Holder files are locked for the lifetime
of the session; an unlocked marker left by a crashed process is stale and may
be removed. A temporary holder protects a worktree while a session attaches.

## Cleaning up

Before removal, inspect the worktree state:
- Uncommitted changes or untracked files: `git -c core.fsmonitor= status --porcelain --untracked-files=all`
- New commits since start: `git rev-list --count <base_commit>..HEAD`

The worktree is clean only if all three are absent. If dirty, confirm with
the user before proceeding — removal discards uncommitted work, untracked
files, and new commits.

If the branch pre-existed (not created by this session), confirm separately
before deleting it. Re-check holders — if another session still holds the
worktree, keep it. `cd` out of the worktree before removing (Windows refuses
to delete a process's cwd).

Remove: `git worktree remove --force <path>`, then `git branch -D <name>` if
the branch was created this session or the user confirmed. Delete the claim
record and `rmdir` empty directories (never `rmtree` — a surviving holder
must not be lost).

## Snapshotting

Before retention removes an inactive worktree, save its state to a ref: stage
all files (including untracked, excluding ignored) and commit to
`refs/vibe/reaped/<name>`. Use a separate index file so the worktree's own
index is untouched. Keep `recovery.json` after removing the active claim so
reopening a chat can recreate the checkout automatically.

## Retention

Keep the newest configured number of managed worktrees across repositories.
Skip active worktrees. Snapshot each inactive excess worktree before removal;
if snapshotting fails, keep it. Reservations without a `base_commit` are
discarded only if empty and no creation process still holds their marker.

Creation, attachment, and retention transitions use a cross-process lock so a
worktree cannot be removed while another process is claiming or attaching it.

## Gotchas

- Disable `core.fsmonitor` when reading someone else's working tree:
  `git -c core.fsmonitor= status`.
- New branches start from the remote default branch, not the current
  checkout's HEAD.
- Symlink paths are rejected — not a stable worktree path.
- `git worktree add` cannot distinguish a taken path from an invalid ref
  (both exit 128), so `mkdir` reserves the path first.
- Worktrees are implicitly trusted for the session only, not persisted.


</details>
