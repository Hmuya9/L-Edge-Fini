# New Contributor Reading Guide

## Quick Start Path

### First Time Setup (15 minutes)
1. **Read**: `/docs/00_OVERVIEW.md` - Understand product scope
2. **Follow**: `/docs/60_QUALITY_GATES.md` - Local development runbook  
3. **Start**: `npm run dev -- --port 9999`
4. **Verify**: Visit `http://localhost:9999/booking` → see time slots

### Core Functionality Understanding (30 minutes)
1. **Read**: `/docs/50_WORKFLOWS.md` - Complete booking flow sequence
2. **Examine**: `src/app/booking/page.tsx` - Client-side booking form
3. **Study**: `src/app/api/bookings/route.ts` - Server-side booking logic
4. **Test**: Create a booking → verify email/ICS generation

### Architecture Deep Dive (45 minutes)
1. **Read**: `/docs/10_ARCHITECTURE.md` - System design patterns
2. **Review**: `/docs/30_DATA_MODEL.md` - Database schema understanding
3. **Examine**: `src/lib/notify.ts` - Email/fallback architecture
4. **Study**: `src/lib/hours.ts` - Availability generation logic

## Component Location Map

### Frontend Pages (`src/app/`)
- `/` → `src/app/page.tsx` - Landing with testimonials + KPIs
- `/plans` → `src/app/plans/page.tsx` - Subscription plan selection
- `/booking` → `src/app/booking/page.tsx` - Main booking form
- `/barber` → `src/app/barber/page.tsx` - Barber dashboard
- `/admin` → `src/app/admin/page.tsx` - Admin metrics view

### API Endpoints (`src/app/api/`)
- `/api/bookings` → Core booking POST/GET logic
- `/api/availability` → Time slot generation for barbers
- `/api/bookings/ics/[id]` → Calendar file downloads
- `/api/admin/metrics` → Dashboard KPIs
- `/api/reviews` → Testimonial data

### Shared Libraries (`src/lib/`)
- `db.ts` - Prisma client singleton
- `notify.ts` - Email service with Resend integration
- `calendar.ts` - iCalendar (.ics) file generation
- `hours.ts` - Barber working hours configuration
- `env.ts` - Environment variable parsing
- `redis.ts` - Optional caching layer

### UI Components (`src/components/`)
- `ui/` - shadcn/ui primitives (button, card, alert, etc.)
- `PricingCard.tsx` - Plan selection component
- `MetricCard.tsx` - Admin dashboard KPI display
- `Navbar.tsx` - Site navigation
- `PlanCard.tsx` - Individual plan display

## Key Concepts Glossary

### Plans
- **Standard Plan**: 2 cuts/month at shop ($39.99/month)
- **Deluxe Plan**: 2 cuts/month customer's location ($60/month)  
- **Free Trial**: One free cut to try service (limit: 1 per customer)

### Appointment Types
- **SHOP**: Cut performed at barbershop location
- **HOME**: Cut performed at customer's location

### Appointment Statuses
- **BOOKED**: Initial status when appointment created
- **CONFIRMED**: Barber acknowledges appointment
- **COMPLETED**: Service provided successfully
- **NO_SHOW**: Customer didn't arrive
- **CANCELED**: Appointment cancelled by customer/barber

### User Roles
- **CLIENT**: End customers who book appointments
- **BARBER**: Service providers (Mike, Alex currently seeded)
- **OWNER**: Admin access to dashboard and metrics

### Technical Terms
- **Idempotency Key**: Unique identifier preventing duplicate bookings
- **Availability Slots**: 30-minute time windows barbers can work
- **ICS File**: Calendar invitation format (.ics = iCalendar)
- **Conflict Detection**: Logic preventing overlapping appointments

## Feature Flags & Configuration

### Email Integration Status
```typescript
// Check email capability
const canEmail = !!process.env.RESEND_API_KEY
```

### Stripe Integration Status  
```typescript
// Check payment capability
const hasStripe = !!process.env.STRIPE_SECRET_KEY
```

### Redis Caching Status
```typescript
// Check cache capability  
const hasCache = !!process.env.REDIS_URL
```

## Common Development Patterns

### Error Handling
```typescript
// Consistent API error responses
if (error) {
  return NextResponse.json({ error: "User-friendly message" }, { status: 400 });
}
```

### Form Validation
```typescript
// Zod schema validation
const schema = z.object({...});
const data = schema.parse(body);
```

### Database Operations
```typescript
// Prisma client usage
const appointment = await prisma.appointment.create({
  data: { ... },
  include: { client: true, barber: true }
});
```

### Idempotent Requests
```typescript
// Prevent duplicate bookings
const idempotencyKey = crypto.createHash('sha256')
  .update(`${email}|${barberId}|${startAt}`)
  .digestr('hex');
```

## Debugging Common Issues

### No Time Slots Available
1. Check barber working hours: `src/lib/hours.ts`
2. Verify appointment conflicts: Query existing appointments
3. Confirm date is future/non-working day

### Email Not Sending
1. Verify `RESEND_API_KEY` environment variable
2. Check `NOTIFY_FROM` formatting
3. Confirm domain verification in Resend

### Booking Conflicts
1. Run duplicate cleanup: `npm run db:dedupe`
2. Check unique constraint violations
3. Verify timezone handling consistency

### Windows Development Issues
1. Stop dev server before running `prisma generate`
2. Use PowerShell `;` separator instead of `&&`
3. Clear OneDrive sync conflicts if file locked

## External Service Dependencies

### Required Services
- **Database**: PostgreSQL (production) / SQLite (development)
- **Email**: Resend API (production) / ICS fallback (graceful degradation)

### Optional Services  
- **Payment**: Stripe (required for subscriptions) / Free trials work without
- **Cache**: Redis (performance optimization) / No cache fallback available
- **Auth**: Clerk (security) / Open system currently

## Performance Expectations

### Response Times
- Availability API: < 100ms (with cache), < 500ms (without)
- Booking submission: < 1000ms (includes email)
- Page loads: < 2000ms (first visit), < 500ms (cached)

### Scalability Limits
- SQLite: ~10 concurrent bookings per minute
- Single server: ~100 concurrent users
- Email rate: Dependent on Resend account limits

## Learning Resources

### Next.js Patterns
- [App Router Documentation](https://nextjs.org/docs/app)
- [Server Actions](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations)

### Prisma Best Practices
- [Prisma Client API](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference)
- [Migration Strategies](https://www.prisma.io/docs/concepts/components/prisma-migrate)

### Email Integration
- [Resend Documentation](https://resend.com/docs)
- [iCalendar Format](https://tools.ietf.org/html/rfc5545)

