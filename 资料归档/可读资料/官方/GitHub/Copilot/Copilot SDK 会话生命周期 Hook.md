# Copilot SDK 会话生命周期 Hook

介绍会话开始、结束及 Agent 停止事件，提供加载项目上下文、保存状态、统计指标和清理资源的样例。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/github/docs/commit/d00eabd5ae6575005437fbc848705f0d0415d8d4>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/hooks/session-lifecycle.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 会话生命周期 Hook.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Session lifecycle hooks
shortTitle: Session Lifecycle
intro: >-
  Session lifecycle hooks let you respond to session start and end events. Use
  them to:
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/use-hooks/session-lifecycle
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

* 会话开始时初始化上下文
* 会话结束时清理资源
* 跟踪会话指标和分析
* 动态配置会话行为

## 会话启动挂钩

当会话开始（新的或恢复的）时，将调用 `onSessionStart` 挂钩。

### 钩签名

{% codetabs %}
{% codetab typescript %}

```typescript
type SessionStartHandler = (
  input: SessionStartHookInput,
  invocation: HookInvocation
) => Promise<SessionStartHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
SessionStartHandler = Callable[
    [SessionStartHookInput, dict[str, str]],
    Awaitable[SessionStartHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type SessionStartHandler func(
    input SessionStartHookInput,
    invocation HookInvocation,
) (*SessionStartHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<SessionStartHookOutput?> SessionStartHandler(
    SessionStartHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface SessionStartHandler {
    CompletableFuture<SessionStartHookOutput> handle(
        SessionStartHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

### 输入

|领域|类型|描述|
|-------|------|-------------|
| `timestamp` |数量|触发钩子时的 Unix 时间戳|
| `cwd` |字符串|当前工作目录|
| `source` | `"startup"` \| `"resume"` \| `"new"` |会议是如何开始的|
| `initialPrompt` |字符串 \| 未定义|初始提示（如果提供）|

### 输出

|领域|类型|描述|
|-------|------|-------------|
| `additionalContext` |字符串|在会话开始时添加的上下文|
| `modifiedConfig` |对象|覆盖会话配置|

### 示例

#### 在开始时添加项目上下文

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      console.log(`Session ${invocation.sessionId} started (${input.source})`);
      
      const projectInfo = await detectProjectType(input.cwd);
      
      return {
        additionalContext: `
This is a ${projectInfo.type} project.
Main language: ${projectInfo.language}
Package manager: ${projectInfo.packageManager}
        `.trim(),
      };
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

async def on_session_start(input_data, invocation):
    print(f"Session {invocation['session_id']} started ({input_data['source']})")
    
    project_info = await detect_project_type(input_data["cwd"])
    
    return {
        "additionalContext": f"""
This is a {project_info['type']} project.
Main language: {project_info['language']}
Package manager: {project_info['packageManager']}
        """.strip()
    }

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={"on_session_start": on_session_start})
```

{% endcodetab %}
{% endcodetabs %}

#### 处理会话恢复

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      if (input.source === "resume") {
        // Load previous session state
        const previousState = await loadSessionState(invocation.sessionId);
        
        return {
          additionalContext: `
Session resumed. Previous context:
- Last topic: ${previousState.lastTopic}
- Open files: ${previousState.openFiles.join(", ")}
          `.trim(),
        };
      }
      return null;
    },
  },
});
```

#### 加载用户首选项

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async () => {
      const preferences = await loadUserPreferences();
      
      const contextParts = [];
      
      if (preferences.language) {
        contextParts.push(`Preferred language: ${preferences.language}`);
      }
      if (preferences.codeStyle) {
        contextParts.push(`Code style: ${preferences.codeStyle}`);
      }
      if (preferences.verbosity === "concise") {
        contextParts.push("Keep responses brief and to the point.");
      }
      
      return {
        additionalContext: contextParts.join("\n"),
      };
    },
  },
});
```

## 会话结束挂钩

会话结束时会调用 `onSessionEnd` 挂钩。

### 钩签名

{% codetabs %}
{% codetab typescript %}

```typescript
type SessionEndHandler = (
  input: SessionEndHookInput,
  invocation: HookInvocation
) => Promise<SessionEndHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
SessionEndHandler = Callable[
    [SessionEndHookInput, dict[str, str]],
    Awaitable[SessionEndHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type SessionEndHandler func(
    input SessionEndHookInput,
    invocation HookInvocation,
) (*SessionEndHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<SessionEndHookOutput?> SessionEndHandler(
    SessionEndHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface SessionEndHandler {
    CompletableFuture<SessionEndHookOutput> handle(
        SessionEndHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

### 输入

|领域|类型|描述|
|-------|------|-------------|
| `timestamp` |数量|触发钩子时的 Unix 时间戳|
| `cwd` |字符串|当前工作目录|
| `reason` |字符串|会议为何结束（见下文）|
| `finalMessage` |字符串 \| 未定义|会话中的最后一条消息|
| `error` |字符串 \| 未定义|如果会话因错误而结束，则会出现错误消息|

#### 结束原因

|原因|描述|
|--------|-------------|
| `"complete"` |会话正常完成|
| `"error"` |会话因错误而结束|
| `"abort"` |会话被用户或代码中止|
| `"timeout"` |会话超时|
| `"user_exit"` |用户明确结束会话|

### 输出

|领域|类型|描述|
|-------|------|-------------|
| `suppressOutput` |布尔值|抑制最终会话输出|
| `cleanupActions` |字符串[]|要执行的清理操作列表|
| `sessionSummary` |字符串|logging/analytics 会议摘要|

### 示例

#### 跟踪会话指标

{% codetabs %}
{% codetab typescript %}

```typescript
const sessionStartTimes = new Map<string, number>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionStartTimes.set(invocation.sessionId, input.timestamp);
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const startTime = sessionStartTimes.get(invocation.sessionId);
      const duration = startTime ? input.timestamp - startTime : 0;
      
      await recordMetrics({
        sessionId: invocation.sessionId,
        duration,
        endReason: input.reason,
      });
      
      sessionStartTimes.delete(invocation.sessionId);
      return null;
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

session_start_times = {}

async def on_session_start(input_data, invocation):
    session_start_times[invocation["session_id"]] = input_data["timestamp"]
    return None

async def on_session_end(input_data, invocation):
    start_time = session_start_times.get(invocation["session_id"])
    duration = input_data["timestamp"] - start_time if start_time else 0
    
    await record_metrics({
        "session_id": invocation["session_id"],
        "duration": duration,
        "end_reason": input_data["reason"],
    })
    
    session_start_times.pop(invocation["session_id"], None)
    return None

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={
        "on_session_start": on_session_start,
        "on_session_end": on_session_end,
    })
```

{% endcodetab %}
{% endcodetabs %}

#### 清理资源

```typescript
const sessionResources = new Map<string, { tempFiles: string[] }>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionResources.set(invocation.sessionId, { tempFiles: [] });
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const resources = sessionResources.get(invocation.sessionId);
      
      if (resources) {
        // Clean up temp files
        for (const file of resources.tempFiles) {
          await fs.unlink(file).catch(() => {});
        }
        sessionResources.delete(invocation.sessionId);
      }
      
      console.log(`Session ${invocation.sessionId} ended: ${input.reason}`);
      return null;
    },
  },
});
```

#### 保存会话状态以供恢复

```typescript
const session = await client.createSession({
  hooks: {
    onSessionEnd: async (input, invocation) => {
      if (input.reason !== "error") {
        // Save state for potential resume
        await saveSessionState(invocation.sessionId, {
          endTime: input.timestamp,
          cwd: input.cwd,
          reason: input.reason,
        });
      }
      return null;
    },
  },
});
```

#### 记录会话摘要

```typescript
const sessionData: Record<string, { prompts: number; tools: number; startTime: number }> = {};

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionData[invocation.sessionId] = { 
        prompts: 0, 
        tools: 0, 
        startTime: input.timestamp 
      };
      return null;
    },
    onUserPromptSubmitted: async (_, invocation) => {
      sessionData[invocation.sessionId].prompts++;
      return null;
    },
    onPreToolUse: async (_, invocation) => {
      sessionData[invocation.sessionId].tools++;
      return { permissionDecision: "allow" };
    },
    onSessionEnd: async (input, invocation) => {
      const data = sessionData[invocation.sessionId];
      console.log(`
Session Summary:
  ID: ${invocation.sessionId}
  Duration: ${(input.timestamp - data.startTime) / 1000}s
  Prompts: ${data.prompts}
  Tool calls: ${data.tools}
  End reason: ${input.reason}
      `.trim());
      
      delete sessionData[invocation.sessionId];
      return null;
    },
  },
});
```

## Agent 停止钩

当顶层 agent 自然到达回合结束时，agent 停止钩运行。它与 `onSessionEnd` 分开：会话保持活动状态，并且挂钩可以请求另一个 agent 回合。

|语言|处理程序|
|----------|---------|
| Node.js / TypeScript | `onAgentStop` |
| Python | `on_agent_stop` |
| Go | `OnAgentStop` |
| .NET | `OnAgentStop` |
| Rust | `on_agent_stop` |
| Java | `setOnAgentStop` |

### 输入

公共成员名称遵循每种语言的大小写约定：

|含义| Node.js / Python | Go / .NET | Rust | Java |
|---------|------------------|-----------|------|------|
|为什么agent停止了，例如`end_turn`| `stopReason` | `StopReason` | `stop_reason` | `getStopReason()` |
|磁盘上会话记录的路径| `transcriptPath` | `TranscriptPath` | `transcript_path` | `getTranscriptPath()` |
|早期的区块决定是否已经迫使这种延续| `stopHookActive` | `StopHookActive` | `stop_hook_active` | `getStopHookActive()` |

### 输出

不返回任何输出让agent停止。返回一个块决策以将另一条用户消息排入队列并继续：

```json
{
  "decision": "block",
  "reason": "Run the final validation and fix any failures."
}
```

使用上面列出的 active-stop 成员可以避免由于该钩子而重复阻塞已经继续的 agent。运行时间还限制了连续的块决策。

## 最佳实践

1. **保持`onSessionStart`快速** - 用户正在等待会话准备就绪。

1. **处理所有结束原因** - 不要假设会话干净地结束；处理错误并中止。

1. **清理资源** - 使用`onSessionEnd`释放会话期间分配的所有资源。

1. **存储最小状态** - 如果跟踪会话数据，请保持轻量级。

1. **使清理幂等** - 如果进程崩溃，可能不会调用`onSessionEnd`。

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/error-handling>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>)


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

* Initialize context when sessions begin
* Clean up resources when sessions end
* Track session metrics and analytics
* Configure session behavior dynamically

## Session start hook

The `onSessionStart` hook is called when a session begins (new or resumed).

### Hook signature

{% codetabs %}
{% codetab typescript %}

```typescript
type SessionStartHandler = (
  input: SessionStartHookInput,
  invocation: HookInvocation
) => Promise<SessionStartHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
SessionStartHandler = Callable[
    [SessionStartHookInput, dict[str, str]],
    Awaitable[SessionStartHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type SessionStartHandler func(
    input SessionStartHookInput,
    invocation HookInvocation,
) (*SessionStartHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<SessionStartHookOutput?> SessionStartHandler(
    SessionStartHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface SessionStartHandler {
    CompletableFuture<SessionStartHookOutput> handle(
        SessionStartHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

### Input

| Field | Type | Description |
|-------|------|-------------|
| `timestamp` | number | Unix timestamp when the hook was triggered |
| `cwd` | string | Current working directory |
| `source` | `"startup"` \| `"resume"` \| `"new"` | How the session was started |
| `initialPrompt` | string \| undefined | The initial prompt if provided |

### Output

| Field | Type | Description |
|-------|------|-------------|
| `additionalContext` | string | Context to add at session start |
| `modifiedConfig` | object | Override session configuration |

### Examples

#### Add project context at start

{% codetabs %}
{% codetab typescript %}

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      console.log(`Session ${invocation.sessionId} started (${input.source})`);
      
      const projectInfo = await detectProjectType(input.cwd);
      
      return {
        additionalContext: `
This is a ${projectInfo.type} project.
Main language: ${projectInfo.language}
Package manager: ${projectInfo.packageManager}
        `.trim(),
      };
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

async def on_session_start(input_data, invocation):
    print(f"Session {invocation['session_id']} started ({input_data['source']})")
    
    project_info = await detect_project_type(input_data["cwd"])
    
    return {
        "additionalContext": f"""
This is a {project_info['type']} project.
Main language: {project_info['language']}
Package manager: {project_info['packageManager']}
        """.strip()
    }

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={"on_session_start": on_session_start})
```

{% endcodetab %}
{% endcodetabs %}

#### Handle session resume

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      if (input.source === "resume") {
        // Load previous session state
        const previousState = await loadSessionState(invocation.sessionId);
        
        return {
          additionalContext: `
Session resumed. Previous context:
- Last topic: ${previousState.lastTopic}
- Open files: ${previousState.openFiles.join(", ")}
          `.trim(),
        };
      }
      return null;
    },
  },
});
```

#### Load user preferences

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async () => {
      const preferences = await loadUserPreferences();
      
      const contextParts = [];
      
      if (preferences.language) {
        contextParts.push(`Preferred language: ${preferences.language}`);
      }
      if (preferences.codeStyle) {
        contextParts.push(`Code style: ${preferences.codeStyle}`);
      }
      if (preferences.verbosity === "concise") {
        contextParts.push("Keep responses brief and to the point.");
      }
      
      return {
        additionalContext: contextParts.join("\n"),
      };
    },
  },
});
```

## Session end hook

The `onSessionEnd` hook is called when a session ends.

### Hook signature

{% codetabs %}
{% codetab typescript %}

```typescript
type SessionEndHandler = (
  input: SessionEndHookInput,
  invocation: HookInvocation
) => Promise<SessionEndHookOutput | null | undefined>;
```

{% endcodetab %}
{% codetab python %}

```python
SessionEndHandler = Callable[
    [SessionEndHookInput, dict[str, str]],
    Awaitable[SessionEndHookOutput | None]
]
```

{% endcodetab %}
{% codetab go %}

```golang
type SessionEndHandler func(
    input SessionEndHookInput,
    invocation HookInvocation,
) (*SessionEndHookOutput, error)
```

{% endcodetab %}
{% codetab dotnet %}

```csharp
public delegate Task<SessionEndHookOutput?> SessionEndHandler(
    SessionEndHookInput input,
    HookInvocation invocation);
```

{% endcodetab %}
{% codetab java %}

```java
@FunctionalInterface
public interface SessionEndHandler {
    CompletableFuture<SessionEndHookOutput> handle(
        SessionEndHookInput input,
        HookInvocation invocation);
}
```

{% endcodetab %}
{% endcodetabs %}

### Input

| Field | Type | Description |
|-------|------|-------------|
| `timestamp` | number | Unix timestamp when the hook was triggered |
| `cwd` | string | Current working directory |
| `reason` | string | Why the session ended (see below) |
| `finalMessage` | string \| undefined | The last message from the session |
| `error` | string \| undefined | Error message if session ended due to error |

#### End reasons

| Reason | Description |
|--------|-------------|
| `"complete"` | Session completed normally |
| `"error"` | Session ended due to an error |
| `"abort"` | Session was aborted by user or code |
| `"timeout"` | Session timed out |
| `"user_exit"` | User explicitly ended the session |

### Output

| Field | Type | Description |
|-------|------|-------------|
| `suppressOutput` | boolean | Suppress the final session output |
| `cleanupActions` | string[] | List of cleanup actions to perform |
| `sessionSummary` | string | Summary of the session for logging/analytics |

### Examples

#### Track session metrics

{% codetabs %}
{% codetab typescript %}

```typescript
const sessionStartTimes = new Map<string, number>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionStartTimes.set(invocation.sessionId, input.timestamp);
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const startTime = sessionStartTimes.get(invocation.sessionId);
      const duration = startTime ? input.timestamp - startTime : 0;
      
      await recordMetrics({
        sessionId: invocation.sessionId,
        duration,
        endReason: input.reason,
      });
      
      sessionStartTimes.delete(invocation.sessionId);
      return null;
    },
  },
});
```

{% endcodetab %}
{% codetab python %}

```python
from copilot.session import PermissionHandler

session_start_times = {}

async def on_session_start(input_data, invocation):
    session_start_times[invocation["session_id"]] = input_data["timestamp"]
    return None

async def on_session_end(input_data, invocation):
    start_time = session_start_times.get(invocation["session_id"])
    duration = input_data["timestamp"] - start_time if start_time else 0
    
    await record_metrics({
        "session_id": invocation["session_id"],
        "duration": duration,
        "end_reason": input_data["reason"],
    })
    
    session_start_times.pop(invocation["session_id"], None)
    return None

session = await client.create_session(on_permission_request=PermissionHandler.approve_all, hooks={
        "on_session_start": on_session_start,
        "on_session_end": on_session_end,
    })
```

{% endcodetab %}
{% endcodetabs %}

#### Clean up resources

```typescript
const sessionResources = new Map<string, { tempFiles: string[] }>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionResources.set(invocation.sessionId, { tempFiles: [] });
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const resources = sessionResources.get(invocation.sessionId);
      
      if (resources) {
        // Clean up temp files
        for (const file of resources.tempFiles) {
          await fs.unlink(file).catch(() => {});
        }
        sessionResources.delete(invocation.sessionId);
      }
      
      console.log(`Session ${invocation.sessionId} ended: ${input.reason}`);
      return null;
    },
  },
});
```

#### Save session state for resume

```typescript
const session = await client.createSession({
  hooks: {
    onSessionEnd: async (input, invocation) => {
      if (input.reason !== "error") {
        // Save state for potential resume
        await saveSessionState(invocation.sessionId, {
          endTime: input.timestamp,
          cwd: input.cwd,
          reason: input.reason,
        });
      }
      return null;
    },
  },
});
```

#### Log session summary

```typescript
const sessionData: Record<string, { prompts: number; tools: number; startTime: number }> = {};

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionData[invocation.sessionId] = { 
        prompts: 0, 
        tools: 0, 
        startTime: input.timestamp 
      };
      return null;
    },
    onUserPromptSubmitted: async (_, invocation) => {
      sessionData[invocation.sessionId].prompts++;
      return null;
    },
    onPreToolUse: async (_, invocation) => {
      sessionData[invocation.sessionId].tools++;
      return { permissionDecision: "allow" };
    },
    onSessionEnd: async (input, invocation) => {
      const data = sessionData[invocation.sessionId];
      console.log(`
Session Summary:
  ID: ${invocation.sessionId}
  Duration: ${(input.timestamp - data.startTime) / 1000}s
  Prompts: ${data.prompts}
  Tool calls: ${data.tools}
  End reason: ${input.reason}
      `.trim());
      
      delete sessionData[invocation.sessionId];
      return null;
    },
  },
});
```

## Agent stop hook

The agent stop hook runs when the top-level agent naturally reaches the end of a turn. It is separate from `onSessionEnd`: the session remains active, and the hook can request another agent turn.

| Language | Handler |
|----------|---------|
| Node.js / TypeScript | `onAgentStop` |
| Python | `on_agent_stop` |
| Go | `OnAgentStop` |
| .NET | `OnAgentStop` |
| Rust | `on_agent_stop` |
| Java | `setOnAgentStop` |

### Input

The public member names follow each language's casing conventions:

| Meaning | Node.js / Python | Go / .NET | Rust | Java |
|---------|------------------|-----------|------|------|
| Why the agent stopped, such as `end_turn` | `stopReason` | `StopReason` | `stop_reason` | `getStopReason()` |
| Path to the on-disk session transcript | `transcriptPath` | `TranscriptPath` | `transcript_path` | `getTranscriptPath()` |
| Whether an earlier block decision already forced this continuation | `stopHookActive` | `StopHookActive` | `stop_hook_active` | `getStopHookActive()` |

### Output

Return no output to let the agent stop. Return a block decision to enqueue another user message and continue:

```json
{
  "decision": "block",
  "reason": "Run the final validation and fix any failures."
}
```

Use the active-stop member listed above to avoid repeatedly blocking an agent that has already continued because of this hook. The runtime also caps consecutive block decisions.

## Best practices

1. **Keep `onSessionStart` fast** - Users are waiting for the session to be ready.

1. **Handle all end reasons** - Don't assume sessions end cleanly; handle errors and aborts.

1. **Clean up resources** - Use `onSessionEnd` to free any resources allocated during the session.

1. **Store minimal state** - If tracking session data, keep it lightweight.

1. **Make cleanup idempotent** - `onSessionEnd` might not be called if the process crashes.

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/hooks/error-handling>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/troubleshooting/debugging>)


</details>
