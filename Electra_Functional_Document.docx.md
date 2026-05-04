

| ELECTRA CAR TEST DRIVE BOOKING AGENT Functional Specification Document |
| :---- |

| FUNCTIONAL SPECIFICATION DOCUMENT Electra Car Test Drive Booking AI Agent Document Type Functional Specification Project Name Electra Car Test Drive Booking Agent Platform Salesforce Automotive cloud \+ Agentforce (Service Agent) Version 1.0 Date 02/05/2026 Classification Confidential  |
| ----- |

# **1\. Executive Summary**

The Electra Car Test Drive Booking Agent is an autonomous AI agent designed using the Salesforce Agentforce platform, featuring Automotive Cloud objects and an interactive portal for displaying vehicles.  It delivers end-to-end, conversational management of test drive bookings for Electra Automotive Group, enabling customers to book, modify, cancel, explore vehicles, and escalate to human support — all through a natural language chat interface.

The agent operates through the Web, WhatsApp, integrating natively with Salesforce CRM, Apex backend services, and an Omnichannel routing engine. Its architecture uses a multi-subagent routing model, where a central Agent Router delegates user intents to specialized subagents, each owning a distinct part of the customer journey.

**Agent Name:** Electra Car Test Drive Booking Agent (22 version Active)

**Website to load web agent**: Electra cars ([https://orgfarm-6742659fff.my.site.com/electracars/](https://orgfarm-6742659fff.my.site.com/electracars/))

**Electra cars WhatsApp number**: 7780507042

When a customer visits the Electra Cars website, they can access the chat icon in the bottom right corner to start interacting with an agent. Similarly, when a customer messages the provided WhatsApp number, the agent initiates the conversation.

| Attribute | Value |
| :---- | :---- |
| Agent Developer Name | Electra\_Car\_Test\_Drive\_Booking\_Agent |
| Agent Active version | Version 22 |
| Agent Template | SvcCopilotTmpl\_\_AgentforceServiceAgent |
| Default Agent User | electra\_test\_drive\_booking\_agent@...ext |
| Platform | Salesforce Agentforce |
| Default Locale | en\_US (en\_GB additional) |
| Outbound Route Type | OmniChannelFlow |
| Outbound Route | flow://Test\_Drive\_Route\_to\_Queue |

**Key Outcome**

Customers receive a seamless, automated test drive booking experience with zero manual intervention for standard journeys, while complex cases are intelligently escalated to live agents.

# **2\. Project Overview**

## **2.1 Business Objective**

Electra Automotive Group requires a scalable, intelligent self-service solution that:

* Removes friction from the test drive booking journey.

* Reduces inbound agent workload for routine booking management tasks.

* Delivers personalized, context-aware interactions across digital channels.

* Integrates with existing Salesforce CRM infrastructure without additional manual data entry.

## **2.2 Scope**

The agent covers the following functional areas in Web & WhatsApp

| Functional Area | Description |
| :---- | :---- |
| Test Drive Booking | End-to-end booking of new test drives with duplicate detection |
| Booking Modification | Reschedule, cancel, or check status of existing bookings |
| Vehicle Change | Change the vehicle assigned to an existing test drive booking |
| Vehicle Exploration | Browse, search, compare, and get specs for vehicles and showrooms |
| Human Escalation | Route conversations to live agents via OmniChannel queue |
| Out of Scope | Payment processing, post-purchase support, warranty or service requests |

## **2.3 Target Users**

* Prospective and existing Electra customers engaging via web chat or WhatsApp.

* Electra dealership staff (as recipients of escalated conversations)

# **3\. System Architecture**

## **3.1 Multi-Subagent Design**

The agent employs hub-and-spoke architecture. The Agent Router acts as the central hub, evaluating session state variables and user intent to route each turn to the appropriate subagent. Subagents are isolated reasoning units, each with their own action libraries and step-by-step flow control.

| Component | Role |
| :---- | :---- |
| Agent Router | Classifies user intent and dispatches to the correct subagent. Enforces anti-regression guards during active flows. |
| Book\_Test\_Drive | Collects user details, checks for duplicates, gathers booking preferences, and confirms/creates the booking. |
| Modify\_Booking | Handles reschedule, cancel, and status-check flows for existing bookings. |
| Change\_Vehicle\_for\_Test\_Drive | Allows customers to swap the vehicle on an existing booking while optionally rescheduling. |
| Explore\_Vehicles | Provides vehicle catalog browsing, specification display, and showroom availability lookup. |
| Escalation | Transfers the conversation to a live human agent via OmniChannel routing. |

## **3.2 Session State Management**

All subagents share a common set of mutable session variables. These variables function as the agent's working memory, helping to maintain context throughout multiple exchanges and avoid losing track of information.

| Variable | Purpose |
| :---- | :---- |
| active\_subagent | Locks the current subagent to prevent mid-flow reclassification |
| flow\_step | Tracks the step number within a multi-step subagent flow |
| vehicleId | Stores the Salesforce ID of the user-selected vehicle |
| Booking\_Id | Stores the test drive booking reference for retrieval |
| isVerified / is\_Document\_Signed | Tracks authentication and DocuSign completion flags |
| Current\_Date | Holds today's date for relative date normalization |
| Email / PhoneNumber / firstName / lastName | User identity fields collected during the booking flow |

# **4\. Functional Flows**

## **4.1 Book a Test Drive**

This is the primary user journey. The flow consists of four distinct steps, each managed by the flow\_step variable.

### **Step 0 — Identity Collection**

* Agent requests: First Name, Last Name, Phone (10-digit), and Email

* Phone is validated for 10-digit format; re-prompt issued if invalid

* On completion, flow advances to Step 1

### **Step 1 — Duplicate Detection**

* The Check\_Duplicate\_Test\_Drive action queries existing active bookings by email and phone

* If duplicates exist agent displays existing booking details (vehicle, dealership, date, time) and presents three choices: Proceed anyway, Manage existing booking, or Cancel

* If no duplicates: flow advances automatically to Step 2

### **Step 2 — Slot Filling**

Agent collects the following in a natural, multi-turn conversation:

* Vehicle Model: User selects via Explore\_Vehicles subagent; vehicleId is stored via Get\_Showroom\_Vehicles

* Preferred Date: Must be at least one day in the future; relative dates (e.g., 'tomorrow') are normalized to YYYY-MM-DD using Get\_Today\_Date

* Time Slot: Mapped to one of three values — Morning (9-12), Afternoon (12-5), or Evening (5-7). Times outside 9AM–7PM are rejected with a re-prompt

* Preferred Channel: Email, WhatsApp, or SMS

* Zip Code: 5 or 6-digit code used to resolve nearest dealership via Assign\_Dealership\_by\_Zip\_Code

### **Step 3 — Confirmation & Booking**

* Agent presents a full summary of all collected details

* User may request inline edits to any field (vehicle, date, time, contact method, phone, email, notes) without leaving this step

* On explicit confirmation, Electra\_Create\_Test\_Drive\_Booking is invoked

* Booking confirmation details are displayed and session state is reset

## **4.2 Modify Booking**

Handles reschedule, cancellation, and status enquiries for existing bookings.

### **Identification**

* Primary: User provides Booking ID — record is fetched via Electra\_Car\_Get\_Existing\_Test\_Drive

* Fallback: User provides Email \+ Phone — system fetches and lists all matching bookings; user selects one

### **Intent Routing**

* Check Status: Agent presents current booking details and offers to cancel or reschedule

* Cancel: Agent collects cancellation reason, then evaluates:

* Scheduling conflict: Agent suggests rescheduling instead

* Location/zip code change: Agent cancels current booking and transitions to Book\_Test\_Drive with identity pre-populated

* Other reasons: Agent confirms then executes Electra\_Car\_Cancel\_Test\_Drive

* Reschedule: Agent collects new date, time, and reason; date is normalized via Get\_Today\_Date

### **Slot Availability Check**

* Is\_Booking\_Slots\_Available is invoked with the new date and time slot

* If BookedAppointmentCount \>= 3: slot is full; agent requests alternative date/time

* If BookedAppointmentCount \< 3: agent presents summary and requests confirmation

* On confirmation: Electra\_Car\_Update\_Test\_Drive is executed and results displayed

## **4.3 Change Vehicle for Test Drive**

Allows a customer to swap the vehicle on an existing booking. The flow mirrors Modify\_Booking's identification process, then:

* Fetches available showroom vehicles for the booking's zip code via Get\_Showroom\_vehicles

* User selects a new vehicle; agent confirms whether to retain existing date/time or reschedule

* If rescheduling: date and time are collected, normalized, and slot availability is verified

* On confirmation: Electra\_Car\_Update\_Test\_Drive is called with the new vehicle and (optionally) new date/time

## **4.4 Explore Vehicles**

A vehicle discovery subagent with five interaction paths:

| Path | Trigger | Action |
| :---- | :---- | :---- |
| A — Browse | Broad queries (e.g., 'show all') | Get\_Showroom\_Vehicles or Search\_Vehicle\_Catalog |
| B — Deep Detail | User selects a specific model | Display\_Vehicle\_Details; vehicleId is stored |
| C — Compare | Usernames two vehicles | Get\_Vehicle\_Details called for each; side-by-side display |
| D — Book | User confirms vehicle \+ showroom | vehicleId validated; transition to Book\_Test\_Drive |
| E — Exit | User cancels or exits | reset\_active\_subagent called to clear state |

| Critical Booking Pre-condition The agent will NOT transition to booking unless (1) the user has explicitly confirmed their vehicle choice AND (2) vehicleId contains the ID of that specific confirmed vehicle. This prevents accidental bookings for the wrong vehicle. |
| :---- |

## **4.5 Escalation**

When a user requests a human agent or expresses extreme frustration, the Agent Router transitions to the Escalation subagent. When the escalate\_to\_human utility action is triggered, the conversation is directed to the Test\_Drive\_Route\_to\_Queue OmniChannel flow, and automated responses are discontinued.

# **5\. Business Rules**

| \# | Rule | Detail |
| :---- | :---- | :---- |
| BR-01 | Phone Validation | Phone number must be exactly 10 digits. Agent re-prompts on failure. |
| BR-02 | Future Date Requirement | Test drive date must be at least one calendar day after today. Today and past dates are rejected. |
| BR-03 | Time Slot Boundaries | Bookings only accepted between 9:00 AM and 7:00 PM. Requests outside this window are rejected with business hours guidance. |
| BR-04 | Slot Capacity | Maximum 3 bookings per time slot per day per dealership. At capacity, user must choose an alternative. |
| BR-05 | Duplicate Detection | Existing active bookings under the same email or phone are surfaced before a new booking is created. User must explicitly choose to proceed. |
| BR-06 | VehicleId Guard | Transition to booking from Explore Vehicles requires vehicleId to be set to the confirmed vehicle's Salesforce ID. |
| BR-07 | Preferred Channel Options | Only Email, WhatsApp, or SMS are accepted as confirmation channels. |
| BR-08 | Anti-Regression in Step 3 | Date/time change requests during Step 3 of booking are handled inline. No transition to Modify\_Booking is triggered. |
| BR-09 | Location Change Cancellation | When a user cancels due to a location/zip code change, a new booking flow is initiated with identity details pre-populated. |

# **6\. Date & Time Normalization**

A consistent date and time normalization process is applied across all subagents that accept date/time input from users. The agent must never ask the user to provide dates in a specific format.

## **6.1 Date Normalization**

* The Get\_Today\_Date action retrieves the current system date (stored in Current\_Date)

* Relative phrases such as 'tomorrow', 'next Monday', 'coming Thursday', 'April 30' are automatically converted to absolute YYYY-MM-DD format

* Normalization is performed silently — the user is never asked to convert dates

## **6.2 Time Slot Mapping**

| User Input | Time Range | Mapped Slot Value |
| :---- | :---- | :---- |
| 'Morning' or a time 9AM–11:59AM | 9:00 AM – 11:59 AM | Morning (9-12) |
| 'Afternoon' or a time 12PM–4:59PM | 12:00 PM – 4:59 PM | Afternoon (12-5) |
| 'Evening' or a time 5PM–7PM | 5:00 PM – 7:00 PM | Evening (5-7) |
| Any time outside 9AM–7PM | — | Rejected; re-prompt issued |

# **7\. Channel & Notifications**

## **7.1 Inbound Channels**

* Web chat and Whatsapp messaging via Salesforce Messaging Session

* Inbound email (emailCaseId, endUserEmail, and endUserContactId variables are populated for email-initiated sessions)

## **7.2 Outbound Confirmation Channels**

* Email — digital confirmation sent to the user's email address

* WhatsApp — confirmation delivered via WhatsApp messaging

## **7.3 Escalation Routing**

Conversations requiring human intervention are routed via the Test\_Drive\_Route\_to\_Queue OmniChannel Flow. The escalation message presented to the user before transfer is:

**Escalation Message**

"One moment, I'm transferring our conversation to get you more help."

# **8\. User Experience Guidelines**

## **8.1 Conversation Design Principles**

* Progressive disclosure: Do not ask for all information at once; use natural multi-turn conversation

* Silent processing: Date normalization, time mapping, and ID resolution are performed without narrating the process to the user

* Inline editing: Users may correct any booking detail during the confirmation step without restarting the flow

* Graceful re-prompting: Validation failures (invalid phone, past date, full slot) result in a helpful explanation and a re-prompt, never an error code

## **8.2 Multi-Language Support**

The agent’s primary locale is English (US), with English (UK) also available as an alternative setting.  The adaptive\_response\_allowed flag enables contextual language adaptation within these locales.

## **8.3 Welcome Message**

| Welcome Message "Welcome\! I can help you book a test drive of cars today. Just let me know which vehicle you're interested in, and we will get you on the schedule." |
| :---- |

# **9.Improvements**

1. The DocuSign integration is not working as expected at the moment, and will be resolved if time permits.

     2\. Use the geolocation API to provide accurate dealer search results.  
     3\. Implement SMS integration for customer validation via OTP.

# **10.Git Hub URL**

https://github.com/bhoomi3005/AgentForce-Hackathon