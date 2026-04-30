# Darya's Dental

A single-page static website for a dental clinic, built with vanilla HTML and CSS. No JavaScript, no frameworks, no build step — open `index.html` in a browser and it works.

---

## Sections

| Section | Anchor |
|---|---|
| Hero | `#hero` |
| Services | `#services` |
| Our Team | `#doctors` |
| Contact & Booking | `#contact` |

---

## Folder Structure

```
dental-clinic/
├── index.html                  ← Entire site lives here
├── css/
│   ├── main.css                ← Import cascade only (no styles)
│   ├── base/
│   │   ├── root.css            ← All design tokens (colors, spacing, radius, motion)
│   │   ├── reset.css           ← Box-sizing, scroll padding, browser normalisation
│   │   └── typography.css      ← Heading scale, body text, .sr-only, skip link
│   ├── layout/
│   │   ├── containers.css      ← .container, .container--narrow
│   │   ├── grid.css            ← Generic grid utilities
│   │   ├── header.css          ← Site header wrapper
│   │   ├── sections.css        ← Section spacing, contact layout, scroll-reveal animation
│   │   └── footer.css          ← Dark footer, four-column grid, social icons
│   └── components/
│       ├── nav.css             ← Full-width glass nav, hamburger, mobile dropdown
│       ├── hero.css            ← Hero section, fadeUp keyframes
│       ├── buttons.css         ← .btn BEM system (primary, outline, ghost, sizes)
│       ├── cards.css           ← .service-card, .doctor-card + grids
│       └── forms.css           ← .field wrapper (contact form)
├── assets/
│   ├── icons/                  ← tooth-logo.webp, Instagram/Facebook/WhatsApp/TikTok SVGs
│   └── images/                 ← doctor-male.png, doctor-female.png
└── markdown/                   ← Design guidelines (HTML semantics, CSS, a11y, responsive, animation, tokens, BEM, typography)
```

---

## Stack

- **HTML5 / CSS3** — no frameworks, no preprocessors
- **Zero JavaScript** — all interactivity (mobile nav, card animations) is CSS-only
- **Design tokens** — all colors, spacing, radius, and motion values live in `css/base/root.css`
- **BEM naming** — `.block__element--modifier` throughout
- **Mobile-first** — base styles target mobile, `@media (min-width: …)` scales up
- **Google Fonts** — Playfair Display (headings), DM Sans (body)

---

## Key Techniques

**Mobile nav — no JS**
Uses a hidden `<input type="checkbox">` and CSS sibling selectors. Clicking the hamburger toggles `.nav-toggle-check:checked ~ .nav-mobile`.

**Card entrance animations — no JS**
`.scroll-reveal` uses `@keyframes fadeUp` with `animation-delay: calc(var(--card-index) * 80ms)` staggered via a CSS custom property on each card element.

**Full-width glass nav**
`position: fixed; top: 0; width: 100%; backdrop-filter: blur(20px)` — no floating pill, no border-radius. `scroll-padding-top` on `html` compensates for the fixed bar so anchor links land correctly.

**Social icons**
SVGs in `assets/icons/` render at full brand color. Never apply `filter: brightness(0) invert(1)` — it destroys the color.

---

## Design Tokens (root.css)

| Token | Value | Used for |
|---|---|---|
| `--primary` | `#2e86c8` | Buttons, links, nav active |
| `--primary-dark` | `#1a65a0` | Button hover |
| `--primary-light` | `#e8f3fb` | Hero tint, card hover bg |
| `--accent` | `#12b896` | Teal highlights |
| `--font-display` | Playfair Display | All headings |
| `--font-body` | DM Sans | Body text, buttons |
| `--header-h` | `68px` | Fixed nav height |
| `--container` | `1160px` | Max page width |

To retheme the site, edit only `css/base/root.css`.
