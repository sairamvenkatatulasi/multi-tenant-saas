📘 API Documentation

Base URL (Docker)

http://localhost:5000/api

🔄 Common Response Structure

All API responses follow this standard format:

{
  "success": true,
  "message": "string",
  "data": {}
}
Notes

data can be an object, array, or null

Error responses return:

{
  "success": false,
  "message": "Error description"
}

🔐 Authentication

Type: JWT (JSON Web Token)

Header Required:

Authorization: Bearer <JWT_TOKEN>

1️⃣ Authentication APIs
1.1 Login

Endpoint

POST /auth/login


Request Body

{
  "email": "admin@demo.com",
  "password": "Demo@123",
  "tenantSubdomain": "demo"
}


Success Response (200)

{
  "success": true,
  "message": "Login successful",
  "token": "jwt-token"
}

2️⃣ Tenant APIs
2.1 Get Tenant Details

Endpoint

GET /tenants/{tenantId}


Access

Auth: ✅ Required

Roles: Tenant Admin, Super Admin

2.2 List All Tenants

Endpoint

GET /tenants


Access

Auth: ✅ Required

Roles: Super Admin only

3️⃣ User Management APIs
3.1 Create User

Endpoint

POST /tenants/{tenantId}/users


Access

Auth: ✅ Required

Role: Tenant Admin

Request Body

{
  "email": "user1@demo.com",
  "password": "User@123",
  "fullName": "Demo User",
  "role": "user"
}

3.2 List Users

Endpoint

GET /tenants/{tenantId}/users


Access

Auth: ✅ Required

Roles: Tenant Admin, User

3.3 Update User

Endpoint

PUT /users/{userId}


Access

Auth: ✅ Required

Role: Tenant Admin

3.4 Delete User

Endpoint

DELETE /users/{userId}


Access

Auth: ✅ Required

Role: Tenant Admin

4️⃣ Project APIs
4.1 Create Project

Endpoint

POST /projects


Access

Auth: ✅ Required

Request Body

{
  "name": "Demo Project",
  "description": "Initial demo project"
}


Success Response (201)

{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Demo Project",
    "status": "active"
  }
}

4.2 List Projects

Endpoint

GET /projects


Access

Auth: ✅ Required

4.3 Update Project

Endpoint

PUT /projects/{projectId}


Access

Auth: ✅ Required

Roles: Tenant Admin, Project Creator

4.4 Delete Project

Endpoint

DELETE /projects/{projectId}


Access

Auth: ✅ Required

Roles: Tenant Admin, Project Creator

5️⃣ Task APIs
5.1 Create Task

Endpoint

POST /projects/{projectId}/tasks


Access

Auth: ✅ Required

Request Body

{
  "title": "Initial Task",
  "description": "First demo task",
  "priority": "medium"
}

5.2 List Tasks

Endpoint

GET /projects/{projectId}/tasks


Access

Auth: ✅ Required

5.3 Update Task

Endpoint

PUT /tasks/{taskId}


Access

Auth: ✅ Required

5.4 Update Task Status

Endpoint

PATCH /tasks/{taskId}/status


Access

Auth: ✅ Required

Request Body

{
  "status": "completed"
}

6️⃣ Health Check API
System Health

Endpoint

GET /health


Response

{
  "status": "ok",
  "database": "connected",
  "timestamp": "ISO-date"
}