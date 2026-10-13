# 从旧版 OpenSpec 工作流迁移到 OPSX

对照旧命令与 OPSX 命令，说明迁移前检查、生成文件变化、需要关注的既有工作，以及交互和 CI 环境中的迁移方式。

- 来源：OpenSpec
- 资料类型：版本迁移指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/migration-guide.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/migration-guide.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 迁移到 OPSX

本指南可帮助您从旧版 OpenSpec 工作流程过渡到 OPSX。迁移旨在顺利进行——您现有的工作将被保留，而新系统提供了更大的灵活性。

## 发生了什么变化？

OPSX 用流畅的、基于操作的方法取代了旧的锁相工作流程。这是关键的转变：

|方面|遗产| OPSX |
|--------|--------|------|
|**命令**| `/openspec:proposal`, `/openspec:apply`, `/openspec:archive` |默认值：`/opsx:propose`、`/opsx:explore`、`/opsx:apply`、`/opsx:update`、`/opsx:sync`、`/opsx:archive`（扩展工作流程命令可选）|
|**工作流程**|一次创建所有工件|增量创建或一次性创建——您的选择|
|**回去**|尴尬的相位门|自然——随时更新任何工件|
|**定制**|固定结构|模式驱动，完全可破解|
|**配置**|`CLAUDE.md` 带标记 + `project.md`|清理`openspec/config.yaml`中的配置|

**理念的改变：**工作不是线性的。 OPSX 别再假装了。

---

## 开始之前

### 您现有的工作是安全的

迁移过程的设计考虑到了保护：

- ** `openspec/changes/`** 中的有效更改 — 完全保留。您可以使用 OPSX 命令继续它们。
- **已存档的更改** — 未受影响。你的历史仍然完好无损。
- **`openspec/specs/`**中的主要规格 - 未触及。这些是你的真相来源。
- **您在 CLAUDE.md、AGENTS.md 等中的内容 ** — 保留。仅删除 OpenSpec 标记块；您写的所有内容都会保留。

### 什么被删除

仅被替换的 OpenSpec 管理的文件：

|什么|为什么|
|------|-----|
|旧版斜杠命令 directories/files|被新的技能系统取代|
| `openspec/AGENTS.md` |过时的工作流触发器|
|`CLAUDE.md`、`AGENTS.md` 等中的 OpenSpec 标记|不再需要|

**按工具列出的旧命令位置**（示例 - 您的工具可能会有所不同）：

- Claude Code: `.claude/commands/openspec/`
- Cursor: `.cursor/commands/openspec-*.md`
- Devin 桌面，原 Windsurf：`.windsurf/workflows/openspec-*.md`
- Cline: `.clinerules/workflows/openspec-*.md`
- 小袋鼠：`.roo/commands/openspec-*.md`
- GitHub Copilot：`.github/prompts/openspec-*.prompt.md`（仅限 IDE 扩展；Copilot CLI 不支持）
- Codex：OpenSpec 现在使用规范的 `.agents/skills/openspec-*` 路径。仅在存在替换后，才会协调前 `.codex/skills` 路径下的 OpenSpec 管理的 `SKILL.md` 文件；自定义文件和不同的副本保留在原处。如果未标记的 `.agents` 树已包含 OpenSpec 技能，则 OpenSpec 会保留其现有的 Codex (`$openspec-*`) 或通用 (`/openspec-*`) 渲染，而不是从旧目录中猜测。使用 `openspec init` 明确选择 `codex` 以切换所有权。旧版提示清理仍仅针对 `$CODEX_HOME/prompts` 或 `~/.codex/prompts` 中 OpenSpec 列入白名单的文件名。
- 以及其他（Augment、Continue、Amazon Q 等）

迁移会检测您配置的任何工具并清理其旧文件。

删除列表可能看起来很长，但这些都是OpenSpec最初创建的文件。您自己的内容永远不会被删除。

### 需要注意什么

一个文件需要手动迁移：

**`openspec/project.md`** - 该文件不会自动删除，因为它可能包含您编写的项目上下文。您需要：

1. 查看其内容
2. 将有用的上下文移至 `openspec/config.yaml`（请参阅下面的指南）
3. 准备好后删除文件

**为什么我们进行此更改：**

旧的`project.md`是被动的——agents可能会读它，可能不会，可能会忘记他们读过的内容。我们发现可靠性不一致。

新的 `config.yaml` 上下文 ** 主动注入到每个 OpenSpec 规划请求 ** 中。这意味着当 AI 创建工件时，您的项目约定、技术堆栈和规则始终存在。更高的可靠性。

**权衡：**

由于上下文会注入到每个请求中，因此您需要简洁。专注于真正重要的事情：
- 技术堆栈和关键约定
- AI 需要了解的非明显约束
- 以前经常被忽视的规则

不用担心是否完美。我们仍在学习什么在这里最有效，并且我们将在实验时改进上下文注入的工作方式。

---

## 运行迁移

`openspec init` 和 `openspec update` 都会检测旧文件并指导您完成相同的清理过程。使用适合您情况的选项：

- 新安装默认配置文件 `core`（`propose`、`explore`、`apply`、`update`、`sync`、`archive`）。
- 迁移的安装通过在需要时编写 `custom` 配置文件来保留您以前安装的工作流程。

### 使用`openspec init`

如果您想添加新工具或重新配置已设置的工具，请运行此命令：

```bash
openspec init
```

init 命令检测遗留文件并指导您完成清理：

```
Upgrading to the new OpenSpec

OpenSpec now uses agent skills, the emerging standard across coding
agents. This simplifies your setup while keeping everything working
as before.

Files to remove
No user content to preserve:
  • .claude/commands/openspec/
  • openspec/AGENTS.md

Files to update
OpenSpec markers will be removed, your content preserved:
  • CLAUDE.md
  • AGENTS.md

Needs your attention
  • openspec/project.md
    We won't delete this file. It may contain useful project context.

    The new openspec/config.yaml has a "context:" section for planning
    context. This is included in every OpenSpec request and works more
    reliably than the old project.md approach.

    Review project.md, move any useful content to config.yaml's context
    section, then delete the file when ready.

? Upgrade and clean up legacy files? (Y/n)
```

**当你说“是”时会发生什么：**

1. 旧的斜杠命令目录被删除
2. OpenSpec 标记已从 `CLAUDE.md`、`AGENTS.md` 等中删除（您的内容保留）
3. `openspec/AGENTS.md` 已删除
4. 新技能安装在`.claude/skills/`
5. `openspec/config.yaml` 使用默认架构创建

### 使用`openspec update`

如果您只想将现有工具迁移并刷新到最新版本，请运行此命令：

```bash
openspec update
```

更新命令还会检测并清理遗留工件，然后刷新生成的 skills/commands 以匹配您当前的配置文件和交付设置。

### 非交互式/CI 环境

对于脚本化迁移：

```bash
openspec init --force --tools claude
```

`--force` 标志会跳过提示并自动接受清理。

这包括清理全局 Codex 提示目录中的 OpenSpec 管理的 Codex 提示文件。清理仅针对 OpenSpec 列入许可名单的旧 Codex 提示文件名，仅在替换 `.agents/skills/openspec-*` 技能存在后将其删除，并保留所有其他文件。

---

## 正在将 project.md 迁移到 config.yaml

旧的 `openspec/project.md` 是项目上下文的自由格式 Markdown 文件。新的 `openspec/config.yaml` 是结构化的，并且至关重要的是，** 被注入到每个规划请求 ** 中，因此当 AI 工作时，您的约定始终存在。

### 之前 (project.md)

```markdown
# Project Context

This is a TypeScript monorepo using React and Node.js.
We use Jest for testing and follow strict ESLint rules.
Our API is RESTful and documented in docs/api.md.

## Conventions

- All public APIs must maintain backwards compatibility
- New features should include tests
- Use Given/When/Then format for specifications
```

### 之后（config.yaml）

```yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  Testing: Jest with React Testing Library
  API: RESTful, documented in docs/api.md
  We maintain backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan for risky changes
  specs:
    - Use Given/When/Then format for scenarios
    - Reference existing patterns before inventing new ones
  design:
    - Include sequence diagrams for complex flows
```

### 主要差异

| project.md | config.yaml |
|------------|-------------|
|自由格式降价|结构化 YAML|
|一团文本|单独的上下文和每个工件规则|
|不清楚什么时候使用|上下文出现在 ALL 工件中；规则仅出现在匹配工件中|
|没有模式选择|显式 `schema:` 字段设置默认工作流程|

### 保留什么，放弃什么

迁移时，要有选择性。问问自己：“AI 是否需要这个来满足 * 每个 * 规划请求？”

**`context:`**的良好候选人
- 技术堆栈（语言、框架、数据库）
- 关键架构模式（monorepo、微服务等）
- 非明显的约束（“我们不能使用库 X 因为......”）
- 经常被忽视的关键约定

**移至`rules:`改为**
- 特定于工件的格式（“在规范中使用 Given/When/Then”）
- 审查标准（“提案必须包括回滚计划”）
- 这些仅针对匹配的工件出现，使其他请求更轻松

**完全省略**
- AI 已经知道的一般最佳实践
- 可以总结的详细解释
- 不影响当前工作的历史背景

### 迁移步骤

1. **创建config.yaml**（如果init尚未创建）：
   ```yaml
   schema: spec-driven
   ```

2. **添加您的上下文**（要简洁 - 这适用于每个请求）：
   ```yaml
   context: |
     Your project background goes here.
     Focus on what the AI genuinely needs to know.
   ```

3. **添加每个工件规则**（可选）：
   ```yaml
   rules:
     proposal:
       - Your proposal-specific guidance
     specs:
       - Your spec-writing rules
   ```

4. **一旦你移动了所有有用的东西，就删除project.md**。

**不要想太多。**从要点开始，迭代。如果您发现 AI 缺少重要内容，请添加它。如果上下文感觉臃肿，请修剪它。这是一份动态文件。

### 需要帮助吗？使用此提示

如果您不确定如何提取 project.md，请询问您的 AI 助理：

```
I'm migrating from OpenSpec's old project.md to the new config.yaml format.

Here's my current project.md:
[paste your project.md content]

Please help me create a config.yaml with:
1. A concise `context:` section (this gets injected into every planning request, so keep it tight—focus on tech stack, key constraints, and conventions that often get ignored)
2. `rules:` for specific artifacts if any content is artifact-specific (e.g., "use Given/When/Then" belongs in specs rules, not global context)

Leave out anything generic that AI models already know. Be ruthless about brevity.
```

AI 将帮助您确定哪些是必需的，哪些是可以修剪的。

---

## 新命令

命令可用性取决于配置文件：

**默认（`core`配置文件）：**

|命令|目的|
|---------|---------|
| `/opsx:propose` |一步创建变更并生成规划工件|
| `/opsx:explore` |思考没有结构的想法|
| `/opsx:apply` |执行tasks.md的任务|
| `/opsx:update` |修改变更的计划工件并保持它们的连贯性|
| `/opsx:sync` |将增量规格合并到主要规格中|
| `/opsx:archive` |完成并存档变更|

**扩展工作流程（自定义选择）：**

|命令|目的|
|---------|---------|
| `/opsx:new` |开始新的变革脚手架|
| `/opsx:continue` |创建下一个工件（一次一个）|
| `/opsx:ff` |快进——立即创建规划工件|
| `/opsx:verify` |验证实施是否符合规范|
| `/opsx:bulk-archive` |一次存档多个更改|
| `/opsx:onboard` |引导式端到端入职工作流程|

使用 `openspec config profile` 启用扩展命令，然后运行 `openspec update`。

### 传统命令映射

|遗产|OPSX 等效|
|--------|-----------------|
| `/openspec:proposal` |`/opsx:propose`（默认）或 `/opsx:new` 然后 `/opsx:ff`（扩展）|
| `/openspec:apply` | `/opsx:apply` |
| `/openspec:archive` | `/opsx:archive` |

### 新功能

这些功能是扩展工作流命令集的一部分。

**颗粒工件创建：**
```
/opsx:continue
```
根据依赖关系一次创建一个工件。当您想要查看每个步骤时使用此选项。

**探索模式：**
```
/opsx:explore
```
在做出改变之前，与合作伙伴一起思考想法。

---

## 了解新架构

### 从锁相到流体

遗留工作流程强制线性进展：

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   PLANNING   │ ───► │ IMPLEMENTING │ ───► │   ARCHIVING  │
│    PHASE     │      │    PHASE     │      │    PHASE     │
└──────────────┘      └──────────────┘      └──────────────┘

If you're in implementation and realize the design is wrong?
Too bad. Phase gates don't let you go back easily.
```

OPSX 使用动作，而不是阶段：

```
         ┌───────────────────────────────────────────────┐
         │           ACTIONS (not phases)                │
         │                                               │
         │     new ◄──► continue ◄──► apply ◄──► archive │
         │      │          │           │             │   │
         │      └──────────┴───────────┴─────────────┘   │
         │                    any order                  │
         └───────────────────────────────────────────────┘
```

### 依赖图

工件形成有向图。依赖是推动者，而不是大门：

```
                        proposal
                       (root node)
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
           specs                       design
        (requires:                  (requires:
         proposal)                   proposal)
              │                           │
              └─────────────┬─────────────┘
                            │
                            ▼
                         tasks
                     (requires:
                     specs, design)
```

当您运行 `/opsx:continue` 时，它会检查已准备好的内容并提供下一个工件。您还可以按任意顺序创建多个现成的工件。

### 技能与命令

旧系统使用特定于工具的命令文件：

```
.claude/commands/openspec/
├── proposal.md
├── apply.md
└── archive.md
```

OPSX 使用新兴的 ** 技能 ** 标准：

```
.claude/skills/
├── openspec-explore/SKILL.md
├── openspec-new-change/SKILL.md
├── openspec-continue-change/SKILL.md
├── openspec-apply-change/SKILL.md
└── ...
```

技能可通过多种 AI 编码工具进行识别，并提供更丰富的元数据。

Codex在OPSX中仅包含技能。 OpenSpec不再生成Codex自定义提示文件；请改用生成的 `.agents/skills/openspec-*` 目录。

---

## 继续现有的变化

您正在进行的更改可与 OPSX 命令无缝协作。

**对旧工作流程有主动更改吗？**

```
/opsx:apply add-my-feature
```

OPSX 读取现有工件并从您上次停下的地方继续。

**想要向现有变更添加更多工件？**

```
/opsx:continue add-my-feature
```

显示根据已存在的内容准备创建的内容。

**需要查看状态吗？**

```bash
openspec status --change add-my-feature
```

---

## 新的配置系统

### config.yaml 结构

```yaml
# Required: Default schema for new changes
schema: spec-driven

# Optional: Project context (max 50KB)
# Injected into ALL artifact instructions
context: |
  Your project background, tech stack,
  conventions, and constraints.

# Optional: Per-artifact rules
# Only injected into matching artifacts
rules:
  proposal:
    - Include rollback plan
  specs:
    - Use Given/When/Then format
  design:
    - Document fallback strategies
  tasks:
    - Break into 2-hour maximum chunks
```

### 模式解析

当确定使用哪个模式时，OPSX 按顺序检查：

1. **CLI 标志**：`--schema <name>`（最高优先级）
2. **更改元数据**：更改目录中的`.openspec.yaml`
3. **项目配置**：`openspec/config.yaml`
4. **默认**：`spec-driven`

### 可用模式

|模式|文物|最适合|
|--------|-----------|----------|
| `spec-driven` |提案→规格→设计→任务|大多数项目|

列出所有可用的模式：

```bash
openspec schemas
```

### 自定义模式

创建您自己的工作流程：

```bash
openspec schema init my-workflow
```

或者分叉一个现有的：

```bash
openspec schema fork spec-driven my-workflow
```

详情请参见[定制](<OpenSpec 项目配置与工作流定制.md>)。

---

## 故障排除

### “在非交互模式下检测到旧文件”

您正在 CI 或非交互式环境中运行。用途：

```bash
openspec init --force
```

### 迁移后命令不出现

重新启动您的IDE。技能在启动时被检测到。

### “规则中的未知神器 ID”

检查您的 `rules:` 键是否与架构的工件 ID 匹配：

- **规格驱动**：`proposal`、`specs`、`design`、`tasks`

运行此命令以查看有效的工件 ID：

```bash
openspec schemas --json
```

### 配置未应用

1. 确保文件位于 `openspec/config.yaml`（而不是 `.yml`）
2. 验证 YAML 语法
3. 配置更改立即生效——无需重启

### project.md 未迁移

系统有意保留 `project.md`，因为它可能包含您的自定义内容。手动查看，将有用的部分移至`config.yaml`，然后删除。

### 想看看会清理什么吗？

运行 init 并拒绝清理提示 — 您将看到完整的检测摘要，而无需进行任何更改。

---

## 快速参考

### 迁移后的文件

```
project/
├── openspec/
│   ├── specs/                    # Unchanged
│   ├── changes/                  # Unchanged
│   │   └── archive/              # Unchanged
│   └── config.yaml               # NEW: Project configuration
├── .claude/
│   └── skills/                   # NEW: OPSX skills
│       ├── openspec-propose/     # default core profile
│       ├── openspec-explore/
│       ├── openspec-apply-change/
│       ├── openspec-update-change/
│       ├── openspec-sync-specs/
│       ├── openspec-archive-change/
│       └── ...                   # expanded profile adds new/continue/ff/etc.
├── CLAUDE.md                     # OpenSpec markers removed, your content preserved
└── AGENTS.md                     # OpenSpec markers removed, your content preserved
```

### 消失了什么

- `.claude/commands/openspec/` — 替换为 `.claude/skills/`
- `openspec/AGENTS.md` — 已过时
- `openspec/project.md` — 迁移到 `config.yaml`，然后删除
- OpenSpec `CLAUDE.md`、`AGENTS.md` 等中的标记块。

### 命令备忘单

```text
/opsx:propose      Start quickly (default core profile)
/opsx:apply        Implement tasks
/opsx:archive      Finish and archive

# Expanded workflow (if enabled):
/opsx:new          Scaffold a change
/opsx:continue     Create next artifact
/opsx:ff           Create planning artifacts
```

---

## 寻求帮助

- **不和谐**：[discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub 问题**：[github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **文档**：[docs/opsx.md](<OpenSpec OPSX 工作流机制.md>)完整的OPSX参考


## 原文对照

<details>
<summary>展开完整原文</summary>

# Migrating to OPSX

This guide helps you transition from the legacy OpenSpec workflow to OPSX. The migration is designed to be smooth—your existing work is preserved, and the new system offers more flexibility.

## What's Changing?

OPSX replaces the old phase-locked workflow with a fluid, action-based approach. Here's the key shift:

| Aspect | Legacy | OPSX |
|--------|--------|------|
| **Commands** | `/openspec:proposal`, `/openspec:apply`, `/openspec:archive` | Default: `/opsx:propose`, `/opsx:explore`, `/opsx:apply`, `/opsx:update`, `/opsx:sync`, `/opsx:archive` (expanded workflow commands optional) |
| **Workflow** | Create all artifacts at once | Create incrementally or all at once—your choice |
| **Going back** | Awkward phase gates | Natural—update any artifact anytime |
| **Customization** | Fixed structure | Schema-driven, fully hackable |
| **Configuration** | `CLAUDE.md` with markers + `project.md` | Clean config in `openspec/config.yaml` |

**The philosophy change:** Work isn't linear. OPSX stops pretending it is.

---

## Before You Begin

### Your Existing Work Is Safe

The migration process is designed with preservation in mind:

- **Active changes in `openspec/changes/`** — Completely preserved. You can continue them with OPSX commands.
- **Archived changes** — Untouched. Your history remains intact.
- **Main specs in `openspec/specs/`** — Untouched. These are your source of truth.
- **Your content in CLAUDE.md, AGENTS.md, etc.** — Preserved. Only the OpenSpec marker blocks are removed; everything you wrote stays.

### What Gets Removed

Only OpenSpec-managed files that are being replaced:

| What | Why |
|------|-----|
| Legacy slash command directories/files | Replaced by the new skills system |
| `openspec/AGENTS.md` | Obsolete workflow trigger |
| OpenSpec markers in `CLAUDE.md`, `AGENTS.md`, etc. | No longer needed |

**Legacy command locations by tool** (examples—your tool may vary):

- Claude Code: `.claude/commands/openspec/`
- Cursor: `.cursor/commands/openspec-*.md`
- Devin Desktop, formerly Windsurf: `.windsurf/workflows/openspec-*.md`
- Cline: `.clinerules/workflows/openspec-*.md`
- Roo: `.roo/commands/openspec-*.md`
- GitHub Copilot: `.github/prompts/openspec-*.prompt.md` (IDE extensions only; not supported in Copilot CLI)
- Codex: OpenSpec now uses the canonical `.agents/skills/openspec-*` path. OpenSpec-managed `SKILL.md` files under the former `.codex/skills` path are reconciled only after replacements exist; custom files and divergent copies stay in place. If an unmarked `.agents` tree already contains OpenSpec skills, OpenSpec preserves its existing Codex (`$openspec-*`) or generic (`/openspec-*`) rendering instead of guessing from the legacy directory. Select `codex` explicitly with `openspec init` to switch ownership. Legacy prompt cleanup still targets only OpenSpec's allowlisted filenames in `$CODEX_HOME/prompts` or `~/.codex/prompts`.
- And others (Augment, Continue, Amazon Q, etc.)

The migration detects whichever tools you have configured and cleans up their legacy files.

The removal list may seem long, but these are all files that OpenSpec originally created. Your own content is never deleted.

### What Needs Your Attention

One file requires manual migration:

**`openspec/project.md`** — This file isn't deleted automatically because it may contain project context you've written. You'll need to:

1. Review its contents
2. Move useful context to `openspec/config.yaml` (see guidance below)
3. Delete the file when ready

**Why we made this change:**

The old `project.md` was passive—agents might read it, might not, might forget what they read. We found reliability was inconsistent.

The new `config.yaml` context is **actively injected into every OpenSpec planning request**. This means your project conventions, tech stack, and rules are always present when the AI is creating artifacts. Higher reliability.

**The tradeoff:**

Because context is injected into every request, you'll want to be concise. Focus on what really matters:
- Tech stack and key conventions
- Non-obvious constraints the AI needs to know
- Rules that frequently got ignored before

Don't worry about getting it perfect. We're still learning what works best here, and we'll be improving how context injection works as we experiment.

---

## Running the Migration

Both `openspec init` and `openspec update` detect legacy files and guide you through the same cleanup process. Use whichever fits your situation:

- New installs default to profile `core` (`propose`, `explore`, `apply`, `update`, `sync`, `archive`).
- Migrated installs preserve your previously installed workflows by writing a `custom` profile when needed.

### Using `openspec init`

Run this if you want to add new tools or reconfigure which tools are set up:

```bash
openspec init
```

The init command detects legacy files and guides you through cleanup:

```
Upgrading to the new OpenSpec

OpenSpec now uses agent skills, the emerging standard across coding
agents. This simplifies your setup while keeping everything working
as before.

Files to remove
No user content to preserve:
  • .claude/commands/openspec/
  • openspec/AGENTS.md

Files to update
OpenSpec markers will be removed, your content preserved:
  • CLAUDE.md
  • AGENTS.md

Needs your attention
  • openspec/project.md
    We won't delete this file. It may contain useful project context.

    The new openspec/config.yaml has a "context:" section for planning
    context. This is included in every OpenSpec request and works more
    reliably than the old project.md approach.

    Review project.md, move any useful content to config.yaml's context
    section, then delete the file when ready.

? Upgrade and clean up legacy files? (Y/n)
```

**What happens when you say yes:**

1. Legacy slash command directories are removed
2. OpenSpec markers are stripped from `CLAUDE.md`, `AGENTS.md`, etc. (your content stays)
3. `openspec/AGENTS.md` is deleted
4. New skills are installed in `.claude/skills/`
5. `openspec/config.yaml` is created with a default schema

### Using `openspec update`

Run this if you just want to migrate and refresh your existing tools to the latest version:

```bash
openspec update
```

The update command also detects and cleans up legacy artifacts, then refreshes generated skills/commands to match your current profile and delivery settings.

### Non-Interactive / CI Environments

For scripted migrations:

```bash
openspec init --force --tools claude
```

The `--force` flag skips prompts and auto-accepts cleanup.

This includes cleanup of OpenSpec-managed Codex prompt files in the global Codex prompt directory. Cleanup only targets OpenSpec's allowlisted legacy Codex prompt filenames, removes them only after replacement `.agents/skills/openspec-*` skills exist, and preserves all other files.

---

## Migrating project.md to config.yaml

The old `openspec/project.md` was a freeform markdown file for project context. The new `openspec/config.yaml` is structured and—critically—**injected into every planning request** so your conventions are always present when the AI works.

### Before (project.md)

```markdown
# Project Context

This is a TypeScript monorepo using React and Node.js.
We use Jest for testing and follow strict ESLint rules.
Our API is RESTful and documented in docs/api.md.

## Conventions

- All public APIs must maintain backwards compatibility
- New features should include tests
- Use Given/When/Then format for specifications
```

### After (config.yaml)

```yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  Testing: Jest with React Testing Library
  API: RESTful, documented in docs/api.md
  We maintain backwards compatibility for all public APIs

rules:
  proposal:
    - Include rollback plan for risky changes
  specs:
    - Use Given/When/Then format for scenarios
    - Reference existing patterns before inventing new ones
  design:
    - Include sequence diagrams for complex flows
```

### Key Differences

| project.md | config.yaml |
|------------|-------------|
| Freeform markdown | Structured YAML |
| One blob of text | Separate context and per-artifact rules |
| Unclear when it's used | Context appears in ALL artifacts; rules appear in matching artifacts only |
| No schema selection | Explicit `schema:` field sets default workflow |

### What to Keep, What to Drop

When migrating, be selective. Ask yourself: "Does the AI need this for *every* planning request?"

**Good candidates for `context:`**
- Tech stack (languages, frameworks, databases)
- Key architectural patterns (monorepo, microservices, etc.)
- Non-obvious constraints ("we can't use library X because...")
- Critical conventions that often get ignored

**Move to `rules:` instead**
- Artifact-specific formatting ("use Given/When/Then in specs")
- Review criteria ("proposals must include rollback plans")
- These only appear for the matching artifact, keeping other requests lighter

**Leave out entirely**
- General best practices the AI already knows
- Verbose explanations that could be summarized
- Historical context that doesn't affect current work

### Migration Steps

1. **Create config.yaml** (if not already created by init):
   ```yaml
   schema: spec-driven
   ```

2. **Add your context** (be concise—this goes into every request):
   ```yaml
   context: |
     Your project background goes here.
     Focus on what the AI genuinely needs to know.
   ```

3. **Add per-artifact rules** (optional):
   ```yaml
   rules:
     proposal:
       - Your proposal-specific guidance
     specs:
       - Your spec-writing rules
   ```

4. **Delete project.md** once you've moved everything useful.

**Don't overthink it.** Start with the essentials and iterate. If you notice the AI missing something important, add it. If context feels bloated, trim it. This is a living document.

### Need Help? Use This Prompt

If you're unsure how to distill your project.md, ask your AI assistant:

```
I'm migrating from OpenSpec's old project.md to the new config.yaml format.

Here's my current project.md:
[paste your project.md content]

Please help me create a config.yaml with:
1. A concise `context:` section (this gets injected into every planning request, so keep it tight—focus on tech stack, key constraints, and conventions that often get ignored)
2. `rules:` for specific artifacts if any content is artifact-specific (e.g., "use Given/When/Then" belongs in specs rules, not global context)

Leave out anything generic that AI models already know. Be ruthless about brevity.
```

The AI will help you identify what's essential vs. what can be trimmed.

---

## The New Commands

Command availability is profile-dependent:

**Default (`core` profile):**

| Command | Purpose |
|---------|---------|
| `/opsx:propose` | Create a change and generate planning artifacts in one step |
| `/opsx:explore` | Think through ideas with no structure |
| `/opsx:apply` | Implement tasks from tasks.md |
| `/opsx:update` | Revise a change's planning artifacts and keep them coherent |
| `/opsx:sync` | Merge delta specs into main specs |
| `/opsx:archive` | Finalize and archive the change |

**Expanded workflow (custom selection):**

| Command | Purpose |
|---------|---------|
| `/opsx:new` | Start a new change scaffold |
| `/opsx:continue` | Create the next artifact (one at a time) |
| `/opsx:ff` | Fast-forward—create planning artifacts at once |
| `/opsx:verify` | Validate implementation matches specs |
| `/opsx:bulk-archive` | Archive multiple changes at once |
| `/opsx:onboard` | Guided end-to-end onboarding workflow |

Enable expanded commands with `openspec config profile`, then run `openspec update`.

### Command Mapping from Legacy

| Legacy | OPSX Equivalent |
|--------|-----------------|
| `/openspec:proposal` | `/opsx:propose` (default) or `/opsx:new` then `/opsx:ff` (expanded) |
| `/openspec:apply` | `/opsx:apply` |
| `/openspec:archive` | `/opsx:archive` |

### New Capabilities

These capabilities are part of the expanded workflow command set.

**Granular artifact creation:**
```
/opsx:continue
```
Creates one artifact at a time based on dependencies. Use this when you want to review each step.

**Exploration mode:**
```
/opsx:explore
```
Think through ideas with a partner before committing to a change.

---

## Understanding the New Architecture

### From Phase-Locked to Fluid

The legacy workflow forced linear progression:

```
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│   PLANNING   │ ───► │ IMPLEMENTING │ ───► │   ARCHIVING  │
│    PHASE     │      │    PHASE     │      │    PHASE     │
└──────────────┘      └──────────────┘      └──────────────┘

If you're in implementation and realize the design is wrong?
Too bad. Phase gates don't let you go back easily.
```

OPSX uses actions, not phases:

```
         ┌───────────────────────────────────────────────┐
         │           ACTIONS (not phases)                │
         │                                               │
         │     new ◄──► continue ◄──► apply ◄──► archive │
         │      │          │           │             │   │
         │      └──────────┴───────────┴─────────────┘   │
         │                    any order                  │
         └───────────────────────────────────────────────┘
```

### Dependency Graph

Artifacts form a directed graph. Dependencies are enablers, not gates:

```
                        proposal
                       (root node)
                            │
              ┌─────────────┴─────────────┐
              │                           │
              ▼                           ▼
           specs                       design
        (requires:                  (requires:
         proposal)                   proposal)
              │                           │
              └─────────────┬─────────────┘
                            │
                            ▼
                         tasks
                     (requires:
                     specs, design)
```

When you run `/opsx:continue`, it checks what's ready and offers the next artifact. You can also create multiple ready artifacts in any order.

### Skills vs Commands

The legacy system used tool-specific command files:

```
.claude/commands/openspec/
├── proposal.md
├── apply.md
└── archive.md
```

OPSX uses the emerging **skills** standard:

```
.claude/skills/
├── openspec-explore/SKILL.md
├── openspec-new-change/SKILL.md
├── openspec-continue-change/SKILL.md
├── openspec-apply-change/SKILL.md
└── ...
```

Skills are recognized across multiple AI coding tools and provide richer metadata.

Codex is skills-only in OPSX. OpenSpec no longer generates Codex custom prompt files; use the generated `.agents/skills/openspec-*` directories instead.

---

## Continuing Existing Changes

Your in-progress changes work seamlessly with OPSX commands.

**Have an active change from the legacy workflow?**

```
/opsx:apply add-my-feature
```

OPSX reads the existing artifacts and continues from where you left off.

**Want to add more artifacts to an existing change?**

```
/opsx:continue add-my-feature
```

Shows what's ready to create based on what already exists.

**Need to see status?**

```bash
openspec status --change add-my-feature
```

---

## The New Config System

### config.yaml Structure

```yaml
# Required: Default schema for new changes
schema: spec-driven

# Optional: Project context (max 50KB)
# Injected into ALL artifact instructions
context: |
  Your project background, tech stack,
  conventions, and constraints.

# Optional: Per-artifact rules
# Only injected into matching artifacts
rules:
  proposal:
    - Include rollback plan
  specs:
    - Use Given/When/Then format
  design:
    - Document fallback strategies
  tasks:
    - Break into 2-hour maximum chunks
```

### Schema Resolution

When determining which schema to use, OPSX checks in order:

1. **CLI flag**: `--schema <name>` (highest priority)
2. **Change metadata**: `.openspec.yaml` in the change directory
3. **Project config**: `openspec/config.yaml`
4. **Default**: `spec-driven`

### Available Schemas

| Schema | Artifacts | Best For |
|--------|-----------|----------|
| `spec-driven` | proposal → specs → design → tasks | Most projects |

List all available schemas:

```bash
openspec schemas
```

### Custom Schemas

Create your own workflow:

```bash
openspec schema init my-workflow
```

Or fork an existing one:

```bash
openspec schema fork spec-driven my-workflow
```

See [Customization](<OpenSpec 项目配置与工作流定制.md>) for details.

---

## Troubleshooting

### "Legacy files detected in non-interactive mode"

You're running in a CI or non-interactive environment. Use:

```bash
openspec init --force
```

### Commands not appearing after migration

Restart your IDE. Skills are detected at startup.

### "Unknown artifact ID in rules"

Check that your `rules:` keys match your schema's artifact IDs:

- **spec-driven**: `proposal`, `specs`, `design`, `tasks`

Run this to see valid artifact IDs:

```bash
openspec schemas --json
```

### Config not being applied

1. Ensure the file is at `openspec/config.yaml` (not `.yml`)
2. Validate YAML syntax
3. Config changes take effect immediately—no restart needed

### project.md not migrated

The system intentionally preserves `project.md` because it may contain your custom content. Review it manually, move useful parts to `config.yaml`, then delete it.

### Want to see what would be cleaned up?

Run init and decline the cleanup prompt—you'll see the full detection summary without any changes being made.

---

## Quick Reference

### Files After Migration

```
project/
├── openspec/
│   ├── specs/                    # Unchanged
│   ├── changes/                  # Unchanged
│   │   └── archive/              # Unchanged
│   └── config.yaml               # NEW: Project configuration
├── .claude/
│   └── skills/                   # NEW: OPSX skills
│       ├── openspec-propose/     # default core profile
│       ├── openspec-explore/
│       ├── openspec-apply-change/
│       ├── openspec-update-change/
│       ├── openspec-sync-specs/
│       ├── openspec-archive-change/
│       └── ...                   # expanded profile adds new/continue/ff/etc.
├── CLAUDE.md                     # OpenSpec markers removed, your content preserved
└── AGENTS.md                     # OpenSpec markers removed, your content preserved
```

### What's Gone

- `.claude/commands/openspec/` — replaced by `.claude/skills/`
- `openspec/AGENTS.md` — obsolete
- `openspec/project.md` — migrate to `config.yaml`, then delete
- OpenSpec marker blocks in `CLAUDE.md`, `AGENTS.md`, etc.

### Command Cheatsheet

```text
/opsx:propose      Start quickly (default core profile)
/opsx:apply        Implement tasks
/opsx:archive      Finish and archive

# Expanded workflow (if enabled):
/opsx:new          Scaffold a change
/opsx:continue     Create next artifact
/opsx:ff           Create planning artifacts
```

---

## Getting Help

- **Discord**: [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub Issues**: [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **Documentation**: [docs/opsx.md](<OpenSpec OPSX 工作流机制.md>) for the full OPSX reference


</details>
