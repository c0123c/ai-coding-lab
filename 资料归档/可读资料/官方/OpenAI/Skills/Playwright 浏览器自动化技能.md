# Playwright 浏览器自动化技能

介绍终端调用、页面快照、表单交互、截图、多标签和追踪，帮助自动检查网页流程与界面问题。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-06](<https://github.com/openai/skills/commit/a5119697b819090e00e5d11ee1d86834d7c1043a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/playwright/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Playwright 浏览器自动化技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: "playwright"
description: "Use when the task requires automating a real browser from the terminal (navigation, form filling, snapshots, screenshots, data extraction, UI-flow debugging) via `playwright-cli` or the bundled wrapper script."
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 剧作家CLI技能

使用 `playwright-cli` 从终端驱动真正的浏览器。首选捆绑的包装器脚本，这样 CLI 即使未全局安装也能正常工作。
将此技能视为 CLI-第一个自动化。除非用户明确要求提供测试文件，否则不要转向 `@playwright/test`。

## 先决条件检查（必填）

在提出命令之前，检查 `npx` 是否可用（包装器依赖于它）：

```bash
command -v npx >/dev/null 2>&1
```

如果不可用，请暂停并要求用户安装Node.js/npm（它提供`npx`）。逐字提供以下步骤：

```bash
# Verify Node/npm are installed
node --version
npm --version

# If missing, install Node.js/npm, then:
npm install -g @playwright/cli@latest
playwright-cli --help
```

一旦出现 `npx`，就继续执行包装器脚本。 `playwright-cli` 的全局安装是可选的。

## 技能路径（设置一次）

```bash
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
export PWCLI="$CODEX_HOME/skills/playwright/scripts/playwright_cli.sh"
```

用户范围的技能安装在 `$CODEX_HOME/skills` 下（默认值：`~/.codex/skills`）。

## 快速启动

使用包装脚本：

```bash
"$PWCLI" open https://playwright.dev --headed
"$PWCLI" snapshot
"$PWCLI" click e15
"$PWCLI" type "Playwright"
"$PWCLI" press Enter
"$PWCLI" screenshot
```

如果用户更喜欢全局安装，这也有效：

```bash
npm install -g @playwright/cli@latest
playwright-cli --help
```

## 核心工作流程

1. 打开页面。
2. 获取稳定元素引用的快照。
3. 使用最新快照中的参考进行交互。
4. 导航或重大 DOM 更改后重新快照。
5. 在有用时捕获工件（屏幕截图、pdf、痕迹）。

最小循环：

```bash
"$PWCLI" open https://example.com
"$PWCLI" snapshot
"$PWCLI" click e3
"$PWCLI" snapshot
```

## 何时再次快照

之后再次快照：

- 导航
- 单击大幅改变 UI 的元素
- opening/closing 模态框或菜单
- 选项卡开关

参考文献可能会过时。当命令由于缺少引用而失败时，请再次快照。

## 推荐图案

### 表格填写并提交

```bash
"$PWCLI" open https://example.com/form
"$PWCLI" snapshot
"$PWCLI" fill e1 "user@example.com"
"$PWCLI" fill e2 "password123"
"$PWCLI" click e3
"$PWCLI" snapshot
```

### 使用跟踪调试 UI 流

```bash
"$PWCLI" open https://example.com --headed
"$PWCLI" tracing-start
# ...interactions...
"$PWCLI" tracing-stop
```

### 多选项卡工作

```bash
"$PWCLI" tab-new https://example.com
"$PWCLI" tab-list
"$PWCLI" tab-select 0
"$PWCLI" snapshot
```

## 包装脚本

包装器脚本使用 `npx --package @playwright/cli playwright-cli`，因此 CLI 无需全局安装即可运行：

```bash
"$PWCLI" --help
```

首选包装器，除非存储库已经标准化了全局安装。

## 参考文献

仅打开您需要的内容：

- CLI命令参考：`references/cli.md`
- 实用工作流程和故障排除：`references/workflows.md`

## 护栏

- 始终在引用元素 ID（例如 `e12`）之前先进行快照。
- 当裁判看起来过时时重新快照。
- 除非需要，否则优先使用显式命令而不是 `eval` 和 `run-code`。
- 当您没有新快照时，请使用占位符引用，例如 `eX` 并说明原因；不要使用 `run-code` 绕过参考。
- 当目视检查有帮助时，请使用 `--headed`。
- 在此存储库中捕获工件时，请使用 `output/playwright/` 并避免引入新的顶级工件文件夹。
- 默认为 CLI 命令和工作流程，而不是 Playwright 测试规范。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Playwright CLI Skill

Drive a real browser from the terminal using `playwright-cli`. Prefer the bundled wrapper script so the CLI works even when it is not globally installed.
Treat this skill as CLI-first automation. Do not pivot to `@playwright/test` unless the user explicitly asks for test files.

## Prerequisite check (required)

Before proposing commands, check whether `npx` is available (the wrapper depends on it):

```bash
command -v npx >/dev/null 2>&1
```

If it is not available, pause and ask the user to install Node.js/npm (which provides `npx`). Provide these steps verbatim:

```bash
# Verify Node/npm are installed
node --version
npm --version

# If missing, install Node.js/npm, then:
npm install -g @playwright/cli@latest
playwright-cli --help
```

Once `npx` is present, proceed with the wrapper script. A global install of `playwright-cli` is optional.

## Skill path (set once)

```bash
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
export PWCLI="$CODEX_HOME/skills/playwright/scripts/playwright_cli.sh"
```

User-scoped skills install under `$CODEX_HOME/skills` (default: `~/.codex/skills`).

## Quick start

Use the wrapper script:

```bash
"$PWCLI" open https://playwright.dev --headed
"$PWCLI" snapshot
"$PWCLI" click e15
"$PWCLI" type "Playwright"
"$PWCLI" press Enter
"$PWCLI" screenshot
```

If the user prefers a global install, this is also valid:

```bash
npm install -g @playwright/cli@latest
playwright-cli --help
```

## Core workflow

1. Open the page.
2. Snapshot to get stable element refs.
3. Interact using refs from the latest snapshot.
4. Re-snapshot after navigation or significant DOM changes.
5. Capture artifacts (screenshot, pdf, traces) when useful.

Minimal loop:

```bash
"$PWCLI" open https://example.com
"$PWCLI" snapshot
"$PWCLI" click e3
"$PWCLI" snapshot
```

## When to snapshot again

Snapshot again after:

- navigation
- clicking elements that change the UI substantially
- opening/closing modals or menus
- tab switches

Refs can go stale. When a command fails due to a missing ref, snapshot again.

## Recommended patterns

### Form fill and submit

```bash
"$PWCLI" open https://example.com/form
"$PWCLI" snapshot
"$PWCLI" fill e1 "user@example.com"
"$PWCLI" fill e2 "password123"
"$PWCLI" click e3
"$PWCLI" snapshot
```

### Debug a UI flow with traces

```bash
"$PWCLI" open https://example.com --headed
"$PWCLI" tracing-start
# ...interactions...
"$PWCLI" tracing-stop
```

### Multi-tab work

```bash
"$PWCLI" tab-new https://example.com
"$PWCLI" tab-list
"$PWCLI" tab-select 0
"$PWCLI" snapshot
```

## Wrapper script

The wrapper script uses `npx --package @playwright/cli playwright-cli` so the CLI can run without a global install:

```bash
"$PWCLI" --help
```

Prefer the wrapper unless the repository already standardizes on a global install.

## References

Open only what you need:

- CLI command reference: `references/cli.md`
- Practical workflows and troubleshooting: `references/workflows.md`

## Guardrails

- Always snapshot before referencing element ids like `e12`.
- Re-snapshot when refs seem stale.
- Prefer explicit commands over `eval` and `run-code` unless needed.
- When you do not have a fresh snapshot, use placeholder refs like `eX` and say why; do not bypass refs with `run-code`.
- Use `--headed` when a visual check will help.
- When capturing artifacts in this repo, use `output/playwright/` and avoid introducing new top-level artifact folders.
- Default to CLI commands and workflows, not Playwright test specs.


</details>
