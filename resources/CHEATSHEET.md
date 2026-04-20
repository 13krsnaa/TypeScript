# TypeScript Cheatsheet

Quick reference for TypeScript syntax and patterns.

## Basic Types

```typescript
let str: string = "hello";
let num: number = 42;
let bool: boolean = true;
let any_type: any = "anything";
let unknown_type: unknown = "value";
let nil: null = null;
let undef: undefined = undefined;
```

## Arrays and Tuples

```typescript
let arr: number[] = [1, 2, 3];
let arr2: Array<number> = [1, 2, 3];
let tuple: [string, number] = ["hello", 42];
let union: (string | number)[] = ["a", 1, "b"];
```

## Functions

```typescript
function add(a: number, b: number): number {
  return a + b;
}

const divide = (a: number, b: number): number => a / b;

// Optional and default parameters
function greet(name: string, age?: number = 0): string {
  return `Hello ${name}, age ${age}`;
}

// Variadic functions
function sum(...numbers: number[]): number {
  return numbers.reduce((a, b) => a + b, 0);
}
```

## Interfaces and Types

```typescript
// Interface
interface User {
  id: number;
  name: string;
  email?: string;  // Optional
  readonly role: string;  // Readonly
}

// Type alias
type Status = "active" | "inactive";
type Callback = (data: string) => void;

// Extend interface
interface Admin extends User {
  permissions: string[];
}
```

## Union and Intersection Types

```typescript
type A = string | number;  // Union
type B = { a: string } & { b: number };  // Intersection

function process(val: string | number) {
  if (typeof val === "string") {
    val.toUpperCase();
  } else {
    val.toFixed();
  }
}
```

## Generics

```typescript
// Generic function
function identity<T>(value: T): T {
  return value;
}

// Generic interface
interface Container<T> {
  value: T;
}

// Generic with constraints
function longest<T extends { length: number }>(a: T, b: T): T {
  return a.length > b.length ? a : b;
}

// Generic class
class Cache<T> {
  private items: Map<string, T> = new Map();
  set(key: string, value: T): void { this.items.set(key, value); }
  get(key: string): T | undefined { return this.items.get(key); }
}
```

## Classes

```typescript
class User {
  // Properties
  public id: number;
  private password: string;
  protected email: string;
  readonly role: string;

  constructor(id: number, email: string, password: string, role: string) {
    this.id = id;
    this.email = email;
    this.password = password;
    this.role = role;
  }

  // Methods
  public greet(): string {
    return `Hello, ${this.email}`;
  }

  private validate(): boolean {
    return this.password.length > 8;
  }

  // Getters and setters
  get userRole(): string {
    return this.role;
  }

  set userRole(role: string) {
    this.role = role;
  }

  // Static members
  static create(email: string): User {
    return new User(Math.random(), email, "default", "user");
  }
}

// Inheritance
class Admin extends User {
  permissions: string[];

  constructor(id: number, email: string, password: string, permissions: string[]) {
    super(id, email, password, "admin");
    this.permissions = permissions;
  }
}

// Abstract class
abstract class Animal {
  abstract makeSound(): void;
  move(): void {
    console.log("Moving...");
  }
}
```

## Utility Types

```typescript
// Partial - all properties optional
type PartialUser = Partial<User>;

// Required - all properties required
type RequiredUser = Required<User>;

// Pick - select specific properties
type UserPreview = Pick<User, "id" | "name">;

// Omit - exclude specific properties
type UserWithoutPassword = Omit<User, "password">;

// Record - object with specific keys
type StatusCount = Record<"pending" | "done", number>;

// Readonly - make all properties readonly
type ReadonlyUser = Readonly<User>;

// Exclude - remove from union
type NonNullUser = Exclude<User | null, null>;

// Extract - keep matching types
type StringKeys = Extract<"id" | "name" | "email", string>;

// ReturnType - get function return type
type GreetReturn = ReturnType<typeof greet>;
```

## Advanced Types

```typescript
// Mapped types
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

// Conditional types
type IsString<T> = T extends string ? true : false;
type Flatten<T> = T extends Array<infer U> ? U : T;

// Template literal types
type EventMap = {
  "click": MouseEvent;
  "keydown": KeyboardEvent;
};

type EventHandlers = {
  [K in keyof EventMap as `on${Capitalize<string & K>}`]: (e: EventMap[K]) => void;
};
```

## Async/Await and Promises

```typescript
// Promise
const promise: Promise<string> = new Promise((resolve, reject) => {
  if (Math.random() > 0.5) {
    resolve("Success!");
  } else {
    reject(new Error("Failed!"));
  }
});

// Async/await
async function fetchUser(id: number): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error("Not found");
    return response.json();
  } catch (error) {
    console.error(error);
    throw error;
  }
}

// Promise.all
const [users, posts] = await Promise.all([
  fetchUsers(),
  fetchPosts()
]);
```

## Modules

```typescript
// Export
export function greet(name: string): string { return `Hello ${name}`; }
export const PI = 3.14159;
export interface User { id: number; name: string; }
export default class Logger { }

// Import
import { greet, PI, User } from "./utils";
import Logger from "./logger";
import * as utils from "./utils";
import { greet as sayHello } from "./utils";
```

## React with TypeScript

```typescript
import React, { FC, useState, useEffect } from "react";

interface Props {
  name: string;
  age?: number;
}

const Greeting: FC<Props> = ({ name, age = 0 }) => {
  return <div>Hello {name}, {age}</div>;
};

function Counter(): JSX.Element {
  const [count, setCount] = useState<number>(0);
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    // Fetch user
  }, []);

  const handleClick = (): void => {
    setCount(count + 1);
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>): void => {
    console.log(e.currentTarget.value);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleClick}>Increment</button>
      <input onChange={handleChange} />
    </div>
  );
}
```

## Error Handling

```typescript
// Custom error
class ValidationError extends Error {
  constructor(public field: string, message: string) {
    super(message);
    this.name = "ValidationError";
  }
}

try {
  throw new ValidationError("email", "Invalid email");
} catch (error) {
  if (error instanceof ValidationError) {
    console.log(`Field ${error.field}: ${error.message}`);
  }
}

// Result type pattern
type Result<T, E = string> = 
  | { ok: true; value: T }
  | { ok: false; error: E };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { ok: false, error: "Division by zero" };
  }
  return { ok: true, value: a / b };
}
```

## Type Guards

```typescript
// typeof guard
function process(value: string | number) {
  if (typeof value === "string") {
    value.toUpperCase();
  } else {
    value.toFixed();
  }
}

// instanceof guard
if (error instanceof Error) {
  console.log(error.message);
}

// Property check
if ("property" in obj) {
  obj.property = "value";
}

// Type predicate
function isUser(obj: any): obj is User {
  return obj.id && obj.name;
}

// Discriminated union
type Response = 
  | { status: "success"; data: any }
  | { status: "error"; error: string };

function handle(response: Response) {
  if (response.status === "success") {
    console.log(response.data);
  }
}
```

## tsconfig.json Essentials

```json
{
  "compilerOptions": {
    "target": "ES2020",              // Output JavaScript version
    "module": "ES2020",              // Module system
    "lib": ["ES2020", "DOM"],        // Available APIs
    "outDir": "./dist",              // Output folder
    "rootDir": "./src",              // Source folder
    "strict": true,                  // Enable all strict checks
    "esModuleInterop": true,         // CommonJS compatibility
    "skipLibCheck": true,            // Skip .d.ts checking
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

## Quick Patterns

### Singleton Pattern
```typescript
class Singleton {
  private static instance: Singleton;
  private constructor() {}
  static getInstance(): Singleton {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }
    return Singleton.instance;
  }
}
```

### Observer Pattern
```typescript
class EventEmitter<T> {
  private listeners: ((data: T) => void)[] = [];
  on(listener: (data: T) => void): void {
    this.listeners.push(listener);
  }
  emit(data: T): void {
    this.listeners.forEach(l => l(data));
  }
}
```

### Builder Pattern
```typescript
class UserBuilder {
  private user: Partial<User> = {};
  setName(name: string): this { this.user.name = name; return this; }
  setEmail(email: string): this { this.user.email = email; return this; }
  build(): User { return this.user as User; }
}
```

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
