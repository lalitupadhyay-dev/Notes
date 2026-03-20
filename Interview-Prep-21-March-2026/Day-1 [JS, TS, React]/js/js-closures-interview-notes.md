# JavaScript Closures — Interview Revision Notes

> Source: [MDN Web Docs — Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Closures)

---

## 1. What is a Closure?

> **A closure is a function bundled together with references to its surrounding lexical environment (outer scope).**

- Closures give a function access to its **outer scope even after the outer function has returned**.
- In JavaScript, **a closure is created every time a function is created**, at function creation time.

---

## 2. Lexical Scoping

- **Lexical scoping** = variable lookup is based on where the variable is **declared in the source code**, not where it's called.
- Nested (inner) functions have access to variables declared in their **outer (parent) function's** scope.

```js
function init() {
  var name = "Mozilla";
  function displayName() {   // inner function — forms a closure
    console.log(name);       // accesses outer variable
  }
  displayName();
}
init(); // "Mozilla"
```

### `var` vs `let` / `const` — Scope Rules

| Keyword | Scope        |
|---------|-------------|
| `var`   | Function or Global (NOT block-scoped) |
| `let`   | Block-scoped (ES6+) |
| `const` | Block-scoped (ES6+) |

```js
if (true) { var x = 1; }
console.log(x); // 1 — var leaks out of the block!

if (true) { const y = 1; }
console.log(y); // ReferenceError — const is block-scoped
```

---

## 3. How Closures Work

```js
function makeFunc() {
  const name = "Mozilla";
  function displayName() {
    console.log(name);
  }
  return displayName; // returned WITHOUT executing
}

const myFunc = makeFunc();
myFunc(); // Still logs "Mozilla" — the closure kept `name` alive!
```

**Why does this work?**  
When `makeFunc()` finishes, normally `name` would be garbage collected. But because `displayName` holds a **reference to its lexical environment**, `name` stays alive as long as `myFunc` exists.

### Function Factory Pattern

```js
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

const add5 = makeAdder(5);
const add10 = makeAdder(10);

add5(2);  // 7
add10(2); // 12
```

- `add5` and `add10` are **independent closures** — same function body, different lexical environments.
- `add5` has `x = 5` in its closure; `add10` has `x = 10`.

---

## 4. Practical Uses of Closures

### Event Callbacks

Closures are the standard way to handle event-based code on the web. They let you **"remember" data** when a callback fires later.

```js
function makeSizer(size) {
  return () => {
    document.body.style.fontSize = `${size}px`;
  };
}

const size12 = makeSizer(12);
const size14 = makeSizer(14);

document.getElementById("size-12").onclick = size12;
document.getElementById("size-14").onclick = size14;
```

---

## 5. Emulating Private Methods (Module Pattern)

Before ES6 classes, closures were used to implement **data hiding and encapsulation**.

```js
const counter = (function () {
  let privateCounter = 0; // private variable

  function changeBy(val) { // private function
    privateCounter += val;
  }

  return { // public API
    increment() { changeBy(1); },
    decrement() { changeBy(-1); },
    value()     { return privateCounter; },
  };
})(); // IIFE — Immediately Invoked Function Expression

counter.increment();
counter.increment();
counter.value(); // 2
```

**Key points:**
- `privateCounter` and `changeBy` are **not accessible from outside**.
- The three public functions share **one single lexical environment** (the IIFE's scope).
- This pattern is called the **Module Design Pattern**.

### Multiple Independent Counters

```js
const counter1 = makeCounter();
const counter2 = makeCounter();
// counter1 and counter2 have SEPARATE closures — no shared state
```

---

## 6. Closure Scope Chain

Closures can access **all outer scopes**, forming a chain:

```js
const e = 10; // global
function sum(a) {
  return function(b) {
    return function(c) {
      return function(d) {
        return a + b + c + d + e; // accesses all outer + global
      };
    };
  };
}

sum(1)(2)(3)(4); // 20
```

Closures also capture:
- **Block-scoped variables** (`let`/`const` inside `{}`)
- **Module-scoped variables** (exported from ES modules)

```js
// myModule.js
let x = 5;
export const getX = () => x;   // closes over module-scoped x
export const setX = (val) => { x = val; };
```

> ⚠️ Imported bindings are **live** — if the original module changes the value, the imported closure sees the updated value too.

---

## 7. Classic Pitfall: Closures in Loops with `var`

```js
// ❌ BUG — all callbacks share the same `item` reference
for (var i = 0; i < helpText.length; i++) {
  var item = helpText[i]; // var is function-scoped, not block-scoped
  element.onfocus = function() {
    showHelp(item.help); // always uses the LAST value of `item`
  };
}
```

**Why?** All three closures share the **same** `item` variable (due to `var` hoisting). By the time callbacks fire, the loop has finished and `item` is the last element.

### ✅ Fix 1: Use `let` / `const` (Recommended)

```js
for (let i = 0; i < helpText.length; i++) {
  const item = helpText[i]; // block-scoped — each iteration gets its own `item`
  element.onfocus = () => showHelp(item.help);
}
```

### ✅ Fix 2: Function Factory (creates a new scope per iteration)

```js
function makeHelpCallback(help) {
  return function() { showHelp(help); };
}

for (var i = 0; i < helpText.length; i++) {
  element.onfocus = makeHelpCallback(helpText[i].help);
}
```

### ✅ Fix 3: IIFE inside loop

```js
for (var i = 0; i < helpText.length; i++) {
  (function() {
    var item = helpText[i];
    element.onfocus = function() { showHelp(item.help); };
  })();
}
```

### ✅ Fix 4: Modern alternatives

```js
for (const item of helpText) { ... }      // for...of with const
helpText.forEach(item => { ... });          // forEach
```

---

## 8. Performance Considerations

- Every function instance **manages its own scope and closure** — this has a memory cost.
- **Don't create closures unnecessarily** inside other functions if you don't need them.

### ❌ Inefficient — methods recreated on every object creation

```js
function MyObject(name) {
  this.name = name;
  this.getName = function() { return this.name; }; // new function per instance
}
```

### ✅ Better — attach methods to the prototype (shared across all instances)

```js
function MyObject(name) {
  this.name = name;
}
MyObject.prototype.getName = function() { return this.name; };
```

---

## Quick-Fire Interview Q&A

| Question | Answer |
|----------|--------|
| What is a closure? | A function + its reference to the outer lexical environment |
| When is a closure created? | Every time a function is created |
| Can a closure access variables after the outer function returns? | Yes — it retains a reference to its lexical environment |
| What's the classic closure-in-loop bug caused by? | `var` being function-scoped, so all callbacks share one variable |
| How do you fix the loop bug? | Use `let`/`const`, a function factory, or `forEach` |
| What is the Module Pattern? | Using an IIFE + closure to create private state with a public API |
| Are closures in different instances independent? | Yes — each call to the outer function creates a new lexical environment |
| What is an IIFE? | Immediately Invoked Function Expression — runs as soon as it's defined |
| Do closures affect performance? | Yes — avoid unnecessary closures; prefer prototype methods over constructor methods |
