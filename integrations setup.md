
  🎯 Zoho Webhook Setup - Quick Start

  Your Webhook URL:

  https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook

  Steps to Configure in Zoho Desk:

  1. Go to Zoho Desk:
    - Log in: https://desk.zoho.com
    - Navigate: Setup (⚙️) → Automation → Webhooks
    - Click + New Webhook
  2. Configure Webhook:
  Name: Enterprise AI Support - Auto Processing
  URL: https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook
  Method: POST
  Content Type: application/json
  3. Select Events:
    - ✅ Ticket Create
    - ✅ Ticket Thread Add
  4. Add Channel Filter:
  Channel equals Email
  4. (This prevents processing chat/phone tickets)
  5. Retry Settings:
  Retry Count: 3
  Retry Interval: 60 seconds
  Timeout: 30 seconds
  6. Test & Enable:
    - Click Test Webhook to verify
    - Toggle Enable to ON
    - Click Save

  What Happens Automatically:

  Customer sends email
      ↓
  Zoho creates ticket
      ↓
  Webhook triggers your app (3-5 sec)
      ↓
  Claude AI processes & responds
      ↓
  Reply sent to customer via Zoho
      ↓
  Data saved to Supabase
      ↓
  Jira ticket created if escalation needed

  Test It:

  After setup, test by:
  1. Sending an email to your Zoho support address
  2. Check Zoho - should see AI response within 5 seconds
  3. Check Supabase - ticket data should appear
  4. Check Vercel logs - processing logs visible

  Full Documentation:

  I've created a complete guide at:
  /Users/admin/Documents/claudecode/Projects/enterprise-ai-support-v12/ZOHO-WEBHOOK-SETUP.md

  This includes:
  - Detailed configuration steps
  - Troubleshooting guide
  - Security best practices
  - Monitoring instructions

  Ready to configure the webhook in Zoho Desk now? Let me know if you need help with any specific step!

