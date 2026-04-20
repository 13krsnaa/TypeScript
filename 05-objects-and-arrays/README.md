# Objects and Arrays

## Why This Topic Matters

Objects and arrays are everywhere in TypeScript. Proper typing for them ensures:

- **Data structure validation** - Know what properties an object must have
- **Array element safety** - Can't accidentally mix types in an array
- **Refactoring confidence** - Change object structure, catch all usages
- **API consistency** - Match expected data shapes from backends

Most of your TypeScript work involves typing objects and arrays.

## Core Concept

### Objects

Objects are collections of key-value pairs. TypeScript ensures each key has the right value type:

```typescript
// Define what an object looks like
interface User {
  id: number;
  name: string;
  email: string;
}

// Create an object matching that shape
const user: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};
```

### Arrays

Arrays are collections of elements. TypeScript ensures all elements have the same type:

```typescript
// Array of numbers
const numbers: number[] = [1, 2, 3];

// Array of strings
const names: string[] = ["Alice", "Bob"];

// Array of objects
const users: User[] = [
  { id: 1, name: "Alice", email: "alice@example.com" },
  { id: 2, name: "Bob", email: "bob@example.com" },
];
```

## Syntax

### Object Types

```typescript
// Using interface
interface User {
  id: number;
  name: string;
  email: string;
}

// Using type alias
type Product = {
  id: number;
  name: string;
  price: number;
};

// Nested objects
interface Order {
  id: number;
  user: User;
  products: Product[];
  total: number;
}

// Optional properties
interface Profile {
  name: string;
  bio?: string; // Optional
  age?: number;
}

// Readonly properties
interface Config {
  readonly apiUrl: string;
  readonly port: number;
}
```

### Array Types

```typescript
// Basic array syntax
let numbers: number[] = [1, 2, 3];
let names: string[] = ["Alice", "Bob"];
let flags: boolean[] = [true, false];

// Generic array syntax (same as above)
let nums: Array<number> = [1, 2, 3];

// Array of objects
interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

let todos: Todo[] = [
  { id: 1, title: "Learn TypeScript", completed: true },
  { id: 2, title: "Build a project", completed: false },
];

// Union types in arrays
let mixed: (string | number)[] = ["hello", 42, "world"];

// Tuples (fixed-length arrays with specific types)
let coordinates: [number, number] = [10, 20];
let response: [string, number, boolean] = ["success", 200, true];
```

## Examples

### Example 1: Simple Object

```typescript
// ❌ Without types
function displayUser(user) {
  console.log(user.name);
  console.log(user.email);
  // What if these properties don't exist?
}

// ✅ With types
interface User {
  id: number;
  name: string;
  email: string;
}

function displayUser(user: User): void {
  console.log(user.name);
  console.log(user.email);
  // TypeScript ensures these exist

  // ❌ This error is caught at compile-time:
  // console.log(user.phone);  // ERROR: Property 'phone' does not exist
}
```

### Example 2: Array of Objects

```typescript
interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

// ✅ Typed array
const todos: Todo[] = [
  { id: 1, title: "Learn TypeScript", completed: true },
  { id: 2, title: "Build a project", completed: false },
];

// TypeScript knows what properties exist
todos.forEach((todo) => {
  console.log(`${todo.id}: ${todo.title} - ${todo.completed}`);
});

// ❌ This errors at compile-time:
// const newTodo: Todo = {
//   id: 3,
//   title: "Missing property",
//   // completed is required but missing!
// };

// ✅ Correct:
const newTodo: Todo = {
  id: 3,
  title: "Complete project",
  completed: false,
};

todos.push(newTodo); // ✅ Type-safe
```

### Example 3: Optional Properties

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  phone?: string; // Optional - might not exist
  avatar?: string;
}

// ✅ Valid - only required properties
const user1: User = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
};

// ✅ Also valid - with optional properties
const user2: User = {
  id: 2,
  name: "Bob",
  email: "bob@example.com",
  phone: "555-1234",
  avatar: "https://...",
};

// ❌ Invalid - missing required property
// const user3: User = {
//   id: 3,
//   email: "bob@example.com"
//   // name is required but missing!
// };
```

### Example 4: Nested Objects

```typescript
interface Address {
  street: string;
  city: string;
  country: string;
}

interface Person {
  id: number;
  name: string;
  address: Address;
}

// Create nested object
const person: Person = {
  id: 1,
  name: "Alice",
  address: {
    street: "123 Main St",
    city: "New York",
    country: "USA",
  },
};

// Access nested properties
console.log(person.address.city); // "New York"

// ✅ TypeScript knows the structure at each level
// ❌ This errors:
// console.log(person.address.zipCode);  // ERROR: Property 'zipCode' does not exist
```

### Example 5: Tuples (Fixed-Length Arrays)

```typescript
// Regular array - can be any length
const colors: string[] = ["red", "green", "blue", "yellow"];

// Tuple - specific length and types at each position
type RGB = [number, number, number];
const red: RGB = [255, 0, 0];
const green: RGB = [0, 255, 0];

// ✅ Correct
const myColor: RGB = [100, 150, 200];

// ❌ Wrong length
// const invalid: RGB = [100, 150];  // ERROR: Type '[number, number]' is not assignable to type 'RGB'

// ❌ Wrong type at position
// const invalid: RGB = [100, "150", 200];  // ERROR: Type 'string' is not assignable to type 'number'

// Tuples with labels (more readable)
type Response = [status: number, message: string];
const response: Response = [200, "OK"];
console.log(response[0]); // 200
```

## Common Mistakes

### Mistake 1: Using `any` for Object Properties

```typescript
// ❌ BAD
interface User {
  name: any;
  email: any;
}

// ✅ GOOD
interface User {
  name: string;
  email: string;
}
```

### Mistake 2: Forgetting Required Properties

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
}

// ❌ Compile error - missing 'price'
// const product: Product = {
//   id: 1,
//   name: "Laptop"
// };

// ✅ Include all required properties
const product: Product = {
  id: 1,
  name: "Laptop",
  price: 999,
};
```

### Mistake 3: Mixing Array Types

```typescript
// ❌ Confusing - is it an array of numbers or strings?
const mixed: any[] = [1, "two", 3, "four"];

// ✅ Explicit - array of numbers or strings
const mixed: (number | string)[] = [1, "two", 3, "four"];

// Or even better - use union type if they're related
type NumberOrString = number | string;
const values: NumberOrString[] = [1, "two", 3, "four"];
```

### Mistake 4: Mutating Readonly Objects

```typescript
interface Config {
  readonly apiUrl: string;
  readonly timeout: number;
}

const config: Config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

// ❌ ERROR: Cannot assign to 'apiUrl' because it is a read-only property
// config.apiUrl = "https://newapi.com";

// ✅ To modify, create a new object
const newConfig: Config = {
  ...config,
  apiUrl: "https://newapi.com",
};
```

## Practice Tasks

1. **Create User Object Type**
   - Create an interface for a user with `id`, `name`, `email`
   - Create a user object matching this type

2. **Array of Objects**
   - Create an array of users
   - Add and remove users from the array

3. **Optional Properties**
   - Create an interface with some optional properties
   - Create objects with and without those properties

4. **Nested Objects**
   - Create an interface for an address
   - Create an interface for a person with an address
   - Create a person object

5. **Tuple Types**
   - Create a tuple for coordinates `[x: number, y: number]`
   - Create a tuple for API response `[status: number, data: string]`

6. **Union Types in Arrays**
   - Create an array that can hold both strings and numbers
   - Create an array of objects OR strings

## Mini Project: Todo List Manager

Create a simple todo list with proper types:

```typescript
interface Todo {
  id: number;
  title: string;
  description: string;
  completed: boolean;
  dueDate?: string;
}

class TodoManager {
  private todos: Todo[] = [];
  private nextId: number = 1;

  addTodo(title: string, description: string, dueDate?: string): Todo {
    const todo: Todo = {
      id: this.nextId++,
      title,
      description,
      completed: false,
      dueDate,
    };
    this.todos.push(todo);
    return todo;
  }

  getTodos(): Todo[] {
    return this.todos;
  }

  completeTodo(id: number): void {
    const todo = this.todos.find((t) => t.id === id);
    if (todo) {
      todo.completed = true;
    }
  }

  deleteTodo(id: number): void {
    this.todos = this.todos.filter((t) => t.id !== id);
  }
}

// Usage:
const manager = new TodoManager();
manager.addTodo("Learn TypeScript", "Complete the roadmap", "2024-12-31");
manager.addTodo("Build a project", "Create a full-stack app");

console.log(manager.getTodos());
manager.completeTodo(1);
console.log(manager.getTodos());
```

## Official TypeScript Docs

- **Objects and Interfaces**: https://www.typescriptlang.org/docs/handbook/2/objects.html
- **Arrays**: https://www.typescriptlang.org/docs/handbook/2/arrays.html
- **Tuples**: https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types

## Previous Topic

← [Functions](../04-functions/README.md)

## Next Topic

→ [Type Aliases and Interfaces](../06-type-aliases-and-interfaces/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
