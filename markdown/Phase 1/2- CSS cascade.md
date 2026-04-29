___
## CSS Specificity & the Cascade

### What the Cascade Actually Is

The cascade is the algorithm browsers use to decide **which rule wins** when multiple CSS declarations target the same element. It's not random — it follows a strict priority order.

There are four layers the browser works through, in order:

1. **Origin & importance** — where did the rule come from?
2. **Specificity** — how precisely does the selector target the element?
3. **Order** — which rule appeared last in the source?

Understanding these three resolves almost every "why isn't my CSS working" moment.

---

### Layer 1 — Origin & Importance

CSS can come from multiple sources. The browser resolves them in this priority order (highest to lowest):

```
!important in author CSS       ← highest
Normal author CSS              ← your stylesheets
User agent stylesheet          ← browser defaults
```

`!important` overrides everything. Which is exactly why you should almost never use it — it breaks the natural cascade and makes debugging painful.

```css
/* ❌ Fighting the cascade with !important */
.card { color: red !important; }

/* ✅ Fix the specificity instead — don't force it */
.services .card { color: red; }
```

The one legitimate use for `!important` is overriding third-party stylesheets you can't modify.

---

### Layer 2 — Specificity

Specificity is a scoring system. Every selector has a score, and the **highest score wins**. Think of it as a three-column number: **(A, B, C)**.

|Column|What it counts|Score|
|---|---|---|
|**A**|ID selectors (`#id`)|1-0-0|
|**B**|Classes, attributes, pseudo-classes|0-1-0|
|**C**|Elements, pseudo-elements|0-0-1|

These columns **never carry over**. Ten classes (0-10-0) still loses to one ID (1-0-0).

```css
p                      /* (0,0,1)  — 1 element */
.card                  /* (0,1,0)  — 1 class */
p.card                 /* (0,1,1)  — 1 class + 1 element */
#hero                  /* (1,0,0)  — 1 ID */
#hero .card p          /* (1,1,1)  — 1 ID + 1 class + 1 element */
```

---

### Calculating It — Examples

```css
/* (0,0,1) — one element */
h2 { color: black; }

/* (0,1,0) — one class */
.title { color: blue; }

/* (0,1,1) — one class + one element */
h2.title { color: green; }

/* (0,2,1) — two classes + one element */
.services h2.title { color: red; }

/* (1,0,0) — one ID — beats all of the above */
#hero { color: purple; }
```

For this HTML:

```html
<section id="hero" class="services">
  <h2 class="title">Our Services</h2>
</section>
```

The `h2.title` gets `color: red` from `.services h2.title` (0,2,1) — which beats `.title` (0,1,0) and `h2.title` (0,1,1). But `#hero` (1,0,0) applied to the section doesn't compete — it targets a different element.

---

### Layer 3 — Source Order

When two rules have **identical specificity**, the one that appears **later in the CSS wins**.

```css
.card { background: white; }
.card { background: teal; }   /* ✅ this wins — same specificity, later in source */
```

This is why import order matters in your `main.css`. Components imported later can override base styles:

```css
@import './base/reset.css';       /* lowest */
@import './base/tokens.css';
@import './layout/grid.css';
@import './components/cards.css'; /* overrides layout if same specificity */
```

---

### The Universal Selector & Combinators

These contribute **zero specificity**:

```css
*           /* (0,0,0) — universal selector */
div > p     /* (0,0,2) — combinators don't add score, only the elements do */
div + p     /* (0,0,2) */
div ~ p     /* (0,0,2) */
```

The combinator (`>`, `+`, `~`, ) itself scores nothing. Only the selectors on either side of it contribute.

---

### Pseudo-classes vs Pseudo-elements

```css
/* Pseudo-classes — (0,1,0) each — they're like classes */
a:hover        /* (0,1,1) */
li:first-child /* (0,1,1) */
:not(.card)    /* (0,1,0) — :not() itself scores 0, argument scores normally */
:is(.card, p)  /* (0,1,0) — takes specificity of its highest-specificity argument */

/* Pseudo-elements — (0,0,1) each — they're like elements */
p::before      /* (0,0,2) */
p::first-line  /* (0,0,2) */
```

---

### The Inheritance Layer

Before specificity even comes into play, some properties **inherit** their value from parent elements automatically. Others don't.

```css
/* These inherit by default */
color, font-size, font-family, line-height,
text-align, list-style, cursor, visibility

/* These do NOT inherit */
margin, padding, border, background,
width, height, display, position
```

```html
<section style="color: teal;">
  <p>This text is teal</p>      <!-- inherits color ✅ -->
  <p>This too</p>               <!-- inherits color ✅ -->
</section>
```

You can force inheritance with the `inherit` keyword, or cut it off with `initial`:

```css
.card { color: inherit; }        /* force inheritance */
.card { color: initial; }        /* reset to browser default */
.card { color: unset; }          /* inherit if inheritable, initial otherwise */
```

---

### The Specificity Trap

The most common mistake is **escalating specificity to fix bugs**, which creates a cycle that's very hard to escape.

```css
/* Someone wrote this */
#sidebar .card { color: gray; }     /* (1,1,0) */

/* You try to override it */
.card.featured { color: teal; }     /* (0,2,0) — loses ❌ */

/* So you escalate */
#sidebar .card.featured { color: teal; }   /* (1,2,0) — wins, but now you're locked in */

/* Next time you need to override THAT... */
#sidebar .content .card.featured { color: blue; }  /* (1,3,0) — it never ends */
```

The fix is to **avoid IDs in CSS** and keep selectors as flat as possible.

```css
/* ✅ Low specificity — easy to override anywhere */
.card { color: gray; }           /* (0,1,0) */
.card--featured { color: teal; } /* (0,1,0) */
```

---

### Practical Rules to Live By

**1. Never use IDs in CSS.** Use them for JS hooks and anchor links only. They create specificity debt immediately.

```css
/* ❌ */
#hero-section { background: teal; }

/* ✅ */
.hero { background: teal; }
```

**2. Keep selectors as short as possible.** One or two classes is usually enough.

```css
/* ❌ Over-qualified — fragile and specific */
section.services div.card h2.title { font-size: 1.5rem; }

/* ✅ Direct — easy to override */
.card__title { font-size: 1.5rem; }
```

**3. Use `cascade layers` for large projects.** A modern CSS feature that lets you explicitly control priority between groups of styles, regardless of specificity:

```css
@layer base, components, utilities;

@layer base {
  h2 { color: black; }          /* even (1,0,0) here loses to... */
}

@layer utilities {
  .text-teal { color: teal; }   /* ...this (0,1,0) — layer order wins */
}
```

**4. Debug with DevTools.** In browser DevTools, the Styles panel shows every rule targeting an element, sorted by specificity — with overridden rules shown as strikethrough. This is the fastest way to understand why something isn't applying.

---

### The Cascade in One Mental Model

When the browser needs to style an element, it asks these questions in order:

```
1. Is any rule marked !important?
      Yes → that wins (ignore everything else)
      No  → continue

2. Which rules have the highest specificity?
      One winner → apply it
      Tie        → continue

3. Which rule appears last in the source?
      → that one wins
```

That's the entire cascade. Every "why isn't my CSS working" question is answered somewhere in those three steps.

---
