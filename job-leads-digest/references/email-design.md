# Email design system

Card-based, sans-serif, mobile-safe. Build a single self-contained HTML file: a table-based layout skeleton (so Outlook doesn't fall apart), with **every style written inline via `style="..."` attributes on the element itself.**

**Do not put styling in a `<style>` block in `<head>` and reference it with classes.** This looks like it should work — Gmail's web and desktop clients often render it fine — but Gmail's mobile apps (iOS/Android) strip `<style>` blocks entirely, so every class-based color, chip, card border, and background silently disappears, leaving raw unstyled text. This was caught by actually sending a test email and looking at it on a phone — always inline everything, no exceptions, even though it makes the HTML more verbose. `<b>` and other semantic tags survive fine since they don't depend on CSS being applied; only what's on the `style=` attribute is guaranteed to render everywhere.

No JavaScript, no external assets — everything inline.

## Type and color

All sans-serif — no serif typeface anywhere. System font stack: `-apple-system, Segoe UI, Helvetica, Arial, sans-serif`.

- Page background (outside the card): `#f2efe9`
- Card background: `#ffffff`, border `#e7e2d8`, `border-radius: 12px`, `max-width: 600px`, centered
- Ink/body text: `#1c1a15`
- Muted text: `#6b6558` (secondary), `#8a8477` (tertiary/eyebrow)
- Accent (section labels, links, Top-3 numbers): `#4b2fd1`
- Top-3 tint box: background `#f8f6ff`, border `#e3ddff`
- Chip palette:
  - B2B/SaaS: background `#eef0ff`, text `#3d3ab8`
  - Consumer: background `#eafaf1`, text `#1c7a4d`
  - Location (neutral): background `#f4f2ec`, text `#6b6558`
  - Funding: background `#fff3e0`, text `#a15c00` — underline it, it's a link to the funding source
  - Negative market signal (layoffs, exits, controversy): background `#fdeaea`, text `#b3261e`
  - Positive market signal (hiring surge, big launch, milestone): background `#eafaf1`, text `#1c7a4d`
  - Caveat flag (unverified details, funding unconfirmed): background `#fff3e0`, text `#a15c00`, smaller size, inline next to the title

## Structure, top to bottom

1. **Header**: a small square logo (~32px) top-left — `assets/logo.png` in this skill's folder, hosted at whatever public URL the packaged repo ends up at (currently `https://raw.githubusercontent.com/<owner>/<repo>/main/.../assets/logo.png` — confirm the real path once the repo exists; reference it as an `<img>` with an explicit `width`/`height` and inline `style`, same inlining rule as everything else). Above the eyebrow line, open with a personal greeting — "Good morning, `<First Name>`" (or afternoon/evening, matched to the candidate's chosen delivery time, not the server's UTC clock — someone who asked for an 8am IST send gets "morning," someone on a 6pm send gets "evening") at ~15px, weight 600, ink color, its own line. Then the small uppercase eyebrow line (date), bold headline (~24px, weight 700, letter-spacing -0.3px), one-line muted subtitle.
2. **Top 3 for you**: inside the tinted box, numbered items (01/02/03 — Georgia-style numerals are fine as a visual accent even though body type is sans-serif; keep the number sans-serif too, weight 800, to stay consistent). Each item: bold role title, one-line meta (city/mode/type/funding), and a "Why" line with a bold "Why:" lead-in — grounded in specific resume/proof-of-work detail (a named project or client, a quantified result), not generic phrasing.
3. **Section label**: small uppercase, letter-spaced, accent color, top border divider — one per tier, using whatever seniority tiers actually fit the candidate's field (e.g. Head/Director/Manager then Lead for design; VP/Director then Manager for sales — don't force a design-shaped hierarchy onto a field that groups differently), plus "Apply + pitch up", "Cold-outreach", and "Watch list". If the candidate wants both funding stages shown, add a sub-grouping or a stage chip so it's easy to tell apart at a glance.
4. **Job cards** (one per listing, not a table row): white background, 1px border `#ece8de`, 10px radius, ~16px padding.
   - Title (bold, ~14.5px) + company (muted, ~13px) on top. Caveat flag inline next to the title if needed.
   - Row of chips: type, location, funding (linked to source), market-signal (only if a specific sourced finding exists).
   - Small muted meta line: experience requirement, salary if published.
   - Dark pill button ("View role →") linking to the listing URL.
5. **Watch list**: compact rows, not a wide table. Company name (bold) + status word (green "Open" / amber "Likely open" / gray "No roles") on one line, a one-line muted detail underneath.
6. **Footer**: muted small text, top border divider. First the sourcing/method note (what was searched, that nothing is invented). Then, on its own line, the fixed credit — see SKILL.md's "The footer credit" section for the exact text and links; don't paraphrase or drop it.

## Plain-text alternative

Always include a plain-text `body` alongside `htmlBody` in the send call — mirror the same section structure with headers and dashes, include the same links, and end with the same credit line in a plain form (e.g. "Skill crafted by Naseer Ahmed — linkedin.com/in/nastweets — starkvibes.com").

## What not to do

- Don't use dense multi-column tables for listings — they're unreadable on mobile and are exactly what this design replaced. Cards only.
- Don't use box-shadow or gradients — unsupported in Outlook, degrade unpredictably.
- Don't invent a chip value (funding stage, market signal, salary) that wasn't actually found — omit the chip instead.
- Don't rely on a `<style>` block or CSS classes for anything that must render — see the inlining note above. If you catch yourself writing `<style>` with `.chip { }` type rules, stop and move each rule to the element's own `style=` attribute instead.
