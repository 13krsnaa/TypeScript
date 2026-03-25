# 📦 05. Objects, Arrays, Tuples

[← Previous: Functions](../04-functions/README.md) | [Back to Hub](../README.md) | [Next: Unions, Literal Types, Enums →](../06-unions-literals-enums/README.md)

> **Read Time**: 20 minutes  
> **Goal**: Structured data ko TypeScript me strongly type karna

---

## 🎯 What You'll Learn

1. Object shape kaise define karte hain
2. Typed arrays ka syntax
3. Tuples kya hote hain
4. Real data structure ko readable aur safe kaise banate hain

---

## 🧠 Ye Topic Important Kyu Hai?

Real applications me data zyadatar:

- objects me hota hai
- arrays me hota hai
- kabhi fixed-position tuple form me hota hai

TypeScript ki real strength yahin shine karti hai.

---

## 📌 Quick Reference Table

| Structure | Use Case | Example |
|-----------|----------|---------|
| Object | Related properties ko group karna | user, product, invoice |
| Array | Same type ki list | skills, marks, tags |
| Tuple | Fixed order + fixed length data | `[name, age]` |

---

## 🧪 Object Example

```ts
let user: { name: string; age: number; isAdmin: boolean } = {
  name: "Aman",
  age: 22,
  isAdmin: false
};
```

Yahan object ka exact shape clear hai.

---

## 🧪 Array Example

```ts
let skills: string[] = ["HTML", "CSS", "TypeScript"];
let marks: number[] = [78, 85, 92];
```

`string[]` ka matlab:

```text
Is array ke har item ka type string hona chahiye
```

---

## 🧪 Tuple Example

```ts
let userRecord: [string, number] = ["Aman", 22];
```

Yahan:

- first value `string`
- second value `number`

Order important hai.

---

## 🧪 Combined Real Example

```ts
let product: {
  id: number;
  name: string;
  tags: string[];
  rating: [number, string];
} = {
  id: 101,
  name: "Keyboard",
  tags: ["electronics", "office"],
  rating: [5, "Excellent"]
};

console.log(product.name);
console.log(product.tags[0]);
console.log(product.rating[1]);
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `id: number` | Product id number hoga |
| `tags: string[]` | Tags sirf string values ki list hogi |
| `rating: [number, string]` | Pehle number, phir string |
| `product.tags[0]` | Array ke first item ko access karna |

---

## 🆚 JavaScript Vs TypeScript

JavaScript me object ka shape mostly tumhare dimaag ya comments me hota hai.
TypeScript me wo directly code me hota hai.

Ye especially team projects me bahut useful hota hai.

---

## ⚠️ Common Mistakes

| Mistake | Problem |
|---------|---------|
| Object property miss kar dena | Type mismatch ya error |
| Array me mixed random types daal dena | Data structure unclear ho jata hai |
| Tuple ka order galat rakh dena | Contract break ho jata hai |

---

## 📝 Practice Tasks

1. Ek `student` object banao jisme `name`, `className`, aur `rollNumber` ho.
2. Ek `fruits` array banao jo sirf `string` values accept kare.
3. Ek tuple banao jo `[string, boolean]` format follow kare.

---

## ✅ Quick Recap

- Objects shape define karte hain
- Arrays same type ke multiple items store karte hain
- Tuples fixed structure ke liye useful hote hain

---

[← Previous: Functions](../04-functions/README.md) | [Next: Unions, Literal Types, Enums →](../06-unions-literals-enums/README.md)
