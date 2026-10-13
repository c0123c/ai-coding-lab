# 应用管理 OCI GenAI 沙箱样例

介绍访问配置、沙箱启动、执行服务、生成计划和停止删除资源；属于特定云环境接入示例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/oci/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/oci/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理 OCI GenAI 沙箱

您的应用程序创建一个 Agents API 会话和一个 OCI GenAI 沙箱，启动 
`codex exec-server`，并要求 agent 将 `brief.txt` 转换为`plan.md`.
打印计划，停止并删除沙箱，并删除会话。

## 开始之前

OCI GenAI 沙盒处于测试阶段。请联系您的 Oracle 客户经理以请求 
 访问您的帐户。

从 Oracle 获取测试版 Python SDK。创建一个启用沙箱的生成 AI
 项目并保存其 OCID。

授予您的 OCI 组权限来管理所选 
 分区中的项目和沙箱：

```text
allow group <group-name> to manage generative-ai-sandbox in compartment <compartment-name>
allow group <group-name> to manage generative-ai-project in compartment <compartment-name>
```

使用具有 Node.js 和 npm 的运行时。允许出站 HTTPS 到 `registry.npmjs.org`
 和 `api.openai.com`，以及 WebSocket 连接到 
`codex-cloud-environments.chatgpt.com`。允许您的任务需要的任何其他主机。

## 配置访问权限

使用 OCI CLI 验证 OCI 安全令牌配置文件：

```bash
uv tool run --from oci-cli oci session authenticate --profile-name Sandbox --region us-chicago-1
```

设置 `OPENAI_API_KEY` 和单独的受限 `OPENAI_EXECUTOR_API_KEY`。
密钥必须具有相同的所有者、组织和项目。只有执行者
密钥以`CODEX_API_KEY`身份进入沙箱。请参阅
[执行者身份验证](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>)。

设置您的项目 OCID 以及 Oracle 中测试版 SDK 轮的路径：

```bash
export OCI_SANDBOX_PROJECT_ID="ocid1.generativeaiproject..."
export OCI_SANDBOX_SDK_WHEEL="/path/to/oci-<beta-version>-py3-none-any.whl"
```

该脚本使用 `Sandbox` 配置文件和 `us-chicago-1` 区域。可选设置：

|变量|默认|
| --- | --- |
| `OCI_SANDBOX_PROFILE` | `Sandbox` |
| `OCI_SANDBOX_REGION` | `us-chicago-1` |
| `OCI_SANDBOX_ENDPOINT` | `https://inference.generativeai.<region>.oci.oraclecloud.com` |
| `OCI_SANDBOX_RUNTIME` | `python-3.11` |
| `OCI_SANDBOX_SHAPE` | `SMALL` |
| `OCI_SANDBOX_EXPIRATION` | `PT30M` |

使用您的测试版入门 
 说明确认运行时、形状、区域和端点。运行时必须包含 npm 或支持安装它。

## 运行

从 Cookbook 存储库根目录中，使用 [uv](<https://docs.astral.sh/uv/>) 运行：

```bash
uv run --with "$OCI_SANDBOX_SDK_WHEEL" examples/agents_api/sandboxes/oci/application_managed/main.py
```

该脚本声明内联 Agents API 依赖项，并通过 `--with` 加载 Oracle 的 beta SDK
。测试版SDK必须提供`oci.generative_ai_sandbox`；使用 Oracle 为您的租赁提供的 
 包。

该示例等待沙箱启动最多两分钟，
agent 回合最多等待六分钟。它请求 30 分钟的沙箱过期，并尝试清理 `finally` 中的 
 两个资源。如果清理失败，请使用打印的 ID 删除 
 剩余资源。

保持会话和沙箱处于活动状态以进行后续回合。请勿将 
 配置 Webhook 处理程序附加到此应用程序管理的会话。

## 参考文献

- [OCI 生成 AI 文档 ](<https://docs.oracle.com/en-us/iaas/Content/generative-ai/>)
- [OCI Python SDK](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/pythonsdk.htm>)
- [OCI TypeScript SDK](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/typescriptsdk.htm>)
- [OCI CLI 认证](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/clitoken.htm>)

SDK 链接通常覆盖 OCI。本示例使用 Oracle 的测试版 Python SDK。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed OCI GenAI Sandbox

Your application creates an Agents API session and an OCI GenAI Sandbox, starts
`codex exec-server`, and asks the agent to turn `brief.txt` into `plan.md`.
It prints the plan, stops and deletes the sandbox, and deletes the session.

## Before you begin

OCI GenAI Sandboxes are in beta. Contact your Oracle account manager to request
access for your account.

Get the beta Python SDK from Oracle. Create a sandbox-enabled Generative AI
Project and save its OCID.

Grant your OCI group permission to manage projects and sandboxes in the chosen
compartment:

```text
allow group <group-name> to manage generative-ai-sandbox in compartment <compartment-name>
allow group <group-name> to manage generative-ai-project in compartment <compartment-name>
```

Use a runtime with Node.js and npm. Allow outbound HTTPS to `registry.npmjs.org`
and `api.openai.com`, plus WebSocket connections to
`codex-cloud-environments.chatgpt.com`. Allow any additional hosts your task needs.

## Configure access

Authenticate an OCI security-token profile with the OCI CLI:

```bash
uv tool run --from oci-cli oci session authenticate --profile-name Sandbox --region us-chicago-1
```

Set `OPENAI_API_KEY` and a separate restricted `OPENAI_EXECUTOR_API_KEY`.
The keys must have the same owner, organization, and project. Only the executor
key enters the sandbox as `CODEX_API_KEY`. See
[executor authentication](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>).

Set your project OCID and the path to the beta SDK wheel from Oracle:

```bash
export OCI_SANDBOX_PROJECT_ID="ocid1.generativeaiproject..."
export OCI_SANDBOX_SDK_WHEEL="/path/to/oci-<beta-version>-py3-none-any.whl"
```

The script uses the `Sandbox` profile and `us-chicago-1` region. Optional settings:

| Variable | Default |
| --- | --- |
| `OCI_SANDBOX_PROFILE` | `Sandbox` |
| `OCI_SANDBOX_REGION` | `us-chicago-1` |
| `OCI_SANDBOX_ENDPOINT` | `https://inference.generativeai.<region>.oci.oraclecloud.com` |
| `OCI_SANDBOX_RUNTIME` | `python-3.11` |
| `OCI_SANDBOX_SHAPE` | `SMALL` |
| `OCI_SANDBOX_EXPIRATION` | `PT30M` |

Confirm the runtime, shape, region, and endpoint with your beta onboarding
instructions. The runtime must include npm or support installing it.

## Run

From the Cookbook repository root, run with [uv](<https://docs.astral.sh/uv/>):

```bash
uv run --with "$OCI_SANDBOX_SDK_WHEEL" examples/agents_api/sandboxes/oci/application_managed/main.py
```

The script declares the Agents API dependency inline and loads Oracle's beta SDK
through `--with`. The beta SDK must provide `oci.generative_ai_sandbox`; use the
package Oracle supplies for your tenancy.

The example waits up to two minutes for sandbox startup and six minutes for the
agent turn. It requests a 30-minute sandbox expiration and attempts cleanup of
both resources in `finally`. If cleanup fails, use the printed IDs to remove the
remaining resources.

Keep the session and sandbox alive for follow-up turns. Do not attach a
provisioning webhook handler to sessions this application manages.

## References

- [OCI Generative AI documentation](<https://docs.oracle.com/en-us/iaas/Content/generative-ai/>)
- [OCI Python SDK](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/pythonsdk.htm>)
- [OCI TypeScript SDK](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/typescriptsdk.htm>)
- [OCI CLI authentication](<https://docs.oracle.com/en-us/iaas/Content/API/SDKDocs/clitoken.htm>)

The SDK links cover OCI generally. Use Oracle's beta Python SDK for this example.


</details>
