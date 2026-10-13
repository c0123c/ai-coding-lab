# Qwen Code MCP 服务器配置详解

解释工具发现执行架构、传输、settings.json 字段、OAuth 与令牌管理，供排查复杂接入问题。

- 来源：Qwen／Qwen Code
- 资料类型：存档文档
- 存档版本：`7e3de59d3aba`。
- 文档时间：源码提交（存档版）：[2026-08-23](<https://github.com/QwenLM/qwen-code/commit/4ddbf227e898ceb322116af5e2004c37a51184f6>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/QwenLM/qwen-code/blob/main/docs/developers/tools/mcp-server.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Qwen/Qwen Code/Qwen Code MCP 服务器配置详解.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# MCP 具有 Qwen Code 的服务器

本文档提供了通过 Qwen Code 配置和使用 Model Context Protocol (MCP) 服务器的指南。

## 什么是MCP服务器？

MCP服务器是一个通过Model Context Protocol向CLI公开工具和资源的应用程序，允许其与外部系统和数据源进行交互。 MCP 服务器充当模型与本地环境或 API 等其他服务之间的桥梁。

MCP 服务器使 CLI 能够：

- **发现工具：**通过标准化模式定义列出可用工具、其描述和参数。
- **执行工具：**使用定义的参数调用特定工具并接收结构化响应。
- **访问资源：**从特定资源读取数据（尽管CLI主要关注工具执行）。

借助 MCP 服务器，您可以扩展 CLI 的功能以执行其内置功能之外的操作，例如与数据库、API、自定义脚本或专用工作流程交互。

## 核心集成架构

Qwen Code 通过核心包 (`packages/core/src/tools/`) 中内置的复杂发现和执行系统与 MCP 服务器集成：

### 发现层（`mcp-client.ts`）

发现过程由 `discoverMcpTools()` 精心安排，其中：

1. **从您的 `settings.json` `mcpServers` 配置中迭代配置的服务器 **
2. **使用适当的传输机制（Stdio、SSE 或 Streamable HTTP）建立连接**
3. **使用 MCP 协议从每台服务器获取工具定义 **
4. **清理并验证**工具架构以与Qwen API兼容
5. **在全局工具注册表中注册工具**并解决冲突

### 执行层（`mcp-tool.ts`）

每个发现的 MCP 工具都包含在一个 `DiscoveredMCPTool` 实例中，该实例：

- **根据服务器信任设置和用户首选项处理确认逻辑**
- **通过使用适当的参数调用 MCP 服务器来管理工具执行 **
- **处理 LLM 上下文和用户显示的响应 **
- **维护连接状态**并处理超时

连接丢失后，当该工具显式声明 `idempotentHint: true` 或声明 `readOnlyHint: true` 而没有冲突的 `destructiveHint: true` 或 `idempotentHint: false` 时，仅对受信任工作区中的受信任服务器重播当前调用。丢失或冲突的注释以及来自不受信任的服务器或工作区的注释被视为不安全，因为服务器可能在响应丢失之前完成了副作用。工具作者应发布准确的MCP注释；管理员仍应在启用服务器信任之前验证它们。

### 运输机制

CLI 支持三种 MCP 传输类型：

- **Stdio Transport：** 生成子进程并通过 stdin/stdout 进行通信
- **SSE 传输：** 连接到服务器发送的事件端点
- **可流式传输 HTTP 传输：** 使用 HTTP 流式传输进行通信

## 如何设置您的MCP服务器

Qwen Code 使用 `settings.json` 文件中的 `mcpServers` 配置来查找并连接到 MCP 服务器。此配置支持具有不同传输机制的多个服务器。

### 在settings.json中配置MCP服务器

您可以通过两种主要方式在 `settings.json` 文件中配置 MCP 服务器：通过用于特定服务器定义的顶级 `mcpServers` 对象，以及通过用于控制服务器发现和执行的全局设置的 `mcp` 对象。

#### 全局 MCP 设置 (`mcp`)

`settings.json` 中的 `mcp` 对象允许您为所有 MCP 服务器定义全局规则。

- **`mcp.serverCommand`**（字符串）：启动 MCP 服务器的全局命令。
- **`mcp.allowed`**（字符串数组）：允许的 MCP 服务器名称列表。如果设置了此项，则仅连接此列表中的服务器（与 `mcpServers` 对象中的键匹配）。
- **`mcp.excluded`**（字符串数组）：要排除的 MCP 服务器名称列表。将不会连接到此列表中的服务器。

**示例：**

```json
{
  "mcp": {
    "allowed": ["my-trusted-server"],
    "excluded": ["experimental-server"]
  }
}
```

#### 服务器特定配置 (`mcpServers`)

`mcpServers` 对象是您定义想要 CLI 连接到的每个单独的 MCP 服务器的位置。

### 配置结构

将 `mcpServers` 对象添加到您的 `settings.json` 文件中：

```json
{ ...file contains other config objects
  "mcpServers": {
    "serverName": {
      "command": "path/to/server",
      "args": ["--arg1", "value1"],
      "env": {
        "API_KEY": "$MY_API_TOKEN"
      },
      "cwd": "./server-directory",
      "timeout": 30000,
      "trust": false
    }
  }
}
```

### 配置属性

每个服务器配置都支持以下属性：

#### 必填（以下之一）

- **`command`**（字符串）：Stdio 传输可执行文件的路径
- **`url`**（字符串）：SSE 端点 URL（例如，`"http://localhost:8080/sse"`）
- **`httpUrl`**（字符串）：HTTP流端点URL

#### 可选

- **`args`**（字符串[]）：Stdio 传输的命令行参数
- **`headers`**（对象）：使用 `url` 或 `httpUrl` 时自定义 HTTP 标头
- **`env`**（对象）：服务器进程的环境变量。值可以使用 `$VAR_NAME` 或 `${VAR_NAME}` 语法引用环境变量
- **`cwd`**（字符串）：Stdio 传输的工作目录
- **`timeout`**（数字）：请求超时以毫秒为单位（默认值：600,000ms = 10 分钟）
- **`versionNegotiation`**（`"auto" | "legacy"`，默认值：`"legacy"`）：对于 Stdio 服务器，`"auto"` 选择在一次性同级进程上进行 `server/discover` 探测。
- **`trust`**（布尔值）：当 `true` 时，绕过受信任工作区中此服务器的工具调用确认（默认值：`false`）
- **`includeTools`**（字符串[]）：要包含在此 MCP 服务器中的工具名称列表。指定后，只有此处列出的工具可从此服务器上使用（白名单行为）。如果未指定，则默认启用服务器中的所有工具。
- **`excludeTools`**（字符串[]）：要从此MCP服务器中排除的工具名称列表。此处列出的工具将不可用于模型，即使它们由服务器公开也是如此。 **注意：** `excludeTools` 优先于 `includeTools` - 如果工具同时在两个列表中，则它将被排除。
- **`targetAudience`**（字符串）：OAuth 客户端 ID 已列入您尝试访问的 IAP- 受保护应用程序的白名单。与 `authProviderType: 'service_account_impersonation'` 一起使用。
- **`targetServiceAccount`**（字符串）：要模拟的 Google 云服务帐户的电子邮件地址。与 `authProviderType: 'service_account_impersonation'` 一起使用。

### 对远程 MCP 服务器的 OAuth 支持

Qwen Code 支持使用 SSE 或 HTTP 传输对远程 MCP 服务器进行 OAuth 2.0 身份验证。这样可以安全访问需要身份验证的 MCP 服务器。

#### 自动 OAuth 发现

对于支持OAuth发现的服务器，您可以省略OAuth配置并让CLI自动发现它：

```json
{
  "mcpServers": {
    "discoveredServer": {
      "url": "https://api.example.com/sse"
    }
  }
}
```

CLI 将自动：

- 检测服务器何时需要 OAuth 身份验证（401 响应）
- 从服务器元数据发现 OAuth 端点
- 如果支持，执行动态客户端注册
- 处理 OAuth 流程和令牌管理

#### 认证流程

连接到启用 OAuth 的服务器时：

1. **初始连接尝试**失败，并显示401未经授权
2. **OAuth 发现** 查找授权和令牌端点
3. **浏览器打开**进行用户身份验证（需要本地浏览器访问）
4. **授权代码**交换为访问令牌
5. **令牌被安全存储**以供将来使用
6. **连接重试**成功并具有有效令牌

#### 浏览器重定向要求

**重要提示：** OAuth 身份验证要求重定向 URI 可访问：

- **默认行为**：重定向到`http://localhost:7777/oauth/callback`（适用于本地设置）
- **自定义重定向URI**：使用`--oauth-redirect-uri`或在settings.json中配置`redirectUri`指定以`/oauth/callback`结尾的公共URL。在运行 Qwen Code 的计算机上反向代理到 `http://127.0.0.1:7777/oauth/callback` 的路径。

对于 **remote/cloud 服务器部署 **（例如，Web 终端、SSH 会话、云 IDE）：

- 默认的 `localhost` 重定向将 NOT 工作
- 您MUST配置一个自定义`redirectUri`，指向以`/oauth/callback`结尾的可公开访问的URL
- 在反向代理处终止 TLS 并仅将该路径转发到 `http://127.0.0.1:7777/oauth/callback`

远程服务器示例：

```bash
qwen mcp add --transport sse remote-server https://api.example.com/sse/ \
  --oauth-redirect-uri https://your-remote-server.example.com/oauth/callback
```

OAuth 不适用于：

- 无需浏览器访问的无头环境
- 从用户的浏览器无法访问已配置的 `redirectUri` 的环境

#### 管理 OAuth 身份验证

使用交互式 Qwen Code 会话中的 `/mcp` 对话框来检查 MCP
 服务器并管理 OAuth 身份验证。

#### OAuth 配置属性

- **`enabled`**（布尔值）：为此服务器启用 OAuth
- **`clientId`**（字符串）：OAuth 客户端标识符（动态注册时可选）
- **`clientSecret`**（字符串）：OAuth 客户端密钥（公共客户端可选）
- **`authorizationUrl`**（字符串）：OAuth 授权端点（如果省略则自动发现）
- **`tokenUrl`**（字符串）：OAuth 令牌端点（如果省略，则自动发现）
- **`scopes`**（字符串[]）：必需的 OAuth 范围
- **`redirectUri`**（字符串）：自定义重定向URI。 **对于远程部署至关重要**：默认为`http://localhost:7777/oauth/callback`。对于远程使用，请设置以 `/oauth/callback` 结尾的公共 URL 并将其反向代理到本地回调侦听器。可以通过`qwen mcp add --oauth-redirect-uri`或直接在settings.json中配置。
- **`tokenParamName`**（字符串）：SSE URL 中令牌的查询参数名称
- **`audiences`**（字符串[]）：令牌有效的受众

#### 代币管理

OAuth 令牌会自动：

- **默认将**存储在`~/.qwen/mcp-oauth-tokens.json`中（明文，模式0600）。如果设置了 `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true`，则 Qwen Code 使用可用的钥匙串支持存储，或使用 AES-256-GCM 加密的 `~/.qwen/mcp-oauth-tokens-v2.json`。
- **过期时刷新**（如果刷新令牌可用）
- **在每次连接尝试之前验证**
- **无效或过期时已清理**

> [!WARNING]
> 默认情况下，OAuth 令牌未加密地存储在磁盘上。在共享或多用户计算机上，设置 `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` 以保护凭据。

#### 身份验证提供商类型

您可以使用 `authProviderType` 属性指定身份验证提供程序类型：

- **`authProviderType`**（字符串）：指定身份验证提供程序。可以是以下之一：
  - **`dynamic_discovery`**（默认）：CLI将自动从服务器发现OAuth配置。
  - **`google_credentials`**：CLI 将使用 Google 应用程序默认凭据 (ADC) 向服务器进行身份验证。使用此提供程序时，您必须指定所需的范围。
  - **`service_account_impersonation`**：CLI将模拟Google云服务帐户以向服务器进行身份验证。这对于访问 IAP- 受保护的服务非常有用（这是专门为 Cloud Run 服务设计的）。

#### Google 凭证

```json
{
  "mcpServers": {
    "googleCloudServer": {
      "httpUrl": "https://my-gcp-service.run.app/mcp",
      "authProviderType": "google_credentials",
      "oauth": {
        "scopes": ["https://www.googleapis.com/auth/userinfo.email"]
      }
    }
  }
}
```

#### 服务帐户模拟

要使用服务帐户模拟向服务器进行身份验证，您必须将 `authProviderType` 设置为 `service_account_impersonation` 并提供以下属性：

- **`targetAudience`**（字符串）：OAuth 客户端 ID 已列入您尝试访问的 IAP- 受保护应用程序的白名单。
- **`targetServiceAccount`**（字符串）：要模拟的 Google 云服务帐户的电子邮件地址。

CLI 将使用您的本地应用程序默认凭据 (ADC) 为指定的服务帐户和受众生成 OIDC ID 令牌。然后，该令牌将用于与 MCP 服务器进行身份验证。

#### 设置说明

1. **[创建](<https://cloud.google.com/iap/docs/oauth-client-creation>)或使用现有的OAuth 2.0客户端ID。**要使用现有的OAuth 2.0客户端ID，请按照以下步骤操作[如何共享OAuth客户端](<https://cloud.google.com/iap/docs/sharing-oauth-clients>)。
2. **将 OAuth ID 添加到应用程序的 [编程访问 ](<https://cloud.google.com/iap/docs/sharing-oauth-clients#programmatic_access>) 的白名单中。** 由于 Cloud Run 尚不是 gcloud iap 中受支持的资源类型，因此您必须将项目上的客户端 ID 列入白名单。
3. **创建服务帐号。** [文档](<https://cloud.google.com/iam/docs/service-accounts-create#creating>)、[云控制台链接](<https://console.cloud.google.com/iam-admin/serviceaccounts>)
4. **将服务帐户和用户添加到 Cloud Run 服务本身的“安全”选项卡中或通过 gcloud 中的 IAP 策略 **。
5. **向将访问 MCP 服务器的所有用户和组 ** 授予 [ 模拟服务帐户 ](<https://cloud.google.com/docs/authentication/use-service-account-impersonation>)（即 `roles/iam.serviceAccountTokenCreator`）所需的权限。
6. **[为您的项目启用](<https://console.cloud.google.com/apis/library/iamcredentials.googleapis.com>)IAM凭据API**。

### 配置示例

#### Python MCP 服务器 (Stdio)

```json
{
  "mcpServers": {
    "pythonTools": {
      "command": "python",
      "args": ["-m", "my_mcp_server", "--port", "8080"],
      "cwd": "./mcp-servers/python",
      "env": {
        "DATABASE_URL": "$DB_CONNECTION_STRING",
        "API_KEY": "${EXTERNAL_API_KEY}"
      },
      "timeout": 15000
    }
  }
}
```

#### Node.js MCP 服务器 (Stdio)

```json
{
  "mcpServers": {
    "nodeServer": {
      "command": "node",
      "args": ["dist/server.js", "--verbose"],
      "cwd": "./mcp-servers/node",
      "trust": true
    }
  }
}
```

#### 基于 Docker 的 MCP 服务器

```json
{
  "mcpServers": {
    "dockerizedServer": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "API_KEY",
        "-v",
        "${PWD}:/workspace",
        "my-mcp-server:latest"
      ],
      "env": {
        "API_KEY": "$EXTERNAL_SERVICE_TOKEN"
      }
    }
  }
}
```

#### HTTP-基于MCP服务器

```json
{
  "mcpServers": {
    "httpServer": {
      "httpUrl": "http://localhost:3000/mcp",
      "timeout": 5000
    }
  }
}
```

#### 具有自定义标头的基于 HTTP- 的 MCP 服务器

```json
{
  "mcpServers": {
    "httpServerWithAuth": {
      "httpUrl": "http://localhost:3000/mcp",
      "headers": {
        "Authorization": "Bearer your-api-token",
        "X-Custom-Header": "custom-value",
        "Content-Type": "application/json"
      },
      "timeout": 5000
    }
  }
}
```

#### MCP 带工具过滤的服务器

```json
{
  "mcpServers": {
    "filteredServer": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "includeTools": ["safe_tool", "file_reader", "data_processor"],
      // "excludeTools": ["dangerous_tool", "file_deleter"],
      "timeout": 30000
    }
  }
}
```

### SSE MCP 具有 SA 模拟的服务器

```json
{
  "mcpServers": {
    "myIapProtectedServer": {
      "url": "https://my-iap-service.run.app/sse",
      "authProviderType": "service_account_impersonation",
      "targetAudience": "YOUR_IAP_CLIENT_ID.apps.googleusercontent.com",
      "targetServiceAccount": "your-sa@your-project.iam.gserviceaccount.com"
    }
  }
}
```

## 探索过程深入探讨

当Qwen Code启动时，它通过以下详细过程执行MCP服务器发现：

### 1。服务器迭代与连接

对于 `mcpServers` 中每个配置的服务器：

1. **状态跟踪开始：**服务器状态设置为`CONNECTING`
2. **传输选择：** 基于配置属性：
   - `httpUrl` → `StreamableHTTPClientTransport`
   - `url` → `SSEClientTransport`
   - `command` → `StdioClientTransport`
3. **连接建立：** MCP客户端尝试使用配置的超时进行连接
4. **错误处理：**记录连接失败并将服务器状态设置为`DISCONNECTED`

### 2。工具发现

连接成功后：

1. **工具列表：**客户端调用MCP服务器的工具列表端点
2. **架构验证：**每个工具的函数声明都经过验证
3. **工具过滤：**根据`includeTools`和`excludeTools`配置过滤工具
4. **名称清理：**工具名称已清理以满足QwenAPI要求：
   - 无效字符（非字母数字、下划线、点、连字符）将替换为下划线
   - 长度超过 63 个字符的名称将被截断并进行中间替换 (`___`)

### 3。冲突解决

当多个服务器公开同名工具时：

1. **首次注册获胜：**第一个注册工具名称的服务器获得无前缀名称
2. **自动前缀：** 后续服务器获得前缀名称：`serverName__toolName`
3. **注册表跟踪：**工具注册表维护服务器名称及其工具之间的映射

### 4。模式处理

工具参数模式经过清理以实现 API 兼容性：

- **`$schema` 属性 ** 已删除
- **`additionalProperties`** 已被剥离
- **`anyOf` 和 `default`** 已删除默认值（Vertex AI 兼容性）
- **递归处理**适用于嵌套模式

### 5。连接管理

发现后：

- **持久连接：**成功注册工具的服务器保持其连接
- **清理：**不提供可用工具的服务器已关闭其连接
- **状态更新：**最终服务器状态设置为`CONNECTED`或`DISCONNECTED`

## 工具执行流程

当模型决定使用 MCP 工具时，会发生以下执行流程：

### 1。工具调用

该模型生成一个 `FunctionCall`：

- **工具名称：**注册名称（可能带有前缀）
- **参数：** JSON 与工具参数架构匹配的对象

### 2。确认流程

每个 `DiscoveredMCPTool` 都实现复杂的确认逻辑：

#### 基于信任的绕过

```typescript
if (this.trust === true && this.cliConfig?.isTrustedFolder()) {
  return 'allow';
}
return 'ask';
```

#### 动态允许列表

系统维护以下内容的内部允许列表：

- **服务器级别：** `serverName` → 来自该服务器的所有工具都是可信的
- **工具级别：** `serverName.toolName` → 该特定工具受信任

#### 用户选择处理

当需要确认时，用户可以选择：

- **进行一次：**仅执行本次
- **始终允许此工具：** 添加到工具级别允许列表
- **始终允许此服务器：** 添加到服务器级别允许列表
- **取消：**中止执行

### 3。执行

确认后（或信任绕过）：

1. **参数准备：**根据工具的模式验证参数
2. **MCP 调用：** 底层 `CallableTool` 通过以下方式调用服务器：

   ```typescript
   const functionCalls = [
     {
       name: this.serverToolName, // Original server tool name
       args: params,
     },
   ];
   ```

3. **响应处理：**结果针对LLM上下文和用户显示进行格式化

### 4。响应处理

执行结果包含：

- **`llmContent`:** 语言模型上下文的原始响应部分
- **`returnDisplay`:** 用于用户显示的格式化输出（通常在 Markdown 代码块中为 JSON）

## 如何与您的 MCP 服务器交互

### 使用`/mcp`命令

`/mcp` 命令提供有关 MCP 服务器设置的全面信息：

```bash
/mcp
```

这显示：

- **服务器列表：**所有已配置的MCP服务器
- **连接状态：**、`CONNECTED`、`CONNECTING`或`DISCONNECTED`
- **服务器详细信息：**配置摘要（不包括敏感数据）
- **可用工具：**每个服务器的工具列表及其说明
- **发现状态：**整体发现过程状态

### 示例 `/mcp` 输出

```
MCP Servers Status:

📡 pythonTools (CONNECTED)
  Command: python -m my_mcp_server --port 8080
  Working Directory: ./mcp-servers/python
  Timeout: 15000ms
  Tools: calculate_sum, file_analyzer, data_processor

🔌 nodeServer (DISCONNECTED)
  Command: node dist/server.js --verbose
  Error: Connection refused

🐳 dockerizedServer (CONNECTED)
  Command: docker run -i --rm -e API_KEY my-mcp-server:latest
  Tools: docker__deploy, docker__status

Discovery State: COMPLETED
```

### 工具使用

一旦发现，MCP 工具就可以像内置工具一样可供 Qwen 模型使用。该模型将自动：

1. **根据您的要求选择合适的工具**
2. **当前确认对话框**（除非服务器受信任）
3. **使用正确的参数执行工具**
4. **以用户友好的格式显示结果**

## 状态监控和故障排除

### 连接状态

MCP 集成跟踪多个状态：

#### 服务器状态（`MCPServerStatus`）

- **`DISCONNECTED`:** 服务器未连接或有错误
- **`CONNECTING`:** 正在进行连接尝试
- **`CONNECTED`:** 服务器已连接并准备就绪

#### 发现状态（`MCPDiscoveryState`）

- **`NOT_STARTED`:** 发现尚未开始
- **`IN_PROGRESS`:** 当前正在发现服务器
- **`COMPLETED`:** 发现完成（有或没有错误）

### 常见问题及解决方案

#### 服务器无法连接

**症状：**服务器显示`DISCONNECTED`状态

**故障排除：**

1. **检查配置：**验证`command`、`args`和`cwd`是否正确
2. **手动测试：**直接运行服务器命令以确保其有效
3. **检查依赖关系：**确保安装了所有必需的软件包
4. **查看日志：** 在 CLI 输出中查找错误消息
5. **验证权限：**确保CLI可以执行服务器命令

#### 没有发现任何工具

**症状：**服务器已连接，但没有可用的工具

**故障排除：**

1. **验证工具注册：**确保您的服务器实际注册了工具
2. **检查MCP协议：**确认您的服务器正确实现了MCP工具列表
3. **查看服务器日志：**检查 stderr 输出中是否存在服务器端错误
4. **测试工具列表：**手动测试服务器的工具发现端点

#### 工具未执行

**症状：** 工具被发现但在执行过程中失败

**故障排除：**

1. **参数验证：**确保您的工具接受预期参数
2. **架构兼容性：**验证您的输入架构是否有效JSON架构
3. **错误处理：**检查您的工具是否抛出未处理的异常
4. **超时问题：**考虑增加`timeout`设置

#### 沙盒兼容性

**症状：启用沙箱时，** MCP 服务器出现故障

**解决方案：**

1. **基于 Docker 的服务器：** 使用包含所有依赖项的 Docker 容器
2. **路径可访问性：**确保服务器可执行文件在沙箱中可用
3. **网络访问：**配置沙箱以允许必要的网络连接
4. **环境变量：**验证所需的环境变量是否已传递

### 调试技巧

1. **启用调试模式：**使用`--debug`运行CLI以获得详细输出
2. **检查stderr：** MCP服务器stderr被捕获并记录（INFO消息被过滤）
3. **测试隔离：**在集成之前独立测试您的MCP服务器
4. **增量设置：**从简单的工具开始，然后添加复杂的功能
5. **经常使用`/mcp`：**开发过程中监控服务器状态

## 重要提示

### 安全考虑

- **信任设置：** `trust` 选项仅在受信任的工作区中绕过工具确认对话框。谨慎使用并且仅用于您完全控制的服务器
- **访问令牌：**配置包含API密钥或令牌的环境变量时要注意安全
- **沙箱兼容性：**使用沙箱时，确保MCP服务器在沙箱环境中可用
- **私人数据：**使用广泛的个人访问令牌可能会导致存储库之间的信息泄漏

### 绩效和资源管理

- **连接持久性：** CLI 维护与成功注册工具的服务器的持久连接
- **自动清理：**与不提供工具的服务器的连接会自动关闭
- **超时管理：**根据服务器的响应特征配置适当的超时
- **资源监控：** MCP服务器作为单独的进程运行并消耗系统资源

### 架构兼容性

- **架构合规模式：** 默认情况下 (`schemaCompliance: "auto"`)，工具架构按原样传递。在 `settings.json` 中设置 `"model": { "generationConfig": { "schemaCompliance": "openapi_30" } }` 将模型转换为严格 OpenAPI 3.0 格式。
- **OpenAPI 3.0 转换：** 当启用 `openapi_30` 模式时，系统处理：
  - 可空类型： `["string", "null"]` -> `type: "string", nullable: true`
  - 常量值：`const: "foo"` -> `enum: ["foo"]`
  - 独占限制：数字 `exclusiveMinimum` -> 带有 `minimum` 的布尔形式
  - 关键字删除：`$schema`、`$id`、`dependencies`、`patternProperties`
- **名称清理：** 工具名称会自动清理以满足 API 要求
- **冲突解决：**服务器之间的工具名称冲突通过自动前缀解决

这种全面的集成使 MCP 服务器成为扩展 CLI 功能的强大方式，同时保持安全性、可靠性和易用性。

## 从工具返回丰富的内容

MCP 工具不仅限于返回简单文本。您可以在单个工具响应中返回丰富的多部分内容，包括文本、图像、音频和其他二进制数据。这使您可以构建强大的工具，可以一次性为模型提供多种信息。

从工具返回的所有数据都会被处理并发送到模型作为下一代的上下文，使其能够推理或总结所提供的信息。

### 它是如何运作的

要返回丰富的内容，您的工具的响应必须遵守 [`CallToolResult`](<https://modelcontextprotocol.io/specification/2025-06-18/server/tools#tool-result>) 的 MCP 规范。结果的 `content` 字段应该是 `ContentBlock` 对象的数组。 CLI 将正确处理该数组，将文本与二进制数据分离并将其打包到模型中。

您可以在 `content` 数组中混合搭配不同的内容块类型。支持的块类型包括：

- `text`
- `image`
- `audio`
- `resource`（嵌入内容）
- `resource_link`

### 示例：返回文本和图像

以下是来自 MCP 工具的有效 JSON 响应的示例，该工具返回文本描述和图像：

```json
{
  "content": [
    {
      "type": "text",
      "text": "Here is the logo you requested."
    },
    {
      "type": "image",
      "data": "BASE64_ENCODED_IMAGE_DATA_HERE",
      "mimeType": "image/png"
    },
    {
      "type": "text",
      "text": "The logo was created in 2025."
    }
  ]
}
```

当Qwen Code收到此响应时，它将：

1.  提取所有文本并将其组合成模型的单个 `functionResponse` 部分。
2.  将图像数据呈现为单独的 `inlineData` 部分。
3.  在 CLI 中提供清晰、用户友好的摘要，表明已收到文本和图像。

这使您能够构建复杂的工具，为 Qwen 模型提供丰富的多模式上下文。

## MCP 提示为斜杠命令

除了工具之外，MCP 服务器还可以公开预定义的提示，这些提示可以在 Qwen Code 中作为斜杠命令执行。这允许您为常见或复杂的查询创建快捷方式，可以通过名称轻松调用。

### 在服务器上定义提示

下面是一个定义提示的 stdio MCP 服务器的小示例：

```ts
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({
  name: 'prompt-server',
  version: '1.0.0',
});

server.registerPrompt(
  'poem-writer',
  {
    title: 'Poem Writer',
    description: 'Write a nice haiku',
    argsSchema: { title: z.string(), mood: z.string().optional() },
  },
  ({ title, mood }) => ({
    messages: [
      {
        role: 'user',
        content: {
          type: 'text',
          text: `Write a haiku${mood ? ` with the mood ${mood}` : ''} called ${title}. Note that a haiku is 5 syllables followed by 7 syllables followed by 5 syllables `,
        },
      },
    ],
  }),
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

这可以包含在 `mcpServers` 下的 `settings.json` 中：

```json
{
  "mcpServers": {
    "nodeServer": {
      "command": "node",
      "args": ["filename.ts"]
    }
  }
}
```

### 调用提示

一旦发现提示，您可以使用其名称作为斜杠命令来调用它。 CLI 将自动处理解析参数。

```bash
/poem-writer --title="Qwen Code" --mood="reverent"
```

或者，使用位置参数：

```bash
/poem-writer "Qwen Code" reverent
```

当您运行此命令时，CLI 使用提供的参数在 MCP 服务器上执行 `prompts/get` 方法。服务器负责将参数替换到提示模板中并返回最终的提示文本。然后，CLI 将此提示发送到模型以执行。这提供了一种自动化和共享公共工作流程的便捷方法。

## 使用 `qwen mcp` 管理 MCP 服务器

虽然您始终可以通过手动编辑 `settings.json` 文件来配置 MCP 服务器，但 CLI 提供了一组方便的命令来以编程方式管理服务器配置。这些命令简化了添加、列出和删除 MCP 服务器的过程，无需直接编辑 JSON 文件。

### 添加服务器 (`qwen mcp add`)

`add` 命令在您的 `settings.json` 中配置新的 MCP 服务器。根据范围 (`-s, --scope`)，它将添加到用户配置 `~/.qwen/settings.json` 或项目配置 `.qwen/settings.json` 文件中。

**命令：**

```bash
qwen mcp add [options] <name> <commandOrUrl> [args...]
```

- `<name>`：服务器的唯一名称。
- `<commandOrUrl>`：要执行的命令（对于`stdio`）或URL（对于`http`/`sse`）。
- `[args...]`：`stdio` 命令的可选参数。

**选项（标志）：**

- `-s, --scope`：配置范围（用户或项目）。 [默认：“项目”]
- `-t, --transport`：传输类型（stdio、sse、http）。 [默认：“stdio”]
- `-e, --env`：设置环境变量（例如-e KEY=值）。
- `-H, --header`：为 SSE 和 HTTP 传输设置 HTTP 标头（例如 -H "X-Api-Key: abc123" -H "Authorization: Bearer abc123"）。
- `--timeout`：设置连接超时时间（以毫秒为单位）。
- `--trust`：信任服务器（绕过受信任工作区中的工具调用确认）。
- `--description`：设置服务器的描述。
- `--include-tools`：要包含的以逗号分隔的工具列表。
- `--exclude-tools`：要排除的以逗号分隔的工具列表。
- `--oauth-client-id`：用于 MCP 服务器身份验证的 OAuth 客户端 ID。
- `--oauth-client-secret`：用于 MCP 服务器身份验证的 OAuth 客户端密钥。
- `--oauth-redirect-uri`：OAuth 重定向 URI（例如，`https://your-server.com/oauth/callback`）。本地设置默认为 `http://localhost:7777/oauth/callback`。 **对于远程部署很重要**：使用以`/oauth/callback`结尾的公共URL并将其反向代理到`http://127.0.0.1:7777/oauth/callback`。
- `--oauth-authorization-url`：OAuth授权URL。
- `--oauth-token-url`：OAuth 令牌 URL。
- `--oauth-scopes`：OAuth 范围（以逗号分隔）。

#### 添加 stdio 服务器

这是运行本地服务器的默认传输。

```bash
# Basic syntax
qwen mcp add <name> <command> [args...]

# Example: Adding a local server
qwen mcp add my-stdio-server -e API_KEY=123 /path/to/server arg1 arg2 arg3

# Example: Adding a local python server
qwen mcp add python-server python server.py --port 8080
```

#### 添加 HTTP 服务器

此传输适用于使用可流传输的 HTTP 传输的服务器。

```bash
# Basic syntax
qwen mcp add --transport http <name> <url>

# Example: Adding an HTTP server
qwen mcp add --transport http http-server https://api.example.com/mcp/

# Example: Adding an HTTP server with an authentication header
qwen mcp add --transport http secure-http https://api.example.com/mcp/ --header "Authorization: Bearer abc123"
```

#### 添加 SSE 服务器

此传输适用于使用服务器发送事件 (SSE) 的服务器。

```bash
# Basic syntax
qwen mcp add --transport sse <name> <url>

# Example: Adding an SSE server
qwen mcp add --transport sse sse-server https://api.example.com/sse/

# Example: Adding an SSE server with an authentication header
qwen mcp add --transport sse secure-sse https://api.example.com/sse/ --header "Authorization: Bearer abc123"

# Example: Adding an OAuth-enabled SSE server
qwen mcp add --transport sse oauth-server https://api.example.com/sse/ \
  --oauth-client-id your-client-id \
  --oauth-redirect-uri https://your-server.com/oauth/callback \
  --oauth-authorization-url https://provider.example.com/authorize \
  --oauth-token-url https://provider.example.com/token
```

### 管理服务器 (`/mcp`)

要查看和管理当前配置的所有 MCP 服务器，请在交互式 Qwen Code 会话中打开 `/mcp`
 对话框。此对话框可让您：

- 查看所有MCP服务器及其连接状态
- Enable/disable 服务器
- 重新连接到断开连接的服务器
- 查看各服务器提供的工具和提示
- 查看服务器日志

**命令：**

```bash
qwen
```

然后输入：

```text
/mcp
```

管理对话框提供了一个可视化界面，显示每个服务器的名称、配置详细信息、连接状态和可用的 tools/prompts.

### 删除服务器 (`qwen mcp remove`)

要从配置中删除服务器，请使用 `remove` 命令和服务器名称。

**命令：**

```bash
qwen mcp remove <name>
```

**示例：**

```bash
qwen mcp remove my-server
```

这将根据范围 (`-s, --scope`) 从适当的 `settings.json` 文件中的 `mcpServers` 对象中查找并删除“my-server”条目。


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP servers with Qwen Code

This document provides a guide to configuring and using Model Context Protocol (MCP) servers with Qwen Code.

## What is an MCP server?

An MCP server is an application that exposes tools and resources to the CLI through the Model Context Protocol, allowing it to interact with external systems and data sources. MCP servers act as a bridge between the model and your local environment or other services like APIs.

An MCP server enables the CLI to:

- **Discover tools:** List available tools, their descriptions, and parameters through standardized schema definitions.
- **Execute tools:** Call specific tools with defined arguments and receive structured responses.
- **Access resources:** Read data from specific resources (though the CLI primarily focuses on tool execution).

With an MCP server, you can extend the CLI's capabilities to perform actions beyond its built-in features, such as interacting with databases, APIs, custom scripts, or specialized workflows.

## Core Integration Architecture

Qwen Code integrates with MCP servers through a sophisticated discovery and execution system built into the core package (`packages/core/src/tools/`):

### Discovery Layer (`mcp-client.ts`)

The discovery process is orchestrated by `discoverMcpTools()`, which:

1. **Iterates through configured servers** from your `settings.json` `mcpServers` configuration
2. **Establishes connections** using appropriate transport mechanisms (Stdio, SSE, or Streamable HTTP)
3. **Fetches tool definitions** from each server using the MCP protocol
4. **Sanitizes and validates** tool schemas for compatibility with the Qwen API
5. **Registers tools** in the global tool registry with conflict resolution

### Execution Layer (`mcp-tool.ts`)

Each discovered MCP tool is wrapped in a `DiscoveredMCPTool` instance that:

- **Handles confirmation logic** based on server trust settings and user preferences
- **Manages tool execution** by calling the MCP server with proper parameters
- **Processes responses** for both the LLM context and user display
- **Maintains connection state** and handles timeouts

After a connection loss, the current invocation is replayed only for a trusted server in a trusted workspace when the tool explicitly declares `idempotentHint: true`, or declares `readOnlyHint: true` without a conflicting `destructiveHint: true` or `idempotentHint: false`. Missing or conflicting annotations, and annotations from an untrusted server or workspace, are treated as unsafe because the server may have completed a side effect before the response was lost. Tool authors should publish accurate MCP annotations; administrators should still verify them before enabling server trust.

### Transport Mechanisms

The CLI supports three MCP transport types:

- **Stdio Transport:** Spawns a subprocess and communicates via stdin/stdout
- **SSE Transport:** Connects to Server-Sent Events endpoints
- **Streamable HTTP Transport:** Uses HTTP streaming for communication

## How to set up your MCP server

Qwen Code uses the `mcpServers` configuration in your `settings.json` file to locate and connect to MCP servers. This configuration supports multiple servers with different transport mechanisms.

### Configure the MCP server in settings.json

You can configure MCP servers in your `settings.json` file in two main ways: through the top-level `mcpServers` object for specific server definitions, and through the `mcp` object for global settings that control server discovery and execution.

#### Global MCP Settings (`mcp`)

The `mcp` object in your `settings.json` allows you to define global rules for all MCP servers.

- **`mcp.serverCommand`** (string): A global command to start an MCP server.
- **`mcp.allowed`** (array of strings): A list of MCP server names to allow. If this is set, only servers from this list (matching the keys in the `mcpServers` object) will be connected to.
- **`mcp.excluded`** (array of strings): A list of MCP server names to exclude. Servers in this list will not be connected to.

**Example:**

```json
{
  "mcp": {
    "allowed": ["my-trusted-server"],
    "excluded": ["experimental-server"]
  }
}
```

#### Server-Specific Configuration (`mcpServers`)

The `mcpServers` object is where you define each individual MCP server you want the CLI to connect to.

### Configuration Structure

Add an `mcpServers` object to your `settings.json` file:

```json
{ ...file contains other config objects
  "mcpServers": {
    "serverName": {
      "command": "path/to/server",
      "args": ["--arg1", "value1"],
      "env": {
        "API_KEY": "$MY_API_TOKEN"
      },
      "cwd": "./server-directory",
      "timeout": 30000,
      "trust": false
    }
  }
}
```

### Configuration Properties

Each server configuration supports the following properties:

#### Required (one of the following)

- **`command`** (string): Path to the executable for Stdio transport
- **`url`** (string): SSE endpoint URL (e.g., `"http://localhost:8080/sse"`)
- **`httpUrl`** (string): HTTP streaming endpoint URL

#### Optional

- **`args`** (string[]): Command-line arguments for Stdio transport
- **`headers`** (object): Custom HTTP headers when using `url` or `httpUrl`
- **`env`** (object): Environment variables for the server process. Values can reference environment variables using `$VAR_NAME` or `${VAR_NAME}` syntax
- **`cwd`** (string): Working directory for Stdio transport
- **`timeout`** (number): Request timeout in milliseconds (default: 600,000ms = 10 minutes)
- **`versionNegotiation`** (`"auto" | "legacy"`, default: `"legacy"`): For Stdio servers, `"auto"` opts into the `server/discover` probe on a disposable sibling process.
- **`trust`** (boolean): When `true`, bypasses tool call confirmations for this server in a trusted workspace (default: `false`)
- **`includeTools`** (string[]): List of tool names to include from this MCP server. When specified, only the tools listed here will be available from this server (allowlist behavior). If not specified, all tools from the server are enabled by default.
- **`excludeTools`** (string[]): List of tool names to exclude from this MCP server. Tools listed here will not be available to the model, even if they are exposed by the server. **Note:** `excludeTools` takes precedence over `includeTools` - if a tool is in both lists, it will be excluded.
- **`targetAudience`** (string): The OAuth Client ID allowlisted on the IAP-protected application you are trying to access. Used with `authProviderType: 'service_account_impersonation'`.
- **`targetServiceAccount`** (string): The email address of the Google Cloud Service Account to impersonate. Used with `authProviderType: 'service_account_impersonation'`.

### OAuth Support for Remote MCP Servers

Qwen Code supports OAuth 2.0 authentication for remote MCP servers using SSE or HTTP transports. This enables secure access to MCP servers that require authentication.

#### Automatic OAuth Discovery

For servers that support OAuth discovery, you can omit the OAuth configuration and let the CLI discover it automatically:

```json
{
  "mcpServers": {
    "discoveredServer": {
      "url": "https://api.example.com/sse"
    }
  }
}
```

The CLI will automatically:

- Detect when a server requires OAuth authentication (401 responses)
- Discover OAuth endpoints from server metadata
- Perform dynamic client registration if supported
- Handle the OAuth flow and token management

#### Authentication Flow

When connecting to an OAuth-enabled server:

1. **Initial connection attempt** fails with 401 Unauthorized
2. **OAuth discovery** finds authorization and token endpoints
3. **Browser opens** for user authentication (requires local browser access)
4. **Authorization code** is exchanged for access tokens
5. **Tokens are stored** securely for future use
6. **Connection retry** succeeds with valid tokens

#### Browser Redirect Requirements

**Important:** OAuth authentication requires that the redirect URI is accessible:

- **Default behavior**: Redirects to `http://localhost:7777/oauth/callback` (works for local setups)
- **Custom redirect URI**: Use `--oauth-redirect-uri` or configure `redirectUri` in settings.json to specify a public URL ending in `/oauth/callback`. Reverse-proxy that path to `http://127.0.0.1:7777/oauth/callback` on the machine running Qwen Code.

For **remote/cloud server deployments** (e.g., web terminals, SSH sessions, cloud IDEs):

- The default `localhost` redirect will NOT work
- You MUST configure a custom `redirectUri` pointing to a publicly accessible URL ending in `/oauth/callback`
- Terminate TLS at a reverse proxy and forward only that path to `http://127.0.0.1:7777/oauth/callback`

Example for remote servers:

```bash
qwen mcp add --transport sse remote-server https://api.example.com/sse/ \
  --oauth-redirect-uri https://your-remote-server.example.com/oauth/callback
```

OAuth will not work in:

- Headless environments without browser access
- Environments where the configured `redirectUri` is unreachable from the user's browser

#### Managing OAuth Authentication

Use the `/mcp` dialog inside an interactive Qwen Code session to inspect MCP
servers and manage OAuth authentication.

#### OAuth Configuration Properties

- **`enabled`** (boolean): Enable OAuth for this server
- **`clientId`** (string): OAuth client identifier (optional with dynamic registration)
- **`clientSecret`** (string): OAuth client secret (optional for public clients)
- **`authorizationUrl`** (string): OAuth authorization endpoint (auto-discovered if omitted)
- **`tokenUrl`** (string): OAuth token endpoint (auto-discovered if omitted)
- **`scopes`** (string[]): Required OAuth scopes
- **`redirectUri`** (string): Custom redirect URI. **Critical for remote deployments**: Defaults to `http://localhost:7777/oauth/callback`. For remote use, set a public URL ending in `/oauth/callback` and reverse-proxy it to the local callback listener. Can be configured via `qwen mcp add --oauth-redirect-uri` or directly in settings.json.
- **`tokenParamName`** (string): Query parameter name for tokens in SSE URLs
- **`audiences`** (string[]): Audiences the token is valid for

#### Token Management

OAuth tokens are automatically:

- **Stored** in `~/.qwen/mcp-oauth-tokens.json` (plaintext, mode 0600) by default. If `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` is set, Qwen Code uses keychain-backed storage where available, or `~/.qwen/mcp-oauth-tokens-v2.json` with AES-256-GCM encryption.
- **Refreshed** when expired (if refresh tokens are available)
- **Validated** before each connection attempt
- **Cleaned up** when invalid or expired

> [!WARNING]
> By default, OAuth tokens are stored unencrypted on disk. On shared or multi-user machines, set `QWEN_CODE_FORCE_ENCRYPTED_FILE_STORAGE=true` to protect credentials.

#### Authentication Provider Type

You can specify the authentication provider type using the `authProviderType` property:

- **`authProviderType`** (string): Specifies the authentication provider. Can be one of the following:
  - **`dynamic_discovery`** (default): The CLI will automatically discover the OAuth configuration from the server.
  - **`google_credentials`**: The CLI will use the Google Application Default Credentials (ADC) to authenticate with the server. When using this provider, you must specify the required scopes.
  - **`service_account_impersonation`**: The CLI will impersonate a Google Cloud Service Account to authenticate with the server. This is useful for accessing IAP-protected services (this was specifically designed for Cloud Run services).

#### Google Credentials

```json
{
  "mcpServers": {
    "googleCloudServer": {
      "httpUrl": "https://my-gcp-service.run.app/mcp",
      "authProviderType": "google_credentials",
      "oauth": {
        "scopes": ["https://www.googleapis.com/auth/userinfo.email"]
      }
    }
  }
}
```

#### Service Account Impersonation

To authenticate with a server using Service Account Impersonation, you must set the `authProviderType` to `service_account_impersonation` and provide the following properties:

- **`targetAudience`** (string): The OAuth Client ID allowslisted on the IAP-protected application you are trying to access.
- **`targetServiceAccount`** (string): The email address of the Google Cloud Service Account to impersonate.

The CLI will use your local Application Default Credentials (ADC) to generate an OIDC ID token for the specified service account and audience. This token will then be used to authenticate with the MCP server.

#### Setup Instructions

1. **[Create](<https://cloud.google.com/iap/docs/oauth-client-creation>) or use an existing OAuth 2.0 client ID.** To use an existing OAuth 2.0 client ID, follow the steps in [How to share OAuth Clients](<https://cloud.google.com/iap/docs/sharing-oauth-clients>).
2. **Add the OAuth ID to the allowlist for [programmatic access](<https://cloud.google.com/iap/docs/sharing-oauth-clients#programmatic_access>) for the application.** Since Cloud Run is not yet a supported resource type in gcloud iap, you must allowlist the Client ID on the project.
3. **Create a service account.** [Documentation](<https://cloud.google.com/iam/docs/service-accounts-create#creating>), [Cloud Console Link](<https://console.cloud.google.com/iam-admin/serviceaccounts>)
4. **Add both the service account and users to the IAP Policy** in the "Security" tab of the Cloud Run service itself or via gcloud.
5. **Grant all users and groups** who will access the MCP Server the necessary permissions to [impersonate the service account](<https://cloud.google.com/docs/authentication/use-service-account-impersonation>) (i.e., `roles/iam.serviceAccountTokenCreator`).
6. **[Enable](<https://console.cloud.google.com/apis/library/iamcredentials.googleapis.com>) the IAM Credentials API** for your project.

### Example Configurations

#### Python MCP Server (Stdio)

```json
{
  "mcpServers": {
    "pythonTools": {
      "command": "python",
      "args": ["-m", "my_mcp_server", "--port", "8080"],
      "cwd": "./mcp-servers/python",
      "env": {
        "DATABASE_URL": "$DB_CONNECTION_STRING",
        "API_KEY": "${EXTERNAL_API_KEY}"
      },
      "timeout": 15000
    }
  }
}
```

#### Node.js MCP Server (Stdio)

```json
{
  "mcpServers": {
    "nodeServer": {
      "command": "node",
      "args": ["dist/server.js", "--verbose"],
      "cwd": "./mcp-servers/node",
      "trust": true
    }
  }
}
```

#### Docker-based MCP Server

```json
{
  "mcpServers": {
    "dockerizedServer": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "-e",
        "API_KEY",
        "-v",
        "${PWD}:/workspace",
        "my-mcp-server:latest"
      ],
      "env": {
        "API_KEY": "$EXTERNAL_SERVICE_TOKEN"
      }
    }
  }
}
```

#### HTTP-based MCP Server

```json
{
  "mcpServers": {
    "httpServer": {
      "httpUrl": "http://localhost:3000/mcp",
      "timeout": 5000
    }
  }
}
```

#### HTTP-based MCP Server with Custom Headers

```json
{
  "mcpServers": {
    "httpServerWithAuth": {
      "httpUrl": "http://localhost:3000/mcp",
      "headers": {
        "Authorization": "Bearer your-api-token",
        "X-Custom-Header": "custom-value",
        "Content-Type": "application/json"
      },
      "timeout": 5000
    }
  }
}
```

#### MCP Server with Tool Filtering

```json
{
  "mcpServers": {
    "filteredServer": {
      "command": "python",
      "args": ["-m", "my_mcp_server"],
      "includeTools": ["safe_tool", "file_reader", "data_processor"],
      // "excludeTools": ["dangerous_tool", "file_deleter"],
      "timeout": 30000
    }
  }
}
```

### SSE MCP Server with SA Impersonation

```json
{
  "mcpServers": {
    "myIapProtectedServer": {
      "url": "https://my-iap-service.run.app/sse",
      "authProviderType": "service_account_impersonation",
      "targetAudience": "YOUR_IAP_CLIENT_ID.apps.googleusercontent.com",
      "targetServiceAccount": "your-sa@your-project.iam.gserviceaccount.com"
    }
  }
}
```

## Discovery Process Deep Dive

When Qwen Code starts, it performs MCP server discovery through the following detailed process:

### 1. Server Iteration and Connection

For each configured server in `mcpServers`:

1. **Status tracking begins:** Server status is set to `CONNECTING`
2. **Transport selection:** Based on configuration properties:
   - `httpUrl` → `StreamableHTTPClientTransport`
   - `url` → `SSEClientTransport`
   - `command` → `StdioClientTransport`
3. **Connection establishment:** The MCP client attempts to connect with the configured timeout
4. **Error handling:** Connection failures are logged and the server status is set to `DISCONNECTED`

### 2. Tool Discovery

Upon successful connection:

1. **Tool listing:** The client calls the MCP server's tool listing endpoint
2. **Schema validation:** Each tool's function declaration is validated
3. **Tool filtering:** Tools are filtered based on `includeTools` and `excludeTools` configuration
4. **Name sanitization:** Tool names are cleaned to meet Qwen API requirements:
   - Invalid characters (non-alphanumeric, underscore, dot, hyphen) are replaced with underscores
   - Names longer than 63 characters are truncated with middle replacement (`___`)

### 3. Conflict Resolution

When multiple servers expose tools with the same name:

1. **First registration wins:** The first server to register a tool name gets the unprefixed name
2. **Automatic prefixing:** Subsequent servers get prefixed names: `serverName__toolName`
3. **Registry tracking:** The tool registry maintains mappings between server names and their tools

### 4. Schema Processing

Tool parameter schemas undergo sanitization for API compatibility:

- **`$schema` properties** are removed
- **`additionalProperties`** are stripped
- **`anyOf` with `default`** have their default values removed (Vertex AI compatibility)
- **Recursive processing** applies to nested schemas

### 5. Connection Management

After discovery:

- **Persistent connections:** Servers that successfully register tools maintain their connections
- **Cleanup:** Servers that provide no usable tools have their connections closed
- **Status updates:** Final server statuses are set to `CONNECTED` or `DISCONNECTED`

## Tool Execution Flow

When the model decides to use an MCP tool, the following execution flow occurs:

### 1. Tool Invocation

The model generates a `FunctionCall` with:

- **Tool name:** The registered name (potentially prefixed)
- **Arguments:** JSON object matching the tool's parameter schema

### 2. Confirmation Process

Each `DiscoveredMCPTool` implements sophisticated confirmation logic:

#### Trust-based Bypass

```typescript
if (this.trust === true && this.cliConfig?.isTrustedFolder()) {
  return 'allow';
}
return 'ask';
```

#### Dynamic Allow-listing

The system maintains internal allow-lists for:

- **Server-level:** `serverName` → All tools from this server are trusted
- **Tool-level:** `serverName.toolName` → This specific tool is trusted

#### User Choice Handling

When confirmation is required, users can choose:

- **Proceed once:** Execute this time only
- **Always allow this tool:** Add to tool-level allow-list
- **Always allow this server:** Add to server-level allow-list
- **Cancel:** Abort execution

### 3. Execution

Upon confirmation (or trust bypass):

1. **Parameter preparation:** Arguments are validated against the tool's schema
2. **MCP call:** The underlying `CallableTool` invokes the server with:

   ```typescript
   const functionCalls = [
     {
       name: this.serverToolName, // Original server tool name
       args: params,
     },
   ];
   ```

3. **Response processing:** Results are formatted for both LLM context and user display

### 4. Response Handling

The execution result contains:

- **`llmContent`:** Raw response parts for the language model's context
- **`returnDisplay`:** Formatted output for user display (often JSON in markdown code blocks)

## How to interact with your MCP server

### Using the `/mcp` Command

The `/mcp` command provides comprehensive information about your MCP server setup:

```bash
/mcp
```

This displays:

- **Server list:** All configured MCP servers
- **Connection status:** `CONNECTED`, `CONNECTING`, or `DISCONNECTED`
- **Server details:** Configuration summary (excluding sensitive data)
- **Available tools:** List of tools from each server with descriptions
- **Discovery state:** Overall discovery process status

### Example `/mcp` Output

```
MCP Servers Status:

📡 pythonTools (CONNECTED)
  Command: python -m my_mcp_server --port 8080
  Working Directory: ./mcp-servers/python
  Timeout: 15000ms
  Tools: calculate_sum, file_analyzer, data_processor

🔌 nodeServer (DISCONNECTED)
  Command: node dist/server.js --verbose
  Error: Connection refused

🐳 dockerizedServer (CONNECTED)
  Command: docker run -i --rm -e API_KEY my-mcp-server:latest
  Tools: docker__deploy, docker__status

Discovery State: COMPLETED
```

### Tool Usage

Once discovered, MCP tools are available to the Qwen model like built-in tools. The model will automatically:

1. **Select appropriate tools** based on your requests
2. **Present confirmation dialogs** (unless the server is trusted)
3. **Execute tools** with proper parameters
4. **Display results** in a user-friendly format

## Status Monitoring and Troubleshooting

### Connection States

The MCP integration tracks several states:

#### Server Status (`MCPServerStatus`)

- **`DISCONNECTED`:** Server is not connected or has errors
- **`CONNECTING`:** Connection attempt in progress
- **`CONNECTED`:** Server is connected and ready

#### Discovery State (`MCPDiscoveryState`)

- **`NOT_STARTED`:** Discovery hasn't begun
- **`IN_PROGRESS`:** Currently discovering servers
- **`COMPLETED`:** Discovery finished (with or without errors)

### Common Issues and Solutions

#### Server Won't Connect

**Symptoms:** Server shows `DISCONNECTED` status

**Troubleshooting:**

1. **Check configuration:** Verify `command`, `args`, and `cwd` are correct
2. **Test manually:** Run the server command directly to ensure it works
3. **Check dependencies:** Ensure all required packages are installed
4. **Review logs:** Look for error messages in the CLI output
5. **Verify permissions:** Ensure the CLI can execute the server command

#### No Tools Discovered

**Symptoms:** Server connects but no tools are available

**Troubleshooting:**

1. **Verify tool registration:** Ensure your server actually registers tools
2. **Check MCP protocol:** Confirm your server implements the MCP tool listing correctly
3. **Review server logs:** Check stderr output for server-side errors
4. **Test tool listing:** Manually test your server's tool discovery endpoint

#### Tools Not Executing

**Symptoms:** Tools are discovered but fail during execution

**Troubleshooting:**

1. **Parameter validation:** Ensure your tool accepts the expected parameters
2. **Schema compatibility:** Verify your input schemas are valid JSON Schema
3. **Error handling:** Check if your tool is throwing unhandled exceptions
4. **Timeout issues:** Consider increasing the `timeout` setting

#### Sandbox Compatibility

**Symptoms:** MCP servers fail when sandboxing is enabled

**Solutions:**

1. **Docker-based servers:** Use Docker containers that include all dependencies
2. **Path accessibility:** Ensure server executables are available in the sandbox
3. **Network access:** Configure sandbox to allow necessary network connections
4. **Environment variables:** Verify required environment variables are passed through

### Debugging Tips

1. **Enable debug mode:** Run the CLI with `--debug` for verbose output
2. **Check stderr:** MCP server stderr is captured and logged (INFO messages filtered)
3. **Test isolation:** Test your MCP server independently before integrating
4. **Incremental setup:** Start with simple tools before adding complex functionality
5. **Use `/mcp` frequently:** Monitor server status during development

## Important Notes

### Security Considerations

- **Trust settings:** The `trust` option bypasses tool confirmation dialogs only in a trusted workspace. Use cautiously and only for servers you completely control
- **Access tokens:** Be security-aware when configuring environment variables containing API keys or tokens
- **Sandbox compatibility:** When using sandboxing, ensure MCP servers are available within the sandbox environment
- **Private data:** Using broadly scoped personal access tokens can lead to information leakage between repositories

### Performance and Resource Management

- **Connection persistence:** The CLI maintains persistent connections to servers that successfully register tools
- **Automatic cleanup:** Connections to servers providing no tools are automatically closed
- **Timeout management:** Configure appropriate timeouts based on your server's response characteristics
- **Resource monitoring:** MCP servers run as separate processes and consume system resources

### Schema Compatibility

- **Schema compliance mode:** By default (`schemaCompliance: "auto"`), tool schemas are passed through as-is. Set `"model": { "generationConfig": { "schemaCompliance": "openapi_30" } }` in your `settings.json` to convert models to Strict OpenAPI 3.0 format.
- **OpenAPI 3.0 transformations:** When `openapi_30` mode is enabled, the system handles:
  - Nullable types: `["string", "null"]` -> `type: "string", nullable: true`
  - Const values: `const: "foo"` -> `enum: ["foo"]`
  - Exclusive limits: numeric `exclusiveMinimum` -> boolean form with `minimum`
  - Keyword removal: `$schema`, `$id`, `dependencies`, `patternProperties`
- **Name sanitization:** Tool names are automatically sanitized to meet API requirements
- **Conflict resolution:** Tool name conflicts between servers are resolved through automatic prefixing

This comprehensive integration makes MCP servers a powerful way to extend the CLI's capabilities while maintaining security, reliability, and ease of use.

## Returning Rich Content from Tools

MCP tools are not limited to returning simple text. You can return rich, multi-part content, including text, images, audio, and other binary data in a single tool response. This allows you to build powerful tools that can provide diverse information to the model in a single turn.

All data returned from the tool is processed and sent to the model as context for its next generation, enabling it to reason about or summarize the provided information.

### How It Works

To return rich content, your tool's response must adhere to the MCP specification for a [`CallToolResult`](<https://modelcontextprotocol.io/specification/2025-06-18/server/tools#tool-result>). The `content` field of the result should be an array of `ContentBlock` objects. The CLI will correctly process this array, separating text from binary data and packaging it for the model.

You can mix and match different content block types in the `content` array. The supported block types include:

- `text`
- `image`
- `audio`
- `resource` (embedded content)
- `resource_link`

### Example: Returning Text and an Image

Here is an example of a valid JSON response from an MCP tool that returns both a text description and an image:

```json
{
  "content": [
    {
      "type": "text",
      "text": "Here is the logo you requested."
    },
    {
      "type": "image",
      "data": "BASE64_ENCODED_IMAGE_DATA_HERE",
      "mimeType": "image/png"
    },
    {
      "type": "text",
      "text": "The logo was created in 2025."
    }
  ]
}
```

When Qwen Code receives this response, it will:

1.  Extract all the text and combine it into a single `functionResponse` part for the model.
2.  Present the image data as a separate `inlineData` part.
3.  Provide a clean, user-friendly summary in the CLI, indicating that both text and an image were received.

This enables you to build sophisticated tools that can provide rich, multi-modal context to the Qwen model.

## MCP Prompts as Slash Commands

In addition to tools, MCP servers can expose predefined prompts that can be executed as slash commands within Qwen Code. This allows you to create shortcuts for common or complex queries that can be easily invoked by name.

### Defining Prompts on the Server

Here's a small example of a stdio MCP server that defines prompts:

```ts
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import { z } from 'zod';

const server = new McpServer({
  name: 'prompt-server',
  version: '1.0.0',
});

server.registerPrompt(
  'poem-writer',
  {
    title: 'Poem Writer',
    description: 'Write a nice haiku',
    argsSchema: { title: z.string(), mood: z.string().optional() },
  },
  ({ title, mood }) => ({
    messages: [
      {
        role: 'user',
        content: {
          type: 'text',
          text: `Write a haiku${mood ? ` with the mood ${mood}` : ''} called ${title}. Note that a haiku is 5 syllables followed by 7 syllables followed by 5 syllables `,
        },
      },
    ],
  }),
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

This can be included in `settings.json` under `mcpServers` with:

```json
{
  "mcpServers": {
    "nodeServer": {
      "command": "node",
      "args": ["filename.ts"]
    }
  }
}
```

### Invoking Prompts

Once a prompt is discovered, you can invoke it using its name as a slash command. The CLI will automatically handle parsing arguments.

```bash
/poem-writer --title="Qwen Code" --mood="reverent"
```

or, using positional arguments:

```bash
/poem-writer "Qwen Code" reverent
```

When you run this command, the CLI executes the `prompts/get` method on the MCP server with the provided arguments. The server is responsible for substituting the arguments into the prompt template and returning the final prompt text. The CLI then sends this prompt to the model for execution. This provides a convenient way to automate and share common workflows.

## Managing MCP Servers with `qwen mcp`

While you can always configure MCP servers by manually editing your `settings.json` file, the CLI provides a convenient set of commands to manage your server configurations programmatically. These commands streamline the process of adding, listing, and removing MCP servers without needing to directly edit JSON files.

### Adding a Server (`qwen mcp add`)

The `add` command configures a new MCP server in your `settings.json`. Based on the scope (`-s, --scope`), it will be added to either the user config `~/.qwen/settings.json` or the project config `.qwen/settings.json` file.

**Command:**

```bash
qwen mcp add [options] <name> <commandOrUrl> [args...]
```

- `<name>`: A unique name for the server.
- `<commandOrUrl>`: The command to execute (for `stdio`) or the URL (for `http`/`sse`).
- `[args...]`: Optional arguments for a `stdio` command.

**Options (Flags):**

- `-s, --scope`: Configuration scope (user or project). [default: "project"]
- `-t, --transport`: Transport type (stdio, sse, http). [default: "stdio"]
- `-e, --env`: Set environment variables (e.g. -e KEY=value).
- `-H, --header`: Set HTTP headers for SSE and HTTP transports (e.g. -H "X-Api-Key: abc123" -H "Authorization: Bearer abc123").
- `--timeout`: Set connection timeout in milliseconds.
- `--trust`: Trust the server (bypass its tool call confirmations in a trusted workspace).
- `--description`: Set the description for the server.
- `--include-tools`: A comma-separated list of tools to include.
- `--exclude-tools`: A comma-separated list of tools to exclude.
- `--oauth-client-id`: OAuth client ID for MCP server authentication.
- `--oauth-client-secret`: OAuth client secret for MCP server authentication.
- `--oauth-redirect-uri`: OAuth redirect URI (e.g., `https://your-server.com/oauth/callback`). Defaults to `http://localhost:7777/oauth/callback` for local setups. **Important for remote deployments**: Use a public URL ending in `/oauth/callback` and reverse-proxy it to `http://127.0.0.1:7777/oauth/callback`.
- `--oauth-authorization-url`: OAuth authorization URL.
- `--oauth-token-url`: OAuth token URL.
- `--oauth-scopes`: OAuth scopes (comma-separated).

#### Adding an stdio server

This is the default transport for running local servers.

```bash
# Basic syntax
qwen mcp add <name> <command> [args...]

# Example: Adding a local server
qwen mcp add my-stdio-server -e API_KEY=123 /path/to/server arg1 arg2 arg3

# Example: Adding a local python server
qwen mcp add python-server python server.py --port 8080
```

#### Adding an HTTP server

This transport is for servers that use the streamable HTTP transport.

```bash
# Basic syntax
qwen mcp add --transport http <name> <url>

# Example: Adding an HTTP server
qwen mcp add --transport http http-server https://api.example.com/mcp/

# Example: Adding an HTTP server with an authentication header
qwen mcp add --transport http secure-http https://api.example.com/mcp/ --header "Authorization: Bearer abc123"
```

#### Adding an SSE server

This transport is for servers that use Server-Sent Events (SSE).

```bash
# Basic syntax
qwen mcp add --transport sse <name> <url>

# Example: Adding an SSE server
qwen mcp add --transport sse sse-server https://api.example.com/sse/

# Example: Adding an SSE server with an authentication header
qwen mcp add --transport sse secure-sse https://api.example.com/sse/ --header "Authorization: Bearer abc123"

# Example: Adding an OAuth-enabled SSE server
qwen mcp add --transport sse oauth-server https://api.example.com/sse/ \
  --oauth-client-id your-client-id \
  --oauth-redirect-uri https://your-server.com/oauth/callback \
  --oauth-authorization-url https://provider.example.com/authorize \
  --oauth-token-url https://provider.example.com/token
```

### Managing Servers (`/mcp`)

To view and manage all MCP servers currently configured, open the `/mcp`
dialog inside an interactive Qwen Code session. This dialog lets you:

- View all MCP servers with their connection status
- Enable/disable servers
- Reconnect to disconnected servers
- View tools and prompts provided by each server
- View server logs

**Command:**

```bash
qwen
```

Then enter:

```text
/mcp
```

The management dialog provides a visual interface showing each server's name, configuration details, connection status, and available tools/prompts.

### Removing a Server (`qwen mcp remove`)

To delete a server from your configuration, use the `remove` command with the server's name.

**Command:**

```bash
qwen mcp remove <name>
```

**Example:**

```bash
qwen mcp remove my-server
```

This will find and delete the "my-server" entry from the `mcpServers` object in the appropriate `settings.json` file based on the scope (`-s, --scope`).


</details>
