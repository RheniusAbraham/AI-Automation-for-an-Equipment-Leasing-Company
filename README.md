# AI-Automation-for-an-Equipment-Leasing-Company
🏗 Construction Equipment Leasing — AI Automation Prototype
A low-cost, AI-powered automation system for a construction equipment leasing business in Tamil Nadu, India. Built using n8n, Google Sheets, Groq API, and Telegram.

📋 Table of Contents

Business Problem
Solution Overview
Tech Stack
System Architecture
Google Drive Structure
Google Sheets Database
n8n Workflow
Telegram Bot Setup
Phone Recording Setup
Special Notes — Scaffolding Pricing
How It Works End-to-End
Current Status
Known Issues & Fixes
Next Steps


Business Problem
The owner of a construction equipment leasing business was:

Logging all bookings manually in a physical diary
Losing money due to human miscalculations
Missing delayed payments with no tracking system
Having no visibility into idle machines
Running the entire business through phone calls and WhatsApp


Solution Overview
An automated pipeline that:

Detects new call recordings uploaded to Google Drive
Transcribes the audio using Groq Whisper (supports Tamil + English)
Extracts booking information using Groq LLM (Llama 3.3)
Logs structured data into Google Sheets automatically
Sends instant Telegram alerts to the owner and employees


Tech Stack
ToolPurposeCostn8n CloudAutomation orchestrationFree trialGoogle DriveAudio file storageFreeGoogle SheetsDatabaseFreeGroq APIWhisper STT + Llama 3.3 LLMFree tierTelegram BotOwner + employee notificationsFreeSamsung Call RecorderCall recording on AndroidFree (built-in)FolderSync LiteAuto-upload recordings to DriveFree
Total running cost: ₹0 (prototype phase)

System Architecture
📞 Phone Call (Owner's Samsung Android)
        ↓
🎙 Samsung Built-in Call Recorder (.ogg / .m4a)
        ↓
📱 FolderSync App (auto-upload every 15 min)
        ↓
☁️ Google Drive → EquipmentLeasing_AI/CallRecordings/
        ↓
⚡ n8n Cloud (Google Drive Trigger — watches folder)
        ↓
📥 Download File Node (fetches audio binary)
        ↓
🎙 Groq Whisper API (transcribes Tamil + English audio)
        ↓
🤖 Groq Llama 3.3 (extracts structured booking JSON)
        ↓
📊 Google Sheets (appends row to Bookings tab)
        ↓
📲 Telegram Bot (notifies owner + employees)

Google Drive Structure
📁 EquipmentLeasing_AI/
    📁 CallRecordings/          ← n8n watches this folder
        📁 Processed/           ← files moved here after processing
        📁 Failed/              ← files that failed transcription
    📁 Database/
        📊 Master_Database      ← main Google Sheets file
    📁 Reports/
        📁 Daily/
        📁 Weekly/
    📁 Backups/
Important: n8n only watches the root CallRecordings/ folder, not subfolders. Files land here → get processed → move to Processed/.

Google Sheets Database
The Master_Database file has 4 tabs:
Tab 1 — Machines (Yellow)
ColumnDescriptionMachine_IDUnique ID e.g. M001Machine_Namee.g. JCB 01Machine_TypeJCB / Crane / Excavator / Dumper / ScaffoldingStatusFree / Booked / Maintenance / IdleOperator_NameAssigned operatorOperator_PhoneFor future alertsBase_Rate₹ per day OR ₹ per sqft/day (for Scaffolding)Rate_TypePer Day / Per SqFt/DayIdle_SinceAuto-set when status = FreeNotesManual notes
Tab 2 — Bookings (Purple)
ColumnDescriptionBooking_IDAuto-generated e.g. B1741234567Created_AtTimestamp of when row was createdSourceCall / WhatsApp / ManualCustomer_NameExtracted by AICustomer_PhoneExtracted by AIEquipment_TypeExtracted by AIQuantityNumber of machinesSquare_FeetOnly for Scaffolding bookingsStart_DateYYYY-MM-DDEnd_DateYYYY-MM-DDReturn_DateFilled manually when machine returnsDays_BookedAuto-calculated formulaDaily_RateExtracted by AITotal_AmountAuto-calculated formulaAdvance_PaidExtracted by AIBalance_DueAuto-calculated formulaPayment_StatusPending / Partial / Paid / OverdueReview_StatusPending / Confirmed / Flagged — owner updates thisAI_Confidence0-100% confidence of AI extractionNotesAI flags uncertain fields here
Tab 3 — Payments (Green)
ColumnDescriptionPayment_IDP001, P002...DatePayment dateBooking_IDLinks to Bookings tabCustomer_NameCopied from bookingAmount_PaidIn ₹ModeCash / UPI / NEFT / ChequeLogged_ByOwner / n8n / OperatorNotesAny notes
Tab 4 — Customers (Blue)
ColumnDescriptionCustomer_NameUnique customerPhoneContact numberTotal_BookingsFormula — count from BookingsTotal_RevenueFormula — sum from BookingsTotal_PaidFormula — sum from PaymentsOutstandingFormula — Revenue minus PaidScorePlatinum / Gold / Risk / BlacklistLast_BookingFormula — latest booking dateNotesBlacklist reason etc.

n8n Workflow
Workflow Name: Call Processing — Equipment Leasing
Node 1 — Google Drive Trigger

Trigger On: Changes Involving a Specific Folder
Folder: EquipmentLeasing_AI/CallRecordings/ (by URL)
Watch For: File Created
Poll Every: 15 Minutes

Node 2 — Download File

Resource: File
Operation: Download
File: By ID → {{ $json.id }}

Node 3 — HTTP Request (Groq Whisper)

Method: POST
URL: https://api.groq.com/openai/v1/audio/transcriptions
Authentication: Header Auth

Name: Authorization
Value: Bearer YOUR_GROQ_API_KEY


Body Type: Form-Data
Fields:

file → {{ $binary.data }} → n8n Binary File
model → whisper-large-v3
language → ta
response_format → text



Node 4 — HTTP Request1 (Groq LLM)

Method: POST
URL: https://api.groq.com/openai/v1/chat/completions
Authentication: Same Header Auth account
Body Type: JSON
Body:

json{
  "model": "llama-3.3-70b-versatile",
  "temperature": 0,
  "response_format": { "type": "json_object" },
  "messages": [
    {
      "role": "system",
      "content": "You are a booking extraction assistant for a construction equipment leasing business in Tamil Nadu, India. Extract ONLY business booking information from the transcript. Ignore all greetings and personal talk. Return ONLY valid JSON, nothing else. Today's date is {{ $now.toFormat('yyyy-MM-dd') }}. Equipment types: JCB, Crane, Excavator, Dumper, Scaffolding. For Scaffolding, extract square_feet. If a field is not mentioned, return null. If unsure about a field, add it to ambiguous_fields."
    },
    {
      "role": "user",
      "content": "Extract booking data from this transcript and return JSON with these fields: is_business_call (true/false), confidence (0-100), customer_name, customer_phone, equipment_type, quantity, square_feet (only for scaffolding), start_date (YYYY-MM-DD), end_date (YYYY-MM-DD), daily_rate, advance_amount, site_location, ambiguous_fields (array), notes.\n\nTranscript:\n{{ $json.data }}"
    }
  ]
}
Node 5 — Append Row in Sheet

Operation: Append Row
Document: Master_Database
Sheet: Bookings
Mapping: Manual
All expressions use: {{ JSON.parse($json.choices[0].message.content).field_name }}
Leave empty: Return_Date, Days_Booked, Total_Amount, Balance_Due (Sheets formulas handle these)

Node 6 — Telegram (Send Message)

Credential: Telegram Bot Token
Operation: Send Message
Chat ID: Owner's Chat ID
Parse Mode: Markdown (legacy)
Text:

📋 *NEW BOOKING DETECTED*

👤 Customer: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).customer_name }}
📞 Phone: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).customer_phone }}
🚜 Equipment: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).equipment_type }}
📅 Start: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).start_date }}
📅 End: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).end_date }}
💰 Rate: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).daily_rate }}
💵 Advance: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).advance_amount }}
🎯 Confidence: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).confidence }}%
⚠️ Notes: {{ JSON.parse($('HTTP Request1').item.json.choices[0].message.content).notes }}

✅ Check Google Sheets to CONFIRM or FLAG this booking.
For multiple employees: Add separate Telegram nodes in parallel after Node 5, each with a different Chat ID.

Telegram Bot Setup

Open Telegram → search @BotFather
Send /newbot → follow instructions
Copy the Bot Token given by BotFather
Open your bot → press Start → send any message
Get Chat ID from:

https://api.telegram.org/bot{YOUR_TOKEN}/getUpdates

Copy the "id" number inside the "chat" object

Note: If you get {"ok":false,"error_code":404} — the token in the URL is wrong. Go to BotFather → /mybots → select your bot → API Token to get a fresh copy.

Phone Recording Setup
Device: Samsung Android (built-in call recorder)
Auto-upload setup:

Install FolderSync Lite from Play Store
Link your Google account
Phone folder: /storage/emulated/0/Recordings/Call/
Drive destination: EquipmentLeasing_AI/CallRecordings/
Sync type: One way (phone → Drive)
Schedule: Every 15 minutes

Important: First verify that Samsung's built-in recorder captures both sides of the call clearly before relying on this pipeline.

Special Notes — Scaffolding Pricing
Scaffolding uses a different pricing model from other equipment:
EquipmentPricing FormulaJCB, Crane, Excavator, DumperDaily Rate × DaysScaffoldingSquare Feet × Rate per SqFt × Days
Example:

2400 sqft × ₹12/sqft/day × 10 days = ₹2,88,000

In Machines tab:

Base_Rate = rate per sqft per day (e.g. 12)
Rate_Type = Per SqFt/Day

In Bookings tab:

Square_Feet column captures the area from the call
AI extracts area mentions like "2400 square feet", "60×40 area" etc.


How It Works End-to-End

Owner receives a business call on his Samsung phone
Samsung auto-records the call
FolderSync uploads the recording to Google Drive every 15 min
n8n detects the new file and triggers the workflow
Groq Whisper transcribes the Tamil/English audio to text
Groq Llama 3.3 extracts structured booking data as JSON
n8n appends a new row to the Bookings sheet with Review_Status: Pending
Owner and employees receive a Telegram alert with booking summary
Owner opens Google Sheets and changes Review_Status to Confirmed or Flagged
If flagged → owner manually corrects the row


Current Status
ComponentStatusGoogle Drive folder structure✅ DoneMaster_Database Google Sheet✅ Donen8n workflow (all 6 nodes)✅ DoneGroq API connected✅ DoneTelegram Bot created✅ DoneNode 1 — Drive Trigger✅ Tested & WorkingNode 2 — Download File✅ Tested & WorkingNode 3 — Whisper STT🔧 Auth fix in progressNode 4 — LLM Extraction⏳ Pending testNode 5 — Google Sheets⏳ Pending testNode 6 — Telegram Alert⏳ Pending testFolderSync auto-upload⏳ Not configured yetFull end-to-end test⏳ Pending

Known Issues & Fixes
Issue 1 — Groq API 401 Unauthorized
Cause: Bearer prefix missing in Header Auth credential
Fix: Value must be exactly Bearer YOUR_API_KEY with a space between Bearer and the key
Issue 2 — LLM returns string not object
Cause: $json.choices[0].message.content is a string, not a parsed object
Fix: Wrap all expressions with JSON.parse():
{{ JSON.parse($json.choices[0].message.content).field_name }}
Issue 3 — Telegram 404 Not Found
Cause: Wrong bot token in the getUpdates URL
Fix: Get fresh token from BotFather → /mybots → your bot → API Token

Next Steps
Phase 2 — Intelligence Layer

 Daily 7AM report via Telegram (Cron node)
 Payment overdue alerts (>3 days)
 Idle machine alerts (>5 days)
 Machine status auto-update when booking starts/ends
 Move processed audio files to /Processed folder after workflow runs

Phase 3 — Analytics

 Weekly performance report (Sunday 6PM)
 Monthly business intelligence report
 Customer scoring (Platinum / Gold / Risk / Blacklist)

Phase 4 — Production

 Switch from Telegram to WhatsApp Business API
 Set up FolderSync for fully automatic recording pipeline
 Migrate from Google Sheets to Supabase
 Build simple web dashboard


Prototype built: March 2026
Stack: n8n Cloud + Google Drive + Google Sheets + Groq API + Telegram
Language support: Tamil + English (code-switching)
