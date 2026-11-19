---
name: design-artifacts
description: Build previews on a Peekdesign canvas (add_frame / edit_frame / get_frame_screenshot) or as hosted Design Artifacts (create_artifact / update_artifact) — html vs react, the libraries that import by name (three, React Three Fiber, drei, GSAP, framer-motion), real WebGL for glass and 3D, animation craft, looking at the render, versioned edits and sharing the URL. Use whenever you show a design on Peekdesign.
---

# Canvas frames and Design Artifacts

Two stages, same code:

- **A canvas** (`/c/<id>`) holds many frames side by side — screens, states, variants. The user watches you work live. Use it for anything with more than one screen or option. `list_boards` first and reuse their canvas; `create_board` (2 credits) only for a new project.
- **An artifact** (`/x/<id>`) is one hosted, versioned page to share. Use it for a single preview you'll send around.

Each change costs 1 credit (an identical rewrite is free); a screenshot costs 2. Batch related edits into one call.

## Pick a kind

- **`html`** — one complete HTML document. Best for CSS and GSAP motion, canvas/WebGL, or anything that should load instantly. Put CSS in `<style>`, JS in `<script>`. Module scripts can `import * as THREE from "three"`, `three/addons/…`, `gsap` and `gsap/…` by name; other libraries load from `cdnjs.cloudflare.com`, `cdn.jsdelivr.net`, `unpkg.com` or `esm.sh` with exact versions. Tailwind Play CDN works.
- **`react`** — one component file with `export default function …`. Tailwind classes work. These import by name: `react`, `framer-motion` (alias `motion`), `lucide-react`, `gsap`, `three`, `@react-three/fiber`, `@react-three/drei`, `@react-three/postprocessing`. Best when the design is stateful (tabs, toggles, a pricing switcher), mirrors a React codebase, or is a 3D scene.

Nothing else on the network: other CDNs, APIs and fonts outside Google Fonts are blocked. No base64 images — use https URLs.

## Rendered materials are WebGL

Glass, crystal, liquid, chrome, clay, soft 3D icons: CSS can only imitate them, and it shows. Build them as a three.js / React Three Fiber scene. Read our guide first — `read_guide` with id `glass-3d` (it has a tested frosted-glass template) — and keep text and UI in HTML on top of the canvas. Screenshots render WebGL (in software: slightly softer, accurate composition and colour); set `gl={{ preserveDrawingBuffer: true }}` so they capture the scene. A blank screenshot means the scene threw — check imports are bare names and nothing loads from a URL.

## Structure that reads as "designed"

- Full-bleed: `html, body { margin: 0; min-height: 100% }`, an explicit `background`, `-webkit-font-smoothing: antialiased`.
- Tokens once as CSS custom properties (`--bg`, `--ink`, `--accent`, `--radius`, `--ease-out`), used everywhere — critiques and edits get easier. From `get_style_directions`, paste its `tokens_css`.
- Responsive with `clamp()` for display type and spacing; check at 360px wide (a `phone` frame is 390×844).
- Plausible on-brief copy — never lorem ipsum, never the reference artist's text.
- Attribution in the artifact `description` when reference-driven: "Technique after <Name> (@handle)".

## Look at what you made

Pass `look: true` on `add_frame`, `write_frame` and `edit_frame`: the result comes back with a screenshot (+2 credits) so you check it in the same step. Look for overflow, clipped text, contrast, fonts that didn't load, an empty WebGL canvas. Fix with `edit_frame`, then look again. A screenshot is a still: make the settled state the one it shows, and reason about timing from the code. Never tell the user a design is done without having seen it.

## Animation craft

| Use | Curve | Duration |
| --- | --- | --- |
| Enter / reveal | `cubic-bezier(0.16, 1, 0.3, 1)` (expo-out) | 400–700ms |
| UI response (hover, press) | `cubic-bezier(0.2, 0, 0, 1)` | 120–200ms |
| Exit | `cubic-bezier(0.5, 0, 0.75, 0)` (quart-in) | 60–70% of enter |
| Move on screen | `cubic-bezier(0.65, 0, 0.35, 1)` (in-out) | 300–500ms |
| Sheet / drawer | `cubic-bezier(0.32, 0.72, 0, 1)` | 400–500ms |
| Playful | spring, `stiffness 300–500, damping 25–35` | — |

- **Stagger** siblings 30–60ms (cap the cascade near 400ms), in reading order.
- **Choreograph**: one hero motion, everything else supports it. Opacity finishes before transform settles.
- Animate only `transform`, `opacity`, `filter` and `clip-path`; `will-change` only on what is actually moving.
- Loops: ease-in-out, 4–12s, subtle; pause off-screen and when the tab is hidden. In WebGL, damp motion and keep it slow.
- **Always** honor reduced motion:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: 1ms !important; animation-iteration-count: 1 !important; transition-duration: 1ms !important; scroll-behavior: auto !important; }
}
```

In JS: `matchMedia('(prefers-reduced-motion: reduce)').matches` → jump to end states (`useReducedMotion()` in framer-motion, `gsap.matchMedia()`).

## Editing

- Canvas: `read_board` before editing; don't change a frame someone else touched seconds ago unless asked — put new work in a new frame or `fork_variant`. `set_status` with a short present-tense line while you work.
- `edit_frame` / `update_artifact` with **`edits`** (`old_str` → `new_str`, each copied exactly from the latest code and unique) for targeted changes; full code only for rewrites.
- Artifacts: always pass **`base_version`** (the version you last read or created). On a conflict, `get_artifact`, merge onto the newer code, retry — never blindly overwrite. Add a short `message` per version.

## Sharing

Return the URL (`…/c/<id>?frame=<frame>` or `…/x/<id>`) every time you create or change something, with one line on what changed. List existing work (`list_boards`, `list_artifacts`) before making a duplicate.
