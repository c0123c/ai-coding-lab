# 通过 tuistory 测试终端应用

介绍后台终端会话、读写输入、等待状态、快照和端到端测试，用于复现并检查 Cline CLI 的交互界面问题。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-08-01](<https://github.com/cline/cline/commit/edaab58716b41a709bdf2a80568d1f98d82c9d56>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/tuistory/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/通过 tuistory 测试终端应用.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: tuistory
description: |
  Drive and test terminal apps (especially the Cline CLI TUI in apps/cli) through tuistory — named background PTY sessions that agents can read, wait on, snapshot, screenshot, and type into. Like Playwright/tmux for terminals, with reactive waiting instead of blind `sleep`.

  Use this skill when you need to:
  - Manually test or reproduce bugs in the interactive Cline TUI (`bun run cli -i`) from a headless environment
  - Run a dev server or any long-lived/interactive process in the background without hanging your tool call
  - Write or extend Playwright-style e2e tests for the TUI (`bun run test:e2e:tuistory` in apps/cli)
  - Capture text snapshots or styled PNG screenshots of a TUI screen as evidence
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 历史

[tuistory](<https://github.com/remorses/tuistory>) 将任何终端命令包装在由 Ghostty 终端模拟器支持的命名后台 PTY 会话中。 Agents 通过立即返回的简短 CLI 调用与会话进行交互；人类可以`tuistory attach`到同一会话观看或干预。不需要真正的终端或显示器（`DISPLAY`）——它完全无头工作，这使得它成为云agents行使ClineTUI的首选方式。

它作为 `@cline/cli` 的 devDependency 安装，因此当您从 `apps/cli` 运行时，固定的二进制文件会解析：

```bash
cd apps/cli
bunx tuistory --help   # source of truth for commands, options, and syntax
```

对于完整的上游文档：`curl -s https://raw.githubusercontent.com/remorses/tuistory/refs/heads/main/README.md`

## 无头驾驶ClineTUI

在隔离环境中启动 TUI，这样您就不会触及真实的用户配置 (`~/.cline`)：

```bash
cd apps/cli
DATA_DIR=$(mktemp -d) && HOME_DIR=$(mktemp -d)
bunx tuistory -s cline --cols 120 --rows 36 \
  --env HOME=$HOME_DIR --env CLINE_DATA_DIR=$DATA_DIR \
  --env CLINE_DISABLE_CLINE_PASS_NOTICE=1 --env CLINE_TELEMETRY_DISABLED=1 \
  -- bun src/index.ts --provider anthropic -m claude-sonnet-4-6 -k test-key
```

虚拟人 `-k test-key` 呈现完整聊天 UI；只有实际的 agent 转弯才会失败。对于记录的 LLM 圈数，请使用 `apps/cli/src/tests/helpers/env.ts` (`CLINE_VCR=playback` + `CLINE_VCR_CASSETTE`) 中描述的 VCR 磁带。真正的回合需要提供者凭证（例如 `ANTHROPIC_API_KEY`、`CLINE_API_KEY`）。

然后使用 **observe → act →observe** 循环：

```bash
# Wait reactively for the chat view — never use sleep
bunx tuistory -s cline wait "What can I do for you?" --timeout 30000

# Act, then always observe the resulting screen state
bunx tuistory -s cline type "/settings"
bunx tuistory -s cline snapshot --trim
bunx tuistory -s cline press enter
bunx tuistory -s cline snapshot --trim

# Styled PNG of the current screen (prints the file path) — good for artifacts
bunx tuistory -s cline screenshot

# Full raw output stream (snapshot shows only the visible screen)
bunx tuistory read -s cline --all

# Tear down a session YOU started (double Ctrl+C exits the TUI cleanly)
bunx tuistory -s cline press ctrl c
bunx tuistory -s cline press ctrl c
bunx tuistory -s cline close
```

## 后台进程（而不是 tmux）

```bash
bunx tuistory -s my-server -- bun run dev:sidecar   # returns immediately
bunx tuistory -s my-server wait "/listening|ready/i" --timeout 30000
bunx tuistory read -s my-server                     # new output since last read
bunx tuistory -s my-server restart                  # after code changes
```

## 关键规则

- **`--`之前的选项，之后的命令。**第一个`--`之后的所有内容都逐字传递给子项：`tuistory -s name --cols 150 -- bun src/index.ts`是正确的。
- **每次操作后的快照。** TUI 是有状态的；对话框和错误可以呈现在您期望的视图上。 `snapshot` 反映了用户实际看到的内容（被遮挡的文本不算在内），这与 grep 原始流不同。
- **等等，永远不要睡觉。** `wait "text"` / `wait "/regex/i"`（默认区分大小写）与终端更新一样快速反应； `wait-idle` 当您不知道会发生什么时。始终通过`--timeout`。
- **按键立即落地。**与基于睡眠的脚本不同，排队的第二次按键可能会泄漏到下一个视图中（例如，一个 Enter 既接受斜杠补全并提交它）。
- **切勿关闭您未启动的会话。** 会话与人类 (`tuistory attach -s name`) 和其他 agents 共享。默认保持会话运行；使用`read`/`wait`/`snapshot`进行不间断检查。
- `--cols`/`--rows` 影响 TUI 布局（断言对宽度敏感）； `--pixel-ratio 2` 提供更清晰的屏幕截图。

## 使用库编写 e2e 测试 API

`apps/cli/src/cli.tuistory.e2e.test.ts`（运行：`bun run test:e2e:tuistory`）是参考。程序化的 API 在进程内运行 — 无守护进程：

```ts
import { launchTerminal } from "tuistory";

const session = await launchTerminal({
	command: "bun",
	args: ["src/index.ts", "--provider", "anthropic", "-k", "test-key"],
	cwd: cliRoot,
	env: isolatedEnv, // see createCliEnv() in the reference test
	cols: 120,
	rows: 36,
	waitForDataTimeout: 30_000, // CLI cold start compiles a large TS graph
});

await session.waitForText("What can I do for you?", { timeout: 30_000 });
const screen = await session.text({ trimEnd: true }); // emulated screen state
await session.type("/settings");
await session.press("enter");
session.close(); // always close in test teardown
```

屏幕状态断言可以检查陈旧的 UI 是否已 * 消失 * (`expect(screen).not.toContain(...)`)，而流 greping 工具则无法做到这一点。 `session.text({ only: { bold: true } })`按风格过滤； `session.read()` 返回自上次读取以来的原始流。


## 原文对照

<details>
<summary>展开完整原文</summary>

# tuistory

[tuistory](<https://github.com/remorses/tuistory>) wraps any terminal command in a named background PTY session backed by a Ghostty terminal emulator. Agents interact with the session via short CLI calls that return instantly; humans can `tuistory attach` to the same session to watch or intervene. No real terminal or display (`DISPLAY`) is needed — it works fully headless, which makes it the preferred way for cloud agents to exercise the Cline TUI.

It is installed as a devDependency of `@cline/cli`, so the pinned binary resolves when you run from `apps/cli`:

```bash
cd apps/cli
bunx tuistory --help   # source of truth for commands, options, and syntax
```

For full upstream docs: `curl -s https://raw.githubusercontent.com/remorses/tuistory/refs/heads/main/README.md`

## Driving the Cline TUI headlessly

Launch the TUI in an isolated environment so you don't touch real user config (`~/.cline`):

```bash
cd apps/cli
DATA_DIR=$(mktemp -d) && HOME_DIR=$(mktemp -d)
bunx tuistory -s cline --cols 120 --rows 36 \
  --env HOME=$HOME_DIR --env CLINE_DATA_DIR=$DATA_DIR \
  --env CLINE_DISABLE_CLINE_PASS_NOTICE=1 --env CLINE_TELEMETRY_DISABLED=1 \
  -- bun src/index.ts --provider anthropic -m claude-sonnet-4-6 -k test-key
```

The dummy `-k test-key` renders the full chat UI; only an actual agent turn would fail. For recorded LLM turns, use the VCR cassettes described in `apps/cli/src/tests/helpers/env.ts` (`CLINE_VCR=playback` + `CLINE_VCR_CASSETTE`). Real turns need a provider credential (e.g. `ANTHROPIC_API_KEY`, `CLINE_API_KEY`).

Then use an **observe → act → observe** loop:

```bash
# Wait reactively for the chat view — never use sleep
bunx tuistory -s cline wait "What can I do for you?" --timeout 30000

# Act, then always observe the resulting screen state
bunx tuistory -s cline type "/settings"
bunx tuistory -s cline snapshot --trim
bunx tuistory -s cline press enter
bunx tuistory -s cline snapshot --trim

# Styled PNG of the current screen (prints the file path) — good for artifacts
bunx tuistory -s cline screenshot

# Full raw output stream (snapshot shows only the visible screen)
bunx tuistory read -s cline --all

# Tear down a session YOU started (double Ctrl+C exits the TUI cleanly)
bunx tuistory -s cline press ctrl c
bunx tuistory -s cline press ctrl c
bunx tuistory -s cline close
```

## Background processes (instead of tmux)

```bash
bunx tuistory -s my-server -- bun run dev:sidecar   # returns immediately
bunx tuistory -s my-server wait "/listening|ready/i" --timeout 30000
bunx tuistory read -s my-server                     # new output since last read
bunx tuistory -s my-server restart                  # after code changes
```

## Key rules

- **Options before `--`, command after.** Everything after the first `--` is passed verbatim to the child: `tuistory -s name --cols 150 -- bun src/index.ts` is correct.
- **Snapshot after every action.** TUIs are stateful; dialogs and errors can render over the view you expect. `snapshot` reflects what the user actually sees (occluded text does not count), unlike grepping the raw stream.
- **Wait, never sleep.** `wait "text"` / `wait "/regex/i"` (case-sensitive by default) reacts as fast as the terminal updates; `wait-idle` when you don't know what to expect. Always pass `--timeout`.
- **Keys land instantly.** Unlike sleep-based scripts, a queued second keypress can leak into the next view (e.g. one Enter both accepts a slash completion and submits it).
- **Never close a session you didn't start.** Sessions are shared with humans (`tuistory attach -s name`) and other agents. Default to leaving sessions running; use `read`/`wait`/`snapshot` to inspect without disrupting.
- `--cols`/`--rows` affect TUI layout (assertions are width-sensitive); `--pixel-ratio 2` gives sharper screenshots.

## Writing e2e tests with the library API

`apps/cli/src/cli.tuistory.e2e.test.ts` (run: `bun run test:e2e:tuistory`) is the reference. The programmatic API runs in-process — no daemon:

```ts
import { launchTerminal } from "tuistory";

const session = await launchTerminal({
	command: "bun",
	args: ["src/index.ts", "--provider", "anthropic", "-k", "test-key"],
	cwd: cliRoot,
	env: isolatedEnv, // see createCliEnv() in the reference test
	cols: 120,
	rows: 36,
	waitForDataTimeout: 30_000, // CLI cold start compiles a large TS graph
});

await session.waitForText("What can I do for you?", { timeout: 30_000 });
const screen = await session.text({ trimEnd: true }); // emulated screen state
await session.type("/settings");
await session.press("enter");
session.close(); // always close in test teardown
```

Screen-state assertions can check that stale UI is *gone* (`expect(screen).not.toContain(...)`), which stream-grepping harnesses cannot. `session.text({ only: { bold: true } })` filters by style; `session.read()` returns the raw stream since the last read.


</details>
