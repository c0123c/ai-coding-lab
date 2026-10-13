# Codex 官方仓库快速开始

提供 CLI 安装运行、ChatGPT 账号接入、IDE 扩展和文档入口，帮助定位基础使用方式。

- 来源：OpenAI／Codex
- 资料类型：存档文档
- 存档版本：`5ef96ab2785f`。
- 文档时间：源码提交（存档版）：[2026-07-23](<https://github.com/openai/codex/commit/39a2438d16514d0d6f88105d17b0f747994af487>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/codex/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Codex/Codex 官方仓库快速开始.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

<p align="center"><strong>Codex CLI</strong> 是来自 OpenAI 的编码 agent，在您的计算机上本地运行。
<p align="center">
  

![Codex CLI 启动画面](<https://github.com/openai/codex/blob/main/.github/codex-cli-splash.png>)

</p>
</br>
如果您想要在代码编辑器中使用Codex（VS Code、Cursor、Windsurf），<a href="https://developers.openai.com/codex/ide">安装在您的IDE中。</a>
</br>如果您想要桌面应用程序体验，运行<code>codex app</code>或访问<a href="https://chatgpt.com/codex?app-landing-page=true">Codex应用程序页面</a>。
</br>如果您正在寻找OpenAI的<em>基于云的agent</em>， <strong>Codex 网页</strong>，前往<a href="https://chatgpt.com/codex">chatgpt.com/codex</a>.</p>

---

## 快速入门

### 安装并运行Codex CLI

在 Mac 或 Linux 上运行以下命令来安装 Codex CLI：

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

在 Windows 上运行以下命令来安装 Codex CLI：

```shell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

独立安装程序默认从 `https://releases.openai.com/codex` 下载，如果元数据或资产下载不可用，则回退到 GitHub 版本。要强制释放 GitHub，请将 `CODEX_INSTALLER_USE_RELEASES_OPENAI_COM` 设置为 `false`（也接受 `0` 和 `no`）：

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | CODEX_INSTALLER_USE_RELEASES_OPENAI_COM=false sh
```

```powershell
$env:CODEX_INSTALLER_USE_RELEASES_OPENAI_COM='false'; irm https://chatgpt.com/codex/install.ps1 | iex
```

Codex CLI 也可以通过以下包管理器安装：

```shell
# Install using npm
npm install -g @openai/codex
```

```shell
# Install using Homebrew
brew install --cask codex
```

然后只需运行 `codex` 即可开始。



<summary>您还可以访问<a href="https://github.com/openai/codex/releases/latest">最新GitHub版本</a>并下载适合您平台的二进制文件。</summary>

每个 GitHub 版本都包含许多可执行文件，但实际上，您可能需要其中之一：

- macOS
  - 苹果 Silicon/arm64: `codex-aarch64-apple-darwin.tar.gz`
  - x86_64（较旧的 Mac 硬件）：`codex-x86_64-apple-darwin.tar.gz`
- Linux
  - x86_64: `codex-x86_64-unknown-linux-musl.tar.gz`
  - ARM64：`codex-aarch64-unknown-linux-musl.tar.gz`

每个存档都包含一个条目，其名称中包含平台（例如，`codex-x86_64-unknown-linux-musl`），因此您可能希望在解压后将其重命名为 `codex`。




### 将 Codex 与您的 ChatGPT 计划一起使用

运行`codex`并选择**使用ChatGPT**登录。我们建议登录您的 ChatGPT 帐户，以将 Codex 作为您的 Plus、Pro、Business、Edu 或 Enterprise 计划的一部分。 [详细了解您的 ChatGPT 计划 ](<https://help.openai.com/en/articles/11369540-codex-in-chatgpt>) 中包含的内容。

您还可以将Codex与API键一起使用，但这需要[额外设置](<https://developers.openai.com/codex/auth#sign-in-with-an-api-key>)。

## 文档

- [**Codex 文档**](<https://developers.openai.com/codex>)
- [**贡献**](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/docs/contributing.md>)
- [**安装和搭建**](<从源码安装与构建 Codex.md>)
- [**开源基金**](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/docs/open-source-fund.md>)

此存储库根据 [Apache-2.0 许可证 ](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/LICENSE>) 获得许可。


## 原文对照

<details>
<summary>展开完整原文</summary>

<p align="center"><strong>Codex CLI</strong> is a coding agent from OpenAI that runs locally on your computer.
<p align="center">
  

![Codex CLI splash](<https://github.com/openai/codex/blob/main/.github/codex-cli-splash.png>)

</p>
</br>
If you want Codex in your code editor (VS Code, Cursor, Windsurf), <a href="https://developers.openai.com/codex/ide">install in your IDE.</a>
</br>If you want the desktop app experience, run <code>codex app</code> or visit <a href="https://chatgpt.com/codex?app-landing-page=true">the Codex App page</a>.
</br>If you are looking for the <em>cloud-based agent</em> from OpenAI, <strong>Codex Web</strong>, go to <a href="https://chatgpt.com/codex">chatgpt.com/codex</a>.</p>

---

## Quickstart

### Installing and running Codex CLI

Run the following on Mac or Linux to install Codex CLI:

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

Run the following on Windows to install Codex CLI:

```shell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

The standalone installers download from `https://releases.openai.com/codex` by default and fall back to GitHub Releases if a metadata or asset download is unavailable. To force GitHub Releases, set `CODEX_INSTALLER_USE_RELEASES_OPENAI_COM` to `false` (`0` and `no` are also accepted):

```shell
curl -fsSL https://chatgpt.com/codex/install.sh | CODEX_INSTALLER_USE_RELEASES_OPENAI_COM=false sh
```

```powershell
$env:CODEX_INSTALLER_USE_RELEASES_OPENAI_COM='false'; irm https://chatgpt.com/codex/install.ps1 | iex
```

Codex CLI can also be installed via the following package managers:

```shell
# Install using npm
npm install -g @openai/codex
```

```shell
# Install using Homebrew
brew install --cask codex
```

Then simply run `codex` to get started.



<summary>You can also go to the <a href="https://github.com/openai/codex/releases/latest">latest GitHub Release</a> and download the appropriate binary for your platform.</summary>

Each GitHub Release contains many executables, but in practice, you likely want one of these:

- macOS
  - Apple Silicon/arm64: `codex-aarch64-apple-darwin.tar.gz`
  - x86_64 (older Mac hardware): `codex-x86_64-apple-darwin.tar.gz`
- Linux
  - x86_64: `codex-x86_64-unknown-linux-musl.tar.gz`
  - arm64: `codex-aarch64-unknown-linux-musl.tar.gz`

Each archive contains a single entry with the platform baked into the name (e.g., `codex-x86_64-unknown-linux-musl`), so you likely want to rename it to `codex` after extracting it.




### Using Codex with your ChatGPT plan

Run `codex` and select **Sign in with ChatGPT**. We recommend signing into your ChatGPT account to use Codex as part of your Plus, Pro, Business, Edu, or Enterprise plan. [Learn more about what's included in your ChatGPT plan](<https://help.openai.com/en/articles/11369540-codex-in-chatgpt>).

You can also use Codex with an API key, but this requires [additional setup](<https://developers.openai.com/codex/auth#sign-in-with-an-api-key>).

## Docs

- [**Codex Documentation**](<https://developers.openai.com/codex>)
- [**Contributing**](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/docs/contributing.md>)
- [**Installing & building**](<从源码安装与构建 Codex.md>)
- [**Open source fund**](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/docs/open-source-fund.md>)

This repository is licensed under the [Apache-2.0 License](<https://github.com/openai/codex/blob/5ef96ab2785f3ecddf279639eff7b1b42c906fa4/LICENSE>).


</details>
