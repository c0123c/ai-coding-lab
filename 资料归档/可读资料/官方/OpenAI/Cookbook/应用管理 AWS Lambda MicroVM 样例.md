# 应用管理 AWS Lambda MicroVM 样例

说明创建运行角色、构建镜像、传输文件、验证输出和清理会话，展示自托管执行环境的基础流程。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-29](<https://github.com/openai/openai-cookbook/commit/86ff2cc0c4b2417feb36bc76ff0f24c5c4e785be>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/sandboxes/aws/application_managed/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/examples/agents_api/sandboxes/aws/application_managed/README.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 应用程序管理 AWS Lambda MicroVM

[main.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/main.py>)要求agent在MicroVM中写入`hello.txt`，然后下载
并检查它。它终止 VM 并删除会话。
请参阅 [AWS 指南 ](<用 AWS Lambda MicroVM 运行 Agent 工具.md>) 了解架构。

## 设置

您需要具有 Lambda MicroVMs 访问权限的 Python 3.11+、`uv`、AWS 凭证，以及具有 Agents API 访问权限的 
`OPENAI_API_KEY`。从存储库根目录运行这些命令：

```bash
export AWS_PROFILE=your-profile
export AWS_REGION=us-west-2
export EXAMPLE_DIR=examples/agents_api/sandboxes/aws/application_managed
aws sts get-caller-identity --region "$AWS_REGION"
```

为本地应用程序设置`OPENAI_API_KEY`。在 Secrets Manager 中将单独的环境密钥 
(`OPENAI_EXECUTOR_API_KEY`) 存储为 `codex/agents-api/executor`。
这两个密钥必须共享相同的组织、项目以及用户或服务帐户 
 所有者。使用带有 `OPENAI_EXECUTOR_API_KEY` 字段、
 和默认 Secrets Manager 加密密钥的明文 SecretString 或 JSON。

### 1。创建运行时角色

```bash
export EXECUTOR_SECRET_ARN="$(aws secretsmanager describe-secret \
  --secret-id codex/agents-api/executor --region "$AWS_REGION" \
  --query ARN --output text)"
uv run "$EXAMPLE_DIR/configure_execution_role.py" \
  --executor-secret-arn "$EXECUTOR_SECRET_ARN"
```

运行一次以创建具有对该密钥的读取访问权限的 `codex-agents-api-executor`。
角色和密钥 ARN 保存在 `.local/execution-role.json` 中。
客户管理的 KMS 密钥还需要 `kms:Decrypt` 权限。

### 2。构建图像

```bash
uv run "$EXAMPLE_DIR/build_image.py" --name codex-executor
```

该脚本创建 S3 源存储桶、构建角色和 CloudWatch 日志组，
，然后上传 [Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/Dockerfile>) 和[sandbox_server.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/sandbox_server.py>).
AWS 使用 8 GB 内存（4 vCPU 基线）构建映像。该脚本将其 ARN
 和版本保存在 `.local/image.json` 中。

### 3。运行

```bash
uv run "$EXAMPLE_DIR/main.py"
cat "$EXAMPLE_DIR/.local/hello.txt"
```

要在两圈内重复使用 VM：

```bash
uv run "$EXAMPLE_DIR/main.py" --suspend-resume
```

该脚本在第一回合后暂停并恢复 VM。下一回合读取
现有的`hello.txt`，脚本再次下载它以验证其内容。

使用 `--image-state`、`--execution-state` 或 `--output` 更改本地路径。
状态和输出文件保存在 `.local/` 中，Git 会忽略它。

## 运行时挂钩

[sandbox_server.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/sandbox_server.py>) 处理 AWS 的生命周期挂钩。就绪
钩子让AWS对空闲服务器进行快照。启动时，运行挂钩接收 
 会话的环境 ID、远程 URL 和机密 ARN。它通过
VM的IAM角色读取密钥，并在其环境中使用`CODEX_API_KEY`启动Codex。
验证挂钩检查Codex和工作区访问权限。挂起并终止停止
执行器并刷新写入。恢复重新加载密钥并重新连接。

## 传输文件

两条路由都使用 AWS 的经过身份验证的入口代理和相对于 `/workspace` 的路径：

- `PUT /upload/<path>` 将原始字节写入文件并创建丢失的目录。
限制为 64 MiB。不完整的上传会使现有文件保持不变。
- `GET /download/<path>` 下载文件。

`/workspace` 外部的目录和路径或符号链接返回 404。
共享帮助程序处理 AWS 身份验证和 URL 编码：

```python
from pathlib import Path
from common import upload_file, download_file

await upload_file(microvms, vm_id, Path("input.csv"), "data/input.csv")
await download_file(microvms, vm_id, "reports/result.csv", Path("result.csv"))
```

执行器通过 AWS 的互联网出口连接器将出站连接到 OpenAI。
使用不同的连接器进行专用网络。

## 重用和清理

镜像、角色、源存储桶、日志组和密钥保留在AWS中以供重复使用。
更改Dockerfile或服务器后重新构建：

```bash
uv run "$EXAMPLE_DIR/build_image.py" --rebuild
```

清理在成功或错误时运行。每个VM都有15分钟的最大寿命和
10分钟的入站空闲限制；出站执行程序流量不会重置空闲计时器。
suspend/resume 示例最多允许暂停五分钟。在使用此选项之前重建旧的 
 映像。仅在工具完成后在转弯之间暂停。

要删除该示例，请终止剩余的虚拟机并删除状态文件中列出的映像、源存储桶、
日志组和角色。当没有其他 
 工作负载使用该密钥时，删除该密钥。 [webhook 管理的示例](<Webhook 管理 AWS Lambda MicroVM 样例.md>)
共享映像和运行时角色。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Application-managed AWS Lambda MicroVM

[main.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/main.py>) asks the agent to write `hello.txt` in a MicroVM, then downloads
and checks it. It terminates the VM and deletes the session.
See the [AWS guide](<用 AWS Lambda MicroVM 运行 Agent 工具.md>) for the architecture.

## Setup

You need Python 3.11+, `uv`, AWS credentials with Lambda MicroVMs access, and an
`OPENAI_API_KEY` with Agents API access. Run these commands from the repository root:

```bash
export AWS_PROFILE=your-profile
export AWS_REGION=us-west-2
export EXAMPLE_DIR=examples/agents_api/sandboxes/aws/application_managed
aws sts get-caller-identity --region "$AWS_REGION"
```

Set `OPENAI_API_KEY` for the local application. Store a separate environment key
(`OPENAI_EXECUTOR_API_KEY`) in Secrets Manager as `codex/agents-api/executor`.
The two keys must share the same organization, project, and user or service-account
owner. Use a plaintext SecretString or JSON with an `OPENAI_EXECUTOR_API_KEY` field,
and the default Secrets Manager encryption key.

### 1. Create the runtime role

```bash
export EXECUTOR_SECRET_ARN="$(aws secretsmanager describe-secret \
  --secret-id codex/agents-api/executor --region "$AWS_REGION" \
  --query ARN --output text)"
uv run "$EXAMPLE_DIR/configure_execution_role.py" \
  --executor-secret-arn "$EXECUTOR_SECRET_ARN"
```

Run once to create `codex-agents-api-executor` with read access to that secret.
The role and secret ARNs are saved in `.local/execution-role.json`.
A customer-managed KMS key also requires `kms:Decrypt` permission.

### 2. Build the image

```bash
uv run "$EXAMPLE_DIR/build_image.py" --name codex-executor
```

The script creates an S3 source bucket, build role, and CloudWatch log group,
then uploads the [Dockerfile](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/Dockerfile>) and [sandbox_server.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/sandbox_server.py>).
AWS builds the image with 8 GB memory (4 vCPU baseline). The script saves its ARN
and version in `.local/image.json`.

### 3. Run

```bash
uv run "$EXAMPLE_DIR/main.py"
cat "$EXAMPLE_DIR/.local/hello.txt"
```

To reuse the VM across two turns:

```bash
uv run "$EXAMPLE_DIR/main.py" --suspend-resume
```

The script suspends and resumes the VM after the first turn. The next turn reads
the existing `hello.txt`, and the script downloads it again to verify its contents.

Use `--image-state`, `--execution-state`, or `--output` to change the local paths.
State and output files are saved in `.local/`, which Git ignores.

## Runtime hooks

[sandbox_server.py](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/sandboxes/aws/application_managed/sandbox_server.py>) handles AWS's lifecycle hooks. The ready
hook lets AWS snapshot the idle server. At launch, the run hook receives the
session's environment ID, remote URL, and secret ARN. It reads the key through
the VM's IAM role and starts Codex with `CODEX_API_KEY` in its environment.
The validate hook checks Codex and workspace access. Suspend and terminate stop
the executor and flush writes. Resume reloads the key and reconnects.

## Transfer files

Both routes use AWS's authenticated ingress proxy and paths relative to `/workspace`:

- `PUT /upload/<path>` writes raw bytes to a file and creates missing directories.
  The limit is 64 MiB. An incomplete upload leaves the existing file unchanged.
- `GET /download/<path>` downloads a file.

Directories and paths or symlinks outside `/workspace` return 404.
The shared helpers handle AWS authentication and URL encoding:

```python
from pathlib import Path
from common import upload_file, download_file

await upload_file(microvms, vm_id, Path("input.csv"), "data/input.csv")
await download_file(microvms, vm_id, "reports/result.csv", Path("result.csv"))
```

The executor connects outbound to OpenAI through AWS's internet egress connector.
Use a different connector for private networking.

## Reuse and cleanup

The image, roles, source bucket, log group, and secret stay in AWS for reuse.
Rebuild after changing the Dockerfile or server:

```bash
uv run "$EXAMPLE_DIR/build_image.py" --rebuild
```

Cleanup runs on success or error. Each VM has a 15-minute maximum lifetime and a
10-minute inbound idle limit; outbound executor traffic doesn't reset the idle timer.
The suspend/resume example allows up to five minutes suspended. Rebuild an older
image before using this option. Only suspend between turns, after tools finish.

To remove the example, terminate remaining VMs and delete the image, source bucket,
log group, and roles listed in the state files. Delete the secret when no other
workloads use it. The [webhook-managed example](<Webhook 管理 AWS Lambda MicroVM 样例.md>)
shares the image and runtime role.


</details>
