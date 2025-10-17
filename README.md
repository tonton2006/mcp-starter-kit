# MCP Server Starter Kit

A comprehensive guide and template collection for building Model Context Protocol (MCP) servers using FastMCP and deploying them to Google Cloud Run.

## 🚀 New Team Member? Start Here!

**For Automated Setup** (5 minutes):
1. Open Claude Code (the CLI)
2. Copy and paste the prompt from [`SETUP-PROMPT.md`](./SETUP-PROMPT.md)
3. Follow the interactive guidance
4. Done! You'll have everything configured and a test server running.

**For Manual Setup** or more details:
- Read the full [`ONBOARDING.md`](./ONBOARDING.md) guide

---

## Quick Start for Team Members

This starter kit provides:
- 📚 Working example implementations to learn from
- 🎯 Clean templates to start new projects
- 📖 Comprehensive documentation and patterns
- 🤖 Claude Desktop prompts for automated development

## Prerequisites

- Python 3.12+
- Claude Code CLI installed
- Claude Desktop (optional, for orchestration workflow)
- Google Cloud SDK (optional, for deployment)

## Example Implementations

Learn from these production-ready MCP servers:

### 🔐 [SSH Executor MCP Server](https://github.com/tonton2006/ssh-executor-mcp)
Execute commands and manage files on remote servers via SSH
- Command execution (shell, package installation, scripts)
- SFTP file transfer (upload/download)
- System information gathering
- OS Login (IAM-based) and key-based authentication

### ☁️ [GCP Cloud Orchestrator MCP Server](https://github.com/tonton2006/ai-cloud-orchestrator)
Manage Google Cloud Platform resources
- Compute Engine VMs (create, start, stop, delete)
- Cloud Run services (deploy, update, traffic management)
- Firewall rules and network management
- Resource aggregation and search

> **Note**: The GCP Orchestrator includes deployment-specific configurations (project IDs, regions). Use it as a reference for patterns, but don't deploy it directly to your GCP project.

## Getting Started

### 1. Set Up Claude Desktop with Claude Code

First, enable Claude Desktop to orchestrate Claude Code for complex tasks:

```bash
# Add to your Claude Desktop config:
# Windows: %APPDATA%/Claude/claude_desktop_config.json
# Mac: ~/Library/Application Support/Claude/claude_desktop_config.json
# Linux: ~/.config/Claude/claude_desktop_config.json
```

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

**Restart Claude Desktop** after adding this configuration.

### 2. Clone This Repository

```bash
git clone https://github.com/tonton2006/mcp-starter-kit.git
cd mcp-starter-kit
```

### 3. Use Claude Desktop to Create Your Server

Open Claude Desktop and use one of the prompts from [`prompts/claude-desktop-prompts.md`](./prompts/claude-desktop-prompts.md):

**Example:**
```
Your work folder is ~/projects/my-database-mcp

Create a new MCP server for PostgreSQL database management following
the FastMCP Cloud Run template pattern from:
https://github.com/tonton2006/ssh-executor-mcp

Requirements:
- Tools for: connecting to databases, running queries, managing tables
- Follow the SSH Executor's authentication and error handling patterns
- Use FastMCP 2.3.0+ with Pydantic Settings
- Include comprehensive CLAUDE.md documentation
```

Claude Desktop will invoke Claude Code to:
- Clone/reference the example repositories
- Create your new MCP server following the patterns
- Set up the project structure
- Write comprehensive documentation
- Create tests

### 4. Run Locally

```bash
cd ~/projects/my-database-mcp
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python -m mcp_server.main
```

Your server runs on `http://localhost:8080` and can be tested with MCP clients.

## Architecture Pattern

All servers follow this FastMCP + Cloud Run pattern:

```
mcp-server/
├── mcp_server/
│   ├── __init__.py
│   ├── main.py              # FastMCP entry point with @mcp.tool() decorators
│   ├── config.py            # Pydantic Settings for environment variables
│   ├── tools/               # Tool implementations (async functions)
│   │   ├── __init__.py
│   │   └── [tool_modules].py
│   └── utils/               # Shared utilities
│       ├── logger.py
│       └── [service_utils].py
├── tests/                   # Pytest test suite
├── Dockerfile               # Python 3.12-slim, port 8080
├── requirements.txt         # FastMCP, dependencies
├── .env.example             # Environment variable template
├── .gitignore              # Python, venv, secrets
├── CLAUDE.md               # Documentation for Claude Code
└── README.md               # User-facing documentation
```

## Key Patterns

### FastMCP Tool Registration
```python
# mcp_server/main.py
from fastmcp import FastMCP

mcp = FastMCP("Your Server Name")

@mcp.tool()
async def your_tool(param1: str, param2: int = 10) -> dict:
    """
    Tool description (shown to LLM clients)

    Args:
        param1: Description of param1
        param2: Description of param2 (default: 10)

    Returns:
        Dict with status and results
    """
    # Delegate to service module
    return await tools.your_module.execute(param1, param2)
```

### Error Handling Convention
```python
{
    "status": "success" | "failed" | "error",
    # ... operation-specific fields
    "error": "error message"  # only present on error
}
```

### Configuration with Pydantic
```python
# mcp_server/config.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    log_level: str = "INFO"
    api_key: str = ""  # From environment

    class Config:
        env_file = ".env"
```

## Documentation

- [Creating a New MCP Server](./docs/creating-new-server.md)
- [Local Development Guide](./docs/local-development.md)
- [Deployment to Cloud Run](./docs/deployment-guide.md)
- [Testing Best Practices](./docs/testing-guide.md)
- [Claude Desktop Usage Patterns](./docs/claude-desktop-usage.md)
- [Prompt Library for Claude Desktop](./prompts/claude-desktop-prompts.md)

## Workflow: Claude Desktop + Claude Code

**The Power Combo:**
1. **Claude Desktop** (orchestrator) - Plans and coordinates
2. **Claude Code** (executor) - Implements and executes

**Example Workflow:**
```
You (in Claude Desktop): "Create a new MCP server for Redis cache management"

Claude Desktop:
- Understands requirements
- Formulates implementation plan
- Calls claude_code tool with detailed prompt

Claude Code:
- Reads example implementations
- Creates project structure
- Writes code following patterns
- Sets up tests
- Generates documentation
- Returns summary to Claude Desktop

Claude Desktop: Shows you the summary and next steps
```

## Team Development

### Running Servers Locally

Team members don't need GCP accounts to develop MCP servers. Everything runs locally:

```bash
# Terminal 1: Run your MCP server
cd ~/projects/your-server
python -m mcp_server.main

# Terminal 2: Test with an MCP client or Claude Desktop
```

### Optional: Deploying to Your Own Cloud Run

If you want to deploy to Cloud Run (not required for local dev):

```bash
# 1. Ensure you have a GCP project
gcloud config set project YOUR_PROJECT_ID

# 2. Enable required APIs
gcloud services enable run.googleapis.com

# 3. Deploy
gcloud run deploy your-server-name \
  --source . \
  --region us-central1 \
  --allow-unauthenticated \
  --project YOUR_PROJECT_ID
```

## Template

A clean starting template is available in [`templates/fastmcp-cloudrun/`](./templates/fastmcp-cloudrun/).

You can copy it manually, but we recommend using Claude Desktop with the prompts in [`prompts/claude-desktop-prompts.md`](./prompts/claude-desktop-prompts.md) to generate customized servers.

## Contributing

This starter kit is designed to help the team build consistent, high-quality MCP servers. If you discover new patterns or best practices:

1. Update the relevant documentation
2. Consider adding examples to the template
3. Share prompt patterns that worked well

## Resources

- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [FastMCP Documentation](https://github.com/jlowin/fastmcp)
- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)
- [Google Cloud Run Documentation](https://cloud.google.com/run/docs)

## License

MIT License - Feel free to use for your projects.
