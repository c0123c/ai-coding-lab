# Google ADK 评测的环境模拟

介绍外部工具的固定响应、错误、延迟和条件注入以及插件配置，帮助在测试中复现失败而避免调用真实依赖。

- 来源：Google／Agent Development Kit
- 资料类型：存档文档
- 存档版本：`12712d00d487`。
- 文档时间：源码提交（存档版）：[2026-08-14](<https://github.com/google/adk-docs/commit/1bd29540df973965c7876ec23191a708223a0c02>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google/adk-docs/blob/main/docs/evaluate/environment_simulation.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Agent Development Kit/Google ADK 评测的环境模拟.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 评估环境模拟

<div class="language-support-tag">
 <span class="lst-supported">ADK</span><span class="lst-python">Python v1.24.0</span>
</div> 支持

在评估依赖于外部依赖项（例如 API、
数据库或第三方服务）的 agents 时，在测试期间实时运行这些工具可能会 
 速度缓慢、成本高昂或不可靠。 **环境模拟器**可让您在agent执行期间安全
拦截这些工具调用，并将其替换为
受控的确定性响应，而无需修改agent本身。这种
方法可以填补agent改进循环中的关键空白，使您能够
创建封闭的离线测试运行，隔离您的agent逻辑，以实现可靠的
评分。

总的来说，此功能可以让您：

*   测试 agent 如何处理 API 错误或边缘情况响应。
*   离线运行评估，无需访问实时后端。
*   使用 LLM 自动生成真实的模拟响应。
*   通过播种概率注入来产生可重复的测试运行。

环境模拟通过 
[`before_tool_callback`](<https://google.github.io/adk-docs/callbacks/types-of-callbacks/#tool-execution-callbacks>)
 挂钩或 [ 插件系统 ](<https://google.github.io/adk-docs/plugins/>) 与 ADK 的工具执行管道集成，因此不需要对 agent 代码进行 
 更改。

```
The Environment Simulation is an experimental feature. Its API may change in future
releases.
```

## 它是如何运作的

[用户模拟](<https://google.github.io/adk-docs/evaluate/user-sim/>)
推动对话向前发展，而环境模拟则提供稳定的
后端。在较高的层面上，环境模拟器位于您的 agent 和 
 工具之间。当 agent 调用工具时，模拟器会拦截该调用，并 
 决定是否返回合成响应 - 预定义的注入 
 或 LLM- 生成的模拟 - 或者让真正的工具执行。

对于每个配置的工具，决策逻辑遵循以下顺序：

1.  首先按顺序检查**注入配置**。如果匹配注射
找到（基于参数匹配和概率），立即返回其错误或响应
。
2.  如果没有应用注入配置，则 ** 模拟策略 ** 用作后备。的
模拟器调用 LLM 根据工具的 
 模式和任何有状态上下文生成真实响应。
3.  如果该工具不在模拟器配置中，则返回 **No-op** (`None`)，
允许真正的工具正常执行。

## 整合

`EnvironmentSimulationFactory` 类提供两个集成点：

*   `create_callback()` - 返回一个适合用作
任何 `LlmAgent` 上的 `before_tool_callback`。
*   `create_plugin()` — 返回一个 `EnvironmentSimulationPlugin` 实例
与ADK插件系统集成。

### 用作回调

以下示例演示如何创建环境模拟作为 adk agent 回调之一。


```python
from google.adk.agents import LlmAgent
from google.adk.tools.environment_simulation import EnvironmentSimulationFactory
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    InjectedError,
    InjectionConfig,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="get_user_profile",
            injection_configs=[
                InjectionConfig(
                    injected_error=InjectedError(
                        injected_http_error_code=503,
                        error_message="Service temporarily unavailable.",
                    )
                )
            ],
        )
    ]
)

agent = LlmAgent(
    name="my_agent",
    model="gemini-flash-latest",
    tools=[get_user_profile],
    before_tool_callback=EnvironmentSimulationFactory.create_callback(config),
)
```

### 作为插件使用

以下示例展示了如何创建环境模拟作为 ADK agent 插件。

```python
from google.adk.apps import App
from google.adk.tools.environment_simulation import EnvironmentSimulationFactory
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    MockStrategy,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        )
    ]
)

app = App(
    name="my_app",
    root_agent=my_agent,
    plugins=[EnvironmentSimulationFactory.create_plugin(config)],
)
```

## 配置参考

您可以使用一组数据类配置环境模拟器。 
以下部分提供了每个配置对象的详细参考。

### `EnvironmentSimulationConfig`

顶级配置对象。

字段 | 类型 | 默认值 | 描述 
:------------------------------- | :---------------------------- | :-------------------- | ：----------
`tool_simulation_configs` | `List[ToolSimulationConfig]` | 需要 | 每个工具需要一个条目进行模拟。不能为空，且工具名称必须唯一。
`simulation_model` | `str` | `"gemini-flash-latest"` | 用于工具连接分析和模拟响应的 LLM 
`simulation_model_configuration` | `GenerateContentConfig` | 思考启用 | LLM 内部模拟器调用的生成配置。
`environment_data` | `str \| None` | `None` | 可选环境上下文（例如，JSON 数据库快照）传递给模拟策略以生成更真实的响应。
`tracing` | `str \| None` | `None` | 跟踪数据（例如，JSON 字符串格式的先前 agent 运行跟踪）以提供历史上下文。

### `ToolSimulationConfig`

定义如何模拟单个命名工具。

字段 | 类型 | 默认值 | 描述 
:-------------------- | :------------------------ | :------------------------ | :----------
`tool_name` | `str` | 必需 | 必须与工具的注册名称完全匹配。
`injection_configs` | `List[InjectionConfig]` | `[]` | 零个或多个注入配置，在模拟策略之前按顺序检查。
`mock_strategy_type` | `MockStrategy` | `MOCK_STRATEGY_UNSPECIFIED` | 未触发注入时的回退策略。

### `InjectionConfig`

控制可注入到工具调用中的单个合成响应。
必须设置 `injected_error` 或 `injected_response` 之一。

字段 | 类型 | 默认值 | 描述 
:---------------------------------------- | :------------------------ | :-------- | :----------
`injected_error` | `InjectedError \| None` | `None` | 返回错误（与 `injected_response` 互斥）。
`injected_response` | `Dict[str, Any] \| None` | `None` | 修复返回的响应字典（与 `injected_error` 互斥）。
`injection_probability` | `float` | `1.0` | 此注入触发的概率 `[0.0, 1.0]`。
`match_args` | `Dict[str, Any] \| None` | `None` | 如果设置，则仅当工具的参数包含 `match_args`.
`injected_latency_seconds` | `float` | `0.0` 中的所有键值对时才会触发注入| 在返回注入结果之前添加人工延迟（≤ 120 s）。
`random_seed` | `int \| None` | `None` | 用于概率检查的种子，启用确定性注入行为。

### `InjectedError`

定义 HTTP- 样式错误响应。

|领域|类型|描述|
| :------------------------- | :---- | :-------------------------------------- |
| `injected_http_error_code` | `int` |HTTP 状态代码显示为|
: : : 工具响应中的`"error_code"`。    :
| `error_message`            | `str` |人类可读的消息出现为|
: : : 工具响应中的`"error_message"`。 :

### `MockStrategy`

枚举控制模拟器在没有注入触发时如何生成响应。

|价值|描述|
| :------------------------ | :---------------------------------------------- |
| `MOCK_STRATEGY_TOOL_SPEC` |使用该工具的架构和有状态上下文|
: : 提示 LLM 生成真实的响应。 :
| `MOCK_STRATEGY_TRACING`   |*（已弃用）* 请使用|
: : `MOCK_STRATEGY_TOOL_SPEC` 带跟踪输入。   :

## 注射方式

使用注入配置来测试特定的故障或边缘情况场景。
注入按列表顺序进行评估；应用满足 `match_args`
 标准（且概率检查通过）的第一个。

### 注入错误

以下示例展示了如何将具有特定错误代码和错误消息的错误注入到agent。

```python
from google.adk.tools.environment_simulation.environment_simulation_config import (
    InjectedError,
    InjectionConfig,
    ToolSimulationConfig,
)

ToolSimulationConfig(
    tool_name="charge_payment",
    injection_configs=[
        InjectionConfig(
            injected_error=InjectedError(
                injected_http_error_code=402,
                error_message="Payment declined.",
            )
        )
    ],
)
```

agent 将收到 `{"error_code": 402, "error_message": "Payment
declined."}`，而不是真实的工具结果，以便您评估 
agent 如何处理付款失败。

### 注入固定响应

使用以下 InjectionConfig 指定具有固定响应负载的成功响应。

```python
InjectionConfig(
    injected_response={"status": "ok", "order_id": "ORD-9999"}
)
```

### 带参数匹配的条件注入

仅当传递特定参数时才使用 `match_args` 进行注入。

```python
InjectionConfig(
    match_args={"item_id": "ITEM-404"},
    injected_error=InjectedError(
        injected_http_error_code=404,
        error_message="Item not found.",
    ),
)
```

此处，仅当使用 
`item_id="ITEM-404"` 调用该工具时才会注入错误。所有其他调用都会传递到下一个注入配置
或模拟策略。

### 概率注入

将 `injection_probability` 设置为 `0.0` 和 `1.0` 之间的值以模拟片状 
 行为。对于可重复的测试运行，请使用 `random_seed` 固定随机结果。

```python
InjectionConfig(
    injection_probability=0.3,
    random_seed=42,
    injected_error=InjectedError(
        injected_http_error_code=500,
        error_message="Internal server error.",
    ),
)
```

### 注入延迟

使用 `injected_latency_seconds` 模拟缓慢的后端响应，对于 
 测试超时处理或降级条件下的用户体验非常有用。

```python
InjectionConfig(
    injected_latency_seconds=5.0,
    injected_response={"result": "slow but successful"},
)
```

### 组合多个注入配置

按顺序检查单个工具上的多个注入配置。您可以
组合它们来测试多个场景：

```python
ToolSimulationConfig(
    tool_name="get_inventory",
    injection_configs=[
        # Always fail for a specific out-of-stock item
        InjectionConfig(
            match_args={"sku": "OOS-001"},
            injected_response={"quantity": 0, "available": False},
        ),
        # Randomly fail 20% of the time for all other items
        InjectionConfig(
            injection_probability=0.2,
            random_seed=7,
            injected_error=InjectedError(
                injected_http_error_code=503,
                error_message="Inventory service unavailable.",
            ),
        ),
    ],
)
```

## 模拟策略模式

当您希望模拟器自动生成合理的响应时 - 
而不是返回手工制作的值 - 请使用 `MOCK_STRATEGY_TOOL_SPEC`。

模拟器使用 LLM 来：

1.  分析 agent 有权访问的所有工具的架构，并识别
它们之间的 * 有状态依赖关系 *（例如，`create_order` 工具生成 
 和 `get_order` 消耗的 `order_id`）。
2.  跟踪会话期间创建的 ID 和资源的 ** 状态存储 **。
3.  生成与工具的架构和内容一致的响应
当前状态 — 如果使用工具请求从未创建的 
 资源，则返回 404 类型的错误。

```python
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    MockStrategy,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="create_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
        ToolSimulationConfig(
            tool_name="get_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
        ToolSimulationConfig(
            tool_name="cancel_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ]
)
```

通过此配置，模拟器将在模拟
`create_order`时自动生成`order_id`，并在随后调用`get_order`或`cancel_order`时使用它返回一致的结果（或
未找到错误）。

### 提供环境数据

通过 `environment_data` 传递特定于域的上下文，以使模拟响应 
 更加真实。这可以是一个 JSON 字符串，表示 
 数据库的快照或 LLM 在生成响应时应使用的任何结构化上下文。

```python
import json

db_snapshot = {
    "products": [
        {"id": "P-001", "name": "Wireless Headphones", "price": 79.99, "stock": 12},
        {"id": "P-002", "name": "USB-C Hub", "price": 34.99, "stock": 0},
    ],
    "warehouse_location": "US-WEST-2",
}

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ],
    environment_data=json.dumps(db_snapshot),
)
```

LLM 将使用此数据返回与您的域匹配的产品名称、价格和库存水平 
，而不是生成任意占位符值。

### 提供追踪数据

将 agent 中生成的跟踪信息通过 `tracing` 进行模拟，以使模拟 
 响应更加真实。

```python
import json

agent_traces = [
    {
        "invocation_id": "inv-001",
        "user_content": {"role": "user", "parts": [{"text": "Search for high-end headphones"}]},
        "intermediate_data": {
            "tool_uses": [
                {
                    "name": "search_products",
                    "args": {"query": "high-end headphones"},
                    "response": {"products": [{"id": "P-123", "name": "Premium Wireless ANC Headphones"}]}
                }
            ]
        }
    }
]

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ],
    tracing=json.dumps(agent_traces),
)
```

LLM 将使用此数据返回与您的域匹配的产品名称、价格和库存水平 
，而不是生成任意占位符值。

## 混合注入和模拟策略

注入配置和模拟策略可以组合在同一个工具上。
注入总是首先检查；仅当没有
注入适用时，模拟策略才会触发。

```python
ToolSimulationConfig(
    tool_name="send_notification",
    injection_configs=[
        # Always fail for a known-bad recipient
        InjectionConfig(
            match_args={"recipient_id": "INVALID"},
            injected_error=InjectedError(
                injected_http_error_code=400,
                error_message="Invalid recipient.",
            ),
        ),
    ],
    # For all other recipients, generate a plausible success response
    mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
)
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Environment simulation for evaluations

<div class="language-support-tag">
    <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python v1.24.0</span>
</div>

When evaluating agents that rely on external dependencies — such as APIs,
databases, or third-party services — running those tools live during testing can
be slow, costly, or unreliable. The **Environment Simulator** lets you safely
intercept these tool calls during agent execution and replace them with
controlled, deterministic responses, without modifying the agent itself. This
approach can fill a critical gap in the agent improvement loop, allowing you to
create hermetic, offline test runs that isolate your agent logic for reliable
scoring.

Overall, this feature lets you:

*   Test how an agent handles API errors or edge-case responses.
*   Run evaluations offline, without access to live backends.
*   Generate realistic mock responses automatically using an LLM.
*   Produce reproducible test runs by seeding probabilistic injections.

The Environment Simulation integrates with ADK's tool execution pipeline via the
[`before_tool_callback`](<https://google.github.io/adk-docs/callbacks/types-of-callbacks/#tool-execution-callbacks>)
hook or the [plugin system](<https://google.github.io/adk-docs/plugins/>), so no
changes to your agent code are required.

```
The Environment Simulation is an experimental feature. Its API may change in future
releases.
```

## How it works

While [User Simulation](<https://google.github.io/adk-docs/evaluate/user-sim/>)
drives the conversation forward, Environment Simulation provides the stable
backend. At a high level, the Environment Simulator sits between your agent and
its tools. When the agent calls a tool, the simulator intercepts the call and
decides whether to return a synthetic response — either a predefined injection
or an LLM-generated mock — or to let the real tool execute.

The decision logic follows this order for each configured tool:

1.  **Injection configs** are checked first, in order. If a matching injection
    is found (based on argument matching and probability), its error or response
    is returned immediately.
2.  **Mock strategy** is used as a fallback if no injection config applies. The
    simulator calls an LLM to generate a realistic response based on the tool's
    schema and any stateful context.
3.  **No-op** is returned (`None`) if the tool is not in the simulator config,
    allowing the real tool to execute normally.

## Integration

The `EnvironmentSimulationFactory` class provides two integration points:

*   `create_callback()` — Returns an async callable suitable for use as a
    `before_tool_callback` on any `LlmAgent`.
*   `create_plugin()` — Returns an `EnvironmentSimulationPlugin` instance that
    integrates with the ADK plugin system.

### Using as a callback

The following example shows how to create an environment simulation as one of the adk agent callbacks.


```python
from google.adk.agents import LlmAgent
from google.adk.tools.environment_simulation import EnvironmentSimulationFactory
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    InjectedError,
    InjectionConfig,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="get_user_profile",
            injection_configs=[
                InjectionConfig(
                    injected_error=InjectedError(
                        injected_http_error_code=503,
                        error_message="Service temporarily unavailable.",
                    )
                )
            ],
        )
    ]
)

agent = LlmAgent(
    name="my_agent",
    model="gemini-flash-latest",
    tools=[get_user_profile],
    before_tool_callback=EnvironmentSimulationFactory.create_callback(config),
)
```

### Using as a plugin

The following example shows how to create environment simulation as an ADK agent plugin.

```python
from google.adk.apps import App
from google.adk.tools.environment_simulation import EnvironmentSimulationFactory
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    MockStrategy,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        )
    ]
)

app = App(
    name="my_app",
    root_agent=my_agent,
    plugins=[EnvironmentSimulationFactory.create_plugin(config)],
)
```

## Configuration reference

You can configure the Environment Simulator with a set of dataclasses. The
following sections provide a detailed reference for each configuration object.

### `EnvironmentSimulationConfig`

The top-level configuration object.

Field                            | Type                         | Default              | Description
:------------------------------- | :--------------------------- | :------------------- | :----------
`tool_simulation_configs`        | `List[ToolSimulationConfig]` | required             | One entry per tool to simulate. Must not be empty, and tool names must be unique.
`simulation_model`               | `str`                        | `"gemini-flash-latest"` | The LLM used for tool connection analysis and mock response generation.
`simulation_model_configuration` | `GenerateContentConfig`      | thinking enabled     | LLM generation config for internal simulator calls.
`environment_data`               | `str \| None`                | `None`               | Optional environment context (e.g., a JSON database snapshot) passed to mock strategies to generate more realistic responses.
`tracing`                        | `str \| None`                | `None`               | Tracing data (e.g., a prior agent run trace in JSON string format) to provide historical context.

### `ToolSimulationConfig`

Defines how a single named tool should be simulated.

Field                | Type                    | Default                     | Description
:------------------- | :---------------------- | :-------------------------- | :----------
`tool_name`          | `str`                   | required                    | Must match the tool's registered name exactly.
`injection_configs`  | `List[InjectionConfig]` | `[]`                        | Zero or more injection configs, checked in order before the mock strategy.
`mock_strategy_type` | `MockStrategy`          | `MOCK_STRATEGY_UNSPECIFIED` | Fallback strategy when no injection is triggered.

### `InjectionConfig`

Controls a single synthetic response that can be injected into a tool call.
Exactly one of `injected_error` or `injected_response` must be set.

Field                      | Type                     | Default | Description
:------------------------- | :----------------------- | :------ | :----------
`injected_error`           | `InjectedError \| None`  | `None`  | Error to return (mutually exclusive with `injected_response`).
`injected_response`        | `Dict[str, Any] \| None` | `None`  | Fixed response dict to return (mutually exclusive with `injected_error`).
`injection_probability`    | `float`                  | `1.0`   | Probability `[0.0, 1.0]` that this injection fires.
`match_args`               | `Dict[str, Any] \| None` | `None`  | If set, the injection only fires when the tool's arguments contain all key-value pairs in `match_args`.
`injected_latency_seconds` | `float`                  | `0.0`   | Artificial delay (≤ 120 s) added before returning the injection result.
`random_seed`              | `int \| None`            | `None`  | Seed for the probability check, enabling deterministic injection behavior.

### `InjectedError`

Defines an HTTP-style error response.

| Field                      | Type  | Description                             |
| :------------------------- | :---- | :-------------------------------------- |
| `injected_http_error_code` | `int` | HTTP status code to surface as          |
:                            :       : `"error_code"` in the tool response.    :
| `error_message`            | `str` | Human-readable message surfaced as      |
:                            :       : `"error_message"` in the tool response. :

### `MockStrategy`

Enum controlling how the simulator generates responses when no injection fires.

| Value                     | Description                                     |
| :------------------------ | :---------------------------------------------- |
| `MOCK_STRATEGY_TOOL_SPEC` | Uses the tool's schema and stateful context to  |
:                           : prompt an LLM to generate a realistic response. :
| `MOCK_STRATEGY_TRACING`   | *(Deprecated)* Please use                       |
:                           : `MOCK_STRATEGY_TOOL_SPEC` with tracing input.   :

## Injection mode

Use injection configs to test specific failure or edge-case scenarios.
Injections are evaluated in list order; the first one whose `match_args`
criteria are met (and whose probability check passes) is applied.

### Injecting errors

The following example shows how to inject errors with specific error code and error message to the agent.

```python
from google.adk.tools.environment_simulation.environment_simulation_config import (
    InjectedError,
    InjectionConfig,
    ToolSimulationConfig,
)

ToolSimulationConfig(
    tool_name="charge_payment",
    injection_configs=[
        InjectionConfig(
            injected_error=InjectedError(
                injected_http_error_code=402,
                error_message="Payment declined.",
            )
        )
    ],
)
```

The agent will receive `{"error_code": 402, "error_message": "Payment
declined."}` instead of a real tool result, allowing you to evaluate how the
agent handles payment failures.

### Injecting fixed responses

Use the following InjectionConfig to specify a success response with fixed response payload. 

```python
InjectionConfig(
    injected_response={"status": "ok", "order_id": "ORD-9999"}
)
```

### Conditional injection with argument matching

Use `match_args` to inject only when specific arguments are passed.

```python
InjectionConfig(
    match_args={"item_id": "ITEM-404"},
    injected_error=InjectedError(
        injected_http_error_code=404,
        error_message="Item not found.",
    ),
)
```

Here, the error is injected only when the tool is called with
`item_id="ITEM-404"`. All other calls pass through to the next injection config
or to the mock strategy.

### Probabilistic injection

Set `injection_probability` to a value between `0.0` and `1.0` to simulate flaky
behavior. For reproducible test runs, pin the random outcome with `random_seed`.

```python
InjectionConfig(
    injection_probability=0.3,
    random_seed=42,
    injected_error=InjectedError(
        injected_http_error_code=500,
        error_message="Internal server error.",
    ),
)
```

### Injecting latency

Use `injected_latency_seconds` to simulate slow backend responses, useful for
testing timeout handling or user experience under degraded conditions.

```python
InjectionConfig(
    injected_latency_seconds=5.0,
    injected_response={"result": "slow but successful"},
)
```

### Combining multiple injection configs

Multiple injection configs on a single tool are checked in order. You can
combine them to test multiple scenarios:

```python
ToolSimulationConfig(
    tool_name="get_inventory",
    injection_configs=[
        # Always fail for a specific out-of-stock item
        InjectionConfig(
            match_args={"sku": "OOS-001"},
            injected_response={"quantity": 0, "available": False},
        ),
        # Randomly fail 20% of the time for all other items
        InjectionConfig(
            injection_probability=0.2,
            random_seed=7,
            injected_error=InjectedError(
                injected_http_error_code=503,
                error_message="Inventory service unavailable.",
            ),
        ),
    ],
)
```

## Mock strategy mode

When you want the simulator to generate plausible responses automatically —
rather than returning hand-crafted values — use `MOCK_STRATEGY_TOOL_SPEC`.

The simulator uses an LLM to:

1.  Analyze the schemas of all tools the agent has access to, and identify
    *stateful dependencies* between them (e.g., a `create_order` tool produces
    an `order_id` that `get_order` consumes).
2.  Track a **state store** of IDs and resources created during the session.
3.  Generate a response that is consistent with the tool's schema and the
    current state — returning a 404-style error if a consuming tool requests a
    resource that was never created.

```python
from google.adk.tools.environment_simulation.environment_simulation_config import (
    EnvironmentSimulationConfig,
    MockStrategy,
    ToolSimulationConfig,
)

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="create_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
        ToolSimulationConfig(
            tool_name="get_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
        ToolSimulationConfig(
            tool_name="cancel_order",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ]
)
```

With this config, the simulator will automatically generate an `order_id` when
`create_order` is mocked, and use it to return consistent results (or a
not-found error) when `get_order` or `cancel_order` are subsequently called.

### Providing environment data

Pass domain-specific context through `environment_data` to make mock responses
more realistic. This can be a JSON string representing a snapshot of your
database or any structured context the LLM should use when generating responses.

```python
import json

db_snapshot = {
    "products": [
        {"id": "P-001", "name": "Wireless Headphones", "price": 79.99, "stock": 12},
        {"id": "P-002", "name": "USB-C Hub", "price": 34.99, "stock": 0},
    ],
    "warehouse_location": "US-WEST-2",
}

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ],
    environment_data=json.dumps(db_snapshot),
)
```

The LLM will use this data to return product names, prices, and stock levels
that match your domain, rather than generating arbitrary placeholder values.

### Providing tracing data

Feed traces generated in the agent to be mocked through `tracing` to make mock
responses more realistic.

```python
import json

agent_traces = [
    {
        "invocation_id": "inv-001",
        "user_content": {"role": "user", "parts": [{"text": "Search for high-end headphones"}]},
        "intermediate_data": {
            "tool_uses": [
                {
                    "name": "search_products",
                    "args": {"query": "high-end headphones"},
                    "response": {"products": [{"id": "P-123", "name": "Premium Wireless ANC Headphones"}]}
                }
            ]
        }
    }
]

config = EnvironmentSimulationConfig(
    tool_simulation_configs=[
        ToolSimulationConfig(
            tool_name="search_products",
            mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
        ),
    ],
    tracing=json.dumps(agent_traces),
)
```

The LLM will use this data to return product names, prices, and stock levels
that match your domain, rather than generating arbitrary placeholder values.

## Mixing injections and mock strategy

Injection configs and a mock strategy can be combined on the same tool.
Injections are always checked first; the mock strategy fires only when no
injection applies.

```python
ToolSimulationConfig(
    tool_name="send_notification",
    injection_configs=[
        # Always fail for a known-bad recipient
        InjectionConfig(
            match_args={"recipient_id": "INVALID"},
            injected_error=InjectedError(
                injected_http_error_code=400,
                error_message="Invalid recipient.",
            ),
        ),
    ],
    # For all other recipients, generate a plausible success response
    mock_strategy_type=MockStrategy.MOCK_STRATEGY_TOOL_SPEC,
)
```


</details>
