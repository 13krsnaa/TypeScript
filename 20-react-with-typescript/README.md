# React with TypeScript

## Why This Topic Matters

TypeScript transforms React development by providing:

- **Component prop safety** - Catch wrong props at compile time
- **State type safety** - Know exactly what state is
- **Event handler safety** - Type-safe events
- **Better autocomplete** - IDE knows component props
- **Easier refactoring** - Change props, errors show everywhere

React + TypeScript is the modern standard for frontend development.

## Core Concept

Every React component has types for props and state:

```typescript
// ❌ JavaScript - no type safety
function UserCard(props) {
  return <div>{props.name}</div>;
}

<UserCard />  // Missing props, no error
<UserCard name={123} />  // Wrong type, no error

// ✅ TypeScript - type-safe
interface UserCardProps {
  name: string;
  age: number;
}

function UserCard({ name, age }: UserCardProps): JSX.Element {
  return <div>{name}, {age}</div>;
}

<UserCard name="Alice" age={30} />  // ✅ Type-safe
// <UserCard />  // ❌ ERROR: Missing required props
// <UserCard name={123} age={30} />  // ❌ ERROR: name must be string
```

## Setup

### Create React App with TypeScript

```bash
npx create-react-app my-app --template typescript
cd my-app
npm start
```

### Manual Setup

```bash
npm install react react-dom
npm install --save-dev @types/react @types/react-dom typescript
```

`tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "jsx": "react-jsx",
    "module": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

## Examples

### Example 1: Functional Component

```typescript
import React from "react";

interface GreetingProps {
  name: string;
  age?: number;  // Optional prop
}

function Greeting({ name, age }: GreetingProps): JSX.Element {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      {age && <p>You are {age} years old</p>}
    </div>
  );
}

export default Greeting;

// Usage:
<Greeting name="Alice" />
<Greeting name="Bob" age={30} />
```

### Example 2: State Management

```typescript
import React, { useState } from "react";

interface Counter {
  count: number;
  label: string;
}

function CounterComponent(): JSX.Element {
  const [counter, setCounter] = useState<Counter>({
    count: 0,
    label: "Count"
  });

  const increment = (): void => {
    setCounter(prev => ({
      ...prev,
      count: prev.count + 1
    }));
  };

  return (
    <div>
      <p>{counter.label}: {counter.count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

### Example 3: Event Handlers

```typescript
import React, { ChangeEvent, FormEvent } from "react";

interface FormState {
  name: string;
  email: string;
}

function FormComponent(): JSX.Element {
  const [form, setForm] = React.useState<FormState>({
    name: "",
    email: ""
  });

  const handleChange = (e: ChangeEvent<HTMLInputElement>): void => {
    const { name, value } = e.currentTarget;
    setForm(prev => ({
      ...prev,
      [name]: value
    }));
  };

  const handleSubmit = (e: FormEvent<HTMLFormElement>): void => {
    e.preventDefault();
    console.log(form);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="name"
        value={form.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        name="email"
        value={form.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Example 4: Custom Hooks

```typescript
import { useState, useCallback } from "react";

interface UseToggleReturn {
  value: boolean;
  toggle: () => void;
  setTrue: () => void;
  setFalse: () => void;
}

function useToggle(initial: boolean = false): UseToggleReturn {
  const [value, setValue] = useState<boolean>(initial);

  const toggle = useCallback((): void => {
    setValue(v => !v);
  }, []);

  const setTrue = useCallback((): void => {
    setValue(true);
  }, []);

  const setFalse = useCallback((): void => {
    setValue(false);
  }, []);

  return { value, toggle, setTrue, setFalse };
}

// Usage:
function MyComponent(): JSX.Element {
  const { value, toggle } = useToggle(false);

  return (
    <div>
      <p>State: {value.toString()}</p>
      <button onClick={toggle}>Toggle</button>
    </div>
  );
}
```

### Example 5: API Integration

```typescript
import React, { useState, useEffect } from "react";

interface User {
  id: number;
  name: string;
  email: string;
}

interface UserListProps {
  apiUrl: string;
}

function UserList({ apiUrl }: UserListProps): JSX.Element {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetchUsers();
  }, [apiUrl]);

  const fetchUsers = async (): Promise<void> => {
    try {
      const response = await fetch(apiUrl);
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }
      const data: User[] = await response.json();
      setUsers(data);
    } catch (err) {
      setError(err instanceof Error ? err.message : "Unknown error");
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name} ({user.email})
        </li>
      ))}
    </ul>
  );
}
```

### Example 6: Context API

```typescript
import React, { createContext, useContext, ReactNode } from "react";

interface Theme {
  dark: boolean;
  color: string;
}

interface ThemeContextType {
  theme: Theme;
  setTheme: (theme: Theme) => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

interface ThemeProviderProps {
  children: ReactNode;
}

export function ThemeProvider({ children }: ThemeProviderProps): JSX.Element {
  const [theme, setTheme] = React.useState<Theme>({
    dark: false,
    color: "blue"
  });

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme(): ThemeContextType {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }
  return context;
}

// Usage in component:
function ThemedComponent(): JSX.Element {
  const { theme, setTheme } = useTheme();

  return (
    <div style={{ backgroundColor: theme.dark ? "black" : "white" }}>
      <button onClick={() => setTheme({ ...theme, dark: !theme.dark })}>
        Toggle Theme
      </button>
    </div>
  );
}
```

## Common Mistakes

### Mistake 1: Using `any` for Props

```typescript
// ❌ Lost type safety
interface Props {
  data: any;
  callback: any;
}

// ✅ Be specific
interface User {
  id: number;
  name: string;
}

interface Props {
  data: User;
  callback: (user: User) => void;
}
```

### Mistake 2: Forgetting Optional Props

```typescript
// ❌ All props required
interface ButtonProps {
  onClick: () => void;
  children: string;
  disabled: boolean;
}

// ✅ Make optional props optional
interface ButtonProps {
  onClick: () => void;
  children: string;
  disabled?: boolean;
}
```

### Mistake 3: Wrong Event Type

```typescript
// ❌ Generic event type
function handleChange(e: Event): void {
  // console.log(e.currentTarget.value);  // ❌ ERROR
}

// ✅ Use specific event type
function handleChange(e: React.ChangeEvent<HTMLInputElement>): void {
  console.log(e.currentTarget.value); // ✅
}
```

### Mistake 4: Not Typing useState Generics

```typescript
// ❌ Type inference works but less clear
const [user, setUser] = useState({ id: 1, name: "Alice" });

// ✅ Explicit type
interface User {
  id: number;
  name: string;
}

const [user, setUser] = useState<User>({ id: 1, name: "Alice" });
```

## Practice Tasks

1. **Simple Component**
   - Create a component with typed props
   - Accept required and optional props

2. **State Hook**
   - Create component with useState
   - Type the state explicitly

3. **Form Component**
   - Create form with typed inputs
   - Handle events properly

4. **Custom Hook**
   - Create custom hook with typed return
   - Use it in a component

5. **API Integration**
   - Fetch data with types
   - Handle loading and error states

## Mini Project: Todo App in React

```typescript
import React, { useState } from "react";

interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

function TodoApp(): JSX.Element {
  const [todos, setTodos] = useState<Todo[]>([]);
  const [input, setInput] = useState<string>("");
  const [nextId, setNextId] = useState<number>(1);

  const addTodo = (): void => {
    if (!input.trim()) return;

    const newTodo: Todo = {
      id: nextId,
      text: input,
      completed: false
    };

    setTodos([...todos, newTodo]);
    setInput("");
    setNextId(nextId + 1);
  };

  const toggleTodo = (id: number): void => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const deleteTodo = (id: number): void => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  const handleKeyPress = (e: React.KeyboardEvent<HTMLInputElement>): void => {
    if (e.key === "Enter") {
      addTodo();
    }
  };

  return (
    <div>
      <h1>Todo App</h1>

      <div>
        <input
          value={input}
          onChange={(e) => setInput(e.currentTarget.value)}
          onKeyPress={handleKeyPress}
          placeholder="Add a new todo"
        />
        <button onClick={addTodo}>Add</button>
      </div>

      <ul>
        {todos.map(todo => (
          <li key={todo.id} style={{ textDecoration: todo.completed ? "line-through" : "none" }}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            {todo.text}
            <button onClick={() => deleteTodo(todo.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default TodoApp;
```

## Official TypeScript Docs

- **React with TypeScript**: https://react-typescript-cheatsheet.netlify.app/
- **React PropTypes in TypeScript**: https://www.typescriptlang.org/docs/handbook/react.html
- **TypeScript React Cheatsheet**: https://react-typescript-cheatsheet.netlify.app/docs/basic/setup

## Previous Topic

← [Databases and ORMs](../19-databases-and-orms/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_

### Next Steps

Now you have completed the TypeScript full-stack roadmap! Here are the next steps:

1. **Build a Real Project** - Create a full-stack application using these concepts
2. **Read the Cheatsheet** - Quick reference for common patterns
3. **Review Interview Questions** - Prepare for technical interviews
4. **Practice with Projects** - Try the projects in the `/projects` folder

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
