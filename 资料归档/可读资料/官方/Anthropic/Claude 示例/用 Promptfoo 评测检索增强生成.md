# 用 Promptfoo 评测检索增强生成

说明环境和配置，并分别评测检索效果与端到端回答，帮助将资料检索和最终回复的质量分开检查。

- 来源：Anthropic／Claude 示例
- 资料类型：存档文档
- 存档版本：`d7265d6ae994`。
- 文档时间：源码提交（存档版）：[2025-10-14](<https://github.com/anthropics/claude-cookbooks/commit/ed9b068f77bcd4d663bfc2bc564ad132c7b7d85d>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/anthropics/claude-cookbooks/blob/main/capabilities/retrieval_augmented_generation/evaluation/README.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/Claude 示例/用 Promptfoo 评测检索增强生成.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Promptfoo 进行评估

### 先决条件
要使用 Promptfoo，您需要在系统上安装 node.js 和 npm。欲了解更多信息，请关注[本指南](<https://docs.npmjs.com/downloading-and-installing-node-js-and-npm>)

您可以使用npm安装promptfoo或使用npx直接运行它。在本指南中，我们将使用 npx。

*注意：对于此示例，您不需要运行`npx promptfoo@latest init`，此目录中已经有一个初始化的`promptfooconfig.yaml`文件*

请参阅官方文档[此处](<https://www.promptfoo.dev/docs/getting-started>)


### 开始使用
评估由 `promptfooconfig...` `.yaml` 文件精心安排。在我们的应用程序中，我们将评估逻辑划分为用于评估检索系统的`promptfooconfig_retrieval.yaml`和用于评估端到端性能的`promptfooconfig_end_to_end.yaml`。在每个文件中，我们定义以下部分

### 检索评估

- 提示
    - Promptfoo 使您能够导入多种不同格式的提示。您可以阅读更多相关内容 [这里](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - 在我们的例子中，我们每次都会跳过提供新的提示，而仅将 `{{query}}` 传递给每个检索“提供者”进行评估
- 供应商
    - 我们没有使用标准的 LLM 提供程序，而是为 `guide.ipynb` 中找到的每个检索方法编写了自定义提供程序
- 测试
    - 我们将使用`guide.ipynb`中使用的相同数据。我们将其拆分为 `end_to_end_dataset.csv` 和 `retrieval_dataset.csv` 并向每个数据集添加一个 `__expected` 列，这允许我们自动为每一行运行断言
    - 您可以在`eval_end_to_end.py`中找到我们的检索评估逻辑

### 端到端评估

- 提示
    - Promptfoo 使您能够导入多种不同格式的提示。您可以阅读更多相关内容 [这里](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - 我们的端到端评估配置中有 3 提示：每个提示对应于一种方法使用
        - 这些函数与 `guide.ipynb` 中使用的函数相同，只是它们不调用 Claude API，只是返回提示符。 Promptfoo 然后处理调用 API 并存储结果的编排。
        - 您可以阅读有关提示功能的更多信息 [这里](<https://www.promptfoo.dev/docs/configuration/parameters#prompt-functions>)。使用 python 可以让我们重用 VectorDB 必要的课程 RAG，这定义在 `vectordb.py`.
- 供应商
    - 使用 Promptfoo，您可以从不同平台连接到许多不同的法学硕士，请参阅[此处了解更多信息](<https://www.promptfoo.dev/docs/providers>)。在 `guide.ipynb` 中，我们使用俳句和默认温度 0.0。我们将使用 Promptfoo 来尝试不同的模型。
- 测试
    - 我们将使用`guide.ipynb`中使用的相同数据。我们将其拆分为 `end_to_end_dataset.csv` 和 `retrieval_dataset.csv` 并向每个数据集添加一个 `__expected` 列，这允许我们自动为每一行运行断言
    - Promptfoo 有大量内置测试，可以在 [ 这里 ](<https://www.promptfoo.dev/docs/configuration/expected-outputs/deterministic>) 找到。
    - 检索系统的测试逻辑可以在`eval_retrieval.py`中找到，端到端系统的测试逻辑可以在`eval_end_to_end.py`中找到。
- 输出
    - 我们定义输出文件的路径。 Promptfoo可以输出多种格式的结果，[参见这里](<https://www.promptfoo.dev/docs/configuration/parameters/#output-file>)。或者，您可以使用 Promptfoo 的网络 UI、[ 参见此处 ](<https://www.promptfoo.dev/docs/usage/web-ui>)。


### 运行评估

要开始使用 Promptfoo，请打开终端并导航到此目录 (`./evaluation`)。

在运行评估之前，您必须定义以下环境变量：

`export ANTHROPIC_API_KEY=YOUR_API_KEY`  
`export VOYAGE_API_KEY=YOUR_API_KEY`

从 `evaluation` 目录中，运行以下命令之一。

- 评估端到端系统性能：`npx promptfoo@latest eval -c promptfooconfig_end_to_end.yaml --output ../data/end_to_end_results.json`

- 单独评估检索系统性能：`npx promptfoo@latest eval -c promptfooconfig_retrieval.yaml --output ../data/retrieval_results.json`

评估完成后，终端将打印数据集中每一行的结果。您还可以运行`npx promptfoo@latest view`以在promptfoo UI查看器中查看输出。


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
The evaluation is orchestrated by the `promptfooconfig...` `.yaml` files. In our application we divide the evaluation logic between `promptfooconfig_retrieval.yaml` for evaluating the retrieval system and `promptfooconfig_end_to_end.yaml` to evaluate the end to end performance. In each of these files we define the following sections

### Retrieval Evaluations

- Prompts
    - Promptfoo enables you to import prompts in many different formats. You can read more about this [here](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - In our case, we skip providing a new prompt each time, and merely pass through the `{{query}}` to each retrieval 'provider' for evaluation
- Providers
    - Instead of using a standard LLM provider, we wrote custom providers for each retrieval method found in `guide.ipynb`
- Tests
    - We will use the same data that was used in `guide.ipynb`. We split it into `end_to_end_dataset.csv` and `retrieval_dataset.csv` and added an `__expected` column to each dataset which allows us to automatically run assertions for each row
    - You can find our retrieval evaluation logic in `eval_end_to_end.py`

### End to End Evaluations

- Prompts
    - Promptfoo enables you to import prompts in many different formats. You can read more about this [here](<https://www.promptfoo.dev/docs/configuration/parameters>).
    - We have 3 prompts in our end to end evaluation config: each of which corresponds to a method use
        - The functions are identical to those used in `guide.ipynb` except that instead of calling the Claude API they just return the prompt. Promptfoo then handles the orchestration of calling the API and storing the results.
        - You can read more about prompt functions [here](<https://www.promptfoo.dev/docs/configuration/parameters#prompt-functions>). Using python allows us to reuse the VectorDB class which is necessary for RAG, this is defined in `vectordb.py`.
- Providers
    - With Promptfoo you can connect to many different LLMs from different platforms, see [here for more](<https://www.promptfoo.dev/docs/providers>). In `guide.ipynb` we used Haiku with default temperature 0.0. We will use Promptfoo to experiment with different models.
- Tests
    - We will use the same data that was used in `guide.ipynb`. We split it into `end_to_end_dataset.csv` and `retrieval_dataset.csv` and added an `__expected` column to each dataset which allows us to automatically run assertions for each row
    - Promptfoo has a wide array of built in tests which can be found [here](<https://www.promptfoo.dev/docs/configuration/expected-outputs/deterministic>).
    - You can find the test logic for the retrieval system in `eval_retrieval.py` and the test logic for the end to end system in `eval_end_to_end.py`
- Output
    - We define the path for the output file. Promptfoo can output results in many formats, [see here](<https://www.promptfoo.dev/docs/configuration/parameters/#output-file>). Alternatively you can use Promptfoo's web UI, [see here](<https://www.promptfoo.dev/docs/usage/web-ui>).


### Run the eval

To get started with Promptfoo open your terminal and navigate to this directory (`./evaluation`).

Before running your evaluation you must define the following enviroment variables:

`export ANTHROPIC_API_KEY=YOUR_API_KEY`  
`export VOYAGE_API_KEY=YOUR_API_KEY`

From the `evaluation` directory, run one of the following commands.  

- To evaluate the end to end system performance: `npx promptfoo@latest eval -c promptfooconfig_end_to_end.yaml --output ../data/end_to_end_results.json`

- To evaluate the retrieval system performance in isolation: `npx promptfoo@latest eval -c promptfooconfig_retrieval.yaml --output ../data/retrieval_results.json`

When the evaluation is complete the terminal will print the results for each row in the dataset. You can also run `npx promptfoo@latest view` to view outputs in the promptfoo UI viewer.


</details>
