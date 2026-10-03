# EventForce Management System — Salesforce Setup Guide

This document is an independently worded implementation guide for rebuilding the **EventForce Management System** in a Salesforce Developer Edition org. The configuration values, object model, automation behavior, security structure, and project milestones are retained from the supplied EventForce implementation document, while the explanatory wording and instructions have been rewritten for original documentation.

> **Implementation order:** Configure the Profiles, Roles, and Users before building the Event Cancellation Approval Process. The approval configuration depends on the user hierarchy and approver setup.

---

## 1. Salesforce Developer Edition Setup — Milestone 1

### Create the development organization

1. Open the Salesforce Developer Edition registration page.
2. Complete the registration form with your name, email, developer role, college/company information, country, postal code, and a unique Salesforce username.
3. Use a username in the form `username@organization.com`. It does not have to be an active email address, but it must be unique in Salesforce.
4. Submit the registration form.
5. Open the verification email sent to the registration address.
6. Verify the account, create the password, answer the security question, and enter Salesforce Setup.

---

## 2. Data Model and Custom Objects — Milestone 2

The EventForce application is built around five main business objects plus one junction object used for the Event–Vendor many-to-many relationship.

### Object structure

| Object | Record Name | Type / Purpose |
|---|---|---|
| Event | Event Name | Text |
| Client | Client Name | Text |
| Vendor | Vendor Name | Text |
| Venue | Venue Name | Text |
| Feedback | Feedback | Auto Number: `F-{0000}`, starting at 0 |
| Event Vendor | EventVendor__c | Junction object for Event–Vendor |

### Create the objects

Go to **Setup → Object Manager → Create → Custom Object**.

For the custom objects, enable the required reporting and search options. Add Notes and Attachments to the default page layout. For the Event object, also enable Activities and Field History Tracking, and keep the object deployed.

### Event object

1. Open **Setup → Object Manager**.
2. Select **Create → Custom Object**.
3. Set the label to `Event` and plural label to `Events`.
4. Use `Event Name` as the record name.
5. Choose **Text** as the record-name data type.
6. Enable **Allow Reports**, **Allow Activities**, **Track Field History**, and **Allow Search**.
7. Enable **Add Notes and Attachments related list**.
8. Keep Deployment Status as **Deployed**.
9. Save the object.

### Client object

Create another custom object with:

- Label: `Client`
- Plural Label: `Clients`
- Record Name: `Client Name`
- Record Name Type: `Text`
- Allow Reports: Enabled
- Allow Search: Enabled
- Notes and Attachments related list: Enabled

### Vendor object

Create the object using:

- Label: `Vendor`
- Plural Label: `Vendors`
- Record Name: `Vendor Name`
- Record Name Type: `Text`
- Allow Reports: Enabled
- Allow Search: Enabled
- Notes and Attachments related list: Enabled

### Venue object

Create the object using:

- Label: `Venue`
- Plural Label: `Venues`
- Record Name: `Venue Name`
- Record Name Type: `Text`
- Allow Reports: Enabled
- Allow Search: Enabled
- Notes and Attachments related list: Enabled

### Feedback object

Create the object using:

- Label: `Feedback`
- Plural Label: `Feedbacks`
- Record Name: `Feedback`
- Data Type: `Auto Number`
- Display Format: `F-{0000}`
- Starting Number: `0`
- Allow Reports: Enabled
- Allow Search: Enabled
- Notes and Attachments related list: Enabled

---

## 3. Custom Object Tabs — Milestone 3

Create navigation tabs for the five main objects.

1. Open **Setup**.
2. Search for **Tabs** in Quick Find.
3. Open **Custom Object Tabs** and select **New**.
4. Create tabs for:
   - Event
   - Client
   - Vendor
   - Venue
   - Feedback
5. Select any suitable tab style.
6. Continue with **Next → Next → Save**.

---

## 4. Fields and Relationships — Milestone 4

### Event fields

| Field | Data Type | Values / Configuration |
|---|---|---|
| Event Type | Picklist | Wedding, Corporate, Birthday, Anniversary, Festival, Concert, Other |
| Event Date | Date | — |
| Event Status | Picklist | Planned, Confirmed, Completed, Pending Cancellation, Canceled, Rejected |
| Event Budget | Formula (Currency) | Calculated from Event Type |

### Client fields

| Field | Data Type | Values / Configuration |
|---|---|---|
| Email | Email | — |
| Phone | Phone | — |
| Address | Text Area | — |
| Country | Picklist | India, Philippines, Saudi Arabia, USA, Switzerland, Thailand, Maldives, Dubai, Sri Lanka, Bangkok, Singapore, Myanmar, France, Nepal |
| City | Picklist | Hyderabad, Mumbai, Delhi, Bangalore, Chennai, Alaminos, Angeles City, Palayan, Quezon City, San Juan, Riyadh, Jeddah, Tayma, Dammam, Medina, New York, Los Angeles, Chicago, Houston, Columbus, Geneva, Basel, Lausanne, Bern, Lucerne |

### Vendor fields

| Field | Data Type | Values / Configuration |
|---|---|---|
| Service Type | Multi-Select Picklist | Catering, Decor, Photography, Videography, Lighting, Stage Setup, Makeup Artist, DJ/Music, Transportation, Hosting/Anchor |
| Email | Email | — |
| Phone | Phone | — |
| Status | Picklist | Available, Booked, Cancelled |

### Venue fields

| Field | Data Type | Values / Configuration |
|---|---|---|
| Address | Text Area | — |
| Location | URL | — |
| Capacity | Number | — |
| Availability Status | Picklist | Available, Reserved, Not Available |

### Feedback fields

| Field | Data Type | Values / Configuration |
|---|---|---|
| Rating | Picklist | 1, 2, 3, 4, 5 |
| Comments | Long Text Area | — |

### Event Budget formula

Create **Event Budget** as a Formula field with a Currency return type. Use the following business-value mapping:

```text
CASE(
  TEXT(Event_Type__c),
  "Wedding", 50000,
  "Corporate", 30000,
  "Birthday", 10000,
  "Anniversary", 20000,
  "Festival", 60000,
  "Concert", 40000,
  "Other", 15000,
  0
)
```

Check the formula syntax before saving.

---

### Create the object relationships

The Event object acts as the main business record. It connects to clients and venues through Lookup relationships. Feedback connects to both Event and Client. Event Vendor acts as the junction between Event and Vendor.

| Relationship | Salesforce Relationship Type |
|---|---|
| Event → Client | Lookup |
| Event → Venue | Lookup |
| Feedback → Event | Lookup |
| Feedback → Client | Lookup |
| Event Vendor → Event | Master-Detail |
| Event Vendor → Vendor | Master-Detail |

### Event → Venue lookup

1. Go to **Setup → Object Manager → Event**.
2. Open **Fields & Relationships → New**.
3. Select **Lookup Relationship**.
4. Select **Venue** as the related object.
5. Set the field label to `Venue`.
6. Complete the remaining steps and save.

### Event → Client lookup

Repeat the lookup-field process on Event, but select **Client** as the related object and use `Client` as the field label.

### Event Vendor junction object

1. Create a custom object called **Event Vendor**.
2. Use the API name `EventVendor__c`.
3. Open its **Fields & Relationships** section.
4. Create a **Master-Detail Relationship** to Event and label the field `Event`.
5. Create another **Master-Detail Relationship** to Vendor and label the field `Vendor`.

This provides the Event ↔ Vendor many-to-many model.

### Feedback → Event lookup

On **Feedback → Fields & Relationships**, create a Lookup Relationship to **Event** and name the field `Event`.

### Feedback → Client lookup

On **Feedback → Fields & Relationships**, create a Lookup Relationship to **Client** and name the field `Client`.

---

### Feedback lookup filter

The Feedback Event lookup must be restricted so that, after a client is selected, only that client's events are available.

1. Open **Setup → Object Manager → Feedback**.
2. Select **Fields & Relationships**.
3. Open the `Event` lookup field and choose **Edit**.
4. Locate **Lookup Filter** and select **Show Filter Settings**.
5. Configure the comparison as:
   - Left field: `Event: Client`
   - Operator: `equals`
   - Right value: the **Field** option → `Feedback: Client`
6. Enable the filter.
7. Save the field.

---

## 5. Client Email Validation — Milestone 5

Create a validation rule on the Client object to stop malformed email addresses from being saved.

1. Go to **Setup → Object Manager → Client → Validation Rules**.
2. Select **New**.
3. Rule Name: `Email_Valid_Address`
4. Set the rule to **Active**.
5. Use this condition:

```text
NOT(REGEX(Email__c, "^[a-zA-Z0-9._]+@[a-zA-Z0-9.]+\\.[a-zA-Z]{2,}$"))
```

6. Error Message: `Please Enter Valid Email Address`
7. Error Location: **Field → Email**
8. Save the rule.

---

# 6. Security Configuration

Security is implemented through Profiles, Roles, Users, Permission Sets, Organization-Wide Defaults, and Sharing Rules.

## 6.1 Profiles — Milestone 14

A profile determines the baseline permissions available to a Salesforce user. The EventForce project uses four custom profiles.

| Profile | Base Profile | Required Object Access |
|---|---|---|
| Event Admin | System Administrator | Full access |
| Event Coordinator | Standard Platform User | Event: C/R/E/D; Client: R/E; Vendor: R; Venue: R; Feedback: C/R |
| Vendor Manager | Standard Platform User | Vendor: C/R/E/D; Event: R; Venue: R; Client: R; Feedback: R |
| Client Profile | Standard Platform User | Event: R; Feedback: C/R; Vendor: No Access; Venue: No Access |

**Legend:** C = Create, R = Read, E = Edit, D = Delete.

For Event Coordinator, Vendor Manager, and Client Profile:

- Set **Event Planner** as the default custom app.
- Session timeout: **2 hours of inactivity**.
- Password expiration: **Never expires**.
- Minimum password length: **8 characters**.

### Event Admin profile

1. Open **Setup → Profiles**.
2. Select **New Profile**.
3. Use **System Administrator** as the existing profile.
4. Name the new profile `Event Admin`.
5. Save.

The System Administrator base provides the broad access required by the Event Admin role.

### Event Coordinator profile

1. Create a new profile using **Standard Platform User** as the base.
2. Name it `Event Coordinator`.
3. Edit its permissions.
4. Set Event Planner as the default custom app.
5. Configure the object permissions listed in the table above.
6. Apply the common password and session settings.
7. Save.

### Vendor Manager profile

Create a profile from **Standard Platform User**, name it `Vendor Manager`, set Event Planner as the default app, apply the permissions from the table, configure the common security settings, and save.

### Client Profile

Create a profile from **Standard Platform User**, name it `Client Profile`, set Event Planner as the default app, provide read access to Event, create/read access to Feedback, and no access to Vendor and Venue. Apply the common password and session settings, then save.

---

## 6.2 Roles — Milestone 15

Roles establish the reporting hierarchy used by EventForce.

Create the hierarchy below:

```text
CEO
└── Event Admin
    ├── Event Coordinator
    ├── Vendor Manager
    └── Client
```

### Setup

1. In Setup, search for **Roles**.
2. Open **Set Up Roles**.
3. Expand the hierarchy.
4. Add `Event Admin` under CEO.
5. Add `Event Coordinator`, `Vendor Manager`, and `Client` under Event Admin.
6. Save each role.

---

## 6.3 Users — Milestone 16

A Salesforce user account identifies a person who can sign in and determines the permissions and records available to that person.

| User Type | Role | License | Profile |
|---|---|---|---|
| Admin user | Event Admin | Salesforce | Event Admin |
| Coordinator user | Event Coordinator | Salesforce Platform | Event Coordinator |
| Vendor manager user | Vendor Manager | Salesforce Platform | Vendor Manager |
| Client user | Client | Salesforce Platform | Client Profile |

### Create the Event Admin user

1. Open **Setup → Users → Users**.
2. Click **New User**.
3. Enter the user's first name and last name.
4. Provide an alias.
5. Enter the user's email address.
6. Create a unique username in the `text@text.text` format.
7. Enter a nickname.
8. Role: `Event Admin`.
9. User License: `Salesforce`.
10. Profile: `Event Admin`.
11. Save.

### Create the remaining users

Repeat the same procedure for:

- Event Coordinator → Salesforce Platform → Event Coordinator profile
- Vendor Manager → Salesforce Platform → Vendor Manager profile
- Client → Salesforce Platform → Client Profile

> **License note:** Developer Edition organizations have a limited number of Salesforce licenses. If the Salesforce license is unavailable, check the existing users and deactivate an unnecessary Salesforce-license user or use the permitted alternative license arrangement.

---

## 6.4 Permission Set — Milestone 17

Create a permission set named **Feedback Manager** to provide additional Feedback permissions without modifying the user's profile.

1. Open **Setup → Permission Sets**.
2. Click **New**.
3. Label: `Feedback Manager`.
4. Save.
5. Open **Object Settings → Feedback → Edit**.
6. Enable **Read, Create, Edit, Delete**.
7. Save.
8. Open **Manage Assignments**.
9. Click **Add Assignment**.
10. Select the Event Coordinator user.
11. Continue and assign the permission set.

---

## 6.5 Sharing and Organization-Wide Defaults — Milestone 18

### Event OWD

1. Go to **Setup → Sharing Settings**.
2. Click **Edit**.
3. Find the Event object under Organization-Wide Defaults.
4. Set **Default Internal Access = Private**.
5. Set **Default External Access = Private**.
6. Save.

Private access establishes the baseline restriction. Additional access can then be opened through the configured sharing mechanisms.

### Event sharing rule

Create the following rule:

| Setting | Value |
|---|---|
| Rule Label | `Event_Sharing_For_Vendors` |
| Records shared from | Role: Event Coordinator |
| Shared with | Role: Vendor Manager |
| Access | Read Only |

Steps:

1. Remain in **Sharing Settings**.
2. Locate Event sharing rules.
3. Select **New**.
4. Enter the rule label.
5. Choose Event Coordinator as the source role.
6. Choose Vendor Manager as the receiving role.
7. Set access to **Read Only**.
8. Save.

---

# 7. Event Cancellation Approval — Milestone 6

> Complete Profiles, Roles, and Users before configuring this process.

## 7.1 Email templates

Create the following plain-text Classic Email Templates.

| Template | Subject |
|---|---|
| Event Cancellation Request Notification | `Approval Request: Cancel Event {!Event__c.Name}` |
| Email to Client After Cancellation | `Event Cancellation Notice – {!Event__c.Name}` |
| Approval Notification to Event Owner | `Event Cancellation Approved: {!Event__c.Name}` |
| Rejection Notification to Event Owner | `Event Cancellation Rejected: {!Event__c.Name}` |

A basic cancellation request message can contain:

```text
Hello!

An approval request has been submitted to cancel the event:
- Event Name: {!Event__c.Name}
- Event Date: {!Event__c.Event_Date__c}

Thank you,
EventForce Management System
```

Use the project-specific subjects and merge fields shown above in the other templates as required.

## 7.2 Build the approval process

Open **Setup → Approval Processes → Event → Create New Approval Process → Use Standard Setup Wizard**.

1. Process Name: `Event_Cancellation_Process`.
2. Entry condition: Event Status **equals** `Pending Cancellation`.
3. Configure the approver using either the specified user or the submitter's manager, according to the project configuration.
4. Select **Event Cancellation Request Notification** as the approval email template.
5. Display the following fields to the approver:
   - Event Name
   - Owner
   - Client
   - Event Budget
   - Event Date
   - Event Status
   - Event Type
   - Venue
6. Set **Event Owner** as an initial submitter.
7. Add an initial Email Alert named `Alert - Event Cancellation Request` and send it to the Event Coordinator role.
8. Configure final approval:
   - Update Event Status to `Canceled`.
   - Send `Alert - Cancellation Approved` to the Event Owner.
9. Configure final rejection:
   - Send `Alert - Cancellation Rejected` to the Event Owner.
   - Update Event Status to `Rejected`.
10. Add an approval step named **Manager Review**.
11. Configure the step for all records and assign the required approver.
12. Open the approval process detail page and select **Activate**.

---

# 8. Three-Day Client Reminder Flow — Milestone 7

### Intended behavior

When an Event is created or updated with status **Confirmed**, Salesforce schedules an email to the related client three days before the Event Date.

### Build the flow

1. Open **Setup → Flows → New Flow**.
2. Choose **Record-Triggered Flow**.
3. Select **Event** as the object.
4. Trigger when a record is **created or updated**.
5. Set the condition:
   - Field: Event Status
   - Operator: Equals
   - Value: `Confirmed`
6. Optimize the flow for **Actions and Related Records**.
7. From Start, choose **Add Schedule Path**.
8. Path Name: `3_Days_Before`.
9. Time Source: `Event__c: Event Date`.
10. Offset: `3` days before.

### Email resources

Create a Plain Text Text Template named `Subject`:

```text
Reminder: Your Event {!$Record.Name} is in 3 days
```

Create another Plain Text Text Template named `Body`:

```text
Hello {!$Record.Client__r.Name},

This is a reminder that your event "{!$Record.Name}" will take place on {!$Record.Event_Date__c}.

Venue: {!$Record.Venue__r.Name}
Type: {!$Record.Event_Type__c}

We look forward to seeing you!

- EventForce Team
```

### Send the email

1. Under the `3_Days_Before` schedule path, add an **Action**.
2. Select **Send Email**.
3. Label the action `Alert_Client_3Day_Reminder`.
4. Use the Client Email as the recipient.
5. Add Event Owner as CC.
6. Select the `Subject` resource for the subject.
7. Select the `Body` resource for the body.
8. Save the flow with the label `Client Reminder - 3 Days Before`.
9. Activate the flow.

---

# 9. Apex Development — Milestones 8–10

Open **Developer Console** from the Salesforce gear menu when creating the Apex components.

| Milestone | Apex Component | Business Function |
|---|---|---|
| 8 | `VenueStatusHelper` + `EventTrigger13` | Updates venue availability when event status changes |
| 9 | `PreventDoubleBooking` | Stops conflicting events at the same venue and date |
| 10 | `BatchCompleteEvents` + `ScheduleCompleteEvents` | Marks past events as Completed through scheduled asynchronous processing |

## 9.1 Venue status helper — Milestone 8

### Apex class

Create a class named `VenueStatusHelper`.

```apex
public with sharing class VenueStatusHelper {
    public static void updateVenueStatus(List<Event__c> events){
        List<Venue__c> venues = new List<Venue__c>();
        for(Event__c ev : events){
            if(ev.Venue__c != null){
                if(ev.Event_Status__c == 'Confirmed'){
                    venues.add(new Venue__c(Id=ev.Venue__c, Availability_Status__c='Reserved'));
                } else if(ev.Event_Status__c == 'Canceled'){
                    venues.add(new Venue__c(Id=ev.Venue__c, Availability_Status__c='Available'));
                }
            }
        }
        if(!venues.isEmpty()) update venues;
    }
}
```

Save the class.

### Trigger

Create an Apex Trigger named `EventTrigger13` for `Event__c`.

```apex
trigger EventTrigger13 on Event__c (after insert, after update) {
    VenueStatusHelper.updateVenueStatus(Trigger.new);
}
```

Save the trigger.

## 9.2 Prevent double booking — Milestone 9

Create the `PreventDoubleBooking` trigger on `Event__c` using the following project logic:

```apex
trigger PreventDoubleBooking on Event__c (before insert, before update) {
    Set<Id> venueIds = new Set<Id>();
    for(Event__c ev : Trigger.new){
        if(ev.Venue__c != null) venueIds.add(ev.Venue__c);
    }

    Map<Id, List<Event__c>> venueEventMap = new Map<Id, List<Event__c>>();
    for(Event__c e : [SELECT Id, Venue__c, Event_Date__c
                      FROM Event__c WHERE Venue__c IN :venueIds]){
        if(!venueEventMap.containsKey(e.Venue__c)){
            venueEventMap.put(e.Venue__c, new List<Event__c>());
        }
        venueEventMap.get(e.Venue__c).add(e);
    }

    for(Event__c ev : Trigger.new){
        if(ev.Venue__c != null && venueEventMap.containsKey(ev.Venue__c)){
            for(Event__c existing : venueEventMap.get(ev.Venue__c)){
                if(existing.Event_Date__c == ev.Event_Date__c && existing.Id != ev.Id){
                    ev.addError('This Venue is already booked on this date.');
                }
            }
        }
    }
}
```

Save the trigger.

## 9.3 Batch and scheduled Apex — Milestone 10

The asynchronous process changes past events to **Completed**.

### Batch class

Create `BatchCompleteEvents`:

```apex
global class BatchCompleteEvents implements Database.Batchable<sObject> {
    global Database.QueryLocator start(Database.BatchableContext bc){
        return Database.getQueryLocator([
            SELECT Id, Event_Date__c, Event_Status__c
            FROM Event__c
            WHERE Event_Date__c < TODAY
            AND Event_Status__c != 'Completed'
        ]);
    }

    global void execute(Database.BatchableContext bc, List<Event__c> scope){
        for(Event__c ev : scope){
            ev.Event_Status__c = 'Completed';
        }
        update scope;
    }

    global void finish(Database.BatchableContext bc){
        System.debug('Past events marked as Completed');
    }
}
```

### Schedulable class

Create `ScheduleCompleteEvents`:

```apex
global class ScheduleCompleteEvents implements Schedulable {
    global void execute(SchedulableContext sc){
        Database.executeBatch(new BatchCompleteEvents(), 200);
    }
}
```

### Schedule the job

1. Open **Setup**.
2. Search for **Apex Classes**.
3. Click **Schedule Apex**.
4. Job Name: `Daily Event Completion`.
5. Apex Class: `ScheduleCompleteEvents`.
6. Frequency: **Weekly**.
7. Select **all days**.
8. Preferred Start Time: **8:00 PM**.
9. Save.

---

# 10. Event Planner Lightning App — Milestone 11

Create the application's main Lightning workspace.

1. Open **Setup → App Manager**.
2. Click **New Lightning App**.
3. App Name: `Event Planner`.
4. Upload the application image if required.
5. Keep the default App Options.
6. Keep the default Utility Items.
7. Move the following items into the navigation menu:
   - Event
   - Client
   - Vendor
   - Venue
   - Feedback
   - Reports
   - Dashboards
8. Assign the app to the **System Administrator** profile.
9. Select **Save & Finish**.
10. Open the **App Launcher**, search for `Event Planner`, and launch it to verify the setup.

---

# 11. Reports — Milestone 12

## Create the report folder

1. From the App Launcher, open **Reports**.
2. Select **New Folder**.
3. Folder Label: `EventForce Report`.
4. Save.

### Share the report folder

1. Open the Reports tab inside the Event Planner app.
2. Find `EventForce Report` in the folder list.
3. Use the folder dropdown and select **Share**.
4. Share with the roles:
   - Event Coordinator
   - Vendor Manager
5. Give those roles **View** access.
6. Save the sharing configuration.

> **Data preparation:** Create at least 10 records in each object before building the report. Completing as many fields as possible makes the report more representative.

## Upcoming Events by Month

Purpose: provide a month-wise view of future events and their budgets.

1. Open **App Launcher → Reports → New Report**.
2. Search for **Events** or **Events with Client & Venue**.
3. Start the report.
4. Include these columns:
   - Event Name
   - Event Date
   - Event Type
   - Client Name
   - Venue Name
   - Event Budget
5. Remove fields that are not required.
6. Enable automatic preview updates.
7. Apply filters:
   - Show Me = All Events
   - Event Date = Today or later (or the project-approved future range)
   - Event Status ≠ `Canceled`
8. Group Event Date by **Calendar Month**.
9. Add summaries for:
   - Row Count
   - SUM of Event Budget
10. Add a **Column** or **Line** chart.
11. Use Calendar Month for the X-axis.
12. Use Row Count or total Event Budget for the Y-axis.
13. Save the report as `Upcoming Events by Month` in the EventForce Report folder.
14. Run the report.

---

# 12. EventForce Operations Dashboard — Milestone 13

1. Open the **Dashboards** tab from the Event Planner application.
2. Click **New Dashboard**.
3. Name it `EventForce Operations Dashboard`.
4. Create the dashboard.
5. Add a new widget.
6. Select the **Upcoming Events by Month** report.
7. Configure the widget as:
   - Chart Type: **Donut Chart**
   - Maximum Values Displayed: **6**
   - Subtitle: `Upcoming Events Overview`
   - Widget Theme: **Light**
8. Save the widget.

---

# 13. Data Migration, Testing, and Security

The implementation phase includes loading validated sample data, configuring user access, and checking the behavior of the CRM before considering the project complete.

### Data loading

Records for the following objects can be prepared and imported through Salesforce's Data Import Wizard:

- Events
- Clients
- Vendors
- Venues
- Feedback

Validate the data before importing it so that duplicate or inconsistent records are avoided.

### Security verification

Confirm that:

- Each user has the correct Profile.
- Each user has the correct Role.
- The Feedback Manager permission set is assigned to the Event Coordinator user.
- Event OWD is Private.
- The Event sharing rule provides Vendor Manager with Read Only access to records shared from Event Coordinator.

---

# 14. End-to-End Testing Checklist

Use the following checks after configuration:

- [ ] Create records for all required objects and verify that their fields save correctly.
- [ ] Enter an invalid Client email and confirm that the validation rule blocks the record.
- [ ] Try creating two Events for the same Venue and Event Date and verify the double-booking error.
- [ ] Change an Event to `Confirmed` and verify that the related Venue becomes `Reserved`.
- [ ] Change an Event to `Pending Cancellation` and verify that the approval process starts and the configured notification is generated.
- [ ] Test both approval and rejection paths and verify the resulting Event Status values.
- [ ] Test the three-day reminder using a suitable test Event Date and confirmed status.
- [ ] Log in or test access using each configured user and verify the permissions provided by the corresponding Profile and Permission Set.
- [ ] Run `Upcoming Events by Month` and confirm that the Dashboard receives the report data.
- [ ] Confirm that the scheduled Apex job is configured for the intended time.

---

# 15. Deployment and Maintenance

## Deployment approach

The EventForce project is implemented and tested in a Salesforce Developer Edition environment. A production migration is outside the scope of this academic implementation.

For a real deployment, the configured metadata could be moved through appropriate Salesforce development and release mechanisms such as:

- Sandbox-based deployment
- Change Sets
- Salesforce DX
- GitHub-based CI/CD workflows

Before migration, verify that objects, fields, relationships, flows, approval processes, Apex components, security settings, reports, and dashboards are correctly versioned and tested.

## Maintenance and monitoring

Ongoing administration should include monitoring:

- Record-triggered flows
- Scheduled and batch Apex
- Approval processes
- User permissions
- Validation rules
- Sharing rules
- Reports and dashboards

Typical troubleshooting work includes investigating access errors, permission conflicts, failed automation, Apex trigger behavior, and changes to validation or sharing requirements.

---

# 16. EventForce Project Structure at a Glance

```text
EventForce Management System
│
├── Data Model
│   ├── Event
│   ├── Client
│   ├── Vendor
│   ├── Venue
│   ├── Feedback
│   └── Event Vendor (Junction)
│
├── Relationships
│   ├── Event → Client (Lookup)
│   ├── Event → Venue (Lookup)
│   ├── Feedback → Event (Lookup)
│   ├── Feedback → Client (Lookup)
│   └── Event Vendor → Event/Vendor (Master-Detail)
│
├── Validation
│   └── Client Email Validation
│
├── Automation
│   ├── 3-Day Client Reminder Flow
│   └── Event Cancellation Approval Process
│
├── Apex
│   ├── VenueStatusHelper
│   ├── EventTrigger13
│   ├── PreventDoubleBooking
│   ├── BatchCompleteEvents
│   └── ScheduleCompleteEvents
│
├── Security
│   ├── Profiles
│   ├── Roles
│   ├── Permission Set
│   ├── OWD
│   └── Sharing Rule
│
└── Analytics
    ├── Upcoming Events by Month
    └── EventForce Operations Dashboard
```

---

# 17. Project Implementation Summary

EventForce is a Salesforce CRM solution designed to bring event-related information into one connected system. The central Event record is linked to Clients and Venues, while Vendors are connected through the Event Vendor junction object. Feedback is associated with both the Event and Client.

The implementation combines declarative Salesforce configuration with programmatic automation. Validation protects Client email data, the scheduled reminder flow communicates with clients before confirmed events, and the cancellation approval process provides controlled status changes. Apex extends the system with venue-status synchronization, double-booking prevention, and scheduled completion of past events.

Profiles, Roles, Permission Sets, Organization-Wide Defaults, and Sharing Rules establish controlled access. Reports and dashboards provide an operational view of upcoming events and their budgets.

The resulting setup follows the core EventForce implementation while presenting the instructions in independently written documentation suitable for a GitHub repository.

---

## Core Configuration Reference

| Area | EventForce Configuration |
|---|---|
| Main App | Event Planner |
| Main Business Object | Event |
| Junction Object | EventVendor__c |
| Validation Rule | `Email_Valid_Address` |
| Approval Process | `Event_Cancellation_Process` |
| Reminder Flow | `Client Reminder - 3 Days Before` |
| Venue Helper | `VenueStatusHelper` |
| Venue Trigger | `EventTrigger13` |
| Double Booking Trigger | `PreventDoubleBooking` |
| Batch Apex | `BatchCompleteEvents` |
| Scheduled Apex | `ScheduleCompleteEvents` |
| Permission Set | `Feedback Manager` |
| Event Sharing Rule | `Event_Sharing_For_Vendors` |
| Report | `Upcoming Events by Month` |
| Dashboard | `EventForce Operations Dashboard` |
