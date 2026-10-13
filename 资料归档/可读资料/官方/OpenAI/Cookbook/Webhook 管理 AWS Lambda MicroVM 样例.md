# Webhook 管理 AWS Lambda MicroVM 样例

通过 API Gateway 和 Lambda 接收事件、启动执行环境、检查文件产物并清理，说明重试、生命周期与监控。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/openai/openai-cookbook/commit/86ff2cc0c4b2417feb36bc76ff0f24c5c4e785be>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/aws/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/aws/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Webhook 管理的 AWS Lambda MicroVM

OpenAI webhook 通过 API 网关和 Lambda 启动 MicroVM。
[main.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/main.py>) 要求 agent 写入 `hello.txt`，然后下载并检查它。
它终止VM 并删除会话。有关架构请参阅 [AWS 指南 ](<用 AWS Lambda MicroVM 运行 Agent 工具.md>)
。

## 设置

使用 
[ 应用程序管理的设置 ](<应用管理 AWS Lambda MicroVM 样例.md>) 创建运行时角色和映像。
部署脚本读取其 `.local/image.json` 和 `.local/execution-role.json`。
您还需要 Lambda 的 AWS 权限， API 网关、IAM 和 Secrets Manager。
从存储库根目录运行以下命令：

```bash
export AWS_PROFILE=your-profile
export AWS_REGION=us-west-2
export EXAMPLE_DIR=examples/agents_api/sandboxes/aws/webhook_managed
```

将 `OPENAI_API_KEY` 设置为相同的应用程序密钥。为此 webhook
 创建一个 agent 并设置其返回的 ID：

```bash
uv run "$EXAMPLE_DIR/main.py" --create-agent codex-microvm-webhook-demo
export OPENAI_AGENT_ID=agent_...
```

### 1。创建控制器机密

使用这些 JSON 字段在 Secrets Manager 中创建 `codex/agents-api/webhook-controller`：

- `OPENAI_API_KEY`：应用程序密钥。
- `OPENAI_WEBHOOK_SECRET`：保留为空，直到注册 Webhook。

使用默认 Secrets Manager 加密密钥。

```bash
export CONTROLLER_SECRET_ARN="$(aws secretsmanager describe-secret \
  --secret-id codex/agents-api/webhook-controller --region "$AWS_REGION" \
  --query ARN --output text)"
```

### 2。部署并注册

```bash
uv run "$EXAMPLE_DIR/deploy.py" \
  --agent-id "$OPENAI_AGENT_ID" \
  --controller-secret-arn "$CONTROLLER_SECRET_ARN"
```

[deploy.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/deploy.py>) 创建 Lambda 和 API 网关的 `POST /webhook` 路由。
它将资源 ID 保存在 `.local/deployment.json` 中。重新运行它以部署代码更改
或使用新的映像版本。

在您的OpenAI项目的**设置→Webhooks**中，将打印的URL注册为
`agent.session.action_required`和`agent.session.failed`。将签名密钥 
 复制到控制器密钥中的 `OPENAI_WEBHOOK_SECRET` 中，并保留 `OPENAI_API_KEY`。

### 3。运行

```bash
uv run "$EXAMPLE_DIR/main.py"
cat "$EXAMPLE_DIR/.local/hello.txt"
```

[handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/handler.py>) 验证 webhook 签名并检查会话 
 是否属于配置的 agent 并且仍然需要执行程序。它启动 VM
 并将其 ID 保存在会话元数据中以供客户端读取。

客户端需要 AWS 权限才能读取 VM 详细信息、创建身份验证令牌并
终止 VM。

要测试 webhook 驱动的恢复，请重建映像，重新部署处理程序，然后运行：

```bash
uv run "$EXAMPLE_DIR/main.py" --suspend-resume
```

客户端在第一回合后暂停 VM，然后发送另一个输入。
webhook 恢复相同的 VM。 agent读取已有的`hello.txt`，
客户端再次下载以验证其内容。

客户端还需要`lambda:SuspendMicrovm`。部署脚本授予
启动器`lambda:GetMicrovm`和`lambda:ResumeMicrovm`。

## 生命周期和重试次数

客户端在最后一轮后停止VM。子代理事件不会触发
清理。检查当前会话状态后，处理程序还会停止 
`agent.session.failed` 上的 VM。

每个 VM 都有一个 15 分钟的生命周期限制。使用`--suspend-resume`，它可以保持
暂停五分钟。仅在工具完成后，在轮次之间暂停。
如果记录的 VM 过期，则开始新的会话。在记录 VM ID
 之前，Webhook 重试仍可以启动额外的虚拟机。

## 监控和清理

将`MICROVM_IMAGE_ARN`设置为共享`.local/image.json`中的`image_arn`。

```bash
aws logs tail /aws/lambda/codex-agents-api-webhook --since 10m --region "$AWS_REGION"
aws lambda-microvms list-microvms --image-identifier "$MICROVM_IMAGE_ARN" \
  --region "$AWS_REGION" --query 'items[].{id:microvmId,state:state}' --output table
```

HTTP 400表示签名无效； 503 表示签名密钥丢失。
对于 HTTP 500，检查 CloudWatch 中的错误类型以及处理程序的 IAM 权限。

要删除部署，请先删除 OpenAI Webhook 并停止新的运行。
终止剩余的虚拟机，然后删除 `.local/deployment.json` 中列出的 API 网关 API、Lambda、角色和日志 
 组。不再使用时删除agent和控制器密码
。映像和执行程序密钥与 
 应用程序管理的示例共享。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Webhook-managed AWS Lambda MicroVM

An OpenAI webhook starts a MicroVM through API Gateway and Lambda.
[main.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/main.py>) asks the agent to write `hello.txt`, then downloads and checks it.
It terminates the VM and deletes the session. See the [AWS guide](<用 AWS Lambda MicroVM 运行 Agent 工具.md>)
for the architecture.

## Setup

Create the runtime role and image with the
[application-managed setup](<应用管理 AWS Lambda MicroVM 样例.md>).
The deploy script reads its `.local/image.json` and `.local/execution-role.json`.
You also need AWS permissions for Lambda, API Gateway, IAM, and Secrets Manager.
Run these commands from the repository root:

```bash
export AWS_PROFILE=your-profile
export AWS_REGION=us-west-2
export EXAMPLE_DIR=examples/agents_api/sandboxes/aws/webhook_managed
```

Set `OPENAI_API_KEY` to the same application key. Create an agent for this webhook
and set its returned ID:

```bash
uv run "$EXAMPLE_DIR/main.py" --create-agent codex-microvm-webhook-demo
export OPENAI_AGENT_ID=agent_...
```

### 1. Create the controller secret

Create `codex/agents-api/webhook-controller` in Secrets Manager with these JSON fields:

- `OPENAI_API_KEY`: the application key.
- `OPENAI_WEBHOOK_SECRET`: leave empty until the webhook is registered.

Use the default Secrets Manager encryption key.

```bash
export CONTROLLER_SECRET_ARN="$(aws secretsmanager describe-secret \
  --secret-id codex/agents-api/webhook-controller --region "$AWS_REGION" \
  --query ARN --output text)"
```

### 2. Deploy and register

```bash
uv run "$EXAMPLE_DIR/deploy.py" \
  --agent-id "$OPENAI_AGENT_ID" \
  --controller-secret-arn "$CONTROLLER_SECRET_ARN"
```

[deploy.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/deploy.py>) creates the Lambda and API Gateway's `POST /webhook` route.
It saves resource IDs in `.local/deployment.json`. Rerun it to deploy code changes
or use a new image version.

In your OpenAI project's **Settings → Webhooks**, register the printed URL for
`agent.session.action_required` and `agent.session.failed`. Copy the signing secret
into `OPENAI_WEBHOOK_SECRET` in the controller secret, preserving `OPENAI_API_KEY`.

### 3. Run

```bash
uv run "$EXAMPLE_DIR/main.py"
cat "$EXAMPLE_DIR/.local/hello.txt"
```

[handler.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/webhook_managed/handler.py>) verifies the webhook signature and checks that the session
belongs to the configured agent and still needs an executor. It launches the VM
and saves its ID in session metadata for the client to read.

The client needs AWS permission to read VM details, create an authentication token, and
terminate the VM.

To test webhook-driven resume, rebuild the image, redeploy the handler, then run:

```bash
uv run "$EXAMPLE_DIR/main.py" --suspend-resume
```

The client suspends the VM after the first turn, then sends another input.
The webhook resumes the same VM. The agent reads the existing `hello.txt`, and
the client downloads it again to verify its contents.

The client also needs `lambda:SuspendMicrovm`. The deployment script grants the
launcher `lambda:GetMicrovm` and `lambda:ResumeMicrovm`.

## Lifetime and retries

The client stops the VM after the final turn. Subagent events do not trigger
cleanup. The handler also stops the VM on
`agent.session.failed` after checking the current session state.

Each VM has a 15-minute lifetime limit. With `--suspend-resume`, it can remain
suspended for five minutes. Only suspend between turns, after tools finish.
If the recorded VM expires, start a new session. Webhook retries before a VM ID
is recorded can still launch extra VMs.

## Monitoring and cleanup

Set `MICROVM_IMAGE_ARN` to the `image_arn` in the shared `.local/image.json`.

```bash
aws logs tail /aws/lambda/codex-agents-api-webhook --since 10m --region "$AWS_REGION"
aws lambda-microvms list-microvms --image-identifier "$MICROVM_IMAGE_ARN" \
  --region "$AWS_REGION" --query 'items[].{id:microvmId,state:state}' --output table
```

HTTP 400 means the signature is invalid; 503 means the signing secret is missing.
For HTTP 500, check the error type in CloudWatch and the handler's IAM permissions.

To remove the deployment, delete the OpenAI webhook and stop new runs first.
Terminate remaining VMs, then remove the API Gateway API, Lambda, role, and log
group listed in `.local/deployment.json`. Delete the agent and controller secret
when no longer used. The image and executor secret are shared with the
application-managed example.


</details>
