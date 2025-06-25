# Understanding Claude Code

[← Back: Basic Commands](03-basic-commands.md) | [Main](../README.md) | [Next: MCP Protocol →](05-mcp-protocol.md)

---

## What is Claude Code?

Claude Code is Anthropic's official command-line interface that brings the power of Claude AI directly to your development environment. As a standalone NPM package (`@anthropic-ai/claude-code`), it's designed to be:

- **Low-level and unopinionated**: Direct model access without imposed workflows
- **Flexible**: Adapt to any development style or process
- **Powerful**: Full access to Claude's capabilities via API
- **Safe**: Built-in permission controls and safety features
- **Standalone**: Runs independently in your terminal with API authentication

## Core Components

### 1. CLI Interface
Standalone command-line tool (`claude`) that provides direct interaction with Claude models via API.

### 2. API Authentication
Uses Anthropic API keys for secure, direct access to Claude models.

### 3. File Operations
Built-in tools for reading, writing, and editing files in your development environment.

### 4. MCP Protocol
Universal connectivity to external tools and data sources through MCP servers.

### 5. Memory System
Persistent knowledge storage across sessions for maintaining context.

## Key Features

- **Multi-modal Input**: Process text, code, and images
- **Extended Context**: Up to 200K token context windows
- **Tool Use**: Execute commands, read/write files, search the web
- **Parallel Processing**: Multi-agent orchestration
- **Version Control**: Git integration and awareness

---

[← Back: Basic Commands](03-basic-commands.md) | [Main](../README.md) | [Next: MCP Protocol →](05-mcp-protocol.md)