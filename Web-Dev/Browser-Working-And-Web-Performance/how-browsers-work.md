# 🌐 How Browsers Work — Developer Notes

> Based on: [MDN — Populating the page: how browsers work](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/How_browsers_work)

---

## Table of Contents

1. [Big Picture](#big-picture)
2. [Step 1 — Navigation](#step-1--navigation)
   - [DNS Lookup](#dns-lookup)
   - [TCP Handshake](#tcp-handshake)
   - [TLS Negotiation (HTTPS)](#tls-negotiation-https)
3. [Step 2 — Response & TCP Slow Start](#step-2--response--tcp-slow-start)
4. [Step 3 — Parsing](#step-3--parsing)
   - [Building the DOM](#building-the-dom)
   - [Preload Scanner](#preload-scanner)
   - [Building the CSSOM](#building-the-cssom)
   - [JavaScript Compilation](#javascript-compilation)
   - [Accessibility Tree (AOM)](#accessibility-tree-aom)
5. [Step 4 — Render](#step-4--render)
   - [Style (Render Tree)](#style-render-tree)
   - [Layout (Reflow)](#layout-reflow)
   - [Paint](#paint)
   - [Compositing](#compositing)
6. [Step 5 — Interactivity (TTI)](#step-5--interactivity-tti)
7. [Key Performance Takeaways](#key-performance-takeaways)
8. [Glossary](#glossary)

---

## Big Picture

Every time you open a webpage, the browser does a LOT of work behind the scenes. At a high level, it goes through these phases:

```
URL entered
    │
    ▼
┌─────────────┐
│  NAVIGATION │  ← DNS + TCP + TLS (finding & connecting to the server)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   RESPONSE  │  ← Server sends back HTML (first chunk = 14KB)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   PARSING   │  ← Browser reads HTML/CSS/JS → builds DOM + CSSOM
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   RENDER    │  ← Style → Layout → Paint → Composite
└──────┬──────┘
       │
       ▼
┌──────────────┐
│ INTERACTIVITY│  ← Page is usable (TTI)
└──────────────┘
```

> **Two biggest enemies of performance:** Latency (network delay) and the browser's single-threaded nature (it can only do one thing at a time on the main thread).

---

## Step 1 — Navigation

Navigation starts the moment a user types a URL, clicks a link, or submits a form.

### DNS Lookup

Before the browser can talk to a server, it needs to find its **IP address** — like looking up someone's address in a phone book.

```
Browser → "Where is example.com?"
    │
    ▼
DNS Server → "It's at 93.184.216.34"
    │
    ▼
Browser caches that IP for future use ✅
```

> ⚠️ **Performance note:** Every unique hostname (fonts, images, scripts, ads) needs its own DNS lookup. If your page loads from 10 different domains, that's 10 DNS lookups. On mobile networks, each DNS lookup request has to go through Cell Phone Tower that adds more delay.

---

### TCP Handshake

Once the IP is known, the browser opens a connection to the server using a **TCP 3-way handshake** (called SYN-SYN-ACK).

```
Browser  ──── SYN ────►  Server    ("Hey, want to talk?")
Browser  ◄── SYN-ACK ──  Server    ("Sure!")
Browser  ──── ACK ────►  Server    ("Great, let's go!")
```

This takes **3 round trips** before any data is actually sent.

---

### TLS Negotiation (HTTPS)

For secure HTTPS connections, an additional handshake happens to establish encryption.

```
Browser ──► Server : Client Hello
Browser ◄── Server : Server Hello + Certificate
Browser ──► Server : Key exchange
Browser ◄── Server : Finished
Browser ──► Server : Finished
```

That's **5 more round trips** just to set up encryption.

> So by the time the browser even asks for the HTML page, **8 round trips** have already happened (3 TCP + 5 TLS).

> ✅ Worth it though — HTTPS ensures no one in the middle can read your data.

---

## Step 2 — Response & TCP Slow Start

Once connected, the browser sends an HTTP GET request and the server starts responding.

### The Magic 14KB Rule

The server doesn't dump all data at once. It starts small and ramps up — this is called **TCP Slow Start**.

```
Round 1: Server sends  ~14KB  (1 window)
Round 2: Server sends  ~28KB  (doubled if ACK received)
Round 3: Server sends  ~56KB  (doubled again)
...and so on
```

**Why?** To avoid overwhelming the network. The server gradually figures out how much data the connection can handle.

> 💡 **Pro tip:** This is why the first **14KB** of your HTML is so critical. If the browser can start rendering from just that first chunk, users see *something* much faster. Always try to get your critical HTML + CSS within 14KB.

**TTFB (Time to First Byte)** = Time from user's click → receiving the first byte of data. This is an important performance metric.

---

## Step 3 — Parsing

Now the browser starts reading the HTML and building structures it can work with.

### Building the DOM

The browser reads the HTML and constructs a **DOM (Document Object Model)** tree — a tree structure representing every element on the page.

```html
<!-- HTML -->
<html>
  <body>
    <h1>Hello</h1>
    <p>World <a href="#">link</a></p>
  </body>
</html>
```

```
DOM Tree:
html
 └── body
      ├── h1
      │    └── "Hello" (text node)
      └── p
           ├── "World " (text node)
           └── a
                └── "link" (text node)
```

> ⚠️ **JS blocks parsing!** When the parser hits a `<script>` tag (without `async`/`defer`), it **stops** parsing HTML until the script is downloaded and executed. This is why you put scripts at the bottom or use `async`/`defer`.

```html
<!-- ❌ Blocks HTML parsing -->
<script src="app.js"></script>

<!-- ✅ Doesn't block — runs after HTML is parsed -->
<script src="app.js" defer></script>

<!-- ✅ Doesn't block — runs as soon as downloaded -->
<script src="app.js" async></script>
```

---

### Preload Scanner

While the main thread is building the DOM, a background process called the **Preload Scanner** peeks ahead in the HTML and starts downloading high-priority resources early (CSS, JS, fonts, images).

```
Main Thread:     Building DOM slowly... 
                 [<html> → <head> → ...]

Preload Scanner: Already found styles.css, app.js, logo.png
                 → Downloading them in parallel in the background 🚀
```

> This is why the browser feels faster than it theoretically should be — resources are often already downloaded by the time the parser needs them.

---

### Building the CSSOM

Similar to the DOM, the browser also parses all CSS and builds a **CSSOM (CSS Object Model)** tree.

```
CSS Rules:
body { font-size: 16px; }
p    { color: blue; }
p a  { color: red; }

CSSOM Tree:
body (font-size: 16px)
 └── p (color: blue)
      └── a (color: red)
```

The browser **cascades** styles from general → specific (that's where "Cascading" in CSS comes from).

> ✅ CSSOM building is very fast — generally not a performance bottleneck.
>
> ⚠️ But CSS **blocks JavaScript** — if JS tries to read CSS properties before CSSOM is ready, it has to wait.

---

### JavaScript Compilation

While DOM + CSSOM are being built, JS files are also being processed:

```
JS File Downloaded
      │
      ▼
   Parsed → Abstract Syntax Tree (AST)
      │
      ▼
   Compiled → Bytecode
      │
      ▼
   Executed on Main Thread
```

> ⚠️ JS execution happens on the **main thread**, which means it competes with rendering. Heavy JS = janky page. Use **Web Workers** for heavy JS code.

---

### Accessibility Tree (AOM)

In parallel, the browser also builds an **Accessibility Object Model (AOM)** — a semantic version of the DOM used by screen readers and assistive devices.

```
DOM:                    AOM:
<button>Submit</button> → Role: Button, Label: "Submit"
<img alt="Logo">        → Role: Image, Label: "Logo"
<nav>...</nav>          → Role: Navigation
```

> Screen readers cannot access page content until the AOM is built.

---

## Step 4 — Render

Now the actual visual rendering begins. This has 4 sub-steps:

```
DOM + CSSOM
    │
    ▼
 STYLE  → Render Tree (only visible elements)
    │
    ▼
 LAYOUT → Calculate size & position of every element
    │
    ▼
 PAINT  → Draw pixels: colors, text, borders, shadows
    │
    ▼
COMPOSITE → Combine layers → display on screen
```

---

### Style (Render Tree)

The DOM and CSSOM are **merged** into a **Render Tree** — which only contains visible elements.

```
DOM includes everything:          Render Tree (visible only):
html                              html
 ├── head         ✗ excluded       └── body
 │    └── title   ✗ excluded            ├── h1
 └── body                               └── p
      ├── h1      ✓ included                 └── a
      ├── p       ✓ included
      └── div [display:none]  ✗ excluded
```

> `display: none` → **excluded** from render tree (no space taken)
> `visibility: hidden` → **included** in render tree (space is reserved, just invisible)

---

### Layout (Reflow)

The browser now calculates the **exact position and size** of every element in the render tree.

```
Render Tree node: <p>
  → Width: 800px (viewport width)
  → Height: auto (based on content)
  → X: 0, Y: 120px (from top)
```

- First time it runs → called **Layout**
- Any subsequent recalculation → called **Reflow**

> ⚠️ **Reflows are expensive!** Changing one element's size can cascade and force the entire page to recalculate. For example, if an image loads without defined dimensions, the browser reflows the entire page once it knows the image size.

```html
<!-- ❌ Causes reflow when image loads (no dimensions defined) -->
<img src="photo.jpg" />

<!-- ✅ No reflow — browser already knows the space to reserve -->
<img src="photo.jpg" width="400" height="300" />
```

---

### Paint

The browser now converts layout boxes into **actual pixels** — drawing text, colors, borders, shadows, etc.

```
Layout box (x:0, y:0, w:800, h:50)
    │
    ▼
Painted pixels: blue background, white text "Hello World"
```

To make this fast, the browser breaks the page into **layers** (like Photoshop layers). Some elements automatically get their own layer:

| Element / Property | Gets own layer |
|---|---|
| `<video>` | ✅ Yes |
| `<canvas>` | ✅ Yes |
| `opacity` | ✅ Yes |
| `transform: translate3d(...)` | ✅ Yes |
| `will-change: transform` | ✅ Yes |

> 💡 GPU handles layers → much faster than CPU for repaints.
> ⚠️ But don't overuse layers — they consume memory.

---

### Compositing

Once layers are painted, the browser **composites** (combines) them in the correct order to produce the final image on screen.

```
Layer 3 (fixed navbar)      ─┐
Layer 2 (main content)       ├── Composited → Final Frame on Screen
Layer 1 (background)        ─┘
```

> This is why **CSS transforms and opacity** are the best properties to animate — they only trigger compositing (GPU), skipping layout and paint entirely.

| CSS Property Changed | Triggers |
|---|---|
| `width`, `height`, `margin` | Layout → Paint → Composite (slowest ❌) |
| `color`, `background` | Paint → Composite |
| `transform`, `opacity` | Composite only (fastest ✅) |

---

## Step 5 — Interactivity (TTI)

The page is *painted* but is it actually *usable*? Not necessarily.

**TTI (Time to Interactive)** = The point where the page responds to user input within 50ms.

```
Page Painted ≠ Page Interactive

If the main thread is still busy executing JavaScript...
→ User clicks button → no response 😤
→ User tries to scroll → janky 😤
```

```
Timeline example:
0ms    : Navigation starts
800ms  : First Contentful Paint (user sees something)
2300ms : JS finishes executing (TTI — page is now interactive)
         └── For those 1500ms, clicks/scrolls are ignored!
```

> ✅ Goal: Keep TTI as close to First Paint as possible.
> ✅ Solution: Split large JS bundles, use `defer`, avoid long-running scripts on the main thread.

---

## Key Performance Takeaways

| Problem | Solution |
|---|---|
| Too many DNS lookups | Use fewer domains, use `dns-prefetch` |
| Slow TTFB | Optimize server, use CDN |
| Render-blocking JS | Use `defer` or `async` on scripts |
| No image dimensions set | Always set `width` and `height` on images |
| Heavy JS blocking main thread | Code-split, lazy-load, use web workers |
| Animating expensive properties | Animate only `transform` and `opacity` |
| Too many compositor layers | Don't overuse `will-change` |
| Large initial HTML | Keep critical content within first 14KB |

---

## Glossary

| Term | Simple meaning |
|---|---|
| **DNS** | Phone book of the internet — maps domain names to IPs |
| **TCP Handshake** | The "hello" ritual before browser and server start talking |
| **TLS** | Encryption setup for HTTPS connections |
| **TTFB** | Time to First Byte — how quickly server starts responding |
| **DOM** | Browser's tree structure of all HTML elements |
| **CSSOM** | Browser's tree structure of all CSS styles |
| **Render Tree** | DOM + CSSOM merged, with only visible elements |
| **Layout / Reflow** | Calculating positions and sizes of elements |
| **Paint** | Drawing actual pixels on the screen |
| **Compositing** | Merging layers into the final screen image |
| **TTI** | Time to Interactive — when the page actually responds to clicks |
| **Preload Scanner** | Background process that fetches resources early |
| **TCP Slow Start** | Network algorithm that gradually increases data transfer |
| **AOM** | Accessibility Object Model — for screen readers |
| **Layer** | A portion of the page rendered independently (often on GPU) |

---

> 📌 **Source:** [MDN Web Docs — How Browsers Work](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/How_browsers_work)
> 
> 📅 Last updated: April 2026
