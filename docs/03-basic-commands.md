# Basic Commands

[← Back: Quick Start](02-quick-start.md) | [Main](../README.md) | [Next: Understanding Claude Code →](04-understanding-claude-code.md)

---

## Claude Code Commands (Within Desktop App)

These commands are available when using Claude Code within the Claude desktop application.

### Session Management
- `/help` - Show available commands
- `/clear` - Clear conversation context
- `/exit` or `Ctrl+D` - Exit Claude Code
- `/context` - Show current context usage
- `/model` - Show or switch AI model

### File Operations
- `Read <file>` - Read file contents
- `Write <file>` - Create or overwrite file
- `Edit <file>` - Modify existing file
- `MultiEdit <file>` - Make multiple edits
- `Glob <pattern>` - Find files by pattern
- `Grep <pattern>` - Search file contents

### Development Tools
- `Bash <command>` - Execute shell commands
- `TodoWrite` - Manage task list
- `TodoRead` - View current tasks
- `Memory.store()` - Save persistent data
- `Memory.get()` - Retrieve saved data

## Claude-Flow Commands (Separate Tool)

These commands require claude-flow installation and are run in your terminal:

### MCP Server Management
- `./claude-flow mcp start` - Start MCP server
- `./claude-flow mcp list` - List available MCP tools
- `./claude-flow mcp status` - Check MCP server status
- `./claude-flow mcp tools` - List available MCP tools

### Other Claude-Flow Commands
- `./claude-flow swarm` - Multi-agent orchestration
- `./claude-flow sparc` - SPARC development modes
- `./claude-flow memory` - Memory operations
- `./claude-flow monitor` - System monitoring

---

[← Back: Quick Start](02-quick-start.md) | [Main](../README.md) | [Next: Understanding Claude Code →](04-understanding-claude-code.md)