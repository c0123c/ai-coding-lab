# OpenAI Agents SDK 云端沙箱验证样例

按不同提供方给出设置、运行和预期结果，用于人工验证沙箱后端；属于工程测试样例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-08-12](<https://github.com/openai/openai-agents-python/commit/c12f14d1e98a37774c449107b21f7bbe82d0aa31>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/OpenAI Agents SDK 云端沙箱验证样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 云沙盒扩展示例

这些示例用于手动验证 `agents.extensions.sandbox` 下的云沙箱后端。

他们有意保持流程简单：

1. 在内存中构建一个微小的清单。
2. 创建一个 `SandboxAgent`，通过一个 shell 工具检查该工作区。
3. 针对 E2B、Modal、Daytona、Cloudflare、Runloop、Blaxel 或 Vercel 运行 agent。

所有这些示例都需要 `OPENAI_API_KEY`，因为它们通过正常的 `Runner` 路径调用模型。每个云后端还需要自己的提供商凭据。

## E2B

### 设置

安装额外的存储库：

```bash
uv sync --extra e2b
```

创建一个E2B帐户，创建一个API密钥，并将其导出为`E2B_API_KEY`。
官方设置文档是：

- <https://e2b.dev/docs/api-key>
- <https://e2b.dev/docs/quickstart>

导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export E2B_API_KEY=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/e2b_runner.py --stream
```

有用的标志：

- `--sandbox-type e2b_code_interpreter`
- `--template <template-name>`
- `--timeout 300`
- `--pause-on-exit`

该示例默认为 `e2b`，它提供了 bash 风格的界面。使用 `e2b_code_interpreter` 作为 Jupyter 风格的界面。

## 莫代尔

如果您想要 `examples/sandbox/basic.py` 中显示的相同显式会话生命周期，该示例现在接受 
`--backend modal` 并重用相同的流式工具输出流：

```bash
uv run python examples/sandbox/basic.py \
  --backend modal
```

下面的专用脚本仍然是较小的特定于扩展的示例。

### 设置

安装额外的存储库：

```bash
uv sync --extra modal
```

使用 CLI 令牌设置或环境变量对 Modal 进行身份验证。 
官方参考文献是：

- <https://modal.com/docs/reference/cli/token>
- <https://modal.com/docs/reference/modal.config>
- <https://modal.com/docs/guide/sandbox>

如果您想直接从 CLI 配置凭据：

```bash
uv run modal token set --token-id <token-id> --token-secret <token-secret>
```

或者导出当前 shell 的环境变量：

```bash
export OPENAI_API_KEY=...
export MODAL_TOKEN_ID=...
export MODAL_TOKEN_SECRET=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/modal_runner.py \
  --app-name openai-agents-python-sandbox-example \
  --stream
```

有用的标志：

- `--workspace-persistence tar`
- `--workspace-persistence snapshot_filesystem`
- `--workspace-persistence snapshot_directory`
- `--sandbox-create-timeout-s 60`
- `--native-cloud-bucket-secret-name my-modal-secret`

`app_name` 是 `ModalSandboxClientOptions` 所必需的，因此该示例将其设置为显式 CLI 标志，而不是隐藏它。

模态沙箱还支持通过 `S3Mount`、`R2Mount` 和 HMAC- 验证的 `GCSMount` 上的 `ModalCloudBucketMountStrategy` 原生云存储桶安装。

对于本机云存储桶测试，您可以导出原始凭证环境变量或传递 `--native-cloud-bucket-secret-name` 以重用现有的命名 Modal Secret。

## 云耀

### 设置

安装额外的存储库：

```bash
uv sync --extra cloudflare
```

导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export CLOUDFLARE_SANDBOX_WORKER_URL=...
```

如果您的 Cloudflare Sandbox Service Worker 需要承载身份验证，还需导出：

```bash
export CLOUDFLARE_SANDBOX_API_KEY=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/cloudflare_runner.py --stream
```

有用的标志：

- `--stream` -- 将模型输出流到终端。
- `--demo pty` -- 运行 PTY 演示（与 `tty=true` 的交互式 Python 会话）。
- `--skip-snapshot-check` -- 跳过 stop/resume 快照往返验证。
- `--native-cloud-bucket-name <bucket>` -- 通过 `CloudflareBucketMountStrategy` 安装 R2/S3 存储桶。
- `--native-cloud-bucket-endpoint-url <url>` -- 可选 S3 端点 URL。
- `--api-key <key>`——工人的不记名令牌（或设置`CLOUDFLARE_SANDBOX_API_KEY`）。


Cloudflare 沙箱支持通过 `S3Mount`、`R2Mount` 和 HMAC- 验证的 `GCSMount` 上的 `CloudflareBucketMountStrategy` 原生云存储桶安装。

## 会发生什么

每个脚本都要求模型检查一个小工作区并对其进行总结。 A
成功运行应该：

1. 启动所选的云沙箱后端。
2. 将清单具体化到沙箱工作区中。
3. 至少调用一次 shell 工具。
4. 打印流文本或有关工作区的最终简短答案。

这些示例并未在 CI 中进行实时验证，因为它们依赖于外部云凭据，但它们的形状使得贡献者可以使用每个提供商的一个命令在本地验证后端行为。

## 韦尔塞尔

### 设置

安装额外的存储库：

```bash
uv sync --extra vercel
```

导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export VERCEL_OIDC_TOKEN=...
```

或者使用显式标记和范围变量：

```bash
export OPENAI_API_KEY=...
export VERCEL_TOKEN=...
export VERCEL_PROJECT_ID=...
export VERCEL_TEAM_ID=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/vercel_runner.py --stream
```

有用的标志：

- `--workspace-persistence tar`
- `--workspace-persistence snapshot`
- `--runtime node22`
- `--timeout-ms 120000`

Vercel 示例故意保留在非 PTY 路径上。它涵盖命令执行、工作区具体化和持久性验证，而不依赖于交互式 Websocket 支持。

## 代托纳

### 设置

安装额外的存储库：

```bash
uv sync --extra daytona
```

导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export DAYTONA_API_KEY=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/daytona/daytona_runner.py --stream
```

## 运行循环

### 设置

安装额外的存储库：

```bash
uv sync --extra runloop
```

注册 Runloop，无需信用卡，积分为 $50 @ [platform.runloop.ai](<https://platform.runloop.ai/>).
导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export RUNLOOP_API_KEY=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/runloop/runner.py --stream
```

有用的标志：

- `--blueprint-name <name>`
- `--pause-on-exit`
- `--root`

Runloop 特定的 SDK 功能也可直接在 
`RunloopSandboxClientOptions` 和 `RunloopSandboxClient.platform` 上使用。示例：

```python
from agents.extensions.sandbox.runloop import (
    RunloopAfterIdle,
    RunloopGatewaySpec,
    RunloopLaunchParameters,
    RunloopMcpSpec,
    RunloopSandboxClient,
    RunloopSandboxClientOptions,
    RunloopTunnelConfig,
)

client = RunloopSandboxClient()
sandbox = await client.create(
    options=RunloopSandboxClientOptions(
        blueprint_name="python-3-12",
        launch_parameters=RunloopLaunchParameters(
            network_policy_id="np_123",
            resource_size_request="MEDIUM",
            after_idle=RunloopAfterIdle(idle_time_seconds=300, on_idle="suspend"),
        ),
        tunnel=RunloopTunnelConfig(auth_mode="authenticated"),
        gateways={
            "OPENAI_GATEWAY": RunloopGatewaySpec(
                gateway="openai",
                secret="OPENAI_GATEWAY_SECRET",
            )
        },
        mcp={
            "GITHUB_MCP": RunloopMcpSpec(
                mcp_config="github-readonly",
                secret="GITHUB_MCP_SECRET",
            )
        },
        managed_secrets={"OPENAI_API_KEY": "..."},
        metadata={"team": "agents"},
    )
)

public_blueprints = await client.platform.blueprints.list_public()
public_benchmarks = await client.platform.benchmarks.list_public()
```

`managed_secrets` 存储为 Runloop 帐户机密，并且只有机密引用保留在会话状态中。条目的值可能是 `RunloopExistingSecret()`，它附加一个已按名称存储在帐户中的秘密，不上传任何内容并且保持存储的值不变。平台外观还公开了用于蓝图、基准、秘密、网络策略和轴突的 Runloop 原生助手。

如果启用 `--root`，Runloop 将使用 `launch_parameters.user_parameters={"username":"root","uid":0}` 启动 devbox。在该模式下，默认主目录和工作目录变为 `/root`，因此该示例还使用 `/root` 作为其清单工作空间根。如果您在自己的代码中配置根启动，请依赖根模式默认值或在 `/root` 下显式选择 `manifest.root`。
## 布莱克塞尔

### 设置

安装额外的存储库：

```bash
uv sync --extra blaxel
```

创建 Blaxel 帐户并获取 API 密钥。官方文档是：

- <https://docs.blaxel.ai>
- <https://app.blaxel.ai>

导出所需的环境变量：

```bash
export OPENAI_API_KEY=...
export BL_API_KEY=...
export BL_WORKSPACE=...
```

### 运行

```bash
uv run python examples/sandbox/extensions/blaxel_runner.py --stream
```

有用的标志：

- `--image blaxel/py-app`
- `--region us-pdx-1`
- `--memory 4096`
- `--ttl 1h`
- `--pause-on-exit`
- `--skip-snapshot-check`

该运行程序还包括各个功能的独立演示。通过
`--demo <name>` 运行一个：

- `pty` -- 通过 PTY 由 agent 驱动的交互式 Python 会话
- `drive` -- [Blaxel 驱动器安装 ](<https://docs.blaxel.ai/Agent-drive/Overview>)（持久存储，需要 `--drive-name`）

Blaxel 沙箱支持通过 `BlaxelCloudBucketMountStrategy` 进行云存储桶挂载（S3、R2、GCS）以及通过 `BlaxelDriveMountStrategy` 进行持久驱动器挂载。有关详细信息，请参阅 [Blaxel 驱动器文档 ](<https://docs.blaxel.ai/Agent-drive/Overview>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Cloud Sandbox Extension Examples

These examples are for manual verification of the cloud sandbox backends that live under `agents.extensions.sandbox`.

They intentionally keep the flow simple:

1. Build a tiny manifest in memory.
2. Create a `SandboxAgent` that inspects that workspace through one shell tool.
3. Run the agent against E2B, Modal, Daytona, Cloudflare, Runloop, Blaxel, or Vercel.

All of these examples require `OPENAI_API_KEY`, because they call the model through the normal `Runner` path. Each cloud backend also needs its own provider credentials.

## E2B

### Setup

Install the repo extra:

```bash
uv sync --extra e2b
```

Create an E2B account, create an API key, and export it as `E2B_API_KEY`.
The official setup docs are:

- <https://e2b.dev/docs/api-key>
- <https://e2b.dev/docs/quickstart>

Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export E2B_API_KEY=...
```

### Run

```bash
uv run python examples/sandbox/extensions/e2b_runner.py --stream
```

Useful flags:

- `--sandbox-type e2b_code_interpreter`
- `--template <template-name>`
- `--timeout 300`
- `--pause-on-exit`

The example defaults to `e2b`, which provides a bash-style interface. Use `e2b_code_interpreter` for a Jupyter-style interface.

## Modal

If you want the same explicit session lifecycle shown in `examples/sandbox/basic.py`, that example now accepts
`--backend modal` and reuses the same streamed tool-output flow:

```bash
uv run python examples/sandbox/basic.py \
  --backend modal
```

The dedicated script below stays as the smaller extension-specific example.

### Setup

Install the repo extra:

```bash
uv sync --extra modal
```

Authenticate Modal with either CLI token setup or environment variables. The
official references are:

- <https://modal.com/docs/reference/cli/token>
- <https://modal.com/docs/reference/modal.config>
- <https://modal.com/docs/guide/sandbox>

If you want to configure credentials directly from the CLI:

```bash
uv run modal token set --token-id <token-id> --token-secret <token-secret>
```

Or export environment variables for the current shell:

```bash
export OPENAI_API_KEY=...
export MODAL_TOKEN_ID=...
export MODAL_TOKEN_SECRET=...
```

### Run

```bash
uv run python examples/sandbox/extensions/modal_runner.py \
  --app-name openai-agents-python-sandbox-example \
  --stream
```

Useful flags:

- `--workspace-persistence tar`
- `--workspace-persistence snapshot_filesystem`
- `--workspace-persistence snapshot_directory`
- `--sandbox-create-timeout-s 60`
- `--native-cloud-bucket-secret-name my-modal-secret`

`app_name` is required by `ModalSandboxClientOptions`, so the example makes it an explicit CLI flag instead of hiding it.

Modal sandboxes also support native cloud bucket mounts through `ModalCloudBucketMountStrategy` on `S3Mount`, `R2Mount`, and HMAC-authenticated `GCSMount`.

For native cloud bucket testing, you can either export raw credential environment variables or pass `--native-cloud-bucket-secret-name` to reuse an existing named Modal Secret instead.

## Cloudflare

### Setup

Install the repo extra:

```bash
uv sync --extra cloudflare
```

Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export CLOUDFLARE_SANDBOX_WORKER_URL=...
```

If your Cloudflare Sandbox Service worker requires bearer auth, also export:

```bash
export CLOUDFLARE_SANDBOX_API_KEY=...
```

### Run

```bash
uv run python examples/sandbox/extensions/cloudflare_runner.py --stream
```

Useful flags:

- `--stream` -- stream model output to the terminal.
- `--demo pty` -- run a PTY demo (interactive Python session with `tty=true`).
- `--skip-snapshot-check` -- skip the stop/resume snapshot round-trip verification.
- `--native-cloud-bucket-name <bucket>` -- mount an R2/S3 bucket via `CloudflareBucketMountStrategy`.
- `--native-cloud-bucket-endpoint-url <url>` -- optional S3 endpoint URL.
- `--api-key <key>` -- bearer token for the worker (or set `CLOUDFLARE_SANDBOX_API_KEY`).


Cloudflare sandboxes support native cloud bucket mounts through `CloudflareBucketMountStrategy` on `S3Mount`, `R2Mount`, and HMAC-authenticated `GCSMount`.

## What to expect

Each script asks the model to inspect a small workspace and summarize it. A
successful run should:

1. Start the chosen cloud sandbox backend.
2. Materialize the manifest into the sandbox workspace.
3. Call the shell tool at least once.
4. Print either streamed text or a final short answer about the workspace.

These examples are not live-validated in CI because they depend on external cloud credentials, but they are shaped so contributors can verify backend behavior locally with one command per provider.

## Vercel

### Setup

Install the repo extra:

```bash
uv sync --extra vercel
```

Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export VERCEL_OIDC_TOKEN=...
```

Or use explicit token and scope variables:

```bash
export OPENAI_API_KEY=...
export VERCEL_TOKEN=...
export VERCEL_PROJECT_ID=...
export VERCEL_TEAM_ID=...
```

### Run

```bash
uv run python examples/sandbox/extensions/vercel_runner.py --stream
```

Useful flags:

- `--workspace-persistence tar`
- `--workspace-persistence snapshot`
- `--runtime node22`
- `--timeout-ms 120000`

The Vercel example stays on the non-PTY path on purpose. It covers command execution, workspace materialization, and persistence verification without depending on interactive websocket support.

## Daytona

### Setup

Install the repo extra:

```bash
uv sync --extra daytona
```

Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export DAYTONA_API_KEY=...
```

### Run

```bash
uv run python examples/sandbox/extensions/daytona/daytona_runner.py --stream
```

## Runloop

### Setup

Install the repo extra:

```bash
uv sync --extra runloop
```

Sign up for Runloop, no credit card required and $50 in credits @ [platform.runloop.ai](<https://platform.runloop.ai/>).
Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export RUNLOOP_API_KEY=...
```

### Run

```bash
uv run python examples/sandbox/extensions/runloop/runner.py --stream
```

Useful flags:

- `--blueprint-name <name>`
- `--pause-on-exit`
- `--root`

Runloop-specific SDK features are also available directly on
`RunloopSandboxClientOptions` and `RunloopSandboxClient.platform`. Example:

```python
from agents.extensions.sandbox.runloop import (
    RunloopAfterIdle,
    RunloopGatewaySpec,
    RunloopLaunchParameters,
    RunloopMcpSpec,
    RunloopSandboxClient,
    RunloopSandboxClientOptions,
    RunloopTunnelConfig,
)

client = RunloopSandboxClient()
sandbox = await client.create(
    options=RunloopSandboxClientOptions(
        blueprint_name="python-3-12",
        launch_parameters=RunloopLaunchParameters(
            network_policy_id="np_123",
            resource_size_request="MEDIUM",
            after_idle=RunloopAfterIdle(idle_time_seconds=300, on_idle="suspend"),
        ),
        tunnel=RunloopTunnelConfig(auth_mode="authenticated"),
        gateways={
            "OPENAI_GATEWAY": RunloopGatewaySpec(
                gateway="openai",
                secret="OPENAI_GATEWAY_SECRET",
            )
        },
        mcp={
            "GITHUB_MCP": RunloopMcpSpec(
                mcp_config="github-readonly",
                secret="GITHUB_MCP_SECRET",
            )
        },
        managed_secrets={"OPENAI_API_KEY": "..."},
        metadata={"team": "agents"},
    )
)

public_blueprints = await client.platform.blueprints.list_public()
public_benchmarks = await client.platform.benchmarks.list_public()
```

`managed_secrets` are stored as Runloop account secrets and only secret references are persisted in session state. An entry's value may instead be `RunloopExistingSecret()`, which attaches a secret that is already stored on the account by name, uploading nothing and leaving the stored value untouched. The platform facade also exposes Runloop-native helpers for blueprints, benchmarks, secrets, network policies, and axons.

If you enable `--root`, Runloop launches the devbox with `launch_parameters.user_parameters={"username":"root","uid":0}`. In that mode, the default home and working directory become `/root`, so the example also uses `/root` as its manifest workspace root. If you configure root launch in your own code, either rely on that root-mode default or explicitly choose a `manifest.root` under `/root`.
## Blaxel

### Setup

Install the repo extra:

```bash
uv sync --extra blaxel
```

Create a Blaxel account and get an API key. The official docs are:

- <https://docs.blaxel.ai>
- <https://app.blaxel.ai>

Export the required environment variables:

```bash
export OPENAI_API_KEY=...
export BL_API_KEY=...
export BL_WORKSPACE=...
```

### Run

```bash
uv run python examples/sandbox/extensions/blaxel_runner.py --stream
```

Useful flags:

- `--image blaxel/py-app`
- `--region us-pdx-1`
- `--memory 4096`
- `--ttl 1h`
- `--pause-on-exit`
- `--skip-snapshot-check`

The runner also includes standalone demos for individual features. Pass
`--demo <name>` to run one:

- `pty` -- agent-driven interactive Python session via PTY
- `drive` -- [Blaxel Drive mount](<https://docs.blaxel.ai/Agent-drive/Overview>) (persistent storage, requires `--drive-name`)

Blaxel sandboxes support cloud bucket mounts (S3, R2, GCS) through `BlaxelCloudBucketMountStrategy` and persistent drive mounts through `BlaxelDriveMountStrategy`. See the [Blaxel Drive docs](<https://docs.blaxel.ai/Agent-drive/Overview>) for details.


</details>
