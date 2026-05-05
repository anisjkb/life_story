# Complete Development Guideline

## Audit Firm Management Web Application

This is the consolidated development blueprint for the full system: **frontend, backend, database, file storage, reporting, QA, Docker, and deployment**.

The final recommended stack remains:

| Layer             | Technology               |
| ----------------- | ------------------------ |
| Frontend          | Next.js                  |
| Frontend Language | TypeScript               |
| UI                | Tailwind CSS + shadcn/ui |
| Backend           | NestJS                   |
| Backend Language  | TypeScript               |
| Database          | PostgreSQL               |
| ORM               | Prisma                   |
| Authentication    | JWT + Refresh Token      |
| Authorization     | RBAC + Permission Guard  |
| File Storage      | DigitalOcean Spaces      |
| Reports           | PDFKit + ExcelJS         |
| API Docs          | Swagger/OpenAPI          |
| Deployment        | Docker + VPS             |
| Reverse Proxy     | Nginx                    |
| SSL               | Certbot / Let’s Encrypt  |

Next.js officially supports project creation through `create-next-app` with TypeScript, ESLint, App Router, and path alias setup; NestJS recommends creating projects through the Nest CLI; Prisma provides ORM, migrations, and generated Prisma Client for database access. ([Next.js][1])

---

# Phase 0: Required Software Setup

Install these on every developer machine.

## 0.1 Core Software

| Software                    | Purpose                                    |
| --------------------------- | ------------------------------------------ |
| Node.js LTS                 | Run frontend/backend JavaScript/TypeScript |
| npm / pnpm                  | Package management                         |
| Git                         | Version control                            |
| VS Code                     | Code editor                                |
| Docker Desktop              | Local containerized database/app           |
| PostgreSQL client / pgAdmin | Database inspection                        |
| Postman / Insomnia          | API testing                                |
| DBeaver                     | Optional database GUI                      |

Use the current official Node.js LTS release from Node.js downloads; the current Node.js release information is maintained by Node.js official channels. ([Node.js][2])

Recommended VS Code extensions:

```txt
ESLint
Prettier
Prisma
Docker
PostgreSQL
Tailwind CSS IntelliSense
GitLens
Thunder Client
```

---

# Phase 1: Repository Setup

Recommended repository structure:

```txt
audit-firm-management-app/
├── frontend/
├── backend/
├── docker-compose.yml
├── docker-compose.prod.yml
├── README.md
└── docs/
```

Create repository:

```bash
mkdir audit-firm-management-app
cd audit-firm-management-app
git init
```

Create frontend and backend folders:

```bash
mkdir frontend backend docs
```

Recommended branches:

```txt
main
develop
feature/auth
feature/users
feature/clients
feature/audits
feature/tasks
feature/documents
feature/reports
feature/dashboard
```

---

# Phase 2: Backend Setup

## 2.1 Create NestJS Backend

Inside project root:

```bash
cd backend
npm i -g @nestjs/cli
nest new .
```

Choose npm or pnpm. Use TypeScript.

NestJS documentation recommends creating a new project through the Nest CLI, and NestJS is structured around modules, controllers, and providers/services. ([NestJS Documentation][3])

---

## 2.2 Install Backend Dependencies

```bash
npm install @nestjs/config @nestjs/jwt @nestjs/passport passport passport-jwt bcrypt
npm install @nestjs/swagger swagger-ui-express
npm install class-validator class-transformer
npm install @nestjs/event-emitter
npm install @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
npm install multer
npm install exceljs pdfkit
npm install helmet compression cookie-parser
npm install @prisma/client
npm install -D prisma tsx
npm install -D @types/passport-jwt @types/bcrypt @types/multer @types/pdfkit @types/express @types/cookie-parser
```

DigitalOcean Spaces is S3-compatible, so the AWS S3 SDK can be used by configuring the Spaces endpoint and credentials. ([DigitalOcean Docs][4])

---

## 2.3 Backend Environment File

Create:

```txt
backend/.env
```

```env
NODE_ENV="development"
PORT=5000
FRONTEND_URL="http://localhost:3000"

DATABASE_URL="postgresql://postgres:password@localhost:5432/audit_app?schema=public"

JWT_ACCESS_SECRET="change_this_access_secret"
JWT_REFRESH_SECRET="change_this_refresh_secret"
JWT_ACCESS_EXPIRES_IN="15m"
JWT_REFRESH_EXPIRES_IN="7d"

DO_SPACES_ENDPOINT="https://sgp1.digitaloceanspaces.com"
DO_SPACES_REGION="sgp1"
DO_SPACES_BUCKET="audit-app-files"
DO_SPACES_KEY="your_spaces_access_key"
DO_SPACES_SECRET="your_spaces_secret_key"
DO_SPACES_SIGNED_URL_EXPIRES_SECONDS=900

MAX_UPLOAD_FILE_SIZE_MB=25
```

Never commit `.env`.

Create:

```txt
backend/.env.example
```

with the same keys but dummy values.

---

# Phase 3: Database and Prisma Setup

## 3.1 Start PostgreSQL Locally

Option A: Docker PostgreSQL

```bash
docker run --name audit_postgres \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=audit_app \
  -p 5432:5432 \
  -d postgres:18
```

The official PostgreSQL Docker image supports current PostgreSQL versions, including PostgreSQL 18 tags; PostgreSQL’s current documentation is for PostgreSQL 18.3. ([Docker Hub][5])

Option B: Install PostgreSQL locally and create database:

```sql
CREATE DATABASE audit_app;
```

---

## 3.2 Initialize Prisma

Inside backend:

```bash
npx prisma init
```

Update:

```txt
backend/prisma/schema.prisma
```

with the final schema covering these main entities:

```txt
User
Role
Permission
RolePermission
Client
ClientContact
Audit
AuditAssignment
AuditTask
TaskComment
Document
DocumentVersion
Report
Notification
AuditTrail
Setting
```

Then run:

```bash
npx prisma validate
npx prisma format
npx prisma migrate dev --name init
npx prisma generate
```

Prisma uses a schema file to define models, migrations to evolve the database, and Prisma Client to query the database from TypeScript. ([Prisma][6])

---

# Phase 4: Backend Foundation Modules

Build the backend in this order.

## 4.1 Common Foundation

Create common folders:

```txt
src/common/
├── decorators/
│   ├── current-user.decorator.ts
│   ├── public.decorator.ts
│   ├── roles.decorator.ts
│   └── permissions.decorator.ts
├── guards/
│   ├── roles.guard.ts
│   └── permissions.guard.ts
├── types/
│   └── authenticated-user.type.ts
└── utils/
    ├── api-response.ts
    └── prisma-json.util.ts
```

Required helpers:

```txt
successResponse()
CurrentUser decorator
Public decorator
Roles decorator
Permissions decorator
JWT guard
Roles guard
Permissions guard
Prisma JSON helper
```

---

## 4.2 Prisma Module

Create:

```txt
src/prisma/prisma.module.ts
src/prisma/prisma.service.ts
```

This module should be global or imported wherever database access is needed.

---

## 4.3 Auth Module

Purpose:

```txt
Login
Refresh token
Logout
Current user profile
Change password
Forgot password
Reset password
```

Main endpoints:

```txt
POST /auth/login
POST /auth/refresh
POST /auth/logout
GET /auth/me
PATCH /auth/change-password
POST /auth/forgot-password
POST /auth/reset-password
```

Security rules:

```txt
Hash passwords with bcrypt
Store refresh token hash only
Rotate refresh token
Clear token on logout
Block inactive users
Log login/logout/password actions
```

---

## 4.4 RBAC Module

RBAC means **Role-Based Access Control**.

Recommended roles:

```txt
SUPER_ADMIN
ADMIN
PARTNER_MANAGER
REVIEWER
AUDITOR
CLIENT
```

Permission naming convention:

```txt
module.action
```

Examples:

```txt
user.create
user.read
audit.create
audit.assign
task.review
document.download
report.generate
dashboard.read
settings.update
```

Use both:

```ts
@Permissions("audit.create")
```

and where necessary:

```ts
@Roles("SUPER_ADMIN")
```

---

# Phase 5: Backend Business Modules

Build modules in this sequence.

## 5.1 Users Module

Purpose:

```txt
Create users
List users
Search/filter users
Update users
Deactivate users
Soft delete users
List auditors/reviewers/managers
User activity
```

Endpoints:

```txt
GET /users
POST /users
GET /users/auditors
GET /users/reviewers
GET /users/managers
GET /users/:id
PATCH /users/:id
PATCH /users/:id/status
DELETE /users/:id
GET /users/:id/activity
```

Important rule:

```txt
Client users must have clientId.
Non-client users must not have clientId.
Admin cannot assign SUPER_ADMIN unless current user is SUPER_ADMIN.
```

---

## 5.2 Clients Module

Purpose:

```txt
Client company management
Client contacts
Client audit history
Client documents
Client reports
```

Endpoints:

```txt
GET /clients
POST /clients
GET /clients/:id
PATCH /clients/:id
PATCH /clients/:id/status
DELETE /clients/:id
GET /clients/:id/audits
GET /clients/:id/documents
GET /clients/:id/reports
GET /clients/:clientId/contacts
POST /clients/:clientId/contacts
GET /client-contacts/:id
PATCH /client-contacts/:id
DELETE /client-contacts/:id
```

---

## 5.3 Audits Module

Purpose:

```txt
Create audit engagement
Assign audit team
Track audit status
View audit overview/progress
```

Endpoints:

```txt
GET /audits
POST /audits
GET /audits/:id
PATCH /audits/:id
DELETE /audits/:id
PATCH /audits/:id/status
GET /audits/:id/overview
GET /audits/:id/progress
GET /audits/:id/activity
GET /audits/:id/assignments
POST /audits/:id/assignments
PATCH /audit-assignments/:id
DELETE /audit-assignments/:id
```

Audit workflow:

```txt
DRAFT → IN_PROGRESS → UNDER_REVIEW → COMPLETED
DRAFT → CANCELLED
IN_PROGRESS → CANCELLED
UNDER_REVIEW → IN_PROGRESS
UNDER_REVIEW → CANCELLED
```

---

## 5.4 Audit Tasks / Checklist Module

Purpose:

```txt
Task/checklist management
Task assignment
Submit for review
Approve/reject task
Task comments
My tasks
Overdue tasks
Pending review queue
```

Endpoints:

```txt
GET /audits/:auditId/tasks
POST /audits/:auditId/tasks
GET /tasks/my
GET /tasks/overdue
GET /tasks/pending-review
GET /tasks/:id
PATCH /tasks/:id
DELETE /tasks/:id
PATCH /tasks/:id/status
PATCH /tasks/:id/assign
POST /tasks/:id/submit
POST /tasks/:id/review
GET /tasks/:id/comments
POST /tasks/:id/comments
PATCH /task-comments/:id
DELETE /task-comments/:id
```

Task workflow:

```txt
PENDING → IN_PROGRESS → SUBMITTED → REVIEWED → DONE
SUBMITTED → REJECTED
REJECTED → IN_PROGRESS
```

---

## 5.5 Documents Module with DigitalOcean Spaces

Purpose:

```txt
Upload audit evidence
Upload client documents
Upload task attachments
Download using signed URLs
Maintain document versions
Control visibility
```

Endpoints:

```txt
GET /documents
POST /documents/upload
GET /documents/:id
GET /documents/:id/download
PATCH /documents/:id
DELETE /documents/:id
POST /documents/:id/versions
GET /documents/:id/versions
PATCH /documents/:id/visibility
POST /documents/:id/approve
```

Visibility options:

```txt
INTERNAL_ONLY
CLIENT_VISIBLE
BOTH
```

DigitalOcean Spaces is object storage with an S3-compatible API, so use private buckets and signed URLs instead of public permanent document URLs. ([DigitalOcean Docs][7])

---

## 5.6 Reports Module

Purpose:

```txt
Generate PDF reports
Generate Excel reports
Store generated reports in DigitalOcean Spaces
Download reports by signed URL
```

Report types:

```txt
AUDIT_SUMMARY
TASK_PROGRESS
CLIENT_AUDIT_HISTORY
DOCUMENT_SUBMISSION
AUDITOR_WORKLOAD
```

Endpoints:

```txt
GET /reports
GET /reports/:id
GET /reports/:id/download
POST /reports/audit-summary
POST /reports/task-progress
POST /reports/client-history
POST /reports/document-submission
POST /reports/auditor-workload
DELETE /reports/:id
```

---

## 5.7 Dashboard Module

Purpose:

```txt
Summary cards
Audit status chart
Upcoming deadlines
Recent activities
My tasks
Auditor workload
```

Endpoints:

```txt
GET /dashboard/summary
GET /dashboard/audit-status
GET /dashboard/upcoming-deadlines
GET /dashboard/recent-activities
GET /dashboard/my-tasks
GET /dashboard/auditor-workload
```

Role behavior:

```txt
Admin / Super Admin: full firm dashboard
Partner Manager: operational dashboard
Reviewer: assigned audit/task dashboard
Auditor: assigned task dashboard
Client: own company dashboard
```

---

## 5.8 Notifications Module

Purpose:

```txt
In-app notifications
Unread count
Mark read
Task assignment notifications
Task review notifications
Document upload notifications
Audit status notifications
Report generation notifications
```

Endpoints:

```txt
GET /notifications
GET /notifications/unread-count
GET /notifications/:id
PATCH /notifications/:id/read
PATCH /notifications/read-all
DELETE /notifications/:id
```

For MVP:

```txt
Use database-backed notifications + REST polling.
```

For Phase 2:

```txt
Add WebSocket real-time notifications.
```

NestJS supports event-driven application logic through `@nestjs/event-emitter`, which is useful for decoupling notification creation from business modules. ([NestJS Documentation][3])

---

## 5.9 Audit Trail Module

Purpose:

```txt
Compliance logging
Activity monitoring
Entity activity
User activity
Excel export
```

Endpoints:

```txt
GET /audit-trails
GET /audit-trails/export
GET /audit-trails/entity/:entityName/:entityId
GET /audit-trails/user/:userId
GET /audit-trails/:id
```

Audit logs should be immutable.

Recommended actions:

```txt
LOGIN
LOGOUT
CREATE_USER
UPDATE_USER
CREATE_CLIENT
UPDATE_CLIENT
CREATE_AUDIT
UPDATE_AUDIT_STATUS
ASSIGN_TASK
SUBMIT_TASK
APPROVE_TASK
REJECT_TASK
UPLOAD_DOCUMENT
DOWNLOAD_DOCUMENT
GENERATE_REPORT
EXPORT_AUDIT_TRAIL
UPDATE_SETTING
```

---

## 5.10 Settings Module

Purpose:

```txt
Firm profile
Report branding
File upload limits
Notification preferences
Security options
System settings
```

Endpoints:

```txt
GET /settings
GET /settings/public
POST /settings/seed-defaults
GET /settings/:key
PATCH /settings/:key
```

Setting keys:

```txt
FIRM_PROFILE
REPORT_BRANDING
FILE_UPLOAD
NOTIFICATION_PREFERENCES
SECURITY
SYSTEM
```

Do not store secrets in settings. Keep secrets in `.env`.

---

# Phase 6: Frontend Setup

## 6.1 Create Next.js Frontend

Inside project root:

```bash
cd frontend
npx create-next-app@latest .
```

Choose:

```txt
TypeScript: Yes
ESLint: Yes
Tailwind CSS: Yes
App Router: Yes
src directory: Yes
Import alias: @/*
```

Next.js official installation docs use `create-next-app` and support TypeScript, ESLint, App Router, and path aliases. ([Next.js][1])

---

## 6.2 Install Frontend Dependencies

```bash
npm install axios
npm install zod react-hook-form @hookform/resolvers
npm install zustand
npm install @tanstack/react-query
npm install lucide-react
npm install recharts
npm install date-fns
npm install clsx tailwind-merge
```

Install shadcn/ui:

```bash
npx shadcn@latest init
```

Add common components:

```bash
npx shadcn@latest add button input label card table dialog dropdown-menu select textarea badge alert tabs form sheet separator skeleton toast
```

shadcn/ui provides a documented Next.js installation flow and component add commands; Tailwind CSS provides a Next.js setup guide and utility-first styling workflow. ([shadcn/ui][8])

---

## 6.3 Frontend Environment File

Create:

```txt
frontend/.env.local
```

```env
NEXT_PUBLIC_API_BASE_URL="http://localhost:5000/api/v1"
NEXT_PUBLIC_APP_NAME="Audit Firm Management App"
```

---

## 6.4 Recommended Frontend Structure

```txt
frontend/src/
├── app/
│   ├── login/
│   ├── dashboard/
│   ├── users/
│   ├── clients/
│   ├── audits/
│   ├── tasks/
│   ├── documents/
│   ├── reports/
│   ├── notifications/
│   ├── audit-trails/
│   ├── settings/
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ui/
│   ├── layout/
│   ├── forms/
│   ├── tables/
│   └── charts/
├── features/
│   ├── auth/
│   ├── users/
│   ├── clients/
│   ├── audits/
│   ├── tasks/
│   ├── documents/
│   ├── reports/
│   ├── dashboard/
│   ├── notifications/
│   ├── audit-trails/
│   └── settings/
├── lib/
│   ├── api.ts
│   ├── auth.ts
│   ├── query-client.ts
│   └── utils.ts
├── hooks/
├── types/
└── middleware.ts
```

---

# Phase 7: Frontend Development Phases

## 7.1 Frontend Phase 1: Auth UI

Pages:

```txt
/login
/forgot-password
/reset-password
```

Features:

```txt
Login form
Token storage
Refresh token handling
Logout
Protected routes
Role-based sidebar
```

API mapping:

```txt
POST /auth/login
POST /auth/refresh
GET /auth/me
POST /auth/logout
POST /auth/forgot-password
POST /auth/reset-password
```

---

## 7.2 Frontend Phase 2: Main Layout

Build:

```txt
Dashboard layout
Sidebar
Top navigation
Notification bell
User dropdown
Breadcrumbs
Protected route wrapper
Permission-based menu rendering
```

Sidebar modules:

```txt
Dashboard
Users
Clients
Audits
Tasks
Documents
Reports
Notifications
Audit Trails
Settings
```

---

## 7.3 Frontend Phase 3: Dashboard

Widgets:

```txt
Summary cards
Audit status chart
Upcoming deadlines
Recent activities
My tasks
Auditor workload chart
```

API mapping:

```txt
GET /dashboard/summary
GET /dashboard/audit-status
GET /dashboard/upcoming-deadlines
GET /dashboard/recent-activities
GET /dashboard/my-tasks
GET /dashboard/auditor-workload
```

---

## 7.4 Frontend Phase 4: Users and RBAC

Pages:

```txt
/users
/users/create
/users/:id
/roles
/permissions
```

Features:

```txt
User table
Search/filter
Create user
Update user
Deactivate user
Role assignment
Permission view
```

API mapping:

```txt
GET /users
POST /users
GET /users/:id
PATCH /users/:id
PATCH /users/:id/status
DELETE /users/:id
GET /roles
GET /permissions
```

---

## 7.5 Frontend Phase 5: Clients

Pages:

```txt
/clients
/clients/create
/clients/:id
/clients/:id/contacts
/clients/:id/audits
/clients/:id/documents
/clients/:id/reports
```

Features:

```txt
Client list
Client profile
Contact person management
Client audit history
Client documents
Client reports
```

---

## 7.6 Frontend Phase 6: Audits

Pages:

```txt
/audits
/audits/create
/audits/:id
/audits/:id/overview
/audits/:id/team
/audits/:id/tasks
/audits/:id/documents
/audits/:id/reports
/audits/:id/activity
```

Features:

```txt
Audit list
Create audit
Update audit
Status change
Team assignment
Progress summary
```

---

## 7.7 Frontend Phase 7: Tasks / Checklist

Pages:

```txt
/tasks/my
/tasks/overdue
/tasks/pending-review
/tasks/:id
/audits/:auditId/tasks
```

Features:

```txt
Checklist table
Task detail
Assign task
Submit task
Approve/reject task
Comments
Attached documents
```

---

## 7.8 Frontend Phase 8: Documents

Pages:

```txt
/documents
/documents/:id
```

Features:

```txt
Upload document
List documents
Filter by client/audit/task
Download signed URL
Version upload
Visibility change
Approve client document
```

Use `multipart/form-data` for uploads.

---

## 7.9 Frontend Phase 9: Reports

Pages:

```txt
/reports
/reports/generate
/reports/:id
```

Features:

```txt
Generate audit summary
Generate task progress
Generate client history
Generate document submission
Generate auditor workload
Download report
```

---

## 7.10 Frontend Phase 10: Notifications, Audit Trails, Settings

Notifications:

```txt
Notification dropdown
Unread badge
Notification list
Mark read
Mark all read
```

Audit Trails:

```txt
Audit trail table
Filters
Entity activity
User activity
Export Excel
```

Settings:

```txt
Firm profile
Report branding
File upload settings
Security settings
Notification preferences
```

---

# Phase 8: API Client Setup in Frontend

Create:

```txt
frontend/src/lib/api.ts
```

Core logic:

```ts
import axios from "axios";

export const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_BASE_URL,
  withCredentials: true,
});

api.interceptors.request.use((config) => {
  const token =
    typeof window !== "undefined" ? localStorage.getItem("accessToken") : null;

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

Add refresh-token interceptor later.

---

# Phase 9: QA and Testing

## 9.1 Backend QA Commands

Inside backend:

```bash
npm install
npx prisma validate
npx prisma format
npx prisma generate
npx prisma migrate dev
npx prisma db seed
npm run build
npm run start:dev
```

Open:

```txt
http://localhost:5000/api/docs
```

Swagger/OpenAPI integration in NestJS is supported through `@nestjs/swagger`, commonly used to expose interactive API documentation. ([NestJS Documentation][3])

---

## 9.2 Frontend QA Commands

Inside frontend:

```bash
npm install
npm run lint
npm run build
npm run dev
```

Open:

```txt
http://localhost:3000
```

---

## 9.3 Manual QA Sequence

Test in this order:

```txt
1. Login
2. Dashboard load
3. Create client
4. Create users
5. Create audit
6. Assign audit team
7. Create checklist task
8. Assign task
9. Submit task
10. Review task
11. Upload document
12. Generate report
13. Download report
14. Check notifications
15. Check audit trail
16. Update settings
```

---

# Phase 10: Docker Setup

## 10.1 Root Docker Compose

Create:

```txt
docker-compose.yml
```

```yaml
services:
  postgres:
    image: postgres:18
    container_name: audit_postgres
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: audit_app
    ports:
      - "5432:5432"
    volumes:
      - audit_postgres_data:/var/lib/postgresql/data

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: audit_backend
    restart: always
    depends_on:
      - postgres
    environment:
      NODE_ENV: production
      PORT: 5000
      FRONTEND_URL: http://localhost:3000
      DATABASE_URL: postgresql://postgres:password@postgres:5432/audit_app?schema=public
      JWT_ACCESS_SECRET: change_this_access_secret
      JWT_REFRESH_SECRET: change_this_refresh_secret
      JWT_ACCESS_EXPIRES_IN: 15m
      JWT_REFRESH_EXPIRES_IN: 7d
      DO_SPACES_ENDPOINT: https://sgp1.digitaloceanspaces.com
      DO_SPACES_REGION: sgp1
      DO_SPACES_BUCKET: audit-app-files
      DO_SPACES_KEY: your_spaces_access_key
      DO_SPACES_SECRET: your_spaces_secret_key
      DO_SPACES_SIGNED_URL_EXPIRES_SECONDS: 900
      MAX_UPLOAD_FILE_SIZE_MB: 25
    ports:
      - "5000:5000"

volumes:
  audit_postgres_data:
```

---

## 10.2 Backend Dockerfile

Create:

```txt
backend/Dockerfile
```

```dockerfile
FROM node:24-alpine AS builder

WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/

RUN npm ci

COPY . .

RUN npx prisma generate
RUN npm run build

FROM node:24-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production

COPY package*.json ./
COPY prisma ./prisma/

RUN npm ci --omit=dev

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules/.prisma ./node_modules/.prisma
COPY --from=builder /app/node_modules/@prisma ./node_modules/@prisma

EXPOSE 5000

CMD ["node", "dist/main.js"]
```

---

## 10.3 Docker Commands

From project root:

```bash
docker compose build
docker compose up -d
docker compose exec backend npx prisma migrate deploy
docker compose exec backend npx prisma db seed
docker compose logs -f backend
```

Docker’s official Ubuntu installation guide covers Docker Engine installation and supported Ubuntu versions; Docker Compose is used to run multi-container environments such as backend plus PostgreSQL. ([Docker Documentation][9])

---

# Phase 11: Production Deployment

Recommended MVP hosting:

```txt
DigitalOcean VPS / Droplet
2 vCPU
4 GB RAM
80 GB SSD
Ubuntu 24.04 LTS
```

Production components:

```txt
Nginx
Docker
Docker Compose
PostgreSQL container or managed PostgreSQL
Backend container
Frontend deployment
DigitalOcean Spaces
SSL certificate
```

Recommended production domain setup:

```txt
Frontend: https://app.yourdomain.com
Backend API: https://api.yourdomain.com
Swagger: https://api.yourdomain.com/api/docs
```

---

## 11.1 Nginx Backend Proxy

```nginx
server {
    listen 80;
    server_name api.yourdomain.com;

    client_max_body_size 30M;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

Install SSL:

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d api.yourdomain.com
```

---

# Phase 12: Final Development Timeline

For an MVP with one frontend developer and one backend developer:

| Phase                     |   Duration |
| ------------------------- | ---------: |
| Setup + Database + Prisma |   3–5 days |
| Auth + RBAC               |   5–7 days |
| Users + Clients           |   5–7 days |
| Audits + Assignments      |   5–7 days |
| Tasks / Checklist         |   5–7 days |
| Documents + Spaces        |   5–7 days |
| Reports                   |   4–6 days |
| Dashboard                 |   3–5 days |
| Notifications             |   3–5 days |
| Audit Trails + Settings   |   4–6 days |
| Frontend integration      | 15–25 days |
| QA + bug fixing           | 10–15 days |
| Docker + deployment       |   3–5 days |

Estimated MVP timeline:

```txt
8 to 12 weeks
```

---

# Phase 13: Final Module Delivery Order

Use this order strictly:

```txt
1. Repository setup
2. Backend NestJS setup
3. PostgreSQL + Prisma setup
4. Prisma schema + migrations
5. Seed roles and permissions
6. Auth module
7. RBAC module
8. Users module
9. Clients module
10. Audits module
11. Audit Tasks / Checklist module
12. Documents module
13. Reports module
14. Dashboard module
15. Notifications module
16. Audit Trails module
17. Settings module
18. Frontend Next.js setup
19. Frontend auth and layout
20. Frontend business pages
21. Full QA
22. Docker deployment
23. VPS deployment
24. UAT
25. Production release
```

---

# Final Recommendation

For this audit firm application, build the backend first until Swagger APIs are stable. Then connect the frontend module by module.

Best practical development sequence:

```txt
Backend API first
Swagger verification second
Frontend integration third
Docker deployment fourth
Client UAT fifth
```

The MVP should include:

```txt
Auth
RBAC
Users
Clients
Audits
Tasks / Checklist
Documents
Reports
Dashboard
Notifications
Audit Trails
Settings
```

Do not start advanced features like AI audit assistance, email automation, payment, or mobile app before the MVP is stable.

[1]: https://nextjs.org/docs/app/getting-started/installation?utm_source=chatgpt.com "Getting Started: Installation | Next.js"
[2]: https://nodejs.org/en/download?utm_source=chatgpt.com "Download Node.js®"
[3]: https://docs.nestjs.com/?utm_source=chatgpt.com "Documentation | NestJS - A progressive Node.js framework"
[4]: https://docs.digitalocean.com/products/spaces/how-to/use-aws-sdks/?utm_source=chatgpt.com "How to Use DigitalOcean Spaces with AWS S3 SDKs"
[5]: https://hub.docker.com/_/postgres/?utm_source=chatgpt.com "postgres - Official Image | Docker Hub"
[6]: https://www.prisma.io/docs?utm_source=chatgpt.com "Get started with Prisma | Prisma Documentation"
[7]: https://docs.digitalocean.com/products/spaces/?utm_source=chatgpt.com "Spaces Object Storage | DigitalOcean Documentation"
[8]: https://ui.shadcn.com/docs/installation/next?utm_source=chatgpt.com "Next.js - shadcn/ui"
[9]: https://docs.docker.com/engine/install/ubuntu/?utm_source=chatgpt.com "Install Docker Engine on Ubuntu | Docker Docs - Docker Documentation"
