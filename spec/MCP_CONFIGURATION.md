# MCP Configuration Guide

## Overview

This guide explains how to configure the Model Context Protocol (MCP) to use the agent specifications in this boilerplate.

## Prerequisites

- MCP-compatible AI assistant or tool
- Access to the spec files in this repository
- Understanding of your project requirements

## Configuration Options

### Option 1: Direct File Reference

Configure your MCP client to reference the AGENTS.md files directly:

```json
{
  "mcpServers": {
    "backend-agents": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/backend/AGENTS.md"],
      "description": "Backend development agents"
    },
    "frontend-agents": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/frontend/AGENTS.md"],
      "description": "Frontend development agents"
    }
  }
}
```

### Option 2: Combined Configuration

Use both backend and frontend agents together:

```json
{
  "mcpServers": {
    "development-agents": {
      "command": "mcp-server",
      "args": [
        "--spec", "./spec/backend/AGENTS.md",
        "--spec", "./spec/frontend/AGENTS.md"
      ],
      "description": "Full-stack development agents"
    }
  }
}
```

### Option 3: Custom Agent Selection

Select specific agents for your project:

```json
{
  "mcpServers": {
    "api-development": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/backend/AGENTS.md"],
      "filters": ["api-development", "database", "testing"],
      "description": "API-focused agents"
    },
    "ui-development": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/frontend/AGENTS.md"],
      "filters": ["component-development", "styling", "accessibility"],
      "description": "UI-focused agents"
    }
  }
}
```

## Agent Invocation

### Basic Agent Call

```
@agent backend-architecture "Design a RESTful API for a blog platform"
```

### Agent with Context

```
@agent api-development "Implement the blog post CRUD endpoints" \
  --context "database: PostgreSQL, framework: Express.js, auth: JWT"
```

### Multi-Agent Workflow

```
@workflow new-feature "Add blog commenting system"
  --agents "backend-architecture, api-development, database, testing"
  --context "existing: blog-posts-api, users-api"
```

## Environment Variables

Configure these environment variables for optimal agent performance:

```bash
# Project context
export PROJECT_NAME="my-project"
export PROJECT_TYPE="web-application"
export TECH_STACK="react,nodejs,postgresql"

# Agent preferences
export AGENT_VERBOSE=true
export AGENT_AUTO_DOC=true
export AGENT_TEST_COVERAGE=80

# MCP configuration
export MCP_SERVER_URL="http://localhost:3000"
export MCP_TIMEOUT=30000
```

## Integration with IDEs

### VS Code Configuration

Add to `.vscode/settings.json`:

```json
{
  "mcp.agents.enabled": true,
  "mcp.agents.specs": [
    "./spec/backend/AGENTS.md",
    "./spec/frontend/AGENTS.md"
  ],
  "mcp.agents.autoSuggest": true,
  "mcp.agents.inlineHelp": true
}
```

### IntelliJ/WebStorm Configuration

Add to `.idea/mcp.xml`:

```xml
<component name="MCPConfiguration">
  <specs>
    <spec path="spec/backend/AGENTS.md" />
    <spec path="spec/frontend/AGENTS.md" />
  </specs>
  <options>
    <option name="autoSuggest" value="true" />
    <option name="inlineHelp" value="true" />
  </options>
</component>
```

## CI/CD Integration

### GitHub Actions

```yaml
name: AI-Assisted Development

on: [push, pull_request]

jobs:
  agent-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup MCP
        run: npm install -g @modelcontextprotocol/cli
      
      - name: Run Security Agent
        run: |
          mcp-agent run \
            --spec ./spec/backend/AGENTS.md \
            --agent security \
            --action audit \
            --context "pr: ${{ github.event.pull_request.number }}"
      
      - name: Run Testing Agent
        run: |
          mcp-agent run \
            --spec ./spec/backend/AGENTS.md \
            --agent testing \
            --action validate-coverage
```

### GitLab CI

```yaml
stages:
  - review
  - test

agent-security-review:
  stage: review
  script:
    - mcp-agent run --spec ./spec/backend/AGENTS.md --agent security --action audit

agent-test-generation:
  stage: test
  script:
    - mcp-agent run --spec ./spec/backend/AGENTS.md --agent testing --action generate
```

## Advanced Configuration

### Custom Agent Parameters

```json
{
  "mcpServers": {
    "backend-agents": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/backend/AGENTS.md"],
      "env": {
        "AGENT_MODEL": "gpt-4",
        "AGENT_TEMPERATURE": "0.7",
        "AGENT_MAX_TOKENS": "2000"
      }
    }
  }
}
```

### Agent Overrides

Override specific agent behaviors:

```json
{
  "agentOverrides": {
    "backend-architecture": {
      "context": {
        "preferred_patterns": ["microservices", "event-driven"],
        "constraints": ["aws-only", "serverless-preferred"]
      }
    },
    "database": {
      "deliverables": {
        "additional": ["data-migration-scripts", "seed-data"]
      }
    }
  }
}
```

## Troubleshooting

### Agent Not Found

If an agent is not found:
1. Verify the spec file path is correct
2. Check that the agent name matches the specification
3. Ensure MCP server is running

### Context Not Loading

If agent context is not loading:
1. Verify file permissions
2. Check for syntax errors in spec files
3. Ensure all referenced files exist

### Performance Issues

If agents are slow:
1. Reduce context size
2. Use specific agents instead of all agents
3. Increase timeout values
4. Cache frequently used contexts

## Best Practices

1. **Start Simple** - Begin with basic configuration and add complexity
2. **Use Environment Variables** - Keep configuration flexible
3. **Version Control** - Commit your MCP configuration files
4. **Document Custom Settings** - Explain project-specific configurations
5. **Regular Updates** - Keep agent specifications up to date
6. **Monitor Usage** - Track which agents are most valuable
7. **Feedback Loop** - Continuously improve agent specifications

## Example Workflows

### New Feature Development

```bash
# 1. Design phase
mcp-agent run --spec ./spec/backend/AGENTS.md \
  --agent backend-architecture \
  --action analyze \
  --input "Add user notification system"

# 2. Database design
mcp-agent run --spec ./spec/backend/AGENTS.md \
  --agent database \
  --action generate \
  --context "notifications: in-app, email, push"

# 3. API implementation
mcp-agent run --spec ./spec/backend/AGENTS.md \
  --agent api-development \
  --action generate \
  --context "RESTful API for notifications"

# 4. Testing
mcp-agent run --spec ./spec/backend/AGENTS.md \
  --agent testing \
  --action generate \
  --context "notification API tests"
```

### Code Review

```bash
# Run multiple review agents
mcp-agent run --spec ./spec/backend/AGENTS.md \
  --agent security,performance,testing \
  --action validate \
  --context "branch: feature/notifications"
```

## Additional Resources

- [MCP Protocol Specification](https://modelcontextprotocol.io/spec)
- [Agent Development Guide](https://modelcontextprotocol.io/docs/agents)
- [MCP CLI Documentation](https://modelcontextprotocol.io/docs/cli)

---

*Last Updated: 2026-02-08*
