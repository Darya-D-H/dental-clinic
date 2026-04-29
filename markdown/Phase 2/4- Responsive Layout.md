___
## Responsive Structure & Scalability — Deep Dive

## Part 1 — The Mobile-First Mindset

### Why Mobile-First, Not Desktop-First

Mobile-first means writing your **base styles for the smallest screen**, then adding complexity as the viewport grows. The alternative — writing for desktop and stripping back — creates heavier, more fragile CSS.

```css
/* ❌ Desktop-first — you're adding overrides to undo things */
.card {
  display: flex;
  gap: 2rem;
  padding: 2rem;
}

@media (max-width: 768px) {
  .card {
    display: block;  /* undoing flex */
    gap: 0;          /* undoing gap */
    padding: 1rem;   /* undoing padding */
  }
}

/* ✅ Mobile-first — you're adding, never undoing */
.card {
  display: block;
  padding: 1rem;
}

@media (min-width: 768px) {
  .card {
    display: flex;
    gap: 2rem;
    padding: 2rem;
  }
}
```

The mobile-first version is lighter — the browser only loads what it needs. The desktop-first version loads everything and then overrides it.

### Media Query Syntax in Depth

```css
/* min-width — applies at this width and above (mobile-first) */
@media (min-width: 768px) { ... }

/* max-width — applies at this width and below (desktop-first, avoid) */
@media (max-width: 767px) { ... }

/* range syntax — modern CSS, cleaner than min+max */
@media (768px <= width <= 1200px) { ... }

/* combining conditions */
@media (min-width: 768px) and (orientation: landscape) { ... }

/* targeting print */
@media print { ... }

/* targeting dark mode preference */
@media (prefers-color-scheme: dark) { ... }

/* targeting reduced motion */
@media (prefers-reduced-motion: reduce) { ... }
```

### Breakpoint Strategy

Three to four breakpoints is almost always enough. Define them in your token file and comment them clearly — never scatter magic numbers across your stylesheets.

```css
/* tokens.css */
/*
  Breakpoints (mobile-first, min-width):
  --bp-sm:  640px   large phones, small tablets
  --bp-md:  768px   tablets, small laptops
  --bp-lg:  1024px  laptops, small desktops
  --bp-xl:  1280px  standard desktops and above
*/
```

Note that CSS custom properties don't work inside media query conditions — so you document them as comments and use the raw values in your queries. This is one area where a preprocessor like Sass helps, but it's not essential.

**The most important rule about breakpoints:** tie them to your content, not to device names. When your layout starts to look uncomfortable — text lines too long, cards too cramped, navigation wrapping awkwardly — that's where the breakpoint goes. Don't add a breakpoint because "iPhone 15 is 390px wide." Add it because your design needs it at that point.

---

## Part 2 — CSS Grid In Depth

### The Mental Model

Grid is a **two-dimensional** layout system. You define rows and columns on a container, then place items into that grid — either explicitly (you say where they go) or implicitly (the browser figures it out).

```css
.grid {
  display: grid;
  grid-template-columns: 200px 1fr 1fr;  /* 3 columns: fixed, fluid, fluid */
  grid-template-rows: auto 1fr auto;     /* 3 rows: shrink, grow, shrink */
  gap: 1.5rem;
}
```

### The `fr` Unit

`fr` means **fraction of available space** — after fixed sizes and gaps are subtracted.

```css
/* Three equal columns */
grid-template-columns: 1fr 1fr 1fr;

/* Sidebar + main content (sidebar fixed, content takes the rest) */
grid-template-columns: 240px 1fr;

/* Weighted columns — middle gets twice as much */
grid-template-columns: 1fr 2fr 1fr;
```

### `repeat()` — Avoiding Repetition

```css
/* Without repeat */
grid-template-columns: 1fr 1fr 1fr 1fr;

/* With repeat */
grid-template-columns: repeat(4, 1fr);

/* Mixed — sidebar then three equal columns */
grid-template-columns: 240px repeat(3, 1fr);
```

### `auto-fit` vs `auto-fill`

Both create as many columns as fit, but they differ in how they handle empty space when there are fewer items than columns.

```css
/* auto-fit — collapses empty columns, stretches existing items to fill */
grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));

/* auto-fill — keeps empty column tracks, doesn't stretch */
grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
```

For service cards on Darya's Dental, `auto-fit` is what you want — three cards reflow to two, then to one, and each card fills its row naturally. `auto-fill` is useful when you want to maintain column positions even with fewer items (like a calendar grid).

### `minmax()` — The Responsive Column Formula

```css
grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
```

Read this as: "create as many columns as fit, where each column is at minimum 280px wide and at maximum 1 fraction of available space." No media queries needed — at 900px the grid shows three columns, at 600px it shows two, at 320px it shows one.

### Explicit Placement

When you need precise control over where items sit:

```css
.hero {
  grid-column: 1 / -1;       /* span full width (1 to last line) */
  grid-row: 1 / 2;
}

.sidebar {
  grid-column: 1 / 2;
  grid-row: 1 / 3;           /* spans two rows */
}

.main-content {
  grid-column: 2 / -1;
  grid-row: 1 / 2;
}
```

`-1` always refers to the last grid line. `1 / -1` means "from the first to the last line" — full width.

### Named Grid Areas — The Most Readable Layout Syntax

```css
.page {
  display: grid;
  grid-template-areas:
    "header  header"
    "sidebar content"
    "footer  footer";
  grid-template-columns: 240px 1fr;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
  gap: 0;
}

.page-header  { grid-area: header; }
.page-sidebar { grid-area: sidebar; }
.page-content { grid-area: content; }
.page-footer  { grid-area: footer; }

/* Mobile — stack everything */
@media (max-width: 767px) {
  .page {
    grid-template-areas:
      "header"
      "content"
      "sidebar"
      "footer";
    grid-template-columns: 1fr;
  }
}
```

The `grid-template-areas` value is a visual map of your layout. You can read it and immediately understand the structure — sidebar next to content, header and footer full-width. Changing the layout for mobile is just rewriting that string.

### Subgrid — Alignment Across Nested Components

A newer feature (now well-supported) that lets child grids align to their parent's tracks. Extremely useful for card grids where you want titles, images, and buttons to align across cards regardless of content length.

```css
.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.5rem;
}

.card {
  display: grid;
  grid-template-rows: auto 1fr auto;  /* image, content, button */
  /* Each card has 3 internal rows */
}

/* With subgrid — all cards share the PARENT's row tracks */
.card {
  grid-row: span 3;
  display: grid;
  grid-template-rows: subgrid;
}
```

Without subgrid, a card with a long title pushes the button down, misaligning all cards in the row. With subgrid, all cards share the same row heights — titles, content areas, and buttons all line up perfectly.

### Container Queries — The Future of Responsive Components

Media queries respond to the **viewport**. Container queries respond to the **parent element's size**. This is a major shift — components can now be truly self-contained and responsive regardless of where they're placed.

```css
/* Define a containment context on the parent */
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

/* The card responds to its container, not the viewport */
@container card (min-width: 400px) {
  .card {
    display: flex;
    flex-direction: row;
  }
}

@container card (max-width: 399px) {
  .card {
    display: block;
  }
}
```

This means a card component placed in a narrow sidebar is stacked, and the same card placed in a wide main area is horizontal — with zero changes to the component itself. The container determines its layout.

---

## Part 3 — Flexbox In Depth

### The Mental Model

Flexbox is **one-dimensional** — it lays items out along a single axis (row or column). It's for component-level layout: navbars, button groups, card internals, form rows.

```css
.nav {
  display: flex;
  flex-direction: row;          /* default — horizontal */
  align-items: center;          /* cross-axis alignment (vertical here) */
  justify-content: space-between; /* main-axis distribution */
  gap: 1.5rem;
}
```

### The Two Axes

```
flex-direction: row (default)

Main axis →  [item] [item] [item]
Cross axis ↓
```

```
flex-direction: column

Main axis ↓  [item]
             [item]
             [item]
Cross axis →
```

`justify-content` always controls the **main axis**. `align-items` always controls the **cross axis**. When you switch `flex-direction`, these axes flip — which is why centering feels different in row vs column layouts.

### `justify-content` Values

```css
justify-content: flex-start;     /* pack to start (default) */
justify-content: flex-end;       /* pack to end */
justify-content: center;         /* center along main axis */
justify-content: space-between;  /* first and last at edges, equal gaps between */
justify-content: space-around;   /* equal space around each item (half at edges) */
justify-content: space-evenly;   /* equal space everywhere including edges */
```

### `align-items` Values

```css
align-items: stretch;      /* fill cross-axis height (default) */
align-items: flex-start;   /* align to start of cross axis */
align-items: flex-end;     /* align to end */
align-items: center;       /* center on cross axis */
align-items: baseline;     /* align text baselines — useful for mixed font sizes */
```

### `flex` Shorthand — The Most Misunderstood Property

`flex` is shorthand for three properties: `flex-grow`, `flex-shrink`, `flex-basis`.

```css
flex: 1;          /* grow: 1, shrink: 1, basis: 0 — take equal share of space */
flex: 0 1 auto;   /* default — don't grow, can shrink, natural size */
flex: 0 0 200px;  /* fixed — don't grow, don't shrink, always 200px */
flex: 1 0 auto;   /* grow if space available, don't shrink below natural size */
```

```css
/* Navigation: logo fixed, links share remaining space */
.nav__logo    { flex: 0 0 auto; }     /* natural size, never grow/shrink */
.nav__links   { flex: 1; }            /* take all remaining space */
.nav__actions { flex: 0 0 auto; }     /* natural size, never grow/shrink */
```

### `flex-wrap` — Responsive Without Media Queries

```css
.tags {
  display: flex;
  flex-wrap: wrap;   /* items wrap to next line when they don't fit */
  gap: 0.5rem;
}
```

Combine with `flex-basis` for a poor-man's responsive grid:

```css
.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
}

.card {
  flex: 1 1 280px;  /* grow, shrink, minimum 280px — wraps automatically */
}
```

### `align-self` and `justify-self`

Override alignment for individual items:

```css
.nav__items {
  display: flex;
  align-items: center;      /* all items centered by default */
}

.nav__badge {
  align-self: flex-start;   /* this one item sticks to the top */
}
```

---

## Part 4 — Fluid Typography with `clamp()`

### How `clamp()` Works

```css
clamp(minimum, preferred, maximum)
```

The browser uses the preferred value (usually a viewport unit like `vw`) but clamps it between the minimum and maximum. The result scales smoothly with the viewport — no breakpoints needed.

```css
font-size: clamp(1rem, 2.5vw, 1.5rem);
/*                ↑         ↑        ↑
              never smaller  scales  never larger
              than 1rem      with    than 1.5rem
                             viewport */
```

### Building a Type Scale

```css
:root {
  --text-xs:   clamp(0.75rem,  1vw,   0.875rem);  /* small labels */
  --text-sm:   clamp(0.875rem, 1.5vw, 1rem);      /* captions, hints */
  --text-base: clamp(1rem,     2vw,   1.125rem);   /* body text */
  --text-lg:   clamp(1.125rem, 2.5vw, 1.375rem);  /* lead paragraphs */
  --text-xl:   clamp(1.375rem, 3vw,   1.75rem);   /* subheadings */
  --text-2xl:  clamp(1.75rem,  4vw,   2.5rem);    /* section headings */
  --text-3xl:  clamp(2.25rem,  5vw,   3.5rem);    /* page headings */
  --text-hero: clamp(3rem,     7vw,   5.5rem);    /* hero display text */
}
```

```css
body       { font-size: var(--text-base); }
h1         { font-size: var(--text-hero); }
h2         { font-size: var(--text-3xl); }
h3         { font-size: var(--text-2xl); }
h4         { font-size: var(--text-xl); }
p          { font-size: var(--text-base); }
small      { font-size: var(--text-sm); }
figcaption { font-size: var(--text-sm); }
```

One definition, zero breakpoints for type. The scale shifts fluidly from mobile to desktop.

### `clamp()` for Spacing Too

```css
:root {
  --space-xs:  clamp(0.25rem, 0.5vw,  0.5rem);
  --space-sm:  clamp(0.5rem,  1vw,    1rem);
  --space-md:  clamp(1rem,    2vw,    1.5rem);
  --space-lg:  clamp(1.5rem,  3vw,    2.5rem);
  --space-xl:  clamp(2rem,    5vw,    4rem);
  --space-2xl: clamp(3rem,    8vw,    7rem);
}
```

Section padding and component spacing now scale with the viewport the same way type does — everything breathes proportionally.

---

## Part 5 — CSS Custom Properties as a Design System

### What Properties Can and Can't Do

Custom properties (CSS variables) are more powerful than Sass variables because they're **live** — they can change at runtime, be read and set by JavaScript, and be scoped to specific elements.

```css
:root {
  --color-primary: #1D9E75;
}

/* Sass variable — static, compiled away */
$color-primary: #1D9E75;

/* CSS custom property — live in the browser */
--color-primary: #1D9E75;
```

```javascript
// Read a CSS custom property
getComputedStyle(document.documentElement)
  .getPropertyValue('--color-primary');  // "#1D9E75"

// Set a CSS custom property — updates the entire UI
document.documentElement.style
  .setProperty('--color-primary', '#0F6E56');
```

### A Complete Token System for Darya's Dental

```css
/* tokens.css */
:root {

  /* ── Brand Colors ── */
  --color-brand-primary:   #1D9E75;
  --color-brand-dark:      #0F6E56;
  --color-brand-darker:    #085041;
  --color-brand-light:     #E1F5EE;
  --color-brand-subtle:    #F0FAF6;

  /* ── Neutrals ── */
  --color-white:           #FFFFFF;
  --color-gray-50:         #F9FAFB;
  --color-gray-100:        #F3F4F6;
  --color-gray-200:        #E5E7EB;
  --color-gray-400:        #9CA3AF;
  --color-gray-600:        #4B5563;
  --color-gray-800:        #1F2937;
  --color-gray-900:        #111827;

  /* ── Semantic Colors (what the colors mean, not what they are) ── */
  --color-text-primary:    var(--color-gray-900);
  --color-text-secondary:  var(--color-gray-600);
  --color-text-muted:      var(--color-gray-400);
  --color-text-inverse:    var(--color-white);
  --color-text-accent:     var(--color-brand-primary);

  --color-surface:         var(--color-white);
  --color-surface-raised:  var(--color-gray-50);
  --color-surface-accent:  var(--color-brand-subtle);

  --color-border:          var(--color-gray-200);
  --color-border-strong:   var(--color-gray-400);
  --color-border-accent:   var(--color-brand-primary);

  --color-feedback-success: #059669;
  --color-feedback-error:   #DC2626;
  --color-feedback-warning: #D97706;
  --color-feedback-info:    #2563EB;

  /* ── Typography ── */
  --font-sans:   'Inter', system-ui, sans-serif;
  --font-serif:  'Playfair Display', Georgia, serif;
  --font-mono:   'Fira Code', monospace;

  --text-xs:     clamp(0.75rem,  1vw,   0.875rem);
  --text-sm:     clamp(0.875rem, 1.5vw, 1rem);
  --text-base:   clamp(1rem,     2vw,   1.125rem);
  --text-lg:     clamp(1.125rem, 2.5vw, 1.375rem);
  --text-xl:     clamp(1.375rem, 3vw,   1.75rem);
  --text-2xl:    clamp(1.75rem,  4vw,   2.5rem);
  --text-3xl:    clamp(2.25rem,  5vw,   3.5rem);
  --text-hero:   clamp(3rem,     7vw,   5.5rem);

  --leading-tight:  1.25;
  --leading-snug:   1.375;
  --leading-normal: 1.6;
  --leading-loose:  1.8;

  --tracking-tight:  -0.02em;
  --tracking-normal:  0;
  --tracking-wide:    0.05em;
  --tracking-wider:   0.1em;

  /* ── Spacing ── */
  --space-1:   0.25rem;
  --space-2:   0.5rem;
  --space-3:   0.75rem;
  --space-4:   1rem;
  --space-5:   1.25rem;
  --space-6:   1.5rem;
  --space-8:   2rem;
  --space-10:  2.5rem;
  --space-12:  3rem;
  --space-16:  4rem;
  --space-20:  5rem;
  --space-24:  6rem;

  /* ── Layout ── */
  --container-max:     1200px;
  --container-pad:     clamp(1rem, 5vw, 2.5rem);
  --section-gap:       clamp(3rem, 8vw, 7rem);

  /* ── Shape ── */
  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   16px;
  --radius-xl:   24px;
  --radius-full: 9999px;

  /* ── Elevation (shadows) ── */
  --shadow-sm:  0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md:  0 4px 6px -1px rgb(0 0 0 / 0.07);
  --shadow-lg:  0 10px 15px -3px rgb(0 0 0 / 0.08);

  /* ── Motion ── */
  --duration-fast:   150ms;
  --duration-normal: 250ms;
  --duration-slow:   400ms;
  --ease-standard:   cubic-bezier(0.4, 0, 0.2, 1);
  --ease-decelerate: cubic-bezier(0, 0, 0.2, 1);
  --ease-accelerate: cubic-bezier(0.4, 0, 1, 1);

  /* ── Z-index Scale ── */
  --z-below:   -1;
  --z-base:     0;
  --z-raised:   10;
  --z-dropdown: 100;
  --z-sticky:   200;
  --z-overlay:  300;
  --z-modal:    400;
  --z-toast:    500;
}
```

### Semantic vs Primitive Tokens

Notice the two-layer approach above — primitive tokens (`--color-gray-900`) and semantic tokens (`--color-text-primary: var(--color-gray-900)`).

This distinction matters for theming:

```css
/* Dark mode — you only change semantic tokens */
@media (prefers-color-scheme: dark) {
  :root {
    --color-text-primary:   var(--color-gray-50);
    --color-text-secondary: var(--color-gray-400);
    --color-surface:        var(--color-gray-900);
    --color-surface-raised: var(--color-gray-800);
    --color-border:         var(--color-gray-700, #374151);
  }
}
```

Every component using `var(--color-text-primary)` automatically switches — you changed one token, not hundreds of selectors.

### Scoping Custom Properties

Custom properties cascade and can be scoped to any element:

```css
/* Global tokens */
:root { --color-surface: white; }

/* Scoped override — only inside .section--dark */
.section--dark {
  --color-surface: #0F6E56;
  --color-text-primary: white;
  background: var(--color-surface);
  color: var(--color-text-primary);
}

/* Components inside .section--dark automatically use the dark values */
.section--dark .card {
  background: var(--color-surface);  /* picks up the scoped value */
}
```

---

## Part 6 — The Container Pattern

### Why You Need It

Every section of a page needs two separate concerns: a background that goes edge to edge, and content that stays readable within a max-width. The container pattern separates these cleanly.

```css
/* The container — reusable, never modified */
.container {
  width: 100%;
  max-width: var(--container-max);
  margin-inline: auto;
  padding-inline: var(--container-pad);
}
```

```html
<!-- Section background goes edge to edge -->
<section class="services" style="background: var(--color-brand-light);">

  <!-- Container keeps content readable -->
  <div class="container">
    <h2>Our Services</h2>
    <div class="services__grid">...</div>
  </div>

</section>
```

Never put `max-width` directly on a `<section>`. Put it on `.container` inside the section. This keeps your sections structurally flexible — you can change the background color or image without touching the content width logic.

### Multiple Container Widths

Some layouts need narrower containers — articles, forms, hero text. Define variants:

```css
.container           { max-width: var(--container-max); }        /* 1200px */
.container--narrow   { max-width: 720px; }   /* articles, forms */
.container--wide     { max-width: 1400px; }  /* full-bleed sections */
.container--text     { max-width: 65ch; }    /* optimal reading width */
```

`65ch` is a unit equal to the width of the "0" character in the current font. It produces the optimal line length for reading (60-75 characters) regardless of font size.

---

## Part 7 — Scalable File Architecture

### The Full Structure

```
assets/
└── css/
    ├── main.css              ← imports only, no styles
    │
    ├── base/
    │   ├── reset.css         ← normalize browser defaults
    │   ├── tokens.css        ← all custom properties
    │   ├── typography.css    ← base type styles
    │   └── root.css          ← html, body, scroll behavior
    │
    ├── layout/
    │   ├── container.css     ← .container and variants
    │   ├── grid.css          ← page-level grid systems
    │   ├── header.css        ← site header layout
    │   ├── footer.css        ← site footer layout
    │   └── sections.css      ← section spacing patterns
    │
    ├── components/
    │   ├── buttons.css
    │   ├── cards.css
    │   ├── nav.css
    │   ├── forms.css
    │   ├── hero.css
    │   └── testimonials.css
    │
    └── pages/
        ├── home.css          ← styles unique to index.html
        ├── services.css
        └── contact.css
```

```css
/* main.css — nothing but imports, in cascade order */

/* 1. Base — lowest specificity, broadest scope */
@import './base/reset.css';
@import './base/tokens.css';
@import './base/root.css';
@import './base/typography.css';

/* 2. Layout — structural patterns */
@import './layout/container.css';
@import './layout/grid.css';
@import './layout/header.css';
@import './layout/footer.css';
@import './layout/sections.css';

/* 3. Components — reusable UI pieces */
@import './components/buttons.css';
@import './components/cards.css';
@import './components/nav.css';
@import './components/forms.css';
@import './components/hero.css';
@import './components/testimonials.css';

/* 4. Pages — most specific, last in cascade */
@import './pages/home.css';
@import './pages/services.css';
@import './pages/contact.css';
```

### What Goes Where — Decision Rules

|You're styling…|File|
|---|---|
|A CSS custom property value|`tokens.css`|
|`html`, `body`, `*` reset|`reset.css`|
|`h1`, `p`, `a` default styles|`typography.css`|
|`.container`, page-wide grid|`layout/`|
|A reusable UI piece|`components/`|
|Something only on one page|`pages/`|

When in doubt: **as close to the component as possible, as general as necessary.**

---

## Part 8 — Responsive Images

Images are often the largest assets on a page. Done wrong, they're the biggest performance drag. Done right, they're invisible.

```html
<img
  src="clinic-800.webp"
  srcset="
    clinic-400.webp  400w,
    clinic-800.webp  800w,
    clinic-1200.webp 1200w,
    clinic-1600.webp 1600w
  "
  sizes="
    (max-width: 640px)  100vw,
    (max-width: 1024px) 80vw,
    1200px
  "
  alt="Darya's Dental reception area"
  width="1200"
  height="800"
  loading="lazy"
  decoding="async"
>
```

- `srcset` tells the browser which images exist and their widths
- `sizes` tells the browser how wide the image will be at each breakpoint
- The browser picks the best image for the screen density and viewport
- `width` and `height` prevent layout shift while the image loads
- `loading="lazy"` defers off-screen images
- `decoding="async"` lets the browser decode the image off the main thread

For your hero image — which is always visible on load — use `loading="eager"` (the default) and `fetchpriority="high"`.

---

## Putting It Together — A Responsive Page Section

```html
<section class="services">
  <div class="container">

    <header class="section-header">
      <h2 class="section-header__title">Our Services</h2>
      <p class="section-header__lead">
        Comprehensive dental care for the whole family.
      </p>
    </header>

    <div class="services__grid">
      <article class="card">
        <div class="card__icon">...</div>
        <h3 class="card__title">Teeth Whitening</h3>
        <p class="card__body">Professional whitening that lasts.</p>
        <a href="/services/whitening" class="btn btn--outline">Learn more</a>
      </article>
      <!-- more cards -->
    </div>

  </div>
</section>
```

```css
/* sections.css */
.services {
  padding-block: var(--section-gap);
  background: var(--color-surface-accent);
}

.section-header {
  text-align: center;
  max-width: 600px;
  margin-inline: auto;
  margin-bottom: var(--space-12);
}

.section-header__title {
  font-size: var(--text-3xl);
  font-family: var(--font-serif);
  color: var(--color-text-primary);
  line-height: var(--leading-tight);
  margin-bottom: var(--space-4);
}

.section-header__lead {
  font-size: var(--text-lg);
  color: var(--color-text-secondary);
  line-height: var(--leading-normal);
}

/* cards.css */
.services__grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: var(--space-8);
}

.card {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  padding: var(--space-8);
  display: grid;
  grid-template-rows: auto auto 1fr auto;
  gap: var(--space-4);
  transition: box-shadow var(--duration-normal) var(--ease-standard);
}

.card:hover {
  box-shadow: var(--shadow-lg);
}

.card__title {
  font-size: var(--text-xl);
  font-weight: 600;
  color: var(--color-text-primary);
  line-height: var(--leading-snug);
}

.card__body {
  font-size: var(--text-base);
  color: var(--color-text-secondary);
  line-height: var(--leading-normal);
}
```

---

That's the full picture. Every concept connects: tokens flow into components, components sit inside containers, containers live inside sections, sections stack into pages, and the file architecture keeps it all navigable as the project grows.