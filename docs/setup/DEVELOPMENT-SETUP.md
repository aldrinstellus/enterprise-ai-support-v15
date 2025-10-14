# Development Environment Setup

Complete guide for running the Enterprise AI Support V12 application with webhook integration.

## Quick Start

```bash
# Make scripts executable (first time only)
chmod +x start-dev.sh stop-dev.sh get-webhook-url.sh

# Start development environment
./start-dev.sh

# Get current webhook URL
./get-webhook-url.sh

# Stop development environment
./stop-dev.sh
```

## What `start-dev.sh` Does

1. ✅ Checks for required dependencies (ngrok, Node.js)
2. ✅ Cleans up any existing processes on port 3011
3. ✅ Starts Next.js dev server on port 3011
4. ✅ Starts ngrok tunnel
5. ✅ Displays webhook URL for Zoho Desk configuration
6. ✅ Monitors logs in real-time

## Persistent Configuration

### Option 1: Free ngrok (URL changes each restart)

**Current Setup** - No additional configuration needed, but you'll need to update the webhook URL in Zoho Desk each time you restart.

**After each restart:**
1. Run `./get-webhook-url.sh` to see the new URL
2. Update the URL in Zoho Desk webhook settings

### Option 2: ngrok Paid Plan (Consistent URLs)

If you upgrade to a paid ngrok plan, you can use a consistent subdomain:

1. **Get your ngrok authtoken:**
   ```bash
   ngrok config check
   ```

2. **Edit `ngrok.yml`:**
   ```yaml
   tunnels:
     enterprise-ai-support:
       subdomain: your-custom-subdomain
   ```

3. **Update `start-dev.sh` to use named tunnel:**
   Replace:
   ```bash
   ngrok http 3011 --log=stdout
   ```
   With:
   ```bash
   ngrok start enterprise-ai-support --log=stdout
   ```

4. **One-time Zoho Desk setup:**
   - URL will always be: `https://your-custom-subdomain.ngrok-free.dev/api/zoho/webhook`
   - Configure once, works forever!

## Zoho Desk Webhook Configuration

### Required Settings

1. **Go to Zoho Desk:**
   - Setup → Automation → Webhooks

2. **Create/Update Webhook:**
   - **Name**: `Enterprise AI Support - Dev`
   - **URL**: `https://[your-ngrok-url]/api/zoho/webhook`
   - **Method**: `POST`
   - **Content Type**: `application/json`

3. **Events to Enable:**
   - ✅ **Ticket Add** (or Ticket Create)
   - ✅ **Ticket Thread Add**

4. **Channel Filter:**
   ```
   Channel equals Email
   ```

5. **Save and Enable** the webhook

## Troubleshooting

### Port 3011 is already in use
```bash
./stop-dev.sh
./start-dev.sh
```

### ngrok not found
```bash
brew install ngrok
```

### Can't see ngrok URL
```bash
# Check ngrok web interface
open http://localhost:4040  # or http://localhost:4041
```

### Webhook not receiving events
1. Check webhook is enabled in Zoho Desk
2. Verify both events are selected (Ticket Add + Thread Add)
3. Check channel filter is set to "Email"
4. Test the endpoint:
   ```bash
   curl https://[your-ngrok-url]/api/zoho/webhook
   ```

## Environment Variables

Ensure your `.env.local` has:

```bash
# Claude AI
ANTHROPIC_API_KEY=sk-ant-api03-...

# Database
DATABASE_URL=postgresql://...

# Zoho Desk
ZOHO_ORG_ID=900826394
ZOHO_CLIENT_ID=...
ZOHO_CLIENT_SECRET=...
ZOHO_REFRESH_TOKEN=...

# Optional: Dify AI, Jira
# DIFY_KB_ID=...
# JIRA_BASE_URL=...
```

## Production Deployment

For production, use your Vercel deployment URL instead of ngrok:

1. **Deploy to Vercel:**
   ```bash
   vercel --prod
   ```

2. **Update Zoho Desk webhook:**
   - URL: `https://your-app.vercel.app/api/zoho/webhook`
   - This URL is permanent, no need to update

3. **Environment Variables:**
   - All `.env.local` variables must be configured in Vercel dashboard

## Useful Commands

```bash
# View logs
tail -f /tmp/ngrok.log

# Check if services are running
lsof -i:3011    # Next.js
lsof -i:4040    # ngrok web interface

# Test webhook endpoint
curl https://[ngrok-url]/api/zoho/webhook

# Test Zoho connection
curl http://localhost:3011/api/zoho/test

# Manual sync tickets
curl "http://localhost:3011/api/zoho/sync?limit=10"
```

## Architecture

```
Email → Zoho Desk → Webhook → ngrok → localhost:3011 → AI Processing → Reply
```

**Development Flow:**
1. Customer sends email to support@atcaisupport.zohodesk.com
2. Zoho Desk creates ticket
3. Zoho sends webhook to ngrok public URL
4. ngrok forwards to localhost:3011
5. AI processes ticket and sends reply
6. All data synced to database

## Support

If you encounter issues:
1. Check logs: `/tmp/ngrok.log`
2. Check Next.js terminal output
3. Verify environment variables
4. Test API endpoints individually
5. Review Zoho Desk webhook execution logs
