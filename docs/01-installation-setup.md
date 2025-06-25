# Installation & Setup

[← Back to Main](../README.md) | [Next: Quick Start Guide →](02-quick-start.md)

---

## System Requirements

- **Operating System**: macOS, Linux, Windows (WSL2 recommended)
- **Node.js**: Version 18.0 or higher
- **Memory**: Minimum 8GB RAM (16GB recommended)
- **Storage**: At least 2GB free space

## Installation Methods

### Method 1: NPM Global Installation (Recommended)

```bash
# Install Claude Code CLI globally
npm install -g @anthropic/claude-cli

# Verify installation
claude --version

# Expected output: claude-cli version X.X.X
```

### Method 2: Claude-Flow Enhanced Installation

For the complete development environment with multi-agent orchestration:

```bash
# Clone Claude-Flow repository
git clone https://github.com/ruvnet/claude-code-flow
cd claude-code-flow

# Install dependencies
npm install

# Initialize SPARC development environment
./claude-flow init --sparc

# Verify installation
./claude-flow --version
```

### Method 3: Docker Installation

```bash
# Pull official Claude Code image
docker pull anthropic/claude-code:latest

# Run with volume mounting
docker run -it -v $(pwd):/workspace anthropic/claude-code
```

## Authentication

### Claude Subscription Authentication

```bash
# Login with Claude Pro/Max subscription
claude auth login --subscription

# You'll be prompted to:
# 1. Open browser to claude.ai
# 2. Copy authentication token
# 3. Paste token in terminal
```

### API Key Authentication

```bash
# Set API key as environment variable
export ANTHROPIC_API_KEY="sk-ant-..."

# Or use auth command
claude auth login --api-key sk-ant-...

# Verify authentication
claude auth status
```

### Console Account Authentication

```bash
# For Anthropic Console users
claude auth login --console

# Follow browser authentication flow
```

## Initial Configuration

### 1. Create Project Configuration

Create a `CLAUDE.md` file in your project root:

```markdown
# Claude Code Configuration

## Build Commands
- `npm run build`: Build the project
- `npm run test`: Run test suite
- `npm run lint`: Run ESLint
- `npm run typecheck`: TypeScript checking

## Code Style
- Use ES modules (import/export)
- Prefer async/await over promises
- Follow existing naming conventions
- Add JSDoc for public APIs

## Testing
- Jest for unit tests
- Playwright for E2E tests
- Minimum 80% coverage required

## Project Structure
- `/src`: Source code
- `/tests`: Test files
- `/docs`: Documentation
- `/scripts`: Build scripts
```

### 2. Configure Permissions

```bash
# Open permissions configuration
claude permissions

# Or create .claude/permissions.json manually
```

Example permissions file:

```json
{
  "rules": [
    {
      "tool": "Bash",
      "pattern": "npm run *",
      "allow": true
    },
    {
      "tool": "Bash",
      "pattern": "rm -rf *",
      "allow": false
    },
    {
      "tool": "WebFetch",
      "domain": "docs.anthropic.com",
      "allow": true
    },
    {
      "tool": "Write",
      "pattern": "/etc/*",
      "allow": false
    }
  ],
  "defaultPolicy": "ask"
}
```

### 3. Configure MCP Servers

Create `.mcp.json` in your project:

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "mcp-server-filesystem",
      "args": ["--root", "./"]
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "database": {
      "type": "stdio",
      "command": "mcp-server-postgres",
      "args": ["--connection-string", "${DATABASE_URL}"]
    }
  }
}
```

## Environment Variables

Create a `.env` file for local development:

```bash
# Claude Configuration
ANTHROPIC_API_KEY=sk-ant-...
CLAUDE_MODEL=opus-4  # or sonnet-4
CLAUDE_MAX_TOKENS=100000
CLAUDE_TEMPERATURE=0.7

# MCP Configuration
MCP_DEBUG=false
MCP_TIMEOUT=30000

# GitHub Integration
GITHUB_TOKEN=ghp_...

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb

# Feature Flags
CLAUDE_CACHE_ENABLED=true
CLAUDE_PARALLEL_REQUESTS=5
CLAUDE_RETRY_ATTEMPTS=3
```

## VS Code Integration

### Install Extension

```bash
# Install from VS Code marketplace
code --install-extension anthropic.claude-code
```

### Configure Extension

In VS Code settings (`settings.json`):

```json
{
  "claude-code.apiKey": "${env:ANTHROPIC_API_KEY}",
  "claude-code.model": "opus-4",
  "claude-code.autoComplete": true,
  "claude-code.showThinkingProcess": true,
  "claude-code.mcpServers": {
    "workspace": {
      "command": "mcp-server-filesystem",
      "args": ["--root", "${workspaceFolder}"]
    }
  }
}
```

## Verify Installation

Run the verification script:

```bash
# Check all components
./claude-flow doctor

# Expected output:
# ✓ Claude CLI installed
# ✓ Authentication valid
# ✓ MCP servers configured
# ✓ Permissions set
# ✓ Memory system initialized
# ✓ All systems operational
```

## Troubleshooting

### Common Issues

1. **Permission Denied**
   ```bash
   chmod +x ./claude-flow
   ```

2. **Node Version Error**
   ```bash
   # Install Node 18+ using nvm
   nvm install 18
   nvm use 18
   ```

3. **Authentication Failed**
   ```bash
   # Clear credentials and re-authenticate
   claude auth logout
   claude auth login --subscription
   ```

4. **MCP Connection Issues**
   ```bash
   # Debug MCP connections
   claude --mcp-debug
   ```

## Next Steps

✅ Installation complete! Continue to:
- [Quick Start Guide](02-quick-start.md) - Your first Claude Code session
- [Basic Commands](03-basic-commands.md) - Essential commands reference
- [MCP Protocol](05-mcp-protocol.md) - Set up MCP servers

---

[← Back to Main](../README.md) | [Next: Quick Start Guide →](02-quick-start.md)