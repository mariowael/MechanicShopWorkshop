# Mechanic Shop Management System

A web-based workshop management platform designed to digitize and streamline automotive repair shop operations, including customer and vehicle management, repair tasks, work orders, scheduling, technician assignment, and operational monitoring.

---

## Product Requirements Document (PRD)

**Product:** Mechanic Shop Management System
**Version:** 1.0
**Date:** September 2026
**Document Owner:** Product Management Team

---

## 1. Product Overview

The **Mechanic Shop Management System** is a workshop operations platform designed to manage:

* Customers
* Vehicles
* Repair tasks
* Work orders
* Scheduling
* Technician assignments
* Service bays
* Operational monitoring

The system provides a centralized platform for tracking repair jobs, preventing scheduling conflicts, and maintaining service quality through standardized repair procedures.

---

## 2. Problem Statement

Mechanic shops often lack a centralized system to:

* Track customer and vehicle information efficiently
* Manage the complete lifecycle of repair jobs
* Prevent scheduling conflicts and technician double-booking
* Monitor operational performance
* Enforce business rules consistently

The system aims to digitize these operations and provide a **single source of truth** for workshop management.

---

## 3. Goals & Objectives

The system aims to:

* Digitize workshop operations and eliminate paper-based processes
* Reduce scheduling conflicts and double-booking errors
* Improve visibility into work order status for staff and management
* Enable data-driven operational decisions through real-time metrics
* Standardize repair processes through reusable task templates
* Increase operational efficiency and customer satisfaction

---

## 4. User Roles & Permissions

| Role                   | Permissions                                                                                        |
| ---------------------- | -------------------------------------------------------------------------------------------------- |
| **Manager**            | Full system access: manage customers, vehicles, repair tasks, work orders, scheduling, and reports |
| **Technician (Labor)** | View assigned work orders and update work order status to `InProgress` or `Completed`              |

---

# 5. Functional Requirements

## 5.1 Customer & Vehicle Management

### Manager Capabilities

As a Manager, I need to:

* Register new customers with contact information
* Update customer details:

  * Name
  * Phone
  * Email
  * Address
* Delete customer records that have no associated work orders
* Add vehicles to customer accounts:

  * Make
  * Model
  * Year
  * VIN
* Update vehicle information
* Remove vehicles that have no active work orders

### Business Rules

* Customers cannot be deleted if they have existing work orders
* Vehicles cannot be deleted if they have active work orders
* VIN must be unique across the system
* All contact information must be validated

---

## 5.2 Repair Task Catalog

### Manager Capabilities

As a Manager, I need to:

* Create repair task templates
* Define:

  * Task name
  * Description
  * Estimated cost
  * Estimated duration
* Add required parts to repair task templates
* Update repair task details and associated parts
* Delete repair task templates from the catalog

### Business Rules

* Repair tasks are reusable templates, not unique instances
* The same task template can be used in multiple work orders
* Deleting a task template must not affect existing work orders that reference it

### Acceptance Criteria

* Task template names must be unique
* Estimated cost must be positive
* Estimated duration must be positive
* Parts can be added, updated, or removed from a task template

---

# 5.3 Work Order Management

### Manager Capabilities

As a Manager, I need to:

* Create work orders for specific vehicles
* Add repair tasks to work orders from the task catalog
* Assign technicians to work orders
* Assign service bays
* Change scheduled time
* Reschedule work orders
* Delete work orders when permitted
* View work order summaries
* View complete work order details, including:

  * Tasks
  * Parts
  * Labor
  * Costs
* Update work order status throughout its lifecycle

### Technician Capabilities

As a Technician, I need to:

* View my assigned work orders
* Mark a work order as `InProgress` when work begins
* Mark a work order as `Completed` when work finishes

---

## Work Order Lifecycle

```text
                 ┌──────────────┐
                 │   Scheduled  │
                 └──────┬───────┘
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
      ┌─────────────┐       ┌─────────────┐
      │  InProgress │       │  Cancelled  │
      └──────┬──────┘       └─────────────┘
             │
             ▼
      ┌─────────────┐
      │  Completed  │
      └─────────────┘
```

### Valid Transitions

| Current Status | Allowed Transition |
| -------------- | ------------------ |
| `Scheduled`    | → `InProgress`     |
| `Scheduled`    | → `Cancelled`      |
| `InProgress`   | → `Completed`      |
| `InProgress`   | → `Cancelled`      |

### Business Rules

* A work order cannot be created for a non-existent vehicle
* A technician cannot be assigned to overlapping time slots
* A work order cannot be deleted once it is `InProgress`
* Scheduled work orders are automatically cancelled 15 minutes after the scheduled time if the customer does not show
* Only Managers can create, delete, and reschedule work orders
* Technicians can only update the status of their assigned work orders

### Acceptance Criteria

* New work orders default to `Scheduled`
* Invalid state transitions are rejected with clear error messages
* No-show auto-cancellation occurs 15 minutes after the scheduled time
* Work orders display total estimated cost
* Work orders display total estimated duration

---

# 5.4 Scheduling

### Manager Capabilities

As a Manager, I need to:

* Schedule work orders for specific dates and times
* Assign service bays
* View the daily schedule by date
* View schedules by technician
* Reschedule existing work orders
* Cancel scheduled work orders

### Important Constraint

All scheduling operations must be performed **from the Schedule Page only**.

Other pages may display schedule information but cannot modify it.

This provides a single point of control and helps prevent scheduling conflicts.

### Business Rules

* A technician cannot have overlapping work orders
* A service bay cannot have overlapping work orders
* A work order cannot be removed from the schedule once it is `InProgress`
* The schedule should show availability gaps for optimal planning

### Acceptance Criteria

* Daily schedule displays all work orders for the selected date
* Technician view displays assignments for the selected technician
* Scheduling conflicts have clear visual indicators
* UI supports drag-and-drop rescheduling

---

# 5.5 Labor Management

### Manager Capabilities

As a Manager, I need to:

* View all technicians
* View technician availability
* Assign technicians to work orders
* Reassign work orders when necessary

### Acceptance Criteria

* Technicians cannot be double-booked
* System prevents overlapping assignments
* Technicians can only view their own assigned work orders

---

# 5.6 Dashboard & Reporting

### Manager Capabilities

As a Manager, I need to:

* View work order statistics by date
* View total work orders
* View completed work orders
* View in-progress work orders
* View cancelled work orders

### Future Enhancements

The following are outside the current scope:

* Revenue tracking
* Technician productivity metrics
* Customer history reports
* Parts usage reports

### Acceptance Criteria

* Dashboard updates in real-time or near-real-time
* Statistics can be filtered by date range
* Dashboard statistics accurately reflect work order data

---

# 5.7 Authentication & Authorization

### User Capabilities

Users should be able to:

* Log in using username and password
* Remain authenticated for a reasonable session duration
* Log out when finished

### Security Requirements

* All pages except the login page require authentication
* Managers have access to all features
* Technicians only have access to their assigned work orders
* Passwords must never be stored in plain text
* Sessions expire after a period of inactivity
* Failed login attempts should be rate-limited

### Acceptance Criteria

* Unauthenticated users are redirected to the login page
* Invalid credentials display a clear error message
* Users only see features they are authorized to access
* Sessions persist across page refreshes
* Logout completely clears the user session

---

# 6. Non-Functional Requirements

## Performance

* Page load time: **< 2 seconds** under normal conditions
* Standard API response time: **< 500 ms**
* Support at least **10 concurrent users** without degradation
* Database queries should be optimized

## Availability

* System available during business hours: **6 AM – 8 PM**
* Planned maintenance should be communicated in advance
* Database data should be backed up daily

## Usability

* Intuitive interface requiring minimal training
* Clear and actionable error messages
* Responsive design for desktop and tablet
* Consistent navigation across all pages

## Security

* HTTPS for sensitive data transmission
* Password complexity requirements
* User sessions expire after 30 minutes of inactivity
* Audit log records:

  * Who performed the action
  * What was changed
  * When it was changed

## Data Integrity

* Database operations should be transactional
* Input validation prevents invalid data
* Referential integrity must be maintained
* Customers with work orders cannot be deleted
* Data recovery must be possible using daily backups

## Maintainability

* Consistent coding standards
* Clear API documentation
* Business logic separated from data access
* Automated tests for critical workflows

---

# 7. User Stories

## Epic: Work Order Management

### Story 1 — Create Work Order

**As a Manager**,
I want to create a work order for a customer's vehicle,
so that I can schedule and track repair work.

#### Acceptance Criteria

* Select customer and vehicle from dropdowns
* Add multiple repair tasks from the catalog
* Assign technician
* Assign service bay
* Set scheduled date and time
* Validate scheduling conflicts
* Create the work order with `Scheduled` status

---

### Story 2 — Start Work on Vehicle

**As a Technician**,
I want to mark my assigned work order as `InProgress`,
so that everyone knows I am actively working on it.

#### Acceptance Criteria

* Technician can only update work orders assigned to them
* Only `Scheduled → InProgress` transition is allowed
* Timestamp is recorded when status changes
* Manager can see the updated status immediately

---

### Story 3 — Handle Customer No-Show

**As a Manager**,
I want work orders to be automatically cancelled if the customer does not arrive,
so that I can release the service bay and reschedule the appointment.

#### Acceptance Criteria

* Work order is auto-cancelled 15 minutes after scheduled time
* Only work orders still in `Scheduled` status are affected
* Cancellation reason is recorded as `Auto-cancelled: No-show`
* Manager is notified of the auto-cancellation

---

## Epic: Scheduling

### Story 4 — Prevent Double-Booking

**As a Manager**,
I want the system to prevent overlapping assignments,
so that technicians and service bays are not double-booked.

#### Acceptance Criteria

* Cannot assign a technician to overlapping time slots
* Cannot assign a service bay to overlapping time slots
* Clear error message is displayed when a conflict is detected
* System can suggest alternative times or technicians

---

# 8. Out of Scope

The following features are explicitly excluded from Version 1.0.

## Phase 1 Exclusions

* Parts inventory and stock management
* Financial transactions and payment processing
* Customer email/SMS communication
* Multiple shop locations
* Mobile application
* Customer self-service portal
* Third-party integrations such as QuickBooks or parts suppliers

### Rationale

These features are excluded from the initial release to maintain focus on the core workshop workflow, work order management, and scheduling.

Advanced features such as inventory management and payment processing may be considered for Phase 2 based on user feedback and business requirements.

---

# 9. Success Metrics

## Operational Metrics

Measured approximately six months after launch:

* Reduce scheduling conflicts by **90%**
* Achieve **100% work order tracking accuracy**
* Achieve **>80% user adoption** within the first month
* Average work order creation time: **< 2 minutes**
* Reduce customer appointment no-shows by **30%**
* Provide **100% visibility** into technician utilization

## User Satisfaction Metrics

* Manager satisfaction score: **> 4/5**
* Technician ease-of-use score: **> 4/5**
* System uptime during business hours: **> 99%**

---

# 10. Assumptions & Dependencies

## Assumptions

* Single shop location
* 2–4 service bays
* 3–10 technicians
* Customers are registered before creating work orders
* Internet connectivity is available during business hours
* Users have basic computer literacy

## Dependencies

* Web server for application hosting
* Database server
* Modern web browser:

  * Chrome
  * Firefox
  * Safari
  * Edge
* No external system integrations required for Version 1.0

---

# 11. Constraints

## Technical Constraints

* Must be a web-based application
* Must support modern web browsers
* Internet Explorer 11 is not supported
* Must use RESTful API architecture for future extensibility

## Business Constraints

* Initial release targets a single shop
* No multi-tenancy required in Phase 1
* English language only
* US date/time formats

## Resource Constraints

* Development budget: **To be defined**
* Timeline: **To be defined**
* Team size: **To be defined**

---

# 12. Acceptance Criteria Summary

## Must Have — P0

* [x] User authentication and role-based access
* [x] Customer and vehicle CRUD operations
* [x] Repair task catalog management
* [x] Work order lifecycle management
* [x] Scheduling with conflict prevention
* [x] Auto-cancellation of customer no-shows
* [x] Basic dashboard with statistics

## Should Have — P1

* [ ] Audit logging
* [ ] Advanced search and filtering
* [ ] Data export capabilities

## Could Have — P2

* [ ] Reporting and analytics
* [ ] Email notifications
* [ ] Calendar-based schedule view

## Won't Have — Version 1.0

* Payment processing
* Inventory management
* Multi-location support
* Mobile application

---

# 13. Glossary

| Term                   | Definition                                                                        |
| ---------------------- | --------------------------------------------------------------------------------- |
| **Work Order**         | A job request to perform repair work on a specific vehicle                        |
| **Repair Task**        | A reusable template defining a standard repair/service procedure                  |
| **Labor / Technician** | A technician responsible for performing repair work                               |
| **Service Bay**        | A physical location where repair work is performed                                |
| **Schedule**           | Allocation of work orders to specific dates, times, technicians, and service bays |
| **No-Show**            | A situation where a customer fails to arrive for a scheduled appointment          |

---

## Document Information

| Field                | Value                                             |
| -------------------- | ------------------------------------------------- |
| **Document Version** | 1.0                                               |
| **Product Version**  | 1.0                                               |
| **Last Updated**     | September 2026                                    |
| **Document Owner**   | Product Management Team                           |
| **Stakeholders**     | Business Operations, Engineering, Shop Management |

---

## Project Status

**Version 1.0 — Core Workshop Management**

The current version focuses on the essential workshop workflow:

**Customers → Vehicles → Repair Tasks → Work Orders → Scheduling → Technicians → Dashboard**

Future versions may introduce inventory management, financial processing, notifications, analytics, mobile applications, and third-party integrations.
