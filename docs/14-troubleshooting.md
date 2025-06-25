# Troubleshooting

[← Back: Command Reference](13-command-reference.md) | [Main](../README.md) | [Next: Examples & Recipes →](15-examples-recipes.md)

---

## Common Issues and Solutions

### Authentication Issues

#### Problem: "Authentication failed" error

**Symptoms:**
```
Error: Authentication failed. Please check your credentials.
```

**Solutions:**

1. **Check API Key**
   ```bash
   # Verify environment variable
   echo $ANTHROPIC_API_KEY
   
   # Re-authenticate
   claude auth logout
   claude auth login --api-key sk-ant-...
   ```

2. **Subscription Authentication**
   ```bash
   # Clear cached credentials
   rm -rf ~/.claude/auth
   
   # Re-login with subscription
   claude auth login --subscription
   ```

3. **Token Expiry**
   ```bash
   # Check auth status
   claude auth status
   
   # Refresh token
   claude auth refresh
   ```

#### Problem: "Invalid API key format"

**Solution:**
```bash
# Ensure key starts with sk-ant-
export ANTHROPIC_API_KEY="sk-ant-api03-..."

# Verify format
if [[ $ANTHROPIC_API_KEY =~ ^sk-ant- ]]; then
  echo "Valid format"
else
  echo "Invalid format"
fi
```

### Rate Limiting

#### Problem: "Rate limit exceeded"

**Symptoms:**
```
Error: Rate limit exceeded. Please wait before retrying.
```

**Solutions:**

1. **Check Current Usage**
   ```bash
   # Monitor usage
   ccusage blocks --live
   
   # View 5-hour window
   ccusage blocks --current
   ```

2. **Switch Models**
   ```bash
   # Use Sonnet for routine tasks
   /model sonnet-4
   ```

3. **Enable Request Queuing**
   ```bash
   export CLAUDE_QUEUE_REQUESTS=true
   export CLAUDE_RETRY_DELAY=5000
   ```

4. **Implement Backoff**
   ```javascript
   async function retryWithBackoff(fn, maxRetries = 3) {
     for (let i = 0; i < maxRetries; i++) {
       try {
         return await fn();
       } catch (error) {
         if (error.code === 'RATE_LIMIT' && i < maxRetries - 1) {
           const delay = Math.pow(2, i) * 1000;
           await new Promise(resolve => setTimeout(resolve, delay));
         } else {
           throw error;
         }
       }
     }
   }
   ```

### Context Overflow

#### Problem: "Context window exceeded"

**Symptoms:**
```
Error: Maximum context length (200000 tokens) exceeded
```

**Solutions:**

1. **Clear Context**
   ```bash
   # Check current usage
   /context
   
   # Clear if needed
   /clear
   ```

2. **Save Important Context**
   ```javascript
   // Before clearing
   Memory.store("context/important", {
     summary: "Current task state",
     key_decisions: [...],
     next_steps: [...]
   });
   
   // After clearing
   const saved = Memory.get("context/important");
   ```

3. **Use Summarization**
   ```bash
   # Summarize before clearing
   claude> Summarize our conversation focusing on decisions and next steps
   
   # Save summary
   Memory.store("session/summary", summary);
   
   # Clear and continue
   /clear
   ```

### MCP Connection Issues

#### Problem: "MCP server connection failed"

**Symptoms:**
```
Error: Failed to connect to MCP server 'database'
```

**Solutions:**

1. **Debug MCP Connections**
   ```bash
   # Enable debug mode
   claude --mcp-debug
   
   # Check server status
   ./claude-flow mcp status
   
   # List available servers
   ./claude-flow mcp list
   ```

2. **Verify Server Installation**
   ```bash
   # Check if server is installed
   which mcp-server-postgres
   
   # Install if missing
   npm install -g @modelcontextprotocol/server-postgres
   ```

3. **Check Configuration**
   ```json
   // .mcp.json
   {
     "mcpServers": {
       "database": {
         "type": "stdio",
         "command": "mcp-server-postgres",
         "args": ["--connection-string", "${DATABASE_URL}"],
         "env": {
           "NODE_ENV": "development"
         }
       }
     }
   }
   ```

4. **Test Connection**
   ```bash
   # Test database connection
   psql $DATABASE_URL -c "SELECT 1"
   
   # Restart MCP server
   ./claude-flow mcp restart database
   ```

### File Operation Errors

#### Problem: "Permission denied" when reading/writing files

**Solutions:**

1. **Check File Permissions**
   ```bash
   # Check permissions
   ls -la problematic-file.js
   
   # Fix permissions
   chmod 644 problematic-file.js
   ```

2. **Update Claude Settings**
   ```json
   // .claude/settings.json
   {
     "permissions": {
       "fileAccess": {
         "allowed": ["./src", "./tests"],
         "readOnly": ["/etc", "/usr"],
         "noAccess": ["~/.ssh", ".env.production"]
       }
     }
   }
   ```

3. **Use Correct Paths**
   ```javascript
   // BAD: Relative paths can be ambiguous
   Read('../src/file.js')
   
   // GOOD: Absolute paths
   Read('/home/user/project/src/file.js')
   
   // BETTER: From project root
   Read('./src/file.js')
   ```

### Memory Issues

#### Problem: "Memory operation failed"

**Solutions:**

1. **Check Memory Usage**
   ```bash
   # View memory stats
   ./claude-flow memory stats
   
   # Clean up old entries
   ./claude-flow memory cleanup --older-than 30
   ```

2. **Fix Corrupted Data**
   ```bash
   # Export current data
   ./claude-flow memory export backup.json
   
   # Reset memory
   rm -rf ~/.claude/memory
   
   # Import clean data
   ./claude-flow memory import backup-clean.json
   ```

3. **Use Namespaces**
   ```javascript
   // Isolate data by namespace
   Memory.store("key", data, { namespace: "project-123" });
   
   // Clean namespace
   Memory.cleanup("*", { namespace: "old-project" });
   ```

### Performance Issues

#### Problem: "Claude Code is running slowly"

**Solutions:**

1. **Check System Resources**
   ```bash
   # Monitor system
   top
   
   # Check disk space
   df -h
   
   # Check Claude processes
   ps aux | grep claude
   ```

2. **Optimize Requests**
   ```javascript
   // BAD: Many small requests
   for (const file of files) {
     await Read(file);
   }
   
   // GOOD: Batch request
   await Read(files);
   ```

3. **Clear Cache**
   ```bash
   # Clear Claude cache
   rm -rf ~/.claude/cache
   
   # Clear npm cache
   npm cache clean --force
   ```

4. **Reduce Parallel Operations**
   ```bash
   # Limit parallel agents
   export CLAUDE_MAX_PARALLEL=3
   
   # Use sequential for stability
   ./claude-flow swarm "Task" --sequential
   ```

### Model-Specific Issues

#### Problem: "Model not available"

**Solutions:**

1. **Check Subscription**
   ```bash
   # Verify plan
   claude account info
   
   # Pro users: Sonnet only
   /model sonnet-4
   
   # Max users: Both models
   /model opus-4
   ```

2. **Handle Auto-Switching**
   ```bash
   # Disable auto-switching
   export CLAUDE_AUTO_SWITCH=false
   
   # Force specific model
   /model opus-4 --force
   ```

### Integration Issues

#### Problem: "CI/CD pipeline failing"

**Solutions:**

1. **Headless Mode**
   ```bash
   # Use headless flag
   claude --headless --print "Task"
   
   # Set timeout
   claude --headless --timeout 60000 "Task"
   ```

2. **Environment Variables**
   ```yaml
   # GitHub Actions
   env:
     ANTHROPIC_API_KEY: ${{ secrets.CLAUDE_API_KEY }}
     CLAUDE_HEADLESS: true
     CLAUDE_NO_INTERACTIVE: true
   ```

3. **Error Handling**
   ```bash
   #!/bin/bash
   set -e  # Exit on error
   
   claude --headless "Review code" || {
     echo "Claude review failed"
     exit 1
   }
   ```

### Debugging Techniques

#### Enable Verbose Logging

```bash
# Set log level
export CLAUDE_LOG_LEVEL=debug

# Enable all debug flags
export CLAUDE_DEBUG=*
export MCP_DEBUG=true
export NODE_DEBUG=claude*
```

#### Trace Execution

```bash
# Trace Claude execution
strace -o claude.trace claude "Simple task"

# Analyze trace
grep -i error claude.trace
```

#### Check Logs

```bash
# View Claude logs
tail -f ~/.claude/logs/claude.log

# View specific session
cat ~/.claude/logs/session-*.log

# Search for errors
grep -i error ~/.claude/logs/*.log
```

### Emergency Recovery

#### Complete Reset

```bash
#!/bin/bash
# backup-and-reset.sh

# Backup current state
mkdir -p ~/claude-backup
cp -r ~/.claude ~/claude-backup/
cp -r ./.claude ~/claude-backup/project-claude

# Reset Claude
rm -rf ~/.claude
rm -rf ./.claude

# Reinstall
npm uninstall -g @anthropic/claude-cli
npm install -g @anthropic/claude-cli

# Re-authenticate
claude auth login

echo "Claude Code reset complete"
```

#### Recover from Corrupted State

```bash
# Export what you can
./claude-flow memory export recovery.json 2>/dev/null || true
./claude-flow session export current 2>/dev/null || true

# Clean state
rm -rf ~/.claude/state
rm -rf ~/.claude/sessions

# Restart
./claude-flow init --force
```

## Getting Help

### Built-in Help

```bash
# General help
/help

# Command-specific help
claude --help
./claude-flow --help
./claude-flow swarm --help
```

### Diagnostic Commands

```bash
# Run full diagnostic
./claude-flow doctor

# Check specific component
./claude-flow doctor --component mcp
./claude-flow doctor --component memory
./claude-flow doctor --component auth
```

### Community Support

1. **GitHub Issues**: [Report bugs](https://github.com/anthropics/claude-code/issues)
2. **Discord**: Join the Anthropic Discord
3. **Stack Overflow**: Tag questions with `claude-code`
4. **Documentation**: Check the [official docs](https://docs.anthropic.com/claude-code)

### Reporting Issues

When reporting issues, include:

```bash
# Generate diagnostic report
./claude-flow diagnostic --output report.json

# Include:
# - Claude version: claude --version
# - OS and version: uname -a
# - Node version: node --version
# - Error messages (full)
# - Steps to reproduce
# - Expected vs actual behavior
```

## Next Steps

- Explore [Examples & Recipes](15-examples-recipes.md) for solutions
- Review [Best Practices](12-best-practices.md) to avoid issues
- Return to [Main Documentation](../README.md)

---

[← Back: Command Reference](13-command-reference.md) | [Main](../README.md) | [Next: Examples & Recipes →](15-examples-recipes.md)