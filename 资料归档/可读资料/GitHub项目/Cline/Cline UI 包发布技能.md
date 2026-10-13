# Cline UI 包发布技能

说明独立 UI 包的版本调整、发布前验证、npm 发布工作流和首次可信发布配置；属于仓库维护资料。

- 来源：Cline
- 资料类型：存档文档
- 存档版本：`f87705844954`。
- 文档时间：源码提交（存档版）：[2026-07-18](<https://github.com/cline/cline/commit/7274d8badcf77b19ddb4e4d4fa9fe5a9dff68e26>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/cline/cline/blob/main/.cline/skills/publish-ui/SKILL.md>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Cline/Cline UI 包发布技能.md>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: publish-ui
description: Prepare, validate, and publish standalone @cline/ui npm releases. Use when bumping the UI package version, publishing latest or next through ui-publish.yml, checking UI release readiness, or completing the one-time npm trusted-publishing bootstrap.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 发布UI

独立于 Cline SDK 运行时包发布 `@cline/ui`。

## 发布合约

- 版本来源：`sdk/packages/ui/package.json`。
- 工作流程：`.github/workflows/ui-publish.yml`。
- 该软件包保留 `internal: true` 只是为了远离 SDK 的共享
version/publish 脚本。它仍然是公共的 npm 包，因为 
 `private: false` 和 `publishConfig.access: public` 控制 npm 发布。
- `latest`为制作频道。 `next` 是一个选择加入的预览频道。
- 使用预发布版本，例如将 `0.2.0-next.0` 替换为 `next`；不要发布
预览标签下的 `latest` 版本，因为 npm 版本 
 无法重新发布。
- 没有 UI Git 标签、GitHub 版本、时间表或 Slack 公告。
- 工作流程仅通过手动调度运行。每次发布尝试都会运行 UI
发布前进行质量检查，并需要 
 `main` 中的 `confirm_publish=publish`。
- 发布作业和 npm 信任关系使用受保护的 `Publish`
环境。
- 每个 npm 出版物都需要新的 semver 版本； npm 版本是不可变的。
- 在推送提交、触发发布工作流程、更改之前始终询问
npm 信任设置，或运行本地发布命令。

## 正常发布

1. 检查分支、当前版本、npm 状态和 UI 更改。

```sh
git status --short --branch
node -p "require('./sdk/packages/ui/package.json').version"
npm view @cline/ui dist-tags versions --json
git log --oneline --no-merges -- \
  sdk/packages/ui apps/examples/desktop-app/webview/components/views/chat \
  .github/workflows/ui-publish.yml
```

2. 一起询问npm频道和版本。对于`latest`，请求补丁，
次要版本、主要版本或显式版本。对于 `next`，需要显式的 
 预发布版本，例如 `0.2.0-next.0`。不要猜测。仅更新
 `sdk/packages/ui/package.json` 及其在`bun.lock` 中的工作区版本。不要
运行SDK版本命令。

3. 验证候选版本。

```sh
bun install --filter @cline/ui --filter @cline/code --frozen-lockfile
bun -F @cline/ui typecheck
bun -F @cline/ui test
bun -F @cline/ui test:package
bun -F @cline/ui build-storybook
bun -F @cline/code test:chat-ui
```

打包测试使用 Bun/React 19 和 
npm/Node/React 18.
 在导出的文件集更改时检查 `bun pm pack --dry-run`。

4. 将版本更新与功能工作分开提交。推动前先询问。

```sh
git add sdk/packages/ui/package.json bun.lock
git commit -m "chore(ui): release vX.Y.Z"
git push origin HEAD
```

5. 发布提交达到`main`后，重新声明所选的npm标签并询问
以获得明确的发布批准。然后触发并观看独立的
工作流程：

```sh
run_url=$(gh workflow run ui-publish.yml --ref main \
  -f npm_tag=latest \
  -f confirm_publish=publish)
test -n "$run_url"
run_id=${run_url##*/}
gh run watch "$run_id" --exit-status
```

仅将 `npm_tag=next` 用于有意预览。在
工作流程成功并且npm在所选标签下显示确切版本之前，请勿报告成功。

```sh
npm view @cline/ui dist-tags versions --json
```

## 一次性 npm 引导程序

仅当 `npm view @cline/ui` 返回 `E404` 时才使用此选项。 npm 要求 
 软件包存在，然后才能配置其 GitHub 可信发布者。

1. 将包和`ui-publish.yml`合并到`main`。从干净开始，
已查看 `main` 结账。验证身份验证、帐户 2FA 以及对 `@cline` npm 组织的写入 
 访问权限。步骤 4
 中的 `npm trust` 命令需要 npm CLI 11.15 或更新版本；自动可信发布工作流程 
 本身强制执行 npm 11.5.1 或更新版本。

```sh
npm --version
npm whoami
npm view @cline/ui version
```

如果 npm 早于 11.15，请在使用 
`npm install -g npm@^11.15.0` 升级之前询问。

2. 运行上面步骤 3 中的正常发布验证。然后构建、打包、测试，
并检查确切的初始 tarball。记录最终命令打印的绝对存档路径
。

```sh
bun -F @cline/ui build
pack_dir=$(mktemp -d)
(cd sdk/packages/ui && bun pm pack --ignore-scripts --destination "$pack_dir" --quiet)
tarball=$(find "$pack_dir" -maxdepth 1 -name '*.tgz' -print -quit)
test -n "$tarball"
bun sdk/packages/ui/scripts/smoke-package.ts "$tarball"
tar -tzf "$tarball"
printf 'Bootstrap archive: %s\n' "$tarball"
```

3. 请求明确批准，然后在下公开发布初始版本
   `latest`:

```sh
npm publish /absolute/path/from-step-2.tgz --access public --tag latest
```

4. 在将独立工作流程配置为可信工作流程之前单独询问
出版商：

```sh
npm trust github @cline/ui \
  --repo cline/cline \
  --file ui-publish.yml \
  --env Publish \
  --allow-publish
```

5. 验证包状态和信任。每个后续版本都使用该工作流程；
不要添加长期存在的 npm 令牌。

```sh
npm view @cline/ui dist-tags versions --json
npm trust list @cline/ui
```

## 最终报告

报告版本和 npm 标签、发布提交、是否推送了任何内容、
 工作流程 URL 或引导结果、npm 验证和 tests/builds 运行。如果 
 包仍然返回 `E404`，则说明仍然需要引导程序。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Publish UI

Release `@cline/ui` independently from the Cline SDK runtime packages.

## Release contract

- Version source: `sdk/packages/ui/package.json`.
- Workflow: `.github/workflows/ui-publish.yml`.
- The package keeps `internal: true` only to stay out of the SDK's shared
  version/publish scripts. It is still a public npm package because
  `private: false` and `publishConfig.access: public` control npm publication.
- `latest` is the production channel. `next` is an opt-in preview channel.
- Use prerelease versions such as `0.2.0-next.0` for `next`; do not publish a
  version intended for `latest` under the preview tag because npm versions
  cannot be republished.
- There is no UI Git tag, GitHub release, schedule, or Slack announcement.
- The workflow runs only by manual dispatch. Every release attempt runs the UI
  quality checks before publishing and requires `confirm_publish=publish` from
  `main`.
- The publish job and npm trust relationship use the protected `Publish`
  environment.
- Every npm publication needs a new semver version; npm versions are immutable.
- Always ask before pushing commits, triggering the publish workflow, changing
  npm trust settings, or running a local publish command.

## Normal release

1. Inspect the branch, current version, npm state, and UI changes.

```sh
git status --short --branch
node -p "require('./sdk/packages/ui/package.json').version"
npm view @cline/ui dist-tags versions --json
git log --oneline --no-merges -- \
  sdk/packages/ui apps/examples/desktop-app/webview/components/views/chat \
  .github/workflows/ui-publish.yml
```

2. Ask for the npm channel and version together. For `latest`, ask for patch,
   minor, major, or an explicit version. For `next`, require an explicit
   prerelease version such as `0.2.0-next.0`. Do not guess. Update only
   `sdk/packages/ui/package.json` and its workspace version in `bun.lock`. Do
   not run the SDK version command.

3. Validate the release candidate.

```sh
bun install --filter @cline/ui --filter @cline/code --frozen-lockfile
bun -F @cline/ui typecheck
bun -F @cline/ui test
bun -F @cline/ui test:package
bun -F @cline/ui build-storybook
bun -F @cline/code test:chat-ui
```

The packed-package test installs the tarball with Bun/React 19 and with
npm/Node/React 18.
Inspect `bun pm pack --dry-run` when the exported file set changed.

4. Commit the version bump separately from feature work. Ask before pushing.

```sh
git add sdk/packages/ui/package.json bun.lock
git commit -m "chore(ui): release vX.Y.Z"
git push origin HEAD
```

5. After the release commit reaches `main`, restate the selected npm tag and ask
   for explicit publish approval. Then trigger and watch the standalone
   workflow:

```sh
run_url=$(gh workflow run ui-publish.yml --ref main \
  -f npm_tag=latest \
  -f confirm_publish=publish)
test -n "$run_url"
run_id=${run_url##*/}
gh run watch "$run_id" --exit-status
```

Use `npm_tag=next` only for a deliberate preview. Do not report success until
the workflow succeeds and npm shows the exact version under the selected tag.

```sh
npm view @cline/ui dist-tags versions --json
```

## One-time npm bootstrap

Use this only while `npm view @cline/ui` returns `E404`. npm requires the
package to exist before its GitHub trusted publisher can be configured.

1. Merge the package and `ui-publish.yml` to `main`. Start from a clean,
   reviewed `main` checkout. Verify authentication, account 2FA, and write
   access to the `@cline` npm organization. The `npm trust` command in step 4
   requires npm CLI 11.15 or newer; the automated trusted-publishing workflow
   itself enforces npm 11.5.1 or newer.

```sh
npm --version
npm whoami
npm view @cline/ui version
```

If npm is older than 11.15, ask before upgrading with
`npm install -g npm@^11.15.0`.

2. Run the normal release validation in step 3 above. Then build, pack, test,
   and inspect the exact initial tarball. Record the absolute archive path
   printed by the final command.

```sh
bun -F @cline/ui build
pack_dir=$(mktemp -d)
(cd sdk/packages/ui && bun pm pack --ignore-scripts --destination "$pack_dir" --quiet)
tarball=$(find "$pack_dir" -maxdepth 1 -name '*.tgz' -print -quit)
test -n "$tarball"
bun sdk/packages/ui/scripts/smoke-package.ts "$tarball"
tar -tzf "$tarball"
printf 'Bootstrap archive: %s\n' "$tarball"
```

3. Ask for explicit approval, then publish the initial version publicly under
   `latest`:

```sh
npm publish /absolute/path/from-step-2.tgz --access public --tag latest
```

4. Ask separately before configuring the standalone workflow as the trusted
   publisher:

```sh
npm trust github @cline/ui \
  --repo cline/cline \
  --file ui-publish.yml \
  --env Publish \
  --allow-publish
```

5. Verify both package state and trust. Every later release uses the workflow;
   do not add a long-lived npm token.

```sh
npm view @cline/ui dist-tags versions --json
npm trust list @cline/ui
```

## Final report

Report the version and npm tag, release commit, whether anything was pushed,
workflow URL or bootstrap result, npm verification, and tests/builds run. If
the package still returns `E404`, state that bootstrap remains required.


</details>
