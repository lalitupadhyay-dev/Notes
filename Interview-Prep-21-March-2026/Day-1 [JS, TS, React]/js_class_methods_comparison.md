# Defining Methods in a JS Class — All Ways & Results

---

## 4 Ways to Define a Method in a Class

```javascript
class Dog {
    constructor(name) {
        this.name = name;
        this.bark4 = function() { return "Woof!"; }  // way 4
    }

    bark1() { return "Woof!"; }           // way 1 — regular method
    bark2 = () => { return "Woof!"; }     // way 2 — arrow class field
    // function bark3() {}               // way 3 — ❌ SyntaxError!
}
```

---

## Way 1 — Regular Method (shorthand syntax)

```javascript
class Dog {
    bark() {
        return "Woof!";
    }
}
```

- ✅ Stored on `Dog.prototype` — shared across all instances
- ✅ Memory efficient — only one copy exists
- ⚠️ `this` is dynamic — depends on how the method is called

```javascript
const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");

console.log(dog1.bark === dog2.bark);  // true ✅ same reference
console.log(Dog.prototype.bark);       // ƒ bark() ✅
```

---

## Way 2 — Arrow Function Class Field

```javascript
class Dog {
    bark = () => {
        return "Woof!";
    }
}
```

- ❌ NOT stored on prototype — created fresh on each instance
- ❌ Memory inefficient — every instance gets its own copy
- ✅ `this` is lexical — always locked to the instance

```javascript
const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");

console.log(dog1.bark === dog2.bark);  // false ❌ different copies
console.log(Dog.prototype.bark);       // undefined ❌ not on prototype
```

Under the hood, it's exactly the same as:
```javascript
constructor(name) {
    this.name = name;
    this.bark = () => { return "Woof!"; }  // created per instance
}
```

---

## Way 3 — `function` keyword inside class body

```javascript
class Dog {
    function bark() {   // ❌ SyntaxError: Unexpected token 'function'
        return "Woof!";
    }
}
```

> The `function` keyword is **not allowed** inside a class body. Period.

---

## Way 4 — Regular function assigned via `this` in constructor

```javascript
class Dog {
    constructor(name) {
        this.name = name;
        this.bark = function() {  // stored on instance
            return "Woof!";
        }
    }
}
```

- ❌ NOT stored on prototype — duplicated on every instance
- ❌ Memory inefficient
- ⚠️ `this` is dynamic — depends on how it's called

```javascript
const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");

console.log(dog1.bark === dog2.bark);  // false ❌ different copies
console.log(Dog.prototype.bark);       // undefined ❌ not on prototype
```

---

## Way 5 — Manually attach to prototype (ES5 style)

```javascript
function Dog(name) {       // old constructor function
    this.name = name;
}

Dog.prototype.bark = function() {   // manually attached
    return "Woof!";
};
```

- ✅ Stored on prototype — shared across all instances
- ✅ Memory efficient
- This is the **ES5 equivalent** of a class method — `class` is just syntactic sugar over this!

```javascript
const dog1 = new Dog("Daisy");
console.log(Dog.prototype.bark);           // ƒ () ✅
console.log(dog1.__proto__ === Dog.prototype);  // true ✅
```

---

## The `this` Problem — Where Arrow Fields Shine

```javascript
class Dog {
    constructor(name) { this.name = name; }

    bark1() { return this.name; }         // regular method
    bark2 = () => { return this.name; }   // arrow field
}

const dog = new Dog("Daisy");

// Called normally — both work fine
dog.bark1();  // ✅ "Daisy"
dog.bark2();  // ✅ "Daisy"

// Detached from object — this is where they differ!
const b1 = dog.bark1;
b1();   // ❌ undefined — 'this' is lost (no object before the dot)

const b2 = dog.bark2;
b2();   // ✅ "Daisy" — 'this' always locked to instance
```

### Real world case — Event listeners & callbacks:

```javascript
class Button {
    constructor(label) { this.label = label; }

    handleClick1() { console.log(this.label); }        // regular
    handleClick2 = () => { console.log(this.label); }  // arrow
}

const btn = new Button("Submit");

document.addEventListener("click", btn.handleClick1);  // ❌ undefined
document.addEventListener("click", btn.handleClick2);  // ✅ "Submit"
```

> When a method is passed as a callback, it loses its object context.
> Arrow fields keep `this` locked — making them perfect for event handlers.

---

## Full Comparison Table

| Way | Syntax | On Prototype? | Shared? | `this` |
|---|---|---|---|---|
| Regular method | `bark() {}` | ✅ Yes | ✅ One copy | Dynamic |
| Arrow class field | `bark = () => {}` | ❌ No | ❌ Per instance | Lexical (locked) |
| `function` in class body | `function bark() {}` | 💥 SyntaxError | — | — |
| `this.bark` in constructor | `this.bark = function(){}` | ❌ No | ❌ Per instance | Dynamic |
| Manual prototype (ES5) | `Dog.prototype.bark = function(){}` | ✅ Yes | ✅ One copy | Dynamic |

---

## Rule of Thumb

| Situation | Use |
|---|---|
| Normal method, care about memory | Regular method `bark() {}` |
| Callback / event listener, need stable `this` | Arrow field `bark = () => {}` |
| Old ES5 codebase without classes | `Constructor.prototype.method = function(){}` |

> Arrow class fields trade **memory efficiency** for **guaranteed `this` binding**.
> Regular methods trade **`this` safety** for **memory efficiency**.
