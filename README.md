# EVENTFORCE-MANAGEMENT-SYSTEM
EventForce Management System is a Salesforce-based CRM application designed to streamline event management operations by centralizing events, clients, vendors, venues, and feedback in one platform

# EventForce Management System – Salesforce Implementation

Hey there! 👋 Welcome to my repository. This project is part of my hands-on learning and implementation with Salesforce. I built a complete CRM for an event planning business that digitizes bookings, venues, vendors, and feedback in one platform.

## 💡 What this project is about

Event planners often juggle client bookings, venue reservations, vendor coordination, and feedback across spreadsheets and phone calls. **EventForce Management System** brings all of this into a single Salesforce CRM, with automated reminders, cancellation approvals, double-booking prevention, and real-time reports and dashboards.

**Business goals:** reduce manual processes by 60%, improve booking efficiency by 40%, and give clients timely communication backed by data-driven reporting.

---

## 🛠️ How I Built It (Key Components)

- **Custom Objects:** Event, Client, Vendor, Venue, Feedback, and Event Vendor (junction object).
- **Relationships:** Lookup (Event → Client, Event → Venue, Feedback → Event, Feedback → Client), Master-Detail (Event Vendor → Event / Vendor), and Many-to-Many (Event ↔ Vendor), plus a lookup filter on Feedback.
- **Validation Rules & Formula Fields:** Email format validation on Client; Event Budget calculated from Event Type.
- **Approval Process:** Event cancellation workflow with email alerts to coordinators, clients, and event owners.
- **Record-Triggered Flow:** Sends the client a reminder email 3 days before a confirmed event.
- **Apex:** Venue status updates, double-booking prevention trigger, and a nightly batch job that marks past events as Completed.
- **Security:** Custom Profiles, Roles, Permission Set, Organization-Wide Defaults (Private), and Sharing Rules.
- **Lightning App, Reports & Dashboard:** "Event Planner" app, "Upcoming Events by Month" report, and "EventForce Operations Dashboard".

---

## 🗂️ Data Model

| Object | Key Fields |
|--------|-----------|
| `Event__c` | Event Name, Event Date, Event Type, Event Status, Event Budget (formula) |
| `Client__c` | Client Name, Email, Phone, Address, Country, City |
| `Vendor__c` | Vendor Name, Email, Phone, Service Type, Status |
| `Venue__c` | Venue Name, Address, Location (URL), Capacity, Availability Status |
| `Feedback__c` | Feedback ID (Auto Number), Rating, Comments |
| `EventVendor__c` | Junction object linking Event ↔ Vendor |

| Relationship | Type |
|--------------|------|
| Event → Client | Lookup |
| Event → Venue | Lookup |
| Feedback → Event | Lookup |
| Feedback → Client | Lookup |
| EventVendor → Event | Master-Detail |
| EventVendor → Vendor | Master-Detail |

---

## 📂 Project Structure & Workflow

The project was implemented in five phases:

1. **Requirement Analysis & Planning:** Functional scope, stakeholder mapping, and execution roadmap.
2. **Backend Development & Configuration:** Objects, tabs, fields, relationships, validation rules, approval process, flow, and Apex.
3. **UI/UX Customization:** Lightning App, reports, and dashboard.
4. **Data Migration, Testing & Security:** Profiles, roles, users, permission set, and sharing settings.
5. **Deployment, Documentation & Maintenance:** Simulated deployment, monitoring approach, and documentation.

```
eventforce-management-system/
├── README.md
├── docs/
│   └── setup_guide.md        # Step-by-step build guide
├── apex/                     # Apex source code
│   ├── VenueStatusHelper.cls
│   ├── EventTrigger13.trigger
│   ├── PreventDoubleBooking.trigger
│   ├── BatchCompleteEvents.cls
│   └── ScheduleCompleteEvents.cls
└── screenshots/              # Proof of each configuration step
```

See the full walkthrough in [docs/setup_guide.md](docs/setup_guide.md).

---

## 👥 Stakeholders & Roles

| Role | Responsibility |
|------|---------------|
| Client | Requests event services, gives feedback, views own events |
| Event Coordinator | Manages scheduling, client communication, and approvals |
| Vendor Manager | Handles vendor services such as catering, decor, and logistics |
| Event Admin | Oversees system operations, security, and data |

---

## 📸 Screenshots

| Step | Preview |
|------|---------|
| Objects & Fields | ![Objects](screenshots/01-objects-and-fields.png) |
| Relationships | ![Relationships](screenshots/02-relationships.png) |
| Approval Process | ![Approval](screenshots/03-approval-process.png) |
| 3-Day Reminder Flow | ![Flow](screenshots/04-reminder-flow.png) |
| Apex Code | ![Apex](screenshots/05-apex.png) |
| Lightning App | ![App](screenshots/06-lightning-app.png) |
| Report & Dashboard | ![Dashboard](screenshots/07-dashboard.png) |

---

## 👨‍💻 Team Information / Contributors

- **Project Name:** EventForce Management System – Salesforce Implementation
- **Platform:** Salesforce Developer Edition Org

| Role      | Name            |
|-----------|-----------------|
| Team Lead | Swetha .RM      |
| Member    | Iswarya .C      |
| Member    | Meenakshi .N    |

---

## ✨ What I Learned & Achieved

- Designed a relational data model with lookup, master-detail, and many-to-many relationships.
- Automated reminders and cancellation approvals using Flow and Approval Processes.
- Wrote Apex triggers and batch jobs to enforce business rules and keep data accurate.
- Secured the app with profiles, roles, permission sets, OWD, and sharing rules.
- Built reports and dashboards for real-time visibility into event operations.

---

## 🚀 Future Improvements

- Real production deployment using sandboxes, Salesforce DX, and GitHub CI/CD.
- Apex test classes with code coverage for all triggers and batch jobs.
- Client self-service portal using Experience Cloud.
- More dashboards for vendor performance and client satisfaction.

*Feel free to check out the screenshots and configuration steps in this repo. If you have any feedback or want to collaborate, let's connect!*
