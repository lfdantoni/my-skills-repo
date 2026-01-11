# TypeScript Specialist Rules and Principles

## Fundamental Principles

### 1. Type Safety First
- **Never use `any`** unless absolutely necessary. Prefer `unknown` when the type is truly unknown.
- **Enable strict mode** in `tsconfig.json`: `strict: true`, `noImplicitAny: true`, `strictNullChecks: true`.
- **Use explicit types** for function parameters and return values.
- **Avoid type assertions** (`as`) when possible. Prefer type guards and runtime validation.

### 2. Project Configuration
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

### 3. Types and Interfaces

#### Prefer Interfaces for Objects
- Use `interface` to define object shapes that can be extended.
- Use `type` for unions, intersections, and more complex types.

```typescript
// ✅ Good
interface User {
  id: string;
  name: string;
  email: string;
}

// ✅ Good for unions
type Status = 'pending' | 'approved' | 'rejected';

// ❌ Avoid
type User = {
  id: string;
  name: string;
}
```

#### Generic Types
- Use descriptive names for generic parameters: `T`, `K`, `V` for simple cases, more descriptive names for complex cases.
- Apply constraints when appropriate.

```typescript
// ✅ Good
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// ✅ Good with constraints
interface HasId {
  id: string;
}

function findById<T extends HasId>(items: T[], id: string): T | undefined {
  return items.find(item => item.id === id);
}
```

### 4. Null and Undefined Handling

- **Use optional types** (`?`) instead of `| null | undefined` when appropriate.
- **Use nullish coalescing** (`??`) instead of `||` when you want to handle only `null`/`undefined`.
- **Use optional chaining** (`?.`) to access nested properties.

```typescript
// ✅ Good
interface Config {
  apiUrl?: string;
  timeout?: number;
}

const url = config.apiUrl ?? 'https://api.example.com';
const value = obj?.nested?.property;
```

### 5. Functions

#### Explicit Return Types
- Always specify the return type of public functions.
- Use `void` explicitly when a function returns nothing.

```typescript
// ✅ Good
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

function logMessage(message: string): void {
  console.log(message);
}
```

#### Async Functions
- Use `Promise<T>` explicitly when necessary.
- Prefer `async/await` over `.then()` for better readability.

```typescript
// ✅ Good
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error('Failed to fetch user');
  }
  return response.json();
}
```

### 6. Error Handling

- **Use specific error types** instead of generic `Error` when possible.
- **Validate data at runtime** before assuming types.

```typescript
// ✅ Good
class ValidationError extends Error {
  constructor(public field: string, message: string) {
    super(message);
    this.name = 'ValidationError';
  }
}

function validateUser(data: unknown): User {
  if (!isUser(data)) {
    throw new ValidationError('user', 'Invalid user data');
  }
  return data;
}

function isUser(data: unknown): data is User {
  return (
    typeof data === 'object' &&
    data !== null &&
    'id' in data &&
    'name' in data &&
    'email' in data
  );
}
```

### 7. TypeScript Utilities

#### Type Guards
- Create type guards for runtime type validation.

```typescript
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

function isNumber(value: unknown): value is number {
  return typeof value === 'number' && !isNaN(value);
}
```

#### Utility Types
- Leverage built-in utility types: `Partial<T>`, `Required<T>`, `Pick<T, K>`, `Omit<T, K>`, `Record<K, V>`, etc.

```typescript
// ✅ Good
type UserUpdate = Partial<Pick<User, 'name' | 'email'>>;
type UserPreview = Omit<User, 'password' | 'tokens'>;
```

### 8. Code Organization

#### Imports and Exports
- Use named `export` instead of `export default` when possible.
- Group imports: external first, then internal.

```typescript
// ✅ Good
import { useState, useEffect } from 'react';
import { UserService } from './services/UserService';
import type { User, UserRole } from './types';
```

#### Namespaces and Modules
- Prefer ES6 modules over namespaces when possible.
- Use namespaces only to group related types.

### 9. Performance and Best Practices

- **Avoid complex recursive types** that may slow down the compiler.
- **Use `const` assertions** for more precise type inference.

```typescript
// ✅ Good
const colors = ['red', 'green', 'blue'] as const;
type Color = typeof colors[number]; // 'red' | 'green' | 'blue'
```

- **Use `satisfies`** (TypeScript 4.9+) to validate types without losing inference.

```typescript
// ✅ Good
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
} satisfies Config;
```

### 10. Testing and TypeScript

- Use strict types in tests.
- Create helper types for mocks and fixtures.

```typescript
// ✅ Good
type MockUser = Partial<User> & { id: string };

function createMockUser(overrides?: Partial<MockUser>): User {
  return {
    id: '1',
    name: 'Test User',
    email: 'test@example.com',
    ...overrides,
  };
}
```

### 11. Decorators and Metadata

- Use decorators only when necessary (Angular, NestJS, etc.).
- Document the use of custom decorators.

### 12. Compatibility and Versions

- **Specify minimum TypeScript version** in `package.json`.
- **Use `@types` packages** for typing JavaScript libraries.
- **Keep types updated** with dependency versions.

### 13. Documentation

- Use JSDoc to document functions and complex types.
- Include usage examples when appropriate.

```typescript
/**
 * Calculates the total of an item list applying discounts.
 * 
 * @param items - List of items to calculate
 * @param discount - Discount percentage (0-100)
 * @returns The calculated total with discount applied
 * 
 * @example
 * ```typescript
 * const total = calculateTotalWithDiscount(items, 10);
 * ```
 */
function calculateTotalWithDiscount(items: Item[], discount: number): number {
  // ...
}
```

### 14. Linting Rules

- Configure ESLint with TypeScript-specific rules.
- Use `@typescript-eslint/recommended` and `@typescript-eslint/recommended-requiring-type-checking`.

### 15. Migration and Refactoring

- Use `ts-migrate` or similar tools for large migrations.
- Refactor gradually, enabling strict rules progressively.
- Use `// @ts-expect-error` with explanatory comments instead of `// @ts-ignore`.

## Review Checklist

Before considering TypeScript code complete, verify:

- [ ] No use of `any` without justification
- [ ] All return types are specified
- [ ] Type guards are used for runtime validation
- [ ] Generic types have appropriate constraints
- [ ] `null` and `undefined` are handled correctly
- [ ] Errors have specific types
- [ ] Code passes all strict type checks
- [ ] Imports are organized correctly
- [ ] JSDoc documentation is present for public functions
- [ ] Project naming conventions are followed

## Additional Resources

- TypeScript Handbook: https://www.typescriptlang.org/docs/handbook/intro.html
- TypeScript Deep Dive: https://basarat.gitbook.io/typescript/
- Effective TypeScript: https://effectivetypescript.com/
