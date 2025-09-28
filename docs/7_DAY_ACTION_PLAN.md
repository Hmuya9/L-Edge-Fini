# 7-Day Action Plan - Le Fade

## Overview

Based on the AI Review Summary, this plan addresses the biggest gaps while maintaining a clean, incremental development approach.

## AI Workflow Setup

### Role Division
- **GPT-5**: Root cause analysis, build/deployment fixes, error resolution
- **Claude 3.5 Sonnet**: Repo-wide consistency, design system, architecture refactors
- **Cursor IDE**: Code execution, local testing, clean diffs
- **GitHub Copilot** (optional): Inline completion for new functions

### Workflow Loop
1. **Run locally** → copy errors/logs to GPT-5
2. **GPT-5**: "Find root cause, suggest smallest safest fix"
3. **Claude**: "Scan repo for inconsistencies, suggest clean fixes"
4. **Cursor**: Apply patches, test locally, commit
5. **Verify build** → `npm run dev` and `npm run build`
6. **If broken**: Back to GPT-5 with logs
7. **Refactor incrementally**: Small PRs (feature/bugfix per PR)

## 7-Day PR Plan

### PR #1: Fix Build Health
**Title**: `fix: clean ESLint config and resolve TS strict mode errors`

**Scope**:
- Remove deprecated ESLint options (`useEslintrc`, `extensions`)
- Migrate to ESLint flat config
- Resolve TypeScript strict mode errors
- Fix implicit `any` types in API routes

**Acceptance Criteria**:
- ✅ `npm run lint` passes with zero warnings
- ✅ `npm run typecheck` passes with zero errors
- ✅ `npm run build` completes successfully
- ✅ All existing functionality preserved

**Test Notes**:
- Run full build pipeline locally
- Verify all pages load correctly
- Check API routes respond properly

**Owner**: GPT-5 (error resolution specialist)

---

### PR #2: Design System Unification
**Title**: `feat: unify design system across all pages`

**Scope**:
- Consolidate Tailwind tokens in `tailwind.config.ts`
- Apply consistent Button, Card, Input components
- Standardize spacing, colors, typography
- Fix loading states and error patterns

**Acceptance Criteria**:
- ✅ All pages use consistent design tokens
- ✅ Button/Card/Input components unified
- ✅ Loading skeletons replace "Loading..." text
- ✅ Error boundaries with toast notifications
- ✅ Mobile-first responsive design

**Test Notes**:
- Visual regression testing
- Mobile device testing
- Accessibility audit (contrast ratios)

**Owner**: Claude 3.5 Sonnet (consistency specialist)

---

### PR #3: Testing Framework Setup
**Title**: `feat: add Jest/React Testing Library + first unit test`

**Scope**:
- Install and configure Jest + React Testing Library
- Add Vitest for component testing
- Create first unit test for pricing logic
- Set up test coverage reporting

**Acceptance Criteria**:
- ✅ `npm run test` passes
- ✅ Test coverage > 80% for critical paths
- ✅ CI pipeline runs tests automatically
- ✅ First unit test for `src/config/plans.ts`

**Test Notes**:
- Test pricing calculations
- Test component rendering
- Test API route responses

**Owner**: GPT-5 (testing specialist)

---

### PR #4: Redis Caching for Bookings
**Title**: `feat: add Redis caching layer for booking availability`

**Scope**:
- Set up Redis connection with graceful fallback
- Cache booking availability with TTL
- Invalidate cache on booking create/cancel
- Add caching to admin metrics

**Acceptance Criteria**:
- ✅ Redis caching works with DB fallback
- ✅ Cache invalidation on booking changes
- ✅ Performance improvement measurable
- ✅ Graceful degradation when Redis unavailable

**Test Notes**:
- Load testing with Redis enabled/disabled
- Cache hit/miss ratio monitoring
- Booking flow end-to-end testing

**Owner**: Claude 3.5 Sonnet (architecture specialist)

---

### PR #5: Reviews System MVP
**Title**: `feat: add simple testimonial model + UI`

**Scope**:
- Add Review model to Prisma schema
- Create review submission API
- Add testimonials section to landing page
- Basic review moderation (admin approval)

**Acceptance Criteria**:
- ✅ Review model with rating, comment, moderation
- ✅ API endpoint for review submission
- ✅ Testimonials display on landing page
- ✅ Admin can approve/reject reviews

**Test Notes**:
- Review submission flow
- Admin moderation interface
- Testimonials display logic

**Owner**: GPT-5 (feature implementation)

## OSS Template Integration

### Leverage Existing Templates
- **SaaS Starter Kit**: Subscription flows, billing logic
- **Taxonomy**: Clean design system with shadcn/ui
- **Next.js Boilerplate**: SEO, testing, CI/CD best practices

### Copy Strategy
1. **Identify weak spots** in current repo
2. **Copy relevant code** from OSS templates
3. **Adapt to Le Fade** using Cursor
4. **Test thoroughly** before merging

## Daily Schedule

### Day 1-2: Build Health (PR #1)
- Fix ESLint/TS issues
- Ensure clean builds
- Set up proper error handling

### Day 3-4: Design System (PR #2)
- Unify components across pages
- Standardize loading/error states
- Mobile responsiveness audit

### Day 5: Testing Framework (PR #3)
- Set up Jest/RTL
- Add first unit tests
- Configure CI test runs

### Day 6: Redis Caching (PR #4)
- Implement caching layer
- Add performance monitoring
- Test fallback scenarios

### Day 7: Reviews System (PR #5)
- Add review functionality
- Create testimonials UI
- Set up moderation workflow

## Success Metrics

### Build Health
- Zero ESLint warnings
- Zero TypeScript errors
- 100% build success rate

### Design Consistency
- Unified component library
- Consistent spacing/colors
- Mobile-first responsive design

### Testing Coverage
- >80% test coverage
- All critical paths tested
- CI/CD pipeline green

### Performance
- Redis caching active
- <3s page load times
- Smooth user interactions

### Feature Completeness
- Reviews system functional
- Admin moderation working
- Testimonials displaying

## Risk Mitigation

### Build Issues
- **Risk**: Breaking existing functionality
- **Mitigation**: Small, incremental changes with thorough testing

### Design Inconsistencies
- **Risk**: Visual regression
- **Mitigation**: Component-by-component updates with visual testing

### Performance Degradation
- **Risk**: Caching layer issues
- **Mitigation**: Graceful fallbacks and monitoring

### Feature Complexity
- **Risk**: Over-engineering reviews system
- **Mitigation**: Start with MVP, iterate based on feedback

## Next Steps After 7 Days

1. **Monitor metrics** and user feedback
2. **Iterate on reviews system** based on usage
3. **Add advanced features** (search, filtering, analytics)
4. **Scale infrastructure** (monitoring, logging, alerting)
5. **Prepare for growth** (multi-tenant, internationalization)

This plan provides a clear path from current state to production-ready while maintaining code quality and user experience.
