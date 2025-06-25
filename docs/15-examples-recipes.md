# Examples & Recipes

[← Back: Troubleshooting](14-troubleshooting.md) | [Main](../README.md) | [Next: API & SDK Reference →](16-api-sdk-reference.md)

---

## Quick Recipes

### Create a REST API with Authentication

```bash
# One command to build complete API
./claude-flow swarm "Build REST API with JWT auth, user CRUD, PostgreSQL" \
  --strategy development \
  --mode hierarchical \
  --max-agents 6 \
  --parallel

# What it creates:
# - Express.js server with TypeScript
# - JWT authentication with refresh tokens
# - User registration/login endpoints
# - PostgreSQL database schema
# - Input validation and error handling
# - Comprehensive test suite
# - API documentation
```

### Refactor Legacy Code

```bash
# Analyze and refactor old codebase
./claude-flow sparc run analyzer "Analyze legacy PHP code for modernization"

# Plan refactoring
./claude-flow sparc run architect "Plan migration from PHP to Node.js"

# Execute refactoring
./claude-flow swarm "Refactor authentication module to TypeScript" \
  --strategy optimization \
  --mode distributed
```

### Add Feature to Existing App

```javascript
// Use TodoWrite to track the feature
TodoWrite([
  {
    id: "design",
    content: "Design real-time notifications",
    status: "pending",
    priority: "high"
  },
  {
    id: "websocket",
    content: "Implement WebSocket server",
    status: "pending",
    priority: "high"
  },
  {
    id: "client",
    content: "Add client-side integration",
    status: "pending",
    priority: "medium"
  },
  {
    id: "tests",
    content: "Write integration tests",
    status: "pending",
    priority: "medium"
  }
]);

// Execute with TDD
claude> ./claude-flow sparc tdd "Real-time notifications with Socket.io"
```

## Complete Examples

### 1. E-Commerce Platform

```bash
# Phase 1: Architecture
./claude-flow sparc run architect "Design e-commerce platform with:
  - Product catalog
  - Shopping cart
  - Payment processing (Stripe)
  - Order management
  - User accounts"

# Phase 2: Database Design
./claude-flow sparc run coder "Create PostgreSQL schema for e-commerce"

# Phase 3: Build API
./claude-flow swarm "Build e-commerce REST API" \
  --strategy development \
  --mode hierarchical \
  --max-agents 8 \
  --config ecommerce.json

# Phase 4: Frontend
./claude-flow swarm "Build React frontend for e-commerce" \
  --strategy development \
  --mode distributed \
  --max-agents 6

# Phase 5: Integration Testing
./claude-flow sparc run tester "E2E tests for complete purchase flow"
```

**Configuration file (ecommerce.json):**
```json
{
  "agents": {
    "backend": {
      "count": 4,
      "tasks": ["api", "database", "payments", "auth"]
    },
    "frontend": {
      "count": 3,
      "tasks": ["components", "state", "routing"]
    },
    "testing": {
      "count": 1,
      "tasks": ["integration", "e2e"]
    }
  },
  "technologies": {
    "backend": ["Node.js", "Express", "PostgreSQL", "Redis"],
    "frontend": ["React", "Redux", "Tailwind"],
    "testing": ["Jest", "Playwright"]
  }
}
```

### 2. Microservices Migration

```bash
# Step 1: Analyze monolith
./claude-flow sparc run analyzer "Analyze monolithic app for microservices split"

# Step 2: Design services
Memory.store("architecture/services", {
  "user-service": ["authentication", "profiles", "permissions"],
  "product-service": ["catalog", "inventory", "pricing"],
  "order-service": ["cart", "checkout", "fulfillment"],
  "notification-service": ["email", "sms", "push"]
});

# Step 3: Create services
for service in user product order notification; do
  ./claude-flow swarm "Create ${service}-service microservice" \
    --strategy development \
    --mode centralized \
    --max-agents 4
done

# Step 4: API Gateway
./claude-flow sparc run coder "Create API gateway with:
  - Route management
  - Authentication
  - Rate limiting
  - Request/response transformation"

# Step 5: Service orchestration
./claude-flow sparc run architect "Design service orchestration with Kubernetes"
```

### 3. Real-Time Chat Application

```bash
# Complete chat app with Claude
./claude-flow swarm "Build real-time chat with:
  - WebSocket connections
  - Multiple rooms
  - Direct messages
  - File sharing
  - Message history
  - Online status" \
  --strategy development \
  --mode hierarchical \
  --max-agents 7 \
  --monitor

# Add AI features
./claude-flow sparc run coder "Add AI features:
  - Message summarization
  - Auto-moderation
  - Smart replies
  - Language translation"
```

### 4. CI/CD Pipeline Setup

```yaml
# Generate complete CI/CD pipeline
claude> Create GitHub Actions workflow for:
  - Multiple environments (dev, staging, prod)
  - Automated testing
  - Code quality checks
  - Security scanning
  - Docker builds
  - Kubernetes deployment

# Result: .github/workflows/deploy.yml
name: Deploy Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup
        run: |
          npm ci
          npm run build
      
      - name: Test
        run: |
          npm run test:unit
          npm run test:integration
          npm run test:e2e
      
      - name: Claude Review
        run: |
          npx @anthropic/claude-cli --headless \
            "Review code quality and security" > review.md
      
  security:
    runs-on: ubuntu-latest
    steps:
      - name: Security Scan
        run: |
          ./claude-flow sparc run analyzer "Security audit"
  
  deploy:
    needs: [test, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        run: |
          kubectl apply -f k8s/
```

### 5. Data Processing Pipeline

```python
# Create with Claude
claude> Create data processing pipeline:
  - Read from multiple sources (S3, API, Database)
  - Transform and validate data
  - Machine learning predictions
  - Store results
  - Generate reports

# Implementation
Memory.store("pipeline/config", {
  "sources": [
    {"type": "s3", "bucket": "raw-data"},
    {"type": "api", "endpoint": "https://api.example.com/data"},
    {"type": "postgres", "table": "transactions"}
  ],
  "transformations": [
    "normalize_dates",
    "clean_nulls",
    "aggregate_metrics"
  ],
  "ml_models": ["fraud_detection", "churn_prediction"],
  "outputs": ["warehouse", "dashboard", "alerts"]
});

# Execute pipeline creation
./claude-flow swarm "Build data pipeline from config" \
  --strategy development \
  --mode distributed \
  --max-agents 5
```

## Specialized Recipes

### Performance Optimization

```bash
# 1. Profile application
./claude-flow sparc run analyzer "Profile Node.js app for performance"

# 2. Identify bottlenecks
Memory.store("performance/bottlenecks", analysisResults);

# 3. Optimize identified issues
./claude-flow swarm "Optimize performance bottlenecks" \
  --strategy optimization \
  --mode mesh \
  --max-agents 4

# Common optimizations:
claude> Implement caching strategy for database queries
claude> Optimize image loading with lazy loading and CDN
claude> Add database indexes for slow queries
claude> Implement connection pooling
```

### Security Hardening

```bash
# Security audit and fixes
./claude-flow swarm "Security hardening for production" \
  --strategy analysis \
  --mode centralized \
  --config security-audit.json

# Specific security tasks
claude> Implement rate limiting on all endpoints
claude> Add input sanitization for XSS prevention
claude> Set up CSP headers
claude> Implement SQL injection prevention
claude> Add authentication timeout
claude> Enable audit logging
```

### Database Migration

```sql
-- Generate migration with Claude
claude> Create migration to:
  1. Add user_profiles table
  2. Add foreign key to users
  3. Migrate existing data
  4. Add indexes
  5. Create rollback script

-- Result
-- UP Migration
CREATE TABLE user_profiles (
    id SERIAL PRIMARY KEY,
    user_id INTEGER NOT NULL REFERENCES users(id),
    bio TEXT,
    avatar_url VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_user_profiles_user_id ON user_profiles(user_id);

-- Migrate existing data
INSERT INTO user_profiles (user_id, bio)
SELECT id, bio FROM users WHERE bio IS NOT NULL;

-- Remove old column
ALTER TABLE users DROP COLUMN bio;

-- DOWN Migration
ALTER TABLE users ADD COLUMN bio TEXT;

UPDATE users u
SET bio = p.bio
FROM user_profiles p
WHERE u.id = p.user_id;

DROP TABLE user_profiles;
```

### Testing Strategies

```javascript
// Comprehensive test generation
claude> Generate tests for UserService including:
  - Unit tests for all methods
  - Integration tests with database
  - API endpoint tests
  - Error scenarios
  - Edge cases
  - Performance tests

// Example output
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid data', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'SecurePass123!',
        name: 'Test User'
      };
      
      const user = await userService.createUser(userData);
      
      expect(user).toHaveProperty('id');
      expect(user.email).toBe(userData.email);
      expect(user.password).not.toBe(userData.password); // Should be hashed
    });
    
    it('should reject duplicate email', async () => {
      await userService.createUser(existingUserData);
      
      await expect(
        userService.createUser(existingUserData)
      ).rejects.toThrow('Email already exists');
    });
    
    // ... more tests
  });
});
```

### Documentation Generation

```bash
# Auto-generate comprehensive docs
./claude-flow sparc run documenter "Generate:
  - API documentation (OpenAPI)
  - Code documentation (JSDoc)
  - Architecture diagrams
  - Setup guide
  - Deployment guide"

# Generate specific docs
claude> Create OpenAPI 3.0 spec from Express routes
claude> Generate PlantUML diagrams for system architecture
claude> Create developer onboarding guide
```

## Workflow Automation

### Daily Development Workflow

```bash
#!/bin/bash
# daily-workflow.sh

# Morning setup
echo "🌅 Starting daily workflow..."

# 1. Update dependencies
./claude-flow sparc run maintenance "Check and update dependencies"

# 2. Review yesterday's work
./claude-flow sparc run reviewer "Review commits from last 24 hours"

# 3. Plan today's tasks
./claude-flow sparc "Plan tasks based on current sprint goals"

# 4. Set up monitoring
./claude-flow monitor --background

echo "✅ Ready for development!"
```

### Automated Code Review

```javascript
// Automated PR review
const reviewPR = async (prNumber) => {
  // Get PR changes
  const changes = await github.getPR(prNumber);
  
  // Claude review
  const review = await claude.ask(`
    Review this PR for:
    1. Code quality and style
    2. Security vulnerabilities
    3. Performance implications
    4. Test coverage
    5. Documentation updates
    
    ${changes}
  `);
  
  // Post review
  await github.postReview(prNumber, review);
  
  // Run automated checks
  const checks = await runAutomatedChecks(changes);
  
  return { review, checks };
};
```

### Continuous Learning

```javascript
// Learn from codebase patterns
Memory.store("patterns/learned", {
  "api-structure": analyzeAPIPatterns(),
  "error-handling": analyzeErrorPatterns(),
  "testing-style": analyzeTestPatterns(),
  "naming-conventions": analyzeNamingPatterns()
});

// Apply learned patterns
claude> Following the patterns I've learned from this codebase,
        create a new API endpoint for user preferences
```

## Integration Recipes

### Slack Integration

```javascript
// Send Claude insights to Slack
const slackIntegration = {
  dailyReport: async () => {
    const usage = await ccusage.getDaily();
    const tasks = await TodoRead();
    
    const report = await claude.ask(
      `Summarize today's development progress and costs`
    );
    
    await slack.post({
      channel: '#engineering',
      text: report,
      attachments: [usage, tasks]
    });
  }
};
```

### JIRA Integration

```javascript
// Sync with JIRA
claude> Based on JIRA ticket PROJ-123:
  1. Implement the feature
  2. Create tests
  3. Update documentation
  4. Comment on ticket with progress
```

### Monitoring Integration

```yaml
# Datadog integration
./claude-flow mcp add datadog \
  -e DD_API_KEY=$DD_API_KEY \
  -e DD_APP_KEY=$DD_APP_KEY \
  -- mcp-server-datadog

# Use in Claude
claude> Analyze Datadog metrics for performance anomalies
claude> Create alert for high error rates
```

## Advanced Patterns

### Self-Improving Workflows

```javascript
// Track success metrics
Memory.store("metrics/workflow-success", {
  "tdd-success-rate": 0.95,
  "bug-fix-time": "2.5 hours average",
  "code-quality-score": 8.5
});

// Adjust strategies based on metrics
if (metrics.bugFixTime > 3) {
  // Use more thorough analysis
  strategy = "deep-analysis";
}
```

### Adaptive Agent Allocation

```javascript
// Dynamic agent allocation based on task
function determineAgentCount(task) {
  const complexity = analyzeComplexity(task);
  const urgency = checkUrgency(task);
  
  if (complexity > 8 && urgency > 8) {
    return { count: 10, mode: "parallel" };
  } else if (complexity > 5) {
    return { count: 5, mode: "hierarchical" };
  } else {
    return { count: 3, mode: "centralized" };
  }
}
```

## Conclusion

These examples and recipes demonstrate the versatility and power of Claude Code with MCP integration. The key to success is:

1. **Start Simple**: Begin with basic commands and gradually increase complexity
2. **Use Memory**: Persist important information across sessions
3. **Leverage Swarms**: Use multi-agent orchestration for complex tasks
4. **Monitor Usage**: Keep track of costs and optimize accordingly
5. **Iterate**: Continuously improve your workflows based on results

For more examples, join the community and share your own recipes!

---

[← Back: Troubleshooting](14-troubleshooting.md) | [Main](../README.md) | [Next: API & SDK Reference →](16-api-sdk-reference.md)