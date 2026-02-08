# MCP Server Configuration Example

This configuration file demonstrates how to expose the agent specifications via MCP (Model Context Protocol).

## Configuration File: `.mcp/config.json`

```json
{
  "version": "1.0.0",
  "name": "spec-driven-develop-boilerplate",
  "description": "AI agent specifications for backend and frontend development",
  "mcpVersion": "1.0",
  
  "servers": {
    "backend-agents": {
      "type": "specification",
      "enabled": true,
      "spec": "./spec/backend/AGENTS.md",
      "name": "Backend Development Agents",
      "description": "10 specialized agents for backend development",
      "agents": [
        "backend-architecture",
        "api-development",
        "database",
        "authentication-authorization",
        "business-logic",
        "testing",
        "performance-optimization",
        "devops-deployment",
        "documentation",
        "security"
      ],
      "capabilities": [
        "system-design",
        "code-generation",
        "testing",
        "documentation",
        "security-audit"
      ]
    },
    
    "frontend-agents": {
      "type": "specification",
      "enabled": true,
      "spec": "./spec/frontend/AGENTS.md",
      "name": "Frontend Development Agents",
      "description": "15 specialized agents for frontend development",
      "agents": [
        "ui-ux-design",
        "component-development",
        "state-management",
        "api-integration",
        "routing-navigation",
        "forms-validation",
        "performance-optimization",
        "testing",
        "styling-theming",
        "accessibility",
        "build-bundle",
        "internationalization",
        "documentation",
        "seo-meta",
        "security"
      ],
      "capabilities": [
        "ui-design",
        "component-development",
        "testing",
        "accessibility",
        "performance-optimization"
      ]
    }
  },
  
  "tools": {
    "file-system": {
      "enabled": true,
      "permissions": ["read", "write"],
      "paths": ["./src", "./docs", "./tests"]
    },
    "version-control": {
      "enabled": true,
      "provider": "git"
    },
    "package-manager": {
      "enabled": true,
      "managers": ["npm", "yarn", "pnpm", "pip"]
    },
    "test-runner": {
      "enabled": true,
      "runners": ["jest", "vitest", "pytest", "playwright"]
    },
    "build-tools": {
      "enabled": true,
      "tools": ["webpack", "vite", "rollup", "esbuild"]
    }
  },
  
  "context": {
    "project": {
      "name": "${PROJECT_NAME}",
      "type": "${PROJECT_TYPE}",
      "domain": "${PROJECT_DOMAIN}"
    },
    "techStack": {
      "backend": {
        "language": "${BACKEND_LANGUAGE}",
        "framework": "${BACKEND_FRAMEWORK}",
        "database": "${DATABASE}"
      },
      "frontend": {
        "framework": "${FRONTEND_FRAMEWORK}",
        "language": "${FRONTEND_LANGUAGE}",
        "styling": "${STYLING_FRAMEWORK}"
      }
    }
  },
  
  "workflows": {
    "new-feature": {
      "spec": "./spec/workflows/NEW_FEATURE.md",
      "agents": [
        "backend-architecture",
        "ui-ux-design",
        "api-development",
        "component-development",
        "testing",
        "documentation"
      ]
    },
    "bug-fix": {
      "spec": "./spec/workflows/BUG_FIX.md",
      "agents": [
        "testing",
        "security",
        "performance-optimization",
        "documentation"
      ]
    }
  },
  
  "examples": {
    "ecommerce": {
      "spec": "./spec/examples/ECOMMERCE.md",
      "enabled": true
    }
  }
}
```

## Environment Variables

Create a `.env` file for project-specific configuration:

```bash
# Project Configuration
PROJECT_NAME=my-awesome-project
PROJECT_TYPE=web-application
PROJECT_DOMAIN=ecommerce

# Backend Stack
BACKEND_LANGUAGE=typescript
BACKEND_FRAMEWORK=express
DATABASE=postgresql

# Frontend Stack
FRONTEND_FRAMEWORK=react
FRONTEND_LANGUAGE=typescript
STYLING_FRAMEWORK=tailwind

# MCP Configuration
MCP_SERVER_URL=http://localhost:3000
MCP_TIMEOUT=30000
MCP_VERBOSE=true

# Agent Configuration
AGENT_MODEL=gpt-4
AGENT_TEMPERATURE=0.7
AGENT_MAX_TOKENS=2000
AGENT_AUTO_TEST=true
AGENT_AUTO_DOC=true
```

## Server Startup

### Option 1: Using MCP CLI

```bash
# Install MCP CLI (if not already installed)
npm install -g @modelcontextprotocol/cli

# Start MCP server with configuration
mcp-server start --config .mcp/config.json

# Or with environment file
mcp-server start --config .mcp/config.json --env .env
```

### Option 2: Using Node.js

```javascript
// server.js
const { MCPServer } = require('@modelcontextprotocol/server');
const path = require('path');

const server = new MCPServer({
  config: path.join(__dirname, '.mcp/config.json'),
  env: path.join(__dirname, '.env')
});

server.start(3000).then(() => {
  console.log('MCP Server running on http://localhost:3000');
  console.log('Agent specifications loaded:');
  console.log('- Backend agents: 10');
  console.log('- Frontend agents: 15');
});
```

### Option 3: Using Docker

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy spec files
COPY spec/ ./spec/
COPY .mcp/ ./.mcp/

# Install MCP server
RUN npm install -g @modelcontextprotocol/server

# Expose port
EXPOSE 3000

# Start server
CMD ["mcp-server", "start", "--config", ".mcp/config.json"]
```

```bash
# Build and run
docker build -t mcp-agents .
docker run -p 3000:3000 mcp-agents
```

## Client Configuration

### VS Code Extension

Add to `.vscode/settings.json`:

```json
{
  "mcp.servers": [
    {
      "name": "Project Agents",
      "url": "http://localhost:3000",
      "enabled": true
    }
  ],
  "mcp.agents.autoSuggest": true,
  "mcp.agents.inlineHelp": true
}
```

### CLI Usage

```bash
# List available agents
mcp-client list-agents --server http://localhost:3000

# Invoke an agent
mcp-client invoke \
  --server http://localhost:3000 \
  --agent backend-architecture \
  --action analyze \
  --context "Design a blog platform API"

# Run a workflow
mcp-client workflow \
  --server http://localhost:3000 \
  --workflow new-feature \
  --input "Add user authentication"
```

## API Endpoints

When the MCP server is running, these endpoints are available:

### List Agents
```
GET /api/agents
```

Response:
```json
{
  "backend": [
    {
      "id": "backend-architecture",
      "name": "Backend Architecture Agent",
      "description": "Design and maintain backend architecture"
    }
  ],
  "frontend": [
    {
      "id": "component-development",
      "name": "Component Development Agent",
      "description": "Build reusable UI components"
    }
  ]
}
```

### Invoke Agent
```
POST /api/agents/{agentId}/invoke
```

Request:
```json
{
  "action": "generate",
  "context": {
    "requirements": "Build a REST API for blog posts",
    "constraints": {
      "framework": "Express.js",
      "database": "PostgreSQL"
    }
  }
}
```

### Get Agent Spec
```
GET /api/agents/{agentId}/spec
```

### Health Check
```
GET /health
```

## Integration Examples

### GitHub Actions

```yaml
name: AI Agent Review

on: [pull_request]

jobs:
  agent-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Start MCP Server
        run: |
          npm install -g @modelcontextprotocol/cli
          mcp-server start --config .mcp/config.json &
          sleep 5
      
      - name: Run Security Review
        run: |
          mcp-client invoke \
            --agent security \
            --action audit \
            --context "branch: ${{ github.head_ref }}"
```

### Pre-commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Start MCP server if not running
if ! curl -s http://localhost:3000/health > /dev/null; then
  mcp-server start --config .mcp/config.json &
  sleep 5
fi

# Run security check
echo "Running security check..."
mcp-client invoke \
  --agent security \
  --action validate \
  --context "staged files"

# Run tests generation check
echo "Checking test coverage..."
mcp-client invoke \
  --agent testing \
  --action validate-coverage
```

## Monitoring

### Health Monitoring

```bash
# Check server health
curl http://localhost:3000/health

# Get server metrics
curl http://localhost:3000/metrics
```

### Logging

Configure logging in `.mcp/config.json`:

```json
{
  "logging": {
    "level": "info",
    "format": "json",
    "outputs": [
      {
        "type": "file",
        "path": "./logs/mcp-server.log"
      },
      {
        "type": "console"
      }
    ]
  }
}
```

## Security

### Authentication

Add authentication to `.mcp/config.json`:

```json
{
  "security": {
    "authentication": {
      "enabled": true,
      "type": "bearer-token",
      "tokens": [
        {
          "name": "dev-team",
          "token": "${MCP_DEV_TOKEN}"
        }
      ]
    }
  }
}
```

### CORS Configuration

```json
{
  "cors": {
    "enabled": true,
    "origins": [
      "http://localhost:3000",
      "https://your-domain.com"
    ]
  }
}
```

## Troubleshooting

### Server Won't Start

```bash
# Check if port is in use
lsof -i :3000

# Check configuration
mcp-server validate --config .mcp/config.json

# Start with verbose logging
mcp-server start --config .mcp/config.json --verbose
```

### Agent Not Found

```bash
# List all available agents
mcp-client list-agents

# Verify spec file
cat spec/backend/AGENTS.md | grep "### 1."
```

---

*This configuration makes the boilerplate ready for delivery via MCP.*
