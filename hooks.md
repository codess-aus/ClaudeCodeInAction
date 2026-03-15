Hooks allow you to run commands before or after Claude attempts to run a tool. They're incredibly useful for implementing automated workflows like running code formatters after file edits, executing tests when files change, or blocking access to specific files.

How Hooks Work
To understand hooks, let's first review the normal flow when you interact with Claude Code. When you ask Claude something, your query gets sent to the Claude model along with tool definitions. Claude might decide to use a tool by providing a formatted response, and then Claude Code executes that tool and returns the result.

Hooks insert themselves into this process, allowing you to execute code just before or just after the tool execution happens.

![hooks](https://github.com/codess-aus/ClaudeCodeInAction/blob/859853686625f9de9b0f7bf561a5ff1d70fe4de0/assets/cc%20pretool.png)

There are two types of hooks:

PreToolUse hooks - Run before a tool is called
PostToolUse hooks - Run after a tool is called
Hook Configuration
Hooks are defined in Claude settings files. You can add them to:

Global - ~/.claude/settings.json (affects all projects)
Project - .claude/settings.json (shared with team)
Project (not committed) - .claude/settings.local.json (personal settings)
You can write hooks by hand in these files or use the /hooks command inside Claude Code.



The configuration structure includes two main sections:

![config](https://github.com/codess-aus/ClaudeCodeInAction/blob/859853686625f9de9b0f7bf561a5ff1d70fe4de0/assets/cc%20hook%20config.png)

PreToolUse Hooks
PreToolUse hooks run before a tool is executed. They include a matcher that specifies which tool types to target:

![hookdef](https://github.com/codess-aus/ClaudeCodeInAction/blob/859853686625f9de9b0f7bf561a5ff1d70fe4de0/assets/cchookdef.png)

"PreToolUse": [
  {
    "matcher": "Read",
    "hooks": [
      {
        "type": "command",
        "command": "node /home/hooks/read_hook.ts"
      }
    ]
  }
]
Before the 'Read' tool is executed, this configuration runs the specified command. Your command receives details about the tool call Claude wants to make, and you can:

Allow the operation to proceed normally
Block the tool call and send an error message back to Claude
PostToolUse Hooks
PostToolUse hooks run after a tool has been executed. Here's an example that triggers after write, edit, or multi-edit operations:

"PostToolUse": [
  {
    "matcher": "Write|Edit|MultiEdit",
    "hooks": [
      {
        "type": "command", 
        "command": "node /home/hooks/edit_hook.ts"
      }
    ]
  }
]
Since the tool call has already occurred, PostToolUse hooks can't block the operation. However, they can:

Run follow-up operations (like formatting a file that was just edited)
Provide additional feedback to Claude about the tool use

![followup](https://github.com/codess-aus/ClaudeCodeInAction/blob/859853686625f9de9b0f7bf561a5ff1d70fe4de0/assets/ccfollowup.png)


Practical A
