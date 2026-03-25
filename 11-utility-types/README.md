# 🧰 11. Utility Types

[← Previous: Classes And Access Modifiers](../10-classes-and-access-modifiers/README.md) | [Back to Hub](../README.md) | [Next: tsconfig And Workflow →](../12-tsconfig-and-workflow/README.md)

> **Read Time**: 18-20 minutes  
> **Goal**: Existing types ko quickly reuse aur transform karna

---

## 🎯 What You'll Learn

1. Utility types kya hote hain
2. `Partial`, `Required`, `Pick`, `Omit`, `Record`
3. Real-world examples me inka use
4. Type duplication kam kaise hoti hai

---

## 🧠 Utility Types Kyu Important Hain?

Real code me baar baar ye need aati hai:

- full object ka update version banana
- sirf kuch selected fields nikalna
- kuch fields hata dena
- key-value map structure banana

Har baar naya type manually likhne ke bajay utility types ka use hota hai.

---

## 📌 Common Utility Types Table

| Utility Type | Kya Karta Hai |
|--------------|---------------|
| `Partial<T>` | Sab properties optional bana deta hai |
| `Required<T>` | Sab properties required bana deta hai |
| `Pick<T, Keys>` | Sirf selected properties leta hai |
| `Omit<T, Keys>` | Selected properties hata deta hai |
| `Record<Keys, Type>` | Key-value object structure banata hai |

---

## 🧪 Base Type Example

```ts
interface User {
  id: number;
  name: string;
  email: string;
  isAdmin: boolean;
}
```

---

## 🧪 Real Example

```ts
interface User {
  id: number;
  name: string;
  email: string;
  isAdmin: boolean;
}

type UserUpdate = Partial<User>;
type PublicUser = Pick<User, "id" | "name">;
type AdminConfig = Record<string, boolean>;

const updateData: UserUpdate = {
  name: "New Name"
};

const publicProfile: PublicUser = {
  id: 1,
  name: "Aman"
};

const permissions: AdminConfig = {
  canEdit: true,
  canDelete: false
};
```

---

## 🔍 Code Breakdown

| Part | Meaning |
|------|---------|
| `Partial<User>` | Update payload jaisa type ban sakta hai |
| `Pick<User, "id" | "name">` | Sirf selected public fields rakhe jate hain |
| `Record<string, boolean>` | String keys aur boolean values ka map |

---

## 📌 Real-World Use Cases

| Situation | Useful Utility Type |
|-----------|---------------------|
| Form update payload | `Partial<T>` |
| Public profile data | `Pick<T, Keys>` |
| Internal field remove karna | `Omit<T, Keys>` |
| Permission map | `Record<K, V>` |

---

## ⚠️ Common Mistakes

1. Utility type use karna jab normal direct type enough ho.
2. `Partial` ko validation ka replacement samajhna.
3. `Pick` aur `Omit` me wrong field names use karna.

---

## 📝 Practice Tasks

1. Ek `Product` interface banao aur uska `Partial<Product>` update type create karo.
2. Ek `Pick` example banao jo sirf `id` aur `title` choose kare.
3. Ek `Record<string, number>` example banao for subject marks.

---

## ✅ Quick Recap

- Utility types type duplication kam karte hain
- Ye existing types ko reuse aur reshape karne me help karte hain
- Real-world code me ye time bachate hain aur consistency badhate hain

---

[← Previous: Classes And Access Modifiers](../10-classes-and-access-modifiers/README.md) | [Next: tsconfig And Workflow →](../12-tsconfig-and-workflow/README.md)
