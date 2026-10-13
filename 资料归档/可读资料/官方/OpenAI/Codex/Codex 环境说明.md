# Codex 环境说明

介绍任务运行环境及相关配置入口，帮助定位环境准备和执行条件；按归档内容阅读。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/environments/modes>)）；本地抓取：2026-10-09
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/environments/modes.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 环境说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Codex 环境

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

在 ChatGPT 桌面应用程序中，打开 ChatGPT 下拉列表并选择 **Codex**。
启动 Codex 聊天时，选择其运行位置：

- **本地**：直接在当前项目目录中工作。
- **Worktree**：隔离 Git 工作树中的更改。 [了解更多](<https://learn.chatgpt.com/docs/environments/git-worktrees>)。
- **云**：从已发布的、在其自己的工作区中启动远程任务，
可重复使用的环境。请参阅[云环境](<https://learn.chatgpt.com/docs/environments/cloud-environments>)
来准备和发布您的任务所需的存储库、依赖项和工具。

**本地**和**工作树**聊天都在您的计算机上运行。
使用**工作在**中选择**这个电脑**或**云**。对于本地 Git 项目，
单独的 **Worktree** 控件可让您将更改与当前签出隔离。

云任务需要您可以访问的已发布环境。如果没有可用的，
在网络或桌面应用程序中创建一个，或请求工作区管理员访问
共享环境。选择环境与创建或
发布环境是分开的。共享访问让您可以使用环境；它不授予
编辑它的权限。

您还可以在 Web 或移动设备上启动并继续云任务。在移动设备上，
打开**Codex**并选择已发布的环境。

要创建环境，请在网络或桌面应用程序中启动新任务。
选择**在** > **云**，打开**选择环境**，然后选择
**创建环境**。 Codex 检查您选择的存储库并帮助
在发布之前准备和测试其设置。

每个云任务都有自己的工作文件。任务中的文件更改不会更新
可重用环境。要更改未来任务的起点，请更新 
 并重新发布环境的设置。

云任务使用其云环境中可用的文件和服务。
您计算机的本地文件、正在运行的进程、浏览器登录和VPN访问
不会自动传输到它。

有关更多术语，请参阅[Codex概念](<https://learn.chatgpt.com/docs/prompting>)。



  

> 插图：桌面编辑器，其中“工作范围”菜单向“本计算机”和“云”打开，以及一个单独的“工作树”选项


## 原文对照

<details>
<summary>展开完整原文</summary>

# Codex environments

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

In the ChatGPT desktop app, open the ChatGPT dropdown and select **Codex**.
When starting a Codex chat, choose where it runs:

- **Local**: work directly in your current project directory.
- **Worktree**: isolate changes in a Git worktree. [Learn more](<https://learn.chatgpt.com/docs/environments/git-worktrees>).
- **Cloud**: start a remote task in its own workspace from a published,
  reusable environment. See [Cloud environments](<https://learn.chatgpt.com/docs/environments/cloud-environments>)
  to prepare and publish the repositories, dependencies, and tools your tasks need.

Both **Local** and **Worktree** chats run on your computer.
Use **Work in** to choose **This computer** or **Cloud**. For a local Git project,
the separate **Worktree** control lets you isolate changes from your current checkout.

A cloud task requires a published environment you can access. If none is available,
create one on the web or in the desktop app, or ask a workspace admin for access
to a shared environment. Selecting an environment is separate from creating or
publishing it. Shared access lets you use the environment; it doesn't grant
permission to edit it.

You can also start and continue cloud tasks on the web or on mobile. On mobile,
open **Codex** and select a published environment.

To create an environment, start a new task on the web or in the desktop app.
Choose **Work in** > **Cloud**, open **Select environment**, then select
**Create environment**. Codex inspects your selected repositories and helps
prepare and test their setup before you publish it.

Each Cloud task has its own working files. File changes in a task don't update
the reusable environment. To change the starting point for future tasks, update
and republish the environment's setup.

A cloud task uses the files and services available in its cloud environment.
Your computer's local files, running processes, browser sign-ins, and VPN access
aren't automatically transferred to it.

For more terms, see [Codex concepts](<https://learn.chatgpt.com/docs/prompting>).



  

> Illustration: Desktop composer with the Work in menu open to This computer and Cloud, and a separate Worktree option


</details>
