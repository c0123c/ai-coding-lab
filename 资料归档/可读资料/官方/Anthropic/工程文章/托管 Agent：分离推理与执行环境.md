# 托管 Agent：分离推理与执行环境

介绍把模型推理、会话状态和执行环境分离的托管架构，解释环境恢复与并行工作的设计，并讨论哪些运行框架假设会随模型能力变化。

- 来源：Anthropic／工程文章
- 资料类型：存档文档
- 文档时间：发表：[2026-04-08](<https://www.anthropic.com/engineering/managed-agents>)；更新：[2026-04-24](<https://www.anthropic.com/engineering/managed-agents>)；本地抓取：2026-10-11
- 阅读状态：完整中文机器译文，由 Google 翻译；代码、命令与配置保留原样，未逐句人工校对。

[原文发布页](<https://www.anthropic.com/engineering/managed-agents>) · [原始文件](<../../../../原始抓取备份/原文/官方/Anthropic/工程文章/托管 Agent：分离推理与执行环境.md>) · [返回资料总览](<../../../资料总览.md>)

[查看原文对照](#原文对照)

## 中文全文

*开始使用Claude按照我们的[文档](<https://platform.claude.com/docs/en/managed-agents/overview>)管理Agents。*
  
A running topic on the Engineering Blog is how to [build effective agents](<https://www.anthropic.com/engineering/building-effective-agents>) and [design harnesses](<https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>) for [long-running work](<https://www.anthropic.com/engineering/harness-design-long-running-apps>).这项工作的一个共同点是利用关于 Claude 本身无法完成的事情的编码假设。然而，这些假设需要经常受到质疑，因为随着模型的改进，它们可能会[变得陈旧](<http://www.incompleteideas.net/IncIdeas/BitterLesson.html>)。

As just one example, in prior work [we found](<https://www.anthropic.com/engineering/harness-design-long-running-apps>) that Claude Sonnet 4.5 would wrap up tasks prematurely as it sensed its context limit approaching—a behavior sometimes called “context anxiety.”我们通过向线束添加上下文重置来解决这个问题。但当我们在 Claude Opus 4.5 上使用相同的线束时，我们发现该行为消失了。重置已经成为沉重的负担。

我们预计安全带将继续发展。 So we built Managed Agents: a hosted service in the Claude Platform that runs long-horizon agents on your behalf through a small set of interfaces meant to outlast any particular implementation—including the ones we run today.

Building Managed Agents meant solving an old problem in computing: how to design a system for “[programs as yet unthought of](<http://www.catb.org/esr/writings/taoup/html/ch03s01.html>).” Decades ago, operating systems solved this problem by virtualizing hardware into abstractions—*process, file*—general enough for programs that didn't exist yet.抽象比硬件更持久。 `read()` 命令不知道它访问的是 20 世纪 70 年代的磁盘包还是现代的 SSD。顶层的抽象保持稳定，而底层的实现则自由变化。

托管Agents遵循相同的模式。 We virtualized the components of an agent: a session (the append-only log of everything that happened), a harness (the loop that calls Claude and routes Claude’s tool calls to the relevant infrastructure), and a sandbox (an execution environment where Claude can run code and edit files).这允许交换每个的实现而不干扰其他的实现。我们对这些界面的形状有自己的看法，而不是它们背后运行的内容。

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F903b624ada206b10753a24c6a1367e74a869165d-1080x1080.png&w=3840&q=75>)

## 不要领养宠物

我们首先将所有 agent 组件放入一个容器中，这意味着会话、agent 工具和沙箱都共享一个环境。这种方法有很多好处，包括文件编辑是直接的系统调用，并且没有需要设计的服务边界。

但是，通过将所有内容耦合到一个容器中，我们遇到了一个旧的基础设施问题：我们采用了[*宠物*](<https://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/>)。在宠物与牛的类比中，宠物是一个有名字的、手工照料的个体，你不能失去它，而牛是可以互换的。在我们的例子中，服务器变成了那个宠物；如果容器发生故障，会话就会丢失。如果容器没有响应，我们必须将其恢复到正常状态。

护理容器意味着调试无响应的卡住会话。我们唯一的窗口是 WebSocket 事件流，但这无法告诉我们 * 哪里出现 * 故障，这意味着线束中的错误、事件流中的数据包丢失或容器离线都呈现相同的情况。为了找出问题所在，工程师必须在容器内打开一个 shell，但由于该容器通常还保存用户数据，这种方法本质上意味着我们缺乏调试能力。

第二个问题是，该安全带假定 Claude 所做的任何事情都与它一起存在于容器中。 When customers asked us to connect Claude to their virtual private cloud, they had to either peer their network with ours, or run our harness in their own environment.当我们想要将线束连接到不同的基础设施时，线束中的假设就成为了一个问题。

## 将大脑与双手分离

The solution we arrived at was to decouple what we thought of as the “brain” (Claude and its harness) from both the “hands” (sandboxes and tools that perform actions) and the “session” (the log of session events).每个接口都成为一个对其他接口几乎不做任何假设的接口，并且每个接口都可能失败或被独立替换。

**The harness leaves the container.** Decoupling the brain from the hands meant the harness no longer lived inside the container.它以调用任何其他工具的方式调用容器：`execute(name, input) → string`。容器变成了牛。如果容器失效，线束会将故障捕获为工具调用错误，并将其传递回 Claude。如果 Claude 决定重试，则可以使用标准配方重新初始化新容器：`provision({resources})`。我们不再需要护理失效的容器使其恢复健康。

**从线束故障中恢复。**线束也变成了牛。由于会话日志位于线束外部，因此线束中的任何内容都不需要在崩溃时幸存下来。当其中一个失败时，可以使用 `wake(sessionId)` 重新启动新的事件，使用 `getSession(id)` 取回事件日志，并从上一个事件恢复。在 agent 循环期间，线束会使用 `emitEvent(id, event)` 写入会话，以保留事件的持久记录。

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F73e900af5b9d6ed8c64db0a8e74d4465963556b7-1640x1596.png&w=3840&q=75>)

**安全边界。**在耦合设计中，Claude生成的任何不受信任的代码都在与凭据相同的容器中运行，因此提示注入只需说服Claude读取其自己的环境即可。一旦攻击者拥有这些令牌，他们就可以产生新的、不受限制的会话并将工作委托给他们。 Narrow scoping is an obvious mitigation, but this encodes an assumption about what Claude can't do with a limited token—and Claude is getting increasingly smart.结构性修复是为了确保永远无法从运行 Claude 生成的代码的沙箱访问代币。

我们使用两种模式来确保这一点。身份验证可以与资源捆绑在一起，也可以保存在沙箱外部的保管库中。对于 Git，我们使用每个存储库的访问令牌在沙箱初始化期间克隆存储库并将其连接到本地 git 远程。 Git `push` 和 `pull` 在沙箱内部工作，而 agent 本身并不处理令牌。对于自定义工具，我们支持 MCP 并将 OAuth 令牌存储在安全保管库中。 Claude通过专用代理调用MCP工具；该代理接收与会话关联的令牌。然后，代理可以从保管库中获取相应的凭据并调用外部服务。安全带永远不会知道任何凭证。

## 该会话不是 Claude 的上下文窗口

Long-horizon tasks often exceed the length of Claude’s context window, and the standard ways to address this all involve irreversible decisions about what to keep.我们在 [ 之前关于上下文工程的工作 ](<https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>) 中探索了这些技术。 For example, compaction lets Claude save a summary of its context window and the memory tool lets Claude write context to files, enabling learning across sessions.这可以与上下文修剪配合使用，后者有选择地删除旧工具结果或思维块等标记。

但有选择地保留或丢弃上下文的不可逆转的决定可能会导致失败。很难知道未来的轮次需要哪些代币。 If messages are transformed by a compaction step, the harness removes compacted messages from Claude’s context window, and these are recoverable only if they are stored. Prior work [has explored](<https://arxiv.org/pdf/2512.24601>) ways to address this by storing context as an object that lives *outside* the context window.例如，上下文可以是 REPL 中的一个对象，LLM 通过编写代码来过滤或切片它以编程方式访问。

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2Fcf0719d7832b1f577b7393c84a7c53eecc725ca4-760x200.png&w=1920&q=75>)

在托管 Agents 中，会话提供了同样的好处，充当位于 Claude 上下文窗口之外的上下文对象。但上下文不是存储在沙箱或 REPL 中，而是持久存储在会话日志中。界面 `getEvents(),` 允许大脑通过选择事件流的位置切片来询问上下文。 The interface can be used flexibly, allowing the brain to pick up from wherever it last stopped reading, rewinding a few events before a specific moment to see the lead up, or rereading context before a specific action.

任何获取的事件也可以在传递到 Claude 的上下文窗口之前在线束中进行转换。 These transformations can be whatever the harness encodes, including context organization to achieve a high prompt cache hit rate and context engineering. We separated the concerns of recoverable context storage in the session and arbitrary context management in the harness because we can’t predict what specific context engineering will be required in future models.这些接口将上下文管理推入工具中，并且仅保证会话持久且可用于询问。

## 多动脑子，多动手

**很多大脑。**将大脑与双手分离解决了我们最早的客户投诉之一。 When teams wanted Claude to work against resources in their own VPC, the only path was to peer their network with ours, because the container holding the harness assumed every resource sat next to it.一旦安全带不再位于容器中，这种假设就消失了。同样的改变也带来了性能回报。当我们最初将大脑放入容器中时，这意味着许多大脑需要同样多的容器。对于每个大脑来说，在配置该容器之前不会进行任何推理。每个会话都预先支付了全部容器设置成本。每个会话，即使是那些永远不会接触沙箱的会话，都必须克隆存储库，启动进程，从我们的服务器获取挂起的事件。

That dead time is expressed in time-to-first-token (TTFT), which measures how long a session waits between accepting work and producing its first response token. TTFT 是用户 * 最明显感受到的延迟 *。

将大脑与双手分离意味着仅在需要时才由大脑通过调用 `(execute(name, input) → string)` 的工具来配置容器。因此，不需要立即使用容器的会话不会等待容器。一旦编排层从会话日志中提取待处理事件，推理就可以开始。使用这种架构，我们的 p50 TTFT 大约下降了 60%，p95 下降了 90%。扩展到许多大脑只意味着启动许多无状态的安全带，并仅在需要时将它们连接到手。

**许多手。**我们还希望能够将每个大脑连接到许多手。实际上，这意味着 Claude 必须推理许多执行环境并决定将工作发送到哪里——这是比在单个 shell 中操作更困难的认知任务。我们从单个容器中的大脑开始，因为早期的模型无法做到这一点。随着智力的扩展，单个容器反而成为了限制：当该容器发生故障时，我们就会失去大脑所触及的每只手的状态。

将大脑与手解耦，使每只手都成为工具，`execute(name, input) → string`：输入名称和输入，返回一个字符串。该界面支持任何自定义工具、任何 MCP 服务器以及我们自己的工具。线束不知道沙箱是容器、手机还是神奇宝贝模拟器。而且由于没有一只手与任何大脑相连，因此大脑可以将手相互传递。

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F4f67b1c10566552aec514a716ea43544ab330e0b-668x243.png&w=1920&q=75>)

## 结论

我们面临的挑战是一个老问题：如何为“尚未想到的程序”设计一个系统。通过将硬件虚拟化为对于尚不存在的程序来说足够通用的抽象，操作系统已经存在了数十年。通过托管 Agents，我们的目标是设计一个系统，以适应未来的线束、沙箱或 Claude 周围的其他组件。

托管 Agents 是一个具有相同精神的元安全带，对 Claude 将来需要的 * 特定 * 安全带没有意见。相反，它是一个具有通用接口的系统，允许许多不同的线束。例如，Claude Code 是一个出色的工具，我们在各种任务中广泛使用。我们还展示了特定任务的 agent 在狭窄的领域中发挥着卓越的作用。托管 Agents 可以容纳其中任何一个，随着时间的推移，匹配 Claude 的智能。

元线束设计意味着对 Claude 周围的接口有自己的看法：我们预计 Claude 将需要具有操纵状态（会话）和执行计算（沙箱）的能力。我们还预计 Claude 将需要扩展到许多大脑和许多手的能力。我们设计了这些接口，以便这些接口可以长期可靠、安全地运行。但我们不对 Claude 需要的大脑或手的数量或位置做出任何假设。

## 致谢

由兰斯·马丁、加布·塞马吉和迈克尔·科恩撰写。感谢 Nodir Turakulov 和 Jeremy Fox 就这些主题进行了有益的对话。特别感谢 Agents API 团队和 Jake Eaton 的贡献。


## 原文对照

<details>
<summary>展开完整原文</summary>

*Get started with Claude Managed Agents by following our [docs](<https://platform.claude.com/docs/en/managed-agents/overview>).*  
  
A running topic on the Engineering Blog is how to [build effective agents](<https://www.anthropic.com/engineering/building-effective-agents>) and [design harnesses](<https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>) for [long-running work](<https://www.anthropic.com/engineering/harness-design-long-running-apps>). A common thread across this work is that harnesses encode assumptions about what Claude can’t do on its own. However, those assumptions need to be frequently questioned because they can [go stale](<http://www.incompleteideas.net/IncIdeas/BitterLesson.html>) as models improve.

As just one example, in prior work [we found](<https://www.anthropic.com/engineering/harness-design-long-running-apps>) that Claude Sonnet 4.5 would wrap up tasks prematurely as it sensed its context limit approaching—a behavior sometimes called “context anxiety.” We addressed this by adding context resets to the harness. But when we used the same harness on Claude Opus 4.5, we found that the behavior was gone. The resets had become dead weight.

We expect harnesses to continue evolving. So we built Managed Agents: a hosted service in the Claude Platform that runs long-horizon agents on your behalf through a small set of interfaces meant to outlast any particular implementation—including the ones we run today.

Building Managed Agents meant solving an old problem in computing: how to design a system for “[programs as yet unthought of](<http://www.catb.org/esr/writings/taoup/html/ch03s01.html>).” Decades ago, operating systems solved this problem by virtualizing hardware into abstractions—*process, file*—general enough for programs that didn't exist yet. The abstractions outlasted the hardware. The `read()` command is agnostic as to whether it’s accessing a disk pack from the 1970s or a modern SSD. The abstractions on top stayed stable while the implementations underneath changed freely.

Managed Agents follow the same pattern. We virtualized the components of an agent: a session (the append-only log of everything that happened), a harness (the loop that calls Claude and routes Claude’s tool calls to the relevant infrastructure), and a sandbox (an execution environment where Claude can run code and edit files). This allows the implementation of each to be swapped without disturbing the others. We're opinionated about the shape of these interfaces, not about what runs behind them.

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F903b624ada206b10753a24c6a1367e74a869165d-1080x1080.png&w=3840&q=75>)

## Don’t adopt a pet

We started by placing all agent components into a single container, which meant the session, agent harness, and sandbox all shared an environment. There were benefits to this approach, including that file edits are direct syscalls, and there were no service boundaries to design.

But by coupling everything into one container, we ran into an old infrastructure problem: we’d adopted a [*pet*](<https://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/>). In the pets-vs-cattle analogy, a pet is a named, hand-tended individual you can’t afford to lose, while cattle are interchangeable. In our case, the server became that pet; if a container failed, the session was lost. If a container was unresponsive, we had to nurse it back to health.

Nursing containers meant debugging unresponsive stuck sessions. Our only window in was the WebSocket event stream, but that couldn’t tell us *where* failures arose, which meant that a bug in the harness, a packet drop in the event stream, or a container going offline all presented the same. To figure out what went wrong, an engineer had to open a shell inside the container, but because that container often also held user data, that approach essentially meant we lacked the ability to debug.

A second issue was that the harness assumed that whatever Claude worked on lived in the container with it. When customers asked us to connect Claude to their virtual private cloud, they had to either peer their network with ours, or run our harness in their own environment. An assumption baked into the harness became a problem when we wanted to connect it to different infrastructure.

## Decouple the brain from the hands

The solution we arrived at was to decouple what we thought of as the “brain” (Claude and its harness) from both the “hands” (sandboxes and tools that perform actions) and the “session” (the log of session events). Each became an interface that made few assumptions about the others, and each could fail or be replaced independently.

**The harness leaves the container.** Decoupling the brain from the hands meant the harness no longer lived inside the container. It called the container the way it called any other tool: `execute(name, input) → string`. The container became cattle. If the container died, the harness caught the failure as a tool-call error and passed it back to Claude. If Claude decided to retry, a new container could be reinitialized with a standard recipe: `provision({resources})`. We no longer had to nurse failed containers back to health.

**Recovering from harness failure.** The harness also became cattle. Because the session log sits outside the harness, nothing in the harness needs to survive a crash. When one fails, a new one can be rebooted with `wake(sessionId)`, use `getSession(id)` to get back the event log, and resume from the last event. During the agent loop, the harness writes to the session with `emitEvent(id, event)` in order to keep a durable record of events.

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F73e900af5b9d6ed8c64db0a8e74d4465963556b7-1640x1596.png&w=3840&q=75>)

**The security boundary.** In the coupled design, any untrusted code that Claude generated was run in the same container as credentials—so a prompt injection only had to convince Claude to read its own environment. Once an attacker has those tokens, they can spawn fresh, unrestricted sessions and delegate work to them. Narrow scoping is an obvious mitigation, but this encodes an assumption about what Claude can't do with a limited token—and Claude is getting increasingly smart. The structural fix was to make sure the tokens are never reachable from the sandbox where Claude’s generated code runs.

We used two patterns to ensure this. Auth can be bundled with a resource or held in a vault outside the sandbox. For Git, we use each repository’s access token to clone the repo during sandbox initialization and wire it into the local git remote. Git `push` and `pull` work from inside the sandbox without the agent ever handling the token itself. For custom tools, we support MCP and store OAuth tokens in a secure vault. Claude calls MCP tools via a dedicated proxy; this proxy takes in a token associated with the session. The proxy can then fetch the corresponding credentials from the vault and make the call to the external service. The harness is never made aware of any credentials.

## The session is not Claude’s context window

Long-horizon tasks often exceed the length of Claude’s context window, and the standard ways to address this all involve irreversible decisions about what to keep. We’ve explored these techniques in [prior work](<https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>) on context engineering. For example, compaction lets Claude save a summary of its context window and the memory tool lets Claude write context to files, enabling learning across sessions. This can be paired with context trimming, which selectively removes tokens such as old tool results or thinking blocks.

But irreversible decisions to selectively retain or discard context can lead to failures. It is difficult to know which tokens the future turns will need. If messages are transformed by a compaction step, the harness removes compacted messages from Claude’s context window, and these are recoverable only if they are stored. Prior work [has explored](<https://arxiv.org/pdf/2512.24601>) ways to address this by storing context as an object that lives *outside* the context window. For example, context can be an object in a REPL that the LLM programmatically accesses by writing code to filter or slice it.

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2Fcf0719d7832b1f577b7393c84a7c53eecc725ca4-760x200.png&w=1920&q=75>)

In Managed Agents, the session provides this same benefit, serving as a context object that lives outside Claude’s context window. But rather than be stored within the sandbox or REPL, context is durably stored in the session log. The interface, `getEvents(),` allows the brain to interrogate context by selecting positional slices of the event stream. The interface can be used flexibly, allowing the brain to pick up from wherever it last stopped reading, rewinding a few events before a specific moment to see the lead up, or rereading context before a specific action.

Any fetched events can also be transformed in the harness before being passed to Claude’s context window. These transformations can be whatever the harness encodes, including context organization to achieve a high prompt cache hit rate and context engineering. We separated the concerns of recoverable context storage in the session and arbitrary context management in the harness because we can’t predict what specific context engineering will be required in future models. The interfaces push that context management into the harness, and only guarantee that the session is durable and available for interrogation.

## Many brains, many hands

**Many brains.** Decoupling the brain from the hands solved one of our earliest customer complaints. When teams wanted Claude to work against resources in their own VPC, the only path was to peer their network with ours, because the container holding the harness assumed every resource sat next to it. Once the harness was no longer in the container, that assumption went away. The same change had a performance payoff. When we initially put the brain in a container, it meant that many brains required as many containers. For each brain, no inference could happen until that container was provisioned; every session paid the full container setup cost up front. Every session, even ones that would never touch the sandbox, had to clone the repo, boot the process, fetch pending events from our servers.

That dead time is expressed in time-to-first-token (TTFT), which measures how long a session waits between accepting work and producing its first response token. TTFT is the latency the user most acutely *feels*.

Decoupling the brain from the hands means that containers are provisioned by the brain via a tool call `(execute(name, input) → string)` only if they are needed. So a session that didn't need a container right away didn't wait for one. Inference could start as soon as the orchestration layer pulled pending events from the session log. Using this architecture, our p50 TTFT dropped roughly 60% and p95 dropped over 90%. Scaling to many brains just meant starting many stateless harnesses, and connecting them to hands only if needed.

**Many hands.** We also wanted the ability to connect each brain to many hands. In practice, this means Claude must reason about many execution environments and decide where to send work—a harder cognitive task than operating in a single shell. We started with the brain in a single container because earlier models weren't capable of this. As intelligence scaled, the single container became the limitation instead: when that container failed, we lost state for every hand that the brain was reaching into.

Decoupling the brain from the hands makes each hand a tool, `execute(name, input) → string`: a name and input go in, and a string is returned. That interface supports any custom tool, any MCP server, and our own tools. The harness doesn’t know whether the sandbox is a container, a phone, or a Pokémon emulator. And because no hand is coupled to any brain, brains can pass hands to one another.

![](<https://www.anthropic.com/_next/image?url=https%3A%2F%2Fwww-cdn.anthropic.com%2Fimages%2F4zrzovbb%2Fwebsite%2F4f67b1c10566552aec514a716ea43544ab330e0b-668x243.png&w=1920&q=75>)

## Conclusion

The challenge we faced is an old one: how to design a system for “programs as yet unthought of.” Operating systems have lasted decades by virtualizing the hardware into abstractions general enough for programs that didn't exist yet. With Managed Agents, we aimed to design a system that accommodates future harnesses, sandboxes, or other components around Claude.

Managed Agents is a meta-harness in the same spirit, unopinionated about the *specific* harness that Claude will need in the future. Rather, it is a system with general interfaces that allow many different harnesses. For example, Claude Code is an excellent harness that we use widely across tasks. We’ve also shown that task-specific agent harnesses excel in narrow domains. Managed Agents can accommodate any of these, matching Claude’s intelligence over time.

Meta-harness design means being opinionated about the interfaces around Claude: we expect that Claude will need the ability to manipulate state (the session) and perform computation (the sandbox). We also expect that Claude will require the ability to scale to many brains and many hands. We designed the interfaces so that these can be run reliably and securely over long time horizons. But we make no assumptions about the number or location of brains or hands that Claude will need.

## Acknowledgements

Written by Lance Martin, Gabe Cemaj, and Michael Cohen. Thanks to Nodir Turakulov and Jeremy Fox for helpful conversations on these topics. Special thanks to the Agents API team and Jake Eaton for their contributions.


</details>
