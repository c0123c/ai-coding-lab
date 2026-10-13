# OpenSpec 入门与首个变更

介绍初始化后第一个变更的工作循环、生成的工件、增量规格格式，以及终端命令和 AI 聊天命令的运行位置。

- 来源：OpenSpec
- 资料类型：入门操作指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/getting-started.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/getting-started.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 开始使用

本指南解释了 OpenSpec 在安装并初始化后如何工作。有关安装说明，请参阅[主README](<OpenSpec 项目介绍与快速开始.md#快速入门>)或[安装指南](<OpenSpec 安装与初始化指南.md>)。对整个文档集不熟悉？ [文档主页](<OpenSpec 文档导航与学习路线.md>)映射了所有内容。

> **我在哪里输入这些命令？**两个地方，混合它们是最常见的早期错误。
>
> - `openspec ...` 命令（如 `openspec init`）在您的 ** 终端 ** 中运行。
> - `/opsx:...` 命令（如 `/opsx:propose`）在您的 **AI 助手的聊天室 ** 中运行，您可以在同一个框中要求它编写代码。
>
> 没有单独的“交互模式”可以启动。您只需在聊天中输入斜杠命令，您的助手就会从那里获取它。完整说明：[命令如何工作](<OpenSpec 两类命令如何协作.md>)。

## 你的前五分钟

整个循环，每个步骤都标有发生的位置：

```text
TERMINAL   $ npm install -g @fission-ai/openspec@latest
TERMINAL   $ cd your-project && openspec init
AI CHAT      /opsx:explore                    (optional: think it through first)
AI CHAT      /opsx:propose add-dark-mode      (AI drafts the plan; you review it)
AI CHAT      /opsx:apply                      (AI builds it)
AI CHAT      /opsx:archive                    (specs updated, change filed away)
```

设置两个终端步骤，然后您就可以开始聊天了。本指南的其余部分将详细介绍每个步骤的作用以及您将看到的内容。

**不想自己做终端部分？**将[设置提示](<OpenSpec 安装与初始化指南.md#使用-ai-助手安装>)粘贴到您的助手中，它会处理这两行，然后报告它创建的内容。

> **还不知道要构建什么？从 `/opsx:explore`.** 开始，它是一个无风险思考的合作伙伴，可以在编写任何代码之前读取您的代码库、权衡选项并将模糊的想法转化为具体的计划。当图像清晰时，将其交给`/opsx:propose`。这是使用 AI 的最佳习惯，否则它会自信地构建出错误的东西。请参阅[探索指南](<使用 OpenSpec 探索问题与澄清需求.md>)。

## 它是如何运作的

OpenSpec 帮助您和您的 AI 编码助手在编写任何代码之前就要构建的内容达成一致。

**默认快速路径（核心配置文件）：**

```text
/opsx:explore ──► /opsx:propose ──► /opsx:apply ──► /opsx:sync ──► /opsx:archive
   (optional)
```

当您正在弄清楚要做什么时，请从 `/opsx:explore` 开始，或者当您已经知道时，请直接跳至 `/opsx:propose`。探索位于默认配置文件中，因此当您需要时它始终存在。

**扩展路径（自定义工作流程选择）：**

```text
/opsx:new ──► /opsx:ff or /opsx:continue ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

默认全局配置文件为“`core`”，包括“`propose`”、“`explore`”、“`apply`”、“`update`”、“`sync`”和“`archive`”。您可以使用 `openspec config profile` 和 `openspec update` 启用扩展的工作流程命令。

## OpenSpec 创建了什么

运行`openspec init`后，您的项目具有以下结构：

```
openspec/
├── specs/              # Source of truth (your system's behavior)
│   └── <domain>/
│       └── spec.md
├── changes/            # Proposed updates (one folder per change)
│   └── <change-name>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/      # Delta specs (what's changing)
│           └── <domain>/
│               └── spec.md
└── config.yaml         # Project configuration (optional)
```

**两个关键目录：**

- **`specs/`** - 真相的来源。这些规格描述了您的系统当前的行为方式。按域组织（例如，`specs/auth/`、`specs/payments/`）。

- **`changes/`** - 提议的修改。每个更改都有自己的文件夹，其中包含所有相关的工件。更改完成后，其规格会合并到主 `specs/` 目录中。

## 了解工件

每个更改文件夹都包含指导工作的工件：

|神器|目的|
|----------|---------|
| `proposal.md` |“为什么”和“什么”——捕捉意图、范围和方法|
| `specs/` |达美规格显示 ADDED/MODIFIED/REMOVED 要求|
| `design.md` |“如何”——技术方法和架构决策|
| `tasks.md` |带有复选框的实施清单|

**工件相互构建：**

```
proposal ──► specs ──► design ──► tasks ──► implement
   ▲           ▲          ▲                    │
   └───────────┴──────────┴────────────────────┘
            update as you learn
```

当您在实施过程中了解更多信息时，您可以随时返回并完善早期的工件。

## Delta 规格的工作原理

Delta 规格是 OpenSpec 中的关键概念。它们显示了相对于您当前规格的变化。

### 格式

Delta 规范使用部分来指示更改的类型：

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST require a second factor during login.

#### Scenario: OTP required
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented

## MODIFIED Requirements

### Requirement: Session Timeout
The system SHALL expire sessions after 30 minutes of inactivity.
(Previously: 60 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA)
```

### 档案中发生了什么

当您归档更改时：

1. **ADDED** 要求附加在主要规格中
2. **MODIFIED**要求替换现有版本
3. **REMOVED** 要求从主要规范中删除

更改文件夹移动到 `openspec/changes/archive/` 以查看审核历史记录。

## 示例：您的第一个改变

让我们逐步了解如何向应用程序添加暗模式。

### 1。开始更改（默认）

```text
You: /opsx:propose add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md — why we're doing this, what's changing
     ✓ specs/       — requirements and scenarios
     ✓ design.md    — technical approach
     ✓ tasks.md     — implementation checklist
     Ready for implementation!
```

如果您已启用扩展的工作流程配置文件，则还可以通过两个步骤执行此操作：`/opsx:new`，然后 `/opsx:ff`（或增量 `/opsx:continue`）。

### 2。创建了什么

**proposal.md** - 捕获意图：

```markdown
# Proposal: Add Dark Mode

## Intent
Users have requested a dark mode option to reduce eye strain
during nighttime usage.

## Scope
- Add theme toggle in settings
- Support system preference detection
- Persist preference in localStorage

## Approach
Use CSS custom properties for theming with a React context
for state management.
```

**specs/ui/spec.md** - 达美航空显示新要求：

```markdown
# Delta for UI

## ADDED Requirements

### Requirement: Theme Selection
The system SHALL allow users to choose between light and dark themes.

#### Scenario: Manual toggle
- GIVEN a user on any page
- WHEN the user clicks the theme toggle
- THEN the theme switches immediately
- AND the preference persists across sessions

#### Scenario: System preference
- GIVEN a user with no saved preference
- WHEN the application loads
- THEN the system's preferred color scheme is used
```

**tasks.md** - 实施清单：

```markdown
# Tasks

## 1. Theme Infrastructure
- [ ] 1.1 Create ThemeContext with light/dark state
- [ ] 1.2 Add CSS custom properties for colors
- [ ] 1.3 Implement localStorage persistence

## 2. UI Components
- [ ] 2.1 Create ThemeToggle component
- [ ] 2.2 Add toggle to settings page
- [ ] 2.3 Update Header to include quick toggle

## 3. Styling
- [ ] 3.1 Define dark theme color palette
- [ ] 3.2 Update components to use CSS variables
```

### 3。实施

```
You: /opsx:apply

AI:  Working through tasks...
     ✓ 1.1 Created ThemeContext with light/dark state
     ✓ 1.2 Added CSS custom properties to globals.css
     ✓ 1.3 Implemented localStorage persistence
     ✓ 2.1 Created ThemeToggle component
     ...
     All tasks complete!
```

在实施过程中，如果您发现设计需要调整，只需更新工件并继续即可。

### 4。存档

```
You: /opsx:archive

AI:  Archiving add-dark-mode...
     ✓ Merged specs into openspec/specs/ui/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/
     Done! Ready for the next feature.
```

您的增量规格现在是主要规格的一部分，记录您的系统如何工作。

## 验证和审查

使用 CLI 检查您的更改：

```bash
# List active changes
openspec list

# View change details
openspec show add-dark-mode

# Validate spec formatting
openspec validate add-dark-mode

# Interactive dashboard
openspec view
```

## 下一步

- [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>) - 在提交之前使用`/opsx:explore`思考一个想法
- [审查更改](<OpenSpec 变更审阅指南.md>) - 在任何代码之前在 AI 草案的计划中检查哪些内容
- [编写好的规范](<编写清晰的 OpenSpec 行为规格.md>) - 强烈的需求和场景是什么样的
- [在现有项目中使用OpenSpec](<在已有项目中采用 OpenSpec.md>) - 从大型棕地代码库开始
- [编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md>) - 更新工件、返回、协调手动编辑
- [核心概念一览](<OpenSpec 核心概念速览.md>) - 整个心理模型在一页上
- [示例和食谱](<OpenSpec 功能、修复与重构工作流实例.md>) - 真正的变化，从头到尾
- [工作流程](<OpenSpec 快速与扩展工作流.md>) - 常见模式以及何时使用每个命令
- [命令](<OpenSpec AI 聊天命令参考.md>) - 所有斜杠命令的完整参考
- [概念](<OpenSpec 核心概念与规格结构.md>) - 更深入地了解规范、变更和架构
- [定制](<OpenSpec 项目配置与工作流定制.md>) - 让OpenSpec按您的方式工作
- [商店](<OpenSpec Store 跨仓库规划指南（Beta）.md>) - 跨存储库或团队的规划？将其保留在自己的存储库中（测试版）
- [FAQ](<OpenSpec 常见问题.md>) 和 [ 故障排除 ](<OpenSpec 安装与变更排障.md>) - 当您遇到困难时


## 原文对照

<details>
<summary>展开完整原文</summary>

# Getting Started

This guide explains how OpenSpec works after you've installed and initialized it. For installation instructions, see the [main README](<OpenSpec 项目介绍与快速开始.md#quick-start>) or the [Installation guide](<OpenSpec 安装与初始化指南.md>). New to the whole docs set? The [documentation home](<OpenSpec 文档导航与学习路线.md>) maps everything.

> **Where do I type these commands?** Two places, and mixing them up is the most common early stumble.
>
> - `openspec ...` commands (like `openspec init`) run in your **terminal**.
> - `/opsx:...` commands (like `/opsx:propose`) run in your **AI assistant's chat**, the same box where you'd ask it to write code.
>
> There's no separate "interactive mode" to start. You just type the slash command in chat and your assistant takes it from there. Full explanation: [How Commands Work](<OpenSpec 两类命令如何协作.md>).

## Your First Five Minutes

The whole loop, with each step labeled by where it happens:

```text
TERMINAL   $ npm install -g @fission-ai/openspec@latest
TERMINAL   $ cd your-project && openspec init
AI CHAT      /opsx:explore                    (optional: think it through first)
AI CHAT      /opsx:propose add-dark-mode      (AI drafts the plan; you review it)
AI CHAT      /opsx:apply                      (AI builds it)
AI CHAT      /opsx:archive                    (specs updated, change filed away)
```

Two terminal steps to set up, then you live in chat. The rest of this guide unpacks what each step does and what you'll see.

**Don't want to do the terminal part yourself?** Paste the [setup prompt](<OpenSpec 安装与初始化指南.md#install-with-your-ai-assistant>) into your assistant and it handles both lines, then reports what it created.

> **Not sure what to build yet? Start with `/opsx:explore`.** It's a no-stakes thinking partner that reads your codebase, weighs options, and sharpens a fuzzy idea into a concrete plan, all before any code gets written. When the picture is clear, it hands off to `/opsx:propose`. This is the single best habit for working with an AI that will otherwise confidently build the wrong thing. See the [Explore guide](<使用 OpenSpec 探索问题与澄清需求.md>).

## How It Works

OpenSpec helps you and your AI coding assistant agree on what to build before any code is written.

**Default quick path (core profile):**

```text
/opsx:explore ──► /opsx:propose ──► /opsx:apply ──► /opsx:sync ──► /opsx:archive
   (optional)
```

Start with `/opsx:explore` when you're figuring out what to do, or jump straight to `/opsx:propose` when you already know. Explore is in the default profile, so it's always there when you want it.

**Expanded path (custom workflow selection):**

```text
/opsx:new ──► /opsx:ff or /opsx:continue ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

The default global profile is `core`, which includes `propose`, `explore`, `apply`, `update`, `sync`, and `archive`. You can enable the expanded workflow commands with `openspec config profile` and then `openspec update`.

## What OpenSpec Creates

After running `openspec init`, your project has this structure:

```
openspec/
├── specs/              # Source of truth (your system's behavior)
│   └── <domain>/
│       └── spec.md
├── changes/            # Proposed updates (one folder per change)
│   └── <change-name>/
│       ├── proposal.md
│       ├── design.md
│       ├── tasks.md
│       └── specs/      # Delta specs (what's changing)
│           └── <domain>/
│               └── spec.md
└── config.yaml         # Project configuration (optional)
```

**Two key directories:**

- **`specs/`** - The source of truth. These specs describe how your system currently behaves. Organized by domain (e.g., `specs/auth/`, `specs/payments/`).

- **`changes/`** - Proposed modifications. Each change gets its own folder with all related artifacts. When a change is complete, its specs merge into the main `specs/` directory.

## Understanding Artifacts

Each change folder contains artifacts that guide the work:

| Artifact | Purpose |
|----------|---------|
| `proposal.md` | The "why" and "what" - captures intent, scope, and approach |
| `specs/` | Delta specs showing ADDED/MODIFIED/REMOVED requirements |
| `design.md` | The "how" - technical approach and architecture decisions |
| `tasks.md` | Implementation checklist with checkboxes |

**Artifacts build on each other:**

```
proposal ──► specs ──► design ──► tasks ──► implement
   ▲           ▲          ▲                    │
   └───────────┴──────────┴────────────────────┘
            update as you learn
```

You can always go back and refine earlier artifacts as you learn more during implementation.

## How Delta Specs Work

Delta specs are the key concept in OpenSpec. They show what's changing relative to your current specs.

### The Format

Delta specs use sections to indicate the type of change:

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST require a second factor during login.

#### Scenario: OTP required
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented

## MODIFIED Requirements

### Requirement: Session Timeout
The system SHALL expire sessions after 30 minutes of inactivity.
(Previously: 60 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA)
```

### What Happens on Archive

When you archive a change:

1. **ADDED** requirements are appended to the main spec
2. **MODIFIED** requirements replace the existing version
3. **REMOVED** requirements are deleted from the main spec

The change folder moves to `openspec/changes/archive/` for audit history.

## Example: Your First Change

Let's walk through adding dark mode to an application.

### 1. Start the Change (Default)

```text
You: /opsx:propose add-dark-mode

AI:  Created openspec/changes/add-dark-mode/
     ✓ proposal.md — why we're doing this, what's changing
     ✓ specs/       — requirements and scenarios
     ✓ design.md    — technical approach
     ✓ tasks.md     — implementation checklist
     Ready for implementation!
```

If you've enabled the expanded workflow profile, you can also do this as two steps: `/opsx:new` then `/opsx:ff` (or `/opsx:continue` incrementally).

### 2. What Gets Created

**proposal.md** - Captures the intent:

```markdown
# Proposal: Add Dark Mode

## Intent
Users have requested a dark mode option to reduce eye strain
during nighttime usage.

## Scope
- Add theme toggle in settings
- Support system preference detection
- Persist preference in localStorage

## Approach
Use CSS custom properties for theming with a React context
for state management.
```

**specs/ui/spec.md** - Delta showing new requirements:

```markdown
# Delta for UI

## ADDED Requirements

### Requirement: Theme Selection
The system SHALL allow users to choose between light and dark themes.

#### Scenario: Manual toggle
- GIVEN a user on any page
- WHEN the user clicks the theme toggle
- THEN the theme switches immediately
- AND the preference persists across sessions

#### Scenario: System preference
- GIVEN a user with no saved preference
- WHEN the application loads
- THEN the system's preferred color scheme is used
```

**tasks.md** - Implementation checklist:

```markdown
# Tasks

## 1. Theme Infrastructure
- [ ] 1.1 Create ThemeContext with light/dark state
- [ ] 1.2 Add CSS custom properties for colors
- [ ] 1.3 Implement localStorage persistence

## 2. UI Components
- [ ] 2.1 Create ThemeToggle component
- [ ] 2.2 Add toggle to settings page
- [ ] 2.3 Update Header to include quick toggle

## 3. Styling
- [ ] 3.1 Define dark theme color palette
- [ ] 3.2 Update components to use CSS variables
```

### 3. Implement

```
You: /opsx:apply

AI:  Working through tasks...
     ✓ 1.1 Created ThemeContext with light/dark state
     ✓ 1.2 Added CSS custom properties to globals.css
     ✓ 1.3 Implemented localStorage persistence
     ✓ 2.1 Created ThemeToggle component
     ...
     All tasks complete!
```

During implementation, if you discover the design needs adjustment, just update the artifact and continue.

### 4. Archive

```
You: /opsx:archive

AI:  Archiving add-dark-mode...
     ✓ Merged specs into openspec/specs/ui/spec.md
     ✓ Moved to openspec/changes/archive/2025-01-24-add-dark-mode/
     Done! Ready for the next feature.
```

Your delta specs are now part of the main specs, documenting how your system works.

## Verifying and Reviewing

Use the CLI to check on your changes:

```bash
# List active changes
openspec list

# View change details
openspec show add-dark-mode

# Validate spec formatting
openspec validate add-dark-mode

# Interactive dashboard
openspec view
```

## Next Steps

- [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) - Use `/opsx:explore` to think through an idea before you commit
- [Reviewing a Change](<OpenSpec 变更审阅指南.md>) - What to check in the plan the AI drafts, before any code
- [Writing Good Specs](<编写清晰的 OpenSpec 行为规格.md>) - What a strong requirement and scenario look like
- [Using OpenSpec in an Existing Project](<在已有项目中采用 OpenSpec.md>) - Start on a large brownfield codebase
- [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md>) - Update artifacts, go back, reconcile manual edits
- [Core Concepts at a Glance](<OpenSpec 核心概念速览.md>) - The whole mental model on one page
- [Examples & Recipes](<OpenSpec 功能、修复与重构工作流实例.md>) - Real changes, start to finish
- [Workflows](<OpenSpec 快速与扩展工作流.md>) - Common patterns and when to use each command
- [Commands](<OpenSpec AI 聊天命令参考.md>) - Full reference for all slash commands
- [Concepts](<OpenSpec 核心概念与规格结构.md>) - Deeper understanding of specs, changes, and schemas
- [Customization](<OpenSpec 项目配置与工作流定制.md>) - Make OpenSpec work your way
- [Stores](<OpenSpec Store 跨仓库规划指南（Beta）.md>) - Planning that spans repos or teams? Keep it in its own repo (beta)
- [FAQ](<OpenSpec 常见问题.md>) and [Troubleshooting](<OpenSpec 安装与变更排障.md>) - When you get stuck


</details>
