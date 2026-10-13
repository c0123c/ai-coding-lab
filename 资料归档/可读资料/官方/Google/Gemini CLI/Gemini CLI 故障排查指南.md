# Gemini CLI 故障排查指南

围绕认证、常见报错、退出码和调试提供检查路径，并说明如何查找或提交相关 Issue。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-06-18](<https://github.com/google-gemini/gemini-cli/commit/93844dfa10f6d71edc09be40dfde205edfbcc939>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/resources/troubleshooting.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/resources/troubleshooting.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 故障排除指南

本指南提供常见问题的解决方案和调试技巧，包括
主题：

- 身份验证或登录错误
- 常见问题 (FAQ)
- 调试技巧
- 现有 GitHub 与您的问题类似的问题或创建新问题

## 身份验证或登录错误

- **错误：
  `You must be a named user on your organization's Gemini Code Assist Standard edition subscription to use this service. Please contact your administrator to request an entitlement to Gemini Code Assist Standard edition.`**

  - **原因：**如果Gemini CLI检测到
`GOOGLE_CLOUD_PROJECT` 或 `GOOGLE_CLOUD_PROJECT_ID` 环境变量是
 定义的。设置这些变量会强制进行组织订阅检查。
 如果您使用的是个人 Google 帐户，未
 链接到组织订阅，这可能会出现问题。

  - **解决方案：**

    - **个人用户：**取消设置`GOOGLE_CLOUD_PROJECT`并
`GOOGLE_CLOUD_PROJECT_ID` 环境变量。检查并从 shell 配置文件（例如，`.bashrc`、
 `.zshrc`）和任何 `.env` 文件中删除这些 
 变量。如果这不能解决问题，请尝试使用不同的 Google 帐户
。

    - **组织用户：**联系您的Google云管理员
添加到您组织的 Gemini Code Assist 订阅。

- **错误：
  `Failed to sign in. Message: Your current account is not eligible... because it is not currently available in your location.`**

  - **原因：** Gemini CLI 目前不支持您的位置。对于一个完整的
支持的位置列表，请参阅以下页面：
    - Gemini 个人代码协助：
[可用地点](<https://developers.google.com/gemini-code-assist/resources/available-locations#americas>)

- **错误：`Failed to sign in. Message: Request contains an invalid argument`**

  - **原因：** 拥有 Google 工作区帐户或 Google 云帐户的用户
与其 Gmail 帐户关联的用户可能无法激活 Google 代码协助计划的免费 
 层级。
  - **解决方案：** 对于 Google 云帐户，您可以通过设置来解决此问题
    `GOOGLE_CLOUD_PROJECT` 到你的项目 ID。或者，您可以获得
    Gemini API 钥匙来自
    [Google AI 工作室](<http://aistudio.google.com/app/apikey>)，这也
    包括一个单独的免费套餐。

- **错误：`UNABLE_TO_GET_ISSUER_CERT_LOCALLY` 或
  `unable to get local issuer certificate`**
  - **原因：**您所在的公司网络可能带有拦截的防火墙
并检查 SSL/TLS 流量。这通常需要 Node.js 信任自定义根 CA
 证书。
  - **解决方案：**首先尝试设置`NODE_USE_SYSTEM_CA`；如果没有的话
解决该问题，设置`NODE_EXTRA_CA_CERTS`。
    - 设置`NODE_USE_SYSTEM_CA=1`环境变量告诉Node.js使用
操作系统的本机证书存储（通常已安装公司 
 证书）。
      - 示例：`export NODE_USE_SYSTEM_CA=1`（Windows PowerShell：
        `$env:NODE_USE_SYSTEM_CA=1`)
    - 设置`NODE_EXTRA_CA_CERTS`环境变量为绝对路径
您的公司根 CA 证书文件。
      - 示例：`export NODE_EXTRA_CA_CERTS=/path/to/your/corporate-ca.crt`
（Windows PowerShell：
 `$env:NODE_EXTRA_CA_CERTS="C:\path\to\your\corporate-ca.crt"`）

## 常见错误信息及解决方案

- **启动 MCP 服务器时出现错误：`EADDRINUSE`（地址已在使用中）。**

  - **原因：**另一个进程已在使用MCP服务器所在的端口
试图绑定到。
  - **解决方案：** 停止正在使用该端口的其他进程，或者
将 MCP 服务器配置为使用不同的端口。

- **错误：未找到命令（尝试运行 Gemini CLI 时
  `gemini`).**

  - **原因：**Gemini CLI未正确安装或不在您的计算机中
系统的`PATH`。
  - **解决方案：**更新取决于您的安装方式Gemini CLI：
    - 如果您全局安装了 `gemini`，请检查您的 `npm` 全局二进制文件
目录位于您的`PATH`中。您可以使用命令
 `npm install -g @google/gemini-cli@latest`更新Gemini CLI。
    - 如果您从源代码运行 `gemini`，请确保您使用正确的
命令来调用它（例如，`node packages/cli/dist/index.js ...`）。
 要更新 Gemini CLI，请从存储库中提取最新更改，然后使用命令 `npm run build` 重建 
。

- **错误：`MODULE_NOT_FOUND`或导入错误。**

  - **原因：**依赖项未正确安装，或者项目尚未安装
已建成。
  - **解决方案：**
    1.  运行 `npm install` 以确保所有依赖项都存在。
    2.  运行`npm run build`编译项目。
    3.  使用 `npm run start` 验证构建是否成功完成。

- **错误：“不允许操作”、“权限被拒绝”或类似错误。**

  - **原因：**启用沙箱时，Gemini CLI可能会尝试操作
受沙箱配置限制，例如在项目目录或系统临时目录之外写入
。
  - **解决方案：**参考[配置：沙盒](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>)
文档以获取更多信息，包括如何自定义沙箱
配置。

- **Gemini CLI 未在“CI”环境中以交互模式运行**

  - **问题：如果出现以下情况，** Gemini CLI 不会进入交互模式（不出现提示）
以 `CI_` 开头的环境变量（例如 `CI_TOKEN`）设置为 
。这是因为底层 UI
 框架使用的 `is-in-ci` 包会检测这些变量并假定非交互式 CI
 环境。
  - **原因：** `is-in-ci` 包检查是否存在 `CI`，
`CONTINUOUS_INTEGRATION`，或任何带有 `CI_` 前缀的环境变量。
 当找到其中任何一个时，它表示环境是 
 非交互式的，这会阻止 Gemini CLI 在其交互式 
 模式下启动。
  - **解决方案：**如果CLI不需要`CI_`前缀变量
    function, you can temporarily unset it for the command. For example,
    `env -u CI_TOKEN gemini`

- **DEBUG 模式无法在项目 .env 文件中工作 **

  - **问题：** 在项目的 `.env` 文件中设置 `DEBUG=true` 无法启用
gemini-cli 的调试模式。
  - **原因：** `DEBUG` 和 `DEBUG_MODE` 变量被自动排除
来自项目 `.env` 文件，以防止干扰 gemini-cli 行为。
  - **解决方案：**改用`.gemini/.env`文件，或者配置
`settings.json` 中的 `advanced.excludedEnvVars` 设置可排除较少的 
 变量。

- **警告：`npm WARN deprecated node-domexception@1.0.0`或
`npm WARN deprecated glob` 在 install/update** 期间
  - **问题：** 通过全局安装或更新 Gemini CLI 时
`npm install -g @google/gemini-cli` 或 `npm update -g @google/gemini-cli`、
 您可能会看到有关 `node-domexception` 或旧 
 版本的 `glob` 的弃用警告。
  - **原因：**出现这些警告是因为某些依赖项（或其
子依赖项，例如 `google-auth-library`）依赖于较旧的包 
 版本。由于 Gemini CLI 需要 Node.js 20 或更高版本，因此使用平台的 
 本机功能（如本机 `DOMException`），使这些 
 警告纯粹是信息性的。
  - **解决方案：** 这些警告是无害的，可以安全地忽略。你的
安装或更新将成功完成并正常运行
，无需任何操作。

## 退出代码

Gemini CLI使用特定的退出代码来指示终止的原因。此
对于脚本编写和自动化特别有用。

|退出代码|错误类型|描述|
| --------- | -------------------------- | --------------------------------------------------------------------------------------------------- |
| 41        | `FatalAuthenticationError` |身份验证过程中发生错误。|
| 42        | `FatalInputError`          |向 CLI 提供的输入无效或丢失。 （仅限非交互模式）|
| 44        | `FatalSandboxError`        |沙盒环境（例如 Docker、Podman 或 Seatbelt）发生错误。|
| 52        | `FatalConfigError`         |配置文件 (`settings.json`) 无效或包含错误。|
| 53        | `FatalTurnLimitedError`    |已达到会话的最大对话轮数。 （仅限非交互模式）|

## 调试技巧

- **CLI调试：**

  - 使用 `--debug` 标志可获得更详细的输出。在交互模式下，按
F12 查看调试控制台。
  - 检查 CLI 日志，通常在用户特定的配置或缓存中找到
目录。

- **核心调试：**

  - 检查服务器控制台输出中的错误消息或堆栈跟踪。
  - 如果可配置，则增加日志详细程度。例如设置`DEBUG_MODE`
环境变量为 `true` 或 `1`。
  - 如果需要，请使用Node.js调试工具（例如`node --inspect`）
单步执行服务器端代码。

- **工具问题：**

  - 如果特定工具出现故障，请尝试通过运行以下命令来隔离问题
工具执行的命令或操作的最简单版本。
  - 对于 `run_shell_command`，检查该命令是否直接在您的 shell 中运行
首先。
  - 对于 _ 文件系统工具 _，验证路径是否正确并检查
权限。

- **飞行前检查：**
  - 在提交代码之前始终运行 `npm run preflight`。这个可以抓很多
与格式、linting 和类型错误相关的常见问题。

## 现有 GitHub 问题与您的问题相似或正在创建新问题

如果您遇到本文未涵盖的问题 _故障排除
指南_，考虑搜索 Gemini CLI
[问题跟踪器开启 GitHub](<https://github.com/google-gemini/gemini-cli/issues>).
如果您找不到与您的问题类似的问题，请考虑创建一个新问题 GitHub
问题有详细描述。也欢迎请求请求！

<!-- prettier-ignore -->
> [！NOTE]
> 标记为“🔒仅限维护者”的问题保留给项目
> 维护者。我们不会接受与这些问题相关的拉取请求。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Troubleshooting guide

This guide provides solutions to common issues and debugging tips, including
topics on:

- Authentication or login errors
- Frequently asked questions (FAQs)
- Debugging tips
- Existing GitHub Issues similar to yours or creating new Issues

## Authentication or login errors

- **Error:
  `You must be a named user on your organization's Gemini Code Assist Standard edition subscription to use this service. Please contact your administrator to request an entitlement to Gemini Code Assist Standard edition.`**

  - **Cause:** This error might occur if Gemini CLI detects the
    `GOOGLE_CLOUD_PROJECT` or `GOOGLE_CLOUD_PROJECT_ID` environment variable is
    defined. Setting these variables forces an organization subscription check.
    This might be an issue if you are using an individual Google account not
    linked to an organizational subscription.

  - **Solution:**

    - **Individual Users:** Unset the `GOOGLE_CLOUD_PROJECT` and
      `GOOGLE_CLOUD_PROJECT_ID` environment variables. Check and remove these
      variables from your shell configuration files (for example, `.bashrc`,
      `.zshrc`) and any `.env` files. If this doesn't resolve the issue, try
      using a different Google account.

    - **Organizational Users:** Contact your Google Cloud administrator to be
      added to your organization's Gemini Code Assist subscription.

- **Error:
  `Failed to sign in. Message: Your current account is not eligible... because it is not currently available in your location.`**

  - **Cause:** Gemini CLI does not currently support your location. For a full
    list of supported locations, see the following pages:
    - Gemini Code Assist for individuals:
      [Available locations](<https://developers.google.com/gemini-code-assist/resources/available-locations#americas>)

- **Error: `Failed to sign in. Message: Request contains an invalid argument`**

  - **Cause:** Users with Google Workspace accounts or Google Cloud accounts
    associated with their Gmail accounts may not be able to activate the free
    tier of the Google Code Assist plan.
  - **Solution:** For Google Cloud accounts, you can work around this by setting
    `GOOGLE_CLOUD_PROJECT` to your project ID. Alternatively, you can obtain the
    Gemini API key from
    [Google AI Studio](<http://aistudio.google.com/app/apikey>), which also
    includes a separate free tier.

- **Error: `UNABLE_TO_GET_ISSUER_CERT_LOCALLY` or
  `unable to get local issuer certificate`**
  - **Cause:** You may be on a corporate network with a firewall that intercepts
    and inspects SSL/TLS traffic. This often requires a custom root CA
    certificate to be trusted by Node.js.
  - **Solution:** First try setting `NODE_USE_SYSTEM_CA`; if that does not
    resolve the issue, set `NODE_EXTRA_CA_CERTS`.
    - Set the `NODE_USE_SYSTEM_CA=1` environment variable to tell Node.js to use
      the operating system's native certificate store (where corporate
      certificates are typically already installed).
      - Example: `export NODE_USE_SYSTEM_CA=1` (Windows PowerShell:
        `$env:NODE_USE_SYSTEM_CA=1`)
    - Set the `NODE_EXTRA_CA_CERTS` environment variable to the absolute path of
      your corporate root CA certificate file.
      - Example: `export NODE_EXTRA_CA_CERTS=/path/to/your/corporate-ca.crt`
        (Windows PowerShell:
        `$env:NODE_EXTRA_CA_CERTS="C:\path\to\your\corporate-ca.crt"`)

## Common error messages and solutions

- **Error: `EADDRINUSE` (Address already in use) when starting an MCP server.**

  - **Cause:** Another process is already using the port that the MCP server is
    trying to bind to.
  - **Solution:** Either stop the other process that is using the port or
    configure the MCP server to use a different port.

- **Error: Command not found (when attempting to run Gemini CLI with
  `gemini`).**

  - **Cause:** Gemini CLI is not correctly installed or it is not in your
    system's `PATH`.
  - **Solution:** The update depends on how you installed Gemini CLI:
    - If you installed `gemini` globally, check that your `npm` global binary
      directory is in your `PATH`. You can update Gemini CLI using the command
      `npm install -g @google/gemini-cli@latest`.
    - If you are running `gemini` from source, ensure you are using the correct
      command to invoke it (for example, `node packages/cli/dist/index.js ...`).
      To update Gemini CLI, pull the latest changes from the repository, and
      then rebuild using the command `npm run build`.

- **Error: `MODULE_NOT_FOUND` or import errors.**

  - **Cause:** Dependencies are not installed correctly, or the project hasn't
    been built.
  - **Solution:**
    1.  Run `npm install` to ensure all dependencies are present.
    2.  Run `npm run build` to compile the project.
    3.  Verify that the build completed successfully with `npm run start`.

- **Error: "Operation not permitted", "Permission denied", or similar.**

  - **Cause:** When sandboxing is enabled, Gemini CLI may attempt operations
    that are restricted by your sandbox configuration, such as writing outside
    the project directory or system temp directory.
  - **Solution:** Refer to the [Configuration: Sandboxing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/sandbox.md>)
    documentation for more information, including how to customize your sandbox
    configuration.

- **Gemini CLI is not running in interactive mode in "CI" environments**

  - **Issue:** Gemini CLI does not enter interactive mode (no prompt appears) if
    an environment variable starting with `CI_` (for example, `CI_TOKEN`) is
    set. This is because the `is-in-ci` package, used by the underlying UI
    framework, detects these variables and assumes a non-interactive CI
    environment.
  - **Cause:** The `is-in-ci` package checks for the presence of `CI`,
    `CONTINUOUS_INTEGRATION`, or any environment variable with a `CI_` prefix.
    When any of these are found, it signals that the environment is
    non-interactive, which prevents Gemini CLI from starting in its interactive
    mode.
  - **Solution:** If the `CI_` prefixed variable is not needed for the CLI to
    function, you can temporarily unset it for the command. For example,
    `env -u CI_TOKEN gemini`

- **DEBUG mode not working from project .env file**

  - **Issue:** Setting `DEBUG=true` in a project's `.env` file doesn't enable
    debug mode for gemini-cli.
  - **Cause:** The `DEBUG` and `DEBUG_MODE` variables are automatically excluded
    from project `.env` files to prevent interference with gemini-cli behavior.
  - **Solution:** Use a `.gemini/.env` file instead, or configure the
    `advanced.excludedEnvVars` setting in your `settings.json` to exclude fewer
    variables.

- **Warning: `npm WARN deprecated node-domexception@1.0.0` or
  `npm WARN deprecated glob` during install/update**
  - **Issue:** When installing or updating Gemini CLI globally via
    `npm install -g @google/gemini-cli` or `npm update -g @google/gemini-cli`,
    you might see deprecation warnings regarding `node-domexception` or old
    versions of `glob`.
  - **Cause:** These warnings occur because some dependencies (or their
    sub-dependencies, like `google-auth-library`) rely on older package
    versions. Since Gemini CLI requires Node.js 20 or higher, the platform's
    native features (like the native `DOMException`) are used, making these
    warnings purely informational.
  - **Solution:** These warnings are harmless and can be safely ignored. Your
    installation or update will complete successfully and function properly
    without any action required.

## Exit codes

Gemini CLI uses specific exit codes to indicate the reason for termination. This
is especially useful for scripting and automation.

| Exit Code | Error Type                 | Description                                                                                         |
| --------- | -------------------------- | --------------------------------------------------------------------------------------------------- |
| 41        | `FatalAuthenticationError` | An error occurred during the authentication process.                                                |
| 42        | `FatalInputError`          | Invalid or missing input was provided to the CLI. (non-interactive mode only)                       |
| 44        | `FatalSandboxError`        | An error occurred with the sandboxing environment (for example, Docker, Podman, or Seatbelt).       |
| 52        | `FatalConfigError`         | A configuration file (`settings.json`) is invalid or contains errors.                               |
| 53        | `FatalTurnLimitedError`    | The maximum number of conversational turns for the session was reached. (non-interactive mode only) |

## Debugging tips

- **CLI debugging:**

  - Use the `--debug` flag for more detailed output. In interactive mode, press
    F12 to view the debug console.
  - Check the CLI logs, often found in a user-specific configuration or cache
    directory.

- **Core debugging:**

  - Check the server console output for error messages or stack traces.
  - Increase log verbosity if configurable. For example, set the `DEBUG_MODE`
    environment variable to `true` or `1`.
  - Use Node.js debugging tools (for example, `node --inspect`) if you need to
    step through server-side code.

- **Tool issues:**

  - If a specific tool is failing, try to isolate the issue by running the
    simplest possible version of the command or operation the tool performs.
  - For `run_shell_command`, check that the command works directly in your shell
    first.
  - For _file system tools_, verify that paths are correct and check the
    permissions.

- **Pre-flight checks:**
  - Always run `npm run preflight` before committing code. This can catch many
    common issues related to formatting, linting, and type errors.

## Existing GitHub issues similar to yours or creating new issues

If you encounter an issue that was not covered here in this _Troubleshooting
guide_, consider searching Gemini CLI
[Issue tracker on GitHub](<https://github.com/google-gemini/gemini-cli/issues>).
If you can't find an issue similar to yours, consider creating a new GitHub
Issue with a detailed description. Pull requests are also welcome!

<!-- prettier-ignore -->
> [!NOTE]
> Issues tagged as "🔒Maintainers only" are reserved for project
> maintainers. We will not accept pull requests related to these issues.


</details>
