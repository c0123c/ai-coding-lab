# Netlify 网站部署技能

串联 CLI 认证、绑定或创建站点、依赖检查、配置及预览或生产部署，并提供错误排查。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-02](<https://github.com/openai/skills/commit/bf9e226541ec801750547acfa8cf761b7c878f27>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/netlify-deploy/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Netlify 网站部署技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: netlify-deploy
description: Deploy web projects to Netlify using the Netlify CLI (`npx netlify`). Use when the user asks to deploy, host, publish, or link a site/repo on Netlify, including preview and production deploys.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# Netlify部署技巧

使用 Netlify CLI 将 Web 项目部署到 Netlify，并智能检测项目配置和部署上下文。

## 概述

此技能通过以下方式自动执行 Netlify 部署：
- 验证 Netlify CLI 身份验证
- 检测项目配置和框架
- 链接到现有网站或创建新网站
- 部署到生产或预览环境

## 先决条件

- **Netlify CLI**：通过 npx 安装（无需全局安装）
- **身份验证**：具有活动登录会话的 Netlify 帐户
- **项目**：当前目录中的有效Web项目
- 当沙箱阻止部署网络调用时，请使用 `sandbox_permissions=require_escalated` 重新运行。
- 部署可能需要几分钟时间。使用适当的超时值。

## 认证模式

该技能使用 ** 预先验证的 Netlify CLI** 方法：

1. 使用`npx netlify status`检查身份验证状态
2. 如果未通过身份验证，则引导用户通过`npx netlify login`
3. 如果无法建立身份验证，则优雅地失败

身份验证使用以下任一方法：
- **基于浏览器的OAuth**（主要）：`netlify login`打开浏览器进行身份验证
- **API键**（替代）：设置`NETLIFY_AUTH_TOKEN`环境变量

## 工作流程

### 1。验证 Netlify CLI 身份验证

检查用户是否登录 Netlify：

```bash
npx netlify status
```

**预期输出模式**：
- ✅ 已验证：显示登录用户电子邮件和网站链接状态
- ❌ 未验证：“未登录任何站点”或验证错误

**如果未通过身份验证**，引导用户：

```bash
npx netlify login
```

这将打开一个用于 OAuth 身份验证的浏览器窗口。等待用户完成登录，然后再次使用`netlify status`进行验证。

**替代：API密钥认证**

如果浏览器身份验证不可用，用户可以设置：

```bash
export NETLIFY_AUTH_TOKEN=your_token_here
```

代币可以在以下位置生成：https://app.netlify.com/user/applications#personal-access-tokens

### 2。检测站点链接状态

根据 `netlify status` 输出，确定：
- **已链接**：站点已连接到 Netlify（显示站点 name/URL）
- **未链接**：需要链接或创建网站

### 3。链接到现有站点或创建新站点

**如果已链接** → 跳至步骤4

**如果未链接**，尝试通过Git远程链接：

```bash
# Check if project is Git-based
git remote show origin

# If Git-based, extract remote URL
# Format: https://github.com/username/repo or git@github.com:username/repo.git

# Try to link by Git remote
npx netlify link --git-remote-url <REMOTE_URL>
```

**如果链接失败**（Netlify 上不存在站点）：

```bash
# Create new site interactively
npx netlify init
```

这将引导用户：
1. 选择team/account
2. 设置站点名称
3. 配置构建设置
4. 如果需要，创建 netlify.toml

### 4。验证依赖关系

部署之前，确保项目依赖项已安装：

```bash
# For npm projects
npm install

# For other package managers, detect and use appropriate command
# yarn install, pnpm install, etc.
```

### 5。部署到 Netlify

根据上下文选择部署类型：

**Preview/Draft 部署**（现有站点的默认设置）：

```bash
npx netlify deploy
```

这将创建一个部署预览，其中包含用于测试的唯一 URL。

**生产部署**（对于新站点或显式生产部署）：

```bash
npx netlify deploy --prod
```

这将部署到实时生产 URL。

**部署流程**：
1. CLI 检测构建设置（来自 netlify.toml 或提示用户）
2. 在本地构建项目
3. 将构建的资产上传到 Netlify
4. 返回部署 URL

### 6。报告结果

部署完成后，向用户报告：
- **部署 URL**：此部署的唯一 URL
- **站点 URL**：生产 URL（如果生产部署）
- **部署日志**：链接到 Netlify 仪表板以获取日志
- **后续步骤**：建议`netlify open`查看网站或仪表板

## 处理netlify.toml

如果 `netlify.toml` 文件存在，CLI 会自动使用它。如果没有，CLI会提示：
- **构建命令**：例如，`npm run build`、`next build`
- **发布目录**：例如`dist`、`build`、`.next`

通用框架默认值：
- **Next.js**：构建命令`npm run build`，发布`.next`
- **React（Vite）**：构建命令`npm run build`，发布`dist`
- **静态HTML**：无构建命令，发布当前目录

如果可能，该技能应从 `package.json` 检测框架并建议适当的设置。

## 完整工作流程示例

```bash
# 1. Check authentication
npx netlify status

# If not authenticated:
npx netlify login

# 2. Link site (if needed)
# Try Git-based linking first
git remote show origin
npx netlify link --git-remote-url https://github.com/user/repo

# If no site exists, create new one:
npx netlify init

# 3. Install dependencies
npm install

# 4. Deploy (preview for testing)
npx netlify deploy

# 5. Deploy to production (when ready)
npx netlify deploy --prod
```

## 错误处理

常见问题及解决方案：

**“未登录”**
→运行`npx netlify login`

**“没有链接站点”**
→运行`npx netlify link`或`npx netlify init`

**“构建失败”**
→检查netlify.toml或CLI提示中的构建命令和发布目录
→验证依赖项是否已安装
→查看构建日志以查找特定错误

**“未找到发布目录”**
→ 验证构建命令是否成功运行
→ 检查发布目录路径是否正确

## 故障排除

### 升级网络访问

如果部署由于网络问题（超时、DNS 错误、连接重置）而失败，请使用升级的权限重新运行部署（使用 `sandbox_permissions=require_escalated`）。当沙箱网络阻止出站请求时，部署需要升级网络访问。

对用户的指导示例：

```
The deploy needs escalated network access to deploy to Netlify. I can rerun the command with escalated permissions—want me to proceed?
```

## 环境变量

对于秘密和配置：

1. 切勿将秘密提交给 Git
2. 在 Netlify 仪表板中设置：站点设置 → 环境变量
3. 通过 `process.env.VARIABLE_NAME` 访问构建

## 温馨提示

- 生产前先使用`netlify deploy`（无`--prod`）进行测试
- 运行 `netlify open` 以在 Netlify 仪表板中查看站点
- 运行`netlify logs`查看函数日志（如果使用Netlify Functions）
- 使用 `netlify dev` 通过 Netlify Functions 进行本地开发

## 参考

- Netlify CLI 文档：https://docs.netlify.com/cli/get-started/
- netlify.toml 参考：https://docs.netlify.com/configure-builds/file-based-configuration/

## 捆绑参考文献（根据需要加载）

- [CLI 命令](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/cli-commands.md>)
- [部署模式](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/deployment-patterns.md>)
- [netlify.toml 指南](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/netlify-toml.md>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Netlify Deployment Skill

Deploy web projects to Netlify using the Netlify CLI with intelligent detection of project configuration and deployment context.

## Overview

This skill automates Netlify deployments by:
- Verifying Netlify CLI authentication
- Detecting project configuration and framework
- Linking to existing sites or creating new ones
- Deploying to production or preview environments

## Prerequisites

- **Netlify CLI**: Installed via npx (no global install required)
- **Authentication**: Netlify account with active login session
- **Project**: Valid web project in current directory
- When sandboxing blocks the deployment network calls, rerun with `sandbox_permissions=require_escalated`.
- The deployment might take a few minutes. Use appropriate timeout values.

## Authentication Pattern

The skill uses the **pre-authenticated Netlify CLI** approach:

1. Check authentication status with `npx netlify status`
2. If not authenticated, guide user through `npx netlify login`
3. Fail gracefully if authentication cannot be established

Authentication uses either:
- **Browser-based OAuth** (primary): `netlify login` opens browser for authentication
- **API Key** (alternative): Set `NETLIFY_AUTH_TOKEN` environment variable

## Workflow

### 1. Verify Netlify CLI Authentication

Check if the user is logged into Netlify:

```bash
npx netlify status
```

**Expected output patterns**:
- ✅ Authenticated: Shows logged-in user email and site link status
- ❌ Not authenticated: "Not logged into any site" or authentication error

**If not authenticated**, guide the user:

```bash
npx netlify login
```

This opens a browser window for OAuth authentication. Wait for user to complete login, then verify with `netlify status` again.

**Alternative: API Key authentication**

If browser authentication isn't available, users can set:

```bash
export NETLIFY_AUTH_TOKEN=your_token_here
```

Tokens can be generated at: https://app.netlify.com/user/applications#personal-access-tokens

### 2. Detect Site Link Status

From `netlify status` output, determine:
- **Linked**: Site already connected to Netlify (shows site name/URL)
- **Not linked**: Need to link or create site

### 3. Link to Existing Site or Create New

**If already linked** → Skip to step 4

**If not linked**, attempt to link by Git remote:

```bash
# Check if project is Git-based
git remote show origin

# If Git-based, extract remote URL
# Format: https://github.com/username/repo or git@github.com:username/repo.git

# Try to link by Git remote
npx netlify link --git-remote-url <REMOTE_URL>
```

**If link fails** (site doesn't exist on Netlify):

```bash
# Create new site interactively
npx netlify init
```

This guides user through:
1. Choosing team/account
2. Setting site name
3. Configuring build settings
4. Creating netlify.toml if needed

### 4. Verify Dependencies

Before deploying, ensure project dependencies are installed:

```bash
# For npm projects
npm install

# For other package managers, detect and use appropriate command
# yarn install, pnpm install, etc.
```

### 5. Deploy to Netlify

Choose deployment type based on context:

**Preview/Draft Deploy** (default for existing sites):

```bash
npx netlify deploy
```

This creates a deploy preview with a unique URL for testing.

**Production Deploy** (for new sites or explicit production deployments):

```bash
npx netlify deploy --prod
```

This deploys to the live production URL.

**Deployment process**:
1. CLI detects build settings (from netlify.toml or prompts user)
2. Builds the project locally
3. Uploads built assets to Netlify
4. Returns deployment URL

### 6. Report Results

After deployment, report to user:
- **Deploy URL**: Unique URL for this deployment
- **Site URL**: Production URL (if production deploy)
- **Deploy logs**: Link to Netlify dashboard for logs
- **Next steps**: Suggest `netlify open` to view site or dashboard

## Handling netlify.toml

If a `netlify.toml` file exists, the CLI uses it automatically. If not, the CLI will prompt for:
- **Build command**: e.g., `npm run build`, `next build`
- **Publish directory**: e.g., `dist`, `build`, `.next`

Common framework defaults:
- **Next.js**: build command `npm run build`, publish `.next`
- **React (Vite)**: build command `npm run build`, publish `dist`
- **Static HTML**: no build command, publish current directory

The skill should detect framework from `package.json` if possible and suggest appropriate settings.

## Example Full Workflow

```bash
# 1. Check authentication
npx netlify status

# If not authenticated:
npx netlify login

# 2. Link site (if needed)
# Try Git-based linking first
git remote show origin
npx netlify link --git-remote-url https://github.com/user/repo

# If no site exists, create new one:
npx netlify init

# 3. Install dependencies
npm install

# 4. Deploy (preview for testing)
npx netlify deploy

# 5. Deploy to production (when ready)
npx netlify deploy --prod
```

## Error Handling

Common issues and solutions:

**"Not logged in"**
→ Run `npx netlify login`

**"No site linked"**
→ Run `npx netlify link` or `npx netlify init`

**"Build failed"**
→ Check build command and publish directory in netlify.toml or CLI prompts
→ Verify dependencies are installed
→ Review build logs for specific errors

**"Publish directory not found"**
→ Verify build command ran successfully
→ Check publish directory path is correct

## Troubleshooting

### Escalated Network Access

If deployment fails due to network issues (timeouts, DNS errors, connection resets), rerun the deploy with escalated permissions (use `sandbox_permissions=require_escalated`). The deploy requires escalated network access when sandbox networking blocks outbound requests.

Example guidance to the user:

```
The deploy needs escalated network access to deploy to Netlify. I can rerun the command with escalated permissions—want me to proceed?
```

## Environment Variables

For secrets and configuration:

1. Never commit secrets to Git
2. Set in Netlify dashboard: Site Settings → Environment Variables
3. Access in builds via `process.env.VARIABLE_NAME`

## Tips

- Use `netlify deploy` (no `--prod`) first to test before production
- Run `netlify open` to view site in Netlify dashboard
- Run `netlify logs` to view function logs (if using Netlify Functions)
- Use `netlify dev` for local development with Netlify Functions

## Reference

- Netlify CLI Docs: https://docs.netlify.com/cli/get-started/
- netlify.toml Reference: https://docs.netlify.com/configure-builds/file-based-configuration/

## Bundled References (Load As Needed)

- [CLI commands](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/cli-commands.md>)
- [Deployment patterns](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/deployment-patterns.md>)
- [netlify.toml guide](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/netlify-deploy/references/netlify-toml.md>)


</details>
