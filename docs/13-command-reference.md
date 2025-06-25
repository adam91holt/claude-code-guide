# Command Reference

[← Back: Best Practices](12-best-practices.md) | [Main](../README.md) | [Next: Troubleshooting →](14-troubleshooting.md)

---

## Claude Code Commands

### Session Management

> **Note**: Many commands are theoretical. Claude Code's actual command set may be more limited.

| Command | Description | Status |
|---------|-------------|--------|
| `/help` | Show available commands | Confirmed |
| `/clear` | Clear conversation context | Likely exists |
| `/exit` | Exit Claude Code | Likely exists (or Ctrl+D) |
| `/context` | Show current context usage | Unconfirmed |
| `/model` | Show or switch model | Unconfirmed |

### File Operations

| Tool | Description | Example |
|------|-------------|---------|
| `Read` | Read file contents | `Read('/src/index.js')` |
| `Write` | Create or overwrite file | `Write('/src/new.js', content)` |
| `Edit` | Modify existing file | `Edit('/src/app.js', 'old', 'new')` |
| `MultiEdit` | Multiple edits in one file | `MultiEdit('/src/app.js', edits[])` |
| `Glob` | Find files by pattern | `Glob('**/*.test.js')` |
| `Grep` | Search file contents | `Grep('TODO', '**/*.js')` |

### Development Tools

| Tool | Description | Example |
|------|-------------|---------|
| `Bash` | Execute shell commands | `Bash('npm test')` |
| `TodoWrite` | Update task list | `TodoWrite(tasks[])` |
| `TodoRead` | View current tasks | `TodoRead()` |
| `WebSearch` | Search the web | `WebSearch('React best practices')` |
| `WebFetch` | Fetch and analyze URL | `WebFetch(url, prompt)` |

### Memory Operations (Claude-Flow Specific)

> **⚠️ Note**: Memory operations require claude-flow installation. These are not JavaScript APIs.

| Operation | Description | Claude-Flow Command |
|-----------|-------------|---------------------|
| Store data | Save data | `./claude-flow memory store <key> <data>` |
| Retrieve data | Get stored data | `./claude-flow memory get <key>` |
| List entries | Show all keys | `./claude-flow memory list` |
| Export data | Export to file | `./claude-flow memory export <file>` |
| Import data | Import from file | `./claude-flow memory import <file>` |
| Cleanup | Remove old entries | `./claude-flow memory cleanup` |

## Claude-Flow Commands

### System Commands

```bash
# Start orchestration system
./claude-flow start [options]
  --ui              # Enable web interface
  --port <number>   # Port number (default: 3000)
  --host <string>   # Host address (default: localhost)

# Check system status
./claude-flow status

# Real-time monitoring
./claude-flow monitor
  --session <id>    # Monitor specific session
  --filter <type>   # Filter by type (error, warning, info)

# Configuration management
./claude-flow config <subcommand>
  show              # Show current config
  get <key>         # Get specific value
  set <key> <val>   # Set configuration
  init              # Initialize config
  validate          # Validate config
```

### Agent Management

```bash
# Spawn agents
./claude-flow agent spawn <type> [options]
  --name <name>     # Agent name
  --model <model>   # Model to use
  --priority <level> # Priority level

# List agents
./claude-flow agent list
  --active          # Show only active
  --session <id>    # Filter by session

# Quick spawn (alias)
./claude-flow spawn <type>
```

### Task Management

```bash
# Create tasks
./claude-flow task create <type> [description]
  --priority <level> # high, medium, low
  --assign <agent>   # Assign to agent
  --depends <id>     # Task dependencies

# List tasks
./claude-flow task list
  --status <status>  # pending, active, completed
  --agent <name>     # Filter by agent

# Execute workflow
./claude-flow workflow <file>
  --dry-run         # Preview execution
  --parallel        # Run in parallel
```

### Memory Management

```bash
# Store data
./claude-flow memory store <key> <data>
  --ttl <seconds>   # Time to live
  --namespace <ns>  # Namespace

# Retrieve data
./claude-flow memory get <key>
  --namespace <ns>  # Namespace

# List entries
./claude-flow memory list
  --pattern <glob>  # Filter pattern
  --namespace <ns>  # Namespace

# Export/Import
./claude-flow memory export <file>
  --format <type>   # json, csv
./claude-flow memory import <file>

# Statistics
./claude-flow memory stats
  --detailed        # Detailed breakdown

# Cleanup
./claude-flow memory cleanup
  --older-than <days> # Age threshold
  --pattern <glob>    # Pattern to clean
```

### SPARC Modes

```bash
# List all modes
./claude-flow sparc modes

# Run specific mode
./claude-flow sparc run <mode> "<task>"
  --timeout <ms>    # Execution timeout
  --model <model>   # Override model

# Available modes:
- orchestrator      # Default coordinator
- coder            # Code implementation
- researcher       # Research and analysis
- tdd              # Test-driven development
- architect        # System design
- reviewer         # Code review
- debugger         # Bug investigation
- tester           # Test creation
- analyzer         # Performance analysis
- optimizer        # Code optimization
- documenter       # Documentation
- designer         # UI/UX design
- innovator        # Creative solutions
- swarm-coordinator # Multi-agent management
- memory-manager   # Knowledge persistence
- batch-executor   # Bulk operations
- workflow-manager # Process automation

# TDD mode
./claude-flow sparc tdd "<feature>"
  --coverage <percent> # Min coverage
  --framework <name>   # Test framework
```

### Swarm Orchestration

```bash
# Launch swarm
./claude-flow swarm "<objective>" [options]

# Strategies
--strategy <type>
  research          # Information gathering
  development       # Building features
  analysis          # Code analysis
  testing           # Quality assurance
  optimization      # Performance tuning
  maintenance       # Updates and fixes

# Coordination modes
--mode <type>
  centralized       # Single coordinator
  distributed       # Peer-to-peer
  hierarchical      # Layered management
  mesh              # Fully connected
  hybrid            # Adaptive mode

# Other options
--max-agents <n>    # Max agents (1-10)
--parallel          # Parallel execution
--monitor           # Real-time monitoring
--output <format>   # json, sqlite, csv, html
--config <file>     # Custom config file

# Control commands
./claude-flow swarm pause --session <id>
./claude-flow swarm resume --session <id>
./claude-flow swarm scale --session <id> --add <n>
./claude-flow swarm stop --session <id>
```

### MCP Server Management

```bash
# Start MCP server
./claude-flow mcp start [options]
  --port <number>   # Port number
  --host <string>   # Host address

# Check status
./claude-flow mcp status
  --detailed        # Detailed info

# List tools
./claude-flow mcp tools
  --server <name>   # Specific server

# Add server
./claude-flow mcp add <name> [options]
  -s, --scope <scope>  # project, user, global
  -e, --env <KEY=val>  # Environment variables
  -- <command>         # Server command

# Remove server
./claude-flow mcp remove <name>
  --scope <scope>   # Scope to remove from

# List servers
./claude-flow mcp list
  --remote          # Include remote servers
  --scope <scope>   # Filter by scope

# Debug mode
claude --mcp-debug
```

### Claude Integration (Claude-Flow Specific)

> **Note**: These commands are claude-flow specific, not native Claude Code.

```bash
# Authentication (claude-flow only)
./claude-flow claude auth

# List models (claude-flow only)
./claude-flow claude models

# Interactive chat (claude-flow only) 
./claude-flow claude chat
```

### Session Management

```bash
# Manage sessions
./claude-flow session
  list              # List all sessions
  info <id>         # Session details
  resume <id>       # Resume session
  export <id>       # Export session
  cleanup           # Clean old sessions

# REPL mode
./claude-flow repl
  --model <model>   # Model to use
  --history         # Load history
```

### Project Commands

```bash
# Initialize project
./claude-flow init
  --sparc           # Include SPARC
  --template <name> # Use template
  --force           # Overwrite existing

# Project management
./claude-flow project <subcommand>
  create <name>     # Create project
  switch <name>     # Switch project
  list              # List projects
  info              # Project info
  archive <name>    # Archive project
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `ANTHROPIC_API_KEY` | API key for Claude | Required |
| `CLAUDE_MODEL` | Default model | `sonnet-4` |
| `CLAUDE_MAX_TOKENS` | Max tokens per request | `100000` |
| `CLAUDE_TEMPERATURE` | Response temperature | `0.7` |
| `CLAUDE_ENV` | Environment | `development` |
| `CLAUDE_LOG_LEVEL` | Log verbosity | `info` |
| `CLAUDE_CACHE_ENABLED` | Enable caching | `false` |
| `CLAUDE_CACHE_TTL` | Cache TTL (seconds) | `3600` |
| `CLAUDE_TIMEOUT` | Request timeout (ms) | `300000` |
| `CLAUDE_RETRY_ATTEMPTS` | Retry attempts | `3` |
| `CLAUDE_PARALLEL_REQUESTS` | Max parallel requests | `5` |
| `MCP_DEBUG` | MCP debug mode | `false` |
| `MCP_TIMEOUT` | MCP timeout (ms) | `30000` |

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+C` | Cancel current operation |
| `Ctrl+D` | Exit Claude Code |
| `Ctrl+L` | Clear screen |
| `Ctrl+R` | Search command history |
| `Ctrl+A` | Move to beginning of line |
| `Ctrl+E` | Move to end of line |
| `Ctrl+K` | Clear line after cursor |
| `Ctrl+U` | Clear line before cursor |
| `Tab` | Auto-complete |
| `↑/↓` | Navigate history |

## Special Syntax

### Think Modes (Unverified)
> **⚠️ Warning**: These trigger phrases are not officially documented and may not work.

```
think about <problem>        # Unverified
think hard about <problem>   # Unverified  
think harder about <problem> # Unverified
ultrathink about <problem>   # Unverified
```

### Model Switching
```
/model opus-4    # Switch to Opus
/model sonnet-4  # Switch to Sonnet
/model           # Show current model
```

### Batch Operations
```javascript
// Read multiple files
Read(['file1.js', 'file2.js', 'file3.js'])

// Multi-edit
MultiEdit('file.js', [
  { old_string: 'foo', new_string: 'bar' },
  { old_string: 'baz', new_string: 'qux' }
])
```

### Memory Patterns
```javascript
// Hierarchical keys
Memory.store('project/module/component', data)

// Wildcards
Memory.query('project/*/config')

// Namespaces
Memory.store('key', data, { namespace: 'session-123' })
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Invalid arguments |
| 3 | Authentication failed |
| 4 | Rate limit exceeded |
| 5 | Token limit exceeded |
| 6 | MCP connection failed |
| 7 | File operation failed |
| 8 | Memory operation failed |
| 9 | Network error |
| 10 | Timeout |

## Next Steps

- Check [Troubleshooting](14-troubleshooting.md) for common issues
- Explore [Examples & Recipes](15-examples-recipes.md) for patterns
- Return to [Main Documentation](../README.md)

---

[← Back: Best Practices](12-best-practices.md) | [Main](../README.md) | [Next: Troubleshooting →](14-troubleshooting.md)