# Advanced Types

## Why This Topic Matters

Advanced types enable sophisticated type transformations. They're essential for:

- **Mapped types** - Transform types dynamically
- **Conditional types** - Type depends on another type
- **Type inference** - Let TypeScript infer complex types
- **Template literal types** - Type with string templates
- **Framework development** - Build type-safe libraries

Advanced types are where TypeScript becomes truly powerful.

## Core Concept

### Mapped Types

Transform each property of a type:

```typescript
// Add readonly to all properties
type Readonly<T> = {
  readonly [K in keyof T]: T[K];
};

interface User {
  id: number;
  name: string;
}

type ReadonlyUser = Readonly<User>;
// Result: { readonly id: number; readonly name: string }
```

### Conditional Types

Type depends on a condition:

```typescript
// If T is array, return array element type, else return T
type Flatten<T> = T extends Array<infer U> ? U : T;

type A = Flatten<string[]>; // string
type B = Flatten<string>; // string
```

## Mapped Types

```typescript
// Make all properties optional
type Optional<T> = {
  [K in keyof T]?: T[K];
};

// Make all properties readonly
type ReadOnly<T> = {
  readonly [K in keyof T]: T[K];
};

// Make all properties functions that return the value
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

// Filter to only string properties
type StringPropertiesOnly<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

// Add prefix to keys
type Prefixed<T> = {
  [K in keyof T as `prefix_${string & K}`]: T[K];
};
```

## Conditional Types

```typescript
// Check if type extends another
type IsString<T> = T extends string ? true : false;

type A = IsString<"hello">; // true
type B = IsString<number>; // false

// Extract type from union
type Extract<T, U> = T extends U ? T : never;

type Status = "pending" | "completed" | "failed";
type Success = Extract<Status, "completed" | "pending">; // "completed" | "pending"

// Get return type
type GetReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function greet(name: string): string {
  return `Hello, ${name}`;
}

type GreetReturn = GetReturnType<typeof greet>; // string
```

## Examples

### Example 1: Mapped Type for Getters

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// Create getters for all properties
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type UserGetters = Getters<User>;
// Result:
// {
//   getId: () => number;
//   getName: () => string;
//   getEmail: () => string;
// }

class UserModel implements UserGetters {
  constructor(private user: User) {}

  getId(): number {
    return this.user.id;
  }

  getName(): string {
    return this.user.name;
  }

  getEmail(): string {
    return this.user.email;
  }
}
```

### Example 2: Conditional Type for API Responses

```typescript
type ApiCall<T> = T extends Promise<any> ? T : Promise<T>;

// If T is already a Promise, keep it
// Otherwise, wrap in Promise
type AsyncUserId = ApiCall<number>; // Promise<number>
type AsyncUser = ApiCall<Promise<User>>; // Promise<User>
```

### Example 3: Filter Type Properties

```typescript
interface Config {
  database: string;
  port: number;
  timeout: number;
  apiKey: string;
}

// Extract only string properties
type StringConfig<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

type StringConfigPart = StringConfig<Config>;
// Result: { database: string; apiKey: string }
```

### Example 4: Extract Function Parameters

```typescript
type GetParameters<T> = T extends (...args: infer P) => any ? P : never;

function process(id: number, name: string, active: boolean): void {}

type ProcessParams = GetParameters<typeof process>;
// Result: [id: number, name: string, active: boolean]

function handleParams(...args: ProcessParams) {
  // args has correct types
}
```

### Example 5: Nested Property Accessor

```typescript
type NestedProperty<
  T,
  K extends string,
> = K extends `${infer First}.${infer Rest}`
  ? First extends keyof T
    ? NestedProperty<T[First], Rest>
    : never
  : K extends keyof T
    ? T[K]
    : never;

interface User {
  id: number;
  profile: {
    name: string;
    address: {
      city: string;
    };
  };
}

type UserCity = NestedProperty<User, "profile.address.city">; // string
```

## Common Mistakes

### Mistake 1: Incorrect keyof Usage

```typescript
// ❌ Wrong - keyof T is the property names as union
type Bad<T> = {
  [K in T]: string; // ERROR: T is not iterable
};

// ✅ Correct
type Good<T> = {
  [K in keyof T]: string;
};
```

### Mistake 2: Forgetting `infer` in Conditional

```typescript
// ❌ Can't capture type without infer
// type GetReturn<T> = T extends (...args: any[]) => any ? T : never;

// ✅ Use infer to capture
type GetReturn<T> = T extends (...args: any[]) => infer R ? R : never;
```

### Mistake 3: Overly Complex Conditional Types

```typescript
// ❌ Too complex - hard to understand
type DeepPartial<T> = T extends object
  ? {
      [P in keyof T]?: DeepPartial<T[P]>;
    }
  : T;

// ✅ Add comments
/**
 * Recursively make all properties optional
 */
type DeepPartial<T> = T extends object
  ? {
      [P in keyof T]?: DeepPartial<T[P]>;
    }
  : T;
```

## Practice Tasks

1. **Mapped Type**
   - Create a mapped type that adds a prefix to all keys
   - Create a mapped type that makes all values optional

2. **Conditional Type**
   - Create a conditional type that checks for strings
   - Create a type that extracts array elements

3. **Complex Mapped Type**
   - Create getters for all properties
   - Create setters for all properties

4. **Extract Return Types**
   - Create a type that gets function return types
   - Use it with different functions

5. **Filter Properties**
   - Create a type that extracts only string properties
   - Create a type that extracts only function properties

## Mini Project: Type-Safe ORM Query Builder

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Create where clause type - only allows User properties as keys
type WhereClause<T> = {
  [K in keyof T]?: T[K];
};

// Select specific columns
type Select<T, K extends keyof T> = Pick<T, K>;

// Query builder
class QueryBuilder<T> {
  private whereConditions: Partial<T> = {};
  private selectedColumns: (keyof T)[] = [];

  where(conditions: WhereClause<T>): this {
    this.whereConditions = conditions;
    return this;
  }

  select<K extends keyof T>(...columns: K[]): QueryBuilder<Select<T, K>> {
    // Would return a new builder with subset of columns
    return this as any;
  }

  build(): { where: Partial<T>; select: (keyof T)[] } {
    return {
      where: this.whereConditions,
      select: this.selectedColumns,
    };
  }
}

// Usage:
const query = new QueryBuilder<User>();
query.where({ name: "Alice", age: 30 }).select("id", "name", "email").build();

// ✅ Type-safe - only User properties allowed
// query.where({ unknown: "field" });  // ❌ ERROR
```

## Official TypeScript Docs

- **Mapped Types**: https://www.typescriptlang.org/docs/handbook/2/mapped-types.html
- **Conditional Types**: https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
- **Template Literal Types**: https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html

## Previous Topic

← [Utility Types](../14-utility-types/README.md)

## Next Topic

→ [DOM and Frontend](../16-dom-and-frontend/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
