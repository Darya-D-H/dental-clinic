___
## Theming & Dark Mode — Deep Dive

## Part 1 — How Theming Actually Works

Before writing a line of CSS, it helps to understand what theming means structurally. A theme is a **named set of token values** applied to a scope. The components themselves never change — only the token values they reference do.

```
Component references token → Token has a value → Value comes from the active theme
     .btn { color: var(--color-text-primary); }
                        ↑
              :root { --color-text-primary: #111; }   ← light theme
              [data-theme="dark"] { --color-text-primary: #f5f5f5; }  ← dark theme
```

The component is completely unaware of which theme is active. It just asks for `--color-text-primary` and gets whatever the current theme provides. This is the core principle — and it only works if your components reference semantic tokens, never raw values.

```css
/* ❌ This can never be themed — raw value is hardcoded */
.btn { background: #1D9E75; }

/* ✅ This themes automatically — value comes from wherever the token is defined */
.btn { background: var(--color-brand-primary); }
```

---

## Part 2 — The Token Architecture in Full

A robust token system has three layers. Most people only build one.

### Layer 1 — Primitive Tokens (the palette)

Raw values with no meaning attached. These never change between themes — they're the full range of available values.

```css
:root {

  /* ── Complete color palette ── */

  /* Teal ramp — your brand */
  --teal-50:  #E1F5EE;
  --teal-100: #9FE1CB;
  --teal-200: #5DCAA5;
  --teal-400: #1D9E75;
  --teal-600: #0F6E56;
  --teal-800: #085041;
  --teal-900: #04342C;

  /* Gray ramp */
  --gray-0:   #FFFFFF;
  --gray-50:  #F9FAFB;
  --gray-100: #F3F4F6;
  --gray-200: #E5E7EB;
  --gray-300: #D1D5DB;
  --gray-400: #9CA3AF;
  --gray-500: #6B7280;
  --gray-600: #4B5563;
  --gray-700: #374151;
  --gray-800: #1F2937;
  --gray-900: #111827;
  --gray-950: #030712;

  /* Feedback ramps */
  --green-400: #22C55E;
  --green-600: #16A34A;
  --green-100: #DCFCE7;

  --red-400:   #F87171;
  --red-600:   #DC2626;
  --red-100:   #FEE2E2;

  --amber-400: #FBBF24;
  --amber-600: #D97706;
  --amber-100: #FEF3C7;

  --blue-400:  #60A5FA;
  --blue-600:  #2563EB;
  --blue-100:  #DBEAFE;
}
```

### Layer 2 — Semantic Tokens (meaning)

These map primitives to roles. They change per theme. Components reference only these.

```css
/* Light theme — default */
:root {

  /* Text */
  --color-text-primary:   var(--gray-900);
  --color-text-secondary: var(--gray-600);
  --color-text-muted:     var(--gray-400);
  --color-text-disabled:  var(--gray-300);
  --color-text-inverse:   var(--gray-0);
  --color-text-accent:    var(--teal-600);
  --color-text-accent-hover: var(--teal-800);

  /* Surfaces */
  --color-surface-page:    var(--gray-50);
  --color-surface-base:    var(--gray-0);
  --color-surface-raised:  var(--gray-0);
  --color-surface-overlay: var(--gray-0);
  --color-surface-sunken:  var(--gray-100);
  --color-surface-accent:  var(--teal-50);

  /* Borders */
  --color-border-subtle:  var(--gray-100);
  --color-border-default: var(--gray-200);
  --color-border-strong:  var(--gray-400);
  --color-border-accent:  var(--teal-400);

  /* Brand */
  --color-brand-primary:   var(--teal-400);
  --color-brand-hover:     var(--teal-600);
  --color-brand-active:    var(--teal-800);
  --color-brand-subtle:    var(--teal-50);
  --color-brand-on:        var(--gray-0);  /* text ON brand background */

  /* Feedback */
  --color-success-text:    var(--green-600);
  --color-success-bg:      var(--green-100);
  --color-success-border:  var(--green-400);

  --color-error-text:      var(--red-600);
  --color-error-bg:        var(--red-100);
  --color-error-border:    var(--red-400);

  --color-warning-text:    var(--amber-600);
  --color-warning-bg:      var(--amber-100);
  --color-warning-border:  var(--amber-400);

  --color-info-text:       var(--blue-600);
  --color-info-bg:         var(--blue-100);
  --color-info-border:     var(--blue-400);
}
```

### Layer 3 — Component Tokens (optional, for large systems)

Component-specific tokens that map semantic tokens to specific roles within a component. Useful when a component has many states that vary between themes.

```css
/* Defined once, referenced inside the component */
:root {
  --btn-bg:           var(--color-brand-primary);
  --btn-bg-hover:     var(--color-brand-hover);
  --btn-bg-active:    var(--color-brand-active);
  --btn-text:         var(--color-brand-on);
  --btn-radius:       var(--radius-md);
  --btn-padding-x:    var(--space-6);
  --btn-padding-y:    var(--space-3);

  --card-bg:          var(--color-surface-raised);
  --card-border:      var(--color-border-default);
  --card-radius:      var(--radius-lg);
  --card-padding:     var(--space-8);
  --card-shadow:      var(--shadow-md);
}
```

For smaller projects like Darya's Dental, two layers (primitives + semantics) is enough. Component tokens become valuable when you have a design system shared across multiple projects or teams.

---

## Part 3 — Dark Mode, Done Properly

### Strategy 1 — System Preference Only

The simplest approach. Responds to the OS setting automatically.

```css
/* Light is default (already in :root above) */

@media (prefers-color-scheme: dark) {
  :root {
    /* Text */
    --color-text-primary:   var(--gray-50);
    --color-text-secondary: var(--gray-400);
    --color-text-muted:     var(--gray-600);
    --color-text-disabled:  var(--gray-700);
    --color-text-inverse:   var(--gray-900);
    --color-text-accent:    var(--teal-200);
    --color-text-accent-hover: var(--teal-100);

    /* Surfaces */
    --color-surface-page:    var(--gray-950);
    --color-surface-base:    var(--gray-900);
    --color-surface-raised:  var(--gray-800);
    --color-surface-overlay: var(--gray-800);
    --color-surface-sunken:  var(--gray-950);
    --color-surface-accent:  var(--teal-900);

    /* Borders */
    --color-border-subtle:  var(--gray-800);
    --color-border-default: var(--gray-700);
    --color-border-strong:  var(--gray-500);
    --color-border-accent:  var(--teal-400);

    /* Brand */
    --color-brand-primary:  var(--teal-200);
    --color-brand-hover:    var(--teal-100);
    --color-brand-active:   var(--teal-50);
    --color-brand-subtle:   var(--teal-900);
    --color-brand-on:       var(--teal-900);

    /* Feedback — same hue, adjusted for dark backgrounds */
    --color-success-text:   var(--green-400);
    --color-success-bg:     #052e16;
    --color-success-border: var(--green-600);

    --color-error-text:     var(--red-400);
    --color-error-bg:       #450a0a;
    --color-error-border:   var(--red-600);

    --color-warning-text:   var(--amber-400);
    --color-warning-bg:     #451a03;
    --color-warning-border: var(--amber-600);

    --color-info-text:      var(--blue-400);
    --color-info-bg:        #172554;
    --color-info-border:    var(--blue-600);
  }
}
```

### Strategy 2 — Manual Toggle with System Preference Fallback

This is what most modern sites do — detect the system preference as the default, but let users override it. The preference is saved to `localStorage` so it persists across sessions.

The `data-theme` attribute on `<html>` is the toggle mechanism:

```css
/* Light — default */
:root,
[data-theme="light"] {
  --color-text-primary:    var(--gray-900);
  --color-surface-base:    var(--gray-0);
  --color-border-default:  var(--gray-200);
  /* ... all light tokens */
}

/* Dark — via attribute OR system preference */
[data-theme="dark"],
@media (prefers-color-scheme: dark) {
  :root:not([data-theme="light"]) {
    --color-text-primary:   var(--gray-50);
    --color-surface-base:   var(--gray-900);
    --color-border-default: var(--gray-700);
    /* ... all dark tokens */
  }
}
```

The `:not([data-theme="light"])` selector is the key — it means the media query only applies when the user hasn't explicitly chosen light. If they've forced light mode, the media query is ignored even if their OS is in dark mode.

The cleaner way to write this, avoiding the awkward `:not()`:

```css
/* 1. System preference — lowest priority */
@media (prefers-color-scheme: dark) {
  :root { /* dark tokens */ }
}

/* 2. Explicit choice — overrides system preference */
[data-theme="light"] { /* light tokens */ }
[data-theme="dark"]  { /* dark tokens */ }
```

The cascade handles priority — explicit `data-theme` always wins over the media query because it appears later in the stylesheet.

### The Toggle — HTML

```html
<button
  class="theme-toggle"
  aria-label="Switch to dark mode"
  aria-pressed="false"
>
  <svg class="theme-toggle__icon theme-toggle__icon--sun" aria-hidden="true">
    <!-- sun icon -->
  </svg>
  <svg class="theme-toggle__icon theme-toggle__icon--moon" aria-hidden="true">
    <!-- moon icon -->
  </svg>
</button>
```

### The Toggle — JavaScript

```javascript
const toggle = document.querySelector('.theme-toggle');
const root   = document.documentElement;

// Keys for localStorage
const STORAGE_KEY = 'daryas-dental-theme';
const DARK  = 'dark';
const LIGHT = 'light';

// Detect system preference
function getSystemPreference() {
  return window.matchMedia('(prefers-color-scheme: dark)').matches
    ? DARK
    : LIGHT;
}

// Get the active theme — stored choice takes priority
function getActiveTheme() {
  return localStorage.getItem(STORAGE_KEY) || getSystemPreference();
}

// Apply a theme to the document
function applyTheme(theme) {
  root.setAttribute('data-theme', theme);
  toggle.setAttribute('aria-label', `Switch to ${theme === DARK ? 'light' : 'dark'} mode`);
  toggle.setAttribute('aria-pressed', theme === DARK ? 'true' : 'false');
}

// Save and apply
function setTheme(theme) {
  localStorage.setItem(STORAGE_KEY, theme);
  applyTheme(theme);
}

// Toggle between light and dark
toggle.addEventListener('click', () => {
  const current = root.getAttribute('data-theme') || getSystemPreference();
  setTheme(current === DARK ? LIGHT : DARK);
});

// Initialize on page load
applyTheme(getActiveTheme());

// Respond to OS preference changes (if no manual choice is stored)
window.matchMedia('(prefers-color-scheme: dark)')
  .addEventListener('change', (e) => {
    if (!localStorage.getItem(STORAGE_KEY)) {
      applyTheme(e.matches ? DARK : LIGHT);
    }
  });
```

### Preventing the Flash of Wrong Theme

The biggest dark mode problem — the page loads in light mode, then JS runs and switches to dark. The user sees a flash. The fix is a tiny inline script in the `<head>`, before any CSS loads:

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- FODT prevention — must be inline, must be first -->
  <script>
    (function() {
      const stored = localStorage.getItem('daryas-dental-theme');
      const system = window.matchMedia('(prefers-color-scheme: dark)').matches
        ? 'dark' : 'light';
      document.documentElement.setAttribute('data-theme', stored || system);
    })();
  </script>

  <link rel="stylesheet" href="assets/css/main.css">
  <title>Darya's Dental</title>
</head>
```

This runs synchronously before the page renders — the correct theme is applied before the first paint. No flash.

---

## Part 4 — The `light-dark()` Function

A newer CSS function (now well-supported) that lets you define both theme values inline, without writing separate media queries or `data-theme` selectors.

```css
/* Old way — two separate rules */
:root { --color-text-primary: #111; }
@media (prefers-color-scheme: dark) {
  :root { --color-text-primary: #f5f5f5; }
}

/* New way — both values in one declaration */
:root {
  color-scheme: light dark;  /* required — tells the browser this element supports both */
  --color-text-primary: light-dark(#111, #f5f5f5);
}
```

You can use it directly on properties too:

```css
body {
  color-scheme: light dark;
  background: light-dark(var(--gray-0), var(--gray-950));
  color:       light-dark(var(--gray-900), var(--gray-50));
}

.card {
  background: light-dark(white, var(--gray-800));
  border:     1px solid light-dark(var(--gray-200), var(--gray-700));
}
```

For token-based systems, combining `light-dark()` with custom properties at the token level is clean:

```css
:root {
  color-scheme: light dark;

  --color-text-primary:   light-dark(var(--gray-900), var(--gray-50));
  --color-surface-base:   light-dark(var(--gray-0),   var(--gray-900));
  --color-border-default: light-dark(var(--gray-200), var(--gray-700));
  --color-brand-primary:  light-dark(var(--teal-400), var(--teal-200));
}
```

No media queries, no `data-theme` selectors — the browser handles the switching based on system preference. The limitation is that `light-dark()` responds only to system preference, not to a manual toggle. For a manual toggle you still need the `data-theme` attribute approach.

---

## Part 5 — Dark Mode for Images, SVGs, and Media

### Images

Photographs usually look fine in dark mode — they don't invert. But illustrations, logos, and diagrams often need dark variants.

```css
/* Reduce brightness and contrast slightly for photos in dark mode */
@media (prefers-color-scheme: dark) {
  img:not([data-no-dim]) {
    filter: brightness(0.9) contrast(0.95);
  }
}

/* Invert logos that are black-on-white */
.logo--dark-invert {
  filter: invert(1);
}

@media (prefers-color-scheme: dark) {
  .logo--dark-invert {
    filter: invert(1) hue-rotate(180deg);  /* invert + restore hue */
  }
}
```

For the Darya's Dental logo — if it's a dark teal on white, you'll want a separate white/light version for dark mode:

```html
<picture>
  <source
    srcset="assets/images/logo-light.svg"
    media="(prefers-color-scheme: dark)"
  >
  <img
    src="assets/images/logo-dark.svg"
    alt="Darya's Dental"
    class="site-logo"
  >
</picture>
```

When using a manual toggle, `<picture>` with `media` won't respond to `data-theme` — it only reads system preference. Use CSS instead:

```css
.logo-dark-variant  { display: none; }
.logo-light-variant { display: block; }

[data-theme="dark"] .logo-dark-variant  { display: block; }
[data-theme="dark"] .logo-light-variant { display: none; }
```

### SVGs

Inline SVGs (embedded in HTML) can reference CSS custom properties directly — they participate in the cascade:

```html
<svg viewBox="0 0 24 24" aria-hidden="true">
  <path
    d="M12 2L..."
    fill="var(--color-brand-primary)"  <!-- responds to theme automatically -->
    stroke="var(--color-border-default)"
  />
</svg>
```

External SVGs (`<img src="icon.svg">`) are isolated from your CSS — they can't read custom properties. For icons that need to theme, either inline them or use `currentColor`:

```css
/* currentColor inherits the element's text color */
.icon { color: var(--color-text-primary); }
```

```html
<svg viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
  <path d="M12 2L..."/>
</svg>
```

Now the icon color is controlled entirely by the CSS `color` property of its parent — and themes automatically.

### Backgrounds with `color-mix()`

A newer function that blends two colors — useful for generating hover states and tints without defining additional tokens:

```css
/* 80% brand color mixed with white — a light tint */
.card--accent {
  background: color-mix(in srgb, var(--color-brand-primary) 15%, white);
}

/* In dark mode, mix with dark surface instead */
[data-theme="dark"] .card--accent {
  background: color-mix(in srgb, var(--color-brand-primary) 20%, var(--gray-900));
}
```

---

## Part 6 — Multi-Brand Theming

If you ever build a system where multiple clients or products share the same components but different visual identities, multi-brand theming is the pattern. The structure is identical to dark mode — just more themes.

```css
/* Brand A — Darya's Dental */
[data-brand="daryas-dental"] {
  --color-brand-primary: #1D9E75;
  --color-brand-hover:   #0F6E56;
  --font-display:        'Playfair Display', serif;
  --radius-md:           8px;
}

/* Brand B — hypothetical second clinic */
[data-brand="city-dental"] {
  --color-brand-primary: #2563EB;
  --color-brand-hover:   #1D4ED8;
  --font-display:        'Libre Baskerville', serif;
  --radius-md:           4px;
}
```

```html
<html data-brand="daryas-dental" data-theme="light">
```

Components reference tokens only — they look completely different between brands without a single component rule changing.

---

## Part 7 — CSS `@layer` for Token Management

`@layer` lets you explicitly control the cascade order of your styles, regardless of where they appear in the source. This is particularly powerful for theming because it prevents token overrides from requiring higher specificity.

```css
/* Declare layer order — lower in the list = higher priority */
@layer reset, tokens, base, layout, components, utilities, themes;

@layer tokens {
  :root {
    --color-text-primary: var(--gray-900);
    --color-brand-primary: var(--teal-400);
  }
}

@layer themes {
  /* Even though this has the same specificity as the tokens layer,
     the themes layer is declared later so it wins */
  [data-theme="dark"] {
    --color-text-primary: var(--gray-50);
    --color-brand-primary: var(--teal-200);
  }
}

@layer components {
  /* Components reference tokens — they never need to know about themes */
  .btn {
    background: var(--color-brand-primary);
    color: var(--color-brand-on);
  }
}

@layer utilities {
  /* Utilities can override components without !important */
  .text-accent { color: var(--color-text-accent); }
}
```

The key advantage — `@layer` resolves specificity wars between your own styles. Utilities can override components without escalating to `!important`, and theme overrides don't need to fight component selectors.

---

## Part 8 — Toggle Button CSS

The theme toggle itself needs to look right in both modes, and communicate its state clearly:

```css
.theme-toggle {
  position: relative;
  width: 40px;
  height: 40px;
  border-radius: var(--radius-full);
  border: 1px solid var(--color-border-default);
  background: var(--color-surface-raised);
  color: var(--color-text-secondary);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition:
    background  var(--duration-fast) var(--ease-standard),
    border-color var(--duration-fast) var(--ease-standard),
    color        var(--duration-fast) var(--ease-standard);
}

.theme-toggle:hover {
  background: var(--color-surface-sunken);
  border-color: var(--color-border-strong);
  color: var(--color-text-primary);
}

.theme-toggle:focus-visible {
  outline: 2px solid var(--color-brand-primary);
  outline-offset: 3px;
}

/* Icon visibility controlled by theme */
.theme-toggle__icon { width: 18px; height: 18px; }

.theme-toggle__icon--moon { display: none; }
.theme-toggle__icon--sun  { display: block; }

[data-theme="dark"] .theme-toggle__icon--moon { display: block; }
[data-theme="dark"] .theme-toggle__icon--sun  { display: none; }

/* Smooth icon transition */
.theme-toggle__icon {
  transition: opacity var(--duration-fast) var(--ease-standard);
}
```

---

## Part 9 — The Full Implementation Checklist

When adding theming to a project, work through this in order:

```
1. ✅ Define primitive tokens (full color palette in :root)
2. ✅ Define semantic tokens (light theme defaults in :root)
3. ✅ Define dark semantic tokens (in data-theme="dark" and media query)
4. ✅ Add FODT prevention script to <head> (inline, before CSS)
5. ✅ Write the theme toggle JS (read localStorage, apply data-theme)
6. ✅ Build the toggle button (with aria-label and aria-pressed)
7. ✅ Handle images (picture element or CSS class swap)
8. ✅ Use currentColor or CSS variables in inline SVGs
9. ✅ Wrap body in smooth color transition (optional)
10. ✅ Test with system preference AND manual toggle
11. ✅ Test with JS disabled (system preference should still work)
12. ✅ Verify no FODT flash on hard reload
```

---

## The Mental Model in One Sentence

**Primitives are what colors exist. Semantics are what they mean. Themes are which semantic values are active. Components only ever speak to semantics — they're fully ignorant of both primitives and which theme is running.**
