# 用 Agent 修复 API 缺陷

通过 Node.js 分页问题演示复现、代码调查、失败回归测试、修复与边界验证，强调用实际运行结果确认修改。

- 来源：Microsoft／VS Code
- 资料类型：文档源码（含未展开模板）
- 存档版本：`7dde53519982`。
- 文档时间：官方审核：[2026-10-07](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/fix-a-bug-with-agents.md>)；源码提交（存档版）：[2026-10-06](<https://github.com/microsoft/vscode-docs/commit/4fe6b03408632f2e9338a970d26afa480e4a022d>)；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/microsoft/vscode-docs/blob/main/docs/agents/guides/fix-a-bug-with-agents.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Microsoft/VS Code/用 Agent 修复 API 缺陷（存档7dde5351-641）.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
ContentId: b850af5c-5578-4981-a810-62050e0abfb8
DateApproved: 10/7/2026
MetaDescription: Fix a pagination bug in a Node.js API with an AI agent in VS Code by reproducing it, adding regression tests, and reviewing the fix.
MetaSocialImage: ../images/shared/github-copilot-social.png
Keywords:
- ai
- agents
- debugging
- testing
- api
- tutorial
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

# 教程：使用 agent 修复 API 错误

大多数软件工作都是从现有代码和错误报告开始的，而不是空项目。在本教程中，您将使用 {% data variables.product.prodname_vscode %} 中的 AI agent 来调查分页 API 跳过记录的原因，添加证明错误的回归测试，实施重点修复并验证结果。

> [!NOTE]
> 示例 API 适合一个文件，并且仅使用内置的 Node.js 模块，因此您可以专注于调试工作流程。当请求处理、应用程序逻辑和测试分布在较大的项目中时，适用相同的流程。

## 先决条件

要完成本教程，您需要：

* [下载并安装{% data variables.product.prodname_vscode %}](<https://github.com/download>)。
* [启用{% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/getstarted/overview.md#enable-ai-features>)中的AI功能。
* 安装 [Node.js 22 或更高版本 ](<https://nodejs.org/en/download>)。
* 安装 [Git](<https://git-scm.com/downloads>) 并配置它以进行提交。

## 设置示例 API

从具有有效测试但包含分页错误的 API 开始。

1. 创建一个名为`pagination-bug`的文件夹并在其中初始化Git存储库：

    ```bash
    mkdir pagination-bug
    cd pagination-bug
    git init
    ```

1. 在{% data variables.product.prodname_vscode_shortname %}中，选择**文件**>**打开文件夹**，然后打开`pagination-bug`文件夹。

1. 创建一个名为 `issues-api.js` 的文件并粘贴以下代码：

    ```javascript
    const assert = require("node:assert/strict");
    const http = require("node:http");
    const { test } = require("node:test");

    const PORT = 3000;
    const PAGE_SIZE = 10;
    const issues = Array.from({ length: 35 }, (_, index) => ({
      id: index + 1,
      title: `Issue ${index + 1}`,
    }));

    function getIssues(requestUrl) {
      const url = new URL(requestUrl, `http://localhost:${PORT}`);
      const pageParameter = url.searchParams.get("page");
      const page = pageParameter === null ? 0 : Number(pageParameter);

      if (
        pageParameter !== null &&
        (!Number.isInteger(page) || page < 1)
      ) {
        return {
          status: 400,
          body: { error: "page must be a positive integer" },
        };
      }

      const start = page * PAGE_SIZE;
      const requestedPage = pageParameter === null ? 1 : page;

      return {
        status: 200,
        body: {
          page: requestedPage,
          pageSize: PAGE_SIZE,
          total: issues.length,
          issues: issues.slice(start, start + PAGE_SIZE),
        },
      };
    }

    function createServer() {
      return http.createServer((request, response) => {
        const requestUrl = request.url ?? "/";
        const url = new URL(requestUrl, `http://localhost:${PORT}`);

        let result;
        if (request.method === "GET" && url.pathname === "/issues") {
          result = getIssues(requestUrl);
        } else {
          result = {
            status: 404,
            body: { error: "not found" },
          };
        }

        response.writeHead(result.status, {
          "Content-Type": "application/json",
        });
        response.end(JSON.stringify(result.body, null, 2));
      });
    }

    const command = process.argv[2];

    if (command === "serve") {
      createServer().listen(PORT, () => {
        console.log(`Issues API listening on http://localhost:${PORT}`);
      });
    } else if (command === "test") {
      test("default request returns the first page", () => {
        const result = getIssues("/issues");

        assert.equal(result.status, 200);
        assert.deepEqual(
          result.body.issues.map((issue) => issue.id),
          [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
        );
      });

      test("page numbers must be positive integers", () => {
        assert.equal(getIssues("/issues?page=0").status, 400);
      });
    } else {
      console.error("Usage: node issues-api.js <serve|test>");
      process.exitCode = 1;
    }
    ```

1. 运行现有测试：

    ```bash
    node issues-api.js test
    ```

两项测试均通过。通过的测试套件是有用的证据，但它并不能证明未经测试的行为是正确的。

1. 提交起点，以便您可以从示例代码中区分 agent 的更改：

    ```bash
    git add issues-api.js
    git commit -m "Add pagination bug sample"
    ```

## 重现该错误

错误报告说：

> 翻阅结果时，一些问题会消失。 `GET /issues` 返回问题 1-10，但 `GET /issues?page=2` 返回问题 21-30。第二页应返回问题 11-20。省略 `page` 的行为应与请求 `page=1` 相同。

在要求 agent 更改代码之前验证报告。

1. 打开终端（`kb(workbench.action.terminal.toggleTerminal)`）并启动API：

    ```bash
    node issues-api.js serve
    ```

1. 打开另一个终端并请求默认页面：

    ```bash
    node -e "fetch('http://localhost:3000/issues').then(r => r.json()).then(d => console.log(d.issues.map(i => i.id)))"
    ```

响应包含预期的 ID：

    ```text
    [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
    ```

1. 请求第二页：

    ```bash
    node -e "fetch('http://localhost:3000/issues?page=2').then(r => r.json()).then(d => console.log(d.issues.map(i => i.id)))"
    ```

回复证实了所报告的差距：

    ```text
    [ 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 ]
    ```

1. 在运行API的终端中，按`kbstyle(Ctrl+C)`停止它。

您现在已经有了可重现的症状和预期的结果。提供两者有助于 agent 调查实际缺陷，而不是猜测“分页损坏”的含义。

## 请agent调查

从调查开始，而不是立即要求修复。这让您有机会在编辑项目之前回顾 agent 的理解。

1. 打开 {% data variables.copilot.chat_view %} (`kb(workbench.action.chat.open)`) 并从 agent 选择器中选择 **Agent**。

1. 输入以下提示：

    ```prompt
    Investigate this pagination bug. Do not edit files yet.

    GET /issues returns IDs 1-10, but GET /issues?page=2 returns
    IDs 21-30. The API uses one-based page numbers, so page 2
    should return IDs 11-20. Omitting page should match page=1.

    Run the existing tests, trace the request through the relevant
    code, explain the cause with file references, and propose a
    regression test.
    ```

1. 在继续之前查看解释。令人信服的诊断应该：

    * 考虑两个观察到的响应。
    * 将 API 从一开始的页码连接到从零开始的数组偏移量。
    * 确定现有测试尽管存在缺陷但仍能通过的原因。
    * 避免对响应格式或示例数据提出不相关的更改。

如果解释没有考虑到观察到的 ID，请提供命令输出并要求 agent 进一步调查。

## 添加失败的回归测试

回归测试证明测试套件可以检测到报告的错误。它还为 agent 提供了修复的具体成功条件。

1. 要求 agent 添加测试而不更改 API 实现：

    ```prompt
    Add regression coverage in the existing test block, but do not
    change getIssues or other production code yet.

    Verify that:
    - /issues?page=1 returns the same issue IDs as /issues
    - /issues?page=2 returns IDs 11-20

    Run the tests and confirm that the new coverage fails because the
    returned IDs are wrong, not because of a test setup error.
    ```

1. 检查更改的测试代码和测试输出。新断言应该因不同的预期问题 ID 和实际问题 ID 而失败。仅检查 HTTP 状态的测试不会重现所报告的问题。

    <!-- TODO: Add a screenshot showing the regression tests failing with the expected and actual issue IDs. -->

如果agent也改变了实现，[恢复之前的检查点](<https://github.com/docs/agents/run/review-code-edits.md#restore-a-checkpoint>)并以更窄的范围重复请求。

## 实施并测试修复

失败的测试确定了预期的行为后，要求 agent 纠正实施。

1. 输入以下提示：

    ```prompt
    Fix the root cause of the pagination bug with the smallest
    production-code change. Keep the public API one-based, preserve
    the existing response shape and validation, and do not add
    dependencies. Run the complete test suite after the change.
    ```

1. 确认回归覆盖率和原始测试通过。

1. 查看实现如何处理页码。正确的解决方案在 API 边界处使用从一开始的值，并在切片数组时将它们转换为从零开始的偏移量。如果等效实现保留相同的 API 合约并通过测试，则它们是有效的。

如果测试仍然失败，请向 agent 提供完全失败的输出，并要求其在进行另一次更改之前解释剩余的不匹配情况。

## 验证页面边界

回归测试涵盖了报告的案例。检查相邻和边界情况，以确保修复不仅仅适用于第二页。

1. 再次启动API：

    ```bash
    node issues-api.js serve
    ```

1. 在另一个终端中，请求第一、第二、第四和第五页：

    ```bash
    node -e "(async () => { for (const p of [1, 2, 4, 5]) { const d = await fetch('http://localhost:3000/issues?page=' + p).then(r => r.json()); console.log('page ' + p + ':', d.issues.map(i => i.id)); } })()"
    ```

验证以下输出：

    ```text
    page 1: [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
    page 2: [ 11, 12, 13, 14, 15, 16, 17, 18, 19, 20 ]
    page 4: [ 31, 32, 33, 34, 35 ]
    page 5: []
    ```

响应没有间隙或重复的 ID，保留最终的部分页面，数据之外的页面为空。

1. 按 `kbstyle(Ctrl+C)` 停止 API。

## 查看更改

在提交 agent 的工作之前，请检查代码和支持它的证据。

1. 打开 ** 源代码控制 ** 视图并选择 `issues-api.js` 以检查差异。

1. 检查：

    * 回归测试代表记录的 API 合约，而不是实现细节。
    * 您观察到回归测试在修复之前失败，之后通过。
    * 生产更改解决了偏移计算问题，而无需更改响应形状、验证或样本数据。
    * agent 没有添加依赖项或不相关的重构。
    * 完整的测试套件和手动边界检查通过。

    <!-- TODO: Add a screenshot showing the focused production-code and regression-test changes in the Source Control diff. -->

1. 如果 diff 包含不相关的更改，请要求 agent 恢复它们并重新运行测试。

1. 提交经过验证的修复：

    ```bash
    git add issues-api.js
    git commit -m "Fix issue pagination"
    ```

您通过在更改生产代码之前建立预期行为修复了可重现的 API 缺陷。此工作流程为您提供诊断证据，通过回归测试保护行为，并保持最终更改的重点和可审查性。

## 相关资源

* 探索更多方法 [测试用 GitHub Copilot](<https://github.com/docs/agents/guides/test-code-with-ai.md>).
* 查看如何[检查、修改和恢复agent更改](<https://github.com/docs/agents/run/review-code-edits.md>)。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Tutorial: Fix an API bug with an agent

Most software work starts with existing code and a bug report, not an empty project. In this tutorial, you use an AI agent in {% data variables.product.prodname_vscode %} to investigate why a paginated API skips records, add a regression test that proves the bug, implement a focused fix, and verify the result.

> [!NOTE]
> The sample API fits in one file and uses only built-in Node.js modules, so you can focus on the debugging workflow. The same process applies when request handling, application logic, and tests are spread across a larger project.

## Prerequisites

To complete this tutorial, you need:

* [Download and install {% data variables.product.prodname_vscode %}](<https://github.com/download>).
* [Enable AI features in {% data variables.product.prodname_vscode_shortname %}](<https://github.com/docs/getstarted/overview.md#enable-ai-features>).
* Install [Node.js 22 or later](<https://nodejs.org/en/download>).
* Install [Git](<https://git-scm.com/downloads>) and configure it for commits.

## Set up the sample API

Start with an API that has working tests but contains a pagination bug.

1. Create a folder named `pagination-bug` and initialize a Git repository in it:

    ```bash
    mkdir pagination-bug
    cd pagination-bug
    git init
    ```

1. In {% data variables.product.prodname_vscode_shortname %}, select **File** > **Open Folder**, and then open the `pagination-bug` folder.

1. Create a file named `issues-api.js` and paste in the following code:

    ```javascript
    const assert = require("node:assert/strict");
    const http = require("node:http");
    const { test } = require("node:test");

    const PORT = 3000;
    const PAGE_SIZE = 10;
    const issues = Array.from({ length: 35 }, (_, index) => ({
      id: index + 1,
      title: `Issue ${index + 1}`,
    }));

    function getIssues(requestUrl) {
      const url = new URL(requestUrl, `http://localhost:${PORT}`);
      const pageParameter = url.searchParams.get("page");
      const page = pageParameter === null ? 0 : Number(pageParameter);

      if (
        pageParameter !== null &&
        (!Number.isInteger(page) || page < 1)
      ) {
        return {
          status: 400,
          body: { error: "page must be a positive integer" },
        };
      }

      const start = page * PAGE_SIZE;
      const requestedPage = pageParameter === null ? 1 : page;

      return {
        status: 200,
        body: {
          page: requestedPage,
          pageSize: PAGE_SIZE,
          total: issues.length,
          issues: issues.slice(start, start + PAGE_SIZE),
        },
      };
    }

    function createServer() {
      return http.createServer((request, response) => {
        const requestUrl = request.url ?? "/";
        const url = new URL(requestUrl, `http://localhost:${PORT}`);

        let result;
        if (request.method === "GET" && url.pathname === "/issues") {
          result = getIssues(requestUrl);
        } else {
          result = {
            status: 404,
            body: { error: "not found" },
          };
        }

        response.writeHead(result.status, {
          "Content-Type": "application/json",
        });
        response.end(JSON.stringify(result.body, null, 2));
      });
    }

    const command = process.argv[2];

    if (command === "serve") {
      createServer().listen(PORT, () => {
        console.log(`Issues API listening on http://localhost:${PORT}`);
      });
    } else if (command === "test") {
      test("default request returns the first page", () => {
        const result = getIssues("/issues");

        assert.equal(result.status, 200);
        assert.deepEqual(
          result.body.issues.map((issue) => issue.id),
          [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
        );
      });

      test("page numbers must be positive integers", () => {
        assert.equal(getIssues("/issues?page=0").status, 400);
      });
    } else {
      console.error("Usage: node issues-api.js <serve|test>");
      process.exitCode = 1;
    }
    ```

1. Run the existing tests:

    ```bash
    node issues-api.js test
    ```

    Both tests pass. A passing test suite is useful evidence, but it doesn't prove that untested behavior is correct.

1. Commit the starting point so that you can distinguish the agent's changes from the sample code:

    ```bash
    git add issues-api.js
    git commit -m "Add pagination bug sample"
    ```

## Reproduce the bug

The bug report says:

> Some issues disappear when paging through results. `GET /issues` returns issues 1-10, but `GET /issues?page=2` returns issues 21-30. Page two should return issues 11-20. Omitting `page` should behave the same as requesting `page=1`.

Verify the report before asking the agent to change the code.

1. Open the terminal (`kb(workbench.action.terminal.toggleTerminal)`) and start the API:

    ```bash
    node issues-api.js serve
    ```

1. Open another terminal and request the default page:

    ```bash
    node -e "fetch('http://localhost:3000/issues').then(r => r.json()).then(d => console.log(d.issues.map(i => i.id)))"
    ```

    The response contains the expected IDs:

    ```text
    [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
    ```

1. Request page two:

    ```bash
    node -e "fetch('http://localhost:3000/issues?page=2').then(r => r.json()).then(d => console.log(d.issues.map(i => i.id)))"
    ```

    The response confirms the reported gap:

    ```text
    [ 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 ]
    ```

1. In the terminal that runs the API, press `kbstyle(Ctrl+C)` to stop it.

You now have a reproducible symptom and an expected result. Providing both helps the agent investigate the actual defect instead of guessing what "pagination is broken" means.

## Ask the agent to investigate

Start with investigation instead of immediately asking for a fix. This gives you a chance to review the agent's understanding before it edits the project.

1. Open the {% data variables.copilot.chat_view %} (`kb(workbench.action.chat.open)`) and select **Agent** from the agent picker.

1. Enter the following prompt:

    ```prompt
    Investigate this pagination bug. Do not edit files yet.

    GET /issues returns IDs 1-10, but GET /issues?page=2 returns
    IDs 21-30. The API uses one-based page numbers, so page 2
    should return IDs 11-20. Omitting page should match page=1.

    Run the existing tests, trace the request through the relevant
    code, explain the cause with file references, and propose a
    regression test.
    ```

1. Review the explanation before continuing. A convincing diagnosis should:

    * Account for both observed responses.
    * Connect the API's one-based page numbers to the zero-based array offset.
    * Identify why the existing tests pass despite the defect.
    * Avoid proposing unrelated changes to the response format or sample data.

If the explanation doesn't account for the observed IDs, provide the command output and ask the agent to investigate further.

## Add a failing regression test

A regression test proves that the test suite can detect the reported bug. It also gives the agent a concrete success condition for the fix.

1. Ask the agent to add the test without changing the API implementation:

    ```prompt
    Add regression coverage in the existing test block, but do not
    change getIssues or other production code yet.

    Verify that:
    - /issues?page=1 returns the same issue IDs as /issues
    - /issues?page=2 returns IDs 11-20

    Run the tests and confirm that the new coverage fails because the
    returned IDs are wrong, not because of a test setup error.
    ```

1. Inspect the changed test code and the test output. The new assertions should fail with different expected and actual issue IDs. A test that only checks the HTTP status doesn't reproduce the reported problem.

    <!-- TODO: Add a screenshot showing the regression tests failing with the expected and actual issue IDs. -->

If the agent also changes the implementation, [restore the previous checkpoint](<https://github.com/docs/agents/run/review-code-edits.md#restore-a-checkpoint>) and repeat the request with a narrower scope.

## Implement and test the fix

After the failing test establishes the expected behavior, ask the agent to correct the implementation.

1. Enter the following prompt:

    ```prompt
    Fix the root cause of the pagination bug with the smallest
    production-code change. Keep the public API one-based, preserve
    the existing response shape and validation, and do not add
    dependencies. Run the complete test suite after the change.
    ```

1. Confirm that the regression coverage and the original tests pass.

1. Review how the implementation handles page numbers. A correct solution uses one-based values at the API boundary and converts them to a zero-based offset when slicing the array. Equivalent implementations are valid if they preserve the same API contract and pass the tests.

If a test still fails, give the agent the complete failure output and ask it to explain the remaining mismatch before making another change.

## Verify page boundaries

The regression test covers the reported case. Check adjacent and boundary cases to make sure the fix doesn't only work for page two.

1. Start the API again:

    ```bash
    node issues-api.js serve
    ```

1. In another terminal, request the first, second, fourth, and fifth pages:

    ```bash
    node -e "(async () => { for (const p of [1, 2, 4, 5]) { const d = await fetch('http://localhost:3000/issues?page=' + p).then(r => r.json()); console.log('page ' + p + ':', d.issues.map(i => i.id)); } })()"
    ```

    Verify the following output:

    ```text
    page 1: [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
    page 2: [ 11, 12, 13, 14, 15, 16, 17, 18, 19, 20 ]
    page 4: [ 31, 32, 33, 34, 35 ]
    page 5: []
    ```

    The responses have no gaps or duplicate IDs, the final partial page is preserved, and a page beyond the data is empty.

1. Stop the API by pressing `kbstyle(Ctrl+C)`.

## Review the change

Before you commit an agent's work, inspect both the code and the evidence that supports it.

1. Open the **Source Control** view and select `issues-api.js` to inspect the diff.

1. Check that:

    * The regression test represents the documented API contract rather than the implementation details.
    * You observed the regression test fail before the fix and pass afterward.
    * The production change addresses the offset calculation without changing the response shape, validation, or sample data.
    * The agent didn't add dependencies or unrelated refactoring.
    * The complete test suite and manual boundary checks pass.

    <!-- TODO: Add a screenshot showing the focused production-code and regression-test changes in the Source Control diff. -->

1. If the diff contains unrelated changes, ask the agent to revert them and rerun the tests.

1. Commit the verified fix:

    ```bash
    git add issues-api.js
    git commit -m "Fix issue pagination"
    ```

You fixed a reproducible API defect by establishing the expected behavior before changing production code. This workflow gives you evidence for the diagnosis, protects the behavior with a regression test, and keeps the final change focused and reviewable.

## Related resources

* Explore more ways to [test with GitHub Copilot](<https://github.com/docs/agents/guides/test-code-with-ai.md>).
* Review how to [inspect, revise, and revert agent changes](<https://github.com/docs/agents/run/review-code-edits.md>).


</details>
