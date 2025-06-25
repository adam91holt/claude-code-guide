# MCP (Model Context Protocol) Deep Dive

[← Back: Understanding Claude Code](04-understanding-claude-code.md) | [Main](../README.md) | [Next: Extended Thinking →](06-extended-thinking.md)

---

## What is MCP?

Model Context Protocol (MCP) is like a universal USB-C port for AI applications. It provides a standardized way to connect Claude Code to virtually any data source, API, or tool you can imagine.

<p align="center">
  <img src="../assets/images/tool_permissions.png" alt="MCP Configuration" width="600">
</p>

## MCP Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Claude Code   │────▶│   MCP Client    │────▶│   MCP Server    │
│   (AI Agent)    │◀────│   (Protocol)    │◀────│   (Your Tool)   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         │                                                │
         └──────────── Bidirectional Communication ───────┘
```

## Configuration Methods

### Method 1: Direct Config File (Recommended)

Edit `.mcp.json` directly in your project root:

```json
{
  "mcpServers": {
    "database": {
      "type": "stdio",
      "command": "mcp-server-postgres",
      "args": ["--connection-string", "postgresql://localhost/mydb"],
      "env": {
        "NODE_ENV": "production",
        "LOG_LEVEL": "info"
      }
    },
    "filesystem": {
      "type": "stdio",
      "command": "mcp-server-filesystem",
      "args": ["--root", "/home/user/projects", "--readonly", "false"]
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "sequentialthinking": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "mcp-sequentialthinking-tools"]
    }
  }
}
```

### Method 2: CLI Configuration

```bash
# Add project-scoped server
claude mcp add my-server -s project -- npx "@myorg/mcp-server"

# Add user-scoped server (available in all projects)
claude mcp add my-server -s user -- npx "@myorg/mcp-server"

# Add with environment variables
claude mcp add api-server \
  -e API_KEY=your-key \
  -e API_URL=https://api.example.com \
  -- npx "@example/mcp-api-server"

# List configured servers
claude mcp list

# Remove a server
claude mcp remove my-server
```

## Popular MCP Servers

### 1. Filesystem Server

Access and manipulate files:

```json
{
  "filesystem": {
    "type": "stdio",
    "command": "mcp-server-filesystem",
    "args": ["--root", "./", "--watch", "true"]
  }
}
```

Usage:
```bash
claude> List all TypeScript files in the project
claude> Watch for changes in the src directory
claude> Create a new component file in src/components
```

### 2. Database Servers

#### PostgreSQL
```json
{
  "postgres": {
    "type": "stdio",
    "command": "mcp-server-postgres",
    "args": ["--connection-string", "${DATABASE_URL}"]
  }
}
```

#### MongoDB
```json
{
  "mongodb": {
    "type": "stdio",
    "command": "mcp-server-mongodb",
    "args": ["--uri", "${MONGODB_URI}", "--database", "myapp"]
  }
}
```

Usage:
```bash
claude> Show me the user schema
claude> Find all orders from the last week
claude> Update user preferences for user ID 123
```

### 3. API Integration Servers

#### REST API Server
```json
{
  "api": {
    "type": "stdio",
    "command": "mcp-server-rest",
    "args": ["--base-url", "https://api.example.com"],
    "env": {
      "API_TOKEN": "${API_TOKEN}"
    }
  }
}
```

#### GraphQL Server
```json
{
  "graphql": {
    "type": "stdio",
    "command": "mcp-server-graphql",
    "args": ["--endpoint", "https://api.example.com/graphql"],
    "env": {
      "AUTH_HEADER": "Bearer ${GRAPHQL_TOKEN}"
    }
  }
}
```

### 4. Development Tool Servers

#### Git Server
```json
{
  "git": {
    "type": "stdio",
    "command": "mcp-server-git",
    "args": ["--repo", "./"]
  }
}
```

#### Docker Server
```json
{
  "docker": {
    "type": "stdio",
    "command": "mcp-server-docker",
    "args": ["--socket", "/var/run/docker.sock"]
  }
}
```

## Building Custom MCP Servers

### Python Example

```python
# mcp_custom_server.py
from fastmcp import FastMCP
import asyncio
import json

# Create MCP server instance
mcp = FastMCP("custom-analytics")

@mcp.tool()
async def analyze_metrics(
    metric_type: str,
    start_date: str,
    end_date: str
) -> dict:
    """Analyze business metrics for a date range"""
    # Your implementation here
    results = {
        "metric": metric_type,
        "period": f"{start_date} to {end_date}",
        "value": 42.5,
        "trend": "increasing"
    }
    return results

@mcp.tool()
async def generate_report(
    report_type: str,
    format: str = "json"
) -> str:
    """Generate various types of reports"""
    # Implementation
    if format == "json":
        return json.dumps({"report": report_type, "data": []})
    elif format == "csv":
        return "header1,header2\nvalue1,value2"

@mcp.resource("config://analytics")
async def get_analytics_config():
    """Provide analytics configuration"""
    return {
        "metrics": ["revenue", "users", "engagement"],
        "refresh_interval": 3600,
        "data_retention_days": 90
    }

# Run the server
if __name__ == "__main__":
    import sys
    asyncio.run(mcp.run(transport="stdio"))
```

### Node.js Example

```javascript
// mcp-custom-server.js
import { Server } from '@modelcontextprotocol/sdk';
import { StdioTransport } from '@modelcontextprotocol/sdk/transport';

const server = new Server({
  name: 'custom-analytics',
  version: '1.0.0',
  description: 'Custom analytics MCP server'
});

// Register tools
server.addTool({
  name: 'analyze_user_behavior',
  description: 'Analyze user behavior patterns',
  parameters: {
    type: 'object',
    properties: {
      userId: { type: 'string' },
      timeRange: { type: 'string', enum: ['day', 'week', 'month'] }
    },
    required: ['userId', 'timeRange']
  },
  handler: async ({ userId, timeRange }) => {
    // Your analysis logic here
    return {
      userId,
      timeRange,
      sessions: 42,
      avgDuration: '5m 32s',
      topActions: ['view_product', 'add_to_cart']
    };
  }
});

// Register resources
server.addResource({
  uri: 'analytics://dashboard',
  name: 'Analytics Dashboard',
  mimeType: 'application/json',
  handler: async () => {
    return {
      totalUsers: 10000,
      activeUsers: 2500,
      revenue: '$50,000'
    };
  }
});

// Start server
const transport = new StdioTransport();
server.connect(transport);
```

## Remote MCP Support

Claude Code now supports remote MCP servers with OAuth:

```bash
# Connect to remote MCP server
claude mcp add remote-analytics \
  --remote https://mcp.analytics.com \
  --auth oauth

# List remote servers
claude mcp list --remote

# Configure OAuth callback
export CLAUDE_OAUTH_CALLBACK=http://localhost:3000/oauth/callback
```

### Remote Server Configuration

```json
{
  "mcpServers": {
    "remote-api": {
      "type": "remote",
      "url": "https://api.company.com/mcp",
      "auth": {
        "type": "oauth",
        "clientId": "${OAUTH_CLIENT_ID}",
        "scopes": ["read:data", "write:data"]
      }
    }
  }
}
```

## Enterprise Integration Patterns

### 1. Multi-Database Setup

```json
{
  "mcpServers": {
    "primary-db": {
      "type": "stdio",
      "command": "mcp-server-postgres",
      "args": ["--connection-string", "${PRIMARY_DB_URL}"]
    },
    "analytics-db": {
      "type": "stdio",
      "command": "mcp-server-postgres",
      "args": ["--connection-string", "${ANALYTICS_DB_URL}", "--readonly"]
    },
    "cache": {
      "type": "stdio",
      "command": "mcp-server-redis",
      "args": ["--url", "${REDIS_URL}"]
    }
  }
}
```

### 2. Microservices Integration

```json
{
  "mcpServers": {
    "user-service": {
      "type": "remote",
      "url": "https://users.internal.company.com/mcp"
    },
    "order-service": {
      "type": "remote",
      "url": "https://orders.internal.company.com/mcp"
    },
    "inventory-service": {
      "type": "remote",
      "url": "https://inventory.internal.company.com/mcp"
    }
  }
}
```

## Debugging MCP Connections

### Enable Debug Mode

```bash
# Run Claude with MCP debugging
claude --mcp-debug

# Set debug environment variable
export MCP_DEBUG=true
```

### Debug Output Example

```
[MCP] Connecting to filesystem server...
[MCP] Command: mcp-server-filesystem --root ./
[MCP] Server started with PID: 12345
[MCP] Handshake successful
[MCP] Available tools: ['read_file', 'write_file', 'list_directory']
[MCP] Ready for requests
```

### Common Issues and Solutions

1. **Server Won't Start**
   ```bash
   # Check if command exists
   which mcp-server-filesystem
   
   # Install missing server
   npm install -g @modelcontextprotocol/server-filesystem
   ```

2. **Authentication Errors**
   ```bash
   # Verify environment variables
   echo $DATABASE_URL
   
   # Test connection directly
   psql $DATABASE_URL -c "SELECT 1"
   ```

3. **Permission Denied**
   ```bash
   # Check file permissions
   ls -la .mcp.json
   
   # Fix permissions
   chmod 644 .mcp.json
   ```

## Best Practices

### 1. Security

- Never commit sensitive credentials to `.mcp.json`
- Use environment variables for secrets
- Implement proper authentication for remote servers
- Use read-only mode for production databases

### 2. Performance

- Limit concurrent MCP connections
- Implement caching in custom servers
- Use connection pooling for databases
- Set appropriate timeouts

### 3. Monitoring

```bash
# Monitor MCP server health
./claude-flow mcp status

# View server logs
./claude-flow mcp logs filesystem

# Check resource usage
./claude-flow mcp stats
```

## Next Steps

- Learn about [Extended Thinking](06-extended-thinking.md) for complex reasoning
- Explore [SPARC Development Modes](07-sparc-modes.md)
- Master [Multi-Agent Orchestration](08-multi-agent-orchestration.md)

---

[← Back: Understanding Claude Code](04-understanding-claude-code.md) | [Main](../README.md) | [Next: Extended Thinking →](06-extended-thinking.md)