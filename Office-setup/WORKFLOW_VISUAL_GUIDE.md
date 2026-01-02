# Visual Workflow Guide - Car Detailing Daily Email Campaign

## 🎨 Workflow Overview

This visual guide helps you understand how the workflow processes data and sends emails.

---

## 📊 High-Level Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    DAILY EMAIL WORKFLOW                     │
│                                                             │
│  INPUT: Google Sheet       →  PROCESS: Loop & Send  →  OUTPUT: Emails + Logs │
│  (Business List)                (Validate & Personalize)     (Email Log Sheet) │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔄 Detailed Node-by-Node Flow

### Phase 1: Initialization (Nodes 1-4)

```
┏━━━━━━━━━━━━━━━━━━━━━━┓
┃ 1. Daily Schedule    ┃  ⏰ Triggers at 9:00 AM every day
┃    (9 AM Trigger)    ┃  📅 Cron: 0 9 * * *
┗━━━━━━━━━━━┳━━━━━━━━━━┛
            │
            ▼
┏━━━━━━━━━━━━━━━━━━━━━━┓
┃ 2. Read Business     ┃  📄 Reads Google Sheet
┃    List              ┃  📊 Columns: Email, Business Owner Name
┗━━━━━━━━━━━┳━━━━━━━━━━┛  🔄 Fresh data each run
            │
            ▼
┏━━━━━━━━━━━━━━━━━━━━━━┓
┃ 3. Add Batch         ┃  🏷️  Adds metadata:
┃    Metadata          ┃    • timestamp
┗━━━━━━━━━━━┳━━━━━━━━━━┛    • batchId (20240115_090000)
            │              • totalBusinesses count
            ▼
┏━━━━━━━━━━━━━━━━━━━━━━┓
┃ 4. Log Batch Start   ┃  📝 Writes to Email Log sheet
┗━━━━━━━━━━━┳━━━━━━━━━━┛  ✓ Records workflow start time
            │
            ▼
```

### Phase 2: Main Processing Loop (Nodes 5-12)

```
            │
            ▼
    ┏━━━━━━━━━━━━━━━━━━━━━━━┓
    ┃ 5. Loop Over          ┃  🔁 Split In Batches
    ┃    Businesses         ┃  📦 Batch Size: 1 (one at a time)
    ┗━━━━━━━━━━┳━━━━━━━━━━━━┛  ⚙️  Maintains loop state
               │
               ▼
    ┏━━━━━━━━━━━━━━━━━━━━━━━┓
    ┃ 6. Validate Data      ┃  ✓ Check Email not empty
    ┗━━━━━━━━━┳━━━━━━━━━━━━━┛  ✓ Check Business Owner Name not empty
               │
               ├─────────────────────┐
               │                     │
           ✅ VALID              ❌ INVALID
               │                     │
               ▼                     ▼
    ┏━━━━━━━━━━━━━━━━━━┓   ┏━━━━━━━━━━━━━━━━━━┓
    ┃ 7. Prepare Email ┃   ┃ 10. Log Skipped  ┃
    ┃    Template      ┃   ┗━━━━━━━━┳━━━━━━━━━┛
    ┗━━━━━━┳━━━━━━━━━━━┛            │
           │                        │
           │  Creates:              │  Logs:
           │  • emailSubject        │  • status: skipped
           │  • emailBody           │  • reason message
           │  • recipientEmail      │
           │  • businessOwnerName   │
           │                        │
           ▼                        │
    ┏━━━━━━━━━━━━━━━━━━┓            │
    ┃ 8. Send Email    ┃            │
    ┃    (Gmail)       ┃            │
    ┗━━━━━━┳━━━━━━━━━━━┛            │
           │                        │
       SUCCESS                      │
           │                        │
           ▼                        │
    ┏━━━━━━━━━━━━━━━━━━┓            │
    ┃ 9. Log Success   ┃            │
    ┗━━━━━━┳━━━━━━━━━━━┛            │
           │                        │
           └────────┬───────────────┘
                    │
                    ▼
         ┏━━━━━━━━━━━━━━━━━━━━━┓
         ┃ 11. Write Log       ┃  📝 Append to Email Log sheet
         ┃     to Sheet        ┃  💾 Status: success/error/skipped
         ┗━━━━━━━━━━┳━━━━━━━━━━┛
                    │
                    ▼
         ┏━━━━━━━━━━━━━━━━━━━━━┓
         ┃ 12. Wait (Rate      ┃  ⏱️  Delay: 2 seconds
         ┃     Limiting)       ┃  🛡️  Prevents spam filters
         ┗━━━━━━━━━━┳━━━━━━━━━━┛
                    │
                    │
                    └────────────────┐
                                     │
                         ◀───────────┘ Loop continues for next business
                                     │
                                     │ (when all businesses processed)
                                     ▼
                          ┏━━━━━━━━━━━━━━━━┓
                          ┃ 13. Complete   ┃
                          ┗━━━━━━━━━━━━━━━━┛
```

### Error Handling Path (Nodes 14-15)

```
    ┏━━━━━━━━━━━━━━━━━━┓
    ┃ 8. Send Email    ┃
    ┗━━━━━━┳━━━━━━━━━━━┛
           │
       ❌ ERROR
           │
           ▼
    ┏━━━━━━━━━━━━━━━━━━━━━┓
    ┃ 14. Handle Send     ┃  ⚠️  Catches email errors
    ┃     Error           ┃  📋 Extracts error message
    ┗━━━━━━━━━━┳━━━━━━━━━━┛
               │
               ▼
    ┏━━━━━━━━━━━━━━━━━━━━━┓
    ┃ 15. Log Error       ┃  📝 Logs:
    ┗━━━━━━━━━━┳━━━━━━━━━━┛    • status: error
               │                • error message
               │                • timestamp
               │
               └────────────────▶ To "Write Log to Sheet"
```

---

## 📋 Data Flow Example

### Input Data (Google Sheet)

```
Row 1 (Header):
┌────────────────────────────┬────────────────────┐
│ Email                      │ Business Owner Name│
├────────────────────────────┼────────────────────┤
│ john@detailing.com         │ John               │
│ sarah@autoshine.com        │ Sarah              │
│ mike@carcare.com           │ Mike               │
│ (empty)                    │ (empty)            │ ← Will be skipped
└────────────────────────────┴────────────────────┘
```

### Processing Row 1 (John)

```
Step 1: Read Data
  ↓
  {
    "Email": "john@detailing.com",
    "Business Owner Name": "John"
  }

Step 2: Validate
  ↓
  ✓ Email: john@detailing.com (valid)
  ✓ Business Owner Name: John (valid)
  → PASS to email preparation

Step 3: Prepare Template
  ↓
  {
    "emailSubject": "Missed calls = missed car detailing bookings",
    "emailBody": "Hi John,\n\nQuick question — what happens...",
    "recipientEmail": "john@detailing.com",
    "businessOwnerName": "John"
  }

Step 4: Send Email
  ↓
  To: john@detailing.com
  From: your-email@gmail.com
  Subject: Missed calls = missed car detailing bookings
  Body: Hi John, [personalized content]
  
  → ✅ SUCCESS

Step 5: Log Result
  ↓
  {
    "status": "success",
    "email": "john@detailing.com",
    "businessOwnerName": "John",
    "sentAt": "2024-01-15T09:00:23.456Z",
    "message": "Email sent successfully"
  }

Step 6: Write to Log Sheet
  ↓
  Appends row to Email Log sheet

Step 7: Wait 2 seconds
  ↓
  (prevents spam filters)

Step 8: Loop to next business (Sarah)
```

### Processing Row 4 (Empty Row)

```
Step 1: Read Data
  ↓
  {
    "Email": "",
    "Business Owner Name": ""
  }

Step 2: Validate
  ↓
  ❌ Email: (empty)
  ❌ Business Owner Name: (empty)
  → FAIL validation → Skip to Log Skipped

Step 3: Log Skipped
  ↓
  {
    "status": "skipped",
    "email": "N/A",
    "businessOwnerName": "N/A",
    "sentAt": "2024-01-15T09:00:35.789Z",
    "message": "Skipped - Missing email or business owner name"
  }

Step 4: Write to Log Sheet
  ↓
  Appends row to Email Log sheet

Step 5: Continue to next business
```

---

## 📊 Data Transformation Journey

### Raw Input → Processed Output

```
┌─────────────────────────────────────────────────────────────────────┐
│                         DATA TRANSFORMATION                         │
└─────────────────────────────────────────────────────────────────────┘

INPUT (Google Sheet Row):
┌──────────────────────────────────────┐
│ Email: john@detailing.com            │
│ Business Owner Name: John            │
└──────────────────────────────────────┘
                │
                ▼
        [Add Metadata]
                │
                ▼
┌──────────────────────────────────────┐
│ Email: john@detailing.com            │
│ Business Owner Name: John            │
│ timestamp: 2024-01-15T09:00:00Z      │
│ batchId: 20240115_090000             │
│ totalBusinesses: 50                  │
└──────────────────────────────────────┘
                │
                ▼
        [Prepare Template]
                │
                ▼
┌──────────────────────────────────────┐
│ emailSubject: "Missed calls..."      │
│ emailBody: "Hi John, Quick..."       │
│ recipientEmail: john@detailing.com   │
│ businessOwnerName: John              │
└──────────────────────────────────────┘
                │
                ▼
          [Send Email]
                │
                ▼
┌──────────────────────────────────────┐
│ status: success                      │
│ email: john@detailing.com            │
│ businessOwnerName: John              │
│ sentAt: 2024-01-15T09:00:23.456Z     │
│ message: Email sent successfully     │
└──────────────────────────────────────┘
                │
                ▼
OUTPUT (Email Log Sheet Row):
┌──────────────────────────────────────┐
│ success | john@detailing.com | John  │
│ | 2024-01-15T09:00:23.456Z           │
│ | Email sent successfully            │
└──────────────────────────────────────┘
```

---

## 🎯 Decision Points in Workflow

### 1. Data Validation Decision

```
            [Validate Data]
                  │
          ┌───────┴───────┐
          │               │
      Is Email         Is Business
      NOT empty?       Owner Name
          │            NOT empty?
          │               │
          └───────┬───────┘
                  │
           ┌──────┴──────┐
           │             │
        ✓ YES          ❌ NO
           │             │
    [Proceed to     [Log as
     Send Email]     Skipped]
```

### 2. Email Send Result Decision

```
       [Send Email Attempt]
               │
        ┌──────┴──────┐
        │             │
     ✅ Success    ❌ Error
        │             │
        │             │
   [Log Success] [Log Error]
```

---

## 📈 Execution Timeline

### Single Email Process (Approximately 3-4 seconds)

```
Time (seconds)    Action
───────────────────────────────────────────────────────
0.0               Read business data from sheet
0.2               Validate data
0.3               Prepare email template
0.5               Send email via Gmail API
1.5               Receive send confirmation
1.6               Log success to sheet
2.0               Write log to Google Sheet
3.0               Wait (rate limiting)
4.0               ▶ Ready for next business
```

### Full Workflow Execution (50 businesses)

```
Timeline for 50 Businesses:
─────────────────────────────────────────
09:00:00  ▶ Workflow triggered (daily schedule)
09:00:01  ▶ Read 50 businesses from sheet
09:00:02  ▶ Add batch metadata
09:00:03  ▶ Log batch start

09:00:05  ▶ Process Business #1
09:00:09  ▶ Process Business #2
09:00:13  ▶ Process Business #3
...
09:03:21  ▶ Process Business #50

09:03:25  ▶ Workflow complete
─────────────────────────────────────────
Total Time: ~3 minutes 25 seconds
```

---

## 🔄 Loop Mechanics

### Split In Batches Node Behavior

```
INPUT: Array of 5 businesses
┌────────────────────────────────────────────┐
│ [                                          │
│   { Email: "john@...", Name: "John" },     │
│   { Email: "sarah@...", Name: "Sarah" },   │
│   { Email: "mike@...", Name: "Mike" },     │
│   { Email: "", Name: "" },                 │
│   { Email: "lisa@...", Name: "Lisa" }      │
│ ]                                          │
└────────────────────────────────────────────┘
                │
                ▼
        [Split In Batches]
        Batch Size: 1
                │
┌───────────────┼───────────────┐
│               │               │
▼               ▼               ▼
Iteration 1  Iteration 2  Iteration 3  ...
┌─────────┐  ┌─────────┐  ┌─────────┐
│ John    │  │ Sarah   │  │ Mike    │
└─────────┘  └─────────┘  └─────────┘
    │            │            │
    ▼            ▼            ▼
 Process      Process      Process
    │            │            │
    ▼            ▼            ▼
  Wait 2s      Wait 2s      Wait 2s
    │            │            │
    └────────────┴────────────┘
                │
        Loop continues ◀───┘
```

---

## 📝 Logging Flow

### Comprehensive Activity Tracking

```
┌─────────────────────────────────────────────────────┐
│              LOGGING ARCHITECTURE                   │
└─────────────────────────────────────────────────────┘

Before Loop:
    │
    ├─▶ Log Batch Start
    │       ↓
    │   [Writes to Email Log]
    │   • Batch ID
    │   • Start timestamp
    │   • Total businesses count
    │
During Loop (each business):
    │
    ├─▶ Success Path
    │       ↓
    │   [Log Success Node]
    │       ↓
    │   [Write Log to Sheet]
    │   • status: success
    │   • email
    │   • businessOwnerName
    │   • sentAt timestamp
    │   • message
    │
    ├─▶ Skipped Path
    │       ↓
    │   [Log Skipped Node]
    │       ↓
    │   [Write Log to Sheet]
    │   • status: skipped
    │   • reason message
    │
    └─▶ Error Path
            ↓
        [Log Error Node]
            ↓
        [Write Log to Sheet]
        • status: error
        • error details

Result: Complete audit trail in Google Sheets
```

---

## 🎨 Visual Workflow Snapshot

### Node Layout (as seen in n8n)

```
                                TOP OF CANVAS
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   [Daily Schedule]  →  [Read Business List]                │
│                                                             │
│   ↓                                                         │
│                                                             │
│   [Add Batch Metadata]  →  [Log Batch Start]               │
│                                                             │
│   ↓                                                         │
│                                                             │
│   [Loop Over Businesses]  ←──────────┐                     │
│                                      │                     │
│   ↓                                  │                     │
│                                      │                     │
│   [Validate Data]                    │                     │
│   ├── Valid   ──→  [Prepare Email]   │                     │
│   │                      ↓            │                     │
│   │                [Send Email]       │                     │
│   │                      ↓            │                     │
│   │                [Log Success] ──┐  │                     │
│   │                               │  │                     │
│   └── Invalid ──→  [Log Skipped]──┤  │                     │
│                                   │  │                     │
│                        [Write Log to Sheet]                │
│                                   │  │                     │
│                              [Wait 2s]──┘                   │
│                                                             │
│                         [Complete]                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                               BOTTOM OF CANVAS
```

---

## 🔍 Debugging Visual Guide

### How to Track Issues

```
┌────────────────────────────────────────────────────────┐
│              DEBUGGING CHECKLIST                       │
└────────────────────────────────────────────────────────┘

1. Check Schedule Trigger
   ┌─────────────────────┐
   │ Daily Schedule      │  ← Is it activated?
   │ (9 AM)              │  ← Is the time correct?
   └─────────────────────┘

2. Verify Sheet Read
   ┌─────────────────────┐
   │ Read Business List  │  ← Credential connected?
   │                     │  ← Sheet ID correct?
   └─────────────────────┘  ← Data visible in output?

3. Check Data Validation
   ┌─────────────────────┐
   │ Validate Data       │  ← Are emails/names present?
   │                     │  ← Which path is taken?
   └─────────────────────┘

4. Monitor Email Sending
   ┌─────────────────────┐
   │ Send Email (Gmail)  │  ← Credential authorized?
   │                     │  ← From email set correctly?
   └─────────────────────┘  ← Check execution error?

5. Review Logs
   ┌─────────────────────┐
   │ Write Log to Sheet  │  ← Logs appearing in sheet?
   │                     │  ← Status values correct?
   └─────────────────────┘
```

---

## 📊 Success/Failure Patterns

### Visual Status Guide

```
SUCCESSFUL EXECUTION:
┌──────────────────────────────────────┐
│ All Nodes: ✅ Green Checkmarks       │
│                                      │
│ ✅ → ✅ → ✅ → ✅ → ✅ → ✅ → ✅        │
│                                      │
│ Email Log: All "success" entries     │
└──────────────────────────────────────┘

PARTIAL FAILURE (some skipped):
┌──────────────────────────────────────┐
│ Some Nodes: ⚠️  Yellow/Orange         │
│                                      │
│ ✅ → ✅ → ⚠️  → ⚠️  → ✅ → ✅ → ✅     │
│                                      │
│ Email Log: Mix of "success" &        │
│            "skipped" entries         │
└──────────────────────────────────────┘

COMPLETE FAILURE:
┌──────────────────────────────────────┐
│ Early Node: ❌ Red X                 │
│                                      │
│ ✅ → ❌ (workflow stops)              │
│                                      │
│ Check: Credentials, Sheet access     │
└──────────────────────────────────────┘
```

---

## 🚀 Performance Visualization

### Email Throughput

```
RATE LIMITING IMPACT
────────────────────────────────────────────────
Without Rate Limiting (⚠️  Risky):
│▓│▓│▓│▓│▓│▓│▓│▓│▓│▓│ = 10 emails in 5 seconds
                        → High spam risk

With 2s Rate Limiting (✅ Safe):
│▓│  │▓│  │▓│  │▓│  │▓│  │ = 5 emails in 10 seconds
                              → Better deliverability

With 5s Rate Limiting (🔐 Very Safe):
│▓│    │▓│    │▓│    │ = 3 emails in 15 seconds
                        → Maximum deliverability
────────────────────────────────────────────────
```

---

## 💡 Key Takeaways

### Visual Summary

```
┌─────────────────────────────────────────────────┐
│  WORKFLOW CHARACTERISTICS                       │
├─────────────────────────────────────────────────┤
│  ⏰ Trigger      │ Daily at 9 AM               │
│  📊 Data Source  │ Google Sheets              │
│  🔁 Processing   │ One-by-one loop            │
│  ✉️  Sending      │ Gmail or SendGrid          │
│  📝 Logging      │ Google Sheets (Email Log)  │
│  ⏱️  Rate Limit  │ 2 seconds between emails   │
│  ✅ Validation   │ Email & Name required      │
│  🛡️  Error Handling │ Comprehensive logs      │
└─────────────────────────────────────────────────┘
```

---

**This visual guide complements the technical documentation and helps you understand the workflow at a glance.**

For detailed setup instructions, see:
- [Quick Start Guide](./QUICK_START_GUIDE.md)
- [Full Documentation](./CAR_DETAILING_EMAIL_WORKFLOW_README.md)
