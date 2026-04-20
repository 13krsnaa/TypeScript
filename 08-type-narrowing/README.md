# Type Narrowing

## Why This Topic Matters

With union types, you often need to figure out which type you actually have. Type narrowing lets you:

- **Safely use union types** - Check what type you have before using it
- **Write type-safe code** - TypeScript helps you not forget checks
- **Reduce runtime errors** - Catch type mismatches early
- **Improve code clarity** - Explicit type checks make code easier to read

Without proper narrowing, union types are hard to work with.

## Core Concept

When you have a union type, TypeScript forces you to check which type it actually is before using type-specific methods:

```typescript
type Value = string | number;

function example(value: Value) {
  // Can't use string methods yet - don't know if it's a string
  // value.toUpperCase();  // ❌ ERROR

  // First narrow to string
  if (typeof value === "string") {
    value.toUpperCase(); // ✅ Now it's safe
  } else {
    // Now we know it's a number
    value.toFixed(2); // ✅ Safe
  }
}
```

## Narrowing Techniques

### 1. `typeof` Guard

```typescript
type Value = string | number | boolean;

function process(value: Value) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "number") {
    console.log(value.toFixed(2));
  } else {
    console.log(value);
  }
}
```

### 2. `instanceof` Guard

```typescript
class Dog {
  bark() {
    return "Woof!";
  }
}

class Cat {
  meow() {
    return "Meow!";
  }
}

type Animal = Dog | Cat;

function makeSound(animal: Animal) {
  if (animal instanceof Dog) {
    console.log(animal.bark());
  } else if (animal instanceof Cat) {
    console.log(animal.meow());
  }
}
```

### 3. Property Check (`in` Operator)

```typescript
interface Admin {
  name: string;
  permission: string;
}

interface User {
  name: string;
  email: string;
}

type Person = Admin | User;

function describe(person: Person) {
  if ("permission" in person) {
    // Narrowed to Admin
    console.log(`Admin with permission: ${person.permission}`);
  } else {
    // Narrowed to User
    console.log(`User with email: ${person.email}`);
  }
}
```

### 4. Truthiness Check

```typescript
function printLength(value: string | null) {
  if (value) {
    // value can't be null here
    console.log(value.length);
  } else {
    console.log("Value is null or empty");
  }
}
```

### 5. Discriminator Pattern

```typescript
type Response =
  | { status: "success"; data: any }
  | { status: "error"; error: string };

function handle(response: Response) {
  // Use the 'status' property to narrow
  if (response.status === "success") {
    console.log("Data:", response.data);
  } else {
    console.log("Error:", response.error);
  }
}
```

## Examples

### Example 1: String or Number

```typescript
// ❌ Without narrowing
function add(a: string | number, b: string | number) {
  return a + b; // ❌ ERROR: + operator doesn't work on all combinations
}

// ✅ With narrowing
function add(a: string | number, b: string | number): string | number {
  if (typeof a === "number" && typeof b === "number") {
    return a + b; // Numeric addition
  } else {
    return String(a) + String(b); // String concatenation
  }
}

console.log(add(5, 3)); // 8
console.log(add("5", "3")); // "53"
console.log(add(5, "3")); // "53"
```

### Example 2: Null Check

```typescript
interface User {
  name: string;
  email: string | null;
}

function getEmail(user: User): string {
  // ❌ Can't do this - email might be null
  // return user.email.toLowerCase();

  // ✅ Check for null first
  if (user.email === null) {
    return "Email not provided";
  }
  return user.email.toLowerCase();

  // ✅ Or use optional chaining
  // return user.email?.toLowerCase() ?? "Email not provided";
}
```

### Example 3: Type Guard Function

```typescript
// Define a type guard
interface Admin {
  name: string;
  role: "admin";
  permissions: string[];
}

interface User {
  name: string;
  role: "user";
  email: string;
}

type Person = Admin | User;

// Type guard function
function isAdmin(person: Person): person is Admin {
  return person.role === "admin";
}

function showPrivileges(person: Person) {
  if (isAdmin(person)) {
    // TypeScript knows person is Admin here
    console.log("Permissions:", person.permissions);
  } else {
    // TypeScript knows person is User here
    console.log("Email:", person.email);
  }
}
```

### Example 4: Array Check

```typescript
type Value = string | string[];

function process(value: Value): void {
  if (Array.isArray(value)) {
    // It's an array
    console.log("Array length:", value.length);
    value.forEach((item) => console.log(item));
  } else {
    // It's a string
    console.log("String:", value);
  }
}

process("hello"); // String
process(["hello", "world"]); // Array
```

### Example 5: Complex Narrowing

```typescript
type Data =
  | { type: "user"; user: { id: number; name: string } }
  | { type: "error"; error: { code: number; message: string } };

function processData(data: Data): string {
  if (data.type === "user") {
    return `User: ${data.user.name} (ID: ${data.user.id})`;
  } else if (data.type === "error") {
    return `Error ${data.error.code}: ${data.error.message}`;
  }
}

// Usage:
processData({
  type: "user",
  user: { id: 1, name: "Alice" },
});

processData({
  type: "error",
  error: { code: 404, message: "Not found" },
});
```

## Common Mistakes

### Mistake 1: Forgetting to Narrow

```typescript
// ❌ Using string method on union type
function uppercase(value: string | number) {
  return value.toUpperCase(); // ❌ ERROR: number doesn't have toUpperCase
}

// ✅ Narrow first
function uppercase(value: string | number): string {
  if (typeof value === "string") {
    return value.toUpperCase();
  } else {
    return String(value);
  }
}
```

### Mistake 2: Not Handling All Cases

```typescript
// ❌ Incomplete narrowing
function process(value: string | number | boolean) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "number") {
    console.log(value.toFixed());
  }
  // What about boolean?
}

// ✅ Handle all types
function process(value: string | number | boolean): void {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "number") {
    console.log(value.toFixed());
  } else {
    console.log(value);
  }
}
```

### Mistake 3: Wrong Type Guard

```typescript
// ❌ `typeof null` is "object"!
function process(value: string | null) {
  if (typeof value === "object") {
    // This is false even when value is null!
    console.log(value.length);
  }
}

// ✅ Check for null explicitly
function process(value: string | null) {
  if (value !== null) {
    console.log(value.length);
  }
}
```

### Mistake 4: Assuming Narrowing After Other Checks

```typescript
// ❌ Narrowing is lost
let value: string | number = "hello";

if (typeof value === "string") {
  value = 5; // Changed to number
}

// Now you can't use string methods without checking again
// value.toUpperCase();  // ❌ ERROR: could be number
```

## Practice Tasks

1. **typeof Guard**
   - Create a function that takes string | number
   - Use typeof to narrow and handle each type

2. **Null Checking**
   - Create a function that takes string | null
   - Check before using string methods

3. **instanceof**
   - Create two classes
   - Write a function that takes a union of both
   - Use instanceof to narrow

4. **Discriminated Union**
   - Create a discriminated union with a "type" property
   - Use the type to narrow

5. **Type Guard Function**
   - Create a custom type guard function
   - Use it to narrow a union type

## Mini Project: Response Handler

Create a handler for different API responses:

```typescript
type ApiResponse =
  | { status: 200; data: { user: string; age: number } }
  | { status: 201; data: { id: number; created: boolean } }
  | { status: 400; error: string }
  | { status: 500; error: string; trace?: string };

function handleResponse(response: ApiResponse): string {
  if (response.status === 200) {
    return `User ${response.data.user} is ${response.data.age} years old`;
  } else if (response.status === 201) {
    return `Created with ID ${response.data.id}`;
  } else if (response.status === 400) {
    return `Bad Request: ${response.error}`;
  } else if (response.status === 500) {
    const trace = response.trace ? ` (Trace: ${response.trace})` : "";
    return `Server Error: ${response.error}${trace}`;
  }
}

// Usage:
console.log(handleResponse({ status: 200, data: { user: "Alice", age: 30 } }));
console.log(handleResponse({ status: 400, error: "Invalid input" }));
```

## Official TypeScript Docs

- **Type Guards and Differentiating Types**: https://www.typescriptlang.org/docs/handbook/2/narrowing.html
- **typeof Type Guards**: https://www.typescriptlang.org/docs/handbook/2/narrowing.html#typeof-type-guards
- **Truthiness Narrowing**: https://www.typescriptlang.org/docs/handbook/2/narrowing.html#truthiness-narrowing

## Previous Topic

← [Unions and Literals](../07-unions-and-literals/README.md)

## Next Topic

→ [Generics](../09-generics/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
