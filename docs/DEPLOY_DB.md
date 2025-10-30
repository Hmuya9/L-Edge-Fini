# Database Deployment Guide (Prod: Postgres on Neon, Dev: SQLite)

This project uses Postgres in production (Vercel) and SQLite locally. Prisma's main schema is configured for Postgres. For local dev on SQLite, use a separate Prisma schema file.

## Files
- prisma/schema.prisma (Postgres – used for prod and migrations)
- prisma/schema.local.prisma (SQLite – used only for local dev commands)
- prisma/seed.ts (seeds Availability rows)

## Local Dev (SQLite)
Keep your local `.env` as:
```
DATABASE_URL="file:./dev.db"
```
Generate Prisma Client against local schema:
```
# PowerShell
cd web
npx prisma generate --schema prisma/schema.local.prisma
```
(Optional) Push local schema to SQLite (will create dev.db):
```
# PowerShell
npx prisma db push --schema prisma/schema.local.prisma
```

## Production (Postgres on Neon)

1) Create Neon Postgres
- Go to https://neon.tech
- Create project: `lefade-prod`
- Copy the connection string: `postgres://<user>:<password>@<host>/<db>?sslmode=require`

2) Set Vercel Production Envs
```
# PowerShell (from web/)
vercel env add DATABASE_URL production
# paste Neon postgres URL when prompted

# Ensure these exist (add if missing)
vercel env add RESEND_API_KEY production
vercel env add NOTIFY_FROM production
vercel env add NOTIFY_TO production
vercel env add STRIPE_SECRET_KEY production
vercel env add STRIPE_WEBHOOK_SECRET production
vercel env add NEXT_PUBLIC_APP_URL production
```

3) Author Postgres Migration (init-postgres)
Temporarily point Prisma to Neon only for migration authoring.
```
# PowerShell
cd web
$env:DATABASE_URL="<NEON_POSTGRES_URL>"
npx prisma migrate dev --name init-postgres --schema prisma/schema.prisma
```
This creates `prisma/migrations/<timestamp>_init-postgres/` with SQL. Review it.

4) Apply Migration to Neon and Generate Client
```
# PowerShell
$env:DATABASE_URL="<NEON_POSTGRES_URL>"
# Ensure DB is up to date (safe for prod if empty/new)
npx prisma migrate deploy --schema prisma/schema.prisma
# Generate client against Postgres
npx prisma generate --schema prisma/schema.prisma
```

5) Seed Availability (Mike & Alex)
The seed uses future dates. Run it against Neon:
```
# PowerShell
$env:DATABASE_URL="<NEON_POSTGRES_URL>"
npx tsx prisma/seed.ts
```

6) Redeploy to Vercel (Production)
```
# PowerShell
vercel --prod
```
Wait for build to complete.

7) Verify
- API (Prod):
```
# Replace <app> and <YYYY-MM-DD> with a seeded date
curl "https://<app>.vercel.app/api/availability?barberName=Mike&date=<YYYY-MM-DD>"
```
- UI (Prod):
Visit `https://<app>.vercel.app/booking`, select Mike and the seeded date – time slots should appear.

## Notes / Troubleshooting
- Provider mismatch: The main schema is Postgres-only. Use `--schema prisma/schema.local.prisma` for local SQLite commands.
- Local Client regen:
```
# PowerShell
npx prisma generate --schema prisma/schema.local.prisma
```
- If migration reset is required: Create a new migration name (e.g., `init-postgres`) and run migrate dev against Neon as above.
- Prisma Studio (local):
```
# PowerShell
npx prisma studio --schema prisma/schema.local.prisma
```
- Prisma Studio (Neon):
```
# PowerShell
$env:DATABASE_URL="<NEON_POSTGRES_URL>"
npx prisma studio --schema prisma/schema.prisma
```
