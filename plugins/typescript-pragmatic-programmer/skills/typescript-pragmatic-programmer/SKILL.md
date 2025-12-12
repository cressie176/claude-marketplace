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

## Use High-Quality Libraries: Don't Reinvent the Wheel

### Overview

Before implementing functionality yourself, check if the user has expressed a preference for a suitable library. If not, search for well-maintained, high-quality libraries on npm. Reusing proven solutions saves time, reduces bugs, and gives you battle-tested code that handles edge cases you might miss.

### The Principle

Writing custom implementations is expensive: you pay for development time, testing time, bug fixes, maintenance, and opportunity cost. Good libraries have already paid these costs and spread them across thousands of users. Your time is better spent on domain-specific problems that differentiate your application.

### Process

When you need common functionality:

1. **Use existing dependencies**: Is there already a library in package.json that can handle this?
2. **Check user preferences**: Has the user specified a library preference in their configuration, documentation, skills or previous prompts?
3. **Search npm**: Look for high-quality libraries if no preference exists
4. **Evaluate quality**: Check downloads, maintenance, TypeScript support, bundle size
5. **Install and use**: Add the dependency rather than implementing from scratch

### Examples

#### Generating Unique IDs

**Instead of:**
```typescript
// Custom implementation with potential collisions
function generateId(): string {
  return Math.random().toString(36).substring(2, 15);
}
```

**First check**: Does the user have a preferred ID generation library?

**If not, recommend:**
```typescript
import { nanoid } from 'nanoid';

const id = nanoid();  // URL-safe, collision-resistant
```

**Alternative libraries**: `uuid`, `ulid`, `cuid2`

#### URL Canonicalization

**Instead of:**
```typescript
// Custom URL normalization with edge cases
function canonicalizeUrl(url: string): string {
  return url.toLowerCase().replace(/\/$/, '');
}
```

**First check**: Does the user have a preferred URL manipulation library?

**If not, recommend:**
```typescript
import { normalizeUrl } from '@sindresorhus/normalize-url';

const canonical = normalizeUrl('HTTP://Example.com//path/');
// 'http://example.com/path'
```

**Alternative libraries**: `normalize-url`, `url-parse`

#### Date Manipulation

**Instead of:**
```typescript
// Custom date math with bugs
function addDays(date: Date, days: number): Date {
  const result = new Date(date);
  result.setDate(result.getDate() + days);
  return result;
}
```

**First check**: Does the user have a preferred date library?

**If not, recommend:**
```typescript
import { addDays, format } from 'date-fns';

const future = addDays(new Date(), 7);
const formatted = format(future, 'yyyy-MM-dd');
```

**Alternative libraries**: `dayjs`, `luxon`

#### Input Validation

**Instead of:**
```typescript
// Custom validation prone to edge cases
function isValidEmail(email: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}
```

**First check**: Does the user have a preferred validation library?

**If not, recommend:**
```typescript
import { z } from 'zod';

const EmailSchema = z.string().email();

function validateEmail(email: string): boolean {
  return EmailSchema.safeParse(email).success;
}
```

**Alternative libraries**: `yup`, `joi`, `superstruct`

### When to Write Your Own

Write custom implementations only when:

- **Domain-specific logic**: The functionality is unique to your business domain
- **No suitable library exists**: You've searched and found nothing appropriate
- **Library is abandoned**: Last update was years ago with open critical issues
- **Extreme simplicity**: The implementation is truly 2-3 lines (e.g., `Math.max(a, b)`)
- **Performance critical**: Profiling shows the library is a bottleneck (rare)

### How to Evaluate Libraries

When recommending libraries, consider:

- **Maintenance**: Number of long-standing / serious issues. Recent commits and releases
- **TypeScript support**: Native types or @types package
- **Downloads**: npm weekly downloads (higher suggests stability)
- **Size**: Bundle size impact (use bundlephobia.com)
- **Dependencies**: Fewer transitive dependencies reduces risk
- **Documentation**: Clear examples and API docs
- **Tests**: Well-tested with high coverage

### Guidelines

- **Check user preferences FIRST**: Look in package.json, project docs, or ask
- **Search before coding**: Spend 5 minutes searching npm before implementing
- **Prefer well-maintained**: Choose libraries with recent activity
- **TypeScript-first**: Prefer libraries with native TypeScript support
- **Bundle size matters**: Check impact on frontend applications
- **Read the docs**: Understand the API before dismissing as "too complex"
- **Domain code only**: Save your effort for problems unique to your application

## Fix Root Problems: Never Compromise on Quality

### Overview

When you encounter a problem while implementing the user's design, fix the root issue rather than abandoning the approach or disabling safeguards. If the user has specified a design pattern or architecture, honor it even when it creates challenges. Taking shortcuts—like disabling TypeScript, turning off tests, or ignoring lint rules—creates technical debt and undermines the quality foundation.

### The Principle

Quality tools like TypeScript, tests, and linters exist to catch problems. When they complain, they're doing their job. Disabling them is shooting the messenger. Similarly, if the user has chosen an architectural approach (events, dependency injection, etc.), they did so for good reasons. Abandoning it at the first sign of difficulty shows lack of discipline and understanding.

### When TypeScript Definitions Break

**Wrong Response:**
```typescript
// DON'T: Disable type checking
// @ts-ignore
emitter.emit('user:created', userData);

// DON'T: Use 'any' to silence errors
const emitter: any = new EventEmitter();
```

**Right Response:**
```typescript
// DO: Fix the type definitions
import { EventEmitter } from 'node:events';

interface ApplicationEvents {
  'user:created': [user: User];
  'user:deleted': [userId: number];
}

declare interface TypedEventEmitter {
  on<K extends keyof ApplicationEvents>(
    event: K,
    listener: (...args: ApplicationEvents[K]) => void
  ): this;
  emit<K extends keyof ApplicationEvents>(
    event: K,
    ...args: ApplicationEvents[K]
  ): boolean;
}

class TypedEventEmitter extends EventEmitter {}

const emitter = new TypedEventEmitter();
emitter.emit('user:created', user);  // Fully typed
```

### When Tests Fail

**Wrong Response:**
```typescript
// DON'T: Skip failing tests
test.skip('should validate email', () => {
  // This test is failing, will fix later
});

// DON'T: Disable test temporarily
// test('should create user', async () => {
//   // Disabled because of database issues
// });
```

**Right Response:**
```typescript
// DO: Fix the test or the implementation
test('should validate email', () => {
  // Fixed the validation logic to handle edge cases
  const result = validateEmail('user@example.com');
  expect(result.success).toBe(true);
});

// DO: Fix the underlying issue
test('should create user', async () => {
  // Fixed database setup in test environment
  const user = await userService.createUser(validData);
  expect(user.id).toBeDefined();
});
```

### When Linters Complain

**Wrong Response:**
```typescript
// DON'T: Disable lint rules
/* eslint-disable @typescript-eslint/no-explicit-any */
function processData(data: any): any {
  return data.map((item: any) => item.value);
}

// DON'T: Ignore all errors in file
/* eslint-disable */
```

**Right Response:**
```typescript
// DO: Write code that satisfies the linter
function processData<T extends { value: unknown }>(data: T[]): unknown[] {
  return data.map((item) => item.value);
}

// DO: If rule is genuinely wrong for this case, disable with justification
function legacyApiWrapper(data: LegacyType): ModernType {
  // eslint-disable-next-line @typescript-eslint/no-explicit-any -- Legacy API requires any
  return (legacyLib as any).process(data);
}
```

### When User's Design Seems Difficult

**Wrong Response:**
```typescript
// User specified event-driven architecture, but you find it complex

// DON'T: Abandon the pattern
class UserService {
  async createUser(data: CreateUserInput): Promise<User> {
    const user = await this.db.users.create(data);
    // Just call email service directly instead of events
    await this.emailService.sendWelcome(user);
    return user;
  }
}
```

**Right Response:**
```typescript
// DO: Implement the pattern correctly, even if challenging
class UserService {
  constructor(
    private db: Database,
    private emitter: TypedEventEmitter
  ) {}

  async createUser(data: CreateUserInput): Promise<User> {
    const user = await this.db.users.create(data);
    // Honor user's event-driven design
    this.emitter.emit('user:created', user);
    return user;
  }
}

// Fix TypeScript definitions to make it work
```

### When Something "Just Won't Work"

**Wrong Response:**
- "TypeScript won't let me do this, so I'll use `any`"
- "The tests keep failing, so I'll skip them for now"
- "The linter is annoying, so I'll turn it off"
- "This pattern is too hard, so I'll use a simpler approach"

**Right Response:**
- "TypeScript won't let me do this, so I'll fix my type definitions"
- "The tests keep failing, so I'll fix the bug they're catching"
- "The linter is complaining, so I'll write better code"
- "This pattern is challenging, so I'll learn how to implement it correctly"

### Examples of Proper Fixes

#### Event Typing Challenge

```typescript
// Problem: User wants events, but EventEmitter isn't type-safe

// DON'T give up on events and use callbacks instead
// DO: Create properly typed event emitter (shown above)
```

#### Strict Null Check Errors

```typescript
// Problem: TypeScript strict null checks catch potential undefined access

// DON'T: Disable strictNullChecks or use non-null assertions everywhere
const user = await findUser(id)!;  // Wrong

// DO: Handle null/undefined properly
const user = await findUser(id);
if (!user) {
  throw new NotFoundError('User not found');
}
```

#### Integration Test Complexity

```typescript
// Problem: Integration tests are complex to set up

// DON'T: Switch to unit tests with mocks everywhere
// DO: Invest in proper test infrastructure
// - Docker compose for test databases
// - Test fixtures and factories
// - Database reset utilities
```

### Guidelines

- **Honor the user's design choices**: They specified patterns for good reasons
- **Fix TypeScript errors properly**: Add types, don't use `any` or `@ts-ignore`
- **Fix failing tests**: Don't skip or disable them
- **Respect linter rules**: Write compliant code, don't disable checks
- **Solve root causes**: Don't work around problems with hacks
- **Ask for clarification**: If the design seems wrong, ask the user to explain
- **Learn the pattern**: If struggling with user's approach, understand it better
- **Quality is non-negotiable**: Tests, types, and linting are safeguards, not obstacles
