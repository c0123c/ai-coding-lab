# Copilot SDK 的 Azure 托管身份认证

通过令牌提供回调接入 Azure Identity，替代固定 API 密钥；包含前提、代码样例、环境配置和适用场景。

- 来源：GitHub／Copilot
- 资料类型：存档文档
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-07-21](<https://github.com/github/docs/commit/129b085ae236bb2cf21aa7b998666b754ed2263e>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/copilot-sdk/setup/azure-managed-identity.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/Copilot SDK 的 Azure 托管身份认证.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Azure managed identity with BYOK
shortTitle: Azure Managed Identity
intro: >-
  The GitHub Copilot SDK's [AUTOTITLE](/copilot/how-tos/copilot-sdk/auth/byok) supports static API
  keys, but Azure deployments often use **Managed Identity** (Microsoft Entra
  ID) instead of long-lived keys. The GitHub Copilot SDK is designed to compose
  with the Azure Identity SDK for maximum flexibility. Supply a bearer token
  provider callback that can fetch fresh tokens on demand using an Azure
  Identity SDK API.
versions:
  fpt: '*'
  ghec: '*'
redirect_from:
  - /copilot/how-tos/copilot-sdk/set-up-copilot-sdk/azure-managed-identity
contentType: how-tos
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

本指南介绍如何使用 Azure Identity SDK API 通过 GitHub Copilot SDK 对 Microsoft Foundry 模型进行身份验证。大多数语言使用`DefaultAzureCredential`； Rust 在本地使用 `DeveloperToolsCredential`，在 Azure 中使用 `ManagedIdentityCredential`。

## 它是如何运作的

Microsoft Foundry 的 OpenAI 兼容端点 (`https://<resource-name>.openai.azure.com/openai/v1/`) 接受来自 Microsoft Entra ID 的不记名令牌来代替静态 API 密钥。本指南使用令牌提供程序回调，因此 GitHub Copilot SDK 运行时可以按需请求新令牌。

以Python为例，流程为：

1. 为您的环境配置 `DefaultAzureCredential`。
1. 在 BYOK 提供程序配置的 `bearer_token_provider` 中传递一个回调，该回调使用 `DefaultAzureCredential` 获取 `https://ai.azure.com/.default` 范围的令牌。
1. 让 GitHub Copilot SDK 通过该回调按需请求新令牌。

![图表：显示所描述过程的序列图。](<https://github.com/assets/images/help/copilot/copilot-sdk/setup-azure-managed-identity-diagram-0.png>)

## 代码示例

### 先决条件

安装适合您的语言的 Azure Identity 和 GitHub Copilot SDK 包：

{% codetabs %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```bash
dotnet add package GitHub.Copilot.SDK
dotnet add package Azure.Core
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```bash
go get github.com/github/copilot-sdk/go
go get github.com/Azure/azure-sdk-for-go/sdk/azidentity
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```xml
<dependency>
    <groupId>com.github</groupId>
    <artifactId>copilot-sdk-java</artifactId>
    <version>${copilot.sdk.version}</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>${azure.identity.version}</version>
</dependency>
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```bash
pip install github-copilot-sdk azure-identity
```

{% endcodetab %}
{% codetab rust %}

<!-- docs-validate: skip -->

```bash
cargo add github-copilot-sdk azure_identity azure_core
cargo add tokio --features macros,rt-multi-thread
```

{% endcodetab %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```bash
npm install @github/copilot-sdk @azure/identity
```

{% endcodetab %}
{% endcodetabs %}

### 使用令牌提供者回调

当您希望 GitHub Copilot SDK 运行时通过您提供的回调按需请求新令牌时，请使用此方法。 Azure Identity SDK 处理令牌缓存和刷新计时。

以下是特定于语言的实现：

{% codetabs %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```csharp
using Azure.Core;
using Azure.Identity;
using GitHub.Copilot;

DefaultAzureCredential credential = new(
    DefaultAzureCredential.DefaultEnvironmentVariableName);
await using CopilotClient client = new();
string foundryUrl = Environment.GetEnvironmentVariable("FOUNDRY_RESOURCE_URL")!;

await using CopilotSession session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.5",
    Provider = new ProviderConfig
    {
        Type = "openai",
        BaseUrl = $"{foundryUrl}/openai/v1/",
        BearerTokenProvider = async _ =>
        {
            AccessToken token = await credential.GetTokenAsync(
                new TokenRequestContext(["https://ai.azure.com/.default"]));
            return token.Token;
        },
        WireApi = "responses",
    },
});

AssistantMessageEvent? response = await session.SendAndWaitAsync(
    new MessageOptions { Prompt = "Hello from Managed Identity!" });
Console.WriteLine(response?.Data.Content);
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
package main

import (
	"context"
	"fmt"
	"log"
	"os"
	"time"

	"github.com/Azure/azure-sdk-for-go/sdk/azcore/policy"
	"github.com/Azure/azure-sdk-for-go/sdk/azidentity"
	copilot "github.com/github/copilot-sdk/go"
)
func main() {
	opts := azidentity.DefaultAzureCredentialOptions{RequireAzureTokenCredentials: true}
	credential, err := azidentity.NewDefaultAzureCredential(&opts)
	if err != nil {
		log.Fatal(err)
	}

	getBearerToken := func(args copilot.ProviderTokenArgs) (string, error) {
		token, err := credential.GetToken(context.Background(), policy.TokenRequestOptions{
			Scopes: []string{"https://ai.azure.com/.default"},
		})
		if err != nil {
			return "", err
		}
		return token.Token, nil
	}

	client := copilot.NewClient(nil)
	if err := client.Start(context.Background()); err != nil {
		log.Fatal(err)
	}
	defer client.Stop()

	foundryURL := os.Getenv("FOUNDRY_RESOURCE_URL")

	session, err := client.CreateSession(context.Background(), &copilot.SessionConfig{
		Model: "gpt-5.5",
		Provider: &copilot.ProviderConfig{
			Type:                "openai",
			BaseURL:             fmt.Sprintf("%s/openai/v1/", foundryURL),
			BearerTokenProvider: getBearerToken,
			WireAPI:             "responses",
		},
	})
	if err != nil {
		log.Fatal(err)
	}
	defer session.Disconnect()

	ctx, cancel := context.WithTimeout(context.Background(), 60*time.Second)
	defer cancel()

	response, err := session.SendAndWait(ctx, copilot.MessageOptions{
		Prompt: "Hello from Managed Identity!",
	})
	if err != nil {
		log.Fatal(err)
	}

	if response != nil {
		if data, ok := response.Data.(*copilot.AssistantMessageData); ok {
			fmt.Println(data.Content)
		}
	}
}
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.azure.core.credential.TokenRequestContext;
import com.azure.identity.AzureIdentityEnvVars;
import com.azure.identity.DefaultAzureCredentialBuilder;
import com.github.copilot.CopilotClient;
import com.github.copilot.generated.AssistantMessageEvent;
import com.github.copilot.rpc.BearerTokenProvider;
import com.github.copilot.rpc.MessageOptions;
import com.github.copilot.rpc.ProviderConfig;
import com.github.copilot.rpc.SessionConfig;

public class ManagedIdentityExample {
    public static void main(String[] args) throws Exception {
        var credential = new DefaultAzureCredentialBuilder()
                .requireEnvVars(AzureIdentityEnvVars.AZURE_TOKEN_CREDENTIALS)
                .build();
        BearerTokenProvider tokenProvider = providerArgs ->
            credential
                .getToken(new TokenRequestContext().addScopes("https://ai.azure.com/.default"))
                .map(accessToken -> accessToken.getToken())
                .toFuture();
        String foundryUrl = System.getenv("FOUNDRY_RESOURCE_URL");

        try (var client = new CopilotClient()) {
            client.start().get();

            var session = client.createSession(new SessionConfig()
                    .setModel("gpt-5.5")
                    .setProvider(new ProviderConfig()
                            .setType("openai")
                            .setBaseUrl(foundryUrl + "/openai/v1/")
                            .setBearerTokenProvider(tokenProvider)
                            .setWireApi("responses")))
                .get();

            AssistantMessageEvent response = session
                    .sendAndWait(new MessageOptions().setPrompt("Hello from Managed Identity!"))
                    .get();
            System.out.println(response.getData().content());

            session.disconnect().get();
        }
    }
}
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```python
import asyncio
import os

from azure.identity.aio import DefaultAzureCredential
from copilot import CopilotClient
from copilot.session import PermissionHandler, ProviderConfig

async def main():
    credential = DefaultAzureCredential(require_envvar=True)
    async def get_bearer_token(_args) -> str:
        token = await credential.get_token("https://ai.azure.com/.default")
        return token.token

    foundry_url = os.environ["FOUNDRY_RESOURCE_URL"]

    client = CopilotClient()
    await client.start()

    session = await client.create_session(
        on_permission_request=PermissionHandler.approve_all,
        model="gpt-5.5",
        provider=ProviderConfig(
            type="openai",
            base_url=f"{foundry_url.rstrip('/')}/openai/v1/",
            bearer_token_provider=get_bearer_token,
            wire_api="responses",
        ),
    )

    response = await session.send_and_wait("Hello from Managed Identity!")
    print(response.data.content)

    await client.stop()
    await credential.close()


asyncio.run(main())
```

{% endcodetab %}
{% codetab rust %}

<!-- docs-validate: skip -->

```rust
use std::sync::Arc;

use azure_core::credentials::TokenCredential;
use azure_identity::{DeveloperToolsCredential, ManagedIdentityCredential};
use github_copilot_sdk::{BearerTokenError, Client, ClientOptions, MessageOptions, ProviderTokenArgs};
use github_copilot_sdk::types::{ProviderConfig, SessionConfig};

fn credential_for_environment() -> azure_core::Result<Arc<dyn TokenCredential>> {
    match std::env::var("AZURE_TOKEN_CREDENTIALS").as_deref() {
        Ok("ManagedIdentityCredential") => Ok(ManagedIdentityCredential::new(None)?),
        _ => Ok(DeveloperToolsCredential::new(None)?),
    }
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let credential = credential_for_environment()?;
    let foundry_url = std::env::var("FOUNDRY_RESOURCE_URL")?;

    let get_bearer_token = {
        let credential = credential.clone();
        move |_args: ProviderTokenArgs| {
            let credential = credential.clone();
            async move {
                let token = credential
                    .get_token(&["https://ai.azure.com/.default"], None)
                    .await
                    .map_err(|err| BearerTokenError::message(err.to_string()))?;
                Ok(token.token.secret().to_string())
            }
        }
    };

    let mut provider = ProviderConfig::default();
    provider.provider_type = Some("openai".to_string());
    provider.base_url = format!("{}/openai/v1/", foundry_url.trim_end_matches('/'));
    provider.bearer_token_provider = Some(Arc::new(get_bearer_token));
    provider.wire_api = Some("responses".to_string());

    let mut config = SessionConfig::default();
    config.model = Some("gpt-5.5".to_string());
    config.provider = Some(provider);

    let client = Client::start(ClientOptions::default()).await?;
    let session = client.create_session(config).await?;

    session
        .send_and_wait(MessageOptions::new("Hello from Managed Identity!"))
        .await?;

    session.disconnect().await?;
    client.stop().await?;
    Ok(())
}
```

{% endcodetab %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CopilotClient } from "@github/copilot-sdk";

const credential = new DefaultAzureCredential({
  requiredEnvVars: ["AZURE_TOKEN_CREDENTIALS"],
});
const getBearerToken = async () => {
  const tokenResponse = await credential.getToken("https://ai.azure.com/.default");
  return tokenResponse.token;
};

const client = new CopilotClient();

const session = await client.createSession({
  model: "gpt-5.5",
  provider: {
    type: "openai",
    baseUrl: `${process.env.FOUNDRY_RESOURCE_URL}/openai/v1/`,
    bearerTokenProvider: getBearerToken,
    wireApi: "responses",
  },
});

const response = await session.sendAndWait({ prompt: "Hello from Managed Identity!" });
console.log(response?.data.content);

await client.stop();
```

{% endcodetab %}
{% endcodetabs %}

## 环境配置

|变量|描述|示例|
|----------|-------------|---------|
| `AZURE_TOKEN_CREDENTIALS` |When running in **Azure**, set it to `ManagedIdentityCredential`.在本地运行 ** ** 时，将其设置为 `dev` 或开发者工具凭证名称，例如 `AzureCliCredential`。| `ManagedIdentityCredential` |
| `AZURE_CLIENT_ID` |*可选。* 在 **Azure** 中运行时，在使用 `ManagedIdentityCredential` 时将其设置为用户分配的托管标识的客户端 ID。 If not set, Azure uses the System-assigned Managed Identity.| `11111111-2222-3333-4444-555555555555` |
| `FOUNDRY_RESOURCE_URL` |Your Microsoft Foundry resource URL| `https://<my-resource>.openai.azure.com` |

不需要 API 关键环境变量 - 身份验证由 Azure Identity 凭据处理。在.NET、Go、Java、Python和TypeScript中，`DefaultAzureCredential`自动支持：

* **托管身份**（系统分配或用户分配）：适用于 Azure 托管应用程序
* **Azure CLI** (`az login`): for local development
* **Environment variables** (`AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`): for service principals
* **工作负载身份**：用于 Kubernetes

在 .NET、Go、Java、Python 和 TypeScript 中，`ManagedIdentityCredential` 读取 `AZURE_CLIENT_ID` 以选择用户分配的托管身份。 Rust 是本指南中的一个例外。

在 Rust 中，本地开发使用 `DeveloperToolsCredential`，在 Azure 中运行时使用 `ManagedIdentityCredential`。对于其他语言，请参阅 `DefaultAzureCredential` 文档以获取完整的凭证链：

* [.NET](<https://aka.ms/azsdk/net/identity/credential-chains#defaultazurecredential-overview>)
* [Go](<https://aka.ms/azsdk/go/identity/credential-chains#defaultazurecredential-overview>)
* [Java](<https://aka.ms/azsdk/java/identity/credential-chains#defaultazurecredential-overview>)
* [Python](<https://aka.ms/azsdk/python/identity/credential-chains#defaultazurecredential-overview>)
* [TypeScript](<https://aka.ms/azsdk/js/identity/credential-chains#defaultazurecredential-overview>)

## 何时使用此模式

|场景|推荐|
|----------|----------------|
|具有托管身份的 Azure 托管应用程序|✅ 使用这个模式|
|具有现有 Microsoft Entra 服务主体的应用程序|✅ 使用这个模式|
|使用 `az login` 进行本地开发|✅ 使用这个模式|
|Non-Azure environment with static API key|使用[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth/byok>)|
|GitHub Copilot 可供订阅|使用[AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/github-oauth>)|

## 另请参阅

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth/byok>)：静态API按键配置
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/backend-services>)：服务器端部署


## 原文对照

<details>
<summary>展开完整原文</summary>

<!-- markdownlint-disable GHD046 GHD005 -->
<!-- Suppressed: GHD046 (outdated release terminology), GHD005 (hardcoded data variable) -->

This guide shows how to use Azure Identity SDK APIs to authenticate with Microsoft Foundry models through the GitHub Copilot SDK. Most languages use `DefaultAzureCredential`; Rust uses `DeveloperToolsCredential` locally and `ManagedIdentityCredential` in Azure.

## How it works

Microsoft Foundry's OpenAI-compatible endpoint (`https://<resource-name>.openai.azure.com/openai/v1/`) accepts bearer tokens from Microsoft Entra ID in place of static API keys. This guide uses a token provider callback so the GitHub Copilot SDK runtime can request fresh tokens on demand.

Using Python as an example, the flow is:

1. Configure `DefaultAzureCredential` for your environment.
1. Pass a callback, in `bearer_token_provider` of the BYOK provider configuration, that uses `DefaultAzureCredential` to obtain a token for the `https://ai.azure.com/.default` scope.
1. Let the GitHub Copilot SDK request fresh tokens on demand through that callback.

![Diagram: Sequence diagram showing the described process.](<https://github.com/assets/images/help/copilot/copilot-sdk/setup-azure-managed-identity-diagram-0.png>)

## Code samples

### Prerequisites

Install the Azure Identity and GitHub Copilot SDK packages for your language:

{% codetabs %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```bash
dotnet add package GitHub.Copilot.SDK
dotnet add package Azure.Core
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```bash
go get github.com/github/copilot-sdk/go
go get github.com/Azure/azure-sdk-for-go/sdk/azidentity
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```xml
<dependency>
    <groupId>com.github</groupId>
    <artifactId>copilot-sdk-java</artifactId>
    <version>${copilot.sdk.version}</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>${azure.identity.version}</version>
</dependency>
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```bash
pip install github-copilot-sdk azure-identity
```

{% endcodetab %}
{% codetab rust %}

<!-- docs-validate: skip -->

```bash
cargo add github-copilot-sdk azure_identity azure_core
cargo add tokio --features macros,rt-multi-thread
```

{% endcodetab %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```bash
npm install @github/copilot-sdk @azure/identity
```

{% endcodetab %}
{% endcodetabs %}

### Use a token provider callback

Use this approach when you want the GitHub Copilot SDK runtime to request fresh tokens on demand through a callback that you provide. The Azure Identity SDK handles token caching and refresh timing.

Here are language-specific implementations:

{% codetabs %}
{% codetab dotnet %}

<!-- docs-validate: skip -->

```csharp
using Azure.Core;
using Azure.Identity;
using GitHub.Copilot;

DefaultAzureCredential credential = new(
    DefaultAzureCredential.DefaultEnvironmentVariableName);
await using CopilotClient client = new();
string foundryUrl = Environment.GetEnvironmentVariable("FOUNDRY_RESOURCE_URL")!;

await using CopilotSession session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.5",
    Provider = new ProviderConfig
    {
        Type = "openai",
        BaseUrl = $"{foundryUrl}/openai/v1/",
        BearerTokenProvider = async _ =>
        {
            AccessToken token = await credential.GetTokenAsync(
                new TokenRequestContext(["https://ai.azure.com/.default"]));
            return token.Token;
        },
        WireApi = "responses",
    },
});

AssistantMessageEvent? response = await session.SendAndWaitAsync(
    new MessageOptions { Prompt = "Hello from Managed Identity!" });
Console.WriteLine(response?.Data.Content);
```

{% endcodetab %}
{% codetab go %}

<!-- docs-validate: skip -->

```golang
package main

import (
	"context"
	"fmt"
	"log"
	"os"
	"time"

	"github.com/Azure/azure-sdk-for-go/sdk/azcore/policy"
	"github.com/Azure/azure-sdk-for-go/sdk/azidentity"
	copilot "github.com/github/copilot-sdk/go"
)
func main() {
	opts := azidentity.DefaultAzureCredentialOptions{RequireAzureTokenCredentials: true}
	credential, err := azidentity.NewDefaultAzureCredential(&opts)
	if err != nil {
		log.Fatal(err)
	}

	getBearerToken := func(args copilot.ProviderTokenArgs) (string, error) {
		token, err := credential.GetToken(context.Background(), policy.TokenRequestOptions{
			Scopes: []string{"https://ai.azure.com/.default"},
		})
		if err != nil {
			return "", err
		}
		return token.Token, nil
	}

	client := copilot.NewClient(nil)
	if err := client.Start(context.Background()); err != nil {
		log.Fatal(err)
	}
	defer client.Stop()

	foundryURL := os.Getenv("FOUNDRY_RESOURCE_URL")

	session, err := client.CreateSession(context.Background(), &copilot.SessionConfig{
		Model: "gpt-5.5",
		Provider: &copilot.ProviderConfig{
			Type:                "openai",
			BaseURL:             fmt.Sprintf("%s/openai/v1/", foundryURL),
			BearerTokenProvider: getBearerToken,
			WireAPI:             "responses",
		},
	})
	if err != nil {
		log.Fatal(err)
	}
	defer session.Disconnect()

	ctx, cancel := context.WithTimeout(context.Background(), 60*time.Second)
	defer cancel()

	response, err := session.SendAndWait(ctx, copilot.MessageOptions{
		Prompt: "Hello from Managed Identity!",
	})
	if err != nil {
		log.Fatal(err)
	}

	if response != nil {
		if data, ok := response.Data.(*copilot.AssistantMessageData); ok {
			fmt.Println(data.Content)
		}
	}
}
```

{% endcodetab %}
{% codetab java %}

<!-- docs-validate: skip -->

```java
import com.azure.core.credential.TokenRequestContext;
import com.azure.identity.AzureIdentityEnvVars;
import com.azure.identity.DefaultAzureCredentialBuilder;
import com.github.copilot.CopilotClient;
import com.github.copilot.generated.AssistantMessageEvent;
import com.github.copilot.rpc.BearerTokenProvider;
import com.github.copilot.rpc.MessageOptions;
import com.github.copilot.rpc.ProviderConfig;
import com.github.copilot.rpc.SessionConfig;

public class ManagedIdentityExample {
    public static void main(String[] args) throws Exception {
        var credential = new DefaultAzureCredentialBuilder()
                .requireEnvVars(AzureIdentityEnvVars.AZURE_TOKEN_CREDENTIALS)
                .build();
        BearerTokenProvider tokenProvider = providerArgs ->
            credential
                .getToken(new TokenRequestContext().addScopes("https://ai.azure.com/.default"))
                .map(accessToken -> accessToken.getToken())
                .toFuture();
        String foundryUrl = System.getenv("FOUNDRY_RESOURCE_URL");

        try (var client = new CopilotClient()) {
            client.start().get();

            var session = client.createSession(new SessionConfig()
                    .setModel("gpt-5.5")
                    .setProvider(new ProviderConfig()
                            .setType("openai")
                            .setBaseUrl(foundryUrl + "/openai/v1/")
                            .setBearerTokenProvider(tokenProvider)
                            .setWireApi("responses")))
                .get();

            AssistantMessageEvent response = session
                    .sendAndWait(new MessageOptions().setPrompt("Hello from Managed Identity!"))
                    .get();
            System.out.println(response.getData().content());

            session.disconnect().get();
        }
    }
}
```

{% endcodetab %}
{% codetab python %}

<!-- docs-validate: skip -->

```python
import asyncio
import os

from azure.identity.aio import DefaultAzureCredential
from copilot import CopilotClient
from copilot.session import PermissionHandler, ProviderConfig

async def main():
    credential = DefaultAzureCredential(require_envvar=True)
    async def get_bearer_token(_args) -> str:
        token = await credential.get_token("https://ai.azure.com/.default")
        return token.token

    foundry_url = os.environ["FOUNDRY_RESOURCE_URL"]

    client = CopilotClient()
    await client.start()

    session = await client.create_session(
        on_permission_request=PermissionHandler.approve_all,
        model="gpt-5.5",
        provider=ProviderConfig(
            type="openai",
            base_url=f"{foundry_url.rstrip('/')}/openai/v1/",
            bearer_token_provider=get_bearer_token,
            wire_api="responses",
        ),
    )

    response = await session.send_and_wait("Hello from Managed Identity!")
    print(response.data.content)

    await client.stop()
    await credential.close()


asyncio.run(main())
```

{% endcodetab %}
{% codetab rust %}

<!-- docs-validate: skip -->

```rust
use std::sync::Arc;

use azure_core::credentials::TokenCredential;
use azure_identity::{DeveloperToolsCredential, ManagedIdentityCredential};
use github_copilot_sdk::{BearerTokenError, Client, ClientOptions, MessageOptions, ProviderTokenArgs};
use github_copilot_sdk::types::{ProviderConfig, SessionConfig};

fn credential_for_environment() -> azure_core::Result<Arc<dyn TokenCredential>> {
    match std::env::var("AZURE_TOKEN_CREDENTIALS").as_deref() {
        Ok("ManagedIdentityCredential") => Ok(ManagedIdentityCredential::new(None)?),
        _ => Ok(DeveloperToolsCredential::new(None)?),
    }
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let credential = credential_for_environment()?;
    let foundry_url = std::env::var("FOUNDRY_RESOURCE_URL")?;

    let get_bearer_token = {
        let credential = credential.clone();
        move |_args: ProviderTokenArgs| {
            let credential = credential.clone();
            async move {
                let token = credential
                    .get_token(&["https://ai.azure.com/.default"], None)
                    .await
                    .map_err(|err| BearerTokenError::message(err.to_string()))?;
                Ok(token.token.secret().to_string())
            }
        }
    };

    let mut provider = ProviderConfig::default();
    provider.provider_type = Some("openai".to_string());
    provider.base_url = format!("{}/openai/v1/", foundry_url.trim_end_matches('/'));
    provider.bearer_token_provider = Some(Arc::new(get_bearer_token));
    provider.wire_api = Some("responses".to_string());

    let mut config = SessionConfig::default();
    config.model = Some("gpt-5.5".to_string());
    config.provider = Some(provider);

    let client = Client::start(ClientOptions::default()).await?;
    let session = client.create_session(config).await?;

    session
        .send_and_wait(MessageOptions::new("Hello from Managed Identity!"))
        .await?;

    session.disconnect().await?;
    client.stop().await?;
    Ok(())
}
```

{% endcodetab %}
{% codetab typescript %}

<!-- docs-validate: skip -->

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CopilotClient } from "@github/copilot-sdk";

const credential = new DefaultAzureCredential({
  requiredEnvVars: ["AZURE_TOKEN_CREDENTIALS"],
});
const getBearerToken = async () => {
  const tokenResponse = await credential.getToken("https://ai.azure.com/.default");
  return tokenResponse.token;
};

const client = new CopilotClient();

const session = await client.createSession({
  model: "gpt-5.5",
  provider: {
    type: "openai",
    baseUrl: `${process.env.FOUNDRY_RESOURCE_URL}/openai/v1/`,
    bearerTokenProvider: getBearerToken,
    wireApi: "responses",
  },
});

const response = await session.sendAndWait({ prompt: "Hello from Managed Identity!" });
console.log(response?.data.content);

await client.stop();
```

{% endcodetab %}
{% endcodetabs %}

## Environment configuration

| Variable | Description | Example |
|----------|-------------|---------|
| `AZURE_TOKEN_CREDENTIALS` | When running in **Azure**, set it to `ManagedIdentityCredential`. When running **locally**, set it to either `dev` or a developer tool credential name, such as `AzureCliCredential`. | `ManagedIdentityCredential` |
| `AZURE_CLIENT_ID` | *Optional.* When running in **Azure**, set this to the client ID of a User-assigned Managed Identity when using `ManagedIdentityCredential`. If not set, Azure uses the System-assigned Managed Identity. | `11111111-2222-3333-4444-555555555555` |
| `FOUNDRY_RESOURCE_URL` | Your Microsoft Foundry resource URL | `https://<my-resource>.openai.azure.com` |

No API key environment variable is needed—authentication is handled by Azure Identity credentials. In .NET, Go, Java, Python, and TypeScript, `DefaultAzureCredential` automatically supports:

* **Managed Identity** (System-assigned or User-assigned): for Azure-hosted apps
* **Azure CLI** (`az login`): for local development
* **Environment variables** (`AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`): for service principals
* **Workload Identity**: for Kubernetes

In .NET, Go, Java, Python, and TypeScript, `ManagedIdentityCredential` reads `AZURE_CLIENT_ID` to select a User-assigned Managed Identity. Rust is an exception in this guide.

In Rust, use `DeveloperToolsCredential` for local development and `ManagedIdentityCredential` when running in Azure. For other languages, see the `DefaultAzureCredential` documentation for the full credential chain:

* [.NET](<https://aka.ms/azsdk/net/identity/credential-chains#defaultazurecredential-overview>)
* [Go](<https://aka.ms/azsdk/go/identity/credential-chains#defaultazurecredential-overview>)
* [Java](<https://aka.ms/azsdk/java/identity/credential-chains#defaultazurecredential-overview>)
* [Python](<https://aka.ms/azsdk/python/identity/credential-chains#defaultazurecredential-overview>)
* [TypeScript](<https://aka.ms/azsdk/js/identity/credential-chains#defaultazurecredential-overview>)

## When to use this pattern

| Scenario | Recommendation |
|----------|----------------|
| Azure-hosted app with Managed Identity | ✅ Use this pattern |
| App with existing Microsoft Entra service principal | ✅ Use this pattern |
| Local development with `az login` | ✅ Use this pattern |
| Non-Azure environment with static API key | Use [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth/byok>) |
| GitHub Copilot subscription available | Use [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/github-oauth>) |

## See also

* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/auth/byok>): Static API key configuration
* [AUTOTITLE](<https://github.com/copilot/how-tos/copilot-sdk/setup/backend-services>): Server-side deployment


</details>
