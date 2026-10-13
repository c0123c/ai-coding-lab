# Claude 平台的高级工具使用

介绍工具搜索、通过代码批量调用工具和工具使用示例，分析大量工具接入的上下文与延迟问题，说明各项机制的使用条件和适用场景。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2025-11-24](<https://www.anthropic.com/engineering/advanced-tool-use>)；更新：[2026-03-16](<https://www.anthropic.com/engineering/advanced-tool-use>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/advanced-tool-use>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/Claude 平台的高级工具使用.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

AI agents 的未来是模型可以跨数百或数千种工具无缝运行。一个集成了 git 操作、文件操作、包管理器、测试框架和部署管道的 IDE 助手。一个运营协调器，可同时连接 Slack、GitHub、Google Drive、Jira、公司数据库和数十台 MCP 服务器。

为了[构建有效的agents](<https://www.anthropic.com/research/building-effective-agents>)，他们需要使用无限的工具库，而不需要预先将每个定义填充到上下文中。我们关于使用 [ 代码执行和 MCP](<https://www.anthropic.com/engineering/code-execution-with-mcp>) 的博客文章讨论了工具结果和定义有时如何在 agent 读取请求之前消耗 50,000+ 令牌。 Agents 应该按需发现和加载工具，仅保留与当前任务相关的内容。

Agents 还需要能够从代码调用工具。使用自然语言工具调用时，每次调用都需要完整的推理过程，中间结果会在上下文中堆积，无论它们是否有用。代码非常适合编排逻辑，例如循环、条件和数据转换。 Agents 需要根据手头的任务灵活地在代码执行和推理之间进行选择。

Agents 还需要从示例中学习正确的工具用法，而不仅仅是模式定义。 JSON 模式定义了结构上有效的内容，但无法表达使用模式：何时包含可选参数、哪些组合有意义，或者 API 期望的约定。

今天，我们发布了三项使这成为可能的功能：

* **工具搜索工具，**允许Claude使用搜索工具访问数千个工具，而不消耗其上下文窗口
* **编程工具调用**，允许Claude在代码执行环境中调用工具，减少对模型上下文窗口的影响
* **工具使用示例**，它提供了演示如何有效使用给定工具的通用标准

在内部测试中，我们发现这些功能帮助我们构建了传统工具使用模式无法实现的东西。例如，**[Claude for Excel](<https://www.claude.com/claude-for-excel>)** 使用编程工具调用来读取和修改具有数千行的电子表格，而不会导致模型的上下文窗口过载。

根据我们的经验，我们相信这些功能为您使用 Claude 构建内容开辟了新的可能性。

## 工具搜索工具

### 挑战

MCP 工具定义提供了重要的上下文，但随着更多服务器连接，这些令牌可能会增加。考虑五台服务器的设置：

* GitHub：35 工具（~26K 代币）
* Slack：11 工具（约 21K 代币）
* Sentry：5 工具（~3K 代币）
* Grafana：5 工具（~3K 代币）
* Splunk：2 工具（~2K 代币）

在对话开始之前，58 工具就消耗了大约 55K 令牌。添加更多像 Jira 这样的服务器（仅使用约 17K 令牌），您很快就会接近 100K+ 令牌开销。在 Anthropic，我们看到工具定义在优化之前消耗了 134K 令牌。

但代币成本并不是唯一的问题。最常见的故障是工具选择错误和参数不正确，尤其是当工具名称相似时，例如 `notification-send-user` 与 `notification-send-channel`。

### 我们的解决方案

工具搜索工具不是预先加载所有工具定义，而是按需发现工具。 Claude 只能看到当前任务实际需要的工具。

![工具搜索工具图](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2Ff359296f770706608901eadaffbff4ca0b67874c-1999x1125.png&w=3840&q=75>)

与采用 Claude 传统方法的 122,800 相比，*工具搜索工具保留了 191,300 上下文标记。*

传统方法：

* 所有工具定义均预先加载（50+ MCP 工具约 72K 令牌）
* 通话记录与系统提示争夺剩余空间
* 总上下文消耗：在任何工作开始之前约 77K 令牌

使用工具搜索工具：

* 仅预先加载工具搜索工具（~500 令牌）
* 根据需要按需发现的工具（3-5相关工具，~3K 代币）
* 上下文总消耗：~8.7K 令牌，保留上下文窗口的 95%

这意味着令牌使用量减少 85%，同时保持对完整工具库的访问。内部测试表明，在使用大型工具库时，MCP 评估的准确性显着提高。启用工具搜索工具后，Opus 4 从 49% 改进为 74%，Opus 4.5 从 79.5% 改进为 88.1%。

### 工具搜索工具的工作原理

工具搜索工具让 Claude 动态发现工具，而不是预先加载所有定义。您向 API 提供所有工具定义，但用 `defer_loading: true` 标记工具，以便按需发现它们。延迟工具最初不会加载到 Claude 的上下文中。 Claude 只能看到工具搜索工具本身以及带有 `defer_loading: false` 的任何工具（您最关键、最常用的工具）。

当Claude需要特定功能时，它会搜索相关工具。工具搜索工具返回对匹配工具的引用，这些工​​具在 Claude 的上下文中扩展为完整定义。

例如，如果 Claude 需要与 GitHub 交互，它会搜索“github”，并且仅加载 `github.createPullRequest` 和 `github.listIssues`，而不加载 Slack、Jira 和 Google Drive 中的其他 50+ 工具。

这样，Claude 就可以访问您的完整工具库，而只需为其实际需要的工具支付代币成本。

**提示缓存注意：**工具搜索工具不会破坏提示缓存，因为延迟工具完全从初始提示中排除。它们仅在 Claude 搜索后添加到上下文中，因此您的系统提示和核心工具定义仍然可缓存。

**实施：**

```
{
  "tools": [
    // Include a tool search tool (regex, BM25, or custom)
    {"type": "tool_search_tool_regex_20251119", "name": "tool_search_tool_regex"},

    // Mark tools for on-demand discovery
    {
      "name": "github.createPullRequest",
      "description": "Create a pull request",
      "input_schema": {...},
      "defer_loading": true
    }
    // ... hundreds more deferred tools with defer_loading: true
  ]
}
```

对于 MCP 服务器，您可以推迟加载整个服务器，同时保持加载特定的高使用率工具：

```
{
  "type": "mcp_toolset",
  "mcp_server_name": "google-drive",
  "default_config": {"defer_loading": true}, # defer loading the entire server
  "configs": {
    "search_files": {
"defer_loading": false
    }  // Keep most used tool loaded
  }
}
```

Claude 开发者平台提供了开箱即用的基于正则表达式和基于 BM25- 的搜索工具，但您也可以使用嵌入或其他策略来实现自定义搜索工具。

### 何时使用工具搜索工具

与任何架构决策一样，启用工具搜索工具需要权衡。该功能在工具调用之前添加了一个搜索步骤，因此当上下文节省和准确性改进超过额外延迟时，它可以提供最佳的 ROI。

**在以下情况下使用：**

* 消耗 >10K 令牌的工具定义
* 遇到刀具选择精度问题
* 构建具有多个服务器的 MCP- 动力系统
* 10+可用工具

**在以下情况下效果较差：**

* 小工具库（<10工具）
* 每个会话中经常使用的所有工具
* 工具定义很紧凑

## 程序化工具调用

### 挑战

随着工作流程变得更加复杂，传统的工具调用会产生两个基本问题：

* **来自中间结果的上下文污染**：当Claude分析10MB日志文件中的错误模式时，整个文件进入其上下文窗口，即使Claude只需要错误频率的摘要。当跨多个表获取客户数据时，每条记录都会在上下文中累积，无论相关性如何。这些中间结果消耗大量的代币预算，并且可以将重要信息完全推出上下文窗口。
* **推理开销和手动综合**：每个工具调用都需要完整的模型推理过程。收到结果后，Claude 必须“观察”数据以提取相关信息，推理各部分如何组合在一起，并决定下一步做什么——所有这些都通过自然语言处理进行。五工具工作流程意味着五次推理过程加上 Claude 解析每个结果、比较值和综合结论。这既缓慢又容易出错。

### 我们的解决方案

编程工具调用可实现 Claude 通过代码而不是个人来编排工具 API 往返。而不是 Claude 一次请求一个工具，并将每个结果返回到其上下文， Claude 编写调用多个工具、处理其输出并控制实际进入其上下文窗口的信息的代码。

Claude 擅长编写代码，并通过让它在 Python 中表达编排逻辑，而不是通过自然语言工具调用，您可以获得更可靠、更精确的控制流。循环、条件、数据转换和错误处理在代码中都是显式的，而不是在 Claude 的推理中隐式的。

#### 示例：预算合规性检查

考虑一个常见的业务任务：“哪些团队成员超出了他们的 Q3 差旅预算？”

您可以使用三种工具：

* `get_team_members(department)` - 返回包含 ID 和级别的团队成员列表
* `get_expenses(user_id, quarter)` - 返回用户的费用行项目
* `get_budget_by_level(level)` - 返回员工级别的预算限制

**传统方法**：

* 获取团队成员 → 20 人
* 对于每个人，获取其 Q3 费用 → 20 工具调用，每个调用返回 50-100 行项目（航班、酒店、餐饮、收据）
* 按员工级别获取预算限制
* 所有这些都进入 Claude 的上下文：2,000+ 费用行项目 (50 KB+)
* Claude 手动汇总每个人的费用，查找他们的预算，将费用与预算限制进行比较
* 模型的往返次数更多，上下文消耗显着

**通过编程工具调用**：

Claude 不是将每个工具结果返回到 Claude，而是编写一个 Python 脚本来编排整个工作流程。该脚本在代码执行工具（沙盒环境）中运行，在需要工具的结果时暂停。当您通过 API 返回工具结果时，它们将由脚本处理，而不是由模型使用。脚本继续执行，Claude 只能看到最终的输出。

![编程工具调用流程](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F65737d69a3290ed5c1f3c3b8dc873645a9dcc2eb-1999x1491.png&w=3840&q=75>)

编程工具调用使 Claude 能够通过代码而不是通过单独的 API 往返来编排工具，从而允许并行工具执行。

以下是 Claude 预算合规任务的编排代码：

```
team = await get_team_members("engineering")

# Fetch budgets for each unique level
levels = list(set(m["level"] for m in team))
budget_results = await asyncio.gather(*[
    get_budget_by_level(level) for level in levels
])

# Create a lookup dictionary: {"junior": budget1, "senior": budget2, ...}
budgets = {level: budget for level, budget in zip(levels, budget_results)}

# Fetch all expenses in parallel
expenses = await asyncio.gather(*[
    get_expenses(m["id"], "Q3") for m in team
])

# Find employees who exceeded their travel budget
exceeded = []
for member, exp in zip(team, expenses):
    budget = budgets[member["level"]]
    total = sum(e["amount"] for e in exp)
    if total > budget["travel_limit"]:
        exceeded.append({
            "name": member["name"],
            "spent": total,
            "limit": budget["travel_limit"]
        })

print(json.dumps(exceeded))
```

Claude 的上下文仅收到最终结果：超出预算的两到三个人。 2,000+ 行项目、中间总和和预算查找不会影响 Claude 的上下文，从而将消耗量从 200KB 的原始费用数据减少到仅 1KB 的结果。

效率提升是巨大的：

* **代币节省**：通过将中间结果保留在Claude的上下文之外，PTC极大地减少了代币消耗。平均使用量从 43,588 下降到 27,297 代币，复杂研究任务减少了 37%。
* **减少延迟**：每次API往返都需要模型推理（数百毫秒到秒）。当 Claude 在单个代码块中协调 20+ 工具调用时，您可以消除 19+ 推理过程。 API 处理工具执行，而无需每次返回模型。
* **提高准确性**：通过编写明确的编排逻辑，Claude比用自然语言处理多个工具结果时犯的错误更少。内部知识检索由25.6%改进为28.5%； [GIA 基准测试 ](<https://arxiv.org/abs/2311.12983>) 从 46.5% 到 51.2%。

生产工作流程涉及混乱的数据、条件逻辑和需要扩展的操作。编程工具调用让 Claude 以编程方式处理这种复杂性，同时将重点放在可操作的结果而不是原始数据处理上。

### 编程工具调用的工作原理

#### 1。将工具标记为可从代码调用

将代码\_执行添加到工具中，并将允许的\_调用者设置为选择加入工具以进行编程执行：

```
{
  "tools": [
    {
      "type": "code_execution_20250825",
      "name": "code_execution"
    },
    {
      "name": "get_team_members",
      "description": "Get all members of a department...",
      "input_schema": {...},
      "allowed_callers": ["code_execution_20250825"] # opt-in to programmatic tool calling
    },
    {
      "name": "get_expenses",
 	...
    },
    {
      "name": "get_budget_by_level",
	...
    }
  ]
}
```

API将这些工具定义转换为Claude可以调用的Python函数。

#### 2。 Claude编写编排代码

Claude 不是一次请求一个工具，而是生成 Python 代码：

```
{
  "type": "server_tool_use",
  "id": "srvtoolu_abc",
  "name": "code_execution",
  "input": {
    "code": "team = get_team_members('engineering')\n..." # the code example above
  }
}
```

#### 3。工具在不触及 Claude 上下文的情况下执行

当代码调用 get\_expenses() 时，您会收到带有调用者字段的工具请求：

```
{
  "type": "tool_use",
  "id": "toolu_xyz",
  "name": "get_expenses",
  "input": {"user_id": "emp_123", "quarter": "Q3"},
  "caller": {
    "type": "code_execution_20250825",
    "tool_id": "srvtoolu_abc"
  }
}
```

您提供结果，该结果在代码执行环境而不是 Claude 的上下文中进行处理。对于代码中的每个工具调用，都会重复此请求-响应循环。

#### 4。只有最终输出进入上下文

当代码运行完成后，仅将代码的结果返回到Claude：

```
{
  "type": "code_execution_tool_result",
  "tool_use_id": "srvtoolu_abc",
  "content": {
    "stdout": "[{\"name\": \"Alice\", \"spent\": 12500, \"limit\": 10000}...]"
  }
}
```

这是 Claude 看到的所有内容，而不是一路处理的 2000+ 费用行项目。

### 何时使用编程工具调用

编程工具调用向您的工作流程添加了代码执行步骤。当令牌节省、延迟改进和准确性提升显着时，这种额外的开销就会得到回报。

**最有利的时间：**

* 处理仅需要聚合或摘要的大型数据集
* 使用三个或更多相关工具调用运行多步骤工作流
* 在 Claude 看到工具结果之前对其进行过滤、排序或转换
* 处理中间数据不应影响 Claude 推理的任务
* 跨多个项目运行并行操作（例如，检查 50 端点）

**在以下情况下效果较差：**

* 进行简单的单一工具调用
* 处理 Claude 应该查看并推理所有中间结果的任务
* 以较小的响应运行快速查找

## 工具使用示例

### 挑战

JSON Schema 擅长定义结构（类型、必填字段、允许的枚举），但它无法表达使用模式：何时包含可选参数、哪些组合有意义，或者 API 期望什么约定。

考虑一张支持票 API：

```
{
  "name": "create_ticket",
  "input_schema": {
    "properties": {
      "title": {"type": "string"},
      "priority": {"enum": ["low", "medium", "high", "critical"]},
      "labels": {"type": "array", "items": {"type": "string"}},
      "reporter": {
        "type": "object",
        "properties": {
          "id": {"type": "string"},
          "name": {"type": "string"},
          "contact": {
            "type": "object",
            "properties": {
              "email": {"type": "string"},
              "phone": {"type": "string"}
            }
          }
        }
      },
      "due_date": {"type": "string"},
      "escalation": {
        "type": "object",
        "properties": {
          "level": {"type": "integer"},
          "notify_manager": {"type": "boolean"},
          "sla_hours": {"type": "integer"}
        }
      }
    },
    "required": ["title"]
  }
}
```

该模式定义了什么是有效的，但没有回答关键问题：

* **格式歧义：** `due_date` 应该使用“2024-11-06”、“11 月 6、2024”，或“2024-11-06T00：00：00Z”？
* **ID 约定：** `reporter.id` 是 UUID、“USR-12345”还是只是“12345”？
* **嵌套结构用法：**何时应Claude填充`reporter.contact`？
* **参数关联：**`escalation.level`和`escalation.sla_hours`与优先级有何关系？

这些歧义可能导致格式错误的工具调用和不一致的参数使用。

### 我们的解决方案

工具使用示例让您可以直接在工具定义中提供示例工具调用。您不只依赖架构，而是展示 Claude 具体使用模式：

```
{
    "name": "create_ticket",
    "input_schema": { /* same schema as above */ },
    "input_examples": [
      {
        "title": "Login page returns 500 error",
        "priority": "critical",
        "labels": ["bug", "authentication", "production"],
        "reporter": {
          "id": "USR-12345",
          "name": "Jane Smith",
          "contact": {
            "email": "jane@acme.com",
            "phone": "+1-555-0123"
          }
        },
        "due_date": "2024-11-06",
        "escalation": {
          "level": 2,
          "notify_manager": true,
          "sla_hours": 4
        }
      },
      {
        "title": "Add dark mode support",
        "labels": ["feature-request", "ui"],
        "reporter": {
          "id": "USR-67890",
          "name": "Alex Chen"
        }
      },
      {
        "title": "Update API documentation"
      }
    ]
  }
```

从这三个例子中，Claude了解到：

* **格式约定**：日期使用YYYY-MM-DD，用户ID遵循USR-XXXXX，标签使用短横线大小写
* **嵌套结构模式**：如何使用嵌套的联系人对象构造报告者对象
* **可选参数相关性**：关键错误具有完整的联系信息+严格SLA的升级；功能请求有记者但没有 contact/escalation；内部任务只有标题

在我们自己的内部测试中，工具使用示例将复杂参数处理的准确性从 72% 提高到 90%。

### 何时使用工具使用示例

工具使用示例将标记添加到您的工具定义中，因此当准确性改进超过额外成本时，它们是最有价值的。

**最有利的时间：**

* 复杂的嵌套结构，其中有效的 JSON 并不意味着正确的用法
* 具有许多可选参数和包含模式的工具很重要
* 具有特定于域的约定的 API 未在架构中捕获
* 类似的工具，其中的示例阐明了要使用哪一个（例如，`create_ticket` 与 `create_incident`）

**在以下情况下效果较差：**

* 简单的单参数工具，用法一目了然
* Claude 已经理解的标准格式，例如 URL 或电子邮件
* JSON 模式约束可以更好地处理验证问题

## 最佳实践

构建采取现实世界行动的 agents 意味着同时处理规模、复杂性和精度。这三个功能共同解决工具使用工作流程中的不同瓶颈。以下是如何有效地将它们结合起来。

### 战略性地分层特征

并非每个 agent 都需要针对给定任务使用所有三个功能。从你最大的瓶颈开始：

* 工具定义中的上下文膨胀 → 工具搜索工具
* 大量中间结果污染上下文 → 编程工具调用
* 参数错误和格式错误的调用 → 工具使用示例

这种集中的方法可以让您解决限制 agent 性能的特定约束，而不是预先增加复杂性。

然后根据需要分层附加功能。它们是互补的：工具搜索工具确保找到正确的工具，编程工具调用确保高效执行，工具使用示例确保正确调用。

### 设置工具搜索工具以更好地发现

工具搜索与名称和描述相匹配，因此清晰的描述性定义可以提高发现的准确性。

```
// Good
{
    "name": "search_customer_orders",
    "description": "Search for customer orders by date range, status, or total amount. Returns order details including items, shipping, and payment info."
}

// Bad
{
    "name": "query_db_orders",
    "description": "Execute order query"
}
```

添加系统提示指导，以便 Claude 知道可用的内容：

```
You have access to tools for Slack messaging, Google Drive file management, 
Jira ticket tracking, and GitHub repository operations. Use the tool search 
to find specific capabilities.
```

始终加载三到五个最常用的工具，推迟其余的。这可以平衡常见操作的即时访问与其他所有内容的按需发现。

### 设置编程工具调用以正确执行

由于Claude编写代码来解析工具输出，因此文档返回格式清晰。这有助于Claude编写正确的解析逻辑：

```
{
    "name": "get_orders",
    "description": "Retrieve orders for a customer.
Returns:
    List of order objects, each containing:
    - id (str): Order identifier
    - total (float): Order total in USD
    - status (str): One of 'pending', 'shipped', 'delivered'
    - items (list): Array of {sku, quantity, price}
    - created_at (str): ISO 8601 timestamp"
}
```

请参阅下文，了解受益于程序化编排的选择加入工具：

* 可以并行运行的工具（独立操作）
* 可安全重试的操作（幂等）

### 设置工具使用示例以确保参数准确性

行为清晰的工艺示例：

* 使用真实的数据（真实的城市名称、合理的价格，而不是“字符串”或“值”）
* 以最小、部分和完整规格模式显示多样性
* 保持简洁：1-5 每个工具的示例
* 关注歧义（仅添加模式中正确用法不明显的示例）

## 开始使用

这些功能在测试版中可用。要启用它们，请添加 beta 标头并包含您需要的工具：

```
client.beta.messages.create(
    betas=["advanced-tool-use-2025-11-20"],
    model="claude-sonnet-4-5-20250929",
    max_tokens=4096,
    tools=[
        {"type": "tool_search_tool_regex_20251119", "name": "tool_search_tool_regex"},
        {"type": "code_execution_20250825", "name": "code_execution"},
        # Your tools with defer_loading, allowed_callers, and input_examples
    ]
)
```

有关详细的 API 文档和 SDK 示例，请参阅我们的：

* 工具搜索工具的[文档](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool>)和[说明书](<https://github.com/anthropics/claude-cookbooks/blob/main/tool_use/tool_search_with_embeddings.ipynb>)
* 用于编程工具调用的[文档](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling>)和[说明书](<https://github.com/anthropics/claude-cookbooks/blob/main/tool_use/programmatic_tool_calling_ptc.ipynb>)
* [工具使用示例文档](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/implement-tool-use#providing-tool-use-examples>)

这些功能将工具的使用从简单的函数调用转向智能编排。随着 agents 处理跨越数十种工具和大型数据集的更复杂的工作流程，动态发现、高效执行和可靠调用成为基础。

我们很高兴看到您所构建的内容。

## 致谢

由 Bin Wu 编写，Adam Jones、Artur Renault、Henry Tay、Jake Noble、Noah Picard、Sam Jiang 和 Claude 开发者平台团队的贡献。这项工作建立在 Chris Gorgolewski、Daniel Jiang、Jeremy Fox 和 Mike Lambert 的基础研究的基础上。我们还从 AI 生态系统中汲取灵感，包括 [Joel Pobar 的 LLMVM](<https://github.com/9600dev/llmvm>)、[Cloudflare 的代码模式 ](<https://blog.cloudflare.com/code-mode/>) 和 [ 代码执行为 MCP](<https://www.anthropic.com/engineering/code-execution-with-mcp>)。特别感谢 Andy Schumeister、Hamish Kerr、Keir Bradwell、Matt Bleifer 和 Molly Vorwerck 的支持。


## 原文对照

<details>
<summary>展开完整原文</summary>

The future of AI agents is one where models work seamlessly across hundreds or thousands of tools. An IDE assistant that integrates git operations, file manipulation, package managers, testing frameworks, and deployment pipelines. An operations coordinator that connects Slack, GitHub, Google Drive, Jira, company databases, and dozens of MCP servers simultaneously.

To [build effective agents](<https://www.anthropic.com/research/building-effective-agents>), they need to work with unlimited tool libraries without stuffing every definition into context upfront. Our blog article on using [code execution with MCP](<https://www.anthropic.com/engineering/code-execution-with-mcp>) discussed how tool results and definitions can sometimes consume 50,000+ tokens before an agent reads a request. Agents should discover and load tools on-demand, keeping only what's relevant for the current task.

Agents also need the ability to call tools from code. When using natural language tool calling, each invocation requires a full inference pass, and intermediate results pile up in context whether they're useful or not. Code is a natural fit for orchestration logic, such as loops, conditionals, and data transformations. Agents need the flexibility to choose between code execution and inference based on the task at hand.

Agents also need to learn correct tool usage from examples, not just schema definitions. JSON schemas define what's structurally valid, but can't express usage patterns: when to include optional parameters, which combinations make sense, or what conventions your API expects.

Today, we're releasing three features that make this possible:

* **Tool Search Tool,** which allows Claude to use search tools to access thousands of tools without consuming its context window
* **Programmatic Tool Calling**, which allows Claude to invoke tools in a code execution environment reducing the impact on the model’s context window
* **Tool Use Examples**, which provides a universal standard for demonstrating how to effectively use a given tool

In internal testing, we’ve found these features have helped us build things that wouldn’t have been possible with conventional tool use patterns. For example, **[Claude for Excel](<https://www.claude.com/claude-for-excel>)** uses Programmatic Tool Calling to read and modify spreadsheets with thousands of rows without overloading the model’s context window.

Based on our experience, we believe these features open up new possibilities for what you can build with Claude.

## Tool Search Tool

### The challenge

MCP tool definitions provide important context, but as more servers connect, those tokens can add up. Consider a five-server setup:

* GitHub: 35 tools (~26K tokens)
* Slack: 11 tools (~21K tokens)
* Sentry: 5 tools (~3K tokens)
* Grafana: 5 tools (~3K tokens)
* Splunk: 2 tools (~2K tokens)

That's 58 tools consuming approximately 55K tokens before the conversation even starts. Add more servers like Jira (which alone uses ~17K tokens) and you're quickly approaching 100K+ token overhead. At Anthropic, we've seen tool definitions consume 134K tokens before optimization.

But token cost isn't the only issue. The most common failures are wrong tool selection and incorrect parameters, especially when tools have similar names like `notification-send-user` vs. `notification-send-channel`.

### Our solution

Instead of loading all tool definitions upfront, the Tool Search Tool discovers tools on-demand. Claude only sees the tools it actually needs for the current task.

![Tool Search Tool diagram](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2Ff359296f770706608901eadaffbff4ca0b67874c-1999x1125.png&w=3840&q=75>)

*Tool Search Tool preserves 191,300 tokens of context compared to 122,800 with Claude’s traditional approach.*

Traditional approach:

* All tool definitions loaded upfront (~72K tokens for 50+ MCP tools)
* Conversation history and system prompt compete for remaining space
* Total context consumption: ~77K tokens before any work begins

With the Tool Search Tool:

* Only the Tool Search Tool loaded upfront (~500 tokens)
* Tools discovered on-demand as needed (3-5 relevant tools, ~3K tokens)
* Total context consumption: ~8.7K tokens, preserving 95% of context window

This represents an 85% reduction in token usage while maintaining access to your full tool library. Internal testing showed significant accuracy improvements on MCP evaluations when working with large tool libraries. Opus 4 improved from 49% to 74%, and Opus 4.5 improved from 79.5% to 88.1% with Tool Search Tool enabled.

### How the Tool Search Tool works

The Tool Search Tool lets Claude dynamically discover tools instead of loading all definitions upfront. You provide all your tool definitions to the API, but mark tools with `defer_loading: true` to make them discoverable on-demand. Deferred tools aren't loaded into Claude's context initially. Claude only sees the Tool Search Tool itself plus any tools with `defer_loading: false` (your most critical, frequently-used tools).

When Claude needs specific capabilities, it searches for relevant tools. The Tool Search Tool returns references to matching tools, which get expanded into full definitions in Claude's context.

For example, if Claude needs to interact with GitHub, it searches for "github," and only `github.createPullRequest` and `github.listIssues` get loaded—not your other 50+ tools from Slack, Jira, and Google Drive.

This way, Claude has access to your full tool library while only paying the token cost for tools it actually needs.

**Prompt caching note:** Tool Search Tool doesn't break prompt caching because deferred tools are excluded from the initial prompt entirely. They're only added to context after Claude searches for them, so your system prompt and core tool definitions remain cacheable.

**Implementation:**

```
{
  "tools": [
    // Include a tool search tool (regex, BM25, or custom)
    {"type": "tool_search_tool_regex_20251119", "name": "tool_search_tool_regex"},

    // Mark tools for on-demand discovery
    {
      "name": "github.createPullRequest",
      "description": "Create a pull request",
      "input_schema": {...},
      "defer_loading": true
    }
    // ... hundreds more deferred tools with defer_loading: true
  ]
}
```

For MCP servers, you can defer loading entire servers while keeping specific high-use tools loaded:

```
{
  "type": "mcp_toolset",
  "mcp_server_name": "google-drive",
  "default_config": {"defer_loading": true}, # defer loading the entire server
  "configs": {
    "search_files": {
"defer_loading": false
    }  // Keep most used tool loaded
  }
}
```

The Claude Developer Platform provides regex-based and BM25-based search tools out of the box, but you can also implement custom search tools using embeddings or other strategies.

### When to use the Tool Search Tool

Like any architectural decision, enabling the Tool Search Tool involves trade-offs. The feature adds a search step before tool invocation, so it delivers the best ROI when the context savings and accuracy improvements outweigh additional latency.

**Use it when:**

* Tool definitions consuming >10K tokens
* Experiencing tool selection accuracy issues
* Building MCP-powered systems with multiple servers
* 10+ tools available

**Less beneficial when:**

* Small tool library (<10 tools)
* All tools used frequently in every session
* Tool definitions are compact

## Programmatic Tool Calling

### The challenge

Traditional tool calling creates two fundamental problems as workflows become more complex:

* **Context pollution from intermediate results**: When Claude analyzes a 10MB log file for error patterns, the entire file enters its context window, even though Claude only needs a summary of error frequencies. When fetching customer data across multiple tables, every record accumulates in context regardless of relevance. These intermediate results consume massive token budgets and can push important information out of the context window entirely.
* **Inference overhead and manual synthesis**: Each tool call requires a full model inference pass. After receiving results, Claude must "eyeball" the data to extract relevant information, reason about how pieces fit together, and decide what to do next—all through natural language processing. A five tool workflow means five inference passes plus Claude parsing each result, comparing values, and synthesizing conclusions. This is both slow and error-prone.

### Our solution

Programmatic Tool Calling enables Claude to orchestrate tools through code rather than through individual API round-trips. Instead of Claude requesting tools one at a time with each result being returned to its context, Claude writes code that calls multiple tools, processes their outputs, and controls what information actually enters its context window.

Claude excels at writing code and by letting it express orchestration logic in Python rather than through natural language tool invocations, you get more reliable, precise control flow. Loops, conditionals, data transformations, and error handling are all explicit in code rather than implicit in Claude's reasoning.

#### Example: Budget compliance check

Consider a common business task: "Which team members exceeded their Q3 travel budget?"

You have three tools available:

* `get_team_members(department)` - Returns team member list with IDs and levels
* `get_expenses(user_id, quarter)` - Returns expense line items for a user
* `get_budget_by_level(level)` - Returns budget limits for an employee level

**Traditional approach**:

* Fetch team members → 20 people
* For each person, fetch their Q3 expenses → 20 tool calls, each returning 50-100 line items (flights, hotels, meals, receipts)
* Fetch budget limits by employee level
* All of this enters Claude's context: 2,000+ expense line items (50 KB+)
* Claude manually sums each person's expenses, looks up their budget, compares expenses against budget limits
* More round-trips to the model, significant context consumption

**With Programmatic Tool Calling**:

Instead of each tool result returning to Claude, Claude writes a Python script that orchestrates the entire workflow. The script runs in the Code Execution tool (a sandboxed environment), pausing when it needs results from your tools. When you return tool results via the API, they're processed by the script rather than consumed by the model. The script continues executing, and Claude only sees the final output.

![Programmatic tool calling flow](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F65737d69a3290ed5c1f3c3b8dc873645a9dcc2eb-1999x1491.png&w=3840&q=75>)

Programmatic Tool Calling enables Claude to orchestrate tools through code rather than through individual API round-trips, allowing for parallel tool execution.

Here's what Claude's orchestration code looks like for the budget compliance task:

```
team = await get_team_members("engineering")

# Fetch budgets for each unique level
levels = list(set(m["level"] for m in team))
budget_results = await asyncio.gather(*[
    get_budget_by_level(level) for level in levels
])

# Create a lookup dictionary: {"junior": budget1, "senior": budget2, ...}
budgets = {level: budget for level, budget in zip(levels, budget_results)}

# Fetch all expenses in parallel
expenses = await asyncio.gather(*[
    get_expenses(m["id"], "Q3") for m in team
])

# Find employees who exceeded their travel budget
exceeded = []
for member, exp in zip(team, expenses):
    budget = budgets[member["level"]]
    total = sum(e["amount"] for e in exp)
    if total > budget["travel_limit"]:
        exceeded.append({
            "name": member["name"],
            "spent": total,
            "limit": budget["travel_limit"]
        })

print(json.dumps(exceeded))
```

Claude's context receives only the final result: the two to three people who exceeded their budget. The 2,000+ line items, the intermediate sums, and the budget lookups do not affect Claude’s context, reducing consumption from 200KB of raw expense data to just 1KB of results.

The efficiency gains are substantial:

* **Token savings**: By keeping intermediate results out of Claude's context, PTC dramatically reduces token consumption. Average usage dropped from 43,588 to 27,297 tokens, a 37% reduction on complex research tasks.
* **Reduced latency**: Each API round-trip requires model inference (hundreds of milliseconds to seconds). When Claude orchestrates 20+ tool calls in a single code block, you eliminate 19+ inference passes. The API handles tool execution without returning to the model each time.
* **Improved accuracy**: By writing explicit orchestration logic, Claude makes fewer errors than when juggling multiple tool results in natural language. Internal knowledge retrieval improved from 25.6% to 28.5%; [GIA benchmarks](<https://arxiv.org/abs/2311.12983>) from 46.5% to 51.2%.

Production workflows involve messy data, conditional logic, and operations that need to scale. Programmatic Tool Calling lets Claude handle that complexity programmatically while keeping its focus on actionable results rather than raw data processing.

### How Programmatic Tool Calling works

#### 1. Mark tools as callable from code

Add code\_execution to tools, and set allowed\_callers to opt-in tools for programmatic execution:

```
{
  "tools": [
    {
      "type": "code_execution_20250825",
      "name": "code_execution"
    },
    {
      "name": "get_team_members",
      "description": "Get all members of a department...",
      "input_schema": {...},
      "allowed_callers": ["code_execution_20250825"] # opt-in to programmatic tool calling
    },
    {
      "name": "get_expenses",
 	...
    },
    {
      "name": "get_budget_by_level",
	...
    }
  ]
}
```

The API converts these tool definitions into Python functions that Claude can call.

#### 2. Claude writes orchestration code

Instead of requesting tools one at a time, Claude generates Python code:

```
{
  "type": "server_tool_use",
  "id": "srvtoolu_abc",
  "name": "code_execution",
  "input": {
    "code": "team = get_team_members('engineering')\n..." # the code example above
  }
}
```

#### 3. Tools execute without hitting Claude's context

When the code calls get\_expenses(), you receive a tool request with a caller field:

```
{
  "type": "tool_use",
  "id": "toolu_xyz",
  "name": "get_expenses",
  "input": {"user_id": "emp_123", "quarter": "Q3"},
  "caller": {
    "type": "code_execution_20250825",
    "tool_id": "srvtoolu_abc"
  }
}
```

You provide the result, which is processed in the Code Execution environment rather than Claude's context. This request-response cycle repeats for each tool call in the code.

#### 4. Only final output enters context

When the code finishes running, only the results of the code are returned to Claude:

```
{
  "type": "code_execution_tool_result",
  "tool_use_id": "srvtoolu_abc",
  "content": {
    "stdout": "[{\"name\": \"Alice\", \"spent\": 12500, \"limit\": 10000}...]"
  }
}
```

This is all Claude sees, not the 2000+ expense line items processed along the way.

### When to use Programmatic Tool Calling

Programmatic Tool Calling adds a code execution step to your workflow. This extra overhead pays off when the token savings, latency improvements, and accuracy gains are substantial.

**Most beneficial when:**

* Processing large datasets where you only need aggregates or summaries
* Running multi-step workflows with three or more dependent tool calls
* Filtering, sorting, or transforming tool results before Claude sees them
* Handling tasks where intermediate data shouldn't influence Claude's reasoning
* Running parallel operations across many items (checking 50 endpoints, for example)

**Less beneficial when:**

* Making simple single-tool invocations
* Working on tasks where Claude should see and reason about all intermediate results
* Running quick lookups with small responses

## Tool Use Examples

### The challenge

JSON Schema excels at defining structure–types, required fields, allowed enums–but it can't express usage patterns: when to include optional parameters, which combinations make sense, or what conventions your API expects.

Consider a support ticket API:

```
{
  "name": "create_ticket",
  "input_schema": {
    "properties": {
      "title": {"type": "string"},
      "priority": {"enum": ["low", "medium", "high", "critical"]},
      "labels": {"type": "array", "items": {"type": "string"}},
      "reporter": {
        "type": "object",
        "properties": {
          "id": {"type": "string"},
          "name": {"type": "string"},
          "contact": {
            "type": "object",
            "properties": {
              "email": {"type": "string"},
              "phone": {"type": "string"}
            }
          }
        }
      },
      "due_date": {"type": "string"},
      "escalation": {
        "type": "object",
        "properties": {
          "level": {"type": "integer"},
          "notify_manager": {"type": "boolean"},
          "sla_hours": {"type": "integer"}
        }
      }
    },
    "required": ["title"]
  }
}
```

The schema defines what's valid, but leaves critical questions unanswered:

* **Format ambiguity:** Should `due_date` use "2024-11-06", "Nov 6, 2024", or "2024-11-06T00:00:00Z"?
* **ID conventions:** Is `reporter.id` a UUID, "USR-12345", or just "12345"?
* **Nested structure usage:** When should Claude populate `reporter.contact`?
* **Parameter correlations:** How do `escalation.level` and `escalation.sla_hours` relate to priority?

These ambiguities can lead to malformed tool calls and inconsistent parameter usage.

### Our solution

Tool Use Examples let you provide sample tool calls directly in your tool definitions. Instead of relying on schema alone, you show Claude concrete usage patterns:

```
{
    "name": "create_ticket",
    "input_schema": { /* same schema as above */ },
    "input_examples": [
      {
        "title": "Login page returns 500 error",
        "priority": "critical",
        "labels": ["bug", "authentication", "production"],
        "reporter": {
          "id": "USR-12345",
          "name": "Jane Smith",
          "contact": {
            "email": "jane@acme.com",
            "phone": "+1-555-0123"
          }
        },
        "due_date": "2024-11-06",
        "escalation": {
          "level": 2,
          "notify_manager": true,
          "sla_hours": 4
        }
      },
      {
        "title": "Add dark mode support",
        "labels": ["feature-request", "ui"],
        "reporter": {
          "id": "USR-67890",
          "name": "Alex Chen"
        }
      },
      {
        "title": "Update API documentation"
      }
    ]
  }
```

From these three examples, Claude learns:

* **Format conventions**: Dates use YYYY-MM-DD, user IDs follow USR-XXXXX, labels use kebab-case
* **Nested structure patterns**: How to construct the reporter object with its nested contact object
* **Optional parameter correlations**: Critical bugs have full contact info + escalation with tight SLAs; feature requests have reporter but no contact/escalation; internal tasks have title only

In our own internal testing, tool use examples improved accuracy from 72% to 90% on complex parameter handling.

### When to use Tool Use Examples

Tool Use Examples add tokens to your tool definitions, so they’re most valuable when accuracy improvements outweigh the additional cost.

**Most beneficial when:**

* Complex nested structures where valid JSON doesn't imply correct usage
* Tools with many optional parameters and inclusion patterns matter
* APIs with domain-specific conventions not captured in schemas
* Similar tools where examples clarify which one to use (e.g., `create_ticket` vs `create_incident`)

**Less beneficial when:**

* Simple single-parameter tools with obvious usage
* Standard formats like URLs or emails that Claude already understands
* Validation concerns better handled by JSON Schema constraints

## Best practices

Building agents that take real-world actions means handling scale, complexity, and precision simultaneously. These three features work together to solve different bottlenecks in tool use workflows. Here's how to combine them effectively.

### Layer features strategically

Not every agent needs to use all three features for a given task. Start with your biggest bottleneck:

* Context bloat from tool definitions → Tool Search Tool
* Large intermediate results polluting context → Programmatic Tool Calling
* Parameter errors and malformed calls → Tool Use Examples

This focused approach lets you address the specific constraint limiting your agent's performance, rather than adding complexity upfront.

Then layer additional features as needed. They're complementary: Tool Search Tool ensures the right tools are found, Programmatic Tool Calling ensures efficient execution, and Tool Use Examples ensure correct invocation.

### Set up Tool Search Tool for better discovery

Tool search matches against names and descriptions, so clear, descriptive definitions improve discovery accuracy.

```
// Good
{
    "name": "search_customer_orders",
    "description": "Search for customer orders by date range, status, or total amount. Returns order details including items, shipping, and payment info."
}

// Bad
{
    "name": "query_db_orders",
    "description": "Execute order query"
}
```

Add system prompt guidance so Claude knows what's available:

```
You have access to tools for Slack messaging, Google Drive file management, 
Jira ticket tracking, and GitHub repository operations. Use the tool search 
to find specific capabilities.
```

Keep your three to five most-used tools always loaded, defer the rest. This balances immediate access for common operations with on-demand discovery for everything else.

### Set up Programmatic Tool Calling for correct execution

Since Claude writes code to parse tool outputs, document return formats clearly. This helps Claude write correct parsing logic:

```
{
    "name": "get_orders",
    "description": "Retrieve orders for a customer.
Returns:
    List of order objects, each containing:
    - id (str): Order identifier
    - total (float): Order total in USD
    - status (str): One of 'pending', 'shipped', 'delivered'
    - items (list): Array of {sku, quantity, price}
    - created_at (str): ISO 8601 timestamp"
}
```

See below for opt-in tools that benefit from programmatic orchestration:

* Tools that can run in parallel (independent operations)
* Operations safe to retry (idempotent)

### Set up Tool Use Examples for parameter accuracy

Craft examples for behavioral clarity:

* Use realistic data (real city names, plausible prices, not "string" or "value")
* Show variety with minimal, partial, and full specification patterns
* Keep it concise: 1-5 examples per tool
* Focus on ambiguity (only add examples where correct usage isn't obvious from schema)

## Getting started

These features are available in beta. To enable them, add the beta header and include the tools you need:

```
client.beta.messages.create(
    betas=["advanced-tool-use-2025-11-20"],
    model="claude-sonnet-4-5-20250929",
    max_tokens=4096,
    tools=[
        {"type": "tool_search_tool_regex_20251119", "name": "tool_search_tool_regex"},
        {"type": "code_execution_20250825", "name": "code_execution"},
        # Your tools with defer_loading, allowed_callers, and input_examples
    ]
)
```

For detailed API documentation and SDK examples, see our:

* [Documentation](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool>) and [cookbook](<https://github.com/anthropics/claude-cookbooks/blob/main/tool_use/tool_search_with_embeddings.ipynb>) for Tool Search Tool
* [Documentation](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling>) and [cookbook](<https://github.com/anthropics/claude-cookbooks/blob/main/tool_use/programmatic_tool_calling_ptc.ipynb>) for Programmatic Tool Calling
* [Documentation](<https://platform.claude.com/docs/en/agents-and-tools/tool-use/implement-tool-use#providing-tool-use-examples>) for Tool Use Examples

These features move tool use from simple function calling toward intelligent orchestration. As agents tackle more complex workflows spanning dozens of tools and large datasets, dynamic discovery, efficient execution, and reliable invocation become foundational.

We're excited to see what you build.

## Acknowledgements

Written by Bin Wu, with contributions from Adam Jones, Artur Renault, Henry Tay, Jake Noble, Noah Picard, Sam Jiang, and the Claude Developer Platform team. This work builds on foundational research by Chris Gorgolewski, Daniel Jiang, Jeremy Fox and Mike Lambert. We also drew inspiration from across the AI ecosystem, including [Joel Pobar's LLMVM](<https://github.com/9600dev/llmvm>), [Cloudflare's Code Mode](<https://blog.cloudflare.com/code-mode/>) and [Code Execution as MCP](<https://www.anthropic.com/engineering/code-execution-with-mcp>). Special thanks to Andy Schumeister, Hamish Kerr, Keir Bradwell, Matt Bleifer and Molly Vorwerck for their support.


</details>
