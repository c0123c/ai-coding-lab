# Gemini CLI 官方仓库说明

介绍安装渠道、发布通道、代码理解与生成、自动化、GitHub 集成和认证方式，提供产品与开发入口。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-05-01](<https://github.com/google-gemini/gemini-cli/commit/4e81f48646602e3b55d5e0776001e2fa062301a0>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 官方仓库说明.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini CLI

[！[Gemini CLI CI](<https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml/badge.svg>)](<https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml>)
[！[Gemini CLI E2E （链接）](<https://github.com/google-gemini/gemini-cli/actions/workflows/chained_e2e.yml/badge.svg>)](<https://github.com/google-gemini/gemini-cli/actions/workflows/chained_e2e.yml>)
[！[版本](<https://img.shields.io/npm/v/@google/gemini-cli>)](<https://www.npmjs.com/package/@google/gemini-cli>)
[！[许可证](<https://img.shields.io/github/license/google-gemini/gemini-cli>)](<https://github.com/google-gemini/gemini-cli/blob/main/LICENSE>)
[！[查看代码Wiki](<https://assets.codewiki.google/readme-badge/static.svg>)](<https://codewiki.google/github.com/google-gemini/gemini-cli?utm_source=badge&utm_medium=github&utm_campaign=github.com/google-gemini/gemini-cli>)

![Gemini CLI 截图](<https://github.com/docs/assets/gemini-screenshot.png>)

Gemini CLI 是一个开源 AI agent，它将 Gemini 的功能直接带入您的终端 
。它提供对 Gemini 的轻量级访问，为您提供从提示到我们的模型的 
 最直接的路径。

在我们的 [ 文档 ](<https://geminicli.com/docs/>) 中了解有关 Gemini CLI 的所有信息。

## 🚀 为什么是Gemini CLI？

- **🎯 免费套餐**：60 requests/min 和 1,000 requests/day 以及个人 Google
帐户。
- **🧠强大的Gemini 3模型**：获得改进的推理和1M代币
上下文窗口。
- **🔧内置工具**：Google搜索接地、文件操作、shell
命令，网页抓取。
- **🔌可扩展**：MCP（Model Context Protocol）支持自定义
集成。
- **💻 终端优先**：专为使用命令行的开发人员设计。
- **🛡️ 开源**：Apache 2.0 许可。

## 📦 安装

请参阅
[Gemini CLI安装、执行和发布](<https://www.geminicli.com/docs/get-started/installation>)
以获取推荐的系统规格和详细的安装指南。

### 快速安装

#### 使用 npx 立即运行

```bash
# Using npx (no installation required)
npx @google/gemini-cli
```

#### 使用 npm 进行全局安装

```bash
npm install -g @google/gemini-cli
```

#### 使用 Homebrew 进行全局安装 (macOS/Linux)

```bash
brew install gemini-cli
```

#### 使用 MacPorts (macOS) 进行全局安装

```bash
sudo port install gemini-cli
```

#### 使用 Anaconda 安装（适用于受限环境）

```bash
# Create and activate a new environment
conda create -y -n gemini_env -c conda-forge nodejs
conda activate gemini_env

# Install Gemini CLI globally via npm (inside the environment)
npm install -g @google/gemini-cli
```

## 发布渠道

有关更多详细信息，请参阅 [ 版本 ](<https://www.geminicli.com/docs/changelogs>)。

### 预览

新的预览版本将在每周星期二的 UTC 23:59 上发布。这些
版本尚未经过充分审查，可能包含回归或其他
未决问题。请使用`preview`标签帮助我们测试和安装。

```bash
npm install -g @google/gemini-cli@preview
```

### 稳定

- 新的稳定版本将每周二在 UTC 20:00 发布，此
将全面推广上周的 `preview` 版本 + 任何错误修复 
 和验证。使用`latest`标签。

```bash
npm install -g @google/gemini-cli@latest
```

### 每晚

- 新版本将每天在 UTC 00:00 发布。这将是所有的改变
来自发布时所示的主分支。应该假设
 存在未决的验证和问题。使用`nightly`标签。

```bash
npm install -g @google/gemini-cli@nightly
```

## 📋 主要特点

### 代码理解和生成

- 查询和编辑大型代码库
- 使用多模式功能从 PDF、图像或草图生成新应用程序
- 使用自然语言调试问题并排除故障

### 自动化与集成

- 自动执行操作任务，例如查询拉取请求或处理复杂的任务
变基
- 使用 MCP 服务器连接新功能，包括
[使用 Imagen、Veo 或 Lyria 生成媒体](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia>)
- 在脚本中以非交互方式运行以实现工作流程自动化

### 先进的功能

- 使用内置的查询来解答您的疑问
[Google搜索](<https://ai.google.dev/gemini-api/docs/grounding>)实时
信息
- 对话检查点以保存和恢复复杂的会话
- 自定义上下文文件 (GEMINI.md) 为您的项目定制行为

### GitHub 集成

使用 
[**Gemini CLI GitHub 操作 **](<https://github.com/google-github-actions/run-gemini-cli>) 将 Gemini CLI 直接集成到 GitHub 工作流程中：

- **拉取请求审查**：带有上下文反馈的自动代码审查和
建议
- **问题分类**：基于GitHub问题的自动标记和优先级排序
关于内容分析
- **按需帮助**：在问题和拉取请求中提及`@gemini-cli`
寻求调试、解释或任务委派方面的帮助
- **自定义工作流程**：构建自动化、计划和按需工作流程
根据您团队的需求量身定制

## 🔐 身份验证选项

选择最适合您需求的身份验证方法：

### 选项1：使用Google登录（使用您的Google帐户进行OAuth登录）

**✨ 最适合：** 个人开发者以及拥有 Gemini 代码 
 辅助许可证的任何人。 （详情请参阅
[配额限制和服务条款](<https://cloud.google.com/gemini/docs/quotas>)
）

**优点：**

- **免费套餐**：60 requests/min 和 1,000 requests/day
- **Gemini 3 型号 ** 具有 1M 令牌上下文窗口
- **无API密钥管理** - 只需使用您的Google帐户登录
- **自动更新**到最新型号

#### 启动 Gemini CLI，然后选择 _使用 Google 登录_，并在出现提示时按照浏览器身份验证流程进行操作

```bash
gemini
```

#### 如果您使用组织提供的付费代码辅助许可证，请记住设置 Google 云项目

```bash
# Set your Google Cloud Project
export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
gemini
```

### 选项 2: Gemini API 键

**✨ 最适合：** 需要特定模型控制或付费层访问权限的开发者

**优点：**

- **免费套餐**：1000 requests/day 与 Gemini 3（Flash 和 Pro 的组合）
- **型号选择**：选择具体的Gemini型号
- **基于使用情况的计费**：需要时升级以获得更高的限制

```bash
# Get your key from https://aistudio.google.com/apikey
export GEMINI_API_KEY="YOUR_API_KEY"
gemini
```

### 选项 3：顶点 AI

**✨ 最适合：** 企业团队和生产工作负载

**优点：**

- **企业功能**：高级安全性和合规性
- **可扩展**：计费帐户的费率限制更高
- **集成**：与现有的Google云基础设施配合使用

```bash
# Get your key from Google Cloud Console
export GOOGLE_API_KEY="YOUR_API_KEY"
export GOOGLE_GENAI_USE_VERTEXAI=true
gemini
```

对于Google工作区帐户和其他身份验证方法，请参阅
[身份验证指南](<https://www.geminicli.com/docs/get-started/authentication>)。

## 🚀 开始使用

### 基本用法

#### 从当前目录开始

```bash
gemini
```

#### 包含多个目录

```bash
gemini --include-directories ../lib,../docs
```

#### 使用特定型号

```bash
gemini -m gemini-2.5-flash
```

#### 脚本的非交互模式

获取简单的文本响应：

```bash
gemini -p "Explain the architecture of this codebase"
```

对于更高级的脚本，包括如何解析 JSON 和处理错误，请使用 
 `--output-format json` 标志来获取结构化输出：

```bash
gemini -p "Explain the architecture of this codebase" --output-format json
```

对于实时事件流（对于监视长时间运行的操作很有用），
使用`--output-format stream-json`获取换行符分隔的JSON事件：

```bash
gemini -p "Run tests and deploy" --output-format stream-json
```

### 快速示例

#### 开始一个新项目

```bash
cd new-project/
gemini
> Write me a Discord bot that answers questions using a FAQ.md file I will provide
```

#### 分析现有代码

```bash
git clone https://github.com/google-gemini/gemini-cli
cd gemini-cli
gemini
> Give me a summary of all of the changes that went in yesterday
```

## 📚 文档

### 开始使用

- [**快速入门指南**](<https://www.geminicli.com/docs/get-started>) - 起床
并且跑得很快。
- [**身份验证设置**](<https://www.geminicli.com/docs/get-started/authentication>) -
详细的身份验证配置。
- [**配置指南**](<https://www.geminicli.com/docs/reference/configuration>) -
设置和定制。
- [**键盘快捷键**](<https://www.geminicli.com/docs/reference/keyboard-shortcuts>) -
生产力技巧。

### 核心特点

- [**命令参考**](<https://www.geminicli.com/docs/reference/commands>) -
所有斜杠命令（`/help`、`/chat` 等）。
- [**自定义命令**](<https://www.geminicli.com/docs/cli/custom-commands>) -
创建您自己的可重用命令。
- [**上下文文件 (GEMINI.md)**](<https://www.geminicli.com/docs/cli/gemini-md>) -
为 Gemini CLI 提供持久上下文。
- [**检查点**](<https://www.geminicli.com/docs/cli/checkpointing>) - 保存
并恢复对话。
- [**令牌缓存**](<https://www.geminicli.com/docs/cli/token-caching>) -
优化代币使用。

### 工具和扩展

- [**内置工具概述**](<https://www.geminicli.com/docs/reference/tools>)
  - [文件系统操作](<https://www.geminicli.com/docs/tools/file-system>)
  - [Shell 命令](<https://www.geminicli.com/docs/tools/shell>)
  - [网页获取和搜索](<https://www.geminicli.com/docs/tools/web-fetch>)
- [**MCP 服务器集成**](<https://www.geminicli.com/docs/tools/mcp-server>) -
使用自定义工具进行扩展。
- [**自定义扩展**](<https://geminicli.com/docs/extensions/writing-extensions>) -
构建并共享您自己的命令。

### 高级主题

- [**无头模式（脚本）**](<https://www.geminicli.com/docs/cli/headless>) -
在自动化工作流程中使用 Gemini CLI。
- [**IDE 集成**](<https://www.geminicli.com/docs/ide-integration>) - VS
代码伴侣。
- [**沙箱和安全**](<https://www.geminicli.com/docs/cli/sandbox>) - 安全
执行环境。
- [**受信任文件夹**](<https://www.geminicli.com/docs/cli/trusted-folders>) -
按文件夹控制执行策略。
- [**企业指南**](<https://www.geminicli.com/docs/cli/enterprise>) - 部署
并在企业环境中进行管理。
- [**遥测与监控**](<https://www.geminicli.com/docs/cli/telemetry>) -
使用情况跟踪。
- [**工具参考**](<https://www.geminicli.com/docs/reference/tools>) -
内置工具概述。
- [**本地开发**](<https://www.geminicli.com/docs/local-development>) -
本地开发工具。

### 故障排除与支持

- [**故障排除指南**](<https://www.geminicli.com/docs/resources/troubleshooting>) -
常见问题及解决方案。
- [**FAQ**](<https://www.geminicli.com/docs/resources/faq>) - 常见问题
问题。
- 使用`/bug`命令直接从CLI报告问题。

### 使用 MCP 服务器

配置 `~/.gemini/settings.json` 中的 MCP 服务器以使用 
 自定义工具扩展 Gemini CLI：

```text
> @github List my open pull requests
> @slack Send a summary of today's commits to #dev channel
> @database Run a query to find inactive users
```

有关设置说明，请参阅
[MCP服务器集成指南](<https://www.geminicli.com/docs/tools/mcp-server>)
。

## 🤝 贡献

我们欢迎贡献！ Gemini CLI 是完全开源的（Apache 2.0），我们
鼓励社区：

- 报告错误并建议功能。
- 改进文档。
- 提交代码改进。
- 共享您的 MCP 服务器和扩展。

请参阅我们的 [ 贡献指南 ](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/CONTRIBUTING.md>)，了解开发设置、编码 
 标准以及如何提交拉取请求。

查看我们的[官方路线图](<https://github.com/orgs/google-gemini/projects/11>)
以了解计划的功能和优先级。

## 📖 资源

- **[免费课程](<https://learn.deeplearning.ai/courses/gemini-cli-code-and-create-with-an-open-source-agent/information>)** -
学习基础知识。
- **[官方路线图](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/ROADMAP.md>)** - 看看接下来会发生什么。
- **[变更日志](<https://www.geminicli.com/docs/changelogs>)** - 查看最近的内容
值得注意的更新。
- **[NPM 封装](<https://www.npmjs.com/package/@google/gemini-cli>)** - 封装
注册表。
- **[GitHub 问题](<https://github.com/google-gemini/gemini-cli/issues>)** -
报告错误或请求功能。
- **[安全公告](<https://github.com/google-gemini/gemini-cli/security/advisories>)** -
安全更新。

### 卸载

请参阅[卸载指南](<https://www.geminicli.com/docs/resources/uninstall>)
以获取删除说明。

## 📄 法律

- **许可证**：[Apache许可证2.0](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/LICENSE>)
- **服务条款**：
[条款与隐私](<https://www.geminicli.com/docs/resources/tos-privacy>)
- **安全**：[安全策略](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/SECURITY.md>)

<p align="left">
 <a href="https://www.star-history.com/google-gemini/gemini-cli">
  <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/badge?repo=google-gemini/gemini-cli&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/badge?repo=google-gemini/gemini-cli" />
   

![明星历史排名](<https://api.star-history.com/badge?repo=google-gemini/gemini-cli>)

  </picture>
 </a>
</p>

---

<p align="center">
 由 Google 和开源社区 
</p> 使用 ❤️ 构建


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini CLI

[![Gemini CLI CI](<https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml/badge.svg>)](<https://github.com/google-gemini/gemini-cli/actions/workflows/ci.yml>)
[![Gemini CLI E2E (Chained)](<https://github.com/google-gemini/gemini-cli/actions/workflows/chained_e2e.yml/badge.svg>)](<https://github.com/google-gemini/gemini-cli/actions/workflows/chained_e2e.yml>)
[![Version](<https://img.shields.io/npm/v/@google/gemini-cli>)](<https://www.npmjs.com/package/@google/gemini-cli>)
[![License](<https://img.shields.io/github/license/google-gemini/gemini-cli>)](<https://github.com/google-gemini/gemini-cli/blob/main/LICENSE>)
[![View Code Wiki](<https://assets.codewiki.google/readme-badge/static.svg>)](<https://codewiki.google/github.com/google-gemini/gemini-cli?utm_source=badge&utm_medium=github&utm_campaign=github.com/google-gemini/gemini-cli>)

![Gemini CLI Screenshot](<https://github.com/docs/assets/gemini-screenshot.png>)

Gemini CLI is an open-source AI agent that brings the power of Gemini directly
into your terminal. It provides lightweight access to Gemini, giving you the
most direct path from your prompt to our model.

Learn all about Gemini CLI in our [documentation](<https://geminicli.com/docs/>).

## 🚀 Why Gemini CLI?

- **🎯 Free tier**: 60 requests/min and 1,000 requests/day with personal Google
  account.
- **🧠 Powerful Gemini 3 models**: Access to improved reasoning and 1M token
  context window.
- **🔧 Built-in tools**: Google Search grounding, file operations, shell
  commands, web fetching.
- **🔌 Extensible**: MCP (Model Context Protocol) support for custom
  integrations.
- **💻 Terminal-first**: Designed for developers who live in the command line.
- **🛡️ Open source**: Apache 2.0 licensed.

## 📦 Installation

See
[Gemini CLI installation, execution, and releases](<https://www.geminicli.com/docs/get-started/installation>)
for recommended system specifications and a detailed installation guide.

### Quick Install

#### Run instantly with npx

```bash
# Using npx (no installation required)
npx @google/gemini-cli
```

#### Install globally with npm

```bash
npm install -g @google/gemini-cli
```

#### Install globally with Homebrew (macOS/Linux)

```bash
brew install gemini-cli
```

#### Install globally with MacPorts (macOS)

```bash
sudo port install gemini-cli
```

#### Install with Anaconda (for restricted environments)

```bash
# Create and activate a new environment
conda create -y -n gemini_env -c conda-forge nodejs
conda activate gemini_env

# Install Gemini CLI globally via npm (inside the environment)
npm install -g @google/gemini-cli
```

## Release Channels

See [Releases](<https://www.geminicli.com/docs/changelogs>) for more details.

### Preview

New preview releases will be published each week at UTC 23:59 on Tuesdays. These
releases will not have been fully vetted and may contain regressions or other
outstanding issues. Please help us test and install with `preview` tag.

```bash
npm install -g @google/gemini-cli@preview
```

### Stable

- New stable releases will be published each week at UTC 20:00 on Tuesdays, this
  will be the full promotion of last week's `preview` release + any bug fixes
  and validations. Use `latest` tag.

```bash
npm install -g @google/gemini-cli@latest
```

### Nightly

- New releases will be published each day at UTC 00:00. This will be all changes
  from the main branch as represented at time of release. It should be assumed
  there are pending validations and issues. Use `nightly` tag.

```bash
npm install -g @google/gemini-cli@nightly
```

## 📋 Key Features

### Code Understanding & Generation

- Query and edit large codebases
- Generate new apps from PDFs, images, or sketches using multimodal capabilities
- Debug issues and troubleshoot with natural language

### Automation & Integration

- Automate operational tasks like querying pull requests or handling complex
  rebases
- Use MCP servers to connect new capabilities, including
  [media generation with Imagen, Veo or Lyria](<https://github.com/GoogleCloudPlatform/vertex-ai-creative-studio/tree/main/experiments/mcp-genmedia>)
- Run non-interactively in scripts for workflow automation

### Advanced Capabilities

- Ground your queries with built-in
  [Google Search](<https://ai.google.dev/gemini-api/docs/grounding>) for real-time
  information
- Conversation checkpointing to save and resume complex sessions
- Custom context files (GEMINI.md) to tailor behavior for your projects

### GitHub Integration

Integrate Gemini CLI directly into your GitHub workflows with
[**Gemini CLI GitHub Action**](<https://github.com/google-github-actions/run-gemini-cli>):

- **Pull Request Reviews**: Automated code review with contextual feedback and
  suggestions
- **Issue Triage**: Automated labeling and prioritization of GitHub issues based
  on content analysis
- **On-demand Assistance**: Mention `@gemini-cli` in issues and pull requests
  for help with debugging, explanations, or task delegation
- **Custom Workflows**: Build automated, scheduled and on-demand workflows
  tailored to your team's needs

## 🔐 Authentication Options

Choose the authentication method that best fits your needs:

### Option 1: Sign in with Google (OAuth login using your Google Account)

**✨ Best for:** Individual developers as well as anyone who has a Gemini Code
Assist License. (see
[quota limits and terms of service](<https://cloud.google.com/gemini/docs/quotas>)
for details)

**Benefits:**

- **Free tier**: 60 requests/min and 1,000 requests/day
- **Gemini 3 models** with 1M token context window
- **No API key management** - just sign in with your Google account
- **Automatic updates** to latest models

#### Start Gemini CLI, then choose _Sign in with Google_ and follow the browser authentication flow when prompted

```bash
gemini
```

#### If you are using a paid Code Assist License from your organization, remember to set the Google Cloud Project

```bash
# Set your Google Cloud Project
export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
gemini
```

### Option 2: Gemini API Key

**✨ Best for:** Developers who need specific model control or paid tier access

**Benefits:**

- **Free tier**: 1000 requests/day with Gemini 3 (mix of flash and pro)
- **Model selection**: Choose specific Gemini models
- **Usage-based billing**: Upgrade for higher limits when needed

```bash
# Get your key from https://aistudio.google.com/apikey
export GEMINI_API_KEY="YOUR_API_KEY"
gemini
```

### Option 3: Vertex AI

**✨ Best for:** Enterprise teams and production workloads

**Benefits:**

- **Enterprise features**: Advanced security and compliance
- **Scalable**: Higher rate limits with billing account
- **Integration**: Works with existing Google Cloud infrastructure

```bash
# Get your key from Google Cloud Console
export GOOGLE_API_KEY="YOUR_API_KEY"
export GOOGLE_GENAI_USE_VERTEXAI=true
gemini
```

For Google Workspace accounts and other authentication methods, see the
[authentication guide](<https://www.geminicli.com/docs/get-started/authentication>).

## 🚀 Getting Started

### Basic Usage

#### Start in current directory

```bash
gemini
```

#### Include multiple directories

```bash
gemini --include-directories ../lib,../docs
```

#### Use specific model

```bash
gemini -m gemini-2.5-flash
```

#### Non-interactive mode for scripts

Get a simple text response:

```bash
gemini -p "Explain the architecture of this codebase"
```

For more advanced scripting, including how to parse JSON and handle errors, use
the `--output-format json` flag to get structured output:

```bash
gemini -p "Explain the architecture of this codebase" --output-format json
```

For real-time event streaming (useful for monitoring long-running operations),
use `--output-format stream-json` to get newline-delimited JSON events:

```bash
gemini -p "Run tests and deploy" --output-format stream-json
```

### Quick Examples

#### Start a new project

```bash
cd new-project/
gemini
> Write me a Discord bot that answers questions using a FAQ.md file I will provide
```

#### Analyze existing code

```bash
git clone https://github.com/google-gemini/gemini-cli
cd gemini-cli
gemini
> Give me a summary of all of the changes that went in yesterday
```

## 📚 Documentation

### Getting Started

- [**Quickstart Guide**](<https://www.geminicli.com/docs/get-started>) - Get up
  and running quickly.
- [**Authentication Setup**](<https://www.geminicli.com/docs/get-started/authentication>) -
  Detailed auth configuration.
- [**Configuration Guide**](<https://www.geminicli.com/docs/reference/configuration>) -
  Settings and customization.
- [**Keyboard Shortcuts**](<https://www.geminicli.com/docs/reference/keyboard-shortcuts>) -
  Productivity tips.

### Core Features

- [**Commands Reference**](<https://www.geminicli.com/docs/reference/commands>) -
  All slash commands (`/help`, `/chat`, etc).
- [**Custom Commands**](<https://www.geminicli.com/docs/cli/custom-commands>) -
  Create your own reusable commands.
- [**Context Files (GEMINI.md)**](<https://www.geminicli.com/docs/cli/gemini-md>) -
  Provide persistent context to Gemini CLI.
- [**Checkpointing**](<https://www.geminicli.com/docs/cli/checkpointing>) - Save
  and resume conversations.
- [**Token Caching**](<https://www.geminicli.com/docs/cli/token-caching>) -
  Optimize token usage.

### Tools & Extensions

- [**Built-in Tools Overview**](<https://www.geminicli.com/docs/reference/tools>)
  - [File System Operations](<https://www.geminicli.com/docs/tools/file-system>)
  - [Shell Commands](<https://www.geminicli.com/docs/tools/shell>)
  - [Web Fetch & Search](<https://www.geminicli.com/docs/tools/web-fetch>)
- [**MCP Server Integration**](<https://www.geminicli.com/docs/tools/mcp-server>) -
  Extend with custom tools.
- [**Custom Extensions**](<https://geminicli.com/docs/extensions/writing-extensions>) -
  Build and share your own commands.

### Advanced Topics

- [**Headless Mode (Scripting)**](<https://www.geminicli.com/docs/cli/headless>) -
  Use Gemini CLI in automated workflows.
- [**IDE Integration**](<https://www.geminicli.com/docs/ide-integration>) - VS
  Code companion.
- [**Sandboxing & Security**](<https://www.geminicli.com/docs/cli/sandbox>) - Safe
  execution environments.
- [**Trusted Folders**](<https://www.geminicli.com/docs/cli/trusted-folders>) -
  Control execution policies by folder.
- [**Enterprise Guide**](<https://www.geminicli.com/docs/cli/enterprise>) - Deploy
  and manage in a corporate environment.
- [**Telemetry & Monitoring**](<https://www.geminicli.com/docs/cli/telemetry>) -
  Usage tracking.
- [**Tools reference**](<https://www.geminicli.com/docs/reference/tools>) -
  Built-in tools overview.
- [**Local development**](<https://www.geminicli.com/docs/local-development>) -
  Local development tooling.

### Troubleshooting & Support

- [**Troubleshooting Guide**](<https://www.geminicli.com/docs/resources/troubleshooting>) -
  Common issues and solutions.
- [**FAQ**](<https://www.geminicli.com/docs/resources/faq>) - Frequently asked
  questions.
- Use `/bug` command to report issues directly from the CLI.

### Using MCP Servers

Configure MCP servers in `~/.gemini/settings.json` to extend Gemini CLI with
custom tools:

```text
> @github List my open pull requests
> @slack Send a summary of today's commits to #dev channel
> @database Run a query to find inactive users
```

See the
[MCP Server Integration guide](<https://www.geminicli.com/docs/tools/mcp-server>)
for setup instructions.

## 🤝 Contributing

We welcome contributions! Gemini CLI is fully open source (Apache 2.0), and we
encourage the community to:

- Report bugs and suggest features.
- Improve documentation.
- Submit code improvements.
- Share your MCP servers and extensions.

See our [Contributing Guide](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/CONTRIBUTING.md>) for development setup, coding
standards, and how to submit pull requests.

Check our [Official Roadmap](<https://github.com/orgs/google-gemini/projects/11>)
for planned features and priorities.

## 📖 Resources

- **[Free Course](<https://learn.deeplearning.ai/courses/gemini-cli-code-and-create-with-an-open-source-agent/information>)** -
  Learn the basics.
- **[Official Roadmap](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/ROADMAP.md>)** - See what's coming next.
- **[Changelog](<https://www.geminicli.com/docs/changelogs>)** - See recent
  notable updates.
- **[NPM Package](<https://www.npmjs.com/package/@google/gemini-cli>)** - Package
  registry.
- **[GitHub Issues](<https://github.com/google-gemini/gemini-cli/issues>)** -
  Report bugs or request features.
- **[Security Advisories](<https://github.com/google-gemini/gemini-cli/security/advisories>)** -
  Security updates.

### Uninstall

See the [Uninstall Guide](<https://www.geminicli.com/docs/resources/uninstall>)
for removal instructions.

## 📄 Legal

- **License**: [Apache License 2.0](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/LICENSE>)
- **Terms of Service**:
  [Terms & Privacy](<https://www.geminicli.com/docs/resources/tos-privacy>)
- **Security**: [Security Policy](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/SECURITY.md>)

<p align="left">
 <a href="https://www.star-history.com/google-gemini/gemini-cli">
  <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/badge?repo=google-gemini/gemini-cli&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/badge?repo=google-gemini/gemini-cli" />
   

![Star History Rank](<https://api.star-history.com/badge?repo=google-gemini/gemini-cli>)

  </picture>
 </a>
</p>

---

<p align="center">
  Built with ❤️ by Google and the open source community
</p>


</details>
