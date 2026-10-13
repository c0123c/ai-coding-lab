# OpenCode 浏览器界面与服务配置

产品指南。介绍启动网页界面、端口和监听地址、mDNS、CORS、认证、会话、服务状态，以及连接同一后台的终端界面。

- 来源：OpenCode
- 资料类型：产品指南
- 存档版本：`055d95bb7e27`。
- 文档时间：源码提交（存档版）：[2026-02-03](<https://github.com/anomalyco/opencode/commit/95d0d476e306e0767e569466e39152390e7024d1>)；发表/更新：存档未标注；抓取：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。
- 使用边界：服务监听地址和认证决定访问范围。

[原文发布页](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/web.mdx>) · [原始文件](<../../../原始抓取备份/原文/GitHub项目/OpenCode/OpenCode 浏览器界面与服务配置.mdx>) · [返回资料总览](<../../资料总览.md>)


<details>
<summary>源码自带的配置与元数据（保留原样）</summary>

~~~yaml
---
title: Web
description: Using OpenCode in your browser.
---
~~~

</details>

[查看原文对照](#原文对照)

## 中文全文

OpenCode 可以在浏览器中作为 Web 应用程序运行，无需终端即可提供同样强大的 AI 编码体验。

![OpenCode 网页 - 新会话](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-new-session.png>)

## 开始使用

通过运行以下命令启动 Web 界面：

```bash
opencode web
```

这将使用随机可用端口在 `127.0.0.1` 上启动本地服务器，并自动在默认浏览器中打开 OpenCode。

**注意：**

如果未设置`OPENCODE_SERVER_PASSWORD`，服务器将不安全。这对于本地使用来说很好，但应该针对网络访问进行设置。

**提示：Windows用户：**

为了获得最佳体验，请从 [WSL](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>) 运行 `opencode web`，而不是 PowerShell。这确保了正确的文件系统访问和终端集成。

---

## 配置

您可以使用命令行标志或在 [ 配置文件 ](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>) 中配置 Web 服务器。

### 港口

默认情况下，OpenCode 选择一个可用端口。您可以指定一个端口：

```bash
opencode web --port 4096
```

### 主机名

默认情况下，服务器绑定到 `127.0.0.1`（仅限本地主机）。要使 OpenCode 在您的网络上可访问：

```bash
opencode web --hostname 0.0.0.0
```

当使用`0.0.0.0`时，OpenCode将同时显示本地地址和网络地址：

```
  Local access:       http://localhost:4096
  Network access:     http://192.168.1.100:4096
```

### mDNS 发现

启用 mDNS 以使您的服务器在本地网络上可发现：

```bash
opencode web --mdns
```

这会自动将主机名设置为 `0.0.0.0` 并将服务器通告为 `opencode.local`。

您可以自定义mDNS域名以在同一网络上运行多个实例：

```bash
opencode web --mdns --mdns-domain myproject.local
```

### CORS

允许 CORS 附加域（对于自定义前端有用）：

```bash
opencode web --cors https://example.com
```

### 认证

要保护访问，请使用 `OPENCODE_SERVER_PASSWORD` 环境变量设置密码：

```bash
OPENCODE_SERVER_PASSWORD=secret opencode web
```

用户名默认为 `opencode`，但可以使用 `OPENCODE_SERVER_USERNAME` 进行更改。

---

## 使用网络界面

启动后，网络界面将提供对您的 OpenCode 会话的访问。

### 会议

从主页查看和管理您的会话。您可以查看活动会话并开始新会话。

![OpenCode 网络 - 活动会话](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-active-session.png>)

### 服务器状态

单击“查看服务器”可查看连接的服务器及其状态。

![OpenCode Web - 查看服务器](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-see-servers.png>)

---

## 连接终端

您可以将终端 TUI 连接到正在运行的 Web 服务器：

```bash
# Start the web server
opencode web --port 4096

# In another terminal, attach the TUI
opencode attach http://localhost:4096
```

这允许您同时使用 Web 界面和终端，共享相同的会话和状态。

---

## 配置文件

您还可以在 `opencode.json` 配置文件中配置服务器设置：

```json
{
  "server": {
    "port": 4096,
    "hostname": "0.0.0.0",
    "mdns": true,
    "cors": ["https://example.com"]
  }
}
```

命令行标志优先于配置文件设置。


## 原文对照

<details>
<summary>展开完整原文</summary>

OpenCode can run as a web application in your browser, providing the same powerful AI coding experience without needing a terminal.

![OpenCode Web - New Session](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-new-session.png>)

## Getting Started

Start the web interface by running:

```bash
opencode web
```

This starts a local server on `127.0.0.1` with a random available port and automatically opens OpenCode in your default browser.

**注意：**

If `OPENCODE_SERVER_PASSWORD` is not set, the server will be unsecured. This is fine for local use but should be set for network access.

**提示：Windows Users：**

For the best experience, run `opencode web` from [WSL](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_在 Windows 与 WSL 中运行 OpenCode.md>) rather than PowerShell. This ensures proper file system access and terminal integration.

---

## Configuration

You can configure the web server using command line flags or in your [config file](<https://github.com/anomalyco/opencode/blob/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/content/docs/OpenCode_OpenCode 配置位置、合并顺序与功能选项.md>).

### Port

By default, OpenCode picks an available port. You can specify a port:

```bash
opencode web --port 4096
```

### Hostname

By default, the server binds to `127.0.0.1` (localhost only). To make OpenCode accessible on your network:

```bash
opencode web --hostname 0.0.0.0
```

When using `0.0.0.0`, OpenCode will display both local and network addresses:

```
  Local access:       http://localhost:4096
  Network access:     http://192.168.1.100:4096
```

### mDNS Discovery

Enable mDNS to make your server discoverable on the local network:

```bash
opencode web --mdns
```

This automatically sets the hostname to `0.0.0.0` and advertises the server as `opencode.local`.

You can customize the mDNS domain name to run multiple instances on the same network:

```bash
opencode web --mdns --mdns-domain myproject.local
```

### CORS

To allow additional domains for CORS (useful for custom frontends):

```bash
opencode web --cors https://example.com
```

### Authentication

To protect access, set a password using the `OPENCODE_SERVER_PASSWORD` environment variable:

```bash
OPENCODE_SERVER_PASSWORD=secret opencode web
```

The username defaults to `opencode` but can be changed with `OPENCODE_SERVER_USERNAME`.

---

## Using the Web Interface

Once started, the web interface provides access to your OpenCode sessions.

### Sessions

View and manage your sessions from the homepage. You can see active sessions and start new ones.

![OpenCode Web - Active Session](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-active-session.png>)

### Server Status

Click "See Servers" to view connected servers and their status.

![OpenCode Web - See Servers](<https://raw.githubusercontent.com/anomalyco/opencode/055d95bb7e278c94baf06235a52cac79dd13ba67/packages/web/src/assets/web/web-homepage-see-servers.png>)

---

## Attaching a Terminal

You can attach a terminal TUI to a running web server:

```bash
# Start the web server
opencode web --port 4096

# In another terminal, attach the TUI
opencode attach http://localhost:4096
```

This allows you to use both the web interface and terminal simultaneously, sharing the same sessions and state.

---

## Config File

You can also configure server settings in your `opencode.json` config file:

```json
{
  "server": {
    "port": 4096,
    "hostname": "0.0.0.0",
    "mdns": true,
    "cors": ["https://example.com"]
  }
}
```

Command line flags take precedence over config file settings.


</details>
