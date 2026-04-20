# TypeScript Practice Exercises

Topic-by-topic coding exercises to reinforce learning.

---

## Basic Types (Topic 3)

### Exercise 1: Type Inference

```typescript
// Identify the types without explicit annotations
const age = 30;              // What type?
const name = "Alice";        // What type?
const isActive = true;       // What type?
const values = [1, 2, 3];    // What type?
const user = { id: 1, name: "Bob" };  // What type?

// Answer: number, string, boolean, number[], { id: number; name: string }
```

### Exercise 2: Type Annotations

```typescript
// Add type annotations
const getUserAge = (user) => {
  return user.age;
};

// Solution:
interface User { age: number; }
const getUserAge = (user: User): number => {
  return user.age;
};
```

### Exercise 3: Union Types

```typescript
// Create a function that accepts either string or number
function processValue(value) {
  // Function body
}

// Solution:
function processValue(value: string | number): void {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else {
    console.log(value.toFixed(2));
  }
}
```

---

## Functions (Topic 4)

### Exercise 1: Function Types

```typescript
// Type this function
const greet = (name, greeting) => {
  return `${greeting}, ${name}!`;
};

// Solution:
const greet = (name: string, greeting: string): string => {
  return `${greeting}, ${name}!`;
};
```

### Exercise 2: Optional Parameters

```typescript
// Make age optional with default value
function createUser(name: string, age) {
  return { name, age };
}

// Solution:
function createUser(name: string, age: number = 0): { name: string; age: number } {
  return { name, age };
}
```

### Exercise 3: Rest Parameters

```typescript
// Create a function that sums any number of arguments
function sum(num1, num2, ...rest) {
  return num1 + num2 + rest.reduce((a, b) => a + b, 0);
}

// Solution:
function sum(num1: number, num2: number, ...rest: number[]): number {
  return num1 + num2 + rest.reduce((a, b) => a + b, 0);
}
```

---

## Objects and Arrays (Topic 5)

### Exercise 1: Object Type Definition

```typescript
// Create a type for a book with id, title, author, and year
const book = {
  id: 1,
  title: "TypeScript Guide",
  author: "Alice",
  year: 2024
};

// Solution:
interface Book {
  id: number;
  title: string;
  author: string;
  year: number;
}
```

### Exercise 2: Array Types

```typescript
// Type these arrays
const numbers = [1, 2, 3];
const strings = ["a", "b", "c"];
const mixed = [1, "a", true];

// Solution:
const numbers: number[] = [1, 2, 3];
const strings: string[] = ["a", "b", "c"];
const mixed: (number | string | boolean)[] = [1, "a", true];
```

### Exercise 3: Tuple Types

```typescript
// Create a tuple type for (name, age, email)
const user = ["Alice", 30, "alice@example.com"];

// Solution:
type UserTuple = [string, number, string];
const user: UserTuple = ["Alice", 30, "alice@example.com"];
```

---

## Type Aliases and Interfaces (Topic 6)

### Exercise 1: Interface Definition

```typescript
// Create an interface for a Product
const product = {
  id: 1,
  name: "Laptop",
  price: 999,
  inStock: true,
  tags: ["electronics", "computers"]
};

// Solution:
interface Product {
  id: number;
  name: string;
  price: number;
  inStock: boolean;
  tags: string[];
}
```

### Exercise 2: Type vs Interface

```typescript
// When should you use type vs interface?
// Answer: Interface for objects, type for unions/primitives/literals

interface User { id: number; name: string; }  // Interface
type Status = "active" | "inactive";          // Type (union)
type ID = number | string;                    // Type (union)
```

### Exercise 3: Extending Types

```typescript
// Extend the User interface to create Admin
interface User {
  id: number;
  name: string;
}

// Solution:
interface Admin extends User {
  permissions: string[];
  role: "admin";
}
```

---

## Unions and Literals (Topic 7)

### Exercise 1: Discriminated Unions

```typescript
// Create a discriminated union for API responses
type SuccessResponse = { status: "success"; data: string };
type ErrorResponse = { status: "error"; message: string };
type Response = SuccessResponse | ErrorResponse;

// Solution - already correct!
```

### Exercise 2: Literal Types

```typescript
// Create a type for HTTP methods
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

function sendRequest(url: string, method: HttpMethod) {
  // Implementation
}
```

---

## Type Narrowing (Topic 8)

### Exercise 1: typeof Guard

```typescript
// Narrow type using typeof
function process(value: string | number) {
  if (typeof value === "string") {
    // What is value here?
  } else {
    // What is value here?
  }
}

// Solution: string in first block, number in second
```

### Exercise 2: instanceof Guard

```typescript
// Narrow using instanceof
class Dog {
  bark() { console.log("Woof!"); }
}
class Cat {
  meow() { console.log("Meow!"); }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark();
  } else {
    animal.meow();
  }
}
```

---

## Generics (Topic 9)

### Exercise 1: Generic Function

```typescript
// Make this function generic
function getFirst(arr) {
  return arr[0];
}

// Solution:
function getFirst<T>(arr: T[]): T {
  return arr[0];
}
```

### Exercise 2: Generic Interface

```typescript
// Create a generic Container interface
interface Container {
  value: any;
}

// Solution:
interface Container<T> {
  value: T;
}

const strContainer: Container<string> = { value: "hello" };
const numContainer: Container<number> = { value: 42 };
```

### Exercise 3: Generic Constraints

```typescript
// Create a function that only accepts objects with 'length' property
function getLength(obj) {
  return obj.length;
}

// Solution:
function getLength<T extends { length: number }>(obj: T): number {
  return obj.length;
}
```

---

## Classes and OOP (Topic 10)

### Exercise 1: Class Definition

```typescript
// Create a Person class with constructor
class Person {
  // Define private properties and constructor
}

// Solution:
class Person {
  private id: number;
  private name: string;

  constructor(id: number, name: string) {
    this.id = id;
    this.name = name;
  }
}
```

### Exercise 2: Inheritance

```typescript
// Create an Employee class that extends Person
class Employee extends Person {
  private salary: number;

  constructor(id: number, name: string, salary: number) {
    super(id, name);
    this.salary = salary;
  }
}
```

### Exercise 3: Access Modifiers

```typescript
// Fix the access modifiers
class User {
  id: number;           // Public (fine)
  password: string;     // Should be private!
  email: string;        // Could be protected
}

// Solution:
class User {
  public id: number;
  private password: string;
  protected email: string;
}
```

---

## Async and Promises (Topic 12)

### Exercise 1: Promise Type

```typescript
// Type this promise
const userPromise = new Promise((resolve) => {
  resolve({ id: 1, name: "Alice" });
});

// Solution:
interface User { id: number; name: string; }
const userPromise: Promise<User> = new Promise((resolve) => {
  resolve({ id: 1, name: "Alice" });
});
```

### Exercise 2: Async/Await

```typescript
// Type this async function
async function getUsers() {
  const response = await fetch("/api/users");
  return response.json();
}

// Solution:
async function getUsers(): Promise<User[]> {
  const response = await fetch("/api/users");
  return response.json();
}
```

---

## Utility Types (Topic 14)

### Exercise 1: Partial

```typescript
// Use Partial to make a type with all optional properties
interface User { id: number; name: string; email: string; }
type UserPreview = Partial<User>;

// Now UserPreview allows any combination of User properties
```

### Exercise 2: Pick and Omit

```typescript
// Create types using Pick and Omit
interface User { id: number; name: string; email: string; password: string; }

// Pick - select specific properties
type UserPublic = Pick<User, "id" | "name" | "email">;

// Omit - exclude specific properties
type UserWithoutPassword = Omit<User, "password">;
```

### Exercise 3: Record

```typescript
// Create a Record type for status counts
type StatusCount = Record<"pending" | "done" | "blocked", number>;

const counts: StatusCount = {
  pending: 5,
  done: 3,
  blocked: 1
};
```

---

## Real-World Scenarios

### Exercise 1: API Response Handler

```typescript
// Type an API response handler
interface ApiResponse<T> {
  status: "success" | "error";
  data?: T;
  error?: string;
}

async function handleResponse<T>(res: Response): Promise<ApiResponse<T>> {
  if (!res.ok) {
    return { status: "error", error: `HTTP ${res.status}` };
  }
  const data: T = await res.json();
  return { status: "success", data };
}
```

### Exercise 2: Form Validation

```typescript
// Type a form validation function
interface FormData {
  email: string;
  password: string;
  name: string;
}

function validateForm(data: Partial<FormData>): { valid: boolean; errors: string[] } {
  const errors: string[] = [];
  if (!data.email?.includes("@")) errors.push("Invalid email");
  if (!data.password || data.password.length < 8) errors.push("Password too short");
  return { valid: errors.length === 0, errors };
}
```

### Exercise 3: State Management

```typescript
// Type a reducer function
type Action = 
  | { type: "ADD"; payload: string }
  | { type: "REMOVE"; payload: number }
  | { type: "CLEAR" };

function reducer(state: string[], action: Action): string[] {
  switch (action.type) {
    case "ADD":
      return [...state, action.payload];
    case "REMOVE":
      return state.filter((_, i) => i !== action.payload);
    case "CLEAR":
      return [];
  }
}
```

---

## Solutions Guide

All exercises have solutions provided above. Work through each exercise:

1. Try to solve it first
2. Compare with the solution
3. Understand any differences
4. Refactor your solution if needed
5. Practice the pattern in new contexts

---

## Progressive Difficulty

**Beginner:** Basic Types, Functions, Objects/Arrays 
**Intermediate:** Type Aliases, Unions, Type Narrowing, Generics, Classes 
**Advanced:** Async, Utility Types, Real-World Scenarios 

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
