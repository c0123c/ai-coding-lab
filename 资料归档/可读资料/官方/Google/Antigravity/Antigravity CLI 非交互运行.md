# Antigravity CLI 非交互运行

介绍通过命令执行单次任务并获取文本、JSON 或流式结果，涵盖结构化输出、认证和自动化集成。

- 来源：Google／Antigravity
- 资料类型：存档文档
- 文档时间：官方未公开发表/更新时间（[已复查](<https://antigravity.google/docs/cli/headless/>)）；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://antigravity.google/docs/cli/headless/>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Antigravity/Antigravity CLI 非交互运行.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 无头模式

以非交互方式运行 Antigravity CLI 以编写 agent 任务脚本，与 CI 管道集成，并捕获机器可读的输出。

无头模式（也称为打印模式）向 agent 发送单个提示，流式传输或返回响应，然后退出。当您在程序中需要 agent 的输出而不是终端 UI 时，请使用它。

## 运行单个提示

使用 -p （或其别名 --print 和 --prompt ）传递提示符以运行一次并退出：

```
agy\n -p\n "In one sentence, what is a git rebase?"
```

```
A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.
```

响应发送至 stdout 。诊断 — 错误、身份验证提示、进度和权限通知 — 转到 stderr 。这种分割使捕获的响应保持干净：

```
# Capture only the model response; diagnostics still print to the terminal.\n
\nanswer\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n)
```

注意：无头模式使用您缓存的凭据。首先使用 
 交互式 agy 会话进行一次身份验证。在没有 
 终端的非交互式环境中（例如，CI），尚未经过身份验证的运行会退出并出现 
 需要身份验证的错误，而不是挂起。

注意：无头模式使用您缓存的凭据。首先使用 
 交互式 agy 会话进行一次身份验证。在没有 
 终端的非交互式环境中（例如，CI），尚未经过身份验证的运行会退出并出现 
 需要身份验证的错误，而不是挂起。

## 输出格式

--output-format 标志控制 stdout 的形状。它接受三个值：

格式标准输出形状 将其用于文本 响应文本（默认） 人类可读的输出，快速脚本 json 完成时打印的一个 JSON 对象 捕获结果和元数据 Stream-json 换行符分隔的 JSON (NDJSON) 事件 监控进度、工具和令牌使用情况

### 文字

这是默认格式。响应文本直接发送到标准输出，没有换行：

```
agy\n -p\n "In one sentence, what does the command git bisect do?"
```

```
Git bisect executes a binary search algorithm across a project's commit history to rapidly isolate the precise commit responsible for introducing a defect.
```

### JSON

设置 --output-format json 以在运行完成后获取单个 JSON 信封。 CLI 在一行上发出它；通过 jq 进行管道打印：

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n json\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"055a398f-db14-4c5f-abbb-1bf03f8120a7"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.\n\n\n"\n,\n
\n  "duration_seconds"\n: \n7.16\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "usage"\n: {\n
\n    "input_tokens"\n: \n10415\n,\n
\n    "output_tokens"\n: \n657\n,\n
\n    "thinking_tokens"\n: \n616\n,\n
\n    "cache_read_tokens"\n: \n8113\n,\n
\n    "total_tokens"\n: \n11072\n
\n  }\n
\n}
```

信封包含以下字段：

字段 类型 描述 conversation_id 会话的字符串 ID，用于恢复稍后状态字符串 终端状态（参见状态值） 响应字符串 agent 的自由文本响应错误字符串 错误消息；仅在失败时出现 duration_seconds 数字 运行的挂钟持续时间 num_turns 数字 对话中的用户轮数 structured_output 对象 解析的模式输出；仅与 --json-schema json_schema 对象一起存在 强制实施的架构；仅与 --json-schema 使用对象一起出现 令牌计数： input_tokens 、 output_tokens 、 thinking_tokens 、 cache_read_tokens 、 total_tokens

#### 具有模式的结构化输出

传递 --json-schema 将答案限制为模式。解析后的对象出现在 structured_output 中，并且响应包含序列化为字符串的相同有效负载：

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"4e502687-290c-4030-b908-5ed6c68fa5dc"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"{\n\"\nmajor\n\"\n:2,\n\"\nminor\n\"\n:14,\n\"\npatch\n\"\n:3}\n\n\n"\n,\n
\n  "duration_seconds"\n: \n4.45\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "structured_output"\n: { \n"major"\n: \n2\n, \n"minor"\n: \n14\n, \n"patch"\n: \n3\n },\n
\n  "json_schema"\n: {\n
\n    "type"\n: \n"object"\n,\n
\n    "properties"\n: {\n
\n      "major"\n: { \n"type"\n: \n"integer"\n },\n
\n      "minor"\n: { \n"type"\n: \n"integer"\n },\n
\n      "patch"\n: { \n"type"\n: \n"integer"\n }\n
\n    },\n
\n    "required"\n: [\n"major"\n, \n"minor"\n, \n"patch"\n]\n
\n  },\n
\n  "usage"\n: { \n"input_tokens"\n: \n10522\n, \n"output_tokens"\n: \n354\n, \n"thinking_tokens"\n: \n329\n, \n"cache_read_tokens"\n: \n8112\n, \n"total_tokens"\n: \n10876\n }\n
\n}
```

该标志接受架构字符串、.json 架构文件的路径或基本类型名称（ string 、 number 、 integer 、 boolean ）。从 structured_output 读取解析值：

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n \\n
\n  |\n jq\n '.structured_output'
```

### 流媒体 JSON

设置 --output-format stream-json 以在运行过程中每行 (NDJSON) 发出一个 JSON 对象。使用此格式可以实时观察工具调用和令牌使用情况。

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json
```

该流以一个 init 事件开始，后跟任意数量的 step_update 事件，并以一个结果事件结束（cwd 和 tools 数组在以下示例中缩写）：

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.28\n,\n"usage"\n:{\n"input_tokens"\n:\n10302\n,\n"output_tokens"\n:\n582\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n10884\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"checkpoint"\n,\n"duration_seconds"\n:\n0.53\n,\n"usage"\n:{\n"input_tokens"\n:\n116\n,\n"output_tokens"\n:\n7\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n123\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.88\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n10418\n,\n"output_tokens"\n:\n589\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n11007\n}}}
```

当响应以块形式传输时， agent_response 步骤会在其最终 DONE 之前发出一个或多个携带部分 text_delta 片段的 ACTIVE 事件；像这样的简短响应会以单个 DONE 形式到达。

每一行都是一个事件对象，其事件字段命名其类型：

事件 有效负载密钥 发出 init init 一次，在流开始时 step_update step_update 对于每个步骤转换或文本增量结果结果 一次，在结束时（与 json 形状相同）

初始化负载记录了运行配置。型号和 agent 仅在设置 --model 或 --agent 时出现； permission_mode 默认情况下是请求审核（并且始终在 --dangerously-skip-permissions 下继续）：

字段类型 说明 cwd string 工作目录工具 string[] 所有可用工具的名称 permission_mode string 有效权限模式模型 string 覆盖时使用的模型 agent string 覆盖时活动 agent json_schema 对象 强制模式，设置为--json-schema

每个 step_update 有效负载描述一个步骤。观察到的 step_type 值包括 user_input 、 agent_response 、 tool 和 checkpoint ；步骤运行时状态为 ACTIVE，完成时状态为 DONE：

字段 类型 说明 conversation_id 会话的字符串 ID step_index 数字 步骤状态的从零开始的索引 字符串 ACTIVE 或 DONE step_type 字符串 步骤类别，例如 agent_response 或工具tool_name 字符串 规范工具名称，关于工具步骤 text_delta 字符串 增量响应文本 duration_seconds 数字 步骤持续时间，已知使用情况时 对象 每步骤令牌使用情况，已知时 tool_info 对象 工具调用详细信息 subagent_info 对象 子代理调用详细信息

#### 流中的工具调用

在工具步骤上，tool_info 携带调用及其结果。这是执行 echo hello_headless_demo 的运行中的真实工具步骤：

```
{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"edb1c8c1-50ba-4f3f-87eb-412d0e9d47c3"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"tool"\n,\n"tool_name"\n:\n"run_command"\n,\n"duration_seconds"\n:\n0.07\n,\n"tool_info"\n:{\n"name"\n:\n"run_command"\n,\n"parameters"\n:{\n"CommandLine"\n:\n"echo hello_headless_demo"\n},\n"output"\n:\n"hello_headless_demo\n\r\n\n"\n}}}
```

tool_info 保存 name 、parameters 、output ，以及（当工具失败时）带有 type 和 message 的错误对象。生成子代理的步骤改为携带 subagent_info，在子代理下列出每个子代理（使用 type_name 、 role 、 conversation_id 、 log_uri 和 workspace_uris ）。

#### 流中的结构化输出

对于 --json-schema ，架构适用于最终结果事件，该事件携带与 json 信封相同的 structured_output 和 json_schema 字段。

## 使用 jq 解析输出

stdout 是机器可读的，因此 jq 提取您需要的内容。

从 JSON 运行获取响应文本：

```
agy\n -p\n "Name three popular version control systems, comma-separated."\n --output-format\n json\n |\n jq\n -r\n '.response'
```

```
Git, Subversion, Mercurial.
```

在流文本到达时将其连接起来：

```
agy\n -p\n "Explain what a merge conflict is in two sentences."\n --output-format\n stream-json\n \\n
\n  |\n jq\n -j\n 'select(.event=="step_update") | .step_update.text_delta // empty'
```

从终端结果事件中读取令牌使用情况：

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json\n \\n
\n  |\n jq\n 'select(.event=="result") | .result.usage'
```

提示：连接 text_delta 片段 
 时使用 jq -j （连接输出），这样 jq 就不会在它们之间插入换行符。

提示：连接 text_delta 片段 
 时使用 jq -j （连接输出），这样 jq 就不会在它们之间插入换行符。

## Continue 一段对话

默认情况下，无头运行是无状态的。使用 --continue ( -c ) 恢复最近对话的先前上下文，或使用 --conversation 恢复之前运行的 conversation_id 中的 ID ：

```
# Continue the most recent conversation.\n
\nagy\n -p\n "Now explain your previous answer in more detail"\n --continue\n
\n
\n# Resume a specific conversation by ID.\n
\nagy\n -p\n "Summarize what we discussed"\n --conversation\n 055a398f-db14-4c5f-abbb-1bf03f8120a7
```

其中每一个都会启动一个新的过程。要在一个进程内运行多轮，请参阅 stdin 中的流提示。

## 来自 stdin 的流提示

使用 --input-format Stream-json 来维护单个、连续的对话过程，并在标准输入 (stdin) 上一一提示。每个提示都会执行一个完整的回合并发出其自己的结果事件。

此方法非常适合根据上一个答案动态确定下一个提示的应用程序。由于该进程仅启动一次，因此后续轮次会跳过启动开销并重用预热对话。这使得它比使用 --continue 运行重复命令要快得多。

注意：--input-formatstream-json需要--output-formatstream-json。
在流会话中，CLI每回合发出一个结果事件。

注意：--input-formatstream-json需要--output-formatstream-json。
在流会话中，CLI每回合发出一个结果事件。

### 发送提示

每行将一个 JSON 对象写入 stdin 。事件键指定消息类型（与输出流格式匹配）。提示表示为带有消息的用户事件：

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly the word: apple. Nothing else."\n } }
```

您可以将多个提示通过管道传输到单个会话中：

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly the word: apple. Nothing else."}}'\n \\n
\n  '{"event":"user","message":{"content":"What word did I ask you to reply with in my previous message? Answer with just that word."}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json
```

内容字段接受标准字符串或文本块列表。以下两种格式是等效的：

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly: banana"\n } }\n
\n{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: [{ \n"type"\n: \n"text"\n, \n"text"\n: \n"Reply with exactly: banana"\n }] } }
```

文本是唯一受支持的块类型。提交任何其他块类型会以错误消息结束会话，而不是默默地删除该块。这可确保 agent 永远不会回答您未明确发送的提示。

### 阅读结果

输出流的工作原理如下：

- 以单个 init 事件打开。

- 为活动回合发出一系列 step_update 事件。

- 以最终结果事件结束回合。

此示例显示了上一节中的两个提示符 Bash 命令的输出（缩写的 init 有效负载）：

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"ACTIVE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"\n\n\n"\n,\n"duration_seconds"\n:\n1.169607627\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n1.427806958\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n0.895679386\n,\n"usage"\n:{\n"input_tokens"\n:\n278\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n282\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n2.548755756\n,\n"num_turns"\n:\n2\n,\n"usage"\n:{\n"input_tokens"\n:\n30662\n,\n"output_tokens"\n:\n8\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n30670\n}}}
```

第二回合从第一回合的上下文中回答苹果。请注意，单个 conversation_id 跟踪整个会话，并且 init 仅发送一次。

解析结果对象时，请记住响应文本仅适用于当前回合，而元数据计数器跟踪累积会话：

字段范围响应 发出它的回合 num_turns 会话使用情况累积 会话累积 duration_seconds 会话累积

要过滤并仅查看最终响应，您可以通过 jq 管道输出：

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: one"}}'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: two"}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json\n \\n
\n  |\n jq\n -r\n 'select(.event=="result") | "\(.result.num_turns): \(.result.response)"'
```

```
1: one\n
\n
\n2: two
```

### 以编程方式驱动会话

您可以在脚本中保持标准输入管道打开，而不是预先传递所有提示。这允许您的应用程序在提交下一个提示之前评估模型的答案。

提示：您可以逐行读取 stdout，并根据事件字段调度逻辑。请等待，直到收到当前 
 提示的结果事件，然后再写入下一个提示。

提示：您可以逐行读取 stdout，并根据事件字段调度逻辑。请等待，直到收到当前 
 提示的结果事件，然后再写入下一个提示。

例如：

```
import\n json\n
\nimport\n subprocess\n
\n
\nproc \n=\n subprocess.Popen(\n
\n    [\n"agy"\n, \n"--input-format"\n, \n"stream-json"\n, \n"--output-format"\n, \n"stream-json"\n],\n
\n    stdin\n=\nsubprocess.\nPIPE\n,\n
\n    stdout\n=\nsubprocess.\nPIPE\n,\n
\n    text\n=\nTrue\n,\n
\n    bufsize\n=\n1\n,\n
\n)\n
\n
\n
\ndef\n ask\n(prompt):\n
\n    """Send one prompt and return the response for that turn."""\n
\n    message \n=\n {\n"event"\n: \n"user"\n, \n"message"\n: {\n"content"\n: prompt}}\n
\n    proc.stdin.write(json.dumps(message) \n+\n "\n\n\n"\n)\n
\n    proc.stdin.flush()\n
\n    for\n line \nin\n proc.stdout:\n
\n        event \n=\n json.loads(line)\n
\n        if\n event[\n"event"\n] \n==\n "result"\n:\n
\n            return\n event[\n"result"\n][\n"response"\n]\n
\n
\n
\nfirst \n=\n ask(\n"Name one popular version control system. Answer with one word."\n)\n
\nprint\n(ask(\nf\n"Name a competitor to \n{\nfirst.strip()\n}\n. Answer with one word."\n))\n
\n
\nproc.stdin.close()\n
\nproc.wait()
```

### 结束会话

要正常关闭会话，请关闭 stdin 。输入管道关闭且当前回合完成后，进程退出。如果应用程序写入最终提示并立即关闭管道，它仍然会在进程终止之前收到最终结果。

干净会话以 0 退出，这与标准无头模式行为相匹配。

### 不支持的消息

为了防止不可预测的行为，CLI 会验证输入。如果遇到格式错误或不受支持的消息，它将按下表所述进行响应：

输入结果 退出 无法识别的事件名称 已跳过：警告已记录到 stderr — control_request 或 control_response 事件 ERROR 结果，会话结束 2 由 CLI 处理的斜杠命令，例如 /model ERROR 结果，会话结束 2 消息缺少事件字段 ERROR 结果，会话结束 1 无效 JSON 行 ERROR 结果，会话结束 1 文本以外的内容块类型ERROR 结果，会话结束 1

无法识别的事件名称将被安全地跳过并发出警告。这可以确保针对较新版本的流协议构建的应用程序在较旧的 CLI 版本上运行时不会崩溃：

```
warning: ignoring unsupported stream input message event "future_thing"
```

对于所有其他错误，会话将立即终止。之前完成的任何回合都会保留其结果事件，但格式错误的输入行会中止会话的其余部分。

CLI 可以直接响应的斜杠命令（例如 /model 和 /usage ）生成文本报告而不是标准事件流。流会话不能使用这些类型的斜杠命令。例如：

```
{ \n"event"\n: \n"result"\n, \n"result"\n: { \n"conversation_id"\n: \n"4fae3a70-409d-42a4-86ea-9de206a49ff4"\n, \n"status"\n: \n"ERROR"\n, \n"response"\n: \n""\n, \n"error"\n: \n"/model is answered by the CLI itself and is unavailable with --input-format stream-json; run it as its own --print /model invocation"\n, \n"duration_seconds"\n: \n0\n, \n"num_turns"\n: \n0\n, \n"usage"\n: { \n"input_tokens"\n: \n0\n, \n"output_tokens"\n: \n0\n, \n"thinking_tokens"\n: \n0\n, \n"cache_read_tokens"\n: \n0\n, \n"total_tokens"\n: \n0\n } } }
```

### 常见错误

错误 失败原因 如何修复 与 --output-format json 或文本配对 这些格式在进程退出时仅发出单个输出信封，导致除最后一个之外的每一轮都会丢失。在此输入模式下始终使用 --output-formatstream-json。使用 -p 标志传递提示 流模式专门侦听 stdin 上的提示。通过命令行标志传递的任何提示都会被删除。而是将提示作为用户消息发送到 stdin。将 /model 或 /usage 发送到流中 CLI 在事件流外部内部处理这些命令，从而中断 JSON 流。将 agy -p /model 作为完全独立的命令运行。将 num_turns 视为每轮计数元数据计数器（如轮次、持续时间和使用情况）跟踪整个累积会话，而不仅仅是活动轮次。使用响应字段获取当前回合的文本。在读取 stdout 之前等待进程退出 会话无限期地保持打开状态，直到 stdin 关闭。如果您的脚本等待退出信号，它将挂起。当事件到达时逐行读取它们，并在完成后手动关闭标准输入。

## 选择模型、工作量或 agent

列出可用的模型段，然后为运行固定第一个：

```
agy\n models
```

```
gemini-3.8-flash-high     Gemini 3.8 Flash (High)
gemini-3.8-flash-medium   Gemini 3.8 Flash (Medium)
gemini-3.7-flash-high     Gemini 3.7 Flash (High)
gemini-3.7-flash-medium   Gemini 3.7 Flash (Medium)
gemini-3.6-flash-high     Gemini 3.6 Flash (High)
gemini-3.6-flash-medium   Gemini 3.6 Flash (Medium)
gemini-3.1-pro-high       Gemini 3.1 Pro (High)
...
```

```
# Pin a model by slug.\n
\nagy\n -p\n "Reverse the string antigravity."\n --model\n gemini-3.8-flash-medium\n
\n
\n# Set reasoning effort (low, medium, or high).\n
\nagy\n -p\n "Outline a plan to add caching to this service."\n --effort\n high\n
\n
\n# Select an agent (list them with `agy agents`).\n
\nagy\n -p\n "Review this function for edge cases."\n --agent\n <\nagent-nam\ne\n>
```

与交互式 UI 不同，当 --model 命名未知模型时，无头模式不会默默地退回。它以非零状态退出，状态为 ERROR，因此固定管道会大声失败，而不是运行错误的模型。

## 无头模式下的权限

无头模式下没有交互式提示，因此通常要求确认的工具由策略处理。

默认情况下，CLI 遵循您设置中的权限模式。需要批准但无法获得的工具会被软拒绝：运行继续，退出 0 ，并向 stderr 打印一条通知，命名该工具以及如何允许它。自动允许在活动工作区中读取和写入文件； shell 命令等操作默认为“询问”，并且在无头模式下会被软拒绝，除非您授予它们。

通过在 ~/.gemini/antigravity-cli/settings.json 中的 requests.allow 下添加操作（目标）规则来提前授予工具：

```
{\n
\n  "permissions"\n: {\n
\n    "allow"\n: [\n
\n      "command(git)"\n,\n
\n      "command(regex:npm run (build|lint|test))"\n,\n
\n      "write_file(src/)"\n
\n    ]\n
\n  }\n
\n}
```

要自动批准运行的每个工具，请传递 --dangerously-skip-permissions ：

```
agy\n -p\n "Run the test suite and report failures"\n --dangerously-skip-permissions
```

警告：--dangerously-skip-permissions批准所有工具调用，
包括文件写入和命令执行。优先选择范围权限。除非您完全信任提示和环境，否则允许规则。
请参阅权限以了解完整的规则语法。

警告：--dangerously-skip-permissions批准所有工具调用，
包括文件写入和命令执行。优先选择范围权限。除非您完全信任提示和环境，否则允许规则。
请参阅权限以了解完整的规则语法。

## 处理退出代码和错误

成功运行退出 0 。无法产生响应的运行将以非零值退出并将原因写入 stderr 。在 json 和stream-json 模式下，失败也会出现在状态和错误字段中。

例如，固定未知模型会退出 1 并返回错误包络：

```
agy\n -p\n "hi"\n --model\n does-not-exist-model\n --output-format\n json\n; \necho\n "exit=\n$?\n"
```

```
{\n"conversation_id"\n:\n""\n,\n"status"\n:\n"ERROR"\n,\n"response"\n:\n""\n,\n"error"\n:\n"invalid model selection (--model \n\"\ndoes-not-exist-model\n\"\n --effort \n\"\"\n): model does-not-exist-model is not recognized as a known model or custom model in settings\n\n\nAvailable models:\n\n\n  Gemini 3.6 Flash (High)\n\n\n  ..."\n,\n"duration_seconds"\n:\n0\n,\n"num_turns"\n:\n0\n,\n"usage"\n:{\n"input_tokens"\n:\n0\n,\n"output_tokens"\n:\n0\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n0\n}}
```

```
exit=1
```

状态字段报告运行的最终状态：

状态 含义 SUCCESS 运行已完成并产生响应 ERROR 运行因错误而结束 CANCELED 运行被取消 INTERRUPTED 运行被中断（例如，SIGINT） INVALID 运行达到无效状态WAITING 等待输入时运行结束 RUNNING 运行未达到最终状态

默认情况下，运行最多等待五分钟以获得响应。使用 --print-timeout 调整上限：

```
agy\n -p\n "Summarize the design tradeoffs of optimistic locking."\n --print-timeout\n 15m
```

## 标志参考

标志 默认 说明 -p 、 --print 、 --prompt — 以非交互方式运行单个提示并打印响应 --output-format 文本 输出格式：text 、 json 或 stream-json --input-format 文本 输入格式：text 或 stream-json ；读取标准输入上的提示 --json-schema - 强制结构化输出的模式字符串或文件路径 --model - 本次运行的模型段（请参阅 agy 模型） --effort - 推理工作：低、中或高 --agent - 本次运行的 Agent（请参阅 agy） agents ) --continue , -c false Continue 最近的对话 --conversation — 通过 ID 恢复对话 --dangerously-skip-permissions false 自动批准所有工具权限请求 --print-timeout 5m 最大时间等待响应 --sandbox false 在启用终端沙箱限制的情况下运行

## 示例：运行CI中的agent

因错误而导致作业失败并保存响应：

```
#!/usr/bin/env bash\n
\nset\n -euo\n pipefail\n
\n
\nresult\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n \\n
\n  --output-format\n json\n \\n
\n  --print-timeout\n 10m\n)\n
\n
\nstatus\n=\n$(\necho\n "\n$result\n"\n |\n jq\n -r\n '.status'\n)\n
\nif\n [[ \n"\n$status\n"\n !=\n "SUCCESS"\n ]]; \nthen\n
\n  echo\n "Agent run failed: $(\necho\n "\n$result\n" \n|\n jq\n -r\n '.error')"\n >&2\n
\n  exit\n 1\n
\nfi\n
\n
\necho\n "\n$result\n"\n |\n jq\n -r\n '.response'\n >\n result.txt
```

## 后续步骤

浏览以下指南以了解有关 CLI 功能的更多信息：

- 提示与互动：为agent编写有效的提示。

- 权限：配置允许、拒绝和询问规则。

- 后台任务和子代理：将工作委派给专门的 agents。

- 参考：完整命令和标志参考。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Headless mode

Run Antigravity CLI non-interactively to script agent tasks, integrate with CI pipelines, and capture machine-readable output.

Headless mode (also called print mode) sends a single prompt to the agent, streams or returns the response, and exits. Use it whenever you need the agent’s output in a program instead of a terminal UI.

## Run a single prompt

Pass a prompt with -p (or its aliases --print and --prompt ) to run once and exit:

```
agy\n -p\n "In one sentence, what is a git rebase?"
```

```
A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.
```

The response goes to stdout . Diagnostics—errors, authentication prompts, progress, and permission notices—go to stderr . This split keeps the captured response clean:

```
# Capture only the model response; diagnostics still print to the terminal.\n
\nanswer\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n)
```

Note: Headless mode uses your cached credentials. Authenticate once with
an interactive agy session first. In a non-interactive environment with no
terminal (for example, CI), a run that isn’t already authenticated exits with
an authentication required error instead of hanging.

Note: Headless mode uses your cached credentials. Authenticate once with
an interactive agy session first. In a non-interactive environment with no
terminal (for example, CI), a run that isn’t already authenticated exits with
an authentication required error instead of hanging.

## Output formats

The --output-format flag controls the shape of stdout . It accepts three values:

Format stdout shape Use it for text The response text (default) Human-readable output, quick scripts json One JSON object printed on completion Capturing a result plus metadata stream-json Newline-delimited JSON (NDJSON) events Monitoring progress, tools, and token usage

### Text

This is the default format. The response text goes straight to stdout with no wrapping:

```
agy\n -p\n "In one sentence, what does the command git bisect do?"
```

```
Git bisect executes a binary search algorithm across a project's commit history to rapidly isolate the precise commit responsible for introducing a defect.
```

### JSON

Set --output-format json to get a single JSON envelope after the run completes. The CLI emits it on one line; pipe through jq to pretty-print:

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n json\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"055a398f-db14-4c5f-abbb-1bf03f8120a7"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"A git rebase rewrites the commit history by transplanting a sequence of commits onto a new base commit, imposing a strictly linear progression of changes that eliminates arbitrary merge artifacts.\n\n\n"\n,\n
\n  "duration_seconds"\n: \n7.16\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "usage"\n: {\n
\n    "input_tokens"\n: \n10415\n,\n
\n    "output_tokens"\n: \n657\n,\n
\n    "thinking_tokens"\n: \n616\n,\n
\n    "cache_read_tokens"\n: \n8113\n,\n
\n    "total_tokens"\n: \n11072\n
\n  }\n
\n}
```

The envelope contains these fields:

Field Type Description conversation_id string ID of the conversation, for resuming later status string Terminal status (refer to Status values ) response string The agent’s free-text response error string Error message; present only on failure duration_seconds number Wall-clock duration of the run num_turns number Number of user turns in the conversation structured_output object Parsed schema output; present only with --json-schema json_schema object The schema that was enforced; present only with --json-schema usage object Token counts: input_tokens , output_tokens , thinking_tokens , cache_read_tokens , total_tokens

#### Structured output with a schema

Pass --json-schema to constrain the answer to a schema. The parsed object appears in structured_output , and response holds the same payload serialized as a string:

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n |\n jq
```

```
{\n
\n  "conversation_id"\n: \n"4e502687-290c-4030-b908-5ed6c68fa5dc"\n,\n
\n  "status"\n: \n"SUCCESS"\n,\n
\n  "response"\n: \n"{\n\"\nmajor\n\"\n:2,\n\"\nminor\n\"\n:14,\n\"\npatch\n\"\n:3}\n\n\n"\n,\n
\n  "duration_seconds"\n: \n4.45\n,\n
\n  "num_turns"\n: \n1\n,\n
\n  "structured_output"\n: { \n"major"\n: \n2\n, \n"minor"\n: \n14\n, \n"patch"\n: \n3\n },\n
\n  "json_schema"\n: {\n
\n    "type"\n: \n"object"\n,\n
\n    "properties"\n: {\n
\n      "major"\n: { \n"type"\n: \n"integer"\n },\n
\n      "minor"\n: { \n"type"\n: \n"integer"\n },\n
\n      "patch"\n: { \n"type"\n: \n"integer"\n }\n
\n    },\n
\n    "required"\n: [\n"major"\n, \n"minor"\n, \n"patch"\n]\n
\n  },\n
\n  "usage"\n: { \n"input_tokens"\n: \n10522\n, \n"output_tokens"\n: \n354\n, \n"thinking_tokens"\n: \n329\n, \n"cache_read_tokens"\n: \n8112\n, \n"total_tokens"\n: \n10876\n }\n
\n}
```

The flag accepts a schema string, a path to a .json schema file, or a primitive type name ( string , number , integer , boolean ). Read the parsed value from structured_output :

```
agy\n -p\n "Parse the semantic version string v2.14.3 into an object with integer fields major, minor, and patch."\n \\n
\n  --output-format\n json\n \\n
\n  --json-schema\n '{"type":"object","properties":{"major":{"type":"integer"},"minor":{"type":"integer"},"patch":{"type":"integer"}},"required":["major","minor","patch"]}'\n \\n
\n  |\n jq\n '.structured_output'
```

### Streaming JSON

Set --output-format stream-json to emit one JSON object per line (NDJSON) as the run progresses. Use this format to observe tool calls and token usage in real time.

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json
```

The stream begins with one init event, followed by any number of step_update events, and ends with one result event (the cwd and tools array are abbreviated in the following example):

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.28\n,\n"usage"\n:{\n"input_tokens"\n:\n10302\n,\n"output_tokens"\n:\n582\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n10884\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"checkpoint"\n,\n"duration_seconds"\n:\n0.53\n,\n"usage"\n:{\n"input_tokens"\n:\n116\n,\n"output_tokens"\n:\n7\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n123\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"c3b66b04-872b-4fbe-a3a4-058a026ef20a"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"Git rebase destructively rewrites a branch's commit history by systematically detaching its unique commits and sequentially reapplying them onto a new base commit.\n\n\n"\n,\n"duration_seconds"\n:\n6.88\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n10418\n,\n"output_tokens"\n:\n589\n,\n"thinking_tokens"\n:\n551\n,\n"cache_read_tokens"\n:\n8113\n,\n"total_tokens"\n:\n11007\n}}}
```

When the response streams in chunks, the agent_response step emits one or more ACTIVE events carrying partial text_delta fragments before its final DONE ; short responses like this one arrive in a single DONE .

Every line is an event object whose event field names its type:

event Payload key Emitted init init Once, at stream start step_update step_update For each step transition or text delta result result Once, at the end (same shape as json )

The init payload records the run configuration. model and agent appear only when set with --model or --agent ; permission_mode is request-review by default (and always-proceed under --dangerously-skip-permissions ):

Field Type Description cwd string Working directory tools string[] Names of all available tools permission_mode string Effective permission mode model string Model in use, when overridden agent string Active agent, when overridden json_schema object Enforced schema, when set with --json-schema

Each step_update payload describes one step. Observed step_type values include user_input , agent_response , tool , and checkpoint ; state is ACTIVE while a step runs and DONE when it finishes:

Field Type Description conversation_id string ID of the conversation step_index number Zero-based index of the step state string ACTIVE or DONE step_type string Step category, for example agent_response or tool tool_name string Canonical tool name, on tool steps text_delta string Incremental response text duration_seconds number Step duration, when known usage object Per-step token usage, when known tool_info object Tool invocation details subagent_info object Subagent invocation details

#### Tool calls in the stream

On tool steps, tool_info carries the call and its result. This is a real tool step from a run that executed echo hello_headless_demo :

```
{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"edb1c8c1-50ba-4f3f-87eb-412d0e9d47c3"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"tool"\n,\n"tool_name"\n:\n"run_command"\n,\n"duration_seconds"\n:\n0.07\n,\n"tool_info"\n:{\n"name"\n:\n"run_command"\n,\n"parameters"\n:{\n"CommandLine"\n:\n"echo hello_headless_demo"\n},\n"output"\n:\n"hello_headless_demo\n\r\n\n"\n}}}
```

tool_info holds name , parameters , output , and—when the tool fails—an error object with type and message . Steps that spawn subagents carry subagent_info instead, listing each subagent under subagents (with type_name , role , conversation_id , log_uri , and workspace_uris ).

#### Structured output in the stream

With --json-schema , the schema applies to the terminal result event, which carries the same structured_output and json_schema fields as the json envelope.

## Parse output with jq

stdout is machine-readable, so jq extracts what you need.

Get the response text from a JSON run:

```
agy\n -p\n "Name three popular version control systems, comma-separated."\n --output-format\n json\n |\n jq\n -r\n '.response'
```

```
Git, Subversion, Mercurial.
```

Concatenate streaming text as it arrives:

```
agy\n -p\n "Explain what a merge conflict is in two sentences."\n --output-format\n stream-json\n \\n
\n  |\n jq\n -j\n 'select(.event=="step_update") | .step_update.text_delta // empty'
```

Read token usage from the terminal result event:

```
agy\n -p\n "In one sentence, what is a git rebase?"\n --output-format\n stream-json\n \\n
\n  |\n jq\n 'select(.event=="result") | .result.usage'
```

Tip: Use jq -j (join output) when concatenating text_delta fragments
so jq doesn’t insert newlines between them.

Tip: Use jq -j (join output) when concatenating text_delta fragments
so jq doesn’t insert newlines between them.

## Continue a conversation

Headless runs are stateless by default. Resume prior context with --continue ( -c ) for the most recent conversation, or --conversation with an ID from a previous run’s conversation_id :

```
# Continue the most recent conversation.\n
\nagy\n -p\n "Now explain your previous answer in more detail"\n --continue\n
\n
\n# Resume a specific conversation by ID.\n
\nagy\n -p\n "Summarize what we discussed"\n --conversation\n 055a398f-db14-4c5f-abbb-1bf03f8120a7
```

Each of these starts a new process. To run multiple turns inside one process, refer to Stream prompts from stdin .

## Stream prompts from stdin

Use --input-format stream-json to maintain a single, continuous conversation process, feeding it prompts one by one on standard input (stdin). Each prompt executes a full turn and emits its own result event.

This approach is ideal for applications that dynamically determine the next prompt based on the previous answer. Because the process only starts once, subsequent turns skip startup overhead and reuse the warmed-up conversation. This makes it significantly faster than running repeated commands with --continue .

Note: --input-format stream-json requires --output-format stream-json .
In a streaming session, the CLI emits one result event per turn.

Note: --input-format stream-json requires --output-format stream-json .
In a streaming session, the CLI emits one result event per turn.

### Send a prompt

Write one JSON object per line to stdin . The event key specifies the message type (matching the output stream format). A prompt is represented as a user event with a message :

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly the word: apple. Nothing else."\n } }
```

You can pipe multiple prompts into a single session:

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly the word: apple. Nothing else."}}'\n \\n
\n  '{"event":"user","message":{"content":"What word did I ask you to reply with in my previous message? Answer with just that word."}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json
```

The content field accepts either a standard string or a list of text blocks. The following two formats are equivalent:

```
{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: \n"Reply with exactly: banana"\n } }\n
\n{ \n"event"\n: \n"user"\n, \n"message"\n: { \n"content"\n: [{ \n"type"\n: \n"text"\n, \n"text"\n: \n"Reply with exactly: banana"\n }] } }
```

text is the only supported block type. Submitting any other block type ends the session with an error message rather than silently dropping the block. This ensures the agent never answers a prompt you didn’t explicitly send.

### Read the results

The output stream works as follows:

- Opens with a single init event.

- Emits a series of step_update events for the active turn.

- Concludes the turn with a final result event.

This example shows the output from the two-prompt Bash command in the previous section (with the init payload abbreviated):

```
{\n"event"\n:\n"init"\n,\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"init"\n:{\n"cwd"\n:\n"/home/user/project"\n,\n"tools"\n:[\n"ask_permission"\n,\n"run_command"\n,\n"write_to_file"\n,\n"..."\n],\n"permission_mode"\n:\n"request-review"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n0\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"ACTIVE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n2\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"\n\n\n"\n,\n"duration_seconds"\n:\n1.169607627\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n1.427806958\n,\n"num_turns"\n:\n1\n,\n"usage"\n:{\n"input_tokens"\n:\n30384\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n30388\n}}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n3\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"user_input"\n}}\n
\n{\n"event"\n:\n"step_update"\n,\n"step_update"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"step_index"\n:\n4\n,\n"state"\n:\n"DONE"\n,\n"step_type"\n:\n"agent_response"\n,\n"text_delta"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n0.895679386\n,\n"usage"\n:{\n"input_tokens"\n:\n278\n,\n"output_tokens"\n:\n4\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n282\n}}}\n
\n{\n"event"\n:\n"result"\n,\n"result"\n:{\n"conversation_id"\n:\n"9ec58bfd-4d67-4f5e-83a5-9d907e9c6b1f"\n,\n"status"\n:\n"SUCCESS"\n,\n"response"\n:\n"apple\n\n\n"\n,\n"duration_seconds"\n:\n2.548755756\n,\n"num_turns"\n:\n2\n,\n"usage"\n:{\n"input_tokens"\n:\n30662\n,\n"output_tokens"\n:\n8\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n30214\n,\n"total_tokens"\n:\n30670\n}}}
```

The second turn answers apple from the first turn’s context. Notice that a single conversation_id tracks the entire session, and init is only sent once.

When parsing the result object, keep in mind that the response text applies only to the current turn, while metadata counters track the cumulative session:

Field Scope response The turn that emitted it num_turns Cumulative over the session usage Cumulative over the session duration_seconds Cumulative over the session

To filter and only view the final responses, you can pipe the output through jq :

```
printf\n '%s\n'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: one"}}'\n \\n
\n  '{"event":"user","message":{"content":"Reply with exactly: two"}}'\n \\n
\n  |\n agy\n --input-format\n stream-json\n --output-format\n stream-json\n \\n
\n  |\n jq\n -r\n 'select(.event=="result") | "\(.result.num_turns): \(.result.response)"'
```

```
1: one\n
\n
\n2: two
```

### Drive a session programmatically

Instead of passing all prompts up front, you can hold the stdin pipe open in a script. This allows your application to evaluate the model’s answer before submitting the next prompt.

Tip: You can read stdout line by line and dispatch logic based on the event field. Wait until you receive the result event for the current
prompt before writing the next one.

Tip: You can read stdout line by line and dispatch logic based on the event field. Wait until you receive the result event for the current
prompt before writing the next one.

For example:

```
import\n json\n
\nimport\n subprocess\n
\n
\nproc \n=\n subprocess.Popen(\n
\n    [\n"agy"\n, \n"--input-format"\n, \n"stream-json"\n, \n"--output-format"\n, \n"stream-json"\n],\n
\n    stdin\n=\nsubprocess.\nPIPE\n,\n
\n    stdout\n=\nsubprocess.\nPIPE\n,\n
\n    text\n=\nTrue\n,\n
\n    bufsize\n=\n1\n,\n
\n)\n
\n
\n
\ndef\n ask\n(prompt):\n
\n    """Send one prompt and return the response for that turn."""\n
\n    message \n=\n {\n"event"\n: \n"user"\n, \n"message"\n: {\n"content"\n: prompt}}\n
\n    proc.stdin.write(json.dumps(message) \n+\n "\n\n\n"\n)\n
\n    proc.stdin.flush()\n
\n    for\n line \nin\n proc.stdout:\n
\n        event \n=\n json.loads(line)\n
\n        if\n event[\n"event"\n] \n==\n "result"\n:\n
\n            return\n event[\n"result"\n][\n"response"\n]\n
\n
\n
\nfirst \n=\n ask(\n"Name one popular version control system. Answer with one word."\n)\n
\nprint\n(ask(\nf\n"Name a competitor to \n{\nfirst.strip()\n}\n. Answer with one word."\n))\n
\n
\nproc.stdin.close()\n
\nproc.wait()
```

### End a session

To close a session gracefully, close stdin . The process exits after the input pipe is closed and the current turn completes. If an application writes a final prompt and immediately closes the pipe, it still receives the final result before the process terminates.

Clean sessions exit with 0 , which matches the standard headless mode behavior.

### Unsupported messages

To prevent unpredictable behavior, the CLI validates inputs. If it encounters a malformed or unsupported message, it responds as described in the following table:

Input Result Exit Unrecognized event name Skipped: warning logged to stderr — control_request or control_response events ERROR result, session ends 2 Slash command handled by the CLI such as /model ERROR result, session ends 2 Message missing the event field ERROR result, session ends 1 Invalid JSON line ERROR result, session ends 1 Content block type other than text ERROR result, session ends 1

Unrecognized event names are safely skipped with a warning. This ensures that applications built against newer versions of the streaming protocol don’t crash when running on older CLI versions:

```
warning: ignoring unsupported stream input message event "future_thing"
```

For all other errors, the session terminates immediately. Any turns that previously completed retain their result events, but the malformed input line aborts the remainder of the session.

Slash commands that the CLI can respond to directly (such as /model and /usage ) produce a text report rather than a standard event stream. A streaming session can’t use these types of slash commands. For example:

```
{ \n"event"\n: \n"result"\n, \n"result"\n: { \n"conversation_id"\n: \n"4fae3a70-409d-42a4-86ea-9de206a49ff4"\n, \n"status"\n: \n"ERROR"\n, \n"response"\n: \n""\n, \n"error"\n: \n"/model is answered by the CLI itself and is unavailable with --input-format stream-json; run it as its own --print /model invocation"\n, \n"duration_seconds"\n: \n0\n, \n"num_turns"\n: \n0\n, \n"usage"\n: { \n"input_tokens"\n: \n0\n, \n"output_tokens"\n: \n0\n, \n"thinking_tokens"\n: \n0\n, \n"cache_read_tokens"\n: \n0\n, \n"total_tokens"\n: \n0\n } } }
```

### Common mistakes

Mistake Why it fails How to fix it Pairing with --output-format json or text Those formats only emit a single output envelope when the process exits, causing every turn except the last one to be lost. Always use --output-format stream-json with this input mode. Passing a prompt with the -p flag Streaming mode exclusively listens for prompts on stdin . Any prompt passed through a command-line flag is dropped. Send the prompt into stdin as a user message instead. Sending /model or /usage into the stream The CLI handles these commands internally outside of the event stream, breaking the JSON flow. Run agy -p /model as an entirely separate, standalone command. Treating num_turns as a per-turn count Metadata counters (like turns, duration, and usage) track the entire cumulative session, not just the active turn. Use the response field to get the text for the current turn. Waiting for the process to exit before reading stdout The session stays open indefinitely until stdin is closed. If your script waits for an exit signal, it hangs. Read the events line-by-line as they arrive, and manually close stdin when finished.

## Select a model, effort, or agent

List the available model slugs, then pin one for the run:

```
agy\n models
```

```
gemini-3.8-flash-high     Gemini 3.8 Flash (High)
gemini-3.8-flash-medium   Gemini 3.8 Flash (Medium)
gemini-3.7-flash-high     Gemini 3.7 Flash (High)
gemini-3.7-flash-medium   Gemini 3.7 Flash (Medium)
gemini-3.6-flash-high     Gemini 3.6 Flash (High)
gemini-3.6-flash-medium   Gemini 3.6 Flash (Medium)
gemini-3.1-pro-high       Gemini 3.1 Pro (High)
...
```

```
# Pin a model by slug.\n
\nagy\n -p\n "Reverse the string antigravity."\n --model\n gemini-3.8-flash-medium\n
\n
\n# Set reasoning effort (low, medium, or high).\n
\nagy\n -p\n "Outline a plan to add caching to this service."\n --effort\n high\n
\n
\n# Select an agent (list them with `agy agents`).\n
\nagy\n -p\n "Review this function for edge cases."\n --agent\n <\nagent-nam\ne\n>
```

Unlike the interactive UI, headless mode doesn’t silently fall back when --model names an unknown model. It exits non-zero with an ERROR status so a pinned pipeline fails loudly instead of running the wrong model.

## Permissions in headless mode

There is no interactive prompt in headless mode, so tools that would normally ask for confirmation are handled by policy.

By default, the CLI respects the permission mode in your settings. A tool that requires approval it can’t obtain is soft-denied: the run continues, exits 0 , and prints a notice to stderr naming the tool and how to allow it. Reading and writing files inside your active workspace is auto-allowed; actions such as shell commands default to Ask and are soft-denied in headless mode unless you grant them.

Grant a tool ahead of time by adding an action(target) rule under permissions.allow in ~/.gemini/antigravity-cli/settings.json :

```
{\n
\n  "permissions"\n: {\n
\n    "allow"\n: [\n
\n      "command(git)"\n,\n
\n      "command(regex:npm run (build|lint|test))"\n,\n
\n      "write_file(src/)"\n
\n    ]\n
\n  }\n
\n}
```

To auto-approve every tool for a run, pass --dangerously-skip-permissions :

```
agy\n -p\n "Run the test suite and report failures"\n --dangerously-skip-permissions
```

Warning: --dangerously-skip-permissions approves all tool calls,
including file writes and command execution. Prefer scoped permissions.allow rules unless you fully trust the prompt and environment.
Refer to Permissions for the full rule syntax.

Warning: --dangerously-skip-permissions approves all tool calls,
including file writes and command execution. Prefer scoped permissions.allow rules unless you fully trust the prompt and environment.
Refer to Permissions for the full rule syntax.

## Handle exit codes and errors

A successful run exits 0 . A run that fails to produce a response exits non-zero and writes the reason to stderr . In json and stream-json modes, the failure also appears in the status and error fields.

For example, pinning an unknown model exits 1 and returns an error envelope:

```
agy\n -p\n "hi"\n --model\n does-not-exist-model\n --output-format\n json\n; \necho\n "exit=\n$?\n"
```

```
{\n"conversation_id"\n:\n""\n,\n"status"\n:\n"ERROR"\n,\n"response"\n:\n""\n,\n"error"\n:\n"invalid model selection (--model \n\"\ndoes-not-exist-model\n\"\n --effort \n\"\"\n): model does-not-exist-model is not recognized as a known model or custom model in settings\n\n\nAvailable models:\n\n\n  Gemini 3.6 Flash (High)\n\n\n  ..."\n,\n"duration_seconds"\n:\n0\n,\n"num_turns"\n:\n0\n,\n"usage"\n:{\n"input_tokens"\n:\n0\n,\n"output_tokens"\n:\n0\n,\n"thinking_tokens"\n:\n0\n,\n"cache_read_tokens"\n:\n0\n,\n"total_tokens"\n:\n0\n}}
```

```
exit=1
```

The status field reports the terminal state of the run:

Status Meaning SUCCESS The run completed and produced a response ERROR The run ended with an error CANCELED The run was canceled INTERRUPTED The run was interrupted (for example, SIGINT ) INVALID The run reached an invalid state WAITING The run ended while waiting on input RUNNING The run did not reach a terminal state

By default, a run waits up to five minutes for a response. Adjust the ceiling with --print-timeout :

```
agy\n -p\n "Summarize the design tradeoffs of optimistic locking."\n --print-timeout\n 15m
```

## Flag reference

Flag Default Description -p , --print , --prompt — Run a single prompt non-interactively and print the response --output-format text Output format: text , json , or stream-json --input-format text Input format: text or stream-json ; reads prompts on stdin --json-schema — Schema string or file path to enforce structured output --model — Model slug for this run (refer to agy models ) --effort — Reasoning effort: low , medium , or high --agent — Agent for this run (refer to agy agents ) --continue , -c false Continue the most recent conversation --conversation — Resume a conversation by ID --dangerously-skip-permissions false Auto-approve all tool permission requests --print-timeout 5m Maximum time to wait for a response --sandbox false Run with terminal sandbox restrictions enabled

## Example: run the agent in CI

Fail the job on error and save the response:

```
#!/usr/bin/env bash\n
\nset\n -euo\n pipefail\n
\n
\nresult\n=\n$(\nagy\n -p\n "Name three popular version control systems, comma-separated."\n \\n
\n  --output-format\n json\n \\n
\n  --print-timeout\n 10m\n)\n
\n
\nstatus\n=\n$(\necho\n "\n$result\n"\n |\n jq\n -r\n '.status'\n)\n
\nif\n [[ \n"\n$status\n"\n !=\n "SUCCESS"\n ]]; \nthen\n
\n  echo\n "Agent run failed: $(\necho\n "\n$result\n" \n|\n jq\n -r\n '.error')"\n >&2\n
\n  exit\n 1\n
\nfi\n
\n
\necho\n "\n$result\n"\n |\n jq\n -r\n '.response'\n >\n result.txt
```

## Next steps

Explore the following guides to learn more about CLI capabilities:

- Prompting and interaction : Write effective prompts for the agent.

- Permissions : Configure allow, deny, and ask rules.

- Background tasks and subagents : Delegate work to specialized agents.

- Reference : Full command and flag reference.


</details>
