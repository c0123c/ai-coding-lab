# 在 GitLab CI 与 GitLab Duo 中使用 OpenCode

工程指南。分别介绍 CI/CD 和 Duo 两种接入，给出组件、凭据、环境和任务示例，对应 GitLab 仓库中的自动开发操作。

- 来源：OpenCode
- 资料类型：工程指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-01-15](<https://github.com/anomalyco/opencode/commit/63176bb049d00643c65cd7dcd836403f8250022e>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：两种界面的功能、权限和配置不同。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/gitlab.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/在 GitLab CI 与 GitLab Duo 中使用 OpenCode.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: GitLab
description: Use OpenCode in GitLab issues and merge requests.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 通过 GitLab CI/CD 管道或 GitLab Duo 与您的 GitLab 工作流程集成。

在这两种情况下，OpenCode 都将在您的 GitLab 跑步者上运行。

---

## GitLab CI

OpenCode 在常规 GitLab 管道中工作。您可以将其作为 [CI 组件](<https://docs.gitlab.com/ee/ci/components/>) 构建到管道中

在这里，我们使用社区创建的 CI/CD 组件来表示 OpenCode — [nagyv/gitlab-opencode](<https://gitlab.com/nagyv/gitlab-opencode>)。

---

### 特点

- **每个作业使用自定义配置**：使用自定义配置目录配置OpenCode，例如`./config/#custom-directory`以启用或禁用每个OpenCode调用的功能。
- **最小设置**：CI组件在后台设置OpenCode，您只需创建OpenCode配置和初始提示。
- **灵活**：CI组件支持多个输入来自定义其行为

---

### 设置

1. 将您的 OpenCode 身份验证 JSON 作为文件类型 CI 环境变量存储在 ** 设置 ** > **CI/CD** > ** 变量 ** 下。确保将它们标记为“屏蔽和隐藏”。
2. 将以下内容添加到您的 `.gitlab-ci.yml` 文件中。

**代码示例：** `.gitlab-ci.yml`

```yaml
include:
  - component: $CI_SERVER_FQDN/nagyv/gitlab-opencode/opencode@2
    inputs:
      config_dir: ${CI_PROJECT_DIR}/opencode-config
      auth_json: $OPENCODE_AUTH_JSON # The variable name for your OpenCode authentication JSON
      command: optional-custom-command
      message: "Your prompt here"
```

有关更多输入和用例 [，请查看此组件的文档 ](<https://gitlab.com/explore/catalog/nagyv/gitlab-opencode>)。

---

## GitLab 双人

OpenCode 与您的 GitLab 工作流程集成。
在评论中提及 `@opencode`，OpenCode 将在您的 GitLab CI 管道中执行任务。

---

### 特点

- **分类问题**：请OpenCode调查问题并向您解释。
- **修复并实施**：要求OpenCode修复问题或实施功能。
它将创建一个新分支并提出包含更改的合并请求。
- **安全**：OpenCode在您的GitLab跑步者上运行。

---

### 设置

OpenCode 在您的 GitLab CI/CD 管道中运行，以下是您需要进行设置的内容：

**提示：**

查看 [**GitLab 文档 **](<https://docs.gitlab.com/user/duo_agent_platform/agent_assistant/>) 以获取最新说明。

1.  配置您的 GitLab 环境
2.  设置CI/CD
3.  获取 AI 模型提供商 API 密钥
4.  创建服务帐户
5.  配置CI/CD变量
6.  创建一个流配置文件，这是一个示例：

流量配置

```yaml
image: node:22-slim
commands:
  - echo "Installing opencode"
  - npm install --global opencode-ai
  - echo "Installing glab"
  - export GITLAB_TOKEN=$GITLAB_TOKEN_OPENCODE
  - apt-get update --quiet && apt-get install --yes curl wget gpg git && rm --recursive --force /var/lib/apt/lists/*
  - curl --silent --show-error --location "https://raw.githubusercontent.com/upciti/wakemeops/main/assets/install_repository" | bash
  - apt-get install --yes glab
  - echo "Configuring glab"
  - echo $GITLAB_HOST
  - echo "Creating OpenCode auth configuration"
  - mkdir --parents ~/.local/share/opencode
  - |
    cat > ~/.local/share/opencode/auth.json << EOF
    {
      "anthropic": {
        "type": "api",
        "key": "$ANTHROPIC_API_KEY"
      }
    }
    EOF
  - echo "Configuring git"
  - git config --global user.email "opencode@gitlab.com"
  - git config --global user.name "OpenCode"
  - echo "Testing glab"
  - glab issue list
  - echo "Running OpenCode"
  - |
    opencode run "
    You are an AI assistant helping with GitLab operations.

    Context: $AI_FLOW_CONTEXT
    Task: $AI_FLOW_INPUT
    Event: $AI_FLOW_EVENT

    Please execute the requested task using the available GitLab tools.
    Be thorough in your analysis and provide clear explanations.

    <important>
    Please use the glab CLI to access data from GitLab. The glab CLI has already been authenticated. You can run the corresponding commands.

    If you are asked to summarize an MR or issue or asked to provide more information then please post back a note to the MR/Issue so that the user can see it.
    You don't need to commit or push up changes, those will be done automatically based on the file changes you make.
    </important>
    "
  - git checkout --branch $CI_WORKLOAD_REF origin/$CI_WORKLOAD_REF
  - echo "Checking for git changes and pushing if any exist"
  - |
    if ! git diff --quiet || ! git diff --cached --quiet || [ --not --zero "$(git ls-files --others --exclude-standard)" ]; then
      echo "Git changes detected, adding and pushing..."
      git add .
      if git diff --cached --quiet; then
        echo "No staged changes to commit"
      else
        echo "Committing changes to branch: $CI_WORKLOAD_REF"
        git commit --message "Codex changes"
        echo "Pushing changes up to $CI_WORKLOAD_REF"
        git push https://gitlab-ci-token:$GITLAB_TOKEN@$GITLAB_HOST/gl-demo-ultimate-dev-ai-epic-17570/test-java-project.git $CI_WORKLOAD_REF
        echo "Changes successfully pushed"
      fi
    else
      echo "No git changes detected, skipping push"
    fi
variables:
  - ANTHROPIC_API_KEY
  - GITLAB_TOKEN_OPENCODE
  - GITLAB_HOST
```

您可以参考[GitLab CLI agents文档](<https://docs.gitlab.com/user/duo_agent_platform/agent_assistant/>)了解详细说明。

---

### 示例

以下是如何在 GitLab 中使用 OpenCode 的一些示例。

**提示：**

您可以配置使用与 `@opencode` 不同的触发短语。

- **解释一个问题**

在 GitLab 问题中添加此评论。

```
@opencode explain this issue
```

OpenCode将阅读该问题并回复并提供清晰的解释。

- **修复问题**

在 GitLab 问题中，说：

```
@opencode fix this
```

OpenCode 将创建一个新分支，实施更改，并打开包含更改的合并请求。

- **审查合并请求**

对 GitLab 合并请求留下以下评论。

```
@opencode review this merge request
```

OpenCode 将审核合并请求并提供反馈。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode integrates with your GitLab workflow through your GitLab CI/CD pipeline or with GitLab Duo.

In both cases, OpenCode will run on your GitLab runners.

---

## GitLab CI

OpenCode works in a regular GitLab pipeline. You can build it into a pipeline as a [CI component](<https://docs.gitlab.com/ee/ci/components/>)

Here we are using a community-created CI/CD component for OpenCode — [nagyv/gitlab-opencode](<https://gitlab.com/nagyv/gitlab-opencode>).

---

### Features

- **Use custom configuration per job**: Configure OpenCode with a custom configuration directory, for example `./config/#custom-directory` to enable or disable functionality per OpenCode invocation.
- **Minimal setup**: The CI component sets up OpenCode in the background, you only need to create the OpenCode configuration and the initial prompt.
- **Flexible**: The CI component supports several inputs for customizing its behavior

---

### Setup

1. Store your OpenCode authentication JSON as a File type CI environment variables under **Settings** > **CI/CD** > **Variables**. Make sure to mark them as "Masked and hidden".
2. Add the following to your `.gitlab-ci.yml` file.

**代码示例：** `.gitlab-ci.yml`

```yaml
include:
  - component: $CI_SERVER_FQDN/nagyv/gitlab-opencode/opencode@2
    inputs:
      config_dir: ${CI_PROJECT_DIR}/opencode-config
      auth_json: $OPENCODE_AUTH_JSON # The variable name for your OpenCode authentication JSON
      command: optional-custom-command
      message: "Your prompt here"
```

For more inputs and use cases [check out the docs](<https://gitlab.com/explore/catalog/nagyv/gitlab-opencode>) for this component.

---

## GitLab Duo

OpenCode integrates with your GitLab workflow.
Mention `@opencode` in a comment, and OpenCode will execute tasks within your GitLab CI pipeline.

---

### Features

- **Triage issues**: Ask OpenCode to look into an issue and explain it to you.
- **Fix and implement**: Ask OpenCode to fix an issue or implement a feature.
  It will create a new branch and raise a merge request with the changes.
- **Secure**: OpenCode runs on your GitLab runners.

---

### Setup

OpenCode runs in your GitLab CI/CD pipeline, here's what you'll need to set it up:

**提示：**

Check out the [**GitLab docs**](<https://docs.gitlab.com/user/duo_agent_platform/agent_assistant/>) for up to date instructions.

1.  Configure your GitLab environment
2.  Set up CI/CD
3.  Get an AI model provider API key
4.  Create a service account
5.  Configure CI/CD variables
6.  Create a flow config file, here's an example:

Flow configuration

```yaml
image: node:22-slim
commands:
  - echo "Installing opencode"
  - npm install --global opencode-ai
  - echo "Installing glab"
  - export GITLAB_TOKEN=$GITLAB_TOKEN_OPENCODE
  - apt-get update --quiet && apt-get install --yes curl wget gpg git && rm --recursive --force /var/lib/apt/lists/*
  - curl --silent --show-error --location "https://raw.githubusercontent.com/upciti/wakemeops/main/assets/install_repository" | bash
  - apt-get install --yes glab
  - echo "Configuring glab"
  - echo $GITLAB_HOST
  - echo "Creating OpenCode auth configuration"
  - mkdir --parents ~/.local/share/opencode
  - |
    cat > ~/.local/share/opencode/auth.json << EOF
    {
      "anthropic": {
        "type": "api",
        "key": "$ANTHROPIC_API_KEY"
      }
    }
    EOF
  - echo "Configuring git"
  - git config --global user.email "opencode@gitlab.com"
  - git config --global user.name "OpenCode"
  - echo "Testing glab"
  - glab issue list
  - echo "Running OpenCode"
  - |
    opencode run "
    You are an AI assistant helping with GitLab operations.

    Context: $AI_FLOW_CONTEXT
    Task: $AI_FLOW_INPUT
    Event: $AI_FLOW_EVENT

    Please execute the requested task using the available GitLab tools.
    Be thorough in your analysis and provide clear explanations.

    <important>
    Please use the glab CLI to access data from GitLab. The glab CLI has already been authenticated. You can run the corresponding commands.

    If you are asked to summarize an MR or issue or asked to provide more information then please post back a note to the MR/Issue so that the user can see it.
    You don't need to commit or push up changes, those will be done automatically based on the file changes you make.
    </important>
    "
  - git checkout --branch $CI_WORKLOAD_REF origin/$CI_WORKLOAD_REF
  - echo "Checking for git changes and pushing if any exist"
  - |
    if ! git diff --quiet || ! git diff --cached --quiet || [ --not --zero "$(git ls-files --others --exclude-standard)" ]; then
      echo "Git changes detected, adding and pushing..."
      git add .
      if git diff --cached --quiet; then
        echo "No staged changes to commit"
      else
        echo "Committing changes to branch: $CI_WORKLOAD_REF"
        git commit --message "Codex changes"
        echo "Pushing changes up to $CI_WORKLOAD_REF"
        git push https://gitlab-ci-token:$GITLAB_TOKEN@$GITLAB_HOST/gl-demo-ultimate-dev-ai-epic-17570/test-java-project.git $CI_WORKLOAD_REF
        echo "Changes successfully pushed"
      fi
    else
      echo "No git changes detected, skipping push"
    fi
variables:
  - ANTHROPIC_API_KEY
  - GITLAB_TOKEN_OPENCODE
  - GITLAB_HOST
```

You can refer to the [GitLab CLI agents docs](<https://docs.gitlab.com/user/duo_agent_platform/agent_assistant/>) for detailed instructions.

---

### Examples

Here are some examples of how you can use OpenCode in GitLab.

**提示：**

You can configure to use a different trigger phrase than `@opencode`.

- **Explain an issue**

  Add this comment in a GitLab issue.

```
@opencode explain this issue
```

  OpenCode will read the issue and reply with a clear explanation.

- **Fix an issue**

  In a GitLab issue, say:

```
@opencode fix this
```

  OpenCode will create a new branch, implement the changes, and open a merge request with the changes.

- **Review merge requests**

  Leave the following comment on a GitLab merge request.

```
@opencode review this merge request
```

  OpenCode will review the merge request and provide feedback.


</details>
