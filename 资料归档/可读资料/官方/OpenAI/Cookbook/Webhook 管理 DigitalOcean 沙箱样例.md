# Webhook 管理 DigitalOcean 沙箱样例

验证事件签名并创建带执行服务的 MicroVM，介绍控制器、镜像、配置和资源回收。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/digitalocean/webhook_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/digitalocean/webhook_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# DigitalOcean webhook 管理的沙箱

端点验证签名并提供 DigitalOcean 托管 Agents
运行时服务（MARS) 需要沙箱的会话。 MARS 靴子一个
鞭炮 microVM 从已经包含的已发布图像 Codex CLI
并启动执行程序本身，因此该处理程序永远不会运行 `codex exec-server`
并且从不管理容器。

沙箱以 OpenAI 会话 ID 命名，控制器在配置之前检查所有 
 匹配页面。进程本地锁序列化 webhook
 协调。运行 ** 一个控制器工作线程和一个副本 **；多个副本
或重叠部署需要分布式协调或提供者端
幂等性。重新启动会重用DigitalOcean列表中可见的沙箱，但
不明确的创建超时在重试之前仍然需要协调。
空闲沙箱会自动暂停并在重新连接时恢复。

## 部署

使用 Python 3.11+ 和启用沙箱的 DigitalOcean 有权访问的帐户
`codex-agentapi`. [DigitalOcean 托管 Agents](<https://docs.digitalocean.com/products/managed-agents/>)
正在公开预览中。创建 agent
使用 [共享设置](<Webhook 管理沙箱的总入口.md#设置一次>) 与该提供商的
`webhook_managed/client.py`.

使用 [PyDo 0.41.0 或稍后](<https://github.com/digitalocean/pydo/releases>) 与
异步支持。该脚本内联声明此依赖项。
单独安装：

```bash
pip install "pydo[aio]>=0.41.0"
```

应用程序平台从 Git 存储库进行部署。推送这些示例的副本
到您自己的存储库，保留 `examples/agents_api/` 路径。套装
`github.repo` 在 [app.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/app.yaml>) 到它，然后创建应用程序。的
[Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/Dockerfile>) 打包处理程序并共享 [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>),
从存储库根目录构建并安装依赖项
在构建时；凭证仅在运行时可用。

此配方使用 OpenAI Python SDK `3.13.0` 或更高版本与 `client.beta.agents`。
不要将 API 键或 Git 令牌添加到 Dockerfile 或构建上下文。

安装 [doctl 1.170.0 或更高版本 ](<https://github.com/digitalocean/doctl/releases>)
 并使用 `doctl auth init` 进行身份验证以运行下面的部署和清理 
 命令。 CLI 不需要在本地运行 Python 控制器。

```bash
doctl apps create --spec examples/agents_api/sandboxes/digitalocean/webhook_managed/app.yaml
```

在服务上配置这些变量：

- `OPENAI_API_KEY`：controller/session-read 凭证。
- `OPENAI_AGENT_ID`：共享设置中的agent ID。
- `OPENAI_EXECUTOR_API_KEY`：受限执行者凭证，范围为
  `api.agents.environments.connect`.
- `OPENAI_WEBHOOK_SECRET`：注册端点时由OpenAI生成。
- `DIGITALOCEAN_TOKEN`：可以管理MARS会话的API令牌。

仅对于第一次部署，使用 `pending-webhook-registration` 作为签名 
 值；在您配置签名密钥之前，接收方会拒绝请求。

`doctl apps list` 打印部署的 URL。 Webhook URL 是 
`https://YOUR_APP.ondigitalocean.app/webhook`，并且 OpenAI 必须能够在不登录的情况下访问 
。注册 webhook，启用 `agent.session.action_required`
 和 `agent.session.failed`，将签名值替换为真实密钥，然后重新部署 
。然后按照[运行并重新连接](<Webhook 管理沙箱的总入口.md#运行并重新连接>)：

```bash
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

环境变量更改需要新的部署。

PyDo 0.41.0 可以在成功空恢复 
 响应后引发 `StreamConsumedError`。如果是，控制器会在确认 
 webhook 之前在 
 十秒内检查 `SESSION_STATUS_READY`。未经确认的简历或任何其他 SDK 错误仍然会导致投递失败。

### 本地运行控制器

```bash
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/handler.py
```

使用隧道公开端口 8080 并注册该端口 URL。在 macOS python.org
 构建上，对 `api.digitalocean.com` 的请求可能会失败，并显示 
`CERTIFICATE_VERIFY_FAILED`；运行`Install Certificates.command`，或导出
`SSL_CERT_FILE="$(python -c 'import certifi; print(certifi.where())')"`。

## 配置沙箱

共享的[environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>)是配置的单位。两个 
 字段最重要：

- `agent: codex-agentapi` 选择运行执行器的映像。
- `config` 在创建会话时传递到 OpenAI，并且
需要。

凭证属于 `secrets`，而不是 `env`；创建 API 拒绝 
 凭证形状的 `env` 值。 `egress` 接受如图所示的主机白名单，或 
`unrestricted` 保持目的地开放。要调整沙箱大小或固定区域，
添加相应的清单字段 - 请参阅`doctl harness-runtime sizes`。

## 停止或清理

沙箱名称为 `mars-`，后跟 API 会话 ID。

```bash
doctl harness-runtime remove mars-SESSION_ID
```

沙箱在回合之间保留文件；被摧毁的沙箱重新开始。 
 没有会话删除 Webhook，因此删除 API 会话不会释放 
 沙箱 — 将两者都删除。在删除应用程序
平台应用程序之前，请先删除OpenAI webhook。


## 原文对照

<details>
<summary>展开完整原文</summary>

# DigitalOcean webhook-managed sandbox

The endpoint verifies the signature and provisions a DigitalOcean Managed Agents
Runtime Service (MARS) sandbox for the session that needs one. MARS boots a
Firecracker microVM from a published image that already contains the Codex CLI
and starts the executor itself, so this handler never runs `codex exec-server`
and never manages a container.

The sandbox is named after the OpenAI session ID, and the controller checks all
matching pages before provisioning. A process-local lock serializes webhook
reconciliation. Run **one controller worker and one replica**; multiple replicas
or overlapping deployments need distributed coordination or provider-side
idempotency. A restart reuses sandboxes visible in DigitalOcean's listing, but
an ambiguous create timeout still needs reconciliation before retrying.
Idle sandboxes are paused automatically and resumed on reconnect.

## Deploy

Use Python 3.11+ and a sandbox-enabled DigitalOcean account with access to
`codex-agentapi`. [DigitalOcean Managed Agents](<https://docs.digitalocean.com/products/managed-agents/>)
is in public preview. Create the agent
using the [shared setup](<Webhook 管理沙箱的总入口.md#set-up-once>) with this provider's
`webhook_managed/client.py`.

Use [PyDo 0.41.0 or later](<https://github.com/digitalocean/pydo/releases>) with
async support. The script declares this dependency inline.
To install it separately:

```bash
pip install "pydo[aio]>=0.41.0"
```

App Platform deploys from a Git repository. Push a copy of these examples
to your own repository, preserving the `examples/agents_api/` paths. Set
`github.repo` in [app.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/app.yaml>) to it, then create the app. The
[Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/webhook_managed/Dockerfile>) packages the handler and shared [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>),
builds from the repository root, and installs dependencies
at build time; credentials are available only at runtime.

This recipe uses OpenAI Python SDK `3.13.0` or later with `client.beta.agents`.
Do not add API keys or Git tokens to the Dockerfile or build context.

Install [doctl 1.170.0 or later](<https://github.com/digitalocean/doctl/releases>)
and authenticate with `doctl auth init` to run the deployment and cleanup
commands below. The CLI is not required to run the Python controller locally.

```bash
doctl apps create --spec examples/agents_api/sandboxes/digitalocean/webhook_managed/app.yaml
```

Configure these variables on the service:

- `OPENAI_API_KEY`: controller/session-read credential.
- `OPENAI_AGENT_ID`: agent ID from the shared setup.
- `OPENAI_EXECUTOR_API_KEY`: restricted executor credential, scoped to
  `api.agents.environments.connect`.
- `OPENAI_WEBHOOK_SECRET`: generated by OpenAI when registering the endpoint.
- `DIGITALOCEAN_TOKEN`: API token that can manage MARS sessions.

For the first deployment only, use `pending-webhook-registration` as the signing
value; the receiver rejects requests until you configure the signing secret.

`doctl apps list` prints the deployed URL. The webhook URL is
`https://YOUR_APP.ondigitalocean.app/webhook`, and OpenAI must be able to reach
it without a login. Register the webhook, enable `agent.session.action_required`
and `agent.session.failed`, replace the signing value with the real secret, and
redeploy. Then follow [run and reconnect](<Webhook 管理沙箱的总入口.md#run-and-reconnect>):

```bash
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/client.py --agent-id "$OPENAI_AGENT_ID"
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/client.py --session-id "$SESSION_ID" --input "Run another shell command."
```

Environment-variable changes require a new deployment.

PyDo 0.41.0 can raise `StreamConsumedError` after a successful empty resume
response. If it does, the controller checks for `SESSION_STATUS_READY` within
ten seconds before acknowledging the
webhook. An unconfirmed resume or any other SDK error still fails the delivery.

### Run the controller locally

```bash
uv run examples/agents_api/sandboxes/digitalocean/webhook_managed/handler.py
```

Expose port 8080 with a tunnel and register that URL. On macOS python.org
builds, requests to `api.digitalocean.com` can fail with
`CERTIFICATE_VERIFY_FAILED`; run `Install Certificates.command`, or export
`SSL_CERT_FILE="$(python -c 'import certifi; print(certifi.where())')"`.

## Configure the sandbox

The shared [environment.yaml](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/digitalocean/environment.yaml>) is the unit of configuration. Two
fields matter most:

- `agent: codex-agentapi` selects the image that runs the executor.
- `config` is passed through to OpenAI when the session is created, and is
  required.

Credentials belong under `secrets`, not `env`; the create API rejects a
credential-shaped `env` value. `egress` accepts a host allowlist as shown, or
`unrestricted` to leave destinations open. To size the sandbox or pin a region,
add the corresponding manifest fields — see `doctl harness-runtime sizes`.

## Stop or clean up

The sandbox name is `mars-` followed by the API session ID.

```bash
doctl harness-runtime remove mars-SESSION_ID
```

Sandboxes persist files between turns; a destroyed sandbox starts fresh. There
is no session-deletion webhook, so deleting the API session does not release the
sandbox — delete both. Remove the OpenAI webhook before deleting the App
Platform app.


</details>
