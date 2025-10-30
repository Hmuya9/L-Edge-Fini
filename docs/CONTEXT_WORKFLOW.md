# Context Workflow - Le Fade Development

## Overview

This document outlines the daily workflow for maintaining context, solving problems, and ensuring code quality in the Le Fade repository.

## 🔄 Daily / Session Start

### 1. Context Refresh
**Action**: Run the Context Refresh Prompt in Cursor
**Output**: Regenerate `/docs/AI_CONTEXT_REFRESH.md`
**Purpose**: Get current repo status snapshot

### 2. Alignment Check
**Action**: Skim the TL;DR (5 bullets) → copy here so we stay aligned
**Purpose**: Ensure everyone understands current state and priorities

## 🛠️ Problem Solving Workflow

### Errors / Logs → GPT-5
**When to use**: Build errors, Prisma errors, Stripe webhook issues, TypeScript errors
**Input**: Paste build errors, stack traces, error logs
**Output**: Root cause analysis + minimal safe fix
**Example**:
```
"Error in Le Fade repo:
File: src/app/api/bookings/route.ts:45
Error: TS2305: Module '"@/lib/db"' has no exported member 'db'
Stack trace: [paste full trace]

Find the root cause and suggest the smallest fix that maintains existing functionality."
```

### Repo Consistency → Claude Sonnet
**When to use**: Design system inconsistencies, spacing issues, UX patterns
**Input**: Feed entire files or directories
**Output**: Clean refactors without breaking functionality
**Example**:
```
"Review these files for design system consistency:
- src/components/Button.tsx
- src/components/Card.tsx
- src/app/page.tsx
- src/app/plans/page.tsx

Identify inconsistencies in:
- Color usage
- Spacing patterns
- Component structure
- Loading states

Suggest a unified approach."
```

### Execution → Cursor
**When to use**: Apply AI-suggested changes
**Actions**:
1. Apply edits precisely
2. Review diffs
3. Run `npm run build` and confirm
4. Test functionality
5. Commit with clean message

## 🎯 External Inspiration

### Open-Source Reference Repos
**Sources**:
- [Next.js SaaS Starter](https://github.com/vercel/nextjs-subscription-payments)
- [Next.js Boilerplate](https://github.com/vercel/next.js/tree/canary/examples)
- [Next.js Prisma Stripe Starter](https://github.com/prisma/prisma-examples)

**Workflow**:
1. **Copy patterns** (pricing components, dashboards, auth flows) into your repo
2. **Claude adapts styling** to match Le Fade design system
3. **GPT-5 checks integration risks** and compatibility

### Pattern Integration
**Examples**:
- Pricing components from SaaS starter
- Dashboard layouts from boilerplate
- Auth flows from Prisma examples
- Error handling patterns
- Testing setups

## ✅ Quality Gates

### Before Merging PRs
- ✅ `npm run build` passes
- ✅ No ESLint/TS errors
- ✅ All core flows work (Landing → Plan → Booking → Stripe)
- ✅ Mobile responsiveness maintained
- ✅ Authentication flows intact
- ✅ Database operations working

### Pre-Deploy Checklist
- ✅ Vercel build succeeds
- ✅ Environment variables configured
- ✅ Database migrations applied
- ✅ Stripe webhooks configured
- ✅ Error boundaries in place
- ✅ Performance metrics acceptable

## 🚀 Current Priority Queue

### PR #1: Fix ESLint (In Progress)
**Scope**: Pin ESLint to v8.57.0, clean config, verify Vercel build
**Status**: Ready to implement
**Owner**: GPT-5 (error resolution)

### PR #2: Add Testing Framework
**Scope**: Set up Jest + React Testing Library, write first test for plans.ts
**Status**: Pending
**Owner**: GPT-5 (testing specialist)

### PR #3: Design System Unification
**Scope**: Standardize loading states, error handling, remove hardcoded colors
**Status**: Pending
**Owner**: Claude Sonnet (consistency specialist)

## 📋 Daily Workflow Checklist

### Morning Routine
- [ ] Run Context Refresh Prompt
- [ ] Review TL;DR and current priorities
- [ ] Check for any overnight build issues
- [ ] Review pending PRs and tasks

### Development Session
- [ ] Follow problem-solving workflow (GPT-5 → Claude → Cursor)
- [ ] Apply quality gates before commits
- [ ] Test core flows after changes
- [ ] Update documentation as needed

### End of Day
- [ ] Commit all changes with clear messages
- [ ] Update TODO list with progress
- [ ] Note any blockers or issues
- [ ] Plan next day's priorities

## 🎯 Success Metrics

### Code Quality
- Zero TypeScript errors
- Zero ESLint warnings
- 100% build success rate
- All tests passing

### User Experience
- Core flows working (Landing → Plan → Booking → Stripe)
- Mobile responsiveness maintained
- Fast loading times
- Smooth interactions

### Developer Experience
- Clear documentation
- Easy local setup
- Automated quality gates
- Consistent code patterns

## 🔧 Tools & Resources

### AI Specialists
- **GPT-5**: Error resolution, build fixes, testing
- **Claude Sonnet**: Consistency, design system, architecture
- **Cursor**: Code execution, local testing, diffs

### External Resources
- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Stripe Documentation](https://stripe.com/docs)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [shadcn/ui](https://ui.shadcn.com/)

### Quality Tools
- TypeScript compiler
- ESLint
- Prettier
- Jest/React Testing Library
- Playwright (for E2E)
- Lighthouse (for performance)

## 📝 Documentation Standards

### Context Refresh
- Update after major changes
- Include current build status
- Note any blockers or issues
- Keep TL;DR current and accurate

### PR Descriptions
- Clear title with scope
- Detailed description of changes
- Acceptance criteria
- Test notes
- Risk assessment

### Commit Messages
- Use conventional commits format
- Be descriptive but concise
- Include file paths for major changes
- Reference issues when applicable

This workflow ensures consistent development practices while maintaining code quality and team alignment.


