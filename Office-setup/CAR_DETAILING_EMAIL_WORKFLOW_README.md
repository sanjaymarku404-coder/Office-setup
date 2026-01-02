# Car Detailing Daily Email Campaign Workflow

## Overview
This n8n workflow automatically sends personalized daily emails to car detailing businesses from a Google Sheets list. It includes comprehensive error handling, logging, rate limiting, and duplicate prevention.

## Features
- ✅ **Daily Schedule**: Runs automatically at 9 AM every day
- ✅ **Google Sheets Integration**: Reads business list dynamically
- ✅ **Personalized Emails**: Uses {{Business Owner Name}} template variable
- ✅ **Error Handling**: Validates data and logs errors
- ✅ **Comprehensive Logging**: Tracks sent/failed/skipped emails
- ✅ **Rate Limiting**: 2-second delay between emails to avoid spam filters
- ✅ **Duplicate Prevention**: Batch ID tracking for each run

## Prerequisites

### 1. Google Sheets Setup
Create a Google Sheet with two sheets:

#### Sheet 1: "Business List" (Main Data)
| Email | Business Owner Name |
|-------|-------------------|
| john@detailing.com | John Smith |
| sarah@autocare.com | Sarah Johnson |
| mike@carshine.com | Mike Williams |

**Columns:**
- `Email` (required): Business email address
- `Business Owner Name` (required): Owner's first name or full name

#### Sheet 2: "Email Log" (For Tracking)
| status | email | businessOwnerName | sentAt | message |
|--------|-------|-------------------|--------|---------|
| success | john@detailing.com | John Smith | 2024-01-15T09:00:00 | Email sent successfully |

**Columns:**
- `status`: success, error, or skipped
- `email`: Recipient email
- `businessOwnerName`: Business owner name
- `sentAt`: ISO timestamp
- `message`: Status message

### 2. n8n Setup

#### Required Credentials
1. **Google Sheets OAuth2 API**
   - Navigate to n8n Credentials
   - Add "Google Sheets OAuth2 API"
   - Authorize with your Google account
   - Grant access to Google Sheets

2. **Gmail OAuth2** (or alternative email service)
   - Add "Gmail OAuth2" credential
   - Authorize with your sending email account
   - **Important**: Use a dedicated sending account, not your primary email

#### Alternative Email Services
Instead of Gmail, you can use:
- **SendGrid**: Better for bulk emails, higher deliverability
- **SMTP**: For custom email servers
- **Amazon SES**: Cost-effective for large volumes

## Installation Steps

### Step 1: Import Workflow
1. Open n8n
2. Click "Add Workflow" → "Import from File"
3. Select `Car_Detailing_Daily_Email.json`
4. Workflow will be imported in **inactive** state

### Step 2: Configure Google Sheets
1. Click on the **"Read Business List"** node
2. In the "Document ID" field:
   - Click "Select from list"
   - Choose your Google Sheet
3. Set "Sheet Name" to your main sheet (default: Sheet1)
4. Save the node

### Step 3: Configure Email Logging
1. Click on **"Log Batch Start"** node
2. Set Document ID to the same Google Sheet
3. Set Sheet Name to "Email Log"
4. Repeat for **"Write Log to Sheet"** node

### Step 4: Configure Email Sender
1. Click on **"Send Email (Gmail)"** node
2. Update the "From Email" field to your sending email
3. Add Gmail OAuth2 credentials
4. Test the connection

### Step 5: Adjust Schedule (Optional)
The workflow runs daily at 9:00 AM by default.

To change the time:
1. Click **"Daily Schedule (9 AM)"** node
2. Modify the cron expression:
   - `0 9 * * *` = 9:00 AM daily
   - `0 14 * * *` = 2:00 PM daily
   - `0 8 * * 1-5` = 8:00 AM Monday-Friday only

### Step 6: Test the Workflow

#### Manual Test (Recommended First)
1. Add 2-3 test records to your Google Sheet
2. Click "Test Workflow" in n8n
3. Verify emails are received
4. Check the "Email Log" sheet for entries

#### Production Activation
1. After successful testing, click "Active" toggle
2. The workflow will now run automatically

## Workflow Logic

### Process Flow
```
┌─────────────────────┐
│ Daily Schedule      │
│ (9 AM Trigger)      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Read Business List  │
│ (Google Sheets)     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Add Batch Metadata  │
│ (Timestamp, ID)     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Log Batch Start     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Loop Over Businesses│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Validate Data       │
│ (Check Email/Name)  │
└──────┬──────┬───────┘
       │      │
   Valid│     │Invalid
       │      │
       ▼      ▼
   ┌─────┐  ┌──────────┐
   │Send │  │Log       │
   │Email│  │Skipped   │
   └──┬──┘  └────┬─────┘
      │          │
      ▼          │
   ┌─────────┐   │
   │Log      │   │
   │Success  │   │
   └────┬────┘   │
        │        │
        └────┬───┘
             │
             ▼
      ┌────────────┐
      │Write Log   │
      │to Sheet    │
      └─────┬──────┘
            │
            ▼
      ┌────────────┐
      │Wait 2s     │
      │(Rate Limit)│
      └─────┬──────┘
            │
            └──► Loop continues
```

### Data Validation
The workflow validates each record before sending:
- ✓ Email field is not empty
- ✓ Business Owner Name is not empty
- ✗ If validation fails, logs as "skipped"

### Error Handling
- **Missing Data**: Skipped and logged
- **Email Send Failure**: Caught, logged with error message
- **Sheet Read Failure**: Workflow stops, error notification sent

## Email Template

### Subject
```
Missed calls = missed car detailing bookings
```

### Body
```
Hi {{Business Owner Name}},

Quick question — what happens when a customer calls while you're busy detailing a car?
Most detailing businesses lose bookings simply because no one answers instantly.

I've built an **AI receptionist** made specifically for car detailers.
It answers calls & WhatsApp messages, books appointments, shares pricing, and follows up — 24/7.
No hiring, no apps, works with your current number.

If this could bring you more bookings this month,
can I show you a quick 10-minute demo?

– Sanjay
```

### Customizing the Template
To modify the email content:
1. Open **"Prepare Email Template"** node
2. Edit the `emailSubject` field for the subject line
3. Edit the `emailBody` field for the email content
4. Use `{{ $json['Business Owner Name'] }}` for personalization

## Monitoring & Logging

### Check Email Logs
All email activities are logged in the "Email Log" sheet:

**Success Example:**
```
status: success
email: john@detailing.com
businessOwnerName: John Smith
sentAt: 2024-01-15T09:00:23.456Z
message: Email sent successfully
```

**Skipped Example:**
```
status: skipped
email: N/A
businessOwnerName: 
sentAt: 2024-01-15T09:00:25.789Z
message: Skipped - Missing email or business owner name
```

**Error Example:**
```
status: error
email: invalid@example.com
businessOwnerName: Jane Doe
sentAt: 2024-01-15T09:00:30.123Z
message: Invalid email address
```

### View Execution History
1. Go to n8n Executions panel
2. Filter by workflow name
3. Review each execution's details
4. Check input/output data for each node

## Duplicate Prevention

### Daily Batches
Each workflow run creates a unique batch ID:
```
batchId: 20240115_090000
```

This ensures:
- Each day's emails are tracked separately
- You can identify which run sent each email
- Historical tracking for compliance

### Preventing Double-Sends
To prevent sending to the same business twice in one day:

**Option 1: Manual Tracking**
- Check "Email Log" sheet before adding new businesses
- Filter by today's date

**Option 2: Advanced Logic (Custom)**
Add a node before "Send Email" that:
1. Reads Email Log sheet
2. Filters entries from today
3. Compares current email against today's sent emails
4. Skips if already sent

## Rate Limiting

The workflow includes a 2-second delay between emails to:
- Avoid triggering spam filters
- Comply with email service rate limits
- Maintain sender reputation

**Adjust Rate Limit:**
1. Click **"Wait (Rate Limiting)"** node
2. Change "Amount" value:
   - `1` second for faster sending (riskier)
   - `5` seconds for safer sending (recommended for cold outreach)

## Troubleshooting

### Emails Not Sending

**Check 1: Credentials**
- Verify Gmail OAuth2 credentials are authorized
- Test by sending a manual email from n8n

**Check 2: Google Sheets Access**
- Ensure the sheet is accessible by the authenticated account
- Check sheet names match exactly (case-sensitive)

**Check 3: Data Format**
- Column names must match exactly: "Email", "Business Owner Name"
- No extra spaces in column headers

### Emails Going to Spam

**Solutions:**
1. **Warm up your sending domain**
   - Start with 10-20 emails per day
   - Gradually increase volume over 2 weeks

2. **Use a custom domain**
   - Avoid free Gmail accounts for bulk sending
   - Set up SPF, DKIM, DMARC records

3. **Use SendGrid or professional email service**
   - Better deliverability
   - Built-in spam prevention

### Wrong Personalization

**Check Template Syntax:**
- Use `{{ $json['Business Owner Name'] }}` exactly
- Column name in sheet must match exactly
- No typos in column names

**Common Mistakes:**
- ❌ `{{Business Owner Name}}` (missing spaces)
- ❌ `{{ $json.BusinessOwnerName }}` (wrong format)
- ✅ `{{ $json['Business Owner Name'] }}` (correct)

### Sheet Not Found Error

**Fix:**
1. Open "Read Business List" node
2. Click "Select from list" for Document ID
3. Refresh the list
4. Re-select your sheet

## Best Practices

### 1. Start Small
- Test with 5-10 businesses first
- Verify deliverability and formatting
- Scale gradually

### 2. Monitor Responses
- Track reply rates in the Email Log sheet
- Adjust email copy based on feedback
- A/B test different subject lines

### 3. Compliance
- Include unsubscribe link (optional node can be added)
- Only email businesses you have permission to contact
- Follow CAN-SPAM and GDPR regulations

### 4. Backup Data
- Export your Google Sheet regularly
- Keep a copy of the workflow JSON
- Document any customizations

### 5. Maintain List Quality
- Remove bounced emails promptly
- Update names and emails regularly
- Archive successful conversions

## Advanced Customizations

### Add Unsubscribe Link
1. Edit "Prepare Email Template" node
2. Add to email body:
   ```
   ---
   Don't want to receive these emails? [Click here to unsubscribe](https://yoursite.com/unsubscribe?email={{$json.Email}})
   ```

### Track Opens/Clicks
1. Use SendGrid instead of Gmail
2. Enable tracking in SendGrid settings
3. View analytics in SendGrid dashboard

### A/B Testing
1. Duplicate the workflow
2. Create variant B with different subject/body
3. Split your list into two sheets
4. Run both workflows
5. Compare results in Email Log

### Send at Recipient's Timezone
1. Add "Timezone" column to Google Sheet
2. Add node to convert time
3. Use Schedule Trigger with dynamic execution

## Support & Updates

### Get Help
- n8n Community Forum: https://community.n8n.io
- n8n Documentation: https://docs.n8n.io

### Workflow Updates
- Check for node version updates in n8n
- Backup before making changes
- Test in a duplicate workflow first

## Checklist for Going Live

- [ ] Google Sheet created with correct columns
- [ ] Email Log sheet created
- [ ] Google Sheets credentials configured
- [ ] Gmail/SendGrid credentials configured
- [ ] Test emails sent successfully
- [ ] Email template reviewed and approved
- [ ] Schedule set to desired time
- [ ] Rate limiting configured appropriately
- [ ] Error handling tested
- [ ] Logging verified in Email Log sheet
- [ ] Workflow activated in n8n
- [ ] First production run monitored
- [ ] Deliverability checked (not in spam)

---

**Created by:** Sanjay  
**Version:** 1.0  
**Last Updated:** 2024  
**License:** MIT
