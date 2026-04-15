---
description: Frontend UI quality — distinctive design, accessible, performant, production-grade components.
globs: __GLOBS__
---

# Frontend Quality

Production frontend is judged on craft: visual hierarchy, typography, spacing rhythm, motion, performance, and accessibility. Avoid the generic AI-assistant aesthetic (gradients for no reason, rounded corners everywhere, emoji padding, sameness).

## Rules

- **Do** use a consistent spacing scale (4/8px base) — never ad-hoc pixel values.
- **Do** pick one type scale and stick to it — 5–6 sizes max.
- **Do** respect accessibility baseline: contrast ≥ 4.5:1 body / 3:1 large, focus-visible on every interactive element, semantic HTML first.
- **Do** measure — LCP < 2.5s, INP < 200ms, CLS < 0.1 as a floor.
- **Do not** use div soup — use `<button>`, `<nav>`, `<main>`, `<article>` etc. for their semantics.
- **Do not** animate for decoration — animate to clarify state transitions and cause / effect only.
- **Do not** ship generic gradient-purple-pill-shaped components by default.

## Component quality checklist

- Loading, error, empty, and disabled states are designed, not afterthoughts.
- Target size ≥ 24×24px (WCAG 2.2 Target Size Minimum).
- Keyboard navigable with visible focus ring.
- Respects `prefers-reduced-motion`.
- Dark mode is derived from design tokens, not duplicated styles.
- No hardcoded strings that need i18n later — extract from day one if i18n is in scope.

## Performance discipline

- Code split at routes; lazy-load heavy components.
- Images: correct dimensions, modern format (AVIF/WebP), `loading="lazy"` below the fold.
- Fonts: `font-display: swap`, subset, preload only the critical face.
- No blocking third-party scripts above the fold.
