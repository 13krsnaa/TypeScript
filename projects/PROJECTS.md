# TypeScript Real-World Projects

Practical project ideas to practice TypeScript from beginner to advanced.

---

## Beginner Projects

### 1. Todo List Application

```typescript
// types/Todo.ts
export interface Todo {
  id: number;
  title: string;
  completed: boolean;
  createdAt: Date;
}

// services/TodoService.ts
export class TodoService {
  private todos: Todo[] = [];
  private nextId = 1;

  addTodo(title: string): Todo {
    const todo: Todo = {
      id: this.nextId++,
      title,
      completed: false,
      createdAt: new Date(),
    };
    this.todos.push(todo);
    return todo;
  }

  toggleTodo(id: number): void {
    const todo = this.todos.find(t => t.id === id);
    if (todo) todo.completed = !todo.completed;
  }

  getTodos(): Todo[] {
    return this.todos;
  }
}
```

### 2. Weather App

```typescript
// API integration with proper typing
interface WeatherData {
  temperature: number;
  humidity: number;
  description: string;
}

async function getWeather(city: string): Promise<WeatherData> {
  const response = await fetch(`https://api.weather.com/city/${city}`);
  const data = await response.json();
  return {
    temperature: data.main.temp,
    humidity: data.main.humidity,
    description: data.weather[0].description,
  };
}
```

### 3. Note-Taking App

```typescript
// Combine interfaces and classes
interface Note {
  id: string;
  title: string;
  content: string;
  tags: string[];
  created: Date;
  modified: Date;
}

class NoteManager {
  private notes = new Map<string, Note>();

  createNote(title: string, content: string): Note {
    const id = crypto.randomUUID();
    const note: Note = {
      id,
      title,
      content,
      tags: [],
      created: new Date(),
      modified: new Date(),
    };
    this.notes.set(id, note);
    return note;
  }

  searchByTag(tag: string): Note[] {
    return Array.from(this.notes.values()).filter(n =>
      n.tags.includes(tag)
    );
  }
}
```

---

## Intermediate Projects

### 4. Express REST API

```typescript
// middleware/errorHandler.ts
import { Request, Response, NextFunction } from "express";

class AppError extends Error {
  constructor(message: string, public statusCode: number) {
    super(message);
  }
}

function errorHandler(
  err: AppError,
  req: Request,
  res: Response,
  next: NextFunction
) {
  res.status(err.statusCode).json({
    status: "error",
    message: err.message,
  });
}

// routes/users.ts
import express from "express";

const router = express.Router();

router.get("/:id", async (req: Request, res: Response) => {
  try {
    const user = await getUserById(parseInt(req.params.id));
    if (!user) throw new AppError("User not found", 404);
    res.json(user);
  } catch (error) {
    throw error;
  }
});

export default router;
```

### 5. React Component Library

```typescript
// components/Button.tsx
import React, { FC, ButtonHTMLAttributes } from "react";

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: "primary" | "secondary";
  size?: "small" | "medium" | "large";
  loading?: boolean;
}

const Button: FC<ButtonProps> = ({
  variant = "primary",
  size = "medium",
  loading = false,
  children,
  ...props
}) => {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={loading}
      {...props}
    >
      {loading ? "Loading..." : children}
    </button>
  );
};

export default Button;
```

### 6. CLI Tool

```typescript
// commands/generate.ts
import { Command } from "commander";
import fs from "fs/promises";

interface FileConfig {
  name: string;
  template: "component" | "service" | "interface";
  path: string;
}

export const generateCommand = new Command("generate")
  .description("Generate new files")
  .option("-t, --template <type>", "Template type")
  .option("-p, --path <path>", "Output path")
  .action(async (options: { template: string; path: string }) => {
    const config: FileConfig = {
      name: "NewFile",
      template: options.template as any,
      path: options.path,
    };
    // Generate file logic
  });
```

---

## Advanced Projects

### 7. Real-Time Chat Application

```typescript
// WebSocket server with TypeScript
import { WebSocketServer } from "ws";

interface Message {
  type: "text" | "image" | "typing";
  sender: string;
  content: string;
  timestamp: Date;
}

interface User {
  id: string;
  name: string;
  status: "online" | "offline";
}

class ChatServer {
  private wss = new WebSocketServer({ port: 8080 });
  private users = new Map<string, User>();
  private messages: Message[] = [];

  constructor() {
    this.wss.on("connection", ws => {
      ws.on("message", (data: string) => {
        const message: Message = JSON.parse(data);
        this.handleMessage(message);
      });
    });
  }

  private handleMessage(message: Message): void {
    this.messages.push(message);
    this.broadcast(message);
  }

  private broadcast(message: Message): void {
    this.wss.clients.forEach(client => {
      if (client.readyState === 1) {
        client.send(JSON.stringify(message));
      }
    });
  }
}
```

### 8. Database ORM

```typescript
// Generic repository pattern
interface Repository<T> {
  create(data: Omit<T, "id">): Promise<T>;
  findById(id: number): Promise<T | null>;
  update(id: number, data: Partial<T>): Promise<T>;
  delete(id: number): Promise<void>;
  findAll(): Promise<T[]>;
}

abstract class BaseRepository<T> implements Repository<T> {
  constructor(protected tableName: string) {}

  async create(data: Omit<T, "id">): Promise<T> {
    // Implementation
    return {} as T;
  }

  async findById(id: number): Promise<T | null> {
    // Implementation
    return null;
  }

  async update(id: number, data: Partial<T>): Promise<T> {
    // Implementation
    return {} as T;
  }

  async delete(id: number): Promise<void> {
    // Implementation
  }

  async findAll(): Promise<T[]> {
    // Implementation
    return [];
  }
}
```

### 9. GraphQL Server

```typescript
// GraphQL schema with TypeScript
import { buildSchema } from "graphql";

interface User {
  id: string;
  name: string;
  email: string;
}

const schema = buildSchema(`
  type User {
    id: ID!
    name: String!
    email: String!
  }

  type Query {
    user(id: ID!): User
    users: [User]
  }

  type Mutation {
    createUser(name: String!, email: String!): User
  }
`);

interface RootValue {
  user: (args: { id: string }) => User | null;
  users: () => User[];
  createUser: (args: { name: string; email: string }) => User;
}

const rootValue: RootValue = {
  user: ({ id }) => users.find(u => u.id === id) || null,
  users: () => users,
  createUser: ({ name, email }) => {
    const user: User = { id: String(Date.now()), name, email };
    users.push(user);
    return user;
  },
};
```

### 10. Monorepo with Monorepo Tools

```typescript
// Structure for shared packages
packages/
  shared/
    src/
      types/
        common.ts
      utilities/
        validators.ts
        formatters.ts
  api/
    src/
      server.ts
  web/
    src/
      App.tsx

// tsconfig.json with path mapping
{
  "compilerOptions": {
    "paths": {
      "@shared/*": ["../shared/src/*"],
      "@api/*": ["../api/src/*"],
      "@web/*": ["../web/src/*"]
    }
  }
}

// Usage
import { User } from "@shared/types/common";
import { validateEmail } from "@shared/utilities/validators";
```

---

## Project Progression Path

**Beginner (1-3):** Learn syntax, types, classes
- Focus: Basic TypeScript features
- Complexity: Low
- Estimated time: 1-2 weeks each

**Intermediate (4-6):** Apply patterns, integrate libraries
- Focus: Real frameworks and patterns
- Complexity: Medium
- Estimated time: 2-3 weeks each

**Advanced (7-10):** Architecture, scalability, optimization
- Focus: Advanced patterns and architecture
- Complexity: High
- Estimated time: 4+ weeks each

---

## Resources for Each Project

- **Documentation:** Official libraries and frameworks
- **Testing:** Jest, Vitest, Mocha
- **Linting:** ESLint with TypeScript support
- **Build:** Webpack, Vite, esbuild
- **Deployment:** Docker, GitHub Actions, Vercel

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
