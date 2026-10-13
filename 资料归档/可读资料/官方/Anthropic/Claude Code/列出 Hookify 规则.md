# 列出 Hookify 规则

提供检查项目中已配置规则的命令流程，读取规则状态并展示结果，同时处理没有规则的情况；属于插件命令样例。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：源码提交（存档版）：[2025-11-17](<https://github.com/anthropics/claude-code/commit/59372c0921b0170e81f9c63777962d02347411d5>)；发表/更新：官方未公开；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/anthropics/claude-code/main/plugins/hookify/commands/list.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/列出 Hookify 规则.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
description: List all configured hookify rules
allowed-tools: ["Glob", "Read", "Skill"]
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 列出 Hookify 规则

**首先加载hookify：编写规则技巧**以了解规则格式。

显示项目中所有配置的 hookify 规则。

## 步骤

1. 使用Glob工具查找所有hookify规则文件：
   ```
   pattern: ".claude/hookify.*.local.md"
   ```

2. 对于找到的每个文件：
   - 使用Read工具读取文件
   - 提取 frontmatter 字段：名称、启用、事件、模式
   - 提取消息预览（第一个 100 字符）

3. 将结果呈现在表格中：

```
## Configured Hookify Rules

| Name | Enabled | Event | Pattern | File |
|------|---------|-------|---------|------|
| warn-dangerous-rm | ✅ Yes | bash | rm\s+-rf | hookify.dangerous-rm.local.md |
| warn-console-log | ✅ Yes | file | console\.log\( | hookify.console-log.local.md |
| check-tests | ❌ No | stop | .* | hookify.require-tests.local.md |

**Total**: 3 rules (2 enabled, 1 disabled)
```

4. 对于每条规则，显示简短的预览：
```
### warn-dangerous-rm
**Event**: bash
**Pattern**: `rm\s+-rf`
**Message**: "⚠️ **Dangerous rm command detected!** This command could delete..."

**Status**: ✅ Active
**File**: .claude/hookify.dangerous-rm.local.md
```

5. 添加有用的页脚：
```
---

To modify a rule: Edit the .local.md file directly
To disable a rule: Set `enabled: false` in frontmatter
To enable a rule: Set `enabled: true` in frontmatter
To delete a rule: Remove the .local.md file
To create a rule: Use `/hookify` command

**Remember**: Changes take effect immediately - no restart needed
```

## 如果没有找到规则

如果不存在 hookify 规则：

```
## No Hookify Rules Configured

You haven't created any hookify rules yet.

To get started:
1. Use `/hookify` to analyze conversation and create rules
2. Or manually create `.claude/hookify.my-rule.local.md` files
3. See `/hookify:help` for documentation

Example:
```
/hookify 当我使用 console.log 时警告我
```

Check `${CLAUDE_PLUGIN_ROOT}/examples/` for example rule files.
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# List Hookify Rules

**Load hookify:writing-rules skill first** to understand rule format.

Show all configured hookify rules in the project.

## Steps

1. Use Glob tool to find all hookify rule files:
   ```
   pattern: ".claude/hookify.*.local.md"
   ```

2. For each file found:
   - Use Read tool to read the file
   - Extract frontmatter fields: name, enabled, event, pattern
   - Extract message preview (first 100 chars)

3. Present results in a table:

```
## Configured Hookify Rules

| Name | Enabled | Event | Pattern | File |
|------|---------|-------|---------|------|
| warn-dangerous-rm | ✅ Yes | bash | rm\s+-rf | hookify.dangerous-rm.local.md |
| warn-console-log | ✅ Yes | file | console\.log\( | hookify.console-log.local.md |
| check-tests | ❌ No | stop | .* | hookify.require-tests.local.md |

**Total**: 3 rules (2 enabled, 1 disabled)
```

4. For each rule, show a brief preview:
```
### warn-dangerous-rm
**Event**: bash
**Pattern**: `rm\s+-rf`
**Message**: "⚠️ **Dangerous rm command detected!** This command could delete..."

**Status**: ✅ Active
**File**: .claude/hookify.dangerous-rm.local.md
```

5. Add helpful footer:
```
---

To modify a rule: Edit the .local.md file directly
To disable a rule: Set `enabled: false` in frontmatter
To enable a rule: Set `enabled: true` in frontmatter
To delete a rule: Remove the .local.md file
To create a rule: Use `/hookify` command

**Remember**: Changes take effect immediately - no restart needed
```

## If No Rules Found

If no hookify rules exist:

```
## No Hookify Rules Configured

You haven't created any hookify rules yet.

To get started:
1. Use `/hookify` to analyze conversation and create rules
2. Or manually create `.claude/hookify.my-rule.local.md` files
3. See `/hookify:help` for documentation

Example:
```
/hookify Warn me when I use console.log
```

Check `${CLAUDE_PLUGIN_ROOT}/examples/` for example rule files.
```


</details>
