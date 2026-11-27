---
description: Hand a design brief to Peek, Peekdesign's own design agent, watch it work on a canvas, and bring the result back
argument-hint: "<what to design — subject, look, sizes>"
---

Brief: $ARGUMENTS

1. If the brief is thin, add what you know from the codebase and this conversation: the product, the brand colors and fonts, the size or format, and any style direction or references chosen earlier (their slugs go in `reference_slugs`).
2. Tell the user Peek is billed by its real usage (a new design is usually 120–300 credits on Opus at medium effort; an account that has never paid gets its first run free; `get_account` shows the balance) and get a yes, unless they already asked for Peek by name.
3. Call `ask_peek` (reuse the current canvas's `board_id` if there is one). Give the user the `canvas_url` right away so they can watch.
4. Call `get_peek_run` with `wait_seconds: 120`, repeating until the status is no longer `running`.
5. Summarise Peek's reply and the frames it made, with links. Check the main frame with `get_frame_screenshot`.
6. Offer to bring it into the codebase: `read_frame` for the code, then adapt it to the project's components and tokens. For changes, run `ask_peek` again with the same `board_id`.
