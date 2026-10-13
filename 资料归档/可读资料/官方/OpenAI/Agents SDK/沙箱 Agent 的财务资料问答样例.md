# 沙箱 Agent 的财务资料问答样例

展示先检索再回答、保留来源文件关系和加载指令的方式；使用合成财报，属于资料检索业务示例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/tutorials/dataroom_qa/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/沙箱 Agent 的财务资料问答样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 资料室问答

## 目标

通过合成的 10-K 数据包回答基础财务问题。

该数据包使用合成公司数据，但文档的形状类似于年度报告摘录：MD&A 文本使用 10-K `Part II, Item 7`，而报表 PDF 和脚注文本使用 `Part II, Item 8`。

## 为什么这很有价值

该演示展示了有界金融语料库上的检索优先 agent 模式，其中每个指标和解释都应与源文件相关联。

## 设置

运行夹具生成器，然后从存储库根运行 Unix 本地示例。运行示例之前，请在 shell 环境中设置 `OPENAI_API_KEY`。

```bash
uv run python examples/sandbox/tutorials/data/dataroom/setup.py
uv run python examples/sandbox/tutorials/dataroom_qa/main.py
```

在最初的回答之后，演示将沙箱会话保持打开状态，以提供丰富的后续提示。通过 `--no-interactive` 进行一击。

要在 Docker 中运行相同的清单，请构建共享教程映像一次并传递 
`--docker`：

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/dataroom_qa/main.py --docker
```

## 预期的工件

- 流媒体 agent 响应中直接引用的答案。
- 引文使用 `[n](data/source-file.txt:line:14)` 作为文本摘录，使用 `[n](data/source-file.pdf:page:1)` 作为一页合成 PDF。

## 演示形状

- 输入：5 综合归档文本文档和 3 来自 `examples/sandbox/tutorials/data/dataroom/` 的简单归档 PDF。
- 运行时原语：沙箱本地 bash/file 搜索。

## 指令如何加载

启动时，包装器将此文件夹的 `AGENTS.md` 加载到 agent 指令中，并构建一个硬编码清单，将共享 SEC 数据包从 `examples/sandbox/tutorials/data/dataroom/` 映射到沙箱中作为 `data/...`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Dataroom Q&A

## Goal

Answer grounded financial questions over a synthetic 10-K packet.

The packet uses synthetic company data, but the documents are shaped like annual report excerpts: MD&A text uses 10-K `Part II, Item 7`, while statement PDFs and footnote text use `Part II, Item 8`.

## Why this is valuable

This demo shows a retrieval-first agent pattern over a bounded financial corpus where each metric and explanation should stay tied to source files.

## Setup

Run the fixture generator and then the Unix-local example from the repository root. Set `OPENAI_API_KEY` in your shell environment before running the example.

```bash
uv run python examples/sandbox/tutorials/data/dataroom/setup.py
uv run python examples/sandbox/tutorials/dataroom_qa/main.py
```

After the initial answer, the demo keeps the sandbox session open for Rich-rendered follow-up prompts. Pass `--no-interactive` for a one-shot run.

To run the same manifest in Docker, build the shared tutorial image once and pass
`--docker`:

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/dataroom_qa/main.py --docker
```

## Expected artifacts

- A direct cited answer in the streamed agent response.
- Citations use `[n](data/source-file.txt:line:14)` for text excerpts and `[n](data/source-file.pdf:page:1)` for the one-page synthetic PDFs.

## Demo shape

- Inputs: 5 synthetic filing text docs and 3 simple filing PDFs from `examples/sandbox/tutorials/data/dataroom/`.
- Runtime primitives: sandbox-local bash/file search.

## How instructions are loaded

At startup, the wrapper loads this folder's `AGENTS.md` into the agent instructions and builds a hard-coded manifest that maps the shared SEC packet from `examples/sandbox/tutorials/data/dataroom/` into the sandbox as `data/...`.


</details>
