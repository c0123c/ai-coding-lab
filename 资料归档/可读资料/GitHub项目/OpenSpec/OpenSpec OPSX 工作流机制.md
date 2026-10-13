# OpenSpec OPSX 工作流机制

介绍 OPSX 基于动作和工件依赖的工作流、使用体验、项目配置以及 Schema 驱动的工件生成过程。

- 来源：OpenSpec
- 资料类型：OPSX 工作流说明
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/opsx.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/opsx.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# OPSX 工作流程

> 欢迎在[Discord](<https://discord.gg/YctCnvvshC>)上提供反馈。

## 它是什么？

OPSX 现在是 OpenSpec 的标准工作流程。

这是针对 OpenSpec 更改的 ** 流畅、迭代工作流程 **。不再有严格的阶段——只需您可以随时采取的行动。

## 为什么存在

旧的 OpenSpec 工作流程可以工作，但它 ** 被锁定 **：

- **指令是硬编码的** - 埋在TypeScript中，你无法更改它们
- **全有或全无** - 一个大命令创建一切，无法测试单个部分
- **固定结构** — 每个人的工作流程相同，无需定制
- **黑匣子** - 当AI输出不好时，你无法调整提示

**OPSX 打开它。** 现在任何人都可以：

1. **按照说明进行实验** — 编辑模板，看看AI是否效果更好
2. **精细测试** - 独立验证每个工件的指令
3. **自定义工作流程** — 定义您自己的工件和依赖项
4. **快速迭代** — 更改模板，立即测试，无需重建

```
Legacy workflow:                      OPSX:
┌────────────────────────┐           ┌────────────────────────┐
│  Hardcoded in package  │           │  schema.yaml           │◄── You edit this
│  (can't change)        │           │  templates/*.md        │◄── Or this
│        ↓               │           │        ↓               │
│  Wait for new release  │           │  Instant effect        │
│        ↓               │           │        ↓               │
│  Hope it's better      │           │  Test it yourself      │
└────────────────────────┘           └────────────────────────┘
```

**这适合所有人：**
- **Teams** — 创建符合您实际工作方式的工作流程
- **高级用户** - 调整提示以获得更好的代码库输出AI
- **OpenSpec 贡献者 ** — 在不发布的情况下尝试新方法

我们仍在学习什么是最有效的。 OPSX让我们一起学习。

## 用户体验

**线性工作流程的问题：**
你“处于规划阶段”，然后“处于实施阶段”，然后“完成”。但真正的工作却并非如此。你实现了一些东西，意识到你的设计是错误的，需要更新规范，继续实现。线性阶段与工作的实际发生方式相矛盾。

**OPSX做法：**
- **行动，而不是阶段** — 创建、实施、更新、归档 — 随时执行其中任何一项
- **依赖关系是推动者** - 它们显示什么是可能的，而不是接下来需要什么

```
  proposal ──→ specs ──→ design ──→ tasks ──→ implement
```

## 设置

```bash
# Make sure you have openspec installed — skills are automatically generated
openspec init
```

这会创建`.claude/skills/`（或同等内容）中的技能，AI编码助手会自动检测。

默认情况下，OpenSpec 使用 `core` 工作流程配置文件（`propose`、`explore`、`apply`、`update`、`sync`、`archive`）。如果您需要扩展的工作流程命令（`new`、`continue`、`ff`、`verify`、`bulk-archive`、`onboard`），请使用 `openspec config profile` 配置它们并应用`openspec update`。

在设置过程中，系统会提示您创建 ** 项目配置 ** (`openspec/config.yaml`)。这是可选的，但建议这样做。

## 项目配置

项目配置允许您设置默认值并将特定于项目的上下文注入到所有工件中。

### 创建配置

配置是在`openspec init`期间创建的，或者手动创建的：

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  API conventions: RESTful, JSON responses
  Testing: Vitest for unit tests, Playwright for e2e
  Style: ESLint with Prettier, strict TypeScript

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format for scenarios
  design:
    - Include sequence diagrams for complex flows
```

### 配置字段

|领域|类型|描述|
|-------|------|-------------|
| `schema` |字符串|新更改的默认架构（例如，`spec-driven`）|
| `context` |字符串|项目上下文注入到所有工件指令中|
| `rules` |对象|每个工件的规则，由工件 ID 键入|

### 它是如何运作的

**架构优先级**（从最高到最低）：
1. CLI 标志 (`--schema <name>`)
2. 更改元数据（更改目录中的`.openspec.yaml`）
3. 项目配置（`openspec/config.yaml`）
4. 默认（`spec-driven`）

**上下文注入：**
- 上下文被添加到每个工件的指令之前
- 包裹在 `<context>...</context>` 标签中
- 帮助AI了解您的项目约定

**规则注入：**
- 仅针对匹配工件注入规则
- 包裹在 `<rules>...</rules>` 标签中
- 出现在上下文之后、模板之前

### 按架构划分的工件 ID

**规范驱动**（默认）：
- `proposal` — 变更提案
- `specs` — 规格
- `design` — 技术设计
- `tasks` — 实施任务

### 配置验证

- `rules` 中的未知工件 ID 会生成警告
- 模式名称根据可用模式进行验证
- 上下文的大小限制为 50KB
- 报告无效的 YAML 和行号

### 故障排除

**“规则中的未知工件ID：X”**
- 检查工件 ID 是否与您的架构匹配（请参阅上面的列表）
- 运行 `openspec schemas --json` 以查看每个架构的工件 ID

**配置未应用：**
- 确保文件位于 `openspec/config.yaml`（而不是 `.yml`）
- 使用验证器检查 YAML 语法
- 配置更改立即生效（无需重启）

**上下文太大：**
- 上下文限制为 50KB
- 总结或链接到外部文档

## 命令

|命令|它的作用|
|---------|--------------|
| `/opsx:propose` |一步创建变更并生成计划工件（默认快速路径）|
| `/opsx:explore` |思考思路、研究问题、明确需求|
| `/opsx:new` |启动新的变革脚手架（扩展的工作流程）|
| `/opsx:continue` |创建下一个工件（扩展工作流程）|
| `/opsx:ff` |快进规划工件（扩展工作流程）|
| `/opsx:apply` |实施任务，根据需要更新工件|
| `/opsx:update` |修改变更的计划工件并保持它们的连贯性|
| `/opsx:verify` |根据工件验证实施（扩展工作流程）|
| `/opsx:sync` |将增量规格合并到主要规格中（可选）|
| `/opsx:archive` |完成后存档|
| `/opsx:bulk-archive` |存档多个已完成的更改（扩展工作流程）|
| `/opsx:onboard` |端到端变更的引导演练（扩展工作流程）|

## 用途

### 探索一个想法
```
/opsx:explore
```
思考想法、调查问题、比较选项。不需要结构——只需一个有思想的合作伙伴。当见解具体化时，过渡到 `/opsx:propose`（默认）或 `/opsx:new`/`/opsx:ff`（扩展）。

### 开始新的改变
```
/opsx:propose
```
创建变更并生成实施前所需的规划工件。

如果您启用了扩展工作流程，则可以改为使用：

```text
/opsx:new        # scaffold only
/opsx:continue   # create one artifact at a time
/opsx:ff         # create all planning artifacts at once
```

### 创建工件
```
/opsx:continue
```
根据依赖关系显示准备创建的内容，然后创建一个工件。反复使用以逐步建立您的更改。

```
/opsx:ff add-dark-mode
```
立即创建所有计划工件。当您对正在构建的内容有清晰的了解时使用。

### 实施（流体部分）
```
/opsx:apply
```
完成任务，边做边检查。如果您要处理多个更改，可以运行 `/opsx:apply <name>`；否则，它应该从对话中进行推断，并提示您选择是否无法判断。

### 更新更改
```
/opsx:update add-dark-mode - we're storing the theme in a cookie now
```
修改变更的现有规划工件并使它们在任何方向上保持一致（设计编辑可能会波及到提案）。它从不编辑代码。每次编辑都会首先与您确认。请参阅[更新参考](<OpenSpec AI 聊天命令参考.md#opsxupdate>)，了解它如何在不启动新工件的情况下处理丢失的文件。

如果更改已实施，则建议 `/opsx:apply`，以便代码赶上修订后的计划。如果您的修订更改了更改的*意图*，请重新开始。请参阅[何时更新与重新开始](<#何时更新与重新开始>)。

### 同步增量规格
```text
/opsx:sync
```
将当前更改的增量规范合并到您的主 `openspec/specs/` 中，而不进行存档 - 更改保持活动状态。它适用于整个增量：`## REMOVED` 下的要求已从主规范中删除，并且重命名的要求已就地重新命名，而增量未提及的内容则保持不变。同步是可选的 - 如果尚未同步，存档会提示您先同步。当您希望在归档之前更新主要规范时，当并行更改需要基于刚刚添加的规范时，或者当您想在归档之前查看合并的主要规范时，请使用它。

### 完成
```
/opsx:archive   # Move to archive when done (prompts to sync specs if needed)
```

## 何时更新与重新开始

您始终可以在实施之前编辑您的提案或规格。但什么时候精炼变成了“这是不同的工作”呢？

### 提案的重点是什么

提案定义了三件事：
1. **意图** — 您要解决什么问题？
2. **范围** — in/out 的界限是什么？
3. **方法** — 你将如何解决它？

问题是：哪些改变了，改变了多少？

### 在以下情况下更新现有更改：

**同样的初心，精致的执行**
- 你发现了你没有考虑到的边缘情况
- 方法需要调整，但目标不变
- 实施表明设计略有偏差

**范围缩小**
- 您意识到全范围太大，想先发货MVP
- “添加深色模式”→“添加深色模式切换（v2中的系统偏好设置）”

**学习驱动的修正**
- 代码库的结构与您的想法不同
- 依赖项未按预期工作
- “使用 CSS 变量”→“使用 Tailwind 的 dark: 前缀代替”

### 在以下时间开始新的改变：

**意图根本改变**
- 现在问题本身不同了
- “添加深色模式”→“添加具有自定义颜色、字体、间距的综合主题系统”

**范围爆炸**
- 变化如此之大，本质上是不同的工作
- 更新后原提案将无法识别
- “修复登录错误”→“重写认证系统”

**原件已完成**
- 原始更改可以标记为“完成”
- 新作品是独立的，而不是改进
- 完成“添加黑暗模式MVP”→存档→新更改“增强黑暗模式”

### 启发法

```
                        ┌─────────────────────────────────────┐
                        │     Is this the same work?          │
                        └──────────────┬──────────────────────┘
                                       │
                    ┌──────────────────┼──────────────────┐
                    │                  │                  │
                    ▼                  ▼                  ▼
             Same intent?      >50% overlap?      Can original
             Same problem?     Same scope?        be "done" without
                    │                  │          these changes?
                    │                  │                  │
          ┌────────┴────────┐  ┌──────┴──────┐   ┌───────┴───────┐
          │                 │  │             │   │               │
         YES               NO YES           NO  NO              YES
          │                 │  │             │   │               │
          ▼                 ▼  ▼             ▼   ▼               ▼
       UPDATE            NEW  UPDATE       NEW  UPDATE          NEW
```

|测试|更新|新变化|
|------|--------|------------|
|**身份**|“同样的事情，精致”|《不一样的工作》|
|**范围重叠**| >50% 重叠|<50% 重叠|
|**完成**|不进行改变就无法“完成”|可以独立完成原创、新作|
|**故事**|更新链讲述连贯的故事|补丁只会让人困惑而不是澄清|

### 原则

> **更新保留上下文。新的变化提供了清晰度。**
>
> 当你的思维历史有价值时选择更新。
> 当重新开始时选择新比修补更清晰。

把它想象成 git 分支：
- 在开发同一个功能的同时不断投入
- 当真正的新工作出现时开始一个新的分支
- 有时合并部分功能并重新开始阶段 2

## 有什么不同？

| |旧版 (`/openspec:proposal`)| OPSX (`/opsx:*`) |
|---|---|---|
|**结构**|一份大提案文件|具有依赖性的离散工件|
|**工作流程**|线性阶段：计划→实施→归档|流畅的行动——随时做任何事|
|**迭代**|不好意思回去|在学习时更新工件|
|**定制**|固定结构|模式驱动（定义您自己的工件）|

**关键见解：**工作不是线性的。 OPSX 别再假装了。

## 架构深度探究

本节介绍 OPSX 的工作原理以及它与旧工作流程的比较。
本节中的示例使用扩展命令集（`new`、`continue` 等）；默认`core`用户可以将相同的流映射到`propose → apply → sync → archive`。

### 理念：阶段与行动

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         LEGACY WORKFLOW                                      │
│                    (Phase-Locked, All-or-Nothing)                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌──────────────┐      ┌──────────────┐      ┌──────────────┐             │
│   │   PLANNING   │ ───► │ IMPLEMENTING │ ───► │   ARCHIVING  │             │
│   │    PHASE     │      │    PHASE     │      │    PHASE     │             │
│   └──────────────┘      └──────────────┘      └──────────────┘             │
│         │                     │                     │                       │
│         ▼                     ▼                     ▼                       │
│   /openspec:proposal   /openspec:apply      /openspec:archive              │
│                                                                             │
│   • Creates ALL artifacts at once                                          │
│   • Can't go back to update specs during implementation                    │
│   • Phase gates enforce linear progression                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                            OPSX WORKFLOW                                     │
│                      (Fluid Actions, Iterative)                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│              ┌────────────────────────────────────────────┐                 │
│              │           ACTIONS (not phases)             │                 │
│              │                                            │                 │
│              │   new ◄──► continue ◄──► apply ◄──► archive │                 │
│              │    │          │           │           │    │                 │
│              │    └──────────┴───────────┴───────────┘    │                 │
│              │              any order                     │                 │
│              └────────────────────────────────────────────┘                 │
│                                                                             │
│   • Create artifacts one at a time OR fast-forward                         │
│   • Update specs/design/tasks during implementation                        │
│   • Dependencies enable progress, phases don't exist                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 组件架构

**旧工作流程**使用TypeScript中的硬编码模板：

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      LEGACY WORKFLOW COMPONENTS                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Hardcoded Templates (TypeScript strings)                                  │
│                    │                                                        │
│                    ▼                                                        │
│   Tool-specific configurators/adapters                                      │
│                    │                                                        │
│                    ▼                                                        │
│   Generated Command Files (.claude/commands/openspec/*.md)                  │
│                                                                             │
│   • Fixed structure, no artifact awareness                                  │
│   • Change requires code modification + rebuild                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**OPSX** 使用外部模式和依赖图引擎：

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         OPSX COMPONENTS                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Schema Definitions (YAML)                                                 │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  name: spec-driven                                                  │   │
│   │  artifacts:                                                         │   │
│   │    - id: proposal                                                   │   │
│   │      generates: proposal.md                                         │   │
│   │      requires: []              ◄── Dependencies                     │   │
│   │    - id: specs                                                      │   │
│   │      generates: specs/**/*.md  ◄── Glob patterns                    │   │
│   │      requires: [proposal]      ◄── Enables after proposal           │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                    │                                                        │
│                    ▼                                                        │
│   Artifact Graph Engine                                                     │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  • Topological sort (dependency ordering)                           │   │
│   │  • State detection (filesystem existence)                           │   │
│   │  • Rich instruction generation (templates + context)                │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                    │                                                        │
│                    ▼                                                        │
│   Skill Files (.claude/skills/openspec-*/SKILL.md)                          │
│                                                                             │
│   • Cross-editor compatible (Claude Code, Cursor, Devin)                    │
│   • Skills query CLI for structured data                                    │
│   • Fully customizable via schema files                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 依赖图模型

工件形成有向无环图 (DAG)。依赖项是 ** 启用器 **，而不是门：

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
                                  │
                                  ▼
                          ┌──────────────┐
                          │ APPLY PHASE  │
                          │ (requires:   │
                          │  tasks)      │
                          └──────────────┘
```

**状态转换：**

```
   BLOCKED ────────────────► READY ────────────────► DONE
      │                        │                       │
   Missing                  All deps               File exists
   dependencies             are DONE               on filesystem
```

### 信息流

**旧工作流程** — agent 接收静态指令：

```
  User: "/openspec:proposal"
           │
           ▼
  ┌─────────────────────────────────────────┐
  │  Static instructions:                   │
  │  • Create proposal.md                   │
  │  • Create tasks.md                      │
  │  • Create design.md                     │
  │  • Create delta spec files              │
  │                                         │
  │  No awareness of what exists or         │
  │  dependencies between artifacts         │
  └─────────────────────────────────────────┘
           │
           ▼
  Agent creates ALL artifacts in one go
```

**OPSX** — agent 查询丰富的上下文：

```
  User: "/opsx:continue"
           │
           ▼
  ┌──────────────────────────────────────────────────────────────────────────┐
  │  Step 1: Query current state                                             │
  │  ┌────────────────────────────────────────────────────────────────────┐  │
  │  │  $ openspec status --change "add-auth" --json                      │  │
  │  │                                                                    │  │
  │  │  {                                                                 │  │
  │  │    "artifacts": [                                                  │  │
  │  │      {"id": "proposal", "status": "done"},                         │  │
  │  │      {"id": "specs", "status": "ready"},      ◄── First ready      │  │
  │  │      {"id": "design", "status": "ready"},                          │  │
  │  │      {"id": "tasks", "status": "blocked",                          │  │
  │  │       "missingDeps": ["specs", "design"]}                          │  │
  │  │    ]                                                               │  │
  │  │  }                                                                 │  │
  │  └────────────────────────────────────────────────────────────────────┘  │
  │                                                                          │
  │  Step 2: Get rich instructions for ready artifact                        │
  │  ┌────────────────────────────────────────────────────────────────────┐  │
  │  │  $ openspec instructions specs --change "add-auth" --json          │  │
  │  │                                                                    │  │
  │  │  {                                                                 │  │
  │  │    "template": "# Specification\n\n## ADDED Requirements...",      │  │
  │  │    "dependencies": [{"id": "proposal", "path": "...", "done": true}│  │
  │  │    "unlocks": ["tasks"]                                            │  │
  │  │  }                                                                 │  │
  │  └────────────────────────────────────────────────────────────────────┘  │
  │                                                                          │
  │  Step 3: Read dependencies → Create ONE artifact → Show what's unlocked  │
  └──────────────────────────────────────────────────────────────────────────┘
```

### 迭代模型

**旧工作流程** - 难以迭代：

```
  ┌─────────┐     ┌─────────┐     ┌─────────┐
  │/proposal│ ──► │ /apply  │ ──► │/archive │
  └─────────┘     └─────────┘     └─────────┘
       │               │
       │               ├── "Wait, the design is wrong"
       │               │
       │               ├── Options:
       │               │   • Edit files manually (breaks context)
       │               │   • Abandon and start over
       │               │   • Push through and fix later
       │               │
       │               └── No official "go back" mechanism
       │
       └── Creates ALL artifacts at once
```

**OPSX** — 自然迭代：

```
  /opsx:new ───► /opsx:continue ───► /opsx:apply ───► /opsx:archive
      │                │                  │
      │                │                  ├── "The design is wrong"
      │                │                  │
      │                │                  ▼
      │                │            Just edit design.md
      │                │            and continue!
      │                │                  │
      │                │                  ▼
      │                │         /opsx:apply picks up
      │                │         where you left off
      │                │
      │                └── Creates ONE artifact, shows what's unlocked
      │
      └── Scaffolds change, waits for direction
```

### 自定义模式

使用架构管理命令创建自定义工作流程：

```bash
# Create a new schema from scratch (interactive)
openspec schema init my-workflow

# Or fork an existing schema as a starting point
openspec schema fork spec-driven my-workflow

# Validate your schema structure
openspec schema validate my-workflow

# See where a schema resolves from (useful for debugging)
openspec schema which my-workflow
```

模式存储在 `openspec/schemas/`（项目本地，版本控制）或 `~/.local/share/openspec/schemas/`（用户全局）中。

**架构结构：**
```
openspec/schemas/research-first/
├── schema.yaml
└── templates/
    ├── research.md
    ├── proposal.md
    └── tasks.md
```

**示例schema.yaml:**
```yaml
name: research-first
artifacts:
  - id: research        # Added before proposal
    generates: research.md
    requires: []

  - id: proposal
    generates: proposal.md
    requires: [research]  # Now depends on research

  - id: tasks
    generates: tasks.md
    requires: [proposal]
```

**依赖关系图：**
```
   research ──► proposal ──► tasks
```

### 总结

|方面|遗产| OPSX |
|--------|----------|------|
|**模板**|硬编码 TypeScript|外部 YAML + Markdown|
|**依赖项**|无（一次全部）|DAG 拓扑排序|
|**状态**|基于阶段的心理模型|文件系统存在|
|**定制**|编辑源码，重建|创建schema.yaml|
|**迭代**|锁相|流畅，编辑任何内容|
|**编辑器支持**|工具专用configurator/adapters|单一技能目录|

## 模式

模式定义存在哪些工件及其依赖关系。目前可用：

- **规范驱动**（默认）：提案→规范→设计→任务

```bash
# List available schemas
openspec schemas

# See all schemas with their resolution sources
openspec schema which --all

# Create a new schema interactively
openspec schema init my-workflow

# Fork an existing schema for customization
openspec schema fork spec-driven my-workflow

# Validate schema structure before use
openspec schema validate my-workflow
```

## 温馨提示

- 在做出改变之前使用 `/opsx:explore` 仔细思考一个想法
- `/opsx:ff` 当你知道自己想要什么时，`/opsx:continue` 当你探索时
- 在 `/opsx:apply` 期间，如果出现问题 — 修复工件，然后继续
- 任务通过 `tasks.md` 中的复选框跟踪进度
- 随时查看状态：`openspec status --change "name"`

## 反馈意见

这很粗糙。这是有意为之的——我们正在学习什么是有效的。

发现错误？有想法吗？加入我们的 [Discord](<https://discord.gg/YctCnvvshC>) 或在 [GitHub](<https://github.com/Fission-AI/openspec/issues>) 上提出问题。


## 原文对照

<details>
<summary>展开完整原文</summary>

# OPSX Workflow

> Feedback welcome on [Discord](<https://discord.gg/YctCnvvshC>).

## What Is It?

OPSX is now the standard workflow for OpenSpec.

It's a **fluid, iterative workflow** for OpenSpec changes. No more rigid phases — just actions you can take anytime.

## Why This Exists

The legacy OpenSpec workflow works, but it's **locked down**:

- **Instructions are hardcoded** — buried in TypeScript, you can't change them
- **All-or-nothing** — one big command creates everything, can't test individual pieces
- **Fixed structure** — same workflow for everyone, no customization
- **Black box** — when AI output is bad, you can't tweak the prompts

**OPSX opens it up.** Now anyone can:

1. **Experiment with instructions** — edit a template, see if the AI does better
2. **Test granularly** — validate each artifact's instructions independently
3. **Customize workflows** — define your own artifacts and dependencies
4. **Iterate quickly** — change a template, test immediately, no rebuild

```
Legacy workflow:                      OPSX:
┌────────────────────────┐           ┌────────────────────────┐
│  Hardcoded in package  │           │  schema.yaml           │◄── You edit this
│  (can't change)        │           │  templates/*.md        │◄── Or this
│        ↓               │           │        ↓               │
│  Wait for new release  │           │  Instant effect        │
│        ↓               │           │        ↓               │
│  Hope it's better      │           │  Test it yourself      │
└────────────────────────┘           └────────────────────────┘
```

**This is for everyone:**
- **Teams** — create workflows that match how you actually work
- **Power users** — tweak prompts to get better AI outputs for your codebase
- **OpenSpec contributors** — experiment with new approaches without releases

We're all still learning what works best. OPSX lets us learn together.

## The User Experience

**The problem with linear workflows:**
You're "in planning phase", then "in implementation phase", then "done". But real work doesn't work that way. You implement something, realize your design was wrong, need to update specs, continue implementing. Linear phases fight against how work actually happens.

**OPSX approach:**
- **Actions, not phases** — create, implement, update, archive — do any of them anytime
- **Dependencies are enablers** — they show what's possible, not what's required next

```
  proposal ──→ specs ──→ design ──→ tasks ──→ implement
```

## Setup

```bash
# Make sure you have openspec installed — skills are automatically generated
openspec init
```

This creates skills in `.claude/skills/` (or equivalent) that AI coding assistants auto-detect.

By default, OpenSpec uses the `core` workflow profile (`propose`, `explore`, `apply`, `update`, `sync`, `archive`). If you want the expanded workflow commands (`new`, `continue`, `ff`, `verify`, `bulk-archive`, `onboard`), configure them with `openspec config profile` and apply with `openspec update`.

During setup, you'll be prompted to create a **project config** (`openspec/config.yaml`). This is optional but recommended.

## Project Configuration

Project config lets you set defaults and inject project-specific context into all artifacts.

### Creating Config

Config is created during `openspec init`, or manually:

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  API conventions: RESTful, JSON responses
  Testing: Vitest for unit tests, Playwright for e2e
  Style: ESLint with Prettier, strict TypeScript

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format for scenarios
  design:
    - Include sequence diagrams for complex flows
```

### Config Fields

| Field | Type | Description |
|-------|------|-------------|
| `schema` | string | Default schema for new changes (e.g., `spec-driven`) |
| `context` | string | Project context injected into all artifact instructions |
| `rules` | object | Per-artifact rules, keyed by artifact ID |

### How It Works

**Schema precedence** (highest to lowest):
1. CLI flag (`--schema <name>`)
2. Change metadata (`.openspec.yaml` in change directory)
3. Project config (`openspec/config.yaml`)
4. Default (`spec-driven`)

**Context injection:**
- Context is prepended to every artifact's instructions
- Wrapped in `<context>...</context>` tags
- Helps AI understand your project's conventions

**Rules injection:**
- Rules are only injected for matching artifacts
- Wrapped in `<rules>...</rules>` tags
- Appear after context, before the template

### Artifact IDs by Schema

**spec-driven** (default):
- `proposal` — Change proposal
- `specs` — Specifications
- `design` — Technical design
- `tasks` — Implementation tasks

### Config Validation

- Unknown artifact IDs in `rules` generate warnings
- Schema names are validated against available schemas
- Context has a 50KB size limit
- Invalid YAML is reported with line numbers

### Troubleshooting

**"Unknown artifact ID in rules: X"**
- Check artifact IDs match your schema (see list above)
- Run `openspec schemas --json` to see artifact IDs for each schema

**Config not being applied:**
- Ensure file is at `openspec/config.yaml` (not `.yml`)
- Check YAML syntax with a validator
- Config changes take effect immediately (no restart needed)

**Context too large:**
- Context is limited to 50KB
- Summarize or link to external docs instead

## Commands

| Command | What it does |
|---------|--------------|
| `/opsx:propose` | Create a change and generate planning artifacts in one step (default quick path) |
| `/opsx:explore` | Think through ideas, investigate problems, clarify requirements |
| `/opsx:new` | Start a new change scaffold (expanded workflow) |
| `/opsx:continue` | Create the next artifact (expanded workflow) |
| `/opsx:ff` | Fast-forward planning artifacts (expanded workflow) |
| `/opsx:apply` | Implement tasks, updating artifacts as needed |
| `/opsx:update` | Revise a change's planning artifacts and keep them coherent |
| `/opsx:verify` | Validate implementation against artifacts (expanded workflow) |
| `/opsx:sync` | Merge delta specs into main specs (optional) |
| `/opsx:archive` | Archive when done |
| `/opsx:bulk-archive` | Archive multiple completed changes (expanded workflow) |
| `/opsx:onboard` | Guided walkthrough of an end-to-end change (expanded workflow) |

## Usage

### Explore an idea
```
/opsx:explore
```
Think through ideas, investigate problems, compare options. No structure required - just a thinking partner. When insights crystallize, transition to `/opsx:propose` (default) or `/opsx:new`/`/opsx:ff` (expanded).

### Start a new change
```
/opsx:propose
```
Creates the change and generates planning artifacts needed before implementation.

If you've enabled expanded workflows, you can instead use:

```text
/opsx:new        # scaffold only
/opsx:continue   # create one artifact at a time
/opsx:ff         # create all planning artifacts at once
```

### Create artifacts
```
/opsx:continue
```
Shows what's ready to create based on dependencies, then creates one artifact. Use repeatedly to build up your change incrementally.

```
/opsx:ff add-dark-mode
```
Creates all planning artifacts at once. Use when you have a clear picture of what you're building.

### Implement (the fluid part)
```
/opsx:apply
```
Works through tasks, checking them off as you go. If you're juggling multiple changes, you can run `/opsx:apply <name>`; otherwise it should infer from the conversation and prompt you to choose if it can't tell.

### Updating a change
```
/opsx:update add-dark-mode - we're storing the theme in a cookie now
```
Revises the change's existing planning artifacts and keeps them coherent in any direction (a design edit may ripple back to the proposal). It never edits code. Every edit is confirmed with you first. See [the update reference](<OpenSpec AI 聊天命令参考.md#opsxupdate>) for how it handles missing files without starting a new artifact.

If the change was already implemented, it recommends `/opsx:apply` so the code catches up with the revised plan. If your revision changes the change's *intent*, start fresh instead. See [When to Update vs. Start Fresh](<#when-to-update-vs-start-fresh>).

### Sync delta specs
```text
/opsx:sync
```
Merges the current change's delta specs into your main `openspec/specs/` without archiving — the change stays active. It applies the whole delta: a requirement under `## REMOVED` is deleted from the main spec and a renamed one is retitled in place, while content the delta doesn't mention is left untouched. Syncing is optional — archive prompts you to sync first if you haven't. Reach for it when you want main specs updated before archiving, when a parallel change needs to build on specs this one just added, or when you want to review the merged main spec before archiving.

### Finish up
```
/opsx:archive   # Move to archive when done (prompts to sync specs if needed)
```

## When to Update vs. Start Fresh

You can always edit your proposal or specs before implementation. But when does refining become "this is different work"?

### What a Proposal Captures

A proposal defines three things:
1. **Intent** — What problem are you solving?
2. **Scope** — What's in/out of bounds?
3. **Approach** — How will you solve it?

The question is: which changed, and by how much?

### Update the Existing Change When:

**Same intent, refined execution**
- You discover edge cases you didn't consider
- The approach needs tweaking but the goal is unchanged
- Implementation reveals the design was slightly off

**Scope narrows**
- You realize full scope is too big, want to ship MVP first
- "Add dark mode" → "Add dark mode toggle (system preference in v2)"

**Learning-driven corrections**
- Codebase isn't structured how you thought
- A dependency doesn't work as expected
- "Use CSS variables" → "Use Tailwind's dark: prefix instead"

### Start a New Change When:

**Intent fundamentally changed**
- The problem itself is different now
- "Add dark mode" → "Add comprehensive theme system with custom colors, fonts, spacing"

**Scope exploded**
- Change grew so much it's essentially different work
- Original proposal would be unrecognizable after updates
- "Fix login bug" → "Rewrite auth system"

**Original is completable**
- The original change can be marked "done"
- New work stands alone, not a refinement
- Complete "Add dark mode MVP" → Archive → New change "Enhance dark mode"

### The Heuristics

```
                        ┌─────────────────────────────────────┐
                        │     Is this the same work?          │
                        └──────────────┬──────────────────────┘
                                       │
                    ┌──────────────────┼──────────────────┐
                    │                  │                  │
                    ▼                  ▼                  ▼
             Same intent?      >50% overlap?      Can original
             Same problem?     Same scope?        be "done" without
                    │                  │          these changes?
                    │                  │                  │
          ┌────────┴────────┐  ┌──────┴──────┐   ┌───────┴───────┐
          │                 │  │             │   │               │
         YES               NO YES           NO  NO              YES
          │                 │  │             │   │               │
          ▼                 ▼  ▼             ▼   ▼               ▼
       UPDATE            NEW  UPDATE       NEW  UPDATE          NEW
```

| Test | Update | New Change |
|------|--------|------------|
| **Identity** | "Same thing, refined" | "Different work" |
| **Scope overlap** | >50% overlaps | <50% overlaps |
| **Completion** | Can't be "done" without changes | Can finish original, new work stands alone |
| **Story** | Update chain tells coherent story | Patches would confuse more than clarify |

### The Principle

> **Update preserves context. New change provides clarity.**
>
> Choose update when the history of your thinking is valuable.
> Choose new when starting fresh would be clearer than patching.

Think of it like git branches:
- Keep committing while working on the same feature
- Start a new branch when it's genuinely new work
- Sometimes merge a partial feature and start fresh for phase 2

## What's Different?

| | Legacy (`/openspec:proposal`) | OPSX (`/opsx:*`) |
|---|---|---|
| **Structure** | One big proposal document | Discrete artifacts with dependencies |
| **Workflow** | Linear phases: plan → implement → archive | Fluid actions — do anything anytime |
| **Iteration** | Awkward to go back | Update artifacts as you learn |
| **Customization** | Fixed structure | Schema-driven (define your own artifacts) |

**The key insight:** work isn't linear. OPSX stops pretending it is.

## Architecture Deep Dive

This section explains how OPSX works under the hood and how it compares to the legacy workflow.
Examples in this section use the expanded command set (`new`, `continue`, etc.); default `core` users can map the same flow to `propose → apply → sync → archive`.

### Philosophy: Phases vs Actions

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         LEGACY WORKFLOW                                      │
│                    (Phase-Locked, All-or-Nothing)                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌──────────────┐      ┌──────────────┐      ┌──────────────┐             │
│   │   PLANNING   │ ───► │ IMPLEMENTING │ ───► │   ARCHIVING  │             │
│   │    PHASE     │      │    PHASE     │      │    PHASE     │             │
│   └──────────────┘      └──────────────┘      └──────────────┘             │
│         │                     │                     │                       │
│         ▼                     ▼                     ▼                       │
│   /openspec:proposal   /openspec:apply      /openspec:archive              │
│                                                                             │
│   • Creates ALL artifacts at once                                          │
│   • Can't go back to update specs during implementation                    │
│   • Phase gates enforce linear progression                                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                            OPSX WORKFLOW                                     │
│                      (Fluid Actions, Iterative)                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│              ┌────────────────────────────────────────────┐                 │
│              │           ACTIONS (not phases)             │                 │
│              │                                            │                 │
│              │   new ◄──► continue ◄──► apply ◄──► archive │                 │
│              │    │          │           │           │    │                 │
│              │    └──────────┴───────────┴───────────┘    │                 │
│              │              any order                     │                 │
│              └────────────────────────────────────────────┘                 │
│                                                                             │
│   • Create artifacts one at a time OR fast-forward                         │
│   • Update specs/design/tasks during implementation                        │
│   • Dependencies enable progress, phases don't exist                       │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Component Architecture

**Legacy workflow** uses hardcoded templates in TypeScript:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      LEGACY WORKFLOW COMPONENTS                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Hardcoded Templates (TypeScript strings)                                  │
│                    │                                                        │
│                    ▼                                                        │
│   Tool-specific configurators/adapters                                      │
│                    │                                                        │
│                    ▼                                                        │
│   Generated Command Files (.claude/commands/openspec/*.md)                  │
│                                                                             │
│   • Fixed structure, no artifact awareness                                  │
│   • Change requires code modification + rebuild                             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

**OPSX** uses external schemas and a dependency graph engine:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         OPSX COMPONENTS                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   Schema Definitions (YAML)                                                 │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  name: spec-driven                                                  │   │
│   │  artifacts:                                                         │   │
│   │    - id: proposal                                                   │   │
│   │      generates: proposal.md                                         │   │
│   │      requires: []              ◄── Dependencies                     │   │
│   │    - id: specs                                                      │   │
│   │      generates: specs/**/*.md  ◄── Glob patterns                    │   │
│   │      requires: [proposal]      ◄── Enables after proposal           │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                    │                                                        │
│                    ▼                                                        │
│   Artifact Graph Engine                                                     │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │  • Topological sort (dependency ordering)                           │   │
│   │  • State detection (filesystem existence)                           │   │
│   │  • Rich instruction generation (templates + context)                │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                    │                                                        │
│                    ▼                                                        │
│   Skill Files (.claude/skills/openspec-*/SKILL.md)                          │
│                                                                             │
│   • Cross-editor compatible (Claude Code, Cursor, Devin)                    │
│   • Skills query CLI for structured data                                    │
│   • Fully customizable via schema files                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Dependency Graph Model

Artifacts form a directed acyclic graph (DAG). Dependencies are **enablers**, not gates:

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
                                  │
                                  ▼
                          ┌──────────────┐
                          │ APPLY PHASE  │
                          │ (requires:   │
                          │  tasks)      │
                          └──────────────┘
```

**State transitions:**

```
   BLOCKED ────────────────► READY ────────────────► DONE
      │                        │                       │
   Missing                  All deps               File exists
   dependencies             are DONE               on filesystem
```

### Information Flow

**Legacy workflow** — agent receives static instructions:

```
  User: "/openspec:proposal"
           │
           ▼
  ┌─────────────────────────────────────────┐
  │  Static instructions:                   │
  │  • Create proposal.md                   │
  │  • Create tasks.md                      │
  │  • Create design.md                     │
  │  • Create delta spec files              │
  │                                         │
  │  No awareness of what exists or         │
  │  dependencies between artifacts         │
  └─────────────────────────────────────────┘
           │
           ▼
  Agent creates ALL artifacts in one go
```

**OPSX** — agent queries for rich context:

```
  User: "/opsx:continue"
           │
           ▼
  ┌──────────────────────────────────────────────────────────────────────────┐
  │  Step 1: Query current state                                             │
  │  ┌────────────────────────────────────────────────────────────────────┐  │
  │  │  $ openspec status --change "add-auth" --json                      │  │
  │  │                                                                    │  │
  │  │  {                                                                 │  │
  │  │    "artifacts": [                                                  │  │
  │  │      {"id": "proposal", "status": "done"},                         │  │
  │  │      {"id": "specs", "status": "ready"},      ◄── First ready      │  │
  │  │      {"id": "design", "status": "ready"},                          │  │
  │  │      {"id": "tasks", "status": "blocked",                          │  │
  │  │       "missingDeps": ["specs", "design"]}                          │  │
  │  │    ]                                                               │  │
  │  │  }                                                                 │  │
  │  └────────────────────────────────────────────────────────────────────┘  │
  │                                                                          │
  │  Step 2: Get rich instructions for ready artifact                        │
  │  ┌────────────────────────────────────────────────────────────────────┐  │
  │  │  $ openspec instructions specs --change "add-auth" --json          │  │
  │  │                                                                    │  │
  │  │  {                                                                 │  │
  │  │    "template": "# Specification\n\n## ADDED Requirements...",      │  │
  │  │    "dependencies": [{"id": "proposal", "path": "...", "done": true}│  │
  │  │    "unlocks": ["tasks"]                                            │  │
  │  │  }                                                                 │  │
  │  └────────────────────────────────────────────────────────────────────┘  │
  │                                                                          │
  │  Step 3: Read dependencies → Create ONE artifact → Show what's unlocked  │
  └──────────────────────────────────────────────────────────────────────────┘
```

### Iteration Model

**Legacy workflow** — awkward to iterate:

```
  ┌─────────┐     ┌─────────┐     ┌─────────┐
  │/proposal│ ──► │ /apply  │ ──► │/archive │
  └─────────┘     └─────────┘     └─────────┘
       │               │
       │               ├── "Wait, the design is wrong"
       │               │
       │               ├── Options:
       │               │   • Edit files manually (breaks context)
       │               │   • Abandon and start over
       │               │   • Push through and fix later
       │               │
       │               └── No official "go back" mechanism
       │
       └── Creates ALL artifacts at once
```

**OPSX** — natural iteration:

```
  /opsx:new ───► /opsx:continue ───► /opsx:apply ───► /opsx:archive
      │                │                  │
      │                │                  ├── "The design is wrong"
      │                │                  │
      │                │                  ▼
      │                │            Just edit design.md
      │                │            and continue!
      │                │                  │
      │                │                  ▼
      │                │         /opsx:apply picks up
      │                │         where you left off
      │                │
      │                └── Creates ONE artifact, shows what's unlocked
      │
      └── Scaffolds change, waits for direction
```

### Custom Schemas

Create custom workflows using the schema management commands:

```bash
# Create a new schema from scratch (interactive)
openspec schema init my-workflow

# Or fork an existing schema as a starting point
openspec schema fork spec-driven my-workflow

# Validate your schema structure
openspec schema validate my-workflow

# See where a schema resolves from (useful for debugging)
openspec schema which my-workflow
```

Schemas are stored in `openspec/schemas/` (project-local, version controlled) or `~/.local/share/openspec/schemas/` (user global).

**Schema structure:**
```
openspec/schemas/research-first/
├── schema.yaml
└── templates/
    ├── research.md
    ├── proposal.md
    └── tasks.md
```

**Example schema.yaml:**
```yaml
name: research-first
artifacts:
  - id: research        # Added before proposal
    generates: research.md
    requires: []

  - id: proposal
    generates: proposal.md
    requires: [research]  # Now depends on research

  - id: tasks
    generates: tasks.md
    requires: [proposal]
```

**Dependency Graph:**
```
   research ──► proposal ──► tasks
```

### Summary

| Aspect | Legacy | OPSX |
|--------|----------|------|
| **Templates** | Hardcoded TypeScript | External YAML + Markdown |
| **Dependencies** | None (all at once) | DAG with topological sort |
| **State** | Phase-based mental model | Filesystem existence |
| **Customization** | Edit source, rebuild | Create schema.yaml |
| **Iteration** | Phase-locked | Fluid, edit anything |
| **Editor Support** | Tool-specific configurator/adapters | Single skills directory |

## Schemas

Schemas define what artifacts exist and their dependencies. Currently available:

- **spec-driven** (default): proposal → specs → design → tasks

```bash
# List available schemas
openspec schemas

# See all schemas with their resolution sources
openspec schema which --all

# Create a new schema interactively
openspec schema init my-workflow

# Fork an existing schema for customization
openspec schema fork spec-driven my-workflow

# Validate schema structure before use
openspec schema validate my-workflow
```

## Tips

- Use `/opsx:explore` to think through an idea before committing to a change
- `/opsx:ff` when you know what you want, `/opsx:continue` when exploring
- During `/opsx:apply`, if something's wrong — fix the artifact, then continue
- Tasks track progress via checkboxes in `tasks.md`
- Check status anytime: `openspec status --change "name"`

## Feedback

This is rough. That's intentional — we're learning what works.

Found a bug? Have ideas? Join us on [Discord](<https://discord.gg/YctCnvvshC>) or open an issue on [GitHub](<https://github.com/Fission-AI/openspec/issues>).


</details>
