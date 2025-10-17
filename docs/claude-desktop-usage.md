# Claude Desktop Usage Guide

This guide explains how to use Claude Desktop with the `claude-code-mcp` tool to build MCP servers efficiently.

## The Orchestration Workflow

**Claude Desktop + Claude Code = Powerful Development**

- **Claude Desktop**: High-level planning and orchestration
- **Claude Code**: Implementation and execution

This separation allows you to describe *what* you want in natural language, while Claude Code handles the *how*.

## Setup

### 1. Configure Claude Desktop

Add the `claude-code-mcp` server to your Claude Desktop configuration:

**Windows:**
```
%APPDATA%\Claude\claude_desktop_config.json
```

**Mac:**
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Linux:**
```
~/.config/Claude/claude_desktop_config.json
```

Add this entry:
```json
{
  "mcpServers": {
    "claude-code-mcp": {
      "command": "npx",
      "args": ["-y", "@steipete/claude-code-mcp@latest"]
    }
  }
}
```

### 2. Restart Claude Desktop

The configuration is only loaded on startup.

### 3. Verify It's Working

In Claude Desktop, try:
```
Can you see a tool called claude_code?
```

Claude Desktop should confirm it has access to the `claude_code` tool.

## Using Claude Desktop for MCP Development

### Basic Pattern

```
Your work folder is ~/projects/[project-name]

[Your request here]
```

**The "Your work folder is..." line is CRITICAL** - it tells Claude Code where to work.

### Example 1: Create New Server

```
Your work folder is ~/projects/slack-bot-mcp

Create a new MCP server for Slack integration following the pattern from
https://github.com/tonton2006/ssh-executor-mcp

Tools needed:
- send_message: Post message to channel
- list_channels: Get list of channels
- get_messages: Get recent messages from channel
- set_status: Update user status

Use Slack Web API with bot token from environment variable.
Include full documentation and tests.
```

### Example 2: Add Feature to Existing Server

```
Your work folder is ~/projects/slack-bot-mcp

Add a new tool called get_user_info that retrieves information about
a Slack user by their user ID or email address.

Follow the existing patterns in this server.
Include tests.
```

### Example 3: Debug an Issue

```
Your work folder is ~/projects/slack-bot-mcp

The send_message tool is returning a 401 error. Please:
1. Review the authentication code
2. Check if the bot token is being passed correctly
3. Fix any issues found
4. Test that it works
```

### Example 4: Improve Documentation

```
Your work folder is ~/projects/slack-bot-mcp

Update the CLAUDE.md file to include:
- Architecture diagram description
- Examples of each tool usage
- Common troubleshooting scenarios
- Integration patterns with other MCP servers
```

## Advanced Usage

### Multi-Step Workflows

Claude Desktop can break down complex tasks:

```
Your work folder is ~/projects/monitoring-mcp

Create a comprehensive monitoring MCP server that:
1. Checks if services are running
2. Monitors disk space and memory
3. Sends alerts via Slack (integrate with slack-bot-mcp)
4. Generates health check reports

Reference the ssh-executor patterns for remote execution.
```

Claude Desktop will:
1. Plan the implementation
2. Call Claude Code multiple times if needed
3. Verify each step
4. Report progress back to you

### Referencing Multiple Examples

```
Your work folder is ~/projects/api-gateway-mcp

Create an API gateway MCP server that combines patterns from:
- https://github.com/tonton2006/ssh-executor-mcp (authentication patterns)
- https://github.com/tonton2006/ai-cloud-orchestrator (GCP integration)

This server should proxy requests to multiple backend APIs with:
- Request authentication
- Rate limiting
- Response caching
- Error handling
```

### Iterative Development

You can have a conversation with Claude Desktop:

```
You: Create a basic Redis MCP server with get/set tools
[Claude Desktop creates it]

You: Add support for lists (lpush, rpop, lrange)
[Claude Desktop adds the features]

You: Now add pub/sub support
[Claude Desktop adds pub/sub]

You: Great! Now add comprehensive tests for everything
[Claude Desktop adds tests]
```

## Best Practices

### 1. Always Specify Working Directory

❌ **Don't:**
```
Create a new MCP server for databases
```

✅ **Do:**
```
Your work folder is ~/projects/database-mcp

Create a new MCP server for databases...
```

### 2. Reference Example Implementations

❌ **Don't:**
```
Create an MCP server with good architecture
```

✅ **Do:**
```
Create an MCP server following the pattern from:
https://github.com/tonton2006/ssh-executor-mcp
```

### 3. Be Specific About Requirements

❌ **Don't:**
```
Add some database tools
```

✅ **Do:**
```
Add these specific tools:
- postgres_query: Execute SQL SELECT/INSERT/UPDATE/DELETE
- postgres_list_tables: Return list of table names
- postgres_describe_table: Return column names and types
```

### 4. Request Documentation

❌ **Don't:**
```
Create the server
```

✅ **Do:**
```
Create the server with:
- Comprehensive CLAUDE.md for Claude Code
- README.md for users
- Inline code comments
```

### 5. Ask for Tests

❌ **Don't:**
```
Just create the functionality
```

✅ **Do:**
```
Include pytest tests with:
- Success cases
- Error cases
- Mocked dependencies
- >80% coverage
```

## What Claude Code Can See

When Claude Desktop calls Claude Code:

**✅ Claude Code CAN see:**
- Files in the working directory you specify
- Public GitHub repositories (via URLs)
- Files you've previously created
- Environment (OS, available tools)

**❌ Claude Code CANNOT see:**
- Other users' local files
- Private repositories without access
- Your GCP project (unless credentials are configured)
- Files outside the working directory

## Local Development Only

**Key Point**: Team members run servers locally during development.

```
Your work folder is ~/projects/my-server

Run the server locally on port 8080 for testing
```

They DON'T need:
- GCP accounts
- Cloud Run access
- Deployment credentials

Everything works on `localhost:8080`.

## When to Deploy

After local development and testing, team members can optionally deploy to their own Cloud Run instances:

```
Your work folder is ~/projects/my-server

Deploy this server to Cloud Run in my GCP project: my-team-project-123
Region: us-central1
Service name: my-server
```

But this is optional - local development is often sufficient.

## Troubleshooting

### Claude Desktop Doesn't Use Claude Code

**Symptoms**: Claude Desktop tries to implement things itself instead of calling the tool.

**Fix**:
1. Restart Claude Desktop
2. Verify config file is valid JSON
3. Try explicitly asking: "Use the claude_code tool to do this"

### "Permission Denied" or "Cannot Access File"

**Symptoms**: Claude Code says it can't access files.

**Fix**:
1. Verify the working directory path is correct
2. Use absolute paths (~/projects/..., not relative paths)
3. Ensure the directory exists (or ask Claude Code to create it first)

### Server Not Following Patterns

**Symptoms**: Generated code doesn't match your examples.

**Fix**:
1. Be more explicit: "Follow the exact structure from [URL]"
2. Reference specific files: "Use the same error handling pattern as tools/commands.py in ssh-executor"
3. Provide the example URL in the prompt

### Clone vs Reference

Claude Desktop might ask Claude Code to clone repos or just reference them:

- **Clone**: Downloads the repo, can modify it
- **Reference**: Reads files via GitHub API, doesn't download

Both work fine. If you want a local copy:
```
Clone https://github.com/tonton2006/ssh-executor-mcp to ~/projects/ssh-executor-example
for reference during development
```

## Example Session Transcript

Here's what a real session looks like:

```
USER: Your work folder is ~/projects/weather-mcp

Create a new MCP server for weather data that fetches current weather
and forecasts using the OpenWeatherMap API.

Reference: https://github.com/tonton2006/ssh-executor-mcp

Tools needed:
- get_current_weather: Get current weather for city
- get_forecast: Get 5-day forecast for city
- get_weather_alerts: Get active weather alerts

Use OpenWeatherMap API key from OPENWEATHERMAP_API_KEY env var.

---

CLAUDE DESKTOP: I'll create this MCP server for you using Claude Code...

[Calls claude_code tool with the prompt]

---

CLAUDE CODE: (working...)
- Created project structure
- Implemented mcp_server/main.py with FastMCP
- Created tools/weather.py with three tools
- Added config.py with Pydantic settings
- Created Dockerfile
- Added requirements.txt
- Created .env.example
- Added .gitignore
- Wrote comprehensive CLAUDE.md
- Created README.md
- Added basic tests

---

CLAUDE DESKTOP: I've created your weather MCP server! Here's what was built:

[Shows summary]

To run it:
1. cd ~/projects/weather-mcp
2. python3 -m venv venv
3. source venv/bin/activate
4. pip install -r requirements.txt
5. cp .env.example .env (then add your API key)
6. python -m mcp_server.main

Would you like me to add any additional features?

---

USER: Yes, add a tool to get historical weather data for the past week

---

CLAUDE DESKTOP: [Calls claude_code tool again to add the feature]

Done! Added get_historical_weather tool. The server is ready to use.
```

## Tips for Team Adoption

### For New Team Members

1. **Start with examples**: Have them clone and run an existing server
2. **Use prompts**: Give them the prompts from `prompts/claude-desktop-prompts.md`
3. **Iterate**: Start simple, add features incrementally
4. **Review**: Have experienced team members review generated code

### For Experienced Developers

Claude Desktop + Claude Code speeds up:
- **Boilerplate creation**: Project structure, configs, etc.
- **Pattern replication**: "Make another tool like X"
- **Documentation**: "Update CLAUDE.md with these changes"
- **Testing**: "Add tests for this new feature"

You still review, refine, and understand the code - but the mechanical work is automated.

## Next Steps

1. ✅ Configure Claude Desktop with `claude-code-mcp`
2. ✅ Restart Claude Desktop
3. 📝 Try a simple prompt from the prompt library
4. 🔍 Review the generated code
5. 🚀 Iterate and improve
6. 📚 Document patterns that work well for your team

Happy building!
