# 在 Skills 中使用脚本

介绍命令引用、自包含依赖、非交互接口、帮助、错误和结构化输出，帮助把重复操作做成 Agent 可调用工具。

- 来源：通用规范／Agent Skills
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-02-27](<https://agentskills.io/skill-creation/using-scripts>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://agentskills.io/skill-creation/using-scripts.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/通用规范/Agent Skills/在 Skills 中使用脚本.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://agentskills.io/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# 在技能中使用脚本

> 如何在您的技能中运行命令并捆绑可执行脚本。

技能可以指示agents运行shell命令并将可重用脚本捆绑在`scripts/`目录中。本指南涵盖一次性命令、具有自己的依赖项的自包含脚本，以及如何设计用于代理使用的脚本接口。

## 一次性命令

当现有包已经满足您的需要时，您可以直接在 `SKILL.md` 说明中引用它，而无需 `scripts/` 目录。许多生态系统提供了在运行时自动解决依赖关系的工具。



  

#### `uvx`

[uvx](<https://docs.astral.sh/uv/guides/tools/>) 在具有积极缓存的隔离环境中运行 Python 包。它附带 [uv](<https://docs.astral.sh/uv/>)。

    ```bash theme={null}
    uvx ruff@0.8.0 check .
    uvx black@24.10.0 .
    ```

    * 不与 Python 捆绑 — 需要单独安装。
    * 快。积极缓存，因此重复运行几乎是即时的。
  


  

#### `pipx`

[pipx](<https://pipx.pypa.io/>) 在隔离环境中运行 Python 软件包。可通过 OS 包管理器（`apt install pipx`、`brew install pipx`）获得。

    ```bash theme={null}
    pipx run 'black==24.10.0' .
    pipx run 'ruff==0.8.0' check .
    ```

    * 不与 Python 捆绑 — 需要单独安装。
    * `uvx` 的成熟替代品。虽然 `uvx` 已成为标准建议，但 `pipx` 仍然是可靠的选择，具有更广泛的 OS 包管理器可用性。
  


  

#### `npx`

[npx](<https://docs.npmjs.com/cli/commands/npx>) 运行 npm 软件包，按需下载它们。它随 npm 一起提供（随 Node.js 一起提供）。

    ```bash theme={null}
    npx eslint@9 --fix .
    npx create-vite@6 my-app
    ```

    * 与 Node.js 捆绑在一起 — 无需额外安装。
    * 下载包、运行它并缓存它以供将来使用。
    * 具有 `npx package@version` 的引脚版本可实现再现性。
  


  

#### `bunx`

[bunx](<https://bun.sh/docs/cli/bunx>) 是 Bun 等价于 `npx`。它附带 [Bun](<https://bun.sh/>)。

    ```bash theme={null}
    bunx eslint@9 --fix .
    bunx create-vite@6 my-app
    ```

    * 在基于 Bun 的环境中直接替换 `npx`。
    * 仅当用户环境具有 Bun 而不是 Node.js 时才适用。
  


  

#### `deno run`

[deno run](<https://docs.deno.com/runtime/reference/cli/run/>) 直接从 URL 或说明符运行脚本。它附带 [Deno](<https://deno.com/>)。

    ```bash theme={null}
    deno run npm:create-vite@6 my-app
    deno run --allow-read npm:eslint@9 -- --fix .
    ```

    * filesystem/network 访问需要权限标志（`--allow-read` 等）。
    * 使用 `--` 将 Deno 标志与工具自己的标志分开。
  


  

#### `go run`

[go run](<https://pkg.go.dev/cmd/go#hdr-Compile_and_run_Go_program>)直接编译并运行Go包。它内置于 `go` 命令中。

    ```bash theme={null}
    go run golang.org/x/tools/cmd/goimports@v0.28.0 .
    go run github.com/golangci/golangci-lint/cmd/golangci-lint@v1.62.0 run
    ```

    * 内置于 Go 中 — 无需额外的工具。
    * 固定版本或使用 `@latest` 使命令明确。
  


**技能中一次性命令提示：**

* **引脚版本**（例如，`npx eslint@9.0.0`）因此该命令随着时间的推移表现相同。
* **在 `SKILL.md` 中说明先决条件 **（例如，“需要 Node.js 18+”），而不是假设 agent 的环境具有这些条件。对于运行时级别的要求，请使用 [`compatibility` frontmatter 字段 ](<https://agentskills.io/specification#compatibility-field>)。
* **将复杂的命令移至脚本中。**当您调用带有几个标志的工具时，一次性命令效果很好。当命令变得足够复杂以至于第一次尝试就很难正确时，`scripts/` 中经过测试的脚本会更可靠。

## 引用来自 `SKILL.md` 的脚本

使用技能目录根**中的**相对路径来引用捆绑文件。 agent 自动解析这些路径——不需要绝对路径。

列出 `SKILL.md` 中的可用脚本，以便 agent 知道它们存在：

```markdown SKILL.md theme={null}
## Available scripts

- **`scripts/validate.sh`** — Validates configuration files
- **`scripts/process.py`** — Processes input data
```

然后指示agent运行它们：

````markdown SKILL.md theme={null}
## Workflow

1. Run the validation script:
   ```bash
   bash scripts/validate.sh "$INPUT_FILE"
   ```

2. Process the results:
   ```bash
   python3 scripts/process.py --input results.json
   ```
````



**注释：**

相同的相对路径约定适用于 `references/*.md` 等支持文件 - 脚本执行路径（在代码块中）相对于 ** 技能目录根 **，因为 agent 从那里运行命令。


## 独立的脚本

当您需要可重用逻辑时，请在 `scripts/` 中捆绑一个脚本，该脚本声明其自己的内联依赖项。 agent 可以使用单个命令运行脚本 - 不需要单独的清单文件或安装步骤。

多种语言支持内联依赖声明：



  

#### `Python`

[PEP 723](<https://peps.python.org/pep-0723/>) 定义内联脚本元数据的标准格式。在 `# ///` 标记内的 TOML 块中声明依赖项：

    ```python scripts/extract.py theme={null}
    # /// script
    # dependencies = [
    #   "beautifulsoup4",
    # ]
    # ///

    from bs4 import BeautifulSoup

    html = '<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>'
    print(BeautifulSoup(html, "html.parser").select_one("p.info").get_text())
    ```

使用 [uv](<https://docs.astral.sh/uv/>) 运行（推荐）：

    ```bash theme={null}
    uv run scripts/extract.py
    ```

`uv run`创建隔离环境，安装声明的依赖项，并运行脚本。 [pipx](<https://pipx.pypa.io/>) (`pipx run scripts/extract.py`) 还支持 PEP 723。

    * 具有 [PEP 508](<https://peps.python.org/pep-0508/>) 说明符的引脚版本：`"beautifulsoup4>=4.12,<5"`。
    * 使用 `requires-python` 约束 Python 版本。
    * 使用 `uv lock --script` 创建锁定文件以实现完全可重复性。
  


  

#### `Deno`

Deno 的 `npm:` 和 `jsr:` 导入说明符使每个脚本默认是独立的：

    ```typescript scripts/extract.ts theme={null}
    #!/usr/bin/env -S deno run

    import * as cheerio from "npm:cheerio@1.0.0";

    const html = `<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>`;
    const $ = cheerio.load(html);
    console.log($("p.info").text());
    ```

    ```bash theme={null}
    deno run scripts/extract.ts
    ```

    * 对 npm 软件包使用 `npm:`，对 Deno 本机软件包使用 `jsr:`。
    * 版本说明符遵循 semver：`@1.0.0`（精确）、`@^1.0.0`（兼容）。
    * 依赖项在全局缓存。使用`--reload`强制重新获取。
    * 带有本机插件（node-gyp）的软件包可能无法工作 - 提供预构建二进制文件的软件包效果最好。
  


  

#### `Bun`

当未找到 `node_modules` 目录时，Bun 会在运行时自动安装缺少的软件包。直接将版本固定在导入路径中：

    ```typescript scripts/extract.ts theme={null}
    #!/usr/bin/env bun

    import * as cheerio from "cheerio@1.0.0";

    const html = `<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>`;
    const $ = cheerio.load(html);
    console.log($("p.info").text());
    ```

    ```bash theme={null}
    bun run scripts/extract.ts
    ```

    * 不需要 `package.json` 或 `node_modules`。 TypeScript 本机工作。
    * 包在全局缓存。首次运行下载；随后的运行几乎是即时的。
    * 如果目录树上的任何位置都存在 `node_modules` 目录，则自动安装将被禁用，并且 Bun 会回退到标准 Node.js 分辨率。
  


  

#### `Ruby`

自 2.6 起，Bundler 随 Ruby 一起提供。使用`bundler/inline`直接在脚本中声明gem：

    ```ruby scripts/extract.rb theme={null}
    require 'bundler/inline'

    gemfile do
      source 'https://rubygems.org'
      gem 'nokogiri'
    end

    html = '<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>'
    doc = Nokogiri::HTML(html)
    puts doc.at_css('p.info').text
    ```

    ```bash theme={null}
    ruby scripts/extract.rb
    ```

    * 显式固定版本 (`gem 'nokogiri', '~> 1.16'`) — 没有锁定文件。
    * 工作目录中现有的 `Gemfile` 或 `BUNDLE_GEMFILE` 环境变量可能会产生干扰。
  


## 设计用于代理使用的脚本

当 agent 运行您的脚本时，它会读取 stdout 和 stderr 来决定下一步要做什么。一些设计选择使脚本显着更易于 agents 使用。

### 避免交互式提示

这是agent执行环境的硬性要求。 Agents 在非交互式 shell 中运行 — 它们无法响应 TTY 提示、密码对话框或确认菜单。阻止交互式输入的脚本将无限期挂起。

通过命令行标志、环境变量或标准输入接受所有输入：

```
# Bad: hangs waiting for input
$ python scripts/deploy.py
Target environment: _

# Good: clear error with guidance
$ python scripts/deploy.py
Error: --env is required. Options: development, staging, production.
Usage: python scripts/deploy.py --env staging --tag v1.2.3
```

### 文档使用`--help`

`--help` 输出是 agent 学习脚本接口的主要方式。包括简要说明、可用标志和使用示例：

```
Usage: scripts/process.py [OPTIONS] INPUT_FILE

Process input data and produce a summary report.

Options:
  --format FORMAT    Output format: json, csv, table (default: json)
  --output FILE      Write output to FILE instead of stdout
  --verbose          Print progress to stderr

Examples:
  scripts/process.py data.csv
  scripts/process.py --format csv --output report.csv data.csv
```

保持简洁 - 输出与它正在处理的其他所有内容一起进入 agent 的上下文窗口。

### 编写有用的错误消息

当 agent 出现错误时，该消息将直接影响其下一次尝试。不透明的“错误：无效输入”浪费了一个回合。相反，请说出出了什么问题、预期发生了什么以及要尝试什么：

```
Error: --format must be one of: json, csv, table.
       Received: "xml"
```

### 使用结构化输出

与自由格式文本相比，更喜欢结构化格式 - JSON、CSV、TSV。 agent 和标准工具（`jq`、`cut`、`awk`）都可以使用结构化格式，从而使您的脚本可在管道中组合。

```
# Whitespace-aligned — hard to parse programmatically
NAME          STATUS    CREATED
my-service    running   2025-01-15

# Delimited — unambiguous field boundaries
{"name": "my-service", "status": "running", "created": "2025-01-15"}
```

**将数据与诊断分离：**将结构化数据发送到标准输出，并将进度消息、警告和其他诊断发送到标准错误。这使得 agent 捕获干净、可解析的输出，同时在需要时仍然可以访问诊断信息。

### 进一步的考虑

* **幂等性。** Agents 可以重试命令。 “如果不存在则创建”比“创建并重复失败”更安全。
* **输入约束。**通过明确的错误而不是猜测来拒绝不明确的输入。尽可能使用枚举和闭集。
* ** 试运行支持。** 对于破坏性或有状态操作，`--dry-run` 标志可让 agent 预览将要发生的情况。
* **有意义的退出代码。**针对不同的失败类型（未找到、无效参数、身份验证失败）使用不同的退出代码，并将它们记录在 `--help` 输出中，以便 agent 知道每个代码的含义。
* **安全默认值。**考虑破坏性操作是否应该需要显式确认标志（`--confirm`、`--force`）或适合风险级别的其他保护措施。
* **可预测的输出大小。**许多agent工具会自动截断超过阈值的工具输出（例如，10-30K字符），可能会丢失关键信息。如果您的脚本可能会产生大量输出，请默认为摘要或合理的限制，并支持 `--offset` 等标志，以便 agent 可以在需要时请求更多信息。或者，如果输出很大并且不适合分页，则需要 agents 传递指定输出文件的 `--output` 标志，或 `-` 显式选择加入标准输出。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://agentskills.io/llms.txt
> Use this file to discover all available pages before exploring further.

# Using scripts in skills

> How to run commands and bundle executable scripts in your skills.

Skills can instruct agents to run shell commands and bundle reusable scripts in a `scripts/` directory. This guide covers one-off commands, self-contained scripts with their own dependencies, and how to design script interfaces for agentic use.

## One-off commands

When an existing package already does what you need, you can reference it directly in your `SKILL.md` instructions without a `scripts/` directory. Many ecosystems provide tools that auto-resolve dependencies at runtime.



  

#### `uvx`

    [uvx](<https://docs.astral.sh/uv/guides/tools/>) runs Python packages in isolated environments with aggressive caching. It ships with [uv](<https://docs.astral.sh/uv/>).

    ```bash theme={null}
    uvx ruff@0.8.0 check .
    uvx black@24.10.0 .
    ```

    * Not bundled with Python — requires a separate install.
    * Fast. Caches aggressively so repeat runs are near-instant.
  


  

#### `pipx`

    [pipx](<https://pipx.pypa.io/>) runs Python packages in isolated environments. Available via OS package managers (`apt install pipx`, `brew install pipx`).

    ```bash theme={null}
    pipx run 'black==24.10.0' .
    pipx run 'ruff==0.8.0' check .
    ```

    * Not bundled with Python — requires a separate install.
    * A mature alternative to `uvx`. While `uvx` has become the standard recommendation, `pipx` remains a reliable option with broader OS package manager availability.
  


  

#### `npx`

    [npx](<https://docs.npmjs.com/cli/commands/npx>) runs npm packages, downloading them on demand. It ships with npm (which ships with Node.js).

    ```bash theme={null}
    npx eslint@9 --fix .
    npx create-vite@6 my-app
    ```

    * Bundled with Node.js — no extra install needed.
    * Downloads the package, runs it, and caches it for future use.
    * Pin versions with `npx package@version` for reproducibility.
  


  

#### `bunx`

    [bunx](<https://bun.sh/docs/cli/bunx>) is Bun's equivalent of `npx`. It ships with [Bun](<https://bun.sh/>).

    ```bash theme={null}
    bunx eslint@9 --fix .
    bunx create-vite@6 my-app
    ```

    * Drop-in replacement for `npx` in Bun-based environments.
    * Only appropriate when the user's environment has Bun rather than Node.js.
  


  

#### `deno run`

    [deno run](<https://docs.deno.com/runtime/reference/cli/run/>) runs scripts directly from URLs or specifiers. It ships with [Deno](<https://deno.com/>).

    ```bash theme={null}
    deno run npm:create-vite@6 my-app
    deno run --allow-read npm:eslint@9 -- --fix .
    ```

    * Permission flags (`--allow-read`, etc.) are required for filesystem/network access.
    * Use `--` to separate Deno flags from the tool's own flags.
  


  

#### `go run`

    [go run](<https://pkg.go.dev/cmd/go#hdr-Compile_and_run_Go_program>) compiles and runs Go packages directly. It is built into the `go` command.

    ```bash theme={null}
    go run golang.org/x/tools/cmd/goimports@v0.28.0 .
    go run github.com/golangci/golangci-lint/cmd/golangci-lint@v1.62.0 run
    ```

    * Built into Go — no extra tooling needed.
    * Pin versions or use `@latest` to make the command explicit.
  


**Tips for one-off commands in skills:**

* **Pin versions** (e.g., `npx eslint@9.0.0`) so the command behaves the same over time.
* **State prerequisites** in your `SKILL.md` (e.g., "Requires Node.js 18+") rather than assuming the agent's environment has them. For runtime-level requirements, use the [`compatibility` frontmatter field](<https://agentskills.io/specification#compatibility-field>).
* **Move complex commands into scripts.** A one-off command works well when you're invoking a tool with a few flags. When a command grows complex enough that it's hard to get right on the first try, a tested script in `scripts/` is more reliable.

## Referencing scripts from `SKILL.md`

Use **relative paths from the skill directory root** to reference bundled files. The agent resolves these paths automatically — no absolute paths needed.

List available scripts in your `SKILL.md` so the agent knows they exist:

```markdown SKILL.md theme={null}
## Available scripts

- **`scripts/validate.sh`** — Validates configuration files
- **`scripts/process.py`** — Processes input data
```

Then instruct the agent to run them:

````markdown SKILL.md theme={null}
## Workflow

1. Run the validation script:
   ```bash
   bash scripts/validate.sh "$INPUT_FILE"
   ```

2. Process the results:
   ```bash
   python3 scripts/process.py --input results.json
   ```
````



**注释：**

  The same relative-path convention works in support files like `references/*.md` — script execution paths (in code blocks) are relative to the **skill directory root**, because the agent runs commands from there.


## Self-contained scripts

When you need reusable logic, bundle a script in `scripts/` that declares its own dependencies inline. The agent can run the script with a single command — no separate manifest file or install step required.

Several languages support inline dependency declarations:



  

#### `Python`

    [PEP 723](<https://peps.python.org/pep-0723/>) defines a standard format for inline script metadata. Declare dependencies in a TOML block inside `# ///` markers:

    ```python scripts/extract.py theme={null}
    # /// script
    # dependencies = [
    #   "beautifulsoup4",
    # ]
    # ///

    from bs4 import BeautifulSoup

    html = '<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>'
    print(BeautifulSoup(html, "html.parser").select_one("p.info").get_text())
    ```

    Run with [uv](<https://docs.astral.sh/uv/>) (recommended):

    ```bash theme={null}
    uv run scripts/extract.py
    ```

    `uv run` creates an isolated environment, installs the declared dependencies, and runs the script. [pipx](<https://pipx.pypa.io/>) (`pipx run scripts/extract.py`) also supports PEP 723.

    * Pin versions with [PEP 508](<https://peps.python.org/pep-0508/>) specifiers: `"beautifulsoup4>=4.12,<5"`.
    * Use `requires-python` to constrain the Python version.
    * Use `uv lock --script` to create a lockfile for full reproducibility.
  


  

#### `Deno`

    Deno's `npm:` and `jsr:` import specifiers make every script self-contained by default:

    ```typescript scripts/extract.ts theme={null}
    #!/usr/bin/env -S deno run

    import * as cheerio from "npm:cheerio@1.0.0";

    const html = `<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>`;
    const $ = cheerio.load(html);
    console.log($("p.info").text());
    ```

    ```bash theme={null}
    deno run scripts/extract.ts
    ```

    * Use `npm:` for npm packages, `jsr:` for Deno-native packages.
    * Version specifiers follow semver: `@1.0.0` (exact), `@^1.0.0` (compatible).
    * Dependencies are cached globally. Use `--reload` to force re-fetch.
    * Packages with native addons (node-gyp) may not work — packages that ship pre-built binaries work best.
  


  

#### `Bun`

    Bun auto-installs missing packages at runtime when no `node_modules` directory is found. Pin versions directly in the import path:

    ```typescript scripts/extract.ts theme={null}
    #!/usr/bin/env bun

    import * as cheerio from "cheerio@1.0.0";

    const html = `<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>`;
    const $ = cheerio.load(html);
    console.log($("p.info").text());
    ```

    ```bash theme={null}
    bun run scripts/extract.ts
    ```

    * No `package.json` or `node_modules` needed. TypeScript works natively.
    * Packages are cached globally. First run downloads; subsequent runs are near-instant.
    * If a `node_modules` directory exists anywhere up the directory tree, auto-install is disabled and Bun falls back to standard Node.js resolution.
  


  

#### `Ruby`

    Bundler ships with Ruby since 2.6. Use `bundler/inline` to declare gems directly in the script:

    ```ruby scripts/extract.rb theme={null}
    require 'bundler/inline'

    gemfile do
      source 'https://rubygems.org'
      gem 'nokogiri'
    end

    html = '<html><body><h1>Welcome</h1><p class="info">This is a test.</p></body></html>'
    doc = Nokogiri::HTML(html)
    puts doc.at_css('p.info').text
    ```

    ```bash theme={null}
    ruby scripts/extract.rb
    ```

    * Pin versions explicitly (`gem 'nokogiri', '~> 1.16'`) — there is no lockfile.
    * An existing `Gemfile` or `BUNDLE_GEMFILE` env var in the working directory can interfere.
  


## Designing scripts for agentic use

When an agent runs your script, it reads stdout and stderr to decide what to do next. A few design choices make scripts dramatically easier for agents to use.

### Avoid interactive prompts

This is a hard requirement of the agent execution environment. Agents operate in non-interactive shells — they cannot respond to TTY prompts, password dialogs, or confirmation menus. A script that blocks on interactive input will hang indefinitely.

Accept all input via command-line flags, environment variables, or stdin:

```
# Bad: hangs waiting for input
$ python scripts/deploy.py
Target environment: _

# Good: clear error with guidance
$ python scripts/deploy.py
Error: --env is required. Options: development, staging, production.
Usage: python scripts/deploy.py --env staging --tag v1.2.3
```

### Document usage with `--help`

`--help` output is the primary way an agent learns your script's interface. Include a brief description, available flags, and usage examples:

```
Usage: scripts/process.py [OPTIONS] INPUT_FILE

Process input data and produce a summary report.

Options:
  --format FORMAT    Output format: json, csv, table (default: json)
  --output FILE      Write output to FILE instead of stdout
  --verbose          Print progress to stderr

Examples:
  scripts/process.py data.csv
  scripts/process.py --format csv --output report.csv data.csv
```

Keep it concise — the output enters the agent's context window alongside everything else it's working with.

### Write helpful error messages

When an agent gets an error, the message directly shapes its next attempt. An opaque "Error: invalid input" wastes a turn. Instead, say what went wrong, what was expected, and what to try:

```
Error: --format must be one of: json, csv, table.
       Received: "xml"
```

### Use structured output

Prefer structured formats — JSON, CSV, TSV — over free-form text. Structured formats can be consumed by both the agent and standard tools (`jq`, `cut`, `awk`), making your script composable in pipelines.

```
# Whitespace-aligned — hard to parse programmatically
NAME          STATUS    CREATED
my-service    running   2025-01-15

# Delimited — unambiguous field boundaries
{"name": "my-service", "status": "running", "created": "2025-01-15"}
```

**Separate data from diagnostics:** send structured data to stdout and progress messages, warnings, and other diagnostics to stderr. This lets the agent capture clean, parseable output while still having access to diagnostic information when needed.

### Further considerations

* **Idempotency.** Agents may retry commands. "Create if not exists" is safer than "create and fail on duplicate."
* **Input constraints.** Reject ambiguous input with a clear error rather than guessing. Use enums and closed sets where possible.
* **Dry-run support.** For destructive or stateful operations, a `--dry-run` flag lets the agent preview what will happen.
* **Meaningful exit codes.** Use distinct exit codes for different failure types (not found, invalid arguments, auth failure) and document them in your `--help` output so the agent knows what each code means.
* **Safe defaults.** Consider whether destructive operations should require explicit confirmation flags (`--confirm`, `--force`) or other safeguards appropriate to the risk level.
* **Predictable output size.** Many agent harnesses automatically truncate tool output beyond a threshold (e.g., 10-30K characters), potentially losing critical information. If your script might produce large output, default to a summary or a reasonable limit, and support flags like `--offset` so the agent can request more information when needed. Alternatively, if output is large and not amenable to pagination, require agents to pass an `--output` flag that specifies either an output file or `-` to explicitly opt in to stdout.


</details>
