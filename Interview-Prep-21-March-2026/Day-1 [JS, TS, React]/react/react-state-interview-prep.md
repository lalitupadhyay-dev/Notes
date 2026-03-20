# React State: Preserving & Resetting — Interview Readiness

---

## Core Concept to Nail First

> React tracks components by their **position in the JSX tree**, not by variable names, labels, or component function names. State belongs to the position, not the component.

---

## Q&A: Most Likely Interview Questions

---

### Q1. When does React preserve component state?

React preserves state when the **same component type renders at the same position** in the tree across re-renders.

```jsx
// State is PRESERVED — same component (Counter), same position
{showA ? <Counter /> : <Counter />}
```

React sees the same type at the same position on both renders and reuses the existing state.

---

### Q2. When does React destroy/reset component state?

State is destroyed when:

1. **The component is removed from the tree** (unmounted)
2. **A different component type renders at the same position**
3. **A `key` prop changes** on the component

```jsx
// State is RESET — different types at position 1
{isPaused ? <p>Paused</p> : <Counter />}
```

When `isPaused` toggles, React sees a different type (`<p>` vs `<Counter>`) and destroys the old state entirely.

---

### Q3. Does wrapping a component in a new tag reset its state?

**Yes.** Adding or removing a wrapper element changes the tree structure. React sees the child at a different position and resets its state.

```jsx
// Before: Counter is at depth 1
<Counter />

// After: Counter is now at depth 2 — state RESETS
<div>
  <Counter />
</div>
```

This is a common gotcha in conditional rendering. Even one extra wrapper div will destroy the state of all children below it.

---

### Q4. Two `<Counter />` components are rendered side by side. Are their states shared or independent?

**Fully independent.** Each position in the tree has its own isolated state. Position 1's Counter and Position 2's Counter each maintain their own count, even though they're the same component function.

```jsx
<Counter />  {/* position 1 — count: 5 */}
<Counter />  {/* position 2 — count: 12 */}
```

---

### Q5. Explain the first-name / last-name swap bug.

**The scenario:** Two inputs are rendered. On checkbox toggle, their order is reversed. But the *values typed* don't swap with the labels — the value stays in its original position.

**Why it happens:**

```jsx
{reversed ? (
  <>
    <label>Last Name</label>
    <input />   {/* Position 1 */}
    <label>First Name</label>
    <input />   {/* Position 2 */}
  </>
) : (
  <>
    <label>First Name</label>
    <input />   {/* Position 1 */}
    <label>Last Name</label>
    <input />   {/* Position 2 */}
  </>
)}
```

React only sees:
- Position 1 → `<input />` (before and after) ✅ same type
- Position 2 → `<input />` (before and after) ✅ same type

So it reuses both inputs and their state. Only the `<label>` text changes. The value "John" stays in Position 1's input, now labeled "Last Name".

**The labels are just decorative text to React. The input's identity is its position.**

---

### Q6. How do you force React to reset state at the same position?

**Use the `key` prop.** A `key` ties a component's identity to a value, not just its position. When the key changes, React destroys the old component and mounts a fresh one.

```jsx
// Fix for the swap bug — keys make them truly distinct
<input key="firstname" />
<input key="lastname" />
```

When reversed, React sees `key="firstname"` at Position 2 (it was at Position 1 before) and treats it as a brand new component — state is reset.

---

### Q7. What are the two ways to reset state at the same position?

1. **Render the component at a different position** — use conditional rendering that genuinely changes tree structure
2. **Use the `key` prop** — give each component instance a unique, stable key

---

### Q8. Give a real-world use case for using `key` to reset state.

**Chat application:** When a user switches between contacts, the message input should be cleared.

```jsx
// Without key — input state is preserved when switching contacts
<ChatInput contact={selectedContact} />

// With key — input resets every time a new contact is selected
<ChatInput key={selectedContact.id} contact={selectedContact} />
```

Changing `key` to the contact's ID ensures each contact gets a fresh input field.

---

### Q9. What is the mental model for how React sees the component tree?

Think of the UI tree as a grid of **seats** in a cinema.

- Each seat (position) can hold a component and stores its state independently
- React doesn't care about the name badge you stick on a seat — it only cares about *which seat* and *what type of person is sitting there*
- If the same type of person sits in the same seat across renders, their belongings (state) are untouched
- If a different type of person sits down, the old belongings are cleared out

---

### Q10. Does state live inside the component function?

**No.** State lives inside React itself, associated with a position in the render tree. The component function just reads from and writes to that position. This is why state persists across re-renders even though the function re-executes every time.

---

## Key Terms Cheat Sheet

| Term | What it means |
|---|---|
| **Position** | Where a component sits in the JSX tree (depth + sibling index) |
| **Mounting** | A component rendering into the tree for the first time |
| **Unmounting** | A component being removed from the tree; state is destroyed |
| **key prop** | Overrides position-based identity; changing key resets state |
| **State preservation** | React keeping existing state because type + position haven't changed |
| **State reset** | React destroying state because type, position, or key changed |

---

## Common Gotchas to Mention in Interviews

- **Never define a component inside another component's render.** Every render creates a new function reference. React sees a "new" component type each time and resets state on every render.

  ```jsx
  // ❌ BAD — MyInput is a new type every render, state resets constantly
  function Form() {
    function MyInput() { return <input />; }
    return <MyInput />;
  }

  // ✅ GOOD — defined outside, stable type reference
  function MyInput() { return <input />; }
  function Form() { return <MyInput />; }
  ```

- **Conditionally adding a wrapper resets all children's state**, even if you only meant to style something. Be careful with conditional `<div>` wrappers.

- **The `key` prop is not just for lists.** It's a general-purpose identity tool and is the cleanest way to reset state intentionally.

---

## One-Liner Summary (Say This in Interviews)

> "React identifies components by their position in the tree and their type. If both stay the same across renders, state is preserved. If either changes — or if the key changes — state is destroyed and reset. Labels, variable names, and component names don't factor in at all."
