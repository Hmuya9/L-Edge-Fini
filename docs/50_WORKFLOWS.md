# Workflow Documentation

## Core Booking Flow

### Sequence Diagram: Complete Booking Process
```mermaid
sequenceDiagram
    participant Customer as Customer
    participant UI as Booking UI
    participant AvailAPI as Availability API
    participant BookAPI as Booking API
    participant DB as Database
    participant Email as Resend Email
    participant ICS as ICS Generator

    Note over Customer: Plans Page Selection
    Customer->>UI: Click plan CTA → /booking?plan=trial
    UI->>UI: Preselect trial plan

    Note over Customer: Date/Time Selection  
    Customer->>UI: Select barber (Mike/Alex)
    UI->>AvailAPI: GET /api/availability?barberName=Mike&date=2025-10-04
    AvailAPI->>DB: Query existing appointments
    AvailAPI-->>UI: Return 15 available slots
    UI->>UI: Filter + display time buttons
    Customer->>UI: Select 2:00 PM slot

    Note over Customer: Booking Submission
    Customer->>UI: Fill form + submit
    UI->>BookAPI: POST /api/bookings with idempotency-key
    BookAPI->>DB: Check existing by idempotency-key
    BookAPI->>BookAPI: Validate trial usage (one per customer)
    BookAPI->>BookAPI: Check barber time conflicts
    BookAPI->>DB: Create user + appointment
    BookAPI->>Email: Send customer confirmation + internal alert
    BookAPI->>ICS: Generate calendar file
    BookAPI-->>UI: Return success + icsUrl
    
    alt Email sent successfully
        Note over Customer: Email received with calendar invite
    else Email failed  
        UI->>ICS: Show "Add to Calendar" download button
    end
```

## Availability Checking Flow

### Time Slot Generation Logic
```mermaid
flowchart TD
    A[Request Availability] --> B{Barber Exists?}
    B -->|No| C[404 Error]
    B -->|Yes| D[Check Working Days]
    D --> E{Is Working Day?}
    E -->|No| F[Return Empty Slots]
    E -->|Yes| G[Generate 30-min Slots]
    G --> H[Query Existing Appointments]
    H --> I[Apply Conflict Detection]
    I --> J{Any Conflicts?}
    J -->|Yes| K[Remove Conflicting Slots]
    J -->|No| L[Keep All Slots]
    K --> M[Return Available Slots]
    L --> M
    
    style G fill:#e1f5fe
    style M fill:#c8e6c9
```

### Working Hours Configuration
```typescript
// src/lib/hours.ts
const BARBER_HOURS = {
  Mike: {
    start: '09:00',
    end: '17:30',
    workingDays: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  },
  Alex: {
    start: '10:00',
    end: '16:30', 
    workingDays: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
  }
}
```

## Deduplication & Unique Constraints

### Duplicate Prevention Strategy
```mermaid
sequenceDiagram
    participant Client as Client Request
    participant API as Booking API
    participant DB as Database
    participant Email as Email Service

    Client->>API: POST /bookings (first attempt)
    API->>DB: Generate idempotency-key (hash of email+barber+time)
    API->>DB: Check for existing booking with same key
    alt No existing booking
        API->>DB: Create new appointment
        API->>Email: Send confirmation
        API-->>Client: 201 Created + success response
    else Existing booking found
        API-->>Client: Return existing booking (idempotent)
    end

    Note over Client: Double-click scenario
    Client->>API: POST /bookings (rapid retry)
    API->>DB: Check idempotency-key (matches existing)
    API-->>Client: Return same booking (no duplicate created)
```

### Database Constraints
1. **Barber Time Slot**: `@@unique([barberId, startAt])`
2. **Client Time Slot**: `@@unique([clientId, startAt])`
3. **Idempotency**: `@@unique([idempotencyKey])`

### Cleanup Process
```bash
# Remove duplicates before applying constraints
npm run db:dedupe

# Apply schema changes
npm run db:migrate
```

## Plans → Booking Integration

### URL-Based Plan Selection
```typescript
// src/app/booking/page.tsx
const searchParams = useSearchParams();
const initialPlan = searchParams.get('plan') || 'standard';

// Form preselects based on URL
defaultValues: {
  plan: initialPlan as "standard" | "deluxe" | "trial"
}
```

### Plan Flow Mapping
- `/plans` → Click "Free Trial" → `/booking?plan=trial`
- `/plans` → Click "Get Standard" → `/booking?plan=standard`  
- `/plans` → Click "Get Deluxe" → `/booking?plan=deluxe`

## Barber Dashboard Workflow

### Auto-Refresh Pattern
```typescript
// src/app/barber/page.tsx
useEffect(() => {
  fetchAppointments();
  
  // Auto-refresh every 60 seconds
  const interval = setInterval(() => {
    fetchAppointments();
  }, 60000);

  return () => clearInterval(interval);
}, []);
```

### Status Transition Flow
```mermaid
stateDiagram-v2
    [*] --> BOOKED: New appointment created
    BOOKED --> CONFIRMED: Barber confirms
    CONFIRMED --> COMPLETED: Service completed
    COMPLETED --> [*]: Processed
    
    BOOKED --> CANCELED: Customer/barber cancels
    CONFIRMED --> CANCELED: Customer/barber cancels
    
    BOOKED --> NO_SHOW: Customer doesn't arrive
    CONFIRMED --> NO_SHOW: Customer doesn't arrive
    
    note right of BOOKED
      Default status for 
      new bookings
    end note
```

## Email Integration Workflow

### Email Service Initialization
```typescript
// src/lib/notify.ts
const resend = process.env.RESEND_API_KEY 
  ? new Resend(process.env.RESEND_API_KEY) 
  : null;

export async function sendBookingEmail(appointment) {
  if (!resend || !process.env.NOTIFY_FROM) {
    return { emailed: false, reason: 'no-resend' };
  }
  
  // Send customer + internal emails
  // Return success/failure status
}
```

### Email Fallback Chain
1. **Primary**: Resend API with HTML template + ICS attachment
2. **Fallback**: ICS download link + UI notification
3. **Graceful**: Check console logs show "email skipped" message

## Data Migration Workflows

### Development to Production
```bash
# 1. Backup existing data
npm run db:studio  # Export via Prisma Studio

# 2. Clean production database  
npm run db:dedupe  # Remove duplicates

# 3. Apply schema changes
npm run db:migrate # Push latest schema

# 4. Seed production data
npm run seed:reviews
```

### SQLite to PostgreSQL Migration
1. **Schema Export**: `prisma db push --preview-feature`
2. **Data Migration**: Export/import via Prisma migrations
3. **Environment Update**: Change `DATABASE_URL` to PostgreSQL
4. **Validation**: Run full test suite against new database

