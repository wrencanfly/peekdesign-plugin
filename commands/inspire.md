---
description: Turn what you're building into three style directions with real references, then set one up in your codebase
argument-hint: "<what you're building — product, audience, mood, module>"
---

Act as an art director using the `art-director` skill and the Peekdesign MCP tools.

Brief: $ARGUMENTS

1. If the brief is missing the product, the mood or the module (hero, pricing, onboarding, icons…), ask at most two short questions — otherwise infer from the codebase and go.
2. Call `get_style_directions` with a short plain-words version of the brief ("calm fintech onboarding", "3D glass icons"). For a long, specific brief with constraints, call `find_inspiration` instead.
3. Present the three directions compactly: name, one line on why it fits, palette (hex + role), the font pairing, and 2–3 references with the artist's @handle and link.
4. Ask which one to use (or whether to mix). Don't build yet.
5. When the user picks: save that direction's `style_md` as `STYLE.md`, merge its `tokens_css` into the project's tokens (Tailwind config or CSS variables, matching how the project already does it), add the Google Fonts, and offer to build the first module.
