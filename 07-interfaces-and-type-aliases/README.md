# 🏗️ 07. Interfaces And Type Aliases

[← Previous: Unions, Literal Types, Enums](../06-unions-literals-enums/README.md) | [Back to Hub](../README.md) | [Next: Generics →](../08-generics/README.md)

> **Read Time**: 20-22 minutes  
> **Goal**: Reusable types banana aur object structures ko clean tarike se organize karna

---

## 🎯 What You'll Learn

1. `interface` kya hota hai
2. `type` alias kya hota hai
3. Dono me difference kya hai
4. Reusable structure banana kyu important hai

---

## 🧠 Problem Statement

Agar tum bar bar same object shape likhoge, to:

- code lengthy ho jayega
- typo ka chance badhega
- maintenance difficult hogi

Yahin `interface` aur `type alias` useful hote hain.

---

## 📌 Quick Comparison Table

| Feature | Interface | Type Alias |
|---------|-----------|------------|
| Object shapes | ✅ Best fit | ✅ Possible |
| Union types | ❌ | ✅ |
| Tuple types | ❌ | ✅ |
| Extend karna | ✅ Very common | ✅ Possible |
| Beginner use case | Models / object contracts | Flexible type definitions |

---

## 🧪 Interface Example

```ts
interface User {
  id: number;
  name: string;
  email: string;
}
```

---

## 🧪 Type Alias Example

```ts
type Status = "loading" | "success" | "error";
```

---

## 🧪 Real Example

```ts
interface Product {
  id: number;
  title: string;
  price: number;
}

type ApiStatus = "idle" | "loading" | "success" | "error";

const product: Product = {
  id: 1,
  title: "Mouse",
  price: 799
};

let apiStatus: ApiStatus = "loading";

console.log(product.title);
console.log(apiStatus);
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `interface Product` | Reusable object contract |
| `type ApiStatus = ...` | Fixed string values ka reusable type |
| `const product: Product` | Product object ko defined structure follow karna hoga |
| `let apiStatus: ApiStatus` | Variable sirf allowed status values le sakta hai |

---

## 🧪 Extending Interface

```ts
interface Person {
  name: string;
}

interface Employee extends Person {
  employeeId: number;
}
```

Yahan `Employee` ne `Person` ke features inherit kiye.

---

## 🧠 Kab Kya Use Karein?

| Situation | Best Choice |
|-----------|-------------|
| Object model banana hai | `interface` |
| Union ya tuple banana hai | `type` |
| API response ke exact states define karne hain | `type` |
| Large object contracts aur extension chahiye | `interface` |

---

## ⚠️ Common Mistakes

1. Reusable object ko inline type ke saath hi likhte rehna.
2. Interface aur type ko magic samajhna without understanding.
3. Object properties miss kar dena aur expect karna ki code phir bhi safe rahega.

---

## 📝 Practice Tasks

1. Ek `Student` interface banao jisme `name`, `rollNumber`, aur `isPresent` ho.
2. Ek `ResultStatus` type banao jo `"pass"` ya `"fail"` allow kare.
3. Ek `Teacher` interface banao jo kisi base `Person` interface ko extend kare.

---

## ✅ Quick Recap

- Interface object contracts ke liye bahut useful hai
- Type alias broader use-cases handle karta hai
- Reusable types code ko cleaner aur maintainable banate hain

---

[← Previous: Unions, Literal Types, Enums](../06-unions-literals-enums/README.md) | [Next: Generics →](../08-generics/README.md)
