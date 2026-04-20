# STANDARDS.md

This document describes all mandatory standards and conventions for coding agent implementation of the Hotel Booking System. All requirements herein are **exhaustive** and must be adhered to for all code generated.

---

## 1. Naming Conventions

### 1.1 Variables, Functions, Classes

- **Variables & Functions:** camelCase (`getUserById`, `bookingCount`)
- **Classes & Interfaces:** PascalCase (`BookingService`, `UserRepository`)
- **Constants:** UPPER_SNAKE_CASE for module-level (`MAX_RETRY_COUNT`), camelCase for local
- **Files:** kebab-case (`booking.service.ts`), **except** React components (PascalCase, e.g. `BookingForm.tsx`)
- **Database:** snake_case for all identifiers (`created_at`, `user_id`)
- **API URLs:** kebab-case, plural nouns (`/api/v1/room-types`)
- **Environment variables:** UPPER_SNAKE_CASE (`DATABASE_URL`)

### 1.2 TypeScript

- Prefer `interface` for extensible object shapes, `type` for unions/intersections
- No `any` or `@ts-ignore`. No `unknown` unless type-guarded immediately.
- Explicit return types on all exported functions. Internal helper functions may rely on inference.
- Nullability handled explicitly—never use optional chaining as a substitute.

### 1.3 Project Structure

#### **Monorepo**

```
{project-name}/
  frontend/
  backend/
  docker-compose.yml
  .env.example
  README.md
```

#### **Frontend (React)**

- `src/components/`: Shared components, one per file (`ComponentName.tsx`)
- `src/pages/`: Route-level, `{Feature}Page.tsx`
- `src/api/`: Typed API functions (`bookings.ts`, `auth.ts`)
- `src/types/`: Shared TypeScript definitions
- `src/hooks/`: Custom React hooks
- `src/stores/`: Zustand stores, one per feature
- `src/utils/`: Pure utility functions
- Style files: `ComponentName.module.css`
- All files must not exceed size limits (see [Section 7](#7-file-size-limits))

#### **Backend (Node/Express/TypeScript)**

- `src/controllers/`: One per resource (`booking.controller.ts`)
- `src/services/`: Business logic only (`booking.service.ts`)
- `src/repositories/`: Data access (Prisma), strictly one per domain
  - `reports/`: Complex reporting queries only, raw SQL permitted with review
- `src/middleware/`: Express middleware (`auth.ts`, `error.ts`, `validate.ts`)
- `src/models/`: Types/interfaces (`booking.types.ts`), AppError class hierarchy
- `src/routes/`: Route definitions (`booking.routes.ts`)
- `src/utils/`: Utilities (`asyncHandler.ts`, etc.)
- `src/config/`: Startup config, environment validation, Prisma client singleton

#### **Database**

- `prisma/schema.prisma`: All models defined here
- `prisma/migrations/`: One migration per change, never edited post-apply
- `prisma/seed.ts`: Seed data

---

## 2. File Organization Rules

- **Directories:** No files outside prescribed structure.
- **Single Responsibility:** Each file handles one domain or concern.
- **No file exceeds:** see [Section 7](#7-file-size-limits).
- **React:** One component per file. Pages named `{Feature}Page.tsx`.
- **Backend:** One controller/service/repository per domain entity.

---

## 3. API Standards

### 3.1 RESTful Resource Naming & Structure

- Base: `/api/v1/`
- Resource names: plural, kebab-case (`/api/v1/bookings`)
- Operations:
  - List:  `GET /api/v1/bookings`
  - Get by ID:  `GET /api/v1/bookings/{id}`
  - Create:  `POST /api/v1/bookings`
  - Update:  `PATCH /api/v1/bookings/{id}` and/or `PUT` if full replace
  - Delete (soft):  `DELETE /api/v1/bookings/{id}`
- **Resource IDs:** UUIDs, never sequential or integer IDs.

### 3.2 Request & Response

- All requests and responses use JSON.
- `Content-Type: application/json` on all non-file upload POST/PATCH/PUT.
- Field names: camelCase (both request and response).
- Success responses wrap content in `{ data: ... }`, add `{ meta: ... }` for lists.
- All paginated list responses include `meta.total`, `meta.page`, `meta.limit`, `meta.totalPages`.

### 3.3 Pagination & Filtering

- Pagination query: `?page=1&limit=20`. Default `limit`: 20, max: 100.
- Sorting: `?sortBy=createdAt&sortOrder=desc`.
- Filtering: exact match (`?status=active`), date range (`?fromDate=...&toDate=...`), search (`?search=...`).

### 3.4 Error Responses

- Error envelope:
  ```json
  {
    "error": "Human-readable message",
    "code": "MACHINE_READABLE_CODE",
    "details": [{ "field": "...", "message": "..." }] // only on 400s
  }
  ```
- No stack traces or internal messages in responses
- Fields in `camelCase`; all timestamps ISO8601 UTC

### 3.5 HTTP Status Codes

- Use only: `200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `422`, `429`, `500`
- Business rule violations: `422` (`BUSINESS_RULE_VIOLATION`)
- 409 for conflicts (e.g. duplicate, overlap)
- 400 for validation errors only (with details array)

---

## 4. Error Handling Patterns

### 4.1 Backend — Error Class Hierarchy

All errors extend `AppError` (defined in a single file), never throw plain `Error`:

```
AppError (base)
  ├── ValidationError       400
  ├── AuthenticationError   401
  ├── ForbiddenError        403
  ├── NotFoundError         404
  ├── ConflictError         409
  ├── BusinessRuleError     422
  └── InternalError         500
```

**AppError signature:**
```typescript
class AppError extends Error {
  constructor(
    public readonly message: string,
    public readonly code: string,
    public readonly statusCode: number,
    public readonly details?: { field: string; message: string }[]
  ) { super(message) }
}
```

### 4.2 Where to Throw

- **Repositories:** Only throw `InternalError` for unexpected Prisma errors.
- **Services:** Throw all business errors (NotFound, Conflict, Forbidden, BusinessRule, etc).
- **Controllers:** Never throw or catch; let errors propagate to global handler.
- **Middleware:** Throws `AuthenticationError` (JWTs), `ValidationError` (Zod).

### 4.3 Global Error Handling

- A single global error handler (`src/middleware/error.ts`) must:
  - Map subclasses of AppError to appropriate status code and error code.
  - Map known Prisma errors (`P2002` for conflict, `P2025` for not found).
  - All unexpected errors are logged (full stack), send `500` with generic error message.
  - Stack traces or internals never exposed in production.

**Production 500 response:**
```json
{ "error": "An unexpected error occurred. Please try again.", "code": "INTERNAL_ERROR" }
```

### 4.4 Async Error Handling

- All async route handlers are wrapped in `asyncHandler` utility that passes errors to `next()`.
- No try/catch in controllers or services except for explicit recoveries.
- All uncaught rejections/uncaught exceptions are logged and process-exiting.

### 4.5 Frontend Error Handling

- Every page-level component is wrapped in `ErrorBoundary` (`src/components/ErrorBoundary.tsx`).
- All user-facing errors are shown via shared `<Toast>` component, never `alert()` or raw output.
- API errors are handled via TanStack Query hooks (via `onError`).
- 401s auto-trigger silent refresh; if refresh fails, user is logged out and redirected to `/login`.

---

## 5. Logging

### 5.1 Backend Logging (pino)

- Structured JSON logging only.
- Every request: method, path, status code, response time, user UUID (if authenticated).
- Every error: type, message, stack if internal, request ID, user UUID.
- Log level:
  - `error`: 500 InternalError
  - `warn`: 409 Conflict, 422 BusinessRule
  - `info`: 400/401/403/404 (validation/auth/business expected)
- Never log plaintext passwords, tokens, or PII. Use user UUID in logs.
- Logs go to stdout (container collects).

### 5.2 Frontend Logging

- No logging to console in production.
- All unhandled client errors are posted to `/api/v1/client-errors` for backend collection.
- No PII or sensitive user data sent in logs.

---

## 6. Security Requirements

### 6.1 Input Validation & Sanitization

- All inputs validated at boundary with Zod on both frontend (client) and backend (server).
- Validation schemas are explicit whitelists. Unknown fields are stripped.
- All string inputs are trimmed, have max lengths.

### 6.2 Authentication & Token Handling

- **Backend:** 
  - Authentication is via JWT (RS256), 15min expiry (memory only, never persisted clientside).
  - Refresh tokens: 32-byte hex, httpOnly, Secure, SameSite=Strict cookie, expiry 7 days.
  - *Passwords* hashed with bcrypt, min cost 12.
  - User lockout: 5 failed logins within 10 mins => locked for 30 mins. Details in users table.
- **Frontend:**
  - Store access token in Zustand (memory). Never use localStorage/sessionStorage/cookies.

### 6.3 Authorization

- Every protected endpoint checks JWT via Express middleware.
- Role is stored *only* in JWT token, never trusted from requests.
- All resource writes and reads check for resource ownership (unless role is admin).
- Role checked in service layer, never in controller/middleware.
- Resource queries must always be filtered by current user's role/ID unless admin.

### 6.4 Database Security

- PostgreSQL only accessed via Prisma.
- Passwords/tokens stored hashed (never plaintext).
- Application DB user is limited to CRUD on application tables.
- Connection string from environment variable only.

### 6.5 HTTP Security Headers

- Set via `helmet` in backend.
- Content-Security-Policy: no inline/eval scripts
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- Referrer-Policy: strict-origin-when-cross-origin
- Permissions-Policy: block camera, mic, geolocation

### 6.6 CORS & Rate Limiting

- CORS allowed only from the frontend's configured origin. No wildcard in production.
- Auth endpoints: 10 requests/min/IP. All other endpoints 100 requests/min/user.
- 429 returned with Retry-After header on rate limit.

### 6.7 CSRF, XSS, Sensitive Data

- Refresh token cookie `SameSite=Strict`—no further CSRF tokens needed; must be documented.
- No usage of `dangerouslySetInnerHTML` in React.
- All API responses JSON only; never serve user data as HTML from the API.
- No sensitive data in logs or error responses.

---

## 7. File Size Limits

| File type            | Max lines | Required action                              |
|----------------------|-----------|----------------------------------------------|
| Controller           | 100       | Split into sub-controllers/move to service   |
| Service              | 200       | Split by responsibility                      |
| Repository           | 200       | Split by query category                      |
| React component      | 250       | Extract sub-components                       |
| React page           | 150       | Extract sections into components             |
| Utility file         | 150       | Split into focused files                     |

If a file exceeds the limit, *refactor into smaller units before proceeding.*

---

## 8. Database Standards

- **Tables:** snake_case, plural (`bookings`, `room_types`)
- **Columns:** snake_case (`created_at`, `user_id`)
- **Primary keys:** Always `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`
- **Timestamps:** All tables have `created_at`, `updated_at`, `deleted_at` (soft delete marker)
- **Booleans:** `is_`/`has_` prefix
- **Foreign keys:** `{entity}_id` pattern, explicit FK constraints, always with `ON DELETE` actions
- **Monetary:** `NUMERIC(19, 4)`; never floating-point
- **Enums:** Use PostgreSQL enum for small sets, or `TEXT`+check for extensible
- **Indexes:** Explicit for all FKs and frequent WHERE/OFF conditions. Partial for `{deleted_at IS NULL}`.
- **Migrations:** Prisma Migrate only; one logical change per migration; destructive drops require 2-phase migration
- **Security:** DB user limited to CRUD; no schema changes in app runtime

---

## 9. Testing Requirements

### 9.1 Coverage

| Layer                       | Minimum coverage         |
|-----------------------------|-------------------------|
| Backend services (unit)     | 85% line coverage       |
| Backend controllers (intg)  | 1 happy + 1 error/test  |
| Components                  | Test user interaction   |
| E2E                         | 1 happy path per role   |

Coverage enforced in CI. PRs must not drop below thresholds.

### 9.2 Backend

- **Unit tests:** Services/utilities; repositories mocked, no DB.
- **Integration tests:** Controller→service→real DB.
- **Test isolation:** Each test sets up/tears down data, no shared state.
- **Factories:** All test data via factory functions, no hardcoded IDs.
- **Raw SQL queries:** Integration tested; reviewed.

### 9.3 Frontend

- **Component tests:** With React Testing Library, MSW for mocking API.
- **No snapshot tests.** Test user-facing behaviour only.
- **Error boundary:** All pages under ErrorBoundary.
- **API errors:** Shown in shared Toast component.

### 9.4 E2E (Playwright)

- Core flows only; run against staging. Max 10 tests in MVP. No permutations.

---

## 10. Coding Style and Best Practices

### 10.1 TypeScript

- `strict` mode enabled throughout; no `any`, no `@ts-ignore`.
- No circular dependencies between layers.
- All API types centrally defined; no inline type literals.

### 10.2 Functions

- Max 30 lines each. If larger, extract helper functions.
- Max 3 parameters per function; use options object for more.
- Functions must do one thing—no "and" in function names.
- Use early returns over nested conditionals.

### 10.3 React

- All components functional; one per file.
- State: local (`useState`), shared (>2 levels: Zustand), server: TanStack Query.
- No Redux/Context for app state (Context for auth/theme only).
- **Forms:** React Hook Form + Zod via resolver; no manual `onChange` for each input.

### 10.4 Comments

- Write comments to explain "why", not "what".
- No dead/commented-out code ever committed.
- No `// TODO` in codebase—all TODOs must be tracked in issues.

### 10.5 Async

- Use `async/await` only; no `.then().catch()`.
- No `await` in loops for parallelisable work—use `Promise.all`.
- Always catch errors—never let Promises go uncaught.

### 10.6 Linting & Formatting

- ESLint + Prettier strictly enforced on all files.
- All ESLint warnings treated as errors.
- Formatting checked in CI; PR blocked if violations found.
- External imports, internal aliases, relative paths—import order enforced.

### 10.7 Git & PR

#### **Branch Naming**

- `feature/{ticket-id}-summary`
- `fix/{ticket-id}-summary`
- `chore/{summary}`
- `docs/{summary}`

#### **Commit Messages**

- Format: `{type}: {desc}` (imperative, ≤72 chars)
- Types: `feat`, `fix`, `chore`, `docs`, `test`, `refactor`, `perf`
- Each commit is one logical change.
- Squash merges only; feature branch commits are squashed.
- PRs: One per feature/fix, ≤400 lines changed per PR.
- All CI checks must pass before merge.
- PR description includes: what/why/how to test.
- Delete branch after merge.
- Reviewers check for correctness, security, layer separation, error handling, test coverage, conventions.

---

## 11. Frontend-Specific Rules

- No business logic in JSX—precompute before render.
- No inline styles except dynamic values; use CSS modules only.
- Dark theme by default; all colours as CSS variables.
- All routes defined in `src/routes.tsx`; paths always kebab-case.
- Protected routes use a single `<ProtectedRoute>` component.
- On app load, if no token, attempt silent refresh; if fails, redirect to `/login`.
- All API calls go through typed functions in `src/api/`.
- All user-facing errors via `<Toast>`.
- Maximum 3 concurrent toasts; error toasts must persist until dismissed.
- 401 API responses cause logout/redirect to `/login`, toast "Session expired".

---

## 12. Backend-Specific Rules

- **Layering:** Route → Controller → Service → Repository → Database
- **No business logic in controllers.** Services contain business rules; repositories only make queries.
- All protected routes run through JWT middleware. 
- Role checks in service layer only.
- Pagination required on all list endpoints.
- Soft deletes enforced—never hard delete app data.
- All database operations via Prisma; raw SQL only in `repositories/reports/`.
- All environment config loaded and validated at startup via Zod. No `process.env` calls outside config loader.
- Health endpoint: `GET /api/health` (see deployment spec).

---

## 13. Environment and Deployment

- All environments run via Docker; no code differences between environments.
- Secrets (DB uri, JWT keys) passed as env vars or secrets at runtime; not stored in source/image.
- Health checks exposed for backend (`/api/health`).
- Nginx proxy terminates TLS, serves frontend, proxies `/api/` to backend.
- No logs on disk—logs must go to stdout/stderr for container collection.
- `.env.example` must always be up to date and validated at startup.

---

## 14. Pattern Enforcement Examples

### Auth Data Fetch

- All requests from frontend include Bearer token from Zustand store.
- 401 triggers silent refresh; failure logs out user.

### API Error Flows

- 400—validation errors include field details.
- 409—conflict (e.g. overlapping booking) returns code and friendly message.
- 403—"Forbidden"; 404—"Not Found".
- 422—business rule fail ("Room type in use").
- 500—never details; always generic.

### Role-Based Access

- Frontend: role in auth store controls visible UI.
- Backend: all role/ownership checks in service only; never in controller.

### Validation

- Zod schemas for all inputs on both client and server; must be kept logically in sync.
- Server rules always take precedence.
- Unknown/extra fields stripped server-side before use.

---

## 15. Compliance

The coding agent **must** adhere to all standards in this document. Any deviation must be reported, justified, and presented as a pull request for a standards update before proceeding.

**This is a non-negotiable baseline for all Hotel Booking System code and infrastructure.**