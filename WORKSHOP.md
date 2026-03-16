# Workshop: Accessibility Audit & Fix

A hands-on guide to finding and fixing accessibility violations on a real product page using audit tools, keyboard testing, and screen reader testing.

## Table of Contents

1. [Overview](#overview)
2. [Duration](#duration)
3. [Prerequisites](#prerequisites)
4. [What You'll Learn](#what-youll-learn)
5. [Files](#files)
6. [Setup -- The Broken Page](#setup----the-broken-page)
7. [Part 1: Automated Audit](#part-1----automated-audit-40-min)
8. [Part 2: Manual Testing](#part-2----manual-testing-50-min)
9. [Part 3: Triage and Prioritize](#part-3----triage-and-prioritize-15-min)
10. [Part 4: Fix the Issues](#part-4----fix-the-issues-90-min)
11. [Part 5: Re-audit and Reflect](#part-5----re-audit-and-reflect-30-min)
12. [Key Takeaways](#key-takeaways)
13. [Quick Reference](#quick-reference)
14. [Common Mistakes](#common-mistakes)

---

## Overview

**Goal:** Given a deliberately inaccessible e-commerce product page, find and fix every issue using real audit tools, keyboard testing, and screen reader testing. Learn not just the technical fixes but WHY each issue prevents real people from using the web.

## Duration

~4 hours (expanded from 3-4 to give proper time to screen reader testing and fix implementation)

## Prerequisites

- Basic HTML, CSS, and JavaScript knowledge
- Chrome or Firefox installed
- axe DevTools browser extension installed (free)
- Accessibility Insights for Web extension installed (free, optional but recommended)

## What You'll Learn

> **The First Rule of ARIA:** If you can use a native HTML element or attribute with the semantics and behavior you require already built in, do that instead of adding ARIA roles and properties to a non-semantic element. ARIA should be a last resort, not a first tool. Keep this in mind throughout the entire workshop.

- Semantic HTML and why it is the foundation of accessibility
- The first rule of ARIA: use native HTML elements before reaching for ARIA
- ARIA roles, states, properties, and live regions
- Keyboard navigation and focus management (including focus trapping)
- Color contrast, visual accessibility, and `prefers-reduced-motion`
- Screen reader navigation (practical keystrokes, not just theory)
- How to write effective alt text and form labels
- How to prioritize accessibility fixes by user impact
- Using audit tools: axe DevTools, Lighthouse, Accessibility Insights, browser dev tools
- How to prevent accessibility regressions in a development workflow

## Files

- `starter/index.html` -- The broken page. Open this in your browser and audit it. It contains 23 intentional accessibility violations that are invisible to sighted mouse users.
- `solution/index.html` -- The fully fixed version with teaching comments explaining each fix. Compare against your work when done.

---

## Setup -- The Broken Page

Open `starter/index.html` in your browser. It is a single-file e-commerce product page for a fictional wireless headphone product. The page looks professionally designed and visually appealing. It contains **23 intentional accessibility violations** that are invisible to sighted mouse users.

**The scenario:** "You have joined a team that just shipped this product page. A customer who uses a screen reader has complained that they cannot purchase the product. Your job is to find and fix every accessibility barrier on this page."

**Page components:**
1. Header with logo, navigation links, and a cart icon
2. "Skip to content" link (missing -- they must add it)
3. Hero product image with thumbnails
4. Product title, price, and star rating
5. Color selector (custom div-based, not keyboard accessible)
6. "Add to Cart" button (built with a div)
7. Product description accordion (FAQ-style, no ARIA)
8. Customer review form (email + rating + text, no labels)
9. Existing customer reviews section (dynamic content area)
10. Quick-view modal (triggered by a "Quick View" button, no focus management)
11. Footer with links
12. Auto-playing background animation (CSS animation, no reduced-motion support)

**Complete violation list with user impact:**

*All references are to WCAG 2.2.*

| # | Category | Violation | User Impact | WCAG |
|---|----------|-----------|-------------|------|
| 1 | Structure | No `lang` attribute on `<html>`, no `<title>` | Screen readers do not know what language to use for pronunciation; browser tabs show the URL instead of a page name. | 3.1.1, 2.4.2 |
| 2 | Semantic | All content in `<div>` and `<span>` -- no landmarks (`<nav>`, `<main>`, `<header>`, `<footer>`) | Screen reader users cannot jump between page sections; they must listen to the entire page linearly. | 1.3.1 |
| 3 | Semantic | Headings skip levels: `<h1>` then `<h4>` then `<h6>` | Screen reader users who navigate by heading (very common) encounter a confusing, broken hierarchy. | 1.3.1 |
| 4 | Semantic | Navigation links built with styled `<div>` elements instead of `<ul><li><a>` | Screen readers do not announce these as links; keyboard users cannot Tab to them. | 1.3.1, 4.1.2 |
| 5 | Semantic | "Add to Cart" built as `<div onclick>` instead of `<button>` | Not focusable via keyboard, not announced as interactive by screen readers. A keyboard-only user literally cannot buy the product. | 2.1.1, 4.1.2 |
| 6 | Images | Product image has no `alt` attribute | A blind user has no idea what the product looks like. | 1.1.1 |
| 7 | Images | Decorative divider image has `alt="decorative-line.png"` | Screen reader pointlessly announces a filename, adding noise and confusion. | 1.1.1 |
| 8 | Forms | Inputs use `placeholder` instead of `<label>` elements | Placeholder text disappears on focus, leaving users (especially those with cognitive disabilities) unable to remember what the field is for. Screen readers may not announce placeholder as the input's name. | 1.3.1, 3.3.2 |
| 9 | Forms | Error messages exist but are not linked to inputs via `aria-describedby` | Screen reader users are not told which field has an error or what the error message says. | 1.3.1, 3.3.1 |
| 10 | Forms | Form validation provides no accessible feedback on submission | Screen reader users submit the form and hear nothing -- they do not know if it succeeded or failed. | 3.3.1, 4.1.3 |
| 11 | Color | Product description text uses light gray (#999) on white (#fff) -- contrast ratio ~2.8:1 | Users with low vision or in bright environments cannot read the product details. | 1.4.3 |
| 12 | Color | Star rating and status indicators use color alone (gold stars, green "In Stock" text) with no supporting text or icons | Color-blind users cannot distinguish the rating or stock status. | 1.4.1 |
| 13 | Keyboard | Custom color selector dropdown cannot be operated with keyboard | Keyboard users cannot select a product color, blocking purchase. | 2.1.1 |
| 14 | Keyboard | Global `*:focus { outline: none }` removes all focus indicators | Keyboard users cannot see where they are on the page. They are navigating blind. | 2.4.7 |
| 15 | Keyboard | Modal opens but focus is not moved into it; Tab escapes the modal into the page behind the overlay | Keyboard users are trapped behind a visually-blocking overlay with no way to interact with the modal content or close it. | 2.4.3 |
| 16 | Keyboard | No skip navigation link | Keyboard users must Tab through every header link on every page load before reaching the main content. | 2.4.1 |
| 17 | ARIA | `aria-hidden="true"` placed on the product price element (visible and critical) | Screen reader users cannot hear the price of the product. | 4.1.2 |
| 18 | ARIA | Accordion has no `role`, `aria-expanded`, or `aria-controls` | Screen reader users do not know the sections are expandable, what state they are in, or how to operate them. | 4.1.2 |
| 19 | Dynamic | "Added to cart" confirmation message appears visually but is not announced via `aria-live` | Screen reader users click "Add to Cart" and hear nothing. They do not know if their action succeeded. | 4.1.3 |
| 20 | Motion | CSS animations play unconditionally with no `prefers-reduced-motion` support | Users with vestibular disorders may experience dizziness or nausea. | 2.2.2 |
| 21 | Links | Footer contains "Click here" and "Read more" links with no context | Screen reader users who navigate by link list hear "Click here, Click here, Read more" with no indication of where the links go. | 2.4.4 |
| 22 | Media | Background video autoplays with no pause/stop control | Users cannot stop distracting motion; violates both motion and user control requirements. | 1.4.2, 2.2.2 |
| 23 | Touch | Interactive elements (close button, color swatches) are smaller than 24x24px | Users with motor impairments on touch devices cannot reliably activate these controls. | 2.5.8 |

---

## Part 1 -- Automated Audit (40 min)

**Setup (10 min):**
1. Open `starter/index.html` in Chrome
2. Install axe DevTools extension if not already installed
3. Open Chrome DevTools (F12)

**Lighthouse Audit (10 min):**
1. Go to DevTools > Lighthouse tab
2. Check only "Accessibility" category
3. Click "Analyze page load"
4. **Record the score** (it should be in the 40-55 range)
5. Read through each failure -- Lighthouse groups them by impact (Critical, Serious, Moderate, Minor)

**axe DevTools Audit (10 min):**
1. Go to DevTools > axe DevTools tab (after installing extension)
2. Click "Scan ALL of my page"
3. Read through each violation -- axe provides:
   - What the violation is
   - Why it matters
   - How to fix it
   - Which element(s) are affected (click to highlight)
4. Note: axe typically finds more issues than Lighthouse

**Create Your Findings Log (10 min):**

Use this template to document every issue found:

| Issue | Location on Page | Tool That Found It | Severity | Fixed? |
|---|---|---|---|---|
| Missing alt text on product image | Hero image section | axe, Lighthouse | Critical | |
| ... | ... | ... | ... | |

**Discussion point:** "You just ran two automated tools. They probably found 10-15 issues. The page has 23. Where are the other 8-13? Automated tools catch approximately 30-40% of accessibility issues. The rest require human testing."

---

## Part 2 -- Manual Testing (50 min)

### 2A: Keyboard-Only Testing (20 min)

**Instructions:** Physically move your mouse to the side of the desk. You will not touch it for the next 20 minutes.

**Key commands reference card:**

| Key | Action |
|---|---|
| `Tab` | Move to next interactive element |
| `Shift + Tab` | Move to previous interactive element |
| `Enter` | Activate links and buttons |
| `Space` | Activate buttons, toggle checkboxes |
| `Escape` | Close modals, dismiss popups |
| Arrow keys | Navigate within components (dropdowns, radio groups, tabs) |

**Task checklist -- attempt each action and record pass/fail:**

1. Can you Tab from the browser address bar into the page content?
2. Is there a "Skip to content" link that appears on first Tab press?
3. Can you see where focus is at ALL times? (Is there a visible indicator?)
4. Can you activate every navigation link using Enter?
5. Can you select a product color using keyboard only?
6. Can you activate "Add to Cart" using Enter or Space?
7. When the modal opens, does focus move into the modal?
8. Can you Tab through all elements inside the modal without focus escaping?
9. Can you close the modal with Escape?
10. After closing the modal, does focus return to the element that opened it?
11. Can you expand/collapse the accordion sections?
12. Can you fill out and submit the review form?

### 2B: Screen Reader Testing (20 min)

**Choose your screen reader based on your operating system:**

**Windows -- NVDA (free, recommended):**
- Download from https://www.nvaccess.org if not installed
- Start: `Ctrl + Alt + N`
- Stop: `Insert + Q`
- Key commands:
  - Read next item: `Down Arrow`
  - Read previous item: `Up Arrow`
  - Next heading: `H`
  - Next landmark: `D`
  - Next form field: `F`
  - Next link: `K`
  - Activate/click: `Enter` or `Space`
  - List all headings: `Insert + F7`
  - List all landmarks: `Insert + F7` then select Landmarks

**macOS -- VoiceOver (built-in):**
- Start/Stop: `Cmd + F5`
- The "VO key" is `Ctrl + Option` (abbreviated as VO below)
- Key commands:
  - Read next item: `VO + Right Arrow`
  - Read previous item: `VO + Left Arrow`
  - Next heading: `VO + Cmd + H`
  - Interact with element: `VO + Shift + Down Arrow`
  - Stop interacting: `VO + Shift + Up Arrow`
  - Activate/click: `VO + Space`
  - Open rotor (list headings, links, etc.): `VO + U`

**Linux -- Orca (GNOME, use with Firefox):**
- Start: `Super + Alt + S` (or enable in GNOME Settings > Accessibility)
- Note: Orca works best with **Firefox**, not Chrome
- Key commands:
  - Read next item: `Down Arrow`
  - Next heading: `H`
  - Next landmark: `D`
  - Activate: `Enter`

**Alternative -- Chrome Accessibility Tree (all platforms, no screen reader needed):**
- Open DevTools > Elements panel
- Select any element
- Look at the "Accessibility" pane on the right
- This shows what a screen reader WOULD announce, but it is not a substitute for actual screen reader testing

**Task checklist -- listen and record what the screen reader announces:**

1. When the page loads, what is announced? (Page title? Language?)
2. Navigate by headings (press H repeatedly). Is the heading hierarchy logical? Any skipped levels?
3. Navigate by landmarks (press D). Can you jump between header, nav, main, footer?
4. Navigate to the product image. Is it described? What does the screen reader say?
   - *Example of what you will hear:* The screen reader says **"graphic"** and nothing else, because there is no alt text. A sighted user sees a product photo; a blind user gets zero information.
5. Navigate to the "Add to Cart" element. Is it announced as a button? Does it have a label?
   - *Example:* Because it is a `<div>`, the screen reader may say **"Add to Cart"** as plain text -- it will NOT say **"Add to Cart, button"**, so the user does not know it is interactive.
6. Navigate to the price. Can you hear the price?
   - *Example:* The price section has `aria-hidden="true"`, so the screen reader **skips it entirely**. The user hears nothing about the cost.
7. Navigate to the review form. Are the form fields labeled? When you focus each field, do you know what to enter?
   - *Example:* Without labels, the screen reader says **"edit text"** with no indication of what the field is for.
8. Trigger an error. Is the error message announced?
9. Open the accordion. Is the expanded/collapsed state announced?
10. Add an item to cart. Is the cart update announced?

### 2C: Color and Visual Checks (10 min)

1. **Contrast check:** In Chrome DevTools, click on any text element, look at the "color" property in Styles. The color picker shows the contrast ratio. Check:
   - Body text: needs 4.5:1 minimum (WCAG AA)
   - Large text (18px+ bold or 24px+ regular): needs 3:1 minimum
   - UI components (borders, icons): needs 3:1 minimum
2. **Color-only information:** Look at the page and ask: "If I could not see color, would I lose any information?" Check the star rating, stock status, and form error states.
3. **Zoom test:** Zoom the browser to 200% (Ctrl/Cmd + plus). Does content reflow? Is anything cut off or overlapping?

---

## Part 3 -- Triage and Prioritize (15 min)

Before fixing anything, prioritize your findings by user impact:

**Priority 1 -- Blockers (user cannot complete a task):**
- "Add to Cart" not keyboard-accessible (cannot purchase)
- Modal focus trap broken (cannot interact with modal)
- Form inputs not labeled (cannot fill out form correctly)
- Custom dropdown not keyboard-accessible (cannot select options)

**Priority 2 -- Serious (user can complete task but with major difficulty):**
- No landmarks (must listen to entire page)
- No skip link (must Tab through all header links)
- No alt text (cannot understand product)
- Insufficient color contrast (cannot read content)
- No aria-live for dynamic content (does not know if actions succeeded)
- Price hidden from screen readers (cannot know the cost)
- No page title (screen reader users cannot identify the page; browser tabs show URL)

**Priority 3 -- Moderate (user experience degraded but task completable):**
- Skipped heading levels
- Color-only information
- Missing aria-expanded on accordion
- Decorative image with bad alt text
- Vague link text

**Priority 4 -- Minor (polish and best practices):**
- prefers-reduced-motion
- Touch target sizes

**Discussion:** "In real development, you fix Priority 1 issues before shipping. Priority 2 issues are fixed in the current sprint. Priority 3 in the next sprint. Priority 4 in the backlog. For this workshop, we will fix everything."

---

## Part 4 -- Fix the Issues (90 min)

**Important framing before starting:**

> **The First Rule of ARIA:** If you can use a native HTML element or attribute with the semantics and behavior you require already built in, do that instead of adding ARIA roles and properties to a non-semantic element. ARIA should be a last resort, not a first tool.

### Round 1 -- Document Structure and Semantics (20 min)

**Fixes:**
1. Add `lang="en"` to `<html>` and a descriptive `<title>`
2. Replace the outermost structural `<div>` elements with `<header>`, `<nav>`, `<main>`, `<footer>`
3. Fix heading hierarchy: ensure headings go h1 > h2 > h3 with no skipped levels
4. Replace the nav's `<div>` links with `<nav><ul><li><a href>` structure
5. Replace `<div onclick="addToCart()">` with `<button type="button" onclick="addToCart()">`
6. Add a skip link as the first child of `<body>`:

```html
<a href="#main-content" class="skip-link">Skip to main content</a>
<!-- ... -->
<main id="main-content">
```

```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  padding: 0.75rem 1.5rem;
  background: #000;
  color: #fff;
  z-index: 1000;
  font-weight: 600;
}
.skip-link:focus {
  top: 0;
}
```

**Why these matter:** Landmarks and headings are the primary way screen reader users navigate. Without them, using a webpage is like reading a book with no table of contents, no chapter titles, and no page numbers.

### Round 2 -- Images and Forms (20 min)

**Alt text -- how to write it well:**
- **Informational images:** Describe what the image conveys. Ask: "If I removed this image, what information would be lost?" That is your alt text.
  - Bad: `alt="image"`, `alt="photo.jpg"`, `alt="headphones"`
  - Good: `alt="SoundMax Pro wireless headphones in midnight blue, over-ear design with cushioned ear cups"`
- **Decorative images:** Use `alt=""` (empty string, not missing). This tells screen readers to skip it entirely.
  - Bad: `alt="decorative-line.png"` (screen reader reads the filename)
  - Good: `alt=""` (screen reader skips it silently)
- **Functional images** (images inside buttons/links): Describe the action, not the image.
  - Bad: `alt="magnifying glass icon"`
  - Good: `alt="Search"`

**Form fixes:**

0. Change the review form container from `<div class="review-form">` to `<form class="review-form" onsubmit="submitReview(event)" novalidate>`. A `<div>` has no form semantics -- screen readers cannot identify the region as a form, and native form features (like Enter to submit) do not work. The `novalidate` attribute disables browser-default validation so you can provide your own accessible error messages via `aria-describedby` and `aria-invalid`.

1. Add a visible `<label>` element for every input:

```html
<!-- Before: -->
<input type="email" placeholder="Your email">

<!-- After: -->
<label for="review-email">Email address</label>
<input type="email" id="review-email" placeholder="e.g., name@example.com">
```

2. Link error messages to inputs with `aria-describedby`:

```html
<label for="review-email">Email address</label>
<input type="email" id="review-email" aria-describedby="email-error" aria-invalid="true">
<span id="email-error" role="status">Please enter a valid email address</span>
```

3. Add form submission feedback using an `aria-live` region:

```html
<div aria-live="polite" id="form-status"></div>
```

```js
function submitForm() {
  // ... validation ...
  document.getElementById('form-status').textContent = 'Review submitted successfully. Thank you!';
}
```

**Checkpoint: Re-run axe DevTools now.** Your score should have improved significantly after fixing document structure, semantics, images, and forms. Many of the "Critical" and "Serious" findings from Part 1 should now be resolved. Note which issues remain -- these will guide your work in the next rounds.

---

### Round 3 -- Color and Visual (15 min)

**Fixing contrast:**
1. Change light gray text from `#999999` to `#595959` (or darker) to achieve 4.5:1 ratio on white
2. Use the DevTools color picker: click the contrast ratio, it shows two lines on the color spectrum -- the top line is the AA threshold, the bottom is AAA. Pick a color above the AA line.

**Fixing color-only information:**
- Star rating: add visible text like "(4 out of 5 stars)" or use `aria-label="4 out of 5 stars"` and visually display the number alongside the stars
- Stock status: change from just green text "In Stock" to include a checkmark icon: "In Stock" with a visible indicator beyond color
- Form errors: add an error icon (such as an exclamation mark) alongside the red text

**Restoring focus indicators:**
```css
/* Remove the harmful global rule: */
/* BAD: *:focus { outline: none; } */

/* Replace with visible, attractive focus styles: */
*:focus-visible {
  outline: 2px solid #1a56db;
  outline-offset: 2px;
}
```

**Why `focus-visible` instead of `focus`?** `:focus-visible` only shows the focus ring for keyboard navigation, not for mouse clicks. This gives keyboard users the indicator they need without the visual noise that made designers add `outline: none` in the first place.

### Round 4 -- Keyboard and ARIA (25 min)

**Fix 1: Make the custom color selector keyboard-accessible**

The color selector needs keyboard support. The best fix is to use native `<input type="radio">` elements styled as color swatches:

```html
<fieldset>
  <legend>Choose a color</legend>
  <label>
    <input type="radio" name="color" value="black" class="visually-hidden">
    <span class="color-swatch" style="background: #1a1a1a;" aria-label="Black"></span>
  </label>
  <label>
    <input type="radio" name="color" value="blue" class="visually-hidden">
    <span class="color-swatch" style="background: #1a56db;" aria-label="Blue"></span>
  </label>
</fieldset>
```

This approach uses native radio button behavior (arrow keys to navigate, Space to select) with visual styling to look like color swatches.

**Fix 2: Accordion ARIA pattern**

```html
<!-- Each accordion section: -->
<h3>
  <button
    aria-expanded="false"
    aria-controls="panel-specs"
    id="trigger-specs"
  >
    Specifications
  </button>
</h3>
<div
  id="panel-specs"
  role="region"
  aria-labelledby="trigger-specs"
  hidden
>
  <p>Panel content here...</p>
</div>
```

```js
function toggleAccordion(button) {
  const expanded = button.getAttribute('aria-expanded') === 'true';
  button.setAttribute('aria-expanded', String(!expanded));
  const panel = document.getElementById(button.getAttribute('aria-controls'));
  panel.hidden = expanded;
}
```

**Fix 3: Modal focus management**

```js
var modalTrigger = null;
var trapFocusHandler = null;

function openModal() {
  var modal = document.getElementById('quickViewModal');

  // Save the element that triggered the modal so we can return focus later
  modalTrigger = document.getElementById('quickViewTrigger');

  // Show the modal (uses CSS: .modal-overlay.open { display: flex; })
  modal.classList.add('open');

  // Prevent scrolling on the body while modal is open
  document.body.style.overflow = 'hidden';

  // Find all focusable elements inside the modal
  var focusableSelectors = 'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])';
  var modalContent = modal.querySelector('.modal');
  var focusableElements = modalContent.querySelectorAll(focusableSelectors);
  var firstFocusable = focusableElements[0];
  var lastFocusable = focusableElements[focusableElements.length - 1];

  // Move focus to the first focusable element (the close button)
  setTimeout(function() {
    firstFocusable.focus();
  }, 50);

  // Trap Tab within the modal and handle Escape
  trapFocusHandler = function(e) {
    if (e.key === 'Escape') {
      closeModal();
      return;
    }
    if (e.key !== 'Tab') return;

    if (e.shiftKey) {
      if (document.activeElement === firstFocusable) {
        e.preventDefault();
        lastFocusable.focus();
      }
    } else {
      if (document.activeElement === lastFocusable) {
        e.preventDefault();
        firstFocusable.focus();
      }
    }
  };

  document.addEventListener('keydown', trapFocusHandler);
}

function closeModal() {
  var modal = document.getElementById('quickViewModal');
  modal.classList.remove('open');
  document.body.style.overflow = '';

  // Remove the focus trap event listener
  if (trapFocusHandler) {
    document.removeEventListener('keydown', trapFocusHandler);
    trapFocusHandler = null;
  }

  // Return focus to the trigger element
  if (modalTrigger) {
    modalTrigger.focus();
    modalTrigger = null;
  }
}
```

**Fix 4: Remove incorrect `aria-hidden="true"` from the price**

Simply remove `aria-hidden="true"` from any visible, interactive content. `aria-hidden="true"` should only be used on content that is genuinely invisible or purely decorative (like an icon next to text that already provides the same information).

> **Also check the modal footer.** The starter has `aria-hidden="true"` on the modal footer `<div>`, which hides the interactive Close and Add to Cart buttons from screen readers. Remove that attribute as well.

**Fix 5: Add `aria-live` for dynamic cart updates**

```html
<div aria-live="polite" id="cart-status" class="visually-hidden"></div>
```

```js
function addToCart() {
  // ... add item logic ...
  document.getElementById('cart-status').textContent = 'SoundMax Pro headphones added to cart. Cart total: 1 item.';
}
```

Use `aria-live="polite"` for non-urgent updates (cart count, search results count). Use `aria-live="assertive"` for urgent updates (error messages, session timeouts).

**Fix 6: `prefers-reduced-motion`**

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

Also, any autoplaying video or animation should be paused by default when this preference is set, or at minimum provide a visible pause/stop control.

**Fix 7: Vague link text**

```html
<!-- Before: -->
<a href="/privacy">Click here</a>

<!-- After: -->
<a href="/privacy">Privacy Policy</a>
```

If the visual design requires short text, use `aria-label` to provide context:
```html
<a href="/reviews" aria-label="Read more customer reviews">Read more</a>
```

### Round 5 -- Final Cleanup (10 min)

1. Ensure all interactive elements meet minimum 24x24px touch target size (44x44px preferred)
2. Add `prefers-reduced-motion` media query to disable/reduce all animations
3. Ensure the decorative image uses `alt=""`
4. Run a final check: does every interactive element have an accessible name?

---

## Part 5 -- Re-audit and Reflect (30 min)

**Re-run automated tools (10 min):**
1. Run Lighthouse again -- compare before/after scores (target: 95+)
2. Run axe DevTools again -- should show zero violations
3. If any issues remain, fix them now

**Re-test manually (10 min):**
1. Keyboard-only: repeat the checklist from Part 2A. Every item should pass.
2. Screen reader: navigate by headings, by landmarks, and through the form. Everything should be properly announced.

**Group discussion (10 min):**
- Which violations were caught by automated tools and which required manual testing?
- Which fixes were just changing an HTML tag (easy) and which required JavaScript (hard)?
- Which issues would you never have noticed without a screen reader?
- How can we prevent these issues from being introduced in the first place?

**Preventative tools and practices to introduce:**
- **In the editor:** Use IDE extensions that flag accessibility issues (e.g., axe Linter for VS Code)
- **In code review:** Use a checklist -- does this PR add images (do they have alt text?), forms (do they have labels?), interactive elements (are they keyboard accessible?)?
- **In CI/CD:** Integrate `axe-core` via `@axe-core/cli` or `pa11y` to automatically fail builds that introduce accessibility regressions
- **In React projects:** `eslint-plugin-jsx-a11y` catches issues at lint time
- **In the design process:** Use a contrast checker plugin in Figma/Sketch. Ensure designs specify focus states, error states, and alt text.

---

## Key Takeaways

1. **Semantic HTML solves most accessibility issues for free.** A `<button>` is keyboard-accessible, focusable, and announced correctly by screen readers. A `<div onclick>` is none of these.
2. **The first rule of ARIA is "don't use ARIA."** Native HTML elements have built-in accessibility. ARIA is for custom widgets that have no native equivalent.
3. **Automated tools catch 30-40% of issues.** The rest require human testing with keyboard and screen reader.
4. **Accessibility is not a feature -- it is a quality attribute.** Like security or performance, it should be part of every task, not a separate ticket.
5. **Every fix has a human behind it.** A missing alt attribute means a blind person cannot see your product. A missing focus indicator means a keyboard user is lost on your page. Keep the human impact in mind.
6. **Prevention is cheaper than remediation.** Catching an issue in code review costs minutes. Catching it after launch costs hours of refactoring plus the users you have already excluded.

---

## Quick Reference

### Common ARIA Attributes

| Attribute | Purpose | Example |
|---|---|---|
| `aria-label` | Provides an accessible name when visible text is absent | `<button aria-label="Close">X</button>` |
| `aria-labelledby` | Points to the ID of an element that labels this one | `<div role="dialog" aria-labelledby="modal-title">` |
| `aria-describedby` | Points to the ID of an element that describes this one (e.g., error messages, instructions) | `<input aria-describedby="email-error">` |
| `aria-expanded` | Indicates whether a collapsible section is open or closed | `<button aria-expanded="false">` |
| `aria-hidden` | Hides content from screen readers (use only on truly decorative/redundant content) | `<span aria-hidden="true">&#9733;</span>` |
| `aria-live` | Makes a region announce dynamic changes (`polite` waits, `assertive` interrupts) | `<div aria-live="polite" id="status">` |
| `aria-invalid` | Marks a form field as having an invalid value | `<input aria-invalid="true">` |
| `aria-current` | Indicates the current item in a set (e.g., current page in breadcrumb) | `<span aria-current="page">` |
| `role` | Overrides the element's implicit role (use sparingly -- prefer semantic HTML) | `<div role="dialog">` |

### Contrast Ratio Requirements (WCAG 2.2 AA)

| Content Type | Minimum Ratio | Example |
|---|---|---|
| Normal text (under 18pt / 24px) | 4.5:1 | Body copy, labels, form text |
| Large text (18pt+ bold or 24px+ regular) | 3:1 | Headings, large UI text |
| UI components and graphical objects | 3:1 | Borders, icons, focus indicators |

### Keyboard Shortcut Summary

| Key | Action |
|---|---|
| `Tab` | Move to next focusable element |
| `Shift + Tab` | Move to previous focusable element |
| `Enter` | Activate links and buttons |
| `Space` | Activate buttons, toggle checkboxes, select radio buttons |
| `Escape` | Close modals, dismiss popups |
| `Arrow keys` | Navigate within composite widgets (radio groups, tabs, dropdowns) |

---

## Common Mistakes

1. **Using `role="button"` on a `<div>` instead of using `<button>`.** Adding `role="button"` makes the screen reader announce "button," but it does NOT add keyboard focusability (`tabindex` needed), Enter/Space activation (`onkeydown` needed), or form submission behavior. A native `<button>` gives you all of this for free.

2. **Removing focus outlines globally (`*:focus { outline: none; }`) without providing a replacement.** This makes the page invisible to keyboard users. If you must customize focus styles, use `:focus-visible` and provide a visible alternative (e.g., a colored outline or box-shadow).

3. **Using `aria-label` on a non-interactive `<div>` or `<span>`.** `aria-label` only works reliably on interactive elements (links, buttons, inputs) and landmark elements. On a generic `<div>`, most screen readers ignore it entirely.

4. **Putting `aria-hidden="true"` on visible, meaningful content.** `aria-hidden` removes content from the accessibility tree. If sighted users can see it and need it (like a price or a button), screen reader users need it too. Only use `aria-hidden` on purely decorative or redundant content.

5. **Using placeholder text as a substitute for labels.** Placeholder text disappears when the user starts typing, leaving them unable to remember what the field is for. It often has insufficient contrast, and screen readers may not reliably announce it as the field's accessible name. Always use a visible `<label>` element.
