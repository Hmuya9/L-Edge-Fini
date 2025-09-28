# AI Context Refresh - Le Fade Repository

## TL;DR (5 bullets)
- **Business model**: Haircut subscription service with Standard ($39.99) and Deluxe ($60) plans, targeting busy professionals
- **Tech stack**: Next.js 14, Prisma, Clerk auth, Stripe payments, Tailwind/shadcn UI - all properly configured
- **Current status**: Core features implemented (landing, plans, booking, admin), but ESLint v9 compatibility issues blocking clean builds
- **Design system**: Unified color palette and components exist, but some inconsistencies across pages
- **Next priority**: Fix ESLint config, add testing framework, implement Redis caching for production readiness

## Repo Status Snapshot

### Current Features Implemented
- ✅ **Landing Page**: Hero section, plans overview, testimonials, gallery placeholder
- ✅ **Plans Page**: Dynamic pricing from config, Stripe integration with fallbacks
- ✅ **Booking Page**: Form with validation, barber selection, plan-based location fields
- ✅ **Admin Dashboard**: Metrics cards, profit analysis, quick actions
- ✅ **Authentication**: Clerk middleware with graceful fallbacks
- ✅ **Database**: Prisma schema with User, Appointment, Payment, Review models
- ✅ **API Routes**: Stripe webhooks, admin metrics, booking endpoints

### Current Build/Test Status
- ✅ **TypeScript**: Passes (`npm run typecheck`) - no TS errors
- ❌ **ESLint**: Fails with v9 compatibility issues (deprecated options)
- ✅ **Build**: Works locally (`npm run build`) but ESLint warnings
- ❌ **Tests**: No test framework configured
- ❌ **CI/CD**: No GitHub Actions or automated testing

### Recent Errors/Blockers
- ESLint v9 removed `useEslintrc`, `extensions` options that Next.js 14 expects
- Package.json has ESLint v9 but Next.js 14.2.33 expects ESLint v8
- No test coverage or CI pipeline for quality gates

## Architecture & Data Flows

### Tech Stack
- **Frontend**: Next.js 14.2.33 (App Router), React 18, TypeScript 5
- **Styling**: Tailwind CSS 4.1.13, shadcn/ui components
- **Database**: Prisma 6.16.2, PostgreSQL (via DATABASE_URL)
- **Authentication**: Clerk 4.31.8 with middleware protection
- **Payments**: Stripe 18.5.0 with webhook handling
- **State**: Zustand 4.4.0, React Query 5.0.0
- **Forms**: React Hook Form 7.63.0, Zod 4.1.11 validation

### File/Module Structure
```
web/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── page.tsx           # Landing page
│   │   ├── plans/page.tsx     # Subscription plans
│   │   ├── booking/page.tsx   # Appointment booking
│   │   ├── admin/page.tsx     # Admin dashboard
│   │   ├── barber/page.tsx    # Barber dashboard
│   │   └── api/               # API routes
│   ├── components/            # Reusable components
│   │   ├── ui/               # shadcn/ui primitives
│   │   ├── PricingCard.tsx   # Plan display
│   │   ├── MetricCard.tsx    # Admin metrics
│   │   └── Navbar.tsx        # Navigation
│   ├── lib/                   # Utilities
│   │   ├── env.ts            # Environment config
│   │   ├── db.ts             # Prisma client
│   │   ├── stripe.ts         # Stripe utilities
│   │   └── auth.ts           # Auth helpers
│   └── config/
│       └── plans.ts          # Pricing configuration
├── prisma/
│   └── schema.prisma         # Database schema
└── package.json              # Dependencies
```

### Main Data Flows
1. **Client → API → DB → Stripe → Client**
   - User selects plan → Stripe checkout → Webhook confirms → DB updates
   - Booking form → API validates → Creates appointment → Sends confirmation
   - Admin dashboard → API fetches metrics → Displays KPIs

2. **Authentication Flow**
   - Clerk middleware protects `/admin`, `/barber` routes
   - Graceful fallback when Clerk not configured
   - User creation on first login

3. **Payment Flow**
   - Plans page → Stripe checkout session → Payment confirmation
   - Webhook updates subscription status in database
   - Fallback to payment links if Stripe not configured

## Design System & Consistency

### Source of Truth
- **Colors**: `tailwind.config.ts` - primary (zinc-900), accent (amber-500), success, danger, info
- **Typography**: Inter font family, consistent sizing
- **Spacing**: Tailwind spacing scale with custom values (18, 88)
- **Components**: shadcn/ui primitives (Button, Card, Input, Alert)

### Core UI Components
- **Button**: Variants (default, outline, ghost, link), sizes, asChild support
- **Card**: Consistent padding, shadows, borders
- **Input**: Form inputs with validation states
- **Alert**: Success, warning, error variants
- **Toast**: Notification system with variants

### Detected Inconsistencies
- **Loading States**: Mix of "Loading..." text and SkeletonList components
- **Error Handling**: Some console.error, some ErrorState components
- **Color Usage**: Mostly consistent, but some hardcoded colors in components
- **Spacing**: Generally consistent, but some custom spacing values

## Risks & Gaps

### Known TypeScript/ESLint Issues
- ESLint v9 compatibility with Next.js 14 (deprecated options)
- No strict TypeScript configuration
- Missing type definitions for some API responses

### Potential Build/Deploy Blockers
- ESLint failures will block Vercel deployment
- No test coverage means potential regressions
- Missing error boundaries on some pages
- No monitoring or logging for production

### Missing Pieces
- **Testing**: No unit, integration, or E2E tests
- **Caching**: No Redis implementation for performance
- **Monitoring**: No error tracking (Sentry) or performance monitoring
- **CI/CD**: No automated quality gates
- **Reviews**: Review system not fully implemented

## Actionable Next Steps

### Top 3 Most Urgent Fixes (Production Readiness)
1. **Fix ESLint Configuration**
   - Pin ESLint to v8.57.0 for Next.js 14 compatibility
   - Remove deprecated options, use flat config
   - Ensure clean builds on Vercel

2. **Add Testing Framework**
   - Set up Jest + React Testing Library
   - Add Vitest for component testing
   - Create first unit tests for critical paths

3. **Implement Error Boundaries**
   - Add global error boundary in layout
   - Replace console.error with proper error handling
   - Add toast notifications for user feedback

### Top 3 Most Valuable Improvements (UX/Business Impact)
1. **Design System Unification**
   - Standardize loading states (use SkeletonList everywhere)
   - Unify error handling patterns
   - Ensure consistent spacing and colors

2. **Redis Caching Implementation**
   - Cache booking availability
   - Cache admin metrics
   - Improve performance and reliability

3. **Reviews System Completion**
   - Implement review submission API
   - Add testimonials to landing page
   - Create admin moderation interface

## Key Files & Responsibilities

| Path | Responsibility | Consumes | Exposes | Notes |
|------|----------------|----------|---------|-------|
| `package.json` | Dependencies & scripts | - | Build commands | ESLint v9 issue |
| `next.config.js` | Next.js configuration | - | Build settings | ESLint ignored during builds |
| `tailwind.config.ts` | Design system tokens | - | Colors, spacing, fonts | Well-configured |
| `src/app/page.tsx` | Landing page | PLANS, env | Hero, plans, testimonials | Complete |
| `src/app/plans/page.tsx` | Subscription plans | PLANS, env, PricingCard | Plan selection | Stripe integration |
| `src/app/booking/page.tsx` | Appointment booking | PLANS, env | Booking form | Validation with Zod |
| `src/app/admin/page.tsx` | Admin dashboard | API, MetricCard | Business metrics | Complete |
| `src/config/plans.ts` | Pricing configuration | - | PLANS constant | Single source of truth |
| `prisma/schema.prisma` | Database schema | - | Models, relations | Well-designed |
| `src/lib/env.ts` | Environment variables | process.env | Typed env access | Graceful fallbacks |
| `src/lib/db.ts` | Database client | Prisma | prisma, db exports | Singleton pattern |
| `src/middleware.ts` | Route protection | Clerk | Auth middleware | Graceful fallbacks |
| `src/components/ui/button.tsx` | Button component | Radix Slot | Button, variants | asChild support |
| `src/components/ui/toast.tsx` | Toast component | - | Toast, variants | className support |
| `src/components/PricingCard.tsx` | Plan display | PLANS | Pricing UI | ReadonlyArray support |

## Environment Variables Matrix

| Name | Used In | Required? | Fallback | Risk |
|------|---------|-----------|----------|------|
| `NEXT_PUBLIC_APP_URL` | env.ts | No | localhost:3000 | Low |
| `DATABASE_URL` | db.ts | Yes | - | High |
| `STRIPE_SECRET_KEY` | stripe.ts | No | - | Medium |
| `NEXT_PUBLIC_STRIPE_PRICE_STANDARD` | plans.ts | No | "" | Medium |
| `NEXT_PUBLIC_STRIPE_PRICE_DELUXE` | plans.ts | No | "" | Medium |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | middleware.ts | No | - | Medium |
| `CLERK_SECRET_KEY` | middleware.ts | No | - | Medium |
| `REDIS_URL` | env.ts | No | - | Low |

## Pricing Usage Map

| File | Usage | Type |
|------|-------|------|
| `src/config/plans.ts` | Source of truth | ReadonlyArray |
| `src/app/page.tsx` | Landing page display | ReadonlyArray |
| `src/app/plans/page.tsx` | Plans page display | ReadonlyArray |
| `src/app/booking/page.tsx` | Plan selection | ReadonlyArray |
| `src/components/PricingCard.tsx` | Component props | ReadonlyArray |

## Constraints & Style

- **Base everything on the repo** - no hallucinations
- **Cite files & line ranges** where helpful
- **Be blunt about risks** - propose smallest PRs for biggest wins
- **Maintain existing functionality** - no breaking changes
- **Follow existing patterns** - consistent with current architecture

The repository is in good shape with core functionality implemented, but needs build health fixes and testing infrastructure for production readiness.
