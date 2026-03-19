# Factory Pattern — Interview Ready Notes

---

## What is it?

The Factory Pattern uses a **factory function** to create and return new objects —
**without using the `new` keyword**.

> Any function that returns a new object without `new` is a factory function.

---

## Basic Implementation

```javascript
const createUser = ({ firstName, lastName, email }) => ({
    firstName,
    lastName,
    email,
    fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
});

const user1 = createUser({ firstName: "John", lastName: "Doe", email: "john@doe.com" });
const user2 = createUser({ firstName: "Jane", lastName: "Doe", email: "jane@doe.com" });

console.log(user1.fullName());  // "John Doe"
console.log(user2.fullName());  // "Jane Doe"
```

No `new`, no `class` — just a function that **returns an object**.

---

## Dynamic / Configurable Objects

Factory functions shine when object shape depends on **conditions or config**:

```javascript
// Dynamic keys from array
const createObjectFromArray = ([key, value]) => ({
    [key]: value,
});

createObjectFromArray(["name", "John"]);  // { name: "John" }
createObjectFromArray(["age", 30]);       // { age: 30 }
```

```javascript
// Environment-based config
const createConfig = (env) => ({
    apiUrl: env === "production" ? "https://api.prod.com" : "https://api.dev.com",
    debug: env !== "production",
    timeout: env === "production" ? 5000 : 10000,
});

const config = createConfig("production");
```

---

## Factory Function vs Class

```javascript
// Factory Function
const createUser = ({ firstName, lastName, email }) => ({
    firstName,
    lastName,
    email,
    fullName() { return `${this.firstName} ${this.lastName}`; }
});

const user1 = createUser({ firstName: "John", lastName: "Doe", email: "john@doe.com" });


// Class (equivalent)
class User {
    constructor(firstName, lastName, email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
    }
    fullName() { return `${this.firstName} ${this.lastName}`; }
}

const user2 = new User("John", "Doe", "john@doe.com");
```

| | Factory Function | Class |
|---|---|---|
| `new` keyword | ❌ Not used | ✅ Required |
| Methods on prototype? | ❌ Per instance | ✅ Shared on prototype |
| Memory efficiency | ❌ Less (methods duplicated) | ✅ More (methods shared) |
| Flexibility | ✅ Very flexible | ⚠️ More rigid |
| Encapsulation / privacy | ✅ Easy with closures | ⚠️ Needs private fields (`#`) |

---

## Memory Consideration ⚠️

This is a key tradeoff — the most common interview follow-up:

```javascript
// Factory function — fullName() created fresh on EVERY object
const u1 = createUser({ firstName: "John", ... });
const u2 = createUser({ firstName: "Jane", ... });

console.log(u1.fullName === u2.fullName);  // false ❌ different copies!

// Class — fullName() lives on prototype, shared by ALL instances
const u3 = new User("John", ...);
const u4 = new User("Jane", ...);

console.log(u3.fullName === u4.fullName);  // true ✅ same reference!
```

> **Factory functions duplicate methods per object — classes share them via prototype.**
> For large numbers of objects, classes are more memory efficient.

---

## Where Factory Pattern Is Useful

- Creating **many similar objects** with slight differences
- Object shape depends on **environment or user config**
- You want **encapsulation without a class** (using closures for private data)
- Avoiding complexity of `new` and `this` binding issues

```javascript
// Private data with closures — something class can't do as cleanly
const createCounter = () => {
    let count = 0;              // private — not accessible outside!
    return {
        increment() { return ++count; },
        decrement() { return --count; },
        getCount()  { return count; }
    };
};

const counter = createCounter();
counter.increment();  // 1
counter.increment();  // 2
console.log(counter.count);  // undefined ← truly private ✅
```

---

## Pros ✅

- Simple and clean — just a function returning an object
- Very **flexible** — can return different object shapes based on input/config
- Easy **private data** via closures (no need for `#privateField` syntax)
- No `this` binding confusion — no `new` keyword needed
- Great for **configurable or environment-specific** object creation

## Cons ⚠️

- **Memory inefficient** — methods are duplicated on every created object (not shared via prototype)
- For large-scale object creation, a `class` is more memory efficient
- Not much more than a plain function in JS — can feel like overkill for simple cases

---

## One-liner for Interviews

> *"The Factory Pattern is a function that creates and returns new objects without using `new` — great for flexible, configurable object creation, but less memory efficient than classes since methods aren't shared via prototype."*
