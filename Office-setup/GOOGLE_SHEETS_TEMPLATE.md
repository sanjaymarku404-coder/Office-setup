# Google Sheets Template for Car Detailing Email Campaign

## Quick Setup Guide

### Create Your Google Sheet

1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new blank spreadsheet
3. Name it: "Car Detailing Email Campaign"
4. Create two sheets as described below

---

## Sheet 1: Business List

**Sheet Name:** `Business List` (or keep default "Sheet1")

### Column Structure

| Column A | Column B |
|----------|----------|
| Email | Business Owner Name |

### Example Data

| Email | Business Owner Name |
|-------|-------------------|
| john.smith@pristinedetailing.com | John |
| sarah.j@autoshine.com | Sarah |
| mike@carcarepro.com | Mike |
| jennifer.brown@detailstudio.com | Jennifer |
| robert.davis@elitedetail.com | Robert |
| lisa.wilson@sparkleauto.com | Lisa |
| david.moore@detailexperts.com | David |
| emily.taylor@shinemaster.com | Emily |
| chris.anderson@carpolish.com | Chris |
| amanda.thomas@detailpro.com | Amanda |

### Column Descriptions

**Email (Column A)**
- Required field
- Must be a valid email address
- Will be used as the recipient address
- Duplicates are allowed but not recommended

**Business Owner Name (Column B)**
- Required field
- Used for email personalization
- Can be first name only or full name
- Appears in email as "Hi {{Name}},"

### Data Entry Tips

1. **First Names Work Best**
   - ✅ "John" → "Hi John,"
   - ✅ "Sarah" → "Hi Sarah,"
   - ⚠️ "Mr. Smith" → "Hi Mr. Smith," (formal but okay)
   - ❌ "" (empty) → Email will be skipped

2. **Valid Email Formats**
   - ✅ john@example.com
   - ✅ john.smith@example.co.uk
   - ❌ john@example (invalid, will fail)
   - ❌ (empty) (will be skipped)

3. **Special Characters**
   - ✅ "José" → Works fine
   - ✅ "Mary-Ann" → Works fine
   - ✅ "O'Brien" → Works fine

---

## Sheet 2: Email Log

**Sheet Name:** `Email Log`

### Column Structure

| Column A | Column B | Column C | Column D | Column E |
|----------|----------|----------|----------|----------|
| status | email | businessOwnerName | sentAt | message |

### Example Log Entries

| status | email | businessOwnerName | sentAt | message |
|--------|-------|-------------------|--------|---------|
| success | john.smith@pristinedetailing.com | John | 2024-01-15T09:00:23.456Z | Email sent successfully |
| success | sarah.j@autoshine.com | Sarah | 2024-01-15T09:00:25.789Z | Email sent successfully |
| skipped | | | 2024-01-15T09:00:27.123Z | Skipped - Missing email or business owner name |
| error | invalid@test | Mike | 2024-01-15T09:00:29.456Z | Invalid email address format |
| success | jennifer.brown@detailstudio.com | Jennifer | 2024-01-15T09:00:31.789Z | Email sent successfully |

### Column Descriptions

**status (Column A)**
- Automatically populated
- Values: `success`, `error`, `skipped`
- Use for filtering and reporting

**email (Column B)**
- Automatically populated
- The recipient email address
- "N/A" for skipped entries

**businessOwnerName (Column C)**
- Automatically populated
- The personalized name used
- Empty for skipped entries

**sentAt (Column D)**
- Automatically populated
- ISO 8601 timestamp format
- Timezone: UTC

**message (Column E)**
- Automatically populated
- Status description
- Error details if applicable

### Using the Log

**Daily Summary**
```
Filter by: sentAt contains today's date
Count by: status column
Result: "50 success, 2 error, 3 skipped"
```

**Find Errors**
```
Filter: status = "error"
Review: message column for details
Action: Fix issues and retry
```

**Track Individual Business**
```
Filter: email = specific email address
View: Complete send history
```

---

## Sample Data for Testing

### Test Data (5 Businesses)

Copy this data to test the workflow:

```
Email,Business Owner Name
test1@yourdomain.com,Alex
test2@yourdomain.com,Brian
test3@yourdomain.com,Carol
test4@yourdomain.com,Diana
test5@yourdomain.com,Ethan
```

**Important for Testing:**
- Replace `yourdomain.com` with a domain you control
- Use real email addresses you have access to
- Verify emails arrive in inbox (not spam)
- Check personalization is correct

---

## Google Sheets Formulas (Optional)

### Auto-count Sent Today

In a separate "Dashboard" sheet, add:

**Cell A1:** `Today's Emails Sent`

**Cell B1:**
```
=COUNTIFS('Email Log'!A:A,"success",'Email Log'!D:D,">"&TODAY())
```

### Auto-count Errors Today

**Cell A2:** `Today's Errors`

**Cell B2:**
```
=COUNTIFS('Email Log'!A:A,"error",'Email Log'!D:D,">"&TODAY())
```

### Success Rate

**Cell A3:** `Success Rate`

**Cell B3:**
```
=B1/(B1+B2)
```

### Last Run Time

**Cell A4:** `Last Run`

**Cell B4:**
```
=MAX('Email Log'!D:D)
```

---

## Data Management

### Adding New Businesses

**Method 1: Manual Entry**
1. Open the "Business List" sheet
2. Add new row at the bottom
3. Fill in Email and Business Owner Name
4. Save (auto-saves in Google Sheets)

**Method 2: Import from CSV**
1. File → Import
2. Select CSV file
3. Choose "Append to current sheet"
4. Map columns correctly

**Method 3: Google Forms**
1. Create a Google Form
2. Add fields: Email, Business Owner Name
3. Link form responses to your sheet
4. New submissions automatically added

### Removing Businesses

**Option 1: Delete Row**
- Right-click row number → Delete row
- Permanent deletion

**Option 2: Archive**
- Create "Archived" sheet
- Move old businesses there
- Keeps history intact

### Cleaning Data

**Remove Duplicates:**
1. Select Email column (Column A)
2. Data → Remove duplicates
3. Review and confirm

**Trim Whitespace:**
1. Select all data
2. Data → Trim whitespace
3. Removes extra spaces

**Validate Emails:**
1. Select Email column
2. Data → Data validation
3. Criteria: Text contains "@"

---

## Sharing & Permissions

### For Workflow Access

The Google account used for n8n credentials needs:
- **Editor** access to the sheet
- Read access to "Business List" sheet
- Write access to "Email Log" sheet

### Sharing Steps

1. Click "Share" button (top right)
2. Add the email used in n8n credentials
3. Set permission to "Editor"
4. Uncheck "Notify people"
5. Click "Done"

### Team Collaboration

**Read-Only Access:**
- Team members who view data only
- Can't modify business list
- Good for managers/analysts

**Editor Access:**
- Team members who update list
- Can add/remove businesses
- Can modify email log

**Commenting Access:**
- Team members who review data
- Can leave notes on entries
- Can't modify cells

---

## Backup Strategy

### Automatic Backups

**Option 1: Google Sheets Version History**
1. File → Version history → See version history
2. Restore previous versions if needed

**Option 2: Scheduled Export**
1. Install "Sheetgo" add-on
2. Set up daily export to Google Drive
3. Creates timestamped copies

### Manual Backups

**Before Major Changes:**
1. File → Make a copy
2. Name: "Car Detailing Campaign - Backup YYYY-MM-DD"
3. Store in backup folder

**Export to CSV:**
1. File → Download → CSV
2. Save locally
3. Upload to cloud storage

---

## Advanced Sheet Setup

### Conditional Formatting

**Highlight Duplicate Emails:**
1. Select Email column
2. Format → Conditional formatting
3. Format cells if: "Custom formula is"
4. Formula: `=COUNTIF($A:$A,$A1)>1`
5. Choose red background

**Highlight Long Names:**
1. Select Business Owner Name column
2. Format → Conditional formatting
3. Format cells if: "Text contains"
4. Enter: more than 20 characters
5. Choose yellow background

### Data Validation

**Ensure Email Format:**
1. Select Email column (except header)
2. Data → Data validation
3. Criteria: "Text contains"
4. Value: "@"
5. Reject invalid input: ✓

**Prevent Empty Names:**
1. Select Business Owner Name column
2. Data → Data validation
3. Criteria: "Text is not empty"
4. Reject invalid input: ✓

---

## Troubleshooting

### Sheet Not Found in n8n

**Solution:**
1. Verify sharing permissions
2. Use same Google account in n8n
3. Refresh credential in n8n
4. Re-authorize if needed

### Columns Not Recognized

**Solution:**
1. Check exact spelling: "Email" not "email"
2. Remove extra spaces in headers
3. Ensure headers are in Row 1

### Data Not Updating

**Solution:**
1. Check Google Sheets API quota
2. Verify sheet isn't in "Protected" mode
3. Refresh n8n node cache

---

## Sample Sheet Link

**Download Template:**
You can create a copy of this structure in Google Sheets by:

1. Creating a new blank spreadsheet
2. Setting up Sheet 1 with columns: `Email | Business Owner Name`
3. Setting up Sheet 2 with columns: `status | email | businessOwnerName | sentAt | message`
4. Adding sample data for testing

---

## Pro Tips

### 1. Use Named Ranges
- Select data range
- Data → Named ranges
- Name: "BusinessList"
- Reference in formulas: `=COUNTIF(BusinessList,...)`

### 2. Create Dashboard
- Add summary metrics
- Use charts for visualization
- Track trends over time

### 3. Protect Headers
- Select Row 1
- Data → Protect sheets and ranges
- Set to "Restrict who can edit"
- Prevents accidental deletion

### 4. Color Code Status
- In Email Log sheet
- Format "success" as green
- Format "error" as red
- Format "skipped" as yellow

---

**Need Help?** Contact support or check the main workflow README for troubleshooting steps.
