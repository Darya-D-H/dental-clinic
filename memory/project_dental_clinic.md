---
name: Project — Darya's Dental website
description: Structure, decisions, and CSS architecture for the dental clinic website
type: project
---

Static dental clinic website at C:\Users\Darya\Desktop\dental-clinic. Single-page (index.html) with pages/faq.html and pages/login.html.

**Why:** The user is building this as a learning project alongside study markdowns in markdown/Phase 1/ and markdown/Phase 2/.

**Key decisions made:**
- Zero JavaScript — user explicitly requested no JS. Mobile hamburger menu uses CSS checkbox trick (hidden `<input type="checkbox" id="nav-toggle">`). Card entrance animations use CSS `@keyframes` with `animation-delay` stagger via `--card-index` custom property.
- Footer year hardcoded to 2026 (update manually each year).
- Fonts loaded with simple `<link rel="stylesheet">` (no JS `media="print" onload` trick).

**Bug fixed:** `.btn--primary:hover` text was invisible because `a:hover { color: var(--primary-dark) }` in typography.css (specificity 0,1,1) overrode `.btn--primary { color: white }` (specificity 0,1,0) at hover time. Fixed by adding `color: var(--text-on-primary)` to `.btn--primary:hover` in buttons.css.

**How to apply:** Always check that `.btn--primary:hover` and any future button hover states explicitly set `color` to prevent the base `a:hover` rule from overriding.

**CSS architecture:** BEM naming + SMACSS file structure. All design tokens in css/base/root.css. Cards use `.scroll-reveal` class with `--card-index` CSS custom property for stagger timing. CLAUDE.md requires all markdown/Phase 1 and markdown/Phase 2 design features to be applied on every prompt.

**Service card structure (updated):** Icons removed. Cards now use the same image-on-top layout as doctor cards — `.service-card__image-wrap` (4:3 aspect-ratio placeholder, `--bg-subtle` background) at the top, then `.service-card__body` (padding wrapper with flex column) containing `.service-card__title`, `.service-card__desc`, and a CTA button. Each card has an HTML comment showing the image `<img>` tag to uncomment when real images are ready.
