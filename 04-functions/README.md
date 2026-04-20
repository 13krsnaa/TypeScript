# Functions

## Why This Topic Matters

Functions are where type safety becomes powerful. Proper function typing ensures:

- **Callers know what parameters to pass** - IDE autocomplete helps
- **Functions guarantee return types** - No surprises
- **Refactoring is safe** - Change a return type, and all usages error out
- **API contracts are clear** - Documentations in types

Without function types, you'll get mysterious runtime errors from mismatched parameters.

## Core Concept

TypeScript functions have **three type annotations**:

```
function name(param1: ParamType, param2: ParamType): ReturnType {
  return something;
}
```

1. **Parameter Types** - What the function receives
2. **Return Type** - What the function returns
3. **Optional Parameters** - Parameters you don't have to pass

## Syntax

```typescript
// Basic function with types
function add(a: number, b: number): number {
  return a + b;
}

// Function with different parameter types
function greet(name: string, age: number): string {
  return `Hello ${name}, you are ${age} years old`;
}

// Function that returns nothing (void)
function logMessage(message: string): void {
  console.log(message);
}

// Function with optional parameter
function createUser(name: string, email?: string): void {
  console.log(`User: ${name}${email ? `, email: ${email}` : ""}`);
}

// Function with default parameter
function multiply(a: number, b: number = 2): number {
  return a * b;
}

// Arrow functions
const divide = (a: number, b: number): number => a / b;

// Function with array parameter
function sum(numbers: number[]): number {
  return numbers.reduce((acc, num) => acc + num, 0);
}
```

## Examples

### Example 1: Basic Function

```typescript
// ❌ Without types
function calculateDiscount(price, percentage) {
  return (price * percentage) / 100;
}

// Bugs:
calculateDiscount("100", "50"); // Returns NaN
calculateDiscount(100); // Returns NaN (missing parameter)

// ✅ With types
function calculateDiscount(price: number, percentage: number): number {
  return (price * percentage) / 100;
}

// TypeScript catches errors:
// calculateDiscount("100", "50");  // ❌ ERROR: Argument of type 'string' is not assignable
// calculateDiscount(100);          // ❌ ERROR: Expected 2 arguments, got 1

// Correct usage:
const discount = calculateDiscount(100, 50); // 50
```

### Example 2: Optional and Default Parameters

```typescript
// User registration - email is optional
function registerUser(
  name: string,
  email?: string, // Optional parameter (?)
  role: string = "user", // Default parameter (= "user")
): object {
  return {
    name,
    email: email || "not provided",
    role,
  };
}

// Usage:
registerUser("Alice"); // email and role use defaults
registerUser("Bob", "bob@example.com"); // role uses default
registerUser("Charlie", "charlie@example.com", "admin"); // All explicit

// Order matters: optional parameters come before parameters with defaults
// ❌ function bad(required: string, optional?: string, defaulted: string = "value") {}
// ✅ function good(required: string, optional?: string) {}
// ✅ function good2(required: string, defaulted: string = "value") {}
```

### Example 3: Array Parameter

```typescript
// Calculate average of array
function average(numbers: number[]): number {
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((acc, num) => acc + num, 0);
  return sum / numbers.length;
}

// Usage:
average([10, 20, 30]); // 20
average([]); // 0

// ❌ TypeScript catches wrong types
// average(["10", "20"]);     // ERROR: Type 'string[]' is not assignable to type 'number[]'
// average("10,20,30");       // ERROR: Type 'string' is not assignable to type 'number[]'
```

### Example 4: Return Type Matters

```typescript
// ❌ Without explicit return type - what does this return?
function processData(data: string) {
  if (data.length > 10) {
    return {
      valid: true,
      message: "Data is long enough",
    };
  }
  return false; // Bug! Sometimes returns object, sometimes boolean!
}

// ✅ With explicit return type - TypeScript catches inconsistency
function processData(
  data: string,
): { valid: boolean; message: string } | boolean {
  if (data.length > 10) {
    return {
      valid: true,
      message: "Data is long enough",
    };
  }
  return false;
}

// ✅ Better - fix the return type to be consistent
function processData(data: string): { valid: boolean; message: string } {
  if (data.length > 10) {
    return {
      valid: true,
      message: "Data is long enough",
    };
  }
  return {
    valid: false,
    message: "Data is too short",
  };
}
```

### Example 5: Function Types (Advanced)

```typescript
// Define a function type
type Transformer = (input: string) => string;

// Use it
const toUpperCase: Transformer = (input) => input.toUpperCase();
const reverse: Transformer = (input) => input.split("").reverse().join("");

// Function that takes a function parameter
function applyTransformation(text: string, transform: Transformer): string {
  return transform(text);
}

// Usage:
applyTransformation("hello", toUpperCase); // "HELLO"
applyTransformation("hello", reverse); // "olleh"
```

## Common Mistakes

### Mistake 1: Missing Return Type Annotation

```typescript
// ❌ Unclear what gets returned
function getData() {
  return {
    user: { id: 1, name: "Alice" },
    timestamp: new Date(),
  };
}

// ✅ Explicit return type
function getData(): { user: { id: number; name: string }; timestamp: Date } {
  return {
    user: { id: 1, name: "Alice" },
    timestamp: new Date(),
  };
}

// ✅ Or use an interface (better!)
interface DataResult {
  user: { id: number; name: string };
  timestamp: Date;
}

function getData(): DataResult {
  return {
    user: { id: 1, name: "Alice" },
    timestamp: new Date(),
  };
}
```

### Mistake 2: Forgetting Optional Parameter Order

```typescript
// ❌ Required parameter after optional - ERROR!
// function badOrder(optional?: string, required: string) {}

// ✅ Optional parameters must come last
function goodOrder(required: string, optional?: string) {}

// ✅ Or use union type
function workaround(param1: string | undefined, param2: string) {}
```

### Mistake 3: Using `any` in Function Parameters

```typescript
// ❌ Defeats type safety
function processUser(user: any) {
  console.log(user.name.toUpperCase()); // Could crash
}

// ✅ Be specific
interface User {
  name: string;
  age: number;
}

function processUser(user: User) {
  console.log(user.name.toUpperCase()); // Safe
}
```

### Mistake 4: Not Handling `null` or `undefined` Returns

```typescript
// ❌ Function might return null, but caller doesn't handle it
function findUser(id: number) {
  if (id > 0) {
    return { id, name: "User" };
  }
  return null;
}

const user = findUser(1);
console.log(user.name); // ❌ ERROR: Object is possibly 'null'

// ✅ Handle it
function findUser(id: number): { id: number; name: string } | null {
  if (id > 0) {
    return { id, name: "User" };
  }
  return null;
}

const user = findUser(1);
if (user) {
  console.log(user.name); // ✅ Safe now
}
```

## Practice Tasks

1. **Create Basic Functions**
   - Write a function that takes two strings and returns their concatenation
   - Write a function that takes a number and returns its square
   - Write a function that takes a boolean and returns its opposite

2. **Optional Parameters**
   - Create a function with required and optional parameters
   - Test calling it with and without optional parameters

3. **Default Parameters**
   - Create a function with default parameters
   - Verify the defaults work when not provided

4. **Array Operations**
   - Write a function that takes an array of numbers and returns the sum
   - Write a function that takes an array of strings and returns the longest one

5. **Complex Return Types**
   - Write a function that returns `{ success: boolean; message: string }`
   - Write a function that returns either an object or `null`

6. **Function Callbacks**
   - Write a function that takes a callback function as a parameter
   - Use it to filter an array

## Mini Project: Calculator

Create a simple calculator with proper function types:

```typescript
type Operation = (a: number, b: number) => number;

const add: Operation = (a, b) => a + b;
const subtract: Operation = (a, b) => a - b;
const multiply: Operation = (a, b) => a * b;
const divide: Operation = (a, b) => {
  if (b === 0) throw new Error("Division by zero");
  return a / b;
};

interface CalculatorResult {
  operation: string;
  a: number;
  b: number;
  result: number;
}

function calculate(
  a: number,
  b: number,
  operation: Operation,
  operationName: string,
): CalculatorResult {
  return {
    operation: operationName,
    a,
    b,
    result: operation(a, b),
  };
}

// Usage:
const result1 = calculate(10, 5, add, "add");
const result2 = calculate(10, 5, divide, "divide");

console.log(`${result1.a} + ${result1.b} = ${result1.result}`);
console.log(`${result2.a} / ${result2.b} = ${result2.result}`);
```

## Official TypeScript Docs

- **Functions**: https://www.typescriptlang.org/docs/handbook/2/functions.html
- **Parameter Types**: https://www.typescriptlang.org/docs/handbook/2/functions.html#parameter-type-annotations
- **Return Types**: https://www.typescriptlang.org/docs/handbook/2/functions.html#return-type-annotation

## Previous Topic

← [Basic Types](../03-basic-types/README.md)

## Next Topic

→ [Objects and Arrays](../05-objects-and-arrays/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
