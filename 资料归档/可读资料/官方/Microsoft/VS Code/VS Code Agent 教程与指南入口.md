# VS Code Agent 教程与指南入口

按项目实践、测试、角色定制和故障恢复提供阅读路径，用于找到适合当前开发任务的指南。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/overview.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/overview.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code Agent 教程与指南入口.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: a73cc399-b974-4639-9965-497749172539
DateApproved: 10/7/2026
MetaDescription: Find agent tutorials and guides for {% data variables.product.prodname_vscode_shortname %} by task, from a first app to testing and project workflows.
MetaSocialImage: ../images/agents-overview/chat-view-expanded.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Agent 教程和指南

选择一个教程来通过示例项目进行学习，或者按照指南将 agent 工作流程应用到 {% data variables.product.prodname_vscode_shortname %} 中您自己的代码库。

## 选择从哪里开始

如果您对 {% data variables.product.prodname_vscode_shortname %} 和 AI 都不熟悉，请首先 [ 安装编辑器并打开工作区 ](<https://github.com/docs/getstarted/overview.md>)。如果您已经了解 {% data variables.product.prodname_vscode_shortname %}，请使用以下示例项目之一来了解 agent 工作流程：

<div class="card-grid">
 <a class="card" href="/docs/agents/quickstart">
 <i class="codicon codicon-hubot" aria-hidden="true"></i>
 <div>
 <p><strong>完成你的第一个 agent 任务</strong></p>
 <p>推荐起点。构建并验证一个小型 Web 应用程序，然后查看更改。</p>
 <p>示例项目。无需运行时或构建工具。</p>
 </div>
 </a>
 <a class="card" href="/docs/agents/agents-tutorial">
 <i class="codicon codicon-mortar-board" aria-hidden="true"></i>
 <div>
 <p><strong>逐步构建应用程序</strong></p>
 <p>在学习 agent、编辑器、浏览器和源代码控制工作流程的同时构建作品集页面。</p>
 <p>示例项目。需要 Git。</p>
 </div>
 </a>
</div>

已经使用agents？跳过示例项目并遵循 [ 有经验的 - agent 快速通道 ](<#经验丰富-agent快速通道>)。如果您想要 AI 帮助而不委托更改，[选择轻量级 AI 功能 ](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>)。

或者跳到一个任务：

* [从事项目](<#从事一个项目>)。
* [测试和验证](<#测试和验证>)。
* [定制和协调agents](<#定制和协调-agents>)。
* [改善结果并恢复](<#改善结果并恢复>)。

## 经验丰富-【agent】快速通道

使用您自己的存储库并保留第一个任务的默认线束和会话设置。仅当您的提供商、环境或项目需要不同的设置时才调整它们。

1. [将存储库作为工作区打开](<https://github.com/docs/getstarted/overview.md#open-your-code>)，以便文件、源代码控制、终端、测试和agents共享相同的项目上下文。

1. 将您现有的工作流程映射到 [{% data variables.product.prodname_vscode_shortname %} 会话 ](<https://github.com/docs/agents/concepts/sessions.md>)，然后 [ 了解会话控制 ](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>)。如果提供商或本地、远程和云执行约束很重要，[选择会话目标](<https://github.com/docs/agents/run/agent-harnesses.md#choose-a-session-target>)。

1. 从您自己的存储库中的有界任务开始。 [探索代码库](<https://github.com/docs/agents/guides/explore-a-codebase.md>)而不进行更改，或者[使用现有的开发环境和测试添加功能](<https://github.com/docs/agents/guides/add-a-feature.md>)。

1. [在集成代码编辑之前检查并验证代码编辑](<https://github.com/docs/agents/run/review-code-edits.md>)。

在完成一项实际项目任务后，[使agents适应您的项目](<https://github.com/docs/agents/guides/customize-copilot-guide.md>)并[比较质量、可靠性和AI信用使用情况](<https://github.com/docs/agents/guides/optimize-usage.md>)。如果您在支持的应用程序之间移动，请了解如何[查看来自其他应用程序的会话](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>)。

在托管设备上，您的组织可以控制可用的 agents 和提供商。开发人员可以查看 [agent 可用性故障排除步骤 ](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>)，管理员可以 [ 管理 AI 设置 ](<https://github.com/docs/enterprise/manage-ai-settings.md>)。对于键盘、屏幕阅读器和低视力工作流程，请使用 [ 辅助视图和其他辅助功能 ](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>)。

## 从事一个项目

* [探索代码库](<https://github.com/docs/agents/guides/explore-a-codebase.md>)：在不编辑文件的情况下跟踪行为并根据源引用验证解释。 **使用您自己的存储库。**

* [添加功能](<https://github.com/docs/agents/guides/add-a-feature.md>)：研究、计划、实施和验证有界变更。 **使用您自己的存储库以及工作开发环境和测试。**

* [修复API错误](<https://github.com/docs/agents/guides/fix-a-bug-with-agents.md>)：重现分页错误，添加回归测试，并查看修复情况。 **示例项目。需要 Node.js 22 或更高版本以及 Git。**

* [安全重构](<https://github.com/docs/agents/guides/refactor-safely.md>)：以可审查的步骤更改代码结构，同时保留行为并检查现有调用者。 **使用您自己的存储库。**

* [使用 Jupyter 笔记本](<https://github.com/docs/agents/guides/notebooks-with-ai.md>)：创建或编辑笔记本、运行单元并查看分析结果。 **使用您自己的数据。需要 Jupyter 扩展和笔记本内核。**

## 测试和验证

* [向现有代码添加测试](<https://github.com/docs/agents/guides/test-code-with-ai.md>)：为函数或模块生成测试，运行它们，并检查断言、失败和覆盖率。 **使用自己的项目。**

* [使用浏览器工具验证网络应用程序](<https://github.com/docs/agents/guides/browser-agent-testing-guide.md>)：构建一个计算器应用程序并根据可观察的验收标准对其进行检查。 **示例网络应用程序。**

* [设置测试驱动开发](<https://github.com/docs/agents/guides/test-driven-development-guide.md>)：创建自定义agents和可重复的测试优先工作流程的说明。 **工作流程设置，而不是一次性测试任务。**

## 定制和协调 agents

* [使agents适应您的项目](<https://github.com/docs/agents/guides/customize-copilot-guide.md>)：与您的团队共享项目说明，然后根据需要添加技能或专业agents。 **使用您自己的存储库。**

* [设置上下文工程工作流程](<https://github.com/docs/agents/guides/context-engineering-guide.md>)：管理项目上下文，并通过说明、自定义 agents 和提示文件将规划与实施联系起来。 **工作流程设置。**

* [并行运行独立任务](<https://github.com/docs/agents/guides/delegate-two-tasks.md>)：在单独的 Git 工作树中工作，检查每个结果，然后集成并重新测试更改。 **完成单个 agent 任务后使用您自己的 Git 存储库。**

## 改善结果并恢复

* [让agent回到正轨](<https://github.com/docs/agents/guides/get-agent-back-on-track.md>)：选择是否引导请求、恢复不需要的更改或重置对话上下文。

* [减少AI信用使用](<https://github.com/docs/agents/guides/optimize-usage.md>)：比较模型成本和结果，关注上下文和范围工具，以减少不必要的使用。

* [查找提示示例](<https://github.com/docs/agents/guides/prompt-examples.md>)：调整用于探索代码、构建功能、调试和测试的提示。

## 背景和帮助

* [agents如何工作](<https://github.com/docs/agents/concepts/agents.md>)：了解agent循环以及模型、上下文和工具的作用。

* [使用AI](<https://github.com/docs/agents/best-practices.md>)的最佳实践：范围任务、提供相关上下文并验证结果。

* [对 AI 功能进行故障排除 ](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>)：诊断设置、登录、连接和其他技术问题。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent tutorials and guides

Choose a tutorial to learn with a sample project, or follow a guide to apply an agent workflow to your own codebase in {% data variables.product.prodname_vscode_shortname %}.

## Choose where to start

If you're new to both {% data variables.product.prodname_vscode_shortname %} and AI, first [install the editor and open a workspace](<https://github.com/docs/getstarted/overview.md>). If you already know {% data variables.product.prodname_vscode_shortname %}, use one of these sample projects to learn the agent workflow:

<div class="card-grid">
    <a class="card" href="/docs/agents/quickstart">
        <i class="codicon codicon-hubot" aria-hidden="true"></i>
        <div>
            <p><strong>Complete your first agent task</strong></p>
            <p>Recommended starting point. Build and validate a small web app, then review the changes.</p>
            <p>Sample project. No runtime or build tools required.</p>
        </div>
    </a>
    <a class="card" href="/docs/agents/agents-tutorial">
        <i class="codicon codicon-mortar-board" aria-hidden="true"></i>
        <div>
            <p><strong>Build an app step by step</strong></p>
            <p>Build a portfolio page while learning agent, editor, browser, and source control workflows.</p>
            <p>Sample project. Requires Git.</p>
        </div>
    </a>
</div>

Already use agents? Skip the sample projects and follow the [experienced-agent fast track](<#experienced-agent-fast-track>). If you want AI help without delegating changes, [choose a lighter-weight AI feature](<https://github.com/docs/agents/reference/ai-features-cheat-sheet.md#choose-a-feature>).

Or jump to a task:

* [Work on a project](<#work-on-a-project>).
* [Test and validate](<#test-and-validate>).
* [Customize and coordinate agents](<#customize-and-coordinate-agents>).
* [Improve results and recover](<#improve-results-and-recover>).

## Experienced-agent fast track

Use your own repository and keep the default harness and session settings for your first task. Adjust them only when your provider, environment, or project requires a different setup.

1. [Open your repository as a workspace](<https://github.com/docs/getstarted/overview.md#open-your-code>) so that files, source control, terminals, tests, and agents share the same project context.

1. Map your existing workflow to [{% data variables.product.prodname_vscode_shortname %} sessions](<https://github.com/docs/agents/concepts/sessions.md>), then [understand the session controls](<https://github.com/docs/agents/run/agent-harnesses.md#understand-the-session-controls>). If provider or local, remote, and cloud execution constraints matter, [choose a session target](<https://github.com/docs/agents/run/agent-harnesses.md#choose-a-session-target>).

1. Start with a bounded task in your own repository. [Explore the codebase](<https://github.com/docs/agents/guides/explore-a-codebase.md>) without making changes, or [add a feature](<https://github.com/docs/agents/guides/add-a-feature.md>) with your existing development environment and tests.

1. [Review and validate the code edits](<https://github.com/docs/agents/run/review-code-edits.md>) before you integrate them.

After one real-project task, [adapt agents to your project](<https://github.com/docs/agents/guides/customize-copilot-guide.md>) and [compare quality, reliability, and AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md>). If you move between supported applications, learn how to [view sessions from other applications](<https://github.com/docs/agents/run/sessions/manage-sessions.md#view-sessions-from-other-applications>).

On a managed device, your organization might control which agents and providers are available. Developers can review the [agent availability troubleshooting steps](<https://github.com/docs/agents/agent-troubleshooting/faq.md#agents-are-not-available-in-chat>), and administrators can [manage AI settings](<https://github.com/docs/enterprise/manage-ai-settings.md>). For keyboard, screen reader, and low-vision workflows, use the [Accessible View and other accessibility features](<https://github.com/docs/configure/accessibility/accessibility.md#accessible-view>).

## Work on a project

* [Explore a codebase](<https://github.com/docs/agents/guides/explore-a-codebase.md>): trace a behavior without editing files and verify explanations against source references. **Use your own repository.**

* [Add a feature](<https://github.com/docs/agents/guides/add-a-feature.md>): research, plan, implement, and verify a bounded change. **Use your own repository with a working development environment and tests.**

* [Fix an API bug](<https://github.com/docs/agents/guides/fix-a-bug-with-agents.md>): reproduce a pagination bug, add a regression test, and review the fix. **Sample project. Requires Node.js 22 or later and Git.**

* [Refactor safely](<https://github.com/docs/agents/guides/refactor-safely.md>): change code structure in reviewable steps while preserving behavior and checking existing callers. **Use your own repository.**

* [Work with Jupyter notebooks](<https://github.com/docs/agents/guides/notebooks-with-ai.md>): create or edit a notebook, run cells, and review analysis results. **Use your own data. Requires the Jupyter extension and a notebook kernel.**

## Test and validate

* [Add tests to existing code](<https://github.com/docs/agents/guides/test-code-with-ai.md>): generate tests for a function or module, run them, and review assertions, failures, and coverage. **Use your own project.**

* [Validate a web app with browser tools](<https://github.com/docs/agents/guides/browser-agent-testing-guide.md>): build a calculator app and check it against observable acceptance criteria. **Sample web app.**

* [Set up test-driven development](<https://github.com/docs/agents/guides/test-driven-development-guide.md>): create custom agents and instructions for a repeatable test-first workflow. **Workflow setup, rather than a one-off testing task.**

## Customize and coordinate agents

* [Adapt agents to your project](<https://github.com/docs/agents/guides/customize-copilot-guide.md>): share project instructions with your team, then add skills or specialized agents where needed. **Use your own repository.**

* [Set up a context engineering workflow](<https://github.com/docs/agents/guides/context-engineering-guide.md>): curate project context and connect planning to implementation with instructions, custom agents, and prompt files. **Workflow setup.**

* [Run independent tasks in parallel](<https://github.com/docs/agents/guides/delegate-two-tasks.md>): work in separate Git worktrees, review each result, then integrate and retest the changes. **Use your own Git repository after completing a single agent task.**

## Improve results and recover

* [Get an agent back on track](<https://github.com/docs/agents/guides/get-agent-back-on-track.md>): choose whether to steer a request, recover unwanted changes, or reset conversation context.

* [Reduce AI credit usage](<https://github.com/docs/agents/guides/optimize-usage.md>): compare model costs and results, focus context, and scope tools to reduce unnecessary usage.

* [Find prompt examples](<https://github.com/docs/agents/guides/prompt-examples.md>): adapt prompts for exploring code, building features, debugging, and testing.

## Background and help

* [How agents work](<https://github.com/docs/agents/concepts/agents.md>): understand the agent loop and the role of models, context, and tools.

* [Best practices for using AI](<https://github.com/docs/agents/best-practices.md>): scope tasks, provide relevant context, and verify results.

* [Troubleshoot AI features](<https://github.com/docs/agents/agent-troubleshooting/troubleshooting.md>): diagnose setup, sign-in, connection, and other technical issues.


</details>
