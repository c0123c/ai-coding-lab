# 验证 gpt-oss 推理实现

说明 API 格式、工具调用和模型行为的检查方法，并用评测验证不同提供方实现；属于模型集成工程参考。

- 来源：OpenAI／Cookbook
- 资料类型：存档文档
- 存档版本：`5bd6cb40b6b8`。
- 文档时间：源码提交（存档版）：[2025-08-11](<https://github.com/openai/openai-cookbook/commit/28881e5c855d121baba02ade0c929933275f50ac>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/openai/openai-cookbook/blob/main/articles/gpt-oss/verifying-implementations.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/OpenAI/Cookbook/验证 gpt-oss 推理实现.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 验证 gpt-oss 实施

[OpenAI gpt-oss 模型](<https://openai.com/open-models>) 向开放模型生态系统引入了许多新概念，[让它们按预期执行可能需要一些时间](<https://x.com/ClementDelangue/status/1953119901649891367>)。本指南旨在帮助开发人员构建推理解决方案以验证其实现，或者帮助想要自行测试任何提供商的实现以获得信心的开发人员。

## 为什么实施 gpt-oss 模型不同？

新型号的行为与我们的其他一些 OpenAI 型号比现有的开放型号更相似。几个例子包括：

1. **和声响应格式。** 这些模型根据我们的 [OpenAI 和声格式 ](<https://cookbook.openai.com/articles/openai-harmony>) 进行训练来构建对话。虽然常规 API 开发人员在大多数情况下不需要处理和谐，但提供聊天完成兼容、响应兼容或其他推理 API 的推理提供程序需要将输入正确映射到 OpenAI 和谐格式。如果模型没有收到正确格式的提示，则可能会出现级联生成问题，并且至少会导致函数调用性能较差。
2. **工具调用之间的处理思路（CoT）**。这些模型可以作为 CoT 的一部分执行工具调用。这样做的结果是模型需要在后续采样中接收 CoT，直到达到最终响应。这意味着，虽然原始 CoT 不应显示给最终用户，但它应该由 API 返回，以便开发人员可以将其与工具调用和工具输出一起传回。 [您可以在这个单独的指南](<https://cookbook.openai.com/articles/gpt-oss/handle-raw-cot>)中了解更多信息。
3. **实际推理代码的差异**。我们专门以 MXFP4 格式发布了专家混合 (MoE) 权重。这仍然是一种相对较新的格式，与其他架构决策一起，为其他开放模型编写的现有推理代码必须适应 gpt-oss 模型。因此，我们发布了基本（未优化）的 [PyTorch 实现 ](<https://github.com/openai/gpt-oss/tree/main/gpt_oss/torch>) 和 [ 更优化的 Triton 实现 ](<https://github.com/openai/gpt-oss/tree/main/gpt_oss/triton>)。此外，我们还验证了 [vLLM 实现 ](<https://github.com/vllm-project/vllm/blob/7e3a8dc90670fd312ce1e0d4eba9bf11c571e3ad/vllm/model_executor/models/gpt_oss.py>) 的正确性。我们希望这些可以作为其他实现的教育材料。

## API 设计

### 回复 API

为了获得最佳性能，我们建议推理提供者实现我们的响应 API 格式，因为 API 形状是专门为输出原始 CoT 以及汇总 CoTs（显示给用户）和工具调用等行为而设计的，而无需将其他属性附加到格式上。为了获得准确的性能，最重要的 
 部分是将原始 CoT 作为 `output` 的一部分返回。

为此，我们向响应 API 的 `reasoning` 项目添加了一个新的 `content` 数组。原始 CoT 应包装到 `reasoning_text` 类型元素中，使整体输出项如下所示：

```
{
  "type": "reasoning",
  "id": "item_67ccd2bf17f0819081ff3bb2cf6508e60bb6a6b452d3795b",
  "status": "completed",
  "summary": [
    /* optional summary elements */
  ],
  "content": [
    {
      "type": "reasoning_text",
      "text": "The user needs to know the weather, I will call the get_weather tool."
    }
  ]
}
```

这些项目应在后续回合中接收，然后插入回和声格式的提示中，如 [ 原始 CoT 处理指南 ](<https://cookbook.openai.com/articles/gpt-oss/handle-raw-cot>) 中所述。

[查看整个规范的响应API文档](<https://platform.openai.com/docs/api-reference/responses/create>)。

### 聊天完成

许多提供商都提供与聊天完成兼容的 API。虽然我们没有在文档中扩充已发布的 API 参考来提供接收原始 CoT 的方法，但通过聊天完成兼容的 API 提供 gpt-oss 模型的提供商将 CoT 作为其消息的一部分返回，并且开发人员有办法将它们传回，这一点仍然很重要。

目前，社区中还没有普遍同意的规范，消息的一般属性是 `reasoning` 或 `reasoning_content`。 **为了与 OpenAI Agents SDK 等客户端兼容，我们建议使用 `reasoning` 字段作为聊天完成 ** 中原始 CoT 的主要属性。

## 快速验证刀具调用和API形状

要验证提供程序是否正常工作，您可以使用我们的 [gpt-oss GitHub 存储库 ](<https://github.com/openai/gpt-oss>) 中发布的 Node.js 脚本，您也可以使用它来运行其他评估。您需要安装 [Node.js](<http://nodejs.org/>) 或类似的运行时才能运行测试。

这些测试将对您尝试测试的响应 API 或聊天完成 API 运行一系列 tool/function 调用请求。然后，他们将评估是否调用了正确的工具以及 API 形状是否正确。

这在很大程度上充当冒烟测试，但应该可以很好地指示 API 是否与我们的 SDK 兼容并且可以处理基本的函数调用。它不保证推理实现的完全准确性（有关详细信息，请参阅下面的评估部分），也不保证与 OpenAI API 完全兼容。它们仍然应该是主要实施问题的有用指标。

要运行测试套件，请运行以下命令：

```shell
# clone the repository
git clone https://github.com/openai/gpt-oss.git

# go into the compatibility test directory
cd gpt-oss/compatibility-test/

# install the dependencies
npm install

# change the provider config in providers.ts to add your provider

# run the tests
npm start -- --provider <your-provider-name>
```

之后，您应该收到 API 实现的结果以及有关函数调用性能的任何详细信息。

如果测试成功，输出应在 pass@k 和 pass^k 上显示 0 无效请求和超过 90%。这意味着实施应该是正确的。为了完全确定，您还应该按如下所述检查评估。

如果您想详细查看各个响应，可以使用在您的目录中创建的 `jsonl` 文件。

您还可以启用调试模式以使用 `DEBUG=openai-agents:openai npm start -- --provider <provider-name>` 查看任何实际请求负载，但它可能会产生噪音。要仅运行一项测试，请使用 `-n 1` 标志以便于调试。要测试流事件，您可以使用 `--streaming`。

## 通过evals验证正确性

Artificial Analysis 的团队正在为各种提供商运行 AIME 和 GPQA 评估。如果您不确定您的提供商，[查看人工分析以获取最新指标](<https://artificialanalysis.ai/models/gpt-oss-120b/providers#evaluations>)。

为了安全起见，您应该考虑自己运行评估。要运行您自己的评估，您可以在与上面的测试相同的存储库中找到 `gpt_oss/evals` 文件夹，其中包含我们用于验证 AIME（每个问题的 16 尝试次数）、GPQA（每个问题的 8 尝试次数）和 Healthbench 的测试工具（1 每个问题的尝试）对 vLLM 实现和我们自己的一些参考实现进行评估。您可以使用相同的脚本来测试您的实现。

要测试响应 API 兼容 API 运行：

```bash
python -m gpt_oss.evals --base-url http://localhost:8000/v1 --eval aime25 --sampler responses --model openai/gpt-oss-120b --reasoning-effort high
```

要测试聊天完成 API 兼容 API 运行：

```bash
python -m gpt_oss.evals --base-url http://localhost:8000/v1 --eval aime25 --sampler chat_completions --model openai/gpt-oss-120b --reasoning-effort high
```

如果您得到与我们发布的类似的基准测试结果，并且上面的函数调用测试成功，那么您可能已经正确实现了 gpt-oss。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Verifying gpt-oss implementations

The [OpenAI gpt-oss models](<https://openai.com/open-models>) are introducing a lot of new concepts to the open-model ecosystem and [getting them to perform as expected might take some time](<https://x.com/ClementDelangue/status/1953119901649891367>). This guide is meant to help developers building inference solutions to verify their implementations or for developers who want to test any provider’s implementation on their own to gain confidence.

## Why is implementing gpt-oss models different?

The new models behave more similarly to some of our other OpenAI models than to existing open models. A couple of examples include:

1. **The harmony response format.** These models were trained on our [OpenAI harmony format](<https://cookbook.openai.com/articles/openai-harmony>) to structure a conversation. While regular API developers won’t need to deal with harmony in most cases, the inference providers that provide a Chat Completions-compatible, Responses-compatible or other inference API need to map the inputs correctly to the OpenAI harmony format. If the model does not receive the prompts in the right format this can have cascading generation issues and at minimum a worse function calling performance.
2. **Handling chain of thought (CoT) between tool calls**. These models can perform tool calls as part of the CoT. A consequence of this is that the model needs to receive the CoT in subsequent sampling until it reaches a final response. This means that while the raw CoT should not be displayed to end-users, it should be returned by APIs so that developers can pass it back in along with the tool call and tool output. [You can learn more about it in this separate guide](<https://cookbook.openai.com/articles/gpt-oss/handle-raw-cot>).
3. **Differences in actual inference code**. We published our mixture-of-experts (MoE) weights exclusively in MXFP4 format. This is still a relatively new format and along with other architecture decisions, existing inference code that was written for other open-models will have to be adapted for gpt-oss models. For that reason we published both a basic (unoptimized) [PyTorch implementation](<https://github.com/openai/gpt-oss/tree/main/gpt_oss/torch>), and a [more optimized Triton implementation](<https://github.com/openai/gpt-oss/tree/main/gpt_oss/triton>). Additionally, we verified the [vLLM implementation](<https://github.com/vllm-project/vllm/blob/7e3a8dc90670fd312ce1e0d4eba9bf11c571e3ad/vllm/model_executor/models/gpt_oss.py>) for correctness. We hope these can serve as educational material for other implementations.

## API Design

### Responses API

For best performance we recommend inference providers to implement our Responses API format as the API shape was specifically designed for behaviors like outputting raw CoT along with summarized CoTs (to display to users) and tool calls without bolting additional properties onto a format. The most important  
part for accurate performance is to return the raw CoT as part of the `output`.

For this we added a new `content` array to the Responses API’s `reasoning` items. The raw CoT should be wrapped into `reasoning_text` type element, making the overall output item look the following:

```
{
  "type": "reasoning",
  "id": "item_67ccd2bf17f0819081ff3bb2cf6508e60bb6a6b452d3795b",
  "status": "completed",
  "summary": [
    /* optional summary elements */
  ],
  "content": [
    {
      "type": "reasoning_text",
      "text": "The user needs to know the weather, I will call the get_weather tool."
    }
  ]
}
```

These items should be received in subsequent turns and then inserted back into the harmony formatted prompt as outlined in the [raw CoT handling guide](<https://cookbook.openai.com/articles/gpt-oss/handle-raw-cot>).

[Check out the Responses API docs for the whole specification](<https://platform.openai.com/docs/api-reference/responses/create>).

### Chat Completions

A lot of providers are offering a Chat Completions-compatible API. While we have not augmented our published API reference on the docs to provide a way to receive raw CoT, it’s still important that providers that offer the gpt-oss models via a Chat Completions-compatible API return the CoT as part of their messages and for developers to have a way to pass them back.

There is currently no generally agreed upon specification in the community with the general properties on a message being either `reasoning` or `reasoning_content`. **To be compatible with clients like the OpenAI Agents SDK we recommend using a `reasoning` field as the primary property for the raw CoT in Chat Completions**.

## Quick verification of tool calling and API shapes

To verify if a provider is working you can use the Node.js script published in our [gpt-oss GitHub repository](<https://github.com/openai/gpt-oss>) that you can also use to run other evals. You’ll need [Node.js](<http://nodejs.org/>) or a similar runtime installed to run the tests.

These tests will run a series of tool/function calling based requests to the Responses API or Chat Completions API you are trying to test. Afterwards they will evaluate both whether the right tool was called and whether the API shapes are correct.

This largely acts as a smoke test but should be a good indicator on whether the APIs are compatible with our SDKs and can handle basic function calling. It does not guarantee full accuracy of the inference implementation (see the evals section below for details on that) nor does it guarantee full compatibility with the OpenAI APIs. They should still be a helpful indicator of major implementation issues.

To run the test suite run the following commands:

```shell
# clone the repository
git clone https://github.com/openai/gpt-oss.git

# go into the compatibility test directory
cd gpt-oss/compatibility-test/

# install the dependencies
npm install

# change the provider config in providers.ts to add your provider

# run the tests
npm start -- --provider <your-provider-name>
```

Afterwards you should receive a result of both the API implementation and any details on the function call performance.

If your tests are successful, the output should show 0 invalid requests and over 90% on both pass@k and pass^k. This means the implementation should likely be correct. To be fully sure, you should also inspect the evals as described below.

If you want a detailed view of the individual responses, you can the `jsonl` file that was created in your directory.

You can also enable debug mode to view any of the actual request payloads using `DEBUG=openai-agents:openai npm start -- --provider <provider-name>` but it might get noisy. To run only one test use the `-n 1` flag for easier debugging. For testing streaming events you can use `--streaming`.

## Verifying correctness through evals

The team at Artificial Analysis is running AIME and GPQA evals for a variety of providers. If you are unsure about your provider, [check out Artificial Analysis for the most recent metrics](<https://artificialanalysis.ai/models/gpt-oss-120b/providers#evaluations>).

To be on the safe side you should consider running evals yourself. To run your own evals, you can find in the same repository as the test above a `gpt_oss/evals` folder that contains the test harnesses that we used to verify the AIME (16 attempts per problem), GPQA (8 attempts per problem) and Healthbench (1 attempt per problem) evals for the vLLM implementation and some of our own reference implementations. You can use the same script to test your implementations.

To test a Responses API compatible API run:

```bash
python -m gpt_oss.evals --base-url http://localhost:8000/v1 --eval aime25 --sampler responses --model openai/gpt-oss-120b --reasoning-effort high
```

To test a Chat Completions API compatible API run:

```bash
python -m gpt_oss.evals --base-url http://localhost:8000/v1 --eval aime25 --sampler chat_completions --model openai/gpt-oss-120b --reasoning-effort high
```

If you are getting similar benchmark results as those published by us and your function calling tests above succeeded you likely have a correct implementation of gpt-oss.


</details>
