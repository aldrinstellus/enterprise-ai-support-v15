# Enterprise AI Support Dashboard V12

AI-Powered Customer Support System with Zoho Desk integration, Supabase database, and Claude AI.

## 🚀 Quick Start

```bash
npm install
cp .env.example .env.local
# Edit .env.local with your credentials
npx prisma generate && npx prisma db push
npm run dev
```

**Open**: http://localhost:3011

## 📚 Complete Setup Guide

**For detailed setup instructions**, see [SETUP_FOR_DEVELOPER.md](./SETUP_FOR_DEVELOPER.md)

## ✨ Features

- 🎫 Automated ticket processing with Zoho Desk webhooks
- 🤖 AI-powered responses using Claude AI
- 📊 Live dashboard with real-time updates
- 🔄 Automatic data sync (Zoho → Supabase)
- 🎯 7 workflow scenarios (password reset, account unlock, etc.)

## 🛠️ Tech Stack

- Next.js 15 + TypeScript
- Prisma ORM + Supabase PostgreSQL
- Anthropic Claude SDK
- Zoho Desk API

## 📦 What's Included

- ✅ `.env.example` - Environment variable template
- ✅ `SETUP_FOR_DEVELOPER.md` - Complete setup guide
- ✅ `prisma/schema.prisma` - Database schema
- ✅ Full source code with TypeScript

## 🔐 Required Credentials

You'll need:
1. Supabase account (database)
2. Anthropic API key (Claude AI)
3. Zoho Desk credentials (ticket system)

See `SETUP_FOR_DEVELOPER.md` for detailed instructions.

## 📝 License

Proprietary - All rights reserved

---

**Built with Claude AI** 🤖
