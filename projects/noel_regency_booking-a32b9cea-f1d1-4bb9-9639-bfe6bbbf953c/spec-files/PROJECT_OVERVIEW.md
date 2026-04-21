# PROJECT_OVERVIEW.md

## 1. Application Purpose

The **Hotel Booking System** is a web-based application for hotel staff to manage room bookings, track room and guest status, maintain customer and room master data, enforce secure user access, and generate operational reports and business documents. The system replaces legacy manual and semi-automated workflows, centralizes critical operations, and emphasizes security, data validation, and auditability. Key business objectives include improving operational efficiency, increasing booking/payment accuracy, reducing human error, and ensuring clear accountability via role-based access and activity logging.

---

## 2. Key Business Domains

- **Authentication & Security:** User login, password change, role-based access, session timeout, audit logging
- **Booking Management:** Creation, editing, voiding, check-in/out, assignment of rooms, payment entry, receipt generation, and booking auditing
- **Room Management:** Maintenance of room and room type records, real-time room status updates (free, occupied, housekeeping, maintenance), dashboard summaries
- **Customer Management:** Search, retrieval, and history export of customer records and past bookings
- **User & Access Management:** Creation and maintenance of users, roles, and module-level permissions
- **Operational Reporting:** Export and review of daily/weekly/monthly booking reports, staff activity reports, and customer history
- **Audit & Logging:** Tracking of all major business actions for operational accountability and troubleshooting

---

## 3. Core Entities & Relationships

### 3.1 Entities

- **Booking**
  - Fields: `id`, `guestName`, `guestContact`, `guestOrigin`, `guestPassport`, `roomId`, `stayDuration`, `checkInAt`, `checkOutAt`, `subTotal`, `deposit`, `payment`, `refund`, `xStatus`, `createdBy`, `createdAt`, `lastModifiedBy`, `lastModifiedAt`
- **Room**
  - Fields: `id`, `roomShortName`, `roomLongName`, `roomTypeId`, `roomPrice`, `roomStatus`, `bookingId`
- **RoomType**
  - Fields: `id`, `typeShortName`, `typeLongName`, `active`
- **User**
  - Fields: `id`, `userId`, `userName`, `userGroupId`, `userPassword`, `salt`, `loginAttempts`, `changePassword`, `isActive`, `idleLimitMin`
- **UserGroup / Role**
  - Fields: `id`, `groupName`, `groupDesc`, `securityLevel`
- **ModuleAccess**
  - Fields: `id`, `userGroupId`, `module`, `canView`, `canEdit`, `canExport`, `canAdmin`
- **ReportDefinition**
  - Fields: `id`, `reportName`, `reportTitle`, `reportQuery`, `dateField`, `active`
- **LogBooking** (Audit)
  - Tracks booking record changes, with booking and user/change details
- **LogRoom** (Audit)
  - Tracks room or room status changes with audit info
- **LogError**
  - Stores system error logs: message, module, method, user, timestamp

### 3.2 Relationships

- **Booking ↔ Room:** Each booking is assigned to a room via `roomId`. Each room may have zero or one current booking association.
- **Room ↔ RoomType:** Each room belongs to a room type (`roomTypeId`). Room types categorize rooms and are referenced in pricing and grouping.
- **User ↔ UserGroup (Role):** Each user belongs to a user group (role), which drives access control.
- **UserGroup ↔ ModuleAccess:** Each role (user group) has controlled permissions at the module/feature level via the `ModuleAccess` table.
- **Booking ↔ LogBooking:** Each booking action is tracked in `LogBooking`, capturing all changes for audit.
- **Room ↔ LogRoom:** Room status changes/audits are tracked in `LogRoom`.
- **User ↔ LogError:** All error logs are attributed to the user (if possible) in `LogError`.

---

## 4. Critical Business Rules

- **Authentication**
  - Secure login with unique user ID and password.
  - Account must force password change at next login if flagged (`changePassword`).
  - Passwords stored as salted hash (bcrypt, unique salt per user).
  - Failed login attempts are tracked; accounts are locked after 5 failures in 10 minutes, reset after 30 minutes or admin unlock.
  - Session ends after configurable inactivity (`idleLimitMin` per user).
- **Booking Management**
  - Temporary booking identifier is issued before final save during creation.
  - Bookings require guest details, assigned room (which must be available), and accurate stay/payment info.
  - Booking subtotal automatically calculated from room price, dates, and extras; deposit, payment, refund fields tracked.
  - Room status updates automatically: check-in/out, booking save, or void adjusts room status accordingly.
  - Voided bookings are excluded from active occupancy and reporting.
  - Actions on bookings (create, update, void) are fully audited.
- **Room Management**
  - Each room must have a unique short/long name, type, price, and status.
  - Room type cannot be deleted or changed if in active use by any room or booking.
  - Room status may be set to Free, Occupied, Housekeeping, or Maintenance.
  - Dashboard displays live room status and booking summaries, with alerts for bookings that need operational attention.
- **Customer Management**
  - Search by guest name, contact number, origin, or passport.
  - Viewing customer record exposes full booking history.
  - Export of customer transaction history available to permitted roles.
- **User & Access Control**
  - Users can only access actions/modules authorized by their assigned role.
  - Roles explicitly control view, export, edit, and admin privileges at the module level.
  - Only administrators may create, modify, activate, or deactivate users and assign roles.
  - Admins can force password changes on user accounts.
- **Reporting**
  - Operational and staff activity reports available in daily, weekly, and monthly granularity.
  - Export, view, print, and report edit rights are permission-controlled actions.
  - Report definitions stored as data, edited only by permitted users.
- **Audit & Logging**
  - All booking and room actions are audited with responsible user, time, and action details.
  - All system errors are logged with responsible module, method, user (if authenticated), and timestamp. No sensitive data exposed to clients.

---

## 5. User Roles & Permissions

### Roles

- **System Administrator**
  - Full access: All modules, all features (user, access, room, booking, reports, customer, audit).
- **Hotel Supervisor**
  - Full access: Dashboard, booking management, room/room type management, reporting, customer search.
  - No access: User and access control panels.
- **Front Desk Staff**
  - Access: Dashboard, booking management, customer search, receipt generation.
  - No access: Room/room type management, report export, user management, access control.
- **Auditor/Viewer**
  - Access: View-only access to customer search and report list.
  - No access: Any operational or management module.

### Module/Feature Matrix (summary)

| Module/Feature            | Administrator | Supervisor | Staff | Auditor/Viewer |
|---------------------------|---------------|------------|-------|---------------|
| Dashboard                 | Full          | Full       | View  | No            |
| Booking Management        | Full          | Full       | Full  | No            |
| Customer Search/History   | Full          | Full       | Full  | View          |
| Room Management           | Full          | Full       | No    | No            |
| Room Type Management      | Full          | Full       | No    | No            |
| User Management           | Full          | No         | No    | No            |
| Access Control            | Full          | No         | No    | No            |
| Reports List              | Full          | Full       | View  | View          |
| Report Export             | Full          | Full       | No    | No            |
| Report Editing            | Full          | No         | No    | No            |

### Permissions Principles

- Permissions are enforced at the API service layer as well as in the UI (hiding not sufficient).
- Each critical action (e.g., booking void, room status change, report export) is mapped to a permission bit in `ModuleAccess`.
- All RBAC checks are performed using the current authenticated user's effective role.
- Resource-level access (e.g., bookings created by user) may be restricted further for non-admin roles.

---

## 6. Important Constraints

- **Platform:** Web application designed and tested for modern desktop browsers only. No mobile/responsive target for v1.0.
- **Authentication:** JWT (RS256) access token, 15min expiry; stateless. Refresh tokens via secure, httpOnly cookies. Passwords are bcrypt-hashed, unique per user.
- **Access Control:** All feature and data access is role-gated. Enforcement at API layer mandatory (frontend controls are for UX only).
- **Database:** PostgreSQL, with strict schema standards. All primary keys are UUIDs. All tables have audit columns (`created_at`, `updated_at`, `deleted_at`). Soft deletes only.
- **Data Integrity:** Bookings, rooms, and users all have audit logs. Historical data is never deleted, always appended for auditing.
- **Security:** Strict password policies (minimum 12 chars, complexity enforced via zxcvbn, cannot reuse any of last 5 passwords). Session inactivity logout (per-user setting). No user data or tokens logged or exposed in error messages.
- **Performance Targets:**
  - Dashboard load: <2 seconds.
  - Booking create/update: <1 second per operation.
  - Reports export: <10 seconds for standard operational reports.
- **Scalability:** MVP designed for up to 50 concurrent users. Stateless API, safe for horizontal scaling in future.
- **Reporting:** Long-running report queries separated for eventual execution on read replica DBs.
- **Testing:** Minimum 85% unit/integration test coverage for service layer, all endpoints tested for both happy and error paths.
- **Environment:** Containerized via Docker, one codebase, .env-based config, four environments (dev/test/staging/prod), strictly separated.

---

## 7. Summary Table — Core Entities

| Entity         | Description                                             | Key Relationships                     |
|----------------|--------------------------------------------------------|---------------------------------------|
| Booking        | Guest reservation and business transaction             | ↔ Room, ↔ LogBooking (audit)          |
| Room           | Physical room inventory, status, assignment            | ↔ RoomType, ↔ Booking                 |
| RoomType       | Room category and pricing                             | ↔ Room                                |
| User           | Application user, authentication, security             | ↔ UserGroup, ↔ ModuleAccess           |
| UserGroup      | Role/group + security level                           | ↔ User, ↔ ModuleAccess                |
| ModuleAccess   | Feature/action permission by role                     | ↔ UserGroup                           |
| ReportDefinition| Query template for system and business reports        | n/a                                   |
| LogBooking     | Audit of all booking changes                          | ↔ Booking                             |
| LogRoom        | Audit of all room/room status changes                 | ↔ Room                                |
| LogError       | Error/audit log at system level                       | ↔ User (optional)                     |

---

## 8. Key Requirements to Guide Implementation

- All business requirements and access control rules are concretely specified in the PRD.
- No feature, field, or role may be generically defined — every name, rule, or action must correspond to a real, enumerated business need in the PRD.
- Architecture is strictly layered (route → controller → service → repository → DB); no logic leaks upward.
- All validation is performed at both UI and API layers; API-side is always final authority.
- All error flows use structured responses with no internal/stack trace data exposed to clients.
- All audit logging for bookings, rooms, and user actions is mandatory and non-bypassable.
- No hard deletes, sequential IDs, or non-audited changes anywhere in the application.

---

This overview is to be used by the coding agent to ensure **all implementation choices map accurately to explicit business needs, roles, workflow routes, and access policies as specified above**. No ambiguous or placeholder design is permitted.