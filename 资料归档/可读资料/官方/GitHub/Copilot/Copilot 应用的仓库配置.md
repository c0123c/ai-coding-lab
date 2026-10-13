# Copilot 应用的仓库配置

说明配置位置、信任审阅、项目指令、脚本、服务器检测和自动化行为，以及运行时变量和旧配置兼容。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/github/docs/commit/51e43a92f0651fc5a7c5ab395474c57a27c3cf72>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/reference/github-copilot-app-reference/repository-configuration.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 应用的仓库配置.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Repository configuration for the GitHub Copilot app
shortTitle: Repository configuration
allowTitleToDifferFromFilename: true
intro: 'Define repository-specific instructions, scripts, and automation behavior for the {% data variables.copilot.github_copilot_app %}.'
product: '{% data reusables.gated-features.github-app %}<br><a href="https://github.com/features/ai/github-app" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Download {% data variables.copilot.github_copilot_app %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: reference
category:
  - Author and optimize with Copilot
  - Configure Copilot
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

在存储库中使用 `.github/github-app.yml` 来定义 {% data variables.copilot.github_copilot_app %} 对于该项目的行为方式。

您还可以在应用程序 UI 中编辑这些项目设置。如果 `.github/github-app.yml` 已存在，则 UI 更改将写回该文件。如果尚不存在，您可以从应用程序中的当前项目设置创建它。

## 关于配置文件位置

在以下位置创建文件：

```text copy
.github/github-app.yml
```

该应用程序还支持旧文件名 `.github/copilot-desktop.yml` 以实现向后兼容性。

有关基于 UI- 的自定义步骤，请参阅 [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/customize-github-copilot-app>)。

## 检查并信任配置

当应用程序从存储库检测到配置时，它不会应用文件中的存储库说明、脚本或其他设置，直到您查看并接受该配置。这可以防止您运行其他贡献者添加的命令或应用设置。您通过应用程序 UI 创建或更新的配置将自动受到信任。

> [!WARNING]
> 在接受存储库配置之前，请检查每个配置的命令及其运行的依赖项。脚本及其子进程接收本文稍后描述的 {% data variables.product.github %} 凭据，因此切勿将它们配置为记录或保留这些环境变量。

如果文件在应用程序外部发生更改（包括对空格或注释的更改），您必须在应用程序应用更新的配置之前查看并接受更新的配置。在您接受当前版本之前，应用程序将继续使用之前在应用程序中配置的项目设置。

## 配置示例

```yaml copy
instructions: |
  Use bun instead of npm.

scripts:
  - name: Setup
    command: bun install
    triggers:
      - session.create
  - name: Run
    command: bun run dev
  - name: Archive cleanup
    command: rm -rf node_modules
    triggers:
      - session.archive

server_ready_pattern: '(?i)Local:\s+(https?://\S+)'
auto_open_in_browser: true

automation:
  auto_issue_session: true
  remote_control: false
```

## 配置指令和脚本

### `instructions`

使用 `instructions` 将特定于存储库的指导添加到项目中会话的系统提示中。如果您还在应用程序中配置了全局指令，则首先应用全局指令，然后应用项目指令。

### `scripts`

使用 `scripts` 定义应用程序中显示的命令，并且可以手动运行或在特定触发器上运行。

每个脚本项支持：

* `name` (`string`)：UI中的显示名称。
* `command` (`string`)：要运行的命令。
* `triggers`（`string[]`，可选）：自动运行脚本的事件。

没有 `triggers` 的脚本是手动的。

### 触发值

在文件中使用规范触发器值：

* `session.create`
* `session.archive`

该应用程序在解析现有文件时还接受这些旧别名：

* `workspace.create`（`session.create` 的别名）
* `workspace.archive`（`session.archive` 的别名）

当触发的脚本运行时，`COPILOT_SCRIPT_TRIGGER` 设置为规范值：

* `session.create`
* `session.archive`

## 配置服务器检测和浏览器行为

### `server_ready_pattern`

`server_ready_pattern` 是一个正则表达式，用于检测运行脚本何时启动服务器。

模式使用 Rust 的 `regex` 包支持的语法。有关语法详细信息，请参阅 crate 文档中的 [Syntax](<https://docs.rs/regex/1/regex/#syntax>)。如果模式无效，应用程序将使用其默认服务器检测模式。

对检测到的 URL 或端口使用第一个捕获组。应用程序读取捕获组 `1`：

* 如果捕获是 URL（`http://...` 或 `https://...`），则使用 URL。
* 如果捕获的只是端口号（例如 `3000`），应用程序会将其转换为 `http://localhost:3000`。

### `auto_open_in_browser`

如果 `auto_open_in_browser` 是 `true`，应用程序会在集成浏览器中打开检测到的运行 URL。如果省略此字段，则有效默认值为 `true`。

## 配置自动化行为

在`automation`下设置自动化选项：

* `automation.auto_issue_session` (`boolean`) 控制应用程序是否自动启动具有问题上下文的会话。如果省略，则有效默认值为 `true`。
* `automation.remote_control` (`boolean`) 控制是否可以从 {% data variables.product.github %} Web 界面或 {% data variables.product.prodname_mobile %} 访问会话。如果省略，则有效默认值为 `false`。

如果您的 {% data variables.product.prodname_copilot_short %} 席位来自组织，则适用的“在云中存储本地会话”策略必须设置为“查看和控制”才能使用远程控制。即使 `automation.remote_control` 为 `true`，企业管理的 `remoteControl` 设置也可以进一步限制远程控制。有关更多信息，请参阅[AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-remote-control>)和[AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings>)。

## 脚本的运行时环境变量

脚本使用这些应用程序提供的环境变量运行：

|变量|描述|
| --- | --- |
| `COPILOT_WORKSPACE_NAME` |当前工作区名称。|
| `COPILOT_WORKSPACE_PATH` |工作区的绝对路径。|
| `COPILOT_ROOT_PATH` |项目根结帐的绝对路径。|
| `COPILOT_DEFAULT_BRANCH` |项目默认分支。|
| `COPILOT_PORT` |当前工作区上下文的应用程序 WebSocket 端口。|
| `COPILOT_SCRIPT_TRIGGER` |启动脚本的触发器（仅为触发脚本设置）。|
| `GH_TOKEN` |所选 {% data variables.product.github %} 账户的令牌。|
| `GH_HOST` |所选 {% data variables.product.github %} 帐户的主机。|
| `COPILOT_GH_ACCOUNT_*` |每个登录帐户（包括所选帐户）的主机和帐户特定令牌。|

对于每个 `COPILOT_GH_ACCOUNT_*` 变量，应用程序会小写主机和登录名，保持 ASCII 字母和数字不变，并将所有其他 UTF-8 字节替换为其下划线包围的大写十六进制值。变量名称使用格式`COPILOT_GH_ACCOUNT_<HOST>_<LOGIN>`。例如，`github.com`上的`alice`的令牌是`COPILOT_GH_ACCOUNT_github_2E_com_alice`，`ghe-example.com`上的`user`的令牌是`COPILOT_GH_ACCOUNT_ghe_2D_example_2E_com_user`。

## 旧版兼容性

为了向后兼容，应用程序仍然可以解析旧的基于对象的 `scripts` 形状：

```yaml copy
scripts:
  setup: bun install
  run: bun run dev
  archive: rm -rf node_modules
```

在这个传统形状中：

* `setup` 映射到带有创建触发器的脚本。
* `archive` 映射到具有存档触发器的脚本。
* `run` 映射到手动脚本条目，可以是单个命令字符串或 `{ name, command }` 对象列表。


## 原文对照

<details>
<summary>展开完整原文</summary>

Use `.github/github-app.yml` in your repository to define how the {% data variables.copilot.github_copilot_app %} should behave for that project.

You can also edit these project settings in the app UI. If `.github/github-app.yml` already exists, UI changes are written back to that file. If it does not exist yet, you can create it from current project settings in the app.

## About the config file location

Create the file at:

```text copy
.github/github-app.yml
```

The app also supports the legacy filename `.github/copilot-desktop.yml` for backward compatibility.

For UI-based customization steps, see [AUTOTITLE](<https://github.com/copilot/how-tos/github-copilot-app/customize-github-copilot-app>).

## Review and trust the configuration

When the app detects a configuration from the repository, it does not apply repository instructions, scripts, or other settings from the file until you review and accept the configuration. This protects you from running commands or applying settings that were added by another contributor. Configurations that you create or update through the app UI are trusted automatically.

> [!WARNING]
> Before accepting a repository configuration, review every configured command and the dependencies it runs. Scripts and their child processes receive the {% data variables.product.github %} credentials described later in this article, so never configure them to log or persist these environment variables.

If the file changes outside the app, including changes to whitespace or comments, you must review and accept the updated configuration before the app applies it. Until you accept the current version, the app continues to use the project settings that were previously configured in the app.

## Example configuration

```yaml copy
instructions: |
  Use bun instead of npm.

scripts:
  - name: Setup
    command: bun install
    triggers:
      - session.create
  - name: Run
    command: bun run dev
  - name: Archive cleanup
    command: rm -rf node_modules
    triggers:
      - session.archive

server_ready_pattern: '(?i)Local:\s+(https?://\S+)'
auto_open_in_browser: true

automation:
  auto_issue_session: true
  remote_control: false
```

## Configure instructions and scripts

### `instructions`

Use `instructions` to add repository-specific guidance to the system prompt for sessions in the project. If you also configure global instructions in the app, the global instructions are applied first, followed by the project instructions.

### `scripts`

Use `scripts` to define commands that appear in the app and can run manually or on specific triggers.

Each script item supports:

* `name` (`string`): Display name in the UI.
* `command` (`string`): Command to run.
* `triggers` (`string[]`, optional): Events that auto-run the script.

Scripts without `triggers` are manual.

### Trigger values

Use canonical trigger values in your file:

* `session.create`
* `session.archive`

The app also accepts these legacy aliases when parsing existing files:

* `workspace.create` (alias for `session.create`)
* `workspace.archive` (alias for `session.archive`)

When a triggered script runs, `COPILOT_SCRIPT_TRIGGER` is set to the canonical value:

* `session.create`
* `session.archive`

## Configure server detection and browser behavior

### `server_ready_pattern`

`server_ready_pattern` is a regular expression used to detect when a run script has started a server.

Patterns use the syntax supported by Rust's `regex` crate. For syntax details, see [Syntax](<https://docs.rs/regex/1/regex/#syntax>) in the crate documentation. If the pattern is invalid, the app uses its default server detection pattern.

Use a first capture group for the detected URL or port. The app reads capture group `1`:

* If the capture is a URL (`http://...` or `https://...`), the URL is used.
* If the capture is only a port number (for example `3000`), the app converts it to `http://localhost:3000`.

### `auto_open_in_browser`

If `auto_open_in_browser` is `true`, the app opens the detected run URL in the integrated browser. If this field is omitted, the effective default is `true`.

## Configure automation behavior

Set automation options under `automation`:

* `automation.auto_issue_session` (`boolean`) controls whether the app automatically starts a session with issue context. If omitted, the effective default is `true`.
* `automation.remote_control` (`boolean`) controls whether sessions can be accessed from the {% data variables.product.github %} web interface or {% data variables.product.prodname_mobile %}. If omitted, the effective default is `false`.

If your {% data variables.product.prodname_copilot_short %} seat comes from an organization, the applicable "Store local sessions in the Cloud" policy must be set to "View and control" for remote control to be available. Enterprise-managed `remoteControl` settings can further restrict remote control even when `automation.remote_control` is `true`. For more information, see [AUTOTITLE](<https://github.com/copilot/concepts/agents/copilot-cli/about-remote-control>) and [AUTOTITLE](<https://github.com/copilot/reference/enterprise-administrators/enterprise-managed-settings>).

## Runtime environment variables for scripts

Scripts run with these app-provided environment variables:

| Variable | Description |
| --- | --- |
| `COPILOT_WORKSPACE_NAME` | Current workspace name. |
| `COPILOT_WORKSPACE_PATH` | Absolute path to the workspace. |
| `COPILOT_ROOT_PATH` | Absolute path to the project root checkout. |
| `COPILOT_DEFAULT_BRANCH` | Project default branch. |
| `COPILOT_PORT` | App WebSocket port for the current workspace context. |
| `COPILOT_SCRIPT_TRIGGER` | Trigger that launched the script (only set for triggered scripts). |
| `GH_TOKEN` | Token for the selected {% data variables.product.github %} account. |
| `GH_HOST` | Host for the selected {% data variables.product.github %} account. |
| `COPILOT_GH_ACCOUNT_*` | Host- and account-specific tokens for every signed-in account, including the selected account. |

For each `COPILOT_GH_ACCOUNT_*` variable, the app lowercases the host and login, leaves ASCII letters and digits unchanged, and replaces every other UTF-8 byte with its uppercase hexadecimal value surrounded by underscores. The variable name uses the format `COPILOT_GH_ACCOUNT_<HOST>_<LOGIN>`. For example, the token for `alice` on `github.com` is `COPILOT_GH_ACCOUNT_github_2E_com_alice`, and the token for `user` on `ghe-example.com` is `COPILOT_GH_ACCOUNT_ghe_2D_example_2E_com_user`.

## Legacy compatibility

For backward compatibility, the app can still parse the older object-based `scripts` shape:

```yaml copy
scripts:
  setup: bun install
  run: bun run dev
  archive: rm -rf node_modules
```

In this legacy shape:

* `setup` maps to a script with the create trigger.
* `archive` maps to a script with the archive trigger.
* `run` maps to manual script entries and can be either a single command string or a list of `{ name, command }` objects.


</details>
