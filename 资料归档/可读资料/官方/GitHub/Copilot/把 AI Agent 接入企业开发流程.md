# 把 AI Agent 接入企业开发流程

以计划、编码、MCP 测试、代码审查、优化和安全修复串联开发示例，展示各 Copilot 入口如何配合。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-15](<https://github.com/github/docs/commit/1413b140620171c5e4494c52e28a736e4b68b36e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/tutorials/roll-out-at-scale/enable-developers/integrate-ai-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/把 AI Agent 接入企业开发流程.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Integrating agentic AI into your enterprise's software development lifecycle
shortTitle: Integrate AI agents
intro: See how agents can boost productivity across your enterprise.
versions:
  feature: copilot
allowTitleToDifferFromFilename: true
redirect_from:
  - /copilot/rolling-out-github-copilot-at-scale/enabling-developers/integrating-agentic-ai
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/enabling-developers/integrating-agentic-ai
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/enabling-developers/integrate-ai-agents
  - /copilot/tutorials/rolling-out-github-copilot-at-scale/enable-developers/integrate-ai-agents
contentType: tutorials
category:
  - Roll Copilot out at scale
  - Manage Copilot for a team
docsTeamMetrics:
  - ai-governance
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 关于 {% data variables.product.github %} 上的 AI agents

您企业中的开发人员可能已经使用AI作为结对编程工具，在开发过程中同步接收代码建议。

AI agents 更像是同行程序员。他们可以：

* 执行异步任务，例如运行测试或修复待办事项中的问题，而无需人工干预。
* 为开发阶段之外的工作流程做出贡献，例如发布后的构思或优化。

与 agents 协作可以让您的员工有更多时间专注于其他优先事项，例如高层规划，并通过赋予自然语言提示更多权力，将 AI 的优势带给非开发人员角色。

{% data variables.product.prodname_copilot %} 的代理功能集成到 {% data variables.product.github %} 的平台中，与第三方工具相比，提供简化的体验以及简化的许可和治理。

## 示例场景

您是精品雨伞零售商 Mona's 的工程经理。您的团队正在向在线商店添加一个 **AI- 支持的小部件 **，该小部件会根据用户的位置和当地天气推荐雨伞。

为了满足紧迫的期限，您希望加快开发人员和非开发人员的每个阶段的速度，同时保持启动后的维护易于管理。

>[！NOTE] {% data variables.product.github %} 正在不断扩展其 AI- 动力平台。本文描述的一些功能在{% data variables.release-phases.public_preview %}中，并且默认情况下可能不会为企业启用。您可以在 [代理入门 AI](<#get-started-with-agentic-ai>) 部分找到每个功能的资源。

## 1。与{% data variables.copilot.copilot_chat_short %}一起计划

1. 产品经理开始在 [github.com/copilot](<https://github.com/copilot>) 进行规划 **{% data variables.copilot.copilot_chat_short %}**。

他们提出 {% data variables.product.prodname_copilot_short %} 高级问题，以了解新功能所需的开发工作。为了让 {% data variables.product.prodname_copilot_short %} 访问有关项目的重要上下文，他们上传模型文件并链接到存储代码库的存储库。

1. 当PM与{% data variables.product.prodname_copilot_short %}合作以获得所需任务的概述时，他们要求{% data variables.product.prodname_copilot_short %}为工作的每个部分**创建问题**。

PM 将一些问题标记为“必备”或“维护”。这些可能是 {% data variables.copilot.copilot_cloud_agent %} 的良好候选者。

![Copilot聊天截图。 Copilot 询问用户是否愿意继续创建一组优先问题。](<https://github.com/assets/images/help/copilot/sdlc-guide/issue-creation.png>)

1. 为了帮助开发者快速入门，PM在[github.com/copilot/spaces](<https://github.com/copilot/spaces>)处创建了一个带有**{% data variables.copilot.copilot_spaces %}**的空间。 PM 收集图表和代码文件参考等资源，提交一些测试问题，然后与其组织共享空间。

开发人员现在可以在所有 PM 的上下文都可用的情况下在空间中提问。

## 2。使用 {% data variables.copilot.copilot_cli_short %} 创建

1. 在 {% data variables.product.prodname_copilot_short %} 空间中提出一些初始问题后，开发人员在其终端中启动 {% data variables.copilot.copilot_cli_short %} 会话以开始查看代码。
1. 在“计划”模式下，开发人员要求{% data variables.product.prodname_copilot_short %}为该工作推荐几种AI模型，并列出每个模型的优缺点。
1. 编写一些代码后，开发人员要求 {% data variables.product.prodname_copilot_short %} 将代码重构为几个不同的函数，并根据组织的标准对其进行 lint 处理。他们调用组织的自定义 agents 之一，其中包括组织的自定义指令。

{% data variables.product.prodname_copilot_short %} 可以一次更新多个文件，并在开发人员授权的情况下运行命令来执行安装依赖项或运行测试等操作。

1. 在打开拉取请求之前，开发人员运行 `/security-review` 来识别活动本地更改中可能存在的漏洞并应用修复措施。

1. 开发人员检查差异并选择保留哪些代码。

## 3。使用 MCP 服务器进行测试

1. 代码完成后，开发人员使用 Playwright 在本地构建上运行测试。

   1. 开发人员已为 Playwright 配置了 **Model Context Protocol (MCP) 服务器 **，这是企业私有 MCP 注册表中批准的服务器。
   1. 开发人员要求 {% data variables.product.prodname_copilot_short %} 在 `.feature` 文件中概述测试场景，然后在浏览器中运行测试。
   1. {% data variables.product.prodname_copilot_short %} 要求开发者在打开浏览器并与 UI 交互时授权其操作。它识别失败的测试并建议修复。

1. 当他们对测试感到满意时，开发人员会要求 {% data variables.product.prodname_copilot_short %} 为 {% data variables.product.github %} 上的工作打开拉取请求。使用 **{% data variables.product.github %} MCP 服务器 **，{% data variables.product.prodname_copilot_short %} 打开一个已填写标题和描述的拉取请求。

   >[!TIP]
   > 与 {% data variables.product.github %} MCP 服务器的交互受到 ** 推送保护 ** 的保护，这会阻止机密包含在 AI- 生成的响应中，并防止您通过以下方式泄露机密您使用服务器执行的任何操作（仅限公共存储库）。

## 4。与{% data variables.copilot.copilot_code-review_short %}一起回顾

1. 存储库所有者已在存储库上由 {% data variables.product.prodname_copilot_short %} 配置自动 ** 代码审查 **。 {% data variables.product.prodname_copilot_short %} 提供对拉取请求的初步审查，识别开发人员可以在人工审查员处理拉取请求之前修复的错误和潜在性能问题。
1. 开发人员的同事审查并批准拉取请求。工作已准备好合并。

## 5。使用 {% data variables.copilot.copilot_cloud_agent %} 进行优化

1. 发布后，产品经理发现了通过切换到更可靠的天气来改进小部件的机会 API。他们创建了一个问题，并 ** 将其直接分配给 {% data variables.product.github %} 上的 {% data variables.product.prodname_copilot_short %}**。
1. {% data variables.copilot.copilot_cloud_agent %} 在后台工作并打开拉取请求，产品经理将其标记为准备审查。

![{% data variables.copilot.copilot_cloud_agent %}.](<https://github.com/assets/images/help/copilot/sdlc-guide/agent-pr.png>) 创建的拉取请求的屏幕截图

1. 开发人员审查拉取请求并留下反馈，{% data variables.product.prodname_copilot_short %} 会纳入其中。然后开发人员合并拉取请求。

   >[！TIP] {% data variables.copilot.copilot_cloud_agent %} 带有默认护栏。例如，{% data variables.product.prodname_copilot_short %} 本身无法合并拉取请求。您可以使用存储库规则集为目标分支定义额外的保护。

1. 后来，在开发单独的功能时，开发人员注意到 AI 小部件的代码中存在一个小错误。为了避免上下文切换，开发人员将工作直接从 {% data variables.copilot.copilot_cli_short %} 会话委托给 {% data variables.copilot.copilot_cloud_agent %}。

   `/delegate Create a PR for the widget function to correctly validate that the user's age is a positive integer.`

## 6。使用 {% data variables.copilot.copilot_autofix_short %} 进行安全保护

1. 管理员已在存储库上启用 {% data variables.product.prodname_code_scanning %}，并且 {% data variables.product.prodname_code_scanning %} 警报表明代码中存在潜在漏洞。
1. 安全经理请求 **{% data variables.copilot.copilot_autofix_short %}** 自动建议漏洞修复方案，并由开发人员审核并批准。

![GitHub.com 上的代码扫描警报的屏幕截图。标有“生成修复”的按钮以橙色轮廓显示。](<https://github.com/assets/images/help/copilot/sdlc-guide/autofix.png>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## About AI agents on {% data variables.product.github %}

Developers in your enterprise may already use AI as a pair programming tool, receiving code suggestions synchronously during development.

AI agents are more like peer programmers. They can:

* Perform asynchronous tasks, such as running tests or fixing issues in your backlog, with less need for human intervention.
* Contribute to workflows beyond the development phase, such as ideation or optimization after a release.

Collaborating with agents can give your employees more time to focus on other priorities, such as high-level planning, and bring the benefits of AI to non-developer roles by giving more power to natural language prompts.

{% data variables.product.prodname_copilot %}'s agentic features are integrated into {% data variables.product.github %}'s platform, providing a streamlined experience and simplified licensing and governance compared to third-party tools.

## Example scenario

You're an engineering manager at Mona's, a boutique umbrella retailer. Your team is adding an **AI-powered widget** to the online store that recommends umbrellas based on the user's location and local weather.

To meet a tight deadline, you want to speed up each stage for both developers and non-developers, while keeping maintenance manageable after launch.

>[!NOTE] {% data variables.product.github %} is continually expanding its AI-powered platform. Some of the features described in this article are in {% data variables.release-phases.public_preview %}, and may not be enabled for enterprises by default. You will find resources for each feature in the [Get started with agentic AI](<#get-started-with-agentic-ai>) section.

## 1. Plan with {% data variables.copilot.copilot_chat_short %}

1. A product manager starts planning with **{% data variables.copilot.copilot_chat_short %}** at [github.com/copilot](<https://github.com/copilot>).

   They ask {% data variables.product.prodname_copilot_short %} high-level questions to get a sense of the development work required for the new feature. To give {% data variables.product.prodname_copilot_short %} access to important context about the project, they upload mockup files and link to the repository where the codebase is stored.

1. When the PM has worked with {% data variables.product.prodname_copilot_short %} to get an overview of the tasks required, they ask {% data variables.product.prodname_copilot_short %} to **create issues** for each part of the work.

   The PM marks some of the issues as nice-to-haves or maintenance. These may be good candidates for {% data variables.copilot.copilot_cloud_agent %}.

   ![Screenshot of Copilot Chat. Copilot asks if the user would like to proceed with creating a set of prioritized issues.](<https://github.com/assets/images/help/copilot/sdlc-guide/issue-creation.png>)

1. To help the developer get started quickly, the PM creates a space with **{% data variables.copilot.copilot_spaces %}** at [github.com/copilot/spaces](<https://github.com/copilot/spaces>). The PM collects resources like diagrams and references to code files, submits a few test questions, then shares the space with their organization.

   The developer can now ask questions in the space with all the PM's context already available.

## 2. Create with {% data variables.copilot.copilot_cli_short %}

1. After asking some initial questions in the {% data variables.product.prodname_copilot_short %} space, the developer starts a {% data variables.copilot.copilot_cli_short %} session in their terminal to start looking at the code.
1. In "plan" mode, the developer asks {% data variables.product.prodname_copilot_short %} to recommend several AI models for the job and list the pros and cons of each.
1. After writing some code, the developer asks {% data variables.product.prodname_copilot_short %} to refactor the code into several different functions and lint it based on the organization's standards. They invoke one of the organization's custom agents, which includes custom instructions for the organization.

   {% data variables.product.prodname_copilot_short %} can update multiple files at once and, with the developer's authorization, run commands for actions like installing dependencies or running tests.

1. Before opening a pull request, the developer runs `/security-review` to identify likely vulnerabilities in active local changes and apply remediations.

1. The developer reviews the diff and chooses which code to keep.

## 3. Test with an MCP server

1. With the code finished, the developer runs tests on their local build using Playwright.

   1. The developer has configured the **Model Context Protocol (MCP) server** for Playwright, an approved server in the enterprise's private MCP registry.
   1. The developer asks {% data variables.product.prodname_copilot_short %} to outline test scenarios in a `.feature` file, then run the tests in the browser.
   1. {% data variables.product.prodname_copilot_short %} asks the developer to authorize its actions as it opens the browser and interacts with the UI. It identifies a failing test and suggests a fix.

1. When they're satisfied with the tests, the developer asks {% data variables.product.prodname_copilot_short %} to open a pull request for the work on {% data variables.product.github %}. Using the **{% data variables.product.github %} MCP server**, {% data variables.product.prodname_copilot_short %} opens a pull request with the title and description already filled in.

   >[!TIP]
   > Interactions with the {% data variables.product.github %} MCP server are secured by **push protection**, which blocks secrets from being included in AI-generated responses and prevents you from exposing secrets through any actions you perform using the server (public repositories only).

## 4. Review with {% data variables.copilot.copilot_code-review_short %}

1. A repository owner has configured automatic **code reviews** by {% data variables.product.prodname_copilot_short %} on the repository. {% data variables.product.prodname_copilot_short %} provides an initial review on the pull request, identifying bugs and potential performance issues that the developer can fix before a human reviewer gets to the pull request.
1. The developer's colleague reviews and approves the pull request. The work is ready to merge.

## 5. Optimize with {% data variables.copilot.copilot_cloud_agent %}

1. After the release, the product manager identifies an opportunity to improve the widget by switching to a more reliable weather API. They create an issue and **assign it to {% data variables.product.prodname_copilot_short %}** directly on {% data variables.product.github %}.
1. {% data variables.copilot.copilot_cloud_agent %} works in the background and opens a pull request, which the product manager marks as ready for review.

   ![Screenshot of a pull request created by {% data variables.copilot.copilot_cloud_agent %}.](<https://github.com/assets/images/help/copilot/sdlc-guide/agent-pr.png>)

1. A developer reviews the pull request and leaves feedback, which {% data variables.product.prodname_copilot_short %} incorporates. The developer then merges the pull request.

   >[!TIP] {% data variables.copilot.copilot_cloud_agent %} comes with default guardrails. For example, {% data variables.product.prodname_copilot_short %} cannot merge pull requests by itself. You can define additional protections for target branches using repository rulesets.

1. Later, while working on a separate feature, the developer notices a small bug in the code for the AI widget. To avoid context switching, the developer delegates the work to {% data variables.copilot.copilot_cloud_agent %} directly from their {% data variables.copilot.copilot_cli_short %} session.

   `/delegate Create a PR for the widget function to correctly validate that the user's age is a positive integer.`

## 6. Secure with {% data variables.copilot.copilot_autofix_short %}

1. An administrator has enabled {% data variables.product.prodname_code_scanning %} on the repository, and a {% data variables.product.prodname_code_scanning %} alert suggests a potential vulnerability in the code.
1. A security manager requests **{% data variables.copilot.copilot_autofix_short %}** to automatically suggest a fix for the vulnerability, which a developer reviews and approves.

   ![Screenshot of a code scanning alert on GitHub.com. A button labeled "Generate fix" is outlined in orange.](<https://github.com/assets/images/help/copilot/sdlc-guide/autofix.png>)


</details>
