# OpenSpec 核心概念与规格结构

解释规格、变更、增量规格与工件的关系，以及流动、迭代、轻量和适用已有代码库的工作方式。

- 来源：OpenSpec
- 资料类型：概念与数据结构说明
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/concepts.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/concepts.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 概念

本指南解释了 OpenSpec 背后的核心思想以及它们如何组合在一起。实际使用请参见[入门](<OpenSpec 入门与首个变更.md>)和[工作流程](<OpenSpec 快速与扩展工作流.md>)。

## 理念

OpenSpec 围绕四个原则构建：

```
fluid not rigid         — no phase gates, work on what makes sense
iterative not waterfall — learn as you build, refine as you go
easy not complex        — lightweight setup, minimal ceremony
brownfield-first        — works with existing codebases, not just greenfield
```

### 为什么这些原则很重要

**流动而不僵化。**传统的规范系统将您锁定在几个阶段：首先您计划，然后您实施，然后您就完成了。 OpenSpec 更加灵活 — 您可以按照对您的工作有意义的任何顺序创建工件。

**迭代而非瀑布。**需求发生变化。理解加深。一开始看似不错的方法在您看到代码库后可能行不通。 OpenSpec接受这个现实。

**简单而不复杂。**一些规范框架需要大量的设置、严格的格式或重量级的流程。 OpenSpec 不妨碍您。在几秒钟内初始化，立即开始工作，仅在需要时进行定制。

**布朗菲尔德优先。** 大多数软件工作不是从头开始构建的 - 它正在修改现有系统。 OpenSpec 基于增量的方法可以轻松指定对现有行为的更改，而不仅仅是描述新系统。

## 大局观

OpenSpec 将您的工作分为两个主要领域：

```
┌────────────────────────────────────────────────────────────────────┐
│                        openspec/                                   │
│                                                                    │
│   ┌─────────────────────┐      ┌───────────────────────────────┐   │
│   │       specs/        │      │         changes/              │   │
│   │                     │      │                               │   │
│   │  Source of truth    │◄─────│  Proposed modifications       │   │
│   │  How your system    │ merge│  Each change = one folder     │   │
│   │  currently works    │      │  Contains artifacts + deltas  │   │
│   │                     │      │                               │   │
│   └─────────────────────┘      └───────────────────────────────┘   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

**规格**是事实来源——它们描述了您的系统当前的行为方式。

**更改**是建议的修改 - 它们位于单独的文件夹中，直到您准备好合并它们为止。

这种分离是关键。您可以并行处理多个更改，而不会发生冲突。您可以在更改影响主要规格之前对其进行检查。当您归档更改时，其增量会干净地合并到事实来源中。

## 规格

规范使用结构化需求和场景描述系统的行为。

### 结构

```
openspec/specs/
├── auth/
│   └── spec.md           # Authentication behavior
├── payments/
│   └── spec.md           # Payment processing
├── notifications/
│   └── spec.md           # Notification system
└── ui/
    └── spec.md           # UI behavior and themes
```

按领域组织规范 - 对您的系统有意义的逻辑分组。常见模式：

- **按功能区域**：`auth/`、`payments/`、`search/`
- **按组件**：`api/`、`frontend/`、`workers/`
- **按有界上下文**：`ordering/`，`fulfillment/`，`inventory/`

### 规格格式

规范包含需求，每个需求都有场景：

```markdown
# Auth Specification

## Purpose
Authentication and session management for the application.

## Requirements

### Requirement: User Authentication
The system SHALL issue a JWT token upon successful login.

#### Scenario: Valid credentials
- GIVEN a user with valid credentials
- WHEN the user submits login form
- THEN a JWT token is returned
- AND the user is redirected to dashboard

#### Scenario: Invalid credentials
- GIVEN invalid credentials
- WHEN the user submits login form
- THEN an error message is displayed
- AND no token is issued

### Requirement: Session Expiration
The system MUST expire sessions after 30 minutes of inactivity.

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated
- AND the user must re-authenticate
```

**关键要素：**

|元素|目的|
|---------|---------|
| `## Purpose` |该规范域的高级描述|
| `### Requirement:` |系统必须具有的特定行为|
| `#### Scenario:` |实际需求的具体示例|
| SHALL/MUST/SHOULD |RFC 2119 表示需求强度的关键字|

### 为什么要以这种方式构建规格

**需求是“什么”**——它们说明系统应该做什么，而不指定实现。

**场景是“何时”**——它们提供了可以验证的具体示例。好的场景：
- 可测试（您可以为它们编写自动化测试）
- 涵盖快乐路径和边缘情况
- 使用 Given/When/Then 或类似的结构化格式

**RFC 2119 关键字** (SHALL、MUST、SHOULD、MAY) 传达意图：
- **MUST/SHALL** — 绝对要求
- **SHOULD** — 推荐，但存在例外情况
- **MAY** — 可选

### 规格是什么（和不是什么）

规范是**行为契约**，而不是实施计划。

良好的规格内容：
- 用户或下游系统依赖的可观察行为
- 输入、输出和错误条件
- 外部约束（安全性、隐私性、可靠性、兼容性）
- 可以测试或显式验证的场景

规格中避免：
- 内部 class/function 名称
- 库或框架选择
- 分步实施细节
- 详细的执行计划（属于`design.md`或`tasks.md`）

快速测试：
- 如果可以在不改变外部可见行为的情况下改变实现，那么它可能不属于规范。

### 保持轻量化：渐进式严谨

OpenSpec旨在避免官僚主义。使用仍然可以验证更改的最轻级别。

**精简版规格（默认）：**
- 简短的行为优先要求
- 明确的范围和非目标
- 一些具体的验收检查

**完整规格（风险较高）：**
- 跨团队或跨存储库更改
- API/contract 更改、迁移、security/privacy 问题
- 含糊不清的变更可能会导致昂贵的返工

大多数更改应保留在 Lite 模式下。

### 人类+Agent协作

在许多团队中，人类探索并agents起草工件。预期的循环是：

1. 人类提供意图、背景和约束。
2. Agent 将其转换为行为优先的需求和场景。
3. Agent 将实现细节保留在 `design.md` 和 `tasks.md` 中，而不是 `spec.md` 中。
4. 在实施之前进行验证以确认结构和清晰度。

这使得规范对于人类来说是可读的并且对于 agents 来说是一致的。

## 变化

更改是对系统的建议修改，打包为一个文件夹，其中包含理解和实现它所需的所有内容。

### 改变结构

```
openspec/changes/add-dark-mode/
├── proposal.md           # Why and what
├── design.md             # How (technical approach)
├── tasks.md              # Implementation checklist
├── .openspec.yaml        # Change metadata (optional): schema, created, skip_specs, retire_capabilities
└── specs/                # Delta specs
    └── ui/
        └── spec.md       # What's changing in ui/spec.md
```

每个更改都是独立的。它有：
- **Artifacts** — 捕获意图、设计和任务的文档
- **Delta 规格** — 添加、修改或删除内容的规格
- **元数据** - 此特定更改的可选配置

### 为什么更改是文件夹

将更改打包为文件夹有几个好处：

1. **一切都在一起。**提案、设计、任务和规格集中在一处。禁止在不同地点狩猎。

2. **并行工作。**多个更改可以同时存在而不发生冲突。 `add-dark-mode` 的工作同时 `fix-auth-bug` 也在进行中。

3. **清除历史记录。**存档后，更改将移至`changes/archive/`，并保留其完整上下文。您可以回顾并了解不仅发生了什么变化，而且还了解原因。

4. **审查友好。**更改文件夹很容易审查 - 打开它，阅读提案，检查设计，查看规格增量。

## 文物

工件是变更中指导工作的文档。

### 工件流程

```
proposal ──────► specs ──────► design ──────► tasks ──────► implement
    │               │             │              │
   why            what           how          steps
 + scope        changes       approach      to take
```

工件相互构建。每个工件都为下一个工件提供上下文。

### 神器类型

#### 提案（`proposal.md`）

该提案在高层次上捕获了**意图**、**范围**和**方法**。

```markdown
# Proposal: Add Dark Mode

## Intent
Users have requested a dark mode option to reduce eye strain
during nighttime usage and match system preferences.

## Scope
In scope:
- Theme toggle in settings
- System preference detection
- Persist preference in localStorage

Out of scope:
- Custom color themes (future work)
- Per-page theme overrides

## Approach
Use CSS custom properties for theming with a React context
for state management. Detect system preference on first load,
allow manual override.
```

**何时更新提案：**
- 范围变更（缩小或扩大）
- 意图澄清（更好地理解问题）
- 方法从根本上转变

#### 规格（`specs/` 中的增量规格）

Delta 规格描述了 ** 相对于当前规格的变化 **。请参阅下面的 [Delta 规格 ](<#达美规格>)。

#### 设计（`design.md`）

该设计捕获了**技术方法**和**架构决策**。

````markdown
# Design: Add Dark Mode

## Technical Approach
Theme state managed via React Context to avoid prop drilling.
CSS custom properties enable runtime switching without class toggling.

## Architecture Decisions

### Decision: Context over Redux
Using React Context for theme state because:
- Simple binary state (light/dark)
- No complex state transitions
- Avoids adding Redux dependency

### Decision: CSS Custom Properties
Using CSS variables instead of CSS-in-JS because:
- Works with existing stylesheet
- No runtime overhead
- Browser-native solution

## Data Flow
```
ThemeProvider (context)
       │
       ▼
ThemeToggle ◄──► localStorage
       │
       ▼
CSS Variables (applied to :root)
```

## File Changes
- `src/contexts/ThemeContext.tsx` (new)
- `src/components/ThemeToggle.tsx` (new)
- `src/styles/globals.css` (modified)
````

**何时更新设计：**
- 实施表明该方法行不通
- 发现更好的解决方案
- 依赖性或约束发生变化

#### 任务（`tasks.md`）

任务是**实施清单**——带有复选框的具体步骤。

```markdown
# Tasks

## 1. Theme Infrastructure
- [ ] 1.1 Create ThemeContext with light/dark state
- [ ] 1.2 Add CSS custom properties for colors
- [ ] 1.3 Implement localStorage persistence
- [ ] 1.4 Add system preference detection

## 2. UI Components
- [ ] 2.1 Create ThemeToggle component
- [ ] 2.2 Add toggle to settings page
- [ ] 2.3 Update Header to include quick toggle

## 3. Styling
- [ ] 3.1 Define dark theme color palette
- [ ] 3.2 Update components to use CSS variables
- [ ] 3.3 Test contrast ratios for accessibility
```

**任务最佳实践：**
- 将相关任务分组到标题下
- 使用分层编号（1.1、1.2等）
- 保持任务足够小，以便在一次会话中完成
- 说明每个任务的验证方式（测试、命令或可观察结果）
- 将每个小组的工作所需的测试和文档放在该小组内部，而不是放在最终的追赶小组中
- 完成任务后将其勾掉

## 达美规格

Delta 规格是使 OpenSpec 适用于棕地开发的关键概念。他们描述了**正在发生的变化**，而不是重述整个规范。

### 格式

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST support TOTP-based two-factor authentication.

#### Scenario: 2FA enrollment
- GIVEN a user without 2FA enabled
- WHEN the user enables 2FA in settings
- THEN a QR code is displayed for authenticator app setup
- AND the user must verify with a code before activation

#### Scenario: 2FA login
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented
- AND login completes only after valid OTP

## MODIFIED Requirements

### Requirement: Session Expiration
The system MUST expire sessions after 15 minutes of inactivity.
(Previously: 30 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 15 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA. Users should re-authenticate each session.)
```

### 三角洲剖面

|部分|含义|档案中发生了什么|
|---------|---------|------------------------|
| `## ADDED Requirements` |新行为|附加到主要规格|
| `## MODIFIED Requirements` |行为改变|取代现有要求|
| `## REMOVED Requirements` |已弃用的行为|从主要规范中删除；当更改声明 `retire_capabilities: true` 时，删除最后一个要求将停用该功能并删除其规范文件|
| `## Purpose` |全新功能的用途是什么|播种正在创建的主要规范的目的；当规范已经存在时被忽略|

### 为什么选择 Delta 而非全规格

**清晰度。** 增量准确地显示了正在发生的变化。阅读完整的规范，您必须在心里将其与当前版本进行区分。

**避免冲突。**两个更改可以触及同一个spec文件而不发生冲突，只要它们修改不同的需求。

**审阅效率。**审阅者看到的是变化，而不是不变的上下文。专注于重要的事情。

**棕地适应。**大多数工作都会修改现有行为。 Delta 使修改成为一流的，而不是事后才想到的。

## 模式

模式定义工作流的工件类型及其依赖关系。

### 模式如何工作

```yaml
# openspec/schemas/spec-driven/schema.yaml
name: spec-driven
artifacts:
  - id: proposal
    generates: proposal.md
    requires: []              # No dependencies, can create first

  - id: specs
    generates: specs/**/*.md
    requires: [proposal]      # Needs proposal before creating

  - id: design
    generates: design.md
    requires: [proposal]      # Can create in parallel with specs

  - id: tasks
    generates: tasks.md
    requires: [specs, design] # Needs both specs and design first
```

**工件形成依赖图：**

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

**依赖关系是推动者，而不是门。**它们显示了可以创建的内容，而不是接下来必须创建的内容。如果不需要，可以跳过设计。您可以在设计之前或之后创建规格——两者都仅取决于提案。

### 内置模式

**规格驱动**（默认）

规范驱动开发的标准工作流程：

```
proposal → specs → design → tasks → implement
```

最适合：大多数功能都可以在您希望在实施之前就规格达成一致的情况下使用。

### 自定义模式

为您的团队工作流程创建自定义架构：

```bash
# Create from scratch
openspec schema init research-first

# Or fork an existing one
openspec schema fork spec-driven research-first
```

**自定义架构示例：**

```yaml
# openspec/schemas/research-first/schema.yaml
name: research-first
artifacts:
  - id: research
    generates: research.md
    requires: []           # Do research first

  - id: proposal
    generates: proposal.md
    requires: [research]   # Proposal informed by research

  - id: tasks
    generates: tasks.md
    requires: [proposal]   # Skip specs/design, go straight to tasks
```

有关创建和使用自定义架构的完整详细信息，请参阅[自定义](<OpenSpec 项目配置与工作流定制.md>)。

## 存档

归档通过将其增量规范合并到主要规范中并保留历史更改来完成更改。

### 存档时会发生什么

```
Before archive:

openspec/
├── specs/
│   └── auth/
│       └── spec.md ◄────────────────┐
└── changes/                         │
    └── add-2fa/                     │
        ├── proposal.md              │
        ├── design.md                │ merge
        ├── tasks.md                 │
        └── specs/                   │
            └── auth/                │
                └── spec.md ─────────┘


After archive:

openspec/
├── specs/
│   └── auth/
│       └── spec.md        # Now includes 2FA requirements
└── changes/
    └── archive/
        └── 2025-01-24-add-2fa/    # Preserved for history
            ├── proposal.md
            ├── design.md
            ├── tasks.md
            └── specs/
                └── auth/
                    └── spec.md
```

### 存档过程

1. **合并增量。** 每个增量规格部分 (ADDED/MODIFIED/REMOVED) 应用于相应的主规格。

2. **移至存档。** 更改文件夹移至 `changes/archive/`，并带有日期前缀以按时间顺序排序。

3. **保留上下文。**所有工件在存档中保持完整。您始终可以回顾过去以了解为何进行更改。

### 为什么存档很重要

**干净状态。**活动更改(`changes/`)仅显示正在进行的工作。已完成的工作将移开。

**审计跟踪。**存档保留了每次更改的完整上下文 - 不仅包括更改内容，还包括解释原因的提案、解释如何更改的设计以及显示已完成工作的任务。

**规格演变。**规格随着更改的归档而有机增长。每个档案都会合并其增量，随着时间的推移建立一个全面的规范。

## 这一切是如何结合在一起的

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              OPENSPEC FLOW                                   │
│                                                                              │
│   ┌────────────────┐                                                         │
│   │  1. START      │  /opsx:propose (core) or /opsx:new (expanded)           │
│   │     CHANGE     │                                                         │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  2. CREATE     │  /opsx:ff or /opsx:continue (expanded workflow)         │
│   │     ARTIFACTS  │  Creates proposal → specs → design → tasks              │
│   │                │  (based on schema dependencies)                         │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  3. IMPLEMENT  │  /opsx:apply                                            │
│   │     TASKS      │  Work through tasks, checking them off                  │
│   │                │◄──── Update artifacts as you learn                      │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  4. VERIFY     │  /opsx:verify (optional)                                │
│   │     WORK       │  Check implementation matches specs                     │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐     ┌──────────────────────────────────────────────┐    │
│   │  5. ARCHIVE    │────►│  Delta specs merge into main specs           │    │
│   │     CHANGE     │     │  Change folder moves to archive/             │    │
│   └────────────────┘     │  Specs are now the updated source of truth   │    │
│                          └──────────────────────────────────────────────┘    │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

**良性循环：**

1. 规范描述了当前的行为
2. 变更建议修改（作为增量）
3. 实施使改变成为现实
4. 存档将增量合并到规格中
5. 规范现在描述了新的行为
6. 下一次更改基于更新的规格

## 词汇表

|期限|定义|
|------|------------|
|**神器**|变更中的文档（提案、设计、任务或增量规范）|
|**存档**|完成变更并将其增量合并到主要规范的过程|
|**更改**|对系统的拟议修改，打包为包含工件的文件夹|
|**Delta规格**|描述相对于当前规范的更改 (ADDED/MODIFIED/REMOVED) 的规范|
|**域名**|规格的逻辑分组（例如，`auth/`、`payments/`）|
|**要求**|系统必须具有的特定行为|
|**场景**|需求的具体示例，通常采用 Given/When/Then 格式|
|**架构**|工件类型及其依赖关系的定义|
|**规格**|描述系统行为的规范，包含需求和场景|
|**真相来源**|`openspec/specs/`目录，包含当前商定的行为|

## 下一步

- [入门](<OpenSpec 入门与首个变更.md>) - 实用的第一步
- [工作流程](<OpenSpec 快速与扩展工作流.md>) - 常见模式以及何时使用每种模式
- [命令](<OpenSpec AI 聊天命令参考.md>) - 完整命令参考
- [自定义](<OpenSpec 项目配置与工作流定制.md>) - 创建自定义架构并配置您的项目


## 原文对照

<details>
<summary>展开完整原文</summary>

# Concepts

This guide explains the core ideas behind OpenSpec and how they fit together. For practical usage, see [Getting Started](<OpenSpec 入门与首个变更.md>) and [Workflows](<OpenSpec 快速与扩展工作流.md>).

## Philosophy

OpenSpec is built around four principles:

```
fluid not rigid         — no phase gates, work on what makes sense
iterative not waterfall — learn as you build, refine as you go
easy not complex        — lightweight setup, minimal ceremony
brownfield-first        — works with existing codebases, not just greenfield
```

### Why These Principles Matter

**Fluid not rigid.** Traditional spec systems lock you into phases: first you plan, then you implement, then you're done. OpenSpec is more flexible — you can create artifacts in any order that makes sense for your work.

**Iterative not waterfall.** Requirements change. Understanding deepens. What seemed like a good approach at the start might not hold up after you see the codebase. OpenSpec embraces this reality.

**Easy not complex.** Some spec frameworks require extensive setup, rigid formats, or heavyweight processes. OpenSpec stays out of your way. Initialize in seconds, start working immediately, customize only if you need to.

**Brownfield-first.** Most software work isn't building from scratch — it's modifying existing systems. OpenSpec's delta-based approach makes it easy to specify changes to existing behavior, not just describe new systems.

## The Big Picture

OpenSpec organizes your work into two main areas:

```
┌────────────────────────────────────────────────────────────────────┐
│                        openspec/                                   │
│                                                                    │
│   ┌─────────────────────┐      ┌───────────────────────────────┐   │
│   │       specs/        │      │         changes/              │   │
│   │                     │      │                               │   │
│   │  Source of truth    │◄─────│  Proposed modifications       │   │
│   │  How your system    │ merge│  Each change = one folder     │   │
│   │  currently works    │      │  Contains artifacts + deltas  │   │
│   │                     │      │                               │   │
│   └─────────────────────┘      └───────────────────────────────┘   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

**Specs** are the source of truth — they describe how your system currently behaves.

**Changes** are proposed modifications — they live in separate folders until you're ready to merge them.

This separation is key. You can work on multiple changes in parallel without conflicts. You can review a change before it affects the main specs. And when you archive a change, its deltas merge cleanly into the source of truth.

## Specs

Specs describe your system's behavior using structured requirements and scenarios.

### Structure

```
openspec/specs/
├── auth/
│   └── spec.md           # Authentication behavior
├── payments/
│   └── spec.md           # Payment processing
├── notifications/
│   └── spec.md           # Notification system
└── ui/
    └── spec.md           # UI behavior and themes
```

Organize specs by domain — logical groupings that make sense for your system. Common patterns:

- **By feature area**: `auth/`, `payments/`, `search/`
- **By component**: `api/`, `frontend/`, `workers/`
- **By bounded context**: `ordering/`, `fulfillment/`, `inventory/`

### Spec Format

A spec contains requirements, and each requirement has scenarios:

```markdown
# Auth Specification

## Purpose
Authentication and session management for the application.

## Requirements

### Requirement: User Authentication
The system SHALL issue a JWT token upon successful login.

#### Scenario: Valid credentials
- GIVEN a user with valid credentials
- WHEN the user submits login form
- THEN a JWT token is returned
- AND the user is redirected to dashboard

#### Scenario: Invalid credentials
- GIVEN invalid credentials
- WHEN the user submits login form
- THEN an error message is displayed
- AND no token is issued

### Requirement: Session Expiration
The system MUST expire sessions after 30 minutes of inactivity.

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 30 minutes pass without activity
- THEN the session is invalidated
- AND the user must re-authenticate
```

**Key elements:**

| Element | Purpose |
|---------|---------|
| `## Purpose` | High-level description of this spec's domain |
| `### Requirement:` | A specific behavior the system must have |
| `#### Scenario:` | A concrete example of the requirement in action |
| SHALL/MUST/SHOULD | RFC 2119 keywords indicating requirement strength |

### Why Structure Specs This Way

**Requirements are the "what"** — they state what the system should do without specifying implementation.

**Scenarios are the "when"** — they provide concrete examples that can be verified. Good scenarios:
- Are testable (you could write an automated test for them)
- Cover both happy path and edge cases
- Use Given/When/Then or similar structured format

**RFC 2119 keywords** (SHALL, MUST, SHOULD, MAY) communicate intent:
- **MUST/SHALL** — absolute requirement
- **SHOULD** — recommended, but exceptions exist
- **MAY** — optional

### What a Spec Is (and Is Not)

A spec is a **behavior contract**, not an implementation plan.

Good spec content:
- Observable behavior users or downstream systems rely on
- Inputs, outputs, and error conditions
- External constraints (security, privacy, reliability, compatibility)
- Scenarios that can be tested or explicitly validated

Avoid in specs:
- Internal class/function names
- Library or framework choices
- Step-by-step implementation details
- Detailed execution plans (those belong in `design.md` or `tasks.md`)

Quick test:
- If implementation can change without changing externally visible behavior, it likely does not belong in the spec.

### Keep It Lightweight: Progressive Rigor

OpenSpec aims to avoid bureaucracy. Use the lightest level that still makes the change verifiable.

**Lite spec (default):**
- Short behavior-first requirements
- Clear scope and non-goals
- A few concrete acceptance checks

**Full spec (for higher risk):**
- Cross-team or cross-repo changes
- API/contract changes, migrations, security/privacy concerns
- Changes where ambiguity is likely to cause expensive rework

Most changes should stay in Lite mode.

### Human + Agent Collaboration

In many teams, humans explore and agents draft artifacts. The intended loop is:

1. Human provides intent, context, and constraints.
2. Agent converts this into behavior-first requirements and scenarios.
3. Agent keeps implementation detail in `design.md` and `tasks.md`, not `spec.md`.
4. Validation confirms structure and clarity before implementation.

This keeps specs readable for humans and consistent for agents.

## Changes

A change is a proposed modification to your system, packaged as a folder with everything needed to understand and implement it.

### Change Structure

```
openspec/changes/add-dark-mode/
├── proposal.md           # Why and what
├── design.md             # How (technical approach)
├── tasks.md              # Implementation checklist
├── .openspec.yaml        # Change metadata (optional): schema, created, skip_specs, retire_capabilities
└── specs/                # Delta specs
    └── ui/
        └── spec.md       # What's changing in ui/spec.md
```

Each change is self-contained. It has:
- **Artifacts** — documents that capture intent, design, and tasks
- **Delta specs** — specifications for what's being added, modified, or removed
- **Metadata** — optional configuration for this specific change

### Why Changes Are Folders

Packaging a change as a folder has several benefits:

1. **Everything together.** Proposal, design, tasks, and specs live in one place. No hunting through different locations.

2. **Parallel work.** Multiple changes can exist simultaneously without conflicting. Work on `add-dark-mode` while `fix-auth-bug` is also in progress.

3. **Clean history.** When archived, changes move to `changes/archive/` with their full context preserved. You can look back and understand not just what changed, but why.

4. **Review-friendly.** A change folder is easy to review — open it, read the proposal, check the design, see the spec deltas.

## Artifacts

Artifacts are the documents within a change that guide the work.

### The Artifact Flow

```
proposal ──────► specs ──────► design ──────► tasks ──────► implement
    │               │             │              │
   why            what           how          steps
 + scope        changes       approach      to take
```

Artifacts build on each other. Each artifact provides context for the next.

### Artifact Types

#### Proposal (`proposal.md`)

The proposal captures **intent**, **scope**, and **approach** at a high level.

```markdown
# Proposal: Add Dark Mode

## Intent
Users have requested a dark mode option to reduce eye strain
during nighttime usage and match system preferences.

## Scope
In scope:
- Theme toggle in settings
- System preference detection
- Persist preference in localStorage

Out of scope:
- Custom color themes (future work)
- Per-page theme overrides

## Approach
Use CSS custom properties for theming with a React context
for state management. Detect system preference on first load,
allow manual override.
```

**When to update the proposal:**
- Scope changes (narrowing or expanding)
- Intent clarifies (better understanding of the problem)
- Approach fundamentally shifts

#### Specs (delta specs in `specs/`)

Delta specs describe **what's changing** relative to the current specs. See [Delta Specs](<#delta-specs>) below.

#### Design (`design.md`)

The design captures **technical approach** and **architecture decisions**.

````markdown
# Design: Add Dark Mode

## Technical Approach
Theme state managed via React Context to avoid prop drilling.
CSS custom properties enable runtime switching without class toggling.

## Architecture Decisions

### Decision: Context over Redux
Using React Context for theme state because:
- Simple binary state (light/dark)
- No complex state transitions
- Avoids adding Redux dependency

### Decision: CSS Custom Properties
Using CSS variables instead of CSS-in-JS because:
- Works with existing stylesheet
- No runtime overhead
- Browser-native solution

## Data Flow
```
ThemeProvider (context)
       │
       ▼
ThemeToggle ◄──► localStorage
       │
       ▼
CSS Variables (applied to :root)
```

## File Changes
- `src/contexts/ThemeContext.tsx` (new)
- `src/components/ThemeToggle.tsx` (new)
- `src/styles/globals.css` (modified)
````

**When to update the design:**
- Implementation reveals the approach won't work
- Better solution discovered
- Dependencies or constraints change

#### Tasks (`tasks.md`)

Tasks are the **implementation checklist** — concrete steps with checkboxes.

```markdown
# Tasks

## 1. Theme Infrastructure
- [ ] 1.1 Create ThemeContext with light/dark state
- [ ] 1.2 Add CSS custom properties for colors
- [ ] 1.3 Implement localStorage persistence
- [ ] 1.4 Add system preference detection

## 2. UI Components
- [ ] 2.1 Create ThemeToggle component
- [ ] 2.2 Add toggle to settings page
- [ ] 2.3 Update Header to include quick toggle

## 3. Styling
- [ ] 3.1 Define dark theme color palette
- [ ] 3.2 Update components to use CSS variables
- [ ] 3.3 Test contrast ratios for accessibility
```

**Task best practices:**
- Group related tasks under headings
- Use hierarchical numbering (1.1, 1.2, etc.)
- Keep tasks small enough to complete in one session
- State how each task is verified (a test, command, or observable result)
- Land the tests and documentation each group's work calls for inside that group, not in a final catch-up group
- Check tasks off as you complete them

## Delta Specs

Delta specs are the key concept that makes OpenSpec work for brownfield development. They describe **what's changing** rather than restating the entire spec.

### The Format

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST support TOTP-based two-factor authentication.

#### Scenario: 2FA enrollment
- GIVEN a user without 2FA enabled
- WHEN the user enables 2FA in settings
- THEN a QR code is displayed for authenticator app setup
- AND the user must verify with a code before activation

#### Scenario: 2FA login
- GIVEN a user with 2FA enabled
- WHEN the user submits valid credentials
- THEN an OTP challenge is presented
- AND login completes only after valid OTP

## MODIFIED Requirements

### Requirement: Session Expiration
The system MUST expire sessions after 15 minutes of inactivity.
(Previously: 30 minutes)

#### Scenario: Idle timeout
- GIVEN an authenticated session
- WHEN 15 minutes pass without activity
- THEN the session is invalidated

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA. Users should re-authenticate each session.)
```

### Delta Sections

| Section | Meaning | What Happens on Archive |
|---------|---------|------------------------|
| `## ADDED Requirements` | New behavior | Appended to main spec |
| `## MODIFIED Requirements` | Changed behavior | Replaces existing requirement |
| `## REMOVED Requirements` | Deprecated behavior | Deleted from main spec; removing the last requirement retires the capability and deletes its spec file, when the change declares `retire_capabilities: true` |
| `## Purpose` | What a brand-new capability is for | Seeds the Purpose of the main spec being created; ignored when the spec already exists |

### Why Deltas Instead of Full Specs

**Clarity.** A delta shows exactly what's changing. Reading a full spec, you'd have to diff it mentally against the current version.

**Conflict avoidance.** Two changes can touch the same spec file without conflicting, as long as they modify different requirements.

**Review efficiency.** Reviewers see the change, not the unchanged context. Focus on what matters.

**Brownfield fit.** Most work modifies existing behavior. Deltas make modifications first-class, not an afterthought.

## Schemas

Schemas define the artifact types and their dependencies for a workflow.

### How Schemas Work

```yaml
# openspec/schemas/spec-driven/schema.yaml
name: spec-driven
artifacts:
  - id: proposal
    generates: proposal.md
    requires: []              # No dependencies, can create first

  - id: specs
    generates: specs/**/*.md
    requires: [proposal]      # Needs proposal before creating

  - id: design
    generates: design.md
    requires: [proposal]      # Can create in parallel with specs

  - id: tasks
    generates: tasks.md
    requires: [specs, design] # Needs both specs and design first
```

**Artifacts form a dependency graph:**

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

**Dependencies are enablers, not gates.** They show what's possible to create, not what you must create next. You can skip design if you don't need it. You can create specs before or after design — both depend only on proposal.

### Built-in Schemas

**spec-driven** (default)

The standard workflow for spec-driven development:

```
proposal → specs → design → tasks → implement
```

Best for: Most feature work where you want to agree on specs before implementation.

### Custom Schemas

Create custom schemas for your team's workflow:

```bash
# Create from scratch
openspec schema init research-first

# Or fork an existing one
openspec schema fork spec-driven research-first
```

**Example custom schema:**

```yaml
# openspec/schemas/research-first/schema.yaml
name: research-first
artifacts:
  - id: research
    generates: research.md
    requires: []           # Do research first

  - id: proposal
    generates: proposal.md
    requires: [research]   # Proposal informed by research

  - id: tasks
    generates: tasks.md
    requires: [proposal]   # Skip specs/design, go straight to tasks
```

See [Customization](<OpenSpec 项目配置与工作流定制.md>) for full details on creating and using custom schemas.

## Archive

Archiving completes a change by merging its delta specs into the main specs and preserving the change for history.

### What Happens When You Archive

```
Before archive:

openspec/
├── specs/
│   └── auth/
│       └── spec.md ◄────────────────┐
└── changes/                         │
    └── add-2fa/                     │
        ├── proposal.md              │
        ├── design.md                │ merge
        ├── tasks.md                 │
        └── specs/                   │
            └── auth/                │
                └── spec.md ─────────┘


After archive:

openspec/
├── specs/
│   └── auth/
│       └── spec.md        # Now includes 2FA requirements
└── changes/
    └── archive/
        └── 2025-01-24-add-2fa/    # Preserved for history
            ├── proposal.md
            ├── design.md
            ├── tasks.md
            └── specs/
                └── auth/
                    └── spec.md
```

### The Archive Process

1. **Merge deltas.** Each delta spec section (ADDED/MODIFIED/REMOVED) is applied to the corresponding main spec.

2. **Move to archive.** The change folder moves to `changes/archive/` with a date prefix for chronological ordering.

3. **Preserve context.** All artifacts remain intact in the archive. You can always look back to understand why a change was made.

### Why Archive Matters

**Clean state.** Active changes (`changes/`) shows only work in progress. Completed work moves out of the way.

**Audit trail.** The archive preserves the full context of every change — not just what changed, but the proposal explaining why, the design explaining how, and the tasks showing the work done.

**Spec evolution.** Specs grow organically as changes are archived. Each archive merges its deltas, building up a comprehensive specification over time.

## How It All Fits Together

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              OPENSPEC FLOW                                   │
│                                                                              │
│   ┌────────────────┐                                                         │
│   │  1. START      │  /opsx:propose (core) or /opsx:new (expanded)           │
│   │     CHANGE     │                                                         │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  2. CREATE     │  /opsx:ff or /opsx:continue (expanded workflow)         │
│   │     ARTIFACTS  │  Creates proposal → specs → design → tasks              │
│   │                │  (based on schema dependencies)                         │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  3. IMPLEMENT  │  /opsx:apply                                            │
│   │     TASKS      │  Work through tasks, checking them off                  │
│   │                │◄──── Update artifacts as you learn                      │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐                                                         │
│   │  4. VERIFY     │  /opsx:verify (optional)                                │
│   │     WORK       │  Check implementation matches specs                     │
│   └───────┬────────┘                                                         │
│           │                                                                  │
│           ▼                                                                  │
│   ┌────────────────┐     ┌──────────────────────────────────────────────┐    │
│   │  5. ARCHIVE    │────►│  Delta specs merge into main specs           │    │
│   │     CHANGE     │     │  Change folder moves to archive/             │    │
│   └────────────────┘     │  Specs are now the updated source of truth   │    │
│                          └──────────────────────────────────────────────┘    │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

**The virtuous cycle:**

1. Specs describe current behavior
2. Changes propose modifications (as deltas)
3. Implementation makes the changes real
4. Archive merges deltas into specs
5. Specs now describe the new behavior
6. Next change builds on updated specs

## Glossary

| Term | Definition |
|------|------------|
| **Artifact** | A document within a change (proposal, design, tasks, or delta specs) |
| **Archive** | The process of completing a change and merging its deltas into main specs |
| **Change** | A proposed modification to the system, packaged as a folder with artifacts |
| **Delta spec** | A spec that describes changes (ADDED/MODIFIED/REMOVED) relative to current specs |
| **Domain** | A logical grouping for specs (e.g., `auth/`, `payments/`) |
| **Requirement** | A specific behavior the system must have |
| **Scenario** | A concrete example of a requirement, typically in Given/When/Then format |
| **Schema** | A definition of artifact types and their dependencies |
| **Spec** | A specification describing system behavior, containing requirements and scenarios |
| **Source of truth** | The `openspec/specs/` directory, containing the current agreed-upon behavior |

## Next Steps

- [Getting Started](<OpenSpec 入门与首个变更.md>) - Practical first steps
- [Workflows](<OpenSpec 快速与扩展工作流.md>) - Common patterns and when to use each
- [Commands](<OpenSpec AI 聊天命令参考.md>) - Full command reference
- [Customization](<OpenSpec 项目配置与工作流定制.md>) - Create custom schemas and configure your project


</details>
