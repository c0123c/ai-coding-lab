# Hookify 插件说明

介绍从对话或明确要求创建规则、防止重复问题的插件，涵盖快速开始、命令、事件和测试方式。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/hookify/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Hookify 插件说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Hookify 插件

通过分析对话模式或明确的指令，轻松创建自定义挂钩以防止不良行为。

## 概述

hookify 插件可以轻松创建挂钩，而无需编辑复杂的 `hooks.json` 文件。相反，您可以创建轻量级 Markdown 配置文件，用于定义要监视的模式以及这些模式匹配时要显示的消息。

**主要特点：**
- 🎯 分析对话以自动发现不需要的行为
- 📝 带有 YAML frontmatter 的简单 markdown 配置文件
- 🔍 正则表达式模式匹配强大的规则
- 🚀 无需编码 - 只需描述行为
- 🔄 简单【enable/disable】无需重启

## 快速入门

### 1。创建您的第一条规则

```bash
/hookify Warn me when I use rm -rf commands
```

这会分析您的请求并创建 `.claude/hookify.warn-rm.local.md`。

### 2。立即测试

**无需重启！**规则在下次使用工具时生效。

要求 Claude 运行应触发规则的命令：
```
Run rm -rf /tmp/test
```

您应该立即看到警告消息！

## 用途

### 主命令：/hookify

**带参数：**
```
/hookify Don't use console.log in TypeScript files
```
根据您的明确指示创建规则。

**无参数：**
```
/hookify
```
分析最近的对话，找出您已纠正或感到沮丧的行为。

### 辅助命令

**列出所有规则：**
```
/hookify:list
```

**交互式配置规则：**
```
/hookify:configure
```
Enable/disable 通过交互界面现有规则。

**获取帮助：**
```
/hookify:help
```

## 规则配置格式

### 简单规则（单一模式）

`.claude/hookify.dangerous-rm.local.md`:
```markdown
---
name: block-dangerous-rm
enabled: true
event: bash
pattern: rm\s+-rf
action: block
---

⚠️ **Dangerous rm command detected!**

This command could delete important files. Please:
- Verify the path is correct
- Consider using a safer approach
- Make sure you have backups
```

**动作字段：**
- `warn`：显示警告但允许操作（默认）
- `block`：阻止操作执行（PreToolUse）或停止会话（停止事件）

### 高级规则（多个条件）

`.claude/hookify.sensitive-files.local.md`:
```markdown
---
name: warn-sensitive-files
enabled: true
event: file
action: warn
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$|credentials|secrets
  - field: new_text
    operator: contains
    pattern: KEY
---

🔐 **Sensitive file edit detected!**

Ensure credentials are not hardcoded and file is in .gitignore.
```

**所有条件必须与 ** 匹配才能触发规则。

## 事件类型

- **`bash`**：Bash 工具命令上的触发器
- **`file`**：在编辑、写入、MultiEdit 工具上触发
- **`stop`**：当Claude想要停止时触发（用于完成检查）
- **`prompt`**：用户提示提交时触发
- **`all`**：在所有事件上触发

## 模式语法

使用 Python 正则表达式语法：

|图案|火柴|示例|
|---------|---------|---------|
| `rm\s+-rf` |射频|rm -rf /tmp|
| `console\.log\(` |控制台.日志（|控制台.log(“测试”)|
| `(eval\|exec)\(` |评估（或执行（|评估（“代码”）|
| `\.env$` |以 .env 结尾的文件|.env、.env.local|
| `chmod\s+777` |chmod 777|chmod 777 file.txt|

**提示：**
- 使用 `\s` 作为空白
- 转义特殊字符：`\.` 用于文字点
- 将 `|` 用于 OR：`(foo|bar)`
- 使用 `.*` 匹配任何内容
- 设置`action: block`进行危险操作
- 设置 `action: warn`（或省略）以获取信息警告

## 示例

### 示例1：阻止危险命令

```markdown
---
name: block-destructive-ops
enabled: true
event: bash
pattern: rm\s+-rf|dd\s+if=|mkfs|format
action: block
---

🛑 **Destructive operation detected!**

This command can cause data loss. Operation blocked for safety.
Please verify the exact path and use a safer approach.
```

**此规则阻止操作** - Claude将不允许执行这些命令。

### 示例 2：关于调试代码的警告

```markdown
---
name: warn-debug-code
enabled: true
event: file
pattern: console\.log\(|debugger;|print\(
action: warn
---

🐛 **Debug code detected**

Remember to remove debugging statements before committing.
```

**此规则发出警告，但允许** - Claude 看到该消息，但仍可以继续。

### 示例 3：停止前需要测试

```markdown
---
name: require-tests-run
enabled: false
event: stop
action: block
conditions:
  - field: transcript
    operator: not_contains
    pattern: npm test|pytest|cargo test
---

**Tests not detected in transcript!**

Before stopping, please run tests to verify your changes work correctly.
```

**如果会话记录中没有出现测试命令，这会阻止Claude停止**。仅当您需要严格执行时才启用。

## 高级用法

### 多种条件

同时检查多个字段：

```markdown
---
name: api-key-in-typescript
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.tsx?$
  - field: new_text
    operator: regex_match
    pattern: (API_KEY|SECRET|TOKEN)\s*=\s*["']
---

🔐 **Hardcoded credential in TypeScript!**

Use environment variables instead of hardcoded values.
```

### 操作参考

- `regex_match`：模式必须匹配（最常见）
- `contains`：字符串必须包含模式
- `equals`：精确字符串匹配
- `not_contains`：字符串必须 NOT 包含模式
- `starts_with`：字符串以模式开头
- `ends_with`：字符串以模式结尾

### 现场参考

**对于 bash 事件：**
- `command`：bash命令字符串

**对于文件事件：**
- `file_path`：正在编辑的文件的路径
- `new_text`：正在添加新内容（编辑、写入）
- `old_text`：旧内容被替换（仅限编辑）
- `content`：文件内容（只写）

**对于提示事件：**
- `user_prompt`：用户提交的提示文字

**对于停止事件：**
- 对会话状态使用一般匹配

## 管理

### Enable/Disable 规则

**暂时禁用：**
编辑`.local.md`文件并设置`enabled: false`

**重新启用：**
设置`enabled: true`

**或使用交互工具：**
```
/hookify:configure
```

### 删除规则

只需删除 `.local.md` 文件即可：
```bash
rm .claude/hookify.my-rule.local.md
```

### 查看所有规则

```
/hookify:list
```

## 安装

该插件是 Claude Code 市场的一部分。安装市场时应该会自动发现它。

**手动测试：**
```bash
cc --plugin-dir /path/to/hookify
```

## 要求

- Python 3.7+
- 没有外部依赖项（仅使用 stdlib）

## 故障排除

**规则未触发：**
1. 检查 `.claude/` 目录中是否存在规则文件（在项目根目录中，而不是插件目录中）
2. 验证 frontmatter 中的 `enabled: true`
3. 单独测试正则表达式模式
4. 规则应立即生效 - 无需重新启动
5. 尝试 `/hookify:list` 查看规则是否已加载

**导入错误：**
- 确保 Python 3 可用：`python3 --version`
- 检查hookify插件是否安装

**模式不匹配：**
- 测试正则表达式：`python3 -c "import re; print(re.search(r'pattern', 'text'))"`
- 在 YAML 中使用不带引号的模式以避免转义问题
- 从简单开始，然后增加复杂性

**Hook 似乎很慢：**
- 保持模式简单（避免复杂的正则表达式）
- 使用特定事件类型（bash、文件）而不是“全部”
- 限制活动规则的数量

## 贡献

找到有用的规则模式？考虑通过 PR 共享示例文件！

## 未来的增强功能

- 严重级别（error/warning/info 区别）
- 规则模板库
- 交互式模式生成器
- 钩子测试实用程序
- JSON 格式支持（除了markdown）

## 许可证

MIT 许可证


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hookify Plugin

Easily create custom hooks to prevent unwanted behaviors by analyzing conversation patterns or from explicit instructions.

## Overview

The hookify plugin makes it simple to create hooks without editing complex `hooks.json` files. Instead, you create lightweight markdown configuration files that define patterns to watch for and messages to show when those patterns match.

**Key features:**
- 🎯 Analyze conversations to find unwanted behaviors automatically
- 📝 Simple markdown configuration files with YAML frontmatter
- 🔍 Regex pattern matching for powerful rules
- 🚀 No coding required - just describe the behavior
- 🔄 Easy enable/disable without restarting

## Quick Start

### 1. Create Your First Rule

```bash
/hookify Warn me when I use rm -rf commands
```

This analyzes your request and creates `.claude/hookify.warn-rm.local.md`.

### 2. Test It Immediately

**No restart needed!** Rules take effect on the very next tool use.

Ask Claude to run a command that should trigger the rule:
```
Run rm -rf /tmp/test
```

You should see the warning message immediately!

## Usage

### Main Command: /hookify

**With arguments:**
```
/hookify Don't use console.log in TypeScript files
```
Creates a rule from your explicit instructions.

**Without arguments:**
```
/hookify
```
Analyzes recent conversation to find behaviors you've corrected or been frustrated by.

### Helper Commands

**List all rules:**
```
/hookify:list
```

**Configure rules interactively:**
```
/hookify:configure
```
Enable/disable existing rules through an interactive interface.

**Get help:**
```
/hookify:help
```

## Rule Configuration Format

### Simple Rule (Single Pattern)

`.claude/hookify.dangerous-rm.local.md`:
```markdown
---
name: block-dangerous-rm
enabled: true
event: bash
pattern: rm\s+-rf
action: block
---

⚠️ **Dangerous rm command detected!**

This command could delete important files. Please:
- Verify the path is correct
- Consider using a safer approach
- Make sure you have backups
```

**Action field:**
- `warn`: Shows warning but allows operation (default)
- `block`: Prevents operation from executing (PreToolUse) or stops session (Stop events)

### Advanced Rule (Multiple Conditions)

`.claude/hookify.sensitive-files.local.md`:
```markdown
---
name: warn-sensitive-files
enabled: true
event: file
action: warn
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$|credentials|secrets
  - field: new_text
    operator: contains
    pattern: KEY
---

🔐 **Sensitive file edit detected!**

Ensure credentials are not hardcoded and file is in .gitignore.
```

**All conditions must match** for the rule to trigger.

## Event Types

- **`bash`**: Triggers on Bash tool commands
- **`file`**: Triggers on Edit, Write, MultiEdit tools
- **`stop`**: Triggers when Claude wants to stop (for completion checks)
- **`prompt`**: Triggers on user prompt submission
- **`all`**: Triggers on all events

## Pattern Syntax

Use Python regex syntax:

| Pattern | Matches | Example |
|---------|---------|---------|
| `rm\s+-rf` | rm -rf | rm -rf /tmp |
| `console\.log\(` | console.log( | console.log("test") |
| `(eval\|exec)\(` | eval( or exec( | eval("code") |
| `\.env$` | files ending in .env | .env, .env.local |
| `chmod\s+777` | chmod 777 | chmod 777 file.txt |

**Tips:**
- Use `\s` for whitespace
- Escape special chars: `\.` for literal dot
- Use `|` for OR: `(foo|bar)`
- Use `.*` to match anything
- Set `action: block` for dangerous operations
- Set `action: warn` (or omit) for informational warnings

## Examples

### Example 1: Block Dangerous Commands

```markdown
---
name: block-destructive-ops
enabled: true
event: bash
pattern: rm\s+-rf|dd\s+if=|mkfs|format
action: block
---

🛑 **Destructive operation detected!**

This command can cause data loss. Operation blocked for safety.
Please verify the exact path and use a safer approach.
```

**This rule blocks the operation** - Claude will not be allowed to execute these commands.

### Example 2: Warn About Debug Code

```markdown
---
name: warn-debug-code
enabled: true
event: file
pattern: console\.log\(|debugger;|print\(
action: warn
---

🐛 **Debug code detected**

Remember to remove debugging statements before committing.
```

**This rule warns but allows** - Claude sees the message but can still proceed.

### Example 3: Require Tests Before Stopping

```markdown
---
name: require-tests-run
enabled: false
event: stop
action: block
conditions:
  - field: transcript
    operator: not_contains
    pattern: npm test|pytest|cargo test
---

**Tests not detected in transcript!**

Before stopping, please run tests to verify your changes work correctly.
```

**This blocks Claude from stopping** if no test commands appear in the session transcript. Enable only when you want strict enforcement.

## Advanced Usage

### Multiple Conditions

Check multiple fields simultaneously:

```markdown
---
name: api-key-in-typescript
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.tsx?$
  - field: new_text
    operator: regex_match
    pattern: (API_KEY|SECRET|TOKEN)\s*=\s*["']
---

🔐 **Hardcoded credential in TypeScript!**

Use environment variables instead of hardcoded values.
```

### Operators Reference

- `regex_match`: Pattern must match (most common)
- `contains`: String must contain pattern
- `equals`: Exact string match
- `not_contains`: String must NOT contain pattern
- `starts_with`: String starts with pattern
- `ends_with`: String ends with pattern

### Field Reference

**For bash events:**
- `command`: The bash command string

**For file events:**
- `file_path`: Path to file being edited
- `new_text`: New content being added (Edit, Write)
- `old_text`: Old content being replaced (Edit only)
- `content`: File content (Write only)

**For prompt events:**
- `user_prompt`: The user's submitted prompt text

**For stop events:**
- Use general matching on session state

## Management

### Enable/Disable Rules

**Temporarily disable:**
Edit the `.local.md` file and set `enabled: false`

**Re-enable:**
Set `enabled: true`

**Or use interactive tool:**
```
/hookify:configure
```

### Delete Rules

Simply delete the `.local.md` file:
```bash
rm .claude/hookify.my-rule.local.md
```

### View All Rules

```
/hookify:list
```

## Installation

This plugin is part of the Claude Code Marketplace. It should be auto-discovered when the marketplace is installed.

**Manual testing:**
```bash
cc --plugin-dir /path/to/hookify
```

## Requirements

- Python 3.7+
- No external dependencies (uses stdlib only)

## Troubleshooting

**Rule not triggering:**
1. Check rule file exists in `.claude/` directory (in project root, not plugin directory)
2. Verify `enabled: true` in frontmatter
3. Test regex pattern separately
4. Rules should work immediately - no restart needed
5. Try `/hookify:list` to see if rule is loaded

**Import errors:**
- Ensure Python 3 is available: `python3 --version`
- Check hookify plugin is installed

**Pattern not matching:**
- Test regex: `python3 -c "import re; print(re.search(r'pattern', 'text'))"`
- Use unquoted patterns in YAML to avoid escaping issues
- Start simple, then add complexity

**Hook seems slow:**
- Keep patterns simple (avoid complex regex)
- Use specific event types (bash, file) instead of "all"
- Limit number of active rules

## Contributing

Found a useful rule pattern? Consider sharing example files via PR!

## Future Enhancements

- Severity levels (error/warning/info distinctions)
- Rule templates library
- Interactive pattern builder
- Hook testing utilities
- JSON format support (in addition to markdown)

## License

MIT License


</details>
