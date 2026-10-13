# OpenSpec 安装与初始化指南

列出 Node.js 前提、各包管理器安装方式、由编程助手执行安装的提示模板，以及项目初始化与工具配置步骤。

- 来源：OpenSpec
- 资料类型：安装指南；含可执行命令与提示模板
- 存档版本：`9111a7654d78`。
- 文档时间：发表／更新：未知；抓取核验：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/Fission-AI/OpenSpec/blob/9111a7654d7800391459431fff4eaf66e33a3d2e/docs/installation.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenSpec/docs/installation.md>) · [返回资料总览](<../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 安装

## 先决条件

- **Node.js 20.19.0 或更高版本 ** — 检查您的版本：`node --version`

## 使用 AI 助手安装

而不是手动执行此操作？将以下提示粘贴到任何可以运行 shell 命令的编码助手中 - Claude Code、Codex、Cursor、Gemini CLI、Copilot 以及其余 [ 支持的工具 ](<OpenSpec 支持的 AI 工具与调用方式.md>)。它安装 CLI，初始化该项目，并报告实际发生的情况。

下面的手动步骤是事实来源——提示符只是为您运行这些步骤。如果你的助手停下来并交还一些东西，这是设计使然：它会在任何特权之前进行询问，并且从不编辑你的 shell 启动文件。使用 [ 包管理器 ](<#包管理器>) 和 [ 故障排除 ](<OpenSpec 安装与变更排障.md>) 自行完成这些部分。

```text
Install OpenSpec in this project and set it up for me. Follow these steps in
order, and stop where a step tells you to stop.

1. RUNTIME. Run `node --version`. OpenSpec needs Node.js 20.19.0 or higher. If
   Node is missing or older, say so and stop — don't install Node, switch
   versions, or reconfigure my version manager for me.

2. INSTALL. Use whichever package manager is already on my PATH, preferring npm:
     npm install -g @fission-ai/openspec@latest
     pnpm add -g @fission-ai/openspec@latest
     bun add -g @fission-ai/openspec@latest
     yarn global add @fission-ai/openspec@latest   (Yarn 1.x only)
   Don't pick based on this project's lockfile — a global install has nothing to
   do with how this repo's own dependencies are installed. If none of those four
   is available, stop and tell me — don't improvise an install. (If I'm on Nix,
   point me at the Nix section of the OpenSpec installation docs instead.)
   Show me the exact command and let me confirm before you run it; this installs
   software outside the project, and I may want a different package manager to
   own it.
   Stop and ask me again if the install needs sudo or admin rights, fails with a
   permissions error, or reports that its global bin directory is missing or
   unconfigured. Never edit my shell startup files (.bashrc, .zshrc, .profile,
   fish, PowerShell profile), and never run a setup command that edits them for
   me — show me the change and let me make it.

3. PATH. Run `openspec --version`. If the command isn't found, it may just be
   missing from this shell: tell me where the package manager installed it and
   how to add that directory to PATH for my shell and OS, then stop until I
   confirm. If it prints an older version than the one the install just
   reported, an earlier copy is shadowing it on PATH — tell me both versions
   instead of continuing. If I use a version manager, say so rather than editing
   PATH around it: with nvm or fnm the CLI is tied to the Node version that was
   active when you installed it, and with asdf or volta a shim may need
   regenerating.

4. INITIALIZE. Ask me which AI coding tool or tools I use and map each to an id
   from `openspec init --help` (Copilot is `github-copilot`, Zoo Code is
   `roocode`). `--tools` takes a comma-separated list, so name all of them.
   `openspec init --tools <ids>` deletes leftovers from older OpenSpec versions
   automatically, without asking — including `opsx-*.md` prompt files in my home
   directory (Codex keeps them in ~/.codex/prompts). Before you run it, look for
   those: `.../commands/openspec/` folders, OpenSpec marker blocks in files like
   CLAUDE.md or AGENTS.md, and home-directory `opsx-*.md` prompts. List whatever
   you find and wait for my go-ahead; if you find nothing, say so and carry on
   without asking. An existing `openspec/` folder is not a problem — init
   refreshes it and leaves my specs and changes alone.
   Confirm I'm in the right folder too: init creates `openspec/` wherever it
   runs, including inside a monorepo package.
   Then run: openspec init --tools <ids>

5. REPORT. Don't assume what should exist — tell me what init actually printed:
   how many skills and/or commands it created and where, the config file line,
   any "Setup required" note, and what to restart or reload. Some tools are
   skills-only and correctly create zero command files, so missing commands is
   not a failure on its own. If init said nothing was generated, relay the fix
   it suggested instead of retrying. Finish by telling me how to invoke OpenSpec
   in my tool, and take the exact spelling from the files init created rather
   than from its summary line: the punctuation differs per tool (/opsx:propose
   in some, /opsx-propose in others, @opsx-propose in Amazon Q), and tools that
   get skills instead of commands are invoked by skill name (/openspec-propose,
   or $openspec-propose in Codex, or /skill:openspec-propose in Kimi Code).
```

提示中的任何内容都不是特定于供应商的：它是简单的说明加上本页上记录的相同命令。它适用于 macOS、Linux 和 Windows，并且当某个步骤需要您的许可时，它会故意停止而不是即兴发挥。您的助手确实需要能够运行 shell 命令 - 一些 IDE 集成不能。

## 包管理器

### npm

```bash
npm install -g @fission-ai/openspec@latest
```

### pnpm

```bash
pnpm add -g @fission-ai/openspec@latest
```

### 纱线

```bash
yarn global add @fission-ai/openspec@latest
```

Yarn 2 和后来的 (Berry) 删除了 `global` 命令。在这些版本上，安装 OpenSpec 和 npm、pnpm 或 bun — 全局 CLI 不需要共享项目的包管理器。

### 德诺

Deno有时在解析@latest标签时会出现问题，但我们可以在最初安装时指定版本。
如果发生这种情况，您可以尝试使用版本更改@latest标签，例如`@^1.3.1`

```bash
deno install --global \
  --allow-read --allow-write --allow-env --allow-sys=cpus,homedir --allow-net=edge.openspec.dev \
  npm:@fission-ai/openspec@latest
# or
deno install --global \
  --allow-read --allow-write --allow-env --allow-sys=cpus,homedir --allow-net=edge.openspec.dev \
  npm:@fission-ai/openspec@^1.3.1
```

注意：如果您的子命令启动外部工具，例如配置编辑、反馈或打开工作区，您可能需要一个作用域 --allow-run=<program>.

### bun

Bun 可以全局安装 OpenSpec，但 OpenSpec 目前在 Node.js 上运行。
您仍然需要 Node.js 20.19.0 或 `PATH` 上可用的更高版本。

```bash
bun add -g @fission-ai/openspec@latest
```

## 尼克斯

无需安装，直接运行【OpenSpec】：

```bash
nix run github:Fission-AI/OpenSpec -- init
```

或者安装到您的个人资料中：

```bash
nix profile install github:Fission-AI/OpenSpec
```

或者在`flake.nix`中添加到你的开发环境中：

```nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    openspec.url = "github:Fission-AI/OpenSpec";
  };

  outputs = { nixpkgs, openspec, ... }: {
    devShells.x86_64-linux.default = nixpkgs.legacyPackages.x86_64-linux.mkShell {
      buildInputs = [ openspec.packages.x86_64-linux.default ];
    };
  };
}
```

## 验证安装

```bash
openspec --version
```

## 更新中

升级包，然后刷新每个项目的生成文件：

```bash
npm install -g @fission-ai/openspec@latest   # or pnpm/yarn/bun equivalent
openspec update                              # run inside each project
```

`openspec update` 为您配置的工具重新生成技能和命令文件，因此您的斜杠命令与安装的版本保持最新。它还检查是否已发布较新的 CLI 并提供升级，因为升级是首先使新工作流程可用的原因 - 请参阅 [CLI 参考 ](<OpenSpec 命令行参考.md#openspec-update>)。

## 卸载

没有 `openspec uninstall` 命令，因为 OpenSpec 只是一个全局包加上项目中的一些文件。删除它只需几个手动步骤，并且这里没有任何内容会触及您的源代码。

**1。删除全局包：**

```bash
npm uninstall -g @fission-ai/openspec   # or: pnpm rm -g / yarn global remove / bun rm -g
```

**2。从项目中删除 OpenSpec（可选）。** 如果您不再需要其规格和更改，请删除 `openspec/` 目录：

```bash
rm -rf openspec/
```

在执行此操作之前请考虑一下：`openspec/specs/` 和 `openspec/changes/archive/` 是系统行为方式及其更改原因的记录。如果您可能需要该历史记录，请在卸载后保留该文件夹（或将其保留在 git 中）。

**3。删除生成的 AI 工具文件（可选）。** OpenSpec 将技能和命令文件写入每个工具目录，例如 `.claude/skills/openspec-*/`、`.cursor/commands/opsx-*` 等。删除您配置的任何工具的 `openspec-*` 技能和 `opsx-*` 命令。 [支持的工具](<OpenSpec 支持的 AI 工具与调用方式.md>)中列出了每个工具的确切路径。

如果您在 `CLAUDE.md` 或 `AGENTS.md` 等文件中还有 OpenSpec 标记块，请手动删除这些块；这些文件中您自己的内容由您保留。

## 下一步

安装后，在项目中初始化OpenSpec：

```bash
cd your-project
openspec init
```

请参阅[入门](<OpenSpec 入门与首个变更.md>)以获取完整的演练。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Installation

## Prerequisites

- **Node.js 20.19.0 or higher** — Check your version: `node --version`

## Install with your AI assistant

Rather not do this by hand? Paste the prompt below into any coding assistant that can run shell commands — Claude Code, Codex, Cursor, Gemini CLI, Copilot, and the rest of the [supported tools](<OpenSpec 支持的 AI 工具与调用方式.md>). It installs the CLI, initializes this project, and reports back what actually happened.

The manual steps below are the source of truth — the prompt just runs them for you. If your assistant stops and hands something back, that's by design: it asks before anything privileged and never edits your shell startup files. Finish those bits yourself with [Package Managers](<#package-managers>) and [Troubleshooting](<OpenSpec 安装与变更排障.md>).

```text
Install OpenSpec in this project and set it up for me. Follow these steps in
order, and stop where a step tells you to stop.

1. RUNTIME. Run `node --version`. OpenSpec needs Node.js 20.19.0 or higher. If
   Node is missing or older, say so and stop — don't install Node, switch
   versions, or reconfigure my version manager for me.

2. INSTALL. Use whichever package manager is already on my PATH, preferring npm:
     npm install -g @fission-ai/openspec@latest
     pnpm add -g @fission-ai/openspec@latest
     bun add -g @fission-ai/openspec@latest
     yarn global add @fission-ai/openspec@latest   (Yarn 1.x only)
   Don't pick based on this project's lockfile — a global install has nothing to
   do with how this repo's own dependencies are installed. If none of those four
   is available, stop and tell me — don't improvise an install. (If I'm on Nix,
   point me at the Nix section of the OpenSpec installation docs instead.)
   Show me the exact command and let me confirm before you run it; this installs
   software outside the project, and I may want a different package manager to
   own it.
   Stop and ask me again if the install needs sudo or admin rights, fails with a
   permissions error, or reports that its global bin directory is missing or
   unconfigured. Never edit my shell startup files (.bashrc, .zshrc, .profile,
   fish, PowerShell profile), and never run a setup command that edits them for
   me — show me the change and let me make it.

3. PATH. Run `openspec --version`. If the command isn't found, it may just be
   missing from this shell: tell me where the package manager installed it and
   how to add that directory to PATH for my shell and OS, then stop until I
   confirm. If it prints an older version than the one the install just
   reported, an earlier copy is shadowing it on PATH — tell me both versions
   instead of continuing. If I use a version manager, say so rather than editing
   PATH around it: with nvm or fnm the CLI is tied to the Node version that was
   active when you installed it, and with asdf or volta a shim may need
   regenerating.

4. INITIALIZE. Ask me which AI coding tool or tools I use and map each to an id
   from `openspec init --help` (Copilot is `github-copilot`, Zoo Code is
   `roocode`). `--tools` takes a comma-separated list, so name all of them.
   `openspec init --tools <ids>` deletes leftovers from older OpenSpec versions
   automatically, without asking — including `opsx-*.md` prompt files in my home
   directory (Codex keeps them in ~/.codex/prompts). Before you run it, look for
   those: `.../commands/openspec/` folders, OpenSpec marker blocks in files like
   CLAUDE.md or AGENTS.md, and home-directory `opsx-*.md` prompts. List whatever
   you find and wait for my go-ahead; if you find nothing, say so and carry on
   without asking. An existing `openspec/` folder is not a problem — init
   refreshes it and leaves my specs and changes alone.
   Confirm I'm in the right folder too: init creates `openspec/` wherever it
   runs, including inside a monorepo package.
   Then run: openspec init --tools <ids>

5. REPORT. Don't assume what should exist — tell me what init actually printed:
   how many skills and/or commands it created and where, the config file line,
   any "Setup required" note, and what to restart or reload. Some tools are
   skills-only and correctly create zero command files, so missing commands is
   not a failure on its own. If init said nothing was generated, relay the fix
   it suggested instead of retrying. Finish by telling me how to invoke OpenSpec
   in my tool, and take the exact spelling from the files init created rather
   than from its summary line: the punctuation differs per tool (/opsx:propose
   in some, /opsx-propose in others, @opsx-propose in Amazon Q), and tools that
   get skills instead of commands are invoked by skill name (/openspec-propose,
   or $openspec-propose in Codex, or /skill:openspec-propose in Kimi Code).
```

Nothing in the prompt is vendor-specific: it's plain instructions plus the same commands documented on this page. It works on macOS, Linux, and Windows, and it deliberately stops rather than improvising when a step needs your permission. Your assistant does need to be able to run shell commands — a few IDE integrations can't.

## Package Managers

### npm

```bash
npm install -g @fission-ai/openspec@latest
```

### pnpm

```bash
pnpm add -g @fission-ai/openspec@latest
```

### yarn

```bash
yarn global add @fission-ai/openspec@latest
```

Yarn 2 and later (Berry) removed the `global` command. On those versions, install OpenSpec with npm, pnpm, or bun instead — a global CLI doesn't need to share your project's package manager.

### deno

Deno sometimes has issues parsing the @latest tag, but we can specify a version while installing initially.
If that happens, you could try to change the @latest tag with the version, something like `@^1.3.1`

```bash
deno install --global \
  --allow-read --allow-write --allow-env --allow-sys=cpus,homedir --allow-net=edge.openspec.dev \
  npm:@fission-ai/openspec@latest
# or
deno install --global \
  --allow-read --allow-write --allow-env --allow-sys=cpus,homedir --allow-net=edge.openspec.dev \
  npm:@fission-ai/openspec@^1.3.1
```

Note: If your subcommands launch external tools, like config edit, feedback, or workspace open, you may need a scoped --allow-run=<program>.

### bun

Bun can install OpenSpec globally, but OpenSpec currently runs on Node.js.
You still need Node.js 20.19.0 or higher available on `PATH`.

```bash
bun add -g @fission-ai/openspec@latest
```

## Nix

Run OpenSpec directly without installation:

```bash
nix run github:Fission-AI/OpenSpec -- init
```

Or install to your profile:

```bash
nix profile install github:Fission-AI/OpenSpec
```

Or add to your development environment in `flake.nix`:

```nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    openspec.url = "github:Fission-AI/OpenSpec";
  };

  outputs = { nixpkgs, openspec, ... }: {
    devShells.x86_64-linux.default = nixpkgs.legacyPackages.x86_64-linux.mkShell {
      buildInputs = [ openspec.packages.x86_64-linux.default ];
    };
  };
}
```

## Verify Installation

```bash
openspec --version
```

## Updating

Upgrade the package, then refresh each project's generated files:

```bash
npm install -g @fission-ai/openspec@latest   # or pnpm/yarn/bun equivalent
openspec update                              # run inside each project
```

`openspec update` regenerates the skill and command files for the tools you've configured, so your slash commands stay current with the installed version. It also checks whether a newer CLI has been published and offers to upgrade, since upgrading is what makes new workflows available in the first place — see [CLI Reference](<OpenSpec 命令行参考.md#openspec-update>).

## Uninstalling

There's no `openspec uninstall` command, because OpenSpec is just a global package plus some files in your project. Removing it is a few manual steps, and nothing here touches your source code.

**1. Remove the global package:**

```bash
npm uninstall -g @fission-ai/openspec   # or: pnpm rm -g / yarn global remove / bun rm -g
```

**2. Remove OpenSpec from a project (optional).** Delete the `openspec/` directory if you no longer want its specs and changes:

```bash
rm -rf openspec/
```

Think before you do this: `openspec/specs/` and `openspec/changes/archive/` are your record of how the system behaves and why it changed. If you might want that history, keep the folder (or keep it in git) even after uninstalling.

**3. Remove generated AI tool files (optional).** OpenSpec writes skill and command files into per-tool directories like `.claude/skills/openspec-*/`, `.cursor/commands/opsx-*`, and so on. Delete the `openspec-*` skills and `opsx-*` commands for whichever tools you configured. The exact paths per tool are listed in [Supported Tools](<OpenSpec 支持的 AI 工具与调用方式.md>).

If you also have OpenSpec marker blocks in files like `CLAUDE.md` or `AGENTS.md`, remove those blocks by hand; your own content in those files is yours to keep.

## Next Steps

After installing, initialize OpenSpec in your project:

```bash
cd your-project
openspec init
```

See [Getting Started](<OpenSpec 入门与首个变更.md>) for a full walkthrough.


</details>
