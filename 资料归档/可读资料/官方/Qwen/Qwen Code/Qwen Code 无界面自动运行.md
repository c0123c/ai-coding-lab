# Qwen Code 无界面自动运行

讲解脚本提示、标准输入、多轮恢复、系统指令、文本及 JSON 输出和无人值守控制，适用于自动化与 CI。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-28](<https://github.com/QwenLM/qwen-code/commit/e39567eb564cf5473dc703b25b219c66059fcb1b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/headless.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code 无界面自动运行.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 无头模式

无头模式允许您从命令行 
 脚本和自动化工具以编程方式运行 Qwen Code，无需任何交互式 UI。这非常适合
脚本、自动化、CI/CD管道和构建AI-动力工具。

## 概述

无头模式为 Qwen Code 提供无头接口：

- 通过命令行参数或标准输入接受提示
- 返回结构化输出（文本或 JSON）
- 支持文件重定向和管道
- 支持自动化和脚本工作流程
- 为错误处理提供一致的退出代码
- 可以恢复当前项目范围内的先前会话以实现多步骤自动化

## 基本用法

### 直接提示

使用 `--prompt` （或 `-p`）标志在无头模式下运行：

```bash
qwen --prompt "What is machine learning?"
```

### 标准输入

从终端通过管道将输入输入到 Qwen Code：

```bash
echo "Explain this code" | qwen
```

### 与文件输入结合

从文件中读取并使用 Qwen Code 进行处理：

```bash
cat README.md | qwen --prompt "Summarize this documentation"
```

### 恢复之前的会话（无头）

在无头脚本中重用当前项目中的对话上下文：

```bash
# Continue the most recent session for this project and run a new prompt
qwen --continue -p "Run the tests again and summarize failures"

# Resume a specific session ID directly (no UI)
qwen --resume 123e4567-e89b-12d3-a456-426614174000 -p "Apply the follow-up refactor"
```

> [！注意]
>
> - 会话数据的范围是 `~/.qwen/projects/<sanitized-cwd>/chats` 下的项目范围 JSONL。
> - 在发送新提示之前恢复对话历史记录、工具输出和聊天压缩检查点。

## 坚持不懈的目标

无头模式接受 `/goal` 作为整个提示符。目标状态与会话一起存储，因此使用 `--continue` 或 `--resume <sessionId>` 从后续进程中检查或控制相同的目标。这需要 `general.chatRecording` 保持启用状态（默认）。

```bash
# Create a Goal and start its worker
qwen -p "/goal Finish the release checklist"

# Inspect its saved state in the same session
qwen --continue -p "/goal"
```

对其他操作使用相同的 `qwen --continue -p "<control>"` 模式：

|控制|行为|
| ------------------------------------ | ---------------------------------------------------------------------------------------- |
| `/goal`                              |报告存储的状态而不调用模型。|
|`/goal <objective>` 或 `/goal set …`|创建或替换目标并开始无头目标工作。|
| `/goal edit <objective>`             |修改未完成的目标；当结果状态处于活动状态时，工作立即开始。|
| `/goal pause`                        |暂停活动目标而不调用模型。|
| `/goal resume`                       |恢复符合条件的目标并开始无头目标工作。|
| `/goal clear`                        |无需确认或模型调用即可清除目标。|

目标的好坏取决于其完成条件。请参阅[目标](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/goals.md>)以了解验证者可以判断和不能判断的内容，并使用`qwen -p "/goal-draft <intent>"`在设置目标之前起草目标。

运行时计划的目标延续段不会计入 `--max-session-turns`，但实际用户提示仍然会计入。显式`--max-wall-time`和`--max-tool-calls`预算继续适用；超过任一条件都会在运行因特定于预算的错误而退出之前暂停活动的目标工作。

对于 `--output-format stream-json`，每个目标状态更改都会发出一个 `stream_event`，其 `event.type` 为 `goal_state`。即使没有 `--include-partial-messages`，也会发出此状态事件。不再发出过去作为兼容性投影跟随的 `active_goal` 事件；自动化读取`goal_state`。

> [！注意]
>
> 此行为适用于标准无头 CLI 运行。 ACP-驱动的会话（IDE集成、Web Shell）通过`sessionGoalControl`扩展方法驱动相同的Goal运行时，并以携带`_meta.goalState`的`session/update`通知而不是`goal_state`流事件接收每个状态更改。

## 自定义主会话提示

您可以更改单个 CLI 运行的主会话系统提示符，而无需编辑共享内存文件。

### 覆盖内置系统提示

使用 `--system-prompt` 替换 Qwen Code 当前运行的内置主会话提示符：

```bash
qwen -p "Review this patch" --system-prompt "You are a terse release reviewer. Report only blocking issues."
```

### 附加额外说明

使用 `--append-system-prompt` 保留内置提示并为此运行添加额外说明：

```bash
qwen -p "Review this patch" --append-system-prompt "Be terse and focus on concrete findings."
```

当您需要自定义基本提示加上额外的特定于运行的指令时，您可以组合这两个标志：

```bash
qwen -p "Summarize this repository" \
  --system-prompt "You are a migration planner." \
  --append-system-prompt "Return exactly three bullets."
```

### 选择输出样式

使用 `--output-style` 为本次运行选择 [ 输出样式 ](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/output-styles>) — 内置或自定义。样式是分层在内置提示上的命名指令块，它会改变答案的编写方式 - `Concise` 引导结果并删除序言和旁白，`Proactive` 开始工作而不是提出建议，`Explanatory` 一路上添加有关代码库的简短注释。它覆盖 `general.outputStyle` 设置； `default` 不选择任何样式。

```bash
qwen -p "Why does the build fail on Windows?" --output-style Concise
```

> [！注意]
>
> - `Learning` 要求您编写部分代码并等待回复，因此在无头运行中会跳过它。
> - 未知的样式名称会打印警告，并以默认样式继续运行。
> - 当 `--system-prompt` 或 `QWEN_SYSTEM_MD` 替换内置提示时，`--output-style` 无效 - 样式仅分层到内置提示上。

> [！注意]
>
> - `--system-prompt` 仅适用于当前运行的主会话。
> - 加载的内存和上下文文件（例如 `QWEN.md`）仍附加在 `--system-prompt` 之后。
> - `--append-system-prompt`在内置提示并加载内存后应用，可与`--system-prompt`一起使用。

## 输出格式

Qwen Code 支持针对不同用例的多种输出格式：

### 文本输出（默认）

标准人类可读输出：

```bash
qwen -p "What is the capital of France?"
```

响应格式：

```
The capital of France is Paris.
```

### JSON 输出

以 JSON 数组的形式返回结构化数据。当会话完成时，所有消息都会被缓冲并一起输出。这种格式非常适合编程处理和自动化脚本。

JSON 输出是消息对象数组。输出包括多种消息类型：系统消息（会话初始化）、辅助消息（AI响应）和结果消息（执行摘要）。

#### 用法示例

```bash
qwen -p "What is the capital of France?" --output-format json
```

输出（执行结束时）：

```json
[
  {
    "type": "system",
    "subtype": "session_start",
    "uuid": "...",
    "session_id": "...",
    "model": "qwen3-coder-plus",
    ...
  },
  {
    "type": "assistant",
    "uuid": "...",
    "session_id": "...",
    "message": {
      "id": "...",
      "type": "message",
      "role": "assistant",
      "model": "qwen3-coder-plus",
      "content": [
        {
          "type": "text",
          "text": "The capital of France is Paris."
        }
      ],
      "usage": {...}
    },
    "parent_tool_use_id": null
  },
  {
    "type": "result",
    "subtype": "success",
    "uuid": "...",
    "session_id": "...",
    "is_error": false,
    "duration_ms": 1234,
    "result": "The capital of France is Paris.",
    "usage": {...}
  }
]
```

### 流-JSON输出

Stream-JSON 格式在执行过程中发生时立即发出 JSON 消息，从而实现实时监控。此格式使用行分隔的 JSON，其中每条消息都是单行上的完整 JSON 对象。

```bash
qwen -p "Explain TypeScript" --output-format stream-json
```

输出（事件发生时流式传输）：

```json
{"type":"system","subtype":"session_start","uuid":"...","session_id":"..."}
{"type":"assistant","uuid":"...","session_id":"...","message":{...}}
{"type":"result","subtype":"success","uuid":"...","session_id":"..."}
```

与 `--include-partial-messages` 结合使用时，会实时发出额外的流事件（message_start、content_block_delta 等）以进行实时 UI 更新。

对于 JSON 并流-JSON 输出，文本 `tool_result.content` 值是
有界于 65,536 UTF-8 之后的字节数 JSON 字符串序列化。超大号
值作为确定性发出 head/tail 预览。同样的界限也适用
到持久流-JSON 会议， SDK 传输、子代理工具结果以及
双输出。文本模式仍然只打印最终响应，同时保留
仅内部有界预览。此限制不限制整个 JSON
会议， JSONL 事件、工具输入或部分消息。

```bash
qwen -p "Write a Python script" --output-format stream-json --include-partial-messages
```

### 输入格式

`--input-format` 参数控制 Qwen Code 如何使用来自标准输入的输入：

- **`text`**（默认）：来自标准输入或命令行参数的标准文本输入
- **`stream-json`**：通过 stdin 进行双向通信的 JSON 消息协议

> **注意：** Stream-json 输入模式目前正在构建中，旨在用于 SDK 集成。需要设置`--output-format stream-json`。

### 文件重定向

将输出保存到文件或通过管道传输到其他命令：

```bash
# Save to file
qwen -p "Explain Docker" > docker-explanation.txt
qwen -p "Explain Docker" --output-format json > docker-explanation.json

# Append to file
qwen -p "Add more details" >> docker-explanation.txt

# Pipe to other tools
qwen -p "What is Kubernetes?" --output-format json | jq -r '.[-1].result'
qwen -p "Explain microservices" | wc -w
qwen -p "List programming languages" | grep -i "python"

# Stream-JSON output for real-time processing
qwen -p "Explain Docker" --output-format stream-json | jq '.type'
qwen -p "Write code" --output-format stream-json --include-partial-messages | jq '.event.type'
```

## 配置选项

无头使用的关键命令行选项：

|选项|描述|示例|
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `--prompt`, `-p`             |以无头模式运行| `qwen -p "query"`                                                        |
| `--output-format`, `-o`      |指定输出格式（text、json、stream-json）| `qwen -p "query" --output-format json`                                   |
| `--input-format`             |指定输入格式（文本、stream-json）| `qwen --input-format text --output-format stream-json`                   |
| `--include-partial-messages` |在 Stream-json 输出中包含部分消息| `qwen -p "query" --output-format stream-json --include-partial-messages` |
| `--system-prompt`            |覆盖本次运行的主会话系统提示| `qwen -p "query" --system-prompt "You are a terse reviewer."`            |
| `--append-system-prompt`     |在此运行的主会话系统提示符中附加额外的指令| `qwen -p "query" --append-system-prompt "Focus on concrete findings."`   |
| `--output-style`             |本次运行的输出样式（`Concise`、`Proactive`、`Explanatory`、`Learning`、自定义样式名称，或 `default` 表示无）；覆盖`general.outputStyle`| `qwen -p "query" --output-style Concise`                                 |
| `--debug`, `-d`              |启用调试模式| `qwen -p "query" --debug`                                                |
| `--safe-mode`                |禁用所有自定义 — 上下文文件、挂钩、扩展、技能、MCP 服务器、自定义子代理（仅加载内置子代理）、权限规则、源自设置的批准模式覆盖、内存功能、跨会话消息传递和沙箱设置 — 以隔离问题； CLI标志`--yolo`和`--approval-mode`仍然有效。请参阅[故障排除](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>)。也可通过 `QWEN_CODE_SAFE_MODE=true` 设置。| `qwen -p "query" --safe-mode`                                            |
| `--model`, `-m`              |用于本次运行的模型| `qwen -p "query" --model qwen3-coder-plus`                               |
| `--include-directories`      |包括附加目录| `qwen -p "query" --include-directories src,docs`                         |
| `--yolo`, `-y`               |自动批准所有操作| `qwen -p "query" --yolo`                                                 |
| `--approval-mode`            |设置审批模式（`plan`、`default`、`auto-edit`、`auto`、`yolo`）| `qwen -p "query" --approval-mode auto-edit`                              |
| `--continue`                 |恢复该项目的最新会话| `qwen --continue -p "Pick up where we left off"`                         |
| `--resume [sessionId]`       |恢复特定会话（或交互选择）| `qwen --resume 123e... -p "Finish the refactor"`                         |
| `--max-session-turns`        |限制运行中的 user/model/tool 圈数| `qwen -p "..." --max-session-turns 30`                                   |
| `--max-wall-time`            |挂钟预算；接受 `90` (s)、`30s`、`5m`、`1h`、`1.5h`| `qwen -p "..." --max-wall-time 10m`                                      |
| `--max-tool-calls`           |运行的累积工具调用预算| `qwen -p "..." --max-tool-calls 50`                                      |

有关所有可用配置选项、设置文件和环境变量的完整详细信息，请参阅[配置指南](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>)。

## 无人值守运行的安全性

无头/CI 与 `--yolo`（或 `--approval-mode=yolo`）结合运行，自动批准每个工具调用，包括 `shell`、`write` 和 `edit`。 **`--yolo` 不启用沙箱** — 这些工具在主机进程的权限级别运行。当 Qwen Code 在未配置沙箱的情况下检测到此组合时，它会在启动时向 stderr 打印一行警告。查看权衡后，使用 `QWEN_CODE_SUPPRESS_YOLO_WARNING=1` 抑制警告。

### 运行级别预算

当无人值守运行超过以下阈值之一时，Qwen Code 可以中止无人值守运行。默认每个都是`-1`（无限制）；设置任何一项都足以限制失控行为。它们是针对已经带有 SIGINT 的相同 `AbortController` 协同执行的，因此预算中止会发出结构化的 `FatalBudgetExceededError`（退出代码 **55**） - 与转帽退出代码 53 和 SIGINT 不同130 因此 CI 脚本可以根据原因进行分支。

|旗帜|设置键|它的界限是什么|
| --------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--max-wall-time`     | `model.maxWallTimeSeconds` |整个跑步的挂钟持续时间。标志接受 `90` (s)、`30s`、`5m`、`1h`、`1.5h`（支持小数单位）。最小 1 秒 — 亚秒值会因拼写错误而被拒绝。设置为秒。|
| `--max-tool-calls`    | `model.maxToolCalls`       |由主运行循环分派的累积顶级工具调用（对成功 _ 和 _ 失败进行计数 - 模型仍然在错误时消耗令牌）。有关子代理/结构化输出豁免，请参阅下面的“范围”。|
| `--max-session-turns` | `model.maxSessionTurns`    |user/model/tool圈数；预先存在的。超出时退出并显示代码 53（与预算退出 55 不同）。|

#### 适用范围

- **`--max-tool-calls` 仅计算顶级调度。** 当模型调用 `agent` 工具时，调度计为 **1**；由生成的子代理执行的内部工具调用不被计算为 ****。通过子代理来集中工作的模型可以在较小的顶层预算下完成无限的内部工作。如果您需要更紧的盖子，请与 `--exclude-tools agent` 结合使用。
- **`structured_output` 不受 `--max-tool-calls` 的约束。** 在 `--json-schema` 下，模型的终端 `structured_output` 调用是“我完成了”合同，而不是真正的工作 — 它不计入 `--max-tool-calls`，因此预算边缘完成不会中止，因为误报。豁免是无条件的（包括失败的 Ajv 验证），因此陷入格式错误的输出重试循环的模型是 NOT，受 `--max-tool-calls` 限制；与 `--max-session-turns` 或 `--max-wall-time` 组合以限制重试。
- **`structured_output` 是 NOT，不受 `--max-session-turns` 的约束。** 该计数器是预先存在的，并且每个回合都会发生碰撞，包括终端合同。如果您想允许 `N` 实际工作在 `--json-schema` 下轮换，请将 `--max-session-turns` 大小调整为 `N+1`。
- **单次 vs `--input-format stream-json`:** 在流 json 输入模式下，守护进程会在每个用户消息开始时重置预算计数器；预算是按消息计算的，而不是按进程计算的。
- **`qwen serve` / ACP 会话：** 守护进程 ACP 会话路径 NOT 当前从 settings.json 咨询 `--max-wall-time` / `--max-tool-calls`。这些预算仅适用于单次 `qwen -p` 运行和 `--input-format stream-json` 会话。 （如果在设置中设置了 `tools.approvalMode: 'yolo'`，`qwen serve` 会在启动时发出 YOLO-无沙箱警告。）

### 推荐组合

- **可信、隔离环境（临时CI运行器、容器）：** `qwen -p "..." --yolo --max-session-turns N --max-wall-time 10m --output-format json`。固定回合预算和挂钟预算，这样卡住的 agent 就不会耗尽你的 CI 分钟，并捕获 `--output-format json` 进行运行后使用/工具调用审核。
- **本地计算机或共享基础设施：**还传递`--sandbox`（或设置`QWEN_SANDBOX=1`），以便shell/写入/编辑工具在沙箱映像内运行。
- ** 具有重试速率限制的长期运行 CI：** 将 `QWEN_CODE_UNATTENDED_RETRY=1` 与 `--max-wall-time` 组合。重试环境使运行在瞬态 429 / 529 响应之后保持活动状态；挂钟预算确保持续失败的供应商无法无限期地延长工作时间。
- **有界审计/探索：**对于只读任务，`--max-tool-calls 25`限制模型可以grep/读取的积极程度。与 `--exclude-tools shell,write,edit` 结合使绑定有意义。

## 示例

### 代码审查

```bash
cat src/auth.py | qwen -p "Review this authentication code for security issues" > security-review.txt
```

### 生成提交消息

```bash
result=$(git diff --cached | qwen -p "Write a concise commit message for these changes" --output-format json)
echo "$result" | jq -r '.[-1].result'
```

### API 文档

```bash
result=$(cat api/routes.js | qwen -p "Generate OpenAPI spec for these routes" --output-format json)
echo "$result" | jq -r '.[-1].result' > openapi.json
```

### 批量代码分析

```bash
for file in src/*.py; do
    echo "Analyzing $file..."
    result=$(cat "$file" | qwen -p "Find potential bugs and suggest improvements" --output-format json)
    echo "$result" | jq -r '.[-1].result' > "reports/$(basename "$file").analysis"
    echo "Completed analysis for $(basename "$file")" >> reports/progress.log
done
```

### PR 代码审查

```bash
result=$(git diff origin/main...HEAD | qwen -p "Review these changes for bugs, security issues, and code quality" --output-format json)
echo "$result" | jq -r '.[-1].result' > pr-review.json
```

### 日志分析

```bash
grep "ERROR" /var/log/app.log | tail -20 | qwen -p "Analyze these errors and suggest root cause and fixes" > error-analysis.txt
```

### 发行说明生成

```bash
result=$(git log --oneline v1.0.0..HEAD | qwen -p "Generate release notes from these commits" --output-format json)
response=$(echo "$result" | jq -r '.[-1].result')
echo "$response"
echo "$response" >> CHANGELOG.md
```

### 模型和工具使用情况跟踪

```bash
result=$(qwen -p "Explain this database schema" --include-directories db --output-format json)
total_tokens=$(echo "$result" | jq -r '.[-1].stats.models // {} | to_entries | map(.value.tokens.total) | add // 0')
models_used=$(echo "$result" | jq -r '.[-1].stats.models // {} | keys | join(", ") | if . == "" then "none" else . end')
tool_calls=$(echo "$result" | jq -r '.[-1].stats.tools.totalCalls // 0')
tools_used=$(echo "$result" | jq -r '.[-1].stats.tools.byName // {} | keys | join(", ") | if . == "" then "none" else . end')
echo "$(date): $total_tokens tokens, $tool_calls tool calls ($tools_used) used with models: $models_used" >> usage.log
echo "$result" | jq -r '.[-1].result' > schema-docs.md
echo "Recent usage trends:"
tail -5 usage.log
```

## 持续重试模式

当 Qwen Code 在 CI/CD 管道中运行或作为后台守护进程运行时，短暂的 API 中断（速率限制或过载）不应终止一个持续数小时的任务。 **持久重试模式** 使Qwen Code 无限期地重试瞬态API 错误，直到服务恢复。

### 它是如何运作的

- **仅限瞬态错误**：HTTP 429（速率限制）和529（过载）会无限期重试。其他错误（400、500等）仍然正常失败。
- **带上限的指数退避**：重试延迟呈指数增长，但每次重试的上限为 **5 分钟 **。
- **心跳保持活动**：在长时间等待期间，每**30秒**都会将状态行打印到stderr，以防止CI运行者因不活动而终止进程。
- **优雅降级**：非瞬态错误和交互模式完全不受影响。

### 激活

将`QWEN_CODE_UNATTENDED_RETRY`环境变量设置为`true`或`1`（严格匹配，区分大小写）：

```bash
export QWEN_CODE_UNATTENDED_RETRY=1
```

> [！重要]
> 持续重试需要**显式选择加入**。 `CI=true` 本身 ** 不会 ** 激活它 - 默默地将快速失败的 CI 作业变成无限等待作业将是危险的。始终在管道配置中显式设置 `QWEN_CODE_UNATTENDED_RETRY`。

### 示例

#### GitHub 行动

```yaml
- name: Automated code review
  env:
    QWEN_CODE_UNATTENDED_RETRY: '1'
  run: |
    qwen -p "Review all files in src/ for security issues" \
      --output-format json \
      --yolo > review.json
```

#### 隔夜批处理

```bash
export QWEN_CODE_UNATTENDED_RETRY=1
qwen -p "Migrate all callback-style functions to async/await in src/" --yolo
```

#### 后台守护进程

```bash
QWEN_CODE_UNATTENDED_RETRY=1 nohup qwen -p "Audit all dependencies for known CVEs" \
  --output-format json > audit.json 2> audit.log &
```

### 监控

在持久重试期间，心跳消息会打印到 **stderr**：

```
[qwen-code] Waiting for API capacity... attempt 3, retry in 45s
[qwen-code] Waiting for API capacity... attempt 3, retry in 15s
```

这些消息使 CI 跑步者保持活力并让您监控进度。它们不会出现在标准输出中，因此通过管道传输到其他工具的 JSON 输出保持干净。

## 资源

- [CLI 配置](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings#command-line-arguments>) - 完整配置指南
- [身份验证](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>) - 设置身份验证
- [命令](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>) - 交互式命令参考
- [教程](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart>) - 分步自动化指南


## 原文对照

<details>
<summary>展开完整原文</summary>

# Headless Mode

Headless mode allows you to run Qwen Code programmatically from command line
scripts and automation tools without any interactive UI. This is ideal for
scripting, automation, CI/CD pipelines, and building AI-powered tools.

## Overview

The headless mode provides a headless interface to Qwen Code that:

- Accepts prompts via command line arguments or stdin
- Returns structured output (text or JSON)
- Supports file redirection and piping
- Enables automation and scripting workflows
- Provides consistent exit codes for error handling
- Can resume previous sessions scoped to the current project for multi-step automation

## Basic Usage

### Direct Prompts

Use the `--prompt` (or `-p`) flag to run in headless mode:

```bash
qwen --prompt "What is machine learning?"
```

### Stdin Input

Pipe input to Qwen Code from your terminal:

```bash
echo "Explain this code" | qwen
```

### Combining with File Input

Read from files and process with Qwen Code:

```bash
cat README.md | qwen --prompt "Summarize this documentation"
```

### Resume Previous Sessions (Headless)

Reuse conversation context from the current project in headless scripts:

```bash
# Continue the most recent session for this project and run a new prompt
qwen --continue -p "Run the tests again and summarize failures"

# Resume a specific session ID directly (no UI)
qwen --resume 123e4567-e89b-12d3-a456-426614174000 -p "Apply the follow-up refactor"
```

> [!note]
>
> - Session data is project-scoped JSONL under `~/.qwen/projects/<sanitized-cwd>/chats`.
> - Restores conversation history, tool outputs, and chat-compression checkpoints before sending the new prompt.

## Run a Persistent Goal

Headless mode accepts `/goal` as the entire prompt. Goal state is stored with the session, so use `--continue` or `--resume <sessionId>` to inspect or control the same Goal from a later process. This requires `general.chatRecording` to remain enabled (the default).

```bash
# Create a Goal and start its worker
qwen -p "/goal Finish the release checklist"

# Inspect its saved state in the same session
qwen --continue -p "/goal"
```

Use the same `qwen --continue -p "<control>"` pattern for the other operations:

| Control                              | Behavior                                                                                 |
| ------------------------------------ | ---------------------------------------------------------------------------------------- |
| `/goal`                              | Report the stored state without calling the model.                                       |
| `/goal <objective>` or `/goal set …` | Create or replace the Goal and start headless Goal work.                                 |
| `/goal edit <objective>`             | Revise a non-completed Goal; work starts immediately when the resulting state is active. |
| `/goal pause`                        | Pause an active Goal without calling the model.                                          |
| `/goal resume`                       | Resume an eligible Goal and start headless Goal work.                                    |
| `/goal clear`                        | Clear the Goal without confirmation or a model call.                                     |

A Goal is only as good as its completion condition. See [Goals](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/goals.md>) for what the verifier can and cannot judge, and use `qwen -p "/goal-draft <intent>"` to have the objective drafted before you set it.

Runtime-scheduled Goal continuation segments do not count against `--max-session-turns`, but real user prompts still do. Explicit `--max-wall-time` and `--max-tool-calls` budgets continue to apply; exceeding either pauses active Goal work before the run exits with the budget-specific error.

With `--output-format stream-json`, each Goal status change emits a `stream_event` whose `event.type` is `goal_state`. This state event is emitted even without `--include-partial-messages`. The `active_goal` event that used to follow it as a compatibility projection is no longer emitted; automation reads `goal_state`.

> [!note]
>
> This behavior applies to standard headless CLI runs. ACP-driven sessions (IDE integrations, Web Shell) drive the same Goal runtime through the `sessionGoalControl` extension method, and receive each status change as a `session/update` notification carrying `_meta.goalState` instead of a `goal_state` stream event.

## Customize the Main Session Prompt

You can change the main session system prompt for a single CLI run without editing shared memory files.

### Override the Built-in System Prompt

Use `--system-prompt` to replace Qwen Code's built-in main-session prompt for the current run:

```bash
qwen -p "Review this patch" --system-prompt "You are a terse release reviewer. Report only blocking issues."
```

### Append Extra Instructions

Use `--append-system-prompt` to keep the built-in prompt and add extra instructions for this run:

```bash
qwen -p "Review this patch" --append-system-prompt "Be terse and focus on concrete findings."
```

You can combine both flags when you want a custom base prompt plus an extra run-specific instruction:

```bash
qwen -p "Summarize this repository" \
  --system-prompt "You are a migration planner." \
  --append-system-prompt "Return exactly three bullets."
```

### Choose an Output Style

Use `--output-style` to pick an [output style](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/output-styles>) — built-in or custom — for this run. A style is a named block of instructions layered onto the built-in prompt that changes how the answer is written — `Concise` leads with the result and drops preamble and narration, `Proactive` starts working instead of proposing, `Explanatory` adds short notes about the codebase along the way. It overrides the `general.outputStyle` setting; `default` selects no style.

```bash
qwen -p "Why does the build fail on Windows?" --output-style Concise
```

> [!note]
>
> - `Learning` asks you to write part of the code and waits for a reply, so it is skipped in headless runs.
> - An unknown style name prints a warning and the run continues with the default style.
> - `--output-style` has no effect when `--system-prompt` or `QWEN_SYSTEM_MD` replaces the built-in prompt — a style is only layered onto the built-in prompt.

> [!note]
>
> - `--system-prompt` applies only to the current run's main session.
> - Loaded memory and context files such as `QWEN.md` are still appended after `--system-prompt`.
> - `--append-system-prompt` is applied after the built-in prompt and loaded memory, and can be used together with `--system-prompt`.

## Output Formats

Qwen Code supports multiple output formats for different use cases:

### Text Output (Default)

Standard human-readable output:

```bash
qwen -p "What is the capital of France?"
```

Response format:

```
The capital of France is Paris.
```

### JSON Output

Returns structured data as a JSON array. All messages are buffered and output together when the session completes. This format is ideal for programmatic processing and automation scripts.

The JSON output is an array of message objects. The output includes multiple message types: system messages (session initialization), assistant messages (AI responses), and result messages (execution summary).

#### Example Usage

```bash
qwen -p "What is the capital of France?" --output-format json
```

Output (at end of execution):

```json
[
  {
    "type": "system",
    "subtype": "session_start",
    "uuid": "...",
    "session_id": "...",
    "model": "qwen3-coder-plus",
    ...
  },
  {
    "type": "assistant",
    "uuid": "...",
    "session_id": "...",
    "message": {
      "id": "...",
      "type": "message",
      "role": "assistant",
      "model": "qwen3-coder-plus",
      "content": [
        {
          "type": "text",
          "text": "The capital of France is Paris."
        }
      ],
      "usage": {...}
    },
    "parent_tool_use_id": null
  },
  {
    "type": "result",
    "subtype": "success",
    "uuid": "...",
    "session_id": "...",
    "is_error": false,
    "duration_ms": 1234,
    "result": "The capital of France is Paris.",
    "usage": {...}
  }
]
```

### Stream-JSON Output

Stream-JSON format emits JSON messages immediately as they occur during execution, enabling real-time monitoring. This format uses line-delimited JSON where each message is a complete JSON object on a single line.

```bash
qwen -p "Explain TypeScript" --output-format stream-json
```

Output (streaming as events occur):

```json
{"type":"system","subtype":"session_start","uuid":"...","session_id":"..."}
{"type":"assistant","uuid":"...","session_id":"...","message":{...}}
{"type":"result","subtype":"success","uuid":"...","session_id":"..."}
```

When combined with `--include-partial-messages`, additional stream events are emitted in real-time (message_start, content_block_delta, etc.) for real-time UI updates.

For JSON and stream-JSON output, textual `tool_result.content` values are
bounded to 65,536 UTF-8 bytes after JSON string serialization. Oversized
values are emitted as deterministic head/tail previews. The same bound applies
to persistent stream-JSON sessions, SDK transports, subagent tool results, and
Dual Output. Text mode still prints only the final response, while retaining
only the bounded preview internally. This limit does not cap an entire JSON
session, JSONL event, tool input, or partial message.

```bash
qwen -p "Write a Python script" --output-format stream-json --include-partial-messages
```

### Input Format

The `--input-format` parameter controls how Qwen Code consumes input from standard input:

- **`text`** (default): Standard text input from stdin or command-line arguments
- **`stream-json`**: JSON message protocol via stdin for bidirectional communication

> **Note:** Stream-json input mode is currently under construction and is intended for SDK integration. It requires `--output-format stream-json` to be set.

### File Redirection

Save output to files or pipe to other commands:

```bash
# Save to file
qwen -p "Explain Docker" > docker-explanation.txt
qwen -p "Explain Docker" --output-format json > docker-explanation.json

# Append to file
qwen -p "Add more details" >> docker-explanation.txt

# Pipe to other tools
qwen -p "What is Kubernetes?" --output-format json | jq -r '.[-1].result'
qwen -p "Explain microservices" | wc -w
qwen -p "List programming languages" | grep -i "python"

# Stream-JSON output for real-time processing
qwen -p "Explain Docker" --output-format stream-json | jq '.type'
qwen -p "Write code" --output-format stream-json --include-partial-messages | jq '.event.type'
```

## Configuration Options

Key command-line options for headless usage:

| Option                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                             | Example                                                                  |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `--prompt`, `-p`             | Run in headless mode                                                                                                                                                                                                                                                                                                                                                                                                                                    | `qwen -p "query"`                                                        |
| `--output-format`, `-o`      | Specify output format (text, json, stream-json)                                                                                                                                                                                                                                                                                                                                                                                                         | `qwen -p "query" --output-format json`                                   |
| `--input-format`             | Specify input format (text, stream-json)                                                                                                                                                                                                                                                                                                                                                                                                                | `qwen --input-format text --output-format stream-json`                   |
| `--include-partial-messages` | Include partial messages in stream-json output                                                                                                                                                                                                                                                                                                                                                                                                          | `qwen -p "query" --output-format stream-json --include-partial-messages` |
| `--system-prompt`            | Override the main session system prompt for this run                                                                                                                                                                                                                                                                                                                                                                                                    | `qwen -p "query" --system-prompt "You are a terse reviewer."`            |
| `--append-system-prompt`     | Append extra instructions to the main session system prompt for this run                                                                                                                                                                                                                                                                                                                                                                                | `qwen -p "query" --append-system-prompt "Focus on concrete findings."`   |
| `--output-style`             | Output style for this run (`Concise`, `Proactive`, `Explanatory`, `Learning`, a custom style's name, or `default` for none); overrides `general.outputStyle`                                                                                                                                                                                                                                                                                            | `qwen -p "query" --output-style Concise`                                 |
| `--debug`, `-d`              | Enable debug mode                                                                                                                                                                                                                                                                                                                                                                                                                                       | `qwen -p "query" --debug`                                                |
| `--safe-mode`                | Disable all customizations — context files, hooks, extensions, skills, MCP servers, custom subagents (only built-in subagents load), permission rules, settings-sourced approval mode overrides, memory features, cross-session messaging, and sandbox settings — to isolate problems; the CLI flags `--yolo` and `--approval-mode` still take effect. See [Troubleshooting](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/support/troubleshooting>). Also settable via `QWEN_CODE_SAFE_MODE=true`. | `qwen -p "query" --safe-mode`                                            |
| `--model`, `-m`              | Model to use for this run                                                                                                                                                                                                                                                                                                                                                                                                                               | `qwen -p "query" --model qwen3-coder-plus`                               |
| `--include-directories`      | Include additional directories                                                                                                                                                                                                                                                                                                                                                                                                                          | `qwen -p "query" --include-directories src,docs`                         |
| `--yolo`, `-y`               | Auto-approve all actions                                                                                                                                                                                                                                                                                                                                                                                                                                | `qwen -p "query" --yolo`                                                 |
| `--approval-mode`            | Set approval mode (`plan`, `default`, `auto-edit`, `auto`, `yolo`)                                                                                                                                                                                                                                                                                                                                                                                      | `qwen -p "query" --approval-mode auto-edit`                              |
| `--continue`                 | Resume the most recent session for this project                                                                                                                                                                                                                                                                                                                                                                                                         | `qwen --continue -p "Pick up where we left off"`                         |
| `--resume [sessionId]`       | Resume a specific session (or choose interactively)                                                                                                                                                                                                                                                                                                                                                                                                     | `qwen --resume 123e... -p "Finish the refactor"`                         |
| `--max-session-turns`        | Cap the number of user/model/tool turns in the run                                                                                                                                                                                                                                                                                                                                                                                                      | `qwen -p "..." --max-session-turns 30`                                   |
| `--max-wall-time`            | Wall-clock budget; accepts `90` (s), `30s`, `5m`, `1h`, `1.5h`                                                                                                                                                                                                                                                                                                                                                                                          | `qwen -p "..." --max-wall-time 10m`                                      |
| `--max-tool-calls`           | Cumulative tool-call budget for the run                                                                                                                                                                                                                                                                                                                                                                                                                 | `qwen -p "..." --max-tool-calls 50`                                      |

For complete details on all available configuration options, settings files, and environment variables, see the [Configuration Guide](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings>).

## Safety in unattended runs

Headless / CI runs combined with `--yolo` (or `--approval-mode=yolo`) auto-approve every tool call, including `shell`, `write`, and `edit`. **`--yolo` does not enable a sandbox** — those tools run at the host process's privilege level. When Qwen Code detects this combination with no sandbox configured, it prints a one-line warning to stderr at startup. Suppress the warning with `QWEN_CODE_SUPPRESS_YOLO_WARNING=1` once you've reviewed the trade-off.

### Run-level budgets

Qwen Code can abort an unattended run when it crosses one of the following thresholds. Each is `-1` (unlimited) by default; setting any one is enough to bound runaway behavior. They are enforced cooperatively against the same `AbortController` that already carries SIGINT, so a budget abort emits a structured `FatalBudgetExceededError` (exit code **55**) — distinct from the turn-cap exit code 53 and SIGINT's 130 so CI scripts can branch on the reason.

| Flag                  | Settings key               | What it bounds                                                                                                                                                                                                |
| --------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--max-wall-time`     | `model.maxWallTimeSeconds` | Wall-clock duration of the whole run. Flag accepts `90` (s), `30s`, `5m`, `1h`, `1.5h` (fractional units supported). Minimum 1s — sub-second values are rejected as typos. Settings is seconds.               |
| `--max-tool-calls`    | `model.maxToolCalls`       | Cumulative top-level tool calls dispatched by the main run loop (counts successes _and_ failures — the model still consumes tokens on errors). See "Scope" below for subagent / structured-output exemptions. |
| `--max-session-turns` | `model.maxSessionTurns`    | Number of user/model/tool turns; pre-existing. Exits with code 53 on overrun (distinct from budget exit 55).                                                                                                  |

#### Scope

- **`--max-tool-calls` counts top-level dispatches only.** When the model calls the `agent` tool, the dispatch counts as **1**; inner tool calls performed by the spawned subagent are **not** counted. A model that funnels work through subagents can do unbounded inner work under a small top-level budget. Combine with `--exclude-tools agent` if you need a tighter cap.
- **`structured_output` is exempt from `--max-tool-calls`.** Under `--json-schema`, the model's terminal `structured_output` call is the "I'm done" contract, not real work — it doesn't count against `--max-tool-calls` so a budget-edge completion isn't aborted as a false positive. The exemption is unconditional (including failed Ajv validations), so a model stuck in a malformed-output retry loop is NOT bounded by `--max-tool-calls`; combine with `--max-session-turns` or `--max-wall-time` to cap retries.
- **`structured_output` is NOT exempt from `--max-session-turns`.** That counter is pre-existing and bumps for every turn including the terminal contract. Size `--max-session-turns` to `N+1` if you want to allow `N` real-work turns under `--json-schema`.
- **Single-shot vs `--input-format stream-json`:** in stream-json input mode the daemon resets the budget counters at the start of every user message; the budget is per-message, not per-process.
- **`qwen serve` / ACP sessions:** the daemon ACP session path does NOT currently consult `--max-wall-time` / `--max-tool-calls` from settings.json. These budgets only apply to single-shot `qwen -p` runs and to `--input-format stream-json` sessions. (`qwen serve` does emit the YOLO-no-sandbox warning at boot if `tools.approvalMode: 'yolo'` is set in settings.)

### Recommended combinations

- **Trusted, isolated environment (ephemeral CI runner, container):** `qwen -p "..." --yolo --max-session-turns N --max-wall-time 10m --output-format json`. Pin a turn budget and a wall-clock budget so a stuck agent can't burn through your CI minutes, and capture `--output-format json` for post-run usage / tool-call auditing.
- **Local machine or shared infra:** also pass `--sandbox` (or set `QWEN_SANDBOX=1`) so shell / write / edit tools run inside the sandbox image.
- **Long-running CI with retry-on-rate-limit:** combine `QWEN_CODE_UNATTENDED_RETRY=1` with `--max-wall-time`. The retry env keeps the run alive past transient 429 / 529 responses; the wall-clock budget ensures a persistently-failing provider can't extend the job indefinitely.
- **Bounded auditing / exploration:** for read-only tasks, `--max-tool-calls 25` caps how aggressively the model can grep / read. Combine with `--exclude-tools shell,write,edit` to make the bound meaningful.

## Examples

### Code review

```bash
cat src/auth.py | qwen -p "Review this authentication code for security issues" > security-review.txt
```

### Generate commit messages

```bash
result=$(git diff --cached | qwen -p "Write a concise commit message for these changes" --output-format json)
echo "$result" | jq -r '.[-1].result'
```

### API documentation

```bash
result=$(cat api/routes.js | qwen -p "Generate OpenAPI spec for these routes" --output-format json)
echo "$result" | jq -r '.[-1].result' > openapi.json
```

### Batch code analysis

```bash
for file in src/*.py; do
    echo "Analyzing $file..."
    result=$(cat "$file" | qwen -p "Find potential bugs and suggest improvements" --output-format json)
    echo "$result" | jq -r '.[-1].result' > "reports/$(basename "$file").analysis"
    echo "Completed analysis for $(basename "$file")" >> reports/progress.log
done
```

### PR code review

```bash
result=$(git diff origin/main...HEAD | qwen -p "Review these changes for bugs, security issues, and code quality" --output-format json)
echo "$result" | jq -r '.[-1].result' > pr-review.json
```

### Log analysis

```bash
grep "ERROR" /var/log/app.log | tail -20 | qwen -p "Analyze these errors and suggest root cause and fixes" > error-analysis.txt
```

### Release notes generation

```bash
result=$(git log --oneline v1.0.0..HEAD | qwen -p "Generate release notes from these commits" --output-format json)
response=$(echo "$result" | jq -r '.[-1].result')
echo "$response"
echo "$response" >> CHANGELOG.md
```

### Model and tool usage tracking

```bash
result=$(qwen -p "Explain this database schema" --include-directories db --output-format json)
total_tokens=$(echo "$result" | jq -r '.[-1].stats.models // {} | to_entries | map(.value.tokens.total) | add // 0')
models_used=$(echo "$result" | jq -r '.[-1].stats.models // {} | keys | join(", ") | if . == "" then "none" else . end')
tool_calls=$(echo "$result" | jq -r '.[-1].stats.tools.totalCalls // 0')
tools_used=$(echo "$result" | jq -r '.[-1].stats.tools.byName // {} | keys | join(", ") | if . == "" then "none" else . end')
echo "$(date): $total_tokens tokens, $tool_calls tool calls ($tools_used) used with models: $models_used" >> usage.log
echo "$result" | jq -r '.[-1].result' > schema-docs.md
echo "Recent usage trends:"
tail -5 usage.log
```

## Persistent Retry Mode

When Qwen Code runs in CI/CD pipelines or as a background daemon, a brief API outage (rate limiting or overload) should not kill a multi-hour task. **Persistent retry mode** makes Qwen Code retry transient API errors indefinitely until the service recovers.

### How it works

- **Transient errors only**: HTTP 429 (Rate Limit) and 529 (Overloaded) are retried indefinitely. Other errors (400, 500, etc.) still fail normally.
- **Exponential backoff with cap**: Retry delays grow exponentially but are capped at **5 minutes** per retry.
- **Heartbeat keepalive**: During long waits, a status line is printed to stderr every **30 seconds** to prevent CI runners from killing the process due to inactivity.
- **Graceful degradation**: Non-transient errors and interactive mode are completely unaffected.

### Activation

Set the `QWEN_CODE_UNATTENDED_RETRY` environment variable to `true` or `1` (strict match, case-sensitive):

```bash
export QWEN_CODE_UNATTENDED_RETRY=1
```

> [!important]
> Persistent retry requires an **explicit opt-in**. `CI=true` alone does **not** activate it — silently turning a fast-fail CI job into an infinite-wait job would be dangerous. Always set `QWEN_CODE_UNATTENDED_RETRY` explicitly in your pipeline configuration.

### Examples

#### GitHub Actions

```yaml
- name: Automated code review
  env:
    QWEN_CODE_UNATTENDED_RETRY: '1'
  run: |
    qwen -p "Review all files in src/ for security issues" \
      --output-format json \
      --yolo > review.json
```

#### Overnight batch processing

```bash
export QWEN_CODE_UNATTENDED_RETRY=1
qwen -p "Migrate all callback-style functions to async/await in src/" --yolo
```

#### Background daemon

```bash
QWEN_CODE_UNATTENDED_RETRY=1 nohup qwen -p "Audit all dependencies for known CVEs" \
  --output-format json > audit.json 2> audit.log &
```

### Monitoring

During persistent retry, heartbeat messages are printed to **stderr**:

```
[qwen-code] Waiting for API capacity... attempt 3, retry in 45s
[qwen-code] Waiting for API capacity... attempt 3, retry in 15s
```

These messages keep CI runners alive and let you monitor progress. They do not appear in stdout, so JSON output piped to other tools remains clean.

## Resources

- [CLI Configuration](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/settings#command-line-arguments>) - Complete configuration guide
- [Authentication](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/configuration/auth.md>) - Setup authentication
- [Commands](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/features/commands>) - Interactive commands reference
- [Tutorials](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/docs/users/quickstart>) - Step-by-step automation guides


</details>
