# uniflow.ai
# UniFlow AI

> A simple AI-powered institutional service assistant that understands student requests, retrieves verified institutional information, creates service requests, asks for human approval before consequential actions, and maintains an auditable action trail.

---

## 1. Project Goal

Build a **simple, production-looking MVP** available as both:

* Web application
* Mobile application

The application should demonstrate a secure **agentic AI workflow** without being unnecessarily complicated.

The AI should not simply answer questions.

It should:

```text
Understand Request
        ↓
Retrieve Verified Information
        ↓
Plan Action
        ↓
Check Permissions / Policy
        ↓
Ask Human Approval
        ↓
Execute Action
        ↓
Track Status
        ↓
Create Audit Trail
```

The application must prioritize **simplicity, reliability, and a complete working end-to-end flow** over having many features.

---

# 2. Application Name

## UniFlow AI

If this name conflicts with an existing product, use:

**[YOUR_APP_NAME]**

---

# 3. Target Institution

Build the MVP for:

**Institution Name:** [YOUR_INSTITUTION_NAME]

**Institution Type:** [COLLEGE / UNIVERSITY / SCHOOL / OTHER]

**Location:** [CITY, COUNTRY]

**Institution Email Domain:** [EMAIL_DOMAIN]

---

# 4. User Roles

Only create three roles for the MVP:

### Student

Can:

* Log in
* Chat with the AI
* Submit service requests
* View their requests
* Approve their own consequential actions
* Track request status
* View their own audit history

### Staff

Can:

* View assigned requests
* Update request status
* Add comments
* Resolve requests

### Administrator

Can:

* View all requests
* Approve administrative actions
* Escalate requests
* View complete audit logs
* Manage basic institutional policies

Do not create additional roles unless absolutely necessary.

---

# 5. Authentication & Authorization

Use **Firebase Authentication**.

Authentication methods:

* Email/password
* Google Sign-In

Use Firebase Authentication for user identity.

Use Firebase Firestore for application data.

Use Firebase Security Rules for authorization.

Never trust the frontend to determine whether a user is allowed to perform an action.

Every sensitive operation must be checked on the backend/server before execution.

Use role-based access:

```text
student
staff
admin
```

Store the user's role securely.

Example:

```text
users/{userId}

name
email
role
department
studentId
roomNumber
createdAt
```

Firebase currently provides a no-cost Spark plan with usage limits, making it appropriate for a hackathon MVP.

---

# 6. AI Model

Use:

## Primary LLM

**Google Gemini 2.5 Flash-Lite**

Use it for:

* Intent detection
* Request classification
* Information extraction
* Simple planning
* Tool selection
* Response generation
* Uncertainty detection

Google currently lists Gemini 2.5 Flash-Lite with a free tier, making it the preferred choice for this MVP.

## Fallback

If Flash-Lite does not perform well enough for a particular task, use:

**Gemini 2.5 Flash**

It also has a free tier.

Do not use multiple LLM providers.

Do not build a multi-agent architecture.

Use **one AI agent with tools**.

---

# 7. Core AI Agent

Create one central agent called:

```text
UniFlow Agent
```

The agent should have access only to explicitly defined tools.

The AI must never directly access Firestore or execute arbitrary database queries.

The architecture should be:

```text
User
 ↓
UniFlow Agent
 ↓
Tool
 ↓
Permission Check
 ↓
Database / Workflow
```

---

# 8. AI Tools

Initially implement only these tools:

```text
getUserProfile()

searchInstitutionalPolicy()

getRequestStatus()

createMaintenanceRequest()

createCertificateRequest()

createGrievance()

escalateRequest()
```

Do not add unnecessary tools.

Every tool must validate:

* User identity
* User role
* Required parameters
* Permission
* Request state

---

# 9. MVP Workflows

Implement exactly **three workflows**.

## Workflow 1 — Certificate Request

Example:

> "I need a bonafide certificate."

AI should:

```text
Understand request
        ↓
Identify certificate type
        ↓
Check institutional policy
        ↓
Check required information
        ↓
Ask missing questions
        ↓
Prepare certificate request
        ↓
Ask user for approval
        ↓
Create request
        ↓
Show request ID
```

Example approval:

```text
ACTION REQUIRES APPROVAL

Certificate: Bonafide Certificate
Student: [STUDENT_NAME]
Purpose: Scholarship

Do you want to submit this request?

[Approve] [Cancel]
```

---

# 10. Workflow 2 — Maintenance Request

Example:

> "The AC in my hostel room is not working."

AI extracts:

```text
Category: Maintenance
Issue: AC not working
Location: Hostel B-204
Priority: Medium
```

If information is missing, ask for it.

Then:

```text
Check policy
 ↓
Prepare ticket
 ↓
Ask approval
 ↓
Create ticket
 ↓
Show ticket ID
```

Example:

```text
ACTION REQUIRES APPROVAL

Maintenance Request

Issue: AC not working
Location: Hostel B-204
Priority: Medium

[Approve] [Cancel]
```

---

# 11. Workflow 3 — Grievance Escalation

Example:

> "My scholarship complaint has not been solved for five days."

AI should:

```text
Find existing complaint
        ↓
Check complaint status
        ↓
Check institutional resolution policy
        ↓
Determine whether escalation is allowed
        ↓
Explain result
        ↓
Ask for approval
        ↓
Escalate complaint
```

Example:

```text
Your complaint has been open for 5 days.

The institutional policy allows escalation
after 3 working days.

Would you like to escalate this complaint
to the Finance Department?

[Escalate] [Cancel]
```

---

# 12. Human Approval

This is a mandatory feature.

The AI must not automatically perform consequential actions.

Examples:

```text
Create certificate request
Create maintenance request
Create grievance
Escalate grievance
```

must require approval.

The AI should pause the workflow until approval is received.

Approval screen:

```text
┌──────────────────────────────┐
│ ACTION REQUIRES APPROVAL     │
│                              │
│ Action: Create Request       │
│                              │
│ Details:                     │
│ AC not working               │
│ Hostel B-204                 │
│                              │
│ [Cancel]       [Approve]     │
└──────────────────────────────┘
```

Record:

```text
approvedBy
approvedAt
action
requestId
```

---

# 13. Institutional Knowledge Base

Create a very small predefined knowledge base.

Do not build a complicated document management system.

Initially include:

```text
maintenance-policy.md
certificate-policy.md
grievance-policy.md
institution-contact.md
```

Example:

```text
Maintenance Policy

Normal maintenance requests should normally
be addressed within 48 hours.

Requests unresolved beyond the specified
resolution period may be escalated.
```

The AI must retrieve information from these documents before making policy-related claims.

Every policy response should show:

```text
Source:
Maintenance Policy
Last Updated:
[DATE]
```

The AI must never invent institutional policies.

---

# 14. Uncertainty Handling

This is mandatory.

If the AI does not have enough information:

```text
I don't have enough verified information
to determine this.
```

If two policies conflict:

```text
I found conflicting institutional information.
I cannot safely determine which policy applies.

Please contact an administrator.
```

Never fabricate:

* Fees
* Deadlines
* Eligibility
* Department names
* Approval requirements
* Request status

---

# 15. Multilingual Support

Support:

```text
English
Hindi
```

The user should be able to write:

> "Mere hostel ka AC kharab hai."

The AI should understand the request and create the same internal workflow.

Allow the user to choose:

```text
English
हिन्दी
```

The internal database should continue using standardized English field values.

---

# 16. Audit Trail

Every important AI action must be logged.

Create:

```text
auditLogs/{logId}
```

Store:

```text
userId
requestId
action
description
timestamp
actor
result
source
```

Example:

```text
23:41:02
User request received

23:41:03
Intent identified: Maintenance

23:41:04
Policy retrieved:
Maintenance Policy

23:41:06
Action proposed:
Create maintenance ticket

23:41:20
User approval received

23:41:21
Maintenance ticket created
```

Create a simple audit screen.

---

# 17. Request Database

Use Firebase Firestore.

Create these basic collections:

```text
users
requests
policies
auditLogs
notifications
```

Request structure:

```text
requests/{requestId}

id
userId
type
title
description
status
priority
department
createdAt
updatedAt
approvedAt
resolvedAt
```

Request statuses:

```text
pending
awaiting_approval
approved
in_progress
resolved
rejected
escalated
```

---

# 18. Web Application

Build using:

```text
Next.js
TypeScript
Tailwind CSS
Firebase
```

Pages:

```text
/login
/dashboard
/chat
/requests
/requests/[id]
/approvals
/audit
/admin
```

Do not create unnecessary pages.

---

# 19. Mobile Application

Build using:

```text
React Native
Expo
TypeScript
Firebase
```

Keep the mobile app functionally equivalent to the web app.

Main screens:

```text
Login
Home
AI Chat
Requests
Request Details
Approval
Profile
```

Do not build separate backend logic for mobile.

Both applications must use the same Firebase backend and API.

---

# 20. Main Dashboard

Student dashboard:

```text
Welcome, [NAME]

Active Requests       2
Awaiting Approval     1
Resolved              5

[Ask UniFlow AI]

Recent Requests
--------------------------
#REQ-1024  AC not working
In Progress

#REQ-1023  Bonafide
Pending
```

Keep the dashboard simple.

---

# 21. Admin Dashboard

Show:

```text
Total Requests
Pending
In Progress
Resolved
Escalated
```

Admin should be able to:

* View requests
* Filter requests
* Change status
* Assign department
* Escalate
* View audit trail

Do not build complex analytics.

---

# 22. External Services

Use as few external services as possible.

### Required

**Firebase**

Use for:

* Authentication
* Firestore
* Security Rules

**Google Gemini API**

Use for:

* AI agent
* Intent detection
* Planning
* Tool calling
* Response generation

### Optional

**Cloudinary**

Only use Cloudinary if the MVP needs users to upload images, such as a photo of a broken AC, damaged equipment, etc.

Cloudinary currently has a free plan, so it can be added without complicating the MVP too much.

If image uploads are not necessary, **do not add Cloudinary**.

### Do NOT use

```text
Stripe
Twilio
WhatsApp API
Google Maps
AWS
Redis
Kafka
Kubernetes
Microservices
Vector databases
Multiple LLM providers
```

unless they become genuinely necessary.

---

# 23. Backend Architecture

Keep the architecture simple.

```text
Web App
     \
      \
       → API / Server
      /
Mobile App
     |
     ↓
Firebase Auth
     |
     ↓
UniFlow Agent
     |
     ├── Gemini API
     |
     ├── Policy Search
     |
     ├── Request Tools
     |
     └── Approval System
     |
     ↓
Firebase Firestore
```

The Gemini API key must never be exposed in the frontend.

Keep secrets in environment variables.

---

# 24. Environment Variables

Create:

```text
GEMINI_API_KEY=
FIREBASE_PROJECT_ID=
FIREBASE_CLIENT_EMAIL=
FIREBASE_PRIVATE_KEY=
NEXT_PUBLIC_FIREBASE_API_KEY=
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=
NEXT_PUBLIC_FIREBASE_PROJECT_ID=
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=
NEXT_PUBLIC_FIREBASE_APP_ID=
```

Clearly mark where the developer must enter their own values.

---

# 25. Important Developer Input

Do not invent these values.

Create placeholders for:

```text
[YOUR_INSTITUTION_NAME]

[YOUR_INSTITUTION_EMAIL_DOMAIN]

[YOUR_FIREBASE_PROJECT_ID]

[YOUR_GEMINI_API_KEY]

[YOUR_ADMIN_EMAIL]

[YOUR_INSTITUTION_LOCATION]

[YOUR_DEPARTMENT_NAMES]

[YOUR_POLICY_DOCUMENTS]
```

The application must work with placeholder/sample data before real institutional data is added.

---

# 26. UI Design

Use a clean modern SaaS-style interface.

Requirements:

* Responsive
* Mobile friendly
* Simple
* Fast
* Accessible
* Professional
* Minimal animations

Primary focus:

```text
Chat
Approval
Request Tracking
Audit Trail
```

Do not make the interface overly futuristic.

---

# 27. Security Rules

Implement:

### Students

Can only:

```text
Read own profile
Read own requests
Create own requests
Read own audit logs
```

### Staff

Can:

```text
Read assigned requests
Update assigned requests
```

### Admin

Can:

```text
Read all requests
Update requests
Escalate requests
Read audit logs
```

Never allow:

```text
Client → arbitrary Firestore write
Client → arbitrary tool execution
LLM → direct database access
LLM → arbitrary API execution
```

---

# 28. Error Handling

If Gemini fails:

```text
I'm temporarily unable to process this request.
Please try again.
```

If Firebase fails:

```text
We couldn't save your request.
Please try again.
```

If a tool fails:

```text
The requested action could not be completed.
No changes were made.
```

Do not show raw API errors to users.

---

# 29. Demo Data

Include sample users:

```text
Student:
Name: [DEMO_STUDENT_NAME]
Role: student

Staff:
Name: [DEMO_STAFF_NAME]
Role: staff

Admin:
Name: [DEMO_ADMIN_NAME]
Role: admin
```

Include sample requests so the dashboard does not look empty.

---

# 30. Hackathon Demo Flow

The application must support this complete demo:

### Step 1

Student logs in.

### Step 2

Student says:

> "Mere hostel room ka AC kharab hai."

### Step 3

AI understands:

```text
Issue: AC not working
Category: Maintenance
```

### Step 4

AI retrieves the maintenance policy.

### Step 5

AI asks for missing room information if required.

### Step 6

AI prepares:

```text
Create Maintenance Request
Room: B-204
Issue: AC not working
Priority: Medium
```

### Step 7

AI asks:

> Do you want me to submit this request?

Student clicks:

**Approve**

### Step 8

Request is created.

```text
REQ-1024
Status: In Progress
```

### Step 9

Admin sees the request.

### Step 10

Admin changes status.

### Step 11

Student sees the updated status.

### Step 12

Audit trail shows every important action.

---

# 31. Development Rules

This is an **MVP**.

Prioritize:

```text
Working > Fancy
Simple > Complex
Reliable > Experimental
End-to-end > Feature-heavy
```

Do not over-engineer.

Do not create microservices.

Do not create multiple AI agents.

Do not build a custom AI model.

Do not implement unnecessary external integrations.

Do not add features that are not required for the core workflow.

---

# 32. Final Definition of Done

The project is complete when a user can:

```text
Register/Login
      ↓
Ask AI for a service
      ↓
AI understands request
      ↓
AI retrieves verified policy
      ↓
AI asks missing information
      ↓
AI prepares action
      ↓
User approves
      ↓
System executes action
      ↓
Request is stored
      ↓
Admin can process it
      ↓
User sees status
      ↓
Complete audit trail exists
```

The same backend must work for both:

```text
Web Application
       +
Mobile Application
```

Build the **simplest possible implementation that satisfies this flow**.

Do not add extra features unless the core flow is fully working.
