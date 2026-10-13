# Cline 桌面应用发布技能

介绍稳定版、测试版和夜间构建的版本、标签、发布工作流与密钥配置；属于该项目的桌面应用发布操作说明。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/cline/cline/commit/b51c27b09a93c7b13834c220d5188cdaa43a6569>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/publish-desktop/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline 桌面应用发布技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: publish-desktop
description: Use when preparing, tagging, and publishing a Cline desktop app (apps/examples/desktop-app) release — stable (desktop-vX.Y.Z from main), beta (desktop-vX.Y.Z-beta.N from desktop-experimental, shipped as the side-by-side "Cline Beta" app), or nightly (artifact-only test build of main's HEAD, tagged desktop-nightly-<stamp> after it builds). Guides changelog drafting, version bumps in package.json + tauri.conf.json, tagging, and the desktop-publish GitHub workflow that builds, signs, notarizes, and updates the per-channel auto-update feed.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 桌面应用程序发布

当用户请求发布桌面应用程序、发布 Cline 桌面应用程序、削减桌面测试版、每晚削减桌面、升级桌面版本、创建 `desktop-vX.Y.Z`（或 `desktop-vX.Y.Z-beta.N`）标签或触发桌面发布工作流时，请使用此技能。

> 工作目录：从存储库根运行下面的每个命令。

桌面版发布了三个平台，完全在 GitHub Actions 中构建 - 没有本地发布路径。 macOS：单一签名+公证的通用DMG，可在 Apple Silicon 和 Intel 上本机运行。 Linux：在 `build-linux` 作业中的 Ubuntu 22.04 上构建的 x64 `.deb` 和 `.rpm` 软件包（`<Product>_<version>_amd64.deb`、`<Product>_<version>_x86_64.rpm`），仅 deb + rpm（无 AppImage：linuxdeploy 无法处理Bun-编译的 sidecar）。 Windows：Authenticode 签名的 NSIS 安装程序 (`<Product>_<version>_x64-setup.exe`)，通过 `build-windows` 作业中的 Azure 可信签名进行签名（通过 Tauri 的 `signCommand` 进行 jsign，请参阅 `apps/examples/desktop-app/scripts/tauri-sign-windows.ps1`；需要存储库级别 `AZURE_*` 机密，包括`AZURE_TRUSTED_SIGNING_CERTIFICATE_PROFILE_DESKTOP`，加上 `cline-cli-signing` Entra 应用程序上的 `PublishDesktop` 环境联合凭证）。已安装的应用程序会通过 Tauri 更新程序自动发现新版本，因此发布版本会将更新发送给该渠道 ** 上的每个现有用户 **。

## 发布合约

- 三个通道，一个工作流程（`channel` 在 `desktop-publish.yml` 上输入）：
  - **稳定** - 标签`desktop-vX.Y.Z`（无后缀；工作流程拒绝此通道上的预发布后缀），从`main`剪切，提供滚动`desktop-latest`版本，以“Cline”形式发货。
  - **beta** — 标签 `desktop-vX.Y.Z-beta.N`，从 `desktop-experimental` 中剪切，提供滚动 `desktop-beta` 版本，以“Cline Beta”形式发货（单独的捆绑包标识符 `bot.cline.app.beta`；与稳定）。采用额外的 `src-tauri/tauri.beta.conf.json` 覆盖层构建。流程背景：`apps/examples/desktop-app/EXPERIMENTAL.md`。
  - **nightly** — 没有发布标签输入，没有发布，没有提要。构建 `main` 的 HEAD，以“Cline Nightly”形式发布（标识符 `bot.cline.app.nightly`、空更新程序端点、`src-tauri/tauri.nightly.conf.json` 覆盖），仅将已签名的安装程序作为操作工件上传，然后标记提交 `desktop-nightly-<utc stamp>` 并宣布自前一个 nightly 或发布到 Slack 以来发生的变化。请参阅下面的“夜间构建”。
- 版本来源（必须相互匹配且与标签匹配）：`apps/examples/desktop-app/package.json` 和 `apps/examples/desktop-app/src-tauri/tauri.conf.json`。 （`src-tauri/Cargo.toml` 有自己的版本，但 `tauri.conf.json` 覆盖它；无需触及它。）
- Beta 版本是 **next** stable 的预发布：stable `0.0.13` → beta `0.0.14-beta.1`、`-beta.2`，...一旦稳定版 ≥ beta 基础版发布，下一个 beta 版本就会提升其基础版 (`0.0.15-beta.1`)。
- 发布准备包括已批准的发行说明、版本升级和 `apps/examples/desktop-app/CHANGELOG.md` 更新 - 致力于 `main` 稳定版，`desktop-experimental` 测试版。
- 发布路径：`.github/workflows/desktop-publish.yml`（workflow_dispatch；对于稳定版和测试版，它要求标签存在，指向签出的提交，并且可以从通道的分支访问 - `origin/main` 对于稳定版，`origin/desktop-experimental` 对于测试版。Nightly 不接受标签，验证会拒绝标签）。
- **每个通道从 `main` 调度。** 这是一个安全不变式，而不是方便：运行执行 `main` 的工作流程副本，并且只有签出点位于标签处，因此签名秘密门（`github.ref == main` 检查和PublishDesktop 环境的仅主要部署分支策略）也适用于测试版。切勿将 `desktop-experimental` 添加到 PublishDesktop 部署分支策略。
- 该工作流程创建标签的 GitHub 版本（通用 DMG + macOS 更新程序工件 + Windows NSIS 安装程序和 Linux deb/rpm 软件包，每个软件包都有其更新程序签名，+ `latest.json`；标记为 beta 预发布）并刷新频道的滚动源发布，这是该频道上每个安装的应用程序轮询的静态自动更新源。切勿删除 `desktop-latest` 或 `desktop-beta` 版本或标签。
- 变更日志的 `## <version>` 部分（完全匹配，而不是“最顶层”）被逐字提取到 GitHub 发布正文、Slack 公告和更新程序清单注释中。
- 在推送提交或标签之前始终询问。

## 工作流程

0. 如果用户没有说，请询问此版本适用于哪个频道 - **稳定版、测试版或夜间版 **。下面的一切都在它上面分支​​；永远不要猜测。

对于 **nightly**，完全跳过步骤 1–7 并跳转到“Nightly builds”。

1. 收集背景信息。

```sh
git status --short --branch
git fetch origin --tags
git tag --list 'desktop-v*' --sort=-v:refname | head -10
node -p "require('./apps/examples/desktop-app/package.json').version"
node -p "require('./apps/examples/desktop-app/src-tauri/tauri.conf.json').version"
```

如果还没有`desktop-v*`标签，则这是第一次发布；使用桌面应用程序的第一次提交作为基线，并说基线是推断出来的。

对于 **beta ** 版本，请处理 `desktop-experimental`（查看 `origin/desktop-experimental`；如果 `origin/main` 落后，请先将其合并到其中 - 请参阅 EXPERIMENTAL.md 了解冲突策略）并从该分支读取版本文件。最后一个标签基线是作为分支祖先的任一通道的最新 `desktop-v*` 标签。

2. 收集发布提交。

```sh
# stable (on main):
git log <last-desktop-tag>..HEAD --oneline --no-merges -- apps/examples/desktop-app sdk/packages .github/workflows/desktop-publish.yml
# beta (on desktop-experimental):
git log <last-desktop-tag>..origin/desktop-experimental --oneline --no-merges -- apps/examples/desktop-app sdk/packages .github/workflows/desktop-publish.yml
```

sidecar 捆绑了 `@cline/core` 和来自 monorepo 的朋友，因此 SDK 也在桌面应用程序内部进行了更改。将用户可见的 SDK 更改（提供者、模型、行为修复）折叠到注释中；跳过纯内部的。

3. 起草面向用户的发行说明。

扁平的项目符号列表，面向用户的语言。

**桌面应用程序的范围注释。**范围内的共享代码提交通常针对CLI或扩展。仅当更改达到 `apps/examples/desktop-app` 时才保留项目符号（CLI 子命令、TUI 和 yolo 提示规则则不会）。跳过最后一个桌面部分中已有的项目，grep 任何引用的字符串，并忽略任何不确定的内容。

提交草稿并等待批准后再编辑文件。

4. 决定版本碰撞。

稳定：询问这是否是补丁、次要、主要或显式版本。如果用户没有说清楚，请勿猜测。

Beta：应用版本控制规则 — 基础 = 下一个稳定版本，增量 `N`（`0.0.14-beta.1` → `0.0.14-beta.2`；稳定 `0.0.14` 发布后，下一个是 `0.0.15-beta.1`）。与用户确认计算出的版本。

5. 更新发布文件（在 `main` 上获得稳定版，在 `desktop-experimental` 上获得测试版）。

- `apps/examples/desktop-app/package.json` → 新版本
- `apps/examples/desktop-app/src-tauri/tauri.conf.json` → 相同版本
- 将 `## X.Y.Z`（无日期；`## X.Y.Z-beta.N` 用于测试版）添加到 `apps/examples/desktop-app/CHANGELOG.md` 之前并添加已批准的注释。

6. 提交前验证。

```sh
bun -F @cline/code typecheck
bun test apps/examples/desktop-app/scripts/generate-update-manifest.test.ts
```

完整的桌面捆绑包只能构建在 macOS 上；工作流程的构建工作才是真正的验证。为了让本地用户对 Mac 结帐更有信心，请使用应用程序目录中的 `bun run package:desktop:mac --allow-unsigned-mac`。

7. 提交发布更改。

```sh
git add apps/examples/desktop-app/package.json apps/examples/desktop-app/src-tauri/tauri.conf.json apps/examples/desktop-app/CHANGELOG.md
git commit -m "chore(desktop): release vX.Y.Z"
```

在推送发布提交之前询问，然后在创建和推送标签之前询问：

```sh
git push origin HEAD
git tag -a desktop-vX.Y.Z -m "Desktop vX.Y.Z"       # beta: desktop-vX.Y.Z-beta.N / "Desktop vX.Y.Z-beta.N"
git push origin refs/tags/desktop-vX.Y.Z
```

8. 发布。

发布提交必须位于通道的分支上（`main` 表示稳定版，`desktop-experimental` 表示测试版）并且标签首先推送。从 `main` 为 ** 每个 ** 通道调度（请参阅发布合同了解原因）。

```sh
# stable:
gh workflow run desktop-publish.yml --ref main -f git_tag=desktop-vX.Y.Z -f channel=stable -f confirm_publish=publish
# beta:
gh workflow run desktop-publish.yml --ref main -f git_tag=desktop-vX.Y.Z-beta.N -f channel=beta -f confirm_publish=publish
# nightly (no tag — see "Nightly builds"):
gh workflow run desktop-publish.yml --ref main -f channel=nightly -f confirm_publish=publish

gh run list --workflow=desktop-publish.yml --limit=1 --json url,status,conclusion,createdAt --jq '.[0]'
```

**运行暂停等待批准。** `validate` 立即运行，然后 `build`
 作业在 `PublishDesktop` 环境中等待，直到所需的审阅者批准 
 — 运行位于 `waiting`，这是预期的，而不是挂起。在 
 运行的网络 UI 中批准它（“审核部署”），或者：

```sh
gh api repos/cline/cline/actions/runs/<run-id>/pending_deployments \
  --method POST -f state=approved -f comment="desktop vX.Y.Z" \
  -F 'environment_ids[]=19152605990'   # PublishDesktop
```

在此之前，`validate` 后不会运行任何内容，并且签名密钥不可读。

该工作流程构建一个通用的 macOS 捆绑包（`tauri build --target universal-apple-darwin` lipos aarch64 + x86_64 Rust 二进制文件；Bun sidecar 由 `build-sidecar-bin.ts` lipo'd；beta 添加 `tauri.beta.conf.json`覆盖），验证捆绑包中的每个 Mach-O 都携带两个切片，并且编译后的二进制文件是否准确嵌入了自己频道的提要 URL，使用开发人员 ID 证书进行签名，使用 App Store Connect API 密钥进行公证，并使用 Tauri 更新程序密钥对更新程序工件进行签名。同时，`build-windows` 在 Windows 运行程序上构建 x64 NSIS 安装程序，通过 Azure 可信签名（Tauri `signCommand` -> `scripts/tauri-sign-windows.ps1`）对每个二进制文件进行 Authenticode 签名，运行相同的馈送端点和遥测护栏，并使用以下命令验证附带的安装程序`Get-AuthenticodeSignature`。然后，发布作业创建 GitHub 版本（测试版预发布），刷新频道的源（`desktop-latest/latest.json` 或 `desktop-beta/latest.json`），并发布到 Slack。公证通常会增加 2–10 分钟。

如果工作流程因缺少凭据而失败，请参阅下面的“发布机密（一次性设置）”。

9. 运行成功后验证更新源。

```sh
curl -sL https://github.com/cline/cline/releases/download/desktop-latest/latest.json | head -30   # stable
curl -sL https://github.com/cline/cline/releases/download/desktop-beta/latest.json | head -30    # beta
```

`version`字段必须是新版本； `darwin-aarch64` 和 `darwin-x86_64` 条目必须指向发布标签下的同一个新的通用 `.app.tar.gz` 资产（胖二进制文件的每个切片在运行时请求自己的拱密钥，因此两个密钥都服务于一个工件），`windows-x86_64` 条目必须指向新的 `*_x64-setup.exe` 资产，并且`linux-x86_64-deb` / `linux-x86_64-rpm` 条目位于新的 `*_amd64.deb` / `*_x86_64.rpm` 资产中。该渠道上安装的应用程序（包括旧的每个架构安装）将在下次启动时或在 2 小时内获取更新。

在 **beta** 发布后，还要确认稳定源未受影响：`desktop-latest/latest.json` 必须仍提供以前的稳定版本。 （工作流程可以保护这种故障关闭，但验证起来很便宜，错过的话会带来灾难性的后果——更新器比较器是一个简单的 semver“更新于”，因此 `desktop-latest` 上的 Beta 清单会自动将每个稳定安装更新到 Beta 上。）

10. 最终回应。

报告：通道、版本、标签、更新的变更日志、提交哈希、推送的内容、工作流程 URL 以及提要验证结果。

## 每晚构建

每晚是 ** 一次性测试版本 **，而不是发布：没有版本提升，没有 
 变更日志，没有提交，没有 GitHub 发布，并且自动更新提要都没有 
 触及（`release` 作业被门控） `if: channel != 'nightly'`，以及夜间
Tauri 覆盖层提供空的更新程序端点）。它的存在是为了向某人提供一个已签名、
公证的安装程序，该安装程序目前在 `main` 上。它
在存储库中留下的唯一痕迹是内置
提交上的轻量级`desktop-nightly-<stamp>`标签，这是下一个夜间公告的比较对象。

工作流程的不同之处：

- 检查 `github.sha`（即调度时 `main` 的 HEAD）而不是标签。
在此通道上传递 `git_tag` 是一个验证错误。
- 仅在构建作业中标记版本本身，如
  `<package.json base version>-nightly.<utcYYYYMMDDHHMMSS>.<run id>.<run attempt>`.
  没有任何承诺，所以基地保持在最后一个马厩撞到的地方——
  因此每晚都可以 *较老的* 比相同基地的发布稳定
  塞姆弗。无害，因为夜间睡眠永远不会到达饲料。
- 构建为 **“Cline Nightly”**，标识符为 `bot.cline.app.nightly`
(`src-tauri/tauri.nightly.conf.json`)，因此它与稳定版和测试版一起安装。
- 仍然 signs/notarizes 具有真实凭证，因此 ** 仍然需要
PublishDesktop 批准** — 同样的等待，同样的审阅者。
- 结束于工件：`desktop-universal` (macOS)、`desktop-windows-x64` 和 `desktop-linux-x64`，
30 天保留，在运行页面上。
- 然后 `announce-nightly` 作业标记提交 `desktop-nightly-<utcYYYYMMDDHHMMSS>`
（版本中的标记）并发布到 Slack 发布通道：
 自最近的 `desktop-v*` 或 `desktop-nightly-*` 标签（之前的 
 每晚或最后一个版本，以较接近者为准）开始提交，范围为 
 `apps/examples/desktop-app`， `sdk/packages`，以及发布工作流程，带有 
 PR 链接和比较链接。 15 行的列表上限保持在 Slack 的 
 3000 字符块限制之下。切勿重命名 `desktop-nightly-*` 标签； 
 稳定版本比较 (`--exclude 'desktop-v*-*'`, `--match 'desktop-v*'`)
 通过前缀忽略它们。

步骤：确认`main`为当前（`git fetch origin main && git status -sb`），然后

```sh
gh workflow run desktop-publish.yml --ref main -f channel=nightly -f confirm_publish=publish
gh run list --workflow=desktop-publish.yml --limit=1 --json url,status,databaseId --jq '.[0]'
```

然后让用户批准 PublishDesktop 门（切勿通过 `gh api` 批准）、
，并在运行完成时报告工件名称和标记版本：

```sh
gh run view <run-id> --json status,conclusion,jobs --jq '{status,conclusion,jobs:[.jobs[]|{name,conclusion}]}'
gh api repos/cline/cline/actions/runs/<run-id>/artifacts --jq '.artifacts[].name'
```

不适用提要验证步骤 - 但如果您想要放心，请确认 
`desktop-latest/latest.json` 仍然提供以前的稳定版本。


## 发布机密（一次性设置）

这些文件存在于 **`PublishDesktop` 环境 ** 中，而不是存储库级别，因此 
 只有 `build` 作业才能读取它们，并且只有在获得批准后才能读取它们。在
设置→环境→PublishDesktop→环境秘密下设置它们。环境 
 还限制部署到 `main` 并且需要审核者。

添加其中之一作为 * 存储库 * 秘密是常见的错误。构建 
 仍然会成功 - 环境门控作业也可以解析存储库机密，
 环境值只是优先 - 因此凭证将位于 
 存储库范围内，而一切看起来都很好。因此，如果其中任何 
 在没有环境的作业中解析，`validate` 运行就会失败。如果您点击该按钮，请删除
存储库级副本而不是复制它。

如果到处都缺少秘密，则预检 `build` 运行命名失败
缺失的条目。 Apple 值来自同一个 Apple 开发者帐户
用于手动签名（请参阅应用程序 README's "macOS 签字及公证”
有关如何获取它们的部分）：

|秘密|价值|
| --- | --- |
| `APPLE_CERTIFICATE` |从钥匙串访问导出为 `.p12` 的 ** 开发人员 ID 应用程序 ** 身份的 Base64（必须包含私钥）：`base64 -i certificate.p12 \| pbcopy`|
| `APPLE_CERTIFICATE_PASSWORD` |导出`.p12`时选择的密码|
| `APPLE_SIGNING_IDENTITY` |`Developer ID Application: <Team Name> (<TEAMID>)` — 来自 `security find-identity -v -p codesigning`|
| `APPLE_API_KEY` |App Store Connect API **密钥 ID**（公证）|
| `APPLE_API_KEY_CONTENT` |`AuthKey_<KEYID>.p8` 文件的内容|
| `APPLE_API_ISSUER` |App Store Connect **发行者 ID**（来自用户和访问 → 集成的 UUID）|
| `TAURI_SIGNING_PRIVATE_KEY` |Tauri 更新程序私钥 (`tauri signer generate`) 的内容。如果此密钥丢失，发布的应用程序将无法再验证更新 - 保护它。|
| `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` |该密钥的密码|

Slack + 遥测机密（`SLACK_RELEASE_BOT_TOKEN`、`TELEMETRY_SERVICE_API_KEY`、
`ERROR_SERVICE_API_KEY`、OTEL 设置）与 CLI、SDK 和扩展 
 发布工作流共享并已配置。 **不要将它们移动到
`PublishDesktop`** - 将它们范围限定到此环境会清空它们在每个其他
发布工作流程中，静默，除了缺少遥测和失败的
Slack帖子之外没有任何错误。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Desktop App Release

Use this skill when the user asks to release the desktop app, publish the Cline desktop app, cut a desktop beta, cut a desktop nightly, bump the desktop version, create a `desktop-vX.Y.Z` (or `desktop-vX.Y.Z-beta.N`) tag, or trigger the desktop publish workflow.

> Working directory: run every command below from the repository root.

Desktop releases ship three platforms, built entirely in GitHub Actions — there is no local publish path. macOS: a single signed + notarized universal DMG that runs natively on both Apple Silicon and Intel. Linux: x64 `.deb` and `.rpm` packages (`<Product>_<version>_amd64.deb`, `<Product>_<version>_x86_64.rpm`) built on Ubuntu 22.04 in the `build-linux` job, deb + rpm only (no AppImage: linuxdeploy cannot process the Bun-compiled sidecar). Windows: an Authenticode-signed NSIS installer (`<Product>_<version>_x64-setup.exe`), signed via Azure Trusted Signing in the `build-windows` job (jsign through Tauri's `signCommand`, see `apps/examples/desktop-app/scripts/tauri-sign-windows.ps1`; requires the repo-level `AZURE_*` secrets including `AZURE_TRUSTED_SIGNING_CERTIFICATE_PROFILE_DESKTOP`, plus a `PublishDesktop`-environment federated credential on the `cline-cli-signing` Entra app). Installed apps discover new releases automatically through the Tauri updater, so publishing a release is what ships the update to every existing user **on that channel**.

## Release contract

- Three channels, one workflow (`channel` input on `desktop-publish.yml`):
  - **stable** — tag `desktop-vX.Y.Z` (no suffix; the workflow rejects prerelease suffixes on this channel), cut from `main`, feeds the rolling `desktop-latest` release, ships as "Cline".
  - **beta** — tag `desktop-vX.Y.Z-beta.N`, cut from `desktop-experimental`, feeds the rolling `desktop-beta` release, ships as "Cline Beta" (separate bundle identifier `bot.cline.app.beta`; installs side by side with stable). Built with the extra `src-tauri/tauri.beta.conf.json` overlay. Process background: `apps/examples/desktop-app/EXPERIMENTAL.md`.
  - **nightly** — no release tag input, no release, no feed. Builds `main`'s HEAD, ships as "Cline Nightly" (identifier `bot.cline.app.nightly`, empty updater endpoints, `src-tauri/tauri.nightly.conf.json` overlay), uploads the signed installers as Actions artifacts only, then tags the commit `desktop-nightly-<utc stamp>` and announces the changes since the previous nightly or release to Slack. See "Nightly builds" below.
- Version sources (must match each other and the tag): `apps/examples/desktop-app/package.json` and `apps/examples/desktop-app/src-tauri/tauri.conf.json`. (`src-tauri/Cargo.toml` has its own version but `tauri.conf.json` overrides it; no need to touch it.)
- Beta versions are prereleases of the **next** stable: stable `0.0.13` → betas `0.0.14-beta.1`, `-beta.2`, … Once a stable ≥ the beta base ships, the next beta bumps its base (`0.0.15-beta.1`).
- Release prep includes approved release notes, the version bumps, and an `apps/examples/desktop-app/CHANGELOG.md` update — committed on `main` for stable, on `desktop-experimental` for beta.
- Publish path: `.github/workflows/desktop-publish.yml` (workflow_dispatch; for stable and beta it requires the tag to exist, point at the checked-out commit, and be reachable from the channel's branch — `origin/main` for stable, `origin/desktop-experimental` for beta. Nightly takes no tag and validation rejects one).
- **Every channel dispatches from `main`.** This is a security invariant, not a convenience: the run executes `main`'s workflow copy and only the checkout points at the tag, so the signing-secret gates (the `github.ref == main` check and the PublishDesktop environment's main-only deployment-branch policy) hold for beta too. Never add `desktop-experimental` to the PublishDesktop deployment-branch policy.
- The workflow creates the tag's GitHub release (universal DMG + macOS updater artifact + Windows NSIS installer and Linux deb/rpm packages, each with its updater signature, + `latest.json`; marked prerelease for beta) and refreshes the channel's rolling feed release, which is the static auto-update feed every installed app on that channel polls. Never delete the `desktop-latest` or `desktop-beta` release or tag.
- The changelog's `## <version>` section (exact-match, not "topmost") is extracted verbatim into the GitHub release body, the Slack announcement, and the updater manifest notes.
- Always ask before pushing commits or tags.

## Workflow

0. Ask which channel this release is for — **stable, beta, or nightly** — if the user has not said. Everything below branches on it; never guess.

   For **nightly**, skip steps 1–7 entirely and jump to "Nightly builds".

1. Gather context.

```sh
git status --short --branch
git fetch origin --tags
git tag --list 'desktop-v*' --sort=-v:refname | head -10
node -p "require('./apps/examples/desktop-app/package.json').version"
node -p "require('./apps/examples/desktop-app/src-tauri/tauri.conf.json').version"
```

If there is no `desktop-v*` tag yet, this is the first release; use the desktop app's first commit as the baseline and say the baseline is inferred.

For a **beta** release, work on `desktop-experimental` (check out `origin/desktop-experimental`; merge `origin/main` into it first if it is behind — see EXPERIMENTAL.md for the conflict policy) and read the version files from that branch. The last-tag baseline is the newest `desktop-v*` tag of either channel that is an ancestor of the branch.

2. Collect release commits.

```sh
# stable (on main):
git log <last-desktop-tag>..HEAD --oneline --no-merges -- apps/examples/desktop-app sdk/packages .github/workflows/desktop-publish.yml
# beta (on desktop-experimental):
git log <last-desktop-tag>..origin/desktop-experimental --oneline --no-merges -- apps/examples/desktop-app sdk/packages .github/workflows/desktop-publish.yml
```

The sidecar bundles `@cline/core` and friends from the monorepo, so SDK changes ship inside the desktop app too. Fold user-visible SDK changes (providers, models, behavior fixes) into the notes; skip purely internal ones.

3. Draft user-facing release notes.

Flat bullet list, user-facing language.

**Scope notes to the desktop app.** Shared-code commits in the range are often for the CLI or the extension. Keep a bullet only if the change reaches `apps/examples/desktop-app` (CLI subcommands, TUI, and yolo prompt rules don't). Skip items already in the last desktop section, grep any quoted string, and leave out anything uncertain.

Present the draft and wait for approval before editing files.

4. Decide the version bump.

Stable: ask whether this is patch, minor, major, or an explicit version. Do not guess if the user has not made it clear.

Beta: apply the versioning rule — base = next stable version, increment `N` (`0.0.14-beta.1` → `0.0.14-beta.2`; after stable `0.0.14` ships, next is `0.0.15-beta.1`). Confirm the computed version with the user.

5. Update release files (on `main` for stable, on `desktop-experimental` for beta).

- `apps/examples/desktop-app/package.json` → new version
- `apps/examples/desktop-app/src-tauri/tauri.conf.json` → same version
- Prepend `## X.Y.Z` (no date; `## X.Y.Z-beta.N` for beta) to `apps/examples/desktop-app/CHANGELOG.md` with the approved notes.

6. Verify before committing.

```sh
bun -F @cline/code typecheck
bun test apps/examples/desktop-app/scripts/generate-update-manifest.test.ts
```

The full desktop bundle can only be built on macOS; the workflow's build job is the real verification. For extra local confidence on a Mac checkout, `bun run package:desktop:mac --allow-unsigned-mac` from the app directory.

7. Commit release changes.

```sh
git add apps/examples/desktop-app/package.json apps/examples/desktop-app/src-tauri/tauri.conf.json apps/examples/desktop-app/CHANGELOG.md
git commit -m "chore(desktop): release vX.Y.Z"
```

Ask before pushing the release commit, then before creating and pushing the tag:

```sh
git push origin HEAD
git tag -a desktop-vX.Y.Z -m "Desktop vX.Y.Z"       # beta: desktop-vX.Y.Z-beta.N / "Desktop vX.Y.Z-beta.N"
git push origin refs/tags/desktop-vX.Y.Z
```

8. Publish.

The release commit must be on the channel's branch (`main` for stable, `desktop-experimental` for beta) and the tag pushed first. Dispatch from `main` for **every** channel (see the release contract for why).

```sh
# stable:
gh workflow run desktop-publish.yml --ref main -f git_tag=desktop-vX.Y.Z -f channel=stable -f confirm_publish=publish
# beta:
gh workflow run desktop-publish.yml --ref main -f git_tag=desktop-vX.Y.Z-beta.N -f channel=beta -f confirm_publish=publish
# nightly (no tag — see "Nightly builds"):
gh workflow run desktop-publish.yml --ref main -f channel=nightly -f confirm_publish=publish

gh run list --workflow=desktop-publish.yml --limit=1 --json url,status,conclusion,createdAt --jq '.[0]'
```

**The run pauses for approval.** `validate` runs immediately, then the `build`
job waits on the `PublishDesktop` environment until a required reviewer approves
it — the run sits in `waiting`, which is expected, not a hang. Approve it in the
run's web UI ("Review deployments"), or:

```sh
gh api repos/cline/cline/actions/runs/<run-id>/pending_deployments \
  --method POST -f state=approved -f comment="desktop vX.Y.Z" \
  -F 'environment_ids[]=19152605990'   # PublishDesktop
```

Nothing after `validate` runs — and no signing key is readable — until then.

The workflow builds one universal macOS bundle (`tauri build --target universal-apple-darwin` lipos the aarch64 + x86_64 Rust binaries; the Bun sidecar is lipo'd by `build-sidecar-bin.ts`; beta adds the `tauri.beta.conf.json` overlay), verifies every Mach-O in the bundle carries both slices and that the compiled binary embeds exactly its own channel's feed URL, signs with the Developer ID certificate, notarizes with the App Store Connect API key, and signs the updater artifact with the Tauri updater key. In parallel, `build-windows` builds the x64 NSIS installer on a Windows runner, Authenticode-signs every binary via Azure Trusted Signing (Tauri `signCommand` -> `scripts/tauri-sign-windows.ps1`), runs the same feed-endpoint and telemetry guardrails, and verifies the shipped installer with `Get-AuthenticodeSignature`. The release job then creates the GitHub release (prerelease for beta), refreshes the channel's feed (`desktop-latest/latest.json` or `desktop-beta/latest.json`), and posts to Slack. Notarization typically adds 2–10 minutes.

If the workflow fails on missing credentials, see "Publish secrets (one-time setup)" below.

9. Verify the update feed after the run succeeds.

```sh
curl -sL https://github.com/cline/cline/releases/download/desktop-latest/latest.json | head -30   # stable
curl -sL https://github.com/cline/cline/releases/download/desktop-beta/latest.json | head -30    # beta
```

The `version` field must be the new release; both `darwin-aarch64` and `darwin-x86_64` entries must point at the same new universal `.app.tar.gz` asset under the release tag (each slice of the fat binary requests its own arch key at runtime, so both keys serve the one artifact), the `windows-x86_64` entry must point at the new `*_x64-setup.exe` asset, and the `linux-x86_64-deb` / `linux-x86_64-rpm` entries at the new `*_amd64.deb` / `*_x86_64.rpm` assets. Installed apps on that channel — including older per-arch installs — pick the update up on next launch or within 2 hours.

After a **beta** publish, also confirm the stable feed was not touched: `desktop-latest/latest.json` must still serve the previous stable version. (The workflow guards this fail-closed, but it is cheap to verify and catastrophic to miss — the updater comparator is a plain semver "newer than", so a beta manifest on `desktop-latest` would auto-update every stable install onto the beta.)

10. Final response.

Report: channel, version, tag, changelog updated, commit hash, what was pushed, workflow URL, and the feed verification result.

## Nightly builds

A nightly is a **throwaway test build**, not a release: no version bump, no
changelog, no commit, no GitHub release, and neither auto-update feed is
touched (the `release` job is gated `if: channel != 'nightly'`, and the nightly
Tauri overlay ships empty updater endpoints). It exists to hand someone a signed,
notarized installer of whatever is on `main` right now. The only trace it
leaves in the repo is a lightweight `desktop-nightly-<stamp>` tag on the built
commit, which is what the next nightly's announcement compares against.

What the workflow does differently:

- Checks out `github.sha` (i.e. `main`'s HEAD at dispatch) instead of a tag.
  Passing `git_tag` on this channel is a validation error.
- Stamps the version itself, in the build jobs only, as
  `<package.json base version>-nightly.<utcYYYYMMDDHHMMSS>.<run id>.<run attempt>`.
  Nothing is committed, so the base stays whatever the last stable bumped it to —
  a nightly therefore sorts *older* than the released stable of the same base by
  semver. Harmless, because nightlies never reach a feed.
- Builds as **"Cline Nightly"** with identifier `bot.cline.app.nightly`
  (`src-tauri/tauri.nightly.conf.json`), so it installs alongside stable and beta.
- Still signs/notarizes with the real credentials, so it **still requires the
  PublishDesktop approval** — same wait, same reviewer.
- Ends at artifacts: `desktop-universal` (macOS), `desktop-windows-x64`, and `desktop-linux-x64`,
  30-day retention, on the run page.
- Then the `announce-nightly` job tags the commit `desktop-nightly-<utcYYYYMMDDHHMMSS>`
  (the stamp from the version) and posts to the Slack release channel: the
  commits since the nearest `desktop-v*` or `desktop-nightly-*` tag (previous
  nightly or last release, whichever is closer), scoped to
  `apps/examples/desktop-app`, `sdk/packages`, and the publish workflow, with
  PR links and a compare link. The list caps at 15 lines to stay under Slack's
  3000-character block limit. Never rename `desktop-nightly-*` tags; the
  stable release compare (`--exclude 'desktop-v*-*'`, `--match 'desktop-v*'`)
  ignores them by prefix.

Steps: confirm `main` is current (`git fetch origin main && git status -sb`), then

```sh
gh workflow run desktop-publish.yml --ref main -f channel=nightly -f confirm_publish=publish
gh run list --workflow=desktop-publish.yml --limit=1 --json url,status,databaseId --jq '.[0]'
```

Then have the user approve the PublishDesktop gate (never approve it via `gh api`),
and when the run finishes report the artifact names and the stamped version:

```sh
gh run view <run-id> --json status,conclusion,jobs --jq '{status,conclusion,jobs:[.jobs[]|{name,conclusion}]}'
gh api repos/cline/cline/actions/runs/<run-id>/artifacts --jq '.artifacts[].name'
```

No feed verification step applies — but if you want the reassurance, confirm
`desktop-latest/latest.json` still serves the previous stable version.


## Publish secrets (one-time setup)

These live on the **`PublishDesktop` environment**, not at repository level, so
only the `build` job can read them and only after an approval. Set them under
Settings → Environments → PublishDesktop → Environment secrets. The environment
also restricts deployments to `main` and requires a reviewer.

Adding one of these as a *repository* secret is the common mistake. The build
would still succeed — an environment-gated job resolves repository secrets too,
with environment values simply taking precedence — so the credential would sit
repo-wide while everything looked fine. `validate` therefore fails the run if any
of them resolves in a job with no environment. If you hit that, delete the
repository-level copy rather than duplicating it.

If a secret is missing everywhere, the preflight in `build` fails the run naming
the missing entries. The Apple values come from the same Apple Developer account
used for manual signing (see the app README's "macOS signing & notarization"
section for how to obtain them):

| Secret | Value |
| --- | --- |
| `APPLE_CERTIFICATE` | Base64 of the **Developer ID Application** identity exported from Keychain Access as `.p12` (must include the private key): `base64 -i certificate.p12 \| pbcopy` |
| `APPLE_CERTIFICATE_PASSWORD` | The password chosen when exporting the `.p12` |
| `APPLE_SIGNING_IDENTITY` | `Developer ID Application: <Team Name> (<TEAMID>)` — from `security find-identity -v -p codesigning` |
| `APPLE_API_KEY` | App Store Connect API **Key ID** (notarization) |
| `APPLE_API_KEY_CONTENT` | Contents of the `AuthKey_<KEYID>.p8` file |
| `APPLE_API_ISSUER` | App Store Connect **Issuer ID** (UUID from Users and Access → Integrations) |
| `TAURI_SIGNING_PRIVATE_KEY` | Contents of the Tauri updater private key (`tauri signer generate`). If this key is ever lost, shipped apps can no longer verify updates — guard it. |
| `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` | Password for that key |

The Slack + telemetry secrets (`SLACK_RELEASE_BOT_TOKEN`, `TELEMETRY_SERVICE_API_KEY`,
`ERROR_SERVICE_API_KEY`, OTEL settings) are shared with the CLI, SDK, and extension
publish workflows and already configured. **Do not move these into
`PublishDesktop`** — scoping them to this environment empties them in every other
publish workflow, silently, with no error beyond missing telemetry and a failed
Slack post.


</details>
