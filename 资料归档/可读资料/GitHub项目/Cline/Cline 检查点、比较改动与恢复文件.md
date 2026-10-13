# Cline 检查点、比较改动与恢复文件

产品指南。解释独立影子 Git 仓库如何记录工具操作后的文件状态，介绍启停、比较和恢复检查点，以及编辑消息时恢复文件与会话的关系。

- 来源：Cline
- 资料类型：产品指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-02-15](<https://github.com/cline/cline/commit/e884699d249b55496f18425276e4c001634a539c>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：检查点的覆盖范围以工具实现为准，不能推断外部系统操作也能回滚。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/core-workflows/checkpoints.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 检查点、比较改动与恢复文件.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Checkpoints"
sidebarTitle: "Checkpoints"
description: "Roll back code changes while keeping your conversation. Experiment freely."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

检查点可让您撤消代码更改而不会丢失对话。每次 Cline 修改文件或运行命令时，它都会保存项目文件的快照。您可以恢复到任何检查点，在恢复代码时保留您构建的上下文。

这会改变您使用 Cline 的方式。您可以让 Cline 快速移动并在出现问题时回滚，而不是在批准之前仔细审查每个更改。错误的成本几乎降至零。

**提示：**

默认情况下启用检查点。如果需要关闭检查点，请参阅[启用或禁用检查点](<#启用或禁用检查点>)。

## 它是如何运作的

Cline 维护一个与项目的实际 Git 历史记录分开的影子 Git 存储库。每次使用工具（文件编辑、命令等）后，Cline 都会将文件的当前状态提交到此影子存储库。您的主 Git 存储库保持不变。

这意味着：

- 您的 Git 历史记录保持干净并在您的控制之下
- 检查点捕获所有内容，包括 Git 未跟踪的文件
- 您可以恢复到任务中的任何点，而不会影响您所做的提交
- 检查点在编辑器会话中持续存在

每个检查点都会捕获当时的完整文件状态。如果Cline依次编辑三个文件，您将获得三个检查点，并且可以独立恢复到其中任何一个。

## 启用或禁用检查点

默认情况下启用检查点。要切换它们：

1. 打开Cline设置（Cline侧栏中的齿轮图标）
2. 滚动到“功能设置”部分
3. 切换“启用检查点”

**注释：**

对于非常大的存储库，检查点可能会使用大量存储并减慢 Cline，因为它会在每次使用工具后提交文件快照。如果您发现性能问题，请考虑禁用它们。

## 查看和比较更改

每次使用工具后，对话中都会出现一个检查点指示器。查找标记为“检查点”的书签图标，该图标带有连接到 **比较 ** 和 **恢复 ** 按钮的虚线。

单击 ** 比较 ** 打开差异视图，准确显示该检查点发生的更改。这将在编辑器的差异查看器中打开，让您查看所有受影响文件的添加、删除和修改。

当 Cline 做出您在决定是否保留之前想要了解的更改时，这非常有用。您可以查看差异，然后继续或恢复以撤消。

## 恢复检查点

单击任意步骤旁边的**恢复**以打开恢复菜单。您有三个选择：

|选项|它的作用|何时使用它|
|--------|--------------|----------------|
|**恢复文件**|将项目的文件恢复到此检查点的快照|撤消代码更改，同时保持对话|
|**仅恢复任务**|此后删除消息，不影响文件|在保留当前代码的同时尝试不同的提示|
|**恢复文件和任务**|此后恢复文件并删除消息|从已知的良好状态完全重新开始|

正确的选择取决于出了什么问题：

- 如果对话富有成效，但代码更改破坏了某些内容，请使用 ** 恢复文件 **。 Cline 保留您讨论过的所有上下文，并且可以尝试不同的实现。

- 如果 Cline 的代码更改良好，但对话偏离了轨道，请使用 ** 仅恢复任务 **。您保留文件并可以以不同的方式引导对话。

- 如果您想从头开始，请使用**恢复文件和任务**。这会将您的文件和对话重置为该检查点。

## 何时使用检查点

|场景|建议采取的行动|
|----------|-------------------|
|Cline 重构代码并破坏了一些东西|恢复文件，寻求不同的方法|
|尝试多种解决方案|比较每个检查点，恢复到最好的一个|
|Cline误解了你的意图|恢复文件和任务，重新表述您的请求|
|想尝试不同的提示|仅恢复任务，保留文件，重新提交|
|在提交到 Git 之前检查更改|使用 Compare 进行检查，然后手动提交|
|测试有风险的变更|让Cline继续，失败则恢复|

## 使用自动批准

检查点使 [ 自动批准 ](<https://docs.cline.bot/features/auto-approve>) 变得实用。如果没有检查点，自动批准会感觉有风险，因为 Cline 可以在您发现问题之前进行许多更改。通过检查点，您可以让 Cline 自主工作并在需要时回滚。

典型的工作流程：

1. 启用文件编辑和命令的自动批准
2. 让Cline快速完成您的任务
3. 查看最终结果
4. 如果出现问题，恢复到最后一个好的检查点
5. 给予Cline更具体的指导

这种方法比单独审查每个更改更快，并且检查点提供了安全网。

## 检查点和消息编辑

消息编辑功能与检查点集成。当您编辑上一条消息并选择“全部恢复”时，Cline 会将您的文件恢复到重新提交编辑的消息之前当时的检查点。

这样您就可以修复措辞不当的提示，并通过一项操作撤消由此产生的所有更改。


## 原文对照

<details>
<summary>展开完整原文</summary>

Checkpoints let you undo code changes without losing your conversation. Every time Cline modifies a file or runs a command, it saves a snapshot of your project files. You can restore to any checkpoint, keeping the context you've built while reverting the code.

This changes how you work with Cline. Instead of carefully reviewing every change before approving, you can let Cline move fast and roll back if something goes wrong. The cost of a mistake drops to nearly zero.

**提示：**

Checkpoints are enabled by default. See [Enable or Disable Checkpoints](<#enable-or-disable-checkpoints>) if you need to turn them off.

## How It Works

Cline maintains a shadow Git repository separate from your project's actual Git history. After each tool use (file edits, commands, etc.), Cline commits the current state of your files to this shadow repo. Your main Git repository stays untouched.

This means:

- Your Git history remains clean and under your control
- Checkpoints capture everything, including files not tracked by Git
- You can restore to any point in a task without affecting commits you've made
- Checkpoints persist across editor sessions

Each checkpoint captures the complete file state at that moment. If Cline edits three files in sequence, you get three checkpoints and can restore to any of them independently.

## Enable or Disable Checkpoints

Checkpoints are enabled by default. To toggle them:

1. Open Cline settings (gear icon in the Cline sidebar)
2. Scroll to the "Feature Settings" section
3. Toggle "Enable Checkpoints"

**注释：**

For very large repositories, checkpoints may use significant storage and slow down Cline as it commits file snapshots after each tool use. Consider disabling them if you notice performance issues.

## Viewing and Comparing Changes

After each tool use, a checkpoint indicator appears in your conversation. Look for a bookmark icon labeled "Checkpoint" with a dotted line connecting to **Compare** and **Restore** buttons.

Click **Compare** to open a diff view showing exactly what changed at that checkpoint. This opens in your editor's diff viewer, letting you see additions, deletions, and modifications across all affected files.

This is useful when Cline makes changes you want to understand before deciding whether to keep them. You can review the diff, then either continue or restore to undo.

## Restoring Checkpoints

Click **Restore** next to any step to open the restore menu. You have three options:

| Option | What It Does | When to Use It |
|--------|--------------|----------------|
| **Restore Files** | Reverts your project's files to the snapshot at this checkpoint | Undoing code changes while keeping the conversation |
| **Restore Task Only** | Deletes messages after this point, does not affect files | Trying a different prompt while keeping current code |
| **Restore Files & Task** | Reverts files and deletes messages after this point | Starting over completely from a known good state |

The right choice depends on what went wrong:

- If the conversation is productive but the code changes broke something, use **Restore Files**. Cline keeps all the context you've discussed and can try a different implementation.

- If Cline's code changes are good but the conversation went off track, use **Restore Task Only**. You keep the files and can guide the conversation differently.

- If you want to start over from a clean slate, use **Restore Files & Task**. This resets both your files and the conversation to that checkpoint.

## When to Use Checkpoints

| Scenario | Recommended Action |
|----------|-------------------|
| Cline refactored code and broke something | Restore Files, ask for a different approach |
| Experimenting with multiple solutions | Compare each checkpoint, restore to the best one |
| Cline misunderstood your intent | Restore Files & Task, rephrase your request |
| Want to try a different prompt | Restore Task Only, keep the files, resubmit |
| Reviewing changes before committing to Git | Use Compare to inspect, then commit manually |
| Testing risky changes | Let Cline proceed, restore if it fails |

## Working with Auto-Approve

Checkpoints make [auto-approve](<https://docs.cline.bot/features/auto-approve>) practical. Without checkpoints, auto-approve feels risky because Cline can make many changes before you notice a problem. With checkpoints, you can let Cline work autonomously and roll back if needed.

A typical workflow:

1. Enable auto-approve for file edits and commands
2. Let Cline work through your task quickly
3. Review the final result
4. If something is wrong, restore to the last good checkpoint
5. Give Cline more specific guidance

This approach is faster than reviewing every change individually, and checkpoints provide the safety net.

## Checkpoints and Message Editing

The message editing feature integrates with checkpoints. When you edit a previous message and select "Restore All," Cline restores your files to the checkpoint at that point before resubmitting your edited message.

This lets you fix a poorly worded prompt and undo all the changes that resulted from it in one action.


</details>
