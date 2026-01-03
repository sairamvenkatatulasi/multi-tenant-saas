Multi-Tenant SaaS Platform – API Documentation
🔐 Authentication & Security

Auth: JWT (Bearer Token)

Header: Authorization: Bearer <JWT_TOKEN>

Token Expiry: 24 Hours

Base URL: http://localhost:5000/api

1. System
Health Check

GET /health (Public)

{
  "status": "ok",
  "database": "connected"
}

2. Authentication
Register Tenant

POST /auth/register-tenant (Public)

{
  "tenantName": "Acme Corp",
  "subdomain": "acme",
  "adminEmail": "admin@acme.com",
  "password": "SecurePassword123"
}

{
  "message": "Tenant registered successfully",
  "tenantId": "uuid"
}

Login

POST /auth/login (Public)

{
  "email": "admin@acme.com",
  "password": "SecurePassword123"
}

{
  "token": "jwt-token",
  "user": {
    "id": "uuid",
    "email": "admin@acme.com",
    "role": "tenant_admin",
    "tenantId": "uuid"
  }
}

Current User

GET /auth/me (Protected)

{
  "user": {
    "id": "uuid",
    "fullName": "John Doe",
    "email": "john@acme.com",
    "role": "user"
  }
}

3. Tenant Management (Super Admin)
List Tenants

GET /tenants

{
  "data": {
    "tenants": [
      { "id": "1", "name": "Acme Corp", "subdomain": "acme" }
    ]
  }
}

Tenant Details

GET /tenants/:id

{
  "id": "uuid",
  "name": "Acme Corp",
  "status": "active"
}

Update Tenant

PUT /tenants/:id

{
  "name": "Acme Global",
  "status": "inactive"
}

4. User Management (Tenant Admin)
List Users

GET /tenants/:tenantId/users

{
  "data": {
    "users": [
      { "id": "u1", "fullName": "Alice", "role": "user" }
    ]
  }
}

Create User

POST /tenants/:tenantId/users

{
  "email": "alice@acme.com",
  "password": "Password123",
  "fullName": "Alice Smith",
  "role": "user"
}

Update User

PUT /users/:id

{
  "fullName": "Alice Jones",
  "role": "tenant_admin"
}

Delete User

DELETE /users/:id

5. Project Management
List Projects

GET /projects

{
  "data": {
    "projects": [
      { "id": "p1", "title": "Website Redesign", "status": "active" }
    ]
  }
}

Create Project

POST /projects

{
  "title": "Q3 Marketing Campaign",
  "description": "Planning for Q3",
  "status": "active"
}

Project Details

GET /projects/:id

Update Project

PUT /projects/:id

{
  "status": "completed"
}

6. Task Management
List Tasks

GET /projects/:projectId/tasks

{
  "data": {
    "tasks": [
      { "id": "t1", "title": "Draft content", "status": "TODO" }
    ]
  }
}

Create Task

POST /projects/:projectId/tasks

{
  "title": "Fix Header Bug",
  "description": "CSS issue on mobile",
  "priority": "HIGH",
  "dueDate": "2023-12-31"
}

Update Task Status

PATCH /tasks/:id/status

{
  "status": "IN_PROGRESS"
}

Update Task

PUT /tasks/:id

{
  "title": "Fix Header Bug (Updated)",
  "priority": "MEDIUM"
}


Standard API Response Format

All API endpoints in this application follow a consistent response structure to ensure predictability and ease of integration for frontend and automated evaluation scripts.

✅ Success Response

On successful execution, the API returns:

{
  "success": true,
  "message": "Operation completed successfully",
  "data": {}
}


Fields:

success – Boolean indicating request status

message – Human-readable success message (optional)

data – Payload containing the requested or modified resource

❌ Error Response

On failure, the API returns:

{
  "success": false,
  "message": "Error description"
}


Fields:

success – Always false for errors

message – Clear explanation of the error

🔐 Authorization Errors

If the user is not authorized to access a resource:

{
  "success": false,
  "message": "Access denied"
}


HTTP Status: 403 Forbidden

🔍 Resource Not Found

If a resource does not exist or belongs to another tenant:

{
  "success": false,
  "message": "Resource not found"
}


HTTP Status: 404 Not Found

This behavior ensures tenant data isolation by not revealing the existence of resources from other tenants.

🧪 Validation Errors

For invalid input data:

{
  "success": false,
  "message": "Validation failed",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email format"
    }
  ]
}


HTTP Status: 400 Bad Request

## HTTP Status Codes

The API uses standard HTTP status codes to indicate request outcomes.

- **200 OK** – Request successful
- **201 Created** – Resource created successfully
- **400 Bad Request** – Validation or input error
- **401 Unauthorized** – Missing or invalid JWT token
- **403 Forbidden** – Insufficient permissions
- **404 Not Found** – Resource not found or inaccessible
- **500 Internal Server Error** – Unexpected server error
