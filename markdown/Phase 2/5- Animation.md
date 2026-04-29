___
## CSS Animations & Transitions

## Part 1 — Transitions

Transitions are the simpler of the two. They animate a property **from one state to another** — triggered by a change like `:hover`, `:focus`, a class being added, or a CSS variable updating.

### The Syntax

```css
transition: property duration easing delay;
```

```css
.btn {
  background: var(--color-brand-primary);

  /* Single property */
  transition: background 250ms ease;

  /* Multiple properties */
  transition:
    background  250ms ease,
    transform   200ms ease,
    box-shadow  250ms ease;

  /* All properties — convenient but expensive */
  transition: all 250ms ease;
}

.btn:hover {
  background: var(--color-brand-dark);
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}
```

Avoid `transition: all` in production. It watches every property for changes — including ones you don't intend to animate. Be explicit.

---

### The Four Transition Properties

```css
/* Which property to animate */
transition-property: background, transform;

/* How long it takes */
transition-duration: 250ms;

/* The timing curve (more on this below) */
transition-timing-function: ease;

/* Wait before starting */
transition-delay: 100ms;
```

---

### Easing Functions — The Feel of Motion

Easing defines the **rate of change** over time. It's the single biggest factor in whether an animation feels natural or robotic.

```css
/* Built-in keywords */
transition-timing-function: linear;      /* constant speed — mechanical, unnatural */
transition-timing-function: ease;        /* fast start, slow end — the default */
transition-timing-function: ease-in;     /* slow start, fast end — feels heavy */
transition-timing-function: ease-out;    /* fast start, slow end — feels light */
transition-timing-function: ease-in-out; /* slow start AND end — feels polished */
```

The keywords map to `cubic-bezier()` curves under the hood. You can write custom curves directly:

```css
/* cubic-bezier(x1, y1, x2, y2) */
transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);  /* Material Design standard */
transition-timing-function: cubic-bezier(0, 0, 0.2, 1);    /* decelerate — entering */
transition-timing-function: cubic-bezier(0.4, 0, 1, 1);    /* accelerate — leaving */
```

Using your token system from before:

```css
:root {
  --ease-standard:   cubic-bezier(0.4, 0, 0.2, 1);  /* most UI interactions */
  --ease-decelerate: cubic-bezier(0, 0, 0.2, 1);    /* elements entering the screen */
  --ease-accelerate: cubic-bezier(0.4, 0, 1, 1);    /* elements leaving the screen */
  --ease-bounce:     cubic-bezier(0.34, 1.56, 0.64, 1); /* slight overshoot */
}
```

The rule of thumb: **elements entering should decelerate** (fast then slow — they arrive and settle). **Elements leaving should accelerate** (slow then fast — they leave quickly, not reluctantly).

---

### What You Can and Can't Transition

Not all CSS properties are animatable. The browser can only interpolate between values it can calculate a midpoint for.

```css
/* ✅ Animatable — browser can calculate intermediate values */
opacity, transform, color, background-color,
border-color, box-shadow, width, height,
padding, margin, font-size, letter-spacing,
border-radius, clip-path, filter

/* ❌ Not animatable — binary, no in-between state */
display, visibility, font-family,
position, overflow, content
```

```css
/* Common pattern — visibility + opacity together */
.dropdown {
  opacity: 0;
  visibility: hidden;             /* hidden from keyboard too */
  transition:
    opacity    250ms ease,
    visibility 250ms ease;        /* visibility transitions between visible/hidden */
}

.dropdown.is-open {
  opacity: 1;
  visibility: visible;
}
```

Using `display: none` / `display: block` can't be transitioned. The element simply appears or disappears. The `opacity` + `visibility` pattern is the standard workaround.

---

### Performance — The GPU Rule

The browser repaints the page constantly. Animating certain properties forces the browser to **recalculate layout** on every frame, which is expensive and causes jank.

Properties that trigger layout recalculation (avoid animating):

```css
width, height, margin, padding, top, left, right, bottom,
font-size, line-height, border-width
```

Properties that the GPU can handle without layout recalculation (always prefer):

```css
transform   /* translate, scale, rotate, skew */
opacity     /* fade in/out */
filter      /* blur, brightness — GPU in modern browsers */
```

```css
/* ❌ Triggers layout — janky on slow devices */
.card:hover {
  margin-top: -4px;
  width: 105%;
}

/* ✅ GPU-composited — smooth on any device */
.card:hover {
  transform: translateY(-4px) scale(1.02);
}
```

The visual result is identical. The performance difference is significant. Always reach for `transform` and `opacity` first.

---

### Practical Transition Patterns

**Button hover:**

```css
.btn {
  background: var(--color-brand-primary);
  color: white;
  border-radius: var(--radius-md);
  padding: var(--space-3) var(--space-6);
  transition:
    background  var(--duration-fast) var(--ease-standard),
    transform   var(--duration-fast) var(--ease-standard),
    box-shadow  var(--duration-fast) var(--ease-standard);
}

.btn:hover {
  background: var(--color-brand-dark);
  transform: translateY(-1px);
  box-shadow: var(--shadow-md);
}

.btn:active {
  transform: translateY(0);
  box-shadow: none;
}
```

**Card lift:**

```css
.card {
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-sm);
  transition:
    transform   var(--duration-normal) var(--ease-decelerate),
    box-shadow  var(--duration-normal) var(--ease-decelerate);
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: var(--shadow-lg);
}
```

**Nav link underline:**

```css
.nav__link {
  position: relative;
  text-decoration: none;
}

.nav__link::after {
  content: '';
  position: absolute;
  bottom: -2px;
  left: 0;
  width: 100%;
  height: 2px;
  background: var(--color-brand-primary);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform var(--duration-normal) var(--ease-standard);
}

.nav__link:hover::after,
.nav__link[aria-current="page"]::after {
  transform: scaleX(1);
}
```

**Focus ring:**

```css
.btn,
.input,
.nav__link {
  outline: 2px solid transparent;
  outline-offset: 3px;
  transition: outline-color var(--duration-fast) var(--ease-standard);
}

.btn:focus-visible,
.input:focus-visible,
.nav__link:focus-visible {
  outline-color: var(--color-brand-primary);
}
```

**Color scheme transition:**

```css
/* Smooth dark/light mode switch */
body {
  transition:
    background-color var(--duration-slow) var(--ease-standard),
    color           var(--duration-slow) var(--ease-standard);
}
```

---

## Part 2 — Keyframe Animations

Where transitions react to state changes, `@keyframes` animations are **self-contained sequences** — they run independently, can loop, and can have multiple steps.

### The Syntax

```css
/* Define the animation */
@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(24px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Apply it */
.hero__title {
  animation: slideInUp 600ms ease-out forwards;
}
```

### The `animation` Shorthand

```css
animation: name duration easing delay iteration-count direction fill-mode;
```

```css
.hero__title {
  animation-name:            slideInUp;
  animation-duration:        600ms;
  animation-timing-function: var(--ease-decelerate);
  animation-delay:           0ms;
  animation-iteration-count: 1;
  animation-direction:       normal;
  animation-fill-mode:       forwards;  /* keeps end state after finishing */
  animation-play-state:      running;
}

/* Shorthand */
.hero__title {
  animation: slideInUp 600ms var(--ease-decelerate) forwards;
}
```

---

### `fill-mode` — Before and After

```css
/* none — element snaps back to original state after animation */
animation-fill-mode: none;

/* forwards — holds the final keyframe state */
animation-fill-mode: forwards;

/* backwards — applies the first keyframe state during delay */
animation-fill-mode: backwards;

/* both — backwards during delay, forwards after */
animation-fill-mode: both;
```

`forwards` is almost always what you want for entrance animations. Without it, an element fades in and then snaps back to invisible.

---

### Multi-Step Keyframes

Using percentages lets you define intermediate states:

```css
@keyframes pulse {
  0%   { transform: scale(1); }
  50%  { transform: scale(1.05); }
  100% { transform: scale(1); }
}

@keyframes shimmer {
  0%   { background-position: -200% 0; }
  100% { background-position:  200% 0; }
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  30%       { transform: translateY(-12px); }
  60%       { transform: translateY(-6px); }
  80%       { transform: translateY(-3px); }
}
```

---

### Building a Staggered Entrance

Staggered animations — where elements appear one after another — create a sense of hierarchy and draw attention through the page in sequence.

```css
@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Base animation — all cards use this */
.card {
  opacity: 0;  /* start invisible before animation fires */
  animation: fadeUp 500ms var(--ease-decelerate) both;
}

/* Stagger via delay — each card starts slightly later */
.card:nth-child(1) { animation-delay: 0ms; }
.card:nth-child(2) { animation-delay: 100ms; }
.card:nth-child(3) { animation-delay: 200ms; }
.card:nth-child(4) { animation-delay: 300ms; }
```

For dynamic lists where you don't know the count, use a CSS custom property:

```css
.card {
  animation: fadeUp 500ms var(--ease-decelerate) both;
  animation-delay: calc(var(--card-index, 0) * 100ms);
}
```

```html
<!-- Set the index via inline style -->
<article class="card" style="--card-index: 0">...</article>
<article class="card" style="--card-index: 1">...</article>
<article class="card" style="--card-index: 2">...</article>
```

This keeps the stagger logic entirely in CSS with zero JS.

---

### A Complete Hero Section Entrance

```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(24px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to   { opacity: 1; transform: scale(1); }
}

.hero__eyebrow {
  animation: fadeUp 500ms var(--ease-decelerate) 0ms   both;
}

.hero__title {
  animation: fadeUp 600ms var(--ease-decelerate) 100ms both;
}

.hero__subtitle {
  animation: fadeUp 500ms var(--ease-decelerate) 250ms both;
}

.hero__cta {
  animation: fadeUp 500ms var(--ease-decelerate) 400ms both;
}

.hero__image {
  animation: scaleIn 700ms var(--ease-decelerate) 200ms both;
}
```

The result: eyebrow label appears, then the heading (slightly delayed), then the subtitle, then the button, with the image scaling in from behind. Each element draws the eye down through the hierarchy.

---

## Part 3 — Scroll-Triggered Animations

Pure CSS can't yet detect scroll position for complex animations, but the **Intersection Observer API** gives you scroll-triggered class toggling with a few lines of JS, keeping the animation logic entirely in CSS.

```css
/* Default state — below the fold, invisible */
.animate-on-scroll {
  opacity: 0;
  transform: translateY(30px);
  transition:
    opacity   600ms var(--ease-decelerate),
    transform 600ms var(--ease-decelerate);
}

/* Class added by JS when element enters the viewport */
.animate-on-scroll.is-visible {
  opacity: 1;
  transform: translateY(0);
}
```

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('is-visible');
      observer.unobserve(entry.target); // animate once, then stop watching
    }
  });
}, {
  threshold: 0.15,       // trigger when 15% of element is visible
  rootMargin: '0px 0px -50px 0px'  // trigger 50px before it reaches the edge
});

document.querySelectorAll('.animate-on-scroll').forEach(el => {
  observer.observe(el);
});
```

Apply to any element:

```html
<article class="card animate-on-scroll" style="--card-index: 0">...</article>
<article class="card animate-on-scroll" style="--card-index: 1">...</article>
<article class="card animate-on-scroll" style="--card-index: 2">...</article>
```

---

## Part 4 — Loading States

### Skeleton Screens

Better than spinners — they show the shape of incoming content, reducing perceived load time.

```css
.skeleton {
  background: var(--color-gray-100);
  border-radius: var(--radius-sm);
  position: relative;
  overflow: hidden;
}

.skeleton::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(
    90deg,
    transparent 0%,
    rgba(255, 255, 255, 0.6) 50%,
    transparent 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s ease-in-out infinite;
}

@keyframes shimmer {
  from { background-position: -200% 0; }
  to   { background-position:  200% 0; }
}

/* Usage */
.skeleton--text   { height: 1rem; width: 80%; margin-bottom: 0.5rem; }
.skeleton--title  { height: 2rem; width: 60%; margin-bottom: 1rem; }
.skeleton--image  { aspect-ratio: 16/9; width: 100%; }
```

### Spinner

```css
@keyframes spin {
  to { transform: rotate(360deg); }
}

.spinner {
  width: 24px;
  height: 24px;
  border: 2px solid var(--color-gray-200);
  border-top-color: var(--color-brand-primary);
  border-radius: 50%;
  animation: spin 700ms linear infinite;
}
```

---

## Part 5 — Page Transitions

For multi-page sites like Darya's Dental, smooth page transitions make the experience feel like a single-page app. The **View Transitions API** (now well-supported in modern browsers) handles this natively.

```css
/* Default cross-fade — built in, no extra CSS needed */
/* Just add the JS call and the browser handles it */

/* Custom transitions using the API's pseudo-elements */
::view-transition-old(root) {
  animation: 300ms var(--ease-accelerate) both fadeOut;
}

::view-transition-new(root) {
  animation: 400ms var(--ease-decelerate) both fadeIn;
}

@keyframes fadeOut {
  to { opacity: 0; }
}

@keyframes fadeIn {
  from { opacity: 0; }
}
```

```javascript
// Wrap navigation in startViewTransition
document.querySelectorAll('a[href]').forEach(link => {
  link.addEventListener('click', async (e) => {
    const href = link.getAttribute('href');
    if (!href.startsWith('/')) return;   // external links, skip

    e.preventDefault();

    if (!document.startViewTransition) {
      window.location.href = href;       // fallback for unsupported browsers
      return;
    }

    document.startViewTransition(async () => {
      const response = await fetch(href);
      const html = await response.text();
      const doc = new DOMParser().parseFromString(html, 'text/html');

      document.title = doc.title;
      document.querySelector('main').replaceWith(doc.querySelector('main'));
      window.history.pushState({}, '', href);
    });
  });
});
```

This gives you smooth cross-fades between pages with native browser support and a CSS-only animation definition.

---

## Part 6 — Reduced Motion

Every animation you write needs this:

```css
/* Your animation */
.card {
  transition: transform var(--duration-normal) var(--ease-standard);
}

.hero__title {
  animation: fadeUp 600ms var(--ease-decelerate) both;
}

/* Reduced motion — strip or replace with instant/subtle alternatives */
@media (prefers-reduced-motion: reduce) {
  /* Option 1 — remove entirely */
  .hero__title {
    animation: none;
    opacity: 1;
    transform: none;
  }

  /* Option 2 — replace with a simple fade, no movement */
  .card {
    transition: opacity var(--duration-fast) ease;
  }

  /* Option 3 — global kill switch */
  *, *::before, *::after {
    animation-duration:        0.01ms !important;
    animation-iteration-count: 1      !important;
    transition-duration:       0.01ms !important;
  }
}
```

The global kill switch (Option 3) is a blunt instrument but reliable for large codebases where you can't annotate every animation individually.

---

## Part 7 — Common Animation Library for Darya's Dental

A reusable set of animations you can apply across the project:

```css
/* animations.css */

/* ── Keyframe Definitions ── */

@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes fadeUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeDown {
  from { opacity: 0; transform: translateY(-20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeLeft {
  from { opacity: 0; transform: translateX(20px); }
  to   { opacity: 1; transform: translateX(0); }
}

@keyframes fadeRight {
  from { opacity: 0; transform: translateX(-20px); }
  to   { opacity: 1; transform: translateX(0); }
}

@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.92); }
  to   { opacity: 1; transform: scale(1); }
}

@keyframes slideInDown {
  from { transform: translateY(-100%); }
  to   { transform: translateY(0); }
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50%       { opacity: 0.6; }
}

@keyframes shimmer {
  from { background-position: -200% 0; }
  to   { background-position:  200% 0; }
}

/* ── Utility Classes ── */

.anim-fade-in    { animation: fadeIn    500ms var(--ease-decelerate) both; }
.anim-fade-up    { animation: fadeUp    600ms var(--ease-decelerate) both; }
.anim-fade-down  { animation: fadeDown  600ms var(--ease-decelerate) both; }
.anim-scale-in   { animation: scaleIn   500ms var(--ease-decelerate) both; }

/* Apply stagger delay via custom property */
.anim-delay { animation-delay: calc(var(--delay, 0) * 100ms); }

/* Scroll-triggered base state */
.scroll-reveal {
  opacity: 0;
  transform: translateY(24px);
  transition:
    opacity   600ms var(--ease-decelerate),
    transform 600ms var(--ease-decelerate);
}

.scroll-reveal.is-visible {
  opacity: 1;
  transform: translateY(0);
}

/* ── Reduced Motion ── */
@media (prefers-reduced-motion: reduce) {
  .anim-fade-in,
  .anim-fade-up,
  .anim-fade-down,
  .anim-scale-in {
    animation: none;
    opacity: 1;
    transform: none;
  }

  .scroll-reveal {
    opacity: 1;
    transform: none;
    transition: none;
  }
}
```

Usage anywhere in your HTML:

```html
<h1 class="hero__title anim-fade-up">Welcome to Darya's Dental</h1>

<p class="hero__subtitle anim-fade-up anim-delay" style="--delay: 1">
  Trusted dental care in Baghdad
</p>

<a class="btn anim-fade-up anim-delay" style="--delay: 2" href="/contact">
  Book an appointment
</a>

<article class="card scroll-reveal" style="--card-index: 0">...</article>
```

---

That's the full picture — from the basics of transitions through to page transitions, scroll-triggered reveals, loading states, and a reusable animation library. The key principles to hold onto: always prefer `transform` and `opacity` for performance, always include `prefers-reduced-motion`, and keep animation purposeful — it should guide attention and communicate state, not decorate for its own sake.