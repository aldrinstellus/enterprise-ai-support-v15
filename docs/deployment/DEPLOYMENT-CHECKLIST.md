# Deployment Checklist - Multi-Scenario Workflow System

**Date**: 2025-10-09
**Version**: 12.0.0
**Branch**: main
**Status**: Ready for deployment after completing remaining tests

---

## 📋 Pre-Deployment Checklist

### Code Quality
- [x] TypeScript compiles without errors
- [x] Server runs successfully on port 3011
- [x] No breaking changes to existing password reset workflow
- [x] All tested scenarios working (5/7 complete)
- [ ] Complete remaining 2 scenario tests
- [ ] Run production build: `npm run build`
- [ ] Run type-check: `npm run type-check`

### Database
- [x] Prisma schema includes workflow fields
- [ ] Run migrations: `npx prisma migrate deploy`
- [ ] Verify database connection
- [ ] Test with production database URL

### Environment Variables
Required for production:

```bash
# Database
DATABASE_URL="postgresql://postgres:PASSWORD@HOST:5432/DATABASE"

# Zoho Integration
ZOHO_DESK_ORG_ID=your_org_id
ZOHO_DESK_ACCESS_TOKEN=your_token

# Email (for Zoho notifications)
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=noreply@example.com
SMTP_PASS=your_password

# Optional: Real integrations (currently using mocks)
AZURE_AD_CLIENT_ID=
AZURE_AD_CLIENT_SECRET=
AZURE_AD_TENANT_ID=

SLACK_BOT_TOKEN=
SLACK_WORKSPACE_URL=

SHAREPOINT_CLIENT_ID=
SHAREPOINT_CLIENT_SECRET=

JIRA_API_URL=
JIRA_API_TOKEN=
JIRA_EMAIL=

LMS_API_URL=
LMS_API_KEY=
```

---

## 🚀 GitHub Deployment Steps

### 1. Create New Repository (if not exists)
```bash
# Initialize git (if needed)
git init

# Add GitHub remote
git remote add origin https://github.com/YOUR_USERNAME/enterprise-ai-support-v12.git

# Or update existing remote
git remote set-url origin https://github.com/YOUR_USERNAME/enterprise-ai-support-v12.git
```

### 2. Commit All Changes
```bash
# Stage all files
git add .

# Create comprehensive commit
git commit -m "feat: Multi-scenario workflow system with 7 automated workflows

- Implemented 7 workflow scenarios (password reset, account unlock, access request, general support, email notifications, printer issues, course completion)
- Added intelligent keyword-based KB search
- Fixed duplicate password reset detection bug
- Added beautiful HTML email templates with gradient styling
- Implemented graceful escalation to human agents
- Zero breaking changes to existing password reset workflow
- Complete with mock integrations for Azure AD, Slack, SharePoint, Jira, LMS

Tested: 5/7 scenarios working perfectly
Files: 10 new files, ~3000 lines of code
Performance: 3-9 second response times"

# Push to GitHub
git push -u origin main
```

### 3. Create Release Tag
```bash
# Tag the release
git tag -a v12.0.0 -m "Multi-Scenario Workflow System
- 7 automated support workflows
- Intelligent KB search
- Agent escalation system
- Production-ready email templates"

# Push tags
git push --tags
```

---

## ☁️ Vercel Deployment Steps

### 1. Install Vercel CLI (if needed)
```bash
npm install -g vercel
```

### 2. Login to Vercel
```bash
vercel login
```

### 3. Link Project
```bash
# Link to existing project or create new
vercel link

# Set project name
# Project name: enterprise-ai-support-v12
# Scope: your-username or your-team
```

### 4. Set Environment Variables
```bash
# Production database
vercel env add DATABASE_URL production

# Zoho credentials
vercel env add ZOHO_DESK_ORG_ID production
vercel env add ZOHO_DESK_ACCESS_TOKEN production

# Add all other required env vars
```

### 5. Deploy to Production
```bash
# Deploy to production
vercel --prod

# Or deploy preview first
vercel

# Then promote to production
vercel --prod
```

### 6. Verify Deployment
```bash
# Check deployment status
vercel ls

# View logs
vercel logs [deployment-url]

# Test the deployment
curl https://your-deployment.vercel.app/api/health
```

---

## 🗄️ Supabase Database Setup

### 1. Run Migrations
```bash
# Set database URL
export DATABASE_URL="postgresql://postgres:PASSWORD@db.PROJECT.supabase.co:6543/postgres?pgbouncer=true"

# Run migrations
npx prisma migrate deploy

# Or push schema (development)
npx prisma db push
```

### 2. Seed Initial Data (if needed)
```bash
# Create seed script
npx prisma db seed

# Or manually insert:
# - Agent users (Sarah Johnson, etc.)
# - KB articles
# - Initial settings
```

### 3. Verify Database
```bash
# Open Prisma Studio
npx prisma studio

# Check tables:
# - tickets
# - users
# - agent_metrics
# - activities
# - notifications
```

---

## 🔐 Security Checklist

### Environment Security
- [ ] All secrets stored in environment variables (not in code)
- [ ] `.env.local` added to `.gitignore`
- [ ] No API keys committed to GitHub
- [ ] Production database uses SSL
- [ ] Vercel environment variables set to "Production" only

### API Security
- [ ] Rate limiting configured
- [ ] CORS properly configured
- [ ] Webhook signature verification enabled
- [ ] Input validation on all endpoints
- [ ] SQL injection protection (Prisma handles this)

### Email Security
- [ ] SPF records configured
- [ ] DKIM signatures enabled
- [ ] DMARC policy set
- [ ] Email templates sanitized against XSS

---

## 📊 Monitoring Setup

### Vercel Analytics
```bash
# Enable Vercel Analytics
npm install @vercel/analytics

# Add to layout.tsx
import { Analytics } from '@vercel/analytics/react';
```

### Error Tracking
```bash
# Optional: Add Sentry
npm install @sentry/nextjs

# Configure Sentry
npx @sentry/wizard@latest -i nextjs
```

### Performance Monitoring
- [ ] Enable Vercel Speed Insights
- [ ] Set up uptime monitoring
- [ ] Configure error alerts
- [ ] Monitor database performance

---

## 🧪 Post-Deployment Testing

### Smoke Tests
```bash
# Test webhook endpoint
curl -X POST https://your-app.vercel.app/api/zoho/webhook \
  -H "Content-Type: application/json" \
  -d '{"test": true}'

# Test health endpoint
curl https://your-app.vercel.app/api/health

# Test scenario detection
# Send test emails through Zoho to verify all 7 scenarios
```

### Functional Tests
- [ ] Password reset email flow
- [ ] Agent assignment on follow-up
- [ ] Account unlock automation
- [ ] Access request provisioning
- [ ] KB search returns correct articles
- [ ] Email notification system health check
- [ ] Printer troubleshooting guide
- [ ] Course completion verification

---

## 📝 Documentation to Update

### README.md
- [ ] Update feature list with 7 workflows
- [ ] Add workflow scenario descriptions
- [ ] Update architecture diagram
- [ ] Add deployment instructions
- [ ] Update environment variables section

### API Documentation
- [ ] Document webhook endpoints
- [ ] Add workflow scenario API docs
- [ ] Update integration guides
- [ ] Add troubleshooting section

### User Guides
- [ ] Create admin setup guide
- [ ] Document workflow configuration
- [ ] Add agent training materials
- [ ] Create customer-facing KB articles

---

## 🔄 Rollback Plan

### If Deployment Fails

1. **Revert to Previous Version**
```bash
# Get previous deployment URL
vercel ls

# Promote previous deployment
vercel promote [previous-deployment-url]
```

2. **Database Rollback**
```bash
# Revert last migration
npx prisma migrate resolve --rolled-back [migration-name]
```

3. **GitHub Rollback**
```bash
# Revert to previous commit
git revert HEAD

# Or reset to specific commit
git reset --hard [commit-hash]
git push --force
```

---

## 📈 Success Metrics

### Performance Targets
- [ ] Response time < 10 seconds
- [ ] Email delivery rate > 99%
- [ ] Workflow success rate > 95%
- [ ] System uptime > 99.9%

### Business Metrics
- [ ] AI resolution rate > 70%
- [ ] Agent workload reduction > 50%
- [ ] Customer satisfaction score > 4.5/5
- [ ] Average handling time reduction > 40%

---

## 🎯 Post-Launch Tasks

### Week 1
- [ ] Monitor error logs daily
- [ ] Track workflow success rates
- [ ] Collect user feedback
- [ ] Fine-tune KB article matching

### Week 2
- [ ] Analyze performance metrics
- [ ] Optimize slow workflows
- [ ] Add more KB articles
- [ ] Review agent assignments

### Month 1
- [ ] Replace mock integrations with real APIs
- [ ] Add more workflow scenarios
- [ ] Implement advanced analytics
- [ ] Create admin dashboard

---

## 📞 Support & Escalation

### If Issues Arise
1. Check Vercel logs: `vercel logs --follow`
2. Check database: Supabase dashboard
3. Check email delivery: SMTP logs
4. Check Zoho webhooks: Zoho Desk webhook logs

### Emergency Contacts
- DevOps Team: devops@company.com
- Database Admin: dba@company.com
- Zoho Support: support ticket
- Vercel Support: vercel.com/support

---

**Deployment Owner**: DevOps Team
**Approved By**: Technical Lead
**Deployment Window**: After completing 7/7 scenario tests
**Rollback Window**: 24 hours post-deployment
