# Error Handling

## Why This Topic Matters

Robust applications handle errors gracefully. Proper error handling ensures:

- **Stable applications** - Don't crash on unexpected input
- **Good user experience** - Clear error messages
- **Debugging easier** - Know what went wrong
- **Type safety** - Know what errors can occur
- **Production reliability** - Catch and log errors

Poor error handling is a sign of amateur code.

## Core Concept

Errors are unexpected situations that need handling:

```typescript
// ❌ Code that can crash
function divide(a: number, b: number): number {
  return a / b; // What if b is 0?
}

// ✅ Handle the error
function divide(a: number, b: number): number | Error {
  if (b === 0) {
    throw new Error("Division by zero");
  }
  return a / b;
}

// ✅ Or return null/optional
function divide(a: number, b: number): number | null {
  if (b === 0) {
    return null;
  }
  return a / b;
}
```

## Error Types

### Built-in Error Classes

```typescript
// Error - generic error
throw new Error("Something went wrong");

// TypeError - wrong type
throw new TypeError("Expected string, got number");

// RangeError - value out of range
throw new RangeError("Array index out of bounds");

// SyntaxError - syntax issue
throw new SyntaxError("Invalid JSON");

// Custom error class
class ValidationError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "ValidationError";
  }
}
```

## Syntax

### Try/Catch/Finally

```typescript
try {
  // Code that might throw
  throw new Error("Something failed");
} catch (error) {
  // Handle the error
  console.log("Error occurred:", error);
} finally {
  // Runs regardless of success/failure
  console.log("Cleanup");
}
```

### Custom Errors

```typescript
class ValidationError extends Error {
  constructor(
    public field: string,
    message: string,
  ) {
    super(message);
    this.name = "ValidationError";
  }
}

try {
  throw new ValidationError("email", "Invalid email format");
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`Field ${error.field}: ${error.message}`);
  }
}
```

### Function with Error Return

```typescript
type Result<T> = { success: true; data: T } | { success: false; error: string };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { success: false, error: "Division by zero" };
  }
  return { success: true, data: a / b };
}

const result = divide(10, 2);
if (result.success) {
  console.log(result.data);
} else {
  console.log(result.error);
}
```

## Examples

### Example 1: Basic Try/Catch

```typescript
function parseJSON(text: string): any {
  try {
    const data = JSON.parse(text);
    return data;
  } catch (error) {
    console.log("Invalid JSON:", text);
    return null;
  }
}

console.log(parseJSON('{"name":"Alice"}')); // { name: "Alice" }
console.log(parseJSON("not valid json")); // null
```

### Example 2: Custom Error Class

```typescript
class HttpError extends Error {
  constructor(
    public statusCode: number,
    public statusMessage: string,
    message: string,
  ) {
    super(message);
    this.name = "HttpError";
  }
}

async function fetchData(url: string): Promise<any> {
  try {
    const response = await fetch(url);

    if (!response.ok) {
      throw new HttpError(
        response.status,
        response.statusText,
        `Failed to fetch ${url}`,
      );
    }

    return response.json();
  } catch (error) {
    if (error instanceof HttpError) {
      console.log(`HTTP ${error.statusCode}: ${error.statusMessage}`);
    } else {
      console.log("Unknown error:", error);
    }
    throw error;
  }
}
```

### Example 3: Validation Error Handling

```typescript
class ValidationError extends Error {
  public errors: Record<string, string> = {};

  constructor(message: string) {
    super(message);
    this.name = "ValidationError";
  }

  addError(field: string, message: string): void {
    this.errors[field] = message;
  }
}

function validateUser(data: any): void {
  const errors = new ValidationError("User validation failed");

  if (!data.email || !data.email.includes("@")) {
    errors.addError("email", "Invalid email");
  }

  if (!data.age || data.age < 18) {
    errors.addError("age", "Must be 18 or older");
  }

  if (Object.keys(errors.errors).length > 0) {
    throw errors;
  }
}

// Usage:
try {
  validateUser({ email: "invalid", age: 16 });
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Validation failed:");
    console.log(error.errors);
  }
}
```

### Example 4: Async Error Handling

```typescript
interface User {
  id: number;
  name: string;
}

async function fetchUser(id: number): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    const user: User = await response.json();
    return user;
  } catch (error) {
    if (error instanceof TypeError) {
      console.log("Network error:", error.message);
    } else if (error instanceof Error) {
      console.log("Error:", error.message);
    }
    throw error;
  }
}
```

### Example 5: Result Type Pattern

```typescript
// Instead of throwing, return a Result type
type Result<T, E = string> = { ok: true; value: T } | { ok: false; error: E };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { ok: false, error: "Division by zero" };
  }
  return { ok: true, value: a / b };
}

function handleResult() {
  const result = divide(10, 2);

  if (result.ok) {
    console.log("Result:", result.value);
  } else {
    console.log("Error:", result.error);
  }
}

// Chaining results
function chain<T, E>(
  result: Result<T, E>,
  fn: (value: T) => Result<number, E>,
): Result<number, E> {
  if (!result.ok) {
    return result;
  }
  return fn(result.value);
}
```

## Common Mistakes

### Mistake 1: Catching Generic `Error`

```typescript
// ❌ Can't tell what kind of error
try {
  throw new ValidationError("email", "Invalid");
} catch (error) {
  // error is 'unknown' - no type info
  console.log(error);
}

// ✅ Check the error type
try {
  throw new ValidationError("email", "Invalid");
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`Field ${error.field}: ${error.message}`);
  } else if (error instanceof Error) {
    console.log(error.message);
  } else {
    console.log("Unknown error");
  }
}
```

### Mistake 2: Ignoring Promise Rejections

```typescript
// ❌ Unhandled rejection
async function process() {
  await fetchData(); // What if it fails?
}

// ✅ Handle it
async function process() {
  try {
    await fetchData();
  } catch (error) {
    console.log("Error:", error);
  }
}
```

### Mistake 3: Not Providing Error Context

```typescript
// ❌ Generic error
function process(data: any) {
  if (!data.id) {
    throw new Error("Error"); // What's wrong?
  }
}

// ✅ Descriptive error
function process(data: any) {
  if (!data.id) {
    throw new Error("User data must have an 'id' field");
  }
}
```

### Mistake 4: Silent Failures

```typescript
// ❌ Error is silently ignored
async function fetchData() {
  await fetch("/api/data").catch(() => {
    // Error is swallowed
  });
}

// ✅ Log or handle the error
async function fetchData() {
  try {
    await fetch("/api/data");
  } catch (error) {
    console.log("Failed to fetch:", error);
    // Rethrow or handle appropriately
  }
}
```

## Practice Tasks

1. **Try/Catch**
   - Write a function that might throw
   - Use try/catch to handle it

2. **Custom Error Class**
   - Create a custom error class
   - Throw and catch it

3. **Validation Error**
   - Create validation error with multiple fields
   - Check and display errors

4. **Async Error**
   - Create an async function that might fail
   - Handle the error properly

5. **Result Type**
   - Implement the Result pattern
   - Use it instead of throwing

## Mini Project: Form Validator

Create a form validator with comprehensive error handling:

```typescript
interface FormData {
  name: string;
  email: string;
  age: number;
  password: string;
}

interface ValidationError {
  field: string;
  message: string;
}

class FormValidator {
  validate(data: any): ValidationError[] {
    const errors: ValidationError[] = [];

    // Validate name
    if (!data.name || data.name.trim().length < 2) {
      errors.push({
        field: "name",
        message: "Name must be at least 2 characters",
      });
    }

    // Validate email
    if (!data.email || !this.isValidEmail(data.email)) {
      errors.push({ field: "email", message: "Invalid email format" });
    }

    // Validate age
    if (!data.age || data.age < 18 || data.age > 120) {
      errors.push({ field: "age", message: "Age must be between 18 and 120" });
    }

    // Validate password
    if (!data.password || data.password.length < 8) {
      errors.push({
        field: "password",
        message: "Password must be at least 8 characters",
      });
    }

    return errors;
  }

  private isValidEmail(email: string): boolean {
    return /^[^@]+@[^@]+\.[^@]+$/.test(email);
  }
}

// Usage:
const validator = new FormValidator();
const errors = validator.validate({
  name: "A",
  email: "invalid",
  age: 15,
  password: "short",
});

if (errors.length > 0) {
  errors.forEach((error) => {
    console.log(`${error.field}: ${error.message}`);
  });
} else {
  console.log("Validation passed!");
}
```

## Official TypeScript Docs

- **Error Handling**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling
- **Custom Errors**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error

## Previous Topic

← [Async and Promises](../12-async-and-promises/README.md)

## Next Topic

→ [Utility Types](../14-utility-types/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
