# Claude Desktop Prompts for MCP Server Development

This document contains standardized prompts to use with Claude Desktop (once you've configured the `claude-code-mcp` tool). These prompts leverage Claude Code to automatically create MCP servers following our team's patterns.

## Prerequisites

1. Claude Desktop configured with `claude-code-mcp` server (see main README)
2. Example repositories cloned or accessible:
   - https://github.com/tonton2006/ssh-executor-mcp
   - https://github.com/tonton2006/ai-cloud-orchestrator

## How to Use These Prompts

1. Open Claude Desktop
2. Copy one of the prompts below
3. Replace `[PLACEHOLDERS]` with your specific values
4. Paste into Claude Desktop
5. Claude Desktop will call Claude Code to execute the work
6. Review the created code

---

## Prompt 1: Create New MCP Server from Scratch

Use this to create a completely new MCP server following the FastMCP + Cloud Run pattern.

```
Your work folder is ~/projects/[SERVER-NAME]

Create a new MCP server for [PURPOSE/DESCRIPTION].

Reference Implementation:
- Clone or read: https://github.com/tonton2006/ssh-executor-mcp
- Follow its structure, patterns, and conventions

Requirements:
1. Server name: [SERVER-NAME]
2. Purpose: [DETAILED PURPOSE]
3. Tools needed:
   - [tool_name_1]: [description]
   - [tool_name_2]: [description]
   - [tool_name_3]: [description]

4. Follow these patterns from ssh-executor:
   - FastMCP 2.3.0+ with @mcp.tool() decorators
   - Pydantic Settings for configuration
   - Structure: mcp_server/main.py, config.py, tools/, utils/
   - Error handling: return dicts with "status" field
   - Async tool functions
   - Comprehensive docstrings
   - Port 8080 for Cloud Run compatibility

5. Authentication/credentials:
   [Describe what authentication is needed, e.g., API keys, database credentials]

6. Create comprehensive CLAUDE.md documentation explaining:
   - Project overview and architecture
   - How to add new tools
   - Development workflow
   - Testing patterns
   - Deployment instructions

7. Include:
   - Dockerfile (Python 3.12-slim, port 8080)
   - requirements.txt
   - .env.example
   - .gitignore (Python, secrets, venv)
   - README.md
   - Basic pytest tests

Please implement this server with production-ready code quality.
```

**Example Usage:**
```
Your work folder is ~/projects/redis-cache-mcp

Create a new MCP server for Redis cache management.

Reference Implementation:
- Clone or read: https://github.com/tonton2006/ssh-executor-mcp
- Follow its structure, patterns, and conventions

Requirements:
1. Server name: redis-cache-mcp
2. Purpose: Manage Redis cache operations (get, set, delete, keys, TTL)
3. Tools needed:
   - redis_get: Get value by key
   - redis_set: Set key-value with optional TTL
   - redis_delete: Delete key(s)
   - redis_keys: List keys matching pattern
   - redis_ttl: Get/set time-to-live
   - redis_info: Get Redis server info

4. Follow these patterns from ssh-executor:
   - FastMCP 2.3.0+ with @mcp.tool() decorators
   - Pydantic Settings for configuration
   - Structure: mcp_server/main.py, config.py, tools/, utils/
   - Error handling: return dicts with "status" field
   - Async tool functions
   - Comprehensive docstrings
   - Port 8080 for Cloud Run compatibility

5. Authentication/credentials:
   - Redis connection string (REDIS_URL environment variable)
   - Optional username/password

6. Create comprehensive CLAUDE.md documentation explaining:
   - Project overview and architecture
   - How to add new tools
   - Development workflow
   - Testing patterns
   - Deployment instructions

7. Include:
   - Dockerfile (Python 3.12-slim, port 8080)
   - requirements.txt (fastmcp, redis)
   - .env.example
   - .gitignore (Python, secrets, venv)
   - README.md
   - Basic pytest tests with mocked Redis

Please implement this server with production-ready code quality.
```

---

## Prompt 2: Add New Tool to Existing Server

Use this to add a new tool to an MCP server you've already created.

```
Your work folder is ~/projects/[SERVER-NAME]

Add a new tool to the existing MCP server.

Tool Requirements:
- Name: [tool_name]
- Purpose: [what it does]
- Parameters:
  - [param1]: [description]
  - [param2]: [description]
- Return value: [description]

Implementation Steps:
1. Create mcp_server/tools/[module_name].py with async implementation
2. Import and register in mcp_server/main.py with @mcp.tool() decorator
3. Add comprehensive docstring (shown to LLM clients)
4. Follow error handling pattern: return dict with "status" field
5. Add configuration to config.py if needed
6. Create tests in tests/test_[module_name].py
7. Update CLAUDE.md with tool documentation

Follow the existing patterns in this server for consistency.
```

**Example Usage:**
```
Your work folder is ~/projects/redis-cache-mcp

Add a new tool to the existing MCP server.

Tool Requirements:
- Name: redis_flush
- Purpose: Flush all keys from database (with confirmation)
- Parameters:
  - database: int (0-15, which Redis database to flush)
  - confirm: bool (must be True to execute, safety check)
- Return value: dict with status and number of keys flushed

Implementation Steps:
1. Create mcp_server/tools/flush.py with async implementation
2. Import and register in mcp_server/main.py with @mcp.tool() decorator
3. Add comprehensive docstring (shown to LLM clients)
4. Follow error handling pattern: return dict with "status" field
5. Add configuration to config.py if needed
6. Create tests in tests/test_flush.py
7. Update CLAUDE.md with tool documentation

Follow the existing patterns in this server for consistency.
```

---

## Prompt 3: Create Server Based on Specific Example

Use this when you want to create a similar server to an existing one.

```
Your work folder is ~/projects/[NEW-SERVER-NAME]

Create a new MCP server similar to [EXAMPLE-SERVER-URL] but for [NEW-PURPOSE].

Base your implementation on:
[EXAMPLE-SERVER-URL]

Key Differences:
1. [Difference 1]
2. [Difference 2]
3. [Difference 3]

Tools to implement:
- [tool_1]: [description]
- [tool_2]: [description]

Maintain the same:
- Architecture and structure
- Error handling patterns
- Configuration approach
- Testing patterns
- Documentation style (CLAUDE.md)

Authentication:
[How authentication differs from the example]

Please create a production-ready server following all patterns from the example.
```

**Example Usage:**
```
Your work folder is ~/projects/postgres-db-mcp

Create a new MCP server similar to https://github.com/tonton2006/ssh-executor-mcp but for PostgreSQL database management.

Base your implementation on:
https://github.com/tonton2006/ssh-executor-mcp

Key Differences:
1. Replace SSH/Paramiko with PostgreSQL/psycopg2
2. Replace command execution tools with SQL query tools
3. Replace file transfer tools with table/data management tools

Tools to implement:
- postgres_query: Execute SQL query and return results
- postgres_list_tables: List tables in database
- postgres_describe_table: Get table schema
- postgres_insert: Insert rows into table
- postgres_update: Update rows in table
- postgres_delete: Delete rows from table

Maintain the same:
- Architecture and structure (main.py, config.py, tools/, utils/)
- Error handling patterns (status dict)
- Configuration approach (Pydantic Settings)
- Testing patterns (pytest with mocks)
- Documentation style (CLAUDE.md)

Authentication:
- Database connection string (POSTGRES_URL)
- Optional SSL configuration

Please create a production-ready server following all patterns from the example.
```

---

## Prompt 4: Improve Existing Server

Use this to enhance or refactor an existing server.

```
Your work folder is ~/projects/[SERVER-NAME]

Improve the existing MCP server with the following enhancements:

1. [Enhancement 1]
2. [Enhancement 2]
3. [Enhancement 3]

Maintain:
- All existing functionality
- All existing tests (update if needed)
- Backward compatibility
- Documentation (update CLAUDE.md and README)

Please ensure all tests pass after changes.
```

**Example Usage:**
```
Your work folder is ~/projects/redis-cache-mcp

Improve the existing MCP server with the following enhancements:

1. Add connection pooling for better performance
2. Add retry logic with exponential backoff for transient failures
3. Add metrics/logging for all operations
4. Improve error messages with more context
5. Add support for Redis Cluster (multiple nodes)

Maintain:
- All existing functionality
- All existing tests (update if needed)
- Backward compatibility
- Documentation (update CLAUDE.md and README)

Please ensure all tests pass after changes.
```

---

## Prompt 5: Deploy Existing Server to Cloud Run

Use this to deploy your local server to Google Cloud Run.

```
Your work folder is ~/projects/[SERVER-NAME]

Deploy this MCP server to Google Cloud Run.

Requirements:
- GCP Project: [YOUR-GCP-PROJECT-ID]
- Region: [REGION, e.g., us-central1]
- Service Name: [SERVICE-NAME]
- Environment variables to set:
  - [VAR1]=value1
  - [VAR2]=value2

Steps:
1. Verify Dockerfile exists and is correct
2. Run gcloud run deploy with:
   - --source . (build from source)
   - --region [REGION]
   - --allow-unauthenticated (or configure auth)
   - --project [PROJECT-ID]
   - --set-env-vars for configuration
3. Test the deployed service
4. Update CLAUDE.md with deployment information
5. Provide the service URL

Please execute the deployment and confirm it's working.
```

---

## Prompt 6: Add Comprehensive Tests

Use this to improve test coverage for an existing server.

```
Your work folder is ~/projects/[SERVER-NAME]

Add comprehensive pytest tests for this MCP server.

Requirements:
1. Test all tools with success and error cases
2. Mock external dependencies (databases, APIs, SSH, etc.)
3. Test configuration loading
4. Test error handling paths
5. Achieve >80% code coverage
6. Follow patterns from: https://github.com/tonton2006/ssh-executor-mcp/tree/master/tests

Create:
- tests/test_tools.py (test all tool functions)
- tests/test_config.py (test configuration)
- tests/conftest.py (pytest fixtures)
- Update requirements.txt with test dependencies

Run the tests and ensure they all pass.
```

---

## Prompt 7: Generate Documentation

Use this to create or improve documentation for a server.

```
Your work folder is ~/projects/[SERVER-NAME]

Create comprehensive documentation for this MCP server.

Generate:
1. README.md with:
   - Project overview
   - Quick start guide
   - Installation instructions
   - Usage examples for each tool
   - Configuration reference
   - Deployment guide

2. CLAUDE.md with:
   - Architecture overview
   - Component descriptions
   - Data flow patterns
   - How to add new tools
   - Testing guidelines
   - Common commands
   - Integration patterns

Follow the documentation style from:
https://github.com/tonton2006/ssh-executor-mcp

Make it comprehensive enough that Claude Code can understand the project by reading these files.
```

---

## Tips for Effective Prompts

### 1. Always Specify the Working Directory
```
Your work folder is ~/projects/[SERVER-NAME]
```
This is CRITICAL - Claude Code needs to know where to work.

### 2. Reference Example Implementations
```
Reference: https://github.com/tonton2006/ssh-executor-mcp
Follow its patterns and structure
```
This ensures consistency across your team's servers.

### 3. Be Specific About Requirements
Don't say "create a database server" - instead:
```
Tools needed:
- postgres_query: Execute SQL SELECT/INSERT/UPDATE/DELETE
- postgres_list_tables: Return list of table names
- postgres_describe_table: Return table schema with columns and types
```

### 4. Request Comprehensive Documentation
```
Create comprehensive CLAUDE.md documentation so Claude Code can understand this server in the future.
```
This enables future modifications by Claude Code.

### 5. Ask for Tests
```
Include pytest tests with mocked dependencies. Test both success and error cases.
```
Quality is maintained through testing.

---

## Troubleshooting

### Claude Desktop doesn't see the claude_code tool
- Restart Claude Desktop after adding to config
- Verify config file syntax (valid JSON)

### Claude Code can't access files
- Ensure working directory path is correct
- Use absolute paths when referencing other repos

### Server doesn't follow patterns
- Be more explicit in your prompt
- Reference specific files from example repos
- Ask Claude Code to "read [specific file] from [repo URL]"

---

## Advanced: Combining Multiple Servers

```
Your work folder is ~/projects/orchestrator-mcp

Create a new MCP server that orchestrates multiple other MCP servers.

This server should:
1. Integrate with ssh-executor-mcp for remote command execution
2. Integrate with gcp-orchestrator-mcp for VM management
3. Provide high-level workflows:
   - create_and_configure_vm: Create VM + install packages + deploy app
   - backup_server: SSH in + run backup commands + store in Cloud Storage

Reference both:
- https://github.com/tonton2006/ssh-executor-mcp
- https://github.com/tonton2006/ai-cloud-orchestrator

Use Python's httpx or requests to call other MCP servers' endpoints.
```

---

## Questions?

If these prompts don't work as expected:
1. Check that Claude Desktop has the `claude-code-mcp` tool configured
2. Verify your working directory path is correct
3. Make sure example repos are accessible
4. Try being more specific in your requirements

Happy building!
