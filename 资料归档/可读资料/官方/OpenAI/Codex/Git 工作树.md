# Git 工作树

介绍工作树概念、创建与使用方式、本地和隔离目录之间的交接，以及并行任务管理。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/environments/git-worktrees>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/environments/git-worktrees.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Git 工作树.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 工作树

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Worktrees 让 Codex 在同一项目中运行多个独立的聊天，而不会互相干扰。存储库、工作树和命令保留在包含项目的计算机或远程开发环境中。您可以直接在 ChatGPT 桌面应用程序中工作，或使用 ChatGPT 移动应用程序中的 [Codex](<https://learn.chatgpt.com/docs/mobile>) 在已连接的计算机上启动、指导、批准和审查工作树聊天。

对于 Git 存储库，[计划任务](<https://learn.chatgpt.com/docs/automations>)可以在专用后台工作树上运行，因此它们不会与您正在进行的工作发生冲突。在非版本控制的项目中，计划任务直接在项目目录中运行。您还可以在工作树中手动启动聊天，并使用切换在本地和工作树之间移动聊天。

工作树不在您的手机上本地运行。 ChatGPT 移动应用程序控制已连接计算机上的 
 Codex（存储库和工作树保留在其中），或控制计算机使用的远程开发环境中的 
。下面特定于桌面的 
 说明适用于连接的计算机。

## 什么是工作树

工作树仅适用于属于 Git 存储库的项目，因为它们在底层使用 [Git 工作树 ](<https://git-scm.com/docs/git-worktree>)。工作树允许您创建存储库的第二个副本（“签出”）。每个工作树都有自己的存储库中每个文件的副本，但它们都共享有关提交、分支等的相同元数据（`.git` 文件夹）。这允许您并行签出并处理多个分支。

## 术语

- **本地签出**：您创建的存储库。有时在 ChatGPT 桌面应用程序中简称为 **Local**。
- **Worktree**：从 ChatGPT 桌面应用程序中的本地结帐创建的 [Git 工作树 ](<https://git-scm.com/docs/git-worktree>)。
- **Handoff**：在本地和工作树之间移动聊天的流程。 Codex 处理在它们之间安全移动工作所需的 Git 操作。

## 为什么使用工作树

1. 与 Codex 并行工作，不会干扰您当前的本地设置。
2. 当您专注于前台时，将后台工作排队。
3. 当您准备好更直接地检查、测试或协作时，请将聊天移至本地。

## 开始使用

工作树需要 Git 存储库。确保您选择的项目位于其中。

<WorkflowSteps variant="headings">

1.  选择“工作树”

在新的聊天视图中，选择编辑器下的 ** 工作树 **。
 或者，选择 [ 本地环境 ](<https://learn.chatgpt.com/docs/environments/local-environment>) 来运行工作树的设置脚本。

2.  选择起始分支

在编辑器下方，选择工作树所基于的 Git 分支。这可以是您的 `main` / `master` 分支、功能分支或具有未暂存本地更改的当前分支。

3.  提交您的提示

提交您的提示，Codex 根据您选择的分支创建一个 Git 工作树。默认情况下，Codex 在 [“独立 HEAD”](<https://git-scm.com/docs/git-checkout#_detached_head>) 中工作。

4.  选择继续工作的地方

准备好后，您可以继续直接在工作树上工作，也可以将聊天交给本地结账。与本地之间的切换会移动您的聊天 _ 和 _ 代码，以便您可以继续进行其他结账。

</WorkflowSteps>

## 在本地和工作树之间工作

工作树的外观和感觉很像您本地的结帐处。不同之处在于它们适合您流程的位置。您可以将Local视为前景，将Worktree视为背景。切换可让您在他们之间移动聊天。

在底层，Handoff 处理在两个结账之间安全移动工作所需的 Git 操作。这很重要，因为 **Git 只允许一次在一个地方检出分支 **。如果您检出工作树上的分支，您**无法**同时在本地检出中检出它，反之亦然。

在实践中，有两种常见的路径：

1. [专门在工作树上工作](<#选项-1在工作树上工作>)。当您可以直接在工作树上验证更改时，此路径效果最佳，例如，因为您使用 [ 本地环境设置脚本 ](<https://learn.chatgpt.com/docs/environments/local-environment>) 安装了依赖项和工具。
2. [将聊天交给本地](<#选项-2将聊天转交给本地>)。当您想要将聊天带入前台时，请使用此选项，例如因为您想要检查常用 IDE 中的更改，或者只能运行应用程序的一个实例。

### 选项 1：在工作树上工作







如果您想将更改保留在工作树上，请使用聊天标题中的 **在此处创建分支** 按钮将工作树转换为分支。

从这里您可以提交更改，将分支推送到远程存储库，然后在 GitHub 上打开拉取请求。

您可以使用标题中的“打开”按钮将 IDE 打开到工作树，使用集成终端或需要从工作树目录执行的任何其他操作。





  

> 插图：带有分支控件和工作树详细信息的工作树聊天视图







请记住，如果您在工作树上创建分支，则无法在任何其他工作树中检出它，包括本地检出。

<a id="option-2-handing-a-thread-off-to-local"></a>
<a id="option-2-handing-a-chat-off-to-local"></a>
<a id="option-2-handing-a-task-off-to-local"></a>

### 选项 2：将聊天转交给本地







如果您想将聊天置于前台，请在聊天标题中选择**Hand off**并将其移至**Local**。

当您想要在常用的 IDE 窗口中读取更改、运行现有的开发服务器或在您日常使用的同一环境中验证工作时，此路径非常有效。

Codex 处理在工作树和本地结帐之间安全移动聊天所需的 Git 步骤。

随着时间的推移，每个聊天都会保留相同的关联工作树。如果您稍后将聊天交回工作树，Codex 将其返回到相同的后台环境，以便您可以从上次中断的地方继续。





  

> 插图：切换对话框将聊天从工作树移动到本地







你也可以走另一个方向。如果您已经在本地工作并且想要释放前台，请使用 **Hand off** 将聊天移至工作树。当您希望 Codex 继续在后台工作，同时将注意力转移回本地其他事情时，这非常有用。

由于 Handoff 使用 Git 操作，因此 `.gitignore` 文件中的任何文件都不会随聊天一起移动，除非 Codex 将它们复制到具有 `.worktreeinclude` 的本地托管工作树中。

## 高级细节

### Codex-托管和永久工作树

默认情况下，聊天使用 Codex 管理的工作树。这些旨在感觉轻便且一次性。 Codex 管理的工作树通常专用于一个聊天，并且如果您稍后将该聊天交回那里，Codex 会将该聊天返回到同一工作树。

如果您想要一个长期存在的环境，请从侧边栏项目上的三点菜单创建永久工作树。这将创建一个新的永久工作树作为其自己的项目。永久工作树不会自动删除，您可以从同一工作树启动多个聊天。

### Codex 如何为您管理工作树

Codex 在 `$CODEX_HOME/worktrees` 中创建工作树。起始提交是您开始聊天时选择的分支的 `HEAD` 提交。如果您选择具有本地更改的分支，Codex 也会将未提交的更改应用到工作树。工作树不作为分支检出。它处于 [ 分离 HEAD](<https://git-scm.com/docs/git-checkout#_detached_head>) 状态。这可以让 Codex 创建多个工作树，而不会污染您的分支。

### 将忽略的本地文件复制到托管工作树中

本地 Codex 管理的工作树从 Git 签出开始，因此跟踪的文件已经存在。如果您的存储库忽略新工作树所需的本地设置文件，请将 `.worktreeinclude` 文件添加到存储库根目录，并列出在 Codex 创建托管工作树时要复制的忽略路径或 `.gitignore` 样式模式。

将此用于 Git 故意忽略的文件，例如 `.env`、`.env.local` 或 `config/secrets.json`。 Codex仅复制与`.worktreeinclude`匹配的忽略文件；它不会复制 Git 不跟踪的其他本地文件。不要列出跟踪的文件。

Codex 会自动将忽略的 `AGENTS.override.md` 复制到本地托管工作树中，因此您无需将其列在 `.worktreeinclude` 中。

```text
# .worktreeinclude
.env
.env.local
config/secrets.json
```

Codex 跳过源符号链接，并且不会覆盖新签出中已存在的文件。此行为适用于本地 ChatGPT 桌面应用程序管理的工作树，而不是远程工作树或您从命令行自行创建的 Git 工作树。

### 分支机构限制

假设 Codex 完成了工作树上的一些工作，并且您选择使用 **在此处创建分支 ** 在其上创建 `feature/a` 分支。现在，您想在本地结帐处尝试一下。如果您尝试签出该分支，您将收到以下错误：

```
fatal: 'feature/a' is already used by worktree at '<WORKTREE_PATH>'
```

要解决此问题，您需要检查工作树上的另一个分支而不是 `feature/a`。

如果您计划在本地签出分支，请使用 Handoff 将聊天移至本地，而不是尝试同时在两个位置签出同一分支。

<ToggleSection title="Why this limitation exists">
Git 阻止同一分支同时在多个工作树中签出，因为分支代表单个可变引用 (`refs/heads/<name>`)，其含义是工作树的“当前签出状态”。

当签出分支时，Git 将其 HEAD 视为该工作树所拥有，并期望提交、重置、变基和合并等操作以明确定义的序列化方式推进该引用。允许多个工作树同时签出同一分支会产生歧义和竞争条件，工作树的操作会围绕该条件更新分支引用，从而可能导致提交丢失、索引不一致或冲突解决方案不明确。

通过强制每个工作树一个分支规则，Git 保证每个分支都有一个权威的工作副本，同时仍然允许其他工作树通过分离的 HEAD 或单独的分支安全地引用相同的提交。

</ToggleSection>

### 工作树清理

工作树可能会占用大量磁盘空间。每个都有自己的一组存储库文件、依赖项、构建缓存等。因此，ChatGPT 桌面应用程序尝试将工作树的数量保持在合理的限制内。

默认情况下，Codex 保留最新的 15 Codex 管理的工作树。如果您希望自己管理磁盘使用情况，可以更改此限制或在设置中关闭自动删除。

Codex 尝试避免删除仍然重要的工作树。在以下情况下，Codex 管理的工作树不会自动删除：

- 固定的聊天与其绑定
- 聊天仍在进行中
- 工作树是永久工作树

在以下情况下，Codex 管理的工作树会自动删除：

- 您存档关联的聊天记录
- Codex 需要删除较旧的工作树以保持在配置的限制内

在删除 Codex 管理的工作树之前，Codex 会保存其工作的快照。如果您在删除工作树后打开聊天，您将看到恢复它的选项。

## 常见问题

<ToggleSection title="Can I control where worktrees are created?">
  是的。 Codex 在下创建托管工作树 `$CODEX_HOME/worktrees` 通过
  默认。要选择其他位置，请打开 **设置 > 工作树** 并改变
  **工作树根**.
</ToggleSection>

<a id="can-i-move-a-chat-between-local-and-worktree"></a>

<ToggleSection title="Can I move a chat between Local and Worktree?">
 是的。使用聊天标题中的 **Hand off ** 在本地 
 结帐和工作树之间移动聊天。 Codex 处理在环境之间安全移动 
 聊天所需的 Git 操作。如果您稍后将聊天交回工作树，
 Codex 将其返回到相同的关联工作树。
</ToggleSection>

<a id="what-happens-to-chats-if-a-worktree-is-deleted"></a>

<ToggleSection title="What happens to chats if a worktree is deleted?">
 即使基础工作树目录被
删除，聊天也可以保留在您的历史记录中。对于 Codex 管理的工作树，Codex 在删除 
 工作树之前保存快照，并在您重新打开关联的聊天时提供恢复快照。
 当您存档其 
 聊天时，永久工作树不会自动删除。
</ToggleSection>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Worktrees

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Worktrees let Codex run multiple independent chats in the same project without interfering with each other. The repository, worktree, and commands remain on the computer or remote development environment that contains the project. You can work directly in the ChatGPT desktop app, or use [Codex](<https://learn.chatgpt.com/docs/mobile>) in the ChatGPT mobile app to start, guide, approve, and review worktree chats on a connected computer.

For Git repositories, [scheduled tasks](<https://learn.chatgpt.com/docs/automations>) can run on dedicated background worktrees so they don't conflict with your ongoing work. In non-version-controlled projects, scheduled tasks run directly in the project directory. You can also start chats in a worktree manually and use Handoff to move a chat between Local and Worktree.

Worktrees don't run locally on your phone. The ChatGPT mobile app controls
  Codex on your connected computer, where the repository and worktree remain, or
  in the remote development environment that computer uses. The desktop-specific
  instructions below apply on the connected computer.

## What's a worktree

Worktrees only work in projects that are part of a Git repository since they use [Git worktrees](<https://git-scm.com/docs/git-worktree>) under the hood. A worktree allows you to create a second copy ("checkout") of your repository. Each worktree has its own copy of every file in your repo but they all share the same metadata (`.git` folder) about commits, branches, etc. This allows you to check out and work on multiple branches in parallel.

## Terminology

- **Local checkout**: The repository that you created. Sometimes just referred to as **Local** in the ChatGPT desktop app.
- **Worktree**: A [Git worktree](<https://git-scm.com/docs/git-worktree>) that was created from your local checkout in the ChatGPT desktop app.
- **Handoff**: The flow that moves a chat between Local and Worktree. Codex handles the Git operations required to move your work safely between them.

## Why use a worktree

1. Work in parallel with Codex without disturbing your current Local setup.
2. Queue up background work while you stay focused on the foreground.
3. Move a chat into Local later when you're ready to inspect, test, or collaborate more directly.

## Getting started

Worktrees require a Git repository. Make sure the project you selected lives in one.

<WorkflowSteps variant="headings">

1.  Select "Worktree"

    In the new chat view, select **Worktree** under the composer.
    Optionally, choose a [local environment](<https://learn.chatgpt.com/docs/environments/local-environment>) to run setup scripts for the worktree.

2.  Select the starting branch

    Below the composer, choose the Git branch to base the worktree on. This can be your `main` / `master` branch, a feature branch, or your current branch with unstaged local changes.

3.  Submit your prompt

    Submit your prompt, and Codex creates a Git worktree based on the branch you selected. By default, Codex works in a ["detached HEAD"](<https://git-scm.com/docs/git-checkout#_detached_head>).

4.  Choose where to keep working

    When you're ready, you can either keep working directly on the worktree or hand the chat off to your local checkout. Handing off to or from Local moves your chat _and_ code so you can continue in the other checkout.

</WorkflowSteps>

## Working between Local and Worktree

Worktrees look and feel much like your local checkout. The difference is where they fit into your flow. You can think of Local as the foreground and Worktree as the background. Handoff lets you move a chat between them.

Under the hood, Handoff handles the Git operations required to move work between two checkouts safely. This matters because **Git only allows a branch to be checked out in one place at a time**. If you check out a branch on a worktree, you **can't** check it out in your local checkout at the same time, and vice versa.

In practice, there are two common paths:

1. [Work exclusively on the worktree](<#option-1-working-on-the-worktree>). This path works best when you can verify changes directly on the worktree, for example because you have dependencies and tools installed using a [local environment setup script](<https://learn.chatgpt.com/docs/environments/local-environment>).
2. [Hand the chat off to Local](<#option-2-handing-a-chat-off-to-local>). Use this when you want to bring the chat into the foreground, for example because you want to inspect changes in your usual IDE or can run only one instance of your app.

### Option 1: Working on the worktree







If you want to stay exclusively on the worktree with your changes, turn your worktree into a branch using the **Create branch here** button in the chat header.

From here you can commit your changes, push your branch to your remote repository, and open a pull request on GitHub.

You can open your IDE to the worktree using the "Open" button in the header, use the integrated terminal, or anything else that you need to do from the worktree directory.





  

> Illustration: Worktree chat view with branch controls and worktree details







Remember, if you create a branch on a worktree, you can't check it out in any other worktree, including your local checkout.

<a id="option-2-handing-a-thread-off-to-local"></a>
<a id="option-2-handing-a-chat-off-to-local"></a>
<a id="option-2-handing-a-task-off-to-local"></a>

### Option 2: Handing a chat off to Local







If you want to bring a chat into the foreground, select **Hand off** in the chat header and move it to **Local**.

This path works well when you want to read the changes in your usual IDE window, run your existing development server, or validate the work in the same environment you already use day to day.

Codex handles the Git steps required to move the chat safely between the worktree and your local checkout.

Each chat keeps the same associated worktree over time. If you hand the chat back to a worktree later, Codex returns it to that same background environment so you can pick up where you left off.





  

> Illustration: Handoff dialog moving a chat from a worktree to Local







You can also go the other direction. If you're already working in Local and want to free up the foreground, use **Hand off** to move the chat to a worktree. This is useful when you want Codex to keep working in the background while you switch your attention back to something else locally.

Since Handoff uses Git operations, any files that are part of your `.gitignore` file won't move with the chat unless Codex copies them into a local managed worktree with `.worktreeinclude`.

## Advanced details

### Codex-managed and permanent worktrees

By default, chats use a Codex-managed worktree. These are meant to feel lightweight and disposable. A Codex-managed worktree is typically dedicated to one chat, and Codex returns that chat to the same worktree if you hand it back there later.

If you want a long-lived environment, create a permanent worktree from the three-dot menu on a project in the sidebar. This creates a new permanent worktree as its own project. Permanent worktrees aren't automatically deleted, and you can start multiple chats from the same worktree.

### How Codex manages worktrees for you

Codex creates worktrees in `$CODEX_HOME/worktrees`. The starting commit is the `HEAD` commit of the branch selected when you start your chat. If you chose a branch with local changes, Codex applies the uncommitted changes to the worktree as well. The worktree isn't checked out as a branch. It's in a [detached HEAD](<https://git-scm.com/docs/git-checkout#_detached_head>) state. This lets Codex create several worktrees without polluting your branches.

### Copy ignored local files into managed worktrees

Local Codex-managed worktrees start from a Git checkout, so tracked files are already present. If your repository ignores local setup files that a new worktree needs, add a `.worktreeinclude` file to the repository root and list the ignored paths or `.gitignore`-style patterns to copy when Codex creates a managed worktree.

Use this for files Git intentionally ignores, such as `.env`, `.env.local`, or `config/secrets.json`. Codex only copies ignored files that match `.worktreeinclude`; it doesn't copy other local files that Git doesn't track. Don't list tracked files.

Codex automatically copies an ignored `AGENTS.override.md` into local managed worktrees, so you don't need to list it in `.worktreeinclude`.

```text
# .worktreeinclude
.env
.env.local
config/secrets.json
```

Codex skips source symlinks and won't overwrite files that already exist in the new checkout. This behavior applies to local ChatGPT desktop app managed worktrees, not remote worktrees or Git worktrees you create yourself from the command line.

### Branch limitations

Suppose Codex finishes some work on a worktree and you choose to create a `feature/a` branch on it using **Create branch here**. Now, you want to try it on your local checkout. If you tried to check out the branch, you would get the following error:

```
fatal: 'feature/a' is already used by worktree at '<WORKTREE_PATH>'
```

To resolve this, you would need to check out another branch instead of `feature/a` on the worktree.

If you plan on checking out the branch locally, use Handoff to move the chat into Local instead of trying to keep the same branch checked out in both places at once.

<ToggleSection title="Why this limitation exists">
Git prevents the same branch from being checked out in more than one worktree at a time because a branch represents a single mutable reference (`refs/heads/<name>`) whose meaning is “the current checked-out state” of a working tree.

When a branch is checked out, Git treats its HEAD as owned by that worktree and expects operations like commits, resets, rebases, and merges to advance that reference in a well-defined, serialized way. Allowing multiple worktrees to simultaneously check out the same branch would create ambiguity and race conditions around which worktree’s operations update the branch reference, potentially leading to lost commits, inconsistent indexes, or unclear conflict resolution.

By enforcing a one-branch-per-worktree rule, Git guarantees that each branch has a single authoritative working copy, while still allowing other worktrees to safely reference the same commits via detached HEADs or separate branches.

</ToggleSection>

### Worktree cleanup

Worktrees can take up a lot of disk space. Each one has its own set of repository files, dependencies, build caches, etc. As a result, the ChatGPT desktop app tries to keep the number of worktrees to a reasonable limit.

By default, Codex keeps your most recent 15 Codex-managed worktrees. You can change this limit or turn off automatic deletion in settings if you prefer to manage disk usage yourself.

Codex tries to avoid deleting worktrees that are still important. Codex-managed worktrees won't be deleted automatically if:

- A pinned chat is tied to it
- The chat is still in progress
- The worktree is a permanent worktree

Codex-managed worktrees are deleted automatically when:

- You archive the associated chat
- Codex needs to delete older worktrees to stay within your configured limit

Before deleting a Codex-managed worktree, Codex saves a snapshot of the work on it. If you open a chat after its worktree was deleted, you'll see the option to restore it.

## Frequently asked questions

<ToggleSection title="Can I control where worktrees are created?">
  Yes. Codex creates managed worktrees under `$CODEX_HOME/worktrees` by
  default. To choose another location, open **Settings > Worktrees** and change
  **Worktree root**.
</ToggleSection>

<a id="can-i-move-a-chat-between-local-and-worktree"></a>

<ToggleSection title="Can I move a chat between Local and Worktree?">
  Yes. Use **Hand off** in the chat header to move a chat between your local
  checkout and a worktree. Codex handles the Git operations needed to move the
  chat safely between environments. If you hand a chat back to a worktree later,
  Codex returns it to the same associated worktree.
</ToggleSection>

<a id="what-happens-to-chats-if-a-worktree-is-deleted"></a>

<ToggleSection title="What happens to chats if a worktree is deleted?">
  Chats can remain in your history even if the underlying worktree directory is
  deleted. For Codex-managed worktrees, Codex saves a snapshot before deleting
  the worktree and offers to restore it if you reopen the associated chat.
  Permanent worktrees are not automatically deleted when you archive their
  chats.
</ToggleSection>


</details>
