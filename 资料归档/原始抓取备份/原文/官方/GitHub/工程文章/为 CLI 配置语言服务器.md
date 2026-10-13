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
* `fileExtensions` maps each extension (with leading dot) to a [language identifier](https://code.visualstudio.com/docs/languages/identifiers#_known-language-identifiers)
* Existing entries in the config file are preserved — the agent merges, never overwrites

### 7. Verification

The agent runs `which <binary>` (or `where.exe` on Windows) to confirm the server is accessible, then validates the config file is well-formed JSON.

## Supported languages

The skill comes with a [set of predefined language servers](https://github.com/github/awesome-copilot/blob/main/skills/lsp-setup/references/lsp-servers.md) for several programming languages. If the coding agent faces one that it is not mapped out already, it will search for an appropriate server and walk you through manual configuration.

## What changes after setup

Once an LSP server is configured, the CLI agent can:

* **Resolve types across dependencies** — no more grepping through JAR files or `node_modules`
* **Jump to definitions** in third-party libraries, even when source isn’t checked into the repository
* **Find all references** to a symbol across the project
* **Read hover documentation** for any function, class, or type

This means the agent spends less time on tool calls and produces more accurate code on the first pass. For you, that’s less time waiting while the agent decompiles a JAR file or greps through node\_modules to answer a question your IDE already knows, and fewer wrong turns built on a misread signature. The agent reasons about your code with the same structured understanding you get from go-to-definition in your editor, so you can hand it bigger, gnarlier tasks and trust the result.

## Get started

1. **Download the skill**: visit the [Awesome Copilot LSP Setup skill page](https://awesome-copilot.github.com/skills/#file=skills%2Flsp-setup%2FSKILL.md) and click the **Download** button to get a ZIP file.
2. **Extract the ZIP** to `~/.copilot/skills/` by running:

```
unzip lsp-setup.zip -d ~/.copilot/skills/
```

3. **Restart GitHub Copilot CLI**: if Copilot CLI is already running, type `/exit` first. Then relaunch `copilot` so it picks up the new skill.
4. **Ask the agent** to set up a language server: for example, *“set up LSP for Java”* or *“enable code intelligence for Python”*.
5. **Verify**: after the skill installs and configures the LSP server, restart Copilot CLI one more time (`/exit`, then relaunch), run `/lsp` to check the server status, and try go-to-definition on a symbol from one of your dependencies.

The skill is part of the [Awesome Copilot](https://awesome-copilot.github.com/) project. It’s open source, so contributions and feedback are welcome!

---

## Tags:

* [GitHub Copilot](https://github.blog/tag/github-copilot/)
* [GitHub Copilot CLI](https://github.blog/tag/github-copilot-cli/)

## Written by

![Bruno Borges](https://avatars.githubusercontent.com/u/129743?v=4&s=200)

### [Bruno Borges](https://github.blog/author/brunoborges/)

[@brunoborges](https://github.com/brunoborges)

Principal Product Manager
