# 🧩 04. Functions

[← Previous: Inference, Any, Unknown, Never](../03-inference-any-unknown-never/README.md) | [Back to Hub](../README.md) | [Next: Objects, Arrays, Tuples →](../05-objects-arrays-tuples/README.md)

> **Read Time**: 18 minutes  
> **Goal**: TypeScript me typed functions ko confidently likhna

---

## 🎯 What You'll Learn

Is README ke through tum samajhoge:

1. Parameter types kaise define karte hain
2. Return type kaise likhte hain
3. Optional parameters kya hote hain
4. Default parameters aur practical function design ka basic idea

---

## 🧠 Function Typing Kyu Important Hai?

Functions real code ka backbone hote hain.
TypeScript me function typing ka matlab hai:

```text
Function kya lega + kya return karega = clear contract
```

Jitna clear contract hoga, utna readable aur safe code hoga.

---

## 📌 Quick Reference Table

| Feature | Syntax | Meaning |
|---------|--------|---------|
| Parameter type | `name: string` | Argument ka type |
| Return type | `): number` | Function kya return karega |
| Optional parameter | `prefix?: string` | Ho bhi sakta hai, na bhi ho |
| Default parameter | `paid = false` | Default value already set hai |

---

## 🧪 Basic Example

```ts
function greet(name: string): string {
  return `Hello, ${name}`;
}
```

---

## 🧪 Real Example

```ts
function createInvoice(
  customerName: string,
  amount: number,
  paid: boolean = false
): string {
  const paymentStatus = paid ? "Paid" : "Pending";
  return `${customerName} - Rs.${amount} - ${paymentStatus}`;
}

console.log(createInvoice("Aman", 1200));
console.log(createInvoice("Sara", 2500, true));
```

---

## 🔍 Code Breakdown

| Code Part | Meaning |
|-----------|---------|
| `customerName: string` | First argument text hona chahiye |
| `amount: number` | Second argument numeric value hona chahiye |
| `paid: boolean = false` | Third argument optional jaisa behave karega because default value hai |
| `): string` | Function final output me string dega |

---

## 📌 Optional Parameter Example

```ts
function showMessage(message: string, prefix?: string): string {
  return prefix ? `${prefix}: ${message}` : message;
}
```

`prefix?` ka matlab:

- user de sakta hai
- ya skip bhi kar sakta hai

---

## 🆚 JavaScript Vs TypeScript

### JavaScript

```js
function createInvoice(customerName, amount, paid = false) {
  const paymentStatus = paid ? "Paid" : "Pending";
  return `${customerName} - Rs.${amount} - ${paymentStatus}`;
}
```

### TypeScript

```ts
function createInvoice(
  customerName: string,
  amount: number,
  paid: boolean = false
): string {
  const paymentStatus = paid ? "Paid" : "Pending";
  return `${customerName} - Rs.${amount} - ${paymentStatus}`;
}
```

TypeScript version me function ka input-output contract visually clear hai.

---

## ⚠️ Common Mistakes

| Mistake | Problem |
|---------|---------|
| Return type ignore kar dena | Complex functions me confusion badh jata hai |
| Optional parameter ko wrong place rakhna | Function signature confusing ho sakti hai |
| Arguments ka order galat dena | Unexpected output ya error aa sakta hai |

---

## 📝 Practice Tasks

1. Ek `subtract` function banao jo do `number` le aur difference return kare.
2. Ek `welcomeUser` function banao jisme second parameter optional ho.
3. Ek `calculateDiscount` function banao jisme third parameter default value ke saath ho.

---

## ✅ Quick Recap

- Functions me types likhne se contract clear hota hai
- Parameters aur return type dono important hain
- Optional aur default parameters real-world functions ko flexible banate hain

---

[← Previous: Inference, Any, Unknown, Never](../03-inference-any-unknown-never/README.md) | [Next: Objects, Arrays, Tuples →](../05-objects-arrays-tuples/README.md)
