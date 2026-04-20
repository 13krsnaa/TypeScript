# Async and Promises

## Why This Topic Matters

Modern web applications are asynchronous. Understanding async code is crucial for:

- **API calls** - Fetch data without blocking
- **Database queries** - Wait for results
- **File operations** - Read/write files
- **User experience** - Keep UI responsive
- **Backend services** - Handle multiple requests

Async TypeScript ensures type safety for delayed operations.

## Core Concept

**Async code** runs in the background. Instead of waiting for it to finish, you can do other things:

```typescript
// ❌ Synchronous - blocks everything
function fetchData(): Data {
  wait(5 seconds);  // Everything is frozen
  return data;
}

// ✅ Asynchronous - doesn't block
async function fetchData(): Promise<Data> {
  const data = await fetch(...);  // Wait, but don't block
  return data;
}
```

## Key Concepts

### Promise

A `Promise` represents a value that might not be ready yet:

```typescript
// A promise that resolves to a string
const promise: Promise<string> = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Done!");
  }, 1000);
});

// Use the promise
promise.then((result) => {
  console.log(result); // "Done!" after 1 second
});
```

### Async/Await

Modern way to work with promises:

```typescript
async function getData(): Promise<string> {
  const result = await somePromise(); // Wait for promise
  return result; // Return the resolved value
}
```

## Syntax

### Promise

```typescript
// Create a promise
const promise: Promise<string> = new Promise((resolve, reject) => {
  if (/* success */) {
    resolve("Success!");
  } else {
    reject(new Error("Failed!"));
  }
});

// Use the promise
promise
  .then(result => console.log(result))
  .catch(error => console.log(error));

// Chain promises
promise
  .then(result => process(result))
  .then(processed => save(processed))
  .catch(error => console.log(error));
```

### Async/Await

```typescript
// Async function
async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  const user = await response.json();
  return user;
}

// Use it
const user = await fetchUser(1);
console.log(user.name);

// Error handling
try {
  const user = await fetchUser(1);
  console.log(user.name);
} catch (error) {
  console.log("Error fetching user:", error);
}
```

## Examples

### Example 1: Basic Promise

```typescript
// ❌ Without promise - can't handle async
function fetchData(url: string): Data {
  let result;
  // Can't wait here...
  return result;
}

// ✅ With promise
function fetchData(url: string): Promise<Data> {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ id: 1, name: "Data" });
    }, 1000);
  });
}

// Use it
fetchData("/api/data").then((data) => {
  console.log(data.name);
});
```

### Example 2: Async Function

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// Async function returns a Promise
async function fetchUser(id: number): Promise<User> {
  // Await pauses execution until promise resolves
  const response = await fetch(`/api/users/${id}`);
  const user: User = await response.json();
  return user;
}

// Usage
async function main() {
  const user = await fetchUser(1);
  console.log(user.name);
}

main();
```

### Example 3: Error Handling

```typescript
interface User {
  id: number;
  name: string;
}

async function fetchUser(id: number): Promise<User> {
  try {
    const response = await fetch(`/api/users/${id}`);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    const user: User = await response.json();
    return user;
  } catch (error) {
    console.log("Error fetching user:", error);
    throw error; // Re-throw if needed
  }
}

// Usage
try {
  const user = await fetchUser(999);
} catch (error) {
  console.log("Failed to get user");
}
```

### Example 4: Multiple Async Operations

```typescript
interface User {
  id: number;
  name: string;
}

interface Post {
  id: number;
  title: string;
  userId: number;
}

async function getUserWithPosts(userId: number): Promise<{
  user: User;
  posts: Post[];
}> {
  // ❌ Sequential - slow
  // const user = await fetchUser(userId);
  // const posts = await fetchUserPosts(userId);

  // ✅ Parallel - faster
  const [user, posts] = await Promise.all([
    fetchUser(userId),
    fetchUserPosts(userId),
  ]);

  return { user, posts };
}

async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

async function fetchUserPosts(userId: number): Promise<Post[]> {
  const response = await fetch(`/api/users/${userId}/posts`);
  return response.json();
}
```

### Example 5: Array of Async Operations

```typescript
interface User {
  id: number;
  name: string;
}

async function fetchUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Fetch multiple users - parallel
async function fetchMultipleUsers(ids: number[]): Promise<User[]> {
  const promises = ids.map((id) => fetchUser(id));
  return Promise.all(promises);
}

// Usage
const users = await fetchMultipleUsers([1, 2, 3]);
console.log(users);
```

## Common Mistakes

### Mistake 1: Forgetting `await`

```typescript
// ❌ Returns a promise instead of data
async function getData() {
  return fetch("/api/data"); // Returns Promise<Response>
}

const data = getData(); // data is still a promise!

// ✅ Use await
async function getData() {
  const response = await fetch("/api/data");
  return response.json(); // Returns Promise<any>
}

const data = await getData(); // Now data has the value
```

### Mistake 2: Not Handling Errors

```typescript
// ❌ Unhandled promise rejection
async function getData() {
  const response = await fetch("/api/data"); // Could fail!
  return response.json();
}

// No error handling - could crash

// ✅ Handle errors
async function getData() {
  try {
    const response = await fetch("/api/data");
    return response.json();
  } catch (error) {
    console.log("Error:", error);
    return null;
  }
}
```

### Mistake 3: Sequential Instead of Parallel

```typescript
// ❌ Slow - waits for each
async function process() {
  const user = await fetchUser(1); // 1s
  const posts = await fetchPosts(1); // 1s
  // Total: 2s
}

// ✅ Fast - runs in parallel
async function process() {
  const [user, posts] = await Promise.all([fetchUser(1), fetchPosts(1)]);
  // Total: 1s
}
```

### Mistake 4: Mixing Callbacks and Promises

```typescript
// ❌ Confusing - mixing old and new patterns
function getData(callback: (error: Error | null, data?: Data) => void) {
  fetch("/api/data")
    .then((response) => response.json())
    .then((data) => callback(null, data))
    .catch((error) => callback(error));
}

// ✅ Use promises consistently
async function getData(): Promise<Data> {
  const response = await fetch("/api/data");
  return response.json();
}
```

## Practice Tasks

1. **Basic Promise**
   - Create a promise that resolves after 2 seconds
   - Use .then() to log the result

2. **Async Function**
   - Create an async function that fetches data
   - Use await and log the result

3. **Error Handling**
   - Create an async function with try/catch
   - Test both success and failure cases

4. **Multiple Async Operations**
   - Create two async functions
   - Use Promise.all() to run them in parallel

5. **Array of Promises**
   - Create a function that maps over an array
   - Use Promise.all() to wait for all

## Mini Project: Data Fetcher with Caching

Create a data fetcher with caching:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

class UserFetcher {
  private cache: Map<number, User> = new Map();

  async fetchUser(id: number): Promise<User> {
    // Check cache first
    if (this.cache.has(id)) {
      console.log(`Returning cached user ${id}`);
      return this.cache.get(id)!;
    }

    try {
      console.log(`Fetching user ${id}`);
      const response = await fetch(`/api/users/${id}`);

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }

      const user: User = await response.json();

      // Cache it
      this.cache.set(id, user);
      return user;
    } catch (error) {
      console.log(`Error fetching user ${id}:`, error);
      throw error;
    }
  }

  async fetchMultiple(ids: number[]): Promise<User[]> {
    const promises = ids.map((id) => this.fetchUser(id));
    return Promise.all(promises);
  }

  clearCache(): void {
    this.cache.clear();
  }
}

// Usage:
const fetcher = new UserFetcher();
const users = await fetcher.fetchMultiple([1, 2, 1, 3]);
// Second fetch of user 1 comes from cache
```

## Official TypeScript Docs

- **Async/Await**: https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-7.html#async-await
- **Promises**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- **Promise.all**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all

## Previous Topic

← [Modules and Namespaces](../11-modules-and-namespaces/README.md)

## Next Topic

→ [Error Handling](../13-error-handling/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
