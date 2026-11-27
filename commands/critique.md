---
description: Score a design (artifact, page or component) against curated references and turn the critique into fixes
argument-hint: "[artifact id/URL, file path, or public screenshot URL] [goal]"
---

Use the `design-critique` skill.

Target: $ARGUMENTS

1. Resolve the target: a Design Artifact id or `/x/<id>` URL → `artifact_id`; a canvas frame → `read_frame` and pass its code as `html`; a local file or component → read it and pass its source as `html`; an image URL → `image_url`. If nothing is given, use the component or page most recently edited in this session.
2. If references were chosen earlier in this session, pass their slugs as `reference_slugs`; pass the module and the goal when known.
3. Call `critique_design` (from 20 credits, metered by model use).
4. Report the overall score and the six sub-scores in one compact table, then the top fixes ordered by impact ÷ effort.
5. Map each fix to a concrete code change (file, selector or token, before → after). Ask before applying, then apply the top 3, look at the result (`get_frame_screenshot` or `look: true`), and offer a re-critique.
