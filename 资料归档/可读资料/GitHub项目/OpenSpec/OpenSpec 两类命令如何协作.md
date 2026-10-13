# OpenSpec 两类命令如何协作

说明终端 CLI 和 AI 聊天指令各自的职责、不同工具的调用语法、生成的 Skill 与命令文件，以及安装确认步骤。

- 来源：OpenSpec
- 资料类型：命令机制与调用指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/how-commands-work.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/how-commands-work.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 命令如何工作

**需要知道的一件事：OpenSpec有两种命令，它们在两个不同的地方运行。**

- `openspec ...` 命令在您的 ** 终端 ** 中运行。 （示例：`openspec init`。）
- `/opsx:...` 命令在 **AI 助理的聊天 ** 中运行。 （示例：`/opsx:propose`。）

如果您在终端中输入 `/opsx:propose` 但没有任何反应，此页面就是原因。你正在与错误的一半OpenSpec交谈。斜杠命令不是终端命令。它们是您向 AI 编码助手提供的说明，在同一个聊天框中您通常会键入“添加登录表单”。

这种单一的区别是新用户最常见的障碍，所以让我们把它说得一清二楚。

## 两半

OpenSpec 是一个身兼两职的项目。

** CLI（终端一半）。** 一个名为 `openspec` 的程序，您可以从 shell 安装并运行该程序。它设置您的项目、列出并验证更改、显示仪表板并存档已完成的工作。您可以将这些输入到 iTerm、VS Code 终端、PowerShell、任何运行 `git` 或 `npm` 的地方。

```bash
openspec init        # set up OpenSpec in this project
openspec list        # see active changes
openspec view        # open the interactive dashboard
```

**斜杠命令（聊天一半）。** 您在 AI 助手中输入的简短命令，例如 `/opsx:propose` 和 `/opsx:apply`。这些告诉 AI 遵循 OpenSpec 工作流程：起草提案、编写规格、从任务列表构建、完成后存档。您可以将这些输入到 Claude Code、Cursor、Devin Desktop、Copilot 或您使用的任何助手中。

```text
/opsx:propose add-dark-mode    (typed in your AI chat)
/opsx:apply                    (typed in your AI chat)
/opsx:archive                  (typed in your AI chat)
```

这是一张图片中的心理模型：

```text
        YOUR TERMINAL                         YOUR AI ASSISTANT'S CHAT
   ┌──────────────────────┐               ┌──────────────────────────────┐
   │  $ openspec init     │   installs    │  /opsx:propose add-dark-mode  │
   │  $ openspec list     │  ──────────►  │  /opsx:apply                  │
   │  $ openspec view     │   commands    │  /opsx:archive                │
   └──────────────────────┘    & skills   └──────────────────────────────┘
        run openspec here                       run /opsx:* here
```

注意箭头。在终端中运行 `openspec init` 就是 * 将 * 斜杠命令安装到 AI 工具中。终端一半设置聊天一半。此后，日常驾驶大部分都在聊天中进行。

## “如何启动交互模式？”

**没有单独的交互模式可以启动。**这个问题出现了很多次，所以值得一个简单的答案。

您没有进入特殊的OpenSpec模式。您只需像往常一样打开 AI 编码助手，然后在聊天中输入斜杠命令即可。斜杠命令*是*“输入”OpenSpec的方式。您的助手会识别它，加载匹配的 OpenSpec 技能，并开始遵循工作流程。

所以真正的指令是：

1. 在项目中打开 AI 编码助手（Claude Code、Cursor、Devin Desktop 等）。
2. 在其聊天中输入 `/opsx:propose`，与您输入任何其他请求的位置相同。
3. 观看自动完成：如果安装了 OpenSpec，您将在键入斜杠时看到 `/opsx:propose`、`/opsx:apply` 和朋友。

就是这样。没有模式可以切换，没有守护程序可以启动，没有单独的窗口。

\* 是 * 真正交互的一件事存在于终端中：`openspec view`。它会打开一个仪表板来浏览您的规格和更改。但这是一个查看器，而不是您提出和构建的东西。建造是通过聊天中的斜杠命令进行的。

## 为什么存在这种分裂

它值得理解，因为它解释了为什么 OpenSpec 与 30 + 不同的 AI 工具一起使用。

CLI 是 ** 引擎 **。它知道规则：更改文件夹是什么样子，哪些工件依赖于哪些工件，如何将增量规范合并到您的事实来源中。到处都一样。

斜线命令是**方向盘**，每个AI工具都有一个稍微不同的工具。 Claude Code 将它们称为命令。 Cursor 和 Devin Desktop 有自己的格式。有些工具称之为技能。当您运行 `openspec init` 时，OpenSpec 会为您选择的每个工具生成正确类型的文件，因此无论您喜欢哪个助手，相同的 `/opsx:propose` 意图都会起作用。

这种设计的优势在于：您只需学习一次工作流程即可跨工具使用。权衡：命令的确切语法在工具之间可能略有不同，这是下一节。

## 按工具列出的斜杠命令语法

各地的意图都是相同的。拼写遵循您的工具加载的文件。

|您的工具的命令文件|你如何输入|示例工具|
|--------------------------|-----------------|---------------|
| `.../commands/opsx/<id>.*` | `/opsx:propose` |Claude Code、Gemini CLI、粉碎|
| `.../opsx-<id>.*` | `/opsx-propose` |Cursor、GitHub Copilot (IDE)、Devin 桌面、Trae、Oh My Pi|
| `.amazonq/prompts/opsx-<id>.md` | `@opsx-propose` | Amazon Q Developer |
|无 — 仅技能| `/openspec-propose` |CodeArts、ForgeCode、爱马仕、Mistral Vibe、Zed Agent、共享 `.agents`|
|无 — Kimi 代码| `/skill:openspec-propose` |基米密码|
|无 — Codex CLI| `$openspec-propose` | Codex |

Devin 是一款跨两行的工具。 Devin Desktop 读取为 
`.devin/workflows/`，因此 `/opsx-propose` 在那里工作； [Devin Local 确实是
而不是](<https://docs.devin.ai/desktop/devin-local>)，所以agent使用
`/openspec-propose`技能来代替。写入到 
`.devin/skills/` 的技能 OpenSpec 对两者都有效，这就是为什么它们通过技能 
 名称相互引用。

每个工具都列在[如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)中——那个
表是权威的。两行根本不是斜杠命令：Amazon Q
 将其文件加载到使用 `@` 调用的提示库中，最后三行 
 使用 * 技能 * 名称，这不是命令 ID（`/opsx:apply` 是
`openspec-apply-change`技能）。

如有疑问，请阅读打印的“入门”行 `openspec init`：它已经
 使用您注册的工具的形式。对于显示斜杠命令的工具来说，输入斜杠并观察自动完成
也有效。

## 命令如何到达那里：技能和命令

当您运行 `openspec init`（或 `openspec update`）时，OpenSpec 会将小文件写入您的项目，以便您的 AI 工具可以找到工作流程。根据您的工具和设置，这些是 ** 技能 **、** 命令 ** 或两者。

- **技能**住在`.claude/skills/openspec-*/SKILL.md`这样的地方。它们是新兴的跨工具标准：助手自动检测的指令文件夹。
- **命令**位于`.cursor/commands/opsx-<id>.md`或`.claude/commands/opsx/<id>.md`等地方 - 布局是工具的，它决定您如何键入命令。它们是较旧的每个工具斜杠命令文件。 Codex没有获取生成的命令文件；使用`.agents/skills/openspec-*`。

您不必关心您的工具使用哪一种。您只需输入斜线命令即可运行。但是，当出现问题时，了解这些文件的存在会有所帮助：如果您的命令消失，通常意味着这些文件丢失或过时，并且 `openspec update` 会重新生成它们。

请参阅 [ 支持的工具 ](<OpenSpec 支持的 AI 工具与调用方式.md>) 了解每个工具的确切路径，并参阅 [ 迁移指南 ](<从旧版 OpenSpec 工作流迁移到 OPSX.md>) 了解技能如何取代旧的仅命令方法。

## 确认已安装

快速检查，最快优先：

1. **在 AI 聊天中输入斜杠。** 开始输入 `/opsx` 并注意自动完成建议。如果它们出现，你就准备好了。在仅限技能的工具上（Codex、Kimi Code、CodeArts、ForgeCode、Hermes、Mistral Vibe、Zed Agent 或共享 `.agents` 目标）`/opsx` 甚至在健康的安装 - 请尝试使用上表中的技能名称。
2. **查找文件。** 对于 Claude Code，检查 `.claude/skills/` 是否包含 `openspec-*` 文件夹。其他工具使用自己的目录（[支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)列出了它们）。
3. **重新运行安装程序。**从项目根目录运行`openspec update`。这将为您配置的任何工具重新生成技能和命令文件。
4. **重新启动助手。**许多工具在启动时扫描技能和命令，因此可能缺少一个新窗口。

## 我有哪些命令？

默认情况下，OpenSpec 安装 **core** 斜线命令集：

- `/opsx:explore`：在做出改变之前用AI思考一个想法（当你不确定时这是很好的第一步）
- `/opsx:propose`：一步创建变更并起草其所有规划工件
- `/opsx:apply`：通过处理其任务列表来构建变更
- `/opsx:update`：修改变更的计划工件并保持它们的连贯性
- `/opsx:sync`：将变更的规格更新合并到您的主要规格中（通常是自动的）
- `/opsx:archive`：完成更改并将其归档

一个好的默认节奏：`explore`，当你想知道要做什么时，然后是`propose`，`apply`，`archive`。 [首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)指南解释了为什么这个开始步骤会得到回报。

还有一个 ** 扩展 ** 套装，适合那些想要更精细控制的人（`/opsx:new`、`/opsx:continue`、`/opsx:ff`、`/opsx:verify`、`/opsx:bulk-archive`、 `/opsx:onboard`）。您可以使用`openspec config profile`将其打开，然后使用`openspec update`应用它。

这一切都是新的吗？ `/opsx:onboard`（在扩展集中）将引导您完成对自己的代码库的完整更改，并叙述每个步骤。这是最友好的介绍。

有关每个命令的详细信息，请参阅[命令](<OpenSpec AI 聊天命令参考.md>)。有关何时到达哪个，请参阅[工作流程](<OpenSpec 快速与扩展工作流.md>)。

## 干净的第一次运行

把它们放在一起，这就是整个序列，每个步骤都标有发生的位置。

```text
TERMINAL   $ npm install -g @fission-ai/openspec@latest
TERMINAL   $ cd your-project
TERMINAL   $ openspec init
              (installs slash commands into your AI tool)

AI CHAT      /opsx:explore
              (optional: think the idea through with the AI first)

AI CHAT      /opsx:propose add-dark-mode
              (AI drafts proposal, specs, design, tasks)

AI CHAT      /opsx:apply
              (AI builds it, checking off tasks)

AI CHAT      /opsx:archive
              (change is merged into your specs and filed away)
```

需要设置两个终端步骤。然后你们就生活在聊天中。这就是节奏。

## 相关

- [入门](<OpenSpec 入门与首个变更.md>)：完整的首次更改演练
- [命令](<OpenSpec AI 聊天命令参考.md>)：每个斜杠命令的详细信息
- [CLI](<OpenSpec 命令行参考.md>)：每个终端命令详细信息
- [支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)：每个工具的语法和文件位置
- [FAQ](<OpenSpec 常见问题.md>)：更快速的答案
- [故障排除](<OpenSpec 安装与变更排障.md>)：修复命令不显示的问题


## 原文对照

<details>
<summary>展开完整原文</summary>

# How Commands Work

**The one thing to know: OpenSpec has two kinds of commands, and they run in two different places.**

- `openspec ...` commands run in your **terminal**. (Example: `openspec init`.)
- `/opsx:...` commands run in your **AI assistant's chat**. (Example: `/opsx:propose`.)

If you ever type `/opsx:propose` into your terminal and nothing happens, this page is why. You are talking to the wrong half of OpenSpec. Slash commands are not terminal commands. They are instructions you give to your AI coding assistant, in the same chat box where you'd normally type "add a login form."

That single distinction is the most common stumbling block for new users, so let's make it crystal clear.

## The two halves

OpenSpec is one project wearing two hats.

**The CLI (terminal half).** A program named `openspec` that you install and run from your shell. It sets up your project, lists and validates changes, shows a dashboard, and archives finished work. You type these into iTerm, the VS Code terminal, PowerShell, anywhere you'd run `git` or `npm`.

```bash
openspec init        # set up OpenSpec in this project
openspec list        # see active changes
openspec view        # open the interactive dashboard
```

**The slash commands (chat half).** Short commands like `/opsx:propose` and `/opsx:apply` that you type into your AI assistant. These tell the AI to follow the OpenSpec workflow: draft a proposal, write specs, build from the task list, archive when done. You type these into Claude Code, Cursor, Devin Desktop, Copilot, or whichever assistant you use.

```text
/opsx:propose add-dark-mode    (typed in your AI chat)
/opsx:apply                    (typed in your AI chat)
/opsx:archive                  (typed in your AI chat)
```

Here's the mental model in one picture:

```text
        YOUR TERMINAL                         YOUR AI ASSISTANT'S CHAT
   ┌──────────────────────┐               ┌──────────────────────────────┐
   │  $ openspec init     │   installs    │  /opsx:propose add-dark-mode  │
   │  $ openspec list     │  ──────────►  │  /opsx:apply                  │
   │  $ openspec view     │   commands    │  /opsx:archive                │
   └──────────────────────┘    & skills   └──────────────────────────────┘
        run openspec here                       run /opsx:* here
```

Notice the arrow. Running `openspec init` in your terminal is what *installs* the slash commands into your AI tool. The terminal half sets up the chat half. After that, day-to-day driving mostly happens in chat.

## "How do I start interactive mode?"

**There is no separate interactive mode to start.** This question comes up a lot, so it deserves a plain answer.

You don't enter a special OpenSpec mode. You just open your AI coding assistant like you always do, and type a slash command into the chat. The slash command *is* how you "enter" OpenSpec. Your assistant recognizes it, loads the matching OpenSpec skill, and starts following the workflow.

So the real instructions are:

1. Open your AI coding assistant (Claude Code, Cursor, Devin Desktop, and so on) in your project.
2. Type `/opsx:propose` in its chat, the same place you type any other request.
3. Watch the autocomplete: if OpenSpec is installed, you'll see `/opsx:propose`, `/opsx:apply`, and friends appear as you type the slash.

That's it. No mode to toggle, no daemon to launch, no separate window.

One thing that *is* genuinely interactive lives in the terminal: `openspec view`. It opens a dashboard for browsing your specs and changes. But that's a viewer, not the thing you propose and build with. The building happens through slash commands in chat.

## Why this split exists

It's worth understanding, because it explains why OpenSpec works with 30+ different AI tools.

The CLI is the **engine**. It knows the rules: what a change folder looks like, which artifacts depend on which, how to merge a delta spec into your source of truth. It's the same everywhere.

The slash commands are the **steering wheel**, and every AI tool has a slightly different one. Claude Code calls them commands. Cursor and Devin Desktop have their own formats. Some tools call them skills. When you run `openspec init`, OpenSpec generates the right kind of file for each tool you selected, so the same `/opsx:propose` intent works no matter which assistant you prefer.

The strength of this design: you learn the workflow once and carry it across tools. The tradeoff: the exact syntax of a command can differ slightly between tools, which is the next section.

## Slash command syntax by tool

The intent is identical everywhere. The spelling follows the file your tool loads.

| Your tool's command file | How you type it | Example tools |
|--------------------------|-----------------|---------------|
| `.../commands/opsx/<id>.*` | `/opsx:propose` | Claude Code, Gemini CLI, Crush |
| `.../opsx-<id>.*` | `/opsx-propose` | Cursor, GitHub Copilot (IDE), Devin Desktop, Trae, Oh My Pi |
| `.amazonq/prompts/opsx-<id>.md` | `@opsx-propose` | Amazon Q Developer |
| none — skills only | `/openspec-propose` | CodeArts, ForgeCode, Hermes, Mistral Vibe, Zed Agent, shared `.agents` |
| none — Kimi Code | `/skill:openspec-propose` | Kimi Code |
| none — Codex CLI | `$openspec-propose` | Codex |

Devin is the one tool that spans two rows. Devin Desktop reads
`.devin/workflows/`, so `/opsx-propose` works there; [Devin Local does
not](<https://docs.devin.ai/desktop/devin-local>), so on that agent use the
`/openspec-propose` skill instead. The skills OpenSpec writes to
`.devin/skills/` work on both, which is why they reference each other by skill
name.

Every tool is listed in [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>) — that
table is the authoritative one. Two rows are not slash commands at all: Amazon Q
loads its files into a prompt library invoked with `@`, and the last three rows
use the *skill* name, which is not the command id (`/opsx:apply` is the
`openspec-apply-change` skill).

When in doubt, read the "Getting started" line `openspec init` printed: it already
uses the form your tools registered. Typing a slash and watching the autocomplete
works too, for the tools that surface slash commands at all.

## How the commands got there: skills and commands

When you run `openspec init` (or `openspec update`), OpenSpec writes small files into your project so your AI tool can find the workflow. Depending on your tool and settings, these are **skills**, **commands**, or both.

- **Skills** live in places like `.claude/skills/openspec-*/SKILL.md`. They're the emerging cross-tool standard: a folder of instructions your assistant auto-detects.
- **Commands** live in places like `.cursor/commands/opsx-<id>.md` or `.claude/commands/opsx/<id>.md` — the layout is the tool's, and it decides how you type the command. They're the older per-tool slash command files. Codex does not get generated command files; use `.agents/skills/openspec-*`.

You don't have to care which one your tool uses. You just type the slash command and it works. But knowing these files exist helps when something goes wrong: if your commands vanish, it usually means these files are missing or stale, and `openspec update` regenerates them.

See [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>) for the exact paths per tool, and [Migration Guide](<从旧版 OpenSpec 工作流迁移到 OPSX.md>) for how skills replaced the older command-only approach.

## Confirming it's installed

Quick checks, fastest first:

1. **Type a slash in your AI chat.** Start typing `/opsx` and watch for autocomplete suggestions. If they appear, you're set. On a skills-only tool (Codex, Kimi Code, CodeArts, ForgeCode, Hermes, Mistral Vibe, Zed Agent, or the shared `.agents` target) `/opsx` never completes even on a healthy install — try the skill name from the table above instead.
2. **Look for the files.** For Claude Code, check that `.claude/skills/` contains `openspec-*` folders. Other tools use their own directories ([Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>) lists them).
3. **Re-run setup.** From your project root, run `openspec update`. This regenerates the skill and command files for whatever tools you configured.
4. **Restart your assistant.** Many tools scan for skills and commands at startup, so a fresh window can be the missing step.

## Which commands do I even have?

By default, OpenSpec installs the **core** set of slash commands:

- `/opsx:explore`: think through an idea with the AI before committing to a change (great first step when you're unsure)
- `/opsx:propose`: create a change and draft all its planning artifacts in one step
- `/opsx:apply`: build the change by working through its task list
- `/opsx:update`: revise a change's planning artifacts and keep them coherent
- `/opsx:sync`: merge a change's spec updates into your main specs (usually automatic)
- `/opsx:archive`: finish a change and file it away

A good default rhythm: `explore` when you're figuring out what to do, then `propose`, `apply`, `archive`. The [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>) guide explains why that opening step pays off.

There's also an **expanded** set for people who want finer control (`/opsx:new`, `/opsx:continue`, `/opsx:ff`, `/opsx:verify`, `/opsx:bulk-archive`, `/opsx:onboard`). You turn it on with `openspec config profile`, then apply it with `openspec update`.

New to all of this? `/opsx:onboard` (in the expanded set) walks you through a complete change on your own codebase, narrating each step. It's the friendliest possible introduction.

For what each command does in detail, see [Commands](<OpenSpec AI 聊天命令参考.md>). For when to reach for which, see [Workflows](<OpenSpec 快速与扩展工作流.md>).

## A clean first run

Putting it together, here is the whole sequence with each step labeled by where it happens.

```text
TERMINAL   $ npm install -g @fission-ai/openspec@latest
TERMINAL   $ cd your-project
TERMINAL   $ openspec init
              (installs slash commands into your AI tool)

AI CHAT      /opsx:explore
              (optional: think the idea through with the AI first)

AI CHAT      /opsx:propose add-dark-mode
              (AI drafts proposal, specs, design, tasks)

AI CHAT      /opsx:apply
              (AI builds it, checking off tasks)

AI CHAT      /opsx:archive
              (change is merged into your specs and filed away)
```

Two terminal steps to set up. Then you live in chat. That's the rhythm.

## Related

- [Getting Started](<OpenSpec 入门与首个变更.md>): the full first-change walkthrough
- [Commands](<OpenSpec AI 聊天命令参考.md>): every slash command in detail
- [CLI](<OpenSpec 命令行参考.md>): every terminal command in detail
- [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>): per-tool syntax and file locations
- [FAQ](<OpenSpec 常见问题.md>): more quick answers
- [Troubleshooting](<OpenSpec 安装与变更排障.md>): fixes when commands don't show up


</details>
