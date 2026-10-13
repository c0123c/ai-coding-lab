# 用 VS Code AI 操作 Jupyter Notebook

说明创建笔记本、修改运行单元格、添加变量和输出上下文、局部编辑及跟随执行，配合人工检查代码结果。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/notebooks-with-ai.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/notebooks-with-ai.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/用 VS Code AI 操作 Jupyter Notebook.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: 101027aa-e73c-4d1b-a93f-b8ce10e1f946
DateApproved: 10/7/2026
MetaDescription: Create, edit, run, and analyze Jupyter notebooks with AI in {% data variables.product.prodname_vscode %}.
MetaSocialImage: ../images/shared/github-copilot-social.png
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 使用 {% data variables.product.prodname_vscode_shortname %} 中的 AI 处理 Jupyter 笔记本

在整个笔记本工作流程中使用 AI。 agent 可以创建 Jupyter 笔记本、编辑和运行单元、检查输出以及迭代错误。对于重点更改，请直接在笔记本单元中使用内联聊天。

本指南介绍如何在 Jupyter Notebook 中使用 AI 进行数据分析，同时查看生成的代码并控制其运行时间。

## 先决条件

* 设置 [{% data variables.product.prodname_copilot %} 在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>).

* 安装 [Jupyter 扩展](<https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter>)。了解如何配置 Python 环境并选择内核，请参阅 [Jupyter 笔记本在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/datascience/jupyter-notebooks.md>).

* 打开您信任的 [ 工作空间 ](<https://github.com/docs/editing/workspaces/workspace-trust.md>)。运行笔记本单元会在选定的内核中执行代码，并且可以访问工作区文件。

## 创建一个带有 agent 的笔记本

从您想要的结果开始，而不是指定单个单元格。 agent 规划笔记本，创建 Markdown 和代码单元，并可以运行代码来验证结果。

1. 打开 [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) (`kb(workbench.action.chat.open)`) 并启动 **本地** agent 会话。

1. 添加您的数据文件作为上下文，然后描述您想要的分析和输出。

    ```prompt
    Create a Jupyter notebook that uses pandas and seaborn to analyze #housing.csv. Clean the data, summarize key metrics, and visualize the price distribution.
    ```

1. 在继续之前，请查看任何请求的工具和命令批准。如果笔记本没有内核，请按照提示选择或创建Python环境。

agent 创建一个 `.ipynb` 文件，您可以在同一会话中继续优化该文件。您还可以以 `/newNotebook` 开始提示，以明确请求新的 Jupyter 笔记本。

## 通过编辑和运行单元格进行迭代

Agents 具有笔记本专用工具，用于理解单元结构、编辑单元、运行代码和读取单元输出。此编辑-运行循环让 agent 验证分析并响应运行时错误，而不是仅生成静态笔记本。

要求 agent 从当前笔记本继续：

```prompt
Run the notebook from top to bottom. Fix any errors you find, then explain the main result.
```

您还可以请求具体修订：

```prompt
Add a section that identifies outliers, explains how they affect the analysis, and compares the results before and after removing them.
```

当 agent 工作时，保持笔记本编辑器打开以查看单元格更改和输出。配置 agent 权限以控制哪些工具和命令调用需要批准。了解有关 [agent 批准 ](<https://github.com/docs/agents/run/approvals.md>) 的更多信息。

<!-- TODO: Add a screenshot showing an agent editing and running notebook cells in the current UI. -->

## 将笔记本上下文添加到提示中

当请求取决于当前内核状态或单元结果时，给出 agent 笔记本特定的上下文。

### 添加内核变量

在笔记本处于活动状态的情况下，在聊天输入中键入 `#` 并从建议中选择一个内核变量。所选变量可用作您的请求的上下文。

例如，添加 `df` 变量并输入：

```prompt
Check this DataFrame for missing values and recommend a cleaning strategy.
```

### 添加单元格输出

从单元输出工具栏中，选择 **将单元输出添加到聊天 ** 以附加支持的输出。然后，您可以要求 agent 解释图表、诊断错误或根据结果更新笔记本。

```prompt
Explain the outliers in this chart and update the analysis to investigate them.
```

<!-- TODO: Add a screenshot showing Add Cell Output to Chat in the current notebook UI. -->

## 通过内联聊天进行焦点单元格更改

当您想要更改一个单元格而不启动更广泛的 agent 工作流程时，请使用内联聊天。

1. 将 cursor 放入单元格中或选择要更改的代码。

1. 按 `kb(inlinechat.start)` 并描述更改。

1. 查看建议的差异。选择 ** 接受并运行 ** 以应用更改并一步执行单元格。

要生成新单元格，请在单元格之间选择 **生成 **，或在单元格编辑器未聚焦时按 `kb(notebook.cell.chat.start)`。 **生成**操作是实验性的，由`setting(notebook.experimental.generate)`设置控制。

<!-- TODO: Add a screenshot showing inline chat editing an existing notebook cell in the current UI. -->

## 跟踪单元执行（实验）

要使 agent 正在运行的单元保持在视图中，请启用 `setting(github.copilot.chat.notebook.followCellExecution.enabled)` 设置。 agent 开始运行单元后，使用笔记本工具栏中的固定操作来暂停或恢复当前执行。

## 后续步骤

* [在 {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/datascience/jupyter-notebooks.md>) 中了解有关 Jupyter 笔记本的更多信息
* [添加聊天上下文](<https://github.com/docs/chat/copilot-chat-context.md>)
* [探索 Jupyter 笔记本的提示示例](<https://github.com/docs/agents/guides/prompt-examples.md#working-with-jupyter-notebooks>)


## 原文对照

<details>
<summary>展开完整原文</summary>

# Work with Jupyter notebooks using AI in {% data variables.product.prodname_vscode_shortname %}

Use AI throughout your notebook workflow. An agent can create a Jupyter notebook, edit and run cells, inspect outputs, and iterate on errors. For focused changes, use inline chat directly in a notebook cell.

This guide shows how to use AI for data analysis in a Jupyter notebook while you review the generated code and control when it runs.

## Prerequisites

* Set up [{% data variables.product.prodname_copilot %} in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/setup/copilot.md>).

* Install the [Jupyter extension](<https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter>). To learn how to configure a Python environment and select a kernel, see [Jupyter notebooks in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/datascience/jupyter-notebooks.md>).

* Open a [workspace that you trust](<https://github.com/docs/editing/workspaces/workspace-trust.md>). Running a notebook cell executes code in the selected kernel and can access workspace files.

## Create a notebook with an agent

Start with the outcome you want instead of specifying individual cells. The agent plans the notebook, creates Markdown and code cells, and can run the code to validate the result.

1. Open the [{% data variables.copilot.chat_view %}](<https://github.com/docs/agents/run/chat-view.md>) (`kb(workbench.action.chat.open)`) and start a **Local** agent session.

1. Add your data file as context, then describe the analysis and output you want.

    ```prompt
    Create a Jupyter notebook that uses pandas and seaborn to analyze #housing.csv. Clean the data, summarize key metrics, and visualize the price distribution.
    ```

1. Review any requested tool and command approvals before you continue. If the notebook does not have a kernel, follow the prompts to select or create a Python environment.

The agent creates an `.ipynb` file that you can continue to refine in the same session. You can also start your prompt with `/newNotebook` to explicitly request a new Jupyter notebook.

## Iterate by editing and running cells

Agents have notebook-specific tools for understanding the cell structure, editing cells, running code, and reading cell outputs. This edit-run loop lets the agent validate the analysis and respond to runtime errors instead of only generating a static notebook.

Ask the agent to continue from the current notebook:

```prompt
Run the notebook from top to bottom. Fix any errors you find, then explain the main result.
```

You can also request a specific revision:

```prompt
Add a section that identifies outliers, explains how they affect the analysis, and compares the results before and after removing them.
```

Keep the notebook editor open to review cell changes and outputs as the agent works. Configure agent permissions to control which tool and command invocations require approval. Learn more about [agent approvals](<https://github.com/docs/agents/run/approvals.md>).

<!-- TODO: Add a screenshot showing an agent editing and running notebook cells in the current UI. -->

## Add notebook context to a prompt

Give the agent notebook-specific context when a request depends on the current kernel state or a cell result.

### Add a kernel variable

With the notebook active, type `#` in the chat input and select a kernel variable from the suggestions. The selected variable becomes available as context for your request.

For example, add the `df` variable and enter:

```prompt
Check this DataFrame for missing values and recommend a cleaning strategy.
```

### Add a cell output

From the cell output toolbar, select **Add Cell Output to Chat** to attach a supported output. You can then ask the agent to explain a chart, diagnose an error, or update the notebook based on the result.

```prompt
Explain the outliers in this chart and update the analysis to investigate them.
```

<!-- TODO: Add a screenshot showing Add Cell Output to Chat in the current notebook UI. -->

## Make focused cell changes with inline chat

Use inline chat when you want to change one cell without starting a broader agent workflow.

1. Place the cursor in a cell or select the code you want to change.

1. Press `kb(inlinechat.start)` and describe the change.

1. Review the suggested diff. Select **Accept and Run** to apply the change and execute the cell in one step.

To generate a new cell, select **Generate** between cells or press `kb(notebook.cell.chat.start)` when a cell editor is not focused. The **Generate** action is experimental and is controlled by the `setting(notebook.experimental.generate)` setting.

<!-- TODO: Add a screenshot showing inline chat editing an existing notebook cell in the current UI. -->

## Follow cell execution (Experimental)

To keep the cell that an agent is running in view, enable the `setting(github.copilot.chat.notebook.followCellExecution.enabled)` setting. After the agent starts to run cells, use the pin action in the notebook toolbar to pause or resume following the current execution.

## Next steps

* [Learn more about Jupyter notebooks in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/datascience/jupyter-notebooks.md>)
* [Add context to chat](<https://github.com/docs/chat/copilot-chat-context.md>)
* [Explore prompt examples for Jupyter notebooks](<https://github.com/docs/agents/guides/prompt-examples.md#working-with-jupyter-notebooks>)


</details>
