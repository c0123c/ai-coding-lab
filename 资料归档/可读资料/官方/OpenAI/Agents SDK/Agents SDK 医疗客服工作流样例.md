# Agents SDK 医疗客服工作流样例

用合成场景展示角色编排、隔离工作区、支持产物和人工复核；可参考工程结构，属于业务示例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/healthcare_support/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/Agents SDK 医疗客服工作流样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 医疗保健支持

此示例演示如何使用标准 agents 和沙箱 agent 构建 Agents SDK 的医疗保健支持工作流程。该场景是有意合成和通用的：患者询问计费或承保范围问题，工作流程检查本地记录，检查隔离沙箱工作区中的保单文档，编写支持工件，并可选择将一个不明确的案例路由给人工审核员。

## 这个例子说明了什么

- **标准 agent 编排 ** 具有顶级支持编排器和福利子代理。
- **沙箱agents**，带有已安装的工作区、shell 命令、生成的输出文件夹和运行时选择的沙箱配置。
- **沙盒功能**包括`Shell`、`Filesystem`和延迟加载`Skills`。
- **使用审批门控队列路由工具进行人机交互审批**。
- **持久内存**与`SQLiteSession`，在场景运行之间共享。
- **每位专家 agent 的结构化输出 ** 和最终案例解决方案。
- **跟踪**，以便您可以在OpenAI跟踪查看器中检查每个模型调用和工具调用。
- **CLI-第一个工作流程**可以从存储库签出中按场景运行。

## 建筑

该工作流有两种协同工作的执行模式：

1. **标准协调器agent**在正常的AgentsSDK循环中运行，首先调用好处子代理，然后调用沙箱agent工具，并决定是否请求人工切换。
2. **沙箱策略agent**在`agents.sandbox`后面运行，读取已安装的案例文件和策略文档，使用shell命令和延迟加载的技能，将markdown工件写入`output/`，并返回结构化策略摘要。

本地夹具数据位于`data/scenarios/*.json`和`data/fixtures/*.json`中。沙箱策略库位于`policies/*.md`。生成的工件将复制到 `.cache/healthcare_support/output/<scenario_id>/`。

## 应用场景

内置场景的复杂性增加：

- `eligibility_verification_basic` 检查一个简单的福利问题。
- `referral_status_check` 添加了引用查找。
- `blue_cross_pt_benefits` 显示了受益于共享 SQLite 内存的后续操作。
- `prior_auth_confusion_ct` 重点关注事先授权和摄入路由混乱。
- `billing_coverage_clarification` 将福利查找与沙箱策略搜索和文档生成相结合。
- `messy_ambiguous_knee_case` 在排队切换之前触发人工审批流程。

## 运行 CLI 演示

从存储库根目录：

```bash
uv run python examples/sandbox/healthcare_support/main.py
```

有用的选项：

```bash
uv run python examples/sandbox/healthcare_support/main.py --list-scenarios
uv run python examples/sandbox/healthcare_support/main.py --scenario blue_cross_pt_benefits
uv run python examples/sandbox/healthcare_support/main.py --scenario messy_ambiguous_knee_case
uv run python examples/sandbox/healthcare_support/main.py --reset-memory
```

对于无人值守运行，请将 `EXAMPLES_INTERACTIVE_MODE=auto` 设置为自动应答提示：

```bash
EXAMPLES_INTERACTIVE_MODE=auto uv run python examples/sandbox/healthcare_support/main.py --scenario messy_ambiguous_knee_case
```

## 首先读取的文件

- [`main.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/main.py>) 运行独立的 CLI 演示。
- [`workflow.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/workflow.py>) 包含共享工作流执行逻辑、沙箱设置、工件复制、跟踪和批准恢复循环。
- [`support_agents.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/support_agents.py>) 定义了协调器、好处子代理、沙箱策略 agent 和内存回顾 agent。
- [`tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/tools.py>) 定义了本地查找工具和审批门控人工切换工具。
- [`skills/prior-auth-packet-builder/SKILL.md`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/skills/prior-auth-packet-builder/SKILL.md>)是运行时加载的沙箱技能。

## 注释

- 这是一个演示工作流程，而不是生产医疗保健系统。
- 此示例中的所有患者、付款人和保单数据都是综合的。
- 该示例从存储库根 `.env` 文件和此演示的可选本地 `.env` 文件加载环境默认值。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Healthcare support

This example shows how to build a healthcare support workflow with Agents SDK using both standard agents and a sandbox agent. The scenario is intentionally synthetic and generic: a patient asks a billing or coverage question, the workflow checks local records, inspects policy documents in an isolated sandbox workspace, writes support artifacts, and optionally routes one ambiguous case to a human reviewer.

## What this example demonstrates

- **Standard agent orchestration** with a top-level support orchestrator and a benefits subagent.
- **Sandbox agents** with a mounted workspace, shell commands, a generated output folder, and runtime-selected sandbox config.
- **Sandbox capabilities** including `Shell`, `Filesystem`, and lazy-loaded `Skills`.
- **Human-in-the-loop approvals** using an approval-gated queue-routing tool.
- **Persistent memory** with `SQLiteSession`, shared across scenario runs.
- **Structured outputs** for each specialist agent and the final case resolution.
- **Tracing** so you can inspect every model call and tool call in the OpenAI trace viewer.
- **CLI-first workflow** that can be run scenario by scenario from the repository checkout.

## Architecture

The workflow has two execution modes working together:

1. A **standard orchestrator agent** runs in the normal Agents SDK loop, calls the benefits subagent first, then calls a sandbox agent tool, and decides whether to request a human handoff.
2. A **sandbox policy agent** runs behind `agents.sandbox`, reads the mounted case files and policy documents, uses shell commands plus a lazily loaded skill, writes markdown artifacts into `output/`, and returns a structured policy summary.

The local fixture data lives in `data/scenarios/*.json` and `data/fixtures/*.json`. The sandbox policy library lives in `policies/*.md`. Generated artifacts are copied to `.cache/healthcare_support/output/<scenario_id>/`.

## Scenarios

The built-in scenarios increase in complexity:

- `eligibility_verification_basic` checks a straightforward benefits question.
- `referral_status_check` adds a referral lookup.
- `blue_cross_pt_benefits` shows a follow-up turn that benefits from the shared SQLite memory.
- `prior_auth_confusion_ct` focuses on prior-authorization and intake-routing confusion.
- `billing_coverage_clarification` combines benefits lookup with sandbox policy search and document generation.
- `messy_ambiguous_knee_case` triggers the human approval flow before queueing a handoff.

## Run the CLI demo

From the repository root:

```bash
uv run python examples/sandbox/healthcare_support/main.py
```

Useful options:

```bash
uv run python examples/sandbox/healthcare_support/main.py --list-scenarios
uv run python examples/sandbox/healthcare_support/main.py --scenario blue_cross_pt_benefits
uv run python examples/sandbox/healthcare_support/main.py --scenario messy_ambiguous_knee_case
uv run python examples/sandbox/healthcare_support/main.py --reset-memory
```

For unattended runs, set `EXAMPLES_INTERACTIVE_MODE=auto` to auto-answer prompts:

```bash
EXAMPLES_INTERACTIVE_MODE=auto uv run python examples/sandbox/healthcare_support/main.py --scenario messy_ambiguous_knee_case
```

## Files to read first

- [`main.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/main.py>) runs the standalone CLI demo.
- [`workflow.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/workflow.py>) contains the shared workflow execution logic, sandbox setup, artifact copying, tracing, and approval resume loop.
- [`support_agents.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/support_agents.py>) defines the orchestrator, benefits subagent, sandbox policy agent, and memory recap agent.
- [`tools.py`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/tools.py>) defines the local lookup tools and the approval-gated human handoff tool.
- [`skills/prior-auth-packet-builder/SKILL.md`](<https://github.com/openai/openai-agents-python/blob/125efa029b4bfd84238bd2c4fd69c3406f802663/examples/sandbox/healthcare_support/skills/prior-auth-packet-builder/SKILL.md>) is the sandbox skill loaded at runtime.

## Notes

- This is a demo workflow, not a production healthcare system.
- All patient, payer, and policy data in this example is synthetic.
- The example loads environment defaults from the repository-root `.env` file and from this demo's optional local `.env` file.


</details>
