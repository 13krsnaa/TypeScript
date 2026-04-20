# Introduction to TypeScript

## Why This Topic Matters

TypeScript is the foundation of modern JavaScript development. It catches bugs _before_ your code runs by adding static types. For full-stack developers, this means:

- **Frontend**: Catch component prop bugs during development
- **Backend**: Ensure API responses match expected types
- **Full-Stack**: Share types between frontend and backend for consistency

Without TypeScript, you'll spend hours debugging runtime errors. With TypeScript, many errors are caught during development.

## What is TypeScript?

TypeScript is a **superset of JavaScript** that adds static typing. Think of it as "JavaScript with a type safety layer."

```
JavaScript Code
       ↓
   + Types
       ↓
   TypeScript Code
       ↓
   Compiled to JavaScript
       ↓
   Runs in Browser/Node.js
```

## Core Concept

TypeScript allows you to specify what type of data a variable should hold:

```typescript
// JavaScript (no type checking)
let age = "twenty-five"; // Works, but probably a bug!
let name = 25; // Works, but probably a bug!

// TypeScript (with type checking)
let age: number = "twenty-five"; // ❌ ERROR: Type 'string' is not assignable to type 'number'
let name: string = 25; // ❌ ERROR: Type 'number' is not assignable to type 'string'

let age: number = 25; // ✅ Correct
let name: string = "John"; // ✅ Correct
```

## Key Characteristics

| Feature               | Description                                   |
| --------------------- | --------------------------------------------- |
| **Static Typing**     | Declare types before code runs                |
| **Compiles to JS**    | TypeScript → JavaScript → Browser/Node        |
| **Gradual Adoption**  | Add TypeScript gradually to existing projects |
| **Zero Runtime Cost** | Types are removed during compilation          |
| **IDE Support**       | Better autocomplete and error detection       |

## Syntax

The basic syntax is simple: add `: TypeName` after variable declarations.

```typescript
// Basic variable types
let count: number = 42;
let name: string = "Alice";
let isActive: boolean = true;
let nothing: null = null;
let notDefined: undefined = undefined;

// Function with types
function greet(name: string): string {
  return `Hello, ${name}!`;
}

// Function with multiple parameters
function add(a: number, b: number): number {
  return a + b;
}

// Function that returns nothing
function logMessage(message: string): void {
  console.log(message);
}
```

## Examples

### Example 1: Basic Type Safety

```typescript
// ❌ BAD: No types (JavaScript)
function calculateTotal(price, quantity) {
  return price * quantity;
}

// This works but is wrong:
calculateTotal("$100", "5"); // Returns "$100$100$100$100$100"

// ✅ GOOD: With TypeScript types
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}

// ❌ TypeScript catches this error:
calculateTotal("$100", "5"); // ERROR: Argument of type 'string' is not assignable to parameter of type 'number'

// ✅ Correct usage:
calculateTotal(100, 5); // Returns 500
```

### Example 2: Real-World API Response

```typescript
// ❌ Without TypeScript - Runtime error
async function getUserData(userId) {
  const response = await fetch(`/api/users/${userId}`);
  const user = await response.json();
  console.log(user.name); // What if 'name' doesn't exist?
  console.log(user.email); // What if this field is different?
}

// ✅ With TypeScript - Compile-time safety
interface User {
  id: number;
  name: string;
  email: string;
  age?: number; // optional field
}

async function getUserData(userId: number): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  const user: User = await response.json();
  console.log(user.name); // ✅ TypeScript knows this exists
  console.log(user.email); // ✅ TypeScript knows this exists
  // console.log(user.phone);  // ❌ ERROR: 'phone' does not exist on type 'User'
}
```

### Example 3: Component Props (React)

```typescript
// ❌ React without TypeScript
function UserCard(props) {
  return <div>Name: {props.name}</div>;
}

// How are you using it?
<UserCard />  // Forgot 'name' prop - no error until runtime
<UserCard name={123} />  // Wrong type - no error until runtime

// ✅ React with TypeScript
interface UserCardProps {
  name: string;
  age: number;
}

function UserCard({ name, age }: UserCardProps) {
  return <div>Name: {name}, Age: {age}</div>;
}

// Usage:
<UserCard />  // ❌ ERROR: Missing required props 'name' and 'age'
<UserCard name={123} age={30} />  // ❌ ERROR: 'name' should be string, not number
<UserCard name="John" age={30} />  // ✅ Correct
```

## Common Mistakes

### Mistake 1: Over-Typing Simple Code

```typescript
// ❌ Unnecessary - TypeScript can infer the type
const age: number = 25;

// ✅ Better - Let TypeScript infer
const age = 25;
```

### Mistake 2: Using `any` Type (TypeScript's Escape Hatch)

```typescript
// ❌ BAD - Defeats the purpose of TypeScript
let user: any = fetchUser();
console.log(user.name.toUpperCase()); // No error, but could fail at runtime
console.log(user.nonExistentProperty); // No error, but will be undefined

// ✅ GOOD - Explicit type
interface User {
  name: string;
}

let user: User = fetchUser();
console.log(user.name.toUpperCase()); // ✅ Safe
// console.log(user.nonExistentProperty);  // ❌ ERROR at compile-time
```

### Mistake 3: Ignoring Null/Undefined

```typescript
// ❌ Dangerous - user could be null or undefined
function getUsername(user) {
  return user.name; // Could crash if user is null
}

// ✅ Handle it explicitly
function getUsername(user: User | null): string {
  if (user === null) {
    return "Guest";
  }
  return user.name;
}

// ✅ Or use optional chaining
function getUsername(user: User | null): string {
  return user?.name ?? "Guest";
}
```

## Practice Tasks

1. **Convert JavaScript to TypeScript**

   ```javascript
   // Convert this to TypeScript with proper types
   function addNumbers(a, b) {
     return a + b;
   }

   const result = addNumbers(5, 10);
   ```

2. **Create a User Type**
   - Create a type for a user with `id` (number), `name` (string), and `email` (string)
   - Write a function that takes a User and returns the formatted email

3. **Fix the Type Error**

   ```typescript
   let count: number = "5"; // Fix this error
   let isReady: boolean = 1; // Fix this error
   ```

4. **Create a Product Interface**
   - Create an interface for a product: `id`, `name`, `price`, `inStock` (boolean)
   - Write a function to format the product info

5. **Handle Null Values**
   - Write a function that takes a `string | null` and returns the length, or 0 if null

## Mini Project: Simple User Registration

Create a user registration system with proper types:

```typescript
// Define types
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Simulate a database
const users: User[] = [];

// Function to add a user
function registerUser(name: string, email: string, age: number): User {
  const user: User = {
    id: users.length + 1,
    name,
    email,
    age,
  };
  users.push(user);
  return user;
}

// Function to find a user
function findUserByEmail(email: string): User | undefined {
  return users.find((user) => user.email === email);
}

// Usage
const newUser = registerUser("Alice", "alice@example.com", 28);
console.log(newUser);

const found = findUserByEmail("alice@example.com");
console.log(found);
```

## Official TypeScript Docs

- **Handbook Home**: https://www.typescriptlang.org/docs/handbook/
- **What is TypeScript**: https://www.typescriptlang.org/docs/handbook/2/basic-types.html
- **The TypeScript Playground**: https://www.typescriptlang.org/play/ (Practice here!)

## Next Topic

→ [Setup and Configuration](../02-setup-and-configuration/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
