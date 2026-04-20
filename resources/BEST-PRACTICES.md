# TypeScript Best Practices

Production-ready patterns, conventions, and optimization strategies.

---

## Code Organization

### ✅ Organize by Feature

```typescript
// Good
src/
  features/
    users/
      types.ts
      service.ts
      repository.ts
      index.ts
    products/
      types.ts
      service.ts
      repository.ts
      index.ts
```

### ❌ Avoid Generic Grouping

```typescript
// Bad - mixes unrelated concerns
src/
  models/
  services/
  controllers/
```

---

## Type Definitions

### ✅ Use Explicit Types

```typescript
// Good
interface User {
  id: number;
  name: string;
  email: string;
}

function getUser(id: number): Promise<User> {
  // implementation
}
```

### ❌ Avoid `any`

```typescript
// Bad
function getUser(id: any): any {
  // loses type information
}
```

---

## Configuration Management

### ✅ Separate Config from Code

```typescript
// config/database.ts
export const dbConfig = {
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || "5432"),
  database: process.env.DB_NAME,
};

// services/database.ts
import { dbConfig } from "../config/database";

export function initDatabase() {
  // use dbConfig
}
```

### ✅ Validate Configuration

```typescript
// config/env.ts
const envSchema = {
  NODE_ENV: z.enum(["development", "production", "test"]),
  DATABASE_URL: z.string().url(),
  PORT: z.string().transform(Number),
};

export const config = envSchema.parse(process.env);
```

---

## Error Handling

### ✅ Create Custom Errors

```typescript
// errors/AppError.ts
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500,
    public isOperational: boolean = true
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 400);
  }
}
```

### ✅ Handle Errors Consistently

```typescript
// middleware/errorHandler.ts
export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  if (error instanceof AppError) {
    return res.status(error.statusCode).json({
      status: "error",
      message: error.message,
    });
  }

  // Log unexpected errors
  console.error("Unexpected error:", error);
  res.status(500).json({ status: "error", message: "Internal server error" });
}
```

---

## Async Patterns

### ✅ Use Async/Await

```typescript
// Good
async function fetchUserData(userId: number): Promise<UserData> {
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) throw new Error("Failed to fetch");
    return response.json();
  } catch (error) {
    throw new AppError("Failed to fetch user", 500);
  }
}
```

### ❌ Avoid Callback Hell

```typescript
// Bad - nested callbacks
function fetchUserData(id: number, callback: Function) {
  fetch(`/api/users/${id}`, (err, user) => {
    if (err) {
      fetch(`/api/users/${id}/default`, (err, defaultUser) => {
        callback(defaultUser);
      });
    } else {
      callback(user);
    }
  });
}
```

---

## Dependency Injection

### ✅ Inject Dependencies

```typescript
// services/UserService.ts
export class UserService {
  constructor(private userRepository: UserRepository) {}

  async getUser(id: number): Promise<User> {
    return this.userRepository.findById(id);
  }
}

// Composition
const userRepository = new UserRepository(database);
const userService = new UserService(userRepository);
```

### ❌ Avoid Global State

```typescript
// Bad - hard to test
export let userRepository = new UserRepository();

export class UserService {
  async getUser(id: number) {
    return userRepository.findById(id);
  }
}
```

---

## Testing

### ✅ Write Testable Code

```typescript
// Good - pure function
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// Test
describe("calculateTotal", () => {
  it("should sum item prices", () => {
    expect(calculateTotal([{ price: 10 }, { price: 20 }])).toBe(30);
  });
});
```

### ✅ Mock Dependencies

```typescript
// Mock repository for testing
class MockUserRepository implements UserRepository {
  findById(id: number): Promise<User> {
    return Promise.resolve({ id, name: "Test User" });
  }
}

// Use in tests
const mockRepo = new MockUserRepository();
const userService = new UserService(mockRepo);
```

---

## Performance Optimization

### ✅ Use Memoization

```typescript
// Expensive computation
function memoize<Args extends any[], Return>(
  fn: (...args: Args) => Return
): (...args: Args) => Return {
  const cache = new Map<string, Return>();
  
  return (...args: Args): Return => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key)!;
    
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

const expensiveFunction = memoize((n: number) => {
  // Heavy computation
  return n * 2;
});
```

### ✅ Lazy Load Modules

```typescript
// Load module only when needed
async function loadFeature(): Promise<Feature> {
  const { createFeature } = await import("./features/feature");
  return createFeature();
}
```

---

## Type Narrowing Best Practices

### ✅ Use Discriminated Unions

```typescript
type Result<T> =
  | { success: true; data: T }
  | { success: false; error: string };

function handleResult<T>(result: Result<T>): T {
  if (result.success) {
    return result.data; // TypeScript knows this exists
  } else {
    throw new Error(result.error);
  }
}
```

### ✅ Create Type Guards

```typescript
function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" &&
    obj !== null &&
    "id" in obj &&
    "name" in obj
  );
}

const data: unknown = { id: 1, name: "Alice" };
if (isUser(data)) {
  console.log(data.name); // Safe
}
```

---

## Documentation

### ✅ Document Complex Types

```typescript
/**
 * User preferences for notifications
 * @example
 * const prefs: UserPreferences = {
 *   emailNotifications: true,
 *   pushNotifications: false,
 *   frequency: "daily"
 * };
 */
interface UserPreferences {
  emailNotifications: boolean;
  pushNotifications: boolean;
  frequency: "instant" | "daily" | "weekly";
}
```

### ✅ Document Public APIs

```typescript
/**
 * Validates and transforms user input
 * @param input - User-provided data
 * @returns Validated user object
 * @throws ValidationError if input is invalid
 */
export function validateUser(input: unknown): User {
  // implementation
}
```

---

## Tsconfig Best Practices

### ✅ Enable Strict Mode

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true,
    "alwaysStrict": true
  }
}
```

---

## Common Pitfalls

### ❌ Don't Over-Engineer

```typescript
// Too complex
type DeepPartial<T> = T extends object
  ? { [K in keyof T]?: DeepPartial<T[K]> }
  : T;

// Use when actually needed
type UserPreview = Partial<User>;
```

### ❌ Don't Ignore Warnings

```typescript
// Bad - ignoring TypeScript errors
// @ts-ignore
const data: string = 123;
```

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
