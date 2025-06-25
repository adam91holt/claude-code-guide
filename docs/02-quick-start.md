# Quick Start Guide

[← Back: Installation](01-installation-setup.md) | [Main](../README.md) | [Next: Basic Commands →](03-basic-commands.md)

---

## Your First Claude Code Session

### 1. Basic Interactive Mode

```bash
# Start Claude Code through desktop app
# No command line required - use the desktop interface

# Or if using command line integration:
# (Actual command may vary based on desktop app implementation)

# Try your first request:
# "Create a simple Python function that calculates fibonacci numbers"

# Claude will generate the code and ask for confirmation before writing files
```

### 2. First Project Setup

Let's create a simple Node.js API:

```bash
# Create project directory
mkdir my-first-api && cd my-first-api

# Initialize with Claude-Flow (using NPX)
npx claude-flow@latest init --sparc

# Start interactive session with UI
./claude-flow start --ui --port 3000
```

### 3. Your First Swarm Command

```bash
# Create a complete REST API with 5 agents working in parallel
./claude-flow swarm "Build a TODO API with CRUD operations and SQLite database" \
  --strategy development \
  --mode hierarchical \
  --max-agents 5 \
  --parallel \
  --monitor
```

## Understanding the Output

### Agent Coordination

When you run a swarm command, you'll see:

```
🚀 SWARM EXECUTION: Build a TODO API with CRUD operations and SQLite database
📊 Strategy: development | Mode: hierarchical | Agents: 5

Agent 1 (Architect): Designing API structure...
Agent 2 (Database): Creating SQLite schema...
Agent 3 (Backend): Implementing CRUD endpoints...
Agent 4 (Tester): Writing test cases...
Agent 5 (Documenter): Generating API documentation...

✅ Task completed in 2m 34s
```

### File Generation

Claude Code will create:

```
my-first-api/
├── src/
│   ├── index.js          # Main application entry
│   ├── routes/           # API routes
│   ├── models/           # Data models
│   └── middleware/       # Express middleware
├── tests/
│   └── api.test.js       # Test suite
├── package.json          # Dependencies
├── README.md             # Documentation
└── CLAUDE.md            # Claude configuration
```

## Essential First Commands

### 1. Model Selection (Max Users)

```bash
# Check current model
/model

# Switch to Opus for complex tasks
/model opus-4

# Switch to Sonnet for routine tasks
/model sonnet-4
```

### 2. Using Think Modes

```bash
# Basic problem solving
claude> Think about the best database design for a social media app

# Deep analysis
claude> Think harder about optimizing this sorting algorithm for millions of records

# Maximum reasoning
claude> Ultrathink about architecting a distributed microservices system
```

### 3. Memory System

```bash
# Store important information
claude> Memory.store("project-config", { 
  database: "PostgreSQL", 
  framework: "Express", 
  testing: "Jest" 
})

# Retrieve later in any session
claude> Memory.get("project-config")

# List all stored items
claude> Memory.list()
```

## Common Workflows

### Workflow 1: TDD Development

```bash
# Start test-driven development
./claude-flow sparc tdd "User authentication with JWT"

# Claude will:
# 1. Write failing tests first
# 2. Implement minimal code to pass
# 3. Refactor for quality
# 4. Repeat until complete
```

### Workflow 2: Bug Investigation

```bash
# Debug with extended thinking
claude> The app crashes after 1000 requests. Think hard about potential memory leaks.

# Or use debugger mode
./claude-flow sparc run debugger "Investigate high CPU usage in production"
```

### Workflow 3: Code Review

```bash
# Review existing code
./claude-flow sparc run reviewer "Review src/ for security vulnerabilities"

# Review specific PR
claude> Review the changes in PR #123 focusing on performance and security
```

## Working with MCP Servers

### Quick MCP Setup

```bash
# Add a filesystem MCP server (claude-flow command)
./claude-flow mcp add fs -s project -- npx mcp-server-filesystem --root ./

# Verify it's working (claude-flow command)
./claude-flow mcp list

# Use it in Claude
# "Read all JavaScript files in the src directory using the fs MCP server"
```

### Database Integration

```bash
# Add PostgreSQL MCP server (claude-flow command)
./claude-flow mcp add db \
  -e DATABASE_URL=postgresql://localhost/myapp \
  -- npx mcp-server-postgres

# Now Claude can query your database
# "Show me all users created in the last 24 hours"
```

## Quick Tips

### 1. Use TodoWrite for Complex Tasks

```javascript
TodoWrite([
  {
    id: "setup",
    content: "Set up Express server",
    status: "pending",
    priority: "high"
  },
  {
    id: "routes",
    content: "Create API routes",
    status: "pending",
    priority: "high"
  }
])
```

### 2. Batch File Operations

```bash
# Read multiple files at once
claude> Read package.json, src/index.js, and README.md

# Edit multiple locations
claude> Update all test files to use the new API endpoint
```

### 3. Context Management

```bash
# Clear context when switching tasks
/clear

# Check current context usage
/context

# Save state before clearing
claude> Memory.store("current-progress", "Completed authentication module")
```

## Interactive Example

Try this complete example:

```bash
# 1. Start Claude Code
claude

# 2. Create a new feature
claude> I need to add user profile functionality to an existing Express app. 
        Think about the implementation and then proceed.

# 3. Claude will:
#    - Analyze existing code structure
#    - Plan the implementation
#    - Create necessary files
#    - Add routes and models
#    - Write tests
#    - Update documentation

# 4. Monitor progress
./claude-flow monitor

# 5. Run tests
npm test
```

## What's Next?

Now that you've completed the basics:

1. **Learn Commands**: See [Basic Commands](03-basic-commands.md) for full reference
2. **Explore MCP**: Dive into [MCP Protocol](05-mcp-protocol.md) for integrations
3. **Advanced Features**: Try [Extended Thinking](06-extended-thinking.md) modes
4. **Scale Up**: Learn [Multi-Agent Orchestration](08-multi-agent-orchestration.md)

## Getting Help

```bash
# Built-in help
/help

# Check documentation
claude> Show me examples of working with databases

# Debug mode
claude --debug

# Community support
# https://github.com/anthropics/claude-code/issues
```

---

[← Back: Installation](01-installation-setup.md) | [Main](../README.md) | [Next: Basic Commands →](03-basic-commands.md)