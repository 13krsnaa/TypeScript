# ⚙️ 12. tsconfig And Workflow

[← Previous: Utility Types](../11-utility-types/README.md) | [Back to Hub](../README.md)

> **Read Time**: 20 minutes  
> **Goal**: TypeScript project ka practical setup aur compiler workflow samajhna

---

## 🎯 What You'll Learn

1. `tsconfig.json` kya karta hai
2. Important compiler options ka meaning
3. Source aur output folder ka flow
4. Basic TypeScript workflow kaise chalti hai

---

## 🧠 `tsconfig.json` Kya Hota Hai?

`tsconfig.json` TypeScript project ka control center hota hai.

Ye decide karta hai:

- compiler ka behavior
- source files ka location
- output files ka location
- strictness rules

---

## 📌 Important Compiler Options

| Option | Meaning |
|--------|---------|
| `target` | Kaunsa JavaScript version output me chahiye |
| `module` | Import/export ka format |
| `rootDir` | Source code kahan pada hai |
| `outDir` | Compiled files kahan jayengi |
| `strict` | Strong type-checking rules on karta hai |
| `noImplicitAny` | Hidden `any` ko discourage karta hai |
| `esModuleInterop` | Module compatibility improve karta hai |

---

## 🧪 Example `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "CommonJS",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "noImplicitAny": true,
    "esModuleInterop": true
  },
  "include": ["src"]
}
```

---

## 🔍 Option Breakdown

| Setting | Beginner-Friendly Meaning |
|---------|---------------------------|
| `"rootDir": "./src"` | Tumhara original TypeScript code `src` me hai |
| `"outDir": "./dist"` | Compiled JavaScript `dist` me jayegi |
| `"strict": true` | Better habits aur safer typing |
| `"noImplicitAny": true` | Compiler chupke se `any` accept nahi karega |

---

## 📂 Project Flow

```text
project/
|-- src/
|   `-- index.ts
|-- dist/
|   `-- index.js
|-- package.json
`-- tsconfig.json
```

Flow:

```text
Write TypeScript in src/ -> Run compiler -> Get JavaScript in dist/
```

---

## ⚡ Basic Workflow Commands

### Install TypeScript

```bash
npm install -D typescript
```

### Create config

```bash
npx tsc --init
```

### Compile project

```bash
npx tsc
```

### Watch mode

```bash
npx tsc --watch
```

---

## 🆚 Beginner Mistake Vs Better Habit

| Weak Habit | Better Habit |
|------------|--------------|
| `strict` off rakhna | `strict` on rakho |
| Source aur output files mix karna | `src` aur `dist` separate rakho |
| Config copy-paste kar dena | Har option ka meaning samjho |

---

## 📝 Practice Tasks

1. Ek basic `tsconfig.json` socho ya banao jisme `rootDir` `src` aur `outDir` `dist` ho.
2. `strict: true` aur `noImplicitAny: true` ka meaning apne words me likho.
3. Ek chhota project structure draw karo aur batao compiled output kahan jayega.

---

## ✅ Quick Recap

- `tsconfig.json` TypeScript compiler ka main control file hai
- `strict` aur `noImplicitAny` beginner ke liye bahut useful rules hain
- Clean project workflow samajhna long-term developer habit ka part hai

---

[← Previous: Utility Types](../11-utility-types/README.md) | [Back to Hub](../README.md)
