# Cline CLI 发布技能

记录 CLI 包的变更日志、版本更新、标签、npm 发布与工作流操作；适用于该仓库的发布维护工作。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/cline/cline/commit/b51c27b09a93c7b13834c220d5188cdaa43a6569>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/publish-cli/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline CLI 发布技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: publish-cli
description: Use when preparing, tagging, and publishing an apps/cli npm release. Guides changelog drafting, apps/cli/package.json version bumps, cli-vX.Y.Z tags, local npm publishing, and the publish-cli GitHub workflow.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# CLI 发布

当用户要求发布 CLI、发布 `cline`、提升 CLI 版本、起草发行说明、创建 `cli-vX.Y.Z` 标签或触发 CLI 发布工作流程时，请使用此技能。

CLI 仅适用于 npm。不要添加备用分销渠道。 Windows 二进制文件由发布工作流通过 Azure 可信签名自动进行 Authenticode 签名（请参阅 `.github/actions/sign-windows-cli` 复合操作和 `apps/cli/DISTRIBUTION.md` 中的“Windows 代码签名”）；如果未配置签名机密，工作流会发出警告并发布未签名的二进制文件。本地发布 (`bun release cli`) 不签名 — 对于用户在 Windows 上运行的版本，更喜欢使用 GitHub 操作发布路径。

> 工作目录：从存储库根运行下面的每个命令。路径和脚本（例如 `apps/cli/package.json`、`sdk/packages/`、`bun release cli`、`bun run version`）是相对于存储库根编写的。

该技能应引导用户完成一个发布准备流程，然后提供发布路径选项。两个正常的发布路径是 GitHub 操作和来自经过身份验证的计算机的本地发布。

## 发布合约

- SDK先决条件：CLI通过`workspace:*`（`@cline/core`、`@cline/shared`和朋友）依赖于SDK。如果SDK自上次发布后发生变化，请先发布SDK，并等待其完成发布后再发布CLI。请参阅下面的“步骤 0：如果 SDK 发生更改，请先释放它”。
- 版本来源：`apps/cli/package.json`。
- 主要发布标签：`cli-vX.Y.Z`，其中`X.Y.Z`与`apps/cli/package.json`匹配。
- 每晚发布版本：`X.Y.Z-nightly.TIMESTAMP`。
- 发布准备包括批准的发行说明、版本提升和 `apps/cli/CHANGELOG.md` 更新。
- 发布路径：
  - GitHub 工作流程：`.github/workflows/cli-publish.yml`。
  - 本地发布助手：`bun release cli`。
- npm dist-tags 和 git 标签是分开的。 `--tag latest` 和 `--tag nightly` 是 npm 注册表通道。 `cli-vX.Y.Z` 是源历史记录和 GitHub 版本的 git 标签。
- GitHub 主要发布工作流程从 `main` 运行，需要现有的 `cli-vX.Y.Z` 标签，检查该标签并从中发布。
- GitHub 每晚工作流程使用 `nightly` dist-tag 发布到 npm，并且不创建标签。
- 本地发布助手需要在发布之前进行干净的签出和 `cli-vX.Y.Z` 以指向本地的 `HEAD` 和 `origin`。
- 本地 GitHub 发布创建需要 `gh` 通过存储库的发布权限进行身份验证。
- 在推送提交或标签之前始终询问。
- 除非明确要求，否则不要修改提交。

## 步骤0：如果SDK发生变化，请先释放它

在下面的工作流程中执行任何其他操作之前执行此操作。

CLI 根据 monorepo 中的 SDK 源进行构建和发布（`workspace:*` 表示 `@cline/core`、`@cline/shared` 等），因此 CLI 版本始终包含最新的 SDK 代码，无论是否SDK 已发布。构建和测试也使用该源，而不是 npm 中的任何内容。与 CLI 一起发布 SDK 仍然值得这样做，原因有两个：

- 中心新鲜度。集线器守护进程位于 `@cline/core` 中，并标记默认为 `@cline/core` 软件包版本的 `buildId`（`sdk/packages/core/src/hub/discovery/index.ts` 中的 `resolveHubBuildId`）。仅当 `buildId` 更改时（`sdk/packages/core/src/hub/daemon/index.ts` 中的 `isCompatibleHubRecord` / `retireIncompatibleHub`），正在运行的集线器才会退役并重新生成。因此，如果 SDK 代码更改但版本未更改，则升级 CLI 的用户会继续与已运行的集线器通信，而该集线器仍在执行旧的 SDK 代码。碰撞 SDK 版本会使新的 CLI 的 `buildId` 有所不同，因此过时的集线器被检测为不兼容并使用新代码重新生成。
- 释放卫生。我们希望定期发布 SDK；每当我们删除 CLI 版本时，都会删除一个，以使已发布的 SDK 与 CLI 发布的内容保持一致。

因此，当 SDK 发生更改时，请先发布它（这会碰撞 `@cline/core` 版本），然后在该碰撞之上剪切 CLI 版本。将 CLI 的 SDK 依赖项保留为 `workspace:*` — 修复方法是释放 SDK，而不是固定 CLI。

1. 检查未发布的 SDK 更改。

```sh
git fetch origin --tags
git tag --list 'sdk/sdk/v*' 'sdk-v*' --sort=-v:refname | head -1
git log <last-sdk-tag>..origin/main --oneline --no-merges -- sdk/packages
```

`sdk/<pkg>/v*`标签由`sdk-publish.yml`工作流程创建； `sdk-v*` 标签由本地 `bun release sdk` 帮助程序创建。使用最新的作为基线。

如果 `git log` 没有打印任何提交，则 SDK 已经是最新的。跳过步骤 0 的其余部分并继续下面的工作流程。

如果它打印提交，请对差异进行健全性检查（忽略仅是先前版本碰撞提交的锁定文件或生成文件的条目），然后释放 SDK。

2. 决定SDK版本冲突。

所有 SDK 软件包共享一个版本，从 `sdk/packages/llms/package.json` 读取。询问这是补丁版本、次要版本、主要版本还是显式版本。补丁是默认的。如果用户没有说清楚，请勿猜测。

3. 起草 SDK 发行说明并更新变更日志。

从步骤 1 中找到的 SDK 提交起草面向用户的注释，将提交消息翻译成面向用户的语言（与下面的 CLI 发行说明相同的方法）。在 `sdk/CHANGELOG.md` 的顶部添加一个新的 `## <version>` 部分，其中包含这些注释，使用不带日期的标题格式 `## <version>` — 与 `apps/cli/CHANGELOG.md` 相同的扁平、最新的顶部格式。这是 SDK 变更日志（所有 SDK 软件包共享一个版本）并且由手工维护； `sdk-publish.yml` 工作流程不会读取它。

4. 碰撞版本并重新生成。

```sh
bun run version <version>
```

这会将每个 SDK `package.json` 升级到新版本，重新生成锁定文件和生成的模型目录、格式和构建。查看结果。

5. 提交并将碰撞推至 `main`。

`sdk-publish.yml` 工作流程发布在 `main` 上提交的版本以及提交的标签，因此在工作流程运行之前，凹凸必须落在 `main` 上。

```sh
git add -A
git commit -m "chore(sdk): release v<version>"
```

推送前询问：

```sh
git push origin HEAD
```

6. 在 `latest` 通道上触发 SDK 发布工作流程。

```sh
gh workflow run sdk-publish.yml -f channel=latest -f confirm_publish=publish
gh run list --workflow=sdk-publish.yml --limit=1 --json databaseId,url,status,createdAt --jq '.[0]'
```

该工作流程运行 SDK 测试，将 `@cline/shared`、`@cline/llms`、`@cline/agents`、`@cline/core` 和 `@cline/sdk` 发布到 npm，其中包含 `latest` dist-tag依赖顺序，并推送 `sdk/<pkg>/v<version>` git 标签。

7. 等待 SDK 工作流程成功，然后再开始 CLI 发布。

```sh
gh run watch <run-id> --exit-status
```

不要启动 CLI 释放直到本次运行成功完成。的 CLI 不安装 SDK 来自 npm，但是切割 CLI 在干净、完成的基础上发布 SDK 发布使两者保持同步： CLI 然后发布提交位于 `@cline/core` 版本颠簸，所以发货了 CLI 携带新版本，强制正在运行的集线器使用新代码重生，并且您不是在构建 CLI 释放在 SDK 中途发布失败。

SDK 版本成功后，拉取 `main`，以便在 SDK 版本升级的基础上准备好 CLI 版本：

```sh
git checkout main && git pull --ff-only
```

然后继续下面的工作流程。

对于从经过身份验证的计算机而不是工作流程进行本地 SDK 发布，`bun release sdk <version>` 存在，但更喜欢使用 `sdk-publish.yml` 工作流程进行正常发布，以便 CLI 发布可以控制单个 GitHub 操作运行。

## 工作流程

首先完成步骤 0。仅在释放 SDK 后继续（或者您确认不需要释放 SDK）。

1. 收集背景信息。

```sh
git status --short --branch
git fetch origin --tags
git tag --list 'cli-v*' --sort=-v:refname | head -10
node -p "require('./apps/cli/package.json').version"
```

找到最新的CLI标签。如果没有 `cli-v*` 标签，则使用第一个相关的 CLI 发布提交作为基线，并说基线是推断出来的。

2. 收集发布提交。

```sh
git log <last-cli-tag>..HEAD --oneline --no-merges -- apps/cli sdk/packages sdk/scripts .github/workflows/cli-publish.yml
```

尽管 SDK 是在步骤 0 中单独发布的，但 `sdk/packages` 在这里提出了问题：CLI 捆绑了 SDK，因此 SDK 在此 CLI 版本中也发生了变化。阅读这些提交并将与 CLI 用户相关的任何内容折叠到发行说明中（provider/model 更新、行为更改、修复 CLI 继承）。跳过纯粹内部的或没有 CLI- 可见效果的 SDK 更改。

3. 起草面向用户的发行说明。

包括面向用户的功能、修复、行为更改、兼容性更改以及显着的安装或发布更改。排除纯粹的重构、测试、样式、杂务和内部文件移动，除非它们对用户很重要。

写一个简单的项目符号列表。将提交消息翻译成面向用户的语言。如果提交不清楚，请在总结之前阅读完整的提交。

**CLI.**范围内的共享代码提交通常适用于桌面或扩展。仅当更改达到 `apps/cli` 时才保留项目符号（集线器修复会这样做；桌面 webview/sidecar/voice/packaging 不会）。跳过最后一个 CLI 部分中已有的项目，grep 任何带引号的字符串，并忽略任何不确定的内容。

提交草稿并等待批准后再编辑文件。

4. 决定版本碰撞。

询问这应该是补丁版本、次要版本、主要版本还是显式版本。如果用户没有说清楚，请勿猜测。

5. 更新发布文件。

将`apps/cli/package.json`更新到批准的版本。

使用批准的发行说明在批准版本的 `apps/cli/CHANGELOG.md` 前面添加一个部分。使用不带日期的标头格式 `## X.Y.Z`。发布工作流程通过匹配 `^## [0-9]` 提取变更日志的顶部部分，并将其逐字粘贴到 GitHub 发布正文和 Slack 发布公告中，因此该部分内容是发布的发行说明。

6. 提交前验证。

首先运行重点检查：

```sh
bun -F @cline/cli typecheck
bun -F @cline/cli test:unit
```

为了获得更高的信心，请运行：

```sh
bun run types
bun --cwd apps/cli run build:platforms:single
```

如果用户希望在标记之前完全释放信心，请运行：

```sh
bun run test
bun --cwd apps/cli run build:platforms
```

已知的仅限本地测试失败：`src/commands/distribution-package.test.ts > rejects direct source package packing by default` 将在 `~/.npmrc` 中具有 `ignore-scripts=true` 的计算机上失败（由 npm 供应链强化指南设置）。 Bun 从 `~/.npmrc` 读取 npm 的 `ignore-scripts`，因此 `bun pm pack --dry-run` 跳过源发布 `prepack` 防护并退出 0，测试将其读取为失败。 CI 未设置 `ignore-scripts`，因此测试通过。直接运行`bun pm pack --dry-run`进行确认：`~/.npmrc`到位后，它退出0，没有保护输出；将 `~/.npmrc` 移到一边后，它退出 1 并打印防护消息。这本身并不是一个发布阻止程序，但它确实意味着本地发布路径（`bun release cli`）也将绕过本机上的源发布防护；首选在全局设置了 `ignore-scripts=true` 的计算机上使用 GitHub 操作发布路径，或者在本地发布期间暂时取消设置（`npm config delete ignore-scripts` 或 `mv ~/.npmrc ~/.npmrc.bak`）。

7. 提交发布更改。

仅在用户批准注释和版本后：

```sh
git add apps/cli/package.json apps/cli/CHANGELOG.md
git commit -m "chore(cli): release vX.Y.Z"
```

在推送发布提交之前询问：

```sh
git push origin HEAD
```

对于 GitHub 主要发布路径，在创建和推送发布标签之前询问：

```sh
git tag -a cli-vX.Y.Z -m "CLI vX.Y.Z"
git push origin refs/tags/cli-vX.Y.Z
```

8. 发布。

询问用户使用哪个路径：

- GitHub主要发布。在 `main` 上发布提交并且已推送匹配的 `cli-vX.Y.Z` 标签后使用此选项。工作流程从该标签发布到 npm，创建 GitHub 版本，然后发布到 Slack。
- 本地发布。当用户想要从本机发布时使用此选项。本地计算机必须通过 npm 和 GitHub 的身份验证。
- GitHub 每晚发布。
- 版本提交后停止。

对于 GitHub 主要版本：

```sh
gh workflow run cli-publish.yml -f publish_target=main -f git_tag=cli-vX.Y.Z -f confirm_publish=publish
gh run list --workflow=cli-publish.yml --limit=1 --json url,status,conclusion,createdAt --jq '.[0]'
```

对于 GitHub 每晚版本：

```sh
gh workflow run cli-publish.yml -f publish_target=nightly
```

对于强制 GitHub 每晚发布：

```sh
gh workflow run cli-publish.yml -f publish_target=nightly -f force_nightly_publish=true
```

对于本地发布：

```sh
gh auth status
npm whoami
git tag -a cli-vX.Y.Z -m "CLI vX.Y.Z"
git push origin refs/tags/cli-vX.Y.Z
bun release cli
```

本地发布成功后，运行前询问：

```sh
gh release create cli-vX.Y.Z --verify-tag --title "CLI vX.Y.Z" --notes "Paste the approved release notes here."
```

如果使用另一个 npm dist-tag 发布：

```sh
bun release cli --tag next
```

9. 最终回应。

报告：

- 版本
- 标签
- 变更日志文件已更新
- 提交哈希值
- 是否有任何东西被推动
- 发布路径选择
- 工作流程 URL 或本地发布结果
- 测试和构建运行


## 原文对照

<details>
<summary>展开完整原文</summary>

# CLI Release

Use this skill when the user asks to release the CLI, publish `cline`, bump the CLI version, draft release notes, create a `cli-vX.Y.Z` tag, or trigger the CLI publish workflow.

The CLI is npm-only. Do not add alternate distribution channels. Windows binaries are Authenticode-signed automatically by the publish workflow via Azure Trusted Signing (see the `.github/actions/sign-windows-cli` composite action and "Windows code signing" in `apps/cli/DISTRIBUTION.md`); if the signing secrets are not configured the workflow warns and publishes unsigned binaries. Local publishes (`bun release cli`) do not sign — prefer the GitHub Actions publish path for releases users run on Windows.

> Working directory: run every command below from the repository root. Paths and scripts (e.g. `apps/cli/package.json`, `sdk/packages/`, `bun release cli`, `bun run version`) are written relative to the repo root.

The skill should guide the user through one release preparation flow, then offer the publish path options. The two normal publish paths are GitHub Actions and local publishing from an authenticated machine.

## Release contract

- SDK prerequisite: the CLI depends on the SDK via `workspace:*` (`@cline/core`, `@cline/shared`, and friends). If the SDK changed since its last release, release the SDK first and wait for it to finish publishing before releasing the CLI. See "Step 0: Release the SDK first if it changed" below.
- Version source: `apps/cli/package.json`.
- Main release tag: `cli-vX.Y.Z`, where `X.Y.Z` matches `apps/cli/package.json`.
- Nightly release version: `X.Y.Z-nightly.TIMESTAMP`.
- Release prep includes approved release notes, a version bump, and an `apps/cli/CHANGELOG.md` update.
- Publish paths:
  - GitHub workflow: `.github/workflows/cli-publish.yml`.
  - Local publish helper: `bun release cli`.
- npm dist-tags and git tags are separate. `--tag latest` and `--tag nightly` are npm registry channels. `cli-vX.Y.Z` is a git tag for source history and GitHub releases.
- The GitHub main release workflow runs from `main`, requires an existing `cli-vX.Y.Z` tag, checks out that tag, and publishes from it.
- The GitHub nightly workflow publishes to npm with the `nightly` dist-tag and does not create a tag.
- The local release helper requires a clean checkout and `cli-vX.Y.Z` to point at `HEAD` locally and on `origin` before publishing.
- Local GitHub release creation requires `gh` to be authenticated with release permissions for the repo.
- Always ask before pushing commits or tags.
- Do not amend commits unless explicitly requested.

## Step 0: Release the SDK first if it changed

Do this before anything else in the Workflow below.

The CLI builds and ships against the SDK source in the monorepo (`workspace:*` for `@cline/core`, `@cline/shared`, and the rest), so a CLI release always contains the latest SDK code whether or not the SDK was released. The build and tests use that source too, not anything from npm. Releasing the SDK alongside the CLI is still worth doing for two reasons:

- Hub freshness. The hub daemon lives in `@cline/core` and stamps a `buildId` that defaults to the `@cline/core` package version (`resolveHubBuildId` in `sdk/packages/core/src/hub/discovery/index.ts`). A running hub is only retired and respawned when that `buildId` changes (`isCompatibleHubRecord` / `retireIncompatibleHub` in `sdk/packages/core/src/hub/daemon/index.ts`). So if the SDK code changed but the version did not, a user who upgrades the CLI keeps talking to their already-running hub, which is still executing the old SDK code. Bumping the SDK version makes the new CLI's `buildId` differ, so the stale hub is detected as incompatible and respawned with the fresh code.
- Release hygiene. We want regular SDK releases; cutting one whenever we cut a CLI release keeps the published SDK in step with what the CLI ships.

So when the SDK has changed, release it first (which bumps the `@cline/core` version), then cut the CLI release on top of that bump. Leave the CLI's SDK dependency as `workspace:*` — the fix is to release the SDK, not to pin the CLI.

1. Check for unreleased SDK changes.

```sh
git fetch origin --tags
git tag --list 'sdk/sdk/v*' 'sdk-v*' --sort=-v:refname | head -1
git log <last-sdk-tag>..origin/main --oneline --no-merges -- sdk/packages
```

`sdk/<pkg>/v*` tags are created by the `sdk-publish.yml` workflow; `sdk-v*` tags are created by the local `bun release sdk` helper. Use whichever is newest as the baseline.

If `git log` prints no commits, the SDK is already up to date. Skip the rest of Step 0 and continue with the Workflow below.

If it prints commits, sanity-check the diff (ignore entries that are only the previous version-bump commit's lockfile or generated files), then release the SDK.

2. Decide the SDK version bump.

All SDK packages share one version, read from `sdk/packages/llms/package.json`. Ask whether this is patch, minor, major, or an explicit version. Patch is the default. Do not guess if the user has not made it clear.

3. Draft the SDK release notes and update the changelog.

Draft user-facing notes from the SDK commits found in step 1, translating commit messages into user-facing language (same approach as the CLI release notes below). Prepend a new `## <version>` section with those notes to the top of `sdk/CHANGELOG.md`, using the header format `## <version>` with no date — the same flat, newest-on-top format as `apps/cli/CHANGELOG.md`. This is the SDK changelog (all SDK packages share one version) and it is maintained by hand; the `sdk-publish.yml` workflow does not read it.

4. Bump versions and regenerate.

```sh
bun run version <version>
```

This bumps every SDK `package.json` to the new version, regenerates the lockfile and the generated model catalog, formats, and builds. Review the result.

5. Commit and push the bump to `main`.

The `sdk-publish.yml` workflow publishes the version that is committed on `main` and tags that commit, so the bump must land on `main` before the workflow runs.

```sh
git add -A
git commit -m "chore(sdk): release v<version>"
```

Ask before pushing:

```sh
git push origin HEAD
```

6. Trigger the SDK publish workflow on the `latest` channel.

```sh
gh workflow run sdk-publish.yml -f channel=latest -f confirm_publish=publish
gh run list --workflow=sdk-publish.yml --limit=1 --json databaseId,url,status,createdAt --jq '.[0]'
```

The workflow runs the SDK tests, publishes `@cline/shared`, `@cline/llms`, `@cline/agents`, `@cline/core`, and `@cline/sdk` to npm with the `latest` dist-tag in dependency order, and pushes `sdk/<pkg>/v<version>` git tags.

7. Wait for the SDK workflow to succeed before starting the CLI release.

```sh
gh run watch <run-id> --exit-status
```

Do not start the CLI release until this run has finished successfully. The CLI does not install the SDK from npm, but cutting the CLI release on top of a clean, completed SDK release keeps the two in step: the CLI release commit then sits on top of the `@cline/core` version bump, so the shipped CLI carries the new version that forces a running hub to respawn with the new code, and you are not building a CLI release on top of an SDK release that failed midway.

After the SDK release succeeds, pull `main` so the CLI release is prepared on top of the SDK version bump:

```sh
git checkout main && git pull --ff-only
```

Then continue with the Workflow below.

For a local SDK publish from an authenticated machine instead of the workflow, `bun release sdk <version>` exists, but prefer the `sdk-publish.yml` workflow for normal releases so the CLI release can gate on a single GitHub Actions run.

## Workflow

Complete Step 0 first. Only proceed once the SDK is released (or you confirmed no SDK release was needed).

1. Gather context.

```sh
git status --short --branch
git fetch origin --tags
git tag --list 'cli-v*' --sort=-v:refname | head -10
node -p "require('./apps/cli/package.json').version"
```

Find the latest CLI tag. If there is no `cli-v*` tag, use the first relevant CLI release commit as the baseline and say that the baseline is inferred.

2. Collect release commits.

```sh
git log <last-cli-tag>..HEAD --oneline --no-merges -- apps/cli sdk/packages sdk/scripts .github/workflows/cli-publish.yml
```

The `sdk/packages` commits matter here even though the SDK was released separately in Step 0: the CLI bundles the SDK, so SDK changes ship in this CLI release too. Read those commits and fold anything user-relevant to the CLI into the release notes (provider/model updates, behavior changes, fixes the CLI inherits). Skip SDK changes that are purely internal or have no CLI-visible effect.

3. Draft user-facing release notes.

Include user-facing features, fixes, behavior changes, compatibility changes, and notable install or release changes. Exclude pure refactors, tests, style, chores, and internal file moves unless they matter to users.

Write a flat bullet list. Translate commit messages into user-facing language. If a commit is unclear, read the full commit before summarizing it.

**Scope notes to the CLI.** Shared-code commits in the range are often for desktop or the extension. Keep a bullet only if the change reaches `apps/cli` (hub fixes do; desktop webview/sidecar/voice/packaging don't). Skip items already in the last CLI section, grep any quoted string, and leave out anything uncertain.

Present the draft and wait for approval before editing files.

4. Decide the version bump.

Ask whether this should be patch, minor, major, or an explicit version. Do not guess if the user has not made it clear.

5. Update release files.

Update `apps/cli/package.json` to the approved version.

Prepend a section to `apps/cli/CHANGELOG.md` for the approved version using the approved release notes. Use the header format `## X.Y.Z` with no date. The publish workflow extracts the top section of the changelog by matching `^## [0-9]` and pastes it verbatim into the GitHub release body and the Slack release announcement, so the section content is the release notes that get shipped.

6. Verify before committing.

Run focused checks first:

```sh
bun -F @cline/cli typecheck
bun -F @cline/cli test:unit
```

For higher confidence, run:

```sh
bun run types
bun --cwd apps/cli run build:platforms:single
```

If the user wants full release confidence before tagging, run:

```sh
bun run test
bun --cwd apps/cli run build:platforms
```

Known local-only test failure: `src/commands/distribution-package.test.ts > rejects direct source package packing by default` will fail on machines that have `ignore-scripts=true` in `~/.npmrc` (set by the npm supply-chain hardening guide). Bun reads npm's `ignore-scripts` from `~/.npmrc`, so `bun pm pack --dry-run` skips the source-publish `prepack` guard and exits 0, which the test reads as a failure. CI does not set `ignore-scripts`, so the test passes there. Confirm by running `bun pm pack --dry-run` directly: with `~/.npmrc` in place it exits 0 with no guard output; with `~/.npmrc` moved aside it exits 1 and prints the guard message. This is not a release blocker by itself, but it does mean the local-publish path (`bun release cli`) will also bypass the source-publish guard on this machine; prefer the GitHub Actions publish path on machines with `ignore-scripts=true` set globally, or temporarily unset it (`npm config delete ignore-scripts` or `mv ~/.npmrc ~/.npmrc.bak`) for the duration of a local publish.

7. Commit release changes.

Only after the user approves the notes and version:

```sh
git add apps/cli/package.json apps/cli/CHANGELOG.md
git commit -m "chore(cli): release vX.Y.Z"
```

Ask before pushing the release commit:

```sh
git push origin HEAD
```

For the GitHub main release path, ask before creating and pushing the release tag:

```sh
git tag -a cli-vX.Y.Z -m "CLI vX.Y.Z"
git push origin refs/tags/cli-vX.Y.Z
```

8. Publish.

Ask the user which path to use:

- GitHub main release. Use this after the release commit is on `main` and the matching `cli-vX.Y.Z` tag has been pushed. The workflow publishes to npm from that tag, creates the GitHub release, and posts to Slack.
- Local release. Use this when the user wants to publish from this machine. The local machine must be authenticated to npm and GitHub.
- GitHub nightly release.
- Stop after the version commit.

For GitHub main release:

```sh
gh workflow run cli-publish.yml -f publish_target=main -f git_tag=cli-vX.Y.Z -f confirm_publish=publish
gh run list --workflow=cli-publish.yml --limit=1 --json url,status,conclusion,createdAt --jq '.[0]'
```

For GitHub nightly release:

```sh
gh workflow run cli-publish.yml -f publish_target=nightly
```

For forced GitHub nightly release:

```sh
gh workflow run cli-publish.yml -f publish_target=nightly -f force_nightly_publish=true
```

For local publish:

```sh
gh auth status
npm whoami
git tag -a cli-vX.Y.Z -m "CLI vX.Y.Z"
git push origin refs/tags/cli-vX.Y.Z
bun release cli
```

After a successful local publish, ask before running:

```sh
gh release create cli-vX.Y.Z --verify-tag --title "CLI vX.Y.Z" --notes "Paste the approved release notes here."
```

If publishing with another npm dist-tag:

```sh
bun release cli --tag next
```

9. Final response.

Report:

- version
- tag
- changelog file updated
- commit hash
- whether anything was pushed
- publish path selected
- workflow URL or local publish result
- tests and builds run


</details>
