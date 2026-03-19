# Module Pattern — Interview Ready Notes

---

## What is it?

The Module Pattern splits code into **smaller, reusable, self-contained pieces** (files/modules).
Each module keeps certain values **private by default** — only what you explicitly `export` is accessible outside.

### Two key benefits:
- **Encapsulation** — private values stay private, no accidental leaks
- **No global scope pollution** — prevents naming collisions across files

---

## ES2015 Modules — The Modern Way

Every `.js` file can be a module. Values inside are **scoped to that file by default**.

```javascript
// math.js
const privateValue = "only available inside math.js";  // ❌ not exported

export function add(x, y) { return x + y; }
export function multiply(x) { return x * 2; }
export function subtract(x, y) { return x - y; }
export function square(x) { return x * x; }
```

```javascript
// index.js
import { add, multiply } from "./math.js";

console.log(privateValue);  // ❌ ReferenceError: privateValue is not defined
console.log(add(2, 3));     // ✅ 5
```

---

## Named Exports vs Default Export

### Named Exports — multiple, must use `{ }`

```javascript
// math.js
export function add(x, y) { return x + y; }       // named
export function multiply(x) { return x * 2; }     // named
```

```javascript
// index.js
import { add, multiply } from "./math.js";  // must use { }
```

### Default Export — only ONE per module, no `{ }` needed

```javascript
// math.js
export default function add(x, y) { return x + y; }  // default
export function multiply(x) { return x * 2; }         // named
```

```javascript
// index.js
import add, { multiply } from "./math.js";
//     ^^^  no curly braces for default!

// Default export can be renamed freely
import addValues, { multiply } from "./math.js";  // ✅ also valid
```

### Key difference:

| | Named Export | Default Export |
|---|---|---|
| How many per module | Multiple ✅ | Only ONE |
| Import syntax | `import { name }` | `import name` (no `{}`) |
| Can rename on import? | Yes, with `as` | Yes, freely |

---

## Renaming with `as`

When imported name clashes with a local variable:

```javascript
// index.js — has its own add() function
import {
    add as addValues,        // renamed to avoid collision
    multiply as multiplyValues,
    subtract,
    square
} from "./math.js";

function add(...args) { ... }      // local add — no conflict now ✅
addValues(7, 8);                   // math.js add ✅
```

---

## Import Everything with `*`

```javascript
import * as math from "./math.js";

math.default(7, 8);    // default export
math.multiply(8, 9);   // named export
math.subtract(10, 3);  // named export
math.square(3);        // named export
```

> ⚠️ Use carefully — you may import values you don't need.
> Private (unexported) values are still NOT accessible even with `*`.

---

## Dynamic Import — Load on Demand

By default, all top-level imports load **before** the file runs.
Dynamic imports load modules **only when needed** — great for performance.

```javascript
// Static import — always loads upfront
import { add } from "./math.js";

// Dynamic import — loads only when called
button.addEventListener("click", () => {
    import("./math.js").then(module => {
        console.log(module.add(1, 2));      // ✅
        console.log(module.multiply(3, 2)); // ✅
    });
});

// With async/await
button.addEventListener("click", async () => {
    const module = await import("./math.js");
    console.log(module.add(1, 2));
});
```

### Dynamic import with expressions (template literals):

```javascript
// Load different files based on a variable
const res = await import(`../assets/dog${num}.png`);
```

> Adds flexibility — module path doesn't need to be hardcoded!

### Why dynamic imports matter:
- Reduces **initial page load time**
- Code is only loaded, parsed, and compiled **when the user actually needs it**
- Avoids loading heavy third-party libraries if the feature isn't used

---

## Module Pattern in React

Each component lives in its own file = its own module:

```
src/
 ├── components/
 │     ├── Button.js    ← its own module
 │     ├── Input.js     ← its own module
 │     └── TodoList.js  ← its own module
 └── index.js
```

```javascript
// Button.js
const styles = { color: "blue" };   // private to Button.js — no collision!
export function Button() { ... }

// Input.js
const styles = { color: "red" };    // same variable name — NO conflict ✅
export function Input() { ... }
```

> Both files use `styles` — module scoping prevents any naming collision.

---

## Static vs Dynamic Import Comparison

| | Static Import | Dynamic Import |
|---|---|---|
| When loaded | Always, at startup | On demand, when called |
| Syntax | `import x from 'y'` | `import('y').then(...)` |
| Can be conditional? | ❌ No | ✅ Yes |
| Performance | Slower initial load | Faster initial load |
| Use case | Core dependencies | Optional/heavy features |

---

## Pros ✅

- **Encapsulation** — private values stay private inside the module
- **No global scope pollution** — reduces risk of naming collisions
- **Reusability** — import only what you need, where you need it
- **Maintainability** — split large codebases into focused, manageable files
- **Performance** — dynamic imports reduce initial bundle size

## Cons ⚠️

- Requires a transpiler like **Babel** to work across all JS runtimes
- Overusing `import *` can pull in unnecessary code

---

## One-liner for Interviews

> *"The Module Pattern encapsulates code into reusable files — only explicitly exported values are public, keeping everything else private, preventing global scope pollution and naming collisions."*
