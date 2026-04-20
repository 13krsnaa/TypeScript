# Classes and OOP

## Why This Topic Matters

Classes bring object-oriented programming to TypeScript. They're important for:

- **Organizing code** - Group related data and functions
- **Reusability** - Inheritance and composition patterns
- **Encapsulation** - Control what's public and private
- **React development** - Class components (though hooks are now preferred)
- **Backend services** - Database models, business logic
- **Design patterns** - Singleton, Factory, Observer, etc.

Most full-stack projects use classes at some point.

## Core Concept

A class combines **data (properties) and functions (methods)**:

```typescript
class User {
  // Properties
  name: string;
  age: number;

  // Constructor - runs when creating a new instance
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  // Method
  greet(): string {
    return `Hello, I'm ${this.name}`;
  }
}

// Create an instance
const user = new User("Alice", 30);
console.log(user.greet()); // "Hello, I'm Alice"
```

## Syntax

### Basic Class

```typescript
class Person {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  describe(): string {
    return `${this.name} is ${this.age} years old`;
  }
}

// Create instance
const person = new Person("Alice", 30);
```

### With Access Modifiers

```typescript
class User {
  // Public - anyone can access (default)
  public id: number;

  // Private - only inside the class
  private password: string;

  // Protected - inside class and subclasses
  protected email: string;

  constructor(id: number, email: string, password: string) {
    this.id = id;
    this.email = email;
    this.password = password;
  }

  // Private method
  private validatePassword(): boolean {
    return this.password.length > 8;
  }

  // Public method
  public isValid(): boolean {
    return this.validatePassword();
  }
}
```

### Inheritance

```typescript
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  speak(): void {
    console.log(`${this.name} makes a sound`);
  }
}

class Dog extends Animal {
  breed: string;

  constructor(name: string, breed: string) {
    super(name); // Call parent constructor
    this.breed = breed;
  }

  speak(): void {
    console.log(`${this.name} barks`);
  }
}

const dog = new Dog("Buddy", "Golden Retriever");
dog.speak(); // "Buddy barks"
```

### Static Members

```typescript
class MathUtils {
  // Static property
  static PI = 3.14159;

  // Static method
  static calculateArea(radius: number): number {
    return this.PI * radius * radius;
  }
}

// Use without creating instance
console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.calculateArea(5)); // ~78.5
```

### Getters and Setters

```typescript
class User {
  private _age: number;

  constructor(age: number) {
    this._age = age;
  }

  get age(): number {
    return this._age;
  }

  set age(value: number) {
    if (value < 0 || value > 150) {
      console.log("Invalid age");
      return;
    }
    this._age = value;
  }
}

const user = new User(25);
console.log(user.age); // 25 (getter)
user.age = 26; // setter
user.age = -5; // Invalid age
```

## Examples

### Example 1: Simple Class

```typescript
class BankAccount {
  accountNumber: string;
  balance: number;

  constructor(accountNumber: string, initialBalance: number) {
    this.accountNumber = accountNumber;
    this.balance = initialBalance;
  }

  deposit(amount: number): void {
    this.balance += amount;
    console.log(`Deposited $${amount}. New balance: $${this.balance}`);
  }

  withdraw(amount: number): boolean {
    if (amount > this.balance) {
      console.log("Insufficient funds");
      return false;
    }
    this.balance -= amount;
    console.log(`Withdrew $${amount}. New balance: $${this.balance}`);
    return true;
  }

  getBalance(): number {
    return this.balance;
  }
}

// Usage
const account = new BankAccount("123456", 1000);
account.deposit(500); // Deposited $500. New balance: $1500
account.withdraw(200); // Withdrew $200. New balance: $1300
```

### Example 2: Inheritance

```typescript
// Parent class
class Vehicle {
  brand: string;
  year: number;

  constructor(brand: string, year: number) {
    this.brand = brand;
    this.year = year;
  }

  info(): string {
    return `${this.brand} (${this.year})`;
  }
}

// Child class
class Car extends Vehicle {
  doors: number;

  constructor(brand: string, year: number, doors: number) {
    super(brand, year);
    this.doors = doors;
  }

  info(): string {
    return `${super.info()} - ${this.doors} doors`;
  }
}

const car = new Car("Toyota", 2023, 4);
console.log(car.info()); // "Toyota (2023) - 4 doors"
```

### Example 3: Access Modifiers

```typescript
class SecureUser {
  public id: number;
  private _password: string;

  constructor(id: number, password: string) {
    this.id = id;
    this._password = password;
  }

  // ✅ Can access private properties from inside the class
  public authenticate(password: string): boolean {
    return password === this._password;
  }
}

const user = new SecureUser(1, "secret123");
console.log(user.id); // ✅ Public - accessible
// console.log(user._password);  // ❌ Private - not accessible
console.log(user.authenticate("secret123")); // ✅ Use public method
```

### Example 4: Static Members

```typescript
class Counter {
  private static count: number = 0;

  static increment(): void {
    Counter.count++;
  }

  static getCount(): number {
    return Counter.count;
  }
}

Counter.increment();
Counter.increment();
console.log(Counter.getCount()); // 2
```

### Example 5: Getters and Setters

```typescript
class Temperature {
  private _celsius: number;

  constructor(celsius: number) {
    this._celsius = celsius;
  }

  // Getter
  get fahrenheit(): number {
    return (this._celsius * 9) / 5 + 32;
  }

  // Setter
  set fahrenheit(f: number) {
    this._celsius = ((f - 32) * 5) / 9;
  }

  get celsius(): number {
    return this._celsius;
  }

  set celsius(c: number) {
    this._celsius = c;
  }
}

const temp = new Temperature(0);
console.log(temp.fahrenheit); // 32
temp.fahrenheit = 68;
console.log(temp.celsius); // 20
```

## Common Mistakes

### Mistake 1: Forgetting `super()` in Constructor

```typescript
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

// ❌ ERROR - must call super()
// class Dog extends Animal {
//   constructor(name: string, breed: string) {
//     this.breed = breed;  // ERROR - must call super() first
//   }
// }

// ✅ Call super() first
class Dog extends Animal {
  breed: string;
  constructor(name: string, breed: string) {
    super(name);
    this.breed = breed;
  }
}
```

### Mistake 2: Using `private` Then Accessing from Outside

```typescript
class Secret {
  private data: string = "secret";
}

const s = new Secret();
// console.log(s.data);  // ❌ ERROR: Property 'data' is private

// ✅ Provide a public method if needed
class Secret {
  private data: string = "secret";

  getData(): string {
    return this.data;
  }
}

console.log(new Secret().getData()); // ✅
```

### Mistake 3: Not Initializing Properties

```typescript
// ❌ Property 'age' has no initializer
// class User {
//   age: number;
// }

// ✅ Initialize in constructor
class User {
  age: number;

  constructor(age: number) {
    this.age = age;
  }
}

// ✅ Or provide a default value
class User {
  age: number = 0;
}
```

### Mistake 4: Confusion Between Instance and Static

```typescript
// ❌ Confusing - what's the difference?
class Counter {
  count: number = 0; // Instance property
  static total: number = 0; // Static property
}

// ✅ Understand the difference
class Counter {
  // Each instance has its own count
  count: number = 0;

  // Shared across all instances
  static total: number = 0;

  constructor() {
    Counter.total++;
  }
}

const c1 = new Counter();
const c2 = new Counter();
console.log(c1.count); // 0
console.log(c2.count); // 0
console.log(Counter.total); // 2
```

## Practice Tasks

1. **Create a Simple Class**
   - Create a Person class with name and age
   - Add a method to describe the person

2. **Inheritance**
   - Create an Animal class
   - Create Dog and Cat classes that extend Animal
   - Override methods

3. **Access Modifiers**
   - Create a class with public, private, and protected members
   - Try to access each type

4. **Static Members**
   - Create a class with static properties and methods
   - Use them without creating an instance

5. **Getters and Setters**
   - Create a class with private property
   - Use getters and setters to control access

## Mini Project: Todo Manager Class

Create a todo manager with classes:

```typescript
interface Todo {
  id: number;
  title: string;
  description: string;
  completed: boolean;
}

class TodoManager {
  private todos: Todo[] = [];
  private nextId: number = 1;

  addTodo(title: string, description: string): Todo {
    const todo: Todo = {
      id: this.nextId++,
      title,
      description,
      completed: false,
    };
    this.todos.push(todo);
    return todo;
  }

  completeTodo(id: number): void {
    const todo = this.todos.find((t) => t.id === id);
    if (todo) {
      todo.completed = true;
    }
  }

  getTodos(): Todo[] {
    return this.todos;
  }

  getCompletedCount(): number {
    return this.todos.filter((t) => t.completed).length;
  }

  deleteTodo(id: number): void {
    this.todos = this.todos.filter((t) => t.id !== id);
  }
}

// Usage:
const manager = new TodoManager();
manager.addTodo("Learn TypeScript", "Complete the roadmap");
manager.addTodo("Build a project", "Create a full-stack app");
manager.completeTodo(1);

console.log(manager.getTodos());
console.log(`Completed: ${manager.getCompletedCount()}`);
```

## Official TypeScript Docs

- **Classes**: https://www.typescriptlang.org/docs/handbook/2/classes.html
- **Class Members**: https://www.typescriptlang.org/docs/handbook/2/classes.html#class-members
- **Member Visibility**: https://www.typescriptlang.org/docs/handbook/2/classes.html#member-visibility
- **Inheritance**: https://www.typescriptlang.org/docs/handbook/2/classes.html#inheritance

## Previous Topic

← [Generics](../09-generics/README.md)

## Next Topic

→ [Modules and Namespaces](../11-modules-and-namespaces/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
