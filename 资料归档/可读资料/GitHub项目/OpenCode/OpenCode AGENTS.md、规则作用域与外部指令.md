# OpenCode AGENTS.md、规则作用域与外部指令

产品指南。展示 /init、项目与全局规则、Claude Code 文件兼容、优先级和外部文件引用，帮助提供项目约定及上下文；Monorepo 内容是示例。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-04-01](<https://github.com/anomalyco/opencode/commit/897d83c589528bf56c529179a47ebade89b0a3cc>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：示例规范应按项目调整；不同工具的规则兼容范围需核对。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/rules.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode AGENTS.md、规则作用域与外部指令.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Rules
description: Set custom instructions for opencode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

您可以通过创建 `AGENTS.md` 文件向 opencode 提供自定义指令。这与Cursor的规则类似。它包含将包含在 LLM 上下文中的说明，以便为您的特定项目自定义其行为。

---

## 初始化

要创建新的`AGENTS.md`文件，可以在opencode中运行`/init`命令。

**提示：**

您应该将项目的 `AGENTS.md` 文件提交到 Git。

`/init` 扫描存储库中的重要文件，当代码库无法回答时可能会提出一些有针对性的问题，然后使用简洁的特定于项目的指导创建或更新 `AGENTS.md`。

它重点关注未来 agent 会议最可能需要的内容：

- 构建、lint 和测试命令
- 重要时的命令顺序和重点验证步骤
- 仅从文件名来看并不明显的架构和存储库结构
- 特定于项目的约定、设置怪癖和操作陷阱
- 对现有指令源的引用，例如 Cursor 或 Copilot 规则

如果您已有【`AGENTS.md`】，【`/init`】会就地改进，而不是盲目更换。

---

## 示例

您也可以手动创建此文件。以下是您可以放入 `AGENTS.md` 文件中的一些内容的示例。

**代码示例：** `AGENTS.md`

```markdown
# SST v3 Monorepo Project

This is an SST v3 monorepo with TypeScript. The project uses bun workspaces for package management.

## Project Structure

- `packages/` - Contains all workspace packages (functions, core, web, etc.)
- `infra/` - Infrastructure definitions split by service (storage.ts, api.ts, web.ts)
- `sst.config.ts` - Main SST configuration with dynamic imports

## Code Standards

- Use TypeScript with strict mode enabled
- Shared code goes in `packages/core/` with proper exports configuration
- Functions go in `packages/functions/`
- Infrastructure should be split into logical files in `infra/`

## Monorepo Conventions

- Import shared modules using workspace names: `@my-app/core/example`
```

我们在此处添加特定于项目的说明，这将在您的团队中共享。

---

## 类型

opencode还支持从多个位置读取`AGENTS.md`文件。这有不同的目的。

### 项目

将 `AGENTS.md` 放置在项目根目录中以获取特定于项目的规则。这些仅适用于您在此目录或其子目录中工作时。

### 全球

您还可以在 `~/.config/opencode/AGENTS.md` 文件中包含全局规则。这适用于所有 opencode 会话。

由于这未提交给 Git 或与您的团队共享，因此我们建议使用它来指定 LLM 应遵循的任何个人规则。

### Claude Code 兼容性

对于从 Claude Code 迁移的用户，OpenCode 支持 Claude Code 的文件约定作为后备：

- **项目规则**：项目目录中的`CLAUDE.md`（如果不存在`AGENTS.md`则使用）
- **全局规则**：`~/.claude/CLAUDE.md`（如果不存在`~/.config/opencode/AGENTS.md`则使用）
- **技能**：`~/.claude/skills/` — 详情请参阅[Agent Skills](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Skills 发现、格式与权限.md>)

要禁用 Claude Code 兼容性，请设置以下环境变量之一：

```bash
export OPENCODE_DISABLE_CLAUDE_CODE=1        # Disable all .claude support
export OPENCODE_DISABLE_CLAUDE_CODE_PROMPT=1 # Disable only ~/.claude/CLAUDE.md
export OPENCODE_DISABLE_CLAUDE_CODE_SKILLS=1 # Disable only .claude/skills
```

---

## 优先级

当opencode启动时，它按以下顺序查找规则文件：

1. **本地文件**从当前目录向上遍历(`AGENTS.md`,`CLAUDE.md`)
2. **全局文件**位于`~/.config/opencode/AGENTS.md`
3. **Claude Code 文件 ** 位于 `~/.claude/CLAUDE.md`（除非禁用）

第一个匹配的文件在每个类别中获胜。例如，如果您同时具有 `AGENTS.md` 和 `CLAUDE.md`，则仅使用 `AGENTS.md`。同样，`~/.config/opencode/AGENTS.md` 优先于 `~/.claude/CLAUDE.md`。

---

## 定制说明

您可以在您的`opencode.json`或全局`~/.config/opencode/opencode.json`中指定自定义指令文件。这允许您和您的团队重用现有规则，而不必将它们复制到 AGENTS.md。

示例：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["CONTRIBUTING.md", "docs/guidelines.md", ".cursor/rules/*.md"]
}
```

您还可以使用远程 URL 从 Web 加载说明。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["https://raw.githubusercontent.com/my-org/shared-rules/main/style.md"]
}
```

远程指令以 5 秒超时获取。

所有说明文件均与您的 `AGENTS.md` 文件合并。

---

## 引用外部文件

虽然 opencode 不会自动解析 `AGENTS.md` 中的文件引用，但您可以通过两种方式实现类似的功能：

### 使用opencode.json

推荐的方法是使用 `opencode.json` 中的 `instructions` 字段：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["docs/development-standards.md", "test/testing-guidelines.md", "packages/*/AGENTS.md"]
}
```

### AGENTS.md 中的手动说明

您可以通过在 `AGENTS.md` 中提供明确的指令来教 opencode 读取外部文件。这是一个实际的例子：

**代码示例：** `AGENTS.md`

```markdown
# TypeScript Project Rules

## External File Loading

CRITICAL: When you encounter a file reference (e.g., @rules/general.md), use your Read tool to load it on a need-to-know basis. They're relevant to the SPECIFIC task at hand.

Instructions:

- Do NOT preemptively load all references - use lazy loading based on actual need
- When loaded, treat content as mandatory instructions that override defaults
- Follow references recursively when needed

## Development Guidelines

For TypeScript code style and best practices: @docs/typescript-guidelines.md
For React component architecture and hooks patterns: @docs/react-patterns.md
For REST API design and error handling: @docs/api-standards.md
For testing strategies and coverage requirements: @test/testing-guidelines.md

## General Guidelines

Read the following file immediately as it's relevant to all workflows: @rules/general-guidelines.md.
```

这种方法允许您：

- 创建模块化、可重用的规则文件
- 通过符号链接或 git 子模块在项目之间共享规则
- 保持 AGENTS.md 简洁，同时参考详细指南
- 确保 opencode 仅在特定任务需要时加载文件

**提示：**

对于单一存储库或具有共享标准的项目，使用具有 glob 模式（如 `packages/*/AGENTS.md`）的 `opencode.json` 比手动指令更易于维护。


## 原文对照

<details>
<summary>展开完整原文</summary>

You can provide custom instructions to opencode by creating an `AGENTS.md` file. This is similar to Cursor's rules. It contains instructions that will be included in the LLM's context to customize its behavior for your specific project.

---

## Initialize

To create a new `AGENTS.md` file, you can run the `/init` command in opencode.

**提示：**

You should commit your project's `AGENTS.md` file to Git.

`/init` scans the important files in your repo, may ask a couple of targeted questions when the codebase cannot answer them, and then creates or updates `AGENTS.md` with concise project-specific guidance.

It focuses on the things future agent sessions are most likely to need:

- build, lint, and test commands
- command order and focused verification steps when they matter
- architecture and repo structure that are not obvious from filenames alone
- project-specific conventions, setup quirks, and operational gotchas
- references to existing instruction sources like Cursor or Copilot rules

If you already have an `AGENTS.md`, `/init` will improve it in place instead of blindly replacing it.

---

## Example

You can also just create this file manually. Here's an example of some things you can put into an `AGENTS.md` file.

**代码示例：** `AGENTS.md`

```markdown
# SST v3 Monorepo Project

This is an SST v3 monorepo with TypeScript. The project uses bun workspaces for package management.

## Project Structure

- `packages/` - Contains all workspace packages (functions, core, web, etc.)
- `infra/` - Infrastructure definitions split by service (storage.ts, api.ts, web.ts)
- `sst.config.ts` - Main SST configuration with dynamic imports

## Code Standards

- Use TypeScript with strict mode enabled
- Shared code goes in `packages/core/` with proper exports configuration
- Functions go in `packages/functions/`
- Infrastructure should be split into logical files in `infra/`

## Monorepo Conventions

- Import shared modules using workspace names: `@my-app/core/example`
```

We are adding project-specific instructions here and this will be shared across your team.

---

## Types

opencode also supports reading the `AGENTS.md` file from multiple locations. And this serves different purposes.

### Project

Place an `AGENTS.md` in your project root for project-specific rules. These only apply when you are working in this directory or its sub-directories.

### Global

You can also have global rules in a `~/.config/opencode/AGENTS.md` file. This gets applied across all opencode sessions.

Since this isn't committed to Git or shared with your team, we recommend using this to specify any personal rules that the LLM should follow.

### Claude Code Compatibility

For users migrating from Claude Code, OpenCode supports Claude Code's file conventions as fallbacks:

- **Project rules**: `CLAUDE.md` in your project directory (used if no `AGENTS.md` exists)
- **Global rules**: `~/.claude/CLAUDE.md` (used if no `~/.config/opencode/AGENTS.md` exists)
- **Skills**: `~/.claude/skills/` — see [Agent Skills](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Skills 发现、格式与权限.md>) for details

To disable Claude Code compatibility, set one of these environment variables:

```bash
export OPENCODE_DISABLE_CLAUDE_CODE=1        # Disable all .claude support
export OPENCODE_DISABLE_CLAUDE_CODE_PROMPT=1 # Disable only ~/.claude/CLAUDE.md
export OPENCODE_DISABLE_CLAUDE_CODE_SKILLS=1 # Disable only .claude/skills
```

---

## Precedence

When opencode starts, it looks for rule files in this order:

1. **Local files** by traversing up from the current directory (`AGENTS.md`, `CLAUDE.md`)
2. **Global file** at `~/.config/opencode/AGENTS.md`
3. **Claude Code file** at `~/.claude/CLAUDE.md` (unless disabled)

The first matching file wins in each category. For example, if you have both `AGENTS.md` and `CLAUDE.md`, only `AGENTS.md` is used. Similarly, `~/.config/opencode/AGENTS.md` takes precedence over `~/.claude/CLAUDE.md`.

---

## Custom Instructions

You can specify custom instruction files in your `opencode.json` or the global `~/.config/opencode/opencode.json`. This allows you and your team to reuse existing rules rather than having to duplicate them to AGENTS.md.

Example:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["CONTRIBUTING.md", "docs/guidelines.md", ".cursor/rules/*.md"]
}
```

You can also use remote URLs to load instructions from the web.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["https://raw.githubusercontent.com/my-org/shared-rules/main/style.md"]
}
```

Remote instructions are fetched with a 5 second timeout.

All instruction files are combined with your `AGENTS.md` files.

---

## Referencing External Files

While opencode doesn't automatically parse file references in `AGENTS.md`, you can achieve similar functionality in two ways:

### Using opencode.json

The recommended approach is to use the `instructions` field in `opencode.json`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["docs/development-standards.md", "test/testing-guidelines.md", "packages/*/AGENTS.md"]
}
```

### Manual Instructions in AGENTS.md

You can teach opencode to read external files by providing explicit instructions in your `AGENTS.md`. Here's a practical example:

**代码示例：** `AGENTS.md`

```markdown
# TypeScript Project Rules

## External File Loading

CRITICAL: When you encounter a file reference (e.g., @rules/general.md), use your Read tool to load it on a need-to-know basis. They're relevant to the SPECIFIC task at hand.

Instructions:

- Do NOT preemptively load all references - use lazy loading based on actual need
- When loaded, treat content as mandatory instructions that override defaults
- Follow references recursively when needed

## Development Guidelines

For TypeScript code style and best practices: @docs/typescript-guidelines.md
For React component architecture and hooks patterns: @docs/react-patterns.md
For REST API design and error handling: @docs/api-standards.md
For testing strategies and coverage requirements: @test/testing-guidelines.md

## General Guidelines

Read the following file immediately as it's relevant to all workflows: @rules/general-guidelines.md.
```

This approach allows you to:

- Create modular, reusable rule files
- Share rules across projects via symlinks or git submodules
- Keep AGENTS.md concise while referencing detailed guidelines
- Ensure opencode loads files only when needed for the specific task

**提示：**

For monorepos or projects with shared standards, using `opencode.json` with glob patterns (like `packages/*/AGENTS.md`) is more maintainable than manual instructions.


</details>
