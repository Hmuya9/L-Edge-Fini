# Dub.co Self-Hosting Guide

## Overview

This guide provides a comprehensive walkthrough for self-hosting Dub – the open-source link attribution platform. Self-hosting gives you complete control over your data, design, and infrastructure while maintaining all the powerful features of Dub.

## Prerequisites

Before beginning the self-hosting process, ensure you have:

### Required Accounts
- **GitHub account** - For code repository and OAuth
- **Tinybird account** - For ClickHouse analytics database
- **Upstash account** - For Redis caching and queuing
- **PlanetScale account** - For MySQL database (or Railway alternative)
- **Vercel account** - For hosting and deployment
- **Cloudflare or AWS account** - For storage and CDN

### Domain Requirements
- **Custom domain** for your Dub instance (e.g., `app.acme.com`)
- **Optional short domain** for links (e.g., `ac.me`)

## Step-by-Step Setup

### Step 1: Local Setup

#### 1. Clone the Repository
```bash
git clone https://github.com/dubinc/dub.git
cd dub
```

#### 2. Install Dependencies
```bash
pnpm i
```

#### 3. Remove Vercel-specific Files
```bash
rm apps/web/vercel.json
```

#### 4. Environment Configuration
Create `.env` file from `.env.example`:

```env
# Domain Configuration
NEXT_PUBLIC_APP_DOMAIN=acme.com
NEXT_PUBLIC_APP_SHORT_DOMAIN=ac.me

# Vercel Configuration (to be filled later)
TEAM_ID_VERCEL=
AUTH_BEARER_TOKEN=
```

### Step 2: Tinybird ClickHouse Database Setup

#### 1. Create Tinybird Workspace
- Sign up at [Tinybird](https://tinybird.co)
- Create a new workspace
- Copy your admin Auth Token

#### 2. Install Tinybird CLI
```bash
cd packages/tinybird
pip install tinybird-cli
tb login
# Paste your admin Auth Token when prompted
```

#### 3. Deploy Datasources and Endpoints
```bash
tb deploy
```

Expected output:
```
$ tb deploy

** Processing ./datasources/click_events.datasource
** Processing ./endpoints/clicks.pipe
...
** Building dependencies
** Running 'click_events'
** 'click_events' created
** Running 'device'
** => Test endpoint at https://api.us-east.tinybird.co/v0/pipes/device.json
** Token device_endpoint_read_8888 not found, creating one
** => Test endpoint with:
** $ curl https://api.us-east.tinybird.co/v0/pipes/device.json?token=p.ey...NWeaoTLM
** 'device' created
```

#### 4. Configure Tinybird Environment Variables
Add to your `.env` file:
```env
TINYBIRD_API_KEY=your_admin_auth_token
TINYBIRD_API_URL=https://api.us-east.tinybird.co
```

### Step 3: Upstash Redis Database Setup

#### 1. Create Upstash Database
- Sign up at [Upstash](https://upstash.com)
- Create a new Redis database
- **Recommended**: Set up global database with multiple read regions for better performance

#### 2. Configure Redis Environment Variables
From your Upstash dashboard, copy the following values to your `.env`:

```env
# REST API Section
UPSTASH_REDIS_REST_URL=your_redis_rest_url
UPSTASH_REDIS_REST_TOKEN=your_redis_rest_token

# QStash Section
QSTASH_TOKEN=your_qstash_token
QSTASH_CURRENT_SIGNING_KEY=your_current_signing_key
QSTASH_NEXT_SIGNING_KEY=your_next_signing_key
```

### Step 4: PlanetScale MySQL Database Setup

#### 1. Create PlanetScale Database
- Sign up at [PlanetScale](https://planetscale.com)
- Create a new database
- Select **Prisma** as your framework

#### 2. Configure Database Environment Variables
- Create a new password for your database
- Copy the `DATABASE_URL` from the "Add credentials to .env" section

```env
DATABASE_URL="mysql://username:password@host:port/database"
```

#### 3. Update Prisma Schema
Navigate to `apps/web/prisma/schema.prisma` and update the `DefaultDomains` model:

```prisma
model DefaultDomains {
  id          String   @id @default(cuid())
  acme        Boolean  @default(true)  // Replace with your domain (e.g., acme for ac.me)
  projectId   String   @unique
  project     Project  @relation(fields: [projectId], references: [id], onDelete: Cascade)
}
```

#### 4. Generate Prisma Client and Create Tables
```bash
cd apps/web
pnpm run prisma:generate
pnpm run prisma:push
```

### Step 5: GitHub OAuth Setup

#### 1. Create GitHub App
- Go to [GitHub Developer Settings](https://github.com/settings/developers)
- Create a new GitHub App
- Set the following callback URLs:
  - `https://app.acme.com/api/auth/callback/github`
  - `http://localhost:8888/api/auth/callback/github` (for local development)

#### 2. Configure GitHub Environment Variables
```env
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret
```

### Step 6: Cloudflare R2 Storage Setup

#### 1. Create R2 Bucket
- Sign up for [Cloudflare R2](https://cloudflare.com/products/r2/)
- Create a new R2 bucket (e.g., `dubassets`)
- Copy the S3 API endpoint

#### 2. Create API Token
- Go to "Manage R2 API Tokens"
- Create a new API token with "Object Read & Write" permissions
- Apply to your specific bucket
- Copy the Access Key ID and Secret Access Key

#### 3. Configure R2 Environment Variables
```env
STORAGE_ACCESS_KEY_ID=your_access_key_id
STORAGE_SECRET_ACCESS_KEY=your_secret_access_key
STORAGE_ENDPOINT=your_s3_api_endpoint
STORAGE_BASE_URL=https://static.example.com  # Your custom domain or R2.dev subdomain
```

#### 4. Set up R2 Domain
- **Option A**: Use your own domain with CNAME record
- **Option B**: Use R2.dev subdomain (ensure "Allow Access" is enabled)

### Step 7: Resend Email Setup (Optional but Recommended)

#### 1. Create Resend Account
- Sign up at [Resend](https://resend.com)
- Create an API key

#### 2. Configure Resend Environment Variables
```env
RESEND_API_KEY=your_resend_api_key
```

#### 3. Verify Domain
- Follow [Resend's domain verification guide](https://resend.com/docs/dashboard/domains/introduction)
- Add required DNS records

### Step 8: Unsplash Integration (Optional)

#### 1. Create Unsplash Application
- Go to [Unsplash Developers](https://unsplash.com/developers)
- Create a new application
- Copy the access key

#### 2. Configure Unsplash Environment Variables
```env
UNSPLASH_ACCESS_KEY=your_unsplash_access_key
```

### Step 9: Vercel Deployment

#### 1. Deploy to GitHub
```bash
git add .
git commit -m "Initial commit"
git push origin main
```

#### 2. Create Vercel Project
- Go to [Vercel Dashboard](https://vercel.com/dashboard)
- Create a new project
- Import your GitHub repository
- **Important**: Set Framework Preset to "Next.js" and Root Directory to "apps/web"

#### 3. Configure Environment Variables
Add all environment variables from your `.env` file to Vercel:
- Remove `PROJECT_ID_VERCEL` (will be added after deployment)
- Update `NEXTAUTH_URL` to your app domain (e.g., `https://app.acme.com`)

#### 4. Deploy and Configure
- Click "Deploy" to deploy your project
- After deployment, retrieve your Vercel project ID
- Add `PROJECT_ID_VERCEL` to both your `.env` and Vercel environment variables
- Add your domains in Vercel Settings > Domains

#### 5. Redeploy
- Go to the Deployments page
- Redeploy your project
- Visit your app domain to verify the deployment

## Le Fade Integration Strategy

### 1. **Custom Domain Setup**
```env
# Le Fade specific configuration
NEXT_PUBLIC_APP_DOMAIN=lefade.com
NEXT_PUBLIC_APP_SHORT_DOMAIN=lf.co
```

### 2. **Branding Customization**
- Update logo and colors to match Le Fade branding
- Customize email templates with Le Fade messaging
- Configure custom social media card templates

### 3. **API Integration**
```typescript
// lib/dub-api.ts
export async function createShortLink(longUrl: string, options: {
  key?: string
  title?: string
  description?: string
  image?: string
}) {
  const response = await fetch('https://your-dub-instance.com/api/links', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${process.env.DUB_API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      url: longUrl,
      domain: 'lf.co',
      ...options
    })
  })
  
  return response.json()
}
```

### 4. **Analytics Integration**
```typescript
// lib/dub-analytics.ts
export async function getLinkAnalytics(linkId: string) {
  const response = await fetch(`https://your-dub-instance.com/api/analytics/${linkId}`, {
    headers: {
      'Authorization': `Bearer ${process.env.DUB_API_KEY}`
    }
  })
  
  return response.json()
}
```

## Cost Considerations

### Monthly Costs (Approximate)
- **Tinybird**: $25-50 (depending on usage)
- **Upstash**: $10-20 (Redis + QStash)
- **PlanetScale**: $29+ (or Railway $5/month alternative)
- **Cloudflare R2**: $5-15 (storage + requests)
- **Vercel**: $20+ (Pro plan)
- **Resend**: $20+ (for transactional emails)
- **Total**: ~$100-150/month

### Cost Optimization
- Use Railway instead of PlanetScale for MySQL ($5 vs $29)
- Start with smaller Tinybird plan and scale up
- Use R2.dev subdomain instead of custom domain initially
- Consider self-hosting Redis and MySQL for higher volume

## Security Considerations

### 1. **Environment Variables**
- Never commit `.env` files to version control
- Use Vercel's environment variable encryption
- Rotate API keys regularly

### 2. **Database Security**
- Enable SSL for all database connections
- Use strong passwords and API keys
- Regularly backup your databases

### 3. **Domain Security**
- Use HTTPS for all domains
- Configure proper CORS settings
- Implement rate limiting

## Maintenance and Updates

### 1. **Regular Updates**
- Keep dependencies updated
- Monitor security advisories
- Update Dub to latest versions

### 2. **Monitoring**
- Set up uptime monitoring
- Monitor database performance
- Track API usage and costs

### 3. **Backups**
- Regular database backups
- Backup configuration files
- Test restore procedures

## Troubleshooting

### Common Issues
1. **Build failures**: Check environment variables
2. **Database connection errors**: Verify connection strings
3. **OAuth issues**: Check callback URLs
4. **Storage errors**: Verify R2 credentials

### Support Resources
- [Dub GitHub Issues](https://github.com/dubinc/dub/issues)
- [Dub Discord Community](https://discord.gg/dub)
- [Documentation](https://dub.co/docs)

## Next Steps for Le Fade

1. **Set up self-hosted Dub** following this guide
2. **Configure Le Fade branding** and domains
3. **Integrate with Le Fade booking system** via API
4. **Set up analytics tracking** for marketing campaigns
5. **Monitor performance** and optimize costs

This self-hosted setup gives Le Fade complete control over their link management and analytics while maintaining professional features and scalability.


