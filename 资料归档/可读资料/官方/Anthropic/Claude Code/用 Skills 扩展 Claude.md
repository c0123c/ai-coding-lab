# 用 Skills 扩展 Claude

介绍创建、调用、分享与管理技能的方法，包括 SKILL.md、附属资源、调用控制、作用范围和内置技能，支持复用开发操作。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-10-10](<https://code.claude.com/docs/en/skills>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/用 Skills 扩展 Claude.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 用技能扩展Claude

> 创建、管理和共享技能以扩展 Claude 在 Claude Code 中的功能。包括自定义命令和捆绑技能。

技能扩展了Claude的能力。创建包含说明的 `SKILL.md` 文件，Claude 将其添加到其工具包中。 Claude 在相关时使用技能，或者您可以直接使用 `/skill-name` 调用技能。

当您不断将相同的说明、清单或多步骤过程粘贴到聊天中时，或者当 CLAUDE.md 的一部分已发展为过程而不是事实时，创建技能。与 CLAUDE.md 内容不同，技能的主体仅在使用时才加载，因此长参考材料在您需要之前几乎不需要花费任何费用。



**注释：**

有关 `/help` 和 `/compact` 等内置命令，以及 `/debug` 和 `/code-review` 等捆绑技能，请参阅 [ 命令参考 ](<https://code.claude.com/docs/en/commands>)。

**自定义命令已合并到技能中。** `.claude/commands/deploy.md` 处的文件和 `.claude/skills/deploy/SKILL.md` 处的技能都会创建 `/deploy` 并以相同的方式工作。您现有的 `.claude/commands/` 文件将继续工作。技能添加了可选功能：支持文件的目录，[的前置内容控制您或Claude是否调用它们](<#控制谁调用技能>)，以及Claude在相关时自动加载它们的能力。


Claude Code 技能遵循 [Agent Skills](<https://agentskills.io>) 开放标准，该标准适用于多种 AI 工具。 Claude Code 通过附加功能扩展了标准，例如 [ 调用控制 ](<#控制谁调用技能>)、[ 子代理执行 ](<#在子代理中运行技能>) 和 [ 动态上下文注入 ](<#注入动态上下文>)。请参阅 [在 Claude Code](<#在外部使用技能前线claude-code>) 之外使用技能 frontmatter，其中 frontmatter 字段是标准的一部分，并且是 Claude Code 扩展。

## 捆绑技能

Claude Code包括一组捆绑技能，例如`/doctor`、`/code-review`、`/batch`、`/debug`、`/loop`和`/claude-api`。捆绑技能是基于提示的：它们给出 Claude 详细说明，并让它使用其工具来协调工作。大多数内置命令直接执行固定逻辑。

您可以像调用任何其他技能一样调用捆绑技能，只需键入 `/`，后跟技能名称。 Claude 在相关时自动调用一些捆绑技能；其他的，包括 `/verify`，仅在您调用它们时运行，这使您可以控制这些运行时间较长的检查何时花费时间和令牌。

大多数捆绑技能在每个课程中都可用。其中一些取决于特定功能：例如，仅当启用 [ 动态工作流程 ](<https://code.claude.com/docs/en/workflows>) 时，`/workflow-authoring` 才可用。

要关闭捆绑技能，请使用 [`disableBundledSkills`](<https://code.claude.com/docs/en/settings-reference#disablebundledskills>) 设置。



**注释：**

当 `disableBundledSkills` 打开时，[`/doctor`](<https://code.claude.com/docs/en/commands#all-commands>) 设置检查在 Claude Code v2.1.205 及更高版本中保持可输入状态。要隐藏它，请设置 `DISABLE_DOCTOR_COMMAND` 环境变量或 `"doctor": "off"` 的 [`skillOverrides`](<#从设置中覆盖技能可见性>) 条目。在v2.1.205之前，`/doctor`是一个内置命令而不是捆绑技能。


捆绑技能与 [ 命令参考 ](<https://code.claude.com/docs/en/commands>) 中的内置命令一起列出，在用途列中标记为 ** 技能 **。

### 使用 `/doctor` 检查您的设置

在 Claude Code 提示符下运行 `/doctor` 进行设置检查，诊断问题并修复它们。 Claude 首先报告其发现，并在更改任何内容之前要求确认。检查范围包括以下几个方面：

* **安装运行状况**：重复或剩余安装、`PATH`问题、无法解析的设置文件以及[发布渠道上是否有更新版本](<https://code.claude.com/docs/en/setup#configure-release-channel>)
* **扩展**：未使用的技能、MCP服务器和插件与其上下文成本相比，以及缓慢的[挂钩](<https://code.claude.com/docs/en/hooks>)
* **`CLAUDE.md` 文件**：复制签入文件的本地 `CLAUDE.md` 文件，签入的 [`CLAUDE.md` 内容 Claude 可以从代码库 ](<https://code.claude.com/docs/en/memory#my-claude-md-is-too-large>) 中派生，以及始终加载的指导，其中 Claude提供迁移到按需加载的技能和嵌套 `CLAUDE.md` 文件的功能
* **权限**：将[自动模式](<https://code.claude.com/docs/en/permissions#permission-modes>)设置为默认权限模式并[预先批准](<https://code.claude.com/docs/en/permissions>)您经常拒绝的只读命令的提议

对于不启动会话的只读安装诊断，请在终端中运行 `claude doctor`。

要审核您的指令而不是您的设置，请在 Claude Code 提示符下运行 `/doctor prompt-audit`。 Claude [ 检查您的 `CLAUDE.md` 文件、技能和其他配置 ](<https://code.claude.com/docs/en/memory#audit-your-instruction-files>) 是否有过时或冲突的指令，而不是运行检查。 `prompt-audit` 子命令需要 Claude Code v2.1.283 或更高版本。

### 运行并验证您的应用程序

三个捆绑技能协同工作来启动您的应用程序并确认针对正在运行的应用程序的更改，而不是单独进行测试：

|技能|目的|
| :- | :- |
| `/run` |启动并驱动您的应用程序以查看更改的效果|
| `/verify` |构建并运行您的应用程序以确认代码更改执行其应有的操作，而无需退回到测试或类型检查|
| `/run-skill-generator` |教 `/run` 和 `/verify` 如何构建和启动您的项目|

`/run` 和 `/verify` 无需设置即可工作。它们根据您的项目类型（CLI、服务器、TUI、浏览器驱动）以及 README、`package.json` 或 `Makefile` 中的内容推断启动。对于需要标准启动之外的任何内容的项目来说，这种推论变得不可靠：数据库、环境文件、图形会话、多步骤构建。

`/run-skill-generator` 记录配方。它让您的应用程序在干净的环境中运行，捕获有效的内容（安装命令、环境变量、启动脚本），并将其作为每个项目的技能提交到 `.claude/skills/run-<name>/`。之后，存储库中的 `/run`、`/verify` 和任何其他 agent 将遵循记录的配方，而不是重新发现它。每个项目运行一次 `/run-skill-generator`，如果构建或启动过程发生变化，则再次运行。

`/verify`还可以记录自己的菜谱。当它必须在没有记录配方的情况下构建和驱动您的应用程序时，它会将有效的内容写入存储库根目录中的 `.claude/skills/verify/SKILL.md` 或 monorepo 中的触摸包目录中，因此稍后运行和其他 agents 遵循相同的步骤。在存储库根目录中，记录的技能取代了捆绑的 `/verify`。

Claude 仅在引导运行错误时编辑记录的文件，例如命令失败或缺少步骤，因此您可以提交文件而无需进行每个会话的差异。在 v2.1.205 之前，捆绑技能告诉 Claude 折叠运行中学到的任何内容，这导致频繁的合并冲突。

### 每次提交之前运行检查

当会话以名为 `verify` 或 `simplify` 的技能开始时，Claude Code 的提交指令会告诉 Claude 在每次提交之前运行它，对文档或测试的更改除外。这需要 Claude Code v2.1.286 或更高版本。当会话开始时满足这些条件时，Claude 会获取该指令：

* **位置**：技能从企业、个人、项目或附加目录[位置](<#where-skills-live>)或从具有该名称的`.claude/commands/`文件加载。 `/verify` 在您的存储库根目录中记录的配方是一项项目技能，因此很重要。捆绑的 `/verify` 和 `/simplify`、插件技能以及您的 claude.ai 帐户中的技能不计算在内。
* **调用**：Claude可以调用技能。如果您[阻止Claude调用它](<#控制谁调用技能>)，例如使用`disable-model-invocation: true`，Claude不会收到指令。
* **Git说明**：你还没有关闭[`includeGitInstructions`](<https://code.claude.com/docs/en/settings-reference#includegitinstructions>)。关闭它会删除该指令以及其余的内置提交和 PR 指令。

### 从事 Claude API 项目

捆绑的 `/claude-api` 技能加载项目语言的 [Claude API](<https://platform.claude.com/docs/en/api/overview>) 和 [ 托管 Agents](<https://platform.claude.com/docs/en/managed-agents/overview>) 参考资料。当您的代码导入 `anthropic` 或 `@anthropic-ai/sdk` 时，Claude 也会自动激活它。

要启动一项技能的工作流程，请在 Claude Code 提示符下的技能名称后面键入子命令，例如 `/claude-api migrate`。该表列出了每个子命令的用途以及包含它的最早的 Claude Code 版本。 `migrate` 和 `managed-agents-onboard` 早于表跟踪的最旧版本 v2.1.221。

|子命令|它的作用|最低版本|
| :- | :- | :- |
| `migrate` |将您现有的 Claude API 代码更新为较新的型号|早于v2.1.221|
| `upgrade` |将项目的 Anthropic SDK 依赖项跨主要版本移动，目前 Python `anthropic` 包从 0.x 到 1.x|v2.1.236 或更高版本|
| `managed-agents-onboard` |逐步创建新的托管 Agent|早于v2.1.221|
| `managed-agents-onboard <url>` |构建 URL 页面描述的托管 Agent，例如 [托管 Agents 文档 ](<https://platform.claude.com/docs/en/managed-agents/overview>) 中的页面|v2.1.290 或更高版本|
| `managed-agents-onboard <quickstart-name>` |构建控制台的快速入门模板之一，例如 `deep-researcher`。如果您提供的一个单词不是模板名称，Claude 会列出有效名称|v2.1.290 或更高版本|
| `prompt-audit` |在提示、技能和工具描述中标记为旧模型编写的说明，并提出修复方案作为差异|v2.1.221 或更高版本|
| `cost-optimize` |分析您的项目 Claude API 的支出去向，并建议通过诸如提示缓存、修剪不需要的输入和输出令牌、批处理、工作量和模型选择（一次一项更改）等选项来节省费用|v2.1.247 或更高版本|
| `build-eval` |为您的 Claude 支持的应用程序构建评估集|v2.1.259 或更高版本|
| `hillclimb` |根据现有评估迭代改进您的应用程序|v2.1.259 或更高版本|
| `preserved-thinking-migration` |查找您的集成对先前回合、其系统提示或其工具列表所做的编辑，这些编辑使 [ 保留思维 ](<https://platform.claude.com/docs/en/build-with-claude/preserved-thinking>) 块无效，测量每个块丢弃的推理量，并建议一次修复一个，每次更改后重新测量|v2.1.282 或更高版本|

## 开始使用

### 创建你的第一个技能

此示例创建了一项技能，用于总结 git 存储库中未提交的更改并标记任何有风险的内容。它会在 Claude 读取实时差异之前将其拉入提示中，因此响应基于您的实际工作树，而不是 Claude 从打开的文件中猜测的内容。当您询问更改时，Claude会自动加载技能，或者您可以直接使用`/summarize-changes`调用它。



  

#### 创建技能目录

在您的个人技能文件夹中为该技能创建一个目录。个人技能适用于您的所有项目。

    ```bash theme={null}
    mkdir -p ~/.claude/skills/summarize-changes
    ```
  


  

#### 写入SKILL.md

每个技能都需要一个包含两部分的 `SKILL.md` 文件：`---` 标记之间的 YAML frontmatter，告诉 Claude 何时使用该技能，以及技能运行时带有指令 Claude 的 Markdown 内容。目录名称或设置时的前面内容`name`将成为您键入的命令，而`description`帮助Claude决定何时自动加载技能。

将此保存到`~/.claude/skills/summarize-changes/SKILL.md`：

    ```yaml theme={null}
    ---
    description: Summarizes uncommitted changes and flags anything risky. Use when the user asks what changed, wants a commit message, or asks to review their diff.
    ---

    ## Current changes

    !`git diff HEAD`

    ## Instructions

    Summarize the changes above in two or three bullet points, then list any risks you notice such as missing error handling, hardcoded values, or tests that need updating. If the diff is empty, say there are no uncommitted changes.
    ```

`` !`git diff HEAD` `` 行使用 [ 动态上下文注入 ](<#注入动态上下文>)：Claude Code 运行命令并在 Claude 看到技能内容之前用其输出替换该行，因此指令到达时已内联了当前差异。
  


  

#### 测试技能

打开一个 git 项目，对任何文件进行少量编辑，然后通过运行 `claude` 启动 Claude Code。您可以通过两种方式测试技能。

**让Claude通过询问与描述相匹配的内容来自动调用它**：

    ```text theme={null}
    What did I change?
    ```

**或者直接调用**，技能名称：

    ```text theme={null}
    /summarize-changes
    ```

无论哪种方式，Claude 都应该回复您的编辑的简短摘要和风险列表。
  


<h2 id="where-skills-live">
 选择技能加载位置
</h2>

保存技能的位置决定哪些会话加载它。将其保存在您的主目录下以将其添加到每个项目中，将其提交到存储库以与在那里工作的每个人共享，或者通过插件或托管设置分发它以到达整个团队。

|地点|路径|加载于|
| :- | :- | :- |
|企业|[ 托管设置目录 ](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>) 中的 `.claude/skills/<skill-name>/SKILL.md`|您的组织部署该计算机的计算机上的所有用户|
|个人| `~/.claude/skills/<skill-name>/SKILL.md` |您在此计算机上的所有项目，但不包括 [Cowork 或云会话](<#skills-in-cowork-and-cloud-sessions>)|
|项目| `.claude/skills/<skill-name>/SKILL.md` |此存储库中的会话。提交它，这样你的团队也能得到它|
|嵌套| `<subdir>/.claude/skills/<skill-name>/SKILL.md` |会话在 `<subdir>` 或以下开始。一旦 Claude 处理那里的文件，上面启动的会话就会加载技能。请参阅 [monorepos 和子目录 ](<#discovery-from-parent-and-nested-directories>)|
|附加目录|`.claude/skills/<skill-name>/SKILL.md` 在您通过 `--add-dir` 传递的目录中|那次会议。查看项目外的[目录](<#skills-from-additional-directories>)|
|插件| `<plugin>/skills/<skill-name>/SKILL.md` |无论何时启用[插件](<https://code.claude.com/docs/en/plugins/overview>)，如`/plugin-name:skill-name`|
|克劳德.ai 帐户|为您的 claude.ai 帐户启用的技能|使用该帐户登录的协同工作会话、云会话和终端会话。请参阅[从 claude.ai 同步的技能](<#how-synced-skills-behave>)|

技能文件夹也遵循以下规则：

* **符号链接文件夹**：企业、个人或项目位置中的`<skill-name>`条目可以是指向磁盘上其他位置的目录的符号链接。 Claude Code从目标读取`SKILL.md`并加载一次技能，即使多个位置指向同一目标。插件技能 [ 以不同方式处理符号链接 ](<https://code.claude.com/docs/en/plugins/host-marketplace#share-files-within-a-marketplace-with-symlinks>)。
* **保留名称 `synced`**: 不要命名技能文件夹 `synced`，任何大小写。 Claude Code 用途 `~/.claude/skills/synced/` 为了 [从 claude.ai 下载的技能](<#同步技能加载位置>) 并跳过您在企业、个人和项目位置中以该名称创建的技能。
* **保留名称`anthropic-skills`**：在插件之外，名称为`anthropic-skills`或以`anthropic-skills:`开头的技能文件夹或命令文件不会加载。请参阅[为同步技能保留的名称](<#names-reserved-for-synced-skills>)。
* **命令文件**：`.claude/commands/`中的Markdown文件是较旧的格式，但仍然有效。它支持相同的[frontmatter](<#前言参考>)，除了`name`和`paths`。要查找您键入的用于调用它的名称，请参阅[技能如何获取其命令名称](<#技能如何获取其命令名称>)。更喜欢新工作的技能，因为技能还支持[支持文件](<#添加支持文件>)。
* **技能文件夹作为插件**：将`.claude-plugin/plugin.json`添加到技能文件夹中，它作为[插件](<https://code.claude.com/docs/en/plugins/loading#plugins-shared-through-a-repository>)加载，名为`<name>@skills-dir`，因此它可以捆绑agents，挂钩和MCP服务器。在项目的 `.claude/skills/` 中，这需要首先接受工作区信任对话框。

<h3 id="discovery-from-parent-and-nested-directories">
 monorepos和子目录中的加载技能
</h3>

Claude Code 从启动目录中的 `.claude/skills/` 以及直到存储库根目录的每个父目录中加载项目技能，因此从 `packages/frontend/` 开始仍然会获取在根目录中定义的技能。当您 [ 在 v2.1.246 或更高版本上移动包含 `/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>) 的会话时，Claude Code 将添加新目录的项目技能。

在链接的 [git worktree](<https://code.claude.com/docs/en/worktrees>) 中运行的会话中，Claude Code 仅搜索父目录直至工作树根。在 Claude Code v2.1.277 或更高版本上，当工作树签出的根目录没有 `.claude/skills` 目录时，Claude Code 会加载主签出的项目技能。请参阅[工作树与主结账共享哪些内容](<https://code.claude.com/docs/en/worktrees#what-worktrees-share-with-the-main-checkout>)。

您开始的位置下面的 `.claude/skills/` 目录中的技能不会在启动时加载。它们在第一次 Claude 读取或编辑该子目录中的文件时加载，并在会话的其余部分保持可用。在此之前，它们不会出现在 `/` 菜单中，并且您无法通过名称调用它们。要更快地加载它们，请使用子目录的路径运行 `/add-dir`，这需要 Claude Code v2.1.257 或更高版本。

当嵌套技能的目录名称与另一个技能的名称匹配时，两者都保持可用。在存储库根目录中使用 `deploy` 技能，在 `apps/web/.claude/skills/` 中使用另一个技能：

* `/deploy`运行root技能。 Claude Code 还列出了 Claude 的目录限定变体，并带有一条指令来调用其目录保存其正在处理的文件的变体，因此嵌套技能仍然适用于 `apps/web/` 中的工作。
* `/apps/web:deploy` 自行运行嵌套技能。它的描述命名了它适用的目录。

<h3 id="skills-from-additional-directories">
 从项目外部的目录加载技能
</h3>

当您添加包含`--add-dir`或`/add-dir`的目录时，Claude Code会加载该目录的`.claude/skills/`及其`.claude/commands/`和`.claude/agents/`中的技能。 Agent SDK 通过 TypeScript 中的 [`additionalDirectories`](<https://code.claude.com/docs/en/agent-sdk/typescript#options>) 或 Python 中的 [`add_dirs`](<https://code.claude.com/docs/en/agent-sdk/python#claudeagentoptions>) 添加的目录以相同的方式加载，因为 SDK 将它们作为 `--add-dir` 传递。 `settings.json` 中的 `permissions.additionalDirectories` 设置仅授予文件访问权限，并且不加载任何内容。

Claude Code 在启动时通过 `--add-dir` 传递的目录中监视 `.claude/skills/`，如 [ 在会话期间编辑技能 ](<#live-change-detection>) 所描述的那样。它不会监视添加的目录的 `.claude/commands/` 或 `.claude/agents/`，因此在更改其中的文件后重新启动会话。

这些负载取决于 `project` [ 设置源 ](<https://code.claude.com/docs/en/agent-sdk/claude-code-features#control-filesystem-settings-with-settingsources>)，默认情况下处于打开状态。 [`strictPluginOnlyCustomization`](<https://code.claude.com/docs/en/settings-reference#strictpluginonlycustomization>) 策略、[裸模式](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>) 和 [`--safe-mode`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 各自进一步限制它们，如这些页面所述。请参阅 [其他目录授予文件访问权限，而不是配置 ](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>)，了解添加的目录加载内容的完整表，包括 `CLAUDE.md` 和插件设置。

### 解决具有相同名称的技能

当两个技能共享一个目录或文件名时，每个技能的来源决定运行哪一个 `/name`。对于由 frontmatter `name` 字段设置的名称，请参阅[技能如何获取其命令名称](<#技能如何获取其命令名称>)。该表涵盖了企业、个人、项目、嵌套、插件和 claude.ai 位置、捆绑技能、内置命令和命令文件：

|同名于|哪一个运行|
| :- | :- |
|企业、个人、项目二|企业高于个人，个人高于项目。 `~/.claude/skills/` 和项目的 `.claude/skills/` 中都有 `deploy`，`/deploy` 运行个人的|
|这些位置中的任何一个以及 [ 捆绑技能 ](<#捆绑技能>)|您的技能会替换捆绑的命令，但不会替换其别名。项目 `code-review` 技能替换 `/code-review`，并且捆绑的别名 `/review` 永远不会运行你的技能|
|这些位置中的任何一个和 [ 内置命令 ](<https://code.claude.com/docs/en/commands>)|在本地终端会话中，您的技能会替换内置命令，但不会替换其别名。一个项目`usage`技能替换了`/usage`，内置别名`/cost`仍然运行内置命令|
|`.claude/commands/`中的一项技能和一个文件|技巧|
|项目根技能和嵌套技能|两者都加载。请参阅 [monorepos 和子目录 ](<#discovery-from-parent-and-nested-directories>)|
|插件技能和上述任意位置的技能|两者都会加载，因为插件技能的命名空间为 `/plugin-name:skill-name`|
|上述任何一项以及从您的 claude.ai 帐户同步的技能简称 [](<#how-synced-skills-behave>)|其他技能或命令。然后，同步的技能将被列出，并且仅以其全名运行。请参阅[当同步的技能名称与另一个命令匹配时](<#当同步的技能名称与另一个命令匹配时>)|

<h3 id="skills-in-cowork-and-cloud-sessions">
 在 Cowork 和云会话中使用技能
</h3>

[Cowork](<https://claude.com/product/cowork>)会话和[云会话](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>)，包括[例程](<https://code.claude.com/docs/en/routines>)，请勿在您的计算机上读取`~/.claude/skills/`。交互式和预定的 Cowork 会话都会加载为您的 claude.ai 帐户启用的技能，并在会话开始时同步；从桌面应用程序侧栏中的 **自定义 ** 或从 claude.ai 上的技能设置来管理它们。云会话还加载致力于克隆存储库的 `.claude/skills/` 的项目技能。

如果某个技能仅存在于计算机上的 `~/.claude/skills/` 中，Claude Code 会报告当 [ 例程 ](<https://code.claude.com/docs/en/routines>) 调用该技能时未找到该技能，因为每个例程运行都作为新的云会话启动。要在这些课程中发挥个人技能：

* 对于 Cowork 和云会话，请为您的 claude.ai 帐户启用该技能。 [自托管环境中的某些会话](<https://code.claude.com/docs/en/self-hosted-environments-configuration#how-each-session’s-config-is-assembled>)不会加载您帐户的技能。
* 对于云会话，您可以将技能提交到存储库的 `.claude/skills/`。在存储库的 `.claude/settings.json` 中声明的插件和仅在您的用户设置中启用的插件 [ 不会在云会话中加载 ](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>)。

[桌面计划任务](<https://code.claude.com/docs/en/desktop-scheduled-tasks>)在您的计算机上本地运行，因此它们会加载`~/.claude/skills/`。

<h3 id="how-synced-skills-behave">
 技能从 claude.ai 同步
</h3>

如果您使用 Cowork 或云会话，或者使用 claude.ai 帐户在终端中登录 Claude Code，则本节适用于您。在这些会话中，Claude Code 会加载为您的 claude.ai 帐户启用的技能，而您无需进行任何设置，如 [ 同步技能加载位置 ](<#同步技能加载位置>) 中所述。这些技能包括您在 claude.ai 设置中创建或打开的技能、您的组织在其中提供的技能以及 Anthropic 的内置技能，例如 `pdf` 和 `xlsx`。

Claude Code 从您的帐户下载同步技能，而不是读取您在运行会话的计算机上编写的文件，因此它会将规则应用于不适用于您存储在 [ 技能位置 ](<#where-skills-live>) 中的技能的同步技能。

#### 同步技能加载位置

在 Cowork 或云会话中，Claude Code 加载为您的 claude.ai 帐户启用的技能，[Cowork 和云会话中的技能](<#skills-in-cowork-and-cloud-sessions>) 说明如何选择这些会话获得哪些技能。

在您的终端中，Claude Code 会在您使用 claude.ai 帐户登录的会话中同步这些技能。当会话开始时，Claude Code 会在后台将您帐户的技能下载到 `~/.claude/skills/synced/`，然后在会话运行时检查 claude.ai 是否有更改。当检查发现 claude.ai 上添加、编辑或关闭了一项技能时，Claude Code 会在正在运行的会话中添加、更新或删除该技能，而无需重新启动。在终端会话中同步需要 Claude Code v2.1.273 或更高版本。

当会话空闲时，检查运行的频率会降低：

* **当您或 Claude 在会话中工作时 **：大约每 10 分钟运行一次检查。
* **会话空闲时**：大约每 40 分钟运行一次检查。当您再次输入会话时，Claude Code 会在几分钟内检查上次检查是否早于 10 分钟前。

同步不会延迟启动，因为 Claude 仅在调用该技能时才会等待该技能的下载。因此，短暂的 [ 非交互式 ](<https://code.claude.com/docs/en/headless>) 运行可以在新添加的技能下载之前完成，在这种情况下，稍后的会话会下载它。要进行非交互式运行，请下载您的技能并等待列表回答提示，请将 [`CLAUDE_CODE_SYNC_SKILLS`](<https://code.claude.com/docs/en/env-vars#variables>) 设置为 `1`。在 v2.1.273 之前，终端会话仅在使用此变量集运行的 `-p` 中下载它们。

Claude Code 仅在使用您的 claude.ai 帐户登录的会话中同步，并且 [ 从 Anthropic](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) 获取功能标志。它在这些会话中不同步：

* 不使用 `/login` 存储的登录的会话，例如使用 API 密钥进行身份验证的会话，或者 `ANTHROPIC_AUTH_TOKEN`、`CLAUDE_CODE_OAUTH_TOKEN` 或 `apiKeyHelper` 脚本提供凭据的会话
* 不获取功能标志的会话，例如 Amazon Bedrock 上的会话或您设置的会话 `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`
* [裸模式](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>)下的会话或以`--safe-mode`开始的会话
* 您组织的托管设置 [ 将技能锁定到插件源 ](<https://code.claude.com/docs/en/settings-reference#strictpluginonlycustomization-skills>) 的会话，或者您从 [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 列表开始的会话，其中省略了 `user`

如果您在会话期间使用 `/login` 登录，请重新启动 Claude Code 以开始同步。

早期会话同步的技能保留在磁盘上。 Claude Code 会在登录到同一帐户的后续会话中加载它们，即使它无法访问 claude.ai。

Claude Code 下载同步技能但从不上传它们。如果您或 Claude 编辑 `~/.claude/skills/synced/` 下的文件，则更改不会保存到您的 claude.ai 帐户中，并且稍后的同步可能会覆盖或删除它。要更改同步技能，请在 claude.ai 上更新；下次同步将下载新版本。

要查看同步了哪些技能，请运行 `/skills`。菜单将它们列在 `claude.ai sync` 下。

Anthropic的一些技能，例如`pdf`和`xlsx`，总是同步的。对于其余的，请在 claude.ai 上的技能设置中打开或关闭技能以更改其是否同步。

要停止在计算机上同步，请在用户设置中将 [`syncClaudeAiSkills`](<https://code.claude.com/docs/en/settings-reference#syncclaudeaiskills>) 设置为 `false`。 Claude Code 停止下载，下次启动时会将已同步的技能移动到 `~/.claude/skills/.trash/` 并且不再加载它们。您的组织可以通过关闭 claude.ai 上的技能来关闭每个人的同步。要在保持技能开启的情况下停止同步，可以在[托管设置](<https://code.claude.com/docs/en/managed-settings>)中设置相同的密钥。

如果您的组织在 claude.ai 上关闭技能，Claude Code 会删除下载的技能并且它们会停止加载。删除的技能会移至`~/.claude/skills/.trash/`，您可以在其中恢复文件，直到[保留扫描](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>)将其删除。一旦您的组织重新启用技能，Claude Code 就会下载您在下次同步时启用的技能。

#### 当同步的技能名称与另一个命令匹配时

您可以通过短名称 `/<name>` 或全名称 `/anthropic-skills:<name>` 来调用同步技能。当另一个命令使用短名称时，`/<name>` 运行另一个命令，并且同步技能仅作为 `/anthropic-skills:<name>` 运行。通过本地 `deploy` 技能和同步 `deploy`，`/deploy` 运行本地技能，`/anthropic-skills:deploy` 运行同步技能。在v2.1.269之前，同步技能只有简称。

在`/`菜单、`/skills`和`/context`中，同步技能出现在其简称下，或者在其他命令使用简称时出现在其全名下。在您的会话中运行 `/skills`。列表下方的注释解释了每个丢失短名称的同步技能。如果 `~/.claude/` 中您的个人技能或命令文件之一使用了该名称，该注释还会说明要重命名或删除哪些内容以释放它。

从v2.1.269到v2.1.280，这些列表显示了每个同步技能的全名，而`/skills`没有这样的注释；两者都在v2.1.281中发生了变化。

使用短名称的命令可以是以下任意命令：

* 内置命令或 [ 捆绑技能 ](<#捆绑技能>)，包括在您的会话中不可用的命令，例如在您关闭捆绑技能后
* 任何[本地级别](<#where-skills-live>)的技能或`.claude/commands/`中的文件
* 一个插件技能
* [MCP 提示](<https://code.claude.com/docs/en/mcp#use-mcp-prompts-as-commands>)

Claude Code 为同步技能添加标签，以便您了解它们的来源。 `/skills`菜单和`/context`将`claude.ai sync`下的同步技能分组，`/`命令菜单将它们标记为来自claude.ai。

当比较名称时，Claude Code 会忽略大小写、空格和不可见字符，并将兼容形式（例如全角字母和破折号变体）视为其普通等效形式。例如，名为 `Commit` 的同步技能和名为 `commit` 的本地技能算作同名，因此 `/commit` 会继续运行您的本地技能。

如果名称与另一个字母表中的字母仅有相似之处，则视为不同的名称，`claude.ai sync` 标签是区分这两个名称的方法。这些检查和标签需要 Claude Code v2.1.228 或更高版本。

<h4 id="names-reserved-for-synced-skills">
 为同步技能保留的名称
</h4>

Claude Code 为从 claude.ai 同步的技能保留名称 `anthropic-skills` 以及该命名空间内的每个名称（例如 `anthropic-skills:pdf`），因此同步技能的全名永远不会运行其他任何内容。无论您是否使用 claude.ai 帐户登录，该名称都会在每个会话中保留。

* **技能文件夹、前言`name`、`.claude/commands/`中的文件或子文件夹或[保存的工作流程](<https://code.claude.com/docs/en/workflows#save-the-workflow-for-reuse>)**：它不会加载。 [启动通知](<https://code.claude.com/docs/en/errors#a-skill-command-or-workflow-wasnt-loaded-because-its-name-is-reserved>)命名第一个要重命名或编辑的项目。
* **一个名为`anthropic-skills`**的插件：它加载。当其中一项技能和同步技能都命名为 `<name>` 时，`/anthropic-skills:<name>` 运行同步技能。
* **名为 `anthropic-skills`** 的 MCP 服务器：它已连接并且其工具可以工作，但 [ 其提示不显示为命令 ](<https://code.claude.com/docs/en/mcp#use-mcp-prompts-as-commands>)。重命名 MCP 配置中的服务器以列​​出它们。

#### Claude Code 如何处理同步技能的 frontmatter

Claude Code 对同步技能的 frontmatter 应用两条规则：

* 前面的内容适用于各种会话，因此 `allowed-tools` 授权会经历正常的 [ 权限流程 ](<https://code.claude.com/docs/en/permissions>)。如果您的组织设置了 `allowManagedPermissionRulesOnly`，则补助金 [ 不适用 ](<#当仅适用托管权限规则时>)。
* Claude Code 清理技能提供的显示文本，例如其描述。它会删除控制字符，并且在达到 Claude 的文本（例如描述）中，它还会转义尖括号，因此文本无法模仿 Claude Code 的内部格式。此清理需要 Claude Code v2.1.228 或更高版本。

#### Claude Code 如何处理同步技能的主体

Claude Code 对同步技能主体的作用取决于会话运行的位置：

* 在云会话中，主体保留本地技能的行为，因为会话在隔离的容器中运行。
* 在桌面上的 Cowork 会话中，主体会保留本地技能的行为，只是 Claude Code 将每个 `!` 命令行替换为 [`disableSkillShellExecution` 占位符 ](<#注入动态上下文>)，就像您在此处提供的每个技能一样。
* 在计算机上的任何其他会话中，Claude Code 不会运行 [`!` 命令](<#注入动态上下文>)，不会附加 `@` 引用的文件（以本地技能的方式命名），并且不会替换 `${CLAUDE_PROJECT_DIR}` 和 `${CLAUDE_SESSION_ID}` 占位符，因此`@` 引用和两个占位符都以文字形式到达 Claude。 `!` 命令行也以文字形式到达 Claude，或者当 `disableSkillShellExecution` 开启时作为占位符。此处理需要 Claude Code v2.1.228 或更高版本。

<h3 id="live-change-detection">
 在会话期间编辑技能
</h3>

Claude Code 监视技能目录中的文件更改，[裸模式](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>) 除外。当您在 `~/.claude/skills/`、项目 `.claude/skills/` 或 `--add-dir` 目录中的 `.claude/skills/` 下添加、编辑或删除技能时，Claude Code 将在当前会话中获取更改，而无需重新启动。

如果您创建的顶级技能目录在会话开始时不存在，请运行 [`/reload-skills`](<https://code.claude.com/docs/en/commands#all-commands>) 以获取您放置在那里的技能。 Claude Code 尚未监视该目录，因此每次更改后请再次运行 `/reload-skills`。

实时更改检测仅涵盖 `SKILL.md` 文本。对于同时是[插件](<https://code.claude.com/docs/en/plugins/loading#plugins-shared-through-a-repository>)的技能文件夹，对`hooks/`、`.mcp.json`、`agents/`和`output-styles/`的更改需要`/reload-plugins`才能生效。

### 移除一项技能

如何删除技能取决于它的来源：

* **个人或项目技能**：删除技能目录`~/.claude/skills/<skill-name>/`或`.claude/skills/<skill-name>/`。 Claude Code [将其从当前会话](<#live-change-detection>)的`/skills`中删除；已从中加载的内容 Claude Code 遵循 [ 技能内容生命周期 ](<#技能内容生命周期>)。
* **企业技能**：管理员从[托管设置目录](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>)内的`.claude/skills/`删除技能目录，例如Linux上的`/etc/claude-code/.claude/skills/<skill-name>/`。
* **插件技能**：从`/plugin`菜单或使用`/plugin uninstall <plugin-name>@<marketplace-name>`禁用或卸载提供它的插件。 Claude Code 在 [ 更改应用 ](<https://code.claude.com/docs/en/plugins/cli-reference#reload-plugins>) 或重新启动时卸载插件的技能。
* **从claude.ai同步的技能**：在您[启用它的同一位置关闭您的claude.ai帐户的技能](<#skills-in-cowork-and-cloud-sessions>)。 Claude Code 下次 [ 同步你的技能 ](<#同步技能加载位置>) 时，会将其从 `~/.claude/skills/synced/` 中删除。如果您手动删除该目录，则下次同步时会再次下载该目录，同时该技能在 claude.ai 上保持启用状态。
* **捆绑技能**：将[`disableBundledSkills`](<#捆绑技能>)设置为`true`可关闭捆绑技能，或将[`skillOverrides`](<#从设置中覆盖技能可见性>)中的一项技能设置为`"off"`将其隐藏。

要保留个人或项目技能但阻止 Claude 自行调用它，请在其 frontmatter 中设置 [`disable-model-invocation: true`](<#控制谁调用技能>)，或者当您不想编辑文件时在 [`skillOverrides`](<#从设置中覆盖技能可见性>) 中设置 `"user-invocable-only"`。

## 配置技能

技能通过`SKILL.md`顶部的YAMLfrontmatter和后面的markdown内容进行配置。

### 技能内容类型

技能文件可以包含任何指令，但考虑如何调用它们有助于指导要包含的内容：

**参考内容**增加知识Claude适用于您当前的工作。惯例、模式、风格指南、领域知识。此内容内联运行，因此 Claude 可以将其与您的对话上下文一起使用。

```yaml theme={null}
---
name: api-conventions
description: API design patterns for this codebase
---

When writing API endpoints:
- Use RESTful naming conventions
- Return consistent error formats
- Include request validation
```

**任务内容**为Claude提供特定操作的分步说明，例如部署、提交或代码生成。这些通常是您想要直接使用 `/skill-name` 调用的操作，而不是让 Claude 决定何时运行它们。添加`disable-model-invocation: true`以防止Claude自动触发。下面的示例添加了 `context: fork`，它在自己的子代理上下文中运行技能；请参阅[在子代理中运行技能](<#在子代理中运行技能>)。

```yaml theme={null}
---
name: deploy
description: Deploy the application to production
context: fork
disable-model-invocation: true
---

Deploy the application:
1. Run the test suite
2. Build the application
3. Push to the deployment target
```

保持正文本身简洁。一旦技能加载，其内容 [ 就会在各个回合 ](<#技能内容生命周期>) 中保持在上下文中，因此每一行都是经常性的令牌成本。说明要做什么，而不是叙述如何或为什么，并应用与 [CLAUDE.md 内容 ](<https://code.claude.com/docs/en/best-practices#write-an-effective-claude-md>) 相同的简洁性测试。

### 前言参考

在`SKILL.md`顶部的`---`标记之间使用YAML[frontmatter](<https://code.claude.com/docs/en/glossary#frontmatter>)配置技能，并在结束的`---`后以Markdown形式编写技能的说明。字段名称使用由连字符分隔的小写单词，`when_to_use` 除外。 `.claude/commands/`中的[命令文件](<#where-skills-live>)接受除`name`和`paths`之外的相同字段。此示例设置四个字段：

```yaml theme={null}
---
name: my-skill
description: What this skill does
disable-model-invocation: true
allowed-tools: Read Grep
---

Your skill instructions here...
```

所有字段都是可选的。仅推荐`description`，以便Claude知道何时使用该技能。字段名称必须与表完全匹配，包括连字符：Claude Code 忽略它无法识别的字段而不报告错误。

仅当开头 `---` 是文件的第一行时，Claude Code 才会读取 frontmatter。否则，它将整个文件（包括 `---` 标记）视为技能内容。如果标记之间的 YAML 无法解析，则技能仍会在未设置字段的情况下加载；请参阅[技能未触发](<#技能不触发>)查找并修复错误。

除了 `true` 和 `false` 之外，布尔字段还接受任何字母大小写的 `yes`、`no`、`on`、`off`、`1` 和 `0`。在v2.1.218之前，Claude Code仅识别`true`和`false`。

|领域|必填|描述|
| :- | :- | :- |
| `name` |否|命令名称显示在`/`菜单中。默认为目录名称。请参阅[技能如何获取其命令名称](<#技能如何获取其命令名称>)，了解字段如何与您键入的名称交互以调用技能。|
| `description` |推荐|该技能的作用是什么以及何时使用它。 Claude用它来决定何时应用该技能。如果省略，则使用 Markdown 内容的第一个非空行。将关键用例放在第一位：组合的 `description` 和 `when_to_use` 文本在技能列表中的 1,536 字符处被截断，以减少上下文使用。|
| `when_to_use` |否|Claude 何时应调用该技能的其他上下文，例如触发短语或示例请求。附加到技能列表中的 `description` 并计入 1,536 字符上限。|
| `argument-hint` |否|自动完成期间显示的提示以指示预期的参数。示例：`[issue-number]` 或 `[filename] [format]`。|
| `arguments` |否|技能内容中 [`$name` 替换 ](<#可用的字符串替换>) 的命名位置参数。接受空格分隔的字符串或 YAML 列表。名称按顺序映射到参数位置。|
| `disable-model-invocation` |否|设置为`true`可防止Claude自动加载该技能。用于您想要使用 `/name` 手动触发的工作流程。还可以防止技能 [ 预加载到子代理 ](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>) 中。从 v2.1.196 开始，当 [ 计划任务 ](<https://code.claude.com/docs/en/scheduled-tasks>) 以技能作为提示触发时，也会阻止技能运行。默认值：`false`。|
| `user-invocable` |否|当只有 Claude 应该调用该技能时，设置为 `false`：Claude Code 将其从 `/` 菜单中隐藏，并且当您键入 `/name` 时不会运行它。用于背景知识的用户不应直接调用。默认值：`true`。|
| `allowed-tools` |否|工具Claude在调用该技能的回合中无需征求许可即可使用。当您发送下一条消息时，赠款就会清除。接受空格或逗号分隔的字符串，或 YAML 列表。请参阅[预先批准技能的工具](<#预先批准技能的工具>)。|
| `disallowed-tools` |否|当该技能处于活动状态时，工具已从 Claude 的可用池中移除。用于不应该调用某些工具的自主技能，例如用于后台循环的 `AskUserQuestion`。接受空格或逗号分隔的字符串，或 YAML 列表。当您发送下一条消息时，该限制将被清除。与拒绝规则一样，该字段无法在任何其他工具保留的情况下删除 [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>)。|
| `model` |否|该技能激活时使用的模型。覆盖适用于当前回合的剩余时间，并且不会保存到设置中。当您发送下一个提示时，会话模型将恢复。接受与 [`/model`](<https://code.claude.com/docs/en/model-config>) 或 `inherit` 相同的值以保持活动模型。不使用组织的 [`availableModels`](<https://code.claude.com/docs/en/model-config#restrict-model-selection>) 白名单中排除的值，并且会话将保留其当前模型。在 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 和 [ 计划模式下，当分类器检查命令 ](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>) 时，也不会使用自动模式不支持的模型，并且会话保留其当前模型。对于 `context: fork`，该值会设置 [ 分叉子代理的模型 ](<#在子代理中运行技能>)，并且排除的值遵循 [ 与子代理模型覆盖 ](<https://code.claude.com/docs/en/model-config#restrict-model-selection>) 相同的规则。|
| `effort` |否|[此技能激活时的努力程度](<https://code.claude.com/docs/en/model-config#adjust-effort-level>)。覆盖会话工作量级别。当您省略它时，级别来自 [ 努力分辨率顺序 ](<https://code.claude.com/docs/en/model-config#adjust-effort-level>)。选项：`low`、`medium`、`high`、`xhigh`、`max`；可用级别取决于型号。|
| `context` |否|设置为 `fork` 以在分叉子代理上下文中运行。请参阅[在子代理中运行技能](<#在子代理中运行技能>)。|
| `agent` |否|设置 `context: fork` 时使用哪种子代理类型。|
| `background` |否|仅适用于 `context: fork`。设置为`false`以在调用技能的回合中等待分叉子代理的结果，而不是[在后台运行它](<#在子代理中运行技能>)。默认值：`true`。需要 Claude Code v2.1.218 或更高版本。|
| `hooks` |否|Claude Code 在调用技能时注册的挂钩，并在会话的剩余时间内保持运行。配置格式和`once`选项参见[技巧中的Hooks和agents](<https://code.claude.com/docs/en/hooks#hooks-in-skills-and-agents>)。|
| `paths` |否|限制此技能激活时间的全局模式。接受逗号分隔的字符串或 YAML 列表。设置后，Claude 仅在处理与模式匹配的文件时自动加载技能。使用与 [ 路径特定规则 ](<https://code.claude.com/docs/en/memory#path-specific-rules>) 相同的格式。|
| `shell` |否|用于`` !`command` ``和` ```的外壳！ ` blocks in this skill. Accepts `bash` (default) or `powershell`. Setting `powershell` runs inline shell commands via PowerShell when the [PowerShell tool](/en/tools-reference#powershell-tool) is enabled: it's on by default on Windows without Git Bash, on by default with Git Bash for claude.ai and Console accounts, and needs `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` in Amazon Bedrock, Google Cloud's Agent Platform, and Microsoft Foundry sessions and on macOS, Linux, and WSL. Set it to `0` 关闭该工具。|
| `metadata` |否|您自己的键值数据的自由格式 YAML 映射，例如权利或目录字段，由您自己的工具从 `SKILL.md` 读取。 Claude Code 不会对其内容进行操作，并且会删除不是地图的值。不要重复使用 frontmatter 字段名称（例如 `paths`）作为键。|
| `license` |否|涵盖该技能的许可证。 [Agent Skills](<https://agentskills.io>) 规范的一部分；参见[在Claude Code](<#在外部使用技能前线claude-code>)之外使用技能frontmatter。 Claude Code 接受该字段，但不对其进行操作。|
| `compatibility` |否|技能的环境要求，例如[Agent Skills](<https://agentskills.io>)规范中定义的预期产品或系统先决条件；参见[在Claude Code](<#在外部使用技能前线claude-code>)之外使用技能frontmatter。接受最多 500 个字符的字符串。 Claude Code 接受该字段，但不对其进行操作。|

#### 在外部使用技能前线Claude Code

Claude Code 接受上表中的每个字段。在 Claude Code 之外，您只能使用 [Agent Skills](<https://agentskills.io>) 规范中的字段：

|流通路径|您可以使用的 Frontmatter 字段|
| :- | :- |
|Claude Code [任意级别](<#where-skills-live>)技能，包括[插件](<https://code.claude.com/docs/en/plugins/overview>)技能|上表中的每个字段|
|claude.ai 技能上传、技能 API 以及来自 [anthropics/skills](<https://github.com/anthropics/skills>) 的 `package_skill.py` 打包| `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools` |

当您为 claude.ai 帐户启用个人技能时，例如在 [Cowork 和云会话 ](<#skills-in-cowork-and-cloud-sessions>) 和例程中使用它，您会将其上传到 claude.ai，因此适用相同的规则。

如果您包含规范不允许的任何字段，则打包或上传会失败并出现硬错误，而不是忽略该字段：

```
Unexpected key(s) in SKILL.md frontmatter: argument-hint. Allowed properties are: allowed-tools, compatibility, description, license, metadata, name
```

将 frontmatter 限制为规范的六个字段可以避免上述意外键错误。 [Agent Skills 规范 ](<https://agentskills.io>) 和 [ 技能 API 要求 ](<https://docs.claude.com/en/api/skills-guide>) 定义了这些路径验证的所有其他内容。仅 Claude Code 的正文功能，例如 [ 动态上下文注入 ](<#注入动态上下文>)，在 claude.ai 聊天中或通过 API 不起作用。 Claude Code 接受所有六个字段，因此遵循规范的 frontmatter 会在 Claude Code 中加载，无需更改。

#### 技能如何获取其命令名称

您键入的用于调用技能的命令来自技能文件所在的位置，对于技能目录和插件技能，来自 frontmatter `name` 字段。在个人或项目技能目录中，`name` 设置 `/` 菜单显示且您键入的命令，除非另一个命令已使用该名称。目录名称也会调用该技能。在插件技能中，`name` 设置命令的最后一段，并且插件前缀保持不变。

下表显示了每个布局的命令名称的来源：

|技能位置|命令名称来源|示例|
| :- | :- | :- |
|`~/.claude/skills/`或`.claude/skills/`下的技能目录|Frontmatter `name` 或目录名称|`.claude/skills/deploy-staging/SKILL.md` → `/deploy-staging`，或 `/deploy` 与 `name: deploy`|
|[嵌套](<#where-skills-live>) `.claude/skills/`目录，当目录名与其他技能冲突时|相对于工作目录的子目录路径，然后是技能目录名称| `apps/web/.claude/skills/deploy/SKILL.md` → `/apps/web:deploy` |
|`.claude/commands/`下的文件|不带扩展名的文件名| `.claude/commands/deploy.md` → `/deploy` |
|`.claude/commands/`子目录中的文件|相对于`commands/`的子目录路径，将每个`/`替换为`:`，然后是不带扩展名的文件名| `.claude/commands/frontend/component.md` → `/frontend:component` |
|插件`skills/`子目录|Frontmatter `name` 或目录名称，由插件命名|`my-plugin/skills/review/SKILL.md` → `/my-plugin:review`，或 `/my-plugin:fancy` 与 `name: fancy`|
|插件根目录`SKILL.md`|Frontmatter `name`，以插件目录名称作为后备|`my-plugin/SKILL.md` 与 `name: review` → `/my-plugin:review`。参见[插件根处的单个技能](<https://code.claude.com/docs/en/plugins/components#skills>)|
|技能[从claude.ai同步](<#how-synced-skills-behave>)|您 claude.ai 帐户上的技能名称，前缀为 `anthropic-skills:`|帐户技能 `deploy` → `/anthropic-skills:deploy` 或 `/deploy`，而没有其他命令使用该名称|

在插件技能中，前面的内容 `name` 替换了命令最后一段中的目录名称，因此 `my-plugin/skills/review/SKILL.md` 和 `name: fancy` 变为 `/my-plugin:fancy`。裸露的 `/fancy` 也会调用该技能，除非另一个命令已使用该名称。如果您编写的 `name` 已经以插件自己的前缀开头，则 Claude Code 不会在 v2.1.246 或更高版本上再次添加前缀。例如，`name: my-plugin:fancy` 仍变为 `/my-plugin:fancy`。从 v2.1.216 到 v2.1.245，当 `name` 已经携带前缀时，Claude Code 将前缀加倍。

在 [ 非交互式会话 ](<https://code.claude.com/docs/en/headless>) 中，名称 `help` 和 `feedback` 不为其仅终端内置命令保留，因此具有这些名称之一的插件技能将其裸命令保留在那里。即使命令无法在这些会话中运行，每个其他仅终端内置命令的名称（例如 `/login`）仍保留。

对于插件根 `SKILL.md`，没有可以从中获取名称的技能目录，因此 `name` 提供了整个最后一段。如果没有 `name` 字段，Claude Code 会回退到插件的目录名称。

#### 可用的字符串替换

技能支持技能内容中动态值的字符串替换：

|变量|描述|
| :- | :- |
| `$ARGUMENTS` |调用该技能时所有参数都通过。当没有占位符接收参数时，Claude Code 将它们附加为 `ARGUMENTS: <value>`。请参阅[将参数传递给技能](<#将参数传递给技能>)。|
| `$ARGUMENTS[N]` |通过基于 0 的索引访问特定参数，例如第一个参数为 `$ARGUMENTS[0]`。|
| `$N` |`$ARGUMENTS[N]` 的简写，例如第一个参数为 `$0` 或第二个参数为 `$1`。|
| `$name` |[`arguments`](<#前言参考>) frontmatter 列表中声明的命名参数。名称按顺序映射到位置，因此使用 `arguments: [issue, branch]` 时，占位符 `$issue` 扩展到第一个参数，`$branch` 扩展到第二个参数。|
| `${CLAUDE_SESSION_ID}` |当前会话 ID。对于记录、创建特定于会话的文件或将技能输出与会话相关联非常有用。|
| `${CLAUDE_EFFORT}` |当前努力级别：`low`、`medium`、`high`、`xhigh` 或 `max`。使用此功能可以使技能说明适应主动努力设置。|
| `${CLAUDE_SKILL_DIR}` |包含技能的`SKILL.md`文件的目录。对于插件技能，这是插件内技能的子目录，而不是插件根目录。在 bash 注入命令中使用它来引用与技能捆绑的脚本或文件，无论当前工作目录如何。|
| `${CLAUDE_PROJECT_DIR}` |项目根目录。这与 [hooks](<https://code.claude.com/docs/en/hooks#reference-scripts-by-path>) 和 MCP 服务器接收为 `CLAUDE_PROJECT_DIR` 的路径相同。使用它来引用项目本地脚本或文件，例如 `${CLAUDE_PROJECT_DIR}/.claude/hooks/helper.sh`，与技能的安装位置无关。|
| `${CLAUDE_PLUGIN_ROOT}` |插件的安装目录。仅替换插件技能。使用它来引用插件中任何位置捆绑的脚本或文件，包括插件技能之间共享的资源。请参阅[插件环境变量](<https://code.claude.com/docs/en/plugins/manifest-reference#environment-variables>)。|
| `${CLAUDE_PLUGIN_DATA}` |插件的 [ 持久数据目录 ](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>)，在插件更新后仍然存在。仅替换插件技能。使用它来引用已安装的依赖项、生成的文件或必须比更新更有效的缓存。|

Claude Code 在两个地方替换了 `${CLAUDE_SKILL_DIR}` 和 `${CLAUDE_PROJECT_DIR}`：技能的 Markdown 内容以及 [`allowed-tools`](<#前言参考>) frontmatter 中的 Bash 规则。在插件技能中，Claude Code在相同的两个位置替换了`${CLAUDE_PLUGIN_ROOT}`和`${CLAUDE_PLUGIN_DATA}`。在两个地方使用相同的变量可以让技能在没有权限提示的情况下运行捆绑的脚本。以下技能显示了该模式：

```yaml theme={null}
---
name: render-chart
description: Render a chart from a CSV file
allowed-tools: Bash(${CLAUDE_SKILL_DIR}/scripts/render.sh *)
---

Run `${CLAUDE_SKILL_DIR}/scripts/render.sh <csv-file>` to render the chart.
```

如果此技能安装在 `~/.claude/skills/render-chart/`，则两次出现的 `${CLAUDE_SKILL_DIR}` 都会扩展到该目录。然后，`allowed-tools` 规则与技能主体告诉 Claude 运行的确切命令相匹配，因此脚本会在没有提示的情况下运行。

`${CLAUDE_PROJECT_DIR}` 替换需要 Claude Code v2.1.196 或更高版本。

索引参数使用 shell 样式的引用，因此将多字值括在引号中以将它们作为单个参数传递。例如，`/my-skill "hello world" second` 使 `$0` 扩展为 `hello world`，将 `$1` 扩展为 `second`。 `$ARGUMENTS` 占位符始终扩展为键入的完整参数字符串。

没有相应参数的索引占位符（例如仅传递一个参数时的 `$2`）在内容中保持不变。 [`arguments`](<#前言参考>) frontmatter 中没有匹配参数的命名占位符将扩展为空字符串。

如果您传递的参数值本身包含 `$1` 或 `$ARGUMENTS` 等文本，则 Claude Code 会将其作为文字文本插入并且不会扩展它。例如，如果技能正文包含 `Summarize $0` 并且您运行 `/summarize "$ARGUMENTS from yesterday"`，则 Claude 会收到 `Summarize $ARGUMENTS from yesterday`。 Claude Code 在插入参数后仍会替换 `${CLAUDE_*}` 变量，例如 `${CLAUDE_SKILL_DIR}`。

要在数字、`ARGUMENTS` 或声明的参数名称（例如散文中的 `$1.00`）之前包含文字 `$`，请使用反斜杠对其进行转义：`\$1.00`。任何其他 `$` 之前的反斜杠保持不变。令牌之前只有一个反斜杠会转义。双反斜杠（例如 `\\$1`）会将两个反斜杠保留在原位，并且 `$1` 仍会扩展为参数值。反斜杠转义仅涵盖这些参数占位符。反斜杠不会阻止在应用变量时替换 `${CLAUDE_*}` 变量。

**使用替换的示例：**

```yaml theme={null}
---
name: session-logger
description: Log activity for this session
---

Log the following to logs/${CLAUDE_SESSION_ID}.log:

$ARGUMENTS
```

### 添加支持文件

技能的目录中可以包含多个文件。这使得 `SKILL.md` 专注于要点，同时让 Claude 仅在需要时访问详细的参考资料。大型参考文档、API 规范或示例集合不需要在每次技能运行时加载到上下文中。

```text theme={null}
my-skill/
├── SKILL.md (required - overview and navigation)
├── reference.md (detailed API docs - loaded when needed)
├── examples.md (usage examples - loaded when needed)
└── scripts/
    └── helper.py (utility script - executed, not loaded)
```

参考 `SKILL.md` 中的支持文件，以便 Claude 知道每个文件包含什么以及何时加载它：

```markdown theme={null}
## Additional resources

- For complete API details, see [reference.md](reference.md)
- For usage examples, see [examples.md](examples.md)
```



**提示：**

将 `SKILL.md` 保留在 500 行下。将详细的参考材料移至单独的文件中。


### 控制谁调用技能

默认情况下，你和Claude都可以调用任何技能。您可以输入`/skill-name`直接调用它，Claude可以在与您的对话相关时自动加载它。两个 frontmatter 字段可以让您限制这一点：

* **`disable-model-invocation: true`**：Claude无法单独调用该技能。将此用于有副作用的工作流程或您想要控制时序的工作流程，例如 `/commit`、`/deploy` 或 `/send-slack-message`。您不希望 Claude 因为您的代码看起来已准备就绪而决定部署。

* **`user-invocable: false`**：只有Claude可以调用该技能。将此用于无法作为命令操作的背景知识。 `legacy-system-context` 技能解释了旧系统的工作原理。 Claude 在相关时应该知道这一点，但 `/legacy-system-context` 对于用户来说并不是一个有意义的操作。

此示例创建部署技能。如果设置了`disable-model-invocation: true`，Claude无法自动运行技能：

```yaml theme={null}
---
name: deploy
description: Deploy the application to production
disable-model-invocation: true
---

Deploy $ARGUMENTS to production:

1. Run the test suite
2. Build the application
3. Push to the deployment target
4. Verify the deployment succeeded
```

如果 Claude 无论如何都尝试，Claude Code 会阻止调用并指示它不要以其他方式重现部署步骤，因此预计 Claude 建议自己运行 `/deploy`。

以下是这两个字段如何影响调用和上下文加载：

|前题|您可以调用|Claude可以调用|当加载到上下文中时|
| :- | :- | :- | :- |
|（默认）|是的|是的|描述始终在上下文中，调用时会加载全部技能|
| `disable-model-invocation: true` |是的|不是靠它自己|描述不在上下文中，调用时会加载全部技能|
| `user-invocable: false` |否|是的|描述始终在上下文中，调用时会加载全部技能|



**注释：**

在常规会话中，技能描述会加载到上下文中，以便 Claude 知道可用的内容，但完整的技能内容仅在调用时加载。 [具有预加载技能的子代理](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>)的工作方式有所不同：完整的技能内容在启动时注入。


#### 技能名称写在哪里

要直接运行技能，请将其名称放在消息的开头。纯文本后，名称赋予 Claude 运行技能的权限，但不运行它：

|哪里|示例|会发生什么|
| :- | :- | :- |
|在您的消息的开头| `/deploy staging` |Claude Code直接运行技能|
|纯文本之后，作为一个单独的单词，不附加标点符号| `go ahead and /deploy to staging` |没有什么是直接运行的。该名称被视为您对该消息的许可：Claude可以在响应时运行该技能，并根据您的措辞判断您是否要求它执行|

要在不允许跑动的情况下写下该技能，请省略斜杠。

### 技能内容生命周期

当您或 Claude 调用技能时，呈现的 `SKILL.md` 内容作为单个消息进入对话，并在以后的回合中保留在那里。这种持久性适用于技能的指令，而不是其权限：当您发送下一条消息时，[`allowed-tools`](<#预先批准技能的工具>) 授予会清除。 Claude Code 不会在以后的回合中重新读取技能文件，因此编写应在整个任务中应用的指导作为长期指令而不是一次性步骤。

当 Claude 重新调用其渲染内容与上下文中已有副本相同的技能时，Claude Code 会添加一个简短的注释，表明该技能已加载，而不是内容的第二个副本。当呈现的内容不同时，由于参数更改或 [ 动态上下文 ](<#注入动态上下文>) 命令产生新的输出，Claude Code 会再次附加完整内容。

[自动压缩](<https://code.claude.com/docs/en/how-claude-code-works#when-context-fills-up>)在代币预算内转发调用的技能。当对话被总结为自由上下文时，Claude Code 在摘要后重新附加每个技能的最近调用，并保留每个技能的第一个 5,000 标记。重新附加的技能共享 25,000 代币的合并预算。 Claude Code 从最近调用的技能开始填充此预算，因此如果您在一个会话中调用了多个技能，则在压缩后可以完全删除较旧的技能。

如果 Claude 在会话中途停止关注技能，请参阅 [Claude 停止关注技能 ](<#claude-停止跟随技能>)。

### 预先批准技能的工具

`allowed-tools` 字段授予在调用技能的回合期间使用列出的工具的权限，因此 Claude 可以使用它们而无需提示您批准。当您发送下一条消息时，即使技能内容 [ 保留在上下文中 ](<#技能内容生命周期>)，补助金也会清除；再次调用该技能会在该回合重新应用该技能。它不限制哪些工具可用：每个工具仍然可调用，并且您的 [ 权限设置 ](<https://code.claude.com/docs/en/permissions>) 仍然管理未列出的工具。要预先批准整个会话而不是单个回合的工具，请向这些权限设置添加允许规则。

工作空间信任不会限制这一领域。 Claude Code 即使在您从未信任的文件夹中运行的 `-p` 也会应用项目技能的 `allowed-tools`。技能可以授予自己广泛的工具访问权限，因此在运行 Claude Code 之前，请先查看签入存储库的技能的 `allowed-tools`。要在整个组织的存储库技能中保留该字段，请参阅 [仅适用托管权限规则时 ](<#当仅适用托管权限规则时>)。

这项技能可以让 Claude 每当您调用它时都无需每次使用批准即可运行 git 命令：

```yaml theme={null}
---
name: commit
description: Stage and commit the current changes
disable-model-invocation: true
allowed-tools: Bash(git add *) Bash(git commit *) Bash(git status *)
---
```

要在技能处于活动状态时从 Claude 的可用池中删除工具，请将它们列在技能的 frontmatter 的 `disallowed-tools` 中。当您发送下一条消息时，该限制将被清除。与拒绝规则一样，该字段无法在任何其他工具保留的情况下删除 [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>)。要阻止所有技能和提示中的工具，请在 [ 权限设置 ](<https://code.claude.com/docs/en/permissions>) 中添加拒绝规则。

#### 当仅适用托管权限规则时

当您的组织在托管设置中设置 `allowManagedPermissionRulesOnly` 时，Claude Code 会忽略项目和个人技能中的 `allowed-tools`，以及在 [其他来源中设置的条目列表 ](<https://code.claude.com/docs/en/settings-reference#allowmanagedpermissionrulesonly>)。这需要 Claude Code v2.1.282 或更高版本。

受影响的技能列出的工具会通过组织的托管规则和正常权限提示。运行 `/status` 以列出 `allowed-tools` Claude Code 迄今为止在会话中忽略的每个技能。技能中没有托管规则允许的注入命令遵循 [对注入命令的权限检查 ](<#对注入命令的权限检查>)。

### 将参数传递给技能

你和Claude都可以在调用技能时传递参数。参数可通过 `$ARGUMENTS` 占位符获得。

此技能按数量修复了 GitHub 问题。 `$ARGUMENTS` 占位符将替换为技能名称后面的内容：

```yaml theme={null}
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---

Fix GitHub issue $ARGUMENTS following our coding standards.

1. Read the issue description
2. Understand the requirements
3. Implement the fix
4. Write tests
5. Create a commit
```

当您运行 `/fix-issue 123` 时，Claude 收到“按照我们的编码标准修复 GitHub 问题 123...”

如果您调用带有参数的技能，但技能内容中没有占位符接收参数，则 Claude Code 会将 `ARGUMENTS: <your input>` 附加到技能内容的末尾，以便 Claude 仍然可以看到您键入的内容。占位符是 `$ARGUMENTS`、索引形式（例如 `$1`）或命名参数。其位置上没有参数的索引占位符将保留为文字文本，并且不算作接收文本。即使命名占位符的位置没有参数，它也会计数，因为它会扩展为空字符串。

您还可以在一条消息的开头堆叠多项技能。输入 `/write-tests /fix-issue 123` 会加载这两个技能，并将尾随文本 `123` 作为 `$ARGUMENTS` 传递给每个技能。

Claude Code 扩展第一个技能，并在其后叠加最多五个技能。扩展在第一个不是内联用户可调用技能的标记处停止，因此作为 [ 分叉子代理 ](<#在子代理中运行技能>) 运行的技能（例如 [`/code-review`](<https://code.claude.com/docs/en/code-review#review-a-diff-locally>)），或其参数本身可以以斜杠命令开头的技能（例如 `/loop`）也会在那里结束运行。该标记及其后面的所有内容都成为每个扩展技能的参数文本。 `/code-review` 作为 v2.1.218 的分叉子代理运行；在早期版本中，它内联运行并堆叠。

要按位置访问各个参数，请使用 `$ARGUMENTS[N]` 或更短的 `$N`：

```yaml theme={null}
---
name: migrate-component
description: Migrate a component from one language to another
---

Migrate the $ARGUMENTS[0] component from $ARGUMENTS[1] to $ARGUMENTS[2].
Preserve all existing behavior and tests.
```

运行 `/migrate-component SearchBar JavaScript TypeScript` 会将 `$ARGUMENTS[0]` 替换为 `SearchBar`，将 `$ARGUMENTS[1]` 替换为 `JavaScript`，将 `$ARGUMENTS[2]` 替换为 `TypeScript`。使用 `$N` 简写的相同技巧：

```yaml theme={null}
---
name: migrate-component
description: Migrate a component from one language to another
---

Migrate the $0 component from $1 to $2.
Preserve all existing behavior and tests.
```

## 高级模式

### 注入动态上下文

`` !`<command>` `` 语法在技能内容发送到 Claude 之前运行 shell 命令。命令输出替换了占位符，因此 Claude 接收实际数据，而不是命令本身。当技能 [ 从您的 claude.ai 帐户 ](<#claude-code-如何处理同步技能的主体>) 同步时，Claude Code 不会在您的计算机上运行这些命令。此限制需要 Claude Code v2.1.228 或更高版本。

此技能通过使用 GitHub CLI 获取实时 PR 数据来总结拉取请求。 `` !`gh pr diff` `` 和其他命令首先运行，它们的输出被插入到提示符中：

```yaml theme={null}
---
name: pr-summary
description: Summarize changes in a pull request
context: fork
agent: Explore
allowed-tools: Bash(gh *)
---

## Pull request context
- PR diff: !`gh pr diff`
- PR comments: !`gh pr view --comments`
- Changed files: !`gh pr diff --name-only`

## Your task
Summarize this pull request...
```

替换在原始文件上运行一次。命令输出以纯文本形式插入，并且不会重新扫描进一步的 `` !`<command>` `` 占位符，因此命令无法发出占位符以供后续传递扩展。

仅当 `!` 出现在行首或紧接空格之后时，才能识别内联形式。如果 `!` 后面跟着另一个字符（如 `` KEY=!`cmd` ``），则占位符将保留为文字文本，并且命令不会运行。

对于多行命令，请使用以 ` ``` 打开的隔离代码块！ ` 而不是内联形式：

````markdown theme={null}
## Environment
```!
node --version
git status --short
```
````

要对来自用户、项目、插件或 [ 附加目录 ](<#skills-from-additional-directories>) 源的技能和自定义命令禁用此行为，请在 [ 设置 ](<https://code.claude.com/docs/en/settings>) 中设置 `"disableSkillShellExecution": true`。每个命令都被替换为 `[shell command execution disabled by policy]` 而不是运行。捆绑和管理的技能不受影响。此设置在 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 中最有用，用户无法覆盖它。

Claude Code 当这些命令出现在技能中时，切勿在您的计算机上运行这些命令 [从您的 claude.ai 帐户同步](<#how-synced-skills-behave>)，无论此设置如何。该限制要求 Claude Code v2.1.228 或稍后。 [如何 Claude Code 处理同步技能的主体](<#claude-code-如何处理同步技能的主体>) 说什么 Claude 在各种会话中代替命令接收。



**提示：**

要在技能运行时请求更深入的推理，请在技能内容中的任意位置包含 `ultrathink`。请参阅[使用 ultrathink 进行一次性深度推理](<https://code.claude.com/docs/en/model-config#use-ultrathink-for-one-off-deep-reasoning>)。


#### 注入命令如何运行

Claude Code 从技能的 frontmatter 和您的环境中的 `shell` 键选择运行技能注入命令的工具。每个组合都通过 Bash 工具或 PowerShell 工具运行命令，但调用完全失败的组合除外：

* `shell: powershell`，启用 [PowerShell 工具 ](<https://code.claude.com/docs/en/tools-reference#powershell-tool>)：命令通过 PowerShell 工具运行。
* `shell: bash` 当 bash 不可用时：在任何命令运行之前调用都会失败。这种情况发生在没有 Git Bash 的 Windows 上。 Claude Code 显示``Skill <name> requires bash (`shell: bash` in frontmatter) but Git Bash was not found``。
* 任何其他组合：当 bash 可用时，命令通过 Bash 工具运行。如果不是，他们会运行 PowerShell 工具。

任一工具运行命令的方式与运行 Claude 自己的 shell 命令的方式相同。它们共享工作目录、超时和输出处理：

* **工作目录**：Claude Code在会话shell的当前工作目录中运行每个命令。当 Claude 运行 `cd` 时，该目录会移动。在每次都必须以相同方式解析的路径中使用 [`${CLAUDE_SKILL_DIR}` 或 `${CLAUDE_PROJECT_DIR}`](<#可用的字符串替换>)。
* **stderr**：使用默认的`bash` shell，Claude Code将stderr合并到stdout中。命令写入 stderr 的任何内容都会出现在注入的文本中。
* **超时**：每个命令在Bash工具的默认2分钟[超时](<https://code.claude.com/docs/en/tools-reference#timeout-and-output-limits>)下运行。当 Bash 工具 [ 将超时命令移至后台 ](<https://code.claude.com/docs/en/tools-reference#foreground-commands-that-move-to-the-background>) 时，技能仍会呈现。注入的文本报告移​​动并命名后台任务和收集命令输出的文件。当命令是 Bash 工具从不自动后台运行时，Claude Code 在超时时杀死它。该故障 [ 中止调用 ](<#当注入命令失败时>)。
* **输出大小**：超过 Bash 工具内联上限的输出以文件路径加上简短预览的形式到达，而不是截断的文本。 [输出限制](<https://code.claude.com/docs/en/tools-reference#output-limits>)涵盖了上限以及如何调整每个边界。

PowerShell 工具对其运行的命令应用相同的超时、后台和输出上限行为。有关详细信息，请参阅 [PowerShell 工具 ](<https://code.claude.com/docs/en/tools-reference#powershell-tool>) 部分。

#### 当注入命令失败时

失败的命令会中止整个技能调用，而不仅仅是它自己的占位符。 Claude从未看到该调用的技能内容。中止显示`Shell command failed for pattern "..."`。错误消息包括 `[stderr]` 下的命令输出。

使用默认的 `bash` shell，任何非零退出代码都将被视为失败。一项剥离适用：Claude Code 将 [ 搜索和比较命令 ](<https://code.claude.com/docs/en/tools-reference#output-limits>) 的退出代码 1 视为正常结果，并注入其输出。即使对于这些命令，2 或更高的退出代码也会失败。

哪些命令会被排除取决于 shell：

* 默认 `bash` shell：[输出限制](<https://code.claude.com/docs/en/tools-reference#output-limits>)下列出的命令
* `shell: powershell`，当启用PowerShell工具时：[不同的集合](<https://code.claude.com/docs/en/tools-reference#shell-selection-in-settings-hooks-and-skills>)，其中包括`grep`和`git diff`，但不包括`find`或`diff`

使用默认的 `bash` shell，将 `|| true` 附加到您希望以非零值退出的任何其他命令。一个示例是当发现问题时退出 1 的检查脚本。

#### 对注入命令的权限检查

技能呈现时，注入的命令永远不会提示许可。 Claude Code 首先根据您的 [ 权限规则 ](<https://code.claude.com/docs/en/permissions>) 检查每一项。拒绝规则匹配的命令会中止带有 `Shell command permission check failed for pattern "..."` 的调用。

在 [ 自动模式 ](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>) 之外，当命令的权限检查返回除允许以外的任何内容时，Claude Code 将中止调用并出现相同的错误。这包括通常会询问您的规则。要防止不匹配的命令在此处中止，请使用 [`allowed-tools`](<#预先批准技能的工具>) 预先批准它。如果您的组织将权限规则限制为托管设置，请参阅[仅适用托管权限规则时](<#当仅适用托管权限规则时>)。拒绝和询问规则仍然优先于 `allowed-tools`。请参阅[管理权限](<https://code.claude.com/docs/en/permissions#manage-permissions>)。

在自动模式下，原本需要您批准的命令不会中止调用。该技能带有指令说明 Claude 首先运行命令，并且 Claude自己的电话然后通过 [自动模式的常规检查](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>)。调用仍然中止 [分叉技能](<#在子代理中运行技能>) 设定 `agent`，并且在一个会话中 Claude 没有 [运行注入命令的 shell 工具](<#注入命令如何运行>).

### 在子代理中运行技能

当您希望一项技能单独运行时，请将 `context: fork` 添加到您的 frontmatter 中。 Claude Code 启动`agent` 字段中设置的类型的新子代理，并为其提供技能内容作为提示。子代理看不到您的对话历史记录，因此该技能的指令必须独立存在。



**注释：**

  尽管名字如此，但技能 `context: fork` 不运行在 [当前对话的分支](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>)，这会将您到目前为止讨论的所有内容交给子代理。当任务依赖于该历史记录时，分叉对话而不是使用 `context: fork`.


分叉子代理在 [ 后台 ](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>) 中运行：您在它运行时继续工作，并且在完成时其结果到达您的对话中。在 frontmatter 中设置 `background: false` 来等待调用技能的回合的结果。在v2.1.218之前，分叉技能总是阻塞回合直到完成。

Claude Code 也会等待结果，即使技能未设置 `background: false`，在以下情况下：

* 在非交互模式下，带有`-p`标志或AgentSDK
* 当您将 [`CLAUDE_CODE_DISABLE_BACKGROUND_TASKS`](<https://code.claude.com/docs/en/env-vars>) 设置为 `1` 时，这也会关闭所有其他后台任务功能
* 当您调用分叉技能而同一技能的早期调用仍在运行时
* 当 [ 计划任务 ](<https://code.claude.com/docs/en/scheduled-tasks>) 以技能作为提示触发时

后台分叉还可以使用适用于后台子代理的 [ 更窄的工具集运行 ](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>)：该技能的子代理是常规 agent 类型，因此分叉对话的子代理的豁免不包括在内。如果您的技能步骤取决于该组之外的工具，请设置 `background: false` 以保留完整的工具组。

在后台运行的分叉技能会将其编辑应用到会话的 [ 检查点 ](<https://code.claude.com/docs/en/checkpointing>) 之外，因此 `/rewind` 不会撤消它们；使用 git 恢复它们。



**注意：**

`context: fork` 仅对具有明确指示的技能才有意义。如果您的技能包含诸如“使用这些 API 约定”之类的指南，而没有任务，则子代理会收到指南，但没有可操作的提示，并且返回时没有有意义的输出。


技能和[子代理](<https://code.claude.com/docs/en/sub-agents>)在两个方向上协同工作：

|方法|系统提示|任务|还加载|
| :- | :- | :- | :- |
|技能`context: fork`|来自 agent 类型|SKILL.md内容|CLAUDE.md，根据 agent 的 [ 启动上下文 ](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>)|
|具有 `skills` 字段的子代理|子代理的降价主体|Claude的委托消息|预加载技能 + CLAUDE.md，根据子代理的 [ 启动上下文 ](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>)|

使用 `context: fork`，您可以在技能中编写任务并选择 agent 类型来执行它。内置的 Explore and Plan agents [skip CLAUDE.md 和 git status](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>) 可以保持上下文较小，因此使用 `agent: Explore` 的分叉技能只能看到 SKILL.md 内容和agent自带系统提示。相反，您定义使用技能作为参考材料的自定义子代理，请参阅[子代理](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>)。

#### 示例：使用 Explore agent 研究技能

该技能在分叉的 Explore agent 中运行研究。技能内容成为任务，agent提供了针对代码库探索优化的只读工具：

```yaml theme={null}
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
---

Research $ARGUMENTS thoroughly:

1. Find relevant files using Glob and Grep
2. Read and analyze the code
3. Summarize findings with specific file references
```

当该技能运行时：

1. 创建一个新的隔离上下文
2. 子代理收到技能内容作为提示（“彻底研究\$ARGUMENTS”指令）
3. `agent`字段决定执行环境（模型、工具、权限）
4. 子代理总结其结果并在完成后将其返回到您的主要对话

`agent` 字段指定要使用的子代理配置。选项包括内置 agents（`Explore`、`Plan`、`general-purpose`）或 `.claude/agents/` 中的任何自定义子代理。如果省略，则使用 `general-purpose`。

### 限制Claude的技能访问

默认情况下，Claude可以调用任何未设置`disable-model-invocation: true`的技能。定义 [`allowed-tools`](<#预先批准技能的工具>) 的技能授予 Claude 在调用该技能的回合期间无需每次使用批准即可访问这些工具；当您发送下一条消息时，赠款就会清除。您的 [ 权限设置 ](<https://code.claude.com/docs/en/permissions>) 仍控制所有其他工具的基准审批行为。还可以通过技能工具使用一些内置命令，包括 `/init` 和 `/security-review`。其他内置命令如 `/compact` 则不是。

控制Claude可以调用哪些技能的三种方法：

**通过拒绝`/permissions`中的技能工具来禁用所有技能**：

```text theme={null}
# Add to deny rules:
Skill
```

**允许或拒绝特定技能** 使用 [权限规则](<https://code.claude.com/docs/en/permissions>):

```text theme={null}
# Allow only specific skills
Skill(commit)
Skill(review-pr *)

# Deny specific skills
Skill(deploy *)
```

权限语法：`Skill(name)` 表示精确匹配，`Skill(name *)` 表示与任何参数的前缀匹配。

该表按规则中的名称类型显示了 `deny` 规则阻止您编写的名称之外的内容。

|您的 `deny` 规则名称|规则示例|Claude Code 也阻止|
| :- | :- | :- |
|别名| `Skill(review)` |捆绑的 `/code-review`，通过其 `/review` 别名|
|不合格的名字| `Skill(deploy)` |[嵌套技能](<#where-skills-live>)列为`apps/web:deploy`|
| A [从 claude.ai 同步的技能](<#how-synced-skills-behave>) | `Skill(anthropic-skills:deploy)` |当 Claude Desktop 将其作为插件传递到会话时该技能|
|同步技能的插件形式| `Skill(deploy:deploy)` |同步技能|
|[参数形式的技能](<https://code.claude.com/docs/en/permissions#match-by-input-parameter>)| `Skill(skill:deploy)` |技能以名称 Claude 为准，包括其别名和显示名称|

在 v2.1.260 之前，当拒绝规则仅指定非限定名称时，Claude Code 不会阻止在其限定名称下列出的嵌套技能。

Claude Code 仅与技能自己的名称和 Claude 调用中的名称匹配 `allow` 规则。要在没有提示的情况下批准 [ 同步技能 ](<#how-synced-skills-behave>)，请在其 [ 保留命名空间 ](<#names-reserved-for-synced-skills>) 中命名它：

* `Skill(anthropic-skills:pdf)` 批准同步的 `pdf` 技能
* `Skill(anthropic-skills *)` 批准每项同步技能
* `Skill(anthropic *)` 不覆盖 `anthropic-skills:pdf`，因为命名空间外部的前缀与其内部的名称不匹配

**通过将`disable-model-invocation: true`添加到他们的frontmatter中来隐藏个人技能**。这会从 Claude 的上下文中完全删除该技能。



**注释：**

使用`user-invocable: false`时，你无法调用该技能，但Claude仍然可以。为了防止Claude通过技能工具调用它，请设置`disable-model-invocation: true`。


### 从设置中覆盖技能可见性

`skillOverrides` 设置从您的 [ 设置 ](<https://code.claude.com/docs/en/settings>) 控制技能可见性，而不是技能本身的 frontmatter。将其用于您不想编辑 SKILL.md 的技能，例如签入共享项目存储库的技能。 `/skills`菜单为您编写：突出显示技能并按`Space`循环状态，然后按`Esc`保存到`.claude/settings.local.json`。

每个键都是一个技能名称，每个值是四种状态之一：

|价值|列出至 Claude|在`/`菜单中|
| :- | :- | :- |
| `"on"` |名称和描述|是的|
| `"name-only"` |仅姓名|是的|
| `"user-invocable-only"` |隐藏|是的|
| `"off"` |隐藏|隐藏|

`/skills`菜单将`"user-invocable-only"`状态标记为`user-only`。

除了终端 `/` 菜单之外，`"off"` 还隐藏向 [ 远程控制 ](<https://code.claude.com/docs/en/remote-control>) 客户端和 [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/skills#discover-available-commands>) 调用者通告的命令列表中的技能。通过全名调用隐藏技能会返回 `skillOverrides` 错误，而不是运行它。

`skillOverrides` 中不存在的技能将被视为 `"on"`。下面的示例将一项技能折叠为其名称，并完全关闭另一项技能：

```json theme={null}
{
  "skillOverrides": {
    "legacy-context": "name-only",
    "deploy": "off"
  }
}
```

某些捆绑技能具有别名，例如 `checkup` 代表 `/doctor`。如果您在 [ 托管设置 ](<https://code.claude.com/docs/en/managed-settings>) 中的别名下或在使用 `--settings` 标志传递的文件中设置 `skillOverrides` 条目，则 Claude Code 将其应用于别名背后的技能。您只能通过别名进一步限制技能，而不能使其更加可见，并且如果您还在托管设置中在技能自己的名称下设置了一个条目，则该条目优先。在v2.1.260之前，Claude Code没有将别名下的条目应用于任何设置源中的技能。

在用户、项目和本地设置中，Claude Code 仅与技能名称匹配条目。如果您在那里设置 `review` 条目，它将应用于名为 `review` 的技能，而不是通过其 `/review` 别名捆绑的 `/code-review`。

插件技能不受`skillOverrides`影响。通过 `/plugin` 来管理这些。

### 查找未使用的技能

[技能列表](<#技能描述被缩短>)中的每项技能都会在每个回合中添加到你的背景中，无论Claude是否使用过它。运行 `/skill-doctor` 查看您的每项技能的成本以及使用频率，以便您可以决定关闭哪些技能。在交互式会话中，报告将在 `/plugin` 管理器的 ** 统计信息 ** 选项卡中打开。在 [ 非交互模式 ](<https://code.claude.com/docs/en/headless>) 和 `-p` 下，Claude Code 将其打印为文本。

该报告涵盖了您会话中除捆绑技能和企业技能之外的技能。它会在列表中标记出从未调用过的技能，并说明在哪里关闭它们。它会告诉您在哪里关闭技能，从上下文成本最高的技能开始。该报告还列出了您最近未使用的插件。

`/skill-doctor` 需要 Claude Code v2.1.252 或更高版本，并且在跳过 [ 功能标志获取 ](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>) 的会话中不可用。如果您从手机或浏览器通过[远程控制](<https://code.claude.com/docs/en/remote-control>)运行`/skill-doctor`，Claude Code会回复[`Skill usage reports are not available on this connection.`](<https://code.claude.com/docs/en/errors#skill-usage-reports-are-not-available-on-this-connection>)。在运行会话的计算机上的终端中运行 `/skill-doctor`。

## 评估和迭代技能

看到技能触发器表明您Claude找到了它，而不是它做了您想要的事情。要了解一项技能是否有效，请单独测量 Claude 是否在应有的提示下调用它，以及输出是否符合您的预期。

对两者的检查是基线比较。收集一些真实的提示，在新的会话中使用可用的技能运行每个提示，然后在关闭技能的情况下再次运行，然后比较结果。新的会话很重要，因为编写技能时留下的上下文将掩盖书面说明中的空白。

如何在第二次运行时关闭该技能取决于它的来源：

* **个人或项目技能**：设置为[`skillOverrides`](<#从设置中覆盖技能可见性>)中的`"off"`。
* **插件提供的技能**：`skillOverrides`不适用于插件技能。请改用 [`claude plugin eval`](<https://code.claude.com/docs/en/plugin-evals#the-no-plugin-baseline>)，它会在不加载插件的情况下重复每次运行。

两个工具可以自动进行基线比较。对于 [ 插件 ](<https://code.claude.com/docs/en/plugins/overview>) 中附带的技能，[`claude plugin eval`](<https://code.claude.com/docs/en/plugin-evals>) 在使用或不使用插件的独立会话中运行每个提示，使用您定义的评分器或它为您编写的评分器进行评分，并以低于阈值的非零值退出，以便您可以对其进行门控 CI。为了迭代 Claude Code 对话中的单个技能，下面的技能创建器插件使用自己的 `evals/evals.json` 格式运行类似的循环。这两种格式不可互换。

### 使用技能创建器运行评估

[`skill-creator` 插件 ](<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator>) 自动执行 Claude Code 内的比较循环。在VS Code扩展或桌面应用程序中，按照[安装插件](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>)从官方市场安装它。在终端中，通过运行 `claude` 启动 Claude Code，然后在提示符下输入：

```text theme={null}
/plugin install skill-creator@claude-plugins-official
```

如果安装失败，请匹配消息 Claude Code 报告：

* `Marketplace "claude-plugins-official" not found`：使用 `/plugin marketplace add anthropics/claude-plugins-official` 添加市场，然后重试安装。
* 该插件[在市场中找不到](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>)：检查插件名称。

如果安装摘要报告 `Run /reload-plugins to apply.`，则 Claude Code 会为您运行重新加载。如果重新加载警告您的下一条消息将重新读取对话，请运行 `/reload-plugins --force` 以使插件的技能在当前会话中可用。然后要求 Claude 评估现有技能，例如 `evaluate my summarize-changes skill with skill-creator`。该插件将引导您编写测试用例并运行循环：

* **测试用例**：将提示、输入文件和预期行为存储在技能目录内的`evals/evals.json`中
* **隔离运行**：每个测试用例生成一个[子代理](<https://code.claude.com/docs/en/sub-agents>)，因此每次运行都以干净的上下文开始，并记录令牌计数和持续时间
* **分级**：根据输出检查每个断言，并将通过或失败的证据写入`grading.json`
* **基准**：将有技能与无技能的通过率、时间和令牌聚合到`benchmark.json`中，以便您可以将通过率改进与令牌和时间开销进行比较
* **版本比较**：在技能的两个版本之间运行盲注A/B，以便您可以在提交之前确认编辑是否是改进
* **描述调整**：生成应该触发和不应该触发的提示，测量命中率，并在技能根据错误请求激活时建议编辑描述
* **审阅查看器**：打开 HTML 报告，您可以在其中检查每个输出并记录下一次迭代读取的定性反馈

有关评估文件格式和完整迭代工作流程，请参阅agentskills.io 上的[评估技能输出质量](<https://agentskills.io/skill-creation/evaluating-skills>)。有关基准测试和比较模式的背景信息，请参阅 [ 技能创建者公告 ](<https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills>)。

## 分享技能

根据您的受众，技能可以分布在不同的范围：

* **项目技巧**：将`.claude/skills/`提交到版本控制
* **插件**：在[插件](<https://code.claude.com/docs/en/plugins/overview>)中创建一个`skills/`目录
* **托管**：通过[托管设置](<https://code.claude.com/docs/en/managed-settings>)在组织范围内部署

### 生成视觉输出

技能可以捆绑和运行任何语言的脚本，为 Claude 提供超出单个提示的功能。一种模式是生成可视化输出：在浏览器中打开的交互式 HTML 文件，用于探索数据、调试或创建报告。

此示例创建一个代码库资源管理器：一个交互式树视图，您可以在其中展开和折叠目录、一目了然地查看文件大小并通过颜色识别文件类型。

创建技能目录：

```bash theme={null}
mkdir -p ~/.claude/skills/codebase-visualizer/scripts
```

将其保存到`~/.claude/skills/codebase-visualizer/SKILL.md`。描述告诉Claude何时激活该技能，指令告诉Claude运行捆绑的脚本。脚本路径使用 [`${CLAUDE_SKILL_DIR}`](<#可用的字符串替换>)，因此它可以正确解析该技能是否安装在个人、项目或插件级别：

````yaml theme={null}
---
name: codebase-visualizer
description: Generate an interactive collapsible tree visualization of your codebase. Use when exploring a new repo, understanding project structure, or identifying large files.
allowed-tools: Bash(python3 *)
---

# Codebase Visualizer

Generate an interactive HTML tree view that shows your project's file structure with collapsible directories.

## Usage

Run the visualization script from your project root:

```bash
python3 ${CLAUDE_SKILL_DIR}/scripts/visualize.py .
```

This creates `codebase-map.html` in the current directory and opens it in your default browser.

## What the visualization shows

- **Collapsible directories**: Click folders to expand/collapse
- **File sizes**: Displayed next to each file
- **Colors**: Different colors for different file types
- **Directory totals**: Shows aggregate size of each folder
````

将其保存到`~/.claude/skills/codebase-visualizer/scripts/visualize.py`。该脚本扫描目录树并生成一个独立的 HTML 文件，其中包含：

* **摘要侧边栏**显示文件计数、目录计数、总大小和文件类型数量
* **条形图**按文件类型细分代码库（顶部8按大小）
* **可折叠树**，您可以在其中展开和折叠目录，并带有颜色编码的文件类型指示器

该脚本需要 Python 3 但仅使用内置库，因此无需安装任何软件包：

```python expandable theme={null}
#!/usr/bin/env python3
"""Generate an interactive collapsible tree visualization of a codebase."""

import json
import sys
import webbrowser
from html import escape
from pathlib import Path
from collections import Counter

IGNORE = {'.git', 'node_modules', '__pycache__', '.venv', 'venv', 'dist', 'build'}

def scan(path: Path, stats: dict) -> dict:
    result = {"name": path.name, "children": [], "size": 0}
    try:
        for item in sorted(path.iterdir()):
            if item.name in IGNORE or item.name.startswith('.'):
                continue
            if item.is_file():
                size = item.stat().st_size
                ext = item.suffix.lower() or '(no ext)'
                result["children"].append({"name": item.name, "size": size, "ext": ext})
                result["size"] += size
                stats["files"] += 1
                stats["extensions"][ext] += 1
                stats["ext_sizes"][ext] += size
            elif item.is_dir():
                stats["dirs"] += 1
                child = scan(item, stats)
                if child["children"]:
                    result["children"].append(child)
                    result["size"] += child["size"]
    except PermissionError:
        pass
    return result

def generate_html(data: dict, stats: dict, output: Path) -> None:
    ext_sizes = stats["ext_sizes"]
    total_size = sum(ext_sizes.values()) or 1
    sorted_exts = sorted(ext_sizes.items(), key=lambda x: -x[1])[:8]
    colors = {
        '.js': '#f7df1e', '.ts': '#3178c6', '.py': '#3776ab', '.go': '#00add8',
        '.rs': '#dea584', '.rb': '#cc342d', '.css': '#264de4', '.html': '#e34c26',
        '.json': '#6b7280', '.md': '#083fa1', '.yaml': '#cb171e', '.yml': '#cb171e',
        '.mdx': '#083fa1', '.tsx': '#3178c6', '.jsx': '#61dafb', '.sh': '#4eaa25',
    }
    lang_bars = "".join(
        f'<div class="bar-row"><span class="bar-label">{ext}</span>'
        f'<div class="bar" style="width:{(size/total_size)*100}%;background:{colors.get(ext,"#6b7280")}"></div>'
        f'<span class="bar-pct">{(size/total_size)*100:.1f}%</span></div>'
        for ext, size in sorted_exts
    )
    def fmt(b):
        if b < 1024: return f"{b} B"
        if b < 1048576: return f"{b/1024:.1f} KB"
        return f"{b/1048576:.1f} MB"

    html = f'''<!DOCTYPE html>
<html><head>
  <meta charset="utf-8"><title>Codebase Explorer</title>
  <style>
    body {{ font: 14px/1.5 system-ui, sans-serif; margin: 0; background: #1a1a2e; color: #eee; }}
    .container {{ display: flex; height: 100vh; }}
    .sidebar {{ width: 280px; background: #252542; padding: 20px; border-right: 1px solid #3d3d5c; overflow-y: auto; flex-shrink: 0; }}
    .main {{ flex: 1; padding: 20px; overflow-y: auto; }}
    h1 {{ margin: 0 0 10px 0; font-size: 18px; }}
    h2 {{ margin: 20px 0 10px 0; font-size: 14px; color: #888; text-transform: uppercase; }}
    .stat {{ display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid #3d3d5c; }}
    .stat-value {{ font-weight: bold; }}
    .bar-row {{ display: flex; align-items: center; margin: 6px 0; }}
    .bar-label {{ width: 55px; font-size: 12px; color: #aaa; }}
    .bar {{ height: 18px; border-radius: 3px; }}
    .bar-pct {{ margin-left: 8px; font-size: 12px; color: #666; }}
    .tree {{ list-style: none; padding-left: 20px; }}
    details {{ cursor: pointer; }}
    summary {{ padding: 4px 8px; border-radius: 4px; }}
    summary:hover {{ background: #2d2d44; }}
    .folder {{ color: #ffd700; }}
    .file {{ display: flex; align-items: center; padding: 4px 8px; border-radius: 4px; }}
    .file:hover {{ background: #2d2d44; }}
    .size {{ color: #888; margin-left: auto; font-size: 12px; }}
    .dot {{ width: 8px; height: 8px; border-radius: 50%; margin-right: 8px; }}
  </style>
</head><body>
  <div class="container">
    <div class="sidebar">
      <h1>📊 Summary</h1>
      <div class="stat"><span>Files</span><span class="stat-value">{stats["files"]:,}</span></div>
      <div class="stat"><span>Directories</span><span class="stat-value">{stats["dirs"]:,}</span></div>
      <div class="stat"><span>Total size</span><span class="stat-value">{fmt(data["size"])}</span></div>
      <div class="stat"><span>File types</span><span class="stat-value">{len(stats["extensions"])}</span></div>
      <h2>By file type</h2>
      {lang_bars}
    </div>
    <div class="main">
      <h1>📁 {escape(data["name"])}</h1>
      <ul class="tree" id="root"></ul>
    </div>
  </div>
  <script>
    const data = {json.dumps(data)};
    const colors = {json.dumps(colors)};
    function fmt(b) {{ if (b < 1024) return b + ' B'; if (b < 1048576) return (b/1024).toFixed(1) + ' KB'; return (b/1048576).toFixed(1) + ' MB'; }}
    function esc(s) {{ return s.replace(/[&<>"']/g, c => ({{"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}}[c])); }}
    function render(node, parent) {{
      if (node.children) {{
        const det = document.createElement('details');
        det.open = parent === document.getElementById('root');
        det.innerHTML = `<summary><span class="folder">📁 ${{esc(node.name)}}</span><span class="size">${{fmt(node.size)}}</span></summary>`;
        const ul = document.createElement('ul'); ul.className = 'tree';
        node.children.sort((a,b) => (b.children?1:0)-(a.children?1:0) || a.name.localeCompare(b.name));
        node.children.forEach(c => render(c, ul));
        det.appendChild(ul);
        const li = document.createElement('li'); li.appendChild(det); parent.appendChild(li);
      }} else {{
        const li = document.createElement('li'); li.className = 'file';
        li.innerHTML = `<span class="dot" style="background:${{colors[node.ext]||'#6b7280'}}"></span>${{esc(node.name)}}<span class="size">${{fmt(node.size)}}</span>`;
        parent.appendChild(li);
      }}
    }}
    data.children.forEach(c => render(c, document.getElementById('root')));
  </script>
</body></html>'''
    output.write_text(html)

if __name__ == '__main__':
    target = Path(sys.argv[1] if len(sys.argv) > 1 else '.').resolve()
    stats = {"files": 0, "dirs": 0, "extensions": Counter(), "ext_sizes": Counter()}
    data = scan(target, stats)
    out = Path('codebase-map.html')
    generate_html(data, stats, out)
    print(f'Generated {out.absolute()}')
    webbrowser.open(f'file://{out.absolute()}')
```

要进行测试，请在任何项目中打开 Claude Code 并询问“可视化此代码库”。 Claude 运行脚本，该脚本打印生成的文件的路径，例如 `Generated /path/to/codebase-map.html`，并在浏览器中打开它。如果您在没有打开浏览器的无头环境中工作，则打印的路径会确认脚本已成功。

此模式适用于任何可视化输出：依赖关系图、测试覆盖率报告、API 文档或数据库模式可视化。捆绑的脚本完成工作，而 Claude 处理编排。

## 故障排除

### 技能不触发

如果Claude没有按预期使用你的技能：

1. 检查描述是否包含用户自然会说的关键词
2. 验证技能出现在`What skills are available?`中
3. 尝试重新表述您的请求，使其更符合描述
4. 如果该技能是用户可调用的，则直接使用 `/skill-name` 调用它

如果 frontmatter YAML 格式错误，Claude Code 将使用空元数据加载技能正文，因此 `/skill-name` 仍然有效，但 Claude 无法与您的 `description` 匹配。使用 `--debug` 运行以查看解析错误。

如果该技能在插件中提供，您可以测量它在实际提示中触发的频率，而不是一次检查一个：使用 [`tool_used: Skill` 评分器 ](<https://code.claude.com/docs/en/plugin-evals#create-your-first-eval-suite>) 编写一个评估案例，并在每次描述更改后使用 `claude plugin eval` 运行它。

要查找 frontmatter 无法解析的 `SKILL.md` 文件，请在技能目录上运行 [`claude plugin validate`](<https://code.claude.com/docs/en/plugins/cli-reference#validate-a-directory>)，例如针对项目技能的 `claude plugin validate .claude/skills` 或针对个人技能的 `claude plugin validate ~/.claude/skills`。需要 Claude Code v2.1.233 或更高版本。

### 技能触发过于频繁

如果Claude在你不想要的时候使用了你的技能：

1. 让描述更具体
2. 如果您只想手动调用，请添加`disable-model-invocation: true`

### Claude 停止跟随技能

如果 Claude 在其第一个响应中遵循某项技能并随后停止遵循它，则从以下任一情况匹配开始：

* **Claude跳过了每次都必须保持的规则**：将规则移动到[钩子](<https://code.claude.com/docs/en/hooks-guide>)中。 Claude Code 每次事件发生时都会运行一个钩子，例如在每次文件编辑之前，无论 Claude 是否遵循该技能。要与技能保持规则，请在技能的 [`hooks` frontmatter](<https://code.claude.com/docs/en/hooks#hooks-in-skills-and-agents>) 中定义挂钩。该挂钩从调用技能时开始应用，直到会话结束。
* **Claude 跳过了应根据判断应用的指导**：说明指导，使其适用于整个任务，例如“每次编辑后运行测试”而不是“运行测试”。 Claude Code 在调用技能时将技能的内容添加到对话中，并且 [ 在以后的回合中不会重新读取文件 ](<#技能内容生命周期>)。
* **对话被压缩**：再次调用技能，恢复完整内容。 [压缩](<https://code.claude.com/docs/en/how-claude-code-works#when-context-fills-up>)后，Claude Code[只能保留调用技能的开头](<#技能内容生命周期>)，因此将最重要的指令放在`SKILL.md`顶部附近。

### 技能描述被缩短

Claude Code 将技能名称和描述列表加载到上下文中，以便 Claude 知道可用的内容。列表始终包含每个技能名称，但如果您有很多技能，Claude Code 会删除一些描述以适应列表的字符预算，从而删除需要匹配您的请求的关键字 Claude。预算按模型上下文窗口的 1% 缩放。当列表溢出时，Claude Code 会从您最少调用的技能开始删除描述，因此您最常使用的技能会保留其全文。

运行 `/doctor` 来估计列表的上下文成本及其最大的贡献者。要查找值得关闭的技能，请运行 [`/skill-doctor`](<#查找未使用的技能>)。当列表超出预算时，Claude Code 还会向调试日志写入警告，通过 [`--debug`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) 可见。

`/context` 中的“技能”行报告应用预算后列表的大小，因此它与模型收到的内容相匹配。在 v2.1.196 之前，该行对每个描述的全文进行计数，并且可能显示比配置的预算大几倍的值。

要提高预算，请将 [`skillListingBudgetFraction`](<https://code.claude.com/docs/en/settings-reference#skilllistingbudgetfraction>) 设置（例如，`0.02` = 2%）或 `SLASH_COMMAND_TOOL_CHAR_BUDGET` 环境变量设置为固定字符数。要释放其他技能的预算，请将低优先级条目设置为 [`skillOverrides`](<#从设置中覆盖技能可见性>) 中的 `"name-only"`，以便它们在列出时不带说明。您还可以在源头修剪 `description` 和 `when_to_use` 文本：将关键用例放在第一位，因为无论预算如何，每个条目的组合文本都以 1,536 字符为上限。该上限可通过 [`skillListingMaxDescChars`](<https://code.claude.com/docs/en/settings-reference#skilllistingmaxdescchars>) 进行配置。

### 个人技能消失

如果您在`~/.claude/skills/`中创建的技能文件夹消失了，请查看`~/.claude/skills/.trash/`。当Claude Code [从claude.ai](<#how-synced-skills-behave>)同步技能时，它会将它们下载到单独的`synced`子文件夹中，并且不会移动或删除您创建的文件夹。

在v2.1.280之前，`~/.claude/skills/`中名为`manifest.json`的文件导致Claude Code将该文件列出的技能文件夹移动到`~/.claude/skills/.trash/`下的带时间戳的文件夹中，并且这些技能停止加载。

要恢复技能，请将其文件夹从带时间戳的文件夹移回 `~/.claude/skills/`。在 [ 保留清除 ](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) 删除垃圾条目之前执行此操作，默认情况下，在垃圾条目移至垃圾箱后 30 天。

## 相关资源

* **[调试您的配置](<https://code.claude.com/docs/en/debug-your-config>)**：诊断技能未出现或触发的原因
* **[评估技能输出质量](<https://agentskills.io/skill-creation/evaluating-skills>)**：agentskills.io 上的评估文件格式和迭代工作流程
* **[技能创作最佳实践](<https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices>)**：适用于Claude产品的写作指南
* **[子代理](<https://code.claude.com/docs/en/sub-agents>)**：将任务委托给专门的agents
* **[插件](<https://code.claude.com/docs/en/plugins/overview>)**：与其他扩展一起打包和分发技能
* **[Hooks](<https://code.claude.com/docs/en/hooks>)**：围绕工具事件自动化工作流程
* **[内存](<https://code.claude.com/docs/en/memory>)**：管理持久上下文的CLAUDE.md文件
* **[命令](<https://code.claude.com/docs/en/commands>)**：内置命令和捆绑技能的参考
* **[权限](<https://code.claude.com/docs/en/permissions>)**：控制工具和技能访问
* **[Claude 标签技能](<https://claude.com/docs/claude-tag/admins/skills-repo>)**：当在 Claude 标签通道中使用该存储库时，也会加载提交到存储库的项目技能


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Extend Claude with skills

> Create, manage, and share skills to extend Claude's capabilities in Claude Code. Includes custom commands and bundled skills.

Skills extend what Claude can do. Create a `SKILL.md` file with instructions, and Claude adds it to its toolkit. Claude uses skills when relevant, or you can invoke one directly with `/skill-name`.

Create a skill when you keep pasting the same instructions, checklist, or multi-step procedure into chat, or when a section of CLAUDE.md has grown into a procedure rather than a fact. Unlike CLAUDE.md content, a skill's body loads only when it's used, so long reference material costs almost nothing until you need it.



**注释：**

  For built-in commands like `/help` and `/compact`, and bundled skills like `/debug` and `/code-review`, see the [commands reference](<https://code.claude.com/docs/en/commands>).

  **Custom commands have been merged into skills.** A file at `.claude/commands/deploy.md` and a skill at `.claude/skills/deploy/SKILL.md` both create `/deploy` and work the same way. Your existing `.claude/commands/` files keep working. Skills add optional features: a directory for supporting files, frontmatter to [control whether you or Claude invokes them](<#control-who-invokes-a-skill>), and the ability for Claude to load them automatically when relevant.


Claude Code skills follow the [Agent Skills](<https://agentskills.io>) open standard, which works across multiple AI tools. Claude Code extends the standard with additional features like [invocation control](<#control-who-invokes-a-skill>), [subagent execution](<#run-skills-in-a-subagent>), and [dynamic context injection](<#inject-dynamic-context>). See [Using skill frontmatter outside Claude Code](<#using-skill-frontmatter-outside-claude-code>) for which frontmatter fields are part of the standard and which are Claude Code extensions.

## Bundled skills

Claude Code includes a set of bundled skills, such as `/doctor`, `/code-review`, `/batch`, `/debug`, `/loop`, and `/claude-api`. Bundled skills are prompt-based: they give Claude detailed instructions and let it orchestrate the work using its tools. Most built-in commands instead execute fixed logic directly.

You invoke a bundled skill the same way as any other skill, by typing `/` followed by the skill name. Claude invokes some bundled skills automatically when relevant; others, including `/verify`, run only when you invoke them, which keeps you in control of when these longer-running checks spend time and tokens.

Most bundled skills are available in every session. A few depend on a specific feature: `/workflow-authoring`, for example, is available only when [dynamic workflows](<https://code.claude.com/docs/en/workflows>) are enabled.

To turn bundled skills off, use the [`disableBundledSkills`](<https://code.claude.com/docs/en/settings-reference#disablebundledskills>) setting.



**注释：**

  The [`/doctor`](<https://code.claude.com/docs/en/commands#all-commands>) setup checkup stays typable when `disableBundledSkills` is on, in Claude Code v2.1.205 and later. To hide it, set the `DISABLE_DOCTOR_COMMAND` environment variable or a [`skillOverrides`](<#override-skill-visibility-from-settings>) entry of `"doctor": "off"`. Before v2.1.205, `/doctor` was a built-in command rather than a bundled skill.


Bundled skills are listed alongside built-in commands in the [commands reference](<https://code.claude.com/docs/en/commands>), marked **Skill** in the Purpose column.

### Check your setup with `/doctor`

Run `/doctor` at the Claude Code prompt for a setup checkup that diagnoses issues and can fix them. Claude reports its findings first and asks for confirmation before changing anything. The checkup covers these areas:

* **Installation health**: duplicate or leftover installs, `PATH` problems, unparseable settings files, and whether a newer version is available on your [release channel](<https://code.claude.com/docs/en/setup#configure-release-channel>)
* **Extensions**: unused skills, MCP servers, and plugins compared with their context cost, and slow [hooks](<https://code.claude.com/docs/en/hooks>)
* **`CLAUDE.md` files**: local `CLAUDE.md` files that duplicate checked-in ones, checked-in [`CLAUDE.md` content Claude could derive from the codebase](<https://code.claude.com/docs/en/memory#my-claude-md-is-too-large>), and the always-loaded guidance that remains, which Claude offers to migrate into skills and nested `CLAUDE.md` files that load on demand
* **Permissions**: an offer to make [auto mode](<https://code.claude.com/docs/en/permissions#permission-modes>) your default permission mode and to [pre-approve](<https://code.claude.com/docs/en/permissions>) read-only commands that you frequently deny

For read-only installation diagnostics without starting a session, run `claude doctor` in your terminal instead.

To audit your instructions rather than your setup, run `/doctor prompt-audit` at the Claude Code prompt. Claude [checks your `CLAUDE.md` files, skills, and other configuration](<https://code.claude.com/docs/en/memory#audit-your-instruction-files>) for outdated or conflicting instructions instead of running the checkup. The `prompt-audit` subcommand requires Claude Code v2.1.283 or later.

### Run and verify your app

Three bundled skills work together to launch your app and confirm changes against the running app instead of tests alone:

| Skill | Purpose |
| :- | :- |
| `/run` | Launch and drive your app to see a change working |
| `/verify` | Build and run your app to confirm a code change does what it should, without falling back to tests or type checks |
| `/run-skill-generator` | Teach `/run` and `/verify` how to build and launch your project |

`/run` and `/verify` work without setup. They infer the launch from your project type (CLI, server, TUI, browser-driven) and from what's in your README, `package.json`, or `Makefile`. That inference gets unreliable for projects that need anything beyond a standard launch: a database, an env file, a graphical session, a multi-step build.

`/run-skill-generator` records the recipe instead. It gets your app running from a clean environment, captures what worked (the install commands, the env vars, the launch script), and commits it as a per-project skill at `.claude/skills/run-<name>/`. After that, `/run`, `/verify`, and any other agent in the repo follow the recorded recipe instead of rediscovering it. Run `/run-skill-generator` once per project, and again if the build or launch process changes.

`/verify` can also record its own recipe. When it has to build and drive your app without a recorded recipe, it writes what worked to `.claude/skills/verify/SKILL.md` at the repo root, or in the touched package directory in a monorepo, so later runs and other agents follow the same steps. At the repo root, the recorded skill replaces the bundled `/verify`.

Claude edits the recorded file only when it steered a run wrong, such as a command that failed or a missing step, so you can commit the file without per-session diffs. Before v2.1.205, the bundled skill told Claude to fold in anything a run learned, which caused frequent merge conflicts.

### Run your checks before each commit

When a session starts with a skill named `verify` or `simplify` in place, Claude Code's commit instructions tell Claude to run it right before each commit, except for changes to docs or tests. This requires Claude Code v2.1.286 or later. Claude gets that instruction when these conditions hold at the start of the session:

* **Location**: the skill loads from the enterprise, personal, project, or additional-directory [location](<#where-skills-live>), or from a `.claude/commands/` file with that name. The recipe that `/verify` records at your repo root is a project skill, so it counts. The bundled `/verify` and `/simplify`, plugin skills, and skills from your claude.ai account don't count.
* **Invocation**: Claude can invoke the skill. If you've [stopped Claude from invoking it](<#control-who-invokes-a-skill>), for example with `disable-model-invocation: true`, Claude doesn't get the instruction.
* **Git instructions**: you haven't turned off [`includeGitInstructions`](<https://code.claude.com/docs/en/settings-reference#includegitinstructions>). Turning it off removes this instruction together with the rest of the built-in commit and PR instructions.

### Work on Claude API projects

The bundled `/claude-api` skill loads [Claude API](<https://platform.claude.com/docs/en/api/overview>) and [Managed Agents](<https://platform.claude.com/docs/en/managed-agents/overview>) reference material for your project's language. Claude also activates it automatically when your code imports `anthropic` or `@anthropic-ai/sdk`.

To start one of the skill's workflows, type a subcommand after the skill name at the Claude Code prompt, for example `/claude-api migrate`. The table lists what each subcommand does and the earliest Claude Code version that includes it. `migrate` and `managed-agents-onboard` predate v2.1.221, the oldest version the table tracks.

| Subcommand | What it does | Minimum version |
| :- | :- | :- |
| `migrate` | Update your existing Claude API code to a newer model | Earlier than v2.1.221 |
| `upgrade` | Move your project's Anthropic SDK dependency across a major version, currently the Python `anthropic` package from 0.x to 1.x | v2.1.236 or later |
| `managed-agents-onboard` | Walk through creating a new Managed Agent | Earlier than v2.1.221 |
| `managed-agents-onboard <url>` | Build the Managed Agent that the page at the URL describes, such as a page in the [Managed Agents docs](<https://platform.claude.com/docs/en/managed-agents/overview>) | v2.1.290 or later |
| `managed-agents-onboard <quickstart-name>` | Build one of the Console's quickstart templates, such as `deep-researcher`. If you give one word that isn't a template name, Claude lists the valid names | v2.1.290 or later |
| `prompt-audit` | Flag instructions written for older models in your prompts, skills, and tool descriptions and propose fixes as a diff | v2.1.221 or later |
| `cost-optimize` | Profile where your project's Claude API spend goes and propose savings from options such as prompt caching, trimming unneeded input and output tokens, batch processing, effort, and model choice, one change at a time | v2.1.247 or later |
| `build-eval` | Build an eval set for your Claude-powered app | v2.1.259 or later |
| `hillclimb` | Iteratively improve your app against an existing eval | v2.1.259 or later |
| `preserved-thinking-migration` | Find the edits your integration makes to earlier turns, its system prompt, or its tool list that invalidate [preserved thinking](<https://platform.claude.com/docs/en/build-with-claude/preserved-thinking>) blocks, measure how much reasoning each one drops, and propose fixes one at a time, re-measuring after each change | v2.1.282 or later |

## Getting started

### Create your first skill

This example creates a skill that summarizes the uncommitted changes in your git repository and flags anything risky. It pulls the live diff into the prompt before Claude reads it, so the response is grounded in your actual working tree rather than what Claude can guess from open files. Claude loads the skill automatically when you ask about your changes, or you can invoke it directly with `/summarize-changes`.



  

#### Create the skill directory

    Create a directory for the skill in your personal skills folder. Personal skills are available across all your projects.

    ```bash theme={null}
    mkdir -p ~/.claude/skills/summarize-changes
    ```
  


  

#### Write SKILL.md

    Every skill needs a `SKILL.md` file with two parts: YAML frontmatter between `---` markers that tells Claude when to use the skill, and markdown content with the instructions Claude follows when the skill runs. The directory name, or the frontmatter `name` when you set one, becomes the command you type, and the `description` helps Claude decide when to load the skill automatically.

    Save this to `~/.claude/skills/summarize-changes/SKILL.md`:

    ```yaml theme={null}
    ---
    description: Summarizes uncommitted changes and flags anything risky. Use when the user asks what changed, wants a commit message, or asks to review their diff.
    ---

    ## Current changes

    !`git diff HEAD`

    ## Instructions

    Summarize the changes above in two or three bullet points, then list any risks you notice such as missing error handling, hardcoded values, or tests that need updating. If the diff is empty, say there are no uncommitted changes.
    ```

    The `` !`git diff HEAD` `` line uses [dynamic context injection](<#inject-dynamic-context>): Claude Code runs the command and replaces the line with its output before Claude sees the skill content, so the instructions arrive with the current diff already inlined.
  


  

#### Test the skill

    Open a git project, make a small edit to any file, and start Claude Code by running `claude`. You can test the skill two ways.

    **Let Claude invoke it automatically** by asking something that matches the description:

    ```text theme={null}
    What did I change?
    ```

    **Or invoke it directly** with the skill name:

    ```text theme={null}
    /summarize-changes
    ```

    Either way, Claude should respond with a short summary of your edit and a list of risks.
  


<h2 id="where-skills-live">
  Choose where skills load
</h2>

Where you save a skill decides which sessions load it. Save it under your home directory to get it in every project, commit it to a repository to share it with everyone who works there, or distribute it through a plugin or managed settings to reach a whole team.

| Location | Path | Loads in |
| :- | :- | :- |
| Enterprise | `.claude/skills/<skill-name>/SKILL.md` in the [managed settings directory](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>) | All users on machines where your organization deploys it |
| Personal | `~/.claude/skills/<skill-name>/SKILL.md` | All your projects on this machine, but not [Cowork or cloud sessions](<#skills-in-cowork-and-cloud-sessions>) |
| Project | `.claude/skills/<skill-name>/SKILL.md` | Sessions in this repository. Commit it so your team gets it too |
| Nested | `<subdir>/.claude/skills/<skill-name>/SKILL.md` | Sessions started in or below `<subdir>`. A session started above it loads the skill once Claude works on files there. See [monorepos and subdirectories](<#discovery-from-parent-and-nested-directories>) |
| Additional directory | `.claude/skills/<skill-name>/SKILL.md` in a directory you pass with `--add-dir` | That session. See [directories outside the project](<#skills-from-additional-directories>) |
| Plugin | `<plugin>/skills/<skill-name>/SKILL.md` | Wherever the [plugin](<https://code.claude.com/docs/en/plugins/overview>) is enabled, as `/plugin-name:skill-name` |
| claude.ai account | Skills enabled for your claude.ai account | Cowork sessions, cloud sessions, and terminal sessions where you sign in with that account. See [Skills synced from claude.ai](<#how-synced-skills-behave>) |

Skill folders also follow these rules:

* **Symlinked folders**: a `<skill-name>` entry in the enterprise, personal, or project location can be a symlink to a directory elsewhere on disk. Claude Code reads `SKILL.md` from the target and loads the skill once even if several locations point at the same target. Plugin skills [handle symlinks differently](<https://code.claude.com/docs/en/plugins/host-marketplace#share-files-within-a-marketplace-with-symlinks>).
* **Reserved name `synced`**: don't name a skill folder `synced`, in any capitalization. Claude Code uses `~/.claude/skills/synced/` for [skills downloaded from claude.ai](<#where-synced-skills-load>) and skips a skill you author at that name in the enterprise, personal, and project locations.
* **Reserved name `anthropic-skills`**: outside a plugin, a skill folder or command file whose name is `anthropic-skills` or starts with `anthropic-skills:` doesn't load. See [Names reserved for synced skills](<#names-reserved-for-synced-skills>).
* **Command files**: a Markdown file in `.claude/commands/` is the older format and still works. It supports the same [frontmatter](<#frontmatter-reference>) except `name` and `paths`. To find the name you type to invoke it, see [How a skill gets its command name](<#how-a-skill-gets-its-command-name>). Prefer a skill for new work, since skills also support [supporting files](<#add-supporting-files>).
* **Skill folder as a plugin**: add a `.claude-plugin/plugin.json` to a skill folder and it loads as a [plugin](<https://code.claude.com/docs/en/plugins/loading#plugins-shared-through-a-repository>) named `<name>@skills-dir`, so it can bundle agents, hooks, and MCP servers. In a project's `.claude/skills/`, this requires accepting the workspace trust dialog first.

<h3 id="discovery-from-parent-and-nested-directories">
  Load skills in monorepos and subdirectories
</h3>

Claude Code loads project skills from `.claude/skills/` in the directory where you start it and in every parent directory up to the repository root, so starting in `packages/frontend/` still picks up skills defined at the root. When you [move the session with `/cd`](<https://code.claude.com/docs/en/permissions#move-the-session-to-another-directory>) on v2.1.246 or later, Claude Code adds the new directory's project skills.

In a session running in a linked [git worktree](<https://code.claude.com/docs/en/worktrees>), Claude Code searches parent directories only up to the worktree root. On Claude Code v2.1.277 or later, when the worktree checkout has no `.claude/skills` directory at its root, Claude Code loads the main checkout's project skills instead. See [What worktrees share with the main checkout](<https://code.claude.com/docs/en/worktrees#what-worktrees-share-with-the-main-checkout>).

Skills in a `.claude/skills/` directory below where you started don't load at startup. They load the first time Claude reads or edits a file in that subdirectory and stay available for the rest of the session. Until then they don't appear in the `/` menu and you can't invoke them by name. To load them sooner, run `/add-dir` with the subdirectory's path, which requires Claude Code v2.1.257 or later.

When a nested skill's directory name matches another skill's name, both stay available. With a `deploy` skill at the repository root and another in `apps/web/.claude/skills/`:

* `/deploy` runs the root skill. Claude Code also lists the directory-qualified variants for Claude, with an instruction to invoke the one whose directory holds the files it's working on, so the nested skill still applies to work in `apps/web/`.
* `/apps/web:deploy` runs the nested skill on its own. Its description names the directory it applies to.

<h3 id="skills-from-additional-directories">
  Load skills from a directory outside the project
</h3>

When you add a directory with `--add-dir` or `/add-dir`, Claude Code loads the skills in that directory's `.claude/skills/`, along with its `.claude/commands/` and `.claude/agents/`. Directories the Agent SDK adds through [`additionalDirectories`](<https://code.claude.com/docs/en/agent-sdk/typescript#options>) in TypeScript or [`add_dirs`](<https://code.claude.com/docs/en/agent-sdk/python#claudeagentoptions>) in Python load the same way, because the SDK passes them as `--add-dir`. The `permissions.additionalDirectories` setting in `settings.json` grants file access only and loads none of these.

Claude Code watches `.claude/skills/` in a directory you pass with `--add-dir` at launch, as [Edit a skill during a session](<#live-change-detection>) describes. It doesn't watch the added directory's `.claude/commands/` or `.claude/agents/`, so restart the session after changing a file there.

These loads depend on the `project` [setting source](<https://code.claude.com/docs/en/agent-sdk/claude-code-features#control-filesystem-settings-with-settingsources>), which is on by default. A [`strictPluginOnlyCustomization`](<https://code.claude.com/docs/en/settings-reference#strictpluginonlycustomization>) policy, [bare mode](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>), and [`--safe-mode`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) each restrict them further, as those pages describe. See [Additional directories grant file access, not configuration](<https://code.claude.com/docs/en/permissions#additional-directories-grant-file-access-not-configuration>) for the full table of what an added directory loads, including `CLAUDE.md` and plugin settings.

### Resolve skills that share a name

When two skills share a directory or file name, where each one came from decides which one `/name` runs. For a name set by the frontmatter `name` field, see [How a skill gets its command name](<#how-a-skill-gets-its-command-name>). The table covers the enterprise, personal, project, nested, plugin, and claude.ai locations, bundled skills, built-in commands, and command files:

| Same name in | Which one runs |
| :- | :- |
| Two of enterprise, personal, and project | Enterprise over personal, and personal over project. With `deploy` in both `~/.claude/skills/` and the project's `.claude/skills/`, `/deploy` runs the personal one |
| Any of those locations and a [bundled skill](<#bundled-skills>) | Your skill replaces the bundled command, but not its aliases. A project `code-review` skill replaces `/code-review`, and the bundled alias `/review` never runs your skill |
| Any of those locations and a [built-in command](<https://code.claude.com/docs/en/commands>) | In a local terminal session, your skill replaces the built-in command, but not its aliases. A project `usage` skill replaces `/usage`, and the built-in alias `/cost` still runs the built-in command |
| A skill and a file in `.claude/commands/` | The skill |
| A project-root skill and a nested skill | Both load. See [monorepos and subdirectories](<#discovery-from-parent-and-nested-directories>) |
| A plugin skill and a skill at any of the locations above | Both load, because plugin skills are namespaced as `/plugin-name:skill-name` |
| Any of the above and the short name of a skill [synced from your claude.ai account](<#how-synced-skills-behave>) | The other skill or command. The synced skill is then listed and runs only under its full name. See [When a synced skill name matches another command](<#when-a-synced-skill-name-matches-another-command>) |

<h3 id="skills-in-cowork-and-cloud-sessions">
  Use skills in Cowork and cloud sessions
</h3>

[Cowork](<https://claude.com/product/cowork>) sessions and [cloud sessions](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>), including [routines](<https://code.claude.com/docs/en/routines>), don't read `~/.claude/skills/` on your machine. Both interactive and scheduled Cowork sessions load the skills enabled for your claude.ai account, synced at session start; manage them from **Customize** in the Desktop app sidebar or from the skills settings on claude.ai. Cloud sessions additionally load project skills committed to the cloned repository's `.claude/skills/`.

If a skill exists only in `~/.claude/skills/` on your machine, Claude Code reports that the skill was not found when a [routine](<https://code.claude.com/docs/en/routines>) invokes it, because each routine run starts as a fresh cloud session. To make a personal skill available in these sessions:

* For Cowork and cloud sessions, enable the skill for your claude.ai account. [Some sessions in a self-hosted environment](<https://code.claude.com/docs/en/self-hosted-environments-configuration#how-each-session’s-config-is-assembled>) don't load your account's skills.
* For cloud sessions, you can instead commit the skill to the repository's `.claude/skills/`. Plugins declared in the repository's `.claude/settings.json` and plugins enabled only in your user settings [don't load in cloud sessions](<https://code.claude.com/docs/en/cloud-environments#what-carries-over-from-your-setup>).

[Desktop scheduled tasks](<https://code.claude.com/docs/en/desktop-scheduled-tasks>) run locally on your machine, so they do load `~/.claude/skills/`.

<h3 id="how-synced-skills-behave">
  Skills synced from claude.ai
</h3>

This section applies to you if you use Cowork or cloud sessions, or sign in to Claude Code in your terminal with a claude.ai account. In those sessions, Claude Code loads the skills enabled for your claude.ai account, with no setup on your part, as [Where synced skills load](<#where-synced-skills-load>) describes. Those skills include the ones you create or turn on in your claude.ai settings, skills your organization provides there, and Anthropic's built-in skills such as `pdf` and `xlsx`.

Claude Code downloads a synced skill from your account rather than reading a file you wrote on the machine where the session runs, so it applies rules to synced skills that don't apply to the skills you store in the [skills locations](<#where-skills-live>).

#### Where synced skills load

In a Cowork or cloud session, Claude Code loads the skills enabled for your claude.ai account, and [Skills in Cowork and cloud sessions](<#skills-in-cowork-and-cloud-sessions>) says how to choose which skills those sessions get.

In your terminal, Claude Code syncs those skills in sessions where you sign in with your claude.ai account. When the session starts, Claude Code downloads your account's skills into `~/.claude/skills/synced/` in the background, then checks claude.ai for changes while the session runs. When a check finds that a skill was added, edited, or turned off on claude.ai, Claude Code adds, updates, or removes it in the running session without a restart. Syncing in terminal sessions requires Claude Code v2.1.273 or later.

The checks run less often while the session is idle:

* **While you or Claude work in the session**: a check runs about every 10 minutes.
* **While the session is idle**: a check runs about every 40 minutes. When you type in the session again, Claude Code checks within a few minutes if the last check was more than 10 minutes ago.

The sync never delays startup, because Claude waits for a skill's download only when it invokes that skill. A short [non-interactive](<https://code.claude.com/docs/en/headless>) run can therefore finish before a newly added skill downloads, in which case a later session downloads it. To make a non-interactive run download your skills and wait for the list before it answers the prompt, set [`CLAUDE_CODE_SYNC_SKILLS`](<https://code.claude.com/docs/en/env-vars#variables>) to `1`. Before v2.1.273, terminal sessions downloaded them only in a `-p` run with this variable set.

Claude Code syncs only in a session that signs in with your claude.ai account and [fetches feature flags from Anthropic](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>). It doesn't sync in these sessions:

* A session that doesn't use a sign-in stored by `/login`, such as one that authenticates with an API key, or one where `ANTHROPIC_AUTH_TOKEN`, `CLAUDE_CODE_OAUTH_TOKEN`, or an `apiKeyHelper` script supplies the credential
* A session that doesn't fetch feature flags, such as one on Amazon Bedrock or one where you set `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`
* A session in [bare mode](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>) or one you start with `--safe-mode`
* A session where your organization's managed settings [lock skills to plugin sources](<https://code.claude.com/docs/en/settings-reference#strictpluginonlycustomization-skills>), or one you start with a [`--setting-sources`](<https://code.claude.com/docs/en/cli-reference#cli-flags>) list that leaves out `user`

If you sign in with `/login` during a session, restart Claude Code to start syncing.

Skills that an earlier session synced stay on disk. Claude Code loads them in later sessions signed in to the same account, even when it can't reach claude.ai.

Claude Code downloads synced skills and never uploads them. If you or Claude edit a file under `~/.claude/skills/synced/`, the change isn't saved to your claude.ai account, and a later sync can overwrite or remove it. To change a synced skill, update it on claude.ai; the next sync downloads the new version.

To see which skills synced, run `/skills`. The menu lists them under `claude.ai sync`.

Some of Anthropic's skills, such as `pdf` and `xlsx`, always sync. For the rest, turn a skill on or off in your skills settings on claude.ai to change whether it syncs.

To stop syncing on a machine, set [`syncClaudeAiSkills`](<https://code.claude.com/docs/en/settings-reference#syncclaudeaiskills>) to `false` in your user settings. Claude Code stops downloading, and the next time it starts it moves the skills it already synced to `~/.claude/skills/.trash/` and no longer loads them. Your organization can turn syncing off for everyone by turning off Skills on claude.ai. To stop syncing while leaving Skills on, it can set the same key in [managed settings](<https://code.claude.com/docs/en/managed-settings>).

If your organization turns Skills off on claude.ai, Claude Code removes the downloaded skills and they stop loading. The removed skills move to `~/.claude/skills/.trash/`, where you can recover the files until the [retention sweep](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) deletes them. Once your organization turns Skills back on, Claude Code downloads the skills you enabled at the next sync.

#### When a synced skill name matches another command

You can invoke a synced skill by its short name, `/<name>`, or by its full name, `/anthropic-skills:<name>`. When another command uses the short name, `/<name>` runs the other command, and the synced skill runs only as `/anthropic-skills:<name>`. With a local `deploy` skill and a synced `deploy`, `/deploy` runs the local skill and `/anthropic-skills:deploy` runs the synced one. Before v2.1.269, a synced skill had only its short name.

In the `/` menu, `/skills`, and `/context`, a synced skill appears under its short name, or under its full name while another command uses the short name. Run `/skills` in your session. A note under the list explains each synced skill that lost its short name. If one of your personal skills or command files in `~/.claude/` uses the name, the note also says what to rename or delete to free it.

From v2.1.269 through v2.1.280, these lists showed every synced skill under its full name, and `/skills` had no such note; both changed in v2.1.281.

The command that uses the short name can be any of these:

* A built-in command or a [bundled skill](<#bundled-skills>), including one that's unavailable in your session, for example after you turn bundled skills off
* A skill at any [local level](<#where-skills-live>) or a file in `.claude/commands/`
* A plugin skill
* An [MCP prompt](<https://code.claude.com/docs/en/mcp#use-mcp-prompts-as-commands>)

Claude Code labels synced skills so you can tell where they came from. The `/skills` menu and `/context` group synced skills under `claude.ai sync`, and the `/` command menu marks them as coming from claude.ai.

When it compares names, Claude Code ignores case, spacing, and invisible characters, and treats compatibility forms such as fullwidth letters and dash variants as their plain equivalents. For example, a synced skill named `Commit` and a local skill named `commit` count as the same name, so `/commit` keeps running your local skill.

A name that differs only by a look-alike letter from another alphabet counts as a different name, and the `claude.ai sync` label is how you tell the two apart. These checks and labels require Claude Code v2.1.228 or later.

<h4 id="names-reserved-for-synced-skills">
  Names reserved for synced skills
</h4>

Claude Code reserves the name `anthropic-skills`, and every name inside that namespace such as `anthropic-skills:pdf`, for skills synced from claude.ai, so a synced skill's full name never runs anything else. The name is reserved in every session, whether or not you sign in with a claude.ai account.

* **A skill folder, a frontmatter `name`, a file or subfolder in `.claude/commands/`, or a [saved workflow](<https://code.claude.com/docs/en/workflows#save-the-workflow-for-reuse>)**: it doesn't load. A [startup notice](<https://code.claude.com/docs/en/errors#a-skill-command-or-workflow-wasnt-loaded-because-its-name-is-reserved>) names the first item to rename or edit.
* **A plugin named `anthropic-skills`**: it loads. When one of its skills and a synced skill are both named `<name>`, `/anthropic-skills:<name>` runs the synced skill.
* **An MCP server named `anthropic-skills`**: it connects and its tools work, but [its prompts don't appear as commands](<https://code.claude.com/docs/en/mcp#use-mcp-prompts-as-commands>). Rename the server in your MCP configuration to list them.

#### How Claude Code handles the frontmatter of a synced skill

Claude Code applies two rules to a synced skill's frontmatter:

* The frontmatter applies in every kind of session, so an `allowed-tools` grant goes through the normal [permission flow](<https://code.claude.com/docs/en/permissions>). If your organization sets `allowManagedPermissionRulesOnly`, the grant [doesn't apply](<#when-only-managed-permission-rules-apply>).
* Claude Code sanitizes the display text the skill supplies, such as its description. It removes control characters, and in text that reaches Claude, such as the description, it also escapes angle brackets so the text can't imitate Claude Code's internal formatting. This sanitization requires Claude Code v2.1.228 or later.

#### How Claude Code handles the body of a synced skill

What Claude Code does with a synced skill's body depends on where the session runs:

* In a cloud session, the body keeps the behavior a local skill has, because the session runs in an isolated container.
* In a Cowork session on your desktop, the body keeps the behavior a local skill has, except that Claude Code replaces every `!` command line with the [`disableSkillShellExecution` placeholder](<#inject-dynamic-context>), as it does for every skill you supply there.
* In any other session on your machine, Claude Code doesn't run [`!` commands](<#inject-dynamic-context>), doesn't attach the files that `@` references name the way it does for a local skill, and doesn't substitute the `${CLAUDE_PROJECT_DIR}` and `${CLAUDE_SESSION_ID}` placeholders, so the `@` references and both placeholders reach Claude as literal text. A `!` command line reaches Claude as literal text too, or as that placeholder when `disableSkillShellExecution` is on. This handling requires Claude Code v2.1.228 or later.

<h3 id="live-change-detection">
  Edit a skill during a session
</h3>

Claude Code watches skill directories for file changes, except in [bare mode](<https://code.claude.com/docs/en/headless#start-faster-with-bare-mode>). When you add, edit, or remove a skill under `~/.claude/skills/`, the project `.claude/skills/`, or a `.claude/skills/` inside an `--add-dir` directory, Claude Code picks up the change within the current session, without a restart.

If you create a top-level skills directory that didn't exist when the session started, run [`/reload-skills`](<https://code.claude.com/docs/en/commands#all-commands>) to pick up the skills you put there. Claude Code isn't watching that directory yet, so run `/reload-skills` again after each later change there.

Live change detection covers `SKILL.md` text only. For a skill folder that is also a [plugin](<https://code.claude.com/docs/en/plugins/loading#plugins-shared-through-a-repository>), changes to `hooks/`, `.mcp.json`, `agents/`, and `output-styles/` need `/reload-plugins` to take effect.

### Remove a skill

How you remove a skill depends on where it came from:

* **Personal or project skill**: delete the skill's directory, `~/.claude/skills/<skill-name>/` or `.claude/skills/<skill-name>/`. Claude Code [drops it from `/skills` in the current session](<#live-change-detection>); content Claude Code already loaded from it follows the [skill content lifecycle](<#skill-content-lifecycle>).
* **Enterprise skill**: an administrator deletes the skill's directory from `.claude/skills/` inside the [managed settings directory](<https://code.claude.com/docs/en/managed-settings#delivery-mechanisms>), for example `/etc/claude-code/.claude/skills/<skill-name>/` on Linux.
* **Plugin skill**: disable or uninstall the plugin that provides it, from the `/plugin` menu or with `/plugin uninstall <plugin-name>@<marketplace-name>`. Claude Code unloads the plugin's skills when [the change applies](<https://code.claude.com/docs/en/plugins/cli-reference#reload-plugins>) or when you restart.
* **Skill synced from claude.ai**: turn the skill off for your claude.ai account, in the same place you [enabled it](<#skills-in-cowork-and-cloud-sessions>). Claude Code removes it from `~/.claude/skills/synced/` the next time it [syncs your skills](<#where-synced-skills-load>). If you delete the directory by hand instead, the next sync downloads it again while the skill stays enabled on claude.ai.
* **Bundled skill**: set [`disableBundledSkills`](<#bundled-skills>) to `true` to turn off bundled skills, or set one skill to `"off"` in [`skillOverrides`](<#override-skill-visibility-from-settings>) to hide it.

To keep a personal or project skill but stop Claude from invoking it on its own, set [`disable-model-invocation: true`](<#control-who-invokes-a-skill>) in its frontmatter, or `"user-invocable-only"` in [`skillOverrides`](<#override-skill-visibility-from-settings>) when you don't want to edit the file.

## Configure skills

Skills are configured through YAML frontmatter at the top of `SKILL.md` and the markdown content that follows.

### Types of skill content

Skill files can contain any instructions, but thinking about how you want to invoke them helps guide what to include:

**Reference content** adds knowledge Claude applies to your current work. Conventions, patterns, style guides, domain knowledge. This content runs inline so Claude can use it alongside your conversation context.

```yaml theme={null}
---
name: api-conventions
description: API design patterns for this codebase
---

When writing API endpoints:
- Use RESTful naming conventions
- Return consistent error formats
- Include request validation
```

**Task content** gives Claude step-by-step instructions for a specific action, like deployments, commits, or code generation. These are often actions you want to invoke directly with `/skill-name` rather than letting Claude decide when to run them. Add `disable-model-invocation: true` to prevent Claude from triggering it automatically. The example below adds `context: fork`, which runs the skill in its own subagent context; see [Run skills in a subagent](<#run-skills-in-a-subagent>).

```yaml theme={null}
---
name: deploy
description: Deploy the application to production
context: fork
disable-model-invocation: true
---

Deploy the application:
1. Run the test suite
2. Build the application
3. Push to the deployment target
```

Keep the body itself concise. Once a skill loads, its content [stays in context across turns](<#skill-content-lifecycle>), so every line is a recurring token cost. State what to do rather than narrating how or why, and apply the same conciseness test you would for [CLAUDE.md content](<https://code.claude.com/docs/en/best-practices#write-an-effective-claude-md>).

### Frontmatter reference

Configure a skill with YAML [frontmatter](<https://code.claude.com/docs/en/glossary#frontmatter>) between `---` markers at the top of `SKILL.md`, and write the skill's instructions as Markdown after the closing `---`. Field names use lowercase words separated by hyphens, except `when_to_use`. A [command file](<#where-skills-live>) in `.claude/commands/` accepts the same fields except `name` and `paths`. This example sets four fields:

```yaml theme={null}
---
name: my-skill
description: What this skill does
disable-model-invocation: true
allowed-tools: Read Grep
---

Your skill instructions here...
```

All fields are optional. Only `description` is recommended so Claude knows when to use the skill. A field name must match the table exactly, hyphens included: Claude Code ignores a field it doesn't recognize without reporting an error.

Claude Code reads the frontmatter only when the opening `---` is the file's first line. Otherwise it treats the whole file, `---` markers included, as skill content. If the YAML between the markers doesn't parse, the skill still loads with no fields set; see [Skill not triggering](<#skill-not-triggering>) to find and fix the error.

Boolean fields accept `yes`, `no`, `on`, `off`, `1`, and `0` in any letter case, in addition to `true` and `false`. Before v2.1.218, Claude Code recognized only `true` and `false`.

| Field | Required | Description |
| :- | :- | :- |
| `name` | No | Command name shown in the `/` menu. Defaults to the directory name. See [How a skill gets its command name](<#how-a-skill-gets-its-command-name>) for how the field interacts with the name you type to invoke the skill. |
| `description` | Recommended | What the skill does and when to use it. Claude uses this to decide when to apply the skill. If omitted, uses the first non-empty line of the markdown content. Put the key use case first: the combined `description` and `when_to_use` text is truncated at 1,536 characters in the skill listing to reduce context usage. |
| `when_to_use` | No | Additional context for when Claude should invoke the skill, such as trigger phrases or example requests. Appended to `description` in the skill listing and counts toward the 1,536-character cap. |
| `argument-hint` | No | Hint shown during autocomplete to indicate expected arguments. Example: `[issue-number]` or `[filename] [format]`. |
| `arguments` | No | Named positional arguments for [`$name` substitution](<#available-string-substitutions>) in the skill content. Accepts a space-separated string or a YAML list. Names map to argument positions in order. |
| `disable-model-invocation` | No | Set to `true` to prevent Claude from automatically loading this skill. Use for workflows you want to trigger manually with `/name`. Also prevents the skill from being [preloaded into subagents](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>). As of v2.1.196, also prevents the skill from running when a [scheduled task](<https://code.claude.com/docs/en/scheduled-tasks>) fires with the skill as its prompt. Default: `false`. |
| `user-invocable` | No | Set to `false` when only Claude should invoke the skill: Claude Code hides it from the `/` menu and doesn't run it when you type `/name`. Use for background knowledge users shouldn't invoke directly. Default: `true`. |
| `allowed-tools` | No | Tools Claude can use without asking permission during the turn that invokes this skill. The grant clears when you send your next message. Accepts a space- or comma-separated string, or a YAML list. See [Pre-approve tools for a skill](<#pre-approve-tools-for-a-skill>). |
| `disallowed-tools` | No | Tools removed from Claude's available pool while this skill is active. Use for autonomous skills that should never call certain tools, such as `AskUserQuestion` for a background loop. Accepts a space- or comma-separated string, or a YAML list. The restriction clears when you send your next message. Like deny rules, the field can't remove [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) while any other tool remains. |
| `model` | No | Model to use when this skill is active. The override applies for the rest of the current turn and isn't saved to settings. The session model resumes when you send your next prompt. Accepts the same values as [`/model`](<https://code.claude.com/docs/en/model-config>), or `inherit` to keep the active model. A value excluded by your organization's [`availableModels`](<https://code.claude.com/docs/en/model-config#restrict-model-selection>) allowlist isn't used, and the session keeps its current model. In [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), and in [plan mode while the classifier reviews commands](<https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode>), a model that auto mode doesn't support also isn't used, and the session keeps its current model. With `context: fork`, the value sets the [forked subagent's model](<#run-skills-in-a-subagent>) instead, and an excluded value follows the [same rules as a subagent model override](<https://code.claude.com/docs/en/model-config#restrict-model-selection>). |
| `effort` | No | [Effort level](<https://code.claude.com/docs/en/model-config#adjust-effort-level>) when this skill is active. Overrides the session effort level. When you omit it, the level comes from the [effort resolution order](<https://code.claude.com/docs/en/model-config#adjust-effort-level>). Options: `low`, `medium`, `high`, `xhigh`, `max`; available levels depend on the model. |
| `context` | No | Set to `fork` to run in a forked subagent context. See [Run skills in a subagent](<#run-skills-in-a-subagent>). |
| `agent` | No | Which subagent type to use when `context: fork` is set. |
| `background` | No | Only applies with `context: fork`. Set to `false` to wait for the forked subagent's result in the turn that invoked the skill, instead of [running it in the background](<#run-skills-in-a-subagent>). Default: `true`. Requires Claude Code v2.1.218 or later. |
| `hooks` | No | Hooks that Claude Code registers when the skill is invoked and keeps running for the rest of the session. See [Hooks in skills and agents](<https://code.claude.com/docs/en/hooks#hooks-in-skills-and-agents>) for the configuration format and the `once` option. |
| `paths` | No | Glob patterns that limit when this skill is activated. Accepts a comma-separated string or a YAML list. When set, Claude loads the skill automatically only when working with files matching the patterns. Uses the same format as [path-specific rules](<https://code.claude.com/docs/en/memory#path-specific-rules>). |
| `shell` | No | Shell to use for `` !`command` `` and ` ```! ` blocks in this skill. Accepts `bash` (default) or `powershell`. Setting `powershell` runs inline shell commands via PowerShell when the [PowerShell tool](/en/tools-reference#powershell-tool) is enabled: it's on by default on Windows without Git Bash, on by default with Git Bash for claude.ai and Console accounts, and needs `CLAUDE_CODE_USE_POWERSHELL_TOOL=1` in Amazon Bedrock, Google Cloud's Agent Platform, and Microsoft Foundry sessions and on macOS, Linux, and WSL. Set it to `0` to turn the tool off. |
| `metadata` | No | Free-form YAML map for your own key-value data, such as entitlement or catalog fields, read by your own tooling from `SKILL.md`. Claude Code doesn't act on its contents, and drops a value that isn't a map. Don't reuse frontmatter field names such as `paths` as keys. |
| `license` | No | License covering the skill. Part of the [Agent Skills](<https://agentskills.io>) spec; see [Using skill frontmatter outside Claude Code](<#using-skill-frontmatter-outside-claude-code>). Claude Code accepts the field but doesn't act on it. |
| `compatibility` | No | Environment requirements for the skill, such as intended products or system prerequisites, as defined by the [Agent Skills](<https://agentskills.io>) spec; see [Using skill frontmatter outside Claude Code](<#using-skill-frontmatter-outside-claude-code>). Accepts a string of up to 500 characters. Claude Code accepts the field but doesn't act on it. |

#### Using skill frontmatter outside Claude Code

Claude Code accepts every field in the table above. Outside Claude Code, you can use only the fields in the [Agent Skills](<https://agentskills.io>) spec:

| Distribution path | Frontmatter fields you can use |
| :- | :- |
| Claude Code skills at [any level](<#where-skills-live>), including [plugin](<https://code.claude.com/docs/en/plugins/overview>) skills | Every field in the table above |
| claude.ai skill uploads, the Skills API, and packaging with `package_skill.py` from [anthropics/skills](<https://github.com/anthropics/skills>) | `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools` |

When you enable a personal skill for your claude.ai account, for example to use it in [Cowork and cloud sessions](<#skills-in-cowork-and-cloud-sessions>) and routines, you upload it to claude.ai, so the same rules apply.

If you include any field the spec doesn't allow, packaging or upload fails with a hard error instead of ignoring the field:

```
Unexpected key(s) in SKILL.md frontmatter: argument-hint. Allowed properties are: allowed-tools, compatibility, description, license, metadata, name
```

Restricting frontmatter to the spec's six fields avoids the unexpected-key error above. The [Agent Skills spec](<https://agentskills.io>) and the [Skills API requirements](<https://docs.claude.com/en/api/skills-guide>) define everything else those paths validate. Claude Code-only body features, such as [dynamic context injection](<#inject-dynamic-context>), don't function in claude.ai chat or through the API. Claude Code accepts all six fields, so frontmatter that follows the spec loads in Claude Code without changes.

#### How a skill gets its command name

The command you type to invoke a skill comes from where the skill file lives and, for skill directories and plugin skills, from the frontmatter `name` field. In a personal or project skill directory, `name` sets the command that the `/` menu shows and that you type, unless another command already uses that name. The directory name also invokes the skill. In a plugin skill, `name` sets the last segment of the command and the plugin prefix stays in place.

The table below shows where the command name comes from for each layout:

| Skill location | Command name source | Example |
| :- | :- | :- |
| Skill directory under `~/.claude/skills/` or `.claude/skills/` | Frontmatter `name` or the directory name | `.claude/skills/deploy-staging/SKILL.md` → `/deploy-staging`, or `/deploy` with `name: deploy` |
| [Nested](<#where-skills-live>) `.claude/skills/` directory, when the directory name clashes with another skill | Subdirectory path relative to the working directory, then the skill directory name | `apps/web/.claude/skills/deploy/SKILL.md` → `/apps/web:deploy` |
| File under `.claude/commands/` | File name without extension | `.claude/commands/deploy.md` → `/deploy` |
| File in a subdirectory of `.claude/commands/` | Subdirectory path relative to `commands/` with each `/` replaced by `:`, then the file name without extension | `.claude/commands/frontend/component.md` → `/frontend:component` |
| Plugin `skills/` subdirectory | Frontmatter `name` or the directory name, namespaced by plugin | `my-plugin/skills/review/SKILL.md` → `/my-plugin:review`, or `/my-plugin:fancy` with `name: fancy` |
| Plugin root `SKILL.md` | Frontmatter `name`, with the plugin directory name as a fallback | `my-plugin/SKILL.md` with `name: review` → `/my-plugin:review`. See [a single skill at the plugin root](<https://code.claude.com/docs/en/plugins/components#skills>) |
| Skill [synced from claude.ai](<#how-synced-skills-behave>) | The skill's name on your claude.ai account, prefixed with `anthropic-skills:` | Account skill `deploy` → `/anthropic-skills:deploy`, or `/deploy` while no other command uses that name |

In a plugin skill, the frontmatter `name` replaces the directory name in the last segment of the command, so `my-plugin/skills/review/SKILL.md` with `name: fancy` becomes `/my-plugin:fancy`. The bare `/fancy` also invokes the skill unless another command already uses that name. If the `name` you write already starts with the plugin's own prefix, Claude Code doesn't add the prefix again on v2.1.246 or later. For example, `name: my-plugin:fancy` still becomes `/my-plugin:fancy`. From v2.1.216 through v2.1.245, Claude Code doubled the prefix when the `name` already carried it.

In [non-interactive sessions](<https://code.claude.com/docs/en/headless>), the names `help` and `feedback` aren't reserved for their terminal-only built-in commands, so a plugin skill with one of those names keeps its bare command there. Every other terminal-only built-in's name, such as `/login`, stays reserved even though the command can't run in those sessions.

For a plugin-root `SKILL.md`, there is no skill directory to take the name from, so `name` supplies the whole final segment. Without a `name` field, Claude Code falls back to the plugin's directory name.

#### Available string substitutions

Skills support string substitution for dynamic values in the skill content:

| Variable | Description |
| :- | :- |
| `$ARGUMENTS` | All arguments passed when invoking the skill. When no placeholder receives an argument, Claude Code appends them as `ARGUMENTS: <value>`. See [Pass arguments to skills](<#pass-arguments-to-skills>). |
| `$ARGUMENTS[N]` | Access a specific argument by 0-based index, such as `$ARGUMENTS[0]` for the first argument. |
| `$N` | Shorthand for `$ARGUMENTS[N]`, such as `$0` for the first argument or `$1` for the second. |
| `$name` | Named argument declared in the [`arguments`](<#frontmatter-reference>) frontmatter list. Names map to positions in order, so with `arguments: [issue, branch]` the placeholder `$issue` expands to the first argument and `$branch` to the second. |
| `${CLAUDE_SESSION_ID}` | The current session ID. Useful for logging, creating session-specific files, or correlating skill output with sessions. |
| `${CLAUDE_EFFORT}` | The current effort level: `low`, `medium`, `high`, `xhigh`, or `max`. Use this to adapt skill instructions to the active effort setting. |
| `${CLAUDE_SKILL_DIR}` | The directory containing the skill's `SKILL.md` file. For plugin skills, this is the skill's subdirectory within the plugin, not the plugin root. Use this in bash injection commands to reference scripts or files bundled with the skill, regardless of the current working directory. |
| `${CLAUDE_PROJECT_DIR}` | The project root directory. This is the same path [hooks](<https://code.claude.com/docs/en/hooks#reference-scripts-by-path>) and MCP servers receive as `CLAUDE_PROJECT_DIR`. Use this to reference project-local scripts or files, such as `${CLAUDE_PROJECT_DIR}/.claude/hooks/helper.sh`, independent of where the skill is installed. |
| `${CLAUDE_PLUGIN_ROOT}` | The plugin's installation directory. Substituted only in plugin skills. Use this to reference scripts or files bundled anywhere in the plugin, including resources shared between the plugin's skills. See [plugin environment variables](<https://code.claude.com/docs/en/plugins/manifest-reference#environment-variables>). |
| `${CLAUDE_PLUGIN_DATA}` | The plugin's [persistent data directory](<https://code.claude.com/docs/en/plugins/components#path-variables-and-persistent-data>), which survives plugin updates. Substituted only in plugin skills. Use this to reference installed dependencies, generated files, or caches that must outlive an update. |

Claude Code substitutes `${CLAUDE_SKILL_DIR}` and `${CLAUDE_PROJECT_DIR}` in two places: the skill's markdown content, and Bash rules in the [`allowed-tools`](<#frontmatter-reference>) frontmatter. In a plugin skill, Claude Code substitutes `${CLAUDE_PLUGIN_ROOT}` and `${CLAUDE_PLUGIN_DATA}` in the same two places. Using the same variable in both places lets a skill run a bundled script without a permission prompt. The following skill shows the pattern:

```yaml theme={null}
---
name: render-chart
description: Render a chart from a CSV file
allowed-tools: Bash(${CLAUDE_SKILL_DIR}/scripts/render.sh *)
---

Run `${CLAUDE_SKILL_DIR}/scripts/render.sh <csv-file>` to render the chart.
```

If this skill is installed at `~/.claude/skills/render-chart/`, both occurrences of `${CLAUDE_SKILL_DIR}` expand to that directory. The `allowed-tools` rule then matches the exact command the skill body tells Claude to run, so the script runs without prompting.

The `${CLAUDE_PROJECT_DIR}` substitution requires Claude Code v2.1.196 or later.

Indexed arguments use shell-style quoting, so wrap multi-word values in quotes to pass them as a single argument. For example, `/my-skill "hello world" second` makes `$0` expand to `hello world` and `$1` to `second`. The `$ARGUMENTS` placeholder always expands to the full argument string as typed.

An indexed placeholder with no corresponding argument, such as `$2` when only one argument was passed, stays in the content unchanged. A named placeholder from the [`arguments`](<#frontmatter-reference>) frontmatter with no matching argument expands to an empty string.

If you pass an argument value that itself contains text such as `$1` or `$ARGUMENTS`, Claude Code inserts it as literal text and doesn't expand it. For example, if a skill's body contains `Summarize $0` and you run `/summarize "$ARGUMENTS from yesterday"`, Claude receives `Summarize $ARGUMENTS from yesterday`. Claude Code still replaces `${CLAUDE_*}` variables such as `${CLAUDE_SKILL_DIR}` after it inserts the arguments.

To include a literal `$` before a digit, `ARGUMENTS`, or a declared argument name, such as `$1.00` in prose, escape it with a backslash: `\$1.00`. A backslash before any other `$` is left unchanged. Only a single backslash directly before the token escapes it. A doubled backslash such as `\\$1` leaves both backslashes in place, and `$1` still expands to the argument value. The backslash escape covers only these argument placeholders. A backslash doesn't prevent substitution of a `${CLAUDE_*}` variable where the variable applies.

**Example using substitutions:**

```yaml theme={null}
---
name: session-logger
description: Log activity for this session
---

Log the following to logs/${CLAUDE_SESSION_ID}.log:

$ARGUMENTS
```

### Add supporting files

Skills can include multiple files in their directory. This keeps `SKILL.md` focused on the essentials while letting Claude access detailed reference material only when needed. Large reference docs, API specifications, or example collections don't need to load into context every time the skill runs.

```text theme={null}
my-skill/
├── SKILL.md (required - overview and navigation)
├── reference.md (detailed API docs - loaded when needed)
├── examples.md (usage examples - loaded when needed)
└── scripts/
    └── helper.py (utility script - executed, not loaded)
```

Reference supporting files from `SKILL.md` so Claude knows what each file contains and when to load it:

```markdown theme={null}
## Additional resources

- For complete API details, see [reference.md](reference.md)
- For usage examples, see [examples.md](examples.md)
```



**提示：**

Keep `SKILL.md` under 500 lines. Move detailed reference material to separate files.


### Control who invokes a skill

By default, both you and Claude can invoke any skill. You can type `/skill-name` to invoke it directly, and Claude can load it automatically when relevant to your conversation. Two frontmatter fields let you restrict this:

* **`disable-model-invocation: true`**: Claude can't invoke the skill on its own. Use this for workflows with side effects or that you want to control timing, like `/commit`, `/deploy`, or `/send-slack-message`. You don't want Claude deciding to deploy because your code looks ready.

* **`user-invocable: false`**: Only Claude can invoke the skill. Use this for background knowledge that isn't actionable as a command. A `legacy-system-context` skill explains how an old system works. Claude should know this when relevant, but `/legacy-system-context` isn't a meaningful action for users to take.

This example creates a deploy skill. If you set `disable-model-invocation: true`, Claude can't run the skill automatically:

```yaml theme={null}
---
name: deploy
description: Deploy the application to production
disable-model-invocation: true
---

Deploy $ARGUMENTS to production:

1. Run the test suite
2. Build the application
3. Push to the deployment target
4. Verify the deployment succeeded
```

If Claude tries anyway, Claude Code blocks the call and instructs it not to reproduce the deploy steps another way, so expect Claude to suggest running `/deploy` yourself.

Here's how the two fields affect invocation and context loading:

| Frontmatter | You can invoke | Claude can invoke | When loaded into context |
| :- | :- | :- | :- |
| (default) | Yes | Yes | Description always in context, full skill loads when invoked |
| `disable-model-invocation: true` | Yes | Not on its own | Description not in context, full skill loads when invoked |
| `user-invocable: false` | No | Yes | Description always in context, full skill loads when invoked |



**注释：**

  In a regular session, skill descriptions are loaded into context so Claude knows what's available, but full skill content only loads when invoked. [Subagents with preloaded skills](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>) work differently: the full skill content is injected at startup.


#### Where you write the skill's name

To run a skill directly, put its name at the start of your message. After plain text, the name gives Claude permission to run the skill but doesn't run it:

| Where | Example | What happens |
| :- | :- | :- |
| At the start of your message | `/deploy staging` | Claude Code runs the skill directly |
| After plain text, as a separate word with no punctuation attached | `go ahead and /deploy to staging` | Nothing runs directly. The name counts as your permission for that message: Claude can run the skill while it responds, and judges from your wording whether you asked it to |

To write about the skill without permitting a run, leave off the slash.

### Skill content lifecycle

When you or Claude invoke a skill, the rendered `SKILL.md` content enters the conversation as a single message and stays there across later turns. This persistence applies to the skill's instructions, not its permissions: an [`allowed-tools`](<#pre-approve-tools-for-a-skill>) grant clears when you send your next message. Claude Code does not re-read the skill file on later turns, so write guidance that should apply throughout a task as standing instructions rather than one-time steps.

When Claude re-invokes a skill whose rendered content is identical to the copy already in context, Claude Code adds a short note that the skill is already loaded rather than a second copy of the content. When the rendered content differs, because the arguments changed or a [dynamic context](<#inject-dynamic-context>) command produced new output, Claude Code appends the full content again.

[Auto-compaction](<https://code.claude.com/docs/en/how-claude-code-works#when-context-fills-up>) carries invoked skills forward within a token budget. When the conversation is summarized to free context, Claude Code re-attaches the most recent invocation of each skill after the summary, keeping the first 5,000 tokens of each. Re-attached skills share a combined budget of 25,000 tokens. Claude Code fills this budget starting from the most recently invoked skill, so older skills can be dropped entirely after compaction if you have invoked many in one session.

If Claude stops following a skill partway through a session, see [Claude stops following a skill](<#claude-stops-following-a-skill>).

### Pre-approve tools for a skill

The `allowed-tools` field grants permission for the listed tools during the turn that invokes the skill, so Claude can use them without prompting you for approval. The grant clears when you send your next message, even though the skill content [stays in context](<#skill-content-lifecycle>); invoking the skill again re-applies it for that turn. It does not restrict which tools are available: every tool remains callable, and your [permission settings](<https://code.claude.com/docs/en/permissions>) still govern tools that are not listed. To pre-approve tools for the whole session rather than a single turn, add allow rules to those permission settings instead.

Workspace trust doesn't gate this field. Claude Code applies a project skill's `allowed-tools` even in a `-p` run in a folder you've never trusted. A skill can grant itself broad tool access, so review the `allowed-tools` of skills checked into a repository before you run Claude Code there. To withhold the field from repository skills across your organization, see [When only managed permission rules apply](<#when-only-managed-permission-rules-apply>).

This skill lets Claude run git commands without per-use approval whenever you invoke it:

```yaml theme={null}
---
name: commit
description: Stage and commit the current changes
disable-model-invocation: true
allowed-tools: Bash(git add *) Bash(git commit *) Bash(git status *)
---
```

To remove tools from Claude's available pool while a skill is active, list them in `disallowed-tools` in the skill's frontmatter. The restriction clears when you send your next message. Like deny rules, the field can't remove [`EndConversation`](<https://code.claude.com/docs/en/tools-reference#endconversation-tool-behavior>) while any other tool remains. To block tools across all skills and prompts, add deny rules in your [permission settings](<https://code.claude.com/docs/en/permissions>).

#### When only managed permission rules apply

When your organization sets `allowManagedPermissionRulesOnly` in managed settings, Claude Code ignores `allowed-tools` in project and personal skills and in the [other sources the setting's entry lists](<https://code.claude.com/docs/en/settings-reference#allowmanagedpermissionrulesonly>). This requires Claude Code v2.1.282 or later.

The tools an affected skill lists go through your organization's managed rules and the normal permission prompt instead. Run `/status` to list each skill whose `allowed-tools` Claude Code has ignored so far in the session. An injected command in the skill that no managed rule allows follows [Permission checks on injected commands](<#permission-checks-on-injected-commands>).

### Pass arguments to skills

Both you and Claude can pass arguments when invoking a skill. Arguments are available via the `$ARGUMENTS` placeholder.

This skill fixes a GitHub issue by number. The `$ARGUMENTS` placeholder gets replaced with whatever follows the skill name:

```yaml theme={null}
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---

Fix GitHub issue $ARGUMENTS following our coding standards.

1. Read the issue description
2. Understand the requirements
3. Implement the fix
4. Write tests
5. Create a commit
```

When you run `/fix-issue 123`, Claude receives "Fix GitHub issue 123 following our coding standards..."

If you invoke a skill with arguments but no placeholder in the skill's content receives one, Claude Code appends `ARGUMENTS: <your input>` to the end of the skill content so Claude still sees what you typed. A placeholder is `$ARGUMENTS`, an indexed form such as `$1`, or a named argument. An indexed placeholder with no argument at its position stays as literal text and doesn't count as receiving one. A named placeholder counts even when its position has no argument, because it expands to an empty string.

You can also stack several skills at the start of one message. Typing `/write-tests /fix-issue 123` loads both skills and passes the trailing text `123` as `$ARGUMENTS` to each of them.

Claude Code expands the first skill plus up to five more stacked after it. Expansion stops at the first token that isn't an inline user-invocable skill, so a skill that runs as a [forked subagent](<#run-skills-in-a-subagent>), such as [`/code-review`](<https://code.claude.com/docs/en/code-review#review-a-diff-locally>), or one whose arguments may themselves start with a slash command, such as `/loop`, also ends the run there. That token and everything after it become the argument text for every expanded skill. `/code-review` runs as a forked subagent from v2.1.218; on earlier versions it ran inline and stacked.

To access individual arguments by position, use `$ARGUMENTS[N]` or the shorter `$N`:

```yaml theme={null}
---
name: migrate-component
description: Migrate a component from one language to another
---

Migrate the $ARGUMENTS[0] component from $ARGUMENTS[1] to $ARGUMENTS[2].
Preserve all existing behavior and tests.
```

Running `/migrate-component SearchBar JavaScript TypeScript` replaces `$ARGUMENTS[0]` with `SearchBar`, `$ARGUMENTS[1]` with `JavaScript`, and `$ARGUMENTS[2]` with `TypeScript`. The same skill using the `$N` shorthand:

```yaml theme={null}
---
name: migrate-component
description: Migrate a component from one language to another
---

Migrate the $0 component from $1 to $2.
Preserve all existing behavior and tests.
```

## Advanced patterns

### Inject dynamic context

The `` !`<command>` `` syntax runs shell commands before the skill content is sent to Claude. The command output replaces the placeholder, so Claude receives actual data, not the command itself. Claude Code doesn't run these commands on your machine when the skill is [synced from your claude.ai account](<#how-claude-code-handles-the-body-of-a-synced-skill>). This restriction requires Claude Code v2.1.228 or later.

This skill summarizes a pull request by fetching live PR data with the GitHub CLI. The `` !`gh pr diff` `` and other commands run first, and their output gets inserted into the prompt:

```yaml theme={null}
---
name: pr-summary
description: Summarize changes in a pull request
context: fork
agent: Explore
allowed-tools: Bash(gh *)
---

## Pull request context
- PR diff: !`gh pr diff`
- PR comments: !`gh pr view --comments`
- Changed files: !`gh pr diff --name-only`

## Your task
Summarize this pull request...
```

Substitution runs once over the original file. Command output is inserted as plain text and is not re-scanned for further `` !`<command>` `` placeholders, so a command cannot emit a placeholder for a later pass to expand.

The inline form is only recognized when `!` appears at the start of a line or immediately after whitespace. If `!` follows another character, as in `` KEY=!`cmd` ``, the placeholder is left as literal text and the command does not run.

For multi-line commands, use a fenced code block opened with ` ```! ` instead of the inline form:

````markdown theme={null}
## Environment
```!
node --version
git status --short
```
````

To disable this behavior for skills and custom commands from user, project, plugin, or [additional-directory](<#skills-from-additional-directories>) sources, set `"disableSkillShellExecution": true` in [settings](<https://code.claude.com/docs/en/settings>). Each command is replaced with `[shell command execution disabled by policy]` instead of being run. Bundled and managed skills are not affected. This setting is most useful in [managed settings](<https://code.claude.com/docs/en/managed-settings>), where users cannot override it.

Claude Code never runs these commands on your machine when they appear in skills [synced from your claude.ai account](<#how-synced-skills-behave>), regardless of this setting. This restriction requires Claude Code v2.1.228 or later. [How Claude Code handles the body of a synced skill](<#how-claude-code-handles-the-body-of-a-synced-skill>) says what Claude receives in place of the command in each kind of session.



**提示：**

  To request deeper reasoning when a skill runs, include `ultrathink` anywhere in the skill content. See [Use ultrathink for one-off deep reasoning](<https://code.claude.com/docs/en/model-config#use-ultrathink-for-one-off-deep-reasoning>).


#### How injected commands run

Claude Code picks the tool that runs a skill's injected commands from the `shell` key in the skill's frontmatter and your environment. Every combination runs the commands through the Bash tool or the PowerShell tool, except one that fails the invocation outright:

* `shell: powershell`, with the [PowerShell tool](<https://code.claude.com/docs/en/tools-reference#powershell-tool>) enabled: the commands run through the PowerShell tool.
* `shell: bash` when bash isn't available: the invocation fails before any command runs. This happens on Windows without Git Bash. Claude Code shows ``Skill <name> requires bash (`shell: bash` in frontmatter) but Git Bash was not found``.
* Any other combination: the commands run through the Bash tool when bash is available. When it isn't, they run through the PowerShell tool.

Either tool runs the commands the same way it runs Claude's own shell commands. They share the working directory, timeout, and output handling:

* **Working directory**: Claude Code runs each command in the session shell's current working directory. That directory moves when Claude runs `cd`. Use [`${CLAUDE_SKILL_DIR}` or `${CLAUDE_PROJECT_DIR}`](<#available-string-substitutions>) in paths that must resolve the same way every time.
* **stderr**: with the default `bash` shell, Claude Code merges stderr into stdout. Anything the command writes to stderr appears in the injected text.
* **Timeout**: each command runs under the Bash tool's default 2-minute [timeout](<https://code.claude.com/docs/en/tools-reference#timeout-and-output-limits>). When the Bash tool [moves a timed-out command to the background](<https://code.claude.com/docs/en/tools-reference#foreground-commands-that-move-to-the-background>), the skill still renders. The injected text reports the move and names the background task and the file collecting the command's output. When the command is one the Bash tool never auto-backgrounds, Claude Code kills it at the timeout. That failure [aborts the invocation](<#when-an-injected-command-fails>).
* **Output size**: output past the Bash tool's inline ceiling arrives as a file path plus a short preview, not truncated text. [Output limits](<https://code.claude.com/docs/en/tools-reference#output-limits>) covers the ceiling and how to adjust each boundary.

The PowerShell tool applies the same timeout, backgrounding, and output-ceiling behavior to the commands it runs. See the [PowerShell tool](<https://code.claude.com/docs/en/tools-reference#powershell-tool>) section for its specifics.

#### When an injected command fails

A failed command aborts the entire skill invocation, not just its own placeholder. Claude never sees the skill content for that invocation. The abort shows `Shell command failed for pattern "..."`. The error message includes the command's output under `[stderr]`.

With the default `bash` shell, any non-zero exit code counts as a failure. One carveout applies: Claude Code treats exit code 1 from [search and comparison commands](<https://code.claude.com/docs/en/tools-reference#output-limits>) as a normal result and injects their output. Exit codes of 2 or higher fail even for those commands.

Which commands get the carveout depends on the shell:

* Default `bash` shell: the commands listed under [Output limits](<https://code.claude.com/docs/en/tools-reference#output-limits>)
* `shell: powershell`, when the PowerShell tool is enabled: a [different set](<https://code.claude.com/docs/en/tools-reference#shell-selection-in-settings-hooks-and-skills>) that includes `grep` and `git diff` but not `find` or `diff`

With the default `bash` shell, append `|| true` to any other command you expect to exit non-zero. A check script that exits 1 when it finds problems is one example.

#### Permission checks on injected commands

Injected commands never prompt for permission while the skill renders. Claude Code checks each one against your [permission rules](<https://code.claude.com/docs/en/permissions>) first. A command a deny rule matches aborts the invocation with `Shell command permission check failed for pattern "..."`.

Outside [auto mode](<https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode>), when a command's permission check returns anything other than allow, Claude Code aborts the invocation with the same error. This includes a rule that would normally ask you. To keep an unmatched command from aborting here, pre-approve it with [`allowed-tools`](<#pre-approve-tools-for-a-skill>). If your organization restricts permission rules to managed settings, see [When only managed permission rules apply](<#when-only-managed-permission-rules-apply>). Deny and ask rules still override `allowed-tools`. See [Manage permissions](<https://code.claude.com/docs/en/permissions#manage-permissions>).

In auto mode, a command that would otherwise need your approval doesn't abort the invocation. The skill loads with an instruction telling Claude to run the command first, and Claude's own call then goes through [auto mode's usual checks](<https://code.claude.com/docs/en/permission-modes#how-the-classifier-evaluates-actions>). The invocation still aborts in a [forked skill](<#run-skills-in-a-subagent>) that sets `agent`, and in a session where Claude doesn't have the [shell tool that runs injected commands](<#how-injected-commands-run>).

### Run skills in a subagent

Add `context: fork` to your frontmatter when you want a skill to run in isolation. Claude Code starts a new subagent of the type set in the `agent` field and gives it the skill content as its prompt. The subagent doesn't see your conversation history, so the skill's instructions have to stand on their own.



**注释：**

  Despite the name, a skill with `context: fork` doesn't run in a [fork of the current conversation](<https://code.claude.com/docs/en/sub-agents#fork-the-current-conversation>), which would hand the subagent everything you've discussed so far. When the task depends on that history, fork the conversation instead of using `context: fork`.


The forked subagent runs in the [background](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>): you keep working while it runs, and its result arrives in your conversation when it completes. Set `background: false` in the frontmatter to instead wait for the result in the turn that invoked the skill. Before v2.1.218, forked skills always blocked the turn until they finished.

Claude Code also waits for the result, even when the skill doesn't set `background: false`, in cases like these:

* In non-interactive mode, with the `-p` flag or the Agent SDK
* When you set [`CLAUDE_CODE_DISABLE_BACKGROUND_TASKS`](<https://code.claude.com/docs/en/env-vars>) to `1`, which also turns off all other background task features
* When you invoke a forked skill while an earlier invocation of the same skill is still running
* When a [scheduled task](<https://code.claude.com/docs/en/scheduled-tasks>) fires with the skill as its prompt

A backgrounded fork also runs with the [narrower tool set that applies to background subagents](<https://code.claude.com/docs/en/sub-agents#run-subagents-in-foreground-or-background>): the skill's subagent is a regular agent type, so the exemption for subagents that fork the conversation doesn't cover it. If your skill's steps depend on a tool outside that set, set `background: false` to keep the full tool set.

A forked skill that runs in the background applies its edits outside your session's [checkpoints](<https://code.claude.com/docs/en/checkpointing>), so `/rewind` doesn't undo them; use git to revert them.



**注意：**

  `context: fork` only makes sense for skills with explicit instructions. If your skill contains guidelines like "use these API conventions" without a task, the subagent receives the guidelines but no actionable prompt, and returns without meaningful output.


Skills and [subagents](<https://code.claude.com/docs/en/sub-agents>) work together in two directions:

| Approach | System prompt | Task | Also loads |
| :- | :- | :- | :- |
| Skill with `context: fork` | From agent type | SKILL.md content | CLAUDE.md, per the agent's [startup context](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>) |
| Subagent with `skills` field | Subagent's markdown body | Claude's delegation message | Preloaded skills + CLAUDE.md, per the subagent's [startup context](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>) |

With `context: fork`, you write the task in your skill and pick an agent type to execute it. The built-in Explore and Plan agents [skip CLAUDE.md and git status](<https://code.claude.com/docs/en/sub-agents#what-loads-at-startup>) to keep their context small, so a forked skill using `agent: Explore` sees only the SKILL.md content and the agent's own system prompt. For the inverse, where you define a custom subagent that uses skills as reference material, see [Subagents](<https://code.claude.com/docs/en/sub-agents#preload-skills-into-subagents>).

#### Example: Research skill using Explore agent

This skill runs research in a forked Explore agent. The skill content becomes the task, and the agent provides read-only tools optimized for codebase exploration:

```yaml theme={null}
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
---

Research $ARGUMENTS thoroughly:

1. Find relevant files using Glob and Grep
2. Read and analyze the code
3. Summarize findings with specific file references
```

When this skill runs:

1. A new isolated context is created
2. The subagent receives the skill content as its prompt (the "Research \$ARGUMENTS thoroughly" instructions)
3. The `agent` field determines the execution environment (model, tools, and permissions)
4. The subagent summarizes its results and returns them to your main conversation when it finishes

The `agent` field specifies which subagent configuration to use. Options include built-in agents (`Explore`, `Plan`, `general-purpose`) or any custom subagent from `.claude/agents/`. If omitted, uses `general-purpose`.

### Restrict Claude's skill access

By default, Claude can invoke any skill that doesn't have `disable-model-invocation: true` set. Skills that define [`allowed-tools`](<#pre-approve-tools-for-a-skill>) grant Claude access to those tools without per-use approval during the turn that invokes the skill; the grant clears when you send your next message. Your [permission settings](<https://code.claude.com/docs/en/permissions>) still govern baseline approval behavior for all other tools. A few built-in commands are also available through the Skill tool, including `/init` and `/security-review`. Other built-in commands such as `/compact` are not.

Three ways to control which skills Claude can invoke:

**Disable all skills** by denying the Skill tool in `/permissions`:

```text theme={null}
# Add to deny rules:
Skill
```

**Allow or deny specific skills** using [permission rules](<https://code.claude.com/docs/en/permissions>):

```text theme={null}
# Allow only specific skills
Skill(commit)
Skill(review-pr *)

# Deny specific skills
Skill(deploy *)
```

Permission syntax: `Skill(name)` for exact match, `Skill(name *)` for prefix match with any arguments.

The table shows what a `deny` rule blocks beyond the name you write, by the kind of name in the rule.

| Your `deny` rule names | Example rule | Claude Code also blocks |
| :- | :- | :- |
| An alias | `Skill(review)` | The bundled `/code-review`, through its `/review` alias |
| An unqualified name | `Skill(deploy)` | A [nested skill](<#where-skills-live>) listed as `apps/web:deploy` |
| A [skill synced from claude.ai](<#how-synced-skills-behave>) | `Skill(anthropic-skills:deploy)` | That skill when Claude Desktop delivers it to a session as a plugin |
| The plugin form of a synced skill | `Skill(deploy:deploy)` | The synced skill |
| A skill in the [parameter form](<https://code.claude.com/docs/en/permissions#match-by-input-parameter>) | `Skill(skill:deploy)` | The skill whichever of its names Claude calls it by, including its alias and display name |

Before v2.1.260, Claude Code didn't block a nested skill listed under its qualified name when the deny rule named only the unqualified name.

Claude Code matches an `allow` rule only against the skill's own name and the name in Claude's invocation. To approve a [synced skill](<#how-synced-skills-behave>) without a prompt, name it inside its [reserved namespace](<#names-reserved-for-synced-skills>):

* `Skill(anthropic-skills:pdf)` approves the synced `pdf` skill
* `Skill(anthropic-skills *)` approves every synced skill
* `Skill(anthropic *)` doesn't cover `anthropic-skills:pdf`, because a prefix outside the namespace doesn't match the names inside it

**Hide individual skills** by adding `disable-model-invocation: true` to their frontmatter. This removes the skill from Claude's context entirely.



**注释：**

  With `user-invocable: false`, you can't invoke the skill, but Claude still can. To keep Claude from invoking it through the Skill tool, set `disable-model-invocation: true`.


### Override skill visibility from settings

The `skillOverrides` setting controls skill visibility from your [settings](<https://code.claude.com/docs/en/settings>) instead of the skill's own frontmatter. Use it for skills whose SKILL.md you don't want to edit, such as ones checked into a shared project repo. The `/skills` menu writes it for you: highlight a skill and press `Space` to cycle states, then `Esc` to save to `.claude/settings.local.json`.

Each key is a skill name and each value is one of four states:

| Value | Listed to Claude | In `/` menu |
| :- | :- | :- |
| `"on"` | Name and description | Yes |
| `"name-only"` | Name only | Yes |
| `"user-invocable-only"` | Hidden | Yes |
| `"off"` | Hidden | Hidden |

The `/skills` menu labels the `"user-invocable-only"` state `user-only`.

`"off"` also hides the skill from the command lists advertised to [Remote Control](<https://code.claude.com/docs/en/remote-control>) clients and to [Agent SDK](<https://code.claude.com/docs/en/agent-sdk/skills#discover-available-commands>) callers, in addition to the terminal `/` menu. Invoking a hidden skill by its full name returns the `skillOverrides` error instead of running it.

A skill that is absent from `skillOverrides` is treated as `"on"`. The example below collapses one skill to its name and turns another off entirely:

```json theme={null}
{
  "skillOverrides": {
    "legacy-context": "name-only",
    "deploy": "off"
  }
}
```

Some bundled skills have aliases, such as `checkup` for `/doctor`. If you set a `skillOverrides` entry under an alias in [managed settings](<https://code.claude.com/docs/en/managed-settings>) or in a file you pass with the `--settings` flag, Claude Code applies it to the skill behind the alias. You can only restrict a skill further through an alias, never make it more visible, and if you also set an entry under the skill's own name in managed settings, that entry takes precedence. Before v2.1.260, Claude Code didn't apply an entry under an alias to the skill in any settings source.

In user, project, and local settings, Claude Code matches entries against skill names only. If you set an entry for `review` there, it applies to a skill named `review`, not to the bundled `/code-review` through its `/review` alias.

Plugin skills are not affected by `skillOverrides`. Manage those through `/plugin` instead.

### Find unused skills

Every skill in the [skill listing](<#skill-descriptions-are-cut-short>) adds to your context on every turn, whether or not Claude ever uses it. Run `/skill-doctor` to see what each of your skills costs and how often it gets used, so you can decide which ones to turn off. In an interactive session, the report opens in the `/plugin` manager's **Stats** tab. In [non-interactive mode](<https://code.claude.com/docs/en/headless>) with `-p`, Claude Code prints it as text.

The report covers the skills in your session other than bundled skills and enterprise skills. It flags skills in the listing that have never been invoked and says where to turn them off. Of the skills it tells you where to turn off, start with the ones that have the highest context cost. The report also lists plugins you haven't used recently.

`/skill-doctor` requires Claude Code v2.1.252 or later and isn't available in sessions that skip [feature-flag fetching](<https://code.claude.com/docs/en/env-vars#features-that-need-feature-flag-fetching>). If you run `/skill-doctor` over [Remote Control](<https://code.claude.com/docs/en/remote-control>) from your phone or browser, Claude Code replies [`Skill usage reports are not available on this connection.`](<https://code.claude.com/docs/en/errors#skill-usage-reports-are-not-available-on-this-connection>) instead. Run `/skill-doctor` in the terminal on the machine where the session is running.

## Evaluate and iterate on a skill

Seeing a skill trigger tells you Claude found it, not that it did what you intended. To know a skill is working, measure separately whether Claude invokes it on the prompts it should, and whether the output matches what you expect when it does.

The check for both is a baseline comparison. Collect a few realistic prompts, run each one in a fresh session with the skill available and again with it turned off, and compare the results. A fresh session matters because leftover context from authoring the skill will mask gaps in the written instructions.

How you turn the skill off for the second run depends on where it comes from:

* **Personal or project skill**: set it to `"off"` in [`skillOverrides`](<#override-skill-visibility-from-settings>).
* **Skill that a plugin provides**: `skillOverrides` doesn't apply to plugin skills. Use [`claude plugin eval`](<https://code.claude.com/docs/en/plugin-evals#the-no-plugin-baseline>) instead, which repeats each run with no plugin loaded.

Two tools automate the baseline comparison. For a skill that ships in a [plugin](<https://code.claude.com/docs/en/plugins/overview>), [`claude plugin eval`](<https://code.claude.com/docs/en/plugin-evals>) runs each prompt in an isolated session with and without the plugin, scores it with graders you define or that it writes for you, and exits non-zero below a threshold so you can gate CI on it. For iterating on a single skill inside a Claude Code conversation, the skill-creator plugin below runs a similar loop with its own `evals/evals.json` format. The two formats aren't interchangeable.

### Run evals with skill-creator

The [`skill-creator` plugin](<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator>) automates the comparison loop inside Claude Code. In the VS Code extension or the desktop app, install it from the official marketplace by following [Install a plugin](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>). In a terminal, start Claude Code by running `claude`, then enter this at its prompt:

```text theme={null}
/plugin install skill-creator@claude-plugins-official
```

If the install fails, match the message Claude Code reports:

* `Marketplace "claude-plugins-official" not found`: add the marketplace with `/plugin marketplace add anthropics/claude-plugins-official`, then retry the install.
* The plugin is [not found in the marketplace](<https://code.claude.com/docs/en/plugins/install#install-a-plugin>): check the plugin name.

If the install summary reports `Run /reload-plugins to apply.`, Claude Code then runs that reload for you. If the reload warns that your next message would re-read the conversation, run `/reload-plugins --force` to make the plugin's skills available in the current session. Then ask Claude to evaluate an existing skill, for example `evaluate my summarize-changes skill with skill-creator`. The plugin walks you through writing test cases and runs the loop:

* **Test cases**: stores prompts, input files, and expected behavior in `evals/evals.json` inside the skill directory
* **Isolated runs**: spawns a [subagent](<https://code.claude.com/docs/en/sub-agents>) per test case so each run starts with a clean context, and records token count and duration
* **Grading**: checks each assertion against the output and writes pass or fail with evidence to `grading.json`
* **Benchmark**: aggregates pass rate, time, and tokens for with-skill versus without-skill into `benchmark.json` so you can compare the pass-rate improvement against the token and time overhead
* **Version comparison**: runs a blind A/B between two versions of the skill so you can confirm an edit is an improvement before committing it
* **Description tuning**: generates should-trigger and should-not-trigger prompts, measures the hit rate, and proposes description edits when the skill activates on the wrong requests
* **Review viewer**: opens an HTML report where you inspect each output and record qualitative feedback that the next iteration reads

For the eval file format and the full iteration workflow, see [Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>) on agentskills.io. For background on the benchmark and comparison modes, see the [skill-creator announcement](<https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills>).

## Share skills

Skills can be distributed at different scopes depending on your audience:

* **Project skills**: Commit `.claude/skills/` to version control
* **Plugins**: Create a `skills/` directory in your [plugin](<https://code.claude.com/docs/en/plugins/overview>)
* **Managed**: Deploy organization-wide through [managed settings](<https://code.claude.com/docs/en/managed-settings>)

### Generate visual output

Skills can bundle and run scripts in any language, giving Claude capabilities beyond what's possible in a single prompt. One pattern is generating visual output: interactive HTML files that open in your browser for exploring data, debugging, or creating reports.

This example creates a codebase explorer: an interactive tree view where you can expand and collapse directories, see file sizes at a glance, and identify file types by color.

Create the Skill directory:

```bash theme={null}
mkdir -p ~/.claude/skills/codebase-visualizer/scripts
```

Save this to `~/.claude/skills/codebase-visualizer/SKILL.md`. The description tells Claude when to activate this Skill, and the instructions tell Claude to run the bundled script. The script path uses [`${CLAUDE_SKILL_DIR}`](<#available-string-substitutions>) so it resolves correctly whether the skill is installed at the personal, project, or plugin level:

````yaml theme={null}
---
name: codebase-visualizer
description: Generate an interactive collapsible tree visualization of your codebase. Use when exploring a new repo, understanding project structure, or identifying large files.
allowed-tools: Bash(python3 *)
---

# Codebase Visualizer

Generate an interactive HTML tree view that shows your project's file structure with collapsible directories.

## Usage

Run the visualization script from your project root:

```bash
python3 ${CLAUDE_SKILL_DIR}/scripts/visualize.py .
```

This creates `codebase-map.html` in the current directory and opens it in your default browser.

## What the visualization shows

- **Collapsible directories**: Click folders to expand/collapse
- **File sizes**: Displayed next to each file
- **Colors**: Different colors for different file types
- **Directory totals**: Shows aggregate size of each folder
````

Save this to `~/.claude/skills/codebase-visualizer/scripts/visualize.py`. This script scans a directory tree and generates a self-contained HTML file with:

* A **summary sidebar** showing file count, directory count, total size, and number of file types
* A **bar chart** breaking down the codebase by file type (top 8 by size)
* A **collapsible tree** where you can expand and collapse directories, with color-coded file type indicators

The script requires Python 3 but uses only built-in libraries, so there are no packages to install:

```python expandable theme={null}
#!/usr/bin/env python3
"""Generate an interactive collapsible tree visualization of a codebase."""

import json
import sys
import webbrowser
from html import escape
from pathlib import Path
from collections import Counter

IGNORE = {'.git', 'node_modules', '__pycache__', '.venv', 'venv', 'dist', 'build'}

def scan(path: Path, stats: dict) -> dict:
    result = {"name": path.name, "children": [], "size": 0}
    try:
        for item in sorted(path.iterdir()):
            if item.name in IGNORE or item.name.startswith('.'):
                continue
            if item.is_file():
                size = item.stat().st_size
                ext = item.suffix.lower() or '(no ext)'
                result["children"].append({"name": item.name, "size": size, "ext": ext})
                result["size"] += size
                stats["files"] += 1
                stats["extensions"][ext] += 1
                stats["ext_sizes"][ext] += size
            elif item.is_dir():
                stats["dirs"] += 1
                child = scan(item, stats)
                if child["children"]:
                    result["children"].append(child)
                    result["size"] += child["size"]
    except PermissionError:
        pass
    return result

def generate_html(data: dict, stats: dict, output: Path) -> None:
    ext_sizes = stats["ext_sizes"]
    total_size = sum(ext_sizes.values()) or 1
    sorted_exts = sorted(ext_sizes.items(), key=lambda x: -x[1])[:8]
    colors = {
        '.js': '#f7df1e', '.ts': '#3178c6', '.py': '#3776ab', '.go': '#00add8',
        '.rs': '#dea584', '.rb': '#cc342d', '.css': '#264de4', '.html': '#e34c26',
        '.json': '#6b7280', '.md': '#083fa1', '.yaml': '#cb171e', '.yml': '#cb171e',
        '.mdx': '#083fa1', '.tsx': '#3178c6', '.jsx': '#61dafb', '.sh': '#4eaa25',
    }
    lang_bars = "".join(
        f'<div class="bar-row"><span class="bar-label">{ext}</span>'
        f'<div class="bar" style="width:{(size/total_size)*100}%;background:{colors.get(ext,"#6b7280")}"></div>'
        f'<span class="bar-pct">{(size/total_size)*100:.1f}%</span></div>'
        for ext, size in sorted_exts
    )
    def fmt(b):
        if b < 1024: return f"{b} B"
        if b < 1048576: return f"{b/1024:.1f} KB"
        return f"{b/1048576:.1f} MB"

    html = f'''<!DOCTYPE html>
<html><head>
  <meta charset="utf-8"><title>Codebase Explorer</title>
  <style>
    body {{ font: 14px/1.5 system-ui, sans-serif; margin: 0; background: #1a1a2e; color: #eee; }}
    .container {{ display: flex; height: 100vh; }}
    .sidebar {{ width: 280px; background: #252542; padding: 20px; border-right: 1px solid #3d3d5c; overflow-y: auto; flex-shrink: 0; }}
    .main {{ flex: 1; padding: 20px; overflow-y: auto; }}
    h1 {{ margin: 0 0 10px 0; font-size: 18px; }}
    h2 {{ margin: 20px 0 10px 0; font-size: 14px; color: #888; text-transform: uppercase; }}
    .stat {{ display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid #3d3d5c; }}
    .stat-value {{ font-weight: bold; }}
    .bar-row {{ display: flex; align-items: center; margin: 6px 0; }}
    .bar-label {{ width: 55px; font-size: 12px; color: #aaa; }}
    .bar {{ height: 18px; border-radius: 3px; }}
    .bar-pct {{ margin-left: 8px; font-size: 12px; color: #666; }}
    .tree {{ list-style: none; padding-left: 20px; }}
    details {{ cursor: pointer; }}
    summary {{ padding: 4px 8px; border-radius: 4px; }}
    summary:hover {{ background: #2d2d44; }}
    .folder {{ color: #ffd700; }}
    .file {{ display: flex; align-items: center; padding: 4px 8px; border-radius: 4px; }}
    .file:hover {{ background: #2d2d44; }}
    .size {{ color: #888; margin-left: auto; font-size: 12px; }}
    .dot {{ width: 8px; height: 8px; border-radius: 50%; margin-right: 8px; }}
  </style>
</head><body>
  <div class="container">
    <div class="sidebar">
      <h1>📊 Summary</h1>
      <div class="stat"><span>Files</span><span class="stat-value">{stats["files"]:,}</span></div>
      <div class="stat"><span>Directories</span><span class="stat-value">{stats["dirs"]:,}</span></div>
      <div class="stat"><span>Total size</span><span class="stat-value">{fmt(data["size"])}</span></div>
      <div class="stat"><span>File types</span><span class="stat-value">{len(stats["extensions"])}</span></div>
      <h2>By file type</h2>
      {lang_bars}
    </div>
    <div class="main">
      <h1>📁 {escape(data["name"])}</h1>
      <ul class="tree" id="root"></ul>
    </div>
  </div>
  <script>
    const data = {json.dumps(data)};
    const colors = {json.dumps(colors)};
    function fmt(b) {{ if (b < 1024) return b + ' B'; if (b < 1048576) return (b/1024).toFixed(1) + ' KB'; return (b/1048576).toFixed(1) + ' MB'; }}
    function esc(s) {{ return s.replace(/[&<>"']/g, c => ({{"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}}[c])); }}
    function render(node, parent) {{
      if (node.children) {{
        const det = document.createElement('details');
        det.open = parent === document.getElementById('root');
        det.innerHTML = `<summary><span class="folder">📁 ${{esc(node.name)}}</span><span class="size">${{fmt(node.size)}}</span></summary>`;
        const ul = document.createElement('ul'); ul.className = 'tree';
        node.children.sort((a,b) => (b.children?1:0)-(a.children?1:0) || a.name.localeCompare(b.name));
        node.children.forEach(c => render(c, ul));
        det.appendChild(ul);
        const li = document.createElement('li'); li.appendChild(det); parent.appendChild(li);
      }} else {{
        const li = document.createElement('li'); li.className = 'file';
        li.innerHTML = `<span class="dot" style="background:${{colors[node.ext]||'#6b7280'}}"></span>${{esc(node.name)}}<span class="size">${{fmt(node.size)}}</span>`;
        parent.appendChild(li);
      }}
    }}
    data.children.forEach(c => render(c, document.getElementById('root')));
  </script>
</body></html>'''
    output.write_text(html)

if __name__ == '__main__':
    target = Path(sys.argv[1] if len(sys.argv) > 1 else '.').resolve()
    stats = {"files": 0, "dirs": 0, "extensions": Counter(), "ext_sizes": Counter()}
    data = scan(target, stats)
    out = Path('codebase-map.html')
    generate_html(data, stats, out)
    print(f'Generated {out.absolute()}')
    webbrowser.open(f'file://{out.absolute()}')
```

To test, open Claude Code in any project and ask "Visualize this codebase." Claude runs the script, which prints the generated file's path, such as `Generated /path/to/codebase-map.html`, and opens it in your browser. If you work in a headless environment where no browser opens, the printed path confirms the script succeeded.

This pattern works for any visual output: dependency graphs, test coverage reports, API documentation, or database schema visualizations. The bundled script does the work while Claude handles orchestration.

## Troubleshooting

### Skill not triggering

If Claude doesn't use your skill when expected:

1. Check the description includes keywords users would naturally say
2. Verify the skill appears in `What skills are available?`
3. Try rephrasing your request to match the description more closely
4. Invoke it directly with `/skill-name` if the skill is user-invocable

If the frontmatter YAML is malformed, Claude Code loads the skill body with empty metadata, so `/skill-name` still works but Claude can't match against your `description`. Run with `--debug` to see the parse error.

If the skill ships in a plugin, you can measure how often it triggers across realistic prompts rather than checking one at a time: write an eval case with a [`tool_used: Skill` grader](<https://code.claude.com/docs/en/plugin-evals#create-your-first-eval-suite>) and run it with `claude plugin eval` after each description change.

To find `SKILL.md` files whose frontmatter doesn't parse, run [`claude plugin validate`](<https://code.claude.com/docs/en/plugins/cli-reference#validate-a-directory>) on the skills directory, for example `claude plugin validate .claude/skills` for project skills or `claude plugin validate ~/.claude/skills` for personal skills. Requires Claude Code v2.1.233 or later.

### Skill triggers too often

If Claude uses your skill when you don't want it:

1. Make the description more specific
2. Add `disable-model-invocation: true` if you only want manual invocation

### Claude stops following a skill

If Claude follows a skill in its first response and stops following it later, start with whichever of these cases matches:

* **Claude skipped a rule that must hold every time**: move the rule into a [hook](<https://code.claude.com/docs/en/hooks-guide>). Claude Code runs a hook every time its event occurs, such as before each file edit, whether or not Claude is following the skill. To keep the rule with the skill, define the hook in the skill's [`hooks` frontmatter](<https://code.claude.com/docs/en/hooks#hooks-in-skills-and-agents>). That hook applies from the time the skill is invoked until the session ends.
* **Claude skipped guidance it should apply with judgment**: word the guidance so it applies to the whole task, for example "Run the tests after every edit" rather than "Run the tests". Claude Code adds the skill's content to the conversation when the skill is invoked and [doesn't re-read the file](<#skill-content-lifecycle>) on later turns.
* **The conversation was compacted**: invoke the skill again to restore its full content. After [compaction](<https://code.claude.com/docs/en/how-claude-code-works#when-context-fills-up>), Claude Code [can keep only the start of an invoked skill](<#skill-content-lifecycle>), so put the most important instructions near the top of `SKILL.md`.

### Skill descriptions are cut short

Claude Code loads a listing of skill names and descriptions into context so Claude knows what's available. The listing always contains every skill name, but if you have many skills, Claude Code drops some descriptions to fit the listing's character budget, which removes the keywords Claude needs to match your request. The budget scales at 1% of the model's context window. When the listing overflows, Claude Code drops descriptions starting with the skills you invoke least, so the skills you use most keep their full text.

Run `/doctor` for an estimate of the listing's context cost and its biggest contributors. To find skills worth turning off, run [`/skill-doctor`](<#find-unused-skills>). When the listing exceeds its budget, Claude Code also writes a warning to the debug log, visible with [`--debug`](<https://code.claude.com/docs/en/cli-reference#cli-flags>).

The Skills row in `/context` reports the size of the listing after the budget is applied, so it matches what the model receives. Before v2.1.196, the row counted the full text of every description and could show a value several times larger than the configured budget.

To raise the budget, set the [`skillListingBudgetFraction`](<https://code.claude.com/docs/en/settings-reference#skilllistingbudgetfraction>) setting (for example, `0.02` = 2%) or the `SLASH_COMMAND_TOOL_CHAR_BUDGET` environment variable to a fixed character count. To free budget for other skills, set low-priority entries to `"name-only"` in [`skillOverrides`](<#override-skill-visibility-from-settings>) so they list without a description. You can also trim the `description` and `when_to_use` text at the source: put the key use case first, since each entry's combined text is capped at 1,536 characters regardless of budget. The cap is configurable with [`skillListingMaxDescChars`](<https://code.claude.com/docs/en/settings-reference#skilllistingmaxdescchars>).

### Personal skills disappeared

If skill folders you created in `~/.claude/skills/` are gone, look in `~/.claude/skills/.trash/`. When Claude Code [syncs skills from claude.ai](<#how-synced-skills-behave>), it downloads them into the separate `synced` subfolder and doesn't move or delete the folders you create.

Before v2.1.280, a file named `manifest.json` in `~/.claude/skills/` caused Claude Code to move the skill folders that file listed into a timestamped folder under `~/.claude/skills/.trash/`, and those skills stopped loading.

To restore a skill, move its folder from the timestamped folder back into `~/.claude/skills/`. Do this before the [retention sweep](<https://code.claude.com/docs/en/claude-directory#cleaned-up-automatically>) deletes trash entries, by default 30 days after they were moved to the trash.

## Related resources

* **[Debug your configuration](<https://code.claude.com/docs/en/debug-your-config>)**: diagnose why a skill isn't appearing or triggering
* **[Evaluating skill output quality](<https://agentskills.io/skill-creation/evaluating-skills>)**: the eval file format and iteration workflow on agentskills.io
* **[Skill authoring best practices](<https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices>)**: writing guidance that applies across Claude products
* **[Subagents](<https://code.claude.com/docs/en/sub-agents>)**: delegate tasks to specialized agents
* **[Plugins](<https://code.claude.com/docs/en/plugins/overview>)**: package and distribute skills with other extensions
* **[Hooks](<https://code.claude.com/docs/en/hooks>)**: automate workflows around tool events
* **[Memory](<https://code.claude.com/docs/en/memory>)**: manage CLAUDE.md files for persistent context
* **[Commands](<https://code.claude.com/docs/en/commands>)**: reference for built-in commands and bundled skills
* **[Permissions](<https://code.claude.com/docs/en/permissions>)**: control tool and skill access
* **[Claude Tag skills](<https://claude.com/docs/claude-tag/admins/skills-repo>)**: project skills committed to a repo also load when that repo is used in a Claude Tag channel


</details>
