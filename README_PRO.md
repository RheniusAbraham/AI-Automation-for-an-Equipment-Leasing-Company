# AI-Powered Automation System for Equipment Leasing

**Automated call processing pipeline for construction equipment leasing businesses**

A low-cost, intelligent automation system that converts voice calls into structured booking data, powered by AI transcription and extraction. Designed for equipment leasing operations in Tamil Nadu, India.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
  - [Google Drive Configuration](#google-drive-configuration)
  - [Google Sheets Database](#google-sheets-database)
  - [n8n Workflow Setup](#n8n-workflow-setup)
  - [Telegram Bot Configuration](#telegram-bot-configuration)
  - [Phone Recording Setup](#phone-recording-setup)
- [Workflow Documentation](#workflow-documentation)
- [Pricing Models](#pricing-models)
- [Operations Guide](#operations-guide)
- [Troubleshooting](#troubleshooting)
- [Roadmap](#roadmap)
- [Project Info](#project-info)

---

## Overview

### Problem Statement

Equipment leasing businesses often operate with manual processes that lead to:
- Inaccurate booking records and data entry errors
- Revenue loss due to missed payment tracking
- Poor visibility into equipment utilization
- Delayed processing of rental inquiries
- Dependency on phone calls and unstructured communication

### Solution

An end-to-end automation pipeline that:
1. **Captures** incoming business calls and auto-records them
2. **Transcribes** audio to text using Groq Whisper (Tamil + English support)
3. **Extracts** structured booking data using Groq Llama 3.3 LLM
4. **Logs** entries automatically into Google Sheets
5. **Alerts** owners and employees via Telegram in real-time

---

## Key Features

| Feature | Capability |
|---------|-----------|
| **Multi-language Support** | Tamil and English (code-switching) |
| **Real-time Processing** | 15-minute polling cycle for new recordings |
| **AI-Powered Extraction** | Automatic booking details extraction from conversations |
| **Confidence Scoring** | 0-100% confidence levels for each extraction |
| **Equipment Tracking** | Support for 5 equipment types (JCB, Crane, Excavator, Dumper, Scaffolding) |
| **Dynamic Pricing** | Dual pricing models (per-day and per-sqft) |
| **Audit Trail** | Complete record of AI confidence, extraction ambiguities, and manual corrections |
| **Cost Optimized** | Zero operating costs in prototype phase |

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│ ACQUISITION LAYER                                               │
│ ┌────────────────────┐                                          │
│ │ Samsung Phone      │                                          │
│ │ (Auto-recording)   │                                          │
│ └──────────┬─────────┘                                          │
│            │ .ogg / .m4a                                        │
│            ↓                                                     │
│ ┌────────────────────┐                                          │
│ │ FolderSync Lite    │                                          │
│ │ (15-min sync)      │                                          │
│ └──────────┬─────────┘                                          │
└─────────────┼───────────────────────────────────────────────────┘
              │
┌─────────────┼───────────────────────────────────────────────────┐
│ STORAGE LAYER                                                   │
│            ↓                                                     │
│ ┌────────────────────────────┐                                  │
│ │ Google Drive              │                                  │
│ │ CallRecordings/           │                                  │
│ └──────────┬─────────────────┘                                 │
└─────────────┼───────────────────────────────────────────────────┘
              │
┌─────────────┼───────────────────────────────────────────────────┐
│ ORCHESTRATION LAYER                                             │
│            ↓                                                     │
│ ┌────────────────────────────────────┐                          │
│ │ n8n Cloud - Call Processing        │                          │
│ │ Workflow: Equipment Leasing        │                          │
│ └──────────┬─────────────────────────┘                          │
└─────────────┼───────────────────────────────────────────────────┘
              │
┌─────────────┼───────────────────────────────────────────────────┐
│ PROCESSING LAYER                                                │
│            ├─────────────────┐                                  │
│            │                 │                                  │
│            ↓                 ↓                                  │
│ ┌─────────────────┐  ┌─────────────────┐                       │
│ │ Groq Whisper    │  │ Groq Llama 3.3  │                       │
│ │ (Transcription) │  │ (Extraction)    │                       │
│ └────────┬────────┘  └────────┬────────┘                       │
└─────────────┼──────────────────┼──────────────────────────────┘
              │                  │
┌─────────────┼──────────────────┼──────────────────────────────┐
│ OUTPUT LAYER                   │                              │
│                               ↓                              │
│                 ┌──────────────────────────┐                 │
│                 │ Google Sheets            │                 │
│                 │ Master_Database          │                 │
│                 │ (4 tabs, structured)     │                 │
│                 └──────────┬───────────────┘                 │
│                            │                                  │
│                            ↓                                  │
│                 ┌──────────────────────────┐                 │
│                 │ Telegram Bot             │                 │
│                 │ (Real-time alerts)       │                 │
│                 └──────────────────────────┘                 │
└────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Component | Purpose | Cost | Status |
|-----------|---------|------|--------|
| **n8n Cloud** | Workflow orchestration | Free tier | ✅ Active |
| **Google Drive** | Audio file storage & retrieval | Free | ✅ Active |
| **Google Sheets** | Structured data warehouse | Free | ✅ Active |
| **Groq API** | Speech-to-text + LLM extraction | Free tier | ✅ Active |
| **Telegram Bot API** | Real-time notifications | Free | ✅ Active |
| **Samsung Call Recorder** | Audio capture | Free (built-in) | ✅ Active |
| **FolderSync Lite** | Automated file synchronization | Free | ⏳ Pending setup |

**Total Operating Cost:** ₹0 (Prototype Phase)

---

## Prerequisites

### Required Accounts & Services
- [ ] Google Account (for Drive & Sheets)
- [ ] Groq API account and API key ([groq.com](https://console.groq.com))
- [ ] Telegram account and access to BotFather
- [ ] n8n Cloud account ([n8n.io](https://n8n.io))
- [ ] Samsung Android device with built-in call recorder

### Required Software
- FolderSync Lite (Android): Available on Google Play Store
- Android 8.0+ (for auto-recording and upload)
- Telegram Mobile/Desktop application

### Network Requirements
- Stable internet connection (for Google Drive sync)
- Minimum 1 Mbps upload speed (for 15-minute audio uploads)

---

## Installation & Setup

### 1. Google Drive Configuration

#### Step 1A: Create Folder Structure
1. Open [Google Drive](https://drive.google.com)
2. Create the following folder hierarchy:

```
EquipmentLeasing_AI/
├── CallRecordings/          (n8n monitors this)
│   ├── Processed/           (successful recordings)
│   └── Failed/              (failed transcriptions)
├── Database/
│   └── Master_Database      (Google Sheets - create next)
├── Reports/
│   ├── Daily/
│   └── Weekly/
└── Backups/
```

#### Step 1B: Share & Set Permissions
- Right-click `EquipmentLeising_AI` folder → **Share**
- Add your Google account with Editor access
- Copy the folder URL (you'll need this for n8n)

---

### 2. Google Sheets Database

#### Step 2A: Create Master Database

1. Inside `EquipmentLeasing_AI/Database/`, create a new Google Sheet named `Master_Database`
2. Create these 4 tabs:

#### Tab 1: Machines (Machine Inventory)

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| Machine_ID | Text | Unique identifier | M001 |
| Machine_Name | Text | Display name | JCB 01 |
| Machine_Type | Dropdown | JCB / Crane / Excavator / Dumper / Scaffolding | JCB |
| Status | Dropdown | Free / Booked / Maintenance / Idle | Free |
| Operator_Name | Text | Assigned operator | Raj Kumar |
| Operator_Phone | Text | Contact for alerts | 9876543210 |
| Base_Rate | Number | ₹/day or ₹/sqft/day | 5000 |
| Rate_Type | Dropdown | Per Day / Per SqFt/Day | Per Day |
| Idle_Since | Date | Auto-set when Free | 2026-03-01 |
| Notes | Text | Additional info | New unit |

#### Tab 2: Bookings (Rental Records)

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| Booking_ID | Text | Auto-generated | B1741234567 |
| Created_At | DateTime | System timestamp | 2026-03-08 10:30 |
| Source | Dropdown | Call / WhatsApp / Manual | Call |
| Customer_Name | Text | Extracted by AI | Acme Construction |
| Customer_Phone | Text | Extracted by AI | 9876543210 |
| Equipment_Type | Dropdown | Equipment category | JCB |
| Quantity | Number | Units booked | 2 |
| Square_Feet | Number | Area (Scaffolding only) | 2400 |
| Start_Date | Date | Booking start | 2026-03-10 |
| End_Date | Date | Expected return | 2026-03-20 |
| Return_Date | Date | Actual return | (empty) |
| Days_Booked | Formula | `=IF(Return_Date="",TODAY()-Start_Date,Return_Date-Start_Date)` | 10 |
| Daily_Rate | Number | ₹/day from extraction | 5000 |
| Total_Amount | Formula | `=Daily_Rate * Days_Booked` or `=Square_Feet * Daily_Rate * Days_Booked` | 50000 |
| Advance_Paid | Number | Initial payment | 10000 |
| Balance_Due | Formula | `=Total_Amount - Advance_Paid` | 40000 |
| Payment_Status | Dropdown | Pending / Partial / Paid / Overdue | Partial |
| Review_Status | Dropdown | Pending / Confirmed / Flagged | Confirmed |
| AI_Confidence | Number | 0-100% confidence score | 95 |
| Notes | Text | AI flagged ambiguities | None |

#### Tab 3: Payments (Payment Tracking)

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| Payment_ID | Text | Unique ID | P001 |
| Date | Date | Payment received date | 2026-03-08 |
| Booking_ID | Text | Link to booking | B1741234567 |
| Customer_Name | Text | From Bookings tab | Acme Construction |
| Amount_Paid | Number | Amount in ₹ | 10000 |
| Mode | Dropdown | Cash / UPI / NEFT / Cheque | UPI |
| Logged_By | Dropdown | Owner / n8n / Operator | Owner |
| Notes | Text | Additional details | First installment |

#### Tab 4: Customers (Customer Master)

| Column | Type | Description | Formula |
|--------|------|-------------|---------|
| Customer_Name | Text | Unique customer | Manual entry |
| Phone | Text | Primary contact | Manual entry |
| Total_Bookings | Number | Count of all bookings | `=COUNTIF(Bookings!C:C,A1)` |
| Total_Revenue | Currency | Sum of all booking amounts | `=SUMIF(Bookings!B:B,A1,Bookings!I:I)` |
| Total_Paid | Currency | Sum of all payments | (Formula in Payments tab) |
| Outstanding | Currency | Unpaid balance | `=Total_Revenue - Total_Paid` |
| Score | Dropdown | Platinum / Gold / Risk / Blacklist | Manual |
| Last_Booking | Date | Most recent booking | `=MAX(IF(Bookings!C:C=A1,Bookings!H:H))` |
| Notes | Text | Blacklist reason, special terms | Manual |

**Validation Rules:**
- Booking_ID: Must be unique
- Customer_Phone: Must be 10 digits (Indian format)
- Dates: Start_Date must be before End_Date
- Advance_Paid: Cannot exceed Total_Amount

---

### 3. n8n Workflow Setup

#### Step 3A: Create OAuth Credentials

1. Log in to [n8n Cloud](https://app.n8n.cloud)
2. Go to **Credentials** → **New Credential**
3. Add two credentials:

**Credential 1: Google Drive OAuth**
- Type: Google Drive
- Name: "Google Drive account"
- Grant access to your Google account

**Credential 2: Groq API Key**
- Type: Header Auth
- Name: "Groq API Auth"
- Headers:
  - Key: `Authorization`
  - Value: `Bearer YOUR_GROQ_API_KEY` (get from [console.groq.com](https://console.groq.com))

#### Step 3B: Create Workflow

1. Create new workflow: **Call Processing — Equipment Leasing**
2. Add the following nodes in order:

---

### Node 1: Google Drive Trigger

```yaml
Type: Google Drive Trigger
Parameters:
  - Trigger On: Changes Involving a Specific Folder
  - Folder: EquipmentLeasing_AI/CallRecordings/ (paste URL)
  - Watch For: File Created
  - Poll Every: 15 Minutes
Credentials: Google Drive account
```

---

### Node 2: Download File

```yaml
Type: Google Drive
Parameters:
  - Operation: Download
  - File: By ID
  - File ID: {{ $json.id }}
Credentials: Google Drive account
```

---

### Node 3: Transcribe Audio (Groq Whisper)

```yaml
Type: HTTP Request
Parameters:
  - Method: POST
  - URL: https://api.groq.com/openai/v1/audio/transcriptions
  - Authentication: Header Auth (Groq API Auth)
  - Body Type: Form-Data
  - Fields:
      - file: {{ $binary.data }} (Binary)
      - model: whisper-large-v3
      - language: ta
      - response_format: text
```

---

### Node 4: Extract Booking Data (Groq LLM)

```yaml
Type: HTTP Request
Parameters:
  - Method: POST
  - URL: https://api.groq.com/openai/v1/chat/completions
  - Authentication: Header Auth (Groq API Auth)
  - Body Type: JSON
```

**JSON Body:**
```json
{
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
      "content": "Extract booking data and return JSON with: is_business_call (bool), confidence (0-100), customer_name, customer_phone, equipment_type, quantity, square_feet, start_date (YYYY-MM-DD), end_date (YYYY-MM-DD), daily_rate, advance_amount, site_location, ambiguous_fields (array), notes.\n\nTranscript:\n{{ $json.data }}"
    }
  ]
}
```

---

### Node 5: Log to Google Sheets

```yaml
Type: Google Sheets
Parameters:
  - Operation: Append Row
  - Document: Master_Database
  - Sheet: Bookings
  - Mapping Mode: Define Below
  - Columns:
      Booking_ID: "{{ 'B' + $now.toMillis() }}"
      Created_At: "{{ $now.toFormat('yyyy-MM-dd HH:mm') }}"
      Source: "Call"
      Customer_Name: "{{ JSON.parse($json.choices[0].message.content).customer_name }}"
      Customer_Phone: "{{ JSON.parse($json.choices[0].message.content).customer_phone }}"
      Equipment_Type: "{{ JSON.parse($json.choices[0].message.content).equipment_type }}"
      Quantity: "{{ JSON.parse($json.choices[0].message.content).quantity }}"
      Square_Feet: "{{ JSON.parse($json.choices[0].message.content).square_feet }}"
      Start_Date: "{{ JSON.parse($json.choices[0].message.content).start_date }}"
      End_Date: "{{ JSON.parse($json.choices[0].message.content).end_date }}"
      Daily_Rate: "{{ JSON.parse($json.choices[0].message.content).daily_rate }}"
      Advance_Paid: "{{ JSON.parse($json.choices[0].message.content).advance_amount }}"
      Payment_Status: "Pending"
      Review_Status: "Pending"
      AI_Confidence: "{{ JSON.parse($json.choices[0].message.content).confidence }}"
      Notes: "{{ JSON.parse($json.choices[0].message.content).notes }}"
Credentials: Google Drive account
```

**Leave Empty (auto-calculated by Sheets):**
- Return_Date
- Days_Booked
- Total_Amount
- Balance_Due

---

### Node 6: Send Telegram Alert

```yaml
Type: Telegram
Parameters:
  - Operation: Send Message
  - Chat ID: {{ env.TELEGRAM_OWNER_CHAT_ID }}
  - Parse Mode: Markdown
  - Text: (see template below)
Credentials: Telegram Bot Token
```

**Message Template:**
```
📊 *NEW BOOKING ALERT*

👤 Customer: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).customer_name }}
📞 Phone: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).customer_phone }}

🏗️ Equipment Details:
   • Type: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).equipment_type }}
   • Quantity: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).quantity }}
   • Area: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).square_feet }} sqft

📅 Duration:
   • From: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).start_date }}
   • To: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).end_date }}

💰 Financial:
   • Daily Rate: ₹{{ JSON.parse($('HTTP Request1').json.choices[0].message.content).daily_rate }}
   • Advance: ₹{{ JSON.parse($('HTTP Request1').json.choices[0].message.content).advance_amount }}

🎯 AI Confidence: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).confidence }}%
⚠️ Issues: {{ JSON.parse($('HTTP Request1').json.choices[0].message.content).ambiguous_fields }}

[View in Sheets](https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID)
```

---

### 4. Telegram Bot Configuration

#### Step 4A: Create Bot via BotFather

1. Open Telegram and search for `@BotFather`
2. Send `/newbot`
3. Follow prompts:
   - Bot name: `Equipment Leasing AI Bot`
   - Bot username: `equipment_leasing_[random]_bot`
4. Copy the **Bot Token** (format: `123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11`)
5. Send message `/mybots` → select your bot → edit default admin
   - Add your user ID as admin

#### Step 4B: Get Your Chat ID

1. Start your bot (click Start button)
2. Send any message to your bot
3. Open URL in browser:
   ```
   https://api.telegram.org/bot{YOUR_TOKEN}/getUpdates
   ```
   Replace `{YOUR_TOKEN}` with your bot token
4. Find your user ID in the response under `message.chat.id`

#### Step 4C: Configure in n8n

In n8n workflow Node 6 (Telegram):
- Create new credential type "Telegram Bot Access Token"
- Paste your bot token
- In Chat ID field, enter your user ID

---

### 5. Phone Recording Setup

#### Step 5A: Configure Samsung Phone

1. Ensure Samsung Call Recorder is enabled:
   - Settings → Apps → Permissions → Record Audio → allowed
2. Make a test call to verify recording works
3. Verify location: `/storage/emulated/0/Recordings/Call/`

#### Step 5B: Configure FolderSync Lite

1. Install **FolderSync Lite** from Google Play Store
2. Open app → **Settings** → Add new sync:
   - **Name:** `CallRecordings`
   - **Type:** Google Drive
   - **Sync type:** One-way (Phone → Drive)
   - **Local folder:** `/storage/emulated/0/Recordings/Call/`
   - **Google Drive folder:** `EquipmentLeasing_AI/CallRecordings/`
   - **Schedule:** Every 15 minutes
   - **Delete source:** Leave unchecked
   - **Notification:** Enable

3. Grant permissions when prompted
4. Test sync: Make a test call, wait 15 minutes, verify file appears in Google Drive

---

## Workflow Documentation

### Data Flow

```
Call Recording → FolderSync → Google Drive → n8n Trigger
                                               ↓
                                        Download File
                                               ↓
                                        Groq Whisper
                                        (Transcribe)
                                               ↓
                                        Groq Llama
                                        (Extract)
                                               ↓
                                        Google Sheets
                                        (Log Data)
                                               ↓
                                        Telegram Alert
                                        (Notify User)
```

### Processing Time

- **FolderSync sync:** 0-15 minutes
- **n8n polling:** 0-15 minutes
- **Groq transcription:** 30 seconds - 2 minutes (depends on audio length)
- **Groq extraction:** 5-10 seconds
- **Google Sheets append:** 2-5 seconds
- **Telegram notification:** 1-3 seconds

**Total end-to-end:** 30 seconds - 35 minutes from call end to dashboard alert

---

## Pricing Models

### Standard Equipment (JCB, Crane, Excavator, Dumper)

```
Total Cost = Daily Rate × Number of Days
Example: ₹5,000/day × 10 days = ₹50,000
```

**Machines Tab Configuration:**
- `Base_Rate`: 5000
- `Rate_Type`: Per Day

---

### Scaffolding Equipment

```
Total Cost = Square Feet × Rate per SqFt/Day × Number of Days
Example: 2,400 sqft × ₹12/sqft/day × 10 days = ₹2,88,000
```

**Machines Tab Configuration:**
- `Base_Rate`: 12
- `Rate_Type`: Per SqFt/Day

**Booking Tab:**
- AI automatically extracts area from phrases like:
  - "2400 square feet"
  - "60 by 40" (= 2,400 sqft)
  - "2400 sqft area"

---

## Operations Guide

### Daily Workflow

1. **Receive Call** → Auto-recorded by Samsung
2. **Monitor Dashboard** → Check Telegram alerts
3. **Review Google Sheets** → Verify extracted data
4. **Confirm or Flag** → Update `Review_Status` column
5. **Track Payments** → Log received amounts in Payments tab

### Manual Data Correction

If AI confidence is low or extraction is ambiguous:

1. Open Google Sheets → Bookings tab
2. Locate booking with `Review_Status: Flagged`
3. Edit fields that need correction
4. Update `AI_Confidence` to reflect manual override
5. Add notes explaining changes
6. Set `Review_Status` to `Confirmed`

### Customer Management

1. Customers auto-populate from Bookings tab
2. Update customer score in Customers tab:
   - **Platinum:** 5+ bookings, 100% payment record
   - **Gold:** 3+ bookings, 95%+ payment record
   - **Risk:** 2+ overdue payments
   - **Blacklist:** Fraudulent or non-payment

3. Add notes for blacklist reason (e.g., "Non-payment of B1741234567")

### Payment Tracking

1. Receive customer payment
2. Open Payments tab
3. Add new row with:
   - Date: Today
   - Booking_ID: Reference booking
   - Amount_Paid: ₹ received
   - Mode: Cash/UPI/NEFT/Cheque
   - Logged_By: Owner
4. Balance_Due auto-updates in Bookings tab

---

## Troubleshooting

### Issue 1: File Not Syncing from Phone

**Symptoms:** Recordings not appearing in Google Drive after 20+ minutes

**Diagnosis:**
1. Check FolderSync is running: Settings → see "CallRecordings" sync
2. Check phone internet connection
3. Check phone storage isn't full

**Solutions:**
```
Option A (Quick Fix):
- Open FolderSync → tap CallRecordings → Sync Now
- Monitor sync status notification

Option B (Restart):
- Force close FolderSync
- Reopen and retry

Option C (Reset):
- FolderSync → Settings → Remove sync
- Follow Setup Step 5B again
```

---

### Issue 2: Groq API 401 Unauthorized

**Symptoms:** Error in n8n Node 3 or 4 - "Authorization failed"

**Root Cause:** Invalid or missing bearer token

**Solution:**
1. Get fresh API key: [console.groq.com](https://console.groq.com/keys)
2. In n8n Credentials → "Groq API Auth"
3. Update Header Auth value: `Bearer YOUR_NEW_KEY` (note the space)
4. Save and retry workflow

**Format Validation:**
- ✅ Correct: `Bearer sk-abcd1234...`
- ❌ Incorrect: `sk-abcd1234...` (missing "Bearer")
- ❌ Incorrect: `Bearersk-abcd1234...` (no space)

---

### Issue 3: Telegram Message Not Received

**Symptoms:** Workflow succeeds but no Telegram notification arrives

**Diagnosis:**
1. Check bot token is correct: [BotFather → /mybots → API Token]
2. Check chat ID format (should be a number, often with leading `-`)

**Solutions:**
```javascript
// Test in n8n Debug mode:
{{ env.TELEGRAM_OWNER_CHAT_ID }}  // Should output a number

// If empty, get chat ID manually:
// 1. Open: https://api.telegram.org/bot{TOKEN}/getUpdates
// 2. Find "chat"."id" value
// 3. Add to n8n env variables
```

---

### Issue 4: LLM Returns Invalid JSON

**Symptoms:** Node 4 fails - "JSON parse error"

**Root Cause:** Response text is not valid JSON (extra characters, incomplete)

**Solution:**
1. Check Groq system prompt forces JSON mode
2. Verify `response_format: { "type": "json_object" }` in request body
3. Add error handler:

```javascript
// In next node after LLM:
{{ JSON.parse($json.choices?.[0].message.content || '{}') }}
```

---

### Issue 5: Sheets Append Fails - Column Not Found

**Symptoms:** Node 5 error - "Column X not found"

**Diagnosis:**
- Spreadsheet columns don't match mapping

**Solution:**
1. Open Master_Database sheet → Bookings tab
2. Verify column headers exactly match (case-sensitive):
   - `Booking_ID` ✅ (not `booking_id` or `BookingID`)
   - `Customer_Name` ✅ (not `customerName` or `Customer Name`)
3. In n8n Node 5, hover over column names to auto-detect
4. Click Refresh Columns button

---

### Issue 6: High CPU Usage / Slow Sync

**Symptoms:** Phone battery drains quickly, phone gets hot

**Solution:**
1. FolderSync → Settings → Reduce sync frequency (30 min instead of 15)
2. Disable "Delete source" option if enabled
3. Uncheck "Background sync" if available
4. Reduce notification frequency

---

## Roadmap

### Phase 2: Intelligence Layer (Q2 2026)

| Feature | Description | Priority |
|---------|-------------|----------|
| **Daily Reports** | Automated 7 AM Telegram summary | High |
| **Payment Alerts** | Alert for overdue (>3 days) | High |
| **Machine Alerts** | Idle machine notifications (>5 days) | Medium |
| **Auto Status Update** | Update machine status on booking dates | Medium |
| **File Management** | Auto-move processed files to /Processed | Low |

---

### Phase 3: Analytics Dashboard (Q3 2026)

- Weekly performance report (Sunday 6 PM)
- Monthly revenue & utilization metrics
- Customer payment trends
- Equipment ROI analysis
- Predictive maintenance alerts

---

### Phase 4: Production Deployment (Q4 2026)

- WhatsApp Business API integration
- Migrate database to Supabase (scalable)
- Web dashboard for real-time monitoring
- Mobile app for field teams
- Payment gateway integration (Razorpay)

---

## Project Info

| Property | Value |
|----------|-------|
| **Project Name** | AI-Automation for Equipment Leasing |
| **Prototype Built** | March 2026 |
| **Primary Stack** | n8n + Google Workspace + Groq API + Telegram |
| **Language Support** | Tamil + English (code-switching) |
| **Target Market** | Equipment leasing, construction industry |
| **Current Phase** | MVP - Core automation pipeline |
| **License** | MIT (open for modification) |
| **Operating Cost** | ₹0/month (prototype) |

---

## Support & Maintenance

### Getting Help

- **Technical Issues:** Check Troubleshooting section above
- **Feature Requests:** Document in Issues section
- **Setup Questions:** Refer to Installation & Setup section

### Maintenance Tasks

| Task | Frequency | Owner |
|------|-----------|-------|
| Check Google Drive quota | Monthly | Admin |
| Monitor Groq API usage | Weekly | Admin |
| Backup Google Sheets | Monthly | Admin |
| Review AI confidence scores | Weekly | Operator |
| Update customer scores | Monthly | Operator |

---

**Last Updated:** March 8, 2026  
**Version:** 1.0 - MVP Release





# AI-Automation-for-an-Equipment-Leasing-Company
🏗 Construction Equipment Leasing — AI Automation Prototype
A low-cost, AI-powered automation system for a construction equipment leasing business in Tamil Nadu, India. Built using n8n, Google Sheets, Groq API, and Telegram.

📋 Table of Contents

- [Business Problem](#business-problem)
- [Solution Overview](#solution-overview)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Google Drive Structure](#google-drive-structure)
- [Google Sheets Database](#google-sheets-database)
- [n8n Workflow](#n8n-workflow)
- [Telegram Bot Setup](#telegram-bot-setup)
- [Phone Recording Setup](#phone-recording-setup)
- [Special Notes — Scaffolding Pricing](#special-notes--scaffolding-pricing)
- [How It Works End-to-End](#how-it-works-end-to-end)
- [Current Status](#current-status)
- [Known Issues & Fixes](#known-issues--fixes)
- [Next Steps](#next-steps)


Business Problem

The owner of a construction equipment leasing business was:
- Logging all bookings manually in a physical diary
- Losing money due to human miscalculations
- Missing delayed payments with no tracking system
- Having no visibility into idle machines
- Running the entire business through phone calls and WhatsApp


Solution Overview

An automated pipeline that:
- Detects new call recordings uploaded to Google Drive
- Transcribes the audio using Groq Whisper (supports Tamil + English)
- Extracts booking information using Groq LLM (Llama 3.3)
- Logs structured data into Google Sheets automatically
- Sends instant Telegram alerts to the owner and employees


Tech Stack

| Tool | Purpose | Cost |
|------|---------|------|
| n8n Cloud | Automation orchestration | Free trial |
| Google Drive | Audio file storage | Free |
| Google Sheets | Database | Free |
| Groq API | Whisper STT + Llama 3.3 LLM | Free tier |
| Telegram Bot | Owner + employee notifications | Free |
| Samsung Call Recorder | Call recording on Android | Free (built-in) |
| FolderSync Lite | Auto-upload recordings to Drive | Free |

**Total running cost:** ₹0 (prototype phase)

System Architecture

```
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
```

Google Drive Structure

```
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
```

**Important:** n8n only watches the root CallRecordings/ folder, not subfolders. Files land here → get processed → move to Processed/.

Google Sheets Database

The Master_Database file has 4 tabs:
Tab 1 — Machines (Yellow)

| Column | Description |

| Column | Description |
|--------|-------------|
| Booking_ID | Auto-generated e.g. B1741234567 |
| Created_At | Timestamp of when row was created |
| Source | Call / WhatsApp / Manual |
| Customer_Name | Extracted by AI |
| Customer_Phone | Extracted by AI |
| Equipment_Type | Extracted by AI |
| Quantity | Number of machines |
| Square_Feet | Only for Scaffolding bookings |
| Start_Date | YYYY-MM-DD |
| End_Date | YYYY-MM-DD |
| Return_Date | Filled manually when machine returns |

| Column | Description |

| Column | Description |
|--------|-------------|
| Customer_Name | Unique customer |
| Phone | Contact number |
| Total_Bookings | Formula — count from Bookings |
| Total_Revenue | Formula — sum from Bookings |
| Total_Paid | Formula — sum from Payments |
| Outstanding | Formula — Revenue minus Paid |
| Score | Platinum / Gold / Risk / Blacklist |
| Last_Booking | Formula — latest booking date |
| Notes | Blacklist reason etc. |
| Payment_ID | P001, P002... |
| Date | Payment date |
| Booking_ID | Links to Bookings tab |
| Customer_Name | Copied from booking |
| Amount_Paid | In ₹ |
| Mode | Cash / UPI / NEFT / Cheque |
| Logged_By | Owner / n8n / Operator |
| Notes | Any notes |
| Daily_Rate | Extracted by AI |
| Total_Amount | Auto-calculated formula |
| Advance_Paid | Extracted by AI |
| Balance_Due | Auto-calculated formula |
| Payment_Status | Pending / Partial / Paid / Overdue |
| Review_Status | Pending / Confirmed / Flagged — owner updates this |
| AI_Confidence | 0-100% confidence of AI extraction |
| Notes | AI flags uncertain fields here |
| Machine_ID | Unique ID e.g. M001 |
| Machine_Name | e.g. JCB 01 |
| Machine_Type | JCB / Crane / Excavator / Dumper / Scaffolding |
| Status | Free / Booked / Maintenance / Idle |
| Operator_Name | Assigned operator |
| Operator_Phone | For future alerts |
| Base_Rate | ₹ per day OR ₹ per sqft/day (for Scaffolding) |
| Rate_Type | Per Day / Per SqFt/Day |
| Idle_Since | Auto-set when status = Free |
| Notes | Manual notes |
Tab 2 — Bookings (Purple)
ColumnDescriptionBooking_IDAuto-generated e.g. B1741234567Created_AtTimestamp of when row was createdSourceCall / WhatsApp / ManualCustomer_NameExtracted by AICustomer_PhoneExtracted by AIEquipment_TypeExtracted by AIQuantityNumber of machinesSquare_FeetOnly for Scaffolding bookingsStart_DateYYYY-MM-DDEnd_DateYYYY-MM-DDReturn_DateFilled manually when machine returnsDays_BookedAuto-calculated formulaDaily_RateExtracted by AITotal_AmountAuto-calculated formulaAdvance_PaidExtracted by AIBalance_DueAuto-calculated formulaPayment_StatusPending / Partial / Paid / OverdueReview_StatusPending / Confirmed / Flagged — owner updates thisAI_Confidence0-100% confidence of AI extractionNotesAI flags uncertain fields here
Tab 3 — Payments (Green)
ColumnDescriptionPayment_IDP001, P002...DatePayment dateBooking_IDLinks to Bookings tabCustomer_NameCopied from bookingAmount_PaidIn ₹ModeCash / UPI / NEFT / ChequeLogged_ByOwner / n8n / OperatorNotesAny notes
Tab 4 — Customers (Blue)
ColumnDescriptionCustomer_NameUnique customerPhoneContact numberTotal_BookingsFormula — count from BookingsTotal_RevenueFormula — sum from BookingsTotal_PaidFormula — sum from PaymentsOutstandingFormula — Revenue minus PaidScorePlatinum / Gold / Risk / BlacklistLast_BookingFormula — latest booking dateNotesBlacklist reason etc.

n8n Workflow

**Workflow Name:** Call Processing — Equipment Leasing

**Node 1 — Google Drive Trigger**
- **Trigger On:** Changes Involving a Specific Folder
- **Folder:** `EquipmentLeasing_AI/CallRecordings/` (by URL)
- **Watch For:** File Created
- **Poll Every:** 15 Minutes

**Node 2 — Download File**
- **Resource:** File
- **Operation:** Download
- **File:** By ID → `{{ $json.id }}`

**Node 3 — HTTP Request (Groq Whisper)**
- **Method:** POST
- **URL:** `https://api.groq.com/openai/v1/audio/transcriptions`
- **Authentication:** Header Auth
- **Headers:**
  - `Authorization: Bearer YOUR_GROQ_API_KEY`
- **Body Type:** Form-Data
- **Fields:**
  - `file` → `{{ $binary.data }}` → n8n Binary File
  - `model` → `whisper-large-v3`
  - `language` → `ta`
  - `response_format` → `text`

**Node 4 — HTTP Request1 (Groq LLM)**
- **Method:** POST
- **URL:** `https://api.groq.com/openai/v1/chat/completions`
- **Authentication:** Same Header Auth account
- **Body Type:** JSON
- **Body:**
```json
{
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
```

**Node 5 — Append Row in Sheet**
- **Operation:** Append Row
- **Document:** Master_Database
- **Sheet:** Bookings
- **Mapping:** Manual
- **Note:** All expressions use: `{{ JSON.parse($json.choices[0].message.content).field_name }}`
- **Leave empty:** Return_Date, Days_Booked, Total_Amount, Balance_Due (Sheets formulas handle these)

**Node 6 — Telegram (Send Message)**
- **Credential:** Telegram Bot Token
- **Operation:** Send Message
- **Chat ID:** Owner's Chat ID
- **Parse Mode:** Markdown (legacy)
- **Text:**
```
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
```

**For multiple employees:** Add separate Telegram nodes in parallel after Node 5, each with a different Chat ID.

Telegram Bot Setup

1. Open Telegram → search `@BotFather`
2. Send `/newbot` → follow instructions
3. Copy the Bot Token given by BotFather
4. Open your bot → press Start → send any message
5. Get Chat ID from:
   ```
   https://api.telegram.org/bot{YOUR_TOKEN}/getUpdates
   ```
6. Copy the "id" number inside the "chat" object

**Note:** If you get `{"ok":false,"error_code":404}` — the token in the URL is wrong. Go to BotFather → `/mybots` → select your bot → API Token to get a fresh copy.

Phone Recording Setup

**Device:** Samsung Android (built-in call recorder)

**Auto-upload setup:**

1. Install FolderSync Lite from Play Store
2. Link your Google account
3. Phone folder: `/storage/emulated/0/Recordings/Call/`
4. Drive destination: `EquipmentLeasing_AI/CallRecordings/`
5. Sync type: One way (phone → Drive)
6. Schedule: Every 15 minutes

**Important:** First verify that Samsung's built-in recorder captures both sides of the call clearly before relying on this pipeline.

Special Notes — Scaffolding Pricing

Scaffolding uses a different pricing model from other equipment:

| Equipment | Pricing Formula |
|-----------|-----------------|
| JCB, Crane, Excavator, Dumper | Daily Rate × Days |
| Scaffolding | Square Feet × Rate per SqFt × Days |

**Example:**
- 2400 sqft × ₹12/sqft/day × 10 days = ₹2,88,000

**In Machines tab:**
- `Base_Rate` = rate per sqft per day (e.g. 12)
- `Rate_Type` = Per SqFt/Day

**In Bookings tab:**
- `Square_Feet` column captures the area from the call
- AI extracts area mentions like "2400 square feet", "60×40 area" etc.


1. Owner receives a business call on his Samsung phone
2. Samsung auto-records the call
3. FolderSync uploads the recording to Google Drive every 15 min
4. n8n detects the new file and triggers the workflow
5. Groq Whisper transcribes the Tamil/English audio to text
6. Groq Llama 3.3 extracts structured booking data as JSON
7. n8n appends a new row to the Bookings sheet with `Review_Status: Pending`
8. Owner and employees receive a Telegram alert with booking summary
9. Owner opens Google Sheets and changes `Review_Status` to Confirmed or Flagged
10. Owner and employees receive a Telegram alert with booking summary
Owner opens Google Sheets and changes Review_Status to Confirmed or Flagged
If flagged → owner manually corrects the row


Current Status

| Component | Status |
|-----------|--------|

**Issue 1 — Groq API 401 Unauthorized**
- **Cause:** Bearer prefix missing in Header Auth credential
- **Fix:** Value must be exactly `Bearer YOUR_API_KEY` with a space between Bearer and the key

**Issue 2 — LLM returns string not object**
- **Cause:** `$json.choices[0].message.content` is a string, not a parsed object
- **Fix:** Wrap all expressions with `JSON.parse()`:
  ```
  {{ JSON.parse($json.choices[0].message.content).field_name }}
  ```

**Issue 3 — Telegram 404 Not Found**
- **Cause:** Wrong bot token in the getUpdates URL
- **Fix:**de 5 — Google Sheets | ⏳ Pending test |
| Node 6 — Telegram Alert | ⏳ Pending test |
| FolderSync auto-upload | ⏳ Not configured yet |
| Full end-to-end test | ⏳ Pending |

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

**Phase 2 — Intelligence Layer**
- Daily 7AM report via Telegram (Cron node)
- Payment overdue alerts (>3 days)
- Idle machine alerts (>5 days)
- Machine status auto-update when booking starts/ends
- Move processed audio files to /Processed folder after workflow runs

**Phase 3 — Analytics**
- Weekly performance report (Sunday 6PM)
- Monthly business intelligence report
- Customer scoring (Platinum / Gold / Risk / Blacklist)

**Phase 4 — Production**
- Switch from Telegram to WhatsApp Business API
- Set up FolderSync for fully automatic recording pipeline
- Migrate from Google Sheets to Supabase
- Set up FolderSync for fully automatic recording pipeline
 Migrate from Google Sheets to Supabase
 Build simple web dashboard


Prototype built: March 2026
Stack: n8n Cloud + Google Drive + Google Sheets + Groq API + Telegram
Language support: Tamil + English (code-switching)
