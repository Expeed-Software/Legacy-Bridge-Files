# Product Requirements Document (PRD)
**Product Name:** Hotel Booking System  
**Version:** 1.0  
**Status:** Draft  
**Date:** 2026-04-21  
**Prepared By:** Senior Business Analyst  
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

The Hotel Booking System is a business application used to manage room availability, guest bookings, customer search, user administration, access permissions, and operational reporting for a hotel. It supports front desk staff, supervisors, and administrators in handling the full room booking lifecycle, monitoring room status, maintaining reference data, and producing business documents such as receipts and booking reports.

The modernized product will improve operational control by centralizing room occupancy management, enforcing access rules, validating booking and payment data, and preserving an audit trail of business actions. It will also provide structured reporting and export capabilities for bookings, customer history, and staff activity.

| Attribute        | Detail                        |
|------------------|-------------------------------|
| Product Name     | Hotel Booking System                |
| Business Domain  | Hospitality Operations and Booking Management             |
| Target Platform  | Web application for desktop browser use             |
| Primary Users    | Front Desk Staff, Hotel Manager, System Administrator             |
| Business Problem | Manual or fragmented handling of bookings, room status, access control, and reporting             |
| Expected Benefit | Faster booking operations, better room visibility, stronger control, and improved reporting             |
| Source System    | Legacy Hotel Booking System               |

---

## 2. Goals & Objectives

### 2.1 Business Goals
- Improve booking accuracy and room utilization by centralizing room and guest transaction management.
- Reduce operational risk by enforcing permissions, password controls, and audit logging.
- Improve business visibility by providing searchable records and exportable reports.

### 2.2 Product Goals
- Provide a complete booking workflow covering reservation, check-in, check-out, voiding, and receipt generation.
- Provide configurable role-based access control for operational and administrative modules.
- Provide searchable customer, room, user, and report management views with clear validation and status handling.

### 2.3 Success Metrics

| Metric              | Target         | How to Measure        |
|---------------------|----------------|-----------------------|
| Booking completion time          | < 3 minutes     | Timed user testing for standard booking flow           |
| Unauthorized access rate          | 0 successful incidents     | Access audit review and permission test results           |
| Report export success rate          | > 99%     | Successful export count versus attempts           |

---

## 3. User Personas

### 3.1 Front Desk Staff

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Front Desk Staff                |
| Description  | Operational user responsible for room assignment, guest booking, check-in, check-out, payment entry, and customer lookup.         |
| Goals        | Create accurate bookings, assign available rooms, complete guest transactions, and issue receipts.               |
| Pain Points  | Delays in finding customer history, unclear room status, and booking errors caused by missing data.          |
| Tech Level   | Basic to intermediate          |
| Usage        | Daily throughout each shift     |
| Modules Used | Dashboard, Booking Management, Customer Search, Receipt Export, Reports             |

### 3.2 Hotel Manager

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Hotel Manager                |
| Description  | Supervisory user responsible for monitoring occupancy, managing room status, reviewing reports, and overseeing staff activity.         |
| Goals        | Monitor room utilization, track operational alerts, review booking trends, and control exception handling.               |
| Pain Points  | Limited visibility into booking activity, delayed alerts, and inconsistent room status updates.          |
| Tech Level   | Intermediate          |
| Usage        | Several times daily     |
| Modules Used | Dashboard, Room Management, Reports, Customer Search, Booking Management             |

### 3.3 System Administrator

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | System Administrator                |
| Description  | Administrative user responsible for user accounts, password resets, role assignments, module permissions, and report setup.         |
| Goals        | Control access, maintain users, manage report definitions, and preserve secure system operation.               |
| Pain Points  | Difficulty managing permissions consistently and ensuring users follow password change rules.          |
| Tech Level   | Intermediate to advanced          |
| Usage        | As needed, typically daily or weekly     |
| Modules Used | User Management, Access Control, Change Password, Report Maintenance, Room Type Management, Room Management             |

---

## 4. User Stories

### 4.1 Authentication & Session Management

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-01  | As a user, I want to sign in with my user ID so that I can access the system securely.       | High     | frmUserLogin::cmdOK_Click       |
| US-02  | As a user, I want the system to require a password change when needed so that my account remains secure.       | High     | modFunction::NeedChangePassword       |
| US-03  | As a user, I want to change my password so that I can maintain control of my account.       | High     | frmUserChangePassword::UpdatePassword       |
| US-04  | As an administrator, I want failed login attempts to be tracked so that repeated access failures can be controlled.       | Medium   | UserData.LoginAttempts; frmUserLogin::cmdOK_Click       |
| US-05  | As a business owner, I want inactive sessions to expire so that unattended accounts do not remain open.       | Medium   | UserData.Idle; frmDialog; timer-based timeout pattern       |

### 4.2 Booking Management

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-06  | As a front desk staff member, I want to create a booking so that I can reserve a room for a guest.       | High     | frmBooking::SaveBooking       |
| US-07  | As a front desk staff member, I want to select a room during booking so that I can assign the correct accommodation.       | High     | frmBooking::SelectRoom       |
| US-08  | As a front desk staff member, I want the system to calculate booking totals so that payment amounts are accurate.       | High     | frmBooking::SumTotal       |
| US-09  | As a front desk staff member, I want to check guests in and out so that room occupancy is updated correctly.       | High     | frmBooking UIEvent Check IN/OUT; frmBooking::UpdateRoomStatus       |
| US-10  | As a front desk staff member, I want to void a booking when required so that cancelled or invalid bookings are marked correctly.       | Medium   | frmBooking::VoidBooking       |

### 4.3 Room & Occupancy Management

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-11  | As a manager, I want to view room status on a dashboard so that I can monitor occupancy in real time.       | High     | frmDashboard::GetRoomStatus       |
| US-12  | As a manager, I want to update room status to free, occupied, housekeeping, or maintenance so that operational teams know room availability.       | High     | frmDashboard mnuFree/mnuOccupied/mnuHousekeeping/mnuMaintenance       |
| US-13  | As a manager, I want a room summary by status so that I can monitor overall hotel operations.       | High     | frmDashboard::ShowSummary1; ShowSummary2; ShowSummary3; ShowSummary4; ShowSummary5       |
| US-14  | As an administrator, I want to maintain room records so that room inventory stays accurate.       | Medium   | frmRoomMaintain::SaveRecord       |
| US-15  | As an administrator, I want to maintain room types so that rooms can be categorized consistently.       | Medium   | frmRoomTypeMaintain::SaveRoomType       |

### 4.4 Customer Search & History

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-16  | As a front desk staff member, I want to search customers by key details so that I can find existing guest records quickly.       | High     | frmFindCustomer::ListCustomers       |
| US-17  | As a front desk staff member, I want to view a selected customer's booking history so that I can understand past stays and transactions.       | High     | frmFindCustomer::ListBooking       |
| US-18  | As a front desk staff member, I want to search by name, passport, contact number, or origin so that I can find records using partial information.       | Medium   | frmFindCustomer UIEvents on txtGuestName/txtGuestPassport/txtGuestContact/txtGuestOrigin       |
| US-19  | As a manager, I want to print or export customer history so that I can share or review transaction details.       | Medium   | frmFindCustomer::PrintHistory       |
| US-20  | As a user, I want to clear search filters so that I can start a new search quickly.       | Low      | frmFindCustomer::ResetFields       |

### 4.5 User Management, Access Control & Reporting

| ID     | User Story                                              | Priority | Source (UAST)       |
|--------|---------------------------------------------------------|----------|---------------------|
| US-21  | As an administrator, I want to create and update user accounts so that staff can access the system appropriately.       | High     | frmUserMaintain::SaveUser       |
| US-22  | As an administrator, I want to assign users to roles so that permissions are controlled by job function.       | High     | frmUserMaintain::PopulateGroupName; UserData.UserGroup       |
| US-23  | As an administrator, I want to manage module permissions by role so that users only access authorized features.       | High     | frmModuleAccess::UpdateModuleAccess; modFunction::UserAccessModule       |
| US-24  | As a manager, I want to view available reports so that I can review operational and financial activity.       | High     | frmReport::LoadReports       |
| US-25  | As an administrator, I want to maintain report definitions so that available reports remain accurate and usable.       | Medium   | frmReportMaintain::SaveReport       |

---

## 5. Functional Requirements

### 5.1 Authentication & Session Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-01 | The system SHALL authenticate a user against stored account credentials when the user submits sign-in details.                      | High     | frmUserLogin::cmdOK_Click    |
| FR-02 | The system SHALL force a user to change password when the account is marked as requiring a password change at login.                      | High     | modFunction::NeedChangePassword    |
| FR-03 | The system SHALL allow a user to update password after current password validation succeeds.                     | High   | frmUserChangePassword::CheckPassword; frmUserChangePassword::UpdatePassword    |
| FR-04 | The system SHALL track failed login attempts for each user account after an unsuccessful sign-in.                        | High      | UserData.LoginAttempts; frmUserLogin::cmdOK_Click    |
| FR-05 | The system SHALL store all passwords using an industry-standard hashing algorithm with a unique salt per user when passwords are created or updated.                      | High     | modEncryption::GenSalt; modEncryption::Encrypt    |
| FR-06 | The system SHALL end an authenticated session after a configurable period of user inactivity.                      | High     | UserData.Idle; frmDialog; timer-based timeout pattern    |

### 5.2 Booking Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-07 | The system SHALL create a temporary booking identifier before a booking is finalized.                      | High     | frmBooking::CreateTempBookingID    |
| FR-08 | The system SHALL allow a user to select a room during booking entry when creating or updating a booking.                      | High     | frmBooking::SelectRoom    |
| FR-09 | The system SHALL populate room details into the booking record after a room is selected.                     | High     | frmBooking::GetRoomDetails    |
| FR-10 | The system SHALL calculate booking subtotal using the configured booking charge values when booking values change.                        | High      | frmBooking::SumTotal    |
| FR-11 | The system SHALL capture guest details for each booking before the booking is saved.                      | High     | Booking.GuestName; GuestContact; GuestPassport; GuestOrigin; frmBooking::SaveBooking    |
| FR-12 | The system SHALL store stay duration, total guest count, room details, payment values, and remarks for each booking when the booking is saved.                      | High     | Booking.StayDuration; TotalGuest; RoomNo; Payment; Deposit; Refund; Remarks; frmBooking::SaveBooking    |
| FR-13 | The system SHALL allow a user to save a booking when required booking data has been entered.                      | High     | frmBooking::SaveBooking    |
| FR-14 | The system SHALL update the room status after check-in or check-out is completed.                        | High      | frmBooking::UpdateRoomStatus    |
| FR-15 | The system SHALL prevent check-in if booking payment is incomplete.                      | High     | frmBooking::IsPaid; frmBooking UIEvent Check IN    |
| FR-16 | The system SHALL allow a user to void a booking when the booking requires cancellation.                      | Medium     | frmBooking::VoidBooking    |
| FR-17 | The system SHALL generate a receipt for a booking after a booking transaction is completed.                      | Medium     | frmBooking::PrintReceipt    |

### 5.3 Room & Occupancy Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-18 | The system SHALL display room status for each room on the dashboard when the dashboard is loaded.                      | High     | frmDashboard::GetRoomStatus; frmDashboard::RoomSetup    |
| FR-19 | The system SHALL display booking summaries by room status on the dashboard when operational data is refreshed.                      | High     | frmDashboard::ShowSummary1; ShowSummary2; ShowSummary3; ShowSummary4; ShowSummary5    |
| FR-20 | The system SHALL allow authorized users to set a room status to Free when updating operational room state.                     | High   | frmDashboard::mnuFree Click    |
| FR-21 | The system SHALL allow authorized users to set a room status to Occupied when updating operational room state.                        | High      | frmDashboard::mnuOccupied Click    |
| FR-22 | The system SHALL allow authorized users to set a room status to Housekeeping when updating operational room state.                      | High     | frmDashboard::mnuHousekeeping Click    |
| FR-23 | The system SHALL allow authorized users to set a room status to Maintenance when updating operational room state.                      | High     | frmDashboard::mnuMaintenance Click    |
| FR-24 | The system SHALL alert users to bookings that require attention when alert conditions are met.                      | Medium     | frmDashboard::AlertBooking; frmDashboard::NeedBlink    |
| FR-25 | The system SHALL allow authorized users to create and update room records when maintaining room inventory.                      | Medium     | frmRoomMaintain::SaveRecord    |
| FR-26 | The system SHALL list available rooms for maintenance review when the room management screen is opened.                      | Medium     | frmRoomMaintain::ListRooms    |
| FR-27 | The system SHALL allow authorized users to create and update room type records when maintaining room classifications.                      | Medium     | frmRoomTypeMaintain::SaveRoomType    |
| FR-28 | The system SHALL prevent deletion or deactivation of a room type if the room type is already in use.                      | High     | frmRoomTypeMaintain::IsRoomTypeUsed    |

### 5.4 Customer Search & History

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-29 | The system SHALL allow users to search customer records using guest name, passport number, contact number, or origin.                      | High     | frmFindCustomer::ListCustomers; frmFindCustomer UIEvents    |
| FR-30 | The system SHALL display booking history for a selected customer when a customer record is chosen from search results.                      | High     | frmFindCustomer::ListBooking; lvCustomers_Click    |
| FR-31 | The system SHALL allow users to clear customer search fields when starting a new search.                     | Low   | frmFindCustomer::ResetFields    |
| FR-32 | The system SHALL allow users to export or print customer transaction history for a selected customer.                        | Medium      | frmFindCustomer::PrintHistory    |

### 5.5 User Management

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-33 | The system SHALL allow administrators to create and update user accounts when maintaining system users.                      | High     | frmUserMaintain::SaveUser    |
| FR-34 | The system SHALL store user account status as active or inactive for each user account.                      | High     | UserData.Active; frmUserMaintain::SaveUser    |
| FR-35 | The system SHALL assign each user account to a role when the account is created or updated.                      | High     | frmUserMaintain::PopulateGroupName; UserData.UserGroup    |
| FR-36 | The system SHALL allow administrators to set whether a user must update password on next login when maintaining user accounts.                      | Medium     | frmUserMaintain::chkUpdatePassword Click; UserData.ChangePassword    |
| FR-37 | The system SHALL allow administrators to define whether dashboard booking alerts are enabled for a user account.                      | Medium     | UserData.DashboardBlink; frmDashboard::LoadBlinkSetting; frmDashboard::UpdateBlinkSetting    |
| FR-38 | The system SHALL list user accounts for review and selection when the user management screen is opened.                      | Medium     | frmUserMaintain::ListUsers    |

### 5.6 Access Control

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-39 | The system SHALL enforce role-based access control before a user accesses a protected module.                      | High     | modFunction::UserAccessModule; modGlobal module constants    |
| FR-40 | The system SHALL allow administrators to view module access settings by role when managing permissions.                      | High     | frmModuleAccess::LoadModuleAccess    |
| FR-41 | The system SHALL allow administrators to update module access settings by role when saving permission changes.                      | High     | frmModuleAccess::UpdateModuleAccess    |
| FR-42 | The system SHALL support separate access rules for dashboard, booking, customer search, reporting, room maintenance, user management, and access control modules.                      | High     | MOD_DASHBOARD; MOD_BOOKING; MOD_FIND_CUSTOMER; MOD_REPORT_LIST; MOD_REPORT_PRINT; MOD_REPORT_EXPORT; MOD_MAINTAIN_ROOM; MOD_MAINTAIN_USER; MOD_ACCESS_CONTROL    |

### 5.7 Reporting

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-43 | The system SHALL display a list of available reports to authorized users when the reporting screen is opened.                      | High     | frmReport::LoadReports    |
| FR-44 | The system SHALL allow authorized users to open a selected report for review before export or print actions.                      | Medium     | frmReport::EditReport; lvReports_DblClick    |
| FR-45 | The system SHALL allow authorized users to export business reports in a user-consumable document format.                      | High     | frmReport::PrintReport; frmPrint    |
| FR-46 | The system SHALL allow administrators to create and update report definitions when maintaining report setup.                      | Medium     | frmReportMaintain::SaveReport    |
| FR-47 | The system SHALL support operational reports for daily bookings, weekly bookings, monthly bookings, customer transaction history, staff booking activity, and receipts.                      | High     | ReportDeclaration list; modGlobal report constants    |

### 5.8 Audit & Logging

| ID    | Requirement                                                          | Priority | Source (UAST)    |
|-------|----------------------------------------------------------------------|----------|------------------|
| FR-48 | The system SHALL record booking changes with user and timestamp information when a booking is created or updated.                      | High     | Booking.CreatedBy; CreatedDate; LastModifiedBy; LastModifiedDate; LogBooking table    |
| FR-49 | The system SHALL record room changes with user and timestamp information when a room record or room status is updated.                      | Medium     | Room.CreatedBy; CreatedDate; LastModifiedBy; LastModifiedDate; LogRoom table    |
| FR-50 | The system SHALL record application errors with module, method, user, and timestamp information when an error occurs.                      | Medium     | modFunction::LogErrorDB; modTextFile::Log2File; LogError table    |

---

## 6. Non-Functional Requirements

| ID     | Category       | Requirement                                                      | Metric / Target          | Priority |
|--------|----------------|------------------------------------------------------------------|--------------------------|----------|
| NFR-01 | Performance    | The system SHALL load the main dashboard within the target time. | < 2 seconds              | High     |
| NFR-02 | Performance    | The system SHALL complete all save/update operations promptly.   | < 1 second for standard transactions               | High     |
| NFR-03 | Security       | The system SHALL hash all passwords using a modern algorithm.    | bcrypt or Argon2 with unique salt per user | High     |
| NFR-04 | Security       | The system SHALL enforce role-based access control on all routes.| 0 unauthorized accesses  | High     |
| NFR-05 | Security       | The system SHALL expire sessions after configurable inactivity.  | Configurable in minutes per user policy   | High     |
| NFR-06 | Reliability    | The system SHALL handle data access errors gracefully.              | Error message + log      | High     |
| NFR-07 | Reliability    | The system SHALL maintain target uptime during business hours.   | > 99%                    | High     |
| NFR-08 | Usability      | New users SHALL be able to complete core tasks after training.   | 30–60 min training       | Medium   |
| NFR-09 | Usability      | The system SHALL provide meaningful validation messages.         | Every validation error explained    | Medium   |
| NFR-10 | Compatibility  | The system SHALL run on all modern browsers / target platforms.  | Latest Chrome, Edge, Firefox on desktop        | High     |
| NFR-11 | Scalability    | The system SHALL support the expected number of concurrent users.| 50 users   | Medium   |
| NFR-12 | Auditability   | The system SHALL log all critical actions with user and timestamp.| Full audit trail         | Medium   |

---

## 7. Scope

### 7.1 In Scope — Version 1.0

- User authentication, password change, session timeout, and role-based access control.
- Booking lifecycle management including room assignment, payment validation, check-in, check-out, voiding, and receipt generation.
- Dashboard, room and room type maintenance, customer search and history, user management, module access management, reporting, and audit logging.

### 7.2 Out of Scope — Version 1.0

- Environment setup, infrastructure configuration, and database migration utilities.
- Legacy print viewer behavior and non-business startup screens.
- External messaging, payment gateway integration, and third-party channel integrations.

### 7.3 Future Scope — Version 2.0+

- Two-factor authentication for privileged users.
- Automated notifications for booking alerts and status changes.
- API-based integration with finance, channel, or property systems.

---

## 8. Assumptions & Constraints

### 8.1 Assumptions

- Hotel staff roles and module permissions will be confirmed before build completion.
- Existing business reports remain relevant and should be available in the modern product.
- Audit history for bookings, rooms, and errors is required for operational review.

### 8.2 Constraints

| Type        | Constraint                                               |
|-------------|----------------------------------------------------------|
| Technology  | Solution must be delivered as a modern browser-based business application.                                  |
| Budget      | Version 1.0 must prioritize confirmed operational functions over advanced enhancements.                                      |
| Timeline    | Core booking and access control functions must be delivered first to support business continuity.                                    |
| Team        | Business, QA, and delivery teams must validate inferred rules before final implementation.                                        |
| Platform    | Primary use case is desktop access for front office and administrative staff.                                    |
| Integration | No confirmed external system integrations are evidenced in the source and should not be assumed for v1.0.                                 |

---

## 9. Screen & Feature Descriptions

### 9.1 Login & Password Management

**Purpose:** Allow users to sign in securely and update passwords when required.

**Key Elements:**
- User ID input
- Password input
- Password change form

**Actions Available:**
- Sign in
- Validate current password
- Update password

**Access:** All users

---

### 9.2 Booking Management

**Purpose:** Create, update, complete, and void guest bookings.

**Key Elements:**
- Guest details section
- Room selection section
- Payment and totals section

**Actions Available:**
- Create booking
- Check guest in or out
- Generate receipt

**Access:** Front Desk Staff, Hotel Manager, System Administrator

---

### 9.3 Operations Dashboard

**Purpose:** Show room occupancy, room status, booking alerts, and summary counts.

**Key Elements:**
- Room status grid
- Summary counters
- Booking alerts

**Actions Available:**
- View room status
- Update operational room status
- Open booking details

**Access:** Front Desk Staff, Hotel Manager, System Administrator

---

### 9.4 Customer Search & History

**Purpose:** Find customer records and review prior booking transactions.

**Key Elements:**
- Search fields for customer details
- Customer result list
- Booking history list

**Actions Available:**
- Search customers
- View booking history
- Export customer history

**Access:** Front Desk Staff, Hotel Manager, System Administrator

---

### 9.5 Room Management

**Purpose:** Maintain room master data and room classifications.

**Key Elements:**
- Room list
- Room detail form
- Room type selection

**Actions Available:**
- Add or update room
- Add or update room type
- Review room inventory

**Access:** Hotel Manager, System Administrator

---

### 9.6 User & Access Management

**Purpose:** Maintain user accounts, assign roles, and control feature permissions.

**Key Elements:**
- User list
- User account form
- Module permission matrix

**Actions Available:**
- Create or update user
- Assign role
- Update role permissions

**Access:** System Administrator

---

### 9.7 Reports & Document Export

**Purpose:** Provide operational and transaction reporting for hotel activities.

**Key Elements:**
- Report list
- Report setup form
- Export action controls

**Actions Available:**
- View report catalog
- Maintain report definitions
- Export reports and receipts

**Access:** Hotel Manager, System Administrator, authorized Front Desk Staff

---

## 10. Acceptance Criteria

| Feature               | Given                          | When                          | Then                                        | Source (UAST)      |
|-----------------------|--------------------------------|-------------------------------|---------------------------------------------|--------------------|
| User Sign-In           | A user has a valid active account            | The user submits valid sign-in credentials               | The system grants access to authorized modules                             | frmUserLogin::cmdOK_Click      |
| Forced Password Change           | A user account is marked for password change            | The user signs in successfully               | The system requires the user to update password before continuing                             | modFunction::NeedChangePassword      |
| Booking Payment Validation           | A booking has incomplete payment            | A user attempts check-in               | The system prevents check-in                             | frmBooking::IsPaid; Check IN      |
| Temporary Booking Creation           | A user starts a new booking            | The booking is being prepared before save               | The system creates a temporary booking identifier                             | frmBooking::CreateTempBookingID      |
| Room Status Update           | A guest check-in or check-out is completed            | The transaction is saved               | The system updates the related room status                             | frmBooking::UpdateRoomStatus      |
| Room Type Protection           | A room type is assigned to one or more rooms            | An administrator attempts to remove or deactivate that room type               | The system blocks the action                             | frmRoomTypeMaintain::IsRoomTypeUsed      |
| Customer Search           | Customer records exist            | A user searches by name, passport, contact number, or origin               | The system returns matching customer records                             | frmFindCustomer::ListCustomers      |
| Customer History           | A customer is selected from search results            | The user requests booking history               | The system displays that customer's booking records                             | frmFindCustomer::ListBooking      |
| Module Access Control           | A user lacks permission for a module            | The user attempts to open that module               | The system denies access                             | modFunction::UserAccessModule      |
| Report Availability           | Report definitions exist and the user has permission            | The user opens the reporting screen               | The system displays the available report list                             | frmReport::LoadReports      |

---

## 11. Data Model Summary

### 11.1 Core Entities

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| Booking      | Stores guest booking, stay, payment, and room assignment details.                   | ID, GuestName, GuestContact, GuestPassport, GuestOrigin, RoomID, RoomNo, RoomType, StayDuration, SubTotal, Deposit, Payment, Refund, xStatus                         |
| Room      | Stores room inventory and current operational room state.                   | ID, RoomShortName, RoomLongName, RoomType, RoomLocation, RoomPrice, RoomStatus, BookingID                         |
| User Account      | Stores system user identity, role, password policy, and account settings.                   | ID, UserID, UserName, UserGroup, UserPassword, Salt, LoginAttempts, ChangePassword, Idle                         |
| Report Definition      | Stores metadata for available business reports.                   | ReportID, ReportName1, ReportTitle1, ReportQuery, DateField1, DateType1                         |
| Room Type      | Stores room classification values used by room records.                   | ID, TypeShortName, TypeLongName, Active                         |
| Module Access      | Stores role-based permissions for each module.                   | ModuleID, ModuleDesc1, ModuleType, Group1, Group2, Group3, Group4                         |

### 11.2 Audit & System Tables

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| Booking Audit Log | Stores historical booking transaction details for traceability.                    | BookingID, CreatedBy, CreatedDate, LastModifiedBy, LastModifiedDate, xStatus, Payment, Deposit, Refund                         |
| Room Audit Log | Stores historical room and room status changes for traceability.                    | RoomID, BookingID, RoomStatus, CreatedBy, CreatedDate, LastModifiedBy, LastModifiedDate                         |
| Error Log | Stores application error details for investigation and support.                    | LogDateTime, LogErrorDescription, LogErrorNum, LogMethod, LogModule, LogUserName                         |

---

## 12. Business Rules

| ID    | Domain          | Business Rule                                                              | Source (UAST)     |
|-------|-----------------|----------------------------------------------------------------------------|-------------------|
| BR-01 | Authentication        | A user marked for password change must update password at next successful login.                                                           | modFunction::NeedChangePassword     |
| BR-02 | Booking Management        | A guest cannot be checked in until payment is complete.                                                           | frmBooking::IsPaid; Check IN     |
| BR-03 | Booking Management        | A temporary booking identifier must exist before a booking is finalized.                                                           | frmBooking::CreateTempBookingID     |
| BR-04 | Room Management        | A room type that is already in use cannot be deleted or deactivated.                                                           | frmRoomTypeMaintain::IsRoomTypeUsed     |
| BR-05 | Access Control        | A user may access a module only when the assigned role has permission for that module.                                                           | modFunction::UserAccessModule     |
| BR-06 | Occupancy Management        | Room status must be updated when check-in or check-out is completed.                                                           | frmBooking::UpdateRoomStatus     |
| BR-07 | User Management        | Each user account must be assigned to a role.                                                           | UserData.UserGroup; frmUserMaintain::SaveUser     |
| BR-08 | Customer Search        | Customer history is displayed only after a customer record is selected.                                                           | frmFindCustomer::ListBooking; lvCustomers_Click     |

---

## 13. Access Control Matrix

| Module / Feature     | System Administrator    | Hotel Manager    | Front Desk Staff    | Auditor / Viewer    |
|----------------------|-------------|-------------|-------------|-------------|
| Dashboard           | Full        | Full        | View Only   | No Access   |
| Booking Management           | Full        | Full        | Full   | No Access   |
| Customer Search           | Full        | Full        | Full   | View Only   |
| Room Management           | Full        | Full        | No Access   | No Access   |
| Room Type Management           | Full        | View Only   | No Access   | No Access   |
| User Management           | Full        | No Access   | No Access   | No Access   |
| Access Control           | Full        | No Access   | No Access   | No Access   |
| Report List           | Full        | Full        | View Only   | View Only   |
| Report Export           | Full        | Full        | View Only   | View Only   |
| Report Maintenance           | Full        | No Access   | No Access   | No Access   |

---

## 14. Timeline & Milestones

| Milestone | Name                      | Key Deliverables                         | Target     |
|-----------|---------------------------|------------------------------------------|------------|
| M1        | Authentication & Security Foundations        | Sign-in, password change, session timeout, password hashing, role model                        | Week 2  |
| M2        | Booking Core        | Booking creation, room selection, totals, payment validation, check-in, check-out                        | Week 5  |
| M3        | Dashboard & Room Operations        | Room status dashboard, summaries, room status updates, alert indicators                        | Week 7  |
| M4        | Master Data Administration        | Room management, room type management, user management, permission management                        | Week 9  |
| M5        | Search, Reports & Audit        | Customer search, history export, report list, report maintenance, audit trail                        | Week 11  |
| M6        | Testing & QA              | Full acceptance testing, bug fixes       | Week 12  |
| M7        | User Training & UAT       | Staff training, user acceptance testing  | Week 13  |
| M8        | Go Live                   | Production deployment                    | Week 14  |

---

## 15. Risks & Mitigations

| ID   | Risk                                    | Likelihood | Impact | Mitigation                                              |
|------|-----------------------------------------|------------|--------|---------------------------------------------------------|
| R-01 | Some role meanings are not explicitly named in the source.                                | Medium        | High    | Confirm business role names and permissions with stakeholders before final design.                                          |
| R-02 | Certain booking status transitions are implied but not fully enumerated.                                | Medium        | High    | Validate full booking lifecycle states during requirements workshops.                                          |
| R-03 | Report output formats are not explicitly defined in the source.                                | Medium        | Medium    | Confirm required export formats for each report before build.                                          |
| R-04 | Existing users may rely on undocumented operational alerts or dashboard behaviors.                                | Medium        | Medium    | Run user walkthroughs of current operations and validate alert expectations.                                          |
| R-05 | Data quality issues in guest, room, or user records may affect migration and adoption.                                | High        | High    | Perform data profiling and cleansing before production rollout.                                          |

---

## 16. Gaps & Inferred Requirements

| ID   | Tag                  | Gap / Inferred Requirement                                                        | Action Required                        |
|------|----------------------|-----------------------------------------------------------------------------------|----------------------------------------|
| G-01 | [INFERRED]                | The dashboard alert feature appears to highlight bookings that need attention, but the exact trigger conditions are not explicit.                                                                 | Confirm alert trigger rules with business users.                               |
| G-02 | [NEEDS CONFIRMATION]                | The full list of booking statuses is not explicitly provided, although booking and room status fields indicate a lifecycle exists.                                                                 | Confirm allowed booking statuses and transitions.                               |
| G-03 | [NEEDS CONFIRMATION]                | User roles are stored as numbered groups, but the business meaning of each role must be validated before implementation.                                                                 | Confirm role names, responsibilities, and access levels.                               |
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
**Last Updated:** 2026-04-21
**Version:** 1.0

> This PRD was generated from a legacy application UAST.
> All requirements are traceable to the source system.
> Items marked [INFERRED] or [NEEDS CONFIRMATION] must be
> validated with business stakeholders before development.