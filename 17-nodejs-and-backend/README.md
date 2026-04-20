# Node.js and Backend

## Why This Topic Matters

TypeScript on the backend ensures:

- **API type safety** - Routes, handlers, middleware with types
- **Database integration** - Type-safe queries
- **Server stability** - Catch errors before production
- **Code reusability** - Share types with frontend
- **Team productivity** - Clear interfaces between services

Backend TypeScript prevents runtime errors that affect production.

## Core Concept

Node.js TypeScript uses:

```typescript
import express, { Express, Request, Response } from "express";

const app: Express = express();

app.get("/api/users/:id", (req: Request, res: Response) => {
  const userId: string = req.params.id;
  res.json({ id: userId });
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

## Setting Up Node.js TypeScript

### Installation

```bash
mkdir my-backend
cd my-backend
npm init -y
npm install express
npm install --save-dev typescript @types/node @types/express
npx tsc --init
```

### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

### package.json Scripts

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "ts-node src/index.ts"
  }
}
```

## Examples

### Example 1: Basic Express Server

```typescript
import express, { Express, Request, Response } from "express";

const app: Express = express();
const PORT: number = 3000;

app.use(express.json());

app.get("/", (req: Request, res: Response) => {
  res.send("Hello, TypeScript!");
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Example 2: Type-Safe Routes

```typescript
import express, { Express, Request, Response } from "express";

interface User {
  id: number;
  name: string;
  email: string;
}

const app: Express = express();
app.use(express.json());

// Mock database
const users: User[] = [
  { id: 1, name: "Alice", email: "alice@example.com" },
  { id: 2, name: "Bob", email: "bob@example.com" },
];

// GET /api/users
app.get("/api/users", (req: Request, res: Response<User[]>) => {
  res.json(users);
});

// GET /api/users/:id
app.get("/api/users/:id", (req: Request<{ id: string }>, res: Response) => {
  const id: number = parseInt(req.params.id);
  const user: User | undefined = users.find((u) => u.id === id);

  if (user) {
    res.json(user);
  } else {
    res.status(404).json({ error: "User not found" });
  }
});

// POST /api/users
app.post("/api/users", (req: Request<{}, {}, User>, res: Response) => {
  const newUser: User = {
    id: users.length + 1,
    ...req.body,
  };
  users.push(newUser);
  res.status(201).json(newUser);
});

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

### Example 3: Middleware

```typescript
import express, { Express, Request, Response, NextFunction } from "express";

const app: Express = express();

// Logging middleware
app.use((req: Request, res: Response, next: NextFunction) => {
  console.log(`${req.method} ${req.path}`);
  next();
});

// Authentication middleware
interface AuthenticatedRequest extends Request {
  userId?: number;
}

function authMiddleware(
  req: AuthenticatedRequest,
  res: Response,
  next: NextFunction,
) {
  const token: string | undefined = req.headers.authorization;

  if (token) {
    // Verify token (simplified)
    req.userId = 1;
    next();
  } else {
    res.status(401).json({ error: "Unauthorized" });
  }
}

// Protected route
app.get(
  "/api/profile",
  authMiddleware,
  (req: AuthenticatedRequest, res: Response) => {
    res.json({ userId: req.userId });
  },
);

app.listen(3000);
```

### Example 4: Error Handling

```typescript
import express, { Express, Request, Response, NextFunction } from "express";

const app: Express = express();

class ApiError extends Error {
  constructor(
    public statusCode: number,
    message: string,
  ) {
    super(message);
    this.name = "ApiError";
  }
}

// Route that throws
app.get("/api/users/:id", (req: Request, res: Response, next: NextFunction) => {
  try {
    const id: number = parseInt(req.params.id);

    if (isNaN(id)) {
      throw new ApiError(400, "Invalid user ID");
    }

    res.json({ id });
  } catch (error) {
    next(error);
  }
});

// Error handling middleware
app.use(
  (
    error: Error | ApiError,
    req: Request,
    res: Response,
    next: NextFunction,
  ) => {
    if (error instanceof ApiError) {
      res.status(error.statusCode).json({ error: error.message });
    } else {
      res.status(500).json({ error: "Internal server error" });
    }
  },
);

app.listen(3000);
```

### Example 5: Environment Variables

```typescript
import dotenv from "dotenv";

// Load .env file
dotenv.config();

interface Config {
  port: number;
  databaseUrl: string;
  apiSecret: string;
  nodeEnv: "development" | "production" | "test";
}

function loadConfig(): Config {
  const config: Config = {
    port: parseInt(process.env.PORT || "3000", 10),
    databaseUrl: process.env.DATABASE_URL || "sqlite:db.sqlite",
    apiSecret: process.env.API_SECRET || "secret",
    nodeEnv: (process.env.NODE_ENV as any) || "development",
  };

  // Validate required variables
  if (!config.databaseUrl) {
    throw new Error("DATABASE_URL is required");
  }

  return config;
}

const config = loadConfig();
console.log(`Starting server on port ${config.port}`);
```

## Common Mistakes

### Mistake 1: No Type for Request Body

```typescript
// ❌ req.body is any
app.post("/api/users", (req: Request, res: Response) => {
  console.log(req.body.unknownField); // No error, but might crash
});

// ✅ Type the body
interface CreateUserBody {
  name: string;
  email: string;
}

app.post(
  "/api/users",
  (req: Request<{}, {}, CreateUserBody>, res: Response) => {
    console.log(req.body.name); // Type-safe
  },
);
```

### Mistake 2: Uncaught Promise Rejections

```typescript
// ❌ Async error not caught
app.get("/api/data", async (req: Request, res: Response) => {
  const data = await fetchDatabase(); // Could throw!
  res.json(data);
});

// ✅ Wrap in try/catch or use async handler
app.get(
  "/api/data",
  async (req: Request, res: Response, next: NextFunction) => {
    try {
      const data = await fetchDatabase();
      res.json(data);
    } catch (error) {
      next(error);
    }
  },
);
```

### Mistake 3: Missing Response Type

```typescript
// ❌ Response type unknown
app.get("/api/users", (req: Request, res: Response) => {
  res.json(users); // What type is users?
});

// ✅ Explicit response type
interface ApiResponse<T> {
  status: "success" | "error";
  data: T;
}

app.get("/api/users", (req: Request, res: Response<ApiResponse<User[]>>) => {
  res.json({ status: "success", data: users });
});
```

## Practice Tasks

1. **Setup Express Server**
   - Install Express and types
   - Create a basic server
   - Add a few routes

2. **Type-Safe Routes**
   - Create routes with request/response types
   - Add request body types

3. **Middleware**
   - Create logging middleware
   - Create authentication middleware

4. **Error Handling**
   - Create custom error class
   - Add error handling middleware

5. **Environment Variables**
   - Load .env file
   - Type configuration

## Mini Project: REST API

```typescript
import express, { Express, Request, Response, NextFunction } from "express";

interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
}

const app: Express = express();
app.use(express.json());

const todos: Todo[] = [];
let nextId = 1;

// GET all todos
app.get("/api/todos", (req: Request, res: Response<ApiResponse<Todo[]>>) => {
  res.json({ success: true, data: todos });
});

// GET todo by id
app.get("/api/todos/:id", (req: Request<{ id: string }>, res: Response) => {
  const id = parseInt(req.params.id);
  const todo = todos.find((t) => t.id === id);

  if (todo) {
    res.json({ success: true, data: todo });
  } else {
    res.status(404).json({ success: false, error: "Todo not found" });
  }
});

// POST create todo
app.post(
  "/api/todos",
  (req: Request<{}, {}, { title: string }>, res: Response) => {
    const todo: Todo = {
      id: nextId++,
      title: req.body.title,
      completed: false,
    };
    todos.push(todo);
    res.status(201).json({ success: true, data: todo });
  },
);

// PUT update todo
app.put(
  "/api/todos/:id",
  (req: Request<{ id: string }, {}, Partial<Todo>>, res: Response) => {
    const id = parseInt(req.params.id);
    const todo = todos.find((t) => t.id === id);

    if (todo) {
      if (req.body.title) todo.title = req.body.title;
      if (req.body.completed !== undefined) todo.completed = req.body.completed;
      res.json({ success: true, data: todo });
    } else {
      res.status(404).json({ success: false, error: "Todo not found" });
    }
  },
);

// DELETE todo
app.delete("/api/todos/:id", (req: Request<{ id: string }>, res: Response) => {
  const id = parseInt(req.params.id);
  const index = todos.findIndex((t) => t.id === id);

  if (index !== -1) {
    const deleted = todos.splice(index, 1);
    res.json({ success: true, data: deleted[0] });
  } else {
    res.status(404).json({ success: false, error: "Todo not found" });
  }
});

app.listen(3000, () => {
  console.log("API running on port 3000");
});
```

## Official TypeScript Docs

- **Node.js Types**: https://www.npmjs.com/package/@types/node
- **Express Types**: https://www.npmjs.com/package/@types/express
- **TypeScript with Express**: https://expressjs.com/en/resources/middleware/typescript.html

## Previous Topic

← [DOM and Frontend](../16-dom-and-frontend/README.md)

## Next Topic

→ [API Typing and REST](../18-api-typing-and-rest/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
