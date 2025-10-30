# Brand Rename Documentation

## Overview
This document outlines the changes made to rename the brand from "leFade" to "LaFade" and provides guidance for future operations.

## Changes Made

### Brand Source of Truth
- Created `web/src/lib/brand.ts` with `BRAND = "LaFade"` constant
- All user-visible text now references this constant

### Files Updated
- `web/src/app/layout.tsx` - Page titles and metadata
- `web/src/app/page.tsx` - Homepage hero and content
- `web/src/components/Navbar.tsx` - Navigation branding
- `web/src/lib/calendar.ts` - ICS calendar generation
- `web/scripts/smoke.ts` - Smoke test script
- `web/package.json` - Added smoke test scripts

### What Was NOT Changed
- Package name (`lefade` in package.json)
- Environment variable names
- API routes and URLs
- Database schema or field names
- File/folder names
- Import paths

## Future Operations

### Vercel Project Rename
If you want to rename the Vercel project:
1. Go to Vercel Dashboard → Project Settings → General → Name
2. Change from "lefade" to "lafade" (or preferred name)
3. This is cosmetic only - no functional impact

### Custom Domain Setup
If you add a custom domain:
1. Configure domain in Vercel Dashboard
2. Update `NEXT_PUBLIC_APP_URL` environment variable
3. Redeploy the application
4. Update smoke test to use new domain

### Testing
- Run `npm run smoke:local` for local testing
- Run `npm run smoke:prod` for production testing
- Smoke test verifies:
  - Health endpoint responds correctly
  - Availability API returns data
  - Brand name "LaFade" appears in UI

## Rollback Plan
If rollback is needed:
1. Revert all file changes
2. Change `BRAND` constant back to "Le Fade"
3. Redeploy application

## Notes
- All infrastructure remains stable
- No secrets or environment variables were changed
- API routes maintain backward compatibility
- Database operations unaffected
