# Dental Clinic

A static website for a dental clinic, built with vanilla HTML and CSS using a token-based design system.

## Pages

| Page | File |
|---|---|
| Home | `index.html` |
| Login | `pages/login.html` |
| Sign Up | `pages/sign-up.html` |
| FAQ | `pages/faq.html` |

## Folder Structure

```
dental-clinic/
├── index.html
├── pages/
│   ├── login.html
│   ├── sign-up.html
│   └── faq.html
├── css/
│   ├── main.css              # Import cascade (no styles here)
│   ├── base/
│   │   ├── reset.css
│   │   ├── root.css          # Design tokens (CSS custom properties)
│   │   └── typography.css
│   ├── layout/
│   │   ├── containers.css
│   │   ├── grid.css
│   │   ├── header.css
│   │   ├── footer.css
│   │   └── sections.css
│   ├── components/
│   │   ├── buttons.css
│   │   ├── cards.css
│   │   ├── nav.css
│   │   ├── forms.css
│   │   └── hero.css
│   └── pages/
│       ├── login.css
│       ├── sign-up.css
│       └── faq.css
├── assets/
│   ├── icons/
│   ├── images/
│   └── videos/
└── js/
```

## Stack

- HTML5, CSS3 — no frameworks
- CSS architecture: [CUBE CSS](https://cube.fyi/)
- Design tokens via CSS custom properties (`root.css`)
- Typography: Playfair Display (headings), DM Sans (body) via Google Fonts
