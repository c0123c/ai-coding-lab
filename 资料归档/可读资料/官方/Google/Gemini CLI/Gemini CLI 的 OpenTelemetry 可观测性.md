# Gemini CLI 的 OpenTelemetry 可观测性

说明日志、指标和追踪的配置、本地或云端导出，以及工具、API、会话和模型路由等事件的记录方式。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-10-07](<https://github.com/google-gemini/gemini-cli/commit/b8e550e918e1f3f140e497b1a4ee945f88e672f1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/telemetry.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/cli/telemetry.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OpenTelemetry 的可观察性

可观察性是将实验性 AI 转变为可靠软件的关键。
Gemini CLI 为 OpenTelemetry 提供内置支持，将每个 agent
 交互转换为丰富的日志、指标和跟踪流。这种三支柱 
 方法为您提供了解 agent
 行为、优化性能并确保整个 
 工作流程的可靠性所需的高保真可见性。

无论您是在本地调试复杂的工具交互，还是在云中监控
企业范围的使用情况，Gemini CLI的可观测性系统都提供
从“黑匣子”AI转变为可预测的
高性能系统所需的可操作智能。

## OpenTelemetry 集成

Gemini CLI 与供应商中立的 **[OpenTelemetry]** 和 
 行业标准可观测性框架集成。

可观测性系统提供：

- 通用兼容性：导出到任何 OpenTelemetry 后端（Google 云、
Jaeger、Prometheus、Datadog 等）。
- 标准化数据：在整个组织中使用一致的格式和收集方法
工具链。
- 面向未来的集成：与现有和未来的可观察性相连接
基础设施。
- 无供应商锁定：在后端之间切换而无需更改您的
仪器仪表。

[OpenTelemetry]: <https://opentelemetry.io/>

## 配置

您可以通过 `.gemini/settings.json` 文件控制遥测行为。
环境变量可以覆盖这些设置。

|设置|环境变量|描述|价值观|默认|
| -------------- | --------------------------------- | --------------------------------------------------- | ----------------- | ----------------------- |
| `enabled`      | `GEMINI_TELEMETRY_ENABLED`        |启用或禁用遥测| `true`/`false`    | `false`                 |
| `traces`       | `GEMINI_TELEMETRY_TRACES_ENABLED` |启用详细属性跟踪| `true`/`false`    | `false`                 |
| `target`       | `GEMINI_TELEMETRY_TARGET`         |遥测数据发送到哪里| `"gcp"`/`"local"` | `"local"`               |
| `otlpEndpoint` | `GEMINI_TELEMETRY_OTLP_ENDPOINT`  |OTLP收集器端点|URL 字符串| `http://localhost:4317` |
| `otlpProtocol` | `GEMINI_TELEMETRY_OTLP_PROTOCOL`  |OTLP传输协议| `"grpc"`/`"http"` | `"grpc"`                |
| `otlpHeaders`  | `GEMINI_TELEMETRY_OTLP_HEADERS`   |OTLP 导出器请求的自定义标头| JSON/`key=value`  | -                       |
| `outfile`      | `GEMINI_TELEMETRY_OUTFILE`        |将遥测数据保存到文件（覆盖 `otlpEndpoint`）|文件路径| -                       |
| `logPrompts`   | `GEMINI_TELEMETRY_LOG_PROMPTS`    |在遥测日志中包含提示| `true`/`false`    | `true`                  |
| `useCollector` | `GEMINI_TELEMETRY_USE_COLLECTOR`  |使用外部OTLP收集器（高级）| `true`/`false`    | `false`                 |
| `useCliAuth`   | `GEMINI_TELEMETRY_USE_CLI_AUTH`   |使用 CLI 凭据进行遥测（仅限 GCP 目标）| `true`/`false`    | `false`                 |
| -              | `GEMINI_CLI_SURFACE`              |用于流量报告的可选自定义标签|字符串| -                       |

**关于布尔环境变量的注意事项：** 对于布尔设置，例如 `enabled`、
将环境变量设置为 `true` 或 `1` 启用该功能。

详细配置信息请参见
[配置指南](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>)。

## Google 云遥测

您可以将遥测数据直接导出到Google云追踪、云监控、
和云日志。

### 先决条件

在启用 Google 云遥测之前，您必须完成几个设置步骤。

1.  设置您的Google云项目ID：

    - 要将遥测数据发送到单独的项目：

      **macOS/Linux**

      ```bash
      export OTLP_GOOGLE_CLOUD_PROJECT="your-telemetry-project-id"
      ```

**Windows (PowerShell)**

      ```powershell
      $env:OTLP_GOOGLE_CLOUD_PROJECT="your-telemetry-project-id"
      ```

    - 要将遥测数据发送到与推理相同的项目：

      **macOS/Linux**

      ```bash
      export GOOGLE_CLOUD_PROJECT="your-project-id"
      ```

**Windows (PowerShell)**

      ```powershell
      $env:GOOGLE_CLOUD_PROJECT="your-project-id"
      ```

2.  使用以下方法之一通过 Google 云进行身份验证：

    - **方法 A：应用程序默认凭据 (ADC)**：使用此方法
服务帐户或标准 `gcloud` 身份验证。

      - 对于用户帐户：
        ```bash
        gcloud auth application-default login
        ```
      - 对于服务帐户：

        **macOS/Linux**

        ```bash
        export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account.json"
        ```

**Windows (PowerShell)**

        ```powershell
        $env:GOOGLE_APPLICATION_CREDENTIALS="C:\path\to\your\service-account.json"
        ```

    * **方法B：CLI Auth**（仅直接导出）：本地最简单的方法
用户。 Gemini CLI 使用您用于登录的相同 OAuth 凭据。要
启用此功能，请在`.gemini/settings.json`中设置`useCliAuth: true`：

      ```json
      {
        "telemetry": {
          "enabled": true,
          "target": "gcp",
          "useCliAuth": true
        }
      }
      ```

<!-- prettier-ignore -->
> [!NOTE]
> 此设置需要**直接导出**（进程内导出器）
> ，并且当`useCollector`为`true`时无法使用。如果两者都启用，
> 遥测将被禁用。

3.  确保您的帐户或服务帐户具有以下 IAM 角色：

    - 云踪Agent
    - 监控指标编写器
    - 日志编写器

4.  启用所需的 Google 云 API：
    ```bash
    gcloud services enable \
      cloudtrace.googleapis.com \
      monitoring.googleapis.com \
      logging.googleapis.com \
      --project="$OTLP_GOOGLE_CLOUD_PROJECT"
    ```

### 直接出口

我们建议使用直接导出将遥测数据直接发送到Google云
服务。

1.  在 `.gemini/settings.json` 中启用遥测：
    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "gcp"
      }
    }
    ```
2.  运行Gemini CLI并发送提示。
3.  在 Google Cloud Console 中查看日志、指标和跟踪记录。参见
[查看Google云遥测](<#查看google云遥测>)了解详情。

### 查看Google云遥测

启用遥测并运行Gemini CLI后，您可以在
Google云控制台中查看您的数据。

- **日志：** [日志资源管理器](<https://console.cloud.google.com/logs/>)
- **指标：**
[指标资源管理器](<https://console.cloud.google.com/monitoring/metrics-explorer>)
- **痕迹：** [痕迹浏览器](<https://console.cloud.google.com/traces/list>)

有关如何使用这些工具的详细信息，请参阅以下官方
Google云文档：

- [使用日志资源管理器查看和分析日志](<https://cloud.google.com/logging/docs/view/logs-explorer-interface>)
- [使用 Metrics Explorer 创建图表](<https://cloud.google.com/monitoring/charts/metrics-explorer>)
- [寻找和探索痕迹](<https://cloud.google.com/trace/docs/finding-traces>)

#### 监控仪表板

Gemini CLI 提供预配置的 
[Google 云监控 ](<https://cloud.google.com/monitoring>) 仪表板，以 
 可视化您的遥测数据。

在 **Google 云监控仪表板模板 ** 下找到此仪表板，如下所示：
“**Gemini CLI 监控**”。

![Gemini CLI 监控仪表板概述](<https://github.com/docs/assets/monitoring-dashboard-overview.png>)

![Gemini CLI 监控仪表板指标](<https://github.com/docs/assets/monitoring-dashboard-metrics.png>)

![Gemini CLI 监控仪表板日志](<https://github.com/docs/assets/monitoring-dashboard-logs.png>)

要了解更多信息，请参阅
[即时见解：Gemini CLI的预配置监控仪表板](<https://cloud.google.com/blog/topics/developers-practitioners/instant-insights-gemini-clis-new-pre-configured-monitoring-dashboards/>)。

## 本地遥测

您可以在本地捕获遥测数据以进行开发和调试。我们
建议使用基于文件的输出进行本地开发。

1.  在 `.gemini/settings.json` 中启用遥测：
    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "local",
        "outfile": ".gemini/telemetry.log"
      }
    }
    ```
2.  运行Gemini CLI并发送提示。
3.  查看 `.gemini/telemetry.log` 中的日志和指标。

有关高级本地遥测设置（例如 Jaeger 或 Genkit），请参阅
[本地开发指南](<Gemini CLI 本地开发与链路追踪.md#viewing-traces>)。

## 客户身份识别

Gemini CLI 在其中包含标识符 `User-Agent` 标题可以帮助你
区分并报告 API 来自不同环境的流量（例如
例如，识别来自 Gemini 代码辅助与标准终端）。

### 自动识别

大多数集成环境都会自动识别，无需额外的
配置。该标识符作为 `User-Agent` 的前缀和 
 括号元数据中的“表面”标签包含在内。

|环境|用户-Agent 前缀|表面标签|
| :---------------------------------- | :--------------------------- | :---------- |
|**Gemini 代码辅助（Agent 模式）**| `GeminiCLI-a2a-server`       | `vscode`    |
|**Zed（通过ACP）**| `GeminiCLI-acp-zed`          | `zed`       |
|**XCode（通过ACP）**| `GeminiCLI-acp-xcode`        | `xcode`     |
|**IntelliJ IDEA（通过 ACP）**| `GeminiCLI-acp-intellijidea` | `jetbrains` |
|**标准端子**| `GeminiCLI`                  | `terminal`  |

**示例用户-Agent:**
`GeminiCLI-a2a-server/0.34.0/gemini-pro (linux; x64; vscode)`

### 定制标识

您可以通过
设置`GEMINI_CLI_SURFACE`环境变量为您自己的脚本或自动化提供自定义标识符。这对于
跟踪GCP日志中的特定内部工具或分发渠道非常有用。

**macOS/Linux**

```bash
export GEMINI_CLI_SURFACE="my-custom-tool"
```

**Windows (PowerShell)**

```powershell
$env:GEMINI_CLI_SURFACE="my-custom-tool"
```

设置后，该值出现在 `User-Agent` 括号的末尾：
`GeminiCLI/0.34.0/gemini-pro (linux; x64; my-custom-tool)`

## 日志、指标和跟踪

本节介绍
Gemini CLI生成的日志、指标和跟踪的结构。

Gemini CLI 包括 `session.id`、`installation.id`、`active_approval_mode` 和 
`user.email`（经过身份验证时）作为所有数据的公共属性。

### 日志

日志提供特定事件的带时间戳的记录。 Gemini CLI 记录多个类别的事件 
。

#### 会议

会话日志捕获启动配置和提示提交。

##### `gemini_cli.config`

使用 CLI 配置启动时发出。



<summary>属性</summary>

- `model`（字符串）
- `embedding_model`（字符串）
- `sandbox_enabled`（布尔值）
- `core_tools_enabled`（字符串）
- `approval_mode`（字符串）
- `api_key_enabled`（布尔值）
- `vertex_ai_enabled`（布尔值）
- `log_user_prompts_enabled`（布尔值）
- `file_filtering_respect_git_ignore`（布尔值）
- `debug_mode`（布尔值）
- `mcp_servers`（字符串）
- `mcp_servers_count`（整数）
- `mcp_tools`（字符串）
- `mcp_tools_count`（整数）
- `output_format`（字符串）
- `extensions`（字符串）
- `extension_ids`（字符串）
- `extensions_count`（整数）
- `auth_type`（字符串）
- `worktree_active`（布尔值）
- `github_workflow_name`（字符串，可选）
- `github_repository_hash`（字符串，可选）
- `github_event_name`（字符串，可选）
- `github_pr_number`（字符串，可选）
- `github_issue_number`（字符串，可选）
- `github_custom_tracking_id`（字符串，可选）




##### `gemini_cli.user_prompt`

当您提交提示时发出。



<summary>属性</summary>

- `prompt_length`（整数）
- `prompt_id`（字符串）
- `prompt`（字符串；如果 `telemetry.logPrompts` 为 `false`，则排除）
- `auth_type`（字符串）




#### 审批方式

这些日志跟踪不同审批模式的更改和使用情况。

##### 生命周期

##### `approval_mode_switch`

更改审批模式时记录。



<summary>属性</summary>

- `from_mode`（字符串）
- `to_mode`（字符串）




##### `approval_mode_duration`

记录审批模式下花费的时间。



<summary>属性</summary>

- `mode`（字符串）
- `duration_ms`（整数）




##### 执行

##### `plan_execution`

当您执行计划并从计划模式切换到主动执行时记录。



<summary>属性</summary>

- `approval_mode`（字符串）




#### 工具

工具日志捕获执行、截断和编辑行为。

##### `gemini_cli.tool_call`

为每个工具（函数）调用发出。



<summary>属性</summary>

- `function_name`（字符串）
- `function_args`（字符串）
- `duration_ms`（整数）
- `success`（布尔值）
- `decision`（字符串：“接受”、“拒绝”、“auto_accept”或“修改”）
- `error`（字符串，可选）
- `error_type`（字符串，可选）
- `prompt_id`（字符串）
- `tool_type`（字符串：“native”或“mcp”）
- `mcp_server_name`（字符串，可选）
- `extension_name`（字符串，可选）
- `extension_id`（字符串，可选）
- `content_length`（整数，可选）
- `start_time`（数量，可选）
- `end_time`（数量，可选）
- `metadata`（对象，可选），可能包括：
  - `model_added_lines`（号码）
  - `model_removed_lines`（号码）
  - `user_added_lines`（号码）
  - `user_removed_lines`（号码）
  - `ask_user`（对象）




##### `gemini_cli.tool_output_truncated`

工具输出被截断时记录。



<summary>属性</summary>

- `tool_name`（字符串）
- `original_content_length`（整数）
- `truncated_content_length`（整数）
- `threshold`（整数）
- `lines`（整数）
- `prompt_id`（字符串）




##### `gemini_cli.edit_strategy`

记录所选的编辑策略。



<summary>属性</summary>

- `strategy`（字符串）




##### `gemini_cli.edit_correction`

记录编辑更正的结果。



<summary>属性</summary>

- `correction`（字符串：“成功”或“失败”）




##### `gen_ai.client.inference.operation.details`

提供符合OpenTelemetry约定的详细GenAI操作数据。



<summary>属性</summary>

- `gen_ai.request.model`（字符串）
- `gen_ai.provider.name`（字符串）
- `gen_ai.operation.name`（字符串）
- `gen_ai.input.messages`（json 字符串）
- `gen_ai.output.messages`（json 字符串）
- `gen_ai.response.finish_reasons`（字符串数组）
- `gen_ai.usage.input_tokens`（整数）
- `gen_ai.usage.output_tokens`（整数）
- `gen_ai.request.temperature`（浮动）
- `gen_ai.request.top_p`（浮动）
- `gen_ai.request.top_k`（整数）
- `gen_ai.request.max_tokens`（整数）
- `gen_ai.system_instructions`（json 字符串）
- `server.address`（字符串）
- `server.port`（整数）




#### 文件

文件日志跟踪工具执行的操作。

##### `gemini_cli.file_operation`

每次创建、读取或更新文件时发出。



<summary>属性</summary>

- `tool_name`（字符串）
- `operation`（字符串：“创建”、“读取”或“更新”）
- `lines`（整数，可选）
- `mimetype`（字符串，可选）
- `extension`（字符串，可选）
- `programming_language`（字符串，可选）




#### API

API 记录来自 Gemini API 的捕获请求、响应和错误。

##### `gemini_cli.api_request`

请求已发送至 Gemini API。



<summary>属性</summary>

- `model`（字符串）
- `prompt_id`（字符串）
- `role`（字符串：“用户”、“型号”或“系统”）
- `request_text`（字符串，可选）




##### `gemini_cli.api_response`

从 Gemini API 收到响应。



<summary>属性</summary>

- `model`（字符串）
- `status_code`（整数或字符串）
- `duration_ms`（整数）
- `input_token_count`（整数）
- `output_token_count`（整数）
- `cached_content_token_count`（整数）
- `thoughts_token_count`（整数）
- `tool_token_count`（整数）
- `total_token_count`（整数）
- `prompt_id`（字符串）
- `auth_type`（字符串）
- `finish_reasons`（字符串数组）
- `response_text`（字符串，可选）




##### `gemini_cli.api_error`

当 API 请求失败时记录。



<summary>属性</summary>

- `error.message`（字符串）
- `model_name`（字符串）
- `duration`（整数）
- `prompt_id`（字符串）
- `auth_type`（字符串）
- `error_type`（字符串，可选）
- `status_code`（整数或字符串，可选）
- `role`（字符串，可选）




##### `gemini_cli.malformed_json_response`

当无法解析 JSON 响应时记录。



<summary>属性</summary>

- `model`（字符串）




#### 模型路由

这些日志跟踪 Gemini CLI 如何选择请求并将请求路由到模型。

##### `gemini_cli.slash_command`

记录斜杠命令的执行。



<summary>属性</summary>

- `command`（字符串）
- `subcommand`（字符串，可选）
- `status`（字符串：“成功”或“错误”）




##### `gemini_cli.slash_command.model`

通过斜杠命令记录模型选择。



<summary>属性</summary>

- `model_name`（字符串）




##### `gemini_cli.model_routing`

记录模型路由器决策和推理。



<summary>属性</summary>

- `decision_model`（字符串）
- `decision_source`（字符串）
- `routing_latency_ms`（整数）
- `reasoning`（字符串，可选）
- `failed`（布尔值）
- `error_message`（字符串，可选）
- `approval_mode`（字符串）




#### 聊天和流媒体

这些日志跟踪聊天上下文压缩和流块错误。

##### `gemini_cli.chat_compression`

记录聊天上下文压缩事件。



<summary>属性</summary>

- `tokens_before`（整数）
- `tokens_after`（整数）




##### `gemini_cli.chat.invalid_chunk`

记录流中收到的无效块。



<summary>属性</summary>

- `error_message`（字符串，可选）




##### `gemini_cli.chat.content_retry`

由于内容错误而重试日志。



<summary>属性</summary>

- `attempt_number`（整数）
- `error_type`（字符串）
- `retry_delay_ms`（整数）
- `model`（字符串）




##### `gemini_cli.chat.content_retry_failure`

所有内容重试失败时记录。



<summary>属性</summary>

- `total_attempts`（整数）
- `final_error_type`（字符串）
- `total_duration_ms`（整数，可选）
- `model`（字符串）




##### `gemini_cli.conversation_finished`

对话会话结束时记录。



<summary>属性</summary>

- `approvalMode`（字符串）
- `turnCount`（整数）




#### 韧性

弹性日志记录回退机制和恢复尝试。

##### `gemini_cli.flash_fallback`

日志切换到闪存模型后备。



<summary>属性</summary>

- `auth_type`（字符串）




##### `gemini_cli.ripgrep_fallback`

日志回退到标准 grep。



<summary>属性</summary>

- `error`（字符串，可选）




##### `gemini_cli.web_fetch_fallback_attempt`

记录网络获取回退尝试。



<summary>属性</summary>

- `reason`（字符串：“private_ip”或“primary_failed”）




##### `gemini_cli.agent.recovery_attempt`

日志尝试从 agent 错误中恢复。



<summary>属性</summary>

- `agent_name`（字符串）
- `attempt_number`（整数）
- `success`（布尔值）
- `error_type`（字符串，可选）




#### 扩展

扩展日志跟踪生命周期事件和设置更改。

##### `gemini_cli.extension_install`

安装扩展时记录。



<summary>属性</summary>

- `extension_name`（字符串）
- `extension_version`（字符串）
- `extension_source`（字符串）
- `status`（字符串）




##### `gemini_cli.extension_uninstall`

卸载扩展时记录。



<summary>属性</summary>

- `extension_name`（字符串）
- `status`（字符串）




##### `gemini_cli.extension_enable`

启用扩展时记录。



<summary>属性</summary>

- `extension_name`（字符串）
- `setting_scope`（字符串）




##### `gemini_cli.extension_disable`

禁用扩展时记录。



<summary>属性</summary>

- `extension_name`（字符串）
- `setting_scope`（字符串）




#### Agent 运行

Agent 日志跟踪 agent 执行的生命周期。

##### `gemini_cli.agent.start`

记录 agent 运行开始的时间。



<summary>属性</summary>

- `agent_id`（字符串）
- `agent_name`（字符串）




##### `gemini_cli.agent.finish`

agent 运行完成时记录。



<summary>属性</summary>

- `agent_id`（字符串）
- `agent_name`（字符串）
- `duration_ms`（整数）
- `turn_count`（整数）
- `terminate_reason`（字符串）




#### IDE

IDE 日志捕获 IDE 配套设备的连接事件。

##### `gemini_cli.ide_connection`

记录 IDE 同伴连接。



<summary>属性</summary>

- `connection_type`（字符串）




#### UI

UI 日志跟踪终端渲染问题。

##### `kitty_sequence_overflow`

记录终端控制序列溢出。



<summary>属性</summary>

- `sequence_length`（整数）
- `truncated_sequence`（字符串）




#### 杂项

##### `gemini_cli.rewind`

倒回对话状态时记录。



<summary>属性</summary>

- `outcome`（字符串）




##### `gemini_cli.conseca.verdict`

记录来自 ConSeca 的安全判决。



<summary>属性</summary>

- `verdict`（字符串）
- `decision`（字符串：“接受”、“拒绝”或“修改”）
- `reason`（字符串，可选）
- `tool_name`（字符串，可选）




##### `gemini_cli.hook_call`

记录生命周期钩子的执行。



<summary>属性</summary>

- `hook_name`（字符串）
- `hook_type`（字符串）
- `duration_ms`（整数）
- `success`（布尔值）




##### `gemini_cli.tool_output_masking`

当工具输出因隐私而被屏蔽时记录。



<summary>属性</summary>

- `tokens_before`（整数）
- `tokens_after`（整数）
- `masked_count`（整数）
- `total_prunable_tokens`（整数）




##### `gemini_cli.keychain.availability`

记录钥匙串可用性检查。



<summary>属性</summary>

- `available`（布尔值）

##### `gemini_cli.startup_stats`

记录详细的启动性能统计信息。



<summary>属性</summary>

- `phases`（启动阶段的json数组）
- `os_platform`（字符串）
- `os_release`（字符串）
- `is_docker`（布尔值）







### 指标

指标提供了随时间变化的行为的数值测量。

#### 自定义指标

Gemini CLI 导出多个自定义指标。

##### 会议

##### `gemini_cli.session.count`

每次 CLI 启动时增加一次。

##### 入职

跟踪从身份验证到用户的入职流程

- `gemini_cli.onboarding.start`（计数器，Int）：当
身份验证流程开始。

- `gemini_cli.onboarding.success`（计数器，Int）：当用户
入职流程成功完成。

<summary>属性（成功）</summary>

- `user_tier`（字符串）

##### 工具

##### `gemini_cli.tool.call.count`

计算工具调用次数。



<summary>属性</summary>

- `function_name`（字符串）
- `success`（布尔值）
- `decision`（字符串：“接受”、“拒绝”、“修改”或“auto_accept”）
- `tool_type`（字符串：“mcp”或“native”）




##### `gemini_cli.tool.call.latency`

测量工具调用延迟（以毫秒为单位）。



<summary>属性</summary>

- `function_name`（字符串）




##### API

##### `gemini_cli.api.request.count`

计算所有 API 请求。



<summary>属性</summary>

- `model`（字符串）
- `status_code`（整数或字符串）
- `error_type`（字符串，可选）




##### `gemini_cli.api.request.latency`

测量 API 请求延迟（以毫秒为单位）。



<summary>属性</summary>

- `model`（字符串）




##### 代币使用

##### `gemini_cli.token.usage`

计算输入、输出、思想、缓存和工具令牌。



<summary>属性</summary>

- `model`（字符串）
- `type`（字符串：“输入”、“输出”、“思想”、“缓存”或“工具”）




##### 文件

##### `gemini_cli.file.operation.count`

统计文件操作。



<summary>属性</summary>

- `operation`（字符串：“创建”、“读取”或“更新”）
- `lines`（整数，可选）
- `mimetype`（字符串，可选）
- `extension`（字符串，可选）
- `programming_language`（字符串，可选）




##### `gemini_cli.lines.changed`

计算添加或删除的行数。



<summary>属性</summary>

- `function_name`（字符串，可选）
- `type`（字符串：“添加”或“删除”）




##### 聊天和流媒体

##### `gemini_cli.chat_compression`

计算压缩操作。



<summary>属性</summary>

- `tokens_before`（整数）
- `tokens_after`（整数）




##### `gemini_cli.chat.invalid_chunk.count`

计算无效流块的数量。

##### `gemini_cli.chat.content_retry.count`

计算内容错误重试次数。

##### `gemini_cli.chat.content_retry_failure.count`

对所有重试失败的请求进行计数。

##### 模型路由

##### `gemini_cli.slash_command.model.call_count`

计算模型选择。



<summary>属性</summary>

- `slash_command.model.model_name`（字符串）




##### `gemini_cli.model_routing.latency`

测量路由决策延迟。



<summary>属性</summary>

- `routing.decision_model`（字符串）
- `routing.decision_source`（字符串）
- `routing.approval_mode`（字符串）




##### `gemini_cli.model_routing.failure.count`

计算路由失败次数。



<summary>属性</summary>

- `routing.decision_source`（字符串）
- `routing.error_message`（字符串）
- `routing.approval_mode`（字符串）




##### Agent 运行

##### `gemini_cli.agent.run.count`

计数 agent 次运行。



<summary>属性</summary>

- `agent_name`（字符串）
- `terminate_reason`（字符串）




##### `gemini_cli.agent.duration`

测量 agent 运行持续时间。



<summary>属性</summary>

- `agent_name`（字符串）




##### `gemini_cli.agent.turns`

计算每次 agent 运行的圈数。



<summary>属性</summary>

- `agent_name`（字符串）




##### 审批方式

##### `gemini_cli.plan.execution.count`

计算计划执行情况。



<summary>属性</summary>

- `approval_mode`（字符串）




##### UI

##### `gemini_cli.ui.flicker.count`

统计终端闪烁事件。

##### 性能

Gemini CLI 提供高级监控的详细性能指标。

##### `gemini_cli.startup.duration`

按阶段测量启动时间。



<summary>属性</summary>

- `phase`（字符串）
- `details`（地图，可选）




##### `gemini_cli.memory.usage`

测量堆和 RSS 内存。



<summary>属性</summary>

- `memory_type`（字符串：“heap_used”、“heap_total”、“外部”、“rss”）
- `component`（字符串，可选）




##### `gemini_cli.cpu.usage`

测量 CPU 使用百分比。



<summary>属性</summary>

- `component`（字符串，可选）




##### `gemini_cli.tool.queue.depth`

测量工具执行队列深度。

##### `gemini_cli.tool.execution.breakdown`

按阶段细分工具时间。



<summary>属性</summary>

- `function_name`（字符串）
- `phase`（字符串：“验证”、“准备”、“执行”、
  "result_processing")




#### GenAI 语义约定

这些指标遵循标准 [OpenTelemetry GenAI 语义约定 ]。

- `gen_ai.client.token.usage`：计算每个操作使用的令牌。
- `gen_ai.client.operation.duration`：测量操作持续时间（以秒为单位）。

[OpenTelemetry GenAI 语义约定]:
 https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/gen-ai-metrics.md

### 痕迹

跟踪提供了 agent 和后端操作的“幕后”视图。使用
跟踪来调试工具交互并优化性能。

<!-- prettier-ignore -->
> [！NOTE]
> 默认情况下禁用详细跟踪属性（例如完整提示和工具输出）
> 以最大限度地减少开销。您必须显式将 `telemetry.traces` 设置为 `true`（或设置 
> `GEMINI_TELEMETRY_TRACES_ENABLED=true`）才能捕获它们。

每个跟踪都通过标准跨度属性捕获丰富的元数据。



<summary>标准跨度属性</summary>

- `gen_ai.operation.name`：高级操作（例如，`tool_call`，
`llm_call`、`user_prompt`、`system_prompt`、`agent_call` 或 
 `schedule_tool_calls`）。
- `gen_ai.agent.name`：设置为`gemini-cli`。
- `gen_ai.agent.description`：服务agent描述。
- `gen_ai.input.messages`：输入数据或元数据。
- `gen_ai.output.messages`：输出数据或结果。
- `gen_ai.request.model`：请求型号名称。
- `gen_ai.response.model`：响应模型名称。
- `gen_ai.prompt.name`：提示符名称。
- `gen_ai.tool.name`：执行的工具名称。
- `gen_ai.tool.call_id`：工具调用的唯一ID。
- `gen_ai.tool.description`：工具说明。
- `gen_ai.tool.definitions`：JSON格式的刀具定义。
- `gen_ai.usage.input_tokens`：输入令牌的数量。
- `gen_ai.usage.output_tokens`：输出令牌的数量。
- `gen_ai.system_instructions`：JSON格式的系统指令。
- `gen_ai.conversation.id`：CLI 会话 ID。




有关事件语义约定的更多详细信息，请参阅
[OpenTelemetry文档](<https://github.com/open-telemetry/semantic-conventions/blob/8b4f210f43136e57c1f6f47292eb6d38e3bf30bb/docs/gen-ai/gen-ai-events.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Observability with OpenTelemetry

Observability is the key to turning experimental AI into reliable software.
Gemini CLI provides built-in support for OpenTelemetry, transforming every agent
interaction into a rich stream of logs, metrics, and traces. This three-pillar
approach gives you the high-fidelity visibility needed to understand agent
behavior, optimize performance, and ensure reliability across your entire
workflow.

Whether you are debugging a complex tool interaction locally or monitoring
enterprise-wide usage in the cloud, Gemini CLI's observability system provides
the actionable intelligence needed to move from "black box" AI to predictable,
high-performance systems.

## OpenTelemetry integration

Gemini CLI integrates with **[OpenTelemetry]**, a vendor-neutral,
industry-standard observability framework.

The observability system provides:

- Universal compatibility: Export to any OpenTelemetry backend (Google Cloud,
  Jaeger, Prometheus, Datadog, etc.).
- Standardized data: Use consistent formats and collection methods across your
  toolchain.
- Future-proof integration: Connect with existing and future observability
  infrastructure.
- No vendor lock-in: Switch between backends without changing your
  instrumentation.

[OpenTelemetry]: <https://opentelemetry.io/>

## Configuration

You control telemetry behavior through the `.gemini/settings.json` file.
Environment variables can override these settings.

| Setting        | Environment Variable              | Description                                         | Values            | Default                 |
| -------------- | --------------------------------- | --------------------------------------------------- | ----------------- | ----------------------- |
| `enabled`      | `GEMINI_TELEMETRY_ENABLED`        | Enable or disable telemetry                         | `true`/`false`    | `false`                 |
| `traces`       | `GEMINI_TELEMETRY_TRACES_ENABLED` | Enable detailed attribute tracing                   | `true`/`false`    | `false`                 |
| `target`       | `GEMINI_TELEMETRY_TARGET`         | Where to send telemetry data                        | `"gcp"`/`"local"` | `"local"`               |
| `otlpEndpoint` | `GEMINI_TELEMETRY_OTLP_ENDPOINT`  | OTLP collector endpoint                             | URL string        | `http://localhost:4317` |
| `otlpProtocol` | `GEMINI_TELEMETRY_OTLP_PROTOCOL`  | OTLP transport protocol                             | `"grpc"`/`"http"` | `"grpc"`                |
| `otlpHeaders`  | `GEMINI_TELEMETRY_OTLP_HEADERS`   | Custom headers for OTLP exporter requests           | JSON/`key=value`  | -                       |
| `outfile`      | `GEMINI_TELEMETRY_OUTFILE`        | Save telemetry to file (overrides `otlpEndpoint`)   | file path         | -                       |
| `logPrompts`   | `GEMINI_TELEMETRY_LOG_PROMPTS`    | Include prompts in telemetry logs                   | `true`/`false`    | `true`                  |
| `useCollector` | `GEMINI_TELEMETRY_USE_COLLECTOR`  | Use external OTLP collector (advanced)              | `true`/`false`    | `false`                 |
| `useCliAuth`   | `GEMINI_TELEMETRY_USE_CLI_AUTH`   | Use CLI credentials for telemetry (GCP target only) | `true`/`false`    | `false`                 |
| -              | `GEMINI_CLI_SURFACE`              | Optional custom label for traffic reporting         | string            | -                       |

**Note on boolean environment variables:** For boolean settings like `enabled`,
setting the environment variable to `true` or `1` enables the feature.

For detailed configuration information, see the
[Configuration guide](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>).

## Google Cloud telemetry

You can export telemetry data directly to Google Cloud Trace, Cloud Monitoring,
and Cloud Logging.

### Prerequisites

You must complete several setup steps before enabling Google Cloud telemetry.

1.  Set your Google Cloud project ID:

    - To send telemetry to a separate project:

      **macOS/Linux**

      ```bash
      export OTLP_GOOGLE_CLOUD_PROJECT="your-telemetry-project-id"
      ```

      **Windows (PowerShell)**

      ```powershell
      $env:OTLP_GOOGLE_CLOUD_PROJECT="your-telemetry-project-id"
      ```

    - To send telemetry to the same project as inference:

      **macOS/Linux**

      ```bash
      export GOOGLE_CLOUD_PROJECT="your-project-id"
      ```

      **Windows (PowerShell)**

      ```powershell
      $env:GOOGLE_CLOUD_PROJECT="your-project-id"
      ```

2.  Authenticate with Google Cloud using one of these methods:

    - **Method A: Application Default Credentials (ADC)**: Use this method for
      service accounts or standard `gcloud` authentication.

      - For user accounts:
        ```bash
        gcloud auth application-default login
        ```
      - For service accounts:

        **macOS/Linux**

        ```bash
        export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account.json"
        ```

        **Windows (PowerShell)**

        ```powershell
        $env:GOOGLE_APPLICATION_CREDENTIALS="C:\path\to\your\service-account.json"
        ```

    * **Method B: CLI Auth** (Direct export only): Simplest method for local
      users. Gemini CLI uses the same OAuth credentials you used for login. To
      enable this, set `useCliAuth: true` in your `.gemini/settings.json`:

      ```json
      {
        "telemetry": {
          "enabled": true,
          "target": "gcp",
          "useCliAuth": true
        }
      }
      ```

<!-- prettier-ignore -->
> [!NOTE]
> This setting requires **Direct export** (in-process exporters)
> and cannot be used when `useCollector` is `true`. If both are enabled,
> telemetry will be disabled.

3.  Ensure your account or service account has these IAM roles:

    - Cloud Trace Agent
    - Monitoring Metric Writer
    - Logs Writer

4.  Enable the required Google Cloud APIs:
    ```bash
    gcloud services enable \
      cloudtrace.googleapis.com \
      monitoring.googleapis.com \
      logging.googleapis.com \
      --project="$OTLP_GOOGLE_CLOUD_PROJECT"
    ```

### Direct export

We recommend using direct export to send telemetry directly to Google Cloud
services.

1.  Enable telemetry in `.gemini/settings.json`:
    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "gcp"
      }
    }
    ```
2.  Run Gemini CLI and send prompts.
3.  View logs, metrics, and traces in the Google Cloud Console. See
    [View Google Cloud telemetry](<#view-google-cloud-telemetry>) for details.

### View Google Cloud telemetry

After you enable telemetry and run Gemini CLI, you can view your data in the
Google Cloud Console.

- **Logs:** [Logs Explorer](<https://console.cloud.google.com/logs/>)
- **Metrics:**
  [Metrics Explorer](<https://console.cloud.google.com/monitoring/metrics-explorer>)
- **Traces:** [Trace Explorer](<https://console.cloud.google.com/traces/list>)

For detailed information on how to use these tools, see the following official
Google Cloud documentation:

- [View and analyze logs with Logs Explorer](<https://cloud.google.com/logging/docs/view/logs-explorer-interface>)
- [Create charts with Metrics Explorer](<https://cloud.google.com/monitoring/charts/metrics-explorer>)
- [Find and explore traces](<https://cloud.google.com/trace/docs/finding-traces>)

#### Monitoring dashboards

Gemini CLI provides a pre-configured
[Google Cloud Monitoring](<https://cloud.google.com/monitoring>) dashboard to
visualize your telemetry.

Find this dashboard under **Google Cloud Monitoring Dashboard Templates** as
"**Gemini CLI Monitoring**".

![Gemini CLI Monitoring Dashboard Overview](<https://github.com/docs/assets/monitoring-dashboard-overview.png>)

![Gemini CLI Monitoring Dashboard Metrics](<https://github.com/docs/assets/monitoring-dashboard-metrics.png>)

![Gemini CLI Monitoring Dashboard Logs](<https://github.com/docs/assets/monitoring-dashboard-logs.png>)

To learn more, see
[Instant insights: Gemini CLI’s pre-configured monitoring dashboards](<https://cloud.google.com/blog/topics/developers-practitioners/instant-insights-gemini-clis-new-pre-configured-monitoring-dashboards/>).

## Local telemetry

You can capture telemetry data locally for development and debugging. We
recommend using file-based output for local development.

1.  Enable telemetry in `.gemini/settings.json`:
    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "local",
        "outfile": ".gemini/telemetry.log"
      }
    }
    ```
2.  Run Gemini CLI and send prompts.
3.  View logs and metrics in `.gemini/telemetry.log`.

For advanced local telemetry setups (such as Jaeger or Genkit), see the
[Local development guide](<Gemini CLI 本地开发与链路追踪.md#viewing-traces>).

## Client identification

Gemini CLI includes identifiers in its `User-Agent` header to help you
differentiate and report on API traffic from different environments (for
example, identifying calls from Gemini Code Assist versus a standard terminal).

### Automatic identification

Most integrated environments are identified automatically without additional
configuration. The identifier is included as a prefix to the `User-Agent` and as
a "surface" tag in the parenthetical metadata.

| Environment                         | User-Agent Prefix            | Surface Tag |
| :---------------------------------- | :--------------------------- | :---------- |
| **Gemini Code Assist (Agent Mode)** | `GeminiCLI-a2a-server`       | `vscode`    |
| **Zed (via ACP)**                   | `GeminiCLI-acp-zed`          | `zed`       |
| **XCode (via ACP)**                 | `GeminiCLI-acp-xcode`        | `xcode`     |
| **IntelliJ IDEA (via ACP)**         | `GeminiCLI-acp-intellijidea` | `jetbrains` |
| **Standard Terminal**               | `GeminiCLI`                  | `terminal`  |

**Example User-Agent:**
`GeminiCLI-a2a-server/0.34.0/gemini-pro (linux; x64; vscode)`

### Custom identification

You can provide a custom identifier for your own scripts or automation by
setting the `GEMINI_CLI_SURFACE` environment variable. This is useful for
tracking specific internal tools or distribution channels in your GCP logs.

**macOS/Linux**

```bash
export GEMINI_CLI_SURFACE="my-custom-tool"
```

**Windows (PowerShell)**

```powershell
$env:GEMINI_CLI_SURFACE="my-custom-tool"
```

When set, the value appears at the end of the `User-Agent` parenthetical:
`GeminiCLI/0.34.0/gemini-pro (linux; x64; my-custom-tool)`

## Logs, metrics, and traces

This section describes the structure of logs, metrics, and traces generated by
Gemini CLI.

Gemini CLI includes `session.id`, `installation.id`, `active_approval_mode`, and
`user.email` (when authenticated) as common attributes on all data.

### Logs

Logs provide timestamped records of specific events. Gemini CLI logs events
across several categories.

#### Sessions

Session logs capture startup configuration and prompt submissions.

##### `gemini_cli.config`

Emitted at startup with the CLI configuration.



<summary>Attributes</summary>

- `model` (string)
- `embedding_model` (string)
- `sandbox_enabled` (boolean)
- `core_tools_enabled` (string)
- `approval_mode` (string)
- `api_key_enabled` (boolean)
- `vertex_ai_enabled` (boolean)
- `log_user_prompts_enabled` (boolean)
- `file_filtering_respect_git_ignore` (boolean)
- `debug_mode` (boolean)
- `mcp_servers` (string)
- `mcp_servers_count` (int)
- `mcp_tools` (string)
- `mcp_tools_count` (int)
- `output_format` (string)
- `extensions` (string)
- `extension_ids` (string)
- `extensions_count` (int)
- `auth_type` (string)
- `worktree_active` (boolean)
- `github_workflow_name` (string, optional)
- `github_repository_hash` (string, optional)
- `github_event_name` (string, optional)
- `github_pr_number` (string, optional)
- `github_issue_number` (string, optional)
- `github_custom_tracking_id` (string, optional)




##### `gemini_cli.user_prompt`

Emitted when you submit a prompt.



<summary>Attributes</summary>

- `prompt_length` (int)
- `prompt_id` (string)
- `prompt` (string; excluded if `telemetry.logPrompts` is `false`)
- `auth_type` (string)




#### Approval mode

These logs track changes to and usage of different approval modes.

##### Lifecycle

##### `approval_mode_switch`

Logs when you change the approval mode.



<summary>Attributes</summary>

- `from_mode` (string)
- `to_mode` (string)




##### `approval_mode_duration`

Records time spent in an approval mode.



<summary>Attributes</summary>

- `mode` (string)
- `duration_ms` (int)




##### Execution

##### `plan_execution`

Logs when you execute a plan and switch from plan mode to active execution.



<summary>Attributes</summary>

- `approval_mode` (string)




#### Tools

Tool logs capture executions, truncation, and edit behavior.

##### `gemini_cli.tool_call`

Emitted for each tool (function) call.



<summary>Attributes</summary>

- `function_name` (string)
- `function_args` (string)
- `duration_ms` (int)
- `success` (boolean)
- `decision` (string: "accept", "reject", "auto_accept", or "modify")
- `error` (string, optional)
- `error_type` (string, optional)
- `prompt_id` (string)
- `tool_type` (string: "native" or "mcp")
- `mcp_server_name` (string, optional)
- `extension_name` (string, optional)
- `extension_id` (string, optional)
- `content_length` (int, optional)
- `start_time` (number, optional)
- `end_time` (number, optional)
- `metadata` (object, optional), which may include:
  - `model_added_lines` (number)
  - `model_removed_lines` (number)
  - `user_added_lines` (number)
  - `user_removed_lines` (number)
  - `ask_user` (object)




##### `gemini_cli.tool_output_truncated`

Logs when tool output is truncated.



<summary>Attributes</summary>

- `tool_name` (string)
- `original_content_length` (int)
- `truncated_content_length` (int)
- `threshold` (int)
- `lines` (int)
- `prompt_id` (string)




##### `gemini_cli.edit_strategy`

Records the chosen edit strategy.



<summary>Attributes</summary>

- `strategy` (string)




##### `gemini_cli.edit_correction`

Records the result of an edit correction.



<summary>Attributes</summary>

- `correction` (string: "success" or "failure")




##### `gen_ai.client.inference.operation.details`

Provides detailed GenAI operation data aligned with OpenTelemetry conventions.



<summary>Attributes</summary>

- `gen_ai.request.model` (string)
- `gen_ai.provider.name` (string)
- `gen_ai.operation.name` (string)
- `gen_ai.input.messages` (json string)
- `gen_ai.output.messages` (json string)
- `gen_ai.response.finish_reasons` (array of strings)
- `gen_ai.usage.input_tokens` (int)
- `gen_ai.usage.output_tokens` (int)
- `gen_ai.request.temperature` (float)
- `gen_ai.request.top_p` (float)
- `gen_ai.request.top_k` (int)
- `gen_ai.request.max_tokens` (int)
- `gen_ai.system_instructions` (json string)
- `server.address` (string)
- `server.port` (int)




#### Files

File logs track operations performed by tools.

##### `gemini_cli.file_operation`

Emitted for each file creation, read, or update.



<summary>Attributes</summary>

- `tool_name` (string)
- `operation` (string: "create", "read", or "update")
- `lines` (int, optional)
- `mimetype` (string, optional)
- `extension` (string, optional)
- `programming_language` (string, optional)




#### API

API logs capture requests, responses, and errors from Gemini API.

##### `gemini_cli.api_request`

Request sent to Gemini API.



<summary>Attributes</summary>

- `model` (string)
- `prompt_id` (string)
- `role` (string: "user", "model", or "system")
- `request_text` (string, optional)




##### `gemini_cli.api_response`

Response received from Gemini API.



<summary>Attributes</summary>

- `model` (string)
- `status_code` (int or string)
- `duration_ms` (int)
- `input_token_count` (int)
- `output_token_count` (int)
- `cached_content_token_count` (int)
- `thoughts_token_count` (int)
- `tool_token_count` (int)
- `total_token_count` (int)
- `prompt_id` (string)
- `auth_type` (string)
- `finish_reasons` (array of strings)
- `response_text` (string, optional)




##### `gemini_cli.api_error`

Logs when an API request fails.



<summary>Attributes</summary>

- `error.message` (string)
- `model_name` (string)
- `duration` (int)
- `prompt_id` (string)
- `auth_type` (string)
- `error_type` (string, optional)
- `status_code` (int or string, optional)
- `role` (string, optional)




##### `gemini_cli.malformed_json_response`

Logs when a JSON response cannot be parsed.



<summary>Attributes</summary>

- `model` (string)




#### Model routing

These logs track how Gemini CLI selects and routes requests to models.

##### `gemini_cli.slash_command`

Logs slash command execution.



<summary>Attributes</summary>

- `command` (string)
- `subcommand` (string, optional)
- `status` (string: "success" or "error")




##### `gemini_cli.slash_command.model`

Logs model selection via slash command.



<summary>Attributes</summary>

- `model_name` (string)




##### `gemini_cli.model_routing`

Records model router decisions and reasoning.



<summary>Attributes</summary>

- `decision_model` (string)
- `decision_source` (string)
- `routing_latency_ms` (int)
- `reasoning` (string, optional)
- `failed` (boolean)
- `error_message` (string, optional)
- `approval_mode` (string)




#### Chat and streaming

These logs track chat context compression and streaming chunk errors.

##### `gemini_cli.chat_compression`

Logs chat context compression events.



<summary>Attributes</summary>

- `tokens_before` (int)
- `tokens_after` (int)




##### `gemini_cli.chat.invalid_chunk`

Logs invalid chunks received in a stream.



<summary>Attributes</summary>

- `error_message` (string, optional)




##### `gemini_cli.chat.content_retry`

Logs retries due to content errors.



<summary>Attributes</summary>

- `attempt_number` (int)
- `error_type` (string)
- `retry_delay_ms` (int)
- `model` (string)




##### `gemini_cli.chat.content_retry_failure`

Logs when all content retries fail.



<summary>Attributes</summary>

- `total_attempts` (int)
- `final_error_type` (string)
- `total_duration_ms` (int, optional)
- `model` (string)




##### `gemini_cli.conversation_finished`

Logs when a conversation session ends.



<summary>Attributes</summary>

- `approvalMode` (string)
- `turnCount` (int)




#### Resilience

Resilience logs record fallback mechanisms and recovery attempts.

##### `gemini_cli.flash_fallback`

Logs switch to a flash model fallback.



<summary>Attributes</summary>

- `auth_type` (string)




##### `gemini_cli.ripgrep_fallback`

Logs fallback to standard grep.



<summary>Attributes</summary>

- `error` (string, optional)




##### `gemini_cli.web_fetch_fallback_attempt`

Logs web-fetch fallback attempts.



<summary>Attributes</summary>

- `reason` (string: "private_ip" or "primary_failed")




##### `gemini_cli.agent.recovery_attempt`

Logs attempts to recover from agent errors.



<summary>Attributes</summary>

- `agent_name` (string)
- `attempt_number` (int)
- `success` (boolean)
- `error_type` (string, optional)




#### Extensions

Extension logs track lifecycle events and settings changes.

##### `gemini_cli.extension_install`

Logs when you install an extension.



<summary>Attributes</summary>

- `extension_name` (string)
- `extension_version` (string)
- `extension_source` (string)
- `status` (string)




##### `gemini_cli.extension_uninstall`

Logs when you uninstall an extension.



<summary>Attributes</summary>

- `extension_name` (string)
- `status` (string)




##### `gemini_cli.extension_enable`

Logs when you enable an extension.



<summary>Attributes</summary>

- `extension_name` (string)
- `setting_scope` (string)




##### `gemini_cli.extension_disable`

Logs when you disable an extension.



<summary>Attributes</summary>

- `extension_name` (string)
- `setting_scope` (string)




#### Agent runs

Agent logs track the lifecycle of agent executions.

##### `gemini_cli.agent.start`

Logs when an agent run begins.



<summary>Attributes</summary>

- `agent_id` (string)
- `agent_name` (string)




##### `gemini_cli.agent.finish`

Logs when an agent run completes.



<summary>Attributes</summary>

- `agent_id` (string)
- `agent_name` (string)
- `duration_ms` (int)
- `turn_count` (int)
- `terminate_reason` (string)




#### IDE

IDE logs capture connectivity events for the IDE companion.

##### `gemini_cli.ide_connection`

Logs IDE companion connections.



<summary>Attributes</summary>

- `connection_type` (string)




#### UI

UI logs track terminal rendering issues.

##### `kitty_sequence_overflow`

Logs terminal control sequence overflows.



<summary>Attributes</summary>

- `sequence_length` (int)
- `truncated_sequence` (string)




#### Miscellaneous

##### `gemini_cli.rewind`

Logs when the conversation state is rewound.



<summary>Attributes</summary>

- `outcome` (string)




##### `gemini_cli.conseca.verdict`

Logs security verdicts from ConSeca.



<summary>Attributes</summary>

- `verdict` (string)
- `decision` (string: "accept", "reject", or "modify")
- `reason` (string, optional)
- `tool_name` (string, optional)




##### `gemini_cli.hook_call`

Logs execution of lifecycle hooks.



<summary>Attributes</summary>

- `hook_name` (string)
- `hook_type` (string)
- `duration_ms` (int)
- `success` (boolean)




##### `gemini_cli.tool_output_masking`

Logs when tool output is masked for privacy.



<summary>Attributes</summary>

- `tokens_before` (int)
- `tokens_after` (int)
- `masked_count` (int)
- `total_prunable_tokens` (int)




##### `gemini_cli.keychain.availability`

Logs keychain availability checks.



<summary>Attributes</summary>

- `available` (boolean)

##### `gemini_cli.startup_stats`

Logs detailed startup performance statistics.



<summary>Attributes</summary>

- `phases` (json array of startup phases)
- `os_platform` (string)
- `os_release` (string)
- `is_docker` (boolean)







### Metrics

Metrics provide numerical measurements of behavior over time.

#### Custom metrics

Gemini CLI exports several custom metrics.

##### Sessions

##### `gemini_cli.session.count`

Incremented once per CLI startup.

##### Onboarding

Tracks onboarding flow from authentication to the user

- `gemini_cli.onboarding.start` (Counter, Int): Incremented when the
  authentication flow begins.

- `gemini_cli.onboarding.success` (Counter, Int): Incremented when the user
onboarding flow completes successfully.

<summary>Attributes (Success)</summary>

- `user_tier` (string)

##### Tools

##### `gemini_cli.tool.call.count`

Counts tool calls.



<summary>Attributes</summary>

- `function_name` (string)
- `success` (boolean)
- `decision` (string: "accept", "reject", "modify", or "auto_accept")
- `tool_type` (string: "mcp" or "native")




##### `gemini_cli.tool.call.latency`

Measures tool call latency (in ms).



<summary>Attributes</summary>

- `function_name` (string)




##### API

##### `gemini_cli.api.request.count`

Counts all API requests.



<summary>Attributes</summary>

- `model` (string)
- `status_code` (int or string)
- `error_type` (string, optional)




##### `gemini_cli.api.request.latency`

Measures API request latency (in ms).



<summary>Attributes</summary>

- `model` (string)




##### Token usage

##### `gemini_cli.token.usage`

Counts input, output, thought, cache, and tool tokens.



<summary>Attributes</summary>

- `model` (string)
- `type` (string: "input", "output", "thought", "cache", or "tool")




##### Files

##### `gemini_cli.file.operation.count`

Counts file operations.



<summary>Attributes</summary>

- `operation` (string: "create", "read", or "update")
- `lines` (int, optional)
- `mimetype` (string, optional)
- `extension` (string, optional)
- `programming_language` (string, optional)




##### `gemini_cli.lines.changed`

Counts added or removed lines.



<summary>Attributes</summary>

- `function_name` (string, optional)
- `type` (string: "added" or "removed")




##### Chat and streaming

##### `gemini_cli.chat_compression`

Counts compression operations.



<summary>Attributes</summary>

- `tokens_before` (int)
- `tokens_after` (int)




##### `gemini_cli.chat.invalid_chunk.count`

Counts invalid stream chunks.

##### `gemini_cli.chat.content_retry.count`

Counts content error retries.

##### `gemini_cli.chat.content_retry_failure.count`

Counts requests where all retries failed.

##### Model routing

##### `gemini_cli.slash_command.model.call_count`

Counts model selections.



<summary>Attributes</summary>

- `slash_command.model.model_name` (string)




##### `gemini_cli.model_routing.latency`

Measures routing decision latency.



<summary>Attributes</summary>

- `routing.decision_model` (string)
- `routing.decision_source` (string)
- `routing.approval_mode` (string)




##### `gemini_cli.model_routing.failure.count`

Counts routing failures.



<summary>Attributes</summary>

- `routing.decision_source` (string)
- `routing.error_message` (string)
- `routing.approval_mode` (string)




##### Agent runs

##### `gemini_cli.agent.run.count`

Counts agent runs.



<summary>Attributes</summary>

- `agent_name` (string)
- `terminate_reason` (string)




##### `gemini_cli.agent.duration`

Measures agent run duration.



<summary>Attributes</summary>

- `agent_name` (string)




##### `gemini_cli.agent.turns`

Counts turns per agent run.



<summary>Attributes</summary>

- `agent_name` (string)




##### Approval mode

##### `gemini_cli.plan.execution.count`

Counts plan executions.



<summary>Attributes</summary>

- `approval_mode` (string)




##### UI

##### `gemini_cli.ui.flicker.count`

Counts terminal flicker events.

##### Performance

Gemini CLI provides detailed performance metrics for advanced monitoring.

##### `gemini_cli.startup.duration`

Measures startup time by phase.



<summary>Attributes</summary>

- `phase` (string)
- `details` (map, optional)




##### `gemini_cli.memory.usage`

Measures heap and RSS memory.



<summary>Attributes</summary>

- `memory_type` (string: "heap_used", "heap_total", "external", "rss")
- `component` (string, optional)




##### `gemini_cli.cpu.usage`

Measures CPU usage percentage.



<summary>Attributes</summary>

- `component` (string, optional)




##### `gemini_cli.tool.queue.depth`

Measures tool execution queue depth.

##### `gemini_cli.tool.execution.breakdown`

Breaks down tool time by phase.



<summary>Attributes</summary>

- `function_name` (string)
- `phase` (string: "validation", "preparation", "execution",
  "result_processing")




#### GenAI semantic convention

These metrics follow standard [OpenTelemetry GenAI semantic conventions].

- `gen_ai.client.token.usage`: Counts tokens used per operation.
- `gen_ai.client.operation.duration`: Measures operation duration in seconds.

[OpenTelemetry GenAI semantic conventions]:
  https://github.com/open-telemetry/semantic-conventions/blob/main/docs/gen-ai/gen-ai-metrics.md

### Traces

Traces provide an "under-the-hood" view of agent and backend operations. Use
traces to debug tool interactions and optimize performance.

<!-- prettier-ignore -->
> [!NOTE]
> Detailed trace attributes (like full prompts and tool outputs) are disabled by default
> to minimize overhead. You must explicitly set `telemetry.traces` to `true` (or set
> `GEMINI_TELEMETRY_TRACES_ENABLED=true`) to capture them.

Every trace captures rich metadata via standard span attributes.



<summary>Standard span attributes</summary>

- `gen_ai.operation.name`: High-level operation (for example, `tool_call`,
  `llm_call`, `user_prompt`, `system_prompt`, `agent_call`, or
  `schedule_tool_calls`).
- `gen_ai.agent.name`: Set to `gemini-cli`.
- `gen_ai.agent.description`: The service agent description.
- `gen_ai.input.messages`: Input data or metadata.
- `gen_ai.output.messages`: Output data or results.
- `gen_ai.request.model`: Request model name.
- `gen_ai.response.model`: Response model name.
- `gen_ai.prompt.name`: The prompt name.
- `gen_ai.tool.name`: Executed tool name.
- `gen_ai.tool.call_id`: Unique ID for the tool call.
- `gen_ai.tool.description`: Tool description.
- `gen_ai.tool.definitions`: Tool definitions in JSON format.
- `gen_ai.usage.input_tokens`: Number of input tokens.
- `gen_ai.usage.output_tokens`: Number of output tokens.
- `gen_ai.system_instructions`: System instructions in JSON format.
- `gen_ai.conversation.id`: The CLI session ID.




For more details on semantic conventions for events, see the
[OpenTelemetry documentation](<https://github.com/open-telemetry/semantic-conventions/blob/8b4f210f43136e57c1f6f47292eb6d38e3bf30bb/docs/gen-ai/gen-ai-events.md>).


</details>
