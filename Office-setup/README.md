# Office Setup - n8n Workflows Collection

This directory contains n8n automation workflows for various business automation tasks.

## Available Workflows

### 1. Office Setup (WhatsApp AI Control)
**File:** `Office_setup.json`

Control office devices (lights, printers, etc.) via WhatsApp using AI-powered voice or text commands. Integrates with ESP32 hardware for IoT device control.

**Features:**
- WhatsApp message/voice note trigger
- OpenAI GPT-4o-mini AI agent
- MCP client for device control tools
- Audio transcription with OpenAI Whisper
- Real-time device state management

**Documentation:** See main project README

---

### 2. Office Setup MCP (Tool Server)
**File:** `Office_setup_MCP.json`

Companion workflow that exposes HTTP endpoints for the AI agent to control office devices.

**Features:**
- HTTP tool definitions for MCP client
- Device state queries
- Device control endpoints (on/off)
- Integration with ESP32 REST API

**Documentation:** See main project README

---

### 3. Car Detailing Daily Email Campaign ⭐ NEW
**File:** `Car_Detailing_Daily_Email.json`

Automatically send personalized daily emails to car detailing businesses from a Google Sheets list.

**Features:**
- ✅ Daily scheduled trigger (9 AM)
- ✅ Google Sheets integration for business list
- ✅ Personalized email templates with variable substitution
- ✅ Comprehensive error handling and data validation
- ✅ Automatic logging to Google Sheets
- ✅ Rate limiting to avoid spam filters
- ✅ Duplicate prevention with batch tracking
- ✅ Support for Gmail or SendGrid

**Documentation:**
- **Quick Start:** [`QUICK_START_GUIDE.md`](./QUICK_START_GUIDE.md)
- **Full Documentation:** [`CAR_DETAILING_EMAIL_WORKFLOW_README.md`](./CAR_DETAILING_EMAIL_WORKFLOW_README.md)
- **Google Sheets Setup:** [`GOOGLE_SHEETS_TEMPLATE.md`](./GOOGLE_SHEETS_TEMPLATE.md)
- **SendGrid Setup:** [`SENDGRID_SETUP_GUIDE.md`](./SENDGRID_SETUP_GUIDE.md)
- **Sample Data:** [`sample_business_list.csv`](./sample_business_list.csv)

**Use Case:**
Perfect for B2B outreach campaigns, lead nurturing, or any business that needs to send personalized daily emails to a list maintained in Google Sheets.

**Quick Setup:**
1. Import `Car_Detailing_Daily_Email.json` into n8n
2. Create Google Sheet with columns: `Email`, `Business Owner Name`
3. Connect Google Sheets OAuth2 credential
4. Connect Gmail OAuth2 credential (or SendGrid)
5. Test with sample data
6. Activate workflow

**Email Template:**
- **Subject:** "Missed calls = missed car detailing bookings"
- **Body:** Personalized AI receptionist pitch for car detailers
- **Customizable:** Edit template in "Prepare Email Template" node

---

## Hardware Integration

### ESP32 Device Controller
**File:** `New_ESP_code.ino`

Arduino code for ESP32 microcontroller that provides REST API endpoints for device control.

**Endpoints:**
- `GET /pin-state` - Get current state of all pins
- `POST /set-pin` - Set specific device on/off

**Devices:**
- BAMBOO_PRINTER (GPIO 12)
- SUPPORT_LIGHT (GPIO 13)
- KEY_LIGHT (GPIO 14)
- BULB (GPIO 27)

**Setup:**
1. Install Arduino IDE with ESP32 board support
2. Install libraries: WiFi, WebServer, ArduinoJson
3. Update WiFi credentials in code
4. Flash to ESP32
5. Note the device IP address
6. Update workflow with ESP32 IP

---

## Getting Started

### Prerequisites

1. **n8n Instance**
   - Self-hosted or cloud (n8n.cloud)
   - Version 1.0+ recommended

2. **Required Credentials**
   - Google Sheets OAuth2 API (for email workflow)
   - Gmail OAuth2 or SendGrid API (for email workflow)
   - WhatsApp Cloud API (for office control)
   - OpenAI API (for AI features)

3. **Optional Hardware**
   - ESP32 development board (for office control)
   - Relays for device switching
   - USB power supply

### Installation

1. **Import Workflow**
   ```
   n8n → Add Workflow → Import from File → Select JSON file
   ```

2. **Configure Credentials**
   - Add required credentials in n8n
   - Connect credentials to workflow nodes

3. **Test Workflow**
   - Use n8n "Test Workflow" button
   - Verify all nodes execute successfully

4. **Activate**
   - Toggle "Active" switch in n8n
   - Workflow will run based on trigger

---

## File Structure

```
Office-setup/
├── Office_setup.json                          # WhatsApp AI office control workflow
├── Office_setup_MCP.json                      # MCP tool server workflow
├── Car_Detailing_Daily_Email.json             # Email campaign workflow ⭐
├── New_ESP_code.ino                           # ESP32 Arduino firmware
├── README.md                                  # This file
├── QUICK_START_GUIDE.md                       # Email workflow quick start
├── CAR_DETAILING_EMAIL_WORKFLOW_README.md     # Email workflow full docs
├── GOOGLE_SHEETS_TEMPLATE.md                  # Sheets setup guide
├── SENDGRID_SETUP_GUIDE.md                    # SendGrid configuration
└── sample_business_list.csv                   # Sample email list
```

---

## Workflow Comparison

| Feature | Office Setup | Email Campaign |
|---------|-------------|----------------|
| **Trigger** | WhatsApp message | Daily schedule (cron) |
| **Purpose** | IoT device control | B2B email outreach |
| **AI Integration** | GPT-4o-mini agent | Template-based (customizable) |
| **External APIs** | WhatsApp, OpenAI, ESP32 | Google Sheets, Gmail/SendGrid |
| **User Input** | Voice/text commands | Google Sheet rows |
| **Output** | WhatsApp reply | Personalized emails |
| **Logging** | n8n execution log | Google Sheets + n8n log |
| **Scalability** | Real-time, concurrent | Batch processing, rate-limited |

---

## Support & Documentation

### Email Campaign Workflow
- [Quick Start Guide](./QUICK_START_GUIDE.md) - 15-minute setup
- [Full Documentation](./CAR_DETAILING_EMAIL_WORKFLOW_README.md) - Complete reference
- [Google Sheets Template](./GOOGLE_SHEETS_TEMPLATE.md) - Sheet structure & formulas
- [SendGrid Setup](./SENDGRID_SETUP_GUIDE.md) - Production email service

### Office Control Workflow
- See main project [`README.md`](../README.md)
- ESP32 firmware documentation in Arduino comments

### n8n Resources
- [n8n Documentation](https://docs.n8n.io)
- [n8n Community Forum](https://community.n8n.io)
- [n8n YouTube Channel](https://www.youtube.com/c/n8n-io)

---

## Contributing

Have improvements or new workflows to share?

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

---

## License

MIT License - Feel free to use and modify for your projects.

---

## Credits

**Created by:** Sanjay  
**Technologies:** n8n, OpenAI, WhatsApp Cloud API, Google Sheets, SendGrid, ESP32  
**Version:** 1.0  
**Last Updated:** 2024

---

## FAQ

### Can I customize the email template?
Yes! Edit the "Prepare Email Template" node in the workflow. See [documentation](./CAR_DETAILING_EMAIL_WORKFLOW_README.md#email-template) for details.

### Can I use this for other industries?
Absolutely! Just update the email template and Google Sheet with your target businesses. The workflow is industry-agnostic.

### How do I prevent duplicate emails?
The workflow includes batch ID tracking. For advanced duplicate prevention, see the [troubleshooting guide](./CAR_DETAILING_EMAIL_WORKFLOW_README.md#duplicate-prevention).

### Can I use a different email service?
Yes! The workflow supports Gmail, SendGrid, SMTP, Amazon SES, or any n8n-compatible email service. See [SendGrid guide](./SENDGRID_SETUP_GUIDE.md) for alternatives.

### How many emails can I send per day?
- **Gmail:** 500/day limit
- **SendGrid Free:** 100/day limit  
- **SendGrid Paid:** Up to 100,000+/day
- **Recommendation:** Use SendGrid for >100/day

### Does this work with n8n Cloud?
Yes! All workflows are compatible with both self-hosted and n8n Cloud instances.

---

**Need Help?** Open an issue or check the documentation links above. 🚀
