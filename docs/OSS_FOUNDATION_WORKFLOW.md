# OSS Foundation Workflow - Le Fade

## Overview

This document outlines our 2-Model Workflow for building Le Fade on proven open-source foundations, avoiding reinvention and ensuring production-quality code.

## The 2-Model Workflow

### Model Roles
- **Claude 3.5 Sonnet** → Repo-wide reading, audits, refactor plans (builds the map)
- **GPT-5** → Surgical edits, tests, PRs (drives the car)

### Workflow Pattern
1. **Claude (AM)**: Run repo audit, propose focused plan ("3 fixes only")
2. **GPT-5 (Mid-day)**: Implement + tests + PR
3. **Claude (PM)**: Review PR, check architectural drift, update docs

## Proven OSS Bases to Copy

### 1. **Design System & App Shell**
- **Source**: shadcn/taxonomy (Next.js + shadcn/ui patterns)
- **Copy**: Layout structure, typography, card primitives
- **Target**: Unified design system for Le Fade

### 2. **Commerce-Grade UI/UX Patterns**
- **Source**: Vercel Next.js Commerce
- **Copy**: Production patterns, routing, performance optimizations
- **Target**: Premium Plans/Landing pages

### 3. **Scheduling/Booking**
- **Source**: Cal.com (full open-source)
- **Copy**: Embed widget for instant booking
- **Target**: Drop-in booking solution, migrate later

### 4. **Repo Quality Patterns**
- **Source**: Dub.co docs
- **Copy**: Clean self-hosting, env, CI conventions
- **Target**: Professional development workflow

## Day-1 Setup: Context + Guardrails

### Branch Strategy
```bash
git checkout -b ops/oss-foundation
```

### Claude Tasks (Repo Map)
1. **Dependency Graph**: Pages, components, lib, API relationships
2. **Design Tokens**: Colors, spacing, typography system
3. **Routing Tree**: Static + dynamic routes analysis
4. **Data Flows**: Env → API → Prisma → UI
5. **Gaps vs Goals**: Booking, pricing, dashboards analysis

**Deliverable**: Markdown map + TODO list grouped by:
- **Blockers**: Must fix before launch
- **High Impact**: Significant user experience improvements
- **Nice to Have**: Polish and optimization

### GPT-5 Tasks (Quality Gates)
1. **Code Quality Baseline**:
   - Add `.editorconfig`
   - Strict TypeScript config
   - ESLint flat config (no deprecated options)
   - Prettier configuration
   - Vitest + Testing Library setup

2. **CI/CD Pipeline**:
   - GitHub Actions: typecheck, lint, test, build
   - Lighthouse CI config
   - Automated quality gates

3. **Documentation**:
   - `/docs/DECISIONS.md`
   - `/docs/ARCHITECTURE.md`
   - Auto-link from README

**Deliverable**: PR with minimal code changes + clear summary

## Replace Fragile Bits with Working OSS

### 1. UI Baseline (Copy from Taxonomy)
```typescript
// Target structure
src/
├── components/
│   ├── ui/           # shadcn/ui primitives
│   ├── layout/       # App shell components
│   └── sections/     # Page sections
├── lib/
│   ├── design-tokens.ts
│   └── utils.ts
└── styles/
    └── globals.css
```

**Copy Targets**:
- Layout structure and typography
- Card primitives and components
- Navigation and footer patterns
- Responsive grid systems

### 2. Booking Now (No More Blockers)
```typescript
// components/BookingEmbed.tsx
export function BookingEmbed() {
  const calComUrl = process.env.NEXT_PUBLIC_CALCOM_URL || 'https://cal.com/lefade'
  
  return (
    <div className="w-full h-[600px]">
      <iframe
        src={calComUrl}
        className="w-full h-full border-0 rounded-lg"
        title="Book Appointment"
      />
    </div>
  )
}
```

**Implementation**:
- Embed Cal.com on `/booking` page
- Environment variable fallback
- Plan native flow replacement later

### 3. Design & Patterns (Next.js Commerce)
**Copy Targets**:
- Motion and animation patterns
- Product grid layouts
- Route patterns and navigation
- Performance optimizations

### 4. DevOps Polish (Dub.co)
**Copy Targets**:
- Environment variable documentation
- README structure and clarity
- CI/CD pipeline configuration
- Deployment documentation

## Concrete Cursor Prompts

### 1. Import OSS Pieces (Claude)
```
Goal: production-grade UI + instant booking.

Tasks:
- Add shadcn/ui tokens and primitives mirroring shadcn/taxonomy
- Replace our Navbar, Footer, Card, Button with unified components
- Create PricingCard that maps to our PLANS config (no hardcoded $)
- Create /booking using Cal.com embed with env CALCOM_URL fallback
- Ensure mobile-first spacing and accessible nav (aria-expanded, focus trap)

Explain each change in a PR description.
```

### 2. Kill ESLint/TS Build Breaks (GPT-5)
```
Fix build failures:
- Migrate to ESLint flat config; remove deprecated 'extensions' and 'useEslintrc'
- Align tsconfig with Next.js 14 (no "moduleResolution": "node16" unless required)
- Add strict types for Button 'asChild' prop (match shadcn ButtonProps)
- For readonly arrays in props, type bullets as ReadonlyArray<string> and accept both

Keep behavior identical; add minimal tests to lock types.
```

### 3. Design Hardening Sweep (Claude)
```
Run a design system sweep:
- Enforce tokens (colors, spacing, radius) across Landing, Plans, Booking, Dashboards
- Replace any "Loading..." with skeletons; replace console.error with error boundary + toasts
- Add accessible focus states; verify contrast ratios

Return a diff summary and checklist of remaining inconsistencies.
```

### 4. Golden Path E2E (GPT-5)
```
Add Playwright tests for:
- Landing loads, CTA to Plans works
- Plans renders from PLANS config; clicking "Get Started" routes correctly
- Booking page renders Cal.com embed and fallback message when env missing
- Admin/Barber dashboards render metric cards and handle empty states

Make CI run these headless.
```

## Copy Good Code Targets

### From shadcn/taxonomy
- **Typography system**: Font families, sizes, weights
- **Card primitives**: Consistent spacing, shadows, borders
- **List patterns**: Ordered and unordered list styles
- **Layout components**: Grid systems, containers, sections

### From Next.js Commerce
- **App shell**: Navigation, header, footer patterns
- **Product sections**: Hero, features, testimonials
- **Grid modules**: Responsive product grids
- **Performance**: Image optimization, lazy loading

### From Cal.com
- **Booking widget**: Embed iframe with fallbacks
- **Environment config**: Local vs production URLs
- **Error handling**: Graceful degradation

### From Dub.co
- **Documentation structure**: Clear setup guides
- **CI readability**: Well-documented GitHub Actions
- **Environment management**: Comprehensive .env examples
- **Deployment guides**: Step-by-step instructions

## Daily Two-Model Loop

### Morning (Claude)
1. **Repo Audit**: Scan for architectural drift
2. **Focused Plan**: Identify 3 highest-impact fixes
3. **Documentation**: Update decisions and architecture docs

### Midday (GPT-5)
1. **Implementation**: Code changes with tests
2. **Quality Gates**: Ensure all CI checks pass
3. **PR Creation**: Clear description and summary

### Evening (Claude)
1. **PR Review**: Check for architectural consistency
2. **Drift Detection**: Ensure changes align with OSS patterns
3. **Documentation Update**: Record decisions and learnings

## CI Quality Gates

### Required Checks
- ✅ **TypeScript**: `tsc --noEmit`
- ✅ **ESLint**: `eslint . --ext .ts,.tsx`
- ✅ **Prettier**: `prettier --check .`
- ✅ **Unit Tests**: `vitest run`
- ✅ **E2E Tests**: `playwright test`
- ✅ **Lighthouse**: Performance, accessibility, SEO
- ✅ **Build**: `next build`

### Blocking Rules
- All checks must pass before merge
- No direct pushes to main
- PR reviews required
- Documentation updates required for architectural changes

## Fastest Path to "Pretty + Working"

### Phase 1: Foundation (Week 1)
1. **Fork Taxonomy** for visual baseline
2. **Embed Cal.com** for instant booking
3. **Copy Commerce patterns** for polished pages
4. **Mirror Dub.co docs** for professional setup

### Phase 2: Polish (Week 2)
1. **Design system enforcement** across all pages
2. **Accessibility improvements** and testing
3. **Performance optimization** and monitoring
4. **E2E test coverage** for critical paths

### Phase 3: Scale (Week 3+)
1. **Native booking flow** to replace Cal.com embed
2. **Advanced features** and integrations
3. **Monitoring and analytics** setup
4. **Documentation and onboarding** improvements

## Success Metrics

### Code Quality
- Zero TypeScript errors
- Zero ESLint warnings
- 90%+ test coverage
- Lighthouse score > 90

### User Experience
- Mobile-first responsive design
- Accessible navigation and forms
- Fast loading times (< 3s)
- Smooth animations and transitions

### Developer Experience
- Clear documentation
- Easy local setup
- Automated quality gates
- Consistent code patterns

This workflow ensures Le Fade is built on proven foundations while maintaining high quality and rapid development velocity.


