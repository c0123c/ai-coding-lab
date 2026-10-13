# Mistral Vibe 技能创建说明

讲解技能存放与发现顺序、SKILL.md 字段、支持文件及增改删流程；适用于 Vibe 的技能实现。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-09-09](<https://github.com/mistralai/mistral-vibe/commit/2817f3df81ae05d49ba9538262edb1d5a18fa006>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/vibe/plugins/builtins/vibe/skills/skill-creator/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/Mistral Vibe 技能创建说明.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: "skill-creator"
description: "Load this skill before creating, updating, or deleting a Vibe skill, or whenever you plan to add or modify SKILL.md files under a skills directory. It explains Vibe's SKILL.md frontmatter, discovery order, support files, and the permission flow."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 技能创造者

当用户要求您创建、更新或删除 Vibe 技能时使用此选项。

## 收集需求

创建新技能时，请向用户询问您缺少的详细信息。 **一次询问
一个问题，然后在询问下一个问题之前等待答案。**永远不要
在一条消息中发送多个问题 - 这会令人困惑。按
顺序集合：

1. 名称 — 一个 slug（小写字母，连字符），例如`run-migrations` 或
   `format-changelog`.
2. 目的/描述——什么时候应该加载这项技能？这是路由文本
模型看到了。
3. 说明——技能实际上应该告诉模型做什么？步骤，
约定、命令、陷阱。

跳过用户已经回答的任何问题。一旦你掌握了所有三个，
确认范围（项目与全局）如果不明显，然后写下
技能。

## 什么是技能

技能是一个包含 `SKILL.md` 文件的目录，其中包含 YAML frontmatter 以及 
Markdown 指令。模型通过`skill`工具按需加载；还可以通过输入 `/skill-name` 来访问 
 用户可调用技能。 `description`
 对于路由始终可见 — 仅在选择技能 
 后才加载主体。

## 技能所在（发现顺序，首场比赛获胜）

1. `skill_paths` 来自 `config.toml` 的条目
2. `.vibe/skills/` — 项目范围（需要受信任的文件夹）
3. `.agents/skills/` — 项目范围（需要受信任的文件夹）
4. `~/.vibe/skills/` — 用户全局
5. `~/.agents/skills/` — 用户全局

优先级：内置技能首先播种，其名称为 ** 保留 ** —
 名称与内置技能冲突的用户技能会在加载 
 时默默地跳过，并且永远不会出现。在上述路径中，第一个定义
给定名称的目录获胜；稍后的重复项将被跳过。内置技能是只读的。
注册表技能在`VIBE_HOME`下具体化，不应
手动编辑。

在写入之前选择范围：使用 `.vibe/skills/<name>/` 表示范围为 
 到此项目的技能，或使用 `~/.vibe/skills/<name>/` 表示任何地方都可用的技能。当不明显时询问
他们想要的用户，并写入
与所选范围匹配的目录。

## SKILL.md 格式

```markdown
---
name: my-skill
description: Load this skill when ...
---

# My Skill

Instructions go here.
```

Frontmatter 字段：

- `name`（必填）：仅限小写字母、数字和连字符
(`^[a-z0-9]+(-[a-z0-9]+)*$`)、1–64 字符。它应该与目录名称 
 匹配（不匹配只会记录警告；该技能仍然在 frontmatter
 名称下加载）。不要重复使用内置技能名称（例如`vibe`）或其他技能已经使用的名称
 \- 碰撞会被静默跳过，并且技能将
不会加载。
- `description`（必需）：1–1024 个字符。说 *when* 来加载技能 - 这是
模型在选择之前看到的唯一文本。
- `user-invocable`（可选，默认`true`）：当技能为`false`时
仅限模型 - 从 `/` 菜单隐藏，无法通过 `/skill-name` 访问。
- `allowed-tools`（可选，实验性）：以空格分隔的预先批准的列表
工具。
- `license`、`compatibility`、`metadata`（可选）：仅限元数据。 `metadata`
是一个平面字符串到字符串的映射（例如 `display-name`、`short-description`）。

不要发明 frontmatter 键。其他产品中的字段（例如 `visibility`、
`defaultEnabled`）不是 Vibe 架构的一部分，因此会被忽略。

## 支持文件

让 `SKILL.md` 专注于持久的指令。仅当 Markdown、纯文本、CSV、
JSON 或 YAML 支持文件获得其 
 位置时，才添加到同一目录中。通过相对于技能目录的路径引用它们；模型根据需要读取
和`read_file`。

## 创建、更新、删除

- 创建：使用有效的 frontmatter 创建 `<skills-dir>/<slug>/SKILL.md`，其中
`<skills-dir>` 是所选范围的目录（`.vibe/skills/` 表示 
 项目，`~/.vibe/skills/` 表示全局）。仅在有用时才在同一
 目录下添加支持文件。
- 更新：编辑 `SKILL.md` 或支持文件 `edit`。保留有用的
现有指导；保持差异最小。
- 删除：当要求删除技能时，删除整个技能目录。不
仅删除`SKILL.md`。

## 应用更改

在技能目录下进行写作会经历正常的 `write_file` / `edit`
 权限提示 - 没有单独的建议步骤。更改后，告诉
用户他们可以运行`/reload`来获取新的或编辑的技能，而无需
重新启动。最后的总结要简短；除非
用户要求，否则不要粘贴完整的文件内容。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Skill Creator

Use this when the user asks you to create, update, or delete a Vibe skill.

## Gathering requirements

When creating a new skill, ask the user for the details you are missing. **Ask
one question at a time and wait for the answer before asking the next.** Never
send multiple questions in a single message — it is bewildering. Gather, in
order:

1. Name — a slug (lowercase, hyphens), e.g. `run-migrations` or
   `format-changelog`.
2. Purpose / description — when should this skill load? This is the routing text
   the model sees.
3. Instructions — what should the skill actually tell the model to do? Steps,
   conventions, commands, gotchas.

Skip any question the user has already answered. Once you have all three,
confirm the scope (project vs global) if it is not obvious, then write the
skill.

## What a skill is

A skill is a directory containing a `SKILL.md` file with YAML frontmatter plus
Markdown instructions. The model loads it on demand via the `skill` tool; a
user-invocable skill is also reachable by typing `/skill-name`. The `description`
is always visible for routing — the body is loaded only after the skill is
selected.

## Where skills live (discovery order, first match wins)

1. `skill_paths` entries from `config.toml`
2. `.vibe/skills/` — project scope (requires a trusted folder)
3. `.agents/skills/` — project scope (requires a trusted folder)
4. `~/.vibe/skills/` — user global
5. `~/.agents/skills/` — user global

Precedence: built-in skills are seeded first and their names are **reserved** —
a user skill whose name collides with a built-in is silently skipped at load
time and never appears. Among the paths above, the first directory to define a
given name wins; later duplicates are skipped. Built-in skills are read-only.
Registry skills are materialized under `VIBE_HOME` and should not be
hand-edited.

Choose the scope before writing: use `.vibe/skills/<name>/` for a skill scoped
to this project, or `~/.vibe/skills/<name>/` for one available everywhere. Ask
the user which they want when it is not obvious, and write to the directory that
matches the chosen scope.

## SKILL.md format

```markdown
---
name: my-skill
description: Load this skill when ...
---

# My Skill

Instructions go here.
```

Frontmatter fields:

- `name` (required): lowercase letters, numbers, and hyphens only
  (`^[a-z0-9]+(-[a-z0-9]+)*$`), 1–64 chars. It should match the directory name
  (a mismatch only logs a warning; the skill still loads under the frontmatter
  name). Do not reuse a built-in skill name (e.g. `vibe`) or a name already
  taken by another skill — the collision is silently skipped and the skill will
  not load.
- `description` (required): 1–1024 chars. Say *when* to load the skill — this is
  the only text the model sees before selecting it.
- `user-invocable` (optional, default `true`): when `false` the skill is
  model-only — hidden from the `/` menu and not reachable via `/skill-name`.
- `allowed-tools` (optional, experimental): space-delimited list of pre-approved
  tools.
- `license`, `compatibility`, `metadata` (optional): metadata only. `metadata`
  is a flat string-to-string map (e.g. `display-name`, `short-description`).

Do not invent frontmatter keys. Fields from other products (e.g. `visibility`,
`defaultEnabled`) are not part of Vibe's schema and are ignored.

## Support files

Keep `SKILL.md` focused on durable instructions. Add Markdown, plain text, CSV,
JSON, or YAML support files in the same directory only when they earn their
place. Reference them by path relative to the skill directory; the model reads
them with `read_file` on demand.

## Create, update, delete

- Create: make `<skills-dir>/<slug>/SKILL.md` with valid frontmatter, where
  `<skills-dir>` is the directory for the chosen scope (`.vibe/skills/` for
  project, `~/.vibe/skills/` for global). Add support files under the same
  directory only when useful.
- Update: edit `SKILL.md` or support files in place with `edit`. Preserve useful
  existing guidance; keep the diff minimal.
- Delete: remove the whole skill directory when asked to delete a skill. Do not
  delete only `SKILL.md`.

## Applying changes

Writing under a skills directory goes through the normal `write_file` / `edit`
permission prompts — there is no separate proposal step. After changes, tell the
user they can run `/reload` to pick up the new or edited skill without
restarting. Keep the final summary brief; do not paste full file contents unless
the user asks.


</details>
