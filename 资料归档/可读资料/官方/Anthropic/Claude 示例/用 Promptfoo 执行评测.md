# 用 Promptfoo 执行评测

说明准备 Node.js 与项目依赖、配置测试并运行评测的步骤，帮助对模型提示和输出做可重复比较。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2026-01-27](<https://github.com/anthropics/claude-cookbooks/commit/968fc1698b3f5fe0a1c67d2185d3264de4569d52>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/capabilities/classification/evaluation/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/用 Promptfoo 执行评测.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Promptfoo 进行评估



### 先决条件
要使用 Promptfoo，您需要在系统上安装 node.js 和 npm。欲了解更多信息，请关注[本指南](<https://docs.npmjs.com/downloading-and-installing-node-js-and-npm>)

您可以使用npm安装promptfoo或使用npx直接运行它。在本指南中，我们将使用 npx。

*注意：对于此示例，您不需要运行`npx promptfoo@latest init`，此目录中已经有一个初始化的`promptfooconfig.yaml`文件*

请参阅官方文档[此处](<https://www.promptfoo.dev/docs/getting-started>)



### 开始使用
评估由 `promptfooconfig.yaml` 文件安排。在此文件中，我们定义以下部分：

- 提示
    - Promptfoo 使您能够导入多种不同格式的提示。您可以阅读更多相关内容 [这里](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - 在此示例中，我们将加载 3 提示 - 与 `prompts.py` 文件中的 `guide.ipynb` 中使用的提示相同：
        - 这些函数与 `guide.ipynb` 中使用的函数相同，只是它们不调用 Claude API，只是返回提示符。 Promptfoo 然后处理调用 API 并存储结果的编排。
        - 您可以阅读有关提示功能的更多信息 [这里](<https://www.promptfoo.dev/docs/configuration/parameters#prompt-functions>)。使用 python 可以让我们重用 VectorDB 必要的课程 RAG，这定义在 `vectordb.py`.
- 供应商
    - 使用 Promptfoo，您可以从不同平台连接到许多不同的法学硕士，请参阅[此处了解更多信息](<https://www.promptfoo.dev/docs/providers>)。在 `guide.ipynb` 中，我们使用俳句和默认温度 0.0。我们将使用 Promptfoo 来试验一系列不同的温度设置，以确定适合我们用例的最佳选择。
- 测试
    - 我们将使用`guide.ipynb`中使用的相同数据，这些数据可以在[`dataset.csv`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/capabilities/classification/evaluation/dataset.csv>)中找到。
    - Promptfoo 有大量内置测试，可以在 [ 这里 ](<https://www.promptfoo.dev/docs/configuration/expected-outputs/deterministic>) 找到。
    - 在此示例中，我们将在 `dataset.csv` 中定义一个测试，因为我们的评估条件随每一行而变化，并在 `promptfooconfig.yaml` 中定义一个测试，以获得在所有测试用例中一致的条件。阅读有关此内容的更多信息 [此处](<https://www.promptfoo.dev/docs/configuration/parameters/#import-from-csv>)
- 变换
    - 在`defaultTest`部分我们定义了一个变换函数。这是一个 python 函数，它从 LLM 响应中提取我们想要测试的特定输出。
- 输出
    - 我们定义输出文件的路径。 Promptfoo可以输出多种格式的结果，[参见这里](<https://www.promptfoo.dev/docs/configuration/parameters/#output-file>)。或者，您可以使用 Promptfoo 的网络 UI、[ 参见此处 ](<https://www.promptfoo.dev/docs/usage/web-ui>)。


### 运行评估

要开始使用 Promptfoo，请打开终端并导航到此目录 (`./evaluation`)。

在运行评估之前，您必须定义以下环境变量：

`export ANTHROPIC_API_KEY=YOUR_API_KEY`  
`export VOYAGE_API_KEY=YOUR_API_KEY`

从 `evaluation` 目录中，运行以下命令。

`npx promptfoo@latest eval`

如果您想增加请求的并发性（默认 = 4），请运行以下命令。

`npx promptfoo@latest eval -j 25`  

评估完成后，终端将打印数据集中每一行的结果。

您现在可以返回`guide.ipynb`来分析结果！


## 原文对照

<details>
<summary>展开完整原文</summary>

# Evaluations with Promptfoo



### Pre-requisities 
To use Promptfoo you will need to have node.js & npm installed on your system. For more information follow [this guide](<https://docs.npmjs.com/downloading-and-installing-node-js-and-npm>)  

You can install promptfoo using npm or run it directly using npx. In this guide we will use npx.  

*Note: For this example you will not need to run `npx promptfoo@latest init` there is already an initialized `promptfooconfig.yaml` file in this directory*  

See the official docs [here](<https://www.promptfoo.dev/docs/getting-started>)  



### Getting Started
The evaluation is orchestrated by the `promptfooconfig.yaml` file. In this file we define the following sections:

- Prompts
    - Promptfoo enables you to import prompts in many different formats. You can read more about this [here](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - In this example we will load 3 prompts - the same used in `guide.ipynb` from the `prompts.py` file:
        - The functions are identical to those used in `guide.ipynb` except that instead of calling the Claude API they just return the prompt. Promptfoo then handles the orchestration of calling the API and storing the results.
        - You can read more about prompt functions [here](<https://www.promptfoo.dev/docs/configuration/parameters#prompt-functions>). Using python allows us to reuse the VectorDB class which is necessary for RAG, this is defined in `vectordb.py`.
- Providers
    - With Promptfoo you can connect to many different LLMs from different platforms, see [here for more](<https://www.promptfoo.dev/docs/providers>). In `guide.ipynb` we used Haiku with default temperature 0.0. We will use Promptfoo to experiment with an array of different temperature settings to identify the optimal choice for our use case.
- Tests
    - We will use the same data that was used in `guide.ipynb` which can be found in [`dataset.csv`](<https://github.com/anthropics/claude-cookbooks/blob/d7265d6ae994ccd8429db0594b000073b2f9ad43/capabilities/classification/evaluation/dataset.csv>).
    - Promptfoo has a wide array of built in tests which can be found [here](<https://www.promptfoo.dev/docs/configuration/expected-outputs/deterministic>).
    - In this example we will define a test in our `dataset.csv` as the conditions of our evaluation change with each row and a test in the `promptfooconfig.yaml` for conditions that are consistent across all test cases. Read more about this [here](<https://www.promptfoo.dev/docs/configuration/parameters/#import-from-csv>)
- Transform
    - In the `defaultTest` section we define a transform function. This is a python function which extracts the specific output we want to test from the LLM response. 
- Output
    - We define the path for the output file. Promptfoo can output results in many formats, [see here](<https://www.promptfoo.dev/docs/configuration/parameters/#output-file>). Alternatively you can use Promptfoo's web UI, [see here](<https://www.promptfoo.dev/docs/usage/web-ui>).


### Run the eval

To get started with Promptfoo open your terminal and navigate to this directory (`./evaluation`).

Before running your evaluation you must define the following environment variables:

`export ANTHROPIC_API_KEY=YOUR_API_KEY`  
`export VOYAGE_API_KEY=YOUR_API_KEY`

From the `evaluation` directory, run the following command.  

`npx promptfoo@latest eval`

If you would like to increase the concurrency of the requests (default = 4), run the following command.  

`npx promptfoo@latest eval -j 25`  

When the evaluation is complete the terminal will print the results for each row in the dataset.

You can now go back to `guide.ipynb` to analyze the results!


</details>
