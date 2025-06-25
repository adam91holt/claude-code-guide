# Enterprise Integration

[← Back: Development Workflows](09-development-workflows.md) | [Main](../README.md) | [Next: Cost Management →](11-cost-management.md)

---

## Overview

This guide covers enterprise-grade integration patterns for Claude Code, including CI/CD pipelines, security compliance, team collaboration, and large-scale deployment strategies.

## CI/CD Integration

### GitHub Actions Integration

```yaml
# .github/workflows/claude-assist.yml
name: Claude Code Assistance

on:
  pull_request:
    types: [opened, synchronize]
  push:
    branches: [main, develop]

jobs:
  code-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Claude Code
        run: |
          npm install -g @anthropic/claude-cli
          echo "${{ secrets.CLAUDE_API_KEY }}" > ~/.claude/api_key
      
      - name: Automated Code Review
        run: |
          claude --headless --print "Review this PR for:
            1. Security vulnerabilities
            2. Performance issues
            3. Code quality
            4. Best practices
            Output as markdown" > review.md
      
      - name: Post Review Comment
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const review = fs.readFileSync('review.md', 'utf8');
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '## 🤖 Claude Code Review\n\n' + review
            });

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Security Analysis
        run: |
          ./claude-flow sparc run analyzer "Security audit for OWASP Top 10"
          
      - name: Upload Security Report
        uses: actions/upload-artifact@v3
        with:
          name: security-report
          path: security-audit.json
```

### GitLab CI Integration

```yaml
# .gitlab-ci.yml
stages:
  - review
  - test
  - security
  - deploy

claude-review:
  stage: review
  image: node:18
  before_script:
    - npm install -g @anthropic/claude-cli
    - echo "$CLAUDE_API_KEY" > ~/.claude/api_key
  script:
    - |
      claude --headless --print "Review changes in this MR:
        - Architecture decisions
        - Performance implications
        - Security concerns
        " > review.md
    - cat review.md
  artifacts:
    reports:
      junit: review.xml
    paths:
      - review.md

automated-testing:
  stage: test
  script:
    - ./claude-flow sparc run tester "Generate tests for new code"
    - npm test
```

### Jenkins Integration

```groovy
// Jenkinsfile
pipeline {
    agent any
    
    environment {
        CLAUDE_API_KEY = credentials('claude-api-key')
    }
    
    stages {
        stage('Code Analysis') {
            steps {
                script {
                    sh '''
                        npm install -g @anthropic/claude-cli
                        claude --headless --print "Analyze codebase for:
                          - Technical debt
                          - Refactoring opportunities
                          - Performance bottlenecks
                        " > analysis.md
                    '''
                    
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: '.',
                        reportFiles: 'analysis.md',
                        reportName: 'Claude Analysis'
                    ])
                }
            }
        }
        
        stage('Generate Documentation') {
            steps {
                sh './claude-flow sparc run documenter "Generate API documentation"'
            }
        }
    }
}
```

## Team Collaboration

### Shared Configuration

Create team-wide configuration in `.claude/team-settings.json`:

```json
{
  "team": {
    "name": "Engineering Team",
    "defaultModel": "sonnet-4",
    "sharedMemory": true,
    "costCenter": "engineering-dept"
  },
  "workflows": {
    "codeReview": {
      "requiredChecks": [
        "security",
        "performance",
        "tests",
        "documentation"
      ]
    },
    "deployment": {
      "environments": ["dev", "staging", "prod"],
      "approvalRequired": true
    }
  },
  "integrations": {
    "slack": {
      "webhook": "${SLACK_WEBHOOK}",
      "channels": {
        "alerts": "#claude-alerts",
        "reports": "#engineering"
      }
    },
    "jira": {
      "url": "https://company.atlassian.net",
      "project": "ENG"
    }
  }
}
```

### Team Memory Sharing

```javascript
// Share knowledge across team
Memory.store("team/architecture/decisions", {
  decision: "Migrate to microservices",
  rationale: "Scalability and team independence",
  date: "2025-01-15",
  approvedBy: ["CTO", "Lead Architect"]
});

// Access team knowledge
const decisions = Memory.query("team/architecture/*");
```

### Collaborative Workflows

```bash
# Team code review workflow
./claude-flow swarm "Review sprint deliverables" \
  --strategy analysis \
  --mode distributed \
  --team engineering \
  --output slack

# Collaborative debugging
./claude-flow swarm "Debug production issue #1234" \
  --strategy analysis \
  --mode mesh \
  --team sre \
  --priority urgent
```

## Security & Compliance

### Security Configuration

```json
{
  "security": {
    "encryption": {
      "atRest": true,
      "inTransit": true,
      "algorithm": "AES-256-GCM"
    },
    "authentication": {
      "method": "oauth2",
      "provider": "corporate-sso",
      "mfa": true
    },
    "audit": {
      "enabled": true,
      "logLevel": "detailed",
      "retention": "90d"
    },
    "compliance": {
      "standards": ["SOC2", "GDPR", "HIPAA"],
      "scanFrequency": "daily"
    }
  }
}
```

### Compliance Workflows

```bash
# GDPR compliance check
./claude-flow swarm "GDPR compliance audit" \
  --strategy analysis \
  --mode centralized \
  --config gdpr-compliance.json

# Security scanning
./claude-flow sparc run analyzer "Security scan for:
  - SQL injection
  - XSS vulnerabilities
  - Authentication flaws
  - Encryption weaknesses"

# Generate compliance report
./claude-flow sparc run documenter "Generate SOC2 compliance documentation"
```

### Audit Logging

```python
# Custom audit logger
class ClaudeAuditLogger:
    def __init__(self, compliance_standard="SOC2"):
        self.standard = compliance_standard
        self.log_file = f"/var/log/claude/{compliance_standard}.log"
    
    def log_action(self, action, user, details):
        entry = {
            "timestamp": datetime.utcnow().isoformat(),
            "user": user,
            "action": action,
            "details": details,
            "compliance": self.standard,
            "ip": get_client_ip(),
            "session": get_session_id()
        }
        
        # Write to immutable log
        with open(self.log_file, 'a') as f:
            f.write(json.dumps(entry) + '\n')
        
        # Send to SIEM
        send_to_siem(entry)
```

## Enterprise MCP Patterns

### Multi-Environment Configuration

```json
{
  "environments": {
    "development": {
      "mcpServers": {
        "database": {
          "command": "mcp-server-postgres",
          "args": ["--connection-string", "${DEV_DB_URL}"]
        }
      }
    },
    "staging": {
      "mcpServers": {
        "database": {
          "command": "mcp-server-postgres",
          "args": ["--connection-string", "${STAGING_DB_URL}", "--readonly"]
        }
      }
    },
    "production": {
      "mcpServers": {
        "database": {
          "command": "mcp-server-postgres",
          "args": ["--connection-string", "${PROD_DB_URL}", "--readonly"]
        },
        "monitoring": {
          "command": "mcp-server-datadog",
          "env": {
            "DD_API_KEY": "${DATADOG_API_KEY}"
          }
        }
      }
    }
  }
}
```

### Service Mesh Integration

```yaml
# kubernetes/claude-mcp-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: claude-mcp-gateway
spec:
  replicas: 3
  selector:
    matchLabels:
      app: claude-mcp
  template:
    metadata:
      labels:
        app: claude-mcp
    spec:
      containers:
      - name: mcp-gateway
        image: company/claude-mcp-gateway:latest
        env:
        - name: CLAUDE_API_KEY
          valueFrom:
            secretKeyRef:
              name: claude-secrets
              key: api-key
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
```

## Monitoring & Observability

### Prometheus Integration

```yaml
# prometheus-claude-rules.yml
groups:
  - name: claude_code_alerts
    rules:
      - alert: HighTokenUsage
        expr: rate(claude_tokens_used[5m]) > 10000
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High token usage detected"
          description: "Token usage is {{ $value }} tokens/minute"
      
      - alert: ClaudeAPIErrors
        expr: rate(claude_api_errors[5m]) > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Claude API errors detected"
          description: "Error rate is {{ $value }} errors/minute"
```

### Grafana Dashboard

```json
{
  "dashboard": {
    "title": "Claude Code Enterprise Metrics",
    "panels": [
      {
        "title": "Token Usage by Team",
        "targets": [
          {
            "expr": "sum(claude_tokens_used) by (team)"
          }
        ]
      },
      {
        "title": "Cost by Department",
        "targets": [
          {
            "expr": "sum(claude_cost_usd) by (department)"
          }
        ]
      },
      {
        "title": "API Response Time",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, claude_response_time)"
          }
        ]
      }
    ]
  }
}
```

## Scaling Strategies

### Horizontal Scaling

```bash
# Scale MCP servers
kubectl scale deployment claude-mcp-gateway --replicas=10

# Load balancing configuration
./claude-flow config set load-balancer.algorithm "round-robin"
./claude-flow config set load-balancer.health-check.interval 30s
```

### Rate Limiting

```python
# Enterprise rate limiter
class EnterpriseRateLimiter:
    def __init__(self):
        self.limits = {
            "team": 1000,      # requests per minute per team
            "user": 100,       # requests per minute per user
            "department": 5000 # requests per minute per department
        }
    
    def check_limit(self, entity_type, entity_id):
        current_rate = self.get_current_rate(entity_type, entity_id)
        limit = self.limits.get(entity_type, 100)
        
        if current_rate >= limit:
            raise RateLimitExceeded(
                f"{entity_type} {entity_id} exceeded limit of {limit} req/min"
            )
        
        return True
```

### Caching Strategy

```javascript
// Multi-tier caching
const cacheConfig = {
  tiers: [
    {
      name: "memory",
      ttl: 300,      // 5 minutes
      maxSize: "1GB"
    },
    {
      name: "redis",
      ttl: 3600,     // 1 hour
      connection: process.env.REDIS_URL
    },
    {
      name: "cdn",
      ttl: 86400,    // 24 hours
      provider: "cloudflare"
    }
  ]
};
```

## Enterprise Support

### SLA Configuration

```json
{
  "sla": {
    "availability": "99.9%",
    "responseTime": {
      "p50": "500ms",
      "p95": "2000ms",
      "p99": "5000ms"
    },
    "support": {
      "critical": "1 hour",
      "high": "4 hours",
      "medium": "1 business day",
      "low": "3 business days"
    }
  }
}
```

### Disaster Recovery

```bash
# Backup configuration
./claude-flow backup create --full
./claude-flow backup schedule --daily 02:00

# Disaster recovery test
./claude-flow dr test --scenario regional-outage

# Failover procedure
./claude-flow failover --to secondary-region --verify
```

## Best Practices

1. **Centralize Configuration**: Use configuration management tools
2. **Implement RBAC**: Role-based access control for teams
3. **Monitor Everything**: Token usage, costs, performance
4. **Automate Compliance**: Regular security scans and audits
5. **Plan for Scale**: Design for 10x current usage
6. **Document Workflows**: Maintain runbooks for all processes

## Next Steps

- Learn about [Cost Management](11-cost-management.md) at scale
- Review [Best Practices](12-best-practices.md) for enterprise
- Explore [Command Reference](13-command-reference.md)

---

[← Back: Development Workflows](09-development-workflows.md) | [Main](../README.md) | [Next: Cost Management →](11-cost-management.md)