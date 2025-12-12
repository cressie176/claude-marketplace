---
name: typescript-pragmatic-programmer
description: Pragmatic programming principles and wisdom applied to TypeScript development. Use this skill when writing TypeScript code to follow pragmatic practices for maintainable, robust software.
allowed-tools: Read
---

# TypeScript Pragmatic Programmer

Practical wisdom for TypeScript developers, focusing on pragmatic approaches to building maintainable, robust software.

## Take Small Steps with Immediate Feedback

### Overview

Make small, incremental changes and verify them immediately. After every change, run TypeScript compilation and tests before moving forward. This tight feedback loop catches errors early when they're easiest to fix and prevents you from building on faulty foundations.

### The Principle

Large changes introduce many variables, making it hard to identify what went wrong when something breaks. Small changes with immediate verification create a safety net that catches problems at their source. The cost of fixing a bug grows exponentially with the time between introduction and detection.

### Practice in TypeScript

After every meaningful change, no matter how small:

1. **Compile**: Run `tsc` or your build tool to catch type errors
2. **Test**: Run your test suite to verify behavior
3. **Commit**: If both pass, commit immediately

```bash
# After each change
npm run build    # Verify TypeScript compilation
npm test         # Verify tests pass
git add .
git commit -m "Small, verified change"
```

### What Counts as a Small Change?

- Add a single function
- Rename a variable or function
- Extract a helper function
- Add a type annotation
- Refactor one method
- Add one test case

### Why This Works

- **Immediate feedback**: Type errors appear seconds after introduction
- **Easy debugging**: Only one change to investigate if something breaks
- **Confidence**: Each step is verified before the next
- **Clean history**: Commits represent working states, making bisect effective
- **Less stress**: No "big bang" integration of many unverified changes

### Guidelines

- **Compile after every change**: TypeScript is fast, use it constantly
- **Run affected tests**: Use watch mode or run specific test files
- **Commit working states**: Only commit when compilation and tests pass
- **Use watch mode**: `tsc --watch` and `npm test -- --watch` for instant feedback
- **Fix before moving on**: Don't pile up errors to "fix later"

## Tracer Bullets: Build End-to-End First

### Overview

Build a thin slice through all layers of your system first, then expand it. This ensures all parts integrate correctly before investing in features. A "tracer bullet" hits the target, showing you're aimed correctly.

### Practice in TypeScript

When building a new feature:

1. **Minimal end-to-end**: Request → route → service → database → response
2. **Verify integration**: All layers talk to each other
3. **Add functionality**: Expand each layer incrementally

```typescript
// Step 1: Minimal end-to-end (tracer bullet)
app.post('/api/users', async (c) => {
  const user = await db.users.create({ name: 'test' });
  return c.json(user);
});

// Step 2: Add validation
app.post('/api/users', async (c) => {
  const data = CreateUserSchema.parse(await c.req.json());
  const user = await db.users.create(data);
  return c.json(user);
});

// Step 3: Add service layer
app.post('/api/users', async (c) => {
  const data = CreateUserSchema.parse(await c.req.json());
  const user = await userService.createUser(data);
  return c.json(user);
});
```

### Guidelines

- **Thin slice first**: Get one simple case working end-to-end
- **Expand incrementally**: Add complexity layer by layer
- **Test integration early**: Don't build components in isolation then hope they integrate
