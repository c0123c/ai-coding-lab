# 通过 API 管理 Copilot 云端任务

说明认证、创建和查询任务、检查状态，以及通过 Issue、REST 和 GraphQL 接口分派工作的方式。

- 来源：GitHub／Copilot
- 资料类型：文档源码（含未展开模板）
- 存档版本：`be38ec5d78e2`。
- 文档时间：源码提交（存档版）：[2026-10-05](<https://github.com/github/docs/commit/4780141748da1eac03fa8ac65c96e2bb550bec96>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：中文源码译文，由 Google 翻译；源码中的模板未展开，不等同于官网渲染后的完整文章。代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/github/docs/blob/main/content/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-via-the-api.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/Copilot/通过 API 管理 Copilot 云端任务.md>) · [返回资料总览](<../../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Using Copilot cloud agent via the API
shortTitle: Use cloud agent via the API
intro: 'You can start and manage {% data variables.copilot.copilot_cloud_agent %} tasks programmatically using the REST API.'
product: '{% data reusables.gated-features.copilot-cloud-agent %}<br><a href="https://github.com/features/copilot/plans?ref_product=copilot&ref_type=purchase&ref_style=button" target="_blank" class="btn btn-primary mt-3 mr-3 no-underline"><span>Sign up for {% data variables.product.prodname_copilot_short %}</span> {% octicon "link-external" height:16 %}</a>'
versions:
  feature: copilot
contentType: how-tos
category:
  - Integrate Copilot with your tools
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

您可以使用 agent 任务 API 将 {% data variables.copilot.copilot_cloud_agent_short %} 集成到您自己的工具和工作流程中。例如，您可以启动新任务、列出现有任务或检查任务的状态。

此外，您还可以使用问题的REST和GraphQL API将问题分配给{% data variables.product.prodname_copilot_short %}。

## 使用 agent 任务 API

> [！NOTE]
> agent 任务 API 位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。

### 认证

agent 任务 API 仅支持用户到服务器令牌。您可以使用 {% data variables.product.pat_generic %}、{% data variables.product.prodname_oauth_app %} 令牌或 {% data variables.product.prodname_github_app %} 用户到服务器令牌进行身份验证。

不支持服务器到服务器令牌，例如 {% data variables.product.prodname_github_app %} 安装访问令牌。

### 通过API启动任务

要启动新的 {% data variables.copilot.copilot_cloud_agent_short %} 任务，请向 `/agents/repos/{owner}/{repo}/tasks` 发送 `POST` 请求。唯一需要的参数是`prompt`，这是agent的提示符。

```shell copy
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks \
  -d '{
    "prompt": "Fix the login button on the homepage",
    "base_ref": "main"
  }'
```

替换以下占位符值：

* `YOUR-TOKEN`：{% data variables.product.pat_generic %} 或 {% data variables.product.prodname_github_app %} 用户到服务器令牌。
* `OWNER`：存储库的帐户所有者。
* `REPO`：存储库的名称。

您还可以在请求正文中包含以下可选参数：

* `base_ref`：新分支和拉取请求的基础分支。
* `model`：用于任务的 AI 模型。如果省略，则使用 {% data variables.copilot.copilot_auto_model_selection_short %}。有关支持型号的更多信息，请参阅[AUTOTITLE](<https://github.com/rest/agent-tasks/agent-tasks>)。
* `create_pull_request`：一个布尔值，确定是否为任务创建拉取请求。

### 列出任务

您可以列出特定存储库或您有权访问的所有存储库的任务。

列出特定存储库的任务：

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks
```

要列出所有存储库中的任务：

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/tasks
```

### 检查任务的状态

要检查特定任务的状态，请发送带有任务 ID 的 `GET` 请求：

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks/TASK-ID
```

将 `TASK-ID` 替换为要检查的任务的 ID。您可以在创建任务或列出任务时从响应中获取此ID。响应包括任务的当前 `state`，可以是以下之一：`queued`、`in_progress`、`completed`、`failed`、`idle`、`waiting_for_user`、 `timed_out`或`cancelled`。


## 使用问题API

> [！NOTE]
> 此功能位于 {% data variables.release-phases.public_preview %} 中，可能会发生变化。

您可以使用 GraphQL API 或 REST API 将问题分配给 {% data variables.product.prodname_copilot_short %}。两个 API 都支持可选的 Agent 分配输入来自定义任务：

|GraphQL参数|REST参数|描述|
| --- | --- | --- |
| `targetRepositoryId` | `target_repo` |{% data variables.product.prodname_copilot_short %} 将工作的存储库|
| `baseRef` | `base_branch` |{% data variables.product.prodname_copilot_short %} 将分支的分支|
| `customInstructions` | `custom_instructions` |{% data variables.product.prodname_copilot_short %} 的附加说明|
| `customAgent` | `custom_agent` |用于任务的 {% data variables.copilot.copilot_custom_agent_short %}|
| `model` | `model` |{% data variables.product.prodname_copilot_short %}使用的型号|

### 使用 GraphQL API

> [!NOTE]
> 您必须包含带有值 `issues_copilot_assignment_api_support` 和 `coding_agent_model_selection` 的 `GraphQL-Features` 标头。

您可以使用以下 GraphQL 突变将问题分配给 {% data variables.product.prodname_copilot_short %}：

* [`updateIssue`](<https://github.com/graphql/reference/issues#mutation-updateissue>)
* [`createIssue`](<https://github.com/graphql/reference/issues#mutation-createissue>)
* [`addAssigneesToAssignable`](<https://github.com/graphql/reference/issues#mutation-addassigneestoassignable>)
* [`replaceActorsForAssignable`](<https://github.com/graphql/reference/issues#mutation-replaceactorsforassignable>)

#### 创建并分配新问题

1. 确保您使用用户令牌（例如 {% data variables.product.pat_generic %} 或 {% data variables.product.prodname_github_app %} 用户到服务器令牌）对 API 进行身份验证。

   > [!NOTE]
   > 如果使用{% data variables.product.pat_v2 %}，则需要以下权限才能将{% data variables.product.prodname_copilot_short %}分配给问题：
   > * 对元数据的读取访问
   > * 对操作、内容、问题和拉取请求的读写访问权限
   >
   > 如果使用{% data variables.product.pat_v1 %}，则需要`repo`范围才能将{% data variables.product.prodname_copilot_short %}分配给问题。

1. 通过检查 GraphQL API 中存储库的 `suggestedActors` 是否包含 {% data variables.product.prodname_copilot_short %} 来验证存储库中是否启用了 {% data variables.copilot.copilot_cloud_agent %}。将 `octo-org` 替换为存储库所有者，将 `octo-repo` 替换为存储库名称。

    ```graphql copy
    query {
      repository(owner: "octo-org", name: "octo-repo") {
        suggestedActors(capabilities: [CAN_BE_ASSIGNED], first: 100) {
          nodes {
            login
            __typename

            ... on Bot {
              id
            }

            ... on User {
              id
            }
          }
        }
      }
    }
    ```

如果为用户和存储库启用了 {% data variables.copilot.copilot_cloud_agent %}，则查询返回的第一个节点将具有 `login` 值 `copilot-swe-agent`。

1. 记下此登录名的 `id` 值。

1. 获取要在其中创建问题的存储库的 GraphQL 全局 ID，将 `octo-org` 替换为存储库所有者，将 `octo-repo` 替换为存储库名称。

    ```graphql copy
    query {
      repository(owner: "octo-org", name: "octo-repo") {
        id
      }
    }
    ```

1. 使用 `createIssue` 突变创建问题。将 `REPOSITORY_ID` 替换为上一步返回的 ID，将 `BOT_ID` 替换为上一步返回的 ID。您可以选择包含 `agentAssignment` 输入来自定义任务。

    ```shell copy
    gh api graphql -f query='mutation {
      createIssue(input: {
        repositoryId: "REPOSITORY_ID",
        title: "Implement comprehensive unit tests",
        body: "DETAILS",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Add comprehensive test coverage",
          customAgent: "",
          model: ""
        }
      }) {
        issue {
          id
          title
          assignees(first: 10) {
            nodes {
              login
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

#### 分配现有问题

1. 确保您使用用户令牌（例如 {% data variables.product.pat_generic %} 或 {% data variables.product.prodname_github_app %} 用户到服务器令牌）对 API 进行身份验证。
1. 通过检查 GraphQL API 中存储库的 `suggestedActors` 是否包含 {% data variables.product.prodname_copilot_short %} 来验证存储库中是否启用了 {% data variables.copilot.copilot_cloud_agent %}。将 `octo-org` 替换为存储库所有者，将 `octo-repo` 替换为存储库名称。

    ```graphql copy
    query {
      repository(owner: "monalisa", name: "octocat") {
        suggestedActors(capabilities: [CAN_BE_ASSIGNED], first: 100) {
          nodes {
            login
            __typename

            ... on Bot {
              id
            }

            ... on User {
              id
            }
          }
        }
      }
    }
    ```

如果为用户和存储库启用了 {% data variables.copilot.copilot_cloud_agent %}，则查询返回的第一个节点将具有 `login` 值 `copilot-swe-agent`。

1. 获取要分配给 {% data variables.product.prodname_copilot_short %} 的问题的 GraphQL 全局 ID，将 `monalisa` 替换为存储库所有者，将 `octocat` 替换为名称，将 `9000` 替换为问题编号。

    ```graphql copy
    query {
      repository(owner: "monalisa", name: "octocat") {
        issue(number: 9000) {
          id
          title
        }
      }
    }
    ```

1. 使用 `replaceActorsForAssignable` 突变将现有问题分配给 {% data variables.product.prodname_copilot_short %}。将 `ISSUE_ID` 替换为上一步返回的 ID，将 `BOT_ID` 替换为之前步骤返回的 ID，将 `REPOSITORY_ID` 替换为存储库 ID。您可以选择包含 `agentAssignment` 输入来自定义任务。

    ```shell copy
    gh api graphql -f query='mutation {
      replaceActorsForAssignable(input: {
        assignableId: "ISSUE_ID",
        actorIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Fix the reported bug",
          customAgent: "",
          model: ""
        }
      }) {
        assignable {
          ... on Issue {
            id
            title
            assignees(first: 10) {
              nodes {
                login
              }
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

1. 或者，您可以使用 `updateIssue` 突变来更新现有问题并将其分配给 {% data variables.product.prodname_copilot_short %}。将 `ISSUE_ID` 替换为问题 ID，将 `BOT_ID` 替换为机器人 ID。

    ```shell copy
    gh api graphql -f query='mutation {
      updateIssue(input: {
        id: "ISSUE_ID",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Update feature implementation",
          customAgent: "",
          model: ""
        }
      }) {
        issue {
          id
          title
          assignees(first: 10) {
            nodes {
              login
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

1. 您还可以使用 `addAssigneesToAssignable` 突变将 {% data variables.product.prodname_copilot_short %} 添加到现有问题，同时保留其他受让人。将 `ISSUE_ID` 替换为问题 ID，将 `BOT_ID` 替换为机器人 ID。

    ```shell copy
    gh api graphql -f query='mutation {
      addAssigneesToAssignable(input: {
        assignableId: "ISSUE_ID",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Collaborate on this task",
          customAgent: "",
          model: ""
        }
      }) {
        assignable {
          ... on Issue {
            id
            title
            assignees(first: 10) {
              nodes {
                login
              }
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

### 使用 REST API

您可以使用以下 REST API 端点将问题分配给 {% data variables.product.prodname_copilot_short %}：

* [向问题添加受让人](<https://github.com/rest/issues/assignees#add-assignees-to-an-issue>)
* [创建问题](<https://github.com/rest/issues/issues#create-an-issue>)
* [更新问题](<https://github.com/rest/issues/issues#update-an-issue>)

#### 将受让人添加到现有问题

```shell copy
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues/ISSUE_NUMBER/assignees \
  --input - <<< '{
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```

#### 创建新问题

```shell copy
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues \
  --input - <<< '{
  "title": "Issue title",
  "body": "Issue description.",
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```

#### 更新现有问题

```shell copy
gh api \
  --method PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues/ISSUE_NUMBER \
  --input - <<< '{
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```


## 进一步阅读

* [AUTOTITLE](<https://github.com/rest/agent-tasks/agent-tasks>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>)


## 原文对照

<details>
<summary>展开完整原文</summary>

You can use the agent tasks API to integrate {% data variables.copilot.copilot_cloud_agent_short %} into your own tools and workflows. For example, you can start a new task, list existing tasks, or check the status of a task.

In addition, you can use the REST and GraphQL APIs for issues to assign issues to {% data variables.product.prodname_copilot_short %}.

## Using the agent tasks API

> [!NOTE]
> The agent tasks API is in {% data variables.release-phases.public_preview %} and subject to change.

### Authentication

The agent tasks API only supports user-to-server tokens. You can authenticate using a {% data variables.product.pat_generic %}, a {% data variables.product.prodname_oauth_app %} token or a {% data variables.product.prodname_github_app %} user-to-server token.

Server-to-server tokens, such as {% data variables.product.prodname_github_app %} installation access tokens, are not supported.

### Starting a task via the API

To start a new {% data variables.copilot.copilot_cloud_agent_short %} task, send a `POST` request to `/agents/repos/{owner}/{repo}/tasks`. The only required parameter is `prompt`, which is the prompt for the agent.

```shell copy
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks \
  -d '{
    "prompt": "Fix the login button on the homepage",
    "base_ref": "main"
  }'
```

Replace the following placeholder values:

* `YOUR-TOKEN`: A {% data variables.product.pat_generic %} or {% data variables.product.prodname_github_app %} user-to-server token.
* `OWNER`: The account owner of the repository.
* `REPO`: The name of the repository.

You can also include the following optional parameters in the request body:

* `base_ref`: The base branch for the new branch and pull request.
* `model`: The AI model to use for the task. If omitted, {% data variables.copilot.copilot_auto_model_selection_short %} is used. For more information about supported models, see [AUTOTITLE](<https://github.com/rest/agent-tasks/agent-tasks>).
* `create_pull_request`: A boolean that determines whether to create a pull request for the task.

### Listing tasks

You can list tasks for a specific repository or across all repositories you have access to.

To list tasks for a specific repository:

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks
```

To list your tasks across all repositories:

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/tasks
```

### Checking the status of a task

To check the status of a specific task, send a `GET` request with the task ID:

```shell copy
curl -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: {{ defaultRestApiVersion }}" \
  -H "Authorization: Bearer YOUR-TOKEN" \
  https://api.github.com/agents/repos/OWNER/REPO/tasks/TASK-ID
```

Replace `TASK-ID` with the ID of the task you want to check. You can get this ID from the response when you create a task or list tasks. The response includes the task's current `state`, which can be one of: `queued`, `in_progress`, `completed`, `failed`, `idle`, `waiting_for_user`, `timed_out`, or `cancelled`.


## Using the issues API

> [!NOTE]
> This feature is in {% data variables.release-phases.public_preview %} and subject to change.

You can assign issues to {% data variables.product.prodname_copilot_short %} using either the GraphQL API or the REST API. Both APIs support an optional Agent Assignment input to customize the task:

| GraphQL parameter | REST parameter | Description |
| --- | --- | --- |
| `targetRepositoryId` | `target_repo` | The repository where {% data variables.product.prodname_copilot_short %} will work |
| `baseRef` | `base_branch` | The branch that {% data variables.product.prodname_copilot_short %} will branch from |
| `customInstructions` | `custom_instructions` | Additional instructions for {% data variables.product.prodname_copilot_short %} |
| `customAgent` | `custom_agent` | A {% data variables.copilot.copilot_custom_agent_short %} to use for the task |
| `model` | `model` | The model for {% data variables.product.prodname_copilot_short %} to use |

### Using the GraphQL API

> [!NOTE]
> You must include the `GraphQL-Features` header with the values `issues_copilot_assignment_api_support` and `coding_agent_model_selection`.

You can use the following GraphQL mutations to assign issues to {% data variables.product.prodname_copilot_short %}:

* [`updateIssue`](<https://github.com/graphql/reference/issues#mutation-updateissue>)
* [`createIssue`](<https://github.com/graphql/reference/issues#mutation-createissue>)
* [`addAssigneesToAssignable`](<https://github.com/graphql/reference/issues#mutation-addassigneestoassignable>)
* [`replaceActorsForAssignable`](<https://github.com/graphql/reference/issues#mutation-replaceactorsforassignable>)

#### Creating and assigning a new issue

1. Make sure you're authenticating with the API using a user token, for example a {% data variables.product.pat_generic %} or a {% data variables.product.prodname_github_app %} user-to-server token.

   > [!NOTE]
   > If using a {% data variables.product.pat_v2 %}, it needs the following permissions to assign {% data variables.product.prodname_copilot_short %} to an issue:
   > * Read access to metadata
   > * Read and write access to actions, contents, issues and pull requests
   >
   > If using a {% data variables.product.pat_v1 %}, it needs the `repo` scope to assign {% data variables.product.prodname_copilot_short %} to an issue.

1. Verify that {% data variables.copilot.copilot_cloud_agent %} is enabled in the repository by checking if the repository's `suggestedActors` in the GraphQL API includes {% data variables.product.prodname_copilot_short %}. Replace `octo-org` with the repository owner, and `octo-repo` with the repository name.

    ```graphql copy
    query {
      repository(owner: "octo-org", name: "octo-repo") {
        suggestedActors(capabilities: [CAN_BE_ASSIGNED], first: 100) {
          nodes {
            login
            __typename

            ... on Bot {
              id
            }

            ... on User {
              id
            }
          }
        }
      }
    }
    ```

    If {% data variables.copilot.copilot_cloud_agent %} is enabled for the user and in the repository, the first node returned from the query will have the `login` value `copilot-swe-agent`.

1. Make a note of the `id` value of this login.

1. Fetch the GraphQL global ID of the repository you want to create the issue in, replacing `octo-org` with the repository owner, and `octo-repo` with the repository name.

    ```graphql copy
    query {
      repository(owner: "octo-org", name: "octo-repo") {
        id
      }
    }
    ```

1. Create the issue with the `createIssue` mutation. Replace `REPOSITORY_ID` with the ID returned from the previous step, and `BOT_ID` with the ID returned from the step before that. You can optionally include the `agentAssignment` input to customize the task.

    ```shell copy
    gh api graphql -f query='mutation {
      createIssue(input: {
        repositoryId: "REPOSITORY_ID",
        title: "Implement comprehensive unit tests",
        body: "DETAILS",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Add comprehensive test coverage",
          customAgent: "",
          model: ""
        }
      }) {
        issue {
          id
          title
          assignees(first: 10) {
            nodes {
              login
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

#### Assigning an existing issue

1. Make sure you're authenticating with the API using a user token, for example a {% data variables.product.pat_generic %} or a {% data variables.product.prodname_github_app %} user-to-server token.
1. Verify that {% data variables.copilot.copilot_cloud_agent %} is enabled in the repository by checking if the repository's `suggestedActors` in the GraphQL API includes {% data variables.product.prodname_copilot_short %}. Replace `octo-org` with the repository owner, and `octo-repo` with the repository name.

    ```graphql copy
    query {
      repository(owner: "monalisa", name: "octocat") {
        suggestedActors(capabilities: [CAN_BE_ASSIGNED], first: 100) {
          nodes {
            login
            __typename

            ... on Bot {
              id
            }

            ... on User {
              id
            }
          }
        }
      }
    }
    ```

    If {% data variables.copilot.copilot_cloud_agent %} is enabled for the user and in the repository, the first node returned from the query will have the `login` value `copilot-swe-agent`.

1. Fetch the GraphQL global ID of the issue you want to assign to {% data variables.product.prodname_copilot_short %}, replacing `monalisa` with the repository owner, `octocat` with the name and `9000` with the issue number.

    ```graphql copy
    query {
      repository(owner: "monalisa", name: "octocat") {
        issue(number: 9000) {
          id
          title
        }
      }
    }
    ```

1. Assign the existing issue to {% data variables.product.prodname_copilot_short %} using the `replaceActorsForAssignable` mutation. Replace `ISSUE_ID` with the ID returned from the previous step, `BOT_ID` with the ID returned from the step before that, and `REPOSITORY_ID` with the repository ID. You can optionally include the `agentAssignment` input to customize the task.

    ```shell copy
    gh api graphql -f query='mutation {
      replaceActorsForAssignable(input: {
        assignableId: "ISSUE_ID",
        actorIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Fix the reported bug",
          customAgent: "",
          model: ""
        }
      }) {
        assignable {
          ... on Issue {
            id
            title
            assignees(first: 10) {
              nodes {
                login
              }
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

1. Alternatively, you can use the `updateIssue` mutation to update an existing issue and assign it to {% data variables.product.prodname_copilot_short %}. Replace `ISSUE_ID` with the issue ID and `BOT_ID` with the bot ID.

    ```shell copy
    gh api graphql -f query='mutation {
      updateIssue(input: {
        id: "ISSUE_ID",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Update feature implementation",
          customAgent: "",
          model: ""
        }
      }) {
        issue {
          id
          title
          assignees(first: 10) {
            nodes {
              login
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

1. You can also use the `addAssigneesToAssignable` mutation to add {% data variables.product.prodname_copilot_short %} to an existing issue while keeping other assignees. Replace `ISSUE_ID` with the issue ID and `BOT_ID` with the bot ID.

    ```shell copy
    gh api graphql -f query='mutation {
      addAssigneesToAssignable(input: {
        assignableId: "ISSUE_ID",
        assigneeIds: ["BOT_ID"],
        agentAssignment: {
          targetRepositoryId: "REPOSITORY_ID",
          baseRef: "main",
          customInstructions: "Collaborate on this task",
          customAgent: "",
          model: ""
        }
      }) {
        assignable {
          ... on Issue {
            id
            title
            assignees(first: 10) {
              nodes {
                login
              }
            }
          }
        }
      }
    }' -H 'GraphQL-Features: issues_copilot_assignment_api_support,coding_agent_model_selection'
    ```

### Using the REST API

You can use the following REST API endpoints to assign issues to {% data variables.product.prodname_copilot_short %}:

* [Add assignees to an issue](<https://github.com/rest/issues/assignees#add-assignees-to-an-issue>)
* [Create an issue](<https://github.com/rest/issues/issues#create-an-issue>)
* [Update an issue](<https://github.com/rest/issues/issues#update-an-issue>)

#### Adding assignees to an existing issue

```shell copy
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues/ISSUE_NUMBER/assignees \
  --input - <<< '{
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```

#### Creating a new issue

```shell copy
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues \
  --input - <<< '{
  "title": "Issue title",
  "body": "Issue description.",
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```

#### Updating an existing issue

```shell copy
gh api \
  --method PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/issues/ISSUE_NUMBER \
  --input - <<< '{
  "assignees": ["copilot-swe-agent[bot]"],
  "agent_assignment": {
    "target_repo": "OWNER/REPO",
    "base_branch": "main",
    "custom_instructions": "",
    "custom_agent": "",
    "model": ""
  }
}'
```


## Further reading

* [AUTOTITLE](<https://github.com/rest/agent-tasks/agent-tasks>)
* [AUTOTITLE](<https://github.com/copilot/concepts/copilot-surfaces/copilot-on-github>)
* [AUTOTITLE](<https://github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions>)


</details>
