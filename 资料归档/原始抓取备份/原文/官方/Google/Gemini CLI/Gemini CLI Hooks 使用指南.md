Gemini CLI hooks | Gemini CLI
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
  What are hooks?   
  Getting started      
  Core concepts   
  Hook events   
  Global mechanics      
  Configuration   
  Configuration schema   
  Environment variables      
  Security and risks   
  Managing hooks     
On this page 
  Introduction   
  What are hooks?   
  Getting started      
  Core concepts   
  Hook events   
  Global mechanics      
  Configuration   
  Configuration schema   
  Environment variables      
  Security and risks   
  Managing hooks                 
 Gemini CLI hooks     Copy as Markdown Copied!             
Hooks are scripts or programs that Gemini CLI executes at specific points in the
agentic loop, allowing you to intercept and customize behavior without modifying
the CLI’s source code.

What are hooks?Section titled “What are hooks?”

Hooks run synchronously as part of the agent loop—when a hook event fires,
Gemini CLI waits for all matching hooks to complete before continuing.

With hooks, you can:

Add context: Inject relevant information (like git history) before the
model processes a request.

Validate actions: Review tool arguments and block potentially dangerous
operations.

Enforce policies: Implement security scanners and compliance checks.

Log interactions: Track tool usage and model responses for auditing.

Optimize behavior: Dynamically filter available tools or adjust model
parameters.

Getting startedSection titled “Getting started”

Writing hooks guide: A tutorial on creating
your first hook with comprehensive examples.

Best practices: Guidelines on security,
performance, and debugging.

Hooks reference: The definitive technical
specification of I/O schemas and exit codes.

Core conceptsSection titled “Core concepts”

Hook eventsSection titled “Hook events”

Hooks are triggered by specific events in Gemini CLI’s lifecycle.

EventWhen It FiresImpactCommon Use CasesSessionStartWhen a session begins (startup, resume, clear)Inject ContextInitialize resources, load contextSessionEndWhen a session ends (exit, clear)AdvisoryClean up, save stateBeforeAgentAfter user submits prompt, before planningBlock Turn / ContextAdd context, validate prompts, block turnsAfterAgentWhen agent loop endsRetry / HaltReview output, force retry or halt executionBeforeModelBefore sending request to LLMBlock Turn / MockModify prompts, swap models, mock responsesAfterModelAfter receiving LLM responseBlock Turn / RedactFilter/redact responses, log interactionsBeforeToolSelectionBefore LLM selects toolsFilter ToolsFilter available tools, optimize selectionBeforeToolBefore a tool executesBlock Tool / RewriteValidate arguments, block dangerous opsAfterToolAfter a tool executesBlock Result / ContextProcess results, run tests, hide resultsPreCompressBefore context compressionAdvisorySave state, notify userNotificationWhen a system notification occursAdvisoryForward to desktop alerts, logging

Global mechanicsSection titled “Global mechanics”

Understanding these core principles is essential for building robust hooks.

Strict JSON requirements (The “Golden Rule”)Section titled “Strict JSON requirements (The “Golden Rule”)”

Hooks communicate via stdin (Input) and stdout (Output).

Silence is Mandatory: Your script must not print any plain text to
stdout other than the final JSON object. Even a single echo or print
call before the JSON will break parsing.

Pollution = Failure: If stdout contains non-JSON text, parsing will
fail. The CLI will default to “Allow” and treat the entire output as a
systemMessage.

Debug via Stderr: Use stderr for all logging and debugging (for
example, echo "debug" >&2). Gemini CLI captures stderr but never attempts
to parse it as JSON.

Exit codesSection titled “Exit codes”

Gemini CLI uses exit codes to determine the high-level outcome of a hook
execution:

Exit CodeLabelBehavioral Impact0SuccessThe stdout is parsed as JSON. Preferred code for all logic, including intentional blocks (for example, {"decision": "deny"}).2System BlockCritical Block. The target action (tool, turn, or stop) is aborted. stderr is used as the rejection reason. High severity; used for security stops or script failures.OtherWarningNon-fatal failure. A warning is shown, but the interaction proceeds using original parameters.

MatchersSection titled “Matchers”

You can filter which specific tools or triggers fire your hook using the
matcher field.

Tool events (BeforeTool, AfterTool): Matchers are Regular
Expressions. (for example, "write_.*").

Lifecycle events: Matchers are Exact Strings. (for example,
"startup").

Wildcards: "*" or "" (empty string) matches all occurrences.

ConfigurationSection titled “Configuration”

Hooks are configured in settings.json. Gemini CLI merges configurations from
multiple layers in the following order of precedence (highest to lowest):

Project settings: .gemini/settings.json in the current directory.

User settings: ~/.gemini/settings.json.

System settings: /etc/gemini-cli/settings.json.

Extensions: Hooks defined by installed extensions.

Configuration schemaSection titled “Configuration schema”

{  "hooks": {    "BeforeTool": [      {        "matcher": "write_file|replace",        "hooks": [          {            "name": "security-check",            "type": "command",            "command": "$GEMINI_PROJECT_DIR/.gemini/hooks/security.sh",            "timeout": 5000          }        ]      }    ]  }}

Hook configuration fieldsSection titled “Hook configuration fields”

FieldTypeRequiredDescriptiontypestringYesThe execution engine. Currently only "command" is supported.commandstringYes*The shell command to execute. (Required when type is "command").namestringNoA friendly name for identifying the hook in logs and CLI commands.timeoutnumberNoExecution timeout in milliseconds (default: 60000).descriptionstringNoA brief explanation of the hook’s purpose.

Environment variablesSection titled “Environment variables”

Hooks are executed with a sanitized environment.

GEMINI_PROJECT_DIR: The absolute path to the project root.

GEMINI_PLANS_DIR: The absolute path to the plans directory.

GEMINI_SESSION_ID: The unique ID for the current session.

GEMINI_CWD: The current working directory.

CLAUDE_PROJECT_DIR: (Alias) Provided for compatibility.

Security and risksSection titled “Security and risks”

Caution
Hooks execute arbitrary code with your user privileges. By
configuring hooks, you are allowing scripts to run shell commands on your
machine.

Project-level hooks are particularly risky when opening untrusted projects.
Gemini CLI fingerprints project hooks. If a hook’s name or command changes
(for example, via git pull), it is treated as a new, untrusted hook and
you will be warned before it executes.

See Security Considerations
for a detailed threat model.

Managing hooksSection titled “Managing hooks”

Use the CLI commands to manage hooks without editing JSON manually:

View hooks: /hooks panel

Enable/Disable all: /hooks enable-all or /hooks disable-all

Toggle individual: /hooks enable <name> or /hooks disable <name>
   Last updated: Apr 13, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy