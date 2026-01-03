Research Document
Multi-Tenant SaaS Platform Architecture

Project Name: Multi-Tenant SaaS Project Management System
Date: January 1, 2026
Status: Approved for Implementation
Author: Student Developer

1. Introduction

This research document analyzes architectural decisions involved in designing a multi-tenant SaaS platform.
The focus is on tenant isolation strategies, technology selection, and security considerations required to build a scalable and secure SaaS application.

A key challenge in SaaS systems is enabling multiple organizations (tenants) to share the same application infrastructure while ensuring strict data isolation and performance stability.

2. Multi-Tenancy Architecture Analysis

Multi-tenancy refers to the ability of a single application instance to serve multiple independent customer organizations.
The most critical architectural decision in such systems is the database isolation strategy, as it directly impacts cost, scalability, security, and operational complexity.

Three common approaches were evaluated.

2.1 Shared Database, Shared Schema

(Discriminator Column Model)

In this approach, all tenants share the same database and schema.
Data isolation is enforced using a discriminator column such as tenant_id.

How It Works

Every tenant-owned table includes a tenant_id column

All queries are scoped using tenant_id

Isolation is enforced at the application layer

Advantages

Lowest infrastructure cost

Fast tenant onboarding

Simple DevOps and deployment

Easy cross-tenant analytics

Limitations

Risk of data leaks if queries are not properly scoped

Complex per-tenant backup strategy

Potential noisy-neighbor performance issues

2.2 Shared Database, Separate Schemas

(Schema-per-Tenant Model)

Each tenant has a dedicated schema within the same database.

How It Works

Database schema is selected dynamically per request

Queries are automatically schema-isolated

Advantages

Better isolation than shared schema

Easier per-tenant backups

Allows limited tenant customization

Limitations

Schema migrations become complex at scale

Increased catalog overhead

Slower deployments with many tenants

2.3 Separate Databases

(Database-per-Tenant Model)

Each tenant has a fully isolated database instance.

How It Works

A routing layer maps tenants to database connections

Advantages

Maximum isolation and security

No noisy-neighbor effect

Suitable for highly regulated environments

Limitations

High infrastructure and maintenance cost

Complex DevOps workflows

Poor fit for freemium or MVP-stage products

2.4 Comparison Summary
Aspect	Shared Schema	Separate Schema	Separate Database
Isolation	Low	Medium	High
Cost Efficiency	High	Medium	Low
Onboarding Speed	Instant	Fast	Slow
DevOps Complexity	Low	Medium	High
Data Leak Risk	High	Medium	Very Low
Maintenance Effort	Easy	Hard	Very Hard
Scalability	Vertical	Vertical	Horizontal
2.5 Selected Approach: Shared Database, Shared Schema

Rationale

Best suited for MVP and academic evaluation

Works efficiently with ORM-based access patterns

Simplifies Docker-based deployments

Tenant isolation is enforced centrally via middleware, reducing human error

3. Technology Stack Evaluation
3.1 Backend – Node.js with Express

Reasons for Selection

Non-blocking I/O enables high concurrency

Middleware architecture simplifies tenant isolation

Unified language across frontend and backend

Alternatives Considered

Django (synchronous request handling)

Spring Boot (heavy runtime and configuration)

3.2 Frontend – React (Vite)

Reasons for Selection

Component-based architecture

Fast builds and hot reload via Vite

Large ecosystem for routing and state management

Alternative Considered

Angular (steeper learning curve)

3.3 Database – PostgreSQL

Reasons for Selection

Strong relational integrity

Cascade delete support

Advanced indexing and JSONB support

Compatible with future Row-Level Security (RLS)

Alternative Considered

MongoDB (weaker relational guarantees)

3.4 Authentication – JSON Web Tokens (JWT)

Reasons for Selection

Stateless authentication

Works across subdomains

No shared session store required

Alternative Considered

Server-side sessions (requires Redis or similar)

3.5 Deployment – Docker & Docker Compose

Reasons for Selection

Environment consistency

One-command deployment

Simple service orchestration

4. Security Architecture
4.1 Tenant Isolation via Middleware

JWT validated on every request

tenant_id extracted from token payload

Controllers never trust client-provided tenant identifiers

4.2 Secure Password Handling

Passwords hashed using Bcrypt

Minimum of 10 salt rounds

Protects against brute-force and rainbow table attacks

4.3 Role-Based Access Control (RBAC)
Role	Access Scope
Super Admin	Platform-wide access
Tenant Admin	Full tenant-level control
User	Limited operational access

Authorization checks are enforced before database access.

4.4 API Security Hardening

Strict CORS configuration

Rate limiting on authentication endpoints

Secure HTTP headers using Helmet

4.5 Query-Level Data Isolation

All tenant-owned data queries are scoped using tenant_id:

SELECT *
FROM tasks
WHERE id = :taskId
AND tenant_id = :jwtTenantId;


If the record does not belong to the tenant, the API returns 404 Not Found, ensuring foreign tenant data remains invisible.

5. Authentication & Authorization Flow

User Login
User submits credentials along with tenant identifier (subdomain).

Tenant Resolution
Backend resolves tenant and validates user within that tenant.

JWT Generation
A signed JWT is issued containing:

{
  "userId": "<uuid>",
  "tenantId": "<uuid>",
  "role": "<role>"
}


Authenticated Requests
JWT is sent in the Authorization header for all protected requests.

Token Verification
Middleware validates token signature and attaches claims to request context.

Authorization (RBAC)
Role-based guards verify permissions before executing business logic.

6. Request Lifecycle (Sequence Overview)
sequenceDiagram
    autonumber
    participant Client
    participant API
    participant Auth as Auth Middleware
    participant RBAC as RBAC Guard
    participant DB as PostgreSQL

    Client->>API: Login request
    API->>DB: Validate tenant and user
    API-->>Client: JWT issued

    Client->>API: Authenticated request
    API->>Auth: Verify JWT
    Auth-->>API: Claims attached
    API->>RBAC: Check permissions
    RBAC-->>API: Access granted
    API->>DB: Execute tenant-scoped query
    DB-->>API: Return data
    API-->>Client: Response


    Security Considerations

Security is a core design principle of the Multi-Tenant SaaS Platform. Since multiple organizations share the same infrastructure, strict controls are enforced at every layer to prevent unauthorized access, data leakage, and privilege escalation.

1️⃣ Authentication Security (JWT-Based)

The platform uses JSON Web Tokens (JWT) for stateless authentication.

Tokens are issued only after successful credential validation

JWT payload includes only non-sensitive identifiers:

{
  "userId": "<uuid>",
  "tenantId": "<uuid | null>",
  "role": "<role>"
}


Tokens are signed using a strong secret key

Token expiration is enforced (24 hours)

Benefits:

No server-side session storage required

Scales horizontally without shared state

Works across subdomains and containers

2️⃣ Secure Password Storage

All user passwords are securely stored using Bcrypt hashing.

Minimum 10 salt rounds

Plaintext passwords are never stored or logged

Password comparison is done using secure hash comparison

This protects against:

Database leaks

Rainbow table attacks

Brute-force credential reuse

3️⃣ Role-Based Access Control (RBAC)

The system enforces strict RBAC using middleware guards.

Role	Permissions
super_admin	Global tenant access
tenant_admin	Full control within tenant
user	Limited project/task access

Authorization checks occur before business logic

Unauthorized requests return 403 Forbidden

Role escalation attempts are blocked

This ensures least-privilege access across the platform.

4️⃣ Tenant Data Isolation

Tenant isolation is enforced at the application and query level.

Every tenant-owned table includes a tenant_id

All queries are scoped using tenant_id

Client-provided tenant identifiers are never trusted

Example:

SELECT * FROM projects
WHERE id = :projectId
AND tenant_id = :jwtTenantId;


If data belongs to another tenant, the API returns:

404 Not Found


This prevents attackers from inferring the existence of foreign tenant data.

5️⃣ Super Admin Isolation

Super Admin users:

Have tenant_id = NULL

Do not belong to any tenant

Can view all tenants but cannot access tenant-specific data implicitly

This design prevents accidental data coupling between system-level and tenant-level users.

6️⃣ Input Validation & Injection Prevention

All request payloads are validated at the backend

Required fields, data types, and formats are enforced

Prisma ORM uses parameterized queries

This eliminates:

SQL Injection

Malformed input attacks

Trust on frontend validation

7️⃣ API Hardening

Additional security measures include:

CORS restrictions using environment-based origins

Rate limiting on authentication endpoints

Secure HTTP headers via middleware

Consistent error responses (no stack traces exposed)

8️⃣ Docker & Environment Security

Environment variables are injected via Docker Compose

Secrets are not hardcoded into source files

Containers run in isolated Docker networks

Database access is restricted to backend service only

9️⃣ Audit Logging (Security Visibility)

All critical actions are logged:

User creation and deletion

Project and task updates

Role changes

Audit logs include:

Action type

Entity affected

Tenant context

Timestamp

This enables:

Incident investigation

Compliance review

Accountability tracking