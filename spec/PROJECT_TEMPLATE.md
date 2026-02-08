# Project Template Guide

## Overview

This guide helps you adapt the spec-driven-develop-boilerplate for your specific project. Follow these steps to customize the agent specifications and documentation for your domain.

## Quick Start Checklist

- [ ] Copy this boilerplate to your project repository
- [ ] Review backend and frontend AGENTS.md files
- [ ] Identify relevant agents for your project
- [ ] Add domain-specific context to agent specifications
- [ ] Configure MCP integration
- [ ] Test agent interactions
- [ ] Document your customizations

## Customization Steps

### Step 1: Project Assessment

Answer these questions to guide your customization:

**Project Type:**
- [ ] Web Application
- [ ] Mobile Application
- [ ] Desktop Application
- [ ] API/Backend Service
- [ ] CLI Tool
- [ ] Library/Package
- [ ] Microservices
- [ ] Monolithic Application

**Primary Domain:**
- [ ] E-commerce
- [ ] Healthcare
- [ ] Finance
- [ ] Education
- [ ] Social Media
- [ ] SaaS
- [ ] Gaming
- [ ] IoT
- [ ] Other: ___________

**Technology Stack:**

Backend:
- Language: ___________
- Framework: ___________
- Database: ___________
- Authentication: ___________
- Hosting: ___________

Frontend:
- Framework: ___________
- State Management: ___________
- Styling: ___________
- Build Tool: ___________
- UI Library: ___________

### Step 2: Select Relevant Agents

Review both AGENTS.md files and select agents you need:

**Backend Agents:**
```
Essential:
- [ ] Backend Architecture Agent
- [ ] API Development Agent
- [ ] Database Agent
- [ ] Testing Agent
- [ ] Documentation Agent

Optional:
- [ ] Authentication & Authorization Agent
- [ ] Business Logic Agent
- [ ] Performance Optimization Agent
- [ ] DevOps & Deployment Agent
- [ ] Security Agent
```

**Frontend Agents:**
```
Essential:
- [ ] Component Development Agent
- [ ] State Management Agent
- [ ] Testing Agent
- [ ] Documentation Agent

Optional:
- [ ] UI/UX Design Agent
- [ ] API Integration Agent
- [ ] Routing & Navigation Agent
- [ ] Forms & Validation Agent
- [ ] Performance Optimization Agent
- [ ] Styling & Theming Agent
- [ ] Accessibility Agent
- [ ] Build & Bundle Agent
- [ ] Internationalization Agent
- [ ] SEO & Meta Agent
- [ ] Security Agent
```

### Step 3: Add Domain-Specific Context

For each selected agent, add your domain-specific requirements:

#### Example: E-commerce Project

**Backend Architecture Agent - Additional Context:**
```markdown
### E-commerce Specific Context
- Payment gateway integration (Stripe, PayPal)
- Inventory management system
- Order processing workflows
- Shopping cart persistence
- Product catalog with variants
- Customer account management
- Shipping and fulfillment integration
```

**API Development Agent - Additional Context:**
```markdown
### E-commerce API Endpoints
- Product APIs (CRUD, search, filters)
- Cart APIs (add, update, checkout)
- Order APIs (create, track, history)
- Payment APIs (process, refund)
- User APIs (profile, addresses, preferences)
- Admin APIs (inventory, reports, analytics)
```

#### Example: Healthcare Project

**Security Agent - Additional Context:**
```markdown
### Healthcare Compliance Requirements
- HIPAA compliance mandatory
- PHI (Protected Health Information) handling
- Audit logging for all data access
- Encryption at rest and in transit
- Role-based access control (RBAC)
- Patient consent management
- Data retention policies
```

### Step 4: Define Technology Stack Specifics

Update agent specifications with your chosen technologies:

**Backend Example (Node.js + Express + PostgreSQL):**
```markdown
### Technology Stack
- **Language**: Node.js (v18+)
- **Framework**: Express.js
- **Database**: PostgreSQL 14+
- **ORM**: Prisma
- **Authentication**: JWT + Passport.js
- **Testing**: Jest + Supertest
- **API Docs**: Swagger/OpenAPI 3.0
```

**Frontend Example (React + TypeScript):**
```markdown
### Technology Stack
- **Framework**: React 18+
- **Language**: TypeScript 5+
- **State**: Redux Toolkit
- **Routing**: React Router v6
- **Styling**: Tailwind CSS
- **Forms**: React Hook Form + Zod
- **Testing**: Jest + React Testing Library
- **Build**: Vite
```

### Step 5: Create Project-Specific Templates

Add templates for common project artifacts:

#### API Endpoint Template
```markdown
## API Endpoint: [Name]

**URL**: `/api/v1/[resource]`
**Method**: GET/POST/PUT/DELETE
**Auth**: Required/Optional

### Request
- Headers: [...]
- Body: [...]
- Query Params: [...]

### Response
- Success (200): [...]
- Error (4xx/5xx): [...]

### Business Logic
- [Step 1]
- [Step 2]
- [Step 3]

### Validation Rules
- [Rule 1]
- [Rule 2]

### Database Operations
- [Query 1]
- [Query 2]
```

#### Component Template
```markdown
## Component: [Name]

**Purpose**: [Description]
**Category**: Atom/Molecule/Organism/Template/Page

### Props
- prop1: type - description
- prop2: type - description

### State
- state1: type - description
- state2: type - description

### Events
- onEvent1: callback - description
- onEvent2: callback - description

### Styling
- Base classes: [...]
- Variants: [...]
- Responsive: [...]

### Accessibility
- ARIA labels: [...]
- Keyboard navigation: [...]
- Screen reader: [...]
```

### Step 6: Define Agent Workflows

Create workflow diagrams for common scenarios:

**New Feature Workflow:**
```
1. Product Manager → Requirements
2. Backend Architecture Agent → System Design
3. UI/UX Design Agent → Interface Design
4. Database Agent → Schema Design
5. Backend API Agent → API Implementation
6. Frontend Component Agent → UI Implementation
7. Testing Agent (Backend + Frontend) → Test Suites
8. Documentation Agent → Documentation
9. Security Agent → Security Review
10. DevOps Agent → Deployment
```

**Bug Fix Workflow:**
```
1. Issue Reported → Analysis
2. Testing Agent → Reproduce Bug
3. Backend/Frontend Agent → Fix Implementation
4. Testing Agent → Verify Fix
5. Security Agent → Security Check
6. Documentation Agent → Update Docs
7. DevOps Agent → Deploy Fix
```

### Step 7: Configure MCP Integration

Create your project's MCP configuration:

```json
{
  "project": {
    "name": "my-project",
    "type": "web-application",
    "domain": "e-commerce"
  },
  "mcpServers": {
    "backend": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/backend/AGENTS.md"],
      "env": {
        "PROJECT_TYPE": "e-commerce",
        "TECH_STACK": "nodejs,express,postgresql"
      }
    },
    "frontend": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/frontend/AGENTS.md"],
      "env": {
        "PROJECT_TYPE": "e-commerce",
        "TECH_STACK": "react,typescript,tailwind"
      }
    }
  }
}
```

### Step 8: Document Customizations

Create a PROJECT_AGENTS.md file documenting your customizations:

```markdown
# Project Agent Specifications

## Project: [Name]
## Domain: [Domain]
## Tech Stack: [Stack]

## Customized Agents

### Backend Agents
- Architecture Agent: [Customizations]
- API Agent: [Customizations]
- ...

### Frontend Agents
- Component Agent: [Customizations]
- State Agent: [Customizations]
- ...

## Domain-Specific Agents

### [Custom Agent Name]
**Purpose**: [Description]
**Responsibilities**: [List]
**Context Requirements**: [List]
**Deliverables**: [List]
```

## Domain-Specific Templates

### E-commerce Template

```markdown
## E-commerce Specific Agents

### Payment Processing Agent
- Stripe/PayPal integration
- Payment method management
- Refund processing
- Payment reconciliation

### Inventory Management Agent
- Stock tracking
- Reorder alerts
- Warehouse management
- Product variants

### Order Fulfillment Agent
- Order processing
- Shipping integration
- Tracking updates
- Return handling
```

### Healthcare Template

```markdown
## Healthcare Specific Agents

### HIPAA Compliance Agent
- PHI data protection
- Audit logging
- Access controls
- Encryption standards

### Patient Data Agent
- Medical records management
- Consent management
- Data anonymization
- Record retention

### Appointment Management Agent
- Scheduling system
- Reminder system
- Calendar integration
- Provider availability
```

### SaaS Template

```markdown
## SaaS Specific Agents

### Multi-Tenancy Agent
- Tenant isolation
- Tenant management
- Data segregation
- Tenant-specific config

### Subscription Management Agent
- Plan management
- Billing cycles
- Usage tracking
- Payment processing

### Analytics Agent
- Usage analytics
- Feature adoption
- User behavior
- Revenue metrics
```

## Validation Checklist

After customization, verify:

- [ ] All agents have clear responsibilities
- [ ] Domain context is added to relevant agents
- [ ] Technology stack is documented
- [ ] MCP configuration is complete
- [ ] Templates are created for common artifacts
- [ ] Workflows are defined
- [ ] Customizations are documented
- [ ] Team members can understand and use the specs

## Maintenance

Keep your agent specifications up to date:

1. **Regular Reviews**: Review every sprint/month
2. **Feedback Loop**: Gather team feedback on agent effectiveness
3. **Evolve with Project**: Update as project grows
4. **Version Control**: Track changes to agent specs
5. **Share Learnings**: Document what works well

## Examples

See the [examples](./examples/) directory for complete customization examples:
- E-commerce project
- Healthcare application
- SaaS platform
- Mobile app backend
- Content management system

---

*Last Updated: 2026-02-08*
