# API & SDK Reference

[← Back: Examples & Recipes](15-examples-recipes.md) | [Main](../README.md)

---

## Overview

This reference covers programmatic access to Claude Code features through APIs and SDKs. While Claude Code primarily operates through the desktop app and CLI, several programmatic interfaces are available for automation and integration.

## Claude API Integration

### Direct Anthropic API

```python
# Python SDK
import anthropic

client = anthropic.Anthropic(
    api_key="sk-ant-..."
)

message = client.messages.create(
    model="claude-3-opus-20240229",
    max_tokens=1000,
    temperature=0,
    system="You are a helpful coding assistant.",
    messages=[
        {
            "role": "user",
            "content": "Write a Python function to sort a list"
        }
    ]
)

print(message.content)
```

```javascript
// Node.js SDK
import Anthropic from '@anthropic-ai/sdk';

const anthropic = new Anthropic({
  apiKey: 'sk-ant-...',
});

const message = await anthropic.messages.create({
  model: 'claude-3-opus-20240229',
  max_tokens: 1000,
  messages: [
    {
      role: 'user',
      content: 'Write a JavaScript function to validate email'
    }
  ],
});

console.log(message.content);
```

## Claude-Flow API (Conceptual)

> **⚠️ Important**: The claude-flow API endpoints described below are conceptual examples. Actual API availability and endpoints may vary by claude-flow version.

### Command Line Interface

Claude-flow primarily operates through CLI commands:

```bash
# Swarm operations
./claude-flow swarm "Build authentication system" --strategy development

# SPARC mode execution
./claude-flow sparc run coder "Implement user auth"

# Memory operations
./claude-flow memory store "key" "data"
./claude-flow memory get "key"
```

### Programmatic Usage

For programmatic integration, use subprocess calls:

```python
# Python example
import subprocess
import json

def run_claude_flow(command):
    result = subprocess.run(
        ['./claude-flow'] + command.split(),
        capture_output=True,
        text=True
    )
    return result.stdout

# Execute swarm
result = run_claude_flow('swarm "Build API" --strategy development')
print(result)
```

```javascript
// Node.js example
const { spawn } = require('child_process');

function runClaudeFlow(command) {
    return new Promise((resolve, reject) => {
        const args = command.split(' ');
        const child = spawn('./claude-flow', args);
        
        let output = '';
        child.stdout.on('data', (data) => {
            output += data.toString();
        });
        
        child.on('close', (code) => {
            if (code === 0) {
                resolve(output);
            } else {
                reject(new Error(`Process exited with code ${code}`));
            }
        });
    });
}

// Usage
runClaudeFlow('sparc run coder "Implement auth"')
    .then(result => console.log(result))
    .catch(error => console.error(error));
```

## MCP SDK Integration

### Custom MCP Server

```javascript
// Create custom MCP server
import { Server } from '@modelcontextprotocol/sdk';
import { StdioTransport } from '@modelcontextprotocol/sdk/transport';

const server = new Server({
  name: 'custom-api',
  version: '1.0.0'
});

// Add tools
server.addTool({
  name: 'execute_query',
  description: 'Execute database query',
  parameters: {
    type: 'object',
    properties: {
      query: { type: 'string' },
      params: { type: 'array' }
    }
  },
  handler: async ({ query, params }) => {
    // Your implementation
    return { rows: [], affected: 0 };
  }
});

// Start server
const transport = new StdioTransport();
server.connect(transport);
```

### MCP Client

```python
# Connect to MCP server programmatically
from mcp import Client, StdioTransport
import asyncio

async def main():
    transport = StdioTransport(['mcp-server-postgres', '--connection-string', 'postgresql://...'])
    client = Client(transport)
    
    await client.connect()
    
    # List available tools
    tools = await client.list_tools()
    print(f"Available tools: {[tool.name for tool in tools]}")
    
    # Execute tool
    result = await client.call_tool('execute_query', {
        'query': 'SELECT * FROM users WHERE active = ?',
        'params': [True]
    })
    
    print(f"Query result: {result}")

asyncio.run(main())
```

## CLI Automation

### Headless Mode

```bash
# Run Claude Code in headless mode
claude --headless --timeout 60000 "Implement user login functionality"

# With JSON output
claude --headless --output json "Generate API documentation" > output.json

# Batch processing
echo "Review security of authentication.js" | claude --headless --stdin
```

### Scripting Interface

```bash
#!/bin/bash
# automated-review.sh

# Function to review files
review_file() {
    local file=$1
    echo "Reviewing $file..."
    
    result=$(claude --headless --timeout 30000 "Review $file for security issues and best practices")
    
    if [ $? -eq 0 ]; then
        echo "✓ Review completed for $file"
        echo "$result" > "reviews/$(basename $file).review"
    else
        echo "✗ Review failed for $file"
    fi
}

# Review all JavaScript files
find src -name "*.js" | while read file; do
    review_file "$file"
done
```

## Webhook Integration

### Claude-Flow Webhooks

```javascript
// Set up webhook endpoint
app.post('/webhook/claude-flow', (req, res) => {
  const { event, data } = req.body;
  
  switch (event) {
    case 'swarm.started':
      console.log(`Swarm ${data.swarmId} started with ${data.agentCount} agents`);
      break;
      
    case 'swarm.completed':
      console.log(`Swarm ${data.swarmId} completed in ${data.duration}ms`);
      // Process results
      processSwarmResults(data.results);
      break;
      
    case 'agent.failed':
      console.log(`Agent ${data.agentId} failed: ${data.error}`);
      // Handle failure
      handleAgentFailure(data);
      break;
  }
  
  res.status(200).send('OK');
});

// Configure webhook in claude-flow
// ./claude-flow config set webhook.url http://localhost:3000/webhook/claude-flow
// ./claude-flow config set webhook.events swarm.started,swarm.completed,agent.failed
```

## CI/CD Integration

### GitHub Actions

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review

on: [pull_request]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Claude
        run: |
          npm install -g @anthropic/claude-cli
          
      - name: Review Changes
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          # Get changed files
          git diff --name-only HEAD~1 > changed_files.txt
          
          # Review each file
          while read file; do
            if [[ $file == *.js || $file == *.ts ]]; then
              claude --headless "Review $file for code quality and security" > "review_${file//\//_}.md"
            fi
          done < changed_files.txt
          
      - name: Post Review
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const reviews = fs.readdirSync('.').filter(f => f.startsWith('review_'));
            
            let comment = '## Claude Code Review\n\n';
            reviews.forEach(review => {
              const content = fs.readFileSync(review, 'utf8');
              comment += `### ${review}\n${content}\n\n`;
            });
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```

### GitLab CI

```yaml
# .gitlab-ci.yml
stages:
  - review

claude-review:
  stage: review
  script:
    - git clone https://github.com/ruvnet/claude-code-flow
    - cd claude-code-flow && npm install
    - |
      for file in $(git diff --name-only $CI_MERGE_REQUEST_TARGET_BRANCH_NAME); do
        if [[ $file == *.py ]]; then
          ./claude-flow sparc run reviewer "Review $file for Python best practices"
        fi
      done
  only:
    - merge_requests
  variables:
    ANTHROPIC_API_KEY: $ANTHROPIC_API_KEY
```

## Performance Monitoring

### Token Usage Tracking

```javascript
// Monitor token usage programmatically
const { spawn } = require('child_process');

class TokenMonitor {
  constructor() {
    this.currentUsage = 0;
    this.dailyLimit = 100000;
  }
  
  async getCurrentUsage() {
    return new Promise((resolve, reject) => {
      const ccusage = spawn('ccusage', ['--json']);
      let output = '';
      
      ccusage.stdout.on('data', (data) => {
        output += data.toString();
      });
      
      ccusage.on('close', (code) => {
        if (code === 0) {
          const usage = JSON.parse(output);
          this.currentUsage = usage.today.total_tokens;
          resolve(this.currentUsage);
        } else {
          reject(new Error('Failed to get usage data'));
        }
      });
    });
  }
  
  async checkLimits() {
    const usage = await this.getCurrentUsage();
    const percentUsed = (usage / this.dailyLimit) * 100;
    
    if (percentUsed > 80) {
      console.warn(`Token usage at ${percentUsed.toFixed(1)}% of daily limit`);
      return false;
    }
    
    return true;
  }
}

// Usage
const monitor = new TokenMonitor();
const canProceed = await monitor.checkLimits();
```

## Error Handling

### Retry Logic

```python
# Robust API calls with retry logic
import time
import requests
from functools import wraps

def retry_on_failure(max_retries=3, delay=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except requests.exceptions.RequestException as e:
                    if attempt == max_retries - 1:
                        raise e
                    
                    wait_time = delay * (2 ** attempt)  # Exponential backoff
                    print(f"Attempt {attempt + 1} failed, retrying in {wait_time}s...")
                    time.sleep(wait_time)
            
        return wrapper
    return decorator

@retry_on_failure(max_retries=3)
def call_claude_api(prompt):
    response = requests.post('http://localhost:8080/api/sparc', 
                           json={'mode': 'coder', 'objective': prompt})
    response.raise_for_status()
    return response.json()
```

## Rate Limiting

### Client-Side Rate Limiting

```javascript
// Rate limiter for API calls
class RateLimiter {
  constructor(requestsPerMinute = 60) {
    this.requestsPerMinute = requestsPerMinute;
    this.requests = [];
  }
  
  async waitForSlot() {
    const now = Date.now();
    const oneMinuteAgo = now - 60000;
    
    // Remove old requests
    this.requests = this.requests.filter(time => time > oneMinuteAgo);
    
    if (this.requests.length >= this.requestsPerMinute) {
      const oldestRequest = Math.min(...this.requests);
      const waitTime = 60000 - (now - oldestRequest);
      
      if (waitTime > 0) {
        console.log(`Rate limit reached, waiting ${waitTime}ms...`);
        await new Promise(resolve => setTimeout(resolve, waitTime));
      }
    }
    
    this.requests.push(now);
  }
}

// Usage
const limiter = new RateLimiter(30); // 30 requests per minute

async function makeApiCall(data) {
  await limiter.waitForSlot();
  return fetch('/api/claude', { method: 'POST', body: JSON.stringify(data) });
}
```

## Best Practices

### 1. Authentication Security

```javascript
// Secure API key handling
class ClaudeClient {
  constructor(apiKey = process.env.ANTHROPIC_API_KEY) {
    if (!apiKey) {
      throw new Error('API key required');
    }
    
    if (!apiKey.startsWith('sk-ant-')) {
      throw new Error('Invalid API key format');
    }
    
    this.apiKey = apiKey;
  }
  
  // Never log the API key
  toString() {
    return '[ClaudeClient with authenticated key]';
  }
}
```

### 2. Resource Management

```python
# Context manager for claude-flow operations
import subprocess
import json
from contextlib import contextmanager

@contextmanager
def claude_flow_session(config=None):
    """Manage claude-flow session lifecycle"""
    session_id = None
    try:
        # Start session
        if config:
            cmd = ['./claude-flow', 'start', '--config', json.dumps(config)]
        else:
            cmd = ['./claude-flow', 'start']
            
        result = subprocess.run(cmd, capture_output=True, text=True)
        session_id = result.stdout.strip()
        
        yield session_id
        
    finally:
        # Clean up session
        if session_id:
            subprocess.run(['./claude-flow', 'stop', session_id])

# Usage
with claude_flow_session({'max_agents': 5}) as session:
    # Perform operations
    result = subprocess.run([
        './claude-flow', 'swarm', 
        'Build API endpoints',
        '--session', session
    ])
```

### 3. Error Recovery

```javascript
// Graceful degradation
async function robustClaudeOperation(operation, fallback = null) {
  try {
    return await operation();
  } catch (error) {
    console.error('Claude operation failed:', error.message);
    
    // Try fallback
    if (fallback) {
      console.log('Attempting fallback operation...');
      try {
        return await fallback();
      } catch (fallbackError) {
        console.error('Fallback also failed:', fallbackError.message);
      }
    }
    
    // Return safe default
    return { error: 'Operation failed', message: error.message };
  }
}
```

## Next Steps

- Return to [Main Documentation](../README.md)
- Explore [Examples & Recipes](15-examples-recipes.md) for practical implementations
- Check [Troubleshooting](14-troubleshooting.md) for common API issues

---

[← Back: Examples & Recipes](15-examples-recipes.md) | [Main](../README.md)