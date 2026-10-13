# 使用 Copilot 代码审查

介绍请求审查、自动审查、重新审查、投入级别和建议修改的处理方式，说明项目指令与扩展工具如何影响审查。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 文档时间：官方文件提交（截至抓取）：[2026-10-01](<https://github.com/github/docs/commit/8a740f91003acd5929c05995a0ee4e44a36af9fd>)；发表/更新：官方未公开；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-code-review.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/使用 Copilot 代码审查.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 GitHub Copilot 代码审查

了解如何从 GitHub Copilot 请求代码审查。

## 简介

GitHub Copilot 可以审查您的代码并提供反馈。在可能的情况下，Copilot 的反馈包括建议的更改，您只需单击几下即可应用这些更改。

有关 GitHub Copilot 代码审查的完整介绍，请参阅 [关于 GitHub Copilot 代码审查 ](<https://docs.github.com/en/copilot/concepts/agents/code-review>)。

Copilot 代码审查使用 GitHub 操作来运行代理功能。有关更多信息，请参阅 [Copilot 代码审查的代理功能 ](<https://docs.github.com/en/copilot/concepts/agents/code-review#usage-of-github-actions-runners-for-agentic-capabilities-in-code-review>)。

<div class="ghd-tool webui">

Copilot 没有资格的组织成员也可以进行代码审查 Copilot 许可证，由企业管理员或组织所有者启用时。参见 [Copilot 没有组织成员的代码审查 Copilot 许可证](<https://docs.github.com/en/copilot/concepts/agents/code-review#copilot-code-review-without-a-copilot-license>).

## 使用 Copilot 代码审查

这些说明解释了如何在 GitHub 网站中使用 Copilot 代码审查。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

1. 在 GitHub.com 上，创建拉取请求或导航到现有拉取请求。

2. 在右侧边栏的“审阅者”下，单击 **Copilot** 旁边的 ** 请求 **。

![Copilot 的屏幕截图，其中“审阅者”下带有“请求”按钮。](<https://docs.github.com/assets/images/help/copilot/code-review/request-review@2x.png>)

3. 等待 Copilot 审核您的拉取请求。这通常需要不到 30 秒。

4. 向下滚动并阅读 Copilot 的评论。

![Copilot 留下的代码审查的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/code-review/review-comment@2x.png>)

Copilot 将每个评论的严重性级别标记为“高”、“中”或“低”，以帮助您根据问题的重要性对发现的问题进行优先级排序。

默认情况下，Copilot 留下“评论”审核，而不是“批准”审核或“请求更改”审核。这意味着，默认情况下，Copilot 的评论不计入拉取请求所需的批准。但是，如果配置为这样做，Copilot 可以留下“批准”评论。有关更多信息，请参阅 [从 Copilot](<#从-copilot-获取请求批准>) 获取请求批准。

5. 在可能的情况下，Copilot 的反馈包括建议的更改，您只需单击几下即可应用这些更改。

如果您对更改感到满意，您可以接受来自 Copilot 的单个建议并提交它，或者在一次提交中一起接受一组建议。有关更多信息，请参阅 [在拉取请求中纳入反馈 ](<https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/incorporating-feedback-in-your-pull-request>)。

您还可以调用Copilot云agent来实施建议的更改。为此，您必须：

   * 启用GitHub Copilot代码审查和Copilot云agent。
   * 根据 GitHub Copilot 代码审查的审查意见，单击 ** 使用 Copilot** 修复。这会创建关于拉取请求的草稿评论，您可以在其中指示 Copilot 解决特定反馈。然后，您可以选择 Copilot 是针对您的分支创建新的拉取请求，还是提交应用了建议的同一拉取请求。

6. Copilot 的评论评论的行为就像来自人类的评论评论。您可以向它们添加反应、评论它们、解决它们并隐藏它们。

您添加到 Copilot 的评论评论中的任何评论将对人类可见，但对 Copilot 不可见，并且 Copilot 不会回复。

您还可以通过 GitHub REST API 请求 `copilot-pull-request-reviewer[bot]` 作为审阅者，向 Copilot 请求审阅。有关更多信息，请参阅审核请求的 [REST API 端点 ](<https://docs.github.com/en/rest/pulls/review-requests#request-reviewers-for-a-pull-request>)。

## 启用自动评论

默认情况下，您手动请求 Copilot 对每个拉取请求进行审核，就像您请求人工审核一样。但是，您可以设置 Copilot 以自动审查所有拉取请求。请参阅[配置代码审查GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>)。

## 请求 Copilot 重新审核

当您将更改推送到 Copilot 已审核的拉取请求时，它不会自动重新审核您的更改，除非您将其配置为在启用自动审核后审核新推送。

要手动请求 Copilot 重新审阅，请单击 ** 审阅者 ** 菜单中 Copilot 姓名旁边的 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-sync" aria-label="Re-request review" role="img"><path d="M1.705 8.005a.75.75 0 0 1 .834.656 5.5 5.5 0 0 0 9.592 2.97l-1.204-1.204a.25.25 0 0 1 .177-.427h3.646a.25.25 0 0 1 .25.25v3.646a.25.25 0 0 1-.427.177l-1.38-1.38A7.002 7.002 0 0 1 1.05 8.84a.75.75 0 0 1 .656-.834ZM8 2.5a5.487 5.487 0 0 0-4.131 1.869l1.204 1.204A.25.25 0 0 1 4.896 6H1.25A.25.25 0 0 1 1 5.75V2.104a.25.25 0 0 1 .427-.177l1.38 1.38A7.002 7.002 0 0 1 14.95 7.16a.75.75 0 0 1-1.49.178A5.5 5.5 0 0 0 8 2.5Z"></path></svg> 按钮。有关更多信息，请参阅[请求拉取请求审核](<https://docs.github.com/en/pull-requests/how-tos/create-pull-requests/requesting-a-pull-request-review>)。

要在每次推送时自动请求 Copilot 重新审核，请为存储库启用自动代码审核，并在规则集设置中选择 **审核新推送 **。有关更多信息，请参阅[通过GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#configuring-automatic-code-review-for-repositories-in-an-organization>)配置代码审查。

> \[!NOTE] 重新审查拉取请求时，Copilot 可能会再次重复相同的评论，即使它们已被“解决对话”按钮驳回或被大拇指朝下 (:-1:) 按钮否决。

## 选择审核工作级别

通过审核工作级别，您可以选择与您的代码的关键性相匹配的 Copilot 审核的彻底性级别。

* **Lite**：经济高效的审核，针对错误、安全漏洞和风格不一致等明显问题提供有针对性的反馈。
* **平衡**：使用更高的推理模型，对复杂逻辑、安全敏感代码和跨服务变更进行更深入的分析。

在向 ** 审阅者 ** 部分（其中 Copilot 显示为审阅者）下的 Copilot 请求审阅之前，您可以在拉取请求中选择 Copilot 审阅工作。您还可以在设置中设置默认的 Copilot 审核工作。组织所有者和存储库管理员可以设置默认的 Copilot 审核工作以进行自动审核。

有关更多信息，请参阅[关于GitHub Copilot代码审查](<https://docs.github.com/en/copilot/concepts/agents/code-review#review-effort-level>)。

## 从 Copilot 获取请求批准

> \[！NOTE]
> Copilot 批准处于公开预览阶段，可能会发生变化。

每个 Copilot 代码审查都在概述注释中包含批准评估，指示 Copilot 是否认为拉取请求已准备好批准。就其本身而言，此评估不计入合并要求。

启用批准后，Copilot 可以提交满足您的存储库所需批准规则的批准审核，就像团队成员的批准一样。如果在 Copilot 批准后推送新提交，则批准将被驳回，您可以重新请求审核。有关所需审核的更多详细信息，请参阅[批准具有所需审核的拉取请求](<https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/approving-a-pull-request-with-required-reviews>)。

批准默认处于关闭状态，可以在企业、组织和存储库级别进行配置。存储库管理员还可以使用文件路径来控制哪些 Copilot 批准计入合并要求。配置步骤请参见[配置代码审查GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review>)。

## 使用自定义说明自定义 Copilot 的评论

您可以通过向存储库添加自定义指令来自定义 Copilot 代码审查。存储库自定义指令可以是存储库范围内的，也可以是特定于路径的。

使用 `.github/copilot-instructions.md` 获取应适用于整个代码库的存储库范围审查指南。这是描述组织范围期望的好地方，例如编码标准、审查标准或 Copilot 在每次审查中应考虑的一般实践。

使用存储库根目录中的 `AGENTS.md` 文件提供额外的存储库上下文，帮助 Copilot 更好地了解您的项目如何工作。例如，您可以解释哪些模式是有意为之的，代码库的哪些部分需要更仔细的审查，以及您的团队认为良好的架构、测试和实现实践。这有助于使评论更加相关并与您的团队构建软件的方式保持一致。

使用 `.github/instructions/**/*.instructions.md` 文件获取仅在查看匹配文件时适用的路径特定说明。当存储库的不同部分遵循不同的约定、需要专门的检查或需要针对特定​​语言、框架或子系统定制的审查指南时，这非常有用。

Copilot 代码审查还会从存储库中的 `CLAUDE.md`、`GEMINI.md` 和 `REVIEW.md` 文件中读取自定义指令（如果存在）。

有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

> \[!NOTE]
> 审查拉取请求时，Copilot 从头分支（包含更改的分支）而不是基础分支读取存储库自定义指令、agent 指令和 agent skills。例如，当将 `my-feature-branch` 合并到 `main` 时，Copilot 使用 `my-feature-branch` 中的指令和技能，因此您可以在同一个 Pull 请求中测试对它们的更改，而无需先合并它们。

### 示例

此 `.github/copilot-instructions.md` 文件示例包含三个指令，这些指令将应用于存储库中的所有 Copilot 代码审查。

```text
When performing a code review, respond in Spanish.

When performing a code review, apply the checks in the `/security/security-checklist.md` file.

When performing a code review, focus on readability and avoid nested ternary operators.
```

## MCP 服务器和 agent skills

当Copilot代码审查与正在审查的代码相关时，可以使用存储库中配置的agent skills和MCP服务器。

要使这些可用于 GitHub 上的 Copilot 代码审查，请配置：

* **Agent skills** 在您的存储库中（在 `.github/skills` 中）。如果您希望技能以审阅任务为目标，请使用以审阅为中心的技能目录名称，例如 `code-review`。有关设置详细信息，请参阅[为GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>)添加agent skills。
* **MCP 存储库 Copilot 设置中的服务器 **。默认情况下启用 GitHub MCP 服务器和 Playwright MCP 服务器。有关设置详细信息，请参阅[为您的存储库配置MCP服务器](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>)。

Copilot 代码审查更有可能在以下情况下使用此上下文：

* Agent skills 目录具有以审查为中心的名称和描述，例如 `code-review`，表明它们用于拉取请求审查。
* 您的 agent skills 或自定义指令明确告诉 Copilot 代码审查使用特定的 MCP 上下文。
* 拉取请求描述可通过配置的 MCP 服务器获取，例如问题密钥或事件 ID。

要验证特定审核使用哪个 MCP 上下文 Copilot 代码审核，请检查每个审核评论的底部是否有属性。这些属性引用了 Copilot 代码审查用来生成该评论的特定 agent 技能或 MCP 服务器。您还可以从拉取请求时间线打开链接的审阅会话，然后检查会话日志以查看调用了哪些 MCP 服务器和工具。

在存储库设置中，默认情况下启用**允许Copilot在审查拉取请求时使用MCP工具**。如果您希望 MCP 服务器仅可用于 Copilot 云 agent，而不可用于 Copilot 代码审查，请禁用此设置。有关分步说明，请参阅 [为存储库配置 MCP 服务器 ](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#disabling-mcp-tools-for-code-review>)。

## 自定义 Copilot 代码审查环境

Copilot 代码审查在临时开发环境中运行，类似于 Copilot 云 agent。您可以使用 GitHub Actions 工作流程文件自定义此环境，与 Copilot 云 agent 的方式相同，例如，预安装工具或依赖项，或切换到不同的操作系统。有关如何构造此文件的更多信息，请参阅[配置开发环境](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)。

您可以使用以下任一文件来配置 Copilot 代码审查的环境：

* `.github/workflows/copilot-setup-steps.yml`：如果您已经为Copilot云agent配置了此文件，Copilot代码审核将默认使用相同的配置。
* `.github/workflows/copilot-code-review.yml`：如果您想要独立于 Copilot 云 agent 配置 Copilot 代码审查环境，我们建议您创建此文件。如果您的存储库中存在此文件，则它将用于 Copilot 代码审查，而不是 `copilot-setup-steps.yml`。

您还可以配置防火墙规则，以在组织和存储库级别控制 Copilot 代码审查可以访问的域和 URL。这些设置与 Copilot 云 agent 的防火墙设置分开配置，位于同一“Internet 访问”选项卡的自己的部分中。请参阅[自定义或禁用GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>)的防火墙。

## 提供对 Copilot 评论的反馈

您可以直接在每条评论中提供对 Copilot 评论的反馈。我们使用此信息来改进产品和 Copilot 建议的质量。

要对 Copilot 的审阅评论提供反馈，请单击“竖起大拇指”(:+1:) 或“竖起大拇指”(:-1:) 按钮。

![显示 Copilot 代码审查评论的屏幕截图，其中带有“向上”和“向下”按钮。](<https://docs.github.com/assets/images/help/copilot/code-review/feedback-controls@2x.png>)

</div>

<div class="ghd-tool vscode">

### 检查选定的代码

您可以请求对 Visual Studio Code 中突出显示的代码选择进行初步审查。

1. 在Visual Studio Code中，选择您要查看的代码。
2. 右键单击所选代码，然后选择**生成代码** > **查看**。
3. VS Code 在 ** 评论 ** 面板中创建审阅评论，并在编辑器中内嵌显示它们。

### 审查所有未提交的更改

如果您有未提交的更改，可以在Visual Studio Code中请求审核。

1. 在VS Code中，单击活动栏中的**源代码控制**按钮。

2. 在 ** 源代码控制 ** 视图的顶部，将鼠标悬停在 **CHANGES** 上，然后单击 <!-- Currently (Feb 2025) used here: https://docs.github.com/en/copilot/using-github-copilot/code-review/using-copilot-code-review?tool=vscode#reviewing-changes --> <svg class="octicon" width="16" height="16" xmlns="http://www.w3.org/2000/svg" fill="currentColor"><path d="M14.5 2H1.5L1 2.5V11.5L1.5 12H4V14.5L4.854 14.854L7.707 12H14.5L15 11.5V2.5L14.5 2ZM14 11H7.5L7.146 11.146L5 13.293V11.5L4.5 11H2V3H14V11Z"/><path d="M7.079 5.205L5.262 7.033L7.078 8.853L6.371 9.56L4.2 7.386V6.679L6.37 4.5L7.079 5.205ZM9.7 4.505L9 5.214L10.826 7.033L8.995 8.853L9.7 9.562L11.889 7.389V6.68L9.7 4.505Z"/></svg> **Copilot 代码审查 - 未提交的更改 ** 按钮。

![“源代码管理”视图的屏幕截图。代码审查按钮以深橙色轮廓显示。](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-review-button.png>)

3. 等待 Copilot 查看您的更改。这通常需要不到 30 秒。

4. 如果 Copilot 有任何注释，它们将内联显示在您的文件中以及 ** 问题 ** 选项卡中。

## 使用 Copilot 提供的建议更改

在可能的情况下，Copilot 的反馈包括建议的更改，您只需单击一下即可应用这些更改。

![Visual Studio Code 中 Copilot 的评论的屏幕截图，其中包含建议的更改。](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-comment@2x.png>)

如果您对更改感到满意，可以通过单击 ** 应用和 Go 到下一步 ** 按钮接受 Copilot 的建议。您应用的任何更改都不会自动提交。

如果您不想应用Copilot的建议更改，请单击**放弃和Go到下一步**按钮。

## 提供对 Copilot 评论的反馈

您可以直接在每条评论中提供对 Copilot 评论的反馈。我们使用此信息来改进产品和 Copilot 建议的质量。

要提供反馈，请将鼠标悬停在评论上，然后单击“赞成”或“反对”按钮。

![Visual Studio Code 中来自 Copilot 的评论的屏幕截图，其中显示了反馈按钮。按钮的轮廓为深橙色。](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-comment-feedback@2x.png>)

## 使用自定义说明自定义 Copilot 的评论

您可以通过向存储库添加自定义指令来自定义 Copilot 代码审查。存储库自定义指令可以是存储库范围内的，也可以是特定于路径的。

使用 `.github/copilot-instructions.md` 获取应适用于整个代码库的存储库范围审查指南。这是描述组织范围期望的好地方，例如编码标准、审查标准或 Copilot 在每次审查中应考虑的一般实践。

使用存储库根目录中的 `AGENTS.md` 文件提供额外的存储库上下文，帮助 Copilot 更好地了解您的项目如何工作。例如，您可以解释哪些模式是有意为之的，代码库的哪些部分需要更仔细的审查，以及您的团队认为良好的架构、测试和实现实践。这有助于使评论更加相关并与您的团队构建软件的方式保持一致。

使用 `.github/instructions/**/*.instructions.md` 文件获取仅在查看匹配文件时适用的路径特定说明。当存储库的不同部分遵循不同的约定、需要专门的检查或需要针对特定​​语言、框架或子系统定制的审查指南时，这非常有用。

Copilot 代码审查还会从存储库中的 `CLAUDE.md`、`GEMINI.md` 和 `REVIEW.md` 文件中读取自定义指令（如果存在）。

有关更多信息，请参阅 [为 GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>) 添加存储库自定义指令。

> \[!NOTE]
> 审查拉取请求时，Copilot 从头分支（包含更改的分支）而不是基础分支读取存储库自定义指令、agent 指令和 agent skills。例如，当将 `my-feature-branch` 合并到 `main` 时，Copilot 使用 `my-feature-branch` 中的指令和技能，因此您可以在同一个 Pull 请求中测试对它们的更改，而无需先合并它们。

### 示例

此 `.github/copilot-instructions.md` 文件示例包含三个指令，这些指令将应用于存储库中的所有 Copilot 代码审查。

```text
When performing a code review, respond in Spanish.

When performing a code review, apply the checks in the `/security/security-checklist.md` file.

When performing a code review, focus on readability and avoid nested ternary operators.
```

</div>

<div class="ghd-tool visualstudio">

## 先决条件

要使用 Copilot 代码审查，您必须使用 Visual Studio 版本 17.14 或更高版本。请参阅 [Visual Studio 下载页面 ](<https://visualstudio.microsoft.com/downloads/>)。

## 使用 Copilot 代码审查

这些说明解释了如何在 Visual Studio 中使用 Copilot 代码审查。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

1. 在 Git 更改窗口中，单击 ** 使用 Copilot** 查看更改。
此按钮显示为带有闪光的评论图标。

2. Copilot 将开始审核您的更改。片刻之后，Git Changes 窗口中会出现一个显示代码审查评论数量的链接。

3. 单击链接可查看和导航评论。如果没有发现问题，您将看到以下消息：
Copilot没有对任何文件发表评论。

4. Copilot 显示代码中的注释以及每个潜在问题的摘要。您可以：

   * 根据建议进行审查并进行更改。
   * 使用评论框右上角的向下箭头取消评论。

5. 要删除所有审阅注释，请单击“Git 更改”窗口中代码审阅链接旁边的 <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-x" aria-label="The X icon" role="img"><path d="M3.72 3.72a.75.75 0 0 1 1.06 0L8 6.94l3.22-3.22a.749.749 0 0 1 1.275.326.749.749 0 0 1-.215.734L9.06 8l3.22 3.22a.749.749 0 0 1-.326 1.275.749.749 0 0 1-.734-.215L8 9.06l-3.22 3.22a.751.751 0 0 1-1.042-.018.751.751 0 0 1-.018-1.042L6.94 8 3.72 4.78a.75.75 0 0 1 0-1.06Z"></path></svg>。

有关在 Visual Studio 中启用和配置 Copilot 代码审查的详细信息，请参阅 Visual Studio 文档中的 [使用 Copilot 聊天 ](<https://learn.microsoft.com/en-us/visualstudio/version-control/git-make-commit?view=vs-2022#review-local-changes-with-copilot-chat>) 审查本地更改。

</div>

<div class="ghd-tool mobile">

## 使用 Copilot 代码审查

这些说明解释了如何在 GitHub Mobile 中使用 Copilot 代码审查。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

1. 在 GitHub 移动设备中，打开拉取请求。
2. 向下滚动到 ** 评论 ** 部分并将其展开。
3. 单击**请求评论**。
4. 添加 Copilot 作为审阅者，然后单击 ** 完成 **。

Copilot 将审核更改并提供反馈。

</div>

<div class="ghd-tool xcode">

## 先决条件

要在 Xcode 中使用 Copilot 代码审查，您必须使用 0.41.0 或更高版本的 GitHub Copilot 聊天扩展。从 [`github/CopilotForXcode` 存储库 ](<https://github.com/github/CopilotForXcode/releases/latest>) 下载最新版本。

## 使用 Copilot 代码审查

这些说明解释了如何在 Xcode 中使用 Copilot 代码审查。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

1. 在 Xcode 中，对一个或多个文件进行一些更改。

2. 单击菜单栏中的**编辑器**，打开Copilot聊天窗口，单击**GitHub Copilot**，然后单击**打开聊天**。

3. 在 Copilot 聊天窗口的提示框右下角附近，单击 **Code Review** 按钮（对话气泡图标）。

![Xcode 中 Copilot 聊天窗口的屏幕截图，其中“代码审查”按钮以深橙色标出。](<https://docs.github.com/assets/images/help/copilot/code-review/xcode-ccr-button.png>)

4. 单击**查看暂存更改**或**查看未暂存更改**。

5. 包含更改的文件列表显示在聊天窗口中。单击复选框取消选择您不希望 Copilot 查看的任何文件。

6. 点击**Continue**开始审核流程。

7. 如果Copilot找到要评论的内容，它会在聊天窗口中显示**已审阅的更改**列表，列出它已评论的文件。单击此列表中的文件即可查看注释。

每条评论都显示在弹出窗口中，覆盖在编辑器上。

![Copilot代码审查评论的屏幕截图。](<https://docs.github.com/assets/images/help/copilot/code-review/xcode-review-popup.png>)

8. 如果文件中有多个注释，请使用弹出窗口右上角的向上和向下箭头在注释之间导航。

9. Copilot 可能会建议替换代码。您可以通过单击**接受**应用建议的更改，或通过单击**拒绝**拒绝建议的更改。

10. 单击聊天窗口中的**已审阅的更改**列表中的另一个文件，可以查看另一个文件的审阅评论。

</div>

<div class="ghd-tool jetbrains">

## 先决条件

* **访问Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。

* **兼容JetBrainsIDE**。要在JetBrains中使用GitHub Copilot，您必须安装兼容的JetBrains IDE。 GitHub Copilot 与以下 IDE 兼容：

  * IntelliJ IDEA（终极版、社区版、教育版）
  * 安卓工作室
  * 克利翁
  * 与我一起编码嘉宾
  * DataGrip
  * DataSpell
  * GoLand
  * JetBrains 客户端
  * MPS
  * PhpStorm
  * PyCharm（专业、社区、教育）
  * 骑手
  * RubyMine
  * RustRover
  * WebStorm

请参阅 [JetBrains IDE](<https://www.jetbrains.com/products/?ref_product=copilot\&ref_type=engagement\&ref_style=button>) 工具查找器进行下载。

* **GitHub Copilot扩展**的最新版本。请参阅 JetBrains 市场中的 [GitHub Copilot 插件 ](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot\&ref_type=engagement\&ref_style=text>)。有关安装说明，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>)。

* **在您的JetBrainsIDE**中登录GitHub。有关身份验证说明，请参阅 [在您的环境中安装 GitHub Copilot 扩展 ](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains#installing-the-github-copilot-plugin-in-your-jetbrains-ide>)。

## 使用 Copilot 代码审查

这些说明解释了如何在 JetBrains IDE 中使用 Copilot 代码审查。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

1. 在 JetBrains IDE 中，对一个或多个文件进行一些更改。
2. 打开左侧的“提交”工具窗口。
3. 在提交消息输入字段上方，单击 **Copilot：查看代码更改**。此按钮显示为带有闪光的放大镜图标。
4. Copilot 将开始审核您的更改。
5. Copilot 显示代码中的注释以及每个潜在问题的摘要。您可以：

   * 根据建议进行审查并进行更改。
   * 单击 ** 放弃 ** 驳回评论。
6. 如果有多个评论，请使用弹出窗口右上角的向上和向下箭头在评论之间导航。

</div>

<div class="ghd-tool cli">

## 先决条件

* **访问Copilot**。请参阅[关于GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>)。
* **GitHub CLI**。您必须安装并验证GitHub CLI。请参阅 [GitHub CLI 快速入门 ](<https://docs.github.com/en/github-cli/github-cli/quickstart>)。

## 使用 Copilot 代码审查

这些说明解释了如何将 Copilot 代码审查与 GitHub CLI 结合使用。要查看其他流行编码环境的说明，请单击页面顶部的相应选项卡。

### 创建拉取请求时请求审核

使用 `gh pr create` 创建新的拉取请求时，您可以请求 Copilot 进行审核：

```shell copy
gh pr create --reviewer @copilot
```

您还可以在 `gh pr create` 期间从可搜索审阅者提示中交互选择 Copilot。

```text
? Reviewers  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
  [ ]  Search (7472 more)
  [x]  monalisa (Mona Lisa)
> [x]  Copilot (AI)
```

### 请求对现有拉取请求进行审查

要请求 Copilot 对现有拉取请求进行审核，请使用 `gh pr edit`。如果您不在拉取请求的分支上，请指定拉取请求编号：

```shell copy
gh pr edit PR-NUMBER --add-reviewer @copilot
```

将 `PR-NUMBER` 替换为您要审核的拉取请求的编号。如果您已签出拉取请求的分支，则可以省略该编号。

</div>


## 原文对照

<details>
<summary>展开完整原文</summary>

# Using GitHub Copilot code review

Learn how to request a code review from GitHub Copilot.

## Introduction

GitHub Copilot can review your code and provide feedback. Where possible, Copilot's feedback includes suggested changes which you can apply with a couple of clicks.

For a full introduction to GitHub Copilot code review, see [About GitHub Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review>).

Copilot code review uses GitHub Actions to run agentic capabilities. For more information, see [Agentic capabilities for Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review#usage-of-github-actions-runners-for-agentic-capabilities-in-code-review>).

<div class="ghd-tool webui">

Copilot code review is also available for organization members without a Copilot license, when enabled by an enterprise administrator or organization owner. See [Copilot code review for organization members without a Copilot license](<https://docs.github.com/en/copilot/concepts/agents/code-review#copilot-code-review-without-a-copilot-license>).

## Using Copilot code review

These instructions explain how to use Copilot code review in the GitHub website. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

1. On GitHub.com, create a pull request or navigate to an existing pull request.

2. Under "Reviewers" in the right sidebar, next to **Copilot**, click **Request**.

   ![Screenshot of Copilot with 'Request' button under 'Reviewers'.](<https://docs.github.com/assets/images/help/copilot/code-review/request-review@2x.png>)

3. Wait for Copilot to review your pull request. This usually takes less than 30 seconds.

4. Scroll down and read through Copilot's comments.

   ![Screenshot of a code review left by Copilot.](<https://docs.github.com/assets/images/help/copilot/code-review/review-comment@2x.png>)

   Copilot labels each comment with a severity level of "High," "Medium," or "Low" to help you prioritize the issues it finds based on their importance.

   By default, Copilot leaves a "Comment" review, not an "Approve" review or a "Request changes" review. This means that, by default, Copilot's reviews do not count toward required approvals for the pull request. However, if configured to do so, Copilot can leave "Approve" reviews. For more information, see [Pull request approvals from Copilot](<#pull-request-approvals-from-copilot>).

5. Where possible, Copilot's feedback includes suggested changes which you can apply with a couple of clicks.

   If you're happy with the changes, you can accept a single suggestion from Copilot and commit it, or accept a group of suggestions together in a single commit. For more information, see [Incorporating feedback in your pull request](<https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/incorporating-feedback-in-your-pull-request>).

   You can also invoke Copilot cloud agent to implement suggested changes. To do this, you must:

   * Enable GitHub Copilot code review and Copilot cloud agent.
   * On review comments from GitHub Copilot code review, click **Fix with Copilot**. This creates a draft comment on the pull request, where you can instruct Copilot to address specific feedback. You can then select whether Copilot will create a new pull request against your branch or a commit to the same pull request with the suggestions applied.

6. Copilot's review comments behave like review comments from humans. You can add reactions to them, comment on them, resolve them and hide them.

   Any comments you add to Copilot's review comments will be visible to humans, but they won't be visible to Copilot, and Copilot won't reply.

You can also request a review from Copilot through the GitHub REST API by requesting `copilot-pull-request-reviewer[bot]` as a reviewer. For more information, see [REST API endpoints for review requests](<https://docs.github.com/en/rest/pulls/review-requests#request-reviewers-for-a-pull-request>).

## Enabling automatic reviews

By default, you manually request a review from Copilot on each pull request, in the same way you would request a review from a human. However, you can set up Copilot to automatically review all pull requests. See [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review>).

## Requesting a re-review from Copilot

When you push changes to a pull request that Copilot has reviewed, it won't automatically re-review your changes unless you've configured it to review new pushes after enabling automatic reviews.

To manually request a re-review from Copilot, click the <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-sync" aria-label="Re-request review" role="img"><path d="M1.705 8.005a.75.75 0 0 1 .834.656 5.5 5.5 0 0 0 9.592 2.97l-1.204-1.204a.25.25 0 0 1 .177-.427h3.646a.25.25 0 0 1 .25.25v3.646a.25.25 0 0 1-.427.177l-1.38-1.38A7.002 7.002 0 0 1 1.05 8.84a.75.75 0 0 1 .656-.834ZM8 2.5a5.487 5.487 0 0 0-4.131 1.869l1.204 1.204A.25.25 0 0 1 4.896 6H1.25A.25.25 0 0 1 1 5.75V2.104a.25.25 0 0 1 .427-.177l1.38 1.38A7.002 7.002 0 0 1 14.95 7.16a.75.75 0 0 1-1.49.178A5.5 5.5 0 0 0 8 2.5Z"></path></svg> button next to Copilot's name in the **Reviewers** menu. For more information, see [Requesting a pull request review](<https://docs.github.com/en/pull-requests/how-tos/create-pull-requests/requesting-a-pull-request-review>).

To automatically request re-reviews from Copilot on every push, enable automatic code review for the repository and select **Review new pushes** in the ruleset settings. For more information, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#configuring-automatic-code-review-for-repositories-in-an-organization>).

> \[!NOTE] When re-reviewing a pull request, Copilot may repeat the same comments again, even if they have been dismissed with the "Resolve conversation" button or downvoted with the thumbs down (:-1:) button.

## Choosing a review effort level

With review effort levels, you can choose the level of thoroughness of Copilot's review that matches the criticality of your code.

* **Lite**: Cost-efficient review that provides targeted feedback on glaring issues such as bugs, security vulnerabilities, and style inconsistencies.
* **Balanced**: Deeper analysis of complex logic, security-sensitive code, and cross-service changes, using a higher-reasoning model.

You can select Copilot review effort in the pull request before you request a review from Copilot, under the **Reviewers** section where Copilot appears as a reviewer. You can also set a default Copilot review effort in your settings. Organization owners and repository administrators can set a default Copilot review effort for automatic reviews.

For more information, see [About GitHub Copilot code review](<https://docs.github.com/en/copilot/concepts/agents/code-review#review-effort-level>).

## Pull request approvals from Copilot

> \[!NOTE]
> Copilot approvals are in public preview and subject to change.

Every Copilot code review includes an approval assessment in the overview comment, indicating whether Copilot considers the pull request ready to approve. On its own, this assessment does not count toward merge requirements.

When approvals are enabled, Copilot can submit an approving review that satisfies your repository's required-approval rule the same way a teammate's approval would. If new commits are pushed after Copilot approves, the approval is dismissed, and you can re-request a review. For more details on required reviews, see [Approving a pull request with required reviews](<https://docs.github.com/en/pull-requests/how-tos/review-pull-requests/approving-a-pull-request-with-required-reviews>).

Approvals are off by default and can be configured at the enterprise, organization, and repository levels. Repository administrators can also use file paths to control which Copilot approvals count toward merge requirements. For configuration steps, see [Configuring code review by GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/configure-code-review#customizing-copilot-code-review>).

## Customizing Copilot's reviews with custom instructions

You can customize Copilot code review by adding custom instructions to your repository. Repository custom instructions can either be repository wide or path specific.

Use `.github/copilot-instructions.md` for repository-wide review guidance that should apply across the entire codebase. This is a good place to describe organization-wide expectations, such as coding standards, review criteria, or general practices that Copilot should consider in every review.

Use an `AGENTS.md` file in the root of your repository to provide additional repository context that helps Copilot better understand how your project works. For example, you can explain which patterns are intentional, which parts of the codebase need closer scrutiny, and what your team considers good architecture, testing, and implementation practices. This helps make reviews more relevant and aligned with the way your team builds software.

Use `.github/instructions/**/*.instructions.md` files for path-specific instructions that only apply when reviewing matching files. This is useful when different parts of the repository follow different conventions, require specialized checks, or need review guidance tailored to a particular language, framework, or subsystem.

Copilot code review also reads custom instructions from `CLAUDE.md`, `GEMINI.md`, and `REVIEW.md` files in your repository, if they exist.

For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

> \[!NOTE]
> When reviewing a pull request, Copilot reads repository custom instructions, agent instructions, and agent skills from the head branch (the branch with your changes), not the base branch. For example, when merging `my-feature-branch` into `main`, Copilot uses the instructions and skills in `my-feature-branch`, so you can test changes to them in the same pull request without merging them first.

### Example

This example of a `.github/copilot-instructions.md` file contains three instructions that will be applied to all Copilot code reviews in the repository.

```text
When performing a code review, respond in Spanish.

When performing a code review, apply the checks in the `/security/security-checklist.md` file.

When performing a code review, focus on readability and avoid nested ternary operators.
```

## MCP servers and agent skills

Copilot code review can use agent skills and MCP servers configured in the repository, when they are relevant to the code being reviewed.

To make these available for Copilot code review on GitHub, configure:

* **Agent skills** in your repository (in `.github/skills`). If you want a skill to target review tasks, use a review-focused skill directory name such as `code-review`. For setup details, see [Adding agent skills for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/add-skills>).
* **MCP servers** in repository Copilot settings. The GitHub MCP server and Playwright MCP server are enabled by default. For setup details, see [Configure MCP servers for your repository](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers>).

Copilot code review is more likely to use this context when:

* Agent skills directories have review-focused names and descriptions, such as `code-review`, that indicate they are intended for pull request review.
* Your agent skills or custom instructions explicitly tell Copilot code review to use specific MCP context.
* Pull request descriptions reference items available through configured MCP servers, such as issue keys or incident IDs.

To verify which MCP context Copilot code review used for a specific review, check if there are attributions at the bottom of each review comment. These attributions reference the specific agent skill or MCP server that Copilot code review used to generate that comment. You can also open the linked review session from the pull request timeline, then check the session logs to see which MCP servers and tools were called.

In repository settings, **Allow Copilot to use MCP tools when reviewing pull requests** is enabled by default. Disable this setting if you want MCP servers available only for Copilot cloud agent, and not for Copilot code review. For step-by-step instructions, see [Configure MCP servers for your repository](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers#disabling-mcp-tools-for-code-review>).

## Customizing Copilot code review's environment

Copilot code review runs in an ephemeral development environment, similar to Copilot cloud agent. You can customize this environment using a GitHub Actions workflow file, in the same way you would for Copilot cloud agent, for example, to preinstall tools or dependencies, or to switch to a different operating system. For more information on how to structure this file, see [Configure the development environment](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>).

You can use either of the following files to configure Copilot code review's environment:

* `.github/workflows/copilot-setup-steps.yml`: If you have already configured this file for Copilot cloud agent, Copilot code review will use the same configuration by default.
* `.github/workflows/copilot-code-review.yml`: We recommend creating this file if you want to configure Copilot code review's environment independently of Copilot cloud agent. If this file is present in your repository, it is used for Copilot code review instead of `copilot-setup-steps.yml`.

You can also configure firewall rules to control the domains and URLs that Copilot code review can access, at both the organization and repository level. These settings are configured separately from Copilot cloud agent's firewall settings, in their own section of the same "Internet access" tab. See [Customizing or disabling the firewall for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-the-firewall>).

## Providing feedback on Copilot's reviews

You can provide feedback on Copilot's comments directly within each comment. We use this information to improve the product and the quality of Copilot's suggestions.

To provide feedback on a review comment from Copilot, click the thumbs up (:+1:) or thumbs down (:-1:) button.

![Screenshot showing a Copilot code review comment with the thumbs up and thumbs down buttons.](<https://docs.github.com/assets/images/help/copilot/code-review/feedback-controls@2x.png>)

</div>

<div class="ghd-tool vscode">

### Reviewing a selection of code

You can request an initial review of a highlighted selection of code in Visual Studio Code.

1. In Visual Studio Code, select the code you want to review.
2. Right-click the selected code and choose **Generate Code** > **Review**.
3. VS Code creates review comments in the **Comments** panel and also shows them inline in the editor.

### Reviewing all uncommitted changes

If you have uncommitted changes, you can request a review in Visual Studio Code.

1. In VS Code, click the **Source Control** button in the Activity Bar.

2. At the top of the **Source Control** view, hover over **CHANGES**, then click the <!-- Currently (Feb 2025) used here: https://docs.github.com/en/copilot/using-github-copilot/code-review/using-copilot-code-review?tool=vscode#reviewing-changes --> <svg class="octicon" width="16" height="16" xmlns="http://www.w3.org/2000/svg" fill="currentColor"><path d="M14.5 2H1.5L1 2.5V11.5L1.5 12H4V14.5L4.854 14.854L7.707 12H14.5L15 11.5V2.5L14.5 2ZM14 11H7.5L7.146 11.146L5 13.293V11.5L4.5 11H2V3H14V11Z"/><path d="M7.079 5.205L5.262 7.033L7.078 8.853L6.371 9.56L4.2 7.386V6.679L6.37 4.5L7.079 5.205ZM9.7 4.505L9 5.214L10.826 7.033L8.995 8.853L9.7 9.562L11.889 7.389V6.68L9.7 4.505Z"/></svg> **Copilot Code Review - Uncommitted Changes** button.

   ![Screenshot of the "Source Control" view. The code review button is outlined in dark orange.](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-review-button.png>)

3. Wait for Copilot to review your changes. This usually takes less than 30 seconds.

4. If Copilot has any comments, they will be shown inline in your file(s), and in the **Problems** tab.

## Working with suggested changes provided by Copilot

Where possible, Copilot's feedback includes suggested changes which you can apply with a single click.

![Screenshot of a comment from Copilot in Visual Studio Code with a suggested change.](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-comment@2x.png>)

If you're happy with the change, you can accept a suggestion from Copilot by clicking the **Apply and Go To Next** button. Any changes you apply will not be automatically committed.

If you don't want to apply Copilot's suggested change, click the **Discard and Go to Next** button.

## Providing feedback on Copilot's reviews

You can provide feedback on Copilot's comments directly within each comment. We use this information to improve the product and the quality of Copilot's suggestions.

To provide feedback, hover over the comment and click the thumbs up or thumbs down button.

![Screenshot of a comment from Copilot in Visual Studio Code with feedback buttons displayed. The buttons are outlined in dark orange.](<https://docs.github.com/assets/images/help/copilot/code-review/vscode-comment-feedback@2x.png>)

## Customizing Copilot's reviews with custom instructions

You can customize Copilot code review by adding custom instructions to your repository. Repository custom instructions can either be repository wide or path specific.

Use `.github/copilot-instructions.md` for repository-wide review guidance that should apply across the entire codebase. This is a good place to describe organization-wide expectations, such as coding standards, review criteria, or general practices that Copilot should consider in every review.

Use an `AGENTS.md` file in the root of your repository to provide additional repository context that helps Copilot better understand how your project works. For example, you can explain which patterns are intentional, which parts of the codebase need closer scrutiny, and what your team considers good architecture, testing, and implementation practices. This helps make reviews more relevant and aligned with the way your team builds software.

Use `.github/instructions/**/*.instructions.md` files for path-specific instructions that only apply when reviewing matching files. This is useful when different parts of the repository follow different conventions, require specialized checks, or need review guidance tailored to a particular language, framework, or subsystem.

Copilot code review also reads custom instructions from `CLAUDE.md`, `GEMINI.md`, and `REVIEW.md` files in your repository, if they exist.

For more information, see [Adding repository custom instructions for GitHub Copilot](<https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions>).

> \[!NOTE]
> When reviewing a pull request, Copilot reads repository custom instructions, agent instructions, and agent skills from the head branch (the branch with your changes), not the base branch. For example, when merging `my-feature-branch` into `main`, Copilot uses the instructions and skills in `my-feature-branch`, so you can test changes to them in the same pull request without merging them first.

### Example

This example of a `.github/copilot-instructions.md` file contains three instructions that will be applied to all Copilot code reviews in the repository.

```text
When performing a code review, respond in Spanish.

When performing a code review, apply the checks in the `/security/security-checklist.md` file.

When performing a code review, focus on readability and avoid nested ternary operators.
```

</div>

<div class="ghd-tool visualstudio">

## Prerequisite

To use Copilot code review, you must use Visual Studio version 17.14 or later. See the [Visual Studio downloads page](<https://visualstudio.microsoft.com/downloads/>).

## Using Copilot code review

These instructions explain how to use Copilot code review in Visual Studio. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

1. In the Git Changes window, click **Review changes with Copilot**.
   This button appears as a comment icon with a sparkle.

2. Copilot will begin reviewing your changes. After a few moments, a link showing the number of code review comments appears in the Git Changes window.

3. Click the link to view and navigate the comments. If no issues are found, you'll see the message:
   Copilot did not comment on any files.

4. Copilot displays comments in your code with a summary of each potential issue. You can:

   * Review and make changes based on the suggestions.
   * Dismiss a comment using the downward arrow in the top-right corner of the comment box.

5. To remove all review comments, click <svg version="1.1" width="16" height="16" viewBox="0 0 16 16" class="octicon octicon-x" aria-label="The X icon" role="img"><path d="M3.72 3.72a.75.75 0 0 1 1.06 0L8 6.94l3.22-3.22a.749.749 0 0 1 1.275.326.749.749 0 0 1-.215.734L9.06 8l3.22 3.22a.749.749 0 0 1-.326 1.275.749.749 0 0 1-.734-.215L8 9.06l-3.22 3.22a.751.751 0 0 1-1.042-.018.751.751 0 0 1-.018-1.042L6.94 8 3.72 4.78a.75.75 0 0 1 0-1.06Z"></path></svg> next to the code review link in the Git Changes window.

For more information on enabling and configuring Copilot code review in Visual Studio, see [Review local changes with Copilot Chat](<https://learn.microsoft.com/en-us/visualstudio/version-control/git-make-commit?view=vs-2022#review-local-changes-with-copilot-chat>) in the Visual Studio documentation.

</div>

<div class="ghd-tool mobile">

## Using Copilot code review

These instructions explain how to use Copilot code review in GitHub Mobile. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

1. In GitHub Mobile, open a pull request.
2. Scroll down to the **Reviews** section and expand it.
3. Click **Request Reviews**.
4. Add Copilot as a reviewer, then click **Done**.

Copilot will review the changes and provide feedback.

</div>

<div class="ghd-tool xcode">

## Prerequisite

To use Copilot code review in Xcode, you must use version 0.41.0 or later of the GitHub Copilot Chat extension. Download the latest release from the [`github/CopilotForXcode` repository](<https://github.com/github/CopilotForXcode/releases/latest>).

## Using Copilot code review

These instructions explain how to use Copilot code review in Xcode. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

1. In Xcode, make some changes to one or more files.

2. Open the Copilot chat window by clicking **Editor** in the menu bar, clicking **GitHub Copilot** then **Open Chat**.

3. Near the bottom right of the prompt box in the Copilot chat window, click the **Code Review** button (a speech bubble icon).

   ![Screenshot of the Copilot chat window in Xcode, with the 'Code Review' button outlined in dark orange.](<https://docs.github.com/assets/images/help/copilot/code-review/xcode-ccr-button.png>)

4. Click either **Review Staged Changes** or **Review Unstaged Changes**.

5. A list of files containing changes is displayed in the chat window. Click the check boxes to deselect any files you don't want Copilot to review.

6. Click **Continue** to start the review process.

7. If Copilot finds things to comment on, it displays a **Reviewed Changes** list in the chat window, listing the files it has commented on. Click a file in this list to see the comments.

   Each comment is shown in a popup, overlaid over the editor.

   ![Screenshot of a Copilot code review review comment.](<https://docs.github.com/assets/images/help/copilot/code-review/xcode-review-popup.png>)

8. If there is more than one comment in the file, use the up and down arrows, at the top right of the popup, to navigate between comments.

9. Copilot may suggest replacement code. You can apply the suggested change by clicking **Accept** or reject it by clicking **Dismiss**.

10. Click another file in the **Reviewed Changes** list in the chat window, to see the review comments for another file.

</div>

<div class="ghd-tool jetbrains">

## Prerequisites

* **Access to Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).

* **Compatible JetBrains IDE**. To use GitHub Copilot in JetBrains, you must have a compatible JetBrains IDE installed. GitHub Copilot is compatible with the following IDEs:

  * IntelliJ IDEA (Ultimate, Community, Educational)
  * Android Studio
  * CLion
  * Code With Me Guest
  * DataGrip
  * DataSpell
  * GoLand
  * JetBrains Client
  * MPS
  * PhpStorm
  * PyCharm (Professional, Community, Educational)
  * Rider
  * RubyMine
  * RustRover
  * WebStorm

  See the [JetBrains IDEs](<https://www.jetbrains.com/products/?ref_product=copilot\&ref_type=engagement\&ref_style=button>) tool finder to download.

* **Latest version of the GitHub Copilot extension**. See the [GitHub Copilot plugin](<https://plugins.jetbrains.com/plugin/17718-github-copilot?ref_product=copilot\&ref_type=engagement\&ref_style=text>) in the JetBrains Marketplace. For installation instructions, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains>).

* **Sign in to GitHub in your JetBrains IDE**. For authentication instructions, see [Installing the GitHub Copilot extension in your environment](<https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot/install-copilot-extension?tool=jetbrains#installing-the-github-copilot-plugin-in-your-jetbrains-ide>).

## Using Copilot code review

These instructions explain how to use Copilot code review in JetBrains IDEs. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

1. In a JetBrains IDE, make some changes to one or more files.
2. Open the "Commit" tool window on the left-hand side.
3. Above the commit message input field, click **Copilot: Review Code Changes**. This button appears as a magnifying glass icon with a sparkle.
4. Copilot will begin reviewing your changes.
5. Copilot displays comments in your code with a summary of each potential issue. You can:

   * Review and make changes based on the suggestions.
   * Dismiss a comment by clicking **Discard**.
6. If there is more than one comment, use the up and down arrows, at the top right of the popup, to navigate between comments.

</div>

<div class="ghd-tool cli">

## Prerequisites

* **Access to Copilot**. See [About GitHub Copilot](<https://docs.github.com/en/copilot/get-started/about-github-copilot#get-access>).
* **GitHub CLI**. You must have the GitHub CLI installed and authenticated. See [GitHub CLI quickstart](<https://docs.github.com/en/github-cli/github-cli/quickstart>).

## Using Copilot code review

These instructions explain how to use Copilot code review with the GitHub CLI. To see instructions for other popular coding environments, click the appropriate tab at the top of the page.

### Requesting a review when creating a pull request

You can request a review from Copilot when creating a new pull request using `gh pr create`:

```shell copy
gh pr create --reviewer @copilot
```

You can also select Copilot interactively from the searchable reviewer prompt during `gh pr create`.

```text
? Reviewers  [Use arrows to move, space to select, <right> to all, <left> to none, type to filter]
  [ ]  Search (7472 more)
  [x]  monalisa (Mona Lisa)
> [x]  Copilot (AI)
```

### Requesting a review on an existing pull request

To request a review from Copilot on an existing pull request, use `gh pr edit`. If you are not on the pull request's branch, specify the pull request number:

```shell copy
gh pr edit PR-NUMBER --add-reviewer @copilot
```

Replace `PR-NUMBER` with the number of the pull request you want reviewed. If you have the pull request's branch checked out, you can omit the number.

</div>


</details>
