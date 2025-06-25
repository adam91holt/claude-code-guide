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

### Method 1: NPM Global Installation (Primary Method)

```bash
# Install Claude Code CLI globally
npm install -g @anthropic-ai/claude-code

# Verify installation
claude --version

# Start interactive session
claude
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

### Method 3: Docker Installation (Advanced)

```bash
# Using official Docker setup
docker run -it \
  -v $(pwd):/workspace \
  -e ANTHROPIC_API_KEY="your-api-key" \
  node:18-alpine sh -c "npm install -g @anthropic-ai/claude-code && claude"
```

## Authentication

### API Key Setup (Required)

Claude Code requires an Anthropic API key for authentication:

```bash
# Method 1: Environment variable (recommended)
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# Method 2: Configuration file
echo "sk-ant-api03-..." > ~/.anthropic/api_key

# Method 3: Interactive setup
claude auth

# Verify authentication
claude auth status
```

### Get Your API Key

1. Visit [Anthropic Console](https://console.anthropic.com/)
2. Sign in to your account
3. Navigate to API Keys section
4. Create a new API key
5. Copy the key (starts with `sk-ant-api03-`)

### Subscription Requirements

| Feature | Free Tier | Pro ($20/mo) | Enterprise |
|---------|-----------|-------------|------------|
| Basic Commands | ✅ | ✅ | ✅ |
| File Operations | ✅ | ✅ | ✅ |
| Advanced Models | ❌ | ✅ Sonnet | ✅ All Models |
| High Usage | ❌ | ✅ | ✅ Unlimited |

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

Verify Claude Code is working correctly:

```bash
# Check Claude Code installation
claude --version

# Test authentication
claude auth status

# Start interactive session (should prompt for first-time setup)
claude

# Within Claude session, test basic functionality:
# > Read package.json
# > Write hello.txt "Hello from Claude Code!"
# > Bash ls -la
```

### Optional: Verify Claude-Flow Integration

```bash
# Check claude-flow if installed
./claude-flow doctor

# Expected output:
# ✓ Claude Code CLI accessible
# ✓ Authentication valid
# ✓ MCP servers configured
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