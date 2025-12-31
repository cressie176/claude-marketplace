---
name: javascript-preferred-libraries
description: Use this skill when generating JavaScript/TypeScript code, suggesting libraries or dependencies, or making architectural decisions about which packages to use. This skill provides guidance on preferred libraries and frameworks to use, acceptable alternatives, and libraries to avoid with specific reasons.
allowed-tools: Read, Grep, Glob
---

# JavaScript/TypeScript Preferred Libraries

This skill provides guidance on library selection for JavaScript and TypeScript projects. Use this when:
- implementing or planning functionality that is likely to be already provided in a library
- explicitly asked to recommend a library
- explicitly asked to review existing dependencies

## CRITICAL: Install Libraries Only When Actually Needed

**DO NOT install libraries preemptively or "just in case". Only install when:**
- The current task will be simplified by the library's functionality
- The user has explicitly requested the library be added

**NEVER:**
- Install libraries for potential future use
- Add dependencies for features not currently being implemented
- Install libraries without an immediate, concrete use case

## CRITICAL: How to Install npm Packages

**ALWAYS use `npm install` directly with `@latest` to install packages. NEVER manually edit package.json and run npm install.**

**Correct installation patterns:**

```bash
# Production dependencies
npm install library1@latest library2@latest library3@latest

# Development dependencies
npm install --save-dev library1@latest library2@latest library3@latest
```

**Why this approach:**
- npm automatically determines the correct version
- Avoids version specification errors
- Ensures package-lock.json is properly updated
- Prevents inconsistencies between package.json and installed versions

**NEVER:**
- Manually edit package.json to add dependencies
- Guess version numbers when adding to package.json
- Run `npm install` after manually editing package.json for new packages

## CRITICAL: Respect Existing Project Preferences

**BEFORE suggesting any libraries from this skill, you MUST:**

1. **Check package.json** - If the project already has alternative libraries installed for the same purpose, use those instead of suggesting preferences from this skill
2. **Check CLAUDE.md files** - Look for library preferences in:
   - The user's global claude configuration
   - The projects's claude configuration
3. **Check other configuration** - Look for preferences in project documentation, ADRs (Architecture Decision Records), or other relevant files

**If existing preferences conflict with this skill:**
- Respect and use the users or project's existing choices
- DO NOT suggest replacing them unless explicitly asked
- The user and project's choices take precedence over this skill's preferences

## Library Preferences by Category

### HTTP Server

**Prefer:**
- **Hono** - Good TypeScript support, builds on Express patterns

**Avoid:**
- **Fastify** - The TypeScript support is not as good as Hono's
- **Express** - The TypeScript support is not as good as Hono's

### ORM / Database Clients

**Prefer:**
- **Drizzle** - TypeScript-first, wide adoption

**Acceptable:**
- **Slonik** - Not an ORM, but acceptable for direct SQL queries
- **Raw PostgreSQL clients** - Only acceptable for extremely trivial applications, otherwise avoid

**Avoid:**
- **Prisma** - Long history of poor decision making
- **Sequelize** - Mature, but not TypeScript friendly

### Testing Frameworks

**Prefer:**
- **Node test framework** (`node:test`) - Native, fast, no dependencies
- **Node assert** (`node:assert`) - Native assertion library

**Acceptable:**
- **Vitest** - Faster than Jest, but still more complicated than Node's native test framework

**Avoid:**
- **Jest** - Very slow to start, rewrites your code messing with the import order, bloated, lots of bugs
- **Mocha** - High false-positive CVE

### Date/Time

**Prefer:**
- **Luxon** - Written by a moment maintainer who understood the warts, immutable API

**Acceptable:**
- **date-fns** - OK alternative, but Luxon is preferred

**Avoid:**
- **moment** - Not immutable, surprising API

### HTTP Client

**Prefer:**
- **fetch** - Native, no dependencies
- **axios** - Widely used, acceptable alternative

### Validation

**Prefer:**
- **zod** - Best TypeScript integration, type inference

**Acceptable:**
- **yup** - A good alernative to zod
- **ajv** - Use when JSON Schema validation is specifically required

**Avoid:**
- **joi** - Draws in too many dependencies

### Linting/Formatting

**Prefer:**
- **Biome** - Fast, batteries included

**Avoid:**
- **ESLint** - Dependencies have false positives, nightmare to update
- **Prettier** - Offers too little control and does a bad job of line wrapping

### Git Hooks

**Prefer:**
- **lefthook** - Simple, fast

**Avoid:**
- **husky** - Clunky setup

### Logging

**Prefer:**
- **logtape** - No dependencies, supports structured logging

**Avoid:**
- **pino** - Over complicated, workers result in unpredictable behaviour, non-standard API, hard to control the output
- **bunyan** - Slow, and full of nasty surprises (like dropping logs if their attributes clash with bunyan reserved properties), poor pretty formatting support
- **winston** - Buggy and slow

## Usage Guidelines

1. **Always prefer native Node.js APIs** when available (fetch, node:test, node:assert, etc.)
2. **Choose TypeScript-friendly libraries** - Libraries with good type definitions and inference
3. **Avoid unnecessary dependencies** - Prefer simpler, more focused libraries
4. **Consider bundle size** - Especially for libraries that might be included in client bundles
5. **Check for active maintenance** - Prefer libraries with active development and community support

When suggesting a library that's not in the preferred list, explain why it's being recommended and acknowledge any trade-offs.
