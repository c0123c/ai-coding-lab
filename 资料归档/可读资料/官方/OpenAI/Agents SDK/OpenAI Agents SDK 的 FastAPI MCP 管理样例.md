# OpenAI Agents SDK 的 FastAPI MCP 管理样例

通过 MCPServerManager 集中管理服务生命周期，介绍启动服务、应用、冒烟测试和切换配置；属于官方代码样例。

- 来源：OpenAI／Agents SDK
- 资料类型：存档文档
- 存档版本：`125efa029b4b`。
- 文档时间：源码提交（存档版）：[2026-08-05](<https://github.com/openai/openai-agents-python/commit/8f7e6d763c6a623c296b41a606c6dba0d5c3c19f>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-agents-python/blob/main/examples/mcp/manager_example/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Agents SDK/OpenAI Agents SDK 的 FastAPI MCP 管理样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# MCP 经理示例 (FastAPI)

此存储库示例以 MCP Python SDK v2 为目标，旨在与存储库的锁定开发环境一起运行。 Agents SDK 客户端本身支持 MCP v1 和 v2。

此示例演示如何使用 `MCPServerManager` 将 MCP 服务器生命周期管理保留在具有 Streamable HTTP 传输的 FastAPI 应用程序内的单个任务中。

## 运行 MCP 服务器（可流式传输 HTTP）

```
uv run python examples/mcp/manager_example/mcp_server.py
```

服务器默认监听`http://localhost:8000/mcp`。

您可以使用以下命令覆盖 host/port：

```
export STREAMABLE_HTTP_HOST=127.0.0.1
export STREAMABLE_HTTP_PORT=8000
```

此示例还在 `http://localhost:8001/mcp` 处配置了一个不活动的 MCP 服务器，以演示管理器如何删除失败的 
 服务器。您可以使用以下方法覆盖它：

```
export INACTIVE_MCP_SERVER_URL=http://localhost:8001/mcp
```

## 运行FastAPI应用程序

```
uv run python examples/mcp/manager_example/app.py
```

该应用程序在 `http://127.0.0.1:9001` 处侦听。

## 运行冒烟测试

要验证 MCP 管理器和应用程序集成而不调用模型：

```
uv run python -m examples.mcp.manager_example.smoke_test
```

冒烟测试在临时端口上启动本地 MCP 服务器，将应用程序 MCP 服务器设置指向该服务器，并检查 `/health`、`/tools` 和 `/add`。

## 切换 MCP 管理器使用

默认情况下，应用程序使用 `MCPServerManager`。要禁用它：

```
export USE_MCP_MANAGER=0
```

## 尝试端点

```
curl http://127.0.0.1:9001/health
curl http://127.0.0.1:9001/tools
curl -X POST http://127.0.0.1:9001/add \
  -H 'Content-Type: application/json' \
  -d '{"a": 2, "b": 3}'
```

重新连接失败的 MCP 服务器（必须启用管理器）：

```
curl -X POST http://127.0.0.1:9001/reconnect \
  -H 'Content-Type: application/json' \
  -d '{"failed_only": true}'
```

要使用`/run`，请设置`OPENAI_API_KEY`：

```
export OPENAI_API_KEY=...
curl -X POST http://127.0.0.1:9001/run \
  -H 'Content-Type: application/json' \
  -d '{"input": "Add 4 and 9."}'
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP Manager Example (FastAPI)

This repository example targets MCP Python SDK v2 and is intended to run with the repository's locked development environment. The Agents SDK client itself supports both MCP v1 and v2.

This example shows how to use `MCPServerManager` to keep MCP server lifecycle management in a single task inside a FastAPI app with the Streamable HTTP transport.

## Run the MCP server (Streamable HTTP)

```
uv run python examples/mcp/manager_example/mcp_server.py
```

The server listens at `http://localhost:8000/mcp` by default.

You can override the host/port with:

```
export STREAMABLE_HTTP_HOST=127.0.0.1
export STREAMABLE_HTTP_PORT=8000
```

This example also configures an inactive MCP server at `http://localhost:8001/mcp` to demonstrate how the manager drops failed
servers. You can override it with:

```
export INACTIVE_MCP_SERVER_URL=http://localhost:8001/mcp
```

## Run the FastAPI app

```
uv run python examples/mcp/manager_example/app.py
```

The app listens at `http://127.0.0.1:9001`.

## Run the smoke test

To verify the MCP manager and app integration without calling a model:

```
uv run python -m examples.mcp.manager_example.smoke_test
```

The smoke test starts the local MCP server on a temporary port, points both app MCP server settings at that server, and checks `/health`, `/tools`, and `/add`.

## Toggle MCP manager usage

By default, the app uses `MCPServerManager`. To disable it:

```
export USE_MCP_MANAGER=0
```

## Try the endpoints

```
curl http://127.0.0.1:9001/health
curl http://127.0.0.1:9001/tools
curl -X POST http://127.0.0.1:9001/add \
  -H 'Content-Type: application/json' \
  -d '{"a": 2, "b": 3}'
```

Reconnect failed MCP servers (manager must be enabled):

```
curl -X POST http://127.0.0.1:9001/reconnect \
  -H 'Content-Type: application/json' \
  -d '{"failed_only": true}'
```

To use `/run`, set `OPENAI_API_KEY`:

```
export OPENAI_API_KEY=...
curl -X POST http://127.0.0.1:9001/run \
  -H 'Content-Type: application/json' \
  -d '{"input": "Add 4 and 9."}'
```


</details>
