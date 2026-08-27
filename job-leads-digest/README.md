# Job Leads Digest

A Claude skill that sets up a personalized, recurring job-leads email digest for any career function — design, sales, engineering, marketing, ops, whatever the candidate does. Sourced live from job boards and funding news, matched against the candidate's own resume and proof-of-work (portfolio, GitHub, LinkedIn, or described achievements), deduped over time, and delivered as a clean, card-based HTML email on a schedule they pick.

## What it does

- Interviews a candidate once (field, target titles, locations, funding-stage preference, resume, proof-of-work, delivery schedule, watch list) and turns that into a standing scheduled task.
- Every run: searches known-reliable job boards, cross-checks recent funding and company news (layoffs, hiring surges), dedupes against everything already sent, and grounds its "why this fits you" reasoning in specifics from the candidate's actual resume and proof-of-work — never generic phrasing.
- Sorts findings into direct-fit roles, an "Apply + pitch up" bucket (well-funded companies with an IC opening and no leader in the candidate's function yet), and cold-outreach targets.
- Emails the result as a personally-greeted, card-based HTML digest — sourced funding figures, market-signal chips, stage-grouped listings, all built to survive Gmail's mobile app (which strips `<style>` blocks — every style here is inlined for that reason).

## How to use it

Install this skill in a Claude session with scheduled-task support (Claude Code, Cowork). Then just ask: *"set up a job digest for me"* or *"email me new [role] openings weekly."* The skill handles the interview and creates the scheduled task; see `SKILL.md` for the full setup and run logic.

## Structure

```
job-leads-digest/
├── SKILL.md                       — setup + scheduled-run instructions
├── references/
│   ├── search-sources.md          — which job boards work, their quirks
│   └── email-design.md            — the email's visual/HTML spec
└── assets/
    └── logo.png                   — header logo
```

---

Skill crafted by [Naseer Ahmed](https://www.linkedin.com/in/nastweets/) — [starkvibes.com](https://starkvibes.com/)
