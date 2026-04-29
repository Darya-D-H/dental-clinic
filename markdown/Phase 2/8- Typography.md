___
## Typography in Frontend Design

Typography is the discipline most developers underinvest in and most users notice without knowing why. A page with mediocre layout but excellent typography feels more trustworthy and readable than the reverse. It's the closest thing frontend has to invisible craft.

---

## Part 1 — The Fundamentals

### Typeface vs Font

These are used interchangeably but mean different things.

**Typeface** — the design family. Playfair Display, Inter, Fira Code.

**Font** — a specific instance within a typeface. Playfair Display Bold Italic at 24px is a font. The distinction matters when loading — you load individual fonts, but you design with typefaces.

### The Four Typeface Categories

**Serif** — strokes end in small decorative feet. Conveys tradition, authority, trustworthiness. Common in editorial, legal, medical, luxury contexts. Examples: Playfair Display, Lora, Libre Baskerville, Georgia.

**Sans-serif** — clean strokes with no feet. Conveys modernity, clarity, accessibility. Dominant in UI and digital products. Examples: Inter, DM Sans, Outfit, Geist.

**Monospace** — every character has identical width. Conveys precision, technical accuracy. Used for code and data. Examples: Fira Code, JetBrains Mono, IBM Plex Mono.

**Display** — designed for large sizes only. Highly expressive, often decorative. Falls apart at small sizes. For headlines only. Examples: Fraunces, Cabinet Grotesk, Clash Display.

For Darya's Dental, a serif for headings (authority, trust, warmth) paired with a clean sans-serif for body text (legible, modern) is a strong combination.

---

## Part 2 — Type Scale

A type scale is a **set of harmonically related font sizes**. Rather than picking sizes arbitrarily, a scale produces sizes that feel visually related because they share a common ratio.

### The Modular Scale

Pick a base size (usually `1rem` = 16px) and a ratio. Multiply up, divide down.

```
Ratio 1.25 (Major Third):
xs:    0.64rem   (≈10px)
sm:    0.8rem    (≈13px)
base:  1rem      (16px)     ← base
lg:    1.25rem   (20px)
xl:    1.563rem  (25px)
2xl:   1.953rem  (31px)
3xl:   2.441rem  (39px)
hero:  3.052rem  (49px)
```

```
Ratio 1.333 (Perfect Fourth) — more dramatic contrast:
xs:    0.563rem  (≈9px)
sm:    0.75rem   (12px)
base:  1rem      (16px)
lg:    1.333rem  (21px)
xl:    1.777rem  (28px)
2xl:   2.369rem  (38px)
3xl:   3.157rem  (50px)
hero:  4.209rem  (67px)
```

A larger ratio creates more typographic contrast between levels — more dramatic hierarchy. A smaller ratio is more harmonious and conservative. The right choice depends on the character of the brand.

### Fluid Type Scale for Darya's Dental

Taking the modular scale and making it fluid with `clamp()`:

```css
:root {
  /* Base: 16px → 18px */
  /* Scale ratio: ~1.25 at mobile, ~1.333 at desktop */

  --text-xs:   clamp(0.694rem, 0.9vw,  0.8rem);
  --text-sm:   clamp(0.833rem, 1.2vw,  1rem);
  --text-base: clamp(1rem,     1.5vw,  1.125rem);
  --text-lg:   clamp(1.2rem,   2vw,    1.5rem);
  --text-xl:   clamp(1.44rem,  2.5vw,  2rem);
  --text-2xl:  clamp(1.728rem, 3.5vw,  2.667rem);
  --text-3xl:  clamp(2.074rem, 4.5vw,  3.556rem);
  --text-hero: clamp(2.488rem, 6vw,    5rem);
}
```

The minimum is the mobile size, the maximum is the desktop size, and the viewport unit interpolates smoothly between them.

---

## Part 3 — Line Height

Line height (also called leading) is the vertical space between lines of text. It's one of the most impactful adjustments you can make to readability.

### The Rules

**Body text** needs generous line height — roughly 1.5 to 1.7. Text at reading size needs room to breathe between lines.

**Headings** need tight line height — roughly 1.1 to 1.3. Large type already has visual space around it. Loose leading at display sizes looks like the heading is falling apart.

**Single-line UI elements** (buttons, labels, badges) need a line height of 1 or 1.2. They're not meant to wrap.

```css
:root {
  --leading-none:    1;      /* buttons, badges, single-line labels */
  --leading-tight:   1.2;    /* display headings, h1 */
  --leading-snug:    1.375;  /* subheadings, h2–h3 */
  --leading-normal:  1.6;    /* body paragraphs */
  --leading-relaxed: 1.75;   /* large body text, long-form reading */
  --leading-loose:   2;      /* small captions, spaced lists */
}
```

```css
/* Applied */
h1 { line-height: var(--leading-tight); }
h2 { line-height: var(--leading-snug); }
h3 { line-height: var(--leading-snug); }
p  { line-height: var(--leading-normal); }

.btn   { line-height: var(--leading-none); }
.badge { line-height: var(--leading-none); }
```

### Line Height and Font Size Interact

As font size increases, line height should decrease. Large text needs tighter leading. Small text needs looser leading.

```css
/* Body text — generous */
p { font-size: var(--text-base); line-height: var(--leading-normal); }

/* Heading — tight */
h1 { font-size: var(--text-hero); line-height: var(--leading-tight); }

/* Small print — slightly loose */
.caption { font-size: var(--text-sm); line-height: var(--leading-relaxed); }
```

---

## Part 4 — Measure (Line Length)

Measure is the width of a column of text — the number of characters per line. It's one of the most neglected typographic settings in web design and one of the most impactful on readability.

The optimal measure for body text is **60–75 characters per line**. Too short and the eye jumps too often. Too long and the eye loses its place returning to the next line.

### The `ch` Unit

`1ch` equals the width of the `0` character in the current font. It's not perfect (characters vary in width) but it's a reliable approximation for measure control.

```css
/* Constrain body text to optimal reading width */
p,
.prose {
  max-width: 65ch;
}

/* Headings can be wider */
h1, h2 { max-width: 20ch; }

/* Very wide headings look broken — constrain them */
.hero__title { max-width: 14ch; }
```

```css
/* The container pattern revisited with reading width */
.container        { max-width: var(--container-max); }  /* 1200px — layout */
.container--prose { max-width: 70ch; }                   /* reading content */
.container--tight { max-width: 50ch; }                   /* forms, short text */
```

### Why Prose Columns Should Never Be Full Width

On a 1440px monitor, a paragraph that stretches edge to edge has roughly 200 characters per line. That's nearly three times the optimal measure. Reading it is exhausting — your eye loses its place on every line return.

This is why newspaper columns exist. It's why this chat interface constrains text width. It's why every book and article has margins. Constrain your body text.

---

## Part 5 — Tracking (Letter Spacing)

Tracking is the uniform spacing between all characters in a block of text. Different sizes need different tracking.

**Large display text** benefits from slightly negative tracking — characters pulled together look intentional at big sizes.

**Body text** needs zero or near-zero tracking — the typeface designer set this correctly.

**Small all-caps text** (labels, badges, navigation) needs generous positive tracking — it aids legibility when characters are small.

```css
:root {
  --tracking-tighter: -0.04em;   /* very large display headings */
  --tracking-tight:   -0.02em;   /* large headings */
  --tracking-normal:   0em;      /* body text — don't touch */
  --tracking-wide:     0.05em;   /* small text, UI labels */
  --tracking-wider:    0.1em;    /* all-caps labels, badges */
  --tracking-widest:   0.15em;   /* small caps, overlines */
}
```

```css
.hero__title    { letter-spacing: var(--tracking-tight); }
h2              { letter-spacing: var(--tracking-tight); }
p               { letter-spacing: var(--tracking-normal); }

.badge          { letter-spacing: var(--tracking-wide); }
.overline       { letter-spacing: var(--tracking-widest); text-transform: uppercase; }
.nav__link      { letter-spacing: var(--tracking-wide); }
```

A common mistake is applying positive tracking to large headings. At display sizes, extra letter spacing looks amateurish — the characters look like they're drifting apart. Pull them in slightly instead.

---

## Part 6 — Font Weight

Most typefaces come in multiple weights. Using them creates hierarchy without relying on size alone.

```css
:root {
  --weight-light:    300;
  --weight-regular:  400;
  --weight-medium:   500;
  --weight-semibold: 600;
  --weight-bold:     700;
  --weight-extrabold: 800;
}
```

A robust typographic system rarely needs more than three weights. Too many weights creates visual noise rather than hierarchy.

For Darya's Dental:

```css
/* Regular (400) — body text, descriptions */
p, li, label { font-weight: var(--weight-regular); }

/* Semibold (600) — subheadings, card titles, emphasis */
h3, h4, .card__title, strong { font-weight: var(--weight-semibold); }

/* Bold (700) — main headings, hero text */
h1, h2 { font-weight: var(--weight-bold); }
```

---

## Part 7 — Loading Fonts Correctly

Font loading is where typography meets performance. Loaded incorrectly, fonts cause flash of invisible text (FOIT) or flash of unstyled text (FOUT), visible layout shifts, and degraded Core Web Vitals.

### `@font-face` — The Foundation

```css
@font-face {
  font-family: 'Playfair Display';
  src:
    url('/assets/fonts/playfair-display-bold.woff2') format('woff2');
  font-weight: 700;
  font-style: normal;
  font-display: swap;           /* show fallback immediately, swap when ready */
}

@font-face {
  font-family: 'DM Sans';
  src:
    url('/assets/fonts/dm-sans-regular.woff2') format('woff2');
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

@font-face {
  font-family: 'DM Sans';
  src:
    url('/assets/fonts/dm-sans-semibold.woff2') format('woff2');
  font-weight: 600;
  font-style: normal;
  font-display: swap;
}
```

Always use `.woff2` — it's the modern format, compressed and universally supported. You don't need `.woff`, `.ttf`, or `.eot` anymore.

### `font-display` Values

```css
font-display: auto;      /* browser decides — usually FOIT */
font-display: block;     /* invisible until loaded — worst for UX */
font-display: swap;      /* show fallback immediately, swap when ready — best */
font-display: fallback;  /* brief invisible period, then fallback, then swap */
font-display: optional;  /* use fallback if font isn't cached — best for perf */
```

`swap` is the right choice for most projects. `optional` is better for performance-critical sites where font loading would delay meaningful content.

### Preloading Critical Fonts

Tell the browser to fetch fonts before it parses the CSS that references them:

```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- Preload — fetch immediately, before CSS is parsed -->
  <link
    rel="preload"
    href="/assets/fonts/playfair-display-bold.woff2"
    as="font"
    type="font/woff2"
    crossorigin
  >
  <link
    rel="preload"
    href="/assets/fonts/dm-sans-regular.woff2"
    as="font"
    type="font/woff2"
    crossorigin
  >

  <link rel="stylesheet" href="assets/css/main.css">
</head>
```

`crossorigin` is required even for same-origin fonts — the browser fetches fonts in CORS mode regardless.

Only preload fonts used above the fold. Preloading every font wastes bandwidth and delays more important resources.

### Using Google Fonts Correctly

If self-hosting isn't an option, Google Fonts needs careful handling:

```html
<!-- ❌ Default Google Fonts embed — render-blocking -->
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&display=swap" rel="stylesheet">

<!-- ✅ Preconnect + deferred load -->
<head>
  <!-- Warm up the connection early -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>

  <!-- Load asynchronously -->
  <link
    rel="stylesheet"
    href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=DM+Sans:wght@400;600&display=swap"
    media="print"
    onload="this.media='all'"
  >

  <!-- Fallback for JS disabled -->
  <noscript>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=DM+Sans:wght@400;600&display=swap">
  </noscript>
</head>
```

The `media="print"` trick loads the stylesheet asynchronously — the browser fetches it but doesn't block rendering. `onload="this.media='all'"` switches it to apply to all media once loaded.

Self-hosting is always preferable to Google Fonts for performance and privacy. Download the fonts, serve them from your own domain, and use `@font-face` directly.

### Subsetting — Only Loading What You Need

A full font file includes every character, diacritic, and symbol the typeface supports — often hundreds of glyphs you'll never use. Subsetting removes unused glyphs and can cut font file size by 70–90%.

```css
/* Unicode range — only load the Latin subset */
@font-face {
  font-family: 'Playfair Display';
  src: url('/assets/fonts/playfair-display-bold-latin.woff2') format('woff2');
  font-weight: 700;
  font-display: swap;
  unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC,
                 U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074,
                 U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215,
                 U+FEFF, U+FFFD;
}
```

For Arabic text on a site like Darya's Dental (Baghdad audience), you'd also load an Arabic subset:

```css
@font-face {
  font-family: 'DM Sans';
  src: url('/assets/fonts/dm-sans-arabic.woff2') format('woff2');
  font-weight: 400;
  font-display: swap;
  unicode-range: U+0600-06FF, U+200C-200E, U+2010-2011,
                 U+204F, U+2E41, U+FB50-FDFF, U+FE80-FEFC;
}
```

The browser only downloads the subset file whose `unicode-range` contains characters actually on the page.

---

## Part 8 — Variable Fonts

Variable fonts pack an entire typeface family — all weights, widths, and styles — into a single file with axes you control continuously in CSS. One file replaces four to eight separate weight files.

```css
@font-face {
  font-family: 'DM Sans';
  src: url('/assets/fonts/dm-sans-variable.woff2') format('woff2-variations');
  font-weight: 100 900;          /* full range available */
  font-display: swap;
}
```

```css
/* Now you can use any weight, not just 400/700 */
.hero__title    { font-weight: 750; }  /* between bold and extrabold */
.card__title    { font-weight: 580; }  /* between medium and semibold */
.body-text      { font-weight: 390; }  /* slightly lighter than regular */
```

Variable fonts also support custom axes beyond weight:

```css
/* font-variation-settings for standard axes */
.heading {
  font-variation-settings:
    'wght' 700,      /* weight */
    'wdth' 85,       /* width — condensed */
    'slnt' -10;      /* slant — oblique */
}

/* Animating weight — only possible with variable fonts */
.animated-heading {
  font-variation-settings: 'wght' 400;
  transition: font-variation-settings 300ms ease;
}

.animated-heading:hover {
  font-variation-settings: 'wght' 700;
}
```

For Darya's Dental, using a variable font for DM Sans means one network request instead of multiple weight files, smaller total download, and the ability to animate weight on hover for interactive elements.

---

## Part 9 — Optical Sizing

At large sizes, type designed for body text looks too light and spaced. At small sizes, type designed for display looks too heavy and cramped. Optical sizing corrects this.

Some variable fonts expose an `opsz` axis that adjusts letterform details automatically:

```css
/* Let the browser adjust automatically based on font-size */
.hero__title {
  font-size: var(--text-hero);
  font-optical-sizing: auto;  /* uses opsz axis if available */
}

/* Or control manually */
.display-text {
  font-variation-settings: 'opsz' 48;  /* optimized for 48px display use */
}

.caption-text {
  font-variation-settings: 'opsz' 12;  /* optimized for 12px caption use */
}
```

---

## Part 10 — A Complete Typography System

Pulling everything together into a `typography.css` for Darya's Dental:

```css
/* ── Font Loading ── */
@font-face {
  font-family: 'Playfair Display';
  src: url('/assets/fonts/playfair-display-variable.woff2') format('woff2-variations');
  font-weight: 400 900;
  font-style: normal italic;
  font-display: swap;
}

@font-face {
  font-family: 'DM Sans';
  src: url('/assets/fonts/dm-sans-variable.woff2') format('woff2-variations');
  font-weight: 100 900;
  font-style: normal;
  font-display: swap;
}

/* ── Base Elements ── */
html {
  font-size: 100%;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
  font-optical-sizing: auto;
}

body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  font-weight: var(--weight-regular);
  line-height: var(--leading-normal);
  color: var(--color-text-primary);
}

/* ── Heading Scale ── */
h1 {
  font-family: var(--font-serif);
  font-size: var(--text-hero);
  font-weight: 700;
  line-height: var(--leading-tight);
  letter-spacing: var(--tracking-tight);
  color: var(--color-text-primary);
}

h2 {
  font-family: var(--font-serif);
  font-size: var(--text-3xl);
  font-weight: 700;
  line-height: var(--leading-tight);
  letter-spacing: var(--tracking-tight);
  color: var(--color-text-primary);
}

h3 {
  font-family: var(--font-sans);
  font-size: var(--text-2xl);
  font-weight: 600;
  line-height: var(--leading-snug);
  letter-spacing: var(--tracking-normal);
  color: var(--color-text-primary);
}

h4 {
  font-family: var(--font-sans);
  font-size: var(--text-xl);
  font-weight: 600;
  line-height: var(--leading-snug);
  color: var(--color-text-primary);
}

h5, h6 {
  font-family: var(--font-sans);
  font-size: var(--text-lg);
  font-weight: 600;
  line-height: var(--leading-snug);
  color: var(--color-text-primary);
}

/* ── Body Text ── */
p {
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text-secondary);
  max-width: 65ch;
}

/* Remove max-width when inside a constrained container */
.container--prose p { max-width: none; }

/* ── Links ── */
a {
  color: var(--color-text-accent);
  text-decoration: underline;
  text-underline-offset: 3px;
  text-decoration-thickness: 1px;
  transition: color var(--duration-fast) var(--ease-standard);
}

a:hover {
  color: var(--color-text-accent-hover);
  text-decoration-thickness: 2px;
}

/* ── Utility Text Classes ── */
.text-display {
  font-family: var(--font-serif);
  font-size: var(--text-hero);
  font-weight: 700;
  line-height: var(--leading-tight);
  letter-spacing: var(--tracking-tight);
}

.text-overline {
  font-family: var(--font-sans);
  font-size: var(--text-xs);
  font-weight: 600;
  line-height: var(--leading-none);
  letter-spacing: var(--tracking-widest);
  text-transform: uppercase;
  color: var(--color-text-accent);
}

.text-lead {
  font-size: var(--text-lg);
  line-height: var(--leading-relaxed);
  color: var(--color-text-secondary);
  max-width: 55ch;
}

.text-caption {
  font-size: var(--text-sm);
  line-height: var(--leading-relaxed);
  color: var(--color-text-muted);
}

.text-label {
  font-size: var(--text-sm);
  font-weight: 600;
  letter-spacing: var(--tracking-wide);
  color: var(--color-text-secondary);
}

/* ── Prose — Long-form Content ── */
.prose h2 { margin-top: var(--space-12); margin-bottom: var(--space-4); }
.prose h3 { margin-top: var(--space-8);  margin-bottom: var(--space-3); }
.prose p  { margin-bottom: var(--space-6); }

.prose p + p { margin-top: 0; }

.prose ul,
.prose ol {
  margin-bottom: var(--space-6);
  padding-left: var(--space-6);
  list-style: revert;      /* restore browser default list styling */
}

.prose li { margin-bottom: var(--space-2); line-height: var(--leading-normal); }

.prose strong { font-weight: 600; color: var(--color-text-primary); }
.prose em     { font-style: italic; }

.prose blockquote {
  border-left: 3px solid var(--color-brand-primary);
  padding-left: var(--space-6);
  margin-left: 0;
  font-family: var(--font-serif);
  font-size: var(--text-lg);
  font-style: italic;
  color: var(--color-text-secondary);
}
```

---

## Part 11 — Pairing Typefaces

The combination of Playfair Display for headings and DM Sans for body works because they create contrast across multiple axes — one is serif, one is sans; one is expressive, one is neutral; one carries personality, one carries clarity.

Rules for pairing:

**Contrast on at least two axes.** Serif vs sans-serif is one axis. Pairing two similar sans-serifs (Inter and DM Sans) creates tension without contrast — they look like they're fighting for the same role.

**One typeface leads, one supports.** The display/heading font has personality. The body font is neutral enough to not compete.

**Limit to two typefaces.** A third is almost never necessary and usually feels unfocused. Use weight, size, tracking, and color to create hierarchy within two families.

**Test at the actual sizes you'll use them.** A pairing that looks elegant in a specimen might look heavy or weak at your actual heading sizes.

For Darya's Dental — Playfair Display Bold for `h1` and `h2`, DM Sans for everything else. The warmth and elegance of Playfair communicates trust and care (fitting for a dental clinic). DM Sans is readable and unobtrusive at body sizes, and holds its own as a heading at `h3` and below.

---

Typography done well is the difference between a page that users trust immediately and one they quietly doubt. The mechanics — scale, leading, measure, tracking, loading — are learnable. The judgment comes from paying close attention to type in the real world: in books, on product packaging, in well-designed apps. 