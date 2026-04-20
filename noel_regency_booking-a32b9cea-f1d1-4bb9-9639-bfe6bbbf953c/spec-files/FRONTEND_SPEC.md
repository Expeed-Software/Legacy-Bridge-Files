# FRONTEND_SPEC.md

## Page List & Routes

All routes use kebab-case and are defined in `src/routes.tsx`.

| Page                          | Route Path                  | Auth Required | Roles with Access                |
|-------------------------------|-----------------------------|---------------|-----------------------------------|
| Login                         | `/login`                    | No            | All                              |
| Change Password               | `/change-password`          | Yes           | All                              |
| Dashboard                     | `/dashboard`                | Yes           | staff, manager, admin            |
| Bookings List                 | `/bookings`                 | Yes           | staff, manager, admin            |
| Booking Create                | `/bookings/new`             | Yes           | staff, manager, admin            |
| Booking Detail/Edit           | `/bookings/:bookingId`      | Yes           | staff, manager, admin            |
| Customers Search              | `/customers`                | Yes           | staff, manager, admin, readonly  |
| Customer Details/History      | `/customers/:customerId`    | Yes           | staff, manager, admin, readonly  |
| Rooms List                    | `/rooms`                    | Yes           | manager, admin                   |
| Room Create/Edit              | `/rooms/:roomId?`           | Yes           | manager, admin                   |
| Room Types List/Edit          | `/room-types`               | Yes           | manager, admin                   |
| Users List                    | `/users`                    | Yes           | admin                            |
| User Create/Edit              | `/users/:userId?`           | Yes           | admin                            |
| Access Control                | `/access-control`           | Yes           | admin                            |
| Reports List                  | `/reports`                  | Yes           | staff (view), manager, admin     |
| Report Detail/Export          | `/reports/:reportId`        | Yes           | staff (view), manager, admin     |
| Not Found                     | `*`                         | -             | All                              |

_All protected pages are wrapped in `<ProtectedRoute>`. All pages (except `/login` and `/change-password`) require authentication._

---

## Page and Component Breakdown

### 1. Login Page (`/login`)

**Components:**
- `<LoginForm />`
- `<ErrorBoundary />`
- `<Toast />`

**LoginForm:**
- Fields:
  - User ID / Email (`userId`)
    - Required, max 64 chars.
  - Password (`password`)
    - Required, masked input.
- Actions:
  - Submit (logs in)
- States:
  - Loading spinner during API request
  - Error toast on invalid credentials or locked account (`INVALID_CREDENTIALS`, `FORBIDDEN`)
  - Success: redirect to `/dashboard` or `/change-password` if forced
- Navigates to `/change-password` if user must change password.
- Validation:
  - All fields required, client and server-enforced.

---

### 2. Change Password Page (`/change-password`)

**Components:**
- `<ChangePasswordForm />`
- `<ErrorBoundary />`
- `<Toast />`

**ChangePasswordForm:**
- Fields:
  - Current Password (`currentPassword`)
    - Required
  - New Password (`newPassword`)
    - Required, must meet password policy
  - Confirm New Password (`confirmPassword`)
    - Must match `newPassword`
- Validation:
  - New password:
    - >= 12 characters
    - ≥ 1 uppercase, 1 lowercase, 1 digit, 1 special character
    - Not user's ID or name
    - Not any of last 5 passwords (server side enforced)
    - `zxcvbn` score >= 3 (client-side)
  - Confirm matches new password.
- Interactions:
  - Submit button disabled if validation fails or submitting.
- States:
  - Loading on submit.
  - Error: incorrect current password, password policy errors (field and toast)
  - Success: toast, redirect to `/dashboard`

---

### 3. Dashboard Page (`/dashboard`)

**Components:**
- `<RoomStatusGrid />` – all room tiles with status and numbers
- `<SummaryCounters />` – occupancy, free, maintenance, etc
- `<BookingAlerts />` – visible if triggers present
- `<ChangeRoomStatusDialog />` – modal for changing room status
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Rooms are colored/status-ed tiles (Free, Occupied, Housekeeping, Maintenance)
- Counters: number of rooms by status, number of bookings today, overdue check-outs
- Alerts: highlighted bar/section with attention-needing bookings (as per business alert logic)
- Actions per room (if permitted by role): check in, check out, set housekeeping/maintenance, view booking
- Navigates to `/bookings/new`, `/bookings/:id`, `/rooms/:id` as appropriate

**States:**
- Loading spinner while fetching data
- Error notice (and toast) for failed data load or permission issues

---

### 4. Bookings List Page (`/bookings`)

**Components:**
- `<BookingListFilters />` – search by guest, room, date, status
- `<BookingTable />` – paginated table of bookings
- `<Pagination />`
- `<AddBookingButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Table Columns:**
- Guest Name
- Room
- Check In
- Check Out
- Status (color-coded)
- Subtotal, Paid, Outstanding
- Actions: View, Edit, Void, Export Receipt

**Features:**
- Search inputs for guest name, room, booking status, date range.
- Filters resettable.
- Pagination controls.
- "Add Booking" navigates to `/bookings/new`.

**States:**
- Loading skeleton for table
- Error toast if data fetch fails

---

### 5. Booking Create/Edit Page (`/bookings/new`, `/bookings/:bookingId`)

**Components:**
- `<BookingForm />`
- `<RoomSelector />` – embedded in form
- `<GuestDetailsForm />` – embedded
- `<PaymentSection />` – deposits, payments, refund
- `<BookingSummary />` – totals calculation, timeline
- `<Toast />`
- `<ErrorBoundary />`

**Fields and Sections:**
- Guest Name (required, 2–100 chars)
- Contact Number (required, phone format)
- Origin/Nationality (optional, up to 64 chars)
- Passport/ID Number (optional, up to 32 chars)
- Stay Dates (check in/out, required, check-in < check-out)
- Room Type (required, dropdown, disables unavailable room types)
- Room (required, disables unavailable rooms)
- Deposit Amount (optional, ≥0, up to 6 digits, numeric)
- Payment Amount (optional, ≥0, up to 6 digits, numeric)
- Refund Amount (calculated or input, depending on workflow)
- Notes (optional, max 256 chars)
- Booking Status (readonly on new, editable based on workflow: confirmed, checked-in, checked-out, voided)
- Actions: Save, Save & Check-In/Check-Out (if permitted by workflow), Void (if edit and allowed), Export Receipt

**Form Behaviours:**
- Validation runs client-side and on submit
- Totals auto-calculated on amount/date changes
- Disallow booking if room/type is unavailable for given date range (conflict, 409 from API)
- Prevent void if in use
- Loading overlay during save/API
- Error: field errors shown inline, toast for server errors/business rule violations

**Navigation:**
- After save: redirect to `/bookings/:id` and show success toast

---

### 6. Customers Search Page (`/customers`)

**Components:**
- `<CustomerSearchForm />` – guest name, contact, origin, passport; all optional
- `<CustomerResultsList />` – displays matches, paginated
- `<ResetFiltersButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Search runs on submit, filtered by provided fields
- Results clickable to navigate to `/customers/:id`
- "Reset" clears all filters

**States:**
- Loading spinner for submissions
- Empty state: "No customers found"
- Error state and toast on API issues

---

### 7. Customer Details/History Page (`/customers/:customerId`)

**Components:**
- `<CustomerProfile />` – shows main fields
- `<BookingHistoryList />` – table of previous bookings
- `<ExportHistoryButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Table columns: Booking ID, Check In/Out, Room, Status, Amount
- Export button runs download (API: report export)
- Navigation: back to list

**States:**
- Loading skeletons for profile and history
- Error and empty states with appropriate messages

---

### 8. Rooms List Page (`/rooms`)

**Components:**
- `<RoomListTable />` – paginated
- `<AddRoomButton />`
- `<RoomStatusBadge />`
- `<EditRoomButton />`
- `<DeleteRoomButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Columns: Room No., Name, Type, Price, Status, Actions
- Actions only enabled for manager/admin
- Navigation to create/edit form `/rooms/:roomId?`

**States:**
- Loading for table
- Error toast on load failure

---

### 9. Room Create/Edit Page (`/rooms/:roomId?`)

**Components:**
- `<RoomForm />`
- `<Toast />`
- `<ErrorBoundary />`

**Form Fields:**
- Room Short Name (`shortName`) – required, max 16 chars
- Room Display Name (`longName`) – required, max 64 chars
- Room Type – required, dropdown
- Room Price – required, numeric, ≥0, 2 decimals, max allowed value enforced
- Room Status – Free, Occupied, Housekeeping, Maintenance (select, user role restricted)
- Notes (optional)
- Save/Cancel

**Behaviours:**
- Validation client and server-side
- Disallow room type change if in use (API enforces, error toast/message)
- Success toast/navigate back on save
- Delete disabled if room has booking

---

### 10. Room Types List/Edit Page (`/room-types`)

**Components:**
- `<RoomTypeListTable />`
- `<AddRoomTypeButton />`
- `<EditRoomTypeModal />`
- `<DeleteRoomTypeButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Fields:**
- Type Short Name (required, unique)
- Type Long Name (required)
- Active (toggle)

**Behaviours:**
- Edit opens modal
- Prevent delete/change if in use (enforced, error shown)
- States: loading, error

---

### 11. Users List Page (`/users`)

**Components:**
- `<UserListTable />`
- `<AddUserButton />`
- `<DeactivateUserButton />`
- `<EditUserButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Columns:**
- User ID, Name, Role, Active, Password Expired
- Actions: Edit, Activate/Deactivate

**States:**
- Loading, error, empty state

---

### 12. User Create/Edit Page (`/users/:userId?`)

**Components:**
- `<UserForm />`
- `<Toast />`
- `<ErrorBoundary />`

**Fields:**
- User ID (required, unique, max 64)
- Name (required, max 64)
- Email (required, valid, unique)
- Role (dropdown: staff, manager, admin, readonly)
- Active (toggle)
- Must Change Password (checkbox)
- Idle Timeout Minutes (integer, min 1, max 1440)

**Behaviours:**
- Validation: all fields
- Cannot change role to admin unless admin
- On create: password set to random and emailed (API only provides password to admin directly, never displayed)
- Save success: toast, navigate to list

---

### 13. Access Control Page (`/access-control`)

**Components:**
- `<AccessMatrixTable />` – roles × modules grid
- `<EditRolePermissionsButton />`
- `<SavePermissionsButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Table shows module/feature per row, role per column (checkboxes)
- Clicking edit allows inline changes, save persists via API
- API error yields toast

---

### 14. Reports List Page (`/reports`)

**Components:**
- `<ReportListTable />`
- `<RunReportButton />`
- `<ExportReportButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- List of reports: name, frequency (daily, weekly, monthly), available actions (per permission)
- Click report to `/reports/:reportId`
- Export/print buttons visible based on permissions

**States:**
- Loading, empty, error

---

### 15. Report Detail/Export Page (`/reports/:reportId`)

**Components:**
- `<ReportParametersForm />` – date range, user, etc. as needed
- `<ReportResultsTable />`
- `<ExportReportButton />`
- `<Toast />`
- `<ErrorBoundary />`

**Features:**
- Filter form reflects report params; runs query on submit
- Export as CSV/PDF using backend API
- Permission-based: only export/edit buttons if role allows

**States:**
- Loading for query and export
- Error toasts on permission/validation failures

---

### 16. Not Found (`*`)

**Components:**
- `<NotFound />` page
- Option to navigate home

---

## Component Behaviours and Validation

- **Forms:** All forms use React Hook Form with Zod schemas. Frontend validation matches backend, and error messages are user-friendly per spec.
- **Buttons:** Disabled while submitting or if form invalid.
- **Search/Filter Forms:** Debounced (useDebounce) as appropriate.
- **Lists/Tables:** Paginated, with totals/meta shown at bottom.
- **Error Handling:** All errors shown via `<Toast />`. Field validation errors shown inline; all others as toasts. All page-level components wrapped in `<ErrorBoundary>`.
- **Loading States:** Table/list shows skeleton or spinner. Forms show overlay or button spinner.
- **Navigation:** After successful creates/edits, navigate to detail or list, depending on use case.
- **Role Awareness:** UI elements shown/hidden based on current user's role from auth store. Permission checks defensive — API will enforce even if UI is out of sync.

---

## Page Navigation Flows

- On successful login: `/dashboard` if not password reset required; otherwise, `/change-password`.
- Session expiry or forbidden error from API: clear auth store, redirect to `/login` with toast.
- From Dashboard: navigating to room or booking details as permitted.
- Details and edit pages have "Back" buttons to return to lists.
- Protected routes redirect to `/login` if no access token in store or after silent refresh fails.
- Removal/void/destructive actions present confirmation modals.

---

## Error & Loading State Summary

- **ErrorBoundary:** Catches all rendering errors, displays friendly message and retry button.
- **API errors:** Mapped from API envelope to user messages. Common codes handled globally.
- **Validation errors:** Shown inline at field (required, pattern, min/max, server-side).
- **List/data fetch loading:** Skeleton for tables, spinners for other data.
- **Form submit loading:** Button spinner, disables input, prevents double submission.
- **Access denied:** 403 from API = toast ("You do not have permission") and navigation as per role.

---

## Auth Flow (Frontend Side)

- On app start:
  - If no access token: attempt silent refresh (if refresh token in cookie).
    - If refresh succeeds: store new token, proceed.
    - If fails: redirect to `/login`.
- On 401 errors: one retry after silent refresh, then force logout.
- On forbidden/locked/expired errors: show toast, force logout if needed.
- Authenticated user object/role is always read from Zustand auth store.

---

## Shared Components

- `<ProtectedRoute />` – enforces auth and required role
- `<Toast />` – shows queue of toasts (error/success/info)
- `<ErrorBoundary />` – as required
- `<Pagination />` – for all list views
- `<LoadingSkeleton />` – for lists/forms as needed
- `<ConfirmDialog />` – for dangerous actions
- `<StatusBadge />` – for booking/room status, color-coded
- `<RoleSelector />`, `<UserStatusToggle />`, `<ReportExportButton />` etc. – as per each form's needs

---

## Summary/Checklist

- **All forms**: React Hook Form + Zod, full field validation, error display inline + toast
- **All lists**: Paginated (default 20/pg), sortable columns if meaningful
- **All pages/components**: Loading and error states, wrapped in `ErrorBoundary`
- **Auth/Permission**: Role-based UI, defensive permission checks
- **Navigation**: Consistent after all create/edit/delete/void/submit actions
- **API integration**: All calls via typed API layer, typed hooks
- **Visual:** CSS Modules, variable-driven colours, dark theme by default
- **Testing (guidance):** All main pages/components to have at least one behaviour test per requirements

---

_Refer to the PRD and templates for domain/enumeration values, field character limits, and permission edge cases. UI copy for errors, confirmation, and validation defined as per user-facing error messaging standards._