# DOM and Frontend

## Why This Topic Matters

TypeScript brings type safety to DOM manipulation. Essential for:

- **DOM element access** - Know the exact element type
- **Event handling** - Type-safe event listeners
- **Web APIs** - Fetch, Storage, etc. with types
- **Browser compatibility** - Avoid undefined errors
- **React setup** - DOM types foundation

Frontend TypeScript prevents runtime errors from DOM manipulation.

## Core Concept

The DOM is accessed through types from the `DOM` library. TypeScript knows what methods each element has:

```typescript
// ❌ Without types - anything goes
const button = document.querySelector(".btn");
button.click(); // What if it doesn't exist?
button.addEventListener("click", () => {});

// ✅ With types - safe
const button = document.querySelector<HTMLButtonElement>(".btn");
if (button) {
  button.click(); // Type-safe
  button.addEventListener("click", () => {}); // Type-safe
}
```

## DOM Types

### Element Types

```typescript
// Get specific element type
const button: HTMLButtonElement | null = document.querySelector("button");
const input: HTMLInputElement | null = document.querySelector("input");
const div: HTMLDivElement | null = document.querySelector("div");

// Get all elements
const buttons: NodeListOf<HTMLButtonElement> =
  document.querySelectorAll("button");

// By ID
const element: HTMLElement | null = document.getElementById("myId");

// By class/selector
const elements: HTMLCollectionOf<HTMLElement> =
  document.getElementsByClassName("myClass");
```

### Event Types

```typescript
// Mouse events
document.addEventListener("click", (event: MouseEvent) => {
  console.log(event.clientX, event.clientY);
});

// Keyboard events
document.addEventListener("keydown", (event: KeyboardEvent) => {
  console.log(event.key, event.code);
});

// Form events
const input = document.querySelector<HTMLInputElement>("input");
input?.addEventListener("change", (event: Event) => {
  console.log((event.target as HTMLInputElement).value);
});

// Custom event
window.addEventListener("error", (event: ErrorEvent) => {
  console.log(event.message, event.filename);
});
```

## Examples

### Example 1: Simple Button Click

```typescript
// ❌ Without types
const button = document.querySelector(".submit-btn");
button.addEventListener("click", () => {
  console.log("Clicked");
});

// ✅ With types
const button = document.querySelector<HTMLButtonElement>(".submit-btn");
if (button) {
  button.addEventListener("click", () => {
    console.log("Button clicked");
  });
} else {
  console.log("Button not found");
}
```

### Example 2: Form Handling

```typescript
interface FormData {
  name: string;
  email: string;
  message: string;
}

const form = document.querySelector<HTMLFormElement>("#contactForm");

if (form) {
  form.addEventListener("submit", (event: SubmitEvent) => {
    event.preventDefault();

    // Get form elements with types
    const nameInput =
      form.querySelector<HTMLInputElement>('input[name="name"]');
    const emailInput = form.querySelector<HTMLInputElement>(
      'input[name="email"]',
    );
    const messageInput = form.querySelector<HTMLTextAreaElement>(
      'textarea[name="message"]',
    );

    if (nameInput && emailInput && messageInput) {
      const data: FormData = {
        name: nameInput.value,
        email: emailInput.value,
        message: messageInput.value,
      };

      console.log("Form data:", data);
    }
  });
}
```

### Example 3: Dynamic Content

```typescript
interface TodoItem {
  id: number;
  text: string;
  completed: boolean;
}

const todoList = document.querySelector<HTMLUListElement>("#todoList");

function renderTodos(todos: TodoItem[]): void {
  if (!todoList) return;

  todoList.innerHTML = "";

  todos.forEach((todo) => {
    const li = document.createElement("li");
    li.textContent = todo.text;
    li.className = todo.completed ? "completed" : "";

    li.addEventListener("click", () => {
      // Toggle completed state
      todo.completed = !todo.completed;
      renderTodos(todos);
    });

    todoList.appendChild(li);
  });
}

// Usage
const todos: TodoItem[] = [
  { id: 1, text: "Learn TypeScript", completed: true },
  { id: 2, text: "Build a project", completed: false },
];

renderTodos(todos);
```

### Example 4: Web APIs with Types

```typescript
// LocalStorage with types
interface Settings {
  theme: "light" | "dark";
  language: "en" | "es";
  fontSize: number;
}

function saveSettings(settings: Settings): void {
  localStorage.setItem("settings", JSON.stringify(settings));
}

function loadSettings(): Settings | null {
  const stored = localStorage.getItem("settings");
  if (stored) {
    return JSON.parse(stored) as Settings;
  }
  return null;
}

// Fetch with types
interface ApiUser {
  id: number;
  name: string;
  email: string;
}

async function fetchUser(id: number): Promise<ApiUser> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  return response.json();
}
```

### Example 5: Event Delegation

```typescript
interface ListItem {
  id: number;
  name: string;
}

const list = document.querySelector<HTMLUListElement>("#list");

// Attach one listener to parent
if (list) {
  list.addEventListener("click", (event: Event) => {
    const target = event.target as HTMLElement;

    if (target.classList.contains("delete-btn")) {
      const li = target.closest<HTMLLIElement>("li");
      if (li) {
        const itemId = parseInt(li.dataset.id || "0");
        console.log(`Deleting item ${itemId}`);
        li.remove();
      }
    }
  });
}

// Generate list
function renderList(items: ListItem[]): void {
  if (!list) return;

  list.innerHTML = "";

  items.forEach((item) => {
    const li = document.createElement("li");
    li.dataset.id = String(item.id);

    li.innerHTML = `
      <span>${item.name}</span>
      <button class="delete-btn">Delete</button>
    `;

    list.appendChild(li);
  });
}
```

## Common Mistakes

### Mistake 1: Ignoring Null

```typescript
// ❌ Assuming element exists
const button = document.querySelector(".btn") as HTMLButtonElement;
button.addEventListener("click", () => {}); // Could crash!

// ✅ Check for null
const button = document.querySelector<HTMLButtonElement>(".btn");
if (button) {
  button.addEventListener("click", () => {});
}

// ✅ Or use optional chaining
button?.addEventListener("click", () => {});
```

### Mistake 2: Wrong Cast

```typescript
// ❌ Wrong element type
const input = document.querySelector("div") as HTMLInputElement;
input.value = "text"; // type says it's an input, but it's a div!

// ✅ Use correct selector
const input = document.querySelector<HTMLInputElement>("input");
input?.value = "text";
```

### Mistake 3: Loose Event Type

```typescript
// ❌ Generic event - can't access properties
document.addEventListener("keydown", (event: Event) => {
  // console.log(event.key);  // ❌ ERROR: property doesn't exist
});

// ✅ Use specific event type
document.addEventListener("keydown", (event: KeyboardEvent) => {
  console.log(event.key); // ✅ Works
});
```

### Mistake 4: Forgetting Type Assertion in Event Target

```typescript
// ❌ event.target is generic EventTarget
input.addEventListener("change", (event: Event) => {
  // console.log(event.target.value);  // ❌ ERROR: EventTarget doesn't have value
});

// ✅ Cast to input element
input.addEventListener("change", (event: Event) => {
  const target = event.target as HTMLInputElement;
  console.log(target.value); // ✅ Works
});
```

## Practice Tasks

1. **Query Selectors**
   - Query a button element
   - Query an input element
   - Handle null cases

2. **Event Listeners**
   - Add click handler to button
   - Add keydown handler to input
   - Type the events properly

3. **Form Handling**
   - Get form elements
   - Listen to form submit
   - Get input values

4. **Dynamic Content**
   - Create elements dynamically
   - Append to DOM
   - Add event listeners

5. **Web Storage**
   - Save object to localStorage
   - Retrieve and parse it
   - Use with types

## Mini Project: Todo App

```typescript
interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

class TodoApp {
  private todos: Todo[] = [];
  private nextId = 1;
  private inputElement: HTMLInputElement | null;
  private listElement: HTMLUListElement | null;

  constructor() {
    this.inputElement = document.querySelector<HTMLInputElement>("#todoInput");
    this.listElement = document.querySelector<HTMLUListElement>("#todoList");

    this.setupEventListeners();
    this.loadFromStorage();
    this.render();
  }

  private setupEventListeners(): void {
    const addBtn = document.querySelector<HTMLButtonElement>("#addBtn");
    addBtn?.addEventListener("click", () => this.addTodo());

    this.inputElement?.addEventListener("keypress", (e: KeyboardEvent) => {
      if (e.key === "Enter") {
        this.addTodo();
      }
    });

    this.listElement?.addEventListener("click", (e: Event) => {
      const target = e.target as HTMLElement;
      if (target.classList.contains("delete")) {
        const id = parseInt(target.dataset.id || "0");
        this.deleteTodo(id);
      }
    });
  }

  private addTodo(): void {
    if (!this.inputElement || !this.inputElement.value.trim()) return;

    const todo: Todo = {
      id: this.nextId++,
      text: this.inputElement.value,
      completed: false,
    };

    this.todos.push(todo);
    this.inputElement.value = "";
    this.saveToStorage();
    this.render();
  }

  private deleteTodo(id: number): void {
    this.todos = this.todos.filter((t) => t.id !== id);
    this.saveToStorage();
    this.render();
  }

  private render(): void {
    if (!this.listElement) return;

    this.listElement.innerHTML = this.todos
      .map(
        (todo) => `
        <li class="${todo.completed ? "completed" : ""}">
          <span>${todo.text}</span>
          <button class="delete" data-id="${todo.id}">Delete</button>
        </li>
      `,
      )
      .join("");
  }

  private saveToStorage(): void {
    localStorage.setItem("todos", JSON.stringify(this.todos));
  }

  private loadFromStorage(): void {
    const stored = localStorage.getItem("todos");
    if (stored) {
      this.todos = JSON.parse(stored);
      this.nextId = Math.max(...this.todos.map((t) => t.id)) + 1;
    }
  }
}

// Initialize app when DOM is ready
document.addEventListener("DOMContentLoaded", () => {
  new TodoApp();
});
```

## HTML for the Todo App

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Todo App</title>
    <style>
      body {
        font-family: Arial;
      }
      input {
        padding: 8px;
        width: 200px;
      }
      button {
        padding: 8px 16px;
      }
      li {
        list-style: none;
        margin: 10px 0;
        padding: 10px;
        border: 1px solid #ccc;
      }
      li.completed {
        text-decoration: line-through;
        color: gray;
      }
      .delete {
        margin-left: 10px;
        background: red;
        color: white;
      }
    </style>
  </head>
  <body>
    <h1>Todo App</h1>
    <input type="text" id="todoInput" placeholder="Add a new todo" />
    <button id="addBtn">Add</button>
    <ul id="todoList"></ul>

    <script src="dist/index.js"></script>
  </body>
</html>
```

## Official TypeScript Docs

- **DOM API Reference**: https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
- **Event Handling**: https://www.typescriptlang.org/docs/handbook/dom-manipulation.html
- **Web APIs**: https://developer.mozilla.org/en-US/docs/Web/API

## Previous Topic

← [Advanced Types](../15-advanced-types/README.md)

## Next Topic

→ [Node.js and Backend](../17-nodejs-and-backend/README.md)

---

_Last Updated: 2024 | TypeScript Version: 5.0+_
