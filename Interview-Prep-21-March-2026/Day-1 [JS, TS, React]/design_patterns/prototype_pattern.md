# Prototype Pattern — Interview Ready Notes

---

## What is it?

The Prototype Pattern is a way to **share properties and methods among objects of the same type**
via the **prototype chain** — instead of duplicating them on every instance.

> Every object in JavaScript has a built-in `prototype` — this is not a pattern you add,
> it's how JS works natively. The pattern is about *leveraging* it intentionally.

---

## How Prototype Works with Classes

```javascript
class Dog {
    constructor(name) {
        this.name = name;   // stored on the instance
    }

    bark() {                // stored on Dog.prototype (shared!)
        return "Woof!";
    }
}

const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");
```

- `dog1.name` and `dog2.name` → each instance has its **own copy**
- `dog1.bark` and `dog2.bark` → both point to the **same function** on the prototype

```javascript
console.log(Dog.prototype);
// { constructor: f Dog(), bark: f bark() }

console.log(dog1.__proto__ === Dog.prototype);  // true ✅
```

> `__proto__` on any instance is a **direct reference** to the constructor's prototype.

---

## The Prototype Chain

When you access a property on an object, JS looks for it in this order:

```
dog1.bark()
    │
    ├── 1. Look on dog1 itself         → not found
    ├── 2. Look on dog1.__proto__      → found on Dog.prototype ✅
    └── 3. Look on Object.prototype    → (if still not found)
               └── null               → undefined (end of chain)
```

---

## Adding to Prototype After Creation

You can add new methods to the prototype **even after instances are created** — all instances get access immediately:

```javascript
Dog.prototype.play = () => console.log("Playing!");

dog1.play();  // ✅ works even though dog1 was created before .play was added
dog2.play();  // ✅ same
```

---

## Prototype Chain with Inheritance

```javascript
class SuperDog extends Dog {
    constructor(name) {
        super(name);
    }
    fly() {
        return "Flying!";
    }
}

const dog1 = new SuperDog("Daisy");
dog1.bark();  // ✅ inherited from Dog.prototype
dog1.fly();   // ✅ on SuperDog.prototype
```

### Chain looks like:
```
dog1
  └── __proto__ → SuperDog.prototype  { fly() }
                      └── __proto__ → Dog.prototype  { bark() }
                                          └── __proto__ → Object.prototype
                                                              └── null
```

JS walks UP this chain until it finds the property or hits `null`.

---

## `Object.create` — Explicit Prototype Assignment

Lets you create an object and **manually set its prototype**:

```javascript
const dog = {
    bark() { return "Woof!"; }
};

const pet1 = Object.create(dog);  // dog is now pet1's prototype

pet1.bark();  // ✅ "Woof!" — found on prototype chain

console.log(Object.keys(pet1));           // []  ← no own properties
console.log(Object.keys(pet1.__proto__)); // ["bark"]  ← lives on prototype
```

> `pet1` itself is empty — but inherits everything from `dog` through the chain.

---

## Key Benefit — Memory Efficiency

```javascript
// ❌ Without prototype — each instance gets its own copy of bark()
const dog1 = { name: "Daisy", bark: function() { return "Woof!"; } };
const dog2 = { name: "Max",   bark: function() { return "Woof!"; } };
// bark() duplicated in memory for every object!

// ✅ With prototype — bark() exists once, shared by all
class Dog {
    constructor(name) { this.name = name; }
    bark() { return "Woof!"; }  // one copy, shared by all instances
}
```

---

## Summary Table

| Concept | Description |
|---|---|
| `prototype` | Object where shared methods/properties live |
| `__proto__` | An instance's reference to its constructor's prototype |
| Prototype chain | JS walks up `__proto__` links to find a property |
| `Object.create(obj)` | Creates new object with `obj` as its prototype |
| `extends` | Sets up the prototype chain between two classes |
| `super()` | Calls parent class constructor |

---

## `__proto__` vs `prototype`

| | `prototype` | `__proto__` |
|---|---|---|
| Lives on | Constructor functions / Classes | Object instances |
| Used for | Defining shared methods | Linking to the prototype |
| Example | `Dog.prototype.bark` | `dog1.__proto__` |

---

## One-liner for Interviews

> *"The Prototype Pattern lets objects share properties and methods through the prototype chain — avoiding duplication, saving memory, and enabling inheritance natively in JavaScript."*
