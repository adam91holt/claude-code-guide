# Extended Thinking Modes

[← Back: MCP Protocol](05-mcp-protocol.md) | [Main](../README.md) | [Next: SPARC Modes →](07-sparc-modes.md)

---

## Overview

> **⚠️ Experimental Feature**: The trigger phrases documented below are not officially confirmed by Anthropic and may not work as described. This feature is based on community observations and may vary by Claude version.

Extended thinking modes may provide Claude with additional computation time for complex problem-solving. The following trigger phrases have been observed by users but are not officially documented.

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

## Visual Feedback (Unconfirmed)

Some users report seeing italic gray text during extended thinking, though this behavior is not officially documented:

```
_Analyzing the problem... considering scalability patterns... 
evaluating data consistency requirements... checking latency constraints..._

Based on my analysis, here's a comprehensive solution...
```

## Alternative Approaches

Instead of relying on unconfirmed trigger phrases, consider these proven methods:

1. **Clear Instructions**: Explicitly ask Claude to "think step by step" or "analyze thoroughly"
2. **Structured Prompts**: Break complex problems into smaller parts
3. **Iterative Refinement**: Ask follow-up questions to deepen analysis
4. **Use Official Features**: Leverage Claude's confirmed capabilities like code analysis and multi-file understanding

## Original Best Practices (If Features Work)

1. **Reserve for Complexity**: Use extended thinking for genuinely complex problems
2. **Be Specific**: Provide clear problem statements
3. **Tool Integration**: Claude can use tools during thinking
4. **Cost Awareness**: Extended thinking uses more tokens

---

[← Back: MCP Protocol](05-mcp-protocol.md) | [Main](../README.md) | [Next: SPARC Modes →](07-sparc-modes.md)