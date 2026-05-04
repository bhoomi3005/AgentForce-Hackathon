

| ELECTRA CAR TEST DRIVE BOOKING AGENT Detailed Test Utterances & Scenario Scripts |
| :---- |

| Attribute | Value |
| :---- | :---- |
| Document Type | Test Utterances & Scenario Scripts |
| Agent | Electra Car Test Drive Booking Agent |
| Platform | Salesforce portal web bot & Whatsapp (7780507042) |
| Total Scenarios | 21 |
| Coverage | Happy path, validation, edge cases, boundary, routing guards |
| Site  | https://orgfarm-6742659fff.my.site.com/electracars/ |

# **1\. Book Test Drive**

These scenarios cover the full 4-step booking flow: identity collection, duplicate detection, slot filling, and booking confirmation. Each scenario is presented as a full conversation exchange between the customer (👤 USER) and the agent (🤖 AGENT).

| SCENARIO 01    Happy Path — New Booking End-to-End |
| :---- |

A new customer books a test drive with no existing bookings. All inputs are valid.

| 👤  USER | Hi, I'd like to book a test drive |
| :---: | :---- |
| **🤖  AGENT** | To book your test drive, I need your first name, last name, email address, and a 10-digit phone number. Could you please provide these details? |
| **👤  USER** | Sure — John Smith, john.smith@email.com, 9876543210 |
| **🤖  AGENT** | Which vehicle model would you like to test drive? If you need help exploring available options, let me know your preferences and I can show you what's in our catalog. |
| **👤  USER** | I'm interested in the X7 SUV |
| **🤖  AGENT** | Here are the available X7 SUV vehicles for test drives: Vehicle Group: X7 SUV Showroom Name: Electra Banjara Hills Zip Code: 500034 Vehicle Name: X7 SUV Showroom Name: Electra Banjara Hills Zip Code: 500034  |
| **👤  USER** | Find the nearest one — my zip is 500034 |
| **🤖  AGENT** | Perfect, I've found your nearest dealership: Downtown Showroom. What date would you like to come in? |
| **👤  USER** | How about next Friday? |
| **🤖  AGENT** | That would be Friday, 9th May 2025\. What time works for you — Morning (9–12), Afternoon (12–5), or Evening (5–7)? |
| **👤  USER** | Afternoon please |
| **🤖  AGENT** | And how would you like to receive your confirmation — Email, WhatsApp, or SMS? |
| **👤  USER** | Email |
| **🤖  AGENT** | Here’s your test drive booking summary: Vehicle: X7 SUV Showroom: Electra Banjara Hills (Zip: 500034\) Date: Friday, 2026-05-08 Time: Afternoon (12-5) Contact Method: Email ([john.smith@email.com](mailto:john.smith@email.com)) Name: John Smith Phone: 9876543210 Is everything correct, or would you like to change any detail (vehicle, location, date, time, contact method, phone, email, notes)? Please confirm or specify what you’d like to update.  |
| **👤  USER** | Yes, that's perfect |
| **🤖  AGENT** | Your test drive is booked\! Vehicle: X7 SUV Showroom: Electra Banjara Hills (Zip: 500034\) Date: Friday, 2026-05-08 Time: Afternoon (12-5) Contact Method: Email ([john.smith@email.com](mailto:john.smith@email.com)) Name: John Smith Phone: 9876543210 Booking Reference: TD-0148 Confirmation will be sent to your email. If you have any further questions or need to make changes, let me know. Enjoy your test drive\!  |

| ✔  Expected Behaviour Agent completes all 4 steps. Booking is created in Salesforce. Confirmation message displayed with booking ID. |
| :---- |

| SCENARIO 02    Relative Date Variations (New session) |
| :---- |

Tests that the agent correctly normalises different ways a customer might express a preferred date without asking them to reformat it.

| 👤  USER | I want to book for tomorrow |
| :---: | :---- |
| **🤖  AGENT** | Tomorrow is Friday, 2nd May 2025\. What time works — Morning (9–12), Afternoon (12–5), or Evening (5–7)? |
| **👤  USER** | Actually, make it this coming Sunday |
| **🤖  AGENT** | No problem — Sunday 4th May 2025 it is. Which time slot would you prefer? |
| **👤  USER** | Let's say Monday next week at 10am |
| **🤖  AGENT** | Monday next week is 2026-05-11. You requested 10am, which falls in the "Morning (9-12)" time slot. Here’s your updated booking summary: Vehicle: X7 SUV Showroom: Electra Banjara Hills (Zip: 500034\) Date: Monday, 2026-05-11 Time: Morning (9-12) Contact Method: Email ([john.smith@email.com](mailto:john.smith@email.com)) Name: John Smith Phone: 9876543210 Is everything correct, or would you like to change any detail (vehicle, location, date, time, contact method, phone, email, notes)? Please confirm or specify what you’d like to update.  |

| ✔  Expected Behaviour Agent silently converts all relative date phrases to absolute YYYY-MM-DD. The agent never asks the user to provide a date in a specific format. Time '10am' is silently mapped to 'Morning (9-12)'. |
| :---- |

| SCENARIO 03    Invalid Phone Number Validation (New session) |
| :---- |

| 👤  USER | My name is Sarah Jones, email sarah@test.com, phone 12345 |
| :---: | :---- |
| **🤖  AGENT** | Your phone number must be a 10-digit number. Could you please provide your correct phone number? |
| **👤  USER** | Oh sorry, it's 07712345678 —that's 11 |
| **🤖  AGENT** | Your phone number must be a 10-digit number. Please provide your correct 10-digit phone number so we can proceed with your booking. |
| **👤  USER** | 9876012345 |
| **🤖  AGENT** | Which car model would you like to test drive? If you’re not sure, I can help you explore available vehicles. |

| ✔  Expected Behaviour Agent re-prompts each time an invalid phone is given. Only proceeds once a valid 10-digit number is provided. Does not skip or accept partial numbers. |
| :---- |

| SCENARIO 04    Past Date and Today's Date Rejection |
| :---- |

| 👤  USER | Can I book a test drive for today? |
| :---: | :---- |
| **🤖  AGENT** | Test drives require at least 24 hours’ notice to prepare the vehicle, so the earliest available date is tomorrow. Which car model are you interested in, and would you like to book for tomorrow? |
| **👤  USER** | What about yesterday then? |
| **🤖  AGENT** | Bookings cannot be made for yesterday or any past date. The earliest available test drive date is tomorrow. Which car model are you interested in, and would you like to book for tomorrow? |
| **👤  USER** | Alright, how about next Tuesday? |
| **🤖  AGENT** | Next Tuesday is 2026-05-05. Which car model would you like to test drive? If you’re unsure, I can show you available vehicles to help you decide. |

| ✔  Expected Behaviour Agent rejects today's date and any past date with a clear explanation. Continues to re-prompt until a valid future date (today \+ 1 minimum) is provided. |
| :---- |

| SCENARIO 05    Time Outside Business Hours |
| :---- |

| 👤  USER | I'd like to come in at 8am |
| :---: | :---- |
| **🤖  AGENT** | Our earliest test drive slots start at 9am. Please choose one of these time slots : Morning (9-12) Afternoon (12-5) Evening (5-7) Which slot works best for you?  |
| **👤  USER** | How about 8pm instead? |
| **🤖  AGENT** | Test drives are available only between 9am and 7pm. Please choose from the following time slots : Morning (9-12) Afternoon (12-5) Evening (5-7) Which slot would you like?  |
| **👤  USER** | Fine, Evening then |
| **🤖  AGENT** | Which vehicle model would you like to test drive? If you’re not sure, I can show you available options. |

| ✔  Expected Behaviour Agent rejects both pre-opening (8am) and post-closing (8pm) times with a business hours explanation. Maps 'Evening' to 'Evening (5-7)' correctly. |
| :---- |

| SCENARIO 06    Duplicate Booking Detected — Proceed Anyway (New session) |
| :---- |

| 👤  USER | I want to book another test drive |
| :---: | :---- |
| **🤖  AGENT** | To book another test drive, I need your details. Please provide your first name, last name, email, and a 10-digit phone number. Once I have these, we can proceed with your booking. |
| **👤  USER** | John Smith, john.smith@email.com, 9876543210 |
| **🤖  AGENT** | You already have 1 active test drive booking under your contact details: Dealership: Electra Banjara Hills Date: 2026-05-08 Time Slot: Afternoon (12-5) Would you like to: Proceed anyway with a new test drive booking? Manage your existing booking? Cancel this new request? Let me know how you want to continue.  |
| **👤  USER** | Proceed anyway |
| **🤖  AGENT** | Which vehicle model would you like to test drive? If you have a specific car in mind, let me know the model name. If you want to explore available options, I can show you the catalog. |

| ✔  Expected Behaviour Agent surfaces the existing booking with full details. Presents 3 explicit choices. On 'Proceed anyway', continues to Step 2 slot filling without any disruption. |
| :---- |

| SCENARIO 07    Duplicate Detected — Manage Existing Booking (New session) |
| :---- |

| 👤  USER | I'd like to book a test drive please |
| :---: | :---- |
| **🤖  AGENT** | To book your test drive, I need your first name, last name, email address, and a 10-digit phone number. Could you please provide these details? |
| **👤  USER** | John Smith, [john.smith@email.com](mailto:john.smith@email.com), 9876543210 |
| **🤖  AGENT** | John, I found 1 existing active test drive booking under your contact details: Dealership: Electra Banjara Hills Date: 2026-05-08 Time Slot: Afternoon (12-5) Would you like to: Proceed anyway with a new booking Manage your existing booking Cancel this new request Please let me know how you'd like to continue.  |
| **👤  USER** | I'd like to manage the existing one |
| **🤖  AGENT** | How would you like to manage your existing test drive booking? You can: Reschedule (change the date or time) Cancel the reservation Update your details Check or confirm the booking status Please let me know your preference.  |

| ✔  Expected Behaviour Agent transitions to Modify Booking subagent. Identity details (name, email, phone) carry over — agent does not ask for them again. |
| :---- |

| SCENARIO 08    Step 3 Inline Edit — Change Date and Time (continue to scenario 7\) |
| :---- |

After receiving the booking summary, the customer requests date and time changes. These must be handled inline without transitioning to Modify Booking.

| 👤  USER | Actually can we change the date to next Monday? |
| :---: | :---- |
| **🤖  AGENT** | Next Monday is May 11, 2026\. The morning slot (9-12) is available for rescheduling your test drive. Would you like to confirm this change to May 11, 2026, in the morning slot? If yes, please provide a brief reason for rescheduling (e.g., schedule conflict, personal preference, etc.).  |
| **👤  USER** | Change it to morning please |
| **🤖  AGENT** | To confirm, you want to reschedule your test drive to Monday, May 11, 2026, in the morning slot (9-12). Could you please provide a brief reason for rescheduling? For example: schedule conflict, personal preference, or any other reason.  |
| **👤  USER** | iam unavailable  |
| **🤖  AGENT** | Your test drive has been successfully rescheduled to Monday, May 11, 2026, in the morning slot (9-12) due to your unavailability on the original date. Would you like to update any other details, check your booking status, or make further changes?  |

| ✔  Expected Behaviour Date and time change is handled inline within Step 3\. No transition to Modify Booking. Summary is re-displayed after each edit. Booking only created on explicit 'Yes' confirmation. |
| :---- |

| SCENARIO 09    Step 3 — Ambiguous Change Request (Continue from scenario 8\) |
| :---- |

| 🤖  AGENT | Here's your summary: • Vehicle: Model S  |  Date: 8 May 2025  |  Time: Evening (5–7)  |  Channel: SMS Does this all look correct? |
| :---: | :---- |
| **👤  USER** | I want to change something |
| **🤖  AGENT** | What would you like to change about your booking? You can: Reschedule (change the date or time) Cancel the reservation Update your details Check or confirm booking status Please specify what you want to change.  |
| **👤  USER** | The time |
| **🤖  AGENT** | Please specify your new preferred time for the test drive. Available slots are: Morning (9-12) Afternoon (12-5) Evening (5-7) Let me know which slot you'd like to change to, and I'll check the availability for you.  |
| **👤  USER** | afternoon |
| **🤖  AGENT** | To change your test drive time to the afternoon slot (12-5) on May 11, 2026, please confirm that you would like to proceed. Once you confirm, I will update your booking. Would you like to finalize this change?  |
| **👤  USER** | Yes |
| **🤖  AGENT** | Your test drive has been successfully rescheduled to the afternoon slot (12-5) on May 11, 2026\. Is there anything else you would like to update, check, or change about your booking?  |

| ✔  Expected Behaviour When the user says 'change something' without specifying a field, the agent asks a targeted follow-up listing all editable fields. STOPS and waits before proceeding. |
| :---- |

# **2\. Modify Booking**

| SCENARIO 10    Reschedule by Booking ID — Happy Path |
| :---- |

| 👤  USER | I need to reschedule my test drive |
| :---: | :---- |
| **🤖  AGENT** | To help you reschedule your test drive, please provide your Booking Id. If you do not have it, let me know and I’ll ask for your email and phone number instead.  |
| **👤  USER** | It's TD-0148 |
| **🤖  AGENT** | To reschedule your test drive, please provide your new preferred date, preferred time slot (morning, afternoon, or evening), and a brief reason for rescheduling. Available times are between 9:00 AM and 7:00 PM. Let me know your new preferences and I’ll check availability for you.  |
| **👤  USER** | Can we move it to 15th May in the afternoon? |
| **🤖  AGENT** | The afternoon slot (12-5) on May 15, 2026, is available for your test drive. Would you like to confirm this reschedule? If yes, please provide a brief reason for rescheduling (e.g., schedule conflict, personal preference, etc.). |
| **👤  USER** | Yes please |
| **🤖  AGENT** | Your test drive has been successfully rescheduled to May 15, 2026, in the afternoon slot (12-5) due to a schedule conflict. Would you like to make any other changes, check your booking status, or update your details?  |

| ✔  Expected Behaviour Agent retrieves record by booking ID. Checks slot availability. BookedAppointmentCount \< 3, so proceeds. Confirms update with user before executing. |
| :---- |

| SCENARIO 11    Reschedule — Slot Full (BookedAppointmentCount ≥ 3\) |
| :---- |

| 👤  USER | My booking ID is TD-00789. I want to move to 20th May morning |
| :---: | :---- |
| **🤖  AGENT** | I've pulled up your booking. Let me check availability for 20 May 2025, Morning (9–12)... I'm sorry, that slot is fully booked — we already have 3 test drives scheduled at that time. Could you choose a different date or time? |
| **👤  USER** | How about 20th May afternoon? |
| **🤖  AGENT** | Checking that for you... The Afternoon (12–5) slot on 20th May is available. To confirm: reschedule to 20 May 2025, Afternoon (12–5)? |
| **👤  USER** | Yes |
| **🤖  AGENT** | Your booking has been updated. Confirmation details will follow shortly. |

| ✔  Expected Behaviour Agent correctly identifies the slot as full (count ≥ 3\) and re-prompts. Accepts the next valid slot without re-entering full identification flow. |
| :---- |

| SCENARIO 12    Cancel — Fallback Identification (No Booking ID) (Fresh session ) |
| :---- |

| 👤  USER | I want to cancel my test drive booking |
| :---: | :---- |
| **🤖  AGENT** | To cancel your test drive booking, I need to identify your reservation. Do you have your Booking Id? If yes, please provide it. If not, let me know and I’ll ask for your email and phone number instead. |
| **👤  USER** | I don't have it with me |
| **🤖  AGENT** | Please provide both your email address and phone number so I can locate your test drive booking and assist with cancellation. |
| **👤  USER** | [sunilgedala@google.com](mailto:sunilgedala@google.com) and 9866909613 |
| **🤖  AGENT** | What is the reason you want to cancel your TD-0119 booking? Please share your cancellation reason so I can proceed. If you are unavailable or have a scheduling conflict, I can help you reschedule instead.  |
| **USER** | TD-0119 |
| **AGENT** | What is the reason you want to cancel your TD-0119 booking? Please share your cancellation reason so I can proceed. If you are unavailable or have a scheduling conflict, I can help you reschedule instead.  |
| **👤  USER** | I've decided not to buy a car right now |
| **🤖  AGENT** | You want to cancel booking TD-0119 because you’ve decided not to buy a car right now. To confirm, do you want to permanently cancel this booking? Once cancelled, it cannot be restored. Please reply “Yes, cancel” to proceed or let me know if you changed your mind. |
| **👤  USER** | Yes, please cancel it |
| **🤖  AGENT** | Your TD-0119 test drive booking has been successfully cancelled. If you decide to book again in the future or need assistance with anything else, just let me know. How else can I help you today? |

| ✔  Expected Behaviour Fallback identification via email \+ phone works correctly. Cancellation reason is collected. Explicit confirmation is required before executing the cancel action. |
| :---- |

| SCENARIO 13    Cancel — Location Change (Triggers New Booking) (New session ) |
| :---- |

| 👤  USER | I need to cancel — I've moved to a new area |
| :---: | :---- |
| **🤖  AGENT** | To cancel your test drive booking and help you book a new one in your new area, I need to identify your reservation. Do you have your Booking Id? If not, please provide your Email and Phone Number so I can look up your booking. |
| **👤  USER** | **TD-0121**  |
| **🤖  AGENT** | You want to cancel your booking because you've moved to a new area. I will cancel your current test drive and then help you book a new one in your new location. Can you confirm that your reason for cancellation is moving to a new area? Once confirmed, I will process your cancellation and guide you to book a test drive in your new zip code.  |
| **👤  USER** | 500072 |
| **🤖  AGENT** | Your booking has been successfully cancelled due to your move to zip code 500072\. Would you like to book a new test drive in your new area? If so, please let me know your preferred vehicle and date, and I will help you schedule your test drive in zip code 500072\.  |

| ✔  Expected Behaviour Agent detects location-change cancellation reason. Executes cancel, then transitions to Book Test Drive subagent with name/email/phone pre-populated. Skips identity re-collection. Goes straight to asking for the new zip code. |
| :---- |

| SCENARIO 14    Multiple Bookings Returned — User Selects One  |
| :---- |

| 👤  USER | I want to check my booking status. Email: [sunilgedala@google.com](mailto:sunilgedala@google.com), phone: 9866909613  |
| :---: | :---- |
| **🤖  AGENT** | Here are your current and past test drive bookings: TD-0077: X7 SUV 2, Electra Banjara Hills, 2026-05-03, Afternoon (12-5), Status: Confirmed TD-0078: X7 SUV 2, Electra Banjara Hills, 2026-05-03, Morning (9-12), Status: Confirmed TD-0079: S5 Sedan, Electra Secunderabad, 2026-05-02, Morning (9-12), Status: Confirmed TD-0080: X7 SUV 2, Electra Banjara Hills, 2026-05-02, Morning (9-12), Status: Confirmed TD-0108: S5 Sedan, Electra Secunderabad, 2026-05-03, Afternoon (12-5), Status: Confirmed TD-0109: X7 SUV 2, Electra Banjara Hills, 2026-05-03, Morning (9-12), Status: Confirmed TD-0113: X7 SUV 2, Electra Banjara Hills, 2026-05-03, Morning (9-12), Status: Confirmed TD-0119: X7 SUV 2, Electra Banjara Hills, 2026-05-05, Afternoon (12-5), Status: Cancelled TD-0121: X7 SUV 2, Electra Banjara Hills, 2026-05-05, Afternoon (12-5), Status: Cancelled TD-0138: X7 SUV 2, Electra Banjara Hills, 2026-05-03, Afternoon (12-5), Status: Confirmed TD-0139: X7 SUV, Electra Banjara Hills, 2026-05-04, Afternoon (12-5), Status: Cancelled TD-0140: X7 SUV 2, Electra Banjara Hills, 2026-05-04, Morning (9-12), Status: Confirmed  |
| **👤  USER** | S5 Sedan |
| **🤖  AGENT** | You have two bookings for the S5 Sedan: TD-0079: Electra Secunderabad, 2026-05-02, Morning (9-12), Status: Confirmed TD-0108: Electra Secunderabad, 2026-05-03, Afternoon (12-5), Status: Confirmed Which booking would you like to check the status for, cancel, or reschedule? Please specify the Booking Id (TD-0079 or TD-0108).  |
| **👤  USER** | No, just checking — thanks |
| **🤖  AGENT** | You’re all set\! If you need to check, change, or cancel your S5 Sedan bookings (TD-0079 or TD-0108), just let me know the Booking Id and your request. If you need help with any other vehicle or booking, I’m here to assist. Would you like to do anything else? |

| ✔  Expected Behaviour When multiple records are returned via email/phone fallback, all bookings are listed clearly. Agent waits for user to select one before proceeding. Status is displayed correctly. |
| :---- |

# **3\. Change Vehicle for Test Drive**

| SCENARIO 15    Change Vehicle — Same Date and Time (New session) |
| :---- |

| 👤  USER | I'd like to change the vehicle on my test drive booking |
| :---: | :---- |
| **🤖  AGENT** | To change the vehicle on your test drive booking, I need to identify your booking. Could you please provide your Booking Id? If you don't have it, let me know and I'll ask for your email and phone number instead. |
| **👤  USER** | [TD-0125](https://orgfarm-6742659fff.lightning.force.com/lightning/r/a04ak00000711AvAAI/view) |
| **🤖  AGENT** | These vehicles are available for your test drive booking at Electra LB Nagar (Zip code: 500074): C3 City Tata Tigor Zephyr X3 Which vehicle would you like to switch to? Once you select, would you like to keep your currently scheduled date (2026-05-04) and time slot (Evening, 5-7 PM), or do you want to change them?  |
| **👤  USER** | C3 City please |
| **🤖  AGENT** | Would you like to keep your currently scheduled date (2026-05-04) and time slot (Evening, 5-7 PM) for your test drive with the C3 City, or do you want to change the date or time? |
| **👤  USER** | Yes, keep the same slot |
| **🤖  AGENT** | Your test drive booking has been updated to the C3 City for the same date (2026-05-04) and time slot (Evening, 5-7 PM) at Electra LB Nagar. If you need to make any more changes or have questions about your booking, just let me know\!  |

| ✔  Expected Behaviour Agent displays available vehicles at the same showroom. Vehicle is swapped. Date and time are retained without re-checking slot availability (same slot, same day). |
| :---- |

| SCENARIO 16    Change Vehicle — With Date and Time Reschedule (Continue) |
| :---- |

| 👤  USER | Change vehicle on TD-0125 |
| :---: | :---- |
| **🤖  AGENT** | These vehicles are available for your test drive at Electra LB Nagar (Zip code: 500074): C3 City Tata Tigor Zephyr X3 Which vehicle would you like to switch to for your booking TD-0125? Once you select, would you like to keep your currently scheduled date (2026-05-04) and time slot (Evening, 5-7 PM), or change them?  |
| **👤  USER** | Zephyr X3  |
| **🤖  AGENT** | Your test drive booking TD-0125 has been updated to Zephyr X3 for the same date (2026-05-04) and time slot (Evening, 5-7 PM) at Electra LB Nagar. If you want to make further changes or have questions about your booking, let me know\!  |
| **👤  USER** | No, let's change the date too — 28th May, evening |
| **🤖  AGENT** | To update your booking TD-0125 to Zephyr X3 for 28th May, evening slot, I need a reschedule reason. Please provide your reason for changing the date and vehicle. Available times are between 9:00 AM and 7:00 PM; your requested slot maps to "Evening (5-7)". |
| **👤  USER** | Unavailable on the previous booking date  |
| **🤖  AGENT** | Your test drive booking TD-0125 has been updated to Zephyr X3 for 28th May, evening slot (5-7 PM) at Electra LB Nagar, with the reason "Unavailable on the previous booking date." If you need to make any more changes or want to review your booking details, let me know\!  |

| ✔  Expected Behaviour Agent handles both vehicle change and rescheduling in one flow. Slot availability is checked for new date/time. Confirmation required before update. |
| :---- |

# **4\. Explore Vehicles**

| SCENARIO 17    Browse Catalog — Then Book (New session) |
| :---- |

| 👤  USER | What cars do you have available for test drives? |
| :---: | :---- |
| **🤖  AGENT** | Here are the vehicles available for test drives at Electra showrooms: **X7 SUV** Showroom: Electra Banjara Hills (Zip Code: 500034\) **Electra Zenith X5** Showroom: Electra Kukatpally (Zip Code: 500072\) **C3 City** Showroom: Electra LB Nagar (Zip Code: 500074\) **T9 Truck** Showroom: Electra Banjara Hills (Zip Code: 500034\) **S5 Sedan** Showroom: Electra Secunderabad (Zip Code: 500003\) **Neo Sedan** Showroom: Electra Kondapur (Zip Code: 500032\) **Tata Tigor** Showroom: Electra LB Nagar (Zip Code: 500074\) **Electra Apex** Showroom: Electra Film Nagar (Zip Code: 500016\) **Electra Pulse RS** Showroom: Electra Kukatpally (Zip Code: 500072\) **Zephyr X3** Showroom: Electra LB Nagar (Zip Code: 500074\) **2024 Apex Electra GT** Showroom: Electra Kukatpally (Zip Code: 500072\) **2023 Zenith Pulse** Showroom: Electra Jubilee Hills (Zip Code: 500033\) **2025 Titan Hauler** Showroom: Electra Madhapur (Zip Code: 500081\) **2024 Nova Sprinter** Showroom: Electra Film Nagar (Zip Code: 500016\) **2022 Orion X-Track** Showroom: Electra Gachibowli (Zip Code: 500032\) **2024 Bolt E-Courier** Showroom: Electra Gachibowli (Zip Code: 500032\) **Tesla Model 3 Performance** Showroom: Electra Gachibowli (Zip Code: 500032\) Would you like more details about any specific model or to book a test drive for one of these vehicles?  |
| **👤  USER** | Tell me more about the **Apex Electra GT** |
| **🤖  AGENT** | Here are the technical details for the 2024 Apex Electra GT: **Model:** 2024 Apex Electra GT**Engine/Drive:** AWD (All-Wheel Drive)**Door Count:** 4**Seats:** 5**Maximum Gross Weight:** 2,850 kg**Battery Capacity:** 95 kWh**Availability:** Eligible for test drive Would you like to book a test drive for the 2024 Apex Electra GT at Electra Kukatpally, or do you want more details about its features or specifications?  |
| **👤  USER** | Yes, let's book the Apex Electra GT at Electra Kukatpally  |
| **🤖  AGENT** | To book your test drive for the Apex Electra GT at Electra Kukatpally, I need your details: First name Last name Email address Phone number (10 digits) Please provide these so I can reserve your slot.  |

| ✔  Expected Behaviour Agent displays showroom-grouped catalog. User can drill into a specific vehicle. vehicleId is set when user selects Model S at Downtown. Transition to Book Test Drive only after explicit confirmation of vehicle AND showroom. |
| :---- |

| SCENARIO 18    Compare Two Vehicles  (New session) |
| :---- |

| 👤  USER | Can you compare the Apex Electra GT and the Zephyr X3 for me?  |
| :---: | :---- |
| **🤖  AGENT** | Here is a side-by-side comparison of the Apex Electra GT and Zephyr X3: **Apex Electra GT** Doors: 4 Drivetrain: AWD (All-Wheel Drive) Maximum Gross Weight: 2,850 kg Battery Capacity: 95 kWh Seats: 5 Test Drive Eligible: Yes **Zephyr X3** Doors: 4 Drivetrain: RWD (Rear-Wheel Drive) Maximum Gross Weight: 2,600 kg Battery Capacity: 100 kWh Maximum Battery Range: 550 km Fuel Type: BEV (Battery Electric Vehicle) Body Style: Hatchback Seats: 5 Test Drive Eligible: Yes Would you like more detailed specs or information about test drive availability for either model?  |
| **👤  USER** | Proceed with **Zephyr X3**  |
| **🤖  AGENT** | Here is the Zephyr X3 available for test drive: **Showroom Name:** Electra LB Nagar **Zip Code:** 500074 **Vehicle Name:** Zephyr X3 Please confirm if you want to proceed with booking a test drive for the Zephyr X3 at Electra LB Nagar showroom.  |

| ✔  Expected Behaviour Get\_Vehicle\_Details is called for each vehicle. Side-by-side comparison is displayed without full technical specs to avoid overwhelming the user. Agent then asks for explicit showroom selection. |
| :---- |

| SCENARIO 19    Vehicle Not Available  (New session) |
| :---- |

| 👤  USER | I want to book a test drive |
| :---: | :---- |
| **🤖  AGENT** | To book your test drive, I need your first name, last name, email address, and a 10-digit phone number. Please provide these details so we can proceed. |
| **👤  USER** | Just book it |
| **🤖  AGENT** | To book your test drive, I need your first name, last name, email address, and a 10-digit phone number. Please provide these details so we can proceed with the booking. |
| **👤  USER** | sridhar,dindi,[eeee@gmail.com](mailto:eeee@gmail.com),7898998998  |
| **🤖  AGENT** | Which car model would you like to test drive? If you have a specific vehicle in mind, please let me know. If not, I can help you explore available options. |
| **👤  USER** | cx7 SUV2 |
| **🤖  AGENT** | I'm sorry, I couldn't find any vehicles matching the description "cx7 SUV2." Would you like to view all available vehicles or search for a different model? |

| ✔  Expected Behaviour Agent refuses to transition to booking until vehicleId is set for a confirmed vehicle. Does not proceed on vague input like 'just book it'. Once vehicle and showroom are explicitly confirmed, transition happens correctly. |
| :---- |

# **5\. Agent Router & Escalation**

| SCENARIO 20    Escalation — Human Agent Request |
| :---- |

| 👤  USER | I want to speak to a real person |
| :---: | :---- |
| **🤖  AGENT** | Of course — let me transfer you now. One moment, I'm transferring our conversation to get you more help. |
| **👤  USER** | \*(transferred — no further automated responses)\* |

| ✔  Expected Behaviour Agent immediately routes to the Escalation subagent. escalate\_to\_human action fires. OmniChannel routes to Test\_Drive\_Route\_to\_Queue. No further automated responses are given after escalation. |
| :---- |

| 👤  USER | This is pointless, just get me a human |
| :---: | :---- |
| **🤖  AGENT** | I understand your frustration. Let me connect you with one of our team members right away. One moment, I'm transferring our conversation to get you more help. |

| ✔  Expected Behaviour Extreme frustration signal is also detected and triggers escalation. Same routing behaviour as explicit request. |
| :---- |

| SCENARIO 21    Anti-Regression Guard — Date Change in Step 3 |
| :---- |

This scenario validates that a date/time change request during Step 3 confirmation does NOT trigger a transition to Modify Booking. The edit must be handled entirely within the Book Test Drive subagent.

| 🤖  AGENT | Here's your booking summary: • Vehicle: Model X  |  Date: 9 May 2025  |  Time: Afternoon (12–5) Does everything look correct? |
| :---: | :---- |
| **👤  USER** | Can you reschedule this to next week? |
| **🤖  AGENT** | No problem — what date and time next week would you prefer? |
| **👤  USER** | Wednesday the 14th at 11am |
| **🤖  AGENT** | 11am falls in the Morning slot (9–12). Let me update that for you. Here's your revised summary: • Vehicle: Model X  |  Date: 14 May 2025  |  Time: Morning (9–12) Shall I confirm this booking? |
| **👤  USER** | Yes |
| **🤖  AGENT** | Your test drive is booked\! Confirmation: TD-00956. |

| ✔  Expected Behaviour The word 'reschedule' during Step 3 does NOT trigger a transition to Modify Booking. The Agent Router's anti-regression guard correctly keeps the flow in Book Test Drive. Date is normalised, time is mapped to Morning (9-12). Booking is created on confirmation. |
| :---- |

