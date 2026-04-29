___
## Accessibility in Frontend Development

We covered the core mechanics of accessibility and forms in an earlier session — ARIA, keyboard navigation, focus management, color contrast, skip links, and form structure. Rather than repeat that ground, here's where to go deeper: the **why** behind those rules, the parts that trip people up most, and the concepts that sit just beyond the basics.

---

### The Four Principles — POUR

Everything in accessibility traces back to four properties. A page must be:

- **Perceivable** — information must be presentable in ways users can sense. If something is purely visual with no text equivalent, blind users hit a wall.
- **Operable** — all functionality must be reachable without a mouse. If a dropdown only opens on hover, keyboard users are locked out.
- **Understandable** — content and UI behavior must be predictable. A form that resets without warning, or error messages that appear somewhere unexpected, fail this.
- **Robust** — HTML must be valid enough that assistive technologies can parse it reliably. Broken nesting and duplicate IDs cause screen readers to misread the page.

These four map directly to WCAG (Web Content Accessibility Guidelines), which is the international standard most organizations and legal frameworks reference.

---

### The Accessibility Tree

The browser builds two trees from your HTML: the visual render tree (what you see), and the **accessibility tree** (what assistive technologies read). These are not the same thing.

```html
<!-- What the browser renders visually -->
<div class="card">
  <div class="card-header">Teeth Whitening</div>
  <div class="card-body">From $200 per session</div>
</div>

<!-- What the accessibility tree sees -->
<!-- A generic container. No role, no name, no structure. -->
```

```html
<!-- With semantics — the tree is meaningful -->
<article>
  <h2>Teeth Whitening</h2>
  <p>From $200 per session</p>
</article>

<!-- Accessibility tree: article landmark, heading level 2 "Teeth Whitening", paragraph -->
```

You can inspect the accessibility tree directly in Chrome DevTools under Elements → Accessibility. It shows the role, name, and state of every element as a screen reader would encounter it. This is the most useful debugging tool for accessibility work.

---

### Roles, Names, and States

Every element in the accessibility tree has three properties that matter:

**Role** — what kind of thing it is. Set implicitly by the HTML element, or explicitly with `role=""`.

```html
<button>         <!-- role: button -->
<nav>            <!-- role: navigation -->
<h2>             <!-- role: heading, level 2 -->
<input type="checkbox">  <!-- role: checkbox -->
```

**Name** — what it's called. How a screen reader announces it.

```html
<!-- Button named by its text content -->
<button>Book Appointment</button>

<!-- Button named by aria-label (no visible text) -->
<button aria-label="Close menu">
  <svg aria-hidden="true">...</svg>
</button>

<!-- Input named by its label -->
<label for="email">Email Address</label>
<input type="email" id="email">

<!-- Image named by alt -->
<img src="clinic.jpg" alt="Reception area">
```

**State** — its current condition. Changes dynamically with JS.

```html
<button aria-expanded="false">Menu</button>   <!-- collapsed -->
<button aria-expanded="true">Menu</button>    <!-- expanded -->

<input aria-invalid="true">                   <!-- has an error -->
<li aria-current="page">Services</li>         <!-- active nav item -->
```

If a screen reader announces "button" with no name, you've got a role but no name. If it announces text but it makes no sense out of context ("click here", "read more"), you've got a name but a bad one.

---

### Focus Management in Depth

Keyboard users navigate by Tab (forward), Shift+Tab (backward), Enter/Space (activate). The browser manages this automatically for native elements — but custom components break it.

**The `tabindex` attribute:**

```html
<!-- Default — follows DOM order, no tabindex needed -->
<button>Book Now</button>

<!-- tabindex="0" — adds non-interactive element to tab order -->
<div role="button" tabindex="0">Custom button</div>

<!-- tabindex="-1" — reachable by JS focus(), not by Tab key -->
<div id="modal" tabindex="-1">...</div>

<!-- tabindex="1+" — never do this. Breaks natural tab order -->
<button tabindex="3">Don't</button>
```

`tabindex="-1"` is used for programmatic focus — elements you need to focus via JS but don't want in the natural tab flow, like modals and dialogs.

**Focus trapping in modals:**

When a modal opens, focus must stay inside it. Tab at the last focusable element wraps to the first. Shift+Tab at the first wraps to the last.

```javascript
function trapFocus(modal) {
  const focusable = modal.querySelectorAll(
    'a, button, input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  modal.addEventListener('keydown', (e) => {
    if (e.key !== 'Tab') return;

    if (e.shiftKey) {
      if (document.activeElement === first) {
        e.preventDefault();
        last.focus();
      }
    } else {
      if (document.activeElement === last) {
        e.preventDefault();
        first.focus();
      }
    }
  });
}
```

**The full modal focus lifecycle:**

```javascript
function openModal(modal, trigger) {
  modal.removeAttribute('hidden');
  modal.querySelector('h2').focus();    // move focus in
  trapFocus(modal);
}

function closeModal(modal, trigger) {
  modal.setAttribute('hidden', '');
  trigger.focus();                       // return focus to trigger
}
```

If you open a modal and focus stays on the button behind it, keyboard users are trapped outside the modal. If you close it and don't return focus, they lose their place entirely.

---

### Live Regions — Announcing Dynamic Content

When content updates without a page reload (form errors appearing, a cart updating, a success message), screen readers don't know it changed unless you tell them.

```html
<!-- polite — waits until the user finishes what they're doing -->
<div aria-live="polite" id="status"></div>

<!-- assertive — interrupts immediately. Only for urgent errors -->
<div aria-live="assertive" id="error"></div>
```

```javascript
// Injecting content into a live region announces it automatically
document.getElementById('status').textContent = 'Appointment booked successfully.';
document.getElementById('error').textContent = 'Please enter a valid email address.';
```

The element must exist in the DOM before content is injected — screen readers watch for changes to existing live regions. Creating the element and inserting it simultaneously doesn't reliably trigger an announcement.

---

### Reduced Motion

Some users get nausea or seizures from animation. Always wrap non-trivial animations in a media query:

```css
/* Animations play by default */
.card {
  transition: transform 0.3s ease;
}

.hero__text {
  animation: slideIn 0.6s ease forwards;
}

/* Stripped back for users who prefer less motion */
@media (prefers-reduced-motion: reduce) {
  .card {
    transition: none;
  }

  .hero__text {
    animation: none;
  }
}
```

On Darya's Dental, any page transitions, hover animations, or loading effects should be wrapped in this query. The content still appears — just without the motion.

---

### Colour Alone Is Never Enough

Never use color as the only way to convey information. Colorblind users (roughly 8% of men) will miss it.

```html
<!-- ❌ Color is the only indicator of required fields -->
<label style="color: red;">Email</label>

<!-- ✅ Color + text indicator -->
<label>
  Email <span aria-hidden="true">*</span>
  <span class="sr-only">required</span>
</label>

<!-- ❌ Error state communicated only by red border -->
<input style="border-color: red;">

<!-- ✅ Color + icon + text -->
<input aria-invalid="true" aria-describedby="email-error">
<span id="email-error">
  <svg aria-hidden="true"><!-- warning icon --></svg>
  Please enter a valid email
</span>
```

The `.sr-only` class is one of the most useful patterns in accessible CSS — visually hides content while keeping it in the accessibility tree:

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

Use it for labels on icon-only controls, supplementary screen-reader context, and skip link text.

---

### Writing Accessible Navigation

```html
<header>
  <nav aria-label="Main navigation">
    <ul role="list">
      <li><a href="/" aria-current="page">Home</a></li>
      <li><a href="/services">Services</a></li>
      <li><a href="/team">Our Team</a></li>
      <li><a href="/contact">Contact</a></li>
    </ul>
  </nav>

  <!-- If you have a second nav -->
  <nav aria-label="Utility navigation">
    <ul role="list">
      <li><a href="/login">Login</a></li>
    </ul>
  </nav>
</header>
```

`aria-label` on `<nav>` distinguishes multiple navs from each other. `aria-current="page"` marks the active link — screen readers announce it as "current page". `role="list"` on `<ul>` restores list semantics that some browsers strip when `list-style: none` is applied in CSS.

---

### Common Mistakes at a Glance

|Mistake|Fix|
|---|---|
|`<div onclick="">` for buttons|Use `<button>`|
|`<a href="#">` for actions|Use `<button>`|
|Missing `alt` on images|Add descriptive `alt` or `alt=""` for decorative|
|Placeholder as label|Add a real `<label>`|
|Removing `:focus` outline|Restyle it, never remove it|
|Opening modal without moving focus|Call `.focus()` on first element inside|
|Dynamic content not announced|Use `aria-live` regions|
|Color as the only error indicator|Add icon + text|
|Animating without `prefers-reduced-motion`|Wrap in media query|
|`tabindex` values above 0|Use `0` or `-1` only|

---

Accessibility is less a checklist and more a way of reading your own HTML as someone else's experience. The most reliable habit is testing with a keyboard and occasionally with a screen reader — VoiceOver on Mac (Cmd+F5), NVDA on Windows (free). You'll catch more in five minutes of real testing than any linting tool will find.
