# SPARC Development Modes

[← Back: Extended Thinking](06-extended-thinking.md) | [Main](../README.md) | [Next: Multi-Agent Orchestration →](08-multi-agent-orchestration.md)

---

## What is SPARC?

SPARC (Specification, Pseudocode, Architecture, Refinement, Completion) is a structured methodology that provides 17 specialized AI development modes for different aspects of software engineering.

## Available SPARC Modes

### Core Development Modes

1. **orchestrator** - Coordinate multiple agents for complex tasks
2. **coder** - Write production-ready code
3. **researcher** - Investigate and analyze problems
4. **tdd** - Test-driven development workflow
5. **architect** - System design and architecture

### Specialized Modes

6. **reviewer** - Code review and quality analysis
7. **debugger** - Bug investigation and fixes
8. **tester** - Comprehensive test creation
9. **analyzer** - Performance and security analysis
10. **optimizer** - Code and system optimization
11. **documenter** - Documentation generation
12. **designer** - UI/UX design assistance
13. **innovator** - Creative problem solving
14. **swarm-coordinator** - Multi-agent management
15. **memory-manager** - Knowledge persistence
16. **batch-executor** - Bulk operations
17. **workflow-manager** - Process automation

## Usage Examples

### Basic Usage
```bash
# List all modes
./claude-flow sparc modes

# Run specific mode
./claude-flow sparc run coder "Implement user authentication"

# Default orchestrator mode
./claude-flow sparc "Build REST API"
```

### TDD Mode Example
```bash
./claude-flow sparc tdd "Shopping cart functionality"

# Claude will:
# 1. Write failing tests
# 2. Implement minimal code
# 3. Make tests pass
# 4. Refactor
# 5. Repeat
```

### Architect Mode Example
```bash
./claude-flow sparc run architect "Design microservices for e-commerce"

# Outputs:
# - System architecture diagram
# - Service boundaries
# - Communication patterns
# - Technology recommendations
```

## Mode Selection Guide

| Task Type | Recommended Mode | Example |
|-----------|-----------------|---------|
| New feature | coder | "Add payment processing" |
| Bug fixing | debugger | "Fix memory leak" |
| System design | architect | "Design scalable API" |
| Code quality | reviewer | "Review authentication code" |
| Performance | optimizer | "Optimize database queries" |
| Testing | tester | "Create integration tests" |
| Research | researcher | "Evaluate frameworks" |

## Combining Modes

```bash
# Research then implement
./claude-flow sparc run researcher "Best practices for JWT"
./claude-flow sparc run coder "Implement JWT authentication"

# Design, implement, test
./claude-flow sparc run architect "Design user service"
./claude-flow sparc tdd "Implement user service"
./claude-flow sparc run tester "Integration tests for user service"
```

---

[← Back: Extended Thinking](06-extended-thinking.md) | [Main](../README.md) | [Next: Multi-Agent Orchestration →](08-multi-agent-orchestration.md)