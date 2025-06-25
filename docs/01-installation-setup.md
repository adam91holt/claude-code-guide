# Installation & Setup

[← Back to Main](../README.md) | [Next: Quick Start Guide →](02-quick-start.md)

---

## System Requirements

- **Operating System**: macOS, Linux, Windows (WSL2 recommended)
- **Node.js**: Version 18.0 or higher
- **Memory**: Minimum 8GB RAM (16GB recommended)
- **Storage**: At least 2GB free space

## Version Compatibility

| Component | Latest Version | MCP Support | Notes |
|-----------|----------------|-------------|-------|
| **Claude Code CLI** | Latest | ✅ Full | Standalone NPM package |
| **Claude-Flow** | v1.0+ | ✅ Full | Separate enhancement tool |
| **API Access** | Current | ✅ Full | Requires API key |

**Note**: Features depend on your Anthropic API subscription tier.

## Installation Methods

### Method 1: Desktop App Installation (Primary Method)

**Claude Code runs within the Anthropic Claude Desktop Application:**

1. **Download Claude Desktop App**:
   - Visit [claude.ai](https://claude.ai)
   - Download the desktop application for your platform
   - Install and sign in with your Anthropic account

2. **Access Claude Code**:
   - Claude Code is integrated within the desktop app
   - Authentication is handled automatically through your account
   - No separate CLI installation required

```bash
# Claude Code runs within the desktop app
# No separate installation needed

# Access through:
# - Desktop application interface
# - Integrated terminal within the app
```

### Method 2: Claude-Flow Enhanced Installation (Separate Tool)

For the complete development environment with multi-agent orchestration:

```bash
# Quick start with NPX (recommended)
npx claude-flow@latest init --sparc

# OR global installation
npm install -g claude-flow
claude-flow init --sparc

# Verify installation
claude-flow --version
```


## Authentication

### Desktop App Authentication (Primary Method)

**Claude Code authentication is handled through the desktop app:**

1. **Sign in to Desktop App**:
   - Launch Claude Desktop Application
   - Sign in with your Anthropic account
   - Authentication is managed automatically

2. **API Key Setup (Optional)**:
   ```bash
   # For advanced integrations, you may set API key
   export ANTHROPIC_API_KEY="sk-ant-api03-..."
   ```

3. **Verification**:
   - Claude Code access is confirmed through desktop app functionality
   - No separate authentication commands needed

### Subscription Integration

**Claude Code usage is tied to your Anthropic subscription:**

- **Free Plan**: Limited Claude Code access
- **Pro Plan**: Enhanced access with higher limits
- **Team Plan**: Team features and collaboration
- **Enterprise**: Advanced features and custom limits

Subscription benefits are automatically applied through the desktop app.

### Get Your API Key

1. Visit [Anthropic Console](https://console.anthropic.com/)
2. Sign in to your account
3. Navigate to API Keys section
4. Create a new API key
5. Copy the key (starts with `sk-ant-api03-`)

### Subscription Benefits

| Plan | Authentication | Model Access | Rate Limits |
|------|----------------|--------------|-------------|
| **Max Plan** | Subscription + API | All models | Highest |
| **Pro Plan** | Subscription + API | Sonnet models | Standard |
| **API Only** | API key required | Based on API tier | API limits |

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
   rm -rf ~/.anthropic
   claude auth
   
   # Or use environment variable
   export ANTHROPIC_API_KEY="sk-ant-api03-..."
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