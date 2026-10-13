# 沙箱 Agent 的财务指标提取样例

从合成财报提取 CSV 或 JSONL，并用确定性脚本检查产物；可参考结构化输出验证，属于业务示例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/tutorials/dataroom_metric_extract/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/沙箱 Agent 的财务指标提取样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 数据室指标提取

## 目标

从合成的 10-K 数据包中提取财务指标，将结果表写入 CSV 或 JSONL，然后使用确定性评估脚本验证生成的工件。

该数据包使用合成公司数据，但源文档格式为年度报告摘录，其中包含 10-K `Part II, Item 7` MD&A 部分和 `Part II, Item 8` 财务报表部分。

## 为什么这很有价值

该演示展示了单遍结构化提取模式：沙箱 agent 读取杂乱的归档文档并发出键入的财务行，然后一个单独的主机端评估脚本检查工件。包装器不会在事后修复或删除重复的模型输出；如果行集错误，`evals.py` 失败，您将迭代提示或夹具数据。

## 设置

运行夹具生成器，然后从存储库根运行 Unix 本地示例。运行示例之前，请在 shell 环境中设置 `OPENAI_API_KEY`。

```bash
uv run python examples/sandbox/tutorials/data/dataroom/setup.py
uv run python examples/sandbox/tutorials/dataroom_metric_extract/main.py --output-format csv
uv run python examples/sandbox/tutorials/dataroom_metric_extract/evals.py --artifact-path examples/sandbox/tutorials/dataroom_metric_extract/output/financial_metrics.csv
```

初始提取后，演示将沙盒会话保持打开状态，以便在编写最终工件之前提供丰富的后续提示。通过 `--no-interactive` 进行一击。

要在 Docker 中运行提取，请构建一次共享教程映像并将 `--docker`
 添加到 `main.py`：

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/dataroom_metric_extract/main.py --docker --output-format csv
uv run python examples/sandbox/tutorials/dataroom_metric_extract/evals.py --artifact-path examples/sandbox/tutorials/dataroom_metric_extract/output/financial_metrics.csv
```

## 预期的工件

- `output/financial_metrics.csv`
- `output/financial_metrics.jsonl`

## 演示形状

- 输入：`examples/sandbox/tutorials/data/dataroom/` 中共享的 SEC 夹具数据包。
- 运行时原语：沙箱本地 bash/file 搜索加上类型化 agent 输出。
- 工作流程：沙箱提取器发出`FinancialMetricBatch`的固定单步管道；无需切换。 `main.py` 写入选定的工件格式，`evals.py` 在单独的步骤中验证该工件。
- 暂存空间：提取器可以使用 `scratchpad/` 作为临时票据，但只有选定的 `output/financial_metrics.*` 工件才是最终合同的一部分。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Dataroom metric extract

## Goal

Extract financial metrics from a synthetic 10-K packet, write the resulting table as CSV or JSONL, then validate the generated artifact with a deterministic eval script.

The packet uses synthetic company data, but the source docs are formatted as annual-report excerpts with 10-K `Part II, Item 7` MD&A sections and `Part II, Item 8` financial statement sections.

## Why this is valuable

This demo shows a single-pass structured extraction pattern: a sandbox agent reads messy filing documents and emits typed financial rows, then a separate host-side eval script checks the artifact. The wrapper does not repair or deduplicate model output after the fact; if the row set is wrong, `evals.py` fails and you iterate on the prompt or fixture data instead.

## Setup

Run the fixture generator and then the Unix-local example from the repository root. Set `OPENAI_API_KEY` in your shell environment before running the example.

```bash
uv run python examples/sandbox/tutorials/data/dataroom/setup.py
uv run python examples/sandbox/tutorials/dataroom_metric_extract/main.py --output-format csv
uv run python examples/sandbox/tutorials/dataroom_metric_extract/evals.py --artifact-path examples/sandbox/tutorials/dataroom_metric_extract/output/financial_metrics.csv
```

After the initial extraction, the demo keeps the sandbox session open for Rich-rendered follow-up prompts before writing the final artifact. Pass `--no-interactive` for a one-shot run.

To run extraction in Docker, build the shared tutorial image once and add `--docker`
to `main.py`:

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/dataroom_metric_extract/main.py --docker --output-format csv
uv run python examples/sandbox/tutorials/dataroom_metric_extract/evals.py --artifact-path examples/sandbox/tutorials/dataroom_metric_extract/output/financial_metrics.csv
```

## Expected artifacts

- `output/financial_metrics.csv`
- `output/financial_metrics.jsonl`

## Demo shape

- Inputs: the shared SEC fixture packet in `examples/sandbox/tutorials/data/dataroom/`.
- Runtime primitives: sandbox-local bash/file search plus typed agent outputs.
- Workflow: a fixed single-step pipeline where the sandbox extractor emits `FinancialMetricBatch`; no handoff is needed. `main.py` writes the selected artifact format, and `evals.py` validates that artifact in a separate step.
- Scratch space: the extractor may use `scratchpad/` for interim notes, but only the selected `output/financial_metrics.*` artifact is part of the final contract.


</details>
