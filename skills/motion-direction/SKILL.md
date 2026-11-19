---
name: motion-direction
description: Turn a Peekdesign spec's motion beats (timing, easing, staggering, loops) into production animation code in CSS, GSAP or framer-motion, with an easing translation table. Use when implementing or matching animation from a reference video or a get_design_spec motion timeline.
---

# Motion direction

`get_design_spec` on a video returns a **motion timeline**: beats with start time, duration, the element, the property and an easing description. Your job is to translate those beats faithfully, then make them robust.

## 1. Normalize the timeline

Write the beats as a table before coding — this is the score you'll conduct:

| t (ms) | dur | element | property: from → to | easing | notes |
| --- | --- | --- | --- | --- | --- |
| 0 | 600 | headline lines | y 40px → 0, opacity 0 → 1 | expo-out | stagger 60ms |
| 250 | 500 | hero media | scale 1.08 → 1, clip-path inset 12% → 0 | quint-out | |
| 700 | 300 | CTA | opacity, y 8 → 0 | ease-out | |

- Convert seconds to ms, round to 10ms. Overlaps are intentional — keep them.
- Map every visual change to `transform` / `opacity` / `clip-path` / `filter`. Re-express width/height/top changes as scale/translate (FLIP if layout must change).
- Identify the **hero beat**. Everything else should feel like it's responding to it.

## 2. Easing translation

| Spec says | CSS `cubic-bezier` | GSAP | framer-motion |
| --- | --- | --- | --- |
| linear | `linear` | `none` | `"linear"` |
| ease-out / decelerate (cubic) | `(0.33, 1, 0.68, 1)` | `power2.out` | `[0.33, 1, 0.68, 1]` |
| strong / expo-out, "snappy settle" | `(0.16, 1, 0.3, 1)` | `expo.out` | `[0.16, 1, 0.3, 1]` |
| quint-out | `(0.22, 1, 0.36, 1)` | `power4.out` | `[0.22, 1, 0.36, 1]` |
| ease-in / accelerate (exits) | `(0.5, 0, 0.75, 0)` | `power3.in` | `[0.5, 0, 0.75, 0]` |
| ease-in-out, "smooth" | `(0.65, 0, 0.35, 1)` | `power2.inOut` | `[0.65, 0, 0.35, 1]` |
| sine, "breathing" loop | `(0.37, 0, 0.63, 1)` | `sine.inOut` | `[0.37, 0, 0.63, 1]` |
| overshoot / back-out | `(0.34, 1.56, 0.64, 1)` | `back.out(1.7)` | `[0.34, 1.56, 0.64, 1]` |
| iOS sheet | `(0.32, 0.72, 0, 1)` | `CustomEase` or `expo.out` | `[0.32, 0.72, 0, 1]` |
| spring, "bouncy", "physical" | `linear()` approximation | `elastic.out(1, 0.5)` (sparingly) | `{ type: "spring", stiffness: 400, damping: 30 }` |
| stepped / glitch | `steps(n)` | `steps(n)` | `{ ease: (t) => Math.floor(t * n) / n }` |

## 3. Pick the engine

- **CSS** — entrances, hovers, loops with ≤ 3 beats. `@keyframes` + `animation-delay` for staggers (`style="--i: 2"` → `animation-delay: calc(var(--i) * 60ms)`). Scroll-linked: `animation-timeline: view()` with a fallback.
- **GSAP** — timelines with overlaps, scrubbed scroll (`ScrollTrigger`), SVG/morphs, anything > 3 beats. Use one `gsap.timeline({ defaults: { ease: "expo.out", duration: 0.6 } })`, position parameters (`"<0.1"`, `"-=0.2"`) to encode overlaps, `stagger: { each: 0.06, from: "start" }`. Wrap in `gsap.context()` / `useGSAP()` and revert on unmount.
- **framer-motion** — React state-driven motion, layout animations, gestures, exit animations. Variants with `staggerChildren: 0.06, delayChildren: 0.1`; `AnimatePresence` for exits; `layout` for FLIP for free.

```tsx
const list = { show: { transition: { staggerChildren: 0.06, delayChildren: 0.1 } } };
const item = { hidden: { opacity: 0, y: 24 }, show: { opacity: 1, y: 0, transition: { duration: 0.6, ease: [0.16, 1, 0.3, 1] } } };
```

## 4. Make it robust

- **Reduced motion:** keep opacity fades ≤ 150ms, drop translation, parallax, loops and autoplay (`useReducedMotion()`, `gsap.matchMedia()`, or the CSS media query).
- **Performance:** 60fps on a mid phone — no animating `box-shadow`, `filter: blur` over large areas, or layout properties; pause loops off-screen; `will-change` only during the animation.
- **Interruptibility:** hover/press animations must reverse from their current state (CSS transitions or GSAP `quickTo`), never restart.
- **Timing sanity:** total intro ≤ ~1.2s before content is usable; exits 60–70% of entrance; the same easing family across the page.
- Preview the result as a Design Artifact (see `design-artifacts`) and compare against the reference side by side; adjust durations in 50ms steps.

Credit the reference artist for the motion idea; reproduce the technique, not their footage or assets.
