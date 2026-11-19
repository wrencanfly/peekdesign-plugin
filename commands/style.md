---
description: Get Peek's three style directions for a search and save the one you pick as STYLE.md and tokens
argument-hint: "<a look, mood or use — e.g. warm retro print for a coffee roaster>"
---

Search: $ARGUMENTS

1. Call `get_style_directions` with the search (ask for one in a few words if none was given).
2. Show the three directions: name, why, palette swatches (hex + role), display and detail fonts, what to keep and avoid, and the references with the artist's @handle.
3. Ask which to use. Then write its `style_md` to `STYLE.md` at the project root (or where the project keeps design docs), and merge its `tokens_css` into the project's tokens without breaking existing names. Mention the cost the tool reported (overviews are free for the first 10 and for a search anyone ran this week).
4. Credit the referenced artists in STYLE.md (it already lists the references) and in your reply.
