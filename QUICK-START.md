# 🚀 Quick Start Guide

## Start Development with Webhooks

```bash
npm run dev:webhooks
```

That's it! This will:
- ✅ Start Next.js on port 3011
- ✅ Start ngrok tunnel
- ✅ Display webhook URL for Zoho Desk

## Other Commands

```bash
# Get current webhook URL
npm run dev:webhook-url

# Stop development environment
npm run dev:stop

# Regular dev (no webhooks)
npm run dev
```

## First Time Setup

1. **Make scripts executable** (one-time):
   ```bash
   chmod +x start-dev.sh stop-dev.sh get-webhook-url.sh
   ```

2. **Configure Zoho Desk webhook**:
   - Go to: Setup → Automation → Webhooks
   - Update URL with the one shown by `npm run dev:webhook-url`
   - Events: **Ticket Add** + **Ticket Thread Add**
   - Channel: **Email only**

## Important Notes

### Free ngrok Users
- URL changes on each restart
- Update Zoho Desk webhook URL after each `npm run dev:webhooks`
- Use `npm run dev:webhook-url` to quickly get the current URL

### Paid ngrok Users
- Configure `ngrok.yml` with your subdomain
- URL stays the same forever
- One-time Zoho Desk setup

## Testing the System

1. **Send email** to `support@atcaisupport.zohodesk.com`
2. **Watch logs** - You'll see:
   - Webhook received
   - AI processing
   - Reply sent
3. **Check your email** - AI reply arrives in ~5 seconds

## Need Help?

See [DEVELOPMENT-SETUP.md](./DEVELOPMENT-SETUP.md) for detailed documentation.
