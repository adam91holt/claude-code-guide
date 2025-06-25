# Installation & Setup

[← Back to Main](../README.md) | [Next: Quick Start Guide →](02-quick-start.md)

---

## System Requirements

- **Operating System**: macOS, Linux, Windows (WSL2 recommended)
- **Node.js**: Version 18.0 or higher
- **Memory**: Minimum 8GB RAM (16GB recommended)
- **Storage**: At least 2GB free space

## Version Compatibility

| Claude Version | Desktop App | Claude-Flow | MCP Support | SPARC Modes |
|----------------|-------------|-------------|-------------|-------------|
| **Latest (2025)** | ✅ Built-in | ✅ v1.0+ | ✅ Full | ✅ v1.0+ |
| **Pro Plan** | ✅ Sonnet 4 only | ✅ Limited | ✅ Full | ✅ Limited |
| **Max Plan** | ✅ All models | ✅ Full | ✅ Full | ✅ Full |

**Note**: Features and availability may vary by subscription tier and region.

## Installation Methods

### Method 1: Claude Desktop App (Primary Method)

```bash
# Download Claude desktop app from:
# https://claude.ai/download

# Claude Code is built into the desktop app
# No separate CLI installation needed

# After installation, Claude Code commands are available
# within Claude chat sessions
```

### Method 2: NPM Global Installation (Experimental)

> **⚠️ Warning**: This method is experimental and the package `@anthropic/claude-cli` may not exist. The primary and recommended way to use Claude Code is through the Claude desktop app.

```bash
# Install Claude Code CLI globally (experimental - may not work)
npm install -g @anthropic/claude-cli

# Verify installation (if package exists)
claude --version
```

### Method 3: Claude-Flow Enhanced Installation

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

### Method 4: Docker Installation (Experimental)

> **⚠️ Warning**: The Docker image `anthropic/claude-code:latest` availability is not confirmed by official sources. This method may not work.

```bash
# Pull Claude Code image (experimental - may not exist)
docker pull anthropic/claude-code:latest

# Run with volume mounting (if image exists)
docker run -it -v $(pwd):/workspace anthropic/claude-code
```

## Authentication

### Claude Desktop App Authentication

```bash
# Authentication is handled through the Claude desktop app
# Simply sign in to your Claude account in the app
# No separate CLI authentication needed

# For Pro/Max features, ensure your subscription is active
# in your Claude account settings
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

### Anthropic API Authentication (For API Access)

```bash
# For direct API usage (separate from Claude Code)
# Set API key as environment variable
export ANTHROPIC_API_KEY="sk-ant-..."

# This is for API access, not Claude Code within the desktop app
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
/permissions

# Or create .claude/settings.json manually
mkdir -p .claude
```

Example settings file (`.claude/settings.json`):

```json
{
  "permissions": {
    "allowedCommands": [
      "npm run *",
      "git *",
      "node *",
      "python *"
    ],
    "blockedCommands": [
      "rm -rf /",
      "sudo *"
    ],
    "allowedDomains": [
      "docs.anthropic.com",
      "github.com",
      "npmjs.com"
    ],
    "fileAccess": {
      "readOnly": ["/etc", "/usr", "/bin"],
      "noAccess": ["/private", "~/.ssh"]
    }
  },
  "defaultBehavior": "ask"
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