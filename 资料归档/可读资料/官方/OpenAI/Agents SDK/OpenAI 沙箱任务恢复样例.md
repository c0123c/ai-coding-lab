# OpenAI 沙箱任务恢复样例

先构建并冒烟测试应用，再保存和恢复沙箱状态并补充 pytest，展示跨运行保留上下文与开发成果。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-07-09](<https://github.com/openai/openai-agents-python/commit/133208381c0467e3a2ff098593fc02a50f78d0d3>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/sandbox/tutorials/sandbox_resume/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/OpenAI 沙箱任务恢复样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 沙盒简历

此示例显示了一个小型沙箱恢复流程，其中 `AGENTS.md` 安装在沙箱中并加载到 agent 指令中。它分两个 
 步骤运行：首先构建应用程序并对其进行冒烟测试，然后序列化 
 沙箱会话状态，恢复沙箱并添加 pytest 覆盖范围。

默认情况下，agent 构建一个小型仓库机器人状态 API，对其进行冒烟测试，然后恢复相同的沙箱以添加测试。沙箱工作区以
一个指令文件开始：

- `AGENTS.md` 包含构建 FastAPI 应用程序的说明，使用类型提示和 Pydantic，使用 `uv` 安装依赖项，通过 `uv run python` 运行 Python 命令，并在完成之前进行本地测试。

从存储库根运行示例：

```bash
uv run python examples/sandbox/tutorials/sandbox_resume/main.py
```

该演示在脚本化恢复流程后退出，因此序列化会话状态和恢复步骤易于遵循。

您可以覆盖模型或提示：

```bash
uv run python examples/sandbox/tutorials/sandbox_resume/main.py --model gpt-5.6-sol --question "Build a FastAPI service that exposes a warehouse robot's maintenance status."
```

要在 Docker 中运行相同的流程，请构建一次共享教程映像并传递 
`--docker`：

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/sandbox_resume/main.py --docker
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Sandbox resume

This example shows a small sandbox resume flow with `AGENTS.md` mounted in the sandbox and loaded into the agent instructions. It runs in two
steps: first it builds the app and smoke tests it, then it serializes the
sandbox session state, resumes the sandbox, and adds pytest coverage.

By default the agent builds a tiny warehouse-robot status API, smoke-tests it, then resumes the same sandbox to add tests. The sandbox workspace starts with
one instruction file:

- `AGENTS.md` with instructions to build FastAPI apps, use type hints and Pydantic, install dependencies with `uv`, run Python commands through `uv run python`, and test locally before finishing.

Run the example from the repository root:

```bash
uv run python examples/sandbox/tutorials/sandbox_resume/main.py
```

This demo exits after the scripted resume flow so the serialized session state and resume step stay easy to follow.

You can override the model or prompt:

```bash
uv run python examples/sandbox/tutorials/sandbox_resume/main.py --model gpt-5.6-sol --question "Build a FastAPI service that exposes a warehouse robot's maintenance status."
```

To run the same flow in Docker, build the shared tutorial image once and pass
`--docker`:

```bash
docker build --tag sandbox-tutorials:latest examples/sandbox/tutorials
uv run python examples/sandbox/tutorials/sandbox_resume/main.py --docker
```


</details>
