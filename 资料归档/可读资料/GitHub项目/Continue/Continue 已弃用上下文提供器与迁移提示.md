# Continue 已弃用上下文提供器与迁移提示

旧版参考。列出 Greptile、Commits、Jira、GitLab、数据库、网页等旧提供器的配置，正文说明不再积极维护并建议替代方案或 MCP。

- 来源：Continue
- 资料类型：旧版参考
- 存档版本：`5522c6f44ca0`。
- 文档时间：源码提交（存档版）：[2025-08-28](<https://github.com/continuedev/continue/commit/9f68638e324853d413f16b8ae6f14c57cada85cd>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：原文已标记 Deprecated，仅用于历史配置对照和迁移判断。

[原文发布页](<https://github.com/continuedev/continue/blob/5522c6f44ca0ac3528b37244818fbfa39b5af470/docs/reference/deprecated-context-providers.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/Continue/Continue 已弃用上下文提供器与迁移提示.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: "Context Providers (Deprecated)"
description: "These context providers have been deprecated and are no longer actively maintained. They remain documented here for reference purposes only."
noindex: true
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

**注意：**

本页记录的上下文提供程序已被弃用，并且不再积极维护。我们建议使用替代解决方案或 Model Context Protocol (MCP) 来实现类似的功能。这些提供程序可能会在未来版本中删除。

## 已弃用的提供程序

以下上下文提供程序已被弃用，但保留在此处以供参考。如果您当前正在使用这些提供商中的任何一个，请考虑迁移到替代解决方案。

### `@Greptile`

查询当前repo/branch.的[Greptile](<https://www.greptile.com/>)索引

#### YAML

```yaml
context:
  - provider: greptile
    params:
      greptileToken: "..."
      githubToken: "..."
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "greptile",
      "params": { "GreptileToken": "...", "GithubToken": "..." }
    }
  ]
}
```

### `@Commits`

引用特定的 git 提交元数据和 diff 或所有最近的提交。

#### YAML

```yaml
context:
  - provider: commit
    params:
      Depth: 50
      LastXCommitsDepth: 10
```

#### JSON

```json
{
  "contextProviders": [
    { "name": "commit", "params": { "Depth": 50, "LastXCommitsDepth": 10 } }
  ]
}
```

深度是将多少提交加载到子菜单中，默认为50。 LastXCommitsDepth 是将包含多少个最近提交，默认为 10。

### `@Discord`

参考 Discord 频道中的消息。

#### YAML

```yaml
context:
  - provider: discord
    params:
      discordKey: "bot token"
      guildId: "1234567890"
      channels:
        - id: "123456"
          name: "example-channel"
        - id: "678901"
          name: "example-channel-2"
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "discord",
      "params": {
        "discordKey": "bot token",
        "guildId": "1234567890",
        "channels": [
          { "id": "123456", "name": "example-channel" },
          { "id": "678901", "name": "example-channel-2" }
        ]
      }
    }
  ]
}
```

确保包含您自己的[机器人令牌](<https://discord.com/developers/applications>)，并将其加入您的相关服务器。如果您想更精细地控制搜索哪些频道，您可以指定要搜索的频道 ID 列表。如果您不想指定任何频道，只需包含公会 ID（服务器 ID）即可包含所有频道。提供者仅读取文本通道。

### `@Jira`

参考 Jira 问题中的对话。

#### YAML

```yaml
context:
  - provider: jira
    params:
      domain: company.atlassian.net
      token: ATATT...
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "jira",
      "params": { "domain": "company.atlassian.net", "token": "ATATT..." }
    }
  ]
}
```

确保包含您自己的 [Atlassian API 令牌 ](<https://id.atlassian.com/manage-profile/security/api-tokens>)，或使用您的 `email` 和 `token`，并将令牌设置为您的密码以进行基本身份验证。如果您使用自己的 Atlassian API 令牌，请不要配置您的电子邮件。

#### Jira 数据中心支持

此上下文提供程序支持 Jira API 版本 2 和 3。默认情况下，它将使用版本 3，因为这是云版本使用的版本，但如果您有 Jira 的数据中心版本，则需要使用 `apiVersion` 属性将 API 版本设置为 2。

#### YAML

```yaml
context:
  - provider: jira
    params:
      apiVersion: "2"
```

#### JSON

```json
{ "contextProviders": [{ "name": "jira", "params": { "apiVersion": "2" } }] }
```

#### 问题查询

默认情况下，将使用以下查询来查找问题：

```
assignee = currentUser() AND resolution = Unresolved order by updated DESC
```

您可以通过设置 `issueQuery` 参数来覆盖此查询。

#### 最大结果

您可以设置`maxResults`参数来限制返回结果的数量。默认值为 `50`。

### `@Gitlab Merge Request`

在 GitLab 上引用此分支的开放 MR。

#### YAML

```yaml
context:
  - provider: gitlab-mr
    params:
      token: "..."
```

#### JSON

```json
{ "contextProviders": [{ "name": "gitlab-mr", "params": { "token": "..." } }] }
```

您需要创建一个范围为 `read_api` 的 [ 个人访问令牌 ](<https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html>)。

#### 使用自托管 GitLab

您可以通过在配置中设置 `domain` 参数来指定要通信的域。默认情况下，该值设置为 `gitlab.com`。

#### YAML

```yaml
context:
  - provider: gitlab-mr
    params:
      token: "..."
      domain: "gitlab.example.com"
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "gitlab-mr",
      "params": { "token": "...", "domain": "gitlab.example.com" }
    }
  ]
}
```

#### 过滤评论

如果您选择要编辑的某些代码，则可以让上下文提供程序过滤掉其他文件的注释。要启用此功能，请将 `filterComments` 设置为 `true`。

### `@Google`

参考 Google 搜索的结果。

#### YAML

```yaml
context:
  - provider: google
    params:
      serperApiKey: <YOUR_SERPER.DEV_API_KEY>
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "google",
      "params": { "serperApiKey": "<YOUR_SERPER.DEV_API_KEY>" }
    }
  ]
}
```

例如，如果您想搜索和讨论学习方法Python，请输入“@Google python教程”。

注意：您可以在 [serper.dev](<https://serper.dev>) 免费获取 API 密钥。

### @Database Context Provider – 参考数据库表模式

引用 Sqlite、Postgres、MSSQL 和 MySQL 数据库中的表架构。

#### YAML

```yaml
context:
  - provider: database
    params:
      connections:
        - name: examplePostgres
          connection_type: postgres
          connection:
            user: username
            host: localhost
            database: exampleDB
            password: yourPassword
            port: 5432
        - name: exampleMssql
          connection_type: mssql
          connection:
            user: username
            server: localhost
            database: exampleDB
            password: yourPassword
        - name: exampleSqlite
          connection_type: sqlite
          connection:
            filename: /path/to/your/sqlite/database.db
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "database",
      "params": {
        "connections": [
          {
            "name": "examplePostgres",
            "connection_type": "postgres",
            "connection": {
              "user": "username",
              "host": "localhost",
              "database": "exampleDB",
              "password": "yourPassword",
              "port": 5432
            }
          },
          {
            "name": "exampleMssql",
            "connection_type": "mssql",
            "connection": {
              "user": "username",
              "server": "localhost",
              "database": "exampleDB",
              "password": "yourPassword"
            }
          },
          {
            "name": "exampleSqlite",
            "connection_type": "sqlite",
            "connection": { "filename": "/path/to/your/sqlite/database.db" }
          }
        ]
      }
  }
  ]
}
```

每个连接应包含唯一的名称 `connection_type` 以及特定于每种数据库类型的必要连接参数。

可用的连接类型：

- `postgres`
- `mysql`
- `sqlite`

### `@Issue`

参考 GitHub 问题中的对话。

#### YAML

```yaml
context:
  - provider: issue
    params:
      repos:
        - owner: continuedev
          repo: continue
      githubToken: ghp_xxx
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "issue",
      "params": {
        "repos": [{ "owner": "continuedev", "repo": "continue" }],
        "githubToken": "ghp_xxx"
      }
    }
  ]
}
```

请务必包含您自己的 [GitHub 个人访问令牌 ](<https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token>) 以避免受到速率限制。

### `@Url`

引用给定URL的降价转换内容。

#### YAML

```yaml
context:
  - provider: url
```

#### JSON

```json
{ "contextProviders": [{ "name": "url" }] }
```

### `@Search`

引用代码库搜索的结果，就像从 VS Code 搜索中获得的结果一样。

#### YAML

```yaml
context:
  - provider: search
    params:
      maxResults: 100 # optional, defaults to 200
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "search",
      "params": {
        "maxResults": 100 // optional, defaults to 200
      }
    }
  ]
}
```

该上下文提供程序由 [ripgrep](<https://github.com/BurntSushi/ripgrep>) 提供支持。

### `@Web`

参考网络上的相关页面，根据您的输入自动确定。

（可选）设置 `n` 以限制返回结果的数量（默认 6）。

#### YAML

```yaml
context:
  - provider: web
    params:
      n: 5
```

#### JSON

```json
{ "contextProviders": [{ "name": "web", "params": { "n": 5 } }] }
```


## 原文对照

<details>
<summary>展开完整原文</summary>

**注意：**

The context providers documented on this page have been deprecated and are no longer actively maintained. We recommend using alternative solutions or the Model Context Protocol (MCP) for similar functionality. These providers may be removed in future versions.

## Deprecated Providers

The following context providers have been deprecated but are kept here for reference. If you're currently using any of these providers, consider migrating to alternative solutions.

### `@Greptile`

Query a [Greptile](<https://www.greptile.com/>) index of the current repo/branch.

#### YAML

```yaml
context:
  - provider: greptile
    params:
      greptileToken: "..."
      githubToken: "..."
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "greptile",
      "params": { "GreptileToken": "...", "GithubToken": "..." }
    }
  ]
}
```

### `@Commits`

Reference specific git commit metadata and diff or all of the recent commits.

#### YAML

```yaml
context:
  - provider: commit
    params:
      Depth: 50
      LastXCommitsDepth: 10
```

#### JSON

```json
{
  "contextProviders": [
    { "name": "commit", "params": { "Depth": 50, "LastXCommitsDepth": 10 } }
  ]
}
```

The depth is how many commits will be loaded into the submenu, defaults to 50. The LastXCommitsDepth is how many recent commits will be included, defaults to 10.

### `@Discord`

Reference the messages in a Discord channel.

#### YAML

```yaml
context:
  - provider: discord
    params:
      discordKey: "bot token"
      guildId: "1234567890"
      channels:
        - id: "123456"
          name: "example-channel"
        - id: "678901"
          name: "example-channel-2"
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "discord",
      "params": {
        "discordKey": "bot token",
        "guildId": "1234567890",
        "channels": [
          { "id": "123456", "name": "example-channel" },
          { "id": "678901", "name": "example-channel-2" }
        ]
      }
    }
  ]
}
```

Make sure to include your own [Bot Token](<https://discord.com/developers/applications>), and join it to your related server . If you want more granular control over which channels are searched, you can specify a list of channel IDs to search in. If you don't want to specify any channels, just include the guild id(Server ID) and all channels will be included. The provider only reads text channels.

### `@Jira`

Reference the conversation in a Jira issue.

#### YAML

```yaml
context:
  - provider: jira
    params:
      domain: company.atlassian.net
      token: ATATT...
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "jira",
      "params": { "domain": "company.atlassian.net", "token": "ATATT..." }
    }
  ]
}
```

Make sure to include your own [Atlassian API Token](<https://id.atlassian.com/manage-profile/security/api-tokens>), or use your `email` and `token`, with token set to your password for basic authentication. If you use your own Atlassian API Token, don't configure your email.

#### Jira Datacenter Support

This context provider supports both Jira API version 2 and 3. It will use version 3 by default since that's what the cloud version uses, but if you have the datacenter version of Jira, you'll need to set the API Version to 2 using the `apiVersion` property.

#### YAML

```yaml
context:
  - provider: jira
    params:
      apiVersion: "2"
```

#### JSON

```json
{ "contextProviders": [{ "name": "jira", "params": { "apiVersion": "2" } }] }
```

#### Issue Query

By default, the following query will be used to find issues:

```
assignee = currentUser() AND resolution = Unresolved order by updated DESC
```

You can override this query by setting the `issueQuery` parameter.

#### Max results

You can set the `maxResults` parameter to limit the number of results returned. The default is `50`.

### `@Gitlab Merge Request`

Reference an open MR for this branch on GitLab.

#### YAML

```yaml
context:
  - provider: gitlab-mr
    params:
      token: "..."
```

#### JSON

```json
{ "contextProviders": [{ "name": "gitlab-mr", "params": { "token": "..." } }] }
```

You will need to create a [personal access token](<https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html>) with the `read_api` scope.

#### Using Self-Hosted GitLab

You can specify the domain to communicate with by setting the `domain` parameter in your configurtion. By default this is set to `gitlab.com`.

#### YAML

```yaml
context:
  - provider: gitlab-mr
    params:
      token: "..."
      domain: "gitlab.example.com"
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "gitlab-mr",
      "params": { "token": "...", "domain": "gitlab.example.com" }
    }
  ]
}
```

#### Filtering Comments

If you select some code to be edited, you can have the context provider filter out comments for other files. To enable this feature, set `filterComments` to `true`.

### `@Google`

Reference the results of a Google search.

#### YAML

```yaml
context:
  - provider: google
    params:
      serperApiKey: <YOUR_SERPER.DEV_API_KEY>
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "google",
      "params": { "serperApiKey": "<YOUR_SERPER.DEV_API_KEY>" }
    }
  ]
}
```

For example, type "@Google python tutorial" if you want to search and discuss ways of learning Python.

Note: You can get an API key for free at [serper.dev](<https://serper.dev>).

### @Database Context Provider – Reference Database Table Schemas

Reference table schemas from Sqlite, Postgres, MSSQL, and MySQL databases.

#### YAML

```yaml
context:
  - provider: database
    params:
      connections:
        - name: examplePostgres
          connection_type: postgres
          connection:
            user: username
            host: localhost
            database: exampleDB
            password: yourPassword
            port: 5432
        - name: exampleMssql
          connection_type: mssql
          connection:
            user: username
            server: localhost
            database: exampleDB
            password: yourPassword
        - name: exampleSqlite
          connection_type: sqlite
          connection:
            filename: /path/to/your/sqlite/database.db
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "database",
      "params": {
        "connections": [
          {
            "name": "examplePostgres",
            "connection_type": "postgres",
            "connection": {
              "user": "username",
              "host": "localhost",
              "database": "exampleDB",
              "password": "yourPassword",
              "port": 5432
            }
          },
          {
            "name": "exampleMssql",
            "connection_type": "mssql",
            "connection": {
              "user": "username",
              "server": "localhost",
              "database": "exampleDB",
              "password": "yourPassword"
            }
          },
          {
            "name": "exampleSqlite",
            "connection_type": "sqlite",
            "connection": { "filename": "/path/to/your/sqlite/database.db" }
          }
        ]
      }
  }
  ]
}
```

Each connection should include a unique name, the `connection_type`, and the necessary connection parameters specific to each database type.

Available connection types:

- `postgres`
- `mysql`
- `sqlite`

### `@Issue`

Reference the conversation in a GitHub issue.

#### YAML

```yaml
context:
  - provider: issue
    params:
      repos:
        - owner: continuedev
          repo: continue
      githubToken: ghp_xxx
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "issue",
      "params": {
        "repos": [{ "owner": "continuedev", "repo": "continue" }],
        "githubToken": "ghp_xxx"
      }
    }
  ]
}
```

Make sure to include your own [GitHub personal access token](<https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token>) to avoid being rate-limited.

### `@Url`

Reference the markdown converted contents of a given URL.

#### YAML

```yaml
context:
  - provider: url
```

#### JSON

```json
{ "contextProviders": [{ "name": "url" }] }
```

### `@Search`

Reference the results of codebase search, just like the results you would get from VS Code search.

#### YAML

```yaml
context:
  - provider: search
    params:
      maxResults: 100 # optional, defaults to 200
```

#### JSON

```json
{
  "contextProviders": [
    {
      "name": "search",
      "params": {
        "maxResults": 100 // optional, defaults to 200
      }
    }
  ]
}
```

This context provider is powered by [ripgrep](<https://github.com/BurntSushi/ripgrep>).

### `@Web`

Reference relevant pages from across the web, automatically determined from your input.

Optionally, set `n` to limit the number of results returned (default 6).

#### YAML

```yaml
context:
  - provider: web
    params:
      n: 5
```

#### JSON

```json
{ "contextProviders": [{ "name": "web", "params": { "n": 5 } }] }
```


</details>
