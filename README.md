# 🏨 Hotel Booking CRM Automation

> An n8n workflow that captures hotel booking form submissions, uses a Gemini-powered AI agent to suggest hotels, communicates with guests via Gmail, logs data to Google Sheets, and creates leads in Zoho CRM — fully automated end-to-end.

---

## 📋 Overview

This workflow automates the full lifecycle of a hotel booking inquiry — from the moment a guest fills out a JotForm, all the way to a qualified lead being created in Zoho CRM. An AI agent (powered by Google Gemini) handles personalized hotel and restaurant suggestions, waits for the guest's reply, and records their preference before handing off to the CRM.

---

## 🔁 Workflow Diagram

```
JotForm Submission
      │
      ▼
  User Input
      │
      ▼
    Modify  (data normalization)
      │
      ▼
  AI Agent  ◄──── Google Gemini Chat Model
  │       │ ◄──── Simple Memory
  │       └─────► hotel_sheet (Google Sheets — append)
      │
      ▼
Send Message & Wait for Response  (Gmail)
      │
      ▼
Update Row in Sheet  (Google Sheets — update)
      │
      ▼
Create a Lead  (Zoho CRM)
```

---

## ⚙️ Nodes & Their Roles

| Node | Type | Purpose |
|------|------|---------|
| **User Input** | Trigger | Receives form submission data from JotForm |
| **Modify** | Manual Transform | Normalises and maps incoming form fields |
| **AI Agent** | LangChain Agent | Core intelligence — saves data, drafts suggestions, awaits reply |
| **Google Gemini Chat Model** | LLM | Powers the AI Agent's reasoning and email generation |
| **Simple Memory** | Memory | Keeps conversation context across steps |
| **hotel_sheet** | Google Sheets (Append) | Logs guest data to the booking spreadsheet |
| **Send Message & Wait for Response** | Gmail | Sends hotel/restaurant suggestions and waits for guest reply |
| **Update Row in Sheet** | Google Sheets (Update) | Records the guest's chosen hotel/restaurant |
| **Create a Lead** | Zoho CRM | Creates a qualified lead from the confirmed booking |

---

## 📝 Booking Form

The workflow is triggered by the live JotForm booking form:

🔗 **[Hotel Booking Form](https://form.jotform.com/261044287135454)**

**Fields collected:**
- Full Name (First + Last)
- Location (Country + Travel Place)
- Email *(required)*
- Phone Number
- Room Type (Standard / Family / Private / Dorm options)
- Number of Guests *(required)*
- Arrival Date & Time *(required)*
- Departure Date *(required)*
- Free Pickup preference *(required)*
- Special Requests

---

## 🤖 AI Agent Behaviour

The AI Agent follows a structured 4-step process:

1. **Save User Information** — Appends all booking details to `hotel_sheet` in Google Sheets and marks the entry as saved.

2. **Send Suggestion Email** — Uses Gemini to generate a personalised email with:
   - 3 top hotel recommendations for the guest's destination
   - 3 top restaurant recommendations
   - A prompt asking the guest to reply with their choice (1, 2, or 3)

3. **Wait for Reply** — Gmail node pauses execution and listens for the guest's reply from the same email address.

4. **Confirm & Update** — Once a reply arrives, the agent updates the Google Sheet with the guest's choice and sends a confirmation email.

---

## 🛠️ Prerequisites

- [n8n](https://n8n.io/) (self-hosted or cloud)
- Google account with access to:
  - **Google Sheets** (sheet named `hotel_sheet`)
  - **Gmail** (for sending/receiving emails)
  - **Google Gemini API** key (PaLM API)
- **Zoho CRM** account
- **JotForm** account with the booking form configured

---

## 🚀 Setup Instructions

1. **Import the workflow**
   - In n8n, go to **Workflows → Import from File**
   - Upload `CRM_Automation.json`

2. **Configure credentials**

   | Credential | Where to add |
   |------------|--------------|
   | Google Gemini (PaLM) API Key | AI Agent → Chat Model node |
   | Google Sheets OAuth2 | `hotel_sheet` node & Update row node |
   | Gmail OAuth2 | Send Message & Wait node |
   | Zoho CRM OAuth2 | Create a Lead node |

3. **Set up Google Sheet**
   - Create a Google Sheet named `hotel_sheet`
   - Add columns: `Full Name`, `Email`, `Room Type`, `Guests`, `Departure Date`, `Arrival Date`, `Location`, `Special Requests`, `Free Pickup`, `Data Saved`, `Email Sent`, `Response`, `Reply Received`
   - Paste the Sheet ID into the `hotel_sheet` node

4. **Connect JotForm**
   - In JotForm, go to **Settings → Integrations → Webhooks**
   - Point the webhook to your n8n workflow's trigger URL

5. **Activate the workflow**
   - Toggle the workflow to **Active** in n8n

---

## 📁 File Structure

```
hotel-booking-crm-automation/
├── CRM_Automation.json   # n8n workflow export
├── CRM_Automation.png    # Workflow diagram screenshot
└── README.md             # This file
```

---

## 🔒 Environment Variables / Secrets

Never commit credentials to the repo. Store all API keys and OAuth tokens securely in n8n's built-in **Credentials** manager.

---

## 📄 License

MIT — feel free to fork and adapt for your own hospitality automation use case.
