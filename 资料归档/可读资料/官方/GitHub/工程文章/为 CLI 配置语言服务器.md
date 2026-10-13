# 为 CLI 配置语言服务器

介绍 LSP 配置技能如何识别语言与平台、寻找并安装服务器、选择作用范围和验证连接，以改进代码理解。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-06-10](<https://github.blog/ai-and-ml/github-copilot/give-github-copilot-cli-real-code-intelligence-with-language-servers/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/give-github-copilot-cli-real-code-intelligence-with-language-servers/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/为 CLI 配置语言服务器.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 通过语言服务器提供 GitHub Copilot CLI 真实的代码智能

曾经看过 GitHub Copilot CLI 将 JAR 文件提取到临时目录，grep 遍历 `.class` 文件，并从原始字节码拼凑出 API 签名？ agent 资源丰富，但如果没有语言服务器，那就是它能做到的最好的了。

语言服务器协议 (LSP) 是一种标准，支持在 VS Code 等编辑器中进行定义、查找引用和类型解析。它在终端中也同样有效。 **LSP 设置 ** 技能可以自动安装和配置 Copilot CLI 的 LSP 服务器，因此 agent 可以获得有关代码的精确、结构化的答案，而不是依赖于文本搜索启发式。

在这篇文章中，您将了解该技能的幕后工作原理，查看它生成的配置格式，并为其当前支持的任何 14 语言进行设置。

## 问题：启发式代码理解

如果没有 LSP 服务器，GitHub Copilot CLI 中的 agent 通过文本搜索和二进制提取对 API 信息进行逆向工程。对于 Java 项目，可能如下所示：

```
# Find the dependency JAR 
find ~/.m2/repository -name "*httpclient*.jar" 
 
# Extract it to a temp directory 
mkdir /tmp/httpclient && cd /tmp/httpclient 
jar xf ~/.m2/repository/org/apache/httpcomponents/httpclient/4.5.14/httpclient-4.5.14.jar 
 
# Search extracted class files for a method 
grep -r "execute" --include="*.class" .
```

对于Python，agent可能是`site-packages`内的`cat`文件。对于 TypeScript，它会行走 `node_modules`。这些基于文本的方法适用于简单的情况，但它们对原始文本进行模式匹配，而不是真正的语义分析，因此它们错过了泛型、重载和传递类型，并且根本看不到编译后的字节码。这正是语言服务器所弥补的差距。

LSP服务器从结构上解决了这个问题。当 agent 发送对符号的 `textDocument/definition` 请求时，语言服务器返回确切的源位置、完全解析的类型和签名。

## LSP 设置技能的工作原理

触发后，该技能将执行七个步骤的工作流程：

### 1。语言选择

agent 使用 `ask_user` 和一组选项来确定用户需要 LSP 支持的语言。这驱动所有后续步骤。

### 2。操作系统检测

agent运行`uname -s`（或在Windows上检查`$env:OS` / `%OS%`）以确定目标平台。安装命令因操作系统而异。例如，macOS 上的 `brew install jdtls` 与 Linux 上从 eclipse.org 下载。

### 3。 LSP服务器查找

该技能包括一个参考文件 (`references/lsp-servers.md`)，其中包含 14 语言的精选数据：每个操作系统的安装命令、二进制名称和即用型配置片段。 agent 读取该文件并选择匹配的条目。

### 4。配置范围

agent 询问配置是否应该是：

* **用户级别**：`~/.copilot/lsp-config.json`—适用于所有存储库
* **存储库级别**：位于存储库根目录的`lsp.json`或`.github/lsp.json` - 范围仅限于单个项目

当两者都存在时，存储库级别的配置优先。

### 5。安装

agent 运行适当的安装命令。例如：

```
# TypeScript on any OS 
npm install -g typescript typescript-language-server 
 
# Java on macOS 
brew install jdtls 
 
# Rust on any OS 
rustup component add rust-analyzer
```

### 6。配置

agent 将条目写入或合并到所选的配置文件中。该格式使用 `lspServers` 对象，其中每个键都是服务器标识符：

```
{ 
  "lspServers": { 
    "java": { 
      "command": "jdtls", 
      "args": [], 
      "fileExtensions": { 
        ".java": "java" 
      } 
    } 
  } 
}
```

该技能强制执行的关键规则：

* `command` 必须位于 `$PATH` 或绝对路径上
* `args` 通常包括用于标准 I/O 传输的 `"--stdio"`（某些服务器（如 `jdtls`）在内部处理此问题）
* `fileExtensions` 将每个扩展名（带前导点）映射到 [ 语言标识符 ](<https://code.visualstudio.com/docs/languages/identifiers#_known-language-identifiers>)
* 配置文件中的现有条目将被保留 - agent 合并，永不覆盖

### 7。验证

agent 运行 `which <binary>`（或 Windows 上的 `where.exe`）以确认服务器可访问，然后验证配置文件格式正确 JSON。

## 支持的语言

该技能带有 [一组预定义的语言服务器](<https://github.com/github/awesome-copilot/blob/main/skills/lsp-setup/references/lsp-servers.md>) 适用于多种编程语言。如果编码 agent 面对尚未映射的情况，它将搜索合适的服务器并引导您完成手动配置。

## 设置后有什么变化

配置 LSP 服务器后，CLI agent 可以：

* **跨依赖关系解析类型** - 不再通过JAR文件或`node_modules`进行grep
* **跳转到第三方库中的定义**，即使源代码未签入存储库
* **查找项目中某个符号的所有引用**
* **阅读悬停文档**以了解任何函数、类或类型

这意味着 agent 在工具调用上花费的时间更少，并在第一次通过时生成更准确的代码。对您来说，agent 反编译 JAR 文件或通过节点 \_ 模块进行 grep 回答您的 IDE 已经知道的问题时等待的时间更少，并且基于误读签名的错误次数也更少。 agent 对您的代码的解释与您在编辑器中从定义中获得的结构化理解相同，因此您可以将更大、更复杂的任务交给它并信任结果。

## 开始使用

1. **下载技能**：访问[真棒CopilotLSP技能设置页面](<https://awesome-copilot.github.com/skills/#file=skills%2Flsp-setup%2FSKILL.md>)，点击**下载**按钮获取 ZIP 文件。
2. **通过运行以下命令将ZIP**提取到`~/.copilot/skills/`：

```
unzip lsp-setup.zip -d ~/.copilot/skills/
```

3. **重新启动GitHub Copilot CLI**：如果Copilot CLI已经在运行，请先输入`/exit`。然后重新启动 `copilot`，以便它获取新技能。
4. **询问 agent** 设置语言服务器：例如， *“设置 LSP 为了 Java”* 或 *“启用代码智能 Python”*.
5. **验证**：技能安装并配置LSP服务器后，再次重新启动CopilotCLI（`/exit`，然后重新启动），运行`/lsp`检查服务器状态，并尝试从您的依赖项之一转到符号的定义。

该技能是 [Awesome Copilot](<https://awesome-copilot.github.com/>) 项目的一部分。它是开源的，因此欢迎贡献和反馈！

---

## 标签：

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## 撰写者

![布鲁诺·博尔赫斯](<https://avatars.githubusercontent.com/u/129743?v=4&s=200>)

### [布鲁诺·博尔赫斯](<https://github.blog/author/brunoborges/>)

[@布鲁诺博格斯](<https://github.com/brunoborges>)

首席产品经理


## 原文对照

<details>
<summary>展开完整原文</summary>

# Give GitHub Copilot CLI real code intelligence with language servers

Ever watched GitHub Copilot CLI extract a JAR file to a temporary directory, grep through `.class` files, and piece together an API signature from raw bytecode? The agent is resourceful, but without a language server, that’s the best it can do.

The Language Server Protocol (LSP) is the standard that powers go to definition, find references, and type resolution in editors like VS Code. It works just as well in the terminal. The **LSP Setup** skill automates the installation and configuration of LSP servers for Copilot CLI, so the agent gets precise, structured answers about your code instead of relying on text search heuristics.

In this post, you’ll learn how the skill works under the hood, see the configuration format it generates, and get set up for any of the 14 languages it supports today.

## The problem: heuristic code understanding

Without an LSP server, the agent in GitHub Copilot CLI reverse-engineers API information through text search and binary extraction. For a Java project, that might look like:

```
# Find the dependency JAR 
find ~/.m2/repository -name "*httpclient*.jar" 
 
# Extract it to a temp directory 
mkdir /tmp/httpclient && cd /tmp/httpclient 
jar xf ~/.m2/repository/org/apache/httpcomponents/httpclient/4.5.14/httpclient-4.5.14.jar 
 
# Search extracted class files for a method 
grep -r "execute" --include="*.class" .
```

For Python, the agent might `cat` files inside `site-packages`. For TypeScript, it walks `node_modules`. These text-based approaches work for simple cases, but they’re doing pattern-matching over raw text rather than true semantic analysis, so they miss generics, overloads, and transitive types, and can’t see compiled bytecode at all. That’s exactly the gap a language server close.

An LSP server solves this structurally. When the agent sends a `textDocument/definition` request for a symbol, the language server returns the exact source location, fully resolved type, and signature.

## How the LSP Setup skill works

When triggered, the skill executes a seven-step workflow:

### 1. Language selection

The agent uses `ask_user` with a set of choices to determine which language the user needs LSP support for. This drives all subsequent steps.

### 2. Operating system detection

The agent runs `uname -s` (or checks `$env:OS` / `%OS%` on Windows) to determine the target platform. Install commands vary by operating system. For example, `brew install jdtls` on macOS versus downloading from eclipse.org on Linux.

### 3. LSP server lookup

The skill includes a reference file (`references/lsp-servers.md`) with curated data for 14 languages: install commands per operating system, binary names, and ready-to-use config snippets. The agent reads this file and selects the matching entry.

### 4. Configuration scope

The agent asks whether the config should be:

* **User-level**: `~/.copilot/lsp-config.json`—applies to all repositories
* **Repository-level**: `lsp.json` at the repository root or `.github/lsp.json`—scoped to a single project

Repository-level configuration takes precedence when both exist.

### 5. Installation

The agent runs the appropriate install command. For example:

```
# TypeScript on any OS 
npm install -g typescript typescript-language-server 
 
# Java on macOS 
brew install jdtls 
 
# Rust on any OS 
rustup component add rust-analyzer
```

### 6. Configuration

The agent writes or merges an entry into the chosen config file. The format uses a `lspServers` object where each key is a server identifier:

```
{ 
  "lspServers": { 
    "java": { 
      "command": "jdtls", 
      "args": [], 
      "fileExtensions": { 
        ".java": "java" 
      } 
    } 
  } 
}
```

Key rules the skill enforces:

* `command` must be on `$PATH` or an absolute path
* `args` typically includes `"--stdio"` for standard I/O transport (some servers like `jdtls` handle this internally)
* `fileExtensions` maps each extension (with leading dot) to a [language identifier](<https://code.visualstudio.com/docs/languages/identifiers#_known-language-identifiers>)
* Existing entries in the config file are preserved — the agent merges, never overwrites

### 7. Verification

The agent runs `which <binary>` (or `where.exe` on Windows) to confirm the server is accessible, then validates the config file is well-formed JSON.

## Supported languages

The skill comes with a [set of predefined language servers](<https://github.com/github/awesome-copilot/blob/main/skills/lsp-setup/references/lsp-servers.md>) for several programming languages. If the coding agent faces one that it is not mapped out already, it will search for an appropriate server and walk you through manual configuration.

## What changes after setup

Once an LSP server is configured, the CLI agent can:

* **Resolve types across dependencies** — no more grepping through JAR files or `node_modules`
* **Jump to definitions** in third-party libraries, even when source isn’t checked into the repository
* **Find all references** to a symbol across the project
* **Read hover documentation** for any function, class, or type

This means the agent spends less time on tool calls and produces more accurate code on the first pass. For you, that’s less time waiting while the agent decompiles a JAR file or greps through node\_modules to answer a question your IDE already knows, and fewer wrong turns built on a misread signature. The agent reasons about your code with the same structured understanding you get from go-to-definition in your editor, so you can hand it bigger, gnarlier tasks and trust the result.

## Get started

1. **Download the skill**: visit the [Awesome Copilot LSP Setup skill page](<https://awesome-copilot.github.com/skills/#file=skills%2Flsp-setup%2FSKILL.md>) and click the **Download** button to get a ZIP file.
2. **Extract the ZIP** to `~/.copilot/skills/` by running:

```
unzip lsp-setup.zip -d ~/.copilot/skills/
```

3. **Restart GitHub Copilot CLI**: if Copilot CLI is already running, type `/exit` first. Then relaunch `copilot` so it picks up the new skill.
4. **Ask the agent** to set up a language server: for example, *“set up LSP for Java”* or *“enable code intelligence for Python”*.
5. **Verify**: after the skill installs and configures the LSP server, restart Copilot CLI one more time (`/exit`, then relaunch), run `/lsp` to check the server status, and try go-to-definition on a symbol from one of your dependencies.

The skill is part of the [Awesome Copilot](<https://awesome-copilot.github.com/>) project. It’s open source, so contributions and feedback are welcome!

---

## Tags:

* [GitHub Copilot](<https://github.blog/tag/github-copilot/>)
* [GitHub Copilot CLI](<https://github.blog/tag/github-copilot-cli/>)

## Written by

![Bruno Borges](<https://avatars.githubusercontent.com/u/129743?v=4&s=200>)

### [Bruno Borges](<https://github.blog/author/brunoborges/>)

[@brunoborges](<https://github.com/brunoborges>)

Principal Product Manager


</details>
