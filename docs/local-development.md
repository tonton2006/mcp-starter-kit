# Local Development Guide

This guide covers running and testing MCP servers locally on your machine without deploying to Cloud Run.

## Why Develop Locally?

- ⚡ **Fast iteration**: No deployment delays
- 💰 **No costs**: No cloud resource usage
- 🔒 **Privacy**: Data stays on your machine
- 🧪 **Easy testing**: Full control over environment
- 🔄 **Quick debugging**: Immediate feedback

## Prerequisites

- Python 3.12+ installed
- Git installed
- Code editor (VS Code, PyCharm, etc.)
- Terminal/command line access

**You DON'T need:**
- Google Cloud account
- Cloud Run access
- Production credentials
- Deployment permissions

## Quick Start

### 1. Clone or Create Your Server

**Option A: Clone an example**
```bash
git clone https://github.com/tonton2006/ssh-executor-mcp.git
cd ssh-executor-mcp
```

**Option B: Create new with Claude Desktop**
Use a prompt from `prompts/claude-desktop-prompts.md`.

### 2. Set Up Python Environment

```bash
# Create virtual environment
python3 -m venv venv

# Activate it
# Linux/Mac:
source venv/bin/activate

# Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 3. Configure Environment Variables

```bash
# Copy example config
cp .env.example .env

# Edit with your values
nano .env  # or use your editor
```

Example `.env`:
```bash
# API keys (if needed)
API_KEY=your_api_key_here

# Connection settings
DATABASE_URL=localhost:5432
REDIS_URL=localhost:6379

# Logging
LOG_LEVEL=DEBUG
```

### 4. Run the Server

```bash
python -m mcp_server.main
```

You should see:
```
INFO:     Started server process
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://0.0.0.0:8080
```

Your MCP server is now running on `http://localhost:8080`!

## Testing Your Server

### Method 1: Using Claude Desktop

Add your local server to Claude Desktop config:

```json
{
  "mcpServers": {
    "my-local-server": {
      "command": "npx",
      "args": [
        "mcp-client",
        "http://localhost:8080/mcp"
      ]
    }
  }
}
```

Restart Claude Desktop, then ask it to use your tools.

### Method 2: Using Claude Code

Configure in `.claude.json`:
```json
{
  "mcpServers": {
    "my-local-server": {
      "command": "python",
      "args": ["-m", "mcp_server.main"],
      "cwd": "/path/to/your/server"
    }
  }
}
```

Then use tools in Claude Code sessions.

### Method 3: Using HTTP Requests

Test tools directly:

```bash
# Health check
curl http://localhost:8080/health

# List available tools (MCP endpoint)
curl http://localhost:8080/mcp/tools

# Call a tool
curl -X POST http://localhost:8080/mcp/call \
  -H "Content-Type: application/json" \
  -d '{
    "tool": "your_tool_name",
    "parameters": {
      "param1": "value1"
    }
  }'
```

### Method 4: Using Python Script

```python
# test_client.py
import requests

BASE_URL = "http://localhost:8080/mcp"

def test_tool():
    response = requests.post(f"{BASE_URL}/call", json={
        "tool": "your_tool_name",
        "parameters": {
            "param1": "value1"
        }
    })
    print(response.json())

if __name__ == "__main__":
    test_tool()
```

## Development Workflow

### 1. Make Changes

Edit code in your editor:
```bash
# Example: Add a new tool
nano mcp_server/tools/my_new_tool.py
```

### 2. Restart Server

Stop the server (Ctrl+C) and restart:
```bash
python -m mcp_server.main
```

Or use auto-reload during development:
```bash
# Install watchdog
pip install watchdog

# Run with auto-reload
uvicorn mcp_server.main:app --reload --host 0.0.0.0 --port 8080
```

### 3. Test Changes

Run your tests:
```bash
pytest
```

Or test specific file:
```bash
pytest tests/test_my_new_tool.py -v
```

### 4. Iterate

Repeat steps 1-3 until satisfied.

## Running Tests

### Basic Test Run

```bash
# Run all tests
pytest

# Run with output
pytest -v

# Run specific test file
pytest tests/test_tools.py

# Run specific test function
pytest tests/test_tools.py::test_specific_function
```

### With Coverage

```bash
# Install coverage tool
pip install pytest-cov

# Run with coverage
pytest --cov=mcp_server --cov-report=html

# View coverage report
open htmlcov/index.html  # Mac
xdg-open htmlcov/index.html  # Linux
start htmlcov/index.html  # Windows
```

### Watch Mode

```bash
# Install pytest-watch
pip install pytest-watch

# Run tests on file changes
ptw
```

## Debugging

### Using Print Statements

```python
# mcp_server/tools/my_tool.py
async def my_tool(param: str):
    print(f"DEBUG: Received param: {param}")  # Simple debugging
    result = do_something(param)
    print(f"DEBUG: Result: {result}")
    return result
```

### Using Python Debugger

```python
# mcp_server/tools/my_tool.py
async def my_tool(param: str):
    import pdb; pdb.set_trace()  # Breakpoint
    result = do_something(param)
    return result
```

Run server, trigger the tool, and you'll drop into debugger.

### Using VS Code Debugger

Create `.vscode/launch.json`:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: MCP Server",
      "type": "python",
      "request": "launch",
      "module": "mcp_server.main",
      "console": "integratedTerminal",
      "justMyCode": true
    }
  ]
}
```

Set breakpoints in VS Code and press F5 to debug.

### Viewing Logs

```bash
# Run with debug logging
LOG_LEVEL=DEBUG python -m mcp_server.main

# Or set in .env
echo "LOG_LEVEL=DEBUG" >> .env
```

## Common Issues

### Port Already in Use

```
Error: Address already in use
```

**Solution 1**: Kill the process using port 8080
```bash
# Linux/Mac
lsof -ti:8080 | xargs kill -9

# Windows
netstat -ano | findstr :8080
taskkill /PID <PID> /F
```

**Solution 2**: Use a different port
```bash
# Change port in code or:
uvicorn mcp_server.main:app --port 8081
```

### Module Not Found

```
ModuleNotFoundError: No module named 'fastmcp'
```

**Solution**: Activate venv and install dependencies
```bash
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
```

### Import Errors

```
ImportError: attempted relative import with no known parent package
```

**Solution**: Run as module, not as script
```bash
# Don't do this:
python mcp_server/main.py

# Do this:
python -m mcp_server.main
```

### Environment Variables Not Loading

```
KeyError: 'API_KEY'
```

**Solution**: Ensure .env file exists and is loaded
```python
# Check config.py has:
class Settings(BaseSettings):
    api_key: str

    class Config:
        env_file = ".env"
```

## Using with External Services

### Databases

Run databases locally with Docker:

```bash
# PostgreSQL
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  postgres:15

# Redis
docker run -d \
  --name redis \
  -p 6379:6379 \
  redis:7

# MongoDB
docker run -d \
  --name mongo \
  -p 27017:27017 \
  mongo:7
```

Update your `.env`:
```bash
POSTGRES_URL=postgresql://postgres:password@localhost:5432/mydb
REDIS_URL=redis://localhost:6379
MONGO_URL=mongodb://localhost:27017
```

### Mock External APIs

For services you can't run locally (Slack, AWS, etc.), use mocks in tests:

```python
# tests/test_slack_tools.py
import pytest
from unittest.mock import patch

@patch('mcp_server.tools.slack.slack_client.chat_postMessage')
def test_send_message(mock_post):
    mock_post.return_value = {"ok": True, "ts": "1234567890"}

    result = await send_message("test-channel", "Hello")

    assert result["status"] == "success"
    mock_post.assert_called_once()
```

## Performance Testing

### Load Testing

```bash
# Install wrk
# Mac: brew install wrk
# Linux: apt install wrk

# Run load test
wrk -t4 -c100 -d30s http://localhost:8080/health

# Results show requests/sec, latency, etc.
```

### Profiling

```bash
# Install memory profiler
pip install memory-profiler

# Profile your code
python -m memory_profiler mcp_server/main.py
```

## Code Quality

### Formatting

```bash
# Install black
pip install black

# Format code
black mcp_server/ tests/

# Check without changing
black --check mcp_server/ tests/
```

### Linting

```bash
# Install ruff
pip install ruff

# Lint code
ruff check mcp_server/ tests/

# Auto-fix issues
ruff check --fix mcp_server/ tests/
```

### Type Checking

```bash
# Install mypy
pip install mypy

# Type check
mypy mcp_server/
```

## Hot Reload Development

For fastest iteration:

```bash
# Install uvicorn with reload
pip install uvicorn[standard]

# Run with auto-reload
uvicorn mcp_server.main:app --reload --host 0.0.0.0 --port 8080
```

Now code changes automatically reload the server!

## Next Steps

Once your server works locally:

1. ✅ Add comprehensive tests
2. ✅ Update documentation (CLAUDE.md, README.md)
3. ✅ Commit to git
4. 🚀 **Optionally**: Deploy to Cloud Run (see deployment guide)

But remember: local development is often sufficient! You don't need to deploy unless you're integrating with other cloud services or need public access.

## Quick Reference

```bash
# Setup
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env

# Run server
python -m mcp_server.main

# Run with reload
uvicorn mcp_server.main:app --reload

# Test
pytest
pytest --cov=mcp_server

# Code quality
black mcp_server/ tests/
ruff check mcp_server/ tests/
mypy mcp_server/

# Debug
LOG_LEVEL=DEBUG python -m mcp_server.main
```

Happy local development!
