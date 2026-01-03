System Architecture Document
Multi-Tenant SaaS Project Management System

Version: 1.0
Date: January 1, 2026
Author: Student Developer

1. Overview

This document describes the architecture, components, database design, and API structure of the Multi-Tenant SaaS Project Management System.

The system is designed using a containerized three-tier web architecture to ensure:

Scalability

Modularity

Secure tenant isolation

All services are orchestrated using Docker Compose, providing consistent behavior across development and deployment environments.

2. Architecture Design

Architecture Type: Three-Tier Web Architecture

Deployment Model: Containerized (Docker)

Multi-Tenancy Model: Shared Database, Shared Schema

Isolation Strategy: Logical isolation using tenant_id

3. High-Level Architecture
graph LR
    User[Client Browser] --> FE[Frontend - React (Vite)]
    FE --> BE[Backend - Node.js / Express]
    BE --> DB[(PostgreSQL Database)]

    BE --> JWT[JWT Authentication]
    BE --> RBAC[RBAC Authorization]

4. System Components
4.1 Client Layer (Frontend)

Technology: React.js (Vite)

Port: 3000

Responsibilities:

Renders the user interface

Handles user interactions

Stores and manages JWT authentication state

Communicates with backend REST APIs

Tenant Context Handling:

Identified via subdomain, or

Captured during login

4.2 Application Layer (Backend API)

Technology: Node.js, Express.js

Port: 5000

Responsibilities:

Business logic processing

JWT-based authentication

Role-Based Access Control (RBAC)

Tenant-level data isolation

Tenant Isolation Mechanism:

tenant_id extracted from JWT

Automatically applied to all database queries

Prevents cross-tenant data access

4.3 Data Layer (Database)

Technology: PostgreSQL 15

Port: 5432

Responsibilities:

Persistent relational data storage

Isolation Strategy:

Shared database and shared schema

Logical isolation using tenant_id

tenant_id exists in all tenant-owned tables

5. Database Design
Design Principles

Normalized to Third Normal Form (3NF)

Enforces referential integrity

Optimized for multi-tenant access patterns

Entity Relationship Overview
erDiagram
    TENANTS ||--o{ USERS : owns
    TENANTS ||--o{ PROJECTS : owns
    TENANTS ||--o{ TASKS : owns
    TENANTS ||--o{ AUDIT_LOGS : records

    USERS ||--o{ PROJECTS : creates
    PROJECTS ||--o{ TASKS : contains
    USERS ||--o{ TASKS : assigned_to

6. Schema Summary
Tenants (Root Entity)

Primary Key: id (UUID)

Fields: name, subdomain (unique), status, subscription_plan

Constraints: max_users, max_projects

Note: Root table without tenant_id

Users

Primary Key: id (UUID)

Foreign Key: tenant_id → tenants.id (Cascade delete)

Fields: email, password_hash, full_name, role

Constraint: UNIQUE (tenant_id, email)

Projects

Primary Key: id (UUID)

Foreign Keys: tenant_id, created_by

Fields: name, description, status

Index: tenant_id

Tasks

Primary Key: id (UUID)

Foreign Keys: tenant_id, project_id, assigned_to

Fields: title, priority, status, due_date

Index: tenant_id

Audit Logs

Primary Key: id (UUID)

Foreign Key: tenant_id

Fields: action, entity_type, entity_id, ip_address

7. API Architecture

API Style: RESTful

Total Endpoints: 19

Security: JWT Authentication + RBAC

Tenant Scope: Enforced at middleware level

Standard API Response Format
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}

8. API Modules Overview
Authentication

Register tenant and admin

Login / Logout

Fetch current user context

Tenant Management

List tenants

View tenant details

Update tenant configuration

User Management

Create users

List users

Update user details

Remove users

Project Management

Create projects

List projects

Update projects

Delete projects

Task Management

Create tasks

List tasks

Update task status

Update task details

9. Security Considerations

Stateless JWT authentication

Role-Based Access Control (RBAC)

Strict tenant isolation using tenant_id

Cascading deletes for data consistency

Audit logging for traceability

10. Conclusion

This architecture provides a secure, scalable, and production-ready foundation for a multi-tenant SaaS platform.
The design ensures clear separation of concerns, strong tenant isolation, and ease of deployment using containerization.


🔌 API Endpoint Overview

The backend exposes 19 RESTful API endpoints, organized by functional modules.
All endpoints enforce JWT authentication, role-based access control (RBAC), and tenant-level data isolation.

🔐 Authentication Module
Method	Endpoint	Description	Access
POST	/api/auth/register-tenant	Register a new tenant and tenant admin	Public
POST	/api/auth/login	Authenticate user and issue JWT	Public
GET	/api/auth/me	Get current authenticated user	Authenticated
POST	/api/auth/logout	Logout user (client-side token removal)	Authenticated
🏢 Tenant Management (Super Admin)
Method	Endpoint	Description	Role
GET	/api/tenants	List all tenants	super_admin
GET	/api/tenants/:tenantId	Get tenant details	super_admin
PUT	/api/tenants/:tenantId	Update tenant status or plan	super_admin
👥 User Management (Tenant Scope)
Method	Endpoint	Description	Role
POST	/api/tenants/:tenantId/users	Create user in tenant	tenant_admin
GET	/api/tenants/:tenantId/users	List users in tenant	tenant_admin
PUT	/api/users/:userId	Update user profile or role	tenant_admin
DELETE	/api/users/:userId	Remove user from tenant	tenant_admin
📁 Project Management
Method	Endpoint	Description	Role
POST	/api/projects	Create new project	tenant_admin / user
GET	/api/projects	List tenant projects	tenant_admin / user
GET	/api/projects/:projectId	Get project details	tenant_admin / user
PUT	/api/projects/:projectId	Update project	tenant_admin
DELETE	/api/projects/:projectId	Delete project	tenant_admin
📝 Task Management
Method	Endpoint	Description	Role
POST	/api/projects/:projectId/tasks	Create task under project	tenant_admin
GET	/api/projects/:projectId/tasks	List tasks for project	tenant_admin / user
PATCH	/api/tasks/:taskId/status	Update task status	tenant_admin / user
PUT	/api/tasks/:taskId	Update full task details	tenant_admin
DELETE	/api/tasks/:taskId	Delete task	tenant_admin
❤️ System
Method	Endpoint	Description
GET	/api/health	Health check for API and database
🔒 Security & Isolation Notes

All tenant-scoped endpoints automatically filter data using tenant_id

super_admin users have tenant_id = NULL

Cross-tenant access attempts return 404 Not Found

JWT payload contains only { userId, tenantId, role }


### Tenant Isolation Strategy

All tenant-specific data is isolated using a tenant_id discriminator.
Each API request extracts tenant_id from the JWT token and applies it
to database queries to prevent cross-tenant access.