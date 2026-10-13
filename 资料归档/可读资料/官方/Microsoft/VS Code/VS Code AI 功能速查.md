# VS Code AI 功能速查

按任务列出编辑、Agent 委派、结果改进、权限、定制及调试入口，并附常用命令和快捷键。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/ai-features-cheat-sheet.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/reference/ai-features-cheat-sheet.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/VS Code AI 功能速查.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: de6f9f68-7dd5-4de3-a210-3db57882384b
DateApproved: 10/7/2026
MetaDescription: Choose AI features in {% data variables.product.prodname_vscode_shortname %} for coding, planning, testing, and reviewing changes.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# AI 功能备忘单

在 {% data variables.product.prodname_vscode %} 中找到适合您任务的 AI 功能，从集中编辑到委托给 agent 的工作。使用此参考来了解常见任务、有用的控件和快捷方式。

agents 新手？ [使用agent](<https://github.com/docs/agents/quickstart.md>)完成你的第一个任务。可用功能取决于您的 [agent 线束 ](<https://github.com/docs/agents/run/agent-harnesses.md>)、型号、帐户和组织策略。

<a name="access-ai-in-vs-code"></a>
<a name="editor-ai-features"></a>
<a name="scaffold-a-new-project"></a>
<a name="source-control-and-issues"></a>
<a name="python-and-notebook-support"></a>
<a name="search-and-settings"></a>

## 选择一个功能

|我想...|使用|从...开始|
|---|---|---|
|理解不熟悉的代码|[聊天](<https://github.com/docs/chat/chat-overview.md>)|询问“跟踪请求如何到达数据库”。|
|计划复杂的变革|[计划agent](<https://github.com/docs/agents/run/planning.md>)|选择**计划**并描述实施前研究的变化。|
|构建功能、修复错误或创建项目| [Agents](<#使用agents>) |选择 **Agent**，描述结果并指定如何验证它。|
|进行有针对性的编辑|[内嵌聊天](<https://github.com/docs/chat/inline-chat.md>)|在编辑器中选择代码并描述更改。|
|在打字和编辑时获取帮助|[内嵌建议](<https://github.com/docs/editing/ai-powered-suggestions.md>)|接受幻影文本以完成代码，或使用下一个编辑建议导航并应用相关编辑。|
|管理跨项目的独立任务| [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) |选择标题栏中的**在Agents**中打开，然后启动并监控会话。|
|返回拉取请求的委托工作|[云 agents](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>)|选择 **Cloud** 会话目标和存储库。|
|检查网络应用程序的行为|[浏览器工具](<https://github.com/docs/agents/run/browser-tools.md>)|要求 agent 打开应用程序，测试用户流程并报告结果。|
|按计划重复日常工作|[自动化](<https://github.com/docs/agents/run/automations.md>) `feature(automations)`|在 {% data variables.copilot.agents_window %} 中保存提示和时间表。|
|生成提交消息或重命名符号|[智能动作](<https://github.com/docs/editing/copilot-smart-actions.md>)|使用闪光操作或编辑器上下文菜单，无需编写提示。|
|探索数据或编辑笔记本|[AI 用于笔记本电脑](<https://github.com/docs/agents/guides/notebooks-with-ai.md>)|在 **本地** agent 会话中，要求 agent 创建、编辑和运行笔记本单元。|
|查找没有确切关键字的代码或设置|[语义搜索](<https://github.com/docs/editing/copilot-smart-actions.md#semantic-search-results-preview>)（预览）和[AI设置搜索](<https://github.com/docs/editing/copilot-smart-actions.md#search-settings-with-ai>)|在搜索视图中按含义搜索，或在设置编辑器中描述设置。|

<a name="chat-experience-in-vs-code"></a>
<a name="planning"></a>

## 使用agents

使用 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) 在代码旁边工作，或使用 **{% data variables.copilot.agents_window %}** 专注于分配更高级别的任务和审查结果。

对于复杂的更改，**计划**，**实施**，**验证**，然后**审查**：

* **计划：**要求计划agent研究变更并识别风险。在将其交付实施之前检查该方法 agent。
* **实现：** 描述预期的行为和约束。 agent 搜索您的代码、编辑文件并使用工具。
* **验证：**要求agent运行相关测试或练习应用程序。检查它实际检查的内容。
* **审查：**在提交或合并之前审查差异和验证结果。

例如，对 agent 进行具体的浏览器检查：

```prompt
Open the app and test the sign-up flow with a missing email address.
Verify that it shows a validation error and does not submit the form.
Report what you tested and any problems you found.
```

组织更大的任务：

* 使用[单独的会话和工作树](<https://github.com/docs/agents/guides/delegate-two-tasks.md>)来执行不应共享代码更改的独立任务。
* 使用 [ 子代理 ](<https://github.com/docs/agents/run/subagents.md>) 在单独的上下文中委派任务的重点部分。
* [当您想要继续使用另一个受支持的会话目标时，请切换会话](<https://github.com/docs/agents/run/agent-harnesses.md#hand-off-a-session>)。

<a name="add-context-to-your-prompt"></a>

## 获得更好的结果

|需要|使用什么|
|---|---|
|将请求置于特定代码或失败中|[添加上下文](<https://github.com/docs/chat/copilot-chat-context.md>)和**添加上下文**、`#`-提及或拖动的文件。附上相关代码、错误、测试输出或 GitHub 问题。|
|探索代码库，无需亲自查找每个文件|让agent通过搜索和语言工具收集[工作空间上下文](<https://github.com/docs/agents/reference/workspace-context.md>)。|
|平衡推理、速度和成本|使用 [ 模型选择器 ](<https://github.com/docs/agent-customization/language-models.md>) 或 ** 自动 **（如果可用）。更改型号不会更改 agent 线束。|
|与另一个 agent 提供商合作|[选择线束](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>)，例如{% data variables.product.prodname_copilot_short %}、{% data variables.product.prodname_anthropic_claude %}或{% data variables.product.prodname_openai_codex %}。检查其工具、身份验证选项和设置要求。|
|使用您自己的模型提供商或本地模型|配置[自带密钥(BYOK)](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>)。内联建议和语义搜索仍然需要 {% data variables.product.prodname_copilot %} 服务。|
|在对话中保留有用的知识|请agent用[记忆](<https://github.com/docs/agents/run/memory.md>)来记住它。使用**聊天：显示内存文件**检查存储的笔记。|
|管理长时间对话或其成本|检查[上下文使用情况](<https://github.com/docs/agents/run/sessions/manage-sessions.md#manage-session-context>)，压缩对话，或为不相关的任务开始新的聊天。参见【[】使用指南【](<https://github.com/docs/agents/guides/optimize-usage.md>)】。|

<a name="review-code-experimental"></a>

## 保持控制

|我需要...|行动|
|---|---|
|决定哪些操作需要确认|选择会话 [ 权限级别 ](<https://github.com/docs/agents/run/approvals.md#permission-levels>) 并查看工具请求。 **允许所有**绕过确认，不应被视为使用agents的先决条件。|
|重定向正在进行的工作|[引导、对后续请求进行排队或停止请求](<https://github.com/docs/chat/chat-overview.md#send-messages-while-a-request-is-running>)。停止不会撤消已完成的操作。|
|检查或撤消 agent 更改|[查看差异、提供反馈或恢复检查点](<https://github.com/docs/agents/run/review-code-edits.md>)。在集成之前验证结果。|
|限制终端对文件和网络的访问|使用 [agent 终端沙箱 ](<https://github.com/docs/agents/run/agent-sandboxing.md>)（在 macOS、Linux 和 WSL2 上预览，在 Windows 上进行实验）。 Git 工作树分隔代码更改，但不是安全边界。|
|请 AI 审核我的代码|使用 [ 代码审查智能操作 ](<https://github.com/docs/editing/copilot-smart-actions.md#review-code>)。这会生成审核反馈，但不能替代您对 agent 更改的审核。|

> [!IMPORTANT]
> Agents 可以将编辑直接保存到会话的文件夹或工作树中。 **手动权限** 不要求对每个文件编辑进行确认。在处理现有项目之前，请查看 [ 安全基线 ](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>)。

<a name="customize-your-chat-experience"></a>

## 定制您的工作流程

|我想...|使用|
|---|---|
|应用项目约定而不重复它们|[编码标准、架构和测试命令的说明](<https://github.com/docs/agent-customization/custom-instructions.md>)。|
|使用支持脚本或示例打包工作流程|[Agent skills](<https://github.com/docs/agent-customization/agent-skills.md>)，相关时加载或按名称调用。|
|按需运行已保存的提示|[提示文件](<https://github.com/docs/agent-customization/prompt-files.md>)，作为斜杠命令调用。|
|定义专门的角色和工具选择|[自定义agents](<https://github.com/docs/agent-customization/custom-agents.md>)，例如只读审阅者。|
|连接数据库、问题跟踪器或其他外部系统|[MCP 提供工具的服务器](<https://github.com/docs/agent-customization/mcp-servers.md>)。|
|在特定的 agent 生命周期事件中运行命令|[Hooks](<https://github.com/docs/agent-customization/hooks.md>)，而不是依赖模型来遵循指令。|
|安装一组打包的自定义项|[Agent 插件](<https://github.com/docs/agent-customization/agent-plugins.md>)。|

使用 [Agent 自定义编辑器 ](<https://github.com/docs/agent-customization/overview.md#agent-customizations-editor>) 为您选择的线束创建和管理这些选项。

<a name="generate-tests"></a>

## 调试并修复问题

|任务|起点|
|---|---|
|生成测试|选择代码并使用 **生成代码** > **生成测试**，或要求 agent 添加并使用您的项目框架运行测试。|
|诊断失败的测试|在测试资源管理器中选择**修复测试失败**，或将失败输出附加到聊天中。|
|修复编译器或 linting 错误|使用编辑器代码操作（灯泡），或将问题附加到聊天中。|
|配置调试会话|请求调试配置，或在终端运行命令之前使用 `copilot-debug`（预览），例如 `copilot-debug python foo.py`。|

将任务、约束和验证合并到一个请求中：

```prompt
Find the cause of the failing authentication test.
Fix the implementation without changing the expected behavior.
Run the relevant tests and summarize the change and results.
```

请参阅[测试和错误的智能操作](<https://github.com/docs/editing/copilot-smart-actions.md#fix-testing-errors>)和[调试](<https://github.com/docs/debugtest/debugging.md>)。

<a name="terminal"></a>

## 基本键盘快捷键

|行动|快捷方式|
|---|---|
|打开{% data variables.copilot.chat_view %}| `kb(workbench.action.chat.open)` |
|开始新的聊天| `kb(workbench.action.chat.newChat)` |
|打开编辑器内嵌聊天| `kb(inlineChat.start)` |
|打开[终端内联聊天](<https://github.com/docs/chat/inline-chat.md#use-terminal-inline-chat>)| `kb(workbench.action.terminal.chat.start)` |
|打开[快速聊天](<https://github.com/docs/chat/inline-chat.md#use-quick-chat>)| `kb(workbench.action.quickchat.toggle)` |
|接受内联建议| `kb(editor.action.inlineSuggest.commit)` |
|拒绝内嵌建议| `kb(editor.action.inlineSuggest.hide)` |

对于语音交互，请参阅[语音模式](<https://github.com/docs/configure/accessibility/voice.md#use-voice-mode>) `feature(voice-mode)`和[内置听写](<https://github.com/docs/configure/accessibility/voice.md#use-built-in-dictation>) `feature(built-in-dictation)`。

## 聊天参考

### 聊天工具

Agents自动选择工具。您不需要在提示中命名每个工具。键入 `#` 浏览可用工具和上下文项，或引用工具将 agent 定向到某项功能。有关各个名称，请参阅[工具和上下文参考](<https://github.com/docs/agents/reference/tools-reference.md>)。

### 斜杠命令

输入 `/` 查看当前会话中可用的命令。可用性取决于聊天界面、工具、角色和启用的功能。

|命令|目的|
|---|---|
| `/plan` |研究提出实施方案。|
| `/explain`, `/fix`, `/tests` |解释代码、建议修复或生成测试。|
| `/doc` |从编辑器内联聊天生成文档注释。|
|`/setupTests`（实验）|获取选择和设置测试框架的帮助。|
| `/new`, `/newNotebook` |搭建工作区、文件或笔记本。|
| `/compact`, `/fork` |紧凑的上下文，或根据其历史分支对话。|
| `/clear` |开始新的聊天并存档或将当前聊天标记为已完成。|
| `/rename <name>`, `/help` |重命名本地聊天，或在本地询问聊天中列出命令和 agents。|
| `/models`, `/tools` |打开模型选择器，或配置本地聊天工具。|
| `/init` |在本地 agent 会话中生成或更新工作区指令。|
| `/agents`, `/instructions`, `/skills`, `/prompts`, `/hooks` |配置对应的定制类型。|
| `/create-agent`, `/create-instructions`, `/create-skill`, `/create-prompt`, `/create-hook` |在本地 agent 会话中生成自定义。|
| `/troubleshoot` |在本地或 {% data variables.copilot.copilot_cli_short %} 会话中分析 agent 调试日志。|
| `/debug` |从 {% data variables.copilot.chat_view %} 而不是 {% data variables.copilot.agents_window %} 打开聊天调试视图。|
| `/sandbox-policy` |检查 {% data variables.product.prodname_copilot_short %} Agent 主机会话的 [ 有效沙箱策略 ](<https://github.com/docs/agents/run/agent-sandboxing.md#inspect-the-effective-sandbox-policy>)。|
| `/<name>` |按名称调用 agent 技能或可重复使用的提示。|

例如，`.github/skills/webapp-testing/SKILL.md`中的技能可以用`/webapp-testing`来调用。有关权限和 Autopilot 命令，请参阅 [ 批准和权限 ](<https://github.com/docs/agents/run/approvals.md>)。

### 聊天参与者

在支持参与者的聊天界面中，输入 `@` 以选择专家。使用 `@github` 表示存储库、问题和拉取请求，使用 `@terminal` 表示 shell 问题，或使用 `@vscode` 表示编辑器功能和设置。例如，`@vscode /search` 生成搜索视图查询。

## 后续步骤

* [将 AI 最佳实践应用于您的项目 ](<https://github.com/docs/agents/best-practices.md>)。
* [查找AI设置](<https://github.com/docs/agents/reference/ai-settings.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# AI features cheat sheet

Find the AI feature in {% data variables.product.prodname_vscode %} that fits your task, from a focused edit to work you delegate to an agent. Use this reference for common tasks, useful controls, and shortcuts.

New to agents? [Complete your first task with an agent](<https://github.com/docs/agents/quickstart.md>). Available features depend on your [agent harness](<https://github.com/docs/agents/run/agent-harnesses.md>), model, account, and organization policies.

<a name="access-ai-in-vs-code"></a>
<a name="editor-ai-features"></a>
<a name="scaffold-a-new-project"></a>
<a name="source-control-and-issues"></a>
<a name="python-and-notebook-support"></a>
<a name="search-and-settings"></a>

## Choose a feature

| I want to... | Use | Start with... |
|---|---|---|
| Understand unfamiliar code | [Chat](<https://github.com/docs/chat/chat-overview.md>) | Ask "Trace how a request reaches the database." |
| Plan a complex change | [Plan agent](<https://github.com/docs/agents/run/planning.md>) | Select **Plan** and describe the change to research before implementation. |
| Build a feature, fix a bug, or create a project | [Agents](<#use-agents>) | Select **Agent**, describe the outcome, and specify how to verify it. |
| Make a focused edit | [Inline chat](<https://github.com/docs/chat/inline-chat.md>) | Select code in the editor and describe the change. |
| Get help while typing and editing | [Inline suggestions](<https://github.com/docs/editing/ai-powered-suggestions.md>) | Accept ghost text to complete code, or use next edit suggestions to navigate to and apply a related edit. |
| Manage independent tasks across projects | [{% data variables.copilot.agents_window %}](<https://github.com/docs/agents/run/agents-window.md>) | Select **Open in Agents** in the title bar, then start and monitor sessions. |
| Delegate work that returns a pull request | [Cloud agents](<https://github.com/docs/agents/run/agent-harnesses.md#start-a-cloud-session>) | Choose the **Cloud** session target and a repository. |
| Check a web app's behavior | [Browser tools](<https://github.com/docs/agents/run/browser-tools.md>) | Ask the agent to open the app, test a user flow, and report the result. |
| Repeat routine work on a schedule | [Automations](<https://github.com/docs/agents/run/automations.md>) `feature(automations)` | Save a prompt and schedule in the {% data variables.copilot.agents_window %}. |
| Generate commit messages or rename symbols | [Smart actions](<https://github.com/docs/editing/copilot-smart-actions.md>) | Use the sparkle action or editor context menu without writing a prompt. |
| Explore data or edit a notebook | [AI for notebooks](<https://github.com/docs/agents/guides/notebooks-with-ai.md>) | In a **Local** agent session, ask the agent to create, edit, and run notebook cells. |
| Find code or settings without exact keywords | [Semantic search](<https://github.com/docs/editing/copilot-smart-actions.md#semantic-search-results-preview>) (Preview) and [AI settings search](<https://github.com/docs/editing/copilot-smart-actions.md#search-settings-with-ai>) | Search by meaning in the Search view, or describe a setting in the Settings editor. |

<a name="chat-experience-in-vs-code"></a>
<a name="planning"></a>

## Use agents

Use the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) to work beside your code, or the **{% data variables.copilot.agents_window %}** to focus on assigning higher-level tasks and reviewing outcomes.

For a complex change, **plan**, **implement**, **verify**, then **review**:

* **Plan:** Ask the Plan agent to research the change and identify risks. Review the approach before handing it to an implementation agent.
* **Implement:** Describe the intended behavior and constraints. The agent searches your code, edits files, and uses tools.
* **Verify:** Ask the agent to run relevant tests or exercise the app. Inspect what it actually checked.
* **Review:** Review the diff and validation results before committing or merging.

For example, give the agent a concrete browser check:

```prompt
Open the app and test the sign-up flow with a missing email address.
Verify that it shows a validation error and does not submit the form.
Report what you tested and any problems you found.
```

To organize larger tasks:

* Use [separate sessions and worktrees](<https://github.com/docs/agents/guides/delegate-two-tasks.md>) for independent tasks that should not share code changes.
* Use [subagents](<https://github.com/docs/agents/run/subagents.md>) to delegate a focused part of a task in a separate context.
* [Hand off a session](<https://github.com/docs/agents/run/agent-harnesses.md#hand-off-a-session>) when you want to continue work with another supported session target.

<a name="add-context-to-your-prompt"></a>

## Get better results

| Need | What to use |
|---|---|
| Ground a request in specific code or a failure | [Add context](<https://github.com/docs/chat/copilot-chat-context.md>) with **Add Context**, `#`-mentions, or dragged files. Attach relevant code, errors, test output, or GitHub issues. |
| Explore the codebase without finding every file yourself | Let the agent gather [workspace context](<https://github.com/docs/agents/reference/workspace-context.md>) with search and language tools. |
| Balance reasoning, speed, and cost | Use the [model picker](<https://github.com/docs/agent-customization/language-models.md>), or **Auto** when available. Changing the model does not change the agent harness. |
| Work with another agent provider | [Choose a harness](<https://github.com/docs/agents/run/agent-harnesses.md#configure-a-harness-or-cloud-target>), such as {% data variables.product.prodname_copilot_short %}, {% data variables.product.prodname_anthropic_claude %}, or {% data variables.product.prodname_openai_codex %}. Check its tools, authentication options, and setup requirements. |
| Use your own model provider or a local model | Configure [bring your own key (BYOK)](<https://github.com/docs/agent-customization/language-models.md#bring-your-own-language-model-key>). Inline suggestions and semantic search still require the {% data variables.product.prodname_copilot %} service. |
| Retain useful knowledge across conversations | Ask the agent to remember it with [memory](<https://github.com/docs/agents/run/memory.md>). Use **Chat: Show Memory Files** to inspect stored notes. |
| Manage a long conversation or its cost | Check [context usage](<https://github.com/docs/agents/run/sessions/manage-sessions.md#manage-session-context>), compact the conversation, or start a new chat for an unrelated task. See [usage guidance](<https://github.com/docs/agents/guides/optimize-usage.md>). |

<a name="review-code-experimental"></a>

## Stay in control

| I need to... | Action |
|---|---|
| Decide which actions need confirmation | Choose a session [permission level](<https://github.com/docs/agents/run/approvals.md#permission-levels>) and review tool requests. **Allow all** bypasses confirmations and should not be treated as a prerequisite for using agents. |
| Redirect work in progress | [Steer, queue a follow-up, or stop the request](<https://github.com/docs/chat/chat-overview.md#send-messages-while-a-request-is-running>). Stopping does not undo completed actions. |
| Inspect or undo agent changes | [Review the diff, provide feedback, or restore a checkpoint](<https://github.com/docs/agents/run/review-code-edits.md>). Validate the result before integrating it. |
| Restrict terminal access to files and the network | Use [agent terminal sandboxing](<https://github.com/docs/agents/run/agent-sandboxing.md>) (Preview on macOS, Linux, and WSL2, Experimental on Windows). A Git worktree separates code changes, but is not a security boundary. |
| Ask AI to review my code | Use [code review smart actions](<https://github.com/docs/editing/copilot-smart-actions.md#review-code>). This generates review feedback, not a replacement for your review of agent changes. |

> [!IMPORTANT]
> Agents can save edits directly to the session's folder or worktree. **Manual permissions** does not ask for confirmation for every file edit. Review the [security baseline](<https://github.com/docs/agents/run/security.md#recommended-security-baseline>) before working on an existing project.

<a name="customize-your-chat-experience"></a>

## Customize your workflow

| I want to... | Use |
|---|---|
| Apply project conventions without repeating them | [Instructions](<https://github.com/docs/agent-customization/custom-instructions.md>) for coding standards, architecture, and test commands. |
| Package a workflow with supporting scripts or examples | [Agent skills](<https://github.com/docs/agent-customization/agent-skills.md>), loaded when relevant or invoked by name. |
| Run a saved prompt on demand | [Prompt files](<https://github.com/docs/agent-customization/prompt-files.md>), invoked as slash commands. |
| Define a specialized role and tool selection | [Custom agents](<https://github.com/docs/agent-customization/custom-agents.md>), such as a read-only reviewer. |
| Connect a database, issue tracker, or other external system | [MCP servers](<https://github.com/docs/agent-customization/mcp-servers.md>) that provide tools. |
| Run a command at a specific agent lifecycle event | [Hooks](<https://github.com/docs/agent-customization/hooks.md>), rather than relying on the model to follow an instruction. |
| Install a packaged set of customizations | [Agent plugins](<https://github.com/docs/agent-customization/agent-plugins.md>). |

Use the [Agent Customizations editor](<https://github.com/docs/agent-customization/overview.md#agent-customizations-editor>) to create and manage these options for your selected harness.

<a name="generate-tests"></a>

## Debug and fix problems

| Task | Starting point |
|---|---|
| Generate tests | Select code and use **Generate Code** > **Generate Tests**, or ask an agent to add and run tests with your project's framework. |
| Diagnose a failing test | Select **Fix Test Failure** in the Test Explorer, or attach the failure output to chat. |
| Fix a compiler or linting error | Use the editor Code Action (lightbulb), or attach the problem to chat. |
| Configure a debugging session | Ask for a debug configuration, or use `copilot-debug` (Preview) before a terminal run command, such as `copilot-debug python foo.py`. |

Combine the task, constraint, and verification in one request:

```prompt
Find the cause of the failing authentication test.
Fix the implementation without changing the expected behavior.
Run the relevant tests and summarize the change and results.
```

See [smart actions for tests and errors](<https://github.com/docs/editing/copilot-smart-actions.md#fix-testing-errors>) and [debugging](<https://github.com/docs/debugtest/debugging.md>).

<a name="terminal"></a>

## Essential keyboard shortcuts

| Action | Shortcut |
|---|---|
| Open the {% data variables.copilot.chat_view %} | `kb(workbench.action.chat.open)` |
| Start a new chat | `kb(workbench.action.chat.newChat)` |
| Open editor inline chat | `kb(inlineChat.start)` |
| Open [terminal inline chat](<https://github.com/docs/chat/inline-chat.md#use-terminal-inline-chat>) | `kb(workbench.action.terminal.chat.start)` |
| Open [Quick Chat](<https://github.com/docs/chat/inline-chat.md#use-quick-chat>) | `kb(workbench.action.quickchat.toggle)` |
| Accept an inline suggestion | `kb(editor.action.inlineSuggest.commit)` |
| Dismiss an inline suggestion | `kb(editor.action.inlineSuggest.hide)` |

For spoken interaction, see [Voice Mode](<https://github.com/docs/configure/accessibility/voice.md#use-voice-mode>) `feature(voice-mode)` and [built-in dictation](<https://github.com/docs/configure/accessibility/voice.md#use-built-in-dictation>) `feature(built-in-dictation)`.

## Chat reference

### Chat tools

Agents choose tools automatically. You do not need to name every tool in your prompt. Type `#` to browse available tools and context items, or reference a tool to direct the agent to a capability. See the [Tools and context reference](<https://github.com/docs/agents/reference/tools-reference.md>) for individual names.

### Slash commands

Type `/` to see commands available in the current session. Availability depends on the chat surface, harness, role, and enabled features.

| Command | Purpose |
|---|---|
| `/plan` | Research and propose an implementation plan. |
| `/explain`, `/fix`, `/tests` | Explain code, suggest a fix, or generate tests. |
| `/doc` | Generate documentation comments from editor inline chat. |
| `/setupTests` (Experimental) | Get help choosing and setting up a test framework. |
| `/new`, `/newNotebook` | Scaffold a workspace, file, or notebook. |
| `/compact`, `/fork` | Compact context, or branch the conversation with its history. |
| `/clear` | Start a new chat and archive or mark the current chat as done. |
| `/rename <name>`, `/help` | Rename a local chat, or list commands and agents in a local Ask chat. |
| `/models`, `/tools` | Open the model picker, or configure tools for a local chat. |
| `/init` | Generate or update workspace instructions in a local agent session. |
| `/agents`, `/instructions`, `/skills`, `/prompts`, `/hooks` | Configure the corresponding customization type. |
| `/create-agent`, `/create-instructions`, `/create-skill`, `/create-prompt`, `/create-hook` | Generate a customization in a local agent session. |
| `/troubleshoot` | Analyze agent debug logs in a local or {% data variables.copilot.copilot_cli_short %} session. |
| `/debug` | Open the Chat Debug view from the {% data variables.copilot.chat_view %}, not the {% data variables.copilot.agents_window %}. |
| `/sandbox-policy` | Inspect the [effective sandbox policy](<https://github.com/docs/agents/run/agent-sandboxing.md#inspect-the-effective-sandbox-policy>) for a {% data variables.product.prodname_copilot_short %} Agent Host session. |
| `/<name>` | Invoke an agent skill or reusable prompt by name. |

For example, a skill in `.github/skills/webapp-testing/SKILL.md` can be invoked with `/webapp-testing`. For permission and Autopilot commands, see [approvals and permissions](<https://github.com/docs/agents/run/approvals.md>).

### Chat participants

In chat surfaces that support participants, type `@` to choose a specialist. Use `@github` for repositories, issues, and pull requests, `@terminal` for shell questions, or `@vscode` for editor features and settings. For example, `@vscode /search` generates a Search view query.

## Next steps

* [Apply AI best practices to your project](<https://github.com/docs/agents/best-practices.md>).
* [Look up AI settings](<https://github.com/docs/agents/reference/ai-settings.md>).


</details>
