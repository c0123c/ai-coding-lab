# 在 Gemini CLI 中使用 Gemini 3 系列

介绍所收录版本中的模型开放、使用限制、回退、容量错误和路由设置，并列出管理员及用户启用步骤。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2026-04-10](<https://github.com/google-gemini/gemini-cli/commit/e09410b6e5b7c56222c6583e8f028a35d623c764>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/get-started/gemini-3.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/docs/get-started/gemini-3.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Gemini 3 Pro 和 Gemini 3 在 Gemini CLI 上闪光

了解如何在 Gemini CLI 上使用 Gemini 3 Pro 和 Gemini 3 Flash。

<!-- prettier-ignore -->
> [！NOTE]
> Gemini 3.1 专业预览版正在推出。要确定您是否有权
> 访问Gemini3.1，请使用`/model`命令并选择**手动**。如果您
> 有权访问，您将看到`gemini-3.1-pro-preview`。
>
> 如果您有权访问Gemini 3.1，则当
> 选择**自动时，它将包含在模型路由中（Gemini 3）**。您还可以直接使用 `-m` 标志启动 Gemini 3.1 模型 
> ：
>
> ```
> gemini -m gemini-3.1-pro-preview
> ```
>
> 了解有关[模型](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>)和
> [模型路由](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model-routing.md>)的更多信息。

## 如何开始使用 Gemini CLI 上的 Gemini 3

首先将 Gemini CLI 升级到最新版本：

```bash
npm install -g @google/gemini-cli@latest
```

如果您的版本是0.21.1或更高版本：

1. 运行`/model`。
2. 选择**自动(Gemini3)**。

更多信息请参见【[Gemini CLI】型号选择【](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>)】。

### 使用限制和回退

当您达到 Gemini 3 Pro 每日使用限制时，Gemini CLI 会告诉您。
当您遇到该限制时，您可以选择切换到 Gemini
2.5 Pro、升级到更高限制或停止。当您的使用
限制重置并且Gemini 3 Pro可以再次使用时，您还会收到通知。

<!-- prettier-ignore -->
> [！TIP]
> 想要升级以获得更高的限制？要比较订阅
> 选项并找到适合您需求的配额，请参阅我们的
> [计划页面](<https://geminicli.com/plans/>)。

同样，当您达到 Gemini 2.5 Pro 的每日使用限制时，您会看到 
 一条消息，提示回退到 Gemini 2.5 Flash。

### 容量错误

Gemini 3 Pro 型号有时可能会过载。发生这种情况时，
Gemini CLI 会要求您决定是否要继续尝试 Gemini 3 Pro
 还是回退到 Gemini 2.5 Pro。

<!-- prettier-ignore -->
> [!NOTE]
> **继续尝试**选项使用指数退避，其中当系统繁忙时，Gemini
> CLI在每次重试之间等待更长的时间。如果重试
> 没有立即发生，请等待几分钟以处理请求
> 。

### 模型选择和路由类型

使用 Gemini CLI 时，您可能想要控制请求在模型之间路由的方式 
。默认情况下，Gemini CLI使用**自动**路由。

当使用 Gemini 3 Pro 时，您可能需要使用自动路由或 Pro 路由来 
 管理您的使用限制：

- **自动路由：**自动路由首先确定提示是否涉及
复杂或简单的操作。对于简单的提示，它会自动使用
 Gemini 2.5 Flash。对于复杂的提示，如果启用了Gemini 3 Pro，则使用
 Gemini 3 Pro；否则，它将使用Gemini 2.5 Pro。
- **专业路由：**如果您想确保您的任务被最多人处理
有能力的型号，使用`/model`并选择**Pro**。 Gemini CLI 将优先考虑 
 最强大的可用型号，包括 Gemini 3 Pro（如果已启用）。

要了解有关选择模型和布线的更多信息，请参阅
[Gemini CLI模型选择](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>)。

## 如何在 Gemini 代码辅助上使用 Gemini CLI 启用 Gemini 3

如果您使用的是 Gemini Code Assist Standard 或 Gemini Code Assist Enterprise，
在 Gemini CLI 上启用 Gemini 3 Pro 需要配置您的发布通道。
使用Gemini 3 Pro 将需要两个步骤：管理启用和用户 
 启用。

要了解有关这些设置的更多信息，请参阅
[配置Gemini代码辅助发布通道](<https://developers.google.com/gemini-code-assist/docs/configure-release-channels>)。

### 管理员说明

具有 **Google 云设置管理员 ** 权限的管理员必须遵循 
 以下说明：

- 导航到您与 Gemini CLI 一起使用的 Google 云项目以获取代码
协助。
- Go 至 **Gemini** 的管理员 > **设置 **。
- 在 **本地 IDE 中 Gemini 代码辅助的发布通道 ** 下选择
**预览**。
- 单击**保存更改**。

### 用户说明

管理员启用 **预览**、
后，等待两到三分钟，然后：

- 打开Gemini CLI。
- 使用`/settings`命令。
- 将**预览功能**设置为`true`。

重新启动Gemini CLI，您应该可以访问Gemini 3。

## 后续步骤

如果您需要帮助，我们建议搜索现有的 
[GitHub 问题 ](<https://github.com/google-gemini/gemini-cli/issues>)。如果您
找不到符合您问题的GitHub问题，您可以
[创建一个新问题](<https://github.com/google-gemini/gemini-cli/issues/new/choose>)。
如需评论和反馈，请考虑打开
[GitHub讨论](<https://github.com/google-gemini/gemini-cli/discussions>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Gemini 3 Pro and Gemini 3 Flash on Gemini CLI

Learn about how you can use Gemini 3 Pro and Gemini 3 Flash on Gemini CLI.

<!-- prettier-ignore -->
> [!NOTE]
> Gemini 3.1 Pro Preview is rolling out. To determine whether you have
> access to Gemini 3.1, use the `/model` command and select **Manual**. If you
> have access, you will see `gemini-3.1-pro-preview`.
>
> If you have access to Gemini 3.1, it will be included in model routing when
> you select **Auto (Gemini 3)**. You can also launch the Gemini 3.1 model
> directly using the `-m` flag:
>
> ```
> gemini -m gemini-3.1-pro-preview
> ```
>
> Learn more about [models](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>) and
> [model routing](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model-routing.md>).

## How to get started with Gemini 3 on Gemini CLI

Get started by upgrading Gemini CLI to the latest version:

```bash
npm install -g @google/gemini-cli@latest
```

If your version is 0.21.1 or later:

1. Run `/model`.
2. Select **Auto (Gemini 3)**.

For more information, see [Gemini CLI model selection](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>).

### Usage limits and fallback

Gemini CLI will tell you when you reach your Gemini 3 Pro daily usage limit.
When you encounter that limit, you’ll be given the option to switch to Gemini
2.5 Pro, upgrade for higher limits, or stop. You’ll also be told when your usage
limit resets and Gemini 3 Pro can be used again.

<!-- prettier-ignore -->
> [!TIP]
> Looking to upgrade for higher limits? To compare subscription
> options and find the right quota for your needs, see our
> [Plans page](<https://geminicli.com/plans/>).

Similarly, when you reach your daily usage limit for Gemini 2.5 Pro, you’ll see
a message prompting fallback to Gemini 2.5 Flash.

### Capacity errors

There may be times when the Gemini 3 Pro model is overloaded. When that happens,
Gemini CLI will ask you to decide whether you want to keep trying Gemini 3 Pro
or fallback to Gemini 2.5 Pro.

<!-- prettier-ignore -->
> [!NOTE]
> The **Keep trying** option uses exponential backoff, in which Gemini
> CLI waits longer between each retry, when the system is busy. If the retry
> doesn't happen immediately, wait a few minutes for the request to
> process.

### Model selection and routing types

When using Gemini CLI, you may want to control how your requests are routed
between models. By default, Gemini CLI uses **Auto** routing.

When using Gemini 3 Pro, you may want to use Auto routing or Pro routing to
manage your usage limits:

- **Auto routing:** Auto routing first determines whether a prompt involves a
  complex or simple operation. For simple prompts, it will automatically use
  Gemini 2.5 Flash. For complex prompts, if Gemini 3 Pro is enabled, it will use
  Gemini 3 Pro; otherwise, it will use Gemini 2.5 Pro.
- **Pro routing:** If you want to ensure your task is processed by the most
  capable model, use `/model` and select **Pro**. Gemini CLI will prioritize the
  most capable model available, including Gemini 3 Pro if it has been enabled.

To learn more about selecting a model and routing, refer to
[Gemini CLI Model Selection](<https://github.com/google-gemini/gemini-cli/blob/9b6e0265d16bbd29ca51e33c9e0c01dc4cec5e83/docs/cli/model.md>).

## How to enable Gemini 3 with Gemini CLI on Gemini Code Assist

If you're using Gemini Code Assist Standard or Gemini Code Assist Enterprise,
enabling Gemini 3 Pro on Gemini CLI requires configuring your release channels.
Using Gemini 3 Pro will require two steps: administrative enablement and user
enablement.

To learn more about these settings, refer to
[Configure Gemini Code Assist release channels](<https://developers.google.com/gemini-code-assist/docs/configure-release-channels>).

### Administrator instructions

An administrator with **Google Cloud Settings Admin** permissions must follow
these directions:

- Navigate to the Google Cloud Project you're using with Gemini CLI for Code
  Assist.
- Go to **Admin for Gemini** > **Settings**.
- Under **Release channels for Gemini Code Assist in local IDEs** select
  **Preview**.
- Click **Save changes**.

### User instructions

Wait for two to three minutes after your administrator has enabled **Preview**,
then:

- Open Gemini CLI.
- Use the `/settings` command.
- Set **Preview Features** to `true`.

Restart Gemini CLI and you should have access to Gemini 3.

## Next steps

If you need help, we recommend searching for an existing
[GitHub issue](<https://github.com/google-gemini/gemini-cli/issues>). If you
cannot find a GitHub issue that matches your concern, you can
[create a new issue](<https://github.com/google-gemini/gemini-cli/issues/new/choose>).
For comments and feedback, consider opening a
[GitHub discussion](<https://github.com/google-gemini/gemini-cli/discussions>).


</details>
