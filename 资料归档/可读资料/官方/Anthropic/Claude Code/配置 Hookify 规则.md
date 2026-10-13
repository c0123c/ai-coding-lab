# 配置 Hookify 规则

提供交互式启用或停用项目规则的命令样例，依次读取现有规则、收集用户选择、修改配置并反馈结果；属于插件实现资料。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/anthropics/claude-code/main/plugins/hookify/commands/configure.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/配置 Hookify 规则.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: Enable or disable hookify rules interactively
allowed-tools: ["Glob", "Read", "Edit", "AskUserQuestion", "Skill"]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 配置 Hookify 规则

**首先加载hookify：编写规则技巧**以了解规则格式。

使用交互式界面启用或禁用现有的 hookify 规则。

## 步骤

### 1。查找现有规则

使用Glob工具查找所有hookify规则文件：
```
pattern: ".claude/hookify.*.local.md"
```

如果没有找到规则，通知用户：
```
No hookify rules configured yet. Use `/hookify` to create your first rule.
```

### 2。读取当前状态

对于每个规则文件：
- 读取文件
- 从 frontmatter 中提取 `name` 和 `enabled` 字段
- 构建当前状态的规则列表

### 3。询问用户要切换哪些规则

使用AskUserQuestion让用户选择规则：

```json
{
  "questions": [
    {
      "question": "Which rules would you like to enable or disable?",
      "header": "Configure",
      "multiSelect": true,
      "options": [
        {
          "label": "warn-dangerous-rm (currently enabled)",
          "description": "Warns about rm -rf commands"
        },
        {
          "label": "warn-console-log (currently disabled)",
          "description": "Warns about console.log in code"
        },
        {
          "label": "require-tests (currently enabled)",
          "description": "Requires tests before stopping"
        }
      ]
    }
  ]
}
```

**选项格式：**
- 标签：`{rule-name} (currently {enabled|disabled})`
- 描述：规则消息或模式的简要描述

### 4。解析用户选择

对于每个选定的规则：
- 从标签确定当前状态（enabled/disabled）
- 切换状态：启用→禁用、禁用→启用

### 5。更新规则文件

对于每个要切换的规则：
- 使用阅读工具阅读当前内容
- 使用编辑工具将`enabled: true`更改为`enabled: false`（反之亦然）
- 处理带引号和不带引号的情况

**编辑启用模式：**
```
old_string: "enabled: false"
new_string: "enabled: true"
```

**编辑禁用模式：**
```
old_string: "enabled: true"
new_string: "enabled: false"
```

### 6。确认更改

向用户显示更改的内容：

```
## Hookify Rules Updated

**Enabled:**
- warn-console-log

**Disabled:**
- warn-dangerous-rm

**Unchanged:**
- require-tests

Changes apply immediately - no restart needed
```

## 重要提示

- 更改在下次使用工具时立即生效
- 您还可以手动编辑.claude/hookify.*.local.md文件
- 要永久删除规则，请删除其 .local.md 文件
- 使用`/hookify:list`查看所有配置的规则

## 边缘情况

**没有要配置的规则：**
- 首先显示有关使用 `/hookify` 创建规则的消息

**用户未选择任何规则：**
- 告知没有进行任何更改

**文件read/write错误：**
- 通知用户具体错误
- 建议手动编辑作为后备


## 原文对照

<details>
<summary>展开完整原文</summary>

# Configure Hookify Rules

**Load hookify:writing-rules skill first** to understand rule format.

Enable or disable existing hookify rules using an interactive interface.

## Steps

### 1. Find Existing Rules

Use Glob tool to find all hookify rule files:
```
pattern: ".claude/hookify.*.local.md"
```

If no rules found, inform user:
```
No hookify rules configured yet. Use `/hookify` to create your first rule.
```

### 2. Read Current State

For each rule file:
- Read the file
- Extract `name` and `enabled` fields from frontmatter
- Build list of rules with current state

### 3. Ask User Which Rules to Toggle

Use AskUserQuestion to let user select rules:

```json
{
  "questions": [
    {
      "question": "Which rules would you like to enable or disable?",
      "header": "Configure",
      "multiSelect": true,
      "options": [
        {
          "label": "warn-dangerous-rm (currently enabled)",
          "description": "Warns about rm -rf commands"
        },
        {
          "label": "warn-console-log (currently disabled)",
          "description": "Warns about console.log in code"
        },
        {
          "label": "require-tests (currently enabled)",
          "description": "Requires tests before stopping"
        }
      ]
    }
  ]
}
```

**Option format:**
- Label: `{rule-name} (currently {enabled|disabled})`
- Description: Brief description from rule's message or pattern

### 4. Parse User Selection

For each selected rule:
- Determine current state from label (enabled/disabled)
- Toggle state: enabled → disabled, disabled → enabled

### 5. Update Rule Files

For each rule to toggle:
- Use Read tool to read current content
- Use Edit tool to change `enabled: true` to `enabled: false` (or vice versa)
- Handle both with and without quotes

**Edit pattern for enabling:**
```
old_string: "enabled: false"
new_string: "enabled: true"
```

**Edit pattern for disabling:**
```
old_string: "enabled: true"
new_string: "enabled: false"
```

### 6. Confirm Changes

Show user what was changed:

```
## Hookify Rules Updated

**Enabled:**
- warn-console-log

**Disabled:**
- warn-dangerous-rm

**Unchanged:**
- require-tests

Changes apply immediately - no restart needed
```

## Important Notes

- Changes take effect immediately on next tool use
- You can also manually edit .claude/hookify.*.local.md files
- To permanently remove a rule, delete its .local.md file
- Use `/hookify:list` to see all configured rules

## Edge Cases

**No rules to configure:**
- Show message about using `/hookify` to create rules first

**User selects no rules:**
- Inform that no changes were made

**File read/write errors:**
- Inform user of specific error
- Suggest manual editing as fallback


</details>
