---
name: typescript-tdd-cookbook
description: TypeScript TDD patterns and implementation cookbook. Use this skill when generating TypeScript code to reference proven test-driven development patterns.
allowed-tools: Read
---

# TypeScript TDD Cookbook

A collection of TypeScript test-driven development patterns and implementations.

## Pattern 1: ObjectMother / TestDataFactory Pattern

### Overview

Great tests tell a simple, focused story. You should see instantly what behaviour is under test and which values actually matter. That means pulling the noise out of sight and keeping only the essential details in view. Static, centralised fixtures help a little, but they soon become cumbersome and brittle as every new case needs yet more hard coded data. A better approach is to randomly generate realistic data, then explicitly override the few values you care about. The result is a cleaner test suite with far clearer intent.

### Key Benefits

1. **Focus on Intent**: Tests only specify values they actually assert, making the test's purpose clear
2. **Reduce Duplication**: Shared data generation logic in one place
3. **Avoid Brittleness**: Random data prevents tests from relying on specific values defined elsewhere

### Required Dependencies

```bash
npm install -D @ngneat/falso immer
```

### Implementation

```typescript
// test/TestData.ts
import { randCompanyName, randNumber, randEmail } from '@ngneat/falso';
import { produce } from 'immer';

interface Company {
  name: string;
  number: number;
  email: string;
}

export default class TestData {
  static company(recipe?: (draft: Company) => void): Company {
    return produce({
      name: randCompanyName(),
      number: randNumber({ min: 1000, max: 9999 }),
      email: randEmail()
    }, recipe);
  }
}
```

### Usage Example

```typescript
import { strictEqual as eq } from 'node:assert/strict';
import TestData from './TestData';

it("creates company with specific details", async () => {
  const data = TestData.company((draft) => {
    draft.name = "Stark Industries";
    draft.number = 1234;
    draft.email = "tony@stark.com";
  });

  const created = await companyService.createCompany(data);

  eq(created.name, "Stark Industries");
  eq(created.number, 1234);
  eq(created.email, "tony@stark.com");
});
```

This pattern makes tests self-documenting: the values you set are the values you care about.
