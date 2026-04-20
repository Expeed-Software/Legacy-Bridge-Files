# ARCHITECTURE.md

## 1. High-Level System Architecture

```
┌─────────────────────────────────────┐
│          React SPA (Browser)        │
│  Vite + React 19 + TypeScript       │
└──────────────┬──────────────────────┘
               │ HTTPS / REST (JSON)
               ▼
┌─────────────────────────────────────┐
│        Node.js Express API          │
│ TypeScript + Express + Prisma ORM   │
│    Controllers → Services → Repos   │
└──────────────┬──────────────────────┘
               │ PostgreSQL (Prisma)
               ▼
┌─────────────────────────────────────┐
│            PostgreSQL 16+           │
│       Primary + Read Replica        │
└─────────────────────────────────────┘
```

- All communication between frontend and backend is via HTTPS.
- The React SPA talks **only** to the backend REST API.
- No direct database or third-party API calls from the frontend.
- Only Nginx (reverse proxy) is internet-facing in production. Backend and database are isolated.

---

## 2. Project & Folder Structure

**Repository:** Monorepo, npm workspaces for `frontend/` and `backend/`

```
{project-root}/
  frontend/         ← React SPA app (user interface)
    src/
      api/          # TypeScript API clients/hooks (TanStack Query)
      components/   # Shared React components
      hooks/        # Custom React hooks (pure, reusable)
      pages/        # Page-level components (route targets)
      stores/       # Zustand state stores
      types/        # Shared TypeScript types/interfaces
      utils/        # Formatting, Zod schemas, helpers
      routes.tsx    # Page route definitions
      index.css     # CSS variables, resets, fonts
      main.tsx      # Entrypoint
    public/
    index.html
    vite.config.ts
    tsconfig.json
    package.json
    .env.example

  backend/          ← Node.js API + Express
    src/
      config/
        index.ts            # Env var loading/validation
        database.ts         # Prisma client
      controllers/
        auth.controller.ts
        {resource}.controller.ts
      services/
        {resource}.service.ts
      repositories/
        {resource}.repository.ts
        reports/            # Raw SQL reports
      models/
        errors.ts           # AppError classes
        {resource}.types.ts
      middleware/
        auth.ts             # JWT validate
        error.ts            # Error handling
        validate.ts         # Zod schema validate
        rateLimiter.ts      # Rate limiting config
        requestLogger.ts    # Pino request logging
      routes/
        index.ts            # All routers
        auth.routes.ts
        {resource}.routes.ts
      utils/
        asyncHandler.ts
        pagination.ts
        crypto.ts
      app.ts                # Express app init
      server.ts             # Entrypoint (app.listen)
    prisma/
      schema.prisma         # Full DB schema
      migrations/           # Generated SQL migrations
      seed.ts               # Dev/test seed data
    src/__tests__/
      factories/            # Test data generators
      integration/          # Supertest integration tests
    package.json
    tsconfig.json
    .env.example

  docker-compose.yml
  docker-compose.prod.yml
  .env.example
  .gitignore
  README.md
```

---

## 3. Technology Choices & Versions

**Frontend:**
- React 19
- TypeScript (strict mode)
- Vite (build tool)
- TanStack Query (API state)
- Zustand (client state)
- React Router v6
- React Hook Form + Zod
- CSS Modules
- Testing: Vitest + React Testing Library
- MSW (API mocking, tests)

**Backend:**
- Node.js 20 (alpine)
- TypeScript (strict mode)
- Express 4.x
- Prisma ORM
- PostgreSQL 16+
- JWT (RS256, via jsonwebtoken)
- Zod (runtime validation)
- Pino (structured logging)
- Supertest (integration tests)
- Vitest (unit tests)

**Database:**
- PostgreSQL 16+
- Managed locally with Prisma Migrate
- Connection via Prisma only (no direct pg queries)

**Infrastructure/Deployment:**
- Docker (multi-stage builds for frontend and backend)
- docker-compose for local dev and simple production environments
- Nginx (static SPA & reverse-proxy API, see below)
- GitHub Actions (CI/CD workflows: lint, test, build, deploy)
- Health check endpoint: `GET /api/health`

---

## 4. Service Boundaries

- **Frontend**: User interface only. All business logic and data comes from backend API. Never interacts directly with DB.
- **Backend API**: All HTTP/REST endpoints. Role-based access. Owns business rules, authentication, auditing, and resource ownership. No rendering/templates.
- **Database**: All data in PostgreSQL, managed entirely via Prisma ORM. Soft deletes only—never physical deletes.
- **Admin boundaries**: 
  - Only `admin` users can manage users and permissions.
  - All role-based permissions are enforced by the backend **service layer**.
  - No privileged frontend routes accessible to users without the proper role.

---

## 5. Infrastructure Requirements

### Containers (Dockerized services)

- Nginx (static + reverse-proxy, SSL termination in prod)
- Node.js backend (`backend/`)
- React SPA frontend (`frontend/`)
- PostgreSQL 16+ database (managed via volume)

### Docker Compose (Dev and Production)

- See below for sample services
- All persistent data is in a named Docker volume (e.g. `pgdata`).
- No data stored in containers (stateless except for DB).

### Sample `docker-compose.yml`:

```yaml
services:
  frontend:
    build:
      context: ./frontend
      target: dev
    ports: ["5173:5173"]
    volumes: ["./frontend:/app", "/app/node_modules"]
    environment:
      - VITE_API_BASE_URL=http://localhost:3000

  backend:
    build:
      context: ./backend
      target: dev
    ports: ["3000:3000"]
    volumes: ["./backend:/app", "/app/node_modules"]
    env_file: ./backend/.env
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    ports: ["5432:5432"]
    environment:
      POSTGRES_USER: devuser
      POSTGRES_PASSWORD: devpassword
      POSTGRES_DB: appdb
    volumes: ["pgdata:/var/lib/postgresql/data"]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U devuser"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  pgdata:
```

---

## 6. Environment Configuration

**All config via environment variables.**  
- No secrets checked into source control.  
- All required variables validated on backend startup (Zod).
- Separate `.env.example` in frontend and backend package roots.  
- Never use `.env` files in production—use container environment variables or secrets managers.

**Backend `.env.example`:**

```
# Server
PORT=3000
NODE_ENV=development
LOG_LEVEL=info

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
TEST_DATABASE_URL=postgresql://user:password@localhost:5432/dbname_test

# Auth
JWT_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\n..."
JWT_PUBLIC_KEY="-----BEGIN PUBLIC KEY-----\n..."

# Security
CORS_ORIGIN=http://localhost:5173
```

**Frontend `.env.example`:**

```
VITE_API_BASE_URL=http://localhost:3000
```

**Health check endpoint:**

- `GET /api/health` (no authentication required)
- Returns JSON: `{ "status": "ok", "version": "...", "database": "connected", "uptime": ... }`
- Used for Docker health checks and deployment verification.

---

## 7. Deployment Approach

### Local Development

- `docker-compose.yml` starts the full stack: frontend, backend, and Postgres.
- Hot reload enabled via volume mounts in frontend/backend dev containers.
- Backend connects to database via `DATABASE_URL` from `.env`.

### Production Deployment

- **Build:**  
  - Multi-stage Dockerfile for backend (builder, production, Prisma migration).
  - Multi-stage Dockerfile for frontend (Vite build, Nginx serve).
  - All images tagged and versioned in CI.
- **Deploy:**  
  - All images run via `docker-compose.prod.yml` or Kubernetes manifests.
  - Nginx serves static React app and proxies `/api/*` requests to backend container.
  - Database volume persists across deployments.
- **Migrations:**  
  - DB migrations run via `npx prisma migrate deploy` before backend starts.
  - If migration fails, deployment aborts—old version stays live.
- **Environment:**  
  - No hardcoded secrets. All env vars provided at container start.

### Nginx Reverse Proxy

Configured to:
- Serve React SPA static files (`/`)
- Proxy `/api/` requests to Node.js backend (`localhost:3000`)
- Add secure HTTP headers (HSTS, CSP, etc)
- Block direct external access to backend and database

**Sample Nginx config:**
```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }

  location /api/ {
    proxy_pass http://backend:3000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }

  add_header X-Frame-Options "DENY";
  add_header X-Content-Type-Options "nosniff";
  add_header Referrer-Policy "strict-origin-when-cross-origin";
}
```

### CI/CD Pipeline (GitHub Actions)

- **Pull Request:**  
  1. Lint (ESLint + TypeScript check)
  2. Unit tests (backend + frontend)
  3. Integration tests
  4. Coverage threshold check
  5. Docker build (frontend/backend)

- **Merge to `main`:**
  1. All PR checks
  2. Images built, tagged with SHA
  3. Images pushed to registry
  4. Deploy to staging and run E2E tests

- **Release Tag:**
  1. Images tagged with version
  2. Deploy to production
  3. Smoke test `/api/health`
  4. Automatic rollback if smoke test fails

---

## 8. Prescriptive Summary

- **ALL source structure, naming, and boundaries must match this architecture.**
- **No direct cross-layer calls (e.g., controllers must not call repositories).**
- **All business logic in backend service layer; all data access via repositories.**
- **All frontend API interaction via typed modules in `src/api/`, all authenticated calls use the current bearer token.**
- **No business logic in React components, only view/presentation and local UI state.**
- **All backend authentication is via JWT (RS256); role-based access enforced in service layer only.**
- **Persistent data in PostgreSQL, managed via Prisma and migrations.**
- **Deployment via Docker; never run uncontainerized in production.**
- **Environment variables are the sole configuration mechanism—never code-based config switching.**
- **Security, error handling, and logging standards are mandatory as detailed in the included specs.**

This prescribes the canonical structure and deployment process for the project. All generated code and resources must adhere strictly to this architecture.