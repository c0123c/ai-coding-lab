# Gemini CLI 常见问题排查

汇总 API 配额、安装升级、Windows、模型、认证与密钥配置等问题及处理方式，并区分相关产品订阅条件。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-10](<https://github.com/google-gemini/gemini-cli/commit/e09410b6e5b7c56222c6583e8f028a35d623c764>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/resources/faq.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/resources/faq.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 常见问题（FAQ）

本页提供了常见问题的解答以及使用Gemini CLI时遇到的常见
问题的解决方案。

## 一般问题

本节解决有关 Gemini CLI 使用、安全性和 
 一般错误故障排除的常见问题。

### 为什么我无法将 Claude Code、OpenClaw 或 OpenCode 等第三方软件与 Gemini CLI 一起使用？

使用第三方软件、工具或服务获取或搭载 Gemini
CLI 的 OAuth 身份验证来访问我们的后端服务是直接违反 
 我们的 [ 适用条款和政策 ](<Gemini CLI 许可、条款与隐私说明.md>)。这样做会绕过我们的
预期的身份验证和安全结构，并且此类行为可能会成为
立即暂停或终止您的帐户的理由。如果您想
使用第三方编码agent和Gemini，受支持且安全的方法是
使用Vertex AI或Google AI Studio API键。

### 为什么我会得到 `API error: 429 - Resource exhausted`？

此错误表明您已超出 API 请求限制。 Gemini
API 具有速率限制，以防止滥用并确保公平使用。

要解决此问题，您可以：

- **检查您的使用情况：** 在 Google AI Studio 或您的工作室中查看您的 API 使用情况
Google 云项目仪表板。
- **优化您的提示：**如果您在短时间内提出大量请求，
尝试批量提示或在请求之间引入延迟。
- **请求增加配额：**如果您始终需要更高的限制，您可以
请求 Google 增加配额。

### 为什么我在运行 `npm run start` 时收到 `ERR_REQUIRE_ESM` 错误？

当 
CommonJS 和 ES 模块之间不匹配时，此错误通常发生在 Node.js 项目中。

这通常是由于 `package.json` 或 
`tsconfig.json` 中的配置错误造成的。确保：

1.  您的`package.json`有`"type": "module"`。
2.  您的 `tsconfig.json` 中有 `"module": "NodeNext"` 或兼容的设置
`compilerOptions`。

如果问题仍然存在，请尝试删除 `node_modules` 目录和 
`package-lock.json` 文件，然后再次运行 `npm install`。

### 为什么我在统计输出中看不到缓存的令牌计数？

仅当使用缓存令牌时才会显示缓存令牌信息。
此功能适用于 API 关键用户（Gemini API key 或 Google Cloud
Vertex AI），但不适用于对于 OAuth 用户（例如 Google Personal/Enterprise 帐户 
，例如 Google Gmail 或 Google Workspace）。这是因为 Gemini
代码辅助 API 不支持缓存内容创建。您仍然可以使用Gemini CLI中的`/stats`命令查看
您的代币总使用量。

## 安装和更新

### 如何查看我当前运行的 Gemini CLI 版本？

您可以使用以下方法之一检查当前的 Gemini CLI 版本：

- 从终端运行 `gemini --version` 或 `gemini -v`
- 使用包管理器检查全局安装的版本：
  - npm: `npm list -g @google/gemini-cli`
  - pnpm: `pnpm list -g @google/gemini-cli`
  - 纱线：`yarn global list @google/gemini-cli`
  - bun: `bun pm ls -g @google/gemini-cli`
  - 自制软件：`brew list --versions gemini-cli`
- 在活动的 Gemini CLI 会话中，使用 `/about` 命令

### 如何将Gemini CLI更新到最新版本？

如果您通过`npm`全局安装它，请使用命令
`npm install -g @google/gemini-cli@latest`更新它。如果您从源代码编译它，请从存储库中提取 
 最新更改，然后使用命令 
`npm run build` 进行重建。

## 平台特定问题

### 当我运行 `chmod +x` 这样的命令时，为什么 CLI 在 Windows 上崩溃？

像 `chmod` 这样的命令特定于类 Unix 操作系统（Linux、
macOS）。默认情况下，它们在 Windows 上不可用。

要解决此问题，您可以：

- **使用 Windows 等效命令：** 您可以使用 `icacls` 代替 `chmod`
修改 Windows 上的文件权限。
- **使用兼容层：**Git Bash 或 Windows 子系统等工具
Linux (WSL) 在 Windows 上提供了一个类似 Unix 的环境，这些命令
 可以在其中运行。

## 配置

### 如何配置我的`GOOGLE_CLOUD_PROJECT`？

您可以使用环境变量配置您的Google云项目ID。

在 shell 中设置 `GOOGLE_CLOUD_PROJECT` 环境变量：

**macOS/Linux**

```bash
export GOOGLE_CLOUD_PROJECT="your-project-id"
```

**Windows (PowerShell)**

```powershell
$env:GOOGLE_CLOUD_PROJECT="your-project-id"
```

要使此设置永久生效，请将此行添加到 shell 的启动文件中（例如 
、`~/.bashrc`、`~/.zshrc`）。

### 安全存储我的 API 密钥的最佳方法是什么？

在脚本中暴露 API 密钥或将其签入源代码管理系统存在安全 
 风险。

要安全地存储您的 API 密钥，您可以：

- **使用`.env`文件：**在项目的`.gemini`中创建一个`.env`文件
目录（`.gemini/.env`）并将您的密钥存储在那里。 Gemini CLI将
自动加载这些变量。
- **使用您系统的密钥环：**为了获得最安全的存储，请使用您的操作系统
系统的秘密管理工具（例如 macOS 钥匙串、Windows 凭据
 管理器或 Linux 上的秘密管理器）。然后，您可以让脚本或
环境在运行时从安全存储加载密钥。

### Gemini CLI 配置和设置文件存储在哪里？

Gemini CLI配置存储在两个`settings.json`文件中：

1.  在您的主目录中：`~/.gemini/settings.json`。
2.  在项目的根目录中：`./.gemini/settings.json`。

参考 [Gemini CLI 配置](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>) 了解更多
详细信息。

## Google AI Pro/Ultra 和订阅常见问题解答

### 在哪里可以详细了解我的 Google AI Pro 或 Google AI Ultra 订阅？

要详细了解您的 Google AI Pro 或 Google AI Ultra 订阅，请访问 
**管理订阅 ** 在您的 [ 订阅设置 ](<https://one.google.com>) 中。

### 我如何知道 Google AI Pro 或 Ultra 是否有更高的限制？

如果您订阅了 Google AI Pro 或 Ultra，您自动拥有更高的
限制为 Gemini 代码辅助和 Gemini CLI。这些是共享的 Gemini CLI
和 agent 模式中的 IDE。您可以通过检查是否有更高的限制来确认
您仍然订阅了 Google AI Pro 或 Ultra 在您的
[订阅设置](<https://one.google.com>).

### 如果我订阅了 Google AI Pro 或 Ultra，使用 Gemini Code Assist 或 Gemini CLI 的隐私政策是什么？

要详细了解您的 
 订阅所管辖的隐私政策和服务条款，请访问 
[Gemini 代码辅助：服务条款和隐私政策 ](<https://developers.google.com/gemini-code-assist/resources/privacy-notices>)。

### 我已升级到 Google AI Pro 或 Ultra，但它仍然显示我已达到配额限制。这是一个错误吗？

Google AI Pro 或 Ultra 订阅中的较高限额适用于 Gemini 2.5
，跨 Gemini 2.5 Pro 和 Flash。它们在 Gemini 代码辅助 IDE 扩展中跨 Gemini CLI
 和 agent 模式共享配额。您可以在Gemini代码
协助中了解有关Gemini CLI、Gemini代码协助和agent模式的
配额限制的更多信息，网址为
[配额和限制](<https://developers.google.com/gemini-code-assist/resources/quotas>)。

### 如果我通过购买 Google AI Pro 或 Ultra 订阅升级到 Gemini CLI 和 Gemini Code Assist 的更高限制，Gemini 会开始使用我的数据来改进其机器学习模型吗？

如果您
购买付费计划，Google不会使用您的数据来改进Google的机器学习模型。注意：如果您决定继续使用 
Gemini Code Assist 的免费版本、针对个人的 Gemini Code Assist，您还可以选择不再使用 
 使用您的数据来改进 Google 的机器学习模型。有关更多信息，请参阅
[Gemini个人隐私声明的代码协助](<https://developers.google.com/gemini-code-assist/resources/privacy-notice-gemini-code-assist-individuals>)
。

## 没有看到你的问题？

搜索
[Gemini CLI 问答讨论 GitHub](<https://github.com/google-gemini/gemini-cli/discussions/categories/q-a>)
或
[开始新的讨论 GitHub](<https://github.com/google-gemini/gemini-cli/discussions/new?category=q-a>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Frequently asked questions (FAQ)

This page provides answers to common questions and solutions to frequent
problems encountered while using Gemini CLI.

## General issues

This section addresses common questions about Gemini CLI usage, security, and
troubleshooting general errors.

### Why can't I use third-party software like Claude Code, OpenClaw, or OpenCode with Gemini CLI?

Using third-party software, tools, or services to harvest or piggyback on Gemini
CLI's OAuth authentication to access our backend services is a direct violation
of our [applicable terms and policies](<Gemini CLI 许可、条款与隐私说明.md>). Doing so bypasses our
intended authentication and security structures, and such actions may be grounds
for immediate suspension or termination of your account. If you would like to
use a third-party coding agent with Gemini, the supported and secure method is
to use a Vertex AI or Google AI Studio API key.

### Why am I getting an `API error: 429 - Resource exhausted`?

This error indicates that you have exceeded your API request limit. The Gemini
API has rate limits to prevent abuse and ensure fair usage.

To resolve this, you can:

- **Check your usage:** Review your API usage in the Google AI Studio or your
  Google Cloud project dashboard.
- **Optimize your prompts:** If you are making many requests in a short period,
  try to batch your prompts or introduce delays between requests.
- **Request a quota increase:** If you consistently need a higher limit, you can
  request a quota increase from Google.

### Why am I getting an `ERR_REQUIRE_ESM` error when running `npm run start`?

This error typically occurs in Node.js projects when there is a mismatch between
CommonJS and ES Modules.

This is often due to a misconfiguration in your `package.json` or
`tsconfig.json`. Ensure that:

1.  Your `package.json` has `"type": "module"`.
2.  Your `tsconfig.json` has `"module": "NodeNext"` or a compatible setting in
    the `compilerOptions`.

If the problem persists, try deleting your `node_modules` directory and
`package-lock.json` file, and then run `npm install` again.

### Why don't I see cached token counts in my stats output?

Cached token information is only displayed when cached tokens are being used.
This feature is available for API key users (Gemini API key or Google Cloud
Vertex AI) but not for OAuth users (such as Google Personal/Enterprise accounts
like Google Gmail or Google Workspace, respectively). This is because the Gemini
Code Assist API does not support cached content creation. You can still view
your total token usage using the `/stats` command in Gemini CLI.

## Installation and updates

### How do I check which version of Gemini CLI I'm currently running?

You can check your current Gemini CLI version using one of these methods:

- Run `gemini --version` or `gemini -v` from your terminal
- Check the globally installed version using your package manager:
  - npm: `npm list -g @google/gemini-cli`
  - pnpm: `pnpm list -g @google/gemini-cli`
  - yarn: `yarn global list @google/gemini-cli`
  - bun: `bun pm ls -g @google/gemini-cli`
  - homebrew: `brew list --versions gemini-cli`
- Inside an active Gemini CLI session, use the `/about` command

### How do I update Gemini CLI to the latest version?

If you installed it globally via `npm`, update it using the command
`npm install -g @google/gemini-cli@latest`. If you compiled it from source, pull
the latest changes from the repository, and then rebuild using the command
`npm run build`.

## Platform-specific issues

### Why does the CLI crash on Windows when I run a command like `chmod +x`?

Commands like `chmod` are specific to Unix-like operating systems (Linux,
macOS). They are not available on Windows by default.

To resolve this, you can:

- **Use Windows-equivalent commands:** Instead of `chmod`, you can use `icacls`
  to modify file permissions on Windows.
- **Use a compatibility layer:** Tools like Git Bash or Windows Subsystem for
  Linux (WSL) provide a Unix-like environment on Windows where these commands
  will work.

## Configuration

### How do I configure my `GOOGLE_CLOUD_PROJECT`?

You can configure your Google Cloud Project ID using an environment variable.

Set the `GOOGLE_CLOUD_PROJECT` environment variable in your shell:

**macOS/Linux**

```bash
export GOOGLE_CLOUD_PROJECT="your-project-id"
```

**Windows (PowerShell)**

```powershell
$env:GOOGLE_CLOUD_PROJECT="your-project-id"
```

To make this setting permanent, add this line to your shell's startup file (for
example, `~/.bashrc`, `~/.zshrc`).

### What is the best way to store my API keys securely?

Exposing API keys in scripts or checking them into source control is a security
risk.

To store your API keys securely, you can:

- **Use a `.env` file:** Create a `.env` file in your project's `.gemini`
  directory (`.gemini/.env`) and store your keys there. Gemini CLI will
  automatically load these variables.
- **Use your system's keyring:** For the most secure storage, use your operating
  system's secret management tool (like macOS Keychain, Windows Credential
  Manager, or a secret manager on Linux). You can then have your scripts or
  environment load the key from the secure storage at runtime.

### Where are Gemini CLI configuration and settings files stored?

Gemini CLI configuration is stored in two `settings.json` files:

1.  In your home directory: `~/.gemini/settings.json`.
2.  In your project's root directory: `./.gemini/settings.json`.

Refer to [Gemini CLI Configuration](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/reference/configuration.md>) for more
details.

## Google AI Pro/Ultra and subscription FAQs

### Where can I learn more about my Google AI Pro or Google AI Ultra subscription?

To learn more about your Google AI Pro or Google AI Ultra subscription, visit
**Manage subscription** in your [subscription settings](<https://one.google.com>).

### How do I know if I have higher limits for Google AI Pro or Ultra?

If you're subscribed to Google AI Pro or Ultra, you automatically have higher
limits to Gemini Code Assist and Gemini CLI. These are shared across Gemini CLI
and agent mode in the IDE. You can confirm you have higher limits by checking if
you are still subscribed to Google AI Pro or Ultra in your
[subscription settings](<https://one.google.com>).

### What is the privacy policy for using Gemini Code Assist or Gemini CLI if I've subscribed to Google AI Pro or Ultra?

To learn more about your privacy policy and terms of service governed by your
subscription, visit
[Gemini Code Assist: Terms of Service and Privacy Policies](<https://developers.google.com/gemini-code-assist/resources/privacy-notices>).

### I've upgraded to Google AI Pro or Ultra but it still says I am hitting quota limits. Is this a bug?

The higher limits in your Google AI Pro or Ultra subscription are for Gemini 2.5
across both Gemini 2.5 Pro and Flash. They are shared quota across Gemini CLI
and agent mode in Gemini Code Assist IDE extensions. You can learn more about
quota limits for Gemini CLI, Gemini Code Assist and agent mode in Gemini Code
Assist at
[Quotas and limits](<https://developers.google.com/gemini-code-assist/resources/quotas>).

### If I upgrade to higher limits for Gemini CLI and Gemini Code Assist by purchasing a Google AI Pro or Ultra subscription, will Gemini start using my data to improve its machine learning models?

Google does not use your data to improve Google's machine learning models if you
purchase a paid plan. Note: If you decide to remain on the free version of
Gemini Code Assist, Gemini Code Assist for individuals, you can also opt out of
using your data to improve Google's machine learning models. See the
[Gemini Code Assist for individuals privacy notice](<https://developers.google.com/gemini-code-assist/resources/privacy-notice-gemini-code-assist-individuals>)
for more information.

## Not seeing your question?

Search the
[Gemini CLI Q&A discussions on GitHub](<https://github.com/google-gemini/gemini-cli/discussions/categories/q-a>)
or
[start a new discussion on GitHub](<https://github.com/google-gemini/gemini-cli/discussions/new?category=q-a>)


</details>
