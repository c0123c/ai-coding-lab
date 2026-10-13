# Copilot 的内容排除功能

介绍哪些角色可排除文件、功能适用范围、限制和数据处理说明，用于判断敏感代码的访问控制是否生效。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-09-23](<https://github.com/github/docs/commit/fc2c908a7831570a31dd61732aa3ba4645559b1a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/concepts/security-governance-and-network-settings/content-exclusion.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot 的内容排除功能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Content exclusion for GitHub Copilot
shortTitle: Content exclusion
intro: You can prevent {% data variables.product.prodname_copilot_short %} from accessing certain files.
product: '{% data reusables.gated-features.copilot-business-and-enterprise %}'
versions:
  feature: copilot
redirect_from:
  - /copilot/concepts/content-exclusion-for-github-copilot
  - /copilot/concepts/content-exclusion
  - /copilot/concepts/context/content-exclusion
contentType: concepts
category:
  - Manage Copilot for a team
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

## 关于 {% data variables.product.prodname_copilot_short %} 的内容排除

您可以使用内容排除来配置 {% data variables.product.prodname_copilot_short %} 以忽略某些文件。当您从 {% data variables.product.prodname_copilot_short %} 中排除内容时：

* 受影响的文件中将不提供内嵌建议。
* 受影响文件中的内容不会通知其他文件中的内联建议。
* 受影响文件中的内容不会通知 {% data variables.product.prodname_copilot_short %} 的响应。
* 受影响的文件将不会在 {% data variables.product.prodname_copilot_short %} 代码审查中进行审查。

### 谁可以配置内容排除

存储库管理员、组织所有者和企业所有者可以配置内容排除。

{% data reusables.copilot.content-exclusions-scope %}

### 内容排除的可用性

{% data variables.copilot.copilot_business_short %} 和 {% data variables.copilot.copilot_enterprise_short %} 计划提供以下级别的内容排除支持。

|工具|内嵌建议支持|聊天和 agent 支持|
|-------------------------------------------------|:---------------------------------------------:|:----------------------------------------------:|
| {% data variables.product.prodname_vs %}        | {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_vscode %}    | {% octicon "check" aria-label="Supported" %}  |聊天：{% octicon "check" aria-label="Supported" %}<br>编辑：{% octicon "x" aria-label="Not supported" %}<br>Agent：{% octicon "x" aria-label="Not supported" %}|
|JetBrains IDE| {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| Vim/Neovim                                      | {% octicon "check" aria-label="Supported" %}  |不适用|
|Xcode| {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
|日食| {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
|Azure 数据工作室| {% octicon "x" aria-label="Not supported" %}  |不适用|
|{% data variables.product.github %} 网站|不适用| {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_mobile %}    |不适用| {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.github_copilot_app %} |不适用| {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.copilot_cli %}        |不适用| {% octicon "check" aria-label="Supported" %}   |

内容排除也适用于 {% data variables.product.github %} 网站上的 {% data variables.copilot.copilot_code-review_short %}。

> [!NOTE]
> * 内容排除位于 {% data variables.product.github %} 网站上的 {% data variables.release-phases.public_preview %} 和 {% data variables.product.prodname_mobile %} 中，并且可能会发生变化。
> * {% data variables.product.prodname_vscode %} 和其他编辑器中 {% data variables.copilot.copilot_chat_short %} 的编辑和 Agent 模式目前不支持内容排除。

### 内容排除的限制

{% data reusables.copilot.content-exclusion-limitations %}

目前，内容排除不适用于符号链接（symlinks）和位于远程文件系统上的存储库。

### 数据发送至{% data variables.product.prodname_dotcom %}

配置内容排除后，客户端（例如，{% data variables.product.prodname_vscode_shortname %} 的 {% data variables.product.prodname_copilot_short %} 扩展）会将当前存储库 URL 发送到 {% data variables.product.prodname_dotcom %} 服务器，以便服务器可以向客户端返回正确的策略。这些 URL 不会在任何地方记录。

## 进一步阅读

* [AUTOTITLE](<https://github.com/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/configure-content-exclusion/review-changes>)


## 原文对照

<details>
<summary>展开完整原文</summary>

## About content exclusion for {% data variables.product.prodname_copilot_short %}

You can use content exclusion to configure {% data variables.product.prodname_copilot_short %} to ignore certain files. When you exclude content from {% data variables.product.prodname_copilot_short %}:

* Inline suggestions will not be available in the affected files.
* The content in affected files will not inform inline suggestions in other files.
* The content in affected files will not inform {% data variables.product.prodname_copilot_short %}'s responses.
* Affected files will not be reviewed in a {% data variables.product.prodname_copilot_short %} code review.

### Who can configure content exclusion

Repository administrators, organization owners, and enterprise owners can configure content exclusion.

{% data reusables.copilot.content-exclusions-scope %}

### Availability of content exclusion

The {% data variables.copilot.copilot_business_short %} and {% data variables.copilot.copilot_enterprise_short %} plans provide the following level of support for content exclusion.

| Tool                                            | Inline suggestions support                    | Chat and agent support                         |
|-------------------------------------------------|:---------------------------------------------:|:----------------------------------------------:|
| {% data variables.product.prodname_vs %}        | {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_vscode %}    | {% octicon "check" aria-label="Supported" %}  | Chat: {% octicon "check" aria-label="Supported" %}<br>Edit: {% octicon "x" aria-label="Not supported" %}<br>Agent: {% octicon "x" aria-label="Not supported" %} |
| JetBrains IDEs                                  | {% octicon "check" aria-label="Supported" %}  | {% octicon "check" aria-label="Supported" %}   |
| Vim/Neovim                                      | {% octicon "check" aria-label="Supported" %}  | Not applicable                                 |
| Xcode                                           | {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
| Eclipse                                         | {% octicon "check" aria-label="Supported" %}  | {% octicon "x" aria-label="Not supported" %}   |
| Azure Data Studio                               | {% octicon "x" aria-label="Not supported" %}  | Not applicable                                 |
| The {% data variables.product.github %} website | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.product.prodname_mobile %}    | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.github_copilot_app %} | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |
| {% data variables.copilot.copilot_cli %}        | Not applicable                                | {% octicon "check" aria-label="Supported" %}   |

Content exclusions also apply to {% data variables.copilot.copilot_code-review_short %} on the {% data variables.product.github %} website.

> [!NOTE]
> * Content exclusion is in {% data variables.release-phases.public_preview %} on the {% data variables.product.github %} website and in {% data variables.product.prodname_mobile %} and is subject to change.
> * Content exclusion is currently not supported in Edit and Agent modes of {% data variables.copilot.copilot_chat_short %} in {% data variables.product.prodname_vscode %} and other editors.

### Limitations of content exclusion

{% data reusables.copilot.content-exclusion-limitations %}

Currently, content exclusions do not apply to symbolic links (symlinks) and repositories located on remote filesystems.

### Data sent to {% data variables.product.prodname_dotcom %}

After you configure content exclusion, the client (for example, the {% data variables.product.prodname_copilot_short %} extension for {% data variables.product.prodname_vscode_shortname %}) sends the current repository URL to the {% data variables.product.prodname_dotcom %} server so that the server can return the correct policy to the client. These URLs are not logged anywhere.

## Further reading

* [AUTOTITLE](<https://github.com/copilot/how-tos/configure-content-exclusion/exclude-content-from-copilot>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/configure-content-exclusion/review-changes>)


</details>
