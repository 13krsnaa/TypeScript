# API Typing and REST

## Why This Topic Matters

Type-safe APIs are the bridge between frontend and backend. Essential for:

- **Contract matching** - Frontend and backend agree on data shape
- **Auto-generated documentation** - Types serve as docs
- **Reduced bugs** - Data shape mismatches caught early
- **DX improvement** - IDE autocomplete for API responses
- **Refactoring safety** - Change API shape, errors appear everywhere

This is where TypeScript shines in full-stack development.

## Core Concept

Define types for API requests and responses:

```typescript
// Shared types (frontend and backend both import these)
export interface User {
  id: number;
  name: string;
  email: string;
}

// Backend:
app.get(
  "/api/users/:id",
  (req: Request<{ id: string }>, res: Response<User>) => {
    res.json(user);
  },
);

// Frontend:
const user: User = await fetch("/api/users/1").then((r) => r.json());
console.log(user.name); // Type-safe!
```

## Shared Type Files

Create `types/api.ts`:

```typescript
// User endpoints
export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
}

export interface CreateUserRequest {
  name: string;
  email: string;
  password: string;
}

export interface UpdateUserRequest {
  name?: string;
  email?: string;
}

// Post endpoints
export interface Post {
  id: number;
  title: string;
  content: string;
  userId: number;
  createdAt: string;
}

export interface CreatePostRequest {
  title: string;
  content: string;
}

// API responses
export interface ApiResponse<T> {
  status: number;
  data?: T;
  error?: string;
}

export interface PaginatedResponse<T> {
  items: T[];
  total: number;
  page: number;
  pageSize: number;
}
```

## Backend Implementation

### Type-Safe Routes

```typescript
import express, { Express, Request, Response } from "express";
import {
  User,
  CreateUserRequest,
  UpdateUserRequest,
  ApiResponse,
} from "../types/api";

const app: Express = express();
app.use(express.json());

// Mock database
const users: User[] = [];
let nextId = 1;

// GET /api/users
app.get("/api/users", (req: Request, res: Response<ApiResponse<User[]>>) => {
  res.json({ status: 200, data: users });
});

// POST /api/users
app.post(
  "/api/users",
  (
    req: Request<{}, {}, CreateUserRequest>,
    res: Response<ApiResponse<User>>,
  ) => {
    const user: User = {
      id: nextId++,
      name: req.body.name,
      email: req.body.email,
      createdAt: new Date().toISOString(),
    };
    users.push(user);
    res.status(201).json({ status: 201, data: user });
  },
);

// PUT /api/users/:id
app.put(
  "/api/users/:id",
  (req: Request<{ id: string }, {}, UpdateUserRequest>, res: Response) => {
    const user = users.find((u) => u.id === parseInt(req.params.id));
    if (!user) {
      return res.status(404).json({ status: 404, error: "User not found" });
    }

    if (req.body.name) user.name = req.body.name;
    if (req.body.email) user.email = req.body.email;

    res.json({ status: 200, data: user });
  },
);
```

## Frontend Implementation

### Fetch with Types

```typescript
import {
  User,
  CreateUserRequest,
  Post,
  CreatePostRequest,
  ApiResponse,
} from "../types/api";

class ApiClient {
  private baseUrl: string = "http://localhost:3000";

  async getUsers(): Promise<User[]> {
    const response = await fetch(`${this.baseUrl}/api/users`);
    const data: ApiResponse<User[]> = await response.json();
    return data.data || [];
  }

  async getUser(id: number): Promise<User> {
    const response = await fetch(`${this.baseUrl}/api/users/${id}`);
    if (!response.ok) {
      throw new Error("User not found");
    }
    const data: ApiResponse<User> = await response.json();
    return data.data!;
  }

  async createUser(request: CreateUserRequest): Promise<User> {
    const response = await fetch(`${this.baseUrl}/api/users`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(request),
    });
    const data: ApiResponse<User> = await response.json();
    return data.data!;
  }

  async getPosts(): Promise<Post[]> {
    const response = await fetch(`${this.baseUrl}/api/posts`);
    const data: ApiResponse<Post[]> = await response.json();
    return data.data || [];
  }

  async createPost(request: CreatePostRequest): Promise<Post> {
    const response = await fetch(`${this.baseUrl}/api/posts`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(request),
    });
    const data: ApiResponse<Post> = await response.json();
    return data.data!;
  }
}

// Usage:
const api = new ApiClient();

async function demo() {
  // Type-safe!
  const users = await api.getUsers();
  users.forEach((user) => {
    console.log(user.name); // ✅ TypeScript knows 'name' exists
    // console.log(user.password);  // ❌ ERROR: doesn't exist
  });

  // Create user with type checking
  const newUser = await api.createUser({
    name: "Alice",
    email: "alice@example.com",
    password: "secret",
  });
  console.log(newUser.id);
}
```

## React with API Types

### Using in Components

```typescript
import React, { useState, useEffect } from "react";
import { User, CreateUserRequest } from "../types/api";

const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState<boolean>(true);

  useEffect(() => {
    fetchUsers();
  }, []);

  async function fetchUsers(): Promise<void> {
    try {
      const response = await fetch("/api/users");
      const data = await response.json();
      setUsers(data.data || []);
    } catch (error) {
      console.error("Error fetching users:", error);
    } finally {
      setLoading(false);
    }
  }

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map(user => (
          <li key={user.id}>
            {user.name} ({user.email})
          </li>
        ))}
      </ul>
    </div>
  );
};

// Create user form
interface CreateUserFormProps {
  onSuccess: (user: User) => void;
}

const CreateUserForm: React.FC<CreateUserFormProps> = ({ onSuccess }) => {
  const [name, setName] = useState<string>("");
  const [email, setEmail] = useState<string>("");

  async function handleSubmit(e: React.FormEvent): Promise<void> {
    e.preventDefault();

    const request: CreateUserRequest = { name, email, password: "default" };

    try {
      const response = await fetch("/api/users", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(request)
      });
      const data = await response.json();
      onSuccess(data.data);
    } catch (error) {
      console.error("Error creating user:", error);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        value={email}
        onChange={e => setEmail(e.target.value)}
        placeholder="Email"
      />
      <button type="submit">Create User</button>
    </form>
  );
};
```

## Common Mistakes

### Mistake 1: Different Types on Frontend and Backend

```typescript
// ❌ Frontend and backend don't share types
// Backend:
interface User {
  id: number;
  name: string;
  createdAt: string;
}

// Frontend (different!):
interface User {
  id: number;
  name: string;
  created_at: string; // Different field name!
}

// ✅ Share types from a common file
import { User } from "@shared/types";
```

### Mistake 2: Not Handling API Errors

```typescript
// ❌ Assumes response is always successful
async function getUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  return response.json(); // Could fail!
}

// ✅ Check response status
async function getUser(id: number): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  const data = await response.json();
  return data.data;
}
```

### Mistake 3: Loose Request Types

```typescript
// ❌ Any object works
async function createUser(data: any): Promise<User> {
  const response = await fetch("/api/users", {
    method: "POST",
    body: JSON.stringify(data),
  });
  return response.json();
}

// ✅ Use specific request type
async function createUser(request: CreateUserRequest): Promise<User> {
  const response = await fetch("/api/users", {
    method: "POST",
    body: JSON.stringify(request),
  });
  return response.json();
}
```

## Practice Tasks

1. **Define API Types**
   - Create interfaces for requests and responses
   - Create error response type

2. **Backend Routes**
   - Use types in Express routes
   - Type request and response

3. **Frontend API Client**
   - Create methods using shared types
   - Handle errors

4. **React Component**
   - Use API types in React component
   - Fetch and display data

5. **Full Circle**
   - Create backend endpoint
   - Frontend fetches with types
   - Everything type-safe

## Mini Project: Blog API and Client

`types/api.ts`:

```typescript
export interface Post {
  id: number;
  title: string;
  content: string;
  author: string;
  createdAt: string;
}

export interface CreatePostRequest {
  title: string;
  content: string;
  author: string;
}

export interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
}
```

`backend/routes.ts`:

```typescript
import express, { Express, Request, Response } from "express";
import { Post, CreatePostRequest, ApiResponse } from "../types/api";

const app: Express = express();
app.use(express.json());

const posts: Post[] = [];
let nextId = 1;

app.get("/api/posts", (req: Request, res: Response<ApiResponse<Post[]>>) => {
  res.json({ success: true, data: posts });
});

app.post(
  "/api/posts",
  (
    req: Request<{}, {}, CreatePostRequest>,
    res: Response<ApiResponse<Post>>,
  ) => {
    const post: Post = {
      id: nextId++,
      ...req.body,
      createdAt: new Date().toISOString(),
    };
    posts.push(post);
    res.status(201).json({ success: true, data: post });
  },
);
```

`frontend/blog.tsx`:

```typescript
import React, { useState, useEffect } from "react";
import { Post, CreatePostRequest, ApiResponse } from "../types/api";

const BlogClient: React.FC = () => {
  const [posts, setPosts] = useState<Post[]>([]);

  useEffect(() => {
    fetch("/api/posts")
      .then(r => r.json())
      .then((data: ApiResponse<Post[]>) => setPosts(data.data || []));
  }, []);

  async function addPost(title: string, content: string): Promise<void> {
    const request: CreatePostRequest = {
      title,
      content,
      author: "Me"
    };

    const response = await fetch("/api/posts", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(request)
    });

    const data: ApiResponse<Post> = await response.json();
    if (data.data) {
      setPosts([...posts, data.data]);
    }
  }

  return (
    <div>
      <h1>Blog</h1>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.content}</p>
          <small>By {post.author}</small>
        </div>
      ))}
    </div>
  );
};
```

## Official TypeScript Docs

- **TypeScript with REST APIs**: https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-oop.html
- **Fetch API**: https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API

## Previous Topic

← [Node.js and Backend](../17-nodejs-and-backend/README.md)

## Next Topic

→ [Databases and ORMs](../19-databases-and-orms/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
