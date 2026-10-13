# 在 Qwen Code 中创建与管理 Skills

讲解个人、项目和扩展技能、SKILL.md 字段、调用控制、支持文件及自动生成维护，并说明规则和 Hook 配置。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-09-14](<https://github.com/QwenLM/qwen-code/commit/7e0beb9d18233b0395dd7c203cb2e60a88de30aa>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/users/features/skills.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/docs/users/features/skills.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent Skills

> 创建、管理和共享技能以扩展 Qwen Code 的功能。

本指南向您展示如何在**Qwen Code**中创建、使用和管理Agent Skills。技能是模块化功能，通过包含指令（以及可选的scripts/resources）的有组织的文件夹来扩展模型的有效性。

## 先决条件

- Qwen Code（最新版本）
- 基本熟悉 Qwen Code（[快速入门](<Qwen Code 快速开始.md>)）

## Agent Skills是什么？

Agent Skills 将专业知识融入可发现的功能中。每个技能都包含一个 `SKILL.md` 文件，其中包含模型可以在相关时加载的说明，以及可选的支持文件（例如脚本和模板）。

### 技能如何调用

技能是**模型调用的** - 模型根据您的请求和技能的描述自主决定何时使用它们。这与斜杠命令不同，斜杠命令是**用户调用的**（您显式键入`/command`）。

如果您想显式调用技能，请使用技能名称将其作为斜线命令键入：

```bash
/<skill-name>
```

开始输入 `/` 以自动完成并浏览可用技能及其描述。 `/skills` 命令打开技能面板，您可以在其中交互地浏览、搜索、切换和启动技能。

`<skill-name>` 始终是技能的注册名称。对于已安装扩展中的技能，其名称带有其所有者 - `rust:pdf`，而不是 `pdf` - 因此您输入 `/rust:pdf`。参见[扩展技能如何命名](<#扩展技能如何命名>)。

> **注意：** 如果您之前使用 `/skills <skill-name>` 运行技能，该语法现在只会打开技能面板并忽略尾随参数。使用`/<skill-name>`直接运行技能。

### 好处

- 为您的工作流程扩展 Qwen Code
- 通过 git 在团队中分享专业知识
- 减少重复提示
- 为复杂任务组合多种技能

## 创建技能

技能存储为包含 `SKILL.md` 文件的目录。

### 使用 `/learn` 生成项目技能

使用`/learn`将现有的知识源提炼成可重用的项目
技能：

```text
/learn https://docs.example.com/api
/learn ~/projects/acme-sdk
/learn Our deploy process: run migrate, deploy the service, then check health
```

该命令按照正常的 agent 回合运行，并在 
`.qwen/skills/learned-skill-<name>/SKILL.md` 下创建结果，其 
frontmatter 中包含 `source: learned`。在使用或共享之前查看生成的说明。

`/learn` 还接受本地或直接链接的 `.mp4`、`.webm`、`.mov` 和 `.m4v`
 视频。在路径或 URL 后添加文本，将生成的技能集中在教程的一部分 
 上：

```text
/learn ./tutorial.mp4 focus on the deployment workflow
```

视频学习需要 OpenAI 兼容的提供者上支持视频的模型。
YouTube 页面 URL 不是直接视频输入；将视频下载到 
 工作区并传递其本地路径。

### 个人技能

个人技能适用于您的所有项目。将它们存储在`~/.qwen/skills/`中：

```bash
mkdir -p ~/.qwen/skills/my-skill-name
```

使用个人技能：

- 您的个人工作流程和偏好
- 您正在发展的技能
- 个人生产力助手

### 项目技能

项目技能与您的团队共享。将它们存储在项目中的 `.qwen/skills/` 中：

```bash
mkdir -p .qwen/skills/my-skill-name
```

使用项目技能：

- 团队工作流程和惯例
- 项目特定的专业知识
- 共享实用程序和脚本

项目技能可以签入 git 并自动提供给团队成员。

### 维护自动生成的项目技能

Qwen Code 在本地跟踪生成的项目技能的成功使用，包括禁用新的自动技能生成时，因此重新启用维护不会将最近使用的技能误认为是非活动技能。当启用**自动技能**时，它会定期将不活动的生成技能移出活动库。仅管理名为 `.qwen/skills/auto-skill-*` 且 `SKILL.md` frontmatter 包含 `source: auto-skill` 的目录；个人、扩展、捆绑和手工创作的技能永远不会被选择。

- 在 30 天没有成功使用或 `SKILL.md` 编辑后，自动技能将被标记为过时。
- 90 天后，其完整目录将移动到 `.qwen/archived-skills/`。没有任何内容会被永久删除。
- 自动维护在受信任工作区中最多每 7 天运行一次。每个新观察到的自动技能在维护开始之前都会有一个完整的宽限期。
- 固定的自动技能将被排除在自动过时和存档转换之外，直到取消固定为止。
- 存档目录名称保持保留，现有存档目标仅跳过该冲突，而不是停止对其他技能的维护。

使用 `/curator` 查看活动、陈旧、存档和固定的自动技能。运行 `/curator run --dry-run` 预览维护阶段，运行 `/curator run` 立即应用它，运行 `/curator pin <directory>` 或 `/curator unpin <directory>` 控制每个技能的维护，或运行 `/curator restore <directory>` 将存档的自动技能移回到活动库中。

状态和试运行预览可在安全模式和不受信任的工作区中使用。应用维护、更换引脚和恢复存档的自动技能需要安全模式之外的可信工作空间。

## 写入`SKILL.md`

创建一个包含 YAML frontmatter 和 Markdown 内容的 `SKILL.md` 文件：

```yaml
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
priority: 10
---

# Your Skill Name

## Instructions
Provide clear, step-by-step guidance for Qwen Code.

## Examples
Show concrete examples of using this Skill.
```

### 现场要求

Qwen Code 目前验证：

- `name` 是匹配 `/^[\p{L}\p{N}_:.-]+$/u` 的非空字符串 - Unicode 字母和数字（CJK / 西里尔文 / 重音拉丁文 OK），加上 `_`、`:`、`.`、 `-`。空格、斜杠、括号和其他结构上不安全的字符在解析时会被拒绝。公认的 `:` 允许由扩展 (`rust:pdf`) 注册的技能和自己选择冒号的作者 (`rust:chat`，写在 `rust` 扩展内) 共享一个模式，因此注册名称中的冒号并不是所有者的证明 - 请参阅 [ 扩展技能是如何命名为](<#扩展技能如何命名>)。
- `description` 是一个非空字符串
- `priority` 是可选的。当存在时，它必须是有限数量。较高的值仅在 `/skills` 列表中排序靠前 - 斜杠命令完成（键入 `/`）和 `/help` 自定义命令视图保持字母顺序，因此高优先级技能永远不会重新排序内置命令。省略或无效的值将被视为未设置，其行为类似于 `0`。

推荐约定：

- 对于可共享名称，最好使用带连字符的小写 ASCII（例如 `tsx-helper`）
- 使`description`具体化：包括**技能的作用**和**何时**使用它（用户自然会提到的关键词）
- 对于应该可靠地出现在 `/skills` 中默认字母顺序之前的技能，请谨慎使用 `priority`。允许负优先级，并在未设置的技能下方排序。

### 可选：在文件路径上设置技能 (`paths:`)

对于仅对代码库特定部分重要的技能，请添加 `paths:` 全局模式列表。该技能将保留在模型的可用技能列表中，直到工具调用触及匹配文件为止：

```yaml
---
name: tsx-helper
description: React TSX component helper
paths:
  - 'src/**/*.tsx'
  - 'packages/*/src/**/*.tsx'
---
```

注意事项：

- Glob 相对于项目根与 [picomatch](<https://github.com/micromatch/picomatch>) 进行匹配；项目根目录之外的文件永远不会触发激活。
- 一旦触摸匹配的文件，路径门控技能 ** 将在会话的剩余时间 ** 中保持激活状态。新会话或通过编辑任何技能文件触发的 `refreshCache` 会重置激活。
- `paths:` 仅开启 ** 型号 ** 发现，并且仅在 SkillTool 列表级别。除非设置了 `user-invocable: false`，否则您始终可以通过 `/<skill-name>` 或 `/skills` 选择器自行调用路径门控技能 - 无论激活状态如何，该用户路径都会运行技能主体。然而，模型端保持门控状态，直到触及匹配文件：斜杠调用不会**而不是**解锁模型端激活，因此如果您希望模型链接您的调用（调用`Skill { skill: ... }`本身），也请首先访问与技能的`paths:`匹配的文件。
- 允许将 `paths:` 与 `disable-model-invocation: true` 组合，但门没有任何效果 - 无论如何，技能对模型都是隐藏的，因此路径激活永远不会公布它。

### 可选：控制用户和模型调用

默认情况下，技能是用户可调用的。要隐藏技能以防止直接使用斜线命令，同时保持其可用于模型调用，请设置 `user-invocable: false`：

```yaml
---
name: model-only-helper
description: Helper the model can call when appropriate
user-invocable: false
---
```

这将从 `/<skill-name>` 调用和 `/skills` 选择器结果中删除该技能。它不会向模型隐藏技能。

要从模型调用中隐藏技能，同时保持直接用户调用可用，请设置 `disable-model-invocation: true`：

```yaml
---
name: manual-helper
description: Helper you invoke manually
disable-model-invocation: true
---
```

您可以组合这两个字段，但无法通过普通用户或模型调用路径访问技能。

### 可选：确定性地强制执行规则 (`hooks:`)

`SKILL.md` 正文中的所有内容都是对模型的指令：它是提示文本，因此遵循它取决于模型。当无论模型决定什么规则都必须保持时——除非注入所需的值，否则拒绝运行，永远不要触及受保护的路径——而是在 frontmatter 中声明一个 [hook](<Qwen Code Hooks 配置指南.md>)。挂钩作为代码运行，因此它们不依赖于模型的合作：

```yaml
---
name: gated-skill
description: Calls the downstream CLI using a runtime-injected session ID
hooks:
  PreToolUse:
    - matcher: run_shell_command
      hooks:
        - type: command
          command: '"$QWEN_SKILL_ROOT/scripts/gate-session-id.sh"'
---
```

`$QWEN_SKILL_ROOT` 设置为技能自己的目录，因此挂钩命令可以引用与 `SKILL.md` 一起提供的文件。命令字符串被传递给 shell，因此 ** 保留内部引号 **：不带引号，包含空格的项目路径会分成两个单词，并且门永远不会运行。 **也使脚本可执行** (`chmod +x`)。这两个错误都以相同的方式失败：工具调用继续进行，并且记录或日志中没有显示任何内容表明门未运行。当 `PreToolUse` 钩子以代码 `2` 退出时（stderr 作为原因反馈给模型）或打印 `hookSpecificOutput.permissionDecision: "deny"` 时，会阻止工具调用：

```bash
#!/usr/bin/env bash
if [ -z "${DOWNSTREAM_SESSION_ID:-}" ]; then
  echo "Required input DOWNSTREAM_SESSION_ID is not available. Cannot proceed." >&2
  exit 2
fi
exit 0
```

注意事项：

- 钩子在调用技能时注册，并持续到会话的其余部分。这在两个调用路径上都是如此 — 无论是模型调用技能还是您键入 `/<skill-name>`。
- 使用 `--continue` / `--resume` 恢复会话会重新应用通过技能工具加载的 ** 模型 ** 的每个技能的两半 — `allowedTools` 允许规则和 `hooks:`，因为这些调用会作为工具记录在对话中来电。授权仅持续到恢复的会话，与实时调用相同。您自己开始使用的技能`/<skill-name>`作为普通提示提交，并且不留下任何工具调用记录，因此不会恢复 - 在恢复重新布防其门后重新运行`/<skill-name>`。
- 对于今天新的工具调用会拒绝的技能，恢复不会重新应用它们（已禁用，对带有 `disable-model-invocation` 的模型隐藏，或尚未激活的 `paths:` 技能），对于 ** 项目 ** 技能，其文件夹不再受信任，或者当记录的正文与中的正文不匹配时`SKILL.md` - 因为它已被编辑，或者因为它太长并且被模型截断。在这些情况下，声明 `hooks:` 或 `allowedTools` 的技能会留下调试日志行说明原因。恢复还会跳过从 `exec` 脚本内部加载的技能和不再发现的技能（例如，已删除或重命名的技能），而无需日志行。匹配仅覆盖正文，因此仅对前部内容进行了编辑，并使用新的 `allowedTools` 和 `hooks:` 重新应用。
- 注册是幂等的：重新调用技能不会堆叠重复的钩子。
- 技能挂钩的 `matcher:` 遵循与设置挂钩相同的规则（请参阅 [ 挂钩 ](<Qwen Code Hooks 配置指南.md>)）。省略或空的 `matcher:` 匹配每个工具，并且匹配器未锚定，因此 `edit` 也匹配 `notebook_edit`。技能挂钩用于将其匹配器包装在 `^…$` 中，并且在省略时不匹配任何内容：编写 `^edit$` 以精确匹配一个工具，并将排除项编写为 `^(?!write_file).*$`。
- `command:` 通过平台 shell 运行：macOS 和 Linux 上的 `bash`，以及在 Windows Git Bash 上检测到它时 (`MSYSTEM`/`TERM`)，否则为 `cmd.exe` 或 PowerShell。上面的示例是 POSIX shell — 在 `cmd.exe` 下，`$QWEN_SKILL_ROOT` 未展开，并且 `.sh` 脚本不可执行，因此此处的门无法打开。钩子可以设置 `shell: bash` 来强制 bash，但这会解析为 `PATH` 上的任何 `bash`，因此在 Git Bash 之外的 Windows 上为您实际拥有的 shell 编写门。
- 禁用挂钩的会话不会注册其中任何一个 - `disableAllHooks`、安全模式和 ACP 客户端的 `skipHooks`。技能的主体及其 `allowedTools` 仍然适用于这些会话，但其门不适用，因此您依赖挂钩来执行的规则不会在那里执行。裸模式更进一步：根本没有发现任何技能，因此没有身体，也没有`allowedTools`。
- **项目**技能的钩子运行存储库提供的命令，因此它们仅在受信任的文件夹中注册，并且每次钩子触发和每次决定权限时都会重新读取信任。连接了 IDE 同伴后，该值是实时的：撤销信任会在下一次工具调用时使已注册的门保持沉默，并暂停技能的 `allowedTools`，而无需重新启动。如果没有 IDE 连接，则该值在 CLI 启动时固定，因此通过 CLI 自身的信任对话框所做的更改将在重新启动时生效。授予信任永远不会追溯：再次调用技能。
- `hooks:` 是针对项目、用户和捆绑技能而读取的。扩展提供的技能不支持；请改用扩展程序自己的清单级挂钩。
- 有关完整的事件列表、匹配器语法和输出格式，请参阅 [Hooks](<Qwen Code Hooks 配置指南.md>)。

## 添加支持文件

在 `SKILL.md` 旁边创建其他文件：

```text
my-skill/
├── SKILL.md (required)
├── reference.md (optional documentation)
├── examples.md (optional examples)
├── scripts/
│   └── helper.py (optional utility)
└── templates/
    └── template.txt (optional template)
```

参考 `SKILL.md` 中的这些文件：

````markdown
For advanced usage, see [reference.md](reference.md).

Run the helper script:

```bash
python scripts/helper.py input.txt
```
````

## 查看可用技能

Qwen Code 发现技能：

- 个人技能：`~/.qwen/skills/`
- 项目技能：`.qwen/skills/`
- 扩展技能：安装的扩展提供的技能
- 捆绑技能：Qwen Code附带的技能

### 延伸技能

扩展可以提供启用扩展后可用的自定义技能。这些技能存储在扩展程序的 `skills/` 目录中，并遵循与个人和项目技能相同的格式。

安装并启用扩展时，会自动发现并加载扩展技能。

要查看哪些扩展提供技能，请检查扩展的 `qwen-extension.json` 文件中的 `skills` 字段。

#### 扩展技能如何命名

Qwen Code 将已安装扩展中的技能注册为 `<extensionName>:<name>`，其中 `<extensionName>` 是该扩展的 `qwen-extension.json` 的 `name` 字段，`<name>` 是技能自己的 frontmatter `name`。 `rust` 扩展中名为 `pdf` 的技能注册为 `rust:pdf`。

前缀是在技能加载时添加的，而不是写入文件中：您的 `SKILL.md` 保留您创作的名称，并且 Qwen Code 永远不会通过拆分注册的名称来恢复创作的名称（作者可以合法地在 `rust` 中写入 `rust:chat`）。仅扩展技能带有前缀 - 个人、项目和捆绑技能保留您编写的单一拼写。

在任何引用该技能的地方都使用注册名称：

- 将其调用为 `/rust:pdf`。裸露的 `/pdf` 不是别名 - 扩展程序的技能只能通过其注册名称访问。
- 该模型将其称为 `Skill { skill: "rust:pdf" }`，与 `<available_skills>` 中读取的名称相同。
- 两个扩展，每个扩展都有一个名为 `pdf` 的技能，为您提供两种技能（`rust:pdf` 和 `docs-suite:pdf`），而不是一种获胜技能和一种消失技能。

您阅读和选择“技能”的表面也会命名所有者：“技能”面板（包括设置已锁定的行）、在交互式 UI 外部打印的只读列表 `/skills`（ACP 和其他非交互式模式 - 该命令以交互方式打开面板）以及 `/` 命令面板中的徽章，其内容为`[Extension: Rust]` 而不是裸露的 `[Extension]`。这些标签更喜欢扩展的 `displayName`，并在声明无时回退到其 `name`。

#### 扩展技能和`skills.*`设置

`skills.disabled`、`skills.defaultDisabled` 和 `slashCommands.disabled` 与 ** 或 ** 拼写下的扩展技能匹配，因此您在前缀存在之前编写的 `skills.disabled: ["pdf"]` 仍然隐藏 `rust:pdf`。限制只能移除能力，因此重命名技能不允许提升能力。

`skills.enabled` 是例外，也是现有设置文件的一个可见更改：它授予功能，因此它仅与注册名称匹配。 `skills.enabled: ["pdf"]` 不再单独选择扩展的 `pdf` — 写入 `skills.enabled: ["rust:pdf"]`。继续工作的一对裸露的一对是位于 `skills.defaultDisabled` 中的前缀选择加入，具有相同的拼写：取消比较条目本身，因此 `defaultDisabled: ["pdf"]` + `enabled: ["pdf"]` 取消条目 - 然后该技能最终根据为此工作区存储的启用启用，否则扩展程序自己的默认值；对于默认关闭的技能，请在`skills.enabled`中写入`rust:pdf`。

在技能面板中切换技能会写入注册名称并仅删除该条目，因此启用 `rust:pdf` 不会影响旧版 `disabled: ["pdf"]`。当旧条目位于更高的范围（系统默认值、用户或系统设置）时，面板会这样说并锁定该行，命名要编辑的范围，而不是提供无法移动它的切换。此工作区自己的设置中的旧条目以相同的方式锁定行，命名条目及其范围（`skills.disabled 'pdf' (Workspace)` 或 `skills.defaultDisabled 'pdf' (Workspace)`），以便您知道要编辑哪个文件中的哪个列表。

值得了解的两个限制：

- 跨级别优先级保持不变，并且仍然精确比较注册名称 (`project` > `user` > `extension` > `bundled`)，因此您编写为 `rust:pdf` 的个人或项目技能优先于扩展的 `pdf`。个人或项目技能与捆绑技能之间的裸名冲突同样仍然按该优先级解决，而不是按前缀解决。与自定义命令发生冲突的技能则不会 - 在斜线表面上，最后一个加载程序获胜，并且自定义命令在技能之后加载，因此 `/pdf` 运行自定义命令，同时技能对模型保持可用。
- 技能名称也用作文件名：技能从中读取其调用参数的文件将 `[A-Za-z0-9._-]` 之外的每个字符替换为 `_`，因此注册为 `rust:pdf` 的扩展技能和编写为 `rust_pdf` 的个人或项目技能都会解析为 `qwen-skill-args-rust_pdf.txt` 并共享一个参数文件。 （前缀很少与自身冲突 - `rust:rust_pdf` 变为 `rust_rust_pdf` - 但扩展名可能包含 `_`，因此 `rust_pdf:x` 和 `rust:pdf_x` 折叠为相同的文件名。）非 ASCII 字母以相同的方式折叠，因此创作的`café` 和作者的 `caf_` 也落在 `caf_` 上——这是一个早于前缀的限制，这只会让它更容易被击中。避免使用将`:`变成`_`的另一个名称的技能名称。

要查看可用技能，请直接询问Qwen Code：

```text
What Skills are available?
```

> **注意 - 模型与用户视图。**仅询问模型表面模型当前可以看到的技能。如果技能使用 `paths:`（请参阅上面的“可选：在文件路径上控制技能”），则它会保留在该列表中，直到触及匹配的文件。 `/skills` 斜杠命令显示可以直接调用的技能； `user-invocable: false` 的技能在磁盘上仍然可见，并且可能仍然对模型可见。

或者使用斜杠命令浏览用户可调用列表（包括尚未激活的路径门控技能）：

```text
/skills
```

或者检查文件系统：

```bash
# List personal Skills
ls ~/.qwen/skills/

# List project Skills (if in a project directory)
ls .qwen/skills/

# View a specific Skill's content
cat ~/.qwen/skills/my-skill/SKILL.md
```

## 测试一项技能

创建技能后，通过提出与您的描述相符的问题来测试它。

示例：如果您的描述提到“PDF 文件”：

```text
Can you help me extract text from this PDF?
```

如果模型与请求匹配，则模型会自动决定使用您的技能 - 您无需显式调用它。

## 调试技能

如果 Qwen Code 不使用您的技能，请检查以下常见问题：

### 让描述具体化

太模糊了：

```yaml
description: Helps with documents
```

具体：

```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDFs, forms, or document extraction.
```

### 验证文件路径

- 个人技能：`~/.qwen/skills/<skill-name>/SKILL.md`
- 项目技能：`.qwen/skills/<skill-name>/SKILL.md`

```bash
# Personal
ls ~/.qwen/skills/my-skill/SKILL.md

# Project
ls .qwen/skills/my-skill/SKILL.md
```

### 检查 YAML 语法

无效的 YAML 会导致技能元数据无法正确加载。

```bash
cat SKILL.md | head -n 15
```

确保：

- 上线【`---`】上线【1】
- 在 Markdown 内容之前关闭 `---`
- 有效的 YAML 语法（无制表符，正确的缩进）

### 查看错误

以调试模式运行Qwen Code以查看技能加载错误：

```bash
qwen --debug
```

## 与您的团队分享技能

您可以通过项目存储库共享技能：

1. 在`.qwen/skills/`下添加技能
2. 提交并推动
3. 队友拉动更改

```bash
git add .qwen/skills/
git commit -m "Add team Skill for PDF processing"
git push
```

## 更新技能

直接编辑`SKILL.md`：

```bash
# Personal Skill
code ~/.qwen/skills/my-skill/SKILL.md

# Project Skill
code .qwen/skills/my-skill/SKILL.md
```

在正常会话期间，Qwen Code 监视个人和项目技能 
 目录。添加、编辑或删除技能会在短暂延迟后自动刷新技能列表和
调用状态。裸模式不会启动
这些观察者，因此重新启动Qwen Code以加载该模式下的技能更改。

## 移除技能

删除技能目录：

```bash
# Personal
rm -rf ~/.qwen/skills/my-skill

# Project
rm -rf .qwen/skills/my-skill
git commit -m "Remove unused Skill"
```

## 最佳实践

### 保持技能重点

一项技能应该涉及一种能力：

- 重点：“PDF 表单填写”、“Excel 分析”、“Git 提交消息”
- 太宽泛：“文档处理”（分成更小的技能）

### 写出清晰的描述

通过包含特定触发器来帮助模型发现何时使用技能：

```yaml
description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or .xlsx data.
```

### 与您的团队一起测试

- 技能是否会按预期激活？
- 说明清楚吗？
- 是否缺少示例或边缘情况？


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent Skills

> Create, manage, and share Skills to extend Qwen Code's capabilities.

This guide shows you how to create, use, and manage Agent Skills in **Qwen Code**. Skills are modular capabilities that extend the model's effectiveness through organized folders containing instructions (and optionally scripts/resources).

## Prerequisites

- Qwen Code (recent version)
- Basic familiarity with Qwen Code ([Quickstart](<Qwen Code 快速开始.md>))

## What are Agent Skills?

Agent Skills package expertise into discoverable capabilities. Each Skill consists of a `SKILL.md` file with instructions that the model can load when relevant, plus optional supporting files like scripts and templates.

### How Skills are invoked

Skills are **model-invoked** — the model autonomously decides when to use them based on your request and the Skill's description. This is different from slash commands, which are **user-invoked** (you explicitly type `/command`).

If you want to invoke a Skill explicitly, type it as a slash command using the Skill's name:

```bash
/<skill-name>
```

Start typing `/` to autocomplete and browse available Skills alongside their descriptions. The `/skills` command opens the Skills panel, where you can browse, search, toggle, and launch Skills interactively.

`<skill-name>` is always the Skill's registered name. For a Skill from an installed extension that name carries its owner — `rust:pdf`, not `pdf` — so you type `/rust:pdf`. See [How extension Skills are named](<#how-extension-skills-are-named>).

> **Note:** If you previously ran a Skill with `/skills <skill-name>`, that syntax now just opens the Skills panel and ignores the trailing argument. Use `/<skill-name>` to run a Skill directly.

### Benefits

- Extend Qwen Code for your workflows
- Share expertise across your team via git
- Reduce repetitive prompting
- Compose multiple Skills for complex tasks

## Create a Skill

Skills are stored as directories containing a `SKILL.md` file.

### Generate a project Skill with `/learn`

Use `/learn` to distill an existing knowledge source into a reusable project
Skill:

```text
/learn https://docs.example.com/api
/learn ~/projects/acme-sdk
/learn Our deploy process: run migrate, deploy the service, then check health
```

The command runs as a normal agent turn and creates the result under
`.qwen/skills/learned-skill-<name>/SKILL.md` with `source: learned` in its
frontmatter. Review the generated instructions before using or sharing them.

`/learn` also accepts local or direct-link `.mp4`, `.webm`, `.mov`, and `.m4v`
videos. Add text after the path or URL to focus the generated Skill on one part
of the tutorial:

```text
/learn ./tutorial.mp4 focus on the deployment workflow
```

Video learning requires a video-capable model on an OpenAI-compatible provider.
YouTube page URLs are not direct video input; download the video into the
workspace and pass its local path instead.

### Personal Skills

Personal Skills are available across all your projects. Store them in `~/.qwen/skills/`:

```bash
mkdir -p ~/.qwen/skills/my-skill-name
```

Use personal Skills for:

- Your individual workflows and preferences
- Skills you're developing
- Personal productivity helpers

### Project Skills

Project Skills are shared with your team. Store them in `.qwen/skills/` within your project:

```bash
mkdir -p .qwen/skills/my-skill-name
```

Use project Skills for:

- Team workflows and conventions
- Project-specific expertise
- Shared utilities and scripts

Project Skills can be checked into git and automatically become available to teammates.

### Maintain auto-generated project Skills

Qwen Code tracks successful uses of generated project Skills locally, including while new Auto Skill generation is disabled, so re-enabling maintenance cannot mistake a recently used skill for an inactive one. When **Auto Skill** is enabled, it periodically moves inactive generated Skills out of the active library. Only directories named `.qwen/skills/auto-skill-*` whose `SKILL.md` frontmatter contains `source: auto-skill` are managed; personal, extension, bundled, and hand-authored Skills are never selected.

- After 30 days without a successful use or `SKILL.md` edit, an auto-skill is marked stale.
- After 90 days, its complete directory is moved to `.qwen/archived-skills/`. Nothing is permanently deleted.
- Automatic maintenance runs at most once every 7 days in trusted workspaces. Each newly observed auto-skill gets a full grace period before maintenance begins.
- A pinned auto-skill is excluded from automatic stale and archive transitions until it is unpinned.
- Archived directory names remain reserved, and an existing archive destination skips only that collision rather than stopping maintenance for other skills.

Use `/curator` to see active, stale, archived, and pinned auto-skills. Run `/curator run --dry-run` to preview a maintenance pass, `/curator run` to apply it immediately, `/curator pin <directory>` or `/curator unpin <directory>` to control per-skill maintenance, or `/curator restore <directory>` to move an archived auto-skill back into the active library.

Status and dry-run previews are available in safe mode and untrusted workspaces. Applying maintenance, changing pins, and restoring archived auto-skills require a trusted workspace outside safe mode.

## Write `SKILL.md`

Create a `SKILL.md` file with YAML frontmatter and Markdown content:

```yaml
---
name: your-skill-name
description: Brief description of what this Skill does and when to use it
priority: 10
---

# Your Skill Name

## Instructions
Provide clear, step-by-step guidance for Qwen Code.

## Examples
Show concrete examples of using this Skill.
```

### Field requirements

Qwen Code currently validates that:

- `name` is a non-empty string matching `/^[\p{L}\p{N}_:.-]+$/u` — Unicode letters and digits (CJK / Cyrillic / accented Latin all OK), plus `_`, `:`, `.`, `-`. Whitespace, slashes, brackets and other structurally unsafe characters are rejected at parse time. The admitted `:` is what lets a Skill registered by an extension (`rust:pdf`) and an author who chooses a colon themselves (`rust:chat`, written inside the `rust` extension) share one pattern, so a colon in a registered name is not proof of an owner — see [How extension Skills are named](<#how-extension-skills-are-named>).
- `description` is a non-empty string
- `priority` is optional. When present, it must be a finite number. Higher values sort earlier in the `/skills` listing only — slash-command completion (typing `/`) and the `/help` custom commands view stay alphabetical, so a high-priority Skill never reorders built-in commands. Omitted or invalid values are treated as unset, which behaves like `0`.

Recommended conventions:

- Prefer lowercase ASCII with hyphens for shareable names (e.g. `tsx-helper`)
- Make `description` specific: include both **what** the Skill does and **when** to use it (key words users will naturally mention)
- Use `priority` sparingly for Skills that should reliably appear before the default alphabetical order in `/skills`. Negative priorities are allowed and sort below unset Skills.

### Optional: gate a Skill on file paths (`paths:`)

For Skills that only matter to specific parts of a codebase, add a `paths:` list of glob patterns. The Skill stays out of the model's available-skills listing until a tool call touches a matching file:

```yaml
---
name: tsx-helper
description: React TSX component helper
paths:
  - 'src/**/*.tsx'
  - 'packages/*/src/**/*.tsx'
---
```

Notes:

- Globs are matched relative to the project root with [picomatch](<https://github.com/micromatch/picomatch>); files outside the project root never trigger activation.
- A path-gated Skill **stays activated for the rest of the session** once a matching file is touched. A new session, or a `refreshCache` triggered by editing any Skill file, resets activations.
- `paths:` only gates **model** discovery, and only at the SkillTool listing level. Unless `user-invocable: false` is set, you can always invoke a path-gated Skill yourself via `/<skill-name>` or the `/skills` picker — that user path runs the Skill body regardless of activation state. The model side, however, stays gated until a matching file is touched: a slash invocation does **not** unlock model-side activation, so if you want the model to chain off your invocation (call `Skill { skill: ... }` itself), also access a file matching the skill's `paths:` first.
- Combining `paths:` with `disable-model-invocation: true` is allowed but the gate has no effect — the Skill is hidden from the model regardless, so path activation never advertises it.

### Optional: control user and model invocation

Skills are user-invocable by default. To hide a Skill from direct slash-command use while keeping it available for model invocation, set `user-invocable: false`:

```yaml
---
name: model-only-helper
description: Helper the model can call when appropriate
user-invocable: false
---
```

This removes the Skill from `/<skill-name>` invocation and `/skills` picker results. It does not hide the Skill from the model.

To hide a Skill from model invocation while keeping direct user invocation available, set `disable-model-invocation: true`:

```yaml
---
name: manual-helper
description: Helper you invoke manually
disable-model-invocation: true
---
```

You can combine both fields, but then the Skill is not reachable through the normal user or model invocation paths.

### Optional: enforce a rule deterministically (`hooks:`)

Everything in a `SKILL.md` body is an instruction to the model: it is prompt text, so following it depends on the model. When a rule must hold no matter what the model decides — refuse to run unless a required value was injected, never touch a protected path — declare a [hook](<Qwen Code Hooks 配置指南.md>) in the frontmatter instead. Hooks run as code, so they do not depend on the model's cooperation:

```yaml
---
name: gated-skill
description: Calls the downstream CLI using a runtime-injected session ID
hooks:
  PreToolUse:
    - matcher: run_shell_command
      hooks:
        - type: command
          command: '"$QWEN_SKILL_ROOT/scripts/gate-session-id.sh"'
---
```

`$QWEN_SKILL_ROOT` is set to the Skill's own directory, so hook commands can reference files shipped alongside `SKILL.md`. The command string is handed to a shell, so **keep the inner quotes**: unquoted, a project path containing a space splits into two words and the gate never runs. **Make the script executable** (`chmod +x`) too. Both mistakes fail open in the same way: the tool call proceeds, and nothing appears in the transcript or the log to say the gate did not run. A `PreToolUse` hook blocks the tool call when it exits with code `2` (stderr is fed back to the model as the reason), or when it prints `hookSpecificOutput.permissionDecision: "deny"`:

```bash
#!/usr/bin/env bash
if [ -z "${DOWNSTREAM_SESSION_ID:-}" ]; then
  echo "Required input DOWNSTREAM_SESSION_ID is not available. Cannot proceed." >&2
  exit 2
fi
exit 0
```

Notes:

- Hooks are registered when the Skill is invoked and last for the rest of the session. This is true on both invocation paths — whether the model calls the Skill or you type `/<skill-name>`.
- Resuming a session with `--continue` / `--resume` re-applies both halves — the `allowedTools` allow rules and the `hooks:` — of every Skill the **model** loaded through the Skill tool, because those invocations are recorded in the conversation as tool calls. The grants last for the resumed session only, the same as a live invocation. A Skill you started yourself with `/<skill-name>` is submitted as an ordinary prompt and leaves no tool-call record, so it is not restored — re-run `/<skill-name>` after resuming to re-arm its gate.
- Resume does not re-apply them for a Skill a fresh tool call would refuse today (disabled, hidden from the model with `disable-model-invocation`, or a `paths:` Skill not yet activated), for a **project** Skill whose folder is no longer trusted, or when the recorded body does not match the body in `SKILL.md` — because it was edited, or because it was too long and was truncated for the model. In these cases a Skill that declares `hooks:` or `allowedTools` leaves a debug log line saying why. Resume also skips, without a log line, a Skill loaded from inside an `exec` script and a Skill that is no longer discovered (for example, one that was removed or renamed). The match covers the body only, so an edit to just the frontmatter is re-applied with the new `allowedTools` and `hooks:`.
- Registration is idempotent: re-invoking a Skill does not stack duplicate hooks.
- A Skill hook's `matcher:` follows the same rules as a settings hook (see [Hooks](<Qwen Code Hooks 配置指南.md>)). An omitted or empty `matcher:` matches every tool, and matchers are not anchored, so `edit` also matches `notebook_edit`. Skill hooks used to wrap their matcher in `^…$` and to match nothing when it was omitted: write `^edit$` to match exactly one tool, and write exclusions as `^(?!write_file).*$`.
- The `command:` runs through the platform shell: `bash` on macOS and Linux, and on Windows Git Bash when it is detected (`MSYSTEM`/`TERM`), otherwise `cmd.exe` or PowerShell. The example above is POSIX shell — under `cmd.exe` `$QWEN_SKILL_ROOT` is not expanded and a `.sh` script is not executable, so the gate fails open there. A hook may set `shell: bash` to force bash, but that resolves to whatever `bash` is on `PATH`, so on Windows outside Git Bash write the gate for the shell you actually have.
- Sessions that disable hooks register none of them — `disableAllHooks`, safe mode, and an ACP client's `skipHooks`. The Skill's body and its `allowedTools` still apply in those sessions, but its gate does not, so a rule you rely on a hook to enforce is not enforced there. Bare mode goes further: no Skills are discovered at all, so there is no body and no `allowedTools` either.
- A **project** Skill's hooks run repo-supplied commands, so they are registered only in a trusted folder, and trust is re-read every time a hook fires and every time a permission is decided. With an IDE companion connected that value is live: revoking trust silences an already-registered gate — and suspends the Skill's `allowedTools` — at the next tool call, without a restart. Without an IDE connection the value is fixed when the CLI starts, so a change made through the CLI's own trust dialog takes effect on restart. Granting trust never retro-registers: invoke the Skill again.
- `hooks:` is read for project, user, and bundled Skills. Extension-provided Skills do not support it; use the extension's own manifest-level hooks instead.
- See [Hooks](<Qwen Code Hooks 配置指南.md>) for the full event list, matcher syntax, and output format.

## Add supporting files

Create additional files alongside `SKILL.md`:

```text
my-skill/
├── SKILL.md (required)
├── reference.md (optional documentation)
├── examples.md (optional examples)
├── scripts/
│   └── helper.py (optional utility)
└── templates/
    └── template.txt (optional template)
```

Reference these files from `SKILL.md`:

````markdown
For advanced usage, see [reference.md](reference.md).

Run the helper script:

```bash
python scripts/helper.py input.txt
```
````

## View available Skills

Qwen Code discovers Skills from:

- Personal Skills: `~/.qwen/skills/`
- Project Skills: `.qwen/skills/`
- Extension Skills: Skills provided by installed extensions
- Bundled Skills: Skills shipped with Qwen Code

### Extension Skills

Extensions can provide custom skills that become available when the extension is enabled. These skills are stored in the extension's `skills/` directory and follow the same format as personal and project skills.

Extension skills are automatically discovered and loaded when the extension is installed and enabled.

To see which extensions provide skills, check the extension's `qwen-extension.json` file for a `skills` field.

#### How extension Skills are named

Qwen Code registers a Skill from an installed extension as `<extensionName>:<name>`, where `<extensionName>` is the `name` field of that extension's `qwen-extension.json` and `<name>` is the Skill's own frontmatter `name`. A Skill named `pdf` in the `rust` extension is registered as `rust:pdf`.

The prefix is added while the Skill is loaded, not written into the file: your `SKILL.md` keeps the name you authored, and Qwen Code never recovers the authored name by splitting the registered one apart (an author may legitimately write `rust:chat` inside `rust`). Only extension Skills are prefixed — personal, project, and bundled Skills keep the single spelling you authored.

Use the registered name everywhere you refer to the Skill:

- Invoke it as `/rust:pdf`. The bare `/pdf` is not an alias — the extension's Skill is reachable only under its registered name.
- The model calls it as `Skill { skill: "rust:pdf" }`, the same name it reads in `<available_skills>`.
- Two extensions that each ship a Skill named `pdf` give you two Skills (`rust:pdf` and `docs-suite:pdf`) rather than one winning and one disappearing.

The surfaces where you read and choose Skills also name the owner: the Skills panel (including the rows a setting has locked), the read-only listing a bare `/skills` prints outside the interactive UI (ACP and other non-interactive modes — interactively the command opens the panel), and the badge in the `/` command palette, which reads `[Extension: Rust]` rather than a bare `[Extension]`. Those labels prefer the extension's `displayName` and fall back to its `name` when it declares none.

#### Extension Skills and the `skills.*` settings

`skills.disabled`, `skills.defaultDisabled`, and `slashCommands.disabled` match an extension Skill under **either** spelling, so a `skills.disabled: ["pdf"]` you wrote before the prefix existed still hides `rust:pdf`. A restriction can only remove capability, so renaming a Skill is not allowed to lift one.

`skills.enabled` is the exception, and the one visible change for an existing settings file: it grants capability, so it matches the registered name only. `skills.enabled: ["pdf"]` no longer opts an extension's `pdf` in on its own — write `skills.enabled: ["rust:pdf"]`. The one bare pair that keeps working is a pre-prefix opt-in sitting in `skills.defaultDisabled` with the same spelling: cancellation compares the entries themselves, so `defaultDisabled: ["pdf"]` + `enabled: ["pdf"]` cancels the entry — the skill then ends up enabled per the enablement stored for this workspace, else the extension's own default; for a default-off skill, write `rust:pdf` in `skills.enabled`.

Toggling a Skill in the Skills panel writes the registered name and removes only that entry, so enabling `rust:pdf` leaves a legacy `disabled: ["pdf"]` untouched. When that legacy entry sits in a higher scope — system defaults, user, or system settings — the panel says so and locks the row, naming the scope to edit rather than offering a toggle that cannot move it. A legacy entry in this workspace's own settings locks the row the same way, naming the entry and its scope (`skills.disabled 'pdf' (Workspace)` or `skills.defaultDisabled 'pdf' (Workspace)`) so you know which list in which file to edit.

Two limits worth knowing:

- Cross-level precedence is unchanged and still compares registered names exactly (`project` > `user` > `extension` > `bundled`), so a personal or project Skill you author as `rust:pdf` outranks the extension's `pdf`. Bare-name collisions between a personal or project Skill and a bundled Skill are likewise still settled by that precedence, not by the prefix. A Skill that collides with a custom command is not — on the slash surface the last loader wins, and custom commands load after Skills, so `/pdf` runs the custom command while the Skill stays available to the model.
- Skill names are also used as filenames: the file a Skill reads its invocation arguments from replaces every character outside `[A-Za-z0-9._-]` with `_`, so an extension Skill registered as `rust:pdf` and a personal or project Skill authored `rust_pdf` both resolve to `qwen-skill-args-rust_pdf.txt` and share one arguments file. (The prefix rarely collides with itself — `rust:rust_pdf` becomes `rust_rust_pdf` — but extension names may contain `_`, so `rust_pdf:x` and `rust:pdf_x` fold to the same filename.) Non-ASCII letters fold the same way, so an authored `café` and an authored `caf_` land on `caf_` too — a limitation that predates the prefix, which only makes it easier to hit. Avoid a Skill name that is another name with `:` turned into `_`.

To view available Skills, ask Qwen Code directly:

```text
What Skills are available?
```

> **Heads up — model vs. user view.** Asking the model only surfaces Skills the model can currently see. If a Skill uses `paths:` (see "Optional: gate a Skill on file paths" above), it stays out of that listing until a matching file has been touched. The `/skills` slash command shows Skills you can invoke directly; Skills with `user-invocable: false` remain visible on disk and may still be visible to the model.

Or browse the user-invocable list with the slash command (including path-gated Skills that have not activated yet):

```text
/skills
```

Or inspect the filesystem:

```bash
# List personal Skills
ls ~/.qwen/skills/

# List project Skills (if in a project directory)
ls .qwen/skills/

# View a specific Skill's content
cat ~/.qwen/skills/my-skill/SKILL.md
```

## Test a Skill

After creating a Skill, test it by asking questions that match your description.

Example: if your description mentions "PDF files":

```text
Can you help me extract text from this PDF?
```

The model autonomously decides to use your Skill if it matches the request — you don't need to explicitly invoke it.

## Debug a Skill

If Qwen Code doesn't use your Skill, check these common issues:

### Make the description specific

Too vague:

```yaml
description: Helps with documents
```

Specific:

```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDFs, forms, or document extraction.
```

### Verify file path

- Personal Skills: `~/.qwen/skills/<skill-name>/SKILL.md`
- Project Skills: `.qwen/skills/<skill-name>/SKILL.md`

```bash
# Personal
ls ~/.qwen/skills/my-skill/SKILL.md

# Project
ls .qwen/skills/my-skill/SKILL.md
```

### Check YAML syntax

Invalid YAML prevents the Skill metadata from loading correctly.

```bash
cat SKILL.md | head -n 15
```

Ensure:

- Opening `---` on line 1
- Closing `---` before Markdown content
- Valid YAML syntax (no tabs, correct indentation)

### View errors

Run Qwen Code with debug mode to see Skill loading errors:

```bash
qwen --debug
```

## Share Skills with your team

You can share Skills through project repositories:

1. Add the Skill under `.qwen/skills/`
2. Commit and push
3. Teammates pull the changes

```bash
git add .qwen/skills/
git commit -m "Add team Skill for PDF processing"
git push
```

## Update a Skill

Edit `SKILL.md` directly:

```bash
# Personal Skill
code ~/.qwen/skills/my-skill/SKILL.md

# Project Skill
code .qwen/skills/my-skill/SKILL.md
```

During a normal session, Qwen Code watches personal and project Skill
directories. Adding, editing, or removing a Skill refreshes the Skill list and
invocation state automatically after a short delay. Bare mode does not start
these watchers, so restart Qwen Code to load Skill changes in that mode.

## Remove a Skill

Delete the Skill directory:

```bash
# Personal
rm -rf ~/.qwen/skills/my-skill

# Project
rm -rf .qwen/skills/my-skill
git commit -m "Remove unused Skill"
```

## Best practices

### Keep Skills focused

One Skill should address one capability:

- Focused: "PDF form filling", "Excel analysis", "Git commit messages"
- Too broad: "Document processing" (split into smaller Skills)

### Write clear descriptions

Help the model discover when to use Skills by including specific triggers:

```yaml
description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or .xlsx data.
```

### Test with your team

- Does the Skill activate when expected?
- Are the instructions clear?
- Are there missing examples or edge cases?


</details>
