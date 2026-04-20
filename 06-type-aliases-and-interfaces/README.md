# Type Aliases and Interfaces

## Why This Topic Matters

As projects grow, you'll define many custom types. Type aliases and interfaces let you:

- **Reuse types** across your codebase
- **Document data structures** in your code
- **Create contracts** between functions and components
- **Enable teamwork** - everyone knows the expected shape of data

The difference between type aliases and interfaces is subtle but important.

## Core Concept

### Type Aliases

Use `type` keyword to create a new type name:

```typescript
type Name = string;
type Age = number;
type Email = string;

// Use them:
const name: Name = "Alice";
const age: Age = 25;
```

### Interfaces

Use `interface` keyword to define object shapes:

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}

const user: User = {
  name: "Alice",
  age: 25,
  email: "alice@example.com",
};
```

## Syntax

### Type Aliases

```typescript
// Simple type alias
type Status = string;

// Union type alias
type Result = "success" | "error" | "loading";

// Object type alias
type User = {
  id: number;
  name: string;
  email: string;
};

// Function type alias
type Callback = (data: string) => void;

// Complex type
type Response = {
  status: number;
  data: User[];
  error?: string;
};
```

### Interfaces

```typescript
// Basic interface
interface User {
  id: number;
  name: string;
  email: string;
}

// Interface with optional properties
interface User {
  id: number;
  name: string;
  email: string;
  phone?: string;
}

// Interface with readonly properties
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}

// Interface inheritance (extending)
interface Employee extends User {
  employeeId: string;
  department: string;
}

// Multiple inheritance
interface Developer extends Employee {
  languages: string[];
}
```

## Key Differences

| Feature              | Type Alias | Interface        |
| -------------------- | ---------- | ---------------- |
| Defines primitives   | ✅ Yes     | ❌ No            |
| Defines objects      | ✅ Yes     | ✅ Yes           |
| Unions               | ✅ Yes     | ❌ No            |
| Intersection         | ✅ Yes     | ✅ Yes (extends) |
| Declaration merging  | ❌ No      | ✅ Yes           |
| Better for functions | ✅ Yes     | ❌ No            |
| Better for objects   | 🤝 Both    | 🤝 Both          |

## Examples

### Example 1: Type Alias vs Interface for Objects

```typescript
// ❌ Type alias - works but less idiomatic for objects
type UserType = {
  id: number;
  name: string;
  email: string;
};

// ✅ Interface - more idiomatic for objects
interface UserInterface {
  id: number;
  name: string;
  email: string;
}

// Both work the same way:
const user1: UserType = { id: 1, name: "Alice", email: "alice@example.com" };
const user2: UserInterface = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};
```

### Example 2: Type Aliases for Unions

```typescript
// ✅ Type aliases excel at unions
type Status = "active" | "inactive" | "pending";
type Result = string | number | boolean;
type Response = { data: User[] } | { error: string };

// ❌ Interfaces can't do this
// interface Status = "active" | "inactive";  // ERROR!

// Function using union type
function updateStatus(status: Status): void {
  console.log(`Status: ${status}`);
}

updateStatus("active"); // ✅
// updateStatus("unknown");  // ❌ ERROR
```

### Example 3: Interface Inheritance

```typescript
// Base interface
interface Animal {
  name: string;
  age: number;
}

// Extending an interface
interface Dog extends Animal {
  breed: string;
}

// Multiple inheritance
interface Employee extends Animal {
  employeeId: string;
  salary: number;
}

// Create a dog
const dog: Dog = {
  name: "Buddy",
  age: 5,
  breed: "Golden Retriever",
};

// Type aliases can also extend (using &)
type DogType = Animal & { breed: string };
```

### Example 4: Type Aliases for Functions

```typescript
// ✅ Type aliases are great for function signatures
type Callback = (error: Error | null, data: any) => void;
type Validator = (input: string) => boolean;
type AsyncHandler = (id: number) => Promise<User>;

// Use them in functions
function processUser(userId: number, onComplete: Callback): void {
  try {
    const user = fetchUser(userId);
    onComplete(null, user);
  } catch (error) {
    onComplete(error as Error, null);
  }
}

function validateEmail(email: string): boolean {
  return email.includes("@");
}

const validator: Validator = validateEmail;
```

### Example 5: Declaration Merging (Interfaces Only)

```typescript
// Define same interface twice - TypeScript merges them!
interface User {
  id: number;
  name: string;
}

interface User {
  email: string;
}

// Result is like:
// interface User {
//   id: number;
//   name: string;
//   email: string;
// }

const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};

// Type aliases don't support this:
// type UserType = { id: number };
// type UserType = { name: string };  // ❌ ERROR: Duplicate identifier
```

## Common Mistakes

### Mistake 1: Using `any` in Type Aliases

```typescript
// ❌ BAD
type Response = {
  status: any;
  data: any;
};

// ✅ GOOD
type Response = {
  status: number;
  data: any[] | null;
};
```

### Mistake 2: Mixing Type and Interface Styles Unnecessarily

```typescript
// ❌ Confusing - mixing styles
type UserType = {
  id: number;
  name: string;
};

interface ProductInterface {
  id: number;
  name: string;
}

// ✅ Pick one style and stick with it (interface for objects is conventional)
interface User {
  id: number;
  name: string;
}

interface Product {
  id: number;
  name: string;
}
```

### Mistake 3: Forgetting Required Properties

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// ❌ Missing email
// const user: User = {
//   id: 1,
//   name: "Alice"
// };

// ✅ Include all properties
const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};
```

### Mistake 4: Not Using Readonly When Needed

```typescript
// ❌ Accidentally mutable
interface Config {
  apiUrl: string;
  timeout: number;
}

const config: Config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

config.apiUrl = "https://hacker.com"; // Oops! Changed

// ✅ Make it readonly
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}

const config: Config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

// config.apiUrl = "https://hacker.com";  // ❌ ERROR
```

## Practice Tasks

1. **Create Type Aliases**
   - Create a `Status` type with allowed values
   - Create a `Callback` function type
   - Use them in functions

2. **Create Interfaces**
   - Create a `User` interface with required properties
   - Create a `Product` interface with optional properties

3. **Interface Inheritance**
   - Create a `Person` interface
   - Create an `Employee` interface extending `Person`
   - Create instances of both

4. **Union Types**
   - Create a `Response` type that can be success or error
   - Create a function that handles both cases

5. **Type Aliases for Complex Types**
   - Create a type for API responses
   - Create a type for error objects

## Mini Project: API Type Definitions

Create type definitions for a real-world API:

```typescript
// Define API response types
type HttpStatus = 200 | 201 | 400 | 401 | 404 | 500;

interface ApiResponse<T> {
  status: HttpStatus;
  data: T;
  message: string;
  timestamp: string;
}

interface User {
  id: number;
  name: string;
  email: string;
  created_at: string;
}

interface CreateUserRequest {
  name: string;
  email: string;
}

// Define API functions
type FetchUser = (id: number) => Promise<ApiResponse<User>>;
type CreateUser = (data: CreateUserRequest) => Promise<ApiResponse<User>>;

// Implement
const fetchUser: FetchUser = async (id) => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
};

const createUser: CreateUser = async (data) => {
  const response = await fetch("/api/users", {
    method: "POST",
    body: JSON.stringify(data),
  });
  return response.json();
};
```

## Official TypeScript Docs

- **Type Aliases**: https://www.typescriptlang.org/docs/handbook/2/types-from-types.html#type-aliases
- **Interfaces**: https://www.typescriptlang.org/docs/handbook/2/objects.html
- **Type vs Interface**: https://www.typescriptlang.org/docs/handbook/2/objects.html#differences-between-type-aliases-and-interfaces

## Previous Topic

← [Objects and Arrays](../05-objects-and-arrays/README.md)

## Next Topic

→ [Unions and Literals](../07-unions-and-literals/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
