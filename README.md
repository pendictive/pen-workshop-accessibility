# Workshop: Accessibility Audit & Fix

A hands-on workshop for frontend juniors to learn web accessibility by finding and fixing real issues on a deliberately broken product page.

## What You'll Do

You'll receive a professional-looking e-commerce product page for **"SoundMax Pro" wireless headphones**. It looks great visually — but it contains **23 intentional accessibility violations** that prevent real users from navigating, understanding, or purchasing the product.

Your job: find every issue using audit tools, keyboard testing, and screen readers, then fix them all.

## What You'll Learn

- Semantic HTML and why it's the foundation of accessibility
- The first rule of ARIA: use native HTML before reaching for ARIA
- ARIA roles, states, properties, and live regions
- Keyboard navigation and focus management (including focus trapping)
- Color contrast, visual accessibility, and `prefers-reduced-motion`
- Screen reader navigation (practical keystrokes for NVDA, VoiceOver, Orca)
- How to write effective alt text and form labels
- How to prioritize accessibility fixes by user impact
- Using audit tools: axe DevTools, Lighthouse, browser accessibility inspector
- How to prevent accessibility regressions in a development workflow

## Prerequisites

- Basic HTML, CSS, and JavaScript knowledge
- Chrome or Firefox installed
- [axe DevTools](https://www.deque.com/axe/devtools/) browser extension (free)
- [Accessibility Insights for Web](https://accessibilityinsights.io/docs/web/overview/) extension (optional but recommended)

## Getting Started

1. Clone this repo:
   ```bash
   git clone https://github.com/pendictive/pen-workshop-accessibility.git
   cd pen-workshop-accessibility
   ```

2. Open **[WORKSHOP.md](WORKSHOP.md)** — this is your complete guide.

3. Open `starter/index.html` in your browser. This is the broken page you'll audit and fix.

## Duration

~4 hours

## Repo Structure

```
├── WORKSHOP.md          ← Start here — the full workshop guide
├── starter/
│   └── index.html       ← The broken page (open this and start auditing)
└── solution/
    └── index.html       ← The fully fixed page with teaching comments
```

## How It Works

1. **Find the issues** — Use Lighthouse, axe DevTools, keyboard-only navigation, and a screen reader
2. **Prioritize** — Triage by user impact (blockers → serious → moderate → minor)
3. **Fix** — Work through each issue category by category
4. **Re-audit** — Run the tools again and compare your before/after scores

## Final Solution

After fixing the page yourself, open `solution/index.html` to see the fully corrected version. Every fix includes a comment explaining **what** was wrong, **why** it matters for real users, and **how** the fix works.

> Both pages are single-file with zero dependencies — just open in a browser.
