# IMPLEMENTATION_PLAN.md

This plan outlines the complete step-by-step implementation for the Hotel Booking System (v1.0), covering backend, frontend, database, and DevOps setup. Follow the phases in order. Each phase specifies tasks that are self-contained, testable, and build upon the previous phase.

---

## Phase 1: Project Bootstrap & Environment

- [ ] **Create Monorepo Structure**
  - Create root project directory with the following layout:
    ```
    /frontend/
    /backend/
    docker-compose.yml
    .env.example
    README.md
    .gitignore
    ```
- [ ] **Initialize Git Repo**
  - `git init`
  - Commit initial structure

- [ ] **Node.js Workspace and Package Init**
  - Use npm workspaces for monorepo
  - Initialize `package.json` at root and in `/frontend` and `/backend` (`npm init -y` in each)
  - Add workspace config to root `package.json`

- [ ] **Backend Bootstrap**
  - Set up TypeScript (`tsconfig.json` in `/backend`)
  - Install backend dependencies:  
    `npm install express typescript ts-node-dev prisma @prisma/client zod pino helmet cors dotenv`
  - Add dev tools:  
    `npm install -D eslint prettier vitest supertest @types/express @types/node @types/supertest`

- [ ] **Frontend Bootstrap**
  - Create React app using Vite with TypeScript template
    - `npm create vite@latest frontend -- --template react-ts`
    - `cd frontend && npm install`
  - Add dependencies:  
    `npm install react-router-dom zustand @tanstack/react-query zod react-hook-form`
  - Add dev tools:  
    `npm install -D eslint prettier vitest @testing-library/react @testing-library/user-event msw`

- [ ] **Docker & Compose Setup**
  - Create `docker-compose.yml` for frontend, backend, db (PostgreSQL)
  - Backend: create `Dockerfile` (multi-stage per standards)
  - Frontend: create `Dockerfile` and sample `nginx.conf`

- [ ] **Create .env.example Files**
  - For backend and frontend, include all required vars

- [ ] **Add and Configure Linting & Formatting**
  - ESLint and Prettier config in both `/backend` and `/frontend`

- [ ] **Document setup in README.md**

---

## Phase 2: Database Design & Migration

- [ ] **Design Prisma Data Models**
  - In `/backend/prisma/schema.prisma`, define:
    - `users`
    - `roles` (user groups)
    - `bookings`
    - `rooms`
    - `room_types`
    - `reports`
    - `module_access`
    - `refresh_tokens`
    - `log_booking`
    - `log_room`
    - `log_error`
    - All standard audit columns per spec

- [ ] **Add Database Environment Variables**
  - Update `/backend/.env.example` with `DATABASE_URL` etc.

- [ ] **Run Prisma Migrate for Initial Schema**
  - `npx prisma migrate dev --name init`
  - Ensure UUID primary keys, soft delete, constraints, indexes

- [ ] **Generate Prisma Client**
  - `npx prisma generate`

- [ ] **(Optional) Create Seed Script**
  - `/backend/prisma/seed.ts` for dev users, rooms, roles, etc.

- [ ] **Write Integration Tests for Migrations**
  - Test: All models exist, keys/indexes/constraints as specified

---

## Phase 3: Core API Infrastructure

- [ ] **Backend Project Skeleton**
  - `/backend/src/` directories:
    - `controllers/`, `services/`, `repositories/`, `middleware/`, `routes/`, `models/`, `utils/`, `config/`
  - Bootstrap `app.ts`, `server.ts` as entrypoints

- [ ] **Express Middleware**
  - Request logging (`pino`)
  - CORS using `CORS_ORIGIN`
  - Security headers with `helmet`
  - Zod validation middleware (`middleware/validate.ts`)
  - Error handler middleware per standards

- [ ] **Global Error Classes**
  - Implement `/models/errors.ts` (`AppError` hierarchy)

- [ ] **Prisma Client Singleton**
  - `/config/database.ts`

- [ ] **Environment Loader and Validator**
  - `/config/index.ts` (Zod-based env validation)

- [ ] **API Route Mounting**
  - `/routes/index.ts` mounts versioned API routers

- [ ] **Health Check Endpoint**
  - `GET /api/health` (respond 200 with service uptime & DB status)

- [ ] **Basic Unit and Integration Tests**
  - For health endpoint and error handler

---

## Phase 4: Authentication & Authorization

- [ ] **Database: User & Auth Models**
  - Users: password hash, salt, failed login attempts, lock timestamp, role/group id, etc.
  - Refresh tokens hashed

- [ ] **Repo Layer: Users, Roles, Refresh Tokens**
  - Repositories for user lookup, create, update, role fetch, token management

- [ ] **JWT Auth Utils**
  - `/utils/crypto.ts`: JWT sign/verify, bcrypt hash/check, token generation

- [ ] **Middleware: JWT Auth (`middleware/auth.ts`)**
  - Access token validation per standards, attach currentUser to request

- [ ] **Auth Controllers & Routes**
  - `/controllers/auth.controller.ts`
  - `/routes/auth.routes.ts`
  - Implement endpoints:
    - `POST /api/v1/auth/login`
    - `POST /api/v1/auth/refresh`
    - `POST /api/v1/auth/logout`
    - `POST /api/v1/auth/forgot-password`
    - `POST /api/v1/auth/reset-password`
  - Password policy enforcement

- [ ] **Service Layer: Auth Flows**
  - Login: lockout after 5 failed attempts, resets, role binding
  - Refresh: family tracking, rotation, expiry
  - Logout: revoke all tokens
  - Password reset token flow (single-use, expiry)

- [ ] **Add Test Users and Roles (seed)**
  - At least: `admin`, `manager`, `staff`, `readonly`

- [ ] **Write Unit & Integration Tests**
  - Valid/invalid login, lockout, role assignment, token refresh/rotation, logout, reset
  - Failure cases: wrong credentials, locked user, invalid tokens

---

## Phase 5: User & Access Control Management

- [ ] **Role/Permission Repository & Service Layer**
  - CRUD for `roles`, assignment to users
  - Module-level access per role in `module_access` table

- [ ] **User Management Controller/Routes**
  - `/controllers/user.controller.ts`, `/routes/user.routes.ts`
  - Endpoints:
    - `GET /api/v1/users`
    - `POST /api/v1/users`
    - `GET /api/v1/users/{id}`
    - `PATCH /api/v1/users/{id}`
    - `DELETE /api/v1/users/{id}`
  - Enforce password update flag, lock/unlock, activate/deactivate

- [ ] **Access Control (RBAC) Middleware**
  - Check module-level permission for each protected endpoint in service layer

- [ ] **Write Integration Tests for Access Matrix**
  - Role can and cannot access modules as mapped
  - Only admin can manage users/roles/access

---

## Phase 6: Room & Room Type Management

- [ ] **Repository Layer**
  - `room.repository.ts`, `roomType.repository.ts`
    - CRUD for rooms and room_types (inc. soft delete inactivated types/rooms)

- [ ] **Service Layer**
  - `room.service.ts`, `roomType.service.ts`
    - Prevent deletion/change if type is in use (`BR-04`)
    - Audit logging on change

- [ ] **Controllers & Routes**
  - `room.controller.ts`, `roomType.controller.ts`, `room.routes.ts`, `roomType.routes.ts`
    - Endpoints:
      - `/api/v1/rooms`, `/api/v1/rooms/{id}`
      - `/api/v1/room-types`, `/api/v1/room-types/{id}`
    - Filter, search, status update

- [ ] **Write Unit & Integration Tests**
  - Add/update/delete rooms/types
  - Prevent deleting type in use
  - Status transitions, audit logging

---

## Phase 7: Booking Management

- [ ] **Repository Layer**
  - `booking.repository.ts`:
    - CRUD, list, search by status/date/room/user
    - Find overlaps/conflicts
    - Include audit log table (`log_booking`), auto-write via service

- [ ] **Service Layer**
  - `booking.service.ts`:
    - Booking create flow: temp ID, room selection, price calc, subtotal, deposit/payment/refund, status workflow (`BR-03`, `BR-06`)
    - Void/cancel booking (soft)
    - Check-in/out logic & status updates on room
    - Receipt exporting (stub - mark for integration with reporting in next phase)
    - Audit fields (createdBy, modifiedBy)

- [ ] **Controllers & Routes**
  - `booking.controller.ts`, `booking.routes.ts`:
    - `/api/v1/bookings`, `/api/v1/bookings/{id}`
    - List, get, create, update, delete (soft), check-in/out, void, receipt

- [ ] **Room Status Updates**
  - Service auto-updates room status on booking lifecycle events

- [ ] **Write Unit & Integration Tests**
  - Booking creation, validation, room assignment, financials
  - Status transitions
  - Void/cancel booking, audit log
  - Prevent double-book/overlap

---

## Phase 8: Customer Search & History

- [ ] **Repository Layer**
  - Methods for booking lookup by `guestName`, contact, passport, origin

- [ ] **Controller & Route**
  - `/controllers/customer.controller.ts`
  - `/routes/customer.routes.ts`
  - Endpoints:
    - `GET /api/v1/customers` (filters: guestName, contact, origin, passport, paging)
    - `GET /api/v1/customers/{id}/bookings` (history)
    - `GET /api/v1/customers/{id}/history/export` (exports booking/payment history)

- [ ] **Service Layer**
  - Synchronous to bookings; search returns paged, filtered data, supports reset
  - Export builds reporting structure for downstream

- [ ] **Write Unit & Integration Tests**
  - Lookup by each field, CRUD edge cases, history/view/export, permission enforcement

---

## Phase 9: Reporting & Audit Log

- [ ] **Database View/Raw Query for Reports**
  - Implement `reports` repository (`repositories/reports/`)
  - Standard: daily/weekly/monthly summary, by staff, by room, by customer

- [ ] **Reports Definition CRUD**
  - `/controllers/report.controller.ts`, `/routes/report.routes.ts`
    - List, run, export (`/api/v1/reports`)
    - Maintain definitions (admin only)

- [ ] **Permission Enforcement**
  - Export/edit/report-run as permissioned features
  - Audit: every access to report export is logged

- [ ] **Audit Log Controllers**
  - Basic endpoints to query booking, room, and error logs (admin only)

- [ ] **Export Endpoints**
  - For receipts and history, use CSV or JSON exports (PDF integration is out of scope unless specified by requirements)

- [ ] **Write Tests**
  - Reports permissions
  - Export edge cases (empty, large data)
  - Log access and filtering

---

## Phase 10: Frontend Core Application (React SPA)

- [ ] **Basic App Bootstrap**
  - `App.tsx`, setup routing with React Router v6, `/src/routes.tsx`
  - Zustand-based auth store with full login/refresh/logout/role handling
  - TanStack Query setup and query client

- [ ] **Authentication Screens**
  - `/pages/LoginPage.tsx`
  - `/pages/ResetPasswordPage.tsx`, change password dialog
  - API hooks for all auth endpoints
  - ErrorBoundary and Toast component for error/success messages

- [ ] **Protected Routing**
  - `<ProtectedRoute>` component
  - Auto-redirect on 401, silent refresh logic

- [ ] **Operational Dashboard Page**
  - `/pages/DashboardPage.tsx`
  - Fetch room and occupancy status
  - Show counters, alert indicators
  - Authority-based UI (status change if permission)

- [ ] **Room & Room Type Management UI**
  - `/pages/RoomManagementPage.tsx`
  - CRUD forms for rooms/types
  - Display/disable delete button if type in use

- [ ] **Bookings UI**
  - `/pages/BookingsPage.tsx`, `/components/BookingForm.tsx`
  - Create/edit/void booking, assign room (room selector with live status)
  - Payment and financials
  - Export/print receipt

- [ ] **Customer Search & History**
  - `/pages/CustomerSearchPage.tsx`
  - Search by all filter options, booking history view
  - Export history action

- [ ] **User, Roles, and Access Control**
  - `/pages/UserManagementPage.tsx`
  - `/pages/RoleManagementPage.tsx`
  - `/pages/AccessControlPage.tsx`
  - UI for CRUD, role assignment, module matrix

- [ ] **Reporting Pages**
  - `/pages/ReportsPage.tsx`
  - List available reports, run/export, admin report editor

- [ ] **UI Standards**
  - CSS Modules only, dark theme as default
  - Error/success toasts via shared `<Toast>`
  - Forms use React Hook Form + Zod schemas

- [ ] **Write Component and Page Tests**
  - Happy flows for each feature (login, booking, search)
  - Error flows (invalid, permission denied)

---

## Phase 11: End-to-End Integration & Testing

- [ ] **Backend Integration Tests**
  - Cover every endpoint for happy and error paths as per Testing Strategy

- [ ] **Frontend Component Tests**
  - All interactive components covered

- [ ] **E2E Tests (Playwright)**
  - Core flows:
    - Login
    - Booking create
    - Room status management
    - Customer search/history
    - Report export
    - User/admin feature gating

- [ ] **Test Data Factories**
  - Implement shared factory functions for all test data

- [ ] **Run All CI Pipeline Steps**
  - Lint, unit tests, integration tests, E2E, coverage
  - Ensure pipelines fail if any coverage or test fails

---

## Phase 12: Deployment Setup & Readiness

- [ ] **Finalize Dockerfiles** for frontend and backend per standards
- [ ] **Update README Deployment Instructions**
- [ ] **Configure Health Check Endpoints**
  - For use by Docker and load balancers

- [ ] **Prepare Production docker-compose.prod.yml**
  - Use proper port, volume, and network configuration

- [ ] **Verify Environment Variable Handling**
  - No code differences between environments; all via env vars

- [ ] **Document Versioning Policy**
  - API under `/api/v1/`, response headers for deprecation

- [ ] **Production Smoke Test**
  - Deploy containers, hit health endpoint, run basic flows
  - Confirm clean log output, error handling, auth enforcement

- [ ] **Finalize CI/CD Pipeline**
  - Deploy to staging/production on tag with auto-migration

---

**Completion of all phases produces a secure, role-based, auditable hotel booking system per PRD.**