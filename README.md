# 🏢 ACME PeopleOS — Employee Performance Dashboard

> A full-stack, cloud-native **Employee Performance Management Platform** built for HR teams and managers to track workforce performance, manage skills gaps, run reviews, and drive employee development — all in one place.

[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)](https://react.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-FF9900?logo=awslambda&logoColor=white)](https://aws.amazon.com/lambda/)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)](https://www.terraform.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

---

## 📋 Table of Contents

- [Problem Statement](#-problem-statement)
- [Project Architecture](#-project-architecture)
- [Tech Stack & Tools](#-tech-stack--tools)
- [Features & Modules](#-features--modules)
- [Database Architecture](#-database-architecture)
- [API Endpoints](#-api-endpoints)
- [Authentication & Security](#-authentication--security)
- [Infrastructure & Deployment](#-infrastructure--deployment)
- [Workflow](#-workflow)
- [Local Development Setup](#-local-development-setup)
- [Environment Variables](#-environment-variables)
- [Outcomes & Results](#-outcomes--results)
- [Project Structure](#-project-structure)

---

## 🎯 Problem Statement

Modern enterprises struggle to monitor employee performance holistically. HR departments face several pain points:

- **No unified visibility** into employee ratings, attrition risk, and growth potential across departments.
- **Manual review processes** that are time-consuming, inconsistent, and hard to track.
- **Skill gap blindness** — no structured way to identify competency shortfalls and assign targeted training.
- **Goal misalignment** — employee goals are often disconnected from organizational strategy with no progress tracking.
- **Siloed data** across HR systems preventing effective workforce analytics and decision-making.

**ACME PeopleOS** solves this by providing a centralized, role-aware dashboard that integrates performance reviews, goal tracking, skill assessments, and training management into a single, data-driven platform — hosted on a scalable serverless AWS infrastructure.

---

## 🏗 Project Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        CLIENT BROWSER                        │
│                                                              │
│   React 19 + Vite SPA  ──────► CloudFront CDN (HTTPS)       │
└──────────────────────┬───────────────────────────────────────┘
                       │ /api/app/*
                       ▼
┌──────────────────────────────────────────────────────────────┐
│              AWS CLOUDFRONT DISTRIBUTION                     │
│                                                              │
│  ┌──────────────┐        ┌──────────────────────────────┐   │
│  │  S3 Bucket   │        │  Lambda Function URL Origins  │   │
│  │ (React SPA)  │        │                              │   │
│  │  index.html  │        │  /api/app/* ──► AWS Lambda   │   │
│  └──────────────┘        │               (Python 3.11)  │   │
│                          └──────────┬───────────────────┘   │
└─────────────────────────────────────┼────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────┐
│                    AWS LAMBDA FUNCTION                      │
│                                                             │
│   FastAPI 0.109 + Mangum (ASGI adapter)                    │
│   ┌──────────────────────────────────────────────────┐     │
│   │  Routes: auth / employees / reviews /            │     │
│   │          goals / competencies / trainings        │     │
│   │  Auth: JWT (HS256) + bcrypt password hashing     │     │
│   │  ORM: SQLAlchemy 2.0                             │     │
│   └──────────────────────┬───────────────────────────┘     │
└──────────────────────────┼──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│              AWS RDS — PostgreSQL 16                        │
│    db.t3.micro | 20 GB storage | Encrypted at rest          │
│    Tables: users, employees, reviews, goals,                │
│            competencies, trainings                          │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Highlights

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React 19 SPA | UI rendering, routing, state |
| CDN | AWS CloudFront | Static asset delivery + API proxying |
| Backend | AWS Lambda + FastAPI | Serverless REST API |
| Database | AWS RDS PostgreSQL 16 | Persistent relational data |
| IaC | Terraform | Reproducible cloud infrastructure |
| Security | JWT + bcrypt | Authentication & authorization |

---

## 🛠 Tech Stack & Tools

### Frontend
| Tool | Version | Role |
|---|---|---|
| **React** | 19.x | UI component framework |
| **Vite** | 7.x | Build tool & dev server |
| **React Router DOM** | 7.x | Client-side SPA routing |
| **Material UI (MUI)** | 9.x | UI component library |
| **@emotion/react** | 11.x | CSS-in-JS styling engine |
| **Vanilla CSS** | — | Custom theming & animations |
| **ESLint** | 10.x | Code linting |

### Backend
| Tool | Version | Role |
|---|---|---|
| **Python** | 3.11 | Runtime language |
| **FastAPI** | 0.109.2 | Async REST API framework |
| **Uvicorn** | 0.27.1 | ASGI server (local dev) |
| **Mangum** | 0.17.0 | AWS Lambda ↔ ASGI adapter |
| **SQLAlchemy** | 2.0.25 | ORM for database operations |
| **Pydantic** | 2.6.1 | Request/response schema validation |
| **psycopg2-binary** | 2.9.9 | PostgreSQL driver |
| **python-jose** | 3.3.0 | JWT token creation & validation |
| **passlib + bcrypt** | 1.7.4 | Password hashing |
| **python-multipart** | 0.0.9 | Form data parsing (OAuth2 login) |
| **email-validator** | 2.1.0 | Email format validation |

### Infrastructure & DevOps
| Tool | Role |
|---|---|
| **Terraform** (~> AWS provider) | Infrastructure-as-Code provisioning |
| **AWS Lambda** | Serverless backend compute |
| **AWS RDS (PostgreSQL 16)** | Managed relational database |
| **AWS S3** | Static site hosting for frontend build |
| **AWS CloudFront** | Global CDN + HTTPS + API routing |
| **AWS SQS** | Dead-letter queue for Lambda failures |
| **AWS IAM** | Role-based access for Lambda execution |
| **AWS CloudWatch** | Logs & observability (7-day retention) |

### Developer Tools
| Tool | Role |
|---|---|
| **Git + GitHub** | Version control & collaboration |
| **VS Code** | Primary IDE |
| **npm** | Frontend package management |
| **pip / venv** | Backend Python dependency management |

---

## ✨ Features & Modules

### 1. 🔐 Authentication (Login / Register)
- Email + password login via OAuth2 password flow
- JWT Bearer token issued on successful login (60-minute expiry)
- Role-based access: **HR**, **Manager**, **Employee**
- Secure password hashing using bcrypt
- Protected routes on both frontend and backend

### 2. 📊 Dashboard
- Executive summary: total employees, average rating, attrition risk count, high-potential count
- Department performance breakdown
- Rating distribution chart
- Attrition risk alerts
- Top performers leaderboard

### 3. 👥 Employees
- Full employee list with search & filter
- Employee detail cards: name, department, role, rating, potential, risk status
- Add new employees (HR role required)
- Edit employee profile (role, department, rating, potential, risk)
- Delete employees with cascading data cleanup
- Goal progress tracker per employee

### 4. ⭐ Performance Reviews
- Create, view, edit, and delete performance reviews
- Per-review fields: rating (1–5), comments, development areas, reviewer, date
- Filter reviews by employee or rating range
- Inline editing of review details

### 5. 🚀 Development
- Goals management: add goals with title, description, due date, status
- Goal progress tracking (0–100%)
- Competency / skill tracking with proficiency levels (1–5)
- Training & course enrollment with completion status

### 6. 📈 Analytics
- Workforce analytics across departments
- Skill gap identification
- Attrition risk distribution
- Training completion rates

---

## 🗄 Database Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATABASE SCHEMA                          │
│                                                                 │
│  ┌────────────────────┐         ┌────────────────────────────┐  │
│  │      USERS         │1      1 │       EMPLOYEES            │  │
│  │────────────────────│─────────│────────────────────────────│  │
│  │ id (PK)            │         │ id (PK)                    │  │
│  │ email (UNIQUE)     │         │ user_id (FK → users.id)    │  │
│  │ hashed_password    │         │ name                       │  │
│  │ role               │         │ role                       │  │
│  └────────────────────┘         │ dept                       │  │
│                                 │ rating (Float)             │  │
│                                 │ potential (High/Mod/Low)   │  │
│                                 │ risk (High/Medium/Low)     │  │
│                                 │ status (Active/Inactive)   │  │
│                                 │ manager_id (FK → self)     │  │
│                                 └──────────────┬─────────────┘  │
│                                                │ 1:N            │
│                  ┌─────────────────────────────┼───────────┐    │
│                  │              │              │           │    │
│                  ▼              ▼              ▼           ▼    │
│  ┌─────────────────┐ ┌──────────────┐ ┌─────────────┐ ┌──────┐│
│  │    REVIEWS      │ │    GOALS     │ │COMPETENCIES │ │TRAIN.││
│  │─────────────────│ │──────────────│ │─────────────│ │──────││
│  │ id (PK)         │ │ id (PK)      │ │ id (PK)     │ │ id   ││
│  │ employee_id(FK) │ │ employee_id  │ │ employee_id │ │ emp_.││
│  │ reviewer_id(FK) │ │ title        │ │ skill_name  │ │cours.││
│  │ rating (Float)  │ │ description  │ │ proficiency │ │compl.││
│  │ comments (Text) │ │ status       │ │ (1-5)       │ │statu.││
│  │ development_    │ │ progress (%) │ └─────────────┘ └──────││
│  │   areas (Text)  │ │ due_date     │                        ││
│  │ created_at      │ └──────────────┘                        ││
│  └─────────────────┘                                         ││
└──────────────────────────────────────────────────────────────┘┘
```

### Table Definitions

#### `users`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `email` | STRING UNIQUE | Login identifier |
| `hashed_password` | STRING | bcrypt-hashed password |
| `role` | STRING | `HR` / `Manager` / `Employee` |

#### `employees`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `user_id` | INTEGER FK | Links to `users.id` (1:1) |
| `name` | STRING | Full name |
| `role` | STRING | Job title |
| `dept` | STRING | Department |
| `rating` | FLOAT | Performance rating (0.0–5.0) |
| `potential` | STRING | `High` / `Moderate` / `Low` |
| `risk` | STRING | Attrition risk: `High` / `Medium` / `Low` |
| `status` | STRING | `Active` / `Inactive` |
| `manager_id` | INTEGER FK | Self-referential FK (employee's manager) |

#### `reviews`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `employee_id` | INTEGER FK | Reviewee (→ `employees.id`) |
| `reviewer_id` | INTEGER FK | Reviewer (→ `employees.id`) |
| `rating` | FLOAT | Review score |
| `comments` | TEXT | Review narrative |
| `development_areas` | TEXT | Areas for improvement |
| `created_at` | DATETIME | Timestamp of review |

#### `goals`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `employee_id` | INTEGER FK | Links to `employees.id` |
| `title` | STRING | Goal name |
| `description` | TEXT | Detailed goal description |
| `status` | STRING | `Pending` / `In Progress` / `Completed` |
| `progress` | INTEGER | Progress percentage (0–100) |
| `due_date` | DATE | Target completion date |

#### `competencies`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `employee_id` | INTEGER FK | Links to `employees.id` |
| `skill_name` | STRING | Skill or competency name |
| `proficiency_level` | INTEGER | Score 1 (beginner) – 5 (expert) |

#### `trainings`
| Column | Type | Description |
|---|---|---|
| `id` | INTEGER PK | Auto-increment primary key |
| `employee_id` | INTEGER FK | Links to `employees.id` |
| `course_name` | STRING | Course title |
| `completion_date` | DATE | Date completed (nullable) |
| `status` | STRING | `Not Started` / `In Progress` / `Completed` |

### Cascade Behavior
- Deleting a `User` → cascades to delete the linked `Employee`
- Deleting an `Employee` → cascades to delete all linked `Reviews`, `Goals`, `Competencies`, and `Trainings`

---

## 🔌 API Endpoints

Base URL (production): `https://d1lfg7e3zygcw2.cloudfront.net/api/app`  
Base URL (local): `http://localhost:8000`

### Health
| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | API status check |
| GET | `/health` | Health probe |
| GET | `/init-db` | Initialize/verify DB tables |

### Auth
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/auth/register` | Public | Register new user + employee |
| POST | `/auth/login` | Public | Login, returns JWT token |
| GET | `/auth/me` | Bearer | Get current user info |

### Employees
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/employees` | Bearer | List all employees |
| GET | `/employees/{id}` | Bearer | Get employee by ID |
| PUT | `/employees/{id}` | HR / Manager | Update employee details |
| DELETE | `/employees/{id}` | HR | Delete employee & user |

### Reviews
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/reviews` | Bearer | List all reviews |
| POST | `/reviews` | HR / Manager | Create new review |
| PUT | `/reviews/{id}` | HR / Manager | Update review |
| DELETE | `/reviews/{id}` | HR | Delete review |

### Goals
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/goals` | Bearer | List all goals |
| POST | `/goals` | Bearer | Create a new goal |
| PUT | `/goals/{id}` | Bearer | Update goal / progress |

### Competencies
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/competencies` | Bearer | List all competencies |
| POST | `/competencies` | Bearer | Add competency record |
| PUT | `/competencies/{id}` | Bearer | Update skill proficiency |

### Trainings
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/trainings` | Bearer | List all training records |
| POST | `/trainings` | Bearer | Add training record |

---

## 🔒 Authentication & Security

- **JWT (HS256)** — tokens issued at login, expire after 60 minutes
- **bcrypt** — passwords are hashed before storage, never stored in plain text
- **Role-Based Access Control (RBAC)**:
  - `HR` — full access (create, read, update, delete)
  - `Manager` — can review and update employees under them
  - `Employee` — read-only access to own data
- **CORS** — restricted to the `FRONTEND_URL` environment variable (CloudFront domain in production)
- **HTTPS enforcement** — CloudFront redirects all HTTP to HTTPS

---

## ☁ Infrastructure & Deployment

The entire cloud infrastructure is defined as code using **Terraform**.

### AWS Resources Provisioned

```
infra/
├── main.tf           # Random ID & pet name generators
├── provider.tf       # AWS provider configuration
├── variable.tf       # Input variables
├── locals.tf         # Computed locals (env vars, function discovery)
├── s3.tf             # S3 bucket for React SPA + hot-reload bucket
├── cloudfront.tf     # CloudFront distribution + OAC + bucket policy
├── lambda.tf         # Lambda functions + SQS DLQ + hot-reload
├── rds.tf            # RDS PostgreSQL 16 (db.t3.micro)
├── documentdb.tf     # Optional DocumentDB (disabled by default)
├── data.tf           # Data sources (VPC, subnets, caller identity)
├── output.tf         # CloudFront URL, Lambda URLs, RDS endpoint
└── policy.tftpl      # IAM policy template for Lambda role
```

### Deployment Flow

```
1. terraform init        # Download providers & modules
2. terraform plan        # Preview infrastructure changes
3. terraform apply       # Provision AWS resources
4. cd frontend && npm run build   # Build React SPA
5. aws s3 sync dist/ s3://<bucket>/   # Upload frontend to S3
6. CloudFront invalidates cache       # (optional, automatic via TTL)
```

### Lambda Auto-Discovery
Terraform automatically discovers Lambda functions by scanning `backend/` for:
- `function.py` → Python 3.11 Lambda
- `package.json` → Node.js 24.x Lambda
- `pom.xml` → Java 25 Lambda (Maven build)

---

## 🔄 Workflow

### User Authentication Flow
```
User → Login Page → POST /auth/login (email + password)
     → JWT token stored in localStorage
     → All subsequent API calls include "Authorization: Bearer <token>"
     → GET /auth/me → fetch role & employee data
     → Redirect to Dashboard
```

### Employee Management Flow
```
HR User → Employees page → GET /employees → displays all employees
        → Click "Add Employee" → POST /auth/register → creates user + employee
        → Click "Edit" → PUT /employees/{id} → updates profile
        → Click "Delete" → DELETE /employees/{id} → cascades to all related data
```

### Performance Review Flow
```
Manager → Reviews page → GET /reviews → see all reviews
        → Click "Add Review" → POST /reviews (employee_id, rating, comments)
        → Click "Edit" → inline editing → PUT /reviews/{id}
        → Analytics page → aggregated ratings & department breakdown
```

### Development Plan Flow
```
Employee/Manager → Development page → goals, competencies, trainings
               → Add Goal → POST /goals
               → Update Progress → PUT /goals/{id} (progress: 0-100)
               → Add Skill → POST /competencies
               → Add Training → POST /trainings
```

---

## 💻 Local Development Setup

### Prerequisites
- Node.js 18+ and npm
- Python 3.11+
- PostgreSQL 16 (local or Docker)
- Git

### 1. Clone the Repository
```bash
git clone https://github.com/your-org/Employee-Dashboard.git
cd Employee-Dashboard
```

### 2. Backend Setup
```bash
cd backend/app

# Create and activate virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Set environment variables
set DATABASE_URL=postgresql://postgres:postgres123@localhost:5432/postgres
set JWT_SECRET_KEY=your_local_secret_key
set FRONTEND_URL=http://localhost:3000

# Seed database with sample data (optional)
python ../seed_db.py

# Start the backend server
uvicorn app.main:app --reload --port 8000
```

Backend will be available at: `http://localhost:8000`  
Swagger UI: `http://localhost:8000/docs`

### 3. Frontend Setup
```bash
cd frontend

# Install dependencies
npm install

# Create environment file
echo "VITE_API_BASE_URL=http://localhost:8000" > .env.local

# Start dev server
npm run dev
```

Frontend will be available at: `http://localhost:3000`

### Default Login Credentials (seeded data)
| Role | Email | Password |
|---|---|---|
| HR Admin | admin@acme.com | password123 |
| Manager | manager@acme.com | password123 |
| Employee | employee@acme.com | password123 |

---

## 🔧 Environment Variables

### Backend (Lambda / Uvicorn)
| Variable | Description | Default |
|---|---|---|
| `DATABASE_URL` | Full PostgreSQL connection string | — |
| `POSTGRES_HOST` | PostgreSQL host | `localhost` |
| `POSTGRES_PORT` | PostgreSQL port | `5432` |
| `POSTGRES_NAME` | Database name | `postgres` |
| `POSTGRES_USER` | Database username | `postgres` |
| `POSTGRES_PASS` | Database password | `postgres123` |
| `JWT_SECRET_KEY` | Secret key for JWT signing | `fallback_local_secret` |
| `FRONTEND_URL` | Allowed CORS origin | `http://localhost:3000` |
| `IS_LOCAL` | `true` for LocalStack, `false` for AWS | `false` |

### Frontend (Vite)
| Variable | Description | Default |
|---|---|---|
| `VITE_API_BASE_URL` | Backend API base URL | CloudFront URL |

---

## 📊 Outcomes & Results

### Technical Outcomes
- ✅ **Serverless Architecture** — Backend runs on AWS Lambda with zero server management overhead
- ✅ **Auto-scaling** — Lambda scales automatically with demand, handles traffic spikes without configuration
- ✅ **Global CDN Delivery** — React SPA served from CloudFront edge locations with HTTPS
- ✅ **Infrastructure as Code** — Entire stack reproducible with a single `terraform apply`
- ✅ **Secure Authentication** — JWT + bcrypt with role-based access control across all endpoints
- ✅ **Cascading Data Integrity** — SQLAlchemy ORM enforces referential integrity with cascade deletes
- ✅ **Pydantic v2 Validation** — All API inputs validated with strict schema enforcement

### Business Outcomes
- 📌 **Unified HR Dashboard** — Single pane of glass for all employee performance data
- 📌 **Attrition Risk Detection** — Real-time flagging of employees at risk of leaving
- 📌 **Skill Gap Analysis** — Competency proficiency scores highlight individual training needs
- 📌 **Goal Alignment** — Employees and managers can track OKRs with progress percentages
- 📌 **Audit Trail** — All reviews timestamped and attributed to specific reviewers
- 📌 **Role-Based Workflows** — HR, Managers, and Employees each see only what they need

---

## 📁 Project Structure

```
Employee-Dashboard/
│
├── backend/                         # Python FastAPI backend
│   ├── app/
│   │   ├── main.py                  # FastAPI app entry point + router registration
│   │   ├── models.py                # SQLAlchemy ORM models (6 tables)
│   │   ├── schemas.py               # Pydantic v2 request/response schemas
│   │   ├── database.py              # DB engine & session factory
│   │   ├── auth.py                  # JWT creation, bcrypt hashing, RBAC decorators
│   │   ├── function.py              # Mangum Lambda handler
│   │   ├── requirements.txt         # Python dependencies
│   │   └── routes/
│   │       ├── auth.py              # /auth/* endpoints
│   │       ├── employees.py         # /employees/* endpoints
│   │       ├── reviews.py           # /reviews/* endpoints
│   │       ├── goals.py             # /goals/* endpoints
│   │       ├── competencies.py      # /competencies/* endpoints
│   │       └── trainings.py         # /trainings/* endpoints
│   ├── seed_db.py                   # Database seeding script (dummy data)
│   └── check_db.py                  # DB health check utility
│
├── frontend/                        # React 19 + Vite frontend
│   ├── src/
│   │   ├── main.jsx                 # React app entry point
│   │   ├── App.jsx                  # Root component + routes
│   │   ├── style.css                # Global CSS & design system
│   │   ├── data.js                  # Static fallback data
│   │   ├── utils.jsx                # Shared utility functions
│   │   ├── pages/
│   │   │   ├── Login.jsx            # Authentication page
│   │   │   ├── Dashboard.jsx        # Executive summary dashboard
│   │   │   ├── Employees.jsx        # Employee list & management
│   │   │   ├── EmployeeProfile.jsx  # Detailed employee profile
│   │   │   ├── Reviews.jsx          # Performance reviews CRUD
│   │   │   ├── Development.jsx      # Goals, skills & training
│   │   │   └── Analytics.jsx        # Workforce analytics charts
│   │   ├── components/
│   │   │   ├── Sidebar.jsx          # Navigation sidebar
│   │   │   ├── Topbar.jsx           # Header with sign-out
│   │   │   ├── Modal.jsx            # Reusable modal wrapper
│   │   │   └── UI.jsx               # Shared UI primitives
│   │   └── services/
│   │       └── api.js               # Centralised API client (fetch wrapper)
│   ├── index.html                   # HTML entry point
│   ├── vite.config.js               # Vite config + dev proxy
│   └── package.json                 # Frontend dependencies
│
├── infra/                           # Terraform Infrastructure as Code
│   ├── main.tf                      # Random ID generation
│   ├── provider.tf                  # AWS provider
│   ├── variable.tf                  # Input variables
│   ├── locals.tf                    # Computed locals & env vars
│   ├── s3.tf                        # S3 bucket (frontend hosting)
│   ├── cloudfront.tf                # CloudFront distribution
│   ├── lambda.tf                    # Lambda + SQS DLQ
│   ├── rds.tf                       # RDS PostgreSQL 16
│   ├── documentdb.tf                # Optional DocumentDB
│   ├── data.tf                      # Data sources (VPC, identity)
│   ├── output.tf                    # Output: URLs & endpoints
│   └── policy.tftpl                 # IAM policy template
│
├── docs/                            # Additional documentation
├── .github/                         # GitHub Actions workflows
├── .gitignore
├── LICENSE                          # MIT License
├── CONTRIBUTING.md
└── README.md                        # This file
```

---

## 🤝 Contributing

We welcome contributions! Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines on how to propose bug fixes, new features, or documentation improvements.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](./LICENSE) file for details.

---

<div align="center">
  <strong>Built with ❤️ for the Citi Coding Workshop</strong><br/>
  <em>ACME PeopleOS — Empowering HR teams with data-driven workforce insights</em>
</div>
