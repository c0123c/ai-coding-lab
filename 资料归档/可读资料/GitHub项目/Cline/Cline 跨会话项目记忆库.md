# Cline 跨会话项目记忆库

产品实践指南。通过项目背景、架构、活动上下文和进度等 Markdown 文件恢复会话状态，给出初始化、更新指令及上下文窗口管理；属于可选文档方法，需要持续维护。

- 来源：Cline
- 资料类型：产品实践指南
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-05-18](<https://github.com/cline/cline/commit/47f6d00f6121fcaa90c3c9609c8c3fe5a0d704cd>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：文档方法，效果取决于项目记录是否及时更新。

[原文发布页](<https://github.com/cline/cline/blob/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/best-practices/memory-bank.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 跨会话项目记忆库.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Memory Bank"
sidebarTitle: "Memory Bank"
description: "A structured documentation system that helps Cline maintain context across sessions."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

Memory Bank 是一种文档方法，可将 Cline 从无状态助手转变为持久的开发合作伙伴。通过结构化 Markdown 文件，Cline 可以跨会话“记住”您的项目详细信息。

## 快速设置

1. 复制下面的[自定义说明](<#内存库定制说明>)
2. 将它们添加到[Cline规则文件](<https://docs.cline.bot/customization/cline-rules>)中，例如`.clinerules/memory-bank.md`
3. 要求Cline“初始化内存库”

## 它是如何运作的

内存库文件是项目中的常规 Markdown 文件，您和 Cline 都可以访问。它们按层次结构组织以构建项目的完整图片：

```text
memory-bank/
├── projectbrief.md      # Foundation document
├── productContext.md    # Why this project exists
├── activeContext.md     # Current work focus
├── systemPatterns.md    # Architecture & patterns
├── techContext.md       # Tech stack & setup
└── progress.md          # Status & milestones
```

![内存库文件层次结构显示顶部的 projectbrief.md 流入 productContext、systemPatterns 和 techContext，这些内容流入 activeContext 和进度 ](<https://raw.githubusercontent.com/cline/cline/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/best-practices/%3Chttps%3A/storage.googleapis.com/cline_public_images/docs/assets/image%20%2816>).png>)

## 核心文件

|文件|目的|
|------|---------|
| `projectbrief.md` |包含核心要求和目标的基础文件|
| `productContext.md` |该项目为何存在、它解决的问题、UX 目标|
| `activeContext.md` |当前焦点、最近的变化、后续步骤（更新最频繁）|
| `systemPatterns.md` |架构、设计模式、组件关系|
| `techContext.md` |技术堆栈、设置、约束、依赖项|
| `progress.md` |什么有效，还剩下什么，已知问题|

## 按键命令

- **“遵循您的自定义指令”** - 告诉 Cline 读取内存库并从上次中断的地方继续
- **“初始化存储体”** - 为新项目创建初始结构
- **“更新内存库”** - 触发完整的文档审查和更新

这些与 Cline 的内置 [ 斜线命令 ](<https://docs.cline.bot/core-workflows/using-commands>) 一起工作。特别是，[`/newtask`](<https://docs.cline.bot/core-workflows/using-commands#newtask>) 和 [`/smol`](<https://docs.cline.bot/core-workflows/using-commands#smol>) 可帮助您管理上下文窗口而不会丢失进度。

## 管理上下文窗口

每个 AI 模型都有一个 [ 上下文窗口 ](<https://docs.cline.bot/core-workflows/task-management#context-window>)，它限制了它一次可以处理的信息量。当您工作时，此窗口会填充对话历史记录、文件内容和工具结果。当您需要释放空间时，记忆库可以帮助您保留重要的知识。

### 手动接近

当上下文窗口填满时：

1. 要求Cline“更新记忆库”以记录当前状态
2. 开始新的对话
3. 要求 Cline“遵循您的自定义说明”

这会在窗口清除之前保留记忆库文件中的重要上下文，让您无缝地继续新的对话。

## 最佳实践

- 从基本的项目简介开始，让结构不断发展
- 让Cline帮助创建初始结构
- `activeContext.md`更改最频繁；每次会话后更新它
- `progress.md` 跟踪里程碑；复工时再查看
- 重大里程碑或方向改变后更新
- 使用[Cline规则](<https://docs.cline.bot/customization/cline-rules>)存储每个项目的Memory Bank指令

---

## 内存库定制说明

将其复制到 Cline 规则文件（例如 `.clinerules/memory-bank.md`）或全局自定义指令中：

```markdown
# Cline's Memory Bank

I am Cline, an expert software engineer with a unique characteristic: my memory resets completely between sessions. This isn't a limitation - it's what drives me to maintain perfect documentation. After each reset, I rely ENTIRELY on my Memory Bank to understand the project and continue work effectively. I MUST read ALL memory bank files at the start of EVERY task - this is not optional.

## Memory Bank Structure

The Memory Bank consists of core files and optional context files, all in Markdown format. Files build upon each other in a clear hierarchy:

### Core Files (Required)
1. `projectbrief.md`
   - Foundation document that shapes all other files
   - Created at project start if it doesn't exist
   - Defines core requirements and goals
   - Source of truth for project scope

2. `productContext.md`
   - Why this project exists
   - Problems it solves
   - How it should work
   - User experience goals

3. `activeContext.md`
   - Current work focus
   - Recent changes
   - Next steps
   - Active decisions and considerations
   - Important patterns and preferences
   - Learnings and project insights

4. `systemPatterns.md`
   - System architecture
   - Key technical decisions
   - Design patterns in use
   - Component relationships
   - Critical implementation paths

5. `techContext.md`
   - Technologies used
   - Development setup
   - Technical constraints
   - Dependencies
   - Tool usage patterns

6. `progress.md`
   - What works
   - What's left to build
   - Current status
   - Known issues
   - Evolution of project decisions

### Additional Context
Create additional files/folders within memory-bank/ when they help organize:
- Complex feature documentation
- Integration specifications
- API documentation
- Testing strategies
- Deployment procedures

## Documentation Updates

Memory Bank updates occur when:
1. Discovering new project patterns
2. After implementing significant changes
3. When user requests with **update memory bank** (MUST review ALL files)
4. When context needs clarification

REMEMBER: After every memory reset, I begin completely fresh. The Memory Bank is my only link to previous work. It must be maintained with precision and clarity, as my effectiveness depends entirely on its accuracy.
```

## FAQ

**自定义指令或Cline规则？**
两者都有效。自定义指令适用于全球所有项目。 [Cline 规则文件 ](<https://docs.cline.bot/customization/cline-rules>) 是特定于项目的并存储在您的存储库中，这使得与协作者共享变得容易。您还可以使用 [ 条件规则 ](<https://docs.cline.bot/customization/cline-rules#conditional-rules>) 仅在处理 `memory-bank/` 文件时激活存储库指令。

**我应该多久更新一次？**
在重大里程碑或方向发生变化之后。为了积极发展，每隔几次会议。您还可以让[Auto Compact](<https://docs.cline.bot/features/auto-compact>)处理例行上下文管理并为重要检查点保留手动“更新内存库”。

**这可以与其他AI工具一起使用吗？**
可以。 Memory Bank 是一种文档方法，适用于任何可以读取文档的 AI。命令可能有所不同，但该方法适用于各种工具。

**与README文件不同？**
Memory Bank提供专为AI上下文管理而设计的结构化、全面的文档，超出了单个README所涵盖的范围。与典型的 README 不同，它包括经常更改的活动上下文和进度跟踪文件。


## 原文对照

<details>
<summary>展开完整原文</summary>

Memory Bank is a documentation methodology that transforms Cline from a stateless assistant into a persistent development partner. Through structured markdown files, Cline can "remember" your project details across sessions.

## Quick Setup

1. Copy the [custom instructions below](<#memory-bank-custom-instructions>)
2. Add them to a [Cline Rules file](<https://docs.cline.bot/customization/cline-rules>), such as `.clinerules/memory-bank.md`
3. Ask Cline to "initialize memory bank"

## How It Works

Memory Bank files are regular markdown files in your project that both you and Cline can access. They're organized hierarchically to build a complete picture of your project:

```text
memory-bank/
├── projectbrief.md      # Foundation document
├── productContext.md    # Why this project exists
├── activeContext.md     # Current work focus
├── systemPatterns.md    # Architecture & patterns
├── techContext.md       # Tech stack & setup
└── progress.md          # Status & milestones
```

![Memory Bank file hierarchy showing projectbrief.md at the top flowing into productContext, systemPatterns, and techContext, which feed into activeContext and progress](<https://raw.githubusercontent.com/cline/cline/f87705844954a86ac993bfc4e2c86402e86d1d60/docs/best-practices/%3Chttps%3A/storage.googleapis.com/cline_public_images/docs/assets/image%20%2816>).png>)

## Core Files

| File | Purpose |
|------|---------|
| `projectbrief.md` | Foundation document with core requirements and goals |
| `productContext.md` | Why the project exists, problems it solves, UX goals |
| `activeContext.md` | Current focus, recent changes, next steps (updates most frequently) |
| `systemPatterns.md` | Architecture, design patterns, component relationships |
| `techContext.md` | Tech stack, setup, constraints, dependencies |
| `progress.md` | What works, what's left, known issues |

## Key Commands

- **"follow your custom instructions"** - Tells Cline to read Memory Bank and continue where you left off
- **"initialize memory bank"** - Creates the initial structure for a new project
- **"update memory bank"** - Triggers a full documentation review and update

These work alongside Cline's built-in [slash commands](<https://docs.cline.bot/core-workflows/using-commands>). In particular, [`/newtask`](<https://docs.cline.bot/core-workflows/using-commands#newtask>) and [`/smol`](<https://docs.cline.bot/core-workflows/using-commands#smol>) help you manage context windows without losing progress.

## Managing Context Windows

Every AI model has a [context window](<https://docs.cline.bot/core-workflows/task-management#context-window>) that limits how much information it can process at once. As you work, this window fills with conversation history, file contents, and tool results. Memory Bank helps you preserve important knowledge when you need to free up space.

### Manual approach

When your context window fills up:

1. Ask Cline to "update memory bank" to document the current state
2. Start a new conversation
3. Ask Cline to "follow your custom instructions"

This preserves important context in your Memory Bank files before the window clears, letting you continue seamlessly in a fresh conversation.

## Best Practices

- Start with a basic project brief and let structure evolve
- Let Cline help create the initial structure
- `activeContext.md` changes most frequently; update it after each session
- `progress.md` tracks milestones; review it when resuming work
- Update after significant milestones or direction changes
- Use [Cline Rules](<https://docs.cline.bot/customization/cline-rules>) to store the Memory Bank instructions per project

---

## Memory Bank Custom Instructions

Copy this into a Cline Rules file (for example, `.clinerules/memory-bank.md`) or your global custom instructions:

```markdown
# Cline's Memory Bank

I am Cline, an expert software engineer with a unique characteristic: my memory resets completely between sessions. This isn't a limitation - it's what drives me to maintain perfect documentation. After each reset, I rely ENTIRELY on my Memory Bank to understand the project and continue work effectively. I MUST read ALL memory bank files at the start of EVERY task - this is not optional.

## Memory Bank Structure

The Memory Bank consists of core files and optional context files, all in Markdown format. Files build upon each other in a clear hierarchy:

### Core Files (Required)
1. `projectbrief.md`
   - Foundation document that shapes all other files
   - Created at project start if it doesn't exist
   - Defines core requirements and goals
   - Source of truth for project scope

2. `productContext.md`
   - Why this project exists
   - Problems it solves
   - How it should work
   - User experience goals

3. `activeContext.md`
   - Current work focus
   - Recent changes
   - Next steps
   - Active decisions and considerations
   - Important patterns and preferences
   - Learnings and project insights

4. `systemPatterns.md`
   - System architecture
   - Key technical decisions
   - Design patterns in use
   - Component relationships
   - Critical implementation paths

5. `techContext.md`
   - Technologies used
   - Development setup
   - Technical constraints
   - Dependencies
   - Tool usage patterns

6. `progress.md`
   - What works
   - What's left to build
   - Current status
   - Known issues
   - Evolution of project decisions

### Additional Context
Create additional files/folders within memory-bank/ when they help organize:
- Complex feature documentation
- Integration specifications
- API documentation
- Testing strategies
- Deployment procedures

## Documentation Updates

Memory Bank updates occur when:
1. Discovering new project patterns
2. After implementing significant changes
3. When user requests with **update memory bank** (MUST review ALL files)
4. When context needs clarification

REMEMBER: After every memory reset, I begin completely fresh. The Memory Bank is my only link to previous work. It must be maintained with precision and clarity, as my effectiveness depends entirely on its accuracy.
```

## FAQ

**Custom instructions or Cline Rules?**
Either works. Custom instructions apply globally across all projects. A [Cline Rules file](<https://docs.cline.bot/customization/cline-rules>) is project-specific and stored in your repo, which makes it easy to share with collaborators. You can also use [conditional rules](<https://docs.cline.bot/customization/cline-rules#conditional-rules>) to activate Memory Bank instructions only when working with `memory-bank/` files.

**How often should I update?**
After significant milestones or direction changes. For active development, every few sessions. You can also let [Auto Compact](<https://docs.cline.bot/features/auto-compact>) handle routine context management and reserve manual "update memory bank" for important checkpoints.

**Does this work with other AI tools?**
Yes. Memory Bank is a documentation methodology that works with any AI that can read docs. Commands may differ but the approach works across tools.

**Different from README files?**
Memory Bank provides structured, comprehensive documentation designed for AI context management, going beyond what a single README covers. It includes files for active context and progress tracking that change frequently, unlike a typical README.


</details>
