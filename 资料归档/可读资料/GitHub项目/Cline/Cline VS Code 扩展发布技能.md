# Cline VS Code 扩展发布技能

记录稳定版、夜间版及紧急修复的发布路径，涵盖版本、变更日志、工作流审批、标签和发布后验证。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/cline/cline/commit/b51c27b09a93c7b13834c220d5188cdaa43a6569>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/publish-extension/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline VS Code 扩展发布技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: publish-extension
description: Use when releasing the Cline VS Code extension — stable (standalone SDK build of main via ext-vscode-publish), nightly (ext-vscode-publish-nightly, manual dispatch), or an emergency legacy-branch hotfix (ext-vscode-publish-legacy). Guides version selection, changelog, workflow dispatch, environment approvals, tagging, post-publish verification, and the remaining retirement of the finished A/B rollout machinery.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# VS Code 扩展发布

当用户要求发布、发布或交付 VS Code 扩展（稳定版、夜间版或紧急遗留修补程序）或停用剩余的 A/B 部署机制时，请使用此技能。

> 工作目录：repo root。所有工作流程均从 `main` 调度（GitHub 需要默认分支上的工作流程文件；每个工作流程都会检查其实际构建的引用）。

## 当前时代：独立SDK从`main`扩展

**旧版 → SDK 迁移已完成。** PostHog 标志 `ext-sdk-bundle-rollout` 达到 100%（根据经验验证） 2026-09-15：200/200 `/decide` 探测器返回 `true`），因此组合 VSIX 上的每个用户都运行`next`（SDK、bun）捆绑包和`legacy/`一半是自重。稳定版本现在通过 `ext-vscode-publish.yml` 提供 `main`** 的 ** 简单构建 - A/B 工作流程的副本，其中包含加载程序、`legacy/` 捆绑包和缝合，以及相同的构建-工件-然后-`vsce publish --packagePath` 发布步骤。组合的 A/B 路径 (`ext-vscode-ab-package.yml`) 不再用于发布，并且正在等待删除 — 请参阅底部的“退役 A/B 机器”，了解仍需清理的内容和一个警告（将标记保留在 100%）。

**请勿使用`ext-vscode-publish-stable.yml`。**其发布步骤（`bun run publish:marketplace`→裸`vsce publish`，重新运行`vscode:prepublish`并重新打包源代码树）在v4.1.18的所有五次尝试中都挂起（2026-09-15/17），每个在“发布...”与`Request timeout: /_apis/gallery`之后正好180秒。该路径是客户端的 FIRST 请求（路由发现，无正文） - VSIX 从未上传，因此与工件无关的任何内容都不是原因。相同的代码行在几秒钟内通过预构建的工件路径作为 4.1.19 发出。根本原因尚未确定（请参阅底部的陷阱）；稳定的工作流程仅保留到新工作流程发布后才会被删除。

历史，仅供参考：A/B 时代为 `4.1.0` → `4.1.19`（7 月至 9 月 2026）。设计文档保留在 `apps/vscode-rollout/README.md` 和 PR #12253 中，直到该目录被删除。

### 清单和工作流程

|频道|市场 ID|工作流程|触发|版本|
|---|---|---|---|---|
|**稳定**| `saoudrizwan.claude-dev` | `ext-vscode-publish.yml` |调度，来自 `main`|`apps/vscode/package.json` 在 `main` 上； `version` 输入必须等于它|
|每晚| `saoudrizwan.cline-nightly` | `ext-vscode-publish-nightly.yml` |**仅手动调度**（cron故意删除）|自动 `<major>.<minor>.<unix-ts>` 来自主的 `apps/vscode/package.json`|
|旧版修补程序（仅限紧急情况）| `saoudrizwan.claude-dev` | `ext-vscode-publish-legacy.yml` |派遣|`apps/vscode/package.json` 上 `legacy-extension`|

稳定运行四个作业：`preflight`（输入正则表达式，仅主要发布，市场单调性）和可重用的bun套件（`ext-vscode-test.yml`，测试调度SHA）未门控→`build`（无环境；打包`.vsix`作为运行工件，因此 `publish=false` 排练不需要批准）→ `publish`（`publish` 环境 - 需要审阅者在操作 UI 中批准；上传工件，然后 tag/release/Slack）。每晚使用 `PublishNightly`（仅限分支策略）。每晚 ** 仍然构建组合加载器 VSIX**（加载器 + `next/` + `legacy/`），直到它转换回普通构建 - 该转换位于下面的停用列表中。

## 黄金规则（在任何发布之前阅读）

1. **一份清单，一份版本线。** `claude-dev` 已从多个工作流程和分支发布。每个稳定版本都必须使用一个版本 **严格高于任何分支发布到列表的最高版本** — 市场版本是单调的，无法取消发布（取代，永不删除）。首先检查正在直播的内容：

   ```bash
   curl -s -X POST "https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery" \
     -H "Content-Type: application/json" -H "Accept: application/json;api-version=3.0-preview.1" \
     -d '{"filters":[{"criteria":[{"filterType":7,"value":"saoudrizwan.claude-dev"}]}],"flags":16}' \
     | python3 -c "import json,sys; v=json.load(sys.stdin)['results'][0]['extensions'][0]['versions'][0]; print(v['version'], v['lastUpdated'])"
   ```

`ext-vscode-publish` 运行相同的检查两次（预检，然后在上传之前再次运行 - 批准等待可能会持续数天），因此非单调调度会在几秒钟内失败。仍然自己运行查询来选择号码。 `legacy-extension` 分支位于 `4.0.12`，因此它不会与 `4.1.x` 行冲突，但遗留修补程序必须编号高于实时稳定版本（请参阅紧急部分）。

2. **`version` 输入 == 包版本，强制执行。** 构建作业读取 `apps/vscode/package.json` 并出现硬失败，除非它等于 `version` 输入（普通 `X.Y.Z`，无 `v`）。输入是确认，而不是印章——首先在 `main` 上升级版本。 `v<version>` 标签是在 * 成功发布市场后由工作流程 * 创建的。

3. **变更日志位于存储库 ROOT** (`CHANGELOG.md`) 中 — 而不是 `apps/vscode/CHANGELOG.md`（不存在）。除非第一个 `## [` 标题恰好是 `## [<version>]`，否则工作流会发生硬故障。该部分主体成为 GitHub 发行说明和 Slack 帖子（Slack 副本被修剪为 3000 字符，并带有链接；发行主体保持完整）。

4. **在推送**提交或标签之前询问。 ** 切勿通过 `gh api`** 自行批准 `publish` 环境门 — 将运行 URL 交给维护人员以单击“审核部署”。

5. **并发**：`version` 输入上的工作流与 `cancel-in-progress: false` 分组。发布运行使 `waiting` 处于审批状态，随后会阻止同一版本的每次分发，直到被取消 (`gh run cancel <id>`)。

## 稳定版本——当前路径

### 飞行前

```bash
# 1. What's live (rule 1) → pick <VERSION> strictly above it (normally patch bump).
# 2. Confirm main's package.json is at the *previous* published version, i.e. the repo
#    reflects the live line and nothing unreleased is already bumped:
node -p "require('./apps/vscode/package.json').version"
# 3. What's in the release:
git fetch origin main --tags
git log v<PREV>..origin/main --oneline --no-merges -- apps/vscode sdk/packages
```

CLI/SDK 注释是扩展注释的最佳起点 - 来自源的扩展捆绑包 `@cline/*`，因此同一窗口中的 SDK 版本也发布在这里。阅读`sdk/CHANGELOG.md`以获取匹配的SDK版本并翻译扩展名可见的内容；跳过仅限 CLI- 和仅限桌面的项目。

**扩展的范围注释。**范围内的共享代码提交通常针对CLI或桌面。仅当更改达到 `apps/vscode` 时才保留项目符号。它在没有集线器的情况下运行 SDK 进程内，因此集线器修复不适用，并且仅桌面或仅 CLI- 功能也不适用。跳过最后一个扩展部分中已有的项目，grep 任何引用的字符串，并忽略任何不确定的内容。

### `main` 上的发布准备（PR，不是直接推送）

- 凹凸 `apps/vscode/package.json` → `<VERSION>`。
- 将 `## [<VERSION>]` 添加到根 `CHANGELOG.md` 之前并添加批准的注释。
- 碰撞的副作用：夜间版本成为新基地的`<major>.<minor>.<unix-ts>`——无害（单独列出，仍然单调）。

排练：用 `publish=false` 调度（一旦碰撞发生在 `main` 上）。它使用真实的遥测环境运行预检+测试+真实的CI构建，并将`.vsix`作为运行工件上传，不涉及环境批准（`gh run download <run-id>`）。本地 `vsce package` 只是一个构建检查 - 遥测环境未在本地设置，因此工件的遥测在设计上是黑暗的。

### 派遣

```bash
gh workflow run ext-vscode-publish.yml --ref main -f version=<VERSION> -f publish=true
gh run list --workflow=ext-vscode-publish.yml --limit 1 --json databaseId,url,status
```

运行按顺序执行的操作：`preflight`（版本正则表达式，拒绝`publish=true`关闭`main`，市场单调性）和测试门→`build`检查调度SHA，断言`package.json`版本==输入和（发布仅）更改日志标题 → `bun install --frozen-lockfile` → `bun run build:sdk` → 断言 `better-sqlite3` 本机二进制文件 → 交换 `README.marketplace.md` 并运行 `vsce package --no-dependencies` （这运行 `vscode:prepublish` = `bun run package`；它是唯一的构建）→ 断言打包的清单是 `saoudrizwan.claude-dev@<VERSION>`，并且 `dist/extension.js` 对于构建步骤提供的任何 11 构建时变量都没有剩余的 `process.env.*` 文字（遥测密钥、`CLINE_ENVIRONMENT`、OTEL 导出器设置）→ 上传工件 → `publish` **等待`publish`环境批准**，然后在构建时重新检查单调性和两个PAT→`vsce publish --packagePath`→`ovsx publish --packagePath`→标签`v<VERSION>` SHA → GitHub 与 `.vsix` 一起发布 → Slack 帖子。检查运行正在等待什么：

```bash
gh api repos/cline/cline/actions/runs/<run-id>/pending_deployments
```

没有预发布输入（A/B 路径也从未有过）；每个发布都会进入列表的发布渠道。

### 发布后

1. 验证两个注册表均提供新版本（预计在日志中出现“已发布”后，市场验证会出现几分钟到一小时的延迟）：

   ```bash
   # Marketplace: query from rule 1
   curl -s "https://open-vsx.org/api/saoudrizwan/claude-dev" | python3 -c "import json,sys; d=json.load(sys.stdin); print(d['version'], d['timestamp'])"
   ```

2. 验证记账落地：`git fetch --tags && git tag --list 'v<VERSION>'`、`gh release view v<VERSION>`、发布通道中的 Slack 帖子。 **红色运行≠发布失败**：tag/release/Slack在*市场上传之后运行*，每个`continue-on-error`都根据市场步骤的结果进行门控。如果日志显示 `Published saoudrizwan.claude-dev v<VERSION>.`，则版本已发布。已知的簿记失败：构建的提交触及`.github/workflows/**`（默认令牌无法创建此类引用）→标签推送失败并且GitHub发布被跳过。手动修复（先询问）：`git tag v<VERSION> <built-sha> && git push origin refs/tags/v<VERSION>`，然后是`gh release create v<VERSION> <downloaded .vsix> --notes-file <changelog section>`。

3. 工件检查（`gh run download <run-id>`或发布资产）：`package.json`里面是`saoudrizwan.claude-dev@<VERSION>`； `grep -c 'process.env.TELEMETRY_SERVICE_API_KEY' extension/dist/extension.js` 必须是 **0** （剩余的文字意味着构建在没有其环境的情况下运行，并且遥测已悄然死亡）。构建作业在上传工件之前会在打包的字节上断言这两者，因此这是一个双重检查。对于 `apps/vscode/esbuild.mjs` 的普通构建，`CLINE_ROLLOUT_VARIANT` 被定义为 `""`，因此不需要任何文字，并且遥测不包含 `extension_variant` — 对于独立构建来说是正确的。

4. 监视 `otel.otel_logs` 中 `extension_version = '<VERSION>'` 上的错误（稳定队列是完全可分离的 - 每晚版本是时间戳）。元数据库仪表板 17（任务错误率）和 ​​19（错误深入探究）。

## 每晚发布

**仅手动调度。** cron 被故意删除：`PublishNightly` 环境使计划运行坐在 `waiting` 上，保留并发组，并默默地取消其后面的每个后续计划运行。因此，每晚的列表都会陈旧，而不是错误。

```bash
gh workflow run ext-vscode-publish-nightly.yml --ref main                 # real publish
gh workflow run ext-vscode-publish-nightly.yml --ref main -f dry-run=true # artifact only
```

没有 changelog/version 准备 — 版本已计算。使用针对 `saoudrizwan.cline-nightly` 的市场查询进行验证。在转换之前，nightly 仍然会运送组合加载器 VSIX 以及从 `legacy-extension` 构建的 `legacy/` 捆绑包。

**红色运行≠失败的发布**在此路径上：其标签推送步骤在*发布后运行*，并且每当主的HEAD触及`.github/workflows/**`时就会失败。如果日志中出现“已发布”，则表示该版本已经发布；使用用户凭据手动推送 `nightly-main-<UTC ts>-<sha12>` 标签。

## 紧急回滚

首选：**通过上述稳定工作流程以更高版本**发布`main`的固定版本。这是相同的路径，完全封闭，并且是使用户保持在 SDK 扩展上的唯一回滚。

最后的手段 - 遗留修补程序路径 - 仍然存在，但已降级：`legacy-extension` 是 SDK npm 之前的代码库，最后触及 2026-08-18 在 `4.0.12`，并发布从此，每个用户都会回到几周后的代码上。如果需要的话：

```bash
# On legacy-extension: commit the fix, bump apps/vscode/package.json ABOVE the
# live stable version (rule 1 — e.g. 4.1.18 live -> hotfix is 4.1.19, not 4.0.13),
# add the matching `## [x.y.z]` entry to root CHANGELOG.md, push.
gh workflow run ext-vscode-publish-legacy.yml --ref main -f release-type=release
# (the branch is hardcoded to legacy-extension in the workflow)
```

npm 套件以非门控方式运行，发布作业等待 `publish` 环境、工作流标记并创建 GitHub 版本本身，并将其发布到市场 ** 和 ** 打开 VSX。在该分支上使用 `npm`，而不是 `bun`，并期望旧的整体布局 (`apps/vscode/src/core/...`)。

## 退役 A/B 机器（仍有待完成）

推出已完成，但脚手架仍在回购中。按此顺序将其退役，每个作为其自己的 PR：

1. **Nightly → `main`** 的普通构建：从 `ext-vscode-publish-nightly.yml` 中删除加载程序/`legacy-src` 拼接，以便 nightly 匹配稳定。编辑时保留 `inputs.*` 的 `|| 'default'` 后备。
2. `ext-vscode-publish.yml`发布版本后，删除`ext-vscode-publish-stable.yml`（在4.1.18上超时的重新打包路径）和`ext-vscode-ab-package.yml`；并且，一旦判断上述应急路径不必要，则`ext-vscode-publish-legacy.yml`；保留 `legacy-extension` 分支作为历史记录。 `apps/vscode/scripts/publish-marketplace.mjs` 和 `publish:marketplace*` 包脚本仅服务于已删除的稳定工作流程（每晚都有自己的脚本）——在同一个 PR 中删除它们。
3. 删除 `apps/vscode-rollout/` 和 `apps/vscode/src/services/telemetry/rollout-metadata.ts` 中的仅转出代码路径（`extension_variant` 元数据和 `extension.rollout.bundle_activated` 事件）。
4. ~~移植市场单调性预检~~ — 完成； `ext-vscode-publish.yml` 携带支票的两份副本。
5. **最后存档 PostHog 标志，但尚未存档。** 仍在组合 VSIX (`≤ 4.1.17`) 上的机器在每个窗口加载时咨询 `ext-sdk-bundle-rollout` 并处理*删除了*标记为`legacy`。将其保留在 100% 直到组合版本平坦线的 `extension.rollout.bundle_activated`，然后存档。在接触之前根据经验重新验证百分比（不需要 PostHog 管理员 - 密钥内联在任何附带的组合加载器中；从市场 `vspackage` URL、`gunzip` 下载 4.1.17 VSIX， `unzip`、`grep -o 'phc_[A-Za-z0-9]*' extension/extension.js`）：

   ```bash
   node -e '
   const KEY = process.argv[1];
   (async () => {
     let t = 0, n = 200;
     for (let i = 0; i < n; i += 20) {
       const rs = await Promise.all(Array.from({length: 20}, (_, j) =>
         fetch("https://data.cline.bot/decide?v=3", { method: "POST",
           headers: {"Content-Type": "application/json"},
           body: JSON.stringify({api_key: KEY, distinct_id: `probe-${i+j}-${Math.random()}`})
         }).then(r => r.json())));
       for (const r of rs) if ((r.featureFlags||{})["ext-sdk-bundle-rollout"] === true) t++;
     }
     console.log(`~${(100*t/n).toFixed(1)}% (${t}/${n})`);
   })()' "$KEY"
   ```

6. 更新此技能：删除此部分以及 Nightly 下的组合装载机注释。

## 陷阱索引

- `apps/vscode` 中的 `bun run package` 不会构建 `@cline/*` 工作区 deps — 新的结帐首先需要 `bun run build:sdk`（工作流程处理此问题）。
- 每个外部工作流程都固定 `bun-version: 1.3.14`，而根 `packageManager` 是 `bun@1.3.13`。这在所有这些工作流程中都是一致的，并且交付得很好——不要单独在一个工作流程中“修复”它。
- 发布作业故意固定 ** 节点 22**：节点 24 / npm 11 可以使 vsce 的 `npm list` 检测在打包期间失败并显示 `ELSPROBLEMS`。 `setup-bun` 不提供 Node 运行时，发布脚本和 `npx ovsx` 需要一个。
- **通过简短的新鲜工作发布预构建工件（`vsce publish --packagePath`），而不是源树中的裸露`vsce publish`。**证据，而不是机制：裸露路径（稳定的工作流程）挂在5/5上4.1.18（9月2026）而`--packagePath`发布连续五次花费了3–15秒（4.1.16–4.1.20）。从日志 + vsce 源确定的事实：挂起的请求是 `OPTIONS /_apis/gallery`（azure-devops-node-api 路由发现，第一个请求，无正文），因此 VSIX 字节从未离开运行器；超时是 typed-rest-client 的固定 180 秒套接字超时；稳定作业运行工作区供应的 vsce **3.9.2**（`bun run` 将 `node_modules/.bin` 首先放在 PATH 上 — 它安装的全局 4.0.0 未使用；日志中的 `tmp-<pid>-…` 临时名称为3.9.2 的），而不是 4.0.0； A/B 路径与 3.9.2（9 月 2）和 4.0.0（9 月 17、22）均成功。客户端堆栈在其他方面是相同的（typed-rest-client 1.8.11、azure-devops-node-api 12.5.0、节点 22、相同的运行程序图像、相同的 PAT）。排除：工件 size/content、`main` 条目大小、清单字段、vsce 4.0.0、自 6 月以来的输入漂移。未知：为什么第一个请求挂在该作业形状上。新工作流程使用 `--skip-duplicate` 重试发布到 3×（安全：vsce 首先检查列表，并将上传的 409 映射到“已发布”+ 退出 0；WITHOUT 标记重复是一个错误，这将导致已着陆但响应丢失上传到红色运行，没有 tag/release/Slack）。市场版本时间戳延迟上传 ~13 分钟（验证），因此 `vsce` 超时并不能证明上传失败 - 在手动执行任何操作之前检查实时版本。
- `gh run watch --exit-status` 在运行失败时返回了退出 0。在对结果采取行动之前，请务必先与 `gh run view <id> --json status,conclusion` 确认。
- 工作流程 YAML 中的作业级别 `if:` 引用检查是建议性的（已调度的分支运行其自己的文件副本）；强制边界是存储库设置中每个环境的部署分支策略。
- Marketplace PAT (`VSCE_PAT`/`OVSX_PAT`) 仅安装到发布步骤中；没有发布工作流具有不受信任的触发面。
- 等待的环境批准运行不会很快超时 - 它们会等待数天并阻止其标签的并发组。
- Open VSX 之前曾进行过首次发布（日志显示“已发布”，API 404 持续数小时）。使用 API 查询而不是日志行进行验证。


## 原文对照

<details>
<summary>展开完整原文</summary>

# VS Code Extension Release

Use this skill when the user asks to release, publish, or ship the VS Code extension — stable, nightly, or an emergency legacy hotfix — or to retire the leftover A/B rollout machinery.

> Working directory: repo root. All workflows are dispatched from `main` (GitHub requires the workflow file on the default branch; each workflow checks out the refs it actually builds).

## The current era: standalone SDK extension from `main`

**The legacy → SDK migration is complete.** The PostHog flag `ext-sdk-bundle-rollout` reached 100% (verified empirically 2026-09-15: 200/200 `/decide` probes returned `true`), so every user on the combined VSIX runs the `next` (SDK, bun) bundle and the `legacy/` half is dead weight. Stable releases now ship a **plain build of `main`** through `ext-vscode-publish.yml` — a copy of the A/B workflow with the loader, `legacy/` bundle and stitching removed, and the same build-artifact-then-`vsce publish --packagePath` publish step. The combined A/B path (`ext-vscode-ab-package.yml`) is no longer used for releases and is pending deletion — see "Retiring the A/B machinery" at the bottom for what is still left to clean up and the one caveat (leave the flag at 100%).

**Do not use `ext-vscode-publish-stable.yml`.** Its publish step (`bun run publish:marketplace` → bare `vsce publish`, which re-runs `vscode:prepublish` and repacks the source tree) hung on all five attempts for v4.1.18 (2026-09-15/17), each exactly 180s after "Publishing ..." with `Request timeout: /_apis/gallery`. That path is the client's FIRST request (route discovery, no body) — the VSIX was never uploaded, so nothing about the artifact is the cause. The same code line went out as 4.1.19 through the prebuilt-artifact path in seconds. Root cause is not established (see the gotcha at the bottom); the stable workflow is kept only until the new one has shipped a release, then deleted.

History, for context only: the A/B era ran `4.1.0` → `4.1.19` (Jul–Sep 2026). Design docs remain at `apps/vscode-rollout/README.md` and PR #12253 until that directory is removed.

### The listings and the workflows

| Channel | Marketplace ID | Workflow | Trigger | Version |
|---|---|---|---|---|
| **Stable** | `saoudrizwan.claude-dev` | `ext-vscode-publish.yml` | dispatch, from `main` | `apps/vscode/package.json` on `main`; the `version` input must equal it |
| Nightly | `saoudrizwan.cline-nightly` | `ext-vscode-publish-nightly.yml` | **manual dispatch only** (cron deliberately removed) | auto `<major>.<minor>.<unix-ts>` from main's `apps/vscode/package.json` |
| Legacy hotfix (emergency only) | `saoudrizwan.claude-dev` | `ext-vscode-publish-legacy.yml` | dispatch | `apps/vscode/package.json` on `legacy-extension` |

Stable runs four jobs: `preflight` (input regex, main-only publish, Marketplace monotonicity) and the reusable bun suite (`ext-vscode-test.yml`, tests the dispatch SHA) ungated → `build` (no environment; packages the `.vsix` as a run artifact, so `publish=false` rehearsals need no approval) → `publish` (`publish` environment — required reviewers approve in the Actions UI; uploads the artifact, then tag/release/Slack). Nightly uses `PublishNightly` (branch policy only). Nightly **still builds the combined loader VSIX** (loader + `next/` + `legacy/`) until it is converted back to a plain build — that conversion is on the retirement list below.

## Golden rules (read before any release)

1. **One listing, one version line.** `claude-dev` has been published from multiple workflows and branches. Every stable publish must use a version **strictly above the highest version ever published to the listing from any branch** — marketplace versions are monotonic and cannot be unpublished (supersede, never delete). Check what's live first:

   ```bash
   curl -s -X POST "https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery" \
     -H "Content-Type: application/json" -H "Accept: application/json;api-version=3.0-preview.1" \
     -d '{"filters":[{"criteria":[{"filterType":7,"value":"saoudrizwan.claude-dev"}]}],"flags":16}' \
     | python3 -c "import json,sys; v=json.load(sys.stdin)['results'][0]['extensions'][0]['versions'][0]; print(v['version'], v['lastUpdated'])"
   ```

   `ext-vscode-publish` runs this same check twice (preflight, and again right before the upload — the approval wait can last days), so a non-monotonic dispatch fails in seconds. Still run the query yourself to pick the number. The `legacy-extension` branch sits at `4.0.12`, so it cannot collide with the `4.1.x` line, but a legacy hotfix would have to be numbered above the live stable version (see the emergency section).

2. **`version` input == package version, enforced.** The build job reads `apps/vscode/package.json` and hard-fails unless it equals the `version` input (plain `X.Y.Z`, no `v`). The input is a confirmation, not a stamp — bump the version on `main` first. The `v<version>` tag is created by the workflow *after* a successful Marketplace publish.

3. **Changelog lives at the repo ROOT** (`CHANGELOG.md`) — not `apps/vscode/CHANGELOG.md` (doesn't exist). The workflow hard-fails unless the first `## [` heading is exactly `## [<version>]`. The section body becomes the GitHub release notes and the Slack post (Slack copy is trimmed to 3000 chars with a link out; the release body stays whole).

4. **Ask before pushing** commits or tags. **Never approve the `publish` environment gate yourself via `gh api`** — hand the maintainer the run URL to click "Review deployments".

5. **Concurrency**: the workflow groups on the `version` input with `cancel-in-progress: false`. A publish run left `waiting` on approval blocks every later dispatch of the same version until cancelled (`gh run cancel <id>`).

## Stable release — the current path

### Pre-flight

```bash
# 1. What's live (rule 1) → pick <VERSION> strictly above it (normally patch bump).
# 2. Confirm main's package.json is at the *previous* published version, i.e. the repo
#    reflects the live line and nothing unreleased is already bumped:
node -p "require('./apps/vscode/package.json').version"
# 3. What's in the release:
git fetch origin main --tags
git log v<PREV>..origin/main --oneline --no-merges -- apps/vscode sdk/packages
```

The CLI/SDK notes are the best starting point for the extension notes — the extension bundles `@cline/*` from source, so an SDK release in the same window ships here too. Read `sdk/CHANGELOG.md` for the matching SDK version and translate what's extension-visible; skip CLI-only and desktop-only items.

**Scope notes to the extension.** Shared-code commits in the range are often for the CLI or desktop. Keep a bullet only if the change reaches `apps/vscode`. It runs the SDK in-process with no hub, so hub fixes don't apply, and desktop-only or CLI-only features don't either. Skip items already in the last extension section, grep any quoted string, and leave out anything uncertain.

### Release prep on `main` (PR, not direct push)

- Bump `apps/vscode/package.json` → `<VERSION>`.
- Prepend `## [<VERSION>]` to root `CHANGELOG.md` with the approved notes.
- Side effect of the bump: nightly versions become `<major>.<minor>.<unix-ts>` of the new base — harmless (separate listing, still monotonic).

Rehearsal: dispatch with `publish=false` (once the bump is on `main`). It runs preflight + tests + the real CI build with the real telemetry env and uploads the `.vsix` as a run artifact, with no environment approval involved (`gh run download <run-id>`). A local `vsce package` is only a build check — telemetry env is not set locally, so that artifact's telemetry is dark by design.

### Dispatch

```bash
gh workflow run ext-vscode-publish.yml --ref main -f version=<VERSION> -f publish=true
gh run list --workflow=ext-vscode-publish.yml --limit 1 --json databaseId,url,status
```

What the run does, in order: `preflight` (version regex, refuses `publish=true` off `main`, Marketplace monotonicity) and the test gate → `build` checks out the dispatch SHA, asserts `package.json` version == input and (publish only) the changelog heading → `bun install --frozen-lockfile` → `bun run build:sdk` → asserts the `better-sqlite3` native binary → swaps in `README.marketplace.md` and runs `vsce package --no-dependencies` (this runs `vscode:prepublish` = `bun run package`; it is the only build) → asserts the packaged manifest is `saoudrizwan.claude-dev@<VERSION>` and that `dist/extension.js` has no leftover `process.env.*` literal for any of the 11 build-time variables the build step supplies (telemetry keys, `CLINE_ENVIRONMENT`, OTEL exporter settings) → uploads the artifact → `publish` **waits for `publish` environment approval**, then re-checks monotonicity and both PATs → `vsce publish --packagePath` → `ovsx publish --packagePath` → tag `v<VERSION>` at the built SHA → GitHub release with the `.vsix` → Slack post. Check what a run is waiting on:

```bash
gh api repos/cline/cline/actions/runs/<run-id>/pending_deployments
```

There is no pre-release input (the A/B path never had one either); every publish goes to the release channel of the listing.

### Post-publish

1. Verify both registries serve the new version (expect minutes-to-an-hour of Marketplace validation lag after "Published" appears in the logs):

   ```bash
   # Marketplace: query from rule 1
   curl -s "https://open-vsx.org/api/saoudrizwan/claude-dev" | python3 -c "import json,sys; d=json.load(sys.stdin); print(d['version'], d['timestamp'])"
   ```

2. Verify the bookkeeping landed: `git fetch --tags && git tag --list 'v<VERSION>'`, `gh release view v<VERSION>`, Slack post in the release channel. **Red run ≠ failed publish**: tag/release/Slack run *after* the Marketplace upload, each `continue-on-error`, gated on the Marketplace step's outcome. If the log says `Published saoudrizwan.claude-dev v<VERSION>.` the release is out. Known bookkeeping failure: the built commit touches `.github/workflows/**` (the default token cannot create such refs) → the tag push fails and the GitHub release is skipped. Fix by hand (ask first): `git tag v<VERSION> <built-sha> && git push origin refs/tags/v<VERSION>`, then `gh release create v<VERSION> <downloaded .vsix> --notes-file <changelog section>`.

3. Artifact check (`gh run download <run-id>` or the release asset): `package.json` inside is `saoudrizwan.claude-dev@<VERSION>`; `grep -c 'process.env.TELEMETRY_SERVICE_API_KEY' extension/dist/extension.js` must be **0** (a leftover literal means the build ran without its env and telemetry is silently dead). The build job asserts both of these on the packaged bytes before uploading the artifact, so this is a double-check. `CLINE_ROLLOUT_VARIANT` is defined to `""` for ordinary builds by `apps/vscode/esbuild.mjs`, so no literal is expected and telemetry carries no `extension_variant` — correct for a standalone build.

4. Monitor errors on `extension_version = '<VERSION>'` in `otel.otel_logs` (stable cohort is cleanly separable — nightly versions are timestamps). Metabase dashboards 17 (task error rate) and 19 (error deep dive).

## Nightly release

**Manual dispatch only.** The cron was removed on purpose: the `PublishNightly` environment made scheduled runs sit `waiting`, hold the concurrency group, and silently cancel every later scheduled run behind them. A stale nightly listing is therefore expected, not a bug.

```bash
gh workflow run ext-vscode-publish-nightly.yml --ref main                 # real publish
gh workflow run ext-vscode-publish-nightly.yml --ref main -f dry-run=true # artifact only
```

No changelog/version prep — the version is computed. Verify with the Marketplace query against `saoudrizwan.cline-nightly`. Until converted, nightly still ships the combined loader VSIX with a `legacy/` bundle built from `legacy-extension`.

**Red run ≠ failed publish** on this path: its tag-push step runs *after* publishing and fails whenever main's HEAD touches `.github/workflows/**`. If "Published" appears in the logs, the release went out; push the `nightly-main-<UTC ts>-<sha12>` tag manually with user credentials.

## Emergency rollback

Preferred: **ship a fixed build of `main` at a higher version** through the stable workflow above. It is the same path, fully gated, and the only rollback that keeps users on the SDK extension.

Last resort — the legacy hotfix path — still exists but is degraded: `legacy-extension` is the pre-SDK npm codebase, last touched 2026-08-18 at `4.0.12`, and a publish from it would move every user back onto code that is weeks behind. If it is ever needed:

```bash
# On legacy-extension: commit the fix, bump apps/vscode/package.json ABOVE the
# live stable version (rule 1 — e.g. 4.1.18 live -> hotfix is 4.1.19, not 4.0.13),
# add the matching `## [x.y.z]` entry to root CHANGELOG.md, push.
gh workflow run ext-vscode-publish-legacy.yml --ref main -f release-type=release
# (the branch is hardcoded to legacy-extension in the workflow)
```

The npm suite runs ungated, the publish job waits on the `publish` environment, the workflow tags and creates the GitHub release itself, and it publishes to Marketplace **and** Open VSX. On that branch use `npm`, never `bun`, and expect the old monolith layout (`apps/vscode/src/core/...`).

## Retiring the A/B machinery (still to do)

The rollout is done but the scaffolding is still in the repo. Retire it in this order, each as its own PR:

1. **Nightly → plain build of `main`**: drop the loader/`legacy-src` stitching from `ext-vscode-publish-nightly.yml` so nightly matches stable. Preserve the `|| 'default'` fallbacks for `inputs.*` while editing.
2. Once `ext-vscode-publish.yml` has shipped a release, delete `ext-vscode-publish-stable.yml` (the repack path that timed out on 4.1.18) and `ext-vscode-ab-package.yml`; and, once the emergency path above is judged unnecessary, `ext-vscode-publish-legacy.yml`; keep the `legacy-extension` branch for history. `apps/vscode/scripts/publish-marketplace.mjs` and the `publish:marketplace*` package scripts only serve the deleted stable workflow (nightly has its own script) — remove them in the same PR.
3. Remove `apps/vscode-rollout/` and the rollout-only code paths in `apps/vscode/src/services/telemetry/rollout-metadata.ts` (the `extension_variant` metadata and `extension.rollout.bundle_activated` event).
4. ~~Port the marketplace-monotonicity preflight~~ — done; `ext-vscode-publish.yml` carries both copies of the check.
5. **Archive the PostHog flag last, and not yet.** Machines still on a combined VSIX (`≤ 4.1.17`) consult `ext-sdk-bundle-rollout` on every window load and treat a *deleted* flag as `legacy`. Leave it at 100% until `extension.rollout.bundle_activated` for combined versions flatlines, then archive. Re-verify the percentage empirically before touching it (no PostHog admin needed — the key is inlined in any shipped combined loader; download the 4.1.17 VSIX from the Marketplace `vspackage` URL, `gunzip`, `unzip`, `grep -o 'phc_[A-Za-z0-9]*' extension/extension.js`):

   ```bash
   node -e '
   const KEY = process.argv[1];
   (async () => {
     let t = 0, n = 200;
     for (let i = 0; i < n; i += 20) {
       const rs = await Promise.all(Array.from({length: 20}, (_, j) =>
         fetch("https://data.cline.bot/decide?v=3", { method: "POST",
           headers: {"Content-Type": "application/json"},
           body: JSON.stringify({api_key: KEY, distinct_id: `probe-${i+j}-${Math.random()}`})
         }).then(r => r.json())));
       for (const r of rs) if ((r.featureFlags||{})["ext-sdk-bundle-rollout"] === true) t++;
     }
     console.log(`~${(100*t/n).toFixed(1)}% (${t}/${n})`);
   })()' "$KEY"
   ```

6. Update this skill: delete this section and the combined-loader notes under Nightly.

## Gotchas index

- `bun run package` in `apps/vscode` does not build `@cline/*` workspace deps — fresh checkouts need `bun run build:sdk` first (the workflows handle this).
- Every ext workflow pins `bun-version: 1.3.14` while the root `packageManager` is `bun@1.3.13`. This is consistent across all of them and has shipped fine — don't "fix" it in one workflow alone.
- The publish job pins **Node 22** on purpose: Node 24 / npm 11 can make vsce's `npm list` detection fail with `ELSPROBLEMS` during packaging. `setup-bun` provides no Node runtime, and the publish scripts and `npx ovsx` need one.
- **Publish the prebuilt artifact (`vsce publish --packagePath`) from a short, fresh job, never a bare `vsce publish` from the source tree.** Evidence, not mechanism: the bare path (stable workflow) hung 5/5 on 4.1.18 (Sep 2026) while `--packagePath` publishes took 3–15s five times in a row (4.1.16–4.1.20). Facts established from logs + vsce source: the hung request was `OPTIONS /_apis/gallery` (azure-devops-node-api route discovery, the very first request, no body), so the VSIX bytes never left the runner; the timeout is typed-rest-client's fixed 180s socket timeout; the stable job ran the workspace-vendored vsce **3.9.2** (`bun run` puts `node_modules/.bin` first on PATH — the global 4.0.0 it installed was unused; the `tmp-<pid>-…` temp name in the log is 3.9.2's), not 4.0.0; the A/B path succeeded with both 3.9.2 (Sep 2) and 4.0.0 (Sep 17, 22). Client stack is otherwise identical (typed-rest-client 1.8.11, azure-devops-node-api 12.5.0, Node 22, same runner image, same PAT). Ruled out: artifact size/content, `main` entry size, manifest fields, vsce 4.0.0, input drift since June. Unknown: why the first request hangs from that job shape. The new workflow retries the publish up to 3× with `--skip-duplicate` (safe: vsce checks the listing first, and maps a 409 from the upload, to "already published" + exit 0; WITHOUT the flag a duplicate is an error, which would turn a landed-but-response-lost upload into a red run with no tag/release/Slack). Marketplace version timestamps lag the upload by ~13 min (validation), so a `vsce` timeout does not prove the upload failed — check the live version before doing anything manual.
- `gh run watch --exit-status` has returned exit 0 on a failed run. Always confirm with `gh run view <id> --json status,conclusion` before acting on a result.
- Job-level `if:` ref checks in workflow YAML are advisory (a dispatched branch runs its own copy of the file); the enforced boundary is each environment's deployment-branch policy in repo settings.
- Marketplace PATs (`VSCE_PAT`/`OVSX_PAT`) are only mounted into publish steps; no publish workflow has an untrusted trigger surface.
- Environment-approval runs left waiting don't time out quickly — they sit for days and block their tag's concurrency group.
- Open VSX has held a first-time publish in moderation before (logs say "Published", API 404s for hours). Verify with the API query rather than the log line.


</details>
