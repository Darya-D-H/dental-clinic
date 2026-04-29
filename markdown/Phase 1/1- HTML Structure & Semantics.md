Here's a solid overview of HTML structure principles, particularly as they apply to building production-grade frontends:

---
## HTML Structure & Semantics

### What "Semantic" Actually Means

A semantic element is one whose **name describes its purpose**, not its appearance. The browser, screen readers, search engines, and your future self all read the same HTML — semantic markup makes the document self-explanatory without needing to read the CSS or JS.

```html
<!-- This tells you nothing about what's inside -->
<div class="box-1">
  <div class="inner">Welcome</div>
</div>

<!-- This is self-documenting -->
<header>
  <h1>Welcome to Darya's Dental</h1>
</header>
```

---

### The Document as an Outline

Think of your HTML as a book. Every book has a cover, chapters, sections, and a back page. HTML has the same hierarchy.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Services – Darya's Dental</title>
</head>
<body>

  <header>                        <!-- site-wide top: logo, nav -->
    <nav>...</nav>
  </header>

  <main>                          <!-- the unique content of THIS page -->

    <section>                     <!-- a thematic grouping with a heading -->
      <h1>Our Services</h1>

      <article>                   <!-- self-contained, could stand alone -->
        <h2>Teeth Whitening</h2>
        <p>...</p>
      </article>

      <article>
        <h2>Dental Implants</h2>
        <p>...</p>
      </article>
    </section>

    <aside>                       <!-- related but secondary content -->
      <h2>Why Choose Us?</h2>
      <p>...</p>
    </aside>

  </main>

  <footer>                        <!-- site-wide bottom: links, copyright -->
    <p>&copy; 2025 Darya's Dental</p>
  </footer>

</body>
</html>
```

---

### The Landmark Elements

These are the eight elements that define major regions of a page. Screen readers expose them as navigation landmarks.

|Element|Purpose|Rules|
|---|---|---|
|`<header>`|Site or section header|Can appear inside `<article>` / `<section>` too|
|`<nav>`|Navigation links|Only for major navigation, not every link group|
|`<main>`|Page's primary content|One per page, never nested|
|`<section>`|Thematic grouping|Should always have a heading|
|`<article>`|Self-contained content|Could be syndicated independently|
|`<aside>`|Tangentially related content|Sidebars, callouts, related links|
|`<footer>`|Closing info for its parent|Can appear inside `<article>` too|
|`<address>`|Contact info for nearest `<article>` or `<body>`|Not for postal addresses in general|

---

### Heading Hierarchy — The Skeleton

Headings define the document outline. One `h1` per page. Never skip a level.

```html
<h1>Darya's Dental</h1>         <!-- page identity — one only -->
  <h2>Our Services</h2>          <!-- major section -->
    <h3>Cosmetic Dentistry</h3>  <!-- subsection -->
      <h4>Veneers</h4>           <!-- topic within subsection -->
  <h2>Meet the Team</h2>
  <h2>Book an Appointment</h2>
```

Headings are for structure, not size. If you want large text that isn't a heading, use CSS on a `<p>` or `<span>`. Don't use `<h3>` just because you want medium-sized text.

---

### Inline vs Block — The Core Distinction

**Block elements** start on a new line and take full width by default. They define structure.

**Inline elements** flow within text. They wrap content inside blocks.

```html
<!-- Block — creates a new layout context -->
<p>This is a paragraph of text about our clinic.</p>

<!-- Inline — lives inside the paragraph -->
<p>This is a <strong>paragraph</strong> of text about our <em>clinic</em>.</p>
```

Key rule: **block elements cannot go inside inline elements.**

```html
<!-- ❌ Invalid — div (block) inside span (inline) -->
<span>
  <div>This breaks the document</div>
</span>

<!-- ✅ Valid — inline inside block -->
<div>
  <span>This is fine</span>
</div>
```

---

### Meaningful Inline Elements

Don't reach for `<span>` when a semantic element exists.

```html
<strong>Important</strong>       <!-- strong importance — bold by default -->
<em>Emphasis</em>                <!-- stress emphasis — italic by default -->
<mark>Highlighted</mark>         <!-- highlighted/relevant text -->
<code>font-size: 1rem</code>     <!-- inline code -->
<time datetime="2025-06-15">June 15</time>  <!-- machine-readable date -->
<abbr title="National Health Service">NHS</abbr>  <!-- abbreviation -->
<q>The best dental care in Baghdad</q>  <!-- inline quotation -->
```

`<strong>` and `<em>` carry meaning. `<b>` and `<i>` are purely visual. Prefer the semantic ones.

---

### Lists — Three Types

```html
<!-- Unordered — no sequence matters -->
<ul>
  <li>Teeth whitening</li>
  <li>Implants</li>
  <li>Veneers</li>
</ul>

<!-- Ordered — sequence matters -->
<ol>
  <li>Book your consultation</li>
  <li>Complete your treatment plan</li>
  <li>Begin treatment</li>
</ol>

<!-- Description list — term/definition pairs -->
<dl>
  <dt>Veneer</dt>
  <dd>A thin shell bonded to the front surface of a tooth.</dd>

  <dt>Implant</dt>
  <dd>A titanium post surgically placed into the jawbone.</dd>
</dl>
```

`<ul>` and `<ol>` direct children must only be `<li>`. Never put a `<div>` directly inside a `<ul>`.

---

### The `<div>` and `<span>` Rule

These two are intentionally meaningless — they're grouping tools with no semantic value. Use them only when no semantic element fits.

```html
<!-- ❌ Div soup — no meaning conveyed -->
<div class="header">
  <div class="nav">
    <div class="nav-link">Services</div>
  </div>
</div>

<!-- ✅ Div used correctly — layout wrapper only -->
<header>
  <nav>
    <ul>
      <li><a href="/services">Services</a></li>
    </ul>
  </nav>
</header>

<!-- ✅ Div as layout container where no semantic element fits -->
<div class="container">
  <main>...</main>
</div>
```

A useful test: if you removed all your CSS, would the HTML still make sense? Semantic HTML reads like structured prose without any styling.

---

### Links vs Buttons

This is one of the most common mistakes in the wild.

```html
<!-- <a> — navigates somewhere. Always has an href -->
<a href="/services">View our services</a>

<!-- <button> — triggers an action. No navigation -->
<button type="button">Open the menu</button>
<button type="submit">Book appointment</button>

<!-- ❌ Wrong — anchor with no destination -->
<a href="#">Click me</a>   <!-- use a button instead -->

<!-- ❌ Wrong — div pretending to be a button -->
<div onclick="doSomething()">Click me</div>  <!-- no keyboard access, no semantics -->
```

The rule: **does it go somewhere? Use `<a>`. Does it do something on the page? Use `<button>`.**

---

### Images

```html
<!-- Informative — describe what it shows, not "image of" -->
<img src="dr-sara.jpg" alt="Dr. Sara Khalil smiling in the clinic reception area">

<!-- Decorative — empty alt, screen reader skips it entirely -->
<img src="wave-divider.svg" alt="">

<!-- With a caption — figure wraps image + caption together -->
<figure>
  <img src="before-after.jpg" alt="Teeth before and after whitening treatment">
  <figcaption>Results after a single whitening session</figcaption>
</figure>
```

Always include `alt`. Missing `alt` is an accessibility failure. Empty `alt=""` is intentional and correct for decorative images.

---

### The `<head>` — What Belongs There

```html
<head>
  <!-- 1. Character encoding — always first -->
  <meta charset="UTF-8">

  <!-- 2. Viewport — always second, enables responsive design -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- 3. Page description — shown in search results -->
  <meta name="description" content="Affordable dental care in Baghdad — whitening, implants, and more.">

  <!-- 4. Title — shown in browser tab and search results -->
  <title>Services – Darya's Dental</title>

  <!-- 5. Stylesheets -->
  <link rel="stylesheet" href="assets/css/main.css">

  <!-- 6. Favicon -->
  <link rel="icon" href="assets/images/favicon.ico">
</head>
```

Order matters. Charset and viewport before anything else — the browser needs them immediately to parse and render correctly.

---

### A Mental Checklist When Writing HTML

Before you write a single element, ask:

1. **Is there a semantic element for this?** Check before reaching for `<div>`
2. **Does my heading order make sense?** `h1` → `h2` → `h3`, no skipping
3. **Are my landmarks in place?** `header`, `nav`, `main`, `footer`
4. **Is this a link or a button?** Navigation → `<a>`. Action → `<button>`
5. **Do all my images have `alt` text?** Informative content or empty string
6. **Are my lists valid?** Only `<li>` directly inside `<ul>`/`<ol>`

---
