# OpenCode Skills 发现、格式与权限

产品指南。说明 SKILL.md 放置与发现、名称和长度、描述、工具识别、按 Agent 授权、关闭和排查，帮助组织可复用开发步骤。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-04](<https://github.com/anomalyco/opencode/commit/5aaf8f82475c84640ab5f03caf7fffda4b0ffc9f>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：格式和发现路径以所存版本为准，技能内容需要项目化。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/skills.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode Skills 发现、格式与权限.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Agent Skills"
description: "Define reusable behavior via SKILL.md definitions"
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Agent skills 让 OpenCode 从您的存储库或主目录中发现可重用的指令。
技能通过本机 `skill` 工具按需加载 - agents 查看可用技能，并可以在需要时加载完整内容。

---

## 放置文件

每个技能名称创建一个文件夹，并将 `SKILL.md` 放入其中。
OpenCode 搜索以下位置：

- 项目配置：`.opencode/skills/<name>/SKILL.md`
- 全局配置：`~/.config/opencode/skills/<name>/SKILL.md`
- 项目 Claude 兼容：`.claude/skills/<name>/SKILL.md`
- 全球 Claude 兼容：`~/.claude/skills/<name>/SKILL.md`
- 项目 agent 兼容：`.agents/skills/<name>/SKILL.md`
- 全球 agent 兼容：`~/.agents/skills/<name>/SKILL.md`

---

## 了解发现

对于项目本地路径，OpenCode 从当前工作目录开始，直到到达 git 工作树。
它会加载 `.opencode/` 中任何匹配的 `skills/*/SKILL.md` 以及沿途任何匹配的 `.claude/skills/*/SKILL.md` 或 `.agents/skills/*/SKILL.md`。

全局定义也从 `~/.config/opencode/skills/*/SKILL.md`、`~/.claude/skills/*/SKILL.md` 和 `~/.agents/skills/*/SKILL.md` 加载。

---

## 写前言

每个 `SKILL.md` 必须以 YAML frontmatter 开头。
仅识别这些字段：

- `name`（必填）
- `description`（必填）
- `license`（可选）
- `compatibility`（可选）
- `metadata`（可选，字符串到字符串映射）

未知的 frontmatter 字段将被忽略。

---

## 验证姓名

`name` 必须：

- 为1–64个字符
- 为小写字母数字并带有单个连字符分隔符
- 不以 `-` 开头或结尾
- 不包含连续的`--`
- 匹配包含`SKILL.md`的目录名

等效的正则表达式：

```text
^[a-z0-9]+(-[a-z0-9]+)*$
```

---

## 遵循长度规则

`description` 必须是 1-1024 字符。
保持足够具体，以便 agent 正确选择。

---

## 使用一个例子

像这样创建 `.opencode/skills/git-release/SKILL.md`：

```markdown
---
name: git-release
description: Create consistent releases and changelogs
license: MIT
compatibility: opencode
metadata:
  audience: maintainers
  workflow: github
---

## What I do

- Draft release notes from merged PRs
- Propose a version bump
- Provide a copy-pasteable `gh release create` command

## When to use me

Use this when you are preparing a tagged release.
Ask clarifying questions if the target versioning scheme is unclear.
```

---

## 识别工具说明

OpenCode 列出了 `skill` 工具描述中的可用技能。
每个条目都包含技能名称和描述：

```xml
<available_skills>
  <skill>
    <name>git-release</name>
    <description>Create consistent releases and changelogs</description>
  </skill>
</available_skills>
```

agent通过调用工具加载技能：

```
skill({ name: "git-release" })
```

---

## 配置权限

使用 `opencode.json` 中基于模式的权限控制哪些技能 agents 可以访问：

```json
{
  "permission": {
    "skill": {
      "*": "allow",
      "pr-review": "allow",
      "internal-*": "deny",
      "experimental-*": "ask"
    }
  }
}
```

|许可|行为|
| ---------- | ----------------------------------------- |
| `allow`    |技能立即加载|
| `deny`     |agent 隐藏技能，访问被拒绝|
| `ask`      |加载前提示用户批准|

模式支持通配符：`internal-*` 匹配 `internal-docs`、`internal-tools` 等。

---

## 根据 agent 覆盖

授予特定的 agents 与全局默认值不同的权限。

**对于自定义agents**（在agent frontmatter中）：

```yaml
---
permission:
  skill:
    "documents-*": "allow"
---
```

**对于内置agents**（在`opencode.json`中）：

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "internal-*": "allow"
        }
      }
    }
  }
}
```

---

## 禁用技能工具

完全禁用不应该使用的agents技能：

**对于定制agents**：

```yaml
---
tools:
  skill: false
---
```

**对于内置agents**：

```json
{
  "agent": {
    "plan": {
      "tools": {
        "skill": false
      }
    }
  }
}
```

禁用时，`<available_skills>` 部分将被完全省略。

---

## 解决加载问题

如果某项技能没有显示：

1. 验证 `SKILL.md` 是否全部大写
2. 检查 frontmatter 是否包含 `name` 和 `description`
3. 确保技能名称在所有位置都是唯一的
4. 检查权限 - `deny` 的技能对 agents 隐藏


## 原文对照

<details>
<summary>展开完整原文</summary>

Agent skills let OpenCode discover reusable instructions from your repo or home directory.
Skills are loaded on-demand via the native `skill` tool—agents see available skills and can load the full content when needed.

---

## Place files

Create one folder per skill name and put a `SKILL.md` inside it.
OpenCode searches these locations:

- Project config: `.opencode/skills/<name>/SKILL.md`
- Global config: `~/.config/opencode/skills/<name>/SKILL.md`
- Project Claude-compatible: `.claude/skills/<name>/SKILL.md`
- Global Claude-compatible: `~/.claude/skills/<name>/SKILL.md`
- Project agent-compatible: `.agents/skills/<name>/SKILL.md`
- Global agent-compatible: `~/.agents/skills/<name>/SKILL.md`

---

## Understand discovery

For project-local paths, OpenCode walks up from your current working directory until it reaches the git worktree.
It loads any matching `skills/*/SKILL.md` in `.opencode/` and any matching `.claude/skills/*/SKILL.md` or `.agents/skills/*/SKILL.md` along the way.

Global definitions are also loaded from `~/.config/opencode/skills/*/SKILL.md`, `~/.claude/skills/*/SKILL.md`, and `~/.agents/skills/*/SKILL.md`.

---

## Write frontmatter

Each `SKILL.md` must start with YAML frontmatter.
Only these fields are recognized:

- `name` (required)
- `description` (required)
- `license` (optional)
- `compatibility` (optional)
- `metadata` (optional, string-to-string map)

Unknown frontmatter fields are ignored.

---

## Validate names

`name` must:

- Be 1–64 characters
- Be lowercase alphanumeric with single hyphen separators
- Not start or end with `-`
- Not contain consecutive `--`
- Match the directory name that contains `SKILL.md`

Equivalent regex:

```text
^[a-z0-9]+(-[a-z0-9]+)*$
```

---

## Follow length rules

`description` must be 1-1024 characters.
Keep it specific enough for the agent to choose correctly.

---

## Use an example

Create `.opencode/skills/git-release/SKILL.md` like this:

```markdown
---
name: git-release
description: Create consistent releases and changelogs
license: MIT
compatibility: opencode
metadata:
  audience: maintainers
  workflow: github
---

## What I do

- Draft release notes from merged PRs
- Propose a version bump
- Provide a copy-pasteable `gh release create` command

## When to use me

Use this when you are preparing a tagged release.
Ask clarifying questions if the target versioning scheme is unclear.
```

---

## Recognize tool description

OpenCode lists available skills in the `skill` tool description.
Each entry includes the skill name and description:

```xml
<available_skills>
  <skill>
    <name>git-release</name>
    <description>Create consistent releases and changelogs</description>
  </skill>
</available_skills>
```

The agent loads a skill by calling the tool:

```
skill({ name: "git-release" })
```

---

## Configure permissions

Control which skills agents can access using pattern-based permissions in `opencode.json`:

```json
{
  "permission": {
    "skill": {
      "*": "allow",
      "pr-review": "allow",
      "internal-*": "deny",
      "experimental-*": "ask"
    }
  }
}
```

| Permission | Behavior                                  |
| ---------- | ----------------------------------------- |
| `allow`    | Skill loads immediately                   |
| `deny`     | Skill hidden from agent, access rejected  |
| `ask`      | User prompted for approval before loading |

Patterns support wildcards: `internal-*` matches `internal-docs`, `internal-tools`, etc.

---

## Override per agent

Give specific agents different permissions than the global defaults.

**For custom agents** (in agent frontmatter):

```yaml
---
permission:
  skill:
    "documents-*": "allow"
---
```

**For built-in agents** (in `opencode.json`):

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "internal-*": "allow"
        }
      }
    }
  }
}
```

---

## Disable the skill tool

Completely disable skills for agents that shouldn't use them:

**For custom agents**:

```yaml
---
tools:
  skill: false
---
```

**For built-in agents**:

```json
{
  "agent": {
    "plan": {
      "tools": {
        "skill": false
      }
    }
  }
}
```

When disabled, the `<available_skills>` section is omitted entirely.

---

## Troubleshoot loading

If a skill does not show up:

1. Verify `SKILL.md` is spelled in all caps
2. Check that frontmatter includes `name` and `description`
3. Ensure skill names are unique across all locations
4. Check permissions—skills with `deny` are hidden from agents


</details>
