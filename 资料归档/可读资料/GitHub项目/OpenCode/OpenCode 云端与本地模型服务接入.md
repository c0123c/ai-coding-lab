# OpenCode 云端与本地模型服务接入

配置参考。列出多种供应商的认证、环境变量、地址和自定义服务配置，并给出连接排查；用于比较接入方式，各服务的适用条件独立。

- 来源：OpenCode
- 资料类型：配置参考
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-09-03](<https://github.com/anomalyco/opencode/commit/bbe4c952d707bcb5436646de30a1ed8f0cc64b74>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：供应商接口、价格和认证具有时效性，应核对实际服务。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/providers.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 云端与本地模型服务接入.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Providers
description: Using any LLM provider in OpenCode.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 使用 [AI SDK](<https://ai-sdk.dev/>) 和 [Models.dev](<https://models.dev>) 来支持 **75+ LLM 提供程序**，并且支持运行本地模型。

要添加提供商，您需要：

1. 使用 `/connect` 命令为提供者添加 API 密钥。
2. 在您的 OpenCode 配置中配置提供程序。

---

### 凭证

当您使用 `/connect` 命令添加提供商的 API 密钥时，它们将存储在 
 中 `~/.local/share/opencode/auth.json`。

---

### 配置

您可以通过 OpenCode
 配置中的 `provider` 部分自定义提供程序。

---

#### 底座URL

您可以通过设置 `baseURL` 选项为任何提供商自定义基础 URL。这在使用代理服务或自定义端点时非常有用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "options": {
        "baseURL": "https://api.anthropic.com/v1"
      }
    }
  }
}
```

---

#### 隐藏模型

您可以使用 `blacklist` 选项从提供商的 `/models` 选择器中隐藏特定型号。当提供程序公开您不想使用或选择的模型时，这非常有用。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "blacklist": ["claude-opus-4-20250514"]
    }
  }
}
```

反向 `whitelist` 选项隐藏除列出的模型之外的所有模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "whitelist": ["claude-sonnet-4-20250514"]
    }
  }
}
```

这两个选项都采用模型 ID 数组 - 与 `/models` 选择器中显示的 ID 相同。

- `blacklist` 从选择器中删除列出的型号。
- `whitelist` 仅保留列出的型号并隐藏其余型号。
- 您可以组合它们：`whitelist` 缩小集合范围，然后 `blacklist` 从中删除条目。

---

## OpenCode 禅宗

OpenCode Zen 是 OpenCode 团队提供的模型列表，这些模型已经过 
 测试和验证，可以与 OpenCode 良好配合。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>)。

**提示：**

如果您是新手，我们建议您从 OpenCode Zen 开始。

1. 在TUI中运行`/connect`命令，选择`OpenCode Zen`，然后前往[opencode.ai/auth](<https://opencode.ai/zen>)。

```txt
/connect
```

2. 登录，添加您的账单详细信息，然后复制您的 API 密钥。

3. 粘贴您的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行【TUI】中的【`/models`】即可看到我们推荐的型号列表。

```txt
/models
```

它的工作方式与 OpenCode 中的任何其他提供程序一样，并且完全可以选择使用。

---

## OpenCode Go

OpenCode Go 是一种低成本订阅计划，可以可靠地访问 OpenCode 团队提供的流行开放编码模型，这些模型已经过 
 测试和验证，可以与 OpenCode 良好配合。

1. 在TUI中运行`/connect`命令，选择`OpenCode Go`，然后前往[opencode.ai/auth](<https://opencode.ai/zen>)。

```txt
/connect
```

2. 登录，添加您的账单详细信息，然后复制您的 API 密钥。

3. 粘贴您的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行【TUI】中的【`/models`】即可看到我们推荐的型号列表。

```txt
/models
```

它的工作方式与 OpenCode 中的任何其他提供程序一样，并且完全可以选择使用。

---

## 目录

让我们详细了解一些提供商。如果您想将提供商添加到 
 列表中，请随时打开 PR。

**注释：**

在这里没有看到提供商？提交 PR。

---

### 302.AI

1. 前往 [302.AI 控制台 ](<https://302.ai/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令，搜索**302.AI**。

```txt
/connect
```

3. 输入您的 302.AI API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

---

### 亚马逊基岩

要将 Amazon Bedrock 与 OpenCode 结合使用：

1. 前往 Amazon Bedrock 控制台中的 **模型目录** 并请求
访问您想要的模型。

**提示：**

您需要能够在 Amazon Bedrock 中访问所需的模型。

2. **使用以下方法之一配置身份验证**：

   ***

   #### 环境变量（快速启动）

运行 opencode 时设置以下环境变量之一：

```bash
# Option 1: Using AWS access keys
AWS_ACCESS_KEY_ID=XXX AWS_SECRET_ACCESS_KEY=YYY opencode

# Option 2: Using named AWS profile
AWS_PROFILE=my-profile opencode

# Option 3: Using Bedrock bearer token
AWS_BEARER_TOKEN_BEDROCK=XXX opencode
```

或者将它们添加到您的 bash 配置文件中：

**代码示例：** `~/.bash_profile`

```bash
export AWS_PROFILE=my-dev-profile
export AWS_REGION=us-east-1
```

   ***

   #### 配置文件（推荐）

对于特定于项目或持久的配置，请使用 `opencode.json`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "my-aws-profile"
      }
    }
  }
}
```

**可用选项：**
   - `region` - AWS 区域（例如，`us-east-1`、`eu-west-1`）
   - `profile` - AWS 来自 `~/.aws/credentials` 的命名配置文件
   - `endpoint` - VPC 端点的自定义端点 URL（通用 `baseURL` 选项的别名）

**提示：**

配置文件选项优先于环境变量。

   ***

   #### 高级：VPC 端点

如果您对基岩使用 VPC 端点：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "production",
        "endpoint": "https://bedrock-runtime.us-east-1.vpce-xxxxx.amazonaws.com"
      }
    }
  }
}
```

**注释：**

`endpoint` 选项是通用 `baseURL` 选项的别名，使用 AWS- 特定术语。如果同时指定了`endpoint`和`baseURL`，则`endpoint`优先。

   ***

   #### 认证方式
   - **`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`**：创建IAM用户并在AWS控制台中生成访问密钥
   - **`AWS_PROFILE`**：使用`~/.aws/credentials`中的命名配置文件。首先配置`aws configure --profile my-profile`或`aws sso login`
   - **`AWS_BEARER_TOKEN_BEDROCK`**：从 Amazon Bedrock 控制台生成长期 API 密钥
   - **`AWS_WEB_IDENTITY_TOKEN_FILE` / `AWS_ROLE_ARN`**：适用于 EKS IRSA（IAM 服务帐户角色）或具有 OIDC 联合的其他 Kubernetes 环境。使用服务帐户注释时，这些环境变量由 Kubernetes 自动注入。

   ***

   #### 认证优先级

Amazon Bedrock 使用以下身份验证优先级：
   1. **承载令牌** - `AWS_BEARER_TOKEN_BEDROCK`环境变量或来自`/connect`命令的令牌
   2. **AWS 凭证链** - 配置文件、访问密钥、共享凭证、IAM 角色、Web 身份令牌 (EKS IRSA)、实例元数据

**注释：**

设置持有者令牌（通过 `/connect` 或 `AWS_BEARER_TOKEN_BEDROCK`）时，它优先于所有 AWS 凭证方法，包括配置的配置文件。

3. 运行【`/models`】命令选择您需要的型号。

```txt
/models
```

**注释：**

对于自定义推理配置文件，请在密钥中使用模型和提供程序名称，并将 `id` 属性设置为 arn。这确保了正确的缓存。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      // ...
      "models": {
        "anthropic-claude-sonnet-4.5": {
          "id": "arn:aws:bedrock:us-east-1:xxx:application-inference-profile/yyy"
        }
      }
    }
  }
}
```

---

### Anthropic

1. 注册后，运行 `/connect` 命令并选择 Anthropic。

```txt
/connect
```

2. 在这里您可以选择**Claude Pro/Max**选项，它将打开您的浏览器
并要求您进行身份验证。

```txt
┌ Select auth method
│
│ Manually enter API Key
└
```

3. 现在，当您使用 `/models` 命令时，所有 Anthropic 模型都应该可用。

```txt
/models
```

**说明：**

有些插件允许您将 Claude Pro/Max 模型与 
OpenCode 一起使用。 Anthropic 明确禁止这样做。

以前版本的 OpenCode 与这些插件捆绑在一起，但从 1.3.0 开始，情况不再是 

其他公司支持开发人员工具的自由选择 - 您可以在零设置的情况下使用 
 OpenCode 中的以下订阅：

- ChatGPT 加
- Github Copilot
- 亚特实验室二重奏

---

### 原子聊天

您可以通过 [Atomic Chat](<https://atomic.chat>) 将 opencode 配置为使用本地模型，这是一个桌面应用程序，在 OpenAI 兼容的 API 服务器（默认端点 `http://127.0.0.1:1337/v1`）后面运行本地 LLM。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "atomic-chat": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Atomic Chat (local)",
      "options": {
        "baseURL": "http://127.0.0.1:1337/v1"
      },
      "models": {
        "<your-model-id>": {
          "name": "<your-model-name>"
        }
      }
    }
  }
}
```

在这个例子中：

- `atomic-chat` 是自定义提供程序 ID。这可以是您想要的任何字符串。
- `npm` 指定用于此提供程序的包。这里，`@ai-sdk/openai-compatible`用于任何OpenAI兼容的API。
- `name` 是 UI 中提供者的显示名称。
- `options.baseURL` 是本地服务器的端点。更改主机和端口以匹配您的原子聊天设置。
- `models` 是模型 ID 到其显示名称的映射。每个 ID 必须与 `GET /v1/models` 返回的 `id` 匹配 — 运行 `curl http://127.0.0.1:1337/v1/models` 以列出当前在 Atomic Chat 中加载的 ID。

**提示：**

如果工具调用不能正常工作，请选择具有强大工具调用支持的加载模型（例如，Qwen-Coder 或 DeepSeek-Coder 变体）。

---

### 天蓝色 OpenAI

**注释：**

如果遇到“抱歉，但我无法协助该请求”错误，请尝试将 Azure 资源中的内容筛选器从 **DefaultV2** 更改为 **默认 **。

1. 转到 [Azure 门户 ](<https://portal.azure.com/>) 并创建 **Azure OpenAI** 资源。你需要：
   - **资源名称**：这将成为您的API端点的一部分（`https://RESOURCE_NAME.openai.azure.com/`）
   - **API 密钥**：来自您的资源的 `KEY 1` 或 `KEY 2`

2. Go 到 [Azure AI Foundry](<https://ai.azure.com/>) 并部署模型。

**注释：**

部署名称必须与 opencode 的型号名称匹配才能正常工作。

3. 运行 `/connect` 命令并搜索 **Azure**。

```txt
/connect
```

4. 输入您的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

5. 将您的资源名称设置为环境变量：

```bash
AZURE_RESOURCE_NAME=XXX opencode
```

或者将其添加到您的 bash 配置文件中：

**代码示例：** `~/.bash_profile`

```bash
export AZURE_RESOURCE_NAME=XXX
```

6. 运行 `/models` 命令以选择您部署的模型。

```txt
/models
```

#### Microsoft Entra ID (天蓝色 CLI)

您可以使用 Azure CLI 会话代替 API 密钥。 [安装Azure CLI](<https://learn.microsoft.com/en-us/cli/azure/install-azure-cli>)，运行`az login`，然后运行`/connect`，选择**Azure**，然后选择**Microsoft Entra ID（Azure CLI)**。出现提示时输入 Azure 资源名称。如果资源属于不同的租户，请使用 `az login --tenant TENANT_ID`。

通过在 [Azure 门户](<https://portal.azure.com/>) 或 [Microsoft Foundry](<https://ai.azure.com/>) 中打开 Azure OpenAI 或 Foundry 资源来查找资源名称。它也是端点的第一部分：`https://my-models.openai.azure.com/` 或 `https://my-models.services.ai.azure.com/` 中的 `my-models`。如果您的身份可以列出资源，您还可以通过以下方式查找其名称和资源组：

```bash
az cognitiveservices account list \
  --query "[].{name:name,resourceGroup:resourceGroup}" \
  --output table
```

OpenCode 不查询 Azure 管理 API 或发现部署。选择目录名称与您的部署匹配的模型，或显式配置其部署名称：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "azure": {
      "models": {
        "gpt-5-mini": {
          "id": "gpt-production"
        }
      }
    }
  }
}
```

为您的身份分配部署所需的推理角色：**认知服务 OpenAI 用户**（适用于 Azure OpenAI 模型）或 **认知服务用户**（适用于其他 Foundry 模型）。 OpenCode通过Azure CLI刷新访问令牌，包括2.54.0之前的版本，因此您只需在CLI会话过期时重新登录即可。

---

### Azure 认知服务

1. 转到 [Azure 门户 ](<https://portal.azure.com/>) 并创建 **Azure OpenAI** 资源。你需要：
   - **资源名称**：这将成为您的API端点的一部分（`https://AZURE_COGNITIVE_SERVICES_RESOURCE_NAME.cognitiveservices.azure.com/`）
   - **API 密钥**：来自您的资源的 `KEY 1` 或 `KEY 2`

2. Go 到 [Azure AI Foundry](<https://ai.azure.com/>) 并部署模型。

**注释：**

部署名称必须与 opencode 的型号名称匹配才能正常工作。

3. 运行 `/connect` 命令并搜索 **Azure 认知服务 **。

```txt
/connect
```

4. 输入您的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

5. 将您的资源名称设置为环境变量：

```bash
AZURE_COGNITIVE_SERVICES_RESOURCE_NAME=XXX opencode
```

或者将其添加到您的 bash 配置文件中：

**代码示例：** `~/.bash_profile`

```bash
export AZURE_COGNITIVE_SERVICES_RESOURCE_NAME=XXX
```

6. 运行 `/models` 命令以选择您部署的模型。

```txt
/models
```

---

### 巴斯坦

1. 前往 [Baseten](<https://app.baseten.co/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**Baseten**。

```txt
/connect
```

3. 输入您的 Baseten API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

---

### 大脑

1. 前往 [Cerebras 控制台 ](<https://inference.cerebras.ai/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行 `/connect` 命令并搜索 **Cerebras**。

```txt
/connect
```

3. 输入您的 Cerebras API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令，选择型号，如_Qwen 3 Coder 480B_。

```txt
/models
```

---

### Cloudflare AI 网关

Cloudflare AI 网关允许您通过统一端点访问来自 OpenAI、Anthropic、Workers AI 等的模型。使用 [ 统一计费 ](<https://developers.cloudflare.com/ai-gateway/features/unified-billing/>)，您不需要为每个提供商提供单独的 API 密钥。

1. 前往 [Cloudflare 仪表板 ](<https://dash.cloudflare.com/>)，导航到 **AI** > **AI 网关 **，然后创建一个新网关。记下您的**帐户ID**和**网关ID**。

2. 运行 `/connect` 命令并搜索 **Cloudflare AI 网关 **。

```txt
/connect
```

3. 出现提示时输入您的 ** 帐户 ID**。

```txt
┌ Enter your Cloudflare Account ID
│
│
└ enter
```

4. 出现提示时输入您的 ** 网关 ID**。

```txt
┌ Enter your Cloudflare AI Gateway ID
│
│
└ enter
```

5. 输入您的 **Cloudflare API 令牌 **。

```txt
┌ Gateway API token
│
│
└ enter
```

6. 执行【`/models`】命令选择型号。

```txt
/models
```

您还可以通过 opencode 配置添加模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "cloudflare-ai-gateway": {
      "models": {
        "openai/gpt-4o": {},
        "anthropic/claude-sonnet-4": {}
      }
    }
  }
}
```

或者，您可以设置环境变量而不是使用 `/connect`。

**代码示例：** `~/.bash_profile`

```bash
export CLOUDFLARE_ACCOUNT_ID=your-32-character-account-id
export CLOUDFLARE_GATEWAY_ID=your-gateway-id
export CLOUDFLARE_API_TOKEN=your-api-token
```

---

### Cloudflare 工作人员 AI

Cloudflare Workers AI 可让您直接通过 REST API 在 Cloudflare 的全球网络上运行 AI 模型，支持的模型无需单独的提供商帐户。

1. 前往 [Cloudflare 仪表板 ](<https://dash.cloudflare.com/>)，导航到 **Workers AI**，然后选择 **使用 REST API** 获取您的**账户ID**并创建API令牌。

2. 运行 `/connect` 命令并搜索 **Cloudflare Workers AI**。

```txt
/connect
```

3. 出现提示时输入您的 ** 帐户 ID**。

```txt
┌ Enter your Cloudflare Account ID
│
│
└ enter
```

4. 输入您的 **Cloudflare API 密钥 **。

```txt
┌ API key
│
│
└ enter
```

5. 执行【`/models`】命令选择型号。

```txt
/models
```

或者，您可以设置环境变量而不是使用 `/connect`。

**代码示例：** `~/.bash_profile`

```bash
export CLOUDFLARE_ACCOUNT_ID=your-32-character-account-id
export CLOUDFLARE_API_KEY=your-api-token
```

---

### 科尔特克斯

1. 前往 [Cortecs 控制台 ](<https://cortecs.ai/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**Cortecs**。

```txt
/connect
```

3. 输入您的 Cortecs API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择一个模型，如_Kimi K2 Instruct_。

```txt
/models
```

---

### DeepSeek

1. 前往 [DeepSeek 控制台 ](<https://platform.deepseek.com/>)，创建一个帐户，然后单击 ** 创建新的 API 密钥 **。

2. 运行`/connect`命令并搜索**DeepSeek**。

```txt
/connect
```

3. 输入您的 DeepSeek API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择DeepSeek模型，例如_DeepSeek V4 Pro_。

```txt
/models
```

---

### 深层基础设施

1. 前往 [Deep Infra 仪表板 ](<https://deepinfra.com/dash>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**Deep Infra**。

```txt
/connect
```

3. 输入您的 Deep Infra API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

---

### DigitalOcean

DigitalOcean 的 [ 推理引擎 ](<https://docs.digitalocean.com/products/inference/>) 提供对 GPT-OSS、Llama、Qwen 和 DeepSeek 等开放模型的访问，以及自定义 [ 推理路由器 ](<https://docs.digitalocean.com/products/inference/how-to/use-inference-router/>) 将每个请求路由到最便宜、最快或最适合任务的模型。

OpenCode支持两种认证方式：

- **OAuth（推荐）** — 登录您的DigitalOcean帐户； OpenCode 使用您的 DigitalOcean API 令牌直接进行推理并发现您的推理路由器。
- **模型访问密钥** — 粘贴 DigitalOcean 控制台中的现有密钥。

#### OAuth（推荐）

1. 运行`/connect`命令并搜索**DigitalOcean**。

```txt
/connect
```

2. 选择**使用DigitalOcean**登录。

```txt
┌ Select auth method
│
│ Login with DigitalOcean
│ Paste Model Access Key
└
```

3. 您的浏览器将打开以授权 OpenCode。登录并批准。

**注释：**

OpenCode 请求 `genai:read` 和 `inference:query` OAuth 范围。您的 DigitalOcean API 令牌直接用于推理 - 不会创建单独的模型访问密钥。

**注释：**

推理路由器仅在 OAuth 之后出现在模型选择器中。手动粘贴模型访问密钥不会发现路由器。

4. 运行`/models`命令。您的推理路由器在模型选择中显示为 `router:` 格式。

```txt
/models
```

5. 要选取新创建的推理路由器，请重新运行 `/connect` 并再次选择 **DigitalOcean**。

#### 使用模型访问密钥

如果您想直接粘贴密钥：

1. 前往 **管理** 的推理部分页面 [DigitalOcean 控制台](<https://cloud.digitalocean.com/>) 并创建一个新密钥。

2. 运行`/connect`命令并选择**DigitalOcean**，然后选择**粘贴模型访问密钥**。

```txt
┌ Enter your DigitalOcean Model Access Key
│
│
└ enter
```

**注释：**

使用此方法不会自动发现推理路由器。要在模型选择器中显示它们，请改为通过 OAuth 登录。

3. 执行【`/models`】命令选择型号。

```txt
/models
```

#### 环境变量

或者，将模型访问密钥设置为环境变量。

```bash
export DIGITALOCEAN_ACCESS_TOKEN=your-model-access-key
```

#### 推理路由器

推理路由器可让您定义跨多个模型的路由策略 - 根据任务为每个请求选择最便宜、最快或最合适的模型。 OAuth 之后，OpenCode 在模型选择器中将每个路由器显示为 `router:<router-name>`。

选择路由器型号可以直接替代任何其他型号 - OpenCode 转发您的请求，DigitalOcean 根据您的路由器策略选择底层型号。了解有关 [ 推理路由器 ](<https://docs.digitalocean.com/products/inference/how-to/use-inference-router/>) 的更多信息

---

### 伊甸园AI

[Eden AI](<https://www.edenai.co/>) 是一个基于 EU- 的网关，通过单个 OpenAI 兼容的 API 为来自许多供应商的模型提供服务，并为需要推理以保持在状态的团队提供单独的 EU 端点EU。

1. 前往 [Eden AI 平台 ](<https://app.edenai.run/user/register>) 创建帐户并生成 API 密钥。

2. 运行`/connect`命令并搜索**Eden AI**。

```txt
/connect
```

3. 输入您的 Eden AI API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行 `/models` 命令选择模型，如 _Mistral Large 3_ 或 _Claude Sonnet 5_。

```txt
/models
```

Eden AI 模型 ID 本身采用 `vendor/model` 形式，因此完整参考包含三个部分，例如 `edenai/anthropic/claude-sonnet-5`。

5. 要在 Eden AI 的 EU 网关上保留请求，请设置其基础 URL。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "edenai": {
      "options": {
        "baseURL": "https://api.eu.edenai.run/v3"
      }
    }
  }
}
```

默认值为 `https://api.edenai.run/v3`，因此这会将全局端点交换为 EU 端点。 EU 端点服务于 EU 中可用的目录子集，因此可能无法通过它访问步骤 4 中选择的模型。

---

### FrogBot

1. 前往 [FrogBot 仪表板 ](<https://app.frogbot.ai/signup>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**FrogBot**。

```txt
/connect
```

3. 输入您的 FrogBot API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

---

### 烟花 AI

1. 前往 [Fireworks AI 控制台 ](<https://app.fireworks.ai/>)，创建一个帐户，然后单击 **创建 API 密钥 **。

2. 运行`/connect`命令并搜索**Fireworks AI**。

```txt
/connect
```

3. 输入您的 Fireworks AI API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择一个模型，如_Kimi K2 Instruct_。

```txt
/models
```

---

### GitLab 双人

**注意：实验：**

GitLab OpenCode 中的 Duo 支持是实验性的。功能、配置和 
 行为可能会在未来版本中发生变化。

OpenCode 与 [GitLab Duo Agent 平台 ](<https://docs.gitlab.com/user/duo_agent_platform/>)、
 集成，提供 AI- 强大的代理聊天功能以及本机工具调用功能。

** 注释：许可证要求：**

GitLab Duo Agent 平台需要 **Premium** 或 **Ultimate** GitLab
 订阅。它可在 GitLab.com 和 GitLab 自我管理上获取。
请参阅 [GitLab Duo Agent 平台先决条件 ](<https://docs.gitlab.com/user/duo_agent_platform/#prerequisites>)
 了解完整要求。

1. 运行`/connect`命令并选择GitLab。

```txt
/connect
```

2. 选择您的身份验证方法：

```txt
┌ Select auth method
│
│ OAuth (Recommended)
│ Personal Access Token
└
```

   #### 使用 OAuth（推荐）

选择**OAuth**，您的浏览器将打开进行授权。

   #### 使用个人访问令牌
   1. Go 至 [GitLab 用户设置 > 访问令牌](<https://gitlab.com/-/user_settings/personal_access_tokens>)
   2. 点击**添加新代币**
   3. 名称：`OpenCode`，范围：`api`
   4. 复制令牌（以`glpat-`开头）
   5. 在终端中输入

3. 运行`/models`命令查看可用型号。

```txt
/models
```

提供三种基于 Claude 的型号：
   - **duo-chat-haiku-4-5**（默认）- 快速任务的快速响应
   - **duo-chat-sonnet-4-5** - 大多数工作流程的平衡性能
   - **duo-chat-opus-4-5** - 最有能力进行复杂分析

**注释：**

如果您不希望
将令牌存储在opencode身份验证存储中，您还可以指定“GITLAB_TOKEN”环境变量。

##### 自托管 GitLab

** 注释：合规注释：**

OpenCode 使用一个小模型来执行一些 AI 任务，例如生成会话标题。
它默认配置为使用 gpt-5-nano，由 Zen 托管。要将 OpenCode
 锁定为仅使用您自己的 GitLab 托管实例，请将以下内容添加到您的 
`opencode.json` 文件中。还建议禁用会话共享。

```json
{
  "$schema": "https://opencode.ai/config.json",
  "small_model": "gitlab/duo-chat-haiku-4-5",
  "share": "disabled"
}
```

对于自托管 GitLab 实例：

```bash
export GITLAB_INSTANCE_URL=https://gitlab.company.com
export GITLAB_TOKEN=glpat-...
```

如果您的实例运行自定义 AI 网关：

```bash
GITLAB_AI_GATEWAY_URL=https://ai-gateway.company.com
```

或者添加到您的 bash 配置文件中：

**代码示例：** `~/.bash_profile`

```bash
export GITLAB_INSTANCE_URL=https://gitlab.company.com
export GITLAB_AI_GATEWAY_URL=https://ai-gateway.company.com
export GITLAB_TOKEN=glpat-...
```

**注释：**

您的 GitLab 管理员必须：

1. [开启GitLab双核](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-gitlab-duo-on-or-off>)
对于用户、组或实例
2. [开启Agent平台](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-gitlab-duo-agent-platform-on-or-off>)
(GitLab 18.8+) 或 [启用测试版和实验性功能](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-on-beta-and-experimental-features>)
（GitLab 18.7 及更早版本）
3. 对于自我管理，[配置您的实例](<https://docs.gitlab.com/administration/gitlab_duo/configure/gitlab_self_managed/>)

##### 适用于自托管实例的 OAuth

为了使 Oauth 适用于您的自托管实例，您需要使用 
 回调 URL `http://127.0.0.1:8080/callback` 和以下范围创建 
 一个新应用程序（设置 → 应用程序）：

- api（代表您访问API）
- read_user（读取您的个人信息）
- read_repository（允许对存储库进行只读访问）

然后将应用程序 ID 公开为环境变量：

```bash
export GITLAB_OAUTH_CLIENT_ID=your_application_id_here
```

更多文档请参见 [opencode-gitlab-auth](<https://www.npmjs.com/package/opencode-gitlab-auth>) 主页。

##### 配置

通过【`opencode.json`】定制：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "gitlab": {
      "options": {
        "instanceUrl": "https://gitlab.com"
      }
    }
  }
}
```

##### GitLab Duo Agent 平台 (DAP) 工作流程模型

DAP 工作流模型提供了另一种执行路径，通过 GitLab 的 Duo 工作流服务 (DWS) 路由工具调用 
，而不是标准代理聊天。
选择 `duo-workflow-*` 模型时， OpenCode 将：

1. 从您的 GitLab 命名空间中发现可用模型
2. 如果有多个模型可用，则显示选择选择器
3. 将所选模型缓存到磁盘以便后续快速启动
4. 通过 OpenCode 的权限门控工具系统路由工具执行请求

可用的 DAP 工作流模型遵循 `duo-workflow-*` 命名约定，并且 
 是从您的 GitLab 实例中动态发现的。

##### GitLab API 工具（可选，但强烈推荐）

要访问 GitLab 工具（合并请求、问题、管道、CI/CD 等）：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-gitlab-plugin"]
}
```

该插件提供全面的 GitLab 存储库管理功能，包括 MR 评论、问题跟踪、管道监控等。

---

### GitHub Copilot

要将您的 GitHub Copilot 订阅与 opencode 一起使用：

**注释：**

某些型号可能需要[Pro+
订阅](<https://github.com/features/copilot/plans>)才能使用。

1. 运行`/connect`命令并搜索GitHub Copilot。

```txt
/connect
```

2. 导航至 [github.com/login/device](<https://github.com/login/device>) 并输入代码。

```txt
┌ Login with GitHub Copilot
│
│ https://github.com/login/device
│
│ Enter code: 8F43-6FCF
│
└ Waiting for authorization...
```

3. 现在运行`/models`命令选择您想要的型号。

```txt
/models
```

---

### GMI 云

要将 GMI 云与 OpenCode 一起使用：

1. 前往 [GMI 云控制台 ](<https://console.gmicloud.ai/>) 创建 API 密钥。您还可以查看 [API 参考 ](<https://docs.gmicloud.ai/inference-engine/api-reference/llm-api-reference>) 以了解端点详细信息。

2. 运行`/connect`命令，搜索**GMI云**。

```txt
/connect
```

3. 输入您的GMI云API密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行【`/models`】命令选择您需要的型号。

```txt
/models
```

---

### Google 顶点 AI

要将 Google 顶点 AI 与 OpenCode 一起使用：

1. 前往 Google Cloud Console 中的 **Model Garden** 并检查
您所在地区提供的型号。

**注释：**

您需要有一个启用了 Vertex AI API 的 Google 云项目。

2. 设置所需的环境变量：
   - `GOOGLE_CLOUD_PROJECT`：您的Google云项目ID
   - `VERTEX_LOCATION`（可选）：顶点区域 AI（默认为 `global`）
   - 身份验证（选择一项）：
     - `GOOGLE_APPLICATION_CREDENTIALS`：您的服务帐户 JSON 密钥文件的路径
     - 使用 gcloud CLI 进行身份验证：`gcloud auth application-default login`

在运行 opencode 时设置它们。

```bash
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json GOOGLE_CLOUD_PROJECT=your-project-id opencode
```

或者将它们添加到您的 bash 配置文件中。

**代码示例：** `~/.bash_profile`

```bash
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
export GOOGLE_CLOUD_PROJECT=your-project-id
export VERTEX_LOCATION=global
```

**提示：**

`global` 区域无需额外成本即可提高可用性并减少错误。使用区域端点（例如，`us-central1`）来满足数据驻留要求。 [了解更多](<https://cloud.google.com/vertex-ai/generative-ai/docs/partner-models/use-partner-models#regional_and_global_endpoints>)

3. 运行【`/models`】命令选择您需要的型号。

```txt
/models
```

---

### 格罗克

1. 前往 [Groq 控制台 ](<https://console.groq.com/>)，单击 **创建 API 密钥 **，然后复制密钥。

2. 运行`/connect`命令并搜索Groq。

```txt
/connect
```

3. 输入提供商的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择您想要的。

```txt
/models
```

---

### 抱脸

[Hugging Face Inference Providers](<https://huggingface.co/docs/inference-providers>) 提供对 17+ 提供商支持的开放模型的访问。

1. 前往 [拥抱脸部设置 ](<https://huggingface.co/settings/tokens/new?ownUserPermissions=inference.serverless.write&tokenType=fineGrained>) 创建一个具有调用推理提供者权限的令牌。

2. 运行`/connect`命令并搜索**Hugging Face**。

```txt
/connect
```

3. 输入您的拥抱脸标记。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择模型，如_Kimi-K2-Instruct_或_GLM-4.6_。

```txt
/models
```

---

### 螺旋锥

[Helicone](<https://helicone.ai>) 是一个 LLM 可观测平台，可为您的 AI 应用程序提供日志记录、监控和分析。 Helicone AI 网关根据模型自动将您的请求路由到适当的提供商。

1. 前往 [Helicone](<https://helicone.ai>)，创建一个帐户，并从仪表板生成 API 密钥。

2. 运行`/connect`命令并搜索**Helicone**。

```txt
/connect
```

3. 输入您的 Helicone API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

有关更多提供程序和高级功能（例如缓存和速率限制），请查看 [Helicone 文档 ](<https://docs.helicone.ai>)。

#### 可选配置

如果您发现 Helicone 的某个功能或型号未通过 opencode 自动配置，您始终可以自行配置。

这是 [Helicone 的模型目录 ](<https://helicone.ai/models>)，您需要它来获取要添加的模型的 ID。

**代码示例：** `~/.config/opencode/opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "helicone": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Helicone",
      "options": {
        "baseURL": "https://ai-gateway.helicone.ai",
      },
      "models": {
        "gpt-4o": {
          // Model ID (from Helicone's model directory page)
          "name": "GPT-4o", // Your own custom name for the model
        },
        "claude-sonnet-4-20250514": {
          "name": "Claude Sonnet 4",
        },
      },
    },
  },
}
```

#### 自定义标头

Helicone 支持缓存、用户跟踪和会话管理等功能的自定义标头。使用 `options.headers` 将它们添加到您的提供商配置中：

**代码示例：** `~/.config/opencode/opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "helicone": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Helicone",
      "options": {
        "baseURL": "https://ai-gateway.helicone.ai",
        "headers": {
          "Helicone-Cache-Enabled": "true",
          "Helicone-User-Id": "opencode",
        },
      },
    },
  },
}
```

##### 会话跟踪

Helicone 的 [Sessions](<https://docs.helicone.ai/features/sessions>) 功能可让您将相关的 LLM 请求分组在一起。使用 [opencode-helicone-session](<https://github.com/H2Shami/opencode-helicone-session>) 插件自动将每个 OpenCode 对话记录为 Helicone 中的会话。

```bash
npm install -g opencode-helicone-session
```

将其添加到您的配置中。

**代码示例：** `opencode.json`

```json
{
  "plugin": ["opencode-helicone-session"]
}
```

该插件将 `Helicone-Session-Id` 和 `Helicone-Session-Name` 标头注入您的请求中。在 Helicone 的会话页面中，您将看到每个 OpenCode 对话都列为单独的会话。

##### 常见 Helicone 接头

|标头|描述|
| -------------------------- | ------------------------------------------------------------- |
| `Helicone-Cache-Enabled`   |启用响应缓存（`true`/`false`）|
| `Helicone-User-Id`         |按用户跟踪指标|
| `Helicone-Property-[Name]` |添加自定义属性（例如，`Helicone-Property-Environment`）|
| `Helicone-Prompt-Id`       |将请求与提示版本相关联|

有关所有可用标头，请参阅 [Helicone 标头目录 ](<https://docs.helicone.ai/helicone-headers/header-directory>)。

---

### 骆驼.cpp

您可以通过 [llama.cpp 的 ](<https://github.com/ggml-org/llama.cpp>) llama-server 实用程序将 opencode 配置为使用本地模型

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llama.cpp": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "llama-server (local)",
      "options": {
        "baseURL": "http://127.0.0.1:8080/v1"
      },
      "models": {
        "qwen3-coder:a3b": {
          "name": "Qwen3-Coder: a3b-30b (local)",
          "limit": {
            "context": 128000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

在这个例子中：

- `llama.cpp` 是自定义提供程序 ID。这可以是您想要的任何字符串。
- `npm` 指定用于此提供程序的包。这里，`@ai-sdk/openai-compatible`用于任何OpenAI兼容的API。
- `name` 是 UI 中提供者的显示名称。
- `options.baseURL` 是本地服务器的端点。
- `models` 是模型 ID 与其配置的映射。型号名称将显示在型号选择列表中。

---

### IO.NET

IO.NET 提供针对各种用例优化的 17 模型：

1. 前往 [IO.NET 控制台 ](<https://ai.io.net/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令，搜索**IO.NET**。

```txt
/connect
```

3. 输入您的 IO.NET API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

---

### LM 工作室

您可以通过LM Studio将opencode配置为使用本地模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "lmstudio": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "LM Studio (local)",
      "options": {
        "baseURL": "http://127.0.0.1:1234/v1"
      },
      "models": {
        "google/gemma-3n-e4b": {
          "name": "Gemma 3n-e4b (local)"
        }
      }
    }
  }
}
```

在这个例子中：

- `lmstudio` 是自定义提供程序 ID。这可以是您想要的任何字符串。
- `npm` 指定用于此提供程序的包。这里，`@ai-sdk/openai-compatible`用于任何OpenAI兼容的API。
- `name` 是 UI 中提供者的显示名称。
- `options.baseURL` 是本地服务器的端点。
- `models` 是模型 ID 与其配置的映射。型号名称将显示在型号选择列表中。

---

### 登月 AI

要使用 Moonshot AI 中的 Kimi K2：

1. Head over to the [Moonshot AI console](<https://platform.moonshot.ai/console>), create an account, and click **Create API key**.

2. 运行`/connect`命令并搜索**Moonshot AI**。

```txt
/connect
```

3. 输入您的 Moonshot API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行`/models`命令，选择_Kimi K2_。

```txt
/models
```

---

### MiniMax

1. 前往 [MiniMax API 控制台 ](<https://platform.minimax.io/login>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**MiniMax**。

```txt
/connect
```

3. 输入您的 MiniMax API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行`/models`命令，选择型号，如_M2.1_。

```txt
/models
```

---

### 莫代尔

1. 为您要使用的模型创建一个 [ 共享端点 ](<https://modal.com/endpoints>)。

2. 创建一个 [ 代理令牌 ](<https://modal.com/docs/guide/endpoints#proxy-tokens>)，然后用句点加入其 ID 和秘密：

```txt
wk-<id>.ws-<secret>
```

3. 运行 `/connect` 命令，搜索 **Modal**，然后输入组合的代理令牌。

```txt
/connect
```

4. 运行 `/models` 命令以选择模态工作区中的端点之一。

```txt
/models
```

---

### NVIDIA

NVIDIA 通过 [build.nvidia.com](<https://build.nvidia.com>) 免费提供 Nemotron 模型和许多其他开放模型的访问。

1. 前往 [build.nvidia.com](<https://build.nvidia.com>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**NVIDIA**。

```txt
/connect
```

3. 输入您的 NVIDIA API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择nemotron-3-super-120b-a12b等型号。

```txt
/models
```

#### 本地 / NIM

您还可以通过设置自定义基础URL，通过[NVIDIA NIM](<https://docs.nvidia.com/nim/>)在本地使用NVIDIA模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia": {
      "options": {
        "baseURL": "http://localhost:8000/v1"
      }
    }
  }
}
```

#### 环境变量

或者，将 API 键设置为环境变量。

```bash
export NVIDIA_API_KEY=nvapi-your-key-here
```

---

### Nebius 代币工厂

1. 前往 [Nebius Token Factory 控制台](<https://tokenfactory.nebius.com/>)，创建一个帐户，然后单击 **添加密钥**。

2. 运行`/connect`命令并搜索**Nebius Token Factory**。

```txt
/connect
```

3. 输入您的 Nebius 令牌工厂 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择一个模型，如_Kimi K2 Instruct_。

```txt
/models
```

---

### 奥拉马

您可以通过 Ollama 将 opencode 配置为使用本地模型。

**提示：**

Ollama 可以自动将自身配置为 OpenCode。有关详细信息，请参阅 [Ollama 集成文档 ](<https://docs.ollama.com/integrations/opencode>)。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "llama2": {
          "name": "Llama 2"
        }
      }
    }
  }
}
```

在这个例子中：

- `ollama` 是自定义提供程序 ID。这可以是您想要的任何字符串。
- `npm` 指定用于此提供程序的包。这里，`@ai-sdk/openai-compatible`用于任何OpenAI兼容的API。
- `name` 是 UI 中提供者的显示名称。
- `options.baseURL` 是本地服务器的端点。
- `models` 是模型 ID 与其配置的映射。型号名称将显示在型号选择列表中。

**提示：**

如果工具调用不起作用，请尝试增加 Ollama 中的 `num_ctx`。从 16k - 32k 左右开始。

---

### 奥拉马云

要将 Ollama Cloud 与 OpenCode 一起使用：

1. 前往 [https://ollama.com/](<https://ollama.com/>）并登录或创建帐户。

2. Navigate to **Settings** > **Keys** and click **Add API Key** to generate a new API key.

3. 复制API密钥以在OpenCode中使用。

4. 运行`/connect`命令并搜索**Ollama Cloud**。

```txt
/connect
```

5. 输入您的 Ollama Cloud API 密钥。

```txt
┌ API key
│
│
└ enter
```

6. **重要**：在使用OpenCode中的云模型之前，必须将模型信息拉取到本地：

```bash
ollama pull gpt-oss:20b-cloud
```

7. 运行 `/models` 命令以选择您的 Ollama Cloud 型号。

```txt
/models
```

---

### OpenAI

我们建议注册 [ChatGPT Plus 或 Pro](<https://chatgpt.com/pricing>)。

1. 注册后，运行 `/connect` 命令并选择 OpenAI。

```txt
/connect
```

2. 在这里您可以选择**ChatGPT Plus/Pro**选项，它将打开您的浏览器
并要求您进行身份验证。

```txt
┌ Select auth method
│
│ ChatGPT Plus/Pro
│ Manually enter API Key
└
```

3. 现在，当您使用 `/models` 命令时，所有 OpenAI 模型都应该可用。

```txt
/models
```

##### 计算驻留

For ChatGPT OAuth, OpenCode automatically applies a regional inference residency requirement when one is advertised by your workspace credentials.它转发凭证中的计算驻留值，而不是维护固定的区域列表。静态数据驻留并不意味着区域推断。

##### 使用 API 键

如果您已有API密钥，您可以选择**手动输入API密钥**并将其粘贴到您的终端中。

---

### OpenCode 禅宗

OpenCode Zen 是 OpenCode 团队提供的经过测试和验证的模型列表。 [了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>)。

1. 登录 **[OpenCode Zen](<https://opencode.ai/auth>)** 并单击 **创建 API 密钥 **。

2. 运行`/connect`命令并搜索**OpenCode Zen**。

```txt
/connect
```

3. 输入您的 OpenCode API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令，选择型号，如_Qwen 3 Coder 480B_。

```txt
/models
```

---

### OpenRouter

1. 前往 [OpenRouter 仪表板 ](<https://openrouter.ai/settings/keys>)，单击 ** 创建 API 密钥 **，然后复制密钥。

2. 运行`/connect`命令并搜索OpenRouter。

```txt
/connect
```

3. 输入提供商的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 默认预加载【OpenRouter】多种型号，运行【`/models`】命令选择您想要的型号。

```txt
/models
```

您还可以通过 opencode 配置添加其他模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

5. 您还可以通过 opencode 配置自定义它们。这是指定提供商的示例

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "models": {
        "moonshotai/kimi-k2": {
          "options": {
            "provider": {
              "order": ["baseten"],
              "allow_fallbacks": false
            }
          }
        }
      }
    }
  }
}
```

---

### LLM 网关

1. 前往 [LLM 网关仪表板 ](<https://llmgateway.io/dashboard>)，单击 ** 创建 API 密钥 **，然后复制密钥。

2. 运行`/connect`命令，搜索LLM网关。

```txt
/connect
```

3. 输入提供商的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 默认预加载LLM多种网关型号，运行`/models`命令选择您想要的型号。

```txt
/models
```

您还可以通过 opencode 配置添加其他模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llmgateway": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

5. 您还可以通过 opencode 配置自定义它们。这是指定提供商的示例

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llmgateway": {
      "models": {
        "glm-4.7": {
          "name": "GLM 4.7"
        },
        "gpt-5.2": {
          "name": "GPT-5.2"
        },
        "gemini-2.5-pro": {
          "name": "Gemini 2.5 Pro"
        },
        "claude-3-5-sonnet-20241022": {
          "name": "Claude 3.5 Sonnet"
        }
      }
    }
  }
}
```

---

### 池畔

[Poolside](<https://poolside.ai>) 通过 OpenAI 兼容的 API 提供对其模型的访问。

1. 前往 [platform.poolside.ai](<https://platform.poolside.ai>)，登录，打开 **API 密钥 ** 选项卡，然后单击 **新密钥 ** 以生成 API 密钥。

2. 运行`/connect`命令并搜索**Poolside**。

```txt
/connect
```

3. 输入您的池畔 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

#### 池畔部署

如果您的组织运行池畔部署，请将其配置为自定义 OpenAI 兼容提供程序。将池畔管理员提供的 API 密钥或令牌存储在 `~/.secrets/poolside-key` 中。使用唯一的提供程序 ID 并添加部署中可用的模型 ID。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "poolside-deployment": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Poolside deployment",
      "options": {
        "baseURL": "https://poolside.example.com/openai/v1",
        "apiKey": "{file:~/.secrets/poolside-key}"
      },
      "models": {
        "<served-model-id>": {
          "name": "Poolside deployment model",
          "reasoning": true,
          "interleaved": {
            "field": "reasoning_content"
          }
        }
      }
    }
  }
}
```

将 `poolside.example.com` 替换为部署的主机，将 `<served-model-id>` 替换为其 `/openai/v1/models` 端点返回的模型 ID。模型可用性和思维行为取决于部署的配置。

**提示：**

您还可以安装 [Poolside Agent CLI](<https://github.com/poolsideai/pool>) 直接从终端使用 Poolside agents。

---

### SAP AI 核心

SAP AI Core 通过统一平台提供对来自 OpenAI、Anthropic、Google、Amazon、Meta、Mistral 和 AI21 的 40+ 模型的访问。

1. Go 到您的 [SAP BTP Cockpit](<https://account.hana.ondemand.com/>)，导航到您的 SAP AI 核心服务实例，然后创建服务密钥。

**提示：**

   服务密钥是 JSON 对象包含 `clientid`, `clientsecret`, `url`, 和 `serviceurls.AI_API_URL`。您可以找到您的 AI 下的核心实例 **服务** > **实例和订阅** 在 BTP 驾驶舱。

2. 运行`/connect`命令，搜索**SAPAICore**。

```txt
/connect
```

3. 输入您的服务密钥 JSON。

```txt
┌ Service key
│
│
└ enter
```

或者设置`AICORE_SERVICE_KEY`环境变量：

```bash
AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","url":"...","serviceurls":{"AI_API_URL":"..."}}' opencode
```

或者将其添加到您的 bash 配置文件中：

**代码示例：** `~/.bash_profile`

```bash
export AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","url":"...","serviceurls":{"AI_API_URL":"..."}}'
```

4. （可选）设置部署 ID 和资源组：

```bash
AICORE_DEPLOYMENT_ID=your-deployment-id AICORE_RESOURCE_GROUP=your-resource-group opencode
```

**注释：**

这些设置是可选的，应根据您的 SAP AI 核心设置进行配置。

5. 运行`/models`命令从40+可用型号中进行选择。

```txt
/models
```

---

### STACKIT

STACKIT AI 模型服务为 AI 模型提供完全托管的主权托管环境，专注于 Llama、Mistral 和 Qwen 等法学硕士，对欧洲基础设施拥有最大的数据主权。

1. 前往 [STACKIT 门户 ](<https://portal.stackit.cloud>)，导航到 **AI 模型服务 **，并为您的项目创建一个身份验证令牌。

**提示：**

在创建身份验证令牌之前，您需要 STACKIT 客户帐户、用户帐户和项目。

2. 运行`/connect`命令并搜索**STACKIT**。

```txt
/connect
```

3. 输入您的 STACKIT AI 模型服务身份验证令牌。

```txt
┌ API key
│
│
└ enter
```

4. 运行 `/models` 命令从可用模型中进行选择，例如 _Qwen3-VL 235B_ 或 _Llama 3.3 70B_。

```txt
/models
```

---

### OVHcloud AI 端点

1. 前往 [OVHcloud 面板 ](<https://ovh.com/manager>)。导航到 `Public Cloud` 部分，`AI & Machine Learning` > `AI Endpoints`，然后在 `API Keys` 选项卡中，单击 **创建新的 API 密钥 **。

2. 运行 `/connect` 命令并搜索 **OVHcloud AI 端点 **。

```txt
/connect
```

3. 输入您的 OVHcloud AI 端点 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择模型，如_gpt-oss-120b_。

```txt
/models
```

---

### 斯卡威

要将 [Scaleway 生成 API](<https://www.scaleway.com/en/docs/generative-apis/>) 与 Opencode 结合使用：

1. 前往 [Scaleway 控制台 IAM 设置 ](<https://console.scaleway.com/iam/api-keys>) 以生成新的 API 密钥。

2. 运行`/connect`命令并搜索**Scaleway**。

```txt
/connect
```

3. 输入您的 Scaleway API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择模型，例如_devstral-2-123b-instruct-2512_或_gpt-oss-120b_。

```txt
/models
```

---

### SCX.ai

[SCX.ai](<https://scx.ai>) 是一个澳大利亚主权 AI 平台，通过 OpenAI 兼容的 API 提供开放模型，托管在澳大利亚的可再生能源基础设施上。

1. 前往 [SCX.ai 平台 ](<https://platform.scx.ai>) 创建帐户并生成 API 密钥。

2. 运行`/connect`命令并搜索**SCX.ai**。

```txt
/connect
```

3. 输入您的 SCX.ai API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行`/models`命令，选择_MiniMax-M2.7_或_GLM-5.2_等模型。

```txt
/models
```

---

### 雪花皮质

[Snowflake Cortex](<https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-rest-api>) 可让您通过 OpenAI 兼容的 API 访问前沿模型（Claude、OpenAI GPT-5 等）。所有推理都在 Snowflake 范围内运行，并以 Snowflake 积分计费。有关每个模型的费率，请参阅 [Snowflake 服务消耗表 ](<https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf>)。

没有雪花帐户？ [注册免费试用](<https://signup.snowflake.com/?utm_source=opencode&utm_medium=docs&utm_campaign=cortex-provider>)。

opencode 编码、编辑文件和运行命令的核心工作流程依赖于工具调用。只有 Snowflake Cortex 中的 Claude 和 OpenAI 系列支持此功能。提供者仅限于那些支持核心工作流程的系列。

OpenCode支持两种认证方式：

- **浏览器OAuth（推荐）** — 使用您的IdP/SSO登录；无需管理秘密，令牌会自动刷新。
- **手动不记名令牌** — 从 Snowflake 控制台粘贴 PAT 或 JWT。

#### 浏览器 OAuth（推荐）

1. 运行`/connect`命令并搜索**Snowflake Cortex**。

```txt
/connect
```

2. 选择**使用Snowflake登录（外部浏览器）**。

```txt
┌ Select auth method
│
│ Login with Snowflake (External Browser)
│ Paste PAT or bearer token manually
└
```

3. 出现提示时输入您的 [ 帐户标识符 ](<https://docs.snowflake.com/en/user-guide/admin-account-identifier>)，例如 `myorg-myaccount` 或 `xy12345.us-east-1`。

```txt
┌ Snowflake Account Identifier
│
│
└ enter
```

4. 可以选择输入 Snowflake 角色来确定会话范围（例如 `SYSADMIN`）。留空以使用您的默认角色。

5. 在打开的浏览器中完成登录。 OpenCode 自动捕获 OAuth 回调并存储令牌 — 不需要 copy/paste。

6. 执行【`/models`】命令选择型号。

```txt
/models
```

**注释：**

浏览器 OAuth 使用 Snowflake 的内置 `SNOWFLAKE$LOCAL_APPLICATION` 安全集成（[docs](<https://docs.snowflake.com/en/user-guide/oauth-local-applications>)），该集成正在向所有帐户推出。要检查您帐户中的可用性：

```sql
SHOW SECURITY INTEGRATIONS LIKE 'SNOWFLAKE$LOCAL_APPLICATION';
```

如果结果为空，请在将集成推广到您的帐户时使用下面的 **手动不记名令牌 ** 方法。

#### 手动不记名令牌

如果您希望直接粘贴令牌，或者您的帐户中尚未提供 `SNOWFLAKE$LOCAL_APPLICATION`：

1. 在您的 Snowflake 帐户中生成 [ 编程访问令牌 (PAT)](<https://docs.snowflake.com/en/user-guide/programmatic-access-tokens>)。

2. 运行`/connect`命令，搜索**Snowflake Cortex**，然后选择**手动粘贴PAT或不记名令牌**。

3. 出现提示时，输入您的 [ 帐户标识符 ](<https://docs.snowflake.com/en/user-guide/admin-account-identifier>)。

4. 粘贴您的 PAT。

5. 执行【`/models`】命令选择型号。

```txt
/models
```

#### 环境变量

对于 CI 或无头环境，请在启动 opencode 之前设置 PAT 或 JWT：

```bash
export SNOWFLAKE_ACCOUNT=myorg-myaccount
export SNOWFLAKE_CORTEX_TOKEN=your-pat-or-jwt
```

**注释：**

`SNOWFLAKE_CORTEX_TOKEN` 仅接受 PAT 或 JWT — 浏览器 OAuth 流程仅可通过 `/connect` 使用，无法通过环境变量进行配置。仍支持 `SNOWFLAKE_CORTEX_PAT` 以实现向后兼容性。

自动提供模型目录。只需要一个最小的 `opencode.json`：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "snowflake-cortex/claude-sonnet-4-6",
  "small_model": "snowflake-cortex/claude-haiku-4-5"
}
```

---

### 一起AI

1. 前往[一起AI控制台](<https://api.together.ai>)，创建一个帐户，然后单击**添加密钥**。

2. 运行`/connect`命令并搜索**一起AI**。

```txt
/connect
```

3. 输入您的 Together AI API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择一个模型，如_Kimi K2 Instruct_。

```txt
/models
```

---

### 威尼斯 AI

1. 前往 [威尼斯 AI 控制台 ](<https://venice.ai>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**Venice AI**。

```txt
/connect
```

3. 输入您的威尼斯 AI API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令，选择模型，如_Llama 3.3 70B_。

```txt
/models
```

---

### Vercel AI 网关

Vercel AI 网关可让您通过统一端点访问来自 OpenAI、Anthropic、Google、xAI 等的模型。型号按标价提供，不加价。

1. 前往 [Vercel 仪表板 ](<https://vercel.com/>)，导航到 **AI 网关 ** 选项卡，然后单击 **API 键 ** 创建一个新的 API 密钥。

2. 运行`/connect`命令并搜索**Vercel AI网关**。

```txt
/connect
```

3. 输入您的 Vercel AI 网关 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 执行【`/models`】命令选择型号。

```txt
/models
```

您还可以通过 opencode 配置自定义模型。以下是指定提供者路由顺序的示例。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "vercel": {
      "models": {
        "anthropic/claude-sonnet-4": {
          "options": {
            "order": ["anthropic", "vertex"]
          }
        }
      }
    }
  }
}
```

一些有用的路由选项：

|选项|描述|
| ------------------- | ---------------------------------------------------- |
| `order`             |提供者尝试顺序|
| `only`              |限制特定提供商|
| `zeroDataRetention` |仅使用零数据保留政策的提供商|

---

### xAI

两种身份验证方式：通过设备代码 OAuth 订阅 SuperGrok 或通过 xAI 控制台按量付费 API 密钥。

#### 选项 A — SuperGrok 订阅

1. 运行`/connect`命令并搜索**xAI**。

```txt
/connect
```

2. 选择**SuperGrok订阅**。 OpenCode 打开 xAI 的验证链接，并在支持时预先填充用户代码。

3. 批准同意屏幕。如果xAI要求输入代码，请输入OpenCode显示的用户代码。 OpenCode 轮询 xAI 的令牌端点，并在您批准后存储生成的 OAuth 令牌。

4. 运行`/models`命令选择Grok型号。

```txt
/models
```

OpenCode 自动刷新 OAuth 访问令牌。任何包含 Grok API 访问权限的 Grok 或 X Premium 计划均有效；您不需要单独的 `XAI_API_KEY`。

#### 选项 B — API 键

1. 前往 [xAI 控制台 ](<https://console.x.ai/>)，创建一个帐户，并生成一个 API 密钥。

2. 运行`/connect`命令并搜索**xAI**。

```txt
/connect
```

3. 选择**手动输入API密钥**并粘贴您的xAIAPI密钥。

```txt
┌ API key
│
│
└ enter
```

4. 运行`/models`命令选择模型，如_Grok Beta_。

```txt
/models
```

---

### Z.AI

1. 前往 [Z.AI API 控制台](<https://z.ai/manage-apikey/apikey-list>)，创建一个帐户，然后单击 **创建一个新的 API 密钥 **。

2. 运行`/connect`命令，搜索**Z.AI**。

```txt
/connect
```

如果您订阅了**GLM编码计划**，请选择**Z.AI编码计划**。

3. 输入您的 Z.AI API 键。

```txt
┌ API key
│
│
└ enter
```

4. 执行`/models`命令，选择模型，如_GLM-4.7_。

```txt
/models
```

---

### ZenMux

1. 前往 [ZenMux 仪表板 ](<https://zenmux.ai/settings/keys>)，单击 ** 创建 API 密钥 **，然后复制密钥。

2. 运行`/connect`命令并搜索ZenMux。

```txt
/connect
```

3. 输入提供商的 API 密钥。

```txt
┌ API key
│
│
└ enter
```

4. 默认预加载【ZenMux】多种型号，运行【`/models`】命令选择您想要的型号。

```txt
/models
```

您还可以通过 opencode 配置添加其他模型。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "zenmux": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

---

## 定制提供商

要添加 `/connect` 命令中未列出的任何 **OpenAI 兼容的 ** 提供程序：

**提示：**

您可以将任何 OpenAI 兼容的提供程序与 opencode 一起使用。大多数现代 AI 提供商提供 OpenAI 兼容的 API。

1. 运行`/connect`命令并向下滚动到**其他**。

```bash
$ /connect

┌  Add credential
│
◆  Select provider
│  ...
│  ● Other
└
```

2. 输入提供商的唯一 ID。

```bash
$ /connect

┌  Add credential
│
◇  Enter provider id
│  myprovider
└
```

**注释：**

选择一个令人难忘的 ID，您将在配置文件中使用它。

3. 输入提供商的 API 密钥。

```bash
$ /connect

┌  Add credential
│
▲  This only stores a credential for myprovider - you will need to configure it in opencode.json, check the docs for examples.
│
◇  Enter your API key
│  sk-...
└
```

4. 在项目目录中创建或更新 `opencode.json` 文件：

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "My AI Provider Display Name",
      "options": {
        "baseURL": "https://api.myprovider.com/v1"
      },
      "models": {
        "my-model-name": {
          "name": "My Model Display Name"
        }
      }
    }
  }
}
```

以下是配置选项：
   - **npm**：AI SDK 要使用的包，`@ai-sdk/openai-compatible` 用于 OpenAI 兼容的提供程序（对于 `/v1/chat/completions`）。如果您的provider/model使用`/v1/responses`，请使用`@ai-sdk/openai`。
   - **名称**：UI中的显示名称。
   - **型号**：可用型号。
   - **选项。baseURL**：API端点URL。
   - **选项。apiKey**：如果不使用身份验证，可以选择设置API密钥。
   - **options.headers**：可以选择设置自定义标头。

有关高级选项的更多信息，请参见下面的示例。

5. 运行 `/models` 命令，您的自定义提供程序和模型将出现在选择列表中。

---

##### 示例

以下是设置 `apiKey`、`headers` 和型号 `limit` 选项的示例。

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "My AI Provider Display Name",
      "options": {
        "baseURL": "https://api.myprovider.com/v1",
        "apiKey": "{env:ANTHROPIC_API_KEY}",
        "headers": {
          "Authorization": "Bearer custom-token"
        }
      },
      "models": {
        "my-model-name": {
          "name": "My Model Display Name",
          "limit": {
            "context": 200000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

配置详情：

- **apiKey**：使用`env`变量语法设置，[了解更多](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#env-vars>)。
- **headers**：随每个请求发送的自定义标头。
- **limit.context**：模型接受的最大输入标记。
- **limit.output**：模型可以生成的最大令牌数。

`limit` 字段允许 OpenCode 了解您还剩下多少上下文。标准提供商会自动从 models.dev 中提取这些内容。

---

## 故障排除

如果您在配置提供商时遇到问题，请检查以下内容：

1. **检查身份验证设置**：运行`opencode auth list`以查看凭据是否
提供商的配置已添加到您的配置中。

这不适用于 Amazon Bedrock 等依赖环境变量进行身份验证的提供商。

2. 对于自定义提供程序，请检查 opencode 配置并：
   - 确保 `/connect` 命令中使用的提供程序 ID 与 opencode 配置中的 ID 匹配。
   - 正确的 npm 包用于提供程序。例如，对 Cerebras 使用 `@ai-sdk/cerebras`。对于所有其他 OpenAI 兼容的提供程序，请使用 `@ai-sdk/openai-compatible`（对于 `/v1/chat/completions`）；如果模型使用 `/v1/responses`，请使用 `@ai-sdk/openai`。对于一个提供商下的混合设置，您可以通过 `provider.npm` 覆盖每个模型。
   - 检查 `options.baseURL` 字段中使用的 API 端点是否正确。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode uses the [AI SDK](<https://ai-sdk.dev/>) and [Models.dev](<https://models.dev>) to support **75+ LLM providers** and it supports running local models.

To add a provider you need to:

1. Add the API keys for the provider using the `/connect` command.
2. Configure the provider in your OpenCode config.

---

### Credentials

When you add a provider's API keys with the `/connect` command, they are stored
in `~/.local/share/opencode/auth.json`.

---

### Config

You can customize the providers through the `provider` section in your OpenCode
config.

---

#### Base URL

You can customize the base URL for any provider by setting the `baseURL` option. This is useful when using proxy services or custom endpoints.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "options": {
        "baseURL": "https://api.anthropic.com/v1"
      }
    }
  }
}
```

---

#### Hiding models

You can hide specific models from the `/models` picker for a provider using the `blacklist` option. This is useful when a provider exposes models you don't want to use or select.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "blacklist": ["claude-opus-4-20250514"]
    }
  }
}
```

The inverse `whitelist` option hides every model except the ones listed.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "whitelist": ["claude-sonnet-4-20250514"]
    }
  }
}
```

Both options take an array of model IDs — the same IDs shown in the `/models` picker.

- `blacklist` removes the listed models from the picker.
- `whitelist` keeps only the listed models and hides the rest.
- You can combine them: `whitelist` narrows the set, then `blacklist` removes entries from it.

---

## OpenCode Zen

OpenCode Zen is a list of models provided by the OpenCode team that have been
tested and verified to work well with OpenCode. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>).

**提示：**

If you are new, we recommend starting with OpenCode Zen.

1. Run the `/connect` command in the TUI, select `OpenCode Zen`, and head to [opencode.ai/auth](<https://opencode.ai/zen>).

```txt
/connect
```

2. Sign in, add your billing details, and copy your API key.

3. Paste your API key.

```txt
┌ API key
│
│
└ enter
```

4. Run `/models` in the TUI to see the list of models we recommend.

```txt
/models
```

It works like any other provider in OpenCode and is completely optional to use.

---

## OpenCode Go

OpenCode Go is a low cost subscription plan that provides reliable access to popular open coding models provided by the OpenCode team that have been
tested and verified to work well with OpenCode.

1. Run the `/connect` command in the TUI, select `OpenCode Go`, and head to [opencode.ai/auth](<https://opencode.ai/zen>).

```txt
/connect
```

2. Sign in, add your billing details, and copy your API key.

3. Paste your API key.

```txt
┌ API key
│
│
└ enter
```

4. Run `/models` in the TUI to see the list of models we recommend.

```txt
/models
```

It works like any other provider in OpenCode and is completely optional to use.

---

## Directory

Let's look at some of the providers in detail. If you'd like to add a provider to the
list, feel free to open a PR.

**注释：**

Don't see a provider here? Submit a PR.

---

### 302.AI

1. Head over to the [302.AI console](<https://302.ai/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **302.AI**.

```txt
/connect
```

3. Enter your 302.AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

---

### Amazon Bedrock

To use Amazon Bedrock with OpenCode:

1. Head over to the **Model catalog** in the Amazon Bedrock console and request
   access to the models you want.

**提示：**

   You need to have access to the model you want in Amazon Bedrock.

2. **Configure authentication** using one of the following methods:

   ***

   #### Environment Variables (Quick Start)

   Set one of these environment variables while running opencode:

```bash
# Option 1: Using AWS access keys
AWS_ACCESS_KEY_ID=XXX AWS_SECRET_ACCESS_KEY=YYY opencode

# Option 2: Using named AWS profile
AWS_PROFILE=my-profile opencode

# Option 3: Using Bedrock bearer token
AWS_BEARER_TOKEN_BEDROCK=XXX opencode
```

   Or add them to your bash profile:

**代码示例：** `~/.bash_profile`

```bash
export AWS_PROFILE=my-dev-profile
export AWS_REGION=us-east-1
```

   ***

   #### Configuration File (Recommended)

   For project-specific or persistent configuration, use `opencode.json`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "my-aws-profile"
      }
    }
  }
}
```

   **Available options:**
   - `region` - AWS region (e.g., `us-east-1`, `eu-west-1`)
   - `profile` - AWS named profile from `~/.aws/credentials`
   - `endpoint` - Custom endpoint URL for VPC endpoints (alias for generic `baseURL` option)

**提示：**

   Configuration file options take precedence over environment variables.

   ***

   #### Advanced: VPC Endpoints

   If you're using VPC endpoints for Bedrock:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      "options": {
        "region": "us-east-1",
        "profile": "production",
        "endpoint": "https://bedrock-runtime.us-east-1.vpce-xxxxx.amazonaws.com"
      }
    }
  }
}
```

**注释：**

   The `endpoint` option is an alias for the generic `baseURL` option, using AWS-specific terminology. If both `endpoint` and `baseURL` are specified, `endpoint` takes precedence.

   ***

   #### Authentication Methods
   - **`AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`**: Create an IAM user and generate access keys in the AWS Console
   - **`AWS_PROFILE`**: Use named profiles from `~/.aws/credentials`. First configure with `aws configure --profile my-profile` or `aws sso login`
   - **`AWS_BEARER_TOKEN_BEDROCK`**: Generate long-term API keys from the Amazon Bedrock console
   - **`AWS_WEB_IDENTITY_TOKEN_FILE` / `AWS_ROLE_ARN`**: For EKS IRSA (IAM Roles for Service Accounts) or other Kubernetes environments with OIDC federation. These environment variables are automatically injected by Kubernetes when using service account annotations.

   ***

   #### Authentication Precedence

   Amazon Bedrock uses the following authentication priority:
   1. **Bearer Token** - `AWS_BEARER_TOKEN_BEDROCK` environment variable or token from `/connect` command
   2. **AWS Credential Chain** - Profile, access keys, shared credentials, IAM roles, Web Identity Tokens (EKS IRSA), instance metadata

**注释：**

   When a bearer token is set (via `/connect` or `AWS_BEARER_TOKEN_BEDROCK`), it takes precedence over all AWS credential methods including configured profiles.

3. Run the `/models` command to select the model you want.

```txt
/models
```

**注释：**

For custom inference profiles, use the model and provider name in the key and set the `id` property to the arn. This ensures correct caching.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "amazon-bedrock": {
      // ...
      "models": {
        "anthropic-claude-sonnet-4.5": {
          "id": "arn:aws:bedrock:us-east-1:xxx:application-inference-profile/yyy"
        }
      }
    }
  }
}
```

---

### Anthropic

1. Once you've signed up, run the `/connect` command and select Anthropic.

```txt
/connect
```

2. Here you can select the **Claude Pro/Max** option and it'll open your browser
   and ask you to authenticate.

```txt
┌ Select auth method
│
│ Manually enter API Key
└
```

3. Now all the Anthropic models should be available when you use the `/models` command.

```txt
/models
```

**说明：**

There are plugins that allow you to use your Claude Pro/Max models with
OpenCode. Anthropic explicitly prohibits this.

Previous versions of OpenCode came bundled with these plugins but that is no
longer the case as of 1.3.0

Other companies support freedom of choice with developer tooling - you can use
the following subscriptions in OpenCode with zero setup:

- ChatGPT Plus
- Github Copilot
- Gitlab Duo

---

### Atomic Chat

You can configure opencode to use local models through [Atomic Chat](<https://atomic.chat>), a desktop application that runs local LLMs behind an OpenAI-compatible API server (default endpoint `http://127.0.0.1:1337/v1`).

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "atomic-chat": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Atomic Chat (local)",
      "options": {
        "baseURL": "http://127.0.0.1:1337/v1"
      },
      "models": {
        "<your-model-id>": {
          "name": "<your-model-name>"
        }
      }
    }
  }
}
```

In this example:

- `atomic-chat` is the custom provider ID. This can be any string you want.
- `npm` specifies the package to use for this provider. Here, `@ai-sdk/openai-compatible` is used for any OpenAI-compatible API.
- `name` is the display name for the provider in the UI.
- `options.baseURL` is the endpoint for the local server. Change the host and port to match your Atomic Chat setup.
- `models` is a map of model IDs to their display names. Each ID must match the `id` returned by `GET /v1/models` — run `curl http://127.0.0.1:1337/v1/models` to list the ids currently loaded in Atomic Chat.

**提示：**

If tool calls aren't working well, pick a loaded model with strong tool-calling support (for example, a Qwen-Coder or DeepSeek-Coder variant).

---

### Azure OpenAI

**注释：**

If you encounter "I'm sorry, but I cannot assist with that request" errors, try changing the content filter from **DefaultV2** to **Default** in your Azure resource.

1. Head over to the [Azure portal](<https://portal.azure.com/>) and create an **Azure OpenAI** resource. You'll need:
   - **Resource name**: This becomes part of your API endpoint (`https://RESOURCE_NAME.openai.azure.com/`)
   - **API key**: Either `KEY 1` or `KEY 2` from your resource

2. Go to [Azure AI Foundry](<https://ai.azure.com/>) and deploy a model.

**注释：**

   The deployment name must match the model name for opencode to work properly.

3. Run the `/connect` command and search for **Azure**.

```txt
/connect
```

4. Enter your API key.

```txt
┌ API key
│
│
└ enter
```

5. Set your resource name as an environment variable:

```bash
AZURE_RESOURCE_NAME=XXX opencode
```

   Or add it to your bash profile:

**代码示例：** `~/.bash_profile`

```bash
export AZURE_RESOURCE_NAME=XXX
```

6. Run the `/models` command to select your deployed model.

```txt
/models
```

#### Microsoft Entra ID (Azure CLI)

You can use your Azure CLI session instead of an API key. [Install the Azure CLI](<https://learn.microsoft.com/en-us/cli/azure/install-azure-cli>), run `az login`, then run `/connect`, select **Azure**, and choose **Microsoft Entra ID (Azure CLI)**. Enter the Azure Resource name when prompted. Use `az login --tenant TENANT_ID` if the Resource belongs to a different tenant.

Find the Resource name by opening your Azure OpenAI or Foundry Resource in the [Azure portal](<https://portal.azure.com/>) or [Microsoft Foundry](<https://ai.azure.com/>). It is also the first part of the endpoint: `my-models` in `https://my-models.openai.azure.com/` or `https://my-models.services.ai.azure.com/`. If your identity can list Resources, you can also find their names and Resource groups with:

```bash
az cognitiveservices account list \
  --query "[].{name:name,resourceGroup:resourceGroup}" \
  --output table
```

OpenCode does not query Azure management APIs or discover deployments. Select a model whose catalog name matches your deployment, or configure its deployment name explicitly:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "azure": {
      "models": {
        "gpt-5-mini": {
          "id": "gpt-production"
        }
      }
    }
  }
}
```

Assign your identity the inference role required by the deployment: **Cognitive Services OpenAI User** for Azure OpenAI models or **Cognitive Services User** for other Foundry models. OpenCode refreshes access tokens through the Azure CLI, including versions earlier than 2.54.0, so you only need to sign in again when the CLI session expires.

---

### Azure Cognitive Services

1. Head over to the [Azure portal](<https://portal.azure.com/>) and create an **Azure OpenAI** resource. You'll need:
   - **Resource name**: This becomes part of your API endpoint (`https://AZURE_COGNITIVE_SERVICES_RESOURCE_NAME.cognitiveservices.azure.com/`)
   - **API key**: Either `KEY 1` or `KEY 2` from your resource

2. Go to [Azure AI Foundry](<https://ai.azure.com/>) and deploy a model.

**注释：**

   The deployment name must match the model name for opencode to work properly.

3. Run the `/connect` command and search for **Azure Cognitive Services**.

```txt
/connect
```

4. Enter your API key.

```txt
┌ API key
│
│
└ enter
```

5. Set your resource name as an environment variable:

```bash
AZURE_COGNITIVE_SERVICES_RESOURCE_NAME=XXX opencode
```

   Or add it to your bash profile:

**代码示例：** `~/.bash_profile`

```bash
export AZURE_COGNITIVE_SERVICES_RESOURCE_NAME=XXX
```

6. Run the `/models` command to select your deployed model.

```txt
/models
```

---

### Baseten

1. Head over to the [Baseten](<https://app.baseten.co/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **Baseten**.

```txt
/connect
```

3. Enter your Baseten API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

---

### Cerebras

1. Head over to the [Cerebras console](<https://inference.cerebras.ai/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **Cerebras**.

```txt
/connect
```

3. Enter your Cerebras API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Qwen 3 Coder 480B_.

```txt
/models
```

---

### Cloudflare AI Gateway

Cloudflare AI Gateway lets you access models from OpenAI, Anthropic, Workers AI, and more through a unified endpoint. With [Unified Billing](<https://developers.cloudflare.com/ai-gateway/features/unified-billing/>) you don't need separate API keys for each provider.

1. Head over to the [Cloudflare dashboard](<https://dash.cloudflare.com/>), navigate to **AI** > **AI Gateway**, and create a new gateway. Note your **Account ID** and **Gateway ID**.

2. Run the `/connect` command and search for **Cloudflare AI Gateway**.

```txt
/connect
```

3. Enter your **Account ID** when prompted.

```txt
┌ Enter your Cloudflare Account ID
│
│
└ enter
```

4. Enter your **Gateway ID** when prompted.

```txt
┌ Enter your Cloudflare AI Gateway ID
│
│
└ enter
```

5. Enter your **Cloudflare API token**.

```txt
┌ Gateway API token
│
│
└ enter
```

6. Run the `/models` command to select a model.

```txt
/models
```

   You can also add models through your opencode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "cloudflare-ai-gateway": {
      "models": {
        "openai/gpt-4o": {},
        "anthropic/claude-sonnet-4": {}
      }
    }
  }
}
```

   Alternatively, you can set environment variables instead of using `/connect`.

**代码示例：** `~/.bash_profile`

```bash
export CLOUDFLARE_ACCOUNT_ID=your-32-character-account-id
export CLOUDFLARE_GATEWAY_ID=your-gateway-id
export CLOUDFLARE_API_TOKEN=your-api-token
```

---

### Cloudflare Workers AI

Cloudflare Workers AI lets you run AI models on Cloudflare's global network directly via REST API, with no separate provider accounts needed for supported models.

1. Head over to the [Cloudflare dashboard](<https://dash.cloudflare.com/>), navigate to **Workers AI**, and select **Use REST API** to get your **Account ID** and create an API token.

2. Run the `/connect` command and search for **Cloudflare Workers AI**.

```txt
/connect
```

3. Enter your **Account ID** when prompted.

```txt
┌ Enter your Cloudflare Account ID
│
│
└ enter
```

4. Enter your **Cloudflare API key**.

```txt
┌ API key
│
│
└ enter
```

5. Run the `/models` command to select a model.

```txt
/models
```

   Alternatively, you can set environment variables instead of using `/connect`.

**代码示例：** `~/.bash_profile`

```bash
export CLOUDFLARE_ACCOUNT_ID=your-32-character-account-id
export CLOUDFLARE_API_KEY=your-api-token
```

---

### Cortecs

1. Head over to the [Cortecs console](<https://cortecs.ai/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **Cortecs**.

```txt
/connect
```

3. Enter your Cortecs API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Kimi K2 Instruct_.

```txt
/models
```

---

### DeepSeek

1. Head over to the [DeepSeek console](<https://platform.deepseek.com/>), create an account, and click **Create new API key**.

2. Run the `/connect` command and search for **DeepSeek**.

```txt
/connect
```

3. Enter your DeepSeek API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a DeepSeek model like _DeepSeek V4 Pro_.

```txt
/models
```

---

### Deep Infra

1. Head over to the [Deep Infra dashboard](<https://deepinfra.com/dash>), create an account, and generate an API key.

2. Run the `/connect` command and search for **Deep Infra**.

```txt
/connect
```

3. Enter your Deep Infra API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

---

### DigitalOcean

DigitalOcean's [Inference Engine](<https://docs.digitalocean.com/products/inference/>) provides access to open models like GPT-OSS, Llama, Qwen, and DeepSeek, plus custom [Inference Routers](<https://docs.digitalocean.com/products/inference/how-to/use-inference-router/>) that route each request to the cheapest, fastest, or best-fit model for a task.

OpenCode supports two authentication methods:

- **OAuth (Recommended)** — Sign in to your DigitalOcean account; OpenCode uses your DigitalOcean API token directly for inference and discovers your Inference Routers.
- **Model Access Key** — Paste an existing key from the DigitalOcean console.

#### OAuth (Recommended)

1. Run the `/connect` command and search for **DigitalOcean**.

```txt
/connect
```

2. Select **Login with DigitalOcean**.

```txt
┌ Select auth method
│
│ Login with DigitalOcean
│ Paste Model Access Key
└
```

3. Your browser opens to authorize OpenCode. Sign in and approve.

**注释：**

   OpenCode requests `genai:read` and `inference:query` OAuth scopes. Your DigitalOcean API token is used directly for inference — no separate Model Access Key is created.

**注释：**

   Inference Routers only appear in the model picker after OAuth. Pasting a Model Access Key manually does not discover routers.

4. Run the `/models` command. Your Inference Routers appear as the format `router:` in the model selection.

```txt
/models
```

5. To pick up newly created Inference Routers, re-run `/connect` and select **DigitalOcean** again.

#### Using a Model Access Key

If you'd rather paste a key directly:

1. Head over to the **Manage** page in the Inference section of the [DigitalOcean console](<https://cloud.digitalocean.com/>) and create a new key.

2. Run the `/connect` command and select **DigitalOcean**, then **Paste Model Access Key**.

```txt
┌ Enter your DigitalOcean Model Access Key
│
│
└ enter
```

**注释：**

   Inference Routers are not auto-discovered with this method. To surface them in the model picker, sign in via OAuth instead.

3. Run the `/models` command to select a model.

```txt
/models
```

#### Environment Variable

Alternatively, set your Model Access Key as an environment variable.

```bash
export DIGITALOCEAN_ACCESS_TOKEN=your-model-access-key
```

#### Inference Routers

Inference Routers let you define a routing policy across multiple models — picking the cheapest, fastest, or most appropriate model per request based on the task. After OAuth, OpenCode surfaces each router as `router:<router-name>` in the model picker.

Selecting a router model is a drop-in replacement for any other model — OpenCode forwards your request and DigitalOcean picks the underlying model based on your router's policy. Learn more about [Inference Routers](<https://docs.digitalocean.com/products/inference/how-to/use-inference-router/>)

---

### Eden AI

[Eden AI](<https://www.edenai.co/>) is an EU-based gateway that serves models from many vendors over a single OpenAI-compatible API, with a separate EU endpoint for teams that need inference to stay in the EU.

1. Head over to the [Eden AI platform](<https://app.edenai.run/user/register>) to create an account and generate an API key.

2. Run the `/connect` command and search for **Eden AI**.

```txt
/connect
```

3. Enter your Eden AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Mistral Large 3_ or _Claude Sonnet 5_.

```txt
/models
```

   Eden AI model ids are themselves in `vendor/model` form, so a full reference has three segments, for example `edenai/anthropic/claude-sonnet-5`.

5. To keep requests on Eden AI's EU gateway, set its base URL.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "edenai": {
      "options": {
        "baseURL": "https://api.eu.edenai.run/v3"
      }
    }
  }
}
```

   The default is `https://api.edenai.run/v3`, so this swaps the global endpoint for the EU one. The EU endpoint serves the subset of the catalog that is available in the EU, so a model chosen in step 4 may not be reachable through it.

---

### FrogBot

1. Head over to the [FrogBot dashboard](<https://app.frogbot.ai/signup>), create an account, and generate an API key.

2. Run the `/connect` command and search for **FrogBot**.

```txt
/connect
```

3. Enter your FrogBot API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

---

### Fireworks AI

1. Head over to the [Fireworks AI console](<https://app.fireworks.ai/>), create an account, and click **Create API Key**.

2. Run the `/connect` command and search for **Fireworks AI**.

```txt
/connect
```

3. Enter your Fireworks AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Kimi K2 Instruct_.

```txt
/models
```

---

### GitLab Duo

**注意：Experimental：**

GitLab Duo support in OpenCode is experimental. Features, configuration, and
behavior may change in future releases.

OpenCode integrates with the [GitLab Duo Agent Platform](<https://docs.gitlab.com/user/duo_agent_platform/>),
providing AI-powered agentic chat with native tool calling capabilities.

**注释：License requirements：**

GitLab Duo Agent Platform requires a **Premium** or **Ultimate** GitLab
subscription. It is available on GitLab.com and GitLab Self-Managed.
See [GitLab Duo Agent Platform prerequisites](<https://docs.gitlab.com/user/duo_agent_platform/#prerequisites>)
for full requirements.

1. Run the `/connect` command and select GitLab.

```txt
/connect
```

2. Choose your authentication method:

```txt
┌ Select auth method
│
│ OAuth (Recommended)
│ Personal Access Token
└
```

   #### Using OAuth (Recommended)

   Select **OAuth** and your browser will open for authorization.

   #### Using Personal Access Token
   1. Go to [GitLab User Settings > Access Tokens](<https://gitlab.com/-/user_settings/personal_access_tokens>)
   2. Click **Add new token**
   3. Name: `OpenCode`, Scopes: `api`
   4. Copy the token (starts with `glpat-`)
   5. Enter it in the terminal

3. Run the `/models` command to see available models.

```txt
/models
```

   Three Claude-based models are available:
   - **duo-chat-haiku-4-5** (Default) - Fast responses for quick tasks
   - **duo-chat-sonnet-4-5** - Balanced performance for most workflows
   - **duo-chat-opus-4-5** - Most capable for complex analysis

**注释：**

You can also specify 'GITLAB_TOKEN' environment variable if you don't want
to store token in opencode auth storage.

##### Self-Hosted GitLab

**注释：compliance note：**

OpenCode uses a small model for some AI tasks like generating the session title.
It is configured to use gpt-5-nano by default, hosted by Zen. To lock OpenCode
to only use your own GitLab-hosted instance, add the following to your
`opencode.json` file. It is also recommended to disable session sharing.

```json
{
  "$schema": "https://opencode.ai/config.json",
  "small_model": "gitlab/duo-chat-haiku-4-5",
  "share": "disabled"
}
```

For self-hosted GitLab instances:

```bash
export GITLAB_INSTANCE_URL=https://gitlab.company.com
export GITLAB_TOKEN=glpat-...
```

If your instance runs a custom AI Gateway:

```bash
GITLAB_AI_GATEWAY_URL=https://ai-gateway.company.com
```

Or add to your bash profile:

**代码示例：** `~/.bash_profile`

```bash
export GITLAB_INSTANCE_URL=https://gitlab.company.com
export GITLAB_AI_GATEWAY_URL=https://ai-gateway.company.com
export GITLAB_TOKEN=glpat-...
```

**注释：**

Your GitLab administrator must:

1. [Turn on GitLab Duo](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-gitlab-duo-on-or-off>)
   for the user, group, or instance
2. [Turn on the Agent Platform](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-gitlab-duo-agent-platform-on-or-off>)
   (GitLab 18.8+) or [enable beta and experimental features](<https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/#turn-on-beta-and-experimental-features>)
   (GitLab 18.7 and earlier)
3. For Self-Managed, [configure your instance](<https://docs.gitlab.com/administration/gitlab_duo/configure/gitlab_self_managed/>)

##### OAuth for Self-Hosted instances

In order to make Oauth working for your self-hosted instance, you need to create
a new application (Settings → Applications) with the
callback URL `http://127.0.0.1:8080/callback` and following scopes:

- api (Access the API on your behalf)
- read_user (Read your personal information)
- read_repository (Allows read-only access to the repository)

Then expose application ID as environment variable:

```bash
export GITLAB_OAUTH_CLIENT_ID=your_application_id_here
```

More documentation on [opencode-gitlab-auth](<https://www.npmjs.com/package/opencode-gitlab-auth>) homepage.

##### Configuration

Customize through `opencode.json`:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "gitlab": {
      "options": {
        "instanceUrl": "https://gitlab.com"
      }
    }
  }
}
```

##### GitLab Duo Agent Platform (DAP) Workflow Models

DAP workflow models provide an alternative execution path that routes tool calls
through GitLab's Duo Workflow Service (DWS) instead of the standard agentic chat.
When a `duo-workflow-*` model is selected, OpenCode will:

1. Discover available models from your GitLab namespace
2. Present a selection picker if multiple models are available
3. Cache the selected model to disk for fast subsequent startups
4. Route tool execution requests through OpenCode's permission-gated tool system

Available DAP workflow models follow the `duo-workflow-*` naming convention and
are dynamically discovered from your GitLab instance.

##### GitLab API Tools (Optional, but highly recommended)

To access GitLab tools (merge requests, issues, pipelines, CI/CD, etc.):

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["opencode-gitlab-plugin"]
}
```

This plugin provides comprehensive GitLab repository management capabilities including MR reviews, issue tracking, pipeline monitoring, and more.

---

### GitHub Copilot

To use your GitHub Copilot subscription with opencode:

**注释：**

Some models might need a [Pro+
subscription](<https://github.com/features/copilot/plans>) to use.

1. Run the `/connect` command and search for GitHub Copilot.

```txt
/connect
```

2. Navigate to [github.com/login/device](<https://github.com/login/device>) and enter the code.

```txt
┌ Login with GitHub Copilot
│
│ https://github.com/login/device
│
│ Enter code: 8F43-6FCF
│
└ Waiting for authorization...
```

3. Now run the `/models` command to select the model you want.

```txt
/models
```

---

### GMI Cloud

To use GMI Cloud with OpenCode:

1. Head over to the [GMI Cloud console](<https://console.gmicloud.ai/>) to create an API key. You can also review the [API reference](<https://docs.gmicloud.ai/inference-engine/api-reference/llm-api-reference>) for the endpoint details.

2. Run the `/connect` command and search for **GMI Cloud**.

```txt
/connect
```

3. Enter your GMI Cloud API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select the model you want.

```txt
/models
```

---

### Google Vertex AI

To use Google Vertex AI with OpenCode:

1. Head over to the **Model Garden** in the Google Cloud Console and check the
   models available in your region.

**注释：**

   You need to have a Google Cloud project with Vertex AI API enabled.

2. Set the required environment variables:
   - `GOOGLE_CLOUD_PROJECT`: Your Google Cloud project ID
   - `VERTEX_LOCATION` (optional): The region for Vertex AI (defaults to `global`)
   - Authentication (choose one):
     - `GOOGLE_APPLICATION_CREDENTIALS`: Path to your service account JSON key file
     - Authenticate using gcloud CLI: `gcloud auth application-default login`

   Set them while running opencode.

```bash
GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json GOOGLE_CLOUD_PROJECT=your-project-id opencode
```

   Or add them to your bash profile.

**代码示例：** `~/.bash_profile`

```bash
export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
export GOOGLE_CLOUD_PROJECT=your-project-id
export VERTEX_LOCATION=global
```

**提示：**

The `global` region improves availability and reduces errors at no extra cost. Use regional endpoints (e.g., `us-central1`) for data residency requirements. [Learn more](<https://cloud.google.com/vertex-ai/generative-ai/docs/partner-models/use-partner-models#regional_and_global_endpoints>)

3. Run the `/models` command to select the model you want.

```txt
/models
```

---

### Groq

1. Head over to the [Groq console](<https://console.groq.com/>), click **Create API Key**, and copy the key.

2. Run the `/connect` command and search for Groq.

```txt
/connect
```

3. Enter the API key for the provider.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select the one you want.

```txt
/models
```

---

### Hugging Face

[Hugging Face Inference Providers](<https://huggingface.co/docs/inference-providers>) provides access to open models supported by 17+ providers.

1. Head over to [Hugging Face settings](<https://huggingface.co/settings/tokens/new?ownUserPermissions=inference.serverless.write&tokenType=fineGrained>) to create a token with permission to make calls to Inference Providers.

2. Run the `/connect` command and search for **Hugging Face**.

```txt
/connect
```

3. Enter your Hugging Face token.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Kimi-K2-Instruct_ or _GLM-4.6_.

```txt
/models
```

---

### Helicone

[Helicone](<https://helicone.ai>) is an LLM observability platform that provides logging, monitoring, and analytics for your AI applications. The Helicone AI Gateway routes your requests to the appropriate provider automatically based on the model.

1. Head over to [Helicone](<https://helicone.ai>), create an account, and generate an API key from your dashboard.

2. Run the `/connect` command and search for **Helicone**.

```txt
/connect
```

3. Enter your Helicone API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

For more providers and advanced features like caching and rate limiting, check the [Helicone documentation](<https://docs.helicone.ai>).

#### Optional Configs

In the event you see a feature or model from Helicone that isn't configured automatically through opencode, you can always configure it yourself.

Here's [Helicone's Model Directory](<https://helicone.ai/models>), you'll need this to grab the IDs of the models you want to add.

**代码示例：** `~/.config/opencode/opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "helicone": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Helicone",
      "options": {
        "baseURL": "https://ai-gateway.helicone.ai",
      },
      "models": {
        "gpt-4o": {
          // Model ID (from Helicone's model directory page)
          "name": "GPT-4o", // Your own custom name for the model
        },
        "claude-sonnet-4-20250514": {
          "name": "Claude Sonnet 4",
        },
      },
    },
  },
}
```

#### Custom Headers

Helicone supports custom headers for features like caching, user tracking, and session management. Add them to your provider config using `options.headers`:

**代码示例：** `~/.config/opencode/opencode.jsonc`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "helicone": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Helicone",
      "options": {
        "baseURL": "https://ai-gateway.helicone.ai",
        "headers": {
          "Helicone-Cache-Enabled": "true",
          "Helicone-User-Id": "opencode",
        },
      },
    },
  },
}
```

##### Session tracking

Helicone's [Sessions](<https://docs.helicone.ai/features/sessions>) feature lets you group related LLM requests together. Use the [opencode-helicone-session](<https://github.com/H2Shami/opencode-helicone-session>) plugin to automatically log each OpenCode conversation as a session in Helicone.

```bash
npm install -g opencode-helicone-session
```

Add it to your config.

**代码示例：** `opencode.json`

```json
{
  "plugin": ["opencode-helicone-session"]
}
```

The plugin injects `Helicone-Session-Id` and `Helicone-Session-Name` headers into your requests. In Helicone's Sessions page, you'll see each OpenCode conversation listed as a separate session.

##### Common Helicone headers

| Header                     | Description                                                   |
| -------------------------- | ------------------------------------------------------------- |
| `Helicone-Cache-Enabled`   | Enable response caching (`true`/`false`)                      |
| `Helicone-User-Id`         | Track metrics by user                                         |
| `Helicone-Property-[Name]` | Add custom properties (e.g., `Helicone-Property-Environment`) |
| `Helicone-Prompt-Id`       | Associate requests with prompt versions                       |

See the [Helicone Header Directory](<https://docs.helicone.ai/helicone-headers/header-directory>) for all available headers.

---

### llama.cpp

You can configure opencode to use local models through [llama.cpp's](<https://github.com/ggml-org/llama.cpp>) llama-server utility

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llama.cpp": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "llama-server (local)",
      "options": {
        "baseURL": "http://127.0.0.1:8080/v1"
      },
      "models": {
        "qwen3-coder:a3b": {
          "name": "Qwen3-Coder: a3b-30b (local)",
          "limit": {
            "context": 128000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

In this example:

- `llama.cpp` is the custom provider ID. This can be any string you want.
- `npm` specifies the package to use for this provider. Here, `@ai-sdk/openai-compatible` is used for any OpenAI-compatible API.
- `name` is the display name for the provider in the UI.
- `options.baseURL` is the endpoint for the local server.
- `models` is a map of model IDs to their configurations. The model name will be displayed in the model selection list.

---

### IO.NET

IO.NET offers 17 models optimized for various use cases:

1. Head over to the [IO.NET console](<https://ai.io.net/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **IO.NET**.

```txt
/connect
```

3. Enter your IO.NET API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

---

### LM Studio

You can configure opencode to use local models through LM Studio.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "lmstudio": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "LM Studio (local)",
      "options": {
        "baseURL": "http://127.0.0.1:1234/v1"
      },
      "models": {
        "google/gemma-3n-e4b": {
          "name": "Gemma 3n-e4b (local)"
        }
      }
    }
  }
}
```

In this example:

- `lmstudio` is the custom provider ID. This can be any string you want.
- `npm` specifies the package to use for this provider. Here, `@ai-sdk/openai-compatible` is used for any OpenAI-compatible API.
- `name` is the display name for the provider in the UI.
- `options.baseURL` is the endpoint for the local server.
- `models` is a map of model IDs to their configurations. The model name will be displayed in the model selection list.

---

### Moonshot AI

To use Kimi K2 from Moonshot AI:

1. Head over to the [Moonshot AI console](<https://platform.moonshot.ai/console>), create an account, and click **Create API key**.

2. Run the `/connect` command and search for **Moonshot AI**.

```txt
/connect
```

3. Enter your Moonshot API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select _Kimi K2_.

```txt
/models
```

---

### MiniMax

1. Head over to the [MiniMax API Console](<https://platform.minimax.io/login>), create an account, and generate an API key.

2. Run the `/connect` command and search for **MiniMax**.

```txt
/connect
```

3. Enter your MiniMax API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _M2.1_.

```txt
/models
```

---

### Modal

1. Create a [shared Endpoint](<https://modal.com/endpoints>) for the model you want to use.

2. Create a [proxy token](<https://modal.com/docs/guide/endpoints#proxy-tokens>), then join its ID and secret with a period:

```txt
wk-<id>.ws-<secret>
```

3. Run the `/connect` command, search for **Modal**, and enter the combined proxy token.

```txt
/connect
```

4. Run the `/models` command to select one of the endpoints in your Modal workspace.

```txt
/models
```

---

### NVIDIA

NVIDIA provides access to Nemotron models and many other open models through [build.nvidia.com](<https://build.nvidia.com>) for free.

1. Head over to [build.nvidia.com](<https://build.nvidia.com>), create an account, and generate an API key.

2. Run the `/connect` command and search for **NVIDIA**.

```txt
/connect
```

3. Enter your NVIDIA API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like nemotron-3-super-120b-a12b.

```txt
/models
```

#### On-Prem / NIM

You can also use NVIDIA models locally via [NVIDIA NIM](<https://docs.nvidia.com/nim/>) by setting a custom base URL.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "nvidia": {
      "options": {
        "baseURL": "http://localhost:8000/v1"
      }
    }
  }
}
```

#### Environment Variable

Alternatively, set your API key as an environment variable.

```bash
export NVIDIA_API_KEY=nvapi-your-key-here
```

---

### Nebius Token Factory

1. Head over to the [Nebius Token Factory console](<https://tokenfactory.nebius.com/>), create an account, and click **Add Key**.

2. Run the `/connect` command and search for **Nebius Token Factory**.

```txt
/connect
```

3. Enter your Nebius Token Factory API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Kimi K2 Instruct_.

```txt
/models
```

---

### Ollama

You can configure opencode to use local models through Ollama.

**提示：**

Ollama can automatically configure itself for OpenCode. See the [Ollama integration docs](<https://docs.ollama.com/integrations/opencode>) for details.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "ollama": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Ollama (local)",
      "options": {
        "baseURL": "http://localhost:11434/v1"
      },
      "models": {
        "llama2": {
          "name": "Llama 2"
        }
      }
    }
  }
}
```

In this example:

- `ollama` is the custom provider ID. This can be any string you want.
- `npm` specifies the package to use for this provider. Here, `@ai-sdk/openai-compatible` is used for any OpenAI-compatible API.
- `name` is the display name for the provider in the UI.
- `options.baseURL` is the endpoint for the local server.
- `models` is a map of model IDs to their configurations. The model name will be displayed in the model selection list.

**提示：**

If tool calls aren't working, try increasing `num_ctx` in Ollama. Start around 16k - 32k.

---

### Ollama Cloud

To use Ollama Cloud with OpenCode:

1. Head over to [https://ollama.com/](<https://ollama.com/>) and sign in or create an account.

2. Navigate to **Settings** > **Keys** and click **Add API Key** to generate a new API key.

3. Copy the API key for use in OpenCode.

4. Run the `/connect` command and search for **Ollama Cloud**.

```txt
/connect
```

5. Enter your Ollama Cloud API key.

```txt
┌ API key
│
│
└ enter
```

6. **Important**: Before using cloud models in OpenCode, you must pull the model information locally:

```bash
ollama pull gpt-oss:20b-cloud
```

7. Run the `/models` command to select your Ollama Cloud model.

```txt
/models
```

---

### OpenAI

We recommend signing up for [ChatGPT Plus or Pro](<https://chatgpt.com/pricing>).

1. Once you've signed up, run the `/connect` command and select OpenAI.

```txt
/connect
```

2. Here you can select the **ChatGPT Plus/Pro** option and it'll open your browser
   and ask you to authenticate.

```txt
┌ Select auth method
│
│ ChatGPT Plus/Pro
│ Manually enter API Key
└
```

3. Now all the OpenAI models should be available when you use the `/models` command.

```txt
/models
```

##### Compute residency

For ChatGPT OAuth, OpenCode automatically applies a regional inference residency requirement when one is advertised by your workspace credentials. It forwards the compute residency value from the credential instead of maintaining a fixed list of regions. Data residency at rest does not imply regional inference.

##### Using API keys

If you already have an API key, you can select **Manually enter API Key** and paste it in your terminal.

---

### OpenCode Zen

OpenCode Zen is a list of tested and verified models provided by the OpenCode team. [Learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode Zen 模型服务、接入与团队配置.md>).

1. Sign in to **[OpenCode Zen](<https://opencode.ai/auth>)** and click **Create API Key**.

2. Run the `/connect` command and search for **OpenCode Zen**.

```txt
/connect
```

3. Enter your OpenCode API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Qwen 3 Coder 480B_.

```txt
/models
```

---

### OpenRouter

1. Head over to the [OpenRouter dashboard](<https://openrouter.ai/settings/keys>), click **Create API Key**, and copy the key.

2. Run the `/connect` command and search for OpenRouter.

```txt
/connect
```

3. Enter the API key for the provider.

```txt
┌ API key
│
│
└ enter
```

4. Many OpenRouter models are preloaded by default, run the `/models` command to select the one you want.

```txt
/models
```

   You can also add additional models through your opencode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

5. You can also customize them through your opencode config. Here's an example of specifying a provider

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "openrouter": {
      "models": {
        "moonshotai/kimi-k2": {
          "options": {
            "provider": {
              "order": ["baseten"],
              "allow_fallbacks": false
            }
          }
        }
      }
    }
  }
}
```

---

### LLM Gateway

1. Head over to the [LLM Gateway dashboard](<https://llmgateway.io/dashboard>), click **Create API Key**, and copy the key.

2. Run the `/connect` command and search for LLM Gateway.

```txt
/connect
```

3. Enter the API key for the provider.

```txt
┌ API key
│
│
└ enter
```

4. Many LLM Gateway models are preloaded by default, run the `/models` command to select the one you want.

```txt
/models
```

   You can also add additional models through your opencode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llmgateway": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

5. You can also customize them through your opencode config. Here's an example of specifying a provider

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "llmgateway": {
      "models": {
        "glm-4.7": {
          "name": "GLM 4.7"
        },
        "gpt-5.2": {
          "name": "GPT-5.2"
        },
        "gemini-2.5-pro": {
          "name": "Gemini 2.5 Pro"
        },
        "claude-3-5-sonnet-20241022": {
          "name": "Claude 3.5 Sonnet"
        }
      }
    }
  }
}
```

---

### Poolside

[Poolside](<https://poolside.ai>) provides access to its models through an OpenAI-compatible API.

1. Head over to [platform.poolside.ai](<https://platform.poolside.ai>), sign in, open the **API Keys** tab, and click **New key** to generate an API key.

2. Run the `/connect` command and search for **Poolside**.

```txt
/connect
```

3. Enter your Poolside API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

#### Poolside deployment

If your organization runs a Poolside deployment, configure it as a custom OpenAI-compatible provider. Store the API key or token from your Poolside administrator in `~/.secrets/poolside-key`. Use a unique provider ID and add the model IDs available from your deployment.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "poolside-deployment": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "Poolside deployment",
      "options": {
        "baseURL": "https://poolside.example.com/openai/v1",
        "apiKey": "{file:~/.secrets/poolside-key}"
      },
      "models": {
        "<served-model-id>": {
          "name": "Poolside deployment model",
          "reasoning": true,
          "interleaved": {
            "field": "reasoning_content"
          }
        }
      }
    }
  }
}
```

Replace `poolside.example.com` with your deployment's host and `<served-model-id>` with a model ID returned by its `/openai/v1/models` endpoint. Model availability and thinking behavior depend on the deployment's configuration.

**提示：**

You can also install [Poolside Agent CLI](<https://github.com/poolsideai/pool>) to use Poolside agents directly from your terminal.

---

### SAP AI Core

SAP AI Core provides access to 40+ models from OpenAI, Anthropic, Google, Amazon, Meta, Mistral, and AI21 through a unified platform.

1. Go to your [SAP BTP Cockpit](<https://account.hana.ondemand.com/>), navigate to your SAP AI Core service instance, and create a service key.

**提示：**

   The service key is a JSON object containing `clientid`, `clientsecret`, `url`, and `serviceurls.AI_API_URL`. You can find your AI Core instance under **Services** > **Instances and Subscriptions** in the BTP Cockpit.

2. Run the `/connect` command and search for **SAP AI Core**.

```txt
/connect
```

3. Enter your service key JSON.

```txt
┌ Service key
│
│
└ enter
```

   Or set the `AICORE_SERVICE_KEY` environment variable:

```bash
AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","url":"...","serviceurls":{"AI_API_URL":"..."}}' opencode
```

   Or add it to your bash profile:

**代码示例：** `~/.bash_profile`

```bash
export AICORE_SERVICE_KEY='{"clientid":"...","clientsecret":"...","url":"...","serviceurls":{"AI_API_URL":"..."}}'
```

4. Optionally set deployment ID and resource group:

```bash
AICORE_DEPLOYMENT_ID=your-deployment-id AICORE_RESOURCE_GROUP=your-resource-group opencode
```

**注释：**

   These settings are optional and should be configured according to your SAP AI Core setup.

5. Run the `/models` command to select from 40+ available models.

```txt
/models
```

---

### STACKIT

STACKIT AI Model Serving provides fully managed sovereign hosting environment for AI models, focusing on LLMs like Llama, Mistral, and Qwen, with maximum data sovereignty on European infrastructure.

1. Head over to [STACKIT Portal](<https://portal.stackit.cloud>), navigate to **AI Model Serving**, and create an auth token for your project.

**提示：**

   You need a STACKIT customer account, user account, and project before creating auth tokens.

2. Run the `/connect` command and search for **STACKIT**.

```txt
/connect
```

3. Enter your STACKIT AI Model Serving auth token.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select from available models like _Qwen3-VL 235B_ or _Llama 3.3 70B_.

```txt
/models
```

---

### OVHcloud AI Endpoints

1. Head over to the [OVHcloud panel](<https://ovh.com/manager>). Navigate to the `Public Cloud` section, `AI & Machine Learning` > `AI Endpoints` and in `API Keys` tab, click **Create a new API key**.

2. Run the `/connect` command and search for **OVHcloud AI Endpoints**.

```txt
/connect
```

3. Enter your OVHcloud AI Endpoints API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _gpt-oss-120b_.

```txt
/models
```

---

### Scaleway

To use [Scaleway Generative APIs](<https://www.scaleway.com/en/docs/generative-apis/>) with Opencode:

1. Head over to the [Scaleway Console IAM settings](<https://console.scaleway.com/iam/api-keys>) to generate a new API key.

2. Run the `/connect` command and search for **Scaleway**.

```txt
/connect
```

3. Enter your Scaleway API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _devstral-2-123b-instruct-2512_ or _gpt-oss-120b_.

```txt
/models
```

---

### SCX.ai

[SCX.ai](<https://scx.ai>) is an Australian sovereign AI platform serving open models over an OpenAI-compatible API, hosted on renewable-powered infrastructure in Australia.

1. Head over to the [SCX.ai platform](<https://platform.scx.ai>) to create an account and generate an API key.

2. Run the `/connect` command and search for **SCX.ai**.

```txt
/connect
```

3. Enter your SCX.ai API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _MiniMax-M2.7_ or _GLM-5.2_.

```txt
/models
```

---

### Snowflake Cortex

[Snowflake Cortex](<https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-rest-api>) gives you access to frontier models (Claude, OpenAI GPT-5, and more) via an OpenAI-compatible API. All inference runs within the Snowflake perimeter and is billed in Snowflake credits. For per-model rates, see the [Snowflake Service Consumption Table](<https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf>).

Don't have a Snowflake account? [Sign up for a free trial](<https://signup.snowflake.com/?utm_source=opencode&utm_medium=docs&utm_campaign=cortex-provider>).

opencode's core workflow for coding, editing files, and running commands relies on tool calling. Only the Claude and OpenAI families within Snowflake Cortex support this. The provider is limited to those families to support the core workflow.

OpenCode supports two authentication methods:

- **Browser OAuth (Recommended)** — sign in with your IdP/SSO; no secrets to manage, tokens refresh automatically.
- **Manual bearer token** — paste a PAT or JWT from the Snowflake console.

#### Browser OAuth (Recommended)

1. Run the `/connect` command and search for **Snowflake Cortex**.

```txt
/connect
```

2. Select **Login with Snowflake (External Browser)**.

```txt
┌ Select auth method
│
│ Login with Snowflake (External Browser)
│ Paste PAT or bearer token manually
└
```

3. Enter your [account identifier](<https://docs.snowflake.com/en/user-guide/admin-account-identifier>) when prompted, for example `myorg-myaccount` or `xy12345.us-east-1`.

```txt
┌ Snowflake Account Identifier
│
│
└ enter
```

4. Optionally enter a Snowflake role to scope the session (e.g. `SYSADMIN`). Leave blank to use your default role.

5. Complete sign-in in the browser that opens. OpenCode captures the OAuth callback automatically and stores the token — no copy/paste needed.

6. Run the `/models` command to select a model.

```txt
/models
```

**注释：**

Browser OAuth uses Snowflake's built-in `SNOWFLAKE$LOCAL_APPLICATION` security integration ([docs](<https://docs.snowflake.com/en/user-guide/oauth-local-applications>)), which is rolling out to all accounts. To check availability in your account:

```sql
SHOW SECURITY INTEGRATIONS LIKE 'SNOWFLAKE$LOCAL_APPLICATION';
```

If the result is empty, use the **Manual bearer token** method below while the integration rolls out to your account.

#### Manual bearer token

If you prefer to paste a token directly, or if `SNOWFLAKE$LOCAL_APPLICATION` is not yet available in your account:

1. Generate a [Programmatic Access Token (PAT)](<https://docs.snowflake.com/en/user-guide/programmatic-access-tokens>) in your Snowflake account.

2. Run the `/connect` command, search for **Snowflake Cortex**, and select **Paste PAT or bearer token manually**.

3. Enter your [account identifier](<https://docs.snowflake.com/en/user-guide/admin-account-identifier>) when prompted.

4. Paste your PAT.

5. Run the `/models` command to select a model.

```txt
/models
```

#### Environment variable

For CI or headless environments, set a PAT or JWT before starting opencode:

```bash
export SNOWFLAKE_ACCOUNT=myorg-myaccount
export SNOWFLAKE_CORTEX_TOKEN=your-pat-or-jwt
```

**注释：**

`SNOWFLAKE_CORTEX_TOKEN` accepts a PAT or JWT only — the browser OAuth flow is available via `/connect` only and cannot be configured through an environment variable. `SNOWFLAKE_CORTEX_PAT` is still supported for backward compatibility.

The model catalog is provided automatically. A minimal `opencode.json` is all that's needed:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "snowflake-cortex/claude-sonnet-4-6",
  "small_model": "snowflake-cortex/claude-haiku-4-5"
}
```

---

### Together AI

1. Head over to the [Together AI console](<https://api.together.ai>), create an account, and click **Add Key**.

2. Run the `/connect` command and search for **Together AI**.

```txt
/connect
```

3. Enter your Together AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Kimi K2 Instruct_.

```txt
/models
```

---

### Venice AI

1. Head over to the [Venice AI console](<https://venice.ai>), create an account, and generate an API key.

2. Run the `/connect` command and search for **Venice AI**.

```txt
/connect
```

3. Enter your Venice AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Llama 3.3 70B_.

```txt
/models
```

---

### Vercel AI Gateway

Vercel AI Gateway lets you access models from OpenAI, Anthropic, Google, xAI, and more through a unified endpoint. Models are offered at list price with no markup.

1. Head over to the [Vercel dashboard](<https://vercel.com/>), navigate to the **AI Gateway** tab, and click **API keys** to create a new API key.

2. Run the `/connect` command and search for **Vercel AI Gateway**.

```txt
/connect
```

3. Enter your Vercel AI Gateway API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model.

```txt
/models
```

You can also customize models through your opencode config. Here's an example of specifying provider routing order.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "vercel": {
      "models": {
        "anthropic/claude-sonnet-4": {
          "options": {
            "order": ["anthropic", "vertex"]
          }
        }
      }
    }
  }
}
```

Some useful routing options:

| Option              | Description                                          |
| ------------------- | ---------------------------------------------------- |
| `order`             | Provider sequence to try                             |
| `only`              | Restrict to specific providers                       |
| `zeroDataRetention` | Only use providers with zero data retention policies |

---

### xAI

Two ways to authenticate: a SuperGrok subscription via device-code OAuth or a pay-as-you-go API key from the xAI console.

#### Option A — SuperGrok subscription

1. Run the `/connect` command and search for **xAI**.

```txt
/connect
```

2. Select **SuperGrok Subscription**. OpenCode opens xAI's verification link with the user code pre-populated when supported.

3. Approve the consent screen. If xAI asks for a code, enter the user code displayed by OpenCode. OpenCode polls xAI's token endpoint and stores the resulting OAuth tokens once you approve.

4. Run the `/models` command to select a Grok model.

```txt
/models
```

OpenCode refreshes the OAuth access token automatically. Any Grok or X Premium plan that includes Grok API access works; you do not need a separate `XAI_API_KEY`.

#### Option B — API key

1. Head over to the [xAI console](<https://console.x.ai/>), create an account, and generate an API key.

2. Run the `/connect` command and search for **xAI**.

```txt
/connect
```

3. Select **Manually enter API Key** and paste your xAI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _Grok Beta_.

```txt
/models
```

---

### Z.AI

1. Head over to the [Z.AI API console](<https://z.ai/manage-apikey/apikey-list>), create an account, and click **Create a new API key**.

2. Run the `/connect` command and search for **Z.AI**.

```txt
/connect
```

   If you are subscribed to the **GLM Coding Plan**, select **Z.AI Coding Plan**.

3. Enter your Z.AI API key.

```txt
┌ API key
│
│
└ enter
```

4. Run the `/models` command to select a model like _GLM-4.7_.

```txt
/models
```

---

### ZenMux

1. Head over to the [ZenMux dashboard](<https://zenmux.ai/settings/keys>), click **Create API Key**, and copy the key.

2. Run the `/connect` command and search for ZenMux.

```txt
/connect
```

3. Enter the API key for the provider.

```txt
┌ API key
│
│
└ enter
```

4. Many ZenMux models are preloaded by default, run the `/models` command to select the one you want.

```txt
/models
```

   You can also add additional models through your opencode config.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "zenmux": {
      "models": {
        "somecoolnewmodel": {}
      }
    }
  }
}
```

---

## Custom provider

To add any **OpenAI-compatible** provider that's not listed in the `/connect` command:

**提示：**

You can use any OpenAI-compatible provider with opencode. Most modern AI providers offer OpenAI-compatible APIs.

1. Run the `/connect` command and scroll down to **Other**.

```bash
$ /connect

┌  Add credential
│
◆  Select provider
│  ...
│  ● Other
└
```

2. Enter a unique ID for the provider.

```bash
$ /connect

┌  Add credential
│
◇  Enter provider id
│  myprovider
└
```

**注释：**

   Choose a memorable ID, you'll use this in your config file.

3. Enter your API key for the provider.

```bash
$ /connect

┌  Add credential
│
▲  This only stores a credential for myprovider - you will need to configure it in opencode.json, check the docs for examples.
│
◇  Enter your API key
│  sk-...
└
```

4. Create or update your `opencode.json` file in your project directory:

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "My AI Provider Display Name",
      "options": {
        "baseURL": "https://api.myprovider.com/v1"
      },
      "models": {
        "my-model-name": {
          "name": "My Model Display Name"
        }
      }
    }
  }
}
```

   Here are the configuration options:
   - **npm**: AI SDK package to use, `@ai-sdk/openai-compatible` for OpenAI-compatible providers (for `/v1/chat/completions`). If your provider/model uses `/v1/responses`, use `@ai-sdk/openai`.
   - **name**: Display name in UI.
   - **models**: Available models.
   - **options.baseURL**: API endpoint URL.
   - **options.apiKey**: Optionally set the API key, if not using auth.
   - **options.headers**: Optionally set custom headers.

   More on the advanced options in the example below.

5. Run the `/models` command and your custom provider and models will appear in the selection list.

---

##### Example

Here's an example setting the `apiKey`, `headers`, and model `limit` options.

**代码示例：** `opencode.json`

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "myprovider": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "My AI Provider Display Name",
      "options": {
        "baseURL": "https://api.myprovider.com/v1",
        "apiKey": "{env:ANTHROPIC_API_KEY}",
        "headers": {
          "Authorization": "Bearer custom-token"
        }
      },
      "models": {
        "my-model-name": {
          "name": "My Model Display Name",
          "limit": {
            "context": 200000,
            "output": 65536
          }
        }
      }
    }
  }
}
```

Configuration details:

- **apiKey**: Set using `env` variable syntax, [learn more](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md#env-vars>).
- **headers**: Custom headers sent with each request.
- **limit.context**: Maximum input tokens the model accepts.
- **limit.output**: Maximum tokens the model can generate.

The `limit` fields allow OpenCode to understand how much context you have left. Standard providers pull these from models.dev automatically.

---

## Troubleshooting

If you are having trouble with configuring a provider, check the following:

1. **Check the auth setup**: Run `opencode auth list` to see if the credentials
   for the provider are added to your config.

   This doesn't apply to providers like Amazon Bedrock, that rely on environment variables for their auth.

2. For custom providers, check the opencode config and:
   - Make sure the provider ID used in the `/connect` command matches the ID in your opencode config.
   - The right npm package is used for the provider. For example, use `@ai-sdk/cerebras` for Cerebras. And for all other OpenAI-compatible providers, use `@ai-sdk/openai-compatible` (for `/v1/chat/completions`); if a model uses `/v1/responses`, use `@ai-sdk/openai`. For mixed setups under one provider, you can override per model via `provider.npm`.
   - Check correct API endpoint is used in the `options.baseURL` field.


</details>
