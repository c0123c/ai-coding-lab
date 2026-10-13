# 用 AWS Lambda MicroVM 运行 Agent 工具

比较应用与 Webhook 管理，说明镜像、启动、暂停恢复、文件保留、监控及排查，供自托管执行环境参考。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/openai/openai-cookbook/commit/86ff2cc0c4b2417feb36bc76ff0f24c5c4e785be>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/aws/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/aws/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# AWS 拉姆达 MicroVMs

使用 [AWS Lambda MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-getting-started.html>) 在您的 AWS 帐户中运行 agent 的工具。 OpenAI 运行 agent 安全带；每个 MicroVM 运行 `codex exec-server` 并保存会话的工作区文件。

构建映像，然后运行 [应用程序管理的](<应用管理 AWS Lambda MicroVM 样例.md>) 或 [webhook 管理的](<Webhook 管理 AWS Lambda MicroVM 样例.md>) 示例。两者都使用相同的图像和凭据。

每个示例都要求agent写入`hello.txt`，然后下载并检查它。使用 `--suspend-resume` 验证文件在轮次之间是否存在。

## 它是如何运作的

在 Webhook 路径中，您的应用程序创建自托管会话并发送输入。当会话需要执行程序时，OpenAI 会发送带有 `environment_connection` 操作的 `agent.session.action_required`。 [API 网关 ](<https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html>) 将 Webhook 传送到启动器 [Lambda](<https://docs.aws.amazon.com/lambda/latest/dg/welcome.html>)，后者验证签名、检查当前会话并启动 MicroVM。

MicroVM 的 `/run` 挂钩从 [Secrets Manager](<https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html>) 获取环境密钥并启动执行器。执行器将出站连接到OpenAI，然后等待输入继续。您的应用程序遵循会话流，下载输出文件，并在最后一轮后终止 MicroVM。

![应用程序将输入发送到Agents API。需要连接的 Webhook 通过 API 网关和启动器 Lambda 流向 AWS Lambda MicroVM。启动器使用可重复使用的图像； VM 从 Secrets Manager 读取其环境密钥并将出站连接到 OpenAI。应用程序检索输出文件。](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/images/aws-microvm-sandboxes.webp>)

## 开始之前

- **AWS 访问权限：** 具有 Lambda MicroVMs 访问权限的账户和包含 `lambda-microvms` 的 AWS CLI。构建脚本创建 S3 工件存储桶、IAM 映像构建角色和 CloudWatch 日志组。您的 AWS 凭证需要权限才能创建这些资源并构建和运行 MicroVMs。有关背景信息，请参阅AWS的[入门指南](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-getting-started.html>)。
- **应用程序密钥：**使用`OPENAI_API_KEY`创建会话并提交输入。
- **环境密钥 (`OPENAI_EXECUTOR_API_KEY`):** 创建一个单独的 [环境密钥 ](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>) 并具有匹配的组织、项目和用户或服务帐户所有权。将其值存储在 Secrets Manager 中，例如存储在名为 `codex/agents-api/executor` 的密钥中。 `/run` 钩子将其传递给 Codex 作为 `CODEX_API_KEY`。
- **MicroVM 执行角色：** 允许此角色仅读取环境密钥机密。如果您使用客户管理的 KMS 密钥，请添加解密权限。请参阅 AWS [安全和权限 ](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-security.html>) 了解角色设置。

将应用程序密钥和 Webhook 签名保密在 MicroVM 之外。 Webhook 启动器需要在其自己的 Secrets Manager 密钥中使用这些凭据； VM 仅接收其环境密钥秘密的 ARN。

## 准备可重复使用的图像

使用 [Codex CLI](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#prepare-your-environment>)、工具依赖项、工作目录（例如 `/workspace`）以及用于 AWS 生命周期挂钩的 HTTP 服务器构建映像。

|钩子|行为|
| --------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `/aws/lambda-microvms/runtime/v1/ready` |当服务器准备好 AWS 进行快照时，返回 HTTP 200。让执行器保持断开状态。|
| `/aws/lambda-microvms/runtime/v1/run`   |读取会话连接值和密钥 ARN，获取环境密钥，然后启动执行器。|
| `/aws/lambda-microvms/runtime/v1/validate` |检查 Codex 是否运行并且工作区可写。|
| `/aws/lambda-microvms/runtime/v1/suspend` |在 AWS 快照 VM 之前停止执行器并刷新写入。|
| `/aws/lambda-microvms/runtime/v1/resume` |再次获取密钥并为相同环境重新启动执行器。|
| `/aws/lambda-microvms/runtime/v1/terminate` |在终止之前停止执行器并刷新写入。|

将 `Dockerfile` 和挂钩服务器打包在 S3 工件中，然后使用 8 GB 内存（4 vCPU 基线）构建 `codex-executor`。构建脚本在端口 8080 上启用这些挂钩。将会话 ID、凭据和实时执行程序连接排除在可重用映像快照之外。有关构建和挂钩配置，请参阅 AWS [MicroVM 图像 ](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-images.html>)。

启动时，您的应用程序或启动器将这些值序列化为 `runHookPayload` 中的 JSON：

|价值|目的|
| -------------------------------- | ------------------------------------------------------------------ |
| `session.environment.id`         |标识执行器连接到的环境。|
| `session.environment.remote_url` |提供执行器的OpenAI连接URL。|
|环境密钥秘密 ARN|让挂钩使用 MicroVM 的执行角色检索密钥。|

`/run`钩子解析有效负载，检索密钥，设置`CODEX_API_KEY`，并且[启动执行器](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#start-the-executor>)。一旦进程开始就返回以避免钩子超时。

允许执行者所需的 [ 出站连接 ](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#network-access>) 以及对 Secrets Manager 的访问。为专用资源或网络限制配置 AWS 出口连接器。

使用`PUT /upload/<path>`写入`/workspace`下的文件，使用`GET /download/<path>`读取该文件。上传接受最多 64 MiB 的原始字节。 `upload_file` 和 `download_file` 帮助程序获取端口 8080 的 AWS 身份验证令牌。工作区之外的路径将被拒绝。

## 启动MicroVM

保持会话事件流打开，直到回合结束。

### 应用程序管理的配置

1. [创建一个工作目录与图像匹配的自托管会话](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#create-or-reuse-a-session>)，然后[打开其事件流](<https://developers.openai.com/api/docs/guides/agents-api/sessions/events#consume-a-stream>)。
2. 使用映像 ARN 和版本、执行角色、网络连接器和 `runHookPayload` 调用 AWS [RunMicrovm](<https://docs.aws.amazon.com/lambda/latest/microvm-api/API_RunMicrovm.html>)。将返回的 `microvmId` 与会话 ID 一起保存。
3. [发送输入](<https://developers.openai.com/api/docs/guides/agents-api/sessions#send-input>)并关注回合结果。
4. 检索输出文件并[停止MicroVM](<#停止microvm>)。

### Webhook 管理的配置

您的应用程序创建会话、打开其流并提交输入。 OpenAI webhook 通过 API 网关到达启动器 Lambda。

1. 部署调用启动器的 `POST /webhook` 路由。授予启动器读取其凭据、检查、启动、恢复和终止所选映像中的 MicroVMs 的权限，传递 MicroVM 执行角色，并使用配置的网络连接器。
2. [为`agent.session.action_required`和`agent.session.failed`注册端点](<https://developers.openai.com/api/docs/guides/agents-api/sessions/webhooks#set-up-a-webhook>)。使用启动器的应用程序密钥存储端点的签名密钥。
3. 在访问会话或启动计算之前，根据原始请求正文验证 Webhook 签名。检索当前会话并确认处理程序拥有它，例如通过匹配专用保存的 agent。
4. 如果 `environment_connection` 仍待处理，则恢复已暂停的会话记录的 VM。否则，如果没有记录，则启动VM并保存其ID；如果保存失败，则终止保存。如果执行器在[连接超时](<https://developers.openai.com/api/docs/guides/agents-api/sessions/webhooks#environment-connection-events>)之前连接，则等待输入将继续进行而无需重新提交。
5. 如果会话仍然是`failed`，则终止其记录的MicroVM。忽略已删除的会话、已解决的操作以及另一个处理程序拥有的事件。

该示例将 VM ID 保存在会话元数据中。在记录 ID 之前的并发 Webhook 传送仍然可以启动额外的虚拟机。

## 停止MicroVM

在会话流上，等待主 agent 的 `agent.session.turn.completed`、`agent.session.turn.failed` 或 `agent.session.turn.cancelled`（`event.turn.subagent_id` 是 `null`）。子代理共享MicroVM；它们的终止事件不得触发清理。检索所需文件，调用`TerminateMicrovm`，并验证VM是否达到`TERMINATED`。也对应用程序错误运行清理。

转向结果是流事件，而不是 Webhook 订阅。 `agent.session.failed` webhook 处理会话失败，但不涵盖每个失败的回合。不要单独在 `agent.session.idle` 上终止：它可以在等待输入开始之前到达。

设置 AWS 限制以防清理失败：

|设置|指导|
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| `maximumDurationInSeconds`                       |设置工作负载上限，例如 `900` 表示 15 分钟的测试。它可能会中断正在进行的工作。|
| `maxIdleDurationSeconds`                         |覆盖预期的工作量。 AWS 测量入站流量；执行器的出站连接不会重置此计时器。|
| `suspendedDurationSeconds` / `autoResumeEnabled` |这些示例默认使用 `0` / `false` 以及 `300` / `false` 和 `--suspend-resume`。|

请参阅 AWS [运行和使用 MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html>) 了解寿命和空闲控制。

[单独删除会话](<https://developers.openai.com/api/docs/guides/agents-api/sessions/manage#delete-a-session>)；这不会停止 VM 或发送删除 Webhook。对镜像和环境密钥保密以供重复使用。请参阅[沙盒生命周期](<https://developers.openai.com/api/docs/guides/agents-api/environments/lifecycle>)以处理后续回合。

## 回合之间暂停和恢复

使用 AWS [ 挂起和恢复 ](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html#microvms-launching-suspend-resume>) 在轮次之间保留内存和磁盘。设置非零`suspendedDurationSeconds`；暂停期间会收取快照存储费用。 `maximumDurationInSeconds` 将总运行和暂停时间限制为八小时。

对于`--suspend-resume`，应用程序在第一轮后暂停VM。应用程序管理的示例直接恢复它； Webhook 管理的示例发送另一个输入，以便 Webhook 恢复它。下一回合读取现有的 `hello.txt`，并且应用程序检查其内容是否仍然存在。

挂钩在暂停之前停止 Codex，并在恢复时重新加载其密钥。仅在工具和子代理完成后才挂起。自动恢复需要入站VM流量； Agents API 单独输入无法唤醒它。

## 验证和监控

运行任一示例并检查下载的 `hello.txt`、VM 状态 (`TERMINATED`) 和会话删除 (HTTP 404)。

将 `MICROVM_IMAGE_ARN` 设置为映像 ARN 并使用部署的 AWS 区域：

```bash
aws logs tail /aws/lambda/codex-agents-api-webhook --since 10m

aws lambda-microvms list-microvms \
  --image-identifier "$MICROVM_IMAGE_ARN" \
  --query 'items[].{id:microvmId,state:state}' --output table
```

将会话和 MicroVM ID 一起记录以跟踪运行。将凭证和原始 Webhook 主体保留在日志之外。

## 故障排除

|症状|要检查什么|
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
|Webhook 签名被拒绝|使用端点的签名密钥并验证未修改的请求正文。|
|没有 MicroVM 启动|检查 Webhook 订阅、会话所有权筛选器、待处理的 `environment_connection` 操作以及启动器的 IAM 权限。|
|镜像构建失败|检查 S3 工件、映像构建角色和 `/ready` 挂钩响应。|
|`/run` 失败或超时|检查秘密访问和执行程序启动。开始过程后返回，而不是转弯后返回。|
|执行者无法连接|检查环境ID、远程URL、密钥所有权和出站网络访问。|
|VM 转弯时停止|检查最长寿命和空闲策略；出站执行程序流量不计入入站活动。|

对于连接失败，请检查 `agent.session.environment.failed` 和执行程序日志。有关共享执行器合约，请参阅[自托管沙箱](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted>)。

## 参考文献

- [AWS Agents API](<https://developers.openai.com/api/docs/guides/agents-api/environments/providers/aws>) 的 Lambda MicroVMs 指南
- [运行和使用MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html>)
- [安全和权限](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-security.html>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# AWS Lambda MicroVMs

Use [AWS Lambda MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-getting-started.html>) to run your agent's tools in your AWS account. OpenAI runs the agent harness; each MicroVM runs `codex exec-server` and holds the session's workspace files.

Build an image, then run the [application-managed](<应用管理 AWS Lambda MicroVM 样例.md>) or [webhook-managed](<Webhook 管理 AWS Lambda MicroVM 样例.md>) example. Both use the same image and credentials.

Each example asks the agent to write `hello.txt`, then downloads and checks it. Use `--suspend-resume` to verify the file survives between turns.

## How it works

In the webhook path, your application creates a self-hosted session and sends input. When the session needs an executor, OpenAI sends `agent.session.action_required` with an `environment_connection` action. [API Gateway](<https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html>) delivers the webhook to a launcher [Lambda](<https://docs.aws.amazon.com/lambda/latest/dg/welcome.html>), which verifies the signature, checks the current session, and launches a MicroVM.

The MicroVM's `/run` hook fetches an environment key from [Secrets Manager](<https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html>) and starts the executor. The executor connects outbound to OpenAI, and the waiting input proceeds. Your application follows the session stream, downloads output files, and terminates the MicroVM after the final turn.

![The application sends input to Agents API. A connection-required webhook flows through API Gateway and a launcher Lambda to an AWS Lambda MicroVM. The launcher uses a reusable image; the VM reads its environment key from Secrets Manager and connects outbound to OpenAI. The application retrieves output files.](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/images/aws-microvm-sandboxes.webp>)

## Before you begin

- **AWS access:** An account with Lambda MicroVMs access and an AWS CLI that includes `lambda-microvms`. The build script creates the S3 artifact bucket, IAM image build role, and CloudWatch log group. Your AWS credentials need permission to create these resources and build and run MicroVMs. See AWS's [getting started guide](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-getting-started.html>) for background.
- **Application key:** Use `OPENAI_API_KEY` to create sessions and submit input.
- **Environment key (`OPENAI_EXECUTOR_API_KEY`):** Create a separate [environment key](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#authentication>) with matching organization, project, and user or service-account ownership. Store its value in Secrets Manager, for example in a secret named `codex/agents-api/executor`. The `/run` hook passes it to Codex as `CODEX_API_KEY`.
- **MicroVM execution role:** Allow this role to read only the environment-key secret. Add decryption permission if you use a customer-managed KMS key. See AWS [security and permissions](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-security.html>) for role setup.

Keep the application key and webhook signing secret outside the MicroVM. A webhook launcher needs these credentials in its own Secrets Manager secret; the VM receives only the ARN of its environment-key secret.

## Prepare a reusable image

Build an image with the [Codex CLI](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#prepare-your-environment>), tool dependencies, a working directory such as `/workspace`, and an HTTP server for the AWS lifecycle hooks.

| Hook                                    | Behavior                                                                                              |
| --------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `/aws/lambda-microvms/runtime/v1/ready` | Return HTTP 200 when the server is ready for AWS to snapshot. Leave the executor disconnected.        |
| `/aws/lambda-microvms/runtime/v1/run`   | Read the session connection values and secret ARN, fetch the environment key, and start the executor. |
| `/aws/lambda-microvms/runtime/v1/validate` | Check that Codex runs and the workspace is writable. |
| `/aws/lambda-microvms/runtime/v1/suspend` | Stop the executor and flush writes before AWS snapshots the VM. |
| `/aws/lambda-microvms/runtime/v1/resume` | Fetch the key again and restart the executor for the same environment. |
| `/aws/lambda-microvms/runtime/v1/terminate` | Stop the executor and flush writes before termination. |

Package the `Dockerfile` and hook server in an S3 artifact, then build `codex-executor` with 8 GB memory (4 vCPU baseline). The build script enables these hooks on port 8080. Keep session IDs, credentials, and live executor connections out of the reusable image snapshot. See AWS [MicroVM images](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-images.html>) for build and hook configuration.

At launch, your application or launcher serializes these values as JSON in `runHookPayload`:

| Value                            | Purpose                                                            |
| -------------------------------- | ------------------------------------------------------------------ |
| `session.environment.id`         | Identifies the environment the executor connects to.               |
| `session.environment.remote_url` | Provides the executor's OpenAI connection URL.                     |
| Environment-key secret ARN       | Lets the hook retrieve the key using the MicroVM's execution role. |

The `/run` hook parses the payload, retrieves the key, sets `CODEX_API_KEY`, and [starts the executor](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#start-the-executor>). Return once the process starts to avoid a hook timeout.

Allow the executor's required [outbound connections](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#network-access>) and access to Secrets Manager. Configure an AWS egress connector for private resources or network restrictions.

Use `PUT /upload/<path>` to write a file under `/workspace` and `GET /download/<path>` to read it. Uploads accept raw bytes up to 64 MiB. The `upload_file` and `download_file` helpers obtain an AWS authentication token for port 8080. Paths outside the workspace are rejected.

## Launch the MicroVM

Keep the session event stream open until the turn finishes.

### Application-managed provisioning

1. [Create a self-hosted session](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#create-or-reuse-a-session>) whose working directory matches the image, then [open its event stream](<https://developers.openai.com/api/docs/guides/agents-api/sessions/events#consume-a-stream>).
2. Call AWS [RunMicrovm](<https://docs.aws.amazon.com/lambda/latest/microvm-api/API_RunMicrovm.html>) with the image ARN and version, execution role, network connectors, and `runHookPayload`. Save the returned `microvmId` alongside the session ID.
3. [Send input](<https://developers.openai.com/api/docs/guides/agents-api/sessions#send-input>) and follow the turn's result.
4. Retrieve output files and [stop the MicroVM](<#stop-the-microvm>).

### Webhook-managed provisioning

Your application creates the session, opens its stream, and submits input. An OpenAI webhook reaches the launcher Lambda through API Gateway.

1. Deploy a `POST /webhook` route that invokes the launcher. Give the launcher permission to read its credentials, inspect, launch, resume, and terminate MicroVMs from the selected image, pass the MicroVM execution role, and use the configured network connectors.
2. [Register the endpoint](<https://developers.openai.com/api/docs/guides/agents-api/sessions/webhooks#set-up-a-webhook>) for `agent.session.action_required` and `agent.session.failed`. Store the endpoint's signing secret with the launcher's application key.
3. Verify the webhook signature against the raw request body before accessing sessions or launching compute. Retrieve the current session and confirm the handler owns it, for example by matching a dedicated saved agent.
4. If `environment_connection` is still pending, resume the session's recorded VM if suspended. Otherwise, launch a VM if none is recorded and save its ID; if saving fails, terminate it. If the executor connects before the [connection timeout](<https://developers.openai.com/api/docs/guides/agents-api/sessions/webhooks#environment-connection-events>), the waiting input proceeds without resubmission.
5. If the session is still `failed`, terminate its recorded MicroVM. Ignore deleted sessions, resolved actions, and events owned by another handler.

The example saves the VM ID in session metadata. Concurrent webhook deliveries before that ID is recorded can still launch extra VMs.

## Stop the MicroVM

On the session stream, wait for `agent.session.turn.completed`, `agent.session.turn.failed`, or `agent.session.turn.cancelled` for the main agent (`event.turn.subagent_id` is `null`). Subagents share the MicroVM; their terminal events must not trigger cleanup. Retrieve needed files, call `TerminateMicrovm`, and verify the VM reaches `TERMINATED`. Run cleanup on application errors too.

Turn outcomes are stream events, not webhook subscriptions. The `agent.session.failed` webhook handles session failures, but doesn't cover every failed turn. Don't terminate on `agent.session.idle` alone: it can arrive before waiting input starts.

Set AWS limits in case cleanup fails:

| Setting                                          | Guidance                                                                                                                |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| `maximumDurationInSeconds`                       | Set a ceiling for the workload, such as `900` for a 15-minute test. It can interrupt active work.                       |
| `maxIdleDurationSeconds`                         | Cover the expected workload. AWS measures inbound traffic; the executor's outbound connection doesn't reset this timer. |
| `suspendedDurationSeconds` / `autoResumeEnabled` | The examples use `0` / `false` by default and `300` / `false` with `--suspend-resume`. |

See AWS [Running and using MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html>) for lifetime and idle controls.

[Delete the session](<https://developers.openai.com/api/docs/guides/agents-api/sessions/manage#delete-a-session>) separately; this doesn't stop the VM or send a deletion webhook. Keep the image and environment-key secret for reuse. See [Sandbox lifecycle](<https://developers.openai.com/api/docs/guides/agents-api/environments/lifecycle>) for handling follow-up turns.

## Suspend and resume between turns

Use AWS [suspend and resume](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html#microvms-launching-suspend-resume>) to preserve memory and disk between turns. Set a nonzero `suspendedDurationSeconds`; snapshot storage charges apply while suspended. `maximumDurationInSeconds` limits total running and suspended time to eight hours.

With `--suspend-resume`, the application suspends the VM after the first turn. The application-managed example resumes it directly; the webhook-managed example sends another input so the webhook resumes it. The next turn reads the existing `hello.txt`, and the application checks that its contents survived.

The hooks stop Codex before suspension and reload its key on resume. Suspend only after tools and subagents finish. Automatic resume requires inbound VM traffic; Agents API input alone doesn't wake it.

## Verify and monitor

Run either example and check the downloaded `hello.txt`, VM state (`TERMINATED`), and session deletion (HTTP 404).

Set `MICROVM_IMAGE_ARN` to your image ARN and use your deployment's AWS region:

```bash
aws logs tail /aws/lambda/codex-agents-api-webhook --since 10m

aws lambda-microvms list-microvms \
  --image-identifier "$MICROVM_IMAGE_ARN" \
  --query 'items[].{id:microvmId,state:state}' --output table
```

Log session and MicroVM IDs together to trace a run. Keep credentials and raw webhook bodies out of logs.

## Troubleshooting

| Symptom                       | What to check                                                                                                                      |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Webhook signature is rejected | Use the endpoint's signing secret and verify the unmodified request body.                                                          |
| No MicroVM launches           | Check the webhook subscription, session ownership filter, pending `environment_connection` action, and launcher's IAM permissions. |
| Image build fails             | Check the S3 artifact, image build role, and `/ready` hook response.                                                               |
| `/run` fails or times out     | Check secret access and executor startup. Return after starting the process, not after the turn.                                   |
| Executor can't connect        | Check the environment ID, remote URL, key ownership, and outbound network access.                                                  |
| VM stops during a turn        | Check maximum lifetime and idle policy; outbound executor traffic doesn't count as inbound activity.                               |

For connection failures, inspect `agent.session.environment.failed` and the executor logs. See [Self-hosted sandboxes](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted>) for the shared executor contract.

## References

- [AWS Lambda MicroVMs guide for Agents API](<https://developers.openai.com/api/docs/guides/agents-api/environments/providers/aws>)
- [Running and using MicroVMs](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-launching.html>)
- [Security and permissions](<https://docs.aws.amazon.com/lambda/latest/dg/microvms-security.html>)


</details>
