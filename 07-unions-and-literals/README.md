# Unions and Literals

## Why This Topic Matters

Real-world code needs to handle multiple types and restricted values. Union and literal types enable:

- **Restrictive types** - Only allow specific values
- **Flexible functions** - Accept multiple input types safely
- **Type-safe enums** - Better than magic strings
- **Readable code** - Self-documenting types

Without these, you'll either use loose types or `if-else` chains.

## Core Concept

### Union Types

A value can be **one of several types**:

```typescript
type Result = string | number; // Can be string OR number

let value: Result = "success"; // ✅ string
value = 200; // ✅ number
// value = true;                // ❌ Can't be boolean
```

### Literal Types

A value must be **exactly one specific value**:

```typescript
type Status = "pending" | "success" | "error"; // Must be one of these

let status: Status = "pending"; // ✅
// status = "unknown";           // ❌ Must be exactly one of the allowed values
```

## Syntax

### Union Types

```typescript
// Two types
type StringOrNumber = string | number;

// Multiple types
type Response = string | number | boolean | null;

// Objects or primitives
type Value = { name: string } | "default";

// Arrays
type StringOrStringArray = string | string[];

// Functions
type Callback = (error: Error | null) => void;

// Union with arrays
type Data = User[] | Error | null;
```

### Literal Types

```typescript
// String literals
type Status = "pending" | "success" | "error";
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

// Number literals
type StatusCode = 200 | 201 | 400 | 401 | 404 | 500;

// Boolean literals (rarely useful)
type IsTrue = true;

// Mixed literals
type Response = "success" | 200 | true;

// Combining literals and types
type Result =
  | { status: "success"; data: any }
  | { status: "error"; error: string };
```

## Examples

### Example 1: Union Types for Flexibility

```typescript
// ❌ Without union - either too strict or too loose
function processValue(value: any) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "number") {
    console.log(value.toFixed(2));
  }
}

// ✅ With union - type-safe and clear
type Value = string | number;

function processValue(value: Value): void {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // ✅ TypeScript knows it's a string
  } else {
    console.log(value.toFixed(2)); // ✅ TypeScript knows it's a number
  }
}

processValue("hello"); // ✅
processValue(42); // ✅
// processValue(true);  // ❌ ERROR: Argument of type 'boolean' is not assignable to type 'Value'
```

### Example 2: Literal Types for Status

```typescript
// ❌ Using strings - easy to typo
function updateStatus(status: string) {
  // What valid values are there?
  // "pending"? "processing"? "complete"? "finished"?
}

updateStatus("pneding"); // Typo! But no error in JavaScript

// ✅ Using literal types - catch typos at compile time
type OrderStatus = "pending" | "processing" | "completed" | "cancelled";

function updateStatus(status: OrderStatus): void {
  // Only valid values allowed
}

updateStatus("pending"); // ✅
updateStatus("processing"); // ✅
// updateStatus("pneding");   // ❌ ERROR: '"pneding"' is not assignable to type 'OrderStatus'
```

### Example 3: Complex Union Types

```typescript
// API responses as union
type ApiResponse<T> =
  | { status: "success"; data: T }
  | { status: "error"; error: string }
  | { status: "loading"; progress: number };

// Handle different response types
function handleResponse<T>(response: ApiResponse<T>): void {
  if (response.status === "success") {
    console.log("Data:", response.data);
  } else if (response.status === "error") {
    console.log("Error:", response.error);
  } else {
    console.log("Loading:", response.progress);
  }
}

// Usage
const userResponse: ApiResponse<{ name: string }> = {
  status: "success",
  data: { name: "Alice" },
};

handleResponse(userResponse);
```

### Example 4: Union with Optional

```typescript
// Union with null/undefined
type User = {
  id: number;
  name: string;
  email: string | null; // Can be string or null
};

function displayEmail(user: User): void {
  if (user.email) {
    console.log(`Email: ${user.email}`);
  } else {
    console.log("Email not provided");
  }
}

// Both valid:
displayEmail({ id: 1, name: "Alice", email: "alice@example.com" });
displayEmail({ id: 2, name: "Bob", email: null });
```

### Example 5: Discriminated Unions

```typescript
// Discriminated union - use a common property to distinguish types
type Circle = {
  kind: "circle";
  radius: number;
};

type Square = {
  kind: "square";
  side: number;
};

type Rectangle = {
  kind: "rectangle";
  width: number;
  height: number;
};

type Shape = Circle | Square | Rectangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius * shape.radius;
    case "square":
      return shape.side * shape.side;
    case "rectangle":
      return shape.width * shape.height;
  }
}

// TypeScript knows the properties for each case!
const circle: Circle = { kind: "circle", radius: 5 };
const square: Square = { kind: "square", side: 10 };

console.log(getArea(circle)); // ~78.5
console.log(getArea(square)); // 100
```

## Common Mistakes

### Mistake 1: Over-Using Unions

```typescript
// ❌ Too broad - defeats type safety
type Data = string | number | boolean | null | undefined | any[];

// ✅ Be specific
type UserResponse =
  | { success: true; user: User }
  | { success: false; error: string };
```

### Mistake 2: Forgetting to Handle All Cases

```typescript
type Status = "pending" | "success" | "error";

// ❌ Missing a case
function statusMessage(status: Status): string {
  if (status === "pending") {
    return "Loading...";
  } else if (status === "success") {
    return "Done!";
  }
  // What about "error"?
  return ""; // ❌ Incomplete
}

// ✅ Handle all cases
function statusMessage(status: Status): string {
  switch (status) {
    case "pending":
      return "Loading...";
    case "success":
      return "Done!";
    case "error":
      return "Failed!";
  }
}

// ✅ Or use exhaustive checking
function statusMessage(status: Status): string {
  if (status === "pending") {
    return "Loading...";
  } else if (status === "success") {
    return "Done!";
  } else if (status === "error") {
    return "Failed!";
  }
  // TypeScript won't complain here because all cases are covered
  const _exhaustive: never = status;
  return _exhaustive;
}
```

### Mistake 3: Not Using Literal Types for Enums

```typescript
// ❌ Magic strings
function updatePermission(permission: string) {
  // "admin"? "user"? "viewer"?
  // Easy to typo
}

// ✅ Literal types
type Permission = "admin" | "user" | "viewer";

function updatePermission(permission: Permission) {
  // Only valid values allowed
}
```

### Mistake 4: Union with Common Properties

```typescript
// ❌ Hard to tell them apart
type Response = { status: string } | { status: string; data: any };

// ✅ Use discriminated unions
type SuccessResponse = { status: "success"; data: any };
type ErrorResponse = { status: "error"; error: string };
type Response = SuccessResponse | ErrorResponse;
```

## Practice Tasks

1. **Create Union Types**
   - Create a union type that can be string or number
   - Create a union type for common HTTP methods
   - Write functions that use these types

2. **Literal Types**
   - Create a literal type for user roles ("admin", "user", "guest")
   - Create a function that accepts only these roles

3. **Handle Union Types**
   - Create a function that takes a string or number
   - Check the type inside the function and act accordingly

4. **Discriminated Unions**
   - Create types for success and error responses
   - Create a function that handles both

5. **Complex Unions**
   - Create a union with multiple types and objects
   - Create a type guard function for each type

## Mini Project: State Machine with Unions

Create a type-safe state machine:

```typescript
type State =
  | { kind: "idle" }
  | { kind: "loading"; url: string }
  | { kind: "success"; data: any }
  | { kind: "error"; error: string };

class StateMachine {
  private state: State = { kind: "idle" };

  async fetchData(url: string): Promise<void> {
    this.state = { kind: "loading", url };

    try {
      const response = await fetch(url);
      const data = await response.json();
      this.state = { kind: "success", data };
    } catch (error) {
      this.state = { kind: "error", error: String(error) };
    }
  }

  getStatus(): string {
    switch (this.state.kind) {
      case "idle":
        return "Not started";
      case "loading":
        return `Loading from ${this.state.url}`;
      case "success":
        return "Data loaded successfully";
      case "error":
        return `Error: ${this.state.error}`;
    }
  }

  reset(): void {
    this.state = { kind: "idle" };
  }
}

// Usage:
const sm = new StateMachine();
console.log(sm.getStatus()); // "Not started"
sm.fetchData("https://api.example.com/data");
```

## Official TypeScript Docs

- **Union Types**: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#union-types
- **Literal Types**: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types
- **Discriminated Unions**: https://www.typescriptlang.org/docs/handbook/2/types-from-types.html#discriminated-unions

## Previous Topic

← [Type Aliases and Interfaces](../06-type-aliases-and-interfaces/README.md)

## Next Topic

→ [Type Narrowing](../08-type-narrowing/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
