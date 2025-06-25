# Development Workflows

[← Back: Multi-Agent Orchestration](08-multi-agent-orchestration.md) | [Main](../README.md) | [Next: Enterprise Integration →](10-enterprise-integration.md)

---

## Overview

This guide covers Anthropic's officially recommended development workflows that maximize productivity with Claude Code. These workflows are based on extensive research and real-world usage patterns from Anthropic's engineering team.

**Core Philosophy:** Claude Code is designed to be flexible and unopinionated, allowing you to adapt these workflows to your specific needs.

## Core Workflows

### 1. Explore-Plan-Code-Commit Workflow (Anthropic's Primary Recommendation)

Anthropic's core recommended workflow for complex features. Research and planning are crucial—without them, Claude tends to jump straight to coding.

**Official Anthropic Steps:**
1. **Explore**: Read relevant files and understand context
2. **Plan**: Use think modes for deeper analysis and create implementation plan
3. **Code**: Write code following the plan
4. **Commit**: Commit changes with clear, descriptive messages

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

**Anthropic's favorite workflow** for changes easily verifiable with tests. Particularly effective when requirements are clear and testable.

```bash
# Start TDD session
./claude-flow sparc tdd "User registration with email verification"
```

**Official Anthropic TDD Cycle:**
1. **Write failing test first**
   ```javascript
   describe('User Registration', () => {
     it('should send verification email', async () => {
       const result = await registerUser(userData);
       expect(emailService.send).toHaveBeenCalled();
       expect(result.success).toBe(true);
     });
   });
   ```

2. **Confirm test fails initially** (red)
3. **Implement minimal code to pass** (green)
4. **Refactor while keeping tests green**
5. **Commit incrementally**
6. **Repeat cycle**

**Key Anthropic Insight:** This workflow ensures clear specifications before implementation and provides immediate feedback on correctness.

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

### 3. Visual Iteration Approach (Anthropic Recommended for UI Work)

**Essential for effective UI development** - Anthropic strongly recommends this for any visual work.

```bash
# Step 1: Capture current state
claude> Take screenshot of current dashboard layout

# Step 2: Provide visual mock or design
claude> Here's the desired layout [attach mockup image]
        Implement this design using React and Tailwind CSS

# Step 3: Implement changes
claude> Update the dashboard component with new layout

# Step 4: Visual validation
claude> Take screenshot of implemented changes
        Compare with original mockup

# Step 5: Iterate based on visual feedback
claude> Adjust spacing and colors based on comparison
        Fine-tune responsive behavior
        Take final screenshot to confirm
```

**Benefits:**
- Immediate visual validation
- Reduces miscommunication about UI requirements
- Faster iteration cycles
- Documents the evolution of the interface
- Easier to spot layout and styling issues

### 4. Multi-Claude Strategy (Advanced Anthropic Technique)

**For complex projects requiring parallel development:**

```bash
# Terminal 1: Main feature development
git worktree add ../project-auth feature/authentication
cd ../project-auth
claude> Implement authentication system

# Terminal 2: Database migrations (parallel)
git worktree add ../project-db feature/database-updates
cd ../project-db  
claude> Create and test database migrations

# Terminal 3: Frontend components (parallel)
git worktree add ../project-ui feature/ui-components
cd ../project-ui
claude> Build React components for new features

# Terminal 4: Main branch (hotfixes)
claude> Fix critical production issues on main branch
```

**Key Advantages:**
- Multiple Claude instances work simultaneously
- No context switching between branches
- Parallel development of independent features
- Main branch stays stable for hotfixes

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
    priority: "high"
  }
]);
```

### 2. Memory-Driven Workflows (Anthropic's Context Management)

**Essential for maintaining context across sessions:**

```javascript
// Store workflow state with Anthropic's recommended structure
Memory.store("workflow/current", {
  stage: "implementation",
  completedSteps: ["research", "design"],
  nextSteps: ["coding", "testing"],
  blockers: [],
  context: "Working on user authentication feature",
  decisions: ["Use JWT tokens", "PostgreSQL for user storage"],
  timestamp: new Date().toISOString()
});

// Resume workflow later with full context
const state = Memory.get("workflow/current");
claude> Resuming work on ${state.context}.
        Completed: ${state.completedSteps.join(', ')}
        Next: ${state.nextSteps.join(', ')}
        Previous decisions: ${state.decisions.join(', ')}
```

**Anthropic's Context Management Best Practices:**
- Use `/clear` to reset context window when above 80%
- Store important context in Memory before clearing
- Provide specific, detailed instructions for context continuity
- Use subagents for complex problems requiring focused context

### 3. Checkpoint System

```bash
# Create checkpoints at key stages
Memory.store("checkpoint/after-design", {
  timestamp: new Date(),
  artifacts: ["api-design.md", "database-schema.sql"],
  decisions: ["Use JWT", "PostgreSQL for persistence"]
});
```

### 4. Parallel vs Sequential (Anthropic Guidelines)

**Use Parallel for:**
- Independent components
- Multiple file operations  
- Research tasks
- Test creation
- UI component development
- Documentation generation
- Code analysis tasks

**Use Sequential for:**
- Dependent operations
- Database migrations
- Deployment steps
- Critical fixes
- Authentication flows
- Payment processing
- Security implementations

**Anthropic's Advanced Technique:**
```bash
# Leverage headless mode for automation
./claude-flow sparc run researcher "API best practices" --headless
./claude-flow sparc run architect "Design API structure" --headless
# Results stored in Memory for next phase
```

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

## Measuring Workflow Efficiency (Anthropic's Approach)

**Key Metrics to Track:**
- Time to completion
- Token usage efficiency
- Number of iterations required
- Error rate and debugging time
- Context window utilization

```python
# Track workflow metrics with Anthropic's recommended approach
def track_workflow_metrics(workflow_name):
    start_time = time.time()
    tokens_start = get_token_count()
    context_start = get_context_usage()
    
    # Execute workflow
    execute_workflow(workflow_name)
    
    # Calculate comprehensive metrics
    duration = time.time() - start_time
    tokens_used = get_token_count() - tokens_start
    context_used = get_context_usage() - context_start
    
    Memory.store(f"metrics/{workflow_name}", {
        "duration": duration,
        "tokens": tokens_used,
        "context_efficiency": context_used / tokens_used,
        "cost": calculate_cost(tokens_used),
        "workflow_type": determine_workflow_type(workflow_name),
        "success_rate": calculate_success_rate(),
        "iterations": count_iterations(),
        "timestamp": new Date().toISOString()
    })

# Anthropic's efficiency optimization tips
def optimize_workflow_efficiency():
    """Based on Anthropic's research:
    - Use think modes for complex reasoning
    - Provide clear, specific instructions
    - Iterate multiple times for best results
    - Leverage visual feedback for UI work
    - Use Memory for context persistence
    """
    pass
```

## Next Steps

- Explore [Enterprise Integration](10-enterprise-integration.md) patterns
- Learn about [Cost Management](11-cost-management.md)
- Review [Best Practices](12-best-practices.md)

---

[← Back: Multi-Agent Orchestration](08-multi-agent-orchestration.md) | [Main](../README.md) | [Next: Enterprise Integration →](10-enterprise-integration.md)