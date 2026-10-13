# 编写 Hookify 规则

说明规则文件、条件字段、匹配表达式、事件和消息体的格式，配合案例展示怎样约束工具操作；属于官方插件中的技能实现资料。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/anthropics/claude-code/main/plugins/hookify/skills/writing-rules/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/编写 Hookify 规则.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: Writing Hookify Rules
description: This skill should be used when the user asks to "create a hookify rule", "write a hook rule", "configure hookify", "add a hookify rule", or needs guidance on hookify rule syntax and patterns.
version: 0.1.0
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 编写 Hookify 规则

## 概述

Hookify 规则是带有 YAML frontmatter 的 markdown 文件，用于定义要监视的模式以及这些模式匹配时要显示的消息。规则存储在`.claude/hookify.{rule-name}.local.md`文件中。

## 规则文件格式

### 基本结构

```markdown
---
name: rule-identifier
enabled: true
event: bash|file|stop|prompt|all
pattern: regex-pattern-here
---

Message to show Claude when this rule triggers.
Can include markdown formatting, warnings, suggestions, etc.
```

### 前沿领域

**名称**（必需）：规则的唯一标识符
- 使用 kebab-case: `warn-dangerous-rm`, `block-console-log`
- 具有描述性和行动导向
- 以动词开头：警告、防止、阻止、要求、检查

**启用**（必需）：布尔值到activate/deactivate
- `true`：规则已激活
- `false`：规则已禁用（不会触发）
- 可以切换而不删除规则

**event**（必填）：触发哪个钩子事件
- `bash`：Bash 工具命令
- `file`：编辑、写入、MultiEdit工具
- `stop`：当agent想要停止时
- `prompt`：当用户提交提示时
- `all`：所有事件

**action**（可选）：规则匹配时做什么
- `warn`：显示消息但允许操作（默认）
- `block`：阻止操作（PreToolUse）或停止会话（停止事件）
- 如果省略，则默认为 `warn`

**pattern**（简单格式）：要匹配的正则表达式模式
- 用于简单的单一条件规则
- 与命令 (bash) 或 new_text（文件）匹配
- Python 正则表达式语法

**示例：**
```yaml
event: bash
pattern: rm\s+-rf
```

### 高级格式（多种条件）

对于具有多个条件的复杂规则：

```markdown
---
name: warn-env-file-edits
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

You're adding an API key to a .env file. Ensure this file is in .gitignore!
```

**条件字段：**
- `field`：检查哪个字段
  - 对于 bash：`command`
  - 对于文件：`file_path`、`new_text`、`old_text`、`content`
- `operator`：如何搭配
  - `regex_match`：正则表达式模式匹配
  - `contains`：子串检查
  - `equals`：完全匹配
  - `not_contains`：子字符串必须存在 NOT
  - `starts_with`：前缀检查
  - `ends_with`：后缀检查
- `pattern`：要匹配的模式或字符串

**所有条件必须匹配才能触发规则。**

## 消息正文

当规则触发时，frontmatter 之后的 markdown 内容会显示为 Claude。

**好消息：**
- 解释检测到的内容
- 解释为什么有问题
- 建议替代方案或最佳实践
- 为了清晰起见，使用格式（粗体、列表等）

**示例：**
```markdown
⚠️ **Console.log detected!**

You're adding console.log to production code.

**Why this matters:**
- Debug logs shouldn't ship to production
- Console.log can expose sensitive data
- Impacts browser performance

**Alternatives:**
- Use a proper logging library
- Remove before committing
- Use conditional debug builds
```

## 活动类型指南

### 庆典活动

匹配 Bash 命令模式：

```markdown
---
event: bash
pattern: sudo\s+|rm\s+-rf|chmod\s+777
---

Dangerous command detected!
```

**常见图案：**
- 危险命令：`rm\s+-rf`、`dd\s+if=`、`mkfs`
- 权限提升：`sudo\s+`、`su\s+`
- 权限问题：`chmod\s+777`、`chown\s+root`

### 文件事件

匹配Edit/Write/MultiEdit操作：

```markdown
---
event: file
pattern: console\.log\(|eval\(|innerHTML\s*=
---

Potentially problematic code pattern detected!
```

**不同领域的匹配：**
```markdown
---
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.tsx?$
  - field: new_text
    operator: regex_match
    pattern: console\.log\(
---

Console.log in TypeScript file!
```

**常见图案：**
- 调试代码：`console\.log\(`、`debugger`、`print\(`
- 安全风险：`eval\(`、`innerHTML\s*=`、`dangerouslySetInnerHTML`
- 敏感文件：`\.env$`、`credentials`、`\.pem$`
- 生成的文件：`node_modules/`、`dist/`、`build/`

### 停止活动

当 agent 想要停止时匹配（完成检查）：

```markdown
---
event: stop
pattern: .*
---

Before stopping, verify:
- [ ] Tests were run
- [ ] Build succeeded
- [ ] Documentation updated
```

**用于：**
- 有关所需步骤的提醒
- 完成清单
- 流程执行

### 提示事件

匹配用户提示内容（高级）：

```markdown
---
event: prompt
conditions:
  - field: user_prompt
    operator: contains
    pattern: deploy to production
---

Production deployment checklist:
- [ ] Tests passing?
- [ ] Reviewed by team?
- [ ] Monitoring ready?
```

## 模式写作技巧

### 正则表达式基础知识

**文字字符：**大多数字符与自身匹配
- `rm` 匹配“rm”
- `console.log` 匹配“console.log”

**特殊字符需要转义：**
- `.`（任何字符）→ `\.`（文字点）
- `(` `)` → `\(` `\)`（文字括号）
- `[` `]` → `\[` `\]`（文字括号）

**常用元字符：**
- `\s` - 空白（空格、制表符、换行符）
- `\d` - 数字 (0-9)
- `\w` - 单词字符（a-z、A-Z、0-9、_）
- `.` - 任何字符
- `+` - 一个或多个
- `*` - 零个或多个
- `?` - 零或一
- `|` - OR

**示例：**
```
rm\s+-rf         Matches: rm -rf, rm  -rf
console\.log\(   Matches: console.log(
(eval|exec)\(    Matches: eval( or exec(
chmod\s+777      Matches: chmod 777, chmod  777
API_KEY\s*=      Matches: API_KEY=, API_KEY =
```

### 测试模式

使用前测试正则表达式模式：

```bash
python3 -c "import re; print(re.search(r'your_pattern', 'test text'))"
```

或者使用在线正则表达式测试器（带有 Python 风格的 regex101.com）。

### 常见陷阱

**太宽泛：**
```yaml
pattern: log    # Matches "log", "login", "dialog", "catalog"
```
更好：`console\.log\(|logger\.`

**太具体：**
```yaml
pattern: rm -rf /tmp  # Only matches exact path
```
更好：`rm\s+-rf`

**转义问题：**
- YAML 带引号的字符串：`"pattern"` 需要双反斜杠 `\\s`
- YAML 未引用：`pattern: \s` 按原样工作
- **建议**：在YAML中使用不带引号的模式

## 文件组织

**位置：**`.claude/`目录中的所有规则
**命名：**`.claude/hookify.{descriptive-name}.local.md`
**Gitignore：**添加`.claude/*.local.md`到`.gitignore`

**好名字：**
- `hookify.dangerous-rm.local.md`
- `hookify.console-log.local.md`
- `hookify.require-tests.local.md`
- `hookify.sensitive-files.local.md`

**错误名称：**
- `hookify.rule1.local.md`（非描述性）
- `hookify.md`（缺少.local）
- `danger.local.md`（缺少 hookify 前缀）

## 工作流程

### 创建规则

1. 识别不良行为
2. 确定涉及哪个工具（Bash、Edit 等）
3. 选择事件类型（bash、文件、停止等）
4. 编写正则表达式模式
5. 在项目根目录中创建`.claude/hookify.{name}.local.md`文件
6. 立即测试 - 下次使用工具时动态读取规则

### 细化规则

1. 编辑`.local.md`文件
2. 调整图案或消息
3. 立即测试 - 更改在下次使用工具时生效

### 禁用规则

**临时：**在frontmatter中设置`enabled: false`
**永久：**删除`.local.md`文件

## 示例

有关完整示例，请参阅 `${CLAUDE_PLUGIN_ROOT}/examples/`：
- `dangerous-rm.local.md` - 阻止危险的 rm 命令
- `console-log-warning.local.md` - 关于 console.log 的警告
- `sensitive-files-warning.local.md` - 关于编辑 .env 文件的警告

## 快速参考

**最小可行规则：**
```markdown
---
name: my-rule
enabled: true
event: bash
pattern: dangerous_command
---

Warning message here
```

**条件规则：**
```markdown
---
name: my-rule
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.ts$
  - field: new_text
    operator: contains
    pattern: any
---

Warning message
```

**事件类型：**
- `bash` - Bash 命令
- `file` - 文件编辑
- `stop` - 完成检查
- `prompt` - 用户输入
- `all` - 所有活动

**字段选项：**
- 重击：`command`
- 文件：`file_path`、`new_text`、`old_text`、`content`
- 提示：`user_prompt`

**运营商：**
- `regex_match`, `contains`, `equals`, `not_contains`, `starts_with`, `ends_with`


## 原文对照

<details>
<summary>展开完整原文</summary>

# Writing Hookify Rules

## Overview

Hookify rules are markdown files with YAML frontmatter that define patterns to watch for and messages to show when those patterns match. Rules are stored in `.claude/hookify.{rule-name}.local.md` files.

## Rule File Format

### Basic Structure

```markdown
---
name: rule-identifier
enabled: true
event: bash|file|stop|prompt|all
pattern: regex-pattern-here
---

Message to show Claude when this rule triggers.
Can include markdown formatting, warnings, suggestions, etc.
```

### Frontmatter Fields

**name** (required): Unique identifier for the rule
- Use kebab-case: `warn-dangerous-rm`, `block-console-log`
- Be descriptive and action-oriented
- Start with verb: warn, prevent, block, require, check

**enabled** (required): Boolean to activate/deactivate
- `true`: Rule is active
- `false`: Rule is disabled (won't trigger)
- Can toggle without deleting rule

**event** (required): Which hook event to trigger on
- `bash`: Bash tool commands
- `file`: Edit, Write, MultiEdit tools
- `stop`: When agent wants to stop
- `prompt`: When user submits a prompt
- `all`: All events

**action** (optional): What to do when rule matches
- `warn`: Show message but allow operation (default)
- `block`: Prevent operation (PreToolUse) or stop session (Stop events)
- If omitted, defaults to `warn`

**pattern** (simple format): Regex pattern to match
- Used for simple single-condition rules
- Matches against command (bash) or new_text (file)
- Python regex syntax

**Example:**
```yaml
event: bash
pattern: rm\s+-rf
```

### Advanced Format (Multiple Conditions)

For complex rules with multiple conditions:

```markdown
---
name: warn-env-file-edits
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

You're adding an API key to a .env file. Ensure this file is in .gitignore!
```

**Condition fields:**
- `field`: Which field to check
  - For bash: `command`
  - For file: `file_path`, `new_text`, `old_text`, `content`
- `operator`: How to match
  - `regex_match`: Regex pattern matching
  - `contains`: Substring check
  - `equals`: Exact match
  - `not_contains`: Substring must NOT be present
  - `starts_with`: Prefix check
  - `ends_with`: Suffix check
- `pattern`: Pattern or string to match

**All conditions must match for rule to trigger.**

## Message Body

The markdown content after frontmatter is shown to Claude when the rule triggers.

**Good messages:**
- Explain what was detected
- Explain why it's problematic
- Suggest alternatives or best practices
- Use formatting for clarity (bold, lists, etc.)

**Example:**
```markdown
⚠️ **Console.log detected!**

You're adding console.log to production code.

**Why this matters:**
- Debug logs shouldn't ship to production
- Console.log can expose sensitive data
- Impacts browser performance

**Alternatives:**
- Use a proper logging library
- Remove before committing
- Use conditional debug builds
```

## Event Type Guide

### bash Events

Match Bash command patterns:

```markdown
---
event: bash
pattern: sudo\s+|rm\s+-rf|chmod\s+777
---

Dangerous command detected!
```

**Common patterns:**
- Dangerous commands: `rm\s+-rf`, `dd\s+if=`, `mkfs`
- Privilege escalation: `sudo\s+`, `su\s+`
- Permission issues: `chmod\s+777`, `chown\s+root`

### file Events

Match Edit/Write/MultiEdit operations:

```markdown
---
event: file
pattern: console\.log\(|eval\(|innerHTML\s*=
---

Potentially problematic code pattern detected!
```

**Match on different fields:**
```markdown
---
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.tsx?$
  - field: new_text
    operator: regex_match
    pattern: console\.log\(
---

Console.log in TypeScript file!
```

**Common patterns:**
- Debug code: `console\.log\(`, `debugger`, `print\(`
- Security risks: `eval\(`, `innerHTML\s*=`, `dangerouslySetInnerHTML`
- Sensitive files: `\.env$`, `credentials`, `\.pem$`
- Generated files: `node_modules/`, `dist/`, `build/`

### stop Events

Match when agent wants to stop (completion checks):

```markdown
---
event: stop
pattern: .*
---

Before stopping, verify:
- [ ] Tests were run
- [ ] Build succeeded
- [ ] Documentation updated
```

**Use for:**
- Reminders about required steps
- Completion checklists
- Process enforcement

### prompt Events

Match user prompt content (advanced):

```markdown
---
event: prompt
conditions:
  - field: user_prompt
    operator: contains
    pattern: deploy to production
---

Production deployment checklist:
- [ ] Tests passing?
- [ ] Reviewed by team?
- [ ] Monitoring ready?
```

## Pattern Writing Tips

### Regex Basics

**Literal characters:** Most characters match themselves
- `rm` matches "rm"
- `console.log` matches "console.log"

**Special characters need escaping:**
- `.` (any char) → `\.` (literal dot)
- `(` `)` → `\(` `\)` (literal parens)
- `[` `]` → `\[` `\]` (literal brackets)

**Common metacharacters:**
- `\s` - whitespace (space, tab, newline)
- `\d` - digit (0-9)
- `\w` - word character (a-z, A-Z, 0-9, _)
- `.` - any character
- `+` - one or more
- `*` - zero or more
- `?` - zero or one
- `|` - OR

**Examples:**
```
rm\s+-rf         Matches: rm -rf, rm  -rf
console\.log\(   Matches: console.log(
(eval|exec)\(    Matches: eval( or exec(
chmod\s+777      Matches: chmod 777, chmod  777
API_KEY\s*=      Matches: API_KEY=, API_KEY =
```

### Testing Patterns

Test regex patterns before using:

```bash
python3 -c "import re; print(re.search(r'your_pattern', 'test text'))"
```

Or use online regex testers (regex101.com with Python flavor).

### Common Pitfalls

**Too broad:**
```yaml
pattern: log    # Matches "log", "login", "dialog", "catalog"
```
Better: `console\.log\(|logger\.`

**Too specific:**
```yaml
pattern: rm -rf /tmp  # Only matches exact path
```
Better: `rm\s+-rf`

**Escaping issues:**
- YAML quoted strings: `"pattern"` requires double backslashes `\\s`
- YAML unquoted: `pattern: \s` works as-is
- **Recommendation**: Use unquoted patterns in YAML

## File Organization

**Location:** All rules in `.claude/` directory
**Naming:** `.claude/hookify.{descriptive-name}.local.md`
**Gitignore:** Add `.claude/*.local.md` to `.gitignore`

**Good names:**
- `hookify.dangerous-rm.local.md`
- `hookify.console-log.local.md`
- `hookify.require-tests.local.md`
- `hookify.sensitive-files.local.md`

**Bad names:**
- `hookify.rule1.local.md` (not descriptive)
- `hookify.md` (missing .local)
- `danger.local.md` (missing hookify prefix)

## Workflow

### Creating a Rule

1. Identify unwanted behavior
2. Determine which tool is involved (Bash, Edit, etc.)
3. Choose event type (bash, file, stop, etc.)
4. Write regex pattern
5. Create `.claude/hookify.{name}.local.md` file in project root
6. Test immediately - rules are read dynamically on next tool use

### Refining a Rule

1. Edit the `.local.md` file
2. Adjust pattern or message
3. Test immediately - changes take effect on next tool use

### Disabling a Rule

**Temporary:** Set `enabled: false` in frontmatter
**Permanent:** Delete the `.local.md` file

## Examples

See `${CLAUDE_PLUGIN_ROOT}/examples/` for complete examples:
- `dangerous-rm.local.md` - Block dangerous rm commands
- `console-log-warning.local.md` - Warn about console.log
- `sensitive-files-warning.local.md` - Warn about editing .env files

## Quick Reference

**Minimum viable rule:**
```markdown
---
name: my-rule
enabled: true
event: bash
pattern: dangerous_command
---

Warning message here
```

**Rule with conditions:**
```markdown
---
name: my-rule
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.ts$
  - field: new_text
    operator: contains
    pattern: any
---

Warning message
```

**Event types:**
- `bash` - Bash commands
- `file` - File edits
- `stop` - Completion checks
- `prompt` - User input
- `all` - All events

**Field options:**
- Bash: `command`
- File: `file_path`, `new_text`, `old_text`, `content`
- Prompt: `user_prompt`

**Operators:**
- `regex_match`, `contains`, `equals`, `not_contains`, `starts_with`, `ends_with`


</details>
