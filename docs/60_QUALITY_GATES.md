# Quality Gates & Development Runbook

## Quality Check Commands

### TypeScript Validation
```bash
npm run typecheck
```
**Expected Output**: Zero TypeScript errors
**Current Status**: ⚠️ 6 TypeScript errors detected
- `src/app/api/availability/route.ts:47` - Null assignment issue
- `src/app/api/bookings/ics/[id]/route.ts:29` - Type mismatch  
- `src/app/api/bookings/route.ts:170` - Unknown type handling
- Multiple type incompatibilities in booking flow

### ESLint Checks
```bash
npm run lint
```
**Expected Output**: Zero linting errors/warnings
**Current Status**: ⚠️ 2 console warnings in notify.ts
- Lines 27, 206: Unexpected console statements

### Production Build
```bash
npm run build
```
**Expected Output**: Successful Next.js production build
**Current Status**: ❌ Fails due to Windows EPERM error
- Prisma generate fails on Windows due to file locking
- **Fix**: Stop dev server, run `npx prisma generate`, restart

### Test Coverage
```bash
npm run test
npm run test:coverage
```
**Expected Output**: All tests pass with coverage report
**Current Status**: ✅ Test infrastructure configured
- Vitest + Testing Library setup complete
- Test files present in `src/__tests__/`

## Local Development Runbook

### Initial Setup
```bash
# 1. Install dependencies
npm install

# 2. Environment configuration
cp .env.local.example .env.local
# Edit .env.local with your values

# 3. Database setup
npm run db:migrate  # Apply schema changes
npm run seed:reviews  # Populate initial data

# 4. Start development server
npm run dev -- --port 9999
```

### Development Workflow
```bash
# Daily development
cd web
npm run dev -- --port 9999

# Database operations  
npm run db:dedupe   # Clean duplicates
npm run db:studio   # Visual DB browser

# Quality checks
npm run typecheck   # TypeScript validation
npm run lint        # Code style checks
npm run test        # Run test suite
```

## Windows-Specific Issues

### EPERM File Locking (Critical)
**Problem**: `EPERM: operation not permitted` during Prisma operations
**Root Cause**: OneDrive file synchronization locking `.prisma/client` files
**Solution**:
1. Stop all Node.js processes: `Get-Process node | Stop-Process -Force`
2. Run: `npx prisma generate`
3. Restart dev server

**Prevention**: Avoid running Prisma commands while dev server is active

### Long Path Issues
**Problem**: OneDrive paths exceed Windows path limits
**Solution**: Use relative paths where possible, npm scripts handle absolute paths

### PowerShell Syntax
**Problem**: `&&` operator not supported in PowerShell
**Solution**: Use `;` or separate commands

## Smoke Tests

### Essential Functionality Checklist
```bash
# Server availability
curl http://localhost:9999/api/availability?barberName=Mike&date=2025-10-04

# Booking endpoint  
curl -X POST http://localhost:9999/api/bookings -H "Content-Type: application/json" -d '{"customerName":"Test","customerEmail":"test@example.com","customerPhone":"5551234567","selectedDate":"2025-10-04","selectedTime":"2:00 PM","selectedBarber":"Mike","plan":"trial"}'

# ICS download
curl http://localhost:9999/api/bookings/ics/[appointment-id]
```

**Expected Responses:**
- Availability API: 200 with slot array
- Booking API: 201 with appointment data
- ICS API: Text/calendar file download

### UI Smoke Tests
1. **Landing Page**: `/` loads, shows testimonials and KPIs
2. **Plans Page**: `/plans` shows 3 plans with CTAs
3. **Booking Flow**: `/booking` → select barber → see slots → submit
4. **Barber Dashboard**: `/barber` → see appointments → refresh works
5. **Admin Dashboard**: `/admin` → shows metrics without crashes

### Integration Tests
- **Email Flow**: Booking creates email (if Resend configured) or ICS download
- **Conflicts**: Attempt duplicate booking → get 409 error
- **Trial Validation**: Try second trial with same email → blocked

## Performance Benchmarks

### API Response Times
- **Availability API**: < 100ms (cached), < 500ms (uncached)
- **Booking API**: < 1000ms (includes email + DB)
- **ICS Generation**: < 50ms
- **Page Load**: < 2000ms (first visit), < 500ms (cached)

### Database Performance
- **Slot Generation**: ~15 slots generated in < 50ms
- **Conflict Detection**: Overlap queries < 100ms
- **Unique Constraints**: Prevent duplicate creation without performance impact

## Monitoring Checklist

### Development Monitoring
- [ ] Console logs show successful API connections
- [ ] No Prisma connection errors in startup
- [ ] Email service initializes without crashes
- [ ] Redis cache gracefully degrades when unavailable

### Production Readiness
- [ ] All TypeScript errors resolved
- [ ] ESLint warnings addressed  
- [ ] Build completes without errors
- [ ] Smoke tests pass
- [ ] Environment variables configured
- [ ] Database migrations tested

## Error Recovery Procedures

### Development Environment Recovery
1. **Database Corruption**: `rm dev.db && npm run db:migrate`
2. **File Locking**: Stop processes, clean node_modules, reinstall
3. **Environment Issues**: Delete `.env.local`, recreate from example
4. **Cache Problems**: Clear Next.js cache, restart server

### Production Issue Response
1. **Booking Failures**: Check database connection and email service
2. **Duplicate Creation**: Run deduplication script immediately  
3. **Performance Issues**: Verify Redis connectivity and optimize queries
4. **Email Delivery**: Check Resend API status and domain verification

