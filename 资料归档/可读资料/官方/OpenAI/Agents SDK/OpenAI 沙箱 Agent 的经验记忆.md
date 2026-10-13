# OpenAI 沙箱 Agent 的经验记忆

说明把先前运行经验保存为工作区文件、读取和生成记忆以及隔离不同角色；区分经验文件与会话消息历史。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-09-24](<https://github.com/openai/openai-agents-python/commit/592fa9438bf78276fc5fa79ac315527cf231a13a>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/docs/sandbox/memory.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/docs/sandbox/memory.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# Agent内存

内存可以让未来的沙箱-agent 运行从之前的运行中学习。它与 SDK 的会话 [`Session`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/sessions/index.md>) 内存分开，后者存储消息历史记录。内存将先前运行的经验教训提取到沙箱工作区中的文件中。

内存可以降低未来运行的三种成本：

1. Agent 成本：如果 agent 花费很长时间来完成工作流程，则下一次运行应该需要较少的探索。这可以减少令牌的使用和完成时间。
2. 用户成本：如果用户更正了 agent 或表达了偏好，则将来的运行可以记住该反馈。这可以减少人为干预。
3. 上下文成本：如果 agent 之前完成了一项任务，并且用户想要在该任务的基础上进行构建，则用户不需要查找前一个线程或重新输入所有上下文。这使得任务描述更短。

请参阅 [examples/sandbox/memory.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/memory.py>) 了解完整的两次运行示例，该示例修复错误、生成内存、恢复快照并在后续验证程序运行中使用该内存。请参阅 [examples/sandbox/memory_multi_agent_multiturn.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/memory_multi_agent_multiturn.py>) 了解具有单独内存布局的多匝、多 agent 示例。

## 启用内存

将 `Memory()` 作为功能添加到沙箱 agent。

```python
from pathlib import Path
import tempfile

from agents.sandbox import LocalSnapshotSpec, SandboxAgent
from agents.sandbox.capabilities import Filesystem, Memory, Shell

agent = SandboxAgent(
    name="Memory-enabled reviewer",
    instructions="Inspect the workspace and preserve useful lessons for follow-up runs.",
    capabilities=[Memory(), Filesystem(), Shell()],
)

with tempfile.TemporaryDirectory(prefix="sandbox-memory-example-") as snapshot_dir:
    sandbox = await client.create(
        manifest=manifest,
        snapshot=LocalSnapshotSpec(base_path=Path(snapshot_dir)),
    )
```

如果启用读取，`Memory()`需要`Shell()`，这使得agent在注入的摘要不够时读取和搜索内存文件。当启用实时内存更新（默认情况下）时，它还需要 `Filesystem()`，如果 agent 发现陈旧内存或用户要求其更新内存，则可以让 agent 更新 `memories/MEMORY.md`。

默认情况下，内存工件存储在 `memories/` 下的沙箱工作区中。要在以后的运行中重用它们，请通过保留相同的实时沙箱会话或从持久会话状态或快照恢复来保留和重用整个配置的内存目录；一个新的空沙箱从空内存开始。

`Memory()` 可以读取和生成存储器。将 `Memory(generate=None)` 用于应该读取内存但不应生成新内存的 agents，例如，当由内部 agents、子代理、检查器或一次性工具 agents 运行时，不会添加太多信号。当运行应该为以后生成内存，但用户不希望运行受到现有内存的影响时，请使用`Memory(read=None)`。

## 读取内存

内存读取使用渐进式披露。在运行开始时，SDK 会将一般有用的提示、用户首选项和可用内存的小摘要 (`memory_summary.md`) 注入到 agent 的开发人员提示中。这为 agent 提供了足够的背景来决定先前的工作是否相关。

当之前的工作看起来相关时，agent 会在配置的内存索引（`memories_dir` 下的 `MEMORY.md`）中搜索当前任务中的关键字。仅当任务需要更多详细信息时，它才会在配置的 `rollout_summaries/` 目录下打开相应的先前推出摘要。

内存可能会变得陈旧。 Agents 被指示仅将记忆视为指导并信任当前环境。默认情况下，内存读取已启用`live_update`，因此如果agent发现陈旧内存，它可以在同一运行中更新配置的`MEMORY.md`。当 agent 应该读取内存但在运行期间不修改它时，禁用实时更新，例如，如果运行对延迟敏感。

## 生成内存

运行完成后，沙箱运行时会将该运行段附加到对话文件中。沙箱会话关闭时会处理累积的对话文件。

内存生成有两个阶段：

1. 阶段1：对话提取。内存生成模型处理一个累积的对话文件并生成对话摘要。系统、开发者、推理内容省略。如果对话太长，则会被截断以适合上下文窗口，并保留开头和结尾。它还生成原始记忆摘录：Phase 2 可以整合的对话中的紧凑笔记。
2. 阶段2：布局整合。合并 agent 读取一种内存布局的原始记忆，在需要更多证据时打开对话摘要，并将模式提取到 `MEMORY.md` 和 `memory_summary.md` 中。

默认工作区布局是：

```text
workspace/
├── sessions/
│   └── <rollout-id>.jsonl
└── memories/
    ├── memory_summary.md
    ├── MEMORY.md
    ├── raw_memories.md (intermediate)
    ├── phase_two_selection.json (intermediate)
    ├── raw_memories/ (intermediate)
    │   └── <rollout-id>.md
    ├── rollout_summaries/
    │   └── <rollout-id>_<slug>.md
    └── skills/
```

您可以使用 `MemoryGenerateConfig` 配置内存生成：

```python
from agents.sandbox import MemoryGenerateConfig
from agents.sandbox.capabilities import Memory

memory = Memory(
    generate=MemoryGenerateConfig(
        max_raw_memories_for_consolidation=128,
        extra_prompt="Pay extra attention to what made the customer more satisfied or annoyed",
    ),
)
```

使用 `extra_prompt` 告诉内存生成器哪些信号对您的用例最重要，例如 GTM agent 的客户和公司详细信息。

如果最近的原始记忆超过 `max_raw_memories_for_consolidation`（默认为 256），阶段 2 仅保留最新对话中的记忆并删除较旧的记忆。新近度基于上次更新对话的时间。这种遗忘机制有助于记忆反映最新的环境。

## 多轮对话

对于多轮沙盒聊天，请使用正常的 SDK `Session` 以及相同的实时沙盒会话：

```python
from agents import Runner, SQLiteSession
from agents.run import RunConfig
from agents.sandbox import SandboxRunConfig

conversation_session = SQLiteSession("gtm-q2-pipeline-review")
sandbox = await client.create(manifest=agent.default_manifest)

async with sandbox:
    run_config = RunConfig(
        sandbox=SandboxRunConfig(session=sandbox),
        workflow_name="GTM memory example",
    )
    await Runner.run(
        agent,
        "Analyze data/leads.csv and identify one promising GTM segment.",
        session=conversation_session,
        run_config=run_config,
    )
    await Runner.run(
        agent,
        "Using that analysis, write a short outreach hypothesis.",
        session=conversation_session,
        run_config=run_config,
    )
```

两次运行都通过相同的 SDK 会话会话 (`session=conversation_session`)，因此共享相同的 `session.session_id`。结果，两次运行都附加到一个内存会话文件。这与沙箱 (`sandbox`) 不同，沙箱标识实时工作区并且不用作内存会话 ID。阶段 1 在沙箱会话关闭时看到累积的会话，因此它可以从整个交换中提取内存，而不是两个孤立的回合。

如果您希望多个 `Runner.run(...)` 调用成为一个内存对话，请在这些调用之间传递一个稳定的标识符。当内存将跑步与对话关联起来时，它会按以下顺序解析：

1. `conversation_id`，当您将一个传递给`Runner.run(...)`时
2. `session.session_id`，当您通过 SDK `Session` 例如 `SQLiteSession`
3. `RunConfig.group_id`，当以上都不存在时
4. 当不存在稳定标识符时，每次运行生成的 ID

## 使用不同的布局来隔离不同的内存 agents

内存隔离基于 `MemoryLayoutConfig`，而不是 agent 名称。 Agents具有相同的布局和相同的内存会话ID共享一个内存会话和一个合并内存。具有不同布局的 Agents 保留单独的卷展文件、原始内存、`MEMORY.md` 和 `memory_summary.md`，即使它们共享相同的沙箱工作区。

当多个 agents 共享一个沙箱但不应共享内存时，请使用单独的布局：

```python
from agents import SQLiteSession
from agents.sandbox import MemoryLayoutConfig, SandboxAgent
from agents.sandbox.capabilities import Filesystem, Memory, Shell

gtm_agent = SandboxAgent(
    name="GTM reviewer",
    instructions="Analyze GTM workspace data and write concise recommendations.",
    capabilities=[
        Memory(
            layout=MemoryLayoutConfig(
                memories_dir="memories/gtm",
                sessions_dir="sessions/gtm",
            )
        ),
        Filesystem(),
        Shell(),
    ],
)

engineering_agent = SandboxAgent(
    name="Engineering reviewer",
    instructions="Inspect engineering workspaces and summarize fixes and risks.",
    capabilities=[
        Memory(
            layout=MemoryLayoutConfig(
                memories_dir="memories/engineering",
                sessions_dir="sessions/engineering",
            )
        ),
        Filesystem(),
        Shell(),
    ],
)

gtm_session = SQLiteSession("gtm-q2-pipeline-review")
engineering_session = SQLiteSession("eng-invoice-test-fix")
```

这可以防止 GTM 分析被合并到工程错误修复内存中，反之亦然。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Agent memory

Memory lets future sandbox-agent runs learn from prior runs. It is separate from the SDK's conversational [`Session`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/docs/sessions/index.md>) memory, which stores message history. Memory distills lessons from prior runs into files in the sandbox workspace.

Memory can reduce three kinds of cost for future runs:

1. Agent cost: If the agent took a long time to complete a workflow, the next run should need less exploration. This can reduce token usage and time to completion.
2. User cost: If the user corrected the agent or expressed a preference, future runs can remember that feedback. This can reduce human intervention.
3. Context cost: If the agent completed a task before, and the user wants to build on that task, the user should not need to find the previous thread or re-type all the context. This makes task descriptions shorter.

See [examples/sandbox/memory.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/memory.py>) for a complete two-run example that fixes a bug, generates memory, resumes a snapshot, and uses that memory in a follow-up verifier run. See [examples/sandbox/memory_multi_agent_multiturn.py](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/memory_multi_agent_multiturn.py>) for a multi-turn, multi-agent example with separate memory layouts.

## Enable memory

Add `Memory()` as a capability to the sandbox agent.

```python
from pathlib import Path
import tempfile

from agents.sandbox import LocalSnapshotSpec, SandboxAgent
from agents.sandbox.capabilities import Filesystem, Memory, Shell

agent = SandboxAgent(
    name="Memory-enabled reviewer",
    instructions="Inspect the workspace and preserve useful lessons for follow-up runs.",
    capabilities=[Memory(), Filesystem(), Shell()],
)

with tempfile.TemporaryDirectory(prefix="sandbox-memory-example-") as snapshot_dir:
    sandbox = await client.create(
        manifest=manifest,
        snapshot=LocalSnapshotSpec(base_path=Path(snapshot_dir)),
    )
```

If read is enabled, `Memory()` requires `Shell()`, which lets the agent read and search memory files when the injected summary is not enough. When live memory update is enabled (by default), it also requires `Filesystem()`, which lets the agent update `memories/MEMORY.md` if the agent discovers stale memory or the user asks it to update memory.

By default, memory artifacts are stored in the sandbox workspace under `memories/`. To reuse them in a later run, preserve and reuse the whole configured memories directory by keeping the same live sandbox session or resuming from a persisted session state or snapshot; a fresh empty sandbox starts with empty memory.

`Memory()` enables both reading and generating memories. Use `Memory(generate=None)` for agents that should read memory but should not generate new memories—for example, when runs by internal agents, subagents, checkers, or one-off tool agents do not add much signal. Use `Memory(read=None)` when the run should generate memory for later, but the user doesn't want the run to be influenced by existing memory.

## Read memory

Memory reads use progressive disclosure. At the start of a run, the SDK injects a small summary (`memory_summary.md`) of generally useful tips, user preferences, and available memories into the agent's developer prompt. This gives the agent enough context to decide whether prior work may be relevant.

When prior work looks relevant, the agent searches the configured memory index (`MEMORY.md` under `memories_dir`) for keywords from the current task. It opens the corresponding prior rollout summaries under the configured `rollout_summaries/` directory only when the task needs more detail.

Memory can become stale. Agents are instructed to treat memories as guidance only and trust the current environment. By default, memory reads have `live_update` enabled, so if the agent discovers stale memory, it can update the configured `MEMORY.md` in the same run. Disable live updates when the agent should read memory but not modify it during the run, for example if the run is latency sensitive.

## Generate memory

After a run finishes, the sandbox runtime appends that run segment to a conversation file. Accumulated conversation files are processed when the sandbox session closes.

Memory generation has two phases:

1. Phase 1: conversation extraction. A memory-generating model processes one accumulated conversation file and generates a conversation summary. System, developer, and reasoning content are omitted. If the conversation is too long, it is truncated to fit within the context window, with the beginning and end preserved. It also generates a raw memory extract: compact notes from the conversation that Phase 2 can consolidate.
2. Phase 2: layout consolidation. A consolidation agent reads raw memories for one memory layout, opens conversation summaries when more evidence is needed, and extracts patterns into `MEMORY.md` and `memory_summary.md`.

The default workspace layout is:

```text
workspace/
├── sessions/
│   └── <rollout-id>.jsonl
└── memories/
    ├── memory_summary.md
    ├── MEMORY.md
    ├── raw_memories.md (intermediate)
    ├── phase_two_selection.json (intermediate)
    ├── raw_memories/ (intermediate)
    │   └── <rollout-id>.md
    ├── rollout_summaries/
    │   └── <rollout-id>_<slug>.md
    └── skills/
```

You can configure memory generation with `MemoryGenerateConfig`:

```python
from agents.sandbox import MemoryGenerateConfig
from agents.sandbox.capabilities import Memory

memory = Memory(
    generate=MemoryGenerateConfig(
        max_raw_memories_for_consolidation=128,
        extra_prompt="Pay extra attention to what made the customer more satisfied or annoyed",
    ),
)
```

Use `extra_prompt` to tell the memory generator which signals matter most for your use case, such as customer and company details for a GTM agent.

If recent raw memories exceed `max_raw_memories_for_consolidation` (defaults to 256), Phase 2 keeps only memories from the newest conversations and removes older ones. Recency is based on the last time the conversation is updated. This forgetting mechanism helps memories reflect the newest environment.

## Multi-turn conversations

For multi-turn sandbox chats, use the normal SDK `Session` together with the same live sandbox session:

```python
from agents import Runner, SQLiteSession
from agents.run import RunConfig
from agents.sandbox import SandboxRunConfig

conversation_session = SQLiteSession("gtm-q2-pipeline-review")
sandbox = await client.create(manifest=agent.default_manifest)

async with sandbox:
    run_config = RunConfig(
        sandbox=SandboxRunConfig(session=sandbox),
        workflow_name="GTM memory example",
    )
    await Runner.run(
        agent,
        "Analyze data/leads.csv and identify one promising GTM segment.",
        session=conversation_session,
        run_config=run_config,
    )
    await Runner.run(
        agent,
        "Using that analysis, write a short outreach hypothesis.",
        session=conversation_session,
        run_config=run_config,
    )
```

Both runs pass the same SDK conversation session (`session=conversation_session`) and therefore share the same `session.session_id`. As a result, both runs append to one memory conversation file. This is different from the sandbox (`sandbox`), which identifies the live workspace and is not used as the memory conversation ID. Phase 1 sees the accumulated conversation when the sandbox session closes, so it can extract memory from the whole exchange instead of two isolated turns.

If you want multiple `Runner.run(...)` calls to become one memory conversation, pass a stable identifier across those calls. When memory associates a run with a conversation, it resolves in this order:

1. `conversation_id`, when you pass one to `Runner.run(...)`
2. `session.session_id`, when you pass an SDK `Session` such as `SQLiteSession`
3. `RunConfig.group_id`, when neither of the above is present
4. A generated per-run ID, when no stable identifier is present

## Use different layouts to isolate memory for different agents

Memory isolation is based on `MemoryLayoutConfig`, not on agent name. Agents with the same layout and the same memory conversation ID share one memory conversation and one consolidated memory. Agents with different layouts keep separate rollout files, raw memories, `MEMORY.md`, and `memory_summary.md`, even when they share the same sandbox workspace.

Use separate layouts when multiple agents share one sandbox but should not share memory:

```python
from agents import SQLiteSession
from agents.sandbox import MemoryLayoutConfig, SandboxAgent
from agents.sandbox.capabilities import Filesystem, Memory, Shell

gtm_agent = SandboxAgent(
    name="GTM reviewer",
    instructions="Analyze GTM workspace data and write concise recommendations.",
    capabilities=[
        Memory(
            layout=MemoryLayoutConfig(
                memories_dir="memories/gtm",
                sessions_dir="sessions/gtm",
            )
        ),
        Filesystem(),
        Shell(),
    ],
)

engineering_agent = SandboxAgent(
    name="Engineering reviewer",
    instructions="Inspect engineering workspaces and summarize fixes and risks.",
    capabilities=[
        Memory(
            layout=MemoryLayoutConfig(
                memories_dir="memories/engineering",
                sessions_dir="sessions/engineering",
            )
        ),
        Filesystem(),
        Shell(),
    ],
)

gtm_session = SQLiteSession("gtm-q2-pipeline-review")
engineering_session = SQLiteSession("eng-invoice-test-fix")
```

This prevents GTM analysis from being consolidated into engineering bug-fix memory, and vice versa.


</details>
