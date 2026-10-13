# Hook 开发技能

说明提示型和命令型 Hook、配置格式、事件及校验方式，帮助创建插件中的自动检查与执行控制。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/387dc35db708150c229d7ed2d34342042e417fa0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/hook-development/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Hook 开发技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: Hook Development
description: This skill should be used when the user asks to "create a hook", "add a PreToolUse/PostToolUse/Stop hook", "validate tool use", "implement prompt-based hooks", "use ${CLAUDE_PLUGIN_ROOT}", "set up event-driven automation", "block dangerous commands", or mentions hook events (PreToolUse, PostToolUse, Stop, SubagentStop, SessionStart, SessionEnd, UserPromptSubmit, PreCompact, Notification). Provides comprehensive guidance for creating and implementing Claude Code plugin hooks with focus on advanced prompt-based hooks API.
version: 0.1.0
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Claude Code 插件的 Hook 开发

## 概述

挂钩是事件驱动的自动化脚本，用于响应 Claude Code 事件而执行。使用挂钩来验证操作、实施策略、添加上下文以及将外部工具集成到工作流程中。

**关键功能：**
- 执行前验证工具调用 (PreToolUse)
- React 到工具结果 (PostToolUse)
- 执行完成标准（停止，SubagentStop）
- 加载项目上下文（SessionStart）
- 自动化整个开发生命周期的工作流程

## 挂钩类型

### 基于提示的 Hook（推荐）

使用 LLM- 驱动的决策来进行上下文感知验证：

```json
{
  "type": "prompt",
  "prompt": "Evaluate if this tool use is appropriate: $TOOL_INPUT",
  "timeout": 30
}
```

**支持的事件：**停止、SubagentStop、UserPromptSubmit、PreToolUse

**优点：**
- 基于自然语言推理的上下文感知决策
- 灵活的评估逻辑，无需 bash 脚本
- 更好的边缘情况处理
- 更容易维护和扩展

### 命令挂钩

执行 bash 命令进行确定性检查：

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
  "timeout": 60
}
```

**用于：**
- 快速确定性验证
- 文件系统操作
- 外部工具集成
- 性能关键检查

## 钩子配置格式

### 插件hooks.json格式

**对于`hooks/hooks.json`中的插件挂钩**，使用包装格式：

```json
{
  "description": "Brief explanation of hooks (optional)",
  "hooks": {
    "PreToolUse": [...],
    "Stop": [...],
    "SessionStart": [...]
  }
}
```

**要点：**
- `description` 字段是可选的
- `hooks` 字段是包含实际挂钩事件的必需包装器
- 这是 ** 插件特定格式 **

**示例：**
```json
{
  "description": "Validation hooks for code quality",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/hooks/validate.sh"
          }
        ]
      }
    ]
  }
}
```

### 设置格式（直接）

**对于`.claude/settings.json`中的用户设置**，使用直接格式：

```json
{
  "PreToolUse": [...],
  "Stop": [...],
  "SessionStart": [...]
}
```

**要点：**
- 无包装器 - 事件直接在顶层
- 无描述字段
- 这是**设置格式**

**重要提示：** 下面的示例显示了任一格式中的挂钩事件结构。对于插件 hooks.json，将它们包装在 `{"hooks": {...}}` 中。

## 挂钩事件

### PreToolUse

在任何工具运行之前执行。用于批准、拒绝或修改工具调用。

**示例（基于提示）：**
```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Validate file write safety. Check: system paths, credentials, path traversal, sensitive content. Return 'approve' or 'deny'."
        }
      ]
    }
  ]
}
```

**PreToolUse的输出：**
```json
{
  "hookSpecificOutput": {
    "permissionDecision": "allow|deny|ask",
    "updatedInput": {"field": "modified_value"}
  },
  "systemMessage": "Explanation for Claude"
}
```

### PostToolUse

工具完成后执行。用于对结果做出反应、提供反馈或记录。

**示例：**
```json
{
  "PostToolUse": [
    {
      "matcher": "Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Analyze edit result for potential issues: syntax errors, security vulnerabilities, breaking changes. Provide feedback."
        }
      ]
    }
  ]
}
```

**输出行为：**
- 退出 0：记录中显示标准输出
- 退出2：stderr反馈到Claude
- systemMessage 包含在上下文中

### 停止

当mainagent考虑停止时执行。用于验证完整性。

**示例：**
```json
{
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Verify task completion: tests run, build succeeded, questions answered. Return 'approve' to stop or 'block' with reason to continue."
        }
      ]
    }
  ]
}
```

**决策输出：**
```json
{
  "decision": "approve|block",
  "reason": "Explanation",
  "systemMessage": "Additional context"
}
```

### SubagentStop

当子代理考虑停止时执行。用于确保子代理完成其任务。

与 Stop hook 类似，但用于子代理。

### UserPromptSubmit

当用户提交提示时执行。用于添加上下文、验证或阻止提示。

**示例：**
```json
{
  "UserPromptSubmit": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Check if prompt requires security guidance. If discussing auth, permissions, or API security, return relevant warnings."
        }
      ]
    }
  ]
}
```

### SessionStart

Claude Code 会话开始时执行。用于加载上下文和设置环境。

**示例：**
```json
{
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/load-context.sh"
        }
      ]
    }
  ]
}
```

**特殊功能：**使用`$CLAUDE_ENV_FILE`保留环境变量：
```bash
echo "export PROJECT_TYPE=nodejs" >> "$CLAUDE_ENV_FILE"
```

有关完整示例，请参阅 `examples/load-context.sh`。

### SessionEnd

会话结束时执行。用于清理、记录和状态保存。

### PreCompact

在上下文压缩之前执行。用于添加要保留的关键信息。

### 通知

当Claude发送通知时执行。用于对用户通知做出反应。

## 挂钩输出格式

### 标准输出（所有挂钩）

```json
{
  "continue": true,
  "suppressOutput": false,
  "systemMessage": "Message for Claude"
}
```

- `continue`：如果为 false，则停止处理（默认为 true）
- `suppressOutput`：隐藏转录本的输出（默认 false）
- `systemMessage`：显示给Claude的消息

### 退出代码

- `0` - 成功（记录中显示标准输出）
- `2` - 阻塞错误（stderr 反馈到 Claude）
- 其他 - 非阻塞错误

## 钩子输入格式

所有钩子通过具有公共字段的标准输入接收JSON：

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.txt",
  "cwd": "/current/working/dir",
  "permission_mode": "ask|allow",
  "hook_event_name": "PreToolUse"
}
```

**事件特定字段：**

- **PreToolUse/PostToolUse:** `tool_name`, `tool_input`, `tool_result`
- **UserPromptSubmit:** `user_prompt`
- **Stop/SubagentStop:** `reason`

使用 `$TOOL_INPUT`、`$TOOL_RESULT`、`$USER_PROMPT` 等访问提示中的字段。

## 环境变量

在所有命令挂钩中可用：

- `$CLAUDE_PROJECT_DIR` - 项目根路径
- `$CLAUDE_PLUGIN_ROOT` - 插件目录（用于可移植路径）
- `$CLAUDE_ENV_FILE` - 仅 SessionStart：在此处保留环境变量
- `$CLAUDE_CODE_REMOTE` - 如果在远程上下文中运行则设置

**始终在挂钩命令中使用 ${CLAUDE_PLUGIN_ROOT} 以实现可移植性：**

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
}
```

## 插件挂钩配置

在插件中，在`hooks/hooks.json`中定义钩子：

```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Validate file write safety"
        }
      ]
    }
  ],
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Verify task completion"
        }
      ]
    }
  ],
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/load-context.sh",
          "timeout": 10
        }
      ]
    }
  ]
}
```

插件钩子与用户的钩子合并并并行运行。

## 匹配器

### 工具名称匹配

**完全匹配：**
```json
"matcher": "Write"
```

**多种工具：**
```json
"matcher": "Read|Write|Edit"
```

**通配符（所有工具）：**
```json
"matcher": "*"
```

**正则表达式模式：**
```json
"matcher": "mcp__.*__delete.*"  // All MCP delete tools
```

**注意：** 匹配器区分大小写。

### 常见模式

```json
// All MCP tools
"matcher": "mcp__.*"

// Specific plugin's MCP tools
"matcher": "mcp__plugin_asana_.*"

// All file operations
"matcher": "Read|Write|Edit"

// Bash commands only
"matcher": "Bash"
```

## 安全最佳实践

### 输入验证

始终验证命令挂钩中的输入：

```bash
#!/bin/bash
set -euo pipefail

input=$(cat)
tool_name=$(echo "$input" | jq -r '.tool_name')

# Validate tool name format
if [[ ! "$tool_name" =~ ^[a-zA-Z0-9_]+$ ]]; then
  echo '{"decision": "deny", "reason": "Invalid tool name"}' >&2
  exit 2
fi
```

### 路径安全

检查路径遍历和敏感文件：

```bash
file_path=$(echo "$input" | jq -r '.tool_input.file_path')

# Deny path traversal
if [[ "$file_path" == *".."* ]]; then
  echo '{"decision": "deny", "reason": "Path traversal detected"}' >&2
  exit 2
fi

# Deny sensitive files
if [[ "$file_path" == *".env"* ]]; then
  echo '{"decision": "deny", "reason": "Sensitive file"}' >&2
  exit 2
fi
```

有关完整示例，请参阅 `examples/validate-write.sh` 和 `examples/validate-bash.sh`。

### 引用所有变量

```bash
# GOOD: Quoted
echo "$file_path"
cd "$CLAUDE_PROJECT_DIR"

# BAD: Unquoted (injection risk)
echo $file_path
cd $CLAUDE_PROJECT_DIR
```

### 设置适当的超时

```json
{
  "type": "command",
  "command": "bash script.sh",
  "timeout": 10
}
```

**默认值：** 命令挂钩（60 秒）、提示挂钩（30 秒）

## 性能考虑因素

### 并行执行

所有匹配的钩子并行运行****：

```json
{
  "PreToolUse": [
    {
      "matcher": "Write",
      "hooks": [
        {"type": "command", "command": "check1.sh"},  // Parallel
        {"type": "command", "command": "check2.sh"},  // Parallel
        {"type": "prompt", "prompt": "Validate..."}   // Parallel
      ]
    }
  ]
}
```

**设计含义：**
- Hooks 看不到彼此的输出
- 非确定性排序
- 独立设计

### 优化

1. 使用命令挂钩进行快速确定性检查
2. 使用提示钩子进行复杂推理
3. 将验证结果缓存在临时文件中
4. 最小化热路径中的 I/O

## 临时活动钩子

通过检查标志文件或配置来创建有条件激活的钩子：

**模式：标记文件激活**
```bash
#!/bin/bash
# Only active when flag file exists
FLAG_FILE="$CLAUDE_PROJECT_DIR/.enable-strict-validation"

if [ ! -f "$FLAG_FILE" ]; then
  # Flag not present, skip validation
  exit 0
fi

# Flag present, run validation
input=$(cat)
# ... validation logic ...
```

**模式：基于配置的激活**
```bash
#!/bin/bash
# Check configuration for activation
CONFIG_FILE="$CLAUDE_PROJECT_DIR/.claude/plugin-config.json"

if [ -f "$CONFIG_FILE" ]; then
  enabled=$(jq -r '.strictMode // false' "$CONFIG_FILE")
  if [ "$enabled" != "true" ]; then
    exit 0  # Not enabled, skip
  fi
fi

# Enabled, run hook logic
input=$(cat)
# ... hook logic ...
```

**用例：**
- 仅在需要时启用严格验证
- 临时调试钩子
- 项目特定的钩子行为
- 钩子的功能标志

**最佳实践：**插件README中的文档激活机制，以便用户知道如何enable/disable临时挂钩。

## Hook 生命周期和限制

### 会话开始时挂钩加载

**重要提示：** Hooks 在 Claude Code 会话启动时加载。更改挂钩配置需要重新启动 Claude Code。

**无法热插拔挂钩：**
- 编辑`hooks/hooks.json`不会影响当前会话
- 添加新的钩子脚本将不会被识别
- 更改挂钩 commands/prompts 不会更新
- 必须重新启动Claude Code：退出并再次运行`claude`

**测试挂钩更改：**
1. 编辑钩子配置或脚本
2. 退出 Claude Code 会话
3. 重新启动：`claude`或`cc`
4. 新的钩子配置加载
5. 测试挂钩 `claude --debug`

### 启动时的钩子验证

当 Claude Code 启动时，挂钩将被验证：
- hooks.json中的JSON无效导致加载失败
- 缺少脚本会导致警告
- 调试模式下报告语法错误

使用`/hooks`命令查看当前会话中加载的挂钩。

## 调试钩子

### 启用调试模式

```bash
claude --debug
```

查找钩子注册、执行日志、input/output JSON 和计时信息。

### 测试挂钩脚本

直接测试命令钩子：

```bash
echo '{"tool_name": "Write", "tool_input": {"file_path": "/test"}}' | \
  bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh

echo "Exit code: $?"
```

### 验证 JSON 输出

确保钩子输出有效JSON：

```bash
output=$(./your-hook.sh < test-input.json)
echo "$output" | jq .
```

## 快速参考

### 挂钩事件摘要

|活动|当|用于|
|-------|------|---------|
| PreToolUse |工具前|验证、修改|
| PostToolUse |工具后|反馈、记录|
| UserPromptSubmit |用户输入|上下文、验证|
|停止|Agent 停止|完整性检查|
| SubagentStop |子代理完成|任务验证|
| SessionStart |会议开始|上下文加载|
| SessionEnd |会议结束|清理、记录|
| PreCompact |紧凑前|保留上下文|
|通知|用户已通知|记录、反应|

### 最佳实践

**DO:**
- ✅ 使用基于提示的钩子来实现复杂的逻辑
- ✅ 使用 ${CLAUDE_PLUGIN_ROOT} 实现便携性
- ✅ 验证命令挂钩中的所有输入
- ✅ 引用所有 bash 变量
- ✅ 设置适当的超时时间
- ✅ 返回结构化的 JSON 输出
- ✅ 彻底测试钩子

**DON'T:**
- ❌ 使用硬编码路径
- ❌ 信任用户输入而不进行验证
- ❌ 创建长时间运行的钩子
- ❌ 依赖钩子执行顺序
- ❌ 不可预测地修改全局状态
- ❌ 记录敏感信息

## 其他资源

### 参考文件

有关详细模式和高级技术，请参阅：

- **`references/patterns.md`** - 常见的钩子模式（8+经过验证的模式）
- **`references/migration.md`** - 从基本挂钩迁移到高级挂钩
- **`references/advanced.md`** - 高级用例和技术

### 挂钩脚本示例

`examples/`中的工作示例：

- **`validate-write.sh`** - 文件写入验证示例
- **`validate-bash.sh`** - Bash 命令验证示例
- **`load-context.sh`** - SessionStart 上下文加载示例

### 实用脚本

`scripts/`中的开发工具：

- **`validate-hook-schema.sh`** - 验证 hooks.json 结构和语法
- **`test-hook.sh`** - 在部署之前使用示例输入测试挂钩
- **`hook-linter.sh`** - 检查挂钩脚本的常见问题和最佳实践

### 外部资源

- **官方文档**：https://docs.claude.com/en/docs/claude-code/hooks
- **示例**：查看市场中的安全指导插件
- **测试**：使用`claude --debug`获取详细日志
- **验证**：使用`jq`验证挂钩JSON输出

## 实施工作流程

要在插件中实现钩子：

1. 识别要挂钩的事件（PreToolUse、停止、SessionStart 等）
2. 在基于提示（灵活）或命令（确定性）挂钩之间做出决定
3. 在`hooks/hooks.json`中写入钩子配置
4. 对于命令挂钩，创建挂钩脚本
5. 对所有文件引用使用 ${CLAUDE_PLUGIN_ROOT}
6. 使用 `scripts/validate-hook-schema.sh hooks/hooks.json` 验证配置
7. 部署前使用 `scripts/test-hook.sh` 测试挂钩
8. 使用 `claude --debug` 在 Claude Code 中进行测试
9. 插件中的文档挂钩 README

对于大多数用例，重点关注基于提示的挂钩。保留命令挂钩以进行性能关键或确定性检查。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hook Development for Claude Code Plugins

## Overview

Hooks are event-driven automation scripts that execute in response to Claude Code events. Use hooks to validate operations, enforce policies, add context, and integrate external tools into workflows.

**Key capabilities:**
- Validate tool calls before execution (PreToolUse)
- React to tool results (PostToolUse)
- Enforce completion standards (Stop, SubagentStop)
- Load project context (SessionStart)
- Automate workflows across the development lifecycle

## Hook Types

### Prompt-Based Hooks (Recommended)

Use LLM-driven decision making for context-aware validation:

```json
{
  "type": "prompt",
  "prompt": "Evaluate if this tool use is appropriate: $TOOL_INPUT",
  "timeout": 30
}
```

**Supported events:** Stop, SubagentStop, UserPromptSubmit, PreToolUse

**Benefits:**
- Context-aware decisions based on natural language reasoning
- Flexible evaluation logic without bash scripting
- Better edge case handling
- Easier to maintain and extend

### Command Hooks

Execute bash commands for deterministic checks:

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
  "timeout": 60
}
```

**Use for:**
- Fast deterministic validations
- File system operations
- External tool integrations
- Performance-critical checks

## Hook Configuration Formats

### Plugin hooks.json Format

**For plugin hooks** in `hooks/hooks.json`, use wrapper format:

```json
{
  "description": "Brief explanation of hooks (optional)",
  "hooks": {
    "PreToolUse": [...],
    "Stop": [...],
    "SessionStart": [...]
  }
}
```

**Key points:**
- `description` field is optional
- `hooks` field is required wrapper containing actual hook events
- This is the **plugin-specific format**

**Example:**
```json
{
  "description": "Validation hooks for code quality",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/hooks/validate.sh"
          }
        ]
      }
    ]
  }
}
```

### Settings Format (Direct)

**For user settings** in `.claude/settings.json`, use direct format:

```json
{
  "PreToolUse": [...],
  "Stop": [...],
  "SessionStart": [...]
}
```

**Key points:**
- No wrapper - events directly at top level
- No description field
- This is the **settings format**

**Important:** The examples below show the hook event structure that goes inside either format. For plugin hooks.json, wrap these in `{"hooks": {...}}`.

## Hook Events

### PreToolUse

Execute before any tool runs. Use to approve, deny, or modify tool calls.

**Example (prompt-based):**
```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Validate file write safety. Check: system paths, credentials, path traversal, sensitive content. Return 'approve' or 'deny'."
        }
      ]
    }
  ]
}
```

**Output for PreToolUse:**
```json
{
  "hookSpecificOutput": {
    "permissionDecision": "allow|deny|ask",
    "updatedInput": {"field": "modified_value"}
  },
  "systemMessage": "Explanation for Claude"
}
```

### PostToolUse

Execute after tool completes. Use to react to results, provide feedback, or log.

**Example:**
```json
{
  "PostToolUse": [
    {
      "matcher": "Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Analyze edit result for potential issues: syntax errors, security vulnerabilities, breaking changes. Provide feedback."
        }
      ]
    }
  ]
}
```

**Output behavior:**
- Exit 0: stdout shown in transcript
- Exit 2: stderr fed back to Claude
- systemMessage included in context

### Stop

Execute when main agent considers stopping. Use to validate completeness.

**Example:**
```json
{
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Verify task completion: tests run, build succeeded, questions answered. Return 'approve' to stop or 'block' with reason to continue."
        }
      ]
    }
  ]
}
```

**Decision output:**
```json
{
  "decision": "approve|block",
  "reason": "Explanation",
  "systemMessage": "Additional context"
}
```

### SubagentStop

Execute when subagent considers stopping. Use to ensure subagent completed its task.

Similar to Stop hook, but for subagents.

### UserPromptSubmit

Execute when user submits a prompt. Use to add context, validate, or block prompts.

**Example:**
```json
{
  "UserPromptSubmit": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Check if prompt requires security guidance. If discussing auth, permissions, or API security, return relevant warnings."
        }
      ]
    }
  ]
}
```

### SessionStart

Execute when Claude Code session begins. Use to load context and set environment.

**Example:**
```json
{
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/load-context.sh"
        }
      ]
    }
  ]
}
```

**Special capability:** Persist environment variables using `$CLAUDE_ENV_FILE`:
```bash
echo "export PROJECT_TYPE=nodejs" >> "$CLAUDE_ENV_FILE"
```

See `examples/load-context.sh` for complete example.

### SessionEnd

Execute when session ends. Use for cleanup, logging, and state preservation.

### PreCompact

Execute before context compaction. Use to add critical information to preserve.

### Notification

Execute when Claude sends notifications. Use to react to user notifications.

## Hook Output Format

### Standard Output (All Hooks)

```json
{
  "continue": true,
  "suppressOutput": false,
  "systemMessage": "Message for Claude"
}
```

- `continue`: If false, halt processing (default true)
- `suppressOutput`: Hide output from transcript (default false)
- `systemMessage`: Message shown to Claude

### Exit Codes

- `0` - Success (stdout shown in transcript)
- `2` - Blocking error (stderr fed back to Claude)
- Other - Non-blocking error

## Hook Input Format

All hooks receive JSON via stdin with common fields:

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.txt",
  "cwd": "/current/working/dir",
  "permission_mode": "ask|allow",
  "hook_event_name": "PreToolUse"
}
```

**Event-specific fields:**

- **PreToolUse/PostToolUse:** `tool_name`, `tool_input`, `tool_result`
- **UserPromptSubmit:** `user_prompt`
- **Stop/SubagentStop:** `reason`

Access fields in prompts using `$TOOL_INPUT`, `$TOOL_RESULT`, `$USER_PROMPT`, etc.

## Environment Variables

Available in all command hooks:

- `$CLAUDE_PROJECT_DIR` - Project root path
- `$CLAUDE_PLUGIN_ROOT` - Plugin directory (use for portable paths)
- `$CLAUDE_ENV_FILE` - SessionStart only: persist env vars here
- `$CLAUDE_CODE_REMOTE` - Set if running in remote context

**Always use ${CLAUDE_PLUGIN_ROOT} in hook commands for portability:**

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
}
```

## Plugin Hook Configuration

In plugins, define hooks in `hooks/hooks.json`:

```json
{
  "PreToolUse": [
    {
      "matcher": "Write|Edit",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Validate file write safety"
        }
      ]
    }
  ],
  "Stop": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "prompt",
          "prompt": "Verify task completion"
        }
      ]
    }
  ],
  "SessionStart": [
    {
      "matcher": "*",
      "hooks": [
        {
          "type": "command",
          "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/load-context.sh",
          "timeout": 10
        }
      ]
    }
  ]
}
```

Plugin hooks merge with user's hooks and run in parallel.

## Matchers

### Tool Name Matching

**Exact match:**
```json
"matcher": "Write"
```

**Multiple tools:**
```json
"matcher": "Read|Write|Edit"
```

**Wildcard (all tools):**
```json
"matcher": "*"
```

**Regex patterns:**
```json
"matcher": "mcp__.*__delete.*"  // All MCP delete tools
```

**Note:** Matchers are case-sensitive.

### Common Patterns

```json
// All MCP tools
"matcher": "mcp__.*"

// Specific plugin's MCP tools
"matcher": "mcp__plugin_asana_.*"

// All file operations
"matcher": "Read|Write|Edit"

// Bash commands only
"matcher": "Bash"
```

## Security Best Practices

### Input Validation

Always validate inputs in command hooks:

```bash
#!/bin/bash
set -euo pipefail

input=$(cat)
tool_name=$(echo "$input" | jq -r '.tool_name')

# Validate tool name format
if [[ ! "$tool_name" =~ ^[a-zA-Z0-9_]+$ ]]; then
  echo '{"decision": "deny", "reason": "Invalid tool name"}' >&2
  exit 2
fi
```

### Path Safety

Check for path traversal and sensitive files:

```bash
file_path=$(echo "$input" | jq -r '.tool_input.file_path')

# Deny path traversal
if [[ "$file_path" == *".."* ]]; then
  echo '{"decision": "deny", "reason": "Path traversal detected"}' >&2
  exit 2
fi

# Deny sensitive files
if [[ "$file_path" == *".env"* ]]; then
  echo '{"decision": "deny", "reason": "Sensitive file"}' >&2
  exit 2
fi
```

See `examples/validate-write.sh` and `examples/validate-bash.sh` for complete examples.

### Quote All Variables

```bash
# GOOD: Quoted
echo "$file_path"
cd "$CLAUDE_PROJECT_DIR"

# BAD: Unquoted (injection risk)
echo $file_path
cd $CLAUDE_PROJECT_DIR
```

### Set Appropriate Timeouts

```json
{
  "type": "command",
  "command": "bash script.sh",
  "timeout": 10
}
```

**Defaults:** Command hooks (60s), Prompt hooks (30s)

## Performance Considerations

### Parallel Execution

All matching hooks run **in parallel**:

```json
{
  "PreToolUse": [
    {
      "matcher": "Write",
      "hooks": [
        {"type": "command", "command": "check1.sh"},  // Parallel
        {"type": "command", "command": "check2.sh"},  // Parallel
        {"type": "prompt", "prompt": "Validate..."}   // Parallel
      ]
    }
  ]
}
```

**Design implications:**
- Hooks don't see each other's output
- Non-deterministic ordering
- Design for independence

### Optimization

1. Use command hooks for quick deterministic checks
2. Use prompt hooks for complex reasoning
3. Cache validation results in temp files
4. Minimize I/O in hot paths

## Temporarily Active Hooks

Create hooks that activate conditionally by checking for a flag file or configuration:

**Pattern: Flag file activation**
```bash
#!/bin/bash
# Only active when flag file exists
FLAG_FILE="$CLAUDE_PROJECT_DIR/.enable-strict-validation"

if [ ! -f "$FLAG_FILE" ]; then
  # Flag not present, skip validation
  exit 0
fi

# Flag present, run validation
input=$(cat)
# ... validation logic ...
```

**Pattern: Configuration-based activation**
```bash
#!/bin/bash
# Check configuration for activation
CONFIG_FILE="$CLAUDE_PROJECT_DIR/.claude/plugin-config.json"

if [ -f "$CONFIG_FILE" ]; then
  enabled=$(jq -r '.strictMode // false' "$CONFIG_FILE")
  if [ "$enabled" != "true" ]; then
    exit 0  # Not enabled, skip
  fi
fi

# Enabled, run hook logic
input=$(cat)
# ... hook logic ...
```

**Use cases:**
- Enable strict validation only when needed
- Temporary debugging hooks
- Project-specific hook behavior
- Feature flags for hooks

**Best practice:** Document activation mechanism in plugin README so users know how to enable/disable temporary hooks.

## Hook Lifecycle and Limitations

### Hooks Load at Session Start

**Important:** Hooks are loaded when Claude Code session starts. Changes to hook configuration require restarting Claude Code.

**Cannot hot-swap hooks:**
- Editing `hooks/hooks.json` won't affect current session
- Adding new hook scripts won't be recognized
- Changing hook commands/prompts won't update
- Must restart Claude Code: exit and run `claude` again

**To test hook changes:**
1. Edit hook configuration or scripts
2. Exit Claude Code session
3. Restart: `claude` or `cc`
4. New hook configuration loads
5. Test hooks with `claude --debug`

### Hook Validation at Startup

Hooks are validated when Claude Code starts:
- Invalid JSON in hooks.json causes loading failure
- Missing scripts cause warnings
- Syntax errors reported in debug mode

Use `/hooks` command to review loaded hooks in current session.

## Debugging Hooks

### Enable Debug Mode

```bash
claude --debug
```

Look for hook registration, execution logs, input/output JSON, and timing information.

### Test Hook Scripts

Test command hooks directly:

```bash
echo '{"tool_name": "Write", "tool_input": {"file_path": "/test"}}' | \
  bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh

echo "Exit code: $?"
```

### Validate JSON Output

Ensure hooks output valid JSON:

```bash
output=$(./your-hook.sh < test-input.json)
echo "$output" | jq .
```

## Quick Reference

### Hook Events Summary

| Event | When | Use For |
|-------|------|---------|
| PreToolUse | Before tool | Validation, modification |
| PostToolUse | After tool | Feedback, logging |
| UserPromptSubmit | User input | Context, validation |
| Stop | Agent stopping | Completeness check |
| SubagentStop | Subagent done | Task validation |
| SessionStart | Session begins | Context loading |
| SessionEnd | Session ends | Cleanup, logging |
| PreCompact | Before compact | Preserve context |
| Notification | User notified | Logging, reactions |

### Best Practices

**DO:**
- ✅ Use prompt-based hooks for complex logic
- ✅ Use ${CLAUDE_PLUGIN_ROOT} for portability
- ✅ Validate all inputs in command hooks
- ✅ Quote all bash variables
- ✅ Set appropriate timeouts
- ✅ Return structured JSON output
- ✅ Test hooks thoroughly

**DON'T:**
- ❌ Use hardcoded paths
- ❌ Trust user input without validation
- ❌ Create long-running hooks
- ❌ Rely on hook execution order
- ❌ Modify global state unpredictably
- ❌ Log sensitive information

## Additional Resources

### Reference Files

For detailed patterns and advanced techniques, consult:

- **`references/patterns.md`** - Common hook patterns (8+ proven patterns)
- **`references/migration.md`** - Migrating from basic to advanced hooks
- **`references/advanced.md`** - Advanced use cases and techniques

### Example Hook Scripts

Working examples in `examples/`:

- **`validate-write.sh`** - File write validation example
- **`validate-bash.sh`** - Bash command validation example
- **`load-context.sh`** - SessionStart context loading example

### Utility Scripts

Development tools in `scripts/`:

- **`validate-hook-schema.sh`** - Validate hooks.json structure and syntax
- **`test-hook.sh`** - Test hooks with sample input before deployment
- **`hook-linter.sh`** - Check hook scripts for common issues and best practices

### External Resources

- **Official Docs**: https://docs.claude.com/en/docs/claude-code/hooks
- **Examples**: See security-guidance plugin in marketplace
- **Testing**: Use `claude --debug` for detailed logs
- **Validation**: Use `jq` to validate hook JSON output

## Implementation Workflow

To implement hooks in a plugin:

1. Identify events to hook into (PreToolUse, Stop, SessionStart, etc.)
2. Decide between prompt-based (flexible) or command (deterministic) hooks
3. Write hook configuration in `hooks/hooks.json`
4. For command hooks, create hook scripts
5. Use ${CLAUDE_PLUGIN_ROOT} for all file references
6. Validate configuration with `scripts/validate-hook-schema.sh hooks/hooks.json`
7. Test hooks with `scripts/test-hook.sh` before deployment
8. Test in Claude Code with `claude --debug`
9. Document hooks in plugin README

Focus on prompt-based hooks for most use cases. Reserve command hooks for performance-critical or deterministic checks.


</details>
