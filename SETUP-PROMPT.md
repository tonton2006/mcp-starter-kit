# Automated Setup Prompt for New Team Members

## How to Use This

1. Open **Claude Code** (the CLI, not Claude Desktop)
2. Copy the entire prompt below
3. Paste it into Claude Code
4. Follow the interactive guidance

Claude Code will automatically set up everything for you!

---

## The Prompt

```
I'm a new team member and need to get set up to build MCP servers.

Please help me with automated onboarding:

STEP 1: Clone the starter kit
- Clone https://github.com/tonton2006/mcp-starter-kit to ~/projects/mcp-starter-kit
- Read the README.md to understand the project
- Read docs/claude-desktop-usage.md
- Read docs/local-development.md
- Summarize what I'll be able to do after setup

STEP 2: Check prerequisites
- Verify Python 3.12+ is installed (python3 --version)
- Verify Node.js 20+ is installed (node --version)
- Verify npx is available (npx --version)
- Verify git is installed (git --version)
- If anything is missing, tell me how to install it

STEP 3: Guide me through Claude Desktop configuration
- Show me where my Claude Desktop config file should be located based on my OS
- Show me the exact JSON I need to add for the claude-code-mcp server
- Explain that I need to restart Claude Desktop after making this change
- Create a test file ~/claude-desktop-config-addition.json with the config I need to add

STEP 4: Create a test "Hello World" MCP server
- Working directory: ~/projects/hello-mcp
- Reference implementation: https://github.com/tonton2006/ssh-executor-mcp
- Create a minimal MCP server with:
  - One tool: say_hello(name: str) -> dict that returns "Hello, {name}!"
  - Follow the standard structure: mcp_server/main.py, config.py, tools/, etc.
  - Include Dockerfile, requirements.txt, .env.example, .gitignore
  - Add basic CLAUDE.md documentation
  - Keep it simple but correct

STEP 5: Verify the test server works
- Set up venv and install dependencies
- Try to start the server
- Test the /health endpoint
- If there are any errors, fix them
- Show me how to test the say_hello tool

STEP 6: Next steps summary
- Explain how to use Claude Desktop with the claude_code tool
- Point me to the prompt library in prompts/claude-desktop-prompts.md
- Suggest a good first real project to build
- Give me the key resources I should bookmark

Please go through each step interactively, waiting for confirmation before proceeding to the next step when appropriate.
```

---

## What This Will Do

Claude Code will:

1. ✅ Clone the starter kit repository to your machine
2. ✅ Read all documentation and summarize it for you
3. ✅ Check that you have all required tools installed
4. ✅ Generate the exact Claude Desktop config you need
5. ✅ Create a working "Hello World" MCP server
6. ✅ Set up Python environment and dependencies
7. ✅ Start the server and verify it works
8. ✅ Give you clear next steps

## Time Required

About **5-10 minutes** including reading and following along.

## After Setup

You'll have:
- ✅ Starter kit cloned and understood
- ✅ Claude Desktop configured correctly
- ✅ Your first working MCP server (`hello-mcp`)
- ✅ Understanding of the workflow
- ✅ Next steps to build real servers

## What Happens Next?

### 1. Configure Claude Desktop

Claude Code will create a file with the config you need. You'll need to:
1. Open your Claude Desktop config file (location shown by Claude Code)
2. Add the JSON provided
3. Save the file
4. Restart Claude Desktop

### 2. Verify Claude Desktop Setup

Open Claude Desktop and ask:
```
Do you have access to a tool called claude_code?
```

It should confirm yes.

### 3. Build Your First Real Server

In Claude Desktop (not Claude Code), use a prompt like:
```
Your work folder is ~/projects/my-first-server

Create a new MCP server for [your use case] following the pattern from
https://github.com/tonton2006/ssh-executor-mcp

Requirements:
- Tool 1: [description]
- Tool 2: [description]
```

### 4. Join the Team Workflow

You're now ready to:
- Build MCP servers using Claude Desktop orchestration
- Follow team patterns and conventions
- Develop and test locally
- Share your servers with the team

## Troubleshooting

### If Python/Node/Git is Missing

Claude Code will tell you how to install them for your OS:
- **Mac**: Use Homebrew (`brew install python@3.12`)
- **Linux**: Use apt/yum (`sudo apt install python3.12`)
- **Windows**: Download from official websites

### If Claude Desktop Can't Find claude_code Tool

1. Check the config file syntax (must be valid JSON)
2. Restart Claude Desktop (config only loads on startup)
3. Verify npx works: `npx --version`

### If the Hello MCP Server Won't Start

Claude Code will debug and fix issues. Common problems:
- Port 8080 already in use
- Virtual environment not activated
- Missing dependencies

### If You Get Stuck

Ask Claude Code:
```
I'm having trouble with [issue]. Can you help debug this?
```

Or ask Claude Desktop (after it's configured):
```
I completed the automated setup but [issue]. Can you help?
```

## Alternative: Manual Setup

If you prefer to set up manually, follow the instructions in [`ONBOARDING.md`](./ONBOARDING.md).

## Example Output

Here's what a successful setup looks like:

```bash
$ python -m mcp_server.main
INFO:     Started server process
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8080

$ curl http://localhost:8080/health
{"status":"healthy"}

$ # Test the tool (in another terminal)
$ curl -X POST http://localhost:8080/mcp/call \
  -H "Content-Type: application/json" \
  -d '{"tool":"say_hello","parameters":{"name":"World"}}'
{"status":"success","message":"Hello, World!"}
```

## Pro Tips

### Save the Prompt

Bookmark this page or save the prompt locally. You can share it with new team members:
```bash
# Share with new team member
cat ~/projects/mcp-starter-kit/SETUP-PROMPT.md
```

### Use It for Onboarding Others

When someone new joins:
1. Send them this file
2. They paste the prompt into Claude Code
3. They're ready to go in 10 minutes

### Customize It

If your team has additional requirements:
1. Fork the starter kit
2. Modify this setup prompt
3. Add team-specific steps

## Ready to Start?

**Copy the prompt above and paste it into Claude Code now!**

After setup, come back to the main README and start building! 🚀

---

## Questions?

If you have questions about the setup process:
- Ask Claude Code during setup
- Check the [`ONBOARDING.md`](./ONBOARDING.md) for more details
- Ask in the team chat
