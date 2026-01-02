# Quick Start Guide: Car Detailing Email Campaign

## 🚀 Get Started in 15 Minutes

This guide will help you set up and launch your daily email campaign to car detailing businesses.

---

## Prerequisites Checklist

Before you start, make sure you have:

- [ ] n8n account (self-hosted or cloud)
- [ ] Google account with Gmail
- [ ] Google Sheets access
- [ ] List of car detailing businesses with emails

---

## Step 1: Create Google Sheet (5 minutes)

### 1.1 Create New Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Click **"+ Blank"** to create new spreadsheet
3. Name it: **"Car Detailing Email Campaign"**

### 1.2 Set Up First Sheet

**Sheet Name:** Keep as "Sheet1" or rename to "Business List"

**Add Headers (Row 1):**
- Cell A1: `Email`
- Cell B1: `Business Owner Name`

**Add Test Data (Rows 2-4):**

| Email | Business Owner Name |
|-------|-------------------|
| your-test-email@gmail.com | Test User |
| another-test@gmail.com | Sample Name |

⚠️ **Important:** Use YOUR real email addresses for testing!

### 1.3 Set Up Second Sheet

1. Click **"+"** at bottom to add new sheet
2. Rename it to: **"Email Log"**

**Add Headers (Row 1):**
- Cell A1: `status`
- Cell B1: `email`
- Cell C1: `businessOwnerName`
- Cell D1: `sentAt`
- Cell E1: `message`

### 1.4 Share Sheet

1. Click **"Share"** button (top-right)
2. Copy the sheet URL (you'll need this)
3. Keep the sharing settings for later

✅ **Sheet Setup Complete!**

---

## Step 2: Import Workflow to n8n (3 minutes)

### 2.1 Download Workflow

1. Locate the file: `Car_Detailing_Daily_Email.json`
2. Save it to your computer

### 2.2 Import to n8n

1. Open your n8n instance
2. Click **"Add workflow"** (top-right)
3. Click **"Import from File"**
4. Select `Car_Detailing_Daily_Email.json`
5. Click **"Import"**

### 2.3 Name Your Workflow

1. Click workflow name at top
2. Rename to: **"Car Detailing Daily Emails"**
3. Workflow opens in editor

✅ **Workflow Imported!**

---

## Step 3: Connect Google Sheets (4 minutes)

### 3.1 Add Google Sheets Credential

1. In n8n, go to **Credentials** (left sidebar)
2. Click **"+ Add Credential"**
3. Search for **"Google Sheets OAuth2 API"**
4. Click **"Connect my account"**
5. Sign in with your Google account
6. Grant permissions to n8n
7. Click **"Save"**

### 3.2 Configure "Read Business List" Node

1. Go back to workflow editor
2. Click the **"Read Business List"** node
3. In "Credential to connect with":
   - Select the Google Sheets credential you just created
4. In "Document ID":
   - Click **"From list"**
   - Click **"Select from list"**
   - Choose your "Car Detailing Email Campaign" sheet
5. In "Sheet":
   - Click **"From list"**
   - Select "Sheet1" (or "Business List")
6. Click **"Execute node"** to test
7. You should see your test data appear

### 3.3 Configure "Log Batch Start" Node

1. Click the **"Log Batch Start"** node
2. Set "Credential": Same as before
3. Set "Document ID": Same sheet
4. Set "Sheet": Select "Email Log"
5. Click **"Execute node"** to test

### 3.4 Configure "Write Log to Sheet" Node

1. Click the **"Write Log to Sheet"** node
2. Set "Credential": Same as before
3. Set "Document ID": Same sheet
4. Set "Sheet": Select "Email Log"

✅ **Google Sheets Connected!**

---

## Step 4: Set Up Email Sending (3 minutes)

### 4.1 Add Gmail Credential

1. Go to **Credentials** section
2. Click **"+ Add Credential"**
3. Search for **"Gmail OAuth2"**
4. Click **"Connect my account"**
5. Sign in with Gmail account (use dedicated sending account)
6. Grant permissions
7. Click **"Save"**

### 4.2 Configure "Send Email (Gmail)" Node

1. Click the **"Send Email (Gmail)"** node
2. Set "Credential": Select your Gmail credential
3. In "From Email": Enter your sending email address
4. Leave other fields as-is (they use template variables)
5. Click **"Execute node"** to test

⚠️ **Note:** This won't send yet because it needs data from previous nodes.

✅ **Email Sending Configured!**

---

## Step 5: Test the Workflow (2 minutes)

### 5.1 Full Workflow Test

1. Make sure you have 2-3 test email addresses in your Google Sheet
2. At the top of n8n, click **"Test workflow"** (with all data)
3. Wait for execution to complete
4. Check each node for green checkmark ✓

### 5.2 Verify Results

**Check 1: Email Received**
- Open the test email inboxes
- Verify emails arrived
- Check subject line: "Missed calls = missed car detailing bookings"
- Check personalization: "Hi [Name]," is correct

**Check 2: Email Log Updated**
- Open your Google Sheet
- Go to "Email Log" sheet
- See new rows with "success" status

### 5.3 Troubleshooting Test Failures

**If no email received:**
- Check spam folder
- Verify Gmail credential is authorized
- Check "Send Email" node for errors

**If Google Sheet not updated:**
- Check "Write Log to Sheet" node credentials
- Verify sheet name matches exactly

✅ **Test Successful!**

---

## Step 6: Activate Daily Schedule (1 minute)

### 6.1 Review Schedule Settings

1. Click **"Daily Schedule (9 AM)"** node
2. Current setting: `0 9 * * *` (9:00 AM daily)
3. To change time:
   - `0 8 * * *` = 8:00 AM
   - `0 14 * * *` = 2:00 PM
   - `0 10 * * 1-5` = 10:00 AM weekdays only

### 6.2 Activate Workflow

1. At top-right, toggle **"Active"** switch to ON
2. Confirm activation
3. Workflow will now run automatically every day

✅ **Workflow Active!**

---

## Step 7: Add Real Business Data (Ongoing)

### 7.1 Prepare Your Business List

Format your list with:
- Column A: Email addresses
- Column B: First names

Example:
```
john@pristinedetailing.com, John
sarah@autoshine.com, Sarah
mike@carcare.com, Mike
```

### 7.2 Add to Google Sheet

**Method 1: Manual Entry**
1. Open your Google Sheet
2. Add each business in a new row

**Method 2: Copy-Paste**
1. Copy from Excel/CSV
2. Paste into Google Sheet starting at Row 2
3. Format as needed

**Method 3: Import CSV**
1. File → Import
2. Select your CSV file
3. Choose "Append to current sheet"

### 7.3 Remove Test Data

1. Delete the test email rows
2. Keep only real business contacts
3. Save sheet (auto-saves)

✅ **Ready for Production!**

---

## Daily Operations

### What Happens Each Day

1. **9:00 AM**: Workflow triggers automatically
2. **Read Data**: Pulls current business list from Google Sheet
3. **Send Emails**: Loops through each business
4. **Log Results**: Records success/failure in Email Log
5. **Complete**: Finishes in ~2 minutes for 50 businesses

### Monitoring

**Daily Checklist:**
- [ ] Check Email Log sheet for errors
- [ ] Verify "success" count matches business count
- [ ] Review any "error" or "skipped" entries

**Weekly Tasks:**
- [ ] Add new businesses to the list
- [ ] Remove businesses who replied (to avoid duplicates)
- [ ] Review response rate

### Adding More Businesses

**At Any Time:**
1. Open Google Sheet
2. Add new rows with Email and Business Owner Name
3. Save (automatic)
4. Next day's run will include them

**No Restart Needed:** The workflow reads fresh data each time it runs.

---

## Customizing the Email

### Change Email Subject

1. In n8n workflow, click **"Prepare Email Template"** node
2. Find the "emailSubject" field
3. Change text: `Missed calls = missed car detailing bookings`
4. To something new: `Never miss another car detailing booking`
5. Click **"Save"** (top-right of workflow)

### Change Email Body

1. Same node: **"Prepare Email Template"**
2. Find the "emailBody" field
3. Edit the text
4. Use `{{ $json['Business Owner Name'] }}` for personalization
5. Click **"Save"**

### Preview Changes

1. Click **"Test workflow"**
2. Check test email for new content
3. Verify formatting looks good

---

## Common Issues & Solutions

### Issue 1: Emails Going to Spam

**Solutions:**
- Use a professional email domain (not free Gmail)
- Warm up your sending account (start with 20/day)
- Add unsubscribe link to email body
- Avoid spam trigger words like "free", "guarantee", etc.

### Issue 2: Wrong Personalization

**Check:**
- Column B is named exactly "Business Owner Name"
- No typos or extra spaces in header
- Names are filled in for all rows

**Fix:**
- Correct the column header
- Re-test workflow

### Issue 3: No Emails Sending

**Check:**
- Gmail credential is authorized and not expired
- "Active" toggle is ON for workflow
- Schedule time has passed
- No errors in latest execution log

**Fix:**
- Re-authorize Gmail credential
- Check n8n execution history for errors

### Issue 4: Duplicate Emails

**Prevent:**
- Don't run manual tests after activating
- Check for duplicate rows in Google Sheet
- Remove sent businesses before next run

---

## Best Practices

### 1. Start Small
- Begin with 10-20 businesses
- Test deliverability and responses
- Scale up gradually

### 2. Monitor Responses
- Track who replies
- Move replied contacts to "Archived" sheet
- Avoid sending to them again

### 3. Keep Data Clean
- Remove invalid emails promptly
- Update names if corrections come in
- Use Data Validation in Google Sheets

### 4. Respect Recipients
- Only email businesses you have a reason to contact
- Include contact information in signature
- Honor unsubscribe requests immediately

### 5. Maintain Sender Reputation
- Keep bounce rate below 5%
- Respond to replies quickly
- Don't send to purchased email lists

---

## Getting Help

### n8n Support
- Community Forum: [community.n8n.io](https://community.n8n.io)
- Documentation: [docs.n8n.io](https://docs.n8n.io)
- YouTube Tutorials: Search "n8n workflows"

### Google Sheets Help
- Help Center: [support.google.com/sheets](https://support.google.com/sheets)

### Email Deliverability
- Check Spam Score: [mail-tester.com](https://www.mail-tester.com)
- Sender Reputation: [senderscore.org](https://senderscore.org)

---

## Success Checklist

- [ ] Google Sheet created with correct structure
- [ ] Test data added and verified
- [ ] Workflow imported to n8n
- [ ] Google Sheets credential connected
- [ ] Gmail credential connected
- [ ] All nodes configured correctly
- [ ] Test workflow executed successfully
- [ ] Test emails received and personalized correctly
- [ ] Email Log sheet populated with test results
- [ ] Test data removed from sheet
- [ ] Real business data added
- [ ] Schedule time set appropriately
- [ ] Workflow activated
- [ ] First production run monitored
- [ ] Daily monitoring process established

---

## Next Steps

### Week 1: Testing Phase
- Send to 10-20 businesses
- Monitor deliverability (inbox vs spam)
- Track response rate
- Adjust email copy if needed

### Week 2-3: Scale Up
- Add 20-30 more businesses
- Continue monitoring results
- Optimize email template based on responses

### Month 2+: Full Production
- Maintain steady sending volume
- Add new businesses regularly
- Archive successful conversions
- Refine your approach based on data

---

## Resources

### Included Files
- `Car_Detailing_Daily_Email.json` - Main workflow file
- `CAR_DETAILING_EMAIL_WORKFLOW_README.md` - Detailed documentation
- `GOOGLE_SHEETS_TEMPLATE.md` - Sheet setup guide
- `QUICK_START_GUIDE.md` - This guide

### Additional Learning
- [n8n Beginner Course](https://docs.n8n.io/courses/)
- [Google Sheets Functions](https://support.google.com/docs/table/25273)
- [Email Marketing Best Practices](https://www.campaignmonitor.com/resources/)

---

**Congratulations!** 🎉 You've successfully set up your automated email campaign. The workflow will now run daily and send personalized emails to your car detailing business list.

**Questions?** Refer to the detailed README or n8n community for advanced support.

**Good luck with your outreach!** 🚀

---

**Version:** 1.0  
**Last Updated:** 2024  
**Author:** Sanjay
