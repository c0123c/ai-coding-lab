# 多 Agent 系统的整体评测样例

从合成追踪记录和低层评测标签生成运行摘要，介绍文件、数据及运行方法；属于系统评测工程参考。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-05-20](<https://github.com/openai/openai-cookbook/commit/0075904c509819f8d746794001a7a27066258956>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/partners/macro_evals_for_agentic_systems/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/多 Agent 系统的整体评测样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 代理系统的宏观评估

此示例是一个独立的 OpenAI Cookbook 笔记本，用于对跟踪的多 agent 系统进行宏观级别 
 评估。它从合成导出的 
 跟踪包加上保存的较低级别评估标签开始，将每次运行转换为紧凑的 
 分析文档，发现重复出现的行为模式，并通过 AgentTrace 式诊断通道深入研究一个 
 模式。

不需要 OpenAI API 键。该笔记本完全通过
打包的合成数据离线运行。

## 食谱位置

推荐的 Cookbook 存储库路径：

```text
examples/partners/macro_evals_for_agentic_systems/
```

## 所需文件

仅上传这些文件和文件夹：

```text
examples/partners/macro_evals_for_agentic_systems/
  macro_evals_for_agentic_systems.ipynb
  requirements.txt
  README.md
  run_notebook.sh
  data/
    trace_results.jsonl
    run_summary.json
    eval_labels.jsonl
    trace_bundles.zip
  helpers/
    data_prep.py
    macro_eval_pipeline.py
images/
  agentic-system-architecture.svg
```

请勿上传生成的文件或仅限本地的文件，例如 `.venv/`、`outputs/`、
`.jupyter/`、`.ipython/`、`.cache/`、`data/.macro_eval_cache/`、
`data/trace_bundles/`、`data/trace_snapshot.sqlite` 或`__pycache__/`。

## 数据

打包的数据集是合成的。它包括：

- `data/trace_results.jsonl`：1,000模拟订单的运行级别元数据。
- `data/run_summary.json`：聚合运行摘要。
- `data/eval_labels.jsonl`：为捆绑支持的运行保存较低级别的评估标签。
- `data/trace_bundles.zip`：导出的跟踪包，首次运行时在本地扩展。

故意不包含完整的 SQLite 跟踪快照，因为它很大 
 并且笔记本不需要。如果您本地有，笔记本可以使用
`data/trace_snapshot.sqlite`进行可选的丰富。

## 运行

使用 Python 3.11 或更新版本。

```bash
./run_notebook.sh
```

为了更快地进行冒烟测试：

```bash
MACRO_EVALS_TRACE_LIMIT=48 ./run_notebook.sh
```

该脚本创建本地虚拟环境、安装依赖项、执行 
 笔记本，并将生成的工件写入 `outputs/` 下。

## 注释

- `trace_bundles.zip` 自动扩展为
  `data/.macro_eval_cache/trace_bundles/`.
- 保存的评估标签用作离线证据；笔记本不打电话
模型 API 或重新运行 Promptfoo。
- 笔记本中的Agents SDK引用指向官方OpenAI
使用 agents、工具、切换、
 护栏、跟踪和沙箱执行进行代码优先编排的开发人员文档。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Macro Evals for Agentic Systems

This example is a self-contained OpenAI Cookbook notebook for macro-level
evaluation of a traced multi-agent system. It starts from synthetic exported
trace bundles plus saved lower-level eval labels, turns each run into compact
analysis documents, discovers recurring behavior patterns, and drills into one
pattern with an AgentTrace-style diagnosis pass.

No OpenAI API key is required. The notebook runs entirely offline from the
packaged synthetic data.

## Cookbook Location

Recommended Cookbook repo path:

```text
examples/partners/macro_evals_for_agentic_systems/
```

## Required Files

Upload only these files and folders:

```text
examples/partners/macro_evals_for_agentic_systems/
  macro_evals_for_agentic_systems.ipynb
  requirements.txt
  README.md
  run_notebook.sh
  data/
    trace_results.jsonl
    run_summary.json
    eval_labels.jsonl
    trace_bundles.zip
  helpers/
    data_prep.py
    macro_eval_pipeline.py
images/
  agentic-system-architecture.svg
```

Do not upload generated or local-only files such as `.venv/`, `outputs/`,
`.jupyter/`, `.ipython/`, `.cache/`, `data/.macro_eval_cache/`,
`data/trace_bundles/`, `data/trace_snapshot.sqlite`, or `__pycache__/`.

## Data

The packaged dataset is synthetic. It includes:

- `data/trace_results.jsonl`: run-level metadata for 1,000 simulated orders.
- `data/run_summary.json`: aggregate run summary.
- `data/eval_labels.jsonl`: saved lower-level eval labels for bundle-backed runs.
- `data/trace_bundles.zip`: exported trace bundles, expanded locally on first run.

The full SQLite trace snapshot is intentionally not included because it is large
and is not needed for the notebook. If you have it locally, the notebook can use
`data/trace_snapshot.sqlite` for optional enrichment.

## Run

Use Python 3.11 or newer.

```bash
./run_notebook.sh
```

For a faster smoke test:

```bash
MACRO_EVALS_TRACE_LIMIT=48 ./run_notebook.sh
```

The script creates a local virtual environment, installs dependencies, executes
the notebook, and writes generated artifacts under `outputs/`.

## Notes

- `trace_bundles.zip` is expanded automatically into
  `data/.macro_eval_cache/trace_bundles/`.
- Saved eval labels are used as offline evidence; the notebook does not call
  model APIs or re-run Promptfoo.
- The Agents SDK reference in the notebook points to the official OpenAI
  developer docs for code-first orchestration with agents, tools, handoffs,
  guardrails, tracing, and sandbox execution.


</details>
