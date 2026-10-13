# 沙箱 Agent 的自然语言转 SQL 样例

针对 NASA 支出数据库生成并执行查询，讲解结构说明、工具权限、审计和沙箱；属于数据库应用样例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/extensions/daytona/usaspending_text2sql/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/沙箱 Agent 的自然语言转 SQL 样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# NASA 消费短信至SQL Agent

多轮对话 agent，将有关 NASA 联邦支出的自然语言问题转换为 SQL 查询，针对本地 SQLite 数据库执行它们，并返回结构化表格结果。

## 它是如何运作的

1. **架构知识**：agent在其系统提示中收到紧凑的架构摘要，并且可以按需从工作区文件中读取详细的每个表文档。
2. **SQL执行**：自定义`SqlCapability`提供了带有护栏的`run_sql`工具 - 只读模式、语句验证、行限制和查询超时。 agent被指示使用`run_sql`进行所有查询；该工具在 SQLite 级别强制执行只读访问。
3. **多轮对话**：agent保留了轮次的上下文，因此您可以提出后续问题，例如“按年份细分”或“仅顶部5”。
4. **压缩**：使用`Compaction`功能自动总结旧的对话上下文，将长时间会话保留在模型的上下文窗口内。
5. **Pause/resume**：输入`exit`暂停沙箱并退出。再次运行脚本以重新连接到同一个暂停的沙箱 - 无需重新下载。如果沙箱无法重新连接（例如，它被删除或过期），则会创建一个新沙箱并自动重建数据库。
6. **内存**：使用`Memory`功能从每个对话中提取学习内容并将其合并到结构化文件中。在后续会话中，agent 从之前对话的上下文（有用的查询模式、数据警告等）开始。

## 数据

该数据库包含来自 [USAspending.gov](<https://usaspending.gov>) 的 NASA 联邦支出数据，默认为 FY2021-FY2025（可通过 `setup_db.py` 上的 `--start-fy`/`--end-fy` 标志进行配置）。

它使用单个 `spending` 表，其中每一行都是联邦奖励的一项交易（义务、修改或解除义务）。 agent 根据需要通过 SQL 进行聚合。

数据库在首次运行时自动构建（需要在沙箱中访问互联网）。后续运行将重用现有数据库。

## 先决条件

- Python 3.12+
- `openai-agents` 安装了 Daytona 支持（`uv sync --extra daytona` 来自存储库根目录）
- `OPENAI_API_KEY`环境变量设置（对于LLM）
- `DAYTONA_API_KEY` 环境变量集（对于沙箱 — 从 [daytona.io](<https://daytona.io>) 获取一个）
- 互联网访问（用于沙箱内首次运行的数据库设置）

## 运行

从存储库根目录：

```bash
export OPENAI_API_KEY="sk-..."
export DAYTONA_API_KEY="..."
uv run python -m examples.sandbox.extensions.daytona.usaspending_text2sql.agent
```

## 示例问题

```
> What are NASA's top 10 contractors by total spending?
> Break that down by fiscal year
> Which NASA centers award the most contracts?
> Show me grants to universities in California
> How has NASA spending changed over time?
> What are the largest individual awards in the last 3 years?
> Compare contract vs grant spending by year
```

## 建筑

```
daytona/usaspending_text2sql/
├── agent.py            — SandboxAgent definition + interactive REPL
├── sql_capability.py   — SqlCapability (Capability) with run_sql tool and guardrails
├── setup_db.py         — Runs inside sandbox; fetches data from USAspending API, builds SQLite DB
├── schema/
│   ├── overview.md     — Compact schema summary (injected into instructions)
│   └── tables/         — Per-table column documentation (read on demand via Shell capability)
└── README.md
```

### SQL护栏（纵深防御）

1. **连接级别**：SQLite以`?mode=ro` URI打开（只读）
2. **PRAGMA**：`query_only = ON` 即使绕过验证也会阻止写入
3. **语句验证**：仅允许`SELECT`、`WITH`、`EXPLAIN`、`PRAGMA`
4. **行限制**：具有截断检测的硬上限（默认100行）
5. **超时**：查询在30秒后被终止

### 审核日志

所有沙箱操作（exec 调用、start/stop、SQL 查询及其结果）都通过 SDK 的 `Instrumentation` 和 `JsonlOutboxSink` 作为结构化 JSONL 事件记录到 `.audit_log.jsonl`。这对于调试、重放会话或检查 SQL 的 agent 运行的内容非常有用。

### 沙盒

此示例使用 Daytona 作为其沙箱后端。 agent 和功能定义与后端无关，但入口点 (`agent.py`) 硬编码 `DaytonaSandboxClient` 和 Daytona 特定功能，例如 pause/resume.


## 原文对照

<details>
<summary>展开完整原文</summary>

# NASA Spending Text-to-SQL Agent

Multi-turn conversational agent that translates natural-language questions about NASA federal spending into SQL queries, executes them against a local SQLite database, and returns structured tabular results.

## How it works

1. **Schema knowledge**: The agent receives a compact schema summary in its system prompt and can read detailed per-table documentation from workspace files on demand.
2. **SQL execution**: A custom `SqlCapability` provides a `run_sql` tool with guardrails — read-only mode, statement validation, row limits, and query timeouts. The agent is instructed to use `run_sql` for all queries; the tool enforces read-only access at the SQLite level.
3. **Multi-turn conversation**: The agent retains context across turns, so you can ask follow-up questions like "break that down by year" or "just the top 5".
4. **Compaction**: Uses the `Compaction` capability to automatically summarize older conversation context, keeping long sessions within the model's context window.
5. **Pause/resume**: Type `exit` to pause the sandbox and quit. Run the script again to reconnect to the same paused sandbox — no re-download needed. If the sandbox can't be reconnected (e.g. it was deleted or expired), a fresh one is created and the database is rebuilt automatically.
6. **Memory**: Uses the `Memory` capability to extract learnings from each conversation and consolidate them into structured files. On subsequent sessions, the agent starts with context from previous conversations (useful query patterns, data caveats, etc.).

## Data

The database contains NASA federal spending data from [USAspending.gov](<https://usaspending.gov>), defaulting to FY2021-FY2025 (configurable via `--start-fy`/`--end-fy` flags on `setup_db.py`).

It uses a single `spending` table where each row is one transaction (obligation, modification, or de-obligation) on a federal award. The agent aggregates as needed via SQL.

The database is built automatically on first run (requires internet access in the sandbox). Subsequent runs reuse the existing database.

## Prerequisites

- Python 3.12+
- `openai-agents` installed with Daytona support (`uv sync --extra daytona` from repo root)
- `OPENAI_API_KEY` environment variable set (for the LLM)
- `DAYTONA_API_KEY` environment variable set (for the sandbox — get one at [daytona.io](<https://daytona.io>))
- Internet access (for first-run database setup inside the sandbox)

## Run

From the repository root:

```bash
export OPENAI_API_KEY="sk-..."
export DAYTONA_API_KEY="..."
uv run python -m examples.sandbox.extensions.daytona.usaspending_text2sql.agent
```

## Example questions

```
> What are NASA's top 10 contractors by total spending?
> Break that down by fiscal year
> Which NASA centers award the most contracts?
> Show me grants to universities in California
> How has NASA spending changed over time?
> What are the largest individual awards in the last 3 years?
> Compare contract vs grant spending by year
```

## Architecture

```
daytona/usaspending_text2sql/
├── agent.py            — SandboxAgent definition + interactive REPL
├── sql_capability.py   — SqlCapability (Capability) with run_sql tool and guardrails
├── setup_db.py         — Runs inside sandbox; fetches data from USAspending API, builds SQLite DB
├── schema/
│   ├── overview.md     — Compact schema summary (injected into instructions)
│   └── tables/         — Per-table column documentation (read on demand via Shell capability)
└── README.md
```

### SQL guardrails (defense in depth)

1. **Connection-level**: SQLite opened with `?mode=ro` URI (read-only)
2. **PRAGMA**: `query_only = ON` prevents writes even if validation is bypassed
3. **Statement validation**: Only `SELECT`, `WITH`, `EXPLAIN`, `PRAGMA` are allowed
4. **Row limit**: Hard cap (default 100 rows) with truncation detection
5. **Timeout**: Queries killed after 30 seconds

### Audit log

All sandbox operations (exec calls, start/stop, SQL queries and their results) are logged to `.audit_log.jsonl` as structured JSONL events via the SDK's `Instrumentation` and `JsonlOutboxSink`. This is useful for debugging, replaying sessions, or inspecting exactly what SQL the agent ran.

### Sandbox

This example uses Daytona as its sandbox backend. The agent and capability definitions are backend-agnostic, but the entrypoint (`agent.py`) hardcodes `DaytonaSandboxClient` and Daytona-specific features like pause/resume.


</details>
