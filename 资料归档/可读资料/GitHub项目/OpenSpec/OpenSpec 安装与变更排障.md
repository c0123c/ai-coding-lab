# OpenSpec 安装与变更排障

按命令不可用、Node.js 版本、工具配置、聊天命令缺失和变更查找等症状给出可能原因及具体修复步骤。

- 来源：OpenSpec
- 资料类型：故障排查指南
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/troubleshooting.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/troubleshooting.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 故障排除

针对具体问题进行具体修复。每个条目都会命名一个症状，用一句话解释可能的原因，并提供解决方案。如果您在这里没有看到您的问题，[FAQ](<OpenSpec 常见问题.md>) 可能会有所帮助，并且 [Discord](<https://discord.gg/YctCnvvshC>) 肯定会有所帮助。

## 安装和设置

### `openspec: command not found`

未安装 CLI，或者您的 shell 找不到它。全局安装并检查：

```bash
npm install -g @fission-ai/openspec@latest
openspec --version
```

如果已安装但仍未找到，则您的全局 npm bin 目录可能不在您的 `PATH` 上。运行 `npm prefix -g` 查看全局包所在的位置：在 macOS 和 Linux 上，二进制文件位于该目录的 `bin/` 中，在 Windows 上，它们直接位于其中。确保该路径位于您的 `PATH` 上。 （`npm bin -g` 已在 npm 9 中删除。）

如果您使用 [AI- 辅助安装 ](<OpenSpec 安装与初始化指南.md#使用-ai-助手安装>)，则这是预期的交接点：该提示告诉您的助手向您显示 `PATH` 更改，而不是编辑您的 shell 启动文件本身。

### “需要 Node.js 20.19.0 或更高版本”

OpenSpec 在节点 20.19.0+ 上运行。检查您的版本并根据需要进行升级：

```bash
node --version
```

如果您使用 bun 安装 OpenSpec，请注意，OpenSpec 仍然 * 在节点上运行 *，因此无论如何，您都需要 `PATH` 上可用的节点 20.19.0+。参见[安装](<OpenSpec 安装与初始化指南.md>)。

### `openspec init` 没有配置我的 AI 工具

Init 询问要设置哪些工具。如果您跳过了工具或想要添加另一个工具，只需再次运行它，或使用非交互式形式：

```bash
openspec init --tools claude,cursor
```

工具 ID 的完整列表位于 [ 支持的工具 ](<OpenSpec 支持的 AI 工具与调用方式.md>) 中。使用 `--tools all` 执行所有操作，使用 `--tools none` 跳过工具设置。

## 命令不显示

如果 `/opsx:propose`（或您的工具的等效工具）没有出现或没有执行任何操作，请按照此列表进行操作。他们被订购最快，最先检查。

1. **您可能走错地方了。**斜线命令位于您的AI助理的聊天中，而不是您的终端中。如果您在 shell 中输入 `/opsx:propose`，那就是问题所在。请参阅[命令如何工作](<OpenSpec 两类命令如何协作.md>)。

2. **重新生成文件。**从项目根目录：

   ```bash
   openspec update
   ```

这会重写您配置的每个工具的技能和命令文件。

说明文件来自 * 安装的 * CLI，因此过时的 CLI 会报告最新的所有内容，而无需编写较新的工作流程。 `openspec update` 现在会检查并提供升级 - 如果您看到它，请接受该提议。

3. **重新启动助手。**大多数工具在启动时扫描技能和命令。新鲜的窗户经常可以做到这一点。

4. **确认文件存在。** 对于Claude Code，检查`.claude/skills/`是否包含`openspec-*`文件夹。其他工具使用自己的目录，全部列在[支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)中。

5. **检查您是否初始化了该项目。**技能是按项目编写的。如果您克隆了存储库或切换了文件夹，请在那里运行 `openspec init` （或 `openspec update`）。

6. **确认您的工具支持命令文件。** Codex、CodeArts、ForgeCode、Hermes、Kimi Code、Mistral Vibe、Zed Agent 和共享`.agents` 目标未生成 `opsx-*` 命令文件；他们使用基于技能的调用，因此 `/opsx` 永远不会为他们自动完成。在Codex中输入`$openspec-propose`，在Kimi代码中输入`/skill:openspec-propose`，在其余部分输入`/openspec-propose`。共享的 `.agents` 目标与供应商无关，因此 `/openspec-propose` 是通用形式，而不是有保证的形式 - 如果您的助手没有回答它，请检查它自己的文档以了解它如何调用技能。 Amazon Q 确实获取命令文件，但将它们加载到其提示库而不是斜线菜单中 - 在那里键入 `@opsx-propose`，而不是 `/opsx`。 [如何调用](<OpenSpec 支持的 AI 工具与调用方式.md#如何调用>)中列出了每个工具的形式。

## 处理变更

### “未找到更改”

该命令无法判断您指的是哪个更改。明确命名它，或检查存在的内容：

```bash
openspec list                    # see active changes
/opsx:apply add-dark-mode        # name the change in chat
```

还要确认您位于正确的项目目录中。

### “没有准备好文物”

每个工件要么已创建，要么因依赖项而被阻止。看看是什么阻碍了：

```bash
openspec status --change <name>
```

然后首先创建缺少的依赖项。记住顺序：提案支持规格和设计；规格和设计共同实现任务。

### `openspec validate` 报告警告或错误

验证会检查您的规格并更改结构问题。阅读消息：它命名文件和问题。

```bash
openspec validate <name>           # validate one item
openspec validate --all            # validate everything
openspec validate --all --strict   # stricter checks, good for CI
openspec validate --archived       # fail if archived changes have unchecked tasks
```

常见原因是缺少必需的部分（例如没有场景的规范）或格式错误的增量标头。修复文件并重新运行。 [CLI 参考文献](<OpenSpec 命令行参考.md#openspec-validate>) 记录了输出格式。

有一条消息值得一提：

```text
MODIFIED "<requirement>" omits scenario(s) the current spec still has: "<scenario>"
```

`MODIFIED` 需求替换了整个需求块，因此它必须包含在更改后仍然存在的每个场景，而不仅仅是您编辑的场景。将命名场景从 `openspec/specs/<capability-path>/spec.md` 复制回增量，并保留路径中的所有域目录。在其他人的更改向同一需求添加了场景之后，这通常会出现在较旧的更改中 - 存档拒绝以任何方式进行更改，并且验证现在在您实施之前会这样说。

### AI 创建了不完整或错误的工件

AI 没有足够的上下文。一些杠杆可以提供帮助：

- 在 `openspec/config.yaml` 中添加项目上下文，以便将您的堆栈和约定注入到每个请求中。请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#项目配置>)。
- 添加每个工件 `rules:` 以获得仅适用于规格等的指导。
- 当您提出建议时，请提供更详细的描述。
- 使用扩展的 `/opsx:continue` 一次创建一个工件并查看每个工件，而不是 `/opsx:ff` 一次完成所有这些工作。

### 存档无法完成，或警告任务未完成

存档不会*阻止*未完成的任务，但它会警告您，因为存档通常意味着工作已完成。如果任务仍然是故意的（您正在提交部分更改），请继续。否则先完成任务。如果您尚未同步，Archive 还会将您的增量规格同步到主要规格中；说“是”，除非你有理由不这样做。

### “用户强制关闭提示并显示 0 null”

有东西跑了 `openspec archive`，没有任何东西可以回答问题 - 一个 AI agent 从工具、CI 作业或任何关闭了标准输入的 shell 调用它。存档要求最多三项确认，而无法答复的确认通常会因该原始消息而失败。

通过 `--yes` 预先回答：

```bash
openspec archive <change-name> --yes
```

保留您已经传递的任何标志 - `--skip-specs` 和 `--no-validate` 更改存档的功能，因此裸露的 `--yes` 重新运行不是相同的命令。当前版本为您命名该标志并打印您可以粘贴的 `Fix:` 行。如果您打算从列表中进行选择，请明确传递更改名称：选择器也需要答案。

如果您运行存档并将其输出重定向到文件或由工具捕获，并且 *did* 通过管道传送答案（`printf 'y\n' | openspec archive …`），则旧版本会在绘制提示时将终端转义代码写入该捕获中 - 在某些环境中足以使文件严重膨胀。当 stdout 不是终端时，当前版本会以纯文本形式读取确认提示，并且无参数 `openspec archive`（否则会绘制交互式更改选择器）要求您预先传递更改名称，而不是在捕获中渲染菜单。无论哪种方式，重定向和 agent 运行保持干净；传递 `--yes` （带有更改名称）会完全跳过提示。

## 配置

### 我的 `config.yaml` 没有被应用

三个常见的嫌疑人：

1. **文件名错误。** 必须是`openspec/config.yaml`，而不是`.yml`。
2. **无效YAML。**通过任何YAML验证器运行它； CLI 还报告带有行号的语法错误。
3. **您希望重新启动。**您不需要重新启动。配置更改立即生效。

### “规则中的未知神器 ID：X”

`rules:` 下的密钥与架构中的任何工件都不匹配。对于默认的 `spec-driven` 架构，有效 ID 为 `proposal`、`specs`、`design`、`tasks`。要查看任何架构的 ID：

```bash
openspec schemas --json
```

### “上下文太大”

`context:` 字段故意限制为 50KB，因为它被注入到每个请求中。总结一下，或者链接到更长的文档而不是粘贴它们。精益环境还可以产生更好、更快的结果。

### “未找到架构”

您引用的架构名称不存在。列出可用的内容并检查拼写：

```bash
openspec schemas                    # list available schemas
openspec schema which <name>        # see where a schema resolves from
openspec schema init <name>         # create a custom one
```

请参阅[自定义](<OpenSpec 项目配置与工作流定制.md#自定义模式>)。

## 从旧工作流程迁移

### “在非交互模式下检测到旧文件”

您处于 CI 或非交互式 shell 中，并且 OpenSpec 找到了需要清理的旧文件，但无法提示您。自动批准：

```bash
openspec init --force
```

对于 Codex，OpenSpec 可能会检测到 `$CODEX_HOME/prompts` 或 `~/.codex/prompts` 中旧的托管提示文件。该清理仅限于 OpenSpec 列入许可名单的旧 Codex 提示文件名，非交互式 `openspec init` 仅删除存在替换 `.agents/skills/openspec-*` 技能的文件。非交互式 `openspec update` 不会影响所有遗留清理，除非您通过 `--force`。

### 迁移后命令未出现

重新启动您的IDE。技能在启动时被检测到。如果仍然没有出现，请运行 `openspec update` 并检查 [ 支持的工具 ](<OpenSpec 支持的 AI 工具与调用方式.md>) 中的文件位置。

### 我的旧 `project.md` 未迁移

这是故意的。 OpenSpec 永远不会自动删除 `project.md`，因为它可能包含您编写的上下文。把有用的部分移到`config.yaml`的`context:`部分，然后自己删除。 [迁移指南](<从旧版 OpenSpec 工作流迁移到 OPSX.md#正在将-projectmd-迁移到-configyaml>)介绍了这一点，包括一个提示，您可以交给您的AI进行蒸馏。

## 还卡住了吗？

- **不和谐：** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub 问题：** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **从您的终端：** `openspec feedback "what went wrong"` 为您打开一个问题。

当您报告问题时，请提供您的 OpenSpec 版本 (`openspec --version`)、您的 Node 版本 (`node --version`)、您的 AI 工具以及确切的命令和输出。它使帮助变得更快。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Troubleshooting

Concrete fixes for concrete problems. Each entry names a symptom, explains the likely cause in a sentence, and gives you the fix. If you don't see your issue here, the [FAQ](<OpenSpec 常见问题.md>) may help, and the [Discord](<https://discord.gg/YctCnvvshC>) definitely will.

## Installation and setup

### `openspec: command not found`

The CLI isn't installed, or your shell can't find it. Install it globally and check:

```bash
npm install -g @fission-ai/openspec@latest
openspec --version
```

If it installed but still isn't found, your global npm bin directory probably isn't on your `PATH`. Run `npm prefix -g` to see where global packages live: on macOS and Linux the binaries are in that directory's `bin/`, and on Windows they sit directly in it. Make sure that path is on your `PATH`. (`npm bin -g` was removed in npm 9.)

If you used the [AI-assisted install](<OpenSpec 安装与初始化指南.md#install-with-your-ai-assistant>), this is the expected hand-off point: that prompt tells your assistant to show you the `PATH` change rather than edit your shell startup files itself.

### "Requires Node.js 20.19.0 or higher"

OpenSpec runs on Node 20.19.0+. Check your version and upgrade if needed:

```bash
node --version
```

If you use bun to install OpenSpec, note that OpenSpec still *runs* on Node, so you need Node 20.19.0+ available on your `PATH` regardless. See [Installation](<OpenSpec 安装与初始化指南.md>).

### `openspec init` didn't configure my AI tool

Init asks which tools to set up. If you skipped your tool or want to add another, just run it again, or use the non-interactive form:

```bash
openspec init --tools claude,cursor
```

The full list of tool IDs is in [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>). Use `--tools all` for everything, `--tools none` to skip tool setup.

## Commands don't show up

If `/opsx:propose` (or your tool's equivalent) doesn't appear or doesn't do anything, work down this list. They're ordered fastest-to-check first.

1. **You may be in the wrong place.** Slash commands go in your AI assistant's chat, not your terminal. If you typed `/opsx:propose` into your shell, that's the issue. See [How Commands Work](<OpenSpec 两类命令如何协作.md>).

2. **Regenerate the files.** From your project root:

   ```bash
   openspec update
   ```

   This rewrites the skill and command files for every tool you've configured.

   Instruction files come from the *installed* CLI, so an outdated CLI reports everything up to date without ever writing the newer workflows. `openspec update` now checks for that and offers to upgrade — take the offer if you see it.

3. **Restart your assistant.** Most tools scan for skills and commands at startup. A fresh window often does it.

4. **Confirm the files exist.** For Claude Code, check that `.claude/skills/` contains `openspec-*` folders. Other tools use their own directories, all listed in [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>).

5. **Check you initialized this project.** Skills are written per project. If you cloned a repo or switched folders, run `openspec init` (or `openspec update`) there.

6. **Confirm your tool supports command files.** Codex, CodeArts, ForgeCode, Hermes, Kimi Code, Mistral Vibe, Zed Agent, and the shared `.agents` target don't get generated `opsx-*` command files; they use skill-based invocations instead, so `/opsx` will never autocomplete for them. Type `$openspec-propose` in Codex, `/skill:openspec-propose` in Kimi Code, and `/openspec-propose` in the rest. The shared `.agents` target is vendor-neutral, so `/openspec-propose` is the common form rather than a guaranteed one — if your assistant does not answer to it, check its own docs for how it invokes a skill. Amazon Q does get command files, but loads them into its prompt library rather than its slash menu — type `@opsx-propose` there, not `/opsx`. Every tool's form is listed in [How To Invoke](<OpenSpec 支持的 AI 工具与调用方式.md#how-to-invoke>).

## Working with changes

### "Change not found"

The command couldn't tell which change you meant. Name it explicitly, or check what exists:

```bash
openspec list                    # see active changes
/opsx:apply add-dark-mode        # name the change in chat
```

Also confirm you're in the right project directory.

### "No artifacts ready"

Every artifact is either already created or blocked waiting on a dependency. See what's blocking:

```bash
openspec status --change <name>
```

Then create the missing dependency first. Remember the order: proposal enables specs and design; specs and design together enable tasks.

### `openspec validate` reports warnings or errors

Validation checks your specs and changes for structural problems. Read the message: it names the file and the issue.

```bash
openspec validate <name>           # validate one item
openspec validate --all            # validate everything
openspec validate --all --strict   # stricter checks, good for CI
openspec validate --archived       # fail if archived changes have unchecked tasks
```

Common causes are a missing required section (like a spec with no scenarios) or a malformed delta header. Fix the file and re-run. The [CLI reference](<OpenSpec 命令行参考.md#openspec-validate>) documents the output format.

One message deserves its own note:

```text
MODIFIED "<requirement>" omits scenario(s) the current spec still has: "<scenario>"
```

A `MODIFIED` requirement replaces the whole requirement block, so it has to carry every scenario that survives the change, not only the ones you edited. Copy the named scenarios from `openspec/specs/<capability-path>/spec.md` back into the delta, preserving any domain directories in the path. This often appears on an older change after someone else's change added a scenario to the same requirement — archive refuses that change either way, and validation now says so before you implement it.

### The AI created incomplete or wrong artifacts

The AI didn't have enough context. A few levers help:

- Add project context in `openspec/config.yaml` so your stack and conventions are injected into every request. See [Customization](<OpenSpec 项目配置与工作流定制.md#project-configuration>).
- Add per-artifact `rules:` for guidance that only applies to, say, specs.
- Give a more detailed description when you propose.
- Use the expanded `/opsx:continue` to create one artifact at a time and review each, instead of `/opsx:ff` doing them all at once.

### Archive won't finish, or warns about incomplete tasks

Archive won't *block* on incomplete tasks, but it warns you, because archiving usually means the work is done. If tasks remain on purpose (you're filing a partial change), proceed. Otherwise finish the tasks first. Archive will also offer to sync your delta specs into the main specs if you haven't synced yet; say yes unless you have a reason not to.

### "User force closed the prompt with 0 null"

Something ran `openspec archive` where nothing can answer a question — an AI agent calling it from a tool, a CI job, or any shell with stdin closed. Archive asks up to three confirmations, and an unanswerable one used to fail with that raw message.

Pass `--yes` to answer them up front:

```bash
openspec archive <change-name> --yes
```

Keep any flags you were already passing — `--skip-specs` and `--no-validate` change what archive does, so a bare `--yes` rerun is not the same command. Current versions name the flag for you and print a `Fix:` line you can paste. If you meant to pick from a list, pass the change name explicitly: the picker needs an answer too.

If you instead ran archive with its output redirected to a file or captured by a tool and *did* pipe an answer (`printf 'y\n' | openspec archive …`), older versions wrote terminal escape codes into that capture while drawing the prompt — in some environments enough to bloat the file badly. Current versions read the confirmation prompts as plain text whenever stdout is not a terminal, and a no-argument `openspec archive` (which would otherwise draw an interactive change picker) asks you to pass a change name up front instead of rendering a menu into the capture. Either way, redirected and agent runs stay clean; passing `--yes` (with a change name) skips the prompts entirely.

## Configuration

### My `config.yaml` isn't being applied

Three usual suspects:

1. **Wrong filename.** It must be `openspec/config.yaml`, not `.yml`.
2. **Invalid YAML.** Run it through any YAML validator; the CLI also reports syntax errors with line numbers.
3. **You expected a restart.** You don't need one. Config changes take effect immediately.

### "Unknown artifact ID in rules: X"

A key under `rules:` doesn't match any artifact in your schema. For the default `spec-driven` schema the valid IDs are `proposal`, `specs`, `design`, `tasks`. To see the IDs for any schema:

```bash
openspec schemas --json
```

### "Context too large"

The `context:` field is capped at 50KB, on purpose, because it's injected into every request. Summarize it, or link out to longer docs instead of pasting them. Lean context also produces better, faster results.

### "Schema not found"

The schema name you referenced doesn't exist. List what's available and check spelling:

```bash
openspec schemas                    # list available schemas
openspec schema which <name>        # see where a schema resolves from
openspec schema init <name>         # create a custom one
```

See [Customization](<OpenSpec 项目配置与工作流定制.md#custom-schemas>).

## Migration from the legacy workflow

### "Legacy files detected in non-interactive mode"

You're in CI or a non-interactive shell, and OpenSpec found old files to clean up but can't prompt you. Approve automatically:

```bash
openspec init --force
```

For Codex, OpenSpec may detect old managed prompt files in `$CODEX_HOME/prompts` or `~/.codex/prompts`. That cleanup is limited to OpenSpec's allowlisted legacy Codex prompt filenames, and non-interactive `openspec init` removes only the files whose replacement `.agents/skills/openspec-*` skills exist. Non-interactive `openspec update` leaves all legacy cleanup untouched unless you pass `--force`.

### Commands didn't appear after migrating

Restart your IDE. Skills are detected at startup. If they still don't appear, run `openspec update` and check the file locations in [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>).

### My old `project.md` wasn't migrated

That's intentional. OpenSpec never deletes `project.md` automatically because it may hold context you wrote. Move the useful parts into `config.yaml`'s `context:` section, then delete it yourself. The [Migration Guide](<从旧版 OpenSpec 工作流迁移到 OPSX.md#migrating-projectmd-to-configyaml>) walks through this, including a prompt you can hand to your AI to do the distilling.

## Still stuck?

- **Discord:** [discord.gg/YctCnvvshC](<https://discord.gg/YctCnvvshC>)
- **GitHub Issues:** [github.com/Fission-AI/OpenSpec/issues](<https://github.com/Fission-AI/OpenSpec/issues>)
- **From your terminal:** `openspec feedback "what went wrong"` opens an issue for you.

When you report a problem, include your OpenSpec version (`openspec --version`), your Node version (`node --version`), your AI tool, and the exact command and output. It makes help much faster.


</details>
