# Best Practices

[← Back: Cost Management](11-cost-management.md) | [Main](../README.md) | [Next: Command Reference →](13-command-reference.md)

---

## Overview

This guide consolidates best practices from Anthropic's official Claude Code engineering team and thousands of community sessions. Following these practices will maximize efficiency, minimize costs, and produce higher quality results.

**Key Principles from Anthropic:**
- Claude Code is intentionally low-level and unopinionated
- Provides close to raw model access without forcing specific workflows
- Designed to be a flexible, customizable, scriptable, and safe power tool

## Project Setup

### 1. Always Create CLAUDE.md

**Essential for every project** - Anthropic strongly recommends CLAUDE.md as central documentation for project configuration.

Every project should have a `CLAUDE.md` file documenting:

```markdown
# Claude Code Configuration

## Build Commands
- `npm run build`: Build the project
- `npm run test`: Run test suite  
- `npm run lint`: Run ESLint and format checks
- `npm run typecheck`: Run TypeScript type checking

## Code Style Preferences
- Use ES modules (import/export) syntax
- Destructure imports when possible
- Use TypeScript for all new code
- Follow existing naming conventions
- Add JSDoc comments for public APIs
- Use async/await instead of Promise chains
- Prefer const/let over var

## Testing Requirements
- Jest for unit tests
- Playwright for E2E tests
- Minimum 80% coverage required
- All PRs must include tests

## Project Structure
- `/src`: Source code
- `/tests`: Test files
- `/docs`: Documentation
- `/scripts`: Build and utility scripts

## Important Context
- Using PostgreSQL 14 with PostGIS
- Redis for caching
- Deployed on AWS ECS
- API follows REST conventions

## Unexpected Project Behaviors
- Document any quirks or non-standard behaviors
- Include workarounds for known issues
- List dependencies with specific version requirements
```

### 2. Configure Permissions Properly

**Anthropic Recommendation:** Start with conservative permissions and expand as needed.

Create `.claude/settings.json`:

```json
{
  "permissions": {
    "allowedCommands": [
      "npm run *",
      "git status",
      "git diff",
      "git add *",
      "git commit *",
      "node *.js",
      "python *.py"
    ],
    "blockedCommands": [
      "rm -rf /",
      "sudo *",
      ":(){:|:&};:"
    ],
    "fileAccess": {
      "readOnly": ["/etc", "/usr", "/bin"],
      "noAccess": ["~/.ssh", "/private", ".env.production"]
    }
  }
}
```

### 3. Use .gitignore for Claude

Add Claude-specific entries:

```gitignore
# Claude Code
.claude/
.mcp/
claude-session-*
*.claude-backup
.claude-cache/
```

## Prompting Best Practices

### 1. Be Specific and Concise

```bash
# BAD: Vague and verbose
claude> I need you to help me create a function that will process 
        user data and validate it according to our business rules 
        and then save it to the database with proper error handling

# GOOD: Specific and clear
claude> Create validateAndSaveUser(userData: UserInput): Promise<User>
        - Validate: email format, age >= 18, unique username
        - Hash password with bcrypt
        - Save to PostgreSQL
        - Return saved user or throw ValidationError
```

### 2. Provide Context Upfront

```bash
# Include relevant context in first message
claude> Context: Express.js API using TypeScript, PostgreSQL, Jest tests.
        Task: Add password reset functionality with email tokens.
        Requirements: 24-hour expiry, one-time use, rate limited.
```

### 3. Use Think Modes Appropriately

```bash
# Simple tasks - no thinking needed
claude> Format this JSON file

# Complex logic - use thinking
claude> Think hard about the best database schema for a multi-tenant SaaS

# Critical decisions - maximum thinking
claude> Ultrathink about security implications of this authentication flow
```

## Code Management

### 1. Use Batch Operations

```javascript
// BAD: Multiple individual reads
const file1 = await Read('src/index.js');
const file2 = await Read('src/app.js');
const file3 = await Read('src/config.js');

// GOOD: Single batch read
const files = await Read(['src/index.js', 'src/app.js', 'src/config.js']);

// BETTER: Use glob for patterns
const allTests = await Glob('**/*.test.js');
```

### 2. Leverage MultiEdit

```javascript
// BAD: Multiple single edits
await Edit('src/app.js', 'const PORT = 3000', 'const PORT = process.env.PORT || 3000');
await Edit('src/app.js', 'debug: true', 'debug: process.env.NODE_ENV !== "production"');

// GOOD: Single MultiEdit
await MultiEdit('src/app.js', [
  {
    old_string: 'const PORT = 3000',
    new_string: 'const PORT = process.env.PORT || 3000'
  },
  {
    old_string: 'debug: true',
    new_string: 'debug: process.env.NODE_ENV !== "production"'
  }
]);
```

### 3. Always Use TodoWrite

For any task with more than 2 steps:

```javascript
TodoWrite([
  {
    id: "setup",
    content: "Set up project structure",
    status: "completed",
    priority: "high"
  },
  {
    id: "api", 
    content: "Implement REST API endpoints",
    status: "in_progress",
    priority: "high"
  },
  {
    id: "tests",
    content: "Write comprehensive tests",
    status: "pending",
    priority: "medium"
  },
  {
    id: "docs",
    content: "Generate API documentation",
    status: "pending",
    priority: "low"
  }
]);
```

## Memory Management

### 1. Use Structured Keys

```javascript
// BAD: Unstructured keys
Memory.store("data", results);
Memory.store("config", settings);

// GOOD: Hierarchical structure
Memory.store("project/backend/api/endpoints", endpoints);
Memory.store("project/backend/database/schema", schema);
Memory.store("project/frontend/components/list", components);
```

### 2. Clean Up Regularly

```javascript
// After completing a feature
Memory.cleanup("feature/temp/*");

// Archive old data
const oldData = Memory.query("project/archive/*");
Memory.export("archive-2025-01.json", oldData);
Memory.cleanup("project/archive/*");
```

### 3. Use Memory for Cross-Session State

```javascript
// Save session state before ending
Memory.store("session/last", {
  timestamp: new Date(),
  completed: ["auth", "api", "tests"],
  nextTasks: ["documentation", "deployment"],
  blockers: ["waiting for API keys"],
  context: "Working on user management feature"
});

// Resume in next session
const lastSession = Memory.get("session/last");
console.log(`Resuming from: ${lastSession.context}`);
```

## Model Usage Optimization

### 1. Model Selection Strategy

```javascript
function selectModel(task) {
  const opusTasks = [
    'architecture', 'security', 'algorithm',
    'optimization', 'debugging complex', 'system design'
  ];
  
  const requiresOpus = opusTasks.some(keyword => 
    task.toLowerCase().includes(keyword)
  );
  
  return requiresOpus ? '/model opus-4' : '/model sonnet-4';
}
```

### 2. Context Window Management

```bash
# Monitor context usage
/context

# Clear when above 80%
/clear

# Save important context first
Memory.store("context/important", getCurrentContext());
```

### 3. Token-Efficient Patterns

```python
# BAD: Asking for verbose explanation
claude> Explain in detail how this sorting algorithm works

# GOOD: Direct implementation
claude> Implement quicksort in Python with inline comments

# BETTER: Reference existing patterns
claude> Implement quicksort similar to src/utils/sort.js
```

## Testing Best Practices

### 1. Test-First Development Workflows

**Anthropic's Recommended TDD Approach** - Particularly effective for changes easily verifiable with tests.

```bash
# Method 1: Traditional TDD with claude-flow
./claude-flow sparc tdd "Shopping cart checkout"

# Method 2: Alternative Test-First Workflow (Anthropic Preferred)
claude> 1. Write comprehensive test suite for user authentication
        2. Commit the failing tests first
        3. Implement minimal code to make tests pass
        4. Refactor while keeping tests green
        5. Commit incremental improvements

# Method 3: Explore-Plan-Code-Commit (Anthropic's Core Workflow)
claude> 1. Read relevant files and understand context
        2. Use think modes for deeper analysis
        3. Create detailed implementation plan
        4. Write code following the plan
        5. Commit changes with clear messages

# Benefits of Test-First approach:
# - Clear specifications before implementation
# - Better test coverage
# - Easier debugging when tests fail
# - Forces thinking about edge cases early
# - Provides immediate feedback on implementation
```

### 2. Test Edge Cases

```javascript
// Always test:
// - Empty inputs
// - Null/undefined values
// - Boundary conditions
// - Error scenarios
// - Concurrent operations
```

### 3. Use Test Fixtures

```javascript
// Create reusable test data
Memory.store("test/fixtures/users", [
  { id: 1, email: "test@example.com", role: "admin" },
  { id: 2, email: "user@example.com", role: "user" }
]);

// Use in tests
const testUsers = Memory.get("test/fixtures/users");
```

## Git Workflow Best Practices

### 1. Use Git Worktrees for Parallel Development

**Anthropic's Advanced Multi-Claude Strategy** - Use git worktrees to run multiple Claude instances simultaneously on different branches.

```bash
# Create worktree for feature development
git worktree add ../project-feature-auth feature/authentication

# Benefits:
# - Work on multiple branches simultaneously
# - No need to stash/unstash changes
# - Faster context switching
# - Parallel testing of different approaches
# - Multiple Claude instances can work on different features

# Advanced Multi-Claude workflow
# Terminal 1: Main development
claude> Create git worktree for feature/payment-integration
        Implement Stripe integration in the worktree
        Keep main branch clean for hotfixes

# Terminal 2: Parallel feature development
cd ../project-feature-auth
claude> Implement authentication system in this worktree

# Terminal 3: Bug fixes on main branch
claude> Fix critical bug on main branch while other features develop
```

### 2. Commit Strategies

```bash
# Atomic commits with clear messages
git add -p  # Stage changes interactively
git commit -m "feat: add user authentication with JWT"

# Use conventional commits
# feat: new feature
# fix: bug fix
# docs: documentation changes
# style: formatting changes
# refactor: code restructuring
# test: test additions/changes
# chore: maintenance tasks
```

## Visual Development Best Practices

### 1. Screenshot-Driven UI Development

**Anthropic's Visual Iteration Approach** - Essential for effective UI development.

```bash
# Visual iteration workflow (Anthropic recommended)
# Step 1: Capture current state
claude> Take a screenshot of the current dashboard

# Step 2: Implement changes
claude> Implement the new sidebar navigation

# Step 3: Visual validation
claude> Take another screenshot to show the changes
        Compare with the previous version

# Step 4: Iterate based on feedback
claude> Adjust spacing and colors based on visual comparison
        Take final screenshot to confirm improvements

# Benefits:
# - Immediate visual validation
# - Better communication of UI changes
# - Easier to spot layout issues
# - Documents the evolution of the UI
# - Reduces back-and-forth iterations
```

### 2. Use Visual Mockups

```bash
# Start with mockups
claude> Here's a mockup of the desired layout [attach image]
        Implement this design using React and Tailwind CSS
        Show me the result with a screenshot
```

### 3. Iterative Visual Refinement

```bash
# Multiple rounds of visual feedback
claude> Round 1: Basic layout implementation
        [Screenshot]
        Round 2: Add responsive design
        [Screenshot]
        Round 3: Polish animations and transitions
        [Screenshot]
```

## Security Best Practices

### 1. Never Commit Secrets

```bash
# Check for secrets before committing
claude> Scan all files for potential secrets like API keys, passwords, tokens

# Use environment variables
const apiKey = process.env.API_KEY; // Good
const apiKey = "sk-1234567890"; // Bad
```

### 2. Validate All Inputs

```javascript
// Always validate and sanitize
function processUserInput(input) {
  // Validate type
  if (typeof input !== 'string') {
    throw new TypeError('Input must be string');
  }
  
  // Sanitize
  const sanitized = input.trim().toLowerCase();
  
  // Validate content
  if (!isValid(sanitized)) {
    throw new ValidationError('Invalid input');
  }
  
  return sanitized;
}
```

### 3. Use Secure Defaults

```javascript
// Secure configuration defaults
const config = {
  encryption: process.env.ENCRYPTION || 'AES-256-GCM',
  https: process.env.HTTPS !== 'false', // Default true
  cors: {
    origin: process.env.CORS_ORIGIN || false, // Default restrictive
    credentials: process.env.CORS_CREDENTIALS === 'true' // Default false
  }
};
```

## Performance Optimization

### 1. Profile Before Optimizing

```bash
# Always measure first
claude> Profile this function and identify bottlenecks

# Then optimize
claude> Optimize the identified bottlenecks
```

### 2. Use Caching Wisely

```javascript
// Cache expensive operations
const cacheKey = `user:${userId}:profile`;
let profile = await cache.get(cacheKey);

if (!profile) {
  profile = await expensiveProfileLookup(userId);
  await cache.set(cacheKey, profile, { ttl: 3600 });
}
```

### 3. Batch Database Operations

```javascript
// BAD: N+1 queries
for (const userId of userIds) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [userId]);
  users.push(user);
}

// GOOD: Single query
const users = await db.query(
  'SELECT * FROM users WHERE id IN (?)',
  [userIds]
);
```

## Workflow Optimization

### 1. Plan Before Implementing

```bash
# Always start with research and planning
./claude-flow sparc run researcher "Best practices for {feature}"
./claude-flow sparc run architect "Design {feature} architecture"
# Then implement
./claude-flow sparc run coder "Implement {feature}"
```

### 2. Use Parallel Execution

```bash
# Run independent tasks in parallel
./claude-flow swarm "Build feature X" \
  --parallel \
  --max-agents 5
```

### 3. Regular Checkpoints

```javascript
// Save progress regularly
Memory.store("checkpoint/feature-x/phase1", {
  timestamp: new Date(),
  completed: completedTasks,
  remaining: remainingTasks,
  decisions: majorDecisions
});
```

## Common Pitfalls to Avoid

### 1. Over-Engineering

```bash
# BAD: Creating unnecessary abstraction
claude> Create a fully extensible plugin system for this simple config loader

# GOOD: Start simple
claude> Create a config loader that reads JSON files
```

### 2. Ignoring Existing Patterns

```bash
# Always check existing code first
claude> How are API endpoints structured in this project?
# Then follow the same pattern
```

### 3. Not Testing Edge Cases

```javascript
// Always test:
- Empty arrays/objects
- Very large inputs
- Special characters
- Concurrent access
- Network failures
```

### 4. Forgetting Error Handling

```javascript
// BAD: No error handling
const data = await fetchData();
process(data);

// GOOD: Comprehensive error handling
try {
  const data = await fetchData();
  if (!data) throw new Error('No data received');
  return process(data);
} catch (error) {
  logger.error('Data processing failed:', error);
  throw new ProcessingError('Failed to process data', { cause: error });
}
```

## Continuous Improvement

### 1. Regular Reviews

```bash
# Weekly code review
./claude-flow sparc run reviewer "Review this week's code for improvements"

# Monthly architecture review
./claude-flow sparc run architect "Evaluate current architecture"
```

### 2. Learn from Mistakes

```javascript
// Document lessons learned
Memory.store("lessons/2025-01", {
  issue: "Database deadlock in production",
  cause: "Long-running transactions",
  solution: "Implement transaction timeout and retry logic",
  prevention: "Add transaction duration monitoring"
});
```

### 3. Stay Updated

```bash
# Regular dependency updates
./claude-flow swarm "Update dependencies and fix breaking changes" \
  --strategy maintenance

# Stay informed about best practices
claude> What are the latest best practices for {technology}?
```

## Next Steps

- Review [Command Reference](13-command-reference.md) for all commands
- Check [Troubleshooting](14-troubleshooting.md) for common issues
- Explore [Examples & Recipes](15-examples-recipes.md) for patterns

---

[← Back: Cost Management](11-cost-management.md) | [Main](../README.md) | [Next: Command Reference →](13-command-reference.md)