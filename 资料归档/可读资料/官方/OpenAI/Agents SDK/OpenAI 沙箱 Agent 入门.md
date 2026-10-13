# OpenAI 沙箱 Agent 入门

介绍安装、前提及创建本地沙箱 Agent，说明持久工作区、文件操作、Shell 和状态恢复的基础组合。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-30](<https://github.com/openai/openai-agents-python/commit/9d2b318dd034e5c0333c88e60ed6c704cbbbd04b>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/docs/sandbox_agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/docs/sandbox_agents.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 快速入门

现代 agents 在可以操作文件系统中的真实文件时效果最佳。 ** Agents SDK 中的沙箱 Agents** 为模型提供了一个持久工作区，可以在其中搜索大型文档集、编辑文件、运行命令、生成工件以及从保存的沙箱状态中选择工作备份。

SDK 为您提供执行工具，无需您自己将文件暂存、文件系统工具、shell 访问、沙箱生命周期、快照和特定于提供程序的粘合连接在一起。您保留正常的 `Agent` 和 `Runner` 流程，然后添加用于工作区的 `Manifest`、沙箱本机工具的功能以及用于运行工作的位置的 `SandboxRunConfig`。

## 先决条件

- Python 3.10 或更高
- 基本熟悉 OpenAI Agents SDK
- 沙盒客户端。对于受信任的本地开发，请从 `UnixLocalSandboxClient` 开始。

## 安装

如果您尚未安装SDK：

```bash
pip install openai-agents
```

对于 Docker 支持的沙箱：

```bash
pip install "openai-agents[docker]"
```

## 创建本地沙箱agent

此示例在 `repo/` 下暂存一个本地存储库，延迟加载本地技能，并让运行程序为运行创建一个 Unix 本地沙箱会话。

！！！警告“本地命令使用主机权限”

在 Linux 上，`UnixLocalSandboxClient` 不向命令添加 OS- 级别限制。在macOS上，它通过`sandbox-exec`应用文件系统限制，但不提供网络隔离。将此示例用于受信任的本地开发或在外部隔离的环境中。对于不受信任的命令，包括受不受信任输入影响的命令，请选择适当配置的 Docker 或托管沙箱，或提供外部隔离。请参阅 [Unix 本地执行限制 ](<选择 OpenAI 沙箱客户端.md#决策指南>)。

```python
import asyncio
from pathlib import Path

from agents import Runner
from agents.run import RunConfig
from agents.sandbox import Manifest, SandboxAgent, SandboxRunConfig
from agents.sandbox.capabilities import Capabilities, LocalDirLazySkillSource, Skills
from agents.sandbox.entries import LocalDir
from agents.sandbox.sandboxes.unix_local import UnixLocalSandboxClient

EXAMPLE_DIR = Path(__file__).resolve().parent
HOST_REPO_DIR = EXAMPLE_DIR / "repo"
HOST_SKILLS_DIR = EXAMPLE_DIR / "skills"


def build_agent(model: str) -> SandboxAgent[None]:
    return SandboxAgent(
        name="Sandbox engineer",
        model=model,
        instructions=(
            "Read `repo/task.md` before editing files. Stay grounded in the repository, preserve "
            "existing behavior, and mention the exact verification command you ran. "
            "If you edit files with apply_patch, paths are relative to the sandbox workspace root."
        ),
        default_manifest=Manifest(
            entries={
                "repo": LocalDir(src=HOST_REPO_DIR),
            }
        ),
        capabilities=Capabilities.default()
        + [
            Skills(
                lazy_from=LocalDirLazySkillSource(
                    # This is a host path read by the SDK process.
                    # Requested skills are copied into `skills_path` in the sandbox.
                    source=LocalDir(src=HOST_SKILLS_DIR),
                )
            ),
        ],
    )


async def main() -> None:
    result = await Runner.run(
        build_agent("gpt-5.6-sol"),
        "Open `repo/task.md`, fix the issue, run the targeted test, and summarize the change.",
        run_config=RunConfig(
            sandbox=SandboxRunConfig(client=UnixLocalSandboxClient()),
            workflow_name="Sandbox coding example",
        ),
    )
    print(result.final_output)


if __name__ == "__main__":
    asyncio.run(main())
```

参见[examples/sandbox/docs/coding_task.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docs/coding_task.py>)。它使用一个基于 shell 的小型存储库，因此可以在 Unix 本地运行中确定性地验证该示例。

## 关键选择

一旦基本运行成功，大多数人下一步的选择是：

- `default_manifest`：新沙箱会话的文件、存储库、目录和挂载
- `instructions`：应跨提示应用的简短工作流程规则
- `base_instructions`：用于替换SDK沙箱提示的高级逃生舱口
- `capabilities`：沙箱原生工具，例如文件系统editing/image检查、shell、技能、内存和SDK的压缩机制
- `run_as`：执行面向模型的工具的沙箱用户帐户
- `SandboxRunConfig.client`：沙箱后端
- `SandboxRunConfig.session`、`session_state` 或 `snapshot`：稍后如何运行重新连接到之前的工作

## 下一步去哪里

- [概念](<OpenAI 沙箱 Agent 核心概念.md>)：了解清单、功能、权限、快照、运行配置和组合模式。
- [沙箱客户端](<选择 OpenAI 沙箱客户端.md>)：选择Unix本地、Docker、托管提供程序和安装策略。
- [Agent内存](<OpenAI 沙箱 Agent 的经验记忆.md>)：保留和重用以前沙箱运行的经验教训。

如果 shell 访问只是您偶尔使用的一种工具，请从 [ 工具指南 ](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tools.md>) 中的托管 shell 开始。当工作区隔离、沙箱客户端选择或沙箱会话恢复行为是设计的一部分时，请使用沙箱 agents。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Quickstart

Modern agents work best when they can operate on real files in a filesystem. **Sandbox Agents** in the Agents SDK give the model a persistent workspace where it can search large document sets, edit files, run commands, generate artifacts, and pick work back up from saved sandbox state.

The SDK gives you that execution harness without making you wire together file staging, filesystem tools, shell access, sandbox lifecycle, snapshots, and provider-specific glue yourself. You keep the normal `Agent` and `Runner` flow, then add a `Manifest` for the workspace, capabilities for sandbox-native tools, and `SandboxRunConfig` for where the work runs.

## Prerequisites

- Python 3.10 or higher
- Basic familiarity with the OpenAI Agents SDK
- A sandbox client. For trusted local development, start with `UnixLocalSandboxClient`.

## Installation

If you have not already installed the SDK:

```bash
pip install openai-agents
```

For Docker-backed sandboxes:

```bash
pip install "openai-agents[docker]"
```

## Create a local sandbox agent

This example stages a local repo under `repo/`, loads local skills lazily, and has the runner create a Unix-local sandbox session for the run.

!!! warning "Local commands use host permissions"

    On Linux, `UnixLocalSandboxClient` adds no OS-level confinement to commands. On macOS, it applies filesystem restrictions through `sandbox-exec`, but does not provide network isolation. Use this example for trusted local development or within an externally isolated environment. For untrusted commands, including commands influenced by untrusted inputs, choose an appropriately configured Docker or hosted sandbox, or provide external isolation. See [Unix-local execution limits](<选择 OpenAI 沙箱客户端.md#decision-guide>).

```python
import asyncio
from pathlib import Path

from agents import Runner
from agents.run import RunConfig
from agents.sandbox import Manifest, SandboxAgent, SandboxRunConfig
from agents.sandbox.capabilities import Capabilities, LocalDirLazySkillSource, Skills
from agents.sandbox.entries import LocalDir
from agents.sandbox.sandboxes.unix_local import UnixLocalSandboxClient

EXAMPLE_DIR = Path(__file__).resolve().parent
HOST_REPO_DIR = EXAMPLE_DIR / "repo"
HOST_SKILLS_DIR = EXAMPLE_DIR / "skills"


def build_agent(model: str) -> SandboxAgent[None]:
    return SandboxAgent(
        name="Sandbox engineer",
        model=model,
        instructions=(
            "Read `repo/task.md` before editing files. Stay grounded in the repository, preserve "
            "existing behavior, and mention the exact verification command you ran. "
            "If you edit files with apply_patch, paths are relative to the sandbox workspace root."
        ),
        default_manifest=Manifest(
            entries={
                "repo": LocalDir(src=HOST_REPO_DIR),
            }
        ),
        capabilities=Capabilities.default()
        + [
            Skills(
                lazy_from=LocalDirLazySkillSource(
                    # This is a host path read by the SDK process.
                    # Requested skills are copied into `skills_path` in the sandbox.
                    source=LocalDir(src=HOST_SKILLS_DIR),
                )
            ),
        ],
    )


async def main() -> None:
    result = await Runner.run(
        build_agent("gpt-5.6-sol"),
        "Open `repo/task.md`, fix the issue, run the targeted test, and summarize the change.",
        run_config=RunConfig(
            sandbox=SandboxRunConfig(client=UnixLocalSandboxClient()),
            workflow_name="Sandbox coding example",
        ),
    )
    print(result.final_output)


if __name__ == "__main__":
    asyncio.run(main())
```

See [examples/sandbox/docs/coding_task.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/docs/coding_task.py>). It uses a tiny shell-based repo so the example can be verified deterministically across Unix-local runs.

## Key choices

Once the basic run works, the choices most people reach for next are:

- `default_manifest`: the files, repos, directories, and mounts for fresh sandbox sessions
- `instructions`: short workflow rules that should apply across prompts
- `base_instructions`: an advanced escape hatch for replacing the SDK sandbox prompt
- `capabilities`: sandbox-native tools such as filesystem editing/image inspection, shell, skills, memory, and the SDK's compaction mechanism
- `run_as`: the sandbox user account under which model-facing tools execute
- `SandboxRunConfig.client`: the sandbox backend
- `SandboxRunConfig.session`, `session_state`, or `snapshot`: how later runs reconnect to prior work

## Where to go next

- [Concepts](<OpenAI 沙箱 Agent 核心概念.md>): understand manifests, capabilities, permissions, snapshots, run config, and composition patterns.
- [Sandbox clients](<选择 OpenAI 沙箱客户端.md>): choose Unix-local, Docker, hosted providers, and mount strategies.
- [Agent memory](<OpenAI 沙箱 Agent 的经验记忆.md>): preserve and reuse lessons from previous sandbox runs.

If shell access is just one tool that you use occasionally, start with hosted shell in the [tools guide](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/tools.md>). Reach for sandbox agents when workspace isolation, sandbox client choice, or sandbox-session resume behavior are part of the design.


</details>
