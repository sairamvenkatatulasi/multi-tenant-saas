📐 System Architecture Document
Multi-Tenant SaaS Platform – Project & Task Management System
1. Introduction
1.1 Purpose

This document defines the system architecture of a Multi-Tenant SaaS Project & Task Management Platform. It explains the structural design, component interactions, database model, API organization, and security mechanisms used to support multi-tenancy, scalability, and secure access.

1.2 Scope

The architecture supports:

Multiple tenants (organizations) within a single deployment

Strict tenant data isolation

Role-based access control (RBAC)

Secure authentication using JWT

Containerized deployment using Docker

2. System Overview
2.1 Architectural Style

Type: Layered, Service-Oriented Architecture

Client: Web browser (SPA)

Backend: RESTful API

Database: Relational (PostgreSQL)

Deployment: Docker & Docker Compose

3. High-Level Architecture
3.1 Core Components
3.1.1 Client (Browser)

Entry point for end users

Hosts the frontend application

Sends HTTP requests to backend APIs

3.1.2 Frontend Application

Single Page Application (SPA)

Handles:

User authentication state

Role-based UI rendering

API communication via REST

Stores JWT securely on login

3.1.3 Backend API Server

Exposes REST APIs for:

Authentication

Tenant management

User management

Project management

Task management

Responsibilities:

Business logic

Input validation

Authorization & RBAC

Tenant isolation

JWT generation & verification

Audit logging

3.1.4 Database (PostgreSQL)

Persistent storage for all system data

Enforces:

Relational integrity

Foreign key constraints

Cascading delete rules

Shared database with logical tenant isolation

3.2 Authentication & Authorization Flow

User submits credentials and tenant subdomain.

Backend validates tenant status and user credentials.

Backend issues a JWT containing:

userId

tenantId

role

Frontend stores the token securely.

Token is sent in Authorization: Bearer <token> header.

Middleware validates token and enforces access rules.

3.3 Architecture Diagram

Reference File:

docs/images/system-architecture.png


Diagram Highlights

Browser → Frontend → Backend API → Database

JWT-based authentication

Tenant isolation enforced at API & database levels

4. Database Architecture
4.1 Multi-Tenancy Model

Model Used: Shared Database, Shared Schema

Tenant isolation achieved using tenant_id in all tenant-specific tables

Super Admin records use tenant_id = NULL

4.2 Core Database Tables
Table Name	Description
tenants	Tenant/organization details & subscription info
users	User accounts, roles, tenant mapping
projects	Projects belonging to tenants
tasks	Tasks under projects
audit_logs	System activity & security logs
4.3 Entity Relationship Diagram (ERD)

Reference File:

docs/images/database-erd.png


ERD Features

One-to-many relationships

Foreign key constraints

Cascading deletes

Indexed tenant_id columns

5. API Architecture
5.1 Overview

Total APIs: 19

Style: RESTful

Security: JWT + RBAC

Response Format: Standardized JSON

5.2 Authentication Module
ID	Method	Endpoint	Auth	Access
API-01	POST	/api/auth/register-tenant	❌	Public
API-02	POST	/api/auth/login	❌	Public
API-03	GET	/api/auth/me	✅	Authenticated Users
API-04	POST	/api/auth/logout	✅	Authenticated Users
5.3 Tenant Management Module
ID	Method	Endpoint	Auth	Access
API-05	GET	/api/tenants/:tenantId	✅	Tenant Member / Super Admin
API-06	PUT	/api/tenants/:tenantId	✅	Tenant Admin / Super Admin
API-07	GET	/api/tenants	✅	Super Admin
5.4 User Management Module
ID	Method	Endpoint	Auth	Access
API-08	POST	/api/tenants/:tenantId/users	✅	Tenant Admin
API-09	GET	/api/tenants/:tenantId/users	✅	Tenant Member
API-10	PUT	/api/users/:userId	✅	Tenant Admin / Self
API-11	DELETE	/api/users/:userId	✅	Tenant Admin
5.5 Project Management Module
ID	Method	Endpoint	Auth	Access
API-12	POST	/api/projects	✅	Tenant Member
API-13	GET	/api/projects	✅	Tenant Member
API-14	PUT	/api/projects/:projectId	✅	Tenant Admin / Creator
API-15	DELETE	/api/projects/:projectId	✅	Tenant Admin / Creator
5.6 Task Management Module
ID	Method	Endpoint	Auth	Access
API-16	POST	/api/projects/:projectId/tasks	✅	Tenant Member
API-17	GET	/api/projects/:projectId/tasks	✅	Tenant Member
API-18	PATCH	/api/tasks/:taskId/status	✅	Tenant Member
API-19	PUT	/api/tasks/:taskId	✅	Tenant Member
6. Tenant Isolation & Security

Tenant ID derived only from JWT, never from client input

Centralized middleware enforces tenant-scoped queries

Super Admin role bypasses tenant restrictions when required

Role-based access control enforced at API level

Critical operations logged in audit_logs

7. Conclusion

This system architecture delivers a secure, scalable, and maintainable multi-tenant SaaS platform.
By combining:

Shared-schema multi-tenancy

Strong JWT-based security

Role-based authorization

Modular REST APIs

Containerized deployment

the platform is well-suited for production deployment, academic evaluation, and real-world SaaS use cases.