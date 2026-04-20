# Generics

## Why This Topic Matters

Generics allow you to write flexible, reusable code that works with any type. They're essential for:

- **Reusable components** - One component works with many types
- **API consistency** - Type-safe data structures
- **Framework code** - React, Express, and other libraries use generics heavily
- **Type safety** - No more `any` types
- **DRY principle** - Don't repeat type definitions

Generics are how TypeScript powers frameworks and libraries.

## Core Concept

Generics are **type variables** that hold different types:

```typescript
// Without generics - too loose
function getFirst(arr: any[]): any {
  return arr[0];
}

// With generics - type-safe
function getFirst<T>(arr: T[]): T {
  return arr[0];
}

// Usage - TypeScript infers T
const first = getFirst([1, 2, 3]); // T is number
const name = getFirst(["a", "b", "c"]); // T is string
```

Think of `<T>` like a **function parameter, but for types**.

## Syntax

### Basic Generics

```typescript
// Single generic
function identity<T>(value: T): T {
  return value;
}

// Multiple generics
function combine<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

// Generic with constraints
function longest<T extends { length: number }>(a: T, b: T): T {
  return a.length > b.length ? a : b;
}

// Generic interface
interface Container<T> {
  value: T;
  get(): T;
  set(value: T): void;
}

// Generic class
class Box<T> {
  private content: T;

  constructor(value: T) {
    this.content = value;
  }

  get(): T {
    return this.content;
  }
}

// Generic with default
function wrap<T = string>(value: T): T[] {
  return [value];
}
```

## Examples

### Example 1: Generic Function

```typescript
// ❌ Without generics - loses type information
function getFirstItem(arr: any[]): any {
  return arr[0]; // Returns 'any' - no autocomplete
}

const num = getFirstItem([1, 2, 3]);
// num.toFixed();  // Works but no type info

// ✅ With generics - preserves type
function getFirstItem<T>(arr: T[]): T {
  return arr[0];
}

const num = getFirstItem([1, 2, 3]);
num.toFixed(); // ✅ TypeScript knows it's a number

const name = getFirstItem(["Alice", "Bob"]);
name.toUpperCase(); // ✅ TypeScript knows it's a string
```

### Example 2: Generic Array Function

```typescript
// Map function with generics
function map<T, U>(arr: T[], fn: (item: T) => U): U[] {
  return arr.map(fn);
}

// Usage
const numbers = [1, 2, 3];
const strings = map(numbers, (num) => num.toString()); // string[]
const lengths = map(strings, (str) => str.length); // number[]

// T is number, U is string
// T is string, U is number
```

### Example 3: Generic Interface

```typescript
// API Response wrapper
interface ApiResponse<T> {
  status: number;
  data: T;
  timestamp: string;
}

// Use with different types
interface User {
  id: number;
  name: string;
}

interface Product {
  id: number;
  title: string;
  price: number;
}

const userResponse: ApiResponse<User> = {
  status: 200,
  data: { id: 1, name: "Alice" },
  timestamp: "2024-01-01",
};

const productResponse: ApiResponse<Product> = {
  status: 200,
  data: { id: 1, title: "Laptop", price: 999 },
  timestamp: "2024-01-01",
};

// Same type, different data shapes!
```

### Example 4: Generic Class

```typescript
// Generic cache
class Cache<T> {
  private items: Map<string, T> = new Map();

  set(key: string, value: T): void {
    this.items.set(key, value);
  }

  get(key: string): T | undefined {
    return this.items.get(key);
  }

  clear(): void {
    this.items.clear();
  }
}

// Usage
const stringCache = new Cache<string>();
stringCache.set("name", "Alice");
const name = stringCache.get("name"); // string | undefined

const numberCache = new Cache<number>();
numberCache.set("count", 42);
const count = numberCache.get("count"); // number | undefined
```

### Example 5: Generic Constraints

```typescript
// Ensure T has a certain property
function getLength<T extends { length: number }>(value: T): number {
  return value.length;
}

getLength("hello"); // ✅ strings have length
getLength([1, 2, 3]); // ✅ arrays have length
getLength({ length: 5 }); // ✅ objects with length property

// getLength(42);           // ❌ ERROR: number doesn't have length

// More complex constraint
interface HasId {
  id: number;
}

function getId<T extends HasId>(obj: T): number {
  return obj.id;
}

getId({ id: 1, name: "Alice" }); // ✅
// getId({ name: "Alice" });       // ❌ missing id
```

### Example 6: Generic with Default

```typescript
// Generic with default type
function createArray<T = string>(length: number, value: T): T[] {
  return Array(length).fill(value);
}

const strings = createArray(3); // T is string (default)
const numbers = createArray(3, 42); // T is number
const strings2 = createArray<string>(3, "x"); // T is explicitly string
```

## Common Mistakes

### Mistake 1: Over-Generalizing

```typescript
// ❌ Too generic - loses information
function process<T>(value: T): T {
  // Can't do anything with T - we don't know what it is
  return value;
}

// ✅ Add constraints if needed
function process<T extends { name: string }>(value: T): string {
  return value.name.toUpperCase();
}
```

### Mistake 2: Forgetting Generic Parameter

```typescript
// ❌ Missing generic parameter
// const cache = new Cache();  // T is unknown

// ✅ Specify the type
const cache = new Cache<string>();
```

### Mistake 3: Not Using Type Parameter

```typescript
// ❌ Defines T but doesn't use it
function wrapper<T>(value: string): string {
  return value;
}

// ✅ Use the generic parameter
function wrapper<T>(value: T): T {
  return value;
}
```

### Mistake 4: Wrong Constraint Syntax

```typescript
// ❌ Wrong
// function process<T where T extends string>(value: T) {}

// ✅ Correct
function process<T extends string>(value: T): void {
  console.log(value);
}
```

## Practice Tasks

1. **Basic Generic Function**
   - Create a generic function that returns its input
   - Test it with different types

2. **Generic Array Function**
   - Create a generic filter function
   - Create a generic map function

3. **Generic Interface**
   - Create a generic Response interface
   - Use it with different data types

4. **Generic Class**
   - Create a generic Stack class
   - Implement push and pop methods

5. **Generic Constraints**
   - Create a generic function with extends constraint
   - Test with objects that have the required properties

6. **Multiple Generics**
   - Create a function with two generic parameters
   - Use it to merge or combine types

## Mini Project: Type-Safe API Service

Create a type-safe API service using generics:

```typescript
interface ApiResponse<T> {
  status: number;
  data: T;
  error?: string;
}

interface User {
  id: number;
  name: string;
  email: string;
}

interface Product {
  id: number;
  title: string;
  price: number;
}

class ApiClient {
  async get<T>(url: string): Promise<ApiResponse<T>> {
    const response = await fetch(url);
    const data = await response.json();
    return {
      status: response.status,
      data,
      error: response.ok ? undefined : "Request failed",
    };
  }

  async post<T, R>(url: string, payload: T): Promise<ApiResponse<R>> {
    const response = await fetch(url, {
      method: "POST",
      body: JSON.stringify(payload),
    });
    const data = await response.json();
    return {
      status: response.status,
      data,
      error: response.ok ? undefined : "Request failed",
    };
  }
}

// Usage:
const client = new ApiClient();

// Fetch users - response data is User[]
const usersResponse = await client.get<User[]>("/api/users");

// Fetch single product
const productResponse = await client.get<Product>("/api/products/1");

// Create a user - send User, get User response
const newUserResponse = await client.post<User, User>("/api/users", {
  id: 0,
  name: "New User",
  email: "new@example.com",
});
```

## Official TypeScript Docs

- **Generics**: https://www.typescriptlang.org/docs/handbook/2/generics.html
- **Generic Constraints**: https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-constraints
- **Using Type Parameters in Generic Constraints**: https://www.typescriptlang.org/docs/handbook/2/generics.html#using-type-parameters-in-generic-constraints

## Previous Topic

← [Type Narrowing](../08-type-narrowing/README.md)

## Next Topic

→ [Classes and OOP](../10-classes-and-oop/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
