# OpenSpec 支持的 AI 工具与调用方式

列出工具集成目录、CLI 与聊天命令调用语法、GitHub Copilot 云端代理配置，以及共享 .agents 目标和非交互初始化方式。

- 来源：OpenSpec
- 资料类型：工具兼容与集成目录
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/supported-tools.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/supported-tools.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 支持的工具

OpenSpec 可与许多 AI 编码助手配合使用。当您运行 `openspec init` 时，OpenSpec 使用活动的 profile/workflow 选择和交付模式来配置选定的工具。

## 它是如何运作的

对于每个选定的工具，OpenSpec 可以安装：

1. **技能**（如果交付包含技能）：`.../skills/openspec-*/SKILL.md`
2. **命令**（如果交付时包含命令）：工具特定的`opsx-*`命令文件

Codex 仅限技能：即使交付设置为 `commands`，OpenSpec 也会为 Codex 安装 `.agents/skills/openspec-*/SKILL.md`，并且不会生成 Codex 自定义提示文件。旧 `.codex/skills` 路径下的现有 OpenSpec 管理的技能在写入替换项后将得到协调；保留自定义和不同的文件。

默认情况下，OpenSpec使用`core`配置文件，其中包括：
- `propose`
- `explore`
- `apply`
- `update`
- `sync`
- `archive`

您可以通过`openspec config profile`启用扩展工作流程（`new`、`continue`、`ff`、`verify`、`bulk-archive`、`onboard`），然后运行`openspec update`。

## 如何调用

这些文档使用 `/opsx:propose` 作为规范名称，但每个工具都以 
 加载文件 OpenSpec 写入的方式拼写它。在下面的
[工具目录参考](<#工具目录参考>)中找到您的工具的命令路径，然后在此处匹配其形状。

|命令文件OpenSpec写入|你输入|工具|
|------------------------------|----------|-------|
|`.../commands/opsx/<id>.*` — `opsx/` 文件夹为其命名空间| `/opsx:<id>` |Claude Code、CodeBuddy、粉碎、Gemini CLI、Lingma、Qoder、ZCode|
|`.../opsx-<id>.*` — 文件名是命令| `/opsx-<id>` |除 Amazon Q 和 Devin 之外的所有其他具有生成命令文件的工具|
|`.devin/workflows/opsx-<id>.md` — 仅由 Devin 的两个 agents 之一读取|Devin 桌面上的 `/opsx-<id>`、Devin 本地上的 `/openspec-<skill>`|德文桌面\*\*\*\*|
|`.amazonq/prompts/opsx-<id>.md` — 提示，而不是命令| `@opsx-<id>` | Amazon Q Developer |
|无 — 仅技能| `/openspec-<skill>` |CodeArts、ForgeCode、Hermes、MiniMax 代码、Mistral Vibe、Zed Agent、共享 `.agents`|
|无 — Kimi 代码| `/skill:openspec-<skill>` |基米密码|
|无 — Codex CLI| `$openspec-<skill>` |Codex（[`/openspec-<skill>`无法识别](<https://github.com/openai/codex/issues/11817>)）|

因此，`/opsx:propose` 在 Cursor 中为 `/opsx-propose`，在 Amazon Q 中为 `@opsx-propose`，在 Codex 中为 
`$openspec-propose`。

有两件事是独立变化的，这就是行不会折叠的原因：

- **名称。**行1–2的区别仅在于文件命名命令的方式以及
`opsx-<id>` / `opsx:<id>` 对于生成 
 命令文件的每个工具来说，主干都是相同的。
- **包装器。** Amazon Q 将其文件加载到使用以下命令调用的提示库中
`@`。仅技能工具根本不生成命令文件，因此它们的最后三行
使用*技能*名称 - 列在
 [生成的技能名称](<#生成的技能名称>)下 - 不映射一对一的 
 到命令 ID（`/opsx:apply` 是 `openspec-apply-change` 技能）。

上面的命令路径模式有意与扩展名无关 (`.*`)：
 扩展名是工具的（`.toml` 表示 Gemini CLI，`.prompt` 表示 Continue，
`.prompt.md` 表示Kiro 和 GitHub Copilot），还有一些工具在选择器中显示名称及其扩展名 
。匹配目录形状，而不是扩展名。

文件 OpenSpec 生成，并且在设置后打印“入门”提示，
 已使用您选择的工具的正确形式 - 因此最快的答案是 
 阅读提示。

## 工具目录参考

|工具（ID）|技能路径模式|命令路径模式|
|-----------|---------------------|----------------------|
| Amazon Q Developer (`amazon-q`) | `.amazonq/skills/openspec-*/SKILL.md` | `.amazonq/prompts/opsx-<id>.md` |
|反重力 (`antigravity`)| `.agent/skills/openspec-*/SKILL.md` | `.agent/workflows/opsx-<id>.md` |
|奥吉 (`auggie`)| `.augment/skills/openspec-*/SKILL.md` | `.augment/commands/opsx-<id>.md` |
|IBM 鲍勃·壳 (`bob`)| `.bob/skills/openspec-*/SKILL.md` | `.bob/commands/opsx-<id>.md` |
| Claude Code (`claude`) | `.claude/skills/openspec-*/SKILL.md` | `.claude/commands/opsx/<id>.md` |
| Cline (`cline`) | `.cline/skills/openspec-*/SKILL.md` | `.clinerules/workflows/opsx-<id>.md` |
|命令代码（`command-code`）| `.commandcode/skills/openspec-*/SKILL.md` | `.commandcode/commands/opsx-<id>.md` |
| CodeArts (`codeartsagent`) | `.codeartsdoer/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用基于技能的 `/openspec-*` 调用）|
| CodeBuddy (`codebuddy`) | `.codebuddy/skills/openspec-*/SKILL.md` | `.codebuddy/commands/opsx/<id>.md` |
| Codex (`codex`) | `.agents/skills/openspec-*/SKILL.md` |未生成（仅限技能；使用 `$openspec-*`）|
|Devin 桌面，以前为 Windsurf (`devin`)| `.devin/skills/openspec-*/SKILL.md` | `.devin/workflows/opsx-<id>.md`\*\*\*\* |
| ForgeCode (`forgecode`) | `.forge/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用基于技能的 `/openspec-*` 调用）|
| Continue (`continue`) | `.continue/skills/openspec-*/SKILL.md` | `.continue/prompts/opsx-<id>.prompt` |
| CoStrict (`costrict`) | `.cospec/skills/openspec-*/SKILL.md` | `.cospec/openspec/commands/opsx-<id>.md` |
|粉碎 (`crush`)| `.crush/skills/openspec-*/SKILL.md` | `.crush/commands/opsx/<id>.md` |
| Cursor (`cursor`) | `.cursor/skills/openspec-*/SKILL.md` | `.cursor/commands/opsx-<id>.md` |
|工厂机器人 (`factory`)| `.factory/skills/openspec-*/SKILL.md` | `.factory/commands/opsx-<id>.md` |
| Gemini CLI (`gemini`) | `.gemini/skills/openspec-*/SKILL.md` | `.gemini/commands/opsx/<id>.toml` |
| GitHub Copilot (`github-copilot`) | `.github/skills/openspec-*/SKILL.md` | `.github/prompts/opsx-<id>.prompt.md`\*\* |
|爱马仕 Agent (`hermes`)| `.hermes/skills/openspec-*/SKILL.md`\*\*\* |未生成（无命令适配器；使用基于技能的 `/openspec-*` 调用）|
| iFlow (`iflow`) | `.iflow/skills/openspec-*/SKILL.md` | `.iflow/commands/opsx-<id>.md` |
|朱妮 (`junie`)| `.junie/skills/openspec-*/SKILL.md` | `.junie/commands/opsx-<id>.md` |
|基洛代码 (`kilocode`)| `.kilocode/skills/openspec-*/SKILL.md` | `.kilo/command/opsx-<id>.md` |
|基米代码（`kimi`）| `.kimi-code/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用基于技能的 `/skill:openspec-*` 调用）|
| Kiro (`kiro`) | `.kiro/skills/openspec-*/SKILL.md` | `.kiro/prompts/opsx-<id>.prompt.md` |
|灵玛 (`lingma`)| `.lingma/skills/openspec-*/SKILL.md` | `.lingma/commands/opsx/<id>.md` |
|MiniMax 代码 (`minimax-code`)| `~/.minimax/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用MiniMax代码技巧）|
|Mistral 氛围 (`vibe`)| `.vibe/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用基于技能的 `/openspec-*` 调用）|
|哦我的派 (`oh-my-pi`)| `.omp/skills/openspec-*/SKILL.md` | `.omp/commands/opsx-<id>.md` |
| OpenCode (`opencode`) | `.opencode/skills/openspec-*/SKILL.md` | `.opencode/commands/opsx-<id>.md` |
|圆周率 (`pi`)| `.pi/skills/openspec-*/SKILL.md` | `.pi/prompts/opsx-<id>.md` |
|SourceCraft VS Code 的代码助手 (`codeassistant`)| `.codeassistant/skills/openspec-*/SKILL.md` | `.codeassistant/commands/opsx-<id>.md` |
|Qoder (`qoder`)| `.qoder/skills/openspec-*/SKILL.md` | `.qoder/commands/opsx/<id>.md` |
| Qwen Code (`qwen`) | `.qwen/skills/openspec-*/SKILL.md` | `.qwen/commands/opsx-<id>.md` |
|[Rovo Dev CLI](<https://support.atlassian.com/rovo/docs/use-rovo-dev-cli/>) (`rovodev`)| `.rovodev/skills/openspec-*/SKILL.md` |未生成。 Rovo 没有斜线命令界面——它会自动或通过提示匹配技能（例如“使用 openspec-提议技能”）； `/skills`仅管理它们。生成的内容按名称引用技能，而不是作为 `/openspec-*` 命令。|
|[动物园代码](<https://github.com/Zoo-Code-Org/Zoo-Code>) (`roocode`)| `.roo/skills/openspec-*/SKILL.md` | `.roo/commands/opsx-<id>.md` |
|特雷（`trae`）| `.trae/skills/openspec-*/SKILL.md` | `.trae/commands/opsx-<id>.md` |
|[Zed Agent](<https://zed.dev/docs/ai/skills>) (`zed`)| `.agents/skills/openspec-*/SKILL.md` |未生成（仅限技能；使用 `/openspec-*` 或 `@openspec-*`）|
|Z代码（`zcode`）| `.zcode/skills/openspec-*/SKILL.md` | `.zcode/commands/opsx/<id>.md` |
|共享 `.agents` 技能 (`agents`)| `.agents/skills/openspec-*/SKILL.md` |未生成（无命令适配器；使用基于技能的 `/openspec-*` 调用）|

\*\* GitHub Copilot 提示文件在 IDE 扩展（VS Code、JetBrains、Visual Studio）中被识别为自定义斜杠命令。 Copilot CLI当前不直接消耗`.github/prompts/*.prompt.md`。选择 `github-copilot` 还可以设置 GitHub 托管的 ** 云编码 agent** — 参见下面的 [GitHub Copilot 云编码 agent](<#github-copilot云编码agent>)。

\*\*\*赫尔墨斯默认从`~/.hermes/skills/`加载技能。要使用项目本地OpenSpec技能，请将项目`.hermes/skills/`目录添加到`~/.hermes/config.yaml`中的`skills.external_dirs`中；然后，Hermes 通过面向用户的斜杠调用（例如 `/openspec-propose`）公开技能。

\*\*\*\* Windsurf 已于 6 月 2、2026 [ 更名为 Devin Desktop](<https://docs.devin.ai/desktop/devin-desktop-faq>)，并且其配置目录已移动：`.devin/` 是首选读写位置， `.windsurf/` 旧版只读后备。重命名后为 OpenSpec — 工具 ID 为 `devin`，并且 `--tools windsurf` 仍解析为它，以便现有的安装脚本继续工作。在 `.windsurf/` 中仍保留 OpenSpec 文件的项目将在下一个 `openspec update` 中移动；拒绝会将它们保留在原处，并且您自己编写的文件永远不会被触及。工作流程通过文件名调用，因此 `.devin/workflows/opsx-apply.md` 是 `/opsx-apply`。 [Devin Local agent 不支持工作流程 ](<https://docs.devin.ai/desktop/devin-local>) — 仅支持技能，并且根本不读取 `.windsurf/` — 因此每当 OpenSpec 写入 Devin 技能时，它都会保留其身体以及入门提示，关于 `/openspec-*` 技能调用，适用于 agents。在仅命令传递下，不会写入任何技能，并且都回退到 `/opsx-*`。

SourceCraft 代码助手支持针对其 VS Code 扩展。其[自定义命令](<https://sourcecraft.dev/portal/docs/en/code-assistant/operations/agent/slash-commands>)和[技能](<https://sourcecraft.dev/portal/docs/ru/code-assistant/operations/agent/skills>)仅在VS Code中可用。此集成不会配置 SourceCraft web 或 JetBrains。

对于仅技能交付，请要求 Code Assistant 将 `openspec-propose` 技能用于您的想法。通过请求匹配激活技能； OpenSpec 不会为此工具生成 `/openspec-*` 命令。

MiniMax 代码是全球技能集成。 OpenSpec只写入`~/.minimax/skills/`下的
`openspec-*`目录；它不会创建
repo-local `.minimax`或`.mavis`目录。仅命令交付使 
 现有全局 MiniMax 代码技能保持不变，因此一个项目的交付设置 
 无法删除另一个项目使用的技能。

### GitHub Copilot云编码agent

GitHub 的 [Copilot 编码 agent](<https://docs.github.com/en/copilot/using-github-copilot/coding-agent>) 在 GitHub 操作环境中的 GitHub 上运行 — 与编辑器中的 Copilot 分开。 OpenSpec可以通过生成两个文件来将其设置为使用OpenSpec CLI：

- `.github/workflows/copilot-setup-steps.yml` — 在 agent 环境中安装 `@fission-ai/openspec`
- `.github/agents/openspec.agent.md` — 告诉 agent 如何驾驶 OpenSpec

因为这会将 GitHub 操作工作流程写入您的存储库，所以它是 ** 选择加入 **：

|如何|行为|
|-----|----------|
|`openspec init`（互动）|询问是否设置云文件。默认为**否**。|
| `openspec init --copilot-cloud` |设置它们而不提示（对于 scripts/CI）。|
| `openspec init --no-copilot-cloud` |跳过它们而不提示，并删除任何以前生成的。|
| `openspec update` |从不提示。仅当您选择加入（或项目已有文件）时才刷新文件。如果您选择退出，它将删除 OpenSpec 管理的云文件。|

您的选择将作为 `githubCopilot.cloudAgent: true|false` 保存在 `openspec/config.yaml` 中，因此非交互式更新会遵循它。 OpenSpec 只写入或删除其生成内容的文件 - 如果您自定义 `copilot-setup-steps.yml` 或 `openspec.agent.md`，或者已经拥有自己的文件，则它不会受到影响（`init`/`update` 告诉您这一点）。

### 何时选择共享 `.agents` 目标

`agents` 是供应商中立的选项：它将技能写入 `.agents/skills/`，即许多 agent 工具读取的 
 共享根目录，而不是特定于工具的目录。

|情况|挑选|
|-----------|------|
|您的工具在上面有自己的一行|它自己的 ID — 您可以获得该工具的集成，包括它支持的斜杠命令|
|一个仓库上有几个 agents，全部阅读 `.agents/skills`|`agents` — 一个技能树，而不是每个工具一个|
|您的工具尚未列出，但显示为 `.agents/skills`| `agents` |

将其与特定工具 ID 一起选择就可以了；每个通常写入其
自己的根。 Codex 和 Zed Agent 是例外，因为它们使用相同的规范 
`.agents` 根。如果使用 Zed 或 `agents` 选择 Codex，则 OpenSpec 保留一棵以 
Codex 为主导的树。它的切换将 Codex 命名为 `$openspec-*`，将其他 agents 命名为 
`/openspec-*`，因此 `--tools all` 和现有的多 agent
 设置可以继续工作，而无需两个编写器覆盖相同的文件。
OpenSpec 也提供了它一旦项目具有 `.agents/skills/`
 目录，就会自动执行 - 一个裸露的 `.agents/` 是不够的，因为工具也使用该根来存储规则 
 和子代理定义。注意`.agents`不是`.agent`：单数
目录属于Antigravity。

有两件事需要知道：

- **仅限技能。**不存在命令适配器，因此不存在`opsx-*`命令文件
书面的；使用包含命令的交付模式 `openspec init` 在 `Commands skipped for: … (no adapter)` 下报告的工具中列出了 `agents`
。
 按技能名称调用工作流程 - 
 大多数助手读到 `.agents/skills` 拼写为`/openspec-propose`，打印
表格OpenSpec的设置提示。目标与供应商无关，因此如果它使用其他形式，请检查您的 
 助手自己的文档。
- **未创建或编辑`AGENTS.md`。** 目标是`.agents/`目录。
如果您的根 `AGENTS.md` 仍然带有较旧的 
 版本中的 OpenSpec 标记块，则 `openspec update` 会删除它们 — 请参阅 [ 迁移指南 ](<从旧版 OpenSpec 工作流迁移到 OPSX.md>)。

这里的 Zed 支持是针对内置 Zed Agent。 Zed 外部 Agents 和终端 
 线程使用自己的集成。 Agent Skills 需要 
[Zed v1.4.2](<https://github.com/zed-industries/zed/releases/tag/v1.4.2>) 或更高版本。
在您
[授予信任 ](<https://zed.dev/docs/worktree-trust>) 之前，项目本地技能在不受信任的工作树中不可用。

因为 `.agents/skills/` 由 Codex、Zed Agent 和供应商中立目标共享，
 值得了解 OpenSpec 在那里声明的内容：
 它只写入、刷新和删除`openspec-*` 您的 
 所选工作流程的技能目录，以及一个 `.openspec-target` 标记，用于记录 Codex、
Zed Agent 或供应商中立目标是否呈现该共享树。 
 目录中的任何其他内容均保留。将 `openspec-*` 名称和标记视为 OpenSpec 的 — 其中的 
 编辑将在下一个 `openspec update` 上替换，与 
 每个其他工具相同。

对于预标记项目，OpenSpec 从托管技能参考推断所有权：
`$openspec-*` 表示 Codex，`/openspec-*` 表示供应商中立目标。 
通用规范树与旧版`.codex/skills`一起被视为较旧的
双目标安装并合并到兼容的共享树中。

`openspec update` 也尊重这种所有权。如果项目拥有 `.agents` 作为 
 供应商中立目标，并且仅从杂散 
 提示文件中检测到剩余的 Codex 安装，则更新会保留已建立的 `agents` 树，而不是使用 
 重写它Codex 语法，并保留这些旧提示文件而不是 
 而不是删除它们。要将共享树交给 Codex，请显式运行“openspec init --tools
codex”。

## 非交互式设置

对于 CI/CD 或脚本化设置，请使用 `--tools`（以及可选的 `--profile`）：

```bash
# Configure specific tools
openspec init --tools claude,cursor

# Configure all supported tools
openspec init --tools all

# Skip tool configuration
openspec init --tools none

# Override profile for this init run
openspec init --profile core
```

**可用工具 ID (`--tools`)** — `windsurf` 也被接受，作为 `devin` 的别名：`amazon-q`、`antigravity`、 `auggie`、`bob`、`claude`、`cline`、`command-code`、`codeartsagent`、`codex`、`devin`、`forgecode`、 `codebuddy`、`continue`、`costrict`、`crush`、`cursor`、`factory`、`gemini`、`github-copilot`、`hermes`、 `iflow`、`junie`、`kilocode`、`kimi`、`kiro`、`lingma`、`minimax-code`、`vibe`、`oh-my-pi`、 `opencode`、`pi`、`qoder`、`qwen`、`roocode`、`codeassistant`、`trae`、`zed`、`zcode`、 `agents`

## 工作流程相关安装

OpenSpec 根据选定的工作流程安装工作流程工件：

- **核心配置文件（默认）：** `propose`、`explore`、`apply`、`update`、`sync`、`archive`
- **自定义选择：**所有工作流程 ID 的任意子集：
  `propose`, `explore`, `new`, `continue`, `apply`, `update`, `ff`, `sync`, `archive`, `bulk-archive`, `verify`, `onboard`

换句话说，skill/command 计数取决于配置文件和交付，而不是固定的。

## 生成的技能名称

当通过profile/workflow配置选择时，OpenSpec生成以下技能：

- `openspec-propose`
- `openspec-explore`
- `openspec-new-change`
- `openspec-continue-change`
- `openspec-apply-change`
- `openspec-update-change`
- `openspec-ff-change`
- `openspec-sync-specs`
- `openspec-archive-change`
- `openspec-bulk-archive-change`
- `openspec-verify-change`
- `openspec-onboard`

有关命令行为，请参阅 [ 命令 ](<OpenSpec AI 聊天命令参考.md>)；有关 `init`/`update` 选项，请参阅 [CLI](<OpenSpec 命令行参考.md>)。

## 相关

- [CLI 参考](<OpenSpec 命令行参考.md>) — 终端命令
- [命令](<OpenSpec AI 聊天命令参考.md>) — 斜线命令和技能
- [开始使用](<OpenSpec 入门与首个变更.md>) — 首次设置


## 原文对照

<details>
<summary>展开完整原文</summary>

# Supported Tools

OpenSpec works with many AI coding assistants. When you run `openspec init`, OpenSpec configures selected tools using your active profile/workflow selection and delivery mode.

## How It Works

For each selected tool, OpenSpec can install:

1. **Skills** (if delivery includes skills): `.../skills/openspec-*/SKILL.md`
2. **Commands** (if delivery includes commands): tool-specific `opsx-*` command files

Codex is skills-only: OpenSpec installs `.agents/skills/openspec-*/SKILL.md` for Codex even when delivery is set to `commands`, and it does not generate Codex custom prompt files. Existing OpenSpec-managed skills under the legacy `.codex/skills` path are reconciled after their replacements are written; custom and divergent files are preserved.

By default, OpenSpec uses the `core` profile, which includes:
- `propose`
- `explore`
- `apply`
- `update`
- `sync`
- `archive`

You can enable expanded workflows (`new`, `continue`, `ff`, `verify`, `bulk-archive`, `onboard`) via `openspec config profile`, then run `openspec update`.

## How To Invoke

These docs use `/opsx:propose` as the canonical name, but each tool spells it the
way it loads the file OpenSpec wrote. Find your tool's command path in the
[Tool Directory Reference](<#tool-directory-reference>) below, then match its shape here.

| Command file OpenSpec writes | You type | Tools |
|------------------------------|----------|-------|
| `.../commands/opsx/<id>.*` — an `opsx/` folder namespaces it | `/opsx:<id>` | Claude Code, CodeBuddy, Crush, Gemini CLI, Lingma, Qoder, ZCode |
| `.../opsx-<id>.*` — the filename is the command | `/opsx-<id>` | Every other tool with generated command files, except Amazon Q and Devin |
| `.devin/workflows/opsx-<id>.md` — read by only one of Devin's two agents | `/opsx-<id>` on Devin Desktop, `/openspec-<skill>` on Devin Local | Devin Desktop\*\*\*\* |
| `.amazonq/prompts/opsx-<id>.md` — a prompt, not a command | `@opsx-<id>` | Amazon Q Developer |
| none — skills only | `/openspec-<skill>` | CodeArts, ForgeCode, Hermes, MiniMax Code, Mistral Vibe, Zed Agent, shared `.agents` |
| none — Kimi Code | `/skill:openspec-<skill>` | Kimi Code |
| none — Codex CLI | `$openspec-<skill>` | Codex ([`/openspec-<skill>` is not recognized](<https://github.com/openai/codex/issues/11817>)) |

So `/opsx:propose` is `/opsx-propose` in Cursor, `@opsx-propose` in Amazon Q, and
`$openspec-propose` in Codex.

Two things vary independently, which is why the rows do not collapse:

- **The name.** Rows 1–2 differ only in how the file names the command, and the
  `opsx-<id>` / `opsx:<id>` stem is the same for every tool with generated
  command files.
- **The wrapper.** Amazon Q loads its files into a prompt library invoked with
  `@`. Skills-only tools generate no command files at all, so their last three
  rows use *skill* names — listed under
  [Generated Skill Names](<#generated-skill-names>) — which do not map one-to-one
  onto command ids (`/opsx:apply` is the `openspec-apply-change` skill).

The command path patterns above are extension-neutral (`.*`) on purpose: the
extension is the tool's (`.toml` for Gemini CLI, `.prompt` for Continue,
`.prompt.md` for Kiro and GitHub Copilot), and a few tools show the name with
its extension in the picker. Match the directory shape, not the extension.

The files OpenSpec generates, and the "Getting started" hint printed after setup,
already use the right form for the tools you selected — so the fastest answer is
to read the hint.

## Tool Directory Reference

| Tool (ID) | Skills path pattern | Command path pattern |
|-----------|---------------------|----------------------|
| Amazon Q Developer (`amazon-q`) | `.amazonq/skills/openspec-*/SKILL.md` | `.amazonq/prompts/opsx-<id>.md` |
| Antigravity (`antigravity`) | `.agent/skills/openspec-*/SKILL.md` | `.agent/workflows/opsx-<id>.md` |
| Auggie (`auggie`) | `.augment/skills/openspec-*/SKILL.md` | `.augment/commands/opsx-<id>.md` |
| IBM Bob Shell (`bob`) | `.bob/skills/openspec-*/SKILL.md` | `.bob/commands/opsx-<id>.md` |
| Claude Code (`claude`) | `.claude/skills/openspec-*/SKILL.md` | `.claude/commands/opsx/<id>.md` |
| Cline (`cline`) | `.cline/skills/openspec-*/SKILL.md` | `.clinerules/workflows/opsx-<id>.md` |
| Command Code (`command-code`) | `.commandcode/skills/openspec-*/SKILL.md` | `.commandcode/commands/opsx-<id>.md` |
| CodeArts (`codeartsagent`) | `.codeartsdoer/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use skill-based `/openspec-*` invocations) |
| CodeBuddy (`codebuddy`) | `.codebuddy/skills/openspec-*/SKILL.md` | `.codebuddy/commands/opsx/<id>.md` |
| Codex (`codex`) | `.agents/skills/openspec-*/SKILL.md` | Not generated (skills-only; use `$openspec-*`) |
| Devin Desktop, formerly Windsurf (`devin`) | `.devin/skills/openspec-*/SKILL.md` | `.devin/workflows/opsx-<id>.md`\*\*\*\* |
| ForgeCode (`forgecode`) | `.forge/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use skill-based `/openspec-*` invocations) |
| Continue (`continue`) | `.continue/skills/openspec-*/SKILL.md` | `.continue/prompts/opsx-<id>.prompt` |
| CoStrict (`costrict`) | `.cospec/skills/openspec-*/SKILL.md` | `.cospec/openspec/commands/opsx-<id>.md` |
| Crush (`crush`) | `.crush/skills/openspec-*/SKILL.md` | `.crush/commands/opsx/<id>.md` |
| Cursor (`cursor`) | `.cursor/skills/openspec-*/SKILL.md` | `.cursor/commands/opsx-<id>.md` |
| Factory Droid (`factory`) | `.factory/skills/openspec-*/SKILL.md` | `.factory/commands/opsx-<id>.md` |
| Gemini CLI (`gemini`) | `.gemini/skills/openspec-*/SKILL.md` | `.gemini/commands/opsx/<id>.toml` |
| GitHub Copilot (`github-copilot`) | `.github/skills/openspec-*/SKILL.md` | `.github/prompts/opsx-<id>.prompt.md`\*\* |
| Hermes Agent (`hermes`) | `.hermes/skills/openspec-*/SKILL.md`\*\*\* | Not generated (no command adapter; use skill-based `/openspec-*` invocations) |
| iFlow (`iflow`) | `.iflow/skills/openspec-*/SKILL.md` | `.iflow/commands/opsx-<id>.md` |
| Junie (`junie`) | `.junie/skills/openspec-*/SKILL.md` | `.junie/commands/opsx-<id>.md` |
| Kilo Code (`kilocode`) | `.kilocode/skills/openspec-*/SKILL.md` | `.kilo/command/opsx-<id>.md` |
| Kimi Code (`kimi`) | `.kimi-code/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use skill-based `/skill:openspec-*` invocations) |
| Kiro (`kiro`) | `.kiro/skills/openspec-*/SKILL.md` | `.kiro/prompts/opsx-<id>.prompt.md` |
| Lingma (`lingma`) | `.lingma/skills/openspec-*/SKILL.md` | `.lingma/commands/opsx/<id>.md` |
| MiniMax Code (`minimax-code`) | `~/.minimax/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use MiniMax Code skills) |
| Mistral Vibe (`vibe`) | `.vibe/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use skill-based `/openspec-*` invocations) |
| Oh My Pi (`oh-my-pi`) | `.omp/skills/openspec-*/SKILL.md` | `.omp/commands/opsx-<id>.md` |
| OpenCode (`opencode`) | `.opencode/skills/openspec-*/SKILL.md` | `.opencode/commands/opsx-<id>.md` |
| Pi (`pi`) | `.pi/skills/openspec-*/SKILL.md` | `.pi/prompts/opsx-<id>.md` |
| SourceCraft Code Assistant for VS Code (`codeassistant`) | `.codeassistant/skills/openspec-*/SKILL.md` | `.codeassistant/commands/opsx-<id>.md` |
| Qoder (`qoder`) | `.qoder/skills/openspec-*/SKILL.md` | `.qoder/commands/opsx/<id>.md` |
| Qwen Code (`qwen`) | `.qwen/skills/openspec-*/SKILL.md` | `.qwen/commands/opsx-<id>.md` |
| [Rovo Dev CLI](<https://support.atlassian.com/rovo/docs/use-rovo-dev-cli/>) (`rovodev`) | `.rovodev/skills/openspec-*/SKILL.md` | Not generated. Rovo has no slash-command surface — it matches skills automatically or by prompt (e.g. "use the openspec-propose skill"); `/skills` only manages them. Generated content references skills by name, never as `/openspec-*` commands. |
| [Zoo Code](<https://github.com/Zoo-Code-Org/Zoo-Code>) (`roocode`) | `.roo/skills/openspec-*/SKILL.md` | `.roo/commands/opsx-<id>.md` |
| Trae (`trae`) | `.trae/skills/openspec-*/SKILL.md` | `.trae/commands/opsx-<id>.md` |
| [Zed Agent](<https://zed.dev/docs/ai/skills>) (`zed`) | `.agents/skills/openspec-*/SKILL.md` | Not generated (skills-only; use `/openspec-*` or `@openspec-*`) |
| ZCode (`zcode`) | `.zcode/skills/openspec-*/SKILL.md` | `.zcode/commands/opsx/<id>.md` |
| Shared `.agents` skills (`agents`) | `.agents/skills/openspec-*/SKILL.md` | Not generated (no command adapter; use skill-based `/openspec-*` invocations) |

\*\* GitHub Copilot prompt files are recognized as custom slash commands in IDE extensions (VS Code, JetBrains, Visual Studio). Copilot CLI does not currently consume `.github/prompts/*.prompt.md` directly. Selecting `github-copilot` can also set up the GitHub-hosted **cloud coding agent** — see [GitHub Copilot cloud coding agent](<#github-copilot-cloud-coding-agent>) below.

\*\*\* Hermes loads skills from `~/.hermes/skills/` by default. To use project-local OpenSpec skills, add the project `.hermes/skills/` directory to `skills.external_dirs` in `~/.hermes/config.yaml`; Hermes then exposes skills with user-facing slash invocations such as `/openspec-propose`.

\*\*\*\* Windsurf was [rebranded to Devin Desktop](<https://docs.devin.ai/desktop/devin-desktop-faq>) on June 2, 2026, and its config directory moved: `.devin/` is the preferred read + write location, `.windsurf/` a legacy read-only fallback. OpenSpec follows the rename — the tool id is `devin`, and `--tools windsurf` still resolves to it so existing setup scripts keep working. A project still holding OpenSpec files in `.windsurf/` is offered the move on the next `openspec update`; declining leaves them in place, and files you wrote yourself are never touched. Workflows are invoked by filename, so `.devin/workflows/opsx-apply.md` is `/opsx-apply`. The [Devin Local agent does not support workflows](<https://docs.devin.ai/desktop/devin-local>) — only skills, and it does not read `.windsurf/` at all — so whenever OpenSpec writes Devin skills it keeps their bodies, and the getting-started hint, on `/openspec-*` skill invocations, which work on both agents. Under commands-only delivery no skills are written and both fall back to `/opsx-*`.

SourceCraft Code Assistant support targets its VS Code extension. Its [custom commands](<https://sourcecraft.dev/portal/docs/en/code-assistant/operations/agent/slash-commands>) and [skills](<https://sourcecraft.dev/portal/docs/ru/code-assistant/operations/agent/skills>) are available only in VS Code. This integration does not configure SourceCraft web or JetBrains.

With skills-only delivery, ask Code Assistant to use the `openspec-propose` skill with your idea. Skills activate through request matching; OpenSpec does not generate `/openspec-*` commands for this tool.

MiniMax Code is a global skills-only integration. OpenSpec writes only its
`openspec-*` directories under `~/.minimax/skills/`; it does not create
repo-local `.minimax` or `.mavis` directories. Commands-only delivery leaves
existing global MiniMax Code skills untouched so one project's delivery setting
cannot remove skills used by another project.

### GitHub Copilot cloud coding agent

GitHub's [Copilot coding agent](<https://docs.github.com/en/copilot/using-github-copilot/coding-agent>) runs on GitHub in a GitHub Actions environment — separate from Copilot in your editor. OpenSpec can set it up to use the OpenSpec CLI by generating two files:

- `.github/workflows/copilot-setup-steps.yml` — installs `@fission-ai/openspec` in the agent's environment
- `.github/agents/openspec.agent.md` — tells the agent how to drive OpenSpec

Because this writes a GitHub Actions workflow into your repository, it is **opt-in**:

| How | Behavior |
|-----|----------|
| `openspec init` (interactive) | Asks whether to set up cloud files. Default is **No**. |
| `openspec init --copilot-cloud` | Sets them up without prompting (for scripts/CI). |
| `openspec init --no-copilot-cloud` | Skips them without prompting, and removes any previously generated ones. |
| `openspec update` | Never prompts. Refreshes the files only if you opted in (or the project already has them). If you opted out, it removes OpenSpec-managed cloud files. |

Your choice is saved in `openspec/config.yaml` as `githubCopilot.cloudAgent: true|false`, so non-interactive updates honor it. OpenSpec only ever writes or removes files whose content it generated — if you customize `copilot-setup-steps.yml` or `openspec.agent.md`, or already have your own, it is left untouched (and `init`/`update` tell you so).

### When to pick the shared `.agents` target

`agents` is the vendor-neutral option: it writes skills to `.agents/skills/`, the
shared root many agent tools read, instead of a tool-specific directory.

| Situation | Pick |
|-----------|------|
| Your tool has its own row above | Its own ID — you get that tool's integration, including slash commands where it supports them |
| Several agents on one repo, all reading `.agents/skills` | `agents` — one skill tree instead of one per tool |
| Your tool isn't listed yet but reads `.agents/skills` | `agents` |

Selecting it alongside a tool-specific ID is fine; each normally writes to its
own root. Codex and Zed Agent are the exceptions because they use the same canonical
`.agents` root. If Codex is selected with Zed or `agents`, OpenSpec keeps one
Codex-led tree. Its handoffs name both `$openspec-*` for Codex and
`/openspec-*` for other agents, so `--tools all` and existing multi-agent
setups keep working without two writers overwriting the same files.
OpenSpec also offers it automatically once a project has a `.agents/skills/`
directory — a bare `.agents/` is not enough, since tools use that root for rules
and subagent definitions too. Note `.agents` is not `.agent`: the singular
directory belongs to Antigravity.

Two things to know:

- **Skills only.** No command adapter exists, so no `opsx-*` command files are
  written; with a commands-inclusive delivery mode `openspec init` lists `agents`
  among the tools it reports under `Commands skipped for: … (no adapter)`.
  Invoke the workflows by skill name —
  most assistants that read `.agents/skills` spell that `/openspec-propose`, the form
  OpenSpec's setup hint prints. The target is vendor-neutral, so check your
  assistant's own docs if it uses another form.
- **No `AGENTS.md` is created or edited.** The target is the `.agents/` directory.
  If your root `AGENTS.md` still carries OpenSpec marker blocks from an older
  version, `openspec update` strips them — see the [Migration Guide](<从旧版 OpenSpec 工作流迁移到 OPSX.md>).

Zed support here is for the built-in Zed Agent. Zed External Agents and Terminal
Threads use their own integrations. Agent Skills require
[Zed v1.4.2](<https://github.com/zed-industries/zed/releases/tag/v1.4.2>) or newer.
Project-local skills are unavailable in an untrusted worktree until you
[grant trust](<https://zed.dev/docs/worktree-trust>).

Because `.agents/skills/` is shared by Codex, Zed Agent, and the vendor-neutral target,
it is worth knowing what OpenSpec claims there:
it writes, refreshes, and removes only the `openspec-*` skill directories for your
selected workflows, plus an `.openspec-target` marker that records whether Codex,
Zed Agent, or the vendor-neutral target rendered that shared tree. Anything else in that
directory is left alone. Treat the `openspec-*` names and marker as OpenSpec's —
edits inside them are replaced on the next `openspec update`, the same as for
every other tool.

For pre-marker projects, OpenSpec infers ownership from managed skill references:
`$openspec-*` means Codex and `/openspec-*` means the vendor-neutral target. A
generic canonical tree alongside legacy `.codex/skills` is treated as an older
dual-target install and consolidated into the compatible shared tree.

`openspec update` honors this ownership too. If a project owns `.agents` as the
vendor-neutral target and a leftover Codex install is detected only from stray
prompt files, the update leaves the established `agents` tree in place instead of
rewriting it with Codex syntax, and preserves those legacy prompt files rather
than deleting them. To hand the shared tree to Codex, run `openspec init --tools
codex` explicitly.

## Non-Interactive Setup

For CI/CD or scripted setup, use `--tools` (and optionally `--profile`):

```bash
# Configure specific tools
openspec init --tools claude,cursor

# Configure all supported tools
openspec init --tools all

# Skip tool configuration
openspec init --tools none

# Override profile for this init run
openspec init --profile core
```

**Available tool IDs (`--tools`)** — `windsurf` is also accepted, as an alias for `devin`: `amazon-q`, `antigravity`, `auggie`, `bob`, `claude`, `cline`, `command-code`, `codeartsagent`, `codex`, `devin`, `forgecode`, `codebuddy`, `continue`, `costrict`, `crush`, `cursor`, `factory`, `gemini`, `github-copilot`, `hermes`, `iflow`, `junie`, `kilocode`, `kimi`, `kiro`, `lingma`, `minimax-code`, `vibe`, `oh-my-pi`, `opencode`, `pi`, `qoder`, `qwen`, `roocode`, `codeassistant`, `trae`, `zed`, `zcode`, `agents`

## Workflow-Dependent Installation

OpenSpec installs workflow artifacts based on selected workflows:

- **Core profile (default):** `propose`, `explore`, `apply`, `update`, `sync`, `archive`
- **Custom selection:** any subset of all workflow IDs:
  `propose`, `explore`, `new`, `continue`, `apply`, `update`, `ff`, `sync`, `archive`, `bulk-archive`, `verify`, `onboard`

In other words, skill/command counts are profile-dependent and delivery-dependent, not fixed.

## Generated Skill Names

When selected by profile/workflow config, OpenSpec generates these skills:

- `openspec-propose`
- `openspec-explore`
- `openspec-new-change`
- `openspec-continue-change`
- `openspec-apply-change`
- `openspec-update-change`
- `openspec-ff-change`
- `openspec-sync-specs`
- `openspec-archive-change`
- `openspec-bulk-archive-change`
- `openspec-verify-change`
- `openspec-onboard`

See [Commands](<OpenSpec AI 聊天命令参考.md>) for command behavior and [CLI](<OpenSpec 命令行参考.md>) for `init`/`update` options.

## Related

- [CLI Reference](<OpenSpec 命令行参考.md>) — Terminal commands
- [Commands](<OpenSpec AI 聊天命令参考.md>) — Slash commands and skills
- [Getting Started](<OpenSpec 入门与首个变更.md>) — First-time setup


</details>
