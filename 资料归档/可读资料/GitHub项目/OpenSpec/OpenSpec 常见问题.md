# OpenSpec 常见问题

回答项目定位、已有代码库适用性、工具兼容性和命令运行位置等常见疑问，并指向排障和术语文档。

- 来源：OpenSpec
- 资料类型：常见问题文档
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/faq.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/faq.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# FAQ

快速回答人们最常问的问题。如果您的问题确实是“有问题”的问题，那么 [ 故障排除 ](<OpenSpec 安装与变更排障.md>) 是更好的页面。如果您想要定义术语，请参阅[词汇表](<OpenSpec 术语表.md>)。

## 基础知识

### OpenSpec 用一句话来说是什么？

一个轻量级层，让您和您的 AI 编码助手在编写任何代码之前以书面形式就要构建的内容达成一致。

### 我为什么想要那个？

因为AI助手即使错了也很自信。当需求仅存在于聊天线程中时，AI 会用猜测来填补空白，您会在代码存在后发现。 OpenSpec 提前移动了协议，修复错误的成本很低。有关完整案例，请参阅[核心概念概览](<OpenSpec 核心概念速览.md>)。

### 我必须用它来做所有事情吗？

不会。在协议很重要的地方使用它，这是最重要的工作。为了修复一个字符的拼写错误，仪式可能不值得，但这没关系。

### 我可以在现有的大型代码库上使用它，还是只能在新项目上使用它？

现有的代码库是主要事件。 OpenSpec 是棕地优先：您无需预先记录整个应用程序。您只为每次更改所涉及的内容编写规范，并且您的规范会随着时间的推移围绕您实际所做的工作进行填充。有一个专门的指南：[在现有项目中使用OpenSpec](<在已有项目中采用 OpenSpec.md>)。

### 它是否与一个 AI 工具绑定？

不会。OpenSpec可与30+助手配合使用，包括Claude Code、Cursor、Devin Desktop、GitHub Copilot、Gemini CLI、Codex等。完整列表和每个工具的详细信息位于[支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)中。

## 运行命令

### 在哪里输入 `/opsx:propose`？

在您的 AI 助理的聊天中，而不是在您的终端中。这是最常见的混淆点，因此它有自己的页面：[命令如何工作](<OpenSpec 两类命令如何协作.md>)。简短版本：`openspec ...` 在终端中运行，`/opsx:...` 在聊天中运行。

### 如何“启动交互模式”？

没有单独的模式可以启动。您像平常一样打开 AI 助手，然后在其聊天中输入斜线命令。斜杠命令是您“输入”OpenSpec 的方式。 （一个真正的交互式终端功能是`openspec view`，一个用于浏览规格和更改的仪表板。）[命令如何工作](<OpenSpec 两类命令如何协作.md>)中有完整说明。

### 我输入了斜杠命令，但什么也没发生。为什么？

很可能您在终端中输入了它，而不是在 AI 聊天中输入，您使用了工具未注册的拼写，或者命令尚未安装。如果文件丢失 - 或者您从未设置过该工具 - 运行 `openspec init`； `openspec update` 只刷新已经存在的文件。然后重新启动助手并使用“入门”下打印的表格 - 请参阅[如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)。 [故障排除](<OpenSpec 安装与变更排障.md#命令不显示>)有完整的清单。

### 为什么语法 `/opsx:propose` 在一个工具中而 `/opsx-propose` 在另一个工具中？

每个 AI 工具显示的自定义命令都略有不同，并且 OpenSpec 按照您的工具加载其编写的文件的方式拼写它们。名为`opsx-propose.md`的命令文件，输入为`/opsx-propose`；在 `commands/opsx/` 下提交的一份文件的类型为 `/opsx:propose`。使用技能而不是命令的工具使用技能名称 - Codex 需要 `$openspec-propose`，Kimi 代码 `/skill:openspec-propose`。 `openspec init`“入门”行已经为您选择的工具打印了正确的表格；完整的表格位于[如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)中。

### 技能和命令有什么区别？

两者都是 OpenSpec 写入的文件，以便您的助手可以运行工作流程。技能（`.../skills/openspec-*/SKILL.md`）是较新的交叉工具标准；命令（`.../commands/opsx-*`）是较旧的每个工具斜线文件。没必要挑。您只需键入斜杠命令，OpenSpec 就会安装您的工具使用的任何一个。

## 工作流程

### 如果我不确定要构建什么，我应该从哪里开始？

与`/opsx:explore`。它是一个无风险思考的合作伙伴，可以在编写任何代码之前读取您的代码库、列出选项并将模糊问题转化为具体计划。它位于默认配置文件中，因此始终可用。当计划明确后，就交给`/opsx:propose`。这是最好养成的习惯，因为它可以阻止急切的 AI 自信地构建错误的东西。请参阅[首先探索](<使用 OpenSpec 探索问题与澄清需求.md>)。

### 最简单的流程是什么？

```text
/opsx:explore (optional)   then   /opsx:propose <what you want>   then   /opsx:apply   then   /opsx:archive
```

探索思考，提出起草计划，申请建设，归档归档。当您已经确切知道自己想要什么时，请跳过探索。

### `/opsx:propose` 和 `/opsx:new` 有什么区别？

`/opsx:propose` 是默认的一步命令：它创建更改并立即起草所有计划工件。 `/opsx:new` 是扩展命令集的一部分，仅构建一个空的更改，让您可以使用 `/opsx:continue` 一次创建一个工件（或使用 `/opsx:ff` 一次创建所有工件）。除非您想要逐步控制，否则请使用提议。请参阅[命令](<OpenSpec AI 聊天命令参考.md>)。

### 什么是 `core` 和扩展配置文件？

配置文件决定安装哪些斜杠命令。 **Core**（默认）为您提供`propose`、`explore`、`apply`、`update`、`sync`、`archive`。 **扩展**集添加了`new`、`continue`、`ff`、`verify`、`bulk-archive`和`onboard`以实现更精细的控制。用`openspec config profile`切换，然后用`openspec update`应用。

### 我需要运行`/opsx:sync`吗？

通常不会。同步将变更的增量规格合并到您的主要规格中，并且 `/opsx:archive` 会为您做这件事。仅当您希望在归档之前合并规范时（例如在长时间运行的更改时），才手动运行同步。请参阅[命令](<OpenSpec AI 聊天命令参考.md#opsxsync>)。

### 开始后如何编辑提案、规范或任务？

只需编辑该文件即可。 `openspec/changes/<name>/` 中的每个工件都是纯 Markdown，并且没有锁定阶段或特殊编辑模式。手动更改它，或者要求您的 AI 修改它（“更新设计以使用队列”），然后继续。 AI 始终从当前文件内容开始工作。完整指南：[编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md>)。

### 我可以在实施部分计划后回去更改计划吗？

是的，任何时候。工作流程是流畅的，因此审阅和编辑不是您被锁定的阶段。编辑工件，然后继续。如果您想要进行结构化检查以确保代码仍然符合计划，请运行 `/opsx:verify`。请参阅[编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md#实施后如何回去审核>)。

### 我手动编辑了代码。如何使其与规范保持一致？

在存档之前使它们恢复同步，因为存档使您的规格成为真实的记录。如果代码现在正确，请更新增量规格以匹配您发货的内容；如果规范正确，请继续构建直到代码一致为止。 `/opsx:verify` 揭示了不匹配的情况。请参阅[编辑和迭代更改](<OpenSpec 变更工件的编辑与迭代.md#我手动编辑了代码如何将其与-openspec-协调一致>)。

### 我应该什么时候更新现有更改而不是开始新更改？

当它是相同的作品时更新，精炼。当意图发生根本改变或范围扩展到不同的工作时，重新开始。 [工作流程](<OpenSpec 快速与扩展工作流.md#何时更新与重新开始>)中有决策流程图和示例。

### 如果我的会话脱离了上下文，或者需求在实施过程中发生了变化怎么办？

这就是规格发挥作用的地方。由于该计划存在于文件中（不仅存在于聊天记录中），因此您可以清除上下文，开始新的 AI 会话，然后使用 `/opsx:apply` 继续；它读取工件并从第一个未经检查的任务继续。如果需求发生变化，请编辑工件以匹配新的现实并继续。保持上下文窗口干净也会产生更好的结果；执行前先清除它。

### 我应该将 `openspec/` 文件夹提交到 git 吗？

是的。您的规格、主动更改和存档是项目历史的一部分。像任何其他来源一样提交它们。特别是，存档成为了系统工作方式的持久记录。

## 规格和变化

### 规格与设计的区别是什么？

规范描述了可观察的行为：系统做什么、它的输入、输出和错误条件。设计描述了如何构建它：技术方法、架构决策、文件更改。如果可以在不改变外部可见行为的情况下改变实现，那么它属于设计，而不是规范。 [概念](<OpenSpec 核心概念与规格结构.md#规格是什么和不是什么>)更深入。

### 什么是 Delta 规格？

使用 `ADDED`、`MODIFIED` 和 `REMOVED` 部分仅描述更改内容的规范，而不是重述整个规范。这就是 OpenSpec 干净地处理现有系统编辑的方式。请参阅[概念](<OpenSpec 核心概念与规格结构.md#达美规格>)。

### 存档的更改去哪里？

至 `openspec/changes/archive/YYYY-MM-DD-<name>/`，保留所有变更工件。该更改将从您的活动列表中移出。显式声明 `retire_capabilities: true` 的更改在删除主要功能的最终要求时也可以删除该功能规范。

## 配置和定制

### 我如何告诉 AI 我的技术堆栈？

将其放入`context:`下的`openspec/config.yaml`中。该文本被注入到每个计划请求中，因此 AI 始终了解您的堆栈和约定。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#项目配置>)。

### 我可以用英语以外的语言生成规格吗？

是的。将语言指令添加到您的配置的 `context:`。 [多语言](<OpenSpec 多语言工件配置.md>)具有多种语言的复制粘贴片段。

### 我可以更改工作流程本身吗？

是的，使用自定义模式。模式定义存在哪些工件以及它们如何相互依赖。使用 `openspec schema fork spec-driven my-workflow` 分叉默认值，然后对其进行编辑。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#自定义模式>)。

## 模型、隐私和升级

### 我应该使用哪种AI型号？

OpenSpec 最适合高推理模型。 README 推荐 Codex 5.5 和 Opus 4.7 等模型进行规划和实施。还要保持上下文窗口干净：在实施之前清除它以获得最佳结果。

### OpenSpec 是否收集数据？

它收集匿名使用统计信息：仅命令名称和版本。没有参数、路径、内容或个人数据，并且它会在 CI 中自动关闭。使用 `export OPENSPEC_TELEMETRY=0` 或 `export DO_NOT_TRACK=1` 选择退出。

### 我该如何升级？

两步。升级包（`npm install -g @fission-ai/openspec@latest`），然后在每个项目中运行`openspec update`以刷新生成的技能和命令。

### 如何卸载OpenSpec？

没有卸载命令，因为它只是一个全局包加上项目中的文件。删除软件包（`npm uninstall -g @fission-ai/openspec`），并可以选择删除`openspec/`目录和生成的工具文件。分步说明（包括安全保留的内容）位于[安装：卸载](<OpenSpec 安装与初始化指南.md#卸载>)中。

## 寻求帮助

### 我在哪里提出问题或报告错误？

- **不和谐：** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub 问题：** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **从您的终端：** `openspec feedback "your message"` 为您打开一个 GitHub 问题。

### 这些文档是错误的或令人困惑的。我该怎么办？

告诉我们，或者修复它。文档 PR 受到欢迎并受到重视。打开问题或发送拉取请求。


## 原文对照

<details>
<summary>展开完整原文</summary>

# FAQ

Quick answers to the questions people ask most. If your question is really a "something is broken" question, [Troubleshooting](<OpenSpec 安装与变更排障.md>) is the better page. If you want a term defined, see the [Glossary](<OpenSpec 术语表.md>).

## The basics

### What is OpenSpec, in one sentence?

A lightweight layer that gets you and your AI coding assistant to agree on what to build, in writing, before any code is written.

### Why would I want that?

Because AI assistants are confident even when they're wrong. When the requirements live only in a chat thread, the AI fills gaps with guesses, and you find out after the code exists. OpenSpec moves the agreement earlier, where mistakes are cheap to fix. See [Core Concepts at a Glance](<OpenSpec 核心概念速览.md>) for the full case.

### Do I have to use it for everything?

No. Use it where agreement matters, which is most non-trivial work. For a one-character typo fix, the ceremony probably isn't worth it, and that's fine.

### Can I use it on a big existing codebase, or only new projects?

Existing codebases are the main event. OpenSpec is brownfield-first: you do not document your whole app up front. You write specs only for what each change touches, and your specs fill in over time around the work you actually do. There's a dedicated guide: [Using OpenSpec in an Existing Project](<在已有项目中采用 OpenSpec.md>).

### Is it tied to one AI tool?

No. OpenSpec works with 30+ assistants, including Claude Code, Cursor, Devin Desktop, GitHub Copilot, Gemini CLI, Codex, and more. The full list and per-tool details are in [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>).

## Running commands

### Where do I type `/opsx:propose`?

In your AI assistant's chat, not your terminal. This is the single most common point of confusion, so it has its own page: [How Commands Work](<OpenSpec 两类命令如何协作.md>). Short version: `openspec ...` runs in the terminal, `/opsx:...` runs in chat.

### How do I "start interactive mode"?

There isn't a separate mode to start. You open your AI assistant like normal and type a slash command into its chat. The slash command is how you "enter" OpenSpec. (The one genuinely interactive terminal feature is `openspec view`, a dashboard for browsing specs and changes.) Full explanation in [How Commands Work](<OpenSpec 两类命令如何协作.md>).

### I typed a slash command and nothing happened. Why?

Most likely you typed it in the terminal instead of your AI chat, you used a spelling your tool doesn't register, or the commands aren't installed yet. If the files are missing — or you never set the tool up — run `openspec init`; `openspec update` only refreshes files that already exist. Then restart your assistant and use the form printed under "Getting started" — see [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>). [Troubleshooting](<OpenSpec 安装与变更排障.md#commands-dont-show-up>) has the full checklist.

### Why is the syntax `/opsx:propose` in one tool and `/opsx-propose` in another?

Each AI tool surfaces custom commands a little differently, and OpenSpec spells them the way your tool loads the file it wrote. A command file named `opsx-propose.md` is typed `/opsx-propose`; one filed under `commands/opsx/` is typed `/opsx:propose`. Tools that take skills instead of commands use the skill name — Codex needs `$openspec-propose`, Kimi Code `/skill:openspec-propose`. The `openspec init` "Getting started" line already prints the right form for the tools you picked; the full table is in [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>).

### What's the difference between a skill and a command?

Both are files OpenSpec writes so your assistant can run the workflow. Skills (`.../skills/openspec-*/SKILL.md`) are the newer cross-tool standard; commands (`.../commands/opsx-*`) are the older per-tool slash files. You don't need to pick. You just type the slash command, and OpenSpec installs whichever your tool uses.

## The workflow

### Where should I start if I'm not sure what to build?

With `/opsx:explore`. It's a no-stakes thinking partner that reads your codebase, lays out options, and turns a fuzzy problem into a concrete plan, all before any code gets written. It's in the default profile, so it's always available. When the plan is clear, it hands off to `/opsx:propose`. This is the single best habit to form, because it stops an eager AI from confidently building the wrong thing. See [Explore First](<使用 OpenSpec 探索问题与澄清需求.md>).

### What's the simplest possible flow?

```text
/opsx:explore (optional)   then   /opsx:propose <what you want>   then   /opsx:apply   then   /opsx:archive
```

Explore to think it through, propose to draft the plan, apply to build it, archive to file it away. Skip explore when you already know exactly what you want.

### What's the difference between `/opsx:propose` and `/opsx:new`?

`/opsx:propose` is the default one-step command: it creates the change and drafts all the planning artifacts at once. `/opsx:new` is part of the expanded command set and only scaffolds an empty change, leaving you to create artifacts one at a time with `/opsx:continue` (or all at once with `/opsx:ff`). Use propose unless you want step-by-step control. See [Commands](<OpenSpec AI 聊天命令参考.md>).

### What are `core` and expanded profiles?

A profile decides which slash commands get installed. **Core** (the default) gives you `propose`, `explore`, `apply`, `update`, `sync`, `archive`. The **expanded** set adds `new`, `continue`, `ff`, `verify`, `bulk-archive`, and `onboard` for finer control. Switch with `openspec config profile`, then apply with `openspec update`.

### Do I need to run `/opsx:sync`?

Usually not. Sync merges a change's delta specs into your main specs, and `/opsx:archive` will offer to do it for you. Run sync manually only when you want the specs merged before archiving, for example on a long-running change. See [Commands](<OpenSpec AI 聊天命令参考.md#opsxsync>).

### How do I edit a proposal, spec, or task after I've started?

Just edit the file. Every artifact is plain Markdown in `openspec/changes/<name>/`, and there's no locked phase or special edit mode. Change it by hand, or ask your AI to revise it ("update the design to use a queue"), then keep going. The AI always works from the current file contents. Full guide: [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md>).

### Can I go back and change the plan after implementing some of it?

Yes, at any time. The workflow is fluid, so review and editing aren't phases you get locked out of. Edit the artifact, then continue. If you want a structured check that the code still matches the plan, run `/opsx:verify`. See [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md#how-do-i-go-back-to-review-after-implementing>).

### I edited the code by hand. How do I reconcile it with the spec?

Bring them back in sync before you archive, since archiving makes your specs the record of truth. If the code is now correct, update the delta spec to match what you shipped; if the spec is correct, keep building until the code agrees. `/opsx:verify` surfaces the mismatches. See [Editing & Iterating on a Change](<OpenSpec 变更工件的编辑与迭代.md#i-edited-the-code-by-hand-how-do-i-reconcile-that-with-openspec>).

### When should I update an existing change versus start a new one?

Update when it's the same work, refined. Start fresh when the intent fundamentally changed or the scope exploded into different work. There's a decision flowchart and examples in [Workflows](<OpenSpec 快速与扩展工作流.md#when-to-update-vs-start-fresh>).

### What if my session runs out of context, or requirements change mid-implementation?

This is where specs earn their keep. Because the plan lives in files (not only in chat history), you can clear your context, start a fresh AI session, and pick up with `/opsx:apply`; it reads the artifacts and resumes from the first unchecked task. If requirements change, edit the artifacts to match the new reality and continue. Keeping a clean context window also produces better results; clear it before implementation.

### Should I commit the `openspec/` folder to git?

Yes. Your specs, active changes, and archive are part of your project's history. Commit them like any other source. The archive in particular becomes a durable record of why your system works the way it does.

## Specs and changes

### What goes in a spec versus a design?

A spec describes observable behavior: what the system does, its inputs, outputs, and error conditions. A design describes how you'll build it: the technical approach, architecture decisions, file changes. If implementation could change without changing externally visible behavior, it belongs in the design, not the spec. [Concepts](<OpenSpec 核心概念与规格结构.md#what-a-spec-is-and-is-not>) goes deeper.

### What's a delta spec?

A spec that describes only what's changing, using `ADDED`, `MODIFIED`, and `REMOVED` sections, rather than restating the whole spec. It's how OpenSpec handles edits to existing systems cleanly. See [Concepts](<OpenSpec 核心概念与规格结构.md#delta-specs>).

### Where do archived changes go?

To `openspec/changes/archive/YYYY-MM-DD-<name>/`, with all change artifacts preserved. The change moves out of your active list. A change that explicitly declares `retire_capabilities: true` can also delete a main capability spec when it removes that capability's final requirement.

## Configuration and customization

### How do I tell the AI about my tech stack?

Put it in `openspec/config.yaml` under `context:`. That text is injected into every planning request, so the AI always knows your stack and conventions. See [Customization](<OpenSpec 项目配置与工作流定制.md#project-configuration>).

### Can I generate specs in a language other than English?

Yes. Add a language instruction to your config's `context:`. [Multi-Language](<OpenSpec 多语言工件配置.md>) has copy-paste snippets for several languages.

### Can I change the workflow itself?

Yes, with custom schemas. A schema defines which artifacts exist and how they depend on each other. Fork the default with `openspec schema fork spec-driven my-workflow`, then edit it. See [Customization](<OpenSpec 项目配置与工作流定制.md#custom-schemas>).

## Models, privacy, and upgrades

### Which AI model should I use?

OpenSpec works best with high-reasoning models. The README recommends models like Codex 5.5 and Opus 4.7 for both planning and implementation. Also keep your context window clean: clear it before implementation for best results.

### Does OpenSpec collect data?

It collects anonymous usage stats: command names and version only. No arguments, paths, content, or personal data, and it's off automatically in CI. Opt out with `export OPENSPEC_TELEMETRY=0` or `export DO_NOT_TRACK=1`.

### How do I upgrade?

Two steps. Upgrade the package (`npm install -g @fission-ai/openspec@latest`), then run `openspec update` inside each project to refresh the generated skills and commands.

### How do I uninstall OpenSpec?

There's no uninstall command, because it's just a global package plus files in your project. Remove the package (`npm uninstall -g @fission-ai/openspec`), and optionally delete the `openspec/` directory and the generated tool files. Step-by-step, including what's safe to keep, is in [Installation: Uninstalling](<OpenSpec 安装与初始化指南.md#uninstalling>).

## Getting help

### Where do I ask questions or report bugs?

- **Discord:** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub Issues:** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **From your terminal:** `openspec feedback "your message"` opens a GitHub issue for you.

### These docs are wrong or confusing. What do I do?

Tell us, or fix it. Documentation PRs are welcome and valued. Open an issue or send a pull request.


</details>
