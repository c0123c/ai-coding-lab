# Mistral Vibe 官方仓库说明

介绍安装、Agent 与子任务、交互和程序模式、可信目录、终端、配置及工具扩展，提供产品操作入口。

- 来源：Mistral／Vibe
- 资料类型：存档文档
- 存档版本：`4ae5c5955da8`。
- 文档时间：源码提交（存档版）：[2026-10-09](<https://github.com/mistralai/mistral-vibe/commit/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/mistralai/mistral-vibe/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Mistral/Vibe/Mistral Vibe 官方仓库说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Mistral 氛围

[![PyPI 版本](<https://img.shields.io/pypi/v/mistral-vibe>)](<https://pypi.org/project/mistral-vibe>)
[![Python 版本](<https://img.shields.io/badge/python-3.12%2B-blue>)](<https://www.python.org/downloads/release/python-3120/>)
[![CI状态](<https://github.com/mistralai/mistral-vibe/actions/workflows/ci.yml/badge.svg>)](<https://github.com/mistralai/mistral-vibe/actions/workflows/ci.yml>)
[！[许可证](<https://img.shields.io/github/license/mistralai/mistral-vibe>)](<https://github.com/mistralai/mistral-vibe/blob/main/LICENSE>)

```
██████████████████░░
██████████████████░░
████  ██████  ████░░
████    ██    ████░░
████          ████░░
████  ██  ██  ████░░
██      ██      ██░░
██████████████████░░
██████████████████░░
```

**Mistral的开源CLI编码助手。**

Mistral Vibe 是由 Mistral 模型提供支持的命令行编码助手。它为您的代码库提供了一个对话界面，允许您通过一组强大的工具使用自然语言来探索、修改项目并与之交互。

> [！WARNING]
> Mistral Vibe 可在 Windows 上运行，但我们正式支持并面向 UNIX 环境。

### 一行安装（推荐）

**Linux 和 macOS**

```bash
curl -LsSf https://mistral.ai/vibe/install.sh | bash
```

**Windows**

首先，安装【uv】

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

然后，使用下面的uv命令。

### 使用uv

```bash
uv tool install mistral-vibe
```

### 使用pip

```bash
pip install mistral-vibe
```

## 目录

- [特点](<#特点>)
  - [内置 Agents](<#内置agents>)
  - [子代理和任务委派](<#子代理和任务委派>)
  - [交互式用户问题](<#互动用户问题>)
- [终端要求](<#终端要求>)
- [快速入门](<#快速入门>)
- [使用方法](<#用途>)
  - [交互模式](<#互动模式>)
  - [信任文件夹系统](<#信任文件夹系统>)
  - [编程模式](<#程序化模式>)
  - [TUI 实现](<#tui-实施>)
- [语音模式](<#语音模式>)
- [斜杠命令](<#斜线命令>)
  - [内置斜杠命令](<#内置斜杠命令>)
  - [通过技能自定义斜线命令](<#通过技能自定义斜线命令>)
- [技能系统](<#技能系统>)
  - [创造技能](<#创造技能>)
  - [技能发现](<#技能发现>)
  - [管理技巧](<#管理技巧>)
- [配置](<#配置>)
  - [配置文件位置](<#配置文件位置>)
  - [API 按键配置](<#api-按键配置>)
  - [OpenTelemetry 追踪](<#opentelemetry-追踪>)
  - [自定义系统提示](<#自定义系统提示>)
  - [自定义Agent配置](<#自定义-agent-配置>)
  - [工具管理](<#工具管理>)
  - [MCP服务器配置](<#mcp服务器配置>)
  - [会话管理](<#会话管理>)
  - [更新设置](<#更新设置>)
  - [自定义 Vibe 主目录](<#自定义-vibe-主目录>)
- [Editors/IDEs](<#editorsides>)
- [资源](<#资源>)
- [数据收集与使用](<#数据收集和使用>)
- [许可证](<#许可证>)

## 特点

- **交互式聊天**：对话式AIagent，可以理解您的请求并分解复杂的任务。
- **强大的工具集**：一套直接从聊天提示中进行文件操作、代码搜索、版本控制和命令执行的工具。
  - 读取、写入和修补文件（`read`、`write_file`、`edit`）。
  - 执行 shell 命令，并在部署期间使用托管 shell 会话、轮询和 stdin 帮助程序。
  - 使用 `grep` 递归搜索代码（支持 `ripgrep`）。
  - 管理 `todo` 列表以跟踪 agent 的工作。
  - 提出交互式问题以收集用户输入（`ask_user_question`）。
  - 将任务委派给子代理以进行并行工作 (`task`)。
- **项目感知上下文**：Vibe 自动向 agent 提供项目的位置、Git 分支和最近的提交历史记录，从而提高其对代码库的理解。
- **高级CLI体验**：使用现代库构建，实现平稳高效的工作流程。
  - 自动完成斜杠命令 (`/`) 和文件路径 (`@`)。
  - 通过 `@` 提及的图像附件 — `.png`、`.jpg`、`.jpeg`、`.gif`、`.webp` 文件被发送到具有视觉功能的模型（例如 Mistral Medium） 3.5）作为本机多模式内容。
  - 持久命令历史记录。
  - 美丽的主题。
- **高度可配置**：通过简单的`config.toml`文件自定义模型、提供程序、工具权限和UI首选项。
- **安全第一**：具有工具执行批准功能。
- **多个内置Agents**：从针对特定工作流程定制的不同agent配置文件中进行选择。

### 内置Agents

Vibe 附带多个内置 agent 配置文件，每个配置文件专为不同的用例而设计：

- **`ask`**：需要批准工具执行。
- **`plan`**：只读agent，用于探索和规划。自动批准安全工具，例如 `grep` 和 `read`。
- **`accept-edits`**：默认agent。仅自动批准文件编辑（`write_file`、`edit`）。对于代码重构很有用。
- **`auto-approve`**：自动批准所有工具执行。谨慎使用。

使用 `--agent` 标志选择不同的 agent：

```bash
vibe --agent plan
```

要更改未传递 `--agent` 时使用的默认 agent，请在 `config.toml` 中设置 
`default_agent`：

```toml
default_agent = "plan"
```

有效值为 `ask`、`plan`、`accept-edits`、`auto-approve`、
`lean`（仅当在 `installed_agents` 中列出时）或任何 
 自定义 agent 的名称文件位于`~/.vibe/agents/`或项目的`.vibe/agents/`
目录中。不接受 `explore` 等子代理。

> 注意：`default_agent` 适用于交互式和编程 
> (`-p` / `--prompt`) 会话。当运行应批准所有工具调用而不提示时，将 `--auto-approve` 或 `--yolo` 与任何 
> agent 一起传递。

### 子代理和任务委派

Vibe 支持子代理来委派任务。子代理独立运行，无需用户交互即可执行专门的工作，从而防止上下文过载。

`task` 工具允许 agent 将工作委派给子代理：

```
> Can you explore the codebase structure while I work on something else?

🤖 I'll use the task tool to delegate this to the explore subagent.

> task(task="Analyze the project structure and architecture", agent="explore")
```

通过将 `agent_type = "subagent"` 添加到您的 agent 配置来创建自定义子代理。 Vibe 附带一个名为 `explore` 的内置子代理，这是一个只读子代理，用于内部用于委派的代码库探索和技能加载。

当 Unified Harness 处于活动状态时，交互式提示会显示每个已知的 
 子代理的名称、类型、实时状态和最新测量的上下文大小。打开 
 子代理还会在提示上方显示其活动状态，并在 
 右下仪表中显示其上下文使用情况。对于空提示或本地编辑的提示，从其 
 最后一行按向下键可将焦点置于列表中；
 未发送的文本保留在提示中。使用向上和向下突出显示一行，然后按
Enter键打开它；用鼠标悬停以突出显示并单击以打开。选择 ** 主要 
 对话 ** 返回，或从子代理视图中按 Escape。 Ctrl+C 添加 
 仅限本地的错误样式用户消息，解释子代理无法 
 直接控制；返回Main并要求主agent停止一个。再次按 
Ctrl+C 退出 Vibe。 
打开主对话后，按其所在行的向上键以聚焦提示。当 
 子代理视图打开时，向上会在主行处停止。空闲子代理仍列为
**就绪**，因为主agent可以向它们发送更多指令。返回主窗口后，显式
停止的子代理会离开列表。第一个子代理
更新不添加本地信息消息。当子代理准备就绪时，
其记录会显示一条本地信息消息，解释如何为其指定
新目标或从主代理中停止它。在
`config.toml`中设置`show_subagent_status_list = false`，或者通过`/config`更改，可以隐藏该UI。

### 互动用户问题

`ask_user_question`工具允许agent在工作期间向您询问澄清问题。这可以实现更具交互性和协作性的工作流程。

```
> Can you help me refactor this function?

🤖 I need to understand your requirements better before proceeding.

> ask_user_question(questions=[{
    "question": "What's the main goal of this refactoring?",
    "options": [
        {"label": "Performance", "description": "Make it run faster"},
        {"label": "Readability", "description": "Make it easier to understand"},
        {"label": "Maintainability", "description": "Make it easier to modify"}
    ]
}])
```

agent 可以一次提出多个问题，显示为选项卡。每个问题都支持 2-4 选项以及用于自由文本响应的自动“其他”选项。

## 终端要求

Vibe 的交互界面需要现代终端仿真器。推荐的终端模拟器包括：

- **WezTerm**（跨平台）
- **Alacritty**（跨平台）
- **幽灵**（Linux和macOS）
- **Kitty**（Linux 和 macOS）

大多数现代终端应该可以工作，但较旧或最小的终端模拟器可能存在显示问题。

## 快速入门

1. 导航到项目的根目录：

   ```bash
   cd /path/to/your/project
   ```

2. 运行氛围：

   ```bash
   vibe
   ```

3. 如果这是您第一次运行 Vibe，它将：
   - 使用内置默认值而不创建配置文件，直到您
保存设置
   - 如果尚未配置，则提示您输入 API 密钥
   - 将您的API密钥保存到`~/.vibe/.env`以供将来使用

或者，您可以使用 `vibe --setup` 单独配置 API 密钥。

4. 开始与agent互动！

   ```
   > Can you find all instances of the word "TODO" in the project?

   🤖 The user wants to find all instances of "TODO". The `grep` tool is perfect for this. I will use it to search the current directory.

   > grep(pattern="TODO", path=".")

   ... (grep tool output) ...

   🤖 I found the following "TODO" comments in your project.
   ```

## 用途

### 互动模式

只需运行`vibe`即可进入交互式聊天循环。

- **多行输入**：按`Ctrl+J`或`Shift+Enter`选择端子插入换行符。
- **输入Cursor**：主输入cursor默认闪烁。在 `config.toml` 中设置 `cursor_blink = false` 以获得稳定的 cursor。无需通过 Python 和 Rust 终端中的 `/config` 或 `/reload` 重新启动即可应用更改。
- **文件路径**：使用 `@` 符号进行智能自动完成（例如，`> Read the file @src/agent.py`）在提示中引用文件。裸露的 `@` 快速列出即时非隐藏条目；在路径字符之后，Git 工作区会建议跟踪和不可忽略的文件。粘贴独立的现有绝对或相对于主目录的文件或文件夹也会创建提及。
- **Shell 命令**：在任何命令前面加上 `!` 前缀，可以直接在 shell 中执行它，绕过 agent（例如，`> !ls -l`）。
- **外部编辑器**：按`Ctrl+G`可在外部编辑器中编辑当前输入。
- **工具输出切换**：按`Ctrl+O`切换工具输出视图。
- **待办事项视图切换**：按`Ctrl+T`切换待办事项列表视图。
- **调试控制台**：按`Ctrl+\`切换调试控制台。
- **Agent 选择**：按`Shift+Tab` 循环选择agents（询问、计划...）。
- **排队**：agent工作时提交的提示由应用服务器排队。空 `Enter` 或 `Ctrl+Enter` 会将排队的提示引导到活动回合；在 Unified Harness 会话中，这会自动消耗存储的队列项。 `Ctrl+C` 删除最新的排队提示。 `Escape` 中断活动轮次并暂停剩余提示，`Enter` 恢复暂停的队列。 Shell 命令和非旁路斜杠命令需要空闲会话。
- **退出**：在输入框中键入`/exit`、`exit`、`quit`、`:q`或`:quit`，或按`Ctrl+C` / `Ctrl+D` 在 ~1 秒内两次。设置`ask_confirmation_on_exit = false`（或在`/config`中切换）使`Ctrl+D`在第一次按下时退出； `Ctrl+C` 始终需要确认。

### 复制和文本选择

- **复制**：使用`Ctrl+Y`或`Ctrl+Shift+C`将当前选择复制到剪贴板。启用自动复制（默认通过 `autocopy_to_clipboard = true`）时，鼠标选择会在释放时自动复制并显示简短的确认。
- **多击选择**：双击选择单词，三击选择段落。拖动以相同的粒度扩展选择范围。

您可以使用以下命令在提示符下启动 Vibe：

```bash
vibe "Refactor the main function in cli/main.py to be more modular."
```

### 信任文件夹系统

Vibe 包含信任文件夹系统，以确保您仅在信任的目录中运行 agent。当您首次在包含 `.vibe` 子文件夹的新目录中运行 Vibe 时，它​​可能会要求您确认是否信任该文件夹。

受信任的文件夹会被记住以供将来的会话使用。您可以通过其配置文件`~/.vibe/trusted_folders.toml`管理受信任的文件夹。在信任提示中，使用Up/Down或鼠标滚轮滚动检测到的文件列表。文本也可以在那里选择：双击选择一个单词，三次单击选择一个段落，并在列表边缘附近拖动滚动，同时扩展选择范围。

此安全功能有助于防止在敏感目录中意外执行。

### 程序化模式

您可以通过管道输入或使用 `--prompt` 标志以非交互方式运行 Vibe。这对于编写脚本很有用。

```bash
vibe --prompt "Refactor the main function in cli/main.py to be more modular."
```

默认情况下，它使用您配置的 `default_agent`（`accept-edits`，除非更改）。
要批准所有工具调用而不提示，请传递 `--auto-approve` 或 `--yolo`
（也可用于交互式会话）：

```bash
vibe --prompt "Refactor the main function in cli/main.py to be more modular." --auto-approve
```

#### 编程模式选项

使用`--prompt`时，您可以指定附加选项：

- **`--max-turns N`**：限制辅助最大转数。会话将在 N 轮后停止。
- **`--max-price DOLLARS`**：设置本次运行的最大成本限制（以美元为单位）。一旦成本超过此限制，回合就会中断，结果为`price_limit`。
- **`--max-tokens N`**：设置本次运行的最大累积 LLM 令牌预算，同时计算提示令牌和完成令牌。一旦使用量超过此限制，回合将中断，结果为`token_limit`。
- **`--agent NAME`**：为本次运行选择 agent 配置文件。
- **`--auto-approve`、`--yolo`**：批准所有工具调用而不提示，包括在交互式会话中。可以与任何 `--agent` 值组合。
- **`--enabled-tools TOOL`**：启用特定工具。在编程模式下，这会禁用所有其他工具。可以指定多次。支持精确名称、全局模式（例如，`bash*`）或带有 `re:` 前缀的正则表达式（例如，`re:^serena_.*$`）。
- **`--disabled-tools TOOL`**：在`--enabled-tools`过滤后禁用特定工具。可以指定多次。支持精确名称、全局模式（例如，`bash*`）或带有 `re:` 前缀的正则表达式（例如，`re:^serena_.*$`）。
- **`--output FORMAT`**：设置输出格式。选项：
  - `text`（默认）：人类可读的文本输出
  - `json`：所有消息末尾为JSON
  - `streaming`：每条消息以换行符分隔 JSON

在恢复的会话中，`text` 和 `streaming` 仅覆盖新的运行，因此在没有自己回复的情况下结束的运行不会打印任何内容； `json`主持整个会议。
- **`--prompt-file PATH`**：从文件而不是`--prompt`读取提示。如果使用 `-p` 并且没有文本，则从标准输入读取提示。
- **`--time-limit SECONDS`**：经过这么多秒后停止运行。活动转弯及其工具进程已停止，并且可以使用 `--resume` 恢复会话。 SIGTERM 以同样的方式停止运行。
- **`--output-dir DIR`**：写入 `DIR/export.json` 并将会话日志复制到 `DIR/session/`，无论运行以哪种方式结束。导出记录 Vibe 版本、有效配置（已编辑的标头和环境值）、令牌使用情况、成本、agent 进行的模型调用次数 (`steps`)、停止原因和结果。当会话存在配置问题（例如无法加载技能或配置文件）时，导出会将它们列在 `warnings` 下，每个都为 `{"message": str, "source": str | null}`，其中 `source` 命名其相关的文件或技能；当没有场地时，该场地就会被排除在外，并且无论如何，跑步都会继续进行。使用情况、成本和 `steps` 仅计算本次运行，也在恢复的会话中计算，而复制的日志保留整个会话。只有解析器本身拒绝的参数（未知标志、格式错误的值）会在不导出的情况下退出 1。

示例：

```bash
vibe --prompt "Analyze the codebase" --max-turns 5 --max-price 1.0 --max-tokens 50000 --output json
vibe --prompt-file task.md --auto-approve --time-limit 1800 --output-dir out/
```

#### 退出代码

|代码|`export.json` 的结果|含义|
| --- | --- | --- |
| `0` | `finished` |agent完成。|
| `1` | `usage_error`, `config_error` |运行无法按照要求开始。|
| `2` | `infrastructure_failure` |模型 API（重试后）或运行时失败。|
| `3` | `turn_limit`, `token_limit`, `price_limit`, `deadline`, `terminated`, `length`, `refusal` |agent未完成就停止：限制或模型拒绝；得分剩下的。 `length` 表示对话或最后一个答案达到了模型令牌限制：上下文窗口或输出令牌上限（模型上的 `max_output_tokens`）。|
| `4` | `aborted` |`--agent-socket` 服务器中止了运行（实验性的，见下文）； `export.json`中的`error.message`是服务器的消息。|

模型拒绝以`refusal`结束运行，退出`3`； `export.json`中的`error`的代码为`refusal`，其消息给出了模型发送时拒绝的类别和解释。只有旧版运行时 (`--legacy-harness`) 将拒绝报告为一。

#### Agent 插座（`--agent-socket`，实验性）

实验性：该选项对 `--help` 隐藏，并且它和下面的协议 
 可能会更改或消失，恕不另行通知。

`vibe -p --agent-socket PATH` 在主机上运行与 `vibe -p` 相同的会话，
 具有相同的导出、退出代码、限制和 SIGTERM 处理，连接到 Unix 套接字 `PATH` 上的 
agent 服务器。服务器服务于两个独立的
事物，其中之一或两者：

- **沙箱。**会话的文件和 shell 工具在其中运行，而不是在
该主机，并且会话在沙盒的工作区中启动（请参阅下面的“沙盒
会话”）。
- **工具。**服务器在握手中声明它们，并且会话提供
他们将其与 Vibe 自己的模型相邻；每个调用都会返回到服务器。 
 工具的 `modelAccess` 决定模型是否直接调用它，从 
 `run_typescript` 作为 `tools.<namespace>.<name>`，或两者兼而有之。 `--enabled-tools`和
 `--disabled-tools`仅过滤Vibe自己的工具。如果没有沙箱，
 会话将像普通的 `vibe -p` 一样在此主机上运行。

该选项需要`-p`或`--prompt-file`，且不能与
`--workdir`、`--worktree`、`--add-dir`、`--continue`或`--teleport`组合使用；这些是
使用错误（退出1）。 Windows 不支持它（退出 1）。

要使用新提示继续已完成的运行，请使用 
`--resume SESSION_ID` 开始新的运行，并从第一次运行的 
`export.json` 中的 `session_id` 中获取 ID。新的运行重做了握手，因此服务器可能是新的
并再次声明其工具；该模型会看到整个早期对话 
，然后是新提示。每个限制（`--max-turns`、`--time-limit`、
`--max-tokens`、`--max-price`）从新运行开始时开始计数。它的导出，在
下同`session_id`下，只计算自己的`steps`、`usage`和`cost_usd`；它的
`journal_dir` 占据整个会议，包括早期的运行。

##### 沙盒会话

当服务器提供沙箱服务时，将从沙箱中读取工作区上下文（git 元数据和 
`AGENTS.md` 文件）。会话存储、模型调用、MCP
服务器和挂钩保留在该主机上。后台进程在沙箱中运行，
 对于它们来说必须是 POSIX，如 `enable_background_processes` 所说。倒带
会话不会恢复任何文件，因为文件位于沙箱中。如果 
 沙箱失败，运行将以 `infrastructure_failure` 结束。

Vibe 的工具帮助程序可能会在正常工作的沙箱中崩溃：它在 
 工具调用过程中退出而不应答，例如由于分段错误或 
 内存不足终止。然后，模型读取一个工具错误，指出命令可能或
可能未运行，并带有退出代码和帮助程序打印的内容，然后轮
继续。连续第三次崩溃以 `infrastructure_failure` 结束运行； 
工具调用助手答案，即使出现错误，也会重置计数。共享沙箱的会话
，例如子代理，共享计数。根本无法
启动的助手（`python`没有解释器）会立即结束运行，模型工具调用之外的崩溃
也是如此，例如将技能复制到沙箱
或在那里保存大量输出时。

与在主机上一样，当模型读取文件时，它和工作区之间的 
 目录中的 `AGENTS.md` 文件将添加到它读取的内容中，每个 
 会话一次。查找它们需要每个目录执行一个 `sandbox/readFile` 请求，因此 
 会话会在每个目录中查找一次。当Vibe自己的`write_file`或
`search_replace`写入`AGENTS.md`时，它的目录会在其下面的
下一次读取时再次查找；一个 `bash` 命令，其文本名称 `AGENTS.md` 使每个 
 目录被再次查找。以任何其他方式出现的 `AGENTS.md`，对于来自脚本的 
 示例或 `git checkout`，在已查找的目录中是 
 在会话的其余部分中看不到。

文件工具遵循沙箱中的符号链接，就像在主机上一样。不过，批准
与写入的路径匹配，因为该主机无法看到沙箱的链接。因此，如果没有 `--auto-approve`，
 批准链接也允许其目标，并且工作区中指向其外部的链接 
 也算作工作区路径，例如 `accept-edits` 的 
。目标仍在沙箱中。使用
`--auto-approve`运行，如评估运行，无需请求批准，并且不受
影响。

太大而无法处理整个模型的工具输出将与普通 `vibe -p` 一样保留在 
 该主机上的会话中，并且还保存在沙箱临时目录下的所有者专用目录 
 中。该模型给出了沙箱路径，
，因此它使用沙箱工具读回输出。

此主机上仅加载用户的 Vibe 文件，并且 `--trust` 没有 
 效果。在项目自己的文件中，仅从沙箱中读取`AGENTS.md`文件和技能
（`.vibe/skills`和`.agents/skills`）。 
项目的`.vibe/`目录的其余部分（配置、agents、工具、钩子、插件和
提示）被忽略，而`vibe -p --trust`加载它：加载它会运行
该主机上的沙箱控件代码，例如钩子、MCP服务器和工具。
沙箱工作区的运行记录了警告。

模型使用沙盒工具读取技能，因此在此 
 主机（用户的、插件的，包括内置的）上读取的技能将被复制到工作区外部 `mistralai-vibe-skills/<digest>/`、
 下的 
 沙箱的临时目录中，并为模型指定这些路径。副本由
其文件摘要命名，在重用之前进行检查，并且仅在
缺失时上传，因此稍后在同一沙箱上运行时重用它。技能文件数超过 200
、文件数超过 256、KiB、除 `SKILL.md` 之外的文件数超过 1、MiB，或者 
`SKILL.md` 超过 2 MiB 没有被复制，并且运行没有它。 
 遗漏的每项技能（无论是因为复制失败还是因为复制失败）都会被记录并列在 
 会话的配置问题中，以及导出的 `warnings` 中。

##### 协议

调用者在 `0700` 目录中创建模式为 `0600` 的套接字，并且 
 在整个运行过程中为它提供服务。 Unix 套接字路径限制为 macOS
（Linux 上的 108）上的 104 字节，因此请保持简短，例如在 `/tmp` 下。

对于每个请求，Vibe 都会打开一个新连接，写入一行，读取一行
并关闭连接；并发请求使用并发连接。
也同时回答它们：请求的超时涵盖了它在其他请求之后等待的时间
，例如后台进程在长 shell
命令后面的轮询。一行是一个紧凑的 JSON-RPC 2.0 消息，其中包含 camelCase 字段，后跟 `\n`。
 回复可能携带 Vibe 不知道的字段，而它会忽略这些字段。

|方法|参数|结果|
| --- | --- | --- |
| `agent/initialize` | `{"protocolVersion": 2, "vibeVersion": str}` | `{"protocolVersion": 2, "workspace"?: str, "python"?: str, "tools"?: [ToolDefinition], "toolTimeoutSeconds"?: float, "instructions"?: str}` |
| `sandbox/execute` | `{"command": str, "cwd": str, "timeout": float \| null}` | `{"exitCode": int, "stdout": str, "stderr": str}` |
| `sandbox/readFile` | `{"path": str, "maxBytes": int}` | `{"contentBase64": str \| null}` |
| `tools/call` | `{"callbackId": str, "name": str, "input": any, "toolCallId": str}` | `{"output"?: any, "annotations"?: object, "error"?: {"message": str, "code"?: str, "details"?: any}}` |

- `agent/initialize` 在会话开始之前调用一次，并且必须
在 60 秒内回答。服务器必须
以协议版本2应答。 `workspace` 和 `python` 在一起，或者当服务器不提供沙箱时，
 根本不存在：`workspace` 是会话启动的绝对 
 沙箱路径，而 `python` 运行 
 的解释器Vibe 的工具助手（Python 3.9 或更高版本）。每个工具为
`{"namespace"?: str, "name": str, "description"?: str, "inputSchema"?: object, "outputSchema"?: object, "modelAccess"?: "direct" | "programmatic" | "both"}`；
`namespace`默认为`client`和`modelAccess`为`programmatic`。 
命名空间不能是`vibe`或`ui`，也不能是会话
具有的MCP服务器的名称，并且`<namespace>.<name>`被声明一次。 `toolTimeoutSeconds`，
正数，是`tools/call`可能需要多长时间；默认为 600。
 `instructions` 是会话的项目说明。系统提示符 
 将它们带到包含项目的 `AGENTS.md` 文件的位置，在 
 下具有相同的标题和相同的权重，后跟在 
 工作区中找到的任何 `AGENTS.md`。当 
 `include_project_context` 关闭时，它们会被忽略，就像 `AGENTS.md` 文件一样。如果没有套接字，相同的文本将进入工作区根部的 
 和 `AGENTS.md`，当 
 目录受信任 (`--trust`) 时，`vibe -p` 会读取该文本。
 如果握手失败，运行将结束为`infrastructure_failure`（退出 2）；
 如果它声明 Vibe 无法提供的工具，如 `usage_error`（退出 1），则在 
 调用模型之前。
- `sandbox/execute` 在 `cwd` 中运行 `command` 和 `sh`，使用沙箱自己的
环境。 Vibe 使用 `cwd` `/` 运行其工具帮助程序，并在命令本身中传递 
 会话的工作目录，因此删除 
 或移动该目录的模型会出现工具错误，就像在主机上一样。非零退出
是结果，而不是错误。 
 超过 `timeout` 秒的命令会以错误代码 `-32001` 进行应答：
 模型将其读取为超时命令，如在主机上一样，然后轮流转到 
。更快地停止命令的服务器，在其自身的限制下，在
中如此表示错误的可选`data`，如`{"timeoutSeconds": float}`，并且模型
被告知该限制；如果没有它，模型就没有限制。要停止 
 命令，服务器会终止它启动的进程（swerex 终止其进程 
 组）；命令启动的进程随之而来。 Vibe 等待 30
 秒过去 `timeout` 以获得回复；如果没有出现，它将关闭
连接并且沙箱失败。
- `sandbox/readFile` 最多返回 `maxBytes` 的常规文件，base64
编码，如果没有这样的文件，则为 `null`。
- `tools/call` 运行已声明工具的一次调用。 `callbackId` 是独一无二的
呼叫，`name` 是 `<namespace>.<name>`，`toolCallId` 模型将其称为 
 应答。 `output`
 是工具的结果，`error` 使调用成为工具错误。无论哪种方式，
 模型都会读取它并且回合继续。错误回复、格式错误的 
 回复、连接丢失或 `toolTimeoutSeconds` 内无回复也是如此：每一个都是 
 工具错误。较短的 `--time-limit` 会缩短匹配的等待时间。

对于沙箱请求，任何其他错误回复、格式错误的回复或关闭或无法打开的
连接都意味着沙箱失败：转向
失败，运行以`infrastructure_failure`结束（退出2）。退出非零的工具助手
命令是助手崩溃，而不是沙箱崩溃；请参阅
[沙盒会话](<#沙盒会话>)。

服务器可以根据任何请求自行结束运行，方法是使用 
 错误代码 `-32002` 和消息：`{"code": -32002, "message": str}` 进行应答。 Vibe
 像 SIGTERM 一样停止运行并退出 4，结果为 `aborted` 和 
 服务器的消息为 `export.json` 中的 `error.message`。 Vibe 赋予中止 no
 其自身的含义；该消息适用于阅读导出的任何人。

### TUI 实施

`vibe` 命令通过一个小型启动器启动，该启动器从 `VIBE_CLI` 环境变量中选择终端 
 客户端：

- `VIBE_CLI=rust vibe`启动RustTUI。
- `VIBE_CLI=python vibe` 运行旧版 Python（文本）TUI。
- 未设置 `VIBE_CLI`（或设置为任何其他值）时，`vibe` 运行旧版
  Python TUI.

上面的命令使用 POSIX shell 语法，其中赋值仅将 
 应用于该命令。在 Windows 上，改为设置会话变量：

- PowerShell：`$env:VIBE_CLI = "python"; vibe`（未设置
  `Remove-Item Env:VIBE_CLI`)
- cmd：`set VIBE_CLI=python`，然后运行`vibe`（用`set VIBE_CLI=`取消设置）

当 `VIBE_CLI` 环境变量设置为 `rust` 时，每个 `vibe`
 调用都会选择 Rust 客户端。要使用旧版 Python 客户端运行嵌套命令（例如 
`vibe mcp add ...`），请将该调用的 `VIBE_CLI` 设置为 
`python`。

## 语音模式

> [！WARNING]
> 语音模式是实验性的，可能会在未来的版本中发生变化。

语音模式允许您使用麦克风听写输入而不是打字。

### 激活语音模式

使用 `/voice` 斜杠命令打开或关闭语音模式：

```
> /voice
```

### 录音快捷键

|快捷方式|行动|
| -------- | ---------------- |
| `Ctrl+R` |开始录音|
|任意键|停止录音|
| `Escape` |取消录音|
| `Ctrl+C` |取消录音|

## 斜线命令

在会话期间使用斜杠命令进行元操作和配置更改。

### 内置斜杠命令

Vibe 提供了多个内置斜杠命令。通过在输入框中键入斜杠命令来使用它们：

```
> /help
```

如果模型响应因后端错误而中断，请使用 `/retry` 从部分响应继续 
。在命令后添加可选指导，例如
`/retry keep the conclusion concise`。

使用 `/mcp` 或 `/connectors` 浏览已配置的 MCP 服务器和工作区 
 连接器。浏览器从第一项开始；按向上或向左键聚焦其 
 模糊搜索栏，然后再次向上键换行到最后一项。

使用 Unified Harness，`/loop every two minutes check the build` 要求 
 模型安排重复提示。诸如“/loop工作日
上午 9 点查看CI”之类的日历请求也可以使用计算机的本地时区。要求模特
列出或取消日程； `/loop` 没有 TUI 管理子命令。繁忙时提交的请求
加入正常提示队列。时间表在恢复后继续存在，
但仅在 Vibe 打开且空闲时运行；错过的运行不会累积。
`cron`工具默认启用并允许；显式工具过滤器和
`tools.cron.permission`设置仍然适用。

### 通过技能自定义斜线命令

您可以通过技能系统定义自己的斜线命令。技能是可重复使用的组件，可扩展 Vibe 的功能。

要创建自定义斜杠命令：

1. 创建一个包含`SKILL.md`文件的技能目录
2. 在技能元数据中设置`user-invocable = true`
3. 定义你的技能中的命令逻辑

技能元数据示例：

```markdown
---
name: my-skill
description: My custom skill with slash commands
user-invocable: true
---
```

自定义斜杠命令与内置命令一起出现在自动完成菜单中。

## 技能系统

Vibe 的技能系统允许您通过可重用组件扩展功能。技能可以添加新工具、斜线命令和专门行为。

Vibe 遵循 [Agent Skills 技能格式和结构规范 ](<https://agentskills.io/specification>)。

### 创造技能

技能在具有 `SKILL.md` 文件的目录中定义，该文件包含 YAML frontmatter 中的元数据。例如，`~/.vibe/skills/code-review/SKILL.md`：

```markdown
---
name: code-review
description: Perform automated code reviews
license: MIT
compatibility: Python 3.12+
user-invocable: true
allowed-tools:
  - read
  - grep
  - ask_user_question
---

# Code Review Skill

This skill helps analyze code quality and suggest improvements.
```

默认情况下，用户和模型都可以调用技能。调用控制
是独立的：

- `user-invocable: false`隐藏`/`菜单中的技能并防止直接
`/skill-name` 调用，同时仍允许模型加载它。
- `disable-model-invocation: true` 遵循 Claude Code 约定并使得
仅显式技能：用户仍然可以调用 `/skill-name`，但模型 
 不会自动看到或调用它。
- 使用OpenAI的`agents/openai.yaml`约定的技能可以表达相同的
仅显式行为。无论活跃型号或提供商如何，Vibe 都会应用此政策：

  ```yaml
  policy:
    allow_implicit_invocation: false
  ```

如果策略元数据格式错误或包含未知的策略字段，
 Vibe 会报告问题并保持技能仅显式显示。

### 技能发现

Vibe 从多个位置发现技能：

1. **自定义路径**：通过`skill_paths`在`config.toml`中配置
2. **标准 Agent Skills 路径 **（项目根目录，仅限受信任文件夹）： `.agents/skills/` — [Agent Skills](<https://agentskills.io>) 标准
3. **本地项目技能**（项目根目录，仅限受信任文件夹）：项目中的`.vibe/skills/`
4. **全球技能目录**：`~/.vibe/skills/`和`~/.agents/skills/`

```toml
skill_paths = ["/path/to/custom/skills"]
```

### 管理技巧

使用配置中的模式启用或禁用技能：

```toml
# Enable specific skills
enabled_skills = ["code-review", "test-*"]

# Disable specific skills
disabled_skills = ["experimental-*"]
```

技能支持与工具相同的模式匹配（确切名称、全局模式和正则表达式）。

## 配置

### 配置文件位置

Vibe 通过 `config.toml` 文件进行配置。它首先在 `./.vibe/config.toml` 中查找该文件，然后回退到 `~/.vibe/config.toml`。

### 主题

当可以检测到时，默认的 `auto` 主题遵循终端背景，然后是操作系统 light/dark 首选项。使用 `/theme` 选择另一个主题或明确设置它：

```toml
theme = "dracula"
```

### API 按键配置

要使用 Vibe，您需要 Mistral API 密钥。您可以通过在 [https://console.mistral.ai](https://console.mistral.ai 注册获得一份。

您可以使用 `vibe --setup` 或通过以下方法之一配置您的 API 密钥。

Vibe 支持多种方式来配置 API 键：

1. **交互式设置（推荐首次使用的用户）**：当您第一次运行 Vibe 或您的 API 密钥丢失时，Vibe 将提示您输入该密钥。密钥将安全地保存到 `~/.vibe/.env` 以供将来的会话使用。

2. **环境变量**：将您的API密钥设置为环境变量：

   ```bash
   export MISTRAL_API_KEY="your_mistral_api_key"
   ```

3. **`.env`文件**：在`~/.vibe/`中创建一个`.env`文件并添加你的API密钥：

   ```bash
   MISTRAL_API_KEY=your_mistral_api_key
   ```

Vibe 在启动时自动从 `~/.vibe/.env` 加载 API 按键。如果同时设置了环境变量，则环境变量优先于 `.env` 文件。

**注意**：`.env`文件专门用于API密钥和其他提供商凭据。一般 Vibe 配置应在 `config.toml` 中完成。

### 自定义域

如果您使用 Mistral 兼容部署而不是默认的 `console.mistral.ai` / `api.mistral.ai`，则可以通过浏览器登录它。该凭证仍然是 Mistral API 密钥。

运行`vibe --setup`，选择**启动浏览器**，然后选择**其他**，输入您的登录域，然后通过浏览器登录。裸域以 `https://` 为前缀，身份验证 API 基础派生为 `DOMAIN/api`。覆盖的 `mistral` 提供程序将保存到您的用户配置中，以便后续运行重用它。

**注意**：向导读取`config.toml`中已设置的任何自定义`browser_auth_base_url`。选择 **其他** 会预先填充已配置的域，以便您可以确认或编辑它。配置自定义域时选择 **Mistral AI** 会首先向您发出警告 - 再次按 **Enter** 确认重置为默认域，然后保留该默认域。

### TLS 和企业证书颁发机构

默认情况下，Vibe 使用捆绑的 `certifi` 证书根来处理出站 HTTPS 请求。如果您的组织在操作系统信任存储中安装私有证书颁发机构，您可以选择加入 `config.toml` 中的系统信任存储：

```toml
enable_system_trust_store = true
```

`SSL_CERT_FILE` 和 `SSL_CERT_DIR` 仍受支持并作为附加信任锚加载。

### OpenTelemetry 追踪

Vibe 可以通过 OTLP/HTTP. 导出 agent、模型和工具操作的跟踪 在 `config.toml` 中启用跟踪：

```toml
enable_otel = true
```

默认情况下，Vibe 将跟踪发送到与配置的 Mistral 提供程序关联的遥测端点，并使用该提供程序的 API 密钥进行身份验证。 `enable_telemetry` 也必须保持启用状态。

要将跟踪发送到另一个收集器，请配置其基础 URL。 Vibe 附加 `/v1/traces`；需要时使用标准 `OTEL_EXPORTER_OTLP_*` 环境变量配置身份验证。

```toml
enable_otel = true
otel_endpoint = "https://collector.example.com:4318"
```

Span 属性在导出之前在客户端上进行编辑。默认模式会编辑敏感值，`strict` 会完全编辑敏感属性，`none` 会禁用编辑：

```toml
otel_redaction = "default" # "default", "strict", or "none"
```

仅当收集器被信任接收潜在敏感提示、响应和工具数据时才使用 `none`。

### 自定义系统提示

您可以创建 `AGENTS.md` 文件来添加自定义指令。您还可以替换整个系统提示符。

将 `AGENTS.md` 文件放入：
- `~/.vibe/AGENTS.md` — 所有项目的用户级说明
- 项目目录 - 项目特定的指令，从 cwd 加载到信任根

优先级：较近的目录优先于较远的目录。 `AGENTS.md` 中的说明会覆盖默认的系统提示。仅加载受信任文件夹的文件。

自定义系统提示完全替换默认提示（`prompts/cli.md`）。使用自定义提示内容在 `~/.vibe/prompts/` 目录中创建一个 markdown 文件。

要使用自定义系统提示符，请在配置中设置 `system_prompt_id` 以匹配文件名（不带 `.md` 扩展名）：

```toml
# Use a custom system prompt
system_prompt_id = "my_custom_prompt"
```

这将从 `~/.vibe/prompts/my_custom_prompt.md` 加载提示。

还支持 `.vibe/prompts/` 中的项目本地提示，并覆盖同名的用户级提示。这适用于所有自定义提示（系统、压缩和标题）。

### 自定义压缩提示

压缩默认使用 `prompts/compact.md` 处的内置提示。您可以使用与系统提示相同的解析规则将其替换为 `~/.vibe/prompts/`（或 `.vibe/prompts/`）中的自定义提示。

要使用自定义压缩提示，请在配置中设置 `compaction_prompt_id` 以匹配文件名（不带 `.md` 扩展名）：

```toml
# Use a custom compaction prompt
compaction_prompt_id = "my_compaction_prompt"
```

传递到 `/compact ...` 的任何额外指令都会附加在配置的压缩提示之后。

压缩保持相同的会话和可见的对话。稍后的模型请求
使用最新的压缩上下文，后跟更新的消息。

### 自定义标题提示

Vibe 在后台生成会话标题，并在 `prompts/session_title.md` 处显示内置提示。您可以使用与系统提示相同的解析规则将其替换为 `~/.vibe/prompts/`（或 `.vibe/prompts/`）中的自定义提示。

要使用自定义标题提示，请在配置中设置 `title_prompt_id` 以匹配文件名（不带 `.md` 扩展名）：

```toml
# Use a custom title prompt
title_prompt_id = "my_title_prompt"
```

配置的提示符为后台调用的系统提示符。 Vibe 仍然将会话记录作为用户消息发送，因此提示只需描述标题的编写方式（长度、语言、风格）。

### 自定义 Agent 配置

您可以通过在 `~/.vibe/agents/` 目录中添加 agent 特定的 TOML 文件来为特定用例（例如，红队、专门任务）创建自定义 agent 配置。

要使用自定义 agent，请使用 `--agent` 标志运行 Vibe：

```bash
vibe --agent my_custom_agent
```

Vibe 将在 agents 目录中查找名为 `my_custom_agent.toml` 的文件并应用其配置。

自定义 agent 配置示例 (`~/.vibe/agents/redteam.toml`)：

```toml
# Custom agent configuration for red-teaming
active_model = "mistral-medium-3.5"
system_prompt_id = "redteam"

# Disable some tools for this agent
disabled_tools = ["edit", "write_file"]

# Override tool permissions for this agent
[tools.bash]
permission = "always"

[tools.read]
permission = "always"
```

注意：这意味着您已经设置了一个名为 `~/.vibe/prompts/redteam.md` 的 redteam 提示符。

### 工具管理

内置壳表面由 `managed_shell_tools_enabled` 配置 
 字段和 `vibe_cli_managed_shell_tools` GrowthBook 实验控制。默认变体
保留旧版一次性`bash`工具，包括其现有的Windows行为。
托管变体公开OS-本机shell工具：
POSIX系统，包括WSL，其中Vibe作为Linux运行，得到托管`bash`、
`bash_output`、`bash_stdin`、`bash_sessions` 和 `bash_log_file`；当 Git Bash 可用时，本机 Windows
 获取 `git_bash`、`git_bash_output`、`git_bash_stdin`、`git_bash_sessions` 和 
`git_bash_log_file`。如果 Git Bash 不可用，
本机 Windows 会回退到 `powershell`、`powershell_output`、
`powershell_stdin`、`powershell_sessions` 和 `powershell_log_file`。

托管 shell 会话返回 `session_id`、内联输出、用于轮询 
 更多输出的 cursor，以及 `~/.vibe/shell-tool/sessions/` 下的日志路径。长时间运行的 
 命令可以通过 `background = true` 保持活动状态，而交互式命令可以通过匹配的 stdin 工具驱动 
。

POSIX `bash` 从 `[tools.bash]` 读取权限、允许列表和拒绝列表。
本机 Windows `git_bash` 从 `[tools.git_bash]` 读取它们；本机Windows
`powershell`从`[tools.powershell]`读取它们。 Windows 工具都不会读取 
`[tools.bash]`。当 Vibe 可以从 PATH、Git for Windows 或标准 Git 安装位置解析可用的 `bash.exe`
 时，首选 Git Bash。如果 Git Bash 
 不可用，则 PowerShell 解析顺序为 `pwsh.exe`，然后是 
`powershell.exe`。托管 Windows shell 工具不使用 `cmd.exe`。

```toml
[tools.git_bash]
permission = "ask"
shell = "C:\\Program Files\\Git\\bin\\bash.exe"

[tools.powershell]
permission = "ask"
shell = "powershell.exe"
```

转出分配是服务器管理的，不是 `config.toml` 选项。

`enable_background_processes = false` 在默认运行时将后台进程（`run_typescript` 中的 `tools.process`
）从 agent 中移走：其命令然后 
 在前台运行完成，并且系统提示符不再描述 
 进程工具。默认为 `true`。

`enable_subagents = false` 同样在默认运行时将子代理（
`run_typescript` 中的 `tools.subagent`）从 agent 中剔除：系统提示符 
 不再描述它们，并且 agent 单独工作。默认为 `true`。

#### Enable/Disable 带图案的工具

您可以使用 `enabled_tools` 和 `disabled_tools` 控制哪些工具处于活动状态。
这些字段支持精确名称、全局模式和正则表达式。
两者均设置时，`enabled_tools` 首先缩小工具集，然后 
`disabled_tools` 从该集中删除匹配的工具。

示例：

```toml
# Only enable tools that start with "serena_" (glob)
enabled_tools = ["serena_*"]

# Regex (prefix with re:) — matches full tool name (case-insensitive)
enabled_tools = ["re:^serena_.*$"]

# Disable a group with glob; everything else stays enabled
disabled_tools = ["mcp_*", "grep"]
```

注意事项：

- MCP 工具名称使用下划线，例如 `serena_list` 而不是 `serena.list`。
- 使用 fullmatch 将正则表达式模式与完整工具名称进行匹配。

### MCP服务器配置

您可以配置 MCP (Model Context Protocol) 服务器来扩展 Vibe 的功能。在 `mcp_servers` 部分下添加 MCP 服务器配置：

可以从 shell 以非交互方式添加远程 MCP 服务器。当提供`--api-key-env`或`--header`时，选择静态认证
；否则，
服务器使用OAuth并默认启动浏览器登录。

```bash
vibe mcp add mistralai \
  --url https://api.mistral.ai/mcp \
  --transport streamable-http \
  --api-key-env MISTRAL_API_KEY

vibe mcp add linear \
  --url https://mcp.linear.app/mcp

vibe mcp remove mistralai
```

使用 `--no-login` 在不启动登录的情况下保留 OAuth 服务器。静态认证
还支持可重复的`--header`、`--api-key-header`、`--api-key-format`、
`--startup-timeout-sec`和`--tool-timeout-sec`。运行`vibe mcp add --help`
以获得完整的命令参考。 `vibe mcp remove <name>` 从用户配置中删除服务器
。删除 OAuth 服务器还会删除其存储的 
 令牌、客户端信息和配置指纹（如果可用）。

将 `VIBE_CLI` 设置为 `rust` 时，shell `mcp add` 使用仅限 OAuth 的 
`/mcp add` 语法：`vibe mcp add https://mcp.linear.app/mcp --name Linear
--no-login`. It accepts `--scope` (repeatable), `--transport`，和
`--allow-insecure-http`；如果没有`--no-login`，则开始浏览器登录。 
`add` 和 `remove NAME` 都会更新用户配置，而无需打开聊天 
 会话。对于 stdio 或静态身份验证添加，请将 `VIBE_CLI` 设置为 `python` 并使用上面的标志运行 
`vibe mcp add`。

也可以从 Vibe 内部添加托管 OAuth MCP 服务器：

```text
/mcp add https://mcp.linear.app/mcp
/mcp add https://mcp.example.com/mcp --name docs --scope read --transport http --no-login
```

`/mcp add` 仅限 OAuth。它使用可选的 
 范围写入 `auth.type = "oauth"` 并默认启动登录。它使用`transport = "streamable-http"`
，除非您通过`--transport http`。通过`--no-login`添加服务器，无需
启动OAuth登录。该快捷方式支持`streamable-http`和`http`
传输。

```toml
# Example MCP server configurations
[[mcp_servers]]
name = "my_http_server"
transport = "http"
url = "http://localhost:8000"

[mcp_servers.auth]
type = "static"
headers = { "X-Client" = "vibe" }
api_key_env = "MY_API_KEY_ENV_VAR"
api_key_header = "Authorization"
api_key_format = "Bearer {token}"

[[mcp_servers]]
name = "my_streamable_server"
transport = "streamable-http"
url = "http://localhost:8001"

[mcp_servers.auth]
type = "static"
headers = { "X-Client" = "vibe" }

[[mcp_servers]]
name = "fetch_server"
transport = "stdio"
command = "uvx"
args = ["mcp-server-fetch"]
env = { "DEBUG" = "1", "LOG_LEVEL" = "info" }
```

支持的运输：

- `http`：标准HTTP运输
- `streamable-http`：HTTP 具有流支持的传输
- `stdio`：标准 input/output 传输（用于本地进程）

关键领域：

- `name`：服务器的短别名（用于工具名称）
- `transport`：运输类型
- `url`：用于HTTP传输的基础URL
- `headers`：附加HTTP标头
- `api_key_env`：包含API键的环境变量
- `command`：为 stdio 传输运行的命令
- `args`：stdio 传输的附加参数
- `startup_timeout_sec`：服务器启动和初始化的超时秒数（默认10秒）
- `tool_timeout_sec`：工具执行超时（以秒为单位）（默认 60 秒）
- `env`：为传输类型为 stdio 的 MCP 服务器设置的环境变量

HTTP MCP 服务器可以使用静态身份验证或 OAuth。两者都使用一个 `auth` 块；
遗留顶级 `api_key_env` / `headers` 密钥仍然被接受和推广
当 Vibe 加载配置时进行静态身份验证。

```toml
[[mcp_servers]]
name = "linear"
transport = "streamable-http"
url = "https://mcp.linear.app/mcp"

[mcp_servers.auth]
type = "oauth"
scopes = []
```

MCP 工具使用模式 `{server_name}_{tool_name}` 命名，并且可以像内置工具一样配置权限：

```toml
# Configure permissions for specific MCP tools
[tools.fetch_server_get]
permission = "always"

[tools.my_http_server_query]
permission = "ask"
```

MCP 服务器配置支持附加功能：

- **环境变量**：为MCP服务器设置环境变量
- **自定义超时**：配置启动和工具执行超时

环境变量和超时的示例：

```toml
[[mcp_servers]]
name = "my_server"
transport = "http"
url = "http://localhost:8000"
env = { "DEBUG" = "1", "LOG_LEVEL" = "info" }
startup_timeout_sec = 15
tool_timeout_sec = 120
```

### 挂钩

挂钩将任意 shell 命令连接到 Vibe 的生命周期中，以控制、审核或重写 agent 行为。不需要任何标志——声明一个钩子就足够了。

在 `<project>/.vibe/hooks.toml`（项目，首先加载；仅受信任）和 `~/.vibe/hooks.toml`（用户全局，第二个加载；`name` 的重复项丢失给项目条目）中声明：

```toml
[[hooks]]
name = "deny-rm-rf"
type = "pre_tool"
match = "bash"                       # tool-name matcher (fnmatch glob + `re:` regex escape, case-insensitive)
command = "uv run python /path/to/guard-bash"
timeout = 60.0                       # seconds; default 60 for all hooks
strict = false                       # tool hooks only: turn failures into denials (pre) / text-clears (post)
description = "Reject dangerous shell commands."
```

子代理继承父代理的钩子配置，因此策略可以传递应用。

#### 共同点

每个钩子都是通过 **stdin** (UTF-8) 上的 JSON 调用生成的，其中包含会话上下文：`session_id`、`parent_session_id`、`transcript_path`、 `cwd`，加上`hook_event_name`来区分钩子类型。工具挂钩添加特定于工具的字段（如下）。

每个钩子都通过其 ** 退出代码 ** 和 **stdout** 返回信号。 stdout 上的约定是严格的：要么为空（不执行任何操作），要么为与下面的模式匹配的 JSON 对象。使用 **stderr** 进行诊断/调试日志。

- **退出`0`，清空标准输出** — 直通。
- **退出 `0`，stdout** 上有效的 JSON 对象 — 结构化响应。通用顶级字段：
  - `system_message`（字符串，可选）— 在 UI 中向用户显示。
  - `decision`（`"allow"` | `"deny"`，可选，默认`"allow"`）——`"deny"`的效果取决于钩子类型。
  - `reason`（字符串，可选）- 伴随 `decision: "deny"`。
  - `hook_specific_output` 下的事件特定负载。
- **Exit `0`，非空但不合格的 stdout**（自由格式文本，损坏的 JSON，JSON scalar/array，架构不匹配） — 被视为挂钩失败，解析错误作为消息。默认警告；在工具挂钩上的 `strict = true` 下升级为拒绝/清除。
- **任何非零退出/超时/生成失败** — 相同的故障路径。从 stderr 获取诊断（返回到 stdout，然后是退出代码）。

每个级别都允许未知的 JSON 字段（向前兼容）。对于当前挂钩类型没有意义的字段将被默默忽略。

#### `post_agent`

在每个助理回合结束且没有挂起的工具调用后触发。

- **接收**（除了会话上下文）：没有额外的字段。
- **可以返回**：
  - `decision: "deny"` + `reason` — `reason` 作为要求重试的新用户消息注入。每个用户回合每个钩子的重试次数上限为 **3**；进一步的否认成为最后的警告。
  - 仅 `system_message` — UI-。

#### `pre_tool`

每次工具调用时触发，在 ** 用户权限提示之前 **。首先拒绝对该调用剩余的 `pre_tool` 挂钩进行短路。

- **接收**（除了会话上下文之外）：`tool_name`、`tool_call_id`、`tool_input`（模型的原始参数）。
- **可以返回**：
  - `decision: "deny"` + `reason` — 拒绝工具调用； `reason` 成为 LLM 看到的工具错误。
  - `hook_specific_output.tool_input`（对象）- 模型参数的 ** 完全替换 **。根据工具的模式重新验证（验证失败→综合拒绝）。跨钩子从左到右重写组合。重写的参数也是权限提示显示的内容、工具运行的内容以及后续 LLM 在助理消息中看到的内容。
  - 仅 `system_message` — UI-。

#### `post_tool`

当且仅当工具主体实际运行 ** 时，每次工具调用都会触发 **。 `tool_status` 是 `success`、`failure` 或 `cancelled`（在工具主体期间取消 - 取消被屏蔽，因此审核挂钩仍然运行）。当工具从未执行时不会触发：`pre_tool` 拒绝、用户在批准提示时拒绝、权限 `NEVER` 或正文启动之前取消。

- **接收**（除了会话上下文）：`tool_name`、`tool_call_id`、`tool_input`（重写后）、`tool_status`、`tool_output`（结构化结果字典；失败时为空）， `tool_output_text`（LLM将看到的运行文本，可以通过先前的钩子可变），`tool_error`，`duration_ms`。
- **可以返回**：
  - `decision: "deny"` + `reason` — 将 `tool_output_text` 替换为 `reason`。管道仍在继续；随后的钩子会看到替换。
  - `hook_specific_output.additional_context`（字符串）- **将**（带有`\n`分隔符）附加到`tool_output_text`。使用相同钩子拒绝进行组合：首先拒绝替换，然后将 `additional_context` 附加到替换内容。
  - 仅 `system_message` — UI-。

### 会话管理

#### 会话继续和恢复

Vibe 支持继续之前的会话：

- **`--continue`** 或 **`-c`**：来自最近保存的会话的 Continue
- **`--resume`**：打开交互式会话选择器
- **`--resume SESSION_ID`**：通过ID恢复特定会话（支持部分匹配）
- **`/resume`** 或 **`/continue`**：从 Vibe 内部打开会话选择器；按 `D` 两次可删除本地保存的会话。无法从此选择器中删除活动会话。

```bash
# Continue from last session
vibe --continue

# Open session picker
vibe --resume

# Resume specific session
vibe --resume abc123
```

必须在您的配置中启用会话日志记录才能使这些功能发挥作用。

第一条用户消息将已解析的模型固定到该会话。即使您配置的默认值发生更改，恢复也会保留
固定模型。 `/model` 使用 
 正常配置持久性目标并立即更新现有会话覆盖 
；当发送下一条用户消息
时，会话文件将同步。通过`/config`选择的显式持久目标仅更改
该层。 `/clear` 开始一个新的、未固定的对话，该对话遵循 
 当前配置。如果不再配置所选模型，Vibe 会回退
到当前默认模型。

#### 工作目录控制

使用 `--workdir` 选项指定工作目录：

```bash
vibe --workdir /path/to/project
```

当您想要从当前目录以外的位置运行 Vibe 时，这非常有用。

使用 `--add-dir`（可重复）使其他目录在会话期间可供 agent 使用：

```bash
vibe --add-dir /path/to/other-project --add-dir /path/to/library
```

每个路径都是隐式信任的（无信任提示），并将其 `AGENTS.md` 和 `.vibe/` 配置（工具、技能、agents、提示、挂钩）贡献给会话。文件工具权限以与主工作目录相同的方式对待每个 `--add-dir` 路径 - 在其中读取和写入不需要“外部工作目录”提示。嵌套路径折叠：传递 `/repo` 和 `/repo/sub` 相当于仅传递 `/repo`。

使用 `--worktree NAME` 创建（或重用）一个 [git worktree](<https://git-scm.com/docs/git-worktree>) 并在其中运行：

```bash
vibe --worktree my-feature
```

工作树位于 `$VIBE_HOME/worktrees/<repo-name>-<repo-hash>/NAME` 下，并在名为 `NAME` 的分支上签出（如果不存在则创建，如果存在则附加）。 Vibe `cd` 在会话开始之前插入它并在会话中信任它（无信任提示）。如果从子目录启动 Vibe，Vibe 会进入工作树内的匹配子目录。

仅当现有工作树属于同一 git 存储库并在分支 `NAME` 上签出时，才会重用它们；否则，Vibe 会出现错误并退出，而不是在错误的结账中运行。

通过 `--worktree`（不输入姓名）即可为您提供 Vibe 姓名一：

```bash
vibe "Fix the login bug" --worktree     # -> fix-the-login-bug, on vibe/fix-the-login-bug
vibe --worktree                         # no prompt -> a random slug, e.g. brave-quiet-otter
```

该名称来自您的提示，缩写为整个单词。如果没有提示，或者当提示中没有任何可用的内容（例如只有表情符号）时，Vibe 会生成一个随机的 slug。与命名表单不同，它永远不会重用现有的工作树：Vibe 声明一个自由名称，如果需要，可以添加 `-2`、`-3` 等，因此同时启动的两个会话永远不会出现在同一个结账中。分支始终为 `vibe/<name>`，与 Le Chat Desktop 创建的工作树相匹配。

顺序很重要，因为 `--worktree` 采用可选值：`vibe --worktree "Fix the login bug"` 将提示读取为 * 名称 *。把提示符放在前面，或者用`--`分隔：

```bash
vibe --worktree -- "Fix the login bug"
```

自动清理仅适用于 Vibe 本次运行创建的工作树，并且仅在会话实际启动后 - 启动失败（配置错误，`--continue` 无会话）永远不会删除任何内容，并且重用的工作树始终保留在原处。当交互式会话退出时，如果没有未提交的更改、未跟踪的文件或超出工作树会话开始的提交的提交，Vibe 会自动删除工作树目录。如果其中任何一个存在，Vibe 会询问是否保留或删除工作树。当 Vibe 创建分支时，它会与工作树一起删除；除非您确认删除，否则将保留已存在且仅附加的分支。保留会保留目录和分支，以便您稍后可以返回；删除强制删除它们，丢弃更改、未跟踪的文件和提交。编程运行（`vibe -p ... --worktree NAME`）不会自动清理，因为没有退出提示；使用 `git worktree remove` 手动删除它们。 `--worktree` 与 `--setup` 和 `--check-upgrade` 一起被忽略。

会话的范围仅限于每个目录，因此 `-c`/`--continue` 和 `--resume` 选择器只能看到在该工作树内启动的会话。要跨工作树进行会话，请通过 ID 和 `--resume <ID>` 显式恢复它。

#### 工作树所有权

无论以哪种方式创建工作树，Vibe 都会在其旁边的 `$VIBE_HOME/worktrees/.claims/<repo-name>-<repo-hash>/<name>/` 下写入一条所有权记录，记录分支、会话开始的提交以及 Vibe 是否创建了分支。没有任何东西会被删除：您自己创建的工作树，或者记录丢失或无法读取的工作树，都会被单独保留。

记录目录还保存当前在工作树中工作的每个会话的标记。来自不同客户端的会话在单独的进程中运行，它们之间没有共享任何内容，因此标记是其他人仍在其中的唯一证据。保留任何剩余标记的工作树。彻底杀死的进程会留下其标记，而工作树会幸存下来，这是值得失败的方向。

应用程序服务器永远不会自行删除工作树。关闭会话不算数：桌面应用程序在每次循环后一秒钟释放空闲会话的进程以回收它，并且会话保持活动状态并可恢复，因此其工作树的寿命更长。存在的工作树仅在一种情况下被删除 - **您删除其会话**。它仍然必须是一种创建的 Vibe，没有其他人持有，并且没有自会话开始以来未提交的更改、未跟踪的文件和提交；其他任何内容都会被保留并记录。

有两件事在没有询问的情况下就被清理了，它们都不是您可以在其中工作的工作树。如果会话的第一轮从未完成，则其工作树会回滚，因为这样的会话永远不会发布，也不会留下任何会话文件 - 没有任何内容可返回。从未成为工作树的保留，即 `git worktree add` 未落地的声明留下的空目录，将在下次会话在该存储库中启动时被丢弃。

这种保守主义的代价是，其应用程序服务器被彻底终止的工作树仍保留在磁盘上，并保留了不再存在的会话的标记。删除它是对你的工作是否完成的判断，只有你才能做出。

在 Windows 上，Vibe 解析自动存储库的绝对 Git 可执行文件
检查并忽略当前项目内的可执行文件。套装
`GIT_PYTHON_GIT_EXECUTABLE` 使用自定义或便携式时的绝对路径
git 安装。当没有受信任的 Git 可执行文件可用时，Vibe 仍会启动；
仅依赖于 Git 的元数据和功能不可用。

### 更新设置

Vibe 在会话期间每天最多检查一次更新。当从具有 `uv tool install` 的软件包索引安装 Vibe 时，检查会运行 `uv tool list --outdated`，因此您的 uv 设置（例如 `exclude-newer`）决定提供哪个版本，并且确切的版本引脚 (`mistral-vibe==X`) 会抑制提供。其他安装（git 或本地路径、Homebrew、pip 或对于 `--outdated` 来说太旧的 uv）查询 PyPI。当找到较新的版本时，下次启动时会在打开聊天之前显示更新提示，提示您立即更新（通过 `uv tool upgrade mistral-vibe` 或 `brew upgrade mistral-vibe`）或继续使用当前版本。仅当 `vibe --version` 显示新版本时，Vibe 才会报告成功。如果 uv 安装保留在旧版本上，例如由于版本引脚的原因，Vibe 会询问是否运行 `uv tool install --force mistral-vibe@latest`，这会删除引脚、附加功能和 `--with` 软件包。

立即运行`vibe --check-upgrade`检查，如果有则提示安装较新版本，然后退出。

要完全禁用每日检查，请将其添加到您的 `config.toml` 中：

```toml
enable_update_checks = false
```

### 通知设置

当 agent 需要您关注时（等待批准、提出问题或任务完成），Vibe 可以通知您。当您在 agent 工作时切换到另一个窗口时，这非常有用。

要禁用通知：

```toml
enable_notifications = false
```

### 自定义 Vibe 主目录

默认情况下，Vibe 将其配置存储在 `~/.vibe/` 中。您可以通过设置 `VIBE_HOME` 环境变量来覆盖它：

```bash
export VIBE_HOME="/path/to/custom/vibe/home"
```

这会影响 Vibe 的查找位置：

- `config.toml` - 主要配置
- `.env` - API 键
- `connector_bootstrap_cache.json` - 短暂的连接器发现缓存
- `agents/` - 自定义 agent 配置
- `prompts/` - 自定义系统和压缩提示
- `tools/` - 自定义工具
- `logs/` - 会话日志

自定义工具将在未来版本中弃用。优先选择新的
扩展的技能； Vibe 可以帮助将现有的自定义工具迁移到技能。

### 记录

Vibe 将结构化日志写入 `~/.vibe/logs/vibe.log`。使用 `/log-level` 打开交互式选择器，让您可以将会话覆盖 and/or 保留级别设置为 `config.toml`。您还可以直接在`config.toml`中或通过`/config`屏幕设置`log_level`。

有效级别：`DEBUG`、`INFO`、`WARNING`（默认）、`ERROR`、`CRITICAL`。

优先级：会话覆盖 > `LOG_LEVEL` 环境变量 > config.toml 中的 `log_level` > 默认值。

`LOG_LEVEL` 环境变量会覆盖启动时的配置值。使用`DEBUG_MODE=true`在启动时强制`DEBUG`。

## Editors/IDEs

Mistral Vibe 可在支持 [Agent 客户端协议 ](<https://agentclientprotocol.com/overview/clients>) 的文本编辑器和 IDE 中使用。有关各种编辑器和 IDE 的设置说明，请参阅 [ACP 设置文档 ](<在编辑器中接入 Mistral Vibe ACP.md>)。

## 资源

- [CHANGELOG](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/CHANGELOG.md>) - 查看每个版本中的新增内容
- [CONTRIBUTING](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/CONTRIBUTING.md>) - 功能请求、反馈和错误报告指南

## 数据收集和使用

使用 Vibe 须遵守我们的 [ 隐私政策 ](<https://legal.mistral.ai/terms/privacy-policy>)，并且可能包括收集和处理与您使用服务相关的数据（例如使用数据），以操作、维护和改进 Vibe。您可以通过设置 `enable_telemetry = false` 禁用 `config.toml` 中的遥测和崩溃报告。


## 许可证

版权所有 2025 Mistral AI

根据 Apache 许可证版本 2.0（“许可证”）获得许可；
除非遵守许可证，否则不得使用此文件。
您可以在以下位置获取许可证副本：

    http://www.apache.org/licenses/LICENSE-2.0

除非适用法律要求或书面同意，否则根据许可分发的软件
将在“AS IS”BASIS、
WITHOUT WARRANTIES OR上分发CONDITIONS OF ANY KIND，明示或暗示。
有关完整许可文本，请参阅 [LICENSE](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/LICENSE>) 文件。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Mistral Vibe

[![PyPI Version](<https://img.shields.io/pypi/v/mistral-vibe>)](<https://pypi.org/project/mistral-vibe>)
[![Python Version](<https://img.shields.io/badge/python-3.12%2B-blue>)](<https://www.python.org/downloads/release/python-3120/>)
[![CI Status](<https://github.com/mistralai/mistral-vibe/actions/workflows/ci.yml/badge.svg>)](<https://github.com/mistralai/mistral-vibe/actions/workflows/ci.yml>)
[![License](<https://img.shields.io/github/license/mistralai/mistral-vibe>)](<https://github.com/mistralai/mistral-vibe/blob/main/LICENSE>)

```
██████████████████░░
██████████████████░░
████  ██████  ████░░
████    ██    ████░░
████          ████░░
████  ██  ██  ████░░
██      ██      ██░░
██████████████████░░
██████████████████░░
```

**Mistral's open-source CLI coding assistant.**

Mistral Vibe is a command-line coding assistant powered by Mistral's models. It provides a conversational interface to your codebase, allowing you to use natural language to explore, modify, and interact with your projects through a powerful set of tools.

> [!WARNING]
> Mistral Vibe works on Windows, but we officially support and target UNIX environments.

### One-line install (recommended)

**Linux and macOS**

```bash
curl -LsSf https://mistral.ai/vibe/install.sh | bash
```

**Windows**

First, install uv

```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Then, use uv command below.

### Using uv

```bash
uv tool install mistral-vibe
```

### Using pip

```bash
pip install mistral-vibe
```

## Table of Contents

- [Features](<#features>)
  - [Built-in Agents](<#built-in-agents>)
  - [Subagents and Task Delegation](<#subagents-and-task-delegation>)
  - [Interactive User Questions](<#interactive-user-questions>)
- [Terminal Requirements](<#terminal-requirements>)
- [Quick Start](<#quick-start>)
- [Usage](<#usage>)
  - [Interactive Mode](<#interactive-mode>)
  - [Trust Folder System](<#trust-folder-system>)
  - [Programmatic Mode](<#programmatic-mode>)
  - [TUI Implementations](<#tui-implementations>)
- [Voice Mode](<#voice-mode>)
- [Slash Commands](<#slash-commands>)
  - [Built-in Slash Commands](<#built-in-slash-commands>)
  - [Custom Slash Commands via Skills](<#custom-slash-commands-via-skills>)
- [Skills System](<#skills-system>)
  - [Creating Skills](<#creating-skills>)
  - [Skill Discovery](<#skill-discovery>)
  - [Managing Skills](<#managing-skills>)
- [Configuration](<#configuration>)
  - [Configuration File Location](<#configuration-file-location>)
  - [API Key Configuration](<#api-key-configuration>)
  - [OpenTelemetry Tracing](<#opentelemetry-tracing>)
  - [Custom System Prompts](<#custom-system-prompts>)
  - [Custom Agent Configurations](<#custom-agent-configurations>)
  - [Tool Management](<#tool-management>)
  - [MCP Server Configuration](<#mcp-server-configuration>)
  - [Session Management](<#session-management>)
  - [Update Settings](<#update-settings>)
  - [Custom Vibe Home Directory](<#custom-vibe-home-directory>)
- [Editors/IDEs](<#editorsides>)
- [Resources](<#resources>)
- [Data collection & usage](<#data-collection--usage>)
- [License](<#license>)

## Features

- **Interactive Chat**: A conversational AI agent that understands your requests and breaks down complex tasks.
- **Powerful Toolset**: A suite of tools for file manipulation, code searching, version control, and command execution, right from the chat prompt.
  - Read, write, and patch files (`read`, `write_file`, `edit`).
  - Execute shell commands, with managed shell sessions, polling, and stdin helpers available during rollout.
  - Recursively search code with `grep` (with `ripgrep` support).
  - Manage a `todo` list to track the agent's work.
  - Ask interactive questions to gather user input (`ask_user_question`).
  - Delegate tasks to subagents for parallel work (`task`).
- **Project-Aware Context**: Vibe automatically provides your project's location, Git branch, and recent commit history to the agent, improving its understanding of your codebase.
- **Advanced CLI Experience**: Built with modern libraries for a smooth and efficient workflow.
  - Autocompletion for slash commands (`/`) and file paths (`@`).
  - Image attachments via `@` mentions — `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp` files are sent to vision-capable models (e.g. Mistral Medium 3.5) as native multimodal content.
  - Persistent command history.
  - Beautiful Themes.
- **Highly Configurable**: Customize models, providers, tool permissions, and UI preferences through a simple `config.toml` file.
- **Safety First**: Features tool execution approval.
- **Multiple Built-in Agents**: Choose from different agent profiles tailored for specific workflows.

### Built-in Agents

Vibe comes with several built-in agent profiles, each designed for different use cases:

- **`ask`**: Requires approval for tool executions.
- **`plan`**: Read-only agent for exploration and planning. Auto-approves safe tools like `grep` and `read`.
- **`accept-edits`**: The default agent. Auto-approves file edits only (`write_file`, `edit`). Useful for code refactoring.
- **`auto-approve`**: Auto-approves all tool executions. Use with caution.

Use the `--agent` flag to select a different agent:

```bash
vibe --agent plan
```

To change the default agent used when `--agent` is not passed, set
`default_agent` in your `config.toml`:

```toml
default_agent = "plan"
```

Valid values are `ask`, `plan`, `accept-edits`, `auto-approve`,
`lean` (only when listed in `installed_agents`), or the name of any
custom agent file in `~/.vibe/agents/` or the project's `.vibe/agents/`
directory. Subagents such as `explore` are not accepted.

> Note: `default_agent` applies in both interactive and programmatic
> (`-p` / `--prompt`) sessions. Pass `--auto-approve` or `--yolo` with any
> agent when a run should approve all tool calls without prompting.

### Subagents and Task Delegation

Vibe supports subagents for delegating tasks. Subagents run independently and can perform specialized work without user interaction, preventing the context from being overloaded.

The `task` tool allows the agent to delegate work to subagents:

```
> Can you explore the codebase structure while I work on something else?

🤖 I'll use the task tool to delegate this to the explore subagent.

> task(task="Analyze the project structure and architecture", agent="explore")
```

Create custom subagents by adding `agent_type = "subagent"` to your agent configuration. Vibe comes with a built-in subagent called `explore`, a read-only subagent for codebase exploration and skill loading used internally for delegation.

When the Unified Harness is active, the interactive prompt shows each known
subagent's name, type, live status, and latest measured context size. Opening a
subagent also shows its active status above the prompt and its context use in the
bottom-right gauge. With an empty or locally edited prompt, press Down from its
last line to focus the list;
unsent text stays in the prompt. Use Up and Down to highlight a row, then press
Enter to open it; with the mouse, hover to highlight and click to open. Select **Main
conversation** to return, or press Escape from a subagent view. Ctrl+C adds a
local-only, error-styled user message explaining that subagents cannot be
controlled directly; return to Main and ask the main agent to stop one. Press
Ctrl+C again to quit Vibe. After
opening Main conversation, press Up from its row to focus the prompt. While a
subagent view is open, Up stops at the Main row. Idle subagents remain listed as
**ready** because the main agent can send them more instructions. Explicitly
stopped subagents leave the list after you return to Main. The first subagent
update does not add a local information message. When a subagent becomes ready,
its transcript shows a local information message explaining how to give it a
new goal or stop it from Main. Set `show_subagent_status_list = false` in
`config.toml`, or change it through `/config`, to hide this UI.

### Interactive User Questions

The `ask_user_question` tool allows the agent to ask you clarifying questions during its work. This enables more interactive and collaborative workflows.

```
> Can you help me refactor this function?

🤖 I need to understand your requirements better before proceeding.

> ask_user_question(questions=[{
    "question": "What's the main goal of this refactoring?",
    "options": [
        {"label": "Performance", "description": "Make it run faster"},
        {"label": "Readability", "description": "Make it easier to understand"},
        {"label": "Maintainability", "description": "Make it easier to modify"}
    ]
}])
```

The agent can ask multiple questions at once, displayed as tabs. Each question supports 2-4 options plus an automatic "Other" option for free text responses.

## Terminal Requirements

Vibe's interactive interface requires a modern terminal emulator. Recommended terminal emulators include:

- **WezTerm** (cross-platform)
- **Alacritty** (cross-platform)
- **Ghostty** (Linux and macOS)
- **Kitty** (Linux and macOS)

Most modern terminals should work, but older or minimal terminal emulators may have display issues.

## Quick Start

1. Navigate to your project's root directory:

   ```bash
   cd /path/to/your/project
   ```

2. Run Vibe:

   ```bash
   vibe
   ```

3. If this is your first time running Vibe, it will:
   - Use built-in defaults without creating a configuration file until you
     save a setting
   - Prompt you to enter your API key if it's not already configured
   - Save your API key to `~/.vibe/.env` for future use

   Alternatively, you can configure your API key separately using `vibe --setup`.

4. Start interacting with the agent!

   ```
   > Can you find all instances of the word "TODO" in the project?

   🤖 The user wants to find all instances of "TODO". The `grep` tool is perfect for this. I will use it to search the current directory.

   > grep(pattern="TODO", path=".")

   ... (grep tool output) ...

   🤖 I found the following "TODO" comments in your project.
   ```

## Usage

### Interactive Mode

Simply run `vibe` to enter the interactive chat loop.

- **Multi-line Input**: Press `Ctrl+J` or `Shift+Enter` for select terminals to insert a newline.
- **Input Cursor**: The main input cursor blinks by default. Set `cursor_blink = false` in `config.toml` for a steady cursor. Changes apply without restarting through `/config` or `/reload` in both Python and Rust terminals.
- **File Paths**: Reference files in your prompt using the `@` symbol for smart autocompletion (e.g., `> Read the file @src/agent.py`). A bare `@` quickly lists immediate non-hidden entries; after a path character, Git workspaces suggest tracked and non-ignored files. Pasting a standalone existing absolute or home-relative file or folder also creates a mention.
- **Shell Commands**: Prefix any command with `!` to execute it directly in your shell, bypassing the agent (e.g., `> !ls -l`).
- **External Editor**: Press `Ctrl+G` to edit your current input in an external editor.
- **Tool Output Toggle**: Press `Ctrl+O` to toggle the tool output view.
- **Todo View Toggle**: Press `Ctrl+T` to toggle the todo list view.
- **Debug Console**: Press `Ctrl+\` to toggle the debug console.
- **Agent Selection**: Press `Shift+Tab` to cycle through agents (ask, plan, ...).
- **Queueing**: Prompts submitted while the agent is working are queued by the app server. Empty `Enter` or `Ctrl+Enter` steers the queued prompts into the active turn; on Unified Harness sessions, this atomically consumes the stored queue item. `Ctrl+C` removes the newest queued prompt. `Escape` interrupts the active turn and pauses remaining prompts, and `Enter` resumes a paused queue. Shell commands and non-side-channel slash commands require an idle session.
- **Exit**: Type `/exit`, `exit`, `quit`, `:q`, or `:quit` in the input box, or press `Ctrl+C` / `Ctrl+D` twice within ~1 second. Set `ask_confirmation_on_exit = false` (or toggle it in `/config`) to make `Ctrl+D` quit on the first press; `Ctrl+C` always requires confirmation.

### Copying & Text Selection

- **Copy**: Use `Ctrl+Y` or `Ctrl+Shift+C` to copy the current selection to clipboard. With autocopy enabled (default via `autocopy_to_clipboard = true`), mouse selection automatically copies on release and shows a brief confirmation.
- **Multi-click selection**: Double-click selects a word, triple-click selects the paragraph. Dragging extends the selection at the same granularity.

You can start Vibe with a prompt using the following command:

```bash
vibe "Refactor the main function in cli/main.py to be more modular."
```

### Trust Folder System

Vibe includes a trust folder system to ensure you only run the agent in directories you trust. When you first run Vibe in a new directory which contains a `.vibe` subfolder, it may ask you to confirm whether you trust the folder.

Trusted folders are remembered for future sessions. You can manage trusted folders through its configuration file `~/.vibe/trusted_folders.toml`. In the trust prompt, use Up/Down or the mouse wheel to scroll the detected-file list. Text is selectable there too: double-click selects a word, triple-click selects a paragraph, and dragging near the list edges scrolls while extending the selection.

This safety feature helps prevent accidental execution in sensitive directories.

### Programmatic Mode

You can run Vibe non-interactively by piping input or using the `--prompt` flag. This is useful for scripting.

```bash
vibe --prompt "Refactor the main function in cli/main.py to be more modular."
```

By default, it uses your configured `default_agent` (`accept-edits` unless changed).
To approve all tool calls without prompting, pass `--auto-approve` or `--yolo`
(also available for interactive sessions):

```bash
vibe --prompt "Refactor the main function in cli/main.py to be more modular." --auto-approve
```

#### Programmatic Mode Options

When using `--prompt`, you can specify additional options:

- **`--max-turns N`**: Limit the maximum number of assistant turns. The session will stop after N turns.
- **`--max-price DOLLARS`**: Set a maximum cost limit in dollars for this run. The turn is interrupted once the cost exceeds this limit, and the outcome is `price_limit`.
- **`--max-tokens N`**: Set a maximum cumulative LLM token budget for this run, counting both prompt and completion tokens. The turn is interrupted once usage exceeds this limit, and the outcome is `token_limit`.
- **`--agent NAME`**: Select the agent profile for this run.
- **`--auto-approve`, `--yolo`**: Approves all tool calls without prompting, including in interactive sessions. Can be combined with any `--agent` value.
- **`--enabled-tools TOOL`**: Enable specific tools. In programmatic mode, this disables all other tools. Can be specified multiple times. Supports exact names, glob patterns (e.g., `bash*`), or regex with `re:` prefix (e.g., `re:^serena_.*$`).
- **`--disabled-tools TOOL`**: Disable specific tools after `--enabled-tools` filtering. Can be specified multiple times. Supports exact names, glob patterns (e.g., `bash*`), or regex with `re:` prefix (e.g., `re:^serena_.*$`).
- **`--output FORMAT`**: Set the output format. Options:
  - `text` (default): Human-readable text output
  - `json`: All messages as JSON at the end
  - `streaming`: Newline-delimited JSON per message

  On a resumed session, `text` and `streaming` cover only the new run, so a run that ends without a reply of its own prints none; `json` holds the whole session.
- **`--prompt-file PATH`**: Read the prompt from a file instead of `--prompt`. With `-p` and no text, the prompt is read from stdin.
- **`--time-limit SECONDS`**: Stop the run after this many seconds. The active turn and its tool processes are stopped, and the session can be resumed with `--resume`. SIGTERM stops a run the same way.
- **`--output-dir DIR`**: Write `DIR/export.json` and copy the session journal to `DIR/session/`, whichever way the run ends. The export records the Vibe version, the effective config (header and environment values redacted), token usage, cost, the number of model calls the agent made (`steps`), the stop reason and the outcome. When the session had config issues, such as a skill or a config file it could not load, the export lists them under `warnings`, each as `{"message": str, "source": str | null}`, where `source` names the file or skill it is about; the field is left out when there are none, and the run goes on regardless. Usage, cost and `steps` count this run only, also on a resumed session, while the copied journal holds the whole session. Only an argument the parser itself rejects (an unknown flag, a malformed value) exits 1 without an export.

Example:

```bash
vibe --prompt "Analyze the codebase" --max-turns 5 --max-price 1.0 --max-tokens 50000 --output json
vibe --prompt-file task.md --auto-approve --time-limit 1800 --output-dir out/
```

#### Exit Codes

| Code | Outcome in `export.json` | Meaning |
| --- | --- | --- |
| `0` | `finished` | The agent finished. |
| `1` | `usage_error`, `config_error` | The run could not start as asked. |
| `2` | `infrastructure_failure` | The model API (after retries) or the runtime failed. |
| `3` | `turn_limit`, `token_limit`, `price_limit`, `deadline`, `terminated`, `length`, `refusal` | The agent stopped without finishing: a limit or a model refusal; score what it left. `length` means the conversation or the last answer hit a model token limit: the context window, or the output-token cap (`max_output_tokens` on the model). |
| `4` | `aborted` | The `--agent-socket` server aborted the run (experimental, see below); `error.message` in `export.json` is the server's message. |

A model refusal ends the run as `refusal`, exit `3`; `error` in `export.json` has the code `refusal`, and its message gives the refusal's category and explanation when the model sends them. Only the legacy runtime (`--legacy-harness`) reports a refusal as one.

#### Agent Socket (`--agent-socket`, experimental)

Experimental: the option is hidden from `--help`, and it and the protocol below
may change or go away without notice.

`vibe -p --agent-socket PATH` runs the same session as `vibe -p` on the host,
with the same export, exit codes, limits and SIGTERM handling, connected to an
agent server on the Unix socket `PATH`. The server serves two independent
things, either or both:

- **A sandbox.** The session's file and shell tools run in it instead of on
  this host, and the session starts in the sandbox's workspace (see "Sandboxed
  sessions" below).
- **Tools.** The server declares them in its handshake, and the session offers
  them to the model next to Vibe's own; each call goes back to the server. A
  tool's `modelAccess` decides whether the model calls it directly, from
  `run_typescript` as `tools.<namespace>.<name>`, or both. `--enabled-tools` and
  `--disabled-tools` filter only Vibe's own tools. Without a sandbox, the
  session runs on this host as plain `vibe -p` does.

The option needs `-p` or `--prompt-file`, and cannot be combined with
`--workdir`, `--worktree`, `--add-dir`, `--continue` or `--teleport`; those are
usage errors (exit 1). It is not supported on Windows (exit 1).

To continue a finished run with a new prompt, start a new run with
`--resume SESSION_ID`, taking the ID from `session_id` in the first run's
`export.json`. The new run redoes the handshake, so the server may be a new one
and declares its tools again; the model sees the whole earlier conversation
followed by the new prompt. Every limit (`--max-turns`, `--time-limit`,
`--max-tokens`, `--max-price`) counts from the new run's start. Its export, under
the same `session_id`, counts only its own `steps`, `usage` and `cost_usd`; its
`journal_dir` holds the whole session, earlier runs included.

##### Sandboxed sessions

When the server serves a sandbox, the workspace context (git metadata and
`AGENTS.md` files) is read from the sandbox. Session storage, model calls, MCP
servers and hooks stay on this host. Background processes run in the sandbox,
which must be POSIX for them, as `enable_background_processes` says. Rewinding
the session restores no files, since the files are in the sandbox. If the
sandbox fails, the run ends as `infrastructure_failure`.

Vibe's tool helper can crash in a sandbox that works: it exits part way through
a tool call without answering, say from a segmentation fault or an
out-of-memory kill. The model then reads a tool error saying the command may or
may not have run, with the exit code and what the helper printed, and the turn
goes on. The third crash in a row ends the run as `infrastructure_failure`; a
tool call the helper answers, even with an error, resets the count. Sessions
sharing the sandbox, such as subagents, share the count. A helper that cannot
start at all (no interpreter at `python`) ends the run at once, as does a crash
outside the model's tool calls, such as while copying skills into the sandbox
or saving a large output there.

As on the host, when the model reads a file, the `AGENTS.md` files in the
directories between it and the workspace are added to what it reads, once per
session. Looking for them costs a `sandbox/readFile` request per directory, so
a session looks in each directory once. When Vibe's own `write_file` or
`search_replace` writes an `AGENTS.md`, its directory is looked in again on the
next read below it; a `bash` command whose text names `AGENTS.md` makes every
directory be looked in again. An `AGENTS.md` that appears any other way, for
example from a script or `git checkout`, in a directory already looked in is
not seen for the rest of the session.

File tools follow symlinks in the sandbox, as on the host. Approvals, though,
match a path as written, since this host cannot see the sandbox's links. So
without `--auto-approve`, approving a link also allows its target, and a link
in the workspace that points outside it counts as a workspace path, for example
for `accept-edits`. The target is still in the sandbox. Runs with
`--auto-approve`, such as evaluation runs, ask for no approval and are not
affected.

A tool output too large to hand the model whole is kept with the session on
this host, as with plain `vibe -p`, and also saved in an owner-only directory
under the sandbox's temporary directory. The model is given that sandbox path,
so it reads the output back with the sandboxed tools.

Only the user's Vibe files are loaded on this host, and `--trust` has no
effect. Of the project's own files, only the `AGENTS.md` files and the skills
(`.vibe/skills` and `.agents/skills`) are read from the sandbox. The rest of the
project's `.vibe/` directory (config, agents, tools, hooks, plugins and
prompts) is ignored, whereas `vibe -p --trust` loads it: loading it would run
code the sandbox controls on this host, such as hooks, MCP servers and tools.
A run whose sandbox workspace has it logs a warning.

The model reads skills with the sandboxed tools, so the skills read on this
host (the user's, and the plugins', the built-in ones included) are copied into
the sandbox's temporary directory under `mistralai-vibe-skills/<digest>/`,
outside the workspace, and the model is given those paths. A copy is named by
the digest of its files, checked before it is reused, and uploaded only when
missing, so later runs on the same sandbox reuse it. A skill with more than 200
files, a file over 256 KiB, more than 1 MiB of files besides `SKILL.md`, or a
`SKILL.md` over 2 MiB is not copied, and the run goes without it. Each skill
left out, for that or because the copy failed, is logged and listed in the
session's config issues, and so in the export's `warnings`.

##### Protocol

The caller creates the socket with mode `0600` inside a `0700` directory, and
serves it for the whole run. A Unix socket path is limited to 104 bytes on macOS
(108 on Linux), so keep it short, for example under `/tmp`.

For each request, Vibe opens a new connection, writes one line, reads one line
and closes the connection; concurrent requests use concurrent connections.
Answer them concurrently too: a request's timeout covers the time it waits
behind others, such as a background process's poll behind a long shell
command. A line is a compact JSON-RPC 2.0 message with camelCase fields, followed by `\n`.
Replies may carry fields Vibe does not know, which it ignores.

| Method | Params | Result |
| --- | --- | --- |
| `agent/initialize` | `{"protocolVersion": 2, "vibeVersion": str}` | `{"protocolVersion": 2, "workspace"?: str, "python"?: str, "tools"?: [ToolDefinition], "toolTimeoutSeconds"?: float, "instructions"?: str}` |
| `sandbox/execute` | `{"command": str, "cwd": str, "timeout": float \| null}` | `{"exitCode": int, "stdout": str, "stderr": str}` |
| `sandbox/readFile` | `{"path": str, "maxBytes": int}` | `{"contentBase64": str \| null}` |
| `tools/call` | `{"callbackId": str, "name": str, "input": any, "toolCallId": str}` | `{"output"?: any, "annotations"?: object, "error"?: {"message": str, "code"?: str, "details"?: any}}` |

- `agent/initialize` is called once, before the session starts, and must
  answer within 60 seconds. The server must
  answer with protocol version 2. `workspace` and `python` come together, or
  not at all when the server serves no sandbox: `workspace` is the absolute
  sandbox path the session starts in, and `python` the interpreter that runs
  Vibe's tool helper (Python 3.9 or later). Each tool is
  `{"namespace"?: str, "name": str, "description"?: str, "inputSchema"?: object, "outputSchema"?: object, "modelAccess"?: "direct" | "programmatic" | "both"}`;
  `namespace` defaults to `client` and `modelAccess` to `programmatic`. A
  namespace cannot be `vibe` or `ui`, nor the name of an MCP server the session
  has, and a `<namespace>.<name>` is declared once. `toolTimeoutSeconds`, a
  positive number, is how long a `tools/call` may take; it defaults to 600.
  `instructions` are project instructions for the session. The system prompt
  carries them where it carries the project's `AGENTS.md` files, under the
  same heading and with the same weight, followed by any `AGENTS.md` found in
  the workspace. They are left out, like the `AGENTS.md` files, when
  `include_project_context` is off. Without a socket, the same text goes in
  an `AGENTS.md` at the root of the workspace, which `vibe -p` reads when the
  directory is trusted (`--trust`).
  If the handshake fails, the run ends as `infrastructure_failure` (exit 2);
  if it declares a tool Vibe cannot offer, as `usage_error` (exit 1), before
  the model is called.
- `sandbox/execute` runs `command` with `sh` in `cwd`, with the sandbox's own
  environment. Vibe runs its tool helper with `cwd` `/` and passes the
  session's working directory in the command itself, so a model that deletes
  or moves that directory gets a tool error, as on the host. A non-zero exit
  is a result, not an error. A command that
  outlives `timeout` seconds is answered with the error code `-32001`: the
  model reads it as a timed-out command, as on the host, and the turn goes
  on. A server that stops a command sooner, at a limit of its own, says so in
  the error's optional `data`, as `{"timeoutSeconds": float}`, and the model
  is told that limit; without it, the model is told no limit. To stop a
  command, the server kills the process it started (swerex kills its process
  group); the processes the command started go with it. Vibe waits 30
  seconds past `timeout` for the reply; if none comes, it closes the
  connection and the sandbox has failed.
- `sandbox/readFile` returns at most `maxBytes` of a regular file, base64
  encoded, or `null` when there is no such file.
- `tools/call` runs one call of a declared tool. `callbackId` is unique to the
  call, `name` is `<namespace>.<name>`, and `toolCallId` the model's call it
  answers. `output`
  is the tool's result, and `error` makes the call a tool error. Either way the
  model reads it and the turn goes on. So does an error reply, a malformed
  reply, a lost connection, or no reply within `toolTimeoutSeconds`: each is a
  tool error. A shorter `--time-limit` shortens the wait to match.

For a sandbox request, any other error reply, a malformed reply, or a
connection that closes or cannot be opened means the sandbox failed: the turn
fails and the run ends as `infrastructure_failure` (exit 2). A tool helper
command that exits non-zero is a crash of the helper, not of the sandbox; see
[Sandboxed sessions](<#sandboxed-sessions>).

The server can end the run itself, from any request, by answering it with the
error code `-32002` and a message: `{"code": -32002, "message": str}`. Vibe
stops the run as SIGTERM does and exits 4, with the outcome `aborted` and the
server's message as `error.message` in `export.json`. Vibe gives the abort no
meaning of its own; the message is for whoever reads the export.

### TUI Implementations

The `vibe` command starts through a small launcher that picks the terminal
client from the `VIBE_CLI` environment variable:

- `VIBE_CLI=rust vibe` starts the Rust TUI.
- `VIBE_CLI=python vibe` runs the legacy Python (Textual) TUI.
- With `VIBE_CLI` unset (or set to any other value), `vibe` runs the legacy
  Python TUI.

The commands above use POSIX shell syntax, where the assignment only applies
to that command. On Windows, set the variable for the session instead:

- PowerShell: `$env:VIBE_CLI = "python"; vibe` (unset with
  `Remove-Item Env:VIBE_CLI`)
- cmd: `set VIBE_CLI=python`, then run `vibe` (unset with `set VIBE_CLI=`)

When the `VIBE_CLI` environment variable is set to `rust`, every `vibe`
invocation selects the Rust client. To run a nested command (e.g.
`vibe mcp add ...`) with the legacy Python client instead, set `VIBE_CLI` to
`python` for that invocation.

## Voice Mode

> [!WARNING]
> Voice mode is experimental and may change in future releases.

Voice mode allows you to dictate input using your microphone instead of typing.

### Activating Voice Mode

Toggle voice mode on or off with the `/voice` slash command:

```
> /voice
```

### Recording Shortcuts

| Shortcut | Action           |
| -------- | ---------------- |
| `Ctrl+R` | Start recording  |
| Any key  | Stop recording   |
| `Escape` | Cancel recording |
| `Ctrl+C` | Cancel recording |

## Slash Commands

Use slash commands for meta-actions and configuration changes during a session.

### Built-in Slash Commands

Vibe provides several built-in slash commands. Use slash commands by typing them in the input box:

```
> /help
```

If a model response is interrupted by a backend error, use `/retry` to continue
from the partial response. Add optional guidance after the command, for example
`/retry keep the conclusion concise`.

Use `/mcp` or `/connectors` to browse configured MCP servers and workspace
connectors. The browser starts on the first item; press Up or Left to focus its
fuzzy search bar, then Up again to wrap to the last item.

With the Unified Harness, `/loop every two minutes check the build` asks the
model to schedule a recurring prompt. Calendar requests such as `/loop weekdays
at 9am review CI` work too, using the machine's local timezone. Ask the model to
list or cancel schedules; `/loop` has no TUI management subcommands. Requests
submitted while busy join the normal prompt queue. Schedules survive resume,
but only run while Vibe is open and idle; missed runs do not accumulate.
The `cron` tool is enabled and allowed by default; explicit tool filters and
`tools.cron.permission` settings still apply.

### Custom Slash Commands via Skills

You can define your own slash commands through the skills system. Skills are reusable components that extend Vibe's functionality.

To create a custom slash command:

1. Create a skill directory with a `SKILL.md` file
2. Set `user-invocable = true` in the skill metadata
3. Define the command logic in your skill

Example skill metadata:

```markdown
---
name: my-skill
description: My custom skill with slash commands
user-invocable: true
---
```

Custom slash commands appear in the autocompletion menu alongside built-in commands.

## Skills System

Vibe's skills system allows you to extend functionality through reusable components. Skills can add new tools, slash commands, and specialized behaviors.

Vibe follows the [Agent Skills specification](<https://agentskills.io/specification>) for skill format and structure.

### Creating Skills

Skills are defined in directories with a `SKILL.md` file containing metadata in YAML frontmatter. For example, `~/.vibe/skills/code-review/SKILL.md`:

```markdown
---
name: code-review
description: Perform automated code reviews
license: MIT
compatibility: Python 3.12+
user-invocable: true
allowed-tools:
  - read
  - grep
  - ask_user_question
---

# Code Review Skill

This skill helps analyze code quality and suggest improvements.
```

By default, both the user and the model can invoke a skill. Invocation controls
are independent:

- `user-invocable: false` hides the skill from the `/` menu and prevents direct
  `/skill-name` invocation while still allowing the model to load it.
- `disable-model-invocation: true` follows the Claude Code convention and makes
  the skill explicit-only: users can still invoke `/skill-name`, but the model
  does not see or invoke it automatically.
- Skills using OpenAI's `agents/openai.yaml` convention can express the same
  explicit-only behavior. Vibe applies this policy regardless of active model or provider:

  ```yaml
  policy:
    allow_implicit_invocation: false
  ```

  If the policy metadata is malformed or contains an unknown policy field,
  Vibe reports the issue and keeps the skill explicit-only.

### Skill Discovery

Vibe discovers skills from multiple locations:

1. **Custom paths**: Configured in `config.toml` via `skill_paths`
2. **Standard Agent Skills path** (project root, trusted folders only): `.agents/skills/` — [Agent Skills](<https://agentskills.io>) standard
3. **Local project skills** (project root, trusted folders only): `.vibe/skills/` in your project
4. **Global skills directories**: `~/.vibe/skills/` and `~/.agents/skills/`

```toml
skill_paths = ["/path/to/custom/skills"]
```

### Managing Skills

Enable or disable skills using patterns in your configuration:

```toml
# Enable specific skills
enabled_skills = ["code-review", "test-*"]

# Disable specific skills
disabled_skills = ["experimental-*"]
```

Skills support the same pattern matching as tools (exact names, glob patterns, and regex).

## Configuration

### Configuration File Location

Vibe is configured via a `config.toml` file. It looks for this file first in `./.vibe/config.toml` and then falls back to `~/.vibe/config.toml`.

### Theme

The default `auto` theme follows the terminal background when it can be detected, then the operating-system light/dark preference. Choose another theme with `/theme` or set it explicitly:

```toml
theme = "dracula"
```

### API Key Configuration

To use Vibe, you'll need a Mistral API key. You can obtain one by signing up at [https://console.mistral.ai](<https://console.mistral.ai>).

You can configure your API key using `vibe --setup`, or through one of the methods below.

Vibe supports multiple ways to configure your API keys:

1. **Interactive Setup (Recommended for first-time users)**: When you run Vibe for the first time or if your API key is missing, Vibe will prompt you to enter it. The key will be securely saved to `~/.vibe/.env` for future sessions.

2. **Environment Variables**: Set your API key as an environment variable:

   ```bash
   export MISTRAL_API_KEY="your_mistral_api_key"
   ```

3. **`.env` File**: Create a `.env` file in `~/.vibe/` and add your API keys:

   ```bash
   MISTRAL_API_KEY=your_mistral_api_key
   ```

   Vibe automatically loads API keys from `~/.vibe/.env` on startup. Environment variables take precedence over the `.env` file if both are set.

**Note**: The `.env` file is specifically for API keys and other provider credentials. General Vibe configuration should be done in `config.toml`.

### Custom Domains

If you use a Mistral-compatible deployment instead of the default `console.mistral.ai` / `api.mistral.ai`, you can point browser sign-in at it. The credential is still a Mistral API key.

Run `vibe --setup`, choose **Launch browser** then **Other**, enter your login domain, and sign in through the browser. A bare domain is prefixed with `https://`, and the auth API base is derived as `DOMAIN/api`. The overridden `mistral` provider is saved to your user config so subsequent runs reuse it.

**Note**: the wizard reads any custom `browser_auth_base_url` already set in `config.toml`. Choosing **Other** pre-fills that configured domain so you can confirm or edit it. Choosing **Mistral AI** while a custom domain is configured warns you first — press **Enter** again to confirm the reset to the default domain, which is then persisted.

### TLS and Corporate Certificate Authorities

By default, Vibe uses the bundled `certifi` certificate roots for outbound HTTPS requests. If your organization installs private certificate authorities in the operating system trust store, you can opt in to the system trust store in `config.toml`:

```toml
enable_system_trust_store = true
```

`SSL_CERT_FILE` and `SSL_CERT_DIR` are still supported and are loaded as additional trust anchors.

### OpenTelemetry Tracing

Vibe can export traces for agent, model, and tool operations over OTLP/HTTP. Enable tracing in `config.toml`:

```toml
enable_otel = true
```

By default, Vibe sends traces to the telemetry endpoint associated with the configured Mistral provider and authenticates with that provider's API key. `enable_telemetry` must also remain enabled.

To send traces to another collector, configure its base URL. Vibe appends `/v1/traces`; configure authentication with the standard `OTEL_EXPORTER_OTLP_*` environment variables when needed.

```toml
enable_otel = true
otel_endpoint = "https://collector.example.com:4318"
```

Span attributes are redacted on the client before export. The default mode redacts sensitive values, `strict` redacts sensitive attributes entirely, and `none` disables redaction:

```toml
otel_redaction = "default" # "default", "strict", or "none"
```

Use `none` only when the collector is trusted to receive potentially sensitive prompt, response, and tool data.

### Custom System Prompts

You can create `AGENTS.md` files to add custom instructions. You can also replace the entire system prompt.

Place `AGENTS.md` files in:
- `~/.vibe/AGENTS.md` — user-level instructions for all projects
- Project directories — project-specific instructions, loaded from cwd up to the trust root

Priority: closer directories override more distant ones. Instructions in `AGENTS.md` override the default system prompt. Files are only loaded for trusted folders.

Custom system prompts entirely replace the default one (`prompts/cli.md`). Create a markdown file in the `~/.vibe/prompts/` directory with your custom prompt content.

To use a custom system prompt, set the `system_prompt_id` in your configuration to match the filename (without the `.md` extension):

```toml
# Use a custom system prompt
system_prompt_id = "my_custom_prompt"
```

This will load the prompt from `~/.vibe/prompts/my_custom_prompt.md`.

Project-local prompts in `.vibe/prompts/` are also supported and override user-level prompts with the same name. This applies to all custom prompts (system, compaction, and title).

### Custom Compaction Prompts

Compaction uses the built-in prompt at `prompts/compact.md` by default. You can replace it with a custom prompt from `~/.vibe/prompts/` (or `.vibe/prompts/`) using the same resolution rules as system prompts.

To use a custom compaction prompt, set `compaction_prompt_id` in your configuration to match the filename (without the `.md` extension):

```toml
# Use a custom compaction prompt
compaction_prompt_id = "my_compaction_prompt"
```

Any extra instructions passed to `/compact ...` are appended after the configured compaction prompt.

Compaction keeps the same session and visible conversation. Later model requests
use the latest compacted context followed by newer messages.

### Custom Title Prompts

Vibe generates session titles in the background with the built-in prompt at `prompts/session_title.md`. You can replace it with a custom prompt from `~/.vibe/prompts/` (or `.vibe/prompts/`) using the same resolution rules as system prompts.

To use a custom title prompt, set `title_prompt_id` in your configuration to match the filename (without the `.md` extension):

```toml
# Use a custom title prompt
title_prompt_id = "my_title_prompt"
```

The configured prompt is the system prompt of the background call. Vibe still sends the session transcript as the user message, so the prompt only needs to describe how titles are written (length, language, style).

### Custom Agent Configurations

You can create custom agent configurations for specific use cases (e.g., red-teaming, specialized tasks) by adding agent-specific TOML files in the `~/.vibe/agents/` directory.

To use a custom agent, run Vibe with the `--agent` flag:

```bash
vibe --agent my_custom_agent
```

Vibe will look for a file named `my_custom_agent.toml` in the agents directory and apply its configuration.

Example custom agent configuration (`~/.vibe/agents/redteam.toml`):

```toml
# Custom agent configuration for red-teaming
active_model = "mistral-medium-3.5"
system_prompt_id = "redteam"

# Disable some tools for this agent
disabled_tools = ["edit", "write_file"]

# Override tool permissions for this agent
[tools.bash]
permission = "always"

[tools.read]
permission = "always"
```

Note: This implies that you have set up a redteam prompt named `~/.vibe/prompts/redteam.md`.

### Tool Management

The built-in shell surface is controlled by the `managed_shell_tools_enabled` config
field and the `vibe_cli_managed_shell_tools` GrowthBook experiment. The default variant
keeps the legacy one-shot `bash` tool, including its existing Windows behavior.
The managed variant exposes OS-native shell tools:
POSIX systems, including WSL where Vibe runs as Linux, get managed `bash`,
`bash_output`, `bash_stdin`, `bash_sessions`, and `bash_log_file`; native Windows
gets `git_bash`, `git_bash_output`, `git_bash_stdin`, `git_bash_sessions`, and
`git_bash_log_file` when Git Bash is available. If Git Bash is unavailable,
native Windows falls back to `powershell`, `powershell_output`,
`powershell_stdin`, `powershell_sessions`, and `powershell_log_file`.

Managed shell sessions return a `session_id`, inline output, a cursor for polling
more output, and a log path under `~/.vibe/shell-tool/sessions/`. Long-running
commands can be left alive with `background = true`, and interactive commands can
be driven with the matching stdin tool.

POSIX `bash` reads permissions, allowlists, and denylists from `[tools.bash]`.
Native Windows `git_bash` reads them from `[tools.git_bash]`; native Windows
`powershell` reads them from `[tools.powershell]`. Neither Windows tool reads
`[tools.bash]`. Git Bash is preferred when Vibe can resolve a usable `bash.exe`
from PATH, Git for Windows, or standard Git install locations. If Git Bash is
unavailable, the PowerShell resolution order is `pwsh.exe`, then
`powershell.exe`. `cmd.exe` is not used by the managed Windows shell tools.

```toml
[tools.git_bash]
permission = "ask"
shell = "C:\\Program Files\\Git\\bin\\bash.exe"

[tools.powershell]
permission = "ask"
shell = "powershell.exe"
```

The rollout assignment is server-managed and is not a `config.toml` option.

`enable_background_processes = false` takes background processes (`tools.process`
in `run_typescript`) away from the agent on the default runtime: its commands then
run to completion in the foreground, and the system prompt no longer describes
process tools. It defaults to `true`.

`enable_subagents = false` likewise takes subagents (`tools.subagent` in
`run_typescript`) away from the agent on the default runtime: the system prompt
no longer describes them, and the agent works alone. It defaults to `true`.

#### Enable/Disable Tools with Patterns

You can control which tools are active using `enabled_tools` and `disabled_tools`.
These fields support exact names, glob patterns, and regular expressions.
When both are set, `enabled_tools` first narrows the tool set, then
`disabled_tools` removes matching tools from that set.

Examples:

```toml
# Only enable tools that start with "serena_" (glob)
enabled_tools = ["serena_*"]

# Regex (prefix with re:) — matches full tool name (case-insensitive)
enabled_tools = ["re:^serena_.*$"]

# Disable a group with glob; everything else stays enabled
disabled_tools = ["mcp_*", "grep"]
```

Notes:

- MCP tool names use underscores, e.g., `serena_list` not `serena.list`.
- Regex patterns are matched against the full tool name using fullmatch.

### MCP Server Configuration

You can configure MCP (Model Context Protocol) servers to extend Vibe's capabilities. Add MCP server configurations under the `mcp_servers` section:

Remote MCP servers can be added non-interactively from the shell. Static auth
is selected when `--api-key-env` or `--header` is provided; otherwise the
server uses OAuth and starts browser login by default.

```bash
vibe mcp add mistralai \
  --url https://api.mistral.ai/mcp \
  --transport streamable-http \
  --api-key-env MISTRAL_API_KEY

vibe mcp add linear \
  --url https://mcp.linear.app/mcp

vibe mcp remove mistralai
```

Use `--no-login` to persist an OAuth server without starting login. Static auth
also supports repeatable `--header`, `--api-key-header`, `--api-key-format`,
`--startup-timeout-sec`, and `--tool-timeout-sec`. Run `vibe mcp add --help`
for the complete command reference. `vibe mcp remove <name>` removes the server
from the user configuration. Removing an OAuth server also deletes its stored
tokens, client information, and configuration fingerprint when available.

With `VIBE_CLI` set to `rust`, shell `mcp add` uses the OAuth-only
`/mcp add` syntax: `vibe mcp add https://mcp.linear.app/mcp --name linear
--no-login`. It accepts `--scope` (repeatable), `--transport`, and
`--allow-insecure-http`; without `--no-login`, it starts browser login. Both
`add` and `remove NAME` update the user configuration without opening a chat
session. For stdio or static-auth additions, set `VIBE_CLI` to `python` and run
`vibe mcp add` with the flags above.

Hosted OAuth MCP servers can also be added from inside Vibe:

```text
/mcp add https://mcp.linear.app/mcp
/mcp add https://mcp.example.com/mcp --name docs --scope read --transport http --no-login
```

`/mcp add` is OAuth-only. It writes `auth.type = "oauth"` with optional
scopes and starts login by default. It uses `transport = "streamable-http"`
unless you pass `--transport http`. Pass `--no-login` to add the server without
starting OAuth login. The shortcut supports `streamable-http` and `http`
transports.

```toml
# Example MCP server configurations
[[mcp_servers]]
name = "my_http_server"
transport = "http"
url = "http://localhost:8000"

[mcp_servers.auth]
type = "static"
headers = { "X-Client" = "vibe" }
api_key_env = "MY_API_KEY_ENV_VAR"
api_key_header = "Authorization"
api_key_format = "Bearer {token}"

[[mcp_servers]]
name = "my_streamable_server"
transport = "streamable-http"
url = "http://localhost:8001"

[mcp_servers.auth]
type = "static"
headers = { "X-Client" = "vibe" }

[[mcp_servers]]
name = "fetch_server"
transport = "stdio"
command = "uvx"
args = ["mcp-server-fetch"]
env = { "DEBUG" = "1", "LOG_LEVEL" = "info" }
```

Supported transports:

- `http`: Standard HTTP transport
- `streamable-http`: HTTP transport with streaming support
- `stdio`: Standard input/output transport (for local processes)

Key fields:

- `name`: A short alias for the server (used in tool names)
- `transport`: The transport type
- `url`: Base URL for HTTP transports
- `headers`: Additional HTTP headers
- `api_key_env`: Environment variable containing the API key
- `command`: Command to run for stdio transport
- `args`: Additional arguments for stdio transport
- `startup_timeout_sec`: Timeout in seconds for the server to start and initialize (default 10s)
- `tool_timeout_sec`: Timeout in seconds for tool execution (default 60s)
- `env`: Environment variables to set for the MCP server of transport type stdio

HTTP MCP servers can use either static auth or OAuth. Both use an `auth` block;
legacy top-level `api_key_env` / `headers` keys are still accepted and promoted
to static auth when Vibe loads the configuration.

```toml
[[mcp_servers]]
name = "linear"
transport = "streamable-http"
url = "https://mcp.linear.app/mcp"

[mcp_servers.auth]
type = "oauth"
scopes = []
```

MCP tools are named using the pattern `{server_name}_{tool_name}` and can be configured with permissions like built-in tools:

```toml
# Configure permissions for specific MCP tools
[tools.fetch_server_get]
permission = "always"

[tools.my_http_server_query]
permission = "ask"
```

MCP server configurations support additional features:

- **Environment variables**: Set environment variables for MCP servers
- **Custom timeouts**: Configure startup and tool execution timeouts

Example with environment variables and timeouts:

```toml
[[mcp_servers]]
name = "my_server"
transport = "http"
url = "http://localhost:8000"
env = { "DEBUG" = "1", "LOG_LEVEL" = "info" }
startup_timeout_sec = 15
tool_timeout_sec = 120
```

### Hooks

Hooks wire arbitrary shell commands into Vibe's lifecycle to gate, audit, or rewrite agent behavior. No flag is required — declaring a hook is enough.

Declared in `<project>/.vibe/hooks.toml` (project, loaded first; trusted only) and `~/.vibe/hooks.toml` (user-global, loaded second; duplicates by `name` lose to the project entry):

```toml
[[hooks]]
name = "deny-rm-rf"
type = "pre_tool"
match = "bash"                       # tool-name matcher (fnmatch glob + `re:` regex escape, case-insensitive)
command = "uv run python /path/to/guard-bash"
timeout = 60.0                       # seconds; default 60 for all hooks
strict = false                       # tool hooks only: turn failures into denials (pre) / text-clears (post)
description = "Reject dangerous shell commands."
```

Subagents inherit the parent's hook config so policies apply transitively.

#### Common ground

Every hook is spawned with a JSON invocation on **stdin** (UTF-8) containing the session context: `session_id`, `parent_session_id`, `transcript_path`, `cwd`, plus `hook_event_name` discriminating the hook type. Tool hooks add tool-specific fields (below).

Every hook signals back via its **exit code** and **stdout**. The contract on stdout is strict: either empty (do nothing), or a JSON object matching the schema below. Use **stderr** for diagnostics / debug logs.

- **Exit `0`, empty stdout** — passthrough.
- **Exit `0`, valid JSON object on stdout** — structured response. Universal top-level fields:
  - `system_message` (string, optional) — shown to the user in the UI.
  - `decision` (`"allow"` | `"deny"`, optional, default `"allow"`) — the effect of `"deny"` depends on the hook type.
  - `reason` (string, optional) — accompanies `decision: "deny"`.
  - Event-specific payload under `hook_specific_output`.
- **Exit `0`, non-empty but non-conforming stdout** (free-form text, broken JSON, JSON scalar/array, schema mismatch) — treated as a hook failure with the parse error as the message. Warning by default; escalated to deny / clear under `strict = true` on a tool hook.
- **Any non-zero exit / timeout / spawn failure** — same failure path. Diagnostic taken from stderr (falling back to stdout, then the exit code).

Unknown JSON fields are tolerated at every level (forward-compatible). Fields that aren't meaningful for the current hook type are silently ignored.

#### `post_agent`

Fires after every assistant turn that ends without pending tool calls.

- **Receives** (in addition to the session context): no extra fields.
- **Can return**:
  - `decision: "deny"` + `reason` — `reason` is injected as a new user message asking for a retry. Capped at **3 retries per hook per user turn**; further denies become terminal warnings.
  - `system_message` — UI-only.

#### `pre_tool`

Fires per tool call, **before** the user permission prompt. First deny short-circuits remaining `pre_tool` hooks for that call.

- **Receives** (in addition to the session context): `tool_name`, `tool_call_id`, `tool_input` (the model's raw arguments).
- **Can return**:
  - `decision: "deny"` + `reason` — denies the tool call; `reason` becomes the tool error the LLM sees.
  - `hook_specific_output.tool_input` (object) — **full replacement** of the model's arguments. Re-validated against the tool's schema (validation failure → synthesized denial). Rewrites compose left-to-right across hooks. The rewritten arguments are also what the permission prompt displays, what the tool runs with, and what subsequent LLM turns see on the assistant message.
  - `system_message` — UI-only.

#### `post_tool`

Fires per tool call **if and only if the tool body actually ran**. `tool_status` is `success`, `failure`, or `cancelled` (cancellation during the tool body — cancellation is shielded so audit hooks still run). Does not fire when the tool never executed: `pre_tool` denial, user denial at the approval prompt, permission `NEVER`, or cancellation before the body started.

- **Receives** (in addition to the session context): `tool_name`, `tool_call_id`, `tool_input` (post-rewrite), `tool_status`, `tool_output` (structured result dict; null on failure), `tool_output_text` (the running text the LLM will see, mutable by prior hooks), `tool_error`, `duration_ms`.
- **Can return**:
  - `decision: "deny"` + `reason` — replaces `tool_output_text` with `reason`. Pipeline continues; subsequent hooks see the replacement.
  - `hook_specific_output.additional_context` (string) — **appended** (with a `\n` separator) to `tool_output_text`. Composes with a same-hook deny: deny replaces first, then `additional_context` is appended to the replacement.
  - `system_message` — UI-only.

### Session Management

#### Session Continuation and Resumption

Vibe supports continuing from previous sessions:

- **`--continue`** or **`-c`**: Continue from the most recent saved session
- **`--resume`**: Open an interactive session picker
- **`--resume SESSION_ID`**: Resume a specific session by ID (supports partial matching)
- **`/resume`** or **`/continue`**: Open the session picker from inside Vibe; press `D` twice to delete a local saved session. The active session cannot be deleted from this picker.

```bash
# Continue from last session
vibe --continue

# Open session picker
vibe --resume

# Resume specific session
vibe --resume abc123
```

Session logging must be enabled in your configuration for these features to work.

The first user message pins the resolved model to that session. Resuming keeps
the pinned model even if your configured default changes. `/model` uses the
normal config persistence target and updates an existing session override
immediately; the session file is synchronized when the next user message is
sent. An explicit persistent target selected through `/config` changes only
that layer. `/clear` starts a new, unpinned conversation that follows the
current config. If a selected model is no longer configured, Vibe falls back
to the current default model.

#### Working Directory Control

Use the `--workdir` option to specify a working directory:

```bash
vibe --workdir /path/to/project
```

This is useful when you want to run Vibe from a different location than your current directory.

Use `--add-dir` (repeatable) to make additional directories available to the agent for the duration of the session:

```bash
vibe --add-dir /path/to/other-project --add-dir /path/to/library
```

Each path is implicitly trusted (no trust prompt) and contributes its `AGENTS.md` and `.vibe/` configuration (tools, skills, agents, prompts, hooks) to the session. File-tool permissions treat each `--add-dir` path the same way as your primary working directory — reads and writes inside them don't require the "outside workdir" prompt. Nested paths collapse: passing `/repo` and `/repo/sub` is equivalent to passing just `/repo`.

Use `--worktree NAME` to create (or reuse) a [git worktree](<https://git-scm.com/docs/git-worktree>) and run inside it:

```bash
vibe --worktree my-feature
```

The worktree lives under `$VIBE_HOME/worktrees/<repo-name>-<repo-hash>/NAME` and is checked out on a branch named `NAME` (created if it doesn't exist, attached if it does). Vibe `cd`s into it before the session starts and trusts it for the session (no trust prompt). If you start Vibe from a subdirectory, Vibe enters the matching subdirectory inside the worktree.

Existing worktrees are reused only when they belong to the same git repository and are checked out on branch `NAME`; otherwise Vibe exits with an error instead of running in the wrong checkout.

Pass `--worktree` with no name to have Vibe name one for you:

```bash
vibe "Fix the login bug" --worktree     # -> fix-the-login-bug, on vibe/fix-the-login-bug
vibe --worktree                         # no prompt -> a random slug, e.g. brave-quiet-otter
```

The name comes from your prompt, shortened to whole words. Without a prompt — or when the prompt has nothing usable in it, such as emoji only — Vibe generates a random slug instead. Unlike the named form, this never reuses an existing worktree: Vibe claims a free name, adding `-2`, `-3` and so on if needed, so two sessions started at once can never land in the same checkout. The branch is always `vibe/<name>`, matching the worktrees Le Chat Desktop creates.

Order matters, because `--worktree` takes an optional value: `vibe --worktree "Fix the login bug"` reads the prompt as the *name*. Put the prompt first, or separate it with `--`:

```bash
vibe --worktree -- "Fix the login bug"
```

Automatic cleanup only applies to worktrees Vibe created this run, and only after a session actually started — a startup failure (bad config, `--continue` with no sessions) never deletes anything, and a reused worktree is always left in place. When an interactive session exits, Vibe removes the worktree directory automatically if there are no uncommitted changes, untracked files, or commits beyond the commit where the worktree session started. If any of those exist, Vibe asks whether to keep or remove the worktree. When Vibe created the branch it is deleted alongside the worktree; a branch that already existed and was merely attached is kept unless you confirm its deletion. Keeping preserves the directory and branch so you can return later; removing force-deletes them, discarding changes, untracked files, and commits. Programmatic runs (`vibe -p ... --worktree NAME`) do not clean up automatically because there is no exit prompt; remove them manually with `git worktree remove`. `--worktree` is ignored with `--setup` and `--check-upgrade`.

Sessions are scoped per directory, so `-c`/`--continue` and the `--resume` picker only see sessions started inside that worktree. To carry a session across worktrees, resume it explicitly by ID with `--resume <ID>`.

#### Worktree ownership

Whichever way a worktree is created, Vibe writes an ownership record beside it under `$VIBE_HOME/worktrees/.claims/<repo-name>-<repo-hash>/<name>/`, recording the branch, the commit the session started from, and whether Vibe created the branch. Nothing is ever removed without one: a worktree you made yourself, or one whose record is missing or unreadable, is left alone.

The record directory also holds a marker per session currently working in the worktree. Sessions from different clients run in separate processes with nothing shared between them, so a marker is the only evidence that someone else is still in there. A worktree with any marker left is kept. A process killed outright leaves its marker behind and the worktree survives, which is the direction worth failing in.

The app-server never removes a worktree on its own. Closing a session does not count: the desktop app releases an idle session's process a second after each turn to reclaim it, and the session stays live and resumable, so its worktree outlives that. A worktree that exists is removed in exactly one situation — **you delete its session**. It still has to be one Vibe created, held by nobody else, and free of uncommitted changes, untracked files, and commits made since the session began; anything else is kept and logged.

Two things are cleaned up without asking, neither of which is a worktree you could have worked in. A session whose very first turn never completed has its worktree rolled back, because such a session is never published and leaves no session file — there is nothing to return to. And a reservation that never became a worktree, an empty directory left by a claim whose `git worktree add` did not land, is discarded the next time a session starts in that repo.

The cost of that conservatism is that a worktree whose app-server was killed outright stays on disk, holding a marker for a session that no longer exists. Removing it is a judgement about whether you are finished with the work, which only you can make.

On Windows, Vibe resolves an absolute Git executable for automatic repository
inspection and ignores executables inside the current project. Set
`GIT_PYTHON_GIT_EXECUTABLE` to an absolute path when using a custom or portable
Git installation. Vibe still starts when no trusted Git executable is available;
only Git-dependent metadata and features are unavailable.

### Update Settings

Vibe checks for updates at most once per day during a session. When Vibe is installed from a package index with `uv tool install`, the check runs `uv tool list --outdated`, so your uv settings (such as `exclude-newer`) decide which version is offered, and an exact version pin (`mistral-vibe==X`) suppresses the offer. Other installs (git or local path, Homebrew, pip, or a uv too old for `--outdated`) query PyPI. When a newer version is found, the next launch shows an update prompt before opening the chat, offering to either update immediately (via `uv tool upgrade mistral-vibe` or `brew upgrade mistral-vibe`) or continue with the current version. Vibe reports success only once `vibe --version` shows the new version. If a uv install stays on the old version, for example because of a version pin, Vibe asks whether to run `uv tool install --force mistral-vibe@latest`, which drops the pin, extras and `--with` packages.

Run `vibe --check-upgrade` to check immediately, prompt to install a newer version if one exists, and exit.

To disable the daily check entirely, add this to your `config.toml`:

```toml
enable_update_checks = false
```

### Notification Settings

Vibe can notify you when the agent needs your attention (awaiting approval, asking a question, or task complete). This is useful when you switch to another window while the agent works.

To disable notifications:

```toml
enable_notifications = false
```

### Custom Vibe Home Directory

By default, Vibe stores its configuration in `~/.vibe/`. You can override this by setting the `VIBE_HOME` environment variable:

```bash
export VIBE_HOME="/path/to/custom/vibe/home"
```

This affects where Vibe looks for:

- `config.toml` - Main configuration
- `.env` - API keys
- `connector_bootstrap_cache.json` - Short-lived connector discovery cache
- `agents/` - Custom agent configurations
- `prompts/` - Custom system and compaction prompts
- `tools/` - Custom tools
- `logs/` - Session logs

Custom tools will be deprecated in a future release. Prefer skills for new
extensions; Vibe can help migrate existing custom tools to skills.

### Logging

Vibe writes structured logs to `~/.vibe/logs/vibe.log`. Use `/log-level` to open an interactive picker that lets you set the session override and/or persist a level to `config.toml`. You can also set `log_level` directly in `config.toml` or via the `/config` screen.

Valid levels: `DEBUG`, `INFO`, `WARNING` (default), `ERROR`, `CRITICAL`.

Precedence: session override > `LOG_LEVEL` env var > `log_level` in config.toml > default.

The `LOG_LEVEL` environment variable overrides the config value at startup. Use `DEBUG_MODE=true` to force `DEBUG` at startup.

## Editors/IDEs

Mistral Vibe can be used in text editors and IDEs that support [Agent Client Protocol](<https://agentclientprotocol.com/overview/clients>). See the [ACP Setup documentation](<在编辑器中接入 Mistral Vibe ACP.md>) for setup instructions for various editors and IDEs.

## Resources

- [CHANGELOG](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/CHANGELOG.md>) - See what's new in each version
- [CONTRIBUTING](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/CONTRIBUTING.md>) - Guidelines for feature requests, feedback and bug reports

## Data collection & usage

Use of Vibe is subject to our [Privacy Policy](<https://legal.mistral.ai/terms/privacy-policy>) and may include the collection and processing of data related to your use of the service, such as usage data, to operate, maintain, and improve Vibe. You can disable telemetry and crash reporting in your `config.toml` by setting `enable_telemetry = false`.


## License

Copyright 2025 Mistral AI

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the [LICENSE](<https://github.com/mistralai/mistral-vibe/blob/4ae5c5955da8e761318916bdb1a7f46f0b50c7d0/LICENSE>) file for the full license text.


</details>
