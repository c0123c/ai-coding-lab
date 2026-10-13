# Amazon Bedrock AgentCore 评测接入样例

针对固定测试场景说明非生产运行时、权限、评测调用、结果阈值与排查；属于云端评测集成参考。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2026-09-12](<https://github.com/openai/openai-cookbook/commit/9aad95f0aa4f8e12991ef9b9201df28747860bfc>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/examples/partners/AWS/chatgpt_agents_sdk_aws_agentcore_cookbook/docs/aws-evaluation.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/Amazon Bedrock AgentCore 评测接入样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 可选的亚马逊基岩 AgentCore 评估

使用此通道针对一个专用的
非生产 Amazon Bedrock AgentCore 运行时评估确定性飞行案例。它是可选的，进行实时 AWS
 调用以创建测试调用，并随后对其完成的跨度进行评分。
它与生产流量和离线 Promptfoo 合约 
 套件分开。

AWS 记录了 OpenAI Agents 集成重建了提示、
 响应，并且来自运行时 
[OpenTelemetry 的工具活动跨越了 ](<https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/supported-frameworks-openai-agents.html>)。
 菜谱的正常跟踪默认情况下会编辑该内容，因此此通道 
 需要一个 ** 专用的非生产评估运行时 **，由其 
 所有者使用 `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true` 配置。不要
在普通生产运行时启用内容捕获。运行时所有者必须
批准测试数据、访问、保留和清理控制，然后将
其ARN交给评估者。

|命令|目的|实时凭证|产生费用|
| --- | --- | --- | --- |
| `npm run eval:run` |使用 Promptfoo 的 echo 提供程序检查所有响应合约装置|否|否|
| `npm run eval:agent:validate` |验证实际的-agent Promptfoo 配置|否|否|
| `RUN_PROMPTFOO_AGENT_EVALUATION=1 npm run eval:agent` |运行并得分三个新的本地 Agents SDK 输出|是的|是的|
| `npm run eval:aws:validate` |验证 AWS- 标记的规范夹具元数据|否|否|
| `AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 RUN_AWS_EVALUATION=1 npm run eval:aws` |调用配置的评估运行时，检索其跨度，并运行 AgentCore 内置评估器|是的|是的|

live 命令永远不会在 CI 中运行。此存储库不包含已提交的
实时评估证明。运行者使用AgentCore的按需评估
API并将其编辑结果保存在本地；它不会在 AWS 控制台中创建持久的 
 评估运行。

## 直播跑者评价如何

所有三个评估通道都读取
`runtime-agent/evals/fixtures/flight-status-results.jsonl`，但它们不会
相互替换或调用：

- 无需凭证的 Promptfoo 套件会检查每个适用的签入情况
通过 echo 提供者的合同夹具。
- 受保护的实际-agent Promptfoo 套件调用本地 Agents SDK
三个标记的规范案例的工作流程，并根据运行时合同和确切的预期输出检查每个新响应
。
- AWS 运行程序仅使用显式标记的有效规范案例
与 `aws_evaluation` 元数据。否定案例和仅合同案例不会发送
至AWS。

对于每个标记的案例，跑步者：

1. 使用新的保留会话调用确切的 `AGENTCORE_RUNTIME_AGENT_ARN`
ID 并生成 W3C 跟踪上下文。
2. 等待该会话的跨度到达配置的 CloudWatch 日志组。
3. 调用 `EvaluationClient.run`：
   - `Builtin.Correctness`
   - `Builtin.GoalSuccessRate`
   - `Builtin.TrajectoryExactOrderMatch`
4. 提供案例的预期响应、断言和预期工具顺序
通过`ReferenceInputs`。
5. 在 `runtime-agent/evals/results/` 下写入经过编辑的本地结果。

规范装置存储本地 Promptfoo 响应。 AWS 加载器 
 首先根据当前确定性工作流程验证该响应，然后 
 为 
 运行时评估导出与 `executionMode: "deployed"` 相同的预期事实。断言和预期工具轨迹位于 
 案例的 `aws_evaluation` 标签中。这保留了一个版本化的飞行数据集，而不会
错误标记路线，同时保留独立的评估命令。

此通道不需要 OpenAI 隧道、ChatGPT 插件、CDK 或本地 
agent 进程。它确实需要一个已部署的运行时，该运行时将其 
 会话范围发送到 CloudWatch 中与 
 评估角色使用的同一账户和区域中。

## 向您的 AWS 管理员请求什么

将此文本复制到企业访问票证中并替换您
已经知道的任何值：

```text
I need to run the optional live AgentCore Evaluations workflow in the
flight-status cookbook against one dedicated non-production evaluation Runtime.

Please provide:
- The exact parent AgentCore Runtime ARN, endpoint ARN, Region, Runtime
  qualifier, and CloudWatch Runtime log-group name.
- A role I can assume from my existing AWS CLI identity.
- bedrock-agentcore:InvokeAgentRuntime on only those exact parent Runtime and
  selected endpoint ARNs.
- bedrock-agentcore:Evaluate and bedrock-agentcore:GetEvaluator for the
  approved AWS built-in evaluators.
- CloudWatch Logs query access needed to retrieve the Runtime session spans:
  logs:StartQuery for the designated Runtime log group and aws/spans, and
  logs:GetQueryResults with Resource "*"; AWS does not support resource-level
  scoping for GetQueryResults.
- bedrock:InvokeModel, bedrock:InvokeModelWithResponseStream,
  bedrock:Converse, and bedrock:ConverseStream on only the approved Amazon
  Bedrock judge model or inference profile used by Builtin.Correctness and
  Builtin.GoalSuccessRate.
- Confirmation that Runtime observability and CloudWatch Transaction Search
  are enabled in this account and Region and that the named log group receives
  the Runtime's session spans.
- Confirmation that this is a dedicated non-production evaluation Runtime with
  `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true`, and that its trace
  content, access, retention, and cleanup have been approved.

This runner uses only Builtin.Correctness, Builtin.GoalSuccessRate, and
Builtin.TrajectoryExactOrderMatch. It does not deploy or change the Runtime and
does not create custom evaluators.

I do not need CDK, S3 artifact, ECR, Runtime create/update/delete,
iam:PassRole, Lambda, or custom-evaluator creation permissions.

Please return the evaluation role ARN, the approved source-profile/login
method, the exact Runtime values, the judge model or inference-profile ID, and
the expected AWS account ID so I can verify the assumed identity.
```

如果管理员 [ 询问实施细节，请向他们发送评估运行器策略形状 ](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/partners/AWS/chatgpt_agents_sdk_aws_agentcore_cookbook/docs/aws-iam.md#optional-agentcore-evaluations-runner-role>)
。管理员拥有 AWS 策略；
请勿将策略 JSON 或 AWS 凭据粘贴到应用程序中。

## 配置 AWS CLI 配置文件

将管理员返回的角色添加到`~/.aws/config`中。替换
占位符：

```ini
[profile agentcore-evaluation]
role_arn = arn:aws:iam::<ACCOUNT_ID>:role/AgentCoreFlightEvaluation
source_profile = YOUR_EXISTING_AWS_PROFILE
region = us-west-2
```

您的组织可以使用 IAM Identity Center 或其他凭证源 
，而不是 `source_profile`。按照正常的登录说明进行操作。

在运行评估之前验证身份：

```bash
aws sts get-caller-identity --profile agentcore-evaluation
```

如果返回的帐户或角色不是访问票证中指定的帐户或角色，则停止。

## 配置菜谱

从存储库根目录创建 `.env`（如果不存在）：

```bash
test -f .env || install -m 600 .env.example .env
chmod 600 .env
```

模式 `600` 使凭证文件只能由其所有者读取和写入。

设置管理员提供的值：

```dotenv
AGENTCORE_RUNTIME_REGION=us-west-2
AGENTCORE_RUNTIME_AGENT_ARN=arn:aws:bedrock-agentcore:<REGION>:<ACCOUNT_ID>:runtime/<RUNTIME_ID>
AGENTCORE_RUNTIME_QUALIFIER=<QUALIFIER>
AGENTCORE_EVALUATION_LOG_GROUP=/aws/bedrock-agentcore/runtimes/<RUNTIME_ID>-DEFAULT
RUN_AWS_EVALUATION=0
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=0
```

使用准确的日志组名称；不要从显示名称推断它。将 
 限定符设置为运行时所有者提供的值。仅当
所有者确认运行时应使用其默认限定符时，将其保留为空。

将两个防护装置设置为 `.env` 中的 `0`。在运行时所有者确认专用评估
运行时的内容捕获和数据处理配置后，实时命令将它们设置为`1`仅用于该过程
。

可选控件及其默认值是：

```dotenv
AGENTCORE_EVALUATION_WAIT_SECONDS=180
AGENTCORE_EVALUATION_POLL_SECONDS=30
AGENTCORE_EVALUATION_ATTEMPTS=5
AGENTCORE_EVALUATION_MIN_SCORE=0.5
AGENTCORE_EVALUATION_CASE_IDS=
```

`AGENTCORE_EVALUATION_CASE_IDS` 接受以逗号分隔的案例 ID。将其
留空以运行每个标记的规范案例。选择一个案例以获得成本较低的
第一个证明：

```dotenv
AGENTCORE_EVALUATION_CASE_IDS=<CASE_ID>
```

请勿将 `AWS_PROFILE` 或 AWS 凭据放入 `.env` 中。在运行命令的 shell 
 中导出配置文件。

## 不使用 AWS 进行验证

安装锁定的依赖项，然后验证标记的案例：

```bash
cd runtime-agent
uv sync --locked --extra dev
npm ci
npm run eval:aws:validate
```

此命令不会调用运行时、查询 CloudWatch 或调用 
 计算器。在继续之前修复任何丢失或无效的 AWS 夹具元数据。
如果 `AGENTCORE_EVALUATION_CASE_IDS` 包含未知的 ID，实时命令 
 会在创建任何 AWS 客户端之前拒绝它。

## 运行实时评估

使用组织的正常 AWS 流程登录，然后运行：

```bash
export AWS_PROFILE=agentcore-evaluation
aws sts get-caller-identity
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 \
RUN_AWS_EVALUATION=1 \
npm run eval:aws
```

该命令最初会等待 180 秒，因为运行时跨度无法立即查询
。然后，它会进行最多五次评估尝试，两次尝试之间的间隔为 30
 秒，但没有找到跨度。固定的 AgentCore SDK 可能会在每次尝试中花费 
 最多 60 秒查询 CloudWatch，因此无跨度故障 
 在默认情况下加上普通评估器可能需要大约十分钟
仅当运行时所有者期望不同的摄取
计时时才调整这些设置。

如果该命令无法调用运行时、检索匹配的 
 会话范围、获取所有必需的评估器结果或满足配置的 
 最低分数，则该命令将失败。低分是评估结果，而不是IAM失败。

## 处理结果

运行程序打印摘要并将编辑后的 JSON 工件保存在 
`runtime-agent/evals/results/` 下。 Git 会忽略该目录。工件 
 包含案例和评估器结果以及经过编辑的相关值；它必须
不包含AWS凭证或原始秘密。

将工件视为环境证据：

- 仅通过您组织批准的证据位置共享它。
- 即使跑步者编辑了相关值，也要在共享之前检查它。
- 不要强制将其添加到 Git。
- 记录运行时后缀、区域、评估时间、评估者 ID 和
pass/fail结果与外部证据。

默认情况下不会提交实时工件，因为实时成功取决于 
 调用者的 AWS 帐户、角色、运行时、判断模型访问和可观察性 
 配置。

## 故障排除

### `RUN_AWS_EVALUATION=1 is required`

该防护措施可防止意外的实时呼叫。首先运行`npm run eval:aws:validate`
。仅在应创建 AWS 活动的命令上设置防护：

```bash
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 \
RUN_AWS_EVALUATION=1 \
npm run eval:aws
```

### `AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 is required`

对于正常的生产运行时，请勿绕过此防护。向运行时所有者 
 请求其部署集 
`OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true` 的专用非生产评估运行时，然后确认 
 组织已批准跟踪内容访问、保留和清理 
 控制。仅在预期的评估命令上设置确认标志。

### 运行时调用被拒绝

请管理员确认
`bedrock-agentcore:InvokeAgentRuntime` 确切的父级都允许 ARN
在 `AGENTCORE_RUNTIME_AGENT_ARN` 和确切的终点 ARN 选择者
`AGENTCORE_RUNTIME_QUALIFIER`。还要确认所承担的角色和区域。

### 评估者访问被拒绝

将拒绝的操作发送给管理员。跑步者需要
`bedrock-agentcore:Evaluate`和`bedrock-agentcore:GetEvaluator`作为其
内置。与法官相关的拒绝需要访问专门批准的 
Bedrock 模型或 `bedrock:InvokeModel`、
`bedrock:InvokeModelWithResponseStream`、`bedrock:Converse` 和 
`bedrock:ConverseStream` 的推理配置文件。

### 未找到跨度

确认以下所有内容：

- `AGENTCORE_EVALUATION_LOG_GROUP` 是准确的运行时日志组名称。
- 运行时可观察性和 CloudWatch 事务搜索在
相同的帐户和区域。
- 该角色可以启动查询并读取其结果。
- 运行时调用成功。
- 所选运行时是专用评估运行时及其部署
已启用消息内容捕获。

CloudWatch 摄取可能需要几分钟时间。仅在检查日志组和区域后才增加等待或重试
设置。

如果跨度存在，但正确性、目标成功或轨迹评估报告
缺少输入、输出或工具内容，请停止并要求运行时所有者验证
评估部署。不要在生产流量上启用内容捕获 
 作为故障排除捷径。

### 一名评估员的分数低于阈值

打开经过编辑的结果并识别案例和评估者。正确性 
 失败涉及预期响应，目标成功失败涉及 
 案例断言，精确轨迹失败意味着观察到的工具名称 
 或顺序与标记的预期轨迹不同。在更改规范夹具之前重新运行离线
Promptfoo套件：

```bash
npm run eval:validate
npm run eval:run
```

不要仅仅为了将真正的回归变成绿色而降低最低分数。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Optional Amazon Bedrock AgentCore Evaluations

Use this lane to evaluate the deterministic flight cases against one dedicated
non-production Amazon Bedrock AgentCore Runtime. It is optional, makes live AWS
calls to create test invocations, and scores their completed spans afterward.
It is separate from both production traffic and the offline Promptfoo contract
suite.

AWS documents that the OpenAI Agents integration reconstructs the prompt,
response, and tool activity from the Runtime's
[OpenTelemetry spans](<https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/supported-frameworks-openai-agents.html>).
The cookbook's normal tracing defaults redact that content, so this lane
requires a **dedicated non-production evaluation Runtime** configured by its
owner with `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true`. Do not
enable content capture on an ordinary production Runtime. The Runtime owner must
approve the test data, access, retention, and cleanup controls before handing
its ARN to the evaluator.

| Command | Purpose | Live credentials | Creates charges |
| --- | --- | --- | --- |
| `npm run eval:run` | Check all response-contract fixtures with Promptfoo's echo provider | No | No |
| `npm run eval:agent:validate` | Validate the actual-agent Promptfoo configuration | No | No |
| `RUN_PROMPTFOO_AGENT_EVALUATION=1 npm run eval:agent` | Run and score three fresh local Agents SDK outputs | Yes | Yes |
| `npm run eval:aws:validate` | Validate the AWS-tagged canonical fixture metadata | No | No |
| `AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 RUN_AWS_EVALUATION=1 npm run eval:aws` | Invoke the configured evaluation Runtime, retrieve its spans, and run AgentCore built-in evaluators | Yes | Yes |

The live command is never run in CI. This repository does not contain committed
proof of a live evaluation. The runner uses AgentCore's on-demand evaluation
API and saves its redacted result locally; it does not create a persistent
evaluation run in the AWS console.

## What the live runner evaluates

All three evaluation lanes read
`runtime-agent/evals/fixtures/flight-status-results.jsonl`, but they do not
replace or call each other:

- The credential-free Promptfoo suite checks every applicable checked-in
  contract fixture through the echo provider.
- The guarded actual-agent Promptfoo suite invokes the local Agents SDK
  workflow for the three tagged canonical cases and checks each fresh response
  against the Runtime contract and exact expected output.
- The AWS runner uses only valid canonical cases that are explicitly tagged
  with `aws_evaluation` metadata. Negative and contract-only cases are not sent
  to AWS.

For each tagged case, the runner:

1. Invokes the exact `AGENTCORE_RUNTIME_AGENT_ARN` with a new retained session
   ID and generated W3C trace context.
2. Waits for that session's spans to reach the configured CloudWatch log group.
3. Calls `EvaluationClient.run` with:
   - `Builtin.Correctness`
   - `Builtin.GoalSuccessRate`
   - `Builtin.TrajectoryExactOrderMatch`
4. Supplies the case's expected response, assertions, and expected tool order
   through `ReferenceInputs`.
5. Writes a redacted local result under `runtime-agent/evals/results/`.

The canonical fixture stores the local Promptfoo response. The AWS loader
first verifies that response against the current deterministic workflow, then
derives the same expected facts with `executionMode: "deployed"` for the
Runtime evaluation. Assertions and the expected tool trajectory live in the
case's `aws_evaluation` tag. This keeps one versioned flight dataset without
mislabeling the route, while preserving the independent evaluation commands.

This lane does not require the OpenAI tunnel, a ChatGPT plugin, CDK, or a local
agent process. It does require an already-deployed Runtime that emits its
session spans to CloudWatch in the same account and Region used by the
evaluation role.

## What to request from your AWS administrator

Copy this text into an enterprise access ticket and replace any values you
already know:

```text
I need to run the optional live AgentCore Evaluations workflow in the
flight-status cookbook against one dedicated non-production evaluation Runtime.

Please provide:
- The exact parent AgentCore Runtime ARN, endpoint ARN, Region, Runtime
  qualifier, and CloudWatch Runtime log-group name.
- A role I can assume from my existing AWS CLI identity.
- bedrock-agentcore:InvokeAgentRuntime on only those exact parent Runtime and
  selected endpoint ARNs.
- bedrock-agentcore:Evaluate and bedrock-agentcore:GetEvaluator for the
  approved AWS built-in evaluators.
- CloudWatch Logs query access needed to retrieve the Runtime session spans:
  logs:StartQuery for the designated Runtime log group and aws/spans, and
  logs:GetQueryResults with Resource "*"; AWS does not support resource-level
  scoping for GetQueryResults.
- bedrock:InvokeModel, bedrock:InvokeModelWithResponseStream,
  bedrock:Converse, and bedrock:ConverseStream on only the approved Amazon
  Bedrock judge model or inference profile used by Builtin.Correctness and
  Builtin.GoalSuccessRate.
- Confirmation that Runtime observability and CloudWatch Transaction Search
  are enabled in this account and Region and that the named log group receives
  the Runtime's session spans.
- Confirmation that this is a dedicated non-production evaluation Runtime with
  `OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true`, and that its trace
  content, access, retention, and cleanup have been approved.

This runner uses only Builtin.Correctness, Builtin.GoalSuccessRate, and
Builtin.TrajectoryExactOrderMatch. It does not deploy or change the Runtime and
does not create custom evaluators.

I do not need CDK, S3 artifact, ECR, Runtime create/update/delete,
iam:PassRole, Lambda, or custom-evaluator creation permissions.

Please return the evaluation role ARN, the approved source-profile/login
method, the exact Runtime values, the judge model or inference-profile ID, and
the expected AWS account ID so I can verify the assumed identity.
```

Send the administrator [the evaluation runner policy shape](<https://github.com/openai/openai-cookbook/blob/5bd6cb40b6b84b69b2032d810610aa85064102df/examples/partners/AWS/chatgpt_agents_sdk_aws_agentcore_cookbook/docs/aws-iam.md#optional-agentcore-evaluations-runner-role>)
if they ask for implementation details. The administrator owns the AWS policy;
do not paste policy JSON or AWS credentials into the application.

## Configure the AWS CLI profile

Add the role returned by the administrator to `~/.aws/config`. Replace the
placeholders:

```ini
[profile agentcore-evaluation]
role_arn = arn:aws:iam::<ACCOUNT_ID>:role/AgentCoreFlightEvaluation
source_profile = YOUR_EXISTING_AWS_PROFILE
region = us-west-2
```

Your organization may use IAM Identity Center or another credential source
instead of `source_profile`. Follow its normal login instructions.

Verify the identity before running the evaluation:

```bash
aws sts get-caller-identity --profile agentcore-evaluation
```

Stop if the returned account or role is not the one named in the access ticket.

## Configure the cookbook

From the repository root, create `.env` if it does not exist:

```bash
test -f .env || install -m 600 .env.example .env
chmod 600 .env
```

Mode `600` keeps the credential file readable and writable only by its owner.

Set the values supplied by the administrator:

```dotenv
AGENTCORE_RUNTIME_REGION=us-west-2
AGENTCORE_RUNTIME_AGENT_ARN=arn:aws:bedrock-agentcore:<REGION>:<ACCOUNT_ID>:runtime/<RUNTIME_ID>
AGENTCORE_RUNTIME_QUALIFIER=<QUALIFIER>
AGENTCORE_EVALUATION_LOG_GROUP=/aws/bedrock-agentcore/runtimes/<RUNTIME_ID>-DEFAULT
RUN_AWS_EVALUATION=0
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=0
```

Use the exact log-group name; do not infer it from a display name. Set the
qualifier to the value supplied by the Runtime owner. Leave it empty only when
that owner confirms the Runtime should use its default qualifier.

Keep both guards set to `0` in `.env`. The live command sets them to `1` only
for that process after the Runtime owner confirms the dedicated evaluation
Runtime's content-capture and data-handling configuration.

The optional controls and their defaults are:

```dotenv
AGENTCORE_EVALUATION_WAIT_SECONDS=180
AGENTCORE_EVALUATION_POLL_SECONDS=30
AGENTCORE_EVALUATION_ATTEMPTS=5
AGENTCORE_EVALUATION_MIN_SCORE=0.5
AGENTCORE_EVALUATION_CASE_IDS=
```

`AGENTCORE_EVALUATION_CASE_IDS` accepts comma-separated case IDs. Leave it
empty to run every tagged canonical case. Select one case for a lower-cost
first proof:

```dotenv
AGENTCORE_EVALUATION_CASE_IDS=<CASE_ID>
```

Do not put `AWS_PROFILE` or AWS credentials in `.env`. Export the profile in
the shell that runs the command.

## Validate without AWS

Install the locked dependencies, then validate the tagged cases:

```bash
cd runtime-agent
uv sync --locked --extra dev
npm ci
npm run eval:aws:validate
```

This command does not invoke the Runtime, query CloudWatch, or call an
evaluator. Fix any missing or invalid AWS fixture metadata before proceeding.
If `AGENTCORE_EVALUATION_CASE_IDS` contains an unknown ID, the live command
rejects it before creating any AWS client.

## Run the live evaluation

Sign in using your organization's normal AWS flow, then run:

```bash
export AWS_PROFILE=agentcore-evaluation
aws sts get-caller-identity
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 \
RUN_AWS_EVALUATION=1 \
npm run eval:aws
```

The command initially waits 180 seconds because Runtime spans are not
immediately queryable. It then makes up to five evaluation attempts, with 30
seconds between attempts that find no spans. The pinned AgentCore SDK may spend
up to 60 seconds querying CloudWatch within each attempt, so a no-spans failure
can take roughly ten minutes with the defaults, plus normal evaluator time.
Tune these settings only when the Runtime owner expects different ingestion
timing.

The command fails if it cannot invoke the Runtime, retrieve the matching
session spans, obtain all required evaluator results, or meet the configured
minimum score. A low score is an evaluation result, not an IAM failure.

## Handle the result

The runner prints a summary and saves a redacted JSON artifact under
`runtime-agent/evals/results/`. That directory is ignored by Git. The artifact
contains case and evaluator results plus redacted correlation values; it must
not contain AWS credentials or raw secrets.

Treat the artifact as environment evidence:

- Share it only through your organization's approved evidence location.
- Check it before sharing even though the runner redacts correlation values.
- Do not force-add it to Git.
- Record the Runtime suffix, Region, evaluation time, evaluator IDs, and
  pass/fail result with the external evidence.

No live artifact is committed by default because live success depends on the
caller's AWS account, role, Runtime, judge-model access, and observability
configuration.

## Troubleshooting

### `RUN_AWS_EVALUATION=1 is required`

This guard prevents accidental live calls. Run `npm run eval:aws:validate`
first. Set the guard only on the command that should create AWS activity:

```bash
AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 \
RUN_AWS_EVALUATION=1 \
npm run eval:aws
```

### `AGENTCORE_EVALUATION_CONTENT_CAPTURE_CONFIRMED=1 is required`

Do not bypass this guard for a normal production Runtime. Ask the Runtime owner
for a dedicated non-production evaluation Runtime whose deployment sets
`OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true`, then confirm the
organization has approved the trace-content access, retention, and cleanup
controls. Set the confirmation flag only on the intended evaluation command.

### Runtime invocation is denied

Ask the administrator to confirm
`bedrock-agentcore:InvokeAgentRuntime` is allowed on both the exact parent ARN
in `AGENTCORE_RUNTIME_AGENT_ARN` and the exact endpoint ARN selected by
`AGENTCORE_RUNTIME_QUALIFIER`. Also confirm the assumed role and Region.

### Evaluator access is denied

Send the denied action to the administrator. The runner requires
`bedrock-agentcore:Evaluate` and `bedrock-agentcore:GetEvaluator` for its
built-ins. Judge-related denials require access to the specifically approved
Bedrock model or inference profile for `bedrock:InvokeModel`,
`bedrock:InvokeModelWithResponseStream`, `bedrock:Converse`, and
`bedrock:ConverseStream`.

### No spans are found

Confirm all of the following:

- `AGENTCORE_EVALUATION_LOG_GROUP` is the exact Runtime log-group name.
- Runtime observability and CloudWatch Transaction Search are enabled in the
  same account and Region.
- The role can start a query and read its results.
- The Runtime invocation succeeded.
- The selected Runtime is the dedicated evaluation Runtime and its deployment
  has message-content capture enabled.

CloudWatch ingestion can take several minutes. Increase the wait or retry
settings only after checking the log group and Region.

If spans exist but correctness, goal-success, or trajectory evaluation reports
missing input, output, or tool content, stop and ask the Runtime owner to verify
the evaluation deployment. Do not enable content capture on production traffic
as a troubleshooting shortcut.

### One evaluator scores below the threshold

Open the redacted result and identify the case and evaluator. A correctness
failure concerns the expected response, a goal-success failure concerns the
case assertions, and an exact-trajectory failure means the observed tool names
or order differ from the tagged expected trajectory. Re-run the offline
Promptfoo suite before changing a canonical fixture:

```bash
npm run eval:validate
npm run eval:run
```

Do not lower the minimum score merely to turn a genuine regression green.


</details>
