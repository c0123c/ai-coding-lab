# Deep Research 的 MCP 搜索与读取样例

展示面向文件存储的 Search 与 Fetch 工具、运行方法、请求及流程；属于检索服务实现示例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2025-06-26](<https://github.com/openai/openai-cookbook/commit/012c79d369c3af2afd2dc653f675ee014956e749>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/deep_research_api/how_to_build_a_deep_research_mcp_server/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/Deep Research 的 MCP 搜索与读取样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# MCP 用于深入研究

这是深度研究风格 MCP 服务器的最小示例，用于从 OpenAI 文件存储服务搜索和获取文件。

有关 _ 如何 _ 从响应 API 调用此服务的参考，通过深入研究，请参阅 [ 这本食谱 ](<https://cookbook.openai.com/examples/deep_research_api/introduction_to_deep_research_api>)。要了解如何使用 Agents SDK 调用 MCP 服务器，请查看 [ 这本食谱 ](<https://cookbook.openai.com/examples/deep_research_api/how_to_use_deep_research_API_agents>)！

深度研究 agent 特别依赖于搜索和获取工具。搜索应该在您的对象存储中查找一组特定的 top-k ID。 Fetch，是一个以objectIds为参数，拉取相关资源的工具。

## 设置并运行

将您的内部文件存储在 [OpenAI 矢量存储 ](<https://platform.openai.com/storage/vector_stores/>) 中

Python设置：

```shell
python3 -m venv env
source env/bin/activate
pip install -r requirements.txt
```

运行服务器：

```shell
python main.py
```

服务器将使用 SSE 传输在 `http://0.0.0.0:8000/sse/` 上启动。如果您想从公共互联网访问服务器，有多种方法可以实现，包括使用 ngrok：

```shell
brew install ngrok 
ngrok config add-authtoken <your_token>
ngrok http 8000
```

您现在应该能够从客户端访问本地服务器。

## 文件

- `main.py`: [主服务器代码](<https://github.com/openai/openai-cookbook/blob/main/examples/deep_research_api/how_to_build_a_deep_research_mcp_server/main.py>)

## MCP 服务器的示例流程图

![../../../images/mcp_dr.png](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/images/mcp_dr.png>)

## 请求示例

```python
# system_message includes reference to internal file lookups for MCP.
system_message = """
You are a professional researcher preparing a structured, data-driven report on behalf of a global health economics team. Your task is to analyze the health question the user poses.

Do:
- Focus on data-rich insights: include specific figures, trends, statistics, and measurable outcomes (e.g., reduction in hospitalization costs, market size, pricing trends, payer adoption).
- When appropriate, summarize data in a way that could be turned into charts or tables, and call this out in the response (e.g., "this would work well as a bar chart comparing per-patient costs across regions").
- Prioritize reliable, up-to-date sources: peer-reviewed research, health organizations (e.g., WHO, CDC), regulatory agencies, or pharmaceutical earnings reports.
- Include an internal file lookup tool to retrieve information from our own internal data sources. If you've already retrieved a file, do not call fetch again for that same file. Prioritize inclusion of that data.
- Include inline citations and return all source metadata.

Be analytical, avoid generalities, and ensure that each section supports data-backed reasoning that could inform healthcare policy or financial modeling.
"""

user_query = "Research the economic impact of semaglutide on global healthcare systems."

response = client.responses.create(
  model="o3-deep-research-2025-06-26",
  input=[
    {
      "role": "developer",
      "content": [
        {
          "type": "input_text",
          "text": system_message,
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "input_text",
          "text": user_query,
        }
      ]
    }
  ],
  reasoning={
    "summary": "auto"
  },
  tools=[
    {
      "type": "web_search_preview"
    },
    { # ADD MCP TOOL SUPPORT
      "type": "mcp",
      "server_label": "internal_file_lookup",
      "server_url": "http://0.0.0.0:8000/sse/", # Update to the location of *your* MCP server
      "require_approval": "never"
    }
  ]
)


## 原文对照

<details>
<summary>展开完整原文</summary>

# MCP for Deep Research

This is a minimal example of a Deep Research style MCP server for searching and fetching files from the OpenAI file storage service.

For a reference of _how_ to call this service from the Responses API, with Deep Research see [this cookbook](<https://cookbook.openai.com/examples/deep_research_api/introduction_to_deep_research_api>). To see how to call the MCP server with the Agents SDK, checkout [this cookbook](<https://cookbook.openai.com/examples/deep_research_api/how_to_use_deep_research_API_agents>)!

The Deep Research agent relies specifically on Search and Fetch tools. Search should look through your object store for a set of specfic, top-k IDs. Fetch, is a tool that takes objectIds as arguments and pulls back the relevant resources.

## Set up & run

Store your internal file(s) in [OpenAI Vector Storage](<https://platform.openai.com/storage/vector_stores/>)

Python setup:

```shell
python3 -m venv env
source env/bin/activate
pip install -r requirements.txt
```

Run the server:

```shell
python main.py
```

The server will start on `http://0.0.0.0:8000/sse/` using SSE transport. If you want to reach the server from the public internet, there are a variety of ways to do that including with ngrok:

```shell
brew install ngrok 
ngrok config add-authtoken <your_token>
ngrok http 8000
```

You should now be able to reach your local server from your client. 

## Files

- `main.py`: [Main server code](<https://github.com/openai/openai-cookbook/blob/main/examples/deep_research_api/how_to_build_a_deep_research_mcp_server/main.py>)

## Example Flow diagram for MCP Server

![../../../images/mcp_dr.png](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/images/mcp_dr.png>)

## Example request

```python
# system_message includes reference to internal file lookups for MCP.
system_message = """
You are a professional researcher preparing a structured, data-driven report on behalf of a global health economics team. Your task is to analyze the health question the user poses.

Do:
- Focus on data-rich insights: include specific figures, trends, statistics, and measurable outcomes (e.g., reduction in hospitalization costs, market size, pricing trends, payer adoption).
- When appropriate, summarize data in a way that could be turned into charts or tables, and call this out in the response (e.g., "this would work well as a bar chart comparing per-patient costs across regions").
- Prioritize reliable, up-to-date sources: peer-reviewed research, health organizations (e.g., WHO, CDC), regulatory agencies, or pharmaceutical earnings reports.
- Include an internal file lookup tool to retrieve information from our own internal data sources. If you've already retrieved a file, do not call fetch again for that same file. Prioritize inclusion of that data.
- Include inline citations and return all source metadata.

Be analytical, avoid generalities, and ensure that each section supports data-backed reasoning that could inform healthcare policy or financial modeling.
"""

user_query = "Research the economic impact of semaglutide on global healthcare systems."

response = client.responses.create(
  model="o3-deep-research-2025-06-26",
  input=[
    {
      "role": "developer",
      "content": [
        {
          "type": "input_text",
          "text": system_message,
        }
      ]
    },
    {
      "role": "user",
      "content": [
        {
          "type": "input_text",
          "text": user_query,
        }
      ]
    }
  ],
  reasoning={
    "summary": "auto"
  },
  tools=[
    {
      "type": "web_search_preview"
    },
    { # ADD MCP TOOL SUPPORT
      "type": "mcp",
      "server_label": "internal_file_lookup",
      "server_url": "http://0.0.0.0:8000/sse/", # Update to the location of *your* MCP server
      "require_approval": "never"
    }
  ]
)


</details>
