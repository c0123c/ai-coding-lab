# 使用 AGENTS.md 添加项目指令

介绍全局与项目规则的发现和分层加载、审查规则、备用文件名、配置验证与加载故障排查。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://learn.chatgpt.com/docs/agent-configuration/agents-md>)）；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://learn.chatgpt.com/docs/agent-configuration/agents-md.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/使用 AGENTS.md 添加项目指令（存档2026-10-10-332）.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 自定义指令AGENTS.md

> 完整的文档索引，请参见[llms.txt](<https://learn.chatgpt.com/llms.txt>)。通过将 `.md` 附加到页面 URL 可以获得文档页面的 Markdown 版本。

Codex 在执行任何工作之前读取 `AGENTS.md` 文件。通过将全局指导与特定于项目的覆盖进行分层，无论您打开哪个存储库，您都可以以一致的期望开始每项任务。

## Codex 如何发现指导

Codex 在启动时构建指令链（每次运行一次；在 TUI 中，这通常意味着每个启动会话一次）。发现遵循以下优先顺序：

1. **全局范围：** 在您的 Codex 主目录中（默认为 `~/.codex`，除非您设置 `CODEX_HOME`），Codex 读取 `AGENTS.override.md`（如果存在）。否则，Codex 读取为 `AGENTS.md`。 Codex 仅使用此级别的第一个非空文件。
2. **项目范围：**从项目根目录（通常是 Git 根目录）开始，Codex 向下走到当前工作目录。如果Codex找不到项目根目录，则仅检查当前目录。在路径上的每个目录中，它会检查 `AGENTS.override.md`，然后检查 `AGENTS.md`，然后检查 `project_doc_fallback_filenames` 中的任何后备名称。 Codex 每个目录最多包含一个文件。
3. **合并顺序：** Codex从根向下连接文件，用空行将它们连接起来。更接近当前目录的文件会覆盖之前的指导，因为它们稍后出现在组合提示中。

一旦组合大小达到 `project_doc_max_bytes` 定义的限制（默认为 32 KiB），Codex 将跳过空文件并停止添加文件。有关这些旋钮的详细信息，请参阅[项目说明发现](<https://learn.chatgpt.com/docs/config-file/config-advanced#project-instructions-discovery>)。当达到上限时，提高限制或跨嵌套目录分割指令。

## 创建全球指导

在您的 Codex 主目录中创建持久默认值，以便每个存储库都继承您的工作协议。

1. 确保目录存在：

```bash
   mkdir -p ~/.codex
```

2. 创建具有可重用首选项的 `~/.codex/AGENTS.md`：

```md
   # ~/.codex/AGENTS.md

   ## Working agreements

   - Always run `npm test` after modifying JavaScript files.
   - Prefer `pnpm` when installing dependencies.
   - Ask for confirmation before adding new production dependencies.
```

3. 在任何地方运行 Codex 以确认它加载文件：

```bash
   codex --ask-for-approval never "Summarize the current instructions."
```

预期：Codex 在提出工作之前引用 `~/.codex/AGENTS.md` 中的项目。

当您需要临时全局覆盖而不删除基本文件时，请使用 `~/.codex/AGENTS.override.md`。删除覆盖以恢复共享指导。

## 图层项目说明

存储库级文件使 Codex 了解项目规范，同时仍然继承您的全局默认值。

1. 在您的存储库根目录中，添加涵盖基本设置的 `AGENTS.md`：

```md
   # AGENTS.md

   ## Repository expectations

   - Run `npm run lint` before opening a pull request.
   - Document public utilities in `docs/` when you change behavior.
```

2. 当特定团队需要不同的规则时，在嵌套目录中添加覆盖。例如，在`services/payments/`内创建`AGENTS.override.md`：

```md
   # services/payments/AGENTS.override.md

   ## Payments service rules

   - Use `make test-payments` instead of `npm test`.
   - Never rotate API keys without notifying the security channel.
```

3. 从付款目录启动 Codex：

```bash
   codex --cd services/payments --ask-for-approval never "List the instruction sources you loaded."
```

预期：Codex 首先报告全局文件，然后报告存储库根 `AGENTS.md`，最后报告付款覆盖。

Codex 一旦到达当前目录就会停止搜索，因此将覆盖尽可能靠近专门的工作。

以下是添加全局文件和特定于付款的覆盖后的示例存储库：

<FileTree
  class="mt-4"
  tree={[
    {
      name: "AGENTS.md",
      comment: "Repository expectations",
      highlight: true,
    },
    {
      name: "services/",
      open: true,
      children: [
        {
          name: "payments/",
          open: true,
          children: [
            {
              name: "AGENTS.md",
              comment: "Ignored because an override exists",
            },
            {
              name: "AGENTS.override.md",
              comment: "Payments service rules",
              highlight: true,
            },
            { name: "README.md" },
          ],
        },
        {
          name: "search/",
          children: [{ name: "AGENTS.md" }, { name: "…", placeholder: true }],
        },
      ],
    },
  ]}
/>

## 添加代码审查规则

对于 [Codex 代码审查在 GitHub](<https://learn.chatgpt.com/docs/third-party/github#customize-what-codex-reviews>),
添加一个 `## Code Review Rules` 部分到 `AGENTS.md` 最接近代码的是
规则管辖。将存储库范围的检查放在根和特定于服务的位置
检查嵌套文件。

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

保持规则简洁，解释标记行为以及任何安全路径或 
 异常，并保留 CI 的格式和 lint 检查。请参阅[自定义内容
Codex评论](<https://learn.chatgpt.com/docs/third-party/github#customize-what-codex-reviews>)以获取
设置和规则编写指南。

## 自定义后备文件名

如果您的存储库已使用不同的文件名（例如 `TEAM_GUIDE.md`），请将其添加到后备列表中，以便 Codex 将其视为指令文件。

1. 编辑您的 Codex 配置：

```toml
   # ~/.codex/config.toml
   project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
   project_doc_max_bytes = 65536
```

2. 重新启动 Codex 或运行新命令以便加载更新的配置。

现在Codex按以下顺序检查每个目录：`AGENTS.override.md`，`AGENTS.md`，`TEAM_GUIDE.md`，`.agents.md`。指令发现时将忽略不在此列表中的文件名。较大的字节限制允许在截断之前进行更多组合指导。

后备列表到位后，Codex 将备用文件视为指令：

<FileTree
  class="mt-4"
  tree={[
    {
      name: "TEAM_GUIDE.md",
      comment: "Detected via fallback list",
      highlight: true,
    },
    {
      name: ".agents.md",
      comment: "Fallback file in root",
    },
    {
      name: "support/",
      open: true,
      children: [
        {
          name: "AGENTS.override.md",
          comment: "Overrides fallback guidance",
          highlight: true,
        },
        {
          name: "playbooks/",
          children: [{ name: "…", placeholder: true }],
        },
      ],
    },
  ]}
/>

当您需要不同的配置文件（例如特定于项目的自动化用户）时，请设置 `CODEX_HOME` 环境变量：

```bash
CODEX_HOME=$(pwd)/.codex codex exec "List active instruction sources"
```

预期：输出列出与自定义 `.codex` 目录相关的文件。

## 验证您的设置

- 从存储库根运行 `codex --ask-for-approval never "Summarize the current instructions."`。 Codex 应按优先顺序回显全局文件和项目文件中的指导。
- 使用 `codex --cd subdir --ask-for-approval never "Show which instruction files are active."` 确认嵌套覆盖替换更广泛的规则。
- 要审核加载的指令文件 Codex，请选择使用 `codex -c log_dir=./.codex-log` 进入纯文本 TUI 日志并检查 `./.codex-log/codex-tui.log`，或者如果启用了会话日志记录，则检查最新的 `session-*.jsonl` 文件。
- 如果指令看起来过时，请在目标目录中重新启动 Codex。 Codex 在每次运行时（以及在每个 TUI 会话开始时）重建指令链，因此无需手动清除缓存。

## 解决发现问题

- **没有加载任何内容：**验证您是否位于预期的存储库中，并且`codex status`报告了您期望的工作区根目录。确保说明文件包含内容； Codex 忽略空文件。
- **出现错误指导：**在目录树中或在您的Codex主目录下查找`AGENTS.override.md`。重命名或删除覆盖以回退到常规文件。
- **Codex 忽略后备名称：** 确认您在 `project_doc_fallback_filenames` 中列出的名称没有拼写错误，然后重新启动 Codex 以使更新的配置生效。
- **指令被截断：**提升`project_doc_max_bytes`或跨嵌套目录分割大文件以保持关键指导完整。
- **配置文件混乱：**在启动Codex之前运行`echo $CODEX_HOME`。非默认值将 Codex 指向与您编辑的主目录不同的主目录。

## 后续步骤

- 访问官方[AGENTS.md](<https://agents.md>)网站了解更多信息。
- 查看 [ 提示 Codex](<https://learn.chatgpt.com/docs/prompting>)，了解与持续指导相匹配的对话模式。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Custom instructions with AGENTS.md

> For the complete documentation index, see [llms.txt](<https://learn.chatgpt.com/llms.txt>). Markdown versions of documentation pages are available by appending `.md` to the page URL.

Codex reads `AGENTS.md` files before doing any work. By layering global guidance with project-specific overrides, you can start each task with consistent expectations, no matter which repository you open.

## How Codex discovers guidance

Codex builds an instruction chain when it starts (once per run; in the TUI this usually means once per launched session). Discovery follows this precedence order:

1. **Global scope:** In your Codex home directory (defaults to `~/.codex`, unless you set `CODEX_HOME`), Codex reads `AGENTS.override.md` if it exists. Otherwise, Codex reads `AGENTS.md`. Codex uses only the first non-empty file at this level.
2. **Project scope:** Starting at the project root (typically the Git root), Codex walks down to your current working directory. If Codex cannot find a project root, it only checks the current directory. In each directory along the path, it checks for `AGENTS.override.md`, then `AGENTS.md`, then any fallback names in `project_doc_fallback_filenames`. Codex includes at most one file per directory.
3. **Merge order:** Codex concatenates files from the root down, joining them with blank lines. Files closer to your current directory override earlier guidance because they appear later in the combined prompt.

Codex skips empty files and stops adding files once the combined size reaches the limit defined by `project_doc_max_bytes` (32 KiB by default). For details on these knobs, see [Project instructions discovery](<https://learn.chatgpt.com/docs/config-file/config-advanced#project-instructions-discovery>). Raise the limit or split instructions across nested directories when you hit the cap.

## Create global guidance

Create persistent defaults in your Codex home directory so every repository inherits your working agreements.

1. Ensure the directory exists:

```bash
   mkdir -p ~/.codex
```

2. Create `~/.codex/AGENTS.md` with reusable preferences:

```md
   # ~/.codex/AGENTS.md

   ## Working agreements

   - Always run `npm test` after modifying JavaScript files.
   - Prefer `pnpm` when installing dependencies.
   - Ask for confirmation before adding new production dependencies.
```

3. Run Codex anywhere to confirm it loads the file:

```bash
   codex --ask-for-approval never "Summarize the current instructions."
```

   Expected: Codex quotes the items from `~/.codex/AGENTS.md` before proposing work.

Use `~/.codex/AGENTS.override.md` when you need a temporary global override without deleting the base file. Remove the override to restore the shared guidance.

## Layer project instructions

Repository-level files keep Codex aware of project norms while still inheriting your global defaults.

1. In your repository root, add an `AGENTS.md` that covers basic setup:

```md
   # AGENTS.md

   ## Repository expectations

   - Run `npm run lint` before opening a pull request.
   - Document public utilities in `docs/` when you change behavior.
```

2. Add overrides in nested directories when specific teams need different rules. For example, inside `services/payments/` create `AGENTS.override.md`:

```md
   # services/payments/AGENTS.override.md

   ## Payments service rules

   - Use `make test-payments` instead of `npm test`.
   - Never rotate API keys without notifying the security channel.
```

3. Start Codex from the payments directory:

```bash
   codex --cd services/payments --ask-for-approval never "List the instruction sources you loaded."
```

   Expected: Codex reports the global file first, the repository root `AGENTS.md` second, and the payments override last.

Codex stops searching once it reaches your current directory, so place overrides as close to specialized work as possible.

Here is a sample repository after you add a global file and a payments-specific override:

<FileTree
  class="mt-4"
  tree={[
    {
      name: "AGENTS.md",
      comment: "Repository expectations",
      highlight: true,
    },
    {
      name: "services/",
      open: true,
      children: [
        {
          name: "payments/",
          open: true,
          children: [
            {
              name: "AGENTS.md",
              comment: "Ignored because an override exists",
            },
            {
              name: "AGENTS.override.md",
              comment: "Payments service rules",
              highlight: true,
            },
            { name: "README.md" },
          ],
        },
        {
          name: "search/",
          children: [{ name: "AGENTS.md" }, { name: "…", placeholder: true }],
        },
      ],
    },
  ]}
/>

## Add code review rules

For [Codex code review in GitHub](<https://learn.chatgpt.com/docs/third-party/github#customize-what-codex-reviews>),
add a `## Code Review Rules` section to the `AGENTS.md` closest to the code the
rules govern. Put repository-wide checks at the root and service-specific
checks in a nested file.

```md
## Code Review Rules

### Experiment cohorts

- Do not filter treatment comparisons on post-exposure behavior, including conversion or retention.
  Safe path: build cohorts from assignment or exposure; report conversion as an outcome.
```

Keep rules concise, explain the behavior to flag and any safe path or
exception, and reserve formatting and lint checks for CI. See [Customize what
Codex reviews](<https://learn.chatgpt.com/docs/third-party/github#customize-what-codex-reviews>) for
setup and rule-writing guidance.

## Customize fallback filenames

If your repository already uses a different filename (for example `TEAM_GUIDE.md`), add it to the fallback list so Codex treats it like an instructions file.

1. Edit your Codex configuration:

```toml
   # ~/.codex/config.toml
   project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
   project_doc_max_bytes = 65536
```

2. Restart Codex or run a new command so the updated configuration loads.

Now Codex checks each directory in this order: `AGENTS.override.md`, `AGENTS.md`, `TEAM_GUIDE.md`, `.agents.md`. Filenames not on this list are ignored for instruction discovery. The larger byte limit allows more combined guidance before truncation.

With the fallback list in place, Codex treats the alternate files as instructions:

<FileTree
  class="mt-4"
  tree={[
    {
      name: "TEAM_GUIDE.md",
      comment: "Detected via fallback list",
      highlight: true,
    },
    {
      name: ".agents.md",
      comment: "Fallback file in root",
    },
    {
      name: "support/",
      open: true,
      children: [
        {
          name: "AGENTS.override.md",
          comment: "Overrides fallback guidance",
          highlight: true,
        },
        {
          name: "playbooks/",
          children: [{ name: "…", placeholder: true }],
        },
      ],
    },
  ]}
/>

Set the `CODEX_HOME` environment variable when you want a different profile, such as a project-specific automation user:

```bash
CODEX_HOME=$(pwd)/.codex codex exec "List active instruction sources"
```

Expected: The output lists files relative to the custom `.codex` directory.

## Verify your setup

- Run `codex --ask-for-approval never "Summarize the current instructions."` from a repository root. Codex should echo guidance from global and project files in precedence order.
- Use `codex --cd subdir --ask-for-approval never "Show which instruction files are active."` to confirm nested overrides replace broader rules.
- To audit which instruction files Codex loaded, opt into a plaintext TUI log with `codex -c log_dir=./.codex-log` and check `./.codex-log/codex-tui.log`, or inspect the most recent `session-*.jsonl` file if you enabled session logging.
- If instructions look stale, restart Codex in the target directory. Codex rebuilds the instruction chain on every run (and at the start of each TUI session), so there is no cache to clear manually.

## Troubleshoot discovery issues

- **Nothing loads:** Verify you are in the intended repository and that `codex status` reports the workspace root you expect. Ensure instruction files contain content; Codex ignores empty files.
- **Wrong guidance appears:** Look for an `AGENTS.override.md` higher in the directory tree or under your Codex home. Rename or remove the override to fall back to the regular file.
- **Codex ignores fallback names:** Confirm you listed the names in `project_doc_fallback_filenames` without typos, then restart Codex so the updated configuration takes effect.
- **Instructions truncated:** Raise `project_doc_max_bytes` or split large files across nested directories to keep critical guidance intact.
- **Profile confusion:** Run `echo $CODEX_HOME` before launching Codex. A non-default value points Codex at a different home directory than the one you edited.

## Next steps

- Visit the official [AGENTS.md](<https://agents.md>) website for more information.
- Review [Prompting Codex](<https://learn.chatgpt.com/docs/prompting>) for conversational patterns that pair well with persistent guidance.


</details>
