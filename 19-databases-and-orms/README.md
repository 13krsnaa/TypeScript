# Databases and ORMs

## Why This Topic Matters

Type-safe database access prevents runtime errors. Essential for:

- **Query safety** - Typos in column names caught early
- **Type inference** - Get back proper types from queries
- **Migrations** - Schema changes tracked
- **Developer experience** - Auto-complete in IDE
- **Maintainability** - Changes propagate automatically

ORMs like Prisma, TypeORM bring TypeScript benefits to databases.

## Core Concept

Instead of raw SQL strings, use type-safe query builders:

```typescript
// ❌ Raw SQL - error-prone
const user = await db.query("SELECT * FROM users WHERE id = $1", [1]);
console.log(user.name); // What if 'name' doesn't exist in result?

// ✅ TypeScript ORM - type-safe
const user = await db.users.findUnique({ where: { id: 1 } });
console.log(user.name); // ✅ TypeScript knows 'name' exists
```

## Prisma Example

### Installation

```bash
npm install @prisma/client
npm install --save-dev prisma
npx prisma init
```

### Schema Definition

`prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
  posts Post[]

  @@map("users")
}

model Post {
  id      Int     @id @default(autoincrement())
  title   String
  content String
  userId  Int
  user    User    @relation(fields: [userId], references: [id])

  @@map("posts")
}
```

### Type-Safe Queries

```typescript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

// Get user - returns User type
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: { posts: true },
});

console.log(user?.name); // ✅ Type-safe
// console.log(user?.unknownField);  // ❌ ERROR

// Create user - request and response are typed
const newUser = await prisma.user.create({
  data: {
    name: "Alice",
    email: "alice@example.com",
  },
});

// Update user
const updated = await prisma.user.update({
  where: { id: 1 },
  data: { name: "Alice Updated" },
});

// Delete user
await prisma.user.delete({ where: { id: 1 } });

// Transactions
await prisma.$transaction(async (tx) => {
  const user = await tx.user.create({
    data: { name: "Bob", email: "bob@example.com" },
  });
  await tx.post.create({
    data: {
      title: "My Post",
      content: "Hello",
      userId: user.id,
    },
  });
});

await prisma.$disconnect();
```

## TypeORM Example

### Setup

```bash
npm install typeorm reflect-metadata
npm install --save-dev @types/node
```

### Entity Definition

```typescript
import { Entity, PrimaryGeneratedColumn, Column, OneToMany } from "typeorm";

@Entity()
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column({ unique: true })
  email: string;

  @OneToMany(() => Post, (post) => post.user)
  posts: Post[];
}

@Entity()
export class Post {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @Column()
  content: string;

  @Column()
  userId: number;

  @ManyToOne(() => User, (user) => user.posts)
  user: User;
}
```

### Type-Safe Operations

```typescript
import { getRepository } from "typeorm";
import { User, Post } from "./entities";

const userRepo = getRepository(User);

// Create
const newUser = await userRepo.save({
  name: "Alice",
  email: "alice@example.com",
});

// Read
const user = await userRepo.findOne({
  where: { id: 1 },
  relations: ["posts"],
});

// Update
await userRepo.update({ id: 1 }, { name: "Updated" });

// Delete
await userRepo.delete({ id: 1 });

// Query
const activeUsers = await userRepo.find({
  where: { active: true },
});
```

## Backend Service with TypeScript

```typescript
import { PrismaClient, User, Post } from "@prisma/client";

class UserService {
  private prisma: PrismaClient;

  constructor() {
    this.prisma = new PrismaClient();
  }

  async getAllUsers(): Promise<User[]> {
    return this.prisma.user.findMany();
  }

  async getUserById(id: number): Promise<User | null> {
    return this.prisma.user.findUnique({
      where: { id },
    });
  }

  async getUserWithPosts(id: number): Promise<User | null> {
    return this.prisma.user.findUnique({
      where: { id },
      include: { posts: true },
    });
  }

  async createUser(name: string, email: string): Promise<User> {
    return this.prisma.user.create({
      data: { name, email },
    });
  }

  async updateUser(id: number, data: Partial<User>): Promise<User> {
    return this.prisma.user.update({
      where: { id },
      data,
    });
  }

  async deleteUser(id: number): Promise<void> {
    await this.prisma.user.delete({ where: { id } });
  }

  async disconnect(): Promise<void> {
    await this.prisma.$disconnect();
  }
}

// Express routes using service
import express from "express";

const app = express();
const userService = new UserService();

app.get("/api/users", async (req, res) => {
  const users = await userService.getAllUsers();
  res.json(users);
});

app.get("/api/users/:id", async (req, res) => {
  const id = parseInt(req.params.id);
  const user = await userService.getUserById(id);
  if (user) {
    res.json(user);
  } else {
    res.status(404).json({ error: "User not found" });
  }
});
```

## Common Mistakes

### Mistake 1: Not Using TypeScript Features

```typescript
// ❌ Raw SQL query - no type safety
const result = await db.query("SELECT * FROM users WHERE id = $1", [1]);
console.log(result.rows[0].name);

// ✅ Use ORM for type safety
const user = await prisma.user.findUnique({ where: { id: 1 } });
console.log(user?.name);
```

### Mistake 2: Not Handling Null

```typescript
// ❌ Assuming result exists
const user = await prisma.user.findUnique({ where: { id: 999 } });
console.log(user.name); // ❌ ERROR: Object is possibly 'null'

// ✅ Check for null
const user = await prisma.user.findUnique({ where: { id: 999 } });
if (user) {
  console.log(user.name);
}
```

### Mistake 3: Forgetting to Disconnect

```typescript
// ❌ Connection leak
async function getUser() {
  const prisma = new PrismaClient();
  return prisma.user.findMany();
}

// ✅ Reuse client or disconnect
const prisma = new PrismaClient();

async function getUser() {
  return prisma.user.findMany();
}

// At app shutdown:
await prisma.$disconnect();
```

## Practice Tasks

1. **Define Schema**
   - Create Prisma schema with two models
   - Run migrations

2. **Create Service**
   - Create service class with CRUD methods
   - Type all methods properly

3. **API Routes**
   - Create Express routes using service
   - Type request/response

4. **Transactions**
   - Create multi-step operation in transaction
   - Handle errors

5. **Relationships**
   - Query with relations
   - Include nested data

## Mini Project: Blog with Database

`prisma/schema.prisma`:

```prisma
model User {
  id    Int     @id @default(autoincrement())
  name  String
  email String  @unique
  posts Post[]
}

model Post {
  id      Int     @id @default(autoincrement())
  title   String
  content String
  userId  Int
  user    User    @relation(fields: [userId], references: [id])
}
```

`src/services/blogService.ts`:

```typescript
import { PrismaClient } from "@prisma/client";

export class BlogService {
  private prisma: PrismaClient;

  constructor() {
    this.prisma = new PrismaClient();
  }

  async getAllPosts() {
    return this.prisma.post.findMany({
      include: { user: true },
    });
  }

  async getPost(id: number) {
    return this.prisma.post.findUnique({
      where: { id },
      include: { user: true },
    });
  }

  async createPost(title: string, content: string, userId: number) {
    return this.prisma.post.create({
      data: { title, content, userId },
      include: { user: true },
    });
  }

  async getUserPosts(userId: number) {
    return this.prisma.post.findMany({
      where: { userId },
      include: { user: true },
    });
  }
}
```

`src/routes/blog.ts`:

```typescript
import express, { Express, Request, Response } from "express";
import { BlogService } from "../services/blogService";

const router = express.Router();
const blogService = new BlogService();

router.get("/posts", async (req: Request, res: Response) => {
  const posts = await blogService.getAllPosts();
  res.json(posts);
});

router.get(
  "/posts/:id",
  async (req: Request<{ id: string }>, res: Response) => {
    const post = await blogService.getPost(parseInt(req.params.id));
    if (post) {
      res.json(post);
    } else {
      res.status(404).json({ error: "Post not found" });
    }
  },
);

router.post(
  "/posts",
  async (
    req: Request<{}, {}, { title: string; content: string; userId: number }>,
    res: Response,
  ) => {
    const post = await blogService.createPost(
      req.body.title,
      req.body.content,
      req.body.userId,
    );
    res.status(201).json(post);
  },
);

export default router;
```

## Official TypeScript Docs

- **Prisma**: https://www.prisma.io/docs/
- **Prisma TypeScript**: https://www.prisma.io/docs/orm/typescript
- **TypeORM**: https://typeorm.io/

## Previous Topic

← [API Typing and REST](../18-api-typing-and-rest/README.md)

## Next Topic

→ [React with TypeScript](../20-react-with-typescript/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
