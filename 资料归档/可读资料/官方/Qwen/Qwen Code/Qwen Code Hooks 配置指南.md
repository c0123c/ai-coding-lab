# Qwen Code Hooks 配置指南

详解命令、HTTP、函数和提示 Hook、事件匹配、输入输出及作用域，用于在工具和会话节点执行检查或定制。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-10-08](<https://github.com/QwenLM/qwen-code/commit/d13ff87407d673c547726b43928dedba0465ffa1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/hooks.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/hooks.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Qwen Code 挂钩

## 概述

Qwen Code 挂钩提供了强大的机制来扩展和自定义 Qwen Code 应用程序的行为。挂钩允许用户在应用程序生命周期的特定点执行自定义脚本或程序，例如工具执行之前、工具执行之后、会话期间 start/end 以及其他关键事件期间。

默认情况下启用挂钩。您可以通过在设置文件中将 `disableAllHooks` 设置为 `true` 来临时禁用所有挂钩（在顶层，在 `hooks` 旁边）：

```json
{
  "disableAllHooks": true,
  "hooks": {
    "PreToolUse": [...]
  }
}
```

这将禁用所有挂钩而不删除其配置。

## 什么是钩子？

挂钩是用户定义的脚本或程序，由 Qwen Code 在应用程序流程中的预定义点自动执行。它们允许用户：

- 监控和审核工具的使用情况
- 执行安全政策
- 在对话中注入额外的上下文
- 根据事件自定义应用程序行为
- 与外部系统和服务集成
- 以编程方式修改工具输入或响应

### 浏览你的钩子

运行 `/hooks` 以打开此会话运行的挂钩的只读浏览器。它从事件移动到匹配器，再到匹配器下的各个钩子；没有匹配器支持的事件会直接陷入困境。挂钩的详细信息显示其类型、来源、是否启用、其命令、URL 或提示，以及设置后的超时、状态消息、HTTP `if` 条件以及是否运行一次或在后台运行。通过技能或 SDK 为当前会话注册的挂钩与源会话一起列出。

浏览器永远不会编辑您的配置：要添加、更改或删除挂钩，请编辑 `settings.json`。它确实重新阅读了它。打开交互式菜单将从该会话使用的用户、工作区和系统（系统和 SystemDefaults）设置文件中重新加载挂钩定义，包括会话在工作树中运行时的情况，因此自会话启动以来添加、更改或删除的定义无需重新启动即可生效。如果无法读取或解析用户或工作区文件，则保留先前的设置快照和正在运行的挂钩，文件保持不变，并显示错误。如果无法读取或解析系统设置文件，系统文件中的挂钩将保持原样，您的其他编辑仍然有效，文件保持不变，并会出现错误命名。

重新加载需要这种显式的菜单打开操作：保存文件、拉取更改或切换分支不会自动配备新的挂钩命令。非交互式的`/hooks list`仅显示该进程当前加载的注册表；它不会重新加载设置。在交互终端中，`/hooks list`打开与`/hooks`相同的菜单。

此重新加载涵盖挂钩定义，而不是挂钩控件或 HTTP 安全设置。对 `disableAllHooks`、`stopHookBlockingCap`、`security.allowedHttpHookUrls` 和 `security.allowPrivateNetworkHooks` 的更改仍然需要重新启动。由技能或 SDK 在运行时注册的挂钩不受影响。项目挂钩仅在受信任的文件夹中加载。当通过 `disableAllHooks`、`--safe-mode` 或 `--bare` 关闭挂钩时，不会加载任何内容，并且浏览器会在顶部显示此信息。

### Agent 前沿范围

agent 的 frontmatter 中声明的钩子仅适用于该调用，包括 
 其 `SubagentStart` 和 `SubagentStop` 事件。它们不会继承到嵌套的
agents或为父母和兄弟姐妹运行。全局设置挂钩和会话范围
skill/function挂钩保留其现有范围。项目-agent 挂钩在每个事件之前重新检查 
 提供 agent 的工作区的信任。

使用`SubagentStop`完成agent； `Stop` 不会自动重新映射。
如果要观察整个会话，请将 agent 挂钩移至设置。

## 挂钩类型

Qwen Code支持四种钩子执行器类型：

|类型|描述|
| :--------- | :--------------------------------------------------------------------------------------------- |
| `command`  |执行外壳命令。通过`stdin`接收JSON，通过`stdout`返回结果。|
| `http`     |将 JSON 作为 `POST` 请求正文发送到指定的 URL。通过 HTTP 响应正文返回结果。|
| `function` |直接调用已注册的JavaScript函数（仅限会话级挂钩）。|
| `prompt`   |使用 LLM 评估钩子输入并返回决策。|

### 命令挂钩

命令挂钩通过子进程执行命令。输入 JSON 通过 stdin 传递，输出通过 stdout 返回。

**配置：**

|领域|类型|必填|描述|
| :-------------- | :----------------------- | :------- | :------------------------------------------ |
| `type`          | `"command"`              |是的|挂钩类型|
| `command`       | `string`                 |是的|要执行的命令|
| `name`          | `string`                 |否|挂钩名称（用于记录）|
| `description`   | `string`                 |否|挂钩说明|
| `timeout`       | `number`                 |否|超时以秒为单位，默认60|
| `async`         | `boolean`                |否|是否在后台异步运行|
| `env`           | `Record<string, string>` |否|环境变量|
| `shell`         | `"bash" \| "powershell"` |否|外壳使用|
| `statusMessage` | `string`                 |否|执行期间显示的状态消息|

`timeout` 命令以秒为单位，HTTP 和提示挂钩； SDK-注册函数挂钩保持毫秒。命令挂钩超时过去以毫秒为单位写入，因此对于命令挂钩，`1000` 或更大的值仍被读取为毫秒，并且现有设置继续有效。要迁移，请查找 `timeout` 为 `1000` 或更大的命令挂钩，并以秒为单位重写该值，例如 `10000` 为 `10`。要为命令挂钩设置 1000 秒或更长的超时，请继续以毫秒为单位写入，例如 `1800000` 表示 30 分钟。非正数的命令挂钩 `timeout`（例如 `"30s"`）将被忽略，并应用 60 第二个默认值。启用调试日志记录 (`QWEN_DEBUG_LOG_FILE=1`) 后，每个带有毫秒或忽略的 `timeout` 的命令挂钩在该会话的调试日志中为每个会话命名一次。

**示例：**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "write_file",
        "hooks": [
          {
            "type": "command",
            "command": "\"$QWEN_PROJECT_DIR/.qwen/hooks/security-check.sh\"",
            "name": "security-check",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

`QWEN_PROJECT_DIR`、`CLAUDE_PROJECT_DIR`和`GEMINI_PROJECT_DIR`被设置为每个命令钩子环境中的项目目录。 Bash 挂钩从环境中读取它们，因此像任何其他 shell 变量一样用双引号引用它们，如 `"$QWEN_PROJECT_DIR/.qwen/hooks/security-check.sh"` 中所示。对于 cmd 挂钩，以及 PowerShell 挂钩中的裸 `$QWEN_PROJECT_DIR`，在命令运行之前将变量替换为引用的项目目录； `$env:QWEN_PROJECT_DIR` 也适用于 PowerShell。

迁移：bash 挂钩过去常常在 shell 运行之前在命令文本中替换这些变量，但该替换已被删除。如果 bash 钩子在包含空格的项目路径中保留不带引号的变量，或者将其写入单引号（例如 `'$QWEN_PROJECT_DIR/hook.sh'`）中，则现在必须将其双引号：`"$QWEN_PROJECT_DIR/hook.sh"`。

### HTTP 挂钩

HTTP 挂钩将挂钩输入作为 POST 请求发送到指定的 URL。它们支持URL白名单、DNS-级SSRF保护、环境变量插值和其他安全功能。

**配置：**

|领域|类型|必填|描述|
| :--------------- | :----------------------- | :------- | :-------------------------------------------------------- |
| `type`           | `"http"`                 |是的|挂钩类型|
| `url`            | `string`                 |是的|目标URL|
| `headers`        | `Record<string, string>` |否|请求标头（支持环境变量插值）|
| `allowedEnvVars` | `string[]`               |否|URL/headers 中允许的环境变量白名单|
| `timeout`        | `number`                 |否|超时以秒为单位，默认600|
| `name`           | `string`                 |否|挂钩名称（用于记录）|
| `statusMessage`  | `string`                 |否|执行期间显示的状态消息|
| `once`           | `boolean`                |否|每个会话每个事件仅执行一次（仅限 HTTP 挂钩）|

**安全特性：**

- **URL白名单**：通过`allowedUrls`配置允许的URL模式
- **SSRF 保护**：阻止私有 IP（10.x.x.x、172.16-31.x.x、192.168.x.x 等），但允许环回地址(127.0.0.1,::1)
- **DNS 验证**：在请求之前验证域解析以防止 DNS 重新绑定攻击
- **环境变量插值**：`${VAR}`语法，只允许`allowedEnvVars`白名单中的变量

#### 允许专用网络挂钩（仅限托管环境）

默认情况下，HTTP 挂钩无法定位私有或链接本地 IP 范围。在挂钩接收器是第一方 VPC- 内部端点（例如，解析为 `172.16.0.0/12` 的内部 API 网关）的平台管理环境中，您可以通过以下方式放宽 IP- 范围检查：

```json
{
  "security": {
    "allowPrivateNetworkHooks": true
  }
}
```

- 此设置 ** 仅适用于用户、系统和 SystemDefaults 设置范围 **。在工作区（项目）设置中设置的值将被忽略并记录为警告，因此克隆存储库永远无法自行授予此绕过权限。
- 该标志仅放宽一般 private/CGNAT/link-local ** 范围 ** 检查。云元数据端点在每种配置中都保持被阻止：`BLOCKED_HOSTS` 列表按字面匹配（`metadata.google.internal`、`metadata.azure.internal`，...），并且元数据 IP `169.254.169.254` 和 `100.100.100.200` 以所有序列化形式被阻止（包括 IPv4 映射的 IPv6，例如`::ffff:a9fe:a9fe`）并在DNS分辨率之后。
- `security.allowedHttpHookUrls`白名单仍然独立适用。在托管环境中，将此标志与白名单配对，以便仅可以访问预期的内部端点。仅当没有用户、系统或 SystemDefaults 范围设置为白名单时，才会遵守工作区（项目）设置中的白名单；否则它会被忽略并记录为警告，因此存储库可以缩小其挂钩发送数据的范围，但永远不会替换您配置的白名单（空白名单意味着“允许所有”）。
- HTTP 挂钩从不遵循重定向。 3xx 响应的处理方式与任何其他非 2xx 状态相同：非阻塞挂钩失败，并且永远不会联系重定向目标。

> **警告：**启用此标志可以让挂钩到达网络上的内部基础设施。仅在受信任的托管设置中启用它 - 切勿在您无法控制的存储库中启用它。

**示例：**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "http",
            "url": "http://127.0.0.1:8080/hooks/pre-tool-use",
            "headers": {
              "Authorization": "Bearer ${HOOK_API_KEY}"
            },
            "allowedEnvVars": ["HOOK_API_KEY"],
            "timeout": 10,
            "name": "remote-security-check"
          }
        ]
      }
    ]
  }
}
```

**示例：外部判断服务适配器**

的 `remote-security-check` 上面的配置期望 `http://127.0.0.1:8080/hooks/pre-tool-use` 到
已经在运行一个使用此合约的服务（POST `{tool_name, tool_input, ...}` 在，
`hookSpecificOutput.permissionDecision` 出）。这是一个最小的、仅限 stdlib 的适配器，可填充
在缺失的一半中，连接到一个具体的判断后端，这样整个事情就可以运行并且
可端到端测试而不是存根。只有 `review()` 函数是后端特定的——交换
它的身体和 request/response 适合您使用的任何服务；其他一切（服务器，
无论后端如何，故障打开处理、钩子响应形状）都保持相同。

_披露：下面使用的后端，[invinoveritas](<https://api.babyblueviper.com>)，是作者所属的一项
服务 - 此处使用它是因为它是可以在本示例中进行
端到端验证的服务，而不是认可。任何返回 
JSON 判决的 HTTP 服务都同样有效；仅`review()`需要更改。_

_数据处理：使用`matcher: "*"`，每个**工具调用的**的完整`tool_input`被发送到
判断后端 - 将该输入视为敏感（它可能包含文件内容，路径，或
秘密）。如果只需要判断shell
命令，则缩小匹配器范围（例如缩小为`run_shell_command`）。_

```python
#!/usr/bin/env python3
# judgment_hook.py -- run: JUDGMENT_API_KEY=... python3 judgment_hook.py
import json, os, sys, urllib.request
from http.server import BaseHTTPRequestHandler, HTTPServer

JUDGMENT_API_KEY = os.environ["JUDGMENT_API_KEY"]
JUDGMENT_URL = os.environ.get("JUDGMENT_URL", "https://api.babyblueviper.com/review")

def review(tool_name, tool_input):
    """POST the call to the judgment backend and return its verdict. This is the
    one function to change for a different backend -- request/response shape
    below matches invinoveritas's /review; adapt both to your own backend's
    contract if you swap it out."""
    body = json.dumps({
        "artifact": json.dumps({"tool_name": tool_name, "tool_input": tool_input}),
        "artifact_type": "shell_command" if tool_name in ("run_shell_command", "shell") else "general",
        "context": f"qwen-code PreToolUse: {tool_name}",
    }).encode()
    req = urllib.request.Request(
        JUDGMENT_URL, data=body,
        headers={"Authorization": f"Bearer {JUDGMENT_API_KEY}", "Content-Type": "application/json"},
    )
    # Keep this below the HTTP hook's own timeout (10s in the config above), so a "deny"
    # verdict is always returned before the hook gives up and fails open on its own.
    with urllib.request.urlopen(req, timeout=8) as resp:
        return json.loads(resp.read())  # response includes a "verdict" field: "reject" denies, anything else allows

class Handler(BaseHTTPRequestHandler):
    def do_POST(self):
        payload = json.loads(self.rfile.read(int(self.headers.get("Content-Length", 0))) or b"{}")
        tool_name, tool_input = payload.get("tool_name", "unknown"), payload.get("tool_input", {})
        try:
            verdict = review(tool_name, tool_input)
            decision = "deny" if verdict.get("verdict") == "reject" else "allow"
            reason = verdict.get("summary", f"judgment verdict: {verdict.get('verdict')}")
        except Exception as e:
            decision, reason = "allow", "judgment backend unavailable, failing open"  # never block on a review-side outage
            print(f"judgment backend unavailable for {tool_name}, failing open: {e}", file=sys.stderr)
        out = {"continue": True, "decision": decision, "hookSpecificOutput": {
            "hookEventName": "PreToolUse", "permissionDecision": decision, "permissionDecisionReason": reason,
        }}
        body = json.dumps(out).encode()
        self.send_response(200)
        self.send_header("Content-Type", "application/json")
        self.send_header("Content-Length", str(len(body)))
        self.end_headers()
        self.wfile.write(body)
    def log_message(self, *a): pass

if __name__ == "__main__":
    HTTPServer(("127.0.0.1", 8080), Handler).serve_forever()
```

针对上面的实际生产 API 进行了实时端到端测试：真正具有破坏性的输入 
(`{"tool_name": "run_shell_command", "tool_input": {"command": "rm -rf /important_data"}}`)
 返回了 `permissionDecision: "deny"` 并附有真实的解释；良性的（`ls -la`）返回
`"allow"`。无法从判断 
 后端打开任何 network/timeout/malformed-response 问题，因此中断永远不会阻止合法的工具调用 - 与上面的 `command`-hook 
 示例相同的规则适用于它们自己的退出代码。

### 功能挂钩

函数钩子直接调用注册的JavaScript/TypeScript函数。它们由技能系统内部使用，目前并未作为公共 API 公开给最终用户。

**注意**：对于大多数用例，请使用**命令挂钩**或**HTTP挂钩**，这些可以在设置文件中配置。

### 提示钩子

提示钩子使用 LLM 来评估钩子输入并返回决策。这对于根据上下文做出智能决策非常有用，例如确定是否允许或阻止操作。

> **数据处理：**提示挂钩将其事件输入发送到配置的模型提供程序。启用文件支持的调试日志记录时，完全扩展的提示挂钩请求也会写入会话调试日志。将挂钩输入和调试日志视为潜在敏感的。

**工作原理：**

1. 使用 `$ARGUMENTS` 占位符将挂钩输入 JSON 注入到提示中
2. 提示将发送到 LLM（默认：您当前的型号）
3. LLM 返回 JSON 响应并做出决定
4. Qwen Code 处理决策并相应地继续或阻止执行

**配置：**

|领域|类型|必填|描述|
| :-------------- | :--------- | :------- | :-------------------------------------------------- |
| `type`          | `"prompt"` |是的|挂钩类型|
| `prompt`        | `string`   |是的|提示已发送至LLM。使用`$ARGUMENTS`进行挂钩输入|
| `model`         | `string`   |否|要使用的型号（默认为您当前的型号）|
| `timeout`       | `number`   |否|超时以秒为单位，默认30|
| `name`          | `string`   |否|挂钩名称（用于记录）|
| `description`   | `string`   |否|挂钩说明|
| `statusMessage` | `string`   |否|执行期间显示的状态消息|

**响应格式：**

LLM 必须返回具有以下结构的 JSON：

```json
{
  "ok": true,
  "reason": "Explanation of the decision",
  "additionalContext": "Optional context to inject into the conversation"
}
```

|领域|描述|
| :------------------ | :------------------------------------------------------------------------- |
| `ok`                |`true` 至 allow/continue、`false` 至 block/stop|
| `reason`            |当`ok`为`false`时需要。向模型展示以解释该块|
| `additionalContext` |可选。允许时注入对话的附加上下文|

**支持的事件：**

提示挂钩可用于大多数挂钩事件，包括：

- `PreToolUse` - 评估是否允许工具调用
- `PostToolUse` - 评估工具结果并可能注入上下文
- `Stop` - 判断是继续还是停止
- `SubagentStop` - 评估子代理结果
- `UserPromptSubmit` - 评估或丰富符合条件的模型绑定提示

**示例：停止挂钩**

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "You are evaluating whether Qwen Code should stop working. Context: $ARGUMENTS\n\nAnalyze the conversation and determine if:\n1. All user-requested tasks are complete\n2. Any errors need to be addressed\n3. Follow-up work is needed\n\nRespond with JSON: {\"ok\": true} to allow stopping, or {\"ok\": false, \"reason\": \"your explanation\"} to continue working.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

当 `ok` 为 `false` 时，Qwen Code 将继续工作并使用 `reason` 作为下一个响应的上下文。

**示例：PreToolUse 挂钩**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "run_shell_command",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate this tool call for security concerns. Tool input: $ARGUMENTS\n\nCheck for:\n- Dangerous commands (rm -rf, curl | sh, etc.)\n- Unauthorized access attempts\n- Data exfiltration patterns\n\nRespond with {\"ok\": true} if safe, or {\"ok\": false, \"reason\": \"concern\"} if blocked.",
            "model": "sonnet",
            "timeout": 30,
            "name": "security-evaluator"
          }
        ]
      }
    ]
  }
}
```

## 挂钩事件

Hook 在 Qwen Code 会话期间的特定点触发。不同的事件支持不同的匹配器来过滤触发条件。

|活动|触发时间|匹配器目标|
| :-------------------- | :--------------------------------------------------------------------- | :--------------------------------------------------------------- |
| `PreToolUse`          |工具执行之前|工具 ID（`write_file`、`read_file`、`run_shell_command` 等）|
| `PostToolUse`         |成功执行工具后|工具编号|
| `PostToolUseFailure`  |工具执行失败后|工具编号|
| `PostToolBatch`       |批量中的每个工具调用解决后一次|无（总是触发）|
| `UserPromptSubmit`    |在支持的模型调用之前|无|
| `UserPromptExpansion` |在斜杠命令展开为提示符之后、发送提示符之前|命令名称，不带前导 `/`|
| `SessionStart`        |当会话开始或恢复时|源（`startup`、`resume`、`clear`、`compact`）|
| `SessionEnd`          |当会话结束时|原因（`clear`、`logout`、`prompt_input_exit` 等）|
| `SessionDelete`       |删除明确选择的会话后|无|
| `MessageDisplay`      |随着回复不断重复|无（总是触发）|
| `Stop`                |回合结束前|无（总是触发）|
| `StopFailure`         |当API错误或循环检测结束回合时，而不是`Stop`|错误类型（`rate_limit`、`server_error`、`loop_detected`等）|
| `SubagentStart`       |当子代理启动时|Agent 类型（`Bash`、`Explorer`、`Plan` 等）|
| `SubagentStop`        |当子代理停止时|Agent型|
| `PreCompact`          |对话压缩前|触发（`manual`、`auto`）|
| `PostCompact`         |对话压缩成功后|触发（`manual`、`auto`）|
| `Notification`        |发送通知时|类型（`permission_prompt`、`idle_prompt`、`auth_success`）|
| `PermissionRequest`   |当显示权限对话框时|工具编号|
| `PermissionDenied`    |当AUTO-模式分类拒绝工具调用时|工具编号|
| `TodoCreated`         |当创建新的待办事项时|无（总是触发）|
| `TodoCompleted`       |当待办事项标记为已完成时|无（总是触发）|
| `InstructionsLoaded`  |当加载上下文文件（例如 `QWEN.md`）或其导入的文件时|加载文件的文件路径|

### 匹配器模式

`matcher`是用于过滤触发条件的正则表达式。

|事件类型|活动|匹配器支持|匹配器目标|
| :------------------ | :----------------------------------------------------------------------------------------- | :-------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------- |
|工具事件| `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied` |✅ 正则表达式|工具ID：`write_file`、`read_file`、`run_shell_command`等|
|工具事件| `PostToolBatch`                                                                            |❌ 没有| N/A                                                                                                                                                    |
|子代理事件| `SubagentStart`, `SubagentStop`                                                            |✅ 正则表达式|Agent类型：`Bash`、`Explorer`等|
|会议活动| `SessionStart`                                                                             |✅ 正则表达式|来源：`startup`、`resume`、`clear`、`compact`|
|会议活动| `SessionEnd`                                                                               |✅ 正则表达式|原因：`clear`、`logout`、`prompt_input_exit`等。|
|会议活动| `SessionDelete`                                                                            |❌ 没有| N/A                                                                                                                                                    |
|通知事件| `Notification`                                                                             |✅ 正则表达式|类型：`permission_prompt`、`idle_prompt`、`auth_success`|
|紧凑型活动| `PreCompact`, `PostCompact`                                                                |✅ 正则表达式|触发：`manual`、`auto`|
|都都活动| `TodoCreated`, `TodoCompleted`                                                             |❌ 没有| N/A                                                                                                                                                    |
|提示事件| `UserPromptSubmit`                                                                         |❌ 没有| N/A                                                                                                                                                    |
|提示事件| `UserPromptExpansion`                                                                      |✅ 正则表达式|不带前导的命令名称 `/`，例如 `init`|
|停止活动| `Stop`                                                                                     |❌ 没有| N/A                                                                                                                                                    |
|停止活动| `StopFailure`                                                                              |✅ 正则表达式|错误类型：`rate_limit`、`authentication_failed`、`billing_error`、`invalid_request`、`server_error`、`max_output_tokens`、`loop_detected`、`unknown`|
|信息显示| `MessageDisplay`                                                                           |❌ 没有| N/A                                                                                                                                                    |
|教学活动| `InstructionsLoaded`                                                                       |✅ 正则表达式|加载文件的文件路径|

**匹配器语法：**

- 空字符串 `""`、`"*"` 或 `".*"` 匹配该类型的所有事件
- 首先对匹配器进行精确比较。在 `|` 分隔的列表中，例如 `permission_prompt | idle_prompt`，当任何条目（忽略其周围的空格）是 `*`、`.*` 或恰好是值时，匹配器就会匹配，除非整个匹配器以 `^` 或 `(` 开头，其中case 它只是一个正则表达式。只有 `[...]` 和组之外的 `|` 分隔条目，且未使用反斜杠转义：`notes\|todo\.md`、`foo[ |]bar` 和 `a(b | c)` 中的管道是正则表达式的一部分，并且保留它们周围的空格。列表条目永远不会单独作为正则表达式读取
- 否则，匹配器是未锚定的正则表达式（例如，`^run_shell_command$`、`read_.*`、`(write_file|edit)`）。对于不以 `^` 或 `(` 开头的列表，表达式是根据修剪后的非空条目构建的（保留条目边缘的转义空格，如 `\.env\ |\.pem` 中所示），因此 `read_(file|edit)|` 中的杂散 `|` 会被忽略，并且永远不会生成匹配器匹配所有内容，并且只有 `|` 的匹配器不匹配任何内容。以 `^` 或 `(` 开头的匹配器完全按照编写的方式进行编译，因此尾随 `|` ，如 `^write_file|` 中所示，确实使其匹配所有内容。由于表达式未锚定，因此 `read` 也匹配 `read_file`，并且 `edit` 也匹配 `notebook_edit`。添加 `^` 和 `$` 以匹配整个值，并锚定排除：`^(?!write_file).*$` 排除 `write_file`，而未锚定的 `(?!write_file).*` 仍然匹配它
- 相同的规则适用于支持匹配器的每个事件以及由技能注册的挂钩
- 工具挂钩接收 `tool_name` 中的运行时工具 ID（例如，`write_file`）。为了兼容性，内置显示名称（例如 `WriteFile` 和 `ReadFile`）也被接受作为匹配器别名，工具名称权限规则也接受，包括 Claude Code 的 `Bash`、`Read` 和 `Write`，因此从Claude Code 配置（例如 `"Bash"` 或 `"Write|Edit"`）保持不变。新配置仍应优先选择运行时 ID。别名仅进行精确比较，因此锚定运行时 ID (`^write_file$`)，而不是显示名称。每个别名恰好命名一个工具：与权限规则不同，`Read` 匹配器不涵盖 `grep_search` 或 `glob`，并且 `Bash` 匹配器不涵盖 `monitor`。

**示例：**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "^run_shell_command$",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'bash check' >> /tmp/hooks.log"
          }
        ]
      },
      {
        "matcher": "write_.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'write check' >> /tmp/hooks.log"
          }
        ]
      },
      {
        "matcher": "*",
        "hooks": [
          { "type": "command", "command": "echo 'all tools' >> /tmp/hooks.log" }
        ]
      }
    ],
    "SubagentStart": [
      {
        "matcher": "^(Bash|Explorer)$",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'subagent check' >> /tmp/hooks.log"
          }
        ]
      }
    ]
  }
}
```

## Input/Output 规则

### 钩子输入结构

所有钩子执行器都会接收标准化事件输入。交付边界取决于执行者：

|挂钩类型|输入收件人|
| :--------- | :-------------------------------------------------------------- |
| `command`  |通过 `stdin` 上的 JSON 进行子进程|
| `http`     |通过 JSON `POST` 主体配置的端点|
| `function` |可信进程内回调|
| `prompt`   |输入替换`$ARGUMENTS`后配置的模型提供者|

函数挂钩是在 Qwen 进程中运行的可信代码。它们接收一个进程内对象，因此字段不能被视为对函数挂钩是不可变的。

Qwen 不控制钩子进程、端点、回调或模型提供者是否保留或转发其输入。检查每个配置的执行器的数据处理策略。

**常用字段：**

```json
{
  "session_id": "string",
  "transcript_path": "string",
  "cwd": "string",
  "hook_event_name": "string",
  "timestamp": "string",
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "agent_id": "string (only when the event fires inside a subagent)",
  "prompt_id": "string (when the event belongs to a model turn)",
  "source_type": "string (only when the session has a declared source)",
  "source_id": "string (only when the session source has an id)"
}
```

根据挂钩类型添加特定于事件的字段。 `permission_mode` 是会话的批准模式，除非事件报告应用于它的模式，就像工具和子代理事件一样。 `agent_id` 仅当事件在子代理内部触发时才出现； `agent_type` 已在 `SessionStart`、`SubagentStart` 和 `SubagentStop` 上报告。 `source_type` 和 `source_id` 仅出现在客户端在创建会话时声明了会话源的 ACP 会话中。

挂钩输入是一个可向前扩展的 JSON 合约：可以将新的可选字段添加到现有事件中。消费者应该忽略未知字段。在升级 Qwen Code 之前，必须更新拒绝未知属性的严格解码器，以明确允许每个新的可选字段。对于安全敏感的挂钩，解码器故障可能会更改故障打开或故障关闭行为，因此管理员必须在推出之前根据已部署的挂钩验证升级后的有效负载。

### 挂钩输出结构

挂钩输出通过 `stdout`（命令）或 HTTP 响应正文 (http) 返回为 JSON。

**退出代码行为（命令挂钩）：**

|退出代码|行为|
| :-------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `0`       |成功。 `stdout` 中的 JSON 对象控制行为。任何其他 `stdout`，包括裸 JSON 值，例如 `42`，都是纯文本：它被添加到 `SessionStart`、`UserPromptSubmit` 和 `UserPromptExpansion` 上的模型上下文中，并作为其他事件的系统消息保存。看起来像 JSON 对象但未解析的输出永远不会添加到模型上下文中。|
| `2`       |**阻塞错误**。忽略 `stdout`，将 `stderr` 作为错误反馈传递给模型。|
|其他|非阻塞错误。 `stderr` 仅在调试模式下显示，继续执行。|

将纯文本添加到模型上下文仅适用于命令挂钩的 `stdout`。仅当 `Content-Type` 为 `application/json` 时，HTTP 钩子的响应正文才会被读取为 JSON；任何其他非空主体在每个事件上都会变成 `systemMessage`。添加上下文的 HTTP 挂钩必须返回 JSON 和 `hookSpecificOutput.additionalContext`。

**输出结构：**

Hook 输出支持三类字段：

1. **常用字段**：`continue`、`stopReason`、`suppressOutput`、`systemMessage`
2. **顶层决策**：`decision`、`reason`（由某些事件使用）
3. **事件特定控制**：`hookSpecificOutput`（必须包括`hookEventName`）

```json
{
  "continue": true,
  "decision": "allow",
  "reason": "Operation approved",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "Additional context information"
  }
}
```

### 个人挂钩活动详情

#### PreToolUse

**用途**：在使用工具之前执行，以允许权限检查、输入验证或上下文注入。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool being executed",
  "tool_input": "object containing the tool's input parameters",
  "tool_use_id": "unique identifier for this tool use instance (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)"
}
```

**输出选项**：

- `hookSpecificOutput.permissionDecision`：“允许”、“拒绝”或“询问”(REQUIRED)
- `hookSpecificOutput.permissionDecisionReason`：对该决定的解释（REQUIRED）
- `hookSpecificOutput.updatedInput`：替换工具整个输入的对象（请参阅[替换工具输入](<#更换工具输入>)）
- `hookSpecificOutput.additionalContext`：在空行之后附加到模型接下来看到的此调用的工具结果的文本（对于从代码模式 `exec` 脚本进行的调用，附加到 `exec` 调用的结果；见下文）。无论做出什么决定，它都会被传递：在工具运行时自己的输出之后，或者在被拒绝、停止或失败时在错误消息之后。 `<` 和 `>` 已转义。

`permissionDecision` 值控制该工具是否运行：

- `"allow"` — 让通话继续；通常的权限检查和批准提示仍然适用。
- `"deny"` — 阻止工具；它不会执行，并且会向模型返回错误。
- `"ask"` — 在运行之前暂停并要求用户确认 TUI 中的工具调用。确认运行该工具一次；下降会取消它。此确认取代了通常的批准提示，并且没有权限规则、批准模式（包括YOLO）或PermissionRequest挂钩来批准调用； PermissionRequest 挂钩仍然可以拒绝它，或替换其输入，然后用户确认。在无法提示确认的上下文中 — 无头 (`--prompt`) 运行和后台子代理 — `"ask"` 回退到 `"deny"`。

对于 `"ask"`，TUI 将 `permissionDecisionReason` 显示为文字文本，而不是解释内联 Markdown。这使得格式化标记和链接目标对用户可见。

与 `"ask"`，钩子运行一次。其 `additionalContext` 如果用户确认，则随工具结果一起传递；如果用户拒绝或呼叫取消，则丢弃。在 ACP 会话（IDE 集成）， `"ask"` 目前被视为 `"deny"`，并且上下文随否认一起传递。

`additionalContext` 进入工具结果。它不会更改工具输入、批准提示、工具运行时显示的错误或用户提示。因为它成为工具结果的一部分，所以它与它一起保存在会话记录中，并且可以出现在显示完整工具输出的任何位置，例如 Ctrl+O 详细视图或恢复的会话；它没有与工具自己的输出分开标记。

调用的 PreToolUse 挂钩添加的文本被剪切为 `tools.truncateToolOutputThreshold` 个字符；在 ACP 会话中，相同的限制同时涵盖 PreToolUse 和 PostToolUseFailure 上下文。批次输出限制仍然适用于整个结果。

当取消调用时，或者在工具结果返回模型之前取消转弯时，即使工具本身已完成，也不会传递上下文。

在代码模式 (`tools.codeModeOnly`) 下，从 `exec` 脚本进行的调用将其结果返回到脚本，而不是模型。它的 PreToolUse 和 PostToolUseFailure 上下文被附加到 `exec` 调用的工具结果中，在 `exec` 调用自己的上下文之后，并且脚本接收的值保持不变。

##### 更换工具输入

`updatedInput` 替换该工具的整个输入。它不会与原始字段合并：您遗漏的字段将被删除，并应用该工具自己的默认值。空对象 `{}` 也是替代品。

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "updatedInput": { "file_path": "/workspace/notes/today.md" }
  }
}
```

- 调用的 PreToolUse 挂钩在已知该工具已启用且可调用之后但在验证其输入或根据权限进行检查之前运行一次。因此，替换可以纠正本来会被拒绝的输入。当一轮中的多个调用被安排在一起时，如在交互式终端 UI 中，每个调用的钩子都会在它们中的任何一个运行之前运行。
- 然后，替换会经历原始输入所具有的所有内容：工具自身的验证、`permissions` 规则、批准模式、计划模式和显示替换的批准提示。 `"allow"` 不允许替换者跳过其中任何一个。
- 挂钩无法更改运行的工具、其调用 ID 或其工作目录。模型自己的工具调用保持在对话中；批准提示、工具、其结果和 PostToolUse 事件使用替换。
- 存在的 `updatedInput` 但不存在 JSON 对象（`null`、数组、字符串或数字）会停止调用并出现错误；不使用原始输入。当钩子并行运行时，它们都接收相同的输入，配置顺序中的最后一个替换获胜，并且其中任何一个的无效替换都会停止调用。每个按顺序运行的挂钩都会收到先前的替换作为 `tool_input`。
- 旧的 `hookSpecificOutput.tool_input` 字段仅更改传递到下一个顺序挂钩的输入；它永远不会改变工具运行时使用的输入。请改用 `updatedInput`。当输出同时具有两者时，`updatedInput` 获胜。
- 挂钩仍可以重写的 shell 命令不会与其他工具调用并行运行。
- 如果技能注册了 PreToolUse 挂钩，而同一回合的其他调用仍在等待运行，则这些调用不会运行，因为它们的挂钩在没有它的情况下运行；模型被告知再次调用该工具。
- 用户在批准提示中所做的更改（例如，修改编辑器中的内容）在挂钩运行后发生。
- 来自异步挂钩的 `updatedInput` 被忽略。
- 在 ACP 会话中，即使进行了替换，`"ask"` 仍被视为 `"deny"`。
- 工具调用通过托管工具运行时运行，而编排器进行的固定策略调用尚不支持 `updatedInput`。替换会因错误而停止调用，而不是运行任一输入。

PermissionRequest 钩子的 `decision.updatedInput` 以相同的方式进行检查：不是 JSON 对象的钩子会停止调用，并且在运行之前根据权限规则验证替换，并且钩子的 `"allow"` 仅计为对该替换的批准。需要钩子无法做出决定的替换，例如用户的回答，则会转至批准提示；在 ACP 会话中它不会运行。在 AUTO 模式下，替换在运行之前会再次审核。

**注意**：虽然底层类在技术上支持`decision`和`reason`等标准挂钩输出字段，但官方接口期望`hookSpecificOutput`与`permissionDecision`和`permissionDecisionReason`。

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Security policy blocks database writes",
    "additionalContext": "Current environment: production. Proceed with caution."
  }
}
```

#### PostToolUse

**用途**：在工具成功完成处理结果、记录结果或注入其他上下文后执行。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool that was executed",
  "tool_input": "object containing the tool's input parameters",
  "tool_response": "object containing the tool's response",
  "tool_use_id": "unique identifier for this tool use instance (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)",
  "duration_ms": "tool execution time in milliseconds, excluding approval (optional)"
}
```

**输出选项**：

- `decision`：“允许”、“拒绝”、“阻止”（如果未指定，则默认为“允许”）
- `reason`：决定的原因
- `hookSpecificOutput.additionalContext`：需包含的附加信息

**示例输出**：

```json
{
  "decision": "allow",
  "reason": "Tool executed successfully",
  "hookSpecificOutput": {
    "additionalContext": "File modification recorded in audit log"
  }
}
```

#### PostToolUseFailure

**用途**：当工具执行无法处理错误、发送警报或记录失败时执行。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_use_id": "unique identifier for the tool use (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)",
  "tool_name": "name of the tool that failed",
  "tool_input": "object containing the tool's input parameters",
  "error": "error message describing the failure",
  "is_interrupt": "boolean indicating if failure was due to user interruption (optional)",
  "duration_ms": "tool execution time in milliseconds when execution had started (optional)"
}
```

**输出选项**：

- `hookSpecificOutput.additionalContext`：错误处理信息，附加在模型接下来看到的工具结果中的错误之后。这在工具返回错误和抛出错误时都适用。对于从代码模式 `exec` 脚本进行的调用，它将转至 `exec` 调用的结果（请参阅 [PreToolUse](<#pretooluse>)）。 `<` 和 `>` 已转义。
- 标准钩子输出字段

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Error: File not found. Failure logged in monitoring system."
  }
}
```

#### PostToolBatch

**用途**：在一批中的所有工具调用都解决后运行一次，然后将其结果返回到模型。使用它来检查整个批次、为其添加上下文或停止它。

**匹配器**：无。 `PostToolBatch` 始终会触发，并且其上的 `matcher` 会被忽略。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_calls": [
    {
      "tool_name": "runtime tool id, for example run_shell_command",
      "tool_input": "object containing the tool's input parameters",
      "tool_use_id": "the call id from the model's request",
      "tool_call_id": "the same call id (optional)",
      "status": "success | error | cancelled",
      "tool_response": "object with response_parts, result_display, error, error_type, execution_status, content_length and, when present, vision_bridge_notice (optional)"
    }
  ]
}
```

**输出选项**：

- `hookSpecificOutput.additionalContext`：附加到批次中最后一次调用的结果中，在空行之后。 `<` 和 `>` 已转义。
- `decision`：`"block"`或`"deny"`停止批处理，`continue: false`或退出代码2也是如此。最后一次调用的结果将被替换为带有 `stopReason` 的错误，如果没有 `stopReason`，则替换为 `reason`。其他结果保留，并仍附加`additionalContext`。
- `stopReason` / `reason`：该错误的文本。

如果挂钩失败，或者没有在 15 秒内完成，则批处理将继续，其结果不变。

**示例输出**：

```json
{
  "decision": "block",
  "reason": "This batch edited files outside the allowed directory",
  "hookSpecificOutput": {
    "hookEventName": "PostToolBatch",
    "additionalContext": "Ask the user before retrying these edits."
  }
}
```

#### UserPromptSubmit

**用途**：在支持的模型调用之前执行，以验证、阻止或丰富其输入。在 core/headless 路径上，事件当前覆盖 `UserQuery`、`ToolResult` 和 `Hook` 发送，而 `Retry`、`Steer`、`Cron`、`Notification` 和`Teammate` 发送被跳过。因此，它可能发生在连续路径上，并且 `prompt` 不得假定为原始用户输入。 ACP会话路径有自己的调用策略：重试和新分派的后台任务仍然可以调用旧挂钩； continue、restored-question和runtime-goalturns不会。

**事件特定字段**：

```json
{
  "prompt": "legacy prompt for this invocation; semantics depend on the execution path",
  "submitted_prompt": "optional user text captured at a supported submission boundary"
}
```

`submitted_prompt` 是可选的。它存在于受支持的交互式 TUI 提交和首轮无头 `UserQuery` 发送中。在 ACP 客户端、`serve` 和守护进程主机使用的 ACP 会话路径上，新的轮次必须携带显式提交声明。缺失、非字符串、空或仅有空白的声明会省略该字段；该值永远不会从 `prompt` 或显示标签重建。重试、继续和通道分类轮次将忽略它。通道排除包括自动事件和通过通道适配器中继的人工消息。

Web Shell 在其提交边界提供原始作曲家文本。实时语音切换不会声明出处，因为它们的请求文本来自模型生成的工具参数。其他 ACP/daemon SDK 客户端可以根据请求选择 `_meta: { "qwen.submittedPrompt": "original submitted text" }`，在资源或仅模型扩展之前捕获。没有此声明的现有客户端将继续运行旧版挂钩，但不会触发来源控制的自动调用。不要将声明全局添加到 SDK 传输：计划任务、实时任务运行、子会话生成、模型创作的跨会话消息和提升的中间轮消息不得自动获取它。私有 `qwen.daemon.submittedPrompt` 密钥是为守护进程到子进程的跃点保留的，并且从外部调用者中剥离。这些声明是调用者提供的出处，而不是人类作者或授权的证明。

在 ACP 路径上，初始旧版 `prompt` 是请求的文本块，在资源、附件、斜杠命令或仅模型扩展之前以空格连接。它不公开完整的扩展模型输入。 `submitted_prompt` 可以等于该文本，但仅来自显式声明并保留其原始空白。在 core/headless 路径上，旧版 `prompt` 表示钩子调用的当前模型绑定文本。这两个区域都不是完整的 DLP 检查表面。

以下编写器规则适用于交互式 TUI，而不适用于 ACP 客户端。当字段的来源保持完整时，延迟输入可以保留该字段。仅当每个组成项目都有来源时，组合批次才会保留来源；已编辑、部分已知或其他不明确的输入会忽略该字段。提示符、命令和 shell 历史记录导航或选定的搜索匹配、交叉重新启动存储恢复和对话倒带恢复也会忽略它，因为这些路径可以显示模型绑定文本，而无需其原始出处。需要用户提交文本的消费者应将缺席视为不可用，而不是退回到 `prompt`。

清除或提交已恢复或出处不可用的模型绑定输入后，Composer 还会清除其撤消和重做历史记录。这可以防止撤消在其标记或边车被消耗后恢复扩展文本。

大粘贴占位符在 `submitted_prompt` 中保持紧凑；扩展的粘贴内容仅出现在`prompt`中。在该 TUI 路径上，消费者应该将该字段视为文本投影，而不是剪贴板输入的逐字节记录。 ACP 客户端没有等效的内置 Vim、粘贴占位符、历史记录或倒带出处跟踪；他们拥有无论是恢复还是编辑的文本都保留有效的提交声明。

启用 Vim 模式时出现的任何非空输入都会忽略 `submitted_prompt`，包括禁用 Vim 后，因为在此版本中 Vim 寄存器不携带出处。这个保守的规则也适用于启用 Vim 之前输入的草稿。清除作曲家会启动新的合格输入。

该字段是来源，而不是身份验证、租户身份、授权或 DLP。它是调用者提供的数据。为此事件配置的每个执行器都会收到它；特别是，HTTP 挂钩将其发送到其端点，而提示挂钩将其发送到其模型提供者。

当两个字段都存在时，提示挂钩有效负载包含重叠文本，并且可以消耗额外的模型输入标记。此版本中没有每个钩子场抑制。

顺序UserPromptSubmit钩子可以将`additionalContext`追加到`prompt`； `submitted_prompt` 继续代表捕获的提交。函数挂钩是可信的同进程代码，并且不受不变性保证的约束。

当最终的钩子输出包含非空`additionalContext`时，Qwen首先
清理该值，然后将其作为单独的文本部分发送到模型：

```xml
<qwen:user-prompt-submit-context>
sanitized hook context
</qwen:user-prompt-submit-context>
```

该标签告诉模型和脚本使用者该部分来自 
 配置的挂钩，而不是来自用户提示。它是来源标记，
不是身份验证、授权或一般信任边界。

对于添加了此上下文的 `UserQuery`，会话 JSONL 记录保留 
 模型绑定部分，包括标记部分，并添加以下 
`systemPayload`：

```json
{
  "displayText": "pre-hook display projection",
  "hookContext": "sanitized hook context"
}
```

这个两字段有效负载仅为此类用户提示记录而编写。
`hookContext`故意复制标记部分，以便离线，并且
第三方消费者无需解析模型文本即可识别其出处。
`displayText`是钩子前显示投影，从不包含钩子
上下文。在 core/headless 路径上，它是提交的投影（如果可用），否则是扩展的预挂钩提示。 ACP 当投影、附件引用或输入注释需要有效负载时，在扩展之前记录可信显示投影或原始请求文本；否则记录的用户消息不带`systemPayload`或`displayText`。

当 `systemPayload.hookContext` 是字符串时，转录显示消费者将 `displayText` 视为此用户提示投影 
。为了与仅发布的 
`displayText` 用户提示记录兼容，至少一个其他部分之后的最终 
 部分中的完整标记上下文是等效的配对证据。通知、
cron 和中轮记录也可以具有 `displayText`，但这些值是 
 紧凑显示标签，并且不得在没有该证据的情况下替换其模型绑定文本 
。
旧版裸上下文记录保留其模型绑定显示行为，因为
上下文无法可靠分离。对于使用
当前标记形状的无元数据记录，兼容性使用者可以删除相同的完整
最终标记部分；他们不得推断任意类似标签的用户文本是钩子
的来源。

敏感提示遥测属性（启用后）和托管自动内存 
 调用都使用预挂钩提示。它们不包括
`UserPromptSubmit`添加的上下文。

**输出选项**：

- `decision`：“允许”、“拒绝”、“阻止”或“询问”
- `reason`：人类可读的决策解释
- `hookSpecificOutput.additionalContext`：附加到提示的附加上下文（可选）

当发送到模型时，注入的 `additionalContext` 作为其自己的消息部分附加在保留的 `<qwen:user-prompt-submit-context>...</qwen:user-prompt-submit-context>` 标签中，因此它与模型历史记录和会话记录中用户编写的文本保持可区分。钩子输出中的尖括号在换行之前被转义，因此钩子内容无法关闭或伪造标签。会话笔录还单独记录了用户的原始提示文本；交互式 TUI 和 ACP/export 转录重播路径显示原始文本而不是注入的上下文。

**注意**：由于UserPromptSubmitOutput扩展了HookOutput，所以所有标准字段都可用，但只有hookSpecificOutput中的additionalContext专门为此事件定义。

**示例输出**：

```json
{
  "decision": "allow",
  "reason": "Prompt reviewed and approved",
  "hookSpecificOutput": {
    "additionalContext": "Remember to follow company coding standards."
  }
}
```

#### UserPromptExpansion

**用途**：当斜杠命令扩展为模型的提示时、扩展之后和发送提示之前运行。这涵盖提交提示的命令，例如自定义命令、技能、MCP 提示和内置命令（如 `/init`）。仅在本地起作用的命令不会触发它。它在交互式 UI、无头运行和 ACP 会话中以及当模型运行模型可调用命令时触发。

**匹配器**：与`command_name`匹配。例如，`"matcher": "^init$"` 仅针对 `/init` 触发。

**事件特定字段**：

```json
{
  "command_name": "slash command name without the leading /",
  "command_args": "argument text after the command name",
  "prompt": "the expanded prompt, with non-text parts rendered as text"
}
```

当无头消息或 ACP 消息同时调用多个技能时，`command_name` 是由空格连接的名称，`command_args` 是剩余的文本。

**输出选项**：

- `decision`：`"block"`或`"deny"`停止发送扩展提示，`continue: false`或退出代码2也是如此。用户看到`UserPromptExpansion blocked: <reason>`；对于模型调用的命令，该消息将作为错误返回。
- `reason` / `stopReason`：该消息中显示的文本。
- `hookSpecificOutput.additionalContext`：附加到扩展提示符后的空行之后。 `&`、`<`和`>`被转义，结果被剪切为10,000个字符。

使用纯文本 `stdout` 退出 0 的命令挂钩将该文本视为 `additionalContext`（请参阅 [退出代码行为 ](<#挂钩输出结构>)）。

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptExpansion",
    "additionalContext": "Follow the repository's CONTRIBUTING.md when generating QWEN.md."
  }
}
```

#### SessionStart

**用途**：新会话开始执行初始化任务时执行。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "source": "startup | resume | clear | compact",
  "model": "the model being used",
  "agent_type": "the type of agent if applicable (optional)"
}
```

**输出选项**：

- `hookSpecificOutput.additionalContext`：会话中可用的上下文
- 标准钩子输出字段

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Session started with security policies enabled."
  }
}
```

#### SessionEnd

**用途**：会话结束时执行，执行清理任务。

**事件特定字段**：

```json
{
  "reason": "clear | logout | prompt_input_exit | bypass_permissions_disabled | other"
}
```

**输出选项**：

- 标准钩子输出字段（通常不用于阻塞）

#### SessionDelete

**用途**：在永久删除明确选择的会话后运行。此事件是即发即弃的：输出和失败无法撤消删除。

**事件特定字段**：

```json
{
  "deleted_session_id": "the session that was deleted"
}
```

该挂钩使用删除运行时的正常会话字段（`session_id`、`transcript_path` 和 `cwd`）；在 ACP 上，`transcript_path` 为空，因为删除运行时没有自己的记录。 `SessionDelete` 当前针对交互式 `/delete` 流程和 ACP 的显式 `deleteSession` 方法触发；守护进程 REST 批量删除和内部清理不会发出它。如果 Qwen 在调度后退出，则命令钩子将完成；它的 stdout 和 stderr 被忽略并保持独立于 Qwen 的管道。

#### MessageDisplay

**用途**：在助理的回复流中重复触发——在`Stop`之前，在回合结束时触发一次。对于实时旁白、增量日志记录或任​​何想要对所写回复而不是事后做出反应的消费者很有用。这是一个 ** 即发即弃 ** 事件 - 挂钩输出和退出代码将被忽略。

**事件特定字段**：

```json
{
  "message_id": "stable id for the whole streamed message",
  "displayed_text": "the CUMULATIVE text streamed so far for this message (not a delta)",
  "is_final": "true on the last firing for this message, false otherwise"
}
```

`displayed_text` 是累积的而不是增量，因此钩子脚本永远不需要自己重新组装块 - 每次触发都携带到目前为止的全文。除了最终触发 (`is_final: true`) 之外，触发都会去抖（最多每约 200 毫秒），一旦消息结束，它总是触发，因此回复的尾部永远不会在等待去抖窗口时被丢弃。

**传递语义** — 钩子脚本可以依赖什么：

- **慢速钩子看到更少、更新的有效负载。**每条消息一次最多执行一个中流钩子；当其中一个运行时，较新的去抖有效负载 _ 会替换 _ 排队的有效负载，而不是堆积在其后面。因此，比反跳窗口慢的挂钩会跳过中间快照——无损，因为每个有效负载都携带完整的累积文本。
- **`is_final` 永远不会在过时的交付后面排队。** 最终的有效负载在消息结束的那一刻被调度 - 如果有一个仍在运行的中流执行（一次一个规则的一个例外，以同样的方式证明：最终的累积文本严格取代执行正在处理的任何内容）。您的钩子始终接收 `is_final` 有效负载，并在 `Stop` 钩子触发之前接收它。有状态钩子的一个后果是：当最终执行与被取代的中流执行重叠时，它们的 _ 完成 _ 顺序是未指定的 - 过时的执行可能会在最后一个执行之后完成（甚至在 `Stop` 之后）。将 `is_final` 视为每个 `message_id` 的终端，并让累积文本获胜，而不是假设最后一次完成的执行携带最新状态。
- **回合等待`is_final`交付完成 - 但不会永远。**回合结束（以及`Stop`钩子，当它触发时）最多等待5秒才能完成最终交付。在该预算内完成的挂钩保持最强有力的保证：无头运行 (`qwen -p ...`) 仅在挂钩完成后退出，并且 `is_final` 执行在 `Stop` 开始之前完成。较慢的钩子仍然首先接收 `is_final` - 只是等待其完成是有限的：在终端 UI 或 ACP 会话中，执行只是在后台完成，而无头运行则无需等待即可退出。钩子进程在退出时不会被杀死；它会自行完成，因此链接 `qwen -p … && next-step` 的脚本可以在慢速挂钩仍在运行时观察到 `next-step` 启动。达到此超时会在 stderr 上打印警告。
- **取消行为取决于时间。**在`is_final`调度_之前取消的回合_不会触发`is_final` - 消息被视为已放弃，并且消费者会缓冲直到`is_final`应将取消静音视为其 flush/discard 信号（例如超时回退）。标准是回合结束时中止信号的状态，而不是每个块是否已经流式传输 - 在该检查之前的短暂间隙中中止着陆仍然可以抑制 `is_final` 对于文本实际上已完成到达的消息。在`is_final`分派_（在耗尽等待期间）之后取消_是不同的：仍在运行的钩子执行可能会在飞行中终止（SIGTERM），但有效负载本身已经被传递。
- **`displayed_text` 是临时的，直到 `is_final`。** 它反映了迄今为止已流式传输的内容；将中间有效负载视为显示状态，而不是权威的最终内容。
- **使用工具的回合会产生多条消息。**每个模型调用都有自己的`message_id`和自己的`is_final: true`触发：工具调用之前的文本是一条消息，工具结果之后的延续是另一条消息。不产生显示文本的模型调用（仅工具调用）不会触发任何内容。

**注意**：在终端UI、无头(`-p`)和ACP(IDE/editor/`qwen serve`)会话中触发，每个表面上具有相同的有效负载合同。

#### 停止

**目的**：在Qwen结束响应以提供最终反馈或摘要之前执行。

**事件特定字段**：

```json
{
  "stop_hook_active": "true when this turn is continuing because a stop hook blocked the previous stop check (still true after tool calls made during that continuation); false on the first check and again once the stop is allowed, the blocking cap is reached, the user steers or sends new input, or a new turn, retry or goal turn starts",
  "last_assistant_message": "the last message from the assistant",
  "context_usage": "ratio of context window used (may exceed 1 when tokens exceed window; optional)",
  "context_limit": "context window size in tokens (optional)",
  "input_tokens": "prompt token count (may include output tokens depending on provider; optional)",
  "background_tasks": "array of background tasks, each with id, status, agent_type, started_at and optional description",
  "crons": "array of scheduled jobs, each with id, schedule, prompt, recurring, enabled and optional next_run and last_run"
}
```

`context_usage`、`context_limit` 和 `input_tokens` 字段允许钩子脚本观察上下文使用情况并实现自定义紧凑策略 - 例如，当使用量超过自定义阈值时打印运行 `/compact` 的提醒的脚本。

**输出选项**：

- `decision`：“允许”、“拒绝”、“阻止”或“询问”
- `reason`：人类可读的决策解释
- `stopReason`：要包含在停止响应中的反馈
- `continue`：设置为 false 停止执行
- `hookSpecificOutput.additionalContext`：附加上下文信息

**注意**：由于StopOutput扩展了HookOutput，所以所有标准字段都可用，但stopReason字段与此事件特别相关。

**阻止上限**：每次提示都会计算连续的阻止决策，并在工具往返期间持续计数，因此每次检查时阻止的钩子都会在`stopHookBlockingCap`阻止后结束转弯（默认8；`QWEN_CODE_STOP_HOOK_BLOCK_CAP`在一次运行中覆盖它）。计数和 `stop_hook_active` 共享相同的记录：当允许停止时、当您转向或发送新输入时、当重试转弯时、当目标转弯开始时以及当转弯异常结束时，都会重新启动。在非交互式文本模式下，Stop hook 消息和 cap 警告会写入 stderr； JSON输出不变。

**示例输出**：

```json
{
  "decision": "block",
  "reason": "Must be provided when Qwen Code is blocked from stopping"
}
```

#### StopFailure

**用途**：当API错误或循环检测结束回合时，代替`Stop`运行。 API 错误会在交互式 UI 和 ACP 会话中触发它，但不会在无头 (`-p`) 运行中触发。循环检测会在交互式 UI 和无头运行中触发它，但不会在 ACP 会话中触发它。这是一个**即发即弃**事件：Qwen不等待钩子，并且它们的输出和退出代码被忽略。

**事件特定字段**：

```json
{
  "error": "rate_limit | authentication_failed | billing_error | invalid_request | server_error | max_output_tokens | loop_detected | unknown",
  "error_details": "the error message, or the loop type for loop_detected (optional)",
  "last_assistant_message": "optional; the interactive UI currently sets it to the formatted error text"
}
```

对于 API 错误，`error` 源自 HTTP 状态和错误消息：状态 429 或“速率限制”给出 `rate_limit`； 401或“未经授权”给出`authentication_failed`； 402、403、“计费”或“配额”给出 `billing_error`； 400 或“无效”给出 `invalid_request`； 500 或以上的任何其他状态均给出 `server_error`； “max_tokens”或“代币限制”给出`max_output_tokens`；其他都是`unknown`。检查按该顺序运行。

**Matcher**：与 `error` 字段匹配。例如，`"matcher": "rate_limit"` 仅会因速率限制错误而触发。

**输出选项**：

- **无** - StopFailure 是一劳永逸的。所有挂钩输出、错误和退出代码都将被丢弃。

**退出代码处理**：

|退出代码|行为|
| --------- | ------------------------- |
|任意|被忽略（即发即忘）|

**配置示例**：

```json
{
  "hooks": {
    "StopFailure": [
      {
        "matcher": "rate_limit",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/rate-limit-alert.sh",
            "name": "rate-limit-alerter"
          }
        ]
      }
    ]
  }
}
```

**用例**：

- 速率限制监控和警报
- 身份验证失败日志记录
- 计费错误通知
- 错误统计收集

如果 Qwen 在调度后退出，则命令钩子将完成；它的 stdout 和 stderr 被忽略并保持独立于 Qwen 的管道。

#### SubagentStart

**用途**：启动子代理（如任务工具）以设置上下文或权限时执行。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "agent_id": "identifier for the subagent",
  "agent_type": "type of agent (Bash, Explorer, Plan, Custom, etc.)"
}
```

**输出选项**：

- `hookSpecificOutput.additionalContext`：子代理的初始上下文
- 标准钩子输出字段

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Subagent initialized with restricted permissions."
  }
}
```

#### SubagentStop

**用途**：当子代理完成执行终结任务时执行。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "stop_hook_active": "false on the first stop check; true when the subagent is continuing because a SubagentStop hook blocked its previous stop",
  "agent_id": "identifier for the subagent",
  "agent_type": "type of agent",
  "agent_transcript_path": "path to the subagent's transcript",
  "last_assistant_message": "the last message from the subagent",
  "background_tasks": "array of background tasks, same shape as in Stop",
  "crons": "array of scheduled jobs, same shape as in Stop"
}
```

**输出选项**：

- `decision`：“允许”、“拒绝”、“阻止”或“询问”
- `reason`：人类可读的决策解释

**示例输出**：

```json
{
  "decision": "block",
  "reason": "Must be provided when Qwen Code is blocked from stopping"
}
```

#### PreCompact

**用途**：在会话压缩之前执行，以准备或记录压缩。

**事件特定字段**：

```json
{
  "trigger": "manual | auto",
  "custom_instructions": "custom instructions currently set"
}
```

**输出选项**：

- `hookSpecificOutput.additionalContext`：压缩之前要包含的上下文
- 标准钩子输出字段

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Compacting conversation to maintain optimal context window."
  }
}
```

#### PostCompact

**用途**：在对话压缩成功后运行，以存档摘要或跟踪使用情况。当压缩失败时它不会触发。 Qwen 等待挂钩完成后再继续。

**事件特定字段**：

```json
{
  "trigger": "manual | auto",
  "compact_summary": "the summary that replaced the compacted history, without its <analysis> block"
}
```

**Matcher**：与 `trigger` 字段匹配：请求压缩时为 `manual`，例如与 `/compress` 匹配，当 Qwen 单独压缩时与 `auto` 匹配。例如，`"matcher": "manual"` 仅针对请求的压缩触发。

**输出选项**：

- **无** - PostCompact 输出被忽略。 `decision`、`continue`、`additionalContext` 和退出代码无效。

**配置示例**：

```json
{
  "hooks": {
    "PostCompact": [
      {
        "matcher": "manual",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/save-compact-summary.sh",
            "name": "save-summary"
          }
        ]
      }
    ]
  }
}
```

**用例**：

- 摘要归档到文件或数据库
- 使用统计跟踪
- 上下文变化监控
- 压缩操作的审核日志记录

#### 通知

**用途**：发送通知时执行，以自定义或拦截通知。

**事件特定字段**：

```json
{
  "message": "notification message content",
  "title": "notification title (optional)",
  "notification_type": "permission_prompt | idle_prompt | auth_success"
}
```

> **注意**：`elicitation_dialog`类型已定义但当前未实现。

**输出选项**：

- `hookSpecificOutput.additionalContext`：要包含的附加信息
- 标准钩子输出字段

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Notification processed by monitoring system."
  }
}
```

#### PermissionRequest

**用途**：显示权限对话框时执行，以自动决策或更新权限。

**事件特定字段**：

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool requesting permission",
  "tool_input": "object containing the tool's input parameters",
  "permission_suggestions": "array of suggested permissions (optional)"
}
```

**输出选项**：

- `hookSpecificOutput.decision`：具有权限决策详细信息的结构化对象：
  - `behavior`：“允许”或“拒绝”
  - `updatedInput`：修改刀具输入（可选）
  - `updatedPermissions`：修改权限（可选）
  - `message`：向用户显示的消息（可选）
  - `interrupt`：是否中断工作流程（可选）

**示例输出**：

```json
{
  "hookSpecificOutput": {
    "decision": {
      "behavior": "allow",
      "message": "Permission granted based on security policy",
      "interrupt": false
    }
  }
}
```

#### PermissionDenied

**用途**：当AUTO-模式分类器阻止工具调用时运行，包括当重复的阻止使Qwen回退到询问用户时运行。它报告拒绝，并且无法批准或更改呼叫。 Qwen 等待钩子后再继续；记录钩子失败并且不会改变结果。

**Matcher**：与工具 ID 匹配，与其他工具事件一样。

**事件特定字段**：

```json
{
  "tool_name": "runtime tool id of the denied call",
  "tool_input": "object containing the tool's input parameters",
  "tool_use_id": "the call id",
  "tool_call_id": "the same call id (optional)",
  "reason": "classifier_blocked | classifier_unavailable"
}
```

`reason` 当前始终为 `classifier_blocked`。 `classifier_unavailable` 已定义，但由于分类器未返回判决而导致调用被拒绝，因此不会触发此事件。

**输出选项**：

- **无** - PermissionDenied 输出和退出代码被忽略。

#### TodoCreated

**用途**：通过`todo_write`工具创建新的待办事项时执行。允许验证、记录或阻止待办事项创建。

Todo 挂钩分两个阶段运行：

- `validation`：在持久化之前运行。此阶段仅用于验证；返回 `block` 或 `deny` 会阻止写入。
- `postWrite`：持久化后运行。使用此阶段来产生副作用，例如日志记录或同步； `block` 或 `deny` 在此阶段被忽略。

**事件特定字段**：

```json
{
  "todo_id": "unique identifier for the todo item",
  "todo_content": "content/description of the todo item",
  "todo_status": "pending | in_progress | completed",
  "all_todos": "array of all todo items in the current list",
  "phase": "validation | postWrite"
}
```

**输出选项**：

- `decision`：“允许”、“阻止”或“拒绝”
- `reason`：人类可读的决策解释（阻塞时需要）

**阻止行为**：

在 `validation` 阶段，当 `decision` 为 `block` 或 `deny`（退出代码 2）时，将阻止创建待办事项。待办事项列表保持不变，并将原因作为反馈提供给模型。

在`postWrite`阶段，待办事项已经被持久化。挂钩可能仍会返回输出，但 `block` / `deny` 不会撤消写入，并且不应用于验证。

**示例输出（允许）**：

```json
{
  "decision": "allow",
  "reason": "Todo content validated successfully"
}
```

**示例输出（块）**：

```json
{
  "decision": "block",
  "reason": "Todo content too short. Minimum 5 characters required."
}
```

**挂钩脚本示例**：

```bash
#!/bin/bash
# ~/.qwen/hooks/todo-validator.sh
# Validates todo content before creation

INPUT=$(cat)
CONTENT=$(echo "$INPUT" | jq -r '.todo_content')

# Check minimum length
if [ ${#CONTENT} -lt 5 ]; then
  echo '{"decision": "block", "reason": "Todo content must be at least 5 characters"}'
  exit 2
fi

# Block test-related todos
if [[ "$CONTENT" =~ "test" ]]; then
  echo '{"decision": "block", "reason": "Test todos are not allowed in production"}'
  exit 2
fi

echo '{"decision": "allow"}'
exit 0
```

**配置示例**：

```json
{
  "hooks": {
    "TodoCreated": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.qwen/hooks/todo-validator.sh",
            "name": "todo-validator",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

#### TodoCompleted

**用途**：当待办事项标记为已完成时执行。允许验证、记录或阻止待办事项完成。

Todo 挂钩分两个阶段运行：

- `validation`：在持久化之前运行。此阶段仅用于验证；返回 `block` 或 `deny` 会阻止写入。
- `postWrite`：持久化后运行。使用此阶段来产生副作用，例如日志记录或同步； `block` 或 `deny` 在此阶段被忽略。

**事件特定字段**：

```json
{
  "todo_id": "unique identifier for the todo item",
  "todo_content": "content/description of the todo item",
  "previous_status": "pending | in_progress (status before completion)",
  "all_todos": "array of all todo items in the current list",
  "phase": "validation | postWrite"
}
```

**输出选项**：

- `decision`：“允许”、“阻止”或“拒绝”
- `reason`：人类可读的决策解释（阻塞时需要）

**阻止行为**：

在 `validation` 阶段，当 `decision` 为 `block` 或 `deny`（退出代码 2）时，将阻止 todo 完成。待办事项仍保持其先前的状态，并将原因作为反馈提供给模型。

在`postWrite`阶段，待办事项已经被持久化。挂钩可能仍会返回输出，但 `block` / `deny` 不会撤消写入，并且不应用于验证。

**示例输出（允许）**：

```json
{
  "decision": "allow",
  "reason": "Todo completion approved"
}
```

**示例输出（块）**：

```json
{
  "decision": "block",
  "reason": "Cannot complete this todo until dependent tasks are finished."
}
```

**挂钩脚本示例**：

```bash
#!/bin/bash
# ~/.qwen/hooks/todo-completion-validator.sh
# Validates todo completion conditions

INPUT=$(cat)
TODO_ID=$(echo "$INPUT" | jq -r '.todo_id')
ALL_TODOS=$(echo "$INPUT" | jq -r '.all_todos')

# Check if there are incomplete dependent todos (example logic)
INCOMPLETE_COUNT=$(echo "$ALL_TODOS" | jq '[.[] | select(.status != "completed")] | length')

if [ "$INCOMPLETE_COUNT" -gt 5 ]; then
  echo '{"decision": "block", "reason": "Too many incomplete todos. Complete other tasks first."}'
  exit 2
fi

echo '{"decision": "allow"}'
exit 0
```

**配置示例**：

```json
{
  "hooks": {
    "TodoCompleted": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.qwen/hooks/todo-completion-validator.sh",
            "name": "completion-validator",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**用例**：

- **日志记录**：跟踪待办事项的创建和完成情况以进行审核或分析
- **验证**：执行内容质量标准（最小长度、必需的关键字）
- **工作流程控制**：阻止完成，直到满足先决条件
- **集成**：与外部任务管理系统（Jira、Trello等）同步待办事项

#### InstructionsLoaded

**用途**：针对加载到系统提示符中的每个上下文文件Qwen（例如`QWEN.md`）以及这些文件导入的每个文件运行。使用它来审核会话使用哪些指令文件。它是信息性的：它不能停止或更改加载。

当会话启动时、在会话期间重新加载上下文文件时、以及在读取文件后通过导入拉入的每个文件时，它都会触发。 Qwen 等待钩子后再继续加载；记录挂钩失败并且不会停止加载。

**匹配器**：与`file_path`匹配。例如，`"matcher": "QWEN\\.local\\.md$"` 仅针对本地上下文文件触发。

**事件特定字段**：

```json
{
  "file_path": "path of the loaded file",
  "memory_type": "user | project | local | extension",
  "load_reason": "session_start | include | refresh",
  "trigger_file_path": "for include: the context file whose loading pulled this file in (optional)",
  "parent_file_path": "for include: the file that contains the import (optional)"
}
```

对于初始加载，`load_reason` 为 `session_start`，重新加载上下文文件时为 `refresh`，对于通过导入到达的文件为 `include`。

`memory_type` 是 `extension` 表示扩展的上下文文件及其目录中的文件，`user` 表示全局 `.qwen` 目录中或直接位于主目录中的文件，`local` 表示项目根目录中的 `.qwen/QWEN.local.md`，以及`project` 对于任何其他文件。导入的文件与导入它的上下文文件具有相同的 `memory_type`。

**输出选项**：

- **无** - InstructionsLoaded 输出和退出代码被忽略。

## 挂钩配置

挂钩在 Qwen Code 设置中配置，通常在 `.qwen/settings.json` 或用户配置文件中。系统设置文件（系统和 SystemDefaults）中的挂钩随源系统一起加载，并且与用户挂钩一样，无论文件夹信任如何。在一个事件中，来自设置和扩展的连续挂钩按以下顺序运行：项目、用户、系统、扩展。

> [!NOTE]
> 挂钩必须包含要在运行时单独启用或禁用的名称字段。当满足事件和匹配器条件时，没有名称的挂钩将始终执行。

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "^run_shell_command$",
        "sequential": false,
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/security-check.sh",
            "name": "security-check",
            "description": "Run security checks before tool execution",
            "timeout": 30
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Session started'",
            "name": "session-init"
          }
        ]
      }
    ]
  }
}
```

## 钩子执行

### 并行与顺序执行

- 默认情况下，挂钩并行执行以获得更好的性能
- 在钩子定义中使用 `sequential: true` 强制执行顺序相关的执行
- 顺序钩子可以修改链中后续钩子的输入

### 异步钩子

仅`command`类型支持异步执行。设置 `"async": true` 在后台运行钩子而不阻塞主流程。

异步挂钩的范围是 Qwen 进程。在 POSIX 上，Qwen 在退出时回收仍在运行的异步钩子进程树，但其部分明确保证退出后即发即弃完成的事件类型除外。 Windows 在根退出后无法重建后代树，因此完整的父级退出回收需要作业对象或后代跟踪。

**特点：**

- 无法返回决策控制（挂钩一启动事件就会继续）
- 输出尚未交付：来自异步挂钩的 `systemMessage`、`additionalContext` 和普通 `stdout` 既不向用户显示，也不添加到模型上下文中。调试日志仅记录挂钩启动、完成或失败的情况。计划交付此产出。
- 适用于副作用，例如审核、日志记录和后台测试，将其结果写入您可以读取的地方
- 占用 10 并发异步挂钩槽之一，直到完成或达到其 `timeout`（默认为 60 秒）。当所有 10 插槽都在使用时，会跳过新的异步挂钩

**示例：**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "write_file|edit",
        "hooks": [
          {
            "type": "command",
            "command": "\"$QWEN_PROJECT_DIR/.qwen/hooks/run-tests-async.sh\"",
            "async": true,
            "timeout": 300
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
if [[ "$FILE_PATH" != *.ts && "$FILE_PATH" != *.js ]]; then exit 0; fi
# Async hook output is not shown yet, so write the result to a file.
LOG=/tmp/qwen-async-tests.log
if npm test >"$LOG" 2>&1; then
  echo "Tests passed after editing $FILE_PATH" >>"$LOG"
else
  echo "Tests failed after editing $FILE_PATH" >>"$LOG"
fi
```

### 安全模型

- 挂钩在用户环境中以用户权限运行
- 项目级挂钩需要受信任的文件夹状态
- 超时防止挂起挂钩（默认值：命令挂钩的 60 秒）

## 最佳实践

### 示例 1：安全验证挂钩

一个 PreToolUse 钩子，用于记录并可能阻止危险命令：

**security_check.sh**

```bash
#!/bin/bash

# Read input from stdin
INPUT=$(cat)

# Parse the input to extract tool info
TOOL_NAME=$(echo "$INPUT" | jq -r '.tool_name')
TOOL_INPUT=$(echo "$INPUT" | jq -r '.tool_input')

# Check for potentially dangerous operations
if echo "$TOOL_INPUT" | grep -qiE "(rm.*-rf|mv.*\/|chmod.*777)"; then
  echo '{
    "hookSpecificOutput": {
      "hookEventName": "PreToolUse",
      "permissionDecision": "deny",
      "permissionDecisionReason": "Security policy blocks dangerous command"
    }
  }'
  exit 2  # Blocking error
fi

# Log the operation
echo "INFO: Tool $TOOL_NAME executed safely at $(date)" >> /var/log/qwen-security.log

# Allow with additional context
echo '{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "Security check passed",
    "additionalContext": "Command approved by security policy"
  }
}'
exit 0
```

在`.qwen/settings.json`中配置：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "${SECURITY_CHECK_SCRIPT}",
            "name": "security-checker",
            "description": "Security validation for bash commands",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### 示例 2：HTTP 审核挂钩

将所有工具执行记录发送到远程审核服务的 PostToolUse HTTP 挂钩：

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "http",
            "url": "https://audit.example.com/api/tool-execution",
            "headers": {
              "Authorization": "Bearer ${AUDIT_API_TOKEN}",
              "Content-Type": "application/json"
            },
            "allowedEnvVars": ["AUDIT_API_TOKEN"],
            "timeout": 10,
            "name": "audit-logger"
          }
        ]
      }
    ]
  }
}
```

### 示例 3：提交的提示验证挂钩

在 core/headless 路径上，`prompt` 可以包含生成或扩展的内容，而不是原始用户输入。在 ACP 上，它以预扩展请求文本开头，因此读取它不会检查附件主体或完整的模型输入。 【`UserPromptSubmit`】不涵盖所有型号发送。当需要来源时，不要默默地从 `submitted_prompt` 回退到 `prompt`。

一个 UserPromptSubmit 挂钩，用于验证支持的提交文本并为长提示提供上下文。它还可以在无头提交和显式声明的 ACP/daemon 提交上运行；它不仅仅是TUI-。它会跳过源来源不可用的调用。阻塞结果会停止受影响的调用，包括在这些非 TUI 路径上的调用。关键字检查只是说明性的，并不是完整的 DLP 策略：

**prompt_validator.py**

```python
import json
import sys
import re

# Load input from stdin
try:
    input_data = json.load(sys.stdin)
except json.JSONDecodeError as e:
    print(f"Error: Invalid JSON input: {e}", file=sys.stderr)
    sys.exit(1)

user_prompt = input_data.get("submitted_prompt")
if user_prompt is None:
    # Do not mistake model-bound or machine-generated content for raw input.
    sys.exit(0)

# Sensitive words list
sensitive_words = ["password", "secret", "token", "api_key"]

# Check for sensitive information
for word in sensitive_words:
    if re.search(rf"\b{word}\b", user_prompt.lower()):
        # Block prompts containing sensitive information
        output = {
            "decision": "block",
            "reason": f"Prompt contains sensitive information '{word}'. Please remove sensitive content and resubmit.",
            "hookSpecificOutput": {
                "hookEventName": "UserPromptSubmit"
            }
        }
        print(json.dumps(output))
        sys.exit(0)

# Check prompt length and add warning context if too long
if len(user_prompt) > 1000:
    output = {
        "hookSpecificOutput": {
            "hookEventName": "UserPromptSubmit",
            "additionalContext": "Note: User submitted a long prompt. Please read carefully and ensure all requirements are understood."
        }
    }
    print(json.dumps(output))
    sys.exit(0)

# No processing needed for normal cases
sys.exit(0)
```

## 故障排除

### 钩子不发射

1. **事件名称。** 键必须是 [Hook 事件](<#挂钩事件>) 中的事件之一，拼写准确，包括大小写。跳过未知事件名称并显示警告 `Invalid hook event name`。
2. **Matcher.** 工具事件与运行时工具 ID 匹配，例如 `run_shell_command` 或 `write_file`，或可接受的别名，例如 Claude Code 的 `Bash` 或 `Write`（请参阅[匹配器模式](<#匹配器模式>)）。对于没有匹配器支持的事件，`matcher` 将被忽略。
3. **文件夹信任。** 仅当文件夹受信任时，才会加载项目的 `.qwen/settings.json` 中的挂钩。无论文件夹信任如何，用户挂钩都会加载。
4. **挂钩已禁用。**、`"disableAllHooks": true`、`--safe-mode`（或`QWEN_CODE_SAFE_MODE`）和`--bare`（或`QWEN_CODE_SIMPLE`）关闭所有挂钩，包括用户挂钩。
5. **调试日志。**使用`--debug`启动Qwen Code，或设置`QWEN_DEBUG_LOG_FILE=1`，然后读取`~/.qwen/debug/latest`处的会话日志（设置时在`$QWEN_RUNTIME_DIR`下）。每行都标有一个名称空间：
   - `[HOOK_REGISTRY]`：`Hook registry initialized with N hook entries`，并且钩子定义被视为无效而被丢弃
   - `[TRUSTED_HOOKS]`：`Hook <name> started for event <event>` 以及每次运行的匹配 `ended` 行、异步挂钩状态和挂钩系统消息
   - `[HOOK_MATCHER]`：不是有效正则表达式的匹配器
   - `[HOOK_TIMEOUT]`：命令挂钩 `timeout` 值读取为毫秒或被忽略
   - `[HTTP_HOOK_RUNNER]`、`[URL_VALIDATOR]`、`[PROMPT_HOOK_RUNNER]`、`[FUNCTION_HOOK_RUNNER]`、`[SKILL_HOOKS]`、`[SESSION_HOOKS_MANAGER]`、`[ASYNC_HOOK_REGISTRY]` 和 `[HOOK_AGGREGATOR]`：来自各个参赛者和报名处的详细信息

提示挂钩输入可以写入会话调试日志，因此应用适当的访问和保留控制。

6. **在会话期间编辑。** 会话启动时读取挂钩。编辑挂钩定义后，打开交互式 `/hooks` 菜单一次以重新加载它们，或重新启动 Qwen Code。对挂钩控件和 HTTP 安全设置的更改需要重新启动。

### 其他检查

- 验证钩子脚本权限和可执行性
- 确保挂钩输出中的 JSON 格式正确
- 使用特定的匹配器模式来避免意外的钩子执行
- 暂时禁用所有钩子：在设置中添加`"disableAllHooks": true`


## 原文对照

<details>
<summary>展开完整原文</summary>

# Qwen Code Hooks

## Overview

Qwen Code hooks provide a powerful mechanism for extending and customizing the behavior of the Qwen Code application. Hooks allow users to execute custom scripts or programs at specific points in the application lifecycle, such as before tool execution, after tool execution, at session start/end, and during other key events.

Hooks are enabled by default. You can temporarily disable all hooks by setting `disableAllHooks` to `true` in your settings file (at the top level, alongside `hooks`):

```json
{
  "disableAllHooks": true,
  "hooks": {
    "PreToolUse": [...]
  }
}
```

This disables all hooks without deleting their configurations.

## What are Hooks?

Hooks are user-defined scripts or programs that are automatically executed by Qwen Code at predefined points in the application flow. They allow users to:

- Monitor and audit tool usage
- Enforce security policies
- Inject additional context into conversations
- Customize application behavior based on events
- Integrate with external systems and services
- Modify tool inputs or responses programmatically

### Browsing your hooks

Run `/hooks` to open a read-only browser of the hooks this session runs. It moves from events to matchers to the individual hooks under a matcher; events without matcher support go straight to their hooks. A hook's details show its type, where it comes from, whether it is enabled, its command, URL or prompt, and, when they are set, its timeout, status message, HTTP `if` condition and whether it runs once or in the background. Hooks registered for the current session by skills or the SDK are listed with the source Session.

The browser never edits your configuration: to add, change or remove a hook, edit `settings.json`. It does re-read it. Opening the interactive menu reloads hook definitions from the user, workspace and system (System and SystemDefaults) settings files used by this session, including when the session runs in a worktree, so definitions you added, changed or removed since the session started take effect without a restart. If the user or workspace file cannot be read or parsed, both previous settings snapshots and the running hooks are retained, the files are left untouched, and an error is shown. If a system settings file cannot be read or parsed, the hooks from the system files stay as they were, your other edits still take effect, the file is left untouched, and an error names it.

Reloading requires this explicit menu-open action: saving a file, pulling changes or switching branches does not automatically arm new hook commands. The non-interactive `/hooks list` only displays the registry currently loaded by that process; it does not reload settings. In an interactive terminal, `/hooks list` opens the same menu as `/hooks`.

This reload covers hook definitions, not hook controls or HTTP security settings. Changes to `disableAllHooks`, `stopHookBlockingCap`, `security.allowedHttpHookUrls` and `security.allowPrivateNetworkHooks` still require a restart. Hooks registered at runtime by skills or the SDK are not affected. Project hooks load only in a trusted folder. When hooks are turned off by `disableAllHooks`, `--safe-mode` or `--bare`, none load and the browser says so at the top.

### Agent frontmatter scope

Hooks declared in an agent's frontmatter apply only to that invocation, including
its `SubagentStart` and `SubagentStop` events. They do not inherit into nested
agents or run for parents and siblings. Global settings hooks and session-wide
skill/function hooks retain their existing scope. Project-agent hooks recheck
the trust of the workspace that supplied the agent before every event.

Use `SubagentStop` for agent completion; `Stop` is not automatically remapped.
Move an agent hook to settings if it should observe the whole session.

## Hook Types

Qwen Code supports four hook executor types:

| Type       | Description                                                                                    |
| :--------- | :--------------------------------------------------------------------------------------------- |
| `command`  | Execute a shell command. Receives JSON via `stdin`, returns results via `stdout`.              |
| `http`     | Send JSON as a `POST` request body to a specified URL. Returns results via HTTP response body. |
| `function` | Directly call a registered JavaScript function (session-level hooks only).                     |
| `prompt`   | Use an LLM to evaluate hook input and return a decision.                                       |

### Command Hooks

Command hooks execute commands via child processes. Input JSON is passed through stdin, and output is returned via stdout.

**Configuration:**

| Field           | Type                     | Required | Description                                 |
| :-------------- | :----------------------- | :------- | :------------------------------------------ |
| `type`          | `"command"`              | Yes      | Hook type                                   |
| `command`       | `string`                 | Yes      | Command to execute                          |
| `name`          | `string`                 | No       | Hook name (for logging)                     |
| `description`   | `string`                 | No       | Hook description                            |
| `timeout`       | `number`                 | No       | Timeout in seconds, default 60              |
| `async`         | `boolean`                | No       | Whether to run asynchronously in background |
| `env`           | `Record<string, string>` | No       | Environment variables                       |
| `shell`         | `"bash" \| "powershell"` | No       | Shell to use                                |
| `statusMessage` | `string`                 | No       | Status message displayed during execution   |

`timeout` is in seconds for command, HTTP and prompt hooks; SDK-registered function hooks keep milliseconds. Command hook timeouts used to be written in milliseconds, so for command hooks a value of `1000` or more is still read as milliseconds and existing settings keep working. To migrate, look for command hooks whose `timeout` is `1000` or more and rewrite the value in seconds, for example `10000` as `10`. To give a command hook a timeout of 1000 seconds or more, keep writing it in milliseconds, for example `1800000` for 30 minutes. A command hook `timeout` that is not a positive number, such as `"30s"`, is ignored and the 60 second default applies. With debug logging enabled (`QWEN_DEBUG_LOG_FILE=1`), each command hook with a millisecond or ignored `timeout` is named once per session in that session's debug log.

**Example:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "write_file",
        "hooks": [
          {
            "type": "command",
            "command": "\"$QWEN_PROJECT_DIR/.qwen/hooks/security-check.sh\"",
            "name": "security-check",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

`QWEN_PROJECT_DIR`, `CLAUDE_PROJECT_DIR` and `GEMINI_PROJECT_DIR` are set to the project directory in the environment of every command hook. Bash hooks read them from the environment, so double-quote them like any other shell variable, as in `"$QWEN_PROJECT_DIR/.qwen/hooks/security-check.sh"`. For cmd hooks, and for a bare `$QWEN_PROJECT_DIR` in PowerShell hooks, the variable is replaced with the quoted project directory before the command runs; `$env:QWEN_PROJECT_DIR` also works in PowerShell.

Migration: bash hooks used to have these variables replaced in the command text before the shell ran, and that replacement has been removed. A bash hook that leaves the variable unquoted in a project path containing spaces, or writes it inside single quotes such as `'$QWEN_PROJECT_DIR/hook.sh'`, must now double-quote it: `"$QWEN_PROJECT_DIR/hook.sh"`.

### HTTP Hooks

HTTP hooks send hook input as POST requests to specified URLs. They support URL whitelists, DNS-level SSRF protection, environment variable interpolation, and other security features.

**Configuration:**

| Field            | Type                     | Required | Description                                               |
| :--------------- | :----------------------- | :------- | :-------------------------------------------------------- |
| `type`           | `"http"`                 | Yes      | Hook type                                                 |
| `url`            | `string`                 | Yes      | Target URL                                                |
| `headers`        | `Record<string, string>` | No       | Request headers (supports env var interpolation)          |
| `allowedEnvVars` | `string[]`               | No       | Whitelist of environment variables allowed in URL/headers |
| `timeout`        | `number`                 | No       | Timeout in seconds, default 600                           |
| `name`           | `string`                 | No       | Hook name (for logging)                                   |
| `statusMessage`  | `string`                 | No       | Status message displayed during execution                 |
| `once`           | `boolean`                | No       | Execute only once per event per session (HTTP hooks only) |

**Security Features:**

- **URL Whitelist**: Configure allowed URL patterns via `allowedUrls`
- **SSRF Protection**: Blocks private IPs (10.x.x.x, 172.16-31.x.x, 192.168.x.x, etc.) but allows loopback addresses (127.0.0.1, ::1)
- **DNS Validation**: Validates domain resolution before requests to prevent DNS rebinding attacks
- **Environment Variable Interpolation**: `${VAR}` syntax, only allows variables in `allowedEnvVars` whitelist

#### Allowing private-network hooks (managed environments only)

By default, HTTP hooks cannot target private or link-local IP ranges. In platform-managed environments where the hook receiver is a first-party, VPC-internal endpoint (for example, an internal API gateway resolving to `172.16.0.0/12`), you can relax the IP-range checks with:

```json
{
  "security": {
    "allowPrivateNetworkHooks": true
  }
}
```

- This setting is **only honored from User, System, and SystemDefaults settings scopes**. A value set in Workspace (project) settings is ignored and logged as a warning, so a cloned repository can never self-grant this bypass.
- The flag relaxes only the general private/CGNAT/link-local **range** checks. Cloud metadata endpoints stay blocked in every configuration: the `BLOCKED_HOSTS` list is matched literally (`metadata.google.internal`, `metadata.azure.internal`, ...), and the metadata IPs `169.254.169.254` and `100.100.100.200` are blocked in all serialized forms (including IPv4-mapped IPv6 such as `::ffff:a9fe:a9fe`) and after DNS resolution.
- The `security.allowedHttpHookUrls` whitelist still applies independently. In managed environments, pair this flag with a whitelist so only the intended internal endpoints are reachable. A whitelist in Workspace (project) settings is honored only when no User, System, or SystemDefaults scope sets one; otherwise it is ignored and logged as a warning, so a repository can narrow where its hooks send data but never replace a whitelist you configured (an empty whitelist means "allow all").
- HTTP hooks never follow redirects. A 3xx response is treated like any other non-2xx status: a non-blocking hook failure, and the redirect target is never contacted.

> **Warning:** Enabling this flag lets hooks reach internal infrastructure on your network. Enable it only in trusted, managed settings — never in a repository you do not control.

**Example:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "http",
            "url": "http://127.0.0.1:8080/hooks/pre-tool-use",
            "headers": {
              "Authorization": "Bearer ${HOOK_API_KEY}"
            },
            "allowedEnvVars": ["HOOK_API_KEY"],
            "timeout": 10,
            "name": "remote-security-check"
          }
        ]
      }
    ]
  }
}
```

**Example: External Judgment Service Adapter**

The `remote-security-check` config above expects `http://127.0.0.1:8080/hooks/pre-tool-use` to
already be running a service that speaks this contract (POST `{tool_name, tool_input, ...}` in,
`hookSpecificOutput.permissionDecision` out). Here is a minimal, stdlib-only adapter that fills
in that missing half, wired to one concrete judgment backend so the whole thing is runnable and
testable end to end rather than a stub. Only the `review()` function is backend-specific — swap
its body and request/response shape for whichever service you use; everything else (the server,
the fail-open handling, the hook response shape) stays the same regardless of backend.

_Disclosure: the backend used below, [invinoveritas](<https://api.babyblueviper.com>), is a
service the author is affiliated with — used here because it was the one that could be
verified end to end for this example, not an endorsement. Any HTTP service that returns a
JSON verdict works equally well; only `review()` needs to change._

_Data handling: with `matcher: "*"`, the full `tool_input` of **every** tool call is sent to
the judgment backend — treat that input as sensitive (it may contain file contents, paths, or
secrets). Narrow the matcher (e.g. to `run_shell_command`) if you only need to judge shell
commands._

```python
#!/usr/bin/env python3
# judgment_hook.py -- run: JUDGMENT_API_KEY=... python3 judgment_hook.py
import json, os, sys, urllib.request
from http.server import BaseHTTPRequestHandler, HTTPServer

JUDGMENT_API_KEY = os.environ["JUDGMENT_API_KEY"]
JUDGMENT_URL = os.environ.get("JUDGMENT_URL", "https://api.babyblueviper.com/review")

def review(tool_name, tool_input):
    """POST the call to the judgment backend and return its verdict. This is the
    one function to change for a different backend -- request/response shape
    below matches invinoveritas's /review; adapt both to your own backend's
    contract if you swap it out."""
    body = json.dumps({
        "artifact": json.dumps({"tool_name": tool_name, "tool_input": tool_input}),
        "artifact_type": "shell_command" if tool_name in ("run_shell_command", "shell") else "general",
        "context": f"qwen-code PreToolUse: {tool_name}",
    }).encode()
    req = urllib.request.Request(
        JUDGMENT_URL, data=body,
        headers={"Authorization": f"Bearer {JUDGMENT_API_KEY}", "Content-Type": "application/json"},
    )
    # Keep this below the HTTP hook's own timeout (10s in the config above), so a "deny"
    # verdict is always returned before the hook gives up and fails open on its own.
    with urllib.request.urlopen(req, timeout=8) as resp:
        return json.loads(resp.read())  # response includes a "verdict" field: "reject" denies, anything else allows

class Handler(BaseHTTPRequestHandler):
    def do_POST(self):
        payload = json.loads(self.rfile.read(int(self.headers.get("Content-Length", 0))) or b"{}")
        tool_name, tool_input = payload.get("tool_name", "unknown"), payload.get("tool_input", {})
        try:
            verdict = review(tool_name, tool_input)
            decision = "deny" if verdict.get("verdict") == "reject" else "allow"
            reason = verdict.get("summary", f"judgment verdict: {verdict.get('verdict')}")
        except Exception as e:
            decision, reason = "allow", "judgment backend unavailable, failing open"  # never block on a review-side outage
            print(f"judgment backend unavailable for {tool_name}, failing open: {e}", file=sys.stderr)
        out = {"continue": True, "decision": decision, "hookSpecificOutput": {
            "hookEventName": "PreToolUse", "permissionDecision": decision, "permissionDecisionReason": reason,
        }}
        body = json.dumps(out).encode()
        self.send_response(200)
        self.send_header("Content-Type", "application/json")
        self.send_header("Content-Length", str(len(body)))
        self.end_headers()
        self.wfile.write(body)
    def log_message(self, *a): pass

if __name__ == "__main__":
    HTTPServer(("127.0.0.1", 8080), Handler).serve_forever()
```

Tested live end to end against the real production API above: a genuinely destructive input
(`{"tool_name": "run_shell_command", "tool_input": {"command": "rm -rf /important_data"}}`)
returned `permissionDecision: "deny"` with a real explanation; a benign one (`ls -la`) returned
`"allow"`. Fails open on any network/timeout/malformed-response issue from the judgment
backend, so an outage never blocks legitimate tool calls — same discipline the `command`-hook
examples above apply with their own exit codes.

### Function Hooks

Function hooks directly call registered JavaScript/TypeScript functions. They are used internally by the Skill system and are not currently exposed as a public API for end users.

**Note**: For most use cases, use **command hooks** or **HTTP hooks** instead, which can be configured in settings files.

### Prompt Hooks

Prompt hooks use an LLM to evaluate hook input and return a decision. This is useful for making intelligent decisions based on context, such as determining whether to allow or block an operation.

> **Data handling:** A prompt hook sends its event input to the configured model provider. When file-backed debug logging is enabled, the fully expanded prompt-hook request is also written to the session debug log. Treat hook input and debug logs as potentially sensitive.

**How it works:**

1. The hook input JSON is injected into your prompt using the `$ARGUMENTS` placeholder
2. The prompt is sent to an LLM (default: your current model)
3. The LLM returns a JSON response with the decision
4. Qwen Code processes the decision and continues or blocks execution accordingly

**Configuration:**

| Field           | Type       | Required | Description                                         |
| :-------------- | :--------- | :------- | :-------------------------------------------------- |
| `type`          | `"prompt"` | Yes      | Hook type                                           |
| `prompt`        | `string`   | Yes      | Prompt sent to LLM. Use `$ARGUMENTS` for hook input |
| `model`         | `string`   | No       | Model to use (defaults to your current model)       |
| `timeout`       | `number`   | No       | Timeout in seconds, default 30                      |
| `name`          | `string`   | No       | Hook name (for logging)                             |
| `description`   | `string`   | No       | Hook description                                    |
| `statusMessage` | `string`   | No       | Status message displayed during execution           |

**Response Format:**

The LLM must return JSON with the following structure:

```json
{
  "ok": true,
  "reason": "Explanation of the decision",
  "additionalContext": "Optional context to inject into the conversation"
}
```

| Field               | Description                                                                |
| :------------------ | :------------------------------------------------------------------------- |
| `ok`                | `true` to allow/continue, `false` to block/stop                            |
| `reason`            | Required when `ok` is `false`. Shown to the model to explain the block     |
| `additionalContext` | Optional. Additional context to inject into the conversation when allowing |

**Supported Events:**

Prompt hooks can be used with most hook events, including:

- `PreToolUse` - Evaluate whether to allow a tool call
- `PostToolUse` - Evaluate tool results and potentially inject context
- `Stop` - Determine whether to continue or stop
- `SubagentStop` - Evaluate subagent results
- `UserPromptSubmit` - Evaluate or enrich eligible model-bound prompts

**Example: Stop Hook**

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "You are evaluating whether Qwen Code should stop working. Context: $ARGUMENTS\n\nAnalyze the conversation and determine if:\n1. All user-requested tasks are complete\n2. Any errors need to be addressed\n3. Follow-up work is needed\n\nRespond with JSON: {\"ok\": true} to allow stopping, or {\"ok\": false, \"reason\": \"your explanation\"} to continue working.",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

When `ok` is `false`, Qwen Code will continue working and use the `reason` as context for the next response.

**Example: PreToolUse Hook**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "run_shell_command",
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Evaluate this tool call for security concerns. Tool input: $ARGUMENTS\n\nCheck for:\n- Dangerous commands (rm -rf, curl | sh, etc.)\n- Unauthorized access attempts\n- Data exfiltration patterns\n\nRespond with {\"ok\": true} if safe, or {\"ok\": false, \"reason\": \"concern\"} if blocked.",
            "model": "sonnet",
            "timeout": 30,
            "name": "security-evaluator"
          }
        ]
      }
    ]
  }
}
```

## Hook Events

Hooks fire at specific points during a Qwen Code session. Different events support different matchers to filter trigger conditions.

| Event                 | Triggered When                                                         | Matcher Target                                                   |
| :-------------------- | :--------------------------------------------------------------------- | :--------------------------------------------------------------- |
| `PreToolUse`          | Before tool execution                                                  | Tool id (`write_file`, `read_file`, `run_shell_command`, etc.)   |
| `PostToolUse`         | After successful tool execution                                        | Tool id                                                          |
| `PostToolUseFailure`  | After tool execution fails                                             | Tool id                                                          |
| `PostToolBatch`       | Once after every tool call in a batch has resolved                     | None (always fires)                                              |
| `UserPromptSubmit`    | Before supported model invocations                                     | None                                                             |
| `UserPromptExpansion` | After a slash command expands into a prompt, before the prompt is sent | Command name, without the leading `/`                            |
| `SessionStart`        | When session starts or resumes                                         | Source (`startup`, `resume`, `clear`, `compact`)                 |
| `SessionEnd`          | When session ends                                                      | Reason (`clear`, `logout`, `prompt_input_exit`, etc.)            |
| `SessionDelete`       | After an explicitly selected session is deleted                        | None                                                             |
| `MessageDisplay`      | Repeatedly, as the reply streams                                       | None (always fires)                                              |
| `Stop`                | Before the turn ends                                                   | None (always fires)                                              |
| `StopFailure`         | When an API error or loop detection ends the turn, instead of `Stop`   | Error type (`rate_limit`, `server_error`, `loop_detected`, etc.) |
| `SubagentStart`       | When subagent starts                                                   | Agent type (`Bash`, `Explorer`, `Plan`, etc.)                    |
| `SubagentStop`        | When subagent stops                                                    | Agent type                                                       |
| `PreCompact`          | Before conversation compaction                                         | Trigger (`manual`, `auto`)                                       |
| `PostCompact`         | After conversation compaction succeeds                                 | Trigger (`manual`, `auto`)                                       |
| `Notification`        | When notifications are sent                                            | Type (`permission_prompt`, `idle_prompt`, `auth_success`)        |
| `PermissionRequest`   | When permission dialog is shown                                        | Tool id                                                          |
| `PermissionDenied`    | When AUTO-mode classification denies a tool call                       | Tool id                                                          |
| `TodoCreated`         | When a new todo item is created                                        | None (always fires)                                              |
| `TodoCompleted`       | When a todo item is marked as completed                                | None (always fires)                                              |
| `InstructionsLoaded`  | When a context file such as `QWEN.md`, or a file it imports, is loaded | File path of the loaded file                                     |

### Matcher Patterns

`matcher` is a regular expression used to filter trigger conditions.

| Event Type          | Events                                                                                     | Matcher Support | Matcher Target                                                                                                                                         |
| :------------------ | :----------------------------------------------------------------------------------------- | :-------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tool Events         | `PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionRequest`, `PermissionDenied` | ✅ Regex        | Tool id: `write_file`, `read_file`, `run_shell_command`, etc.                                                                                          |
| Tool Events         | `PostToolBatch`                                                                            | ❌ No           | N/A                                                                                                                                                    |
| Subagent Events     | `SubagentStart`, `SubagentStop`                                                            | ✅ Regex        | Agent type: `Bash`, `Explorer`, etc.                                                                                                                   |
| Session Events      | `SessionStart`                                                                             | ✅ Regex        | Source: `startup`, `resume`, `clear`, `compact`                                                                                                        |
| Session Events      | `SessionEnd`                                                                               | ✅ Regex        | Reason: `clear`, `logout`, `prompt_input_exit`, etc.                                                                                                   |
| Session Events      | `SessionDelete`                                                                            | ❌ No           | N/A                                                                                                                                                    |
| Notification Events | `Notification`                                                                             | ✅ Regex        | Type: `permission_prompt`, `idle_prompt`, `auth_success`                                                                                               |
| Compact Events      | `PreCompact`, `PostCompact`                                                                | ✅ Regex        | Trigger: `manual`, `auto`                                                                                                                              |
| Todo Events         | `TodoCreated`, `TodoCompleted`                                                             | ❌ No           | N/A                                                                                                                                                    |
| Prompt Events       | `UserPromptSubmit`                                                                         | ❌ No           | N/A                                                                                                                                                    |
| Prompt Events       | `UserPromptExpansion`                                                                      | ✅ Regex        | Command name without the leading `/`, for example `init`                                                                                               |
| Stop Events         | `Stop`                                                                                     | ❌ No           | N/A                                                                                                                                                    |
| Stop Events         | `StopFailure`                                                                              | ✅ Regex        | Error type: `rate_limit`, `authentication_failed`, `billing_error`, `invalid_request`, `server_error`, `max_output_tokens`, `loop_detected`, `unknown` |
| Message Display     | `MessageDisplay`                                                                           | ❌ No           | N/A                                                                                                                                                    |
| Instruction Events  | `InstructionsLoaded`                                                                       | ✅ Regex        | File path of the loaded file                                                                                                                           |

**Matcher Syntax:**

- Empty string `""`, `"*"` or `".*"` matches all events of that type
- A matcher is first compared exactly. In a `|`-separated list such as `permission_prompt | idle_prompt`, the matcher matches when any entry, ignoring spaces around it, is `*`, `.*`, or exactly the value, unless the whole matcher starts with `^` or `(`, in which case it is only a regular expression. Only a `|` outside `[...]` and groups, and not escaped with a backslash, separates entries: the pipes in `notes\|todo\.md`, `foo[ |]bar` and `a(b | c)` are part of the regular expression, and the spaces around them are kept. List entries are never read as regular expressions on their own
- Otherwise the matcher is an unanchored regular expression (e.g., `^run_shell_command$`, `read_.*`, `(write_file|edit)`). For a list that does not start with `^` or `(`, the expression is built from the trimmed, non-empty entries (an escaped space at an entry's edge, as in `\.env\ |\.pem`, is kept), so a stray `|` as in `read_(file|edit)|` is ignored and never makes the matcher match everything, and a matcher of only `|` matches nothing. A matcher that starts with `^` or `(` is compiled exactly as written, so a trailing `|` there, as in `^write_file|`, does make it match everything. Because the expression is unanchored, `read` also matches `read_file` and `edit` also matches `notebook_edit`. Add `^` and `$` to match a whole value, and anchor exclusions as well: `^(?!write_file).*$` excludes `write_file`, while unanchored `(?!write_file).*` still matches it
- The same rules apply to every event that supports a matcher, and to hooks registered by skills
- Tool hooks receive the runtime tool id in `tool_name` (for example, `write_file`). Built-in display names such as `WriteFile` and `ReadFile` are also accepted as matcher aliases for compatibility, and so are the tool names permission rules accept, including Claude Code's `Bash`, `Read` and `Write`, so a matcher copied from a Claude Code config such as `"Bash"` or `"Write|Edit"` works unchanged. New configs should still prefer runtime ids. Aliases are only compared exactly, so anchor runtime ids (`^write_file$`), not display names. Each alias names exactly one tool: unlike a permission rule, a `Read` matcher does not also cover `grep_search` or `glob`, and a `Bash` matcher does not cover `monitor`.

**Examples:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "^run_shell_command$",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'bash check' >> /tmp/hooks.log"
          }
        ]
      },
      {
        "matcher": "write_.*",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'write check' >> /tmp/hooks.log"
          }
        ]
      },
      {
        "matcher": "*",
        "hooks": [
          { "type": "command", "command": "echo 'all tools' >> /tmp/hooks.log" }
        ]
      }
    ],
    "SubagentStart": [
      {
        "matcher": "^(Bash|Explorer)$",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'subagent check' >> /tmp/hooks.log"
          }
        ]
      }
    ]
  }
}
```

## Input/Output Rules

### Hook Input Structure

All hook executors receive the standardized event input. The delivery boundary depends on the executor:

| Hook type  | Input recipient                                                 |
| :--------- | :-------------------------------------------------------------- |
| `command`  | Child process through JSON on `stdin`                           |
| `http`     | Configured endpoint through a JSON `POST` body                  |
| `function` | Trusted in-process callback                                     |
| `prompt`   | Configured model provider after the input replaces `$ARGUMENTS` |

Function hooks are trusted code running in the Qwen process. They receive an in-process object, so fields must not be treated as immutable against a function hook.

Qwen does not control whether a hook process, endpoint, callback, or model provider retains or forwards its input. Review each configured executor's data-handling policy.

**Common Fields:**

```json
{
  "session_id": "string",
  "transcript_path": "string",
  "cwd": "string",
  "hook_event_name": "string",
  "timestamp": "string",
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "agent_id": "string (only when the event fires inside a subagent)",
  "prompt_id": "string (when the event belongs to a model turn)",
  "source_type": "string (only when the session has a declared source)",
  "source_id": "string (only when the session source has an id)"
}
```

Event-specific fields are added based on the hook type. `permission_mode` is the session's approval mode unless the event reports the mode that applied to it, as tool and subagent events do. `agent_id` is present only when the event fires inside a subagent; `agent_type` is reported on `SessionStart`, `SubagentStart` and `SubagentStop`. `source_type` and `source_id` appear only in ACP sessions whose client declared a session source when creating the session.

Hook input is a forward-extensible JSON contract: new optional fields can be added to existing events. Consumers should ignore unknown fields. A strict decoder that rejects unknown properties must be updated to explicitly allow each new optional field before upgrading Qwen Code. For security-sensitive hooks, a decoder failure can change fail-open or fail-closed behavior, so administrators must validate the upgraded payload against the deployed hook before rollout.

### Hook Output Structure

Hook output is returned via `stdout` (command) or HTTP response body (http) as JSON.

**Exit Code Behavior (Command Hooks):**

| Exit Code | Behavior                                                                                                                                                                                                                                                                                                                                                                       |
| :-------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `0`       | Success. A JSON object in `stdout` controls behavior. Any other `stdout`, including bare JSON values such as `42`, is plain text: it is added to the model context on `SessionStart`, `UserPromptSubmit` and `UserPromptExpansion`, and kept as a system message on other events. Output that looks like a JSON object but does not parse is never added to the model context. |
| `2`       | **Blocking error**. Ignores `stdout`, passes `stderr` as error feedback to the model.                                                                                                                                                                                                                                                                                          |
| Other     | Non-blocking error. `stderr` only shown in debug mode, execution continues.                                                                                                                                                                                                                                                                                                    |

Adding plain text to the model context applies only to a command hook's `stdout`. An HTTP hook's response body is read as JSON only when its `Content-Type` is `application/json`; any other non-empty body becomes a `systemMessage` on every event. An HTTP hook that adds context must return JSON with `hookSpecificOutput.additionalContext`.

**Output Structure:**

Hook output supports three categories of fields:

1. **Common Fields**: `continue`, `stopReason`, `suppressOutput`, `systemMessage`
2. **Top-level Decision**: `decision`, `reason` (used by some events)
3. **Event-specific Control**: `hookSpecificOutput` (must include `hookEventName`)

```json
{
  "continue": true,
  "decision": "allow",
  "reason": "Operation approved",
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "additionalContext": "Additional context information"
  }
}
```

### Individual Hook Event Details

#### PreToolUse

**Purpose**: Executed before a tool is used to allow for permission checks, input validation, or context injection.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool being executed",
  "tool_input": "object containing the tool's input parameters",
  "tool_use_id": "unique identifier for this tool use instance (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)"
}
```

**Output Options**:

- `hookSpecificOutput.permissionDecision`: "allow", "deny", or "ask" (REQUIRED)
- `hookSpecificOutput.permissionDecisionReason`: explanation for the decision (REQUIRED)
- `hookSpecificOutput.updatedInput`: an object that replaces the tool's whole input (see [Replacing the tool input](<#replacing-the-tool-input>))
- `hookSpecificOutput.additionalContext`: text appended, after a blank line, to this call's tool result that the model sees next (for a call made from a Code Mode `exec` script, to the `exec` call's result; see below). It is delivered whatever the decision: after the tool's own output when it runs, or after the error message when it is denied, stopped, or fails. `<` and `>` are escaped.

The `permissionDecision` value controls whether the tool runs:

- `"allow"` — let the call continue; the usual permission checks and approval prompt still apply.
- `"deny"` — block the tool; it does not execute and an error is returned to the model.
- `"ask"` — pause and ask the user to confirm the tool call in the TUI before it runs. Confirming runs the tool once; declining cancels it. This confirmation takes the place of the usual approval prompt, and no permission rule, approval mode (including YOLO) or PermissionRequest hook approves the call instead; a PermissionRequest hook can still deny it, or replace its input, which the user then confirms. In contexts that cannot prompt for confirmation — headless (`--prompt`) runs and background subagents — `"ask"` falls back to `"deny"`.

For `"ask"`, the TUI displays `permissionDecisionReason` as literal text rather than interpreting inline Markdown. This keeps formatting markers and link targets visible to the user.

With `"ask"`, the hook runs once. Its `additionalContext` is delivered with the tool result if the user confirms, and dropped if the user declines or the call is cancelled. In ACP sessions (IDE integrations), `"ask"` is currently treated as `"deny"`, and the context is delivered with the denial.

`additionalContext` goes into the tool result. It does not change the tool input, the approval prompt, the error shown while the tool runs, or the user's prompt. Because it becomes part of the tool result, it is saved in the session transcript with it and can appear wherever the full tool output is shown, such as the Ctrl+O detail view or a resumed session; it is not marked apart from the tool's own output.

The text a call's PreToolUse hooks add is cut to `tools.truncateToolOutputThreshold` characters; in ACP sessions the same limit covers PreToolUse and PostToolUseFailure context together. The batch output limits still apply to the result as a whole.

The context is not delivered when the call is cancelled, or when the turn is cancelled before the tool results go back to the model, even if the tool itself completed.

In Code Mode (`tools.codeModeOnly`), a call made from an `exec` script returns its result to the script, not to the model. Its PreToolUse and PostToolUseFailure context is appended to the `exec` call's tool result instead, after the `exec` call's own context, and the value the script receives is unchanged.

##### Replacing the tool input

`updatedInput` replaces the tool's entire input. It is not merged with the original: a field you leave out is removed, and the tool's own default for it applies. An empty object `{}` is a replacement too.

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "updatedInput": { "file_path": "/workspace/notes/today.md" }
  }
}
```

- A call's PreToolUse hooks run once, after the tool is known to be enabled and callable but before its input is validated or checked against permissions. A replacement can therefore correct an input that would have been rejected. When several calls from one turn are scheduled together, as in the interactive terminal UI, every call's hooks run before any of them runs.
- The replacement then goes through everything the original input would have: the tool's own validation, `permissions` rules, the approval mode, plan mode, and the approval prompt, which shows the replacement. `"allow"` does not let a replacement skip any of these.
- The hook cannot change which tool runs, its call id or its working directory. The model's own tool call stays as it was in the conversation; the approval prompt, the tool, its result and the PostToolUse event use the replacement.
- An `updatedInput` that is present but not a JSON object (`null`, an array, a string or a number) stops the call with an error; the original input is not used. When hooks run in parallel, they all receive the same input, the last replacement in configuration order wins, and an invalid replacement from any of them stops the call. Hooks that run sequentially each receive the previous replacement as `tool_input`.
- The older `hookSpecificOutput.tool_input` field only changes the input passed to the next sequential hook; it never changes the input the tool runs with. Use `updatedInput` instead. When an output has both, `updatedInput` wins.
- A shell command that a hook could still rewrite is not run in parallel with other tool calls.
- If a skill registers a PreToolUse hook while other calls from the same turn are still waiting to run, those calls are not run, because their hooks ran without it; the model is told to call the tool again.
- Changes the user makes in the approval prompt (for example, modifying the content in an editor) come after the hooks ran.
- `updatedInput` from an async hook is ignored.
- In ACP sessions, `"ask"` is still treated as `"deny"` even with a replacement.
- Tool calls run through a managed tool runtime, and fixed-policy calls made by an orchestrator, do not support `updatedInput` yet. A replacement stops the call with an error instead of running either input.

A PermissionRequest hook's `decision.updatedInput` is checked the same way: one that is not a JSON object stops the call, and a replacement is validated and checked against the permission rules before it runs, and the hook's `"allow"` counts only as the approval of that replacement. A replacement that needs a decision the hook cannot make, such as an answer from the user, goes to the approval prompt instead; in ACP sessions it is not run. In AUTO mode, a replacement is reviewed again before it runs.

**Note**: While standard hook output fields like `decision` and `reason` are technically supported by the underlying class, the official interface expects the `hookSpecificOutput` with `permissionDecision` and `permissionDecisionReason`.

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "Security policy blocks database writes",
    "additionalContext": "Current environment: production. Proceed with caution."
  }
}
```

#### PostToolUse

**Purpose**: Executed after a tool completes successfully to process results, log outcomes, or inject additional context.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool that was executed",
  "tool_input": "object containing the tool's input parameters",
  "tool_response": "object containing the tool's response",
  "tool_use_id": "unique identifier for this tool use instance (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)",
  "duration_ms": "tool execution time in milliseconds, excluding approval (optional)"
}
```

**Output Options**:

- `decision`: "allow", "deny", "block" (defaults to "allow" if not specified)
- `reason`: reason for the decision
- `hookSpecificOutput.additionalContext`: additional information to be included

**Example Output**:

```json
{
  "decision": "allow",
  "reason": "Tool executed successfully",
  "hookSpecificOutput": {
    "additionalContext": "File modification recorded in audit log"
  }
}
```

#### PostToolUseFailure

**Purpose**: Executed when a tool execution fails to handle errors, send alerts, or record failures.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_use_id": "unique identifier for the tool use (internal format, e.g., toolu_xxx)",
  "tool_call_id": "original API call ID from the LLM provider (e.g., call_xxx for OpenAI/Qwen) (optional)",
  "tool_name": "name of the tool that failed",
  "tool_input": "object containing the tool's input parameters",
  "error": "error message describing the failure",
  "is_interrupt": "boolean indicating if failure was due to user interruption (optional)",
  "duration_ms": "tool execution time in milliseconds when execution had started (optional)"
}
```

**Output Options**:

- `hookSpecificOutput.additionalContext`: error handling information, appended after the error in the tool result the model sees next. This applies both when the tool returns an error and when it throws. For a call made from a Code Mode `exec` script, it goes to the `exec` call's result instead (see [PreToolUse](<#pretooluse>)). `<` and `>` are escaped.
- Standard hook output fields

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Error: File not found. Failure logged in monitoring system."
  }
}
```

#### PostToolBatch

**Purpose**: Runs once after all tool calls in a batch have resolved, before their results are returned to the model. Use it to review the batch as a whole, add context to it, or stop it.

**Matcher**: None. `PostToolBatch` always fires, and a `matcher` on it is ignored.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_calls": [
    {
      "tool_name": "runtime tool id, for example run_shell_command",
      "tool_input": "object containing the tool's input parameters",
      "tool_use_id": "the call id from the model's request",
      "tool_call_id": "the same call id (optional)",
      "status": "success | error | cancelled",
      "tool_response": "object with response_parts, result_display, error, error_type, execution_status, content_length and, when present, vision_bridge_notice (optional)"
    }
  ]
}
```

**Output Options**:

- `hookSpecificOutput.additionalContext`: appended to the result of the last call in the batch, after a blank line. `<` and `>` are escaped.
- `decision`: `"block"` or `"deny"` stops the batch, and so does `continue: false` or exit code 2. The last call's result is replaced by an error that carries `stopReason`, or `reason` when there is no `stopReason`. The other results are kept, and `additionalContext` is still appended.
- `stopReason` / `reason`: the text of that error.

If the hooks fail, or do not finish within 15 seconds, the batch continues with its results unchanged.

**Example Output**:

```json
{
  "decision": "block",
  "reason": "This batch edited files outside the allowed directory",
  "hookSpecificOutput": {
    "hookEventName": "PostToolBatch",
    "additionalContext": "Ask the user before retrying these edits."
  }
}
```

#### UserPromptSubmit

**Purpose**: Executed before supported model invocations to validate, block, or enrich their input. On the core/headless path, the event currently covers `UserQuery`, `ToolResult`, and `Hook` sends, while `Retry`, `Steer`, `Cron`, `Notification`, and `Teammate` sends are skipped. It can therefore occur on continuation paths, and `prompt` must not be assumed to be raw user input. The ACP session path has its own invocation policy: retries and newly dispatched background tasks can still invoke legacy hooks; continue, restored-question, and runtime-goal turns do not.

**Event-specific fields**:

```json
{
  "prompt": "legacy prompt for this invocation; semantics depend on the execution path",
  "submitted_prompt": "optional user text captured at a supported submission boundary"
}
```

`submitted_prompt` is optional. It is present on supported interactive TUI submissions and first-turn headless `UserQuery` sends. On the ACP session path used by ACP clients, `serve`, and daemon hosts, a fresh turn must carry an explicit submission declaration. Missing, non-string, empty, or whitespace-only declarations omit the field; the value is never reconstructed from `prompt` or a display label. Retries, continuations, and channel-classified turns omit it. The channel exclusion includes both automated events and human messages relayed through channel adapters.

Web Shell provides the original composer text at its submission boundary. Realtime voice handoffs do not declare provenance because their request text comes from model-generated tool arguments. Other ACP/daemon SDK clients can opt in per request with `_meta: { "qwen.submittedPrompt": "original submitted text" }`, captured before resource or model-only expansion. Existing clients without this declaration continue running legacy hooks but do not trigger provenance-gated Auto Recall. Do not add the declaration globally to an SDK transport: scheduled tasks, Live task runs, sub-session spawns, model-authored cross-session messages, and promoted mid-turn messages must not acquire it automatically. The private `qwen.daemon.submittedPrompt` key is reserved for the daemon-to-child hop and is stripped from external callers. These declarations are caller-supplied provenance, not proof of human authorship or authorization.

On the ACP path, the initial legacy `prompt` is the request's text blocks joined with a space before resource, attachment, slash-command, or model-only expansion. It does not expose the complete expanded model input. `submitted_prompt` can equal that text, but comes only from the explicit declaration and preserves its original whitespace. On the core/headless path, legacy `prompt` represents the current model-bound text for the hook invocation. Neither field is a complete DLP inspection surface.

The following composer rules apply to the interactive TUI, not to ACP clients. Deferred input can retain the field when its provenance remains complete. A combined batch retains provenance only when every constituent item has it; edited, partially known, or otherwise ambiguous input omits the field. Prompt, command, and shell-history navigation or selected search matches, cross-restart stash restores, and conversation rewind restores also omit it because those paths can surface model-bound text without its original provenance. Consumers that require user-submitted text should treat absence as unavailable rather than falling back to `prompt`.

After restored or provenance-unavailable model-bound input is cleared or submitted, the composer also clears its undo and redo history. This prevents undo from restoring expanded text after its marker or sidecar has been consumed.

Large-paste placeholders remain compact in `submitted_prompt`; the expanded pasted content appears only in `prompt`. On that TUI path, consumers should treat the field as a text projection rather than a byte-for-byte record of clipboard input. ACP clients have no equivalent built-in Vim, paste-placeholder, history, or rewind provenance tracking; they own whether restored or edited text retains a valid submission declaration.

Any non-empty input present while Vim mode is enabled omits `submitted_prompt`, including after Vim is disabled, because Vim registers do not carry provenance in this version. This conservative rule also covers drafts entered before enabling Vim. Clearing the composer starts a new eligible input.

This field is provenance, not authentication, tenant identity, authorization, or DLP. It is caller-supplied data. Every executor configured for this event receives it; in particular, HTTP hooks send it to their endpoint and prompt hooks send it to their model provider.

When both fields are present, prompt-hook payloads contain overlapping text and can consume additional model input tokens. There is no per-hook field suppression in this version.

Sequential UserPromptSubmit hooks can append `additionalContext` to `prompt`; `submitted_prompt` continues to represent the captured submission. Function hooks are trusted same-process code and are not constrained by an immutability guarantee.

When the final hook output contains non-empty `additionalContext`, Qwen first
sanitizes the value and then sends it to the model as a separate text part:

```xml
<qwen:user-prompt-submit-context>
sanitized hook context
</qwen:user-prompt-submit-context>
```

The tag tells the model and transcript consumers that the part came from a
configured hook rather than from the user prompt. It is a provenance marker,
not authentication, authorization, or a general trust boundary.

For a `UserQuery` with this added context, the session JSONL record preserves
the model-bound parts, including the tagged part, and adds the following
`systemPayload`:

```json
{
  "displayText": "pre-hook display projection",
  "hookContext": "sanitized hook context"
}
```

This two-field payload is written only for this kind of user-prompt record.
`hookContext` intentionally duplicates the tagged part so offline and
third-party consumers can identify its provenance without parsing model text.
`displayText` is the pre-hook display projection and never includes the hook
context. On the core/headless path it is the submitted projection when available, otherwise the expanded pre-hook prompt. ACP records the trusted display projection or raw request text before expansion when a projection, attachment references, or input annotations require a payload; otherwise it records the user message without `systemPayload` or `displayText`.

Transcript display consumers treat `displayText` as this user-prompt projection
when `systemPayload.hookContext` is a string. For compatibility with released
`displayText`-only user-prompt records, a complete tagged context in the final
part after at least one other part is equivalent pairing evidence. Notification,
cron, and mid-turn records can also have `displayText`, but those values are
compact display labels and must not be substituted for their model-bound text
without that evidence.
Legacy bare-context records keep their model-bound display behavior because the
context cannot be separated reliably. For metadata-free records that use the
current tagged shape, compatibility consumers may remove the same complete
final tagged part; they must not infer that arbitrary tag-like user text is hook
provenance.

Sensitive prompt telemetry attributes, when enabled, and managed auto-memory
recall both use the pre-hook prompt. They do not include
`UserPromptSubmit`-added context.

**Output Options**:

- `decision`: "allow", "deny", "block", or "ask"
- `reason`: human-readable explanation for the decision
- `hookSpecificOutput.additionalContext`: additional context to append to the prompt (optional)

When sent to the model, injected `additionalContext` is appended as its own message part wrapped in a reserved `<qwen:user-prompt-submit-context>...</qwen:user-prompt-submit-context>` tag, so it stays distinguishable from user-authored text in model history and session transcripts. Angle brackets in hook output are escaped before wrapping, so hook content cannot close or forge the tag. The session transcript also records the user's original prompt text separately; the interactive TUI and the ACP/export transcript-replay path display that original text rather than the injected context.

**Note**: Since UserPromptSubmitOutput extends HookOutput, all standard fields are available but only additionalContext in hookSpecificOutput is specifically defined for this event.

**Example Output**:

```json
{
  "decision": "allow",
  "reason": "Prompt reviewed and approved",
  "hookSpecificOutput": {
    "additionalContext": "Remember to follow company coding standards."
  }
}
```

#### UserPromptExpansion

**Purpose**: Runs when a slash command expands into a prompt for the model, after the expansion and before the prompt is sent. This covers commands that submit a prompt, such as custom commands, skills, MCP prompts and built-in commands like `/init`. Commands that only act locally do not fire it. It fires in the interactive UI, headless runs and ACP sessions, and when the model runs a model-invocable command.

**Matcher**: Matches against `command_name`. For example, `"matcher": "^init$"` fires only for `/init`.

**Event-specific fields**:

```json
{
  "command_name": "slash command name without the leading /",
  "command_args": "argument text after the command name",
  "prompt": "the expanded prompt, with non-text parts rendered as text"
}
```

When a headless or ACP message invokes several skills at once, `command_name` is their names joined by spaces and `command_args` is the remaining text.

**Output Options**:

- `decision`: `"block"` or `"deny"` stops the expanded prompt from being sent, and so does `continue: false` or exit code 2. The user sees `UserPromptExpansion blocked: <reason>`; for a model-invoked command, that message is returned as the error.
- `reason` / `stopReason`: the text shown in that message.
- `hookSpecificOutput.additionalContext`: appended to the expanded prompt after a blank line. `&`, `<` and `>` are escaped, and the result is cut to 10,000 characters.

A command hook that exits 0 with plain-text `stdout` has that text treated as `additionalContext` (see [Exit Code Behavior](<#hook-output-structure>)).

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "hookEventName": "UserPromptExpansion",
    "additionalContext": "Follow the repository's CONTRIBUTING.md when generating QWEN.md."
  }
}
```

#### SessionStart

**Purpose**: Executed when a new session starts to perform initialization tasks.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "source": "startup | resume | clear | compact",
  "model": "the model being used",
  "agent_type": "the type of agent if applicable (optional)"
}
```

**Output Options**:

- `hookSpecificOutput.additionalContext`: context to be available in the session
- Standard hook output fields

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Session started with security policies enabled."
  }
}
```

#### SessionEnd

**Purpose**: Executed when a session ends to perform cleanup tasks.

**Event-specific fields**:

```json
{
  "reason": "clear | logout | prompt_input_exit | bypass_permissions_disabled | other"
}
```

**Output Options**:

- Standard hook output fields (typically not used for blocking)

#### SessionDelete

**Purpose**: Runs after an explicitly selected session has been permanently deleted. This event is fire-and-forget: output and failures cannot undo the deletion.

**Event-specific fields**:

```json
{
  "deleted_session_id": "the session that was deleted"
}
```

The hook uses the deleting runtime's normal session fields (`session_id`, `transcript_path`, and `cwd`); over ACP, `transcript_path` is empty because the deleting runtime has no transcript of its own. `SessionDelete` currently fires for the interactive `/delete` flow and ACP's explicit `deleteSession` method; daemon REST batch deletion and internal cleanup do not emit it. A command hook is left to finish if Qwen exits after dispatch; its stdout and stderr are ignored and remain independent of Qwen's pipes.

#### MessageDisplay

**Purpose**: Fires repeatedly as the assistant's reply streams — before `Stop`, which fires once at the end of the turn. Useful for live narration, incremental logging, or any consumer that wants to react to the reply as it's written rather than after the fact. This is a **fire-and-forget** event - hook output and exit codes are ignored.

**Event-specific fields**:

```json
{
  "message_id": "stable id for the whole streamed message",
  "displayed_text": "the CUMULATIVE text streamed so far for this message (not a delta)",
  "is_final": "true on the last firing for this message, false otherwise"
}
```

`displayed_text` is cumulative rather than a delta so hook scripts never need to reassemble chunks themselves — each firing carries the full text so far. Firing is debounced (at most every ~200ms) except for the final firing (`is_final: true`), which always fires once the message ends, so the reply's tail is never dropped waiting on the debounce window.

**Delivery semantics** — what a hook script can rely on:

- **Slow hooks see fewer, newer payloads.** At most one mid-stream hook execution per message is in flight at a time; while one runs, newer debounced payloads _replace_ the queued one rather than piling up behind it. A hook slower than the debounce window therefore skips intermediate snapshots — lossless, since each payload carries the full cumulative text.
- **`is_final` is never queued behind a stale delivery.** The final payload is dispatched the moment the message ends — alongside a still-running mid-stream execution if there is one (the one exception to the one-at-a-time rule, justified the same way: the final cumulative text strictly supersedes whatever that execution is processing). Your hook always receives the `is_final` payload, and receives it before the `Stop` hook fires. One consequence for stateful hooks: when the final execution overlaps a superseded mid-stream one, their _completion_ order is unspecified — the stale execution may finish after the final one (even after `Stop`). Treat `is_final` as terminal per `message_id` and let the cumulative text win, rather than assuming the last execution to finish carries the newest state.
- **The turn waits for `is_final` delivery to complete — but not forever.** The turn's end (and the `Stop` hook, when it fires) waits up to 5 seconds for the final delivery to finish. A hook that completes within that budget keeps the strongest guarantee: a headless run (`qwen -p ...`) exits only after the hook finished, and the `is_final` execution completes before `Stop` starts. A slower hook still receives `is_final` first — only the wait for its completion is bounded: in the terminal UI or an ACP session the execution simply finishes in the background, while a headless run exits without waiting. The hook process is not killed on exit; it is left to finish on its own, so a script chaining `qwen -p … && next-step` can observe `next-step` starting while a slow hook is still running. Hitting this timeout prints a warning on stderr.
- **Cancellation behaviour depends on timing.** A turn cancelled _before `is_final` dispatches_ fires no `is_final` — the message is treated as abandoned, and a consumer that buffers until `is_final` should treat cancellation-silence as its flush/discard signal (e.g. a timeout fallback). The criterion is the abort signal's state at the moment the turn ends, not whether every chunk had already streamed — an abort landing in the brief gap before that check can still suppress `is_final` for a message whose text had, in practice, finished arriving. Cancelling _after `is_final` has dispatched_ (during the drain wait) is different: the still-running hook execution may be terminated mid-flight (SIGTERM), but the payload itself has already been delivered.
- **`displayed_text` is provisional until `is_final`.** It reflects what has streamed so far; treat intermediate payloads as display state, not as authoritative final content.
- **A tool-using turn produces multiple messages.** Each model call gets its own `message_id` with its own `is_final: true` firing: the text before a tool call is one message, the continuation after the tool result is another. Model calls that produce no displayed text (tool-call-only) fire nothing.

**Note**: Fires in the terminal UI, headless (`-p`), and ACP (IDE/editor/`qwen serve`) sessions, with the same payload contract on every surface.

#### Stop

**Purpose**: Executed before Qwen concludes its response to provide final feedback or summaries.

**Event-specific fields**:

```json
{
  "stop_hook_active": "true when this turn is continuing because a stop hook blocked the previous stop check (still true after tool calls made during that continuation); false on the first check and again once the stop is allowed, the blocking cap is reached, the user steers or sends new input, or a new turn, retry or goal turn starts",
  "last_assistant_message": "the last message from the assistant",
  "context_usage": "ratio of context window used (may exceed 1 when tokens exceed window; optional)",
  "context_limit": "context window size in tokens (optional)",
  "input_tokens": "prompt token count (may include output tokens depending on provider; optional)",
  "background_tasks": "array of background tasks, each with id, status, agent_type, started_at and optional description",
  "crons": "array of scheduled jobs, each with id, schedule, prompt, recurring, enabled and optional next_run and last_run"
}
```

The `context_usage`, `context_limit`, and `input_tokens` fields allow hook scripts to observe context usage and implement custom compact strategies — for example, a script that prints a reminder to run `/compact` when usage exceeds a custom threshold.

**Output Options**:

- `decision`: "allow", "deny", "block", or "ask"
- `reason`: human-readable explanation for the decision
- `stopReason`: feedback to include in the stop response
- `continue`: set to false to stop execution
- `hookSpecificOutput.additionalContext`: additional context information

**Note**: Since StopOutput extends HookOutput, all standard fields are available but the stopReason field is particularly relevant for this event.

**Blocking cap**: consecutive blocking decisions are counted per prompt and keep counting across tool round trips, so a hook that blocks on every check ends the turn after `stopHookBlockingCap` blocks (default 8; `QWEN_CODE_STOP_HOOK_BLOCK_CAP` overrides it for one run). The count and `stop_hook_active` share the same record: both restart when the stop is allowed, when you steer or send new input, when a turn is retried, when a goal turn starts, and when the turn ends abnormally. In non-interactive text mode, Stop hook messages and cap warnings are written to stderr; JSON output is unchanged.

**Example Output**:

```json
{
  "decision": "block",
  "reason": "Must be provided when Qwen Code is blocked from stopping"
}
```

#### StopFailure

**Purpose**: Runs instead of `Stop` when an API error or loop detection ends the turn. API errors fire it in the interactive UI and in ACP sessions, but not in headless (`-p`) runs. Loop detection fires it in the interactive UI and in headless runs, but not in ACP sessions. This is a **fire-and-forget** event: Qwen does not wait for the hooks, and their output and exit codes are ignored.

**Event-specific fields**:

```json
{
  "error": "rate_limit | authentication_failed | billing_error | invalid_request | server_error | max_output_tokens | loop_detected | unknown",
  "error_details": "the error message, or the loop type for loop_detected (optional)",
  "last_assistant_message": "optional; the interactive UI currently sets it to the formatted error text"
}
```

For API errors, `error` is derived from the HTTP status and the error message: status 429 or "rate limit" gives `rate_limit`; 401 or "unauthorized" gives `authentication_failed`; 402, 403, "billing" or "quota" gives `billing_error`; 400 or "invalid" gives `invalid_request`; any other status of 500 or above gives `server_error`; "max_tokens" or "token limit" gives `max_output_tokens`; anything else is `unknown`. The checks run in that order.

**Matcher**: Matches against the `error` field. For example, `"matcher": "rate_limit"` will only trigger for rate limit errors.

**Output Options**:

- **None** - StopFailure is fire-and-forget. All hook output, errors and exit codes are discarded.

**Exit Code Handling**:

| Exit Code | Behavior                  |
| --------- | ------------------------- |
| Any       | Ignored (fire-and-forget) |

**Example Configuration**:

```json
{
  "hooks": {
    "StopFailure": [
      {
        "matcher": "rate_limit",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/rate-limit-alert.sh",
            "name": "rate-limit-alerter"
          }
        ]
      }
    ]
  }
}
```

**Use Cases**:

- Rate limit monitoring and alerting
- Authentication failure logging
- Billing error notifications
- Error statistics collection

A command hook is left to finish if Qwen exits after dispatch; its stdout and stderr are ignored and remain independent of Qwen's pipes.

#### SubagentStart

**Purpose**: Executed when a subagent (like the Task tool) is started to set up context or permissions.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "agent_id": "identifier for the subagent",
  "agent_type": "type of agent (Bash, Explorer, Plan, Custom, etc.)"
}
```

**Output Options**:

- `hookSpecificOutput.additionalContext`: initial context for the subagent
- Standard hook output fields

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Subagent initialized with restricted permissions."
  }
}
```

#### SubagentStop

**Purpose**: Executed when a subagent finishes to perform finalization tasks.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "stop_hook_active": "false on the first stop check; true when the subagent is continuing because a SubagentStop hook blocked its previous stop",
  "agent_id": "identifier for the subagent",
  "agent_type": "type of agent",
  "agent_transcript_path": "path to the subagent's transcript",
  "last_assistant_message": "the last message from the subagent",
  "background_tasks": "array of background tasks, same shape as in Stop",
  "crons": "array of scheduled jobs, same shape as in Stop"
}
```

**Output Options**:

- `decision`: "allow", "deny", "block", or "ask"
- `reason`: human-readable explanation for the decision

**Example Output**:

```json
{
  "decision": "block",
  "reason": "Must be provided when Qwen Code is blocked from stopping"
}
```

#### PreCompact

**Purpose**: Executed before conversation compaction to prepare or log the compaction.

**Event-specific fields**:

```json
{
  "trigger": "manual | auto",
  "custom_instructions": "custom instructions currently set"
}
```

**Output Options**:

- `hookSpecificOutput.additionalContext`: context to include before compaction
- Standard hook output fields

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Compacting conversation to maintain optimal context window."
  }
}
```

#### PostCompact

**Purpose**: Runs after a conversation compaction succeeds, to archive the summary or track usage. It does not fire when compaction fails. Qwen waits for the hooks to finish before continuing.

**Event-specific fields**:

```json
{
  "trigger": "manual | auto",
  "compact_summary": "the summary that replaced the compacted history, without its <analysis> block"
}
```

**Matcher**: Matches against the `trigger` field: `manual` when compaction was requested, for example with `/compress`, and `auto` when Qwen compacts on its own. For example, `"matcher": "manual"` fires only for requested compactions.

**Output Options**:

- **None** - PostCompact output is ignored. `decision`, `continue`, `additionalContext` and exit codes have no effect.

**Example Configuration**:

```json
{
  "hooks": {
    "PostCompact": [
      {
        "matcher": "manual",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/save-compact-summary.sh",
            "name": "save-summary"
          }
        ]
      }
    ]
  }
}
```

**Use Cases**:

- Summary archiving to files or databases
- Usage statistics tracking
- Context change monitoring
- Audit logging for compaction operations

#### Notification

**Purpose**: Executed when notifications are sent to customize or intercept them.

**Event-specific fields**:

```json
{
  "message": "notification message content",
  "title": "notification title (optional)",
  "notification_type": "permission_prompt | idle_prompt | auth_success"
}
```

> **Note**: `elicitation_dialog` type is defined but not currently implemented.

**Output Options**:

- `hookSpecificOutput.additionalContext`: additional information to include
- Standard hook output fields

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "additionalContext": "Notification processed by monitoring system."
  }
}
```

#### PermissionRequest

**Purpose**: Executed when permission dialogs are displayed to automate decisions or update permissions.

**Event-specific fields**:

```json
{
  "permission_mode": "default | plan | auto_edit | auto | yolo",
  "tool_name": "name of the tool requesting permission",
  "tool_input": "object containing the tool's input parameters",
  "permission_suggestions": "array of suggested permissions (optional)"
}
```

**Output Options**:

- `hookSpecificOutput.decision`: structured object with permission decision details:
  - `behavior`: "allow" or "deny"
  - `updatedInput`: modified tool input (optional)
  - `updatedPermissions`: modified permissions (optional)
  - `message`: message to show to user (optional)
  - `interrupt`: whether to interrupt the workflow (optional)

**Example Output**:

```json
{
  "hookSpecificOutput": {
    "decision": {
      "behavior": "allow",
      "message": "Permission granted based on security policy",
      "interrupt": false
    }
  }
}
```

#### PermissionDenied

**Purpose**: Runs when the AUTO-mode classifier blocks a tool call, including when repeated blocks make Qwen fall back to asking the user. It reports the denial and cannot approve or change the call. Qwen waits for the hooks before continuing; a hook failure is logged and does not change the outcome.

**Matcher**: Matches against the tool id, like the other tool events.

**Event-specific fields**:

```json
{
  "tool_name": "runtime tool id of the denied call",
  "tool_input": "object containing the tool's input parameters",
  "tool_use_id": "the call id",
  "tool_call_id": "the same call id (optional)",
  "reason": "classifier_blocked | classifier_unavailable"
}
```

`reason` is currently always `classifier_blocked`. `classifier_unavailable` is defined, but a call denied because the classifier returned no verdict does not fire this event.

**Output Options**:

- **None** - PermissionDenied output and exit codes are ignored.

#### TodoCreated

**Purpose**: Executed when a new todo item is created via the `todo_write` tool. Allows validation, logging, or blocking of todo creation.

Todo hooks run in two phases:

- `validation`: runs before persistence. Use this phase for validation only; returning `block` or `deny` prevents the write.
- `postWrite`: runs after persistence. Use this phase for side effects such as logging or syncing; `block` or `deny` is ignored in this phase.

**Event-specific fields**:

```json
{
  "todo_id": "unique identifier for the todo item",
  "todo_content": "content/description of the todo item",
  "todo_status": "pending | in_progress | completed",
  "all_todos": "array of all todo items in the current list",
  "phase": "validation | postWrite"
}
```

**Output Options**:

- `decision`: "allow", "block", or "deny"
- `reason`: human-readable explanation for the decision (required when blocking)

**Blocking Behavior**:

During the `validation` phase, when `decision` is `block` or `deny` (exit code 2), todo creation is prevented. The todo list remains unchanged, and the reason is provided as feedback to the model.

During the `postWrite` phase, the todo has already been persisted. Hooks may still return output, but `block` / `deny` does not undo the write and should not be used for validation.

**Example Output (Allow)**:

```json
{
  "decision": "allow",
  "reason": "Todo content validated successfully"
}
```

**Example Output (Block)**:

```json
{
  "decision": "block",
  "reason": "Todo content too short. Minimum 5 characters required."
}
```

**Example Hook Script**:

```bash
#!/bin/bash
# ~/.qwen/hooks/todo-validator.sh
# Validates todo content before creation

INPUT=$(cat)
CONTENT=$(echo "$INPUT" | jq -r '.todo_content')

# Check minimum length
if [ ${#CONTENT} -lt 5 ]; then
  echo '{"decision": "block", "reason": "Todo content must be at least 5 characters"}'
  exit 2
fi

# Block test-related todos
if [[ "$CONTENT" =~ "test" ]]; then
  echo '{"decision": "block", "reason": "Test todos are not allowed in production"}'
  exit 2
fi

echo '{"decision": "allow"}'
exit 0
```

**Example Configuration**:

```json
{
  "hooks": {
    "TodoCreated": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.qwen/hooks/todo-validator.sh",
            "name": "todo-validator",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

#### TodoCompleted

**Purpose**: Executed when a todo item is marked as completed. Allows validation, logging, or blocking of todo completion.

Todo hooks run in two phases:

- `validation`: runs before persistence. Use this phase for validation only; returning `block` or `deny` prevents the write.
- `postWrite`: runs after persistence. Use this phase for side effects such as logging or syncing; `block` or `deny` is ignored in this phase.

**Event-specific fields**:

```json
{
  "todo_id": "unique identifier for the todo item",
  "todo_content": "content/description of the todo item",
  "previous_status": "pending | in_progress (status before completion)",
  "all_todos": "array of all todo items in the current list",
  "phase": "validation | postWrite"
}
```

**Output Options**:

- `decision`: "allow", "block", or "deny"
- `reason`: human-readable explanation for the decision (required when blocking)

**Blocking Behavior**:

During the `validation` phase, when `decision` is `block` or `deny` (exit code 2), todo completion is prevented. The todo item remains in its previous status, and the reason is provided as feedback to the model.

During the `postWrite` phase, the todo has already been persisted. Hooks may still return output, but `block` / `deny` does not undo the write and should not be used for validation.

**Example Output (Allow)**:

```json
{
  "decision": "allow",
  "reason": "Todo completion approved"
}
```

**Example Output (Block)**:

```json
{
  "decision": "block",
  "reason": "Cannot complete this todo until dependent tasks are finished."
}
```

**Example Hook Script**:

```bash
#!/bin/bash
# ~/.qwen/hooks/todo-completion-validator.sh
# Validates todo completion conditions

INPUT=$(cat)
TODO_ID=$(echo "$INPUT" | jq -r '.todo_id')
ALL_TODOS=$(echo "$INPUT" | jq -r '.all_todos')

# Check if there are incomplete dependent todos (example logic)
INCOMPLETE_COUNT=$(echo "$ALL_TODOS" | jq '[.[] | select(.status != "completed")] | length')

if [ "$INCOMPLETE_COUNT" -gt 5 ]; then
  echo '{"decision": "block", "reason": "Too many incomplete todos. Complete other tasks first."}'
  exit 2
fi

echo '{"decision": "allow"}'
exit 0
```

**Example Configuration**:

```json
{
  "hooks": {
    "TodoCompleted": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "$HOME/.qwen/hooks/todo-completion-validator.sh",
            "name": "completion-validator",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

**Use Cases**:

- **Logging**: Track todo creation and completion for audit or analytics
- **Validation**: Enforce content quality standards (minimum length, required keywords)
- **Workflow Control**: Block completion until prerequisites are met
- **Integration**: Sync todos with external task management systems (Jira, Trello, etc.)

#### InstructionsLoaded

**Purpose**: Runs for each context file Qwen loads into the system prompt, such as `QWEN.md`, and for each file those files import. Use it to audit which instruction files a session uses. It is informational: it cannot stop or change loading.

It fires when the session starts, when context files are reloaded during the session, and for every file pulled in through an import, after the file has been read. Qwen waits for the hooks before it continues loading; a hook failure is logged and does not stop loading.

**Matcher**: Matches against `file_path`. For example, `"matcher": "QWEN\\.local\\.md$"` fires only for local context files.

**Event-specific fields**:

```json
{
  "file_path": "path of the loaded file",
  "memory_type": "user | project | local | extension",
  "load_reason": "session_start | include | refresh",
  "trigger_file_path": "for include: the context file whose loading pulled this file in (optional)",
  "parent_file_path": "for include: the file that contains the import (optional)"
}
```

`load_reason` is `session_start` for the initial load, `refresh` when context files are reloaded, and `include` for a file reached through an import.

`memory_type` is `extension` for an extension's context files and files in its directory, `user` for files in the global `.qwen` directory or directly in your home directory, `local` for `.qwen/QWEN.local.md` at the project root, and `project` for any other file. An imported file has the same `memory_type` as the context file that imported it.

**Output Options**:

- **None** - InstructionsLoaded output and exit codes are ignored.

## Hook Configuration

Hooks are configured in Qwen Code settings, typically in `.qwen/settings.json` or user configuration files. Hooks in the system settings files (System and SystemDefaults) load with the source System and, like user hooks, regardless of folder trust. Within one event, sequential hooks from settings and extensions run in this order: Project, User, System, Extension.

> [!NOTE]
> A hook must include a name field to be enabled or disabled individually at runtime. Hooks without a name will always execute when their event and matcher conditions are met.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "^run_shell_command$",
        "sequential": false,
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/security-check.sh",
            "name": "security-check",
            "description": "Run security checks before tool execution",
            "timeout": 30
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Session started'",
            "name": "session-init"
          }
        ]
      }
    ]
  }
}
```

## Hook Execution

### Parallel vs Sequential Execution

- By default, hooks execute in parallel for better performance
- Use `sequential: true` in hook definition to enforce order-dependent execution
- Sequential hooks can modify input for subsequent hooks in the chain

### Async Hooks

Only `command` type supports asynchronous execution. Setting `"async": true` runs the hook in the background without blocking the main flow.

Async hooks are scoped to the Qwen process. On POSIX, Qwen reclaims a still-running async hook process tree when it exits, except for event types whose sections explicitly guarantee fire-and-forget completion after exit. Windows cannot reconstruct a descendant tree after its root exits, so full parent-exit reclamation there requires a Job Object or descendant tracking.

**Features:**

- Cannot return decision control (the event continues as soon as the hook starts)
- Output is not delivered yet: `systemMessage`, `additionalContext` and plain `stdout` from an async hook are neither shown to the user nor added to the model context. The debug log records only that the hook started, completed or failed. Delivering this output is planned.
- Suitable for side effects such as auditing, logging and background tests that write their results somewhere you can read them
- Occupies one of 10 concurrent async hook slots until it finishes or reaches its `timeout` (60 seconds by default). While all 10 slots are in use, a new async hook is skipped

**Example:**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "write_file|edit",
        "hooks": [
          {
            "type": "command",
            "command": "\"$QWEN_PROJECT_DIR/.qwen/hooks/run-tests-async.sh\"",
            "async": true,
            "timeout": 300
          }
        ]
      }
    ]
  }
}
```

```bash
#!/bin/bash
INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // empty')
if [[ "$FILE_PATH" != *.ts && "$FILE_PATH" != *.js ]]; then exit 0; fi
# Async hook output is not shown yet, so write the result to a file.
LOG=/tmp/qwen-async-tests.log
if npm test >"$LOG" 2>&1; then
  echo "Tests passed after editing $FILE_PATH" >>"$LOG"
else
  echo "Tests failed after editing $FILE_PATH" >>"$LOG"
fi
```

### Security Model

- Hooks run in the user's environment with user privileges
- Project-level hooks require trusted folder status
- Timeouts prevent hanging hooks (default: 60 seconds for command hooks)

## Best Practices

### Example 1: Security Validation Hook

A PreToolUse hook that logs and potentially blocks dangerous commands:

**security_check.sh**

```bash
#!/bin/bash

# Read input from stdin
INPUT=$(cat)

# Parse the input to extract tool info
TOOL_NAME=$(echo "$INPUT" | jq -r '.tool_name')
TOOL_INPUT=$(echo "$INPUT" | jq -r '.tool_input')

# Check for potentially dangerous operations
if echo "$TOOL_INPUT" | grep -qiE "(rm.*-rf|mv.*\/|chmod.*777)"; then
  echo '{
    "hookSpecificOutput": {
      "hookEventName": "PreToolUse",
      "permissionDecision": "deny",
      "permissionDecisionReason": "Security policy blocks dangerous command"
    }
  }'
  exit 2  # Blocking error
fi

# Log the operation
echo "INFO: Tool $TOOL_NAME executed safely at $(date)" >> /var/log/qwen-security.log

# Allow with additional context
echo '{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "allow",
    "permissionDecisionReason": "Security check passed",
    "additionalContext": "Command approved by security policy"
  }
}'
exit 0
```

Configure in `.qwen/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "${SECURITY_CHECK_SCRIPT}",
            "name": "security-checker",
            "description": "Security validation for bash commands",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### Example 2: HTTP Audit Hook

A PostToolUse HTTP hook that sends all tool execution records to a remote audit service:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "http",
            "url": "https://audit.example.com/api/tool-execution",
            "headers": {
              "Authorization": "Bearer ${AUDIT_API_TOKEN}",
              "Content-Type": "application/json"
            },
            "allowedEnvVars": ["AUDIT_API_TOKEN"],
            "timeout": 10,
            "name": "audit-logger"
          }
        ]
      }
    ]
  }
}
```

### Example 3: Submitted Prompt Validation Hook

On the core/headless path, `prompt` can include generated or expanded content rather than original user input. On ACP it starts with the pre-expansion request text, so reading it does not inspect attachment bodies or the complete model input. `UserPromptSubmit` does not cover every model send. Do not silently fall back from `submitted_prompt` to `prompt` when source provenance is required.

A UserPromptSubmit hook that validates supported submitted text and provides context for long prompts. It also runs on headless submissions and explicitly declared ACP/daemon submissions; it is not TUI-only. It skips invocations where source provenance is unavailable. A blocking result stops the affected invocation, including on these non-TUI paths. The keyword check is illustrative and is not a complete DLP policy:

**prompt_validator.py**

```python
import json
import sys
import re

# Load input from stdin
try:
    input_data = json.load(sys.stdin)
except json.JSONDecodeError as e:
    print(f"Error: Invalid JSON input: {e}", file=sys.stderr)
    sys.exit(1)

user_prompt = input_data.get("submitted_prompt")
if user_prompt is None:
    # Do not mistake model-bound or machine-generated content for raw input.
    sys.exit(0)

# Sensitive words list
sensitive_words = ["password", "secret", "token", "api_key"]

# Check for sensitive information
for word in sensitive_words:
    if re.search(rf"\b{word}\b", user_prompt.lower()):
        # Block prompts containing sensitive information
        output = {
            "decision": "block",
            "reason": f"Prompt contains sensitive information '{word}'. Please remove sensitive content and resubmit.",
            "hookSpecificOutput": {
                "hookEventName": "UserPromptSubmit"
            }
        }
        print(json.dumps(output))
        sys.exit(0)

# Check prompt length and add warning context if too long
if len(user_prompt) > 1000:
    output = {
        "hookSpecificOutput": {
            "hookEventName": "UserPromptSubmit",
            "additionalContext": "Note: User submitted a long prompt. Please read carefully and ensure all requirements are understood."
        }
    }
    print(json.dumps(output))
    sys.exit(0)

# No processing needed for normal cases
sys.exit(0)
```

## Troubleshooting

### A hook does not fire

1. **Event name.** The key must be one of the events in [Hook Events](<#hook-events>), spelled exactly, including case. An unknown event name is skipped with the warning `Invalid hook event name`.
2. **Matcher.** Tool events match the runtime tool id, such as `run_shell_command` or `write_file`, or an accepted alias such as Claude Code's `Bash` or `Write` (see [Matcher Patterns](<#matcher-patterns>)). On events without matcher support, `matcher` is ignored.
3. **Folder trust.** Hooks in a project's `.qwen/settings.json` load only when the folder is trusted. User hooks load regardless of folder trust.
4. **Hooks disabled.** `"disableAllHooks": true`, `--safe-mode` (or `QWEN_CODE_SAFE_MODE`) and `--bare` (or `QWEN_CODE_SIMPLE`) turn off all hooks, including user hooks.
5. **Debug log.** Start Qwen Code with `--debug`, or set `QWEN_DEBUG_LOG_FILE=1`, then read the session log at `~/.qwen/debug/latest` (under `$QWEN_RUNTIME_DIR` when that is set). Each line is tagged with a namespace:
   - `[HOOK_REGISTRY]`: `Hook registry initialized with N hook entries`, and hook definitions discarded as invalid
   - `[TRUSTED_HOOKS]`: `Hook <name> started for event <event>` and the matching `ended` line for each run, async hook status, and hook system messages
   - `[HOOK_MATCHER]`: matchers that are not valid regular expressions
   - `[HOOK_TIMEOUT]`: command hook `timeout` values read as milliseconds or ignored
   - `[HTTP_HOOK_RUNNER]`, `[URL_VALIDATOR]`, `[PROMPT_HOOK_RUNNER]`, `[FUNCTION_HOOK_RUNNER]`, `[SKILL_HOOKS]`, `[SESSION_HOOKS_MANAGER]`, `[ASYNC_HOOK_REGISTRY]` and `[HOOK_AGGREGATOR]`: details from the individual runners and registries

   Prompt-hook inputs can be written to the session debug log, so apply appropriate access and retention controls.

6. **Edited during the session.** Hooks are read when the session starts. After editing hook definitions, open the interactive `/hooks` menu once to reload them, or restart Qwen Code. Changes to hook controls and HTTP security settings require a restart.

### Other checks

- Verify hook script permissions and executability
- Ensure proper JSON formatting in hook outputs
- Use specific matcher patterns to avoid unintended hook execution
- Temporarily disable all hooks: add `"disableAllHooks": true` in settings


</details>
