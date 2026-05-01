# Darya's Dental — Customization Guide

A plain-language reference for editing every part of this website without touching code you don't need to understand.

---

## What Changed (Latest First)

### Deleted empty `pages/` folders (`pages/`, `css/pages/`)
Both the root-level `pages/` folder and `css/pages/` subfolder were empty and have been removed. The site is fully contained in `index.html`; no separate page files exist or are planned.

### Service cards redesigned to image-based layout (`index.html`, `css/components/cards.css`)
Removed emoji icons from all 6 service cards. Each card now has a `.service-card__image-wrap` placeholder at the top (matching the doctor card layout — image zone on top, text body below). The description paragraph class was renamed from `.service-card__body` to `.service-card__desc`; `.service-card__body` is now the padding wrapper. To add an image to any card, place the file in `assets/images/` and add a `<img>` tag inside `.service-card__image-wrap`.

### Navigation bar changed from floating pill to full-width glass bar (`css/components/nav.css`, `css/components/hero.css`, `css/base/reset.css`)
The pill-shaped floating nav (centered, limited width, rounded corners) was replaced with a full-width frosted-glass bar fixed to the top of the viewport. The blur/glass effect is preserved. On mobile, the hamburger dropdown now spans the full width of the nav bar instead of being a small centered panel. `scroll-padding-top` was added to `html` so anchor links scroll to the correct position below the fixed bar.

### Social media icons fixed (`css/layout/footer.css`)
The icons were invisible because the CSS was applying `filter: brightness(0) invert(1)` — this crushed all color to black then flipped to white, destroying the brand colors of the SVGs. The fix was to remove the filter entirely and let Instagram, Facebook, WhatsApp, and TikTok icons render in their original colors. Size bumped from 18px to 22px for better clarity.

### Earlier changes
- All JavaScript removed from `index.html`. The site is now fully JS-free.
- Mobile hamburger uses a CSS checkbox trick.
- Card entrance animations use CSS `@keyframes` with `animation-delay` stagger.
- "Book an Appointment" button hover bug fixed (white text was being overridden by `a:hover { color }` in `typography.css`).

---

## File Structure

```
dental-clinic/
├── index.html              ← The main page (everything you see)
├── css/
│   ├── main.css            ← Imports all other CSS files (don't edit this)
│   ├── base/
│   │   ├── root.css        ← ALL color and design tokens (start here to retheme)
│   │   ├── reset.css       ← Browser default normalisation
│   │   └── typography.css  ← Font sizes, heading styles, link styles
│   ├── layout/
│   │   ├── containers.css  ← Page width and max-width
│   │   ├── sections.css    ← Section spacing, contact layout, card animations
│   │   ├── header.css      ← Site header wrapper
│   │   ├── footer.css      ← Footer grid and styles
│   │   └── grid.css        ← Generic grid utilities
│   └── components/
│       ├── buttons.css     ← All button styles (.btn, .btn--primary, etc.)
│       ├── cards.css       ← Service cards, doctor cards
│       ├── nav.css         ← Navigation bar, mobile menu, hamburger
│       ├── hero.css        ← Hero section styles and animations
│       └── forms.css       ← Form and input styles
└── assets/
    ├── images/             ← doctor-male.png, doctor-female.png
    └── icons/              ← tooth-logo.webp, social media SVGs
```

---

## Changing Colors

All colors live in one place: **`css/base/root.css`**.

Open that file and look for the `:root { }` block. Here are the most important ones:

| Variable | What it controls | Default |
|---|---|---|
| `--primary` | Buttons, links, nav active state | `#2e86c8` (blue) |
| `--primary-dark` | Button hover background | `#1a65a0` (dark blue) |
| `--primary-light` | Hero background tint, card hover bg | `#e8f3fb` (pale blue) |
| `--accent` | Green accent (login page buttons) | `#12b896` (teal) |
| `--bg` | Page background | `#ffffff` (white) |
| `--bg-secondary` | Alternate section background | `#f5f8fa` (off-white) |
| `--text` | Main text color | `#1a2535` (near-black) |
| `--text-secondary` | Body text, descriptions | `#486073` (slate) |

**Example — change the brand color to purple:**
```css
/* In css/base/root.css */
--primary:        #7c3aed;
--primary-dark:   #5b21b6;
--primary-light:  #ede9fe;
```

Every button, link, nav item, and highlight updates automatically.

---

## Changing Fonts

Fonts are loaded in two places:

**1. The `<head>` of `index.html`** — change the Google Fonts URL to load different font families.

**2. `css/base/root.css`** — change which fonts are used:
```css
--font-display: 'Playfair Display', Georgia, serif;   /* headings */
--font-body:    'DM Sans', 'Segoe UI', sans-serif;    /* everything else */
```

**Example — switch body font to Inter:**
1. Add `&family=Inter:wght@400;500;600` to the Google Fonts URL in `index.html`
2. Change `--font-body: 'Inter', sans-serif;` in `root.css`

---

## Editing Hero Text

Open `index.html` and find the `<!-- ── HERO ──` section (around line 70).

```html
<p class="hero__eyebrow text-overline">Trusted dental care</p>  ← small label above heading

<h1 id="hero-heading" class="hero__title">
  Your Smile,<br>Our Priority                                    ← main heading
</h1>

<p class="hero__subtitle">
  We provide gentle, modern dental care...                       ← paragraph below heading
</p>
```

Change the text inside those tags. The `<br>` inside `hero__title` creates the line break — remove it to put the heading on one line.

---

## Adding a Service Card

Find the `<!-- SERVICE CARD GRID -->` comment in `index.html` (around line 117). Duplicate any `<article>` block and update:

```html
<article class="service-card scroll-reveal" style="--card-index: 6" role="listitem">
  <div class="service-card__image-wrap">
    <img src="assets/images/service-xray.jpg" alt="X-Ray Imaging">
    <!-- Leave the <img> out (or keep just the div) to show an empty placeholder -->
  </div>
  <div class="service-card__body">
    <h3 class="service-card__title">X-Ray Imaging</h3>
    <p class="service-card__desc">
      Digital X-rays with minimal radiation for precise diagnostics.
    </p>
    <a href="#contact" class="btn btn--outline btn--sm">Learn More</a>
  </div>
</article>
```

Increment `--card-index` by 1 from the last card. This controls the stagger delay in the entrance animation.

To add a service image later: place the image file in `assets/images/`, then replace the HTML comment inside `.service-card__image-wrap` with a real `<img>` tag.

---

## Removing a Service Card

Delete the entire `<article class="service-card ...">...</article>` block for that card. The grid automatically reflows — no other changes needed.

---

## Adding a Doctor Card

Find the `<!-- DOCTOR CARD GRID -->` comment (around line 155). Duplicate any `<article>` block:

```html
<article class="doctor-card scroll-reveal" style="--card-index: 3" role="listitem">
  <div class="doctor-card__photo-wrap">
    <img
      class="doctor-card__photo"
      src="assets/images/doctor-male.png"
      alt="Dr. Ahmad Karimi, Oral Surgeon"
      width="280"
      height="280"
      loading="lazy"
      decoding="async"
    >
  </div>
  <div class="doctor-card__body">
    <h3 class="doctor-card__name">Dr. Ahmad Karimi</h3>
    <p class="doctor-card__specialty">Oral Surgeon</p>
    <p class="doctor-card__bio">
      Specialised in complex extractions and jaw surgery with 8 years of experience.
    </p>
  </div>
</article>
```

Use `assets/images/doctor-male.png` or `assets/images/doctor-female.png`. To use a real photo, place the image in `assets/images/` and update the `src` attribute.

---

## Updating Contact Information

In `index.html`, find the `<!-- Contact info -->` block inside the `#contact` section. Update the text in each `<li>`:

- **Address** — change the text inside `<p>123 Dental Street...</p>`
- **Phone** — update both the `href="tel:+964..."` and the visible `+964...` text
- **Email** — update both the `href="mailto:..."` and the visible email address
- **Hours** — change the two `<p>` lines under the Hours item

The footer has identical contact details — search for `<!-- Contact details -->` in the footer and update those too.

---

## Updating the Footer Year

The footer year is hardcoded. Find this line in `index.html`:

```html
<p>&copy; 2026 Darya's Dental. All rights reserved.</p>
```

Change `2026` to the current year.

---

## Changing Social Media Icons

The social icons in the footer are SVG files in `assets/icons/`. They render in their original brand colors — do not add a CSS `filter` to them or the colors will be destroyed.

To swap an icon for a different platform:
1. Place your new `.svg` file in `assets/icons/`
2. In `index.html`, find the `footer-social` block and update the `src` and `aria-label`:

```html
<a href="https://your-profile-url" aria-label="Follow us on Twitter">
  <img src="assets/icons/Twitter.svg" alt="" width="20" height="20">
</a>
```

The icon size is controlled in `css/layout/footer.css` under `.footer-social img { width: 22px; height: 22px; }`.

---

## Changing Navigation Links

The nav links appear in three places in `index.html` — update all three to keep them in sync:

1. `.nav-links` (desktop nav, around line 40)
2. `.nav-mobile` (mobile dropdown, around line 57)
3. `.footer-nav` with `aria-label="Quick links"` (footer, near the bottom)

Each link uses `href="#section-id"` to scroll to a section on the page (e.g. `href="#services"` scrolls to `<section id="services">`).

---

## How the Mobile Menu Works (No JavaScript)

The hamburger menu uses a hidden HTML checkbox and a CSS sibling selector — no JavaScript:

```html
<!-- Hidden checkbox acts as the toggle state -->
<input type="checkbox" id="nav-toggle" class="nav-toggle-check">

<!-- The label triggers the checkbox when clicked -->
<label for="nav-toggle" class="nav-hamburger-btn">...</label>

<!-- CSS shows the menu when checkbox is checked -->
<!-- In css/components/nav.css: -->
<!-- .nav-toggle-check:checked ~ .nav-mobile { display: block; } -->
```

Clicking the hamburger icon checks/unchecks the hidden checkbox, which CSS uses to show or hide the `.nav-mobile` dropdown. The checkbox itself is invisible (positioned off-screen with opacity 0).

---

## Changing Button Colors

Buttons use two CSS classes: a base `.btn` class and a modifier like `.btn--primary` or `.btn--outline`.

To change the primary button color, edit `css/components/buttons.css`:

```css
.btn--primary {
  background: var(--primary);      /* change --primary in root.css instead */
  border-color: var(--primary);
  color: var(--text-on-primary);   /* text color (white by default) */
}

.btn--primary:hover {
  background: var(--primary-dark); /* hover background */
  color: var(--text-on-primary);   /* keep text white on hover */
}
```

The cleanest approach is to change `--primary` and `--primary-dark` in `root.css` — all primary buttons update at once.

---

## Adjusting Section Spacing

Section top/bottom padding is controlled in `css/layout/sections.css`:

```css
.section {
  padding-block: clamp(3rem, 8vw, 6rem); /* min 3rem, max 6rem, scales with viewport */
}
```

`clamp(min, preferred, max)` — the middle value scales fluidly. Increase the max to add more breathing room.

---

## Adjusting Card Entrance Animation Speed

Cards fade up on page load. To change the timing, edit `css/layout/sections.css`:

```css
.scroll-reveal {
  animation: fadeUp 600ms var(--ease) both;          /* 600ms duration */
  animation-delay: calc(var(--card-index, 0) * 80ms); /* 80ms stagger between cards */
}
```

- Increase `600ms` for a slower, more dramatic entrance
- Decrease `80ms` for faster stagger (cards appear more simultaneously)
- Increase `80ms` for a more pronounced stagger effect

---

---

## Common CSS Variable Quick Reference

```
css/base/root.css
│
├── Colors
│   ├── --primary              Main brand blue
│   ├── --primary-dark         Darker blue (hover states)
│   ├── --primary-light        Pale blue (backgrounds)
│   ├── --accent               Teal green (login page)
│   ├── --bg                   Page background
│   ├── --bg-secondary         Alt section background
│   ├── --text                 Main text color
│   └── --text-secondary       Body/description text
│
├── Borders & Radius
│   ├── --border               Default border color
│   ├── --radius               Default corner radius (10px)
│   ├── --radius-lg            Large radius (16px)
│   └── --radius-pill          Fully rounded (999px)
│
├── Shadows
│   ├── --shadow-xs            Subtle lift
│   ├── --shadow-sm            Card shadow
│   └── --shadow-md            Hover card shadow
│
├── Motion
│   ├── --ease                 Standard easing curve
│   ├── --transition-fast      0.15s transitions
│   └── --transition           0.25s transitions
│
└── Layout
    ├── --header-h             Nav height (68px)
    └── --container            Max page width (1160px)
```
