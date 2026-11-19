---
name: design-critique
description: Run and act on a Peekdesign critique — the six-axis 0–100 rubric, how to read the score, and how to translate each finding into a specific code change. Use when the user asks to review, score, critique or "polish" a design, or after publishing a Design Artifact.
---

# Design critique

`critique_design` (10 credits) scores a design against curated references. Give it the best evidence: an `artifact_id` (preferred — it renders the real thing), the component `html`/JSX/CSS, or a public `image_url`. Always pass `module` (e.g. "pricing section") and `goal` ("convert self-serve teams; feel premium"), plus the `reference_slugs` you designed from — critique without references drifts toward generic taste.

## The rubric

| Axis | What a high score means | Typical fixes |
| --- | --- | --- |
| **Hierarchy** | One obvious first read, a clear second, then detail. The primary action is unmistakable. | Increase display/body size ratio (≥ 2.5×), demote secondary actions to ghost/link, cut competing accents |
| **Typography** | A tight scale, deliberate weights, tracking tuned per size, 60–75ch measure | Negative tracking on display, raise body line-height to 1.5, drop a weight, fix widows with `text-wrap: balance` |
| **Color** | Restrained palette with roles; accent reserved for meaning; AA contrast | Map every color to a token role, remove a hue, darken muted text to ≥ 4.5:1 |
| **Spacing** | A consistent 4/8 scale; proximity groups related things; generous outer margins | Replace ad-hoc values with tokens, double section padding, tighten label↔value gaps |
| **Motion** | Purposeful, choreographed, correctly eased; respects reduced motion | Swap linear/ease for expo-out on enters, add 40ms stagger, shorten exits, add the reduced-motion guard |
| **Craft** | Details: optical alignment, consistent radii, crisp borders, states (hover/focus/empty/loading), responsive | Unify radii, add focus-visible rings, align icons optically, test at 360px |

Reading the overall: **85+** ship it; **70–84** solid, polish the two weakest axes; **55–69** direction is fine but execution is off — fix hierarchy and spacing first; **<55** revisit the direction (go back to references) rather than patching.

## From critique to code

1. **Triage** by impact ÷ effort. Hierarchy and spacing fixes usually move the score most; motion polish last.
2. **Make every fix concrete** before touching code: *where* (file + selector/component/token), *before → after*, *why* (which axis, which reference). "Improve hierarchy" is not a fix; "`.hero h1` 40px → `clamp(44px, 6vw, 72px)`, tracking −0.035em; CTA becomes the only filled button" is.
3. **Prefer token-level changes** — one edit to `--space-section` or the type scale fixes ten places and keeps the system coherent.
4. **Apply in the codebase first**, then mirror into the artifact with one `update_artifact` call (`edits` + `base_version`, message summarizing the round).
5. **Re-critique sparingly** — only after a meaningful round, at most twice per task without asking. Report the delta per axis ("Hierarchy 62 → 78").
6. **Disagree when warranted.** If a suggestion fights the brand or the user's stated goal, say so and skip it; the score serves the design, not the reverse.
