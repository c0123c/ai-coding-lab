# 选择 OpenAI 沙箱客户端

比较本地、Docker 和托管后端、网络与挂载选项，说明各方案的隔离限制和运行配置。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/openai/openai-agents-python/commit/592fa9438bf78276fc5fa79ac315527cf231a13a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/docs/sandbox/clients.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/docs/sandbox/clients.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 沙盒客户端

使用此页面可以选择沙盒工作的运行位置。在大多数情况下，`SandboxAgent` 定义保持不变，而沙箱客户端和客户端特定选项在 [`SandboxRunConfig`][agents.run_config.SandboxRunConfig] 中发生更改。

## 决策指南

<div class="sandbox-nowrap-first-column-table" markdown="1">

|目标|开始于|为什么|
| --- | --- | --- |
|macOS 或 Linux 上的可信本地开发| `UnixLocalSandboxClient` |无需额外安装；命令作为本地主机进程运行。|
|基本容器隔离| `DockerSandboxClient` |使用特定映像在 Docker 内运行工作。|
|托管执行或生产式隔离|托管沙箱客户端|将工作区边界移至提供商管理的环境。|

</div>

！！！警告“Unix 本地执行限制”

`UnixLocalSandboxClient` 作为本地主机进程运行命令。在 Linux 上，此后端不添加 OS- 级别限制：命令可以访问主机进程和任何外部隔离允许的文件和网络资源。工作区目录、`HOME` 或 `cwd` 不限制该访问。

在 macOS 上，该后端使用 `sandbox-exec` 来应用文件系统限制。这些限制不提供网络隔离或与容器相同的边界。

使用 Unix-local 进行受信任的本地开发或在外部隔离的环境中。对于不受信任的命令，包括受不受信任输入影响的命令，请选择适当配置的 Docker 或托管沙箱，或提供外部隔离。查看您的工作负载的所选环境的权限、安装、凭据和网络访问权限。

## 本地客户

对于大多数用户来说，从这两个沙箱客户端之一开始：

<div class="sandbox-nowrap-first-column-table" markdown="1">

|客户|安装|选择它时|示例|
| --- | --- | --- | --- |
| `UnixLocalSandboxClient` |无|macOS 或 Linux 上的可信本地开发，或在外部隔离内执行。|[Unix本地启动器](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/unix_local_runner.py>)|
| `DockerSandboxClient` | `openai-agents[docker]` |您希望容器隔离或特定映像在本地重现目标环境。|[Docker 启动器](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docker/docker_runner.py>)|

</div>

Unix-local 提供本地工作空间，而不需要容器。当您需要后端提供的隔离边界或与其他环境匹配的映像时，请选择 Docker 或托管提供商。

`SandboxPathGrant.host_path` 仅适用于 Docker，并将主机路径映射到容器内的不同 POSIX 路径。 Unix-local 仅支持相同路径授权。有关详细信息，请参阅[清单路径授予](<OpenAI 沙箱 Agent 核心概念.md#清单>)。

### 限制 Unix 本地会话的主机环境继承

默认情况下，`UnixLocalSandboxClient` 从完整的主机进程环境启动每个命令环境。将 `inherit_host_environment=False` 设置为仅传递保守的主机变量白名单：

```python
from agents.sandbox.sandboxes.unix_local import UnixLocalSandboxClient

client = UnixLocalSandboxClient(
    inherit_host_environment=False,
    host_environment_allowlist={"PATH", "LANG", "SSL_CERT_FILE"},
)
```

当省略`inherit_host_environment=False`和`host_environment_allowlist`时，SDK允许`PATH`、`LANG`、`LC_ALL`、`LC_COLLATE`、`LC_CTYPE`、 `LC_MESSAGES`、`LC_MONETARY`、`LC_NUMERIC`、`LC_TIME`、`TZ`、`TERM`、`TMPDIR`、`SSL_CERT_FILE`、`SSL_CERT_DIR`、 `REQUESTS_CA_BUNDLE`、`NODE_EXTRA_CA_CERTS`、`UV_PYTHON`、`NO_COLOR`、`FORCE_COLOR` 和 `CI`。传递自定义集合以替换默认允许列表。自定义允许列表需要 `inherit_host_environment=False`。

`Manifest.environment` 中的值在主机过滤后应用并覆盖继承的值。 Unix 本地命令始终将工作区根接收为 `HOME`。继承策略属于当前客户端而不是序列化会话状态，因此`create(...)`和`resume(...)`应用执行该操作的客户端的策略。

该选项仅过滤继承的环境变量；它不添加OS-级限制。上述 Unix 本地执行限制仍然适用。

要从 Unix-local 切换到 Docker，请保持 agent 定义相同并仅更改运行配置：

```python
from docker import from_env as docker_from_env

from agents.run import RunConfig
from agents.sandbox import SandboxRunConfig
from agents.sandbox.sandboxes.docker import DockerSandboxClient, DockerSandboxClientOptions

run_config = RunConfig(
    sandbox=SandboxRunConfig(
        client=DockerSandboxClient(docker_from_env()),
        options=DockerSandboxClientOptions(image="python:3.14-slim"),
    ),
)
```

当您需要容器隔离或希望沙箱映像与其他环境中使用的映像匹配时，请使用此选项。参见[examples/sandbox/docker/docker_runner.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docker/docker_runner.py>)。

### 禁用 Docker 网络

当 Docker 沙箱不得具有网络访问权限时，设置 `network_mode="none"`：

```python
options = DockerSandboxClientOptions(
    image="python:3.14-slim",
    network_mode="none",
)
```

唯一支持的显式网络模式是`"none"`；省略 `network_mode` 以保留 Docker 的默认行为。禁用网络的沙箱无法公开端口，因此在选项验证期间将 `network_mode="none"` 与非空 `exposed_ports` 元组组合会失败。该设置存储在沙箱会话状态中，如果 SDK 必须在恢复该状态时创建替换容器，则会重新应用该设置。

### 为 Docker 容器添加标签

当应用程序需要识别或管理为沙箱会话创建的 Docker 容器时，设置 `labels`：

```python
options = DockerSandboxClientOptions(
    image="python:3.14-slim",
    labels={
        "com.example.owner": "agents-sdk",
        "com.example.environment": "development",
    },
)
```

SDK 在创建容器时将这些键值对传递给 Docker，并将它们存储在 [`DockerSandboxSessionState`][agents.sandbox.sandboxes.docker.DockerSandboxSessionState] 中。当恢复的会话重新连接到现有容器时，SDK 会验证每个持久标签是否仍具有预期值，如果标签不匹配，则会引发 `ValueError`。当 SDK 从保存的状态创建替换容器时，它会重新应用持久标签。

## 安装和远程存储

挂载条目描述了要公开的存储；挂载策略描述了沙箱后端如何附加该存储。从`agents.sandbox.entries`导入内置挂载条目和通用策略。托管提供商策略可从 `agents.extensions.sandbox` 或提供商特定的扩展包中获取。

常见安装选项：

- `mount_path`：沙箱中存储出现的位置。相对路径在清单根下解析；绝对路径按原样使用。
- `read_only`：默认为`True`。仅当沙箱应写回已安装的存储时才设置 `False`。
- `mount_strategy`：必填。使用与挂载条目和沙箱后端相匹配的策略。

安装被视为临时工作空间条目。快照和持久性流分离或跳过已安装的路径，而不是将已安装的远程存储复制到已保存的工作区中。

通用 local/container 策略：

<div class="sandbox-nowrap-first-column-table" markdown="1">

|策略或模式|当|注释|
| --- | --- | --- |
| `InContainerMountStrategy(pattern=RcloneMountPattern(...))` |沙箱镜像可以运行`rclone`。|支持 S3、GCS、R2、Azure Blob 和 Box。 `RcloneMountPattern`可以运行在`fuse`模式或`nfs`模式下。|
| `InContainerMountStrategy(pattern=MountpointMountPattern(...))` |该映像具有 `mount-s3`，并且您需要 Mountpoint 式 S3 或 S3- 兼容访问。|支持`S3Mount`和`GCSMount`。|
| `InContainerMountStrategy(pattern=FuseMountPattern(...))` |该图像支持 `blobfuse2` 和 FUSE。|支持`AzureBlobMount`。|
| `InContainerMountStrategy(pattern=S3FilesMountPattern(...))` |该映像具有 `mount.s3files`，并且可以到达现有的 S3 文件挂载目标。|支持`S3FilesMount`。|
| `DockerVolumeMountStrategy(driver=...)` |Docker 应该在容器启动之前附加一个由卷驱动程序支持的安装。|仅限 Docker。 S3、GCS、R2、Azure Blob、Box 可以通过`rclone`挂载； S3和GCS也可以通过`mountpoint`安装。|

</div>

## 支持的托管平台

当您需要托管环境时，通常会保留相同的 `SandboxAgent` 定义，并且只有沙箱客户端在 [`SandboxRunConfig`][agents.run_config.SandboxRunConfig] 中发生更改。

如果您使用已发布的 SDK 而不是此存储库签出，请通过匹配的额外包安装沙箱客户端依赖项。

有关特定于提供商的设置说明和签入扩展示例的链接，请参阅 [examples/sandbox/extensions/README.md](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/README.md>)。

<div class="sandbox-nowrap-first-column-table" markdown="1">

|客户|安装|示例|
| --- | --- | --- |
| `BlaxelSandboxClient` | `openai-agents[blaxel]` |[Blaxel 跑步者](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/blaxel_runner.py>)|
| `CloudflareSandboxClient` | `openai-agents[cloudflare]` |[Cloudflare 跑步者](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/cloudflare_runner.py>)|
| `DaytonaSandboxClient` | `openai-agents[daytona]` |[代托纳跑步者](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/daytona/daytona_runner.py>)|
| `E2BSandboxClient` | `openai-agents[e2b]` |[E2B 跑步者](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/e2b_runner.py>)|
| `ModalSandboxClient` | `openai-agents[modal]` |[莫代尔跑鞋](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/modal_runner.py>)|
| `RunloopSandboxClient` | `openai-agents[runloop]` |[Runloop 运行器](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/runloop/runner.py>)|
| `VercelSandboxClient` | `openai-agents[vercel]` |[Vercel 跑步者](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/vercel_runner.py>)|

</div>

### 大小模态沙箱

使用 `ModalSandboxClientOptions.cpu` 和 `ModalSandboxClientOptions.memory` 为新的 Modal 沙箱请求资源。单个值请求该金额。两项 `(request, limit)` 元组使用第一项作为请求，第二项作为限制。内存值位于 MiB 中。

```python
from agents.extensions.sandbox import ModalSandboxClientOptions

options = ModalSandboxClientOptions(
    app_name="agents-sandbox",
    cpu=(1.0, 4.0),
    memory=(2048, 8192),
)
```

将 `cpu`、`memory` 或两者都保留为 `None`，以便对每个省略的资源使用 Modal 的默认值。所选值保留在沙箱会话状态中，因此替换沙箱使用相同的资源配置。

托管沙箱客户端公开特定于提供商的安装策略。选择最适合您的存储提供商的后端和安装策略：

<div class="sandbox-nowrap-first-column-table" markdown="1">

|后端|安装注意事项|
| --- | --- |
|码头工人|支持`S3Mount`、`GCSMount`、`R2Mount`、`AzureBlobMount`、`BoxMount`和`S3FilesMount`本地策略，例如`InContainerMountStrategy`和`DockerVolumeMountStrategy`。|
| `ModalSandboxClient` |支持使用 `ModalCloudBucketMountStrategy` 与 `S3Mount`、`R2Mount` 和 HMAC- 认证的 `GCSMount` 进行云存储桶挂载。您可以使用内联凭据或命名的模态密钥。|
| `CloudflareSandboxClient` |支持使用 `CloudflareBucketMountStrategy` 与 `S3Mount`、`R2Mount` 和 HMAC- 认证的 `GCSMount` 来安装铲斗。|
| `BlaxelSandboxClient` |通过将 `BlaxelCloudBucketMountStrategy` 与 `S3Mount`、`R2Mount` 或 `GCSMount` 条目配对来支持云存储桶挂载。还支持带有 `BlaxelDriveMount` 和 `BlaxelDriveMountStrategy` 的持久 Blaxel 驱动器，两者均可从 `agents.extensions.sandbox.blaxel` 获得。|
| `DaytonaSandboxClient` |支持使用【`DaytonaCloudBucketMountStrategy`】通过【`rclone`】挂载云存储；与 `S3Mount`、`GCSMount`、`R2Mount`、`AzureBlobMount` 和 `BoxMount` 一起使用。|
| `E2BSandboxClient` |支持使用【`E2BCloudBucketMountStrategy`】通过【`rclone`】挂载云存储；与 `S3Mount`、`GCSMount`、`R2Mount`、`AzureBlobMount` 和 `BoxMount` 一起使用。|
| `RunloopSandboxClient` |支持使用【`RunloopCloudBucketMountStrategy`】通过【`rclone`】挂载云存储；与 `S3Mount`、`GCSMount`、`R2Mount`、`AzureBlobMount` 和 `BoxMount` 一起使用。|
| `VercelSandboxClient` |通过将 `VercelCloudBucketMountStrategy` 与 `S3Mount` 条目配对，支持仅创建时 S3 和 S3- 兼容的存储桶安装；无法恢复已安装的会话，并且内联凭据需要 `allow_s3_credential_exposure=True`。|

</div>

挂载表描述了每个后端可以执行哪些存储类型。复选标记不会绕过在模型控制的沙箱内运行的挂载帮助程序的凭据边界，并且并不意味着每个策略都可以在没有凭据的情况下运行。仅当所选助手可以在没有受保护权限的情况下操作时，Agents SDK 才会接受容器内安装而无需确认。它会在启动沙箱或挂载帮助程序之前拒绝需要受保护权限的挂载，除非受信任的应用程序代码明确确认确切挂载路径的暴露。

无凭据 `rclone` 装载仅限于 S3、GCS、R2 和 Azure Blob。容器内 Box 安装需要非交互式身份验证源以及与该源匹配的确认。 `FuseMountPattern` 需要广泛确认，因为 `blobfuse2` 会发现环境 Azure 权限，即使未配置内联凭据也是如此。 `S3FilesMountPattern` 同样需要广泛的确认，因为 `mount.s3files` 使用环境 IAM 权限。当 Docker 作为后端时，这些要求也适用；下面的复选标记表示 Docker 可以在满足适用的权限边界后执行挂载。

对于名为`"data"`的挂载条目，保留与配置的权限匹配的确认返回的复制的`Manifest`：

```python
# Mount-scoped values such as inline access keys.
manifest = manifest.with_in_container_mount_credential_exposure_acknowledged("data")

# Broader authority such as managed or workload identity and external credential files.
manifest = manifest.with_in_container_mount_broad_credential_exposure_acknowledged("data")
```

传递需要确认的每个确切安装路径。使用两个权限类别的安装需要两个确认。确认仅在运行时有效，不会被序列化，并且允许帮助程序接收凭证，而不会将凭证的使用限制在已安装的路径上。首选外部或提供商本机策略（如果可用），否则使用沙箱范围内的、短期的、最低权限的凭据。

`VercelSandboxClientOptions(allow_s3_credential_exposure=True)` 仍然是使用内联挂载范围凭据创建时 Vercel S3 挂载的兼容性选项。它不授予广泛的证书权威。

下表总结了每个后端可以直接挂载的远程存储条目。

<div class="sandbox-nowrap-first-column-table" markdown="1">

|后端| AWS S3 |Cloudflare R2| GCS |Azure Blob 存储|盒子|S3 文件|
| --- | --- | --- | --- | --- | --- | --- |
|码头工人| ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| `ModalSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `CloudflareSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `BlaxelSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `DaytonaSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `E2BSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `RunloopSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `VercelSandboxClient` | ✓ | - | - | - | - | - |

</div>

有关更多可运行示例，请浏览 [examples/sandbox/](<https://github.com/openai/openai-agents-python/tree/main/examples/sandbox>) 以了解本地、编码、内存、切换和 agent 组合模式，并浏览 [examples/sandbox/extensions/](<https://github.com/openai/openai-agents-python/tree/main/examples/sandbox/extensions>) 以了解托管沙箱客户端。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sandbox clients

Use this page to choose where sandbox work should run. In most cases, the `SandboxAgent` definition stays the same while the sandbox client and client-specific options change in [`SandboxRunConfig`][agents.run_config.SandboxRunConfig].

## Decision guide

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Goal | Start with | Why |
| --- | --- | --- |
| Trusted local development on macOS or Linux | `UnixLocalSandboxClient` | No extra install; commands run as local host processes. |
| Basic container isolation | `DockerSandboxClient` | Runs work inside Docker with a specific image. |
| Hosted execution or production-style isolation | A hosted sandbox client | Moves the workspace boundary to a provider-managed environment. |

</div>

!!! warning "Unix-local execution limits"

    `UnixLocalSandboxClient` runs commands as local host processes. On Linux, this backend adds no OS-level confinement: commands can access files and network resources permitted by the host process and any external isolation. A workspace directory, `HOME`, or `cwd` does not restrict that access.

    On macOS, this backend uses `sandbox-exec` to apply filesystem restrictions. Those restrictions do not provide network isolation or the same boundary as a container.

    Use Unix-local for trusted local development or within an externally isolated environment. For untrusted commands, including commands influenced by untrusted inputs, choose an appropriately configured Docker or hosted sandbox, or provide external isolation. Review the selected environment's permissions, mounts, credentials, and network access for your workload.

## Local clients

For most users, start with one of these two sandbox clients:

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Client | Install | Choose it when | Example |
| --- | --- | --- | --- |
| `UnixLocalSandboxClient` | none | Trusted local development on macOS or Linux, or execution within external isolation. | [Unix-local starter](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/unix_local_runner.py>) |
| `DockerSandboxClient` | `openai-agents[docker]` | You want container isolation or a specific image to reproduce a target environment locally. | [Docker starter](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docker/docker_runner.py>) |

</div>

Unix-local provides a local workspace without requiring a container. Choose Docker or a hosted provider when you need an isolation boundary supplied by that backend or an image that matches another environment.

`SandboxPathGrant.host_path` is Docker-only and maps a host path to a different POSIX path inside the container. Unix-local supports only same-path grants. See [Manifest path grants](<OpenAI 沙箱 Agent 核心概念.md#manifest>) for details.

### Limit host environment inheritance for Unix-local sessions

By default, `UnixLocalSandboxClient` starts every command environment from the complete host process environment. Set `inherit_host_environment=False` to pass only a conservative allowlist of host variables instead:

```python
from agents.sandbox.sandboxes.unix_local import UnixLocalSandboxClient

client = UnixLocalSandboxClient(
    inherit_host_environment=False,
    host_environment_allowlist={"PATH", "LANG", "SSL_CERT_FILE"},
)
```

When `inherit_host_environment=False` and `host_environment_allowlist` is omitted, the SDK allows `PATH`, `LANG`, `LC_ALL`, `LC_COLLATE`, `LC_CTYPE`, `LC_MESSAGES`, `LC_MONETARY`, `LC_NUMERIC`, `LC_TIME`, `TZ`, `TERM`, `TMPDIR`, `SSL_CERT_FILE`, `SSL_CERT_DIR`, `REQUESTS_CA_BUNDLE`, `NODE_EXTRA_CA_CERTS`, `UV_PYTHON`, `NO_COLOR`, `FORCE_COLOR`, and `CI`. Pass a custom collection to replace that default allowlist. A custom allowlist requires `inherit_host_environment=False`.

Values from `Manifest.environment` are applied after host filtering and override inherited values. Unix-local commands always receive the workspace root as `HOME`. The inheritance policy belongs to the current client rather than serialized session state, so `create(...)` and `resume(...)` apply the policy of the client that performs that operation.

This option filters inherited environment variables only; it does not add OS-level confinement. The Unix-local execution limits above still apply.

To switch from Unix-local to Docker, keep the agent definition the same and change only the run config:

```python
from docker import from_env as docker_from_env

from agents.run import RunConfig
from agents.sandbox import SandboxRunConfig
from agents.sandbox.sandboxes.docker import DockerSandboxClient, DockerSandboxClientOptions

run_config = RunConfig(
    sandbox=SandboxRunConfig(
        client=DockerSandboxClient(docker_from_env()),
        options=DockerSandboxClientOptions(image="python:3.14-slim"),
    ),
)
```

Use this when you want container isolation or want the sandbox image to match the image used in another environment. See [examples/sandbox/docker/docker_runner.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docker/docker_runner.py>).

### Disable Docker networking

Set `network_mode="none"` when a Docker sandbox must not have network access:

```python
options = DockerSandboxClientOptions(
    image="python:3.14-slim",
    network_mode="none",
)
```

The only supported explicit network mode is `"none"`; omit `network_mode` to preserve Docker's default behavior. A network-disabled sandbox cannot expose ports, so combining `network_mode="none"` with a non-empty `exposed_ports` tuple fails during option validation. The setting is stored in sandbox session state and reapplied if the SDK must create a replacement container while resuming that state.

### Label Docker containers

Set `labels` when an application needs to identify or manage the Docker containers created for sandbox sessions:

```python
options = DockerSandboxClientOptions(
    image="python:3.14-slim",
    labels={
        "com.example.owner": "agents-sdk",
        "com.example.environment": "development",
    },
)
```

The SDK passes these key-value pairs to Docker when it creates the container and stores them in [`DockerSandboxSessionState`][agents.sandbox.sandboxes.docker.DockerSandboxSessionState]. When a resumed session reconnects to an existing container, the SDK verifies that every persisted label still has the expected value and raises `ValueError` if the labels do not match. When the SDK creates a replacement container from the saved state, it reapplies the persisted labels.

## Mounts and remote storage

Mount entries describe what storage to expose; mount strategies describe how a sandbox backend attaches that storage. Import the built-in mount entries and generic strategies from `agents.sandbox.entries`. Hosted-provider strategies are available from `agents.extensions.sandbox` or the provider-specific extension package.

Common mount options:

- `mount_path`: where the storage appears in the sandbox. Relative paths are resolved under the manifest root; absolute paths are used as-is.
- `read_only`: defaults to `True`. Set `False` only when the sandbox should write back to the mounted storage.
- `mount_strategy`: required. Use a strategy that matches both the mount entry and the sandbox backend.

Mounts are treated as ephemeral workspace entries. Snapshot and persistence flows detach or skip mounted paths instead of copying mounted remote storage into the saved workspace.

Generic local/container strategies:

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Strategy or pattern | Use it when | Notes |
| --- | --- | --- |
| `InContainerMountStrategy(pattern=RcloneMountPattern(...))` | The sandbox image can run `rclone`. | Supports S3, GCS, R2, Azure Blob, and Box. `RcloneMountPattern` can run in `fuse` mode or `nfs` mode. |
| `InContainerMountStrategy(pattern=MountpointMountPattern(...))` | The image has `mount-s3` and you want Mountpoint-style S3 or S3-compatible access. | Supports `S3Mount` and `GCSMount`. |
| `InContainerMountStrategy(pattern=FuseMountPattern(...))` | The image has `blobfuse2` and FUSE support. | Supports `AzureBlobMount`. |
| `InContainerMountStrategy(pattern=S3FilesMountPattern(...))` | The image has `mount.s3files` and can reach an existing S3 Files mount target. | Supports `S3FilesMount`. |
| `DockerVolumeMountStrategy(driver=...)` | Docker should attach a volume-driver-backed mount before the container starts. | Docker-only. S3, GCS, R2, Azure Blob, and Box can be mounted through `rclone`; S3 and GCS can also be mounted through `mountpoint`. |

</div>

## Supported hosted platforms

When you need a hosted environment, the same `SandboxAgent` definition usually carries over and only the sandbox client changes in [`SandboxRunConfig`][agents.run_config.SandboxRunConfig].

If you are using the published SDK instead of this repository checkout, install sandbox-client dependencies through the matching package extra.

For provider-specific setup notes and links for the checked-in extension examples, see [examples/sandbox/extensions/README.md](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/README.md>).

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Client | Install | Example |
| --- | --- | --- |
| `BlaxelSandboxClient` | `openai-agents[blaxel]` | [Blaxel runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/blaxel_runner.py>) |
| `CloudflareSandboxClient` | `openai-agents[cloudflare]` | [Cloudflare runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/cloudflare_runner.py>) |
| `DaytonaSandboxClient` | `openai-agents[daytona]` | [Daytona runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/daytona/daytona_runner.py>) |
| `E2BSandboxClient` | `openai-agents[e2b]` | [E2B runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/e2b_runner.py>) |
| `ModalSandboxClient` | `openai-agents[modal]` | [Modal runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/modal_runner.py>) |
| `RunloopSandboxClient` | `openai-agents[runloop]` | [Runloop runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/runloop/runner.py>) |
| `VercelSandboxClient` | `openai-agents[vercel]` | [Vercel runner](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/vercel_runner.py>) |

</div>

### Size Modal sandboxes

Use `ModalSandboxClientOptions.cpu` and `ModalSandboxClientOptions.memory` to request resources for a new Modal sandbox. A single value requests that amount. A two-item `(request, limit)` tuple uses the first item as the request and the second item as the limit. Memory values are in MiB.

```python
from agents.extensions.sandbox import ModalSandboxClientOptions

options = ModalSandboxClientOptions(
    app_name="agents-sandbox",
    cpu=(1.0, 4.0),
    memory=(2048, 8192),
)
```

Leave `cpu`, `memory`, or both as `None` to use Modal's default for each omitted resource. The selected values are preserved in sandbox session state so replacement sandboxes use the same resource configuration.

Hosted sandbox clients expose provider-specific mount strategies. Choose the backend and mount strategy that best fit your storage provider:

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Backend | Mount notes |
| --- | --- |
| Docker | Supports `S3Mount`, `GCSMount`, `R2Mount`, `AzureBlobMount`, `BoxMount`, and `S3FilesMount` with local strategies such as `InContainerMountStrategy` and `DockerVolumeMountStrategy`. |
| `ModalSandboxClient` | Supports cloud bucket mounts by using `ModalCloudBucketMountStrategy` with `S3Mount`, `R2Mount`, and HMAC-authenticated `GCSMount`. You can use inline credentials or a named Modal Secret. |
| `CloudflareSandboxClient` | Supports bucket mounts by using `CloudflareBucketMountStrategy` with `S3Mount`, `R2Mount`, and HMAC-authenticated `GCSMount`. |
| `BlaxelSandboxClient` | Supports cloud bucket mounts by pairing `BlaxelCloudBucketMountStrategy` with an `S3Mount`, `R2Mount`, or `GCSMount` entry. Also supports persistent Blaxel Drives with `BlaxelDriveMount` and `BlaxelDriveMountStrategy`, both available from `agents.extensions.sandbox.blaxel`. |
| `DaytonaSandboxClient` | Supports mounting cloud storage through `rclone` by using `DaytonaCloudBucketMountStrategy`; use it with `S3Mount`, `GCSMount`, `R2Mount`, `AzureBlobMount`, and `BoxMount`. |
| `E2BSandboxClient` | Supports mounting cloud storage through `rclone` by using `E2BCloudBucketMountStrategy`; use it with `S3Mount`, `GCSMount`, `R2Mount`, `AzureBlobMount`, and `BoxMount`. |
| `RunloopSandboxClient` | Supports mounting cloud storage through `rclone` by using `RunloopCloudBucketMountStrategy`; use it with `S3Mount`, `GCSMount`, `R2Mount`, `AzureBlobMount`, and `BoxMount`. |
| `VercelSandboxClient` | Supports create-time-only S3 and S3-compatible bucket mounts by pairing `VercelCloudBucketMountStrategy` with an `S3Mount` entry; mounted sessions cannot be resumed, and inline credentials require `allow_s3_credential_exposure=True`. |

</div>

The mount tables describe which storage types each backend can execute. A check mark does not bypass the credential boundary for a mount helper that runs inside a model-controlled sandbox, and it does not mean that every strategy can operate without credentials. The Agents SDK accepts an in-container mount without an acknowledgement only when the selected helper can operate without protected authority. It rejects a mount that requires protected authority before starting the sandbox or mount helper unless trusted application code explicitly acknowledges the exposure for the exact mount path.

Credentialless `rclone` mounts are limited to S3, GCS, R2, and Azure Blob. An in-container Box mount requires a non-interactive authentication source and the acknowledgement that matches that source. `FuseMountPattern` requires broad acknowledgement because `blobfuse2` discovers ambient Azure authority, even when no inline credential is configured. `S3FilesMountPattern` likewise requires broad acknowledgement because `mount.s3files` uses ambient IAM authority. These requirements also apply when Docker is the backend; the check marks below indicate that Docker can execute the mount after the applicable authority boundary is satisfied.

For a mount entry named `"data"`, retain the copied `Manifest` returned by the acknowledgement that matches the configured authority:

```python
# Mount-scoped values such as inline access keys.
manifest = manifest.with_in_container_mount_credential_exposure_acknowledged("data")

# Broader authority such as managed or workload identity and external credential files.
manifest = manifest.with_in_container_mount_broad_credential_exposure_acknowledged("data")
```

Pass every exact mount path that needs the acknowledgement. A mount that uses both authority classes requires both acknowledgements. The acknowledgements are runtime-only, are not serialized, and permit the helper to receive credentials without confining credential use to the mounted path. Prefer an external or provider-native strategy when available, and otherwise use sandbox-scoped, short-lived, least-privilege credentials.

`VercelSandboxClientOptions(allow_s3_credential_exposure=True)` remains a compatibility option for create-time Vercel S3 mounts with inline mount-scoped credentials. It does not authorize broad credential authority.

The table below summarizes which remote storage entries each backend can mount directly.

<div class="sandbox-nowrap-first-column-table" markdown="1">

| Backend | AWS S3 | Cloudflare R2 | GCS | Azure Blob Storage | Box | S3 Files |
| --- | --- | --- | --- | --- | --- | --- |
| Docker | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| `ModalSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `CloudflareSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `BlaxelSandboxClient` | ✓ | ✓ | ✓ | - | - | - |
| `DaytonaSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `E2BSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `RunloopSandboxClient` | ✓ | ✓ | ✓ | ✓ | ✓ | - |
| `VercelSandboxClient` | ✓ | - | - | - | - | - |

</div>

For more runnable examples, browse [examples/sandbox/](<https://github.com/openai/openai-agents-python/tree/main/examples/sandbox>) for local, coding, memory, handoff, and agent-composition patterns, and [examples/sandbox/extensions/](<https://github.com/openai/openai-agents-python/tree/main/examples/sandbox/extensions>) for hosted sandbox clients.


</details>
