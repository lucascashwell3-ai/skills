---
name: show-me
description: Turn a text-heavy answer into one scannable visual page. Use whenever a reply would have more than ~5 parts — status across several things, options to pick from, a plan, a comparison, a storyboard, an inventory, a review of a build — or when the user says "show me", "visualize it", "make it a page", "too much text", or sounds overwhelmed.
license: MIT
---

# Show me — one page instead of a wall of text

People read pictures and short lines faster than paragraphs. A page must be scannable in 30
seconds and answerable out loud ("3 is wrong, keep 5").

1. **Put the page where this app can show one:**
   - **The app shows pages** (Claude artifacts, ChatGPT canvas, and the like): build ONE page
     there, within the app's own technical rules for pages.
   - **The app can write files** (Claude Code, Codex, Cursor, Gemini CLI, Copilot): write ONE
     self-contained HTML file — inline CSS, no outside scripts or fonts — to a temp folder, open
     it (`open` on macOS, `xdg-open` on Linux, `start` on Windows), and give the path.
   - **Neither:** the same shape in the chat as Markdown — the top box as a quote, then the
     numbered one-liners and at most one table.
2. **Shape of the page:**
   - Top box: **what you need from them / what they need from you** — the one or two
     decisions, each answerable by a word or a number.
   - Then the items, **numbered**, one line each, in plain words. Their own words (quoted)
     wherever they exist. A status pill (Done / Almost / Waiting on you / Parked / Broken) or a
     small picture instead of a sentence wherever possible.
   - Detail hides behind a toggle; nothing important lives only in the toggle.
   - No paragraphs. No jargon. Real links.
   - Readable in light and dark, and at phone width.
3. **The look** — clean and quiet, in the spirit of cursor.com, unless they ask for another:
   - One background and one text colour. Softer text and borders are that same text colour,
     fainter — no extra greys. Borders you barely notice. No gradients, glows, coloured shadows
     or stripe borders.
   - One accent colour, kept for the top box and whatever needs action first.
   - Colour only where it means something: a small filled pill or dot per item — red = needs
     them now, amber = soon, green = done, grey = nothing to do.
   - A filled icon or emoji only where it says something faster than a word — a status, a kind
     of item, an action (⏰ deadline, 💬 reply, 📅 meeting, ✅ done). At most one per line; never
     decoration.
   - One clean sans font, modest sizes, plenty of space between groups; numbers line up.
4. **Chat reply:** the link or file path, at most 3 lines, one ask. Never repeat the page in
   chat.
5. **Facts come from the source** — files, tools, live checks — never from memory alone.
6. **Revise the same page** (or overwrite the same file) so they keep one link.
