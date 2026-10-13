# Building AI-powered GitHub issue triage with the Copilot SDK

The Copilot SDK lets you add the same AI that powers [Copilot Chat](https://copilot.microsoft.com/) to your own applications. I wanted to see what that looks like in practice, so I built an issue triage app called IssueCrush. Here’s what I learned and how you can get started.

If you’ve ever maintained an open source project, or worked on a team with active repositories, you know the feeling. You open GitHub and see that notification badge: 47 issues. Some are bugs, some are feature requests, some are questions that should be discussions, and some are duplicates of issues from three years ago.

The mental overhead of triaging issues is real. Each one requires context-switching: read the title, scan the description, check the labels, think about priority, decide what to do. Multiply that by dozens of issues across multiple repositories, and suddenly your brain is mush.

I wanted to make this faster. And with the GitHub Copilot SDK, I found a way.

## Enter IssueCrush: Swipe right to ship

IssueCrush shows your GitHub issues as swipeable cards. Left to close, right to keep. When you tap “Get AI Summary,” Copilot reads the issue and tells you what it’s about and what to do with it. Instead of reading through every lengthy description, maintainers can get instant, actionable context to make faster triage decisions. Here’s how I integrated the GitHub Copilot SDK to make it happen.

![Screenshot of a developer workspace showing a VS Code editor open to the IssueCrush project, with a package.json file visible and a terminal running a server that logs AI-generated issue summaries using the GitHub Copilot SDK. On the right, an iPhone simulator displays the IssueCrush mobile app, listing a GitHub issue with labels, a progress indicator, and a ‘Get AI Summary’ button, along with swipe-style approve and reject buttons at the bottom.](https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.37.43-AM.png?resize=1305%2C835)

## The architecture challenge

The first technical decision was figuring out where to run the Copilot SDK. React Native apps can’t directly use Node.js packages, and the Copilot SDK requires a Node.js runtime. Internally, the SDK manages a local Copilot CLI process and communicates with it over JSON-RPC. Because of this dependency on the CLI binary and a Node environment, the integration must run server-side rather than directly in a React Native app. This means the server must have the Copilot CLI installed and available on the system PATH.

I settled on a server-side integration pattern:

![Architecture diagram showing a React Native and web client communicating over HTTPS with a Node.js server. The server runs the GitHub Copilot SDK, which manages a local Copilot CLI process via JSON-RPC. The CLI connects to the GitHub Copilot service, while the client separately interacts with GitHub OAuth and the GitHub REST API for issue data.](https://github.blog/wp-content/uploads/2026/03/Screenshot-2026-03-19-at-11.39.25-AM.png?resize=1318%2C921)

Here’s why this setup works:

* **Single SDK instance shared across all clients**, so you’re not spinning up a new connection per mobile client. The server manages one instance for every request. Less overhead, fewer auth handshakes, simpler cleanup.
* **Server-side secrets for Copilot authentication**, to keep credentials secure. Your API tokens never touch the client. They live on the server where they belongnot inside a React Native bundle someone can decompile.
* **Graceful degradation when AI is unavailable**, so you can still triage issues even if the Copilot service goes down or times out. The app falls back to a basic summary. AI makes triage faster, but it shouldn’t be a single point of failure.
* **Logging of requests for debugging and monitoring**, because every prompt and response passes through your server. You can track latency, catch failures, and debug prompt issues without bolting instrumentation onto the mobile client.

Before you build something like this, you need:

1. The [Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) installed on your server.
2. A GitHub Copilot subscription, or a [BYOK configuration](https://github.com/github/copilot-sdk/blob/main/docs/auth/byok.md) with your own API keys.
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

The SDK communicates with the Copilot CLI process via JSON-RPC. You need the [Copilot CLI](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli) installed and available in your PATH. Check the [SDK’s package requirements](https://github.com/github/copilot-sdk) for the minimum Node.js version.

## What I learned building this

**Server-side is the right call**. The SDK needs the Copilot CLI binary, and you’re not installing that on a phone. Running it on a server keeps AI logic in one place, simplifies the mobile client, and means credentials never leave the backend.

**Prompt structure matters more than prompt length**. Feeding the model organized metadata like title, labels, and author produces much better summaries than dumping the entire issue body as raw text. Give the model something to work with, and it’ll give you something useful back.

**Always have a fallback**. AI services go down. Rate limits happen. Design for graceful degradation from day one. Your users should still be able to triage issues even if the AI piece is offline.

**Clean up your sessions**. The SDK requires explicit cleanup: `disconnect()` then `stop()`. I skipped a `disconnect()` call once and spent two hours chasing a memory leak. Use `try/finally` every time.

**Cache the results**. Once you have a summary, store it on the issue object. If the user swipes away and comes back, the cached version renders instantly. No second API call, no wasted money, no extra latency.

**AI can make maintainership sustainable**. Triage is one of those invisible tasks that burns people out. Nobody thanks you for it, and it piles up fast. If you can cut the time it takes to process 50 issues in half, that’s time back for code review, mentoring, or just not dreading your notification badge. The Copilot SDK is one tool, but the bigger idea matters more: look at the parts of maintaining that drain you and ask if AI can take a first pass.

## Try it yourself

The `@github/copilot-sdk` opens real possibilities for building intelligent developer tools. Combined with React Native’s cross-platform reach, you can bring AI-powered workflows to mobile in a way that feels native and fast.

If you’re building something similar, start with the server-side pattern I’ve outlined here. It’s the simplest path to a working integration, and it scales with your app. The source code is available on GitHub: [AndreaGriffiths11/IssueCrush](https://github.com/AndreaGriffiths11/IssueCrush).

[Get started with the Copilot SDK](https://github.com/github/copilot-sdk) to see what else you can build. The [Getting Started guide](https://github.com/github/copilot-sdk#getting-started) walks you through your first integration in about five lines of code. Have feedback or ideas? Join the conversation in the [SDK discussions](https://github.com/github/copilot-sdk/discussions).

---

## Tags:

* [AI integration](https://github.blog/tag/ai-integration/)
* [GitHub Copilot SDK](https://github.blog/tag/github-copilot-sdk/)
* [GitHub Issue](https://github.blog/tag/github-issue/)
* [React Native](https://github.blog/tag/react-native/)

## Written by

![Andrea Griffiths](https://github.blog/wp-content/uploads/2025/08/Andrea-Griffiths_avatar_1755783168-200x200.jpeg)

### [Andrea Griffiths](https://github.blog/author/andreagriffiths11/)

[@AndreaGriffiths11](https://github.com/andreagriffiths11)

Andrea is a Senior Developer Advocate at GitHub with over a decade of experience in developer tools. She combines technical depth with a mission to make advanced technologies more accessible. After transitioning from Army service and construction management to software development, she brings a unique perspective to bridging complex engineering concepts with practical implementation. She lives in Florida with her Welsh partner, two sons, and two dogs, where she continues to drive innovation and support open source through GitHub's global initiatives. Find her online @acolombiadev.
