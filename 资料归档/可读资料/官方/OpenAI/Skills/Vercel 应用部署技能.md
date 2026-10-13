# Vercel 应用部署技能

介绍认证、预览部署、生产部署、结果链接与备用路径，帮助发布网站并排查部署问题。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-21](<https://github.com/openai/skills/commit/2c9ebb321958f64aa9414b46e4240901e2b9e1a9>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/vercel-deploy/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Vercel 应用部署技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: vercel-deploy
description: Deploy applications and websites to Vercel. Use when the user requests deployment actions like "deploy my app", "deploy and give me the link", "push this live", or "create a preview deployment".
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Vercel 部署

立即将任何项目部署到 Vercel。 **始终部署为预览版**（不是生产），除非用户明确要求生产。

## 先决条件

- 检查 Vercel CLI 是否已安装 **，且没有 ** 升级权限（例如，`command -v vercel`）。
- 仅当沙箱阻止部署网络调用时才升级实际部署命令 (`sandbox_permissions=require_escalated`)。
- 部署可能需要几分钟时间。使用适当的超时值。

## 快速入门

1. 检查是否安装了 Vercel CLI（此检查不会升级）：

```bash
command -v vercel
```

2. 如果安装了 `vercel`，请运行此命令（超时为 10 分钟）：
```bash
vercel deploy [path] -y
```

**重要提示：**对部署命令使用 10 分钟（600000 毫秒）超时，因为构建可能需要一段时间。

3. 如果未安装 `vercel`，或者 CLI 失败并显示“未找到现有凭据”，请使用下面的后备方法。

## 后备（无身份验证）

如果 CLI 因身份验证错误而失败，请使用部署脚本：

```bash
skill_dir="<path-to-skill>"

# Deploy current directory
bash "$skill_dir/scripts/deploy.sh"

# Deploy specific project
bash "$skill_dir/scripts/deploy.sh" /path/to/project

# Deploy existing tarball
bash "$skill_dir/scripts/deploy.sh" /path/to/project.tgz
```

该脚本处理框架检测、打包和部署。它等待构建完成并返回 JSON 以及 `previewUrl` 和 `claimUrl`。

**告诉用户：**“您的部署已在[previewUrl]准备就绪。在[claimUrl]领取它以管理您的部署。”

## 生产部署

仅当用户明确询问时：
```bash
vercel deploy [path] --prod -y
```

## 输出

向用户显示部署 URL。对于后备部署，还需显示声明 URL。

**请勿**卷曲或获取已部署的URL以验证其是否有效。只需返回链接即可。

## 故障排除

### 升级网络访问

如果部署因网络问题（超时、DNS 错误、连接重置）而失败，请使用升级的权限重新运行实际部署命令（使用 `sandbox_permissions=require_escalated`）。请勿升级 `command -v vercel` 安装检查。当沙箱网络阻止出站请求时，部署需要升级网络访问。

对用户的指导示例：

```
The deploy needs escalated network access to deploy to Vercel. I can rerun the command with escalated permissions—want me to proceed?
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Vercel Deploy

Deploy any project to Vercel instantly. **Always deploy as preview** (not production) unless the user explicitly asks for production.

## Prerequisites

- Check whether the Vercel CLI is installed **without** escalated permissions (for example, `command -v vercel`).
- Only escalate the actual deploy command if sandboxing blocks the deployment network calls (`sandbox_permissions=require_escalated`).
- The deployment might take a few minutes. Use appropriate timeout values.

## Quick Start

1. Check whether the Vercel CLI is installed (no escalation for this check):

```bash
command -v vercel
```

2. If `vercel` is installed, run this (with a 10 minute timeout):
```bash
vercel deploy [path] -y
```

**Important:** Use a 10 minute (600000ms) timeout for the deploy command since builds can take a while.

3. If `vercel` is not installed, or if the CLI fails with "No existing credentials found", use the fallback method below.

## Fallback (No Auth)

If CLI fails with auth error, use the deploy script:

```bash
skill_dir="<path-to-skill>"

# Deploy current directory
bash "$skill_dir/scripts/deploy.sh"

# Deploy specific project
bash "$skill_dir/scripts/deploy.sh" /path/to/project

# Deploy existing tarball
bash "$skill_dir/scripts/deploy.sh" /path/to/project.tgz
```

The script handles framework detection, packaging, and deployment. It waits for the build to complete and returns JSON with `previewUrl` and `claimUrl`.

**Tell the user:** "Your deployment is ready at [previewUrl]. Claim it at [claimUrl] to manage your deployment."

## Production Deploys

Only if user explicitly asks:
```bash
vercel deploy [path] --prod -y
```

## Output

Show the user the deployment URL. For fallback deployments, also show the claim URL.

**Do not** curl or fetch the deployed URL to verify it works. Just return the link.

## Troubleshooting

### Escalated Network Access

If deployment fails due to network issues (timeouts, DNS errors, connection resets), rerun the actual deploy command with escalated permissions (use `sandbox_permissions=require_escalated`). Do not escalate the `command -v vercel` installation check. The deploy requires escalated network access when sandbox networking blocks outbound requests.

Example guidance to the user:

```
The deploy needs escalated network access to deploy to Vercel. I can rerun the command with escalated permissions—want me to proceed?
```


</details>
