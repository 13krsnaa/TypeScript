# 🔍 09. Type Narrowing

[← Previous: Generics](../08-generics/README.md) | [Back to Hub](../README.md) | [Next: Classes And Access Modifiers →](../10-classes-and-access-modifiers/README.md)

> **Read Time**: 20 minutes  
> **Goal**: Union types ke saath safe property access aur logic branching samajhna

---

## 🎯 What You'll Learn

1. Narrowing kya hota hai
2. `typeof` se narrowing kaise hoti hai
3. `in` operator ka use
4. Discriminated unions ka idea

---

## 🧠 Narrowing Kyu Zaruri Hai?

Agar ek value multiple types ki ho sakti hai, to TypeScript ko certainty chahiye hoti hai.

Example:

```ts
function printId(id: string | number) {
  // id could be string or number
}
```

Yahan TypeScript seedha `id.toUpperCase()` allow nahi karega, kyunki `id` number bhi ho sakta hai.

---

## 📌 Common Narrowing Tools

| Tool | Kab Use Karna Hai |
|------|-------------------|
| `typeof` | Primitive types check karne ke liye |
| `in` | Object property based check ke liye |
| Equality check | Specific literal value check ke liye |
| Discriminated union | Structured union objects ke liye |

---

## 🧪 Narrowing With `typeof`

```ts
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase());
  } else {
    console.log(id.toFixed(2));
  }
}
```

---

## 🧪 Narrowing With `in`

```ts
type Admin = { name: string; permissions: string[] };
type Customer = { name: string; purchaseCount: number };

function printUserInfo(user: Admin | Customer) {
  if ("permissions" in user) {
    console.log(user.permissions);
  } else {
    console.log(user.purchaseCount);
  }
}
```

---

## 🧪 Discriminated Union Example

```ts
type Payment =
  | { method: "card"; cardNumber: string }
  | { method: "upi"; upiId: string };

function processPayment(payment: Payment) {
  if (payment.method === "card") {
    console.log(`Card: ${payment.cardNumber}`);
  } else {
    console.log(`UPI: ${payment.upiId}`);
  }
}
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `string | number` | Value multiple types me se ek ho sakti hai |
| `typeof id === "string"` | TypeScript ko string case ka proof milta hai |
| `"permissions" in user` | Object type narrow hota hai |
| `payment.method === "card"` | Shared field se exact object shape identify hoti hai |

---

## ⚠️ Common Mistakes

1. Union type value ko direct specific method ke saath use karna.
2. Type narrowing ke baad bhi unnecessary assertions lagana.
3. Object unions me property existence check na karna.

---

## 📝 Practice Tasks

1. Ek function banao jo `string | number` input le aur type ke hisab se different output kare.
2. Do object types banao aur `in` operator se unko narrow karo.
3. Ek discriminated union banao for `success` and `error` API responses.

---

## ✅ Quick Recap

- Type narrowing union values ke saath safe coding ka core part hai
- `typeof`, `in`, aur discriminated unions bahut common tools hain
- Runtime checks TypeScript ko better type understanding dete hain

---

[← Previous: Generics](../08-generics/README.md) | [Next: Classes And Access Modifiers →](../10-classes-and-access-modifiers/README.md)
