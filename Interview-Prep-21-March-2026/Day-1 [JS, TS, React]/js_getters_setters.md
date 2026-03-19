# JavaScript Getters & Setters — Quick Revision Notes

---

## The Core Idea

`get` and `set` disguise functions as properties — you access them **without `()`**, but a function runs behind the scenes.

```javascript
let user = {
    get fullName() {
        return `${this.name} ${this.surname}`;  // called like a property
    },
    set fullName(value) {
        [this.name, this.surname] = value.split(" ");  // called on assignment
    }
};

user.fullName;           // → triggers getter  (no parentheses!)
user.fullName = "Alice Cooper";  // → triggers setter
```

---

## Regular Function vs Getter

| | Regular Function | Getter |
|---|---|---|
| Definition | `fullName() { }` | `get fullName() { }` |
| Call syntax | `user.fullName()` | `user.fullName` |
| Feels like | a function | a property |

---

## How It Works Internally

```javascript
// Internally stored as:
{
  get: function fullName() { ... },   // ← no 'value' key!
  set: function fullName(v) { ... },
  enumerable: true,
  configurable: true
}

// Verify with:
Object.getOwnPropertyDescriptor(user, 'fullName');
```

---

## Common Use Cases

```javascript
// 1. Computed/derived value
get fullName() {
    return `${this.name} ${this.surname}`;
}

// 2. Validation on assignment
set age(value) {
    if (value < 0) throw new Error("Age can't be negative!");
    this._age = value;
}

// 3. Read-only property (getter only, no setter)
get id() {
    return this._id;
}
```

---

## With Prototype / Inheritance

```javascript
let admin = {
    __proto__: user,   // inherits from user
    isAdmin: true
};

admin.fullName;                   // ✅ uses inherited getter → "John Smith"
admin.fullName = "Alice Cooper";  // ✅ uses inherited setter → modifies admin only
user.fullName;                    // still "John Smith" — user is untouched
```

> **Key rule:** Setter modifies `this` — and `this` is always the object that triggered the call (`admin`), not where the setter was defined (`user`).

---

## One-liner Summary

> `get fullName()` tells JS: *"When someone reads `fullName`, secretly run this function — but don't make them use `()`."*
