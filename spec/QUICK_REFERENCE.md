# Quick Reference Guide

## Agent Quick Reference

### When to Use Which Agent

#### 🏗️ Starting a New Project
1. **Backend Architecture Agent** - System design
2. **UI/UX Design Agent** - Interface design
3. **Database Agent** - Schema design
4. **Documentation Agent** - Project documentation

#### 🔧 Building Features
1. **API Development Agent** - Backend endpoints
2. **Component Development Agent** - UI components
3. **State Management Agent** - Application state
4. **Testing Agent** - Test suites

#### 🐛 Fixing Bugs
1. **Testing Agent** - Reproduce issue
2. **Backend/Frontend Agent** - Implement fix
3. **Security Agent** - Verify security
4. **Testing Agent** - Prevent regression

#### ⚡ Optimizing Performance
1. **Performance Optimization Agent** - Identify bottlenecks
2. **Database Agent** - Query optimization
3. **Build & Bundle Agent** - Bundle optimization
4. **Testing Agent** - Performance tests

#### 🔒 Security Review
1. **Security Agent** - Audit code
2. **Authentication Agent** - Auth review
3. **Testing Agent** - Security tests
4. **Documentation Agent** - Security docs

#### 📦 Deployment
1. **DevOps Agent** - CI/CD setup
2. **Testing Agent** - E2E tests
3. **Documentation Agent** - Deployment docs
4. **Security Agent** - Pre-deploy audit

## Common Commands

### MCP Agent Invocation

```bash
# Single agent
mcp-agent run \
  --spec ./spec/backend/AGENTS.md \
  --agent api-development \
  --action generate \
  --context "REST API for blog posts"

# Multiple agents
mcp-agent run \
  --spec ./spec/backend/AGENTS.md \
  --agents "architecture,database,api-development" \
  --action design \
  --context "Blog platform with comments"

# With output file
mcp-agent run \
  --spec ./spec/frontend/AGENTS.md \
  --agent component-development \
  --output ./src/components/BlogPost.tsx \
  --context "Blog post card component"
```

### Agent Workflows

```bash
# New feature workflow
mcp-workflow run new-feature \
  --specs backend,frontend \
  --context "Add user notifications" \
  --output ./docs/notifications-design.md

# Bug fix workflow
mcp-workflow run bug-fix \
  --issue-id ISSUE-123 \
  --context "Payment processing error" \
  --priority high

# Code review workflow
mcp-workflow run code-review \
  --branch feature/notifications \
  --agents "security,performance,testing"
```

## Agent Cheat Sheet

### Backend Agents

| Agent | Use For | Output |
|-------|---------|--------|
| Architecture | System design, tech stack selection | Architecture docs, diagrams |
| API Development | REST/GraphQL endpoints | API code, OpenAPI specs |
| Database | Schema design, migrations | SQL migrations, models |
| Auth & Auth | Authentication, permissions | Auth middleware, guards |
| Business Logic | Domain logic, workflows | Service layer code |
| Testing | Test suites | Unit, integration, e2e tests |
| Performance | Optimization, profiling | Performance reports |
| DevOps | CI/CD, deployment | Pipeline configs, IaC |
| Documentation | Technical docs | README, API docs |
| Security | Security audit, fixes | Security reports, fixes |

### Frontend Agents

| Agent | Use For | Output |
|-------|---------|--------|
| UI/UX Design | Interface design, wireframes | Mockups, design specs |
| Components | React/Vue components | Component code |
| State Management | Redux/Zustand/Context | State logic, stores |
| API Integration | Backend integration | API client code |
| Routing | Navigation, routes | Router config |
| Forms | Form handling, validation | Form components |
| Performance | Bundle optimization | Optimization configs |
| Testing | Component tests | Test suites |
| Styling | CSS, themes | Styled components |
| Accessibility | A11y compliance | ARIA implementations |
| Build | Webpack/Vite config | Build configs |
| i18n | Translations | Translation files |
| Documentation | Component docs | Storybook, docs |
| SEO | Meta tags, SEO | SEO implementations |
| Security | XSS, CSP | Security configs |

## Context Templates

### New Feature Context

```markdown
Feature: [Feature Name]

Requirements:
- [Requirement 1]
- [Requirement 2]

Constraints:
- [Constraint 1]
- [Constraint 2]

Acceptance Criteria:
- [Criteria 1]
- [Criteria 2]

Tech Stack:
- Backend: [Stack]
- Frontend: [Stack]
- Database: [DB]
```

### Bug Fix Context

```markdown
Bug: [Bug Description]

Steps to Reproduce:
1. [Step 1]
2. [Step 2]

Expected Behavior:
[Description]

Actual Behavior:
[Description]

Error Logs:
[Logs]

Environment:
- OS: [OS]
- Browser: [Browser]
- Version: [Version]
```

### Code Review Context

```markdown
Review Type: [Feature/Bug/Refactor]

Changes:
- [Change 1]
- [Change 2]

Focus Areas:
- [ ] Security
- [ ] Performance
- [ ] Tests
- [ ] Documentation

Concerns:
[Any specific concerns]
```

## Output Formats

### Code Generation

```json
{
  "language": "typescript",
  "framework": "express",
  "style": "functional",
  "testing": true,
  "documentation": true
}
```

### Documentation Generation

```json
{
  "format": "markdown",
  "include": ["api", "examples", "setup"],
  "level": "detailed"
}
```

### Architecture Design

```json
{
  "diagrams": ["system", "component", "sequence"],
  "format": "mermaid",
  "detail": "high"
}
```

## Best Practices Checklist

### Before Starting
- [ ] Understand requirements clearly
- [ ] Review existing architecture
- [ ] Select appropriate agents
- [ ] Prepare context information
- [ ] Set up MCP environment

### During Development
- [ ] Use agents iteratively
- [ ] Review agent outputs
- [ ] Validate generated code
- [ ] Write tests continuously
- [ ] Document as you go

### Before Deployment
- [ ] Run full test suite
- [ ] Security audit complete
- [ ] Performance tests passed
- [ ] Documentation updated
- [ ] Deployment plan ready

## Common Issues & Solutions

### Issue: Agent Output Not Relevant

**Solution:**
- Provide more specific context
- Include code examples
- Clarify requirements
- Use more focused agent

### Issue: Generated Code Has Bugs

**Solution:**
- Review and validate output
- Run tests immediately
- Iterate with agent
- Add specific test cases

### Issue: Performance Degradation

**Solution:**
- Use Performance Agent
- Profile the code
- Optimize queries
- Add caching

### Issue: Security Vulnerabilities

**Solution:**
- Run Security Agent
- Fix identified issues
- Add security tests
- Document mitigations

## Keyboard Shortcuts (IDE Integration)

```
Cmd/Ctrl + Shift + A  → Invoke agent
Cmd/Ctrl + Shift + D  → Generate docs
Cmd/Ctrl + Shift + T  → Generate tests
Cmd/Ctrl + Shift + R  → Code review
Cmd/Ctrl + Shift + O  → Optimize code
```

## Environment Variables

```bash
# Agent configuration
export MCP_SERVER_URL="http://localhost:3000"
export MCP_TIMEOUT="30000"
export MCP_VERBOSE="true"

# Project context
export PROJECT_NAME="my-project"
export PROJECT_TYPE="web-app"
export TECH_STACK="react,nodejs,postgres"

# Agent preferences
export AGENT_MODEL="gpt-4"
export AGENT_TEMPERATURE="0.7"
export AGENT_AUTO_TEST="true"
export AGENT_AUTO_DOC="true"
```

## File Locations

```
project/
├── spec/
│   ├── backend/AGENTS.md         # Backend agents
│   ├── frontend/AGENTS.md        # Frontend agents
│   ├── BEST_PRACTICES.md         # Best practices guide
│   ├── MCP_CONFIGURATION.md      # MCP setup guide
│   ├── PROJECT_TEMPLATE.md       # Project template
│   ├── examples/                 # Domain examples
│   │   ├── ECOMMERCE.md
│   │   ├── HEALTHCARE.md
│   │   └── SAAS.md
│   └── workflows/                # Workflow examples
│       ├── NEW_FEATURE.md
│       ├── BUG_FIX.md
│       └── CODE_REVIEW.md
└── .mcp/
    └── config.json               # MCP configuration
```

## Useful Links

- [Full Documentation](../README.md)
- [Backend Agents](./backend/AGENTS.md)
- [Frontend Agents](./frontend/AGENTS.md)
- [MCP Protocol](https://modelcontextprotocol.io)
- [Examples](./examples/)
- [Workflows](./workflows/)

## Support

For questions or issues:
1. Check documentation
2. Review examples
3. Ask your team
4. Create an issue

---

*Keep this guide handy for quick reference while developing!*
