# OpenSpec AI 聊天命令参考

说明 core 与扩展工作流中的斜杠命令、每个命令的作用和调用方式，并提示不同 AI 编程工具的命令命名差异。

- 来源：OpenSpec
- 资料类型：AI 助手命令参考
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/commands.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/commands.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 命令

这是OpenSpec斜杠命令的参考。这些命令在您的 AI 编码助手的聊天界面（例如，Claude Code、Cursor、Devin Desktop）中调用。

有关工作流程模式以及何时使用每个命令，请参阅 [ 工作流程 ](<OpenSpec 快速与扩展工作流.md>)。对于CLI命令，请参见[CLI](<OpenSpec 命令行参考.md>)。

这些页面使用 `/opsx:<command>` 作为规范名称。有些工具的拼写 
 不同 — Cursor 和 GitHub Copilot 注册 `/opsx-propose`，Codex 使用 
`$openspec-propose` — 因此请检查 [如何调用 ](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)
工具。 OpenSpec 生成的文件已使用正确的形式。

## 快速参考

### 默认快速路径（`core` 配置文件）

|命令|目的|
|---------|---------|
| `/opsx:propose` |一步创建变更并生成规划工件|
| `/opsx:explore` |在做出改变之前仔细考虑想法|
| `/opsx:apply` |实施变革中的任务|
| `/opsx:update` |修改变更的计划工件并保持它们的连贯性|
| `/opsx:sync` |将增量规格合并到主要规格中|
| `/opsx:archive` |归档已完成的更改|

### 扩展工作流程命令（自定义工作流程选择）

|命令|目的|
|---------|---------|
| `/opsx:new` |开始新的变革脚手架|
| `/opsx:continue` |根据依赖关系创建下一个工件|
| `/opsx:ff` |快进：立即创建所有计划工件|
| `/opsx:verify` |验证实现是否匹配工件|
| `/opsx:bulk-archive` |一次存档多个更改|
| `/opsx:onboard` |完整工作流程的指导教程|

默认全局配置文件是 `core`。要启用扩展工作流命令，请运行 `openspec config profile`，选择工作流，然后在项目中运行 `openspec update`。

---

## 命令参考

### `/opsx:propose`

一步创建新变更并生成规划工件。这是 `core` 配置文件中的默认启动命令。

**语法：**
```text
/opsx:propose [change-name-or-description]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name-or-description` |否|烤肉串名称或简单语言更改描述|

**它的作用：**
- 创建 `openspec/changes/<change-name>/`
- 生成实施前所需的工件（对于 `spec-driven`：提案、规格、设计、任务）
- 当更改准备好 `/opsx:apply` 时停止

**示例：**
```text
You: /opsx:propose add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md
     ✓ specs/ui/spec.md
     ✓ design.md
     ✓ tasks.md
     Ready for implementation. Run /opsx:apply.
```

**提示：**
- 使用它来实现最快的端到端路径
- 如果您想要逐步控制工件，请启用扩展工作流程并使用 `/opsx:new` + `/opsx:continue`

---

### `/opsx:explore`

> **当你不确定时从这里开始。** Explore 是一个无风险的思考伙伴：它会读取你的代码库，比较选项，并在编写任何代码之前将模糊的想法转化为具体的计划。它以默认配置文件形式提供。有关完整案例和更多示例，请参阅 [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) 指南。

在做出改变之前，仔细思考想法、调查问题并澄清要求。

**语法：**
```
/opsx:explore [topic]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `topic` |否|您想要探索或调查的内容|

**它的作用：**
- 开启探索性对话，无需任何结构
- 研究代码库以回答问题
- 比较选项和方法
- 创建可视化图表以阐明思维
- 当您询问或接受其提议时捕获探索：使用 `openspec new change` 构建变更并写入您命名的计划工件，或更新现有变更的工件
- 当见解具体化时，可以过渡到 `/opsx:propose`（默认）或 `/opsx:new`（扩展工作流程）

**示例：**
```text
You: /opsx:explore

AI:  What would you like to explore?

You: How should we handle authentication for the mobile app?

AI:  Let me investigate your current auth setup...
     [Analyzes codebase]

     I see you're using session-based auth for the web app.
     For mobile, we have three main options:

     1. JWT tokens (stateless, works offline)
     2. OAuth2 (if you need third-party login)
     3. Extend current sessions (simpler, but requires connectivity)

     Your API already has CORS configured. Which direction interests you?

You: Let's go with JWT.

AI:  That's a decision worth keeping. Want me to start a change called
     add-jwt-auth? Just the change folder, nothing else yet.

You: Yes.

AI:  Started openspec/changes/add-jwt-auth/. Say the word and I'll
     write the proposal, specs, and tasks from what we just worked out.
```

**提示：**
- 当需求不明确或需要调查时使用
- 它从不编写代码，也不会编写任何其他内容，除非您询问，或者当它提供时说“是”
- 适合在做出决定之前比较多种方法
- 可以读取文件并搜索代码库

---

### `/opsx:new`

开始一个新的变革脚手架。创建更改文件夹并等待您生成带有 `/opsx:continue` 或 `/opsx:ff` 的工件。

此命令是扩展工作流程集的一部分（不包含在默认 `core` 配置文件中）。

**语法：**
```
/opsx:new [change-name] [--schema <schema-name>]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|更改文件夹的名称（如果未提供，则会出现提示）|
| `--schema` |否|要使用的工作流程架构（默认：来自配置或 `spec-driven`）|

**它的作用：**
- 创建`openspec/changes/<change-name>/`目录
- 在更改文件夹中创建 `.openspec.yaml` 元数据文件
- 显示第一个准备创建的工件模板
- 如果未提供，则提示更改名称和架构

**它创造了什么：**
```
openspec/changes/<change-name>/
└── .openspec.yaml    # Change metadata (schema, created date)
```

**示例：**
```
You: /opsx:new add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     Schema: spec-driven

     Ready to create: proposal
     Use /opsx:continue to create it, or /opsx:ff to create all artifacts.
```

**提示：**
- 使用描述性名称：`add-feature`、`fix-bug`、`refactor-module`
- 避免使用通用名称，如 `update`、`changes`、`wip`
- 架构也可以在项目配置中设置（`openspec/config.yaml`）

---

### `/opsx:continue`

创建依赖链中的下一个工件。一次创建一个工件以实现增量进展。

**语法：**
```
/opsx:continue [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|继续进行哪些更改（如果未提供，则从上下文推断）|

**它的作用：**
- 查询工件依赖图
- 显示哪些工件已准备就绪，哪些工件被阻止
- 创建第一个准备好的工件
- 读取上下文的依赖文件
- 显示创建后可用的内容

**示例：**
```
You: /opsx:continue

AI:  Change: add-dark-mode

     Artifact status:
     ✓ proposal    (done)
     ◆ specs       (ready)
     ◆ design      (ready)
     ○ tasks       (blocked - needs: specs, design)

     Creating specs...

     [Reads proposal.md, creates specs based on requirements]

     ✓ Created openspec/changes/add-dark-mode/specs/ui/spec.md

     Now available: design
     Run /opsx:continue to create the next artifact.
```

**提示：**
- 当您想要在继续之前查看每个工件时使用
- 适合需要控制的复杂变更
- 多个工件可能同时准备就绪
- 您可以在继续之前编辑创建的工件

---

### `/opsx:ff`

通过工件创建快进。立即创建所有计划工件。

**语法：**
```
/opsx:ff [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|哪个更改为快进（如果未提供，则从上下文推断）|

**它的作用：**
- 按依赖顺序创建所有工件
- 通过待办事项列表跟踪进度
- 当所有 `apply-required` 工件完成时停止
- 在创建下一个工件之前读取每个依赖项

**示例：**
```
You: /opsx:ff add-dark-mode

AI:  Fast-forwarding add-dark-mode...

     ✓ Creating proposal.md
     ✓ Creating specs/ui/spec.md
     ✓ Creating design.md
     ✓ Creating tasks.md

     All planning artifacts complete!
     Ready for implementation. Run /opsx:apply to begin.
```

**提示：**
- 当您清楚地了解正在构建的内容时使用
- 比 `/opsx:continue` 更快，可以直接进行更改
- 之后您仍然可以编辑工件
- 适合中小型功能

---

### `/opsx:apply`

落实变革中的任务。完成任务列表，编写代码并检查项目。

**语法：**
```
/opsx:apply [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|要实施哪些更改（如果未提供，则从上下文推断）|

**它的作用：**
- 读取 `tasks.md` 并识别未完成的任务
- 一项一项地完成任务
- 根据需要编写代码、创建文件、运行测试
- 使用复选框标记任务已完成 `[x]`

**示例：**
```
You: /opsx:apply

AI:  Implementing add-dark-mode...

     Reading tasks.md:
     - [ ] 1.1 Create ThemeContext
     - [ ] 1.2 Add CSS custom properties
     - [ ] 1.3 Implement localStorage persistence

     Working on 1.1: Create ThemeContext...
     [Creates src/context/ThemeContext.tsx]

     ✓ 1.1 Complete

     Working on 1.2: Add CSS custom properties...
     [Updates src/styles/globals.css]

     ✓ 1.2 Complete
     ...
```

**提示：**
- 如果中断可以从上次中断的地方继续
- 通过指定更改名称用于并行更改
- 在 `tasks.md` 复选框中跟踪完成状态

---

### `/opsx:update`

修改变更的现有计划工件并使它们相互一致。仅规划工件 - 它从不编辑代码。

**语法：**

```text
/opsx:update [change-name]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|要更新哪些更改（如果未提供，则从上下文推断）|

**它的作用：**

- 通过 `openspec status --change <name> --json` 读取更改的工件
- 应用您请求的修订，或者检查工件是否存在矛盾（如果您没有指定）
- 协调任何方向上的其他现有工件（设计编辑可能会影响到提案）
- 在写作之前与您确认每次编辑，一次一件作品
- 最后建议下一步：`/opsx:continue`（未启动的工件）、`/opsx:apply`（将修订后的计划写入代码）或`/opsx:archive`（全部完成）

**丢失文件：**

- 对于具有至少一个现有文件的 glob 工件，例如 `specs/**/*.md`，更新可以建议缺少的伴随文件。它使用架构的说明，并要求您在创建之前确认具体路径。
- 尚无文件的工件仍保留在 `/opsx:continue` 中。故意跳过的工件保持不变。
- 新文件必须保留在更改目录中。如果文件在创建之前出现在确认的路径中，更新将停止而不是覆盖它。

**示例：**

```text
You: /opsx:update add-dark-mode - we're storing the theme in a cookie now, not localStorage

AI:  Reading add-dark-mode artifacts...

     The design references localStorage in two places; tasks 1.3 covers
     localStorage persistence; the proposal doesn't mention storage.

     Proposed revisions:
     1. design.md - swap localStorage decision for cookie storage
     2. tasks.md - reword task 1.3 to cookie persistence

     Apply revision 1? (design.md)
```

**提示：**

- 它不会启动没有现有文件的工件。为此启用 `/opsx:continue`，或者如果未安装该可选工作流程，则使用 `openspec status` 和 `openspec instructions`。
- 如果更改已实施，请跟进 `/opsx:apply`，以便代码与修订后的计划相匹配
- 如果您的修订版本更改了更改的 * 意图 *，请从新更改开始（请参阅 [ 何时更新与重新开始 ](<OpenSpec OPSX 工作流机制.md#何时更新与重新开始>)）

---

### `/opsx:verify`

验证实施是否与您的变更工件相匹配。检查完整性、正确性和连贯性。

**语法：**
```
/opsx:verify [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|要验证哪些更改（如果未提供，则从上下文推断）|

**它的作用：**
- 检查实施质量的三个维度
- 搜索代码库以获取实施证据
- 报告分类为 CRITICAL、WARNING 或 SUGGESTION 的问题
- 不阻止存档，但会出现问题

**验证尺寸：**

|尺寸|它验证了什么|
|-----------|-------------------|
|**完整性**|所有任务已完成，所有要求已实现，已覆盖场景|
|**正确性**|实现符合规范意图，处理边缘情况|
|**相干性**|设计决策体现在代码中，模式一致|

**示例：**
```
You: /opsx:verify

AI:  Verifying add-dark-mode...

     COMPLETENESS
     ✓ All 8 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "System preference detection" has no test coverage

     CORRECTNESS
     ✓ Implementation matches spec intent
     ✓ Edge cases from scenarios are handled
     ✓ Error states match spec definitions

     COHERENCE
     ✓ Design decisions reflected in code structure
     ✓ Naming conventions consistent with design.md
     ⚠ Design mentions "CSS variables" but implementation uses Tailwind classes

     SUMMARY
     ─────────────────────────────
     Critical issues: 0
     Warnings: 2
     Ready to archive: Yes (with warnings)

     Recommendations:
     1. Add test for system preference detection
     2. Update design.md to reflect Tailwind usage, or refactor to use CSS variables
```

**提示：**
- 在归档之前运行以尽早发现不匹配情况
- 警告不会阻止存档，但会指示潜在问题
- 适合在提交之前回顾 AI 的工作
- 可以揭示工件和实现之间的偏差

---

### `/opsx:sync`

**可选命令。** 将变更中的增量规格合并到主要规格中。如果需要，存档将提示同步，因此您通常不需要手动运行此操作。

**语法：**
```
/opsx:sync [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|要同步的更改（如果未提供，则从上下文推断）|

**它的作用：**
- 从变更文件夹中读取增量规格
- 解析 ADDED/MODIFIED/REMOVED/RENAMED 部分
- 将更改合并到主 `openspec/specs/` 目录中
- 保留增量中未提及的现有内容
- 不存档更改（保持活动状态）

**示例：**
```text
You: /opsx:sync

AI:  Syncing add-dark-mode delta specs...

     Reading: openspec/changes/add-dark-mode/specs/ui/spec.md
     Target:  openspec/specs/ui/spec.md

     Changes to apply:
     ✓ ADDED: Theme Selection requirement (2 scenarios)
     ✓ ADDED: Accessibility requirement (1 scenario)

     Merging...
     ✓ openspec/specs/ui/spec.md updated

     Change remains active. Run /opsx:archive when ready to complete.
```

**何时手动使用：**

|场景|使用同步？|
|----------|-----------|
|长期运行的更改，在存档之前需要主要规范|是的|
|多个并行更改需要更新的基本规格|是的|
|想要preview/review单独合并|是的|
|快速更改，直接存档|否（存档处理）|

**提示：**
- 同步是智能的，而不是复制粘贴
- 可以将场景添加到现有需求而无需重复
- 同步后更改保持活动状态（未存档）
- 大多数用户永远不需要直接调用它 - 如果需要的话会出现存档提示

---

### `/opsx:archive`

归档已完成的更改。完成更改并将其移至存档文件夹。

**语法：**
```
/opsx:archive [change-name]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|要存档的更改（如果未提供，则从上下文推断）|

**它的作用：**
- 检查工件完成状态
- 检查任务完成情况（如果未完成则发出警告）
- 如果尚未同步，则提供同步增量规格
- 将更改文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`
- 保留审计跟踪的所有工件

**示例：**
```text
You: /opsx:archive

AI:  Archiving add-dark-mode...

     Artifact status:
     ✓ proposal.md exists
     ✓ specs/ exists
     ✓ design.md exists
     ✓ tasks.md exists (8/8 tasks complete)

     Delta specs: Not yet synced
     → Sync now? (recommended)

You: Yes

AI:  ✓ Synced specs to openspec/specs/ui/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/

     Change archived successfully.
```

**提示：**
- 存档不会阻止未完成的任务，但会发出警告
- Delta 规格可以在存档期间或之前同步
- 存档的更改将保留以供历史记录
- 首先使用`/opsx:verify`来捕获问题

---

### `/opsx:bulk-archive`

一次存档多个已完成的更改。处理变更之间的规范冲突。

**语法：**
```
/opsx:bulk-archive [change-names...]
```

**参数：**
|论证|必填|描述|
|----------|----------|-------------|
| `change-names` |否|对存档的具体更改（如果没有提供则提示选择）|

**它的作用：**
- 列出所有已完成的更改
- 归档前验证每个更改
- 检测变更之间的规范冲突
- 通过检查实际实施的内容来解决冲突
- 按时间顺序归档

**示例：**
```
You: /opsx:bulk-archive

AI:  Found 3 completed changes:
     - add-dark-mode (8/8 tasks complete)
     - fix-login-bug (3/3 tasks complete)
     - update-footer (2/2 tasks complete)

     Checking for spec conflicts...
     ⚠ add-dark-mode and update-footer both touch specs/ui/

     Inspecting codebase to resolve...
     Both changes are implemented. Will merge in chronological order:
     1. add-dark-mode (created Jan 20)
     2. update-footer (created Jan 22)

     Archive all 3 changes?

You: Yes

AI:  ✓ Archived add-dark-mode
     ✓ Archived fix-login-bug
     ✓ Archived update-footer
     Specs merged: add-dark-mode → update-footer
```

**提示：**
- 适合并行工作流
- 冲突解决是代理的（检查代码库）
- 更改按创建顺序存档
- 覆盖规范内容之前提示

---

### `/opsx:onboard`

通过完整的 OpenSpec 工作流程引导新手入门。使用实际代码库的交互式教程。

**语法：**
```
/opsx:onboard
```

**它的作用：**
- 通过旁白走过完整的工作流程周期
- 扫描您的代码库以寻找真正的改进机会
- 用真实的工件创建实际的改变
- 实施实际工作（小的、安全的改变）
- 归档已完成的更改
- 解释每一步发生的情况

**阶段：**
1. 欢迎和代码库分析
2. 寻找改进机会
3. 创建更改 (`/opsx:new`)
4. 撰写提案
5. 创建规格
6. 编写设计
7. 创建任务
8. 执行任务（`/opsx:apply`）
9. 验证实施情况
10. 归档变更
11. 摘要和后续步骤

**示例：**
```
You: /opsx:onboard

AI:  Welcome to OpenSpec!

     I'll walk you through the complete workflow using your actual codebase.
     We'll find something small to improve, create a proper change for it,
     implement it, and archive it.

     Let me scan your codebase for opportunities...

     [Analyzes codebase]

     I found a few things we could work on:
     1. Add input validation to the contact form
     2. Improve error messages in the auth flow
     3. Add loading states to async buttons

     Which interests you? (or suggest something else)
```

**提示：**
- 最适合新用户学习工作流程
- 使用真实代码，而不是玩具示例
- 创建一个真正的改变，你可以保留或放弃
- 需要 15-30 分钟才能完成

---

## AI工具的命令语法

不同的 AI 工具使用略有不同的命令语法。使用与您的工具匹配的格式：

|您的工具的命令文件|语法示例|示例工具|
|--------------------------|----------------|---------------|
| `.../commands/opsx/<id>.*` | `/opsx:propose`, `/opsx:apply` |Claude Code、Gemini CLI、粉碎|
| `.../opsx-<id>.*` | `/opsx-propose`, `/opsx-apply` |Cursor、Devin 桌面、Copilot (IDE)、Trae、Oh My Pi|
|无 — 仅技能| `/openspec-propose`, `/openspec-apply-change` |CodeArts、ForgeCode、Hermes、MiniMax 代码、Mistral Vibe、Zed Agent、共享 `.agents`|
|无 — Kimi 代码| `/skill:openspec-propose` |基米密码|
|无 — Codex CLI| `$openspec-propose` | Codex |

> **Devin Desktop 与 Devin Local：** `.devin/workflows/opsx-*.md` 文件给出 
> Devin Desktop `/opsx-propose`。 Devin Local 没有工作流程 - 使用技能 
> OpenSpec 写入 `.devin/skills/`，例如`/openspec-propose`，适用于 
>  和 agents。

不同工具的意图是相同的，但命令的显示方式可能因集成而异。 [如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)列出了每个支持的工具；该表仅显示每种形状的示例。

> **注意：** GitHub Copilot 命令 (`.github/prompts/*.prompt.md`) 仅在 IDE 扩展（VS Code、JetBrains、Visual Studio）中可用。 GitHub Copilot CLI 目前不支持自定义提示文件 - 请参阅 [ 支持的工具 ](<OpenSpec 支持的 AI 工具与调用方式.md>) 了解详细信息和解决方法。

---

## 旧命令

这些命令使用较旧的“一次性”工作流程。它们仍然有效，但建议使用 OPSX 命令。

|命令|它的作用|
|---------|--------------|
| `/openspec:proposal` |一次创建所有工件（提案、规格、设计、任务）|
| `/openspec:apply` |实施变革|
| `/openspec:archive` |存档更改|

**何时使用旧命令：**
- 使用旧工作流程的现有项目
- 不需要增量创建工件的简单更改
- 偏好全有或全无的方法

**迁移到OPSX：**
可以使用OPSX命令继续旧版更改。工件结构是兼容的。

---

## 故障排除

### “未找到更改”

该命令无法识别要处理哪个更改。

**解决方案：**
- 明确指定更改名称：`/opsx:apply add-dark-mode`
- 检查更改文件夹是否存在：`openspec list`
- 验证您位于正确的项目目录中

### “没有准备好文物”

所有工件要么完整，要么因缺少依赖项而受阻。

**解决方案：**
- 运行 `openspec status --change <name>` 查看阻塞的内容
- 检查所需的工件是否存在
- 首先创建缺失的依赖项

### “未找到架构”

指定的架构不存在。

**解决方案：**
- 列出可用模式：`openspec schemas`
- 检查模式名称的拼写
- 如果是自定义架构，则创建架构：`openspec schema init <name>`

### 无法识别命令

AI 工具无法识别 OpenSpec 命令。

**解决方案：**
- 确保OpenSpec已初始化：`openspec init`
- 再生技能：`openspec update`
- 检查 `.claude/skills/` 目录是否存在（对于 Claude Code）
- 重新启动你的AI工具来学习新技能

### 工件未正确生成

AI 创建不完整或不正确的工件。

**解决方案：**
- 在`openspec/config.yaml`中添加项目上下文
- 添加每个工件的规则以获取具体指导
- 在变更描述中提供更多详细信息
- 使用 `/opsx:continue` 代替 `/opsx:ff` 进行更多控制

---

## 下一步

- [工作流程](<OpenSpec 快速与扩展工作流.md>) - 常见模式以及何时使用每个命令
- [CLI](<OpenSpec 命令行参考.md>) - 用于管理和验证的终端命令
- [自定义](<OpenSpec 项目配置与工作流定制.md>) - 创建自定义架构和工作流程


## 原文对照

<details>
<summary>展开完整原文</summary>

# Commands

This is the reference for OpenSpec's slash commands. These commands are invoked in your AI coding assistant's chat interface (e.g., Claude Code, Cursor, Devin Desktop).

For workflow patterns and when to use each command, see [Workflows](<OpenSpec 快速与扩展工作流.md>). For CLI commands, see [CLI](<OpenSpec 命令行参考.md>).

These pages use `/opsx:<command>` as the canonical name. Some tools spell it
differently — Cursor and GitHub Copilot register `/opsx-propose`, Codex uses
`$openspec-propose` — so check [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>)
for your tool. The files OpenSpec generates already use the right form.

## Quick Reference

### Default Quick Path (`core` profile)

| Command | Purpose |
|---------|---------|
| `/opsx:propose` | Create a change and generate planning artifacts in one step |
| `/opsx:explore` | Think through ideas before committing to a change |
| `/opsx:apply` | Implement tasks from the change |
| `/opsx:update` | Revise a change's planning artifacts and keep them coherent |
| `/opsx:sync` | Merge delta specs into main specs |
| `/opsx:archive` | Archive a completed change |

### Expanded Workflow Commands (custom workflow selection)

| Command | Purpose |
|---------|---------|
| `/opsx:new` | Start a new change scaffold |
| `/opsx:continue` | Create the next artifact based on dependencies |
| `/opsx:ff` | Fast-forward: create all planning artifacts at once |
| `/opsx:verify` | Validate implementation matches artifacts |
| `/opsx:bulk-archive` | Archive multiple changes at once |
| `/opsx:onboard` | Guided tutorial through the complete workflow |

The default global profile is `core`. To enable expanded workflow commands, run `openspec config profile`, select workflows, then run `openspec update` in your project.

---

## Command Reference

### `/opsx:propose`

Create a new change and generate planning artifacts in one step. This is the default start command in the `core` profile.

**Syntax:**
```text
/opsx:propose [change-name-or-description]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name-or-description` | No | Kebab-case name or plain-language change description |

**What it does:**
- Creates `openspec/changes/<change-name>/`
- Generates artifacts needed before implementation (for `spec-driven`: proposal, specs, design, tasks)
- Stops when the change is ready for `/opsx:apply`

**Example:**
```text
You: /opsx:propose add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md
     ✓ specs/ui/spec.md
     ✓ design.md
     ✓ tasks.md
     Ready for implementation. Run /opsx:apply.
```

**Tips:**
- Use this for the fastest end-to-end path
- If you want step-by-step artifact control, enable expanded workflows and use `/opsx:new` + `/opsx:continue`

---

### `/opsx:explore`

> **Start here when you're unsure.** Explore is a no-stakes thinking partner: it reads your codebase, compares options, and sharpens a fuzzy idea into a concrete plan before any code gets written. It ships in the default profile. For the full case and more examples, see the [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) guide.

Think through ideas, investigate problems, and clarify requirements before committing to a change.

**Syntax:**
```
/opsx:explore [topic]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `topic` | No | What you want to explore or investigate |

**What it does:**
- Opens an exploratory conversation with no structure required
- Investigates the codebase to answer questions
- Compares options and approaches
- Creates visual diagrams to clarify thinking
- Captures the exploration when you ask, or when you say yes to its offer: scaffolds a change with `openspec new change` and writes the planning artifacts you name, or updates an existing change's artifacts
- Can transition to `/opsx:propose` (default) or `/opsx:new` (expanded workflow) when insights crystallize

**Example:**
```text
You: /opsx:explore

AI:  What would you like to explore?

You: How should we handle authentication for the mobile app?

AI:  Let me investigate your current auth setup...
     [Analyzes codebase]

     I see you're using session-based auth for the web app.
     For mobile, we have three main options:

     1. JWT tokens (stateless, works offline)
     2. OAuth2 (if you need third-party login)
     3. Extend current sessions (simpler, but requires connectivity)

     Your API already has CORS configured. Which direction interests you?

You: Let's go with JWT.

AI:  That's a decision worth keeping. Want me to start a change called
     add-jwt-auth? Just the change folder, nothing else yet.

You: Yes.

AI:  Started openspec/changes/add-jwt-auth/. Say the word and I'll
     write the proposal, specs, and tasks from what we just worked out.
```

**Tips:**
- Use when requirements are unclear or you need to investigate
- It never writes code, and writes nothing else unless you ask, or say yes when it offers
- Good for comparing multiple approaches before deciding
- Can read files and search the codebase

---

### `/opsx:new`

Start a new change scaffold. Creates the change folder and waits for you to generate artifacts with `/opsx:continue` or `/opsx:ff`.

This command is part of the expanded workflow set (not included in the default `core` profile).

**Syntax:**
```
/opsx:new [change-name] [--schema <schema-name>]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Name for the change folder (prompted if not provided) |
| `--schema` | No | Workflow schema to use (default: from config or `spec-driven`) |

**What it does:**
- Creates `openspec/changes/<change-name>/` directory
- Creates `.openspec.yaml` metadata file in the change folder
- Shows the first artifact template ready for creation
- Prompts for change name and schema if not provided

**What it creates:**
```
openspec/changes/<change-name>/
└── .openspec.yaml    # Change metadata (schema, created date)
```

**Example:**
```
You: /opsx:new add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     Schema: spec-driven

     Ready to create: proposal
     Use /opsx:continue to create it, or /opsx:ff to create all artifacts.
```

**Tips:**
- Use descriptive names: `add-feature`, `fix-bug`, `refactor-module`
- Avoid generic names like `update`, `changes`, `wip`
- Schema can also be set in project config (`openspec/config.yaml`)

---

### `/opsx:continue`

Create the next artifact in the dependency chain. Creates one artifact at a time for incremental progress.

**Syntax:**
```
/opsx:continue [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to continue (inferred from context if not provided) |

**What it does:**
- Queries the artifact dependency graph
- Shows which artifacts are ready vs blocked
- Creates the first ready artifact
- Reads dependency files for context
- Shows what becomes available after creation

**Example:**
```
You: /opsx:continue

AI:  Change: add-dark-mode

     Artifact status:
     ✓ proposal    (done)
     ◆ specs       (ready)
     ◆ design      (ready)
     ○ tasks       (blocked - needs: specs, design)

     Creating specs...

     [Reads proposal.md, creates specs based on requirements]

     ✓ Created openspec/changes/add-dark-mode/specs/ui/spec.md

     Now available: design
     Run /opsx:continue to create the next artifact.
```

**Tips:**
- Use when you want to review each artifact before proceeding
- Good for complex changes where you want control
- Multiple artifacts may become ready simultaneously
- You can edit created artifacts before continuing

---

### `/opsx:ff`

Fast-forward through artifact creation. Creates all planning artifacts at once.

**Syntax:**
```
/opsx:ff [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to fast-forward (inferred from context if not provided) |

**What it does:**
- Creates all artifacts in dependency order
- Tracks progress via todo list
- Stops when all `apply-required` artifacts are complete
- Reads each dependency before creating the next artifact

**Example:**
```
You: /opsx:ff add-dark-mode

AI:  Fast-forwarding add-dark-mode...

     ✓ Creating proposal.md
     ✓ Creating specs/ui/spec.md
     ✓ Creating design.md
     ✓ Creating tasks.md

     All planning artifacts complete!
     Ready for implementation. Run /opsx:apply to begin.
```

**Tips:**
- Use when you have a clear picture of what you're building
- Faster than `/opsx:continue` for straightforward changes
- You can still edit artifacts afterward
- Good for small to medium features

---

### `/opsx:apply`

Implement tasks from the change. Works through the task list, writing code and checking off items.

**Syntax:**
```
/opsx:apply [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to implement (inferred from context if not provided) |

**What it does:**
- Reads `tasks.md` and identifies incomplete tasks
- Works through tasks one by one
- Writes code, creates files, runs tests as needed
- Marks tasks complete with checkboxes `[x]`

**Example:**
```
You: /opsx:apply

AI:  Implementing add-dark-mode...

     Reading tasks.md:
     - [ ] 1.1 Create ThemeContext
     - [ ] 1.2 Add CSS custom properties
     - [ ] 1.3 Implement localStorage persistence

     Working on 1.1: Create ThemeContext...
     [Creates src/context/ThemeContext.tsx]

     ✓ 1.1 Complete

     Working on 1.2: Add CSS custom properties...
     [Updates src/styles/globals.css]

     ✓ 1.2 Complete
     ...
```

**Tips:**
- Can resume where you left off if interrupted
- Use for parallel changes by specifying the change name
- Completion state is tracked in `tasks.md` checkboxes

---

### `/opsx:update`

Revise a change's existing planning artifacts and keep them coherent with one another. Planning artifacts only - it never edits code.

**Syntax:**

```text
/opsx:update [change-name]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to update (inferred from context if not provided) |

**What it does:**

- Reads the change's artifacts via `openspec status --change <name> --json`
- Applies your requested revision, or reviews the artifacts for contradictions if you didn't name one
- Reconciles the other existing artifacts in any direction (a design edit may ripple back to the proposal)
- Confirms every edit with you before writing, one artifact at a time
- Ends by recommending the next step: `/opsx:continue` (unstarted artifacts), `/opsx:apply` (carry a revised plan into code), or `/opsx:archive` (all done)

**Missing files:**

- For a glob artifact such as `specs/**/*.md` with at least one existing file, update can propose a missing companion file. It uses the schema's instructions and asks you to confirm the concrete path before creating it.
- Artifacts with no files yet remain with `/opsx:continue`. Intentionally skipped artifacts stay untouched.
- New files must stay inside the change directory. If a file appears at the confirmed path before creation, update stops instead of overwriting it.

**Example:**

```text
You: /opsx:update add-dark-mode - we're storing the theme in a cookie now, not localStorage

AI:  Reading add-dark-mode artifacts...

     The design references localStorage in two places; tasks 1.3 covers
     localStorage persistence; the proposal doesn't mention storage.

     Proposed revisions:
     1. design.md - swap localStorage decision for cookie storage
     2. tasks.md - reword task 1.3 to cookie persistence

     Apply revision 1? (design.md)
```

**Tips:**

- It won't start an artifact with no existing files. Enable `/opsx:continue` for that, or use `openspec status` and `openspec instructions` if that optional workflow isn't installed.
- If the change was already implemented, follow up with `/opsx:apply` so the code matches the revised plan
- If your revision changes the *intent* of the change, start fresh with a new change instead (see [When to Update vs. Start Fresh](<OpenSpec OPSX 工作流机制.md#when-to-update-vs-start-fresh>))

---

### `/opsx:verify`

Validate that implementation matches your change artifacts. Checks completeness, correctness, and coherence.

**Syntax:**
```
/opsx:verify [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to verify (inferred from context if not provided) |

**What it does:**
- Checks three dimensions of implementation quality
- Searches codebase for implementation evidence
- Reports issues categorized as CRITICAL, WARNING, or SUGGESTION
- Does not block archive, but surfaces issues

**Verification dimensions:**

| Dimension | What it validates |
|-----------|-------------------|
| **Completeness** | All tasks done, all requirements implemented, scenarios covered |
| **Correctness** | Implementation matches spec intent, edge cases handled |
| **Coherence** | Design decisions reflected in code, patterns consistent |

**Example:**
```
You: /opsx:verify

AI:  Verifying add-dark-mode...

     COMPLETENESS
     ✓ All 8 tasks in tasks.md are checked
     ✓ All requirements in specs have corresponding code
     ⚠ Scenario "System preference detection" has no test coverage

     CORRECTNESS
     ✓ Implementation matches spec intent
     ✓ Edge cases from scenarios are handled
     ✓ Error states match spec definitions

     COHERENCE
     ✓ Design decisions reflected in code structure
     ✓ Naming conventions consistent with design.md
     ⚠ Design mentions "CSS variables" but implementation uses Tailwind classes

     SUMMARY
     ─────────────────────────────
     Critical issues: 0
     Warnings: 2
     Ready to archive: Yes (with warnings)

     Recommendations:
     1. Add test for system preference detection
     2. Update design.md to reflect Tailwind usage, or refactor to use CSS variables
```

**Tips:**
- Run before archiving to catch mismatches early
- Warnings don't block archive but indicate potential issues
- Good for reviewing AI's work before committing
- Can reveal drift between artifacts and implementation

---

### `/opsx:sync`

**Optional command.** Merge delta specs from a change into main specs. Archive will prompt to sync if needed, so you typically don't need to run this manually.

**Syntax:**
```
/opsx:sync [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to sync (inferred from context if not provided) |

**What it does:**
- Reads delta specs from change folder
- Parses ADDED/MODIFIED/REMOVED/RENAMED sections
- Merges changes into main `openspec/specs/` directory
- Preserves existing content not mentioned in delta
- Does not archive the change (remains active)

**Example:**
```text
You: /opsx:sync

AI:  Syncing add-dark-mode delta specs...

     Reading: openspec/changes/add-dark-mode/specs/ui/spec.md
     Target:  openspec/specs/ui/spec.md

     Changes to apply:
     ✓ ADDED: Theme Selection requirement (2 scenarios)
     ✓ ADDED: Accessibility requirement (1 scenario)

     Merging...
     ✓ openspec/specs/ui/spec.md updated

     Change remains active. Run /opsx:archive when ready to complete.
```

**When to use manually:**

| Scenario | Use sync? |
|----------|-----------|
| Long-running change, want specs in main before archiving | Yes |
| Multiple parallel changes need the updated base specs | Yes |
| Want to preview/review the merge separately | Yes |
| Quick change, going straight to archive | No (archive handles it) |

**Tips:**
- Sync is intelligent, not copy-paste
- Can add scenarios to existing requirements without duplicating
- Change stays active after sync (not archived)
- Most users will never need to call this directly—archive prompts if needed

---

### `/opsx:archive`

Archive a completed change. Finalizes the change and moves it to the archive folder.

**Syntax:**
```
/opsx:archive [change-name]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Which change to archive (inferred from context if not provided) |

**What it does:**
- Checks artifact completion status
- Checks task completion (warns if incomplete)
- Offers to sync delta specs if not already synced
- Moves change folder to `openspec/changes/archive/YYYY-MM-DD-<name>/`
- Preserves all artifacts for audit trail

**Example:**
```text
You: /opsx:archive

AI:  Archiving add-dark-mode...

     Artifact status:
     ✓ proposal.md exists
     ✓ specs/ exists
     ✓ design.md exists
     ✓ tasks.md exists (8/8 tasks complete)

     Delta specs: Not yet synced
     → Sync now? (recommended)

You: Yes

AI:  ✓ Synced specs to openspec/specs/ui/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/

     Change archived successfully.
```

**Tips:**
- Archive won't block on incomplete tasks, but will warn
- Delta specs can be synced during archive or beforehand
- Archived changes are preserved for history
- Use `/opsx:verify` first to catch issues

---

### `/opsx:bulk-archive`

Archive multiple completed changes at once. Handles spec conflicts between changes.

**Syntax:**
```
/opsx:bulk-archive [change-names...]
```

**Arguments:**
| Argument | Required | Description |
|----------|----------|-------------|
| `change-names` | No | Specific changes to archive (prompts to select if not provided) |

**What it does:**
- Lists all completed changes
- Validates each change before archiving
- Detects spec conflicts across changes
- Resolves conflicts by checking what's actually implemented
- Archives in chronological order

**Example:**
```
You: /opsx:bulk-archive

AI:  Found 3 completed changes:
     - add-dark-mode (8/8 tasks complete)
     - fix-login-bug (3/3 tasks complete)
     - update-footer (2/2 tasks complete)

     Checking for spec conflicts...
     ⚠ add-dark-mode and update-footer both touch specs/ui/

     Inspecting codebase to resolve...
     Both changes are implemented. Will merge in chronological order:
     1. add-dark-mode (created Jan 20)
     2. update-footer (created Jan 22)

     Archive all 3 changes?

You: Yes

AI:  ✓ Archived add-dark-mode
     ✓ Archived fix-login-bug
     ✓ Archived update-footer
     Specs merged: add-dark-mode → update-footer
```

**Tips:**
- Good for parallel work streams
- Conflict resolution is agentic (checks codebase)
- Changes are archived in order of creation
- Prompts before overwriting spec content

---

### `/opsx:onboard`

Guided onboarding through the complete OpenSpec workflow. An interactive tutorial using your actual codebase.

**Syntax:**
```
/opsx:onboard
```

**What it does:**
- Walks through a complete workflow cycle with narration
- Scans your codebase for real improvement opportunities
- Creates an actual change with real artifacts
- Implements actual work (small, safe changes)
- Archives the completed change
- Explains each step as it happens

**Phases:**
1. Welcome and codebase analysis
2. Finding an improvement opportunity
3. Creating a change (`/opsx:new`)
4. Writing the proposal
5. Creating specs
6. Writing the design
7. Creating tasks
8. Implementing tasks (`/opsx:apply`)
9. Verifying implementation
10. Archiving the change
11. Summary and next steps

**Example:**
```
You: /opsx:onboard

AI:  Welcome to OpenSpec!

     I'll walk you through the complete workflow using your actual codebase.
     We'll find something small to improve, create a proper change for it,
     implement it, and archive it.

     Let me scan your codebase for opportunities...

     [Analyzes codebase]

     I found a few things we could work on:
     1. Add input validation to the contact form
     2. Improve error messages in the auth flow
     3. Add loading states to async buttons

     Which interests you? (or suggest something else)
```

**Tips:**
- Best for new users learning the workflow
- Uses real code, not toy examples
- Creates a real change you can keep or discard
- Takes 15-30 minutes to complete

---

## Command Syntax by AI Tool

Different AI tools use slightly different command syntax. Use the format that matches your tool:

| Your tool's command file | Syntax example | Example tools |
|--------------------------|----------------|---------------|
| `.../commands/opsx/<id>.*` | `/opsx:propose`, `/opsx:apply` | Claude Code, Gemini CLI, Crush |
| `.../opsx-<id>.*` | `/opsx-propose`, `/opsx-apply` | Cursor, Devin Desktop, Copilot (IDE), Trae, Oh My Pi |
| none — skills only | `/openspec-propose`, `/openspec-apply-change` | CodeArts, ForgeCode, Hermes, MiniMax Code, Mistral Vibe, Zed Agent, shared `.agents` |
| none — Kimi Code | `/skill:openspec-propose` | Kimi Code |
| none — Codex CLI | `$openspec-propose` | Codex |

> **Devin Desktop vs Devin Local:** the `.devin/workflows/opsx-*.md` files give
> Devin Desktop `/opsx-propose`. Devin Local has no workflows — use the skills
> OpenSpec writes to `.devin/skills/`, e.g. `/openspec-propose`, which work on
> both agents.

The intent is the same across tools, but how commands are surfaced can differ by integration. [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>) lists every supported tool; this table shows only examples of each shape.

> **Note:** GitHub Copilot commands (`.github/prompts/*.prompt.md`) are only available in IDE extensions (VS Code, JetBrains, Visual Studio). GitHub Copilot CLI does not currently support custom prompt files — see [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>) for details and workarounds.

---

## Legacy Commands

These commands use the older "all-at-once" workflow. They still work but OPSX commands are recommended.

| Command | What it does |
|---------|--------------|
| `/openspec:proposal` | Create all artifacts at once (proposal, specs, design, tasks) |
| `/openspec:apply` | Implement the change |
| `/openspec:archive` | Archive the change |

**When to use legacy commands:**
- Existing projects using the old workflow
- Simple changes where you don't need incremental artifact creation
- Preference for the all-or-nothing approach

**Migrating to OPSX:**
Legacy changes can be continued with OPSX commands. The artifact structure is compatible.

---

## Troubleshooting

### "Change not found"

The command couldn't identify which change to work on.

**Solutions:**
- Specify the change name explicitly: `/opsx:apply add-dark-mode`
- Check that the change folder exists: `openspec list`
- Verify you're in the right project directory

### "No artifacts ready"

All artifacts are either complete or blocked by missing dependencies.

**Solutions:**
- Run `openspec status --change <name>` to see what's blocking
- Check if required artifacts exist
- Create missing dependency artifacts first

### "Schema not found"

The specified schema doesn't exist.

**Solutions:**
- List available schemas: `openspec schemas`
- Check spelling of schema name
- Create the schema if it's custom: `openspec schema init <name>`

### Commands not recognized

The AI tool doesn't recognize OpenSpec commands.

**Solutions:**
- Ensure OpenSpec is initialized: `openspec init`
- Regenerate skills: `openspec update`
- Check that `.claude/skills/` directory exists (for Claude Code)
- Restart your AI tool to pick up new skills

### Artifacts not generating properly

The AI creates incomplete or incorrect artifacts.

**Solutions:**
- Add project context in `openspec/config.yaml`
- Add per-artifact rules for specific guidance
- Provide more detail in your change description
- Use `/opsx:continue` instead of `/opsx:ff` for more control

---

## Next Steps

- [Workflows](<OpenSpec 快速与扩展工作流.md>) - Common patterns and when to use each command
- [CLI](<OpenSpec 命令行参考.md>) - Terminal commands for management and validation
- [Customization](<OpenSpec 项目配置与工作流定制.md>) - Create custom schemas and workflows


</details>
