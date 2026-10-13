# Gemini CLI 本地开发与链路追踪

介绍 OpenTelemetry 记录和 Genkit、Jaeger、Google Cloud 的查看方式，帮助调试 Agent 行为及添加追踪。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-06-18](<https://github.com/google-gemini/gemini-cli/commit/93844dfa10f6d71edc09be40dfde205edfbcc939>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/local-development.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/local-development.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 本地发展指南

本指南提供了如何设置和使用 Gemini CLI 的本地开发 
 功能的说明。

## 追踪

Gemini CLI 使用 OpenTelemetry (OTel) 记录跟踪信息，帮助您调试 agent
 行为。跟踪仪器关键事件，例如模型调用、工具调度程序
操作和工具调用。

跟踪提供了对 agent 行为的深入可见性，并帮助您调试复杂的 
 问题。当您启用遥测时，它们会被自动捕获。

### 查看痕迹

您可以使用 Genkit Developer UI、Jaeger 或 Google Cloud 查看跟踪。

#### 使用Genkit

Genkit 提供基于 Web 的 UI，用于查看轨迹和其他遥测数据。

1.  **启动Genkit遥测服务器：**

运行以下命令启动 Genkit 服务器：

    ```bash
    npm run telemetry -- --target=genkit
    ```

该脚本将为 Genkit 开发者 UI 输出 URL。例如：
 `Genkit Developer UI: http://localhost:4000`

2.  **运行Gemini CLI:**

在单独的终端中，运行 Gemini CLI 命令：

    ```bash
    gemini
    ```

3.  **查看痕迹：**

在浏览器中打开 Genkit Developer UI URL 并导航到 
 **Traces** 选项卡以查看跟踪。

#### 使用机甲

您可以在 Jaeger UI 中查看本地开发的痕迹。

1.  **启动遥测收集器：**

在终端中运行以下命令来下载并启动 Jaeger 和 
 OTel 收集器：

    ```bash
    npm run telemetry -- --target=local
    ```

此命令将您的工作区配置为本地遥测，并提供指向 Jaeger UI（通常为 `http://localhost:16686`）的 
 链接。

    - **收集器日志：** `~/.gemini/tmp/<projectHash>/otel/collector.log`

2.  **运行Gemini CLI:**

在单独的终端中，运行 Gemini CLI 命令：

    ```bash
    gemini
    ```

3.  **查看痕迹：**

运行命令后，在浏览器中打开 Jaeger UI 链接以查看 
 跟踪记录。

#### 使用Google云

您可以使用 OpenTelemetry 收集器将遥测数据转发到 Google Cloud
Trace 以进行自定义处理或路由。

<!-- prettier-ignore -->
> [！WARNING]
> 在使用此方法之前，请确保您完成
> [Google云遥测先决条件](<Gemini CLI 的 OpenTelemetry 可观测性.md#先决条件>)
> （项目ID、身份验证、IAM角色和API）。

1.  **配置`.gemini/settings.json`:**

    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "gcp",
        "useCollector": true
      }
    }
    ```

2.  **启动遥测收集器：**

运行以下命令启动本地OTel收集器转发到
 Google云：

    ```bash
    npm run telemetry -- --target=gcp
    ```

该脚本输出用于在 Google
 Cloud Console 中查看跟踪记录、指标和日志的链接。

    - **收集器日志：** `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`

3.  **运行Gemini CLI:**

在单独的终端中，运行 Gemini CLI 命令：

    ```bash
    gemini
    ```

4.  **查看日志、指标和跟踪：**

发送提示后，在 Google Cloud Console 中查看您的数据。请参阅
 [遥测文档](<Gemini CLI 的 OpenTelemetry 可观测性.md#查看google云遥测>)
，获取日志、指标和跟踪浏览器的链接。

有关遥测的更多详细信息，请参阅
[遥测文档](<Gemini CLI 的 OpenTelemetry 可观测性.md>)。

### 带痕迹的仪器代码

您可以将跟踪添加到您自己的代码中以进行更详细的检测。

添加跟踪可以帮助您调试和了解执行流程。使用 
`runInDevTraceSpan` 函数将任何代码部分包装在跟踪范围中。

这是一个基本示例：

```typescript
import { runInDevTraceSpan } from '@google/gemini-cli-core';
import { GeminiCliOperation } from '@google/gemini-cli-core/lib/telemetry/constants.js';

await runInDevTraceSpan(
  {
    operation: GeminiCliOperation.ToolCall,
    attributes: {
      [GEN_AI_AGENT_NAME]: 'gemini-cli',
    },
  },
  async ({ metadata }) => {
    // metadata allows you to record the input and output of the
    // operation as well as other attributes.
    metadata.input = { key: 'value' };
    // Set custom attributes.
    metadata.attributes['custom.attribute'] = 'custom.value';

    // Your code to be traced goes here.
    try {
      const output = await somethingRisky();
      metadata.output = output;
      return output;
    } catch (e) {
      metadata.error = e;
      throw e;
    }
  },
);
```

在这个例子中：

- `operation`：跨度的操作类型，用
`GeminiCliOperation` 枚举。
- `metadata.input`：（可选）包含输入数据的对象
跟踪操作。
- `metadata.output`：（可选）包含来自的输出数据的对象
跟踪操作。
- `metadata.attributes`：（可选）要添加到的自定义属性记录
跨度。
- `metadata.error`：（可选）操作失败时记录的错误对象。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Local development guide

This guide provides instructions for setting up and using local development
features for Gemini CLI.

## Tracing

Gemini CLI uses OpenTelemetry (OTel) to record traces that help you debug agent
behavior. Traces instrument key events like model calls, tool scheduler
operations, and tool calls.

Traces provide deep visibility into agent behavior and help you debug complex
issues. They are captured automatically when you enable telemetry.

### View traces

You can view traces using Genkit Developer UI, Jaeger, or Google Cloud.

#### Use Genkit

Genkit provides a web-based UI for viewing traces and other telemetry data.

1.  **Start the Genkit telemetry server:**

    Run the following command to start the Genkit server:

    ```bash
    npm run telemetry -- --target=genkit
    ```

    The script will output the URL for the Genkit Developer UI. For example:
    `Genkit Developer UI: http://localhost:4000`

2.  **Run Gemini CLI:**

    In a separate terminal, run your Gemini CLI command:

    ```bash
    gemini
    ```

3.  **View the traces:**

    Open the Genkit Developer UI URL in your browser and navigate to the
    **Traces** tab to view the traces.

#### Use Jaeger

You can view traces in the Jaeger UI for local development.

1.  **Start the telemetry collector:**

    Run the following command in your terminal to download and start Jaeger and
    an OTel collector:

    ```bash
    npm run telemetry -- --target=local
    ```

    This command configures your workspace for local telemetry and provides a
    link to the Jaeger UI (usually `http://localhost:16686`).

    - **Collector logs:** `~/.gemini/tmp/<projectHash>/otel/collector.log`

2.  **Run Gemini CLI:**

    In a separate terminal, run your Gemini CLI command:

    ```bash
    gemini
    ```

3.  **View the traces:**

    After running your command, open the Jaeger UI link in your browser to view
    the traces.

#### Use Google Cloud

You can use an OpenTelemetry collector to forward telemetry data to Google Cloud
Trace for custom processing or routing.

<!-- prettier-ignore -->
> [!WARNING]
> Ensure you complete the
> [Google Cloud telemetry prerequisites](<Gemini CLI 的 OpenTelemetry 可观测性.md#prerequisites>)
> (Project ID, authentication, IAM roles, and APIs) before using this method.

1.  **Configure `.gemini/settings.json`:**

    ```json
    {
      "telemetry": {
        "enabled": true,
        "target": "gcp",
        "useCollector": true
      }
    }
    ```

2.  **Start the telemetry collector:**

    Run the following command to start a local OTel collector that forwards to
    Google Cloud:

    ```bash
    npm run telemetry -- --target=gcp
    ```

    The script outputs links to view traces, metrics, and logs in the Google
    Cloud Console.

    - **Collector logs:** `~/.gemini/tmp/<projectHash>/otel/collector-gcp.log`

3.  **Run Gemini CLI:**

    In a separate terminal, run your Gemini CLI command:

    ```bash
    gemini
    ```

4.  **View logs, metrics, and traces:**

    After sending prompts, view your data in the Google Cloud Console. See the
    [telemetry documentation](<Gemini CLI 的 OpenTelemetry 可观测性.md#view-google-cloud-telemetry>)
    for links to Logs, Metrics, and Trace explorers.

For more detailed information on telemetry, see the
[telemetry documentation](<Gemini CLI 的 OpenTelemetry 可观测性.md>).

### Instrument code with traces

You can add traces to your own code for more detailed instrumentation.

Adding traces helps you debug and understand the flow of execution. Use the
`runInDevTraceSpan` function to wrap any section of code in a trace span.

Here is a basic example:

```typescript
import { runInDevTraceSpan } from '@google/gemini-cli-core';
import { GeminiCliOperation } from '@google/gemini-cli-core/lib/telemetry/constants.js';

await runInDevTraceSpan(
  {
    operation: GeminiCliOperation.ToolCall,
    attributes: {
      [GEN_AI_AGENT_NAME]: 'gemini-cli',
    },
  },
  async ({ metadata }) => {
    // metadata allows you to record the input and output of the
    // operation as well as other attributes.
    metadata.input = { key: 'value' };
    // Set custom attributes.
    metadata.attributes['custom.attribute'] = 'custom.value';

    // Your code to be traced goes here.
    try {
      const output = await somethingRisky();
      metadata.output = output;
      return output;
    } catch (e) {
      metadata.error = e;
      throw e;
    }
  },
);
```

In this example:

- `operation`: The operation type of the span, represented by the
  `GeminiCliOperation` enum.
- `metadata.input`: (Optional) An object containing the input data for the
  traced operation.
- `metadata.output`: (Optional) An object containing the output data from the
  traced operation.
- `metadata.attributes`: (Optional) A record of custom attributes to add to the
  span.
- `metadata.error`: (Optional) An error object to record if the operation fails.


</details>
