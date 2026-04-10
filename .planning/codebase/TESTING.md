# Testing Patterns

**Analysis Date:** 2026-04-10

## Test Framework

**Runner:**
- Not detected.
- Config: Not detected (`jest.config.*` and `vitest.config.*` absent at repository root).

**Assertion Library:**
- Not detected.

**Run Commands:**
```bash
Not detected              # Run all tests
Not detected              # Watch mode
Not detected              # Coverage
```

## Test File Organization

**Location:**
- No co-located or dedicated tests detected (`**/*.{test,spec}.{js,jsx,ts,tsx,mjs,cjs}` returned no files).

**Naming:**
- Not applicable (no test files detected).

**Structure:**
```
Not applicable: no test directory or *.test/*.spec files found in `/Users/matteo/Projects/GitHub/tsparticles/wordpress`.
```

## Test Structure

**Suite Organization:**
```typescript
// Not detected in repository: no describe()/it()/test() usage in `src/**/*.js`.
```

**Patterns:**
- Setup pattern: Not detected.
- Teardown pattern: Not detected.
- Assertion pattern: Not detected.

## Mocking

**Framework:** Not detected

**Patterns:**
```typescript
// No vi.mock/jest.mock or equivalent mocking patterns detected.
```

**What to Mock:**
- No existing project convention detected. For future additions, mock external package boundaries (`@wordpress/*`, `@tsparticles/*`) when writing unit tests for helper modules such as `src/utils.js` and `src/load.js`.

**What NOT to Mock:**
- No existing project convention detected. For future additions, avoid mocking pure local data transforms where direct input/output assertions are possible (`transformLoadableObject` in `src/utils.js`).

## Fixtures and Factories

**Test Data:**
```typescript
// Not detected: no fixtures or test factories present.
```

**Location:**
- Not detected.

## Coverage

**Requirements:** None enforced (no coverage tooling/configuration detected in `package.json`, no test runner config files detected).

**View Coverage:**
```bash
Not detected
```

## Test Types

**Unit Tests:**
- Not used (no unit test files detected).

**Integration Tests:**
- Not used (no integration test setup detected).

**E2E Tests:**
- Not used (no E2E framework/config detected).

## Common Patterns

**Async Testing:**
```typescript
// Not detected. Async production patterns to mirror in future tests:
// await loadWordpressParticles(tsParticles, plugins) in `src/view.js`
// await tsParticles.load(...) in `src/edit.js`, `src/save.js`, `src/view.js`
```

**Error Testing:**
```typescript
// Not detected. Current error behavior references:
// throw error in callback-based script flows in `scripts/postversion.js`.
```

---

*Testing analysis: 2026-04-10*
