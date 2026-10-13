Subagents | Gemini CLI
  Skip to content   
Unpaid tier and Google One users: Gemini CLI was replaced by Antigravity
    CLI on June 18th, 2026. To learn more, see our blog post.
                    Gemini CLI      
  Home  
  Plans  
   Extensions    
  Browse extensions  
  About Extensions     
  Docs  
  Reference  
  Resources  
  Changelog              Search  CtrlK       Cancel                 Feedback  GitHub  GitHub       Select theme   DarkLightAuto                      
  Home   
  Plans   
    Extensions       
  Browse extensions   
  About Extensions       
    Docs       
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure           
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference       
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall       
    Changelog       
  Release notes   
  Stable release   
  Preview release                
    Get started       
  Overview   
  Quickstart   
  Installation   
  Authentication   
  CLI cheatsheet   
  Gemini 3 on Gemini CLI       
    Use Gemini CLI       
  File management   
  Get started with Agent Skills   
  Manage context and memory   
  Execute shell commands   
  Manage sessions and history   
  Plan tasks with todos   
  Use Plan Mode with model steering 🔬  
  Web search and fetch   
  Set up an MCP server   
  Automate tasks       
    Features       
    Extensions       
  Overview   
  User guide: Install and manage   
  Developer guide: Build extensions   
  Developer guide: Best practices   
  Developer guide: Releasing   
  Developer guide: Reference       
    Agent Skills       
  Overview   
  Get started with Agent Skills   
  Creating Agent Skills   
  Using Agent Skills   
  Developer guide: Best practices       
  Auto Memory 🔬  
  Checkpointing   
  Headless mode   
  Git worktrees 🔬  
    Hooks       
  Overview   
  Reference       
    IDE integration       
  Overview   
  Developer guide: ACP mode       
    MCP servers       
  Overview   
  Resource tools       
  Model routing   
  Model selection   
  Model steering 🔬  
  Notifications 🔬  
  Plan mode   
  Subagents   
  Remote subagents   
  Rewind   
  Sandboxing   
  Settings   
  Telemetry   
  Token caching       
    Configuration       
  Custom commands   
  Enterprise configuration   
  Ignore files (.geminiignore)   
  Model configuration   
  Project context (GEMINI.md)   
  Settings   
  System prompt override   
  Themes   
  Trusted folders       
    Development       
  Behavioral evaluations   
  Contribution guide   
  Integration testing   
  Issue and PR automation   
  Local development   
  NPM package structure                 GitHub     Select theme   DarkLightAuto                
    Reference       
  Command reference   
  Configuration reference   
  Keyboard shortcuts   
  Memory import processor   
  Policy engine   
  Tools reference                 GitHub     Select theme   DarkLightAuto                
    Resources       
  FAQ   
  Quota and pricing   
  Terms and privacy   
  Troubleshooting   
  Uninstall                 GitHub     Select theme   DarkLightAuto                
    Releases       
  Release notes   
  Stable release   
  Preview release                 GitHub     Select theme   DarkLightAuto                         On this page 
  Introduction   
  What are subagents?   
  How to use subagents   
  Automatic delegation   
  Forcing a subagent (@ syntax)      
  Built-in subagents   
  Codebase Investigator   
  CLI Help Agent   
  Generalist Agent   
  Browser Agent      
  Creating custom subagents   
  Agent definition files   
  File format   
  Configuration schema   
  Tool wildcards   
  Isolation and recursion protection      
  Subagent tool isolation   
  Configuring isolated tools and servers   
  Subagent-specific policies      
  Managing subagents   
  Interactive management (/agents)   
  Persistent configuration (settings.json)   
  Optimizing your subagent      
  Remote subagents (Agent2Agent)   
  Extension subagents   
  Disabling subagents     
On this page 
  Introduction   
  What are subagents?   
  How to use subagents   
  Automatic delegation   
  Forcing a subagent (@ syntax)      
  Built-in subagents   
  Codebase Investigator   
  CLI Help Agent   
  Generalist Agent   
  Browser Agent      
  Creating custom subagents   
  Agent definition files   
  File format   
  Configuration schema   
  Tool wildcards   
  Isolation and recursion protection      
  Subagent tool isolation   
  Configuring isolated tools and servers   
  Subagent-specific policies      
  Managing subagents   
  Interactive management (/agents)   
  Persistent configuration (settings.json)   
  Optimizing your subagent      
  Remote subagents (Agent2Agent)   
  Extension subagents   
  Disabling subagents                 
 Subagents     Copy as Markdown Copied!             
Subagents are specialized agents that operate within your main Gemini CLI
session. They are designed to handle specific, complex tasks—like deep codebase
analysis, documentation lookup, or domain-specific reasoning—without cluttering
the main agent’s context or toolset.

What are subagents?Section titled “What are subagents?”

Subagents are “specialists” that the main Gemini agent can hire for a specific
job.

Focused context: Each subagent has its own system prompt and persona.

Specialized tools: Subagents can have a restricted or specialized set of
tools.

Independent context window: Interactions with a subagent happen in a
separate context loop, which saves tokens in your main conversation history.

Subagents are exposed to the main agent as a tool of the same name. When the
main agent calls the tool, it delegates the task to the subagent. Once the
subagent completes its task, it reports back to the main agent with its
findings.

How to use subagentsSection titled “How to use subagents”

You can use subagents through automatic delegation or by explicitly forcing them
in your prompt.

Automatic delegationSection titled “Automatic delegation”

Gemini CLI’s main agent is instructed to use specialized subagents when a task
matches their expertise. For example, if you ask “How does the auth system
work?”, the main agent may decide to call the codebase_investigator subagent
to perform the research.

Forcing a subagent (@ syntax)Section titled “Forcing a subagent (@ syntax)”

You can explicitly direct a task to a specific subagent by using the @ symbol
followed by the subagent’s name at the beginning of your prompt. This is useful
when you want to bypass the main agent’s decision-making and go straight to a
specialist.

Example:
Terminal window
@codebase_investigator Map out the relationship between the AgentRegistry and the LocalAgentExecutor.

When you use the @ syntax, the CLI injects a system note that nudges the
primary model to use that specific subagent tool immediately.

Built-in subagentsSection titled “Built-in subagents”

Gemini CLI comes with the following built-in subagents:

Codebase InvestigatorSection titled “Codebase Investigator”

Name: codebase_investigator

Purpose: Analyze the codebase, reverse engineer, and understand complex
dependencies.

When to use: “How does the authentication system work?”, “Map out the
dependencies of the AgentRegistry class.”

Configuration: Enabled by default. You can override its settings in
settings.json under agents.overrides. Example (forcing a specific model
and increasing turns):

{  "agents": {    "overrides": {      "codebase_investigator": {        "modelConfig": { "model": "gemini-3-flash-preview" },        "runConfig": { "maxTurns": 50 }      }    }  }}

CLI Help AgentSection titled “CLI Help Agent”

Name: cli_help

Purpose: Get expert knowledge about Gemini CLI itself, its commands,
configuration, and documentation.

When to use: “How do I configure a proxy?”, “What does the /rewind
command do?”

Configuration: Enabled by default.

Generalist AgentSection titled “Generalist Agent”

Name: generalist

Purpose: A general, all-purpose subagent that uses the inherited tool
access and configurations from the main agent. Useful for executing broad,
resource-heavy subtasks in an isolated conversation, optimizing your main
agent’s context by returning only the final result of that given task.

When to use: Use this agent when a task requires many steps, handles large
volumes of information, or requires the same full capabilities as the main
agent. It is ideal for:

Multi-file modifications: Applying refactors or fixing errors across
several files at once.

High-volume execution: Running commands or tests that produce extensive
terminal output.

Action-oriented research: Investigations where the agent needs to both
search code and run commands or make edits to find a solution. By delegating
these tasks, you prevent your main conversation from becoming cluttered or
slow. You can invoke it explicitly using @generalist.

Configuration: Enabled by default.

Browser AgentSection titled “Browser Agent”

Name: browser_agent

Purpose: Automate web browser tasks — navigating websites, filling forms,
clicking buttons, and extracting information from web pages — using the
accessibility tree.

When to use: “Go to example.com and fill out the contact form,” “Extract
the pricing table from this page,” “Click the login button and enter my
credentials.”

PrerequisitesSection titled “Prerequisites”

The browser agent requires:

Chrome version 144 or later (any recent stable release works).

The underlying
chrome-devtools-mcp
server is bundled with Gemini CLI and launched automatically — no separate
installation is needed.

Enabling the browser agentSection titled “Enabling the browser agent”

The browser agent is disabled by default. Enable it in your settings.json:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    }  }}

Session modesSection titled “Session modes”

The sessionMode setting controls how Chrome is launched and managed. Set it
under agents.browser:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "sessionMode": "persistent"    }  }}

The available modes are:

ModeDescriptionpersistent(Default) Launches Chrome with a persistent profile stored at ~/.gemini/cli-browser-profile/. Cookies, history, and settings are preserved between sessions.isolatedLaunches Chrome with a temporary profile that is deleted after each session. Use this for clean-state automation.existingAttaches to an already-running Chrome instance. You must enable remote debugging first by navigating to chrome://inspect/#remote-debugging in Chrome. No new browser process is launched.

First-run consentSection titled “First-run consent”

The first time the browser agent is invoked, Gemini CLI displays a consent
dialog. You must accept before the browser session starts. This dialog only
appears once.

Configuration referenceSection titled “Configuration reference”

All browser-specific settings go under agents.browser in your settings.json.
For full details, see the
agents.browser configuration reference.

SettingTypeDefaultDescriptionsessionModestring"persistent"How Chrome is managed: "persistent", "isolated", or "existing".headlessbooleanfalseRun Chrome in headless mode (no visible window).profilePathstring—Custom path to a browser profile directory.visualModelstring—Model override for the visual agent.allowedDomainsstring[]—Restrict navigation to specific domains (for example, ["github.com"]).disableUserInputbooleantrueDisable user input on the browser window during automation (non-headless only).maxActionsPerTasknumber100Maximum tool calls per task. The agent is terminated when the limit is reached.confirmSensitiveActionsbooleanfalseRequire manual confirmation for upload_file and evaluate_script.blockFileUploadsbooleanfalseHard-block all file upload requests from the agent.

Automation overlay and input blockingSection titled “Automation overlay and input blocking”

In non-headless mode, the browser agent injects a visual overlay into the
browser window to indicate that automation is in progress. By default, user
input (keyboard and mouse) is also blocked to prevent accidental interference.
You can disable this by setting disableUserInput to false.

SecuritySection titled “Security”

The browser agent enforces several layers of security:

Domain restrictions: When allowedDomains is set, the agent can only
navigate to the listed domains (and their subdomains when using *. prefix).
Attempting to visit a disallowed domain throws a fatal error that immediately
terminates the agent. The agent also attempts to detect and block the use of
allowed domains as proxies (e.g., via query parameters or fragments) to access
restricted content.

Blocked URL patterns: The underlying MCP server blocks dangerous URL
schemes including file://, javascript:, data:text/html,
chrome://extensions, and chrome://settings/passwords.

Sensitive action confirmation: Form filling (fill, fill_form) always
requires user confirmation through the policy engine, regardless of approval
mode. When confirmSensitiveActions is true, upload_file and
evaluate_script also require confirmation.

File upload blocking: Set blockFileUploads to true to hard-block all
file upload requests, preventing the agent from uploading any files.

Action rate limiting: The maxActionsPerTask setting (default: 100)
limits the total number of tool calls per task to prevent runaway execution.

Visual agentSection titled “Visual agent”

By default, the browser agent interacts with pages through the accessibility
tree using element uid values. For tasks that require visual identification
(for example, “click the yellow button” or “find the red error message”), you
can enable the visual agent by setting a visualModel:

{  "agents": {    "overrides": {      "browser_agent": {        "enabled": true      }    },    "browser": {      "visualModel": "gemini-2.5-computer-use-preview-10-2025"    }  }}

When enabled, the agent gains access to the analyze_screenshot tool, which
captures a screenshot and sends it to the vision model for analysis. The model
returns coordinates and element descriptions that the browser agent uses with
the click_at tool for precise, coordinate-based interactions.

Note
The visual agent requires API key or Vertex AI authentication. It is
not available when using “Sign in with Google”.

Sandbox supportSection titled “Sandbox support”

The browser agent adjusts its behavior automatically when running inside a
sandbox.
macOS seatbelt (sandbox-exec)Section titled “macOS seatbelt (sandbox-exec)”

When the CLI runs under the macOS seatbelt sandbox, persistent and isolated
session modes are forced to isolated with headless enabled. This avoids
permission errors caused by seatbelt file-system restrictions on persistent
browser profiles. If sessionMode is set to existing, no override is applied.
Container sandboxes (Docker / Podman)Section titled “Container sandboxes (Docker / Podman)”

Chrome is not available inside the container, so the browser agent is
disabled unless sessionMode is set to "existing". When enabled with
existing mode, the agent automatically connects to Chrome on the host via the
resolved IP of host.docker.internal:9222 instead of using local pipe
discovery. Port 9222 is currently hardcoded and cannot be customized.

To use the browser agent in a Docker sandbox:

Start Chrome on the host with remote debugging enabled:
Terminal window
# Option A: Launch Chrome from the command linegoogle-chrome --remote-debugging-port=9222
# Option B: Enable in Chrome settings# Navigate to chrome://inspect/#remote-debugging and enable

Configure sessionMode and allowed domains in your project’s
.gemini/settings.json:

{  "agents": {    "overrides": {      "browser_agent": { "enabled": true }    },    "browser": {      "sessionMode": "existing",      "allowedDomains": ["example.com"]    }  }}

Launch the CLI with port forwarding:
Terminal window
GEMINI_SANDBOX=docker SANDBOX_PORTS=9222 gemini

Creating custom subagentsSection titled “Creating custom subagents”

You can create your own subagents to automate specific workflows or enforce
specific personas.

Agent definition filesSection titled “Agent definition files”

Custom agents are defined as Markdown files (.md) with YAML frontmatter. You
can place them in:

Project-level: .gemini/agents/*.md (Shared with your team)

User-level: ~/.gemini/agents/*.md (Personal agents)

File formatSection titled “File format”

The file MUST start with YAML frontmatter enclosed in triple-dashes ---.
The body of the markdown file becomes the agent’s System Prompt.

Example: .gemini/agents/security-auditor.md

---name: security-auditordescription: Specialized in finding security vulnerabilities in code.kind: localtools:  - read_file  - grep_searchmodel: gemini-3-flash-previewtemperature: 0.2max_turns: 10---
You are a ruthless Security Auditor. Your job is to analyze code for potentialvulnerabilities.
Focus on:
1.  SQL Injection2.  XSS (Cross-Site Scripting)3.  Hardcoded credentials4.  Unsafe file operations
When you find a vulnerability, explain it clearly and suggest a fix. Do not fixit yourself; just report it.

Configuration schemaSection titled “Configuration schema”

FieldTypeRequiredDescriptionnamestringYesUnique identifier (slug) used as the tool name for the agent. Only lowercase letters, numbers, hyphens, and underscores.descriptionstringYesShort description of what the agent does. This is visible to the main agent to help it decide when to call this subagent.kindstringNolocal (default) or remote.toolsarrayNoList of tool names this agent can use. Supports wildcards: * (all tools), mcp_* (all MCP tools), mcp_server_* (all tools from a server). If omitted, it inherits all tools from the parent session.mcpServersobjectNoConfiguration for inline Model Context Protocol (MCP) servers isolated to this specific agent.modelstringNoSpecific model to use (for example, gemini-3-preview). Defaults to inherit (uses the main session model).temperaturenumberNoModel temperature (0.0 - 2.0). Defaults to 1.max_turnsnumberNoMaximum number of conversation turns allowed for this agent before it must return. Defaults to 30.timeout_minsnumberNoMaximum execution time in minutes. Defaults to 10.

Tool wildcardsSection titled “Tool wildcards”

When defining tools for a subagent, you can use wildcards to quickly grant
access to groups of tools:

*: Grant access to all available built-in and discovered tools.

mcp_*: Grant access to all tools from all connected MCP servers.

mcp_my-server_*: Grant access to all tools from a specific MCP server named
my-server.

Isolation and recursion protectionSection titled “Isolation and recursion protection”

Each subagent runs in its own isolated context loop. This means:

Independent history: The subagent’s conversation history does not bloat
the main agent’s context.

Isolated tools: The subagent only has access to the tools you explicitly
grant it.

Recursion protection: To prevent infinite loops and excessive token usage,
subagents cannot call other subagents. If a subagent is granted the *
tool wildcard, it will still be unable to see or invoke other agents.

Subagent tool isolationSection titled “Subagent tool isolation”

Subagent tool isolation moves Gemini CLI away from a single global tool
registry. By providing isolated execution environments, you can ensure that
subagents only interact with the parts of the system they are designed for. This
prevents unintended side effects, improves reliability by avoiding state
contamination, and enables fine-grained permission control.

With this feature, you can:

Specify tool access: Define exactly which tools an agent can access using
a tools list in the agent definition.

Define inline MCP servers: Configure Model Context Protocol (MCP) servers
(which provide a standardized way to connect AI models to external tools and
data sources) directly in the subagent’s markdown frontmatter, isolating them
to that specific agent.

Maintain state isolation: Ensure that subagents only interact with their
own set of tools and servers, preventing side effects and state contamination.

Apply subagent-specific policies: Enforce granular rules in your
Policy Engine TOML configuration based on the
executing subagent’s name.

Configuring isolated tools and serversSection titled “Configuring isolated tools and servers”

You can configure tool isolation for a subagent by updating its markdown
frontmatter. This lets you explicitly state which tools the subagent can use,
rather than relying on the global registry.

Add an mcpServers object to define inline MCP servers that are unique to the
agent.

Example:

---name: my-isolated-agenttools:  - grep_search  - read_filemcpServers:  my-custom-server:    command: 'node'    args: ['path/to/server.js']---

Subagent-specific policiesSection titled “Subagent-specific policies”

You can enforce fine-grained control over subagents using the
Policy Engine’s TOML configuration. This allows
you to grant or restrict permissions specifically for an agent, without
affecting the rest of your CLI session.

To restrict a policy rule to a specific subagent, add the subagent property to
the [[rules]] block in your policy.toml file.

Example:

[[rules]]name = "Allow pr-creator to push code"subagent = "pr-creator"description = "Permit pr-creator to push branches automatically."action = "allow"toolName = "run_shell_command"commandPrefix = "git push"

In this configuration, the policy rule only triggers if the executing subagent’s
name matches pr-creator. Rules without the subagent property apply
universally to all agents.

Managing subagentsSection titled “Managing subagents”

You can manage subagents interactively using the /agents command or
persistently via settings.json.

Interactive management (/agents)Section titled “Interactive management (/agents)”

If you are in an interactive CLI session, you can use the /agents command to
manage subagents without editing configuration files manually. This is the
recommended way to quickly enable, disable, or re-configure agents on the fly.

For a full list of sub-commands and usage, see the
/agents command reference.

Persistent configuration (settings.json)Section titled “Persistent configuration (settings.json)”

While the /agents command and agent definition files provide a starting point,
you can use settings.json for global, persistent overrides. This is useful for
enforcing specific models or execution limits across all sessions.

agents.overridesSection titled “agents.overrides”

Use this to enable or disable specific agents or override their run
configurations.

{  "agents": {    "overrides": {      "security-auditor": {        "enabled": false,        "runConfig": {          "maxTurns": 20,          "maxTimeMinutes": 10        }      }    }  }}

modelConfigs.overridesSection titled “modelConfigs.overrides”

You can target specific subagents with custom model settings (like system
instruction prefixes or specific safety settings) using the overrideScope
field.

{  "modelConfigs": {    "overrides": [      {        "match": { "overrideScope": "security-auditor" },        "modelConfig": {          "generateContentConfig": {            "temperature": 0.1          }        }      }    ]  }}

Safety policies (TOML)Section titled “Safety policies (TOML)”

You can restrict access to specific subagents using the CLI’s Policy Engine.
Subagents are treated as virtual tool names for policy matching purposes.

To govern access to a subagent, create a .toml file in your policy directory
(e.g., ~/.gemini/policies/):

[[rule]]toolName = "codebase_investigator"decision = "deny"deny_message = "Deep codebase analysis is restricted for this session."

For more information on setting up fine-grained safety guardrails, see the
Policy Engine reference.

Optimizing your subagentSection titled “Optimizing your subagent”

The main agent’s system prompt encourages it to use an expert subagent when one
is available. It decides whether an agent is a relevant expert based on the
agent’s description. You can improve the reliability with which an agent is used
by updating the description to more clearly indicate:

Its area of expertise.

When it should be used.

Some example scenarios.

For example, the following subagent description should be called fairly
consistently for Git operations.

Git expert agent which should be used for all local and remote git operations.
For example:

Making commits

Searching for regressions with bisect

Interacting with source control and issues providers such as GitHub.

If you need to further tune your subagent, you can do so by selecting the model
to optimize for with /model and then asking the model why it does not think
that your subagent was called with a specific prompt and the given description.

Remote subagents (Agent2Agent)Section titled “Remote subagents (Agent2Agent)”

Gemini CLI can also delegate tasks to remote subagents using the Agent-to-Agent
(A2A) protocol.

See the Remote Subagents documentation for detailed
configuration, authentication, and usage instructions.

Extension subagentsSection titled “Extension subagents”

Extensions can bundle and distribute subagents. See the
Extensions documentation for details on how
to package agents within an extension.

Disabling subagentsSection titled “Disabling subagents”

Subagents are enabled by default. To disable them, set enableAgents to false
in your settings.json:

{  "experimental": { "enableAgents": false }}   Last updated: Jun 8, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy