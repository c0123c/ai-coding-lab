# Hookify 使用帮助

汇总规则文件、Hook 机制和可用命令，说明创建与管理规则的操作路径。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 存档版本：`2301018b1f61`。
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-code/blob/main/plugins/hookify/commands/help.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/Hookify 使用帮助.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Get help with the hookify plugin
allowed-tools: ["Read"]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Hookify 插件帮助

解释 hookify 插件如何工作以及如何使用它。

## 概述

hookify 插件可以轻松创建自定义挂钩来防止不良行为。用户无需编辑 `hooks.json` 文件，而是创建简单的 Markdown 配置文件来定义要监视的模式。

## 它是如何运作的

### 1。挂钩系统

Hookify 安装在这些事件上运行的通用挂钩：
- **PreToolUse**：在任何工具执行之前（Bash、Edit、Write 等）
- **PostToolUse**：工具执行后
- **停止**：当Claude想要停止工作时
- **UserPromptSubmit**：当用户提交提示时

这些钩子从 `.claude/hookify.*.local.md` 读取配置文件并检查是否有任何规则与当前操作匹配。

### 2。配置文件

用户在`.claude/hookify.{rule-name}.local.md`文件中创建规则：

```markdown
---
name: warn-dangerous-rm
enabled: true
event: bash
pattern: rm\s+-rf
---

⚠️ **Dangerous rm command detected!**

This command could delete important files. Please verify the path.
```

**关键字段：**
- `name`：规则的唯一标识符
- `enabled`: true/false 至 activate/deactivate
- `event`：bash、文件、停止、提示或全部
- `pattern`：要匹配的正则表达式模式

消息正文是规则触发时Claude看到的内容。

### 3。创建规则

**选项A：使用/hookify命令**
```
/hookify Don't use console.log in production files
```

这会分析您的请求并创建适当的规则文件。

**选项B：手动创建**
使用上述格式创建`.claude/hookify.my-rule.local.md`。

**选项 C：分析对话**
```
/hookify
```

在没有参数的情况下，hookify 会分析最近的对话以找到您想要阻止的行为。

## 可用命令

- **`/hookify`** - 根据对话分析或显式指令创建挂钩
- **`/hookify:help`** - 显示此帮助（您现在正在阅读的内容）
- **`/hookify:list`** - 列出所有配置的钩子
- **`/hookify:configure`** - Enable/disable 交互式现有钩子

## 示例用例

**防止危险命令：**
```markdown
---
name: block-chmod-777
enabled: true
event: bash
pattern: chmod\s+777
---

Don't use chmod 777 - it's a security risk. Use specific permissions instead.
```

**有关调试代码的警告：**
```markdown
---
name: warn-console-log
enabled: true
event: file
pattern: console\.log\(
---

Console.log detected. Remember to remove debug logging before committing.
```

**停止前需要测试：**
```markdown
---
name: require-tests
enabled: true
event: stop
pattern: .*
---

Did you run tests before finishing? Make sure `npm test` or equivalent was executed.
```

## 模式语法

使用 Python 正则表达式语法：
- `\s` - 空白
- `\.` - 文字点
- `|` - OR
- `+` - 一个或多个
- `*` - 零个或多个
- `\d` - 数字
- `[abc]` - 角色类别

**示例：**
- `rm\s+-rf` - 匹配“rm -rf”
- `console\.log\(` - 匹配“console.log(”
- `(eval|exec)\(` - 匹配“eval(”或“exec(”)
- `\.env$` - 匹配以 .env 结尾的文件

## 重要提示

**无需重新启动**：Hookify 规则（`.local.md` 文件）在下次使用工具时立即生效。 hookify 挂钩已加载并动态读取您的规则。

**阻止或警告**：规则可以是`block`操作（阻止执行）或`warn`（显示消息但允许）。在规则的 frontmatter 中设置 `action: block` 或 `action: warn`。

**规则文件**：将规则保留在`.claude/hookify.*.local.md`中 - 它们应该被git忽略（如果需要，添加到.gitignore）。

**禁用规则**：在frontmatter中设置`enabled: false`或删除该文件。

## 故障排除

**挂钩未触发：**
- 检查规则文件是否在`.claude/`目录中
- 验证 frontmatter 中的 `enabled: true`
- 确认模式是有效的正则表达式
- 测试图案：`python3 -c "import re; print(re.search('your_pattern', 'test_text'))"`
- 规则立即生效 - 无需重启

**导入错误：**
- 检查 Python 3 是否可用：`python3 --version`
- 验证 hookify 插件是否正确安装

**模式不匹配：**
- 单独测试正则表达式
- 检查转义问题（在 YAML 中使用不带引号的模式）
- 先尝试更简单的模式，然后再完善

## 开始使用

1. 创建您的第一条规则：
   ```
   /hookify Warn me when I try to use rm -rf
   ```

2. 尝试触发它：
   - 要求Claude运行`rm -rf /tmp/test`
   - 您应该看到警告

4. 通过编辑 `.claude/hookify.warn-rm.local.md` 细化规则

5. 当您遇到不良行为时创建更多规则

有关更多示例，请检查 `${CLAUDE_PLUGIN_ROOT}/examples/` 目录。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Hookify Plugin Help

Explain how the hookify plugin works and how to use it.

## Overview

The hookify plugin makes it easy to create custom hooks that prevent unwanted behaviors. Instead of editing `hooks.json` files, users create simple markdown configuration files that define patterns to watch for.

## How It Works

### 1. Hook System

Hookify installs generic hooks that run on these events:
- **PreToolUse**: Before any tool executes (Bash, Edit, Write, etc.)
- **PostToolUse**: After a tool executes
- **Stop**: When Claude wants to stop working
- **UserPromptSubmit**: When user submits a prompt

These hooks read configuration files from `.claude/hookify.*.local.md` and check if any rules match the current operation.

### 2. Configuration Files

Users create rules in `.claude/hookify.{rule-name}.local.md` files:

```markdown
---
name: warn-dangerous-rm
enabled: true
event: bash
pattern: rm\s+-rf
---

⚠️ **Dangerous rm command detected!**

This command could delete important files. Please verify the path.
```

**Key fields:**
- `name`: Unique identifier for the rule
- `enabled`: true/false to activate/deactivate
- `event`: bash, file, stop, prompt, or all
- `pattern`: Regex pattern to match

The message body is what Claude sees when the rule triggers.

### 3. Creating Rules

**Option A: Use /hookify command**
```
/hookify Don't use console.log in production files
```

This analyzes your request and creates the appropriate rule file.

**Option B: Create manually**
Create `.claude/hookify.my-rule.local.md` with the format above.

**Option C: Analyze conversation**
```
/hookify
```

Without arguments, hookify analyzes recent conversation to find behaviors you want to prevent.

## Available Commands

- **`/hookify`** - Create hooks from conversation analysis or explicit instructions
- **`/hookify:help`** - Show this help (what you're reading now)
- **`/hookify:list`** - List all configured hooks
- **`/hookify:configure`** - Enable/disable existing hooks interactively

## Example Use Cases

**Prevent dangerous commands:**
```markdown
---
name: block-chmod-777
enabled: true
event: bash
pattern: chmod\s+777
---

Don't use chmod 777 - it's a security risk. Use specific permissions instead.
```

**Warn about debugging code:**
```markdown
---
name: warn-console-log
enabled: true
event: file
pattern: console\.log\(
---

Console.log detected. Remember to remove debug logging before committing.
```

**Require tests before stopping:**
```markdown
---
name: require-tests
enabled: true
event: stop
pattern: .*
---

Did you run tests before finishing? Make sure `npm test` or equivalent was executed.
```

## Pattern Syntax

Use Python regex syntax:
- `\s` - whitespace
- `\.` - literal dot
- `|` - OR
- `+` - one or more
- `*` - zero or more
- `\d` - digit
- `[abc]` - character class

**Examples:**
- `rm\s+-rf` - matches "rm -rf"
- `console\.log\(` - matches "console.log("
- `(eval|exec)\(` - matches "eval(" or "exec("
- `\.env$` - matches files ending in .env

## Important Notes

**No Restart Needed**: Hookify rules (`.local.md` files) take effect immediately on the next tool use. The hookify hooks are already loaded and read your rules dynamically.

**Block or Warn**: Rules can either `block` operations (prevent execution) or `warn` (show message but allow). Set `action: block` or `action: warn` in the rule's frontmatter.

**Rule Files**: Keep rules in `.claude/hookify.*.local.md` - they should be git-ignored (add to .gitignore if needed).

**Disable Rules**: Set `enabled: false` in frontmatter or delete the file.

## Troubleshooting

**Hook not triggering:**
- Check rule file is in `.claude/` directory
- Verify `enabled: true` in frontmatter
- Confirm pattern is valid regex
- Test pattern: `python3 -c "import re; print(re.search('your_pattern', 'test_text'))"`
- Rules take effect immediately - no restart needed

**Import errors:**
- Check Python 3 is available: `python3 --version`
- Verify hookify plugin is installed correctly

**Pattern not matching:**
- Test regex separately
- Check for escaping issues (use unquoted patterns in YAML)
- Try simpler pattern first, then refine

## Getting Started

1. Create your first rule:
   ```
   /hookify Warn me when I try to use rm -rf
   ```

2. Try to trigger it:
   - Ask Claude to run `rm -rf /tmp/test`
   - You should see the warning

4. Refine the rule by editing `.claude/hookify.warn-rm.local.md`

5. Create more rules as you encounter unwanted behaviors

For more examples, check the `${CLAUDE_PLUGIN_ROOT}/examples/` directory.


</details>
