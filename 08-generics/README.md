# 🧪 08. Generics

[← Previous: Interfaces And Type Aliases](../07-interfaces-and-type-aliases/README.md) | [Back to Hub](../README.md) | [Next: Type Narrowing →](../09-type-narrowing/README.md)

> **Read Time**: 22 minutes  
> **Goal**: Reusable aur type-safe code likhna bina `any` ka overuse kiye

---

## 🎯 What You'll Learn

1. Generics ka basic idea
2. Generic functions kaise likhte hain
3. Generic types real projects me kaise useful hote hain
4. Generics aur `any` me difference kya hai

---

## 🧠 Generics Kya Solve Karte Hain?

Kabhi tumhe aisa code likhna hota hai jo:

- reusable ho
- multiple types ke saath kaam kare
- phir bhi type-safe rahe

Yahin generics help karte hain.

Simple mental model:

```text
Generic = type ke liye placeholder
```

---

## 📌 Without Generics Vs With Generics

| Without Generics | Problem |
|------------------|---------|
| Alag alag function har type ke liye | Code repeat hota hai |
| `any` use karna | Type safety lose ho jati hai |

| With Generics | Benefit |
|---------------|---------|
| Ek reusable function | Code clean hota hai |
| Type preserved rehta hai | Safety bani rehti hai |

---

## 🧪 Basic Syntax

```ts
function identity<T>(value: T): T {
  return value;
}
```

Yahan `T` ka matlab:

```text
Abhi type mat fix karo.
Function call ke time par actual type decide hoga.
```

---

## 🧪 Generic Function Example

```ts
function firstItem<T>(items: T[]): T | undefined {
  return items[0];
}

const firstNumber = firstItem<number>([10, 20, 30]);
const firstName = firstItem<string>(["Aman", "Sara"]);

console.log(firstNumber);
console.log(firstName);
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `<T>` | Generic type placeholder |
| `items: T[]` | Same type ke items ka array |
| `T | undefined` | Item mil bhi sakta hai, ya array empty ho to `undefined` |
| `firstItem<number>(...)` | Yahan `T = number` |
| `firstItem<string>(...)` | Yahan `T = string` |

---

## 🧪 Generic Type Example

```ts
type ApiResponse<T> = {
  data: T;
  success: boolean;
};
```

Example usage:

```ts
type User = {
  id: number;
  name: string;
};

const response: ApiResponse<User> = {
  data: {
    id: 1,
    name: "Aman"
  },
  success: true
};
```

---

## 🆚 Generics Vs `any`

| Generic | `any` |
|---------|-------|
| Type preserve karta hai | Type safety hata deta hai |
| Reusable + safe | Flexible but risky |
| Compiler ko information milti hai | Compiler blind ho jata hai |

---

## ⚠️ Common Mistakes

1. Har function me generic ghusa dena jab zarurat na ho.
2. `T` ko samjhe bina sirf syntax yaad karna.
3. Generic ki jagah `any` use karna jab input-output related ho.

Rule yaad rakho:

```text
Jab input ka type output se connected ho, wahan generic ka chance hota hai.
```

---

## 📝 Practice Tasks

1. Ek generic `getLastItem<T>` function banao jo array ka last item return kare.
2. Ek generic type banao `Box<T>` jisme ek property `value` ho.
3. Ek generic function banao jo kisi input ko array ke andar wrap karke return kare.

---

## ✅ Quick Recap

- Generics reusable aur type-safe code likhne ka tool hain
- Ye `any` se better hote hain jab type relation preserve karna ho
- Real-world API aur utility code me bahut useful hote hain

---

[← Previous: Interfaces And Type Aliases](../07-interfaces-and-type-aliases/README.md) | [Next: Type Narrowing →](../09-type-narrowing/README.md)
