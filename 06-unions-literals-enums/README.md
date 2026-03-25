# 🔀 06. Unions, Literal Types, Enums

[← Previous: Objects, Arrays, Tuples](../05-objects-arrays-tuples/README.md) | [Back to Hub](../README.md) | [Next: Interfaces And Type Aliases →](../07-interfaces-and-type-aliases/README.md)

> **Read Time**: 20 minutes  
> **Goal**: Flexible but controlled values ko TypeScript me safe tarike se represent karna

---

## 🎯 What You'll Learn

1. Union types kya hote hain
2. Literal types kis problem ko solve karte hain
3. Enums kab useful hote hain
4. Fixed allowed values define karne ke different ways

---

## 📌 Quick Comparison Table

| Concept | Meaning | Example |
|---------|---------|---------|
| Union | Value multiple possible types me se ek ho sakti hai | `string \| number` |
| Literal Type | Value sirf specific exact values me se ek ho sakti hai | `"pending" \| "paid"` |
| Enum | Named constant group | `Role.Admin` |

---

## 🧪 Union Example

```ts
let orderId: string | number;

orderId = "ORD-101";
orderId = 101;
```

Yahan `orderId` kabhi string ho sakta hai, kabhi number.

---

## 🧪 Literal Type Example

```ts
let status: "pending" | "success" | "failed";
```

Yahan allowed values fixed hain.
Random string allowed nahi hogi.

---

## 🧪 Enum Example

```ts
enum Role {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST"
}
```

---

## 🧪 Combined Real Example

```ts
type PaymentStatus = "pending" | "paid" | "cancelled";

enum UserRole {
  Admin = "ADMIN",
  Editor = "EDITOR",
  Viewer = "VIEWER"
}

let paymentStatus: PaymentStatus = "paid";
let currentRole: UserRole = UserRole.Admin;

console.log(paymentStatus);
console.log(currentRole);
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `string | number` | Value do types me se kisi ek type ki ho sakti hai |
| `"pending" | "paid"` | Sirf exact values allowed hain |
| `enum UserRole` | Reusable named constants ka group |
| `UserRole.Admin` | Enum member access karna |

---

## 🧠 Literal Type Vs Enum

| If You Need... | Better Choice |
|----------------|---------------|
| Simple fixed string values | Literal types |
| Named reusable constant group | Enum |
| Typos se bachna | Dono help karte hain |
| Cleaner plain string unions | Literal types |

---

## ⚠️ Common Mistakes

1. Literal type hone ke baad bhi random string assign karne ki koshish karna.
2. Union type value ko narrow kiye bina direct use karna.
3. Enum aur literal types ko bina reason mix kar dena.

---

## 📝 Practice Tasks

1. Ek variable banao jo `string | number` accept kare.
2. Ek `theme` type banao jo sirf `"light"` ya `"dark"` allow kare.
3. Ek enum banao jisme `Low`, `Medium`, `High` priority values ho.

---

## ✅ Quick Recap

- Union flexibility deta hai
- Literal type exact allowed values define karta hai
- Enum reusable named constant structure deta hai

---

[← Previous: Objects, Arrays, Tuples](../05-objects-arrays-tuples/README.md) | [Next: Interfaces And Type Aliases →](../07-interfaces-and-type-aliases/README.md)
