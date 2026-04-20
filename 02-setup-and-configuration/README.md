# Setup and Configuration

## Why This Topic Matters

Before you can start writing TypeScript, you need to set it up correctly. Proper configuration ensures:

- Your code compiles correctly to JavaScript
- Your IDE provides accurate type checking and autocomplete
- Your build process works smoothly
- Your team follows the same rules

Misconfigured TypeScript causes mysterious errors that waste hours of debugging.

## Core Concept

TypeScript requires two main things:

1. **TypeScript Compiler** - Converts `.ts` files to `.js` files
2. **Configuration File** - Tells the compiler how to behave (`tsconfig.json`)

```
.ts files (TypeScript)
     ↓
TypeScript Compiler (reads tsconfig.json)
     ↓
.js files (JavaScript)
     ↓
Browser / Node.js
```

## Installation

### Step 1: Install Node.js

TypeScript runs on Node.js. Download from https://nodejs.org/

### Step 2: Create a Project

```bash
mkdir my-typescript-project
cd my-typescript-project
npm init -y
```

### Step 3: Install TypeScript

```bash
npm install --save-dev typescript
```

### Step 4: Create tsconfig.json

```bash
npx tsc --init
```

This creates a `tsconfig.json` file with default settings.

## Understanding tsconfig.json

Here's a **beginner-friendly configuration**:

```json
{
  "compilerOptions": {
    "target": "ES2020",                  // Output JavaScript version
    "module": "commonjs",                // Module system
    "lib": ["ES2020"],                   // Available APIs
    "outDir": "./dist",                  // Output folder
    "rootDir": "./src",                  // Source folder
    "strict": true,                      // Enable all strict type checks
    "esModuleInterop": true,             // Better CommonJS compatibility
    "skipLibCheck": true,                // Skip type checking .d.ts files
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src"],                    // Files to compile
  "exclude": ["node_modules", "dist"]    // Files to ignore
}
```

### Key Options Explained

| Option | Default | Meaning |
|--------|---------|---------|
| `target` | `ES2020` | What JavaScript version to compile to |
| `module` | `commonjs` | Module system (use "es2020" for modern imports) |
| `outDir` | `./dist` | Where to put compiled JavaScript |
| `rootDir` | `./src` | Where source TypeScript files are |
| `strict` | `true` | Enable strict type checking (RECOMMENDED) |
| `lib` | `ES2020` | What browser/Node APIs are available |

## Project Structure

Here's a recommended folder structure:

```
my-typescript-project/
├── src/
│   ├── index.ts
│   ├── utils/
│   │   └── helpers.ts
│   └── types/
│       └── User.ts
├── dist/
│   ├── index.js
│   └── utils/
│       └── helpers.js
├── tsconfig.json
├── package.json
└── package-lock.json
```

## Examples

### Example 1: Your First TypeScript File

Create `src/index.ts`:

```typescript
function greet(name: string): void {
  console.log(`Hello, ${name}!`);
}

greet("TypeScript");
```

### Example 2: Compile TypeScript

```bash
# Compile a single file
npx tsc src/index.ts

# Compile all files (uses tsconfig.json)
npx tsc

# Watch mode - recompile on file changes
npx tsc --watch
```

### Example 3: Add NPM Scripts

Edit `package.json`:

```json
{
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "start": "node dist/index.js"
  }
}
```

Now use:

```bash
npm run build   # Compile TypeScript
npm run dev     # Watch mode
npm start       # Run compiled JavaScript
```

## Common Mistakes

### Mistake 1: Not Setting `strict` Mode

```json
{
  "compilerOptions": {
    "strict": false  // ❌ Removes type safety
  }
}
```

**Why this is wrong**: Without `strict: true`, TypeScript is too lenient and doesn't catch errors.

**Solution**: Always use `"strict": true`.

### Mistake 2: Wrong Output Directory

```bash
# ❌ Compiling to root instead of dist/
npx tsc --outDir ./

# ✅ Use tsconfig.json with proper outDir
npx tsc
```

### Mistake 3: Forgetting to Compile

```bash
# ❌ Running TypeScript directly (doesn't work)
node src/index.ts

# ✅ Compile first, then run JavaScript
npm run build
npm start
```

### Mistake 4: Mixed ES Modules and CommonJS

```json
{
  "compilerOptions": {
    "module": "commonjs"
  }
}
```

```typescript
// ❌ This mixes systems
import express from "express";  // ES Modules syntax
module.exports = app;           // CommonJS syntax

// ✅ Use one or the other
import express from "express";
export default app;
```

## Practice Tasks

1. **Initialize a TypeScript Project**
   - Create a new folder
   - Run `npm init -y`
   - Install TypeScript
   - Initialize `tsconfig.json`

2. **Create Your First File**
   - Create `src/hello.ts`
   - Write a function that prints "Hello, TypeScript"
   - Compile and run it

3. **Modify tsconfig.json**
   - Change `outDir` to `./build`
   - Change `target` to `ES2015`
   - Compile and check the output

4. **Add NPM Scripts**
   - Add `build` script to compile TypeScript
   - Add `start` script to run the compiled code
   - Test both scripts

5. **Organize Code**
   - Create `src/types/User.ts` with a User interface
   - Create `src/utils/helpers.ts` with a utility function
   - Import and use them in `src/index.ts`

## Mini Project: Setup a Node.js Backend Project

Create a TypeScript Node.js project:

```bash
mkdir ts-backend
cd ts-backend
npm init -y
npm install --save-dev typescript @types/node
npx tsc --init
```

Edit `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "resolveJsonModule": true,
    "esModuleInterop": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

Create `src/index.ts`:

```typescript
interface Config {
  port: number;
  host: string;
}

const config: Config = {
  port: 3000,
  host: "localhost"
};

console.log(`Server running on ${config.host}:${config.port}`);
```

Run:

```bash
npm run build
npm start
```

## Official TypeScript Docs

- **tsconfig.json Reference**: https://www.typescriptlang.org/tsconfig/
- **Compiler Options**: https://www.typescriptlang.org/docs/handbook/compiler-options.html
- **Getting Started**: https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html

## Previous Topic

← [Introduction to TypeScript](../01-introduction/README.md)

## Next Topic

→ [Basic Types](../03-basic-types/README.md)

---

*Last Updated: 2024 | TypeScript Version: 5.0+*
