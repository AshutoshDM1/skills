# Strict Environment Variables & Fail-Fast Policy

## Core Principle

Never use silent fallbacks or default values for required environment variables. All environment configuration must fail fast and throw explicit runtime errors when missing or invalid.

---

## Rules & Enforcement

### 1. No Silent Fallbacks

- ❌ **FORBIDDEN**:
  ```typescript
  // DO NOT DO THIS
  const apiKey = process.env.API_KEY || 'default_secret';
  const dbUrl = process.env.DATABASE_URL ?? 'http://localhost:5432';
  ```
- ✅ **REQUIRED**:
  ```typescript
  // Always validate and throw explicitly if missing
  const apiKey = process.env.API_KEY;
  if (!apiKey) {
    throw new Error('Missing required environment variable: API_KEY');
  }
  ```

### 2. Centralized Validation (Recommended Pattern)

When defining environment configuration, centralize checks in an `env.ts` or `config.ts` module with explicit schema validation (e.g., Zod) or assertions:

```typescript
function getEnv(key: string): string {
  const value = process.env[key];
  if (!value || value.trim() === '') {
    throw new Error(`[Env Error] Missing or empty required environment variable: "${key}"`);
  }
  return value;
}

export const serverEnv = {
  DATABASE_URL: getEnv('DATABASE_URL'),
  API_SECRET: getEnv('API_SECRET'),
};
```

### 3. Client vs. Server Boundaries

- Never expose private server secrets to the browser.
- Only variables explicitly prefixed with `NEXT_PUBLIC_` (or framework-equivalent public prefix) may be accessed in client-side code.
- If a required `NEXT_PUBLIC_` variable is missing on the client, throw an error immediately during component/service initialization.

### 4. Continuous Enforcement

- Any code generated, refactored, or reviewed by AI agents must strictly adhere to this fail-fast policy.
