# Observer Pattern — Interview Ready Notes

---

## What is it?

With the Observer Pattern, certain objects (**observers**) subscribe to another object (the **observable**).
Whenever an event occurs, the observable **automatically notifies all its observers**.

Think of it like a YouTube channel — you subscribe once, and get notified every time a new video drops.

```
Observable (the channel)
    │
    ├── notify() ──► observer1 (logger)
    ├── notify() ──► observer2 (toastify)
    └── notify() ──► observer3 (analytics)
```

---

## Core Structure

An observable object has 3 key parts:

| Part | Role |
|---|---|
| `observers` | Array of all subscribed observer functions |
| `subscribe(fn)` | Adds an observer to the list |
| `unsubscribe(fn)` | Removes an observer from the list |
| `notify(data)` | Calls every observer with the given data |

---

## Implementation

```javascript
class Observable {
    constructor() {
        this.observers = [];  // list of subscribers
    }

    subscribe(func) {
        this.observers.push(func);  // add observer
    }

    unsubscribe(func) {
        this.observers = this.observers.filter(
            observer => observer !== func   // remove observer
        );
    }

    notify(data) {
        this.observers.forEach(observer => observer(data));  // notify all
    }
}

const observable = new Observable();
```

---

## Real World Example

Track user interactions and notify a **logger** and a **toast notification**:

```javascript
// Step 1 — define observer functions
function logger(data) {
    console.log(`${Date.now()} ${data}`);
}

function toastify(data) {
    toast(data);  // shows a popup notification
}

// Step 2 — subscribe them to the observable
observable.subscribe(logger);
observable.subscribe(toastify);

// Step 3 — notify on events
function handleClick() {
    observable.notify("User clicked button!");
    // → logger fires   ✅
    // → toastify fires ✅
}

function handleToggle() {
    observable.notify("User toggled switch!");
    // → logger fires   ✅
    // → toastify fires ✅
}
```

### Full flow:
```
User clicks button
    → handleClick()
        → observable.notify("User clicked button!")
            → logger("User clicked button!")      ← logs to console
            → toastify("User clicked button!")    ← shows toast popup
```

---

## Unsubscribing

```javascript
observable.subscribe(logger);     // logger is now active

// later...
observable.unsubscribe(logger);   // logger will no longer be notified

observable.notify("hello");       // only remaining observers are called
```

---

## When to Use Observer Pattern

Perfect for **asynchronous, event-based data**:

- User interaction tracking (clicks, toggles, inputs)
- Real-time notifications (chat messages, alerts)
- Data fetching — notify components when download finishes
- Analytics — multiple systems react to the same event
- Any case where **one event → multiple reactions**

---

## Real Library — RxJS

RxJS is a popular library built entirely on the Observer Pattern:

```javascript
import { fromEvent, merge } from "rxjs";
import { sample, mapTo } from "rxjs/operators";

// Subscribe to mouse events
merge(
    fromEvent(document, "mousedown").pipe(mapTo(false)),
    fromEvent(document, "mousemove").pipe(mapTo(true))
)
.pipe(sample(fromEvent(document, "mouseup")))
.subscribe(isDragging => {
    console.log("Were you dragging?", isDragging);
});
```

> RxJS combines the Observer pattern with the Iterator pattern and functional programming to manage sequences of events.

---

## Pros ✅

- **Separation of concerns** — observable monitors events, observers handle data. Neither knows much about the other.
- **Single responsibility** — each observer does one thing
- **Loose coupling** — observers can be added/removed at any time without changing the observable
- **Scalable** — add new observers without touching existing code

---

## Cons ⚠️

- If an observer becomes too complex, notifying all subscribers can cause **performance issues**
- Hard to track data flow when many observers are subscribed — debugging gets tricky
- Order of notification is not always predictable

---

## Observer vs Pub/Sub — Quick Distinction

| | Observer Pattern | Pub/Sub Pattern |
|---|---|---|
| Communication | Observable notifies observers **directly** | Uses a **message broker** in between |
| Coupling | Observers know about the observable | Publishers & subscribers don't know each other |
| Example | Custom Observable class | EventEmitter, Redux, Message queues |

---

## One-liner for Interviews

> *"The Observer Pattern lets multiple objects subscribe to an observable — whenever an event fires, all subscribers are automatically notified, enabling loose coupling and separation of concerns."*
