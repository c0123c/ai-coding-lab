---
vendor: "Anthropic"
title: "List Hookify Rules"
official_url: https://raw.githubusercontent.com/anthropics/claude-code/main/plugins/hookify/commands/list.md
resolved_url: https://raw.githubusercontent.com/anthropics/claude-code/2301018b1f61073c501a8e7a4813ef48c239163b/plugins/hookify/commands/list.md
fetched_at: 2026-10-10T15:19:43+00:00
status: fetched
scope: core_ai_coding
source_kind: official_github_raw
repository_revision: 2301018b1f61073c501a8e7a4813ef48c239163b
sha256_raw: 6128e44611d981e911c0e956d619b02924f46407a00bbb9deff2e4f3465b9368
sha256_body: 6128e44611d981e911c0e956d619b02924f46407a00bbb9deff2e4f3465b9368
---

# 官方正文

---
description: List all configured hookify rules
allowed-tools: ["Glob", "Read", "Skill"]
---

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
