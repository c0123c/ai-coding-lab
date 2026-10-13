# 在 GitLab CI/CD 中运行 Claude Code

说明如何把 Claude Code 接入 GitLab 流水线，处理 Issue、生成合并请求和执行自动任务；包含安装、认证、配置及生产环境使用建议。

- 来源：Anthropic／Claude Code
- 资料类型：存档文档
- 文档时间：官网现页更新：[2026-08-29](<https://code.claude.com/docs/en/gitlab-ci-cd>)；本地抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://code.claude.com/docs/en/gitlab-ci-cd.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude Code/在 GitLab CI CD 中运行 Claude Code.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

> ## 文档索引
> 在以下位置获取完整的文档索引： https://code.claude.com/docs/llms.txt
> 在进一步探索之前使用此文件来发现所有可用页面。

# Claude Code GitLab CI/CD

> 了解如何使用 GitLab CI/CD 将 Claude Code 集成到您的开发工作流程中



**说明：**

GitLab CI/CD 的 Claude Code 目前处于测试阶段。随着我们改进体验，特性和功能可能会不断发展。

此集成由 GitLab 维护。如需支持，请参阅以下 [GitLab 问题 ](<https://gitlab.com/gitlab-org/gitlab/-/issues/573776>)。




**注释：**

此集成构建在 [Claude Code CLI 和 Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>) 之上，支持在 CI/CD 作业和自定义自动化工作流程中以编程方式使用 Claude。


## 为什么将Claude Code与GitLab一起使用？

* **即时MR创建**：描述您需要什么，Claude提出完整的MR并进行更改和解释
* **自动化实施**：通过单个命令或提及将问题转化为工作代码
* **项目感知**：Claude遵循您的`CLAUDE.md`指南和现有代码模式
* **简单设置**：将一项作业添加到`.gitlab-ci.yml`和一个屏蔽的CI/CD变量
* **企业级**：选择Claude API、Amazon Bedrock 或 Google 云的 Agent 平台来满足数据驻留和采购需求
* **默认安全**：在您的 GitLab 运行器中运行，并获得您的分支保护和批准

## 它是如何运作的

Claude Code 使用 GitLab CI/CD 在隔离作业中运行 AI 任务并通过 MR 提交结果：

1. **事件驱动的编排**：GitLab侦听您选择的触发器（例如，在问题、MR或审阅线程中提及`@claude`的评论）。该作业从线程和存储库收集上下文，根据该输入构建提示，然后运行 ​​Claude Code。

2. **提供程序抽象**：使用适合您环境的提供程序：
   * Claude API (SaaS)
   * Amazon Bedrock（基于IAM-的访问，跨区域选项）
   * Google 云的 Agent 平台（GCP-本机、工作负载身份联合）

3. **沙盒执行**：每次交互都在具有严格网络和文件系统规则的容器中运行。 Claude Code 强制执行工作区范围的权限来限制写入。每个更改都会经过 MR，因此审阅者会看到差异并且批准仍然适用。

选择区域端点以减少延迟并满足数据主权要求，同时使用现有云协议。

## Claude可以做什么？

在 GitLab 管道中，Claude Code 可以：

* 根据问题描述或评论创建和更新 MR
* 分析性能回归并提出优化建议
* 直接在分支中实现功能，然后打开一个MR
* 修复测试或评论发现的错误和回归
* 回复后续评论以迭代请求的更改

## 设置

### 快速设置

最快的开始方法是向您的 `.gitlab-ci.yml` 添加一个最小的作业，并将您的 API 键设置为屏蔽变量。

1. **添加屏蔽的CI/CD变量**
   * Go 至 **设置** → **CI/CD** → **变量**
   * 添加`ANTHROPIC_API_KEY`（根据需要屏蔽、保护）

2. **将 Claude 作业添加到 `.gitlab-ci.yml`**

```yaml theme={null}
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  # Adjust rules to fit how you want to trigger the job:
  # - manual runs
  # - merge request events
  # - web/API triggers when a comment contains '@claude'
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
  script:
    # Optional: start a GitLab MCP server if your setup provides one
    - /bin/gitlab-mcp-server || true
    # Use AI_FLOW_* variables when invoking via web/API triggers with context payloads
    - echo "$AI_FLOW_INPUT for $AI_FLOW_CONTEXT on $AI_FLOW_EVENT"
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR and implement the requested changes'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
```

添加作业和 `ANTHROPIC_API_KEY` 变量后，通过从 **CI/CD** → **Pipelines** 手动运行作业进行测试，或从 MR 触发它，让 Claude 在分支中建议更新并打开MR 如果需要的话。



**注释：**

要在 Amazon Bedrock 或 Google Cloud 的 Agent 平台而不是 Claude API 上运行，请参阅下面的 [与 Amazon Bedrock 和 Google Cloud](<#与-amazon-bedrock-和-google-云一起使用>) 一起使用部分了解身份验证和环境设置。


### 手动设置（建议用于生产）

如果您更喜欢更受控制的设置或需要企业提供商：

1. **配置提供商访问**：
   * **Claude API**：创建并存储 `ANTHROPIC_API_KEY` 作为屏蔽的 CI/CD 变量
   * **Amazon Bedrock**：**配置 GitLab** → **AWS OIDC** 并为 Amazon Bedrock 创建 IAM 角色
   * **Google 云的 Agent 平台 **：**为 GitLab** → **GCP** 配置工作负载身份联合

2. **为 GitLab API 操作添加项目凭据 **：
   * 默认使用 `CI_JOB_TOKEN`，或创建具有 `api` 范围的项目访问令牌
   * 如果使用 PAT，则存储为 `GITLAB_ACCESS_TOKEN`（屏蔽）

3. **将 Claude 作业添加到 `.gitlab-ci.yml`**：使用 Claude API 的 [快速设置 ](<#快速设置>) 作业，或使用 [ 配置中的提供程序作业示例](<#配置举例>)

4. **（可选）启用提及驱动触发器**：
   * 将“注释（注释）”的项目 Webhook 添加到您的事件侦听器（如果您使用的话）
   * 当注释包含 `@claude` 时，让侦听器使用 `AI_FLOW_INPUT` 和 `AI_FLOW_CONTEXT` 等变量调用管道触发器 API

## 示例用例

### 将问题转化为 MR

在问题评论中：

```text wrap theme={null}
@claude implement this feature based on the issue description
```

Claude 分析问题和代码库，在分支中写入更改，并打开 MR 进行审查。

### 获取实施帮助

在 MR 讨论中：

```text wrap theme={null}
@claude suggest a concrete approach to cache the results of this API call
```

Claude 提出更改，添加具有适当缓存的代码，并更新 MR。

### 快速修复错误

在问题或 MR 评论中：

```text wrap theme={null}
@claude fix the TypeError in the user dashboard component
```

Claude 定位 bug，实施修复，并更新分支或打开新的 MR。

## 与 Amazon Bedrock 和 Google 云一起使用

对于企业环境，您可以完全在云基础设施上运行 Claude Code，并具有相同的开发人员体验。



  

#### 亚马逊基岩

    ### 先决条件

在使用 Amazon Bedrock 设置 Claude Code 之前，您需要：

    1. 一个 AWS 账户，可通过 Amazon Bedrock 访问所需的 Claude 模型
    2. GitLab 在 AWS IAM 中配置为 OIDC 身份提供商
    3. 具有 Amazon Bedrock 权限的 IAM 角色以及仅限于您的 GitLab project/refs 的信任策略
    4. GitLab CI/CD 角色假设变量：
       * `AWS_ROLE_TO_ASSUME`（角色ARN）
       * `AWS_REGION`（亚马逊基岩区域）

    ### 设置说明

配置 AWS 以允许 GitLab CI 作业通过 OIDC 承担 IAM 角色（无静态密钥）。

**所需设置：**

    1. 启用 Amazon Bedrock 并请求访问您的目标 Claude 模型
    2. 为 GitLab 创建一个 IAM OIDC 提供程序（如果尚不存在）
    3. 创建一个受 GitLab OIDC 提供商信任的 IAM 角色，仅限于您的项目和受保护的引用
    4. 附加 Amazon Bedrock 调用 API 的最低权限

使用 [Amazon Bedrock 作业示例 ](<#配置举例>) 在运行时将作业的 OIDC 令牌交换为临时 AWS 凭证。
  


  

#### Google 云的Agent 平台

    ### 先决条件

在使用Google云的Agent平台设置Claude Code之前，您需要：

    1. Google 云项目具有：
       * Google 云的 Agent 平台 API 已启用
       * 工作负载身份联合配置为信任 GitLab OIDC
    2. 仅具有所需的 Google 云 Agent 平台角色的专用服务帐户
    3. GitLab CI/CD 变量：
       * `GCP_WORKLOAD_IDENTITY_PROVIDER`（不带`//iam.googleapis.com/`前缀的提供者资源名称，例如`projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`）
       * `GCP_SERVICE_ACCOUNT`（服务帐户电子邮件）
       * `GCP_PROJECT_ID`（Google云项目ID）

    ### 设置说明

配置 Google 云以允许 GitLab CI 作业通过工作负载身份联合模拟服务帐户。

**所需设置：**

    1. 启用 IAM 凭证 API、STS API 和 Google 云的 Agent 平台 API
    2. 为 GitLab OIDC 创建工作负载身份池和提供程序
    3. 使用 Google 云的 Agent 平台角色创建专用服务帐户
    4. 授予 WIF 主体权限来模拟服务帐户

使用 [Agent 平台作业示例 ](<#配置举例>) 进行身份验证，无需存储密钥。
  


## 配置举例

以下是现成的代码片段，您可以根据自己的管道进行调整。

### Amazon Bedrock 作业示例 (OIDC)

**先决条件：**

* Amazon Bedrock 已启用，可以访问您选择的 Claude 型号
* GitLab OIDC 在 AWS 中配置，具有信任您的 GitLab 项目和引用的角色
* 具有 Amazon Bedrock 权限的 IAM 角色（建议最低权限）

**必需的CI/CD变量：**

* `AWS_ROLE_TO_ASSUME`：用于 Amazon Bedrock 访问的 IAM 角色的 ARN
* `AWS_REGION`：亚马逊基岩区域（例如，`us-west-2`）

GitLab 从 `id_tokens:` 块铸造作业的 OIDC 令牌并将其公开为 `GITLAB_OIDC_TOKEN`。将 `aud` 设置为您在 AWS 中的 IAM OIDC 身份提供商上配置的受众群体值，例如您的 GitLab 实例 URL。

```yaml theme={null}
stages:
  - ai

claude-bedrock:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  id_tokens:
    GITLAB_OIDC_TOKEN:
      aud: https://gitlab.example.com
  before_script:
    - apk add --no-cache bash curl jq git aws-cli
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
    # Exchange the job's OIDC token for AWS credentials
    - export AWS_WEB_IDENTITY_TOKEN_FILE="/tmp/oidc_token"
    - printf "%s" "$GITLAB_OIDC_TOKEN" > "$AWS_WEB_IDENTITY_TOKEN_FILE"
    - >
      aws sts assume-role-with-web-identity
      --role-arn "$AWS_ROLE_TO_ASSUME"
      --role-session-name "gitlab-claude-$(date +%s)"
      --web-identity-token "file://$AWS_WEB_IDENTITY_TOKEN_FILE"
      --duration-seconds 3600 > /tmp/aws_creds.json
    - export AWS_ACCESS_KEY_ID="$(jq -r .Credentials.AccessKeyId /tmp/aws_creds.json)"
    - export AWS_SECRET_ACCESS_KEY="$(jq -r .Credentials.SecretAccessKey /tmp/aws_creds.json)"
    - export AWS_SESSION_TOKEN="$(jq -r .Credentials.SessionToken /tmp/aws_creds.json)"
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Implement the requested changes and open an MR'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    AWS_REGION: "us-west-2"
    CLAUDE_CODE_USE_BEDROCK: "1"
```



**注释：**

Amazon Bedrock 的型号 ID 包含区域特定的前缀（例如 `us.anthropic.claude-sonnet-4-6`）。如果您的工作流程支持，则通过作业配置或提示传递所需的模型。


### Agent 平台作业示例（工作负载身份联合）

**先决条件：**

* Google 在您的 GCP 项目中启用了云的 Agent 平台 API
* 工作负载身份联合配置为信任 GitLab OIDC
* 具有 Google 云 Agent 平台权限的服务帐户

**必需的CI/CD变量：**

* `GCP_WORKLOAD_IDENTITY_PROVIDER`：不带`//iam.googleapis.com/`前缀的提供者资源名称，例如`projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`
* `GCP_SERVICE_ACCOUNT`：服务帐户电子邮件
* `GCP_PROJECT_ID`: Google 云项目 ID
* `CLOUD_ML_REGION`：Google 云的 Agent 平台区域（例如，`us-east5`）

GitLab 从 `id_tokens:` 块铸造作业的 OIDC 令牌并将其公开为 `GITLAB_OIDC_TOKEN`。将 `aud` 设置为您在工作负载身份池提供程序上配置的受众值，例如您的 GitLab 实例 URL。该作业将令牌写入文件，凭证配置的 `credential_source` 条目告诉 Google 的身份验证库从那里读取它。将 `GOOGLE_APPLICATION_CREDENTIALS` 设置为凭证配置文件，使其可以通过 [ 应用程序默认凭证 ](<https://code.claude.com/docs/en/google-vertex-ai#3-configure-gcp-credentials>) 供 Claude Code 使用。

```yaml theme={null}
stages:
  - ai

claude-vertex:
  stage: ai
  image: gcr.io/google.com/cloudsdktool/google-cloud-cli:slim
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  id_tokens:
    GITLAB_OIDC_TOKEN:
      aud: https://gitlab.example.com
  before_script:
    - apt-get update && apt-get install -y git && apt-get clean
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
    # Write the job's OIDC token where credential_source expects it
    - printf "%s" "$GITLAB_OIDC_TOKEN" > /tmp/oidc_token
    # Write the WIF credential configuration to a file (no downloaded keys)
    - |
      cat > /tmp/cred.json <<EOF
      {
        "type": "external_account",
        "audience": "//iam.googleapis.com/${GCP_WORKLOAD_IDENTITY_PROVIDER}",
        "subject_token_type": "urn:ietf:params:oauth:token-type:jwt",
        "token_url": "https://sts.googleapis.com/v1/token",
        "credential_source": {
          "file": "/tmp/oidc_token"
        },
        "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/${GCP_SERVICE_ACCOUNT}:generateAccessToken"
      }
      EOF
    # Expose the credentials to Claude Code via Application Default Credentials
    - export GOOGLE_APPLICATION_CREDENTIALS=/tmp/cred.json
    # Authenticate the gcloud CLI with the same credential configuration
    - gcloud auth login --cred-file=/tmp/cred.json
    - gcloud config set project "$GCP_PROJECT_ID"
  script:
    - /bin/gitlab-mcp-server || true
    - >
      CLOUD_ML_REGION="${CLOUD_ML_REGION:-us-east5}"
      claude
      -p "${AI_FLOW_INPUT:-'Review and update code as requested'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    CLOUD_ML_REGION: "us-east5"
    CLAUDE_CODE_USE_VERTEX: "1"
    ANTHROPIC_VERTEX_PROJECT_ID: "$GCP_PROJECT_ID"
```



**注释：**

使用工作负载联合身份验证，您无需存储服务帐户密钥。使用特定于存储库的信任条件和最低权限服务帐户。


## 最佳实践

### CLAUDE.md配置

在存储库根目录中创建一个 `CLAUDE.md` 文件来定义编码标准、审核标准和特定于项目的规则。 Claude 在运行期间读取此文件，并在提出更改时遵循您的约定。

### 安全考虑

**切勿将 API 密钥或云凭据提交到您的存储库 **。始终使用 GitLab CI/CD 变量：

* 添加 `ANTHROPIC_API_KEY` 作为屏蔽变量（并在需要时保护它）
* 尽可能使用特定于提供商的 OIDC（无长期密钥）
* 限制作业权限和网络出口
* 像其他贡献者一样审查 Claude 的 MR

### 优化性能

* 保持 `CLAUDE.md` 重点突出、简洁
* 提供清晰的issue/MR描述以减少迭代
* 尽可能在运行器中缓存 npm 和软件包安装

### CI 费用

将 Claude Code 与 GitLab CI/CD 一起使用时，请注意相关成本：

* **GitLab 跑步时间**：
  * Claude 在您的 GitLab 运行器上运行并消耗计算时间
  * 有关详细信息，请参阅您的 GitLab 计划的跑步者账单

* **API 成本**：
  * 每个 Claude 交互都会根据提示和响应大小消耗令牌
  * 令牌的使用因任务复杂性和代码库大小而异
  * 详情请参阅[Anthropic定价](<https://platform.claude.com/docs/en/about-claude/pricing>)

* **成本优化技巧**：
  * 使用特定的`@claude`命令来减少不必要的转弯
  * 设置适当的 `--max-turns` 和作业 `timeout` 值
  * 限制并发以控制并行运行

## 故障排除

### Claude 不响应@claude 命令

* 验证您的管道是否被触发（手动，MR事件，或通过注释事件listener/webhook）
* 确保您的 `ANTHROPIC_API_KEY` 或云提供商变量存在
* 检查评论是否包含 `@claude`（而不是 `/claude`）并且您的提及触发器是否已配置

### 职位无法发表评论或打开 MR

* 确保 `CI_JOB_TOKEN` 对项目具有足够的权限，或使用具有 `api` 范围的项目访问令牌
* 检查`--allowedTools`中启用了`mcp__gitlab`工具
* 确认作业在 MR 的上下文中运行或通过 `AI_FLOW_*` 变量具有足够的上下文

### 认证错误

* **对于Claude API**：确认`ANTHROPIC_API_KEY`有效且未过期
* **对于 Amazon Bedrock 或 Google 云的 Agent 平台 **：验证 OIDC/WIF 配置、角色模拟和秘密名称；确认区域和型号可用性

## 高级配置

### 公共参数和变量

控制 Claude Code 在您的作业中运行，并使用这些 CLI 标志、GitLab 关键字和变量：

* `-p`：提供内联指令，例如`claude -p "Review this MR"`
* `--max-turns`：限制来回迭代次数
* `timeout`：使用GitLab的作业级别`timeout`关键字限制作业总执行时间，例如`timeout: 30m`
* `ANTHROPIC_API_KEY`：Claude API 所需（不用于 Amazon Bedrock 或 Google 云的 Agent 平台）
* 提供商特定环境：`AWS_REGION`、Google 云的 Agent 平台的 project/region 变量



**注释：**

确切的标志和参数可能因 `@anthropic-ai/claude-code` 版本而异。在您的作业中运行 `claude --help` 以查看支持的选项。


### 自定义 Claude 的行为

您可以通过两种主要方式引导Claude：

1. **CLAUDE.md**：定义编码标准、安全要求和项目约定。 Claude 在跑步期间阅读此内容并遵循您的规则。
2. **自定义提示**：通过作业中的`-p`传递任务特定指令。对不同的工作使用不同的提示（例如，审查、实施、重构）。


## 原文对照

<details>
<summary>展开完整原文</summary>

> ## Documentation Index
> Fetch the complete documentation index at: https://code.claude.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# Claude Code GitLab CI/CD

> Learn about integrating Claude Code into your development workflow with GitLab CI/CD



**说明：**

  Claude Code for GitLab CI/CD is currently in beta. Features and functionality may evolve as we refine the experience.

  This integration is maintained by GitLab. For support, see the following [GitLab issue](<https://gitlab.com/gitlab-org/gitlab/-/issues/573776>).




**注释：**

  This integration is built on top of the [Claude Code CLI and Agent SDK](<https://code.claude.com/docs/en/agent-sdk/overview>), enabling programmatic use of Claude in your CI/CD jobs and custom automation workflows.


## Why use Claude Code with GitLab?

* **Instant MR creation**: Describe what you need, and Claude proposes a complete MR with changes and explanation
* **Automated implementation**: Turn issues into working code with a single command or mention
* **Project-aware**: Claude follows your `CLAUDE.md` guidelines and existing code patterns
* **Simple setup**: Add one job to `.gitlab-ci.yml` and a masked CI/CD variable
* **Enterprise-ready**: Choose Claude API, Amazon Bedrock, or Google Cloud's Agent Platform to meet data residency and procurement needs
* **Secure by default**: Runs in your GitLab runners with your branch protection and approvals

## How it works

Claude Code uses GitLab CI/CD to run AI tasks in isolated jobs and commit results back via MRs:

1. **Event-driven orchestration**: GitLab listens for your chosen triggers (for example, a comment that mentions `@claude` in an issue, MR, or review thread). The job collects context from the thread and repository, builds prompts from that input, and runs Claude Code.

2. **Provider abstraction**: Use the provider that fits your environment:
   * Claude API (SaaS)
   * Amazon Bedrock (IAM-based access, cross-region options)
   * Google Cloud's Agent Platform (GCP-native, Workload Identity Federation)

3. **Sandboxed execution**: Each interaction runs in a container with strict network and filesystem rules. Claude Code enforces workspace-scoped permissions to constrain writes. Every change flows through an MR so reviewers see the diff and approvals still apply.

Pick regional endpoints to reduce latency and meet data-sovereignty requirements while using existing cloud agreements.

## What can Claude do?

In a GitLab pipeline, Claude Code can:

* Create and update MRs from issue descriptions or comments
* Analyze performance regressions and propose optimizations
* Implement features directly in a branch, then open an MR
* Fix bugs and regressions identified by tests or comments
* Respond to follow-up comments to iterate on requested changes

## Setup

### Quick setup

The fastest way to get started is to add a minimal job to your `.gitlab-ci.yml` and set your API key as a masked variable.

1. **Add a masked CI/CD variable**
   * Go to **Settings** → **CI/CD** → **Variables**
   * Add `ANTHROPIC_API_KEY` (masked, protected as needed)

2. **Add a Claude job to `.gitlab-ci.yml`**

```yaml theme={null}
stages:
  - ai

claude:
  stage: ai
  image: node:24-alpine3.21
  # Adjust rules to fit how you want to trigger the job:
  # - manual runs
  # - merge request events
  # - web/API triggers when a comment contains '@claude'
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
  variables:
    GIT_STRATEGY: fetch
  before_script:
    - apk update
    - apk add --no-cache git curl bash
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
  script:
    # Optional: start a GitLab MCP server if your setup provides one
    - /bin/gitlab-mcp-server || true
    # Use AI_FLOW_* variables when invoking via web/API triggers with context payloads
    - echo "$AI_FLOW_INPUT for $AI_FLOW_CONTEXT on $AI_FLOW_EVENT"
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Review this MR and implement the requested changes'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
```

After adding the job and your `ANTHROPIC_API_KEY` variable, test by running the job manually from **CI/CD** → **Pipelines**, or trigger it from an MR to let Claude propose updates in a branch and open an MR if needed.



**注释：**

  To run on Amazon Bedrock or Google Cloud's Agent Platform instead of the Claude API, see the [Using with Amazon Bedrock and Google Cloud](<#using-with-amazon-bedrock-and-google-cloud>) section below for authentication and environment setup.


### Manual setup (recommended for production)

If you prefer a more controlled setup or need enterprise providers:

1. **Configure provider access**:
   * **Claude API**: Create and store `ANTHROPIC_API_KEY` as a masked CI/CD variable
   * **Amazon Bedrock**: **Configure GitLab** → **AWS OIDC** and create an IAM role for Amazon Bedrock
   * **Google Cloud's Agent Platform**: **Configure Workload Identity Federation for GitLab** → **GCP**

2. **Add project credentials for GitLab API operations**:
   * Use `CI_JOB_TOKEN` by default, or create a Project Access Token with `api` scope
   * Store as `GITLAB_ACCESS_TOKEN` (masked) if using a PAT

3. **Add the Claude job to `.gitlab-ci.yml`**: use the [Quick setup](<#quick-setup>) job for the Claude API, or a provider job from [Configuration examples](<#configuration-examples>)

4. **(Optional) Enable mention-driven triggers**:
   * Add a project webhook for "Comments (notes)" to your event listener (if you use one)
   * Have the listener call the pipeline trigger API with variables like `AI_FLOW_INPUT` and `AI_FLOW_CONTEXT` when a comment contains `@claude`

## Example use cases

### Turn issues into MRs

In an issue comment:

```text wrap theme={null}
@claude implement this feature based on the issue description
```

Claude analyzes the issue and codebase, writes changes in a branch, and opens an MR for review.

### Get implementation help

In an MR discussion:

```text wrap theme={null}
@claude suggest a concrete approach to cache the results of this API call
```

Claude proposes changes, adds code with appropriate caching, and updates the MR.

### Fix bugs quickly

In an issue or MR comment:

```text wrap theme={null}
@claude fix the TypeError in the user dashboard component
```

Claude locates the bug, implements a fix, and updates the branch or opens a new MR.

## Using with Amazon Bedrock and Google Cloud

For enterprise environments, you can run Claude Code entirely on your cloud infrastructure with the same developer experience.



  

#### Amazon Bedrock

    ### Prerequisites

    Before setting up Claude Code with Amazon Bedrock, you need:

    1. An AWS account with Amazon Bedrock access to the desired Claude models
    2. GitLab configured as an OIDC identity provider in AWS IAM
    3. An IAM role with Amazon Bedrock permissions and a trust policy restricted to your GitLab project/refs
    4. GitLab CI/CD variables for role assumption:
       * `AWS_ROLE_TO_ASSUME` (role ARN)
       * `AWS_REGION` (Amazon Bedrock region)

    ### Setup instructions

    Configure AWS to allow GitLab CI jobs to assume an IAM role via OIDC (no static keys).

    **Required setup:**

    1. Enable Amazon Bedrock and request access to your target Claude models
    2. Create an IAM OIDC provider for GitLab if not already present
    3. Create an IAM role trusted by the GitLab OIDC provider, restricted to your project and protected refs
    4. Attach least-privilege permissions for Amazon Bedrock invoke APIs

    Use the [Amazon Bedrock job example](<#configuration-examples>) to exchange the job's OIDC token for temporary AWS credentials at runtime.
  


  

#### Google Cloud's Agent Platform

    ### Prerequisites

    Before setting up Claude Code with Google Cloud's Agent Platform, you need:

    1. A Google Cloud project with:
       * Google Cloud's Agent Platform API enabled
       * Workload Identity Federation configured to trust GitLab OIDC
    2. A dedicated service account with only the required Google Cloud's Agent Platform roles
    3. GitLab CI/CD variables:
       * `GCP_WORKLOAD_IDENTITY_PROVIDER` (provider resource name without the `//iam.googleapis.com/` prefix, such as `projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`)
       * `GCP_SERVICE_ACCOUNT` (service account email)
       * `GCP_PROJECT_ID` (Google Cloud project ID)

    ### Setup instructions

    Configure Google Cloud to allow GitLab CI jobs to impersonate a service account via Workload Identity Federation.

    **Required setup:**

    1. Enable IAM Credentials API, STS API, and Google Cloud's Agent Platform API
    2. Create a Workload Identity Pool and provider for GitLab OIDC
    3. Create a dedicated service account with Google Cloud's Agent Platform roles
    4. Grant the WIF principal permission to impersonate the service account

    Use the [Agent Platform job example](<#configuration-examples>) to authenticate without storing keys.
  


## Configuration examples

Below are ready-to-use snippets you can adapt to your pipeline.

### Amazon Bedrock job example (OIDC)

**Prerequisites:**

* Amazon Bedrock enabled with access to your chosen Claude model(s)
* GitLab OIDC configured in AWS with a role that trusts your GitLab project and refs
* IAM role with Amazon Bedrock permissions (least privilege recommended)

**Required CI/CD variables:**

* `AWS_ROLE_TO_ASSUME`: ARN of the IAM role for Amazon Bedrock access
* `AWS_REGION`: Amazon Bedrock region (for example, `us-west-2`)

GitLab mints the job's OIDC token from the `id_tokens:` block and exposes it as `GITLAB_OIDC_TOKEN`. Set `aud` to the audience value you configured on the IAM OIDC identity provider in AWS, for example your GitLab instance URL.

```yaml theme={null}
stages:
  - ai

claude-bedrock:
  stage: ai
  image: node:24-alpine3.21
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  id_tokens:
    GITLAB_OIDC_TOKEN:
      aud: https://gitlab.example.com
  before_script:
    - apk add --no-cache bash curl jq git aws-cli
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
    # Exchange the job's OIDC token for AWS credentials
    - export AWS_WEB_IDENTITY_TOKEN_FILE="/tmp/oidc_token"
    - printf "%s" "$GITLAB_OIDC_TOKEN" > "$AWS_WEB_IDENTITY_TOKEN_FILE"
    - >
      aws sts assume-role-with-web-identity
      --role-arn "$AWS_ROLE_TO_ASSUME"
      --role-session-name "gitlab-claude-$(date +%s)"
      --web-identity-token "file://$AWS_WEB_IDENTITY_TOKEN_FILE"
      --duration-seconds 3600 > /tmp/aws_creds.json
    - export AWS_ACCESS_KEY_ID="$(jq -r .Credentials.AccessKeyId /tmp/aws_creds.json)"
    - export AWS_SECRET_ACCESS_KEY="$(jq -r .Credentials.SecretAccessKey /tmp/aws_creds.json)"
    - export AWS_SESSION_TOKEN="$(jq -r .Credentials.SessionToken /tmp/aws_creds.json)"
  script:
    - /bin/gitlab-mcp-server || true
    - >
      claude
      -p "${AI_FLOW_INPUT:-'Implement the requested changes and open an MR'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    AWS_REGION: "us-west-2"
    CLAUDE_CODE_USE_BEDROCK: "1"
```



**注释：**

  Model IDs for Amazon Bedrock include region-specific prefixes (for example, `us.anthropic.claude-sonnet-4-6`). Pass the desired model via your job configuration or prompt if your workflow supports it.


### Agent Platform job example (Workload Identity Federation)

**Prerequisites:**

* Google Cloud's Agent Platform API enabled in your GCP project
* Workload Identity Federation configured to trust GitLab OIDC
* A service account with Google Cloud's Agent Platform permissions

**Required CI/CD variables:**

* `GCP_WORKLOAD_IDENTITY_PROVIDER`: provider resource name without the `//iam.googleapis.com/` prefix, such as `projects/123456789/locations/global/workloadIdentityPools/my-pool/providers/my-provider`
* `GCP_SERVICE_ACCOUNT`: service account email
* `GCP_PROJECT_ID`: Google Cloud project ID
* `CLOUD_ML_REGION`: Google Cloud's Agent Platform region (for example, `us-east5`)

GitLab mints the job's OIDC token from the `id_tokens:` block and exposes it as `GITLAB_OIDC_TOKEN`. Set `aud` to the audience value you configured on the Workload Identity Pool provider, for example your GitLab instance URL. The job writes the token to a file, and the credential configuration's `credential_source` entry tells Google's auth libraries to read it from there. Setting `GOOGLE_APPLICATION_CREDENTIALS` to the credential configuration file makes it available to Claude Code through [Application Default Credentials](<https://code.claude.com/docs/en/google-vertex-ai#3-configure-gcp-credentials>).

```yaml theme={null}
stages:
  - ai

claude-vertex:
  stage: ai
  image: gcr.io/google.com/cloudsdktool/google-cloud-cli:slim
  rules:
    - if: '$CI_PIPELINE_SOURCE == "web"'
  id_tokens:
    GITLAB_OIDC_TOKEN:
      aud: https://gitlab.example.com
  before_script:
    - apt-get update && apt-get install -y git && apt-get clean
    - curl -fsSL https://claude.ai/install.sh | bash
    # The installer places claude in ~/.local/bin, which isn't on PATH in this image
    - export PATH="$HOME/.local/bin:$PATH"
    # Write the job's OIDC token where credential_source expects it
    - printf "%s" "$GITLAB_OIDC_TOKEN" > /tmp/oidc_token
    # Write the WIF credential configuration to a file (no downloaded keys)
    - |
      cat > /tmp/cred.json <<EOF
      {
        "type": "external_account",
        "audience": "//iam.googleapis.com/${GCP_WORKLOAD_IDENTITY_PROVIDER}",
        "subject_token_type": "urn:ietf:params:oauth:token-type:jwt",
        "token_url": "https://sts.googleapis.com/v1/token",
        "credential_source": {
          "file": "/tmp/oidc_token"
        },
        "service_account_impersonation_url": "https://iamcredentials.googleapis.com/v1/projects/-/serviceAccounts/${GCP_SERVICE_ACCOUNT}:generateAccessToken"
      }
      EOF
    # Expose the credentials to Claude Code via Application Default Credentials
    - export GOOGLE_APPLICATION_CREDENTIALS=/tmp/cred.json
    # Authenticate the gcloud CLI with the same credential configuration
    - gcloud auth login --cred-file=/tmp/cred.json
    - gcloud config set project "$GCP_PROJECT_ID"
  script:
    - /bin/gitlab-mcp-server || true
    - >
      CLOUD_ML_REGION="${CLOUD_ML_REGION:-us-east5}"
      claude
      -p "${AI_FLOW_INPUT:-'Review and update code as requested'}"
      --permission-mode acceptEdits
      --allowedTools "Bash Read Edit Write mcp__gitlab"
      --debug
  variables:
    CLOUD_ML_REGION: "us-east5"
    CLAUDE_CODE_USE_VERTEX: "1"
    ANTHROPIC_VERTEX_PROJECT_ID: "$GCP_PROJECT_ID"
```



**注释：**

  With Workload Identity Federation, you do not need to store service account keys. Use repository-specific trust conditions and least-privilege service accounts.


## Best practices

### CLAUDE.md configuration

Create a `CLAUDE.md` file at the repository root to define coding standards, review criteria, and project-specific rules. Claude reads this file during runs and follows your conventions when proposing changes.

### Security considerations

**Never commit API keys or cloud credentials to your repository**. Always use GitLab CI/CD variables:

* Add `ANTHROPIC_API_KEY` as a masked variable (and protect it if needed)
* Use provider-specific OIDC where possible (no long-lived keys)
* Limit job permissions and network egress
* Review Claude's MRs like any other contributor

### Optimizing performance

* Keep `CLAUDE.md` focused and concise
* Provide clear issue/MR descriptions to reduce iterations
* Cache npm and package installs in runners where possible

### CI costs

When using Claude Code with GitLab CI/CD, be aware of associated costs:

* **GitLab Runner time**:
  * Claude runs on your GitLab runners and consumes compute minutes
  * See your GitLab plan's runner billing for details

* **API costs**:
  * Each Claude interaction consumes tokens based on prompt and response size
  * Token usage varies by task complexity and codebase size
  * See [Anthropic pricing](<https://platform.claude.com/docs/en/about-claude/pricing>) for details

* **Cost optimization tips**:
  * Use specific `@claude` commands to reduce unnecessary turns
  * Set appropriate `--max-turns` and job `timeout` values
  * Limit concurrency to control parallel runs

## Troubleshooting

### Claude not responding to @claude commands

* Verify your pipeline is being triggered (manually, MR event, or via a note event listener/webhook)
* Ensure your `ANTHROPIC_API_KEY` or cloud provider variables are present
* Check that the comment contains `@claude` (not `/claude`) and that your mention trigger is configured

### Job can't write comments or open MRs

* Ensure `CI_JOB_TOKEN` has sufficient permissions for the project, or use a Project Access Token with `api` scope
* Check the `mcp__gitlab` tool is enabled in `--allowedTools`
* Confirm the job runs in the context of the MR or has enough context via `AI_FLOW_*` variables

### Authentication errors

* **For Claude API**: Confirm `ANTHROPIC_API_KEY` is valid and unexpired
* **For Amazon Bedrock or Google Cloud's Agent Platform**: Verify OIDC/WIF configuration, role impersonation, and secret names; confirm region and model availability

## Advanced configuration

### Common parameters and variables

Control Claude Code runs in your jobs with these CLI flags, GitLab keywords, and variables:

* `-p`: provide instructions inline, for example `claude -p "Review this MR"`
* `--max-turns`: limit the number of back-and-forth iterations
* `timeout`: limit total job execution time with GitLab's job-level `timeout` keyword, for example `timeout: 30m`
* `ANTHROPIC_API_KEY`: required for the Claude API (not used for Amazon Bedrock or Google Cloud's Agent Platform)
* Provider-specific environment: `AWS_REGION`, project/region vars for Google Cloud's Agent Platform



**注释：**

  Exact flags and parameters may vary by version of `@anthropic-ai/claude-code`. Run `claude --help` in your job to see supported options.


### Customizing Claude's behavior

You can guide Claude in two primary ways:

1. **CLAUDE.md**: Define coding standards, security requirements, and project conventions. Claude reads this during runs and follows your rules.
2. **Custom prompts**: Pass task-specific instructions via `-p` in the job. Use different prompts for different jobs (for example, review, implement, refactor).


</details>
