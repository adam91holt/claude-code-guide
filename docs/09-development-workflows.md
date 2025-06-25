# Development Workflows

[← Back: Multi-Agent Orchestration](08-multi-agent-orchestration.md) | [Main](../README.md) | [Next: Enterprise Integration →](10-enterprise-integration.md)

---

## Overview

This guide covers proven development workflows that maximize productivity with Claude Code. Each workflow is designed for specific scenarios and can be combined for complex projects.

## Core Workflows

### 1. Research-Plan-Implement Workflow

The most effective approach for complex features. Research and planning are crucial—without them, Claude tends to jump straight to coding.

```bash
# Step 1: Research
./claude-flow sparc run researcher "Research best practices for JWT authentication"

# Step 2: Plan Architecture  
./claude-flow sparc run architect "Design authentication system based on research"

# Step 3: Implement
./claude-flow sparc run coder "Implement JWT authentication with refresh tokens"

# Step 4: Test
./claude-flow sparc run tester "Create comprehensive test suite"

# Step 5: Document
./claude-flow sparc run documenter "Generate API documentation"
```

### 2. Test-Driven Development (TDD)

Anthropic's favorite workflow for changes easily verifiable with tests.

```bash
# Start TDD session
./claude-flow sparc tdd "User registration with email verification"
```

**TDD Cycle:**
1. **Write failing test**
   ```javascript
   describe('User Registration', () => {
     it('should send verification email', async () => {
       const result = await registerUser(userData);
       expect(emailService.send).toHaveBeenCalled();
     });
   });
   ```

2. **Implement minimal code**
3. **Make test pass**
4. **Refactor**
5. **Repeat**

### 3. Bug Investigation Workflow

For debugging complex issues:

```bash
# Step 1: Analyze with extended thinking
claude> The app crashes after 1000 concurrent connections. 
        Think hard about potential causes and diagnostic approaches.

# Step 2: Investigate with debugger
./claude-flow sparc run debugger "Investigate connection limit crash"

# Step 3: Implement fix
./claude-flow sparc run coder "Fix connection pooling issue"

# Step 4: Verify fix
./claude-flow sparc run tester "Test concurrent connection handling"
```

### 4. Refactoring Workflow

For improving existing code:

```bash
# Analyze current state
./claude-flow sparc run analyzer "Analyze UserService for refactoring opportunities"

# Plan refactoring
Memory.store("refactoring/plan", analysisResults);
./claude-flow sparc run architect "Plan UserService refactoring"

# Execute refactoring
./claude-flow swarm "Refactor UserService maintaining backward compatibility" \
  --strategy optimization \
  --mode centralized \
  --max-agents 4
```

### 5. Feature Development Workflow

Complete feature implementation:

```bash
# Launch feature swarm
./claude-flow swarm "Add real-time notifications with WebSockets" \
  --strategy development \
  --mode hierarchical \
  --max-agents 6 \
  --monitor

# Agents will handle:
# - WebSocket server setup
# - Client-side integration
# - Message queue implementation
# - Database schema updates
# - Testing
# - Documentation
```

## Specialized Workflows

### API Development Workflow

```bash
# 1. Design API
./claude-flow sparc run architect "Design RESTful API for blog platform"

# 2. Generate OpenAPI spec
claude> Generate OpenAPI 3.0 specification based on the design

# 3. Implement endpoints
./claude-flow swarm "Implement blog API endpoints" \
  --strategy development \
  --parallel

# 4. Generate tests from spec
claude> Generate integration tests from OpenAPI specification

# 5. Create documentation
./claude-flow sparc run documenter "Create interactive API docs"
```

### Database Migration Workflow

```bash
# 1. Analyze current schema
claude> Analyze current database schema and identify migration needs

# 2. Plan migration
./claude-flow sparc run architect "Plan database migration strategy"

# 3. Generate migration scripts
claude> Generate migration scripts with rollback capability

# 4. Test migration
./claude-flow sparc run tester "Test database migration on staging"

# 5. Execute migration
claude> Execute migration with monitoring and rollback readiness
```

### Security Review Workflow

```bash
# 1. Security audit
./claude-flow swarm "Comprehensive security audit" \
  --strategy analysis \
  --mode mesh \
  --max-agents 5

# 2. Prioritize findings
claude> Prioritize security findings by severity and impact

# 3. Fix vulnerabilities
./claude-flow swarm "Fix critical security vulnerabilities" \
  --strategy maintenance \
  --mode centralized

# 4. Verify fixes
./claude-flow sparc run tester "Security regression tests"
```

## Workflow Combinations

### Full-Stack Feature Workflow

```bash
# Backend + Frontend + Database
./claude-flow swarm "Build user dashboard feature" \
  --strategy development \
  --mode hierarchical \
  --config fullstack.json

# fullstack.json configuration:
{
  "agents": {
    "backend": ["api", "database", "auth"],
    "frontend": ["ui", "state", "routing"],
    "integration": ["testing", "deployment"]
  }
}
```

### Microservices Workflow

```bash
# 1. Design services
./claude-flow sparc run architect "Design microservices for e-commerce"

# 2. Implement services in parallel
./claude-flow swarm "Implement microservices" \
  --strategy development \
  --mode distributed \
  --max-agents 8

# 3. Integration testing
./claude-flow sparc run tester "Integration tests for microservices"
```

## Best Practices

### 1. Use TodoWrite for Workflow Tracking

```javascript
TodoWrite([
  {
    id: "research",
    content: "Research authentication best practices",
    status: "completed",
    priority: "high"
  },
  {
    id: "design",
    content: "Design authentication architecture",
    status: "in_progress",
    priority: "high"
  },
  {
    id: "implement",
    content: "Implement authentication system",
    status: "pending",
    priority: "high",
    dependencies: ["design"]
  }
]);
```

### 2. Memory-Driven Workflows

```javascript
// Store workflow state
Memory.store("workflow/current", {
  stage: "implementation",
  completedSteps: ["research", "design"],
  nextSteps: ["coding", "testing"],
  blockers: []
});

// Resume workflow later
const state = Memory.get("workflow/current");
```

### 3. Checkpoint System

```bash
# Create checkpoints at key stages
Memory.store("checkpoint/after-design", {
  timestamp: new Date(),
  artifacts: ["api-design.md", "database-schema.sql"],
  decisions: ["Use JWT", "PostgreSQL for persistence"]
});
```

### 4. Parallel vs Sequential

**Use Parallel for:**
- Independent components
- Multiple file operations
- Research tasks
- Test creation

**Use Sequential for:**
- Dependent operations
- Database migrations
- Deployment steps
- Critical fixes

## Workflow Templates

### Quick Feature Template

```bash
#!/bin/bash
# feature-workflow.sh

FEATURE_NAME="$1"

# Research phase
./claude-flow sparc run researcher "Best practices for $FEATURE_NAME"

# Design phase
./claude-flow sparc run architect "Design $FEATURE_NAME architecture"

# Implementation phase
./claude-flow sparc tdd "$FEATURE_NAME implementation"

# Documentation
./claude-flow sparc run documenter "Document $FEATURE_NAME"

echo "Feature $FEATURE_NAME completed!"
```

### Daily Development Template

```bash
# Morning: Review and plan
./claude-flow sparc run reviewer "Review yesterday's code"
TodoWrite([...todaysTasks])

# Development: Implement features
./claude-flow swarm "Today's development tasks" \
  --strategy development \
  --parallel

# Evening: Test and document
./claude-flow sparc run tester "Test today's changes"
Memory.store("daily/progress", progressSummary)
```

## Measuring Workflow Efficiency

```python
# Track workflow metrics
def track_workflow_metrics(workflow_name):
    start_time = time.time()
    tokens_start = get_token_count()
    
    # Execute workflow
    execute_workflow(workflow_name)
    
    # Calculate metrics
    duration = time.time() - start_time
    tokens_used = get_token_count() - tokens_start
    
    Memory.store(f"metrics/{workflow_name}", {
        "duration": duration,
        "tokens": tokens_used,
        "cost": calculate_cost(tokens_used),
        "efficiency": calculate_efficiency(duration, tokens_used)
    })
```

## Next Steps

- Explore [Enterprise Integration](10-enterprise-integration.md) patterns
- Learn about [Cost Management](11-cost-management.md)
- Review [Best Practices](12-best-practices.md)

---

[← Back: Multi-Agent Orchestration](08-multi-agent-orchestration.md) | [Main](../README.md) | [Next: Enterprise Integration →](10-enterprise-integration.md)