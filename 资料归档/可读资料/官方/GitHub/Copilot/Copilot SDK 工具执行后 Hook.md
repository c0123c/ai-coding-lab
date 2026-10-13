# Copilot SDK 工具执行后 Hook

说明成功调用工具后的输入输出接口，并提供日志、敏感信息处理、结果截断和补充上下文等样例。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-20](<https://github.com/github/docs/commit/cf960fc42d0894ea600cb10548e681ab141acaf4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/hooks/post-tool-use.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 工具执行后 Hook.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Post-tool use hook
shortTitle: Post Tool Use
intro: >-
  The `onPostToolUse` hook is called **after** a tool executes **successfully**.
  Use it to:
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-hooks/post-tool-use
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

* 转换或过滤工具结果
* 用于审计的日志工具执行情况
* 根据结果添加上下文
* 抑制对话结果

> **失败变体** — `onPostToolUse` 仅在成功执行工具时触发。观察 **失败了** 工具调用、注册 `onPostToolUseFailure` (`on_post_tool_use_failure` 在 Python, `OnPostToolUseFailure` 在 Go/.NET, `on_post_tool_use_failure` 在 Rust）。处理程序接收 `{ sessionId, toolName, toolArgs, error, timestamp, workingDirectory }` — 的 `error` 字段是从工具的失败结果中提取的字符串 - 并且可能返回 `{ additionalContext: string }` 为模型注入额外的指导（例如重试提示）。请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/hooks-overview>) 获取完整列表。
> <a id="failure-variant"></a>

## 钩签名

{% codetabs %}
{% codetab typescript %}

```typescript
type PostToolUseHandler = (
  input: PostToolUseHookInput,
  invocation: HookInvocation,
) => Promise<PostToolUseHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
PostToolUseHandler = Callable[
    [PostToolUseHookInput, dict[str, str]],
    Awaitable[PostToolUseHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type PostToolUseHandler func(
    input PostToolUseHookInput,
    invocation HookInvocation,
) (*PostToolUseHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<PostToolUseHookOutput?> PostToolUseHandler(
    PostToolUseHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface PostToolUseHandler {
    CompletableFuture<PostToolUseHookOutput> handle(
        PostToolUseHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

## 输入

|领域|类型|描述|
| ------------------ | ------------------ | -------------------------------------- |
| `timestamp`        |SDK 时间戳类型|当钩子被触发时|
| `workingDirectory` |字符串|当前工作目录|
| `toolName`         |字符串|调用的工具的名称|
| `toolArgs`         |对象|传递给工具的参数|
| `toolResult`       |对象|工具返回的结果|

## 输出

返回`null`或`undefined`以不变地传递结果。否则，返回具有以下任意字段的对象：

|领域|类型|描述|
| ------------------- | ------- | -------------------------------------------- |
| `modifiedResult`    |对象|使用修改后的结果代替原始结果|
| `additionalContext` |字符串|对话中注入额外的上下文|
| `suppressOutput`    |布尔值|如果为真，结果将不会出现在对话中|

## 示例

### 记录所有工具结果

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input, invocation) => {
      console.log(`[${invocation.sessionId}] Tool: ${input.toolName}`);
      console.log(`  Args: ${JSON.stringify(input.toolArgs)}`);
      console.log(`  Result: ${JSON.stringify(input.toolResult)}`);
      return null; // Pass through unchanged
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

async def on_post_tool_use(input_data, invocation):
    print(f"[{invocation['session_id']}] Tool: {input_data['toolName']}")
    print(f"  Args: {input_data['toolArgs']}")
    print(f"  Result: {input_data['toolResult']}")
    return None  # Pass through unchanged

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={"on_post_tool_use": on_post_tool_use})
```

{% endcodetab %}
{% codetab go %}

```golang
session, _ := client.CreateSession(context.Background(), &copilot.SessionConfig{
    Hooks: &copilot.SessionHooks{
        OnPostToolUse: func(input copilot.PostToolUseHookInput, inv copilot.HookInvocation) (*copilot.PostToolUseHookOutput, error) {
            fmt.Printf("[%s] Tool: %s\n", inv.SessionID, input.ToolName)
            fmt.Printf("  Args: %v\n", input.ToolArgs)
            fmt.Printf("  Result: %v\n", input.ToolResult)
            return nil, nil
        },
    },
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    Hooks = new SessionHooks
    {
        OnPostToolUse = (input, invocation) =>
        {
            Console.WriteLine($"[{invocation.SessionId}] Tool: {input.ToolName}");
            Console.WriteLine($"  Args: {input.ToolArgs}");
            Console.WriteLine($"  Result: {input.ToolResult}");
            return Task.FromResult<PostToolUseHookOutput?>(null);
        },
    },
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.*;
import com.github.copilot.rpc.*;
import java.util.concurrent.CompletableFuture;

var hooks = new SessionHooks()
    .setOnPostToolUse((input, invocation) -> {
        System.out.println("[" + invocation.getSessionId() + "] Tool: " + input.getToolName());
        System.out.println("  Args: " + input.getToolArgs());
        System.out.println("  Result: " + input.getToolResult());
        return CompletableFuture.completedFuture(null);
    });

var session = client.createSession(
    new SessionConfig()
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
        .setHooks(hooks)
).get();
```

{% endcodetab %}
{% endcodetabs %}

### 编辑敏感数据

```typescript
const SENSITIVE_PATTERNS = [
  /api[_-]?key["\s:=]+["']?[\w-]+["']?/gi,
  /password["\s:=]+["']?[\w-]+["']?/gi,
  /secret["\s:=]+["']?[\w-]+["']?/gi,
];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (typeof input.toolResult === "string") {
        let redacted = input.toolResult;
        for (const pattern of SENSITIVE_PATTERNS) {
          redacted = redacted.replace(pattern, "[REDACTED]");
        }

        if (redacted !== input.toolResult) {
          return { modifiedResult: redacted };
        }
      }
      return null;
    },
  },
});
```

### 截断大结果

```typescript
const MAX_RESULT_LENGTH = 10000;

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      const resultStr = JSON.stringify(input.toolResult);

      if (resultStr.length > MAX_RESULT_LENGTH) {
        return {
          modifiedResult: {
            truncated: true,
            originalLength: resultStr.length,
            content: resultStr.substring(0, MAX_RESULT_LENGTH) + "...",
          },
          additionalContext: `Note: Result was truncated from ${resultStr.length} to ${MAX_RESULT_LENGTH} characters.`,
        };
      }
      return null;
    },
  },
});
```

### 根据结果添加上下文

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      // If a file read returned an error, add helpful context
      if (input.toolName === "read_file" && input.toolResult?.error) {
        return {
          additionalContext:
            "Tip: If the file doesn't exist, consider creating it or checking the path.",
        };
      }

      // If shell command failed, add debugging hint
      if (input.toolName === "shell" && input.toolResult?.exitCode !== 0) {
        return {
          additionalContext:
            "The command failed. Check if required dependencies are installed.",
        };
      }

      return null;
    },
  },
});
```

### 过滤错误堆栈跟踪

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (input.toolResult?.error && input.toolResult?.stack) {
        // Remove internal stack trace details
        return {
          modifiedResult: {
            error: input.toolResult.error,
            // Keep only first 3 lines of stack
            stack: input.toolResult.stack.split("\n").slice(0, 3).join("\n"),
          },
        };
      }
      return null;
    },
  },
});
```

### 合规性审计跟踪

```typescript
interface AuditEntry {
  timestamp: Date;
  sessionId: string;
  toolName: string;
  args: unknown;
  result: unknown;
  success: boolean;
}

const auditLog: AuditEntry[] = [];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        toolName: input.toolName,
        args: input.toolArgs,
        result: input.toolResult,
        success: !input.toolResult?.error,
      });

      // Optionally persist to database/file
      await saveAuditLog(auditLog);

      return null;
    },
  },
});
```

### 抑制噪声结果

```typescript
const NOISY_TOOLS = ["list_directory", "search_codebase"];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (NOISY_TOOLS.includes(input.toolName)) {
        // Summarize instead of showing full result
        const items = Array.isArray(input.toolResult)
          ? input.toolResult
          : input.toolResult?.items || [];

        return {
          modifiedResult: {
            summary: `Found ${items.length} items`,
            firstFew: items.slice(0, 5),
          },
        };
      }
      return null;
    },
  },
});
```

## 最佳实践

1. **不需要更改时返回`null`** - 这比返回空对象或相同结果更有效。

1. **小心结果修改** - 更改结果可能会影响模型解释工具输出的方式。仅在必要时修改。

1. **使用 `additionalContext` 进行提示** - 添加上下文来帮助模型解释结果，而不是修改结果。

1. **登录时考虑隐私** - 工具结果可能包含敏感数据。在记录之前应用密文。

1. **保持钩子快速** - 后工具钩子同步运行。繁重的处理应该异步或批量完成。

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/pre-tool-use>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/error-handling>)


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

* Transform or filter tool results
* Log tool execution for auditing
* Add context based on results
* Suppress results from the conversation

> **Failure variant** — `onPostToolUse` only fires for successful tool executions. To observe **failed** tool calls, register `onPostToolUseFailure` (`on_post_tool_use_failure` in Python, `OnPostToolUseFailure` in Go/.NET, `on_post_tool_use_failure` in Rust). The handler receives `{ sessionId, toolName, toolArgs, error, timestamp, workingDirectory }` — the `error` field is a string extracted from the tool's failure result — and may return `{ additionalContext: string }` to inject extra guidance for the model (e.g. retry hints). See the [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/hooks-overview>) for the full list.
> <a id="failure-variant"></a>

## Hook signature

{% codetabs %}
{% codetab typescript %}

```typescript
type PostToolUseHandler = (
  input: PostToolUseHookInput,
  invocation: HookInvocation,
) => Promise<PostToolUseHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
PostToolUseHandler = Callable[
    [PostToolUseHookInput, dict[str, str]],
    Awaitable[PostToolUseHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type PostToolUseHandler func(
    input PostToolUseHookInput,
    invocation HookInvocation,
) (*PostToolUseHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<PostToolUseHookOutput?> PostToolUseHandler(
    PostToolUseHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface PostToolUseHandler {
    CompletableFuture<PostToolUseHookOutput> handle(
        PostToolUseHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

## Input

| Field              | Type               | Description                            |
| ------------------ | ------------------ | -------------------------------------- |
| `timestamp`        | SDK timestamp type | When the hook was triggered            |
| `workingDirectory` | string             | Current working directory              |
| `toolName`         | string             | Name of the tool that was called       |
| `toolArgs`         | object             | Arguments that were passed to the tool |
| `toolResult`       | object             | Result returned by the tool            |

## Output

Return `null` or `undefined` to pass through the result unchanged. Otherwise, return an object with any of these fields:

| Field               | Type    | Description                                  |
| ------------------- | ------- | -------------------------------------------- |
| `modifiedResult`    | object  | Modified result to use instead of original   |
| `additionalContext` | string  | Extra context injected into the conversation |
| `suppressOutput`    | boolean | If true, result won't appear in conversation |

## Examples

### Log all tool results

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input, invocation) => {
      console.log(`[${invocation.sessionId}] Tool: ${input.toolName}`);
      console.log(`  Args: ${JSON.stringify(input.toolArgs)}`);
      console.log(`  Result: ${JSON.stringify(input.toolResult)}`);
      return null; // Pass through unchanged
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

async def on_post_tool_use(input_data, invocation):
    print(f"[{invocation['session_id']}] Tool: {input_data['toolName']}")
    print(f"  Args: {input_data['toolArgs']}")
    print(f"  Result: {input_data['toolResult']}")
    return None  # Pass through unchanged

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={"on_post_tool_use": on_post_tool_use})
```

{% endcodetab %}
{% codetab go %}

```golang
session, _ := client.CreateSession(context.Background(), &copilot.SessionConfig{
    Hooks: &copilot.SessionHooks{
        OnPostToolUse: func(input copilot.PostToolUseHookInput, inv copilot.HookInvocation) (*copilot.PostToolUseHookOutput, error) {
            fmt.Printf("[%s] Tool: %s\n", inv.SessionID, input.ToolName)
            fmt.Printf("  Args: %v\n", input.ToolArgs)
            fmt.Printf("  Result: %v\n", input.ToolResult)
            return nil, nil
        },
    },
})
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
var session = await client.CreateSessionAsync(new SessionConfig
{
    Hooks = new SessionHooks
    {
        OnPostToolUse = (input, invocation) =>
        {
            Console.WriteLine($"[{invocation.SessionId}] Tool: {input.ToolName}");
            Console.WriteLine($"  Args: {input.ToolArgs}");
            Console.WriteLine($"  Result: {input.ToolResult}");
            return Task.FromResult<PostToolUseHookOutput?>(null);
        },
    },
});
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.github.copilot.*;
import com.github.copilot.rpc.*;
import java.util.concurrent.CompletableFuture;

var hooks = new SessionHooks()
    .setOnPostToolUse((input, invocation) -> {
        System.out.println("[" + invocation.getSessionId() + "] Tool: " + input.getToolName());
        System.out.println("  Args: " + input.getToolArgs());
        System.out.println("  Result: " + input.getToolResult());
        return CompletableFuture.completedFuture(null);
    });

var session = client.createSession(
    new SessionConfig()
        .setOnPermissionRequest(PermissionHandler.APPROVE_ALL)
        .setHooks(hooks)
).get();
```

{% endcodetab %}
{% endcodetabs %}

### Redact sensitive data

```typescript
const SENSITIVE_PATTERNS = [
  /api[_-]?key["\s:=]+["']?[\w-]+["']?/gi,
  /password["\s:=]+["']?[\w-]+["']?/gi,
  /secret["\s:=]+["']?[\w-]+["']?/gi,
];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (typeof input.toolResult === "string") {
        let redacted = input.toolResult;
        for (const pattern of SENSITIVE_PATTERNS) {
          redacted = redacted.replace(pattern, "[REDACTED]");
        }

        if (redacted !== input.toolResult) {
          return { modifiedResult: redacted };
        }
      }
      return null;
    },
  },
});
```

### Truncate large results

```typescript
const MAX_RESULT_LENGTH = 10000;

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      const resultStr = JSON.stringify(input.toolResult);

      if (resultStr.length > MAX_RESULT_LENGTH) {
        return {
          modifiedResult: {
            truncated: true,
            originalLength: resultStr.length,
            content: resultStr.substring(0, MAX_RESULT_LENGTH) + "...",
          },
          additionalContext: `Note: Result was truncated from ${resultStr.length} to ${MAX_RESULT_LENGTH} characters.`,
        };
      }
      return null;
    },
  },
});
```

### Add context based on results

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      // If a file read returned an error, add helpful context
      if (input.toolName === "read_file" && input.toolResult?.error) {
        return {
          additionalContext:
            "Tip: If the file doesn't exist, consider creating it or checking the path.",
        };
      }

      // If shell command failed, add debugging hint
      if (input.toolName === "shell" && input.toolResult?.exitCode !== 0) {
        return {
          additionalContext:
            "The command failed. Check if required dependencies are installed.",
        };
      }

      return null;
    },
  },
});
```

### Filter error stack traces

```typescript
const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (input.toolResult?.error && input.toolResult?.stack) {
        // Remove internal stack trace details
        return {
          modifiedResult: {
            error: input.toolResult.error,
            // Keep only first 3 lines of stack
            stack: input.toolResult.stack.split("\n").slice(0, 3).join("\n"),
          },
        };
      }
      return null;
    },
  },
});
```

### Audit trail for compliance

```typescript
interface AuditEntry {
  timestamp: Date;
  sessionId: string;
  toolName: string;
  args: unknown;
  result: unknown;
  success: boolean;
}

const auditLog: AuditEntry[] = [];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        toolName: input.toolName,
        args: input.toolArgs,
        result: input.toolResult,
        success: !input.toolResult?.error,
      });

      // Optionally persist to database/file
      await saveAuditLog(auditLog);

      return null;
    },
  },
});
```

### Suppress noisy results

```typescript
const NOISY_TOOLS = ["list_directory", "search_codebase"];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (NOISY_TOOLS.includes(input.toolName)) {
        // Summarize instead of showing full result
        const items = Array.isArray(input.toolResult)
          ? input.toolResult
          : input.toolResult?.items || [];

        return {
          modifiedResult: {
            summary: `Found ${items.length} items`,
            firstFew: items.slice(0, 5),
          },
        };
      }
      return null;
    },
  },
});
```

## Best practices

1. **Return `null` when no changes needed** - This is more efficient than returning an empty object or the same result.

1. **Be careful with result modification** - Changing results can affect how the model interprets tool output. Only modify when necessary.

1. **Use `additionalContext` for hints** - Instead of modifying results, add context to help the model interpret them.

1. **Consider privacy when logging** - Tool results may contain sensitive data. Apply redaction before logging.

1. **Keep hooks fast** - Post-tool hooks run synchronously. Heavy processing should be done asynchronously or batched.

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/pre-tool-use>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/error-handling>)


</details>
