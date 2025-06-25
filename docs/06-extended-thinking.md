# Extended Thinking Modes

[← Back: MCP Protocol](05-mcp-protocol.md) | [Main](../README.md) | [Next: SPARC Modes →](07-sparc-modes.md)

---

## Overview

Extended thinking modes provide Claude with additional computation time for complex problem-solving. Different trigger phrases allocate progressively more thinking budget.

## Thinking Levels

| Trigger Phrase | Computation Level | Best Use Cases |
|----------------|-------------------|----------------|
| `think about` | Basic | Simple problems, quick analysis |
| `think hard about` | Extended | Architecture decisions, complex bugs |
| `think harder about` | Deep | Algorithm design, system optimization |
| `ultrathink about` | Maximum | Critical decisions, complex architectures |

## Usage Examples

### Basic Thinking
```
claude> Think about the best way to structure this React component.
```

### Extended Thinking
```
claude> Think hard about potential security vulnerabilities in this authentication system.
```

### Deep Thinking
```
claude> Think harder about optimizing this algorithm for millions of concurrent users.
```

### Maximum Thinking
```
claude> Ultrathink about designing a distributed system architecture for global scale.
```

## Visual Feedback

During extended thinking, you'll see italic gray text showing Claude's reasoning process:

```
_Analyzing the problem... considering scalability patterns... 
evaluating data consistency requirements... checking latency constraints..._

Based on my analysis, here's a comprehensive solution...
```

## Best Practices

1. **Reserve for Complexity**: Use extended thinking for genuinely complex problems
2. **Be Specific**: Provide clear problem statements
3. **Tool Integration**: Claude can use tools during thinking
4. **Cost Awareness**: Extended thinking uses more tokens

---

[← Back: MCP Protocol](05-mcp-protocol.md) | [Main](../README.md) | [Next: SPARC Modes →](07-sparc-modes.md)