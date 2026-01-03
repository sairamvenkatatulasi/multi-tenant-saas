Technical Specification
Multi-Tenant SaaS Project Management System

Version: 1.0
Date: January 1, 2026
Author: Student Developer

1. Overview

This document provides the technical structure, setup instructions, and execution details for the Multi-Tenant SaaS Project Management System.

The project is implemented as a monorepo, containing both backend and frontend applications, and is fully containerized using Docker Compose for consistent deployment.

2. Repository Structure
2.1 Root Directory Layout
/Multi-Tenant-SaaS-Platform
├── docker-compose.yml        # Service orchestration
├── submission.json           # Evaluation credentials
├── README.md                 # Project documentation
├── .gitignore                # Git ignore rules
├── docs/                     # PRD, Architecture, Research docs
├── backend/                  # Backend API service
└── frontend/                 # Frontend web application

3. Backend Architecture (/backend)

The backend service is built using Node.js, Express.js, and Prisma, following a modular and scalable structure.

backend/
├── .env.example              # Environment variable template
├── Dockerfile                # Backend container definition
├── package.json              # Backend dependencies
├── prisma/
│   ├── schema.prisma         # Database schema
│   └── migrations/           # Migration history
├── seeds/
│   └── seed.js               # Initial data seeding
└── src/
    ├── controllers/          # Business logic
    ├── middleware/           # Auth, RBAC, error handling
    ├── routes/               # API route definitions
    └── utils/                # Utility helpers (JWT, hashing)

4. Frontend Architecture (/frontend)

The frontend application is built using React with the Vite build tool for fast development and optimized builds.

frontend/
├── Dockerfile                # Frontend container definition
├── package.json              # Frontend dependencies
├── public/                   # Static assets
└── src/
    ├── context/              # Global state management
    ├── pages/                # UI pages (Login, Dashboard, etc.)
    ├── App.js                # Routing and layout
    └── index.js              # Application entry point

5. Development Environment Setup
5.1 Prerequisites

Ensure the following tools are installed:

Docker Desktop (v4.0 or higher)

Node.js (v18 LTS)

Git (v2.0 or higher)

5.2 Environment Configuration

Create a .env file inside the backend/ directory
(or rely on defaults provided via Docker Compose).

PORT=5000
NODE_ENV=development

DATABASE_URL="postgresql://postgres:postgres@database:5432/saas_db?schema=public"

JWT_SECRET="your_secure_random_secret_key_min_32_chars"
JWT_EXPIRES_IN="24h"

FRONTEND_URL="http://localhost:3000"

6. Installation & Setup
6.1 Clone Repository
git clone <repository_url>
cd Multi-Tenant-SaaS-Platform

6.2 Optional Local Dependency Installation

For local development and IDE support:

Backend

cd backend
npm install


Frontend

cd ../frontend
npm install

7. Running the Application (Docker)

The recommended way to run the system is using Docker Compose.

7.1 Build and Start Services

From the root directory:

docker-compose up -d --build

7.2 Service Initialization

On startup, the backend automatically executes:

Database migrations (prisma migrate deploy)

Seed script (node seeds/seed.js)

⏳ Allow 30–60 seconds for full initialization.

8. Application Access Points

Frontend UI: http://localhost:3000

Backend API: http://localhost:5000

Health Endpoint: http://localhost:5000/api/health

9. Testing & Verification
9.1 Manual API Testing

Use Postman or curl

Authenticate using credentials from submission.json

Health Check Example

curl http://localhost:5000/api/health


Expected response:

{
  "status": "ok",
  "database": "connected"
}

9.2 Database Inspection

Access the PostgreSQL container:

docker exec -it database psql -U postgres -d saas_db


Example query:

SELECT * FROM tenants;



Environment variables are used to configure services dynamically.
This allows the same Docker images to run across development and
evaluation environments without code changes.
