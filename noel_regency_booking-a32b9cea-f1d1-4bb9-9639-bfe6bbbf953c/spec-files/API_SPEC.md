# Hotel Booking System — REST API Specification

**Version:** v1  
**Base URL:** `/api/v1/`  
**Format:** JSON  
**Versioning:** URI-based — `/api/v1/`  
**Authentication:** JWT (RS256) access token (`Authorization: Bearer ...`), except public auth endpoints  
**All resource IDs:** UUID (string, canonical)  
**Timestamps:** ISO 8601 UTC  
**Monetary Amounts:** String, 4 decimals (e.g., `"1234.5000"`)

---

## Table of Contents

1. [Authentication](#authentication)
2. [Users](#users)
3. [Roles & Access Control](#roles--access-control)
4. [Rooms](#rooms)
5. [Room Types](#room-types)
6. [Bookings](#bookings)
7. [Booking Receipts](#booking-receipts)
8. [Room Status Management](#room-status-management)
9. [Customer Search & History](#customer-search--history)
10. [Reports](#reports)
11. [Audit & Logging](#audit--logging)
12. [Health & Diagnostics](#health--diagnostics)

---

## Authentication

Authentication uses stateless JWT access tokens and stateful refresh tokens (see [authentication.md](#)).  
All paths except `/auth/*` require authentication.  
See [authentication.md](#) for details on token and session flows.

### 1.1 Login

**POST** `/api/v1/auth/login`

- **Public** (no `Authorization` header required)

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "Password123!"
}
```

**Response 200:**
```json
{
  "data": {
    "accessToken": "eyJ...jwt...",
    "expiresIn": 900,
    "user": {
      "id": "uuid",
      "name": "Sam Lee",
      "role": "admin"
    }
  }
}
```

- Sets httpOnly, Secure, SameSite=Strict cookie with refresh token.

**Error Codes:**  
- `401 INVALID_CREDENTIALS` — wrong credentials  
- `403 FORBIDDEN` — account locked

---

### 1.2 Token Refresh

**POST** `/api/v1/auth/refresh`

- **Public** (uses refresh token httpOnly cookie)

**Response 200:**
```json
{
  "data": {
    "accessToken": "eyJ...jwt...",
    "expiresIn": 900
  }
}
```

**Error Codes:**
- `401 TOKEN_INVALID` — refresh token not found/expired  
- `401 FORBIDDEN` — family attack (all tokens invalidated)

---

### 1.3 Logout

**POST** `/api/v1/auth/logout`

- **Public** (cookie required, no token)

**Response:**  
- `204 No Content` (always)

---

### 1.4 Forgot Password

**POST** `/api/v1/auth/forgot-password`

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response:**  
- `200 OK` — always, never reveals existence of the account

---

### 1.5 Reset Password

**POST** `/api/v1/auth/reset-password`

**Request Body:**
```json
{
  "token": "32_byte_hex",
  "password": "NewPa$$w0rd2024!",
  "confirmPassword": "NewPa$$w0rd2024!"
}
```

**Response:**  
- `200 OK` — password updated, all sessions invalidated

**Error Codes:**
- `400 VALIDATION_ERROR` — passwords do not match/strong enough
- `400 INVALID_CREDENTIALS` — token invalid/expired

---

## Users

Users are managed by administrator accounts.  
Authentication always required (`admin` role unless otherwise specified).

### 2.1 List Users

**GET** `/api/v1/users`

- **Auth:** `admin` role required

**Query Params:**  
- `page`, `limit`, `sortBy`, `sortOrder` (pagination, see API conventions)
- `search` — matches user name or user ID (optional)

**Response 200:**
```json
{
  "data": [
    {
      "id": "uuid",
      "userId": "fng001",
      "userName": "Fran Ng",
      "role": "staff",
      "isActive": true,
      "mustChangePassword": false,
      "idleTimeoutMins": 15,
      "loginAttempts": 1,
      "createdAt": "2026-04-20T10:00:00.000Z",
      "updatedAt": "2026-04-20T10:10:00.000Z"
    }
  ],
  "meta": { "total": 1, "page": 1, "limit": 20, "totalPages": 1 }
}
```

---

### 2.2 Get Single User

**GET** `/api/v1/users/{id}`

- **Auth:** `admin` role required

**Response 200:**
```json
{
  "data": {
    "id": "uuid",
    "userId": "fng001",
    "userName": "Fran Ng",
    "role": "staff",
    "isActive": true,
    "mustChangePassword": false,
    "idleTimeoutMins": 15,
    "loginAttempts": 1,
    "createdAt": "2026-04-20T10:00:00.000Z",
    "updatedAt": "2026-04-20T10:10:00.000Z"
  }
}
```

**Error Codes:**  
- `404 NOT_FOUND`

---

### 2.3 Create User

**POST** `/api/v1/users`

- **Auth:** `admin` role required

**Request Body:**
```json
{
  "userId": "fng001",
  "userName": "Fran Ng",
  "role": "staff",
  "isActive": true,
  "idleTimeoutMins": 15,
  "mustChangePassword": true,
  "password": "StrongPa$$123!"
}
```

**Response 201:**
```json
{
  "data": {
    "id": "uuid",
    "userId": "fng001",
    "userName": "Fran Ng",
    "role": "staff",
    "isActive": true,
    "mustChangePassword": true,
    "idleTimeoutMins": 15,
    "loginAttempts": 0,
    "createdAt": "2026-04-20T10:00:00.000Z",
    "updatedAt": "2026-04-20T10:00:00.000Z"
  }
}
```

**Error Codes:**
- `400 VALIDATION_ERROR` — passwords too weak, missing fields
- `409 CONFLICT` — userId already exists

---

### 2.4 Update User

**PATCH** `/api/v1/users/{id}`

- **Auth:** `admin` role required

**Request Body:** (any subset)
```json
{
  "userName": "Frances Ng",
  "role": "manager",
  "isActive": false,
  "mustChangePassword": true,
  "idleTimeoutMins": 20
}
```
- Password changes must go through `/api/v1/users/{id}/change-password` (see below).

**Response 200:**
```json
{
  "data": {
    "id": "uuid",
    "userId": "fng001",
    "userName": "Frances Ng",
    "role": "manager",
    "isActive": false,
    "mustChangePassword": true,
    "idleTimeoutMins": 20,
    "loginAttempts": 0,
    "createdAt": "2026-04-20T10:00:00.000Z",
    "updatedAt": "2026-04-21T08:00:00.000Z"
  }
}
```

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `404 NOT_FOUND`  
- `409 CONFLICT`  

---

### 2.5 Change Own Password

**POST** `/api/v1/users/self/change-password`

- **Auth:** authenticated user

**Request Body:**
```json
{
  "currentPassword": "OldPa$$word1",
  "newPassword": "Stronger2026!",
  "confirmPassword": "Stronger2026!"
}
```

**Response:**  
- `204 No Content`

**Error Codes:**  
- `400 VALIDATION_ERROR` — passwords too weak, mismatch  
- `401 INVALID_CREDENTIALS` — wrong current password  
- `422 BUSINESS_RULE_VIOLATION` — password matches old password(s)

---

### 2.6 Change Another User's Password

**POST** `/api/v1/users/{id}/change-password`

- **Auth:** `admin` role only

**Request Body:**
```json
{
  "newPassword": "AdminSetPassword2026!",
  "confirmPassword": "AdminSetPassword2026!"
}
```

**Response:**  
- `204 No Content`

**Error Codes:**  
- All above, plus `404 NOT_FOUND`

---

## Roles & Access Control

### 3.1 List Roles

**GET** `/api/v1/roles`

- **Auth:** `admin` required

**Response:**
```json
{
  "data": [
    { "id": "uuid", "roleName": "admin", "displayName": "Administrator", "description": "Full access" }
  ],
  "meta": { "total": 1, "page": 1, "limit": 50, "totalPages": 1 }
}
```

---

### 3.2 Role Access Matrix

**GET** `/api/v1/roles/{id}/permissions`

- **Auth:** `admin` required

**Response:**  
```json
{
  "data": {
    "roleId": "uuid",
    "roleName": "admin",
    "permissions": [
      { "module": "dashboard", "canView": true, "canEdit": true },
      { "module": "booking", "canView": true, "canEdit": true }
    ]
  }
}
```

---

### 3.3 Update Role Access Matrix

**PUT** `/api/v1/roles/{id}/permissions`

- **Auth:** `admin` required

**Request Body:**  
```json
{
  "permissions": [
    { "module": "booking", "canView": true, "canEdit": false }
  ]
}
```

**Response:**
```json
{
  "data": {
    "roleId": "uuid",
    "roleName": "staff",
    "permissions": [
      { "module": "booking", "canView": true, "canEdit": false }
    ]
  }
}
```

---

## Rooms

### 4.1 List Rooms

**GET** `/api/v1/rooms`

- **Auth:** `admin`, `manager`  
- (Optional) `dashboard` view: add `status=free|occupied|housekeeping|maintenance`  
- Filter: `roomType`, `search` (partial match on name/location)

**Query Params:**  
- `status`, `roomType`, `search`, pagination

**Response:**  
```json
{
  "data": [
    {
      "id": "uuid",
      "roomShortName": "101A",
      "roomLongName": "Deluxe 101A, East Wing",
      "roomTypeId": "uuid",
      "roomTypeName": "Deluxe",
      "price": "168.5000",
      "status": "free",
      "bookingId": null,
      "createdAt": "2026-04-20T10:00:00.000Z",
      "updatedAt": "2026-04-20T10:00:00.000Z"
    }
  ],
  "meta": { "total": 10, "page": 1, "limit": 20, "totalPages": 1 }
}
```

---

### 4.2 Get Room

**GET** `/api/v1/rooms/{id}`

- **Auth:** `admin`, `manager`

**Response:**  
Same as above, single `data` object.

**Error Codes:**  
- `404 NOT_FOUND`

---

### 4.3 Create Room

**POST** `/api/v1/rooms`

- **Auth:** `admin`, `manager`

**Request Body:**  
```json
{
  "roomShortName": "101A",
  "roomLongName": "Deluxe 101A, East Wing",
  "roomTypeId": "uuid",
  "price": "168.5000",
  "status": "free"
}
```

**Response 201:**  
Resource with generated `id`, all properties + timestamps.

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `422 BUSINESS_RULE_VIOLATION` — cannot create duplicate name in type

---

### 4.4 Update Room

**PATCH** `/api/v1/rooms/{id}`

- **Auth:** `admin`, `manager`

**Request Body:** (any updatable field)
```json
{
  "roomShortName": "102A",
  "roomLongName": "Deluxe 102A, East Wing",
  "roomTypeId": "uuid",
  "price": "198.0000",
  "status": "housekeeping"
}
```

**Response 200:**  
Resource with updated fields.

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `404 NOT_FOUND`  

---

### 4.5 Update Room Status

**POST** `/api/v1/rooms/{id}/status`

- **Auth:** `admin`, `manager`

**Request Body:**  
```json
{
  "status": "occupied" // One of: free, occupied, housekeeping, maintenance
}
```

**Response 200:**  
Updated room object.

**Error Codes:**  
- `422 BUSINESS_RULE_VIOLATION` — invalid status transition  
- `404 NOT_FOUND`

---

## Room Types

### 5.1 List Room Types

**GET** `/api/v1/room-types`

- **Auth:** `admin`, `manager`

**Response:**  
```json
{
  "data": [
    {
      "id": "uuid",
      "typeShortName": "DLX",
      "typeLongName": "Deluxe",
      "isActive": true,
      "createdAt": "2026-04-20T10:00:00.000Z",
      "updatedAt": "2026-04-20T10:00:00.000Z"
    }
  ],
  "meta": { "total": 2, "page": 1, "limit": 20, "totalPages": 1 }
}
```

---

### 5.2 Create Room Type

**POST** `/api/v1/room-types`

- **Auth:** `admin`, `manager`

**Request Body:**
```json
{
  "typeShortName": "SUI",
  "typeLongName": "Suite",
  "isActive": true
}
```

**Response 201:**  
Resource as above.

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `409 CONFLICT` — duplicate short/long name

---

### 5.3 Update Room Type

**PATCH** `/api/v1/room-types/{id}`

- **Auth:** `admin`, `manager`

**Request Body:**  
(any subset)
```json
{
  "typeShortName": "SUI",
  "typeLongName": "Suite B",
  "isActive": false
}
```

**Response 200:**  
Updated resource.

**Error Codes:**  
- `404 NOT_FOUND`  
- `409 CONFLICT` — changing in-use type

---

### 5.4 Delete Room Type

**DELETE** `/api/v1/room-types/{id}`

- **Auth:** `admin`, `manager`

**Response:**  
- `204 No Content`

**Error Codes:**  
- `422 BUSINESS_RULE_VIOLATION` — type in use by one or more active rooms

---

## Bookings

### 6.1 List Bookings

**GET** `/api/v1/bookings`

- **Auth:** `admin`, `manager`, `staff`
- **Query Params:**
  - `page`, `limit`, `sortBy`, `sortOrder` (as standard)
  - `status`: one or comma-separated of booking status values
  - `fromDate`, `toDate`: filter by check-in/check-out range (ISO 8601 date)
  - `roomId`, `customerId`, `search` (find by guest name/contact/passport/substr)

**Response:**  
```json
{
  "data": [
    {
      "id": "uuid",
      "guestName": "Jane Tan",
      "roomId": "uuid",
      "roomTypeId": "uuid",
      "checkIn": "2026-04-22T08:30:00.000Z",
      "checkOut": "2026-04-23T11:00:00.000Z",
      "guestContact": "Singapore, +65-9123-4567",
      "guestOrigin": "Singapore",
      "guestPassport": "E1234567",
      "status": "booked",
      "subTotal": "298.0000",
      "deposit": "100.0000",
      "payment": "198.0000",
      "refund": "0.0000",
      "createdBy": "uuid",
      "createdAt": "2026-04-20T10:00:00.000Z",
      "updatedBy": "uuid",
      "updatedAt": "2026-04-20T13:00:00.000Z"
    }
  ],
  "meta": { "total": 1, "page": 1, "limit": 20, "totalPages": 1 }
}
```

---

### 6.2 Get Booking

**GET** `/api/v1/bookings/{id}`

- **Auth:** `admin`, `manager`, `staff` (permissions checked in service)

**Response:**  
Single resource as above.

**Error Codes:**  
- `404 NOT_FOUND`  
- `403 FORBIDDEN` — insufficient permissions

---

### 6.3 Create Booking

**POST** `/api/v1/bookings`

- **Auth:** `admin`, `manager`, `staff`

**Request Body:**
```json
{
  "guestName": "Jane Tan",
  "roomId": "uuid",
  "roomTypeId": "uuid", // can be inferred from roomId
  "checkIn": "2026-04-22T08:30:00.000Z",
  "checkOut": "2026-04-23T11:00:00.000Z",
  "guestContact": "Singapore, +65-9123-4567",
  "guestOrigin": "Singapore",
  "guestPassport": "E1234567",
  "deposit": "100.0000",
  "payment": "198.0000",
  "refund": "0.0000"
}
```

- `roomId` required; room must be available for interval
- Subtotal automatically calculated (from stay/room price)
- Named fields only

**Response 201:**  
Booking as above.

**Error Codes:**  
- `400 VALIDATION_ERROR` — missing required fields, invalid formats  
- `409 CONFLICT` — overlapping active booking  
- `422 BUSINESS_RULE_VIOLATION` — e.g., check-out before check-in

---

### 6.4 Update Booking

**PATCH** `/api/v1/bookings/{id}`

- **Auth:** `admin`, `manager`, `staff` (ownership and role restrictions enforced by service)

**Request Body:** (any modifiable fields)
```json
{
  "guestContact": "Japan, +81-999-9999",
  "deposit": "120.0000"
}
```

**Response 200:**  
Updated booking as above.

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `404 NOT_FOUND`  
- `409 CONFLICT`  
- `422 BUSINESS_RULE_VIOLATION`

---

### 6.5 Booking Check-In / Check-Out

**POST** `/api/v1/bookings/{id}/status`

- **Auth:** `admin`, `manager`, `staff`

**Request Body:**  
```json
{
  "action": "check-in" // or "check-out"
}
```

**Response 200:**  
Updated booking (with new status, updated timestamps).

**Error Codes:**  
- `400 VALIDATION_ERROR`  
- `422 BUSINESS_RULE_VIOLATION` — invalid transition e.g., cannot check-in cancelled booking

---

### 6.6 Void Booking

**POST** `/api/v1/bookings/{id}/void`

- **Auth:** `admin`, `manager`, `staff` (ownership and access validated at service)

**Request Body:**  
```json
{
  "reason": "Customer cancelled before arrival"
}
```

**Response 200:**  
Booking with status updated to `"voided"`.

**Error Codes:**  
- `422 BUSINESS_RULE_VIOLATION` — cannot void if already checked in/out/voided

---

### 6.7 Export Booking Receipt

**GET** `/api/v1/bookings/{id}/receipt`

- **Auth:** `admin`, `manager`, `staff`  
- Format: JSON (PDF/print handled client-side or downloadable file in v2)

**Query Params:**  
- `type=official|temporary` (optional, default: `"official"`)

**Response 200:**  
```json
{
  "data": {
    "bookingId": "uuid",
    "guestName": "Jane Tan",
    "room": "101A",
    "roomType": "Deluxe",
    "checkIn": "2026-04-22T08:30:00.000Z",
    "checkOut": "2026-04-23T11:00:00.000Z",
    "subTotal": "298.0000",
    "deposit": "100.0000",
    "payment": "198.0000",
    "refund": "0.0000",
    "status": "checked-in",
    "issuedAt": "2026-04-20T13:45:00.000Z",
    "issuerName": "Fran Ng"
    // add other business-specified fields
  }
}
```

---

## Room Status Management

Handled via room updates (`PATCH /rooms/{id}`), or for bulk/status grid, use:

### 8.1 Dashboard Rooms Status Grid

**GET** `/api/v1/rooms/status-grid`

- **Auth:** `admin`, `manager`

**Response:**
```json
{
  "data": [
    {
      "roomId": "uuid",
      "roomShortName": "101A",
      "type": "Deluxe",
      "status": "occupied",
      "bookingId": "uuid",
      "checkIn": "2026-04-22T08:30:00.000Z",
      "checkOut": "2026-04-23T11:00:00.000Z"
    }
  ]
}
```

---

### 8.2 Dashboard Summary

**GET** `/api/v1/dashboard/summary`

- **Auth:** `admin`, `manager`

**Response:**
```json
{
  "data": {
    "roomsFree": 8,
    "roomsOccupied": 10,
    "roomsMaintenance": 2,
    "bookingsToday": 5,
    "alerts": [
      {
        "type": "overstay",
        "count": 1,
        "details": [
          {
            "bookingId": "uuid",
            "guestName": "Jane Tan",
            "room": "101A",
            "checkOut": "2026-04-23T11:00:00.000Z"
          }
        ]
      }
    ]
  }
}
```

- Alert display is determined by business rules.

---

## Customer Search & History

### 9.1 Search Customers/Bookings

**GET** `/api/v1/customers/search`

- **Auth:** `admin`, `manager`, `staff`
- **Query Params:**
  - `guestName`
  - `guestContact`
  - `guestOrigin`
  - `guestPassport`
  - `search` (applies to any field)
  - pagination

**Response:**
```json
{
  "data": [
    {
      "bookingId": "uuid",
      "guestName": "Jane Tan",
      "guestContact": "SG +65-9123-4567",
      "guestOrigin": "Singapore",
      "guestPassport": "E1234567",
      "lastStay": "2026-03-10T15:00:00.000Z",
      "numStays": 2
    }
  ],
  "meta": { "total": 1, "page": 1, "limit": 20, "totalPages": 1 }
}
```

---

### 9.2 Get Customer Booking History

**GET** `/api/v1/customers/{bookingId}/history`

- **Auth:** `admin`, `manager`, `staff`

**Response:**
```json
{
  "data": {
    "guestName": "Jane Tan",
    "guestContact": "SG +65-9123-4567",
    "guestOrigin": "Singapore",
    "guestPassport": "E1234567",
    "bookings": [
      {
        "bookingId": "uuid",
        "roomId": "uuid",
        "roomShortName": "101A",
        "checkIn": "2026-02-15T10:00:00.000Z",
        "checkOut": "2026-02-16T10:00:00.000Z",
        "status": "completed",
        "subTotal": "208.0000"
      }
    ]
  }
}
```

---

### 9.3 Export Customer Transaction History

**GET** `/api/v1/customers/{bookingId}/history/export`

- **Auth:** `admin`, `manager`
- **Response:**  
  - JSON object as above (for download/print).
  - CSV or other formats: planned for v2.

---

## Reports

### 10.1 List Reports

**GET** `/api/v1/reports`

- **Auth:** `admin`, `manager`
- **Query Params:** none

**Response:**
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Daily Booking Report",
      "description": "List of bookings for the selected day",
      "parameters": [
        { "name": "date", "type": "date", "required": true }
      ]
    }
  ]
}
```

---

### 10.2 Generate Report

**POST** `/api/v1/reports/{id}/generate`

- **Auth:** `admin`, `manager`
- **Request Body:**
```json
{
  "parameters": {
    "date": "2026-05-01"
  }
}
```

**Response:**
```json
{
  "data": {
    "reportId": "uuid",
    "name": "Daily Booking Report",
    "generatedAt": "2026-05-01T02:00:00.000Z",
    "parameters": { "date": "2026-05-01" },
    "rows": [
      { "bookingId": "uuid", "guestName": "Jane Tan", /* ...fields as per report schema*/ }
    ]
  }
}
```

---

### 10.3 Export/Download Report

**GET** `/api/v1/reports/{id}/export`

- **Auth:** `admin`, `manager`
- **Query Params:**
  - All report parameters as query params (e.g. `date=2026-05-01`)
  - `format=json|csv`

**Response 200:**  
- JSON envelope as above (for `"json"`), or file download (for `"csv"`)  

- `Content-Disposition: attachment; filename=DailyBookingReport-2026-05-01.csv`

---

### 10.4 Create/Update Report Definition

**POST / PUT** `/api/v1/reports`  
**PUT** `/api/v1/reports/{id}`

- **Auth:** `admin` only

**Request Body:**
```json
{
  "name": "New Weekly Staff Report",
  "description": "Weekly bookings by staff member",
  "parameters": [
    { "name": "startDate", "type": "date", "required": true }
  ],
  "reportQuery": "SELECT ...", // abstract or SQL for v1
  "dateField": "bookingDate"
}
```

**Response:**  
- 201 / 200, newly defined/updated report

**Error Codes:**  
- `422 BUSINESS_RULE_VIOLATION` — report definition invalid

---

## Audit & Logging

### 11.1 List Audit Events (Bookings)

**GET** `/api/v1/audit/bookings`

- **Auth:** `admin` only
- Query params: `bookingId`, `fromDate`, `toDate`, pagination

**Response:**
```json
{
  "data": [
    {
      "id": "uuid",
      "bookingId": "uuid",
      "action": "create",
      "changedBy": "uuid",
      "changedAt": "2026-04-19T18:30:00.000Z",
      "previous": null,
      "current": {
        /* booking record snapshot */
      }
    }
  ],
  "meta": { ... }
}
```

---

### 11.2 List Audit Events (Rooms)

**GET** `/api/v1/audit/rooms`

- **Auth:** `admin` only
- Query params: `roomId`, date filters, pagination

**Response:**  
See above

---

### 11.3 List Error Logs

**GET** `/api/v1/audit/errors`

- **Auth:** `admin` only
- Query params: `fromDate`, `toDate`, `userId`, pagination

**Response:**
```json
{
  "data": [
    {
      "id": "uuid",
      "errorDateTime": "2026-04-21T13:55:00.000Z",
      "errorDescription": "Database timeout: ...",
      "module": "booking.service",
      "method": "saveBooking",
      "userId": "uuid"
    }
  ],
  "meta": { ... }
}
```

---

## Health & Diagnostics

### 12.1 Health Check (Public)

**GET** `/api/health`

- **Auth:** none (public endpoint)

**Response:**
```json
{
  "status": "ok",
  "version": "1.0.0",
  "database": "connected",
  "uptime": 372384
}
```

- `200 OK` if healthy, `503` if DB unreachable.

---

## Error Codes (Summary)

| Code                     | Status | Notes                        |
|--------------------------|--------|------------------------------|
| `VALIDATION_ERROR`       | 400    | Schema validation failure    |
| `INVALID_CREDENTIALS`    | 401    | Login/wrong password/token   |
| `TOKEN_EXPIRED`          | 401    | JWT expired                  |
| `TOKEN_INVALID`          | 401    | JWT or refresh token invalid |
| `FORBIDDEN`              | 403    | Not permitted                |
| `NOT_FOUND`              | 404    | Resource not found           |
| `CONFLICT`               | 409    | Duplicate/conflicting data   |
| `BUSINESS_RULE_VIOLATION`| 422    | Broken business rule         |
| `RATE_LIMITED`           | 429    | Exceeded rate limit          |
| `INTERNAL_ERROR`         | 500    | Unexpected error             |

---

## Types Overview

**Booking Status values:**  
`booked`, `checked-in`, `checked-out`, `voided`, `cancelled`, `completed`  
_(Confirm with business as per PRD gap G-01)_

**Room Status values:**  
`free`, `occupied`, `housekeeping`, `maintenance`

**User Roles:**  
`admin`, `manager`, `staff`, `readonly` (for viewer/auditor if needed)

**Monetary Amounts:**  
All prices, deposits, payments, and refunds are represented as **strings** with 4 decimal places in both requests and responses.

---

## Notes

- All protected endpoints require `Authorization: Bearer {accessToken}` header.
- Null fields are always present in objects where relevant.
- Pagination follows the [api-contracts.md](#) conventions for all `GET` collection endpoints.
- All error responses are structured as:
  ```json
  {
    "error": "Human-readable message",
    "code": "MACHINE_READABLE_CODE",
    "details": [{ "field": "fieldName", "message": "Validation failure message" }]
  }
  ```

---

**End of API Specification (v1)**
