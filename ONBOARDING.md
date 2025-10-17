# Team Onboarding Guide

Welcome to the team! This guide will get you set up to build MCP (Model Context Protocol) servers using our standardized workflow.

## What You'll Be Able To Do

After onboarding, you'll be able to:
- ✅ Create new MCP servers in minutes (not hours)
- ✅ Follow consistent patterns across the team
- ✅ Develop and test everything locally
- ✅ Use Claude Desktop to orchestrate Claude Code for complex tasks
- ✅ Build production-ready servers with comprehensive docs and tests

## Prerequisites

### Required
- ✅ **Claude Code CLI** installed and authenticated
- ✅ **Claude Desktop** installed
- ✅ **Python 3.12+** installed
- ✅ **Git** installed
- ✅ **Node.js 20+** installed (for npx)

### Not Required
- ❌ Google Cloud account (optional, only for deployment)
- ❌ Production credentials
- ❌ VPN or special network access

## Quick Start (5 minutes)

### Option 1: Automated Setup with Claude Code (RECOMMENDED)

**Just paste this into Claude Code:**

See [`SETUP-PROMPT.md`](./SETUP-PROMPT.md) for the complete prompt.

Claude Code will automatically:
1. Clone this repository
2. Read all documentation
3. Guide you through Claude Desktop configuration
4. Verify everything works
5. Create your first test server

### Option 2: Manual Setup

If you prefer to do it manually:

#### Step 1: Clone This Repository
```bash
cd ~/projects  # or wherever you keep projects
git clone https://github.com/tonton2006/mcp-starter-kit.git
cd mcp-starter-kit
```

#### Step 2: Configure Claude Desktop

Edit your Claude Desktop config file:
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Mac**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

Add this server:
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

**Important**: Restart Claude Desktop after making this change.

#### Step 3: Verify Setup

Open Claude Desktop and ask:
```
Do you have access to a tool called claude_code?
```

It should confirm it can use the `claude_code` tool.

#### Step 4: Create Your First Server

In Claude Desktop, use this prompt:
```
Your work folder is ~/projects/hello-mcp

Create a simple "Hello World" MCP server following the pattern from:
https://github.com/tonton2006/ssh-executor-mcp

Just one tool:
- say_hello: Takes a name parameter and returns "Hello, {name}!"

Keep it minimal but follow the structure: main.py, config.py, tools/, Dockerfile, etc.
```

#### Step 5: Run It Locally
```bash
cd ~/projects/hello-mcp
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
python -m mcp_server.main
```

Visit http://localhost:8080/health to verify it's running.

## Understanding The Workflow

### The Power Combo: Claude Desktop + Claude Code

**Claude Desktop** (Orchestrator)
- Understands high-level intent
- Plans implementation steps
- Calls Claude Code to execute

**Claude Code** (Executor)
- Reads example repositories
- Writes code following patterns
- Creates comprehensive documentation
- Runs tests

### Your Daily Workflow

1. **Think** about what you want to build
2. **Open Claude Desktop** and describe it
3. **Reference our patterns**: "Follow the pattern from ssh-executor-mcp"
4. **Let Claude Code build it**
5. **Review and test** locally
6. **Iterate** as needed

### Example Session

```
YOU: Create a Redis cache management MCP server

CLAUDE DESKTOP:
I'll create that for you. Let me use Claude Code...

[Calls claude_code tool with detailed prompt]

CLAUDE CODE:
Created redis-cache-mcp with:
- 6 tools (get, set, delete, keys, ttl, info)
- Comprehensive CLAUDE.md documentation
- Pytest tests with mocked Redis
- Dockerfile for deployment
- Full error handling

CLAUDE DESKTOP:
Done! Your server is ready at ~/projects/redis-cache-mcp
To run it:
1. cd ~/projects/redis-cache-mcp
2. python3 -m venv venv && source venv/bin/activate
3. pip install -r requirements.txt
4. python -m mcp_server.main

Would you like me to add any features?
```

## Key Resources

### Documentation
- **Main README**: Project overview and quick start
- **Claude Desktop Usage**: [`docs/claude-desktop-usage.md`](./docs/claude-desktop-usage.md)
- **Local Development**: [`docs/local-development.md`](./docs/local-development.md)
- **Prompt Library**: [`prompts/claude-desktop-prompts.md`](./prompts/claude-desktop-prompts.md)

### Example Implementations
- **SSH Executor**: https://github.com/tonton2006/ssh-executor-mcp
  - SSH command execution, file transfer, system info
  - Great example of external service integration

- **GCP Orchestrator**: https://github.com/tonton2006/ai-cloud-orchestrator
  - Cloud resource management
  - Shows GCP integration patterns
  - **Note**: Deployment configs are specific to original author's project

## Common Patterns

All our MCP servers follow this structure:

```
mcp-server/
├── mcp_server/
│   ├── __init__.py
│   ├── main.py              # FastMCP with @mcp.tool() decorators
│   ├── config.py            # Pydantic Settings
│   ├── tools/               # Tool implementations
│   │   └── *.py
│   └── utils/               # Shared utilities
│       └── *.py
├── tests/                   # Pytest tests
├── Dockerfile               # Python 3.12-slim, port 8080
├── requirements.txt
├── .env.example
├── .gitignore
├── CLAUDE.md               # For Claude Code to understand the project
└── README.md
```

### Key Conventions

**Error Handling:**
```python
{
    "status": "success" | "failed" | "error",
    "data": {...},
    "error": "message if error"
}
```

**Configuration:**
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    api_key: str
    log_level: str = "INFO"

    class Config:
        env_file = ".env"
```

**Tool Registration:**
```python
@mcp.tool()
async def my_tool(param: str) -> dict:
    """Description shown to AI clients"""
    return await tools.my_module.execute(param)
```

## Frequently Asked Questions

### Do I need a Google Cloud account?

**No!** You develop and test everything locally. Deployment to Cloud Run is optional.

### What if I want to deploy to Cloud Run?

You can deploy to your own GCP project:
```bash
gcloud run deploy my-server \
  --source . \
  --region us-central1 \
  --project YOUR_PROJECT_ID
```

See the deployment guide in the example repos.

### Can I use different cloud providers?

Yes! The servers are containerized (Dockerfile), so they run anywhere:
- AWS Fargate
- Azure Container Apps
- Heroku
- Railway
- Fly.io
- Or just localhost

### What if I don't know Python well?

That's okay! Claude Desktop + Claude Code generates production-quality Python code. You can:
1. Ask Claude Desktop to create the server
2. Review the generated code
3. Ask Claude Desktop to explain any parts you don't understand
4. Iterate with natural language requests

### How do I add a new tool to an existing server?

Use this prompt in Claude Desktop:
```
Your work folder is ~/projects/my-server

Add a new tool called [tool_name] that [description].
Follow the existing patterns in this server.
```

### How do I share my server with the team?

1. Push to GitHub
2. Add it to the starter kit README as an example
3. Others can reference it in their prompts

### Can I modify the example servers?

The example servers (ssh-executor, gcp-orchestrator) are references. Don't modify them directly. Instead:
1. Clone the server
2. Rename it
3. Modify your copy
4. Share your version if it's useful

## Getting Help

### Debugging Issues

**Server won't start:**
```bash
# Check logs
LOG_LEVEL=DEBUG python -m mcp_server.main

# Check port availability
lsof -ti:8080  # Mac/Linux
netstat -ano | findstr :8080  # Windows
```

**Import errors:**
```bash
# Ensure venv is activated
source venv/bin/activate  # or venv\Scripts\activate

# Reinstall dependencies
pip install -r requirements.txt
```

**Claude Desktop can't see claude_code tool:**
1. Check config file syntax (valid JSON)
2. Restart Claude Desktop
3. Verify npx is in PATH: `npx --version`

### Ask Claude Code

If you're stuck, ask Claude Code directly:
```
I'm having trouble with [issue] in my MCP server at ~/projects/my-server.
Can you help debug this?
```

### Ask Claude Desktop

For higher-level guidance:
```
I want to build an MCP server for [purpose] but I'm not sure how to structure it.
Can you suggest an approach based on our existing examples?
```

### Team Resources

- **Slack Channel**: #mcp-development (if you have one)
- **Code Reviews**: Push to GitHub and request review
- **Pair Programming**: Share screen with Claude Desktop + Claude Code workflow

## Best Practices

### 1. Always Start with a Prompt

Don't start coding manually. Let Claude Desktop + Claude Code generate the initial structure. It's faster and more consistent.

### 2. Test Locally First

Never deploy without local testing:
```bash
python -m mcp_server.main
# Test in another terminal
curl http://localhost:8080/health
```

### 3. Write (or Generate) Tests

Ask Claude Desktop to add tests:
```
Your work folder is ~/projects/my-server

Add comprehensive pytest tests for all tools.
Mock external dependencies.
Achieve >80% coverage.
```

### 4. Document Everything

Every server should have:
- **README.md**: For users
- **CLAUDE.md**: For Claude Code (future modifications)
- **Inline comments**: For complex logic

### 5. Use .env for Configuration

Never hardcode:
- API keys
- Database URLs
- Credentials

Always use `.env` files (gitignored) and `.env.example` (committed).

### 6. Follow the Patterns

Consistency helps the team:
- Same structure
- Same error handling
- Same configuration approach
- Same documentation style

## Your First Week

### Day 1: Setup
- ✅ Complete this onboarding
- ✅ Configure Claude Desktop
- ✅ Create "Hello World" server
- ✅ Run it locally

### Day 2: Learn
- ✅ Clone and run ssh-executor example
- ✅ Read its CLAUDE.md to understand architecture
- ✅ Experiment with adding a tool

### Day 3: Build
- ✅ Create your first real server
- ✅ Use a prompt from the prompt library
- ✅ Test locally
- ✅ Push to GitHub

### Day 4: Iterate
- ✅ Add features to your server
- ✅ Add comprehensive tests
- ✅ Improve documentation

### Day 5: Share
- ✅ Demo your server to the team
- ✅ Get feedback
- ✅ Update based on feedback

## Advanced Topics (After Week 1)

Once comfortable with the basics:

### Multi-Server Integration
Create servers that call other MCP servers:
```python
# Call another MCP server's tool
response = await httpx.post(
    "http://other-server:8080/mcp/call",
    json={"tool": "tool_name", "parameters": {...}}
)
```

### Custom Authentication
Add authentication to your servers:
- API keys
- JWT tokens
- OAuth 2.0

### Performance Optimization
- Connection pooling
- Caching
- Async operations
- Load testing

### CI/CD Pipeline
Set up automated:
- Testing (pytest)
- Linting (black, ruff)
- Type checking (mypy)
- Deployment

## Success Metrics

You'll know you're successful when:
- ✅ You can create a new MCP server in <30 minutes
- ✅ Your servers follow team patterns consistently
- ✅ You're comfortable with the Claude Desktop workflow
- ✅ You can debug issues independently
- ✅ You're contributing examples back to the team

## Welcome Aboard!

You're now part of a team that builds AI-powered tools efficiently. The combination of:
- Standardized patterns (from this starter kit)
- Example implementations (ssh-executor, gcp-orchestrator)
- AI orchestration (Claude Desktop + Claude Code)

...means you'll be incredibly productive.

**Questions?** Ask in the team channel or open an issue in the starter kit repo.

**Ready to start?** Use the automated setup prompt in [`SETUP-PROMPT.md`](./SETUP-PROMPT.md)!

Happy building! 🚀
