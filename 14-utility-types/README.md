# Utility Types

## Why This Topic Matters

TypeScript has built-in utility types that transform existing types. They enable:

- **DRY principle** - Don't repeat type definitions
- **Type transformations** - Make types optional, readonly, etc.
- **Less boilerplate** - Reduce type code
- **Consistency** - Transform types the same way everywhere
- **Library development** - Extract parts of types

Essential for writing clean, maintainable TypeScript.

## Core Concept

Utility types are **type transformations** provided by TypeScript:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// Partial - make all properties optional
type PartialUser = Partial<User>;
// Result: { id?: number; name?: string; email?: string }

// Required - make all properties required
type RequiredUser = Required<PartialUser>;
// Result: { id: number; name: string; email: string }

// Pick - select specific properties
type UserPreview = Pick<User, "id" | "name">;
// Result: { id: number; name: string }

// Omit - exclude specific properties
type UserWithoutEmail = Omit<User, "email">;
// Result: { id: number; name: string }
```

## Common Utility Types

### Partial<T>

Make all properties optional:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type PartialUser = Partial<User>;

// Both valid
const user1: User = { id: 1, name: "Alice", email: "alice@example.com" };
const user2: PartialUser = { id: 1 }; // ✅ Only id, rest optional
```

### Required<T>

Make all properties required:

```typescript
interface Config {
  host?: string;
  port?: number;
  timeout?: number;
}

type RequiredConfig = Required<Config>;

// Must have all properties
const config: RequiredConfig = {
  host: "localhost",
  port: 3000,
  timeout: 5000,
};
```

### Pick<T, K>

Select specific properties:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type UserPreview = Pick<User, "id" | "name">;
// Result: { id: number; name: string }

const preview: UserPreview = { id: 1, name: "Alice" }; // ✅
```

### Omit<T, K>

Exclude specific properties:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string; // Don't expose this
}

type PublicUser = Omit<User, "password">;
// Result: { id: number; name: string; email: string }

const publicUser: PublicUser = {
  id: 1,
  name: "Alice",
  email: "alice@example.com",
}; // ✅
```

### Record<K, T>

Create an object with specific keys:

```typescript
type Status = "pending" | "completed" | "failed";

// Create an object with these keys
type StatusCount = Record<Status, number>;
// Result: { pending: number; completed: number; failed: number }

const counts: StatusCount = {
  pending: 5,
  completed: 10,
  failed: 2,
};
```

### Readonly<T>

Make all properties immutable:

```typescript
interface Config {
  apiUrl: string;
  timeout: number;
}

type ReadonlyConfig = Readonly<Config>;

const config: ReadonlyConfig = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
};

// config.apiUrl = "https://newapi.com";  // ❌ ERROR: cannot assign
```

### Exclude<T, U>

Remove types from a union:

```typescript
type Status = "pending" | "completed" | "failed";

type SuccessStatus = Exclude<Status, "failed">;
// Result: "pending" | "completed"

const status: SuccessStatus = "completed"; // ✅
// const status: SuccessStatus = "failed";  // ❌ ERROR
```

### Extract<T, U>

Keep only types that match:

```typescript
type Status = "pending" | "completed" | "failed";

type ErrorStatus = Extract<Status, "failed">;
// Result: "failed"
```

### ReturnType<T>

Get the return type of a function:

```typescript
function getUserData(id: number): Promise<{ id: number; name: string }> {
  return fetch(`/api/users/${id}`).then((r) => r.json());
}

type UserData = ReturnType<typeof getUserData>;
// Result: Promise<{ id: number; name: string }>
```

### Parameters<T>

Get the parameter types of a function:

```typescript
function process(id: number, name: string, active: boolean): void {}

type ProcessParams = Parameters<typeof process>;
// Result: [id: number, name: string, active: boolean]
```

## Examples

### Example 1: API Response Handling

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: string;
  updatedAt: string;
}

// Public profile (without sensitive fields)
type PublicUser = Omit<User, "password" | "updatedAt">;

// Update request (only name and email)
type UpdateUserRequest = Pick<User, "name" | "email">;

// Partial update (all optional)
type PartialUpdateRequest = Partial<UpdateUserRequest>;

// Usage:
async function getUser(id: number): Promise<PublicUser> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

async function updateUser(
  id: number,
  data: PartialUpdateRequest,
): Promise<User> {
  const response = await fetch(`/api/users/${id}`, {
    method: "PUT",
    body: JSON.stringify(data),
  });
  return response.json();
}
```

### Example 2: Configuration Management

```typescript
type AppConfig = {
  database: {
    host: string;
    port: number;
    user: string;
    password: string;
  };
  server: {
    port: number;
    host: string;
  };
  logging: {
    level: "debug" | "info" | "error";
    format: "json" | "text";
  };
};

// Make entire config optional for partial updates
type PartialConfig = Partial<AppConfig>;

// Only logging settings
type LoggingConfig = AppConfig["logging"];

// Make logging editable
type EditableLogging = Pick<AppConfig["logging"], "level">;
```

### Example 3: State Management

```typescript
interface AppState {
  user: { id: number; name: string } | null;
  loading: boolean;
  error: string | null;
  theme: "light" | "dark";
}

// Getters can return readonly version
type ReadonlyAppState = Readonly<AppState>;

// Updates only modify certain fields
type StateUpdate = Partial<Omit<AppState, "loading">>;

function updateState(state: AppState, update: StateUpdate): AppState {
  return { ...state, ...update };
}
```

### Example 4: Event Handler Types

```typescript
type EventMap = {
  "user-login": { userId: number };
  "user-logout": { userId: number };
  error: { code: number; message: string };
};

// Get specific event type
type LoginEvent = EventMap["user-login"];

// Create a record of handlers
type EventHandlers = Record<keyof EventMap, (data: any) => void>;

const handlers: EventHandlers = {
  "user-login": (data: EventMap["user-login"]) => {
    console.log(`User ${data.userId} logged in`);
  },
  "user-logout": (data: EventMap["user-logout"]) => {
    console.log(`User ${data.userId} logged out`);
  },
  error: (data: EventMap["error"]) => {
    console.log(`Error ${data.code}: ${data.message}`);
  },
};
```

## Common Mistakes

### Mistake 1: Over-Using Partial

```typescript
// ❌ Unclear what properties are optional
function updateUser(user: Partial<User>): void {
  // What's required? What's optional?
}

// ✅ Be explicit
function updateUser(user: Pick<User, "name" | "email">): void {
  // Clear which fields can be updated
}
```

### Mistake 2: Forgetting Utility Type Exists

```typescript
// ❌ Creating a type manually
interface UpdateRequest {
  name?: string;
  email?: string;
}

// ✅ Use Partial and Pick
type UpdateRequest = Partial<Pick<User, "name" | "email">>;
```

### Mistake 3: Wrong Type Extraction

```typescript
type Status = "pending" | "completed" | "failed";

// ❌ Trying to exclude multiple values without union
// type NotFailed = Exclude<Status, "failed">;  // Works
// type NotPendingOrFailed = Exclude<Status, "pending", "failed">;  // ERROR!

// ✅ Use union in second parameter
type NotPendingOrFailed = Exclude<Status, "pending" | "failed">;
```

## Practice Tasks

1. **Partial Type**
   - Create an interface
   - Create a Partial version
   - Use both types

2. **Pick and Omit**
   - Create an interface
   - Create Pick version with subset of properties
   - Create Omit version excluding properties

3. **Record Type**
   - Create a union of strings
   - Create a Record with those keys

4. **Readonly**
   - Create an interface
   - Make a readonly version
   - Try to modify (should error)

5. **Extract and Exclude**
   - Create a union type
   - Use Extract to keep specific types
   - Use Exclude to remove types

## Mini Project: API Response Types

Create comprehensive types for an API:

```typescript
// Base models
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  role: "admin" | "user";
  createdAt: string;
}

interface Post {
  id: number;
  userId: number;
  title: string;
  content: string;
  published: boolean;
  createdAt: string;
}

// API responses
type UserResponse = Omit<User, "password" | "createdAt">;
type CreateUserRequest = Pick<User, "name" | "email" | "password">;
type UpdateUserRequest = Partial<Pick<User, "name" | "email">>;

type PostResponse = Omit<Post, "userId">;
type CreatePostRequest = Pick<Post, "title" | "content">;
type UpdatePostRequest = Partial<Omit<Post, "id" | "userId" | "createdAt">>;

// Generic API response
type ApiResponse<T> = {
  status: number;
  data: T;
  timestamp: string;
};

// Compose responses
type GetUserResponse = ApiResponse<UserResponse>;
type CreateUserResponse = ApiResponse<UserResponse>;
type GetPostsResponse = ApiResponse<PostResponse[]>;

// Status types
type RequestStatus = "idle" | "loading" | "success" | "error";
type StatusRecord = Record<RequestStatus, number>;

const statusCounts: StatusRecord = {
  idle: 0,
  loading: 2,
  success: 10,
  error: 1,
};
```

## Official TypeScript Docs

- **Utility Types**: https://www.typescriptlang.org/docs/handbook/utility-types.html
- **Partial**: https://www.typescriptlang.org/docs/handbook/utility-types.html#partialtype
- **Pick**: https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys
- **Omit**: https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys
- **Record**: https://www.typescriptlang.org/docs/handbook/utility-types.html#recordkeys-type

## Previous Topic

← [Error Handling](../13-error-handling/README.md)

## Next Topic

→ [Advanced Types](../15-advanced-types/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
