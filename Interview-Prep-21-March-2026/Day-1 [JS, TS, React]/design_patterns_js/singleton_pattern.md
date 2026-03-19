# Singleton Pattern — Interview Ready Notes

---

## What is it?

A Singleton is a class that can be **instantiated only once** and accessed globally.
That single instance is shared throughout the application — useful for managing global state.

---

## Implementation

```javascript
let instance;
let counter = 0;

class Counter {
    constructor() {
        if (instance) {
            throw new Error("Only one instance allowed!"); // guard
        }
        instance = this;
    }

    getInstance() { return this; }
    getCount()    { return counter; }
    increment()   { return ++counter; }
    decrement()   { return --counter; }
}

const singletonCounter = Object.freeze(new Counter()); // freeze it!
export default singletonCounter;
```

### Two key things:
- **`instance` check** — throws error if you try to create a second object
- **`Object.freeze()`** — prevents outside code from modifying the singleton

---

## In JS, you don't even need a class

Since objects are passed by reference in JS, a plain frozen object does the same job:

```javascript
let count = 0;

const counter = {
    increment() { return ++count; },
    decrement() { return --count; }
};

Object.freeze(counter);
export { counter };
```

> Both `redButton.js` and `blueButton.js` importing this share the **same reference** automatically.

---

## Why Singleton is an Anti-Pattern in JS ⚠️

This is the key interview question.

### 1. Testing is hard
All tests share the same global instance. One test's changes bleed into the next.
Order of tests starts to matter — that's always a red flag.

### 2. Dependency hiding
When a module uses a singleton internally, the caller has no idea.
They may accidentally mutate shared global state without realizing it.

```javascript
// superCounter.js secretly modifies the singleton inside!
import Counter from "./counter";

export default class SuperCounter {
    increment() {
        Counter.increment(); // ← hidden side effect
        return (this.count += 100);
    }
}
```

### 3. Global mutable state is risky
Multiple parts of the app mutating the same object = unpredictable bugs,
especially as the app grows and dozens of components depend on each other.

---

## Singleton vs Redux / React Context

| | Singleton | Redux / Context |
|---|---|---|
| State type | **Mutable** | **Read-only** |
| Who can change it? | Anyone, anywhere | Only pure reducer functions |
| Predictability | Low ❌ | High ✅ |

> Redux and Context solve the same "global state" problem — but in a controlled, predictable way.

---

## One-liner for Interviews

> *"Singleton ensures only one instance exists globally — useful in theory, but in JS it's often an anti-pattern because it makes testing hard, hides dependencies, and creates unpredictable shared mutable state."*
