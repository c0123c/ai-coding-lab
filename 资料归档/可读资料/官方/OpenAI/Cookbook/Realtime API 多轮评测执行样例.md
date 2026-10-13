# Realtime API 多轮评测执行样例

用模型模拟用户、固定语音分块并模拟工具，说明测试定义、文件和运行流程；属于实时应用评测参考。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-03-02](<https://github.com/openai/openai-cookbook/commit/45a8058f49252317a6a717e08bb7987d82ae1cd1>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/evals/realtime_evals/run_harness/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/Realtime API 多轮评测执行样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 跑步安全带

用于实时 API 的模型模拟多匝评估线束。该线束使用
一个实时模型作为用户模拟器（仅音频），另一个实时模型
作为被测助手。它重放固定分块（VAD 关闭），模拟模拟 JSON 中的工具 
，并记录完整跟踪。

## 它的作用

- 用 pandas 加载 `run_harness/data/simulations.csv`。
- 读取每个模拟 JSON 文件定义场景、模拟器身份、工具模拟、
和LLM-作为评委评分标准。
- 通过实时模拟器模型（仅音频）生成用户音频回合。
- 将用户音频以固定大小的块传输给助手并手动提交。
- 捕获助手 audio/text、工具调用、工具输出和延迟。
- 以 LLM- 作为评委，对转动电平和跟踪电平标准进行评分。
- 在 `run_harness/results/` 下写入 `results.csv`、`summary.json` 和完整跟踪日志。
- 默认情况下，在 `run_harness/results/<run_id>/plots/` 下渲染样式为 PNG 的图。

## 文件

- `run_harness/run_realtime_evals.py`：运行线束脚本。
- `run_harness/data/simulations.csv`：模拟文件索引。
- `run_harness/data/sim_*.json`：模拟定义（此存储库当前提供 3 示例）。
- `run_harness/results/<run_id>/events/*.jsonl`：每次模拟的完整事件跟踪。
- `run_harness/results/<run_id>/conversations/*.txt`：带有工具调用的人类可读的文字记录。

## 模拟定义（高级）

每个 `sim_*.json` 文件定义：

- **场景**（用户尝试执行的操作）
- **工具模拟**（每个工具在调用时应返回的内容）
- **评判规则**（每回合/总体评分应为多少）

这使您可以运行可重复的多轮评估，而无需实时后端集成。

## 如何跑

从仓库根目录：

```bash
python run_harness/run_realtime_evals.py --max-examples 1
```

常用选项：

- `--data-csv`：模拟索引CSV。
- `--model`：被测辅助模型的别名。
- `--assistant-model`：正在测试的实时模型（覆盖`--model`）。
- `--simulator-model`：作为用户使用的实时模型。
- `--system-prompt-file`：助手系统提示文件的别名。
- `--tools-file`：辅助工具文件的别名。
- `--assistant-system-prompt-file`：覆盖助手系统提示文件（更具体）。
- `--assistant-tools-file`：覆盖助手的工具文件（更具体）。
- `--simulator-system-prompt`：覆盖模拟器系统提示符（字符串）。
- `--chunk-ms`, `--sample-rate-hz`, `--real-time`.
- `--max-turns`, `--max-examples`.
- `--skip-plots`.

注意事项：

- 故意不支持温度和最大输出令牌。
- 模拟器仅发出音频。如果有的话，可以从音频增量中捕获文字记录。
- 每次模拟都会确定性地模拟工具输出 JSON。
- `results.csv` 包括指向 JSONL 迹线的 `event_log_path` 列。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Run Harness

Model-simulated multi-turn eval harness for the Realtime API. This harness uses
one realtime model as the user simulator (audio-only) and another realtime model
as the assistant under test. It replays fixed chunking (VAD off), mocks tools
from the simulation JSON, and records full traces.

## What it does

- Loads `run_harness/data/simulations.csv` with pandas.
- Reads per-simulation JSON files defining scenario, simulator identity, tool mocks,
  and LLM-as-judge grading criteria.
- Generates user audio turns via a realtime simulator model (audio-only).
- Streams user audio to the assistant in fixed-size chunks and commits manually.
- Captures assistant audio/text, tool calls, tool outputs, and latencies.
- Grades turn-level and trace-level criteria with an LLM-as-judge.
- Writes `results.csv`, `summary.json`, and full trace logs under `run_harness/results/`.
- Renders styled PNG plots under `run_harness/results/<run_id>/plots/` by default.

## Files

- `run_harness/run_realtime_evals.py`: Run harness script.
- `run_harness/data/simulations.csv`: Index of simulation files.
- `run_harness/data/sim_*.json`: Simulation definitions (this repo currently ships 3 examples).
- `run_harness/results/<run_id>/events/*.jsonl`: Full event trace per simulation.
- `run_harness/results/<run_id>/conversations/*.txt`: Human-readable transcript with tool calls.

## Simulation definitions (high level)

Each `sim_*.json` file defines:

- **the scenario** (what the user is trying to do)
- **tool mocks** (what each tool should return when called)
- **judge rubric** (what should be graded per turn / overall)

This lets you run repeatable multi-turn evals without needing live backend integrations.

## How to run

From repo root:

```bash
python run_harness/run_realtime_evals.py --max-examples 1
```

Common options:

- `--data-csv`: Simulation index CSV.
- `--model`: Alias for the assistant model under test.
- `--assistant-model`: Realtime model under test (overrides `--model`).
- `--simulator-model`: Realtime model used as the user.
- `--system-prompt-file`: Alias for assistant system prompt file.
- `--tools-file`: Alias for assistant tools file.
- `--assistant-system-prompt-file`: Override assistant system prompt file (more specific).
- `--assistant-tools-file`: Override tools file for the assistant (more specific).
- `--simulator-system-prompt`: Override simulator system prompt (string).
- `--chunk-ms`, `--sample-rate-hz`, `--real-time`.
- `--max-turns`, `--max-examples`.
- `--skip-plots`.

Notes:

- Temperature and max output tokens are intentionally not supported.
- The simulator emits audio only. Transcripts are captured from audio deltas if available.
- Tool outputs are mocked deterministically from each simulation JSON.
- `results.csv` includes an `event_log_path` column pointing to the JSONL trace.


</details>
