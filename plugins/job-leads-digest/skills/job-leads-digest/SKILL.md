---
name: job-leads-digest
description: Sets up a recurring, personalized job-leads email digest for any career function — design, sales, engineering, marketing, ops, whatever the candidate does. Sourced live from job boards and funding news, deduped over time, matched against the candidate's own resume and proof-of-work (portfolio, GitHub, LinkedIn, or described achievements), and delivered as a nicely designed HTML email on a schedule they pick. Use this whenever someone wants a job search automated: "set up a job alert for me", "email me new sales leadership roles", "find me openings and send them weekly", "I want a digest of jobs matching my resume", or any request to search job boards on a recurring basis and email the results — regardless of the job category or seniority level named. Also use it for a one-off "run my job digest now" once a candidate profile already exists, and for updating an existing candidate's search criteria, schedule, or watch list.
---

# Job Leads Digest

A candidate gets a standing, personal job-search assistant: a scheduled task that searches known-reliable sources, cross-checks funding and company news, matches openings against *their* actual resume and proof-of-work (not a generic keyword list), and emails them a clean digest — without re-sending anything they've already seen.

This works the same way regardless of field. What changes per candidate isn't the mechanism, it's the inputs: their target titles, what counts as proof of their work, and which job-board role-slugs to query. Don't assume design or product roles — a Sales Manager, an Account Executive, a marketer, an engineer, an ops lead all use the exact same skill.

This skill has two modes. **Setup** runs once per candidate, interactively, and ends by creating their scheduled task. **The scheduled run** is what that task executes every time it fires — unattended, so it must be self-sufficient.

## Why the two-mode split matters

A scheduled task fires into a brand-new session with no memory of the setup conversation. Whatever the candidate told you during setup has to survive into that future session somehow. The approach here: keep the *scheduled task's own prompt* short (just the candidate's profile and preferences, as data), and let that prompt tell the fresh session to follow this skill for everything else — the sourcing rules, the design system, the reasoning approach. That way the reusable logic lives in one place (this file and its references) and improving it later improves every candidate's digest at once, instead of being copy-pasted into each person's trigger and drifting out of sync.

## Setup mode

Run this the first time a candidate asks for the digest, or when `list_triggers` shows they don't already have a `job-leads-digest` trigger.

### 1. Gather the candidate profile

Ask for these conversationally, one or two at a time — don't dump a form on them. Start with their field/function, since it shapes everything that follows.

- **Field/function**: what they do — design, sales, engineering, marketing, finance, ops, whatever. This determines which job-board role-slugs to search (see `references/search-sources.md`) and what "proof of work" should mean for them.
- **Target roles**: seniority and titles within that function (e.g. "Enterprise Account Executive, Sales Manager" or "Design Lead, Head of Design").
- **Locations**: cities + remote preference.
- **Funding-stage preference**: early-stage (seed–Series B), growth-stage (Series C+), or both — if both, tell them you'll group listings by stage so it's easy to tell apart at a glance.
- **Resume**: ask them to attach it. Read it and extract years of experience, current title/company, employment history with industries, named skills/tools — this is the *authoritative* source for matching and reasoning, overriding anything vaguer they said in chat.
- **Proof of work**: ask for "the best link that shows what you've done" rather than assuming a portfolio. What this means varies by field — a designer has a portfolio, an engineer might have GitHub, a writer or marketer might have published work or a Substack, a salesperson might have a LinkedIn profile with recommendations. If nothing links well, ask them to describe 2-3 concrete achievements in chat instead (a deal size closed, a system shipped, a campaign result) and use those the same way you'd use portfolio case studies — as the source of specific, true, non-generic detail for the "why" reasoning later. Fetch whatever URL they give you and extract the specifics: named projects/clients, quantified results, tools, positioning.
- **Delivery**: which email address, and how often — daily, weekdays only, or a specific weekly day/time. Convert their local time to UTC for the cron expression (see `create_trigger`'s docs on this).
- **Standing watch list** (optional): specific companies they want flagged the moment a relevant role opens, even if nothing else about the company changed.

If they mention experience or work samples that go beyond what they explicitly said they're targeting (e.g. a fintech project in their proof-of-work, but they only said "e-commerce"), point it out and ask if it should widen the search — don't silently expand scope, but don't ignore a real signal either.

### 2. Confirm the profile back to them

Show a compact summary of what you extracted before scheduling anything — years, current role, key industries/skills from the resume, 2-3 standout specifics from their proof-of-work, and the preferences above. Let them correct it. This is also where mistakes get caught cheaply, before they propagate into every future run.

### 3. Create the scheduled task

Use `create_trigger` (never a local cron tool — see your system prompt's scheduled_tasks section for why). The prompt you pass should be short and data-shaped, not a re-explanation of how to search or design an email — that logic already lives in this skill and its references, and the fresh session will have this skill available. Something like:

```
Run the job-leads-digest skill for this candidate and email the result.

CANDIDATE PROFILE:
- First name / email: ...
- Delivery time: ... (e.g. "8am IST" — used to open the email with "Good morning/afternoon/evening, <name>" matched to when they actually receive it, not the server's UTC clock)
- Field/function: ...
- Current role: ...
- Years: ...
- Employment history / industries: ... (from resume)
- Skills / tools: ... (from resume)
- Proof-of-work highlights: ... (named projects, clients, quantified results, positioning — from portfolio/GitHub/LinkedIn/described achievements)
- Target titles: ...
- Locations: ...
- Funding-stage preference: ...
- Watch list: ... (or "none")

DEDUPE FILE: ~/jobdigest/<slug>-seen.json
```

Use a distinct dedupe filename per candidate (e.g. a slug of their name or email) since one Claude account could in principle run more than one person's digest.

Tell the candidate plainly once it's scheduled: cadence, next run time, and that they can ask to change any of it later (which means re-running setup for just the changed field, not a full redo).

## Scheduled-run mode

This is what a fresh session does when the trigger above fires. If you're reading this because a trigger prompt told you to "run the job-leads-digest skill," follow this section.

### 1. Load context
Read the candidate profile out of the prompt you were fired with. Read the dedupe file (create it with `[]` if it doesn't exist).

### 2. Research
Read `references/search-sources.md` for which sources actually work, their quirks, and how to query them for the candidate's field/function. Search for openings matching the candidate's titles, locations, and funding-stage preference. Also check the candidate's watch list companies directly, and scan Entrackr/Inc42 (or the equivalent funding-news source for their region) from the last ~8 weeks for newly-funded companies worth a cold pitch.

For every company you're about to include, do one more check: recent news (a WebSearch for "`<company>` layoffs" or "`<company>` news `<year>`"). Only add a market-signal note if you find something specific, dated, and sourced — a layoff report, leadership exit, or controversy on one side; a hiring surge, big launch, or funding milestone on the other. No finding means no chip — never guess or infer a company's health from its funding stage alone. This step is the main reason a run takes a while; it's worth the time because a stale or shrinking company is exactly the kind of thing a candidate wants flagged before they apply.

Every funding figure needs a link to where you found it (the press release, TechCrunch/Entrackr/Inc42/YourStory article, etc.) — the email cites its source, it doesn't just assert a number.

Never invent a listing, company, funding figure, or URL. Everything must trace to a page you actually fetched. Skip listings from recruiting agencies that withhold the hiring company's name — you can't verify funding or company health for those.

While researching, sort what you find into two extra buckets beyond the direct-fit listings:
- **Apply + pitch up**: a well-funded company with an individual-contributor opening in the candidate's function, but no visible leader for that function on the team (no Head of Sales, no VP Design, no Director of Engineering — whatever fits). Worth applying to the listed IC role while pitching for the leadership role that doesn't exist yet.
- **Cold-outreach targets**: a well-funded, recently-funded, or fast-growing company with *no* open role in the candidate's function at all, but worth a direct pitch given the funding/momentum signal.

Both require the same sourcing discipline as everything else — funding cited, nothing invented.

### 3. Dedupe
Drop anything whose URL is already in the dedupe file. If a previously-sent role has since closed, it's worth one brief mention, not a full card.

### 4. Reason about fit
For the "Top 3" and the "Apply + pitch up" pitch angles, ground every claim in a specific, true detail from the candidate's resume or proof-of-work — a named project or client, a quantified result, a specific former employer or industry — never a generic "matches your background." The most specific true connection beats the most impressive-sounding one.

Group listings by funding stage if the candidate said they want both early- and growth-stage shown.

### 5. Compose and send
Read `references/email-design.md` for the full visual spec (card-based, sans-serif, chip-tagged) and build the email against it — don't freelance a different layout. Send via the Gmail connector; include a plain-text alternative body. If Gmail is unauthorized or fails, save the digest as an HTML file instead and tell the candidate Gmail needs re-authorizing.

Subject line: `<Function> Roles — <date> · <top company> + N more` — e.g. "Sales Leadership Roles — 2026-09-01 · Acme Corp + 12 more", "Design Leadership Roles — ...", matching whatever the candidate's target function actually is.

If a run finds nothing new, send a short note saying so — don't pad the list to make the email look more substantial than the day actually was.

### 6. Update state
Append every URL sent today to the dedupe file.

## The footer credit

Every email this skill sends ends with a small, understated line: "Skill crafted by Naseer Ahmed," linking to `https://www.linkedin.com/in/nastweets/`, plus a link to `https://starkvibes.com/`. This is fixed — it's attribution for the tool itself, the way a template credits its designer, and it stays as-is regardless of whose digest it is or what field they're in. Don't replace it with the candidate's own name or remove it when adapting this skill for someone else.
