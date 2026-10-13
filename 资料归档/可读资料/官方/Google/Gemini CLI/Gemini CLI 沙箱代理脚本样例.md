# Gemini CLI 沙箱代理脚本样例

展示通过环境变量配置网络代理，只允许示例域名的指定 HTTPS 连接；用于理解沙箱网络限制实现。

- 来源：Google／Gemini CLI
- 资料类型：存档文档
- 存档版本：`9b6e0265d16b`。
- 文档时间：源码提交（存档版）：[2025-12-01](<https://github.com/google-gemini/gemini-cli/commit/26f050ff10dd90a8c4e6e125effe79d0272aa474>)；发表/更新：官方未公开；本地归档批次：2026-10-10
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.com/google-gemini/gemini-cli/blob/main/docs/examples/proxy-script.md>) · [原始文件](<../../../../原始抓取备份/原文/官方/Google/Gemini CLI/Gemini CLI 沙箱代理脚本样例.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 代理脚本示例

以下是可与 
`GEMINI_SANDBOX_PROXY_COMMAND` 环境变量一起使用的代理脚本示例。该脚本仅允许
`HTTPS`连接到`example.com:443`并拒绝所有其他请求。

```javascript
#!/usr/bin/env node

/**
 * @license
 * Copyright 2025 Google LLC
 * SPDX-License-Identifier: Apache-2.0
 */

// Example proxy server that listens on :::8877 and only allows HTTPS connections to example.com.
// Set `GEMINI_SANDBOX_PROXY_COMMAND=scripts/example-proxy.js` to run proxy alongside sandbox
// Test via `curl https://example.com` inside sandbox (in shell mode or via shell tool)

import http from 'node:http';
import net from 'node:net';
import { URL } from 'node:url';
import console from 'node:console';

const PROXY_PORT = 8877;
const ALLOWED_DOMAINS = ['example.com', 'googleapis.com'];
const ALLOWED_PORT = '443';

const server = http.createServer((req, res) => {
  // Deny all requests other than CONNECT for HTTPS
  console.log(
    `[PROXY] Denying non-CONNECT request for: ${req.method} ${req.url}`,
  );
  res.writeHead(405, { 'Content-Type': 'text/plain' });
  res.end('Method Not Allowed');
});

server.on('connect', (req, clientSocket, head) => {
  // req.url will be in the format "hostname:port" for a CONNECT request.
  const { port, hostname } = new URL(`http://${req.url}`);

  console.log(`[PROXY] Intercepted CONNECT request for: ${hostname}:${port}`);

  if (
    ALLOWED_DOMAINS.some(
      (domain) => hostname == domain || hostname.endsWith(`.${domain}`),
    ) &&
    port === ALLOWED_PORT
  ) {
    console.log(`[PROXY] Allowing connection to ${hostname}:${port}`);

    // Establish a TCP connection to the original destination.
    const serverSocket = net.connect(port, hostname, () => {
      clientSocket.write('HTTP/1.1 200 Connection Established\r\n\r\n');
      // Create a tunnel by piping data between the client and the destination server.
      serverSocket.write(head);
      serverSocket.pipe(clientSocket);
      clientSocket.pipe(serverSocket);
    });

    serverSocket.on('error', (err) => {
      console.error(`[PROXY] Error connecting to destination: ${err.message}`);
      clientSocket.end(`HTTP/1.1 502 Bad Gateway\r\n\r\n`);
    });
  } else {
    console.log(`[PROXY] Denying connection to ${hostname}:${port}`);
    clientSocket.end('HTTP/1.1 403 Forbidden\r\n\r\n');
  }

  clientSocket.on('error', (err) => {
    // This can happen if the client hangs up.
    console.error(`[PROXY] Client socket error: ${err.message}`);
  });
});

server.listen(PROXY_PORT, () => {
  const address = server.address();
  console.log(`[PROXY] Proxy listening on ${address.address}:${address.port}`);
  console.log(
    `[PROXY] Allowing HTTPS connections to domains: ${ALLOWED_DOMAINS.join(', ')}`,
  );
});
```


## 原文对照

<details>
<summary>展开完整原文</summary>

# Example proxy script

The following is an example of a proxy script that can be used with the
`GEMINI_SANDBOX_PROXY_COMMAND` environment variable. This script only allows
`HTTPS` connections to `example.com:443` and declines all other requests.

```javascript
#!/usr/bin/env node

/**
 * @license
 * Copyright 2025 Google LLC
 * SPDX-License-Identifier: Apache-2.0
 */

// Example proxy server that listens on :::8877 and only allows HTTPS connections to example.com.
// Set `GEMINI_SANDBOX_PROXY_COMMAND=scripts/example-proxy.js` to run proxy alongside sandbox
// Test via `curl https://example.com` inside sandbox (in shell mode or via shell tool)

import http from 'node:http';
import net from 'node:net';
import { URL } from 'node:url';
import console from 'node:console';

const PROXY_PORT = 8877;
const ALLOWED_DOMAINS = ['example.com', 'googleapis.com'];
const ALLOWED_PORT = '443';

const server = http.createServer((req, res) => {
  // Deny all requests other than CONNECT for HTTPS
  console.log(
    `[PROXY] Denying non-CONNECT request for: ${req.method} ${req.url}`,
  );
  res.writeHead(405, { 'Content-Type': 'text/plain' });
  res.end('Method Not Allowed');
});

server.on('connect', (req, clientSocket, head) => {
  // req.url will be in the format "hostname:port" for a CONNECT request.
  const { port, hostname } = new URL(`http://${req.url}`);

  console.log(`[PROXY] Intercepted CONNECT request for: ${hostname}:${port}`);

  if (
    ALLOWED_DOMAINS.some(
      (domain) => hostname == domain || hostname.endsWith(`.${domain}`),
    ) &&
    port === ALLOWED_PORT
  ) {
    console.log(`[PROXY] Allowing connection to ${hostname}:${port}`);

    // Establish a TCP connection to the original destination.
    const serverSocket = net.connect(port, hostname, () => {
      clientSocket.write('HTTP/1.1 200 Connection Established\r\n\r\n');
      // Create a tunnel by piping data between the client and the destination server.
      serverSocket.write(head);
      serverSocket.pipe(clientSocket);
      clientSocket.pipe(serverSocket);
    });

    serverSocket.on('error', (err) => {
      console.error(`[PROXY] Error connecting to destination: ${err.message}`);
      clientSocket.end(`HTTP/1.1 502 Bad Gateway\r\n\r\n`);
    });
  } else {
    console.log(`[PROXY] Denying connection to ${hostname}:${port}`);
    clientSocket.end('HTTP/1.1 403 Forbidden\r\n\r\n');
  }

  clientSocket.on('error', (err) => {
    // This can happen if the client hangs up.
    console.error(`[PROXY] Client socket error: ${err.message}`);
  });
});

server.listen(PROXY_PORT, () => {
  const address = server.address();
  console.log(`[PROXY] Proxy listening on ${address.address}:${address.port}`);
  console.log(
    `[PROXY] Allowing HTTPS connections to domains: ${ALLOWED_DOMAINS.join(', ')}`,
  );
});
```


</details>
