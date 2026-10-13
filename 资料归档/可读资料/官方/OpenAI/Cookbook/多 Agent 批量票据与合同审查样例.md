# 多 Agent 批量票据与合同审查样例

说明共享工作区、独立挂载、专门角色分派、技能使用、报告验证和人工批准；属于批量业务处理工程样例。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-26](<https://github.com/openai/openai-cookbook/commit/6dc6324fb9ed780b32b787f23fad336e9f1eff15>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/多 Agent 批量票据与合同审查样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 批量发票和合同审查

将发票和合同的文件夹与可重复使用的应付账款保单技能一起安装。 `gpt-5.6-luna` agent 将每个文档委托给专业子代理，应用发现的技能，编写单独的报告和综合摘要，并将每个批准留给一个人。

## 为什么使用AgentsAPI？

批量审核需要在聊天中多个答案。 Agents API 在共享工作区中协调 
 专家 agents 并保留其命令活动。
安装的文件将输入与输出分开，并且发现的技能为 
 每个审阅者提供相同的策略。您的应用程序验证报告并
决定接下来会发生什么。

![文件审查工作流程：装载文件和审查技能，委托给专家子代理，并编写单独的报告和综合摘要。](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/apps/document_review/assets/workflow.svg>)

## 你需要什么

- Python 3.14+ 和 `uv`。
- 沙箱：自托管 Docker 或 [ 第三方提供商 ](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#sandbox-providers>)。
- 一个 OpenAI API 密钥和一个单独的受限执行者密钥。

## 1。设置工作区

从存储库根目录：

```bash
cp examples/agents_api/apps/document_review/.env.example examples/agents_api/apps/document_review/.env
docker build -t agent-api-sandbox:latest examples/agents_api/sandboxes/docker/application_managed
```

在`examples/agents_api/apps/document_review/.env`中设置`OPENAI_API_KEY`和`OPENAI_EXECUTOR_API_KEY`。使用具有相同所有者、组织和项目的密钥。只有执行者密钥才能进入沙箱。它需要允许沙箱出站网络的 `api.agents.environments.connect` 和 IP 限制。应用程序自动加载该文件。

要创建具有所需权限的执行程序密钥，请打开 [Agents > 环境 > 密钥 ](<https://platform.openai.com/agents?tab=environments&environment_view=keys>) 并选择 ** 创建 **。

您可以将本地 Docker 替换为任何兼容的 [ 沙箱提供程序 ](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#sandbox-providers>)。

## 2。审核文档批次

```bash
uv run examples/agents_api/apps/document_review/main.py \
  --input examples/agents_api/apps/document_review/sample_documents \
  --output ./review-output
```

输入文件夹包含一张多收 $900 的发票和一份具有自动续订、单方面涨价、无限责任和无限制客户数据共享的合同。专业子代理将安装的策略应用于每个文档并写入：

```text
review-output/
  contract.json
  invoice.json
  summary.json
  review-activity.json
```

源文件夹和策略技能以只读方式挂载；报告将写入 `/workspace/output`，并在沙箱退出后在主机上保持可用。使用 Docker Desktop，将输入和输出目录保留在您的主目录下；系统临时目录可能无法共享。

该命令会在审查过程中记录会话、沙箱、安装的目录、专家子代理和生成的工件。

发票报告包括提取的行项目和计算。应用程序
在返回报告之前检查算术。人们仍必须验证
提取的金额是否与源文档匹配并查看建议。

每次运行和唯一文档主干使用空输出文件夹，例如 `invoice-104.txt` 和 `contract-208.txt`。名称 `summary` 和 `review-activity` 被保留。

## 遵照执行

以下摘录遵循 [agent.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/agent.py>) 和 [sandbox.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/sandbox.py>)。
运行上面的命令以进行完整的验证、活动导出和清理。

### 1。创建多 agent 审核会话

协调员将文件委托给专家。所有审阅者通过 `capability_directories` 发现 
 相同的已安装策略。

```python
from openai import AsyncOpenAI

client = AsyncOpenAI()
instructions = """\
Coordinate a batch review of invoices and contracts in /workspace/input.
Delegate documents to specialist subagents before reviewing them.
Each specialist must apply $expense-review-policy and write its JSON report
to /workspace/output/<document-stem>.json.
Wait for all reviewers, then write /workspace/output/summary.json.
Never approve a payment or sign a contract.
"""
session = await client.beta.agents.sessions.create(
    agent={
        "model": "gpt-5.6-luna",
        "instructions": instructions,
        "reasoning": {"effort": "high"},
        "multi_agent": {"enabled": True, "max_concurrent_subagents": 4},
    },
    environment={
        "type": "self_hosted",
        "workspace_directory": "/workspace",
        "capability_directories": ["/workspace/skills"],
    },
)
```

### 2。分别安装输入、策略和输出

`start_executor` 安装输入文件夹，`skills/` 只读。只有输出
文件夹是可写的。清理后输出目录保留在主机上。

```python
import asyncio
from pathlib import Path
from examples.agents_api.apps.document_review.sandbox import start_executor

input_directory = Path("examples/agents_api/apps/document_review/sample_documents")
output_directory = Path("review-output")
output_directory.mkdir(exist_ok=True)

environment = session.environment
assert environment.type == "self_hosted"
container = await asyncio.to_thread(
    start_executor,
    input_directory,
    output_directory,
    environment.id,
    environment.remote_url,
)
```

帮助程序使用返回的连接值运行 `codex exec-server`，并且 
 将 `OPENAI_EXECUTOR_API_KEY` 注入为 `CODEX_API_KEY`。替换已安装的策略
不需要重建映像。

### 3。委派批次并跟踪进度

发送审核任务并观察专家创建和输出事件：

```python
prompt = """\
Review every document in /workspace/input using specialist subagents.
Each specialist must apply $expense-review-policy and include its policy_id
and decision in the report. Wait for every review, write the consolidated
summary, and explain the findings to the human approver.
"""
async with client.beta.agents.sessions.stream(session.id, input=prompt) as events:
    async for event in events:
        if event.type == "agent.session.subagent.created":
            print(f"Specialist started: {event.subagent.id}")
        elif event.type == "agent.session.turn.output_text.delta":
            print(event.delta, end="", flush=True)
        elif event.type in {
            "agent.session.failed", "agent.session.turn.failed", "error",
            "agent.session.turn.cancelled",
        }:
            raise RuntimeError(f"Review did not finish: {event.to_dict()}")
```

该应用程序检查是否已创建专家以及每个源文档是否都有
报告。启用多 agent 执行本身并不能证明工作已被 
 委派。

### 4。在提交报告之前验证它们

加载生成的 JSON 并应用应用程序的验证：

```python
import json
from examples.agents_api.apps.document_review.agent import validate_review

reviews = []
for document in sorted(input_directory.iterdir()):
    if not document.is_file():
        continue
    report = json.loads((output_directory / f"{document.stem}.json").read_text())
    validate_review(report, document.name)
    reviews.append({"report": report, "status": "awaiting_approval"})

summary = json.loads((output_directory / "summary.json").read_text())
```

对于发票，验证会根据提取的行项目和
运费重新计算总计。无法确定提取内容与原始文档相符；
仍需要审核。随附的发票应标记 $900 多收费、
 缺少采购订单以及更改的银行详细信息。

### 5。保留工件并释放计算

在删除会话之前导出保留的命令活动，如下所示。
然后尝试两个清理操作：

```python
try:
    await client.beta.agents.sessions.delete(session.id)
finally:
    await asyncio.to_thread(container.remove, force=True)
```

清理后关闭OpenAI客户端。报告保留在`review-output`中； 
人员在做出任何付款或合同决定之前对其进行审查。

## 检查保留的命令活动

在删除会话之前，应用程序会将保留的命令从协调员 
 和每个专家导出到 `review-activity.json`。 `null` 子代理 ID 标识 
 协调器。专业命令来自他们自己的项目历史记录：

```python
async for subagent in client.beta.agents.sessions.subagents.list(session.id):
    async for item in client.beta.agents.sessions.subagents.items.list(
        subagent.id, session_id=session.id
    ):
        if item.type == "command_execution":
            print(subagent.id, item.command)
```

这些记录来自保留的 API 历史记录，而不是模型编写的审阅者姓名。
它们不是完整的安全审核。像报告一样保护此文件：
命令可以包含文档内容。

## 政策技巧如何发挥作用

所包含的保单适用于：

```text
skills/expense-review-policy/SKILL.md
```

它安装在 `/workspace/skills/expense-review-policy/SKILL.md` 并通过会话的能力根发现：

```python
environment = {
    "type": "self_hosted",
    "workspace_directory": "/workspace",
    "capability_directories": ["/workspace/skills"],
}
```

每个专家都会应用 `$expense-review-policy` 并将其 `policy_id` 和 `decision` 包含在生成的报告中。将 `SKILL.md` 替换为您自己的审核策略，无需重建沙箱映像。

## 让它成为你的

将包含的技能替换为您团队自己的应付账款或合同审核策略，并在部署应用程序时使用您首选的沙箱提供商。在您的申请中保留批准：agent 可以建议做出决定，但它绝不能自行付款或接受合同。

## 文件

- [main.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/main.py>)：命令行参数和批处理摘要。
- [agent.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/agent.py>)：专家评审、报告验证和活动导出。
- [sandbox.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/sandbox.py>)：文档、神器和技能坐骑。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Bulk invoice and contract review

Mount a folder of invoices and contracts alongside a reusable accounts-payable policy skill. A `gpt-5.6-luna` agent delegates each document to a specialist subagent, applies the discovered skill, writes individual reports plus a consolidated summary, and leaves every approval to a person.

## Why use the Agents API?

A batch review needs more than one answer in a chat. The Agents API coordinates
specialist agents against a shared workspace and retains their command activity.
Mounted files keep inputs separate from outputs, and a discovered skill gives
each reviewer the same policy. Your application validates the reports and
decides what happens next.

![Document review workflow: mount documents and a review skill, delegate to specialist subagents, and write individual reports and a consolidated summary.](<https://raw.githubusercontent.com/openai/openai-cookbook/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/agents_api/apps/document_review/assets/workflow.svg>)

## What you need

- Python 3.14+ and `uv`.
- A sandbox: self-hosted Docker or a [third-party provider](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#sandbox-providers>).
- An OpenAI API key and a separate restricted executor key.

## 1. Set up the workspace

From the repository root:

```bash
cp examples/agents_api/apps/document_review/.env.example examples/agents_api/apps/document_review/.env
docker build -t agent-api-sandbox:latest examples/agents_api/sandboxes/docker/application_managed
```

Set `OPENAI_API_KEY` and `OPENAI_EXECUTOR_API_KEY` in `examples/agents_api/apps/document_review/.env`. Use keys with the same owner, organization, and project. Only the executor key enters the sandbox. It needs `api.agents.environments.connect` and IP restrictions that allow the sandbox's outbound network. The application loads this file automatically.

To create an executor key with the required permission, open [Agents > Environments > Keys](<https://platform.openai.com/agents?tab=environments&environment_view=keys>) and select **Create**.

You can replace local Docker with any compatible [sandbox provider](<https://developers.openai.com/api/docs/guides/agents-api/environments/self-hosted#sandbox-providers>).

## 2. Review the document batch

```bash
uv run examples/agents_api/apps/document_review/main.py \
  --input examples/agents_api/apps/document_review/sample_documents \
  --output ./review-output
```

The input folder contains an invoice with a $900 overcharge and a contract with automatic renewal, unilateral price increases, unlimited liability, and unrestricted customer-data sharing. Specialist subagents apply the mounted policy to each document and write:

```text
review-output/
  contract.json
  invoice.json
  summary.json
  review-activity.json
```

The source folder and policy skill are mounted read-only; reports are written to `/workspace/output` and remain available on the host after the sandbox exits. With Docker Desktop, keep input and output directories under your home directory; system temporary directories may not be shared.

The command logs the session, sandbox, mounted directories, specialist subagents, and generated artifacts as the review progresses.

Invoice reports include extracted line items and a calculation. The application
checks the arithmetic before returning a report. A person must still verify that
the extracted amounts match the source document and review the recommendation.

Use an empty output folder for each run and unique document stems, such as `invoice-104.txt` and `contract-208.txt`. The names `summary` and `review-activity` are reserved.

## Follow the implementation

The excerpts below follow [agent.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/agent.py>) and [sandbox.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/sandbox.py>).
Run the command above for the complete validation, activity export, and cleanup.

### 1. Create a multi-agent review session

The coordinator delegates documents to specialists. All reviewers discover the
same mounted policy through `capability_directories`.

```python
from openai import AsyncOpenAI

client = AsyncOpenAI()
instructions = """\
Coordinate a batch review of invoices and contracts in /workspace/input.
Delegate documents to specialist subagents before reviewing them.
Each specialist must apply $expense-review-policy and write its JSON report
to /workspace/output/<document-stem>.json.
Wait for all reviewers, then write /workspace/output/summary.json.
Never approve a payment or sign a contract.
"""
session = await client.beta.agents.sessions.create(
    agent={
        "model": "gpt-5.6-luna",
        "instructions": instructions,
        "reasoning": {"effort": "high"},
        "multi_agent": {"enabled": True, "max_concurrent_subagents": 4},
    },
    environment={
        "type": "self_hosted",
        "workspace_directory": "/workspace",
        "capability_directories": ["/workspace/skills"],
    },
)
```

### 2. Mount inputs, policy, and outputs separately

`start_executor` mounts the input folder and `skills/` read-only. Only the output
folder is writable. The output directory stays on the host after cleanup.

```python
import asyncio
from pathlib import Path
from examples.agents_api.apps.document_review.sandbox import start_executor

input_directory = Path("examples/agents_api/apps/document_review/sample_documents")
output_directory = Path("review-output")
output_directory.mkdir(exist_ok=True)

environment = session.environment
assert environment.type == "self_hosted"
container = await asyncio.to_thread(
    start_executor,
    input_directory,
    output_directory,
    environment.id,
    environment.remote_url,
)
```

The helper runs `codex exec-server` with the returned connection values and
injects `OPENAI_EXECUTOR_API_KEY` as `CODEX_API_KEY`. Replacing the mounted policy
does not require rebuilding the image.

### 3. Delegate the batch and follow progress

Send the review task and watch for specialist creation and output events:

```python
prompt = """\
Review every document in /workspace/input using specialist subagents.
Each specialist must apply $expense-review-policy and include its policy_id
and decision in the report. Wait for every review, write the consolidated
summary, and explain the findings to the human approver.
"""
async with client.beta.agents.sessions.stream(session.id, input=prompt) as events:
    async for event in events:
        if event.type == "agent.session.subagent.created":
            print(f"Specialist started: {event.subagent.id}")
        elif event.type == "agent.session.turn.output_text.delta":
            print(event.delta, end="", flush=True)
        elif event.type in {
            "agent.session.failed", "agent.session.turn.failed", "error",
            "agent.session.turn.cancelled",
        }:
            raise RuntimeError(f"Review did not finish: {event.to_dict()}")
```

The app checks that specialists were created and that every source document has
a report. Enabling multi-agent execution is not, by itself, proof that work was
delegated.

### 4. Validate the reports before presenting them

Load the generated JSON and apply the application's validation:

```python
import json
from examples.agents_api.apps.document_review.agent import validate_review

reviews = []
for document in sorted(input_directory.iterdir()):
    if not document.is_file():
        continue
    report = json.loads((output_directory / f"{document.stem}.json").read_text())
    validate_review(report, document.name)
    reviews.append({"report": report, "status": "awaiting_approval"})

summary = json.loads((output_directory / "summary.json").read_text())
```

For invoices, validation recomputes the total from extracted line items and
shipping. It cannot establish that extraction matched the original document;
that still needs review. The included invoice should flag a $900 overcharge,
a missing purchase order, and changed bank details.

### 5. Keep the artifacts and release compute

Export retained command activity before deleting the session, as shown below.
Then attempt both cleanup operations:

```python
try:
    await client.beta.agents.sessions.delete(session.id)
finally:
    await asyncio.to_thread(container.remove, force=True)
```

Close the OpenAI client after cleanup. Reports remain in `review-output`; a
person reviews them before any payment or contract decision.

## Inspect retained command activity

Before deleting the session, the app exports retained commands from the coordinator
and each specialist to `review-activity.json`. A `null` subagent ID identifies the
coordinator. Specialist commands come from their own item histories:

```python
async for subagent in client.beta.agents.sessions.subagents.list(session.id):
    async for item in client.beta.agents.sessions.subagents.items.list(
        subagent.id, session_id=session.id
    ):
        if item.type == "command_execution":
            print(subagent.id, item.command)
```

These records come from retained API history, not model-written reviewer names.
They are not a complete security audit. Protect this file like the reports:
commands can contain document content.

## How the policy skill works

The included policy lives at:

```text
skills/expense-review-policy/SKILL.md
```

It is mounted at `/workspace/skills/expense-review-policy/SKILL.md` and discovered through the session's capability root:

```python
environment = {
    "type": "self_hosted",
    "workspace_directory": "/workspace",
    "capability_directories": ["/workspace/skills"],
}
```

Each specialist applies `$expense-review-policy` and includes its `policy_id` and `decision` in the generated report. Replace `SKILL.md` with your own review policy without rebuilding the sandbox image.

## Make it yours

Replace the included skill with your team's own accounts-payable or contract-review policy, and use your preferred sandbox provider when you deploy the application. Keep approval in your application: the agent can recommend a decision, but it should never make payments or accept contracts on its own.

## Files

- [main.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/main.py>): Command-line arguments and the batch summary.
- [agent.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/agent.py>): Specialist reviews, report validation, and activity export.
- [sandbox.py](<https://github.com/openai/openai-cookbook/blob/main/examples/agents_api/apps/document_review/sandbox.py>): Document, artifact, and skill mounts.


</details>
