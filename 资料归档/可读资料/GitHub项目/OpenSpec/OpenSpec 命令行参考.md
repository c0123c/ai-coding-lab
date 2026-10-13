# OpenSpec 命令行参考

列出初始化、更新、Store 管理、变更检查与验证等终端命令，区分人工使用与代理兼容命令，并给出选项和输出示例。

- 来源：OpenSpec
- 资料类型：CLI 命令参考
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/cli.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/cli.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# CLI 参考

OpenSpec CLI (`openspec`) 提供用于项目设置、验证、状态检查和管理的终端命令。这些命令补充了 [ 命令 ](<OpenSpec AI 聊天命令参考.md>) 中记录的 AI 斜线命令（如 `/opsx:propose`）。

## 总结

|类别|命令|目的|
|----------|----------|---------|
|**设置**| `init`, `update` |Initialize and update OpenSpec in your project|
|**Stores (standalone OpenSpec repos)**| `store setup`, `store register`, `store unregister`, `store remove`, `store list`, `store doctor` |管理商店 - 您注册的独立 OpenSpec 存储库|
|**健康**| `doctor` |报告已解析根的关系运行状况|
|**Working context**| `context` |组装工作集（根+引用存储）|
|**Personal worksets**| `workset create`, `workset list`, `workset open`, `workset remove` |Keep and open personal, local working views in your tool|
|**正在浏览**| `list`, `view`, `show` |Explore changes and specs|
|**验证**| `validate` |Check changes and specs for issues|
|**Lifecycle**| `archive` |完成已完成的更改|
|**工作流程**| `new change`, `status`, `instructions`, `templates`, `schemas` |工件驱动的工作流程支持|
|**架构**| `schema init`, `schema fork`, `schema validate`, `schema which` |创建和管理自定义工作流程|
|**Config**| `config` |查看和修改设置|
|**Utility**| `feedback`, `completion` |反馈和 shell 集成|

---

## Human vs Agent Commands

Most CLI commands are designed for **human use** in a terminal. Some commands also support **agent/script use** via JSON output.

### 仅限人类的命令

These commands are interactive and designed for terminal use:

|命令|目的|
|---------|---------|
| `openspec init` |初始化项目（交互式提示）|
| `openspec view` |交互式仪表板|
| `openspec workset open <name>` |打开保存的工作集（编辑器窗口或终端 agent 会话）|
| `openspec config edit` |在编辑器中打开配置|
| `openspec feedback` |通过【GitHub】提交反馈|
| `openspec completion install` |Install shell completions|

### Agent-兼容命令

These commands support `--json` output for programmatic use by AI agents and scripts:

|命令|人类使用|Agent 使用|
|---------|-----------|-----------|
| `openspec list` |浏览changes/specs|`--json` 用于结构化数据|
| `openspec show <item>` |阅读内容|`--json` 用于解析|
| `openspec validate` |检查问题|`--all --json` for bulk validation|
| `openspec status` |查看工件进度|`--json` for structured status|
| `openspec instructions` |获取后续步骤|`--json` for agent instructions|
| `openspec templates` |查找模板路径|`--json` for path resolution|
| `openspec schemas` |列出可用模式|`--json` for schema discovery; `--store <id>` to select a registered root|
| `openspec store setup <id>` |Create and register a local store|`--json` with explicit inputs for structured setup output|
| `openspec store register <path>` |Register an existing store|`--json` 用于结构化注册输出|
| `openspec store unregister <id>` |Forget a local store registration|`--json` for structured cleanup output|
| `openspec store remove <id>` |删除已注册的本地存储文件夹|`--yes --json` 用于非交互式删除|
| `openspec store list` |浏览注册商店|`--json` for structured registrations|
| `openspec store doctor` |检查本地商店设置|`--json` for structured diagnostics|
| `openspec new change <id>` |Create repo-local change scaffolding|`--json`, plus `--store <id>` to use a registered store as the OpenSpec root|
| `openspec workset create [name]` |Compose a personal working view|`--member <path> --json` for non-interactive composition|
| `openspec workset list` |浏览保存的工作集|`--json` 用于结构化视图|
| `openspec workset remove <name>` |删除已保存的视图|`--yes --json` 用于非交互式删除|

---

## 全局选项

These options work with all commands:

|选项|描述|
|--------|-------------|
| `--version`, `-V` |显示版本号|
| `--no-color` |禁用颜色输出|
| `--help`, `-h` |显示命令的帮助|

---

## 设置命令

### `openspec init`

Initialize OpenSpec in your project.创建文件夹结构并配置 AI 工具集成。

默认行为使用全局配置默认值：配置文件 `core`、交付 `both`、工作流程 `propose, explore, apply, update, sync, archive`。

```
openspec init [path] [options]
```

使用`--language <language>`将语言指令添加到新项目的
`openspec/config.yaml`中。对于现有项目，编辑配置的 `context`
 字段，以便 OpenSpec 永远不会覆盖特定于项目的指导。

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `path` |否|Target directory (default: current directory)|

**选项：**

|选项|描述|
|--------|-------------|
| `--tools <list>` |Configure AI tools non-interactively.使用 `all`、`none` 或逗号分隔列表|
| `--language <language>` |Write artifacts in this language when creating a new config|
| `--force` |Auto-cleanup legacy files without prompting|
| `--profile <profile>` |覆盖此 init 运行的全局配置文件（`core` 或 `custom`）|
| `--no-animation` |显示静态欢迎屏幕而不是动画欢迎屏幕|
| `--copilot-cloud` |无提示设置GitHub Copilot[云编码-agent文件](<OpenSpec 支持的 AI 工具与调用方式.md#github-copilot云编码agent>)|
| `--no-copilot-cloud` |跳过GitHub Copilot云编码-agent文件而不提示|

`--profile custom` 使用当前在全局配置 (`openspec config profile`) 中选择的任何工作流程。

当设置 `OPENSPEC_NO_ANIMATION` 环境变量（任何值，包括空）、`NO_COLOR` 设置为非空值或启用 OS 减少运动首选项（macOS 减少运动，禁用 GNOME 动画）时，也会跳过欢迎动画。

**支持的工具 ID (`--tools`)** — `windsurf` 也被接受，作为 `devin` 的别名：`amazon-q`、`antigravity`、 `auggie`、`bob`、`claude`、`cline`、`command-code`、`codeartsagent`、`codex`、`devin`、`forgecode`、 `codebuddy`、`continue`、`costrict`、`crush`、`cursor`、`factory`、`gemini`、`github-copilot`、`hermes`、 `iflow`、`junie`、`kilocode`、`kimi`、`kiro`、`lingma`、`minimax-code`、`vibe`、`oh-my-pi`、 `opencode`、`pi`、`codeassistant`、`qoder`、`qwen`、`rovodev`、`roocode`、`trae`、`zed`、 `zcode`, `agents`

> 此列表镜像`src/core/config.ts`中的`AI_TOOLS`。有关每个工具的技能和命令路径，请参阅[支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)。

**示例：**

```bash
# Interactive initialization
openspec init

# Initialize in a specific directory
openspec init ./my-project

# Non-interactive: configure for Claude and Cursor
openspec init --tools claude,cursor

# Non-interactive: configure global MiniMax Code skills
openspec init --tools minimax-code

# Configure for all supported tools
openspec init --tools all

# Override profile for this run
openspec init --profile core

# Skip prompts and auto-cleanup legacy files
openspec init --force
```

**它创造了什么：**

```
openspec/
├── specs/              # Your specifications (source of truth)
├── changes/            # Proposed changes
└── config.yaml         # Project configuration

.claude/skills/         # Claude Code skills (if claude selected)
.cursor/skills/         # Cursor skills (if cursor selected)
.cursor/commands/       # Cursor OPSX commands (if delivery includes commands)
.agents/skills/         # Shared skills for AGENTS.md-compatible tools (if agents selected)
... (other tool configs)
```

---

### `openspec update`

升级CLI后更新OpenSpec指令文件。使用您当前的全局配置文件、选定的工作流程和交付模式重新生成 AI 工具配置文件。

```
openspec update [path] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `path` |否|Target directory (default: current directory)|

**选项：**

|选项|描述|
|--------|-------------|
| `--force` |Force update even when files are up to date|

**示例：**

```bash
# Update instruction files after npm upgrade
npm install -g @fission-ai/openspec@latest
openspec update
```

Upgrade the package first.指令文件是由已安装的 CLI 生成的，因此针对过时的安装运行 `openspec update` 会报告最新的所有内容，而无需添加更新版本的工作流程。

为了使其可见，`openspec update` 询问 npm 注册表是否已发布更新的 CLI。 When yours is behind, it offers to upgrade:

```text
A newer OpenSpec CLI is available (v1.6.0 → v1.7.0).
  Running from: /usr/local/lib/node_modules/@fission-ai/openspec
? Upgrade to v1.7.0 now? (Y/n)
```

回答“是”，它会运行 `npm install -g @fission-ai/openspec@latest`，然后使用新的 CLI 重新运行更新，以便新的工作流程落在同一命令中。它通过询问已安装的二进制文件的版本而不是信任 npm 的退出代码来确认升级，因此，如果您的 `PATH` 上之前的另一个安装仍在回答，它会告诉您而不是声称成功。 Answer no and it prints the command and updates with the CLI you have. Ctrl-C 停止命令。

该优惠仅出现在交互式终端中，并且仅当 npm 拥有安装时才会出现 - 这是 `npm install -g` 实际修复的一种情况。其他所有内容都会获取与其安装方式相匹配的命令：

|OpenSpec如何安装|你得到什么|
|---------------------------|--------------|
|全局 npm 安装|提示和升级在交互式终端中运行；管道输出获取打印的命令|
|全局 pnpm、bun、yarn 或 volta 安装|That manager's own command: `pnpm add -g …@latest`, `bun add -g …@latest`, `yarn global add …@latest`, or `volta install …@latest`|
|项目的依赖|更新依赖项的注释，因为它的包管理器拥有锁定文件|
|`npx` / `dlx` 缓存|`npx @fission-ai/openspec@latest update` — 该命令是更新，因此没有第二步|
|一个 git 克隆|Nothing — your version is whatever the branch says|

每当打印任何内容时，它都会命名正在运行的 CLI 加载的目录 - 当您升级但过时的填充程序仍然拥有您的 `PATH` 时要检查的内容。

It asks the registry in `npm_config_registry` when npm exports it, and `https://registry.npmjs.org` otherwise.不读取 `.npmrc`：让文件内容选择出站请求的去向是值得避免的流程，并且项目的 `.npmrc` 与存储库一起传输。在私有镜像上，导出 `npm_config_registry` — 或设置 `OPENSPEC_NO_UPDATE_CHECK` 以完全跳过检查。当 `CI` 设置为除显式偏离值（`false`、`0`、`no`、`off` 或空）之外的任何值时、在 `NODE_ENV=test` 下以及每当 `OPENSPEC_NO_UPDATE_CHECK`（任何值）、`DO_NOT_TRACK=1` 或 `OPENSPEC_TELEMETRY=0` 被设置。它在更新之前运行，最多可以延迟 1.5 秒 - 即使网络静默丢弃数据包，它也会放弃，并且在注册表无法访问时保持安静。

**如何决定“最新”：**技能文件记录生成
它们的版本，因此OpenSpec将其与已安装的CLI进行比较。命令文件不带有
版本标记，因此对于具有命令但没有技能的工具（交付
`commands`），OpenSpec会将文件内容与其生成的内容进行比较
现在 - 对这些文件的编辑视为漂移并被覆盖。交付
`skills`或`both`时，仅检查录制的版本，因此版本仍然匹配的手工编辑文件
将被保留；使用`--force`重写它。 Either
way, generated files are OpenSpec's to own — keep your own instructions
elsewhere.

---

## 商店（独立 OpenSpec 存储库）

> **Beta。** 存储和基于它们构建的功能（引用、工作上下文、工作集）是新的； command names, flags, file formats, and JSON output may change shape between releases. For the problem-first walkthrough, see the [stores guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>).

A store is a standalone OpenSpec repo you've registered on this machine — for example a planning repo or a contracts repo.注册存储允许普通命令（`list`、`show`、`status`、`validate`、`new change`、`archive`，...）通过传递`--store <id>`从任何地方对其进行操作。

### `openspec store setup`

创建并注册本地商店。 With no arguments in a terminal,
OpenSpec guides the user through setup. Agents and scripts should pass explicit
inputs and use `--json`.

```bash
openspec store setup [id] [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--path <path>` |Folder where the store should live (for example `~/openspec/<id>`)|
| `--remote <url>` |在新店的`store.yaml`中记录规范遥控器|
| `--init-git` |使用初始提交初始化 Git 存储库（默认）|
| `--no-init-git` |Skip every Git action: no init, no initial commit|
| `--json` |输出JSON|

非交互式运行（`--json`、脚本、agents）必须同时传递商店 ID 和 `--path`。在交互式终端中，安装程序会在用户拥有的可见位置（例如 `~/openspec/<id>`）中提示输入位置并提供可编辑建议；它永远不会默认为 OpenSpec 的托管数据目录。

示例：

```bash
openspec store setup
openspec store setup team-context
openspec store setup team-context --path ~/openspec/team-context --no-init-git
openspec store setup team-context --path ~/openspec/team-context --no-init-git --json
```

### `openspec store register`

注册现有的本地存储文件夹。在商店测试期间，根可能是
在存在任何更改、应用规范或更改之前注册
已存档；在那种情况下 `openspec/changes/`, `openspec/specs/`, 和
`openspec/changes/archive/` 可能会不存在，直到正常命令创建它们为止。
一个仅配置的存储库，声明 `store: <id>` 仍然是指向另一个的指针
存储并且不会注册为存储根，除非该指针被删除。

```bash
openspec store register [path] [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--id <id>` |商店 ID；默认存储元数据或文件夹名称|
| `--yes` |确认为健康的 OpenSpec 根创建存储身份元数据|
| `--json` |输出JSON|

### `openspec store unregister`

忘记本地存储注册而不删除文件。

```bash
openspec store unregister <id> [--json]
```

当商店被移动、克隆到其他地方或不应再存在时使用此选项
显示者 OpenSpec 在这台机器上。

### `openspec store remove`

忘记本地商店注册并删除其本地文件夹。

```bash
openspec store remove <id> [--yes] [--json]
```

`remove` 在交互式终端中显示删除之前的确切文件夹。
Agents、脚本和 JSON 调用者必须通过 `--yes` 才能确认删除。
OpenSpec 拒绝删除不包含匹配的 
 存储元数据的文件夹。

### `openspec store list`

列出本地注册的商店。

```bash
openspec store list [--json]
openspec store ls [--json]
```

### `openspec store doctor`

检查本地存储注册、元数据和 Git 状态。

```bash
openspec store doctor [id] [--json]
```

医生仅负责诊断；它报告丢失的根、元数据不匹配和无效的本地注册表状态，而无需修改存储。

### 从项目中引用商店

项目存储库可以声明其工作在 `openspec/config.yaml` 中使用哪个存储：

```yaml
schema: spec-driven
references:
  - team-context
```

从那时起，该存储库中的 `openspec instructions` 输出（每个工件和 `apply` 表面、JSON 和人类模式）携带每个引用商店的规格的索引 - 规格 ID、每个规格的用途部分的单行摘要以及获取命令 (`openspec show <spec-id> --type spec --store <id>`)。该索引是根据每次运行的注册结帐实时构建的；规范内容永远不会复制到输出中。

引用是只读上下文。它们永远不会改变命令的作用位置：工作保留在存储库自己的根中，并且写入引用的存储仍然是显式的 `--store` 操作。无法解析的引用（例如，未在此计算机上注册的存储）会在具有确切修复的索引中降级为警告，并且仍会生成指令。 `openspec doctor` 在一处报告参考运行状况。

### 记录从何处克隆商店

商店可以在其提交的身份文件中记录其规范克隆源，因此入职永远不会在“注册商店”时陷入死胡同：

```bash
openspec store setup team-context --path ~/openspec/team-context \
  --remote git@github.com:acme/team-context.git
```

远程登陆在初始提交内的 `.openspec-store/store.yaml` 中，因此每个克隆生来就知道它。对于现有商店，手动编辑 `store.yaml` 并提交。 `store doctor` 显示记录的远程（以及结帐观察到的 Git 来源）； setup/register共享指南命名它；并注册在机器本地注册表中记录结帐的来源。

参考声明也可以携带克隆源，因此还没有商店的队友可以获得完整的、可粘贴的修复（`git clone <remote> <path> && openspec store register <path> --id <id>`）：

```yaml
references:
  - { id: team-context, remote: "git@github.com:acme/team-context.git" }
```

录制远程不同步：OpenSpec 永远不会自行克隆、拉取或推送。

### 声明默认商店

计划完全外部化的存储库（没有本地 `openspec/specs/` 或 `openspec/changes/`）可以声明其存储一次，而不是在每个命令上传递 `--store`：

```yaml
# openspec/config.yaml (the only file under openspec/)
store: team-context
```

然后，普通命令会自动解析为声明的存储；根横幅和 JSON `root` 阻止报告 `source: "declared"` 和商店 id，并且打印的提示仍然带有 `--store <id>`。该声明是后备，而不是覆盖：显式 `--store` 总是获胜，并且具有实际计划文件夹的目录会忽略指针（带有警告）。要将指针存储库转换为本地 OpenSpec 根，请删除 `store:` 行并运行 `openspec init` — 当声明存在时，init 拒绝搭建脚手架。

机器级变体同时涵盖每个存储库：`openspec config set defaultStore <id>`（请参阅配置）。仅在`--store`、本地根和项目指针都解析失败后才查阅；根横幅和 JSON `root` 块然后报告 `source: "global_default"`。

## 医生（关系健康）

一个只读问题，一个地方：OpenSpec 根是否健康，它引用的存储在这台机器上是否可用？

```bash
openspec doctor [--store <id>] [--json]
```

该报告将根运行状况、商店元数据运行状况（包括记录的远程和结账来源偏离时的注释，以及商店结账偏离其上次获取的上游跟踪引用时的注释）和引用运行状况（显示相同的诊断说明，并针对未解析的引用进行克隆修复）分开。任何严重程度的健康检查结果退出 0 — agents 读取 `status` 数组；仅命令失败（无 root、未知存储）退出 1。 Doctor 从不克隆、同步或修复。要获取组装好的套装本身而不是其生命值，请使用 `openspec context`。

## 工作环境（组装集）

这项工作通过 OpenSpec 声明在一个工作集中涉及到的所有内容：OpenSpec 根及其引用的存储。

```bash
openspec context [--store <id>] [--json] [--code-workspace <path> [--force]]
```

JSON简介是agent消耗品（每个可用的参考商店都带有其获取配方；未解决的成员带有相同的修复说明和医生显示）。 `--code-workspace` 另外写入一个 VS Code 工作区文件，其中包含根目录以及可用的引用存储（`ref:<id>` 文件夹）——此命令执行的写入操作，如果文件存在，则在没有 `--force` 的情况下拒绝。不可用的成员会被报告，而不会被猜测。

“工作上下文”是组装好的集合； `openspec/config.yaml` 中的 `context:` 字段是注入到指令中的项目背景 - 两个不同的东西。 `openspec doctor`回答该集合是否健康； `openspec context`回答了集合是什么。

## 个人工作集

> **Beta。** 工作集是新 Beta 表面的一部分；命令、标志和文件格式可能会在版本之间发生变化。有关演练，请参阅 [ 商店指南 ](<OpenSpec Store 跨仓库规划指南（Beta）.md#工作集重新打开您一起处理的文件夹>)。

工作集是您一起处理的文件夹的个人命名视图 - 计划根加上您选择的任何其他内容 - 保存在您的计算机上并在工具中按名称重新打开。它纯粹是本地的：从不提交，从不共享，从不从声明派生，删除一个也不会触及成员文件夹。

```bash
openspec workset create [name] [--member <path> | --member <name>=<path>]... [--tool <id>] [--json]
openspec workset list [--json]
openspec workset open <name> [--tool <id>]
openspec workset remove <name> [--yes] [--json]
```

`create` 运行一个简短的引导流程（或以非交互方式获取 `--member` 标志；第一个成员是主要成员 - 会话从那里开始）。 `open` 启动所选工具：编辑器（VS Code、Cursor）打开一个窗口，显示每个成员并返回； CLI agents（Claude Code，codex）接管此终端作为会话，每个成员都附加并且没有提示预先填充，当您退出时结束。打开时丢失的成员文件夹会被跳过并带有注释；其余的打开。每次打开时都可以使用 `--tool` 覆盖已保存的工具首选项。

支持新工具的是配置，而不是代码。每个工具都是两种启动样式之一 - `workspace-file`（使用生成的 `.code-workspace` 启动）或 `attach-dirs`（每个成员一个附加标志） - 全局 `config.json` 中的 `openers` 键（使用 `openspec config edit` 打开它）添加工具或调整内置工具每个字段：

```json
{
  "openers": {
    "zed": { "style": "workspace-file" },
    "claude": { "attach_flag": "--dir" }
  }
}
```

所有工作集状态都位于全局数据目录的 `worksets/` 文件夹下（保存的视图加上生成的 `<name>.code-workspace` 文件，每次打开时都会重新生成）；删除该文件夹会删除所有痕迹。

---

## 浏览命令

### `openspec list`

列出项目中的更改或规格。

```
openspec list [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--specs` |列出规格而不是更改|
| `--changes` |列表更改（默认）|
| `--sort <order>` |按 `recent`（默认）或 `name` 排序|
| `--json` |输出为 JSON|

**示例：**

```bash
# List all active changes
openspec list

# List all specs
openspec list --specs

# JSON output for scripts
openspec list --json
```

**输出（文本）：**

```
Changes:
  add-dark-mode     No tasks      just now
```

---

### `openspec view`

显示交互式仪表板以探索规格和更改。

```
openspec view
```

打开基于终端的界面，用于导航项目的规范和更改。

---

### `openspec show`

显示变更或规格的详细信息。

```
openspec show [item-name] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `item-name` |否|变更名称或规格（如果省略则提示）|

**选项：**

|选项|描述|
|--------|-------------|
| `--type <type>` |指定类型：`change` 或 `spec`（如果明确则自动检测）|
| `--json` |输出为 JSON|
| `--no-interactive` |禁用提示|

**更改特定选项：**

|选项|描述|
|--------|-------------|
| `--deltas-only` |仅显示增量规格（JSON 模式）|

**特定规格选项：**

|选项|描述|
|--------|-------------|
| `--requirements` |仅显示需求，排除场景（JSON模式）|
| `--no-scenarios` |排除场景内容（JSON模式）|
| `-r, --requirement <id>` |通过基于 1 的索引显示具体要求（JSON 模式）|

**示例：**

```bash
# Interactive selection
openspec show

# Show a specific change
openspec show add-dark-mode

# Show a specific spec
openspec show auth --type spec

# JSON output for parsing
openspec show add-dark-mode --json
```

---

## 验证命令

### `openspec validate`

验证结构问题的变更和规格，并根据其将替换的主要规格检查变更的 MODIFIED 要求。

```
openspec validate [item-name] [options]
```

规格增量为零的更改将无法验证，除非其 `.openspec.yaml` 声明 `skip_specs: true`（对于纯粹的重构、工具或文档工作 - 请参阅 [ 配方 5](<OpenSpec 功能、修复与重构工作流实例.md#配方5没有行为改变的重构>)）。

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `item-name` |否|要验证的特定项目（如果省略则提示）|

**选项：**

|选项|描述|
|--------|-------------|
| `--all` |验证所有变更和规格|
| `--changes` |验证所有更改|
| `--specs` |验证所有规格|
| `--archived` |验证存档的更改是否已完成所有任务（用于预提交 linting）|
| `--type <type>` |当名称不明确时指定类型：`change` 或 `spec`|
| `--strict` |启用严格验证模式|
| `--json` |输出为 JSON|
| `--concurrency <n>` |最大并行验证数（默认值：6 或 `OPENSPEC_CONCURRENCY` env）|
| `--no-interactive` |禁用提示|

`--archived` 是它自己的范围：它不验证规格增量（已在存档时应用），它验证 `changes/archive/` 下的每个更改是否已勾选所有 `tasks.md` 复选框，如果未选中任何复选框，则退出非零。这可以捕获未完成工作存档的更改 - 在预提交挂钩中很方便。

**示例：**

```bash
# Interactive validation
openspec validate

# Validate a specific change
openspec validate add-dark-mode

# Validate all changes
openspec validate --changes

# Validate everything with JSON output (for CI/scripts)
openspec validate --all --json

# Strict validation with increased parallelism
openspec validate --all --strict --concurrency 12

# Fail if any archived change still has unchecked tasks
openspec validate --archived
```

**输出（文本）：**

```
Validating add-dark-mode...
  ✓ proposal.md valid
  ✓ specs/ui/spec.md valid
  ⚠ design.md: missing "Technical Approach" section

1 warning found
```

**输出（JSON）：**

```json
{
  "version": "1.0.0",
  "results": {
    "changes": [
      {
        "name": "add-dark-mode",
        "valid": true,
        "warnings": ["design.md: missing 'Technical Approach' section"]
      }
    ]
  },
  "summary": {
    "total": 1,
    "valid": 1,
    "invalid": 0
  }
}
```

---

## 生命周期命令

### `openspec archive`

归档已完成的变更并将增量规格合并到主要规格中。

```
openspec archive [change-name] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `change-name` |否|更改为存档（如果省略则提示；当没有任何内容可以回答提示时则需要）|

**选项：**

|选项|描述|
|--------|-------------|
| `-y, --yes` |跳过确认提示。当没有任何东西可以回答它们时需要 - AI agent、CI 作业或任何关闭标准输入的运行|
| `--skip-specs` |跳过一次存档运行的规范更新。永久没有规格增量的更改应该在其 `.openspec.yaml` 中声明 `skip_specs: true` - 它存档时不带标志|
| `--no-validate` |跳过验证（需要确认）。还禁用功能退役——没有验证器判决，什么都不会退役|

**示例：**

```bash
# Interactive archive (asks which change, then confirms)
openspec archive

# Archive specific change
openspec archive add-dark-mode

# Archive without prompts (agents, CI, scripts)
openspec archive add-dark-mode --yes

# Archive a tooling change that doesn't affect specs
openspec archive update-ci-config --skip-specs
```

**停用功能：**将停用标记添加到更改元数据：

```yaml
# openspec/changes/retire-legacy/.openspec.yaml
schema: spec-driven
retire_capabilities: true
```

然后正常归档更改：

```bash
openspec archive retire-legacy --yes
```

当更改删除功能的最后一个要求时，OpenSpec 会删除其
活动`spec.md`。同一更改中的其他功能增量仍然更新其
主要规格。如果没有标记，存档会在更改任何文件之前停止，并且 
 会告诉您添加它。

**它的作用：**

1. 验证更改（除非 `--no-validate`）
2. 提示确认（除非`--yes`）
3. 在更改任何主要规格之前声明存档目标
4. 验证活动增量规范并将其合并到 `openspec/specs/` — 更改删除的最后一个要求已停用的功能，并且其规范文件已删除，但仅当更改的 `.openspec.yaml` 在其 `schema:` 旁边声明 `retire_capabilities: true` 时
5. 将更改文件夹移动到 `openspec/changes/archive/YYYY-MM-DD-<name>/`
6. 如果在保护完整存档之前规范突变或最终移动失败，则恢复规范并在其活动路径中保留或返回更改
7. 如果经过验证的后备副本完成但分阶段源清理失败，则保留完整的存档和提交的规范状态以进行恢复

**没有终端：** AI agent、CI 作业或任何关闭标准输入的运行都无法
应答步骤 2，因此存档在接触任何内容之前停止，退出1，并将要重新运行的 
 命令命名为 `openspec archive <name> --yes`，携带您传递的任何其他标志 
。预先传递 `--yes`（和更改名称）以跳过往返。

---

## 工作流程命令

这些命令支持工件驱动的 OPSX 工作流程。它们对于人类检查进度和 agents 确定后续步骤都很有用。

### `openspec new change`

在已解析的 OpenSpec 根中创建更改目录和可选的签入元数据。

```bash
openspec new change <name> [options]
```

更改名称必须使用小写短横线：小写字母、数字和 
 单连字符。它们不能包含空格、下划线、大写字母、
连续连字符或leading/trailing连字符。允许使用前导数字 
，因此您可以为订单或等级更改添加名称前缀，例如 `100-add-feature`
 或 `00001-add-auth`。

**选项：**

|选项|描述|
|--------|-------------|
| `--description <text>` |添加到 `README.md` 的说明|
| `--goal <text>` |随更改一起存储的可选目标元数据|
| `--schema <name>` |要使用的工作流程架构|
| `--store <id>` |用作 OpenSpec 根的商店 ID（商店是您注册的独立 OpenSpec 存储库）|
| `--json` |输出JSON|

示例：

```bash
openspec new change add-billing-api
openspec new change add-billing-api --store team-context --json
```

### `openspec status`

显示变更的工件完成状态。

```
openspec status [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--change <id>` |更改名称（如果省略则提示）|
| `--schema <name>` |架构覆盖（从更改的配置中自动检测）|
| `--json` |输出为 JSON|

**示例：**

```bash
# Interactive status check
openspec status

# Status for specific change
openspec status --change add-dark-mode

# JSON for agent use
openspec status --change add-dark-mode --json
```

**输出（文本）：**

```
Change: add-dark-mode
Schema: spec-driven
Progress: 2/4 artifacts complete

[x] proposal
[x] specs
[ ] design
[-] tasks (blocked by: design)
```

声明 `skip_specs: true` 的更改将其规格阶段显示为 `[~] specs (skipped: change declares skip_specs)` 并将其从进度计数中排除。

**输出（JSON）：**

```json
{
  "changeName": "add-dark-mode",
  "schemaName": "spec-driven",
  "isPlanningComplete": false,
  "isComplete": false,
  "applyRequires": ["tasks"],
  "artifacts": [
    {"id": "proposal", "outputPath": "proposal.md", "status": "done", "requires": []},
    {"id": "specs", "outputPath": "specs/**/*.md", "status": "done", "requires": ["proposal"]},
    {"id": "design", "outputPath": "design.md", "status": "ready", "requires": ["proposal"]},
    {"id": "tasks", "outputPath": "tasks.md", "status": "blocked", "requires": ["specs", "design"], "missingDeps": ["design"]}
  ]
}
```

`isPlanningComplete` 报告每个非跳过的计划工件是否存在；
 跳过的工件在未创建的情况下算作已满足。它不报告
实施任务是否完成。 `isComplete` 保留为具有相同值的 
 兼容性别名。

工件按依赖关系顺序列出 - 依赖关系永远不会出现在之后
需要它的东西 - 以及同时准备好的工件
（规范驱动的 `specs` 和 `design` 两者都只需要 `proposal`）保持顺序
模式声明它们而不是按字母顺序排列。所以第一个 `ready` 条目
就是接下来要写的神器。

---

### `openspec instructions`

获取有关创建工件或应用任务的丰富说明。 AI agents 使用它来了解接下来要创建的内容。

```
openspec instructions [artifact] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `artifact` |否|工件 ID，或工作流输入表面：`apply` 或 `archive`|

**选项：**

|选项|描述|
|--------|-------------|
| `--change <id>` |更改名称（非交互模式下需要）|
| `--schema <name>` |架构覆盖|
| `--json` |输出为 JSON|

**特殊情况：**使用`apply`获取任务执行指令。使用
`archive`获取当前只读存档输入（`context`和
`operationGuidance`）以进行有效更改；它不会存档或改变任何东西。

**示例：**

```bash
# Get instructions for next artifact
openspec instructions --change add-dark-mode

# Get specific artifact instructions
openspec instructions design --change add-dark-mode

# Get apply/implementation instructions
openspec instructions apply --change add-dark-mode

# Get current archive operation inputs without archiving
openspec instructions archive --change add-dark-mode --json

# JSON for agent consumption
openspec instructions design --change add-dark-mode --json
```

**输出包括：**

- 工件的模板内容
- 来自配置的项目上下文
- 来自依赖项的内容
- 配置中的每个工件规则
- `apply`/`archive`当前项目背景及配套操作指导

每次 
 调用时都会从已解析的存储库或选定的存储中读取操作输入。项目上下文是必需的提示级输入：agents阅读它并
应用相关的项目事实、约定和约束。操作指南是
可选的附加建议：agents考虑每个条目并仅遵循
适用且与内置工作流程兼容的条目。这两个字段仍然
与显式用户选择、CLI-控制状态、内置指令、
和工件规则分开。报告了冲突的上下文；不遵循冲突或不适用的
指南并解释原因。这些是生成的 agents 的行为 
 合约，而不是可执行的 CLI 检查。 `instructions archive`
仅返回选定的更改、可选输入和根元数据；它不
包括静态归档工作流程。

对于通过 `skip_specs: true` 跳过的工件，输出仅为警告（JSON 添加 `skipped`/`warning` 字段） - 不得创建工件。

---

### `openspec templates`

显示架构中所有工件的已解析模板路径。

```
openspec templates [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--schema <name>` |要检查的架构（默认值：`spec-driven`）|
| `--json` |输出为 JSON|

**示例：**

```bash
# Show template paths for default schema
openspec templates

# Show templates for custom schema
openspec templates --schema my-workflow

# JSON for programmatic use
openspec templates --json
```

**输出（文本）：**

```
Schema: spec-driven

Templates:
  proposal  → ~/.openspec/schemas/spec-driven/templates/proposal.md
  specs     → ~/.openspec/schemas/spec-driven/templates/specs.md
  design    → ~/.openspec/schemas/spec-driven/templates/design.md
  tasks     → ~/.openspec/schemas/spec-driven/templates/tasks.md
```

---

### `openspec schemas`

列出可用的工作流模式及其描述和工件流。

```
openspec schemas [options]
```

**选项：**

|选项|描述|
|--------|-------------|
| `--json` |输出为 JSON|
| `--store <id>` |使用注册的商店作为 OpenSpec 根|

**示例：**

```bash
openspec schemas
```

**输出：**

```
Available schemas:

  spec-driven (package)
    The default spec-driven development workflow
    Flow: proposal → specs → design → tasks

  my-custom (project)
    Custom workflow for this project
    Flow: research → proposal → tasks
```

---

## 架构命令

用于创建和管理自定义工作流程架构的命令。

### `openspec schema init`

创建一个新的项目本地架构。

```
openspec schema init <name> [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `name` |是的|架构名称（kebab-case）|

**选项：**

|选项|描述|
|--------|-------------|
| `--description <text>` |架构描述|
| `--artifacts <list>` |以逗号分隔的工件 ID（默认值：`proposal,specs,design,tasks`）|
| `--default` |设置为项目默认架构|
| `--no-default` |不提示设置为默认值|
| `--force` |覆盖现有架构|
| `--json` |输出为 JSON|

**示例：**

```bash
# Interactive schema creation
openspec schema init research-first

# Non-interactive with specific artifacts
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

**它创造了什么：**

```
openspec/schemas/<name>/
├── schema.yaml           # Schema definition
└── templates/
    ├── proposal.md       # Template for each artifact
    ├── specs.md
    ├── design.md
    └── tasks.md
```

---

### `openspec schema fork`

将现有架构复制到您的项目中以进行自定义。

```
openspec schema fork <source> [name] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `source` |是的|要复制的架构|
| `name` |否|新架构名称（默认值：`<source>-custom`）|

**选项：**

|选项|描述|
|--------|-------------|
| `--force` |覆盖现有目的地|
| `--json` |输出为 JSON|

**示例：**

```bash
# Fork the built-in spec-driven schema
openspec schema fork spec-driven my-workflow
```

---

### `openspec schema validate`

验证架构的结构和模板。

```
openspec schema validate [name] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `name` |否|要验证的架构（如果省略则验证所有）|

**选项：**

|选项|描述|
|--------|-------------|
| `--verbose` |显示详细的验证步骤|
| `--json` |输出为 JSON|

**示例：**

```bash
# Validate a specific schema
openspec schema validate my-workflow

# Validate all schemas
openspec schema validate
```

---

### `openspec schema which`

显示架构从何处解析（对于调试优先级很有用）。

```
openspec schema which [name] [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `name` |否|模式名称|

**选项：**

|选项|描述|
|--------|-------------|
| `--all` |列出所有模式及其来源|
| `--json` |输出为 JSON|

**示例：**

```bash
# Check where a schema comes from
openspec schema which spec-driven
```

**输出：**

```
spec-driven resolves from: package
  Source: /usr/local/lib/node_modules/@fission-ai/openspec/schemas/spec-driven
```

**架构优先级：**

1. 项目：`openspec/schemas/<name>/`
2. 用户：`~/.local/share/openspec/schemas/<name>/`
3. 包：内置模式

---

## 配置命令

### `openspec config`

查看和修改全局OpenSpec配置。

```
openspec config <subcommand> [options]
```

**子命令：**

|子命令|描述|
|------------|-------------|
| `path` |显示配置文件位置|
| `list` |显示所有当前设置|
| `get <key>` |获取特定值|
| `set <key> <value>` |设置一个值|
| `unset <key>` |移除一把钥匙|
| `reset` |重置为默认值|
| `edit` |打开于 `$EDITOR`|
| `profile [preset]` |以交互方式或通过预设配置工作流程配置文件|

**示例：**

```bash
# Show config file path
openspec config path

# List all settings
openspec config list

# Get a specific value
openspec config get telemetry.enabled

# Set a value (disable anonymous usage telemetry)
openspec config set telemetry.enabled false

# Set a string value explicitly
openspec config set user.name "My Name" --string

# Remove a custom setting
openspec config unset user.name

# Set a machine-level default store (fallback root when no --store,
# local root, or project store: pointer resolves)
openspec config set defaultStore team-plans

# Reset all configuration
openspec config reset --all --yes

# Edit config in your editor
openspec config edit

# Configure profile with action-based wizard
openspec config profile

# Fast preset: switch workflows to core (keeps delivery mode)
openspec config profile core
```

**遥测选择退出：** `telemetry.enabled` 未设置时默认为打开（选择退出模型）。
将其设置为 `false` 以禁用匿名使用统计信息和 `openspec update` 版本检查。
环境变量优先于配置：`OPENSPEC_TELEMETRY=0`，`DO_NOT_TRACK=1`，
和真实的`CI`值（例如`true`/`1`/`yes`）始终禁用遥测，无论配置值如何。

`openspec config profile` 从当前状态摘要开始，然后让您选择：
- 改变交付+工作流程
- 仅更改交付
- 仅更改工作流程
- 保留当前设置（退出）

如果保留当前设置，则不会写入任何更改，也不会显示更新提示。
如果没有配置更改，但当前项目文件与全局profile/delivery不同步，OpenSpec将显示警告并建议`openspec update`。
按`Ctrl+C` 还会干净地取消流程（无堆栈跟踪）并以代码 `130` 退出。
在工作流程清单中，`[x]` 表示在全局配置中选择了工作流程。要将这些选择应用到项目文件，请运行 `openspec update`（或在项目内出现提示时选择 `Apply changes to this project now?`）。

**互动示例：**

```bash
# Delivery-only update
openspec config profile
# choose: Change delivery only
# choose delivery: Skills only

# Workflows-only update
openspec config profile
# choose: Change workflows only
# toggle workflows in the checklist, then confirm
```

---

## 实用命令

### `openspec feedback`

提交有关 OpenSpec 的反馈。创建 GitHub 问题。

```
openspec feedback <message> [options]
```

**参数：**

|论证|必填|描述|
|----------|----------|-------------|
| `message` |是的|反馈汇总；长文本在问题标题中被缩短并保留在正文中|

**选项：**

|选项|描述|
|--------|-------------|
| `--body <text>` |摘要后包含更多详细信息|

**要求：** GitHub CLI (`gh`) 必须安装并经过身份验证。

**示例：**

```bash
openspec feedback "Add support for custom artifact types" \
  --body "I'd like to define my own artifact types beyond the built-in ones."
```

---

### `openspec completion`

管理 OpenSpec CLI 的 shell 完成。

```
openspec completion <subcommand> [shell]
```

**子命令：**

|子命令|描述|
|------------|-------------|
| `generate [shell]` |将完成脚本输出到 stdout|
| `install [shell]` |为您的 shell 安装完成|
| `uninstall [shell]` |删除已安装的完成项|

**支持的外壳：** `bash`、`zsh`、`fish`、`powershell`

**示例：**

```bash
# Install completions (auto-detects shell)
openspec completion install

# Install for specific shell
openspec completion install zsh

# Generate script for manual installation (bash)
openspec completion generate bash > ~/.bash_completion.d/openspec

# Uninstall
openspec completion uninstall
```

**Windows (PowerShell):** 当前 PowerShell 主机的安装完成情况：

```powershell
$env:PROFILE = $PROFILE
openspec completion install powershell
. $PROFILE
```

`$env:PROFILE` 告诉 OpenSpec 在此会话中配置哪个配置文件。 
安装程序创建缺失的配置文件目录并添加加载
`OpenSpecCompletion.ps1`的托管块。重新加载配置文件可以立即完成。

要从当前主机卸载，请运行：

```powershell
$env:PROFILE = $PROFILE
openspec completion uninstall powershell
```

卸载后重新启动 PowerShell 以清除当前会话的完成内容。

完成是可选择的。当你第一次
在交互式终端中运行命令时，CLI在stderr上提到它们一次，并且再也不会 - 如果你已经安装了补全，它也会保持安静
。将 `OPENSPEC_NO_COMPLETIONS=1` 设置为 
 完全抑制该尖端。

---

## 退出代码

|代码|含义|
|------|---------|
| `0` |成功|
| `1` |错误（验证失败、文件丢失等）|

---

## 环境变量

|变量|描述|
|----------|-------------|
| `OPENSPEC_TELEMETRY` |设置为 `0` 以禁用遥测和 `openspec update` 版本检查（覆盖全局配置中的 `telemetry.enabled`）|
| `DO_NOT_TRACK` |设置为 `1` 以禁用遥测和 `openspec update` 版本检查（标准 DNT 信号；覆盖配置）|
| `OPENSPEC_CONCURRENCY` |批量验证的默认并发数（默认值：6）|
|`EDITOR` 或 `VISUAL`|`openspec config edit` 的编辑|
| `NO_COLOR` |设置后禁用颜色输出|
| `OPENSPEC_NO_ANIMATION` |设置后禁用 `openspec init` 欢迎动画|
| `OPENSPEC_NO_COMPLETIONS` |设置为 `1` 以抑制有关 shell 完成的一次性提示|
| `OPENSPEC_NO_UPDATE_CHECK` |设置时禁用 `openspec update` 检查较新发布的 CLI（任何值，包括空）。设置 `CI` 时也会跳过（除非 `false`/`0`/`no`/`off`）或 `NODE_ENV=test`|
| `npm_config_registry` |注册表`openspec update`版本检查询问。必须是 `http(s)` URL，否则会回退到 `https://registry.npmjs.org`。未读取 `.npmrc` 文件|

---

## 相关文档

- [命令](<OpenSpec AI 聊天命令参考.md>) - AI斜线命令（`/opsx:propose`、`/opsx:apply`等）
- [工作流程](<OpenSpec 快速与扩展工作流.md>) - 常见模式以及何时使用每个命令
- [自定义](<OpenSpec 项目配置与工作流定制.md>) - 创建自定义架构和模板
- [入门](<OpenSpec 入门与首个变更.md>) - 首次设置指南


## 原文对照

<details>
<summary>展开完整原文</summary>

# CLI Reference

The OpenSpec CLI (`openspec`) provides terminal commands for project setup, validation, status inspection, and management. These commands complement the AI slash commands (like `/opsx:propose`) documented in [Commands](<OpenSpec AI 聊天命令参考.md>).

## Summary

| Category | Commands | Purpose |
|----------|----------|---------|
| **Setup** | `init`, `update` | Initialize and update OpenSpec in your project |
| **Stores (standalone OpenSpec repos)** | `store setup`, `store register`, `store unregister`, `store remove`, `store list`, `store doctor` | Manage stores — standalone OpenSpec repos you've registered |
| **Health** | `doctor` | Report relationship health for the resolved root |
| **Working context** | `context` | Assemble the working set (root + referenced stores) |
| **Personal worksets** | `workset create`, `workset list`, `workset open`, `workset remove` | Keep and open personal, local working views in your tool |
| **Browsing** | `list`, `view`, `show` | Explore changes and specs |
| **Validation** | `validate` | Check changes and specs for issues |
| **Lifecycle** | `archive` | Finalize completed changes |
| **Workflow** | `new change`, `status`, `instructions`, `templates`, `schemas` | Artifact-driven workflow support |
| **Schemas** | `schema init`, `schema fork`, `schema validate`, `schema which` | Create and manage custom workflows |
| **Config** | `config` | View and modify settings |
| **Utility** | `feedback`, `completion` | Feedback and shell integration |

---

## Human vs Agent Commands

Most CLI commands are designed for **human use** in a terminal. Some commands also support **agent/script use** via JSON output.

### Human-Only Commands

These commands are interactive and designed for terminal use:

| Command | Purpose |
|---------|---------|
| `openspec init` | Initialize project (interactive prompts) |
| `openspec view` | Interactive dashboard |
| `openspec workset open <name>` | Open a saved workset (editor window or terminal agent session) |
| `openspec config edit` | Open config in editor |
| `openspec feedback` | Submit feedback via GitHub |
| `openspec completion install` | Install shell completions |

### Agent-Compatible Commands

These commands support `--json` output for programmatic use by AI agents and scripts:

| Command | Human Use | Agent Use |
|---------|-----------|-----------|
| `openspec list` | Browse changes/specs | `--json` for structured data |
| `openspec show <item>` | Read content | `--json` for parsing |
| `openspec validate` | Check for issues | `--all --json` for bulk validation |
| `openspec status` | See artifact progress | `--json` for structured status |
| `openspec instructions` | Get next steps | `--json` for agent instructions |
| `openspec templates` | Find template paths | `--json` for path resolution |
| `openspec schemas` | List available schemas | `--json` for schema discovery; `--store <id>` to select a registered root |
| `openspec store setup <id>` | Create and register a local store | `--json` with explicit inputs for structured setup output |
| `openspec store register <path>` | Register an existing store | `--json` for structured registration output |
| `openspec store unregister <id>` | Forget a local store registration | `--json` for structured cleanup output |
| `openspec store remove <id>` | Delete a registered local store folder | `--yes --json` for non-interactive deletion |
| `openspec store list` | Browse registered stores | `--json` for structured registrations |
| `openspec store doctor` | Check local store setup | `--json` for structured diagnostics |
| `openspec new change <id>` | Create repo-local change scaffolding | `--json`, plus `--store <id>` to use a registered store as the OpenSpec root |
| `openspec workset create [name]` | Compose a personal working view | `--member <path> --json` for non-interactive composition |
| `openspec workset list` | Browse saved worksets | `--json` for structured views |
| `openspec workset remove <name>` | Delete a saved view | `--yes --json` for non-interactive removal |

---

## Global Options

These options work with all commands:

| Option | Description |
|--------|-------------|
| `--version`, `-V` | Show version number |
| `--no-color` | Disable color output |
| `--help`, `-h` | Display help for command |

---

## Setup Commands

### `openspec init`

Initialize OpenSpec in your project. Creates the folder structure and configures AI tool integrations.

Default behavior uses global config defaults: profile `core`, delivery `both`, workflows `propose, explore, apply, update, sync, archive`.

```
openspec init [path] [options]
```

Use `--language <language>` to add a language instruction to a new project's
`openspec/config.yaml`. For an existing project, edit the config's `context`
field so OpenSpec never overwrites project-specific guidance.

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `path` | No | Target directory (default: current directory) |

**Options:**

| Option | Description |
|--------|-------------|
| `--tools <list>` | Configure AI tools non-interactively. Use `all`, `none`, or comma-separated list |
| `--language <language>` | Write artifacts in this language when creating a new config |
| `--force` | Auto-cleanup legacy files without prompting |
| `--profile <profile>` | Override global profile for this init run (`core` or `custom`) |
| `--no-animation` | Show a static welcome screen instead of the animated one |
| `--copilot-cloud` | Set up GitHub Copilot [cloud coding-agent files](<OpenSpec 支持的 AI 工具与调用方式.md#github-copilot-cloud-coding-agent>) without prompting |
| `--no-copilot-cloud` | Skip GitHub Copilot cloud coding-agent files without prompting |

`--profile custom` uses whatever workflows are currently selected in global config (`openspec config profile`).

The welcome animation is also skipped when the `OPENSPEC_NO_ANIMATION` environment variable is set (any value, including empty), when `NO_COLOR` is set to a non-empty value, or when the OS reduced-motion preference is enabled (macOS Reduce Motion, GNOME animations disabled).

**Supported tool IDs (`--tools`)** — `windsurf` is also accepted, as an alias for `devin`: `amazon-q`, `antigravity`, `auggie`, `bob`, `claude`, `cline`, `command-code`, `codeartsagent`, `codex`, `devin`, `forgecode`, `codebuddy`, `continue`, `costrict`, `crush`, `cursor`, `factory`, `gemini`, `github-copilot`, `hermes`, `iflow`, `junie`, `kilocode`, `kimi`, `kiro`, `lingma`, `minimax-code`, `vibe`, `oh-my-pi`, `opencode`, `pi`, `codeassistant`, `qoder`, `qwen`, `rovodev`, `roocode`, `trae`, `zed`, `zcode`, `agents`

> This list mirrors `AI_TOOLS` in `src/core/config.ts`. See [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>) for each tool's skill and command paths.

**Examples:**

```bash
# Interactive initialization
openspec init

# Initialize in a specific directory
openspec init ./my-project

# Non-interactive: configure for Claude and Cursor
openspec init --tools claude,cursor

# Non-interactive: configure global MiniMax Code skills
openspec init --tools minimax-code

# Configure for all supported tools
openspec init --tools all

# Override profile for this run
openspec init --profile core

# Skip prompts and auto-cleanup legacy files
openspec init --force
```

**What it creates:**

```
openspec/
├── specs/              # Your specifications (source of truth)
├── changes/            # Proposed changes
└── config.yaml         # Project configuration

.claude/skills/         # Claude Code skills (if claude selected)
.cursor/skills/         # Cursor skills (if cursor selected)
.cursor/commands/       # Cursor OPSX commands (if delivery includes commands)
.agents/skills/         # Shared skills for AGENTS.md-compatible tools (if agents selected)
... (other tool configs)
```

---

### `openspec update`

Update OpenSpec instruction files after upgrading the CLI. Re-generates AI tool configuration files using your current global profile, selected workflows, and delivery mode.

```
openspec update [path] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `path` | No | Target directory (default: current directory) |

**Options:**

| Option | Description |
|--------|-------------|
| `--force` | Force update even when files are up to date |

**Example:**

```bash
# Update instruction files after npm upgrade
npm install -g @fission-ai/openspec@latest
openspec update
```

Upgrade the package first. Instruction files are generated by the installed CLI, so running `openspec update` against a stale install reports everything up to date without adding the workflows newer releases ship.

To make that visible, `openspec update` asks the npm registry whether a newer CLI has been published. When yours is behind, it offers to upgrade:

```text
A newer OpenSpec CLI is available (v1.6.0 → v1.7.0).
  Running from: /usr/local/lib/node_modules/@fission-ai/openspec
? Upgrade to v1.7.0 now? (Y/n)
```

Answer yes and it runs `npm install -g @fission-ai/openspec@latest`, then re-runs the update with the new CLI so the new workflows land in the same command. It confirms the upgrade by asking the installed binary its version rather than trusting npm's exit code, so if another install earlier on your `PATH` is still answering, it tells you instead of claiming success. Answer no and it prints the command and updates with the CLI you have. Ctrl-C stops the command.

The offer appears only in an interactive terminal, and only when npm owns the install — the one case `npm install -g` actually fixes. Everything else gets the command that matches how it was installed instead:

| How OpenSpec is installed | What you get |
|---------------------------|--------------|
| Global npm install | The prompt, and the upgrade run for you — in an interactive terminal; piped output gets the printed command instead |
| Global pnpm, bun, yarn, or volta install | That manager's own command: `pnpm add -g …@latest`, `bun add -g …@latest`, `yarn global add …@latest`, or `volta install …@latest` |
| A dependency of the project | A note to update the dependency, since its package manager owns the lockfile |
| An `npx` / `dlx` cache | `npx @fission-ai/openspec@latest update` — that command is the update, so there is no second step |
| A git clone | Nothing — your version is whatever the branch says |

Whenever anything is printed, it names the directory the running CLI was loaded from — the thing to check when you did upgrade but a stale shim still owns your `PATH`.

It asks the registry in `npm_config_registry` when npm exports it, and `https://registry.npmjs.org` otherwise. No `.npmrc` is read: letting file contents choose where an outbound request goes is a flow worth avoiding, and a project's `.npmrc` travels with the repository. On a private mirror, export `npm_config_registry` — or set `OPENSPEC_NO_UPDATE_CHECK` to skip the check entirely. The check is skipped when `CI` is set to anything but an explicit off-value (`false`, `0`, `no`, `off`, or empty), under `NODE_ENV=test`, and whenever `OPENSPEC_NO_UPDATE_CHECK` (any value), `DO_NOT_TRACK=1`, or `OPENSPEC_TELEMETRY=0` is set. It runs before the update and can delay it by at most 1.5 seconds — it gives up after that even when the network drops packets silently, and stays quiet when the registry is unreachable.

**How "up to date" is decided:** skill files record the version that generated
them, so OpenSpec compares that against the installed CLI. Command files carry no
version stamp, so for a tool that has commands but no skills (delivery
`commands`), OpenSpec compares the file contents against what it would generate
now — edits to those files count as drift and are overwritten. With delivery
`skills` or `both`, only the recorded version is checked, so a hand-edited file
whose version still matches is left alone; use `--force` to rewrite it. Either
way, generated files are OpenSpec's to own — keep your own instructions
elsewhere.

---

## Stores (standalone OpenSpec repos)

> **Beta.** Stores and the features built on them (references, working context, worksets) are new; command names, flags, file formats, and JSON output may change shape between releases. For the problem-first walkthrough, see the [stores guide](<OpenSpec Store 跨仓库规划指南（Beta）.md>).

A store is a standalone OpenSpec repo you've registered on this machine — for example a planning repo or a contracts repo. Registering a store lets normal commands (`list`, `show`, `status`, `validate`, `new change`, `archive`, ...) act in it from anywhere by passing `--store <id>`.

### `openspec store setup`

Create and register a local store. With no arguments in a terminal,
OpenSpec guides the user through setup. Agents and scripts should pass explicit
inputs and use `--json`.

```bash
openspec store setup [id] [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--path <path>` | Folder where the store should live (for example `~/openspec/<id>`) |
| `--remote <url>` | Record the canonical remote in the new store's `store.yaml` |
| `--init-git` | Initialize a Git repository with an initial commit (default) |
| `--no-init-git` | Skip every Git action: no init, no initial commit |
| `--json` | Output JSON |

Non-interactive runs (`--json`, scripts, agents) must pass both the store id and `--path`. In an interactive terminal, setup prompts for the location with an editable suggestion in a visible, user-owned place (for example `~/openspec/<id>`); it never defaults to OpenSpec's managed data directory.

Examples:

```bash
openspec store setup
openspec store setup team-context
openspec store setup team-context --path ~/openspec/team-context --no-init-git
openspec store setup team-context --path ~/openspec/team-context --no-init-git --json
```

### `openspec store register`

Register an existing local store folder. During the stores beta, a root may be
registered before any changes exist, specs have been applied, or changes have
been archived; in that case `openspec/changes/`, `openspec/specs/`, and
`openspec/changes/archive/` may be absent until normal commands create them.
A config-only repo that declares `store: <id>` remains a pointer to another
store and is not registered as a store root unless that pointer is removed.

```bash
openspec store register [path] [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--id <id>` | Store id; defaults to store metadata or folder name |
| `--yes` | Confirm creating store identity metadata for a healthy OpenSpec root |
| `--json` | Output JSON |

### `openspec store unregister`

Forget a local store registration without deleting files.

```bash
openspec store unregister <id> [--json]
```

Use this when a store was moved, cloned somewhere else, or should no longer be
shown by OpenSpec on this machine.

### `openspec store remove`

Forget a local store registration and delete its local folder.

```bash
openspec store remove <id> [--yes] [--json]
```

`remove` shows the exact folder before deleting in an interactive terminal.
Agents, scripts, and JSON callers must pass `--yes` to confirm deletion.
OpenSpec refuses to delete a folder that does not contain matching
store metadata.

### `openspec store list`

List locally registered stores.

```bash
openspec store list [--json]
openspec store ls [--json]
```

### `openspec store doctor`

Check local store registration, metadata, and Git presence.

```bash
openspec store doctor [id] [--json]
```

Doctor is diagnostic-only; it reports missing roots, metadata mismatches, and invalid local registry state without modifying the store.

### Referencing stores from a project

A project repo can declare which stores its work draws on in `openspec/config.yaml`:

```yaml
schema: spec-driven
references:
  - team-context
```

From then on, `openspec instructions` output in that repo (both the per-artifact and `apply` surfaces, JSON and human modes) carries an index of each referenced store's specs — spec ids, a one-line summary from each spec's Purpose section, and the fetch command (`openspec show <spec-id> --type spec --store <id>`). The index is built live from the registered checkout on every run; spec content is never copied into the output.

References are read-only context. They never change where commands act: work stays in the repo's own root, and writing to a referenced store remains an explicit `--store` action. A reference that cannot be resolved (for example, a store not registered on this machine) degrades to a warning in the index with the exact fix, and instructions still generate. `openspec doctor` reports reference health in one place.

### Recording where a store is cloned from

A store can record its canonical clone source in its committed identity file, so onboarding never dead-ends at "register the store":

```bash
openspec store setup team-context --path ~/openspec/team-context \
  --remote git@github.com:acme/team-context.git
```

The remote lands in `.openspec-store/store.yaml` inside the initial commit, so every clone is born knowing it. For an existing store, edit `store.yaml` by hand and commit. `store doctor` shows the recorded remote (and the checkout's observed Git origin); setup/register sharing guidance names it; and register records the checkout's origin in the machine-local registry.

A reference declaration can carry the clone source too, so a teammate who doesn't have the store yet gets a complete, pasteable fix (`git clone <remote> <path> && openspec store register <path> --id <id>`):

```yaml
references:
  - { id: team-context, remote: "git@github.com:acme/team-context.git" }
```

Recording a remote is not sync: OpenSpec never clones, pulls, or pushes on its own.

### Declaring a default store

A repo whose planning is fully externalized — no local `openspec/specs/` or `openspec/changes/` — can declare its store once instead of passing `--store` on every command:

```yaml
# openspec/config.yaml (the only file under openspec/)
store: team-context
```

Normal commands then resolve to the declared store automatically; the root banner and JSON `root` block report `source: "declared"` with the store id, and printed hints still carry `--store <id>`. The declaration is a fallback, never an override: explicit `--store` always wins, and a directory with real planning folders ignores the pointer (with a warning). To convert a pointer repo into a local OpenSpec root, remove the `store:` line and run `openspec init` — init refuses to scaffold while the declaration is present.

A machine-level variant covers every repo at once: `openspec config set defaultStore <id>` (see Configuration). It is consulted only after `--store`, a local root, and a project pointer have all failed to resolve; the root banner and JSON `root` block then report `source: "global_default"`.

## Doctor (relationship health)

One read-only question, one place: is the OpenSpec root healthy, and are the stores it references available on this machine?

```bash
openspec doctor [--store <id>] [--json]
```

The report separates root health, store metadata health (including a note when the recorded remote and the checkout's origin diverge, and a note when the store checkout has drifted behind its last-fetched upstream tracking ref), and reference health (the same diagnostics instructions show, with clone fixes for unresolved references). Health findings of any severity exit 0 — agents read the `status` arrays; only command failures (no root, unknown store) exit 1. Doctor never clones, syncs, or repairs. To get the assembled set itself rather than its health, use `openspec context`.

## Working context (the assembled set)

Everything this work relates to through OpenSpec declarations, in one working set: the OpenSpec root and the stores it references.

```bash
openspec context [--store <id>] [--json] [--code-workspace <path> [--force]]
```

The JSON brief is agent-consumable (each available referenced store carries its fetch recipe; unresolved members carry the same fixes instructions and doctor show). `--code-workspace` additionally writes a VS Code workspace file containing the root plus the available referenced stores (`ref:<id>` folders) — the one write this command performs, refused without `--force` if the file exists. Unavailable members are reported, never guessed at.

"Working context" is the assembled set; the `context:` field in `openspec/config.yaml` is project background injected into instructions — two different things. `openspec doctor` answers whether the set is healthy; `openspec context` answers what the set is.

## Personal worksets

> **Beta.** Worksets are part of the new beta surface; commands, flags, and file formats may change shape between releases. For the walkthrough, see the [stores guide](<OpenSpec Store 跨仓库规划指南（Beta）.md#worksets-reopen-the-folders-you-work-on-together>).

A workset is a personal, named view of the folders you work on together — a planning root plus whatever else you choose — kept on your machine and reopened by name in your tool. It is purely local: never committed, never shared, never derived from declarations, and removing one never touches a member folder.

```bash
openspec workset create [name] [--member <path> | --member <name>=<path>]... [--tool <id>] [--json]
openspec workset list [--json]
openspec workset open <name> [--tool <id>]
openspec workset remove <name> [--yes] [--json]
```

`create` runs a short guided flow (or takes `--member` flags non-interactively; the first member is the primary — sessions start there). `open` launches the chosen tool: editors (VS Code, Cursor) open a window with every member and return; CLI agents (Claude Code, codex) take over this terminal as a session with every member attached and no prompt pre-filled, ending when you exit. A member folder missing at open time is skipped with a note; the rest opens. The saved tool preference is overridable per open with `--tool`.

Supporting a new tool is configuration, not code. Every tool is one of two launch styles — `workspace-file` (launched with the generated `.code-workspace`) or `attach-dirs` (one attach flag per member) — and the `openers` key in the global `config.json` (open it with `openspec config edit`) adds tools or adjusts built-ins per field:

```json
{
  "openers": {
    "zed": { "style": "workspace-file" },
    "claude": { "attach_flag": "--dir" }
  }
}
```

All workset state lives under the global data dir's `worksets/` folder (the saved views plus the generated `<name>.code-workspace` files, regenerated on every open); deleting that folder removes every trace.

---

## Browsing Commands

### `openspec list`

List changes or specs in your project.

```
openspec list [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--specs` | List specs instead of changes |
| `--changes` | List changes (default) |
| `--sort <order>` | Sort by `recent` (default) or `name` |
| `--json` | Output as JSON |

**Examples:**

```bash
# List all active changes
openspec list

# List all specs
openspec list --specs

# JSON output for scripts
openspec list --json
```

**Output (text):**

```
Changes:
  add-dark-mode     No tasks      just now
```

---

### `openspec view`

Display an interactive dashboard for exploring specs and changes.

```
openspec view
```

Opens a terminal-based interface for navigating your project's specifications and changes.

---

### `openspec show`

Display details of a change or spec.

```
openspec show [item-name] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `item-name` | No | Name of change or spec (prompts if omitted) |

**Options:**

| Option | Description |
|--------|-------------|
| `--type <type>` | Specify type: `change` or `spec` (auto-detected if unambiguous) |
| `--json` | Output as JSON |
| `--no-interactive` | Disable prompts |

**Change-specific options:**

| Option | Description |
|--------|-------------|
| `--deltas-only` | Show only delta specs (JSON mode) |

**Spec-specific options:**

| Option | Description |
|--------|-------------|
| `--requirements` | Show only requirements, exclude scenarios (JSON mode) |
| `--no-scenarios` | Exclude scenario content (JSON mode) |
| `-r, --requirement <id>` | Show specific requirement by 1-based index (JSON mode) |

**Examples:**

```bash
# Interactive selection
openspec show

# Show a specific change
openspec show add-dark-mode

# Show a specific spec
openspec show auth --type spec

# JSON output for parsing
openspec show add-dark-mode --json
```

---

## Validation Commands

### `openspec validate`

Validate changes and specs for structural issues, and check a change's MODIFIED requirements against the main specs they would replace.

```
openspec validate [item-name] [options]
```

A change with zero spec deltas fails validation unless its `.openspec.yaml` declares `skip_specs: true` (for pure refactors, tooling, or docs work — see [Recipe 5](<OpenSpec 功能、修复与重构工作流实例.md#recipe-5-a-refactor-with-no-behavior-change>)).

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `item-name` | No | Specific item to validate (prompts if omitted) |

**Options:**

| Option | Description |
|--------|-------------|
| `--all` | Validate all changes and specs |
| `--changes` | Validate all changes |
| `--specs` | Validate all specs |
| `--archived` | Validate that archived changes have all tasks completed (for pre-commit linting) |
| `--type <type>` | Specify type when name is ambiguous: `change` or `spec` |
| `--strict` | Enable strict validation mode |
| `--json` | Output as JSON |
| `--concurrency <n>` | Max parallel validations (default: 6, or `OPENSPEC_CONCURRENCY` env) |
| `--no-interactive` | Disable prompts |

`--archived` is its own scope: it does not validate spec deltas (already applied at archive time), it verifies that every change under `changes/archive/` has all of its `tasks.md` checkboxes ticked, exiting non-zero if any are unchecked. This catches changes that were archived with unfinished work — handy in a pre-commit hook.

**Examples:**

```bash
# Interactive validation
openspec validate

# Validate a specific change
openspec validate add-dark-mode

# Validate all changes
openspec validate --changes

# Validate everything with JSON output (for CI/scripts)
openspec validate --all --json

# Strict validation with increased parallelism
openspec validate --all --strict --concurrency 12

# Fail if any archived change still has unchecked tasks
openspec validate --archived
```

**Output (text):**

```
Validating add-dark-mode...
  ✓ proposal.md valid
  ✓ specs/ui/spec.md valid
  ⚠ design.md: missing "Technical Approach" section

1 warning found
```

**Output (JSON):**

```json
{
  "version": "1.0.0",
  "results": {
    "changes": [
      {
        "name": "add-dark-mode",
        "valid": true,
        "warnings": ["design.md: missing 'Technical Approach' section"]
      }
    ]
  },
  "summary": {
    "total": 1,
    "valid": 1,
    "invalid": 0
  }
}
```

---

## Lifecycle Commands

### `openspec archive`

Archive a completed change and merge delta specs into main specs.

```
openspec archive [change-name] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `change-name` | No | Change to archive (prompts if omitted; required when nothing can answer the prompt) |

**Options:**

| Option | Description |
|--------|-------------|
| `-y, --yes` | Skip confirmation prompts. Required when nothing can answer them — an AI agent, a CI job, or any run with stdin closed |
| `--skip-specs` | Skip spec updates for one archive run. A change that permanently has no spec deltas should declare `skip_specs: true` in its `.openspec.yaml` instead — it archives with no flag |
| `--no-validate` | Skip validation (requires confirmation). Also disables capability retirement — with no validator verdict, nothing is retired |

**Examples:**

```bash
# Interactive archive (asks which change, then confirms)
openspec archive

# Archive specific change
openspec archive add-dark-mode

# Archive without prompts (agents, CI, scripts)
openspec archive add-dark-mode --yes

# Archive a tooling change that doesn't affect specs
openspec archive update-ci-config --skip-specs
```

**Retire a capability:** Add the retirement marker to the change metadata:

```yaml
# openspec/changes/retire-legacy/.openspec.yaml
schema: spec-driven
retire_capabilities: true
```

Then archive the change normally:

```bash
openspec archive retire-legacy --yes
```

When the change removes the capability's last requirement, OpenSpec deletes its
live `spec.md`. Other capability deltas in the same change still update their
main specs. Without the marker, archive stops before changing any files and
tells you to add it.

**What it does:**

1. Validates the change (unless `--no-validate`)
2. Prompts for confirmation (unless `--yes`)
3. Claims the archive destination before changing any main spec
4. Validates and merges the active delta specs into `openspec/specs/` — a capability whose last requirement the change removes is retired, and its spec file deleted, but only when the change's `.openspec.yaml` declares `retire_capabilities: true` next to its `schema:`
5. Moves the change folder to `openspec/changes/archive/YYYY-MM-DD-<name>/`
6. If a spec mutation or final move fails before a complete archive is secured, restores the specs and leaves or returns the change at its active path
7. If a verified fallback copy completes but staged-source cleanup fails, retains the complete archive and committed spec state for recovery

**Without a terminal:** an AI agent, a CI job, or any run with stdin closed cannot
answer step 2, so archive stops before touching anything, exits 1, and names the
command to rerun — `openspec archive <name> --yes`, carrying whatever other flags
you passed. Pass `--yes` (and the change name) up front to skip the round trip.

---

## Workflow Commands

These commands support the artifact-driven OPSX workflow. They're useful for both humans checking progress and agents determining next steps.

### `openspec new change`

Create a change directory and optional checked-in metadata in the resolved OpenSpec root.

```bash
openspec new change <name> [options]
```

Change names must use lowercase kebab-case: lowercase letters, numbers, and
single hyphens. They cannot contain spaces, underscores, uppercase letters,
consecutive hyphens, or leading/trailing hyphens. A leading number is allowed,
so you can prefix names to order or tier changes, for example `100-add-feature`
or `00001-add-auth`.

**Options:**

| Option | Description |
|--------|-------------|
| `--description <text>` | Description to add to `README.md` |
| `--goal <text>` | Optional goal metadata to store with the change |
| `--schema <name>` | Workflow schema to use |
| `--store <id>` | Store id to use as the OpenSpec root (a store is a standalone OpenSpec repo you've registered) |
| `--json` | Output JSON |

Examples:

```bash
openspec new change add-billing-api
openspec new change add-billing-api --store team-context --json
```

### `openspec status`

Display artifact completion status for a change.

```
openspec status [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--change <id>` | Change name (prompts if omitted) |
| `--schema <name>` | Schema override (auto-detected from change's config) |
| `--json` | Output as JSON |

**Examples:**

```bash
# Interactive status check
openspec status

# Status for specific change
openspec status --change add-dark-mode

# JSON for agent use
openspec status --change add-dark-mode --json
```

**Output (text):**

```
Change: add-dark-mode
Schema: spec-driven
Progress: 2/4 artifacts complete

[x] proposal
[x] specs
[ ] design
[-] tasks (blocked by: design)
```

A change that declares `skip_specs: true` shows its specs stage as `[~] specs (skipped: change declares skip_specs)` and excludes it from the progress count.

**Output (JSON):**

```json
{
  "changeName": "add-dark-mode",
  "schemaName": "spec-driven",
  "isPlanningComplete": false,
  "isComplete": false,
  "applyRequires": ["tasks"],
  "artifacts": [
    {"id": "proposal", "outputPath": "proposal.md", "status": "done", "requires": []},
    {"id": "specs", "outputPath": "specs/**/*.md", "status": "done", "requires": ["proposal"]},
    {"id": "design", "outputPath": "design.md", "status": "ready", "requires": ["proposal"]},
    {"id": "tasks", "outputPath": "tasks.md", "status": "blocked", "requires": ["specs", "design"], "missingDeps": ["design"]}
  ]
}
```

`isPlanningComplete` reports whether every non-skipped planning artifact exists;
skipped artifacts count as satisfied without being created. It does not report
whether implementation tasks are complete. `isComplete` is retained as a
compatibility alias with the same value.

Artifacts are listed in dependency order - a dependency never appears after
something that requires it - and artifacts that become ready at the same time
(spec-driven's `specs` and `design` both need only `proposal`) keep the order the
schema declares them rather than an alphabetical one. So the first `ready` entry
is the artifact to write next.

---

### `openspec instructions`

Get enriched instructions for creating an artifact or applying tasks. Used by AI agents to understand what to create next.

```
openspec instructions [artifact] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `artifact` | No | Artifact ID, or workflow input surface: `apply` or `archive` |

**Options:**

| Option | Description |
|--------|-------------|
| `--change <id>` | Change name (required in non-interactive mode) |
| `--schema <name>` | Schema override |
| `--json` | Output as JSON |

**Special cases:** Use `apply` to get task implementation instructions. Use
`archive` to fetch current, read-only archive inputs (`context` and
`operationGuidance`) for a valid change; it does not archive or mutate anything.

**Examples:**

```bash
# Get instructions for next artifact
openspec instructions --change add-dark-mode

# Get specific artifact instructions
openspec instructions design --change add-dark-mode

# Get apply/implementation instructions
openspec instructions apply --change add-dark-mode

# Get current archive operation inputs without archiving
openspec instructions archive --change add-dark-mode --json

# JSON for agent consumption
openspec instructions design --change add-dark-mode --json
```

**Output includes:**

- Template content for the artifact
- Project context from config
- Content from dependency artifacts
- Per-artifact rules from config
- Current project context and matching operation guidance for `apply`/`archive`

Operation inputs are read from the resolved repo or selected store on every
invocation. Project context is a required prompt-level input: agents read it and
apply relevant project facts, conventions, and constraints. Operation guidance is
optional additive advice: agents consider every entry and follow only entries that
are applicable and compatible with the built-in workflow. Both fields remain
separate from explicit user choices, CLI-controlled state, built-in instructions,
and artifact rules. Conflicting context is reported; conflicting or inapplicable
guidance is not followed and the reason is explained. These are behavioral
contracts for generated agents, not enforceable CLI checks. `instructions archive`
returns only the selected change, optional inputs, and root metadata; it does not
include the static archive workflow.

For an artifact skipped via `skip_specs: true`, the output is a warning only (JSON adds `skipped`/`warning` fields) — the artifact must not be created.

---

### `openspec templates`

Show resolved template paths for all artifacts in a schema.

```
openspec templates [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--schema <name>` | Schema to inspect (default: `spec-driven`) |
| `--json` | Output as JSON |

**Examples:**

```bash
# Show template paths for default schema
openspec templates

# Show templates for custom schema
openspec templates --schema my-workflow

# JSON for programmatic use
openspec templates --json
```

**Output (text):**

```
Schema: spec-driven

Templates:
  proposal  → ~/.openspec/schemas/spec-driven/templates/proposal.md
  specs     → ~/.openspec/schemas/spec-driven/templates/specs.md
  design    → ~/.openspec/schemas/spec-driven/templates/design.md
  tasks     → ~/.openspec/schemas/spec-driven/templates/tasks.md
```

---

### `openspec schemas`

List available workflow schemas with their descriptions and artifact flows.

```
openspec schemas [options]
```

**Options:**

| Option | Description |
|--------|-------------|
| `--json` | Output as JSON |
| `--store <id>` | Use a registered store as the OpenSpec root |

**Example:**

```bash
openspec schemas
```

**Output:**

```
Available schemas:

  spec-driven (package)
    The default spec-driven development workflow
    Flow: proposal → specs → design → tasks

  my-custom (project)
    Custom workflow for this project
    Flow: research → proposal → tasks
```

---

## Schema Commands

Commands for creating and managing custom workflow schemas.

### `openspec schema init`

Create a new project-local schema.

```
openspec schema init <name> [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `name` | Yes | Schema name (kebab-case) |

**Options:**

| Option | Description |
|--------|-------------|
| `--description <text>` | Schema description |
| `--artifacts <list>` | Comma-separated artifact IDs (default: `proposal,specs,design,tasks`) |
| `--default` | Set as project default schema |
| `--no-default` | Don't prompt to set as default |
| `--force` | Overwrite existing schema |
| `--json` | Output as JSON |

**Examples:**

```bash
# Interactive schema creation
openspec schema init research-first

# Non-interactive with specific artifacts
openspec schema init rapid \
  --description "Rapid iteration workflow" \
  --artifacts "proposal,tasks" \
  --default
```

**What it creates:**

```
openspec/schemas/<name>/
├── schema.yaml           # Schema definition
└── templates/
    ├── proposal.md       # Template for each artifact
    ├── specs.md
    ├── design.md
    └── tasks.md
```

---

### `openspec schema fork`

Copy an existing schema to your project for customization.

```
openspec schema fork <source> [name] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `source` | Yes | Schema to copy |
| `name` | No | New schema name (default: `<source>-custom`) |

**Options:**

| Option | Description |
|--------|-------------|
| `--force` | Overwrite existing destination |
| `--json` | Output as JSON |

**Example:**

```bash
# Fork the built-in spec-driven schema
openspec schema fork spec-driven my-workflow
```

---

### `openspec schema validate`

Validate a schema's structure and templates.

```
openspec schema validate [name] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `name` | No | Schema to validate (validates all if omitted) |

**Options:**

| Option | Description |
|--------|-------------|
| `--verbose` | Show detailed validation steps |
| `--json` | Output as JSON |

**Example:**

```bash
# Validate a specific schema
openspec schema validate my-workflow

# Validate all schemas
openspec schema validate
```

---

### `openspec schema which`

Show where a schema resolves from (useful for debugging precedence).

```
openspec schema which [name] [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `name` | No | Schema name |

**Options:**

| Option | Description |
|--------|-------------|
| `--all` | List all schemas with their sources |
| `--json` | Output as JSON |

**Example:**

```bash
# Check where a schema comes from
openspec schema which spec-driven
```

**Output:**

```
spec-driven resolves from: package
  Source: /usr/local/lib/node_modules/@fission-ai/openspec/schemas/spec-driven
```

**Schema precedence:**

1. Project: `openspec/schemas/<name>/`
2. User: `~/.local/share/openspec/schemas/<name>/`
3. Package: Built-in schemas

---

## Configuration Commands

### `openspec config`

View and modify global OpenSpec configuration.

```
openspec config <subcommand> [options]
```

**Subcommands:**

| Subcommand | Description |
|------------|-------------|
| `path` | Show config file location |
| `list` | Show all current settings |
| `get <key>` | Get a specific value |
| `set <key> <value>` | Set a value |
| `unset <key>` | Remove a key |
| `reset` | Reset to defaults |
| `edit` | Open in `$EDITOR` |
| `profile [preset]` | Configure workflow profile interactively or via preset |

**Examples:**

```bash
# Show config file path
openspec config path

# List all settings
openspec config list

# Get a specific value
openspec config get telemetry.enabled

# Set a value (disable anonymous usage telemetry)
openspec config set telemetry.enabled false

# Set a string value explicitly
openspec config set user.name "My Name" --string

# Remove a custom setting
openspec config unset user.name

# Set a machine-level default store (fallback root when no --store,
# local root, or project store: pointer resolves)
openspec config set defaultStore team-plans

# Reset all configuration
openspec config reset --all --yes

# Edit config in your editor
openspec config edit

# Configure profile with action-based wizard
openspec config profile

# Fast preset: switch workflows to core (keeps delivery mode)
openspec config profile core
```

**Telemetry opt-out:** `telemetry.enabled` defaults to on when unset (opt-out model).
Set it to `false` to disable anonymous usage stats and the `openspec update` version check.
Environment variables take precedence over config: `OPENSPEC_TELEMETRY=0`, `DO_NOT_TRACK=1`,
and a truthy `CI` value (e.g. `true`/`1`/`yes`) always disable telemetry regardless of the config value.

`openspec config profile` starts with a current-state summary, then lets you choose:
- Change delivery + workflows
- Change delivery only
- Change workflows only
- Keep current settings (exit)

If you keep current settings, no changes are written and no update prompt is shown.
If there are no config changes but the current project files are out of sync with your global profile/delivery, OpenSpec will show a warning and suggest `openspec update`.
Pressing `Ctrl+C` also cancels the flow cleanly (no stack trace) and exits with code `130`.
In the workflow checklist, `[x]` means the workflow is selected in global config. To apply those selections to project files, run `openspec update` (or choose `Apply changes to this project now?` when prompted inside a project).

**Interactive examples:**

```bash
# Delivery-only update
openspec config profile
# choose: Change delivery only
# choose delivery: Skills only

# Workflows-only update
openspec config profile
# choose: Change workflows only
# toggle workflows in the checklist, then confirm
```

---

## Utility Commands

### `openspec feedback`

Submit feedback about OpenSpec. Creates a GitHub issue.

```
openspec feedback <message> [options]
```

**Arguments:**

| Argument | Required | Description |
|----------|----------|-------------|
| `message` | Yes | Feedback summary; long text is shortened in the issue title and preserved in the body |

**Options:**

| Option | Description |
|--------|-------------|
| `--body <text>` | Additional details included after the summary |

**Requirements:** GitHub CLI (`gh`) must be installed and authenticated.

**Example:**

```bash
openspec feedback "Add support for custom artifact types" \
  --body "I'd like to define my own artifact types beyond the built-in ones."
```

---

### `openspec completion`

Manage shell completions for the OpenSpec CLI.

```
openspec completion <subcommand> [shell]
```

**Subcommands:**

| Subcommand | Description |
|------------|-------------|
| `generate [shell]` | Output completion script to stdout |
| `install [shell]` | Install completion for your shell |
| `uninstall [shell]` | Remove installed completions |

**Supported shells:** `bash`, `zsh`, `fish`, `powershell`

**Examples:**

```bash
# Install completions (auto-detects shell)
openspec completion install

# Install for specific shell
openspec completion install zsh

# Generate script for manual installation (bash)
openspec completion generate bash > ~/.bash_completion.d/openspec

# Uninstall
openspec completion uninstall
```

**Windows (PowerShell):** Install completions for the current PowerShell host:

```powershell
$env:PROFILE = $PROFILE
openspec completion install powershell
. $PROFILE
```

`$env:PROFILE` tells OpenSpec which profile to configure in this session. The
installer creates missing profile directories and adds a managed block that loads
`OpenSpecCompletion.ps1`. Reloading the profile enables completions immediately.

To uninstall from the current host, run:

```powershell
$env:PROFILE = $PROFILE
openspec completion uninstall powershell
```

Restart PowerShell after uninstalling to clear completions from the current session.

Completions are opt-in. The CLI mentions them once, on stderr, the first time you
run a command in an interactive terminal, and never again — it also stays quiet
if you already have completions installed. Set `OPENSPEC_NO_COMPLETIONS=1` to
suppress that tip entirely.

---

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success |
| `1` | Error (validation failure, missing files, etc.) |

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `OPENSPEC_TELEMETRY` | Set to `0` to disable telemetry and the `openspec update` version check (overrides `telemetry.enabled` in global config) |
| `DO_NOT_TRACK` | Set to `1` to disable telemetry and the `openspec update` version check (standard DNT signal; overrides config) |
| `OPENSPEC_CONCURRENCY` | Default concurrency for bulk validation (default: 6) |
| `EDITOR` or `VISUAL` | Editor for `openspec config edit` |
| `NO_COLOR` | Disable color output when set |
| `OPENSPEC_NO_ANIMATION` | Disable the `openspec init` welcome animation when set |
| `OPENSPEC_NO_COMPLETIONS` | Set to `1` to suppress the one-time tip about shell completions |
| `OPENSPEC_NO_UPDATE_CHECK` | Disable the `openspec update` check for a newer published CLI when set (any value, including empty). Also skipped when `CI` is set (unless `false`/`0`/`no`/`off`) or `NODE_ENV=test` |
| `npm_config_registry` | Registry the `openspec update` version check asks. Must be an `http(s)` URL or it falls back to `https://registry.npmjs.org`. No `.npmrc` file is read |

---

## Related Documentation

- [Commands](<OpenSpec AI 聊天命令参考.md>) - AI slash commands (`/opsx:propose`, `/opsx:apply`, etc.)
- [Workflows](<OpenSpec 快速与扩展工作流.md>) - Common patterns and when to use each command
- [Customization](<OpenSpec 项目配置与工作流定制.md>) - Create custom schemas and templates
- [Getting Started](<OpenSpec 入门与首个变更.md>) - First-time setup guide


</details>
