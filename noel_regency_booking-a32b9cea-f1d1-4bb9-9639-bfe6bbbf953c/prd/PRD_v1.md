# Product Requirements Document (PRD)
**Product Name:** Hotel Booking System  
**Version:** 1.0  
**Status:** Draft  
**Date:** 2026-04-20  
**Prepared By:** AI Business Analyst  
**Source System:** Legacy Hotel Booking System

---

## Table of Contents
1. [Executive Summary](#1-executive-summary)
2. [Goals & Objectives](#2-goals--objectives)
3. [User Personas](#3-user-personas)
4. [User Stories](#4-user-stories)
5. [Functional Requirements](#5-functional-requirements)
6. [Non-Functional Requirements](#6-non-functional-requirements)
7. [Scope](#7-scope)
8. [Assumptions & Constraints](#8-assumptions--constraints)
9. [Screen & Feature Descriptions](#9-screen--feature-descriptions)
10. [Acceptance Criteria](#10-acceptance-criteria)
11. [Data Model Summary](#11-data-model-summary)
12. [Business Rules](#12-business-rules)
13. [Access Control Matrix](#13-access-control-matrix)
14. [Timeline & Milestones](#14-timeline--milestones)
15. [Risks & Mitigations](#15-risks--mitigations)
16. [Gaps & Inferred Requirements](#16-gaps--inferred-requirements)
17. [Sign-Off](#17-sign-off)

---

## 1. Executive Summary

The Hotel Booking System is a business application for managing room bookings, customer records, room availability, user access, and operational reporting for a hotel. It is used by front desk staff, supervisors, and administrators to support daily booking operations, monitor room status, maintain master data, and produce business documents and reports.

The modernized product will centralize booking and room operations, improve control over user permissions, support secure account management, and provide exportable operational reports and receipts. Its business value is to reduce manual effort, improve booking accuracy, strengthen accountability through audit data, and give staff a clear operational view of occupancy and customer activity.

| Attribute        | Detail                        |
|------------------|-------------------------------|
| Product Name     | Hotel Booking System                |
| Business Domain  | Hospitality Operations             |
| Target Platform  | Web application for modern desktop browsers |
| Primary Users    | Front Desk Staff, Hotel Supervisor, System Administrator |
| Business Problem | Hotel staff need a controlled system to manage bookings, room status, customers, users, and reports |
| Expected Benefit | Faster booking operations, better visibility of room usage, stronger access control, and improved reporting |
| Source System    | Legacy Hotel Booking System               |

---

## 2. Goals & Objectives

### 2.1 Business Goals
- Improve operational efficiency for room booking and guest handling.
- Reduce booking, payment, and room status errors through validation and controlled workflows.
- Strengthen accountability through role-based access and activity logging.

### 2.2 Product Goals
- Provide secure login, password management, and role-based access to business modules.
- Enable staff to create, update, void, and track bookings with room and payment details.
- Provide searchable customer history and exportable business reports and receipts.

### 2.3 Success Metrics

| Metric              | Target         | How to Measure        |
|---------------------|----------------|-----------------------|
| Booking completion time          | < 3 minutes     | Time to create and save a booking |
| Failed unauthorized access attempts | 0 successful violations     | Access audit review |
| Report export completion          | < 10 seconds     | Timed export test for standard reports |

---

## 3. User Personas

### 3.1 Front Desk Staff

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Front Desk Staff                |
| Description  | Handles guest bookings, check-in, check-out, receipt generation, and customer lookups. |
| Goals        | Create accurate bookings, assign rooms quickly, record payments, and retrieve guest history. |
| Pain Points  | Manual room tracking, payment mistakes, and difficulty finding prior guest activity. |
| Tech Level   | Basic to intermediate          |
| Usage        | Daily throughout business hours     |
| Modules Used | Authentication, Dashboard, Booking Management, Customer Search, Receipt Export |

### 3.2 Hotel Supervisor

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Hotel Supervisor                |
| Description  | Oversees occupancy, room status changes, customer activity, and operational reporting. |
| Goals        | Monitor room availability, review summaries, manage exceptions, and access reports. |
| Pain Points  | Limited visibility of live room status and delays in operational reporting. |
| Tech Level   | Intermediate          |
| Usage        | Daily, with periodic reporting and oversight tasks     |
| Modules Used | Dashboard, Booking Management, Room Management, Reporting, Customer Search |

### 3.3 System Administrator

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | System Administrator                |
| Description  | Manages users, passwords, roles, and module-level access permissions. |
| Goals        | Control access, maintain active users, enforce password changes, and configure permissions. |
| Pain Points  | Inconsistent permissions and difficulty maintaining secure user access. |
| Tech Level   | Intermediate to advanced          |
| Usage        | Frequent administration and support tasks     |
| Modules Used | User Management, Access Control, Reporting Setup, Authentication |

---

## 4. User Stories

### 4.1 Authentication

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-01  | As a user, I want to sign in with my user ID so that I can access authorized features.       | High     | frmUserLogin::cmdOK_Click       |
| US-02  | As a user, I want to be prompted to change my password when required so that my account remains compliant.       | High     | modFunction::NeedChangePassword       |
| US-03  | As a user, I want to change my password so that I can secure my account.       | High     | frmUserChangePassword::UpdatePassword       |
| US-04  | As an administrator, I want login attempts tracked so that repeated failed access can be controlled.       | Medium   | UserData.LoginAttempts       |
| US-05  | As a user, I want my session to end after inactivity so that unattended access is limited.       | Medium   | frmDialog::tmrClock, UserData.Idle       |

### 4.2 Booking Management

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-06  | As a front desk staff member, I want to create a booking so that I can reserve a room for a guest.       | High     | frmBooking::SaveBooking       |
| US-07  | As a front desk staff member, I want to select an available room so that the booking is assigned correctly.       | High     | frmBooking::SelectRoom       |
| US-08  | As a front desk staff member, I want the system to calculate totals from room price, deposit, payment, and refund so that the guest balance is accurate.       | High     | frmBooking::SumTotal       |
| US-09  | As a front desk staff member, I want to check guests in and out so that room occupancy is recorded correctly.       | High     | frmBooking UIEvent Check IN/OUT, frmBooking::UpdateRoomStatus       |
| US-10  | As a front desk staff member, I want to void a booking when necessary so that invalid or cancelled bookings are not treated as active.       | Medium   | frmBooking::VoidBooking       |

### 4.3 Room Management

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-11  | As a supervisor, I want to maintain room records so that rooms are available for booking with correct details.       | High     | frmRoomMaintain::SaveRecord       |
| US-12  | As a supervisor, I want to maintain room types so that rooms can be grouped and priced appropriately.       | High     | frmRoomTypeMaintain::SaveRoomType       |
| US-13  | As a supervisor, I want to mark rooms as free, occupied, housekeeping, or maintenance so that room availability is accurate.       | High     | frmDashboard mnuFree/mnuOccupied/mnuHousekeeping/mnuMaintenance       |
| US-14  | As a supervisor, I want to see room status from the dashboard so that I can manage hotel operations quickly.       | High     | frmDashboard::GetRoomStatus, frmDashboard::RoomSetup       |
| US-15  | As a supervisor, I want to prevent deletion or change of room types in use so that active room assignments remain valid.       | Medium   | frmRoomTypeMaintain::IsRoomTypeUsed       |

### 4.4 Customer Search & History

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-16  | As a front desk staff member, I want to search customers by guest details so that I can find existing records quickly.       | High     | frmFindCustomer::ListCustomers, txtGuestName/txtGuestContact/txtGuestOrigin/txtGuestPassport       |
| US-17  | As a front desk staff member, I want to view a customer's booking history so that I can understand past stays and transactions.       | High     | frmFindCustomer::ListBooking       |
| US-18  | As a supervisor, I want to export customer transaction history so that I can review guest activity externally.       | Medium   | frmFindCustomer::PrintHistory       |
| US-19  | As a user, I want search filters reset so that I can start a new customer search quickly.       | Low      | frmFindCustomer::ResetFields       |
| US-20  | As a front desk staff member, I want to select a customer from search results so that I can review matching bookings.       | Medium   | frmFindCustomer::lvCustomers_Click       |

### 4.5 User Management & Access Control

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-21  | As an administrator, I want to create and update user accounts so that staff can access the system appropriately.       | High     | frmUserMaintain::SaveUser       |
| US-22  | As an administrator, I want to assign users to roles so that each user receives the correct level of access.       | High     | frmUserMaintain::PopulateGroupName, UserData.UserGroup       |
| US-23  | As an administrator, I want to manage access by module so that each role can only use approved features.       | High     | frmModuleAccess::UpdateModuleAccess, modFunction::UserAccessModule       |
| US-24  | As an administrator, I want to list and review existing users so that I can maintain account accuracy.       | Medium   | frmUserMaintain::ListUsers       |
| US-25  | As an administrator, I want to control whether a user must update their password so that first-time or reset access is secure.       | Medium   | UserData.ChangePassword, frmUserMaintain::chkUpdatePassword       |

### 4.6 Reporting

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-26  | As a supervisor, I want to see a list of available reports so that I can run operational reporting as needed.       | High     | frmReport::LoadReports       |
| US-27  | As a supervisor, I want to export reports so that I can share or archive business information.       | High     | frmReport::PrintReport, MOD_REPORT_EXPORT       |
| US-28  | As an administrator, I want to maintain report definitions so that report content stays current.       | Medium   | frmReportMaintain::SaveReport       |
| US-29  | As a supervisor, I want access to daily, weekly, and monthly booking reports so that I can monitor performance over time.       | High     | REP_DAILY_BOOKING, REP_WEEKLY_BOOKING, REP_MONTHLY_BOOKING       |
| US-30  | As a manager, I want staff-based booking reports so that I can review operational activity by user.       | Medium   | Booking Report by Staff, REP_SHIFT_FOR_USER, REP_SHIFT_ALL_USER       |

---

## 5. Functional Requirements

### 5.1 Authentication

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-01 | The system SHALL authenticate users using a unique user ID and password when they submit login credentials.                      | High     | frmUserLogin::cmdOK_Click, UserData.UserID, UserData.UserPassword    |
| FR-02 | The system SHALL force a user to change password when the account is marked as requiring a password change at login.                      | High     | modFunction::NeedChangePassword, UserData.ChangePassword    |
| FR-03 | The system SHALL allow users to update their own password after current password validation.                     | High     | frmUserChangePassword::CheckPassword, frmUserChangePassword::UpdatePassword    |
| FR-04 | The system SHALL track failed login attempts for each user account when login is unsuccessful.                        | High     | UserData.LoginAttempts    |
| FR-05 | The system SHALL store all passwords using an industry-standard hashing algorithm with a unique salt per user when passwords are created or updated.                        | High     | modEncryption::Encrypt, modEncryption::GenSalt, UserData.Salt    |
| FR-06 | The system SHALL end the user session after a configurable period of inactivity when the idle limit is reached.                      | High     | frmDialog::tmrClock, UserData.Idle    |

### 5.2 Booking Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-07 | The system SHALL create a temporary booking identifier before a booking is finalized when a new booking is started.                      | High     | frmBooking::CreateTempBookingID, frmBooking::GetTempBookingID    |
| FR-08 | The system SHALL save booking records with guest, room, stay, payment, and status details when the user completes the booking process.                      | High     | frmBooking::SaveBooking, Booking table    |
| FR-09 | The system SHALL allow users to select a room for a booking when assigning accommodation to a guest.                     | High     | frmBooking::SelectRoom, frmBooking::GetRoomDetails    |
| FR-10 | The system SHALL calculate the booking subtotal from the booking inputs when booking values change.                     | High     | frmBooking::SumTotal, dblSubTotal, dblRoomPrice    |
| FR-11 | The system SHALL record deposit, payment, and refund amounts when financial values are entered for a booking.                      | High     | frmBooking txtDeposit/txtPayment/txtRefund LostFocus, Booking.Deposit, Booking.Payment, Booking.Refund    |
| FR-12 | The system SHALL update room status when a booking is checked in, checked out, saved, or voided.                      | High     | frmBooking::UpdateRoomStatus, frmBooking UIEvent Check IN/OUT, frmBooking::VoidBooking    |
| FR-13 | The system SHALL allow users to void a booking when the booking must be cancelled or invalidated.                     | Medium   | frmBooking::VoidBooking    |
| FR-14 | The system SHALL export a receipt for a booking when the user requests booking documentation.                        | Medium   | frmBooking::PrintReceipt, Official Receipt, Temporary Receipt    |
| FR-15 | The system SHALL store booking records with created by, created date, last modified by, and last modified date values when records are created or changed.                      | High     | Booking.CreatedBy, Booking.CreatedDate, Booking.LastModifiedBy, Booking.LastModifiedDate    |

### 5.3 Room Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-16 | The system SHALL maintain room records with room number, location, type, price, and status details when authorized users save room data.                      | High     | frmRoomMaintain::SaveRecord, Room table    |
| FR-17 | The system SHALL list rooms for selection and review when users open room management.                      | High     | frmRoomMaintain::ListRooms    |
| FR-18 | The system SHALL maintain room type records with short name and long name when authorized users save room type data.                     | High     | frmRoomTypeMaintain::SaveRoomType, RoomType table    |
| FR-19 | The system SHALL prevent a room type from being removed or changed in a way that breaks active usage when the room type is already in use.                      | High     | frmRoomTypeMaintain::IsRoomTypeUsed    |
| FR-20 | The system SHALL allow authorized users to set a room status to Free, Occupied, Housekeeping, or Maintenance when managing room availability.                      | High     | frmDashboard mnuFree/mnuOccupied/mnuHousekeeping/mnuMaintenance, Room.RoomStatus    |
| FR-21 | The system SHALL display room status on the dashboard when users review current operational availability.                      | High     | frmDashboard::GetRoomStatus, frmDashboard::RoomSetup    |
| FR-22 | The system SHALL display booking summaries on the dashboard when users access the operational overview.                      | Medium   | frmDashboard::ShowSummary1, frmDashboard::ShowSummary2, frmDashboard::ShowSummary3, frmDashboard::ShowSummary4, frmDashboard::ShowSummary5    |
| FR-23 | The system SHALL alert users to bookings that require attention when alert conditions are met.                      | Medium   | frmDashboard::AlertBooking, frmDashboard::NeedBlink    |

### 5.4 Customer Search & History

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-24 | The system SHALL allow users to search customers by guest name when locating existing customer records.                      | High     | frmFindCustomer::ListCustomers, Booking.GuestName    |
| FR-25 | The system SHALL allow users to search customers by contact number when locating existing customer records.                      | High     | frmFindCustomer::ListCustomers, Booking.GuestContact    |
| FR-26 | The system SHALL allow users to search customers by origin when locating existing customer records.                     | Medium   | frmFindCustomer::ListCustomers, Booking.GuestOrigin    |
| FR-27 | The system SHALL allow users to search customers by passport number when locating existing customer records.                        | Medium   | frmFindCustomer::ListCustomers, Booking.GuestPassport    |
| FR-28 | The system SHALL display booking history for a selected customer when the user opens customer history.                      | High     | frmFindCustomer::ListBooking    |
| FR-29 | The system SHALL export customer transaction history when the user requests customer history output.                      | Medium   | frmFindCustomer::PrintHistory, Customer Transaction History report    |

### 5.5 User Management & Access Control

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-30 | The system SHALL create and update user accounts with user ID, user name, role, active status, idle limit, and password settings when administrators save user records.                      | High     | frmUserMaintain::SaveUser, UserData table    |
| FR-31 | The system SHALL list users for review and selection when administrators open user management.                      | High     | frmUserMaintain::ListUsers    |
| FR-32 | The system SHALL assign each user account to a role when administrators maintain user records.                     | High     | frmUserMaintain::PopulateGroupName, UserData.UserGroup, UserGroup table    |
| FR-33 | The system SHALL allow administrators to enable or disable a user account when maintaining access.                      | High     | UserData.Active, frmUserMaintain::SaveUser    |
| FR-34 | The system SHALL allow administrators to mark a user account as requiring password update when maintaining user credentials.                      | Medium   | frmUserMaintain::chkUpdatePassword, UserData.ChangePassword    |
| FR-35 | The system SHALL maintain module-level access rights by role when administrators update access control settings.                      | High     | frmModuleAccess::LoadModuleAccess, frmModuleAccess::UpdateModuleAccess, ModuleAccess table    |
| FR-36 | The system SHALL restrict access to each feature according to the user's assigned role when the user attempts to open or use that feature.                      | High     | modFunction::UserAccessModule, MOD_* constants    |

### 5.6 Reporting

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-37 | The system SHALL display a list of available reports when authorized users open the reporting module.                      | High     | frmReport::LoadReports    |
| FR-38 | The system SHALL allow authorized users to export reports when a report is selected for output.                      | High     | frmReport::PrintReport, MOD_REPORT_EXPORT    |
| FR-39 | The system SHALL allow authorized users to maintain report definitions when report maintenance access is granted.                     | Medium   | frmReportMaintain::LoadReports, frmReportMaintain::SaveReport    |
| FR-40 | The system SHALL provide daily, weekly, and monthly booking reports when users select standard booking reporting periods.                      | High     | REP_DAILY_BOOKING, REP_WEEKLY_BOOKING, REP_MONTHLY_BOOKING    |
| FR-41 | The system SHALL provide staff-based booking reports when users select reporting by staff activity.                      | Medium   | REP_SHIFT_FOR_USER, REP_SHIFT_ALL_USER, Booking Report by Staff    |
| FR-42 | The system SHALL allow report access, report export, report printing, and report editing as separate permission-controlled actions when role permissions differ.                      | High     | MOD_REPORT_LIST, MOD_REPORT_PRINT, MOD_REPORT_EXPORT, MOD_REPORT_EDIT, MOD_REPORT_EDIT_EXPERT    |

### 5.7 Audit & Logging

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-43 | The system SHALL record booking activity history when booking records are created or changed.                      | Medium   | LogBooking table, frmBooking::SaveBooking, frmBooking::VoidBooking    |
| FR-44 | The system SHALL record room activity history when room records or room status values are changed.                      | Medium   | LogRoom table, frmRoomMaintain::SaveRecord, frmBooking::UpdateRoomStatus    |
| FR-45 | The system SHALL record system errors with module, method, user, date, and error details when application errors occur.                      | High     | modFunction::LogErrorDB, modTextFile::Log2File, LogError table    |

---

## 6. Non-Functional Requirements

| ID     | Category       | Requirement                                                      | Metric / Target          | Priority |
|--------|----------------|------------------------------------------------------------------|--------------------------|----------|
| NFR-01 | Performance    | The system SHALL load the main dashboard within the target time. | < 2 seconds              | High     |
| NFR-02 | Performance    | The system SHALL complete all save/update operations promptly.   | < 1 second               | High     |
| NFR-03 | Security       | The system SHALL hash all passwords using a modern algorithm.    | bcrypt / Argon2 / SHA-256| High     |
| NFR-04 | Security       | The system SHALL enforce role-based access control on all routes.| 0 unauthorized accesses  | High     |
| NFR-05 | Security       | The system SHALL expire sessions after configurable inactivity.  | Configurable (minutes)   | High     |
| NFR-06 | Reliability    | The system SHALL handle database errors gracefully.              | Error message + log      | High     |
| NFR-07 | Reliability    | The system SHALL maintain target uptime during business hours.   | > 99%                    | High     |
| NFR-08 | Usability      | New users SHALL be able to complete core tasks after training.   | 30–60 min training       | Medium   |
| NFR-09 | Usability      | The system SHALL provide meaningful validation messages.         | Every error explained    | Medium   |
| NFR-10 | Compatibility  | The system SHALL run on all modern browsers / target platforms.  | Web application for modern desktop browsers        | High     |
| NFR-11 | Scalability    | The system SHALL support the expected number of concurrent users.| 50 users   | Medium   |
| NFR-12 | Auditability   | The system SHALL log all critical actions with user and timestamp.| Full audit trail         | Medium   |

---

## 7. Scope

### 7.1 In Scope — Version 1.0

- User authentication, password change, session timeout, and role-based access control.
- Booking management including room assignment, payment entry, status updates, voiding, and receipt export.
- Room, room type, user, customer search, reporting, and audit logging functions.

### 7.2 Out of Scope — Version 1.0

- Infrastructure setup and environment configuration screens.
- Technical migration, bootstrap, and sample data creation routines.
- Advanced notification channels such as email and SMS unless confirmed separately.

### 7.3 Future Scope — Version 2.0+

- Two-factor authentication for privileged users.
- External system integrations through APIs.
- Advanced analytics dashboards and automated notifications.

---

## 8. Assumptions & Constraints

### 8.1 Assumptions

- The business will define final role names that map to existing access levels.
- Current booking, room, user, and report data structures represent the minimum information required for operations.
- Users will access the system through managed desktop devices with stable network access.

### 8.2 Constraints

| Type        | Constraint                                               |
|-------------|----------------------------------------------------------|
| Technology  | The product must be delivered as a modern web application with centralized data storage.                                  |
| Budget      | Version 1.0 must prioritize confirmed business functions from the source analysis.                                      |
| Timeline    | Core operational features should be delivered before advanced enhancements.                                    |
| Team        | Business validation will be required to confirm inferred workflows and role naming.                                    |
| Platform    | Initial release is limited to modern desktop browser usage.                                    |
| Integration | No external integrations are confirmed in the source analysis for version 1.0.                                 |

---

## 9. Screen & Feature Descriptions

### 9.1 Login & Authentication

**Purpose:** Allows users to securely access the system and complete required password updates.

**Key Elements:**
- User ID field
- Password field
- Password change prompt

**Actions Available:**
- Sign in
- Change password
- End inactive session

**Access:** All users

---

### 9.2 Operational Dashboard

**Purpose:** Provides a live overview of room availability, room status, and booking summaries.

**Key Elements:**
- Room status grid
- Summary counters
- Alert indicators

**Actions Available:**
- View room status
- Change room status
- Open booking actions

**Access:** Front Desk Staff, Hotel Supervisor, System Administrator

---

### 9.3 Booking Management

**Purpose:** Allows staff to create, update, check in, check out, void, and document guest bookings.

**Key Elements:**
- Guest details
- Stay dates and times
- Payment and totals section

**Actions Available:**
- Create booking
- Assign room
- Export receipt

**Access:** Front Desk Staff, Hotel Supervisor, System Administrator

---

### 9.4 Customer Search

**Purpose:** Allows staff to find customers and review prior booking activity.

**Key Elements:**
- Search filters
- Customer results list
- Booking history view

**Actions Available:**
- Search customers
- View booking history
- Export customer history

**Access:** Front Desk Staff, Hotel Supervisor, System Administrator

---

### 9.5 Room Management

**Purpose:** Allows authorized users to maintain rooms and room types used in booking operations.

**Key Elements:**
- Room list
- Room detail form
- Room type list

**Actions Available:**
- Add or update room
- Add or update room type
- Review room availability data

**Access:** Hotel Supervisor, System Administrator

---

### 9.6 User Management

**Purpose:** Allows administrators to maintain user accounts, roles, and password requirements.

**Key Elements:**
- User list
- User detail form
- Role selector

**Actions Available:**
- Create user
- Update user
- Activate or deactivate user

**Access:** System Administrator

---

### 9.7 Access Control

**Purpose:** Allows administrators to assign feature access by role.

**Key Elements:**
- Module list
- Role permission matrix
- Save permissions action

**Actions Available:**
- View permissions
- Update permissions
- Review access settings

**Access:** System Administrator

---

### 9.8 Reports & Exports

**Purpose:** Allows authorized users to run operational reports and export business documents.

**Key Elements:**
- Report list
- Report details
- Export action

**Actions Available:**
- View report options
- Export reports
- Maintain report definitions

**Access:** Hotel Supervisor, System Administrator

---

## 10. Acceptance Criteria

| Feature               | Given                          | When                          | Then                                        | Source (UAST)      |
|-----------------------|--------------------------------|-------------------------------|---------------------------------------------|--------------------|
| User Login           | A valid active user exists            | The user submits correct credentials               | The system grants access according to the user's role                             | frmUserLogin::cmdOK_Click, modFunction::UserAccessModule      |
| Forced Password Change           | A user account is marked for password change            | The user logs in               | The system requires the user to update the password before continuing                             | modFunction::NeedChangePassword, UserData.ChangePassword      |
| Booking Save           | A user has entered guest, room, and stay details            | The user saves the booking               | The system stores the booking and assigns booking status and audit details                             | frmBooking::SaveBooking, Booking table      |
| Payment Recording           | A booking is open for editing            | The user enters deposit, payment, or refund values               | The system stores the entered amounts against the booking                             | frmBooking txtDeposit/txtPayment/txtRefund LostFocus      |
| Room Status Update           | A booking is checked in, checked out, voided, or saved            | The booking action is completed               | The system updates the related room status                             | frmBooking::UpdateRoomStatus      |
| Room Type Usage Protection           | A room type is linked to existing room usage            | The user attempts to remove or change that room type               | The system prevents the action                             | frmRoomTypeMaintain::IsRoomTypeUsed      |
| Customer Search           | Customer records exist            | The user searches by guest name, contact, origin, or passport               | The system returns matching customer records                             | frmFindCustomer::ListCustomers      |
| Customer History           | A customer is selected from the search results            | The user opens booking history               | The system displays that customer's booking history                             | frmFindCustomer::ListBooking      |
| Permission Control           | A user lacks access to a feature            | The user attempts to open that feature               | The system denies access                             | modFunction::UserAccessModule      |
| Report Export           | A user has permission to export reports            | The user selects a report export action               | The system exports the selected report                             | frmReport::PrintReport, MOD_REPORT_EXPORT      |

---

## 11. Data Model Summary

### 11.1 Core Entities

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| Booking      | Stores guest reservations, stay details, financials, and booking status.                   | ID, GuestName, RoomID, StayDuration, SubTotal, Deposit, Payment, Refund, xStatus                         |
| Room      | Stores room master data, availability, and operational status.                   | ID, RoomShortName, RoomLongName, RoomType, RoomPrice, RoomStatus, BookingID                         |
| User Account      | Stores user credentials, role assignment, and security settings.                   | ID, UserID, UserName, UserGroup, UserPassword, Salt, LoginAttempts, ChangePassword                         |
| Room Type      | Defines room categories used for room assignment.                   | ID, TypeShortName, TypeLongName, Active                         |
| Report Definition      | Stores available reports and report selection information.                   | ReportID, ReportName1, ReportTitle1, ReportQuery, DateField1, Active                         |
| Role      | Defines user role names and security levels.                   | GroupID, GroupName, GroupDesc, SecurityLevel                         |

### 11.2 Audit & System Tables

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| LogBooking | Stores historical copies of booking changes for audit review.                    | BookingID, GuestName, RoomID, Payment, Refund, LastModifiedBy, LastModifiedDate                         |
| LogRoom | Stores historical copies of room and room status changes.                    | RoomID, BookingID, RoomStatus, Maintenance, LastModifiedBy, LastModifiedDate                         |
| LogError | Stores system error information for troubleshooting and audit.                    | ID, LogDateTime, LogErrorDescription, LogMethod, LogModule, LogUserName                         |

---

## 12. Business Rules

| ID    | Domain          | Business Rule                                                              | Source (UAST)     |
|-------|-----------------|----------------------------------------------------------------------------|-------------------|
| BR-01 | Authentication        | A user account requiring password change must update its password at login before normal access continues.                                                           | modFunction::NeedChangePassword, UserData.ChangePassword     |
| BR-02 | Authentication        | Each user account must maintain a configurable inactivity limit for session timeout control.                                                           | UserData.Idle, frmDialog::tmrClock     |
| BR-03 | Booking Management        | A booking must be assigned a temporary identifier before final save during booking creation.                                                           | frmBooking::CreateTempBookingID, frmBooking::GetTempBookingID     |
| BR-04 | Room Management        | A room type that is already in use must not be removed or changed in a way that invalidates existing usage.                                                           | frmRoomTypeMaintain::IsRoomTypeUsed     |
| BR-05 | Access Control        | Access to business modules must be determined by role-based module permissions.                                                           | modFunction::UserAccessModule, frmModuleAccess::UpdateModuleAccess     |
| BR-06 | Booking Management        | Room status must change when booking lifecycle actions such as check-in, check-out, save, or void occur.                                                           | frmBooking::UpdateRoomStatus     |
| BR-07 | Reporting        | Report listing, printing, exporting, and editing are separate controlled permissions.                                                           | MOD_REPORT_LIST, MOD_REPORT_PRINT, MOD_REPORT_EXPORT, MOD_REPORT_EDIT, MOD_REPORT_EDIT_EXPERT     |
| BR-08 | Audit & Logging        | Errors must be recorded with user, module, method, and time details when exceptions occur.                                                           | modFunction::LogErrorDB, LogError table     |

---

## 13. Access Control Matrix

| Module / Feature     | System Administrator    | Hotel Supervisor    | Front Desk Staff    | Auditor / Viewer    |
|----------------------|-------------|-------------|-------------|-------------|
| Dashboard           | Full        | Full        | View Only   | No Access   |
| Booking Management           | Full        | Full        | Full   | No Access   |
| Customer Search           | Full        | Full        | Full   | View Only   |
| Room Management           | Full        | Full        | No Access   | No Access   |
| Room Type Management           | Full        | Full        | No Access   | No Access   |
| User Management           | Full        | No Access   | No Access   | No Access   |
| Access Control           | Full        | No Access   | No Access   | No Access   |
| Report List           | Full        | Full        | View Only   | View Only   |
| Report Export           | Full        | Full        | No Access   | No Access   |
| Report Editing           | Full        | No Access   | No Access   | No Access   |

---

## 14. Timeline & Milestones

| Milestone | Name                      | Key Deliverables                         | Target     |
|-----------|---------------------------|------------------------------------------|------------|
| M1        | Core Access & Security        | Login, password change, session timeout, role setup                        | Week 2  |
| M2        | Booking Operations        | Booking creation, room assignment, totals, receipt export                        | Week 5  |
| M3        | Room & Availability Management        | Room maintenance, room type maintenance, dashboard room statuses                        | Week 7  |
| M4        | Customer Search & History        | Customer search filters, booking history, history export                        | Week 8  |
| M5        | Reporting & Administration        | Report list, export, report maintenance, module access, user management                        | Week 10  |
| M6        | Testing & QA              | Full acceptance testing, bug fixes       | Week 11  |
| M7        | User Training & UAT       | Staff training, user acceptance testing  | Week 12  |
| M8        | Go Live                   | Production deployment                    | Week 13  |

---

## 15. Risks & Mitigations

| ID   | Risk                                    | Likelihood | Impact | Mitigation                                              |
|------|-----------------------------------------|------------|--------|---------------------------------------------------------|
| R-01 | Role names and permission meanings may be unclear from source analysis alone.                                | Medium        | High    | Validate all role mappings and permission rules with business stakeholders before build.                                          |
| R-02 | Booking status workflow may contain hidden exceptions not visible in the extracted summary.                                | Medium        | High    | Run stakeholder workshops to confirm all booking lifecycle states and transitions.                                          |
| R-03 | Report definitions may rely on business logic not fully represented in the available source summary.                                | Medium        | Medium    | Review all report outputs with operational users and confirm required fields and filters.                                          |
| R-04 | Historical data quality may affect migration and audit accuracy.                                | High        | Medium    | Perform data profiling and cleansing before migration and user acceptance testing.                                          |
| R-05 | Staff may resist changes to booking and room handling processes.                                | Medium        | Medium    | Provide role-based training, process guides, and phased user acceptance sessions.                                          |

---

## 16. Gaps & Inferred Requirements

| ID   | Tag                  | Gap / Inferred Requirement                                                        | Action Required                        |
|------|----------------------|-----------------------------------------------------------------------------------|----------------------------------------|
| G-01 | [NEEDS CONFIRMATION]                | The exact booking status values and the allowed transitions between them are not fully explicit in the extracted summary.                                                                 | Confirm complete booking lifecycle with stakeholders.                               |
| G-02 | [INFERRED]                | Dashboard alerts appear to highlight bookings that require attention, but the trigger conditions are not fully defined.                                                                 | Confirm alert rules, thresholds, and user expectations.                               |
| G-03 | [NEEDS CONFIRMATION]                | The meaning of each existing user group and how it maps to business roles is not explicitly named in the source summary.                                                                 | Confirm final role model and access matrix with business and security owners.                               |
| G-04 | [BEST PRACTICE]      | System should support two-factor authentication (2FA) for admin accounts.         | Confirm with stakeholders for v1.0     |
| G-05 | [BEST PRACTICE]      | System should send email/SMS notifications for key business events.               | Confirm scope with product owner       |
| G-06 | [BEST PRACTICE]      | System should provide an API layer for future integrations.                       | Confirm architecture decision          |

---

## 17. Sign-Off

This document requires review and approval from the following stakeholders
before development begins.

| Role                  | Name | Signature | Date |
|-----------------------|------|-----------|------|
| Product Owner         |      |           |      |
| Lead Developer        |      |           |      |
| Business Stakeholder  |      |           |      |
| QA Lead               |      |           |      |
| IT / DevOps Lead      |      |           |      |

---

**Document Status:** Draft
**Last Updated:** 2026-04-20
**Version:** 1.0

> This PRD was generated from a legacy application UAST.
> All requirements are traceable to the source system.
> Items marked [INFERRED] or [NEEDS CONFIRMATION] must be
> validated with business stakeholders before development.