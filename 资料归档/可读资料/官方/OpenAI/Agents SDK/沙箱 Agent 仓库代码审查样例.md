# 沙箱 Agent 仓库代码审查样例

固定仓库版本、检查差异、运行测试并输出逐行意见与补丁报告，展示开发者可以核查的代码审查产物。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-04](<https://github.com/openai/openai-agents-python/commit/a898061dfa72415e97620880a9eae005e390c92c>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/tutorials/repo_code_review/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/沙箱 Agent 仓库代码审查样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 回购代码审查

## 目标

审查一个小型公共 git 存储库，运行其测试，在结构化输出中留下行级审查注释，并编写面向补丁的审查工件。

## 为什么这很有价值

该演示展示了一个编码 agent 工作流程，其中沙箱可以检查真实的 git 工作树、运行测试、对差异进行推理，并生成开发人员可以操作的审查工件。清单将 `pypa/sampleproject` 安装在带有 `GitRepo(...)` 的固定引用处。审查合同有意缩小范围：一项调查结果应针对 CI 工作流程，一项调查结果应针对 `src/sample/simple.py` 中缺少的类型提示。

## 设置

从存储库根运行 Unix 本地示例：

```bash
uv run python examples/sandbox/tutorials/repo_code_review/main.py
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

该演示在脚本审查后退出，因此生成的工件和评估合同保持确定性。

要在 Docker 中运行相同的审查，请构建一次共享教程映像并传递 
`--docker`：

```bash
docker build -t sandbox-tutorials:latest -f examples/sandbox/tutorials/Dockerfile .
uv run python examples/sandbox/tutorials/repo_code_review/main.py --docker
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

## 预期的工件

- `output/review.md`
- `output/findings.jsonl`
- 可选`output/fix.patch`

## 演示形状

- 输入：固定 git ref 处的 `pypa/sampleproject`，作为 `repo/` 安装到工作区中。
- 运行时原语：沙箱本地 bash、可选文件编辑和类型化的 `RepoReviewResult` 最终输出。
- 工作流程：一名沙箱审核员 agent 就足够了；没有切换，因为任务是线性检查 -> 测试 -> 补丁 -> 总结循环。
- 临时空间：审阅者可以使用 `scratchpad/` 进行注释或草稿差异，然后返回最终审阅对象以供包装器保留。
- 评估：`evals.py` 检查两个发现是否仍集中于测试工作流程中的 `uv` 并在 `src/sample/simple.py` 中键入提示，并且补丁仅编辑 `simple.py`。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Repo code review

## Goal

Review a small public git repository, run its tests, leave line-level review comments in the structured output, and write a patch-oriented review artifact.

## Why this is valuable

This demo shows a coding-agent workflow where the sandbox can inspect a real git worktree, run tests, reason over a diff, and produce review artifacts that a developer can act on. The manifest mounts `pypa/sampleproject` at a pinned ref with `GitRepo(...)`. The review contract is intentionally narrow: one finding should target the CI workflow, and one should target the missing type hints in `src/sample/simple.py`.

## Setup

Run the Unix-local example from the repository root:

```bash
uv run python examples/sandbox/tutorials/repo_code_review/main.py
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

This demo exits after the scripted review so the generated artifacts and eval contract stay deterministic.

To run the same review in Docker, build the shared tutorial image once and pass
`--docker`:

```bash
docker build -t sandbox-tutorials:latest -f examples/sandbox/tutorials/Dockerfile .
uv run python examples/sandbox/tutorials/repo_code_review/main.py --docker
uv run python examples/sandbox/tutorials/repo_code_review/evals.py
```

## Expected artifacts

- `output/review.md`
- `output/findings.jsonl`
- Optional `output/fix.patch`

## Demo shape

- Inputs: `pypa/sampleproject` at a pinned git ref, mounted into the workspace as `repo/`.
- Runtime primitives: sandbox-local bash, optional file edits, and a typed `RepoReviewResult` final output.
- Workflow: one sandbox reviewer agent is enough here; there is no handoff because the task is a linear inspect -> test -> patch -> summarize loop.
- Scratch space: the reviewer can use `scratchpad/` for notes or draft diffs, then return the final review object for the wrapper to persist.
- Evals: `evals.py` checks that the two findings stay focused on `uv` in the test workflow and type hints in `src/sample/simple.py`, and that the patch only edits `simple.py`.


</details>
