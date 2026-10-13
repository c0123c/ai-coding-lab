# Agent settings

## macOS and Linux

### Permission settings

This setting controls how agent actions—terminal commands, file access, MCP tools, and web page reads—are approved using a permission preset :

- Default : Commands run without prompting inside the Terminal sandbox ; running outside the sandbox requires approval. The agent can read and write the workspace and temp directories, and needs approval for anything else.

- Request Review : The sandbox is off and every terminal command requires approval. The agent can read and write the workspace, and needs approval for anything else.

- Turbo : All commands run without prompting with no isolation or restrictions, and the agent has full read and write access to your filesystem.

You can configure the preset under Settings > General > Permission Settings , and override it per project under Settings > Projects . Projects default to Inherit General , which follows your global preset.

Your configured allow, deny, and ask permission rules are layered on top of the preset and always take precedence. To learn more, refer to Agent permissions .

## Windows

### Terminal command auto execution

This setting controls how the agent executes generated shell commands:

- Request Review : The agent never executes terminal commands without prompting (except those explicitly added to your configurable Allow list).

- Proceed in Sandbox : Commands run without prompting inside the Terminal sandbox ; commands that need to run outside it still require review.

- Always Proceed : The agent executes commands without prompting (except those explicitly added to your configurable Deny list).

### Agent non-workspace file access

This setting allows the agent to view and edit files outside of the active project folders:

- By default, the agent only has access to the folders inside your project and the application’s local app data directory ~/.gemini/antigravity/ (which contains artifacts, knowledge items, and more).

- Enforcing this boundary protects your local sensitive data. Enable non-workspace access with caution.

### Terminal sandbox mode

This setting restricts agent terminal commands to an isolated sandbox:

- When enabled, shell commands execute inside the sandbox without access to sensitive system paths or unauthorized networks.

- You can configure this setting globally in application preferences or override it per project.

- To learn more, refer to the Terminal sandbox guide.
