# 用 Copilot SDK 为 Issue 自动分流

以 React Native 应用为例介绍 Issue 摘要与分类，涵盖 SDK 生命周期、提示、响应处理和客户端集成。

- 来源：GitHub／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-03-24](<https://github.blog/ai-and-ml/github-copilot/building-ai-powered-github-issue-triage-with-the-copilot-sdk/>)；更新：[2026-04-22](<https://github.blog/ai-and-ml/github-copilot/building-ai-powered-github-issue-triage-with-the-copilot-sdk/>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://github.blog/ai-and-ml/github-copilot/building-ai-powered-github-issue-triage-with-the-copilot-sdk/>) · [原始文件](<../../../../原始抓取备份/原文/官方/GitHub/工程文章/用 Copilot SDK 为 Issue 自动分流.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

# 使用 Copilot SDK 构建 AI- 驱动的 GitHub 问题分类

Copilot SDK 允许您将支持 [Copilot 聊天 ](<https://copilot.microsoft.com/>) 的相同 AI 添加到您自己的应用程序中。我想看看实际情况如何，因此我构建了一个名为 IssueCrush 的问题分类应用程序。这是我学到的知识以及如何开始。

如果您曾经维护过开源项目，或者在拥有活跃存储库的团队中工作过，您就会知道这种感觉。您打开 GitHub 并看到通知徽章：47 问题。有些是错误，有些是功能请求，有些是应该讨论的问题，有些是三年前问题的重复。

分类问题的精神负担是真实存在的。每一个都需要上下文切换：阅读标题，浏览描述，检查标签，考虑优先级，决定做什么。将其乘以多个存储库中的数十个问题，你的大脑就会突然变得混乱。

我想让这更快。通过GitHub Copilot SDK，我找到了一种方法。

## 输入【IssueCrush】：右滑发货

IssueCrush 将您的 GitHub 问题显示为可刷卡。左为关闭，右为保留。当您点击“获取 AI 摘要”时，Copilot 会读取该问题并告诉您它的内容以及如何处理它。维护人员无需阅读每一个冗长的描述，而是可以获得即时、可操作的上下文，以更快地做出分类决策。以下是我如何集成 GitHub Copilot SDK 来实现这一目标。

![开发人员工作区的屏幕截图，显示了对 IssueCrush 项目打开的 VS Code 编辑器，其中可见 package.json 文件，以及运行服务器的终端，该服务器使用 GitHub Copilot 记录 AI- 生成的问题摘要SDK。右侧，iPhone 模拟器显示 IssueCrush 移动应用程序，列出 GitHub 问题，其中包含标签、进度指示器和“获取 AI 摘要”按钮，以及底部的滑动式批准和拒绝按钮。](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.37.43-AM.png?resize=1305%2C835>)

## 架构挑战

第一个技术决策是确定在哪里运行 Copilot SDK。 React 本机应用程序无法直接使用 Node.js 包，并且 Copilot SDK 需要 Node.js 运行时。在内部，SDK管理本地CopilotCLI进程并通过JSON-RPC与其通信。由于对 CLI 二进制文件和 Node 环境的依赖，集成必须在服务器端运行，而不是直接在 React 本机应用程序中运行。这意味着服务器必须安装 Copilot CLI 并且在系统 PATH 上可用。

我选择了服务器端集成模式：

![架构图显示 React 本机和 Web 客户端通过 HTTPS 与 Node.js 服务器进行通信。服务器运行GitHub CopilotSDK，它通过JSON-RPC管理本地CopilotCLI进程。 CLI连接到GitHub Copilot服务，而客户端分别与GitHub OAuth和GitHubRESTAPI交互以获取问题数据。](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.39.25-AM.png?resize=1318%2C921>)

以下是此设置有效的原因：

* **单个 SDK 实例在所有客户端 ** 之间共享，因此您无需为每个移动客户端建立新连接。服务器为每个请求管理一个实例。更少的开销，更少的身份验证握手，更简单的清理。
* **用于Copilot身份验证**的服务器端机密，以确保凭据安全。您的 API 令牌永远不会接触客户端。它们位于它们所属的服务器上，而不是位于可被反编译的 React 本机包内。
* **AI 不可用时平稳降级**，因此即使 Copilot 服务出现故障或超时，您仍然可以对问题进行分类。该应用程序退回到基本摘要。 AI 使分类更快，但它不应该是单点故障。
* **记录调试和监视请求**，因为每个提示和响应都会经过您的服务器。您可以跟踪延迟、捕获故障并调试提示问题，而无需将仪器连接到移动客户端。

在构建这样的东西之前，您需要：

1. [Copilot CLI](<https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli>) 安装在您的服务器上。
2. GitHub Copilot 订阅，或带有您自己的 API 密钥的 [BYOK 配置 ](<https://github.com/github/copilot-sdk/blob/main/docs/auth/byok.md>)。
3. Copilot CLI 已通过身份验证。在您的服务器上运行 copilot auth，或设置 `COPILOT_GITHUB_TOKEN` 环境变量。

## 如何实现CopilotSDK集成

Copilot SDK 使用基于会话的模型。您启动一个客户端（生成 CLI 进程），创建一个会话，发送消息，然后进行清理。

```
const { CopilotClient, approveAll } = await import('@github/copilot-sdk');
 
let client = null; 
let session = null; 
 
try { 
   // 1. Initialize the client (spawns Copilot CLI in server mode) 
   client = new CopilotClient(); 
   await client.start(); 
 
  // 2. Create a session with your preferred model 
  session = await client.createSession({
  model: 'gpt-4.1',
  onPermissionRequest: approveAll,
});
 
  // 3. Send your prompt and wait for response 
   const response = await session.sendAndWait({ prompt }); 
 
  // 4. Extract the content 
   if (response && response.data && response.data.content) { 
     const summary = response.data.content; 
     // Use the summary... 
   } 
 
} finally { 
   // 5. Always clean up 
   if (session) await session.disconnect().catch(() => {}); 
   if (client) await client.stop().catch(() => {}); 
}
```

## 按键 SDK 图案

### 1。生命周期管理

SDK 遵循严格的生命周期：`start() → createSession() → sendAndWait() → disconnect() → stop()`。

这是我通过惨痛教训学到的东西：未能清理会话会导致资源泄漏。我花了两个小时调试内存问题，然后才意识到我忘记了 `disconnect()` 调用。将每个会话交互包装在 try/finally. 中。清理调用上的 `.catch(() => {})` 可防止清理错误掩盖原始错误。

### 2。快速分类工程

提示结构为模型提供了足够的上下文来完成其工作。我提供有关该问题的结构化信息，而不是转储原始文本：

```
const prompt = `You are analyzing a GitHub issue to help a developer quickly understand it and decide how to handle it. 
 
Issue Details: 
- Title: ${issue.title} 
- Number: #${issue.number} 
- Repository: ${issue.repository?.full_name || 'Unknown'} 
- State: ${issue.state} 
- Labels: ${issue.labels?.length ? issue.labels.map(l => l.name).join(', ') : 'None'} 
- Created: ${issue.created_at} 
- Author: ${issue.user?.login || 'Unknown'} 
 
Issue Body: 
${issue.body || 'No description provided.'} 
 
Provide a concise 2-3 sentence summary that: 
1. Explains what the issue is about 
2. Identifies the key problem or request 
3. Suggests a recommended action (e.g., "needs investigation", "ready to implement", "assign to backend team", "close as duplicate") 
 
Keep it clear, actionable, and helpful for quick triage. No markdown formatting.`;
```

标签和作者背景比你想象的更重要。来自首次贡献者的问题需要与来自核心维护者的问题不同的处理，并且 AI 使用此信息来调整其摘要。

### 3。响应处理

一旦会话空闲，`sendAndWait()`方法就会返回助手的响应。在访问嵌套属性之前始终验证响应链是否存在：

```
const response = await session.sendAndWait({ prompt }, 30000); // 30 second timeout 
 
let summary; 
if (response && response.data && response.data.content) { 
   summary = response.data.content; 
} else { 
   throw new Error('No content received from Copilot'); 
}
```

`sendAndWait()` 的第二个参数是以毫秒为单位的超时。将其设置得足够高以解决复杂的问题，但又足够低以确保用户不会盯着旋转器。我已经看到足够多的“未定义不是对象”错误，知道你永远不应该跳过响应链上的空检查。

## 客户端服务层

在 React 本机端，我将 API 调用包装在处理初始化和错误状态的服务类中：

```
// src/lib/copilotService.ts 
import type { GitHubIssue } from '../api/github'; 
import { getToken } from './tokenStorage'; 
export interface SummaryResult { 
   summary: string; 
   fallback?: boolean; 
   requiresCopilot?: boolean; 
} 
 
export class CopilotService { 
   private backendUrl = process.env.EXPO_PUBLIC_API_URL || 'http://localhost:3000'; 
 
  async initialize(): Promise<{ copilotMode: string }> { 
     try { 
       const response = await fetch(`${this.backendUrl}/health`); 
       const data = await response.json(); 
 
      console.log('Backend health check:', data); 
       return { copilotMode: data.copilotMode || 'unknown' }; 
     } catch (error) { 
       console.error('Failed to connect to backend:', error); 
       throw new Error('Backend server not available'); 
     } 
   } 
 
  async summarizeIssue(issue: GitHubIssue): Promise<SummaryResult> { 
     try { 
       const token = await getToken(); 
 
      if (!token) { 
         throw new Error('No GitHub token available'); 
       } 
 
      const response = await fetch(`${this.backendUrl}/api/ai-summary`, { 
         method: 'POST', 
         headers: { 
           'Content-Type': 'application/json', 
         }, 
         body: JSON.stringify({ issue, token }), 
       }); 
 
      const data = await response.json(); 
 
      if (!response.ok) { 
         if (response.status === 403 && data.requiresCopilot) { 
           return { 
             summary: data.message || 'AI summaries require a GitHub Copilot subscription.', 
             requiresCopilot: true, 
           }; 
         } 
         throw new Error(data.error || 'Failed to generate summary'); 
       } 
 
      return { 
         summary: data.summary || 'Unable to generate summary', 
         fallback: data.fallback || false, 
       }; 
     } catch (error) { 
       console.error('Copilot summarization error:', error); 
       throw error; 
     } 
   } 
} 
 
export const copilotService = new CopilotService();
```

## React 本机集成

UI 是简单的 React 状态管理。点击按钮，调用服务，缓存结果：

```
const [loadingAiSummary, setLoadingAiSummary] = useState(false); 
 
const handleGetAiSummary = async () => { 
   const issue = issues[currentIndex]; 
   if (!issue || issue.aiSummary) return; 
 
  setLoadingAiSummary(true); 
   try { 
     const result = await copilotService.summarizeIssue(issue); 
     setIssues(prevIssues => 
       prevIssues.map((item, index) => 
         index === currentIndex ? { ...item, aiSummary: result.summary } : item 
       ) 
     ); 
   } catch (error) { 
     console.error('AI Summary error:', error); 
   } finally { 
     setLoadingAiSummary(false); 
   } 
};
```

一旦问题对象上存在摘要，该卡就会将按钮替换为摘要文本。如果用户滑开并返回，缓存的版本会立即呈现。无需第二次 API 呼叫。

## 优雅降级

AI 服务可能会失败。网络问题、速率限制和服务中断时有发生。服务器处理两种故障模式：订阅错误返回 403，以便客户端可以显示清晰的消息，而其他所有内容都会回退到根据问题元数据构建的摘要。

```
} catch (error) { 
   // Clean up on error 
   try { 
     if (session) await session.disconnect().catch(() => {}); 
     if (client) await client.stop().catch(() => {}); 
   } catch (cleanupError) { 
     // Ignore cleanup errors 
   } 
 
  const errorMessage = error.message.toLowerCase(); 
 
  // Copilot subscription errors get a clear 403 
   if (errorMessage.includes('unauthorized') || 
       errorMessage.includes('forbidden') || 
       errorMessage.includes('copilot') || 
       errorMessage.includes('subscription')) { 
     return res.status(403).json({ 
       error: 'Copilot access required', 
       message: 'AI summaries require a GitHub Copilot subscription.', 
       requiresCopilot: true 
     }); 
   } 
 
  // Everything else falls back to a metadata-based summary 
   const fallbackSummary = generateFallbackSummary(issue); 
   res.json({ summary: fallbackSummary, fallback: true }); 
}
```

后备根据我们已有的内容构建了一个有用的摘要：

```
function generateFallbackSummary(issue) { 
   const parts = [issue.title]; 
 
  if (issue.labels?.length) { 
     parts.push(`\nLabels: ${issue.labels.map(l => l.name).join(', ')}`); 
   } 
 
  if (issue.body) { 
     const firstSentence = issue.body.split(/[.!?]\s/)[0]; 
     if (firstSentence && firstSentence.length < 200) { 
       parts.push(`\n\n${firstSentence}.`); 
     } 
   } 
 
  parts.push('\n\nReview the full issue details to determine next steps.'); 
   return parts.join(''); 
}
```

## 其他一些值得注意的模式

服务器公开一个 `/health` 端点，该端点表示 AI 可用性。客户端在启动时检查它，如果后端不支持，则完全隐藏摘要按钮。没有损坏的按钮。

摘要是按需生成的，而不是预先生成的。这可以降低 API 成本，并避免用户在没有阅读问题的情况下滑过问题时浪费呼叫。

SDK 加载了await `import('@github/copilot-sdk')`，而不是顶级需求。即使 SDK 有问题，服务器也可以启动，从而使部署和调试更加顺利。

## 依赖关系

```
{ 
   "dependencies": { 
     "@github/copilot-sdk": "^0.1.14", 
     "express": "^5.2.1" 
   } 
}
```

SDK通过JSON-RPC与CopilotCLI进程通信。您需要在 PATH 中安装并使用 [Copilot CLI](<https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli>)。检查[SDK的软件包要求](<https://github.com/github/copilot-sdk>)以获取最低Node.js版本。

## 我在构建这个时学到了什么

**服务器端是正确的调用**。 SDK 需要 Copilot CLI 二进制文件，而您没有将其安装在手机上。在服务器上运行它可以将 AI 逻辑保留在一处，简化移动客户端，并且意味着凭证永远不会离开后端。

**提示结构比提示长度更重要**。与将整个问题正文转储为原始文本相比，向模型提供有组织的元数据（如标题、标签和作者）会产生更好的摘要。给模型一些可以使用的东西，它会给你一些有用的回报。

**始终有后备措施**。 AI 服务中断。发生速率限制。从第一天起就进行优雅降级的设计。即使 AI 片段离线，您的用户仍然应该能够对问题进行分类。

**清理您的会话**。 SDK 需要显式清理：`disconnect()` 然后是 `stop()`。我跳过了一次 `disconnect()` 调用，并花了两个小时来追踪内存泄漏。每次都使用`try/finally`。

**缓存结果**。获得摘要后，将其存储在问题对象中。如果用户滑开并返回，缓存的版本会立即呈现。无需第二次 API 呼叫，不会浪费金钱，也不会产生额外的延迟。

**AI 可以使维护工作可持续**。分诊是那些让人筋疲力尽的无形任务之一。没有人为此感谢你，而且它堆积得很快。如果您可以将处理 50 问题所需的时间减少一半，那么就有时间进行代码审查、指导，或者只是不用担心您的通知徽章。 Copilot SDK 是一个工具，但更大的想法更重要：看看维护中消耗你精力的部分，并询问 AI 是否可以通过第一遍。

## 自己尝试一下

`@github/copilot-sdk` 为构建智能开发工具提供了真正的可能性。结合 React Native 的跨平台范围，您可以以一种原生且快速的方式将 AI- 支持的工作流程引入移动设备。

如果您正在构建类似的东西，请从我在这里概述的服务器端模式开始。这是实现有效集成的最简单途径，并且可以根据您的应用程序进行扩展。源代码可在GitHub上找到：[AndreaGriffiths11/IssueCrush](<https://github.com/AndreaGriffiths11/IssueCrush>)。

[开始使用 Copilot SDK](<https://github.com/github/copilot-sdk>) 看看您还可以构建什么。 [入门指南](<https://github.com/github/copilot-sdk#getting-started>)将引导您通过大约五行代码完成第一次集成。有反馈或想法吗？加入[SDK讨论](<https://github.com/github/copilot-sdk/discussions>)中的对话。

---

## 标签：

* [AI 集成](<https://github.blog/tag/ai-integration/>)
* [GitHub Copilot SDK](<https://github.blog/tag/github-copilot-sdk/>)
* [GitHub 问题](<https://github.blog/tag/github-issue/>)
* [React 本机](<https://github.blog/tag/react-native/>)

## 撰写者

![安德里亚·格里菲斯](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [安德里亚·格里菲斯](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea 是 GitHub 的高级开发人员倡导者，在开发人员工具方面拥有十多年的经验。她将技术深度与让先进技术更容易获得的使命结合起来。从陆军服役和施工管理转向软件开发后，她以独特的视角将复杂的工程概念与实际实施联系起来。她与威尔士伴侣、两个儿子和两只狗住在佛罗里达州，在那里她继续通过 GitHub 的全球计划推动创新并支持开源。在线查找她@acolombiadev。


## 原文对照

<details>
<summary>展开完整原文</summary>

# Building AI-powered GitHub issue triage with the Copilot SDK

The Copilot SDK lets you add the same AI that powers [Copilot Chat](<https://copilot.microsoft.com/>) to your own applications. I wanted to see what that looks like in practice, so I built an issue triage app called IssueCrush. Here’s what I learned and how you can get started.

If you’ve ever maintained an open source project, or worked on a team with active repositories, you know the feeling. You open GitHub and see that notification badge: 47 issues. Some are bugs, some are feature requests, some are questions that should be discussions, and some are duplicates of issues from three years ago.

The mental overhead of triaging issues is real. Each one requires context-switching: read the title, scan the description, check the labels, think about priority, decide what to do. Multiply that by dozens of issues across multiple repositories, and suddenly your brain is mush.

I wanted to make this faster. And with the GitHub Copilot SDK, I found a way.

## Enter IssueCrush: Swipe right to ship

IssueCrush shows your GitHub issues as swipeable cards. Left to close, right to keep. When you tap “Get AI Summary,” Copilot reads the issue and tells you what it’s about and what to do with it. Instead of reading through every lengthy description, maintainers can get instant, actionable context to make faster triage decisions. Here’s how I integrated the GitHub Copilot SDK to make it happen.

![Screenshot of a developer workspace showing a VS Code editor open to the IssueCrush project, with a package.json file visible and a terminal running a server that logs AI-generated issue summaries using the GitHub Copilot SDK. On the right, an iPhone simulator displays the IssueCrush mobile app, listing a GitHub issue with labels, a progress indicator, and a ‘Get AI Summary’ button, along with swipe-style approve and reject buttons at the bottom.](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.37.43-AM.png?resize=1305%2C835>)

## The architecture challenge

The first technical decision was figuring out where to run the Copilot SDK. React Native apps can’t directly use Node.js packages, and the Copilot SDK requires a Node.js runtime. Internally, the SDK manages a local Copilot CLI process and communicates with it over JSON-RPC. Because of this dependency on the CLI binary and a Node environment, the integration must run server-side rather than directly in a React Native app. This means the server must have the Copilot CLI installed and available on the system PATH.

I settled on a server-side integration pattern:

![Architecture diagram showing a React Native and web client communicating over HTTPS with a Node.js server. The server runs the GitHub Copilot SDK, which manages a local Copilot CLI process via JSON-RPC. The CLI connects to the GitHub Copilot service, while the client separately interacts with GitHub OAuth and the GitHub REST API for issue data.](<https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.39.25-AM.png?resize=1318%2C921>)

Here’s why this setup works:

* **Single SDK instance shared across all clients**, so you’re not spinning up a new connection per mobile client. The server manages one instance for every request. Less overhead, fewer auth handshakes, simpler cleanup.
* **Server-side secrets for Copilot authentication**, to keep credentials secure. Your API tokens never touch the client. They live on the server where they belongnot inside a React Native bundle someone can decompile.
* **Graceful degradation when AI is unavailable**, so you can still triage issues even if the Copilot service goes down or times out. The app falls back to a basic summary. AI makes triage faster, but it shouldn’t be a single point of failure.
* **Logging of requests for debugging and monitoring**, because every prompt and response passes through your server. You can track latency, catch failures, and debug prompt issues without bolting instrumentation onto the mobile client.

Before you build something like this, you need:

1. The [Copilot CLI](<https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli>) installed on your server.
2. A GitHub Copilot subscription, or a [BYOK configuration](<https://github.com/github/copilot-sdk/blob/main/docs/auth/byok.md>) with your own API keys.
3. The Copilot CLI authenticated. Run copilot auth on your server, or set a `COPILOT_GITHUB_TOKEN` environment variable.

## How to implement the Copilot SDK integration

The Copilot SDK uses a session-based model. You start a client (which spawns the CLI process), create a session, send messages, then clean up.

```
const { CopilotClient, approveAll } = await import('@github/copilot-sdk');
 
let client = null; 
let session = null; 
 
try { 
   // 1. Initialize the client (spawns Copilot CLI in server mode) 
   client = new CopilotClient(); 
   await client.start(); 
 
  // 2. Create a session with your preferred model 
  session = await client.createSession({
  model: 'gpt-4.1',
  onPermissionRequest: approveAll,
});
 
  // 3. Send your prompt and wait for response 
   const response = await session.sendAndWait({ prompt }); 
 
  // 4. Extract the content 
   if (response && response.data && response.data.content) { 
     const summary = response.data.content; 
     // Use the summary... 
   } 
 
} finally { 
   // 5. Always clean up 
   if (session) await session.disconnect().catch(() => {}); 
   if (client) await client.stop().catch(() => {}); 
}
```

## Key SDK patterns

### 1. Lifecycle management

The SDK follows a strict lifecycle: `start() → createSession() → sendAndWait() → disconnect() → stop()`.

Here’s something I learned the hard way: failing to clean up sessions leaks resources. I spent two hours debugging memory issues before realizing I’d forgotten a `disconnect()` call. Wrap every session interaction in try/finally. The `.catch(() => {})` on cleanup calls prevents cleanup errors from masking the original error.

### 2. Prompt engineering for triage

Prompt structure gives the model enough context to do its job. I provide structured information about the issue rather than dumping raw text:

```
const prompt = `You are analyzing a GitHub issue to help a developer quickly understand it and decide how to handle it. 
 
Issue Details: 
- Title: ${issue.title} 
- Number: #${issue.number} 
- Repository: ${issue.repository?.full_name || 'Unknown'} 
- State: ${issue.state} 
- Labels: ${issue.labels?.length ? issue.labels.map(l => l.name).join(', ') : 'None'} 
- Created: ${issue.created_at} 
- Author: ${issue.user?.login || 'Unknown'} 
 
Issue Body: 
${issue.body || 'No description provided.'} 
 
Provide a concise 2-3 sentence summary that: 
1. Explains what the issue is about 
2. Identifies the key problem or request 
3. Suggests a recommended action (e.g., "needs investigation", "ready to implement", "assign to backend team", "close as duplicate") 
 
Keep it clear, actionable, and helpful for quick triage. No markdown formatting.`;
```

The labels and author context matter more than you’d think. An issue from a first-time contributor needs different handling than one from a core maintainer, and the AI uses this information to adjust its summary.

### 3. Response handling

The `sendAndWait()` method returns the assistant’s response once the session goes idle. Always validate that the response chain exists before accessing nested properties:

```
const response = await session.sendAndWait({ prompt }, 30000); // 30 second timeout 
 
let summary; 
if (response && response.data && response.data.content) { 
   summary = response.data.content; 
} else { 
   throw new Error('No content received from Copilot'); 
}
```

The second argument to `sendAndWait()` is a timeout in milliseconds. Set it high enough for complex issues but low enough that users aren’t staring at a spinner. I’ve seen enough “undefined is not an object” errors to know you should never skip the null checks on the response chain.

## Client-side service layer

On the React Native side, I wrap the API calls in a service class that handles initialization and error states:

```
// src/lib/copilotService.ts 
import type { GitHubIssue } from '../api/github'; 
import { getToken } from './tokenStorage'; 
export interface SummaryResult { 
   summary: string; 
   fallback?: boolean; 
   requiresCopilot?: boolean; 
} 
 
export class CopilotService { 
   private backendUrl = process.env.EXPO_PUBLIC_API_URL || 'http://localhost:3000'; 
 
  async initialize(): Promise<{ copilotMode: string }> { 
     try { 
       const response = await fetch(`${this.backendUrl}/health`); 
       const data = await response.json(); 
 
      console.log('Backend health check:', data); 
       return { copilotMode: data.copilotMode || 'unknown' }; 
     } catch (error) { 
       console.error('Failed to connect to backend:', error); 
       throw new Error('Backend server not available'); 
     } 
   } 
 
  async summarizeIssue(issue: GitHubIssue): Promise<SummaryResult> { 
     try { 
       const token = await getToken(); 
 
      if (!token) { 
         throw new Error('No GitHub token available'); 
       } 
 
      const response = await fetch(`${this.backendUrl}/api/ai-summary`, { 
         method: 'POST', 
         headers: { 
           'Content-Type': 'application/json', 
         }, 
         body: JSON.stringify({ issue, token }), 
       }); 
 
      const data = await response.json(); 
 
      if (!response.ok) { 
         if (response.status === 403 && data.requiresCopilot) { 
           return { 
             summary: data.message || 'AI summaries require a GitHub Copilot subscription.', 
             requiresCopilot: true, 
           }; 
         } 
         throw new Error(data.error || 'Failed to generate summary'); 
       } 
 
      return { 
         summary: data.summary || 'Unable to generate summary', 
         fallback: data.fallback || false, 
       }; 
     } catch (error) { 
       console.error('Copilot summarization error:', error); 
       throw error; 
     } 
   } 
} 
 
export const copilotService = new CopilotService();
```

## React Native integration

The UI is straightforward React state management. Tap the button, call the service, cache the result:

```
const [loadingAiSummary, setLoadingAiSummary] = useState(false); 
 
const handleGetAiSummary = async () => { 
   const issue = issues[currentIndex]; 
   if (!issue || issue.aiSummary) return; 
 
  setLoadingAiSummary(true); 
   try { 
     const result = await copilotService.summarizeIssue(issue); 
     setIssues(prevIssues => 
       prevIssues.map((item, index) => 
         index === currentIndex ? { ...item, aiSummary: result.summary } : item 
       ) 
     ); 
   } catch (error) { 
     console.error('AI Summary error:', error); 
   } finally { 
     setLoadingAiSummary(false); 
   } 
};
```

Once a summary exists on the issue object, the card swaps the button for the summary text. If the user swipes away and comes back, the cached version renders instantly. No second API call needed.

## Graceful degradation

AI services can fail. Network issues, rate limits, and service outages happen. The server handles two failure modes: subscription errors return a 403 so the client can show a clear message, and everything else falls back to a summary built from issue metadata.

```
} catch (error) { 
   // Clean up on error 
   try { 
     if (session) await session.disconnect().catch(() => {}); 
     if (client) await client.stop().catch(() => {}); 
   } catch (cleanupError) { 
     // Ignore cleanup errors 
   } 
 
  const errorMessage = error.message.toLowerCase(); 
 
  // Copilot subscription errors get a clear 403 
   if (errorMessage.includes('unauthorized') || 
       errorMessage.includes('forbidden') || 
       errorMessage.includes('copilot') || 
       errorMessage.includes('subscription')) { 
     return res.status(403).json({ 
       error: 'Copilot access required', 
       message: 'AI summaries require a GitHub Copilot subscription.', 
       requiresCopilot: true 
     }); 
   } 
 
  // Everything else falls back to a metadata-based summary 
   const fallbackSummary = generateFallbackSummary(issue); 
   res.json({ summary: fallbackSummary, fallback: true }); 
}
```

The fallback builds a useful summary from what we already have:

```
function generateFallbackSummary(issue) { 
   const parts = [issue.title]; 
 
  if (issue.labels?.length) { 
     parts.push(`\nLabels: ${issue.labels.map(l => l.name).join(', ')}`); 
   } 
 
  if (issue.body) { 
     const firstSentence = issue.body.split(/[.!?]\s/)[0]; 
     if (firstSentence && firstSentence.length < 200) { 
       parts.push(`\n\n${firstSentence}.`); 
     } 
   } 
 
  parts.push('\n\nReview the full issue details to determine next steps.'); 
   return parts.join(''); 
}
```

## A few other patterns worth noting

The server exposes a `/health` endpoint that signals AI availability. Clients check it on startup and hide the summary button entirely if the backend can’t support it. No broken buttons.

Summaries are generated on -demand, not preemptively. This keeps API costs down and avoids wasted calls when users swipe past an issue without reading it.

The SDK is loaded with await `import('@github/copilot-sdk')` instead of a top-level require. This lets the server start even if the SDK has issues, which makes deployment and debugging smoother.

## Dependencies

```
{ 
   "dependencies": { 
     "@github/copilot-sdk": "^0.1.14", 
     "express": "^5.2.1" 
   } 
}
```

The SDK communicates with the Copilot CLI process via JSON-RPC. You need the [Copilot CLI](<https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli>) installed and available in your PATH. Check the [SDK’s package requirements](<https://github.com/github/copilot-sdk>) for the minimum Node.js version.

## What I learned building this

**Server-side is the right call**. The SDK needs the Copilot CLI binary, and you’re not installing that on a phone. Running it on a server keeps AI logic in one place, simplifies the mobile client, and means credentials never leave the backend.

**Prompt structure matters more than prompt length**. Feeding the model organized metadata like title, labels, and author produces much better summaries than dumping the entire issue body as raw text. Give the model something to work with, and it’ll give you something useful back.

**Always have a fallback**. AI services go down. Rate limits happen. Design for graceful degradation from day one. Your users should still be able to triage issues even if the AI piece is offline.

**Clean up your sessions**. The SDK requires explicit cleanup: `disconnect()` then `stop()`. I skipped a `disconnect()` call once and spent two hours chasing a memory leak. Use `try/finally` every time.

**Cache the results**. Once you have a summary, store it on the issue object. If the user swipes away and comes back, the cached version renders instantly. No second API call, no wasted money, no extra latency.

**AI can make maintainership sustainable**. Triage is one of those invisible tasks that burns people out. Nobody thanks you for it, and it piles up fast. If you can cut the time it takes to process 50 issues in half, that’s time back for code review, mentoring, or just not dreading your notification badge. The Copilot SDK is one tool, but the bigger idea matters more: look at the parts of maintaining that drain you and ask if AI can take a first pass.

## Try it yourself

The `@github/copilot-sdk` opens real possibilities for building intelligent developer tools. Combined with React Native’s cross-platform reach, you can bring AI-powered workflows to mobile in a way that feels native and fast.

If you’re building something similar, start with the server-side pattern I’ve outlined here. It’s the simplest path to a working integration, and it scales with your app. The source code is available on GitHub: [AndreaGriffiths11/IssueCrush](<https://github.com/AndreaGriffiths11/IssueCrush>).

[Get started with the Copilot SDK](<https://github.com/github/copilot-sdk>) to see what else you can build. The [Getting Started guide](<https://github.com/github/copilot-sdk#getting-started>) walks you through your first integration in about five lines of code. Have feedback or ideas? Join the conversation in the [SDK discussions](<https://github.com/github/copilot-sdk/discussions>).

---

## Tags:

* [AI integration](<https://github.blog/tag/ai-integration/>)
* [GitHub Copilot SDK](<https://github.blog/tag/github-copilot-sdk/>)
* [GitHub Issue](<https://github.blog/tag/github-issue/>)
* [React Native](<https://github.blog/tag/react-native/>)

## Written by

![Andrea Griffiths](<https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg>)

### [Andrea Griffiths](<https://github.blog/author/andreagriffiths11/>)

[@AndreaGriffiths11](<https://github.com/andreagriffiths11>)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.


</details>
