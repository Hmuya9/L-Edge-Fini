# AI Workflow Guide - Le Fade

## Overview

This guide outlines the AI workflow for efficient development, leveraging different AI models for their strengths while maintaining code quality and consistency.

## AI Role Division

### GPT-5 (Error Resolution Specialist)
**Owns**: Root cause analysis, build/deployment fixes, error resolution

**Strengths**:
- Quick error diagnosis
- Build system expertise
- TypeScript/ESLint fixes
- API debugging

**Workflow**:
1. Paste error logs/stack traces
2. Ask: "Find the root cause of this error in context of the repo. Suggest the smallest, safest fix."
3. Get specific, actionable solutions
4. Apply fixes incrementally

**Example Prompts**:
```
"Fix this TypeScript error in src/app/api/bookings/route.ts: 
TS2305: Module '"@/lib/db"' has no exported member 'db'
Provide the minimal fix that maintains existing functionality."
```

### Claude 3.5 Sonnet (Consistency Specialist)
**Owns**: Repo-wide consistency, design system, architecture refactors

**Strengths**:
- Long-context understanding
- Design system consistency
- Architecture patterns
- Code quality improvements

**Workflow**:
1. Feed entire files or directories
2. Ask: "Scan repo for inconsistencies in design system, pricing, env handling. Suggest clean, repo-wide fixes."
3. Get comprehensive improvement plans
4. Apply changes systematically

**Example Prompts**:
```
"Review src/components/ and src/app/ for design system inconsistencies.
Identify components that don't use our Button, Card, Input primitives.
Suggest a unified approach that maintains existing functionality."
```

### Cursor IDE (Execution Layer)
**Owns**: Code application, local testing, clean diffs

**Strengths**:
- Precise code editing
- Local environment management
- Git workflow
- Real-time feedback

**Workflow**:
1. Apply AI-suggested changes
2. Test locally with `npm run dev`
3. Run build checks with `npm run build`
4. Commit with clean, descriptive messages

### GitHub Copilot (Optional)
**Owns**: Inline completion for new functions

**Strengths**:
- Quick function generation
- Code completion
- Boilerplate reduction

**Use When**: Writing new functions, API routes, or component logic

## Workflow Loop

### 1. Development Phase
```bash
# Start development
npm run dev

# When errors occur, copy logs to GPT-5
# Get root cause analysis and fix suggestions
```

### 2. Consistency Check
```bash
# Feed relevant files to Claude
# Get consistency improvements
# Apply design system fixes
```

### 3. Execution & Testing
```bash
# Apply changes with Cursor
# Test locally
npm run dev
npm run build
npm run test

# Commit with clean message
git add .
git commit -m "fix: resolve TypeScript errors in booking API"
```

### 4. Verification
```bash
# Verify all systems working
npm run lint
npm run typecheck
npm run test
npm run build

# If broken, back to GPT-5 with logs
```

## Common Workflow Patterns

### Pattern 1: Error Resolution
1. **Encounter error** → Copy stack trace to GPT-5
2. **Get fix** → Apply with Cursor
3. **Test** → Verify fix works
4. **Commit** → Clean commit message

### Pattern 2: Feature Addition
1. **Plan feature** → Use Claude for architecture
2. **Implement** → Use Cursor + Copilot for code
3. **Debug issues** → Use GPT-5 for error resolution
4. **Test & commit** → Verify functionality

### Pattern 3: Refactoring
1. **Identify inconsistencies** → Use Claude for analysis
2. **Plan refactor** → Get systematic approach
3. **Apply changes** → Use Cursor incrementally
4. **Verify** → Test each change

### Pattern 4: Build Issues
1. **Build fails** → Copy error to GPT-5
2. **Get solution** → Apply fix
3. **Rebuild** → Verify success
4. **Document** → Update if needed

## Best Practices

### For GPT-5 (Error Resolution)
- **Be specific**: Include file paths, line numbers, error messages
- **Provide context**: Show relevant code snippets
- **Ask for minimal fixes**: Avoid over-engineering
- **Test incrementally**: Apply one fix at a time

### For Claude (Consistency)
- **Feed complete context**: Include related files
- **Ask for systematic approach**: Get comprehensive plans
- **Focus on patterns**: Look for recurring issues
- **Maintain functionality**: Ensure changes don't break existing features

### For Cursor (Execution)
- **Apply changes precisely**: Follow AI suggestions exactly
- **Test immediately**: Verify each change works
- **Commit frequently**: Small, focused commits
- **Use descriptive messages**: Clear commit history

### For Copilot (Completion)
- **Use for boilerplate**: New functions, API routes
- **Review suggestions**: Don't accept blindly
- **Maintain style**: Keep consistent with codebase
- **Test generated code**: Verify functionality

## Error Resolution Workflow

### Step 1: Capture Error
```bash
# Copy complete error message
# Include stack trace
# Note file and line number
```

### Step 2: GPT-5 Analysis
```
"Error in Le Fade repo:
File: src/app/api/bookings/route.ts:45
Error: TS2305: Module '"@/lib/db"' has no exported member 'db'
Stack trace: [paste full trace]

Find the root cause and suggest the smallest fix that maintains existing functionality."
```

### Step 3: Apply Fix
```bash
# Apply suggested fix with Cursor
# Test the change
npm run typecheck
```

### Step 4: Verify
```bash
# Run full test suite
npm run lint
npm run typecheck
npm run test
npm run build
```

## Consistency Improvement Workflow

### Step 1: Identify Scope
```bash
# Choose files/directories to review
# Focus on one area at a time
```

### Step 2: Claude Analysis
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

### Step 3: Apply Changes
```bash
# Apply changes systematically
# Test each component
# Verify visual consistency
```

### Step 4: Document
```bash
# Update design system docs
# Commit with clear message
git commit -m "feat: unify Button component across all pages"
```

## Quality Gates

### Before Committing
- ✅ Code compiles without errors
- ✅ All tests pass
- ✅ Linting passes
- ✅ Build succeeds
- ✅ Functionality preserved

### Before Merging
- ✅ PR reviewed
- ✅ CI pipeline green
- ✅ No breaking changes
- ✅ Documentation updated
- ✅ Performance maintained

## Troubleshooting

### Common Issues
1. **Build failures**: Use GPT-5 for root cause analysis
2. **TypeScript errors**: Get specific fixes from GPT-5
3. **Design inconsistencies**: Use Claude for systematic fixes
4. **Performance issues**: Analyze with Claude, fix with GPT-5

### Escalation Path
1. **Try GPT-5** for specific errors
2. **Use Claude** for broader issues
3. **Apply with Cursor** for precise changes
4. **Test thoroughly** before committing
5. **Document changes** for future reference

This workflow ensures efficient development while maintaining code quality and consistency across the Le Fade codebase.


