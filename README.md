Multi-Tenant SaaS Platform
Project Management System
📌 Overview

The Multi-Tenant SaaS Platform is a B2B project management application designed to demonstrate multi-tenancy, strict data isolation, and role-based access control (RBAC).

Each organization (tenant) operates in a fully isolated workspace where users can collaborate on projects and tasks. The platform enforces a clear role hierarchy to ensure security and proper access control.

🧑‍💼 User Roles
      
Super Admin
   
Manages all tenants globally

Monitors system usage and health

Tenant Admin

Manages users, projects, and tasks within their organization

Controls roles and access permissions

Standard User

Works on assigned projects and tasks

Updates task status and progress

🚀 Key Features

Multi-Tenant Architecture
Logical isolation of tenant data using tenant_id

Secure Authentication
JWT-based authentication with Bcrypt password hashing

Role-Based Access Control (RBAC)
Enforced permissions for Super Admins, Tenant Admins, and Users

Project Management
Create and manage tenant-specific projects

Task Management
Assign tasks with priorities and deadlines

Team Management
Tenant Admins can invite, update, or remove users

Super Admin Dashboard
Global tenant overview for system administrators

Responsive User Interface
Modern dashboard built with React

🛠 Technology Stack
Frontend

Framework: React.js (v18)

State Management: React Context API

Routing: React Router DOM (v6)

HTTP Client: Axios

Styling: CSS3 (Flexbox & Grid)

Backend

Runtime: Node.js (v18)

Framework: Express.js

ORM: Prisma

Security: JWT, Bcrypt, CORS

Validation: Express Validator

Database & DevOps

Database: PostgreSQL (v15)

Containerization: Docker & Docker Compose

Environment: Linux (Alpine containers)

🏗 Architecture Overview

The system follows a client–server architecture:

React frontend communicates with the backend via REST APIs

Backend enforces authentication, authorization, and tenant isolation

PostgreSQL stores all data with queries scoped by tenant_id

This design ensures no data leakage between organizations.

⚙️ Installation & Setup
Prerequisites

Docker & Docker Compose (recommended)

Node.js v18+ (for manual setup)

PostgreSQL (for manual setup)

🐳 Method 1: Docker Setup (Recommended)

This method automatically starts the database, backend, and frontend.

Steps

Clone the Repository

git clone <your-repo-url>
cd Multi-Tenant-SaaS-Platform


Environment Configuration
Create a .env file in the root directory:

POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=saas_db


Run the Application

docker-compose up -d --build


Access the Application

Frontend: http://localhost:3000

Backend Health Check: http://localhost:5000/api/health

Migrations and seed data are applied automatically on startup.

🧪 Method 2: Local Development (Manual)
<details> <summary>Click to expand</summary>
Database

Ensure PostgreSQL is running on port 5432.

Backend
cd backend
npm install
cp .env.example .env
npx prisma migrate dev --name init
npm run seed
npm start

Frontend
cd frontend
npm install
npm start

</details>
🔐 Environment Variables
Variable	Description	Default
PORT	Backend server port	5000
DATABASE_URL	PostgreSQL connection string	Docker internal URL
JWT_SECRET	JWT signing secret	Custom
FRONTEND_URL	CORS allowed origin	http://localhost:3000
📡 API Overview
Authentication

POST /api/auth/register-tenant – Register organization

POST /api/auth/login – User login

Projects & Tasks

GET /api/projects – List tenant projects

POST /api/projects – Create project

POST /api/projects/:id/tasks – Create task

PATCH /api/tasks/:id/status – Update task status

Administration

GET /api/tenants – List all tenants (Super Admin)

GET /api/tenants/:id/users – List users (Tenant Admin)

POST /api/tenants/:id/users – Add user (Tenant Admin)

🧪 Demo Credentials (Seed Data)
Super Admin

Email: superadmin@system.com

Password: Admin@123

Tenant Admin (Demo Organization)

Email: admin@demo.com

Password: Admin@123

Subdomain: demo