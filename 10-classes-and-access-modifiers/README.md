# 🏛️ 10. Classes And Access Modifiers

[← Previous: Type Narrowing](../09-type-narrowing/README.md) | [Back to Hub](../README.md) | [Next: Utility Types →](../11-utility-types/README.md)

> **Read Time**: 20 minutes  
> **Goal**: TypeScript classes aur access control ko beginner-friendly tarike se samajhna

---

## 🎯 What You'll Learn

1. Basic class syntax
2. Constructor kya hota hai
3. `public`, `private`, `protected`, `readonly`
4. Encapsulation ka basic idea

---

## 🧠 Class Kya Hoti Hai?

Class ek blueprint hoti hai.

Simple analogy:

```text
Class = design
Object = us design se bana actual item
```

---

## 🧪 Basic Class Example

```ts
class User {
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

---

## 📌 Access Modifiers Quick Table

| Modifier | Meaning |
|----------|---------|
| `public` | Har jagah accessible |
| `private` | Sirf class ke andar accessible |
| `protected` | Class aur subclasses me accessible |
| `readonly` | Ek baar set hone ke baad change nahi karna chahiye |

---

## 🧪 Real Example

```ts
class BankAccount {
  public owner: string;
  private balance: number;
  readonly accountNumber: string;

  constructor(owner: string, balance: number, accountNumber: string) {
    this.owner = owner;
    this.balance = balance;
    this.accountNumber = accountNumber;
  }

  deposit(amount: number): void {
    this.balance += amount;
  }

  getBalance(): number {
    return this.balance;
  }
}

const account = new BankAccount("Aman", 5000, "AC123");
account.deposit(1000);
console.log(account.getBalance());
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `public owner` | Bahar se access ho sakta hai |
| `private balance` | Direct bahar se access nahi hoga |
| `readonly accountNumber` | Set hone ke baad change nahi hona chahiye |
| `constructor(...)` | Object create hote waqt initial values set hoti hain |
| `deposit()` | Class behavior define karta hai |

---

## 🆚 JavaScript Vs TypeScript

JavaScript me class structure hota hai, lekin TypeScript us structure ko stronger bana deta hai:

- property types clear hoti hain
- constructor contract clear hota hai
- access control readable hota hai

---

## ⚠️ Common Mistakes

1. Sab kuch `public` rakh dena.
2. `readonly` ka use ignore karna.
3. Class properties ko initialize karna bhool jana.
4. Sirf syntax ke liye classes use karna, clarity ke liye nahi.

---

## 📝 Practice Tasks

1. Ek `Car` class banao jisme `brand` aur `year` ho.
2. Ek `StudentAccount` class banao jisme `private marks` ho aur unko read karne ke liye method ho.
3. Ek class banao jisme ek `readonly id` property ho.

---

## ✅ Quick Recap

- Class related data aur behavior ko ek jagah organize karti hai
- Access modifiers control aur safety provide karte hain
- `readonly` accidental changes se bachata hai

---

[← Previous: Type Narrowing](../09-type-narrowing/README.md) | [Next: Utility Types →](../11-utility-types/README.md)
