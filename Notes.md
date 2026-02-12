!!! LINK FOR A BETTER CHEATSHEET !!! ==================================== -> https://awesomeclaude.ai/code-cheatsheet

# What's a coding assistant?

## Typically models work the next way. 

You got a task ("Got an error", Cannot read property 'records' of undefined)-> It enters the block of the assistant (this time claude code)-> Inside we have a major block with two general blocks: 

The first one is the language model, having three major steps\
**Gather context:** Understanding the error, which part of the codebase is affected and what files are relevant\
**Formulate a plan:** Deciding how to solve the issue\
**Take a action:** Actually implementing the solution by updating files and running commands

The other block is another set of tools which may be helpul

## The tool Use Challenge
Commonly language models can't read files because they can only process text and return text, here it comes the importance of a clever system "tool use"

### How does it works? 
When you send a request to a coding assistant , it automatically adds instructions to the message that teach the language how to 
request actions. For example\
- If you wanto to read a file ("What code is written in the main.go file?")
- The model adds **tool instructions** to the request
- The model responds with "ReadFile: main.go"
- The model reads the file and sends its contents

The system allows language models to effectively "read files", "write code", "run commands",etc...

So... Typicaly the flow is [You->Language model]\
But with claude models is actually **[You->Coding Assistant->Language model]**

### Claude series models are particularly stron at understanding what tools do and using them to complete tasks 

## Benefits of strong tool use
- Tackles harer tasks
- ExtensiBLe platform
- Better security

> **Remember:** Coding assistants use language models to complete different tasks, language moldes need tools to handle most real-world programming tasks.


# Using Context mode

When we want to edit or add something into claude.md manually we use 

- Just use: # ...  

And it will give the option to update claude local memory (claude.md) and claude global memory (~/.claude/CLAUDE.md)

## Extract an specified file from project to use 

If you want to extract an specified file: 

**Mention with** @ + ~Path to file~

## Example of use case

> #The database is defined in the @prisma/schema.prisma file. Reference it anytime you need to understand the structure of data stored in the database

And it will update the claude.md file 

# Making Changes

## Using Screenshots for Precise Communication

- Use: Win + Shift + s for taking a screenshot ~on windows~

Then paste it into the terminal with **Ctrl + v**.

>If you are using WSL, take the screenshot, upload it to **https://imgbb.com/upload** then provide the generated link to TUI (Terminal User interface) AKA. claude code

And make your prompt!

## Planning mode 

If you want to do complex tasks get into planning mode **Shift + Tab**

## Thinking mode

Allowing claude to reason more challenging problems ("Think", "Think more", "Think a lot", "Think longer", "Ultrathink")

**Using one of the listed ones uses less or more tokens**

### When to use one or another?

**Planning** is best for:
- Tasks requiring broad understanding of your codebase
- Multi steps implementations
- Changes that affects multiple files or components

**Thinking mode** is best for: 
- Complex logic problems
- Debugging difficult issues
- Algorithmic challenges

> You can use BOTH!

# Controlling context

if u get a repetitive **ERROR** with paths or similar, you can use ESC to interrupt, then use "#" and then add something to resolve the error. 

## Compact

**/compact** summarizes the relevant information progressed in the chat

# Custom commands

It is used to exec repetitive commands that are used frecuently

## Creating a custom command

To create a custom command, you need to setup a specific folder structure in your project

Steps: 
- Find the **.claude** folder in the project directory
- Create a new directory called **commands** inside of it
- Create a new markdown file with your dised command name (eg. audit.md)

**Your filename becomes your command name -so audit.md creates the /audit command-**

>Practical example: 
This audit command does three things:
Runs npm audit to find vulnerable installed packages
Runs npm audit fix to apply updates
Runs tests to verify the updates didn't break any

## Using arguments into commands

Using the **$ARGUMENTS** placeholder makes these custom commands more reusable and flexible

>For example a write_tests.md command might contain: 
Write comprehensive tests for: $ARGUMENTS

Testing conventions:
* Use Vitests with React Testing Library
* Place test files in a __tests__ directory in the same folder as the source file
* Name test files as [filename].test.ts(x)
* Use @/ prefix for imports

Coverage:
* Test happy paths
* Test edge cases
* Test error states

> You can then run this command with a file path: 
> /write_test use-auth.ts file in the hooks directory

# MCP Servers

MCPs provide Claude Code new tools and abilities it wouldn't normally have

> For example playwright which lets claude control a web browser.

For adding a new MCP use: **claude mcp add ´name of mcp´**
> claude mcp add playwright npx @playwright/mcp@latest

## Managing Permissions

You might find tired of permissions prompts, so you can pre-approve the server by editing your settings the next way: 

Open the **.claude/settings.local.json** file in your project and add the server to allow the array: 

{
  "permissions": {
    "allow": ["mcp__playwright"],
    "deny": []
  }
}

*Note the double underscores in ´mcp__playwright´, this allows Claude to use the playwright tools without asking for permission everytime*

# Github Integration

## What it Does
Runs Claude inside GitHub Actions with two main features:
- `@claude` mentions in Issues/PRs (executes tasks and replies)
- Automatic Pull Request reviews (analyzes changes and posts reports)

## Setup
Run:
/install-github-app

This:
- Installs the GitHub App
- Adds your API key
- Creates a PR with workflow files in `.github/workflows/`

## Customization

### Add Setup Steps
Before Claude runs, you can add steps to prepare your environment:


- name: Project Setup
  run: |
    npm run setup
    npm run dev:daemon

### Custom Instructions

Provide project context (server status, DB access, tools, etc.).

### MCP Servers

Configure extra capabilities (e.g., Playwright).

### Tool Permissions

All tools must be explicitly listed:

- allowed_tools: "Bash(npm:*),mcp__playwright__browser_snapshot"

# Introducing Hooks

Hooks allow you to run commands before or after Claude attempts to run a tool

## Types of hooks
(*In a flow Pretool hook runs -> Claude Code Reads File -> PostTool hook runs*)
- PreToolUseHooks: Run before a tool is called
- PostToolUseHooks: Run after a tool is called

## Creating a hook
They are defined in Claude settings files.
You can add them to: 
- **Global**: ~/.claude/settings.json ( affects all projects )
- **Project**: .claude/settings.json (shared with team)
- **Project (not commimtted)**: .claude/settings.local.json (personal settings)

*You can write hooks by hand in these files or use the /hooks command inside Claude Code.*
**Structure:**
<!--
{
  "hooks": {
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
    ],
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
  }
}
-->

*PreToolUse Hooks* run before a tool is executed. They include a mather that specifies which tool types to target.
- So you can Allow the operation to proceed normally
- Block the tool call and send an error message back to Claude

*PostToolUse Hooks* run after a tool has been executed. 
- You can't block an operation at this point
- Run follow-up operations (like formatting a file that just edited )
- Provide additional feedback to Claude about the tool use

# Defining Hooks

## Steps
1. Decide on PreeToolUse or PostToolUse Hook
2. Determine which type of tool calls you want to watch for
3. Write a command that will receive the tool call
4. If needed, command should provide feedback to Claude


- 2. **Tool name -> Purpose**

Read -> Read a file
Edit, MultiEdit -> Edit an existing file
Write -> Create a file and write to it
Bash -> Execute a command
Glob -> Find files/folders based upon a pattern
Grep -> Search for content
Task -> Create a sub-agent to complete a particular task
WebFetch, WebSearch -> Search or fetch a particular page

## Exit Codes

- **Exit Code 0**: Everything is fine, allow the tool call to proceed
- **Exit Code 2**: Block the tool call (PreToolUSe hooks only)

> When you exit with code 2 in a PreToolUse hook, any error messages you write to standard error will be sent to Claude as feedback, explaining why the operation was blocked

# Claude Code Hooks Security & Configuration

## 1. Security Best Practices
To ensure secure hook execution, the following practices must be adhered to:
* **Validate and Sanitize Inputs**: Never trust input data blindly.
* **Quote Shell Variables**: Use `"$VAR"` instead of `$VAR` to prevent injection.
* **Block Path Traversal**: Explicitly check for `..` in file paths.
* **Use Absolute Paths**: Specify full paths for scripts to mitigate path interception.
* **Skip Sensitive Files**: Avoid including `.env`, `.git/`, or cryptographic keys.

## 2. Collaborative Workflow & Portability
Since absolute paths vary between machines, the project uses a placeholder system to share settings effectively:

### Initialization Process
1. **Reference File**: Use `settings.example.json` containing the `$PWD` placeholder.
2. **Setup Execution**: Run `npm run setup` to install dependencies and trigger the initialization script.
3. **Local Generation**: The `init-claude.js` script replaces `$PWD` with the machine's absolute path and renames the file to `settings.local.json`.

# Claude Code Hook Events

## Notification
Triggered when Claude Code sends a notification:
- When it needs permission to use a tool
- After 60 seconds of inactivity

## Stop
Runs when Claude Code finishes responding.

## SubagentStop
Runs when a subagent (shown as a "Task" in the UI) finishes execution.

## PreCompact
Runs before a compact operation (manual or automatic).

## UserPromptSubmit
Runs when the user submits a prompt, before Claude processes it.

## SessionStart
Runs when a session starts or resumes.

## SessionEnd
Runs when a session ends.

# Claude Code SDK – Summary

The **Claude Code SDK** allows you to run Claude Code programmatically inside your own applications, scripts, and workflows. It is available for **TypeScript, Python, and CLI**, and provides the same functionality as the terminal version.

---

## Core Capabilities

- Runs Claude Code programmatically  
- Uses the same tools and behavior as the CLI version  
- Inherits settings from the local `.claude` directory  
- Read-only permissions by default  
- Built for automation and workflow integration  

---

## Basic Example (TypeScript)

```ts
import { query } from "@anthropic-ai/claude-code";

const prompt = "Look for duplicate queries in the ./src/queries dir";

for await (const message of query({
  prompt,
})) {
  console.log(JSON.stringify(message, null, 2));
}



!!! LINK FOR A BETTER CHEATSHEET !!! ==================================== -> https://awesomeclaude.ai/code-cheatsheet
