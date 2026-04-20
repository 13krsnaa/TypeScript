# Modules and Namespaces

## Why This Topic Matters

As projects grow, you need to organize code into separate files. Modules help you:

- **Organize code** - Group related functionality
- **Avoid name conflicts** - Separate namespaces
- **Enable code reuse** - Share code across projects
- **Team development** - Clear file structure
- **Maintain performance** - Lazy load code

Every TypeScript project uses modules.

## Core Concept

A **module** is a single file that exports functions, types, or classes that other files can import.

```typescript
// utils.ts
export function add(a: number, b: number): number {
  return a + b;
}

// main.ts
import { add } from "./utils";
console.log(add(2, 3)); // 5
```

## Syntax

### Exporting

```typescript
// Named exports
export function greet(name: string): void {
  console.log(`Hello, ${name}`);
}

export const PI = 3.14159;

export interface User {
  id: number;
  name: string;
}

export class Calculator {
  add(a: number, b: number): number {
    return a + b;
  }
}

// Default export (one per file)
export default function main() {
  console.log("Main function");
}

// Re-export
export { greet } from "./greet";
export * from "./math"; // Export everything from math.ts
```

### Importing

```typescript
// Import named exports
import { greet, PI, User } from "./utils";

// Import everything as namespace
import * as utils from "./utils";
utils.greet("Alice");

// Import with alias
import { greet as sayHello } from "./utils";
sayHello("Bob");

// Import default export
import main from "./main";
main();

// Import multiple from same file
import { add, subtract, multiply } from "./math";
```

## Examples

### Example 1: Basic Module Structure

Create `src/math.ts`:

```typescript
export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}

export function multiply(a: number, b: number): number {
  return a * b;
}

export function divide(a: number, b: number): number {
  if (b === 0) throw new Error("Division by zero");
  return a / b;
}
```

Create `src/index.ts`:

```typescript
import { add, subtract, multiply, divide } from "./math";

console.log(add(10, 5)); // 15
console.log(subtract(10, 5)); // 5
console.log(multiply(10, 5)); // 50
console.log(divide(10, 5)); // 2
```

### Example 2: Default Exports

Create `src/logger.ts`:

```typescript
interface LogConfig {
  prefix: string;
  timestamp: boolean;
}

export default class Logger {
  private config: LogConfig;

  constructor(config: LogConfig) {
    this.config = config;
  }

  log(message: string): void {
    const prefix = this.config.prefix ? `[${this.config.prefix}] ` : "";
    const time = this.config.timestamp ? `[${new Date().toISOString()}] ` : "";
    console.log(time + prefix + message);
  }
}
```

Create `src/index.ts`:

```typescript
import Logger from "./logger";

const logger = new Logger({
  prefix: "APP",
  timestamp: true,
});

logger.log("Application started");
logger.log("Processing request");
```

### Example 3: Type Exports

Create `src/types.ts`:

```typescript
export interface User {
  id: number;
  name: string;
  email: string;
}

export interface Product {
  id: number;
  title: string;
  price: number;
}

export type Status = "active" | "inactive" | "pending";
```

Create `src/index.ts`:

```typescript
import { User, Product, Status } from "./types";

const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};

const product: Product = {
  id: 1,
  title: "Laptop",
  price: 999,
};

let status: Status = "active";
```

### Example 4: Namespace (Less Common)

```typescript
namespace Math {
  export function add(a: number, b: number): number {
    return a + b;
  }

  export function subtract(a: number, b: number): number {
    return a - b;
  }
}

// Usage
console.log(Math.add(10, 5)); // 15
console.log(Math.subtract(10, 5)); // 5
```

### Example 5: Re-exports

Create `src/math/operations.ts`:

```typescript
export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}
```

Create `src/math/advanced.ts`:

```typescript
export function sqrt(n: number): number {
  return Math.sqrt(n);
}

export function pow(base: number, exp: number): number {
  return Math.pow(base, exp);
}
```

Create `src/math/index.ts` (barrel file):

```typescript
// Re-export everything from other math modules
export * from "./operations";
export * from "./advanced";
```

Create `src/index.ts`:

```typescript
// Import everything from one place
import { add, subtract, sqrt, pow } from "./math";

console.log(add(10, 5)); // 15
console.log(sqrt(16)); // 4
console.log(pow(2, 3)); // 8
```

## Project Structure with Modules

```
my-app/
├── src/
│   ├── index.ts
│   ├── types/
│   │   ├── User.ts
│   │   ├── Product.ts
│   │   └── index.ts (barrel)
│   ├── utils/
│   │   ├── math.ts
│   │   ├── string.ts
│   │   └── index.ts (barrel)
│   ├── services/
│   │   ├── userService.ts
│   │   ├── productService.ts
│   │   └── index.ts (barrel)
│   └── models/
│       ├── User.ts
│       ├── Product.ts
│       └── index.ts (barrel)
├── dist/
├── tsconfig.json
└── package.json
```

## Common Mistakes

### Mistake 1: Circular Imports

```typescript
// ❌ utils.ts imports from main.ts which imports from utils.ts
// This creates a circular dependency

// utils.ts
import { main } from "./main";

export function util() {
  main();
}

// main.ts
import { util } from "./utils";

export function main() {
  util();
}

// ✅ Refactor to break the cycle
// Extract common code into a third file
```

### Mistake 2: Mixing Namespaces and Modules

```typescript
// ❌ Confusing - mixing two organization systems
namespace MyUtils {
  export function add(a: number, b: number): number {
    return a + b;
  }
}

export default MyUtils;

// ✅ Use modules consistently
export function add(a: number, b: number): number {
  return a + b;
}
```

### Mistake 3: Forgetting File Extensions

```typescript
// ❌ Missing .js extension (affects runtime)
// import { add } from "./math";

// ✅ Include extension in imports (for ES modules)
import { add } from "./math.js";
```

### Mistake 4: Not Using Barrel Exports

```typescript
// ❌ Lots of imports from different files
import { User } from "./types/User";
import { Product } from "./types/Product";
import { add } from "./utils/math";
import { toUpper } from "./utils/string";

// ✅ Use barrel files (index.ts in each folder)
import { User, Product } from "./types";
import { add, toUpper } from "./utils";
```

## Practice Tasks

1. **Create Multiple Modules**
   - Create a math.ts module with functions
   - Create a main.ts file that imports and uses them

2. **Type Modules**
   - Create a types.ts file with interfaces
   - Import and use types in other files

3. **Default Exports**
   - Create a class with default export
   - Import it in another file

4. **Barrel Exports**
   - Create an index.ts that re-exports everything
   - Import from that barrel file

5. **Namespaces**
   - Create a namespace for utilities
   - Access functions via namespace.function()

## Mini Project: Multi-File Application

Create a small application with multiple modules:

`src/types.ts`:

```typescript
export interface User {
  id: number;
  name: string;
  email: string;
}

export type UserStatus = "active" | "inactive";
```

`src/userService.ts`:

```typescript
import { User } from "./types";

const users: User[] = [];

export function createUser(name: string, email: string): User {
  const user: User = {
    id: users.length + 1,
    name,
    email,
  };
  users.push(user);
  return user;
}

export function getUsers(): User[] {
  return users;
}

export function findUser(id: number): User | undefined {
  return users.find((u) => u.id === id);
}
```

`src/index.ts`:

```typescript
import { createUser, getUsers, findUser } from "./userService";

createUser("Alice", "alice@example.com");
createUser("Bob", "bob@example.com");

console.log("All users:", getUsers());
console.log("Find user 1:", findUser(1));
```

## Official TypeScript Docs

- **Modules**: https://www.typescriptlang.org/docs/handbook/2/modules.html
- **Import and Export**: https://www.typescriptlang.org/docs/handbook/2/modules.html#export
- **Namespaces**: https://www.typescriptlang.org/docs/handbook/namespaces.html

## Previous Topic

← [Classes and OOP](../10-classes-and-oop/README.md)

## Next Topic

→ [Async and Promises](../12-async-and-promises/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
