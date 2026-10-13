# Qwen Code Java SDK 指南

介绍推荐的守护进程传输和保留的旧接口，说明安装构建、权限、事件、多轮控制及版本限制。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-01](<https://github.com/QwenLM/qwen-code/commit/8b72f4d456d18900abd94976a900ffdc3b6ca45f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/sdk-java.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/developers/sdk-java.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Qwen Code Java SDK

Qwen Code Java SDK 为 `qwen serve` 提供推荐的守护进程传输，并保留实验性旧版 stdio API 以实现兼容性。两个 API 均在同一 `com.alibaba:qwencode-sdk` 工件中提供。

## 要求

- Java >= 11 对于 `0.1.0-alpha`
- 从源代码构建或发布此 SDK 时，Maven >= 3.9.2
- 守护进程 API 的兼容 `qwen serve`，或旧版 stdio API 的 qwen-code >= 0.5.0

### 依赖关系

- **日志记录 API**：org.slf4j:slf4j-api（在应用程序中选择 SLF4J 提供程序）
- **实用程序**：org.apache.commons：commons-lang3
- **JSON处理**：用于编码的Fastjson2和用于严格解码的Jackson Core
- **测试**：JUnit 5（org.junit.jupiter：junit-jupiter）

## 安装

将以下依赖项添加到您的 Maven `pom.xml`：

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>qwencode-sdk</artifactId>
    <version>0.1.0-alpha</version>
</dependency>
```

或者，如果使用 Gradle，请添加到您的 `build.gradle`：

```gradle
implementation 'com.alibaba:qwencode-sdk:0.1.0-alpha'
```

## 构建和运行

### 构建命令

```bash
# Compile the project
mvn compile

# Run tests
mvn test

# Package the JAR
mvn package

# Install to local repository
mvn install
```

### 来自源代码的真实守护进程 E2E

构建工作区和根 CLI 包后，从存储库根运行真实守护程序 Java 集成测试：

```bash
npm run build
npm run bundle
npx tsx scripts/run-java-daemon-sdk-e2e.ts
```

`npm run build`单独不刷新`dist/cli.js`； E2E 工具启动该捆绑包，但在该捆绑包丢失时失败并出现显式先决条件错误。

## 推荐守护进程 API

启动`qwen serve`，然后创建一个独立的线程范围会话。 `promptText`仅在匹配`turn_complete`后返回；不完整的流失败并显示 `PromptOutcomeIndeterminateException`，而不是返回部分文本作为成功。

对于 `0.1.0-alpha` 假定的生命周期保证，请使用与 SDK 相同的源版本发布的 qwen-code 版本。守护进程必须包含来自 [#7386](<https://github.com/QwenLM/qwen-code/pull/7386>) 的幂等每客户端分离账本、来自 [#7400](<https://github.com/QwenLM/qwen-code/pull/7400>) 的每纪元终端保证，以及此版本的确认准入取消加上 FIFO 取消-耗尽栅栏。仅 #7400 提交是不够的：同线守护进程可以在 agent 分派之前确认取消，而不停止已承认的提示，或者让未确认的会话范围取消到达排队的后继者。捆绑的 ACP 子项使用一次确认的承认感知取消握手；没有该扩展的符合自定义标准的 ACP 子级会收到一个标准 `session/cancel` 通知。功能协商无法区分旧的同线守护进程构建，因此 SDK 无法关闭，而不是将部分输出报告为成功。

在守护进程调度其排队的后继进程之前，捆绑取消握手有意等待目标提示调用解决。它没有仅确认取消的超时：这样做可以让后期会话范围的取消到达下一个提示。如果提供程序、工具或自定义集成无限期地忽略其 `AbortSignal`，则取消突变可能会保持结果未知，并且不得重复使用该会话。将在呼叫者观察范围内收到的正式提示终端视为权威；否则在观察失败后关闭或销毁会话。在不干扰其同级会话的情况下恢复楔入的共享 ACP 子级需要更强的运行时隔离，并且不在此 alpha 合约之外。

```java
import com.alibaba.qwen.code.daemon.DaemonClient;
import com.alibaba.qwen.code.daemon.DaemonSessionClient;
import com.alibaba.qwen.code.daemon.PromptTextResult;
import java.net.URI;

try (DaemonClient daemon = DaemonClient.builder()
        .baseUri(URI.create("http://127.0.0.1:4170"))
        .build();
     DaemonSessionClient session = daemon.createSession()) {
    PromptTextResult result = session.promptText("Explain this repository");
    System.out.println(result.getText());
}
```

需要在创建之前分配会话标识的调用者可以传递RFC UUID v1-v5。 SDK 在突变之前检查 `session_id_override` 并报告不同的返回 ID 为 `SessionCreationOutcomeUnknownException`：

```java
CreateSessionRequest request = CreateSessionRequest.builder()
        .sessionId("550E8400-E29B-41D4-A716-446655440000")
        .build();

try (DaemonSessionClient session = daemon.createSession(request)) {
    System.out.println(session.getSession().getSessionId());
}
```

守护进程将 ID 标准化为小写并创建一个新的线程会话。这不是幂等附加；在出现不明确的创建结果后，使用已知的 ID 进行恢复，而不是重试创建。

如果 `qwen serve` 需要身份验证，请将 
`.bearerToken(System.getenv("QWEN_SERVER_TOKEN"))` 添加到 `DaemonClient`
 构建器。 SDK 在 REST 和 SSE 请求上发送承载，并且不会将其放入 
 和 URL 中。

当您需要有序文本、想法、工具、用法、权限和原始事件回调时，将 `startPrompt` 与 `PromptObserver` 结合使用。其`acceptanceFuture()`和`completionFuture()`视图分别公开了守护进程准入和可靠的转向终端。当请求已解决或不再待处理时，`respondToPermission()` 返回 `false`。取消未来视图并不会取消守护进程提示符；使用 `cancelActivePrompt()` 进行会话级守护程序取消操作，并仍然等待匹配的终端。合作取消以`turn_complete`和`stopReason=cancelled`完成； `promptText()` 返回其 `PromptTextResult`，因此区分取消的调用者必须检查 `result.getTerminal().getStopReason()`。如果 agent 或提供者在取消时失败，守护进程可以改为发布 `turn_error`，这使得 `promptText()` 抛出 `PromptTurnException`。

当取消、截止日期、拆卸或 agent 结算竞赛时，守护进程的一次性闩锁会发布第一个正式终端并抑制后来的候选者。始终在接收终端本身上分支；客户端发送的最后一个控制突变无法确定终端类型或错误代码。

SSE 传输发送 `Accept-Encoding: identity` 和 `Last-Event-ID`，验证帧和事件 ID，删除重复数据，并仅重新连接 SSE GET。提示和其他突变请求永远不会自动重试。 HTTP 408 和 5xx 对提示准入、会话创建、许可、取消、心跳、分离或删除的响应被报告为结果未知，因为它们不能证明守护进程拒绝了突变。有限响应机构和SSE观察具有独立的截止日期。

在此 alpha 中，Java 守护进程 SDK API 故意不公开创建时模型选择。守护进程仅将被拒绝的 `modelServiceId` 报告为在创建响应之前发出的 SSE 事件，而此 SDK 从稍后的提示准入水印中打开其流。在守护程序返回明确的创建结果或 SDK 拥有来自 `Last-Event-ID: 0` 的单独会话事件订阅之前，请使用守护程序配置的默认模型。

`PromptRequest.Builder.deadline(Duration)` 请求守护程序强制提示截止日期，并且仅当守护程序通告 `prompt_absolute_deadline` 时才被接受；否则在发送提示之前SDK会失败。该值必须介于 1 和 2,147,483,647 毫秒之间，与守护程序的节点计时器范围相匹配。这与 `observationTimeout(Duration)` 是分开的，它仅限制本地 SSE 观察，并且从不发送取消突变。

在创建会话之前，SDK需要守护进程通告REST传输和`session_scope_override`；这可以防止较旧的守护进程默默地忽略请求的 `thread` 范围并将客户端附加到共享会话。当调用者提供会话 ID 时，SDK 在发送突变之前还需要 `session_id_override`。当通告 `client_heartbeat` 时，打开的会话每分钟发送一次新的心跳，因此守护进程不会获取其他空闲的客户端。在 `DaemonClient` 构建器上设置 `heartbeatInterval(Duration.ZERO)` 以禁用此行为，或选择不同的正区间。心跳永远不会重试；下一个计划的心跳是单独的保活。默认情况下，提示观察仅限于每个客户端的 32 并发提示，并且可以使用 `maximumConcurrentPrompts` 进行调整。入场和航站楼未来回调远离运输工人；仍然被阻止的回调会消耗有限的发布容量。 SSE 流清理也是有界的，并且保持阻塞状态的关闭会保留其清理预留。任何一种情况都可能导致稍后的 `startPrompt` 失败并显示 `DaemonClientCapacityException`，而不是放弃超时关闭或无限增长线程和排队工作。

不确定的完成是结果边界，而不是会话重用边界。在 `PromptAdmissionUnknownException` 或 `PromptOutcomeIndeterminateException` 之后，即使本地流清理稍后成功，`DaemonSessionClient` 也会永久拒绝进一步的提示；而是关闭或销毁会话。发布观察超时，而无需永远等待阻塞流关闭，而清理则异步继续，并保留有限的客户端容量，直到完成。

## 旧版 stdio API

现有的 `com.alibaba.qwen.code.cli` API 仍然可用：

```java
public static void runSimpleExample() {
    List<String> result = QwenCodeCli.simpleQuery("hello world");
    result.forEach(logger::info);
}
```

对于自定义传输选项的更高级用法：

```java
public static void runTransportOptionsExample() {
    TransportOptions options = new TransportOptions()
            .setModel("qwen3-coder-flash")
            .setPermissionMode(PermissionMode.AUTO_EDIT)
            .setCwd("./")
            .setEnv(new HashMap<String, String>() {{put("CUSTOM_VAR", "value");}})
            .setIncludePartialMessages(true)
            .setTurnTimeout(new Timeout(120L, TimeUnit.SECONDS))
            .setMessageTimeout(new Timeout(90L, TimeUnit.SECONDS))
            .setAllowedTools(Arrays.asList("read_file", "write_file", "glob"));

    List<String> result = QwenCodeCli.simpleQuery("who are you, what are your capabilities?", options);
    result.forEach(logger::info);
}
```

对于自定义内容消费者的流内容处理：

```java
public static void runStreamingExample() {
    QwenCodeCli.simpleQuery("who are you, what are your capabilities?",
            new TransportOptions().setMessageTimeout(new Timeout(10L, TimeUnit.SECONDS)), new AssistantContentSimpleConsumers() {

                @Override
                public void onText(Session session, TextAssistantContent textAssistantContent) {
                    logger.info("Text content received: {}", textAssistantContent.getText());
                }

                @Override
                public void onThinking(Session session, ThinkingAssistantContent thinkingAssistantContent) {
                    logger.info("Thinking content received: {}", thinkingAssistantContent.getThinking());
                }

                @Override
                public void onToolUse(Session session, ToolUseAssistantContent toolUseContent) {
                    logger.info("Tool use content received: {} with arguments: {}",
                            toolUseContent, toolUseContent.getInput());
                }

                @Override
                public void onToolResult(Session session, ToolResultAssistantContent toolResultContent) {
                    logger.info("Tool result content received: {}", toolResultContent.getContent());
                }

                @Override
                public void onOtherContent(Session session, AssistantContent<?> other) {
                    logger.info("Other content received: {}", other);
                }

                @Override
                public void onUsage(Session session, AssistantUsage assistantUsage) {
                    logger.info("Usage information received: Input tokens: {}, Output tokens: {}",
                            assistantUsage.getUsage().getInputTokens(), assistantUsage.getUsage().getOutputTokens());
                }
            }.setDefaultPermissionOperation(Operation.allow));
    logger.info("Streaming example completed.");
}
```

其他示例参见src/test/java/com/alibaba/qwen/code/cli/example

## Java 11 迁移和 alpha 限制

`0.1.0-alpha` 将整个工件的最低 Java 版本从 8 提高到 11。 Java 8 应用程序必须保留在 `0.0.3-alpha` 上。 Logback 不再是运行时依赖项；添加您的应用程序使用的 SLF4J 提供程序。

当这个 alpha 无法证明是提示终端时，它会故意失败关闭。它不保证跨守护程序重新启动、自动纪元恢复、snapshot/resync、持久游标或真正的提示-ID-目标取消的精确一次执行。 `prompt_cancelled` 和队列事件是建议性的；只有匹配的 `turn_complete` 和 `turn_error` 才是终端。

如果会话创建的传输结果不明确，守护程序可能会保留 ID 从未到达调用者的会话。 SDK 不会重试创建，也无法分离该未知会话；守护进程端生命周期收获是恢复边界。

## 建筑

该工件包含两个独立的实现：

- **守护进程API**：`DaemonClient`和`DaemonSessionClient`使用REST突变加上可恢复SSE和自己的有界HTTP、提示、维护和计时器资源。
- **旧 stdio API**：`QwenCodeCli`、`Session` 和 `ProcessTransport` 使用现有的 CLI 协议 DTO 和实用程序管理子 CLI 进程。

守护进程实现不会重用遗留进程传输、会话模型、DTO 或全局执行器。

## 传统录音室功能

### 权限模式

SDK支持不同的权限模式来控制工具执行：

- **`default`**：除非通过`canUseTool`回调或在`allowedTools`中批准，否则写入工具将被拒绝。只读工具无需确认即可执行。
- **`plan`**：阻止所有写入工具，指示AI先提出计划。
- **`auto-edit`**：自动批准编辑工具（`edit`、`write_file`、`notebook_edit`），而其他工具需要确认。
- **`yolo`**：所有工具自动执行，无需确认。

### 会话事件消费者和助理内容消费者

SDK 提供了两个关键接口来处理来自 CLI 的事件和内容：

#### SessionEventConsumers 接口

`SessionEventConsumers`接口提供会话期间不同类型消息的回调：

- `onSystemMessage`：处理来自CLI的系统消息（接收Session和SDKSystemMessage）
- `onResultMessage`：处理来自CLI的结果消息（接收Session和SDKResultMessage）
- `onAssistantMessage`：处理助手消息（AI响应）（接收Session和SDKAssistantMessage）
- `onPartialAssistantMessage`：流媒体期间处理部分助手消息（接收Session和SDKPartialAssistantMessage）
- `onUserMessage`：处理用户消息（接收Session和SDKUserMessage）
- `onOtherMessage`：处理其他类型的消息（接收Session和String消息）
- `onControlResponse`：处理控制响应（接收Session和CLIControlResponse）
- `onControlRequest`：处理控制请求（接收Session和CLIControlRequest，返回CLIControlResponse）
- `onPermissionRequest`：处理权限请求（接收Session和CLIControlRequest<CLIControlPermissionRequest>，返回Behaviour）

#### AssistantContentConsumers 接口

`AssistantContentConsumers` 接口处理助理消息中不同类型的内容：

- `onText`：处理文本内容（接收Session和TextAssistantContent）
- `onThinking`：处理思考内容（接收Session和ThinkingAssistantContent）
- `onToolUse`：处理工具使用内容（接收Session和ToolUseAssistantContent）
- `onToolResult`：处理工具结果内容（接收Session和ToolResultAssistantContent）
- `onOtherContent`：处理其他内容类型（接收Session和AssistantContent）
- `onUsage`：处理使用信息（接收Session和AssistantUsage）
- `onPermissionRequest`：处理权限请求（接收Session和CLIControlPermissionRequest，返回Behavior）
- `onOtherControlRequest`：处理其他控制请求（接收Session和ControlRequestPayload，返回ControlResponsePayload）

#### 接口关系

**有关事件层次结构的重要说明：**

- `SessionEventConsumers` 是 ** 高级 ** 事件处理器，用于处理不同的消息类型（系统、助手、用户等）
- `AssistantContentConsumers` 是 ** 低级 ** 内容处理器，用于处理助理消息中不同类型的内容（文本、工具、思维等）

**处理器关系：**

- `SessionEventConsumers` → `AssistantContentConsumers`（SessionEventConsumers 使用 AssistantContentConsumers 处理助理消息中的内容）

**事件派生关系：**

- `onAssistantMessage` → `onText`, `onThinking`, `onToolUse`, `onToolResult`, `onOtherContent`, `onUsage`
- `onPartialAssistantMessage` → `onText`, `onThinking`, `onToolUse`, `onToolResult`, `onOtherContent`
- `onControlRequest` → `onPermissionRequest`, `onOtherControlRequest`

**事件超时关系：**

每个事件处理程序方法都有一个相应的超时方法，允许自定义该特定事件的超时行为：

- `onSystemMessage` ↔ `onSystemMessageTimeout`
- `onResultMessage` ↔ `onResultMessageTimeout`
- `onAssistantMessage` ↔ `onAssistantMessageTimeout`
- `onPartialAssistantMessage` ↔ `onPartialAssistantMessageTimeout`
- `onUserMessage` ↔ `onUserMessageTimeout`
- `onOtherMessage` ↔ `onOtherMessageTimeout`
- `onControlResponse` ↔ `onControlResponseTimeout`
- `onControlRequest` ↔ `onControlRequestTimeout`

对于 AssistantContentConsumers 超时方法：

- `onText` ↔ `onTextTimeout`
- `onThinking` ↔ `onThinkingTimeout`
- `onToolUse` ↔ `onToolUseTimeout`
- `onToolResult` ↔ `onToolResultTimeout`
- `onOtherContent` ↔ `onOtherContentTimeout`
- `onPermissionRequest` ↔ `onPermissionRequestTimeout`
- `onOtherControlRequest` ↔ `onOtherControlRequestTimeout`

**默认超时值：**

- `SessionEventSimpleConsumers`默认超时：180秒（超时。TIMEOUT_180_SECONDS）
- `AssistantContentSimpleConsumers`默认超时：60秒（超时。TIMEOUT_60_SECONDS）

**超时层次结构要求：**

为了正确操作，应保持以下超时关系：

- `onAssistantMessageTimeout`返回值应大于`onTextTimeout`、`onThinkingTimeout`、`onToolUseTimeout`、`onToolResultTimeout`和`onOtherContentTimeout`返回值
- `onControlRequestTimeout`返回值应大于`onPermissionRequestTimeout`和`onOtherControlRequestTimeout`返回值

### 交通选择

`TransportOptions` 类允许配置 SDK 如何与 Qwen Code CLI 通信：

- `pathToQwenExecutable`：Qwen Code CLI 可执行文件的路径
- `cwd`：CLI进程的工作目录
- `model`：用于会话的AI模型
- `permissionMode`：控制工具执行的权限模式
- `env`：传递给CLI进程的环境变量
- `maxSessionTurns`：限制会话中的对话轮数
- `coreTools`：AI 应该可用的核心工具列表
- `excludeTools`：要排除 AI 可用的工具列表
- `allowedTools`：预先批准使用而无需额外确认的工具列表
- `authType`：会话使用的身份验证类型
- `includePartialMessages`：允许在流响应期间接收部分消息
- `turnTimeout`：完整一轮对话超时
- `messageTimeout`：回合内单个消息超时
- `resumeSessionId`：要恢复的上一个会话的ID
- `otherOptions`：传递给CLI的附加命令行选项

### 会话控制功能

- **会话创建**：使用`QwenCodeCli.newSession()`创建带有自定义选项的新会话
- **会话管理**：`Session`类提供发送提示、处理响应和管理会话状态的方法
- **会话清理**：始终使用`session.close()`关闭会话以正确终止CLI进程
- **会话恢复**：使用`TransportOptions`中的`setResumeSessionId()`恢复上一个会话
- **会话中断**：使用`session.interrupt()`中断当前正在运行的提示符
- **动态模型切换**：使用`session.setModel()`在会话期间更改模型
- **动态权限模式切换**：使用`session.setPermissionMode()`在会话过程中更改权限模式

### 线程池配置

SDK使用线程池来管理并发操作，默认配置如下：

- **核心池大小**：30线程
- **最大池大小**：100线程
- **保持活动时间**：60秒
- **队列容量**：300任务（使用LinkedBlockingQueue）
- **线程命名**：“qwen_code_cli-池-{number}”
- **守护线程**：true
- **拒绝执行处理程序**：CallerRunsPolicy

当 JVM 退出时，仍在默认池中运行或排队的任务将被放弃。需要完成的调用者必须明确等待这些任务。

## 错误处理

SDK针对不同的错误场景提供了特定的异常类型：

- `SessionControlException`：当会话控制出现问题时抛出，包括尝试使用已关闭或不可用的会话。会话构建和`start()`可以直接抛出； `QwenCodeCli.newSession()` 将较低级别的创建和初始化失败包装在 `RuntimeException` 中。
- `SessionSendPromptException`：发送提示或接收响应时出现问题时抛出

## FAQ / 故障排除

### 问：我需要单独安装【Qwen】【CLI】吗？

答：是的。守护进程API需要兼容的`qwen serve`；旧版 stdio
API 需要 qwen 代码 0.5.0 或更高版本。

### 问：支持哪些Java版本？

答：`0.1.0-alpha` 需要 Java 11 或更高版本。 Java 8 用户必须保持在 `0.0.3-alpha` 上。

### 问：如何处理长时间运行的请求？

答：SDK 包括超时实用程序。您可以使用`TransportOptions`中的`Timeout`类配置超时。

### 问：为什么有些工具无法执行？

答：这可能是由于权限模式造成的。检查您的权限模式设置并考虑使用`allowedTools`来预先批准某些工具。

### 问：如何恢复之前的会话？

A：使用`TransportOptions`中的`setResumeSessionId()`方法恢复之前的会话。

### 问：我可以自定义CLI进程的环境吗？

A：可以，使用`TransportOptions`中的`setEnv()`方法将环境变量传递给CLI进程。

## 许可证

Apache-2.0 - 有关详细信息，请参阅 [LICENSE](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/LICENSE>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Qwen Code Java SDK

The Qwen Code Java SDK provides a recommended daemon transport for `qwen serve` and retains the experimental legacy stdio API for compatibility. Both APIs ship in the same `com.alibaba:qwencode-sdk` artifact.

## Requirements

- Java >= 11 for `0.1.0-alpha`
- Maven >= 3.9.2 when building or publishing this SDK from source
- A compatible `qwen serve` for the daemon API, or qwen-code >= 0.5.0 for the legacy stdio API

### Dependencies

- **Logging API**: org.slf4j:slf4j-api (choose an SLF4J provider in your application)
- **Utilities**: org.apache.commons:commons-lang3
- **JSON Processing**: Fastjson2 for encoding and Jackson Core for strict decoding
- **Testing**: JUnit 5 (org.junit.jupiter:junit-jupiter)

## Installation

Add the following dependency to your Maven `pom.xml`:

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>qwencode-sdk</artifactId>
    <version>0.1.0-alpha</version>
</dependency>
```

Or if using Gradle, add to your `build.gradle`:

```gradle
implementation 'com.alibaba:qwencode-sdk:0.1.0-alpha'
```

## Building and Running

### Build Commands

```bash
# Compile the project
mvn compile

# Run tests
mvn test

# Package the JAR
mvn package

# Install to local repository
mvn install
```

### Real daemon E2E from source

Run the real-daemon Java integration tests from the repository root after building both the workspaces and the root CLI bundle:

```bash
npm run build
npm run bundle
npx tsx scripts/run-java-daemon-sdk-e2e.ts
```

`npm run build` alone does not refresh `dist/cli.js`; the E2E harness launches that bundle and fails with an explicit prerequisite error when it is missing.

## Recommended daemon API

Start `qwen serve`, then create an independent thread-scoped session. `promptText` returns only after a matching `turn_complete`; incomplete streams fail with `PromptOutcomeIndeterminateException` rather than returning partial text as success.

For the lifecycle guarantees assumed by `0.1.0-alpha`, use the qwen-code build released from the same source revision as the SDK. The daemon must contain the idempotent per-client detach ledger from [#7386](<https://github.com/QwenLM/qwen-code/pull/7386>), the per-epoch terminal guarantee from [#7400](<https://github.com/QwenLM/qwen-code/pull/7400>), and this release's acknowledged admission cancellation plus FIFO cancel-drain fence. The #7400 commit alone is not sufficient: a same-wire daemon can acknowledge cancel before agent dispatch without stopping the admitted prompt, or let an unacknowledged session-scoped cancel reach a queued successor. The bundled ACP child uses one acknowledged admission-aware cancellation handshake; a custom standards-compliant ACP child without that extension receives one standard `session/cancel` notification. Feature negotiation cannot distinguish older same-wire daemon builds, so the SDK fails closed rather than reporting partial output as success.

The bundled cancellation handshake deliberately waits for the targeted prompt call to settle before the daemon dispatches its queued successor. It has no timeout that merely acknowledges cancellation: doing so could let a late session-scoped cancel reach the next prompt. If a provider, tool, or custom integration ignores its `AbortSignal` indefinitely, the cancel mutation can therefore remain outcome-unknown and that session must not be reused. Treat a formal prompt terminal received within the caller's observation boundary as authoritative; otherwise close or destroy the session after observation fails. Recovering a wedged shared ACP child without disturbing its sibling sessions requires stronger runtime isolation and is outside this alpha contract.

```java
import com.alibaba.qwen.code.daemon.DaemonClient;
import com.alibaba.qwen.code.daemon.DaemonSessionClient;
import com.alibaba.qwen.code.daemon.PromptTextResult;
import java.net.URI;

try (DaemonClient daemon = DaemonClient.builder()
        .baseUri(URI.create("http://127.0.0.1:4170"))
        .build();
     DaemonSessionClient session = daemon.createSession()) {
    PromptTextResult result = session.promptText("Explain this repository");
    System.out.println(result.getText());
}
```

Callers that need to allocate the session identity before creation can pass an RFC UUID v1-v5. The SDK checks `session_id_override` before the mutation and reports a different returned ID as `SessionCreationOutcomeUnknownException`:

```java
CreateSessionRequest request = CreateSessionRequest.builder()
        .sessionId("550E8400-E29B-41D4-A716-446655440000")
        .build();

try (DaemonSessionClient session = daemon.createSession(request)) {
    System.out.println(session.getSession().getSessionId());
}
```

The daemon normalizes the ID to lowercase and creates a new thread session. This is not an idempotent attach; after an ambiguous create outcome, recover with the known ID rather than retrying creation.

If `qwen serve` requires authentication, add
`.bearerToken(System.getenv("QWEN_SERVER_TOKEN"))` to the `DaemonClient`
builder. The SDK sends the bearer on REST and SSE requests and never puts it in
the URL.

Use `startPrompt` with a `PromptObserver` when you need ordered text, thought, tool, usage, permission, and raw event callbacks. Its `acceptanceFuture()` and `completionFuture()` views separately expose daemon admission and the reliable turn terminal. `respondToPermission()` returns `false` when the request was already resolved or no longer pending. Cancelling the future views does not cancel the daemon prompt; use `cancelActivePrompt()` for the session-level daemon cancel operation and still wait for the matching terminal. A cooperative cancellation completes with `turn_complete` and `stopReason=cancelled`; `promptText()` returns its `PromptTextResult`, so callers that distinguish cancellation must inspect `result.getTerminal().getStopReason()`. If the agent or provider fails while cancelling, the daemon can instead publish `turn_error`, which makes `promptText()` throw `PromptTurnException`.

When cancellation, deadline, teardown, or agent settlement race, the daemon's exactly-once latch publishes the first formal terminal and suppresses later candidates. Always branch on the received terminal itself; the last control mutation sent by the client does not determine the terminal kind or error code.

The SSE transport sends `Accept-Encoding: identity` and `Last-Event-ID`, validates framing and event IDs, deduplicates replay, and reconnects only the SSE GET. Prompt and other mutation requests are never retried automatically. HTTP 408 and 5xx responses to prompt admission, session creation, permission, cancel, heartbeat, detach, or delete are reported as outcome-unknown because they do not prove that the daemon rejected the mutation. Finite response bodies and SSE observation have independent deadlines.

Creation-time model selection is intentionally not exposed by the Java daemon SDK API in this alpha. The daemon reports a rejected `modelServiceId` only as an SSE event emitted before the create response, while this SDK opens its stream from the later prompt-admission watermark. Until the daemon returns a definitive create result or the SDK owns a separate session-event subscription from `Last-Event-ID: 0`, use the daemon's configured default model.

`PromptRequest.Builder.deadline(Duration)` requests a daemon-enforced prompt deadline and is accepted only when the daemon advertises `prompt_absolute_deadline`; otherwise the SDK fails before sending the prompt. The value must be between 1 and 2,147,483,647 milliseconds, matching the daemon's Node timer range. This is separate from `observationTimeout(Duration)`, which only bounds local SSE observation and never sends a cancel mutation.

Before creating a session, the SDK requires the daemon to advertise the REST transport and `session_scope_override`; this prevents an older daemon from silently ignoring the requested `thread` scope and attaching the client to a shared session. When a caller supplies a session ID, the SDK additionally requires `session_id_override` before sending the mutation. When `client_heartbeat` is advertised, an open session sends a fresh heartbeat every minute so the daemon does not reap an otherwise idle client. Set `heartbeatInterval(Duration.ZERO)` on the `DaemonClient` builder to disable this behavior, or choose a different positive interval. A heartbeat is never retried; the next scheduled heartbeat is a separate keepalive. Prompt observation is bounded to 32 concurrent prompts per client by default and can be adjusted with `maximumConcurrentPrompts`. Admission and terminal future callbacks run away from transport workers; callbacks that remain blocked consume bounded publication capacity. SSE stream cleanup is also bounded, and a close that remains blocked retains its cleanup reservation. Either condition can cause a later `startPrompt` to fail with `DaemonClientCapacityException` rather than dropping a timeout close or growing threads and queued work without limit.

An indeterminate completion is an outcome boundary, not a session-reuse boundary. After `PromptAdmissionUnknownException` or `PromptOutcomeIndeterminateException`, that `DaemonSessionClient` permanently rejects further prompts even if local stream cleanup later succeeds; close or destroy the session instead. An observation timeout is published without waiting forever for a blocked stream close, while cleanup continues asynchronously and retains bounded client capacity until it finishes.

## Legacy stdio API

The existing `com.alibaba.qwen.code.cli` API remains available:

```java
public static void runSimpleExample() {
    List<String> result = QwenCodeCli.simpleQuery("hello world");
    result.forEach(logger::info);
}
```

For more advanced usage with custom transport options:

```java
public static void runTransportOptionsExample() {
    TransportOptions options = new TransportOptions()
            .setModel("qwen3-coder-flash")
            .setPermissionMode(PermissionMode.AUTO_EDIT)
            .setCwd("./")
            .setEnv(new HashMap<String, String>() {{put("CUSTOM_VAR", "value");}})
            .setIncludePartialMessages(true)
            .setTurnTimeout(new Timeout(120L, TimeUnit.SECONDS))
            .setMessageTimeout(new Timeout(90L, TimeUnit.SECONDS))
            .setAllowedTools(Arrays.asList("read_file", "write_file", "glob"));

    List<String> result = QwenCodeCli.simpleQuery("who are you, what are your capabilities?", options);
    result.forEach(logger::info);
}
```

For streaming content handling with custom content consumers:

```java
public static void runStreamingExample() {
    QwenCodeCli.simpleQuery("who are you, what are your capabilities?",
            new TransportOptions().setMessageTimeout(new Timeout(10L, TimeUnit.SECONDS)), new AssistantContentSimpleConsumers() {

                @Override
                public void onText(Session session, TextAssistantContent textAssistantContent) {
                    logger.info("Text content received: {}", textAssistantContent.getText());
                }

                @Override
                public void onThinking(Session session, ThinkingAssistantContent thinkingAssistantContent) {
                    logger.info("Thinking content received: {}", thinkingAssistantContent.getThinking());
                }

                @Override
                public void onToolUse(Session session, ToolUseAssistantContent toolUseContent) {
                    logger.info("Tool use content received: {} with arguments: {}",
                            toolUseContent, toolUseContent.getInput());
                }

                @Override
                public void onToolResult(Session session, ToolResultAssistantContent toolResultContent) {
                    logger.info("Tool result content received: {}", toolResultContent.getContent());
                }

                @Override
                public void onOtherContent(Session session, AssistantContent<?> other) {
                    logger.info("Other content received: {}", other);
                }

                @Override
                public void onUsage(Session session, AssistantUsage assistantUsage) {
                    logger.info("Usage information received: Input tokens: {}, Output tokens: {}",
                            assistantUsage.getUsage().getInputTokens(), assistantUsage.getUsage().getOutputTokens());
                }
            }.setDefaultPermissionOperation(Operation.allow));
    logger.info("Streaming example completed.");
}
```

other examples see src/test/java/com/alibaba/qwen/code/cli/example

## Java 11 migration and alpha limits

`0.1.0-alpha` raises the minimum Java version for the whole artifact from 8 to 11. Java 8 applications must remain on `0.0.3-alpha`. Logback is no longer a runtime dependency; add the SLF4J provider your application uses.

This alpha deliberately fails closed when it cannot prove a prompt terminal. It does not guarantee exactly-once execution across daemon restarts, automatic epoch recovery, snapshot/resync, persisted cursors, or true prompt-ID-targeted cancellation. `prompt_cancelled` and queue events are advisory; only matching `turn_complete` and `turn_error` are terminal.

If session creation has an ambiguous transport outcome, the daemon may retain a session whose ID never reached the caller. The SDK does not retry creation and cannot detach that unknown session; daemon-side lifecycle reaping is the recovery boundary.

## Architecture

The artifact contains two isolated implementations:

- **Daemon API**: `DaemonClient` and `DaemonSessionClient` use REST mutations plus resumable SSE and own bounded HTTP, prompt, maintenance, and timer resources.
- **Legacy stdio API**: `QwenCodeCli`, `Session`, and `ProcessTransport` manage a child CLI process using the existing CLI protocol DTOs and utilities.

The daemon implementation does not reuse the legacy process transport, session model, DTOs, or global executor.

## Legacy stdio features

### Permission Modes

The SDK supports different permission modes for controlling tool execution:

- **`default`**: Write tools are denied unless approved via `canUseTool` callback or in `allowedTools`. Read-only tools execute without confirmation.
- **`plan`**: Blocks all write tools, instructing AI to present a plan first.
- **`auto-edit`**: Auto-approve edit tools (`edit`, `write_file`, `notebook_edit`) while other tools require confirmation.
- **`yolo`**: All tools execute automatically without confirmation.

### Session Event Consumers and Assistant Content Consumers

The SDK provides two key interfaces for handling events and content from the CLI:

#### SessionEventConsumers Interface

The `SessionEventConsumers` interface provides callbacks for different types of messages during a session:

- `onSystemMessage`: Handles system messages from the CLI (receives Session and SDKSystemMessage)
- `onResultMessage`: Handles result messages from the CLI (receives Session and SDKResultMessage)
- `onAssistantMessage`: Handles assistant messages (AI responses) (receives Session and SDKAssistantMessage)
- `onPartialAssistantMessage`: Handles partial assistant messages during streaming (receives Session and SDKPartialAssistantMessage)
- `onUserMessage`: Handles user messages (receives Session and SDKUserMessage)
- `onOtherMessage`: Handles other types of messages (receives Session and String message)
- `onControlResponse`: Handles control responses (receives Session and CLIControlResponse)
- `onControlRequest`: Handles control requests (receives Session and CLIControlRequest, returns CLIControlResponse)
- `onPermissionRequest`: Handles permission requests (receives Session and CLIControlRequest<CLIControlPermissionRequest>, returns Behavior)

#### AssistantContentConsumers Interface

The `AssistantContentConsumers` interface handles different types of content within assistant messages:

- `onText`: Handles text content (receives Session and TextAssistantContent)
- `onThinking`: Handles thinking content (receives Session and ThinkingAssistantContent)
- `onToolUse`: Handles tool use content (receives Session and ToolUseAssistantContent)
- `onToolResult`: Handles tool result content (receives Session and ToolResultAssistantContent)
- `onOtherContent`: Handles other content types (receives Session and AssistantContent)
- `onUsage`: Handles usage information (receives Session and AssistantUsage)
- `onPermissionRequest`: Handles permission requests (receives Session and CLIControlPermissionRequest, returns Behavior)
- `onOtherControlRequest`: Handles other control requests (receives Session and ControlRequestPayload, returns ControlResponsePayload)

#### Relationship Between the Interfaces

**Important Note on Event Hierarchy:**

- `SessionEventConsumers` is the **high-level** event processor that handles different message types (system, assistant, user, etc.)
- `AssistantContentConsumers` is the **low-level** content processor that handles different types of content within assistant messages (text, tools, thinking, etc.)

**Processor Relationship:**

- `SessionEventConsumers` → `AssistantContentConsumers` (SessionEventConsumers uses AssistantContentConsumers to process content within assistant messages)

**Event Derivation Relationships:**

- `onAssistantMessage` → `onText`, `onThinking`, `onToolUse`, `onToolResult`, `onOtherContent`, `onUsage`
- `onPartialAssistantMessage` → `onText`, `onThinking`, `onToolUse`, `onToolResult`, `onOtherContent`
- `onControlRequest` → `onPermissionRequest`, `onOtherControlRequest`

**Event Timeout Relationships:**

Each event handler method has a corresponding timeout method that allows customizing the timeout behavior for that specific event:

- `onSystemMessage` ↔ `onSystemMessageTimeout`
- `onResultMessage` ↔ `onResultMessageTimeout`
- `onAssistantMessage` ↔ `onAssistantMessageTimeout`
- `onPartialAssistantMessage` ↔ `onPartialAssistantMessageTimeout`
- `onUserMessage` ↔ `onUserMessageTimeout`
- `onOtherMessage` ↔ `onOtherMessageTimeout`
- `onControlResponse` ↔ `onControlResponseTimeout`
- `onControlRequest` ↔ `onControlRequestTimeout`

For AssistantContentConsumers timeout methods:

- `onText` ↔ `onTextTimeout`
- `onThinking` ↔ `onThinkingTimeout`
- `onToolUse` ↔ `onToolUseTimeout`
- `onToolResult` ↔ `onToolResultTimeout`
- `onOtherContent` ↔ `onOtherContentTimeout`
- `onPermissionRequest` ↔ `onPermissionRequestTimeout`
- `onOtherControlRequest` ↔ `onOtherControlRequestTimeout`

**Default Timeout Values:**

- `SessionEventSimpleConsumers` default timeout: 180 seconds (Timeout.TIMEOUT_180_SECONDS)
- `AssistantContentSimpleConsumers` default timeout: 60 seconds (Timeout.TIMEOUT_60_SECONDS)

**Timeout Hierarchy Requirements:**

For proper operation, the following timeout relationships should be maintained:

- `onAssistantMessageTimeout` return value should be greater than `onTextTimeout`, `onThinkingTimeout`, `onToolUseTimeout`, `onToolResultTimeout`, and `onOtherContentTimeout` return values
- `onControlRequestTimeout` return value should be greater than `onPermissionRequestTimeout` and `onOtherControlRequestTimeout` return values

### Transport Options

The `TransportOptions` class allows configuration of how the SDK communicates with the Qwen Code CLI:

- `pathToQwenExecutable`: Path to the Qwen Code CLI executable
- `cwd`: Working directory for the CLI process
- `model`: AI model to use for the session
- `permissionMode`: Permission mode that controls tool execution
- `env`: Environment variables to pass to the CLI process
- `maxSessionTurns`: Limits the number of conversation turns in a session
- `coreTools`: List of core tools that should be available to the AI
- `excludeTools`: List of tools to exclude from being available to the AI
- `allowedTools`: List of tools that are pre-approved for use without additional confirmation
- `authType`: Authentication type to use for the session
- `includePartialMessages`: Enables receiving partial messages during streaming responses
- `turnTimeout`: Timeout for a complete turn of conversation
- `messageTimeout`: Timeout for individual messages within a turn
- `resumeSessionId`: ID of a previous session to resume
- `otherOptions`: Additional command-line options to pass to the CLI

### Session Control Features

- **Session creation**: Use `QwenCodeCli.newSession()` to create a new session with custom options
- **Session management**: The `Session` class provides methods to send prompts, handle responses, and manage session state
- **Session cleanup**: Always close sessions using `session.close()` to properly terminate the CLI process
- **Session resumption**: Use `setResumeSessionId()` in `TransportOptions` to resume a previous session
- **Session interruption**: Use `session.interrupt()` to interrupt a currently running prompt
- **Dynamic model switching**: Use `session.setModel()` to change the model during a session
- **Dynamic permission mode switching**: Use `session.setPermissionMode()` to change the permission mode during a session

### Thread Pool Configuration

The SDK uses a thread pool for managing concurrent operations with the following default configuration:

- **Core Pool Size**: 30 threads
- **Maximum Pool Size**: 100 threads
- **Keep-Alive Time**: 60 seconds
- **Queue Capacity**: 300 tasks (using LinkedBlockingQueue)
- **Thread Naming**: "qwen_code_cli-pool-{number}"
- **Daemon Threads**: true
- **Rejected Execution Handler**: CallerRunsPolicy

Tasks still running or queued in the default pool are abandoned when the JVM exits. Callers that require completion must wait for those tasks explicitly.

## Error Handling

The SDK provides specific exception types for different error scenarios:

- `SessionControlException`: Thrown when there's an issue with session control, including attempting to use a closed or unavailable session. Session construction and `start()` can throw it directly; `QwenCodeCli.newSession()` wraps lower-level creation and initialization failures in a `RuntimeException`.
- `SessionSendPromptException`: Thrown when there's an issue sending a prompt or receiving a response

## FAQ / Troubleshooting

### Q: Do I need to install the Qwen CLI separately?

A: Yes. The daemon API requires a compatible `qwen serve`; the legacy stdio
API requires qwen-code 0.5.0 or higher.

### Q: What Java versions are supported?

A: `0.1.0-alpha` requires Java 11 or higher. Java 8 users must remain on `0.0.3-alpha`.

### Q: How do I handle long-running requests?

A: The SDK includes timeout utilities. You can configure timeouts using the `Timeout` class in `TransportOptions`.

### Q: Why are some tools not executing?

A: This is likely due to permission modes. Check your permission mode settings and consider using `allowedTools` to pre-approve certain tools.

### Q: How do I resume a previous session?

A: Use the `setResumeSessionId()` method in `TransportOptions` to resume a previous session.

### Q: Can I customize the environment for the CLI process?

A: Yes, use the `setEnv()` method in `TransportOptions` to pass environment variables to the CLI process.

## License

Apache-2.0 - see [LICENSE](<https://github.com/QwenLM/qwen-code/blob/7e3de59d3aba5cab80767ac5b121c0b3b4e35267/LICENSE>) for details.


</details>
