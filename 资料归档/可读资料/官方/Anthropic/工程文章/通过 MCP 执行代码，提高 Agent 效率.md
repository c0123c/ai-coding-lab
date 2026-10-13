# 通过 MCP 执行代码，提高 Agent 效率

分析大量工具定义和中间结果占用上下文的问题，介绍用代码调用 MCP、按需发现工具、过滤结果和复用技能的方法，并讨论效率、隐私与实现成本。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-04](<https://www.anthropic.com/engineering/code-execution-with-mcp>)；更新：[2025-11-04](<https://www.anthropic.com/engineering/code-execution-with-mcp>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/code-execution-with-mcp>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/通过 MCP 执行代码，提高 Agent 效率.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

[Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) 是用于将 AI agents 连接到外部系统的开放标准。传统上，将 agents 连接到工具和数据需要为每个配对进行自定义集成，从而产生碎片和重复工作，从而难以扩展真正连接的系统。 MCP 提供了一个通用协议——开发人员在他们的 agent 中实现 MCP 一次，它就解锁了整个集成生态系统。

自 11 月 2024 推出 MCP 以来，采用速度很快：社区已构建了数千台 [MCP 服务器 ](<https://github.com/modelcontextprotocol/servers>)、[SDK ](<https://modelcontextprotocol.io/docs/sdk>) 可用于所有主要编程语言，并且业界已采用 MCP作为将 agents 连接到工具和数据的事实上的标准。

如今，开发人员通常会构建 agents，并可以访问数十个 MCP 服务器上的数百或数千种工具。然而，随着连接工具数量的增长，预先加载所有工具定义并通过上下文窗口传递中间结果会减慢速度 agents 并增加成本。

在本博客中，我们将探讨代码执行如何使 agents 能够更有效地与 MCP 服务器交互，在使用更少令牌的同时处理更多工具。

## **工具消耗过多代币使agents效率降低**

随着 MCP 使用规模的扩大，有两种常见模式可能会增加 agent 成本和延迟：

1. 工具定义使上下文窗口过载；
2. 中间工具结果消耗额外的令牌。

### **1。工具定义使上下文窗口过载**

大多数 MCP 客户端将所有工具定义直接加载到上下文中，使用直接工具调用语法将它们暴露给模型。这些工具定义可能如下所示：

```
gdrive.getDocument
     Description: Retrieves a document from Google Drive
     Parameters:
                documentId (required, string): The ID of the document to retrieve
                fields (optional, string): Specific fields to return
     Returns: Document object with title, body content, metadata, permissions, etc.
```

```
salesforce.updateRecord
    Description: Updates a record in Salesforce
    Parameters:
               objectType (required, string): Type of Salesforce object (Lead, Contact,      Account, etc.)
               recordId (required, string): The ID of the record to update
               data (required, object): Fields to update with their new values
     Returns: Updated record object with confirmation
```

工具描述占用更多上下文窗口空间，增加响应时间和成本。如果 agents 连接到数千个工具，它们在读取请求之前需要处理数十万个令牌。

### **2。中间工具结果消耗额外的代币**

大多数MCP客户端允许模型直接调用MCP工具。例如，您可能会询问您的 agent：“从 Google Drive 下载我的会议记录并将其附加到 Salesforce 潜在客户。”

该模型将进行如下调用：

```
TOOL CALL: gdrive.getDocument(documentId: "abc123")
        → returns "Discussed Q4 goals...\n[full transcript text]"
           (loaded into model context)

TOOL CALL: salesforce.updateRecord(
			objectType: "SalesMeeting",
			recordId: "00Q5f000001abcXYZ",
  			data: { "Notes": "Discussed Q4 goals...\n[full transcript text written out]" }
		)
		(model needs to write entire transcript into context again)
```

每个中间结果都必须经过模型。在此示例中，完整的通话记录经过两次。对于 2 小时的销售会议，这可能意味着处理额外的 50,000 代币。即使更大的文档也可能超出上下文窗口限制，从而破坏工作流程。

对于大型文档或复杂的数据结构，模型在工具调用之间复制数据时可能更容易出错。

![MCP 客户端如何与 MCP 服务器和 LLM 一起工作的图像。](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F9ecf165020005c09a22a9472cee6309555485619-1920x1080.png&w=3840&q=75>)

MCP 客户端将工具定义加载到模型的上下文窗口中，并编排一个消息循环，其中每个工具调用和结果在操作之间通过模型。

## **使用MCP执行代码提高了上下文效率**

随着 agents 的代码执行环境变得越来越普遍，解决方案是将 MCP 服务器呈现为代码 API，而不是直接工具调用。然后，agent 可以编写代码来与 MCP 服务器交互。这种方法解决了这两个挑战：agents 可以仅加载所需的工具，并在将结果传递回模型之前在执行环境中处理数据。

有多种方法可以做到这一点。一种方法是从连接的 MCP 服务器生成所有可用工具的文件树。这是使用 TypeScript 的实现：

```
servers
├── google-drive
│   ├── getDocument.ts
│   ├── ... (other tools)
│   └── index.ts
├── salesforce
│   ├── updateRecord.ts
│   ├── ... (other tools)
│   └── index.ts
└── ... (other servers)
```

那么每个工具对应一个文件，类似于：

```
// ./servers/google-drive/getDocument.ts
import { callMCPTool } from "../../../client.js";

interface GetDocumentInput {
  documentId: string;
}

interface GetDocumentResponse {
  content: string;
}

/* Read a document from Google Drive */
export async function getDocument(input: GetDocumentInput): Promise<GetDocumentResponse> {
  return callMCPTool<GetDocumentResponse>('google_drive__get_document', input);
}
```

我们上面的 Google Drive to Salesforce 示例变成了代码：

```
// Read transcript from Google Docs and add to Salesforce prospect
import * as gdrive from './servers/google-drive';
import * as salesforce from './servers/salesforce';

const transcript = (await gdrive.getDocument({ documentId: 'abc123' })).content;
await salesforce.updateRecord({
  objectType: 'SalesMeeting',
  recordId: '00Q5f000001abcXYZ',
  data: { Notes: transcript }
});
```

agent通过探索文件系统来发现工具：列出`./servers/`目录以查找可用服务器（例如`google-drive`和`salesforce`），然后读取它需要的特定工具文件（例如`getDocument.ts`和`updateRecord.ts`）以了解每个工具的接口。这使得 agent 仅加载当前任务所需的定义。这将代币使用量从 150,000 代币减少到 2,000 代币 — 节省了 98.7%**.** 的时间和成本

Cloudflare [ 发布了类似的发现 ](<https://blog.cloudflare.com/code-mode/>)，将 MCP 的代码执行称为“代码模式”。核心见解是相同的：法学硕士擅长编写代码，开发人员应该利用这一优势来构建与 MCP 服务器更有效交互的 agents。

## **使用MCP**执行代码的好处

使用 MCP 执行代码使 agents 能够通过按需加载工具、在数据到达模型之前过滤数据以及一步执行复杂逻辑来更有效地使用上下文。使用这种方法还具有安全性和状态管理优势。

### 渐进式披露

模型非常擅长导航文件系统。将工具呈现为文件系统上的代码允许模型按需读取工具定义，而不是预先读取它们。

或者，可以在服务器中添加`search_tools`工具来查找相关定义。例如，当使用上面使用的假设 Salesforce 服务器时，agent 搜索“salesforce”并仅加载当前任务所需的工具。在 `search_tools` 工具中包含一个详细级别参数，允许 agent 选择所需的详细级别（例如仅名称、名称和描述，或带有模式的完整定义），也有助于 agent 保存上下文并有效地查找工具。

### 上下文有效的工具结果

处理大型数据集时，agents 可以在返回结果之前过滤和转换代码中的结果。考虑获取 10,000 行电子表格：

```
// Without code execution - all rows flow through context
TOOL CALL: gdrive.getSheet(sheetId: 'abc123')
        → returns 10,000 rows in context to filter manually

// With code execution - filter in the execution environment
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(row => 
  row["Status"] === 'pending'
);
console.log(`Found ${pendingOrders.length} pending orders`);
console.log(pendingOrders.slice(0, 5)); // Only log first 5 for review
```

agent 看到五行，而不是 10,000。类似的模式适用于聚合、跨多个数据源的联接或提取特定字段，所有这些都不会使上下文窗口膨胀。

#### **更强大且上下文高效的控制流**

循环、条件和错误处理可以使用熟悉的代码模式来完成，而不是链接单个工具调用。例如，如果您需要 Slack 中的部署通知，agent 可以编写：

```
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({ channel: 'C123456' });
  found = messages.some(m => m.text.includes('deployment complete'));
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
console.log('Deployment notification received');
```

这种方法比通过 agent 循环在 MCP 工具调用和睡眠命令之间交替进行更有效。

此外，能够写出执行的条件树还可以节省“第一个令牌的时间”延迟：agent 可以让代码执行环境执行此操作，而不必等待模型评估 if 语句。

### 隐私保护操作

当agents使用MCP执行代码时，中间结果默认保留在执行环境中。这样，agent 只能看到您显式记录或返回的内容，这意味着您不希望与模型共享的数据可以流经您的工作流程，而无需进入模型的上下文。

对于更敏感的工作负载，agent 工具可以自动标记敏感数据。例如，假设您需要将客户联系详细信息从电子表格导入 Salesforce。 agent 写道：

```
const sheet = await gdrive.getSheet({ sheetId: 'abc123' });
for (const row of sheet.rows) {
  await salesforce.updateRecord({
    objectType: 'Lead',
    recordId: row.salesforceId,
    data: { 
      Email: row.email,
      Phone: row.phone,
      Name: row.name
    }
  });
}
console.log(`Updated ${sheet.rows.length} leads`);
```

MCP 客户端在数据到达模型之前拦截数据并标记 PII：

```
// What the agent would see, if it logged the sheet.rows:
[
  { salesforceId: '00Q...', email: '[EMAIL_1]', phone: '[PHONE_1]', name: '[NAME_1]' },
  { salesforceId: '00Q...', email: '[EMAIL_2]', phone: '[PHONE_2]', name: '[NAME_2]' },
  ...
]
```

然后，当数据在另一个 MCP 工具调用中共享时，它会通过 MCP 客户端中的查找来取消标记。真实的电子邮件地址、电话号码和姓名从 Google 工作表流向 Salesforce，但从未通过模型。这可以防止 agent 意外记录或处理敏感数据。您还可以使用它来定义确定性安全规则，选择数据可以流入和流出的位置。

### 状态坚持和技能

通过文件系统访问执行代码允许 agents 跨操作维护状态。 Agents 可以将中间结果写入文件，使它们能够恢复工作并跟踪进度：

```
const leads = await salesforce.query({ 
  query: 'SELECT Id, Email FROM Lead LIMIT 1000' 
});
const csvData = leads.map(l => `${l.Id},${l.Email}`).join('\n');
await fs.writeFile('./workspace/leads.csv', csvData);

// Later execution picks up where it left off
const saved = await fs.readFile('./workspace/leads.csv', 'utf-8');
```

Agents 还可以将自己的代码保留为可重用函数。一旦 agent 为任务开发了工作代码，它就可以保存该实现以供将来使用：

```
// In ./skills/save-sheet-as-csv.ts
import * as gdrive from './servers/google-drive';
export async function saveSheetAsCsv(sheetId: string) {
  const data = await gdrive.getSheet({ sheetId });
  const csv = data.map(row => row.join(',')).join('\n');
  await fs.writeFile(`./workspace/sheet-${sheetId}.csv`, csv);
  return `./workspace/sheet-${sheetId}.csv`;
}

// Later, in any agent execution:
import { saveSheetAsCsv } from './skills/save-sheet-as-csv';
const csvPath = await saveSheetAsCsv('abc123');
```

这与 [ 技能 ](<https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview>) 的概念密切相关，其中包含可重用指令、脚本和模型资源的文件夹，以提高专门任务的性能。将 SKILL.md 文件添加到这些保存的函数中会创建模型可以参考和使用的结构化技能。随着时间的推移，这可以让您的 agent 构建一个具有更高级别功能的工具箱，从而发展最有效工作所需的脚手架。

请注意，代码执行会带来其自身的复杂性。运行 agent 生成的代码需要一个安全的执行环境，并具有适当的 [ 沙箱 ](<https://www.anthropic.com/engineering/claude-code-sandboxing>)、资源限制和监控。这些基础设施要求增加了直接工具调用所避免的操作开销和安全考虑。代码执行的好处——降低代币成本、降低延迟和改进工具组合——应该与这些实施成本进行权衡。

## **摘要**

MCP为agents提供了连接许多工具和系统的基础协议。然而，一旦连接太多服务器，工具定义和结果可能会消耗过多的令牌，从而降低agent效率。

尽管这里的许多问题感觉很新奇——上下文管理、工具组合、状态持久性——但他们已经从软件工程中找到了已知的解决方案。代码执行将这些已建立的模式应用于 agents，让它们使用熟悉的编程结构更有效地与 MCP 服务器交互。如果您实施此方法，我们鼓励您与 [MCP 社区 ](<https://modelcontextprotocol.io/community/communication>) 分享您的发现。

### 致谢

*本文由 Adam Jones 和 Conor Kelly 撰写。感谢 Jeremy Fox、Jerome Swannack、Stuart Ritchie、Molly Vorwerck、Matt Samuels 和 Maggie Vo 对本文草稿的反馈。*


## 原文对照

<details>
<summary>展开完整原文</summary>

[The Model Context Protocol (MCP)](<https://modelcontextprotocol.io/>) is an open standard for connecting AI agents to external systems. Connecting agents to tools and data traditionally requires a custom integration for each pairing, creating fragmentation and duplicated effort that makes it difficult to scale truly connected systems. MCP provides a universal protocol—developers implement MCP once in their agent and it unlocks an entire ecosystem of integrations.

Since launching MCP in November 2024, adoption has been rapid: the community has built thousands of [MCP servers](<https://github.com/modelcontextprotocol/servers>), [SDKs](<https://modelcontextprotocol.io/docs/sdk>) are available for all major programming languages, and the industry has adopted MCP as the de-facto standard for connecting agents to tools and data.

Today developers routinely build agents with access to hundreds or thousands of tools across dozens of MCP servers. However, as the number of connected tools grows, loading all tool definitions upfront and passing intermediate results through the context window slows down agents and increases costs.

In this blog we'll explore how code execution can enable agents to interact with MCP servers more efficiently, handling more tools while using fewer tokens.

## **Excessive token consumption from tools makes agents less efficient**

As MCP usage scales, there are two common patterns that can increase agent cost and latency:

1. Tool definitions overload the context window;
2. Intermediate tool results consume additional tokens.

### **1. Tool definitions overload the context window**

Most MCP clients load all tool definitions upfront directly into context, exposing them to the model using a direct tool-calling syntax. These tool definitions might look like:

```
gdrive.getDocument
     Description: Retrieves a document from Google Drive
     Parameters:
                documentId (required, string): The ID of the document to retrieve
                fields (optional, string): Specific fields to return
     Returns: Document object with title, body content, metadata, permissions, etc.
```

```
salesforce.updateRecord
    Description: Updates a record in Salesforce
    Parameters:
               objectType (required, string): Type of Salesforce object (Lead, Contact,      Account, etc.)
               recordId (required, string): The ID of the record to update
               data (required, object): Fields to update with their new values
     Returns: Updated record object with confirmation
```

Tool descriptions occupy more context window space, increasing response time and costs. In cases where agents are connected to thousands of tools, they’ll need to process hundreds of thousands of tokens before reading a request.

### **2. Intermediate tool results consume additional tokens**

Most MCP clients allow models to directly call MCP tools. For example, you might ask your agent: "Download my meeting transcript from Google Drive and attach it to the Salesforce lead."

The model will make calls like:

```
TOOL CALL: gdrive.getDocument(documentId: "abc123")
        → returns "Discussed Q4 goals...\n[full transcript text]"
           (loaded into model context)

TOOL CALL: salesforce.updateRecord(
			objectType: "SalesMeeting",
			recordId: "00Q5f000001abcXYZ",
  			data: { "Notes": "Discussed Q4 goals...\n[full transcript text written out]" }
		)
		(model needs to write entire transcript into context again)
```

Every intermediate result must pass through the model. In this example, the full call transcript flows through twice. For a 2-hour sales meeting, that could mean processing an additional 50,000 tokens. Even larger documents may exceed context window limits, breaking the workflow.

With large documents or complex data structures, models may be more likely to make mistakes when copying data between tool calls.

![Image of how the MCP client works with the MCP server and LLM.](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F9ecf165020005c09a22a9472cee6309555485619-1920x1080.png&w=3840&q=75>)

The MCP client loads tool definitions into the model's context window and orchestrates a message loop where each tool call and result passes through the model between operations.

## **Code execution with MCP improves context efficiency**

With code execution environments becoming more common for agents, a solution is to present MCP servers as code APIs rather than direct tool calls. The agent can then write code to interact with MCP servers. This approach addresses both challenges: agents can load only the tools they need and process data in the execution environment before passing results back to the model.

There are a number of ways to do this. One approach is to generate a file tree of all available tools from connected MCP servers. Here's an implementation using TypeScript:

```
servers
├── google-drive
│   ├── getDocument.ts
│   ├── ... (other tools)
│   └── index.ts
├── salesforce
│   ├── updateRecord.ts
│   ├── ... (other tools)
│   └── index.ts
└── ... (other servers)
```

Then each tool corresponds to a file, something like:

```
// ./servers/google-drive/getDocument.ts
import { callMCPTool } from "../../../client.js";

interface GetDocumentInput {
  documentId: string;
}

interface GetDocumentResponse {
  content: string;
}

/* Read a document from Google Drive */
export async function getDocument(input: GetDocumentInput): Promise<GetDocumentResponse> {
  return callMCPTool<GetDocumentResponse>('google_drive__get_document', input);
}
```

Our Google Drive to Salesforce example above becomes the code:

```
// Read transcript from Google Docs and add to Salesforce prospect
import * as gdrive from './servers/google-drive';
import * as salesforce from './servers/salesforce';

const transcript = (await gdrive.getDocument({ documentId: 'abc123' })).content;
await salesforce.updateRecord({
  objectType: 'SalesMeeting',
  recordId: '00Q5f000001abcXYZ',
  data: { Notes: transcript }
});
```

The agent discovers tools by exploring the filesystem: listing the `./servers/` directory to find available servers (like `google-drive` and `salesforce`), then reading the specific tool files it needs (like `getDocument.ts` and `updateRecord.ts`) to understand each tool's interface. This lets the agent load only the definitions it needs for the current task. This reduces the token usage from 150,000 tokens to 2,000 tokens—a time and cost saving of 98.7%**.**

Cloudflare [published similar findings](<https://blog.cloudflare.com/code-mode/>), referring to code execution with MCP as “Code Mode." The core insight is the same: LLMs are adept at writing code and developers should take advantage of this strength to build agents that interact with MCP servers more efficiently.

## **Benefits of code execution with MCP**

Code execution with MCP enables agents to use context more efficiently by loading tools on demand, filtering data before it reaches the model, and executing complex logic in a single step. There are also security and state management benefits to using this approach.

### Progressive disclosure

Models are great at navigating filesystems. Presenting tools as code on a filesystem allows models to read tool definitions on-demand, rather than reading them all up-front.

Alternatively, a `search_tools` tool can be added to the server to find relevant definitions. For example, when working with the hypothetical Salesforce server used above, the agent searches for "salesforce" and loads only those tools that it needs for the current task. Including a detail level parameter in the `search_tools` tool that allows the agent to select the level of detail required (such as name only, name and description, or the full definition with schemas) also helps the agent conserve context and find tools efficiently.

### Context efficient tool results

When working with large datasets, agents can filter and transform results in code before returning them. Consider fetching a 10,000-row spreadsheet:

```
// Without code execution - all rows flow through context
TOOL CALL: gdrive.getSheet(sheetId: 'abc123')
        → returns 10,000 rows in context to filter manually

// With code execution - filter in the execution environment
const allRows = await gdrive.getSheet({ sheetId: 'abc123' });
const pendingOrders = allRows.filter(row => 
  row["Status"] === 'pending'
);
console.log(`Found ${pendingOrders.length} pending orders`);
console.log(pendingOrders.slice(0, 5)); // Only log first 5 for review
```

The agent sees five rows instead of 10,000. Similar patterns work for aggregations, joins across multiple data sources, or extracting specific fields—all without bloating the context window.

#### **More powerful and context-efficient control flow**

Loops, conditionals, and error handling can be done with familiar code patterns rather than chaining individual tool calls. For example, if you need a deployment notification in Slack, the agent can write:

```
let found = false;
while (!found) {
  const messages = await slack.getChannelHistory({ channel: 'C123456' });
  found = messages.some(m => m.text.includes('deployment complete'));
  if (!found) await new Promise(r => setTimeout(r, 5000));
}
console.log('Deployment notification received');
```

This approach is more efficient than alternating between MCP tool calls and sleep commands through the agent loop.

Additionally, being able to write out a conditional tree that gets executed also saves on “time to first token” latency: rather than having to wait for a model to evaluate an if-statement, the agent can let the code execution environment do this.

### Privacy-preserving operations

When agents use code execution with MCP, intermediate results stay in the execution environment by default. This way, the agent only sees what you explicitly log or return, meaning data you don’t wish to share with the model can flow through your workflow without ever entering the model's context.

For even more sensitive workloads, the agent harness can tokenize sensitive data automatically. For example, imagine you need to import customer contact details from a spreadsheet into Salesforce. The agent writes:

```
const sheet = await gdrive.getSheet({ sheetId: 'abc123' });
for (const row of sheet.rows) {
  await salesforce.updateRecord({
    objectType: 'Lead',
    recordId: row.salesforceId,
    data: { 
      Email: row.email,
      Phone: row.phone,
      Name: row.name
    }
  });
}
console.log(`Updated ${sheet.rows.length} leads`);
```

The MCP client intercepts the data and tokenizes PII before it reaches the model:

```
// What the agent would see, if it logged the sheet.rows:
[
  { salesforceId: '00Q...', email: '[EMAIL_1]', phone: '[PHONE_1]', name: '[NAME_1]' },
  { salesforceId: '00Q...', email: '[EMAIL_2]', phone: '[PHONE_2]', name: '[NAME_2]' },
  ...
]
```

Then, when the data is shared in another MCP tool call, it is untokenized via a lookup in the MCP client. The real email addresses, phone numbers, and names flow from Google Sheets to Salesforce, but never through the model. This prevents the agent from accidentally logging or processing sensitive data. You can also use this to define deterministic security rules, choosing where data can flow to and from.

### State persistence and skills

Code execution with filesystem access allows agents to maintain state across operations. Agents can write intermediate results to files, enabling them to resume work and track progress:

```
const leads = await salesforce.query({ 
  query: 'SELECT Id, Email FROM Lead LIMIT 1000' 
});
const csvData = leads.map(l => `${l.Id},${l.Email}`).join('\n');
await fs.writeFile('./workspace/leads.csv', csvData);

// Later execution picks up where it left off
const saved = await fs.readFile('./workspace/leads.csv', 'utf-8');
```

Agents can also persist their own code as reusable functions. Once an agent develops working code for a task, it can save that implementation for future use:

```
// In ./skills/save-sheet-as-csv.ts
import * as gdrive from './servers/google-drive';
export async function saveSheetAsCsv(sheetId: string) {
  const data = await gdrive.getSheet({ sheetId });
  const csv = data.map(row => row.join(',')).join('\n');
  await fs.writeFile(`./workspace/sheet-${sheetId}.csv`, csv);
  return `./workspace/sheet-${sheetId}.csv`;
}

// Later, in any agent execution:
import { saveSheetAsCsv } from './skills/save-sheet-as-csv';
const csvPath = await saveSheetAsCsv('abc123');
```

This ties in closely to the concept of [Skills](<https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview>), folders of reusable instructions, scripts, and resources for models to improve performance on specialized tasks. Adding a SKILL.md file to these saved functions creates a structured skill that models can reference and use. Over time, this allows your agent to build a toolbox of higher-level capabilities, evolving the scaffolding that it needs to work most effectively.

Note that code execution introduces its own complexity. Running agent-generated code requires a secure execution environment with appropriate [sandboxing](<https://www.anthropic.com/engineering/claude-code-sandboxing>), resource limits, and monitoring. These infrastructure requirements add operational overhead and security considerations that direct tool calls avoid. The benefits of code execution—reduced token costs, lower latency, and improved tool composition—should be weighed against these implementation costs.

## **Summary**

MCP provides a foundational protocol for agents to connect to many tools and systems. However, once too many servers are connected, tool definitions and results can consume excessive tokens, reducing agent efficiency.

Although many of the problems here feel novel—context management, tool composition, state persistence—they have known solutions from software engineering. Code execution applies these established patterns to agents, letting them use familiar programming constructs to interact with MCP servers more efficiently. If you implement this approach, we encourage you to share your findings with the [MCP community](<https://modelcontextprotocol.io/community/communication>).

### Acknowledgments

*This article was written by Adam Jones and Conor Kelly. Thanks to Jeremy Fox, Jerome Swannack, Stuart Ritchie, Molly Vorwerck, Matt Samuels, and Maggie Vo for feedback on drafts of this post.*


</details>
