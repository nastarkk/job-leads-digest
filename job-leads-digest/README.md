# Job Leads Digest

A Claude skill that sets up a personalized, recurring job-leads email digest for any career function — design, sales, engineering, marketing, ops, whatever the candidate does. Sourced live from job boards and funding news, matched against the candidate's own resume and proof-of-work (portfolio, GitHub, LinkedIn, or described achievements), deduped over time, and delivered as a clean, card-based HTML email on a schedule they pick.

## What it does

- Interviews a candidate once (field, target titles, locations, funding-stage preference, resume, proof-of-work, delivery schedule, watch list) and turns that into a standing scheduled task.
- Every run: searches known-reliable job boards, cross-checks recent funding and company news (layoffs, hiring surges), dedupes against everything already sent, and grounds its "why this fits you" reasoning in specifics from the candidate's actual resume and proof-of-work — never generic phrasing.
- Sorts findings into direct-fit roles, an "Apply + pitch up" bucket (well-funded companies with an IC opening and no leader in the candidate's function yet), and cold-outreach targets.
- Emails the result as a personally-greeted, card-based HTML digest — sourced funding figures, market-signal chips, stage-grouped listings, all built to survive Gmail's mobile app (which strips `<style>` blocks — every style here is inlined for that reason).

## How to use it

1. **Install the skill.** Copy this folder into your Claude skills directory (in Claude Code or Cowork, that's typically `.claude/skills/job-leads-digest/`), or add the repo as a plugin if you're using a marketplace setup.
2. **Ask for it.** In a session with scheduled-task support, just say something like *"set up a job digest for me"* or *"email me new sales leadership roles weekly."* No slash command needed — the skill triggers on intent.
3. **Answer the interview.** Claude will ask, one or two at a time: your field/function, target titles, locations, funding-stage preference, your resume (attach it), your best proof-of-work link (portfolio, GitHub, LinkedIn — whatever fits your field), delivery email + schedule, and any companies you want on a standing watch list.
4. **Confirm the profile.** Claude shows back what it extracted before scheduling anything — this is your chance to correct anything before it becomes a recurring task.
5. **It's live.** A scheduled task now runs on your account. Every firing researches fresh, dedupes against what you've already seen, and emails you the result. Ask for it again later to adjust criteria, schedule, or watch list — no need to start over.

### What the email looks like

Every candidate gets a personally-greeted, card-based digest — grouped by seniority tier and funding stage, funding figures linked to their source, market-signal chips (layoffs, hiring surges) only when something real was found, and an "Apply + pitch up" section for well-funded companies with an opening but no leader in your function yet.

Below is a sample for a fictional product designer — illustrative data only, not a real run:

![Sample job leads digest email](assets/demo-email.png)

## Structure

```
job-leads-digest/
├── SKILL.md                       — setup + scheduled-run instructions
├── references/
│   ├── search-sources.md          — which job boards work, their quirks
│   └── email-design.md            — the email's visual/HTML spec
└── assets/
    ├── logo.png                   — header logo
    └── demo-email.png             — sample output shown above
```

---

Skill crafted by [Naseer Ahmed](https://www.linkedin.com/in/nastweets/) — [starkvibes.com](https://starkvibes.com/)
