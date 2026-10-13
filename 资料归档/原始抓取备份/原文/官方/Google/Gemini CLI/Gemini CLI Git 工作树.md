Git Worktrees (experimental) | Gemini CLI
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
  How to enable Git worktrees   
  How to use Git worktrees   
  How to exit a Git worktree session   
  Resuming work in a Git worktree   
  Managing Git worktrees manually     
On this page 
  Introduction   
  How to enable Git worktrees   
  How to use Git worktrees   
  How to exit a Git worktree session   
  Resuming work in a Git worktree   
  Managing Git worktrees manually                 
 Git Worktrees (experimental)     Copy as Markdown Copied!             
When working on multiple tasks at once, you can use Git worktrees to give each
Gemini session its own copy of the codebase. Git worktrees create separate
working directories that each have their own files and branch while sharing the
same repository history. This prevents changes in one session from colliding
with another.

Learn more about session management.

Note
This is an experimental feature currently under active development. Your
feedback is invaluable as we refine this feature. If you have ideas,
suggestions, or encounter issues:

Open an issue on GitHub.

Use the /bug command within Gemini CLI to file an issue.

Learn more in the official Git worktree
documentation.

How to enable Git worktreesSection titled “How to enable Git worktrees”

Git worktrees are an experimental feature. You must enable them in your settings
using the /settings command or by manually editing your settings.json file.

Use the /settings command.

Search for and set Enable Git Worktrees to true.

Alternatively, add the following to your settings.json:

{  "experimental": {    "worktrees": true  }}

How to use Git worktreesSection titled “How to use Git worktrees”

Use the --worktree (-w) flag to create an isolated worktree and start Gemini
CLI in it.

Start with a specific name: The value you pass becomes both the directory
name (within .gemini/worktrees/) and the branch name.
Terminal window
gemini --worktree feature-search

Start with a random name: If you omit the name, Gemini generates a random
one automatically (for example, worktree-a1b2c3d4).
Terminal window
gemini --worktree

Note
Remember to initialize your development environment in each new
worktree according to your project’s setup. Depending on your stack, this
might include running dependency installation (npm install, yarn), setting
up virtual environments, or following your project’s standard build process.

How to exit a Git worktree sessionSection titled “How to exit a Git worktree session”

When you exit a worktree session (using /quit or Ctrl+C), Gemini leaves the
worktree intact so your work is not lost. This includes your uncommitted changes
(modified files, staged changes, or untracked files) and any new commits you
have made.

Gemini prioritizes a fast and safe exit: it does not automatically delete
your worktree or branch. You are responsible for cleaning up your worktrees
manually once you are finished with them.

When you exit, Gemini displays instructions on how to resume your work or how to
manually remove the worktree if you no longer need it.

Resuming work in a Git worktreeSection titled “Resuming work in a Git worktree”

To resume a session in a worktree, navigate to the worktree directory and start
Gemini CLI with the --resume flag and the session ID:
Terminal window
cd .gemini/worktrees/feature-searchgemini --resume <session_id>

Managing Git worktrees manuallySection titled “Managing Git worktrees manually”

For more control over worktree location and branch configuration, or to clean up
a preserved worktree, you can use Git directly:

Clean up a preserved Git worktree:
Terminal window
git worktree remove .gemini/worktrees/feature-search --forcegit branch -D worktree-feature-search

Create a Git worktree manually:
Terminal window
git worktree add ../project-feature-search -b feature-searchcd ../project-feature-search && gemini

   Last updated: Mar 20, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy