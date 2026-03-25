# 🔤 02. Basic Types

[← Previous: Why TypeScript And Setup](../01-why-typescript-and-setup/README.md) | [Back to Hub](../README.md) | [Next: Inference, Any, Unknown, Never →](../03-inference-any-unknown-never/README.md)

> **Read Time**: 15 minutes  
> **Goal**: TypeScript ke sabse basic data types ko confidently samajhna

---

## 🎯 What You'll Learn

Is topic me tum ye cover karoge:

1. `string`, `number`, `boolean`
2. `null` aur `undefined`
3. Type annotations ka basic syntax
4. Common mistakes jo beginners karte hain

---

## 🧠 Basic Types Kya Hote Hain?

TypeScript me hum variable ke saath uska type define kar sakte hain.

| Type | Kis ke liye use hota hai | Example |
|------|--------------------------|---------|
| `string` | Text values | `"Aman"` |
| `number` | Numeric values | `25`, `99.5` |
| `boolean` | True/false values | `true` |
| `null` | Intentionally empty value | `null` |
| `undefined` | Value abhi assigned nahi hai | `undefined` |

---

## ⚡ Syntax Samjho

```ts
let userName: string = "Aman";
let age: number = 22;
let isLoggedIn: boolean = true;
```

Formula:

```text
variableName: type = value
```

---

## 🧪 Example

```ts
let courseName: string = "TypeScript Basics";
let lessonsCount: number = 12;
let isFree: boolean = false;
let emptyValue: null = null;
let notAssigned: undefined = undefined;

console.log(courseName);
console.log(lessonsCount);
console.log(isFree);
console.log(emptyValue);
console.log(notAssigned);
```

---

## 🔍 Code Breakdown

| Variable | Type | Meaning |
|----------|------|---------|
| `courseName` | `string` | Text store karta hai |
| `lessonsCount` | `number` | Numeric count store karta hai |
| `isFree` | `boolean` | True ya false store karta hai |
| `emptyValue` | `null` | Intentional empty value |
| `notAssigned` | `undefined` | Value currently defined nahi hai |

---

## 🆚 JavaScript Vs TypeScript

### JavaScript

```js
let courseName = "TypeScript Basics";
let lessonsCount = 12;
let isFree = false;
```

### TypeScript

```ts
let courseName: string = "TypeScript Basics";
let lessonsCount: number = 12;
let isFree: boolean = false;
```

TypeScript version me tum clearly bata rahe ho ki variable kis type ka hai.

---

## 📌 Important Note

TypeScript me `number` hi use hota hai.
Separate `int` aur `float` types nahi hote.

---

## ⚠️ Common Mistakes

| Mistake | Problem |
|---------|---------|
| `"25"` ko `number` samajhna | Wo `string` hai |
| `"true"` ko boolean samajhna | Wo text hai, boolean nahi |
| `string` aur `String` ko mix karna | Beginner confusion create hota hai |
| `null` aur `undefined` ko same samajhna | Dono alag concepts hain |

---

## 📝 Practice Tasks

1. `city` naam ka variable banao jiska type `string` ho.
2. `totalMarks` naam ka variable banao jiska type `number` ho.
3. `hasPassed` naam ka variable banao jiska type `boolean` ho.

---

## ✅ Quick Recap

- Basic types TypeScript ki foundation hain
- `string`, `number`, `boolean` sabse zyada use honge
- Sahi type choose karna readable aur safe code likhne ki habit banata hai

---

[← Previous: Why TypeScript And Setup](../01-why-typescript-and-setup/README.md) | [Next: Inference, Any, Unknown, Never →](../03-inference-any-unknown-never/README.md)
