# 通过 OpenTelemetry 监控 Agent 使用

介绍采集轨迹、模型请求、工具和 Hook 执行信息的配置，说明数据字段与命名空间，用于分析运行行为。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方审核：[2026-10-07](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/guides/monitoring-agents.md>)；官方文件提交（截至抓取）：[2026-10-07](<https://github.com/microsoft/vscode-docs/commit/a2f9dd4e642a53cf17ecda8d9568611404ce2740>)；本地抓取：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/microsoft/vscode-docs/main/docs/agents/guides/monitoring-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/通过 OpenTelemetry 监控 Agent 使用（存档2026-10-10-306）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 4e7a2c91-b8d3-4f6e-a1c5-9d0e3f7b2a84
DateApproved: 10/7/2026
MetaDescription: Monitor GitHub Copilot agents in {% data variables.product.prodname_vscode_shortname %} with OpenTelemetry traces, metrics, events, and identity controls.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- monitoring
- telemetry
- OpenTelemetry
- OTel
- traces
- metrics
- agents
- enterprise
- managed settings
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用 OpenTelemetry 监控 agent 使用情况

本文介绍如何为 {% data variables.product.prodname_vscode_shortname %} 中的 Copilot 聊天 agent 交互启用和配置 OpenTelemetry 监控。

Copilot Chat 可以通过 [OpenTelemetry](<https://opentelemetry.io/>) (OTel) 导出跟踪、指标和事件，让您可以了解 agent 交互、LLM 调用、工具执行和令牌使用情况。所有信号名称和属性均遵循 [OTel GenAI 语义约定 ](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>)，因此数据可与任何 OTel 兼容的后端配合使用。

> [!TIP]
> 要在不设置 OTel 后端的情况下交互式查看缓存指标，请使用 [Cache Explorer](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/cache-explorer.md>) 直接在 {% data variables.product.prodname_vscode_shortname %} 中检查提示缓存命中率。

## 收集什么

Copilot Chat 发出三种类型的 OTel 信号：跟踪、指标和事件。

### 属性命名空间

Copilot Chat 在三个命名空间下发出 OTel 属性：

|命名空间|来源|何时使用|
|---|---|---|
| `gen_ai.*` |[OTel GenAI 语义约定](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>)|每当标准密钥存在时|
| `github.copilot.*` |规范 Copilot 特定命名空间，与 [{% data variables.copilot.copilot_cli %} OpenTelemetry 约定 ](<https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference#opentelemetry-monitoring>) 共享|新仪表板、警报和查询的首选|
| `copilot_chat.*` |原始 {% data variables.product.prodname_vscode_shortname %} 扩展命名空间|遗产。现在有几个按键与 `github.copilot.*` 等效键一起双发射|

旧版 `copilot_chat.*` 键继续无限期地发出，因此现有收集器、仪表板和下游消费者可以继续工作而无需更改。没有日落日期。本节中的表将双发射行标记为 **Legacy**，并带有指向首选键的指针。

### 痕迹

每个 agent 交互都会生成一个捕获完整执行流程的分层跨树：

```text
invoke_agent copilot                           [~15s]
  ├── chat gpt-4o                              [~3s]  (LLM requests tool calls)
  ├── execute_tool readFile                    [~50ms]
  ├── execute_tool runCommand                  [~2s]
  ├── chat gpt-4o                              [~4s]  (LLM generates final response)
  └── (span ends)
```

当 agent 调用子代理时（例如，通过 `runSubagent` 工具），跟踪上下文会自动传播。子代理的 `invoke_agent` 范围显示为父 agent 的 `execute_tool` 范围的子级，从而生成跨异步边界的连接跟踪树。

#### `invoke_agent`跨度

包装整个 agent 编排，包括所有 LLM 调用和工具执行。

|属性|描述|
|---|---|
| `gen_ai.operation.name` |总是`invoke_agent`|
| `gen_ai.provider.name` |提供商（例如，`github`）|
| `gen_ai.agent.name` |Agent 名称（例如，`copilot`、`copilotcli`、`claude`）|
| `gen_ai.conversation.id` |对话会话 ID|
| `gen_ai.request.model` |要求的型号|
| `gen_ai.response.model` |解析模型|
| `gen_ai.usage.input_tokens` |整个会话的输入令牌总数|
| `gen_ai.usage.output_tokens` |整个会话的总输出令牌|
| `gen_ai.usage.cache_read.input_tokens` |缓存读取输入令牌（如果可用）|
| `gen_ai.usage.cache_creation.input_tokens` |缓存创建输入令牌（如果可用）|
| `github.copilot.agent.type` |`builtin`、`custom` 或 `plugin`|
| `github.copilot.git.repository` |存储库远程 URL，位于 Git 存储库中时|
| `github.copilot.git.branch` |活动分支，位于 Git 存储库中时|
| `github.copilot.git.commit_sha` |当前提交，在 Git 存储库中|
| `github.copilot.github.org` |GitHub 组织所有者，仅适用于 GitHub 遥控器|
| `copilot_chat.repo.remote_url` |遗产。优先选择`github.copilot.git.repository`|
| `copilot_chat.repo.head_branch_name` |遗产。优先选择`github.copilot.git.branch`|
| `copilot_chat.repo.head_commit_hash` |遗产。优先选择`github.copilot.git.commit_sha`|
| `copilot_chat.turn_count` |LLM 本场往返|
| `error.type` |错误类别，失败时|
| `gen_ai.input.messages` |完整提示消息（仅限内容捕获）|
| `gen_ai.output.messages` |完整响应消息（仅限内容捕获）|
| `gen_ai.tool.definitions` |工具架构（仅限内容捕获）|

#### `chat`跨度

每个 LLM API 调用一个跨度。

|属性|描述|
|---|---|
| `gen_ai.operation.name` |总是`chat`|
| `gen_ai.provider.name` |提供商名称|
| `gen_ai.request.model` |要求的型号|
| `gen_ai.response.model` |解析模型|
| `gen_ai.response.finish_reasons` |停止原因（例如，`["stop"]`）|
| `gen_ai.request.max_tokens` |最大输出令牌|
| `gen_ai.request.temperature` |设定温度|
| `gen_ai.request.top_p` |Top-p，设置后|
| `gen_ai.usage.input_tokens` |本次调用的输入令牌|
| `gen_ai.usage.output_tokens` |本次调用的输出令牌|
| `gen_ai.usage.cache_read.input_tokens` |缓存读取输入令牌（如果可用）|
| `gen_ai.usage.cache_creation.input_tokens` |缓存创建输入令牌（如果可用）|
| `gen_ai.usage.reasoning.output_tokens` |推理令牌（如果可用）|
| `gen_ai.usage.reasoning_tokens` |遗产。优先选择`gen_ai.usage.reasoning.output_tokens`|
| `copilot_chat.time_to_first_token` |第一个 SSE 令牌的时间（毫秒）|
| `server.address` |API 主机名|
| `error.type` |错误类别，失败时|

#### `execute_tool`跨度

每次工具调用一个跨度。

|属性|描述|
|---|---|
| `gen_ai.operation.name` |总是`execute_tool`|
| `gen_ai.tool.name` |工具名称（例如，`readFile`）|
| `gen_ai.tool.type` |`function` 或 `extension`（MCP 工具）|
| `gen_ai.tool.call.id` |工具调用标识符|
| `github.copilot.tool.parameters.edit_type` |对于编辑工具：`create`、`update`、`str_replace` 或 `insert`|
| `github.copilot.tool.parameters.skill_name` |对于技能调用|
| `github.copilot.tool.parameters.mcp_server_name_hash` |对于 MCP 工具：服务器名称的 SHA-256|
| `github.copilot.tool.parameters.mcp_tool_name` |对于 MCP 工具：调用的工具名称|
| `github.copilot.tool.parameters.command` |对于 shell 工具（仅内容捕获，已截断）|
| `github.copilot.tool.parameters.file_path` |用于文件工具（仅限内容捕获）|
| `github.copilot.tool.parameters.mcp_server_name` |对于 MCP 工具（仅限内容捕获）|
| `error.type` |错误类别，失败时|
| `gen_ai.tool.call.arguments` |工具输入参数（仅限内容捕获）|
| `gen_ai.tool.call.result` |工具输出（仅限内容捕获）|

#### `execute_hook`跨度

每个钩子执行一个跨度（例如，`PreToolUse`、`Stop`）。

|属性|描述|
|---|---|
| `gen_ai.operation.name` |总是`execute_hook`|
| `github.copilot.hook.decision` |`pass`、`block` 或 `non_blocking_error`|
| `github.copilot.hook.duration` |钩子执行持续时间（秒）|
| `github.copilot.hook.tool_names` |挂钩作用域的工具，作为 JSON 数组|
| `copilot_chat.hook_type` |挂钩事件（例如，`PreToolUse`）|
| `copilot_chat.hook_result_kind` |`success`、`error` 或 `non_blocking_error`|
| `copilot_chat.hook_input` |挂钩输入有效负载（截断）|
| `copilot_chat.hook_output` |挂钩标准输出，成功时（截断）|
| `error.type` |错误类别，失败时|

### 指标

GenAI 语义约定指标：

|公制|类型|描述|
|---|---|---|
| `gen_ai.client.operation.duration` |直方图|LLM API 通话时长（秒）|
| `gen_ai.client.token.usage` |直方图|令牌计数（输入和输出）|

特定于扩展的指标：

|公制|类型|描述|
|---|---|---|
| `copilot_chat.tool.call.count` |柜台|按名称和成功进行的工具调用|
| `copilot_chat.tool.call.duration` |直方图|工具执行延迟（毫秒）|
| `copilot_chat.agent.invocation.duration` |直方图|Agent 端到端持续时间（秒）|
| `copilot_chat.agent.turn.count` |直方图|LLM 每次 agent 调用的往返次数|
| `copilot_chat.session.count` |柜台|聊天会话开始|
| `copilot_chat.time_to_first_token` |直方图|第一个 SSE 令牌的时间（秒）|

Agent 活动和结果指标跟踪所有界面上的代理代码更改（内联聊天、本地 agents、Copilot agents、Claude agents 和 {% data variables.copilot.copilot_cloud_agent %}）：

|公制|类型|描述|
|---|---|---|
| `copilot_chat.edit.acceptance.count` |柜台|编辑接受和拒绝决策（内联聊天、聊天编辑、大块级别）|
| `copilot_chat.chat_edit.outcome.count` |柜台|文件级聊天编辑会话结果（接受、拒绝、保存）|
| `copilot_chat.lines_of_code.count` |柜台|通过接受的 agent 编辑添加或删除的代码行|
| `copilot_chat.edit.survival.four_gram` |直方图|4-gram 文本相似度生存分数（0-1）|
| `copilot_chat.edit.survival.no_revert` |直方图|无回复生存评分 (0-1)|
| `copilot_chat.user.action.count` |柜台|用户参与操作：复制、插入、应用、跟进|
| `copilot_chat.user.feedback.count` |柜台|对聊天回复的赞成票和反对票|
| `copilot_chat.agent.edit_response.count` |柜台|Agent 按成功或错误编辑响应|
| `copilot_chat.agent.summarization.count` |柜台|上下文总结结果（已应用、失败）|
| `copilot_chat.pull_request.count` |柜台|通过 Copilot agent 创建的拉取请求|
| `copilot_chat.cloud.session.count` |柜台|合作伙伴 agent 的云和远程 agent 会话|
| `copilot_chat.cloud.pr_ready.count` |柜台|远程 agent 作业 PR- 就绪通知|

指标包括用于过滤的属性，例如`gen_ai.request.model`、`gen_ai.provider.name`、`gen_ai.tool.name`、`copilot_chat.edit.source`和`error.type`。

### 活动

|活动|描述|
|---|---|
| `gen_ai.client.inference.operation.details` |包含模型、令牌和完成原因的完整 LLM 调用元数据|
| `copilot_chat.session.start` |当新的聊天会话开始时发出|
| `copilot_chat.tool.call` |每个工具的调用以及时间和错误详细信息|
| `copilot_chat.agent.turn` |每回合 LLM 往返，带令牌计数|
| `copilot_chat.edit.feedback` |用户接受或拒绝文件级 agent 编辑|
| `copilot_chat.edit.hunk.action` |用户接受或拒绝了某个帅哥|
| `copilot_chat.inline.done` |内联聊天编辑被接受或拒绝|
| `copilot_chat.edit.survival` |定期测量AI-生成的代码在接受后存活的程度|
| `copilot_chat.user.feedback` |用户对聊天回复进行投票（竖起大拇指或竖起大拇指）|
| `copilot_chat.cloud.session.invoke` |云或远程 agent 会话已启动|

### 资源属性

所有信号都带有这些资源属性：

|属性|价值|
|---|---|
| `service.name` |`copilot-chat`（可通过`OTEL_SERVICE_NAME`进行配置）|
| `service.version` |扩展版本|
| `session.id` |每个 {% data variables.product.prodname_vscode_shortname %} 窗口都是唯一的|

使用 `OTEL_RESOURCE_ATTRIBUTES` 添加自定义资源属性，以按团队、部门或其他组织边界进行筛选：

```bash
export OTEL_RESOURCE_ATTRIBUTES="team.id=platform,department=engineering"
```

### 身份捕获

对于本地线束会话，身份捕获可以将 agent 活动归因于单个开发人员和机器。启用后，遥测包括：

* agent 调用范围上的 `user.name`，包括子代理和内联聊天范围。
* `process.user.name` 和 `host.name` 作为资源属性。

使用 `setting(github.copilot.chat.otel.captureIdentity)` 或 `COPILOT_OTEL_CAPTURE_IDENTITY=true` 启用身份捕获。该控件独立于[内容捕获](<#内容捕捉>)。

> [！CAUTION]
> 身份属性可以识别开发人员及其机器。仅当您的组织对此数据有适当的目的、访问控制和保留策略时才启用身份捕获。

> [！NOTE]
> 身份捕获当前仅适用于本地线束。 Agent 主机遥测不包括这些身份属性。

### 内容捕捉

默认情况下，不会捕获任何提示内容、响应或工具参数。仅包含模型名称、令牌计数和持续时间等元数据。

要捕获完整内容，请启用 `setting(github.copilot.chat.otel.captureContent)` 设置或设置 `COPILOT_OTEL_CAPTURE_CONTENT=true`。这将使用完整的提示消息、响应消息、系统提示、工具架构、工具参数和工具结果填充跨度属性。

> [！CAUTION]
> 内容捕获可能包括敏感信息，例如代码、文件内容和用户提示。仅在受信任的环境中启用此功能。

## 启用 OTel 监控

当满足以下任一条件时，OTel 将激活：

* `setting(github.copilot.chat.otel.enabled)` 是 `true`
* `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` 是 `true`
* `COPILOT_OTEL_ENABLED=true`
* `OTEL_EXPORTER_OTLP_ENDPOINT` 已设置

### {% data variables.product.prodname_vscode_shortname %}设置

打开**设置**（`kb(workbench.action.openSettings)`）并搜索`copilot otel`：

|设置|类型|默认|描述|
|---|---|---|---|
| `setting(github.copilot.chat.otel.enabled)` |布尔值| `false` |启用 OTel 发射|
| `setting(github.copilot.chat.otel.exporterType)` |字符串| `"otlp-http"` |`otlp-http`、`otlp-grpc`、`console` 或 `file`|
| `setting(github.copilot.chat.otel.otlpEndpoint)` |字符串| `"http://localhost:4318"` |OTLP收集器端点|
| `setting(github.copilot.chat.otel.captureContent)` |布尔值| `false` |捕获完整的提示和响应内容|
| `setting(github.copilot.chat.otel.captureIdentity)` |布尔值| `false` |捕获本地线束会话的经过身份验证的帐户名、操作系统用户名和计算机主机名|
| `setting(github.copilot.chat.otel.maxAttributeSizeChars)` |整数| `0` |每个内容属性的最大字符数（提示、工具参数、工具结果）。 `0` 禁用截断。设置一个正值以匹配后端的每个属性大小限制。|
| `setting(github.copilot.chat.otel.outfile)` |字符串| `""` |JSON-行输出的文件路径|
| `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` |布尔值| `false` |Persist OTel 跨越到本地 SQLite 数据库以执行 **Chat：导出 Agent 跟踪 DB** 命令。隐式启用 OTel。|

### 环境变量

环境变量始终优先于 {% data variables.product.prodname_vscode_shortname %} 设置。当您的组织要求通过 [Copilot 托管设置 ](<#管理您组织的-otel-配置>) 进行 OTel 配置时，这些托管值将覆盖用户设置。在 Copilot 聊天扩展中，OTel 环境变量仍然可以覆盖托管值。

|变量|默认|描述|
|---|---|---|
| `COPILOT_OTEL_ENABLED` | `false` |启用 OTel。当设置 `OTEL_EXPORTER_OTLP_ENDPOINT` 时也启用。|
| `COPILOT_OTEL_ENDPOINT` | |OTLP端点URL（优先于`OTEL_EXPORTER_OTLP_ENDPOINT`）|
| `OTEL_EXPORTER_OTLP_ENDPOINT` | |标准 OTel OTLP 端点 URL|
| `OTEL_EXPORTER_OTLP_PROTOCOL` | `http/protobuf` |OTLP协议。只有 `grpc` 会改变行为。|
| `COPILOT_OTEL_PROTOCOL` | |覆盖 OTLP 协议（`grpc` 或 `http`）。优先于 `OTEL_EXPORTER_OTLP_PROTOCOL`。|
| `OTEL_SERVICE_NAME` | `copilot-chat` |资源属性中的服务名称|
| `OTEL_RESOURCE_ATTRIBUTES` | |额外资源属性（`key1=val1,key2=val2`）|
| `COPILOT_OTEL_CAPTURE_CONTENT` | `false` |捕获完整的提示和响应内容|
| `COPILOT_OTEL_CAPTURE_IDENTITY` | `false` |捕获本地线束会话的开发人员和机器身份|
| `COPILOT_OTEL_MAX_ATTRIBUTE_SIZE_CHARS` | `0` |覆盖内容属性的最大字符大小。 `0` 禁用截断。优先于 `maxAttributeSizeChars` 设置。|
| `COPILOT_OTEL_LOG_LEVEL` | `info` |最低日志级别：`trace`、`debug`、`info`、`warn` 或 `error`。|
| `COPILOT_OTEL_FILE_EXPORTER_PATH` | |将所有信号作为 JSON 行写入该文件。|
| `COPILOT_OTEL_HTTP_INSTRUMENTATION` | `false` |启用 HTTP- 级 OTel 仪器。|
| `OTEL_EXPORTER_OTLP_HEADERS` | |身份验证标头（例如，`Authorization=Bearer token`）|

### 命令

当 `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` 为 `true` 时，Copilot Chat 会将 OTel 跨度保留到本地 SQLite 数据库。这对于离线检查或共享跟踪数据而不运行 OTLP 后端非常有用。

|命令|描述|
|---|---|
|**聊天：导出 Agent 痕迹 DB** (`github.copilot.chat.otel.exportAgentTracesDB`)|将本地 SQLite span 数据库导出到 `.db` 文件。仅当 `dbSpanExporter.enabled` 设置为 `true` 时可用。|

## 管理您组织的 OTel 配置

企业可以通过 {% data variables.product.prodname_copilot_short %} 企业管理的设置集中强制 OTel 导出配置，以便遥测数据流向批准的收集器，而无需每个开发人员设置 `OTEL_*` 环境变量。托管遥测配置适用于 Copilot 聊天扩展和 agent 主机进程。

有关交付方法、配置字段、客户端支持和优先级，请参阅 [`telemetry` 在企业管理的设置参考中](<https://docs.github.com/en/copilot/reference/enterprise-administrators/enterprise-managed-settings#telemetry>)。对于特定的行为 {% data variables.product.prodname_vscode_shortname %}，参见 [应用托管遥测 {% data variables.product.prodname_vscode_shortname %}](<https://raw.githubusercontent.com/docs/enterprise/manage-ai-settings.md#apply-managed-telemetry-in-vs-code>).

对于使用本地线束的聊天会话，当启用导出的托管 OTel 配置在 Copilot 聊天启动后到达时，{% data variables.product.prodname_vscode_shortname %} 会在每个编辑器会话中自动尝试一次重新启动分机主机。重新启动可能会中断其他分机。如果自动重新启动无法完成，或者为了以后的策略更改，请在出现提示时选择 **重新加载窗口 **。

> [!NOTE]
> 配置托管遥测值时，它将覆盖用户设置。托管身份捕获还覆盖 `COPILOT_OTEL_CAPTURE_IDENTITY`，托管资源属性覆盖 `OTEL_RESOURCE_ATTRIBUTES`。从受管设备中删除其他冲突的 OTel 环境变量，以确保企业配置在 Copilot 聊天扩展中生效。

## 背景和 Claude agents 的迹线结构

启用 OTel 后，将自动检测所有 agent 类型。启用前台 agent 跟踪的相同设置也会启用 Copilot 和 Claude agent 跟踪。

### Copilot

{% data variables.copilot.copilot_sdk_short %} 在与聊天扩展相同的 {% data variables.product.prodname_vscode_shortname %} 进程中运行，并生成丰富的跟踪层次结构，其中包括子代理、权限、挂钩和工具调用。扩展包装器跨度（`invoke_agent copilotcli`，服务 `copilot-chat`）是 SDK 的本机跨度（服务 `github-copilot`）的父级。两者都出现在收集器中的同一跟踪中。

Copilot 会话还在 {% data variables.product.prodname_vscode_shortname %} 的 **Agent 调试日志 ** 面板中显示完整的 SDK 层次结构，与跟踪查看器中显示的内容相同。即使 OTel 导出被禁用，调试面板也可以工作，因为 SDK 的内部跟踪对于面板始终处于活动状态。

禁用 OTel 导出时，调试面板会自动捕获完整的提示和响应内容。启用 OTel 导出后，`setting(github.copilot.chat.otel.captureContent)` 设置控制调试面板和 OTLP 导出的内容捕获。

### {% data variables.copilot.copilot_cli_short %}（终端会话）

当您在终端中运行 {% data variables.copilot.copilot_cli_short %} 会话（**新 {% data variables.copilot.copilot_cli_short %} 会话**）时，它会在单独的进程中运行。当启用 OTel 时，分机会将 `COPILOT_OTEL_ENABLED` 和 `OTEL_EXPORTER_OTLP_ENDPOINT` 转发到终端进程。终端跟踪在服务 `github-copilot` 下显示为独立的根跟踪，并且不链接到扩展跟踪。

CLI运行时仅支持`otlp-http`。当配置`otlp-grpc`时，终端CLI仍然使用HTTP。在同一端口上为两种协议提供服务的后端（例如 Aspire Dashboard）可以透明地工作。

### Claude agent

启用 OTel 时，Claude agent 会话会在服务 `copilot-chat` 下生成遵循 GenAI 语义约定的扩展级跨度。该扩展通过拦截 Claude SDK 消息并通过本地 HTTP 服务器代理 LLM 调用来创建跨度。

`invoke_agent claude` 根跨度包含每个用户请求，并具有嵌套的 `chat`、`execute_tool` 和 `execute_hook` 跨度。当工具是 `Agent`（Claude 子代理调用）时，子 `chat` 和 `execute_tool` 跨度嵌套在下面，提供完整的子代理可见性。

### 按 agent 类型过滤

在跟踪查看器中，按 `service.name` 进行过滤以查看来自特定 agent 运行时的跟踪：

| `service.name` |来源|
|---|---|
| `copilot-chat` |前景 agent、CLI 包装器和 Claude agent 跨度（扩展发出）|
| `github-copilot` |{% data variables.copilot.copilot_sdk_short %} 本机跨度和 CLI 终端会话|
| `claude-code` |Claude Code子进程SDK遥测（当转发`CLAUDE_CODE_ENABLE_TELEMETRY`时）|

在`copilot-chat`服务中，通过`gen_ai.agent.name`区分agent类型：

| `gen_ai.agent.name` |Agent型|
|---|---|
| `{% data variables.copilot.copilot_chat %}` |前台 agent（agent 模式）|
| `copilotcli` |Copilot 包装跨度|
| `claude` | Claude agent |

## 与可观察性后端一起使用

Copilot Chat 的 OTel 输出可与任何支持 OTLP 协议的后端配合使用。将 `setting(github.copilot.chat.otel.otlpEndpoint)` 设置或 `OTEL_EXPORTER_OTLP_ENDPOINT` 环境变量指向后端的 OTLP 摄取 URL，并配置导出器类型以匹配后端的协议（`otlp-http` 或 `otlp-grpc`）。

### 渴望仪表板

[Aspire Dashboard](<https://aspire.dev/dashboard/standalone/>) 是本地开发最简单的选择。它是一个具有内置 OTLP 端点和跟踪查看器的单个应用程序，并且不需要云帐户。

您可以使用 [Aspire CLI](<https://aspire.dev/get-started/install-cli/>) 启动仪表板：

```bash
aspire dashboard run
```

或者从其 Docker 容器映像运行相同的独立仪表板：

```bash
docker run --rm -d -p 18888:18888 -p 4318:18890 --name aspire-dashboard \
  mcr.microsoft.com/dotnet/aspire-dashboard:latest
```

{% data variables.product.prodname_vscode_shortname %}配置：

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.captureContent": true
}
```

打开 `http://localhost:18888` 并转到 **Traces** 查看您的 agent 交互跨度。

![显示 Aspire 仪表板中 agent 交互轨迹的屏幕截图，其中包含 invoke_agent、聊天和 execute_tool 的跨度。](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/monitoring-agents/trace-aspire-dashboard.png>)

### 耶格

[Jaeger](<https://www.jaegertracing.io/>)是一个开源分布式跟踪平台，直接接受OTLP。

```bash
docker run -d --name jaeger -p 16686:16686 -p 4318:4318 jaegertracing/jaeger:latest
```

{% data variables.product.prodname_vscode_shortname %}配置：

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:4318"
}
```

打开`http://localhost:16686`，选择服务`copilot-chat`，然后选择**查找痕迹**。

### Azure 应用程序洞察

将 [OTel 收集器 ](<https://opentelemetry.io/docs/collector/>) 与 [Azure Monitor 导出器 ](<https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/azuremonitorexporter>) 结合使用，将 Copilot 聊天遥测转发到 Application Insights。将 {% data variables.product.prodname_vscode_shortname %} `setting(github.copilot.chat.otel.otlpEndpoint)` 设置指向收集器的 OTLP 终结点，并将收集器配置为导出到 Application Insights 连接字符串。

有关使用现成仪表板的端到端设置，请参阅 [Monitor AI 编码 agents with Grafana](<https://learn.microsoft.com/azure/managed-grafana/grafana-opentelemetry-app-insights#github-copilot>)。本指南将引导您运行 OTel Collector，将 {% data variables.product.prodname_vscode_shortname %} 指向它，然后导入预构建的 [Azure Managed Grafana](<https://learn.microsoft.com/azure/managed-grafana/>) 仪表板。该仪表板可视化 Copilot 操作、输入和输出令牌、聊天会话、工具调用和每个模型的响应时间以及来自 Application Insights 的 TTFT。

### 朗福斯

[Langfuse](<https://langfuse.com/>) 是一个开源 LLM 可观测平台，具有本机 OTLP 摄取并支持 OTel GenAI 语义约定。

{% data variables.product.prodname_vscode_shortname %}配置：

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:3000/api/public/otel",
  "github.copilot.chat.otel.captureContent": true
}
```

使用 `OTEL_EXPORTER_OTLP_HEADERS` 环境变量设置 auth 标头。有关详细信息，请参阅 [Langfuse OTel 文档 ](<https://langfuse.com/docs/opentelemetry/introduction>)。

### 其他后端

任何 OTLP- 兼容后端都可以工作，包括 [Grafana Tempo](<https://grafana.com/oss/tempo/>)、[Honeycomb](<https://www.honeycomb.io/>) 和 [Datadog](<https://www.datadoghq.com/>)。有关 OTLP 摄取设置，请参阅每个后端的文档。

## 其他出口商示例

默认导出器是`otlp-http`。您可以切换到 `otlp-grpc`、`console` 或 `file` 以适合您的后端或调试工作流程。

OTLP/gRPC:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "otlp-grpc",
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:4317"
}
```

控制台输出（快速调试）：

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "console"
}
```

基于文件的输出（离线或CI）：

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "file",
  "github.copilot.chat.otel.outfile": "/tmp/copilot-otel.jsonl"
}
```

远程收集器的身份验证标头只能通过 `OTEL_EXPORTER_OTLP_HEADERS` 环境变量（例如 `Authorization=Bearer your-token`）进行配置。

## 安全和隐私

OTel 监控默认处于关闭状态，并且不会发出任何数据，除非您明确启用它。您可以控制收集的内容及其去向。

|方面|详情|
|---|---|
|**默认关闭**|除非您明确启用，否则不会发出 OTel 数据。 OTel SDK 禁用时不会加载，从而导致运行时开销为零。|
|**默认无内容**|提示、响应和工具参数需要使用 `captureContent` 选择加入。|
|**默认属性中没有PII**|会话 ID、模型名称和令牌计数无法识别个人身份。|
|**用户配置的端点**|数据只传输到您指定的地方。不存在打电话回家的行为。|

## 相关内容

* [AI设置参考](<https://raw.githubusercontent.com/docs/agents/reference/ai-settings.md>)
* [管理企业环境中的AI设置](<https://raw.githubusercontent.com/docs/enterprise/manage-ai-settings.md>)
* [对 {% data variables.product.prodname_vscode_shortname %}](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/troubleshooting.md>) 中的 AI 进行故障排除
* [使用缓存资源管理器诊断提示缓存](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/cache-explorer.md>)
* [OTel GenAI 语义约定](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>)
* [在LLM内调用：GenAI与OpenTelemetry](<https://opentelemetry.io/blog/2026/genai-observability/>)的可观察性
* [Aspire Dashboard 独立文档](<https://aspire.dev/dashboard/standalone/>)
* [视频：使用 OpenTelemetry 和 Aspire](<https://www.youtube.com/watch?v=JsZ3q8OKsMM>) 跟踪 agent 会话


## 原文对照

<details>
<summary>展开完整原文</summary>

# Monitor agent usage with OpenTelemetry

This article describes how to enable and configure OpenTelemetry monitoring for Copilot Chat agent interactions in {% data variables.product.prodname_vscode_shortname %}.

Copilot Chat can export traces, metrics, and events via [OpenTelemetry](<https://opentelemetry.io/>) (OTel), giving you visibility into agent interactions, LLM calls, tool executions, and token usage. All signal names and attributes follow the [OTel GenAI Semantic Conventions](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>), so the data works with any OTel-compatible backend.

> [!TIP]
> For an interactive view of the cache metrics without setting up an OTel backend, use the [Cache Explorer](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/cache-explorer.md>) to inspect prompt cache hit rates directly in {% data variables.product.prodname_vscode_shortname %}.

## What gets collected

Copilot Chat emits three types of OTel signals: traces, metrics, and events.

### Attribute namespaces

Copilot Chat emits OTel attributes under three namespaces:

| Namespace | Source | When to use |
|---|---|---|
| `gen_ai.*` | [OTel GenAI Semantic Conventions](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>) | Whenever a standard key exists |
| `github.copilot.*` | Canonical Copilot-specific namespace, shared with the [{% data variables.copilot.copilot_cli %} OpenTelemetry conventions](<https://docs.github.com/copilot/reference/copilot-cli-reference/cli-command-reference#opentelemetry-monitoring>) | Preferred for new dashboards, alerts, and queries |
| `copilot_chat.*` | Original {% data variables.product.prodname_vscode_shortname %} extension namespace | Legacy. Several keys are now dual-emitted alongside `github.copilot.*` equivalents |

Legacy `copilot_chat.*` keys continue to emit indefinitely so existing collectors, dashboards, and downstream consumers keep working without changes. There is no sunset date. The tables in this section mark dual-emitted rows as **Legacy** with a pointer to the preferred key.

### Traces

Each agent interaction produces a hierarchical span tree that captures the full execution flow:

```text
invoke_agent copilot                           [~15s]
  ├── chat gpt-4o                              [~3s]  (LLM requests tool calls)
  ├── execute_tool readFile                    [~50ms]
  ├── execute_tool runCommand                  [~2s]
  ├── chat gpt-4o                              [~4s]  (LLM generates final response)
  └── (span ends)
```

When an agent invokes a subagent (for example, through the `runSubagent` tool), the trace context is automatically propagated. The subagent's `invoke_agent` span appears as a child of the parent agent's `execute_tool` span, producing a connected trace tree across async boundaries.

#### `invoke_agent` span

Wraps the entire agent orchestration, including all LLM calls and tool executions.

| Attribute | Description |
|---|---|
| `gen_ai.operation.name` | Always `invoke_agent` |
| `gen_ai.provider.name` | Provider (for example, `github`) |
| `gen_ai.agent.name` | Agent name (for example, `copilot`, `copilotcli`, `claude`) |
| `gen_ai.conversation.id` | Conversation session ID |
| `gen_ai.request.model` | Requested model |
| `gen_ai.response.model` | Resolved model |
| `gen_ai.usage.input_tokens` | Total input tokens across the session |
| `gen_ai.usage.output_tokens` | Total output tokens across the session |
| `gen_ai.usage.cache_read.input_tokens` | Cache-read input tokens, when available |
| `gen_ai.usage.cache_creation.input_tokens` | Cache-creation input tokens, when available |
| `github.copilot.agent.type` | `builtin`, `custom`, or `plugin` |
| `github.copilot.git.repository` | Repository remote URL, when in a Git repo |
| `github.copilot.git.branch` | Active branch, when in a Git repo |
| `github.copilot.git.commit_sha` | Current commit, when in a Git repo |
| `github.copilot.github.org` | GitHub org owner, for GitHub remotes only |
| `copilot_chat.repo.remote_url` | Legacy. Prefer `github.copilot.git.repository` |
| `copilot_chat.repo.head_branch_name` | Legacy. Prefer `github.copilot.git.branch` |
| `copilot_chat.repo.head_commit_hash` | Legacy. Prefer `github.copilot.git.commit_sha` |
| `copilot_chat.turn_count` | LLM round-trips in this session |
| `error.type` | Error class, on failure |
| `gen_ai.input.messages` | Full prompt messages (content capture only) |
| `gen_ai.output.messages` | Full response messages (content capture only) |
| `gen_ai.tool.definitions` | Tool schemas (content capture only) |

#### `chat` span

One span per LLM API call.

| Attribute | Description |
|---|---|
| `gen_ai.operation.name` | Always `chat` |
| `gen_ai.provider.name` | Provider name |
| `gen_ai.request.model` | Requested model |
| `gen_ai.response.model` | Resolved model |
| `gen_ai.response.finish_reasons` | Stop reasons (for example, `["stop"]`) |
| `gen_ai.request.max_tokens` | Max output tokens |
| `gen_ai.request.temperature` | Temperature, when set |
| `gen_ai.request.top_p` | Top-p, when set |
| `gen_ai.usage.input_tokens` | Input tokens for this call |
| `gen_ai.usage.output_tokens` | Output tokens for this call |
| `gen_ai.usage.cache_read.input_tokens` | Cache-read input tokens, when available |
| `gen_ai.usage.cache_creation.input_tokens` | Cache-creation input tokens, when available |
| `gen_ai.usage.reasoning.output_tokens` | Reasoning tokens, when available |
| `gen_ai.usage.reasoning_tokens` | Legacy. Prefer `gen_ai.usage.reasoning.output_tokens` |
| `copilot_chat.time_to_first_token` | Time to first SSE token (milliseconds) |
| `server.address` | API hostname |
| `error.type` | Error class, on failure |

#### `execute_tool` span

One span per tool invocation.

| Attribute | Description |
|---|---|
| `gen_ai.operation.name` | Always `execute_tool` |
| `gen_ai.tool.name` | Tool name (for example, `readFile`) |
| `gen_ai.tool.type` | `function` or `extension` (MCP tools) |
| `gen_ai.tool.call.id` | Tool call identifier |
| `github.copilot.tool.parameters.edit_type` | For edit tools: `create`, `update`, `str_replace`, or `insert` |
| `github.copilot.tool.parameters.skill_name` | For skill invocations |
| `github.copilot.tool.parameters.mcp_server_name_hash` | For MCP tools: SHA-256 of the server name |
| `github.copilot.tool.parameters.mcp_tool_name` | For MCP tools: invoked tool name |
| `github.copilot.tool.parameters.command` | For shell tools (content capture only, truncated) |
| `github.copilot.tool.parameters.file_path` | For file tools (content capture only) |
| `github.copilot.tool.parameters.mcp_server_name` | For MCP tools (content capture only) |
| `error.type` | Error class, on failure |
| `gen_ai.tool.call.arguments` | Tool input arguments (content capture only) |
| `gen_ai.tool.call.result` | Tool output (content capture only) |

#### `execute_hook` span

One span per hook execution (for example, `PreToolUse`, `Stop`).

| Attribute | Description |
|---|---|
| `gen_ai.operation.name` | Always `execute_hook` |
| `github.copilot.hook.decision` | `pass`, `block`, or `non_blocking_error` |
| `github.copilot.hook.duration` | Hook execution duration (seconds) |
| `github.copilot.hook.tool_names` | Tools the hook is scoped to, as a JSON array |
| `copilot_chat.hook_type` | Hook event (for example, `PreToolUse`) |
| `copilot_chat.hook_result_kind` | `success`, `error`, or `non_blocking_error` |
| `copilot_chat.hook_input` | Hook input payload (truncated) |
| `copilot_chat.hook_output` | Hook stdout, on success (truncated) |
| `error.type` | Error class, on failure |

### Metrics

GenAI semantic-convention metrics:

| Metric | Type | Description |
|---|---|---|
| `gen_ai.client.operation.duration` | Histogram | LLM API call duration (seconds) |
| `gen_ai.client.token.usage` | Histogram | Token counts (input and output) |

Extension-specific metrics:

| Metric | Type | Description |
|---|---|---|
| `copilot_chat.tool.call.count` | Counter | Tool invocations by name and success |
| `copilot_chat.tool.call.duration` | Histogram | Tool execution latency (milliseconds) |
| `copilot_chat.agent.invocation.duration` | Histogram | Agent end-to-end duration (seconds) |
| `copilot_chat.agent.turn.count` | Histogram | LLM round-trips per agent invocation |
| `copilot_chat.session.count` | Counter | Chat sessions started |
| `copilot_chat.time_to_first_token` | Histogram | Time to first SSE token (seconds) |

Agent activity and outcome metrics track agentic code changes across all surfaces (inline chat, local agents, Copilot agents, Claude agents, and {% data variables.copilot.copilot_cloud_agent %}s):

| Metric | Type | Description |
|---|---|---|
| `copilot_chat.edit.acceptance.count` | Counter | Edit accept and reject decisions (inline chat, chat editing, hunk-level) |
| `copilot_chat.chat_edit.outcome.count` | Counter | File-level chat editing session outcomes (accepted, rejected, saved) |
| `copilot_chat.lines_of_code.count` | Counter | Lines of code added or removed by accepted agent edits |
| `copilot_chat.edit.survival.four_gram` | Histogram | 4-gram text similarity survival score (0-1) |
| `copilot_chat.edit.survival.no_revert` | Histogram | No-revert survival score (0-1) |
| `copilot_chat.user.action.count` | Counter | User engagement actions: copy, insert, apply, followup |
| `copilot_chat.user.feedback.count` | Counter | Thumbs up and thumbs down votes on chat responses |
| `copilot_chat.agent.edit_response.count` | Counter | Agent edit responses by success or error |
| `copilot_chat.agent.summarization.count` | Counter | Context summarization outcomes (applied, failed) |
| `copilot_chat.pull_request.count` | Counter | Pull requests created via the Copilot agent |
| `copilot_chat.cloud.session.count` | Counter | Cloud and remote agent sessions by partner agent |
| `copilot_chat.cloud.pr_ready.count` | Counter | Remote agent job PR-ready notifications |

Metrics include attributes for filtering, such as `gen_ai.request.model`, `gen_ai.provider.name`, `gen_ai.tool.name`, `copilot_chat.edit.source`, and `error.type`.

### Events

| Event | Description |
|---|---|
| `gen_ai.client.inference.operation.details` | Full LLM call metadata with model, tokens, and finish reason |
| `copilot_chat.session.start` | Emitted when a new chat session begins |
| `copilot_chat.tool.call` | Per-tool invocation with timing and error details |
| `copilot_chat.agent.turn` | Per-turn LLM round-trip with token counts |
| `copilot_chat.edit.feedback` | User accepted or rejected a file-level agent edit |
| `copilot_chat.edit.hunk.action` | User accepted or rejected an individual hunk |
| `copilot_chat.inline.done` | Inline chat edit was accepted or rejected |
| `copilot_chat.edit.survival` | Periodic measurements of how much AI-generated code survives after acceptance |
| `copilot_chat.user.feedback` | User voted on a chat response (thumbs up or down) |
| `copilot_chat.cloud.session.invoke` | A cloud or remote agent session was started |

### Resource attributes

All signals carry these resource attributes:

| Attribute | Value |
|---|---|
| `service.name` | `copilot-chat` (configurable with `OTEL_SERVICE_NAME`) |
| `service.version` | Extension version |
| `session.id` | Unique per {% data variables.product.prodname_vscode_shortname %} window |

Add custom resource attributes with `OTEL_RESOURCE_ATTRIBUTES` to filter by team, department, or other organizational boundaries:

```bash
export OTEL_RESOURCE_ATTRIBUTES="team.id=platform,department=engineering"
```

### Identity capture

For Local harness sessions, identity capture can attribute agent activity to an individual developer and machine. When enabled, telemetry includes:

* `user.name` on agent invocation spans, including subagent and inline chat spans.
* `process.user.name` and `host.name` as resource attributes.

Enable identity capture with `setting(github.copilot.chat.otel.captureIdentity)` or `COPILOT_OTEL_CAPTURE_IDENTITY=true`. This control is independent of [content capture](<#content-capture>).

> [!CAUTION]
> Identity attributes can identify a developer and their machine. Enable identity capture only when your organization has an appropriate purpose, access controls, and retention policy for this data.

> [!NOTE]
> Identity capture currently applies only to the Local harness. Agent Host telemetry doesn't include these identity attributes.

### Content capture

By default, no prompt content, responses, or tool arguments are captured. Only metadata like model names, token counts, and durations are included.

To capture full content, enable the `setting(github.copilot.chat.otel.captureContent)` setting or set `COPILOT_OTEL_CAPTURE_CONTENT=true`. This populates span attributes with full prompt messages, response messages, system prompts, tool schemas, tool arguments, and tool results.

> [!CAUTION]
> Content capture can include sensitive information such as code, file contents, and user prompts. Only enable this in trusted environments.

## Enable OTel monitoring

OTel activates when any of the following conditions is true:

* `setting(github.copilot.chat.otel.enabled)` is `true`
* `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` is `true`
* `COPILOT_OTEL_ENABLED=true`
* `OTEL_EXPORTER_OTLP_ENDPOINT` is set

### {% data variables.product.prodname_vscode_shortname %} settings

Open **Settings** (`kb(workbench.action.openSettings)`) and search for `copilot otel`:

| Setting | Type | Default | Description |
|---|---|---|---|
| `setting(github.copilot.chat.otel.enabled)` | boolean | `false` | Enable OTel emission |
| `setting(github.copilot.chat.otel.exporterType)` | string | `"otlp-http"` | `otlp-http`, `otlp-grpc`, `console`, or `file` |
| `setting(github.copilot.chat.otel.otlpEndpoint)` | string | `"http://localhost:4318"` | OTLP collector endpoint |
| `setting(github.copilot.chat.otel.captureContent)` | boolean | `false` | Capture full prompt and response content |
| `setting(github.copilot.chat.otel.captureIdentity)` | boolean | `false` | Capture the authenticated account name, operating system username, and machine hostname for Local harness sessions |
| `setting(github.copilot.chat.otel.maxAttributeSizeChars)` | integer | `0` | Maximum characters per content attribute (prompts, tool args, tool results). `0` disables truncation. Set a positive value to match your backend's per-attribute size limit. |
| `setting(github.copilot.chat.otel.outfile)` | string | `""` | File path for JSON-lines output |
| `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` | boolean | `false` | Persist OTel spans to a local SQLite database for the **Chat: Export Agent Traces DB** command. Implicitly enables OTel. |

### Environment variables

Environment variables always take precedence over {% data variables.product.prodname_vscode_shortname %} settings. When your organization mandates OTel configuration through [Copilot managed settings](<#manage-otel-configuration-for-your-organization>), those managed values override user settings. In the Copilot Chat extension, OTel environment variables can still override managed values.

| Variable | Default | Description |
|---|---|---|
| `COPILOT_OTEL_ENABLED` | `false` | Enable OTel. Also enabled when `OTEL_EXPORTER_OTLP_ENDPOINT` is set. |
| `COPILOT_OTEL_ENDPOINT` | | OTLP endpoint URL (takes precedence over `OTEL_EXPORTER_OTLP_ENDPOINT`) |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | | Standard OTel OTLP endpoint URL |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | `http/protobuf` | OTLP protocol. Only `grpc` changes behavior. |
| `COPILOT_OTEL_PROTOCOL` | | Override OTLP protocol (`grpc` or `http`). Takes precedence over `OTEL_EXPORTER_OTLP_PROTOCOL`. |
| `OTEL_SERVICE_NAME` | `copilot-chat` | Service name in resource attributes |
| `OTEL_RESOURCE_ATTRIBUTES` | | Extra resource attributes (`key1=val1,key2=val2`) |
| `COPILOT_OTEL_CAPTURE_CONTENT` | `false` | Capture full prompt and response content |
| `COPILOT_OTEL_CAPTURE_IDENTITY` | `false` | Capture developer and machine identity for Local harness sessions |
| `COPILOT_OTEL_MAX_ATTRIBUTE_SIZE_CHARS` | `0` | Override the max character size for content attributes. `0` disables truncation. Takes precedence over the `maxAttributeSizeChars` setting. |
| `COPILOT_OTEL_LOG_LEVEL` | `info` | Minimum log level: `trace`, `debug`, `info`, `warn`, or `error`. |
| `COPILOT_OTEL_FILE_EXPORTER_PATH` | | Write all signals to this file as JSON lines. |
| `COPILOT_OTEL_HTTP_INSTRUMENTATION` | `false` | Enable HTTP-level OTel instrumentation. |
| `OTEL_EXPORTER_OTLP_HEADERS` | | Auth headers (for example, `Authorization=Bearer token`) |

### Commands

When `setting(github.copilot.chat.otel.dbSpanExporter.enabled)` is `true`, Copilot Chat persists OTel spans to a local SQLite database. This is useful for offline inspection or sharing trace data without running an OTLP backend.

| Command | Description |
|---|---|
| **Chat: Export Agent Traces DB** (`github.copilot.chat.otel.exportAgentTracesDB`) | Export the local SQLite span database to a `.db` file. Only available when the `dbSpanExporter.enabled` setting is `true`. |

## Manage OTel configuration for your organization

Enterprises can mandate OTel export configuration centrally through {% data variables.product.prodname_copilot_short %} enterprise-managed settings, so that telemetry flows to an approved collector without each developer setting `OTEL_*` environment variables. Managed telemetry configuration applies to both the Copilot Chat extension and the agent host process.

For delivery methods, configuration fields, client support, and precedence, see [`telemetry` in the enterprise-managed settings reference](<https://docs.github.com/en/copilot/reference/enterprise-administrators/enterprise-managed-settings#telemetry>). For the behavior specific to {% data variables.product.prodname_vscode_shortname %}, see [Apply managed telemetry in {% data variables.product.prodname_vscode_shortname %}](<https://raw.githubusercontent.com/docs/enterprise/manage-ai-settings.md#apply-managed-telemetry-in-vs-code>).

For chat sessions that use the Local harness, {% data variables.product.prodname_vscode_shortname %} automatically tries once per editor session to restart the extension hosts when a managed OTel configuration that enables export arrives after Copilot Chat starts. The restart can interrupt other extensions. If the automatic restart can't complete, or for later policy changes, select **Reload Window** when prompted.

> [!NOTE]
> When a managed telemetry value is configured, it overrides user settings. Managed identity capture also overrides `COPILOT_OTEL_CAPTURE_IDENTITY`, and managed resource attributes override `OTEL_RESOURCE_ATTRIBUTES`. Remove other conflicting OTel environment variables from managed devices to ensure that the enterprise configuration takes effect in the Copilot Chat extension.

## Trace structure for background and Claude agents

When OTel is enabled, all agent types are automatically instrumented. The same settings that enable foreground agent traces also enable Copilot and Claude agent traces.

### Copilot

The {% data variables.copilot.copilot_sdk_short %} runs in the same {% data variables.product.prodname_vscode_shortname %} process as the chat extension and produces a rich trace hierarchy that includes subagents, permissions, hooks, and tool calls. The extension wrapper span (`invoke_agent copilotcli`, service `copilot-chat`) parents the SDK's native spans (service `github-copilot`). Both appear in the same trace in your collector.

Copilot sessions also show the full SDK hierarchy in the **Agent Debug Log** panel in {% data variables.product.prodname_vscode_shortname %}, identical to what appears in your trace viewer. The debug panel works even when OTel export is disabled, because the SDK's internal tracing is always active for the panel.

When OTel export is disabled, the debug panel automatically captures full prompt and response content. When OTel export is enabled, the `setting(github.copilot.chat.otel.captureContent)` setting controls content capture for both the debug panel and OTLP export.

### {% data variables.copilot.copilot_cli_short %} (terminal session)

When you run a {% data variables.copilot.copilot_cli_short %} session in the terminal (**New {% data variables.copilot.copilot_cli_short %} Session**), it runs in a separate process. When OTel is enabled, the extension forwards `COPILOT_OTEL_ENABLED` and `OTEL_EXPORTER_OTLP_ENDPOINT` to the terminal process. Terminal traces appear as independent root traces under service `github-copilot` and are not linked to extension traces.

The CLI runtime only supports `otlp-http`. When `otlp-grpc` is configured, the terminal CLI still uses HTTP. Backends that serve both protocols on the same port (such as the Aspire Dashboard) work transparently.

### Claude agent

When OTel is enabled, Claude agent sessions produce extension-level spans under service `copilot-chat` that follow the GenAI semantic conventions. The extension creates spans by intercepting Claude SDK messages and proxying LLM calls through a local HTTP server.

The `invoke_agent claude` root span wraps each user request, with nested `chat`, `execute_tool`, and `execute_hook` spans. When the tool is `Agent` (a Claude subagent invocation), child `chat` and `execute_tool` spans are nested underneath, giving full subagent visibility.

### Filter by agent type

In your trace viewer, filter by `service.name` to see traces from specific agent runtimes:

| `service.name` | Source |
|---|---|
| `copilot-chat` | Foreground agent, CLI wrapper, and Claude agent spans (extension-emitted) |
| `github-copilot` | {% data variables.copilot.copilot_sdk_short %} native spans and CLI terminal sessions |
| `claude-code` | Claude Code subprocess SDK telemetry (when `CLAUDE_CODE_ENABLE_TELEMETRY` is forwarded) |

Within the `copilot-chat` service, distinguish agent types by `gen_ai.agent.name`:

| `gen_ai.agent.name` | Agent type |
|---|---|
| `{% data variables.copilot.copilot_chat %}` | Foreground agent (agent mode) |
| `copilotcli` | Copilot wrapper span |
| `claude` | Claude agent |

## Use with observability backends

Copilot Chat's OTel output works with any backend that supports the OTLP protocol. Point the `setting(github.copilot.chat.otel.otlpEndpoint)` setting or `OTEL_EXPORTER_OTLP_ENDPOINT` environment variable at the backend's OTLP ingestion URL, and configure the exporter type to match the backend's protocol (`otlp-http` or `otlp-grpc`).

### Aspire Dashboard

The [Aspire Dashboard](<https://aspire.dev/dashboard/standalone/>) is the simplest option for local development. It is a single app with a built-in OTLP endpoint and trace viewer, and requires no cloud account.

You can start the dashboard using the [Aspire CLI](<https://aspire.dev/get-started/install-cli/>):

```bash
aspire dashboard run
```

Or run the same standalone dashboard from its Docker container image:

```bash
docker run --rm -d -p 18888:18888 -p 4318:18890 --name aspire-dashboard \
  mcr.microsoft.com/dotnet/aspire-dashboard:latest
```

{% data variables.product.prodname_vscode_shortname %} configuration:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.captureContent": true
}
```

Open `http://localhost:18888` and go to **Traces** to view your agent interaction spans.

![Screenshot showing agent interaction traces in the Aspire Dashboard with spans for invoke_agent, chat, and execute_tool.](<https://raw.githubusercontent.com/microsoft/vscode-docs//docs/agents/images/monitoring-agents/trace-aspire-dashboard.png>)

### Jaeger

[Jaeger](<https://www.jaegertracing.io/>) is an open-source distributed tracing platform that accepts OTLP directly.

```bash
docker run -d --name jaeger -p 16686:16686 -p 4318:4318 jaegertracing/jaeger:latest
```

{% data variables.product.prodname_vscode_shortname %} configuration:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:4318"
}
```

Open `http://localhost:16686`, select service `copilot-chat`, and select **Find Traces**.

### Azure Application Insights

Use an [OTel Collector](<https://opentelemetry.io/docs/collector/>) with the [Azure Monitor exporter](<https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/azuremonitorexporter>) to forward Copilot Chat telemetry to Application Insights. Point the {% data variables.product.prodname_vscode_shortname %} `setting(github.copilot.chat.otel.otlpEndpoint)` setting at the collector's OTLP endpoint, and configure the collector to export to your Application Insights connection string.

For an end-to-end setup with a ready-made dashboard, see [Monitor AI coding agents with Grafana](<https://learn.microsoft.com/azure/managed-grafana/grafana-opentelemetry-app-insights#github-copilot>). The guide walks through running the OTel Collector, pointing {% data variables.product.prodname_vscode_shortname %} at it, and importing a prebuilt [Azure Managed Grafana](<https://learn.microsoft.com/azure/managed-grafana/>) dashboard. The dashboard visualizes Copilot operations, input and output tokens, chat sessions, tool calls, and per-model response time and TTFT from Application Insights.

### Langfuse

[Langfuse](<https://langfuse.com/>) is an open-source LLM observability platform with native OTLP ingestion and support for OTel GenAI Semantic Conventions.

{% data variables.product.prodname_vscode_shortname %} configuration:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:3000/api/public/otel",
  "github.copilot.chat.otel.captureContent": true
}
```

Set the auth header with the `OTEL_EXPORTER_OTLP_HEADERS` environment variable. See the [Langfuse OTel docs](<https://langfuse.com/docs/opentelemetry/introduction>) for details.

### Other backends

Any OTLP-compatible backend works, including [Grafana Tempo](<https://grafana.com/oss/tempo/>), [Honeycomb](<https://www.honeycomb.io/>), and [Datadog](<https://www.datadoghq.com/>). Refer to each backend's documentation for OTLP ingestion setup.

## Other exporter examples

The default exporter is `otlp-http`. You can switch to `otlp-grpc`, `console`, or `file` to fit your backend or debugging workflow.

OTLP/gRPC:

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "otlp-grpc",
  "github.copilot.chat.otel.otlpEndpoint": "http://localhost:4317"
}
```

Console output (quick debugging):

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "console"
}
```

File-based output (offline or CI):

```json
{
  "github.copilot.chat.otel.enabled": true,
  "github.copilot.chat.otel.exporterType": "file",
  "github.copilot.chat.otel.outfile": "/tmp/copilot-otel.jsonl"
}
```

Authentication headers for remote collectors are only configurable through the `OTEL_EXPORTER_OTLP_HEADERS` environment variable (for example, `Authorization=Bearer your-token`).

## Security and privacy

OTel monitoring is off by default and emits no data until you explicitly enable it. You control what is collected and where it goes.

| Aspect | Detail |
|---|---|
| **Off by default** | No OTel data is emitted unless you explicitly enable it. The OTel SDK is not loaded when disabled, resulting in zero runtime overhead. |
| **No content by default** | Prompts, responses, and tool arguments require opt-in with `captureContent`. |
| **No PII in default attributes** | Session IDs, model names, and token counts are not personally identifiable. |
| **User-configured endpoints** | Data goes only where you point it. There is no phone-home behavior. |

## Related content

* [AI settings reference](<https://raw.githubusercontent.com/docs/agents/reference/ai-settings.md>)
* [Manage AI settings in enterprise environments](<https://raw.githubusercontent.com/docs/enterprise/manage-ai-settings.md>)
* [Troubleshoot AI in {% data variables.product.prodname_vscode_shortname %}](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/troubleshooting.md>)
* [Diagnose prompt caching with the Cache Explorer](<https://raw.githubusercontent.com/docs/agents/agent-troubleshooting/cache-explorer.md>)
* [OTel GenAI Semantic Conventions](<https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/>)
* [Inside the LLM Call: GenAI Observability with OpenTelemetry](<https://opentelemetry.io/blog/2026/genai-observability/>)
* [Aspire Dashboard standalone docs](<https://aspire.dev/dashboard/standalone/>)
* [Video: Tracing agent sessions with OpenTelemetry and Aspire](<https://www.youtube.com/watch?v=JsZ3q8OKsMM>)


</details>
