# Basic Types

## Why This Topic Matters

Types are the foundation of TypeScript. Understanding basic types is essential because:

- Every variable has a type
- Types prevent invalid operations (e.g., calling `.toUpperCase()` on a number)
- Types provide IDE autocomplete
- 90% of TypeScript code uses these basic types

Master these, and everything else becomes easier.

## Core Concept

TypeScript has **basic types** that represent the simplest values:

```
Primitive Types:
├── number      → 5, 3.14, -10
├── string      → "hello", 'world', `template`
├── boolean     → true, false
├── null        → null
├── undefined   → undefined
├── symbol      → Symbol("unique")
└── bigint      → 9007199254740991n

Special Types:
├── any         → Anything (avoid this!)
└── never       → Can't reach this code
```

## Syntax

```typescript
// String type
let firstName: string = "John";
let lastName: string = "Doe";
let fullName: string = `${firstName} ${lastName}`;

// Number type
let age: number = 25;
let price: number = 9.99;
let negative: number = -50;

// Boolean type
let isActive: boolean = true;
let isDeleted: boolean = false;

// Null and Undefined
let nothing: null = null;
let notSet: undefined = undefined;

// Symbol (rare, used for unique identifiers)
let id: symbol = Symbol("id");

// BigInt (for very large numbers)
let huge: bigint = 9007199254740991n;

// Any (avoid!)
let mystery: any = "could be anything"; // ❌ Defeats type safety
```

## Type Inference

TypeScript can **infer** types automatically:

```typescript
// TypeScript infers the type without explicit annotation
let name = "Alice"; // inferred as string
let count = 42; // inferred as number
let isActive = true; // inferred as boolean

// You can still check what type was inferred
const x = "hello"; // x is inferred as 'string'
```

## Examples

### Example 1: String Operations

```typescript
let greeting: string = "Hello";

// ✅ String methods work
console.log(greeting.toUpperCase()); // "HELLO"
console.log(greeting.length); // 5
console.log(greeting.charAt(0)); // "H"

// ❌ Number methods don't work on strings
// greeting.toFixed(2);  // ERROR: Property 'toFixed' does not exist on type 'string'

// Template literals
let name: string = "Bob";
let message: string = `Welcome, ${name}!`; // "Welcome, Bob!"
```

### Example 2: Number Operations

```typescript
let age: number = 25;
let price: number = 19.99;

// ✅ Number methods work
console.log(price.toFixed(0)); // "20"
console.log(age.toString()); // "25"

// ❌ String methods don't work on numbers
// age.toUpperCase();  // ERROR

// Math operations
let total: number = price * 2;
let discount: number = total * 0.1;
```

### Example 3: Boolean Logic

```typescript
let isLoggedIn: boolean = true;
let hasPermission: boolean = false;

// ✅ Boolean logic
if (isLoggedIn && hasPermission) {
  console.log("Access granted");
}

// ❌ Numbers are not booleans
let count: number = 1;
// if (count) {  // ❌ ERROR in strict mode - should use explicit boolean
//   console.log("Count is truthy");
// }

// ✅ Explicit boolean
let shouldContinue: boolean = count > 0;
if (shouldContinue) {
  console.log("Count is positive");
}
```

### Example 4: The `any` Type (Use Sparingly!)

```typescript
// ❌ BAD: Using any defeats type safety
let data: any = fetchSomething();
console.log(data.name.toUpperCase()); // Could crash if name doesn't exist
console.log(data.nonExistent); // Returns undefined, no error

// ✅ GOOD: Use proper types
interface Data {
  name: string;
}
let data: Data = fetchSomething();
// console.log(data.nonExistent);  // ❌ ERROR: Property does not exist

// When you MUST use any, document why
// @ts-ignore - Third-party library returns untyped data
let response: any = thirdPartyFunction();
```

### Example 5: Null vs Undefined

```typescript
// ❌ Without type checking - both are treated the same
let x = null;
let y = undefined;
console.log(x === y); // true (both are falsy)

// ✅ With TypeScript - you can be explicit
let intentionallyEmpty: null = null; // Intentionally empty
let notYetSet: undefined = undefined; // Not initialized

// ✅ In strict mode, handle them explicitly
interface User {
  name: string;
  age: number | null; // Can be a number or null
}

const user: User = {
  name: "Alice",
  age: null, // Explicitly null is OK
};

// You MUST check before using
if (user.age !== null) {
  console.log(user.age + 5); // Safe - we checked it's not null
}
```

## Common Mistakes

### Mistake 1: Mixing Number and String

```typescript
// ❌ Dangerous JavaScript behavior
let result = "5" + 3; // "53" (string concatenation, not math!)
let result2 = "5" - 3; // 2 (JavaScript coerces to number)

// ✅ Be explicit about types
let numResult: number = 5 + 3; // 8
let strResult: string = "5" + "3"; // "53"
let strNum: string = String(5) + "3"; // "53"
let finalNum: number = parseInt("5") + 3; // 8
```

### Mistake 2: Assuming `null` and `undefined` are the same

```typescript
// ❌ Treating them the same
let x: null = undefined; // ❌ ERROR: Type 'undefined' is not assignable to type 'null'
let y: undefined = null; // ❌ ERROR: Type 'null' is not assignable to type 'undefined'

// ✅ Use union type if you need both
let maybe: null | undefined = null; // Can be either
let value: string | null = "hello"; // Can be string or null
```

### Mistake 3: Forgetting Type Annotations on Constants

```typescript
// ❌ Without annotation, TypeScript might infer too specifically
const API_URL = "https://api.example.com"; // Inferred as literal type
const MAX_RETRIES = 3; // Inferred as literal 3, not number

// ✅ Explicitly annotate constants
const API_URL: string = "https://api.example.com";
const MAX_RETRIES: number = 3;
```

### Mistake 4: Not Handling Type Coercion

```typescript
// ❌ JavaScript coercion can surprise you
const value: any = "5";
console.log(value === 5); // false
console.log(value == 5); // true (loose equality)
console.log(parseInt(value) === 5); // true

// ✅ Be aware of type differences
let str: string = "5";
let num: number = 5;
console.log(str === num); // false - different types
console.log(Number(str) === num); // true - after conversion
```

## Practice Tasks

1. **Basic Type Assignments**

   ```typescript
   // Create variables with these types:
   // - name (string)
   // - age (number)
   // - isStudent (boolean)
   // - score (number, decimal)
   ```

2. **Type Inference**
   - Create a variable without explicit type annotation
   - Hover over it in VS Code to see what type was inferred
   - Try with string, number, and boolean

3. **String Manipulation**
   - Create a string variable
   - Use `.length`, `.toUpperCase()`, `.toLowerCase()`, `.substring()`
   - Try invalid operations and see the errors

4. **Number Operations**
   - Create a number variable
   - Use `.toFixed()`, `.toString()`, `Math.floor()`, `Math.ceil()`
   - Try string operations on it (should error)

5. **Null vs Undefined**
   - Create variables with `null` and `undefined` types
   - Create a union type that accepts both
   - Check how TypeScript handles them differently

6. **Array of Numbers**
   - Create an array of numbers
   - Try adding a string to it (should error)

## Mini Project: Temperature Converter

Create a simple temperature converter with proper types:

```typescript
// Types
type Celsius = number;
type Fahrenheit = number;

// Convert Celsius to Fahrenheit
function celsiusToFahrenheit(celsius: Celsius): Fahrenheit {
  return (celsius * 9) / 5 + 32;
}

// Convert Fahrenheit to Celsius
function fahrenheitToCelsius(fahrenheit: Fahrenheit): Celsius {
  return ((fahrenheit - 32) * 5) / 9;
}

// Usage
const waterFreezingC: Celsius = 0;
const waterFreezingF: Fahrenheit = celsiusToFahrenheit(waterFreezingC);

console.log(`${waterFreezingC}°C = ${waterFreezingF}°F`);
console.log(`98.6°F = ${fahrenheitToCelsius(98.6).toFixed(1)}°C`);
```

## Official TypeScript Docs

- **Basic Types**: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html
- **Type Inference**: https://www.typescriptlang.org/docs/handbook/type-inference.html
- **Primitive Types**: https://www.typescriptlang.org/play/?#handbook/2/everyday-types

## Previous Topic

← [Setup and Configuration](../02-setup-and-configuration/README.md)

## Next Topic

→ [Functions](../04-functions/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
