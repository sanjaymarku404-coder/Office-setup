# Setup Checklist - Car Detailing Daily Email Workflow

Use this checklist to ensure your workflow is properly configured and ready for production.

---

## 📋 Pre-Setup Requirements

- [ ] n8n instance accessible (self-hosted or cloud)
- [ ] Google account with Gmail access
- [ ] Google Sheets access
- [ ] List of car detailing businesses ready

---

## 🔧 Phase 1: Google Sheets Setup

### Create Spreadsheet
- [ ] Created new Google Sheet
- [ ] Named it "Car Detailing Email Campaign" (or similar)
- [ ] Noted the Google Sheet URL

### Sheet 1: Business List
- [ ] Created/renamed first sheet to "Business List" or "Sheet1"
- [ ] Added header row with exact column names:
  - [ ] Column A: `Email`
  - [ ] Column B: `Business Owner Name`
- [ ] Added 2-3 test records with YOUR email addresses
- [ ] Verified no extra spaces in column headers
- [ ] Verified column names are in Row 1

### Sheet 2: Email Log
- [ ] Created second sheet named "Email Log"
- [ ] Added header row with exact column names:
  - [ ] Column A: `status`
  - [ ] Column B: `email`
  - [ ] Column C: `businessOwnerName`
  - [ ] Column D: `sentAt`
  - [ ] Column E: `message`
- [ ] Left rest of sheet empty (will auto-populate)

---

## 🔐 Phase 2: n8n Credentials Setup

### Google Sheets Credential
- [ ] In n8n, navigated to Credentials section
- [ ] Clicked "Add Credential"
- [ ] Selected "Google Sheets OAuth2 API"
- [ ] Clicked "Connect my account"
- [ ] Signed in with Google account
- [ ] Granted permissions to n8n
- [ ] Clicked "Save"
- [ ] Credential shows as "Connected"

### Gmail Credential (Option 1)
- [ ] In n8n Credentials, clicked "Add Credential"
- [ ] Selected "Gmail OAuth2"
- [ ] Clicked "Connect my account"
- [ ] Signed in with Gmail account (use dedicated sending account)
- [ ] Granted permissions to n8n
- [ ] Clicked "Save"
- [ ] Credential shows as "Connected"

### SendGrid Credential (Option 2 - Production)
- [ ] Created SendGrid account at sendgrid.com
- [ ] Completed domain authentication (optional but recommended)
- [ ] Created API key with "Mail Send" permissions
- [ ] In n8n Credentials, selected "SendGrid API"
- [ ] Pasted API key
- [ ] Clicked "Save"
- [ ] Tested credential

---

## 📥 Phase 3: Workflow Import

### Import Workflow File
- [ ] Downloaded or located `Car_Detailing_Daily_Email.json`
- [ ] In n8n, clicked "Add workflow" (top-right)
- [ ] Selected "Import from File"
- [ ] Chose `Car_Detailing_Daily_Email.json`
- [ ] Clicked "Import"
- [ ] Workflow opened in editor

### Rename Workflow
- [ ] Clicked workflow name at top
- [ ] Renamed to descriptive name (e.g., "Car Detailing Daily Emails")
- [ ] Clicked outside to save name

---

## ⚙️ Phase 4: Node Configuration

### Node 1: Daily Schedule (9 AM)
- [ ] Clicked on "Daily Schedule (9 AM)" node
- [ ] Verified cron expression: `0 9 * * *`
- [ ] Modified if different time needed
- [ ] Noted: Workflow inactive by default

### Node 2: Read Business List
- [ ] Clicked on "Read Business List" node
- [ ] Selected Google Sheets credential from dropdown
- [ ] For "Document ID":
  - [ ] Clicked "From list"
  - [ ] Clicked "Select from list"
  - [ ] Found and selected your Google Sheet
- [ ] For "Sheet":
  - [ ] Clicked "From list"
  - [ ] Selected "Business List" or "Sheet1"
- [ ] Clicked "Execute node" button
- [ ] Verified test data appears in output
- [ ] Node shows green checkmark

### Node 4: Log Batch Start
- [ ] Clicked on "Log Batch Start" node
- [ ] Selected same Google Sheets credential
- [ ] Selected same Document ID
- [ ] For "Sheet":
  - [ ] Selected "Email Log" (Sheet 2)
- [ ] Verified configuration

### Node 8: Send Email (Gmail)
- [ ] Clicked on "Send Email (Gmail)" node
- [ ] Selected Gmail OAuth2 credential
- [ ] Updated "From Email" to your sending email address
- [ ] Verified other fields use template variables (don't change)
- [ ] Left "To Email" as: `={{ $json.recipientEmail }}`
- [ ] Left "Subject" as: `={{ $json.emailSubject }}`
- [ ] Left "Message" as: `={{ $json.emailBody }}`

### Node 11: Write Log to Sheet
- [ ] Clicked on "Write Log to Sheet" node
- [ ] Selected same Google Sheets credential
- [ ] Selected same Document ID
- [ ] For "Sheet":
  - [ ] Selected "Email Log"
- [ ] Verified configuration

### Optional: Review Other Nodes
- [ ] Reviewed "Prepare Email Template" for email content
- [ ] Reviewed "Validate Data" conditions
- [ ] Reviewed "Wait (Rate Limiting)" duration (default: 2 seconds)

---

## 🧪 Phase 5: Testing

### Pre-Test Verification
- [ ] Google Sheet has 2-3 test records
- [ ] Test records use YOUR actual email addresses
- [ ] All credentials are connected
- [ ] All nodes are properly configured

### Run Test
- [ ] In n8n, clicked "Test workflow" button (top-right)
- [ ] Watched execution progress through nodes
- [ ] Waited for completion (30 seconds - 1 minute)
- [ ] All nodes show green checkmarks ✅

### Verify Test Results

#### Check Emails Received
- [ ] Opened test email inbox(es)
- [ ] Received email(s) (check spam folder if not in inbox)
- [ ] Subject line correct: "Missed calls = missed car detailing bookings"
- [ ] Personalization correct: "Hi [Your Name],"
- [ ] Email body displays properly
- [ ] No template variable errors (no {{ }} visible)

#### Check Email Log Sheet
- [ ] Opened Google Sheet
- [ ] Went to "Email Log" sheet
- [ ] New row(s) appeared with:
  - [ ] Status: "success"
  - [ ] Correct email addresses
  - [ ] Correct names
  - [ ] Timestamp in ISO format
  - [ ] Message: "Email sent successfully"

### Troubleshoot Failed Tests
If test failed, check:
- [ ] Node error messages in n8n
- [ ] Credential authorization status
- [ ] Google Sheet sharing permissions
- [ ] Column names match exactly (case-sensitive)
- [ ] Email addresses are valid
- [ ] Review troubleshooting section in documentation

---

## 🗑️ Phase 6: Clean Test Data

### Remove Test Records
- [ ] Opened Google Sheet
- [ ] Went to "Business List" sheet
- [ ] Deleted test data rows (keep headers!)
- [ ] Verified Sheet 1 has only header row

### Clear Test Logs (Optional)
- [ ] Went to "Email Log" sheet
- [ ] Optionally deleted test log entries
- [ ] Or kept for reference

---

## 📊 Phase 7: Add Production Data

### Prepare Business List
- [ ] Have list of real car detailing businesses ready
- [ ] List includes email addresses
- [ ] List includes business owner first names

### Add Businesses to Sheet
- [ ] Opened Google Sheet "Business List" sheet
- [ ] Added businesses starting at Row 2:
  - Column A: Email address
  - Column B: First name or full name
- [ ] Verified all entries have both email and name
- [ ] Verified no duplicate email addresses
- [ ] Saved sheet (auto-saves)

### Data Quality Check
- [ ] All emails have "@" symbol
- [ ] No obvious typos in emails
- [ ] All names are properly capitalized
- [ ] No empty rows in the middle of data
- [ ] Row count noted: _____ businesses

---

## 🎨 Phase 8: Customize Email (Optional)

### Review Current Template
- [ ] Read the default email template
- [ ] Decided if changes are needed

### Modify Template (if needed)
- [ ] Clicked "Prepare Email Template" node
- [ ] Found "emailSubject" field
- [ ] Updated subject line if desired
- [ ] Found "emailBody" field
- [ ] Updated email content if desired
- [ ] Kept `{{ $json['Business Owner Name'] }}` for personalization
- [ ] Saved changes

### Test Customizations
- [ ] Ran another test workflow
- [ ] Verified customizations appear correctly
- [ ] Checked personalization still works

---

## ⏰ Phase 9: Configure Schedule

### Set Send Time
- [ ] Clicked "Daily Schedule (9 AM)" node
- [ ] Current cron: `0 9 * * *` (9:00 AM daily)
- [ ] Modified if different time desired:
  - [ ] For 8 AM: `0 8 * * *`
  - [ ] For 2 PM: `0 14 * * *`
  - [ ] For weekdays only: `0 9 * * 1-5`
- [ ] Noted timezone (n8n uses server timezone)

### Verify Execution Time
- [ ] Confirmed send time is appropriate for recipients
- [ ] Considered recipient time zones
- [ ] Documented send time: _____ (e.g., 9:00 AM EST)

---

## 🚀 Phase 10: Activate Workflow

### Final Pre-Activation Checklist
- [ ] Test successful with real data
- [ ] Production business list populated
- [ ] Email template approved
- [ ] Schedule time set correctly
- [ ] All credentials active and not expired
- [ ] Email Log sheet ready

### Activate
- [ ] In n8n workflow editor
- [ ] Found "Active" toggle (top-right)
- [ ] Toggled to ON (blue)
- [ ] Confirmed activation message
- [ ] Workflow now shows "Active" badge

### Document Activation
- [ ] Activation date: _____
- [ ] First scheduled run: _____
- [ ] Number of businesses: _____
- [ ] Expected emails per day: _____

---

## 📈 Phase 11: Monitor First Run

### Day 1 - After First Execution

#### Check Workflow Execution
- [ ] In n8n, went to "Executions" tab
- [ ] Found today's execution
- [ ] Execution status: Success ✅
- [ ] Reviewed execution details
- [ ] All nodes completed successfully

#### Check Email Log
- [ ] Opened Google Sheet
- [ ] Went to "Email Log" sheet
- [ ] Counted "success" entries: _____
- [ ] Counted "skipped" entries: _____
- [ ] Counted "error" entries: _____
- [ ] Total matches business count: ☐ Yes ☐ No

#### Check Deliverability
- [ ] Checked 2-3 test inboxes (if available)
- [ ] Emails arrived: ☐ Inbox ☐ Spam ☐ Not received
- [ ] If in spam, reviewed SendGrid setup guide

#### Document Results
- [ ] Total sent: _____
- [ ] Success rate: _____%
- [ ] Any issues identified: _____
- [ ] Action items: _____

---

## 🔍 Phase 12: Ongoing Monitoring

### Daily Monitoring (Quick Check)
- [ ] Check n8n execution status (30 seconds)
- [ ] Spot-check Email Log for errors (1 minute)
- [ ] Note any anomalies

### Weekly Monitoring (Detailed Review)
- [ ] Review full Email Log
- [ ] Count success vs. error vs. skipped
- [ ] Identify patterns in errors
- [ ] Update business list (add/remove)
- [ ] Check for responses and update accordingly

### Monthly Review
- [ ] Calculate response rate
- [ ] Review email effectiveness
- [ ] Consider A/B testing subject lines
- [ ] Optimize send time if needed
- [ ] Clean up Email Log (archive old entries)

---

## 🛠️ Phase 13: Maintenance Tasks

### Regular Maintenance
- [ ] Remove bounced emails from business list
- [ ] Update names if corrections received
- [ ] Add new businesses as identified
- [ ] Archive contacted businesses who responded
- [ ] Backup Google Sheet monthly

### Credential Maintenance
- [ ] Check credential expiration (if applicable)
- [ ] Re-authorize if needed
- [ ] Update API keys if rotated

### Workflow Updates
- [ ] Check for n8n node updates
- [ ] Test after any n8n platform updates
- [ ] Update documentation if workflow modified

---

## ✅ Production Ready Checklist

### Final Verification
- [ ] ✅ All phases above completed
- [ ] ✅ Test run successful
- [ ] ✅ Production data loaded
- [ ] ✅ Workflow activated
- [ ] ✅ First run monitored and successful
- [ ] ✅ Team trained (if applicable)
- [ ] ✅ Documentation accessible
- [ ] ✅ Monitoring process established

### Success Criteria Met
- [ ] ✅ Emails sending daily at scheduled time
- [ ] ✅ Personalization working correctly
- [ ] ✅ No errors in Email Log
- [ ] ✅ Emails arriving in inbox (not spam)
- [ ] ✅ Logging working properly
- [ ] ✅ No duplicate emails sent

---

## 📞 Support Resources

If you need help:

1. **Documentation**
   - [ ] Reviewed Quick Start Guide
   - [ ] Checked Full Documentation
   - [ ] Consulted Troubleshooting section

2. **n8n Community**
   - [ ] Searched n8n community forum
   - [ ] Posted question if needed

3. **Service Providers**
   - [ ] Google Sheets support
   - [ ] Gmail support
   - [ ] SendGrid support (if applicable)

---

## 🎉 Congratulations!

If you've completed this checklist, your workflow is:
- ✅ Properly configured
- ✅ Tested and verified
- ✅ Production-ready
- ✅ Actively sending emails
- ✅ Being monitored

**Your car detailing email campaign is now running on autopilot!** 🚀

---

## 📝 Notes & Customizations

Use this space to document your specific setup:

**Send Schedule:**
- Time: _____
- Timezone: _____
- Days: _____

**Business Count:**
- Total businesses: _____
- Active emails: _____

**Customizations Made:**
- Subject line: _____
- Body changes: _____
- Rate limiting: _____

**Contact Information:**
- Sending email: _____
- Reply-to email: _____
- Your name/signature: _____

**Important Dates:**
- Setup completed: _____
- First activation: _____
- Last data update: _____

---

**Version:** 1.0  
**Last Updated:** 2024  
**Print or save this checklist for reference**
