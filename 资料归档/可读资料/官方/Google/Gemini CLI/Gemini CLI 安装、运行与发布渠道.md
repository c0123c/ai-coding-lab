# Gemini CLI 安装、运行与发布渠道

产品安装指南。列出系统要求、npm、Homebrew、Anaconda、npx、源码和容器运行，区分 Stable、Preview、Nightly 发布渠道，帮助准备 CLI 开发环境。

- 来源：Google／Gemini CLI
- 资料类型：产品指南
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-05-14](<https://github.com/google-gemini/gemini-cli/commit/f6494f3862997aeab2dee157407dac0b52a01c17>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：安装包版本与发布渠道分别记录，正文源码提交日不是软件发布日期。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/get-started/installation.mdx>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/get-started/installation.mdx>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI安装、执行和发布

本文档概述了Gemini CLI的系统要求、
安装方法和版本类型。

## 推荐系统规格

- **操作系统：**
  - macOS 15+
  - Windows 11 24H2+
  - Ubuntu 20.04+
- **硬件：**
  - “休闲”使用：4GB+ RAM（简短会话、常见任务和编辑）
  - “Power”使用量：16GB+ RAM（长会话、大代码库、深层上下文）
- **运行时：** Node.js 20.0.0+
- **Shell：** Bash、Zsh 或 PowerShell
- **位置：**
[Gemini 代码辅助支持的位置](<https://developers.google.com/gemini-code-assist/resources/available-locations#americas>)
- **需要互联网连接**

## 安装Gemini CLI

我们建议大多数用户使用以下
安装方法之一来安装Gemini CLI。请注意，Gemini CLI 预安装在 
[**Cloud Shell**](<https://docs.cloud.google.com/shell/docs>) 和 
[**云工作站 **](<https://cloud.google.com/workstations>) 上。

#### npm

使用 npm 进行全局安装：

```bash
npm install -g @google/gemini-cli
```

#### 自制软件 (macOS/Linux)

使用 Homebrew 全局安装：

```bash
brew install gemini-cli
```

#### MacPorts (macOS)

使用 MacPorts 进行全局安装：

```bash
sudo port install gemini-cli
```

#### 蟒蛇

使用 Anaconda 安装（适用于受限环境）：

```bash
# Create and activate a new environment
conda create -y -n gemini_env -c conda-forge nodejs
conda activate gemini_env

# Install Gemini CLI globally via npm (inside the environment)
npm install -g @google/gemini-cli
```

## 运行Gemini CLI

对于大多数用户，我们建议使用 `gemini` 命令运行 Gemini CLI：

```bash
gemini
```

有关选项和其他命令的列表，请参阅 
[CLI 备忘单 ](<https://geminicli.com/docs/get-started/cli/cli-reference.md>)。

您还可以使用以下高级方法之一运行 Gemini CLI：

#### npx

使用 npx 立即运行。您无需永久安装即可运行Gemini CLI。

```bash
# Using npx (no installation required)
npx @google/gemini-cli
```

您还可以直接从GitHub上的主分支执行CLI，这
有助于测试仍在开发中的功能：

```bash
npx https://github.com/google-gemini/gemini-cli
```

#### Docker/Podman 沙盒

为了安全和隔离，Gemini CLI可以在容器内运行。这是
CLI执行可能有副作用的工具的默认方式。

- **直接从注册表：**就可以运行发布的沙箱镜像了
直接。这对于只有 Docker 并希望 
 运行 CLI 的环境非常有用。
```bash
# Run the published sandbox image for a specified CLI version
docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.42.0-nightly.20260428.g59b2dea0e
```
- **使用`--sandbox`标志：**如果您本地安装了Gemini CLI
（使用上述标准安装），您可以指示它在沙箱容器内运行
。
```bash
gemini --sandbox -y -p "your prompt here"
```

#### 从源头

该项目的贡献者将希望直接从源代码 
 运行 CLI。

- **开发模式：**此方法提供热重载，适用于
积极发展。
```bash
# From the root of the repository
npm run start
```
- **生产模式（React优化）：**此方法使用React运行CLI
启用生产模式，这对于测试性能很有用，无需
开发开销。
```bash
# From the root of the repository
npm run start:prod
```
- **类生产模式（链接包）：**该方法模拟了全局
通过链接本地包进行安装。它对于在生产工作流程中测试本地 
 构建非常有用。

```bash
# Link the local cli package to your global node_modules
npm link packages/cli

# Now you can run your local version using the `gemini` command
gemini
```

## 发布

Gemini CLI 有稳定版、预览版、夜间版三种发布渠道。对于大多数
用户，我们推荐稳定版本，这是默认安装。

#### 稳定

每周都会发布稳定版本。从
上周的预览版本以及所有错误修复中创建了稳定版本。稳定版本
使用`latest`标签。省略标签也会默认安装最新的稳定版本
。

```bash
# Both commands install the latest stable release.
npm install -g @google/gemini-cli
npm install -g @google/gemini-cli@latest
```

#### 预览

新的预览版本将每周发布。这些版本尚未经过完全
审查，可能包含回归或其他未决问题。使用 `preview` 标签尝试 
 预览版：

```bash
npm install -g @google/gemini-cli@preview
```

#### 每晚

每天都会发布夜间版本。每晚版本包括发布时主分支的所有 
 更改。应该假设存在
待验证和问题。您可以通过使用 `nightly` 标签安装 
 来帮助测试最新更改：

```bash
npm install -g @google/gemini-cli@nightly
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI installation, execution, and releases

This document provides an overview of Gemini CLI's system requirements,
installation methods, and release types.

## Recommended system specifications

- **Operating System:**
  - macOS 15+
  - Windows 11 24H2+
  - Ubuntu 20.04+
- **Hardware:**
  - "Casual" usage: 4GB+ RAM (short sessions, common tasks and edits)
  - "Power" usage: 16GB+ RAM (long sessions, large codebases, deep context)
- **Runtime:** Node.js 20.0.0+
- **Shell:** Bash, Zsh, or PowerShell
- **Location:**
  [Gemini Code Assist supported locations](<https://developers.google.com/gemini-code-assist/resources/available-locations#americas>)
- **Internet connection required**

## Install Gemini CLI

We recommend most users install Gemini CLI using one of the following
installation methods. Note that Gemini CLI comes pre-installed on
[**Cloud Shell**](<https://docs.cloud.google.com/shell/docs>) and
[**Cloud Workstations**](<https://cloud.google.com/workstations>).

#### npm

Install globally with npm:

```bash
npm install -g @google/gemini-cli
```

#### Homebrew (macOS/Linux)

Install globally with Homebrew:

```bash
brew install gemini-cli
```

#### MacPorts (macOS)

Install globally with MacPorts:

```bash
sudo port install gemini-cli
```

#### Anaconda

Install with Anaconda (for restricted environments):

```bash
# Create and activate a new environment
conda create -y -n gemini_env -c conda-forge nodejs
conda activate gemini_env

# Install Gemini CLI globally via npm (inside the environment)
npm install -g @google/gemini-cli
```

## Run Gemini CLI

For most users, we recommend running Gemini CLI with the `gemini` command:

```bash
gemini
```

For a list of options and additional commands, see the
[CLI cheatsheet](<https://geminicli.com/docs/get-started/cli/cli-reference.md>).

You can also run Gemini CLI using one of the following advanced methods:

#### npx

Run instantly with npx. You can run Gemini CLI without permanent installation.

```bash
# Using npx (no installation required)
npx @google/gemini-cli
```

You can also execute the CLI directly from the main branch on GitHub, which is
helpful for testing features still in development:

```bash
npx https://github.com/google-gemini/gemini-cli
```

#### Docker/Podman Sandbox

For security and isolation, Gemini CLI can be run inside a container. This is
the default way that the CLI executes tools that might have side effects.

- **Directly from the registry:** You can run the published sandbox image
directly. This is useful for environments where you only have Docker and want
to run the CLI.
```bash
# Run the published sandbox image for a specified CLI version
docker run --rm -it us-docker.pkg.dev/gemini-code-dev/gemini-cli/sandbox:0.42.0-nightly.20260428.g59b2dea0e
```
- **Using the `--sandbox` flag:** If you have Gemini CLI installed locally
(using the standard installation described above), you can instruct it to run
inside the sandbox container.
```bash
gemini --sandbox -y -p "your prompt here"
```

#### From source

Contributors to the project will want to run the CLI directly from the source
code.

- **Development mode:** This method provides hot-reloading and is useful for
active development.
```bash
# From the root of the repository
npm run start
```
- **Production mode (React optimizations):** This method runs the CLI with React
production mode enabled, which is useful for testing performance without
development overhead.
```bash
# From the root of the repository
npm run start:prod
```
- **Production-like mode (linked package):** This method simulates a global
installation by linking your local package. It's useful for testing a local
build in a production workflow.

```bash
# Link the local cli package to your global node_modules
npm link packages/cli

# Now you can run your local version using the `gemini` command
gemini
```

## Releases

Gemini CLI has three release channels: stable, preview, and nightly. For most
users, we recommend the stable release, which is the default installation.

#### Stable

Stable releases are published each week. A stable release is created from the
previous week's preview release along with any bug fixes. The stable release
uses the `latest` tag. Omitting the tag also installs the latest stable
release by default.

```bash
# Both commands install the latest stable release.
npm install -g @google/gemini-cli
npm install -g @google/gemini-cli@latest
```

#### Preview

New preview releases will be published each week. These releases are not fully
vetted and may contain regressions or other outstanding issues. Try out the
preview release by using the `preview` tag:

```bash
npm install -g @google/gemini-cli@preview
```

#### Nightly

Nightly releases are published every day. The nightly release includes all
changes from the main branch at time of release. It should be assumed there are
pending validations and issues. You can help test the latest changes by
installing with the `nightly` tag:

```bash
npm install -g @google/gemini-cli@nightly
```


</details>
