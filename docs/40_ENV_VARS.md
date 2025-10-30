# Environment Variables Guide

## Core Requirements

### DATABASE_URL
- **Required**: ✅ Yes (breaks Prisma if missing)
- **Usage**: All database operations via Prisma client
- **Dev Default**: `file:./dev.db` (SQLite)
- **Prod Value**: PostgreSQL connection string
- **Files**: `prisma/schema.prisma`, `src/lib/db.ts`
- **Fallback**: None - hard requirement

### NEXT_PUBLIC_APP_URL  
- **Required**: ⚠️ Recommended (used in emails)
- **Usage**: Email templates, ICS generation, webhook validation
- **Dev Default**: `http://localhost:9999`
- **Prod Value**: `https://lefade.com` (actual domain)
- **Files**: `src/lib/notify.ts`, `src/lib/calendar.ts`
- **Fallback**: Defaults to localhost, email links become invalid

## Email Integration (Resend)

### RESEND_API_KEY
- **Required**: ❌ No (graceful degradation)
- **Usage**: Client initialization for `src/lib/notify.ts`
- **Dev Default**: None
- **Prod Value**: `re_xxxxx` API key from Resend dashboard
- **Files**: `src/lib/notify.ts`
- **Fallback**: System falls back to ICS downloads only

### NOTIFY_FROM
- **Required**: ❌ No (only if RESEND_API_KEY present)
- **Usage**: Email sender identity
- **Dev Default**: `"Le Fade <no-reply@lefade.com>"`
- **Prod Value**: `"Le Fade <no-reply@yourdomain.com>"` *(verified domain required)*
- **Files**: `src/lib/notify.ts`
- **Fallback**: Uses default sender name

### NOTIFY_TO
- **Required**: ❌ No (only if RESEND_API_KEY present)
- **Usage**: Internal booking notifications
- **Dev Default**: `"bookings@lefade.com"`
- **Prod Value**: Admin/staff email address
- **Files**: `src/lib/notify.ts`
- **Fallback**: Uses default admin email

## Payment Integration (Stripe)

### STRIPE_SECRET_KEY
- **Required**: ❌ No (optional payment processing)
- **Usage**: Server-side Stripe operations
- **Dev Default**: None
- **Prod Value**: `sk_live_xxxxx` from Stripe dashboard
- **Files**: `src/lib/stripe.ts`, API routes
- **Fallback**: Plan selection works without payments

### NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY
- **Required**: ❌ No (optional payment processing)
- **Usage**: Client-side payment processing
- **Dev Default**: None
- **Prod Value**: `pk_live_xxxxx` from Stripe dashboard
- **Files**: Client-side payment forms
- **Fallback**: Payment forms disabled gracefully

### STRIPE_WEBHOOK_SECRET
- **Required**: ❌ No (optional webhook processing)
- **Usage**: Verifies Stripe webhook signatures
- **Dev Default**: None
- **Prod Value**: `whsec_xxxxx` from Stripe webhook settings
- **Files**: `src/app/api/stripe/webhook/route.ts`
- **Fallback**: Webhooks processed without signature verification

### NEXT_PUBLIC_STRIPE_PRICE_STANDARD
- **Required**: ❌ No (plan selection works without)
- **Usage**: Standard plan pricing ID
- **Dev Default**: None
- **Prod Value**: `price_xxxxx` from Stripe dashboard
- **Files**: `src/app/plans/page.tsx`
- **Fallback**: Plan shows as "Coming Soon" with free trial option

### NEXT_PUBLIC_STRIPE_PRICE_DELUXE
- **Required**: ❌ No (plan selection works without)
- **Usage**: Deluxe plan pricing ID
- **Dev Default**: None
- **Prod Value**: `price_xxxxx` from Stripe dashboard
- **Files**: `src/app/plans/page.tsx`
- **Fallback**: Plan shows as "Coming Soon" with free trial option

## Cache Layer (Redis)

### REDIS_URL
- **Required**: ❌ No (optional performance optimization)
- **Usage**: Caching availability slots and session data
- **Dev Default**: None
- **Prod Value**: Redis connection string
- **Files**: `src/lib/redis.ts`, `src/app/api/availability/route.ts`
- **Fallback**: System runs without caching (slightly slower)

## External Integrations

### NEXT_PUBLIC_CALENDLY_URL
- **Required**: ❌ No (optional alternative booking)
- **Usage**: External scheduling fallback link
- **Dev Default**: None
- **Prod Value**: Calendly scheduling URL
- **Files**: `src/lib/env.ts`
- **Fallback**: No external booking integration

## Environment Configuration Files

### Development Setup
```bash
# web/.env.local (copy from example)
DATABASE_URL="file:./dev.db"
FORM = "Le Fade <no-reply@yourdomain.com>"
RESEND_API_KEY="your_key_here"
NEXT_PUBLIC_APP_URL="http://localhost:9999"
```

### Production Setup
```bash
# Production environment variables
DATABASE_URL="postgresql://..."
NEXT_PUBLIC_APP_URL="https://lefade.com"
RESEND_API_KEY="re_xxxxx"
NOTIFY_FROM="Le Fade <no-reply@lefade.com>"
NOTIFY_TO="admin@lefade.com"
STRIPE_SECRET_KEY="sk_live_xxxxx"
# ... additional Stripe vars
REDIS_URL="redis://..."
```

## Missing Variable Consequences

### Critical (Breaks System)
- **DATABASE_URL**: Prisma fails to initialize, all DB operations fail

### Functional Degradation
- **RESEND_***: Email confirmations disabled, ICS downloads still work
- **STRIPE_***: Payment processing disabled, free trials still work
- **REDIS_URL**: No caching, slightly slower availability checks

### Quality Impact
- **NEXT_PUBLIC_APP_URL**: Email links point to localhost (broken UX)
- **NOTIFY_****: Admin doesn't get booking notifications

## Environment Guards

### Code Protection Patterns
```typescript
// Email service gracefully degrades
const resend = process.env.RESEND_API_KEY ? new Resend(process.env.RESEND_API_KEY) : null

// Plans page shows "coming soon" without Stripe
const missingStripe = !env.stripeStandard || !env.stripeDeluxe

// ICS fallback when email fails
return { emailed: false, icsUrl: "/api/bookings/ics/appointment"}
```

