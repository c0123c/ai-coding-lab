# OpenAI 沙箱 Agent 样例导航

按小型 API、云端后端、教程和工作流组织示例，并说明本地执行边界；主要用于找到适合的实现。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-17](<https://github.com/openai/openai-agents-python/commit/9f6f6b10155a5ee4d9affb33a2bfa7b5413787c8>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/OpenAI 沙箱 Agent 样例导航.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 沙盒示例

这些示例展示了如何使用托管工作区运行 agents。当您需要最小的表面积时，请从小型 API 示例开始；当您需要指导沙盒教程的共享布局时，请使用教程支架。

Unix 本地示例作为主机进程运行命令。在 Linux 上，`UnixLocalSandboxClient` 不添加 OS- 级别限制；在macOS上，它通过`sandbox-exec`应用文件系统限制，而不提供网络隔离。使用这些示例进行可信的本地开发或外部隔离。对于不受信任的命令，包括受不受信任输入影响的命令，请选择适当配置的 Docker 或托管后端，或提供外部隔离。单独的 Unix 本地工作区或会话不会建立 OS 隔离边界。

大多数示例通过 `Runner` 调用模型，因此请在存储库根 `.env` 文件、示例的 `.env` 文件（如果有）或 shell 环境中设置 `OPENAI_API_KEY`。

`sandbox_agent_with_tools.py` 启动存储库的 MCP v2 参考服务器，旨在与锁定的开发环境一起运行。 Agents SDK 客户端本身支持 MCP v1 和 v2。

## 小 API 示例

|示例|运行|它显示了什么|
| --- | --- | --- |
| [`basic.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/basic.py>) | `uv run python examples/sandbox/basic.py` |从清单创建沙箱会话，运行 `SandboxAgent`，并流式传输结果。|
| [`handoffs.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/handoffs.py>) | `uv run python examples/sandbox/handoffs.py` |使用沙箱支持的切换 agents。|
| [`sandbox_agent_capabilities.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_capabilities.py>) | `uv run python examples/sandbox/sandbox_agent_capabilities.py` |配置具有工作区功能的沙箱 agent。|
| [`sandbox_agent_with_tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_with_tools.py>) | `uv run python examples/sandbox/sandbox_agent_with_tools.py` |将沙箱功能与主机定义的工具相结合。|
| [`sandbox_agents_as_tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agents_as_tools.py>) | `uv run python examples/sandbox/sandbox_agents_as_tools.py` |将沙箱 agents 公开为另一个 agent 的工具。|
| [`sandbox_agent_with_remote_snapshot.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_with_remote_snapshot.py>) | `uv run python examples/sandbox/sandbox_agent_with_remote_snapshot.py` |从远程沙箱快照启动。|
| [`memory.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory.py>) | `uv run python examples/sandbox/memory.py` |在快照恢复中运行一个沙箱 agent 两次，以便它可以读取和写入自己的内存。|
| [`memory_s3.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory_s3.py>) | `source ~/.s3.env && uv run python examples/sandbox/memory_s3.py` |使用 S3- 支持的内存存储在两个新的 Docker 沙箱中运行沙箱内存。|
| [`memory_multi_agent_multiturn.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory_multi_agent_multiturn.py>) | `uv run python examples/sandbox/memory_multi_agent_multiturn.py` |显示共享一个沙箱工作区的两个 agents 的单独内存布局。|
| [`shared_session_workdirs.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/shared_session_workdirs.py>) | `uv run python examples/sandbox/shared_session_workdirs.py` |在受信任的 agents 之间共享一个实时沙箱，同时 Shell、`view_image` 和 `apply_patch` 解析每次运行的 `cwd` 的相对路径。这不是禁闭，而是禁闭。不受信任的工作负载需要提供所需隔离的后端和配置。|
| [`unix_local_pty.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/unix_local_pty.py>) | `uv run python examples/sandbox/unix_local_pty.py` |在 Unix 本地沙箱中使用交互式伪终端。|
| [`unix_local_runner.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/unix_local_runner.py>) | `uv run python examples/sandbox/unix_local_runner.py` |直接针对 Unix 本地沙箱后端运行。|

## 云后端示例

云提供商示例位于 [`extensions/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/extensions>) 下。它们涵盖 E2B、Modal 和 Daytona 沙箱后端，并且除了 `OPENAI_API_KEY` 之外还需要提供商特定的凭据。

## 教程脚手架

[`tutorials/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials>) 包含共享帮助程序代码、Docker 映像以及指导沙箱教程的文件夹约定。教程文件夹是在单独的重点更改中添加的。

## 教程

|示例|它的作用|
| --- | --- |
| [`sandbox_resume`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/sandbox_resume>) |编辑工作区应用程序并重用沙箱快照。|
| [`dataroom_qa`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/dataroom_qa>) |通过安装的数据室回答问题，并提供有源支持的响应。|
| [`dataroom_metric_extract`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/dataroom_metric_extract>) |将结构化财务指标提取到 CSV/JSONL.|
| [`repo_code_review`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/repo_code_review>) |审查示例存储库并编写发现、报告和补丁工件。|
| [`vision_website_clone`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/vision_website_clone>) |使用视觉和浏览器审查循环来克隆参考静态网站。|

## 工作流程示例

|示例|它的作用|
| --- | --- |
| [`healthcare_support`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support>) |使用标准编排器、沙箱策略 agent、内存和人工审批运行综合医疗保健支持工作流程。|

## 共享文件

- [`docker/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/docker>) 包含 Docker 特定的帮助程序示例。
- [`misc/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/misc>) 包含可重用的支持代码和几个沙箱示例使用的小型参考工具。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sandbox examples

These examples show how to run agents with a managed workspace. Start with the small API examples when you want the smallest surface area, or use the tutorial scaffold when you want the shared layout for guided sandbox tutorials.

Unix-local examples run commands as host processes. On Linux, `UnixLocalSandboxClient` adds no OS-level confinement; on macOS, it applies filesystem restrictions through `sandbox-exec` without providing network isolation. Use these examples for trusted local development or within external isolation. For untrusted commands, including commands influenced by untrusted inputs, choose an appropriately configured Docker or hosted backend, or provide external isolation. Separate Unix-local workspaces or sessions do not establish an OS isolation boundary.

Most examples call a model through `Runner`, so set `OPENAI_API_KEY` in the repository-root `.env` file, in the example's `.env` file when it has one, or in your shell environment.

`sandbox_agent_with_tools.py` starts the repository's MCP v2 reference server and is intended to run with the locked development environment. The Agents SDK client itself supports both MCP v1 and v2.

## Small API examples

| Example | Run | What it shows |
| --- | --- | --- |
| [`basic.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/basic.py>) | `uv run python examples/sandbox/basic.py` | Creates a sandbox session from a manifest, runs a `SandboxAgent`, and streams the result. |
| [`handoffs.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/handoffs.py>) | `uv run python examples/sandbox/handoffs.py` | Uses handoffs with sandbox-backed agents. |
| [`sandbox_agent_capabilities.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_capabilities.py>) | `uv run python examples/sandbox/sandbox_agent_capabilities.py` | Configures a sandbox agent with workspace capabilities. |
| [`sandbox_agent_with_tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_with_tools.py>) | `uv run python examples/sandbox/sandbox_agent_with_tools.py` | Combines sandbox capabilities with host-defined tools. |
| [`sandbox_agents_as_tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agents_as_tools.py>) | `uv run python examples/sandbox/sandbox_agents_as_tools.py` | Exposes sandbox agents as tools for another agent. |
| [`sandbox_agent_with_remote_snapshot.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/sandbox_agent_with_remote_snapshot.py>) | `uv run python examples/sandbox/sandbox_agent_with_remote_snapshot.py` | Starts from a remote sandbox snapshot. |
| [`memory.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory.py>) | `uv run python examples/sandbox/memory.py` | Runs one sandbox agent twice across a snapshot resume so it can read and write its own memory. |
| [`memory_s3.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory_s3.py>) | `source ~/.s3.env && uv run python examples/sandbox/memory_s3.py` | Runs sandbox memory across two fresh Docker sandboxes with S3-backed memory storage. |
| [`memory_multi_agent_multiturn.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/memory_multi_agent_multiturn.py>) | `uv run python examples/sandbox/memory_multi_agent_multiturn.py` | Shows separate memory layouts for two agents sharing one sandbox workspace. |
| [`shared_session_workdirs.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/shared_session_workdirs.py>) | `uv run python examples/sandbox/shared_session_workdirs.py` | Shares one live sandbox between trusted agents while Shell, `view_image`, and `apply_patch` resolve relative paths from each run's `cwd`. This is not confinement; untrusted workloads need a backend and configuration that provide the required isolation. |
| [`unix_local_pty.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/unix_local_pty.py>) | `uv run python examples/sandbox/unix_local_pty.py` | Exercises an interactive pseudo-terminal in a Unix-local sandbox. |
| [`unix_local_runner.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/unix_local_runner.py>) | `uv run python examples/sandbox/unix_local_runner.py` | Runs against the Unix-local sandbox backend directly. |

## Cloud backend examples

Cloud-provider examples live under [`extensions/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/extensions>). They cover E2B, Modal, and Daytona sandbox backends and require provider-specific credentials in addition to `OPENAI_API_KEY`.

## Tutorial scaffold

[`tutorials/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials>) contains the shared helper code, Docker image, and folder conventions for guided sandbox tutorials. Tutorial folders are added in separate focused changes.

## Tutorials

| Example | What it does |
| --- | --- |
| [`sandbox_resume`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/sandbox_resume>) | Edits a workspace app and reuses a sandbox snapshot. |
| [`dataroom_qa`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/dataroom_qa>) | Answers questions over a mounted dataroom with source-backed responses. |
| [`dataroom_metric_extract`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/dataroom_metric_extract>) | Extracts structured financial metrics to CSV/JSONL. |
| [`repo_code_review`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/repo_code_review>) | Reviews a sample repo and writes finding, report, and patch artifacts. |
| [`vision_website_clone`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/tutorials/vision_website_clone>) | Uses vision and a browser-review loop to clone a reference static website. |

## Workflow examples

| Example | What it does |
| --- | --- |
| [`healthcare_support`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support>) | Runs a synthetic healthcare support workflow with a standard orchestrator, sandbox policy agent, memory, and human approvals. |

## Shared files

- [`docker/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/docker>) contains Docker-specific helper examples.
- [`misc/`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/misc>) contains reusable support code and tiny reference tools used by several sandbox examples.


</details>
