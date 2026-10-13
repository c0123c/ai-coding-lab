Provide context with GEMINI.md files | Gemini CLI
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
  Understand the context hierarchy   
  Example GEMINI.md file      
  Manage context with the /memory command   
  Modularize context with imports   
  Customize the context file name   
  Next steps     
On this page 
  Introduction   
  Understand the context hierarchy   
  Example GEMINI.md file      
  Manage context with the /memory command   
  Modularize context with imports   
  Customize the context file name   
  Next steps                 
 Provide context with GEMINI.md files     Copy as Markdown Copied!             
Context files, which use the default name GEMINI.md, are a powerful feature
for providing instructional context to the Gemini model. You can use these files
to give project-specific instructions, define a persona, or provide coding style
guides to make the AI’s responses more accurate and tailored to your needs.

Instead of repeating instructions in every prompt, you can define them once in a
context file.

Understand the context hierarchySection titled “Understand the context hierarchy”

The CLI uses a hierarchical system to source context. It loads various context
files from several locations, concatenates the contents of all found files, and
sends them to the model with every prompt. The CLI loads files in the following
order:

Global context file:

Location: ~/.gemini/GEMINI.md (in your user home directory).

Scope: Provides default instructions for all your projects.

Environment and workspace context files:

Location: The CLI searches for GEMINI.md files in your configured
workspace directories and their parent directories.

Scope: Provides context relevant to the projects you are currently
working on.

Just-in-time (JIT) context files:

Location: When a tool accesses a file or directory, the CLI
automatically scans for GEMINI.md files in that directory and its
ancestors up to a trusted root.

Scope: Lets the model discover highly specific instructions for
particular components only when they are needed.

The CLI footer displays the number of loaded context files, which gives you a
quick visual cue of the active instructional context.

Example GEMINI.md fileSection titled “Example GEMINI.md file”

Here is an example of what you can include in a GEMINI.md file at the root of
a TypeScript project:

# Project: My TypeScript Library
## General Instructions
- When you generate new TypeScript code, follow the existing coding style.- Ensure all new functions and classes have JSDoc comments.- Prefer functional programming paradigms where appropriate.
## Coding Style
- Use 2 spaces for indentation.- Prefix interface names with `I` (for example, `IUserService`).- Always use strict equality (`===` and `!==`).

Manage context with the /memory commandSection titled “Manage context with the /memory command”

You can interact with the loaded context files by using the /memory command.

/memory show: Displays the full, concatenated content of the current
hierarchical memory. This lets you inspect the exact instructional context
being provided to the model.

/memory reload: Forces a re-scan and reload of all GEMINI.md files
from all configured locations.

Modularize context with importsSection titled “Modularize context with imports”

You can break down large GEMINI.md files into smaller, more manageable
components by importing content from other files using the @file.md syntax.
This feature supports both relative and absolute paths.

Example GEMINI.md with imports:

# Main GEMINI.md file
This is the main content.
@./components/instructions.md
More content here.
@../shared/style-guide.md

For more details, see the Memory Import Processor
documentation.

Customize the context file nameSection titled “Customize the context file name”

While GEMINI.md is the default filename, you can configure this in your
settings.json file. To specify a different name or a list of names, use the
context.fileName property.

Example settings.json:

{  "context": {    "fileName": ["AGENTS.md", "CONTEXT.md", "GEMINI.md"]  }}

Next stepsSection titled “Next steps”

Learn about Ignoring files to exclude content from the
context system.

Explore the Memory tool to save persistent memories.

See how to use Custom commands to automate common
prompts.
   Last updated: Jun 18, 2026                
This website uses cookies from Google to deliver and enhance the quality of its services and to analyze
        traffic.
 
I understand.
                Terms | Privacy