# TypeScript Interview Preparation

Common interview questions and comprehensive answers.

---

## Fundamental Concepts

### Q1: What is TypeScript and why use it?

**Answer:**
TypeScript is a superset of JavaScript that adds static typing and advanced features. Benefits include:

```typescript
// Type safety catches errors at compile time
const age: number = "25"; // Error - type mismatch

// IntelliSense and autocomplete support
const user: User = { id: 1, name: "Alice" };
user. // IDE shows available properties

// Self-documenting code
function processUser(user: User): Promise<UserData> {
  // Clear what types are expected
}
```

---

### Q2: Explain the difference between `any`, `unknown`, and `never`

**Answer:**
```typescript
// any - disables type checking (avoid!)
let anything: any = "string";
anything.toNumber(); // No error, but could fail at runtime

// unknown - type-safe alternative to any
let unknown_value: unknown = "string";
unknown_value.toUpperCase(); // Error - unsafe
if (typeof unknown_value === "string") {
  unknown_value.toUpperCase(); // OK - narrowed
}

// never - represents impossible values
function throwError(): never {
  throw new Error("Error");
}

type Impossible = string & number; // never type
```

---

### Q3: What are generics and when do you use them?

**Answer:**
```typescript
// Generics create reusable components with type safety
function identity<T>(value: T): T {
  return value;
}

// Constraints on generics
function getProperty<T extends { name: string }>(obj: T): string {
  return obj.name;
}

// Generic classes and interfaces
class Container<T> {
  constructor(private value: T) {}
  getValue(): T { return this.value; }
}

// Use cases: reusable data structures, utility functions, type-safe APIs
```

---

### Q4: What is the difference between `interface` and `type`?

**Answer:**
```typescript
// Interface - best for object shapes
interface Animal {
  name: string;
  sound(): void;
}

// Type - more flexible (unions, primitives, etc.)
type Animal = { name: string; sound(): void };

// Key differences:
// 1. Declaration merging (interface only)
interface User { id: number; }
interface User { name: string; } // Merges

// 2. Unions (type only)
type Result = Success | Error;

// 3. Built-ins (type only)
type Nullable<T> = T | null;

// Modern recommendation: Use interface for objects, type for unions
```

---

### Q5: Explain type narrowing techniques

**Answer:**
```typescript
// typeof guard
function process(value: string | number): void {
  if (typeof value === "string") {
    value.toUpperCase(); // Narrowed to string
  }
}

// instanceof guard
if (error instanceof Error) {
  console.log(error.message); // Error properties available
}

// User-defined type guard
function isUser(obj: any): obj is User {
  return obj.id && obj.name;
}

// Discriminated union
type Result = { success: true; data: any } | { success: false; error: string };
if (result.success) {
  console.log(result.data); // data available
}

// The 'in' operator
if ("name" in obj) {
  console.log(obj.name);
}
```

---

## Advanced Concepts

### Q6: What are utility types and give examples?

**Answer:**
```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

// Partial - all properties optional
type UserPreview = Partial<User>;

// Required - all properties required
type RequiredUser = Required<UserPreview>;

// Pick - select specific properties
type UserPublic = Pick<User, "id" | "name">;

// Omit - exclude specific properties
type UserWithoutPassword = Omit<User, "password">;

// Record - create object with specific keys
type AdminLevel = Record<"super" | "admin" | "moderator", number>;

// Readonly - make all properties readonly
type ReadonlyUser = Readonly<User>;
```

---

### Q7: Explain mapped types and conditional types

**Answer:**
```typescript
// Mapped types - transform object properties
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

// Conditional types - type based on condition
type IsString<T> = T extends string ? true : false;

type Flatten<T> = T extends Array<infer U> ? U : T;
type Str = Flatten<string[]>; // string
type Num = Flatten<number>; // number

// Use case: Choosing return type based on input
type Awaited<T> = T extends Promise<infer U> ? U : T;
```

---

### Q8: How do you handle errors in TypeScript?

**Answer:**
```typescript
// Custom error class
class AppError extends Error {
  constructor(message: string, public statusCode: number) {
    super(message);
    this.name = "AppError";
  }
}

// Try-catch with proper typing
async function fetchUser(id: number): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      throw new AppError(`User not found`, 404);
    }
    return response.json();
  } catch (error) {
    if (error instanceof AppError) {
      console.error(`Error ${error.statusCode}: ${error.message}`);
    } else if (error instanceof Error) {
      console.error(`Error: ${error.message}`);
    } else {
      console.error("Unknown error occurred");
    }
    throw error;
  }
}

// Result type pattern
type Result<T, E = Error> = 
  | { ok: true; value: T }
  | { ok: false; error: E };
```

---

### Q9: Explain decorators and their use cases

**Answer:**
```typescript
// Enable experimentalDecorators in tsconfig.json

// Method decorator
function LogMethod(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function(...args: any[]) {
    console.log(`Calling ${key}`, args);
    return original.apply(this, args);
  };
}

class User {
  @LogMethod
  greet(name: string): string {
    return `Hello ${name}`;
  }
}

// Class decorator
function Serializable(constructor: Function) {
  constructor.prototype.toJSON = function() {
    return JSON.stringify(this);
  };
}

@Serializable
class Document {
  title: string = "Doc";
}
```

---

### Q10: What are ambient declarations and .d.ts files?

**Answer:**
```typescript
// types/custom-module.d.ts
declare module "custom-module" {
  export function process(data: string): number;
  export interface Config {
    timeout: number;
  }
}

// global.d.ts - ambient declarations
declare global {
  interface Window {
    customAPI: {
      getData(): Promise<any>;
    };
  }
}

// Usage - no import needed
customAPI.getData();

// .d.ts files - type definitions for libraries
// myLib/index.d.ts
export function greet(name: string): string;
export interface User { id: number; name: string; }
```

---

## Performance & Architecture

### Q11: How would you optimize TypeScript performance?

**Answer:**
```typescript
// 1. Use incremental compilation
// tsconfig.json
{
  "compilerOptions": {
    "incremental": true,
    "tsBuildInfoFile": ".tsbuildinfo"
  }
}

// 2. Lazy load modules
async function loadFeature() {
  const { Feature } = await import("./features/Feature");
  return new Feature();
}

// 3. Use const assertions for literal types
const config = { timeout: 5000 } as const;
// Type: { readonly timeout: 5000 }

// 4. Avoid deep nested types
type Bad = Nested<Nested<Nested<T>>>;
type Good = Simplified<T>;
```

---

### Q12: Explain dependency injection pattern

**Answer:**
```typescript
// Constructor injection
class UserService {
  constructor(private repo: UserRepository) {}
  
  async getUser(id: number): Promise<User> {
    return this.repo.findById(id);
  }
}

// Benefit: Testable with mock repository
class MockUserRepository implements UserRepository {
  async findById(id: number): Promise<User> {
    return { id, name: "Test User" };
  }
}

// Usage in tests
const mockRepo = new MockUserRepository();
const service = new UserService(mockRepo);
```

---

## Practical Questions

### Q13: How do you structure a large TypeScript project?

**Answer:**
```typescript
// Typical structure
src/
  core/              // Core business logic
    domain/
    services/
  features/          // Feature modules
    users/
      types.ts
      service.ts
    products/
  infrastructure/    // External integrations
    database/
    cache/
  presentation/      // Controllers/handlers
  middleware/
  config/
  utils/

// Benefits: modularity, scalability, maintainability
```

---

### Q14: Write a debounce function with proper typing

**Answer:**
```typescript
function debounce<T extends (...args: any[]) => any>(
  fn: T,
  delay: number
): (...args: Parameters<T>) => void {
  let timeoutId: NodeJS.Timeout;
  
  return (...args: Parameters<T>): void => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn(...args), delay);
  };
}

// Usage
const search = debounce((query: string) => {
  console.log("Searching for:", query);
}, 500);

search("TypeScript");
```

---

### Q15: How would you validate API responses?

**Answer:**
```typescript
// Using zod for schema validation
import { z } from "zod";

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
});

async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  
  // Validates and returns typed result
  return UserSchema.parse(data);
}

// Type inference
type User = z.infer<typeof UserSchema>;
```

---

## Tips for Interview Success

1. **Understand the basics** - `any`, `unknown`, `never`, type narrowing
2. **Know utility types** - `Partial`, `Pick`, `Omit`, `Record`
3. **Practice typing code** - Add types to real-world scenarios
4. **Understand generics** - Most important advanced topic
5. **Know the differences** - interface vs type, async patterns
6. **Ask clarifying questions** - Never assume requirements
7. **Code along** - Show your thought process
8. **Discuss trade-offs** - Why choose one approach over another

---

## Resources

- **Official Docs:** typescriptlang.org/docs
- **Handbook:** typescriptlang.org/docs/handbook
- **Playground:** typescriptlang.org/play
- **Advanced Types:** github.com/type-challenges/type-challenges

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
