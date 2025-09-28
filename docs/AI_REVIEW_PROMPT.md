# AI Review Prompt - Repository Analysis

## Instructions

Read the entire repository and create `/docs/AI_REVIEW_SUMMARY.md` that gives the clearest possible snapshot of where we are and what to do next. Use the repo as the single source of truth (no guesses beyond what code/configs imply). Keep it concise, decisive, and actionable.

## Output Format (in this order)

### 1. Project Goal & Current Scope (5–7 bullets)
- What this app does right now (from README, routes, pages, API, schemas)
- Who it serves (inferred from copy, models, pricing)
- What's explicitly in and out of scope today

### 2. Architecture at a Glance (repo-accurate)
- Frameworks & libs detected (Next.js version, Prisma, Stripe, Tailwind/shadcn, testing stack, etc.)
- Route tree (Next App Router) with public vs protected pages
- Backend map: API routes, Prisma models, integrations (Stripe, auth, analytics)
- Data flow diagram (Mermaid) from client → API → DB/Stripe → client

### 3. Design System & UX Consistency
- Source of truth for colors/typography/spacing (Tailwind config, CSS variables)
- Component primitives in use (e.g., Button, Card, Input), where defined
- Single source of truth for plans/pricing (file path, shape, where consumed)
- Detected inconsistencies (colors, typography, spacing, loading & error patterns)

### 4. Reliability & Build Health
- ESLint/TS config status (flag deprecated options like useEslintrc, extensions)
- Typecheck/lint/build status (based on configs & scripts)
- Error boundaries, loading skeletons, form validation usage (zod/RHF)
- Env handling (where loaded/validated, required vs optional)

### 5. Security & Compliance (repo-derived)
- Auth solution present or missing (Clerk/NextAuth/custom)
- Secrets management (env examples, .env.example, env.ts validators)
- Webhook verification patterns (Stripe, etc.)
- Basic PII/data handling notes

### 6. Deployment & Ops
- Vercel config, Node engines, CI workflows (GitHub Actions), test coverage status
- Any caching (Redis), rate-limiting, idempotency, monitoring (Sentry/Otel) present/missing
- Production-readiness risks

### 7. Key Files Table
A table with: Path | Responsibility | Consumes | Exposes | Notes
Include at least: package.json, next.config.*, tailwind.config.*, src/app/**, src/components/**, src/config/plans.ts, prisma/schema.prisma, src/lib/**, any API routes, CI files, deployment config.

### 8. Gaps vs. Goals (Ranked)
- **Blockers** (must-fix to ship)
- **High-Impact Next** (moves UX/business the most)
- **Nice-to-Have**

### 9. Seven-Day Action Plan
3–5 concrete PRs with titles, scope, acceptance criteria, owner (TBD), and test notes
- Keep each PR shippable and reversible

### 10. Appendix
- Mermaid route tree
- Mermaid data-flow diagram
- Detected env var matrix (name | used in | required? | fallback | risk)
- Detected pricing usage map (all files that read pricing)

## Constraints & Style

- Base everything on the repo—no hallucinations
- Cite files & line ranges where helpful
- Max ~1,600–1,900 words
- Be blunt about risks; propose the smallest PRs that yield the biggest wins
- When done, save to `/docs/AI_REVIEW_SUMMARY.md` and provide a short confirmation message with a 5-bullet TL;DR

## Usage

This prompt should be used when you want a comprehensive, repository-accurate analysis of the current state of Le Fade, with clear next steps and actionable recommendations based solely on what's actually in the codebase.
