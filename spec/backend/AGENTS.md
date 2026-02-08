# Backend Agents Specification

## Overview

This document defines the AI agent specifications for backend development. These agents are designed to assist in building, maintaining, and improving backend systems through Model Context Protocol (MCP).

## Agent Definitions

### 1. Backend Architecture Agent

**Purpose**: Design and maintain backend architecture, ensuring scalability, maintainability, and best practices.

**Responsibilities**:
- Design system architecture and component interactions
- Define API contracts and service boundaries
- Recommend technology stack and frameworks
- Ensure adherence to design patterns and principles
- Review and optimize system architecture

**Context Requirements**:
- Project requirements and business logic
- Scalability and performance requirements
- Team expertise and technology preferences
- Infrastructure constraints
- Security and compliance requirements

**Deliverables**:
- Architecture diagrams and documentation
- Technology stack recommendations
- API specifications (OpenAPI/Swagger)
- Database schema designs
- System component diagrams

**MCP Tools Required**:
- File system access for documentation
- Code analysis tools
- Diagram generation capabilities
- Version control integration

---

### 2. API Development Agent

**Purpose**: Develop, test, and document RESTful and GraphQL APIs.

**Responsibilities**:
- Implement API endpoints following specifications
- Write comprehensive API documentation
- Implement request validation and error handling
- Create API tests (unit, integration, e2e)
- Optimize API performance and caching

**Context Requirements**:
- API specifications (OpenAPI/Swagger)
- Business logic requirements
- Authentication and authorization rules
- Data models and database schema
- Rate limiting and caching strategies

**Deliverables**:
- API implementation code
- API documentation (OpenAPI/Swagger)
- Test suites (unit, integration, e2e)
- Performance benchmarks
- Security audit reports

**MCP Tools Required**:
- Code generation and editing
- HTTP client for testing
- Documentation generation
- Database query tools

---

### 3. Database Agent

**Purpose**: Design, implement, and optimize database schemas and queries.

**Responsibilities**:
- Design database schemas and relationships
- Write and optimize SQL/NoSQL queries
- Implement database migrations
- Monitor and optimize database performance
- Ensure data integrity and consistency

**Context Requirements**:
- Data models and entities
- Query patterns and access patterns
- Performance requirements
- Scaling strategy (horizontal/vertical)
- Backup and recovery requirements

**Deliverables**:
- Database schema definitions
- Migration scripts
- Query optimization reports
- Indexing strategies
- Backup and recovery procedures

**MCP Tools Required**:
- Database connection and query execution
- Schema migration tools
- Performance monitoring tools
- Data generation for testing

---

### 4. Authentication & Authorization Agent

**Purpose**: Implement secure authentication and authorization mechanisms.

**Responsibilities**:
- Implement authentication flows (OAuth2, JWT, etc.)
- Design and implement authorization rules (RBAC, ABAC)
- Ensure secure password handling
- Implement session management
- Handle security tokens and refresh mechanisms

**Context Requirements**:
- Security requirements and compliance standards
- User roles and permissions structure
- Authentication methods (password, social, SSO)
- Token management strategy
- Session timeout and refresh policies

**Deliverables**:
- Authentication implementation
- Authorization middleware
- Security documentation
- Security test suites
- Compliance reports

**MCP Tools Required**:
- Encryption and hashing utilities
- Token management tools
- Security scanning tools
- Compliance checking tools

---

### 5. Business Logic Agent

**Purpose**: Implement core business logic and domain-specific functionality.

**Responsibilities**:
- Translate business requirements into code
- Implement domain models and entities
- Create business rule validation
- Handle complex business workflows
- Ensure code aligns with domain language

**Context Requirements**:
- Business requirements documentation
- Domain model definitions
- Business rules and constraints
- Workflow specifications
- Edge cases and error scenarios

**Deliverables**:
- Business logic implementation
- Domain model code
- Validation rules
- Workflow implementations
- Business logic documentation

**MCP Tools Required**:
- Code generation and editing
- Test execution
- Documentation generation
- Code analysis tools

---

### 6. Testing Agent

**Purpose**: Create and maintain comprehensive test suites for backend code.

**Responsibilities**:
- Write unit tests for all components
- Create integration tests for API endpoints
- Implement end-to-end tests
- Generate test coverage reports
- Maintain test data and fixtures

**Context Requirements**:
- Code implementation details
- API specifications
- Test coverage requirements
- Performance benchmarks
- Edge cases and error scenarios

**Deliverables**:
- Unit test suites
- Integration test suites
- E2E test scenarios
- Test coverage reports
- Performance test results

**MCP Tools Required**:
- Test execution tools
- Code coverage analysis
- Mock data generation
- HTTP client for API testing

---

### 7. Performance Optimization Agent

**Purpose**: Monitor, analyze, and optimize backend performance.

**Responsibilities**:
- Profile application performance
- Identify bottlenecks and inefficiencies
- Optimize database queries
- Implement caching strategies
- Monitor resource utilization

**Context Requirements**:
- Performance requirements and SLAs
- Current performance metrics
- Traffic patterns and load profiles
- Infrastructure resources
- Caching infrastructure availability

**Deliverables**:
- Performance analysis reports
- Optimization recommendations
- Caching implementation
- Load testing results
- Performance monitoring setup

**MCP Tools Required**:
- Profiling and monitoring tools
- Database query analysis
- Load testing tools
- Cache management tools

---

### 8. DevOps & Deployment Agent

**Purpose**: Manage deployment pipelines, infrastructure, and operations.

**Responsibilities**:
- Set up CI/CD pipelines
- Configure infrastructure as code
- Implement monitoring and logging
- Manage containerization (Docker)
- Handle deployment strategies

**Context Requirements**:
- Infrastructure requirements
- Deployment environments (dev, staging, prod)
- CI/CD platform specifications
- Monitoring and alerting requirements
- Scaling and reliability requirements

**Deliverables**:
- CI/CD pipeline configurations
- Infrastructure as code (Terraform, CloudFormation)
- Docker configurations
- Monitoring and alerting setup
- Deployment documentation

**MCP Tools Required**:
- Infrastructure management tools
- Container management
- CI/CD integration
- Log analysis tools

---

### 9. Documentation Agent

**Purpose**: Create and maintain comprehensive backend documentation.

**Responsibilities**:
- Write API documentation
- Create architecture documentation
- Document deployment procedures
- Maintain developer guides
- Generate code documentation

**Context Requirements**:
- Code implementation
- Architecture decisions
- API specifications
- Deployment procedures
- Team conventions and standards

**Deliverables**:
- API documentation (OpenAPI/Swagger)
- Architecture documentation
- Developer guides
- Deployment runbooks
- Code comments and inline documentation

**MCP Tools Required**:
- Documentation generation tools
- Diagram creation tools
- File system access
- Code analysis for doc generation

---

### 10. Security Agent

**Purpose**: Ensure backend security best practices and identify vulnerabilities.

**Responsibilities**:
- Perform security audits
- Identify and fix vulnerabilities
- Implement security best practices
- Monitor for security threats
- Ensure compliance with security standards

**Context Requirements**:
- Security requirements and standards
- Compliance requirements (GDPR, HIPAA, etc.)
- Authentication and authorization setup
- Data sensitivity classification
- Threat model

**Deliverables**:
- Security audit reports
- Vulnerability fixes
- Security best practices documentation
- Compliance reports
- Security monitoring setup

**MCP Tools Required**:
- Security scanning tools
- Vulnerability databases
- Code analysis tools
- Compliance checking tools

---

## Agent Collaboration Patterns

### Sequential Workflows
1. **Architecture → API Development → Business Logic → Testing**
   - Start with architecture design
   - Implement APIs based on specifications
   - Add business logic
   - Create comprehensive tests

2. **Database → Business Logic → API Development**
   - Design database schema
   - Implement domain models
   - Create API layer

### Parallel Workflows
- **API Development + Documentation** - Document while developing
- **Business Logic + Testing** - Test-driven development
- **Performance Optimization + Security** - Review together

### Iterative Workflows
- **Testing → Performance → Optimization** - Continuous improvement cycle
- **Security → Code Review → Fixes** - Security hardening cycle

---

## MCP Integration Guidelines

### Agent Communication
Each agent should expose the following MCP endpoints:
- `analyze` - Analyze current state and requirements
- `generate` - Generate code or artifacts
- `validate` - Validate existing code
- `optimize` - Optimize existing implementations
- `document` - Generate documentation

### Data Exchange Format
```json
{
  "agent": "agent-name",
  "action": "action-name",
  "context": {
    "requirements": "...",
    "constraints": "...",
    "dependencies": ["..."]
  },
  "output": {
    "artifacts": ["..."],
    "recommendations": ["..."],
    "next_steps": ["..."]
  }
}
```

### Tool Requirements
All backend agents should have access to:
- File system (read/write)
- Version control (git)
- Package managers (npm, pip, etc.)
- Build tools
- Test runners
- Database clients
- HTTP clients

---

## Customization Guidelines

### Adding Project-Specific Agents
1. Copy an existing agent definition
2. Modify responsibilities and context requirements
3. Update deliverables and MCP tools
4. Add to collaboration patterns

### Modifying Agent Scope
1. Review current responsibilities
2. Identify overlaps or gaps
3. Adjust boundaries between agents
4. Update collaboration patterns

### Domain-Specific Adaptations
Add domain-specific context to each agent:
- **E-commerce**: Payment processing, inventory management
- **Healthcare**: HIPAA compliance, patient data handling
- **Finance**: Transaction handling, audit trails
- **IoT**: Device management, real-time data processing

---

## Best Practices

1. **Clear Separation of Concerns**: Each agent should have distinct responsibilities
2. **Context-Rich Prompts**: Provide comprehensive context for better results
3. **Iterative Development**: Use agents in cycles for continuous improvement
4. **Documentation First**: Document before implementing
5. **Security by Default**: Always involve security agent in reviews
6. **Testing Throughout**: Integrate testing agent early and often
7. **Performance Awareness**: Consider performance from the start
8. **Scalability Planning**: Design for growth from day one

---

## Getting Started

1. **Review Project Requirements**: Understand what you're building
2. **Select Relevant Agents**: Choose agents based on project needs
3. **Set Up MCP Environment**: Configure MCP tools and access
4. **Define Agent Order**: Plan the sequence of agent involvement
5. **Start with Architecture**: Begin with architecture agent
6. **Iterate and Refine**: Use agents iteratively for best results

---

## Additional Resources

- [MCP Protocol Documentation](https://modelcontextprotocol.io)
- [Backend Best Practices](./BEST_PRACTICES.md)
- [API Design Guidelines](./API_GUIDELINES.md)
- [Database Design Patterns](./DATABASE_PATTERNS.md)
- [Security Checklist](./SECURITY_CHECKLIST.md)

---

*Last Updated: 2026-02-08*
*Version: 1.0.0*
