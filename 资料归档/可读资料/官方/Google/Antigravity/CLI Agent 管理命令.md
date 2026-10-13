# CLI Agent 管理命令

介绍通过 /agents 选择自定义角色、定义本地或全局 Agent，以及观察、检查和停止后台子任务。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/cli/commands/agents/>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.antigravity.google/docs/cli/commands/agents/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/CLI Agent 管理命令.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

Markdown 
 复制 Markdown 查看 Markdown 
 可用于：
 反重力 CLI

 
 
Agents 命令 (/agents) 标题为“Agents 命令 (/agents)”的部分 
 在自定义 agents 之间浏览、选择和切换，或直接在交互式环境中监视活动和已完成的后台子代理TUI面板。
 
开始之前 标题为“开始之前”的部分 
 确保满足以下先决条件： 
 安装 Antigravity CLI 。 
了解异步执行模型。
 
标题为“概述”的概述部分 
 /agents 命令打开交互式 Agent 管理器面板。此界面有两个不同的用途： 
 自定义 agent 选择和发现：在默认 agent 和自定义工作流程特定 agents 之间进行选择，或发现在本地和全局定义新 agents 的位置。 
 子代理监控和控制：跟踪、检查或终止在活动会话期间同时运行的后台子代理。

 
要打开TUI内部的面板，请输入/agents并按Enter键：
 /agents

自定义 agent 选择和发现 标题为“自定义 agent 选择和发现”
 反重力 CLI 的部分支持使用专门的系统指令和工具权限加载自定义 agent 定义。可用的 Agents 部分列出了当前会话可用的所有 agents。
 
在 agents 之间切换 标题为“在 agents 之间切换”的部分 
 要在面板中的 agents 之间切换，请使用以下控件： 
 选择：使用 ↑ / ↓ 突出显示 agent（默认） agent 或自定义 agent) 在可用 Agents 下，然后按 Enter 。 
 状态指示灯：绿色圆圈( ● ) 表示活动或准备状态agent。 
 应用并退出：按 Esc 关闭面板并应用您的选择。

 
 
创建自定义 agents 标题为“创建自定义 agents”的部分 
 /agents 面板的标题显示用于创建新自定义 agents 的模板位置： 
 创建新的 Agents 工作区： {workspace}/.agents/agents/{agent_name}/agent.md 全局：~/.gemini/config/agents/{agent_name}/agent.md 
 要创建可在所有工作区和项目中使用的自定义 agent，请将其放置在全局自定义目录 ( ~/.gemini/config/agents/ ) 下。创建一个与您的 agent 名称匹配的目录，并添加一个带有 YAML frontmatter 的 agent.md 文件： 
 mkdir -p ~/.gemini/config/agents/code-reviewer cat << 'EOF' > ~/.gemini/config/agents/code-reviewer/agent.md --- name:代码审查者描述：严格的代码审查专家，专注于边缘情况和安全性。 --- 您是一位专家代码审查员。仔细分析差异并验证边缘情况。 EOF 
 当您重新打开 /agents 时，CLI 会自动发现代码审查器并将其列在可用 Agents 下。如果您需要严格限制在单个项目存储库范围内的 agent，请将其放置在该工作区的 .agents/agents/ 目录中（例如 /home/user/projects/my-app/.agents/agents/code-reviewer/agent.md ）。您还可以在插件中打包和分发自定义agents。
 
子代理监视和控制 标题为“子代理监视和控制”的部分 
 当您的主 agent 委派任务（例如运行测试或查询大型代码库）时，生成的线程将显示在 Subagents 下的 /agents 面板中，并按其触发提示进行分组。
 
检查子代理进度 标题为“检查子代理进度”的部分 
 使用以下控件监视子代理状态和日志： 
 组切换：在子代理组标题上按 Enter 键 ( ▸ 子代理（1 运行，2 完成））以展开或折叠 ( ▾ )组。 
 状态指示器：每个子代理行显示实时生命周期状态：
 
running ：主动执行工具或生成推理步骤。 
 完成：完成分配的后台任务。 
 错误：执行期间遇到终端故障。 
 Killed ：由您或父进程手动终止。 
 详细信息视图：突出显示特定子代理行并按 Enter 键打开全屏子代理详细信息视图。该视图显示子代理完整的内部思想、工具调用和执行标准输出。按 Esc 键返回列表。
 
终止活动子代理 标题为“终止活动子代理”的部分 
 如果后台子代理循环或运行时间超过所需时间，您可以立即终止它，而无需离开会话： 
 打开 /agents 并突出显示正在运行的子代理行。 
 按 K 终止活动子代理及其所有子线程。
 
内联工具批准 标题为“内联工具批准”的部分 
 如果子代理尝试执行受保护的操作（例如修改文件或在沙盒环境中运行 shell 命令），授权提示会内联显示在 /agents 面板中。您可以直接从列表中按 A 批准或按 D 拒绝。
 
面板键绑定参考 标题为“面板键绑定参考”的部分 
 当聚焦在 /agents 面板内时，应用以下键盘快捷键： 
 按键操作行为 
 ↑ / ↓ 导航 在标题、子代理和可用之间移动 cursor agents。 
 输入选择/切换 Expand/collapse 组，打开子代理详细信息视图，或选择自定义 agent。 
 K 终止活动子代理 立即取消 (CancelSubagent) 突出显示的正在运行的子代理。 
 Esc Go 返回 退出面板，返回提示框，应用任何准备好的agent开关。
 
常见错误 标题为“常见错误”的部分 
 错误 失败原因 修复 
 期望自定义 agent 开关修改回合历史记录 切换 agents 分叉对话以保留历史完整性 Continue 新分叉会话中的工作流程 
 放置直接在配置根目录中的 agent 文件 扫描程序专门查看 agents/ 目录中的内容 将定义移动到 .agents/agents/<name>/agent.md 
 在已完成的子代理上按 K 仅针对活动（正在运行）的子代理进程 按 Enter 来检查已完成的日志
 
后续步骤 标题为“后续步骤”的部分 
 浏览以下指南以了解有关子代理和权限的更多信息： 
 后台任务和子代理：了解有关多线程异步执行架构的更多信息。 
 插件和技能：了解如何将自定义 agents、技能和 MCP 配置捆绑到可共享插件中。 
 权限和沙箱：为后台子代理配置安全护栏和审批规则。


## 原文对照

<details>
<summary>展开完整原文</summary>

Markdown 
 Copy Markdown View Markdown 
 Available on: 
 Antigravity CLI 

 
 
 Agents command (/agents) Section titled “Agents command (/agents)” 
 Browse, select, and switch between custom agents, or monitor active and completed background subagents directly inside an interactive TUI panel. 
 
 Before you begin Section titled “Before you begin” 
 Make sure you meet the following prerequisites: 
 Install Antigravity CLI . 
 Understand the asynchronous execution model . 
 
 Overview Section titled “Overview” 
 The /agents command opens the interactive Agent Manager panel. This interface serves two distinct purposes: 
 Custom agent selection and discovery : Choose between the default agent and custom workflow-specific agents, or discover where to define new agents locally and globally. 
 Subagent monitoring and control : Track, inspect, or terminate background subagents running concurrently during your active session. 

 
 To open the panel inside the TUI, type /agents and press Enter : 
 /agents 

 Custom agent selection and discovery Section titled “Custom agent selection and discovery” 
 Antigravity CLI supports loading custom agent definitions with specialized system instructions and tool permissions. The Available Agents section lists all agents currently available to your session. 
 
 Switching between agents Section titled “Switching between agents” 
 To switch between agents in the panel, use the following controls: 
 Select : Use ↑ / ↓ to highlight an agent ( Default agent or a custom agent) under Available Agents , then press Enter . 
 Status indicator : A green circle ( ● ) indicates the active or prepared agent. 
 Apply and exit : Press Esc to close the panel and apply your selection. 

 
 
 Creating custom agents Section titled “Creating custom agents” 
 The header of the /agents panel displays template locations for creating new custom agents: 
 Create New Agents Workspace: {workspace}/.agents/agents/{agent_name}/agent.md Global: ~/.gemini/config/agents/{agent_name}/agent.md 
 To create a custom agent that is available across all your workspaces and projects, place it under your global customization directory ( ~/.gemini/config/agents/ ). Create a directory matching your agent name and add an agent.md file with YAML frontmatter: 
 mkdir -p ~/.gemini/config/agents/code-reviewer cat << 'EOF' > ~/.gemini/config/agents/code-reviewer/agent.md --- name: code-reviewer description: Rigorous code review specialist focusing on edge cases and security. --- You are an expert code reviewer. Analyze diffs carefully and verify edge cases. EOF 
 When you reopen /agents , the CLI automatically discovers code-reviewer and lists it under Available Agents . If you need an agent scoped strictly to a single project repository, place it inside that workspace’s .agents/agents/ directory (for example, /home/user/projects/my-app/.agents/agents/code-reviewer/agent.md ). You can also package and distribute custom agents inside plugins . 
 
 Subagent monitoring and control Section titled “Subagent monitoring and control” 
 When your primary agent delegates tasks (such as running tests or querying large codebases), the spawned threads appear in the /agents panel under Subagents , grouped by their triggering prompt. 
 
 Inspecting subagent progress Section titled “Inspecting subagent progress” 
 Use the following controls to monitor subagent status and logs: 
 Group toggling : Press Enter on a subagent group header ( ▸ Subagents (1 running, 2 done) ) to expand or collapse ( ▾ ) that group. 
 Status indicators : Each subagent row displays a live lifecycle state:
 
 running : Actively executing tools or generating reasoning steps. 
 done : Completed its assigned background task. 
 error : Encountered a terminal failure during execution. 
 killed : Terminated manually by you or the parent process. 
 Detail view : Highlight a specific subagent row and press Enter to open the full-screen Subagent Detail View . This view displays the subagent’s complete internal thoughts, tool calls, and execution stdout. Press Esc to return to the list. 
 
 Terminating active subagents Section titled “Terminating active subagents” 
 If a background subagent loops or runs longer than needed, you can terminate it immediately without leaving your session: 
 Open /agents and highlight the running subagent row. 
 Press K to terminate the active subagent and all its child threads. 
 
 Inline tool approvals Section titled “Inline tool approvals” 
 If a subagent attempts a protected operation (such as modifying a file or running a shell command in a sandboxed environment), the authorization prompt displays inline in the /agents panel. You can press A to approve or D to deny directly from the list. 
 
 Panel keybindings reference Section titled “Panel keybindings reference” 
 When focused inside the /agents panel, the following keyboard shortcuts apply: 
 Key Action Behavior 
 ↑ / ↓ Navigate Move the cursor between headers, subagents, and available agents. 
 Enter Select / Toggle Expand/collapse groups, open Subagent Detail View, or select a custom agent. 
 K Kill Active Subagent Instantly cancel ( CancelSubagent ) the highlighted running subagent. 
 Esc Go Back Exit the panel, return to the prompt box, and apply any prepared agent switch. 
 
 Common mistakes Section titled “Common mistakes” 
 Mistake Why it fails Fix 
 Expecting custom agent switches to modify turn history Switching agents forks the conversation to preserve historical integrity Continue your workflow in the newly forked session 
 Placing agent files directly in config root Scanner looks specifically inside agents/ directories Move definition to .agents/agents/<name>/agent.md 
 Pressing K on completed subagents Only targets active ( running ) subagent processes Press Enter to inspect completed logs instead 
 
 Next steps Section titled “Next steps” 
 Explore the following guides to learn more about subagents and permissions: 
 Background tasks and subagents : Learn more about the multi-threaded asynchronous execution architecture. 
 Plugins and skills : Discover how to bundle custom agents, skills, and MCP configurations into shareable plugins. 
 Permissions and sandbox : Configure security guardrails and approval rules for background subagents.


</details>
