# Architecture Guide

## Runtime Architecture

### Client/Server Component Boundary
- **Server Components**: All `/app` pages (except forms/booking)
- **Client Components**: Booking forms (`booking/page.tsx`), interactive dashboards (`barber/page.tsx`)
- **API Routes**: Pure server-side logic (`/api/**`)

### Data Flow Patterns
1. **Static Generation**: Landing pages use Next.js SSG
2. **Server Actions**: Form submissions via API routes with Zod validation
3. **Real-time Updates**: Barber dashboard auto-refreshes every 60s
4. **Error Boundaries**: API-level error handling with graceful degradation

## Build/Runtime Configurations

### next.config.js
```javascript
{
  eslint: { ignoreDuringBuilds: false }  // Strict mode enabled
}
```
**Why**: Fails build on ESLint errors for production safety

### tsconfig.json
- Standard Next.js 14 App Router configuration
- Path mapping: `@/` → `src/`
- Target: ES2020 for modern optimizations

## Performance & Caching Strategy

### Cache Layers
1. **Next.js Static Cache**: Landing pages and static assets
2. **Redis Cache**: Availability slots (60s TTL), graceful fallback to no-cache
3. **Browser Cache**: API responses with conditional headers

### Redis Implementation
- **Optional Dependency**: Server runs without Redis (in-memory fallback)
- **Configured in**: `src/lib/redis.ts`
- **TTL Strategy**: 60s for availability, immediate purge on booking

### Performance Hotspots
- **Availability API**: Frequent queries during booking flow
- **Email Generation**: Resend API calls (>200ms)
- **ICS Downloads**: Base64 encoding for calendar files

## Error Handling Strategy

### API Error Patterns
```typescript
// Consistent API responses
{ success?: boolean, error?: string, data?: object }
```

### Graceful Degradations
- **Email Failure**: Falls back to ICS download
- **Redis Unavailable**: Continues without caching
- **Prisma Disconnected**: Returns zero-value metrics

## Windows/OneDrive Considerations

### Known Issues
- **EPERM Errors**: File locking during Prisma client regeneration
- **Path Resolution**: Long OneDrive paths may cause issues

### Remediation
- Stop dev server before `prisma generate`
- Use relative paths where possible
- Windows-specific npm scripts handle process cleanup

## Scalability Considerations

### Database Design
- **Unique Constraints**: Prevent logical duplicates
- **Composite Indexes**: Optimize barber/time queries
- **Migration-Ready**: Easy SQLite → PostgreSQL transition

### Service Boundaries
- **Monolithic**: Single Next.js deployment
- **External Dependencies**: Resend, Stripe, Redis (optional)
- **Stateless Design**: Auto-scaling compatible

## Security Patterns

### Data Protection
- **Environment Isolation**: Separate dev/prod configurations
- **Input Validation**: Zod schemas on all API boundaries
- **SQL Injection**: Prisma ORM protection
- **Rate Limiting**: Built-in Next.js protection

### API Security
- **No Authentication**: Currently open system (planned Clerk integration)
- **Idempotency Keys**: Prevent duplicate spam
- **Input Sanitization**: React Hook Form + Zod validation

