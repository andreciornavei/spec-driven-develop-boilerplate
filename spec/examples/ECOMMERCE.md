# E-commerce Domain Example

## Project Overview

This example demonstrates how to customize the spec-driven-develop-boilerplate for an e-commerce platform.

**Project Name:** ShopFlow
**Domain:** E-commerce
**Scale:** Medium to Large (10k-100k products, 100k+ users)

## Technology Stack

### Backend
- **Language:** Node.js (TypeScript)
- **Framework:** Express.js
- **Database:** PostgreSQL (primary), Redis (caching)
- **Search:** Elasticsearch
- **Queue:** Redis Queue
- **Authentication:** JWT + OAuth2
- **Payment:** Stripe API

### Frontend
- **Framework:** React 18 + TypeScript
- **State Management:** Redux Toolkit
- **Styling:** Tailwind CSS + HeadlessUI
- **Forms:** React Hook Form + Zod
- **Build Tool:** Vite

## Domain-Specific Agents

### Backend Agents Customization

#### 1. Product Catalog Agent

**Purpose:** Manage product listings, variants, categories, and search.

**Responsibilities:**
- Design product data model with variants
- Implement product CRUD operations
- Handle product images and media
- Implement category hierarchy
- Integrate with Elasticsearch for search
- Manage product inventory

**Context Requirements:**
- Product types (simple, configurable, bundle)
- Variant options (size, color, material)
- Category structure
- Image requirements
- SEO requirements
- Search requirements

**Deliverables:**
- Product schema and migrations
- Product API endpoints
- Elasticsearch index configuration
- Image upload and processing
- Search implementation
- Product import/export tools

#### 2. Shopping Cart Agent

**Purpose:** Handle shopping cart functionality and session management.

**Responsibilities:**
- Design cart data model
- Implement cart operations (add, update, remove)
- Handle cart persistence
- Implement cart validation
- Calculate totals and discounts
- Handle abandoned cart scenarios

**Context Requirements:**
- Cart session management
- Guest vs. authenticated users
- Product availability validation
- Price calculation rules
- Discount application
- Cart expiration policy

**Deliverables:**
- Cart schema and APIs
- Cart validation logic
- Price calculation service
- Cart recovery system
- Cart analytics

#### 3. Order Management Agent

**Purpose:** Process and manage customer orders.

**Responsibilities:**
- Design order workflow
- Implement order creation
- Handle order state transitions
- Manage order history
- Generate invoices
- Handle refunds and returns

**Context Requirements:**
- Order states (pending, processing, shipped, delivered)
- Payment integration
- Inventory management
- Shipping integration
- Notification triggers
- Return policy

**Deliverables:**
- Order schema and APIs
- Order state machine
- Payment processing integration
- Shipping integration
- Invoice generation
- Return processing

#### 4. Payment Processing Agent

**Purpose:** Handle secure payment transactions.

**Responsibilities:**
- Integrate with Stripe API
- Handle payment methods
- Process payments securely
- Manage payment failures
- Handle refunds
- Store payment metadata

**Context Requirements:**
- Payment methods (card, wallet, bank)
- Currency support
- PCI compliance
- 3D Secure implementation
- Webhook handling
- Retry logic

**Deliverables:**
- Stripe integration
- Payment API endpoints
- Webhook handlers
- Payment security implementation
- Refund processing
- Payment analytics

#### 5. Inventory Management Agent

**Purpose:** Track and manage product inventory.

**Responsibilities:**
- Design inventory tracking system
- Implement stock updates
- Handle low stock alerts
- Manage multi-warehouse inventory
- Prevent overselling
- Generate inventory reports

**Context Requirements:**
- Warehouse locations
- Stock reservation rules
- Reorder thresholds
- Inventory sync frequency
- Backorder handling
- Stock allocation strategy

**Deliverables:**
- Inventory schema
- Stock management APIs
- Reservation system
- Alert system
- Inventory reports
- Sync mechanisms

### Frontend Agents Customization

#### 1. Product Display Agent

**Purpose:** Create product browsing and detail views.

**Responsibilities:**
- Implement product listing pages
- Create product detail pages
- Handle product variants selection
- Display product images/gallery
- Show reviews and ratings
- Implement quick view

**Context Requirements:**
- Design system components
- Product data structure
- Image requirements
- Variant selection UX
- Mobile responsiveness
- Performance requirements

**Deliverables:**
- Product list component
- Product detail component
- Image gallery component
- Variant selector component
- Product card component
- Quick view modal

#### 2. Shopping Cart UI Agent

**Purpose:** Create intuitive shopping cart interface.

**Responsibilities:**
- Implement cart sidebar/page
- Create add-to-cart interactions
- Display cart summary
- Handle quantity updates
- Show applied discounts
- Implement cart animations

**Context Requirements:**
- Cart design patterns
- Mobile vs. desktop UX
- Animation preferences
- Discount display
- Stock availability UI
- Error handling

**Deliverables:**
- Cart sidebar component
- Cart page component
- Cart item component
- Cart summary component
- Add-to-cart button component
- Cart animations

#### 3. Checkout Flow Agent

**Purpose:** Create seamless checkout experience.

**Responsibilities:**
- Implement multi-step checkout
- Handle address forms
- Integrate payment UI
- Show order review
- Handle form validation
- Implement progress indicator

**Context Requirements:**
- Checkout steps
- Guest checkout support
- Payment methods
- Address validation
- Mobile optimization
- Accessibility requirements

**Deliverables:**
- Checkout container component
- Address form component
- Payment form component
- Order review component
- Checkout progress component
- Confirmation page

## API Specifications

### Product API

```
GET    /api/v1/products              # List products with filters
GET    /api/v1/products/:id          # Get product details
POST   /api/v1/products              # Create product (admin)
PUT    /api/v1/products/:id          # Update product (admin)
DELETE /api/v1/products/:id          # Delete product (admin)
GET    /api/v1/products/search       # Search products
GET    /api/v1/products/:id/variants # Get product variants
```

### Cart API

```
GET    /api/v1/cart                  # Get current cart
POST   /api/v1/cart/items            # Add item to cart
PUT    /api/v1/cart/items/:id        # Update cart item
DELETE /api/v1/cart/items/:id        # Remove cart item
DELETE /api/v1/cart                  # Clear cart
POST   /api/v1/cart/validate         # Validate cart
```

### Order API

```
GET    /api/v1/orders                # List user orders
GET    /api/v1/orders/:id            # Get order details
POST   /api/v1/orders                # Create order
PUT    /api/v1/orders/:id/cancel     # Cancel order
GET    /api/v1/orders/:id/invoice    # Get invoice
POST   /api/v1/orders/:id/return     # Request return
```

## Database Schema

### Products Table
```sql
CREATE TABLE products (
    id UUID PRIMARY KEY,
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    compare_price DECIMAL(10, 2),
    cost_price DECIMAL(10, 2),
    category_id UUID REFERENCES categories(id),
    brand VARCHAR(100),
    weight DECIMAL(10, 2),
    is_active BOOLEAN DEFAULT true,
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE product_variants (
    id UUID PRIMARY KEY,
    product_id UUID REFERENCES products(id),
    sku VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(255),
    price DECIMAL(10, 2),
    options JSONB,
    stock_quantity INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE categories (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(255) UNIQUE NOT NULL,
    parent_id UUID REFERENCES categories(id),
    description TEXT,
    image_url VARCHAR(500),
    created_at TIMESTAMP DEFAULT NOW()
);
```

## MCP Configuration

```json
{
  "project": {
    "name": "shopflow",
    "type": "e-commerce",
    "domain": "retail"
  },
  "mcpServers": {
    "backend-ecommerce": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/backend/AGENTS.md"],
      "env": {
        "PROJECT_TYPE": "e-commerce",
        "TECH_STACK": "nodejs,express,postgresql,redis",
        "DOMAIN_AGENTS": "product-catalog,shopping-cart,order-management,payment,inventory"
      }
    },
    "frontend-ecommerce": {
      "command": "mcp-server",
      "args": ["--spec", "./spec/frontend/AGENTS.md"],
      "env": {
        "PROJECT_TYPE": "e-commerce",
        "TECH_STACK": "react,typescript,redux,tailwind",
        "DOMAIN_AGENTS": "product-display,cart-ui,checkout-flow"
      }
    }
  }
}
```

## Development Workflow

### Phase 1: Foundation (Week 1-2)
1. **Backend Architecture Agent** - Design system architecture
2. **Database Agent** - Design database schema
3. **API Development Agent** - Define API specifications
4. **UI/UX Design Agent** - Create design system and wireframes

### Phase 2: Core Features (Week 3-6)
1. **Product Catalog Agent** - Implement product management
2. **Product Display Agent** - Build product UI components
3. **Shopping Cart Agent** - Implement cart backend
4. **Cart UI Agent** - Build cart interface
5. **Testing Agent** - Create test suites

### Phase 3: Checkout & Payment (Week 7-9)
1. **Order Management Agent** - Implement order processing
2. **Payment Processing Agent** - Integrate Stripe
3. **Checkout Flow Agent** - Build checkout UI
4. **Security Agent** - Audit payment flow
5. **Testing Agent** - Test checkout flow

### Phase 4: Inventory & Admin (Week 10-11)
1. **Inventory Management Agent** - Build inventory system
2. **Component Development Agent** - Build admin UI
3. **Documentation Agent** - Create admin docs

### Phase 5: Optimization & Launch (Week 12)
1. **Performance Optimization Agent** - Optimize backend & frontend
2. **Security Agent** - Final security audit
3. **Testing Agent** - Comprehensive testing
4. **DevOps Agent** - Setup deployment
5. **Documentation Agent** - Final documentation

## Success Metrics

- **Performance:** Page load < 2s, API response < 200ms
- **Availability:** 99.9% uptime
- **Security:** PCI compliant, regular security audits
- **User Experience:** < 3 clicks to purchase
- **Test Coverage:** > 80% for critical paths

---

*This example demonstrates the full customization of the boilerplate for a real-world e-commerce project.*
