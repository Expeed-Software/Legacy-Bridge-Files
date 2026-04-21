# Product Requirements Document (PRD)
**Product Name:** Hotel Booking System  
**Version:** 1.0  
**Status:** Draft  
**Date:** 2026-04-21  
**Prepared By:** Senior Business Analyst

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

The Hotel Booking System is a business application for managing room availability, guest bookings, payments, room status, user access, and operational reporting for a hotel or lodging business. It supports front desk staff, supervisors, and administrators in handling the full booking lifecycle from reservation through check-in, check-out, receipt generation, customer history lookup, and management reporting.

The product aims to improve operational control, reduce manual errors, and provide clear visibility into room occupancy, customer activity, and staff actions. It also supports controlled access to business features, password management, and audit logging to help maintain accountability and business continuity.

| Attribute        | Detail                        |
|------------------|-------------------------------|
| Product Name     | Hotel Booking System          |
| Business Domain  | Hospitality Operations        |
| Target Platform  | Web application for modern browsers |
| Primary Users    | Front Desk Staff, Supervisors, Administrators |
| Business Problem | Manual or fragmented hotel booking and room management processes reduce accuracy and visibility |
| Expected Benefit | Faster booking operations, better room utilisation, stronger access control, and improved reporting |

---

## 2. Goals & Objectives

### 2.1 Business Goals
- Improve booking accuracy and reduce operational delays in front desk processes.
- Increase visibility of room occupancy, maintenance status, and customer activity.
- Strengthen control over user permissions, password management, and audit history.

### 2.2 Product Goals
- Provide a central system for managing bookings, rooms, room types, users, permissions, and reports.
- Enable users to search customers and review booking history quickly.
- Support operational reporting and export of receipts and business reports.

### 2.3 Success Metrics

| Metric              | Target         | How to Measure        |
|---------------------|----------------|-----------------------|
| Booking completion time | < 3 minutes per standard booking | Timed user acceptance testing |
| Report export success rate | 100% of approved reports export successfully | QA execution results |
| Unauthorised feature access | 0 incidents | Security testing and audit review |

---

## 3. User Personas

### 3.1 Front Desk Staff

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Front Desk Staff                |
| Description  | Handles guest bookings, check-in, check-out, payments, and customer searches. |
| Goals        | Create accurate bookings, assign rooms, record payments, and issue receipts. |
| Pain Points  | Delays in finding rooms, errors in payment entry, and difficulty locating customer history. |
| Tech Level   | Moderate                        |
| Usage        | Daily throughout business hours |
| Modules Used | Authentication, Dashboard, Booking Management, Customer Search, Receipt Export |

### 3.2 Operations Supervisor

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | Operations Supervisor           |
| Description  | Oversees room availability, occupancy, housekeeping, maintenance status, and operational reports. |
| Goals        | Monitor room status, review bookings, and access management reports. |
| Pain Points  | Limited visibility of room status changes and slow access to summary information. |
| Tech Level   | Moderate                        |
| Usage        | Daily and end-of-shift review   |
| Modules Used | Authentication, Dashboard, Room Management, Reporting, Customer History |

### 3.3 System Administrator

| Attribute    | Detail                          |
|--------------|---------------------------------|
| Role         | System Administrator            |
| Description  | Manages user accounts, role permissions, password controls, and report definitions. |
| Goals        | Control access, maintain user records, and support secure operations. |
| Pain Points  | Inconsistent permission settings and lack of central user administration. |
| Tech Level   | High                            |
| Usage        | As needed, with periodic reviews |
| Modules Used | Authentication, User Management, Access Control, Password Management, Report Management |

---

## 4. User Stories

### 4.1 Authentication

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-01  | As a user, I want to sign in with my account so that I can access authorised business features. | High     |
| US-02  | As a user, I want to change my password so that I can keep my account secure. | High     |
| US-03  | As an administrator, I want users to be prompted to change their password when required so that account security is maintained. | High     |
| US-04  | As a user, I want my session to end after inactivity so that unattended screens do not remain accessible. | Medium   |
| US-05  | As a manager, I want failed sign-in attempts to be controlled so that unauthorised access is reduced. | Medium   |

### 4.2 Booking Management

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-06  | As a front desk staff member, I want to create a booking so that I can reserve a room for a guest. | High     |
| US-07  | As a front desk staff member, I want to select an available room so that the booking is assigned correctly. | High     |
| US-08  | As a front desk staff member, I want the system to calculate totals, deposits, payments, and refunds so that charges are accurate. | High     |
| US-09  | As a front desk staff member, I want to check guests in and out so that room occupancy is tracked correctly. | High     |
| US-10  | As a supervisor, I want to void a booking when necessary so that invalid or cancelled bookings do not remain active. | Medium   |

### 4.3 Room & Room Type Management

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-11  | As a supervisor, I want to maintain room records so that room details stay accurate. | High     |
| US-12  | As a supervisor, I want to update room status so that staff can see whether a room is free, occupied, under housekeeping, or under maintenance. | High     |
| US-13  | As an administrator, I want to maintain room types so that rooms can be grouped and priced consistently. | High     |
| US-14  | As a supervisor, I want to see room pricing and room type details so that I can assign the correct room. | Medium   |
| US-15  | As an administrator, I want to prevent removal or invalid changes to room types in use so that active room records remain valid. | Medium   |

### 4.4 Customer Search & History

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-16  | As a front desk staff member, I want to search for customers by name, contact, origin, or passport so that I can find returning guests quickly. | High     |
| US-17  | As a front desk staff member, I want to review a customer's booking history so that I can confirm past stays and transactions. | High     |
| US-18  | As a supervisor, I want to print or export customer transaction history so that I can support guest enquiries. | Medium   |
| US-19  | As a user, I want search results to be selectable so that I can review a specific customer record. | Medium   |
| US-20  | As a user, I want to clear search filters so that I can start a new lookup quickly. | Low      |

### 4.5 User Management & Access Control

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-21  | As an administrator, I want to create and update user accounts so that staff can access the system appropriately. | High     |
| US-22  | As an administrator, I want to assign users to roles so that permissions are managed consistently. | High     |
| US-23  | As an administrator, I want to control access to each business module by role so that users only see authorised features. | High     |
| US-24  | As an administrator, I want to update a user's password when required so that access can be restored securely. | Medium   |
| US-25  | As a manager, I want inactive accounts to be disabled so that former or unavailable staff cannot use the system. | Medium   |

### 4.6 Reporting

| ID     | User Story                                              | Priority |
|--------|---------------------------------------------------------|----------|
| US-26  | As a manager, I want to view the list of available reports so that I can run the correct report for the business need. | High     |
| US-27  | As a manager, I want to run booking reports by period so that I can review performance over time. | High     |
| US-28  | As a manager, I want to review staff-based booking activity so that I can monitor operational performance. | Medium   |
| US-29  | As an administrator, I want to maintain report definitions so that available reports remain accurate and relevant. | Medium   |
| US-30  | As a user, I want to export reports and receipts so that I can share or print business documents. | Medium   |

---

## 5. Functional Requirements

### 5.1 Authentication

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-01 | The system SHALL authenticate users using a unique user ID and password. | High     |
| FR-02 | The system SHALL require users flagged for password change to update their password before continuing to normal system use. | High     |
| FR-03 | The system SHALL allow users to change their password from a dedicated password management screen. | High     |
| FR-04 | The system SHALL validate the current password before accepting a password change. | High     |
| FR-05 | The system SHALL track failed sign-in attempts for each user account. | High     |
| FR-06 | The system SHALL enforce session timeout after a configurable period of inactivity. | High     |

### 5.2 Booking Management

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-07 | The system SHALL create a booking record for a guest with booking date, guest details, room details, stay duration, and payment values. | High     |
| FR-08 | The system SHALL allow users to select a room when creating or updating a booking. | High     |
| FR-09 | The system SHALL retrieve and display room details during booking entry after a room is selected. | High     |
| FR-10 | The system SHALL calculate the booking subtotal based on the selected room price and stay details. | High     |
| FR-11 | The system SHALL record deposit, payment, and refund values for each booking. | High     |
| FR-12 | The system SHALL determine whether a booking is fully paid before allowing payment-dependent actions. | High     |
| FR-13 | The system SHALL support guest check-in for an active booking. | High     |
| FR-14 | The system SHALL support guest check-out for an active booking. | High     |
| FR-15 | The system SHALL update the room status when a booking is checked in, checked out, voided, or otherwise changed. | High     |
| FR-16 | The system SHALL allow authorised users to void a booking. | Medium   |
| FR-17 | The system SHALL generate a receipt for a booking payment transaction. | Medium   |
| FR-18 | The system SHALL support a temporary booking state before final booking confirmation. | Medium   |
| FR-19 | The system SHALL store guest name, contact number, origin, passport number, emergency contact name, and emergency contact number for each booking. | High     |
| FR-20 | The system SHALL store booking remarks for each booking. | Medium   |

### 5.3 Dashboard & Room Status Monitoring

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-21 | The system SHALL display a dashboard summary of room and booking status counts. | High     |
| FR-22 | The system SHALL display the current status of each room on the dashboard. | High     |
| FR-23 | The system SHALL allow authorised users to open booking actions for a selected room from the dashboard. | High     |
| FR-24 | The system SHALL allow authorised users to update a room to Free, Occupied, Housekeeping, or Maintenance status. | High     |
| FR-25 | The system SHALL highlight rooms that require user attention through a visible alert indicator. | Medium   |
| FR-26 | The system SHOULD allow each user to save a personal preference for room alert highlighting. | Medium   |

### 5.4 Customer Search & History

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-27 | The system SHALL allow users to search customer records by guest name. | High     |
| FR-28 | The system SHALL allow users to search customer records by contact number. | High     |
| FR-29 | The system SHALL allow users to search customer records by country or place of origin. | Medium   |
| FR-30 | The system SHALL allow users to search customer records by passport number. | High     |
| FR-31 | The system SHALL display matching customer results in a selectable list. | High     |
| FR-32 | The system SHALL display booking history for a selected customer. | High     |
| FR-33 | The system SHALL allow users to export or print customer transaction history. | Medium   |

### 5.5 Room Management

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-34 | The system SHALL allow authorised users to create and update room records. | High     |
| FR-35 | The system SHALL store room short name, room long name, room location, room type, room price, and status for each room. | High     |
| FR-36 | The system SHALL allow users to view a list of rooms. | High     |
| FR-37 | The system SHALL allow users to select a room from the room list to view or edit its details. | High     |
| FR-38 | The system SHALL allow users to mark a room as under housekeeping. | Medium   |
| FR-39 | The system SHALL allow users to mark a room as under maintenance. | Medium   |

### 5.6 Room Type Management

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-40 | The system SHALL allow authorised users to create and update room type records. | High     |
| FR-41 | The system SHALL store a short name and long name for each room type. | High     |
| FR-42 | The system SHALL display a list of room types for selection and maintenance. | High     |
| FR-43 | The system SHALL prevent deletion or invalid modification of a room type that is currently assigned to a room. | High     |

### 5.7 User Management

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-44 | The system SHALL allow authorised users to create and update user accounts. | High     |
| FR-45 | The system SHALL store user ID, user name, role, account status, password change flag, failed login attempt count, and idle timeout setting for each user. | High     |
| FR-46 | The system SHALL allow authorised users to assign a role to each user account. | High     |
| FR-47 | The system SHALL allow authorised users to activate or deactivate user accounts. | High     |
| FR-48 | The system SHALL allow authorised users to update a user's password when permitted by access rights. | Medium   |
| FR-49 | The system SHALL display a searchable list of user accounts for maintenance. | Medium   |

### 5.8 Access Control

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-50 | The system SHALL enforce role-based access control for all business modules. | High     |
| FR-51 | The system SHALL allow authorised administrators to configure module access by role. | High     |
| FR-52 | The system SHALL load only the modules authorised for the signed-in user's role. | High     |
| FR-53 | The system SHALL restrict unauthorised users from viewing or performing actions in protected modules. | High     |

### 5.9 Reporting

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-54 | The system SHALL display a list of available reports to authorised users. | High     |
| FR-55 | The system SHALL allow authorised users to run booking reports. | High     |
| FR-56 | The system SHALL allow authorised users to run daily, weekly, and monthly booking reports. | High     |
| FR-57 | The system SHALL allow authorised users to run booking reports by staff member. | Medium   |
| FR-58 | The system SHALL allow authorised users to maintain report definitions. | Medium   |
| FR-59 | The system SHALL export reports in a shareable format suitable for printing or distribution. | Medium   |

### 5.10 Audit & Error Logging

| ID    | Requirement                                                          | Priority |
|-------|----------------------------------------------------------------------|----------|
| FR-60 | The system SHALL record booking changes in an audit history with user and timestamp details. | High     |
| FR-61 | The system SHALL record room changes in an audit history with user and timestamp details. | Medium   |
| FR-62 | The system SHALL log application errors with date, time, user, module, and error description. | Medium   |

---

## 6. Non-Functional Requirements

| ID     | Category       | Requirement                                                      | Metric / Target          | Priority |
|--------|----------------|------------------------------------------------------------------|--------------------------|----------|
| NFR-01 | Performance    | The system SHALL load the main dashboard within the target time. | < 2 seconds              | High     |
| NFR-02 | Performance    | The system SHALL complete all save and update operations within the target time under normal load. | < 1 second               | High     |
| NFR-03 | Security       | The system SHALL store all passwords using an industry-standard hashing algorithm with a unique salt per user. | bcrypt or Argon2         | High     |
| NFR-04 | Security       | The system SHALL enforce role-based access control on all screens, actions, and reports. | 0 unauthorised accesses  | High     |
| NFR-05 | Security       | The system SHALL expire sessions after configurable inactivity.  | Configurable in minutes  | High     |
| NFR-06 | Reliability    | The system SHALL display a clear error message and log the incident when a transaction cannot be completed. | 100% of critical errors logged | High |
| NFR-07 | Reliability    | The system SHALL maintain service availability during business hours. | > 99% uptime             | High     |
| NFR-08 | Usability      | New users SHALL be able to complete booking, search, and check-out tasks after training. | 30–60 minutes training   | Medium   |
| NFR-09 | Usability      | The system SHALL display clear validation messages for missing or invalid data. | Every validation error explained | Medium |
| NFR-10 | Compatibility  | The system SHALL run on all modern desktop browsers used by hotel operations staff. | Latest two versions of major browsers | High |
| NFR-11 | Scalability    | The system SHALL support the expected number of concurrent users without business process degradation. | 50 concurrent users      | Medium   |
| NFR-12 | Auditability   | The system SHALL log all critical booking, room, user, and access changes with user and timestamp. | Full audit trail         | Medium   |

---

## 7. Scope

### 7.1 In Scope — Version 1.0

- User authentication, password change, session timeout, and role-based access control.
- Booking lifecycle management including room selection, payment tracking, check-in, check-out, voiding, and receipt generation.
- Room, room type, user, access control, customer search, history lookup, reporting, and audit logging.

### 7.2 Out of Scope — Version 1.0

- Public online guest self-service booking.
- External payment gateway integration.
- Email or SMS notifications to guests.

### 7.3 Future Scope — Version 2.0+

- Guest self-service portal for reservations and booking history.
- Automated notifications for booking confirmation, check-in, and payment events.
- Integration with accounting or property management systems.

---

## 8. Assumptions & Constraints

### 8.1 Assumptions

- Hotel staff will use named user accounts rather than shared logins.
- Room status, booking status, and user roles will be defined and approved by business stakeholders before build completion.
- Reports currently used by operations will remain required in the new product unless formally removed from scope.

### 8.2 Constraints

| Type        | Constraint                                               |
|-------------|----------------------------------------------------------|
| Technology  | The product must be delivered as a browser-based business application. |
| Budget      | Version 1.0 must focus on core hotel operations and avoid non-essential enhancements. |
| Timeline    | Core operational features must be prioritised for initial release within a phased delivery plan. |
| Team        | Business analysis, design, development, QA, and user training resources must be shared across the programme. |
| Platform    | The product must support desktop use in hotel front desk operations. |
| Integration | Version 1.0 will operate as a standalone hotel operations system unless stakeholders approve external integrations. |

---

## 9. Screen & Feature Descriptions

### 9.1 Login Screen

**Purpose:** Allow users to securely access the system with their credentials.

**Key Elements:**
- User ID field
- Password field
- Sign-in action
- Password change prompt when required

**Actions Available:**
- Sign in
- Cancel sign-in
- Open password change process when required

**Access:** All users

---

### 9.2 Main Dashboard

**Purpose:** Provide an operational overview of room status and booking activity.

**Key Elements:**
- Room status summary
- Room status grid
- Alert indicators
- Shortcut actions for room-related tasks

**Actions Available:**
- View room status
- Open booking for a selected room
- Update room status where authorised

**Access:** Front Desk Staff, Operations Supervisor, System Administrator

---

### 9.3 Booking Management Screen

**Purpose:** Create, update, and process guest bookings through the booking lifecycle.

**Key Elements:**
- Guest information
- Room selection details
- Stay dates and times
- Payment and refund values
- Booking status information

**Actions Available:**
- Create booking
- Check in guest
- Check out guest
- Void booking
- Generate receipt

**Access:** Front Desk Staff, Operations Supervisor

---

### 9.4 Customer Search Screen

**Purpose:** Find customer records and review historical bookings and transactions.

**Key Elements:**
- Search filters
- Customer result list
- Booking history panel
- History export action

**Actions Available:**
- Search customers
- Select customer
- View booking history
- Export or print customer history

**Access:** Front Desk Staff, Operations Supervisor

---

### 9.5 Room Management Screen

**Purpose:** Maintain room records and operational room status.

**Key Elements:**
- Room list
- Room details form
- Room type selector
- Price and status fields

**Actions Available:**
- Add room
- Edit room
- Mark room as housekeeping
- Mark room as maintenance

**Access:** Operations Supervisor, System Administrator

---

### 9.6 Room Type Management Screen

**Purpose:** Maintain the room type catalogue used for room classification.

**Key Elements:**
- Room type list
- Short name field
- Long name field

**Actions Available:**
- Add room type
- Edit room type
- Select room type

**Access:** System Administrator

---

### 9.7 User Management Screen

**Purpose:** Maintain user accounts and role assignments.

**Key Elements:**
- User list
- User details form
- Role selector
- Account status and password controls

**Actions Available:**
- Add user
- Edit user
- Activate or deactivate account
- Update password setting

**Access:** System Administrator

---

### 9.8 Access Control Screen

**Purpose:** Configure which roles may access each business module.

**Key Elements:**
- Module list
- Role permission matrix
- Save action

**Actions Available:**
- View permissions
- Update permissions
- Save access settings

**Access:** System Administrator

---

### 9.9 Reports & Analytics Screen

**Purpose:** Provide access to operational reports and report maintenance.

**Key Elements:**
- Report list
- Report details
- Export action
- Report maintenance form

**Actions Available:**
- View reports
- Run report
- Export report
- Edit report definition

**Access:** Operations Supervisor, System Administrator

---

### 9.10 Password Change Screen

**Purpose:** Allow users to change passwords securely.

**Key Elements:**
- Current password field
- New password field
- Confirm password field

**Actions Available:**
- Validate current password
- Save new password
- Cancel

**Access:** All users with an active account

---

## 10. Acceptance Criteria

| Feature               | Given                          | When                          | Then                                        |
|-----------------------|--------------------------------|-------------------------------|---------------------------------------------|
| User Login            | Given an active user account with valid credentials | When the user submits the login form | Then the system grants access according to the user's role |
| Forced Password Change | Given a user account marked for password change | When the user signs in successfully | Then the system requires a password update before allowing access to other features |
| Booking Creation      | Given a user has entered guest details, room details, and stay information | When the user saves the booking | Then the system creates the booking and stores the booking values |
| Payment Validation    | Given a booking has outstanding payment | When the user attempts a payment-dependent action | Then the system prevents the action until payment conditions are satisfied |
| Check-In              | Given an active booking with an assigned room | When the user performs check-in | Then the system records guest check-in and updates the room status |
| Check-Out             | Given a checked-in booking | When the user performs check-out | Then the system records guest check-out and updates the room status |
| Room Status Update    | Given a user with room status permission | When the user marks a room as housekeeping or maintenance | Then the system saves the selected room status |
| Customer Search       | Given customer records exist | When the user searches by name, contact, origin, or passport number | Then the system displays matching customers in a selectable list |
| Customer History      | Given a customer has previous bookings | When the user selects the customer | Then the system displays the customer's booking history |
| Module Access Control | Given a user role lacks permission to a module | When the user attempts to access that module | Then the system denies access |
| Report Execution      | Given a user has access to reports | When the user selects a report to run | Then the system displays or exports the report output |
| Room Type Protection  | Given a room type is assigned to one or more rooms | When the user attempts to delete or invalidate that room type | Then the system prevents the action |

---

## 11. Data Model Summary

### 11.1 Core Entities

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| Booking         | Stores guest reservation, stay, and payment details | Booking ID, Guest Name, Room ID, Stay Duration, Deposit, Payment, Refund, Status |
| Room            | Stores room details, pricing, and current status | Room ID, Room Short Name, Room Long Name, Room Type, Room Price, Room Status |
| Room Type       | Defines the classification of rooms  | Room Type ID, Short Name, Long Name                     |
| User Account    | Stores user identity, access role, and account controls | User ID, User Name, Role, Password Hash, Failed Login Attempts, Idle Timeout, Change Password Flag |
| User Role       | Defines business roles and security level | Role ID, Role Name, Role Description, Security Level    |
| Module Access   | Stores role permissions by module    | Module ID, Module Description, Role Permissions         |
| Report          | Stores report definitions and report metadata | Report ID, Report Name, Report Title, Date Field, Active Status |
| Company Profile | Stores organisation-level information used in documents and reporting | Company Name, Registration Number, Tax Number, Contact Number, Address, Currency Symbol |

### 11.2 Audit & System Tables

| Entity          | Purpose                              | Key Fields                                              |
|-----------------|--------------------------------------|---------------------------------------------------------|
| Booking Audit Log | Stores historical booking changes for traceability | Booking ID, Guest Name, Room ID, Payment, Status, Last Modified By, Last Modified Date |
| Room Audit Log  | Stores historical room changes for traceability | Room ID, Room Status, Room Price, Booking ID, Last Modified By, Last Modified Date |
| Error Log       | Stores application error events for support and review | Log Date Time, Error Description, Error Number, Module, User Name, Log Type |

---

## 12. Business Rules

| ID    | Domain          | Business Rule                                                              |
|-------|-----------------|----------------------------------------------------------------------------|
| BR-01 | Authentication  | A user flagged for password change must update the password before accessing normal system functions. |
| BR-02 | Access Control  | A user may only access modules assigned to the user's role. |
| BR-03 | Booking         | A booking must contain guest details, room details, and stay details before it can be saved. |
| BR-04 | Booking         | Room status must change when a guest checks in, checks out, or a booking is voided. |
| BR-05 | Room Type Management | A room type that is currently used by a room cannot be deleted or invalidated. |
| BR-06 | Customer Search | Customer history must be linked to the selected customer record. |
| BR-07 | Reporting       | Only authorised users may run or maintain reports. |
| BR-08 | Audit           | Critical booking and room changes must be recorded with user and timestamp details. |

---

## 13. Access Control Matrix

| Module / Feature     | Front Desk Staff | Operations Supervisor | System Administrator | Auditor |
|----------------------|------------------|-----------------------|----------------------|---------|
| Authentication       | Full             | Full                  | Full                 | Full    |
| Dashboard            | Full             | Full                  | View Only            | View Only |
| Booking Management   | Full             | Full                  | View Only            | View Only |
| Customer Search      | Full             | Full                  | View Only            | View Only |
| Room Management      | No Access        | Full                  | Full                 | View Only |
| Room Type Management | No Access        | View Only             | Full                 | View Only |
| User Management      | No Access        | No Access             | Full                 | View Only |
| Access Control       | No Access        | No Access             | Full                 | View Only |
| Reporting            | View Only        | Full                  | Full                 | View Only |
| Report Maintenance   | No Access        | View Only             | Full                 | No Access |
| Audit Logs           | No Access        | View Only             | Full                 | Full    |

---

## 14. Timeline & Milestones

| Milestone | Name                      | Key Deliverables                         | Target     |
|-----------|---------------------------|------------------------------------------|------------|
| M1        | Discovery & Validation    | Confirm business rules, roles, workflows, and report catalogue | Week 2     |
| M2        | Authentication & Access   | Login, password change, session timeout, role-based access control | Week 4     |
| M3        | Booking Core              | Booking create/update, room selection, totals, payments, check-in, check-out | Week 7     |
| M4        | Room & Customer Operations| Dashboard, room management, room type management, customer search, history | Week 10    |
| M5        | Reporting & Audit         | Report list, report execution, export, audit logging, error logging | Week 12    |
| M6        | Testing & QA              | Full acceptance testing, bug fixes       | Week 14    |
| M7        | User Training & UAT       | Staff training, user acceptance testing  | Week 15    |
| M8        | Go Live                   | Production deployment                    | Week 16    |

---

## 15. Risks & Mitigations

| ID   | Risk                                    | Likelihood | Impact | Mitigation                                              |
|------|-----------------------------------------|------------|--------|---------------------------------------------------------|
| R-01 | Business users may interpret room statuses differently across departments. | Medium     | High   | Define and approve a common room status glossary before build completion. |
| R-02 | Payment rules for booking completion may not be fully agreed. | Medium     | High   | Run stakeholder workshops to confirm deposit, payment, and refund conditions. |
| R-03 | Role permissions may be incomplete or inconsistent at launch. | High       | High   | Produce and approve a full access control matrix during discovery. |
| R-04 | Operational reports may contain fields no longer needed or missing required filters. | Medium     | Medium | Validate each report with report owners before development starts. |
| R-05 | Historical data quality may affect customer search and booking history accuracy. | Medium     | High   | Perform data profiling and cleansing before migration and user acceptance testing. |

---

## 16. Gaps & Inferred Requirements

| ID   | Tag                  | Gap / Inferred Requirement                                                        | Action Required                        |
|------|----------------------|-----------------------------------------------------------------------------------|----------------------------------------|
| G-01 | [INFERRED]           | The booking process appears to support a draft or temporary booking state before final confirmation. | Confirm the exact meaning and lifecycle of temporary bookings with stakeholders. |
| G-02 | [NEEDS CONFIRMATION] | The exact payment rule for allowing check-in or check-out is not fully explicit and may depend on full payment or minimum deposit. | Confirm payment gating rules for each booking action. |
| G-03 | [INFERRED]           | Dashboard alerts appear to indicate rooms or bookings needing attention, but the exact trigger conditions are not explicit. | Define the business events that should trigger dashboard alerts. |
| G-04 | [BEST PRACTICE]      | System should support two-factor authentication (2FA) for admin accounts.         | Confirm with stakeholders for v1.0     |
| G-05 | [BEST PRACTICE]      | System should send email or SMS notifications for key business events.            | Confirm scope with product owner       |
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

> Items marked [INFERRED] or [NEEDS CONFIRMATION] must be validated with
> business stakeholders before development begins.