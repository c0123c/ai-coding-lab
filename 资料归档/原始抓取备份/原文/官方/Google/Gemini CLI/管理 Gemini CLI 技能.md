Managing Agent Skills | Gemini CLI
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
  Discovery tiers   
  In-session management   
  Terminal utilities   
  Install a skill   
  Link for development   
  Uninstall a skill      
  Security and consent   
  Next steps     
On this page 
  Introduction   
  Discovery tiers   
  In-session management   
  Terminal utilities   
  Install a skill   
  Link for development   
  Uninstall a skill      
  Security and consent   
  Next steps                 
 Managing Agent Skills     Copy as Markdown Copied!             
Agent Skills provide Gemini CLI with specialized expertise on demand. This guide
covers advanced management techniques, including using slash commands, terminal
utilities, and understanding discovery tiers.

Discovery tiersSection titled “Discovery tiers”

Gemini CLI discovers skills from several locations, following a specific order
of precedence (lowest to highest):

Built-in Skills: Included with Gemini CLI and always available.

Extension Skills: Bundled within extensions.

User Skills: Located in ~/.gemini/skills/ or the ~/.agents/skills/
alias. These are available across all your projects.

Workspace Skills: Located in .gemini/skills/ or the .agents/skills/
alias within your current directory. These are project-specific.

Tip: If multiple skills share the same name, the version from the
higher-precedence location is used.

In-session managementSection titled “In-session management”

Use the /skills slash command during an interactive session to manage your
available expertise.

List skills: /skills list (shows discovered skills).

Use /skills list all to include internal built-in skills.

Use /skills list nodesc to hide descriptions.

Reload skills: /skills reload (or /skills refresh) to scan for new or
modified skills without restarting the CLI.

Toggle status:

/skills disable <name>: Prevents a skill from being triggered.

/skills enable <name>: Re-enables a disabled skill.

Link local skills: /skills link <path> [--scope user|workspace] to
immediately use a skill you are developing.

Terminal utilitiesSection titled “Terminal utilities”

The gemini skills command provides management utilities directly from your
system shell.

Install a skillSection titled “Install a skill”

To install a skill from a remote repository or a local .skill package:
Terminal window
gemini skills install https://github.com/user/my-awesome-skill

By default, this installs to your user profile. Use --scope workspace to
install it only for the current project.

Link for developmentSection titled “Link for development”

If you are developing a skill, use the link command to create a reference to
your local directory:
Terminal window
gemini skills link ./path/to/my-skill

Uninstall a skillSection titled “Uninstall a skill”

To completely remove an installed or linked skill:
Terminal window
gemini skills uninstall <name>

Security and consentSection titled “Security and consent”

Agent Skills can execute scripts and access your files. To protect your
environment:

Installation consent: When installing from a remote URL, you will be
asked to confirm the source.

Activation consent: Every time a skill is triggered during a session,
the agent must ask for permission to activate it and gain access to its
resources.

Next stepsSection titled “Next steps”

Get started with Agent Skills: A
walkthrough for creating your first skill.

Creating Agent Skills: Detailed guide on bundling
scripts and assets.

Skill best practices: Strategies for building
reliable expertise.
   Last updated: Apr 30, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy