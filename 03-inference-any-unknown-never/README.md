# 🧭 03. Inference, Any, Unknown, Never

[← Previous: Basic Types](../02-basic-types/README.md) | [Back to Hub](../README.md) | [Next: Functions →](../04-functions/README.md)

> **Read Time**: 18-20 minutes  
> **Goal**: Samajhna ki TypeScript type kaise guess karta hai aur `any`, `unknown`, `never` kab use hote hain

---

## 🎯 What You'll Learn

Is topic me tum samjhoge:

1. Type inference kya hota hai
2. `any` dangerous kyu hota hai
3. `unknown` safer option kyu hai
4. `never` ka actual meaning kya hota hai

---

## 📌 Quick Comparison Table

| Concept | Simple Meaning | Safe? | Kab Use Karna Chahiye |
|---------|----------------|-------|-----------------------|
| Inference | TypeScript khud type guess karta hai | ✅ | Jab value clear ho |
| `any` | Type checking almost band | ❌ | Sirf temporary ya exceptional case |
| `unknown` | Value unknown hai, pehle check karo | ✅ | API/input jahan type sure nahi |
| `never` | Function kabhi normal value return nahi karega | ✅ | Error throw ya infinite loop cases |

---

## 🧠 Type Inference

```ts
let language = "TypeScript";
let version = 5;
let isPopular = true;
```

Yahan TypeScript khud samajh leta hai:

- `language` is `string`
- `version` is `number`
- `isPopular` is `boolean`

Isliye har jagah explicit type likhna zaruri nahi hota.

---

## ⚠️ `any` Kya Karta Hai?

```ts
let data: any = "hello";
data = 100;
data = true;
```

`any` bolta hai:

```text
TypeScript, is variable ko lekar mujhe check mat karo.
```

Ye easy lagta hai, lekin learning aur safety dono weak kar deta hai.

---

## ✅ `unknown` Kyu Better Hai?

```ts
let response: unknown = "Hello";

if (typeof response === "string") {
  console.log(response.toUpperCase());
}
```

Yahan TypeScript kehta hai:

```text
Theek hai, value unknown hai.
Lekin use karne se pehle prove karo ki ye kis type ki hai.
```

---

## 🚫 `never` Kya Hota Hai?

```ts
function throwError(message: string): never {
  throw new Error(message);
}
```

`never` ka matlab:

- function normal way me khatam nahi hota
- ya to error throw karta hai
- ya infinite loop me chala jata hai

---

## 🧪 Example

```ts
let title = "Learning TS";
let apiData: unknown = '{"name":"Aman"}';

function fail(message: string): never {
  throw new Error(message);
}

if (typeof apiData === "string") {
  console.log(apiData.length);
} else {
  fail("Expected a string response");
}
```

---

## 🔍 Code Breakdown

| Line / Part | Meaning |
|-------------|---------|
| `let title = "Learning TS"` | Type inference se `string` detect hua |
| `apiData: unknown` | Value available hai, but type trusted nahi hai |
| `typeof apiData === "string"` | Safe check ke baad string methods allowed hoti hain |
| `fail(...): never` | Function value return nahi karega, sirf error throw karega |

---

## 🆚 `void` Vs `never`

| Type | Meaning |
|------|---------|
| `void` | Function useful return value nahi deta |
| `never` | Function normal flow me finish hi nahi hota |

---

## ⚠️ Common Mistakes

1. Error aate hi `any` use kar dena.
2. `unknown` ko `any` jaisa treat karna.
3. `never` aur `void` ko same samajhna.

---

## 📝 Practice Tasks

1. Ek variable banao jisme type inference se `number` set ho.
2. Ek `unknown` variable banao aur type check ke baad us par method use karo.
3. Ek function likho jo error throw kare aur uska return type `never` ho.

---

## ✅ Quick Recap

- Type inference TypeScript ka smart default behavior hai
- `any` fast shortcut hai, lekin risky hai
- `unknown` safer unknown-data handling deta hai
- `never` un cases ke liye hota hai jahan function normal return nahi karta

---

[← Previous: Basic Types](../02-basic-types/README.md) | [Next: Functions →](../04-functions/README.md)
