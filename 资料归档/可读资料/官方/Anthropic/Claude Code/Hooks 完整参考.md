# Hooks 完整参考

列出 Hook 事件、匹配规则、配置字段、输入输出与退出码；同时说明异步、HTTP、提示型 Hook 和 MCP 工具 Hook 等高级行为。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-11](<https://code.claude.com/docs/en/hooks>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/hooks.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Hooks 完整参考.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 挂钩参考

> Claude Code 挂钩事件、配置架构、JSON input/output 格式、退出代码、异步挂钩、HTTP 挂钩、提示挂钩和 MCP 工具挂钩的参考。



**提示：**

有关示例的快速入门指南，请参阅 [使用挂钩自动执行操作 ](<https://code.claude.com/docs/en/hooks-guide>)。


挂钩是用户定义的 shell 命令、HTTP 端点、MCP 工具调用、LLM 提示或在 Claude Code 生命周期中的特定点自动执行的子代理。 Claude Code 无论在何处运行都会触发相同的挂钩事件：终端中的会话、IDE 扩展、[桌面应用程序](<https://code.claude.com/docs/en/desktop-quickstart>) 和 [云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)。使用此参考来查找事件架构、配置选项、JSON input/output 格式以及高级功能，例如异步挂钩、HTTP 挂钩和 MCP 工具挂钩。

插件还可以将钩子注册为JavaScript函数，Claude Code在其自己的进程中调用，该函数可以在界面中进行绘制以及对事件进行操作。一个插件是 [mod](<https://code.claude.com/docs/en/plugins/mods/overview>)，这些函数钩子在 [React 到事件 ](<https://code.claude.com/docs/en/plugins/mods/events>) 中涵盖，而不是在这里。此页面上的挂钩继续与模组一起使用。

## 钩子生命周期

Claude Code 在会话期间的特定点运行挂钩。当事件触发并且匹配器匹配时，Claude Code 将有关该事件的 JSON 上下文传递给您的钩子处理程序。对于命令挂钩，输入到达标准输入。对于 HTTP 挂钩，它作为 POST 请求正文到达。然后，您的处理程序可以检查输入、采取操作，并可选择返回决策。

事件分为三种节奏：

* 每个会话：`SessionStart` 和 `SessionEnd`
* 每回合：`UserPromptSubmit`、`Stop`、`StopFailure`
* 在代理循环内的每个工具调用上：`PreToolUse` 和 `PostToolUse`，[`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) 调用除外，它会跳过这两个调用

<div style={{maxWidth: "500px", margin: "0 auto"}}>
  

    

![Hook 生命周期图显示了输入 SessionStart 的可选设置，然后是包含 UserPromptSubmit、UserPromptExpansion 的每轮循环（用于斜杠命令）、嵌套代理循环（PreToolUse、PermissionRequest、 PostToolUse、PostToolUseFailure、PostToolBatch、SubagentStart/Stop、TaskCreated、TaskCompleted）和停止或 StopFailure，后跟 TeammateIdle， PreCompact、PostCompact 和 SessionEnd，带有嵌套在 MCP 工具执行内的 ElicitationResult，PermissionDenied 作为 PermissionRequest 的侧分支用于自动模式拒绝， WorktreeCreate、WorktreeRemove、通知、ConfigChange、InstructionsLoaded、CwdChanged、FileChanged 和 DirectoryAdded 作为独立异步事件，PreModelSwitch 作为运行的独立顺序事件在请求的模型切换之前，PostModelSwitch 作为在会话模型更改后运行的独立异步事件，MessageDisplay 作为在助理消息文本流 ](<https://mintcdn.com/claude-code/x7pO8l4XcvAXCoVc/images/hooks-lifecycle.svg?fit=max&auto=format&n=x7pO8l4XcvAXCoVc&q=85&s=81b9256c1bbe8832553485f5d9e9c746>) 时运行的仅显示事件


    

![Hook 生命周期图显示了输入 SessionStart 的可选设置，然后是包含 UserPromptSubmit、UserPromptExpansion 的每轮循环（用于斜杠命令）、嵌套代理循环（PreToolUse、PermissionRequest、 PostToolUse、PostToolUseFailure、PostToolBatch、SubagentStart/Stop、TaskCreated、TaskCompleted）和停止或 StopFailure，后跟 TeammateIdle， PreCompact、PostCompact 和 SessionEnd，带有嵌套在 MCP 工具执行内的 ElicitationResult，PermissionDenied 作为 PermissionRequest 的侧分支用于自动模式拒绝， WorktreeCreate、WorktreeRemove、通知、ConfigChange、InstructionsLoaded、CwdChanged、FileChanged 和 DirectoryAdded 作为独立异步事件，PreModelSwitch 作为运行的独立顺序事件在请求的模型切换之前，PostModelSwitch 作为在会话模型更改后运行的独立异步事件，MessageDisplay 作为在助理消息文本流 ](<https://mintcdn.com/claude-code/x7pO8l4XcvAXCoVc/images/hooks-lifecycle-dark.svg?fit=max&auto=format&n=x7pO8l4XcvAXCoVc&q=85&s=c9b3d88487335f58cce0b52e2f9e7531>) 时运行的仅显示事件

  

</div>

下表总结了每个事件触发的时间。 [Hook 事件](<#挂钩事件>) 部分记录了每个事件的完整输入模式和决策控制选项。

|活动|当它着火时|
| :- | :- |
| `SessionStart` |当会话开始或恢复时|
| `Setup` |当您在`-p`模式下使用`--init-only`或`--init`或`--maintenance`启动Claude Code时。用于CI或脚本中的一次性准备|
| `UserPromptSubmit` |提交提示时，在 Claude 处理它之前。也会在 [ 上触发，然后 Claude Code 自行启动 ](<https://code.claude.com/docs/en/hooks#userpromptsubmit>)|
| `UserPromptExpansion` |当用户键入的命令在到达 Claude 之前扩展为提示时。可以阻止扩展|
| `PreToolUse` |在执行工具调用之前。可以阻止它|
| `PermissionRequest` |当工具调用需要权限决策时|
| `PermissionDenied` |当自动模式拒绝工具调用时，包括没有分类器判决的拒绝。使用 JSON `hookSpecificOutput.retry: true` 告诉模型它可以重试被拒绝的工具调用。当分类器未产生判决时，Claude Code 会忽略 `retry`|
| `PostToolUse` |工具调用成功后|
| `PostToolUseFailure` |工具调用失败后|
| `PostToolBatch` |整批并行工具调用解决后，下一次模型调用之前|
| `Notification` |当Claude Code发送通知时|
| `MessageDisplay` |显示助理消息文本时|
| `SubagentStart` |当子代理产生时|
| `SubagentStop` |当子代理完成时|
| `TaskCreated` |当通过`TaskCreate`创建任务时|
| `TaskCompleted` |当任务被标记为已完成时|
| `Stop` |当Claude完成响应时|
| `StopFailure` |当回合因API错误而结束时|
| `TeammateIdle` |当[agent队伍](<https://code.claude.com/docs/en/agent-teams>)队友即将闲置时|
| `InstructionsLoaded` |当 CLAUDE.md 或 `.claude/rules/*.md` 文件加载到上下文中时。在会话开始时以及会话期间延迟加载文件时触发|
| `ConfigChange` |当配置文件在会话期间发生更改时|
| `CwdChanged` |当工作目录改变时，例如当Claude执行`cd`命令时。对于使用 direnv 等工具进行反应式环境管理很有用|
| `DirectoryAdded` |当通过 `/add-dir` 或 SDK `register_repo_root` 控制请求在会话中添加工作目录时|
| `FileChanged` |当监视的文件在磁盘上发生更改时。 `matcher` 字段指定要观看的文件名|
| `WorktreeCreate` |当通过 `--worktree`、`isolation: "worktree"` 创建工作树或为后台会话创建工作树时。替换默认的 git 行为|
| `WorktreeRemove` |当 `WorktreeCreate` 挂钩创建的工作树被删除时|
| `PreCompact` |上下文压缩之前|
| `PostCompact` |上下文压缩完成后|
| `PreModelSwitch` |在 Claude Code 应用您或客户请求的模型切换之前。可以堵住开关|
| `PostModelSwitch` |会话模型更改后，包括 Claude Code 自行进行的更改，例如恢复会话时恢复模型|
| `Elicitation` |当 MCP 服务器在工具调用期间请求用户输入时|
| `ElicitationResult` |用户响应 MCP 诱导后，在响应发送回服务器之前|
| `SessionEnd` |当会话终止时|

### 钩子如何解析

要了解事件、匹配器和处理程序如何组合在一起，请考虑这个阻止破坏性 shell 命令的 `PreToolUse` 挂钩。



  

#### macOS/Linux

`matcher` 缩小为 Bash 工具调用，`if` 条件进一步缩小为与 `rm *` 匹配的 Bash 子命令，因此 `block-rm.sh` 仅在两个过滤器匹配时才会生成：

    ```json theme={null}
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Bash",
            "hooks": [
              {
                "type": "command",
                "if": "Bash(rm *)",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

该脚本从 stdin 读取 JSON 输入，提取命令，如果包含 `rm -rf`，则返回 `"deny"` 的 `permissionDecision`。将其保存到项目中的 `.claude/hooks/block-rm.sh` 并使用 `chmod +x .claude/hooks/block-rm.sh` 使其可执行，以便 Claude Code 可以运行它：

    ```bash theme={null}
    #!/bin/bash
    # .claude/hooks/block-rm.sh
    COMMAND=$(jq -r '.tool_input.command')

    if echo "$COMMAND" | grep -q 'rm -rf'; then
      jq -n '{
        hookSpecificOutput: {
          hookEventName: "PreToolUse",
          permissionDecision: "deny",
          permissionDecisionReason: "Destructive command blocked by hook"
        }
      }'
    else
      exit 0  # no decision; normal permission flow applies
    fi
    ```

该脚本与本页上解析 JSON 输入的其他 Bash 示例一样，使用 `jq`，因此请安装 `jq` 并确保它在您的 `PATH` 上，然后再尝试它们。
  


  

#### Windows（PowerShell）

匹配器 `Bash|PowerShell` 涵盖 [PowerShell 工具 ](<#powershell>) 以及 Bash。单个 `if` 规则仅匹配一个工具的调用，因此每个工具都有自己的处理程序：第一个缩小到匹配 `rm *` 的 Bash 子命令，第二个缩小到匹配 `Remove-Item *` 的 PowerShell 命令。两者都通过 `powershell.exe` 运行相同的脚本：

    ```json theme={null}
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Bash|PowerShell",
            "hooks": [
              {
                "type": "command",
                "if": "Bash(rm *)",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.ps1"
                ]
              },
              {
                "type": "command",
                "if": "PowerShell(Remove-Item *)",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

`-NoProfile` 标志会跳过加载 PowerShell 配置文件，因此挂钩快速启动，并且 `-ExecutionPolicy Bypass` 让 PowerShell 运行本地脚本文件。

该脚本从 stdin 读取 JSON 输入，提取命令，如果包含 `rm -rf` 或 `Remove-Item` 后跟 `-Recurse`，则返回 `"deny"` 的 `permissionDecision`。将其保存到项目中的`.claude/hooks/block-rm.ps1`：

    ```powershell theme={null}
    # .claude/hooks/block-rm.ps1
    $callInput = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $command = $callInput.tool_input.command

    if ($command -match 'rm -rf|Remove-Item.*-Recurse') {
      @{
        hookSpecificOutput = @{
          hookEventName = "PreToolUse"
          permissionDecision = "deny"
          permissionDecisionReason = "Destructive command blocked by hook"
        }
      } | ConvertTo-Json
    } else {
      exit 0  # no decision; normal permission flow applies
    }
    ```
  


现在假设 Claude Code 决定针对 macOS/Linux 配置运行 `Bash "rm -rf /tmp/build"`。发生的情况如下：



  

![ 钩子解析图： PreToolUse 触发，匹配器检查 Bash 匹配，然后 if 条件检查 Bash(rm *) 匹配。如果两者匹配，则钩子命令运行并返回permissionDecision拒绝，因此工具调用被阻止并且Claude Code继续。如果任一检查失败，则跳过挂钩并允许继续进行工具调用。](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/hook-resolution.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=be0bf3053550c26de5f54cd64674c197>)


  

![ 钩子解析图： PreToolUse 触发，匹配器检查 Bash 匹配，然后 if 条件检查 Bash(rm *) 匹配。如果两者匹配，则钩子命令运行并返回permissionDecision拒绝，因此工具调用被阻止并且Claude Code继续。如果任一检查失败，则跳过挂钩并允许继续进行工具调用。](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/hook-resolution-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=e80af91f8507cee6bd51ac3c2dd92f63>)




  

#### 事件引发

`PreToolUse` 事件触发。 Claude Code 将标准输入上的工具输入作为 JSON 发送到钩子：

    ```json theme={null}
    { "tool_name": "Bash", "tool_input": { "command": "rm -rf /tmp/build" }, ... }
    ```
  


  

#### 匹配器检查

匹配器 `"Bash"` 与工具名称匹配，因此该钩子组被激活。如果省略匹配器或使用 `"*"`，则该组会在事件每次发生时激活。
  


  

#### 如果条件检查

`if` 条件 `"Bash(rm *)"` 匹配，因为 `rm -rf /tmp/build` 是匹配 `rm *` 的子命令，因此会生成此处理程序。如果命令是 `npm test`，则 `if` 检查将失败，并且 `block-rm.sh` 将永远不会运行，从而避免了进程生成开销。 `if`字段是可选的；如果没有它，匹配组中的每个处理程序都会运行。
  


  

#### 钩子处理程序运行

该脚本检查完整命令并找到 `rm -rf`，因此它将决定打印到标准输出：

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "deny",
        "permissionDecisionReason": "Destructive command blocked by hook"
      }
    }
    ```

如果该命令是更安全的 `rm` 变体，例如 `rm file.txt`，则脚本将改为点击 `exit 0`。没有输出的退出代码 0 意味着挂钩没有决定报告，因此工具调用继续通过正常的 [ 权限流程 ](<https://code.claude.com/docs/en/permissions>)。钩子可以拒绝呼叫，但保持沉默并不表示批准。
  


  

#### Claude Code 对结果起作用

Claude Code 读取 JSON 决策，阻止工具调用，并显示 Claude 原因。
  


下面的 [Configuration](<#配置>) 部分记录了完整的架构，每个 [hook event](<#挂钩事件>) 部分记录了您的命令接收的输入以及它可以返回的输出。

## 配置

挂钩在 JSON 设置文件中定义。该配置具有三层嵌套：

1. 选择要响应的 [ 挂钩事件 ](<#挂钩事件>)，例如 `PreToolUse` 或 `Stop`
2. 添加 [ 匹配器组 ](<#匹配器模式>) 以在触发时进行过滤，例如“仅适用于 Bash 工具”
3. 定义一个或多个 [ 钩子处理程序 ](<#钩子处理程序字段>) 在匹配时运行

请参阅上面的[挂钩如何解析](<#钩子如何解析>)，以获取带有注释示例的完整演练。



**注释：**

此页面使用每个级别的特定术语：**挂钩事件**表示生命周期点，**匹配器组**表示过滤器，**挂钩处理程序**表示shell命令，HTTP端点、MCP 工具、提示符或运行的 agent。 “钩子”本身指的是一般特征。


### 挂钩位置

定义钩子的位置决定了它的范围：

|地点|适用范围|可分享|
| :- | :- | :- |
| `~/.claude/settings.json` |您的所有项目|否，在您的机器本地|
| `.claude/settings.json` |单一项目|是的，可以提交到回购协议|
| `.claude/settings.local.json` |单一项目|否，当 Claude Code 保存设置时 gitignored|
|托管策略设置|全组织范围|是的，由管理员控制|
|[插件](<https://code.claude.com/docs/en/plugins/overview>) `hooks/hooks.json`|当插件启用时|是的，与插件捆绑在一起|
|[技能](<https://code.claude.com/docs/en/skills>)前题|调用技能后，会话的其余部分。参见[技能中的Hooks和agents](<#挂钩技能和agents>)|是的，在技能文件中定义|
|[子代理](<https://code.claude.com/docs/en/sub-agents>) frontmatter|当该子代理正在运行时|是的，在子代理文件中定义|

[云会话](<https://code.claude.com/docs/en/claude-code-on-the-web>)不读取您的本地`~/.claude/settings.json`。在 [ 自托管环境 ](<https://code.claude.com/docs/en/self-hosted-environments-configuration#permissions-and-tool-approval>) 中，Claude Code 还会运行操作员从运行程序主机 `~/.claude/` 播种的挂钩，并且当该文件位于 [ 托管源中时，它会运行运行程序映像的托管设置文件中的挂钩Claude Code 应用 ](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>)，默认情况下，仅当服务器管理的设置和 MDM- 交付的 Claude Code 策略都不提供托管层时。请参阅 [ 从您的设置中继承哪些内容 ](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>) 哪些设置文件和插件以及哪些挂钩到达云会话。

有关设置文件分辨率的详细信息，请参阅[设置](<https://code.claude.com/docs/en/settings>)。

来自设置文件、托管策略设置和插件的挂钩也在 [ 子代理 ](<https://code.claude.com/docs/en/sub-agents>) 内运行。当子代理调用工具时，`PreToolUse` 和 `PostToolUse` 等工具事件会触发与主对话中相同的配置挂钩，并且输入携带标识子代理的 `agent_id` 和 `agent_type` [ 公共输入字段 ](<#常用输入字段>)。

管理员可以使用 [`allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedhooksonly>) 在 [托管设置](<https://code.claude.com/docs/en/managed-settings>) 限制哪些钩子运行：

* 您的用户、项目、本地和插件挂钩被阻止。在托管设置 `enabledPlugins` 中强制启用的插件的钩子可以免除
* Claude Code 还将您的 [`statusLine`](<https://code.claude.com/docs/en/statusline>)、[`fileSuggestion`](<https://code.claude.com/docs/en/settings-reference#filesuggestion>) 和 [`subagentStatusLine`](<https://code.claude.com/docs/en/statusline#subagent-status-lines>) 设置缩小为托管设置
* Claude Code 还会禁用具有 [`command` 源 ](<https://code.claude.com/docs/en/plugins/marketplace-reference#command-plugin-source>) 的插件，包括在托管设置 `enabledPlugins` 中强制启用的插件，除非 [`disableCommandPluginSources`](<https://code.claude.com/docs/en/settings-reference#disablecommandpluginsources>) 显式设置为 `false`。 `command` 源需要 Claude Code v2.1.229 或更高版本
* Claude Code 还会阻止市场 [`headersHelper` 命令 ](<https://code.claude.com/docs/en/plugins/host-marketplace#authenticate-archive-downloads>)，除非 [`disableCommandPluginSources`](<https://code.claude.com/docs/en/settings-reference#disablecommandpluginsources>) 显式设置为 `false`，托管设置本身声明的市场除外

请参阅 [ 在 `allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#what-runs-under-allowmanagedhooksonly>) 下运行的内容。

挂钩条目跨设置级别合并而不是相互替换：用户、项目和本地设置添加自己的挂钩而不删除托管挂钩，并且 [`disableAllHooks`](<#禁用或删除挂钩>) 设置无法从外部托管设置禁用托管挂钩。

[HTTP 挂钩白名单 ](<https://code.claude.com/docs/en/settings-reference#hook-and-skill-settings>) 适用于来自每个来源的挂钩，包括托管策略设置：

* `allowedHttpHookUrls`：在任何设置级别定义时，Claude Code 仅当其 URL 与合并的白名单匹配时才运行 HTTP 挂钩处理程序
* `httpHookAllowedEnvVars`：定义后，Claude Code 仅将该列表上的环境变量插入到钩子标头中

### 匹配器模式

当钩子触发时，`matcher` 字段会被过滤。匹配器的评估方式取决于它包含的字符：

|匹配器值|评价为|示例|
| :- | :- | :- |
|`"*"`、`""`，或省略|匹配所有|每次事件发生时都会触发|
|仅限字母、数字、`_`、`-`、空格、`,` 和 `\|`|精确字符串，或由 `\|` 或 `,` 分隔的精确字符串列表，周围可选空格|`Bash` 仅匹配 Bash 工具； `Edit\|Write` 和 `Edit, Write` 均与任一工具完全匹配； `code-reviewer` 仅匹配 agent 类型|
|包含任何其他字符|JavaScript 正则表达式，未锚定|`^Notebook` 匹配任何名称以 `Notebook` 开头的工具； `mcp__memory__.*` 匹配 `memory` 服务器中的每个工具|

正则表达式路径上的匹配器使用 JavaScript 的 `RegExp.prototype.test` 进行测试，它在值中的任何位置匹配成功。 `Edit.*` 匹配 `Edit` 和 `NotebookEdit`；当您需要整个字符串匹配时，将模式包装在 `^` 和 `$` 中，如 `^Edit$` 中所示。

`FileChanged` 和 `StopFailure` 仅使用一组较窄的精确匹配字母、数字、`_` 和 `|`。这两个事件的匹配器中的连字符、空格或逗号将其保留在正则表达式路径上，并且仅 `|` 分隔替代项。下表中具有匹配器支持的所有其他事件都接受 `|` 或 `,`。

`FileChanged` 事件在构建其观察列表时不遵循这些规则。参见[FileChanged](<#filechanged>)。

每个事件类型在不同的字段上匹配：

|活动|匹配器过滤什么|匹配器值示例|
| :- | :- | :- |
| `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied` |工具名称| `Bash`, `Edit\|Write`, `mcp__.*` |
| `SessionStart` |会议如何开始| `startup`, `resume`, `clear`, `compact`, `fork` |
| `Setup` |哪个 CLI 标志触发了设置| `init`, `maintenance` |
| `SessionEnd` |会议为何结束| `clear`, `resume`, `logout`, `prompt_input_exit`, `other` |
| `Notification` |通知类型| `permission_prompt`, `idle_prompt`, `auth_success`, `elicitation_dialog`, `elicitation_url_dialog`, `elicitation_complete`, `elicitation_response`, `agent_needs_input`, `agent_completed`, `quota_auto_resume_fired`, `quota_auto_resume_stale`, `quota_auto_resume_disabled` |
| `SubagentStart` |agent型|`general-purpose`、`Explore`、`Plan`、自定义 agent 名称或插件范围名称，例如 `^my-plugin:reviewer$`|
| `PreCompact`, `PostCompact` |是什么触发了压缩| `manual`, `auto` |
| `PreModelSwitch`, `PostModelSwitch` |会话切换到的模型的规范名称，如 [PreModelSwitch](<#premodelswitch>) 中所述| `claude-opus-5`, `claude-opus-4-6\|claude-opus-5`, `.*opus.*` |
| `SubagentStop` |agent型|与 `SubagentStart` 相同的值|
| `ConfigChange` |配置源| `user_settings`, `project_settings`, `local_settings`, `policy_settings`, `skills` |
| `CwdChanged` |没有匹配器支持|总是在每次发生时触发|
| `DirectoryAdded` |目录是如何添加的| `slash_command`, `register_repo_root` |
| `FileChanged` |要观看的文字文件名（请参阅[FileChanged](<#filechanged>)）| `.envrc\|.env` |
| `StopFailure` |错误类型| `rate_limit`, `overloaded`, `authentication_failed`, `oauth_org_not_allowed`, `account_on_hold`, `billing_error`, `invalid_request`, `model_not_found`, `server_error`, `max_output_tokens`, `cloud_credential_error`, `unknown` |
| `InstructionsLoaded` |负载原因| `session_start`, `nested_traversal`, `path_glob_match`, `include`, `compact` |
| `UserPromptExpansion` |命令名称|你的技能或命令名称|
| `Elicitation` |MCP 服务器名称|您配置的 MCP 服务器名称|
| `ElicitationResult` |MCP 服务器名称|与 `Elicitation` 相同的值|
| `UserPromptSubmit`, `PostToolBatch`, `Stop`, `TeammateIdle`, `TaskCreated`, `TaskCompleted`, `WorktreeCreate`, `WorktreeRemove`, `MessageDisplay` |没有匹配器支持|总是在每次发生时触发|

在 `cloud_credential_error` 上匹配 `StopFailure` 需要 Claude Code v2.1.267 或更高版本，这是在该值下报告凭据加载失败的第一个版本，而不是 `server_error` 或 `unknown`。

对于大多数事件，Claude Code 根据它发送到标准输入上的钩子的 [JSON 输入 ](<#钩子输入输出>) 中的字段来评估匹配器。对于工具事件，该字段为 `tool_name`。对于 `PreModelSwitch` 和 `PostModelSwitch`，Claude Code 根据从 `to_model` 派生的规范名称来评估匹配器，如 [PreModelSwitch](<#premodelswitch>) 中所述。每个 [ 挂钩事件 ](<#挂钩事件>) 部分都会列出该事件的完整匹配器值集和输入架构。

此示例仅在 Claude 写入或编辑文件时运行 linting 脚本：

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/lint-check.sh"
          }
        ]
      }
    ]
  }
}
```

如果您将 `matcher` 字段添加到没有匹配器支持的事件中，它将被静默忽略。

对于工具事件，您可以通过在各个挂钩处理程序上设置 [`if` 字段 ](<#公共字段>) 来更严格地过滤。 `if` 使用 [ 权限规则语法 ](<https://code.claude.com/docs/en/permissions>) 来匹配工具名称和参数，因此当 Bash 输入的任何子命令与 `git *` 匹配时 `"Bash(git *)"` 就会运行，并且 `"Edit(*.ts)"` 仅针对 TypeScript 运行文件。

#### 匹配【MCP】工具

[MCP](<https://code.claude.com/docs/en/mcp>) 服务器工具在工具事件（`PreToolUse`、`PostToolUse`、`PostToolUseFailure`、`PermissionRequest`、`PermissionDenied`）中显示为常规工具，因此您可以像匹配任何其他工具名称一样来匹配它们。

MCP 工具遵循命名模式 `mcp__<server>__<tool>`，例如：

* `mcp__memory__create_entities`：内存服务器的创建实体工具
* `mcp__filesystem__read_file`：文件系统服务器的读文件工具
* `mcp__github__search_repositories`：GitHub服务器的搜索工具

要匹配服务器中的每个工具，请将 `.*` 附加到服务器前缀。 `.*` 是必需的：像 `mcp__memory` 或 `mcp__brave-search` 这样的匹配器仅包含精确匹配字符，因此它被作为精确字符串进行比较并且不匹配任何工具。

* `mcp__memory__.*` 匹配 `memory` 服务器中的所有工具
* `mcp__brave-search__.*` 匹配名称包含连字符的服务器中的所有工具
* `mcp__.*__write.*` 匹配任何服务器中名称以 `write` 开头的任何工具

来自 [ 插件捆绑 MCP 服务器 ](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>) 的工具使用包含插件名称的作用域服务器段：`mcp__plugin_<plugin-name>_<server-name>__<tool>`。针对裸服务器密钥编写的匹配器永远不会为这些工具触发。对于名为 `my-plugin` 的插件，该插件在密钥 `db` 下捆绑服务器，`query` 工具显示为 `mcp__plugin_my-plugin_db__query`，因此该服务器中每个工具的匹配器为 `mcp__plugin_my-plugin_db__.*`。在处理程序的 [`if` 字段 ](<#公共字段>) 中使用相同的作用域工具名称。请参阅[插件提供的MCP服务器](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>)了解如何构建范围名称。

此示例记录所有内存服务器操作并验证来自任何 MCP 服务器的写入操作：

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__memory__.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Memory operation initiated' >> ~/mcp-operations.log"
          }
        ]
      },
      {
        "matcher": "mcp__.*__write.*",
        "hooks": [
          {
            "type": "command",
            "command": "/home/user/scripts/validate-mcp-write.py"
          }
        ]
      }
    ]
  }
}
```

### 钩子处理程序字段

内部 `hooks` 数组中的每个对象都是一个钩子处理程序：shell 命令、HTTP 端点、MCP 工具、LLM 提示符或匹配器匹配时运行的 agent。有五种类型：

* **[命令挂钩](<#命令挂钩字段>)**（`type: "command"`）：运行shell命令。您的脚本在标准输入上接收事件的 [JSON 输入 ](<#钩子输入输出>) 并通过退出代码和标准输出传回结果。
* **[HTTP hooks](<#http-钩子字段>)** (`type: "http"`)：将事件的 JSON 输入作为 HTTP POST 请求发送到 URL。端点使用与命令挂钩相同的 [JSON 输出格式 ](<#json-输出>) 通过响应正文传回结果。
* **[MCP工具挂钩](<#mcp-工具挂钩字段>)**（`type: "mcp_tool"`）：在配置的[MCP服务器](<https://code.claude.com/docs/en/mcp>)上调用工具。该工具的文本输出被视为命令挂钩标准输出。
* **[提示挂钩](<#提示和-agent-挂钩字段>)** (`type: "prompt"`)：向Claude模型发送提示以进行单轮评估。该模型将其决策返回为 JSON。请参阅[基于提示的挂钩](<#基于提示的钩子>)。
* **[Agent hooks](<#提示和-agent-挂钩字段>)** (`type: "agent"`)：生成一个子代理，该子代理可以使用 Read、Grep 和 Glob 等工具在返回决策之前验证条件。 Agent 挂钩是实验性的，可能会发生变化。请参阅基于 [Agent 的钩子 ](<#基于agent的挂钩>)。

所有匹配的钩子并行运行。如果您在多个设置文件中定义相同的处理程序，它将运行一次。同一处理程序的插件或技能副本保持独立。

处理程序在 Claude Code 环境的当前目录中运行。如果当前目录不再存在，例如另一个 shell 在会话中删除的工作树或临时目录，Claude Code 从仍然存在的第一个目录运行命令挂钩：会话启动的目录、项目根目录、您的主目录或系统临时目录。 Claude Code 在 [ 调试日志 ](<#调试钩子>) 中记录一条警告，命名后备目录。

`$CLAUDE_CODE_REMOTE` 环境变量在远程 Web 环境中为 `"true"`，而不是在本地 CLI 中设置。当本地会话具有活动的远程控制连接时，Claude Code v2.1.199 及更高版本将 [`$CLAUDE_CODE_BRIDGE_SESSION_ID`](<https://code.claude.com/docs/en/env-vars>) 设置为 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 会话 ID。

#### 公共字段

这些字段适用于所有钩子类型：

|领域|必填|描述|
| :- | :- | :- |
| `type` |是的|`"command"`、`"http"`、`"mcp_tool"`、`"prompt"` 或 `"agent"`|
| `if` |不|[权限规则语法](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>)用于在此钩子运行时进行过滤，例如`"Bash(git *)"`或`"Edit(*.ts)"`。仅当工具调用与模式匹配时，挂钩命令才会运行。请参阅 [Bash 匹配表 ](<#bash-if-matching>)，了解 Bash 模式如何针对子命令、`$()` 和反引号进行评估。仅针对工具事件进行评估：`PreToolUse`、`PostToolUse`、`PostToolUseFailure`、`PermissionRequest` 和 `PermissionDenied`。在其他事件中，设置为 `if` 的挂钩永远不会运行|
| `timeout` |不|取消前几秒。 Claude Code 不会在使用 [`async: true`](<#在后台运行钩子>) 运行的命令挂钩上强制执行它。默认值：`command`、`http` 和 `mcp_tool` 为 600； 30 为 `prompt`； 60 为 `agent`。 Claude Code 将 [`UserPromptSubmit`](<#userpromptsubmit>)、[`PreModelSwitch`](<#premodelswitch>) 和 [`PostModelSwitch`](<#postmodelswitch>) 上的 `command`、`http` 和 `mcp_tool` 默认值降低为 30，并降低到10 在 [`MessageDisplay`](<#messagedisplay>) 上。 [`SessionEnd`](<#sessionend>)挂钩共享1.5-第二个预算；如果您的设置设置了更长的每个挂钩 `timeout`，Claude Code 会提高预算以匹配，最多 60 秒|
| `statusMessage` |不|挂钩运行时显示的自定义微调器消息|
| `once` |不|如果`true`，Claude Code在第一次成功运行后删除钩子。失败、退出代码 2 阻塞或超时的运行会将挂钩保留在原位，因此它会在下一个匹配事件时再次运行。仅对 [ 技能 frontmatter ](<#挂钩技能和agents>) 中声明的钩子授予荣誉；在设置文件和 agent frontmatter 中被忽略|

`if` 字段仅保存一条权限规则。没有用于组合规则的 `&&`、`||` 或列表语法；要应用多个条件，请为每个条件定义一个单独的挂钩处理程序。

在文件工具的 `if` 条件下，像 `"Edit(src/**)"` 这样的单段目录模式仅匹配工作目录中的 `src` 目录及其下的文件。要匹配任意深度的名为 `src` 的目录，请写入 `"Edit(**/src/**)"`。在v2.1.214之前，`"Edit(src/**)"`匹配工作目录下任意深度名为`src`的目录。

<h4 id="bash-if-matching">
 `if` 模式如何匹配 Bash 命令
</h4>

对于 [`if` 字段 ](<#公共字段>) 中的 Bash 模式，您的钩子命令是否运行取决于模式的形状以及正在调用的 Bash 命令 Claude。前导 `VAR=value` 分配在匹配之前被剥离。

|`if`图案|bash 命令|胡克跑？|为什么|
| :- | :- | :- | :- |
| `Bash(git *)` | `FOO=bar git push` |是的|领导任务被剥夺； `git push` 匹配|
| `Bash(git *)` | `npm test && git push` |是的|检查每个子命令； `git push` 匹配|
| `Bash(rm *)` | `echo $(rm -rf /)` |是的|检查 `$()` 内的命令和反引号； `rm -rf /` 匹配|
| `Bash(rm *)` | `echo $(date)` |不|没有子命令匹配 `rm *`|
| `Bash(git push *)` | `echo $(date)` |是的|指定超过命令名称的模式无论如何都会在 `$()`、反引号或 `$VAR` 上运行挂钩|

当 Claude Code 无法确定 Bash 输入运行哪些命令时，无论模式如何，它都会运行您的钩子。由于 `if` 过滤器是尽力而为的，因此请使用 [ 权限系统 ](<https://code.claude.com/docs/en/permissions>) 而不是挂钩来强制执行硬允许或拒绝。

#### 命令挂钩字段

除了 [ 公共字段 ](<#公共字段>) 之外，命令挂钩还接受以下字段：

|领域|必填|描述|
| :- | :- | :- |
| `command` |是的|要执行的 shell 命令。使用 `args`，直接生成可执行文件。参见[Exec形式和shell形式](<#exec形式和shell形式>)|
| `args` |不|参数列表。当存在时，`command` 被解析为可执行文件，并直接以 `args` 作为参数向量生成，不涉及 shell。参见[Exec形式和shell形式](<#exec形式和shell形式>)|
| `async` |不|如果是`true`，则在后台运行而不阻塞。请参阅[在后台运行挂钩](<#在后台运行钩子>)|
| `asyncRewake` |不|如果 `true`，则在后台运行并在退出代码 2 上唤醒 Claude。挂钩的 stderr 或 stdout（如果 stderr 为空）将作为 [ 系统提醒 ](<https://code.claude.com/docs/en/glossary#system-reminder>) 显示到 Claude，以便它可以对长时间运行的后台故障做出反应|
| `shell` |不|用于此钩子的外壳。接受 `"bash"` 或 `"powershell"`。默认为 `"bash"`，或者在 Windows 上未安装 Git Bash 时默认为 `"powershell"`。设置`"powershell"`在Windows上通过PowerShell运行命令。不需要 `CLAUDE_CODE_USE_POWERSHELL_TOOL`，因为钩子直接生成 PowerShell。设置 `args` 时忽略|
| `onFailure` |不|挂钩失败时操作会发生什么情况：`"continue"`（默认值）或 `"block"`。请参阅[挂钩失败时阻止操作](<#当钩子失败时阻止操作>)。需要 Claude Code v2.1.295 或更高版本|

<a id="exec-form-and-shell-form" />

##### Exec形式和shell形式

当设置 `args` 时，命令挂钩以 exec 形式运行；当省略 `args` 时，命令挂钩以 shell 形式运行。每当钩子引用 [ 路径占位符 ](<#按路径引用脚本>) 时，请设置 `args`，因为每个元素都作为一个参数传递，不带引号。当您需要管道或 `&&` 等外壳功能时，或者当两个问题都不适用时，请省略 `args`。

**Exec 表单 ** 在 `args` 存在时运行。 Claude Code 将 `command` 解析为 `PATH` 上的可执行文件，并以 `args` 作为参数向量直接生成它。没有 shell，因此每个 `args` 元素都是一个与写入完全相同的参数，并且像 `${CLAUDE_PLUGIN_ROOT}` 这样的路径占位符将作为纯字符串替换到 `command` 和每个 `args` 元素中。诸如撇号、`$` 和反引号之类的特殊字符会逐字传递，因为没有 shell 来解释它们。任何平台上都不会发生 shell 标记化。

**Shell 形式 ** 在 `args` 不存在时运行。 `command` 字符串将传递到 shell：macOS 和 Linux 上的 `sh -c`、Windows 上的 Git Bash 或未安装 Git Bash 时的 PowerShell。设置 `shell` 字段以明确选择。 shell 标记字符串、扩展变量并解释管道、`&&`、重定向和通配符。



**注释：**

在 Windows 上，exec 形式需要 `command` 解析为真正的可执行文件，例如 `.exe`。 `.cmd` 和 `.bat` 垫片 npm、npx、eslint 和 `node_modules/.bin` 中安装的其他工具不是可执行文件，没有 shell 就无法生成。要以 exec 形式运行它们，请直接使用 `node` 调用底层脚本，例如 `"command": "node", "args": ["${CLAUDE_PLUGIN_ROOT}/node_modules/eslint/bin/eslint.js"]`。 `node` 加脚本路径模式适用于每个平台，因为 `node.exe` 是真正的二进制文件。要按名称运行 `.cmd` 或 `.bat` 填充程序，请使用 shell 形式。


此示例运行与插件捆绑在一起的 Node 脚本。 Exec 形式将解析的脚本路径作为一个参数传递，不带引号：

```json theme={null}
{
  "type": "command",
  "command": "node",
  "args": ["${CLAUDE_PLUGIN_ROOT}/scripts/format.js", "--fix"]
}
```

等效的 shell 形式需要引用来处理带有空格或特殊字符的路径：

```json theme={null}
{
  "type": "command",
  "command": "node \"${CLAUDE_PLUGIN_ROOT}\"/scripts/format.js --fix"
}
```

两种形式都支持相同的 [ 路径占位符 ](<#按路径引用脚本>)，并且都将它们导出为生成进程上的环境变量 `CLAUDE_PROJECT_DIR`、`CLAUDE_PLUGIN_ROOT` 和 `CLAUDE_PLUGIN_DATA`，因此无论脚本如何启动，都可以读取 `process.env.CLAUDE_PLUGIN_ROOT`。

插件挂钩另外替换 [`${user_config.*}`](<https://code.claude.com/docs/en/plugins/manifest-reference#user-configuration>) 值，仅以 exec 形式：该值被替换为 `command` 和每个 `args` 元素作为纯字符串，因此没有 shell 重新解析它。

`command` 引用 `${user_config.*}` 的 shell 形式插件挂钩失败，并出现 [ 错误 ](<https://code.claude.com/docs/en/errors#plugin-command-references-user-config>)，而不是运行。要使用 shell 形式挂钩中的选项值，请读取 `$CLAUDE_PLUGIN_OPTION_<KEY>` 环境变量，例如 `webhook_url` 选项的 `$CLAUDE_PLUGIN_OPTION_WEBHOOK_URL`，或设置 `args` 将挂钩切换为 exec 形式。在v2.1.207之前，shell形式的插件挂钩命令也替代了`${user_config.*}`。



**注释：**

在 exec 形式中，`command` 只是可执行文件名称或路径。如果 `command` 是一个没有路径分隔符的裸名称，并且 `args` 旁边包含空格，则 Claude Code 会记录一条警告，因为生成将失败：没有名为 `node script.js` 的可执行文件。将多余的代币移入`args`。带空格的绝对路径（例如 `C:\Program Files\nodejs\node.exe`）是单个有效的可执行文件，不会触发警告。


#### HTTP 钩子字段

除了 [ 公共字段 ](<#公共字段>) 之外，HTTP 挂钩还接受以下字段：

|领域|必填|描述|
| :- | :- | :- |
| `url` |是的|URL 将 POST 请求发送至|
| `headers` |不|附加 HTTP 标头作为键值对。值支持使用 `$VAR_NAME` 或 `${VAR_NAME}` 语法进行环境变量插值。仅解析 `allowedEnvVars` 中列出的变量|
| `allowedEnvVars` |不|可以插入到标头值中的环境变量名称列表。对未列出变量的引用将替换为空字符串。任何环境变量插值都需要工作|
| `onFailure` |不|挂钩失败时操作会发生什么情况：`"continue"`（默认值）或 `"block"`。请参阅[挂钩失败时阻止操作](<#当钩子失败时阻止操作>)。需要 Claude Code v2.1.295 或更高版本|

Claude Code 将挂钩的 [JSON 输入 ](<#钩子输入输出>) 作为 POST 请求正文与 `Content-Type: application/json` 一起发送。响应正文使用与命令挂钩相同的 [JSON 输出格式 ](<#json-输出>)。

错误处理与命令挂钩不同；请参阅[HTTP响应处理](<#http响应处理>)。

此示例将 `PreToolUse` 事件发送到本地验证服务，使用 `MY_TOKEN` 环境变量中的令牌进行身份验证：

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "http",
            "url": "http://localhost:8080/hooks/pre-tool-use",
            "timeout": 30,
            "headers": {
              "Authorization": "Bearer $MY_TOKEN"
            },
            "allowedEnvVars": ["MY_TOKEN"]
          }
        ]
      }
    ]
  }
}
```

#### MCP 工具挂钩字段

除了 [ 公共字段 ](<#公共字段>) 之外，MCP 工具挂钩还接受以下字段：

|领域|必填|描述|
| :- | :- | :- |
| `server` |是的|已配置的MCP服务器的名称。对于 [ 插件捆绑服务器 ](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>)，这是范围名称 `plugin:<plugin-name>:<server-name>`，例如 `plugin:my-plugin:db`，而不是裸服务器密钥|
| `tool` |是的|在该服务器上调用的工具的名称|
| `input` |不|传递给工具的参数。字符串值支持从钩子的 [JSON 输入 ](<#钩子输入输出>) 替换 `${path}`，例如 `"${tool_input.file_path}"`|

此示例在每个 `Write` 或 `Edit` 之后调用 `my_server` MCP 服务器上的 `security_scan` 工具，并传递已编辑文件的路径：

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "mcp_tool",
            "server": "my_server",
            "tool": "security_scan",
            "input": { "file_path": "${tool_input.file_path}" }
          }
        ]
      }
    ]
  }
}
```

##### 如何读取工具的结果

Claude Code 读取工具的文本内容的方式与读取命令挂钩标准输出的方式相同，遵循 [退出代码下的解析规则 0](<#退出代码-0>)。如果工具返回 `isError: true`，挂钩会产生非阻塞错误并继续执行。

##### 当服务器仍在连接时

对于挂钩可以阻止或更改结果的事件，例如 `PreToolUse` 或 `Stop`，Claude Code 在调用该工具之前等待连接服务器，最多等待 [`MCP_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) 并在挂钩自己的 [`timeout`](<#公共字段>) 内。对于观察事件，例如 `Notification` 或 `SessionEnd`，它不会等待。

当钩子调用其工具时，显示 [`cached` 状态 ](<https://code.claude.com/docs/en/mcp#server-status-detail>) 的服务器将连接。如果此时服务器未连接，则挂钩会产生非阻塞错误并继续执行。该挂钩永远不会启动 OAuth 流程，因此 [ 首先从 `/mcp`](<https://code.claude.com/docs/en/mcp#authenticate-with-remote-mcp-servers>) 对服务器进行身份验证。

##### 在 MCP 服务器可用之前触发的事件

`SessionStart` 启动时，包括 `--continue` 或 `--resume`，并且在会话的 MCP 服务器可用于挂钩之前触发每个 `Setup` 事件。 Claude Code跳过其`mcp_tool`钩子而不调用该工具，并且[调试日志](<#调试钩子>)记录`mcp_tool hooks are not available for the 'SessionStart' hook event (no MCP client context)`，或命名为`Setup`的相同消息。当 `SessionStart` 稍后在会话中再次触发时，在 `/clear` 或压缩之后，其 `mcp_tool` 挂钩运行。对于会话在启动时需要的任何内容，请使用 `SessionStart` 上的 `type: "command"` 挂钩。

#### 提示和 agent 挂钩字段

除了 [ 公共字段 ](<#公共字段>) 之外，提示符和 agent 挂钩还接受以下字段：

|领域|必填|描述|
| :- | :- | :- |
| `prompt` |是的|提示文本发送给模型。使用 `$ARGUMENTS` 作为挂钩输入 JSON 的占位符。使用反斜杠转义以包含文字：`\$1.00` 呈现为 `$1.00`|
| `model` |不|用于评估的模型。默认为模型 Claude Code 用于 [ 后台功能 ](<https://code.claude.com/docs/en/costs#background-token-usage>)|

### 按路径引用脚本

使用这些占位符来引用相对于项目或插件根目录的钩子脚本，无论钩子运行时的工作目录如何：

* `${CLAUDE_PROJECT_DIR}`：会话开始的项目根目录。 Claude Code还在[stdio MCP服务器](<https://code.claude.com/docs/en/mcp#option-3-add-a-local-stdio-server>)和插件LSP服务器的环境中设置此变量。
* `${CLAUDE_PLUGIN_ROOT}`：插件的安装目录，用于与[插件](<https://code.claude.com/docs/en/plugins/overview>)捆绑的脚本。请参阅 [ 插件环境变量 ](<https://code.claude.com/docs/en/plugins/manifest-reference#environment-variables>) 了解路径在更新过程中的行为方式。
* `${CLAUDE_PLUGIN_DATA}`：插件的[持久数据目录](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>)，用于在插件更新后仍然存在的依赖项和状态。



**注释：**

**工作树不同。**如果Claude在会话期间进入[工作树](<https://code.claude.com/docs/en/worktrees>)，Claude Code将`${CLAUDE_PROJECT_DIR}`保留在原来的位置，并以不同的方式将工作树路径传递到您的挂钩：

  * **`${CLAUDE_PROJECT_DIR}` 保持不变**：它仍然指向会话开始的项目根目录，因此诸如 `${CLAUDE_PROJECT_DIR}/.claude/hooks/check-style.sh` 之类的命令仍然在主结帐中运行脚本。
  * **`cwd`跟随Claude**：钩子的[输入JSON](<#常用输入字段>)中的`cwd`字段是Claude进入工作树后的工作树根，Claude运行后的新目录`cd`。当钩子需要知道 Claude 正在哪个目录中工作时，请阅读它。



对于引用路径占位符的任何钩子，首选 [exec form](<#exec形式和shell形式>)。在 shell 形式中，将每个占位符用双引号引起来。



  

#### 项目脚本

此示例在任何 `Write` 或 `Edit` 工具调用后使用 `${CLAUDE_PROJECT_DIR}` 从项目的 `.claude/hooks/` 目录运行样式检查器：

    ```json theme={null}
    {
      "hooks": {
        "PostToolUse": [
          {
            "matcher": "Write|Edit",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/check-style.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```
  


  

#### 插件脚本

使用可选的顶级 `description` 字段在 `hooks/hooks.json` 中定义插件挂钩。启用插件后，它的挂钩会与您的用户和项目挂钩合并。

此示例运行与插件捆绑在一起的格式化脚本：

    ```json theme={null}
    {
      "description": "Automatic code formatting",
      "hooks": {
        "PostToolUse": [
          {
            "matcher": "Write|Edit",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format.sh",
                "args": [],
                "timeout": 30
              }
            ]
          }
        ]
      }
    }
    ```

有关创建插件挂钩的详细信息，请参阅[插件组件参考](<https://code.claude.com/docs/en/plugins/components#hooks>)。
  


### 挂钩技能和agents

除了设置文件和插件之外，还可以使用 frontmatter 在 [skills](<https://code.claude.com/docs/en/skills>) 和 [subagents](<https://code.claude.com/docs/en/sub-agents>) 中直接定义挂钩，其配置格式与基于设置的挂钩相同。 Claude Code 保持它们注册的时间取决于组件：

* **子代理挂钩**：Claude Code仅在该子代理运行时运行它们，并在完成时将其删除。 Claude Code 将此处的 `Stop` 挂钩转换为 `SubagentStop`，即子代理完成时触发的事件。
* **技能挂钩**：Claude Code在您或Claude调用技能时注册它们，并在会话的剩余时间内继续运行它们，以及在技能自己的回合后的回合中运行它们。要让 Claude Code 在第一次成功运行后删除钩子，请在其上设置 [`once: true`](<#公共字段>)。

此技能定义了一个 `PreToolUse` 挂钩，该挂钩在每个 `Bash` 命令之前运行安全验证脚本：

```yaml theme={null}
---
name: secure-operations
description: Perform operations with security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/security-check.sh"
---
```

子代理在其 YAML frontmatter 中使用相同的格式。

项目技能中的 Frontmatter 钩子遵循相同的方法 [工作区信任规则作为设置文件中的挂钩](<#工作空间信任>). Claude Code 当您或 Claude 调用该技能，包括在 `-p` 在您不信任的文件夹中运行。

仅当您接受 agent 文件所在文件夹的 [ 工作区信任对话框 ](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) 后，项目子代理中的 Frontmatter 挂钩才会运行。 `-p` 会话不算接受它。 [在信任文件夹之前运行的内容](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)将此与设置文件规则进行比较，子代理页面列出[哪些范围是豁免的](<https://code.claude.com/docs/en/sub-agents#hooks-in-subagent-frontmatter>)。在 v2.1.218 之前，这些挂钩可以从您不信任的文件夹运行。

### `/hooks` 菜单

在 Claude Code 中键入 `/hooks` 为您配置的挂钩打开只读浏览器。该列表标记每个挂钩的来源，例如用户设置、项目设置、本地设置、插件或当前会话。

选择一个挂钩以查看其运行内容及其定义位置的全文，例如其设置文件的路径或其插件的名称。

要浏览所有挂钩事件（包括未配置挂钩的事件），请选择列表末尾的 `All events`。

### 禁用或删除挂钩

要删除设置文件中定义的挂钩，请从该文件中删除其条目。

要暂时禁用所有挂钩而不删除它们，请在设置文件中设置 `"disableAllHooks": true`。 Claude Code 读取 [ 设置优先级 ](<https://code.claude.com/docs/en/settings#settings-precedence>) 应用后留下的值，因此项目 `.claude/settings.json` 中的 `"disableAllHooks": false` 会覆盖用户设置中的 `true`。无论项目设置如何，要关闭一次运行的挂钩，请传递 `--settings '{"disableAllHooks": true}'`，该设置优先于项目和本地设置。无法在将单个挂钩保留在配置中的同时禁用它。

`disableAllHooks` 设置遵循托管设置层次结构。如果管理员通过托管策略设置配置了挂钩，则在用户、项目或本地设置中设置的 `disableAllHooks` 无法禁用这些托管挂钩。只有在托管设置级别设置的 `disableAllHooks` 才能禁用托管挂钩。有关每个级别的完整范围，请参阅 [`disableAllHooks`](<https://code.claude.com/docs/en/settings-reference#disableallhooks>)。

对设置文件中的挂钩的直接编辑通常由文件观察器自动拾取。

## 钩子输入输出

命令挂钩通过 stdin 接收 JSON 数据，并通过退出代码、stdout 和 stderr 传达结果。 HTTP 挂钩接收与 POST 请求正文相同的 JSON，并通过 HTTP 响应正文传达结果。本节涵盖所有事件通用的字段和行为。 [挂钩事件](<#挂钩事件>)下的每个事件部分都包含其特定的输入模式和决策控制选项。

在 macOS 和 Linux 上，命令挂钩在自己的会话中运行，无需控制终端。挂钩进程和任何子进程无法打开 `/dev/tty` 或直接向 Claude Code 接口发送转义序列。 Windows 没有 `/dev/tty`。

要在任何平台上向用户显示消息，请在 JSON 输出中返回 [`systemMessage`](<#json-输出>)。有些事件会丢弃它或将其传递到其他地方，每个 [ 事件的部分 ](<#挂钩事件>) 都是这么说的。要触发桌面通知、设置窗口标题或按响铃，请改为返回 [`terminalSequence`](<#发出终端通知>)。

### 常用输入字段

除了每个 [ 挂钩事件 ](<#挂钩事件>) 部分中记录的事件特定字段之外，挂钩事件还接收这些字段作为 JSON。对于命令挂钩，此 JSON 通过标准输入到达。对于 HTTP 挂钩，它作为 POST 请求正文到达。

|领域|描述|
| :- | :- |
| `session_id` |当前会话标识符|
| `prompt_id` | UUID 识别当前正在处理的用户提示。匹配 [`prompt.id` 属性上 OpenTelemetry 事件](<https://code.claude.com/docs/en/monitoring-usage#event-correlation-attributes>)，因此您可以将挂钩输出与单个提示的遥测相关联。在第一个用户输入之前不存在 |
| `transcript_path` |对话路径 JSON。脚本文件是异步写入的，可能会滞后于内存中的对话，因此当钩子触发时，它可能尚未包含当前回合的最新消息。需要当前回合最终辅助文本的 Hook 应在 [Stop](<#停止>) 和 [SubagentStop](<#subagentstop>) 上使用 `last_assistant_message`，而不是读取脚本|
| `cwd` |调用钩子时的当前工作目录|
| `scratchpad_dir` |会话的 [ 暂存器目录 ](<https://code.claude.com/docs/en/claude-directory#session-scratchpad-directory>) 的路径，其中 Claude 保存临时工作文件。当会话没有暂存器或临时目录不可用时不存在。需要 Claude Code v2.1.257 或更高版本|
| `permission_mode` |当前[权限模式](<https://code.claude.com/docs/en/permissions#permission-modes>)：`"default"`、`"plan"`、`"acceptEdits"`、`"auto"`、`"dontAsk"`或`"bypassPermissions"`。标记为 **Manual** 的模式以 `"default"` 形式到达，而不是以 `"manual"` 形式到达，因此与 `"default"` 匹配的脚本继续工作。并非所有事件都会收到此字段。检查每个 [ 挂钩事件 ](<#挂钩事件>) 部分中的 JSON 示例|
| `effort` |具有 `level` 字段的对象，该对象在挂钩运行时保持有效的 [ 工作量级别 ](<https://code.claude.com/docs/en/model-config#adjust-effort-level>)：`"low"`、`"medium"`、`"high"`、`"xhigh"` 或`"max"`。如果您设置的活动模型不支持的级别，`level` 会报告运行的级别 Claude Code； [调整努力级别](<https://code.claude.com/docs/en/model-config#adjust-effort-level>)说明它如何选择该级别。该对象与 [ 状态行 ](<https://code.claude.com/docs/en/statusline#available-data>) `effort` 字段匹配。当当前模型支持工作量参数时，表示在工具使用上下文中触发的事件，例如 `PreToolUse`、`PostToolUse`、`Stop` 和 `SubagentStop`。该级别还可以作为 `$CLAUDE_EFFORT` 环境变量用于挂钩命令和 Bash 工具。|
| `hook_event_name` |触发的事件的名称|

当与 `--agent` 一起运行或在子代理内运行时，包含两个附加字段：

|领域|描述|
| :- | :- |
| `agent_id` |子代理的唯一标识符。仅当钩子在子代理调用内触发时才存在。使用它来区分子代理挂钩调用和主线程调用。|
| `agent_type` |Agent 名称（例如，`"Explore"` 或 `"security-reviewer"`）。当会话使用 `--agent` 或钩子在子代理内部触发时出现。对于子代理，子代理的类型优先于会话的 `--agent` 值。请参阅 [SubagentStart](<#subagentstart>) 了解自定义值和插件子代理报告以及如何针对插件范围名称编写匹配器。|

只有 [`SessionStart`](<#sessionstart>) 挂钩可以接收 `model` 字段，并且 Claude Code 并不总是包含它。 [`PreModelSwitch`](<#premodelswitch>) 和 [`PostModelSwitch`](<#postmodelswitch>) 挂钩接收 `from_model` 和 `to_model`，因此请使用 PostModelSwitch 挂钩来跟踪模型在会话期间发生的变化。

没有 `$CLAUDE_MODEL` 环境变量。如果您在 shell 中设置该钩子，则它可以读取 `$ANTHROPIC_MODEL`，但是当您在会话期间使用 `/model` 切换模型时，该值不会更改。

钩子进程继承父环境，除了Claude Code [从它生成的每个子进程中删除的`OTEL_*`导出器变量](<https://code.claude.com/docs/en/monitoring-usage#administrator-configuration>)，以及当[`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars#variables>)设置为`1`时，它删除的变量。在 [ 获取 HIPAA 配置 ](<https://code.claude.com/docs/en/hipaa-setup#check-how-developers-sign-in-and-connect>) 的会话中，Claude Code 还 [ 从挂钩环境中删除 Anthropic 凭据 ](<https://code.claude.com/docs/en/hipaa-setup#anthropic-credentials-in-commands-hooks-and-mcp-servers>)。

例如，Bash 命令的 `PreToolUse` 钩子在标准输入上接收以下内容：

```json theme={null}
{
  "session_id": "abc123",
  "prompt_id": "550e8400-e29b-41d4-a716-446655440000",
  "transcript_path": "/home/user/.claude/projects/.../transcript.jsonl",
  "cwd": "/home/user/my-project",
  "scratchpad_dir": "/tmp/claude-1000/-home-user-my-project/abc123/scratchpad",
  "permission_mode": "default",
  "hook_event_name": "PreToolUse",
  "tool_name": "Bash",
  "tool_input": {
    "command": "npm test",
    "description": "Run test suite",
    "timeout": 120000,
    "run_in_background": false
  },
  "tool_use_id": "toolu_01ABC123..."
}
```

`tool_name`、`tool_input` 和 `tool_use_id` 字段是特定于事件的。每个 [ 挂钩事件 ](<#挂钩事件>) 部分都记录了该事件的附加字段。

### 退出代码输出

您的钩子的退出代码告诉Claude Code是否继续触发钩子的操作，例如工具调用或提示。完成的运行会产生以下三种结果之一：

* **成功**：你的钩子退出0。 Claude Code 应用您的钩子打印的任何 [JSON 输出 ](<#json-输出>) 字段，并且操作将继续进行，除非这些字段阻止或拒绝它。
* **阻塞错误**: 你的钩子退出 2。开 [可以阻止的事件](<#每个事件的退出代码-2-行为>), Claude Code 停止动作。
* **非阻塞错误**：您的钩子因任何其他代码而退出，或以其他方式失败，例如未启动或打印无效JSON。操作继续进行，对于 `PreToolUse` 等事件，您会在记录中看到 `<hook name> hook error` 通知。如果您希望失败的挂钩阻止操作，请设置[`onFailure: "block"`](<#当钩子失败时阻止操作>)。

你的钩子打印到标准输出的内容可以改变结果。例如，如果 `PreToolUse` 挂钩退出 1 但打印通过验证的 JSON，则运行成功，并且 JSON 字段决定会发生什么。要查找诸如 `PreToolUse` 之类的事件的钩子结果，请将其在第一列中打印到 stdout 的内容与其顶部的退出代码进行匹配：

|标准输出|退出0|退出2|任何其他退出代码|
| :- | :- | :- | :- |
|JSON 通过 [ 架构验证的对象 ](<#json-输出>)|成功。字段适用|阻塞错误。 Claude Code 仍然读取字段，但无法覆盖该块|成功。 Claude Code 忽略退出代码，由字段单独决定。对于 [`onFailure: "block"`](<#当钩子失败时阻止操作>)，这算作失败|
|JSON [ 无法解析 ](<#退出代码-0>) 或架构验证失败|非阻塞错误。通知携带解析或验证消息|阻塞错误。你的stderr就是原因|非阻塞错误。通知携带解析或验证消息|
|[纯文本](<#退出代码-0>)，或者什么都没有|成功|阻塞错误。你的stderr就是原因|非阻塞错误。该通知包含 stderr 的第一行|

有些事件有自己的规则：

* **`WorktreeCreate`**：任何非零退出代码都会导致工作树创建失败，无论您的JSON说什么。
* **`WorktreeRemove`**：如果目录随后仍然存在，任何非零退出代码都会导致工作树删除失败。
* **`Stop`、`SubagentStop`、`TaskCompleted` 和插件的 `UserPromptSubmit` 钩子 **：当你的钩子退出 2 时 stdout 上没有任何内容，并且其 stderr 表示文件丢失，例如`No such file or directory`、Claude Code 将运行视为非阻塞错误。
* **`Elicitation` 和 `ElicitationResult`**：Claude Code 当您的钩子退出 0 时应用您的 `hookSpecificOutput`，并在任何其他退出代码上忽略它。
* **丢弃钩子输出的事件，例如`StopFailure`**：Claude Code会忽略任何退出代码上的JSON，除了仍然会触发的副作用字段（例如`terminalSequence`）。

要检查退出代码 2 对您的事件执行的操作，请参阅 [每个事件的退出代码 2 行为 ](<#每个事件的退出代码-2-行为>)。要检查它遵循哪些决策字段，请参阅[决策控制](<#决策控制>)。

#### 退出代码 0

退出0表示成功，并且是当您打印JSON进行结构化控制时预期的退出代码。

对于大多数事件，Claude Code 将 stdout 写入调试日志，并且不在记录中显示它。例外情况是 `UserPromptSubmit`、`UserPromptExpansion`、`SessionStart` 和 `PostModelSwitch`，其中 Claude Code 添加纯文本标准输出作为 Claude 可以查看和操作的上下文。

Claude Code 将标准输出读取为 [JSON 输出 ](<#json-输出>) 还是纯文本取决于它的开始和结束方式，忽略周围的空格：

* **以`{`开头，以`}`**结尾：Claude Code将其解析为JSON。当输出是两行或多行，每行都单独解析为 JSON，并且没有行是设置字段的 [JSON 输出 ](<#json-输出>) 对象时，Claude Code 会将整个输出视为纯文本。当其中一行确实设置了一个字段时，整个输出就是解析失败。
* **以 `{` 开头，但不以 `}`** 结尾：Claude Code 将其视为纯文本。
* **从其他内容开始**：Claude Code将其视为纯文本，包含JSON数组或带引号的JSON字符串。

当 Claude Code 尝试将标准输出解析为 JSON 但无法解析，或者解析的对象失败 [ 架构验证 ](<#json-输出>) 时，运行会出现 [ 非阻塞错误 ](<#退出代码输出>)。 `<hook name> hook error`通知携带解析或验证消息。在添加纯文本标准输出作为上下文的事件中，Claude Code 不会添加无法解析的标准输出。

Claude 永远不会从退出 0 的钩子中看到 stderr。要亲自阅读 `PreToolUse` 等事件，请启用 [ 调试日志记录 ](<#调试钩子>)。要从 `PostToolUse` 或 `PostToolUseFailure` 挂钩向 Claude 发出警告，请退出 2，以便 [Claude 看到 stderr](<#每个事件的退出代码-2-行为>)，即使该工具已经运行。

#### 退出代码 2

使用代码退出 2 来阻止该动作。开 [可以阻止的事件](<#每个事件的退出代码-2-行为>), Claude Code 停止动作：a `PreToolUse` 例如，hook 会阻止工具调用，并且 `UserPromptSubmit` hook 拒绝提示。

该块附带的消息是您的钩子的 stderr。如果您的钩子还打印了做出阻止决定的 JSON，则 Claude Code 将使用该决定的原因。

即使你的钩子打印出 JSON，退出 2 也会被阻止：

* 通过模式验证的 **JSON **：Claude Code 仍然读取 [JSON 输出 ](<#json-输出>) 字段，但它们无法覆盖该块。即使是 `"allow"` 的 `permissionDecision` 也不会让该操作通过。在 `Elicitation` 和 `ElicitationResult` 上，退出 2 挂钩的 `hookSpecificOutput` 被忽略。
* **JSON 架构验证失败**：钩子仍然阻塞。 Claude Code 使用您的 stderr 作为阻塞原因，并在调试日志中记录验证失败。

此脚本通过退出 2 来阻止 `rm` 命令，并将所有其他命令留给正常权限流：

```bash theme={null}
#!/bin/bash
# Reads JSON input from stdin, checks the command
input=$(cat)
command=$(jq -r '.tool_input.command' <<<"$input")

if [[ "$command" == rm* ]]; then
  echo "Blocked: rm commands are not allowed" >&2
  exit 2  # Blocking error: tool call is prevented
fi

exit 0  # No decision: the normal permission flow applies
```

将此脚本注册为 `Bash` 上的 `PreToolUse` 挂钩后，以 `rm` 开头的命令将被阻止，并且 Claude 接收挂钩的 stderr 作为工具的错误，前缀为事件名称、工具名称和挂钩的命令：

```text theme={null}
PreToolUse:Bash hook error: [${CLAUDE_PROJECT_DIR}/.claude/hooks/no-rm.sh]: Blocked: rm commands are not allowed
```

#### 其他退出代码

当您的钩子以 0 或 2 以外的代码退出并向标准输出打印纯文本或不打印任何内容时，运行是一个 [ 非阻塞错误 ](<#退出代码输出>)。您会在记录中看到 `<hook name> hook error` 通知，其中包含 `Failed with non-blocking status code:` 和钩子 stderr 的第一行。例如，当 `Bash` 上的 `PreToolUse` 挂钩将 `something broke` 打印到 stderr 并退出 1 时，`PreToolUse:Bash hook error` 通知将包含以下行：

```text theme={null}
Failed with non-blocking status code: something broke
```

要捕获完整的 stderr 而不是其第一行，请启用 [ 调试日志记录 ](<#调试钩子>)。

无法启动的钩子也是非阻塞错误。在 shell 形式中，当脚本路径不存在或不可执行时，shell 会以类似 127 的代码退出，并且通知会携带解释器的消息，例如 `Failed with non-blocking status code: /bin/sh: /path/to/hook.sh: No such file or directory`。当您设置策略挂钩时，请在首次运行时注意此通知，因为 `settings.json` 中的路径输入错误意味着该挂钩永远不会运行。要阻止该操作，请设置 [`onFailure: "block"`](<#当钩子失败时阻止操作>)。



**注意：**

如果标准输出上没有有效的 JSON，Claude Code 会将退出代码 1 视为非阻塞错误，即使 1 是传统的 Unix 故障代码。如果您的挂钩旨在强制执行策略，请使用 `exit 2`。


#### 超时

除了使用 [`async: true`](<#在后台运行钩子>) 运行的命令挂钩之外，Claude Code 还会取消到达其 [`timeout`](<#公共字段>) 的 `command`、`http` 或 `mcp_tool` 挂钩，并丢弃挂钩的输出，因此在大多数情况下，超时挂钩不会做出任何决定。

在 [`PreModelSwitch`](<#premodelswitch>) 上，超时取消的挂钩会阻止模型切换。在 `PreToolUse` 上，两个钩子系列有所不同：

* 超时的 `command`、`http` 或 `mcp_tool` 挂钩不会阻止工具调用。调用将通过正常的 [ 权限流 ](<https://code.claude.com/docs/en/permissions>) 继续，因此不要指望停止的钩子充当门。要在 `command` 或 `http` 摘机超时时阻止呼叫，请设置 [`onFailure: "block"`](<#当钩子失败时阻止操作>)。
* 安 [Agent SDK 回调钩子](<https://code.claude.com/docs/en/agent-sdk/hooks>) 超过其超时时间 [阻止工具调用](<#pretooluse>).

#### 当钩子失败时阻止操作

在大多数事件中，当挂钩失败或超时时，Claude Code 仍会执行操作，因此路径错误或崩溃脚本的策略挂钩会让一切顺利通过。要阻止该操作，请在 `command` 或 `http` 挂钩上设置 `"onFailure": "block"`。默认值为`"continue"`。需要 Claude Code v2.1.295 或更高版本。

`.claude/settings.json` 中的这个 `PreToolUse` 挂钩在每个 Bash 命令之前运行一个项目脚本，并在脚本失败时阻止该命令：

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "node",
            "args": ["${CLAUDE_PROJECT_DIR}/.claude/hooks/check-command.js"],
            "onFailure": "block"
          }
        ]
      }
    ]
  }
}
```

要测试它，请保留 `check-command.js` 缺失，并要求 Claude 运行 Bash 命令，例如 `ls`。 Claude Code 阻止调用，错误包括 `failed; blocking because onFailure is "block"`，后跟节点自己的错误输出，此处修剪为一行：

```text theme={null}
PreToolUse:Bash hook error: [node ${CLAUDE_PROJECT_DIR}/.claude/hooks/check-command.js]: failed; blocking because onFailure is "block"
Error: Cannot find module '/path/to/project/.claude/hooks/check-command.js'
```

超时后，消息显示 `timed out` 而不是 `failed`。如果没有设置 `onFailure`，相同的缺失脚本是非阻塞错误并且 `ls` 会运行。

其中每一项都算作失败：

* **无法启动**：命令挂钩无法启动，例如因为脚本或可执行文件不存在
* **除 0 或 2** 之外的退出代码：即使打印了允许操作的 JSON，例如 `permissionDecision: "allow"`，也对命令挂钩进行计数。要返回 JSON 决定，请退出 0
* **HTTP错误**：HTTP挂钩连接失败，或者响应状态不是2xx
* **超时**：钩子到达其[`timeout`](<#公共字段>)
* **无效输出**：JSON输出[无法解析](<#退出代码-0>)或失败[架构验证](<#json-输出>)。对于 HTTP 钩子，既不是空的 2xx 主体也不是 JSON 对象。来自命令挂钩的纯文本标准输出并不是失败

设置 `"block"` 后，失败会执行 [ 退出代码 2 对事件 ](<#每个事件的退出代码-2-行为>) 执行的操作，但 `PermissionRequest` 除外，它会拒绝请求。例如，`PreToolUse` 故障会阻止工具调用，`UserPromptSubmit` 故障会阻止提示。

该字段对这些钩子没有影响：

* **`Stop`、`SubagentStop`、`TaskCompleted` 和 `TeammateIdle` 挂钩 **：这些事件的退出代码 2 发送 Claude 返回以继续工作，并且 Claude无法修复无法运行的钩子
* **后台命令挂钩**：设置[`async`或`asyncRewake`](<#在后台运行钩子>)的命令挂钩

#### 每个事件的退出代码 2 行为

退出代码 2 是钩子发出“停止，不要这样做”信号的方式。效果取决于事件，因为有些事件代表可以阻止的操作（例如尚未发生的工具调用），而其他事件则代表已经发生或无法阻止的事情。

|挂钩事件|可以拦截吗？|退出时会发生什么2|
| :- | :- | :- |
| `PreToolUse` |是的|阻止工具调用|
| `PermissionRequest` |否|此事件不支持退出代码 2，并且权限流程将保持不变。通过[`decision`对象](<#permissionrequest决策控制>)来拒绝|
| `UserPromptSubmit` |是的|阻止提示，因此它永远不会到达 Claude。请参阅 [被阻止的提示会留下什么](<#被阻止的提示会留下什么>)|
| `UserPromptExpansion` |是的|阻止扩展|
| `Stop` |是的|阻止 Claude 停止，继续对话|
| `SubagentStop` |是的|防止子代理停止|
| `TeammateIdle` |是的|防止队友闲置，使其继续工作|
| `TaskCreated` |是的|回滚任务创建|
| `TaskCompleted` |是的|防止任务被标记为已完成|
| `ConfigChange` |是的|阻止配置更改生效（`policy_settings` 除外）|
| `StopFailure` |否|输出和退出代码被忽略，`terminalSequence` 除外|
| `PostToolUse` |否|将 stderr 显示为 Claude；该工具已经运行|
| `PostToolUseFailure` |否|将 stderr 显示为 Claude；该工具已经失败|
| `PostToolBatch` |是的|在下一次模型调用之前停止代理循环|
| `PermissionDenied` |否|退出代码和 stderr 将被忽略，因为拒绝已经发生。使用 JSON `hookSpecificOutput.retry: true` 告诉模型可以重试； Claude Code 忽略 `retry: true` 的 [ 无判决否认 ](<#permissiondenied决策控制>)|
| `Notification` |否|退出代码和 stderr 被忽略|
| `SubagentStart` |否|仅向用户显示 stderr|
| `SessionStart` |否|仅向用户显示 stderr|
| `Setup` |否|退出代码和 stderr 被忽略|
| `SessionEnd` |否|仅向用户显示 stderr|
| `CwdChanged` |否|仅向用户显示 stderr|
| `DirectoryAdded` |否|Stderr 转到调试日志；目录已经添加了|
| `FileChanged` |否|仅向用户显示 stderr|
| `PreCompact` |是的|块压缩|
| `PostCompact` |否|仅向用户显示 stderr|
| `PreModelSwitch` |是的|阻止模型切换并向用户显示 stderr|
| `PostModelSwitch` |否|仅向用户显示 stderr；模型已经切换了|
| `Elicitation` |是的|拒绝请求，并且不出现对话框|
| `ElicitationResult` |是的|阻止响应（行动变为拒绝）|
| `WorktreeCreate` |是的|任何非零退出代码都会导致工作树创建失败|
| `WorktreeRemove` |是的|如果目录随后仍然存在，任何非零退出代码都会导致工作树删除失败。请参阅 [WorktreeRemove](<#worktreeremove>) 了解目录发生的情况|
| `InstructionsLoaded` |否|退出代码被忽略|
| `MessageDisplay` |否|显示原文|

对于 `SessionStart`、`SubagentStart` 和 `PostModelSwitch`，Claude Code 将记录中的退出代码 2 stderr 呈现为 `<hook name> hook error` 通知，与呈现 [ 非阻塞的方式相同错误](<#退出代码输出>)。 Claude 没有看到它，并且会话或子代理继续进行。对于 `SubagentStart`，通知出现在子代理自己的记录中，而不是出现在父对话中。

### HTTP响应处理

HTTP 挂钩使用 HTTP 状态代码和响应主体，而不是退出代码和标准输出。以下结果适用于大多数赛事； [每个事件表](<#每个事件的退出代码-2-行为>)中具有自己的失败合约的事件，例如`WorktreeCreate`，也将该合约应用于失败的HTTP挂钩：

* **2xx，主体为空**：成功，相当于退出代码0，没有输出
* **2xx 具有 JSON 对象主体 **：使用与命令挂钩相同的 [JSON 输出 ](<#json-输出>) 模式进行解析。架构验证失败的主体是非阻塞错误
* **2xx 与任何其他正文，例如纯文本**：非阻塞错误，处理方式与非 2xx 状态相同。 Claude Code 未将文本添加到 Claude 的上下文中
* **非2xx状态**：非阻塞错误，继续执行
* **连接失败**：非阻塞错误，继续执行
* **超时**：挂钩被取消，如[超时](<#超时>)中所述

HTTP 挂钩无法仅通过状态代码发出阻塞错误信号：非 2xx 状态或连接失败是 [ 非阻塞错误 ](<#退出代码输出>)。要阻止工具调用或拒绝权限，请返回一个 2xx 响应，其中包含包含适当决策字段的 JSON 正文。要在请求失败或返回非 2xx 状态时阻止操作，请设置 [`onFailure: "block"`](<#当钩子失败时阻止操作>)。

### JSON 输出

退出代码只能让您阻止或保持沉默，但 JSON 输出可以为您提供更细粒度的控制。不要使用代码 2 退出来阻止，而是退出 0 并将 JSON 对象打印到标准输出。 Claude Code 从 JSON 读取特定字段来控制行为，包括用于阻止、允许或升级给用户的 [ 决策控制 ](<#决策控制>)。



**注释：**

每个钩子选择一种方法：要么单独使用退出代码进行信号发送，要么退出 0 并打印 JSON 以进行结构化控制。如果混合使用它们，退出 2 会保留其 [ 阻止效果 ](<#每个事件的退出代码-2-行为>)，并且 Claude Code 仍会读取 JSON 字段，并在 [ 退出代码 2](<#退出代码-2>) 下注明一个引发异常。


您的钩子的标准输出必须仅包含 JSON 对象。如果您的 shell 配置文件在启动时打印文本，则可能会干扰 JSON 解析。请参阅故障排除指南中的 [挂钩 JSON 无效 ](<https://code.claude.com/docs/en/hooks-guide#hook-json-has-no-effect>)。

钩子的 `additionalContext`、`systemMessage` 和 `initialUserMessage` 字符串及其普通标准输出的上限为 10,000 字符：

* **范围**：Claude Code单独测量每个字符串，即使多个钩子针对同一事件运行也是如此。对于JSON输出，每个场单独测量；普通标准输出是整体测量的。
* **超出限制**：Claude Code 将输出保存到会话目录中的文件中，并将其替换为文件路径和最多前 2,000 个字符的预览。大型有效 Bash 结果的处理方式相同，如 [输出限制 ](<https://code.claude.com/docs/en/tools-reference#output-limits>) 中所述。与 Bash 上限不同，此上限没有设置或环境变量来提高它。
* **读取文件**：Claude Code不会要求Claude读取文件，因此将Claude必须始终看到的任何内容保留在上限内。

JSON对象支持三种字段：

* **通用字段**如`continue`列于下表中。每个事件都接受它们，但有些事件会丢弃它们或将 `systemMessage` 传递到转录本以外的其他地方。每个活动的部分都这么说。 `terminalSequence` 也适用于这些事件，但 [ 发出终端通知 ](<#发出终端通知>) 下列出的例外情况。
* **顶级`decision`和`reason`**被某些事件用来阻止或提供反馈。
* **`hookSpecificOutput`** 是需要更丰富控制的事件的嵌套对象。它需要将 `hookEventName` 字段设置为事件名称。

|领域|默认|描述|
| :- | :- | :- |
| `continue` | `true` |如果 `false`，Claude 在钩子运行后完全停止处理。优先于任何特定于事件的决策字段|
| `stopReason` |无|当 `continue` 为 `false` 时向用户显示的消息。它保留在对话中，因此如果对话继续，Claude 就会看到它|
| `suppressOutput` | `false` |无效：Claude Code 接受该字段，但不对其进行操作。成功的钩子的标准输出永远不会显示在脚本中，而是记录在调试日志中|
| `systemMessage` |无|向用户显示警告消息。在 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) 和 [`--output-format stream-json`](<https://code.claude.com/docs/en/headless>) 输出中，它可以作为 [`SDKInformationalMessage`](<https://code.claude.com/docs/en/agent-sdk/typescript#sdkinformationalmessage>) 到达|
| `terminalSequence` |无|Claude Code 代表您发出的终端转义序列，例如桌面通知、窗口标题或响铃。仅限于OSC、`0`/`1`/`2`/`9`/`99`/`777`和BEL。如果该值包含允许列表之外的任何内容，则该字段将被忽略。使用它而不是写入`/dev/tty`，这对于钩子是不可用的|

要完全停止 Claude：

```json theme={null}
{ "continue": false, "stopReason": "Build failed, fix errors before continuing" }
```

对于 `PreToolUse` 和 `PostToolUse` 挂钩，即使工具调用失败或完成但 Claude 仍在传输响应，停止也会适用。

#### 发出终端通知

挂钩在没有控制终端的情况下运行，因此直接将转义序列写入 `/dev/tty` 会失败。相反，在 `terminalSequence` 字段中返回转义序列，并且 Claude Code 通过其自己的终端写入路径为您发出它。这是无竞争的，在 tmux 和 GNU 屏幕内工作，并且在没有 `/dev/tty` 的 Windows 上工作。

该字段接受一个或多个允许转义序列的字符串：

* OSC `0`、`1`、`2`：窗口和图标标题
* OSC `9`：iTerm2、ConEmu、Windows 终端和 WezTerm 通知，包括 `9;4` 任务栏进度
* OSC `99`：Kitty 通知
* OSC `777`：urxvt、Ghostty 和 Warp 通知
* 裸露BEL

序列可以用 BEL 或 ST 终止。允许列表之外的任何内容，包括 CSI cursor 和颜色序列、OSC 调色板序列、OSC 8 超链接、OSC 52 剪贴板写入，以及OSC 1337 被拒绝，并且该字段被忽略。

Claude Code 在处理钩子的输出时自行写入序列，因此该字段适用于丢弃 `systemMessage` 和 `continue` 的事件，例如 `Notification` 和 `StopFailure`。它有两个限制：

* Claude Code 仅在交互式会话中且仅当其界面显示在屏幕上时写入序列。在具有 `-p` 标志和 Agent SDK 的非交互模式下，它会忽略该字段。
* `WorktreeCreate` 命令挂钩无法返回 JSON，因为 Claude Code 将其标准输出读取为工作树路径。 HTTP `WorktreeCreate` 挂钩返回 JSON 并且可以包含该字段。

下面的示例从 `Notification` 挂钩触发桌面通知。转义序列是使用 `printf` 八进制转义构建的，因此控制字节永远不会出现在 shell 命令行上，并且 `jq -n --arg` 构建 JSON 输出，以便正确转义通知消息中的引号、反斜杠和换行符：

```bash theme={null}
#!/bin/bash
# Notification hook: ping the desktop when Claude Code needs attention.
input=$(cat)
title="Claude Code"
body=$(jq -r '.message // "Needs your attention"' <<<"$input")
seq=$(printf '\033]777;notify;%s;%s\007' "$title" "$body")
jq -nc --arg seq "$seq" '{terminalSequence: $seq}'
```

`{ "terminalSequence": "..." }` 形状对于任何 shell 或语言都是相同的。

#### 添加 Claude 的上下文

`additionalContext` 字段将字符串从钩子传递到 Claude 的上下文窗口中。 Claude Code 将字符串包装在 [ 系统提醒 ](<https://code.claude.com/docs/en/glossary#system-reminder>) 中，并将其插入到对话中钩子触发的位置。 Claude 读取下一个模型请求的提醒，但它不会在界面中显示为聊天消息。

返回 `hookSpecificOutput` 内的 `additionalContext` 以及事件名称：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "This file is generated. Edit src/schema.ts and run `bun generate` instead."
  }
}
```

提醒出现的位置取决于事件：

* [SessionStart](<#sessionstart>) 和 [SubagentStart](<#subagentstart>)：在对话开始时，第一个提示之前
* [UserPromptSubmit](<#userpromptsubmit>) 和 [UserPromptExpansion](<#userpromptexpansion>)：在提交的提示旁边
* [PreToolUse](<#pretooluse>)、[PostToolUse](<#posttooluse>)、[PostToolUseFailure](<#posttoolusefailure>) 和 [PostToolBatch](<#posttoolbatch>)：工具结果旁边
* [停止](<#停止>)和[SubagentStop](<#subagentstop>)：在回合结束时。对话仍在继续，以便 Claude 可以根据反馈采取行动。参见[停止决策控制](<#停止决策控制>)
* [PostModelSwitch](<#postmodelswitch>)：与切换后的下一个请求一起。参见[PostModelSwitch决策控制](<#postmodelswitch决策控制>)了解时序

当多个挂钩针对同一事件返回 `additionalContext` 时，Claude 会接收所有值。

如果值超过 10,000 个字符，Claude Code 会将文本写入会话目录中的文件，并传递 Claude 文件路径，并预览最多前 2,000 个字符。 Claude 可以读取该文件，但 Claude Code 不要求它这样做。

使用 `additionalContext` 获取信息 Claude 应该了解您的环境的当前状态或刚刚运行的操作：

* **环境状态**：当前分支、部署目标或活动功能标志
* **条件项目规则**：哪个测试命令适用于刚刚编辑的文件，哪些目录在此工作树中是只读的
* **外部数据**：分配给您的未决问题、最近的CI结果、从内部服务获取的内容

对于永不改变的指令，首选 [CLAUDE.md](<https://code.claude.com/docs/en/memory>)。它无需运行脚本即可加载，是静态项目约定的标准位置。

将文本写成事实陈述而不是命令式系统指令。诸如“部署目标是生产”或“此存储库使用 `bun test`”之类的措辞读取为项目信息。作为带外系统命令的文本可以触发 Claude 的提示注入防御，这会导致 Claude 向您显示文本，而不是将其视为上下文。

Claude Code 将注入的文本保存在会话记录中。对于 `PostToolUse` 或 `UserPromptSubmit` 等会话中事件，当您使用 `--continue` 或 `--resume` 恢复时，Claude Code 会重播已保存的文本，而不是重新运行过去轮次的挂钩，因此时间戳或提交 SHA 等值会变得过时。 `SessionStart` 挂钩在恢复时再次运行，并将 `source` 设置为 `"resume"` 或 `"fork"`（如果添加了 `--fork-session`），以便它们可以刷新其上下文。

#### 决策控制

并非每个事件都支持通过 JSON 阻止或控制行为。每个执行的事件都使用一组不同的字段来表达该决定。在编写钩子之前，请使用此表作为快速参考：

|活动|决策模式|关键领域|
| :- | :- | :- |
|UserPromptSubmit、UserPromptExpansion、PostToolUse、PostToolUseFailure、PostToolBatch、停止、SubagentStop、ConfigChange、PreCompact|顶级`decision`|`decision: "block"`、`reason`。停止并且 SubagentStop 也接受 `hookSpecificOutput.additionalContext` 的 [ 继续对话的非错误反馈 ](<#停止决策控制>)|
| TeammateIdle, TaskCompleted |退出代码或 `continue: false`| 退出代码 2 通过 stderr 反馈阻止该操作。 JSON `{"continue": false, "stopReason": "..."}` 也完全阻止了队友，匹配 `Stop` 钩子行为； [TaskCompleted 当 `TaskUpdate` 工具触发了事件](<#taskcompleted决策控制>) |
| TaskCreated |退出代码或顶级 `decision`|退出代码 2 或 `decision: "block"` [ 取消任务 ](<#taskcreated决策控制>) 并将消息返回到 Claude。 `continue: false` 被忽略|
| PreToolUse | `hookSpecificOutput` | `permissionDecision` (allow/deny/ask/defer), `permissionDecisionReason` |
| PreModelSwitch |`hookSpecificOutput` 或顶级 `decision`|`permissionDecision`（allow/deny/ask），`permissionDecisionReason`。 `decision: "block"`还[取消开关](<#premodelswitch决策控制>)|
| PermissionRequest | `hookSpecificOutput` | `decision.behavior` (allow/deny) |
| PermissionDenied | `hookSpecificOutput` |`retry: true` 告诉模型它可以重试被拒绝的工具调用； Claude Code 忽略它，因为 [ 无判决否认 ](<#permissiondenied决策控制>)|
| WorktreeCreate |路径返回|命令钩子在标准输出上打印路径； HTTP 挂钩返回 `hookSpecificOutput.worktreePath`。挂钩失败或路径丢失导致创建失败|
| WorktreeRemove |退出代码|如果目录随后仍然存在，任何非零退出代码都会导致删除失败。 JSON 输出被丢弃|
|启发，ElicitationResult|`hookSpecificOutput` 或顶级 `decision`|`action` (accept/decline/cancel)、`content`（表单字段值）。 `decision: "block"` 也 [ 拒绝 ](<#拒绝诱导的其他方法>)|
| MessageDisplay | `hookSpecificOutput` |`displayContent` 替换屏幕上显示的文本。仅显示：文字记录和 Claude 看到的内容保留原始内容|
| SessionStart, SubagentStart, PostModelSwitch |仅上下文|`hookSpecificOutput.additionalContext` 添加了 Claude 的上下文。 SessionStart 还接受 [`initialUserMessage`、`watchPaths`、`sessionTitle` 和 `reloadSkills`](<#sessionstart决策控制>)。无阻塞或决策控制|
|设置、通知、SessionEnd、PostCompact、InstructionsLoaded、StopFailure、CwdChanged、DirectoryAdded、FileChanged|无|没有决策控制。用于日志记录或清理等副作用|

一些事件还可以重写内容，而不仅仅是允许或阻止它：

* `PreToolUse`：`hookSpecificOutput` 正下方的 `updatedInput` 在运行之前替换工具的参数。参见[PreToolUse决策控制](<#pretooluse决策控制>)
* `PermissionRequest`：`decision`对象内的`updatedInput`。参见[PermissionRequest决策控制](<#permissionrequest决策控制>)
* `PostToolUse`：`updatedToolOutput` 替换工具的结果。参见[PostToolUse决策控制](<#posttooluse决策控制>)
* `UserPromptSubmit`：无法替换提示；它只在旁边注入 `additionalContext`

对于编辑或转换用例，对于出站工具输入，在 `PreToolUse` 处拦截，对于入站工具结果，在 `PostToolUse` 处拦截。

以下是每种模式的实际应用示例：



  

#### 高层决策

`decision` 的唯一值是 `"block"`。要允许操作继续进行，请从 JSON 中省略 `decision`，或者退出 0，根本不添加任何 JSON：

    ```json theme={null}
    {
      "decision": "block",
      "reason": "Test suite must pass before proceeding"
    }
    ```
  


  

#### PreToolUse

使用 `hookSpecificOutput` 进行更丰富的控制：允许、拒绝或升级给用户。您还可以在运行之前修改工具输入或为 Claude 注入附加上下文。请参阅 [PreToolUse 决策控制 ](<#pretooluse决策控制>) 了解完整的选项集。

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "deny",
        "permissionDecisionReason": "Database writes are not allowed"
      }
    }
    ```
  


  

#### PermissionRequest

使用 `hookSpecificOutput` 代表用户允许或拒绝权限请求。允许时，您还可以修改工具的输入或应用权限规则，以便不会再次提示用户。请参阅 [PermissionRequest 决策控制 ](<#permissionrequest决策控制>) 了解完整的选项集。

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PermissionRequest",
        "decision": {
          "behavior": "allow",
          "updatedInput": {
            "command": "npm run lint"
          }
        }
      }
    }
    ```
  


有关 Bash 命令验证、提示过滤和自动批准脚本等扩展示例，请参阅指南中的 [您可以自动化执行的操作 ](<https://code.claude.com/docs/en/hooks-guide#what-you-can-automate>) 和 [Bash 命令验证器参考实现 ](<https://github.com/anthropics/claude-code/blob/main/examples/hooks/bash_command_validator_example.py>)。

## 挂钩事件

每个事件对应于 Claude Code 生命周期中钩子可以运行的点。以下部分的顺序与生命周期相匹配：从会话设置到代理循环再到会话结束。每个部分都描述事件何时触发、它​​支持哪些匹配器、它接收的 JSON 输入以及如何通过输出控制行为。

### SessionStart

当 Claude Code 启动新会话或恢复现有会话时运行。对于加载开发上下文（例如现有问题或最近对代码库的更改）或设置环境变量很有用。对于不需要脚本的静态上下文，请改用 [CLAUDE.md](<https://code.claude.com/docs/en/memory>)。

SessionStart 在每个会话上运行，因此请保持这些挂钩快速。仅支持 `type: "command"` 和 `type: "mcp_tool"` 挂钩。请参阅 [MCP 工具挂钩字段 ](<#mcp-工具挂钩字段>) 了解 `mcp_tool` 挂钩何时运行。

匹配器值对应于会话的启动方式：

|匹配器|当它着火时|
| :- | :- |
| `startup` |新会话|
| `resume` |`--resume`、`--continue` 或 `/resume`|
| `clear` | `/clear` |
| `compact` |自动或手动压实|
| `fork` |从现有会话分叉的新会话：`--fork-session` 与 `--resume` 或 `--continue`、`/fork` 后台副本、`/branch` 或您 [ 移动到后台的对话 ](<https://code.claude.com/docs/en/agent-view#from-inside-a-session>)|

在 v2.1.214 之前，分叉会话报告源 `"resume"`。

当您启动交互式会话时，在启动时使用 `--continue` 或 `--resume` 恢复对话，或者运行 `/clear`，SessionStart 挂钩在后台运行。您可以立即打字，然后您恢复的对话就会出现，而无需等待挂钩。 Claude 的第一个响应仍然等待钩子完成，因此它们的上下文到达 Claude。

当您在会话内切换与 `/resume` 的对话时，切换会等待挂钩完成。如果您在后台挂钩仍在运行时运行 `/clear` 或切换到另一个对话，则它们返回的任何内容都不会应用于该会话。

同样的等待适用于启动时，包括恢复会话：在 SessionStart 挂钩仍在运行时发送的提示在完成之前不会到达 Claude。

在任一等待期间，按 `Esc` 将提示带回到输入中而不发送它。钩子继续运行。

#### SessionStart输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，SessionStart 挂钩还接收 `source` 以及可选的 `model`、`agent_type` 和 `session_title`：

|领域|描述|
| :- | :- |
| `source` |会话如何开始：`"startup"` 表示新会话，`"resume"` 表示恢复会话，`/clear` 之后的 `"clear"`，压缩后的 `"compact"` 或 `"fork"` 表示从现有会话分叉的新会话|
| `model` |活动模型标识符。它可以被省略，例如在 `/clear` 之后或通过对话恢复恢复会话时，因此在读取之前检查该字段|
| `agent_type` |agent 名称，当您使用 `claude --agent <name>` 启动 Claude Code 时出现|
| `session_title` |会话的自定义标题，在设置时出现，例如使用 `--name`、`/rename`、挂钩的 `sessionTitle` 输出或 Agent SDK 的 `renameSession()`。发出 `sessionTitle` 的钩子可以首先检查此字段以避免覆盖现有的自定义标题|

您尚未命名的会话仍可以具有 [ 生成的标题 ](<https://code.claude.com/docs/en/sessions#name-your-sessions>)。该标题不是自定义标题，并且不会出现在 `session_title` 中。

当 `source` 为 `"resume"` 或 `"fork"` 且转录本包含至少一个来自 Claude 的响应时，SessionStart 挂钩还会接收以下四个字段。您的钩子可以使用它们来报告在第一个请求之前恢复陈旧对话的成本，例如在 [`systemMessage`](<#json-输出>) 中。这些字段需要 Claude Code v2.1.251 或更高版本。

|领域|描述|
| :- | :- |
| `seconds_since_last_response` |自恢复记录中上次响应以来的挂钟秒数|
| `context_tokens` |恢复会话的第一个请求作为提示重新发送|
| `prompt_cache_likely_expired` |`true` 当最后一个响应早于会话的 [ 提示缓存生命周期 ](<https://code.claude.com/docs/en/prompt-caching#cache-lifetime>) 或稍后的压缩替换了缓存的会话|
| `estimated_cache_write_usd` |将 `context_tokens` 写入会话模型上的提示缓存的预计成本（以 US 美元计），不包括响应|

此示例显示在上次响应后 90 分钟恢复的会话的输入：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SessionStart",
  "source": "resume",
  "model": "claude-opus-5",
  "seconds_since_last_response": 5400,
  "context_tokens": 182340,
  "prompt_cache_likely_expired": true,
  "estimated_cache_write_usd": 1.1396
}
```

#### SessionStart决策控制

SessionStart 挂钩可以为 Claude 添加上下文、提供第一条用户消息、设置会话标题、监视文件和重新加载技能。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，返回每个字段的字段：

|领域|描述|
| :- | :- |
| `additionalContext` |在对话开始时、第一个提示之前添加到 Claude 上下文的字符串。请参阅 [为 Claude](<#添加-claude-的上下文>) 添加上下文，了解如何传递文本以及在其中放置什么内容|
| `initialUserMessage` |用作会话的第一条用户消息的字符串，处于[非交互模式](<https://code.claude.com/docs/en/headless>)，带有`-p`标志。即使你没有通过提示，它也会成为第一个回合。您通过的提示将在下一个回合出现|
| `sessionTitle` |设置会话标题，与`/rename`效果相同。当`source`为`"startup"`、`"resume"`或`"fork"`时适用|
| `watchPaths` |在此会话期间监视 [FileChanged](<#filechanged>) 事件的绝对路径数组|
| `reloadSkills` |布尔值。当`true`时，Claude Code在SessionStart钩子完成后重新扫描[技能](<https://code.claude.com/docs/en/skills>)和命令目录。参见[钩子安装的重载技能](<#重新加载挂钩安装的技能>)|

此输出添加上下文并命名会话：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Current branch: feat/auth-refactor\nUncommitted changes: src/auth.ts, src/login.tsx\nActive issue: #4211 Migrate to OAuth2",
    "sessionTitle": "auth-refactor"
  }
}
```

仅添加上下文的钩子可以在不构建 JSON 的情况下打印它，因为 Claude Code 将 SessionStart 钩子的 [ 纯文本标准输出 ](<#退出代码-0>) 添加到 Claude 的上下文。

如果您的插件的 SessionStart 挂钩提供 `initialUserMessage` 或 `sessionTitle`，请在会话开始之前安装该插件。 Claude Code 会忽略在 SessionStart 挂钩运行后完成安装的插件中​​的两个字段。

#### 重新加载挂钩安装的技能

要使 SessionStart 挂钩安装的技能在同一会话中可用，请返回 `reloadSkills`。技能发现通常在 SessionStart 挂钩完成之前运行，因此如果没有它，当第一个提示运行时，写入 `~/.claude/skills/` 或 `.claude/skills/` 的挂钩文件可能会丢失。

此示例同步共享技能存储库并请求重新扫描：

```bash theme={null}
#!/bin/bash

git -C ~/.claude/skills/team-skills pull --quiet 2>/dev/null || \
  git clone --quiet https://git.example.com/your-org/team-skills.git ~/.claude/skills/team-skills

echo '{"hookSpecificOutput": {"hookEventName": "SessionStart", "reloadSkills": true}}'
```

存储库 URL 是一个占位符。将其替换为您自己的技能存储库。

#### 持久化环境变量

SessionStart 挂钩可以访问 `CLAUDE_ENV_FILE` 环境变量，它提供了一个文件路径，您可以在其中为后续 Bash 命令保留环境变量。

要设置各个环境变量，请将 `export` 语句写入 `CLAUDE_ENV_FILE`。使用append(`>>`)来保存其他钩子设置的变量：

```bash theme={null}
#!/bin/bash

if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo 'export NODE_ENV=production' >> "$CLAUDE_ENV_FILE"
  echo 'export DEBUG_LOG=true' >> "$CLAUDE_ENV_FILE"
  echo 'export PATH="$PATH:./node_modules/.bin"' >> "$CLAUDE_ENV_FILE"
fi

exit 0
```

要捕获设置命令中的所有环境更改，请比较之前和之后导出的变量：

```bash theme={null}
#!/bin/bash

ENV_BEFORE=$(export -p | sort)

# Run your setup commands that modify the environment
source ~/.nvm/nvm.sh
nvm use 20

if [ -n "$CLAUDE_ENV_FILE" ]; then
  ENV_AFTER=$(export -p | sort)
  comm -13 <(echo "$ENV_BEFORE") <(echo "$ENV_AFTER") >> "$CLAUDE_ENV_FILE"
fi

exit 0
```



**注释：**

`CLAUDE_ENV_FILE` 可用于 SessionStart、[Setup](<#设置>)、[CwdChanged](<#cwdchanged>) 和 [FileChanged](<#filechanged>) 挂钩。其他钩子类型无法访问此变量。


### 设置

仅在启动时触发 Claude Code 与 `--init-only`，或与 `--init` 或 `--maintenance` 在 [非交互模式](<https://code.claude.com/docs/en/headless>) 与 `-p` 标志。正常启动时它不会触发。将其用于您显式触发的一次性依赖项安装或计划清理 CI 或脚本，与正常会话启动分开。对于每个会话初始化，请使用 [SessionStart](<#sessionstart>) 相反。

匹配器值对应于触发挂钩的 CLI 标志：

|匹配器|当它着火时|
| :- | :- |
| `init` |`claude --init-only` 或 `claude -p --init`|
| `maintenance` | `claude -p --maintenance` |

当您运行 `claude --init-only` 时，Claude Code 将使用 `startup` 匹配器运行安装程序挂钩和 `SessionStart` 挂钩，然后退出而不启动对话。

当您开始或继续与 `-p` 对话时，您还需要提供提示，作为参数或在标准输入上通过管道传输。当 `SessionStart` 挂钩提供 [`initialUserMessage`](<#sessionstart决策控制>) 或使用 [ 延迟工具调用 ](<#推迟工具调用以供稍后使用>) 恢复会话时，您可以跳过提示。

成功后，`--init-only` 不会在终端上打印任何内容。要确认挂钩已运行，请从 `claude --debug-file <path> --init-only` 开始，将 `<path>` 替换为日志文件位置，然后检查日志中的安装程序和 SessionStart 挂钩条目。

由于安装程序不会在每次启动时启动，因此需要安装依赖项的插件不能单独依赖安装程序。实际模式是检查首次使用的依赖性并在未命中时安装，例如测试 `${CLAUDE_PLUGIN_DATA}/node_modules` 并在不存在时运行 `npm install` 的挂钩或技能。请参阅 [ 持久数据目录 ](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>) 以了解存储已安装依赖项的位置。如果您通过市场分发插件，则可能不需要此模式：Claude Code [在缓存插件时自动安装符合条件的Node.js包依赖项](<https://code.claude.com/docs/en/plugins/loading#node-js-package-dependencies>)。

#### 设置输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，安装挂钩还接收设置为 `"init"` 或 `"maintenance"` 的 `trigger` 字段：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Setup",
  "trigger": "init"
}
```

#### 设置决策控制

安装钩子不能阻塞；任何退出代码都会继续执行。对于每个退出代码，Claude Code 都会丢弃安装挂钩的 [JSON 输出字段 ](<#json-输出>)，例如 `systemMessage`、`continue` 和 `hookSpecificOutput.additionalContext`。使用 `-p`，当您使用 `--output-format stream-json --verbose` 启动时，安装程​​序挂钩的 stdout、stderr 和退出代码仅作为 [`hook_response` 事件 ](<https://code.claude.com/docs/en/headless#read-session-metadata>) 出现在运行的输出中。

安装挂钩可以访问 `CLAUDE_ENV_FILE`。写入该文件的变量会保留到会话的后续 Bash 命令中，如 [SessionStart 挂钩 ](<#持久化环境变量>) 中所示。仅 `type: "command"` 挂钩在 `Setup` 上运行。 `Setup` 上的 `type: "mcp_tool"` 挂钩始终会被跳过，如 [MCP 工具挂钩字段 ](<#mcp-工具挂钩字段>) 中所述。

### InstructionsLoaded

当 `CLAUDE.md` 或 `.claude/rules/*.md` 文件加载到上下文中时触发。此事件在会话开始时针对急切加载的文件触发，并在稍后延迟加载文件时触发，例如当 Claude 访问包含嵌套 `CLAUDE.md` 的子目录时或当条件规则与 `paths:` frontmatter 匹配时触发。该钩子不支持阻塞或决策控制。出于可观察性的目的，它异步运行。

当以下情况时此事件不会触发 Claude [读 `AGENTS.md` 直接](<https://code.claude.com/docs/en/memory#agents-md>) 通过 **项目说明** 设置。当 `CLAUDE.md` 导入你的 `AGENTS.md`, 与 `load_reason` 设置为 `include` 对于任何其他导入的文件，以及何时 `CLAUDE.md` 是一个符号链接，就像平常一样 `CLAUDE.md` 负载。

匹配器针对 `load_reason` 运行。例如，使用 `"matcher": "session_start"` 仅针对会话启动时加载的文件触发，或使用 `"matcher": "path_glob_match|nested_traversal"` 仅针对延迟加载触发。

#### InstructionsLoaded输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，InstructionsLoaded 挂钩还接收以下字段：

|领域|描述|
| :- | :- |
| `file_path` |加载的指令文件的绝对路径|
| `memory_type` |文件范围：`"User"`、`"Project"`、`"Local"` 或 `"Managed"`|
| `load_reason` |加载文件的原因：`"session_start"`、`"nested_traversal"`、`"path_glob_match"`、`"include"` 或 `"compact"`。当压缩事件后重新加载指令文件时，`"compact"` 值会触发|
| `globs` |文件的 `paths:` frontmatter 中的路径 glob 模式（如果有）。仅适用于 `path_glob_match` 负载|
| `trigger_file_path` |其访问触发此加载的文件的路径，用于延迟加载|
| `parent_file_path` |包含此文件的父指令文件的路径，用于 `include` 加载|

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "InstructionsLoaded",
  "file_path": "/Users/my-project/CLAUDE.md",
  "memory_type": "Project",
  "load_reason": "session_start"
}
```

#### InstructionsLoaded决策控制

InstructionsLoaded 钩子没有决策控制。他们无法阻止或修改指令加载。 Claude Code 丢弃它们的 [JSON 输出字段 ](<#json-输出>)，例如 `systemMessage` 和 `continue`。使用此事件进行审核日志记录、合规性跟踪或可观察性。

### UserPromptSubmit

在提交提示时运行，在 Claude 处理它之前。这允许您
根据prompt/conversation添加其他上下文，验证提示，或
阻止某些类型的提示。

`UserPromptSubmit` 挂钩不会仅在您键入提示时触发。 Claude Code 也在以下平台上运行它们：

* [ 计划任务 ](<https://code.claude.com/docs/en/scheduled-tasks>) 触发，包括 `/loop` 迭代
* [后台子代理](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>)向启动它的会话报告
* 另一个会话向您的主要对话发送 [ 消息 ](<https://code.claude.com/docs/en/cross-session-messaging>)

对于 `command`、`http` 和 `mcp_tool` 类型，`UserPromptSubmit` 挂钩的默认超时为 30 秒，比大多数其他事件中这些类型的 600 秒默认超时短。由于此挂钩在每个提示之前运行并阻止模型处理直到完成，因此卡住的挂钩会导致会话停止。如果您的挂钩需要更多时间，请在挂钩条目中设置 `timeout` 字段。

除了使用 [`async: true`](<#在后台运行钩子>) 运行的命令挂钩之外，到达超时的 `UserPromptSubmit` 命令、HTTP 或 MCP 工具挂钩将被取消，并且其输出（包括任何 `additionalContext`）将被丢弃。如果没有该上下文，提示符仍然会到达 Claude。要阻止提示，请在命令或 HTTP 挂钩上设置 [`onFailure: "block"`](<#当钩子失败时阻止操作>)。文字记录显示了一条通知，命名了钩子、触发的超时以及输出被丢弃。

`UserPromptSubmit` 上的 [Agent SDK 回调挂钩 ](<https://code.claude.com/docs/en/agent-sdk/hooks>) 达到超时后会阻止提示，并显示一条指定挂钩和超时的消息，因为那里的回调可以充当不得失败打开的策略门。会议继续进行。在v2.1.208之前，该事件的回调超时以执行错误结束了回合。

#### UserPromptSubmit输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，UserPromptSubmit 挂钩还接收包含提交文本的 `prompt` 字段。折叠到 `[Pasted text #N]` 占位符的粘贴内容到达后展开到位。在 Claude Code [ 标记 Claude](<https://code.claude.com/docs/en/terminal-config#how-claude-treats-pasted-text>) 粘贴文本的会话中，扩展内容位于 `<pasted_content id="…">` 行和 `</pasted_content id="…">` 行之间，因此，如果您的钩子解析提示，请考虑这些行。

当会话具有自定义标题时，UserPromptSubmit 挂钩也会收到 `session_title`，其含义与 [SessionStart `session_title` 字段 ](<#sessionstart输入>) 相同。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "UserPromptSubmit",
  "prompt": "Write a function to calculate the factorial of a number"
}
```

#### UserPromptSubmit决策控制

`UserPromptSubmit` 钩子可以控制是否处理提交的提示并添加上下文。所有 [JSON 输出字段 ](<#json-输出>) 均可用。

有两种方法可以在退出代码 0 上向对话添加上下文：

* **纯文本标准输出**：Claude Code将标准输出添加为[将其视为纯文本](<#退出代码-0>)到Claude的上下文
* **JSON 和 `additionalContext`**：使用下面的 JSON 格式进行更多控制。 `additionalContext` 字段添加为上下文

两个通道都不会产生可见的转录条目。普通标准输出和 `additionalContext` 值均作为系统提醒注入，以挂钩名称开头； Claude 读取两者。要确认交付，请检查[调试日志](<#调试钩子>)。

要阻止提示，请返回一个 JSON 对象，并将 `decision` 设置为 `"block"`：

|领域|描述|
| :- | :- |
| `decision` |`"block"` 在到达 Claude 之前停止提示。省略以允许提示继续|
| `reason` |当 `decision` 为 `"block"` 时向用户显示。未添加到上下文中|
| `additionalContext` |与提交的提示一起添加到 Claude 上下文的字符串。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|
| `sessionTitle` |设置会话标题。用于根据提示内容自动命名会话|
| `suppressOriginalPrompt` |如果 `true` 当钩子阻止提示时，则将提示文本保留在阻止消息之外。请参阅 [被阻止的提示会留下什么](<#被阻止的提示会留下什么>)|

通过退出 2 进行阻止的钩子的路由方式与 `reason` 相同：阻止消息向用户显示 stderr 文本，并且不会将其添加到上下文中。

```json theme={null}
{
  "decision": "block",
  "reason": "Explanation for decision",
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "My additional context here",
    "sessionTitle": "My session title",
    "suppressOriginalPrompt": true
  }
}
```

#### 被阻止的提示会留下什么

被阻止的提示永远不会到达 Claude，但其文本不会在所有地方被删除。默认情况下，向用户显示的阻止消息以 `Original prompt:` 结尾，后跟提交的文本，并且 Claude Code 将该消息写入磁盘上的会话转录文件。要将文本保留在消息中，请在 `hookSpecificOutput` 内打印 JSON 和 `"suppressOriginalPrompt": true`。无论钩子是通过`decision: "block"`阻塞还是通过退出2，这都有效。

`suppressOriginalPrompt` 仅更改阻止消息。提交的文本仍然可以出现在本地文件中，例如会话记录和提示历史记录，因此阻止挂钩并不是在磁盘上保密的方法。要限制或删除这些文件，请参阅[明文存储](<https://code.claude.com/docs/en/claude-directory#plaintext-storage>)和[清除本地数据](<https://code.claude.com/docs/en/claude-directory#clear-local-data>)。

### UserPromptExpansion

当用户键入的命令在到达 Claude 之前扩展为提示时运行。使用它来阻止直接调用特定命令、注入特定技能的上下文或记录用户调用的命令。例如，匹配 `deploy` 的挂钩可以阻止 `/deploy`，除非存在批准文件，或者匹配审核技能的挂钩可以将团队的审核清单附加为 `additionalContext`。

此事件涵盖了 `PreToolUse` 没有覆盖的路径：仅当 Claude 调用该工具时，与 `Skill` 工具匹配的 `PreToolUse` 挂钩才会触发，但直接输入 `/skillname` 会绕过 `PreToolUse`。 `UserPromptExpansion` 在该直接路径上触发。

匹配 `command_name`。将匹配器留空以在每个提示类型命令上触发。

#### UserPromptExpansion输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，UserPromptExpansion 挂钩还接收 `expansion_type`、`command_name`、`command_args`、`command_source` 和原始 `prompt` 字符串。对于技能和自定义命令，`expansion_type` 字段为 `slash_command`，对于 MCP 服务器提示，字段为 `mcp_prompt`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../00893aaf.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "UserPromptExpansion",
  "expansion_type": "slash_command",
  "command_name": "example-skill",
  "command_args": "arg1 arg2",
  "command_source": "plugin",
  "prompt": "/example-skill arg1 arg2"
}
```

#### UserPromptExpansion决策控制

`UserPromptExpansion` 钩子可以阻止扩展或添加上下文。所有 [JSON 输出字段 ](<#json-输出>) 均可用。

|领域|描述|
| :- | :- |
| `decision` |`"block"` 阻止命令扩展。省略以允许其继续|
| `reason` |当 `decision` 为 `"block"` 时向用户显示|
| `additionalContext` |字符串与扩展提示一起添加到 Claude 的上下文中。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|

通过退出 2 进行阻止的钩子的路由方式与 `reason` 相同：阻止消息向用户显示 stderr 文本。

```json theme={null}
{
  "decision": "block",
  "reason": "This slash command is not available",
  "hookSpecificOutput": {
    "hookEventName": "UserPromptExpansion",
    "additionalContext": "Additional context for this expansion"
  }
}
```

### MessageDisplay

当助理消息流到屏幕上时运行。 Claude Code 以增量方式显示消息：每当一批新完成的行准备好渲染时，钩子就会对这些行运行一次，并且 Claude Code 在其位置渲染钩子的替换文本。一条长消息会产生多次调用；一条短消息只能产生一个。

使用 MessageDisplay 可以：

* 条带降价以实现最小显示
* 转换 Agent SDK 应用程序向用户显示的文本
* 从 Claude 的响应中编辑 API 密钥或内部主机名

Claude Code 保留每批，直到您的钩子返回，因此请保持钩子快速。如果挂钩失败或超时，Claude Code将显示原始文本。该事件的默认超时时间为10秒；如果您的挂钩需要更多时间，请在挂钩条目中设置 `timeout` 字段。

MessageDisplay 仅用于显示：替换文本仅更改屏幕上呈现的内容。文字记录和 Claude 看到的内容保留原始文本，因此 Claude 永远不会看到替换内容，并且详细模式显示原始文本。该挂钩仅接收辅助消息文本，因此工具结果和您键入的文本呈现不变。

MessageDisplay 不支持匹配器，并且会触发每条流文本的助理消息；没有文本的消息（例如仅工具调用的响应）不会触发它。

在非交互式运行中，包括 Agent SDK 查询和 `claude -p`，MessageDisplay 每个助理消息运行一次，而不是每批行运行一次。消息完成后，单个调用到达并携带完整的消息文本：`index` 是 `0`，`final` 是 `true`，`delta` 保存整个消息。收集每条消息的 `delta` 文本的挂钩在两种模式下都会收到相同的总文本。

#### MessageDisplay输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，MessageDisplay 挂钩还接收回合和消息的标识符、消息中此调用的位置以及 `delta` 中的新文本。批次边界取决于文本流的方式，因此使用 `index` 和 `final` 来跟踪消息的进度，而不是期望行以特定方式分组。

|领域|描述|
| :- | :- |
| `turn_id` |UUID 当前回合|
| `message_id` |正在显示的辅助消息的UUID。在同一消息的每批中都保持稳定。这不是 API `msg_…` id，因此它无法与转录消息 id 关联|
| `index` |消息中该批次的从零开始的索引|
| `final` |`true` 消息的最后一批。每条消息都有一个最终批次|
| `delta` |自上一批以来新完成的行，包括终止换行符。总是整条线，除了最后一批可能在中线结束。在交互式运行中，当消息以换行符结束时，最终批次的增量为空，因此将 `final`（而不是非空增量）视为消息结束信号。在Agent、SDK和`claude -p`运行中，单个调用携带整个消息|

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "MessageDisplay",
  "turn_id": "0c9e6a2f-7d41-4f4e-9a15-3f4f7c2b8d10",
  "message_id": "5b2a9c8e-1f63-4d8a-b7c4-9e0d2a6f1c3b",
  "index": 0,
  "final": false,
  "delta": "Here is the plan:\n"
}
```

#### MessageDisplay 输出

除了可用于所有钩子的 [JSON 输出字段 ](<#json-输出>) 之外，MessageDisplay 钩子还可以返回 `displayContent` 来替换屏幕上的增量：

|领域|描述|
| :- | :- |
| `displayContent` |显示文本来代替增量。省略它以显示原始内容|

MessageDisplay 钩子没有决策控制。他们无法阻止消息或更改记录中存储的内容或发送到 Claude 的内容。 Claude Code 从其 JSON 输出作用于 `displayContent`，并丢弃 `systemMessage` 和 `continue`。

此示例从 Claude 的响应中删除 Markdown 格式以进行纯文本显示。该脚本从标准输入读取每个批次，从 `delta` 中删除粗体标记和内联代码反引号，并将结果返回为 `displayContent`。



  

#### macOS/Linux

在设置文件中为事件注册命令挂钩：

    ```json theme={null}
    {
      "hooks": {
        "MessageDisplay": [
          {
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/plain-display.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

将此脚本保存到项目中的 `.claude/hooks/plain-display.sh` 并使用 `chmod +x` 使其可执行：

    ```bash theme={null}
    #!/bin/bash
    jq '{hookSpecificOutput: {hookEventName: "MessageDisplay", displayContent: (.delta | gsub("\\*\\*"; "") | gsub("`"; ""))}}'
    ```
  


  

#### Windows（PowerShell）

通过PowerShell注册运行脚本的命令钩子：

    ```json theme={null}
    {
      "hooks": {
        "MessageDisplay": [
          {
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/plain-display.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

`-NoProfile` 标志会跳过加载 PowerShell 配置文件，因此挂钩快速启动，并且 `-ExecutionPolicy Bypass` 让 PowerShell 运行本地脚本文件。

将此脚本保存到项目中的 `.claude/hooks/plain-display.ps1`：

    ```powershell theme={null}
    $batch = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $text = $batch.delta -replace '\*\*', '' -replace '`', ''
    @{
      hookSpecificOutput = @{
        hookEventName = "MessageDisplay"
        displayContent = $text
      }
    } | ConvertTo-Json
    ```
  


没有降价的批次不变地通过。如果脚本失败，例如由于缺少 `jq`，Claude Code 将显示原始文本并仅在 [ 调试输出 ](<#调试钩子>) 中而不是在会话中注明失败。

### PreToolUse

在 Claude 创建工具参数之后、处理工具调用之前运行。匹配除 `EndConversation` 之外的任何工具名称：内置工具，例如 `Bash`、`PowerShell`、`Edit`、`Write`、`Read`、`Glob`、`Grep`、 `Agent`、`Workflow`、`WebFetch`、`WebSearch`、`AskUserQuestion` 和 `ExitPlanMode` 以及任何 [MCP 工具名称 ](<#匹配mcp工具>)。

要在磁盘上的特定文件更改时运行挂钩（无论其内容是什么），请使用 [FileChanged](<#filechanged>) 而不是按名称匹配文件编辑工具。与PreToolUse不同，Claude Code在更改后运行FileChanged钩子，并且它们没有决策控制，因此它们无法阻止写入。



**注意：**

  PreToolUse 仅在以下情况下运行 Claude 调用一个工具。给你归档 [参考与 `@` 在你的提示中](<https://code.claude.com/docs/en/common-workflows#reference-files-and-directories>) 无需任何工具调用即可添加： Claude Code 在构建提示时插入它们的内容，所以没有 PreToolUse 为他们触发钩子，包括钩子匹配 `Read`。阻止特定路径 `@` 参考文献，使用 [`Read` 否认规则](<https://code.claude.com/docs/en/permissions#read-and-edit>) 相反。

PreToolUse 也不会针对 [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) 触发。


使用 [PreToolUse 决策控制 ](<#pretooluse决策控制>) 允许、拒绝、询问或推迟工具调用。

`PreToolUse` 上的 [Agent SDK 回调挂钩 ](<https://code.claude.com/docs/en/agent-sdk/hooks>) 超出其超时会阻止工具调用，并且 Claude 收到命名超时的错误结果。另一个钩子返回的显式拒绝仍然优先。

#### PreToolUse输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，PreToolUse 挂钩还接收 `tool_name`、`tool_input` 和 `tool_use_id`。

对于 [MCP 工具 ](<#匹配mcp工具>)，输入还携带 `mcp_server`，一个带有服务器的 `name` 的对象和一个表示服务器定义来自何处的 `source`。 `source`值包括`plugin`、`sdk`以及配置范围，例如`user`和`project`。 Agent SDK 参考文献中的 [`McpServerProvenance`](<https://code.claude.com/docs/en/agent-sdk/typescript#mcpserverprovenance>) 列出了所有这些内容，并说明了如何对待您不认识的人。基于 `source` 而不是 `name` 或 `mcp__<server>__` 工具名称前缀来做出信任决策。 `mcp_server` 字段需要 Claude Code v2.1.274 或更高版本。

对于文件工具 `Write`、`Edit` 和 `Read`，`tool_input.file_path` 始终是绝对值：

* Claude Code 在钩子运行之前扩展 `~` 和相对路径，因此在路径上匹配的钩子不能通过 `~` 或同一路径的相对拼写绕过
* 在 Windows 上，路径以反斜杠分隔符到达，即使您的钩子在 Git Bash 下运行，其中 `$PWD` 看起来像 `/c/project`
* 用正斜杠编写的比较（例如 `/src/` 检查）永远不会匹配反斜杠路径，并且工具调用将继续进行，就好像钩子没有任何东西可以阻止一样
* 在比较之前标准化分隔符：Bash 中的 `FILE_PATH="${FILE_PATH//\\//}"`，或 Python 中的 `file_path.replace("\\", "/")`，然后匹配路径段，例如 `/src/`，而不是使用 `^` 锚定，因为路径是绝对的

Windows 上的 `Write` 调用会提供：

```json theme={null}
{
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "C:\\project\\src\\index.ts",
    "content": "..."
  },
  ...
}
```

`tool_input` 字段取决于工具：

<a id="bash" />

##### 重击

执行外壳命令。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `command` |字符串| `"npm test"` |要执行的 shell 命令|
| `description` |字符串| `"Run test suite"` |命令功能的可选描述|
| `timeout` |数量| `120000` |可选超时（以毫秒为单位）。高于 [ 最大值 ](<https://code.claude.com/docs/en/tools-reference#bash-tool-behavior>) 的值将减少到最大值而不是拒绝|
| `run_in_background` |布尔值| `false` |是否在后台运行命令|

当 Bash 命令更改 Git 存储库中的文件时，Claude Code 可以记录更改的内容。记录[`bashEditDiffEnabled`](<https://code.claude.com/docs/en/settings-reference#basheditdiffenabled>)设置开启录音时各权限模式的变化；该设置的条目表明哪些文件可以设置它。否则，它仅在自动模式和`bypassPermissions`模式下记录它们，并且仅当Claude Code指示Claude通过Bash编辑文件时记录它们。将 `bashEditDiffEnabled` 设置为 `false` 以关闭录音。后台命令和只读命令没有diff。

然后，您的 [PostToolUse 挂钩 ](<#posttooluse>) 会收到 `tool_response.bashEditDiff` 中更改的文件。该列表涵盖了命令运行时存储库下发生的更改。未列出 Git 忽略的文件和子模块中的文件。需要 Claude Code v2.1.269 或更高版本。



**注释：**

该列表已尽最大努力且处于公开测试阶段。 Claude Code 可能会错过更改、包含另一个进程同时更改的文件，或者在其大小限制处停止。字段形状可能会改变。使用该列表来查找要审查的内容，而不是执行政策。


`changedFiles` 和 `files` 列出命令更改的内容；其余字段说明该列表的完整性和可靠性。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `changedFiles` |数组| `["/path/to/src/app.ts"]` |命令更改的文件的绝对路径，最多 200。每当 `files` 保持差异或 `moreFiles` 大于零时出现|
| `files` |数组| `[{"filePath": "/path/to/src/app.ts", "hunks": [...]}]` |最多 5 个已更改文件的差异，用于显示。对于命令添加或删除的文件，`created` 或 `deleted` 是 `true`|
| `moreFiles` |数量| `2` |`files` 中无差异的已更改文件的计数|
| `unavailable` |布尔值| `true` |当差异不完整或无法获取时设置|
| `skipped` |布尔值| `true` |设置用于移动工作树的 Git 命令，例如 `git checkout` 或 `git stash`，因此 Claude Code 不采用 diff|
| `shared` |布尔值| `true` |当另一个 Bash 工具调用（例如子代理的调用）同时在同一存储库中运行时设置，因此列出的某些更改可能是该命令的|

<a id="powershell" />

##### PowerShell

执行 PowerShell 命令。请参阅 [PowerShell 工具 ](<https://code.claude.com/docs/en/tools-reference#powershell-tool>) 了解各平台的可用性。

这些字段与 Bash 工具匹配，命令字符串位于 `command` 中：

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `command` |字符串| `"Get-ChildItem -Recurse"` |要执行的PowerShell命令|
| `description` |字符串| `"List files recursively"` |命令功能的可选描述|
| `timeout` |数量| `120000` |可选超时（以毫秒为单位）|
| `run_in_background` |布尔值| `false` |是否在后台运行命令|

在检查 shell 命令的钩子中匹配 `Bash|PowerShell`，因此它们涵盖了这两个工具：

* 在 Windows 上，只要启用了 PowerShell 工具，Claude 就会将 PowerShell 视为主 shell，并通过它路由 shell 命令。
* 在没有 Git Bash 的 Windows 上，该工具会自动启用，并且 Claude Code 根本不注册 Bash 工具。
* 仅匹配 `Bash` 的钩子永远不会在那里触发。

##### 写

创建或覆盖文件。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `file_path` |字符串| `"/path/to/file.txt"` |要写入的文件的绝对路径|
| `content` |字符串| `"file content"` |要写入文件的内容|

##### 编辑

替换现有文件中的字符串。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `file_path` |字符串| `"/path/to/file.txt"` |要编辑的文件的绝对路径|
| `old_string` |字符串| `"original text"` |要查找和替换的文本|
| `new_string` |字符串| `"replacement text"` |替换文本|
| `replace_all` |布尔值| `false` |是否替换所有出现的情况|

##### 阅读

读取文件内容。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `file_path` |字符串| `"/path/to/file.txt"` |要读取的文件的绝对路径|
| `offset` |数量| `10` |可选的开始读取的行号|
| `limit` |数量| `50` |可选的读取行数|

##### 全局

查找与 glob 模式匹配的文件。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `pattern` |字符串| `"**/*.ts"` |用于匹配文件的全局模式|
| `path` |字符串| `"/path/to/dir"` |可选的搜索目录。默认为当前工作目录|

##### 格雷普

使用正则表达式搜索文件内容。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `pattern` |字符串| `"TODO.*fix"` |要搜索的正则表达式模式|
| `path` |字符串| `"/path/to/dir"` |要搜索的可选文件或目录|
| `glob` |字符串| `"*.ts"` |用于过滤文件的可选 glob 模式|
| `output_mode` |字符串| `"content"` |`"content"`、`"files_with_matches"` 或 `"count"`。默认为 `"files_with_matches"`|
| `-i` |布尔值| `true` |不区分大小写的搜索|
| `multiline` |布尔值| `false` |启用多行匹配|

##### WebFetch

获取并处理网页内容。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `url` |字符串| `"https://example.com/api"` |URL 从中获取内容|
| `prompt` |字符串| `"Extract the API endpoints"` |提示对获取的内容运行|
| `offset` |数量| `100000` |从页面开头跳过的可选字符数。 Claude设置为继续阅读长页。需要 Claude Code v2.1.290 或更高版本|

##### WebSearch

搜索网络。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `query` |字符串| `"react hooks best practices"` |搜索查询|
| `allowed_domains` |数组| `["docs.example.com"]` |可选：仅包含这些域的结果|
| `blocked_domains` |数组| `["spam.example.com"]` |可选：排除这些域中的结果|

##### Agent

生成一个 [ 子代理 ](<https://code.claude.com/docs/en/sub-agents>)。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `prompt` |字符串| `"Find all API endpoints"` |agent要执行的任务|
| `description` |字符串| `"Find API endpoints"` |任务的简短描述|
| `subagent_type` |字符串| `"Explore"` |使用的专用类型agent|
| `model` |字符串| `"sonnet"` |可选的模型别名来覆盖默认值|

当前台 Agent 调用完成时，您的 [PostToolUse 挂钩 ](<#posttooluse>) 接收子代理的结果并在 `tool_response` 中运行遥测。阅读这些字段来检查运行情况；对于跨子代理的令牌和成本汇总，请使用过滤到 `query_source` `"subagent"` 的 [ 令牌和成本计数器 ](<https://code.claude.com/docs/en/monitoring-usage#token-counter>)，因为 `totalTokens` 和 `usage` 仅涵盖最终请求：

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `status` |字符串| `"completed"` |`"completed"` 表示前台子代理，`"async_launched"` 表示后台子代理。子代理默认在后台运行，因此省略 `run_in_background` 的 Agent 调用也会生成 `"async_launched"`|
| `agentId` |字符串| `"a4d2c8f1e0b3a297"` |子代理运行的标识符|
| `content` |数组| `[{"type": "text", "text": "Found 12 endpoints..."}]` |子代理的最终文本块，或者，对于其报告经过 `SubagentHandback` 的子代理，在其位置上显示有关交回的简短注释|
| `resolvedModel` |字符串| `"claude-sonnet-4-5"` |子代理启动时的模型，可能与请求的模型不同|
| `modelsUsed` |数组| `["claude-sonnet-4-5", "claude-haiku-4-5"]` |按顺序使用的模型，连续重复折叠；仅在运行中交换模型时设置。需要 Claude Code v2.1.212 或更高版本|
| `totalTokens` |数量| `12450` |子代理的最终 API 请求的令牌计数：输入、输出和缓存令牌的组合。这不是整个运行的总和|
| `totalDurationMs` |数量| `48211` |子代理运行的挂钟持续时间|
| `totalToolUseCount` |数量| `7` |子代理进行的工具调用计数|
| `usage` |对象| `{"input_tokens": 8320, ...}` |最终 API 请求的每种类型令牌细分：`input_tokens`、`output_tokens`、`cache_creation_input_tokens`、`cache_read_input_tokens`|

在 Claude Code v2.1.271 或更高版本上，与 [`SubagentHandback`](<https://code.claude.com/docs/en/tools-reference>) 工具一起运行的子代理（Claude Code 在 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 中提供）通过该工具传递其报告，而不是将其作为文本返回。然后，其 `completed` 结果的 `content` 字段包含有关该回交的简短注释，而不是报告本身。要阅读报告，请匹配 `SubagentHandback` 上的 `PreToolUse` 或 `PostToolUse` 钩子并读取 `tool_input.message`。

对于后台子代理，该工具会在任务移至后台时返回，因此 `tool_response` 不包含使用字段：后台启动会立即返回，而 Claude Code 后台运行中的前台任务会在该转换时返回。它有`status: "async_launched"`、`agentId`、`description`、`prompt`、`outputFile`和`resolvedModel`。

在 `completed` 响应中，`resolvedModel` 命名子代理启动时使用的模型，该模型可能与 `tool_input` 中的 `model` 值不同，例如当 `availableModels` 或其他覆盖适用时。在 `async_launched` 响应中，`resolvedModel` 命名 agent 移动到后台时使用的模型，因此在后台之前发生的交换会反映在那里。 `modelsUsed` 和后台时间 `resolvedModel` 行为需要 Claude Code v2.1.212 或更高版本。

<a id="askuserquestion" />

##### AskUserQuestion

向用户询问一到四个多项选择题。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `questions` |数组| `[{"question": "Which framework?", "header": "Framework", "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}], "multiSelect": false}]` |要提出的问题，每个问题都有一个 `question` 字符串、短 `header`、`options` 数组和可选的 `multiSelect` 标志|
| `answers` |对象| `{"Which framework?": "React"}` |可选。将问题文本映射到选定的选项标签。多选答案用逗号连接标签。 Claude没有设置该字段；通过 `updatedInput` 提供它以编程方式回答|

##### ExitPlanMode

提出计划并要求用户在 Claude 离开 [ 计划模式 ](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) 之前批准该计划。 Claude 在调用该工具之前将计划写入磁盘上的文件，因此模型中的文字 `tool_input` 通常为空。 Claude Code 在将输入传递给钩子之前注入计划内容和文件路径。

|领域|类型|示例|描述|
| :- | :- | :- | :- |
| `plan` |字符串| `"## Refactor auth\n1. Extract..."` |在 Markdown 中规划内容。从磁盘上的计划文件注入|
| `planFilePath` |字符串| `"/Users/.../plans/refactor-auth.md"` |计划文件的路径。注入|
| `allowedPrompts` |数组| `[{"tool": "Bash", "prompt": "run tests"}]` |已弃用。 Claude Code 接受该字段但忽略它。在v2.1.205之前，它带有请求执行该计划的基于提示的权限Claude|

在 `PostToolUse` 中，`tool_response` 是一个对象，其中 `plan` 和 `filePath` 字段保存已批准的计划以及内部状态标志。读取 `tool_response.plan` 以获取计划内容，而不是从磁盘重新读取文件。

#### PreToolUse决策控制

`PreToolUse` 挂钩可以控制工具调用是否继续。与使用顶级 `decision` 字段的其他挂钩不同，PreToolUse 在 `hookSpecificOutput` 对象内返回其决策。这使其具有更丰富的控制：四种结果（允许、拒绝、询问或推迟）以及在执行之前修改工具输入的能力。

|领域|描述|
| :- | :- |
| `permissionDecision` |`"allow"` 会跳过权限提示，但 [ 无模式自动批准的操作 ](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>) 以及 `AskUserQuestion` 和 `ExitPlanMode` 需要 [`updatedInput` 与之配对 ](<#allow-with-updatedinput>)。 `"deny"` 阻止工具调用。 `"ask"`提示用户确认。 `"defer"` 正常退出，以便稍后可以恢复该工具。 [拒绝和询问规则](<https://code.claude.com/docs/en/permissions#manage-permissions>)无论钩子返回什么，仍然会被评估|
| `permissionDecisionReason` |对于 `"ask"`，在权限提示中向用户显示。当 Claude Code [ 在 `-p` 运行中拒绝调用 ](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>) 且无人能回答该提示时，Claude 会在工具结果中读取原因。对于`"deny"`，显示为Claude。对于`"allow"`和`"defer"`，仅写入[调试日志](<#调试钩子>)|
| `updatedInput` |在执行前修改工具的输入参数。替换整个输入对象，因此将未更改的字段与已修改的字段一起包含在内。 Claude Code 根据您的钩子返回的输入而不是发送的输入 Claude 评估权限规则和 Bash 命令的 [ 自动后台资格 ](<https://code.claude.com/docs/en/tools-reference#foreground-commands-that-move-to-the-background>)。与 `"allow"` 组合以自动批准，或与 `"ask"` 组合以向用户显示修改后的输入。对于 `"defer"`，忽略|
| `additionalContext` |与工具结果一起添加到 Claude 上下文的字符串。当`permissionDecision`为`"defer"`时被忽略。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|

当多个 PreToolUse 挂钩返回不同决策时，优先级为 `deny` > `defer` > `ask` > `allow`。

通过退出 2 进行阻止的钩子的路由方式与 `"deny"` 相同：Claude 将 stderr 消息视为拒绝原因。

当挂钩返回 `"ask"` 时，向用户显示的权限提示包括一个标识挂钩来源的标签：`[settings]` 表示来自任何设置文件或 agent frontmatter 的挂钩，`[plugin:<name>]` 表示插件的挂钩，或 `[skill]` 表示来自技能 frontmatter 的挂钩。这有助于用户了解哪个配置源正在请求确认。

钩子的`"ask"`还在[自动模式](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>)下强制进行权限提示：分类器仍然可以拒绝工具调用，但不能默默地批准调用。在 v2.1.211 之前，分类器可以批准在 [ 沙箱 ](<https://code.claude.com/docs/en/sandboxing>) 之外运行的 Bash 命令，而不显示钩子请求的提示；分类器仍然对该命令应用自己的安全规则，并且钩子 `"deny"` 始终受到尊重。

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "My reason here",
    "updatedInput": {
      "field_to_modify": "new value"
    },
    "additionalContext": "Current environment: production. Proceed with caution."
  }
}
```



**注释：**

PreToolUse 之前使用了顶级 `decision` 和 `reason` 字段，但此事件已弃用这些字段。请改用 `hookSpecificOutput.permissionDecision` 和 `hookSpecificOutput.permissionDecisionReason`。已弃用的值 `"approve"` 和 `"block"` 分别映射到 `"allow"` 和 `"deny"`。其他事件（例如 PostToolUse 和 Stop）继续使用顶级 `decision` 和 `reason` 作为其当前格式。


<h4 id="allow-with-updatedinput">
 需要用户交互的工具
</h4>

`AskUserQuestion` 和 `ExitPlanMode` 需要用户交互。在具有 `-p` 标志的 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 中，Claude Code 仅当运行具有 [ 权限主机 ](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>) 来接收提示时才提供它们，例如 Agent SDK `canUseTool` 回调。

`PreToolUse` 挂钩在执行以下操作时满足该要求：

1. 从标准输入读取工具的输入
2. 通过自己收集答案UI
3. 返回 `permissionDecision: "allow"` 以及保存答案的 `updatedInput`，因此该工具运行时不会出现提示

对于这些工具来说，单独返回 `"allow"` 是不够的。

对于 `AskUserQuestion`，回显原始 `questions` 数组并添加一个 [`answers`](<#askuserquestion>) 对象，将每个问题的文本映射到所选答案。此输出回答了一个带有 `React` 的问题：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "updatedInput": {
      "questions": [
        {
          "question": "Which framework?",
          "header": "Framework",
          "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}],
          "multiSelect": false
        }
      ],
      "answers": {"Which framework?": "React"}
    }
  }
}
```

服务器标记为 [`_meta["anthropic/requiresUserInteraction"]`](<https://code.claude.com/docs/en/mcp#require-approval-for-a-specific-tool>) 的 MCP 工具更严格：无论有或没有 `updatedInput`，钩子都无法跳过带有 `"allow"` 的批准提示，因为 Claude Code 无法确认钩子收集了工具所需的交互。

#### 推迟工具调用以供稍后使用

`"defer"` 适用于将 `claude -p` 作为子进程运行并读取其 JSON 输出的集成，例如 Agent SDK 应用程序或构建在 Claude Code 之上的自定义 UI。它允许调用进程在工具调用时暂停 Claude，通过其自己的接口收集输入，并从中断处恢复。 Claude Code 仅在具有 `-p` 标志的 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 中采用此值。在交互式会话中，它会记录警告并忽略挂钩结果。

`AskUserQuestion`工具是典型的情况：Claude想要询问用户一些问题，但没有终端可以回答。`-p`运行仅当它具有[权限主机](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>)时才提供`AskUserQuestion`，例如MCP 工具是您通过 `--permission-prompt-tool` 传递的工具，因此从一个开始运行。往返的工作方式如下：

1. Claude 呼叫 `AskUserQuestion`。 `PreToolUse` 钩子触发。
2. 钩子返回`permissionDecision: "defer"`。该工具不执行。该进程退出并显示 `stop_reason: "tool_deferred"`，并且待处理的工具调用保留在记录中。
3. 调用进程从 SDK 结果中读取 `deferred_tool_use`，在自己的 UI 中显示问题，并等待答案。
4. 调用进程以相同的权限主机运行`claude -p --resume <session-id>`。相同的工具调用再次触发 `PreToolUse`。
5. 钩子返回 `permissionDecision: "allow"`，答案在 `updatedInput` 中。该工具执行并且 Claude 继续。

`deferred_tool_use` 字段包含工具的 `id`、`name` 和 `input`。 `input` 是为工具调用生成的参数 Claude，在执行之前捕获：

```json theme={null}
{
  "type": "result",
  "subtype": "success",
  "stop_reason": "tool_deferred",
  "session_id": "abc123",
  "deferred_tool_use": {
    "id": "toolu_01abc",
    "name": "AskUserQuestion",
    "input": { "questions": [{ "question": "Which framework?", "header": "Framework", "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}], "multiSelect": false }] }
  }
}
```

没有超时或重试限制。会话将保留在磁盘上，直到您恢复为止，但须遵守 [`cleanupPeriodDays`](<https://code.claude.com/docs/en/settings-reference#cleanupperioddays>) 保留扫描，默认情况下，会按照 [ 保留扫描规则 ](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) 在 30 天后删除会话文件。如果恢复时答案尚未准备好，则挂钩可以再次返回 `"defer"`，并且进程以相同的方式退出。调用进程通过最终从挂钩返回 `"allow"` 或 `"deny"` 来控制何时中断循环。

`"defer"`仅在Claude轮流调用单个工具时才起作用。如果 Claude 同时调用多个工具，`"defer"` 将被忽略并发出警告，并且工具将继续执行正常的权限流程。存在该约束是因为恢复只能重新运行一个工具：无法推迟一批调用中的一个调用而不留下其他未解决的调用。

如果恢复时延迟工具不再可用，则在触发钩子之前，进程会退出并显示 `stop_reason: "tool_deferred_unavailable"` 和 `is_error: true`。当提供该工具的 MCP 服务器未连接以恢复会话时，就会发生这种情况。 `deferred_tool_use` 有效负载仍然包含在内，因此您可以识别哪个工具丢失了。



**注释：**

要在计划模式下恢复推迟的会话，请将 [`--permission-prompt-tool`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 与 `--resume` 一起传递，以便 Claude Code 可以提交计划以供批准。如果您传递某些其他启动标志，恢复的运行不会返回到计划模式；请参阅 [ 使用 `-p`](<https://code.claude.com/docs/en/sessions#resume-in-plan-mode-with-p>) 在计划模式下恢复。需要 Claude Code v2.1.246 或更高版本。

  当您继续使用 `-p`, Claude Code 不恢复任何其他存储的权限模式。它以新的权限模式启动运行 `claude -p` 运行将开始，所以通过 `--permission-mode` 或 `--dangerously-skip-permissions` 如果延迟会话使用了一个，则再次进行。当您继续使用 `claude --resume <session-id>` 没有 `-p`, Claude Code 恢复存储的权限模式，但列出的例外情况 [恢复时的权限模式](<https://code.claude.com/docs/en/sessions#permission-mode-on-resume>).


### PermissionRequest

当 Claude Code 即将询问您使用工具的权限时运行。在无法显示提示的会话中，例如 [非交互模式 ](<https://code.claude.com/docs/en/headless>) 中的后台子代理，Claude Code 仍然运行这些挂钩，如果没有挂钩返回决定，则会拒绝工具调用。对于到达 `--permission-prompt-tool` 或 Agent SDK 的 [`canUseTool` 回调 ](<https://code.claude.com/docs/en/agent-sdk/permissions>) 的调用，挂钩与您的主机一起运行，以先决定者为准。
使用 [PermissionRequest 决定control](<#permissionrequest决策控制>) 代表用户允许或拒绝。

当您在 Claude 请求使用工具的许可时需要信号时，请使用此事件。仅在提示等待大约六秒后，Claude Code 才会运行类型为 `permission_prompt` 的 [Notification](<#通知>) 挂钩。

Claude Code 不会针对沙盒命令的 [ 网络请求 ](<https://code.claude.com/docs/en/sandboxing#network-isolation>) 运行 PermissionRequest 挂钩。要获取该提示的信号，请使用 `permission_prompt` 通知类型。

匹配工具名称，值与 PreToolUse 相同。

#### PermissionRequest输入

PermissionRequest 挂钩像 PreToolUse 挂钩一样接收 `tool_name` 和 `tool_input` 字段，但没有 `tool_use_id`。对于 MCP 工具，它们还会收到 [`mcp_server`](<#pretooluse输入>) 对象。可选的 `permission_suggestions` 数组包含对此请求建议的 [ 权限更新 ](<#权限更新条目>) Claude Code，例如添加允许规则或更改权限模式。

`permission_suggestions` 数组不是您看到的选项的精确列表，因为每个权限对话框都会构建自己的选项。某些对话框（例如用于文件编辑的对话框）根本不读取数组，而是从请求本身获取选项。读取它的对话框仍然可以保留其建议保留在数组中的选项，例如当 [`allowManagedPermissionRulesOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedpermissionrulesonly>) 隐藏规则保存选项时。它还可以提供没有建议条目的选项，例如[**是，并切换到自动模式**](<https://code.claude.com/docs/en/permission-modes#switch-permission-modes>)，这会直接更改权限模式，而不是通过权限更新。

PreToolUse 挂钩在每次工具调用之前运行，无论是否需要权限。 PermissionRequest 挂钩仅在 Claude Code 即将请求您许可时运行，或者当它自动拒绝无法提示的呼叫时运行。 [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) 两个事件都不会触发。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PermissionRequest",
  "tool_name": "Bash",
  "tool_input": {
    "command": "rm -rf node_modules",
    "description": "Remove node_modules directory"
  },
  "permission_suggestions": [
    {
      "type": "addRules",
      "rules": [{ "toolName": "Bash", "ruleContent": "rm -rf node_modules" }],
      "behavior": "allow",
      "destination": "localSettings"
    }
  ]
}
```

#### PermissionRequest决策控制

`PermissionRequest` 挂钩可以允许或拒绝权限请求。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，您的挂钩脚本还可以返回具有以下特定于事件的字段的 `decision` 对象：

|领域|描述|
| :- | :- |
| `behavior` |`"allow"` 授予权限，`"deny"` 拒绝。 [拒绝和询问规则](<https://code.claude.com/docs/en/permissions#manage-permissions>)仍在评估，因此返回`"allow"`的挂钩不会覆盖匹配的拒绝规则|
| `updatedInput` |仅适用于`"allow"`：在执行前修改工具的输入参数。替换整个输入对象，因此将未更改的字段与已修改的字段一起包含在内。根据拒绝和询问规则重新评估修改后的输入|
| `updatedPermissions` |仅适用于 `"allow"`：要应用的 [ 权限更新条目 ](<#权限更新条目>) 数组，例如添加允许规则或更改会话权限模式|
| `message` |仅适用于 `"deny"`：告诉 Claude 权限被拒绝的原因|
| `interrupt` |仅适用于 `"deny"`：如果 `true`，则停止 Claude|

在没有 `decision` 对象的情况下退出 2 的钩子使权限流保持不变，并且其 stderr 被丢弃。要批准或拒绝请求，请返回 `decision` 对象。

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow",
      "updatedInput": {
        "command": "npm run lint"
      }
    }
  }
}
```

#### 权限更新条目

`updatedPermissions` 输出字段和 [`permission_suggestions` 输入字段 ](<#permissionrequest输入>) 都使用相同的条目对象数组。每个条目都有一个确定其其他字段的 `type` 和一个控制更改写入位置的 `destination`。

| `type` |领域|效果|
| :- | :- | :- |
| `addRules` | `rules`, `behavior`, `destination` |添加权限规则。 `rules` 是 `{toolName, ruleContent?}` 对象的数组。省略 `ruleContent` 以匹配整个工具。 `behavior` 是 `"allow"`、`"deny"` 或 `"ask"`|
| `replaceRules` | `rules`, `behavior`, `destination` |将 `destination` 处给定 `behavior` 的所有规则替换为提供的 `rules`|
| `removeRules` | `rules`, `behavior`, `destination` |删除给定 `behavior` 的匹配规则|
| `setMode` | `mode`, `destination` |更改权限模式。有效模式为 `default`、`auto`、`acceptEdits`、`dontAsk`、`bypassPermissions`、`plan` 和 `manual` 作为 `default` 的别名|
| `addDirectories` | `directories`, `destination` |添加工作目录。 `directories` 是路径字符串数组|
| `removeDirectories` | `directories`, `destination` |删除工作目录|



**注释：**

仅当您在已可用的旁路模式下启动会话时，`setMode` 和 `bypassPermissions` 才会生效：[ 用户中的 `--dangerously-skip-permissions`、`--permission-mode bypassPermissions`、`--allow-dangerously-skip-permissions` 或 `permissions.defaultMode: "bypassPermissions"`， `--settings`，或托管设置](<https://code.claude.com/docs/en/settings-reference#permissions-defaultmode>)。否则更新是无效的。当 [`permissions.disableBypassPermissionsMode`](<https://code.claude.com/docs/en/permissions#managed-settings>) 禁用该模式时，或者当会话在 [ 限制模式 ](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 下启动时，更新也是无操作。

无论 `destination` 如何，`bypassPermissions` 永远不会保留为 `defaultMode`。


每个条目上的 `destination` 字段确定更改是保留在内存中还是保留到设置文件中。

| `destination` |写入到|
| :- | :- |
| `session` |仅在内存中，会话结束时丢弃|
| `localSettings` | `.claude/settings.local.json` |
| `projectSettings` | `.claude/settings.json` |
| `userSettings` | `~/.claude/settings.json` |

挂钩可以回显它收到的 `permission_suggestions` 之一作为其自己的 `updatedPermissions` 输出。

### PostToolUse

工具成功完成后立即运行。

匹配工具名称，值与 PreToolUse 相同。

当工具名称不是正确的过滤器时，进行更广泛的匹配：

* 要在任何工具成功完成后运行挂钩，请省略 `matcher` 或将其设置为 `"*"`。然后，您的钩子可以发现自身发生了什么变化，例如通过运行 `git status --porcelain`，它还列出了 `git diff` 错过的未跟踪文件。对于失败的工具调用，请在 [PostToolUseFailure](<#posttoolusefailure>) 下添加相同的挂钩。
* 要在磁盘上的特定文件发生更改时运行挂钩（无论其内容如何），请使用 [FileChanged](<#filechanged>)。当 `Bash` 命令或 Claude Code 外部的进程重写同一文件时，Claude Code 不会运行与 `Edit|Write` 匹配的 `PostToolUse` 挂钩。

#### PostToolUse输入

`PostToolUse` 在工具成功执行后触发。输入包括 `tool_input`（发送到工具的参数）和 `tool_response`（它返回的结果）。两者的确切架构取决于工具。文件工具 `tool_input` 路径的到达格式与 [PreToolUse](<#pretooluse输入>) 相同：始终是绝对路径，带有平台的本机分隔符，因此在 Windows 上使用反斜杠。对于 MCP 工具，输入还携带 [`mcp_server`](<#pretooluse输入>) 对象。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.txt",
    "content": "file content"
  },
  "tool_response": {
    "filePath": "/path/to/file.txt",
    "type": "create"
  },
  "tool_use_id": "toolu_01ABC123...",
  "duration_ms": 12
}
```

|领域|描述|
| :- | :- |
| `duration_ms` |可选。工具执行时间（以毫秒为单位）。不包括在权限提示和 PreToolUse 挂钩中花费的时间|

#### PostToolUse决策控制

`PostToolUse` 钩子可以在工具执行后向 Claude 提供反馈。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，您的挂钩脚本还可以返回这些特定于事件的字段：

|领域|描述|
| :- | :- |
| `decision` |`"block"` 在工具结果旁边添加 `reason`。 Claude 仍然看到原始输出；要替换它，请使用 `updatedToolOutput`|
| `reason` |当`decision`为`"block"`时，对Claude显示的说明|
| `additionalContext` |与工具结果一起添加到 Claude 上下文的字符串。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|
| `classifierContext` |关于 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 分类器而不是 Claude 的此调用结果的简短说明。请参阅[注释自动模式分类器的结果](<#注释自动模式分类器的结果>)。需要 Claude Code v2.1.236 或更高版本|
| `updatedToolOutput` |在将工具的输出发送到 Claude 之前，将其替换为提供的值。该值必须与工具的输出形状匹配|
| `updatedMCPToolOutput` |仅替换 [MCP 工具 ](<#匹配mcp工具>) 的输出。首选 `updatedToolOutput`，适用于所有工具|

下面的示例替换了 `Bash` 调用的输出。替换值与 `Bash` 工具的输出形状匹配：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "Additional information for Claude",
    "updatedToolOutput": {
      "stdout": "[redacted]",
      "stderr": "",
      "interrupted": false,
      "isImage": false
    }
  }
}
```



**注意：**

`updatedToolOutput` 只改变 Claude 看到的内容。该工具在钩子触发时已经运行，因此任何写入的文件、执行的命令或发送的网络请求都已经生效。 OpenTelemetry 等遥测工具跨度和分析事件也会在挂钩运行之前捕获原始输出。要在工具运行之前阻止或修改工具调用，请改用 [PreToolUse](<#pretooluse>) 挂钩。

替换值必须与工具的输出形状匹配。内置工具返回结构化对象而不是纯字符串。例如，`Bash` 返回具有 `stdout`、`stderr`、`interrupted` 和 `isImage` 字段的对象。对于内置工具，与工具的输出架构不匹配的值将被忽略并使用原始输出。 MCP 工具输出无需架构验证即可传递。剥离 Claude 所需的错误详细信息可能会导致其继续进行错误的假设。


#### 注释自动模式分类器的结果

返回 `classifierContext` 将有关工具调用结果的简短注释发送到 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 分类器，而不是 Claude。分类器 [ 本身永远不会接收工具结果 ](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>)，因此此字段是受支持的方式，用于在审查后续操作之前告诉它有关调用返回的内容。该字段需要 Claude Code v2.1.236 或更高版本。

下面的示例告诉分类器查询的输出来自哪里：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "classifierContext": "This query ran against the staging database, not production."
  }
}
```

分类器给予注释的权重取决于您配置钩子的位置：

* ** 在 Claude Code** 中配置的挂钩：对于来自设置文件、插件、技能和 agent frontmatter 的挂钩，分类器将注释视为未经验证的、应用程序提供的上下文。该注释永远不会建立用户意图，如果它声称您批准或请求某些内容，分类器会根据您自己在对话中的消息检查该声明
* **进程内AgentSDK回调**：当嵌入Claude Code的应用程序将钩子注册为[TypeScriptSDK回调](<https://code.claude.com/docs/en/agent-sdk/hooks>)并且在实时会话期间返回注释，分类器可以将注释中转发的用户语句权衡为用户意图。这样的声明可以满足分类器从您发送的消息中接受的同意要求，但它永远不会解除您自己的消息也无法解除的障碍。会话恢复后，Claude Code 将恢复的笔记视为未经验证的上下文。当两个组的钩子注释同一个调用时，分类器将组合注释视为未经验证

Claude Code 在交付票据时应用这些限制：

* **长度**：Claude Code将一个工具调用的注释限制在2,000个字符处，并截断其余部分。该上限在响应该调用的每个钩子之间共享
* **仅同步响应**：Claude Code忽略[在后台运行的挂钩响应中的字段](<#在后台运行钩子>)，因为该响应在Claude Code记录工具结果之后到达
* **分类器不记录的调用**：分类器的转录省略只读查找，例如文件读取和搜索。 Claude Code 丢弃附加到其中一个调用的注释
* **与重写的交互**：当注释描述您要替换为`updatedToolOutput`的输出时，在同一钩子响应中返回这两个字段。如果重写被拒绝或者另一个钩子的重写替换了它，Claude Code 就会删除注释。 Claude Code 提供您返回的注释，即使另一个钩子重写了输出，也无需重写



**注意：**

分类器将您放置在 `classifierContext` 中的内容作为来自托管会话的应用程序的信息进行读取，因此请勿将不受信任的工具输出或第三方文本复制到其中。保留有关此一次调用的简短断言的注释，例如有关其来源的事实或有关它的用户声明；不要使用该字段来传递不相关的消息或事件流。


### PostToolUseFailure

当开始执行的工具失败时运行：工具抛出错误，或者 MCP 工具返回错误结果。使用它来记录故障、发送警报或向 Claude 提供纠正反馈。

匹配工具名称，值与 PreToolUse 相同。



**注释：**

对于执行前拒绝的工具调用，不会触发此事件：未知的工具名称、架构或特定于工具的验证失败的输入或权限拒绝。验证拒绝作为 `tool_use_error` 结果返回，并在钩子运行之前发生，因此它们既不触发 `PreToolUse` 也不触发 `PostToolUseFailure`。权限拒绝火灾 `PreToolUse` 但不是此事件；参见[PermissionDenied](<#permissiondenied>)。


#### PostToolUseFailure输入

PostToolUseFailure 挂钩接收与 PostToolUse 相同的 `tool_name` 和 `tool_input` 字段，以及作为顶级字段的错误信息。对于 MCP 工具，它们还会收到 [`mcp_server`](<#pretooluse输入>) 对象。例如，失败的 `npm test` 命令可能会传递：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolUseFailure",
  "tool_name": "Bash",
  "tool_input": {
    "command": "npm test",
    "description": "Run test suite"
  },
  "tool_use_id": "toolu_01ABC123...",
  "error": "Exit code 1\nError: Cannot find module 'express'",
  "is_interrupt": false,
  "duration_ms": 4187
}
```

|领域|描述|
| :- | :- |
| `error` |描述问题所在的字符串。格式取决于失败的工具|
| `is_interrupt` |可选布尔值。当失败达到 Claude Code 时为真，作为中止而不是工具报告的错误。取消正在运行的工具不会触发此钩子；工具结果携带中断消息|
| `duration_ms` |可选。工具执行时间（以毫秒为单位）。不包括在权限提示和 PreToolUse 挂钩中花费的时间|

`error` 字符串通常与作为失败工具的结果接收的文本 Claude 相同。其格式因工具和故障而异。将钩子放在 `tool_name`、`is_interrupt` 和 `Exit code N` 第一行；将字符串的其余部分视为显示文本，而不是稳定的格式。

* 对于 Bash 和 PowerShell，运行并退出的命令会生成第一行 `Exit code N`，然后该命令生成的任何输出作为一个块，其中 stdout 和 stderr 交错
* 当 Claude Code 无法启动 shell 进程本身时，有效负载还可能携带没有退出代码行的裸故障消息
* Claude Code 中间截断 `... [N characters truncated] ...` 标记周围的长字符串，并且可以插入自己的行，例如 `Command timed out after 2m 0s`

#### PostToolUseFailure决策控制

`PostToolUseFailure` 挂钩可以在工具失败后向 Claude 提供上下文。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，您的挂钩脚本还可以返回这些特定于事件的字段：

|领域|描述|
| :- | :- |
| `additionalContext` |字符串与错误一起添加到 Claude 的上下文中。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUseFailure",
    "additionalContext": "Additional information about the failure for Claude"
  }
}
```

### PostToolBatch

在批处理中的每个工具调用解决后、Claude Code 向模型发送下一个请求之前运行一次。 `PostToolUse` 每个工具触发一次，这意味着当 Claude 进行并行工具调用时它会同时触发。 `PostToolBatch` 在整个批次中只触发一次，因此它是注入上下文的正确位置，该上下文取决于运行的工具集而不是任何单个工具。此事件没有匹配器。

#### PostToolBatch输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，PostToolBatch 挂钩还接收 `tool_calls`，这是一个描述批次中每个工具调用的数组：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolBatch",
  "tool_calls": [
    {
      "tool_name": "Read",
      "tool_input": {"file_path": "/.../ledger/accounts.py"},
      "tool_use_id": "toolu_01...",
      "tool_response": "1\tfrom __future__ import annotations\n2\t..."
    },
    {
      "tool_name": "Read",
      "tool_input": {"file_path": "/.../ledger/transactions.py"},
      "tool_use_id": "toolu_02...",
      "tool_response": "1\tfrom __future__ import annotations\n2\t..."
    }
  ]
}
```

`tool_response` 包含模型在相应的 `tool_result` 块中接收的相同内容。该值是序列化字符串或内容块数组，与工具发出的完全相同。对于 `Read`，这意味着行号前缀文本而不是原始文件内容。响应可能很大，因此仅解析您需要的字段。



**注释：**

`tool_response` 的形状与 `PostToolUse` 的形状不同。 `PostToolUse` 传递工具的结构化`Output`对象，例如`{filePath: "...", type: "create"}`代表`Write`； `PostToolBatch` 传递模型看到的序列化的 `tool_result` 内容。


#### PostToolBatch决策控制

`PostToolBatch` 挂钩可以为 Claude 注入上下文。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，您的挂钩脚本还可以返回这些特定于事件的字段：

|领域|描述|
| :- | :- |
| `additionalContext` |在下一次模型调用之前注入一次上下文字符串。请参阅 [为 Claude](<#添加-claude-的上下文>) 添加上下文，了解交付详细信息、要放入的内容以及恢复的会话如何处理过去的值|

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolBatch",
    "additionalContext": "These files are part of the ledger module. Run pytest before marking the task complete."
  }
}
```

返回 `decision: "block"` 或 `continue: false` 在下一次模型调用之前停止代理循环。阻止消息来自 JSON `reason` 或 `stopReason`，或来自退出时的 stderr 2。您在记录中将其视为警告，并且它保留在对话中，因此 Claude 在对话继续时会看到它。

### PermissionDenied

当 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 拒绝工具调用时运行，包括在没有分类器判决的情况下拒绝工具调用时运行，因为 [ 与自动模式分开的安全检查拒绝了分类器自己的请求 ](<https://code.claude.com/docs/en/errors#auto-mode-cannot-determine-the-safety-of-an-action>) 或其响应未解析。此挂钩仅在自动模式下触发：当您手动拒绝权限对话框、`PreToolUse` 挂钩阻止调用或 `deny` 规则匹配时，它不会运行。使用它来记录拒绝、调整配置或告诉模型它可以重试工具调用。

匹配工具名称，值与 PreToolUse 相同。

#### PermissionDenied输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，PermissionDenied 挂钩还接收 `tool_name`、`tool_input`、`tool_use_id` 和 `reason`。对于 MCP 工具，它们还会收到 [`mcp_server`](<#pretooluse输入>) 对象。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "auto",
  "hook_event_name": "PermissionDenied",
  "tool_name": "Bash",
  "tool_input": {
    "command": "rm -rf /tmp/build",
    "description": "Clean build directory"
  },
  "tool_use_id": "toolu_01ABC123...",
  "reason": "[Irreversible Local Destruction]"
}
```

|领域|描述|
| :- | :- |
| `reason` |拒绝理由。对于分类器判决，在大多数会话中，它会在方括号中命名匹配的规则，例如 `[Data Exfiltration]`；有关其他表格，请参阅[审核拒绝](<https://code.claude.com/docs/en/auto-mode-config#review-denials>)。对于[无判决否认](<#permissiondenied决策控制>)，它以`Auto mode could not evaluate this action and is blocking it for safety`开头。对于由于分类器模型不可用而拒绝的情况，它是固定文本 `Classifier unavailable`|

#### PermissionDenied决策控制

PermissionDenied 挂钩可以告诉模型它可以重试被拒绝的工具调用。返回一个 JSON 对象，其中 `hookSpecificOutput.retry` 设置为 `true`：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionDenied",
    "retry": true
  }
}
```

当 `retry` 为 `true` 时，Claude Code 会向对话添加一条消息，告诉模型可以重试工具调用。 Claude Code 本身并不能逆转否认。如果您的钩子未返回 JSON，或返回 `retry: false`，则拒绝成立并且模型会收到原始拒绝消息。

当分类器产生 [ 没有对操作做出判决时，Claude Code 会忽略 `retry: true` ](<https://code.claude.com/docs/en/errors#auto-mode-cannot-determine-the-safety-of-an-action>)：其响应未解析，或者与自动模式分开的安全检查拒绝了分类器自己的请求。对于这些拒绝，Claude Code 已经在拒绝消息中告诉模型是稍后重试还是继续。

### 通知

当 Claude Code 发送通知时运行。匹配通知类型。省略匹配器来为所有通知类型运行挂钩。

即使桌面通知关闭，您也会收到这些挂钩事件：`preferredNotifChannel` 设置（包括 `notifications_disabled`）仅更改您收到警报的方式，而不更改您的挂钩是否运行。

|匹配器|当它着火时|
| :- | :- |
| `permission_prompt` |Claude需要您批准工具使用或沙盒命令的[网络请求](<https://code.claude.com/docs/en/sandboxing#network-isolation>)，提示已等待约六秒|
| `idle_prompt` |Claude 大约 60 秒前完成回复，此后您还没有输入过内容|
| `auth_success` |认证完成|
| `elicitation_dialog` |MCP 服务器打开一个启发表单，并且您大约有六秒钟没有输入内容|
| `elicitation_url_dialog` |MCP 服务器要求您打开浏览器 URL，并且您大约六秒钟没有键入内容|
| `elicitation_complete` |MCP 服务器报告 [URL- 模式引发 ](<#启发输入>) 已完成|
| `elicitation_response` |MCP 诱导响应被发送回服务器|
| `agent_needs_input` |当 [agent 视图 ](<https://code.claude.com/docs/en/agent-view>) 在终端中打开时，后台会话开始等待您的输入。当终端会话向您显示 [agent 团队队友的终端设置问题 ](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>) 或自动模式关于 [ 分类器请求费用 ](<https://code.claude.com/docs/en/auto-mode-classifier-billing>) 的通知并且您在大约六秒内没有键入时也会触发|
| `agent_completed` |后台会话完成或失败。仅当 [agent 视图 ](<https://code.claude.com/docs/en/agent-view>) 在终端中打开时触发|
| `quota_auto_resume_fired` |Claude Code 在 claude.ai 使用限制暂停后继续您的任务：在重置时，或者在等待期间您在 Claude Code 中执行某些操作（例如添加使用积分、升级您的计划或切换模型）时，使使用再次可用，但 [ 模型设置例外 ](<https://code.claude.com/docs/en/interactive-mode#wait-for-a-usage-limit-to-reset>)|
| `quota_auto_resume_stale` |当您的计算机休眠时间超过 30 分钟时，claude.ai 使用限制被重置。 Claude Code等待您按`Enter`而不是继续。经过较短的睡眠后，它会继续并触发 `quota_auto_resume_fired`|
| `quota_auto_resume_disabled` |Claude Code 结束对 claude.ai 使用限制的等待，而不继续您的任务：[`autoContinueAtUsageLimit`](<https://code.claude.com/docs/en/settings-reference#autocontinueatusagelimit>) 关闭或重置在等待期间移动超过 24 小时 Claude Code 自行启动，继续的任务不断达到限制，或者继续在到达模型之前被阻止。当您按 `Esc` 或 `Ctrl+C`，或选择 ** 不自动继续 ** 时，不会触发|

`quota_auto_resume_fired`、`quota_auto_resume_stale` 和 `quota_auto_resume_disabled` 类型需要 Claude Code v2.1.234 或更高版本。

在终端会话中，沙盒命令的网络请求的 `permission_prompt` 需要 Claude Code v2.1.246 或更高版本。

`agent_needs_input` 队友的终端设置问题需要 Claude Code v2.1.248 或更高版本。



**注释：**

`permission_prompt`、`idle_prompt`、`elicitation_dialog` 和 `elicitation_url_dialog` 类型与桌面通知共享它们的时间，因此在终端会话中，您只有在您似乎离开终端时才能看到它们：

  * 一旦您大约六秒钟没有打字，就会出现`permission_prompt`。当出现权限提示时计时器开始计时，并且每次击键都会延迟计时器。要在 Claude 请求使用工具的权限时立即运行挂钩，请改用 [PermissionRequest](<#permissionrequest>)。
  * 预期在 Claude 完成响应后 60 秒左右，并且仅当您此后没有键入并且没有后台 agent（例如后台 [子代理 ](<https://code.claude.com/docs/en/sub-agents>)）仍在运行时，预计会出现 `idle_prompt`。 Claude Code 在等待 claude.ai 使用限制重置时不会发送 `idle_prompt`。当等待自行结束时，将触发 `quota_auto_resume_*` 类型之一。
  * 一旦您大约六秒钟没有打字，就可以期待 `elicitation_dialog` 获取诱导表单，或者 `elicitation_url_dialog` 获取浏览器 URL 请求。两者与 `permission_prompt` 共享相同的六秒门：计时器在对话框出现时启动，每次击键都会延迟计时器。

当另一个对话框出现在屏幕上时到达的权限请求或启发将保持相同的六秒门，从请求到达时开始计时。当请求仍在打开的对话框后面等待时，您可以收到它的通知。


在向 Agent SDK 的 [`canUseTool` 回调 ](<https://code.claude.com/docs/en/agent-sdk/user-input>) 发送权限请求的会话中，Claude Code 乘以 `permission_prompt` 的方式不同，这就是 Claude 桌面和 VS Code 的方式分机主机Claude Code：

* 在 Claude 请求许可后大约六秒，预计 `permission_prompt` 会出现。 Claude Code 在您键入时不会延迟它。
* 如果您或 [PermissionRequest](<#permissionrequest>) 挂钩应答较早，Claude Code 不会运行 `permission_prompt`。
* 将 [`CLAUDE_CODE_DISABLE_PERMISSION_PROMPT_NOTIFY_HOOKS`](<https://code.claude.com/docs/en/env-vars>) 设置为 `1` 以在这些会话中关闭 `permission_prompt`。

在 v2.1.233 之前，`permission_prompt` 在这些会话中不会触发。

根据通知类型使用单独的匹配器运行不同的处理程序。当 Claude 需要权限批准时，此配置会触发特定于权限的警报脚本，并在 Claude 空闲时触发不同的通知：

```json theme={null}
{
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/permission-alert.sh"
          }
        ]
      },
      {
        "matcher": "idle_prompt",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/idle-notification.sh"
          }
        ]
      }
    ]
  }
}
```

#### 通知输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，通知挂钩还接收带有通知文本的 `message`、可选的 `title` 和指示触发哪种类型的 `notification_type`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Notification",
  "message": "Claude needs your permission",
  "title": "Permission needed",
  "notification_type": "permission_prompt"
}
```

通知挂钩无法阻止或修改通知。 Claude Code 丢弃其 `systemMessage` 和 `continue` 字段，但仍发出 [`terminalSequence`](<#发出终端通知>)，这是桌面通知示例所依赖的。通知挂钩旨在消除副作用，例如将通知转发到外部服务。

### SubagentStart

当 Claude 使用 Agent 工具生成子代理时、当 Claude [ 恢复子代理 ](<https://code.claude.com/docs/en/sub-agents#resume-subagents>) 时以及每次进程内 [agent 团队 ](<https://code.claude.com/docs/en/agent-teams>) 队友处理新消息。支持匹配器按 agent 类型名称进行过滤。对于内置 agents，这是 agent 名称，如 `general-purpose`、`Explore` 或 `Plan`。对于 [ 自定义子代理 ](<https://code.claude.com/docs/en/sub-agents>)，这是 agent 的 frontmatter 中的 `name` 字段，而不是文件名。

对于 [ 插件 ](<https://code.claude.com/docs/en/plugins/overview>) 提供的子代理，agent 类型是插件范围的标识符，例如 `my-plugin:reviewer`，而不是裸露的 frontmatter 名称。冒号将插件范围的名称放在正则表达式路径上，因此使用 `^` 和 `$` 锚定匹配器以实现精确匹配：`^my-plugin:reviewer$`。

#### SubagentStart输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，SubagentStart 挂钩还接收带有子代理的唯一标识符的 `agent_id` 和带有匹配器过滤的 agent 名称的 `agent_type`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SubagentStart",
  "agent_id": "agent-abc123",
  "agent_type": "Explore"
}
```

SubagentStart 挂钩无法阻止子代理创建，但它们可以将上下文注入子代理中。除了可用于所有钩子的 [JSON 输出字段 ](<#json-输出>) 之外，您还可以返回：

|领域|描述|
| :- | :- |
| `additionalContext` |在对话开始时、第一个提示之前添加到子代理上下文的字符串。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Follow security guidelines for this task"
  }
}
```

当挂钩针对同一子代理再次运行时，仅当子代理的上下文尚未保存先前运行的副本时，Claude Code 才会注入返回的上下文。启动时注入的副本保持不变，使子代理的 [ 提示缓存 ](<https://code.claude.com/docs/en/prompt-caching#subagents-and-the-cache>) 完好无损。在 [ 自动压缩 ](<https://code.claude.com/docs/en/sub-agents#auto-compaction>) 丢弃该副本后，Claude Code 再次注入下一次运行的上下文。

### SubagentStop

当 Claude Code 子代理完成响应时运行。匹配 agent 类型，与 SubagentStart 值相同。

#### SubagentStop输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，SubagentStop 挂钩还接收 `stop_hook_active`、`agent_id`、`agent_type`、`agent_transcript_path` 和 `last_assistant_message`。 `agent_type`字段是用于匹配器过滤的值。 `transcript_path` 是主会话的转录本，而 `agent_transcript_path` 是存储在嵌套 `subagents/` 文件夹中的子代理自己的转录本。 `last_assistant_message` 字段包含子代理最终响应的文本内容，因此挂钩可以访问它而无需解析脚本文件。

并非每个 SubagentStop 事件都来自生成的子代理 Claude。 Claude Code 还针对其自身的一些功能运行内部 agents，例如 [ 提示建议 ](<https://code.claude.com/docs/en/interactive-mode#prompt-suggestions>) 和 [`/btw` 附带问题 ](<https://code.claude.com/docs/en/interactive-mode#side-questions-with-%2Fbtw>)，并且 SubagentStop 在其中之一也完成时触发。对于这些事件，`agent_type` 是会话本身运行的 agent 名称，例如使用 [`--agent`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 或 [`agent` 设置 ](<https://code.claude.com/docs/en/settings-reference#agent>) 进行设置，并且当会话运行时没有设置该名称时，为空字符串。

命名 agent 类型的 `matcher` 与空的 `agent_type` 不匹配。匹配器被省略、`""` 或 `"*"` 的钩子，或者是匹配空字符串的正则表达式，也针对具有空 `agent_type` 的事件运行。

在 Claude Code v2.1.271 或更高版本上，与 [`SubagentHandback`](<https://code.claude.com/docs/en/tools-reference>) 工具一起运行的子代理在停止之前通过该工具传递其报告。然后，`last_assistant_message` 字段保存子代理的结束文本（如果有），该文本不是交付的报告。报告是该调用的 `message` 输入，与 `SubagentHandback` 匹配的 `PreToolUse` 或 `PostToolUse` 挂钩接收为 `tool_input.message`。

SubagentStop 挂钩还接收 [Stop input](<#停止输入>) 下描述的 `background_tasks` 和 `session_crons` 数组。两个数组的作用域都是父会话，而不是子代理。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "~/.claude/projects/.../abc123.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "SubagentStop",
  "stop_hook_active": false,
  "agent_id": "def456",
  "agent_type": "Explore",
  "agent_transcript_path": "~/.claude/projects/.../abc123/subagents/agent-def456.jsonl",
  "last_assistant_message": "Analysis complete. Found 3 potential issues...",
  "background_tasks": [],
  "session_crons": []
}
```

SubagentStop 挂钩使用与 [停止挂钩 ](<#停止决策控制>) 相同的决策控制格式，包括 `hookSpecificOutput.additionalContext`，其中 `hookEventName` 设置为 `"SubagentStop"`，以实现保持子代理运行的非错误反馈。使用 `reason` 返回 `decision: "block"` 会使子代理保持运行，并将 `reason` 传递给子代理作为其下一条指令。通过退出 2 进行阻止的钩子以相同的方式传递其 stderr 消息。要在子代理返回后将上下文注入父会话，请改用 `Agent` 工具上的 [`PostToolUse`](<#posttooluse>) 挂钩。

### TaskCreated

当通过以下方式创建任务时运行 `TaskCreate` 工具。使用它来强制命名约定、要求任务描述或阻止创建某些任务。在一个 [没有任务工具的会话](<https://code.claude.com/docs/en/tools-reference#task-tool-availability>)，该事件不会触发。

TaskCreated 钩子不支持匹配器并且每次出现时都会触发。

#### TaskCreated输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，TaskCreated 挂钩还接收 `task_id`、`task_subject` 以及可选的 `task_description`、`teammate_name` 和 `team_name`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "TaskCreated",
  "task_id": "task-001",
  "task_subject": "Implement user authentication",
  "task_description": "Add login and signup endpoints",
  "teammate_name": "implementer",
  "team_name": "session-a1b2c3d4"
}
```

|领域|描述|
| :- | :- |
| `task_id` |正在创建的任务的标识符|
| `task_subject` |任务标题|
| `task_description` |任务的详细描述。可能缺席|
| `teammate_name` |创建任务的队友的姓名。可能缺席|
| `team_name` |已弃用。会话派生的团队名称；将在未来版本中删除|
| `agent_id` |在此事件中，[公共输入字段](<#常用输入字段>)标识创建任务的子代理或[进程内队友](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>)。可能缺席。需要 Claude Code v2.1.290 或更高版本|

#### TaskCreated决策控制

TaskCreated 挂钩可以使用退出代码 2 或 JSON 决策来阻止创建。无论哪种方式，Claude Code 都会删除该任务并将您的消息作为工具错误返回给 Claude。 Claude Code 忽略此事件中的 `continue: false`，并且 Claude 继续工作。

* **退出代码2**：Claude Code返回stderr文本作为消息。
* **JSON `{"decision": "block", "reason": "..."}`**：Claude Code 返回 `reason` 作为消息。

此示例阻止主题不遵循所需格式的任务：

```bash theme={null}
#!/bin/bash
INPUT=$(cat)
TASK_SUBJECT=$(echo "$INPUT" | jq -r '.task_subject')

if [[ ! "$TASK_SUBJECT" =~ ^\[TICKET-[0-9]+\] ]]; then
  echo "Task subject must start with a ticket number, e.g. '[TICKET-123] Add feature'" >&2
  exit 2
fi

exit 0
```

### TaskCompleted

当任务被标记为已完成时运行。这会在两种情况下触发：当任何 agent 通过 TaskUpdate 工具明确将任务标记为已完成时，或者当 [agent 团队 ](<https://code.claude.com/docs/en/agent-teams>) 队友完成正在进行的任务时。使用它来强制执行完成标准，例如在任务关闭之前通过测试或 lint 检查。

TaskCompleted 钩子不支持匹配器并且每次出现时都会触发。

#### TaskCompleted输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，TaskCompleted 挂钩还接收 `task_id`、`task_subject` 以及可选的 `task_description`、`teammate_name` 和 `team_name`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "TaskCompleted",
  "task_id": "task-001",
  "task_subject": "Implement user authentication",
  "task_description": "Add login and signup endpoints",
  "teammate_name": "implementer",
  "team_name": "session-a1b2c3d4"
}
```

|领域|描述|
| :- | :- |
| `task_id` |正在完成的任务的标识符|
| `task_subject` |任务标题|
| `task_description` |任务的详细描述。可能缺席|
| `teammate_name` |完成任务的队友姓名。可能缺席|
| `team_name` |已弃用。会话派生的团队名称；将在未来版本中删除|
| `agent_id` |在此事件中，[公共输入字段](<#常用输入字段>)标识完成任务的子代理或[进程中的队友](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>)。可能缺席。需要 Claude Code v2.1.290 或更高版本|

#### TaskCompleted决策控制

TaskCompleted 钩子支持两种方式来控制任务完成：

* **退出代码2**：任务未标记为已完成，并且stderr消息作为反馈反馈给模型。
* **JSON `{"continue": false, "stopReason": "..."}`**：当队友完成回合时触发事件，完全停止队友，匹配`Stop`钩子行为。 `stopReason` 显示给用户。当【`TaskUpdate`】工具触发事件时，【Claude Code】忽略【`continue: false`】；退出代码 2 仍然阻止完成。

此示例运行测试并在失败时阻止任务完成：

```bash theme={null}
#!/bin/bash
INPUT=$(cat)
TASK_SUBJECT=$(echo "$INPUT" | jq -r '.task_subject')

# Run the test suite
if ! npm test 2>&1; then
  echo "Tests not passing. Fix failing tests before completing: $TASK_SUBJECT" >&2
  exit 2
fi

exit 0
```

### 停止

当主 Claude Code agent 完成响应时运行。如果
由于用户中断而发生停止，则不运行。 API 错误会触发 
[StopFailure](<#stopfailure>)。



**提示：**

[`/goal`](<https://code.claude.com/docs/en/goal>) 命令是会话范围内基于提示的 Stop 挂钩的内置快捷方式。当您希望 Claude 继续朝着某个条件努力而不编写钩子配置时，请使用它。


#### 停止输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，停止挂钩还接收 `stop_hook_active`、`last_assistant_message`、`background_tasks` 和 `session_crons`。当 Claude Code 由于停止挂钩而已经继续时，`stop_hook_active` 字段为 `true`。检查此值或处理记录以避免在永远无法解决的情况下发生阻塞。

Claude Code 应用 8 连续继续上限：在停止钩连续继续转弯八次后，Claude Code 覆盖下一个块并结束转弯。每次 Claude 调用工具时，连续连续计数都会重置。要提高上限，请设置 [`CLAUDE_CODE_STOP_HOOK_BLOCK_CAP`](<https://code.claude.com/docs/en/env-vars>)。

`last_assistant_message` 字段包含 Claude 最终响应的文本内容，因此钩子可以在不解析脚本文件的情况下访问它。对于作用于刚刚完成的回合的钩子，例如朗读或通知钩子，请使用此字段而不是读取 `transcript_path`：转录文件不保证在所有版本上都包含停止时间的最终消息。

`background_tasks` 和 `session_crons` 数组让钩子区分“会话已完成”和“会话已暂停，等待后台工作将其唤醒”。当任务注册表可访问时，这两个数组都存在；当没有任何活动或计划时，这两个数组都为空。

`background_tasks` 中的每个条目都描述一个正在进行的任务并使用以下字段：

|领域|描述|
| :- | :- |
| `id` |任务标识符|
| `type` |友好的任务类型标签，例如 `shell`、`subagent`、`monitor`、`workflow`、`teammate`、`cloud session` 或 `MCP task`。每个标签标识哪个 Claude Code 功能创建了该任务。对于无法识别的类型，退回到原始判别式|
| `status` |当前任务状态|
| `description` |自由文本描述，上限为 1000 个字符，剪切时带有字符串内 `… [+N chars]` 标记|
| `command` |Shell 命令行，上限为 1000 个字符。仅适用于 `shell` 任务|
| `agent_type` |子代理类型名称。仅适用于 `subagent` 任务|
| `server` |MCP 服务器名称。仅适用于 `monitor` 和 `MCP task` 任务|
| `tool` |MCP 工具名称。仅适用于 `monitor` 和 `MCP task` 任务|
| `name` |工作流程名称。仅适用于 `workflow` 任务|

`session_crons` 中的每个条目都描述了一个会话范围的计划唤醒，源自 `CronCreate`、`ScheduleWakeup` 和 `/loop`：

|领域|描述|
| :- | :- |
| `id` |Cron 任务标识符|
| `schedule` |Cron 表达式，例如 `0 9 * * 1-5`|
| `recurring` |`false` 用于一次性唤醒，其时间表对单个触发时间进行编码，`true` 用于在每场比赛中重新触发的任务|
| `prompt` |cron 触发时提交的提示，上限为具有相同 `… [+N chars]` 标记的 1000 字符|

此示例显示了一个停止输入，其中包含一个正在进行的 shell 任务和一个重复的 cron：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "~/.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "Stop",
  "stop_hook_active": true,
  "last_assistant_message": "I've completed the refactoring. Here's a summary...",
  "background_tasks": [
    {
      "id": "task-001",
      "type": "shell",
      "status": "running",
      "description": "tail logs",
      "command": "tail -f /var/log/syslog"
    }
  ],
  "session_crons": [
    {
      "id": "cron-001",
      "schedule": "0 9 * * 1-5",
      "recurring": true,
      "prompt": "check the build"
    }
  ]
}
```

#### 停止决策控制

`Stop`和`SubagentStop`钩子可以控制Claude是否继续。除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，您的挂钩脚本还可以返回这些特定于事件的字段：

|领域|描述|
| :- | :- |
| `decision` |`"block"` 阻止 Claude 停止。省略允许 Claude 停止|
| `reason` |当`decision`为`"block"`时需要。告诉Claude为什么应该继续|
| `hookSpecificOutput.additionalContext` |Claude 的无错误反馈。对话继续，因此 Claude 可以对其采取行动，但与 `decision: "block"` 不同的是，它在记录中显示为钩子反馈而不是钩子错误|

通过退出 2 进行阻止的钩子的路由方式与 `reason` 相同：Claude 接收 stderr 消息作为其应继续的解释。

```json theme={null}
{
  "decision": "block",
  "reason": "Must be provided when Claude is blocked from stopping"
}
```

当钩子按设计工作并提供 Claude 指导时使用 `additionalContext`，例如“在完成之前运行测试套件”。它使对话经历与 `decision: "block"` 相同的循环保护，即 `stop_hook_active` 输入和 8 连续连续上限，但记录将其标记为 `Stop hook feedback` 并且不显示挂钩错误通知：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Stop",
    "additionalContext": "Please run the test suite before finishing"
  }
}
```

### StopFailure

当回合因 API 错误而结束时，运行而不是 [停止](<#停止>)。除了 [`terminalSequence`](<#发出终端通知>) 之外，Claude Code 忽略钩子的输出和退出代码。当 Claude 由于速率限制、身份验证问题或其他 API 错误而无法完成响应时，使用此功能来记录故障、发送警报或采取恢复操作。

#### StopFailure输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，StopFailure 挂钩还接收 `error`、可选 `error_details` 和可选 `last_assistant_message`。 `error`字段标识错误类型并用于匹配器过滤。

|领域|描述|
| :- | :- |
| `error` |错误类型：`rate_limit`、`overloaded`、`authentication_failed`、`oauth_org_not_allowed`、`account_on_hold`、`billing_error`、`invalid_request`、`model_not_found`、 `server_error`、`max_output_tokens`、`cloud_credential_error` 或 `unknown`|
| `error_details` |有关错误的其他详细信息（如果有）|
| `last_assistant_message` |对话中显示的渲染错误文本。与 `Stop` 和 `SubagentStop` 不同，该字段保存 Claude 的会话输出，对于 `StopFailure`，它包含 API 错误字符串本身，例如 `"API Error: Rate limit reached"`|

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "StopFailure",
  "error": "rate_limit",
  "error_details": "429 Too Many Requests",
  "last_assistant_message": "API Error: Rate limit reached"
}
```

StopFailure 钩子没有决策控制。它们仅出于通知和记录目的而运行。

### TeammateIdle

当[agent团队](<https://code.claude.com/docs/en/agent-teams>)队友完成回合后即将闲置时运行。使用此功能可以在队友停止工作之前强制实施质量关卡，例如要求通过 lint 检查或验证输出文件是否存在。

TeammateIdle 钩子不支持匹配器并且每次出现时都会触发。

#### TeammateIdle输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，TeammateIdle 挂钩还接收 `teammate_name` 和 `team_name`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "TeammateIdle",
  "teammate_name": "researcher",
  "team_name": "session-a1b2c3d4"
}
```

|领域|描述|
| :- | :- |
| `teammate_name` |即将闲置的队友名字|
| `team_name` |已弃用。会话派生的团队名称；将在未来版本中删除|
| `agent_id` |在此事件中，[公共输入字段](<#常用输入字段>)标识即将闲置的[正在处理的队友](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>)。可能缺席。需要 Claude Code v2.1.290 或更高版本|

#### TeammateIdle决策控制

TeammateIdle 钩子支持两种控制队友行为的方式：

* **退出代码2**：队友收到stderr消息作为反馈并继续工作而不是闲置。
* **JSON `{"continue": false, "stopReason": "..."}`**：完全停止队友，匹配`Stop`钩子行为。 `stopReason` 显示给用户。

此示例在允许队友闲置之前检查构建工件是否存在：

```bash theme={null}
#!/bin/bash

if [ ! -f "./dist/output.js" ]; then
  echo "Build artifact missing. Run the build before stopping." >&2
  exit 2
fi

exit 0
```

### ConfigChange

当配置文件在会话期间发生更改时运行。使用它来审核设置更改、实施安全策略或阻止对配置文件进行未经授权的修改。

当设置文件、托管策略文件或技能文件更改时，Claude Code 运行 ConfigChange 挂钩。对于托管策略，仅当 `managed-settings.json` 或 `managed-settings.d/` 中的文件发生更改时才运行它们。它应用[服务器管理的设置](<https://code.claude.com/docs/en/server-managed-settings>)并对macOS管理的首选项或Windows注册表策略进行更改，而不运行它们。在 WSL 和 [`wslInheritsWindowsSettings`](<https://code.claude.com/docs/en/settings-reference#wslinheritswindowssettings>) 上，它还会在其策略轮询中应用更改的 Windows 端托管设置文件，而不运行它们。

匹配器过滤配置源：

|匹配器|当它着火时|
| :- | :- |
| `user_settings` |`~/.claude/settings.json` 变更|
| `project_settings` |`.claude/settings.json` 变更|
| `local_settings` |`.claude/settings.local.json` 变更|
| `policy_settings` |`managed-settings.json` 或 `managed-settings.d/` 中的文件发生更改|
| `skills` |`.claude/skills/`中的技能文件发生变化|

此示例记录安全审核的所有配置更改：

```json theme={null}
{
  "hooks": {
    "ConfigChange": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/audit-config-change.sh",
            "args": []
          }
        ]
      }
    ]
  }
}
```

#### ConfigChange输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，ConfigChange 挂钩还接收 `source` 和可选的 `file_path`。 `source` 字段指示更改的配置类型，`file_path` 提供已修改的特定文件的路径。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "ConfigChange",
  "source": "project_settings",
  "file_path": "/Users/.../my-project/.claude/settings.json"
}
```

#### ConfigChange决策控制

ConfigChange 挂钩可以阻止配置更改生效。使用退出代码 2 或 JSON `decision` 来防止更改。阻止时，新设置不会应用于正在运行的会话。

|领域|描述|
| :- | :- |
| `decision` |`"block"` 阻止应用配置更改。省略以允许更改|
| `reason` |已接受但从未显示|

```json theme={null}
{
  "decision": "block",
  "reason": "Configuration changes to project settings require admin approval"
}
```

`policy_settings` 无法阻止更改。当计算机上的托管设置文件发生更改时，挂钩仍然会触发 `policy_settings` 源，因此您可以使用它们来记录这些编辑，但任何阻止决定都会被忽略。这可确保企业管理的设置始终生效。当 [ 服务器管理的设置 ](<https://code.claude.com/docs/en/server-managed-settings>) 到达或刷新时，Claude Code 不会运行 `ConfigChange` 挂钩。

Claude Code 根据 ConfigChange 挂钩的 JSON 输出的阻塞决策进行操作，并丢弃 `systemMessage` 和 `continue`。被阻止的更改不会向您或 Claude 显示任何消息，无论您使用 `reason` 阻止还是退出时使用 stderr 2。 Claude Code 仅向调试日志写入一行。

### CwdChanged

当主对话中的 shell 命令更改工作目录时运行，例如当 Claude 执行 `cd` 命令时。使用它来对目录更改做出反应：重新加载环境变量、激活特定于项目的工具链或自动运行安装脚本。与 [FileChanged](<#filechanged>) 配合使用 [direnv](<https://direnv.net/>) 等管理每个目录环境的工具。

CwdChanged 挂钩可以访问 [`CLAUDE_ENV_FILE`](<#持久化环境变量>)。写入该文件的变量会保留到后续的 Bash 命令中，直到下一个 CwdChanged 事件发生，此时 Claude Code 会清除它们。

CwdChanged 不支持匹配器并在每次出现时触发。

#### CwdChanged输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，CwdChanged 挂钩还接收 `old_cwd` 和 `new_cwd`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project/src",
  "hook_event_name": "CwdChanged",
  "old_cwd": "/Users/my-project",
  "new_cwd": "/Users/my-project/src"
}
```

#### CwdChanged 输出

除了可用于所有钩子的 [JSON 输出字段 ](<#json-输出>) 之外，CwdChanged 钩子还可以返回 `watchPaths` 来动态设置 [FileChanged](<#filechanged>) 监视哪些文件路径：

|领域|描述|
| :- | :- |
| `watchPaths` |绝对路径数组。替换当前的动态监视列表。 `matcher` 配置中的路径始终受到监视。返回空数组会清除动态列表，这在进入新目录时很常见|

CwdChanged 钩子没有决策控制。他们无法阻止目录更改。

Claude Code 从 JSON 输出中读取 `watchPaths` 和 `systemMessage` 并丢弃 `continue`。在交互式会话中，它显示 `systemMessage` 作为简短的终端通知。该消息未到达 SDK 消息流。

### DirectoryAdded

在您使用 `/add-dir` 命令添加工作目录后运行，或者在 SDK 客户端使用 `register_repo_root` 控制请求添加工作目录后运行。使用它来准备新添加的存储库，例如通过安装其依赖项。

Claude Code 在以下情况下不会触发此事件：

* 您传递带有 `--add-dir` 启动标志的目录； [SessionStart](<#sessionstart>) 涵盖这些目录
* 您在 `/permissions` 工作空间选项卡上添加一个目录
* 您添加一个已经是工作目录或内部的目录

Claude Code 在刷新沙箱和权限状态后触发 DirectoryAdded，因此当您的钩子运行时，沙箱工具已经看到新目录。 Hook 命令本身在未沙盒的情况下运行。

Claude Code 不等待挂钩：添加立即完成，挂钩在后台运行，默认超时为 600 秒。

匹配器过滤目录的添加方式：

|匹配器|当它着火时|
| :- | :- |
| `slash_command` |您添加一个带有 `/add-dir` 的目录|
| `register_repo_root` |SDK 客户端使用 `register_repo_root` 控制请求添加目录|

#### DirectoryAdded输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，DirectoryAdded 挂钩还接收 `directory` 和 `source`。

|领域|描述|
| :- | :- |
| `directory` |添加目录的绝对路径|
| `source` |如何添加目录，`/add-dir` 为 `"slash_command"` 或 SDK 控制请求为 `"register_repo_root"`|

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "DirectoryAdded",
  "directory": "/Users/my-other-repo",
  "source": "slash_command"
}
```

DirectoryAdded 钩子没有决策控制。他们无法阻止添加，该添加在钩子运行时已经完成。 Claude Code 丢弃 JSON 输出中的 `continue` 字段，并根据来源以不同方式显示其余部分：

* `slash_command`：Claude Code 将挂钩的 `systemMessage` 传递到 Claude 作为下一个对话回合的上下文，而不是将其显示给您。失败的钩子计数出现在记录中。完整的失败输出将写入调试日志
* `register_repo_root`：Claude Code仅将`systemMessage`输出和失败输出写入调试日志

### FileChanged

当磁盘上监视的文件发生更改时运行。 Claude Code 使用文件系统观察程序而不是通过检查工具调用来检测更改，因此无论文件发生什么变化，它都会运行挂钩：`Edit` 或 `Write` 工具调用、脚本 Claude 与 `Bash` 一起运行，或者完全在 Claude Code 之外的进程。常见用途是在项目配置文件更改时重新加载环境变量。

本次活动的`matcher`有两个角色：

* **构建监视列表**：该值在`|`上分割，每个段在工作目录中注册为文字文件名，因此`".envrc|.env"`准确监视这两个文件。正则表达式模式在这里没有用：像 `^\.env` 这样的值会监视字面上名为 `^\.env` 的文件。
* **过滤哪个钩子运行**：当监视的文件发生更改时，相同的值会使用标准[匹配器规则](<#匹配器模式>)针对更改的文件的基本名称过滤哪些钩子组运行。

此示例在进行任何更改后标准化 `data.csv` 中的行结尾，包括 `Bash` 命令或重写文件的外部脚本：

```json theme={null}
{
  "hooks": {
    "FileChanged": [
      {
        "matcher": "data.csv",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/normalize-line-endings.sh"
          }
        ]
      }
    ]
  }
}
```

该钩子从标准输入上的 [JSON 输入 ](<#filechanged输入>) 的 `file_path` 字段读取已更改文件的绝对路径。它的 `grep` 防护测试与 `perl` 删除的内容相同，即行末尾的 CR，因此规范化后的运行将退出而不触及文件。较宽松的保护会永远循环，因为 `perl -i` 会重写文件，即使它不替换任何内容，并且 Claude Code 在每次重写后都会再次运行挂钩。将此脚本保存在 `/path/to/normalize-line-endings.sh` 并使其可执行：

```bash theme={null}
#!/bin/bash
FILE=$(jq -r .file_path)
if grep -q $'\r$' "$FILE"; then
  perl -pi -e 's/\r$//' "$FILE"
fi
```

要确认挂钩是否有效，请要求 Claude 使用 `Bash` 命令将 CRLF 行附加到 `data.csv`。 Claude Code 运行挂钩，文件以 LF 结尾。

要监视无法预先命名的文件，请从挂钩返回 [`watchPaths`](<#filechanged-输出>) 以动态更新监视列表。 Claude Code 仅在指定要监视的文件时启动监视程序，因此使用匹配器命名至少一个文件的 FileChanged 组或返回 `watchPaths` 的 [SessionStart](<#sessionstart决策控制>) 或 [CwdChanged](<#cwdchanged>) 钩子来为列表播种。匹配器仍然会过滤监视文件更改时运行的钩子组，因此为处理动态路径的组提供一个省略的匹配器，该匹配器匹配每个监视文件并且不会向监视列表添加任何内容。 `"*"` 匹配器也匹配每个文件，但 Claude Code 将其像任何其他值一样注册在监视列表中，作为名为 `*` 的文字文件。

FileChanged 挂钩可以访问 [`CLAUDE_ENV_FILE`](<#持久化环境变量>)。写入该文件的变量会保留到后续的 Bash 命令中，直到下一个 [CwdChanged](<#cwdchanged>) 事件发生，此时 Claude Code 会清除它们。

#### FileChanged输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，FileChanged 挂钩还接收 `file_path` 和 `event`。

|领域|描述|
| :- | :- |
| `file_path` |已更改文件的绝对路径|
| `event` |发生了什么：`"change"` 表示已修改的文件，`"add"` 表示已创建的文件，或 `"unlink"` 表示已删除的文件|

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "FileChanged",
  "file_path": "/Users/my-project/.envrc",
  "event": "change"
}
```

#### FileChanged 输出

除了可用于所有挂钩的 [JSON 输出字段 ](<#json-输出>) 之外，FileChanged 挂钩还可以返回 `watchPaths` 以动态更新监视哪些文件路径：

|领域|描述|
| :- | :- |
| `watchPaths` |绝对路径数组。替换当前的动态监视列表。 `matcher` 配置中的路径始终受到监视。当您的挂钩脚本根据更改的文件发现要监视的其他文件时，请使用此选项|

FileChanged 钩子没有决策控制。他们无法阻止文件更改的发生。

Claude Code 从 JSON 输出中读取 `watchPaths` 和 `systemMessage` 并丢弃 `continue`。在交互式会话中，它显示 `systemMessage` 作为简短的终端通知。该消息未到达 SDK 消息流。

### WorktreeCreate

在创建工作树时运行，无论是来自 `claude --worktree`，从一个 [子代理使用 `isolation: "worktree"`](<https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope>)，或者对于一个 [后台会话](<https://code.claude.com/docs/en/agent-view#how-file-edits-are-isolated>) 那个 Claude Code 隔离在它自己的工作树中。默认情况下 Claude Code 创建隔离的工作副本 `git worktree`。配置一个 WorktreeCreate hook 取代了默认的 git 行为，让您可以使用不同的版本控制系统，例如 SVN、Perforce 或 Mercurial。

由于该挂钩完全替换了默认行为，因此不会处理 [`.worktreeinclude`](<https://code.claude.com/docs/en/worktrees#copy-gitignored-files-into-worktrees>)。如果您需要将 `.env` 等本地配置文件复制到新工作树中，请在挂钩脚本中执行此操作。

挂钩必须返回创建的工作树目录的路径。 Claude Code 使用此路径作为隔离会话的工作目录。请参阅 [WorktreeCreate 输出 ](<#worktreecreate-输出>) 了解每种钩子类型如何返回路径。

Claude Code作用于钩子成功和返回的路径，并丢弃`systemMessage`和`continue`。

此示例创建 SVN 工作副本并打印 Claude Code 使用的路径。将存储库 URL 替换为您自己的存储库：

```json theme={null}
{
  "hooks": {
    "WorktreeCreate": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'NAME=$(jq -r .name); DIR=\"$HOME/.claude/worktrees/$NAME\"; svn checkout https://svn.example.com/repo/trunk \"$DIR\" >&2 && echo \"$DIR\"'"
          }
        ]
      }
    ]
  }
}
```

该钩子从标准输入上的 JSON 输入读取工作树 `name`，将新副本检出到新目录中，并打印目录路径。最后一行的 `echo` 是 Claude Code 读取的工作树路径。将任何其他输出重定向到 stderr，这样它就不会干扰路径。

#### WorktreeCreate输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，WorktreeCreate 挂钩还接收 `name` 字段。这是新工作树的段标识符，由用户指定或自动生成，例如 `bold-oak-a3f2`。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "WorktreeCreate",
  "name": "feature-auth"
}
```

#### WorktreeCreate 输出

WorktreeCreate 挂钩不使用标准 allow/block 决策模型。相反，钩子的成败决定了结果。该钩子必须返回创建的工作树目录的路径：

* **命令挂钩** (`type: "command"`)：将路径打印为stdout的最后一个非空行。 Claude Code 在读取该行之前剥离 ANSI 转义码，因此在 `echo` 之前打印的 shell 启动横幅将被忽略。将任何其他挂钩输出重定向到 stderr。
* **HTTP hooks** (`type: "http"`)：在响应正文中返回 `{ "hookSpecificOutput": { "hookEventName": "WorktreeCreate", "worktreePath": "/absolute/path" } }`。

如果挂钩失败或未生成路径，则工作树创建将失败并出现错误。

Claude Code 解析钩子运行目录的相对路径，折叠其中的任何 `.` 或 `..` 段。如果生成的路径不是 Claude Code 可以输入的目录，会话将打印一条命名路径的错误并以代码 1 退出。

Claude Code 拒绝包含 `.` 或 `..` 段的绝对路径，以及通过存储库根目录下的符号链接的任何路径，因为提交到存储库的符号链接可能会将工作树重定向到存储库之外。该错误命名了被拒绝的组件。返回不通过存储库内的符号链接的规范化路径。在v2.1.216之前，工作树的创建遵循钩子的路径，没有这种筛选。

### WorktreeRemove

当 Claude Code 清理 [`WorktreeCreate`](<#worktreecreate>) 挂钩创建的工作树时运行。该事件在以下情况下触发：

* 您退出交互式 [ 工作树会话 ](<https://code.claude.com/docs/en/worktrees#start-claude-in-a-worktree>) 并在 Claude Code 提示时选择删除工作树
* 您退出了尚未退出的交互式工作树会话 [命名的](<https://code.claude.com/docs/en/sessions#name-your-sessions>), Claude Code 找不到已更改或未跟踪的文件，并且会在不提示您的情况下删除工作树
* 您删除在工作树中运行的 [ 后台会话 ](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>)

Claude Code 使用 git 查找已更改或未跟踪的文件，因此它在非 git checkout 或内部的工作树中找不到任何文件，即使该目录包含未提交的工作也是如此。在删除任何内容之前，请检查 WorktreeRemove 挂钩中的工作。

对于基于 git 的工作树，Claude Code 使用 `git worktree remove` 自动处理清理。如果您配置了 WorktreeCreate 挂钩，请将其与 WorktreeRemove 挂钩配对以控制其创建的工作树的清理：

* **没有WorktreeRemove钩子**：当Claude Code在退出工作树会话时删除工作树时，它会回退到WorktreeCreate钩子返回的路径上的`git worktree remove --force`，因此git识别的工作树被删除。 git 无法识别工作树，例如使用非 git 版本控制系统创建的钩子，它保留在磁盘上。有关删除 [ 后台会话 ](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>) 对挂钩创建的工作树执行的操作，请参阅 agent 视图的删除规则。
* **Hook 退出 0**：工作树算作已删除。 Claude Code 从挂钩中没有读取任何其他内容，因此请确保您的挂钩删除了该目录。
* **Hook 以非零值退出**：如果 `worktree_path` 处的目录随后仍然存在，则删除失败，并且工作树保留在磁盘上，没有 git 回退。在退出非零之前删除目录的钩子算作已删除。如何报告失败请参见[WorktreeRemove输入](<#worktreeremove输入>)。

Claude Code 永远不会删除属于钩子创建的工作树的分支，因为它只知道 WorktreeCreate 钩子返回的路径。如果您的 WorktreeCreate 挂钩创建了分支，请在 WorktreeRemove 挂钩中将其删除。

Claude Code 丢弃 WorktreeRemove 挂钩的 [JSON 输出字段 ](<#json-输出>)，例如 `systemMessage` 和 `continue`。

对于后台会话删除，Claude Code 在运行挂钩之前验证存储的工作树路径，并拒绝符号链接或通过存储库根目录下的路径。仅当您在 [agent 视图 ](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>) 中确认删除时，该挂钩才会针对仍包含文件的工作树运行；对于这样的工作树，[`claude rm`](<https://code.claude.com/docs/en/agent-view#manage-sessions-from-the-shell>) 保留会话和工作树。在 v2.1.216 之前，钩子在存储的路径上运行而没有这些检查。

Claude Code 将 WorktreeCreate 返回的路径作为挂钩输入中的 `worktree_path` 传递。此示例读取该路径并删除该目录：

```json theme={null}
{
  "hooks": {
    "WorktreeRemove": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'jq -r .worktree_path | xargs rm -rf'"
          }
        ]
      }
    ]
  }
}
```

#### WorktreeRemove输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，WorktreeRemove 挂钩还接收 `worktree_path` 字段，这是要删除的工作树的绝对路径。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "WorktreeRemove",
  "worktree_path": "/Users/.../my-project/.claude/worktrees/feature-auth"
}
```

WorktreeRemove 钩子的退出代码决定结果。当钩子以非零值退出并且 `worktree_path` 处的目录随后仍然存在时，删除失败：

* 工作树保留在磁盘上，挂钩的命令和 stderr 转到 [ 调试日志 ](<#调试钩子>)。
* 如果您要删除后台会话，该会话也会保留。 [agent视图](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>)中的拒绝消息报告钩子如何结束，例如`exited 1`，引用其stderr的开头，并说明是否再次删除会话会删除目录。

### PreCompact

在 Claude Code 即将运行紧凑操作之前运行。

匹配器值指示压缩是手动还是自动触发：

|匹配器|当它着火时|
| :- | :- |
| `manual` | `/compact` |
| `auto` |当对话到达 [ 自动压缩窗口 ](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) 时自动压缩|

使用代码 2 退出以阻止压缩。对于手册 `/compact`，将向用户显示 stderr 消息。您还可以通过使用 `"decision": "block"` 返回 JSON 来阻止。

阻止自动压缩会根据其触发时间而产生不同的效果。如果在上下文限制之前主动触发压缩，Claude Code 会跳过它，对话将继续不压缩。如果触发压缩以从 API 已返回的上下文限制错误中恢复，则底层错误会出现并且当前请求失败。

Claude Code 丢弃 PreCompact 挂钩的 `systemMessage` 和 `continue` 字段。

#### PreCompact输入

除了 [公共输入字段](<#常用输入字段>), PreCompact 钩子接收 `trigger` 和 `custom_instructions`。对于 `manual`, `custom_instructions` 包含用户传入的内容 `/compact` 并且是 `null` 当他们什么也没经过时。对于 `auto`, `custom_instructions` 是 `null`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PreCompact",
  "trigger": "manual",
  "custom_instructions": null
}
```

### PostCompact

Claude Code 完成压缩操作后运行。使用此事件对新的压缩状态做出反应，例如记录生成的摘要或更新外部状态。 Claude Code 丢弃 PostCompact 挂钩的 `systemMessage` 和 `continue` 字段。

相同的匹配器值适用于 `PreCompact`：

|匹配器|当它着火时|
| :- | :- |
| `manual` |`/compact`之后|
| `auto` |当对话到达 [ 自动压缩窗口 ](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) 时自动压缩后|

#### PostCompact输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，PostCompact 挂钩还接收 `trigger` 和 `compact_summary`。 `compact_summary`字段包含压缩操作生成的会话摘要。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PostCompact",
  "trigger": "manual",
  "compact_summary": "Summary of the compacted conversation..."
}
```

PostCompact 钩子没有决策控制。它们不会影响压实结果，但可以执行后续任务。

### PreModelSwitch

在 Claude Code 应用您或客户请求的模型切换之前运行。使用它来阻止切换、要求确认或在切换发生之前显示切换的成本。

PreModelSwitch 需要 Claude Code v2.1.251 或更高版本。 Claude Code 针对以下请求运行它：

* `/model <name>` 和 `/model` 选择器
* `Option+P` 或 `Alt+P` 模型选择器
* `/config`中的型号设置
* 在更改会话模型时打开 [ 快速模式 ](<https://code.claude.com/docs/en/fast-mode>)
* 来自 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/typescript#query-object>) 主机或 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 的 `set_model` 请求或 `apply_flag_settings` 请求中的模型更改

Claude Code 不会为其自行创建的开关运行 PreModelSwitch 挂钩，例如 [ 自动模型回退 ](<https://code.claude.com/docs/en/model-config#automatic-model-fallback>) 或在恢复会话时恢复模型。这些更改仅到达 [PostModelSwitch](<#postmodelswitch>)。

Claude Code 将匹配器与会话正在切换到的模型的规范名称进行比较，忽略任何 `[1m]` 后缀。诸如 `opus` 之类的别名、过时的模型 ID 以及特定于提供商的 ID（例如 Amazon Bedrock 模型 ID）都与它们解析到的一个规范名称相匹配，因此 `claude-opus-5` 涵盖了 Opus 5 的每一种拼写。

当 Claude Code 无法确定目标的规范名称时，例如只有您的 [LLM 网关 ](<https://code.claude.com/docs/en/llm-gateway>) 知道的自定义模型 ID，它会运行每个 PreModelSwitch 挂钩，无论匹配器如何。因此，阻塞的钩子应该从其输入中检查 `to_model`，而不是单独依赖匹配器。

将匹配器编写为精确名称、以 `|` 分隔的列表（例如 `claude-opus-4-6|claude-opus-5`）或正则表达式（例如 `.*opus.*`）。此示例使用精确名称匹配器，并且还从挂钩输入检查 `to_model`，因此它通过使用代码 2 退出来拒绝切换到 Opus 4.6，并让任何其他目标通过：



  

#### macOS/Linux

该命令检查 `to_model` 和 `jq`：

    ```json theme={null}
    {
      "hooks": {
        "PreModelSwitch": [
          {
            "matcher": "claude-opus-4-6",
            "hooks": [
              {
                "type": "command",
                "command": "jq -e '.to_model | test(\"opus-4-6\")' > /dev/null && { echo 'Opus 4.6 is retired for this project. Use a newer model.' >&2; exit 2; }; exit 0"
              }
            ]
          }
        ]
      }
    }
    ```
  


  

#### Windows（PowerShell）

通过PowerShell注册一个运行脚本的命令钩子：

    ```json theme={null}
    {
      "hooks": {
        "PreModelSwitch": [
          {
            "matcher": "claude-opus-4-6",
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-opus-46.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

将此脚本保存到项目中的 `.claude/hooks/block-opus-46.ps1`：

    ```powershell theme={null}
    $hookInput = [Console]::In.ReadToEnd() | ConvertFrom-Json
    if ($hookInput.to_model -match 'opus-4-6') {
      [Console]::Error.WriteLine('Opus 4.6 is retired for this project. Use a newer model.')
      exit 2
    }
    exit 0
    ```
  


要确认挂钩是否有效，请从运行不同模型的会话中运行 `/model claude-opus-4-6`。 Claude Code 保留当前模型并报告 PreModelSwitch 挂钩阻塞了交换机，原因是您的消息。

#### PreModelSwitch输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，PreModelSwitch 挂钩还接收此表中的字段。最后五个描述了将对话重新发送到新模型的成本，因此挂钩可以在切换发生之前显示该数字。

|领域|类型|描述|
| :- | :- | :- |
| `from_model` |字符串|型号ID开关由|
| `to_model` |字符串|开关更改为型号ID。匹配器与该模型的规范名称进行比较|
| `requested_model` |字符串或 `null`|请求命名的模型：别名，例如 `opus`、完整模型 ID 或 `null`（当请求针对默认模型时）|
| `source` |字符串|请求来源：`/model <name>`的`"command"`，`/config`中的Model设置，或开启快速模式； `"picker"` 用于模型选择器；来自 Agent SDK 主机或远程控制的 `set_model` 请求的 `"sdk"`，或 `apply_flag_settings` 请求中的型号更改|
| `context_tokens` |数量|下一个请求作为其提示重新发送的令牌：主对话中最后一个响应的输入、缓存读取、缓存创建和输出令牌的组合。 `0` 在第一次回复之前|
| `prompt_cache_warm` |布尔值|当前模型的提示缓存是否可能仍然是热的，这意味着交换机会放弃它|
| `cache_ttl` |字符串|[提示缓存生命周期](<https://code.claude.com/docs/en/prompt-caching#cache-lifetime>) Claude Code此会话的请求：`"5m"` 或 `"1h"`|
| `estimated_cache_write_usd` |数量|以 `cache_ttl` 速率将 `context_tokens` 写入 `to_model` 上的提示缓存的预计成本为 US 美元，不包括下一个响应。服务器可能不需要重新缓存整个上下文，因此将其视为估计|
| `pricing` |字符串|Claude Code 如何定价 `estimated_cache_write_usd`：`"configured"` 按贵组织自己的费率进行配置，`"catalog"` 按标价，或 `"default"`（当 `to_model` 没有已知价格且 Claude Code 假定默认费率时）|

此示例显示运行 Sonnet 5 的会话中 `/model opus` 的输入：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PreModelSwitch",
  "from_model": "claude-sonnet-5",
  "to_model": "claude-opus-5",
  "requested_model": "opus",
  "source": "command",
  "context_tokens": 182340,
  "prompt_cache_warm": true,
  "cache_ttl": "5m",
  "estimated_cache_write_usd": 1.1396,
  "pricing": "catalog"
}
```

#### PreModelSwitch决策控制

`PreModelSwitch`钩子可以取消切换，要求用户确认，或者让它继续进行。退出代码 2 或顶级 `decision: "block"` 取消切换。

为了进行更精细的控制，请在 `hookSpecificOutput` 对象中返回 `permissionDecision` 和 `permissionDecisionReason`，如 [PreToolUse](<#pretooluse决策控制>) 所示。 `PreModelSwitch` 接受 `"allow"`、`"deny"` 和 `"ask"`。它不接受 `"defer"`、`updatedInput` 或 `additionalContext`。下表描述了这两个字段：

|领域|描述|
| :- | :- |
| `permissionDecision` |`"allow"` 继续并跳过提示缓存处于热状态时显示的 [ 确认 Claude Code ](<https://code.claude.com/docs/en/prompt-caching#switching-models>)。 `"deny"`取消切换。 `"ask"`提示用户确认|
| `permissionDecisionReason` |对于 `"deny"`，向用户显示为切换被阻止的原因，或作为 `set_model` 请求的错误返回。对于`"ask"`，显示在确认提示中。忽略 `"allow"`|

只有交互会话中的`/model`才能显示`"ask"`提示符。在所有其他表面上，包括带有 `-p` 标志、`/config` 和 `set_model` 请求的非交互模式，Claude Code 将 `"ask"` 视为拒绝。

此示例要求用户确认并引用 `context_tokens` 中的令牌计数：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreModelSwitch",
    "permissionDecision": "ask",
    "permissionDecisionReason": "Switching now re-sends about 180k tokens to the new model. Continue?"
  }
}
```

当多个 PreModelSwitch 挂钩返回不同决策时，优先级为 `deny` > `ask` > `allow`。

Claude Code 向用户显示您的钩子返回的任何 `systemMessage`，无论决定如何，因此成本报告钩子可以返回 `{"systemMessage": "..."}` 并退出 0。

在超时之前不响应的 PreModelSwitch 挂钩会阻止开关。有关超时对其他事件的影响，请参阅[超时](<#超时>)。此事件的默认超时为 30 秒。 `PreModelSwitch` 仅运行 `command`、`http` 和 `mcp_tool` 挂钩，因此 `prompt` 和 `agent` 默认值不适用。

以 0 或 2 以外的代码退出并且不打印 JSON 决策的钩子是非阻塞错误，如 [其他退出代码 ](<#其他退出代码>) 中所述。

### PostModelSwitch

在会话模型更改后运行。使用它来提供特定于 Claude 型号的指导，而无需编辑每个 CLAUDE.md，例如适用于某些型号的组织范围指令。

PostModelSwitch 需要 Claude Code v2.1.251 或更高版本。它无法阻止，因为模型已经改变。 Claude Code 在进行以下任何更改后运行 PostModelSwitch 挂钩：

* 您或客户要求的开关
* [自动模型回退](<https://code.claude.com/docs/en/model-config#automatic-model-fallback>)，它更改会话的模型
* 设置如[`opusplan`](<https://code.claude.com/docs/en/model-config#opusplan-model-setting>)进入或离开计划模式
* Claude Code 恢复会话时恢复模型

当来自 [ 后备模型链 ](<https://code.claude.com/docs/en/model-config#fallback-model-chains>) 的模型运行一回合时，Claude Code 不会运行 PostModelSwitch 挂钩，因为该替换持续一回合并使会话的模型保持不变。

匹配器遵循与 [PreModelSwitch](<#premodelswitch>) 相同的规则：Claude Code 将其与会话切换到的模型的规范名称进行比较。

每当会话的模型更改为任何 Opus 模型时，此示例都会添加指导：

```json theme={null}
{
  "hooks": {
    "PostModelSwitch": [
      {
        "matcher": ".*opus.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'On Opus, delegate implementation work to subagents and keep this conversation for planning and review.'"
          }
        ]
      }
    ]
  }
}
```

要确认挂钩是否有效，请从运行不同模型的会话切换到 Opus 模型，例如从 Sonnet 会话运行 `/model opus`，然后询问 Claude 它对当前模型有什么指导。

#### PostModelSwitch输入

PostModelSwitch 挂钩接收与 [PreModelSwitch](<#premodelswitch输入>) 相同的字段，其中 `hook_event_name` 设置为 `"PostModelSwitch"` 和另外两个 `source` 值：`"auto"` 用于自动回退或自行进行其他更改 Claude Code，以及`"resume"` 用于恢复会话时恢复的模型。

当`source`为`"auto"`时，`requested_model`为`null`。当`source`为`"resume"`时，恢复的是保存的模型设置Claude Code。

#### PostModelSwitch决策控制

Claude Code 在退出 0 时获取钩子的 [ 纯文本标准输出 ](<#退出代码-0>)，或从 JSON 输出获取 `additionalContext`，并在切换后随下一个请求将其传递到 Claude。除了可用于所有钩子的 [JSON 输出字段 ](<#json-输出>) 之外，您还可以返回：

|领域|描述|
| :- | :- |
| `additionalContext` |与下一个请求一起添加到 Claude 上下文的字符串。请参阅 [添加 Claude](<#添加-claude-的上下文>) 的上下文|

如果在发送下一个提示后五秒内挂钩尚未完成，Claude Code 会发送不带输出的请求，并将其附加到下一个请求。如果模型在下一个请求之前多次更改，Claude Code 仅提供最后一次切换的目标模型的输出。

### SessionEnd

当 Claude Code 会话结束时运行。对于清理任务、记录会话
统计信息或保存会话状态很有用。支持匹配器按退出原因进行过滤。

挂钩输入中的 `reason` 字段指示会话结束的原因：

|原因|描述|
| :- | :- |
| `clear` |使用 `/clear` 命令清除会话|
| `resume` |通过交互方式切换会话 `/resume`|
| `logout` |用户已退出|
| `prompt_input_exit` |当提示输入可见时用户退出|
| `other` |其他退出原因|
| `bypass_permissions_disabled` |在v2.1.234中删除； Claude Code 不发送。将其从 `SessionEnd` 匹配器中删除|

#### SessionEnd输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，SessionEnd 挂钩还会收到一个 `reason` 字段，指示会话结束的原因。有关所有值，请参阅上面的 [ 原因表 ](<#sessionend>)。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SessionEnd",
  "reason": "other"
}
```

SessionEnd 钩子没有决策控制。它们无法阻止会话终止，但可以执行清理任务。 Claude Code 丢弃其 [JSON 输出字段 ](<#json-输出>)，例如 `systemMessage`。

SessionEnd 挂钩的默认超时为 1.5 秒。当您退出、运行 `/clear` 或使用交互式 `/resume` 切换会话时，它适用。您可以通过两种方式给钩子更多时间：

* **每个钩子`timeout`**：在该钩子的配置中设置`timeout`。总体预算会自动上升，以匹配设置文件中每个挂钩的最高 `timeout`，最多为 60 秒。如果您以这种方式提高预算，没有自己的 `timeout` 的挂钩仍保留默认值。在插件提供的挂钩上设置的超时不会增加预算。
* **`CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS`**：以毫秒为单位设置此环境变量以显式覆盖预算。您设置的值也会成为每个没有自己的 `timeout` 的钩子的超时。

此示例将预算设置为 5 秒：

```bash theme={null}
CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS=5000 claude
```

在v2.1.268之前，`CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS`只提高了总体预算，没有自己的`timeout`的钩子在1.5秒后仍然被取消。

### 启发

当 MCP 服务器请求用户输入中间任务时运行。默认情况下，Claude Code 显示一个交互式对话框供用户响应。挂钩可以拦截此请求并以编程方式响应，完全跳过对话框。

有关完整的挂钩及其设置条目和脚本，请参阅[从脚本应答表单请求](<#通过脚本回答表单请求>)。

匹配器字段与 MCP 服务器名称匹配。

#### 启发输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，引出挂钩还接收 `mcp_server_name`、`message` 和可选的 `mode`、`url`、`elicitation_id` 和`requested_schema` 字段。

对于形式模式启发，最常见的情况是：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Elicitation",
  "mcp_server_name": "my-mcp-server",
  "message": "Please provide your credentials",
  "mode": "form",
  "requested_schema": {
    "type": "object",
    "properties": {
      "username": { "type": "string", "title": "Username" }
    }
  }
}
```

对于 URL- 模式引发，用于基于浏览器的身份验证：

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Elicitation",
  "mcp_server_name": "my-mcp-server",
  "message": "Please authenticate",
  "mode": "url",
  "url": "https://auth.example.com/login"
}
```

#### 诱导输出

诱导挂钩可以回答用户的请求、拒绝或取消请求，或者将其留给对话框。要接听、拒绝或取消，请退出 0 并打印带有 `action` 的 `hookSpecificOutput` 对象。服务器收到您的答复，并且不会出现对话框。此表的每一行显示一个结果返回的内容以及 MCP 服务器接收的内容：

|至|返回|服务器接收到|
| :- | :- | :- |
|为用户解答|`"action": "accept"`，表单字段值在`content`中|`accept` 与你的 `content`|
|拒绝请求| `"action": "decline"` | `decline` |
|取消请求| `"action": "cancel"` | `cancel` |
|将请求留给用户|无输出，退出代码 0|[对话框](<https://code.claude.com/docs/en/mcp#respond-to-mcp-elicitation-requests>)中用户的回答|

此输出响应 [ 启发输入 ](<#启发输入>) 下显示的表单模式请求。 `content` 中的键是来自该请求的 `requested_schema` 的属性名称：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Elicitation",
    "action": "accept",
    "content": {
      "username": "alice"
    }
  }
}
```

此输出拒绝请求：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Elicitation",
    "action": "decline"
  }
}
```

在对话框中，选择 ** 拒绝 ** 会发送 `decline`，按 `Esc` 会发送 `cancel`，因此返回您希望服务器看到的内容。

对于 URL- 模式请求，返回 `accept` 的挂钩会跳过对话框，因此 URL 永远不会打开。

Claude Code 会丢弃来自启发挂钩的 JSON 输出的 `reason`、`systemMessage` 和 `continue`，无论您返回哪个 `action`。

#### 拒绝诱导的其他方法

你的钩子也会以这些方式下降。服务器收到与 `"action": "decline"` 相同的 `decline`：

* **退出并显示代码2**：Claude Code忽略由同一钩子打印的`hookSpecificOutput`
* **打印顶级`"decision": "block"`**：该块覆盖同一输出中的`action`

当多个挂钩匹配同一请求时，其中一个挂钩的拒绝会覆盖另一个挂钩的 `accept` 或 `cancel`。

此脚本拒绝 URL- 模式请求并将表单请求留给对话框：

```bash theme={null}
#!/bin/bash
if [ "$(jq -r '.mode')" = "url" ]; then
  exit 2
fi
```

用户和服务器都看不到你的钩子拒绝的原因，因为 Claude Code 没有显示你的 stderr 或你的 `reason`。

Claude Code 忽略了 `Elicitation` 中的顶级 `decision` 和 v2.1.105 中的 `ElicitationResult` 挂钩，直到 v2.1.284 中的修复为止。

#### 通过脚本回答表单请求

此示例回答了用户反复出现的一个问题。名为 `issue-tracker` 的 MCP 服务器在表单中请求项目密钥，挂钩填写 `DOCS`。当 `project_key` 是表单的单个字段时，脚本接受。对于任何其他请求，它不会打印任何内容，因此会出现该对话框。



  

#### macOS/Linux

在设置文件中为事件注册一个命令挂钩，并将服务器名称作为匹配器：

    ```json theme={null}
    {
      "hooks": {
        "Elicitation": [
          {
            "matcher": "issue-tracker",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/answer-project-key.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

将此脚本保存到项目中的 `.claude/hooks/answer-project-key.sh` 并使用 `chmod +x` 使其可执行：

    ```bash theme={null}
    #!/bin/bash
    input=$(cat)
    fields=$(jq -c '.requested_schema.properties // {} | keys' <<<"$input")

    if [ "$fields" = '["project_key"]' ]; then
      jq -n '{hookSpecificOutput: {hookEventName: "Elicitation", action: "accept", content: {project_key: "DOCS"}}}'
    fi
    ```
  


  

#### Windows（PowerShell）

注册一个通过PowerShell运行脚本的命令钩子，使用服务器名称作为匹配器：

    ```json theme={null}
    {
      "hooks": {
        "Elicitation": [
          {
            "matcher": "issue-tracker",
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/answer-project-key.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

将此脚本保存到项目中的 `.claude/hooks/answer-project-key.ps1`：

    ```powershell theme={null}
    $request = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $fields = @($request.requested_schema.properties.PSObject.Properties.Name)

    if ($fields.Count -eq 1 -and $fields[0] -eq 'project_key') {
      @{
        hookSpecificOutput = @{
          hookEventName = "Elicitation"
          action = "accept"
          content = @{ project_key = "DOCS" }
        }
      } | ConvertTo-Json -Depth 3
    }
    ```
  


要确认挂钩是否有效，请使用 `claude --debug` 启动 Claude Code，并为 Claude 提供一个任务，使服务器要求提供项目密钥。没有出现对话框，并且 [ 调试日志 ](<#调试钩子>) 有一行以 `Elicitation resolved by hook: {"action":"accept","content":{"project_key":"DOCS"}}` 结尾。

### ElicitationResult

在用户响应 MCP 引发后运行。挂钩可以在响应发送回 MCP 服务器之前观察、修改或阻止响应。

当 [Elicitation](<#启发>) 挂钩应答请求时，Claude Code 会将该答案发送到服务器，而不运行 ElicitationResult 挂钩。

匹配器字段与 MCP 服务器名称匹配。

#### ElicitationResult输入

除了 [ 公共输入字段 ](<#常用输入字段>) 之外，ElicitationResult 挂钩还接收 `mcp_server_name`、`action` 和可选的 `mode`、`elicitation_id` 和 `content` 字段。

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "ElicitationResult",
  "mcp_server_name": "my-mcp-server",
  "action": "accept",
  "content": { "username": "alice" },
  "mode": "form"
}
```

#### ElicitationResult 输出

ElicitationResult 挂钩可以让用户的响应通过、更改其值或阻止它。要更改或阻止响应，请退出 0 并打印带有 `action` 的 `hookSpecificOutput` 对象。此表的每一行显示一个结果返回的内容以及 MCP 服务器接收的内容：

|至|返回|服务器接收到|
| :- | :- | :- |
|让回应通过|无输出，退出代码 0|用户的响应，不变|
|更改提交的值|`"action": "accept"`，使用 `content` 中的新值|`accept` 用您的 `content` 代替用户的值|
|阻止响应| `"action": "decline"` |`decline`，没有用户的值|
|取消请求| `"action": "cancel"` |`cancel`，以及用户提交的值。要扣留它们，请返回 `"decline"`|

此输出更改了 [ElicitationResult 输入 ](<#elicitationresult输入>) 下显示的响应，因此服务器收到用户提交 `alice` 的 `alice@example.com`：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "ElicitationResult",
    "action": "accept",
    "content": {
      "username": "alice@example.com"
    }
  }
}
```

您的 `content` 替换用户的整个 `content` 对象，因此请包含您不更改的字段。一起返回 `action`，因为 Claude Code 会忽略没有 `action` 的 `hookSpecificOutput`。

当用户拒绝或取消时，ElicitationResult 挂钩也会运行，并且您的 `action` 会替换他们的。在返回 `accept` 之前检查输入的 `action` 是否为 `accept`，否则您的钩子会将拒绝的请求转变为接受的请求。当用户接受时，此脚本会进行相同的更改，保留其他字段，并且不打印任何内容：

```bash theme={null}
#!/bin/bash
input=$(cat)

if [ "$(jq -r '.action' <<<"$input")" = "accept" ]; then
  jq '{hookSpecificOutput: {hookEventName: "ElicitationResult", action: "accept", content: (.content + {username: (.content.username + "@example.com")})}}' <<<"$input"
fi
```

此输出阻止响应：

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "ElicitationResult",
    "action": "decline"
  }
}
```

退出代码 2 和顶级 `"decision": "block"` 也会阻止响应。 [拒绝诱导的其他方法](<#拒绝诱导的其他方法>)涵盖了当钩子组合它们时哪种方法生效、用户看到什么以及哪些版本忽略`decision`。

Claude Code 会丢弃 ElicitationResult 挂钩的 JSON 输出中的 `reason`、`systemMessage` 和 `continue`，无论您返回哪个 `action`。

## 基于提示的钩子

除了命令、HTTP 和 MCP 工具挂钩之外，Claude Code 支持使用 LLM 来评估是否允许或阻止操作的基于提示的挂钩 (`type: "prompt"`) 和 agent 挂钩（`type: "agent"`）生成具有工具访问权限的代理验证器。并非所有事件都支持每种挂钩类型。

支持所有五种挂钩类型（`command`、`http`、`mcp_tool`、`prompt` 和 `agent`）的事件：

* `PermissionDenied`
* `PostToolBatch`
* `PostToolUse`
* `PostToolUseFailure`
* `PreToolUse`
* `Stop`
* `SubagentStop`
* `TaskCompleted`
* `TaskCreated`
* `TeammateIdle`
* `UserPromptExpansion`
* `UserPromptSubmit`

`PermissionRequest` 支持 `command`、`http`、`mcp_tool` 和 `prompt` 挂钩，但不支持 `agent` 挂钩。如果您为此事件配置 agent 挂钩，Claude Code 会跳过它，并且权限流程将保持不变。要允许或拒绝挂钩，请从命令或 HTTP 挂钩返回 [ 决策对象 ](<#permissionrequest决策控制>)。

支持 `command`、`http` 和 `mcp_tool` 挂钩但不支持 `prompt` 或 `agent` 的事件：

* `ConfigChange`
* `CwdChanged`
* `DirectoryAdded`
* `Elicitation`
* `ElicitationResult`
* `FileChanged`
* `InstructionsLoaded`
* `MessageDisplay`
* `Notification`
* `PostCompact`
* `PostModelSwitch`
* `PreCompact`
* `PreModelSwitch`
* `SessionEnd`
* `StopFailure`
* `SubagentStart`
* `WorktreeCreate`
* `WorktreeRemove`

`SessionStart` 和 `Setup` 支持 `command` 和 `mcp_tool` 挂钩，[MCP 工具挂钩字段 ](<#mcp-工具挂钩字段>) 描述其 `mcp_tool` 挂钩何时运行。它们不支持 `http`、`prompt` 或 `agent` 挂钩。

### 基于提示的挂钩如何工作

基于提示的挂钩不执行 Bash 命令：

1. 将挂钩输入和提示发送到 Claude 模型，默认情况下，Claude Code 用于 [ 后台功能 ](<https://code.claude.com/docs/en/costs#background-token-usage>)
2. LLM 以包含决策的结构化 JSON 进行响应
3. Claude Code 自动处理决策

### 提示钩子配置

将 `type` 设置为 `"prompt"` 并提供 `prompt` 字符串而不是 `command`。使用 `$ARGUMENTS` 占位符将挂钩的 JSON 输入数据注入到提示文本中。

在提示或 [agent 挂钩 ](<#基于agent的挂钩>) 中，您可以将 `prompt` 编写为有关阻止或允许的规则，例如“阻止读取 `.env` 文件的任何 Bash 命令”，或作为必须满足的条件，例如“所有单元测试都通过”。

此 `Stop` 挂钩要求 LLM 在允许 Claude 完成之前评估所有任务是否已完成：

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate if Claude should stop: $ARGUMENTS. Check if all tasks are complete."
          }
        ]
      }
    ]
  }
}
```

|领域|必填|描述|
| :- | :- | :- |
| `type` |是的|必须是 `"prompt"`|
| `prompt` |是的|发送到LLM的提示文本。使用 `$ARGUMENTS` 作为挂钩输入 JSON 的占位符。如果 `$ARGUMENTS` 不存在，则输入 JSON 附加到提示中|
| `model` |不|用于评估的模型。默认为模型 Claude Code 用于 [ 后台功能 ](<https://code.claude.com/docs/en/costs#background-token-usage>)|
| `timeout` |不|超时（以秒为单位）。默认值：30|
| `continueOnBlock` |不|在其适用的事件中，`true` 将 `ok: false` 原因反馈给 Claude 并继续而不是结束回合。默认值：`false`。有关每个事件的行为，请参阅 [响应架构 ](<#响应模式>)|

### 响应模式

LLM 必须响应 JSON，其中包含：

```json theme={null}
{
  "ok": true | false,
  "reason": "Explanation for the decision",
  "impossible": true | false
}
```

|领域|描述|
| :- | :- |
| `ok` |`true`允许。对于 `false`，请参阅下面的每个事件的行为|
| `reason` |当`ok`为`false`时需要|
| `impossible` |可选。当模型判断条件永远无法满足时，返回`ok: false`。在`Stop`和`SubagentStop`上，Claude Code然后让回合结束而不是反馈原因。 Agent 钩子和其他事件忽略它|

`ok: false` 上发生的情况取决于事件：

* `Stop`和`SubagentStop`：原因被反馈到Claude作为其下一条指令并且转弯继续，除非响应也设置`impossible: true`，在这种情况下Claude Code允许停止并且转弯结束
* `PreToolUse`：工具调用被拒绝；默认情况下，回合结束，拒绝原因作为警告线显示在聊天中。设置 `continueOnBlock: true` 以将原因作为工具错误返回到 Claude，以便它可以调整并继续，相当于命令挂钩的 `permissionDecision: "deny"`。在v2.1.210之前，拒绝原因被返回到Claude，因为刀具错误并且继续转弯
* `PostToolUse`：默认情况下回合结束，原因作为警告线显示在聊天中。设置`continueOnBlock: true`将原因反馈给Claude并继续转弯
* `PostToolBatch`、`UserPromptSubmit`和`UserPromptExpansion`：回合结束，原因显示为警告线。无论 `continue` 为何，这些事件都会结束 `decision: "block"` 的开启
* `PostToolUseFailure` 和 `TaskCreated`：原因作为刀具错误返回到 Claude，并且继续转动，无论 `continueOnBlock` 为何
* `TaskCompleted`：当它因回合期间任务被标记为完成而触发时，原因将作为工具错误返回到 Claude，并且回合继续，无论 `continueOnBlock` 如何。当它因为队友停止而触发时，它的行为类似于 `TeammateIdle` 并默认停止队友
* `TeammateIdle`：默认情况下队友停止，原因显示为警告线。设置`continueOnBlock: true`将原因反馈给队友并继续工作
* `PermissionRequest`：`ok: false`没有效果。要拒绝钩子的批准，请使用 [ 命令钩子 ](<#命令挂钩字段>) 返回 `hookSpecificOutput.decision.behavior: "deny"`
* `PermissionDenied`：`ok: false`没有任何效果，因为拒绝已经发生。此事件读取的唯一输出是 `hookSpecificOutput.retry`，提示符和 agent 挂钩无法设置。它们在此事件上运行，但它们的输出被丢弃。使用 [ 命令挂钩 ](<#命令挂钩字段>) 返回 `retry`

如果您需要对任何事件进行更精细的控制，请使用 [ 命令挂钩 ](<#命令挂钩字段>) 以及 [ 决策控制 ](<#决策控制>) 中描述的每个事件字段。

### 停止前检查多个条件

此 `Stop` 挂钩使用详细的提示来检查三个条件，然后才允许 Claude 停止。 `SubagentStop` 挂钩使用相同的格式来评估 [ 子代理 ](<https://code.claude.com/docs/en/sub-agents>) 是否应停止。如果模型由于尚未满足条件而返回 `"ok": false`，则 Claude 会继续使用提供的原因作为其下一条指令：

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "You are evaluating whether Claude should stop working. Context: $ARGUMENTS\n\nAnalyze the conversation and determine if:\n1. All user-requested tasks are complete\n2. Any errors need to be addressed\n3. Follow-up work is needed\n\nRespond with JSON: {\"ok\": true} to allow stopping, or {\"ok\": false, \"reason\": \"your explanation\"} to continue working.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

## 基于Agent的挂钩



**注意：**

Agent 钩子是实验性的。未来版本中的行为和配置可能会发生变化。对于生产工作流程，首选 [ 命令挂钩 ](<#命令挂钩字段>)。


基于 Agent 的挂钩 (`type: "agent"`) 类似于基于提示的挂钩，但具有多轮工具访问。 agent 挂钩生成一个子代理，该子代理可以读取文件、搜索代码并检查代码库以验证条件，而不是单个 LLM 调用。 Agent 挂钩支持与 [ 基于提示的挂钩 ](<#基于提示的钩子>) 相同的事件，但 `PermissionRequest` 除外。

### agent 挂钩如何工作

当 agent 钩子触发时：

1. Claude Code 使用您的提示和挂钩的 JSON 输入生成一个子代理
2. 子代理可以使用 Read、Grep 和 Glob 等工具进行调查
3. 在达到 50 回合后，子代理返回结构化的 `{ "ok": true/false }` 决策
4. 如果 `ok` 是 `true`，则 Claude Code 允许该操作。如果 `ok` 为 `false`，则 Claude Code 处理该块的方式与该事件上带有 `continueOnBlock: true` 的提示挂钩相同，如 [响应架构 ](<#响应模式>) 下所列

当验证需要检查实际文件或测试输出，而不仅仅是单独评估挂钩输入数据时，Agent 挂钩非常有用。

### Agent 挂钩配置

将 `type` 设置为 `"agent"` 并提供 `prompt` 字符串，使用 `$ARGUMENTS` 作为挂钩输入 JSON 的占位符。配置字段与 [ 提示钩子 ](<#提示钩子配置>) 相同，除了 agent 钩子具有更长的默认超时 60 秒并且没有 `continueOnBlock` 字段。

响应模式是 `{ "ok": true }` 允许或 `{ "ok": false, "reason": "..." }` 阻止。开 `ok: false`, Claude Code 处理一个 agent hook 处理 a 的方式 [提示钩子 `continueOnBlock: true`](<#响应模式>) 在同一事件中； agent 钩子没有 `continueOnBlock` 字段，并且不支持提示挂钩 `impossible` 场。

此 `Stop` 挂钩会在允许 Claude 完成之前验证所有单元测试是否通过：

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "agent",
            "prompt": "Verify that all unit tests pass. Run the test suite and check the results. $ARGUMENTS",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

## 在后台运行钩子

默认情况下，钩子会阻止 Claude 的执行，直到完成。对于部署、测试套件或外部 API 调用等长时间运行的任务，请将 `"async": true` 设置为在后台运行挂钩，同时 Claude 继续工作。异步挂钩无法阻止或控制 Claude 的行为：`decision`、`permissionDecision` 和 `continue` 等响应字段无效，因为它们要控制的操作已经完成。

### 配置异步钩子

将 `"async": true` 添加到命令挂钩的配置中，以在后台运行它，而不会阻止 Claude。此字段仅在 `type: "command"` 挂钩上可用。

此挂钩在每次 `Write` 工具调用后运行一个测试脚本。 Claude 在 `run-tests.sh` 执行时立即继续工作。脚本完成后，其输出将在下一个对话回合中传递：

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/run-tests.sh",
            "async": true
          }
        ]
      }
    ]
  }
}
```

一旦异步挂钩在后台运行，Claude Code 就不会对其强制执行 `timeout`。 Claude Code 仍然在使用 `asyncRewake` 运行的挂钩上强制执行 `timeout`。

Claude Code 仅在会话运行时传递异步挂钩的结果：

* 在具有 `-p` 标志的 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 中，Claude Code 会杀死在拆卸时仍在运行的任何异步钩子，并以结果 `cancelled` 完成它
* 如果您的钩子工作必须比 `claude -p` 会话持续时间更长，请从中启动一个完全分离的进程

### 异步钩子如何执行

当异步钩子触发时，Claude Code 启动钩子进程并立即继续，而不等待其完成。该挂钩通过 stdin 接收与同步挂钩相同的 JSON 输入。

后台进程退出后，Claude Code 将钩子的 JSON 响应中的 `additionalContext` 和 `systemMessage` 字段传递到下一个会话回合的 Claude。与同步挂钩的 `systemMessage` 不同，这两个字段都不会显示给您。

Claude Code 验证 JSON 针对相同的 [ 输出架构 ](<#json-输出>) 作为同步挂钩的响应，并删除值类型错误的任何字段，例如不是字符串的 `systemMessage`，而不是传递它。使用 `--debug` 运行可以看到命名每个删除字段的警告。在 v2.1.202 之前，异步挂钩的格式错误的 JSON 输出可能会使会话崩溃，并且每次恢复会话时都会再次发生崩溃。

默认情况下会抑制异步挂钩完成通知。要查看它们，请使用 `Ctrl+O` 启用详细模式或使用 `--verbose` 启动 Claude Code。

### 文件更改后运行测试

每当 Claude 写入文件时，此挂钩都会在后台启动测试套件，然后在测试完成时将结果报告回 Claude。将此脚本保存到项目中的 `.claude/hooks/run-tests-async.sh` 并使用 `chmod +x` 使其可执行：

```bash theme={null}
#!/bin/bash
# run-tests-async.sh

# Read hook input from stdin
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

# Only run tests for source files
if [[ "$FILE_PATH" != *.ts && "$FILE_PATH" != *.js ]]; then
  exit 0
fi

# Run tests and report results to Claude via additionalContext
RESULT=$(npm test 2>&1)
EXIT_CODE=$?

if [ $EXIT_CODE -eq 0 ]; then
  MSG="Tests passed after editing $FILE_PATH"
else
  MSG="Tests failed after editing $FILE_PATH: $RESULT"
fi
jq -nc --arg msg "$MSG" '{hookSpecificOutput: {hookEventName: "PostToolUse", additionalContext: $msg}}'
```

然后将此配置添加到项目根目录中的 `.claude/settings.json` 中。 `async: true` 标志让 Claude 在测试运行时继续工作：

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/run-tests-async.sh",
            "args": [],
            "async": true
          }
        ]
      }
    ]
  }
}
```

### 局限性

与同步钩子相比，异步钩子有额外的限制：

* 挂钩输出在下一个对话轮次传递。如果会话空闲，响应将等到下一次用户交互。例外：即使会话空闲，以代码 2 退出的 `asyncRewake` 挂钩也会立即唤醒 Claude。
* 每次执行都会创建一个单独的后台进程。

## 安全考虑

### 免责声明



**注意：**

命令挂钩使用您的完整用户权限执行 shell 命令。他们可以修改、删除或访问您的用户帐户可以访问的任何文件。在将所有挂钩命令添加到您的配置之前，请检查并测试它们。


### 工作空间信任

Claude Code 在运行设置文件中的任何挂钩之前检查工作区信任。什么算作可信取决于会话类型：

* **交互式会话**：Claude Code保留来自每个设置文件的挂钩，包括您自己的`~/.claude/settings.json`，直到您接受文件夹或信任扩展到该文件夹的父目录的[工作空间信任对话框](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>)
* **`-p` 或 SDK 会话 **：Claude Code 从不显示对话框并将该文件夹视为受信任，因此在存储库的 `.claude/settings.json` 中提交的挂钩在您从未信任的文件夹中运行

在您未编写的存储库上编写 `claude -p` 脚本之前，请检查其 `.claude/` 设置文件，从 [`--bare`](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>) 开始，或者 [ 使用 `--settings '{"disableAllHooks": true}'` 关闭该运行 ](<#禁用或删除挂钩>) 的挂钩。项目子代理中的 Frontmatter 挂钩遵循比设置文件挂钩更严格的规则。 [在信任文件夹之前运行的内容](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>)按会话类型列出了每种存储库内容。

### 安全最佳实践

编写钩子时请记住这些做法：

* **验证和清理输入**：永远不要盲目信任输入数据
* **始终引用 shell 变量**：使用 `"$VAR"` 而不是 `$VAR`
* **阻止路径遍历**：检查文件路径中是否有`..`
* **使用绝对路径**：指定脚本的完整路径。在 exec 形式中，使用 `${CLAUDE_PROJECT_DIR}` 并且路径不需要引号。 shell形式下，用双引号括起来
* **跳过敏感文件**：避免`.env`、`.git/`、密钥等。

## Windows PowerShell 工具

在 Windows 上，您可以通过在命令挂钩上设置 `"shell": "powershell"` 来运行 PowerShell 中的各个挂钩。 Claude Code 自动检测 `pwsh.exe`、PowerShell 7 和更高版本的可执行文件，并回退到 Windows PowerShell 5.1 的 `powershell.exe`。

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "shell": "powershell",
            "command": "Write-Host 'File written'"
          }
        ]
      }
    ]
  }
}
```

要从 PowerShell shell 形式命令引用项目根目录，请写入 `${CLAUDE_PROJECT_DIR}` 或 `$env:CLAUDE_PROJECT_DIR`。 Claude Code 将 PowerShell shell 形式命令中的 `${CLAUDE_PROJECT_DIR}`、`${CLAUDE_PLUGIN_ROOT}` 和 `${CLAUDE_PLUGIN_DATA}` 占位符重写为 PowerShell 的 `${env:NAME}` 形式，无论钩子是否定义在`settings.json`，一个插件，或者一个技能。 PowerShell 然后在解析后解析导出环境中的值，因此占位符在双引号字符串内起作用，但在单引号字符串内不起作用，其中 PowerShell 从不扩展变量。

不要在 PowerShell 挂钩中写入裸露的 `$CLAUDE_PROJECT_DIR` 拼写。 PowerShell 将其解析为未定义的局部变量并将其解析为 `$null`，这使得脚本路径没有项目根前缀。 Claude Code 不会重写该形式；它会在 [ 调试日志 ](<#调试钩子>) 中记录一条警告。

下面的示例显示了一个 `settings.json` 挂钩，它运行具有 `$env:` 形式的项目脚本：

```json theme={null}
{
  "type": "command",
  "shell": "powershell",
  "command": "& \"$env:CLAUDE_PROJECT_DIR\\.claude\\hooks\\check.ps1\""
}
```

## 调试钩子

挂钩执行详细信息将写入调试日志文件。使用 `claude --debug-file <path>` 启动 Claude Code 将日志写入已知位置，或运行 `claude --debug` 并读取 `~/.claude/debug/<session-id>.txt` 处的日志。 `--debug` 标志不会打印到终端。

例如，`Write` 上的 `PostToolUse` 挂钩其命令打印 `hook-ran` 会生成如下条目：

```text theme={null}
2026-07-19T02:03:24.382Z [DEBUG] Hook output does not start with {, treating as plain text
2026-07-19T02:03:24.382Z [DEBUG] "Hook PostToolUse:Write (PostToolUse) success:\nhook-ran"
```

有关更精细的挂钩匹配详细信息，请设置 `CLAUDE_CODE_DEBUG_LOG_LEVEL=verbose` 以查看其他日志行，例如挂钩匹配器计数和查询匹配。

要对常见问题进行故障排除，例如挂钩未触发、停止持续阻塞的挂钩或配置错误，请参阅指南中的[限制和故障排除](<https://code.claude.com/docs/en/hooks-guide#limitations-and-troubleshooting>)。有关涵盖 `/context`、`/doctor` 和设置优先级的更广泛的诊断演练，请参阅[调试配置](<https://code.claude.com/docs/en/debug-your-config>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Hooks reference

> Reference for Claude Code hook events, configuration schema, JSON input/output formats, exit codes, async hooks, HTTP hooks, prompt hooks, and MCP tool hooks.



**提示：**

  For a quickstart guide with examples, see [Automate actions with hooks](<https://code.claude.com/docs/en/hooks-guide>).


Hooks are user-defined shell commands, HTTP endpoints, MCP tool calls, LLM prompts, or subagents that execute automatically at specific points in Claude Code's lifecycle. Claude Code fires the same hook events wherever it runs: sessions in the terminal, IDE extensions, the [Desktop app](<https://code.claude.com/docs/en/desktop-quickstart>), and [cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>). Use this reference to look up event schemas, configuration options, JSON input/output formats, and advanced features like async hooks, HTTP hooks, and MCP tool hooks.

A plugin can also register hooks as JavaScript functions that Claude Code calls in its own process, which can draw in the interface as well as act on events. A plugin that does is a [mod](<https://code.claude.com/docs/en/plugins/mods/overview>), and those function hooks are covered in [React to events](<https://code.claude.com/docs/en/plugins/mods/events>) rather than here. The hooks on this page keep working alongside mods.

## Hook lifecycle

Claude Code runs hooks at specific points during a session. When an event fires and a matcher matches, Claude Code passes JSON context about the event to your hook handler. For command hooks, input arrives on stdin. For HTTP hooks, it arrives as the POST request body. Your handler can then inspect the input, take action, and optionally return a decision.

Events fall into three cadences:

* per session: `SessionStart` and `SessionEnd`
* per turn: `UserPromptSubmit`, `Stop`, and `StopFailure`
* on every tool call inside the agentic loop: `PreToolUse` and `PostToolUse`, except [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) calls, which skip both

<div style={{maxWidth: "500px", margin: "0 auto"}}>
  

    

![Hook lifecycle diagram showing optional Setup feeding into SessionStart, then a per-turn loop containing UserPromptSubmit, UserPromptExpansion for slash commands, the nested agentic loop (PreToolUse, PermissionRequest, PostToolUse, PostToolUseFailure, PostToolBatch, SubagentStart/Stop, TaskCreated, TaskCompleted), and Stop or StopFailure, followed by TeammateIdle, PreCompact, PostCompact, and SessionEnd, with Elicitation and ElicitationResult nested inside MCP tool execution, PermissionDenied as a side branch from PermissionRequest for auto-mode denials, WorktreeCreate, WorktreeRemove, Notification, ConfigChange, InstructionsLoaded, CwdChanged, FileChanged, and DirectoryAdded as standalone async events, PreModelSwitch as a standalone sequential event that runs before a requested model switch, PostModelSwitch as a standalone async event that runs after the session's model changes, and MessageDisplay as a display-only event that runs while assistant message text streams](<https://mintcdn.com/claude-code/x7pO8l4XcvAXCoVc/images/hooks-lifecycle.svg?fit=max&auto=format&n=x7pO8l4XcvAXCoVc&q=85&s=81b9256c1bbe8832553485f5d9e9c746>)


    

![Hook lifecycle diagram showing optional Setup feeding into SessionStart, then a per-turn loop containing UserPromptSubmit, UserPromptExpansion for slash commands, the nested agentic loop (PreToolUse, PermissionRequest, PostToolUse, PostToolUseFailure, PostToolBatch, SubagentStart/Stop, TaskCreated, TaskCompleted), and Stop or StopFailure, followed by TeammateIdle, PreCompact, PostCompact, and SessionEnd, with Elicitation and ElicitationResult nested inside MCP tool execution, PermissionDenied as a side branch from PermissionRequest for auto-mode denials, WorktreeCreate, WorktreeRemove, Notification, ConfigChange, InstructionsLoaded, CwdChanged, FileChanged, and DirectoryAdded as standalone async events, PreModelSwitch as a standalone sequential event that runs before a requested model switch, PostModelSwitch as a standalone async event that runs after the session's model changes, and MessageDisplay as a display-only event that runs while assistant message text streams](<https://mintcdn.com/claude-code/x7pO8l4XcvAXCoVc/images/hooks-lifecycle-dark.svg?fit=max&auto=format&n=x7pO8l4XcvAXCoVc&q=85&s=c9b3d88487335f58cce0b52e2f9e7531>)

  

</div>

The table below summarizes when each event fires. The [Hook events](<#hook-events>) section documents the full input schema and decision control options for each one.

| Event | When it fires |
| :- | :- |
| `SessionStart` | When a session begins or resumes |
| `Setup` | When you start Claude Code with `--init-only`, or with `--init` or `--maintenance` in `-p` mode. For one-time preparation in CI or scripts |
| `UserPromptSubmit` | When a prompt is submitted, before Claude processes it. Also fires on [turns Claude Code starts on its own](<https://code.claude.com/docs/en/hooks#userpromptsubmit>) |
| `UserPromptExpansion` | When a user-typed command expands into a prompt, before it reaches Claude. Can block the expansion |
| `PreToolUse` | Before a tool call executes. Can block it |
| `PermissionRequest` | When a tool call needs a permission decision |
| `PermissionDenied` | When auto mode denies a tool call, including denials without a classifier verdict. Use JSON `hookSpecificOutput.retry: true` to tell the model it may retry the denied tool call. Claude Code ignores `retry` when the classifier produced no verdict |
| `PostToolUse` | After a tool call succeeds |
| `PostToolUseFailure` | After a tool call fails |
| `PostToolBatch` | After a full batch of parallel tool calls resolves, before the next model call |
| `Notification` | When Claude Code sends a notification |
| `MessageDisplay` | While assistant message text is displayed |
| `SubagentStart` | When a subagent is spawned |
| `SubagentStop` | When a subagent finishes |
| `TaskCreated` | When a task is being created via `TaskCreate` |
| `TaskCompleted` | When a task is being marked as completed |
| `Stop` | When Claude finishes responding |
| `StopFailure` | When the turn ends due to an API error |
| `TeammateIdle` | When an [agent team](<https://code.claude.com/docs/en/agent-teams>) teammate is about to go idle |
| `InstructionsLoaded` | When a CLAUDE.md or `.claude/rules/*.md` file is loaded into context. Fires at session start and when files are lazily loaded during a session |
| `ConfigChange` | When a configuration file changes during a session |
| `CwdChanged` | When the working directory changes, for example when Claude executes a `cd` command. Useful for reactive environment management with tools like direnv |
| `DirectoryAdded` | When a working directory is added mid-session via `/add-dir` or the SDK `register_repo_root` control request |
| `FileChanged` | When a watched file changes on disk. The `matcher` field specifies which filenames to watch |
| `WorktreeCreate` | When a worktree is being created via `--worktree`, `isolation: "worktree"`, or for a background session. Replaces default git behavior |
| `WorktreeRemove` | When a worktree that a `WorktreeCreate` hook created is being removed |
| `PreCompact` | Before context compaction |
| `PostCompact` | After context compaction completes |
| `PreModelSwitch` | Before Claude Code applies a model switch that you or a client requested. Can block the switch |
| `PostModelSwitch` | After the session's model changes, including changes Claude Code makes on its own, such as restoring the model when you resume a session |
| `Elicitation` | When an MCP server requests user input during a tool call |
| `ElicitationResult` | After a user responds to an MCP elicitation, before the response is sent back to the server |
| `SessionEnd` | When a session terminates |

### How a hook resolves

To see how the event, the matcher, and the handler fit together, consider this `PreToolUse` hook that blocks destructive shell commands.



  

#### macOS/Linux

    The `matcher` narrows to Bash tool calls and the `if` condition narrows further to Bash subcommands matching `rm *`, so `block-rm.sh` only spawns when both filters match:

    ```json theme={null}
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Bash",
            "hooks": [
              {
                "type": "command",
                "if": "Bash(rm *)",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

    The script reads the JSON input from stdin, extracts the command, and returns a `permissionDecision` of `"deny"` if it contains `rm -rf`. Save it to `.claude/hooks/block-rm.sh` in your project and make it executable with `chmod +x .claude/hooks/block-rm.sh` so Claude Code can run it:

    ```bash theme={null}
    #!/bin/bash
    # .claude/hooks/block-rm.sh
    COMMAND=$(jq -r '.tool_input.command')

    if echo "$COMMAND" | grep -q 'rm -rf'; then
      jq -n '{
        hookSpecificOutput: {
          hookEventName: "PreToolUse",
          permissionDecision: "deny",
          permissionDecisionReason: "Destructive command blocked by hook"
        }
      }'
    else
      exit 0  # no decision; normal permission flow applies
    fi
    ```

    This script, like the other Bash examples on this page that parse JSON input, uses `jq`, so install `jq` and make sure it is on your `PATH` before trying them.
  


  

#### Windows (PowerShell)

    The matcher `Bash|PowerShell` covers the [PowerShell tool](<#powershell>) as well as Bash. A single `if` rule matches only one tool's calls, so each tool gets its own handler: the first narrows to Bash subcommands matching `rm *`, the second to PowerShell commands matching `Remove-Item *`. Both run the same script through `powershell.exe`:

    ```json theme={null}
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Bash|PowerShell",
            "hooks": [
              {
                "type": "command",
                "if": "Bash(rm *)",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.ps1"
                ]
              },
              {
                "type": "command",
                "if": "PowerShell(Remove-Item *)",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    The `-NoProfile` flag skips loading your PowerShell profile so the hook starts fast, and `-ExecutionPolicy Bypass` lets PowerShell run the local script file.

    The script reads the JSON input from stdin, extracts the command, and returns a `permissionDecision` of `"deny"` if it contains `rm -rf` or `Remove-Item` followed by `-Recurse`. Save it to `.claude/hooks/block-rm.ps1` in your project:

    ```powershell theme={null}
    # .claude/hooks/block-rm.ps1
    $callInput = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $command = $callInput.tool_input.command

    if ($command -match 'rm -rf|Remove-Item.*-Recurse') {
      @{
        hookSpecificOutput = @{
          hookEventName = "PreToolUse"
          permissionDecision = "deny"
          permissionDecisionReason = "Destructive command blocked by hook"
        }
      } | ConvertTo-Json
    } else {
      exit 0  # no decision; normal permission flow applies
    }
    ```
  


Now suppose Claude Code decides to run `Bash "rm -rf /tmp/build"` against the macOS/Linux config. Here's what happens:



  

![Diagram of hook resolution: PreToolUse fires, the matcher checks for a Bash match, then the if condition checks for a Bash(rm *) match. If both match, the hook command runs and returns permissionDecision deny, so the tool call is blocked and Claude Code continues. If either check fails to match, the hook is skipped and the tool call is allowed to proceed.](<https://mintcdn.com/claude-code/ikqp3_70mqIahteV/images/hook-resolution.svg?fit=max&auto=format&n=ikqp3_70mqIahteV&q=85&s=be0bf3053550c26de5f54cd64674c197>)


  

![Diagram of hook resolution: PreToolUse fires, the matcher checks for a Bash match, then the if condition checks for a Bash(rm *) match. If both match, the hook command runs and returns permissionDecision deny, so the tool call is blocked and Claude Code continues. If either check fails to match, the hook is skipped and the tool call is allowed to proceed.](<https://mintcdn.com/claude-code/_xqph1dUOslCOwsj/images/hook-resolution-dark.svg?fit=max&auto=format&n=_xqph1dUOslCOwsj&q=85&s=e80af91f8507cee6bd51ac3c2dd92f63>)




  

#### Event fires

    The `PreToolUse` event fires. Claude Code sends the tool input as JSON on stdin to the hook:

    ```json theme={null}
    { "tool_name": "Bash", "tool_input": { "command": "rm -rf /tmp/build" }, ... }
    ```
  


  

#### Matcher checks

    The matcher `"Bash"` matches the tool name, so this hook group activates. If you omit the matcher or use `"*"`, the group activates on every occurrence of the event.
  


  

#### If condition checks

    The `if` condition `"Bash(rm *)"` matches because `rm -rf /tmp/build` is a subcommand matching `rm *`, so this handler spawns. If the command had been `npm test`, the `if` check would fail and `block-rm.sh` would never run, avoiding the process spawn overhead. The `if` field is optional; without it, every handler in the matched group runs.
  


  

#### Hook handler runs

    The script inspects the full command and finds `rm -rf`, so it prints a decision to stdout:

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "deny",
        "permissionDecisionReason": "Destructive command blocked by hook"
      }
    }
    ```

    If the command had been a safer `rm` variant like `rm file.txt`, the script would hit `exit 0` instead. Exit code 0 with no output means the hook has no decision to report, so the tool call continues through the normal [permission flow](<https://code.claude.com/docs/en/permissions>). The hook can deny the call, but staying silent doesn't approve it.
  


  

#### Claude Code acts on the result

    Claude Code reads the JSON decision, blocks the tool call, and shows Claude the reason.
  


The [Configuration](<#configuration>) section below documents the full schema, and each [hook event](<#hook-events>) section documents what input your command receives and what output it can return.

## Configuration

Hooks are defined in JSON settings files. The configuration has three levels of nesting:

1. Choose a [hook event](<#hook-events>) to respond to, like `PreToolUse` or `Stop`
2. Add a [matcher group](<#matcher-patterns>) to filter when it fires, like "only for the Bash tool"
3. Define one or more [hook handlers](<#hook-handler-fields>) to run when matched

See [How a hook resolves](<#how-a-hook-resolves>) above for a complete walkthrough with an annotated example.



**注释：**

  This page uses specific terms for each level: **hook event** for the lifecycle point, **matcher group** for the filter, and **hook handler** for the shell command, HTTP endpoint, MCP tool, prompt, or agent that runs. "Hook" on its own refers to the general feature.


### Hook locations

Where you define a hook determines its scope:

| Location | Scope | Shareable |
| :- | :- | :- |
| `~/.claude/settings.json` | All your projects | No, local to your machine |
| `.claude/settings.json` | Single project | Yes, can be committed to the repo |
| `.claude/settings.local.json` | Single project | No, gitignored when Claude Code saves a setting to it |
| Managed policy settings | Organization-wide | Yes, admin-controlled |
| [Plugin](<https://code.claude.com/docs/en/plugins/overview>) `hooks/hooks.json` | When plugin is enabled | Yes, bundled with the plugin |
| [Skill](<https://code.claude.com/docs/en/skills>) frontmatter | The rest of the session once the skill is invoked. See [Hooks in skills and agents](<#hooks-in-skills-and-agents>) | Yes, defined in the skill file |
| [Subagent](<https://code.claude.com/docs/en/sub-agents>) frontmatter | While that subagent is running | Yes, defined in the subagent file |

[Cloud sessions](<https://code.claude.com/docs/en/claude-code-on-the-web>) don't read your local `~/.claude/settings.json`. In a [self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments-configuration#permissions-and-tool-approval>), Claude Code also runs the hooks the operator seeded from the runner host's `~/.claude/`, and it runs the hooks in the runner image's managed settings file when that file is among the [managed sources Claude Code applies](<https://code.claude.com/docs/en/managed-settings#how-claude-code-combines-managed-sources>), which by default means only when neither server-managed settings nor an MDM-delivered Claude Code policy supplies the managed tier. See [what carries over from your setup](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>) for which settings files and plugins, and so which hooks, reach a cloud session.

For details on settings file resolution, see [settings](<https://code.claude.com/docs/en/settings>).

Hooks from settings files, managed policy settings, and plugins also run inside [subagents](<https://code.claude.com/docs/en/sub-agents>). When a subagent calls a tool, tool events such as `PreToolUse` and `PostToolUse` fire the same configured hooks as in the main conversation, and the input carries the `agent_id` and `agent_type` [common input fields](<#common-input-fields>) that identify the subagent.

Administrators can use [`allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedhooksonly>) in [managed settings](<https://code.claude.com/docs/en/managed-settings>) to restrict which hooks run:

* Your user, project, local, and plugin hooks are blocked. Hooks from plugins force-enabled in managed settings `enabledPlugins` are exempt
* Claude Code also narrows your [`statusLine`](<https://code.claude.com/docs/en/statusline>), [`fileSuggestion`](<https://code.claude.com/docs/en/settings-reference#filesuggestion>), and [`subagentStatusLine`](<https://code.claude.com/docs/en/statusline#subagent-status-lines>) settings to managed settings
* Claude Code also disables plugins with a [`command` source](<https://code.claude.com/docs/en/plugins/marketplace-reference#command-plugin-source>), including plugins force-enabled in managed settings `enabledPlugins`, unless [`disableCommandPluginSources`](<https://code.claude.com/docs/en/settings-reference#disablecommandpluginsources>) is explicitly set to `false`. `command` sources require Claude Code v2.1.229 or later
* Claude Code also blocks marketplace [`headersHelper` commands](<https://code.claude.com/docs/en/plugins/host-marketplace#authenticate-archive-downloads>) unless [`disableCommandPluginSources`](<https://code.claude.com/docs/en/settings-reference#disablecommandpluginsources>) is explicitly set to `false`, except for a marketplace that managed settings themselves declare

See [what runs under `allowManagedHooksOnly`](<https://code.claude.com/docs/en/settings-reference#what-runs-under-allowmanagedhooksonly>).

Hook entries merge across settings levels rather than replacing each other: user, project, and local settings add their own hooks without removing managed ones, and the [`disableAllHooks`](<#disable-or-remove-hooks>) setting can't disable managed hooks from outside managed settings.

The [HTTP hook allowlists](<https://code.claude.com/docs/en/settings-reference#hook-and-skill-settings>) apply to hooks from every source, including managed policy settings:

* `allowedHttpHookUrls`: when defined at any settings level, Claude Code runs an HTTP hook handler only if its URL matches the merged allowlist
* `httpHookAllowedEnvVars`: when defined, Claude Code interpolates only the environment variables on that list into hook headers

### Matcher patterns

The `matcher` field filters when hooks fire. How a matcher is evaluated depends on the characters it contains:

| Matcher value | Evaluated as | Example |
| :- | :- | :- |
| `"*"`, `""`, or omitted | Match all | fires on every occurrence of the event |
| Only letters, digits, `_`, `-`, spaces, `,`, and `\|` | Exact string, or list of exact strings separated by `\|` or `,` with optional surrounding whitespace | `Bash` matches only the Bash tool; `Edit\|Write` and `Edit, Write` each match either tool exactly; `code-reviewer` matches only that agent type |
| Contains any other character | JavaScript regular expression, unanchored | `^Notebook` matches any tool whose name starts with `Notebook`; `mcp__memory__.*` matches every tool from the `memory` server |

A matcher on the regular-expression path is tested with JavaScript's `RegExp.prototype.test`, which succeeds on a match anywhere in the value. `Edit.*` matches both `Edit` and `NotebookEdit`; wrap the pattern in `^` and `$`, as in `^Edit$`, when you need a whole-string match.

`FileChanged` and `StopFailure` use a narrower exact-match set of letters, digits, `_`, and `|` only. A hyphen, space, or comma in a matcher for those two events keeps it on the regular-expression path, and only `|` separates alternatives. Every other event with matcher support in the table that follows accepts `|` or `,`.

The `FileChanged` event doesn't follow these rules when building its watch list. See [FileChanged](<#filechanged>).

Each event type matches on a different field:

| Event | What the matcher filters | Example matcher values |
| :- | :- | :- |
| `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied` | tool name | `Bash`, `Edit\|Write`, `mcp__.*` |
| `SessionStart` | how the session started | `startup`, `resume`, `clear`, `compact`, `fork` |
| `Setup` | which CLI flag triggered setup | `init`, `maintenance` |
| `SessionEnd` | why the session ended | `clear`, `resume`, `logout`, `prompt_input_exit`, `other` |
| `Notification` | notification type | `permission_prompt`, `idle_prompt`, `auth_success`, `elicitation_dialog`, `elicitation_url_dialog`, `elicitation_complete`, `elicitation_response`, `agent_needs_input`, `agent_completed`, `quota_auto_resume_fired`, `quota_auto_resume_stale`, `quota_auto_resume_disabled` |
| `SubagentStart` | agent type | `general-purpose`, `Explore`, `Plan`, custom agent names, or plugin-scoped names like `^my-plugin:reviewer$` |
| `PreCompact`, `PostCompact` | what triggered compaction | `manual`, `auto` |
| `PreModelSwitch`, `PostModelSwitch` | canonical name of the model the session switches to, as described under [PreModelSwitch](<#premodelswitch>) | `claude-opus-5`, `claude-opus-4-6\|claude-opus-5`, `.*opus.*` |
| `SubagentStop` | agent type | same values as `SubagentStart` |
| `ConfigChange` | configuration source | `user_settings`, `project_settings`, `local_settings`, `policy_settings`, `skills` |
| `CwdChanged` | no matcher support | always fires on every occurrence |
| `DirectoryAdded` | how the directory was added | `slash_command`, `register_repo_root` |
| `FileChanged` | literal filenames to watch (see [FileChanged](<#filechanged>)) | `.envrc\|.env` |
| `StopFailure` | error type | `rate_limit`, `overloaded`, `authentication_failed`, `oauth_org_not_allowed`, `account_on_hold`, `billing_error`, `invalid_request`, `model_not_found`, `server_error`, `max_output_tokens`, `cloud_credential_error`, `unknown` |
| `InstructionsLoaded` | load reason | `session_start`, `nested_traversal`, `path_glob_match`, `include`, `compact` |
| `UserPromptExpansion` | command name | your skill or command names |
| `Elicitation` | MCP server name | your configured MCP server names |
| `ElicitationResult` | MCP server name | same values as `Elicitation` |
| `UserPromptSubmit`, `PostToolBatch`, `Stop`, `TeammateIdle`, `TaskCreated`, `TaskCompleted`, `WorktreeCreate`, `WorktreeRemove`, `MessageDisplay` | no matcher support | always fires on every occurrence |

Matching `StopFailure` on `cloud_credential_error` requires Claude Code v2.1.267 or later, the first version that reports credential-load failures under that value rather than `server_error` or `unknown`.

For most events, Claude Code evaluates the matcher against a field from the [JSON input](<#hook-input-and-output>) it sends to your hook on stdin. For tool events, that field is `tool_name`. For `PreModelSwitch` and `PostModelSwitch`, Claude Code evaluates the matcher against the canonical name it derives from `to_model`, as described under [PreModelSwitch](<#premodelswitch>). Each [hook event](<#hook-events>) section lists the full set of matcher values and the input schema for that event.

This example runs a linting script only when Claude writes or edits a file:

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/lint-check.sh"
          }
        ]
      }
    ]
  }
}
```

If you add a `matcher` field to an event without matcher support, it is silently ignored.

For tool events, you can filter more narrowly by setting the [`if` field](<#common-fields>) on individual hook handlers. `if` uses [permission rule syntax](<https://code.claude.com/docs/en/permissions>) to match against the tool name and arguments together, so `"Bash(git *)"` runs when any subcommand of the Bash input matches `git *` and `"Edit(*.ts)"` runs only for TypeScript files.

#### Match MCP tools

[MCP](<https://code.claude.com/docs/en/mcp>) server tools appear as regular tools in tool events (`PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied`), so you can match them the same way you match any other tool name.

MCP tools follow the naming pattern `mcp__<server>__<tool>`, for example:

* `mcp__memory__create_entities`: Memory server's create entities tool
* `mcp__filesystem__read_file`: Filesystem server's read file tool
* `mcp__github__search_repositories`: GitHub server's search tool

To match every tool from a server, append `.*` to the server prefix. The `.*` is required: a matcher like `mcp__memory` or `mcp__brave-search` contains only exact-match characters, so it is compared as an exact string and matches no tool.

* `mcp__memory__.*` matches all tools from the `memory` server
* `mcp__brave-search__.*` matches all tools from a server whose name contains a hyphen
* `mcp__.*__write.*` matches any tool whose name starts with `write` from any server

Tools from a [plugin-bundled MCP server](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>) use a scoped server segment that includes the plugin name: `mcp__plugin_<plugin-name>_<server-name>__<tool>`. A matcher written against the bare server key never fires for these tools. For a plugin named `my-plugin` that bundles a server under the key `db`, a `query` tool appears as `mcp__plugin_my-plugin_db__query`, so the matcher for every tool from that server is `mcp__plugin_my-plugin_db__.*`. Use the same scoped tool name in a handler's [`if` field](<#common-fields>). See [Plugin-provided MCP servers](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>) for how the scoped name is built.

This example logs all memory server operations and validates write operations from any MCP server:

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__memory__.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Memory operation initiated' >> ~/mcp-operations.log"
          }
        ]
      },
      {
        "matcher": "mcp__.*__write.*",
        "hooks": [
          {
            "type": "command",
            "command": "/home/user/scripts/validate-mcp-write.py"
          }
        ]
      }
    ]
  }
}
```

### Hook handler fields

Each object in the inner `hooks` array is a hook handler: the shell command, HTTP endpoint, MCP tool, LLM prompt, or agent that runs when the matcher matches. There are five types:

* **[Command hooks](<#command-hook-fields>)** (`type: "command"`): run a shell command. Your script receives the event's [JSON input](<#hook-input-and-output>) on stdin and communicates results back through exit codes and stdout.
* **[HTTP hooks](<#http-hook-fields>)** (`type: "http"`): send the event's JSON input as an HTTP POST request to a URL. The endpoint communicates results back through the response body using the same [JSON output format](<#json-output>) as command hooks.
* **[MCP tool hooks](<#mcp-tool-hook-fields>)** (`type: "mcp_tool"`): call a tool on a configured [MCP server](<https://code.claude.com/docs/en/mcp>). The tool's text output is treated like command-hook stdout.
* **[Prompt hooks](<#prompt-and-agent-hook-fields>)** (`type: "prompt"`): send a prompt to a Claude model for single-turn evaluation. The model returns its decision as JSON. See [Prompt-based hooks](<#prompt-based-hooks>).
* **[Agent hooks](<#prompt-and-agent-hook-fields>)** (`type: "agent"`): spawn a subagent that can use tools like Read, Grep, and Glob to verify conditions before returning a decision. Agent hooks are experimental and may change. See [Agent-based hooks](<#agent-based-hooks>).

All matching hooks run in parallel. If you define the same handler in more than one settings file, it runs once. A plugin's or skill's copy of the same handler stays separate.

Handlers run in the current directory with Claude Code's environment. If the current directory no longer exists, for example a worktree or temp directory that another shell deleted mid-session, Claude Code runs command hooks from the first of these that still exists: the directory the session started in, the project root, your home directory, or the system temp directory. Claude Code records a warning naming the fallback directory in the [debug log](<#debug-hooks>).

The `$CLAUDE_CODE_REMOTE` environment variable is `"true"` in remote web environments and not set in the local CLI. Claude Code v2.1.199 and later sets [`$CLAUDE_CODE_BRIDGE_SESSION_ID`](<https://code.claude.com/docs/en/env-vars>) to the [Remote Control](<https://code.claude.com/docs/en/remote-control>) session ID while the local session has an active Remote Control connection.

#### Common fields

These fields apply to all hook types:

| Field | Required | Description |
| :- | :- | :- |
| `type` | yes | `"command"`, `"http"`, `"mcp_tool"`, `"prompt"`, or `"agent"` |
| `if` | no | [Permission rule syntax](<https://code.claude.com/docs/en/permissions#permission-rule-syntax>) to filter when this hook runs, such as `"Bash(git *)"` or `"Edit(*.ts)"`. The hook command only runs if the tool call matches the pattern. See the [Bash matching table](<#bash-if-matching>) for how Bash patterns evaluate against subcommands, `$()`, and backticks. Only evaluated on tool events: `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, and `PermissionDenied`. On other events, a hook with `if` set never runs |
| `timeout` | no | Seconds before canceling. Claude Code doesn't enforce it on a command hook you run with [`async: true`](<#run-hooks-in-the-background>). Defaults: 600 for `command`, `http`, and `mcp_tool`; 30 for `prompt`; 60 for `agent`. Claude Code lowers the `command`, `http`, and `mcp_tool` default to 30 on [`UserPromptSubmit`](<#userpromptsubmit>), [`PreModelSwitch`](<#premodelswitch>), and [`PostModelSwitch`](<#postmodelswitch>), and to 10 on [`MessageDisplay`](<#messagedisplay>). [`SessionEnd`](<#sessionend>) hooks share a 1.5-second budget; if your settings set a longer per-hook `timeout`, Claude Code raises the budget to match, up to 60 seconds |
| `statusMessage` | no | Custom spinner message displayed while the hook runs |
| `once` | no | If `true`, Claude Code removes the hook after its first successful run. A run that fails, blocks with exit code 2, or times out leaves the hook in place, so it runs again on the next matching event. Only honored for hooks declared in [skill frontmatter](<#hooks-in-skills-and-agents>); ignored in settings files and agent frontmatter |

The `if` field holds exactly one permission rule. There is no `&&`, `||`, or list syntax for combining rules; to apply multiple conditions, define a separate hook handler for each.

In an `if` condition for a file tool, a single-segment directory pattern like `"Edit(src/**)"` matches only the `src` directory in the working directory and the files under it. To match a directory named `src` at any depth, write `"Edit(**/src/**)"`. Before v2.1.214, `"Edit(src/**)"` matched a directory named `src` at any depth under the working directory.

<h4 id="bash-if-matching">
  How `if` patterns match Bash commands
</h4>

For Bash patterns in the [`if` field](<#common-fields>), whether your hook command runs depends on the shape of the pattern and the Bash command Claude is invoking. Leading `VAR=value` assignments are stripped before matching.

| `if` pattern | Bash command | Hook runs? | Why |
| :- | :- | :- | :- |
| `Bash(git *)` | `FOO=bar git push` | yes | leading assignments are stripped; `git push` matches |
| `Bash(git *)` | `npm test && git push` | yes | each subcommand is checked; `git push` matches |
| `Bash(rm *)` | `echo $(rm -rf /)` | yes | commands inside `$()` and backticks are checked; `rm -rf /` matches |
| `Bash(rm *)` | `echo $(date)` | no | no subcommand matches `rm *` |
| `Bash(git push *)` | `echo $(date)` | yes | patterns that specify more than the command name run the hook anyway on `$()`, backticks, or `$VAR` |

When Claude Code can't determine which commands the Bash input runs, it runs your hook regardless of the pattern. Because the `if` filter is best-effort, use the [permission system](<https://code.claude.com/docs/en/permissions>) rather than a hook to enforce a hard allow or deny.

#### Command hook fields

In addition to the [common fields](<#common-fields>), command hooks accept these fields:

| Field | Required | Description |
| :- | :- | :- |
| `command` | yes | Shell command to execute. With `args`, the executable to spawn directly. See [Exec form and shell form](<#exec-form-and-shell-form>) |
| `args` | no | Argument list. When present, `command` is resolved as an executable and spawned directly with `args` as the argument vector, with no shell involved. See [Exec form and shell form](<#exec-form-and-shell-form>) |
| `async` | no | If `true`, runs in the background without blocking. See [Run hooks in the background](<#run-hooks-in-the-background>) |
| `asyncRewake` | no | If `true`, runs in the background and wakes Claude on exit code 2. The hook's stderr, or stdout if stderr is empty, is shown to Claude as a [system reminder](<https://code.claude.com/docs/en/glossary#system-reminder>) so it can react to a long-running background failure |
| `shell` | no | Shell to use for this hook. Accepts `"bash"` or `"powershell"`. Defaults to `"bash"`, or to `"powershell"` on Windows when Git Bash isn't installed. Setting `"powershell"` runs the command via PowerShell on Windows. Does not require `CLAUDE_CODE_USE_POWERSHELL_TOOL` since hooks spawn PowerShell directly. Ignored when `args` is set |
| `onFailure` | no | What happens to the action when the hook fails: `"continue"`, the default, or `"block"`. See [Block the action when a hook fails](<#block-the-action-when-a-hook-fails>). Requires Claude Code v2.1.295 or later |

<a id="exec-form-and-shell-form" />

##### Exec form and shell form

A command hook runs as exec form when `args` is set, and shell form when `args` is omitted. Set `args` whenever the hook references a [path placeholder](<#reference-scripts-by-path>), since each element is passed as one argument with no quoting. Omit `args` when you need shell features like pipes or `&&`, or when neither concern applies.

**Exec form** runs when `args` is present. Claude Code resolves `command` as an executable on `PATH` and spawns it directly with `args` as the argument vector. There is no shell, so each `args` element is one argument exactly as written, and path placeholders like `${CLAUDE_PLUGIN_ROOT}` are substituted into `command` and into each `args` element as plain strings. Special characters such as apostrophes, `$`, and backticks pass through verbatim because there is no shell to interpret them. No shell tokenization happens on any platform.

**Shell form** runs when `args` is absent. The `command` string is passed to a shell: `sh -c` on macOS and Linux, Git Bash on Windows, or PowerShell when Git Bash isn't installed. Set the `shell` field to choose explicitly. The shell tokenizes the string, expands variables, and interprets pipes, `&&`, redirects, and globs.



**注释：**

  On Windows, exec form requires `command` to resolve to a real executable such as a `.exe`. The `.cmd` and `.bat` shims that npm, npx, eslint, and other tools install in `node_modules/.bin` are not executables and can't be spawned without a shell. To run them in exec form, invoke the underlying script with `node` directly, for example `"command": "node", "args": ["${CLAUDE_PLUGIN_ROOT}/node_modules/eslint/bin/eslint.js"]`. The `node` plus script-path pattern works on every platform because `node.exe` is a real binary. To run a `.cmd` or `.bat` shim by name, use shell form.


This example runs a Node script bundled with a plugin. Exec form passes the resolved script path as one argument with no quoting:

```json theme={null}
{
  "type": "command",
  "command": "node",
  "args": ["${CLAUDE_PLUGIN_ROOT}/scripts/format.js", "--fix"]
}
```

The equivalent shell form needs quoting to handle paths with spaces or special characters:

```json theme={null}
{
  "type": "command",
  "command": "node \"${CLAUDE_PLUGIN_ROOT}\"/scripts/format.js --fix"
}
```

Both forms support the same [path placeholders](<#reference-scripts-by-path>), and both export them as the environment variables `CLAUDE_PROJECT_DIR`, `CLAUDE_PLUGIN_ROOT`, and `CLAUDE_PLUGIN_DATA` on the spawned process, so a script can read `process.env.CLAUDE_PLUGIN_ROOT` regardless of how it was launched.

Plugin hooks additionally substitute [`${user_config.*}`](<https://code.claude.com/docs/en/plugins/manifest-reference#user-configuration>) values, in exec form only: the value is substituted into `command` and into each `args` element as a plain string, so no shell re-parses it.

A shell-form plugin hook whose `command` references `${user_config.*}` fails with an [error](<https://code.claude.com/docs/en/errors#plugin-command-references-user-config>) instead of running. To use an option value from a shell-form hook, read the `$CLAUDE_PLUGIN_OPTION_<KEY>` environment variable, such as `$CLAUDE_PLUGIN_OPTION_WEBHOOK_URL` for a `webhook_url` option, or set `args` to switch the hook to exec form. Before v2.1.207, shell-form plugin hook commands also substituted `${user_config.*}`.



**注释：**

  In exec form, `command` is the executable name or path only. If `command` is a bare name with no path separator and contains whitespace alongside `args`, Claude Code logs a warning because the spawn will fail: there is no executable named `node script.js`. Move the extra tokens into `args`. Absolute paths with spaces, such as `C:\Program Files\nodejs\node.exe`, are a single valid executable and don't trigger the warning.


#### HTTP hook fields

In addition to the [common fields](<#common-fields>), HTTP hooks accept these fields:

| Field | Required | Description |
| :- | :- | :- |
| `url` | yes | URL to send the POST request to |
| `headers` | no | Additional HTTP headers as key-value pairs. Values support environment variable interpolation using `$VAR_NAME` or `${VAR_NAME}` syntax. Only variables listed in `allowedEnvVars` are resolved |
| `allowedEnvVars` | no | List of environment variable names that may be interpolated into header values. References to unlisted variables are replaced with empty strings. Required for any env var interpolation to work |
| `onFailure` | no | What happens to the action when the hook fails: `"continue"`, the default, or `"block"`. See [Block the action when a hook fails](<#block-the-action-when-a-hook-fails>). Requires Claude Code v2.1.295 or later |

Claude Code sends the hook's [JSON input](<#hook-input-and-output>) as the POST request body with `Content-Type: application/json`. The response body uses the same [JSON output format](<#json-output>) as command hooks.

Error handling differs from command hooks; see [HTTP response handling](<#http-response-handling>).

This example sends `PreToolUse` events to a local validation service, authenticating with a token from the `MY_TOKEN` environment variable:

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "http",
            "url": "http://localhost:8080/hooks/pre-tool-use",
            "timeout": 30,
            "headers": {
              "Authorization": "Bearer $MY_TOKEN"
            },
            "allowedEnvVars": ["MY_TOKEN"]
          }
        ]
      }
    ]
  }
}
```

#### MCP tool hook fields

In addition to the [common fields](<#common-fields>), MCP tool hooks accept these fields:

| Field | Required | Description |
| :- | :- | :- |
| `server` | yes | Name of a configured MCP server. For a [plugin-bundled server](<https://code.claude.com/docs/en/mcp#plugin-provided-mcp-servers>), this is the scoped name `plugin:<plugin-name>:<server-name>`, such as `plugin:my-plugin:db`, not the bare server key |
| `tool` | yes | Name of the tool to call on that server |
| `input` | no | Arguments passed to the tool. String values support `${path}` substitution from the hook's [JSON input](<#hook-input-and-output>), such as `"${tool_input.file_path}"` |

This example calls the `security_scan` tool on the `my_server` MCP server after each `Write` or `Edit`, passing the edited file's path:

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "mcp_tool",
            "server": "my_server",
            "tool": "security_scan",
            "input": { "file_path": "${tool_input.file_path}" }
          }
        ]
      }
    ]
  }
}
```

##### How the tool's result is read

Claude Code reads the tool's text content the same way it reads command-hook stdout, following the [parsing rule under exit code 0](<#exit-code-0>). If the tool returns `isError: true`, the hook produces a non-blocking error and execution continues.

##### When the server is still connecting

On events where a hook can block or change the result, such as `PreToolUse` or `Stop`, Claude Code waits for a connecting server before it calls the tool, for at most [`MCP_TIMEOUT`](<https://code.claude.com/docs/en/env-vars>) and within the hook's own [`timeout`](<#common-fields>). On observational events, such as `Notification` or `SessionEnd`, it doesn't wait.

A server showing the [`cached` status](<https://code.claude.com/docs/en/mcp#server-status-detail>) connects when the hook calls its tool. If the server isn't connected at that point, the hook produces a non-blocking error and execution continues. The hook never starts an OAuth flow, so [authenticate the server from `/mcp`](<https://code.claude.com/docs/en/mcp#authenticate-with-remote-mcp-servers>) first.

##### Events that fire before MCP servers are available

`SessionStart` at launch, including with `--continue` or `--resume`, and every `Setup` event fire before the session's MCP servers are available to hooks. Claude Code skips their `mcp_tool` hooks without calling the tool, and the [debug log](<#debug-hooks>) records `mcp_tool hooks are not available for the 'SessionStart' hook event (no MCP client context)`, or the same message naming `Setup`. When `SessionStart` fires again later in the session, after `/clear` or a compaction, its `mcp_tool` hooks run. For anything the session needs at launch, use a `type: "command"` hook on `SessionStart` instead.

#### Prompt and agent hook fields

In addition to the [common fields](<#common-fields>), prompt and agent hooks accept these fields:

| Field | Required | Description |
| :- | :- | :- |
| `prompt` | yes | Prompt text to send to the model. Use `$ARGUMENTS` as a placeholder for the hook input JSON. Escape with a backslash to include literal text: `\$1.00` renders as `$1.00` |
| `model` | no | Model to use for evaluation. Defaults to the model Claude Code uses for [background functionality](<https://code.claude.com/docs/en/costs#background-token-usage>) |

### Reference scripts by path

Use these placeholders to reference hook scripts relative to the project or plugin root, regardless of the working directory when the hook runs:

* `${CLAUDE_PROJECT_DIR}`: the project root where the session started. Claude Code also sets this variable in the environment of [stdio MCP servers](<https://code.claude.com/docs/en/mcp#option-3-add-a-local-stdio-server>) and plugin LSP servers.
* `${CLAUDE_PLUGIN_ROOT}`: the plugin's installation directory, for scripts bundled with a [plugin](<https://code.claude.com/docs/en/plugins/overview>). See [plugin environment variables](<https://code.claude.com/docs/en/plugins/manifest-reference#environment-variables>) for how the path behaves across updates.
* `${CLAUDE_PLUGIN_DATA}`: the plugin's [persistent data directory](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>), for dependencies and state that should survive plugin updates.



**注释：**

  **Worktrees are different.** If Claude enters a [worktree](<https://code.claude.com/docs/en/worktrees>) during the session, Claude Code keeps `${CLAUDE_PROJECT_DIR}` where it was and passes the worktree path to your hooks a different way:

  * **`${CLAUDE_PROJECT_DIR}` stays put**: it still points at the project root where the session started, so a command such as `${CLAUDE_PROJECT_DIR}/.claude/hooks/check-style.sh` still runs the script in the main checkout.
  * **`cwd` follows Claude**: the `cwd` field in the hook's [input JSON](<#common-input-fields>) is the worktree root after Claude enters a worktree, and the new directory after Claude runs `cd`. Read it when a hook needs to know which directory Claude is working in.



Prefer [exec form](<#exec-form-and-shell-form>) for any hook that references a path placeholder. In shell form, wrap each placeholder in double quotes.



  

#### Project scripts

    This example uses `${CLAUDE_PROJECT_DIR}` to run a style checker from the project's `.claude/hooks/` directory after any `Write` or `Edit` tool call:

    ```json theme={null}
    {
      "hooks": {
        "PostToolUse": [
          {
            "matcher": "Write|Edit",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/check-style.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```
  


  

#### Plugin scripts

    Define plugin hooks in `hooks/hooks.json` with an optional top-level `description` field. When a plugin is enabled, its hooks merge with your user and project hooks.

    This example runs a formatting script bundled with the plugin:

    ```json theme={null}
    {
      "description": "Automatic code formatting",
      "hooks": {
        "PostToolUse": [
          {
            "matcher": "Write|Edit",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format.sh",
                "args": [],
                "timeout": 30
              }
            ]
          }
        ]
      }
    }
    ```

    See the [plugin components reference](<https://code.claude.com/docs/en/plugins/components#hooks>) for details on creating plugin hooks.
  


### Hooks in skills and agents

In addition to settings files and plugins, hooks can be defined directly in [skills](<https://code.claude.com/docs/en/skills>) and [subagents](<https://code.claude.com/docs/en/sub-agents>) using frontmatter, in the same configuration format as settings-based hooks. How long Claude Code keeps them registered depends on the component:

* **Subagent hooks**: Claude Code runs them only while that subagent is running and removes them when it finishes. Claude Code converts a `Stop` hook here to `SubagentStop`, the event it fires when a subagent completes.
* **Skill hooks**: Claude Code registers them when you or Claude invoke the skill and keeps running them for the rest of the session, on turns after the skill's own turn as well. To have Claude Code remove a hook after its first successful run instead, set [`once: true`](<#common-fields>) on it.

This skill defines a `PreToolUse` hook that runs a security validation script before each `Bash` command:

```yaml theme={null}
---
name: secure-operations
description: Perform operations with security checks
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/security-check.sh"
---
```

Subagents use the same format in their YAML frontmatter.

Frontmatter hooks in a project skill follow the same [workspace trust rule as hooks in settings files](<#workspace-trust>). Claude Code registers them when you or Claude invoke the skill, including in a `-p` run in a folder you haven't trusted.

Frontmatter hooks in a project subagent run only after you accept the [workspace trust dialog](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) for the folder the agent file came from. A `-p` session doesn't count as accepting it. [What runs before you trust a folder](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>) compares this with the settings-file rule, and the subagents page lists [which scopes are exempt](<https://code.claude.com/docs/en/sub-agents#hooks-in-subagent-frontmatter>). Before v2.1.218, these hooks could run from folders you hadn't trusted.

### The `/hooks` menu

Type `/hooks` in Claude Code to open a read-only browser for your configured hooks. The list labels each hook with where it comes from, such as user settings, project settings, local settings, a plugin, or the current session.

Select a hook to see the full text of what it runs and where it's defined, such as the path of its settings file or the name of its plugin.

To browse all hook events, including ones with no hooks configured, select `All events` at the end of the list.

### Disable or remove hooks

To remove a hook defined in a settings file, delete its entry from that file.

To temporarily disable all hooks without removing them, set `"disableAllHooks": true` in your settings file. Claude Code reads the value left after [settings precedence](<https://code.claude.com/docs/en/settings#settings-precedence>) applies, so a `"disableAllHooks": false` in a project's `.claude/settings.json` overrides a `true` in your user settings. To turn hooks off for one run whatever the project's settings say, pass `--settings '{"disableAllHooks": true}'`, which takes precedence over project and local settings. There is no way to disable an individual hook while keeping it in the configuration.

The `disableAllHooks` setting respects the managed settings hierarchy. If an administrator has configured hooks through managed policy settings, `disableAllHooks` set in user, project, or local settings can't disable those managed hooks. Only `disableAllHooks` set at the managed settings level can disable managed hooks. For the full reach of each level, see [`disableAllHooks`](<https://code.claude.com/docs/en/settings-reference#disableallhooks>).

Direct edits to hooks in settings files are normally picked up automatically by the file watcher.

## Hook input and output

Command hooks receive JSON data via stdin and communicate results through exit codes, stdout, and stderr. HTTP hooks receive the same JSON as the POST request body and communicate results through the HTTP response body. This section covers fields and behavior common to all events. Each event's section under [Hook events](<#hook-events>) includes its specific input schema and decision control options.

On macOS and Linux, command hooks run in their own session without a controlling terminal. The hook process and any child processes can't open `/dev/tty` or send escape sequences directly to the Claude Code interface. Windows has no `/dev/tty`.

To surface a message to the user on any platform, return [`systemMessage`](<#json-output>) in JSON output. Some events discard it or deliver it elsewhere, and each [event's section](<#hook-events>) says so. To trigger a desktop notification, set a window title, or ring the bell, return [`terminalSequence`](<#emit-terminal-notifications>) instead.

### Common input fields

Hook events receive these fields as JSON, in addition to event-specific fields documented in each [hook event](<#hook-events>) section. For command hooks, this JSON arrives via stdin. For HTTP hooks, it arrives as the POST request body.

| Field | Description |
| :- | :- |
| `session_id` | Current session identifier |
| `prompt_id` | UUID identifying the user prompt currently being processed. Matches the [`prompt.id` attribute on OpenTelemetry events](<https://code.claude.com/docs/en/monitoring-usage#event-correlation-attributes>), so you can correlate hook output with telemetry for a single prompt. Absent until the first user input |
| `transcript_path` | Path to conversation JSON. The transcript file is written asynchronously and may lag the in-memory conversation, so it may not yet include the current turn's most recent messages when a hook fires. Hooks that need the final assistant text of the current turn should use `last_assistant_message` on [Stop](<#stop>) and [SubagentStop](<#subagentstop>) instead of reading the transcript |
| `cwd` | Current working directory when the hook is invoked |
| `scratchpad_dir` | Path to the session's [scratchpad directory](<https://code.claude.com/docs/en/claude-directory#session-scratchpad-directory>), where Claude keeps temporary working files. Absent when the session has no scratchpad or the temp directory is unavailable. Requires Claude Code v2.1.257 or later |
| `permission_mode` | Current [permission mode](<https://code.claude.com/docs/en/permissions#permission-modes>): `"default"`, `"plan"`, `"acceptEdits"`, `"auto"`, `"dontAsk"`, or `"bypassPermissions"`. The mode labeled **Manual** arrives as `"default"`, never as `"manual"`, so scripts that match `"default"` keep working. Not all events receive this field. Check the JSON example in each [hook event](<#hook-events>) section |
| `effort` | Object with a `level` field holding the [effort level](<https://code.claude.com/docs/en/model-config#adjust-effort-level>) in effect when the hook runs: `"low"`, `"medium"`, `"high"`, `"xhigh"`, or `"max"`. If you set a level the active model doesn't support, `level` reports the level Claude Code ran instead; [Adjust effort level](<https://code.claude.com/docs/en/model-config#adjust-effort-level>) says how it picks that level. The object matches the [status line](<https://code.claude.com/docs/en/statusline#available-data>) `effort` field. Present for events that fire within a tool-use context, such as `PreToolUse`, `PostToolUse`, `Stop`, and `SubagentStop`, when the current model supports the effort parameter. The level is also available to hook commands and the Bash tool as the `$CLAUDE_EFFORT` environment variable. |
| `hook_event_name` | Name of the event that fired |

When running with `--agent` or inside a subagent, two additional fields are included:

| Field | Description |
| :- | :- |
| `agent_id` | Unique identifier for the subagent. Present only when the hook fires inside a subagent call. Use this to distinguish subagent hook calls from main-thread calls. |
| `agent_type` | Agent name (for example, `"Explore"` or `"security-reviewer"`). Present when the session uses `--agent` or the hook fires inside a subagent. For subagents, the subagent's type takes precedence over the session's `--agent` value. See [SubagentStart](<#subagentstart>) for the values custom and plugin subagents report and how to write a matcher against a plugin-scoped name. |

Only [`SessionStart`](<#sessionstart>) hooks can receive a `model` field, and Claude Code doesn't always include it. [`PreModelSwitch`](<#premodelswitch>) and [`PostModelSwitch`](<#postmodelswitch>) hooks receive `from_model` and `to_model` instead, so use a PostModelSwitch hook to follow the model as it changes during a session.

There is no `$CLAUDE_MODEL` environment variable. The hook can read `$ANTHROPIC_MODEL` if you set it in your shell, but that value doesn't change when you switch models with `/model` during a session.

A hook process inherits the parent environment, apart from the `OTEL_*` exporter variables that Claude Code [removes from every subprocess it spawns](<https://code.claude.com/docs/en/monitoring-usage#administrator-configuration>) and, when [`CLAUDE_CODE_SUBPROCESS_ENV_SCRUB`](<https://code.claude.com/docs/en/env-vars#variables>) is set to `1`, the variables it strips. In a session that [gets the HIPAA configuration](<https://code.claude.com/docs/en/hipaa-setup#check-how-developers-sign-in-and-connect>), Claude Code also [removes Anthropic credentials](<https://code.claude.com/docs/en/hipaa-setup#anthropic-credentials-in-commands-hooks-and-mcp-servers>) from the hook's environment.

For example, a `PreToolUse` hook for a Bash command receives this on stdin:

```json theme={null}
{
  "session_id": "abc123",
  "prompt_id": "550e8400-e29b-41d4-a716-446655440000",
  "transcript_path": "/home/user/.claude/projects/.../transcript.jsonl",
  "cwd": "/home/user/my-project",
  "scratchpad_dir": "/tmp/claude-1000/-home-user-my-project/abc123/scratchpad",
  "permission_mode": "default",
  "hook_event_name": "PreToolUse",
  "tool_name": "Bash",
  "tool_input": {
    "command": "npm test",
    "description": "Run test suite",
    "timeout": 120000,
    "run_in_background": false
  },
  "tool_use_id": "toolu_01ABC123..."
}
```

The `tool_name`, `tool_input`, and `tool_use_id` fields are event-specific. Each [hook event](<#hook-events>) section documents the additional fields for that event.

### Exit code output

Your hook's exit code tells Claude Code whether to continue with the action that triggered the hook, such as a tool call or a prompt. A run that finishes has one of three outcomes:

* **Success**: your hook exits 0. Claude Code applies any [JSON output](<#json-output>) fields your hook printed, and the action goes ahead unless those fields block or deny it.
* **Blocking error**: your hook exits 2. On [events that can block](<#exit-code-2-behavior-per-event>), Claude Code stops the action.
* **Non-blocking error**: your hook exits with any other code, or fails in some other way, such as not starting or printing invalid JSON. The action goes ahead, and on events such as `PreToolUse` you see a `<hook name> hook error` notice in the transcript. If you want a failed hook to block the action, set [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>).

What your hook prints to stdout can change the outcome. For example, if a `PreToolUse` hook exits 1 but prints JSON that passes validation, the run is a success and the JSON fields decide what happens. To find your hook's outcome on an event such as `PreToolUse`, match what it printed to stdout in the first column with its exit code along the top:

| Stdout | Exit 0 | Exit 2 | Any other exit code |
| :- | :- | :- | :- |
| JSON object that passes [schema validation](<#json-output>) | Success. The fields apply | Blocking error. Claude Code still reads the fields, but they can't override the block | Success. Claude Code ignores the exit code, and the fields alone decide. With [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>), this counts as a failure |
| JSON that [can't be parsed](<#exit-code-0>) or fails schema validation | Non-blocking error. The notice carries the parse or validation message | Blocking error. Your stderr is the reason | Non-blocking error. The notice carries the parse or validation message |
| [Plain text](<#exit-code-0>), or nothing | Success | Blocking error. Your stderr is the reason | Non-blocking error. The notice carries the first line of your stderr |

Some events have their own rules:

* **`WorktreeCreate`**: any non-zero exit code makes worktree creation fail, whatever your JSON says.
* **`WorktreeRemove`**: any non-zero exit code makes worktree removal fail if the directory still exists afterward.
* **`Stop`, `SubagentStop`, `TaskCompleted`, and a plugin's `UserPromptSubmit` hook**: when your hook exits 2 with nothing on stdout and its stderr says a file is missing, such as `No such file or directory`, Claude Code treats the run as a non-blocking error.
* **`Elicitation` and `ElicitationResult`**: Claude Code applies your `hookSpecificOutput` when your hook exits 0, and ignores it on any other exit code.
* **Events that discard hook output, such as `StopFailure`**: Claude Code ignores your JSON on any exit code, apart from side-effect fields like `terminalSequence`, which still fire.

To check what exit code 2 does on your event, see [Exit code 2 behavior per event](<#exit-code-2-behavior-per-event>). To check which decision fields it honors, see [Decision control](<#decision-control>).

#### Exit code 0

Exit 0 means success, and is the intended exit code when you print JSON for structured control.

For most events, Claude Code writes stdout to the debug log and doesn't show it in the transcript. The exceptions are `UserPromptSubmit`, `UserPromptExpansion`, `SessionStart`, and `PostModelSwitch`, where Claude Code adds plain-text stdout as context that Claude can see and act on.

Whether Claude Code reads your stdout as [JSON output](<#json-output>) or as plain text depends on how it starts and ends, ignoring surrounding whitespace:

* **Starts with `{` and ends with `}`**: Claude Code parses it as JSON. When the output is two or more lines that each parse as JSON on their own, and no line is a [JSON output](<#json-output>) object that sets a field, Claude Code treats the whole output as plain text. When one of those lines does set a field, the whole output is a parse failure.
* **Starts with `{` but doesn't end with `}`**: Claude Code treats it as plain text.
* **Starts with anything else**: Claude Code treats it as plain text, a JSON array or a quoted JSON string included.

When Claude Code tries to parse your stdout as JSON and can't, or the parsed object fails [schema validation](<#json-output>), the run is a [non-blocking error](<#exit-code-output>). The `<hook name> hook error` notice carries the parse or validation message. On the events that add plain-text stdout as context, Claude Code doesn't add stdout it failed to parse.

Claude never sees stderr from a hook that exits 0. To read it yourself on events such as `PreToolUse`, enable [debug logging](<#debug-hooks>). To surface a warning to Claude from a `PostToolUse` or `PostToolUseFailure` hook, exit 2 instead so [Claude sees the stderr](<#exit-code-2-behavior-per-event>) even though the tool already ran.

#### Exit code 2

Exit with code 2 to block the action. On [events that can block](<#exit-code-2-behavior-per-event>), Claude Code stops the action: a `PreToolUse` hook blocks the tool call, for example, and a `UserPromptSubmit` hook rejects the prompt.

The message that comes with the block is your hook's stderr. If your hook also printed JSON that makes a blocking decision, Claude Code uses that decision's reason instead.

Exit 2 blocks even when your hook prints JSON:

* **JSON that passes schema validation**: Claude Code still reads the [JSON output](<#json-output>) fields, but they can't override the block. Even a `permissionDecision` of `"allow"` doesn't let the action through. On `Elicitation` and `ElicitationResult`, an exit-2 hook's `hookSpecificOutput` is ignored.
* **JSON that fails schema validation**: the hook still blocks. Claude Code uses your stderr as the blocking reason and records the validation failure in the debug log.

This script blocks `rm` commands by exiting 2 and leaves every other command to the normal permission flow:

```bash theme={null}
#!/bin/bash
# Reads JSON input from stdin, checks the command
input=$(cat)
command=$(jq -r '.tool_input.command' <<<"$input")

if [[ "$command" == rm* ]]; then
  echo "Blocked: rm commands are not allowed" >&2
  exit 2  # Blocking error: tool call is prevented
fi

exit 0  # No decision: the normal permission flow applies
```

With this script registered as a `PreToolUse` hook on `Bash`, a command that starts with `rm` is blocked, and Claude receives the hook's stderr as the tool's error, prefixed with the event name, the tool name, and the hook's command:

```text theme={null}
PreToolUse:Bash hook error: [${CLAUDE_PROJECT_DIR}/.claude/hooks/no-rm.sh]: Blocked: rm commands are not allowed
```

#### Other exit codes

When your hook exits with a code other than 0 or 2 and prints plain text or nothing to stdout, the run is a [non-blocking error](<#exit-code-output>). You see a `<hook name> hook error` notice in the transcript with `Failed with non-blocking status code:` and the first line of your hook's stderr. For example, when a `PreToolUse` hook on `Bash` prints `something broke` to stderr and exits 1, the `PreToolUse:Bash hook error` notice carries this line:

```text theme={null}
Failed with non-blocking status code: something broke
```

To capture the full stderr rather than its first line, enable [debug logging](<#debug-hooks>).

A hook that can't start is a non-blocking error too. In shell form, when the script path doesn't exist or isn't executable, the shell exits with a code like 127 and the notice carries the interpreter's message, for example `Failed with non-blocking status code: /bin/sh: /path/to/hook.sh: No such file or directory`. When you set up a policy hook, watch for this notice on its first run, because a mistyped path in `settings.json` means the hook never runs. To block the action instead, set [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>).



**注意：**

  Without valid JSON on stdout, Claude Code treats exit code 1 as a non-blocking error, even though 1 is the conventional Unix failure code. If your hook is meant to enforce a policy, use `exit 2`.


#### Timeouts

Apart from a command hook you run with [`async: true`](<#run-hooks-in-the-background>), Claude Code cancels a `command`, `http`, or `mcp_tool` hook that reaches its [`timeout`](<#common-fields>), discarding the hook's output, so on most events a timed-out hook renders no decision.

On [`PreModelSwitch`](<#premodelswitch>), a hook canceled at its timeout blocks the model switch. On `PreToolUse`, the two hook families differ:

* A timed-out `command`, `http`, or `mcp_tool` hook doesn't block the tool call. The call continues through the normal [permission flow](<https://code.claude.com/docs/en/permissions>), so don't count on a stalled hook to act as a gate. To block the call when a `command` or `http` hook times out, set [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>).
* An [Agent SDK callback hook](<https://code.claude.com/docs/en/agent-sdk/hooks>) that exceeds its timeout [blocks the tool call](<#pretooluse>).

#### Block the action when a hook fails

On most events, when a hook fails or times out, Claude Code still carries out the action, so a policy hook with a wrong path or a crashing script lets everything through. To block the action instead, set `"onFailure": "block"` on a `command` or `http` hook. The default value is `"continue"`. Requires Claude Code v2.1.295 or later.

This `PreToolUse` hook in `.claude/settings.json` runs a project script before each Bash command, and blocks the command if the script fails:

```json theme={null}
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "node",
            "args": ["${CLAUDE_PROJECT_DIR}/.claude/hooks/check-command.js"],
            "onFailure": "block"
          }
        ]
      }
    ]
  }
}
```

To test it, leave `check-command.js` missing and ask Claude to run a Bash command such as `ls`. Claude Code blocks the call, and the error includes `failed; blocking because onFailure is "block"` followed by node's own error output, trimmed here to one line:

```text theme={null}
PreToolUse:Bash hook error: [node ${CLAUDE_PROJECT_DIR}/.claude/hooks/check-command.js]: failed; blocking because onFailure is "block"
Error: Cannot find module '/path/to/project/.claude/hooks/check-command.js'
```

After a timeout, the message says `timed out` instead of `failed`. Without `onFailure` set, the same missing script is a non-blocking error and `ls` runs.

Each of these counts as a failure:

* **Can't start**: a command hook fails to start, for example because the script or executable doesn't exist
* **Exit code other than 0 or 2**: counts for a command hook even if it printed JSON that allows the action, such as `permissionDecision: "allow"`. To return a JSON decision, exit 0
* **HTTP error**: an HTTP hook's connection fails, or the response status isn't 2xx
* **Timeout**: the hook reaches its [`timeout`](<#common-fields>)
* **Invalid output**: the JSON output [can't be parsed](<#exit-code-0>) or fails [schema validation](<#json-output>). For an HTTP hook, a 2xx body that is neither empty nor a JSON object also counts. Plain-text stdout from a command hook isn't a failure

With `"block"` set, a failure does what [exit code 2 does on that event](<#exit-code-2-behavior-per-event>), except on `PermissionRequest`, where it denies the request. For example, a `PreToolUse` failure blocks the tool call and a `UserPromptSubmit` failure blocks the prompt.

The field has no effect on these hooks:

* **`Stop`, `SubagentStop`, `TaskCompleted`, and `TeammateIdle` hooks**: exit code 2 on these events sends Claude back to keep working, and Claude can't repair a hook that won't run
* **Background command hooks**: command hooks that set [`async` or `asyncRewake`](<#run-hooks-in-the-background>)

#### Exit code 2 behavior per event

Exit code 2 is the way a hook signals "stop, don't do this." The effect depends on the event, because some events represent actions that can be blocked (like a tool call that hasn't happened yet) and others represent things that already happened or can't be prevented.

| Hook event | Can block? | What happens on exit 2 |
| :- | :- | :- |
| `PreToolUse` | Yes | Blocks the tool call |
| `PermissionRequest` | No | Exit code 2 isn't honored for this event and the permission flow proceeds unchanged. Deny through the [`decision` object](<#permissionrequest-decision-control>) instead |
| `UserPromptSubmit` | Yes | Blocks the prompt, so it never reaches Claude. See [What a blocked prompt leaves behind](<#what-a-blocked-prompt-leaves-behind>) |
| `UserPromptExpansion` | Yes | Blocks the expansion |
| `Stop` | Yes | Prevents Claude from stopping, continues the conversation |
| `SubagentStop` | Yes | Prevents the subagent from stopping |
| `TeammateIdle` | Yes | Prevents the teammate from going idle, so it continues working |
| `TaskCreated` | Yes | Rolls back the task creation |
| `TaskCompleted` | Yes | Prevents the task from being marked as completed |
| `ConfigChange` | Yes | Blocks the configuration change from taking effect (except `policy_settings`) |
| `StopFailure` | No | Output and exit code are ignored, except `terminalSequence` |
| `PostToolUse` | No | Shows stderr to Claude; the tool already ran |
| `PostToolUseFailure` | No | Shows stderr to Claude; the tool already failed |
| `PostToolBatch` | Yes | Stops the agentic loop before the next model call |
| `PermissionDenied` | No | Exit code and stderr are ignored because the denial already occurred. Use JSON `hookSpecificOutput.retry: true` to tell the model it may retry; Claude Code ignores `retry: true` for [no-verdict denials](<#permissiondenied-decision-control>) |
| `Notification` | No | Exit code and stderr are ignored |
| `SubagentStart` | No | Shows stderr to user only |
| `SessionStart` | No | Shows stderr to user only |
| `Setup` | No | Exit code and stderr are ignored |
| `SessionEnd` | No | Shows stderr to user only |
| `CwdChanged` | No | Shows stderr to user only |
| `DirectoryAdded` | No | Stderr goes to the debug log; the directory is already added |
| `FileChanged` | No | Shows stderr to user only |
| `PreCompact` | Yes | Blocks compaction |
| `PostCompact` | No | Shows stderr to user only |
| `PreModelSwitch` | Yes | Blocks the model switch and shows stderr to the user |
| `PostModelSwitch` | No | Shows stderr to user only; the model already switched |
| `Elicitation` | Yes | Declines the request, and no dialog appears |
| `ElicitationResult` | Yes | Blocks the response (action becomes decline) |
| `WorktreeCreate` | Yes | Any non-zero exit code causes worktree creation to fail |
| `WorktreeRemove` | Yes | Any non-zero exit code causes worktree removal to fail if the directory still exists afterward. See [WorktreeRemove](<#worktreeremove>) for what happens to the directory |
| `InstructionsLoaded` | No | Exit code is ignored |
| `MessageDisplay` | No | The original text is displayed |

For `SessionStart`, `SubagentStart`, and `PostModelSwitch`, Claude Code renders the exit code 2 stderr in the transcript as a `<hook name> hook error` notice, the same way it renders a [non-blocking error](<#exit-code-output>). Claude doesn't see it, and the session or subagent proceeds. For `SubagentStart`, the notice appears in the subagent's own transcript, not in the parent conversation.

### HTTP response handling

HTTP hooks use HTTP status codes and response bodies instead of exit codes and stdout. The outcomes below apply to most events; an event with its own failure contract in the [per-event table](<#exit-code-2-behavior-per-event>), such as `WorktreeCreate`, applies that contract to a failed HTTP hook too:

* **2xx with an empty body**: success, equivalent to exit code 0 with no output
* **2xx with a JSON object body**: parsed using the same [JSON output](<#json-output>) schema as command hooks. A body that fails schema validation is a non-blocking error
* **2xx with any other body, such as plain text**: non-blocking error, handled the same as a non-2xx status. Claude Code doesn't add the text to Claude's context
* **Non-2xx status**: non-blocking error, execution continues
* **Connection failure**: non-blocking error, execution continues
* **Timeout**: the hook is canceled, as described under [Timeouts](<#timeouts>)

HTTP hooks can't signal a blocking error through the status code alone: a non-2xx status or a failed connection is a [non-blocking error](<#exit-code-output>). To block a tool call or deny a permission, return a 2xx response with a JSON body containing the appropriate decision fields. To block the action when the request fails or returns a non-2xx status, set [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>).

### JSON output

Exit codes only let you block or stay silent, but JSON output gives you finer-grained control. Instead of exiting with code 2 to block, exit 0 and print a JSON object to stdout. Claude Code reads specific fields from that JSON to control behavior, including [decision control](<#decision-control>) for blocking, allowing, or escalating to the user.



**注释：**

  Choose one approach per hook: either use exit codes alone for signaling, or exit 0 and print JSON for structured control. If you mix them, exit 2 keeps its [blocking effect](<#exit-code-2-behavior-per-event>), and Claude Code still reads the JSON fields, with the one elicitation exception noted under [Exit code 2](<#exit-code-2>).


Your hook's stdout must contain only the JSON object. If your shell profile prints text on startup, it can interfere with JSON parsing. See [Hook JSON has no effect](<https://code.claude.com/docs/en/hooks-guide#hook-json-has-no-effect>) in the troubleshooting guide.

A hook's `additionalContext`, `systemMessage`, and `initialUserMessage` strings, and its plain stdout, are capped at 10,000 characters:

* **Scope**: Claude Code measures each string on its own, even when several hooks run for the same event. For JSON output, each field is measured separately; plain stdout is measured whole.
* **Over the limit**: Claude Code saves the output to a file in the session directory and replaces it with the file path and a preview of up to the first 2,000 characters. A large valid Bash result is handled the same way, described under [Output limits](<https://code.claude.com/docs/en/tools-reference#output-limits>). Unlike that Bash ceiling, this cap has no setting or environment variable to raise it.
* **Reading the file**: Claude Code doesn't ask Claude to read the file, so keep anything Claude must always see within the cap.

The JSON object supports three kinds of fields:

* **Universal fields** like `continue` are listed in the table below. Every event accepts them, but some events discard them or deliver `systemMessage` somewhere other than the transcript. Each event's section says so. `terminalSequence` works on those events too, with the exceptions listed under [Emit terminal notifications](<#emit-terminal-notifications>).
* **Top-level `decision` and `reason`** are used by some events to block or provide feedback.
* **`hookSpecificOutput`** is a nested object for events that need richer control. It requires a `hookEventName` field set to the event name.

| Field | Default | Description |
| :- | :- | :- |
| `continue` | `true` | If `false`, Claude stops processing entirely after the hook runs. Takes precedence over any event-specific decision fields |
| `stopReason` | none | Message shown to the user when `continue` is `false`. It stays in the conversation, so Claude sees it if the conversation continues |
| `suppressOutput` | `false` | Has no effect: Claude Code accepts the field but doesn't act on it. A successful hook's stdout is never shown in the transcript and is recorded in the debug log |
| `systemMessage` | none | Warning message shown to the user. In [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) and [`--output-format stream-json`](<https://code.claude.com/docs/en/headless>) output, it can arrive as an [`SDKInformationalMessage`](<https://code.claude.com/docs/en/agent-sdk/typescript#sdkinformationalmessage>) |
| `terminalSequence` | none | A terminal escape sequence for Claude Code to emit on your behalf, such as a desktop notification, window title, or bell. Restricted to OSC `0`/`1`/`2`/`9`/`99`/`777` and BEL. If the value contains anything outside the allowlist, the field is ignored. Use this instead of writing to `/dev/tty`, which is unavailable to hooks |

To stop Claude entirely:

```json theme={null}
{ "continue": false, "stopReason": "Build failed, fix errors before continuing" }
```

For `PreToolUse` and `PostToolUse` hooks, the stop applies even when the tool call fails or completes while Claude is still streaming a response.

#### Emit terminal notifications

Hooks run without a controlling terminal, so writing escape sequences directly to `/dev/tty` fails. Instead, return the escape sequence in the `terminalSequence` field and Claude Code emits it for you through its own terminal write path. This is race-free, works inside tmux and GNU screen, and works on Windows where there is no `/dev/tty`.

The field accepts a string of one or more allowlisted escape sequences:

* OSC `0`, `1`, `2`: window and icon titles
* OSC `9`: iTerm2, ConEmu, Windows Terminal, and WezTerm notifications, including `9;4` taskbar progress
* OSC `99`: Kitty notifications
* OSC `777`: urxvt, Ghostty, and Warp notifications
* Bare BEL

Sequences may be terminated with BEL or with ST. Anything outside the allowlist, including CSI cursor and color sequences, OSC palette sequences, OSC 8 hyperlinks, OSC 52 clipboard writes, and OSC 1337, is rejected and the field is ignored.

Claude Code writes the sequence itself when it processes your hook's output, so the field works on events that discard `systemMessage` and `continue`, such as `Notification` and `StopFailure`. It has two limits:

* Claude Code writes the sequence only in an interactive session, and only while its interface is on screen. In non-interactive mode with the `-p` flag and in the Agent SDK, it ignores the field.
* A `WorktreeCreate` command hook can't return JSON, because Claude Code reads its stdout as the worktree path. An HTTP `WorktreeCreate` hook returns JSON and can include the field.

The example below fires a desktop notification from a `Notification` hook. The escape sequence is built with `printf` octal escapes so the control bytes never appear on the shell command line, and `jq -n --arg` builds the JSON output so quotes, backslashes, and newlines in the notification message are escaped correctly:

```bash theme={null}
#!/bin/bash
# Notification hook: ping the desktop when Claude Code needs attention.
input=$(cat)
title="Claude Code"
body=$(jq -r '.message // "Needs your attention"' <<<"$input")
seq=$(printf '\033]777;notify;%s;%s\007' "$title" "$body")
jq -nc --arg seq "$seq" '{terminalSequence: $seq}'
```

The `{ "terminalSequence": "..." }` shape is the same from any shell or language.

#### Add context for Claude

The `additionalContext` field passes a string from your hook into Claude's context window. Claude Code wraps the string in a [system reminder](<https://code.claude.com/docs/en/glossary#system-reminder>) and inserts it into the conversation at the point where the hook fired. Claude reads the reminder on the next model request, but it doesn't appear as a chat message in the interface.

Return `additionalContext` inside `hookSpecificOutput` alongside the event name:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "This file is generated. Edit src/schema.ts and run `bun generate` instead."
  }
}
```

Where the reminder appears depends on the event:

* [SessionStart](<#sessionstart>) and [SubagentStart](<#subagentstart>): at the start of the conversation, before the first prompt
* [UserPromptSubmit](<#userpromptsubmit>) and [UserPromptExpansion](<#userpromptexpansion>): alongside the submitted prompt
* [PreToolUse](<#pretooluse>), [PostToolUse](<#posttooluse>), [PostToolUseFailure](<#posttoolusefailure>), and [PostToolBatch](<#posttoolbatch>): next to the tool result
* [Stop](<#stop>) and [SubagentStop](<#subagentstop>): at the end of the turn. The conversation continues so Claude can act on the feedback. See [Stop decision control](<#stop-decision-control>)
* [PostModelSwitch](<#postmodelswitch>): with the next request after the switch. See [PostModelSwitch decision control](<#postmodelswitch-decision-control>) for timing

When several hooks return `additionalContext` for the same event, Claude receives all of the values.

If a value exceeds 10,000 characters, Claude Code writes the text to a file in the session directory and passes Claude the file path with a preview of up to the first 2,000 characters instead. Claude can read the file, but Claude Code doesn't ask it to.

Use `additionalContext` for information Claude should know about the current state of your environment or the operation that just ran:

* **Environment state**: the current branch, deployment target, or active feature flags
* **Conditional project rules**: which test command applies to the file just edited, which directories are read-only in this worktree
* **External data**: open issues assigned to you, recent CI results, content fetched from an internal service

For instructions that never change, prefer [CLAUDE.md](<https://code.claude.com/docs/en/memory>). It loads without running a script and is the standard place for static project conventions.

Write the text as factual statements rather than imperative system instructions. Phrasing such as "The deployment target is production" or "This repo uses `bun test`" reads as project information. Text framed as out-of-band system commands can trigger Claude's prompt-injection defenses, which causes Claude to surface the text to you instead of treating it as context.

Claude Code saves the injected text in the session transcript. For mid-session events like `PostToolUse` or `UserPromptSubmit`, when you resume with `--continue` or `--resume`, Claude Code replays the saved text rather than re-running the hook for past turns, so values like timestamps or commit SHAs become stale. `SessionStart` hooks run again on resume with `source` set to `"resume"`, or `"fork"` if you added `--fork-session`, so they can refresh their context.

#### Decision control

Not every event supports blocking or controlling behavior through JSON. The events that do each use a different set of fields to express that decision. Use this table as a quick reference before writing a hook:

| Events | Decision pattern | Key fields |
| :- | :- | :- |
| UserPromptSubmit, UserPromptExpansion, PostToolUse, PostToolUseFailure, PostToolBatch, Stop, SubagentStop, ConfigChange, PreCompact | Top-level `decision` | `decision: "block"`, `reason`. Stop and SubagentStop also accept `hookSpecificOutput.additionalContext` for [non-error feedback that continues the conversation](<#stop-decision-control>) |
| TeammateIdle, TaskCompleted | Exit code or `continue: false` | Exit code 2 blocks the action with stderr feedback. JSON `{"continue": false, "stopReason": "..."}` also stops the teammate entirely, matching `Stop` hook behavior; [TaskCompleted ignores it when the `TaskUpdate` tool triggered the event](<#taskcompleted-decision-control>) |
| TaskCreated | Exit code or top-level `decision` | Exit code 2 or `decision: "block"` [cancels the task](<#taskcreated-decision-control>) and returns the message to Claude. `continue: false` is ignored |
| PreToolUse | `hookSpecificOutput` | `permissionDecision` (allow/deny/ask/defer), `permissionDecisionReason` |
| PreModelSwitch | `hookSpecificOutput` or top-level `decision` | `permissionDecision` (allow/deny/ask), `permissionDecisionReason`. `decision: "block"` also [cancels the switch](<#premodelswitch-decision-control>) |
| PermissionRequest | `hookSpecificOutput` | `decision.behavior` (allow/deny) |
| PermissionDenied | `hookSpecificOutput` | `retry: true` tells the model it may retry the denied tool call; Claude Code ignores it for [no-verdict denials](<#permissiondenied-decision-control>) |
| WorktreeCreate | path return | Command hook prints path on stdout; HTTP hook returns `hookSpecificOutput.worktreePath`. Hook failure or missing path fails creation |
| WorktreeRemove | Exit code | Any non-zero exit code makes the removal fail if the directory still exists afterward. JSON output is discarded |
| Elicitation, ElicitationResult | `hookSpecificOutput` or top-level `decision` | `action` (accept/decline/cancel), `content` (form field values). `decision: "block"` also [declines](<#other-ways-to-decline-an-elicitation>) |
| MessageDisplay | `hookSpecificOutput` | `displayContent` replaces the displayed text on screen. Display-only: the transcript and what Claude sees keep the original |
| SessionStart, SubagentStart, PostModelSwitch | Context only | `hookSpecificOutput.additionalContext` adds context for Claude. SessionStart also accepts [`initialUserMessage`, `watchPaths`, `sessionTitle`, and `reloadSkills`](<#sessionstart-decision-control>). No blocking or decision control |
| Setup, Notification, SessionEnd, PostCompact, InstructionsLoaded, StopFailure, CwdChanged, DirectoryAdded, FileChanged | None | No decision control. Used for side effects like logging or cleanup |

A few events can also rewrite content rather than only allow or block it:

* `PreToolUse`: `updatedInput` directly under `hookSpecificOutput` replaces a tool's arguments before it runs. See [PreToolUse decision control](<#pretooluse-decision-control>)
* `PermissionRequest`: `updatedInput` inside the `decision` object. See [PermissionRequest decision control](<#permissionrequest-decision-control>)
* `PostToolUse`: `updatedToolOutput` replaces the tool's result. See [PostToolUse decision control](<#posttooluse-decision-control>)
* `UserPromptSubmit`: can't replace the prompt; it only injects `additionalContext` alongside it

For redaction or transformation use cases, intercept at `PreToolUse` for outbound tool inputs and `PostToolUse` for inbound tool results.

Here are examples of each pattern in action:



  

#### Top-level decision

    The only value for `decision` is `"block"`. To allow the action to proceed, omit `decision` from your JSON, or exit 0 without any JSON at all:

    ```json theme={null}
    {
      "decision": "block",
      "reason": "Test suite must pass before proceeding"
    }
    ```
  


  

#### PreToolUse

    Uses `hookSpecificOutput` for richer control: allow, deny, or escalate to the user. You can also modify tool input before it runs or inject additional context for Claude. See [PreToolUse decision control](<#pretooluse-decision-control>) for the full set of options.

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PreToolUse",
        "permissionDecision": "deny",
        "permissionDecisionReason": "Database writes are not allowed"
      }
    }
    ```
  


  

#### PermissionRequest

    Uses `hookSpecificOutput` to allow or deny a permission request on behalf of the user. When allowing, you can also modify the tool's input or apply permission rules so the user isn't prompted again. See [PermissionRequest decision control](<#permissionrequest-decision-control>) for the full set of options.

    ```json theme={null}
    {
      "hookSpecificOutput": {
        "hookEventName": "PermissionRequest",
        "decision": {
          "behavior": "allow",
          "updatedInput": {
            "command": "npm run lint"
          }
        }
      }
    }
    ```
  


For extended examples including Bash command validation, prompt filtering, and auto-approval scripts, see [What you can automate](<https://code.claude.com/docs/en/hooks-guide#what-you-can-automate>) in the guide and the [Bash command validator reference implementation](<https://github.com/anthropics/claude-code/blob/main/examples/hooks/bash_command_validator_example.py>).

## Hook events

Each event corresponds to a point in Claude Code's lifecycle where hooks can run. The sections below are ordered to match the lifecycle: from session setup through the agentic loop to session end. Each section describes when the event fires, what matchers it supports, the JSON input it receives, and how to control behavior through output.

### SessionStart

Runs when Claude Code starts a new session or resumes an existing session. Useful for loading development context like existing issues or recent changes to your codebase, or setting up environment variables. For static context that doesn't require a script, use [CLAUDE.md](<https://code.claude.com/docs/en/memory>) instead.

SessionStart runs on every session, so keep these hooks fast. Only `type: "command"` and `type: "mcp_tool"` hooks are supported. See [MCP tool hook fields](<#mcp-tool-hook-fields>) for when `mcp_tool` hooks run.

The matcher value corresponds to how the session was initiated:

| Matcher | When it fires |
| :- | :- |
| `startup` | New session |
| `resume` | `--resume`, `--continue`, or `/resume` |
| `clear` | `/clear` |
| `compact` | Auto or manual compaction |
| `fork` | A new session forked from an existing one: `--fork-session` with `--resume` or `--continue`, the `/fork` background copy, `/branch`, or a conversation you [move to the background](<https://code.claude.com/docs/en/agent-view#from-inside-a-session>) |

Before v2.1.214, forked sessions reported source `"resume"`.

When you start an interactive session, resume a conversation at launch with `--continue` or `--resume`, or run `/clear`, SessionStart hooks run in the background. You can type right away, and a conversation you resumed appears without waiting for the hooks. Claude's first response still waits for the hooks to finish, so their context reaches Claude.

When you switch conversations with `/resume` inside a session, the switch waits for the hooks to finish instead. If you run `/clear` or switch to another conversation while background hooks are still running, nothing they return applies to the session.

The same wait applies at launch, including a resumed session: a prompt you send while SessionStart hooks are still running doesn't reach Claude until they finish.

During either wait, press `Esc` to take the prompt back into the input without sending it. The hooks keep running.

#### SessionStart input

In addition to the [common input fields](<#common-input-fields>), SessionStart hooks receive `source` and optionally `model`, `agent_type`, and `session_title`:

| Field | Description |
| :- | :- |
| `source` | How the session started: `"startup"` for new sessions, `"resume"` for resumed sessions, `"clear"` after `/clear`, `"compact"` after compaction, or `"fork"` for a new session forked from an existing one |
| `model` | The active model identifier. It can be omitted, for example after `/clear` or when a session is restored through conversation recovery, so check for the field before reading it |
| `agent_type` | The agent name, present when you start Claude Code with `claude --agent <name>` |
| `session_title` | The session's custom title, present when one is set, for example with `--name`, `/rename`, a hook's `sessionTitle` output, or the Agent SDK's `renameSession()`. A hook that emits `sessionTitle` can check this field first to avoid overwriting an existing custom title |

A session you haven't named can still have a [generated title](<https://code.claude.com/docs/en/sessions#name-your-sessions>). That title isn't a custom title and doesn't appear in `session_title`.

When `source` is `"resume"` or `"fork"` and the transcript contains at least one response from Claude, SessionStart hooks also receive the four fields below. Your hook can use them to report what resuming a stale conversation costs before the first request, for example in a [`systemMessage`](<#json-output>). These fields require Claude Code v2.1.251 or later.

| Field | Description |
| :- | :- |
| `seconds_since_last_response` | Wall-clock seconds since the last response in the resumed transcript |
| `context_tokens` | Tokens the first request of the resumed session re-sends as its prompt |
| `prompt_cache_likely_expired` | `true` when the last response is older than the session's [prompt cache lifetime](<https://code.claude.com/docs/en/prompt-caching#cache-lifetime>) or a later compaction replaced the cached conversation |
| `estimated_cache_write_usd` | Estimated cost in US dollars of writing `context_tokens` to the prompt cache on the session's model, excluding the response |

This example shows the input for a session resumed 90 minutes after its last response:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SessionStart",
  "source": "resume",
  "model": "claude-opus-5",
  "seconds_since_last_response": 5400,
  "context_tokens": 182340,
  "prompt_cache_likely_expired": true,
  "estimated_cache_write_usd": 1.1396
}
```

#### SessionStart decision control

A SessionStart hook can add context for Claude, supply the first user message, set the session title, watch files, and reload skills. Return the field for each one, in addition to the [JSON output fields](<#json-output>) available to all hooks:

| Field | Description |
| :- | :- |
| `additionalContext` | String added to Claude's context at the start of the conversation, before the first prompt. See [Add context for Claude](<#add-context-for-claude>) for how the text is delivered and what to put in it |
| `initialUserMessage` | String used as the first user message of the session, in [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag. It becomes the first turn even if you pass no prompt. A prompt you do pass follows as the next turn |
| `sessionTitle` | Sets the session title, with the same effect as `/rename`. Applies when `source` is `"startup"`, `"resume"`, or `"fork"` |
| `watchPaths` | Array of absolute paths to watch for [FileChanged](<#filechanged>) events during this session |
| `reloadSkills` | Boolean. When `true`, Claude Code re-scans the [skill](<https://code.claude.com/docs/en/skills>) and command directories after the SessionStart hooks complete. See [Reload skills that a hook installs](<#reload-skills-that-a-hook-installs>) |

This output adds context and names the session:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "SessionStart",
    "additionalContext": "Current branch: feat/auth-refactor\nUncommitted changes: src/auth.ts, src/login.tsx\nActive issue: #4211 Migrate to OAuth2",
    "sessionTitle": "auth-refactor"
  }
}
```

A hook that only adds context can print it without building JSON, because Claude Code adds a SessionStart hook's [plain-text stdout](<#exit-code-0>) to Claude's context.

If your plugin's SessionStart hook supplies `initialUserMessage` or `sessionTitle`, install the plugin before the session starts. Claude Code ignores both fields from a plugin that finishes installing after the SessionStart hooks have run.

#### Reload skills that a hook installs

To make skills that a SessionStart hook installs available in the same session, return `reloadSkills`. Skill discovery normally runs before SessionStart hooks finish, so without it, files a hook writes into `~/.claude/skills/` or `.claude/skills/` can be missing when the first prompt runs.

This example syncs a shared skills repository and requests the re-scan:

```bash theme={null}
#!/bin/bash

git -C ~/.claude/skills/team-skills pull --quiet 2>/dev/null || \
  git clone --quiet https://git.example.com/your-org/team-skills.git ~/.claude/skills/team-skills

echo '{"hookSpecificOutput": {"hookEventName": "SessionStart", "reloadSkills": true}}'
```

The repository URL is a placeholder. Replace it with your own skills repository.

#### Persist environment variables

SessionStart hooks have access to the `CLAUDE_ENV_FILE` environment variable, which provides a file path where you can persist environment variables for subsequent Bash commands.

To set individual environment variables, write `export` statements to `CLAUDE_ENV_FILE`. Use append (`>>`) to preserve variables set by other hooks:

```bash theme={null}
#!/bin/bash

if [ -n "$CLAUDE_ENV_FILE" ]; then
  echo 'export NODE_ENV=production' >> "$CLAUDE_ENV_FILE"
  echo 'export DEBUG_LOG=true' >> "$CLAUDE_ENV_FILE"
  echo 'export PATH="$PATH:./node_modules/.bin"' >> "$CLAUDE_ENV_FILE"
fi

exit 0
```

To capture all environment changes from setup commands, compare the exported variables before and after:

```bash theme={null}
#!/bin/bash

ENV_BEFORE=$(export -p | sort)

# Run your setup commands that modify the environment
source ~/.nvm/nvm.sh
nvm use 20

if [ -n "$CLAUDE_ENV_FILE" ]; then
  ENV_AFTER=$(export -p | sort)
  comm -13 <(echo "$ENV_BEFORE") <(echo "$ENV_AFTER") >> "$CLAUDE_ENV_FILE"
fi

exit 0
```



**注释：**

  `CLAUDE_ENV_FILE` is available for SessionStart, [Setup](<#setup>), [CwdChanged](<#cwdchanged>), and [FileChanged](<#filechanged>) hooks. Other hook types don't have access to this variable.


### Setup

Fires only when you launch Claude Code with `--init-only`, or with `--init` or `--maintenance` in [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag. It doesn't fire on normal startup. Use it for one-time dependency installation or scheduled cleanup that you trigger explicitly from CI or scripts, separate from normal session startup. For per-session initialization, use [SessionStart](<#sessionstart>) instead.

The matcher value corresponds to the CLI flag that triggered the hook:

| Matcher | When it fires |
| :- | :- |
| `init` | `claude --init-only` or `claude -p --init` |
| `maintenance` | `claude -p --maintenance` |

When you run `claude --init-only`, Claude Code runs Setup hooks and `SessionStart` hooks with the `startup` matcher, then exits without starting a conversation.

When you start or continue a conversation with `-p`, you also need to supply a prompt, as an argument or piped on stdin. You can skip the prompt when a `SessionStart` hook supplies [`initialUserMessage`](<#sessionstart-decision-control>) or when you resume a session with a [deferred tool call](<#defer-a-tool-call-for-later>).

On success, `--init-only` prints nothing to the terminal. To confirm the hooks ran, start with `claude --debug-file <path> --init-only`, replacing `<path>` with a log file location, and check the log for the Setup and SessionStart hook entries.

Because Setup doesn't fire on every launch, a plugin that needs a dependency installed can't rely on Setup alone. The practical pattern is to check for the dependency on first use and install on miss, for example a hook or skill that tests for `${CLAUDE_PLUGIN_DATA}/node_modules` and runs `npm install` if absent. See the [persistent data directory](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>) for where to store installed dependencies. If you distribute your plugin through a marketplace, you may not need this pattern: Claude Code [installs eligible Node.js package dependencies automatically](<https://code.claude.com/docs/en/plugins/loading#node-js-package-dependencies>) when it caches the plugin.

#### Setup input

In addition to the [common input fields](<#common-input-fields>), Setup hooks receive a `trigger` field set to either `"init"` or `"maintenance"`:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Setup",
  "trigger": "init"
}
```

#### Setup decision control

Setup hooks can't block; execution continues on any exit code. On every exit code, Claude Code discards a Setup hook's [JSON output fields](<#json-output>), such as `systemMessage`, `continue`, and `hookSpecificOutput.additionalContext`. With `-p`, a Setup hook's stdout, stderr, and exit code appear in the run's output only as [`hook_response` events](<https://code.claude.com/docs/en/headless#read-session-metadata>) when you launch with `--output-format stream-json --verbose`.

Setup hooks have access to `CLAUDE_ENV_FILE`. Variables written to that file persist into subsequent Bash commands for the session, as in [SessionStart hooks](<#persist-environment-variables>). Only `type: "command"` hooks run on `Setup`. A `type: "mcp_tool"` hook on `Setup` is always skipped, as described under [MCP tool hook fields](<#mcp-tool-hook-fields>).

### InstructionsLoaded

Fires when a `CLAUDE.md` or `.claude/rules/*.md` file is loaded into context. This event fires at session start for eagerly loaded files and again later when files are lazily loaded, for example when Claude accesses a subdirectory that contains a nested `CLAUDE.md` or when conditional rules with `paths:` frontmatter match. The hook doesn't support blocking or decision control. It runs asynchronously for observability purposes.

This event doesn't fire when Claude [reads `AGENTS.md` directly](<https://code.claude.com/docs/en/memory#agents-md>) through the **Project instructions** setting. It does fire when a `CLAUDE.md` imports your `AGENTS.md`, with `load_reason` set to `include` as for any other imported file, and when `CLAUDE.md` is a symlink to it, as a normal `CLAUDE.md` load.

The matcher runs against `load_reason`. For example, use `"matcher": "session_start"` to fire only for files loaded at session start, or `"matcher": "path_glob_match|nested_traversal"` to fire only for lazy loads.

#### InstructionsLoaded input

In addition to the [common input fields](<#common-input-fields>), InstructionsLoaded hooks receive these fields:

| Field | Description |
| :- | :- |
| `file_path` | Absolute path to the instruction file that was loaded |
| `memory_type` | Scope of the file: `"User"`, `"Project"`, `"Local"`, or `"Managed"` |
| `load_reason` | Why the file was loaded: `"session_start"`, `"nested_traversal"`, `"path_glob_match"`, `"include"`, or `"compact"`. The `"compact"` value fires when instruction files are re-loaded after a compaction event |
| `globs` | Path glob patterns from the file's `paths:` frontmatter, if any. Present only for `path_glob_match` loads |
| `trigger_file_path` | Path to the file whose access triggered this load, for lazy loads |
| `parent_file_path` | Path to the parent instruction file that included this one, for `include` loads |

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "InstructionsLoaded",
  "file_path": "/Users/my-project/CLAUDE.md",
  "memory_type": "Project",
  "load_reason": "session_start"
}
```

#### InstructionsLoaded decision control

InstructionsLoaded hooks have no decision control. They can't block or modify instruction loading. Claude Code discards their [JSON output fields](<#json-output>), such as `systemMessage` and `continue`. Use this event for audit logging, compliance tracking, or observability.

### UserPromptSubmit

Runs when a prompt is submitted, before Claude processes it. This allows you
to add additional context based on the prompt/conversation, validate prompts, or
block certain types of prompts.

`UserPromptSubmit` hooks don't fire only on prompts you type. Claude Code also runs them on:

* A [scheduled task](<https://code.claude.com/docs/en/scheduled-tasks>) firing, including a `/loop` iteration
* A [background subagent](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>) reporting back to the session that started it
* A [message another session sends](<https://code.claude.com/docs/en/cross-session-messaging>) to your main conversation

`UserPromptSubmit` hooks have a default timeout of 30 seconds for `command`, `http`, and `mcp_tool` types, shorter than the 600-second default for those types on most other events. Because this hook runs before every prompt and blocks model processing until it completes, a stuck hook stalls the session. If your hook needs more time, set the `timeout` field in the hook entry.

Apart from a command hook you run with [`async: true`](<#run-hooks-in-the-background>), a `UserPromptSubmit` command, HTTP, or MCP tool hook that reaches its timeout is canceled and its output, including any `additionalContext`, is discarded. The prompt still reaches Claude without that context. To block the prompt instead, set [`onFailure: "block"`](<#block-the-action-when-a-hook-fails>) on a command or HTTP hook. The transcript shows a notice naming the hook, the timeout that fired, and that the output was discarded.

An [Agent SDK callback hook](<https://code.claude.com/docs/en/agent-sdk/hooks>) on `UserPromptSubmit` that reaches its timeout blocks the prompt with a message naming the hook and the timeout, because a callback there can be acting as a policy gate that must not fail open. The session continues. Before v2.1.208, a callback timeout on that event ended the turn with an execution error.

#### UserPromptSubmit input

In addition to the [common input fields](<#common-input-fields>), UserPromptSubmit hooks receive the `prompt` field containing the submitted text. Pasted content that collapsed to a `[Pasted text #N]` placeholder arrives expanded in place. In sessions where Claude Code [marks pasted text for Claude](<https://code.claude.com/docs/en/terminal-config#how-claude-treats-pasted-text>), that expanded content sits between a `<pasted_content id="…">` line and a `</pasted_content id="…">` line, so account for those lines if your hook parses the prompt.

UserPromptSubmit hooks also receive `session_title` when the session has a custom title, with the same meaning as the [SessionStart `session_title` field](<#sessionstart-input>).

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "UserPromptSubmit",
  "prompt": "Write a function to calculate the factorial of a number"
}
```

#### UserPromptSubmit decision control

`UserPromptSubmit` hooks can control whether a submitted prompt is processed and add context. All [JSON output fields](<#json-output>) are available.

There are two ways to add context to the conversation on exit code 0:

* **Plain text stdout**: Claude Code adds stdout it [treats as plain text](<#exit-code-0>) to Claude's context
* **JSON with `additionalContext`**: use the JSON format below for more control. The `additionalContext` field is added as context

Neither channel produces a visible transcript entry. Plain stdout and the `additionalContext` value are each injected as a system reminder that starts with the hook's name; Claude reads both. To confirm delivery, check the [debug log](<#debug-hooks>).

To block a prompt, return a JSON object with `decision` set to `"block"`:

| Field | Description |
| :- | :- |
| `decision` | `"block"` stops the prompt before it reaches Claude. Omit to allow the prompt to proceed |
| `reason` | Shown to the user when `decision` is `"block"`. Not added to context |
| `additionalContext` | String added to Claude's context alongside the submitted prompt. See [Add context for Claude](<#add-context-for-claude>) |
| `sessionTitle` | Sets the session title. Use to name sessions automatically based on the prompt content |
| `suppressOriginalPrompt` | If `true` when the hook blocks the prompt, leaves the prompt text out of the block message. See [What a blocked prompt leaves behind](<#what-a-blocked-prompt-leaves-behind>) |

A hook that blocks by exiting 2 routes the same way as `reason`: the block message shows the stderr text to the user, and it isn't added to context.

```json theme={null}
{
  "decision": "block",
  "reason": "Explanation for decision",
  "hookSpecificOutput": {
    "hookEventName": "UserPromptSubmit",
    "additionalContext": "My additional context here",
    "sessionTitle": "My session title",
    "suppressOriginalPrompt": true
  }
}
```

#### What a blocked prompt leaves behind

A blocked prompt never reaches Claude, but its text isn't removed everywhere. By default the block message shown to the user ends with `Original prompt:` followed by the submitted text, and Claude Code writes that message to the session's transcript file on disk. To leave the text out of the message, print JSON with `"suppressOriginalPrompt": true` inside `hookSpecificOutput`. This works whether the hook blocks with `decision: "block"` or by exiting 2.

`suppressOriginalPrompt` changes only the block message. The submitted text can still appear in local files such as the session transcript and your prompt history, so a blocking hook isn't a way to keep a secret off disk. To limit or remove those files, see [Plaintext storage](<https://code.claude.com/docs/en/claude-directory#plaintext-storage>) and [Clear local data](<https://code.claude.com/docs/en/claude-directory#clear-local-data>).

### UserPromptExpansion

Runs when a user-typed command expands into a prompt before reaching Claude. Use this to block specific commands from direct invocation, inject context for a particular skill, or log which commands users invoke. For example, a hook matching `deploy` can block `/deploy` unless an approval file is present, or a hook matching a review skill can append the team's review checklist as `additionalContext`.

This event covers the path `PreToolUse` doesn't: a `PreToolUse` hook matching the `Skill` tool fires only when Claude calls the tool, but typing `/skillname` directly bypasses `PreToolUse`. `UserPromptExpansion` fires on that direct path.

Matches on `command_name`. Leave the matcher empty to fire on every prompt-type command.

#### UserPromptExpansion input

In addition to the [common input fields](<#common-input-fields>), UserPromptExpansion hooks receive `expansion_type`, `command_name`, `command_args`, `command_source`, and the original `prompt` string. The `expansion_type` field is `slash_command` for skill and custom commands, or `mcp_prompt` for MCP server prompts.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../00893aaf.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "UserPromptExpansion",
  "expansion_type": "slash_command",
  "command_name": "example-skill",
  "command_args": "arg1 arg2",
  "command_source": "plugin",
  "prompt": "/example-skill arg1 arg2"
}
```

#### UserPromptExpansion decision control

`UserPromptExpansion` hooks can block the expansion or add context. All [JSON output fields](<#json-output>) are available.

| Field | Description |
| :- | :- |
| `decision` | `"block"` prevents the command from expanding. Omit to allow it to proceed |
| `reason` | Shown to the user when `decision` is `"block"` |
| `additionalContext` | String added to Claude's context alongside the expanded prompt. See [Add context for Claude](<#add-context-for-claude>) |

A hook that blocks by exiting 2 routes the same way as `reason`: the block message shows the stderr text to the user.

```json theme={null}
{
  "decision": "block",
  "reason": "This slash command is not available",
  "hookSpecificOutput": {
    "hookEventName": "UserPromptExpansion",
    "additionalContext": "Additional context for this expansion"
  }
}
```

### MessageDisplay

Runs while an assistant message streams to the screen. Claude Code displays the message in increments: each time a batch of newly completed lines is ready to render, the hook runs once with those lines and Claude Code renders the hook's replacement text in their place. A long message produces several calls; a short message may produce only one.

Use MessageDisplay to:

* strip markdown for a minimal display
* transform the text an Agent SDK application shows its users
* redact API keys or internal hostnames from Claude's responses

Claude Code holds each batch until your hook returns, so keep the hook fast. If the hook fails or times out, Claude Code displays the original text. The default timeout for this event is 10 seconds; if your hook needs more time, set the `timeout` field in the hook entry.

MessageDisplay is display-only: the replacement text changes only what is rendered on screen. The transcript and what Claude sees keep the original text, so Claude never sees the replacement, and verbose mode shows the original. The hook receives assistant message text only, so tool results and the text you type render unchanged.

MessageDisplay doesn't support matchers and fires for every assistant message that streams text; messages with no text, such as tool-call-only responses, don't trigger it.

In non-interactive runs, including Agent SDK queries and `claude -p`, MessageDisplay runs once per assistant message instead of once per batch of lines. The single call arrives after the message completes and carries the full message text: `index` is `0`, `final` is `true`, and `delta` holds the entire message. A hook that collects the `delta` text for each message receives the same total text in both modes.

#### MessageDisplay input

In addition to the [common input fields](<#common-input-fields>), MessageDisplay hooks receive identifiers for the turn and message, the position of this call within the message, and the new text in `delta`. Batch boundaries depend on how the text streams, so use `index` and `final` to track progress through a message rather than expecting lines to be grouped a particular way.

| Field | Description |
| :- | :- |
| `turn_id` | UUID of the current turn |
| `message_id` | UUID of the assistant message being displayed. Stable across every batch of the same message. This is not the API `msg_…` id, so it can't be correlated with transcript message ids |
| `index` | Zero-based index of this batch within the message |
| `final` | `true` on the message's last batch. Each message has exactly one final batch |
| `delta` | The newly completed lines since the prior batch, terminating newlines included. Always whole lines, except the final batch which may end mid-line. In interactive runs, the final batch's delta is empty when the message ends on a newline, so treat `final`, not a non-empty delta, as the end-of-message signal. In Agent SDK and `claude -p` runs, the single call carries the entire message |

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "MessageDisplay",
  "turn_id": "0c9e6a2f-7d41-4f4e-9a15-3f4f7c2b8d10",
  "message_id": "5b2a9c8e-1f63-4d8a-b7c4-9e0d2a6f1c3b",
  "index": 0,
  "final": false,
  "delta": "Here is the plan:\n"
}
```

#### MessageDisplay output

In addition to the [JSON output fields](<#json-output>) available to all hooks, MessageDisplay hooks can return `displayContent` to replace the delta on screen:

| Field | Description |
| :- | :- |
| `displayContent` | Text displayed in place of the delta. Omit it to display the original |

MessageDisplay hooks have no decision control. They can't block the message or change what is stored in the transcript or sent to Claude. Claude Code acts on `displayContent` from their JSON output and discards `systemMessage` and `continue`.

This example strips markdown formatting from Claude's responses for a plain-text display. The script reads each batch from stdin, removes bold markers and inline code backticks from `delta`, and returns the result as `displayContent`.



  

#### macOS/Linux

    Register a command hook for the event in your settings file:

    ```json theme={null}
    {
      "hooks": {
        "MessageDisplay": [
          {
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/plain-display.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

    Save this script to `.claude/hooks/plain-display.sh` in your project and make it executable with `chmod +x`:

    ```bash theme={null}
    #!/bin/bash
    jq '{hookSpecificOutput: {hookEventName: "MessageDisplay", displayContent: (.delta | gsub("\\*\\*"; "") | gsub("`"; ""))}}'
    ```
  


  

#### Windows (PowerShell)

    Register a command hook that runs the script through PowerShell:

    ```json theme={null}
    {
      "hooks": {
        "MessageDisplay": [
          {
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/plain-display.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    The `-NoProfile` flag skips loading your PowerShell profile so the hook starts fast, and `-ExecutionPolicy Bypass` lets PowerShell run the local script file.

    Save this script to `.claude/hooks/plain-display.ps1` in your project:

    ```powershell theme={null}
    $batch = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $text = $batch.delta -replace '\*\*', '' -replace '`', ''
    @{
      hookSpecificOutput = @{
        hookEventName = "MessageDisplay"
        displayContent = $text
      }
    } | ConvertTo-Json
    ```
  


Batches with no markdown pass through unchanged. If the script fails, for example because `jq` is missing, Claude Code displays the original text and notes the failure only in [debug output](<#debug-hooks>), not in the session.

### PreToolUse

Runs after Claude creates tool parameters and before processing the tool call. Matches on any tool name except `EndConversation`: built-in tools such as `Bash`, `PowerShell`, `Edit`, `Write`, `Read`, `Glob`, `Grep`, `Agent`, `Workflow`, `WebFetch`, `WebSearch`, `AskUserQuestion`, and `ExitPlanMode`, and any [MCP tool names](<#match-mcp-tools>).

To run a hook when a specific file changes on disk, whatever wrote it, use [FileChanged](<#filechanged>) instead of matching file-editing tools by name. Unlike PreToolUse, Claude Code runs FileChanged hooks after the change, and they have no decision control, so they can't block the write.



**注意：**

  PreToolUse runs only when Claude calls a tool. Files you [reference with `@` in your prompt](<https://code.claude.com/docs/en/common-workflows#reference-files-and-directories>) are added without any tool call: Claude Code inserts their contents while building the prompt, so no PreToolUse hook fires for them, including hooks matching `Read`. To block specific paths from `@` references, use a [`Read` deny rule](<https://code.claude.com/docs/en/permissions#read-and-edit>) instead.

  PreToolUse also doesn't fire for [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>).


Use [PreToolUse decision control](<#pretooluse-decision-control>) to allow, deny, ask, or defer the tool call.

An [Agent SDK callback hook](<https://code.claude.com/docs/en/agent-sdk/hooks>) on `PreToolUse` that exceeds its timeout blocks the tool call, and Claude receives an error result naming the timeout. An explicit deny returned by another hook still takes precedence.

#### PreToolUse input

In addition to the [common input fields](<#common-input-fields>), PreToolUse hooks receive `tool_name`, `tool_input`, and `tool_use_id`.

For an [MCP tool](<#match-mcp-tools>), the input also carries `mcp_server`, an object with the server's `name` and a `source` that says where the server's definition came from. The `source` values include `plugin`, `sdk`, and configuration scopes such as `user` and `project`. [`McpServerProvenance`](<https://code.claude.com/docs/en/agent-sdk/typescript#mcpserverprovenance>) in the Agent SDK reference lists them all and says how to treat one you don't recognize. Base trust decisions on `source` rather than on `name` or the `mcp__<server>__` tool-name prefix. The `mcp_server` field requires Claude Code v2.1.274 or later.

For the file tools `Write`, `Edit`, and `Read`, `tool_input.file_path` is always absolute:

* Claude Code expands `~` and relative paths before hooks run, so a hook that matches on paths can't be bypassed via `~` or a relative spelling of the same path
* On Windows, the path arrives with backslash separators, even when your hook runs under Git Bash where `$PWD` looks like `/c/project`
* A comparison written with forward slashes, such as a `/src/` check, never matches a backslash path, and the tool call proceeds as if the hook had nothing to block
* Normalize separators before comparing: `FILE_PATH="${FILE_PATH//\\//}"` in Bash, or `file_path.replace("\\", "/")` in Python, then match a path segment such as `/src/` rather than anchoring with `^`, since the path is absolute

A `Write` call on Windows delivers:

```json theme={null}
{
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "C:\\project\\src\\index.ts",
    "content": "..."
  },
  ...
}
```

The `tool_input` fields depend on the tool:

<a id="bash" />

##### Bash

Executes shell commands.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `command` | string | `"npm test"` | The shell command to execute |
| `description` | string | `"Run test suite"` | Optional description of what the command does |
| `timeout` | number | `120000` | Optional timeout in milliseconds. Values above the [maximum](<https://code.claude.com/docs/en/tools-reference#bash-tool-behavior>) are reduced to the maximum rather than rejected |
| `run_in_background` | boolean | `false` | Whether to run the command in background |

When a Bash command changes files in a Git repository, Claude Code can record what changed. It records the changes in every permission mode when the [`bashEditDiffEnabled`](<https://code.claude.com/docs/en/settings-reference#basheditdiffenabled>) setting turns recording on; that setting's entry says which files can set it. Otherwise it records them only in auto mode and `bypassPermissions` mode, and only when Claude Code directs Claude to edit files through Bash. Set `bashEditDiffEnabled` to `false` to turn the recording off. Background commands and read-only commands carry no diff.

Your [PostToolUse hook](<#posttooluse>) then receives the changed files in `tool_response.bashEditDiff`. The list covers what changed under the repository while the command ran. Files that Git ignores and files in submodules aren't listed. Requires Claude Code v2.1.269 or later.



**注释：**

  The list is best effort and in public beta. Claude Code can miss a change, include a file that another process changed at the same time, or stop at its size limits. The field shape may change. Use the list to find what to review, not to enforce a policy.


`changedFiles` and `files` list what the command changed; the remaining fields say how complete and how reliable that list is.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `changedFiles` | array | `["/path/to/src/app.ts"]` | Absolute paths of the files the command changed, at most 200. Present whenever `files` holds a diff or `moreFiles` is above zero |
| `files` | array | `[{"filePath": "/path/to/src/app.ts", "hunks": [...]}]` | Diffs of up to 5 changed files, for display. `created` or `deleted` is `true` for a file the command added or removed |
| `moreFiles` | number | `2` | Count of changed files with no diff in `files` |
| `unavailable` | boolean | `true` | Set when the diff is incomplete or couldn't be taken |
| `skipped` | boolean | `true` | Set for a Git command that moves the working tree, such as `git checkout` or `git stash`, so Claude Code takes no diff |
| `shared` | boolean | `true` | Set when another Bash tool call, such as a subagent's, ran in the same repository at the same time, so some listed changes may be that command's |

<a id="powershell" />

##### PowerShell

Executes PowerShell commands. See the [PowerShell tool](<https://code.claude.com/docs/en/tools-reference#powershell-tool>) for availability by platform.

The fields match the Bash tool, with the command string in `command`:

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `command` | string | `"Get-ChildItem -Recurse"` | The PowerShell command to execute |
| `description` | string | `"List files recursively"` | Optional description of what the command does |
| `timeout` | number | `120000` | Optional timeout in milliseconds |
| `run_in_background` | boolean | `false` | Whether to run the command in background |

Match `Bash|PowerShell` in hooks that inspect shell commands, so they cover both tools:

* On Windows, wherever the PowerShell tool is enabled, Claude treats PowerShell as the primary shell and routes shell commands through it.
* On Windows without Git Bash, the tool is enabled automatically and Claude Code doesn't register the Bash tool at all.
* A hook that matches only `Bash` never fires there.

##### Write

Creates or overwrites a file.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `file_path` | string | `"/path/to/file.txt"` | Absolute path to the file to write |
| `content` | string | `"file content"` | Content to write to the file |

##### Edit

Replaces a string in an existing file.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `file_path` | string | `"/path/to/file.txt"` | Absolute path to the file to edit |
| `old_string` | string | `"original text"` | Text to find and replace |
| `new_string` | string | `"replacement text"` | Replacement text |
| `replace_all` | boolean | `false` | Whether to replace all occurrences |

##### Read

Reads file contents.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `file_path` | string | `"/path/to/file.txt"` | Absolute path to the file to read |
| `offset` | number | `10` | Optional line number to start reading from |
| `limit` | number | `50` | Optional number of lines to read |

##### Glob

Finds files matching a glob pattern.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `pattern` | string | `"**/*.ts"` | Glob pattern to match files against |
| `path` | string | `"/path/to/dir"` | Optional directory to search in. Defaults to current working directory |

##### Grep

Searches file contents with regular expressions.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `pattern` | string | `"TODO.*fix"` | Regular expression pattern to search for |
| `path` | string | `"/path/to/dir"` | Optional file or directory to search in |
| `glob` | string | `"*.ts"` | Optional glob pattern to filter files |
| `output_mode` | string | `"content"` | `"content"`, `"files_with_matches"`, or `"count"`. Defaults to `"files_with_matches"` |
| `-i` | boolean | `true` | Case insensitive search |
| `multiline` | boolean | `false` | Enable multiline matching |

##### WebFetch

Fetches and processes web content.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `url` | string | `"https://example.com/api"` | URL to fetch content from |
| `prompt` | string | `"Extract the API endpoints"` | Prompt to run on the fetched content |
| `offset` | number | `100000` | Optional number of characters to skip from the start of the page. Claude sets it to keep reading a long page. Requires Claude Code v2.1.290 or later |

##### WebSearch

Searches the web.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `query` | string | `"react hooks best practices"` | Search query |
| `allowed_domains` | array | `["docs.example.com"]` | Optional: only include results from these domains |
| `blocked_domains` | array | `["spam.example.com"]` | Optional: exclude results from these domains |

##### Agent

Spawns a [subagent](<https://code.claude.com/docs/en/sub-agents>).

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `prompt` | string | `"Find all API endpoints"` | The task for the agent to perform |
| `description` | string | `"Find API endpoints"` | Short description of the task |
| `subagent_type` | string | `"Explore"` | Type of specialized agent to use |
| `model` | string | `"sonnet"` | Optional model alias to override the default |

When a foreground Agent call completes, your [PostToolUse hook](<#posttooluse>) receives the subagent's result and run telemetry in `tool_response`. Read these fields to inspect the run; for token and cost rollups across subagents, use the [token and cost counters](<https://code.claude.com/docs/en/monitoring-usage#token-counter>) filtered to `query_source` `"subagent"`, since `totalTokens` and `usage` cover the final request only:

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `status` | string | `"completed"` | `"completed"` for foreground subagents, `"async_launched"` for background subagents. Subagents run in the background by default, so an Agent call that omits `run_in_background` also produces `"async_launched"` |
| `agentId` | string | `"a4d2c8f1e0b3a297"` | Identifier for the subagent run |
| `content` | array | `[{"type": "text", "text": "Found 12 endpoints..."}]` | The subagent's final text blocks, or, for a subagent whose report goes through `SubagentHandback`, a short note about that hand-back in their place |
| `resolvedModel` | string | `"claude-sonnet-4-5"` | Model the subagent started on, which may differ from the requested model |
| `modelsUsed` | array | `["claude-sonnet-4-5", "claude-haiku-4-5"]` | Models used in order, with consecutive repeats collapsed; set only when the model was swapped mid-run. Requires Claude Code v2.1.212 or later |
| `totalTokens` | number | `12450` | Token count from the subagent's final API request: input, output, and cache tokens combined. This isn't a total across the whole run |
| `totalDurationMs` | number | `48211` | Wall-clock duration of the subagent run |
| `totalToolUseCount` | number | `7` | Count of tool calls the subagent made |
| `usage` | object | `{"input_tokens": 8320, ...}` | Per-type token breakdown of the final API request: `input_tokens`, `output_tokens`, `cache_creation_input_tokens`, `cache_read_input_tokens` |

On Claude Code v2.1.271 or later, a subagent that runs with the [`SubagentHandback`](<https://code.claude.com/docs/en/tools-reference>) tool, which Claude Code provides in [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), delivers its report through that tool rather than returning it as text. The `content` field of its `completed` result then carries a short note about that hand-back rather than the report itself. To read the report, match a `PreToolUse` or `PostToolUse` hook on `SubagentHandback` and read `tool_input.message`.

For background subagents, the tool returns when the task moves to the background, so `tool_response` carries no usage fields: a background launch returns immediately, and a foreground task that Claude Code backgrounds mid-run returns at that transition. It has `status: "async_launched"`, `agentId`, `description`, `prompt`, `outputFile`, and `resolvedModel`.

On a `completed` response, `resolvedModel` names the model the subagent started on, which can differ from the `model` value in `tool_input`, such as when `availableModels` or another override applies. On an `async_launched` response, `resolvedModel` names the model in use when the agent moved to the background, so a swap that happened before backgrounding is reflected there. `modelsUsed` and the backgrounding-time `resolvedModel` behavior require Claude Code v2.1.212 or later.

<a id="askuserquestion" />

##### AskUserQuestion

Asks the user one to four multiple-choice questions.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `questions` | array | `[{"question": "Which framework?", "header": "Framework", "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}], "multiSelect": false}]` | Questions to present, each with a `question` string, short `header`, `options` array, and optional `multiSelect` flag |
| `answers` | object | `{"Which framework?": "React"}` | Optional. Maps question text to the selected option label. Multi-select answers join labels with commas. Claude doesn't set this field; supply it via `updatedInput` to answer programmatically |

##### ExitPlanMode

Presents a plan and asks the user to approve it before Claude leaves [plan mode](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>). Claude writes the plan to a file on disk before calling the tool, so the literal `tool_input` from the model is typically empty. Claude Code injects the plan content and file path before passing the input to hooks.

| Field | Type | Example | Description |
| :- | :- | :- | :- |
| `plan` | string | `"## Refactor auth\n1. Extract..."` | Plan content in Markdown. Injected from the plan file on disk |
| `planFilePath` | string | `"/Users/.../plans/refactor-auth.md"` | Path to the plan file. Injected |
| `allowedPrompts` | array | `[{"tool": "Bash", "prompt": "run tests"}]` | Deprecated. Claude Code accepts the field but ignores it. Before v2.1.205, it carried prompt-based permissions Claude requested to implement the plan |

In `PostToolUse`, `tool_response` is an object with `plan` and `filePath` fields holding the approved plan, plus internal status flags. Read `tool_response.plan` for the plan content rather than re-reading the file from disk.

#### PreToolUse decision control

`PreToolUse` hooks can control whether a tool call proceeds. Unlike other hooks that use a top-level `decision` field, PreToolUse returns its decision inside a `hookSpecificOutput` object. This gives it richer control: four outcomes (allow, deny, ask, or defer) plus the ability to modify tool input before execution.

| Field | Description |
| :- | :- |
| `permissionDecision` | `"allow"` skips the permission prompt, except for the [actions no mode auto-approves](<https://code.claude.com/docs/en/permission-modes#actions-no-mode-auto-approves>) and for `AskUserQuestion` and `ExitPlanMode`, which need [`updatedInput` paired with it](<#allow-with-updatedinput>). `"deny"` prevents the tool call. `"ask"` prompts the user to confirm. `"defer"` exits gracefully so the tool can be resumed later. [Deny and ask rules](<https://code.claude.com/docs/en/permissions#manage-permissions>) are still evaluated regardless of what the hook returns |
| `permissionDecisionReason` | For `"ask"`, shown to the user in the permission prompt. When Claude Code [denies the call](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>) in a `-p` run where no one can answer that prompt, Claude reads the reason in the tool result instead. For `"deny"`, shown to Claude. For `"allow"` and `"defer"`, written to the [debug log](<#debug-hooks>) only |
| `updatedInput` | Modifies the tool's input parameters before execution. Replaces the entire input object, so include unchanged fields alongside modified ones. Claude Code evaluates permission rules and a Bash command's [auto-background eligibility](<https://code.claude.com/docs/en/tools-reference#foreground-commands-that-move-to-the-background>) against the input your hook returns, not the input Claude sent. Combine with `"allow"` to auto-approve, or `"ask"` to show the modified input to the user. For `"defer"`, ignored |
| `additionalContext` | String added to Claude's context alongside the tool result. Ignored when `permissionDecision` is `"defer"`. See [Add context for Claude](<#add-context-for-claude>) |

When multiple PreToolUse hooks return different decisions, precedence is `deny` > `defer` > `ask` > `allow`.

A hook that blocks by exiting 2 routes the same way as `"deny"`: Claude sees the stderr message as the denial reason.

When a hook returns `"ask"`, the permission prompt displayed to the user includes a label identifying where the hook came from: `[settings]` for a hook from any settings file or from agent frontmatter, `[plugin:<name>]` for a plugin's hook, or `[skill]` for a hook from skill frontmatter. This helps users understand which configuration source is requesting confirmation.

A hook's `"ask"` also forces a permission prompt in [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>): the classifier can still deny the tool call, but it can't approve the call silently. Before v2.1.211, the classifier could approve a Bash command running outside the [sandbox](<https://code.claude.com/docs/en/sandboxing>) without showing the prompt the hook requested; the classifier still applied its own safety rules to that command, and a hook `"deny"` was always honored.

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "My reason here",
    "updatedInput": {
      "field_to_modify": "new value"
    },
    "additionalContext": "Current environment: production. Proceed with caution."
  }
}
```



**注释：**

  PreToolUse previously used top-level `decision` and `reason` fields, but these are deprecated for this event. Use `hookSpecificOutput.permissionDecision` and `hookSpecificOutput.permissionDecisionReason` instead. The deprecated values `"approve"` and `"block"` map to `"allow"` and `"deny"` respectively. Other events like PostToolUse and Stop continue to use top-level `decision` and `reason` as their current format.


<h4 id="allow-with-updatedinput">
  Tools that require user interaction
</h4>

`AskUserQuestion` and `ExitPlanMode` require user interaction. In [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag, Claude Code offers them only when the run has a [permission host](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>) to receive the prompt, such as an Agent SDK `canUseTool` callback.

A `PreToolUse` hook satisfies that requirement when it does the following:

1. Reads the tool's input from stdin
2. Collects the answer through your own UI
3. Returns `permissionDecision: "allow"` together with `updatedInput` that holds the answer, so the tool runs without prompting

Returning `"allow"` alone is not sufficient for these tools.

For `AskUserQuestion`, echo back the original `questions` array and add an [`answers`](<#askuserquestion>) object mapping each question's text to the chosen answer. This output answers one question with `React`:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "updatedInput": {
      "questions": [
        {
          "question": "Which framework?",
          "header": "Framework",
          "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}],
          "multiSelect": false
        }
      ],
      "answers": {"Which framework?": "React"}
    }
  }
}
```

An MCP tool whose server marks it with [`_meta["anthropic/requiresUserInteraction"]`](<https://code.claude.com/docs/en/mcp#require-approval-for-a-specific-tool>) is stricter: a hook can't skip its approval prompt with `"allow"`, with or without `updatedInput`, because Claude Code can't confirm the hook collected the interaction the tool needs.

#### Defer a tool call for later

`"defer"` is for integrations that run `claude -p` as a subprocess and read its JSON output, such as an Agent SDK app or a custom UI built on top of Claude Code. It lets that calling process pause Claude at a tool call, collect input through its own interface, and resume where it left off. Claude Code honors this value only in [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag. In interactive sessions it logs a warning and ignores the hook result.

The `AskUserQuestion` tool is the typical case: Claude wants to ask the user something, but there is no terminal to answer in. A `-p` run offers `AskUserQuestion` only when it has a [permission host](<https://code.claude.com/docs/en/headless#turn-off-permission-prompts-in-unattended-runs>), such as an MCP tool you pass with `--permission-prompt-tool`, so start the run with one. The round trip works like this:

1. Claude calls `AskUserQuestion`. The `PreToolUse` hook fires.
2. The hook returns `permissionDecision: "defer"`. The tool doesn't execute. The process exits with `stop_reason: "tool_deferred"` and the pending tool call preserved in the transcript.
3. The calling process reads `deferred_tool_use` from the SDK result, surfaces the question in its own UI, and waits for an answer.
4. The calling process runs `claude -p --resume <session-id>` with the same permission host. The same tool call fires `PreToolUse` again.
5. The hook returns `permissionDecision: "allow"` with the answer in `updatedInput`. The tool executes and Claude continues.

The `deferred_tool_use` field carries the tool's `id`, `name`, and `input`. The `input` is the parameters Claude generated for the tool call, captured before execution:

```json theme={null}
{
  "type": "result",
  "subtype": "success",
  "stop_reason": "tool_deferred",
  "session_id": "abc123",
  "deferred_tool_use": {
    "id": "toolu_01abc",
    "name": "AskUserQuestion",
    "input": { "questions": [{ "question": "Which framework?", "header": "Framework", "options": [{"label": "React", "description": "Component library"}, {"label": "Vue", "description": "Progressive framework"}], "multiSelect": false }] }
  }
}
```

There is no timeout or retry limit. The session remains on disk until you resume it, subject to the [`cleanupPeriodDays`](<https://code.claude.com/docs/en/settings-reference#cleanupperioddays>) retention sweep, which deletes session files after 30 days by default, following the [retention sweep rules](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>). If the answer is not ready when you resume, the hook can return `"defer"` again and the process exits the same way. The calling process controls when to break the loop by eventually returning `"allow"` or `"deny"` from the hook.

`"defer"` only works when Claude makes a single tool call in the turn. If Claude makes several tool calls at once, `"defer"` is ignored with a warning and the tool proceeds through the normal permission flow. The constraint exists because resume can only re-run one tool: there is no way to defer one call from a batch without leaving the others unresolved.

If the deferred tool is no longer available when you resume, the process exits with `stop_reason: "tool_deferred_unavailable"` and `is_error: true` before the hook fires. This happens when an MCP server that provided the tool is not connected for the resumed session. The `deferred_tool_use` payload is still included so you can identify which tool went missing.



**注释：**

  To resume a deferred session in plan mode, pass [`--permission-prompt-tool`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) along with `--resume` so that Claude Code can present the plan for approval. If you pass certain other launch flags, the resumed run doesn't return to plan mode; see [Resume in plan mode with `-p`](<https://code.claude.com/docs/en/sessions#resume-in-plan-mode-with-p>). Requires Claude Code v2.1.246 or later.

  When you resume with `-p`, Claude Code doesn't restore any other stored permission mode. It starts the run in the permission mode a new `claude -p` run would start in, so pass `--permission-mode` or `--dangerously-skip-permissions` again if the deferred session used one. When you resume with `claude --resume <session-id>` without `-p`, Claude Code restores the stored permission mode, with the exceptions listed in [permission mode on resume](<https://code.claude.com/docs/en/sessions#permission-mode-on-resume>).


### PermissionRequest

Runs when Claude Code is about to ask you for permission to use a tool. In sessions that can't show a prompt, such as background subagents in [non-interactive mode](<https://code.claude.com/docs/en/headless>), Claude Code still runs these hooks, and if no hook returns a decision, it denies the tool call. For a call that reaches a `--permission-prompt-tool` or the Agent SDK's [`canUseTool` callback](<https://code.claude.com/docs/en/agent-sdk/permissions>), the hooks run alongside your host, and whichever decides first applies.
Use [PermissionRequest decision control](<#permissionrequest-decision-control>) to allow or deny on behalf of the user.

Use this event when you need a signal the moment Claude asks for permission to use a tool. Claude Code runs a [Notification](<#notification>) hook with the `permission_prompt` type only after the prompt has waited about six seconds.

Claude Code doesn't run PermissionRequest hooks for a sandboxed command's [network request](<https://code.claude.com/docs/en/sandboxing#network-isolation>). To get a signal for that prompt, use the `permission_prompt` notification type.

Matches on tool name, same values as PreToolUse.

#### PermissionRequest input

PermissionRequest hooks receive `tool_name` and `tool_input` fields like PreToolUse hooks, but without `tool_use_id`. For an MCP tool, they also receive the [`mcp_server`](<#pretooluse-input>) object. An optional `permission_suggestions` array contains the [permission updates](<#permission-update-entries>) Claude Code suggests for this request, such as adding an allow rule or changing the permission mode.

The `permission_suggestions` array isn't an exact list of the options you see, because each permission dialog builds its own options. Some dialogs, such as the one for file edits, don't read the array at all and derive their options from the request itself. A dialog that does read it can still withhold an option whose suggestion stays in the array, for example when [`allowManagedPermissionRulesOnly`](<https://code.claude.com/docs/en/settings-reference#allowmanagedpermissionrulesonly>) hides rule-saving options. It can also offer options that have no suggestion entry, such as [**Yes, and switch to auto mode**](<https://code.claude.com/docs/en/permission-modes#switch-permission-modes>), which changes the permission mode directly rather than through a permission update.

PreToolUse hooks run before every tool call, whether or not it needs permission. PermissionRequest hooks run only when Claude Code is about to ask you for permission, or when it would otherwise auto-deny a call that can't prompt. Neither event fires for [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>).

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PermissionRequest",
  "tool_name": "Bash",
  "tool_input": {
    "command": "rm -rf node_modules",
    "description": "Remove node_modules directory"
  },
  "permission_suggestions": [
    {
      "type": "addRules",
      "rules": [{ "toolName": "Bash", "ruleContent": "rm -rf node_modules" }],
      "behavior": "allow",
      "destination": "localSettings"
    }
  ]
}
```

#### PermissionRequest decision control

`PermissionRequest` hooks can allow or deny permission requests. In addition to the [JSON output fields](<#json-output>) available to all hooks, your hook script can return a `decision` object with these event-specific fields:

| Field | Description |
| :- | :- |
| `behavior` | `"allow"` grants the permission, `"deny"` denies it. [Deny and ask rules](<https://code.claude.com/docs/en/permissions#manage-permissions>) are still evaluated, so a hook returning `"allow"` doesn't override a matching deny rule |
| `updatedInput` | For `"allow"` only: modifies the tool's input parameters before execution. Replaces the entire input object, so include unchanged fields alongside modified ones. The modified input is re-evaluated against deny and ask rules |
| `updatedPermissions` | For `"allow"` only: array of [permission update entries](<#permission-update-entries>) to apply, such as adding an allow rule or changing the session permission mode |
| `message` | For `"deny"` only: tells Claude why the permission was denied |
| `interrupt` | For `"deny"` only: if `true`, stops Claude |

A hook that exits 2 without a `decision` object leaves the permission flow unchanged, and its stderr is discarded. To grant or deny the request, return the `decision` object.

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionRequest",
    "decision": {
      "behavior": "allow",
      "updatedInput": {
        "command": "npm run lint"
      }
    }
  }
}
```

#### Permission update entries

The `updatedPermissions` output field and the [`permission_suggestions` input field](<#permissionrequest-input>) both use the same array of entry objects. Each entry has a `type` that determines its other fields, and a `destination` that controls where the change is written.

| `type` | Fields | Effect |
| :- | :- | :- |
| `addRules` | `rules`, `behavior`, `destination` | Adds permission rules. `rules` is an array of `{toolName, ruleContent?}` objects. Omit `ruleContent` to match the whole tool. `behavior` is `"allow"`, `"deny"`, or `"ask"` |
| `replaceRules` | `rules`, `behavior`, `destination` | Replaces all rules of the given `behavior` at the `destination` with the provided `rules` |
| `removeRules` | `rules`, `behavior`, `destination` | Removes matching rules of the given `behavior` |
| `setMode` | `mode`, `destination` | Changes the permission mode. Valid modes are `default`, `auto`, `acceptEdits`, `dontAsk`, `bypassPermissions`, `plan`, and `manual` as an alias for `default` |
| `addDirectories` | `directories`, `destination` | Adds working directories. `directories` is an array of path strings |
| `removeDirectories` | `directories`, `destination` | Removes working directories |



**注释：**

  `setMode` with `bypassPermissions` only takes effect if you launched the session with bypass mode already available: `--dangerously-skip-permissions`, `--permission-mode bypassPermissions`, `--allow-dangerously-skip-permissions`, or `permissions.defaultMode: "bypassPermissions"` in [user, `--settings`, or managed settings](<https://code.claude.com/docs/en/settings-reference#permissions-defaultmode>). Otherwise the update is a no-op. The update is also a no-op when [`permissions.disableBypassPermissionsMode`](<https://code.claude.com/docs/en/permissions#managed-settings>) disables the mode, or when the session starts in [restricted mode](<https://code.claude.com/docs/en/cli-reference#cli-flags>).

  `bypassPermissions` is never persisted as `defaultMode` regardless of `destination`.


The `destination` field on every entry determines whether the change stays in memory or persists to a settings file.

| `destination` | Writes to |
| :- | :- |
| `session` | in-memory only, discarded when the session ends |
| `localSettings` | `.claude/settings.local.json` |
| `projectSettings` | `.claude/settings.json` |
| `userSettings` | `~/.claude/settings.json` |

A hook can echo one of the `permission_suggestions` it received as its own `updatedPermissions` output.

### PostToolUse

Runs immediately after a tool completes successfully.

Matches on tool name, same values as PreToolUse.

Match more broadly when the tool name isn't the right filter:

* To run a hook after any tool completes successfully, omit the `matcher` or set it to `"*"`. Your hook can then discover what changed itself, for example by running `git status --porcelain`, which also lists untracked files that `git diff` misses. For tool calls that fail, add the same hook under [PostToolUseFailure](<#posttoolusefailure>).
* To run a hook when a specific file changes on disk, whatever wrote it, use [FileChanged](<#filechanged>). Claude Code doesn't run a `PostToolUse` hook matching `Edit|Write` when a `Bash` command or a process outside Claude Code rewrites the same file.

#### PostToolUse input

`PostToolUse` hooks fire after a tool has already executed successfully. The input includes both `tool_input`, the arguments sent to the tool, and `tool_response`, the result it returned. The exact schema for both depends on the tool. File-tool `tool_input` paths arrive in the same format as for [PreToolUse](<#pretooluse-input>): always absolute, with the platform's native separators, so backslashes on Windows. For an MCP tool, the input also carries the [`mcp_server`](<#pretooluse-input>) object.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file.txt",
    "content": "file content"
  },
  "tool_response": {
    "filePath": "/path/to/file.txt",
    "type": "create"
  },
  "tool_use_id": "toolu_01ABC123...",
  "duration_ms": 12
}
```

| Field | Description |
| :- | :- |
| `duration_ms` | Optional. Tool execution time in milliseconds. Excludes time spent in permission prompts and PreToolUse hooks |

#### PostToolUse decision control

`PostToolUse` hooks can provide feedback to Claude after tool execution. In addition to the [JSON output fields](<#json-output>) available to all hooks, your hook script can return these event-specific fields:

| Field | Description |
| :- | :- |
| `decision` | `"block"` adds the `reason` next to the tool result. Claude still sees the original output; to replace it, use `updatedToolOutput` |
| `reason` | Explanation shown to Claude when `decision` is `"block"` |
| `additionalContext` | String added to Claude's context alongside the tool result. See [Add context for Claude](<#add-context-for-claude>) |
| `classifierContext` | Short note about this call's result for the [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) classifier rather than for Claude. See [Annotate a result for the auto mode classifier](<#annotate-a-result-for-the-auto-mode-classifier>). Requires Claude Code v2.1.236 or later |
| `updatedToolOutput` | Replaces the tool's output with the provided value before it is sent to Claude. The value must match the tool's output shape |
| `updatedMCPToolOutput` | Replaces the output for [MCP tools](<#match-mcp-tools>) only. Prefer `updatedToolOutput`, which works for all tools |

The example below replaces the output of a `Bash` call. The replacement value matches the `Bash` tool's output shape:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "additionalContext": "Additional information for Claude",
    "updatedToolOutput": {
      "stdout": "[redacted]",
      "stderr": "",
      "interrupted": false,
      "isImage": false
    }
  }
}
```



**注意：**

  `updatedToolOutput` only changes what Claude sees. The tool has already run by the time the hook fires, so any files written, commands executed, or network requests sent have already taken effect. Telemetry such as OpenTelemetry tool spans and analytics events also captures the original output before the hook runs. To prevent or modify a tool call before it runs, use a [PreToolUse](<#pretooluse>) hook instead.

  The replacement value must match the tool's output shape. Built-in tools return structured objects rather than plain strings. For example, `Bash` returns an object with `stdout`, `stderr`, `interrupted`, and `isImage` fields. For built-in tools, a value that doesn't match the tool's output schema is ignored and the original output is used. MCP tool output is passed through without schema validation. Stripping error details that Claude needs can cause it to proceed on a false assumption.


#### Annotate a result for the auto mode classifier

Return `classifierContext` to send a short note about the tool call's result to the [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) classifier rather than to Claude. The classifier [never receives tool results themselves](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>), so this field is the supported way to tell it something about what a call returned before it reviews later actions. The field requires Claude Code v2.1.236 or later.

The example below tells the classifier where a query's output came from:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUse",
    "classifierContext": "This query ran against the staging database, not production."
  }
}
```

How much weight the classifier gives the note depends on where you configured the hook:

* **Hooks configured in Claude Code**: for hooks from settings files, plugins, skills, and agent frontmatter, the classifier treats the note as unverified, application-provided context. The note never establishes user intent, and if it claims you approved or requested something, the classifier checks that claim against your own messages in the conversation
* **In-process Agent SDK callbacks**: when an application embedding Claude Code registers the hook as a [TypeScript SDK callback](<https://code.claude.com/docs/en/agent-sdk/hooks>) and returns the note during the live session, the classifier may weigh a user statement relayed in the note as user intent. Such a statement can satisfy a consent requirement the classifier would accept from a message you send, but it never lifts a block that your own message couldn't lift either. After a session resumes, Claude Code treats restored notes as unverified context. When hooks from both groups annotate the same call, the classifier treats the combined note as unverified

Claude Code applies these limits when delivering the note:

* **Length**: Claude Code caps the notes for one tool call at 2,000 characters and truncates the rest. The cap is shared across every hook that responds to that call
* **Synchronous responses only**: Claude Code ignores the field in the response of a hook that [runs in the background](<#run-hooks-in-the-background>), because that response arrives after Claude Code records the tool result
* **Calls that the classifier doesn't record**: the classifier's transcript omits read-only lookups such as file reads and searches. Claude Code discards a note attached to one of those calls
* **Interaction with rewrites**: when the note describes output you're replacing with `updatedToolOutput`, return both fields in the same hook response. Claude Code drops the note if that rewrite is rejected or another hook's rewrite replaces it. Claude Code delivers a note you return without a rewrite even when another hook rewrites the output



**注意：**

  The classifier reads content you place in `classifierContext` as information from the application hosting the session, so don't copy untrusted tool output or third-party text into it. Keep the note to a short assertion about this one call, such as a fact about its origin or a user statement about it; don't use the field to deliver unrelated messages or a stream of events.


### PostToolUseFailure

Runs when a tool that started executing fails: the tool threw an error, or an MCP tool returned an error result. Use this to log failures, send alerts, or provide corrective feedback to Claude.

Matches on tool name, same values as PreToolUse.



**注释：**

  This event doesn't fire for tool calls rejected before execution: an unknown tool name, input that fails schema or tool-specific validation, or a permission denial. Validation rejections are returned as `tool_use_error` results and happen before hooks run, so they fire neither `PreToolUse` nor `PostToolUseFailure`. Permission denials fire `PreToolUse` but not this event; see [PermissionDenied](<#permissiondenied>).


#### PostToolUseFailure input

PostToolUseFailure hooks receive the same `tool_name` and `tool_input` fields as PostToolUse, along with error information as top-level fields. For an MCP tool, they also receive the [`mcp_server`](<#pretooluse-input>) object. For example, a failed `npm test` command might deliver:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolUseFailure",
  "tool_name": "Bash",
  "tool_input": {
    "command": "npm test",
    "description": "Run test suite"
  },
  "tool_use_id": "toolu_01ABC123...",
  "error": "Exit code 1\nError: Cannot find module 'express'",
  "is_interrupt": false,
  "duration_ms": 4187
}
```

| Field | Description |
| :- | :- |
| `error` | String describing what went wrong. The format depends on the tool that failed |
| `is_interrupt` | Optional boolean. True when the failure reached Claude Code as an abort rather than as an error the tool reported. Cancelling a running tool does not fire this hook; the tool result carries the interruption message instead |
| `duration_ms` | Optional. Tool execution time in milliseconds. Excludes time spent in permission prompts and PreToolUse hooks |

The `error` string is generally the same text Claude receives as the failed tool's result. Its format varies by tool and failure. Key your hook on `tool_name`, `is_interrupt`, and the `Exit code N` first line; treat the rest of the string as display text, not a stable format.

* For Bash and PowerShell, a command that ran and exited produces a first line `Exit code N`, then any output the command produced as one block with stdout and stderr interleaved
* A payload may also carry a bare failure message with no exit-code line, when Claude Code could not start the shell process itself
* Claude Code middle-truncates long strings around a `... [N characters truncated] ...` marker, and can insert lines of its own, such as `Command timed out after 2m 0s`

#### PostToolUseFailure decision control

`PostToolUseFailure` hooks can provide context to Claude after a tool failure. In addition to the [JSON output fields](<#json-output>) available to all hooks, your hook script can return these event-specific fields:

| Field | Description |
| :- | :- |
| `additionalContext` | String added to Claude's context alongside the error. See [Add context for Claude](<#add-context-for-claude>) |

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolUseFailure",
    "additionalContext": "Additional information about the failure for Claude"
  }
}
```

### PostToolBatch

Runs once after every tool call in a batch has resolved, before Claude Code sends the next request to the model. `PostToolUse` fires once per tool, which means it fires concurrently when Claude makes parallel tool calls. `PostToolBatch` fires exactly once with the full batch, so it is the right place to inject context that depends on the set of tools that ran rather than on any single tool. There is no matcher for this event.

#### PostToolBatch input

In addition to the [common input fields](<#common-input-fields>), PostToolBatch hooks receive `tool_calls`, an array describing every tool call in the batch:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "PostToolBatch",
  "tool_calls": [
    {
      "tool_name": "Read",
      "tool_input": {"file_path": "/.../ledger/accounts.py"},
      "tool_use_id": "toolu_01...",
      "tool_response": "1\tfrom __future__ import annotations\n2\t..."
    },
    {
      "tool_name": "Read",
      "tool_input": {"file_path": "/.../ledger/transactions.py"},
      "tool_use_id": "toolu_02...",
      "tool_response": "1\tfrom __future__ import annotations\n2\t..."
    }
  ]
}
```

`tool_response` contains the same content the model receives in the corresponding `tool_result` block. The value is a serialized string or content-block array, exactly as the tool emitted it. For `Read`, that means line-number-prefixed text rather than raw file contents. Responses can be large, so parse only the fields you need.



**注释：**

  The `tool_response` shape differs from `PostToolUse`'s. `PostToolUse` passes the tool's structured `Output` object, such as `{filePath: "...", type: "create"}` for `Write`; `PostToolBatch` passes the serialized `tool_result` content the model sees.


#### PostToolBatch decision control

`PostToolBatch` hooks can inject context for Claude. In addition to the [JSON output fields](<#json-output>) available to all hooks, your hook script can return these event-specific fields:

| Field | Description |
| :- | :- |
| `additionalContext` | Context string injected once before the next model call. See [Add context for Claude](<#add-context-for-claude>) for delivery details, what to put in it, and how resumed sessions handle past values |

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PostToolBatch",
    "additionalContext": "These files are part of the ledger module. Run pytest before marking the task complete."
  }
}
```

Returning `decision: "block"` or `continue: false` stops the agentic loop before the next model call. The blocking message comes from the JSON `reason` or `stopReason`, or from stderr on exit 2. You see it as a warning in the transcript, and it stays in the conversation, so Claude sees it when the conversation continues.

### PermissionDenied

Runs when [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) denies a tool call, including when it denies without a classifier verdict because [a safety check separate from auto mode refused the classifier's own request](<https://code.claude.com/docs/en/errors#auto-mode-cannot-determine-the-safety-of-an-action>) or its response didn't parse. This hook only fires in auto mode: it doesn't run when you manually deny a permission dialog, when a `PreToolUse` hook blocks a call, or when a `deny` rule matches. Use it to log denials, adjust configuration, or tell the model it may retry the tool call.

Matches on tool name, same values as PreToolUse.

#### PermissionDenied input

In addition to the [common input fields](<#common-input-fields>), PermissionDenied hooks receive `tool_name`, `tool_input`, `tool_use_id`, and `reason`. For an MCP tool, they also receive the [`mcp_server`](<#pretooluse-input>) object.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "auto",
  "hook_event_name": "PermissionDenied",
  "tool_name": "Bash",
  "tool_input": {
    "command": "rm -rf /tmp/build",
    "description": "Clean build directory"
  },
  "tool_use_id": "toolu_01ABC123...",
  "reason": "[Irreversible Local Destruction]"
}
```

| Field | Description |
| :- | :- |
| `reason` | The denial reason. For a classifier verdict, in most sessions it names the matched rule in square brackets, such as `[Data Exfiltration]`; see [Review denials](<https://code.claude.com/docs/en/auto-mode-config#review-denials>) for the other forms. For a [no-verdict denial](<#permissiondenied-decision-control>), it starts with `Auto mode could not evaluate this action and is blocking it for safety`. For a denial because the classifier model was unavailable, it is the fixed text `Classifier unavailable` |

#### PermissionDenied decision control

PermissionDenied hooks can tell the model it may retry the denied tool call. Return a JSON object with `hookSpecificOutput.retry` set to `true`:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PermissionDenied",
    "retry": true
  }
}
```

When `retry` is `true`, Claude Code adds a message to the conversation telling the model it may retry the tool call. Claude Code doesn't reverse the denial itself. If your hook doesn't return JSON, or returns `retry: false`, the denial stands and the model receives the original rejection message.

Claude Code ignores `retry: true` when the classifier produced [no verdict on the action](<https://code.claude.com/docs/en/errors#auto-mode-cannot-determine-the-safety-of-an-action>): its response didn't parse, or a safety check separate from auto mode refused the classifier's own request. For those denials, Claude Code already tells the model in the rejection message whether to retry later or move on.

### Notification

Runs when Claude Code sends notifications. Matches on notification type. Omit the matcher to run hooks for all notification types.

You receive these hook events even with desktop notifications turned off: the `preferredNotifChannel` setting, including `notifications_disabled`, changes only how you're alerted, not whether your hook runs.

| Matcher | When it fires |
| :- | :- |
| `permission_prompt` | Claude needs you to approve a tool use or a sandboxed command's [network request](<https://code.claude.com/docs/en/sandboxing#network-isolation>), and the prompt has waited about six seconds |
| `idle_prompt` | Claude finished responding about 60 seconds ago and you haven't typed since |
| `auth_success` | Authentication completes |
| `elicitation_dialog` | An MCP server opens an elicitation form and you haven't typed for about six seconds |
| `elicitation_url_dialog` | An MCP server asks you to open a browser URL and you haven't typed for about six seconds |
| `elicitation_complete` | An MCP server reports that a [URL-mode elicitation](<#elicitation-input>) is complete |
| `elicitation_response` | An MCP elicitation response is sent back to the server |
| `agent_needs_input` | A background session starts waiting on your input while [agent view](<https://code.claude.com/docs/en/agent-view>) is open in a terminal. Also fires when a terminal session shows you an [agent team teammate's terminal setup question](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>) or auto mode's notice about [classifier request charges](<https://code.claude.com/docs/en/auto-mode-classifier-billing>) and you haven't typed for about six seconds |
| `agent_completed` | A background session finishes or fails. Fires only while [agent view](<https://code.claude.com/docs/en/agent-view>) is open in a terminal |
| `quota_auto_resume_fired` | Claude Code continues your task after a claude.ai usage limit paused it: at the reset, or sooner when something you do in Claude Code during the wait, such as adding usage credits, upgrading your plan, or switching models, makes usage available again, with the [model-setting exception](<https://code.claude.com/docs/en/interactive-mode#wait-for-a-usage-limit-to-reset>) |
| `quota_auto_resume_stale` | A claude.ai usage limit reset while your computer slept for more than about 30 minutes. Claude Code waits for you to press `Enter` instead of continuing. After a shorter sleep it continues and fires `quota_auto_resume_fired` instead |
| `quota_auto_resume_disabled` | Claude Code ends its wait for a claude.ai usage limit without continuing your task: [`autoContinueAtUsageLimit`](<https://code.claude.com/docs/en/settings-reference#autocontinueatusagelimit>) turned off or the reset moved more than 24 hours away during a wait Claude Code started on its own, the continued task kept hitting the limit, or the continuation was blocked before it reached the model. Doesn't fire when you press `Esc` or `Ctrl+C`, or pick **Don't continue automatically** |

The `quota_auto_resume_fired`, `quota_auto_resume_stale`, and `quota_auto_resume_disabled` types require Claude Code v2.1.234 or later.

In terminal sessions, `permission_prompt` for a sandboxed command's network request requires Claude Code v2.1.246 or later.

`agent_needs_input` for a teammate's terminal setup question requires Claude Code v2.1.248 or later.



**注释：**

  The `permission_prompt`, `idle_prompt`, `elicitation_dialog`, and `elicitation_url_dialog` types share their timing with desktop notifications, so in terminal sessions you only see them when you appear to be away from the terminal:

  * Expect `permission_prompt` once you haven't typed for about six seconds. The timer starts when the permission prompt appears, and each keystroke defers it. To run a hook immediately when Claude asks for permission to use a tool, use [PermissionRequest](<#permissionrequest>) instead.
  * Expect `idle_prompt` about 60 seconds after Claude finishes responding, and only if you haven't typed since and no background agent, such as a background [subagent](<https://code.claude.com/docs/en/sub-agents>), is still running. Claude Code doesn't send `idle_prompt` while it waits for a claude.ai usage limit to reset. When the wait ends on its own, one of the `quota_auto_resume_*` types fires instead.
  * Expect `elicitation_dialog` for an elicitation form, or `elicitation_url_dialog` for a browser URL request, once you haven't typed for about six seconds. Both share the same six-second gate as `permission_prompt`: the timer starts when the dialog appears, and each keystroke defers it.

  A permission request or elicitation that arrives while another dialog is on screen keeps the same six-second gate, timed from when the request arrives. Its notification can reach you while the request still waits behind the open dialog.


Claude Code times `permission_prompt` differently in sessions where it sends permission requests to the Agent SDK's [`canUseTool` callback](<https://code.claude.com/docs/en/agent-sdk/user-input>), which is how Claude Desktop and the VS Code extension host Claude Code:

* Expect `permission_prompt` about six seconds after Claude asks for permission. Claude Code doesn't defer it while you type.
* If you or a [PermissionRequest](<#permissionrequest>) hook answer sooner, Claude Code doesn't run `permission_prompt`.
* Set [`CLAUDE_CODE_DISABLE_PERMISSION_PROMPT_NOTIFY_HOOKS`](<https://code.claude.com/docs/en/env-vars>) to `1` to turn `permission_prompt` off in these sessions.

Before v2.1.233, `permission_prompt` didn't fire in these sessions.

Use separate matchers to run different handlers depending on the notification type. This configuration triggers a permission-specific alert script when Claude needs permission approval and a different notification when Claude has been idle:

```json theme={null}
{
  "hooks": {
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/permission-alert.sh"
          }
        ]
      },
      {
        "matcher": "idle_prompt",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/idle-notification.sh"
          }
        ]
      }
    ]
  }
}
```

#### Notification input

In addition to the [common input fields](<#common-input-fields>), Notification hooks receive `message` with the notification text, an optional `title`, and `notification_type` indicating which type fired.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Notification",
  "message": "Claude needs your permission",
  "title": "Permission needed",
  "notification_type": "permission_prompt"
}
```

Notification hooks can't block or modify notifications. Claude Code discards their `systemMessage` and `continue` fields but still emits [`terminalSequence`](<#emit-terminal-notifications>), which is what the desktop notification example relies on. Notification hooks are intended for side effects such as forwarding the notification to an external service.

### SubagentStart

Runs when Claude spawns a subagent with the Agent tool, when Claude [resumes a subagent](<https://code.claude.com/docs/en/sub-agents#resume-subagents>), and each time an in-process [agent team](<https://code.claude.com/docs/en/agent-teams>) teammate handles a new message. Supports matchers to filter by agent type name. For built-in agents, this is the agent name like `general-purpose`, `Explore`, or `Plan`. For [custom subagents](<https://code.claude.com/docs/en/sub-agents>), this is the `name` field from the agent's frontmatter, not the filename.

For subagents shipped by a [plugin](<https://code.claude.com/docs/en/plugins/overview>), the agent type is the plugin-scoped identifier such as `my-plugin:reviewer`, not the bare frontmatter name. The colon places a plugin-scoped name on the regular-expression path, so anchor the matcher with `^` and `$` for an exact match: `^my-plugin:reviewer$`.

#### SubagentStart input

In addition to the [common input fields](<#common-input-fields>), SubagentStart hooks receive `agent_id` with the unique identifier for the subagent and `agent_type` with the agent name that the matcher filters on.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SubagentStart",
  "agent_id": "agent-abc123",
  "agent_type": "Explore"
}
```

SubagentStart hooks can't block subagent creation, but they can inject context into the subagent. In addition to the [JSON output fields](<#json-output>) available to all hooks, you can return:

| Field | Description |
| :- | :- |
| `additionalContext` | String added to the subagent's context at the start of its conversation, before its first prompt. See [Add context for Claude](<#add-context-for-claude>) |

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "SubagentStart",
    "additionalContext": "Follow security guidelines for this task"
  }
}
```

When the hook runs again for the same subagent, Claude Code injects the returned context only when the subagent's context doesn't already hold the copy from an earlier run. The copy injected at launch stays in place, leaving the subagent's [prompt cache](<https://code.claude.com/docs/en/prompt-caching#subagents-and-the-cache>) intact. After [auto-compaction](<https://code.claude.com/docs/en/sub-agents#auto-compaction>) discards that copy, Claude Code injects the next run's context again.

### SubagentStop

Runs when a Claude Code subagent has finished responding. Matches on agent type, same values as SubagentStart.

#### SubagentStop input

In addition to the [common input fields](<#common-input-fields>), SubagentStop hooks receive `stop_hook_active`, `agent_id`, `agent_type`, `agent_transcript_path`, and `last_assistant_message`. The `agent_type` field is the value used for matcher filtering. The `transcript_path` is the main session's transcript, while `agent_transcript_path` is the subagent's own transcript stored in a nested `subagents/` folder. The `last_assistant_message` field contains the text content of the subagent's final response, so hooks can access it without parsing the transcript file.

Not every SubagentStop event comes from a subagent Claude spawned. Claude Code also runs internal agents for some of its own features, such as [prompt suggestions](<https://code.claude.com/docs/en/interactive-mode#prompt-suggestions>) and [`/btw` side questions](<https://code.claude.com/docs/en/interactive-mode#side-questions-with-%2Fbtw>), and SubagentStop fires when one of those finishes too. For those events, `agent_type` is the agent name the session itself runs as, such as one set with [`--agent`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) or the [`agent` setting](<https://code.claude.com/docs/en/settings-reference#agent>), and an empty string when the session runs without one.

A `matcher` that names agent types doesn't match an empty `agent_type`. A hook whose matcher is omitted, `""`, or `"*"`, or is a regular expression that matches an empty string, runs for events with an empty `agent_type` too.

On Claude Code v2.1.271 or later, a subagent that runs with the [`SubagentHandback`](<https://code.claude.com/docs/en/tools-reference>) tool delivers its report through that tool before it stops. The `last_assistant_message` field then holds the subagent's closing text, if any, which is not the delivered report. The report is that call's `message` input, which a `PreToolUse` or `PostToolUse` hook matched on `SubagentHandback` receives as `tool_input.message`.

SubagentStop hooks also receive the `background_tasks` and `session_crons` arrays described under [Stop input](<#stop-input>). Both arrays are scoped to the parent session, not the subagent.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "~/.claude/projects/.../abc123.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "SubagentStop",
  "stop_hook_active": false,
  "agent_id": "def456",
  "agent_type": "Explore",
  "agent_transcript_path": "~/.claude/projects/.../abc123/subagents/agent-def456.jsonl",
  "last_assistant_message": "Analysis complete. Found 3 potential issues...",
  "background_tasks": [],
  "session_crons": []
}
```

SubagentStop hooks use the same decision control format as [Stop hooks](<#stop-decision-control>), including `hookSpecificOutput.additionalContext` with `hookEventName` set to `"SubagentStop"`, for non-error feedback that keeps the subagent running. Returning `decision: "block"` with a `reason` keeps the subagent running and delivers `reason` to the subagent as its next instruction. A hook that blocks by exiting 2 delivers its stderr message the same way. To inject context into the parent session after a subagent returns, use a [`PostToolUse`](<#posttooluse>) hook on the `Agent` tool instead.

### TaskCreated

Runs when a task is being created via the `TaskCreate` tool. Use this to enforce naming conventions, require task descriptions, or prevent certain tasks from being created. In a [session without the Task tools](<https://code.claude.com/docs/en/tools-reference#task-tool-availability>), this event doesn't fire.

TaskCreated hooks don't support matchers and fire on every occurrence.

#### TaskCreated input

In addition to the [common input fields](<#common-input-fields>), TaskCreated hooks receive `task_id`, `task_subject`, and optionally `task_description`, `teammate_name`, and `team_name`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "TaskCreated",
  "task_id": "task-001",
  "task_subject": "Implement user authentication",
  "task_description": "Add login and signup endpoints",
  "teammate_name": "implementer",
  "team_name": "session-a1b2c3d4"
}
```

| Field | Description |
| :- | :- |
| `task_id` | Identifier of the task being created |
| `task_subject` | Title of the task |
| `task_description` | Detailed description of the task. May be absent |
| `teammate_name` | Name of the teammate creating the task. May be absent |
| `team_name` | Deprecated. Session-derived team name; will be removed in a future release |
| `agent_id` | On this event, the [common input field](<#common-input-fields>) identifies the subagent or [in-process teammate](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>) creating the task. May be absent. Requires Claude Code v2.1.290 or later |

#### TaskCreated decision control

A TaskCreated hook can block the creation with exit code 2 or with a JSON decision. Either way, Claude Code deletes the task and returns your message to Claude as the tool's error. Claude Code ignores `continue: false` from this event and Claude keeps working.

* **Exit code 2**: Claude Code returns the stderr text as the message.
* **JSON `{"decision": "block", "reason": "..."}`**: Claude Code returns `reason` as the message.

This example blocks tasks whose subjects don't follow the required format:

```bash theme={null}
#!/bin/bash
INPUT=$(cat)
TASK_SUBJECT=$(echo "$INPUT" | jq -r '.task_subject')

if [[ ! "$TASK_SUBJECT" =~ ^\[TICKET-[0-9]+\] ]]; then
  echo "Task subject must start with a ticket number, e.g. '[TICKET-123] Add feature'" >&2
  exit 2
fi

exit 0
```

### TaskCompleted

Runs when a task is being marked as completed. This fires in two situations: when any agent explicitly marks a task as completed through the TaskUpdate tool, or when an [agent team](<https://code.claude.com/docs/en/agent-teams>) teammate finishes its turn with in-progress tasks. Use this to enforce completion criteria like passing tests or lint checks before a task can close.

TaskCompleted hooks don't support matchers and fire on every occurrence.

#### TaskCompleted input

In addition to the [common input fields](<#common-input-fields>), TaskCompleted hooks receive `task_id`, `task_subject`, and optionally `task_description`, `teammate_name`, and `team_name`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "TaskCompleted",
  "task_id": "task-001",
  "task_subject": "Implement user authentication",
  "task_description": "Add login and signup endpoints",
  "teammate_name": "implementer",
  "team_name": "session-a1b2c3d4"
}
```

| Field | Description |
| :- | :- |
| `task_id` | Identifier of the task being completed |
| `task_subject` | Title of the task |
| `task_description` | Detailed description of the task. May be absent |
| `teammate_name` | Name of the teammate completing the task. May be absent |
| `team_name` | Deprecated. Session-derived team name; will be removed in a future release |
| `agent_id` | On this event, the [common input field](<#common-input-fields>) identifies the subagent or [in-process teammate](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>) completing the task. May be absent. Requires Claude Code v2.1.290 or later |

#### TaskCompleted decision control

TaskCompleted hooks support two ways to control task completion:

* **Exit code 2**: the task is not marked as completed and the stderr message is fed back to the model as feedback.
* **JSON `{"continue": false, "stopReason": "..."}`**: when a teammate finishing its turn triggered the event, stops the teammate entirely, matching `Stop` hook behavior. The `stopReason` is shown to the user. When the `TaskUpdate` tool triggered the event, Claude Code ignores `continue: false`; exit code 2 still blocks the completion.

This example runs tests and blocks task completion if they fail:

```bash theme={null}
#!/bin/bash
INPUT=$(cat)
TASK_SUBJECT=$(echo "$INPUT" | jq -r '.task_subject')

# Run the test suite
if ! npm test 2>&1; then
  echo "Tests not passing. Fix failing tests before completing: $TASK_SUBJECT" >&2
  exit 2
fi

exit 0
```

### Stop

Runs when the main Claude Code agent has finished responding. Does not run if
the stoppage occurred due to a user interrupt. API errors fire
[StopFailure](<#stopfailure>) instead.



**提示：**

  The [`/goal`](<https://code.claude.com/docs/en/goal>) command is a built-in shortcut for a session-scoped prompt-based Stop hook. Use it when you want Claude to keep working toward a condition without writing hook configuration.


#### Stop input

In addition to the [common input fields](<#common-input-fields>), Stop hooks receive `stop_hook_active`, `last_assistant_message`, `background_tasks`, and `session_crons`. The `stop_hook_active` field is `true` when Claude Code is already continuing as a result of a stop hook. Check this value or process the transcript to avoid blocking on a condition that will never resolve.

Claude Code applies an 8-consecutive-continuation cap: after stop hooks have continued the turn eight times in a row, Claude Code overrides the next block and ends the turn. The count of consecutive continuations resets each time Claude calls a tool. To raise the cap, set [`CLAUDE_CODE_STOP_HOOK_BLOCK_CAP`](<https://code.claude.com/docs/en/env-vars>).

The `last_assistant_message` field contains the text content of Claude's final response, so hooks can access it without parsing the transcript file. For hooks that act on the just-completed turn, such as read-aloud or notification hooks, use this field rather than reading `transcript_path`: the transcript file isn't guaranteed to include the final message at Stop time on all versions.

The `background_tasks` and `session_crons` arrays let hooks distinguish "session is done" from "session is paused waiting for background work to wake it back up". Both arrays are present when the task registry is reachable and are empty when nothing is in flight or scheduled.

Each entry in `background_tasks` describes one in-flight task and uses these fields:

| Field | Description |
| :- | :- |
| `id` | Task identifier |
| `type` | Friendly task-type label such as `shell`, `subagent`, `monitor`, `workflow`, `teammate`, `cloud session`, or `MCP task`. Each label identifies which Claude Code feature created the task. Falls back to the raw discriminant for unrecognized types |
| `status` | Current task status |
| `description` | Free-text description, capped at 1000 characters with an in-string `… [+N chars]` marker when clipped |
| `command` | Shell command line, capped at 1000 characters. Present only for `shell` tasks |
| `agent_type` | Subagent type name. Present only for `subagent` tasks |
| `server` | MCP server name. Present only for `monitor` and `MCP task` tasks |
| `tool` | MCP tool name. Present only for `monitor` and `MCP task` tasks |
| `name` | Workflow name. Present only for `workflow` tasks |

Each entry in `session_crons` describes one session-scoped scheduled wakeup, sourced from `CronCreate`, `ScheduleWakeup`, and `/loop`:

| Field | Description |
| :- | :- |
| `id` | Cron task identifier |
| `schedule` | Cron expression, for example `0 9 * * 1-5` |
| `recurring` | `false` for one-shot wakeups whose schedule encodes a single fire time, `true` for tasks that re-fire on every match |
| `prompt` | Prompt submitted when the cron fires, capped at 1000 characters with the same `… [+N chars]` marker |

This example shows a Stop input with one in-flight shell task and one recurring cron:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "~/.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "Stop",
  "stop_hook_active": true,
  "last_assistant_message": "I've completed the refactoring. Here's a summary...",
  "background_tasks": [
    {
      "id": "task-001",
      "type": "shell",
      "status": "running",
      "description": "tail logs",
      "command": "tail -f /var/log/syslog"
    }
  ],
  "session_crons": [
    {
      "id": "cron-001",
      "schedule": "0 9 * * 1-5",
      "recurring": true,
      "prompt": "check the build"
    }
  ]
}
```

#### Stop decision control

`Stop` and `SubagentStop` hooks can control whether Claude continues. In addition to the [JSON output fields](<#json-output>) available to all hooks, your hook script can return these event-specific fields:

| Field | Description |
| :- | :- |
| `decision` | `"block"` prevents Claude from stopping. Omit to allow Claude to stop |
| `reason` | Required when `decision` is `"block"`. Tells Claude why it should continue |
| `hookSpecificOutput.additionalContext` | Non-error feedback for Claude. The conversation continues so Claude can act on it, but unlike `decision: "block"` it is shown in the transcript as hook feedback rather than a hook error |

A hook that blocks by exiting 2 routes the same way as `reason`: Claude receives the stderr message as the explanation for why it should continue.

```json theme={null}
{
  "decision": "block",
  "reason": "Must be provided when Claude is blocked from stopping"
}
```

Use `additionalContext` when the hook is working as designed and giving Claude guidance, such as "run the test suite before finishing". It keeps the conversation going through the same loop protections as `decision: "block"`, namely the `stop_hook_active` input and the 8-consecutive-continuation cap, but the transcript labels it `Stop hook feedback` and no hook error notification is shown:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Stop",
    "additionalContext": "Please run the test suite before finishing"
  }
}
```

### StopFailure

Runs instead of [Stop](<#stop>) when the turn ends due to an API error. Claude Code ignores the hook's output and exit code, apart from [`terminalSequence`](<#emit-terminal-notifications>). Use this to log failures, send alerts, or take recovery actions when Claude can't complete a response due to rate limits, authentication problems, or other API errors.

#### StopFailure input

In addition to the [common input fields](<#common-input-fields>), StopFailure hooks receive `error`, optional `error_details`, and optional `last_assistant_message`. The `error` field identifies the error type and is used for matcher filtering.

| Field | Description |
| :- | :- |
| `error` | Error type: `rate_limit`, `overloaded`, `authentication_failed`, `oauth_org_not_allowed`, `account_on_hold`, `billing_error`, `invalid_request`, `model_not_found`, `server_error`, `max_output_tokens`, `cloud_credential_error`, or `unknown` |
| `error_details` | Additional details about the error, when available |
| `last_assistant_message` | The rendered error text shown in the conversation. Unlike `Stop` and `SubagentStop`, where this field holds Claude's conversational output, for `StopFailure` it contains the API error string itself, such as `"API Error: Rate limit reached"` |

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "StopFailure",
  "error": "rate_limit",
  "error_details": "429 Too Many Requests",
  "last_assistant_message": "API Error: Rate limit reached"
}
```

StopFailure hooks have no decision control. They run for notification and logging purposes only.

### TeammateIdle

Runs when an [agent team](<https://code.claude.com/docs/en/agent-teams>) teammate is about to go idle after finishing its turn. Use this to enforce quality gates before a teammate stops working, such as requiring passing lint checks or verifying that output files exist.

TeammateIdle hooks don't support matchers and fire on every occurrence.

#### TeammateIdle input

In addition to the [common input fields](<#common-input-fields>), TeammateIdle hooks receive `teammate_name` and `team_name`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "permission_mode": "default",
  "hook_event_name": "TeammateIdle",
  "teammate_name": "researcher",
  "team_name": "session-a1b2c3d4"
}
```

| Field | Description |
| :- | :- |
| `teammate_name` | Name of the teammate that is about to go idle |
| `team_name` | Deprecated. Session-derived team name; will be removed in a future release |
| `agent_id` | On this event, the [common input field](<#common-input-fields>) identifies the [in-process teammate](<https://code.claude.com/docs/en/agent-teams#choose-a-display-mode>) that is about to go idle. May be absent. Requires Claude Code v2.1.290 or later |

#### TeammateIdle decision control

TeammateIdle hooks support two ways to control teammate behavior:

* **Exit code 2**: the teammate receives the stderr message as feedback and continues working instead of going idle.
* **JSON `{"continue": false, "stopReason": "..."}`**: stops the teammate entirely, matching `Stop` hook behavior. The `stopReason` is shown to the user.

This example checks that a build artifact exists before allowing a teammate to go idle:

```bash theme={null}
#!/bin/bash

if [ ! -f "./dist/output.js" ]; then
  echo "Build artifact missing. Run the build before stopping." >&2
  exit 2
fi

exit 0
```

### ConfigChange

Runs when a configuration file changes during a session. Use this to audit settings changes, enforce security policies, or block unauthorized modifications to configuration files.

Claude Code runs ConfigChange hooks when a settings file, a managed policy file, or a skill file changes. For managed policy, it runs them only when `managed-settings.json` or a file in `managed-settings.d/` changes. It applies [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>) and changes to macOS managed preferences or Windows registry policy without running them. On WSL with [`wslInheritsWindowsSettings`](<https://code.claude.com/docs/en/settings-reference#wslinheritswindowssettings>), it also applies a changed Windows-side managed settings file on its policy poll without running them.

The matcher filters on the configuration source:

| Matcher | When it fires |
| :- | :- |
| `user_settings` | `~/.claude/settings.json` changes |
| `project_settings` | `.claude/settings.json` changes |
| `local_settings` | `.claude/settings.local.json` changes |
| `policy_settings` | `managed-settings.json` or a file in `managed-settings.d/` changes |
| `skills` | A skill file in `.claude/skills/` changes |

This example logs all configuration changes for security auditing:

```json theme={null}
{
  "hooks": {
    "ConfigChange": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/audit-config-change.sh",
            "args": []
          }
        ]
      }
    ]
  }
}
```

#### ConfigChange input

In addition to the [common input fields](<#common-input-fields>), ConfigChange hooks receive `source` and optionally `file_path`. The `source` field indicates which configuration type changed, and `file_path` provides the path to the specific file that was modified.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "ConfigChange",
  "source": "project_settings",
  "file_path": "/Users/.../my-project/.claude/settings.json"
}
```

#### ConfigChange decision control

ConfigChange hooks can block configuration changes from taking effect. Use exit code 2 or a JSON `decision` to prevent the change. When blocked, the new settings are not applied to the running session.

| Field | Description |
| :- | :- |
| `decision` | `"block"` prevents the configuration change from being applied. Omit to allow the change |
| `reason` | Accepted but never shown |

```json theme={null}
{
  "decision": "block",
  "reason": "Configuration changes to project settings require admin approval"
}
```

`policy_settings` changes can't be blocked. Hooks still fire for `policy_settings` sources when a managed settings file on the machine changes, so you can use them to log those edits, but any blocking decision is ignored. This ensures enterprise-managed settings always take effect. Claude Code doesn't run `ConfigChange` hooks when [server-managed settings](<https://code.claude.com/docs/en/server-managed-settings>) arrive or refresh.

Claude Code acts on the blocking decision from a ConfigChange hook's JSON output and discards `systemMessage` and `continue`. A blocked change surfaces no message to you or to Claude, whether you block with `reason` or with stderr on exit 2. Claude Code only writes a line to the debug log.

### CwdChanged

Runs when a shell command in the main conversation changes the working directory, for example when Claude executes a `cd` command. Use this to react to directory changes: reload environment variables, activate project-specific toolchains, or run setup scripts automatically. Pairs with [FileChanged](<#filechanged>) for tools like [direnv](<https://direnv.net/>) that manage per-directory environment.

CwdChanged hooks have access to [`CLAUDE_ENV_FILE`](<#persist-environment-variables>). Variables written to that file persist into subsequent Bash commands until the next CwdChanged event, when Claude Code clears them.

CwdChanged doesn't support matchers and fires on every occurrence.

#### CwdChanged input

In addition to the [common input fields](<#common-input-fields>), CwdChanged hooks receive `old_cwd` and `new_cwd`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project/src",
  "hook_event_name": "CwdChanged",
  "old_cwd": "/Users/my-project",
  "new_cwd": "/Users/my-project/src"
}
```

#### CwdChanged output

In addition to the [JSON output fields](<#json-output>) available to all hooks, CwdChanged hooks can return `watchPaths` to dynamically set which file paths [FileChanged](<#filechanged>) watches:

| Field | Description |
| :- | :- |
| `watchPaths` | Array of absolute paths. Replaces the current dynamic watch list. Paths from your `matcher` configuration are always watched. Returning an empty array clears the dynamic list, which is typical when entering a new directory |

CwdChanged hooks have no decision control. They can't block the directory change.

Claude Code reads `watchPaths` and `systemMessage` from their JSON output and discards `continue`. In interactive sessions, it shows the `systemMessage` as a brief terminal notification. The message doesn't reach the SDK message stream.

### DirectoryAdded

Runs after you add a working directory mid-session with the `/add-dir` command, or after an SDK client adds one with the `register_repo_root` control request. Use this to prepare a newly added repository, for example by installing its dependencies.

Claude Code doesn't fire this event when:

* You pass a directory with the `--add-dir` startup flag; [SessionStart](<#sessionstart>) covers those directories
* You add a directory on the `/permissions` Workspace tab
* You add a directory that is already a working directory or inside one

Claude Code fires DirectoryAdded after refreshing sandbox and permission state, so sandboxed tools already see the new directory when your hook runs. Hook commands themselves run unsandboxed.

Claude Code doesn't wait for the hook: the add completes immediately, and the hook runs in the background with the 600-second default timeout.

The matcher filters on how the directory was added:

| Matcher | When it fires |
| :- | :- |
| `slash_command` | You add a directory with `/add-dir` |
| `register_repo_root` | An SDK client adds a directory with the `register_repo_root` control request |

#### DirectoryAdded input

In addition to the [common input fields](<#common-input-fields>), DirectoryAdded hooks receive `directory` and `source`.

| Field | Description |
| :- | :- |
| `directory` | Absolute path of the directory that was added |
| `source` | How the directory was added, `"slash_command"` for `/add-dir` or `"register_repo_root"` for the SDK control request |

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "DirectoryAdded",
  "directory": "/Users/my-other-repo",
  "source": "slash_command"
}
```

DirectoryAdded hooks have no decision control. They can't block the add, which has already completed when the hook runs. Claude Code discards the `continue` field from their JSON output and surfaces the rest differently per source:

* `slash_command`: Claude Code delivers the hook's `systemMessage` to Claude as context on the next conversation turn, rather than showing it to you. A count of failed hooks appears in the transcript. Full failure output goes to the debug log
* `register_repo_root`: Claude Code writes `systemMessage` output and failure output to the debug log only

### FileChanged

Runs when a watched file changes on disk. Claude Code detects changes with a filesystem watcher, not by inspecting tool calls, so it runs the hook no matter what changed the file: an `Edit` or `Write` tool call, a script Claude runs with `Bash`, or a process outside Claude Code entirely. A common use is reloading environment variables when project configuration files change.

The `matcher` for this event serves two roles:

* **Build the watch list**: the value is split on `|` and each segment is registered as a literal filename in the working directory, so `".envrc|.env"` watches exactly those two files. Regex patterns are not useful here: a value like `^\.env` would watch a file literally named `^\.env`.
* **Filter which hooks run**: when a watched file changes, the same value filters which hook groups run using the standard [matcher rules](<#matcher-patterns>) against the changed file's basename.

This example normalizes line endings in `data.csv` after any change, including a `Bash` command or an external script rewriting the file:

```json theme={null}
{
  "hooks": {
    "FileChanged": [
      {
        "matcher": "data.csv",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/normalize-line-endings.sh"
          }
        ]
      }
    ]
  }
}
```

The hook reads the changed file's absolute path from the `file_path` field of the [JSON input](<#filechanged-input>) on stdin. Its `grep` guard tests for the same thing `perl` removes, a CR at the end of a line, so the run after a normalization exits without touching the file. A looser guard loops forever, because `perl -i` rewrites the file even when it substitutes nothing and Claude Code runs the hook again after every rewrite. Save this script at `/path/to/normalize-line-endings.sh` and make it executable:

```bash theme={null}
#!/bin/bash
FILE=$(jq -r .file_path)
if grep -q $'\r$' "$FILE"; then
  perl -pi -e 's/\r$//' "$FILE"
fi
```

To confirm the hook works, ask Claude to append a CRLF line to `data.csv` with a `Bash` command. Claude Code runs the hook and the file ends up with LF endings.

To watch files you can't name up front, return [`watchPaths`](<#filechanged-output>) from a hook to update the watch list dynamically. Claude Code starts the watcher only when something names a file to watch, so seed the list with a FileChanged group whose matcher names at least one file, or with a [SessionStart](<#sessionstart-decision-control>) or [CwdChanged](<#cwdchanged>) hook that returns `watchPaths`. The matcher still filters which hook groups run when a watched file changes, so give the group that handles dynamic paths an omitted matcher, which matches every watched file and adds nothing to the watch list. A `"*"` matcher also matches every file, but Claude Code registers it in the watch list like any other value, as a literal file named `*`.

FileChanged hooks have access to [`CLAUDE_ENV_FILE`](<#persist-environment-variables>). Variables written to that file persist into subsequent Bash commands until the next [CwdChanged](<#cwdchanged>) event, when Claude Code clears them.

#### FileChanged input

In addition to the [common input fields](<#common-input-fields>), FileChanged hooks receive `file_path` and `event`.

| Field | Description |
| :- | :- |
| `file_path` | Absolute path to the file that changed |
| `event` | What happened: `"change"` for a modified file, `"add"` for a created file, or `"unlink"` for a deleted file |

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../transcript.jsonl",
  "cwd": "/Users/my-project",
  "hook_event_name": "FileChanged",
  "file_path": "/Users/my-project/.envrc",
  "event": "change"
}
```

#### FileChanged output

In addition to the [JSON output fields](<#json-output>) available to all hooks, FileChanged hooks can return `watchPaths` to dynamically update which file paths are watched:

| Field | Description |
| :- | :- |
| `watchPaths` | Array of absolute paths. Replaces the current dynamic watch list. Paths from your `matcher` configuration are always watched. Use this when your hook script discovers additional files to watch based on the changed file |

FileChanged hooks have no decision control. They can't block the file change from occurring.

Claude Code reads `watchPaths` and `systemMessage` from their JSON output and discards `continue`. In interactive sessions, it shows the `systemMessage` as a brief terminal notification. The message doesn't reach the SDK message stream.

### WorktreeCreate

Runs when a worktree is being created, whether from `claude --worktree`, from a [subagent using `isolation: "worktree"`](<https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope>), or for a [background session](<https://code.claude.com/docs/en/agent-view#how-file-edits-are-isolated>) that Claude Code isolates in its own worktree. By default Claude Code creates the isolated working copy with `git worktree`. Configuring a WorktreeCreate hook replaces that default git behavior, letting you use a different version control system like SVN, Perforce, or Mercurial.

Because the hook replaces the default behavior entirely, [`.worktreeinclude`](<https://code.claude.com/docs/en/worktrees#copy-gitignored-files-into-worktrees>) is not processed. If you need to copy local configuration files like `.env` into the new worktree, do it inside your hook script.

The hook must return the path to the created worktree directory. Claude Code uses this path as the working directory for the isolated session. See [WorktreeCreate output](<#worktreecreate-output>) for how each hook type returns the path.

Claude Code acts on the hook's success and the returned path, and discards `systemMessage` and `continue`.

This example creates an SVN working copy and prints the path for Claude Code to use. Replace the repository URL with your own:

```json theme={null}
{
  "hooks": {
    "WorktreeCreate": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'NAME=$(jq -r .name); DIR=\"$HOME/.claude/worktrees/$NAME\"; svn checkout https://svn.example.com/repo/trunk \"$DIR\" >&2 && echo \"$DIR\"'"
          }
        ]
      }
    ]
  }
}
```

The hook reads the worktree `name` from the JSON input on stdin, checks out a fresh copy into a new directory, and prints the directory path. The `echo` on the last line is what Claude Code reads as the worktree path. Redirect any other output to stderr so it doesn't interfere with the path.

#### WorktreeCreate input

In addition to the [common input fields](<#common-input-fields>), WorktreeCreate hooks receive the `name` field. This is a slug identifier for the new worktree, either specified by the user or auto-generated, for example `bold-oak-a3f2`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "WorktreeCreate",
  "name": "feature-auth"
}
```

#### WorktreeCreate output

WorktreeCreate hooks don't use the standard allow/block decision model. Instead, the hook's success or failure determines the outcome. The hook must return the path to the created worktree directory:

* **Command hooks** (`type: "command"`): print the path as the last non-empty line of stdout. Claude Code strips ANSI escape codes before reading that line, so shell startup banners printed before your `echo` are ignored. Redirect any other hook output to stderr.
* **HTTP hooks** (`type: "http"`): return `{ "hookSpecificOutput": { "hookEventName": "WorktreeCreate", "worktreePath": "/absolute/path" } }` in the response body.

If the hook fails or produces no path, worktree creation fails with an error.

Claude Code resolves a relative path against the directory the hook ran in, collapsing any `.` or `..` segments in it. If the resulting path isn't a directory Claude Code can enter, the session prints an error naming the path and exits with code 1.

Claude Code refuses an absolute path that contains `.` or `..` segments, and any path that passes through a symlink below the repository root, because a symlink committed to the repository could redirect the worktree outside it. The error names the rejected component. Return a normalized path that doesn't pass through a symlink inside the repository. Before v2.1.216, worktree creation followed the hook's path without this screening.

### WorktreeRemove

Runs when Claude Code cleans up a worktree that your [`WorktreeCreate`](<#worktreecreate>) hook created. The event fires when:

* You exit an interactive [worktree session](<https://code.claude.com/docs/en/worktrees#start-claude-in-a-worktree>) and choose to remove the worktree when Claude Code prompts you
* You exit an interactive worktree session you haven't [named](<https://code.claude.com/docs/en/sessions#name-your-sessions>), Claude Code finds no changed or untracked files, and it removes the worktree without prompting you
* You delete a [background session](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>) that runs in the worktree

Claude Code uses git to look for changed or untracked files, so it finds none in a worktree that isn't a git checkout or inside one, even when the directory holds uncommitted work. Check for that work in your WorktreeRemove hook before it deletes anything.

For git-based worktrees, Claude Code handles cleanup automatically with `git worktree remove`. If you configured a WorktreeCreate hook, pair it with a WorktreeRemove hook to control cleanup of the worktrees it creates:

* **No WorktreeRemove hook**: when Claude Code removes the worktree as you exit a worktree session, it falls back to `git worktree remove --force` on the path your WorktreeCreate hook returned, so a worktree git recognizes is removed. A worktree git doesn't recognize, for example one your hook created with a non-git version control system, stays on disk. For what deleting a [background session](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>) does with a hook-created worktree, see agent view's delete rules.
* **Hook exits 0**: the worktree counts as removed. Claude Code reads nothing else from the hook, so make sure your hook deleted the directory.
* **Hook exits non-zero**: the removal fails if the directory at `worktree_path` still exists afterward, and the worktree stays on disk with no git fallback. A hook that deleted the directory before exiting non-zero counts as removed. For how the failure is reported, see [WorktreeRemove input](<#worktreeremove-input>).

Claude Code never deletes a branch belonging to a hook-created worktree, because it only knows the path your WorktreeCreate hook returned. If your WorktreeCreate hook creates a branch, delete it in your WorktreeRemove hook.

Claude Code discards a WorktreeRemove hook's [JSON output fields](<#json-output>), such as `systemMessage` and `continue`.

For a background-session delete, Claude Code verifies the stored worktree path before running the hook and refuses a path that is a symlink or passes through one below the repository root. The hook runs for a worktree that still contains files only when you confirm the delete in [agent view](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>); for such a worktree, [`claude rm`](<https://code.claude.com/docs/en/agent-view#manage-sessions-from-the-shell>) keeps the session and worktree instead. Before v2.1.216, the hook ran on the stored path without these checks.

Claude Code passes the path returned by WorktreeCreate as `worktree_path` in the hook input. This example reads that path and removes the directory:

```json theme={null}
{
  "hooks": {
    "WorktreeRemove": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'jq -r .worktree_path | xargs rm -rf'"
          }
        ]
      }
    ]
  }
}
```

#### WorktreeRemove input

In addition to the [common input fields](<#common-input-fields>), WorktreeRemove hooks receive the `worktree_path` field, which is the absolute path to the worktree being removed.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "WorktreeRemove",
  "worktree_path": "/Users/.../my-project/.claude/worktrees/feature-auth"
}
```

A WorktreeRemove hook's exit code decides the outcome. When a hook exits non-zero and the directory at `worktree_path` still exists afterward, the removal fails:

* The worktree stays on disk, and the hook's command and stderr go to the [debug log](<#debug-hooks>).
* If you were deleting a background session, the session stays too. The refusal message in [agent view](<https://code.claude.com/docs/en/agent-view#what-deleting-a-session-removes>) reports how the hook ended, such as `exited 1`, quotes the start of its stderr, and says whether deleting the session again removes the directory anyway.

### PreCompact

Runs before Claude Code is about to run a compact operation.

The matcher value indicates whether compaction was triggered manually or automatically:

| Matcher | When it fires |
| :- | :- |
| `manual` | `/compact` |
| `auto` | Auto-compact when the conversation reaches the [auto-compact window](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) |

Exit with code 2 to block compaction. For a manual `/compact`, the stderr message is shown to the user. You can also block by returning JSON with `"decision": "block"`.

Blocking automatic compaction has different effects depending on when it fires. If compaction was triggered proactively before the context limit, Claude Code skips it and the conversation continues uncompacted. If compaction was triggered to recover from a context-limit error already returned by the API, the underlying error surfaces and the current request fails.

Claude Code discards a PreCompact hook's `systemMessage` and `continue` fields.

#### PreCompact input

In addition to the [common input fields](<#common-input-fields>), PreCompact hooks receive `trigger` and `custom_instructions`. For `manual`, `custom_instructions` contains what the user passes into `/compact` and is `null` when they pass nothing. For `auto`, `custom_instructions` is `null`.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PreCompact",
  "trigger": "manual",
  "custom_instructions": null
}
```

### PostCompact

Runs after Claude Code completes a compact operation. Use this event to react to the new compacted state, for example to log the generated summary or update external state. Claude Code discards a PostCompact hook's `systemMessage` and `continue` fields.

The same matcher values apply as for `PreCompact`:

| Matcher | When it fires |
| :- | :- |
| `manual` | After `/compact` |
| `auto` | After auto-compact when the conversation reaches the [auto-compact window](<https://code.claude.com/docs/en/model-config#set-the-auto-compact-window>) |

#### PostCompact input

In addition to the [common input fields](<#common-input-fields>), PostCompact hooks receive `trigger` and `compact_summary`. The `compact_summary` field contains the conversation summary generated by the compact operation.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PostCompact",
  "trigger": "manual",
  "compact_summary": "Summary of the compacted conversation..."
}
```

PostCompact hooks have no decision control. They can't affect the compaction result but can perform follow-up tasks.

### PreModelSwitch

Runs before Claude Code applies a model switch that you or a client requested. Use it to block a switch, require confirmation, or show what the switch will cost before it happens.

PreModelSwitch requires Claude Code v2.1.251 or later. Claude Code runs it for these requests:

* `/model <name>` and the `/model` picker
* The `Option+P` or `Alt+P` model picker
* The Model setting in `/config`
* Turning on [fast mode](<https://code.claude.com/docs/en/fast-mode>) when that changes the session's model
* A `set_model` request, or a model change in an `apply_flag_settings` request, from an [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/typescript#query-object>) host or [Remote Control](<https://code.claude.com/docs/en/remote-control>)

Claude Code doesn't run PreModelSwitch hooks for switches it makes on its own, such as an [automatic model fallback](<https://code.claude.com/docs/en/model-config#automatic-model-fallback>) or restoring the model when you resume a session. Those changes reach [PostModelSwitch](<#postmodelswitch>) only.

Claude Code compares the matcher against the canonical name of the model the session is switching to, ignoring any `[1m]` suffix. An alias such as `opus`, a dated model ID, and a provider-specific ID such as an Amazon Bedrock model ID all match the one canonical name they resolve to, so `claude-opus-5` covers every spelling of Opus 5.

When Claude Code can't determine a canonical name for the target, for example a custom model ID that only your [LLM gateway](<https://code.claude.com/docs/en/llm-gateway>) knows, it runs every PreModelSwitch hook regardless of matcher. A hook that blocks should therefore check `to_model` from its input rather than rely on the matcher alone.

Write the matcher as an exact name, a `|`-separated list such as `claude-opus-4-6|claude-opus-5`, or a regular expression such as `.*opus.*`. This example uses an exact-name matcher and also checks `to_model` from the hook input, so it refuses a switch to Opus 4.6 by exiting with code 2 and lets any other target through:



  

#### macOS/Linux

    The command checks `to_model` with `jq`:

    ```json theme={null}
    {
      "hooks": {
        "PreModelSwitch": [
          {
            "matcher": "claude-opus-4-6",
            "hooks": [
              {
                "type": "command",
                "command": "jq -e '.to_model | test(\"opus-4-6\")' > /dev/null && { echo 'Opus 4.6 is retired for this project. Use a newer model.' >&2; exit 2; }; exit 0"
              }
            ]
          }
        ]
      }
    }
    ```
  


  

#### Windows (PowerShell)

    Register a command hook that runs a script through PowerShell:

    ```json theme={null}
    {
      "hooks": {
        "PreModelSwitch": [
          {
            "matcher": "claude-opus-4-6",
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-opus-46.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    Save this script to `.claude/hooks/block-opus-46.ps1` in your project:

    ```powershell theme={null}
    $hookInput = [Console]::In.ReadToEnd() | ConvertFrom-Json
    if ($hookInput.to_model -match 'opus-4-6') {
      [Console]::Error.WriteLine('Opus 4.6 is retired for this project. Use a newer model.')
      exit 2
    }
    exit 0
    ```
  


To confirm the hook works, run `/model claude-opus-4-6` from a session running a different model. Claude Code keeps the current model and reports that a PreModelSwitch hook blocked the switch, with your message as the reason.

#### PreModelSwitch input

In addition to the [common input fields](<#common-input-fields>), PreModelSwitch hooks receive the fields in this table. The last five describe what re-sending the conversation to the new model costs, so a hook can show that figure before the switch happens.

| Field | Type | Description |
| :- | :- | :- |
| `from_model` | string | Model ID the switch changes from |
| `to_model` | string | Model ID the switch changes to. The matcher compares against this model's canonical name |
| `requested_model` | string or `null` | The model the request named: an alias such as `opus`, a full model ID, or `null` when the request was for the default model |
| `source` | string | Where the request came from: `"command"` for `/model <name>`, the Model setting in `/config`, or turning on fast mode; `"picker"` for a model picker; `"sdk"` for a `set_model` request, or a model change in an `apply_flag_settings` request, from an Agent SDK host or Remote Control |
| `context_tokens` | number | Tokens the next request re-sends as its prompt: the input, cache read, cache creation, and output tokens of the last response in the main conversation, combined. `0` before the first response |
| `prompt_cache_warm` | boolean | Whether the current model's prompt cache is likely still warm, meaning the switch forfeits it |
| `cache_ttl` | string | [Prompt cache lifetime](<https://code.claude.com/docs/en/prompt-caching#cache-lifetime>) Claude Code requests for this session: `"5m"` or `"1h"` |
| `estimated_cache_write_usd` | number | Estimated cost in US dollars of writing `context_tokens` to the prompt cache on `to_model` at the `cache_ttl` rate, excluding the next response. The server may not need to re-cache the whole context, so treat it as an estimate |
| `pricing` | string | How Claude Code priced `estimated_cache_write_usd`: `"configured"` at your organization's own rates when it has configured them, `"catalog"` at list price, or `"default"` when `to_model` has no known price and Claude Code assumed a default rate |

This example shows the input for `/model opus` in a session running Sonnet 5:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "PreModelSwitch",
  "from_model": "claude-sonnet-5",
  "to_model": "claude-opus-5",
  "requested_model": "opus",
  "source": "command",
  "context_tokens": 182340,
  "prompt_cache_warm": true,
  "cache_ttl": "5m",
  "estimated_cache_write_usd": 1.1396,
  "pricing": "catalog"
}
```

#### PreModelSwitch decision control

`PreModelSwitch` hooks can cancel the switch, ask the user to confirm it, or let it proceed. Exit code 2 or a top-level `decision: "block"` cancels the switch.

For finer control, return `permissionDecision` and `permissionDecisionReason` in a `hookSpecificOutput` object, as on [PreToolUse](<#pretooluse-decision-control>). `PreModelSwitch` accepts `"allow"`, `"deny"`, and `"ask"`. It doesn't accept `"defer"`, `updatedInput`, or `additionalContext`. The table below describes both fields:

| Field | Description |
| :- | :- |
| `permissionDecision` | `"allow"` proceeds and skips the [confirmation Claude Code shows while the prompt cache is warm](<https://code.claude.com/docs/en/prompt-caching#switching-models>). `"deny"` cancels the switch. `"ask"` prompts the user to confirm it |
| `permissionDecisionReason` | For `"deny"`, shown to the user as the reason the switch was blocked, or returned as the error for a `set_model` request. For `"ask"`, shown in the confirmation prompt. Ignored for `"allow"` |

Only `/model` in an interactive session can show the `"ask"` prompt. On every other surface, including non-interactive mode with the `-p` flag, `/config`, and `set_model` requests, Claude Code treats `"ask"` as a refusal.

This example asks the user to confirm and quotes the token count from `context_tokens`:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "PreModelSwitch",
    "permissionDecision": "ask",
    "permissionDecisionReason": "Switching now re-sends about 180k tokens to the new model. Continue?"
  }
}
```

When multiple PreModelSwitch hooks return different decisions, precedence is `deny` > `ask` > `allow`.

Claude Code shows the user any `systemMessage` your hook returns regardless of the decision, so a cost-report hook can return `{"systemMessage": "..."}` and exit 0.

A PreModelSwitch hook that doesn't respond before its timeout blocks the switch. For what a timeout does on other events, see [Timeouts](<#timeouts>). The default timeout for this event is 30 seconds. `PreModelSwitch` runs `command`, `http`, and `mcp_tool` hooks only, so the `prompt` and `agent` defaults don't apply.

A hook that exits with a code other than 0 or 2 and prints no JSON decision is a non-blocking error, as described under [Other exit codes](<#other-exit-codes>).

### PostModelSwitch

Runs after the session's model changes. Use it to give Claude model-specific guidance without editing every CLAUDE.md, for example an organization-wide instruction that applies on certain models.

PostModelSwitch requires Claude Code v2.1.251 or later. It can't block, because the model has already changed. Claude Code runs PostModelSwitch hooks after any of these changes:

* A switch you or a client requested
* An [automatic model fallback](<https://code.claude.com/docs/en/model-config#automatic-model-fallback>), which changes the session's model
* A setting such as [`opusplan`](<https://code.claude.com/docs/en/model-config#opusplan-model-setting>) entering or leaving plan mode
* Claude Code restoring the model when you resume a session

Claude Code doesn't run PostModelSwitch hooks when a model from a [fallback model chain](<https://code.claude.com/docs/en/model-config#fallback-model-chains>) serves a turn, because that substitution lasts one turn and leaves the session's model unchanged.

The matcher follows the same rules as [PreModelSwitch](<#premodelswitch>): Claude Code compares it against the canonical name of the model the session switched to.

This example adds guidance whenever the session's model changes to any Opus model:

```json theme={null}
{
  "hooks": {
    "PostModelSwitch": [
      {
        "matcher": ".*opus.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'On Opus, delegate implementation work to subagents and keep this conversation for planning and review.'"
          }
        ]
      }
    ]
  }
}
```

To confirm the hook works, switch to an Opus model from a session running a different model, for example run `/model opus` from a Sonnet session, then ask Claude what guidance it has about the current model.

#### PostModelSwitch input

PostModelSwitch hooks receive the same fields as [PreModelSwitch](<#premodelswitch-input>), with `hook_event_name` set to `"PostModelSwitch"` and two more `source` values: `"auto"` for an automatic fallback or other change Claude Code made on its own, and `"resume"` for the model restored when you resume a session.

`requested_model` is `null` when `source` is `"auto"`. When `source` is `"resume"`, it is the saved model setting Claude Code restored.

#### PostModelSwitch decision control

Claude Code takes your hook's [plain-text stdout](<#exit-code-0>) on exit 0, or `additionalContext` from JSON output, and delivers it to Claude with the next request after the switch. In addition to the [JSON output fields](<#json-output>) available to all hooks, you can return:

| Field | Description |
| :- | :- |
| `additionalContext` | String added to Claude's context with the next request. See [Add context for Claude](<#add-context-for-claude>) |

If the hook hasn't finished within five seconds after you send the next prompt, Claude Code sends that request without the output and attaches it to the following request instead. If the model changes several times before the next request, Claude Code delivers only the output for the last switch's target model.

### SessionEnd

Runs when a Claude Code session ends. Useful for cleanup tasks, logging session
statistics, or saving session state. Supports matchers to filter by exit reason.

The `reason` field in the hook input indicates why the session ended:

| Reason | Description |
| :- | :- |
| `clear` | Session cleared with `/clear` command |
| `resume` | Session switched via interactive `/resume` |
| `logout` | User logged out |
| `prompt_input_exit` | User exited while prompt input was visible |
| `other` | Other exit reasons |
| `bypass_permissions_disabled` | Removed in v2.1.234; Claude Code doesn't send it. Drop it from your `SessionEnd` matchers |

#### SessionEnd input

In addition to the [common input fields](<#common-input-fields>), SessionEnd hooks receive a `reason` field indicating why the session ended. See the [reason table](<#sessionend>) above for all values.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "SessionEnd",
  "reason": "other"
}
```

SessionEnd hooks have no decision control. They can't block session termination but can perform cleanup tasks. Claude Code discards their [JSON output fields](<#json-output>), such as `systemMessage`.

SessionEnd hooks have a default timeout of 1.5 seconds. It applies when you exit, run `/clear`, or switch sessions with interactive `/resume`. You can give a hook more time in two ways:

* **Per-hook `timeout`**: set `timeout` in that hook's configuration. The overall budget rises automatically to match the highest per-hook `timeout` in your settings files, up to 60 seconds. If you raise the budget this way, a hook without its own `timeout` still keeps the default. Timeouts set on plugin-provided hooks don't raise the budget.
* **`CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS`**: set this environment variable in milliseconds to override the budget explicitly. The value you set also becomes the timeout for each hook without its own `timeout`.

This example sets the budget to 5 seconds:

```bash theme={null}
CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS=5000 claude
```

Before v2.1.268, `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` raised only the overall budget, and a hook without its own `timeout` was still canceled after 1.5 seconds.

### Elicitation

Runs when an MCP server requests user input mid-task. By default, Claude Code shows an interactive dialog for the user to respond. Hooks can intercept this request and respond programmatically, skipping the dialog entirely.

For a complete hook with its settings entry and script, see [Answer a form request from a script](<#answer-a-form-request-from-a-script>).

The matcher field matches against the MCP server name.

#### Elicitation input

In addition to the [common input fields](<#common-input-fields>), Elicitation hooks receive `mcp_server_name`, `message`, and optional `mode`, `url`, `elicitation_id`, and `requested_schema` fields.

For form-mode elicitation, the most common case:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Elicitation",
  "mcp_server_name": "my-mcp-server",
  "message": "Please provide your credentials",
  "mode": "form",
  "requested_schema": {
    "type": "object",
    "properties": {
      "username": { "type": "string", "title": "Username" }
    }
  }
}
```

For URL-mode elicitation, used for browser-based authentication:

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "Elicitation",
  "mcp_server_name": "my-mcp-server",
  "message": "Please authenticate",
  "mode": "url",
  "url": "https://auth.example.com/login"
}
```

#### Elicitation output

An Elicitation hook can answer the request for the user, decline or cancel it, or leave it to the dialog. To answer, decline, or cancel, exit 0 and print a `hookSpecificOutput` object with an `action`. The server gets your answer and no dialog appears. Each row of this table shows what to return for one outcome and what the MCP server receives:

| To | Return | The server receives |
| :- | :- | :- |
| Answer for the user | `"action": "accept"`, with the form field values in `content` | `accept` with your `content` |
| Decline the request | `"action": "decline"` | `decline` |
| Cancel the request | `"action": "cancel"` | `cancel` |
| Leave the request to the user | No output, with exit code 0 | The user's answer from the [dialog](<https://code.claude.com/docs/en/mcp#respond-to-mcp-elicitation-requests>) |

This output answers the form-mode request shown under [Elicitation input](<#elicitation-input>). The keys in `content` are the property names from that request's `requested_schema`:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Elicitation",
    "action": "accept",
    "content": {
      "username": "alice"
    }
  }
}
```

This output declines a request:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "Elicitation",
    "action": "decline"
  }
}
```

In the dialog, selecting **Decline** sends `decline` and pressing `Esc` sends `cancel`, so return the one you want the server to see.

For a URL-mode request, a hook that returns `accept` skips the dialog, so the URL never opens.

Claude Code discards `reason`, `systemMessage`, and `continue` from an Elicitation hook's JSON output, whichever `action` you return.

#### Other ways to decline an elicitation

Your hook can also decline in these ways. The server receives the same `decline` as for `"action": "decline"`:

* **Exits with code 2**: Claude Code ignores a `hookSpecificOutput` printed by the same hook
* **Prints a top-level `"decision": "block"`**: the block overrides an `action` in the same output

When several hooks match the same request, a decline from one of them overrides an `accept` or `cancel` from another.

This script declines URL-mode requests and leaves form requests to the dialog:

```bash theme={null}
#!/bin/bash
if [ "$(jq -r '.mode')" = "url" ]; then
  exit 2
fi
```

Neither the user nor the server sees why your hook declined, because Claude Code doesn't show your stderr or your `reason`.

Claude Code ignored a top-level `decision` from `Elicitation` and `ElicitationResult` hooks from v2.1.105 until the fix in v2.1.284.

#### Answer a form request from a script

This example answers one recurring question for the user. An MCP server named `issue-tracker` asks for a project key in a form, and the hook fills in `DOCS`. The script accepts when `project_key` is the form's single field. For any other request it prints nothing, so the dialog appears.



  

#### macOS/Linux

    Register a command hook for the event in your settings file, with the server name as the matcher:

    ```json theme={null}
    {
      "hooks": {
        "Elicitation": [
          {
            "matcher": "issue-tracker",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/answer-project-key.sh",
                "args": []
              }
            ]
          }
        ]
      }
    }
    ```

    Save this script to `.claude/hooks/answer-project-key.sh` in your project and make it executable with `chmod +x`:

    ```bash theme={null}
    #!/bin/bash
    input=$(cat)
    fields=$(jq -c '.requested_schema.properties // {} | keys' <<<"$input")

    if [ "$fields" = '["project_key"]' ]; then
      jq -n '{hookSpecificOutput: {hookEventName: "Elicitation", action: "accept", content: {project_key: "DOCS"}}}'
    fi
    ```
  


  

#### Windows (PowerShell)

    Register a command hook that runs the script through PowerShell, with the server name as the matcher:

    ```json theme={null}
    {
      "hooks": {
        "Elicitation": [
          {
            "matcher": "issue-tracker",
            "hooks": [
              {
                "type": "command",
                "command": "powershell.exe",
                "args": [
                  "-NoProfile",
                  "-ExecutionPolicy",
                  "Bypass",
                  "-File",
                  "${CLAUDE_PROJECT_DIR}/.claude/hooks/answer-project-key.ps1"
                ]
              }
            ]
          }
        ]
      }
    }
    ```

    Save this script to `.claude/hooks/answer-project-key.ps1` in your project:

    ```powershell theme={null}
    $request = [Console]::In.ReadToEnd() | ConvertFrom-Json
    $fields = @($request.requested_schema.properties.PSObject.Properties.Name)

    if ($fields.Count -eq 1 -and $fields[0] -eq 'project_key') {
      @{
        hookSpecificOutput = @{
          hookEventName = "Elicitation"
          action = "accept"
          content = @{ project_key = "DOCS" }
        }
      } | ConvertTo-Json -Depth 3
    }
    ```
  


To confirm the hook works, start Claude Code with `claude --debug` and give Claude a task that makes the server ask for the project key. No dialog appears, and the [debug log](<#debug-hooks>) has a line that ends with `Elicitation resolved by hook: {"action":"accept","content":{"project_key":"DOCS"}}`.

### ElicitationResult

Runs after a user responds to an MCP elicitation. Hooks can observe, modify, or block the response before it is sent back to the MCP server.

When an [Elicitation](<#elicitation>) hook answers a request, Claude Code sends that answer to the server without running ElicitationResult hooks.

The matcher field matches against the MCP server name.

#### ElicitationResult input

In addition to the [common input fields](<#common-input-fields>), ElicitationResult hooks receive `mcp_server_name`, `action`, and optional `mode`, `elicitation_id`, and `content` fields.

```json theme={null}
{
  "session_id": "abc123",
  "transcript_path": "/Users/.../.claude/projects/.../00893aaf-19fa-41d2-8238-13269b9b3ca0.jsonl",
  "cwd": "/Users/...",
  "hook_event_name": "ElicitationResult",
  "mcp_server_name": "my-mcp-server",
  "action": "accept",
  "content": { "username": "alice" },
  "mode": "form"
}
```

#### ElicitationResult output

An ElicitationResult hook can let the user's response through, change its values, or block it. To change or block the response, exit 0 and print a `hookSpecificOutput` object with an `action`. Each row of this table shows what to return for one outcome and what the MCP server receives:

| To | Return | The server receives |
| :- | :- | :- |
| Let the response through | No output, with exit code 0 | The user's response, unchanged |
| Change the submitted values | `"action": "accept"`, with the new values in `content` | `accept` with your `content` in place of the user's values |
| Block the response | `"action": "decline"` | `decline`, without the user's values |
| Cancel the request | `"action": "cancel"` | `cancel`, along with the values the user submitted. To withhold them, return `"decline"` |

This output changes the response shown under [ElicitationResult input](<#elicitationresult-input>), so the server receives `alice@example.com` where the user submitted `alice`:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "ElicitationResult",
    "action": "accept",
    "content": {
      "username": "alice@example.com"
    }
  }
}
```

Your `content` replaces the user's whole `content` object, so include the fields you aren't changing. Return `action` along with it, because Claude Code ignores a `hookSpecificOutput` that has no `action`.

ElicitationResult hooks also run when the user declines or cancels, and your `action` replaces theirs. Check that the input's `action` is `accept` before you return `accept`, or your hook turns a declined request into an accepted one. This script makes the same change when the user accepted, keeps the other fields, and prints nothing otherwise:

```bash theme={null}
#!/bin/bash
input=$(cat)

if [ "$(jq -r '.action' <<<"$input")" = "accept" ]; then
  jq '{hookSpecificOutput: {hookEventName: "ElicitationResult", action: "accept", content: (.content + {username: (.content.username + "@example.com")})}}' <<<"$input"
fi
```

This output blocks the response:

```json theme={null}
{
  "hookSpecificOutput": {
    "hookEventName": "ElicitationResult",
    "action": "decline"
  }
}
```

Exit code 2 and a top-level `"decision": "block"` also block the response. [Other ways to decline an elicitation](<#other-ways-to-decline-an-elicitation>) covers which one takes effect when a hook combines them, what the user sees, and which versions ignored `decision`.

Claude Code discards `reason`, `systemMessage`, and `continue` from an ElicitationResult hook's JSON output, whichever `action` you return.

## Prompt-based hooks

In addition to command, HTTP, and MCP tool hooks, Claude Code supports prompt-based hooks (`type: "prompt"`) that use an LLM to evaluate whether to allow or block an action, and agent hooks (`type: "agent"`) that spawn an agentic verifier with tool access. Not all events support every hook type.

Events that support all five hook types (`command`, `http`, `mcp_tool`, `prompt`, and `agent`):

* `PermissionDenied`
* `PostToolBatch`
* `PostToolUse`
* `PostToolUseFailure`
* `PreToolUse`
* `Stop`
* `SubagentStop`
* `TaskCompleted`
* `TaskCreated`
* `TeammateIdle`
* `UserPromptExpansion`
* `UserPromptSubmit`

`PermissionRequest` supports `command`, `http`, `mcp_tool`, and `prompt` hooks but not `agent` hooks. If you configure an agent hook on this event, Claude Code skips it and the permission flow proceeds unchanged. To allow or deny from a hook, return the [decision object](<#permissionrequest-decision-control>) from a command or HTTP hook.

Events that support `command`, `http`, and `mcp_tool` hooks but not `prompt` or `agent`:

* `ConfigChange`
* `CwdChanged`
* `DirectoryAdded`
* `Elicitation`
* `ElicitationResult`
* `FileChanged`
* `InstructionsLoaded`
* `MessageDisplay`
* `Notification`
* `PostCompact`
* `PostModelSwitch`
* `PreCompact`
* `PreModelSwitch`
* `SessionEnd`
* `StopFailure`
* `SubagentStart`
* `WorktreeCreate`
* `WorktreeRemove`

`SessionStart` and `Setup` support `command` and `mcp_tool` hooks, and [MCP tool hook fields](<#mcp-tool-hook-fields>) describes when their `mcp_tool` hooks run. They don't support `http`, `prompt`, or `agent` hooks.

### How prompt-based hooks work

Instead of executing a Bash command, prompt-based hooks:

1. Send the hook input and your prompt to a Claude model, by default the one Claude Code uses for [background functionality](<https://code.claude.com/docs/en/costs#background-token-usage>)
2. The LLM responds with structured JSON containing a decision
3. Claude Code processes the decision automatically

### Prompt hook configuration

Set `type` to `"prompt"` and provide a `prompt` string instead of a `command`. Use the `$ARGUMENTS` placeholder to inject the hook's JSON input data into your prompt text.

In a prompt or [agent hook](<#agent-based-hooks>), you can write the `prompt` as a rule about what to block or allow, such as "Block any Bash command that reads `.env` files", or as a condition that must hold, such as "All unit tests pass".

This `Stop` hook asks the LLM to evaluate whether all tasks are complete before allowing Claude to finish:

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate if Claude should stop: $ARGUMENTS. Check if all tasks are complete."
          }
        ]
      }
    ]
  }
}
```

| Field | Required | Description |
| :- | :- | :- |
| `type` | yes | Must be `"prompt"` |
| `prompt` | yes | The prompt text to send to the LLM. Use `$ARGUMENTS` as a placeholder for the hook input JSON. If `$ARGUMENTS` is not present, input JSON is appended to the prompt |
| `model` | no | Model to use for evaluation. Defaults to the model Claude Code uses for [background functionality](<https://code.claude.com/docs/en/costs#background-token-usage>) |
| `timeout` | no | Timeout in seconds. Default: 30 |
| `continueOnBlock` | no | On the events it applies to, `true` feeds an `ok: false` reason back to Claude and continues instead of ending the turn. Default: `false`. See [Response schema](<#response-schema>) for per-event behavior |

### Response schema

The LLM must respond with JSON containing:

```json theme={null}
{
  "ok": true | false,
  "reason": "Explanation for the decision",
  "impossible": true | false
}
```

| Field | Description |
| :- | :- |
| `ok` | `true` to allow. For `false`, see the per-event behavior below |
| `reason` | Required when `ok` is `false` |
| `impossible` | Optional. The model returns it with `ok: false` when it judges the condition can never be satisfied. On `Stop` and `SubagentStop`, Claude Code then lets the turn end instead of feeding the reason back. Agent hooks and other events ignore it |

What happens on `ok: false` depends on the event:

* `Stop` and `SubagentStop`: the reason is fed back to Claude as its next instruction and the turn continues, unless the response also sets `impossible: true`, in which case Claude Code allows the stop and the turn ends
* `PreToolUse`: the tool call is denied; by default the turn ends and the deny reason appears in the chat as a warning line. Set `continueOnBlock: true` to instead return the reason to Claude as the tool error so it can adjust and continue, equivalent to a command hook's `permissionDecision: "deny"`. Before v2.1.210, the deny reason was returned to Claude as the tool error and the turn continued
* `PostToolUse`: by default the turn ends and the reason appears in the chat as a warning line. Set `continueOnBlock: true` to feed the reason back to Claude and continue the turn instead
* `PostToolBatch`, `UserPromptSubmit`, and `UserPromptExpansion`: the turn ends and the reason appears as a warning line. These events end the turn on `decision: "block"` regardless of `continue`
* `PostToolUseFailure` and `TaskCreated`: the reason is returned to Claude as a tool error and the turn continues, regardless of `continueOnBlock`
* `TaskCompleted`: when it fires because a task is marked completed during a turn, the reason is returned to Claude as a tool error and the turn continues, regardless of `continueOnBlock`. When it fires because a teammate stops, it behaves like `TeammateIdle` and halts the teammate by default
* `TeammateIdle`: by default the teammate stops and the reason appears as a warning line. Set `continueOnBlock: true` to feed the reason back to the teammate and keep it working instead
* `PermissionRequest`: `ok: false` has no effect. To deny an approval from a hook, use a [command hook](<#command-hook-fields>) returning `hookSpecificOutput.decision.behavior: "deny"`
* `PermissionDenied`: `ok: false` has no effect because the denial already happened. The only output this event reads is `hookSpecificOutput.retry`, which prompt and agent hooks can't set. They run on this event, but their output is discarded. Use a [command hook](<#command-hook-fields>) to return `retry`

If you need finer control on any event, use a [command hook](<#command-hook-fields>) with the per-event fields described in [Decision control](<#decision-control>).

### Check multiple conditions before stopping

This `Stop` hook uses a detailed prompt to check three conditions before allowing Claude to stop. `SubagentStop` hooks use the same format to evaluate whether a [subagent](<https://code.claude.com/docs/en/sub-agents>) should stop. If the model returns `"ok": false` because the condition isn't met yet, Claude continues working with the provided reason as its next instruction:

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "You are evaluating whether Claude should stop working. Context: $ARGUMENTS\n\nAnalyze the conversation and determine if:\n1. All user-requested tasks are complete\n2. Any errors need to be addressed\n3. Follow-up work is needed\n\nRespond with JSON: {\"ok\": true} to allow stopping, or {\"ok\": false, \"reason\": \"your explanation\"} to continue working.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

## Agent-based hooks



**注意：**

  Agent hooks are experimental. Behavior and configuration may change in future releases. For production workflows, prefer [command hooks](<#command-hook-fields>).


Agent-based hooks (`type: "agent"`) are like prompt-based hooks but with multi-turn tool access. Instead of a single LLM call, an agent hook spawns a subagent that can read files, search code, and inspect the codebase to verify conditions. Agent hooks support the same events as [prompt-based hooks](<#prompt-based-hooks>), except `PermissionRequest`.

### How agent hooks work

When an agent hook fires:

1. Claude Code spawns a subagent with your prompt and the hook's JSON input
2. The subagent can use tools like Read, Grep, and Glob to investigate
3. After up to 50 turns, the subagent returns a structured `{ "ok": true/false }` decision
4. Claude Code allows the action if `ok` is `true`. If `ok` is `false`, Claude Code handles the block the same way as a prompt hook with `continueOnBlock: true` on that event, as listed under [Response schema](<#response-schema>)

Agent hooks are useful when verification requires inspecting actual files or test output, not just evaluating the hook input data alone.

### Agent hook configuration

Set `type` to `"agent"` and provide a `prompt` string, using `$ARGUMENTS` as a placeholder for the hook input JSON. The configuration fields are the same as [prompt hooks](<#prompt-hook-configuration>), except that agent hooks have a longer default timeout of 60 seconds and no `continueOnBlock` field.

The response schema is `{ "ok": true }` to allow or `{ "ok": false, "reason": "..." }` to block. On `ok: false`, Claude Code handles an agent hook the way it handles a [prompt hook with `continueOnBlock: true`](<#response-schema>) on the same event; agent hooks have no `continueOnBlock` field, and don't support the prompt-hook `impossible` field.

This `Stop` hook verifies that all unit tests pass before allowing Claude to finish:

```json theme={null}
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "agent",
            "prompt": "Verify that all unit tests pass. Run the test suite and check the results. $ARGUMENTS",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

## Run hooks in the background

By default, hooks block Claude's execution until they complete. For long-running tasks like deployments, test suites, or external API calls, set `"async": true` to run the hook in the background while Claude continues working. Async hooks can't block or control Claude's behavior: response fields like `decision`, `permissionDecision`, and `continue` have no effect, because the action they would have controlled has already completed.

### Configure an async hook

Add `"async": true` to a command hook's configuration to run it in the background without blocking Claude. This field is only available on `type: "command"` hooks.

This hook runs a test script after every `Write` tool call. Claude continues working immediately while `run-tests.sh` executes. When the script finishes, its output is delivered on the next conversation turn:

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/run-tests.sh",
            "async": true
          }
        ]
      }
    ]
  }
}
```

Once an async hook is running in the background, Claude Code doesn't enforce `timeout` on it. Claude Code still enforces `timeout` on a hook you run with `asyncRewake`.

Claude Code delivers an async hook's results only while the session runs:

* In [non-interactive mode](<https://code.claude.com/docs/en/headless>) with the `-p` flag, Claude Code kills any async hook still running at teardown and finalizes it with outcome `cancelled`
* If your hook's work must outlive a `claude -p` session, start a fully detached process from it

### How async hooks execute

When an async hook fires, Claude Code starts the hook process and immediately continues without waiting for it to finish. The hook receives the same JSON input via stdin as a synchronous hook.

After the background process exits, Claude Code delivers the `additionalContext` and `systemMessage` fields from the hook's JSON response to Claude on the next conversation turn. Unlike a synchronous hook's `systemMessage`, neither field is shown to you.

Claude Code validates that JSON response against the same [output schema](<#json-output>) as synchronous hooks, and drops any field whose value has the wrong type, such as a `systemMessage` that isn't a string, instead of delivering it. Run with `--debug` to see a warning naming each dropped field. Before v2.1.202, malformed JSON output from an async hook could crash the session, and the crash recurred each time the session was resumed.

Async hook completion notifications are suppressed by default. To see them, enable verbose mode with `Ctrl+O` or start Claude Code with `--verbose`.

### Run tests after file changes

This hook starts a test suite in the background whenever Claude writes a file, then reports the results back to Claude when the tests finish. Save this script to `.claude/hooks/run-tests-async.sh` in your project and make it executable with `chmod +x`:

```bash theme={null}
#!/bin/bash
# run-tests-async.sh

# Read hook input from stdin
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')

# Only run tests for source files
if [[ "$FILE_PATH" != *.ts && "$FILE_PATH" != *.js ]]; then
  exit 0
fi

# Run tests and report results to Claude via additionalContext
RESULT=$(npm test 2>&1)
EXIT_CODE=$?

if [ $EXIT_CODE -eq 0 ]; then
  MSG="Tests passed after editing $FILE_PATH"
else
  MSG="Tests failed after editing $FILE_PATH: $RESULT"
fi
jq -nc --arg msg "$MSG" '{hookSpecificOutput: {hookEventName: "PostToolUse", additionalContext: $msg}}'
```

Then add this configuration to `.claude/settings.json` in your project root. The `async: true` flag lets Claude keep working while tests run:

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/run-tests-async.sh",
            "args": [],
            "async": true
          }
        ]
      }
    ]
  }
}
```

### Limitations

Async hooks have additional constraints compared to synchronous hooks:

* Hook output is delivered on the next conversation turn. If the session is idle, the response waits until the next user interaction. Exception: an `asyncRewake` hook that exits with code 2 wakes Claude immediately even when the session is idle.
* Each execution creates a separate background process.

## Security considerations

### Disclaimer



**注意：**

  Command hooks execute shell commands with your full user permissions. They can modify, delete, or access any files your user account can access. Review and test all hook commands before adding them to your configuration.


### Workspace trust

Claude Code checks workspace trust before it runs any hook from a settings file. What counts as trusted depends on the session type:

* **Interactive session**: Claude Code holds back hooks from every settings file, including your own `~/.claude/settings.json`, until you accept the [workspace trust dialog](<https://code.claude.com/docs/en/permissions#project-allow-rules-and-workspace-trust>) for the folder, or for a parent directory whose trust extends to it
* **`-p` or SDK session**: Claude Code never shows the dialog and treats the folder as trusted, so hooks committed in a repository's `.claude/settings.json` run in a folder you've never trusted

Before you script `claude -p` over a repository you didn't write, review its `.claude/` settings files, start with [`--bare`](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>), or [turn hooks off for that run](<#disable-or-remove-hooks>) with `--settings '{"disableAllHooks": true}'`. Frontmatter hooks in a project subagent follow a stricter rule than settings-file hooks. [What runs before you trust a folder](<https://code.claude.com/docs/en/permissions#what-runs-before-you-trust-a-folder>) lists each kind of repository content by session type.

### Security best practices

Keep these practices in mind when writing hooks:

* **Validate and sanitize inputs**: never trust input data blindly
* **Always quote shell variables**: use `"$VAR"` not `$VAR`
* **Block path traversal**: check for `..` in file paths
* **Use absolute paths**: specify full paths for scripts. In exec form, use `${CLAUDE_PROJECT_DIR}` and the path needs no quoting. In shell form, wrap it in double quotes
* **Skip sensitive files**: avoid `.env`, `.git/`, keys, etc.

## Windows PowerShell tool

On Windows, you can run individual hooks in PowerShell by setting `"shell": "powershell"` on a command hook. Claude Code auto-detects `pwsh.exe`, the PowerShell 7 and later executable, and falls back to `powershell.exe` for Windows PowerShell 5.1.

```json theme={null}
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "shell": "powershell",
            "command": "Write-Host 'File written'"
          }
        ]
      }
    ]
  }
}
```

To reference the project root from a PowerShell shell-form command, write `${CLAUDE_PROJECT_DIR}` or `$env:CLAUDE_PROJECT_DIR`. Claude Code rewrites the `${CLAUDE_PROJECT_DIR}`, `${CLAUDE_PLUGIN_ROOT}`, and `${CLAUDE_PLUGIN_DATA}` placeholders in a PowerShell shell-form command to PowerShell's `${env:NAME}` form, whether the hook is defined in `settings.json`, a plugin, or a skill. PowerShell then resolves the value from the exported environment after parsing, so the placeholder works inside double-quoted strings but not inside single-quoted strings, where PowerShell never expands variables.

Don't write the bare `$CLAUDE_PROJECT_DIR` spelling in a PowerShell hook. PowerShell parses it as an undefined local variable and resolves it to `$null`, which leaves the script path without its project-root prefix. Claude Code doesn't rewrite that form; it logs a warning in the [debug log](<#debug-hooks>) instead.

The example below shows a `settings.json` hook that runs a project script with the `$env:` form:

```json theme={null}
{
  "type": "command",
  "shell": "powershell",
  "command": "& \"$env:CLAUDE_PROJECT_DIR\\.claude\\hooks\\check.ps1\""
}
```

## Debug hooks

Hook execution details are written to the debug log file. Start Claude Code with `claude --debug-file <path>` to write the log to a known location, or run `claude --debug` and read the log at `~/.claude/debug/<session-id>.txt`. The `--debug` flag doesn't print to the terminal.

For example, a `PostToolUse` hook on `Write` whose command prints `hook-ran` produces entries like:

```text theme={null}
2026-07-19T02:03:24.382Z [DEBUG] Hook output does not start with {, treating as plain text
2026-07-19T02:03:24.382Z [DEBUG] "Hook PostToolUse:Write (PostToolUse) success:\nhook-ran"
```

For more granular hook matching details, set `CLAUDE_CODE_DEBUG_LOG_LEVEL=verbose` to see additional log lines such as hook matcher counts and query matching.

For troubleshooting common issues like hooks not firing, Stop hooks that keep blocking, or configuration errors, see [Limitations and troubleshooting](<https://code.claude.com/docs/en/hooks-guide#limitations-and-troubleshooting>) in the guide. For a broader diagnostic walkthrough covering `/context`, `/doctor`, and settings precedence, see [Debug your config](<https://code.claude.com/docs/en/debug-your-config>).


</details>
