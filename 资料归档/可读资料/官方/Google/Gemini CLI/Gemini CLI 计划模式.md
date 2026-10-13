# Gemini CLI 计划模式

介绍只读研究和设计、协作编辑计划、退出后实现的流程，说明工具限制及计划模式的配置。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 文档时间：更新：[2026-06-18](<https://geminicli.com/docs/cli/plan-mode/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://geminicli.com/docs/cli/plan-mode/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 计划模式.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 计划模式

计划模式是一个只读环境，用于在
实施之前构建强大的解决方案。使用计划模式，您可以：

* **研究：**以只读状态探索项目，防止意外
变化。
* **设计：**了解问题、评估权衡并选择解决方案。
* **计划：**在修改任何代码之前对齐执行策略。

默认情况下启用计划模式。您可以使用
`/settings`命令管理此设置。

## 如何进入计划模式

[标题为“如何进入计划模式”的部分](<#如何进入计划模式>)

计划模式无缝集成到您的工作流程中，让您根据需要在
计划和执行之间切换。

您可以将 Gemini CLI 配置为默认在计划模式下启动，也可以在会话期间手动进入 
 计划模式。

### 以计划模式启动

[标题为“以计划模式启动”的部分](<#以计划模式启动>)

默认情况下直接在计划模式下启动Gemini CLI：

1. 使用`/settings`命令。
2. 将**默认审批模式**设置为`Plan`。

要在计划模式下启动 Gemini CLI 一次：

1. 启动Gemini CLI时使用`gemini --approval-mode=plan`。

### 手动进入计划模式

[标题为“手动进入计划模式”的部分](<#手动进入计划模式>)

使用 Gemini CLI 时启动计划模式：

* **键盘快捷键：** 按`Shift+Tab`循环审批模式
（`Default` -> `Auto-Edit` -> `Plan`）。当 Gemini CLI 正在主动处理或显示确认 
 对话框时，计划模式会自动从 
 轮换中删除。
* **命令：** 在输入框中键入`/plan [goal]`。 `[goal]`为可选；
例如，`/plan implement authentication` 将切换到计划模式，
 立即将提示提交给模型。
* **自然语言：**要求Gemini CLI“开始制定……计划”。 Gemini CLI
调用
[`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>)工具
进行模式切换。当Gemini CLI处于
[YOLO模式](<https://geminicli.com/docs/reference/configuration#command-line-arguments>)时，此工具不可用。

## 如何使用计划模式

[标题为“如何使用计划模式”的部分](<#如何使用计划模式>)

计划模式可让您在 
Gemini CLI 采取行动之前与 Gemini CLI 协作设计解决方案。

1. **提供一个目标：**首先描述您想要实现的目标。 Gemini CLI
然后将进入计划模式（如果还没有）来研究任务。
2. **讨论并商定策略：**当Gemini CLI分析您的代码库时，它
   将与您讨论其调查结果和拟议策略，以确保
   对齐。它可能会问您问题或呈现不同的实现
   选项使用 [`ask_user`](<https://geminicli.com/docs/tools/ask-user>). **Gemini CLI 将停止并且
   等待您的确认** 在起草正式计划之前。你应该
   在继续之前就该方法达成非正式协议。
3. **审查计划：**一旦您同意该策略，Gemini CLI将创建
详细的实施计划作为计划目录中的 Markdown 文件。

   * **查看：**您可以打开并阅读此文件以了解建议
变化。
   * **编辑：**按`Ctrl+X`直接在您配置的中打开计划
外部编辑器。
4. **批准或迭代：** Gemini CLI将为您呈现最终的计划
正式批准。

   * **批准：**如果您对计划感到满意，请批准它以启动
立即实施：**是，自动接受编辑**或**是，
手动接受编辑**。
   * **迭代：**如果计划需要调整，请在输入中提供反馈
框或[直接编辑计划文件](<#协同计划编辑>)。 Gemini
     CLI将细化策略并更新计划。
   * **取消：**您可以通过`Esc`取消您的计划。

对于更复杂或专业的规划任务，您可以
[通过技能](<#具有技能的定制规划>)自定义规划工作流程。

### 协同计划编辑

[标题为“协作计划编辑”的部分](<#协同计划编辑>)

您可以通过直接更改或在实施计划中留下评论
来与Gemini CLI协作。这通常比
用自然语言描述复杂的变化更快、更精确。

1. **打开计划：** 当Gemini CLI呈现计划时按`Ctrl+X`
审查。
2. **编辑或评论：** 该计划在您配置的外部编辑器中打开（对于
例如，VS Code 或 Vim）。您可以：
   * **修改步骤：**直接重新排序、删除或重写实现
步骤。
   * **留下评论：**添加内联问题或反馈（例如，“等等，
我们不应该在这里使用现有的 `Logger` 类吗？”）。
3. **保存并关闭：**保存更改并关闭编辑器。
4. **审核和细化：** Gemini CLI自动检测更改、审核
听取您的意见，并调整实施策略。然后它会呈现
完善的计划供您最终批准。

## 如何退出计划模式

[标题为“如何退出计划模式”的部分](<#如何退出计划模式>)

您可以随时退出计划模式，无论您是已确定计划还是想要
切换回其他模式。

* **批准计划：**当Gemini CLI提出最终计划时，批准它
自动退出计划模式并开始实施。
* **键盘快捷键：** 按`Shift+Tab`循环至所需模式。
* **自然语言：**要求Gemini CLI“退出计划模式”或“停止计划”。

## 工具限制

[标题为“工具限制”的部分](<#工具限制>)

计划模式执行严格的安全策略以防止意外更改。

这些是唯一允许的工具：

* **FileSystem（读取）：**
  [`read_file`](<https://geminicli.com/docs/tools/file-system#2-read_file-readfile>),
  [`list_directory`](<https://geminicli.com/docs/tools/file-system#1-list_directory-readfolder>),
  [`glob`](<https://geminicli.com/docs/tools/file-system#4-glob-findfiles>)
* **搜索：** [`grep_search`](<https://geminicli.com/docs/tools/file-system#5-grep_search-searchtext>),
[`google_web_search`](<https://geminicli.com/docs/tools/web-search>)、
 [`web_fetch`](<https://geminicli.com/docs/tools/web-fetch>)（需要明确确认）、
 [`get_internal_docs`](<https://geminicli.com/docs/tools/internal-docs>)
* **研究子代理：**
  [`codebase_investigator`](<https://geminicli.com/docs/core/subagents#codebase-investigator>),
  [`cli_help`](<https://geminicli.com/docs/core/subagents#cli-help-agent>)
* **互动：** [`ask_user`](<https://geminicli.com/docs/tools/ask-user>)
* **MCP 工具（读取）：** 只读 [MCP 工具](<https://geminicli.com/docs/tools/mcp-server>)（用于
例如，允许使用 `github_read_issue`、`postgres_read_schema`) 和 core
 [MCP 资源工具 ](<https://geminicli.com/docs/tools/mcp-resources>) (`list_mcp_resources`、
 `read_mcp_resource`)。
* **规划（写入）：**
  [`write_file`](<https://geminicli.com/docs/tools/file-system#3-write_file-writefile>) 和
  [`replace`](<https://geminicli.com/docs/tools/file-system#6-replace-edit>) 只允许 `.md`
  文件在 `~/.gemini/tmp/<project>/<session-id>/plans/` 目录或您的
  [自定义计划目录](<#自定义计划目录和政策>).
* **技能：** [`activate_skill`](<https://geminicli.com/docs/cli/skills>)（允许加载专门的
说明和资源以只读方式）

## 定制和最佳实践

[标题为“定制和最佳实践”的部分](<#定制和最佳实践>)

默认情况下，计划模式是安全的，但您可以对其进行调整以适应您的特定
工作流程。您可以通过使用技能、调整
安全策略、更改计划存储位置或添加挂钩来自定义Gemini CLI计划的方式。

### 具有技能的定制规划

[标题为“利用技能进行定制规划”的部分](<#具有技能的定制规划>)

您可以使用 [Agent Skills](<https://geminicli.com/docs/cli/skills>) 自定义 Gemini CLI
 如何规划特定类型的任务。在计划模式下激活技能
时，其专门指令和程序工作流程将
指导研究、设计和规划阶段。

例如：

* **“数据库迁移”**技能可以确保计划包括数据安全
检查和回滚策略。
* **“安全审核”** 技能可能会提示 Gemini CLI 寻找特定的
代码库探索期间的漏洞。
* **“前端设计”**技能可以指导Gemini CLI使用特定的UI
其提案中的组件和可访问性标准。

要在计划模式下使用技能，您可以明确要求Gemini CLI“使用
`<skill-name>`技能来计划……”或者Gemini CLI可以根据任务描述自动激活它
。

### 定制政策

[标题为“自定义政策”的部分](<#定制政策>)

计划模式的默认工具限制由
[策略引擎](<https://geminicli.com/docs/reference/policy-engine>)管理，并在内置
[`plan.toml`](<https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/policy/policies/plan.toml>)文件中定义。内置策略（层 1）强制执行只读状态，
，但您可以通过在 
`~/.gemini/policies/` 目录（层 2）中创建自己的策略来自定义这些规则。

#### 全局规则与特定模式规则

[标题为“全局规则与特定模式规则”的部分](<#全局规则与特定模式规则>)

如 
[ 策略引擎文档 ](<https://geminicli.com/docs/reference/policy-engine#approval-modes>) 中所述，任何未明确指定 `modes` 的 
 规则均被视为“始终活动”，并且 
 也将适用于计划模式。

为了保持计划模式作为安全研究环境的完整性，
持久工具批准是上下文感知的。在
默认或自动编辑等模式中授予的批准不适用于计划模式，确保
实施信任的工具在您研究时不会自动执行。然而，在计划模式下授予的
批准被视为
全局信任的有意选择，并适用于所有模式。

如果您想要手动将规则限制为其他模式，但 * 不 * 为计划模式，
您必须显式指定目标模式。例如，要在默认和自动编辑模式下允许 `npm test`
，但在计划模式下不允许：

```
[[rule]]


toolName = "run_shell_command"


commandPrefix = "npm test"


decision = "allow"


priority = 100


# By omitting "plan", this rule will not be active in Plan Mode.


modes = ["default", "autoEdit"]
```

#### 示例：自动批准只读 MCP 工具

[标题为“示例：自动批准只读 MCP 工具”的部分](<#示例自动批准只读-mcp-工具>)

默认情况下，只读 MCP 工具需要用户在计划模式下确认。您可以
使用`toolAnnotations`和`mcpName`通配符为
您的特定环境自定义此行为。

`~/.gemini/policies/mcp-read-only.toml`

```
[[rule]]


toolName = "*"


mcpName = "*"


toolAnnotations = { readOnlyHint = true }


decision = "allow"


priority = 100


modes = ["plan"]
```

有关策略引擎如何工作的更多信息，请参阅
[策略引擎](<https://geminicli.com/docs/reference/policy-engine>)文档。

#### 示例：在计划模式下允许 git 命令

[标题为“示例：在计划模式下允许 git 命令”的部分](<#示例在计划模式下允许-git-命令>)

此规则允许您在计划
模式下检查存储库状态并查看更改。

`~/.gemini/policies/git-research.toml`

```
[[rule]]


toolName = "run_shell_command"


commandPrefix = ["git status", "git diff"]


decision = "allow"


priority = 100


modes = ["plan"]
```

#### 示例：在计划模式下启用自定义子代理

[标题为“示例：在计划模式下启用自定义子代理”的部分](<#示例在计划模式下启用自定义子代理>)

内置研究 [分代理](<https://geminicli.com/docs/core/subagents>) 喜欢
[`codebase_investigator`](<https://geminicli.com/docs/core/subagents#codebase-investigator>) 和
[`cli_help`](<https://geminicli.com/docs/core/subagents#cli-help-agent>) 计划中默认启用
模式。您可以启用额外的
[定制子代理](<https://geminicli.com/docs/core/subagents#creating-custom-subagents>) 通过添加一个
遵守您的政策。

`~/.gemini/policies/research-subagents.toml`

```
[[rule]]


toolName = "my_custom_subagent"


decision = "allow"


priority = 100


modes = ["plan"]
```

告诉Gemini CLI它可以在提示中使用这些工具，例如：*“您可以
检查git中正在进行的更改。”*

### 自定义计划目录和政策

[标题为“自定义计划目录和政策”的部分](<#自定义计划目录和政策>)

默认情况下，规划工件存储在项目外部的托管临时目录 
 中：`~/.gemini/tmp/<project>/<session-id>/plans/`。

您可以为`settings.json`中的计划配置自定义目录。对于 
 示例，要将计划存储在项目内的 `.gemini/plans` 目录中：

```
{


"general": {


"plan": {


"directory": ".gemini/plans"


}


}


}
```

为了维护计划模式的安全，用户配置的计划
目录路径仅限于项目根目录。这可确保项目工作区中定义的自定义规划
位置无法用于转义和
覆盖其他地方的敏感文件。任何用户配置的目录必须驻留在
项目边界内。

使用自定义目录需要更新您的 
[ 策略引擎 ](<https://geminicli.com/docs/reference/policy-engine>) 配置以允许 
`write_file` 和 `replace` 位于该特定位置。例如，要允许
写入项目中的`.gemini/plans`目录，请在`~/.gemini/policies/plan-custom-directory.toml`处创建一个策略
文件：

```
[[rule]]


toolName = ["write_file", "replace"]


decision = "allow"


priority = 100


modes = ["plan"]


# Adjust the pattern to match your custom directory.


# This example matches any .md file in a .gemini/plans directory within the project.


argsPattern = "\"file_path\":\"[^\"]+[\\\\/]+\\.gemini[\\\\/]+plans[\\\\/]+[\\w-]+\\.md\""
```

### 在计划模式下使用钩子

[标题为“在计划模式下使用挂钩”的部分](<#在计划模式下使用钩子>)

您可以使用 [ 挂钩系统 ](<https://geminicli.com/docs/hooks/writing-hooks>) 自动执行部分 
 计划工作流程，或在 Gemini CLI 将 
 转换为计划模式或退出计划模式时强制执行额外检查。

可以配置诸如 `BeforeTool` 或 `AfterTool` 之类的挂钩来拦截 
`enter_plan_mode` 和 `exit_plan_mode` 工具调用。

#### 示例：将批准的计划存档至 GCS (`AfterTool`)

[标题为“示例：将批准的计划存档至 GCS (AfterTool)”的部分](<#示例将批准的计划存档至-gcs-aftertool>)

如果您的组织策略需要记录所有执行计划，则只要 Gemini CLI 退出计划模式以开始实施，您就可以
使用 `AfterTool` 挂钩将计划工件安全地复制到 Google 云
存储。

**`.gemini/hooks/archive-plan.sh`:**

```
#!/usr/bin/env bash


# Extract the plan filename from the tool input JSON


plan_filename=$(jq -r '.tool_input.plan_filename // empty')


# Construct the absolute path using the GEMINI_PLANS_DIR environment variable


plan_path="$GEMINI_PLANS_DIR/$plan_filename"


if [ -f "$plan_path" ]; then


# Generate a unique filename using a timestamp


filename="$(date +%s)_$(basename "$plan_path")"


# Upload the plan to GCS in the background so it doesn't block the CLI


gsutil cp "$plan_path" "gs://my-audit-bucket/gemini-plans/$filename" > /dev/null 2>&1 &


fi


# AfterTool hooks should generally allow the flow to continue


echo '{"decision": "allow"}'
```

要注册此 `AfterTool` 挂钩，请将其添加到您的 `settings.json`：

```
{


"hooks": {


"AfterTool": [


{


"matcher": "exit_plan_mode",


"hooks": [


{


"name": "archive-plan",


"type": "command",


"command": "~/.gemini/hooks/archive-plan.sh"


}


]


}


]


}


}
```

## 命令

[标题为“命令”的部分](<#命令>)

* **`/plan copy`**：将当前批准的计划复制到剪贴板。

## 规划工作流程

[标题为“规划工作流程”的部分](<#规划工作流程>)

计划模式为结构化研究和设计提供构建模块。这些是
实施为 [扩展](<https://geminicli.com/docs/extensions>) 使用核心规划工具
喜欢 [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>),
[`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>), 和
[`ask_user`](<https://geminicli.com/docs/tools/ask-user>).

### 内置规划工作流程

[标题为“内置规划工作流程”的部分](<#内置规划工作流程>)

内置规划器使用自适应工作流程来分析您的项目、咨询
你通过权衡 [`ask_user`](<https://geminicli.com/docs/tools/ask-user>)，并起草一份计划
您的认可。

### 定制规划工作流程

[标题为“自定义规划工作流程”的部分](<#定制规划工作流程>)

您可以安装或创建专门的规划器来适合您的工作流程。

#### 指挥

[标题为“指挥”的部分](<#指挥>)

[Conductor](<https://github.com/gemini-cli-extensions/conductor>) 专为规范驱动开发而设计。它将工作组织到
“轨道”中，并将持久工件存储在项目的`conductor/`
目录中：

* **自动转换：**通过切换到只读模式
  [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>).
* **简化决策：**使用[`ask_user`](<https://geminicli.com/docs/tools/ask-user>)
架构选择。
* **维护项目上下文：**使用以下命令将工件存储在项目目录中
[自定义计划目录和策略](<#自定义计划目录和政策>)。
* **切换执行：**通过
  [`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>).

#### 建立你自己的

[标题为“构建您自己的”的部分](<#建立你自己的>)

由于计划模式是基于模块化构建块构建的，因此您可以开发自己的 
 自定义计划工作流程作为 [ 扩展 ](<https://geminicli.com/docs/extensions>)。通过
利用核心工具和[自定义策略](<#定制政策>)，您可以定义
如何Gemini CLI研究和存储您的特定域的计划。

要构建自定义规划工作流程，您可以使用：

* **工具使用：**使用核心工具，如
[`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>)、
 [`ask_user`](<https://geminicli.com/docs/tools/ask-user>) 和 
 [`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>) 至 
 管理研究和设计过程。
* **自定义：**使用以下命令设置您自己的存储位置和策略规则
[自定义计划目录](<#自定义计划目录和政策>)和
[自定义策略](<#定制政策>)。

通过使用计划模式作为其执行环境，您的自定义方法可以
在设计阶段强制执行只读安全，同时受益于
高推理模型路由。

## 自动模型路由

[标题为“自动模型路由”的部分](<#自动模型路由>)

当使用 [汽车模型](<https://geminicli.com/docs/reference/configuration#model>), Gemini CLI
自动优化 [模型路由](<https://geminicli.com/docs/cli/telemetry#model-routing>) 基于
关于您当前阶段的任务：

1. **规划阶段：**在计划模式下，CLI将请求路由到
高推理 **Pro** 模型可确保稳健的架构决策和 
 高质量计划。
2. **实施阶段：**一旦计划获得批准并且您退出计划模式，
CLI检测到批准的计划是否存在，并自动
切换到高速**闪存**模型。这在计划的实施过程中提供了更快、更
响应的体验。

如果高推理模型不可用或您无权访问它，
Gemini CLI 会自动静默地回退到更快的模型，以确保 
 您的工作流程不会中断。

默认情况下启用此行为，以提供质量和
性能的最佳平衡。您可以在设置中禁用此自动切换：

```
{


"general": {


"plan": {


"modelRouting": false


}


}


}
```

## 清理

[标题为“清理”的部分](<#清理>)

默认情况下，Gemini CLI 会自动清理旧会话数据，包括所有
 关联的计划文件和任务跟踪器。

* **默认行为：** 会话（及其计划）保留 **30 天 **。
* **配置：**您可以通过`/settings`命令自定义此行为
  （搜索 **启用会话清理** 或 **保留聊天记录**）或在你的
  `settings.json` 文件。参见
  [会话保留](<https://geminicli.com/docs/cli/session-management#session-retention>) 了解更多
  详细信息。

手动删除还会删除所有关联的工件：

* **命令行：**使用`gemini --delete-session <index|id>`。
* **会话浏览器：** 按 `/resume`，导航至会话，然后按 `x`。

如果您使用 [ 自定义计划目录 ](<#自定义计划目录和政策>)、
，这些文件不会自动删除，必须手动管理。

## 非交互式执行

[标题为“非交互式执行”的部分](<#非交互式执行>)

在非交互式环境（例如无头 
 脚本或 CI/CD 管道）中运行 Gemini CLI 时，计划模式会针对自动化工作流程进行优化：

* **自动转换：**策略引擎自动批准
`enter_plan_mode` 和 `exit_plan_mode` 工具，不提示用户 
 确认。
* **自动执行：**退出计划模式执行计划时，
Gemini CLI自动切换到
[YOLO模式](<https://geminicli.com/docs/reference/policy-engine#approval-modes>)而不是
标准默认模式。这允许CLI自动执行实施步骤
，而无需等待交互式工具批准。

**示例：**

终端窗口

```
gemini --approval-mode plan -p "Analyze telemetry and suggest improvements"
```

最后更新：六月 18、2026


## 原文对照

<details>
<summary>展开完整原文</summary>

# Plan Mode

Plan Mode is a read-only environment for architecting robust solutions before
implementation. With Plan Mode, you can:

* **Research:** Explore the project in a read-only state to prevent accidental
  changes.
* **Design:** Understand problems, evaluate trade-offs, and choose a solution.
* **Plan:** Align on an execution strategy before any code is modified.

Plan Mode is enabled by default. You can manage this setting using the
`/settings` command.

## How to enter Plan Mode

[Section titled “How to enter Plan Mode”](<#how-to-enter-plan-mode>)

Plan Mode integrates seamlessly into your workflow, letting you switch between
planning and execution as needed.

You can either configure Gemini CLI to start in Plan Mode by default or enter
Plan Mode manually during a session.

### Launch in Plan Mode

[Section titled “Launch in Plan Mode”](<#launch-in-plan-mode>)

To start Gemini CLI directly in Plan Mode by default:

1. Use the `/settings` command.
2. Set **Default Approval Mode** to `Plan`.

To launch Gemini CLI in Plan Mode once:

1. Use `gemini --approval-mode=plan` when launching Gemini CLI.

### Enter Plan Mode manually

[Section titled “Enter Plan Mode manually”](<#enter-plan-mode-manually>)

To start Plan Mode while using Gemini CLI:

* **Keyboard shortcut:** Press `Shift+Tab` to cycle through approval modes
  (`Default` -> `Auto-Edit` -> `Plan`). Plan Mode is automatically removed from
  the rotation when Gemini CLI is actively processing or showing confirmation
  dialogs.
* **Command:** Type `/plan [goal]` in the input box. The `[goal]` is optional;
  for example, `/plan implement authentication` will switch to Plan Mode and
  immediately submit the prompt to the model.
* **Natural Language:** Ask Gemini CLI to “start a plan for…”. Gemini CLI
  calls the
  [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>) tool
  to switch modes. This tool is not available when Gemini CLI is in
  [YOLO mode](<https://geminicli.com/docs/reference/configuration#command-line-arguments>).

## How to use Plan Mode

[Section titled “How to use Plan Mode”](<#how-to-use-plan-mode>)

Plan Mode lets you collaborate with Gemini CLI to design a solution before
Gemini CLI takes action.

1. **Provide a goal:** Start by describing what you want to achieve. Gemini CLI
   will then enter Plan Mode (if it’s not already) to research the task.
2. **Discuss and agree on strategy:** As Gemini CLI analyzes your codebase, it
   will discuss its findings and proposed strategy with you to ensure
   alignment. It may ask you questions or present different implementation
   options using [`ask_user`](<https://geminicli.com/docs/tools/ask-user>). **Gemini CLI will stop and
   wait for your confirmation** before drafting the formal plan. You should
   reach an informal agreement on the approach before proceeding.
3. **Review the plan:** Once you’ve agreed on the strategy, Gemini CLI creates
   a detailed implementation plan as a Markdown file in your plans directory.

   * **View:** You can open and read this file to understand the proposed
     changes.
   * **Edit:** Press `Ctrl+X` to open the plan directly in your configured
     external editor.
4. **Approve or iterate:** Gemini CLI will present the finalized plan for your
   formal approval.

   * **Approve:** If you’re satisfied with the plan, approve it to start the
     implementation immediately: **Yes, automatically accept edits** or **Yes,
     manually accept edits**.
   * **Iterate:** If the plan needs adjustments, provide feedback in the input
     box or [edit the plan file directly](<#collaborative-plan-editing>). Gemini
     CLI will refine the strategy and update the plan.
   * **Cancel:** You can cancel your plan with `Esc`.

For more complex or specialized planning tasks, you can
[customize the planning workflow with skills](<#custom-planning-with-skills>).

### Collaborative plan editing

[Section titled “Collaborative plan editing”](<#collaborative-plan-editing>)

You can collaborate with Gemini CLI by making direct changes or leaving comments
in the implementation plan. This is often faster and more precise than
describing complex changes in natural language.

1. **Open the plan:** Press `Ctrl+X` when Gemini CLI presents a plan for
   review.
2. **Edit or comment:** The plan opens in your configured external editor (for
   example, VS Code or Vim). You can:
   * **Modify steps:** Directly reorder, delete, or rewrite implementation
     steps.
   * **Leave comments:** Add inline questions or feedback (for example, “Wait,
     shouldn’t we use the existing `Logger` class here?”).
3. **Save and close:** Save your changes and close the editor.
4. **Review and refine:** Gemini CLI automatically detects the changes, reviews
   your comments, and adjusts the implementation strategy. It then presents the
   refined plan for your final approval.

## How to exit Plan Mode

[Section titled “How to exit Plan Mode”](<#how-to-exit-plan-mode>)

You can exit Plan Mode at any time, whether you have finalized a plan or want to
switch back to another mode.

* **Approve a plan:** When Gemini CLI presents a finalized plan, approving it
  automatically exits Plan Mode and starts the implementation.
* **Keyboard shortcut:** Press `Shift+Tab` to cycle to the desired mode.
* **Natural language:** Ask Gemini CLI to “exit plan mode” or “stop planning.”

## Tool Restrictions

[Section titled “Tool Restrictions”](<#tool-restrictions>)

Plan Mode enforces strict safety policies to prevent accidental changes.

These are the only allowed tools:

* **FileSystem (Read):**
  [`read_file`](<https://geminicli.com/docs/tools/file-system#2-read_file-readfile>),
  [`list_directory`](<https://geminicli.com/docs/tools/file-system#1-list_directory-readfolder>),
  [`glob`](<https://geminicli.com/docs/tools/file-system#4-glob-findfiles>)
* **Search:** [`grep_search`](<https://geminicli.com/docs/tools/file-system#5-grep_search-searchtext>),
  [`google_web_search`](<https://geminicli.com/docs/tools/web-search>),
  [`web_fetch`](<https://geminicli.com/docs/tools/web-fetch>) (requires explicit confirmation),
  [`get_internal_docs`](<https://geminicli.com/docs/tools/internal-docs>)
* **Research Subagents:**
  [`codebase_investigator`](<https://geminicli.com/docs/core/subagents#codebase-investigator>),
  [`cli_help`](<https://geminicli.com/docs/core/subagents#cli-help-agent>)
* **Interaction:** [`ask_user`](<https://geminicli.com/docs/tools/ask-user>)
* **MCP tools (Read):** Read-only [MCP tools](<https://geminicli.com/docs/tools/mcp-server>) (for
  example, `github_read_issue`, `postgres_read_schema`) and core
  [MCP resource tools](<https://geminicli.com/docs/tools/mcp-resources>) (`list_mcp_resources`,
  `read_mcp_resource`) are allowed.
* **Planning (Write):**
  [`write_file`](<https://geminicli.com/docs/tools/file-system#3-write_file-writefile>) and
  [`replace`](<https://geminicli.com/docs/tools/file-system#6-replace-edit>) only allowed for `.md`
  files in the `~/.gemini/tmp/<project>/<session-id>/plans/` directory or your
  [custom plans directory](<#custom-plan-directory-and-policies>).
* **Skills:** [`activate_skill`](<https://geminicli.com/docs/cli/skills>) (allows loading specialized
  instructions and resources in a read-only manner)

## Customization and best practices

[Section titled “Customization and best practices”](<#customization-and-best-practices>)

Plan Mode is secure by default, but you can adapt it to fit your specific
workflows. You can customize how Gemini CLI plans by using skills, adjusting
safety policies, changing where plans are stored, or adding hooks.

### Custom planning with skills

[Section titled “Custom planning with skills”](<#custom-planning-with-skills>)

You can use [Agent Skills](<https://geminicli.com/docs/cli/skills>) to customize how Gemini CLI
approaches planning for specific types of tasks. When a skill is activated
during Plan Mode, its specialized instructions and procedural workflows will
guide the research, design, and planning phases.

For example:

* A **“Database Migration”** skill could ensure the plan includes data safety
  checks and rollback strategies.
* A **“Security Audit”** skill could prompt Gemini CLI to look for specific
  vulnerabilities during codebase exploration.
* A **“Frontend Design”** skill could guide Gemini CLI to use specific UI
  components and accessibility standards in its proposal.

To use a skill in Plan Mode, you can explicitly ask Gemini CLI to “use the
`<skill-name>` skill to plan…” or Gemini CLI may autonomously activate it
based on the task description.

### Custom policies

[Section titled “Custom policies”](<#custom-policies>)

Plan Mode’s default tool restrictions are managed by the
[policy engine](<https://geminicli.com/docs/reference/policy-engine>) and defined in the built-in
[`plan.toml`](<https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/policy/policies/plan.toml>) file. The built-in policy (Tier 1) enforces the read-only state,
but you can customize these rules by creating your own policies in your
`~/.gemini/policies/` directory (Tier 2).

#### Global vs. mode-specific rules

[Section titled “Global vs. mode-specific rules”](<#global-vs-mode-specific-rules>)

As described in the
[policy engine documentation](<https://geminicli.com/docs/reference/policy-engine#approval-modes>), any
rule that does not explicitly specify `modes` is considered “always active” and
will apply to Plan Mode as well.

To maintain the integrity of Plan Mode as a safe research environment,
persistent tool approvals are context-aware. Approvals granted in modes like
Default or Auto-Edit do not apply to Plan Mode, ensuring that tools trusted for
implementation don’t automatically execute while you’re researching. However,
approvals granted while in Plan Mode are treated as intentional choices for
global trust and apply to all modes.

If you want to manually restrict a rule to other modes but *not* to Plan Mode,
you must explicitly specify the target modes. For example, to allow `npm test`
in default and Auto-Edit modes but not in Plan Mode:

```
[[rule]]


toolName = "run_shell_command"


commandPrefix = "npm test"


decision = "allow"


priority = 100


# By omitting "plan", this rule will not be active in Plan Mode.


modes = ["default", "autoEdit"]
```

#### Example: Automatically approve read-only MCP tools

[Section titled “Example: Automatically approve read-only MCP tools”](<#example-automatically-approve-read-only-mcp-tools>)

By default, read-only MCP tools require user confirmation in Plan Mode. You can
use `toolAnnotations` and the `mcpName` wildcard to customize this behavior for
your specific environment.

`~/.gemini/policies/mcp-read-only.toml`

```
[[rule]]


toolName = "*"


mcpName = "*"


toolAnnotations = { readOnlyHint = true }


decision = "allow"


priority = 100


modes = ["plan"]
```

For more information on how the policy engine works, see the
[policy engine](<https://geminicli.com/docs/reference/policy-engine>) docs.

#### Example: Allow git commands in Plan Mode

[Section titled “Example: Allow git commands in Plan Mode”](<#example-allow-git-commands-in-plan-mode>)

This rule lets you check the repository status and see changes while in Plan
Mode.

`~/.gemini/policies/git-research.toml`

```
[[rule]]


toolName = "run_shell_command"


commandPrefix = ["git status", "git diff"]


decision = "allow"


priority = 100


modes = ["plan"]
```

#### Example: Enable custom subagents in Plan Mode

[Section titled “Example: Enable custom subagents in Plan Mode”](<#example-enable-custom-subagents-in-plan-mode>)

Built-in research [subagents](<https://geminicli.com/docs/core/subagents>) like
[`codebase_investigator`](<https://geminicli.com/docs/core/subagents#codebase-investigator>) and
[`cli_help`](<https://geminicli.com/docs/core/subagents#cli-help-agent>) are enabled by default in Plan
Mode. You can enable additional
[custom subagents](<https://geminicli.com/docs/core/subagents#creating-custom-subagents>) by adding a
rule to your policy.

`~/.gemini/policies/research-subagents.toml`

```
[[rule]]


toolName = "my_custom_subagent"


decision = "allow"


priority = 100


modes = ["plan"]
```

Tell Gemini CLI it can use these tools in your prompt, for example: *“You can
check ongoing changes in git.”*

### Custom plan directory and policies

[Section titled “Custom plan directory and policies”](<#custom-plan-directory-and-policies>)

By default, planning artifacts are stored in a managed temporary directory
outside your project: `~/.gemini/tmp/<project>/<session-id>/plans/`.

You can configure a custom directory for plans in your `settings.json`. For
example, to store plans in a `.gemini/plans` directory within your project:

```
{


"general": {


"plan": {


"directory": ".gemini/plans"


}


}


}
```

To maintain the safety of Plan Mode, user-configured paths for the plans
directory are restricted to the project root. This ensures that custom planning
locations defined within a project’s workspace cannot be used to escape and
overwrite sensitive files elsewhere. Any user-configured directory must reside
within the project boundary.

Using a custom directory requires updating your
[policy engine](<https://geminicli.com/docs/reference/policy-engine>) configurations to allow
`write_file` and `replace` in that specific location. For example, to allow
writing to the `.gemini/plans` directory within your project, create a policy
file at `~/.gemini/policies/plan-custom-directory.toml`:

```
[[rule]]


toolName = ["write_file", "replace"]


decision = "allow"


priority = 100


modes = ["plan"]


# Adjust the pattern to match your custom directory.


# This example matches any .md file in a .gemini/plans directory within the project.


argsPattern = "\"file_path\":\"[^\"]+[\\\\/]+\\.gemini[\\\\/]+plans[\\\\/]+[\\w-]+\\.md\""
```

### Using hooks with Plan Mode

[Section titled “Using hooks with Plan Mode”](<#using-hooks-with-plan-mode>)

You can use the [hook system](<https://geminicli.com/docs/hooks/writing-hooks>) to automate parts of
the planning workflow or enforce additional checks when Gemini CLI transitions
into or out of Plan Mode.

Hooks such as `BeforeTool` or `AfterTool` can be configured to intercept the
`enter_plan_mode` and `exit_plan_mode` tool calls.

#### Example: Archive approved plans to GCS (`AfterTool`)

[Section titled “Example: Archive approved plans to GCS (AfterTool)”](<#example-archive-approved-plans-to-gcs-aftertool>)

If your organizational policy requires a record of all execution plans, you can
use an `AfterTool` hook to securely copy the plan artifact to Google Cloud
Storage whenever Gemini CLI exits Plan Mode to start the implementation.

**`.gemini/hooks/archive-plan.sh`:**

```
#!/usr/bin/env bash


# Extract the plan filename from the tool input JSON


plan_filename=$(jq -r '.tool_input.plan_filename // empty')


# Construct the absolute path using the GEMINI_PLANS_DIR environment variable


plan_path="$GEMINI_PLANS_DIR/$plan_filename"


if [ -f "$plan_path" ]; then


# Generate a unique filename using a timestamp


filename="$(date +%s)_$(basename "$plan_path")"


# Upload the plan to GCS in the background so it doesn't block the CLI


gsutil cp "$plan_path" "gs://my-audit-bucket/gemini-plans/$filename" > /dev/null 2>&1 &


fi


# AfterTool hooks should generally allow the flow to continue


echo '{"decision": "allow"}'
```

To register this `AfterTool` hook, add it to your `settings.json`:

```
{


"hooks": {


"AfterTool": [


{


"matcher": "exit_plan_mode",


"hooks": [


{


"name": "archive-plan",


"type": "command",


"command": "~/.gemini/hooks/archive-plan.sh"


}


]


}


]


}


}
```

## Commands

[Section titled “Commands”](<#commands>)

* **`/plan copy`**: Copy the currently approved plan to your clipboard.

## Planning workflows

[Section titled “Planning workflows”](<#planning-workflows>)

Plan Mode provides building blocks for structured research and design. These are
implemented as [extensions](<https://geminicli.com/docs/extensions>) using core planning tools
like [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>),
[`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>), and
[`ask_user`](<https://geminicli.com/docs/tools/ask-user>).

### Built-in planning workflow

[Section titled “Built-in planning workflow”](<#built-in-planning-workflow>)

The built-in planner uses an adaptive workflow to analyze your project, consult
you on trade-offs via [`ask_user`](<https://geminicli.com/docs/tools/ask-user>), and draft a plan for
your approval.

### Custom planning workflows

[Section titled “Custom planning workflows”](<#custom-planning-workflows>)

You can install or create specialized planners to suit your workflow.

#### Conductor

[Section titled “Conductor”](<#conductor>)

[Conductor](<https://github.com/gemini-cli-extensions/conductor>) is designed for spec-driven development. It organizes work into
“tracks” and stores persistent artifacts in your project’s `conductor/`
directory:

* **Automate transitions:** Switches to read-only mode via
  [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>).
* **Streamline decisions:** Uses [`ask_user`](<https://geminicli.com/docs/tools/ask-user>) for
  architectural choices.
* **Maintain project context:** Stores artifacts in the project directory using
  [custom plan directory and policies](<#custom-plan-directory-and-policies>).
* **Handoff execution:** Transitions to implementation via
  [`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>).

#### Build your own

[Section titled “Build your own”](<#build-your-own>)

Since Plan Mode is built on modular building blocks, you can develop your own
custom planning workflow as an [extensions](<https://geminicli.com/docs/extensions>). By
leveraging core tools and [custom policies](<#custom-policies>), you can define
how Gemini CLI researches and stores plans for your specific domain.

To build a custom planning workflow, you can use:

* **Tool usage:** Use core tools like
  [`enter_plan_mode`](<https://geminicli.com/docs/tools/planning#1-enter_plan_mode-enterplanmode>),
  [`ask_user`](<https://geminicli.com/docs/tools/ask-user>), and
  [`exit_plan_mode`](<https://geminicli.com/docs/tools/planning#2-exit_plan_mode-exitplanmode>) to
  manage the research and design process.
* **Customization:** Set your own storage locations and policy rules using
  [custom plan directories](<#custom-plan-directory-and-policies>) and
  [custom policies](<#custom-policies>).

By using Plan Mode as its execution environment, your custom methodology can
enforce read-only safety during the design phase while benefiting from
high-reasoning model routing.

## Automatic Model Routing

[Section titled “Automatic Model Routing”](<#automatic-model-routing>)

When using an [auto model](<https://geminicli.com/docs/reference/configuration#model>), Gemini CLI
automatically optimizes [model routing](<https://geminicli.com/docs/cli/telemetry#model-routing>) based
on the current phase of your task:

1. **Planning Phase:** While in Plan Mode, the CLI routes requests to a
   high-reasoning **Pro** model to ensure robust architectural decisions and
   high-quality plans.
2. **Implementation Phase:** Once a plan is approved and you exit Plan Mode,
   the CLI detects the existence of the approved plan and automatically
   switches to a high-speed **Flash** model. This provides a faster, more
   responsive experience during the implementation of the plan.

If the high-reasoning model is unavailable or you don’t have access to it,
Gemini CLI automatically and silently falls back to a faster model to ensure
your workflow isn’t interrupted.

This behavior is enabled by default to provide the best balance of quality and
performance. You can disable this automatic switching in your settings:

```
{


"general": {


"plan": {


"modelRouting": false


}


}


}
```

## Cleanup

[Section titled “Cleanup”](<#cleanup>)

By default, Gemini CLI automatically cleans up old session data, including all
associated plan files and task trackers.

* **Default behavior:** Sessions (and their plans) are retained for **30 days**.
* **Configuration:** You can customize this behavior via the `/settings` command
  (search for **Enable Session Cleanup** or **Keep chat history**) or in your
  `settings.json` file. See
  [session retention](<https://geminicli.com/docs/cli/session-management#session-retention>) for more
  details.

Manual deletion also removes all associated artifacts:

* **Command Line:** Use `gemini --delete-session <index|id>`.
* **Session Browser:** Press `/resume`, navigate to a session, and press `x`.

If you use a [custom plans directory](<#custom-plan-directory-and-policies>),
those files are not automatically deleted and must be managed manually.

## Non-interactive execution

[Section titled “Non-interactive execution”](<#non-interactive-execution>)

When running Gemini CLI in non-interactive environments (such as headless
scripts or CI/CD pipelines), Plan Mode optimizes for automated workflows:

* **Automatic transitions:** The policy engine automatically approves the
  `enter_plan_mode` and `exit_plan_mode` tools without prompting for user
  confirmation.
* **Automated implementation:** When exiting Plan Mode to execute the plan,
  Gemini CLI automatically switches to
  [YOLO mode](<https://geminicli.com/docs/reference/policy-engine#approval-modes>) instead of the
  standard Default mode. This allows the CLI to execute the implementation steps
  automatically without hanging on interactive tool approvals.

**Example:**

Terminal window

```
gemini --approval-mode plan -p "Analyze telemetry and suggest improvements"
```

Last updated: Jun 18, 2026


</details>
