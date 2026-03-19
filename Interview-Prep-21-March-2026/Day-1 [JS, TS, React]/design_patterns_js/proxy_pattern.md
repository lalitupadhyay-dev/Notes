# Proxy Pattern — Interview Ready Notes

---

## What is it?

A Proxy wraps an object and **intercepts every interaction** with it — reads, writes, deletions, etc.
Instead of talking to the object directly, you talk to the proxy, which can add logic in between.

---

## Basic Syntax

```javascript
const person = { name: "John Doe", age: 42 };

const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        console.log(`Getting ${prop}: ${obj[prop]}`);
        return obj[prop];
    },
    set: (obj, prop, value) => {
        console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
        obj[prop] = value;
        return true;  // ← must return true in set handler!
    }
});

personProxy.name;       // → triggers get
personProxy.age = 43;   // → triggers set
```

### Two most common handlers:
| Handler | Triggered when |
|---|---|
| `get` | A property is **accessed** |
| `set` | A property is **modified** |

---

## Real Use Case — Validation

```javascript
const personProxy = new Proxy(person, {
    set: (obj, prop, value) => {
        if (prop === "age" && typeof value !== "number") {
            console.log("Age must be a number!");
        } else if (prop === "name" && value.length < 2) {
            console.log("Name too short!");
        } else {
            obj[prop] = value;  // only sets if valid ✅
        }
        return true;
    },
    get: (obj, prop) => {
        if (!obj[prop]) {
            console.log("Property doesn't exist!");
        } else {
            return obj[prop];
        }
    }
});

personProxy.age = "44";  // ❌ "Age must be a number!"
personProxy.name = "";   // ❌ "Name too short!"
personProxy.age = 44;    // ✅ sets successfully
```

---

## `Reflect` — The Cleaner Way

Instead of `obj[prop]` or `obj[prop] = value` inside handlers,
use `Reflect` — the official way to forward operations to the target object.

```javascript
const personProxy = new Proxy(person, {
    get: (obj, prop) => {
        console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`);
    },
    set: (obj, prop, value) => {
        console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
        return Reflect.set(obj, prop, value);
    }
});
```

> `Reflect` methods have the **exact same names** as proxy handler methods.
> Think of `Reflect` as the "default behavior" you can call after your custom logic.

---

## Use Cases

| Use Case | What Proxy does |
|---|---|
| **Validation** | Block invalid values on `set` |
| **Logging / Debugging** | Log every get/set |
| **Default values** | Return fallback if property missing |
| **Notifications** | Trigger events when state changes |
| **Formatting** | Transform values on get/set |

---

## Tradeoff ⚠️

Overusing Proxy or running heavy logic inside handlers can **hurt performance**.
Avoid using Proxy in performance-critical code paths.

---

## Proxy vs Object.defineProperty

| | `Proxy` | `Object.defineProperty` |
|---|---|---|
| Intercepts | All operations (get, set, delete, etc.) | Only get/set on specific property |
| Dynamic | ✅ Works on whole object | ❌ Must define per property |
| Non-existent props | ✅ Can intercept | ❌ Can't |

---

## One-liner for Interviews

> *"Proxy wraps an object and intercepts every get and set on it — perfect for validation, logging, and access control, without touching the original object."*
