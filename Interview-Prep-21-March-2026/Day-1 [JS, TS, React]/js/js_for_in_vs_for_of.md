# `for...in` vs `for...of` in JavaScript — Quick Revision Notes

---

## One-line Difference

| | `for...in` | `for...of` |
|---|---|---|
| Iterates over | **Keys** (property names) | **Values** |
| Works on | Objects, Arrays | Arrays, Strings, Maps, Sets (iterables) |

---

## `for...in` — loops over keys

```javascript
const user = { name: "John", age: 30, city: "Delhi" };

for (let key in user) {
    console.log(key);        // "name", "age", "city"
    console.log(user[key]);  // "John", 30, "Delhi"
}
```

On an **array**, gives **indexes** (as strings):

```javascript
const arr = ["a", "b", "c"];

for (let i in arr) {
    console.log(i);      // "0", "1", "2"  ← indexes, not values!
    console.log(arr[i]); // "a", "b", "c"
}
```

---

## `for...of` — loops over values

```javascript
const arr = ["a", "b", "c"];

for (let val of arr) {
    console.log(val);  // "a", "b", "c"  ← directly values!
}
```

Works on **strings**:
```javascript
for (let char of "Hello") {
    console.log(char);  // "H", "e", "l", "l", "o"
}
```

Works on **Map** and **Set**:
```javascript
const set = new Set([1, 2, 3]);
for (let val of set) {
    console.log(val);  // 1, 2, 3
}

const map = new Map([["a", 1], ["b", 2]]);
for (let [key, val] of map) {
    console.log(key, val);  // "a" 1,  "b" 2
}
```

---

## `for...of` on plain Object — ❌ fails!

```javascript
const user = { name: "John", age: 30 };

for (let val of user) { }  // ❌ TypeError: user is not iterable
```

**Workaround** — use `Object.values()` or `Object.entries()`:

```javascript
for (let val of Object.values(user)) {
    console.log(val);        // "John", 30
}

for (let [key, val] of Object.entries(user)) {
    console.log(key, val);   // "name" "John",  "age" 30
}
```

---

## Danger of `for...in` on Arrays ⚠️

```javascript
const arr = ["a", "b", "c"];
arr.customProp = "oops";   // extra property added

for (let i in arr) {
    console.log(i);   // "0", "1", "2", "customProp"  ← picks up extra props!
}

for (let val of arr) {
    console.log(val); // "a", "b", "c"  ← safe, ignores extra props ✅
}
```

> `for...in` loops over **all enumerable properties** — including accidentally added ones.

---

## Full Comparison Table

| Feature | `for...in` | `for...of` |
|---|---|---|
| Gives | Keys / indexes | Values |
| Plain objects | ✅ | ❌ TypeError |
| Arrays | ⚠️ not recommended | ✅ |
| Strings | ✅ (char indexes) | ✅ (characters) |
| Map / Set | ❌ | ✅ |
| Extra properties | Picks them up ⚠️ | Ignores them ✅ |

---

## One-liner Rule

```
for...in  →  use for OBJECTS  (gives keys)
for...of  →  use for ARRAYS, STRINGS, MAP, SET  (gives values)
```
