# Pezhman Inbox Agent – n8n Gmail AI Triage Workflow

Pezhman Inbox Agent is an automated email triage system built with n8n.  
It watches a Gmail inbox, classifies each new email with an AI model, applies labels, and then takes different actions depending on the category (auto-reply, Slack notification, draft creation, or just mark as read).

This workflow is designed to reduce manual inbox work while still keeping humans in control for sensitive or high-priority messages.

---

## ✨ What It Does

When a new email arrives in Gmail:

1. **Trigger**
   - A Gmail Trigger node polls for new messages.

2. **AI Email Classification**
   - The email’s **subject** and **body** are sent to a text classifier powered by GPT-4.1-mini via OpenRouter.
   - The classifier decides which of these four categories the email belongs to:
     - `customer support`
     - `finance/billing`
     - `high priority`
     - `promotion`

3. **Category-Specific Actions**

   ### 🟦 Customer Support

   - Gmail label: a “customer support” label is applied in the inbox.
   - AI reply: an AI Agent node (using Claude 3.5 Sonnet via OpenRouter) generates a friendly, professional response.
   - Auto-reply: the workflow sends the AI-generated message back to the sender as a reply.

   The system prompt is written so the agent behaves as a warm, on-brand customer support assistant for AI Automation Society, and signs off as **“Pej's AI Assistant”**.

   ### 🟨 Finance / Billing

   - Gmail label: a billing-related label is applied.
   - Slack notification: a structured Slack message is sent to a chosen channel with:
     - Sender name
     - Sender email
     - Email subject
     - Timestamp

   This branch does **not** auto-reply with AI. Instead, it acts as an internal notification so the billing team can respond manually.

   ### 🟥 High Priority

   - Gmail label: a “high priority” label is applied.
   - AI draft: an AI Agent node (Claude 3.5 Sonnet) generates a suggested reply.
   - Draft only: instead of sending immediately, the workflow creates a **draft** reply in Gmail addressed to the original sender, attached to the existing thread.

   This keeps a human in the loop for urgent or sensitive emails: you get an AI-suggested answer but still review and edit before sending.

   ### 🟩 Promotion

   - Gmail label: a “promotion” label is applied.
   - The email is automatically **marked as read** to keep the inbox clean and focused on important messages.

---

## 🧱 Workflow Structure (High-Level)

- **Gmail Trigger**  
  Polls for new emails in the inbox.

- **Text Classifier (LangChain node)**  
  Uses GPT-4.1-mini via OpenRouter to classify the email into one of four categories using a clear, descriptive prompt.

- **Label + Action Branches**
  - `customer support` → Apply label → Claude-powered AI Agent → Send Gmail reply  
  - `finance/billing` → Apply label → Send formatted Slack message  
  - `high priority` → Apply label → Claude-powered AI Agent → Create Gmail draft  
  - `promotion` → Apply label → Mark email as read  

All branches use Gmail label IDs configured in the workflow to match existing labels in the connected Gmail account.

---

## 📁 Files in This Repository

- `pezhman-inbox-agent.json`  
  Exported n8n workflow. You can import this file directly into your own n8n instance.

- `workflow-preview.png`  
  Visual screenshot of the full workflow graph inside n8n, showing all nodes and connections.

- `README.md`  
  Project documentation (this file).

---

## ✅ Requirements

To use this workflow you will need:

- An n8n instance (self-hosted or cloud).
- A Gmail account with OAuth2 credentials set up in n8n.
- An OpenRouter API key configured in n8n for:
  - `gpt-4.1-mini` (classification)
  - `anthropic/claude-3.5-sonnet` (AI agents and replies)
- A Slack workspace and OAuth2 credentials (if you want finance/billing notifications).
- Existing Gmail labels that match or replace the label IDs used in the workflow.

> 🔒 **Important:** The JSON file does not contain secrets, but you still must configure your own credentials (Gmail, OpenRouter, Slack) after importing the workflow.

---

## 🚀 Getting Started

1. **Import the Workflow**
   - In n8n, go to **Workflows → Import**.
   - Upload `pezhman-inbox-agent.json`.

2. **Configure Credentials**
   - Open each node that uses:
     - Gmail
     - OpenRouter
     - Slack
   - Select or create your own credentials for each service.

3. **Update Gmail Label IDs (Optional but Recommended)**
   - In your Gmail account, create labels for:
     - Customer Support
     - Finance / Billing
     - High Priority
     - Promotion
   - In the Gmail nodes named:
     - `Add label to message`
     - `Add label to message1`
     - `Add label to message2`
     - `Add label to message3`
   - Replace the existing `Label_...` IDs with the IDs that correspond to your own labels (or just choose the labels from the dropdown if available in n8n).

4. **Update Slack Channel (If Used)**
   - Open the `Send a message` Slack node.
   - Select your Slack credentials.
   - Choose the channel where billing notifications should be posted.

5. **Customize Prompts (Optional)**
   - Open the `AI Agent` and `AI Agent1` nodes.
   - Adjust the system prompts to match your own brand, tone, and support policy.
   - Update the fallback email address if needed.

6. **Activate the Workflow**
   - Turn the workflow **ON** in n8n.
   - Send yourself some test emails that match each category (support, billing, high priority, promotion) and verify:
     - Labels are applied correctly
     - Replies/drafts look good
     - Slack notifications arrive where expected
     - Promotion emails get marked as read

---

## 🔧 Customization Ideas

- Add logging to Google Sheets, Airtable, or a database to track classification statistics.
- Add another branch for “sales” or “partnerships”.
- Extend the finance/billing branch with an AI-generated internal summary.
- Connect to a ticketing system (e.g., Jira, Linear, Notion) for customer support emails.

---

## 📩 Contact

This workflow was created by Pejman as part of an AI-powered inbox automation system.  
For questions or collaboration, you can adapt the email address in the prompts to your own contact.
