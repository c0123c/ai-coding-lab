# Cline 桌面版新功能提示维护

指导从变更日志筛选重点、编写新功能弹窗内容和预览，说明一次性功能提示与单次发布说明的适用区别。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-10-02](<https://github.com/cline/cline/commit/aa47bcc97f3850423380356db83e9254f9d2b50f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/desktop-whats-new/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 桌面版新功能提示维护.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: desktop-whats-new
description: Use when the team wants to catch desktop app users up on recent features with a one-time "What's new" dialog (apps/examples/desktop-app). Guides deciding whether a catch-up is warranted, picking the highlights from the changelog, writing tight copy, adding the entry to webview/lib/whats-new-content.ts, and previewing it from Settings → About. Not for individual releases — those only appear on the About page's release notes.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 桌面“最新动态”追踪

当用户要求向桌面用户显示新功能、让用户了解最新更新、发布新功能模式或刷新“设置”→“关于”中的突出显示时，请使用此技能。

> 工作目录：从存储库根运行下面的每个命令。

桌面应用程序每几天发布一次，因此单个版本永远不会在应用程序内宣布；设置→关于始终列出捆绑的发行说明。 **追赶**是例外：一旦积累了足够的显着功能，一个对话框会在包含它的更新后第一次启动时显示它们一次。发布一个文件只是在单个文件中进行内容更改加上预览，仅此而已。

## 它是如何运作的

- 内容以 `WHATS_NEW_RELEASES` 形式存在于 `apps/examples/desktop-app/webview/lib/whats-new-content.ts` 中，最新的在前。每个条目都有一个稳定的 `id`、一个 `title` 和 3–4 `highlights`（`title`、`description`、lucide `icon`）。
- `webview/lib/whats-new.ts` 将最后一次看到的 `id` 存储在 localStorage (`cline.code.whats-new.v1`) 中。当最新的 id 与存储的 id 不同时，应用程序 shell (`webview/app/page.tsx`) 显示 `WhatsNewDialog`；关闭它，或单击“查看所有更改”，存储 ID。
- 全新安装永远不会赶上：完成入门标志着看到的最新条目。
- 设置→关于有一个“显示新内容”按钮，可以重播最新条目而不将其标记为已看到。这就是预览路径。
- 对话框（`webview/components/whats-new-dialog.tsx`）已修复：带有眉毛和标题的紫色英雄，2×2高亮网格，页脚带有“查看所有更改”和“开始”。不要添加每个条目的布局选项、图像或额外的按钮；保持内容变化。一个例外是 `spotlight` 条目（目前只有 `"connectors"`）：一个足够大的启动项，足以单独承载对话框，具有自己的正文和对话框中的操作号召。仅当团队明确要求时才添加新的聚光灯。

## 工作流程

1. 决定是否有必要进行追赶。

```sh
git log --oneline -1 -- apps/examples/desktop-app/webview/lib/whats-new-content.ts
sed -n '1,80p' apps/examples/desktop-app/CHANGELOG.md
```

阅读自上次更新以来的变更日志部分（第一个条目的 `id` 以日期为前缀；上面的 git 日志显示了它的登陆时间）。追赶需要**3-4功能，用户将改变他们的工作方式**——新功能，而不是修复或改进。如果少了，就说出来，然后停止；关于页面已经涵盖了其他所有内容。切勿发布单个版本或错误修复的赶超版本。

2. 选择亮点和草稿副本。提交草稿并等待批准后再编辑文件。

- **标题**：3–6个词，标题不是句子。好：“在任何地方并行工作”，“Cline 现在在 Linux 上运行”。坏：“我们对作曲家做了很多改进”。
- **突出显示标题**：功能名称，1–3单词（“SSH遥控器”，“工作树”）。
- **高亮描述**：一句话，在~90个字符下，以用户现在可以执行的操作开头。没有设置说明，没有警告，没有营销形容词。如果某个需求是必要的（例如“需要 gh CLI”），则更改日志和文档会包含该需求，而不是对话框。
- **图标**：代码库或清晰集中已存在的`lucide-react`图标（`Network`，`GitBranchPlus`，`GitPullRequest`，`Users`， `Terminal`、`Mic`、`Cloud`、……）。
- 按影响力对亮点进行排序，从左上角开始。

3. 将条目添加到`apps/examples/desktop-app/webview/lib/whats-new-content.ts`中`WHATS_NEW_RELEASES`的**顶部**。

```ts
{
	id: "2026-11-cloud-and-voice", // YYYY-MM-<slug>, unique, never reused or edited later
	title: "Run it in the cloud",
	highlights: [
		{ title: "Cloud sessions", description: "…", icon: Cloud },
		// 3–4 total
	],
},
```

在文件顶部导入所有新图标。 `id` 是所见跟踪比较的内容，因此更改现有条目的 id 会重新向所有人显示它；改变它的副本不会。保留数组中的旧条目；它们是无害的并记录历史。

4. 验证。

```sh
cd apps/examples/desktop-app
bunx vitest run webview/lib/whats-new.test.ts --config vitest.config.ts   # ids unique, newest resolves
bun run typecheck
```

然后在应用程序中预览。从`apps/examples/desktop-app`，启动`bun run dev:sidecar`和`bun run dev:web`（或本机窗口的`bun run dev`），打开设置→关于→“显示新内容”，然后检查两个主题（常规→深色模式）。确认在默认字体大小下没有任何说明会换行超过三行，并且标题在 560 像素下适合一行。另请确认返回用户在启动时出现的对话框：从 localStorage 中清除 `cline.code.whats-new.v1` 并重新加载。

5. 运送它。下一个桌面版本将迎头赶上；不需要其他任何东西。不要为追赶本身添加变更日志项目符号。如果用户要求释放，请交给`publish-desktop`技能。

## 陷阱

- 更新内容会在发布后首次启动时向 ** 每个 ** 现有用户显示，包括通过多个版本进行更新的用户。为自上一次追赶以来没有见过任何东西的人而写，而不仅仅是上次发布的人。
- 不要将条目与副本中的特定版本号联系起来；该条目通常跨越多个。
- `WHATS_NEW_RELEASES[0]` 必须是最新条目。 `whats-new.test.ts` 检查 id 唯一性，但无法知道预期的顺序。
- Cline Beta 版与稳定版共享此内容； `main` 上添加的追赶在下一次合并时达到测试版。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Desktop "What's new" catch-up

Use this skill when the user asks to show desktop users what's new, catch users up on recent updates, publish a what's-new modal, or refresh the highlights in Settings → About.

> Working directory: run every command below from the repository root.

The desktop app ships every couple of days, so individual releases are never announced in-app; Settings → About always lists the bundled release notes. A **catch-up** is the exception: once enough notable features have accumulated, one dialog shows them, once, on the first launch after the update that carries it. Publishing one is a content change in a single file plus a preview, nothing else.

## How it works

- Content lives in `apps/examples/desktop-app/webview/lib/whats-new-content.ts` as `WHATS_NEW_RELEASES`, newest first. Each entry has a stable `id`, a `title`, and 3–4 `highlights` (`title`, `description`, lucide `icon`).
- `webview/lib/whats-new.ts` stores the last seen `id` in localStorage (`cline.code.whats-new.v1`). The app shell (`webview/app/page.tsx`) shows `WhatsNewDialog` when the newest id differs from the stored one; closing it, or clicking "See all changes", stores the id.
- Fresh installs never see a catch-up: completing onboarding marks the newest entry seen.
- Settings → About has a "Show what's new" button that replays the newest entry without marking it seen. That is the preview path.
- The dialog (`webview/components/whats-new-dialog.tsx`) is fixed: violet hero with an eyebrow and headline, a 2×2 grid of highlights, footer with "See all changes" and "Get Started". Do not add per-entry layout options, images, or extra buttons; keep it a content change. The one exception is a `spotlight` entry (currently only `"connectors"`): a launch big enough to carry the dialog alone, with its own body and call to action in the dialog. Only add a new spotlight when the team explicitly asks for one.

## Workflow

1. Decide whether a catch-up is warranted.

```sh
git log --oneline -1 -- apps/examples/desktop-app/webview/lib/whats-new-content.ts
sed -n '1,80p' apps/examples/desktop-app/CHANGELOG.md
```

Read the changelog sections since the last catch-up (the first entry's `id` is date-prefixed; the git log above shows when it landed). A catch-up needs **3–4 features a user would change how they work for** — new capabilities, not fixes or polish. If there are fewer, say so and stop; the About page already covers everything else. Never ship a catch-up for a single release or for bug fixes.

2. Pick the highlights and draft copy. Present the draft and wait for approval before editing files.

- **Headline**: 3–6 words, a headline not a sentence. Good: "Work anywhere, in parallel", "Cline now runs on Linux". Bad: "We've made a lot of improvements to the composer".
- **Highlight title**: the feature name, 1–3 words ("SSH remotes", "Worktrees").
- **Highlight description**: one sentence, under ~90 characters, leading with what the user can now do. No setup instructions, no caveats, no marketing adjectives. If a requirement is essential (e.g. "needs the gh CLI"), the changelog and docs carry it, not the dialog.
- **Icon**: a `lucide-react` icon that already exists in the codebase or the lucide set (`Network`, `GitBranchPlus`, `GitPullRequest`, `Users`, `Terminal`, `Mic`, `Cloud`, …).
- Order highlights by impact, top-left first.

3. Add the entry to the **top** of `WHATS_NEW_RELEASES` in `apps/examples/desktop-app/webview/lib/whats-new-content.ts`.

```ts
{
	id: "2026-11-cloud-and-voice", // YYYY-MM-<slug>, unique, never reused or edited later
	title: "Run it in the cloud",
	highlights: [
		{ title: "Cloud sessions", description: "…", icon: Cloud },
		// 3–4 total
	],
},
```

Import any new icons at the top of the file. The `id` is what the seen-tracking compares, so changing an existing entry's id would re-show it to everyone; changing its copy would not. Keep old entries in the array; they are harmless and document history.

4. Verify.

```sh
cd apps/examples/desktop-app
bunx vitest run webview/lib/whats-new.test.ts --config vitest.config.ts   # ids unique, newest resolves
bun run typecheck
```

Then preview in the app. From `apps/examples/desktop-app`, start `bun run dev:sidecar` and `bun run dev:web` (or `bun run dev` for the native window), open Settings → About → "Show what's new", and check both themes (General → Dark mode). Confirm no description wraps past three lines at the default font size and the headline fits on one line at 560px. Also confirm the dialog appears on launch for a returning user: clear `cline.code.whats-new.v1` from localStorage and reload.

5. Ship it. The catch-up rides the next desktop release; nothing else is needed. Do not add a changelog bullet for the catch-up itself. If the user asks to release, hand off to the `publish-desktop` skill.

## Gotchas

- A catch-up shows to **every** existing user on their first launch after the release that carries it, including users who updated through several releases. Write for someone who has not seen anything since the previous catch-up, not just the last release.
- Do not tie an entry to a specific version number in copy; the entry usually spans several.
- `WHATS_NEW_RELEASES[0]` must be the newest entry. `whats-new.test.ts` checks id uniqueness but cannot know the intended order.
- Cline Beta shares this content with stable; a catch-up added on `main` reaches beta on the next merge.


</details>
