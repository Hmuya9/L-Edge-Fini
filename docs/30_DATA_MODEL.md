# Data Model

## Prisma Schema Overview

**Database**: SQLite (local), PostgreSQL-ready (production)
**ORM**: Prisma 6.16.2 with generated client
**Location**: `prisma/schema.prisma`

## Core Models

### User Model
```prisma
model User {
  id        String   @id @default(cuid())
  role      Role     @default(CLIENT)
  email     String?  @unique
  phone     String?
  name      String?
  clerkId   String?  @unique
  createdAt DateTime @default(now())
  
  subscriptions Subscription[]
  clientAppts   Appointment[] @relation("ClientAppts")
  barberAppts   Appointment[] @relation("BarberAppts")
  payouts       Payout[]
}

enum Role { 
  CLIENT 
  BARBER 
  OWNER 
}
```

**Notes**: 
- Optional email (supports phone-only clients)
- Prepared for Clerk integration (`clerkId`)
- Supports future multi-role users

### Appointment Model
```prisma
model Appointment {
  id             String   @id @default(cuid())
  clientId       String
  barberId       String
  type           ApptType
  startAt        DateTime
  endAt          DateTime
  status         ApptStatus @default(BOOKED)
  address        String?
  notes          String?
  isFree         Boolean  @default(false)
  idempotencyKey String?  @unique
  
  client         User     @relation("ClientAppts", fields: [clientId], references: [id])
  barber         User     @relation("BarberAppts", fields: [barberId], references: [id])

  @@index([barberId, startAt])
  @@unique([barberId, startAt])
  @@unique([clientId, startAt])
}

enum ApptType { 
  SHOP 
  HOME 
}

enum ApptStatus { 
  BOOKED 
  CONFIRMED 
  COMPLETED 
  NO_SHOW 
  CANCELED 
}
```

**Critical Constraints**:
- `@@unique([barberId, startAt])`: Prevents double-booking barbers
- `@@unique([clientId, startAt])`: Prevents double-booking clients  
- `@@index([barberId, startAt])`: Optimizes availability queries
- `idempotencyKey`: Handles duplicate request scenarios

### Subscription & Payment Models

```prisma
model Subscription {
  id          String   @id @default(cuid())
  userId      String
  planId      String
  status      SubStatus @default(ACTIVE)
  startDate   DateTime @default(now())
  renewsAt    DateTime
  stripeSubId String   @unique
  
  user        User     @relation(fields: [userId], references: [id])
  plan        Plan     @relation(fields: [planId], references: [id])
}

model Plan {
  id           String  @id @default(cuid())
  name         String
  priceMonthly Int     // Price in cents
  cutsPerMonth Int
  isHome       Boolean @default(false)
  stripePriceId String @unique
  
  subscriptions Subscription[]
}
```

**Payment Integration**: Ready for Stripe Billing with unique price IDs

## Indexes & Performance

### Database Indexes
1. **Barber Time Slot**: `[barberId, startAt]` - Critical for availability
2. **Appointment Status**: Optimizes status-based queries
3. **User Email**: Unique constraint enables fast lookups

### Query Patterns
- **Availability**: `WHERE barberId = ? AND startAt BETWEEN ? AND ?`
- **Booking Conflicts**: Overlap detection via composite unique constraints
- **User Lookups**: Email-based client resolution

## Data Invariants

### Business Rules Enforced
1. **One Trial Per Customer**: Application-level check + unique constraints
2. **30-Minute Slots**: Enforced in availability generation logic
3. **Working Hours**: Barbers have defined schedules (see `src/lib/hours.ts`)

### Consistency Guarantees
```sql
-- No overlapping barber appointments
ALTER TABLE Appointment ADD CONSTRAINT unique_barber_time 
UNIQUE (barberId, startAt);

-- No overlapping client appointments  
ALTER TABLE Appendix ADD CONSTRAINT unique_client_time
UNIQUE (clientId, startAt);
```

## Migration History

### Current Migration: `20251002010640_sqlite_init`
- Initial schema with all tables
- Unique constraints implemented
- Index optimization applied

### Migration Scripts Available
- **Database Cleanup**: `scripts/remove-appointment-duplicates.ts`
- **Seed Data**: `scripts/seed-reviews.ts`
- **Role Seeding**: Database seeding for barbers (Mike, Alex)

## Idempotency Strategy

### Duplicate Prevention
- **Client-side**: `idempotencyKey` header on POST requests
- **Server-side**: Deterministic key generation from `email|barberId|startAt`
- **Database-level**: Unique constraints as final safety net

### Conflict Resolution
```typescript
// Example conflict scenarios handled:
// 1. Rapid double-click prevention
// 2. Network retry scenarios  
// 3. Barber time overlap
// 4. Client time overlap
// 5. Trial usage validation
```

## Data Migration Considerations

### SQLite → PostgreSQL Path
- **Schema Compatibility**: Already PostgreSQL-compatible
- **Index Strategy**: Same indexes work in both databases
- **Data Types**: DateTime fields use ISO strings (timezone-aware)

### Production Considerations
- **Backup Strategy**: Leverage provider managed backups
- **Monitoring**: Database query performance tracking needed
- **Scaling**: Plan for read replicas if booking volume grows

