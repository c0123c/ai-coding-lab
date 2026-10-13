# 用 Hooks 定制 Agent 工作流

介绍在仓库中创建自动校验规则，执行格式化、Lint 或安全扫描，并说明排查方法，支持持续约束任务执行。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 文档时间：官方文件提交（截至抓取）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://raw.githubusercontent.com/github/docs/main/content/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/use-hooks.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/用 Hooks 定制 Agent 工作流.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Customize agent workflows with hooks
shortTitle: Use hooks
intro: 'Run automated checks—like linting, formatting, or security scans—at key points during agent execution to enforce quality standards.'
versions:
  feature: copilot
contentType: how-tos
category:
  - Configure Copilot
redirect_from:
  - /copilot/how-tos/use-copilot-agents/coding-agent/use-hooks
  - /copilot/how-tos/use-copilot-agents/cloud-agent/use-hooks
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

{% data reusables.copilot.cloud-agent.hooks-intro %}

## 在 {% data variables.product.github %} 上的存储库中创建挂钩

1. 在存储库的 `.github/hooks/` 文件夹中创建一个新的 `NAME.json` 文件（其中 `NAME` 描述该文件的用途）。

   > [!IMPORTANT]
   > 挂钩配置文件 ** 必须存在于存储库的默认分支上 ** 才能由 {% data variables.copilot.copilot_cloud_agent %} 使用。

{% data reusables.copilot.cloud-agent.create-hooks-instructions %}

## 故障排除

{% data reusables.copilot.cloud-agent.troubleshoot-hooks %}

## 进一步阅读

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/hooks-reference>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-cli>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)


## 原文对照

<details>
<summary>展开完整原文</summary>

{% data reusables.copilot.cloud-agent.hooks-intro %}

## Creating a hook in a repository on {% data variables.product.github %}

1. Create a new `NAME.json` file (where `NAME` describes the purpose of the file) in the `.github/hooks/` folder of your repository.

   > [!IMPORTANT]
   > The hooks configuration file **must be present** on your repository's default branch to be used by {% data variables.copilot.copilot_cloud_agent %}.

{% data reusables.copilot.cloud-agent.create-hooks-instructions %}

## Troubleshooting

{% data reusables.copilot.cloud-agent.troubleshoot-hooks %}

## Further reading

* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/reference/hooks-reference>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/concepts/copilot-surfaces/copilot-cli>)
* [AUTOTITLE](<https://raw.githubusercontent.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment>)


</details>
