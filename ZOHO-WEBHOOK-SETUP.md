# Zoho Desk Webhook Setup Guide

Complete guide to configure automatic ticket syncing from Zoho Desk to your Enterprise AI Support V12 application.

## Overview

When configured, Zoho Desk will automatically send webhook events to your application whenever:
- A new ticket is created
- A new thread/reply is added to a ticket
- Ticket status changes

Your app will then:
1. Process the ticket with Claude AI
2. Generate intelligent responses
3. Sync data to Supabase
4. Create Jira tickets if escalation is needed

## Prerequisites

✅ Zoho Desk account with admin access
✅ Your app deployed and accessible (Vercel production URL)
✅ Environment variables configured in Vercel

## Webhook URL

Use your **production Vercel URL** (not localhost):

```
https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook
```

Or if you set up a custom domain, use:
```
https://yourdomain.com/api/zoho/webhook
```

## Step-by-Step Configuration

### 1. Access Zoho Desk Webhooks Settings

1. Log in to your Zoho Desk: https://desk.zoho.com
2. Navigate to: **Setup** (gear icon) → **Automation** → **Webhooks**
3. Click **+ New Webhook**

### 2. Configure Webhook Details

**Basic Information:**
- **Webhook Name**: `Enterprise AI Support - Ticket Processing`
- **Description**: `Automatic ticket processing with AI responses`
- **Webhook URL**: `https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook`
- **Method**: `POST`

**Request Format:**
- **Content Type**: `application/json`
- **API Type**: `REST API`

### 3. Select Events to Trigger Webhook

Check the following events:

**Ticket Events:**
- ✅ **Ticket Create** - When a new ticket is created
- ✅ **Ticket Thread Add** - When a new reply/thread is added

**Optional (for advanced features):**
- ✅ **Ticket Status Update** - Track status changes
- ✅ **Ticket Priority Update** - Track priority changes

### 4. Configure Event Filter (IMPORTANT)

To avoid processing internal/chat tickets, add this filter:

**Channel Filter:**
```
Channel equals Email
```

This ensures only email tickets are processed (not web, phone, or chat tickets).

### 5. Set Up Request Headers (Optional)

For additional security, you can add a verification header:

**Headers:**
```
X-Webhook-Secret: your-secret-key-here
```

Then update your `.env.local` and Vercel environment variables:
```bash
ZOHO_WEBHOOK_SECRET=your-secret-key-here
```

### 6. Configure Retry Settings

**Recommended Settings:**
- **Retry Count**: 3
- **Retry Interval**: 60 seconds
- **Timeout**: 30 seconds

### 7. Test the Webhook

Before saving, use Zoho's built-in test feature:

1. Click **Test Webhook**
2. Select a sample ticket
3. Click **Send Test**
4. You should see a success response:
   ```json
   {
     "success": true,
     "message": "Webhook received and processing started"
   }
   ```

### 8. Enable and Save

1. Toggle **Enable Webhook** to ON
2. Click **Save**

## Webhook Payload Format

Your endpoint receives this format from Zoho:

```json
{
  "body": [
    {
      "eventType": "Ticket_Create",
      "payload": {
        "id": "1200801000000467001",
        "ticketNumber": "110",
        "subject": "Cannot access application",
        "status": "Open",
        "priority": "Medium",
        "channel": "Email",
        "contact": {
          "email": "customer@example.com",
          "lastName": "Smith"
        },
        "firstThread": {
          "content": "Full email content here..."
        }
      }
    }
  ]
}
```

## How It Works (Automatic Flow)

```
1. Customer sends email to your support address
   ↓
2. Zoho Desk creates ticket
   ↓
3. Zoho sends webhook to your app
   ↓
4. Your app processes ticket:
   • Fetches full conversation history
   • Classifies ticket with Claude AI
   • Searches knowledge base (if configured)
   • Generates intelligent response
   • Sends reply back to Zoho
   • Stores in Supabase
   • Creates Jira ticket if needed (escalation)
   ↓
5. Customer receives AI-generated response
```

**Processing Time**: Typically 3-5 seconds end-to-end

## Testing the Webhook

### Test 1: Verify Endpoint is Live

```bash
curl https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook
```

Expected response:
```json
{
  "status": "active",
  "message": "Zoho Desk webhook endpoint is ready",
  "timestamp": "2025-10-09T03:19:04.329Z"
}
```

### Test 2: Send Test Webhook from Zoho

1. In Zoho Desk webhook settings, click **Test Webhook**
2. Select a test ticket
3. Click **Send Test**
4. Check your app logs (Vercel dashboard → Logs)

### Test 3: Create a Real Ticket

1. Send an email to your Zoho Desk support address
2. Check Zoho Desk - ticket should be created
3. Within 5 seconds, check:
   - Ticket should have an AI-generated reply in Zoho
   - Ticket data should appear in Supabase
   - Logs should show processing steps

## Monitoring & Debugging

### Check Webhook Status in Zoho

1. Go to **Setup** → **Automation** → **Webhooks**
2. Click on your webhook
3. View **Execution Log** tab
4. Check for:
   - Success/failure status
   - Response codes
   - Error messages

### Check Application Logs

**Vercel:**
1. Go to https://vercel.com/aldos-projects-8cf34b67/enterprise-ai-support-v12
2. Click **Logs** tab
3. Search for `[Zoho Webhook]` or `[Processing]`

**Local Development:**
- Logs appear in terminal where you ran `npm run dev`

### Common Issues

**Issue**: Webhook returns 500 error
- **Cause**: Missing environment variables
- **Fix**: Verify all Zoho credentials in Vercel env vars

**Issue**: Ticket processed but no reply sent
- **Cause**: Anthropic API key missing/invalid
- **Fix**: Check `ANTHROPIC_API_KEY` in Vercel

**Issue**: Webhook times out
- **Cause**: Slow AI processing
- **Fix**: Increase timeout in Zoho settings to 60 seconds

## Manual Sync Alternative

If webhooks aren't working, you can manually sync tickets:

```bash
# Sync latest 10 tickets
curl https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/sync?limit=10

# Dry run (test without saving)
curl "https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/sync?limit=5&dryRun=true"
```

## Security Best Practices

1. **Use HTTPS only** - Vercel provides this automatically
2. **Add webhook secret** - Verify webhook authenticity
3. **Validate payload** - App already validates required fields
4. **Rate limiting** - Consider adding if you get spam
5. **IP whitelist** - Optional: Whitelist Zoho IP addresses

## Webhook Secret Verification (Optional Enhancement)

To add webhook secret verification:

1. **Update webhook handler** (`src/app/api/zoho/webhook/route.ts`):

```typescript
// Add at the top of POST handler
const webhookSecret = req.headers.get('x-webhook-secret');
if (webhookSecret !== process.env.ZOHO_WEBHOOK_SECRET) {
  return NextResponse.json(
    { error: 'Invalid webhook secret' },
    { status: 401 }
  );
}
```

2. **Add to environment variables**:
```bash
ZOHO_WEBHOOK_SECRET=your-random-secret-key-here
```

3. **Configure in Zoho Desk webhook headers**

## Support & Troubleshooting

If you encounter issues:

1. Check Vercel deployment logs
2. Verify environment variables are set
3. Test webhook endpoint manually
4. Review Zoho webhook execution logs
5. Check Supabase database for synced tickets

## Next Steps

After webhook is configured:

- ✅ Monitor first few tickets to ensure proper processing
- ✅ Set up alerts for failed webhooks
- ✅ Configure knowledge base integration (Dify)
- ✅ Set up Jira integration for escalations
- ✅ Customize AI response templates

---

**Webhook Status**: Ready to configure ✅
**Endpoint**: https://enterprise-ai-support-v12-ectrapb04-aldos-projects-8cf34b67.vercel.app/api/zoho/webhook
**Last Updated**: October 9, 2025
