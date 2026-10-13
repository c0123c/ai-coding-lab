# 在 GitHub 上启动 Agent 任务

介绍通过 Issue、提示或新仓库启动任务，选择直接创建拉取请求或先在分支上研究和迭代，再进入审查流程。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-04-17](<https://github.com/github/docs/commit/29f73843427229f298e27ca12b42709455689401>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/kick-off-a-task>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/在 GitHub 上启动 Agent 任务.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 在 GitHub 上使用 Copilot agents 开始任务

决定 Copilot 云 agent 是立即创建拉取请求，还是在您首先审查和迭代的分支上工作。

您可以通过多种方式启动 Copilot 云 agent 任务。 **分配问题**始终会创建拉取请求。 **从提示开始** 默认情况下适用于分支，让您有机会在打开拉取请求之前进行审查、引导和迭代。 **播种存储库** 使用脚手架代码创建草稿拉取请求。

## 将问题分配给 Copilot

分配问题总是会创建拉取请求。 Copilot 正在处理该任务并请求您在完成后进行审核。

1. 在问题的右侧栏中，单击**受让人**。

2. 从受让人列表中单击 **Copilot**。

3. （可选）在 **可选提示 ** 字段中添加上下文 - 例如，编码模式、要修改的文件或测试要求。

4. （可选）使用下拉菜单更改目标存储库或基础分支。

5. 或者，如果您想要分配具有专门行为和工具的 agent 或自定义 agent，您可以单击 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> 打开 agent 下拉菜单。您可以从您的存储库、组织或企业中选择现有的自定义 agent。您还可以单击 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Plus button" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> 创建 agent** 在您选择的存储库和分支中创建新的 agent 配置文件。有关更多信息，请参阅[为Copilot云agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)创建自定义agents。
   > \[！NOTE] 第三方编码 agents 适用于所有付费 [Copilot 计划 ](<https://docs.github.com/en/copilot/get-started/plans>)。

6. 或者，您可以使用下拉菜单选择 Copilot 将使用的型号。如果所选模型支持可配置推理，您还可以使用下拉菜单选择推理级别。有关更多信息，请参阅 [更改 GitHub Copilot 云 agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>) 的 AI 模型。

Copilot 在分配时接收问题标题、描述和现有评论。它不会看到分配后添加的评论，因此请在拉取请求上发布后续信息。

## 有提示启动任务

云agent默认在分支上工作。您可以查看差异、使用后续提示进行迭代，并在准备好时创建拉取请求。

1. 打开 agents 面板或选项卡：

   * 打开存储库中的 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="The Agents icon" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg> Agents** 选项卡。
   * **导航至agents页面**：Go至[github.com/copilot/agents](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。您也可以通过打开agents面板，然后单击**查看全部**来到达这里。
   * **打开agents面板**：点击GitHub右上角导航栏中的<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="The Agents icon" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg>。

2. 使用提示字段中的下拉菜单，选择您想要 Copilot 使用的存储库。

3. 键入描述您的请求的提示。您还可以通过粘贴、拖动或上传图像来添加视觉输入，例如屏幕截图或 UI 模型。支持的文件：image/png、image/jpeg、image/gif、image/webp.

例如，`Implement a user friendly message for common errors.`

如果您希望 Copilot 打开拉取请求，您可以在提示中询问，例如 `Open a pull request to implement a user friendly message for common errors.`

4. （可选）为 Copilot 的更改选择一个基础分支。 Copilot将基于此分支创建一个新分支。

5. 或者，如果您想要分配具有专门行为和工具的 agent 或自定义 agent，您可以单击 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> 打开 agent 下拉菜单。您可以从您的存储库、组织或企业中选择现有的自定义 agent。您还可以单击 **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Plus button" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> 创建 agent** 在您选择的存储库和分支中创建新的 agent 配置文件。有关更多信息，请参阅[为Copilot云agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>)创建自定义agents。
   > \[！NOTE] 第三方编码 agents 适用于所有付费 [Copilot 计划 ](<https://docs.github.com/en/copilot/get-started/plans>)。

6. 或者，您可以使用下拉菜单选择 Copilot 将使用的型号。如果所选模型支持可配置推理，您还可以使用下拉菜单选择推理级别。有关更多信息，请参阅 [更改 GitHub Copilot 云 agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>) 的 AI 模型。

7. 单击**<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-paper-airplane" aria-label="Start task" role="img"><path d="M.989 8 .064 2.68a1.342 1.342 0 0 1 1.85-1.462l13.402 5.744a1.13 1.13 0 0 1 0 2.076L1.913 14.782a1.343 1.343 0 0 1-1.85-1.463L.99 8Zm.603-5.288L2.38 7.25h4.87a.75.75 0 0 1 0 1.5H2.38l-.788 4.538L13.929 8Z"></path></svg>**或按<kbd>输入</kbd>。

Copilot将启动一个新会话，该会话将出现在提示框下方的列表中。 Copilot 将处理该任务并推送任何代码更改。

您可以跟踪 Copilot 的工作，并从会话日志中一键打开拉取请求。有关更多信息，请参阅[管理agent会话](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)。

您的 [ 仪表板 ](<https://github.com>) 和 Copilot 聊天中（输入 `/task`）也会出现相同的提示框。

有关创建拉取请求之前研究、规划和迭代的完整工作流程，请参阅 [使用 Copilot 云 agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/research-plan-iterate>) 研究、规划和迭代代码更改。

## 播种新的存储库

当您创建新存储库时，您可以让Copilot生成起始代码。

1. 在任意页面的右上角，选择<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Create something new" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg>，然后单击**新建存储库**。

![GitHub 下拉菜单的屏幕截图，显示用于创建新项目的选项。菜单项“新存储库”以深橙色轮廓显示。](<https://docs.github.com/assets/images/help/repository/repo-create-global-nav-update.png>)
2. 在 ** 提示 ** 字段中，描述您想要 Copilot 构建的内容 — 例如，`Create a Rust CLI for converting CSV spreadsheets to Markdown`。
3. 单击**创建存储库**。

Copilot 使用脚手架代码打开草稿拉取请求。

## 进一步阅读

* [使用GitHub Copilot处理任务的最佳实践](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>)
* [管理 agent 会话](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Kick off a task with Copilot agents on GitHub

Decide whether Copilot cloud agent creates a pull request immediately or works on a branch you review and iterate on first.

You can start a Copilot cloud agent task in several ways. **Assigning an issue** always creates a pull request. **Starting with a prompt** works on a branch by default, giving you a chance to review, steer, and iterate before you open a pull request. **Seeding a repository** creates a draft pull request with scaffolded code.

## Assign an issue to Copilot

Assigning an issue always creates a pull request. Copilot works on the task and requests your review when it finishes.

1. In the right sidebar of the issue, click **Assignees**.

2. Click **Copilot** from the assignees list.

3. Optionally, add context in the **Optional prompt** field—for example, coding patterns, files to modify, or testing requirements.

4. Optionally, change the target repository or base branch using the dropdown menus.

5. Optionally, you can click <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> to open the agent dropdown menu, if you want to assign an agent or a custom agent with specialized behavior and tools. You can select an existing custom agent from your repository, organization, or enterprise. You can also click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Plus button" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> Create an agent** to create a new agent profile in your selected repository and branch. For more information, see [Creating custom agents for Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>).
   > \[!NOTE] Third-party coding agents are available for all paid [Copilot plans](<https://docs.github.com/en/copilot/get-started/plans>).

6. Optionally, you can use the dropdown menu to select the model that Copilot will use. If the selected model supports configurable reasoning, you can also use the dropdown menu to select the reasoning level. For more information, see [Changing the AI model for GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>).

Copilot receives the issue title, description, and existing comments at assignment time. It does not see comments added after assignment, so post follow-up information on the pull request instead.

## Start a task with a prompt

Cloud agent works on a branch by default. You can review the diff, iterate with follow-up prompts, and create a pull request when you're ready.

1. Open the agents panel or tab:

   * Open the **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="The Agents icon" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg> Agents** tab in a repository.
   * **Navigate to the agents page**: Go to [github.com/copilot/agents](<https://github.com/copilot/agents?ref_product=copilot\&ref_type=engagement\&ref_style=text>). You can also get here by opening the agents panel, then clicking **View all**.
   * **Open the agents panel**: Click <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-agent" aria-label="The Agents icon" role="img"><path d="M14.5 8.9v-.052A2.956 2.956 0 0 0 11.542 5.9a.815.815 0 0 1-.751-.501l-.145-.348A3.496 3.496 0 0 0 7.421 2.9h-.206a3.754 3.754 0 0 0-3.736 4.118l.011.121a.822.822 0 0 1-.619.879A1.81 1.81 0 0 0 1.5 9.773v.14c0 1.097.89 1.987 1.987 1.987H4.5a.75.75 0 0 1 0 1.5H3.487A3.487 3.487 0 0 1 0 9.913v-.14C0 8.449.785 7.274 1.963 6.75A5.253 5.253 0 0 1 7.215 1.4h.206a4.992 4.992 0 0 1 4.586 3.024A4.455 4.455 0 0 1 16 8.848V8.9a.75.75 0 0 1-1.5 0Z"></path><path d="m8.38 7.67 2.25 2.25a.749.749 0 0 1 0 1.061L8.38 13.23a.749.749 0 1 1-1.06-1.06l1.719-1.72L7.32 8.731A.75.75 0 0 1 8.38 7.67ZM15 13.45h-3a.75.75 0 0 1 0-1.5h3a.75.75 0 0 1 0 1.5Z"></path></svg> in the navigation bar at the top right of GitHub.

2. Using the dropdown menu in the prompt field, select the repository you want Copilot to work in.

3. Type a prompt describing your request. You can also add visual inputs like screenshots or UI mockups by pasting, dragging, or uploading an image. Files supported: image/png, image/jpeg, image/gif, image/webp.

   For example, `Implement a user friendly message for common errors.`

   If you want Copilot to open a pull request, you can ask in your prompt, for example `Open a pull request to implement a user friendly message for common errors.`

4. Optionally, select a base branch for Copilot's changes. Copilot will create a new branch based on this branch.

5. Optionally, you can click <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-copilot" aria-label="copilot" role="img"><path d="M7.998 15.035c-4.562 0-7.873-2.914-7.998-3.749V9.338c.085-.628.677-1.686 1.588-2.065.013-.07.024-.143.036-.218.029-.183.06-.384.126-.612-.201-.508-.254-1.084-.254-1.656 0-.87.128-1.769.693-2.484.579-.733 1.494-1.124 2.724-1.261 1.206-.134 2.262.034 2.944.765.05.053.096.108.139.165.044-.057.094-.112.143-.165.682-.731 1.738-.899 2.944-.765 1.23.137 2.145.528 2.724 1.261.566.715.693 1.614.693 2.484 0 .572-.053 1.148-.254 1.656.066.228.098.429.126.612.012.076.024.148.037.218.924.385 1.522 1.471 1.591 2.095v1.872c0 .766-3.351 3.795-8.002 3.795Zm0-1.485c2.28 0 4.584-1.11 5.002-1.433V7.862l-.023-.116c-.49.21-1.075.291-1.727.291-1.146 0-2.059-.327-2.71-.991A3.222 3.222 0 0 1 8 6.303a3.24 3.24 0 0 1-.544.743c-.65.664-1.563.991-2.71.991-.652 0-1.236-.081-1.727-.291l-.023.116v4.255c.419.323 2.722 1.433 5.002 1.433ZM6.762 2.83c-.193-.206-.637-.413-1.682-.297-1.019.113-1.479.404-1.713.7-.247.312-.369.789-.369 1.554 0 .793.129 1.171.308 1.371.162.181.519.379 1.442.379.853 0 1.339-.235 1.638-.54.315-.322.527-.827.617-1.553.117-.935-.037-1.395-.241-1.614Zm4.155-.297c-1.044-.116-1.488.091-1.681.297-.204.219-.359.679-.242 1.614.091.726.303 1.231.618 1.553.299.305.784.54 1.638.54.922 0 1.28-.198 1.442-.379.179-.2.308-.578.308-1.371 0-.765-.123-1.242-.37-1.554-.233-.296-.693-.587-1.713-.7Z"></path><path d="M6.25 9.037a.75.75 0 0 1 .75.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 .75-.75Zm4.25.75v1.501a.75.75 0 0 1-1.5 0V9.787a.75.75 0 0 1 1.5 0Z"></path></svg> to open the agent dropdown menu, if you want to assign an agent or a custom agent with specialized behavior and tools. You can select an existing custom agent from your repository, organization, or enterprise. You can also click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Plus button" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg> Create an agent** to create a new agent profile in your selected repository and branch. For more information, see [Creating custom agents for Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/create-custom-agents>).
   > \[!NOTE] Third-party coding agents are available for all paid [Copilot plans](<https://docs.github.com/en/copilot/get-started/plans>).

6. Optionally, you can use the dropdown menu to select the model that Copilot will use. If the selected model supports configurable reasoning, you can also use the dropdown menu to select the reasoning level. For more information, see [Changing the AI model for GitHub Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model>).

7. Click **<svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-paper-airplane" aria-label="Start task" role="img"><path d="M.989 8 .064 2.68a1.342 1.342 0 0 1 1.85-1.462l13.402 5.744a1.13 1.13 0 0 1 0 2.076L1.913 14.782a1.343 1.343 0 0 1-1.85-1.463L.99 8Zm.603-5.288L2.38 7.25h4.87a.75.75 0 0 1 0 1.5H2.38l-.788 4.538L13.929 8Z"></path></svg>** or press <kbd>Enter</kbd>.

   Copilot will start a new session, which will appear in the list below the prompt box. Copilot will work on the task and push any code changes.

   You can track Copilot's work and open a pull request in one click from the session logs. For more information, see [Managing agent sessions](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>).

The same prompt box is available on your [dashboard](<https://github.com>) and in Copilot Chat (type `/task`).

For the full workflow of researching, planning, and iterating before creating a pull request, see [Research, plan, and iterate on code changes with Copilot cloud agent](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/research-plan-iterate>).

## Seed a new repository

When you create a new repository, you can have Copilot generate starter code.

1. In the upper-right corner of any page, select <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-plus" aria-label="Create something new" role="img"><path d="M7.75 2a.75.75 0 0 1 .75.75V7h4.25a.75.75 0 0 1 0 1.5H8.5v4.25a.75.75 0 0 1-1.5 0V8.5H2.75a.75.75 0 0 1 0-1.5H7V2.75A.75.75 0 0 1 7.75 2Z"></path></svg>, then click **New repository**.

   ![Screenshot of a GitHub dropdown menu showing options to create new items. The menu item "New repository" is outlined in dark orange.](<https://docs.github.com/assets/images/help/repository/repo-create-global-nav-update.png>)
2. In the **Prompt** field, describe what you want Copilot to build — for example, `Create a Rust CLI for converting CSV spreadsheets to Markdown`.
3. Click **Create repository**.

Copilot opens a draft pull request with the scaffolded code.

## Further reading

* [Best practices for using GitHub Copilot to work on tasks](<https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results>)
* [Managing agent sessions](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents>)


</details>
