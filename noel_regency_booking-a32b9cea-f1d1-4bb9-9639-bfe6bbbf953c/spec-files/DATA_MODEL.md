# DATA_MODEL.md

## Entity-Relationship Diagram (ERD) — Text Description

The Hotel Booking System schema is designed for a PostgreSQL database, using `UUID` as primary keys for all entities and following soft-delete and auditing best practices throughout. Below is a text-based ERD describing all entities, key relationships, and main constraints:

- **users** *(User Account)*  
  - Each user is assigned a single role (user group).
  - Responsible for authentication and access control.
  - Linked to bookings, room/room type/report/user modifications (for auditing).
- **user_groups** *(Role/Group)*  
  - Defines application roles and their privilege levels.
  - Referenced by users.
- **bookings**  
  - References a guest (inline guest data), a room, type/status, audit fields.
  - Stores payment info and state for each reservation.
  - Linked to **rooms** (one booking per active room at a time).
- **rooms**  
  - Maintains physical room inventory.
  - Linked to **room_types** and optionally to an active booking.
  - Tracks operational state (available, occupied, etc).
- **room_types**  
  - Catalogues categories for rooms and pricing.
  - Enforced constraints to prevent removal if referenced by any non-deleted room.
- **reports**  
  - Metadata and SQL templates for operational/business reports.
  - Maintained by admins.
- **module_access**  
  - Connects user group (role) to permissions per feature/module.
- **log_bookings**  
  - Auditing: all booking changes (snapshots with who/when/what).
- **log_rooms**  
  - Auditing: all room/room status changes (snapshots with who/when/what).
- **log_errors**  
  - System/application errors for troubleshooting and compliance.
- **refresh_tokens**  
  - Stores hashed refresh tokens for authentication (JWT refresh flow).
- **password_resets**  
  - Stores password reset tokens and expiry.
- **booking_history**  
  - (Optional, for full temporal auditing. For this MVP, `log_bookings` suffices.)

**Relations:**
- `users.user_group_id → user_groups.id`
- `bookings.room_id → rooms.id`
- `rooms.room_type_id → room_types.id`
- `bookings.created_by/last_modified_by/voided_by → users.id`
- `rooms.created_by/last_modified_by → users.id`
- `room_types.created_by/last_modified_by → users.id`
- `reports.created_by/last_modified_by → users.id`
- `module_access.user_group_id → user_groups.id`
- `module_access.module → ENUM of modules/features`
- All log tables reference entity and user UUIDs.

---

## Table Definitions

### 1. users

| Column                | Type                        | Constraints                                                  |
|-----------------------|----------------------------|--------------------------------------------------------------|
| id                    | UUID                       | PK, DEFAULT gen_random_uuid(), NOT NULL                      |
| user_id               | VARCHAR(64)                | UNIQUE, NOT NULL                                             |
| user_name             | VARCHAR(100)               | NOT NULL                                                     |
| user_group_id         | UUID                       | FK → user_groups(id), NOT NULL                               |
| password_hash         | VARCHAR(255)               | NOT NULL                                                     |
| password_salt         | VARCHAR(64)                | NOT NULL                                                     |
| login_attempts        | INTEGER                    | NOT NULL, DEFAULT 0                                          |
| last_failed_login_at  | TIMESTAMPTZ                | NULLABLE                                                     |
| is_locked             | BOOLEAN                    | NOT NULL, DEFAULT FALSE                                      |
| last_login_at         | TIMESTAMPTZ                | NULLABLE                                                     |
| must_change_password  | BOOLEAN                    | NOT NULL, DEFAULT FALSE                                      |
| idle_timeout_minutes  | INTEGER                    | NOT NULL, DEFAULT 20                                         |
| is_active             | BOOLEAN                    | NOT NULL, DEFAULT TRUE                                       |
| created_at            | TIMESTAMPTZ                | NOT NULL, DEFAULT NOW()                                      |
| updated_at            | TIMESTAMPTZ                | NOT NULL, DEFAULT NOW()                                      |
| deleted_at            | TIMESTAMPTZ                | NULLABLE                                                     |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`user_id`)
- FK: `user_group_id` → `user_groups(id)` (RESTRICT on delete)
- Partial Index: `idx_users_active` on (`is_active`) WHERE `deleted_at IS NULL`

---

### 2. user_groups

| Column        | Type           | Constraints                                      |
|---------------|---------------|--------------------------------------------------|
| id            | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL          |
| group_name    | VARCHAR(40)   | UNIQUE, NOT NULL                                 |
| group_desc    | TEXT          |                                                  |
| security_level| INTEGER       | NOT NULL                                         |
| created_by    | UUID          | FK → users(id), NOT NULL                         |
| created_at    | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                          |
| updated_at    | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                          |
| deleted_at    | TIMESTAMPTZ   | NULLABLE                                         |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`group_name`)
- FK: `created_by` → `users(id)` (RESTRICT on delete)

---

### 3. rooms

| Column          | Type          | Constraints                                                   |
|-----------------|--------------|---------------------------------------------------------------|
| id              | UUID         | PK, DEFAULT gen_random_uuid(), NOT NULL                       |
| room_short_name | VARCHAR(20)  | NOT NULL                                                      |
| room_long_name  | VARCHAR(100) | NOT NULL                                                      |
| room_type_id    | UUID         | FK → room_types(id), NOT NULL                                 |
| room_price      | NUMERIC(19,4)| NOT NULL DEFAULT 0                                            |
| room_status     | VARCHAR(20)  | NOT NULL CHECK (room_status IN ('free','occupied','housekeeping','maintenance'))     |
| booking_id      | UUID         | FK → bookings(id), NULLABLE                                   |
| is_active       | BOOLEAN      | NOT NULL DEFAULT TRUE                                         |
| created_by      | UUID         | FK → users(id), NOT NULL                                      |
| created_at      | TIMESTAMPTZ  | NOT NULL, DEFAULT NOW()                                       |
| updated_at      | TIMESTAMPTZ  | NOT NULL, DEFAULT NOW()                                       |
| deleted_at      | TIMESTAMPTZ  | NULLABLE                                                     |

**Indexes and Constraints:**
- PK: `id`
- Index: `idx_rooms_room_type_id` on (`room_type_id`)
- Index: `idx_rooms_booking_id` on (`booking_id`)
- Partial Index: `idx_rooms_status_active` on (`room_status`, `is_active`) WHERE `deleted_at IS NULL`
- Unique: (`room_short_name`) WHERE `deleted_at IS NULL`
- FK: `room_type_id` → `room_types(id)`
- FK: `booking_id` → `bookings(id)` (`SET NULL` ON DELETE)
- FK: `created_by` → `users(id)`

---

### 4. room_types

| Column         | Type           | Constraints                                  |
|----------------|---------------|----------------------------------------------|
| id             | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL      |
| type_short_name| VARCHAR(30)   | UNIQUE, NOT NULL                             |
| type_long_name | VARCHAR(100)  |                                              |
| price_default  | NUMERIC(19,4) | NOT NULL DEFAULT 0                           |
| is_active      | BOOLEAN       | NOT NULL DEFAULT TRUE                        |
| created_by     | UUID          | FK → users(id), NOT NULL                     |
| created_at     | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                      |
| updated_at     | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                      |
| deleted_at     | TIMESTAMPTZ   | NULLABLE                                     |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`type_short_name`)
- FK: `created_by` → `users(id)`
- Enforced by application: Cannot delete or modify if referenced by any non-deleted room

---

### 5. bookings

| Column               | Type           | Constraints                                                        |
|----------------------|---------------|--------------------------------------------------------------------|
| id                   | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL                            |
| booking_number       | VARCHAR(32)   | UNIQUE, NOT NULL                                                   |
| room_id              | UUID          | FK → rooms(id), NOT NULL                                           |
| guest_name           | VARCHAR(100)  | NOT NULL                                                           |
| guest_contact        | VARCHAR(40)   |                                                                    |
| guest_origin         | VARCHAR(80)   |                                                                    |
| guest_passport       | VARCHAR(40)   |                                                                    |
| check_in_at          | TIMESTAMPTZ   | NOT NULL                                                           |
| check_out_at         | TIMESTAMPTZ   | NOT NULL                                                           |
| stay_nights          | INTEGER       | NOT NULL, CHECK (stay_nights >= 1)                                 |
| subtotal             | NUMERIC(19,4) | NOT NULL DEFAULT 0                                                 |
| deposit              | NUMERIC(19,4) | NOT NULL DEFAULT 0                                                 |
| payment              | NUMERIC(19,4) | NOT NULL DEFAULT 0                                                 |
| refund               | NUMERIC(19,4) | NOT NULL DEFAULT 0                                                 |
| status               | VARCHAR(20)   | NOT NULL CHECK (status IN ('reserved','checked_in','checked_out','voided','cancelled'))  |
| voided_at            | TIMESTAMPTZ   | NULLABLE                                                           |
| voided_by            | UUID          | FK → users(id), NULLABLE                                           |
| remarks              | TEXT          |                                                                    |
| created_by           | UUID          | FK → users(id), NOT NULL                                           |
| created_at           | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                                            |
| updated_by           | UUID          | FK → users(id), NULLABLE                                           |
| updated_at           | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                                            |
| deleted_at           | TIMESTAMPTZ   | NULLABLE                                                          |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`booking_number`)
- FK: `room_id` → `rooms(id)` (RESTRICT on delete)
- FK: `created_by` → `users(id)`
- FK: `updated_by` → `users(id)`
- FK: `voided_by` → `users(id)`
- Partial Index: `idx_bookings_room_id_status` on (`room_id`, `status`) WHERE `deleted_at IS NULL`
- Full-text Index: On (`guest_name`, `guest_contact`, `guest_origin`, `guest_passport`)
- Application logic must ensure one active booking per room for a date range (overlap prevention).

---

### 6. reports

| Column          | Type           | Constraints                                 |
|-----------------|---------------|---------------------------------------------|
| id              | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL     |
| report_name     | VARCHAR(64)   | UNIQUE, NOT NULL                            |
| title           | VARCHAR(100)  | NOT NULL                                    |
| sql_query       | TEXT          | NOT NULL                                    |
| date_field      | VARCHAR(40)   | NOT NULL                                    |
| is_active       | BOOLEAN       | NOT NULL, DEFAULT TRUE                      |
| created_by      | UUID          | FK → users(id), NOT NULL                    |
| created_at      | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                     |
| updated_by      | UUID          | FK → users(id), NULLABLE                    |
| updated_at      | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                     |
| deleted_at      | TIMESTAMPTZ   | NULLABLE                                    |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`report_name`)
- FK: `created_by` → `users(id)`
- FK: `updated_by` → `users(id)`

---

### 7. module_access

| Column         | Type           | Constraints                                          |
|----------------|---------------|------------------------------------------------------|
| id             | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL              |
| user_group_id  | UUID          | FK → user_groups(id), NOT NULL                       |
| module         | VARCHAR(40)   | NOT NULL                                             |
| can_view       | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_create     | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_update     | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_delete     | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_export     | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_edit       | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| can_print      | BOOLEAN       | NOT NULL DEFAULT FALSE                               |
| created_at     | TIMESTAMPTZ   | NOT NULL DEFAULT NOW()                               |
| updated_at     | TIMESTAMPTZ   | NOT NULL DEFAULT NOW()                               |
| deleted_at     | TIMESTAMPTZ   | NULLABLE                                             |

**Indexes and Constraints:**
- PK: `id`
- Unique: (`user_group_id`, `module`)
- FK: `user_group_id` → `user_groups(id)`

---

### 8. log_bookings

| Column           | Type           | Constraints                                  |
|------------------|---------------|----------------------------------------------|
| id               | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL      |
| booking_id       | UUID          | FK → bookings(id), NOT NULL                  |
| guest_name       | VARCHAR(100)  |                                              |
| room_id          | UUID          | FK → rooms(id), NOT NULL                     |
| payment          | NUMERIC(19,4) |                                              |
| refund           | NUMERIC(19,4) |                                              |
| action           | VARCHAR(20)   | NOT NULL CHECK (action IN ('create','edit','void','check_in','check_out')) |
| changed_by       | UUID          | FK → users(id), NOT NULL                     |
| changed_at       | TIMESTAMPTZ   | NOT NULL DEFAULT NOW()                       |

**Indexes and Constraints:**
- PK: `id`
- FK: `booking_id` → `bookings(id)`
- FK: `room_id` → `rooms(id)`
- FK: `changed_by` → `users(id)`
- Index: `idx_log_bookings_booking_id`
- Index: `idx_log_bookings_changed_by`

---

### 9. log_rooms

| Column           | Type           | Constraints                                  |
|------------------|---------------|----------------------------------------------|
| id               | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL      |
| room_id          | UUID          | FK → rooms(id), NOT NULL                     |
| booking_id       | UUID          | FK → bookings(id), NULLABLE                  |
| room_status      | VARCHAR(20)   | NOT NULL CHECK (room_status IN ('free','occupied','housekeeping','maintenance')) |
| remarks          | TEXT          |                                              |
| action           | VARCHAR(20)   | NOT NULL CHECK (action IN ('create','edit','status_change','maintenance'))       |
| changed_by       | UUID          | FK → users(id), NOT NULL                     |
| changed_at       | TIMESTAMPTZ   | NOT NULL DEFAULT NOW()                       |

**Indexes and Constraints:**
- PK: `id`
- FK: `room_id` → `rooms(id)`
- FK: `booking_id` → `bookings(id)`
- FK: `changed_by` → `users(id)`
- Index: `idx_log_rooms_room_id`

---

### 10. log_errors

| Column                | Type           | Constraints                                 |
|-----------------------|---------------|---------------------------------------------|
| id                    | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL     |
| log_datetime          | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                     |
| error_description     | TEXT          | NOT NULL                                    |
| error_method          | VARCHAR(80)   | NOT NULL                                    |
| error_module          | VARCHAR(80)   | NOT NULL                                    |
| user_name             | VARCHAR(100)  | NULLABLE                                    |
| request_id            | VARCHAR(36)   | NULLABLE                                    |
| context               | JSONB         | NULLABLE                                    |

**Indexes and Constraints:**
- PK: `id`
- Index: `idx_log_errors_log_datetime`
- No foreign keys (historic logs are durable even if user deleted)
---

### 11. refresh_tokens

| Column         | Type           | Constraints                                        |
|----------------|---------------|----------------------------------------------------|
| id             | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL            |
| user_id        | UUID          | FK → users(id), NOT NULL                           |
| token_hash     | CHAR(64)      | NOT NULL                                           |
| expires_at     | TIMESTAMPTZ   | NOT NULL                                           |
| issued_at      | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                            |
| used_at        | TIMESTAMPTZ   | NULLABLE                                           |
| is_invalidated | BOOLEAN       | NOT NULL, DEFAULT FALSE                            |
| created_at     | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                            |
| updated_at     | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                            |

**Indexes and Constraints:**
- PK: `id`
- Index: `idx_refresh_tokens_user_id`
- Unique: (`token_hash`)
- FK: `user_id` → `users(id)`

---

### 12. password_resets

| Column         | Type           | Constraints                                        |
|----------------|---------------|----------------------------------------------------|
| id             | UUID          | PK, DEFAULT gen_random_uuid(), NOT NULL            |
| user_id        | UUID          | FK → users(id), NOT NULL                           |
| token_hash     | CHAR(64)      | NOT NULL                                           |
| expires_at     | TIMESTAMPTZ   | NOT NULL                                           |
| used_at        | TIMESTAMPTZ   | NULLABLE                                           |
| created_at     | TIMESTAMPTZ   | NOT NULL, DEFAULT NOW()                            |

**Indexes and Constraints:**
- PK: `id`
- Index: `idx_password_resets_token_hash`
- Unique: (`token_hash`)
- FK: `user_id` → `users(id)`

---

## Relationships Summary

- **users** → **user_groups**: many-to-one (`user_group_id`)
- **bookings** → **rooms**: many-to-one (`room_id`)
- **rooms** → **room_types**: many-to-one (`room_type_id`)
- **bookings, rooms, room_types, reports** → **users**: created_by, updated_by, voided_by, changed_by FK for full audit trails
- **module_access** → **user_groups**: many-to-one
- **log_bookings** → **bookings**, **rooms**, **users** (`changed_by` FK)
- **log_rooms** → **rooms**, **bookings**, **users** (`changed_by` FK)
- **refresh_tokens**, **password_resets** → **users**
- **log_errors**: optionally reference user by `user_name` (no FK for historical durability)

---

## Migration Notes

- All tables have `created_at`, `updated_at`, and `deleted_at` (soft delete) columns.
- Default values provided for UUIDs (`gen_random_uuid()`) and timestamps.
- Application-side triggers enforce business rules not captured by direct constraints (e.g. preventing modification/removal of room types in active use, session lockout after login failure thresholds, unique room assignment per booking per date).
- Full-text search for customer fields: add PostgreSQL `GIN` index over a `tsvector` computed from the customer fields in `bookings`.
- Enum values for columns like `room_status`, `status`, and `action` may be enforced with PostgreSQL `ENUM` types or text `CHECK` constraints as above.
- **History tables:** Full temporal audit (beyond log tables) can be implemented with database triggers if required for compliance — out of scope for MVP unless requested.
- Cascades are avoided where business data is not to be deleted — ON DELETE RESTRICT (default) except for cases like orphaned nullable FKs.
- All foreign keys have explicit indexes for performance.
- No columns permit NULL without justification (see constraints above).
- All unique constraints, FKs, and indexes (especially for search & join performance) explicitly named as per standards.
- To add new modules or permissions, update ENUM values or seed tables as required in migration.
- No hard deletes. When removing data, set `deleted_at` — queries must filter for `IS NULL`.
- Sensitive fields (password hashes, token hashes) are never logged or exposed.
- All non-trivial columns have maximum lengths, especially user-facing input fields.

---

## Drafted Schema (DDL Example)

*Not executable — use this section as a cross-check for migration files or Prisma models.*

```sql
CREATE EXTENSION IF NOT EXISTS "pgcrypto"; -- for gen_random_uuid()

-- Example: users
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id VARCHAR(64) NOT NULL,
  user_name VARCHAR(100) NOT NULL,
  user_group_id UUID NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  password_salt VARCHAR(64) NOT NULL,
  login_attempts INTEGER NOT NULL DEFAULT 0,
  last_failed_login_at TIMESTAMPTZ,
  is_locked BOOLEAN NOT NULL DEFAULT FALSE,
  last_login_at TIMESTAMPTZ,
  must_change_password BOOLEAN NOT NULL DEFAULT FALSE,
  idle_timeout_minutes INTEGER NOT NULL DEFAULT 20,
  is_active BOOLEAN NOT NULL DEFAULT TRUE,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  deleted_at TIMESTAMPTZ,
  UNIQUE(user_id),
  CONSTRAINT fk_users_usergroup FOREIGN KEY (user_group_id) REFERENCES user_groups(id) ON DELETE RESTRICT
);

-- Create similar DDL for each table above, following naming and key conventions from the definitions.

-- Add all named indexes, foreign keys, and soft-delete handling as described.
```

---

## End of DATA_MODEL.md