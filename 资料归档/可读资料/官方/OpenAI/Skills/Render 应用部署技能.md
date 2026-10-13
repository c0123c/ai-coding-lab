# Render 应用部署技能

说明分析项目、选择部署方式、生成和验证 render.yaml、提交及查看服务，包含连接与故障恢复参考。

- 来源：OpenAI／Skills
- 资料类型：存档文档
- 存档版本：`49f948faa925`。
- 文档时间：源码提交（存档版）：[2026-02-02](<https://github.com/openai/skills/commit/bf9e226541ec801750547acfa8cf761b7c878f27>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/skills/blob/main/skills/.curated/render-deploy/SKILL.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Skills/Render 应用部署技能.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
name: render-deploy
description: Deploy applications to Render by analyzing codebases, generating render.yaml Blueprints, and providing Dashboard deeplinks. Use when the user wants to deploy, host, publish, or set up their application on Render's cloud platform.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 部署到渲染

渲染支持 **Git 支持的 ** 服务和 **预构建的 Docker 映像 ** 服务。

该技能涵盖 **Git 支持的 ** 流程：
1. **蓝图方法** - 为基础设施即代码部署生成render.yaml
2. **直接创建** - 通过MCP工具即时创建服务

蓝图还可以使用 `runtime: image` 运行 ** 预构建的 Docker 映像 **，但 `render.yaml` 仍然必须存在于 Git 存储库中。

如果没有 Git 远程，请停止并要求用户：
- Create/push Git 远程（如果只需要蓝图，可以是最小的），或者
- 使用渲染器 Dashboard/API 部署预构建的 Docker 映像（MCP 无法创建映像支持的服务）。

## 先决条件

- 当沙箱阻止部署网络调用时，请使用 `sandbox_permissions=require_escalated` 重新运行。
- 部署可能需要几分钟时间。使用适当的超时值。

## 何时使用此技能

当用户想要执行以下操作时激活此技能：
- 将应用程序部署到渲染
- 创建 render.yaml 蓝图文件
- 为其项目设置渲染部署
- 在 Render 的云平台上托管或发布他们的应用程序
- 创建数据库、cron 作业或其他渲染资源

## 快乐之路（新用户）

在深入分析之前使用这个简短的提示序列可以减少摩擦：
1. 询问他们是否想要从 Git 存储库或预构建的 Docker 映像进行部署。
2. 询问 Render 是否应提供应用程序所需的一切（基于用户描述中可能的内容），还是仅在用户自带基础设施时提供应用程序。如果依赖关系不清楚，请进行简短的跟进，以确认他们是否需要数据库、工作人员、cron 或其他服务。

然后继续执行下面适当的方法。

## 选择您的源路径

**Git 存储库路径：** 蓝图和直接创建都需要。存储库必须推送到 GitHub、GitLab 或 Bitbucket。

**预构建的 Docker 映像路径：** 通过图像支持服务由渲染支持。这是**而不是MCP支持的**；使用 Dashboard/API. 询问：
- 图片URL（注册表+标签）
- 注册表授权（如果是私有的）
- 服务类型（web/worker）和端口

如果用户选择 Docker 映像，请引导他们进入渲染仪表板映像部署流程或要求他们添加 Git 远程（这样您就可以使用带有 `runtime: image` 的蓝图）。

## 选择您的部署方法 (Git Repo)

这两种方法都需要将 Git 存储库推送到 GitHub、GitLab 或 Bitbucket。 （如果使用 `runtime: image`，则存储库可以很小，并且仅包含 `render.yaml`。）

|方法|最适合|优点|
|--------|----------|------|
|**蓝图**|多服务应用程序，IaC 工作流程|版本控制、可重复、支持复杂的设置|
|**直接创建**|单一服务，快速部署|即时创建，无需render.yaml文件|

### 方法选择启发式

除非用户请求特定方法，否则默认使用此决策规则。首先分析代码库；仅询问部署意图是否不清楚（例如，DB、workers、cron）。

**当 ALL 为 true 时，使用直接创建 (MCP)：**
- 单一服务（一个网络应用程序或一个静态网站）
- 没有单独的 worker/cron 服务
- 没有附加数据库或键值
- 仅简单环境变量（无共享环境组）
如果此路径适合并且尚未配置 MCP，请停止并引导 MCP 设置，然后再继续。

**当ANY为真时使用蓝图：**
- 多种服务（Web + Worker、API + 前端等）
- 需要数据库、Redis/Key 值或其他数据存储
- Cron 作业、后台工作人员或私人服务
- 您想要可复制的 IaC 或 render.yaml 提交给存储库
- 需要一致配置的 Monorepo 或多环境设置

如果不确定，请快速提出澄清问题，但为了安全起见，默认使用蓝图。对于单一服务，强烈建议通过 MCP 直接创建，并根据需要指导 MCP 设置。

## 先决条件检查

开始部署时，请按顺序验证这些要求：

**1。确认源路径（Git vs Docker）**

如果使用基于 Git 的方法（蓝图或直接创建），则必须将存储库推送到 GitHub/GitLab/Bitbucket. 引用预构建映像的蓝图仍然需要具有 `render.yaml` 的 Git 存储库。

```bash
git remote -v
```

- 如果不存在远程，请停止并要求用户将create/push远程**或**切换到Docker映像部署。

**2。检查 MCP 工具可用性（首选单一服务）**

MCP工具提供最佳体验。通过尝试检查是否可用：
```
list_services()
```

如果有MCP工具可用，则大多数操作可以跳过CLI安装。

**3。检查渲染 CLI 安装（用于蓝图验证）**
```bash
render --version
```
如果未安装，请提供安装：
- macOS: `brew install render`
- Linux/macOS: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/main/bin/install.sh | sh`

**4。 MCP 设置（如果未配置 MCP）**

如果 `list_services()` 由于未配置 MCP 而失败，请询问他们是否要设置 MCP（首选）或继续使用 CLI 后备。如果他们选择 MCP，请询问他们正在使用哪种 AI 工具，然后提供下面的匹配说明。始终使用他们的 API 键。

### Cursor

引导用户完成以下步骤：

1) 获取渲染 API 密钥：
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) 将其添加到 `~/.cursor/mcp.json`（替换 `<YOUR_API_KEY>`）：
```json
{
  "mcpServers": {
    "render": {
      "url": "https://mcp.render.com/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_API_KEY>"
      }
    }
  }
}
```

3) 重新启动Cursor，然后重试`list_services()`。

### Claude Code

引导用户完成以下步骤：

1) 获取渲染 API 密钥：
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) 将 MCP 服务器添加为 Claude Code（替换 `<YOUR_API_KEY>`）：
```bash
claude mcp add --transport http render https://mcp.render.com/mcp --header "Authorization: Bearer <YOUR_API_KEY>"
```

3) 重新启动Claude Code，然后重试`list_services()`。

### Codex

引导用户完成以下步骤：

1) 获取渲染 API 密钥：
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) 将其设置在 shell 中：
```bash
export RENDER_API_KEY="<YOUR_API_KEY>"
```

3) 添加带有 Codex CLI 的 MCP 服务器：
```bash
codex mcp add render --url https://mcp.render.com/mcp --bearer-token-env-var RENDER_API_KEY
```

4) 重新启动Codex，然后重试`list_services()`。

### 其他工具

如果用户使用另一个 AI 应用程序，请将他们引导至渲染 MCP 文档，了解该工具的设置步骤和安装方法。

### 工作空间选择

配置 MCP 后，让用户设置活动渲染工作区，提示如下：

```
Set my Render workspace to [WORKSPACE_NAME]
```

**5。检查身份验证（仅CLI后备）**

如果 MCP 不可用，请改用 CLI 并验证您是否可以访问您的帐户：
```bash
# Check if user is logged in (use -o json for non-interactive mode)
render whoami -o json
```

如果`render whoami`失败或返回空数据，则CLI未通过验证。 CLI 不会总是自动提示，因此明确提示用户进行身份验证：

如果两者均未配置，请询问用户他们更喜欢哪种方法：
- **API密钥（CLI）**：`export RENDER_API_KEY="rnd_xxxxx"`（从https://dashboard.render.com/u/*/settings#api-keys获取）
- **登录**：`render login`（打开OAuth浏览器）

**6。检查工作空间上下文**

验证活动工作区：
```
get_selected_workspace()
```

或通过CLI：
```bash
render workspace current -o json
```

列出可用的工作区：
```
list_workspaces()
```

如果用户需要切换工作空间，则必须通过仪表板或 CLI (`render workspace set`) 进行操作。

满足先决条件后，继续进行部署工作流程。

---

# 方法1：蓝图部署（推荐用于复杂应用程序）

## 蓝图工作流程

### 步骤 1：分析代码库

分析代码库以确定 framework/runtime、构建和启动命令、所需的环境变量、数据存储和端口绑定。使用 [references/codebase-analysis.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/codebase-analysis.md>) 中的详细检查表。

### 步骤2：生成render.yaml

按照蓝图规范创建一个 `render.yaml` 蓝图文件。

规格齐全：【[references/blueprint-spec.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/blueprint-spec.md>)】

**要点：**
- 除非用户另有指定，否则始终使用 `plan: free`
- 包含应用程序所需的 ALL 环境变量
- 用 `sync: false` 标记秘密（用户在仪表板中填写这些）
- 使用适当的服务类型：`web`、`worker`、`cron`、`static` 或 `pserv`
- 使用适当的运行时：[references/runtimes.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/runtimes.md>)

**基本结构：**
```yaml
services:
  - type: web
    name: my-app
    runtime: node
    plan: free
    buildCommand: npm ci
    startCommand: npm start
    envVars:
      - key: DATABASE_URL
        fromDatabase:
          name: postgres
          property: connectionString
      - key: JWT_SECRET
        sync: false  # User fills in Dashboard

databases:
  - name: postgres
    databaseName: myapp_db
    plan: free
```

**服务类型：**
- `web`：HTTP 服务、API、Web 应用程序（可公开访问）
- `worker`：后台作业处理器（不可公开访问）
- `cron`：按 cron 计划运行的计划任务
- `static`：静态站点（HTML/CSS/JS通过CDN提供）
- `pserv`：私人服务（仅限内部，同一账户内）

服务类型详细信息：[references/service-types.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/service-types.md>)
运行时选项：[references/runtimes.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/runtimes.md>)
模板示例：[assets/](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/assets>)

### 步骤 2.5：立即执行后续步骤（始终提供）

创建 `render.yaml` 后，始终为用户提供简短、明确的清单，并在 CLI 可用时立即运行验证：
1. **验证（CLI）**：运行`render whoami -o json`（如果未登录，则运行`render login`或设置`RENDER_API_KEY`）
2. **验证（推荐）**：运行`render blueprints validate`
   - 如果未安装 CLI，请主动安装并提供命令。
3. **提交+推送**：`git add render.yaml && git commit -m "Add Render deployment configuration" && git push origin main`
4. **打开仪表板**：使用蓝图深层链接并在出现提示时完成 Git OAuth
5. **填充秘密**：设置标记为`sync: false`的环境变量
6. **部署**：单击“应用”并监控部署

### 步骤 3：验证配置

在部署之前验证 render.yaml 文件以捕获错误。如果安装了【CLI】，则直接运行命令；仅在缺少 CLI 时提示用户：

```bash
render whoami -o json  # Ensure CLI is authenticated (won't always prompt)
render blueprints validate
```

在继续之前修复所有验证错误。常见问题：
- 缺少必填字段（`name`、`type`、`runtime`）
- 无效的运行时值
- YAML 语法不正确
- 无效的环境变量引用

配置指南：【[references/configuration-guide.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/configuration-guide.md>)】

### 步骤 4：提交并推送

**IMPORTANT:** 在部署之前，您必须将 `render.yaml` 文件合并到存储库中。

确保 `render.yaml` 文件已提交并推送到您的 Git 远程：

```bash
git add render.yaml
git commit -m "Add Render deployment configuration"
git push origin main
```

如果还没有 Git 远程，请在此停止并引导用户创建 GitHub/GitLab/Bitbucket 存储库，将其添加为 `origin`，并在继续之前推送。

**为什么这很重要：**仪表板深度链接将从您的存储库中读取render.yaml。如果文件未合并和推送，渲染将找不到配置，部署将失败。

在继续下一步之前，请验证该文件是否位于远程存储库中。

### 步骤5：生成深层链接

获取 Git 存储库 URL：

```bash
git remote get-url origin
```

这将从您的 Git 提供商返回一个 URL。 **如果URL为SSH格式，则将其转换为HTTPS:**

|SSH 格式|HTTPS 格式|
|------------|--------------|
| `git@github.com:user/repo.git` | `https://github.com/user/repo` |
| `git@gitlab.com:user/repo.git` | `https://gitlab.com/user/repo` |
| `git@bitbucket.org:user/repo.git` | `https://bitbucket.org/user/repo` |

**转换模式：**将`git@<host>:`替换为`https://<host>/`，并删除`.git`后缀。

使用 HTTPS 存储库 URL 格式化仪表板深度链接：
```
https://dashboard.render.com/blueprint/new?repo=<REPOSITORY_URL>
```

示例：
```
https://dashboard.render.com/blueprint/new?repo=https://github.com/username/repo-name
```

### 步骤6：引导用户

**CRITICAL:** 在单击深层链接之前，请确保用户已将 render.yaml 文件合并并推送到其存储库。如果该文件不在存储库中，则渲染无法读取蓝图配置，并且部署将失败。

通过以下说明向用户提供深层链接：

1. **验证 render.yaml 已合并** - 确认该文件存在于 GitHub/GitLab/Bitbucket 上的存储库中
2. 单击深层链接打开渲染仪表板
3. 如果出现提示，请完成 Git 提供程序 OAuth
4. 为蓝图命名（或使用 render.yaml 中的默认值）
5. 填写秘密环境变量（标有`sync: false`）
6. 检查服务和数据库配置
7. 点击“应用”进行部署

部署将自动开始。用户可以在渲染仪表板中监控进度。

### 步骤 7：验证部署

用户通过仪表板部署后，验证一切正常。

**通过MCP检查部署状态：**
```
list_deploys(serviceId: "<service-id>", limit: 1)
```
查找 `status: "live"` 以确认部署成功。

**检查运行时错误（部署后等待 2-3 分钟）：**
```
list_logs(resource: ["<service-id>"], level: ["error"], limit: 20)
```

**检查服务运行状况指标：**
```
get_metrics(
  resourceId: "<service-id>",
  metricTypes: ["http_request_count", "cpu_usage", "memory_usage"]
)
```

如果发现错误，请继续执行下面的 **部署后验证和基本分类 ** 部分。

---

# 方法2：直接创建服务（快速单服务部署）

对于没有基础设施即代码的简单部署，请直接通过 MCP 工具创建服务。

## 何时使用直接创建

- 单一 Web 服务或静态站点
- 快速原型或演示
- 当您的存储库中不需要 render.yaml 文件时
- 将数据库或 cron 作业添加到现有项目

## 直接创建的先决条件

**存储库必须推送到 Git 提供程序。** Render 克隆您的存储库以构建和部署服务。

```bash
git remote -v  # Verify remote exists
git push origin main  # Ensure code is pushed
```

支持的提供商：GitHub、GitLab、Bitbucket

如果不存在远程，请停止并要求用户 create/push 远程或切换到 Docker 映像部署。

**注意：** MCP 不支持创建镜像支持的服务。使用 Dashboard/API 进行预构建的 Docker 映像部署。

## 直接创建工作流程

使用下面的简明步骤，并参阅 [references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>) 以获取完整的 MCP 命令示例和后续配置。

### 步骤 1：分析代码库
使用 [references/codebase-analysis.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/codebase-analysis.md>) 确定运行时、build/start 命令、环境变量和数据存储。

### 步骤2：通过MCP创建资源
创建服务（Web 或静态）以及任何所需的数据库或键值存储。参见[references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>)。

如果 MCP 返回有关缺少 Git 凭据或存储库访问权限的错误，请停止并引导用户在渲染仪表板中连接其 Git 提供程序，然后重试。

### 步骤3：配置环境变量
创建后通过 MCP 添加所需的环境变量。参见[references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>)。

提醒用户，如果他们不想通过 MCP 传递机密，可以在仪表板中设置机密。

### 步骤 4：验证部署
检查部署状态、日志和指标。参见[references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>)。

---

有关服务发现、配置详细信息、快速命令和常见问题，请参阅[references/deployment-details.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/deployment-details.md>)。

---

# 部署后验证和基本分类（所有方法）

保持简短且可重复。如果任何检查失败，请在重新部署之前修复它。

1. 确认最新部署是 `live` 并提供流量
2. 点击运行状况端点（或根）并验证 200 响应
3. 扫描最近的错误日志以获取清晰的故障签名
4. 验证所需的环境变量和端口绑定 (`0.0.0.0:$PORT`)

详细清单和命令：[references/post-deploy-checks.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/post-deploy-checks.md>)

如果服务无法启动或运行状况检查超时，请使用基本分类指南：
[references/troubleshooting-basics.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/troubleshooting-basics.md>)

## 故障排除

### 升级网络访问

如果部署由于网络问题（超时、DNS 错误、连接重置）而失败，请使用升级的权限重新运行部署（使用 `sandbox_permissions=require_escalated`）。当沙箱网络阻止出站请求时，部署需要升级网络访问。

对用户的指导示例：

```
The deploy needs escalated network access to deploy to Render. I can rerun the command with escalated permissions—want me to proceed?
```

可选：如果您需要更深入的诊断（metrics/DB checks/error 目录），建议安装 
`render-debug` 技能。核心部署流程不需要它。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Deploy to Render

Render supports **Git-backed** services and **prebuilt Docker image** services.

This skill covers **Git-backed** flows:
1. **Blueprint Method** - Generate render.yaml for Infrastructure-as-Code deployments
2. **Direct Creation** - Create services instantly via MCP tools

Blueprints can also run a **prebuilt Docker image** by using `runtime: image`, but the `render.yaml` still must live in a Git repo.

If there is no Git remote, stop and ask the user to either:
- Create/push a Git remote (can be minimal if only the Blueprint is needed), or
- Use the Render Dashboard/API to deploy a prebuilt Docker image (MCP cannot create image-backed services).

## Prerequisites

- When sandboxing blocks the deployment network calls, rerun with `sandbox_permissions=require_escalated`.
- The deployment might take a few minutes. Use appropriate timeout values.

## When to Use This Skill

Activate this skill when users want to:
- Deploy an application to Render
- Create a render.yaml Blueprint file
- Set up Render deployment for their project
- Host or publish their application on Render's cloud platform
- Create databases, cron jobs, or other Render resources

## Happy Path (New Users)

Use this short prompt sequence before deep analysis to reduce friction:
1. Ask whether they want to deploy from a Git repo or a prebuilt Docker image.
2. Ask whether Render should provision everything the app needs (based on what seems likely from the user's description) or only the app while they bring their own infra. If dependencies are unclear, ask a short follow-up to confirm whether they need a database, workers, cron, or other services.

Then proceed with the appropriate method below.

## Choose Your Source Path

**Git Repo Path:** Required for both Blueprint and Direct Creation. The repo must be pushed to GitHub, GitLab, or Bitbucket.

**Prebuilt Docker Image Path:** Supported by Render via image-backed services. This is **not** supported by MCP; use the Dashboard/API. Ask for:
- Image URL (registry + tag)
- Registry auth (if private)
- Service type (web/worker) and port

If the user chooses a Docker image, guide them to the Render Dashboard image deploy flow or ask them to add a Git remote (so you can use a Blueprint with `runtime: image`).

## Choose Your Deployment Method (Git Repo)

Both methods require a Git repository pushed to GitHub, GitLab, or Bitbucket. (If using `runtime: image`, the repo can be minimal and only contain `render.yaml`.)

| Method | Best For | Pros |
|--------|----------|------|
| **Blueprint** | Multi-service apps, IaC workflows | Version controlled, reproducible, supports complex setups |
| **Direct Creation** | Single services, quick deployments | Instant creation, no render.yaml file needed |

### Method Selection Heuristic

Use this decision rule by default unless the user requests a specific method. Analyze the codebase first; only ask if deployment intent is unclear (e.g., DB, workers, cron).

**Use Direct Creation (MCP) when ALL are true:**
- Single service (one web app or one static site)
- No separate worker/cron services
- No attached databases or Key Value
- Simple env vars only (no shared env groups)
If this path fits and MCP isn't configured yet, stop and guide MCP setup before proceeding.

**Use Blueprint when ANY are true:**
- Multiple services (web + worker, API + frontend, etc.)
- Databases, Redis/Key Value, or other datastores are required
- Cron jobs, background workers, or private services
- You want reproducible IaC or a render.yaml committed to the repo
- Monorepo or multi-env setup that needs consistent configuration

If unsure, ask a quick clarifying question, but default to Blueprint for safety. For a single service, strongly prefer Direct Creation via MCP and guide MCP setup if needed.

## Prerequisites Check

When starting a deployment, verify these requirements in order:

**1. Confirm Source Path (Git vs Docker)**

If using Git-based methods (Blueprint or Direct Creation), the repo must be pushed to GitHub/GitLab/Bitbucket. Blueprints that reference a prebuilt image still require a Git repo with `render.yaml`.

```bash
git remote -v
```

- If no remote exists, stop and ask the user to create/push a remote **or** switch to Docker image deploy.

**2. Check MCP Tools Availability (Preferred for Single-Service)**

MCP tools provide the best experience. Check if available by attempting:
```
list_services()
```

If MCP tools are available, you can skip CLI installation for most operations.

**3. Check Render CLI Installation (for Blueprint validation)**
```bash
render --version
```
If not installed, offer to install:
- macOS: `brew install render`
- Linux/macOS: `curl -fsSL https://raw.githubusercontent.com/render-oss/cli/main/bin/install.sh | sh`

**4. MCP Setup (if MCP isn't configured)**

If `list_services()` fails because MCP isn't configured, ask whether they want to set up MCP (preferred) or continue with the CLI fallback. If they choose MCP, ask which AI tool they're using, then provide the matching instructions below. Always use their API key.

### Cursor

Walk the user through these steps:

1) Get a Render API key:
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) Add this to `~/.cursor/mcp.json` (replace `<YOUR_API_KEY>`):
```json
{
  "mcpServers": {
    "render": {
      "url": "https://mcp.render.com/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_API_KEY>"
      }
    }
  }
}
```

3) Restart Cursor, then retry `list_services()`.

### Claude Code

Walk the user through these steps:

1) Get a Render API key:
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) Add the MCP server with Claude Code (replace `<YOUR_API_KEY>`):
```bash
claude mcp add --transport http render https://mcp.render.com/mcp --header "Authorization: Bearer <YOUR_API_KEY>"
```

3) Restart Claude Code, then retry `list_services()`.

### Codex

Walk the user through these steps:

1) Get a Render API key:
```
https://dashboard.render.com/u/*/settings#api-keys
```

2) Set it in their shell:
```bash
export RENDER_API_KEY="<YOUR_API_KEY>"
```

3) Add the MCP server with the Codex CLI:
```bash
codex mcp add render --url https://mcp.render.com/mcp --bearer-token-env-var RENDER_API_KEY
```

4) Restart Codex, then retry `list_services()`.

### Other Tools

If the user is on another AI app, direct them to the Render MCP docs for that tool's setup steps and install method.

### Workspace Selection

After MCP is configured, have the user set the active Render workspace with a prompt like:

```
Set my Render workspace to [WORKSPACE_NAME]
```

**5. Check Authentication (CLI fallback only)**

If MCP isn't available, use the CLI instead and verify you can access your account:
```bash
# Check if user is logged in (use -o json for non-interactive mode)
render whoami -o json
```

If `render whoami` fails or returns empty data, the CLI is not authenticated. The CLI won't always prompt automatically, so explicitly prompt the user to authenticate:

If neither is configured, ask user which method they prefer:
- **API Key (CLI)**: `export RENDER_API_KEY="rnd_xxxxx"` (Get from https://dashboard.render.com/u/*/settings#api-keys)
- **Login**: `render login` (Opens browser for OAuth)

**6. Check Workspace Context**

Verify the active workspace:
```
get_selected_workspace()
```

Or via CLI:
```bash
render workspace current -o json
```

To list available workspaces:
```
list_workspaces()
```

If user needs to switch workspaces, they must do so via Dashboard or CLI (`render workspace set`).

Once prerequisites are met, proceed with deployment workflow.

---

# Method 1: Blueprint Deployment (Recommended for Complex Apps)

## Blueprint Workflow

### Step 1: Analyze Codebase

Analyze the codebase to determine framework/runtime, build and start commands, required env vars, datastores, and port binding. Use the detailed checklists in [references/codebase-analysis.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/codebase-analysis.md>).

### Step 2: Generate render.yaml

Create a `render.yaml` Blueprint file following the Blueprint specification.

Complete specification: [references/blueprint-spec.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/blueprint-spec.md>)

**Key Points:**
- Always use `plan: free` unless user specifies otherwise
- Include ALL environment variables the app needs
- Mark secrets with `sync: false` (user fills these in Dashboard)
- Use appropriate service type: `web`, `worker`, `cron`, `static`, or `pserv`
- Use appropriate runtime: [references/runtimes.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/runtimes.md>)

**Basic Structure:**
```yaml
services:
  - type: web
    name: my-app
    runtime: node
    plan: free
    buildCommand: npm ci
    startCommand: npm start
    envVars:
      - key: DATABASE_URL
        fromDatabase:
          name: postgres
          property: connectionString
      - key: JWT_SECRET
        sync: false  # User fills in Dashboard

databases:
  - name: postgres
    databaseName: myapp_db
    plan: free
```

**Service Types:**
- `web`: HTTP services, APIs, web applications (publicly accessible)
- `worker`: Background job processors (not publicly accessible)
- `cron`: Scheduled tasks that run on a cron schedule
- `static`: Static sites (HTML/CSS/JS served via CDN)
- `pserv`: Private services (internal only, within same account)

Service type details: [references/service-types.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/service-types.md>)
Runtime options: [references/runtimes.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/runtimes.md>)
Template examples: [assets/](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/assets>)

### Step 2.5: Immediate Next Steps (Always Provide)

After creating `render.yaml`, always give the user a short, explicit checklist and run validation immediately when the CLI is available:
1. **Authenticate (CLI)**: run `render whoami -o json` (if not logged in, run `render login` or set `RENDER_API_KEY`)
2. **Validate (recommended)**: run `render blueprints validate`
   - If the CLI isn't installed, offer to install it and provide the command.
3. **Commit + push**: `git add render.yaml && git commit -m "Add Render deployment configuration" && git push origin main`
4. **Open Dashboard**: Use the Blueprint deeplink and complete Git OAuth if prompted
5. **Fill secrets**: Set env vars marked `sync: false`
6. **Deploy**: Click "Apply" and monitor the deploy

### Step 3: Validate Configuration

Validate the render.yaml file to catch errors before deployment. If the CLI is installed, run the commands directly; only prompt the user if the CLI is missing:

```bash
render whoami -o json  # Ensure CLI is authenticated (won't always prompt)
render blueprints validate
```

Fix any validation errors before proceeding. Common issues:
- Missing required fields (`name`, `type`, `runtime`)
- Invalid runtime values
- Incorrect YAML syntax
- Invalid environment variable references

Configuration guide: [references/configuration-guide.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/configuration-guide.md>)

### Step 4: Commit and Push

**IMPORTANT:** You must merge the `render.yaml` file into your repository before deploying.

Ensure the `render.yaml` file is committed and pushed to your Git remote:

```bash
git add render.yaml
git commit -m "Add Render deployment configuration"
git push origin main
```

If there is no Git remote yet, stop here and guide the user to create a GitHub/GitLab/Bitbucket repo, add it as `origin`, and push before continuing.

**Why this matters:** The Dashboard deeplink will read the render.yaml from your repository. If the file isn't merged and pushed, Render won't find the configuration and deployment will fail.

Verify the file is in your remote repository before proceeding to the next step.

### Step 5: Generate Deeplink

Get the Git repository URL:

```bash
git remote get-url origin
```

This will return a URL from your Git provider. **If the URL is SSH format, convert it to HTTPS:**

| SSH Format | HTTPS Format |
|------------|--------------|
| `git@github.com:user/repo.git` | `https://github.com/user/repo` |
| `git@gitlab.com:user/repo.git` | `https://gitlab.com/user/repo` |
| `git@bitbucket.org:user/repo.git` | `https://bitbucket.org/user/repo` |

**Conversion pattern:** Replace `git@<host>:` with `https://<host>/` and remove `.git` suffix.

Format the Dashboard deeplink using the HTTPS repository URL:
```
https://dashboard.render.com/blueprint/new?repo=<REPOSITORY_URL>
```

Example:
```
https://dashboard.render.com/blueprint/new?repo=https://github.com/username/repo-name
```

### Step 6: Guide User

**CRITICAL:** Ensure the user has merged and pushed the render.yaml file to their repository before clicking the deeplink. If the file isn't in the repository, Render cannot read the Blueprint configuration and deployment will fail.

Provide the deeplink to the user with these instructions:

1. **Verify render.yaml is merged** - Confirm the file exists in your repository on GitHub/GitLab/Bitbucket
2. Click the deeplink to open Render Dashboard
3. Complete Git provider OAuth if prompted
4. Name the Blueprint (or use default from render.yaml)
5. Fill in secret environment variables (marked with `sync: false`)
6. Review services and databases configuration
7. Click "Apply" to deploy

The deployment will begin automatically. Users can monitor progress in the Render Dashboard.

### Step 7: Verify Deployment

After the user deploys via Dashboard, verify everything is working.

**Check deployment status via MCP:**
```
list_deploys(serviceId: "<service-id>", limit: 1)
```
Look for `status: "live"` to confirm successful deployment.

**Check for runtime errors (wait 2-3 minutes after deploy):**
```
list_logs(resource: ["<service-id>"], level: ["error"], limit: 20)
```

**Check service health metrics:**
```
get_metrics(
  resourceId: "<service-id>",
  metricTypes: ["http_request_count", "cpu_usage", "memory_usage"]
)
```

If errors are found, proceed to the **Post-deploy verification and basic triage** section below.

---

# Method 2: Direct Service Creation (Quick Single-Service Deployments)

For simple deployments without Infrastructure-as-Code, create services directly via MCP tools.

## When to Use Direct Creation

- Single web service or static site
- Quick prototypes or demos
- When you don't need a render.yaml file in your repo
- Adding databases or cron jobs to existing projects

## Prerequisites for Direct Creation

**Repository must be pushed to a Git provider.** Render clones your repository to build and deploy services.

```bash
git remote -v  # Verify remote exists
git push origin main  # Ensure code is pushed
```

Supported providers: GitHub, GitLab, Bitbucket

If no remote exists, stop and ask the user to create/push a remote or switch to Docker image deploy.

**Note:** MCP does not support creating image-backed services. Use the Dashboard/API for prebuilt Docker image deploys.

## Direct Creation Workflow

Use the concise steps below, and refer to [references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>) for full MCP command examples and follow-on configuration.

### Step 1: Analyze Codebase
Use [references/codebase-analysis.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/codebase-analysis.md>) to determine runtime, build/start commands, env vars, and datastores.

### Step 2: Create Resources via MCP
Create the service (web or static) and any required databases or key-value stores. See [references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>).

If MCP returns an error about missing Git credentials or repo access, stop and guide the user to connect their Git provider in the Render Dashboard, then retry.

### Step 3: Configure Environment Variables
Add required env vars via MCP after creation. See [references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>).

Remind the user that secrets can be set in the Dashboard if they prefer not to pass them via MCP.

### Step 4: Verify Deployment
Check deploy status, logs, and metrics. See [references/direct-creation.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/direct-creation.md>).

---

For service discovery, configuration details, quick commands, and common issues, see [references/deployment-details.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/deployment-details.md>).

---

# Post-deploy verification and basic triage (All Methods)

Keep this short and repeatable. If any check fails, fix it before redeploying.

1. Confirm the latest deploy is `live` and serving traffic
2. Hit the health endpoint (or root) and verify a 200 response
3. Scan recent error logs for a clear failure signature
4. Verify required env vars and port binding (`0.0.0.0:$PORT`)

Detailed checklist and commands: [references/post-deploy-checks.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/post-deploy-checks.md>)

If the service fails to start or health checks time out, use the basic triage guide:
[references/troubleshooting-basics.md](<https://github.com/openai/skills/blob/49f948faa9258a0c61caceaf225e179651397431/skills/.curated/render-deploy/references/troubleshooting-basics.md>)

## Troubleshooting

### Escalated Network Access

If deployment fails due to network issues (timeouts, DNS errors, connection resets), rerun the deploy with escalated permissions (use `sandbox_permissions=require_escalated`). The deploy requires escalated network access when sandbox networking blocks outbound requests.

Example guidance to the user:

```
The deploy needs escalated network access to deploy to Render. I can rerun the command with escalated permissions—want me to proceed?
```

Optional: If you need deeper diagnostics (metrics/DB checks/error catalog), suggest installing the
`render-debug` skill. It is not required for the core deploy flow.


</details>
