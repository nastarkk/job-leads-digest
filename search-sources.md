# Search sources — what actually works

Learned live from running this against real India job searches (design-leadership roles specifically, so far). The source behaviors below — what fetches, what doesn't, what's JS-only — are properties of each *site*, not of any particular job category, so they should hold regardless of the candidate's field. The role-slug examples are design-flavored only because that's what's been tested; swap in slugs matching the candidate's actual target titles (`sales-manager`, `account-executive`, `software-engineer`, `marketing-manager`, etc.) and expect the same site-level quirks to apply. Update this file when a source's behavior changes, a new one proves reliable, or a role-slug pattern turns out to work differently for a non-design function — it's the part of the skill most likely to go stale.

## Verified working

**startup.jobs** — the most reliable source. Use location + role paths directly: `/locations/<country>/<role-slug>`, e.g. `/locations/india/product-designer`, `/locations/india/sales-manager`, `/locations/india/account-executive`. Construct the role slug from the candidate's target titles (lowercase, hyphenated) — this has been verified for design titles (`product-designer`, `design-manager`, `head-of-design`, `design-director`) and the URL structure is generic enough that other functions should follow the same pattern, but a slug guess can 404 or return nothing even when the general category exists — if so, try a nearby phrasing (e.g. `sales-lead` vs `sales-manager`) before concluding the site has nothing for that function. Pages return real listings via WebFetch.

**Wellfound** — `wellfound.com/role/l/<role-slug>/<city-slug>`. The role-slug filter has been unreliable for several natural-sounding design slugs (`design-lead`, `head-of-design`, `ux-lead`, `lead-product-designer` all silently fell back to an unfiltered city/country feed instead of erroring), while others filtered correctly (`design-director`, `design-manager`, `creative-director`). Expect similar inconsistency for other functions — try the specific slug, but also manually scan a broader feed (e.g. just the city page, or a more generic role slug for that function) to catch what a broken filter missed, and sanity-check that results returned actually match the requested role/city rather than trusting a non-empty response. City slugs are inconsistent too — common ones like `bangalore`, `india`, `pune`, `mumbai` work; less common ones may silently return an unfiltered global list rather than 0 results.

**Cutshort.io** — site search doesn't work, but this two-step does: `WebSearch` with `site:cutshort.io <role> <city>` to find URLs, then `WebFetch` the individual listing pages directly (those load fine). Skip any listing where the hiring company's name is withheld by a recruiting agency (e.g. "Talent Pro", "Qrata", "Peak Hire Solutions") — you can't verify funding or company health without knowing who's actually hiring. This applies to any function, not just design.

**Company career pages** via Greenhouse (`job-boards.greenhouse.io/<company>`), Kula (`careers.kula.ai/<company>`), Trakstar (`<company>.hire.trakstar.com`), Freshteam. These generally render fine via WebFetch, and list every open department (Sales, Engineering, Design, Marketing, etc.) on one board — search or filter for the candidate's function within the listing rather than assuming a design-specific URL path.

**Workday-hosted pages** (`<company>.wd###.myworkdayjobs.com`) often fail to render meaningful content via WebFetch — you may get metadata (job title, ID) but not location/experience/salary. When this happens, include the listing but mark it clearly as needing manual verification (e.g. a small caveat chip) rather than guessing at the missing fields.

**Entrackr** (entrackr.com) and **Inc42** (inc42.com) — good for India-focused funding roundups. Search or browse for the last ~8 weeks of startup funding news, then check each company's careers page directly for roles matching the candidate's function. This is also the main source for market-signal news (layoffs, hiring surges) — a WebSearch for `<company> layoffs` or `<company> news <year>` alongside these two. For candidates outside India, substitute the region's equivalent funding-news outlets (e.g. TechCrunch, Crunchbase News, regional business press) — the pattern (scan recent rounds, check careers pages directly) carries over.

## Doesn't work — don't waste time

- **LinkedIn Jobs and Naukri** block fetching outright, for any role.
- **YC's workatastartup.com** returns 406.
- **Hirist, Uplers** are JS-only — WebFetch returns an empty shell.
- **Weekday.works** — the site search/listing pages are JS-only, but individual job permalinks (`jobs.weekday.works/<slug>`) have worked when found via WebSearch. Worth trying, not worth relying on.
- **VC portfolio boards** (Accel, Peak XV, Blume, etc.) carry very few roles outside engineering/GTM leadership relative to the effort of checking them — still worth a quick scan for a sales or GTM-heavy search, less so for other functions.

## General tips

- When a page returns HTML but WebFetch summarizes it as empty or template-only, that's usually a JS-rendered SPA — try a WebSearch for the specific job title + company instead of re-fetching the same URL.
- A "0 results" response is sometimes a broken filter silently returning nothing, not genuinely zero openings — if a company is known to be hiring in that function elsewhere (e.g. per a LinkedIn presence you can't fetch, or a news mention), it's worth a second query with a broader term before concluding there's nothing.
- Always prefer the company's own careers page over a third-party aggregator when both surface the same listing — the aggregator can be stale.
- The first time you search for a new function on a given source, treat the role-slug as a hypothesis, not a fact — verify the returned listings actually match the requested function before trusting the feed for that source going forward.
