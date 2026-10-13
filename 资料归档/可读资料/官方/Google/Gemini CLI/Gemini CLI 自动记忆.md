# Gemini CLI 自动记忆

介绍后台从历史会话提取长期记忆、用户审阅和启停的机制，说明使用条件、数据隐私与实验性限制。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-05-13](<https://geminicli.com/docs/cli/auto-memory/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/auto-memory/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 自动记忆.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 自动记忆

自动内存是一项实验性功能，可在后台挖掘您过去的 Gemini CLI 会话 
，并提出持久内存更新和可重复使用 
[Agent Skills](<https://geminicli.com/docs/cli/skills>)。您在每个候选者
可用于未来会话之前对其进行审查：应用内存更新，提升技能，或丢弃
任何您不想要的东西。

## 概述

[标题为“概述”的部分](<#概述>)

您使用 Gemini CLI 运行的每个会话都会在本地记录为脚本。自动
内存扫描这些记录，以获取持久的事实、偏好、工作流程
约束以及跨会话重复出现的程序模式。它可以将
内存更新起草为统一差异`.patch`文件，并将可重用程序起草为
`SKILL.md`文件。所有候选者都保存在项目本地收件箱中，直到您
批准或放弃它们。

当您想要执行以下操作时，您将使用自动记忆：

* **捕获您发现自己走过 agent 的团队工作流程 **
不止一次。
* **保留持久的项目上下文**例如重复验证命令，
当地限制，或个人项目笔记。
* **针对特定项目的地雷整理来之不易的修复**，以便未来的会议
避开他们。
* **引导技能库**，无需手动编写每个`SKILL.md`。

自动内存补充了直接内存文件编辑。 agent仍然可以通过编辑适当的Markdown内存文件来保留
显式用户指令；自动
记忆从过去的会话中推断候选人，编写可审阅的补丁或技能
草稿，并且在未经您批准的情况下绝不会应用它们。

## 先决条件

[标题为“先决条件”的部分](<#先决条件>)

* Gemini CLI已安装并经过身份验证。
* 至少一个包含 10 或更多用户消息的空闲项目会话。自动记忆
忽略活动、琐碎和子 agent 会话。

## 如何启用自动记忆

[标题为“如何启用自动记忆”的部分](<#如何启用自动记忆>)

默认情况下，自动记忆功能处于关闭状态。在您的设置文件中启用它：

1. 打开位于 `~/.gemini/settings.json` 的全局设置文件。如果你只
如果想要在一个项目中自动存储，请在该 
 项目中编辑 `.gemini/settings.json`。
2. 添加实验标志：

   ```
   {


   "experimental": {


   "autoMemory": true


   }


   }
   ```
3. 重新启动Gemini CLI。该标志需要重新启动，因为提取
服务在会话引导期间启动。

## 自动记忆的工作原理

[标题为“自动记忆如何工作”的部分](<#自动记忆的工作原理>)

自动内存在会话启动时作为后台任务运行。它不会阻塞 
UI、消耗您的交互式转弯或表面工具提示。

1. **资格扫描。**服务对最近的会话进行索引
`~/.gemini/tmp/<project>/chats/`。仅当会话
 空闲至少三个小时且至少包含10 个用户消息时，会话才符合资格。
2. **锁获取。**项目内存目录下的锁文件
跨多个 CLI 实例进行坐标，因此提取一次最多在 
 运行一次。状态文件记录已处理的会话版本，并且提取受到
限制，因此短暂的背靠背CLI启动不会重复扫描历史记录。
3. **候选提取。**背景提取agent回顾会话
索引，读取任何看起来包含持久内存或
重复程序工作流程的会话，并起草候选者。除非证据有力，否则它默认
不创建任何工件，因此许多运行不会产生
收件箱项目。
4. **安全边界。**自动记忆将候选人写入评论收件箱。它
无法直接编辑活动内存文件、设置、凭据或项目
 `GEMINI.md` 文件。
5. **补丁验证。**技能更新补丁之前已解析并试运行
他们浮出水面。仅当您从收件箱批准内存补丁时，才会对内存补丁进行解析、将其列入目标允许列表并自动应用 
。
6. **通知。**当运行产生新的候选者时，Gemini CLI会显示
内联消息告诉您有多少项目正在等待。

## 如何查看提取的项目

[标题为“如何查看提取的项目”的部分](<#如何查看提取的项目>)

使用`/memory inbox`斜杠命令可以随时打开收件箱对话框：

**命令：** `/memory inbox`

该对话框将待处理项目分为新技能、技能更新和内存
更新。从那里您可以：

* **在决定之前先阅读**完整的`SKILL.md`正文。
* **向您的用户 (`~/.gemini/skills/`) 或工作区提升 ** 技能
(`.gemini/skills/`) 目录。
* **丢弃**你不想要的技能。
* **应用**或拒绝针对现有技能的`.patch`提案。
* **在接触活动文件之前查看**内存差异。
* **应用**或关闭私有和全局内存补丁。私有补丁目标
项目内存目录；全局补丁仅针对您的个人
 `~/.gemini/GEMINI.md` 文件。

升级的技能将在下一个会话中被发现，并遵循标准
[技能发现优先级](<https://geminicli.com/docs/cli/skills#skill-discovery-tiers>)。应用内存
补丁更新底层内存文件并为当前
会话重新加载内存。

## 如何禁用自动记忆

[标题为“如何禁用自动记忆”的部分](<#如何禁用自动记忆>)

要关闭后台提取，请在设置 
 文件中将标志设置回 `false` 并重新启动 Gemini CLI：

```
{


"experimental": {


"autoMemory": false


}


}
```

禁用该标志会在下一个会话
启动时立即停止后台服务。现有收件箱项目保留在磁盘上；您可以先用
`/memory inbox`耗尽它们，或者手动删除项目内存目录。

## 数据和隐私

[标题为“数据和隐私”的部分](<#数据和隐私>)

* 自动内存仅读取本地已存在的会话文件
机。
* 自动记忆使用模型调用来分析选定的本地转录内容
在提取过程中。不会自动应用候选，但转录本 
 摘录可能会作为这些调用的一部分发送到配置的模型。
* 提取 agent 被指示编辑秘密、令牌和凭证
它遇到并且永远不会逐字复制大型工具输出。
* 起草的技能和内存补丁位于项目的内存目录中
直到你推广、应用、驳回或放弃它们。它们不会自动
加载到任何会话中。

## 局限性

[标题为“限制”的部分](<#局限性>)

* 提取 agent 在预览 Gemini Flash 模型上运行。提取质量
取决于模型识别持久模式与一次性
事件的能力。
* 自动记忆不会从当前会话中提取记忆或技能。它
仅考虑空闲三个小时或更长时间的会话。
* 项目 `GEMINI.md` 文件中的项目或工作区共享指令不是
可自动提取。自动记忆可以提出私人项目记忆、全局
个人记忆和技能。
* 收件箱项目按项目存储。在一个工作空间中提取的技能不是
从另一个可见，直到您将它们提升到用户范围技能
 目录。

## 后续步骤

[标题为“后续步骤”的部分](<#后续步骤>)

* 了解如何在 [Agent Skills](<https://geminicli.com/docs/cli/skills>) 中发现和激活技能。
* 探索 [ 内存管理教程 ](<https://geminicli.com/docs/cli/tutorials/memory-management>)
互补的显式记忆和 `GEMINI.md` 工作流程。
* 查看实验设置目录
[设置](<https://geminicli.com/docs/cli/settings#experimental>)。

最后更新：五月 13、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Auto Memory

Auto Memory is an experimental feature that mines your past Gemini CLI sessions
in the background and proposes durable memory updates and reusable
[Agent Skills](<https://geminicli.com/docs/cli/skills>). You review each candidate before it becomes
available to future sessions: apply memory updates, promote skills, or discard
anything you do not want.

## Overview

[Section titled “Overview”](<#overview>)

Every session you run with Gemini CLI is recorded locally as a transcript. Auto
Memory scans those transcripts for durable facts, preferences, workflow
constraints, and procedural patterns that recur across sessions. It can draft
memory updates as unified diff `.patch` files and draft reusable procedures as
`SKILL.md` files. All candidates are held in a project-local inbox until you
approve or discard them.

You’ll use Auto Memory when you want to:

* **Capture team workflows** that you find yourself walking the agent through
  more than once.
* **Preserve durable project context** such as repeated verification commands,
  local constraints, or personal project notes.
* **Codify hard-won fixes** for project-specific landmines so future sessions
  avoid them.
* **Bootstrap a skills library** without writing every `SKILL.md` by hand.

Auto Memory complements direct memory-file editing. The agent can still persist
explicit user instructions by editing the appropriate Markdown memory file; Auto
Memory infers candidates from past sessions, writes reviewable patches or skill
drafts, and never applies them without your approval.

## Prerequisites

[Section titled “Prerequisites”](<#prerequisites>)

* Gemini CLI installed and authenticated.
* At least one idle project session with 10 or more user messages. Auto Memory
  ignores active, trivial, and sub-agent sessions.

## How to enable Auto Memory

[Section titled “How to enable Auto Memory”](<#how-to-enable-auto-memory>)

Auto Memory is off by default. Enable it in your settings file:

1. Open your global settings file at `~/.gemini/settings.json`. If you only
   want Auto Memory in one project, edit `.gemini/settings.json` in that
   project instead.
2. Add the experimental flag:

   ```
   {


   "experimental": {


   "autoMemory": true


   }


   }
   ```
3. Restart Gemini CLI. The flag requires a restart because the extraction
   service starts during session boot.

## How Auto Memory works

[Section titled “How Auto Memory works”](<#how-auto-memory-works>)

Auto Memory runs as a background task on session startup. It does not block the
UI, consume your interactive turns, or surface tool prompts.

1. **Eligibility scan.** The service indexes recent sessions from
   `~/.gemini/tmp/<project>/chats/`. Sessions are eligible only if they have
   been idle for at least three hours and contain at least 10 user messages.
2. **Lock acquisition.** A lock file in the project’s memory directory
   coordinates across multiple CLI instances so extraction runs at most once at
   a time. A state file records processed session versions, and extraction is
   throttled so short back-to-back CLI launches do not repeatedly scan history.
3. **Candidate extraction.** A background extraction agent reviews the session
   index, reads any sessions that look like they contain durable memory or
   repeated procedural workflows, and drafts candidates. It defaults to
   creating no artifacts unless the evidence is strong, so many runs produce no
   inbox items.
4. **Safety boundaries.** Auto Memory writes candidates to a review inbox. It
   cannot directly edit active memory files, settings, credentials, or project
   `GEMINI.md` files.
5. **Patch validation.** Skill update patches are parsed and dry-run before
   they are surfaced. Memory patches are parsed, target-allowlisted, and
   applied atomically only when you approve them from the inbox.
6. **Notification.** When a run produces new candidates, Gemini CLI surfaces an
   inline message telling you how many items are waiting.

## How to review extracted items

[Section titled “How to review extracted items”](<#how-to-review-extracted-items>)

Use the `/memory inbox` slash command to open the inbox dialog at any time:

**Command:** `/memory inbox`

The dialog groups pending items into new skills, skill updates, and memory
updates. From there you can:

* **Read** the full `SKILL.md` body before deciding.
* **Promote** a skill to your user (`~/.gemini/skills/`) or workspace
  (`.gemini/skills/`) directory.
* **Discard** a skill you do not want.
* **Apply** or reject a `.patch` proposal against an existing skill.
* **Review** memory diffs before they touch active files.
* **Apply** or dismiss private and global memory patches. Private patches target
  the project memory directory; global patches target only your personal
  `~/.gemini/GEMINI.md` file.

Promoted skills become discoverable in the next session and follow the standard
[skill discovery precedence](<https://geminicli.com/docs/cli/skills#skill-discovery-tiers>). Applied memory
patches update the underlying memory files and reload memory for the current
session.

## How to disable Auto Memory

[Section titled “How to disable Auto Memory”](<#how-to-disable-auto-memory>)

To turn off background extraction, set the flag back to `false` in your settings
file and restart Gemini CLI:

```
{


"experimental": {


"autoMemory": false


}


}
```

Disabling the flag stops the background service immediately on the next session
start. Existing inbox items remain on disk; you can either drain them with
`/memory inbox` first or remove the project memory directory manually.

## Data and privacy

[Section titled “Data and privacy”](<#data-and-privacy>)

* Auto Memory only reads session files that already exist locally on your
  machine.
* Auto Memory uses model calls to analyze selected local transcript content
  during extraction. No candidates are applied automatically, but transcript
  excerpts may be sent to the configured model as part of those calls.
* The extraction agent is instructed to redact secrets, tokens, and credentials
  it encounters and to never copy large tool outputs verbatim.
* Drafted skills and memory patches live in your project’s memory directory
  until you promote, apply, dismiss, or discard them. They are not automatically
  loaded into any session.

## Limitations

[Section titled “Limitations”](<#limitations>)

* The extraction agent runs on a preview Gemini Flash model. Extraction quality
  depends on the model’s ability to recognize durable patterns versus one-off
  incidents.
* Auto Memory does not extract memory or skills from the current session. It
  only considers sessions that have been idle for three hours or more.
* Project or workspace shared instructions in project `GEMINI.md` files are not
  auto-extractable. Auto Memory can propose private project memory, global
  personal memory, and skills.
* Inbox items are stored per project. Skills extracted in one workspace are not
  visible from another until you promote them to the user-scope skills
  directory.

## Next steps

[Section titled “Next steps”](<#next-steps>)

* Learn how skills are discovered and activated in [Agent Skills](<https://geminicli.com/docs/cli/skills>).
* Explore the [memory management tutorial](<https://geminicli.com/docs/cli/tutorials/memory-management>) for
  the complementary explicit-memory and `GEMINI.md` workflows.
* Review the experimental settings catalog in
  [Settings](<https://geminicli.com/docs/cli/settings#experimental>).

Last updated: May 13, 2026


</details>
