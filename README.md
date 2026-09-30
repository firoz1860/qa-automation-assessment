# Automation & QA Developer — Take-Home Submission

**Candidate:** Firoz Ahmad · **Date:** 2026-09-29

Two deliverables from the assessment, plus the optional bonus:

| Task | What it is | Status |
|------|-----------|--------|
| **Task 1** | Live QA & debug report of a "vibe-coded" web app | ✅ Complete — 6 evidenced issues + root-cause analysis |
| **Task 2** | n8n workflow: GitHub digest → enrich → branch → Discord | ✅ Built · ✅ **imported, executed & Discord delivery verified in n8n** (exec `#5` digest + `#6` failure alert, both `{"success":true}`) · ⏳ screenshots are yours |
| **Bonus** | n8n uptime monitor (5-min ping → Discord alert) | ✅ Built · ✅ **imported & executed** (no-alert run `#1`, alert run `#7` Discord `{"success":true}`) · ⏳ screenshot is yours |

> **n8n execution evidence (2026-09-30) — Discord delivery CONFIRMED:** both workflows were imported
> into the connected n8n account and run via the authenticated n8n MCP. After a **Discord Webhook**
> credential was attached to the Discord nodes, Task 2 (`S4ZDlYmHw7tGrion`) delivered a digest
> (exec `#5`, `Discord: Send Digest` → `{"success":true}`) and a failure alert (exec `#6`,
> `Discord: Send Failure Alert` → `{"success":true}`); the Bonus (`OVvevoSqyVotFgSs`) delivered an
> uptime alert on a temporary down target (exec `#7`, `Discord: Uptime Alert` → `{"success":true}`),
> and the healthy path returns 200 → "OK, no alert" (exec `#1`). Temporary test hosts were restored;
> the 5-minute schedule is left **disabled** (workflows not activated). No webhook URL/secret is in
> this repo. Full detail in `Task2_README.md`.

## Repository contents

```
Task1_QA_Report_Firoz_Ahmad.pdf     ← primary Task 1 deliverable (with embedded screenshots)
Task1_QA_Report_Firoz_Ahmad.md      ← same report, GitHub-readable
Task1_QA_Report_Firoz_Ahmad.html    ← source used to render the PDF
Task2_README.md                     ← Task 2 write-up (APIs, transform, threshold, errors, run steps)
workflows/
  Task2_Workflow_Firoz_Ahmad.json   ← importable n8n workflow (no secrets)
  Bonus_UptimeMonitor_Firoz_Ahmad.json
screenshots/                        ← real screenshots captured during Task 1 testing
MANUAL_STEPS.md                     ← the exact steps only you can do (n8n + Discord + screenshots + Loom)
WALKTHROUGH_SCRIPT.md               ← shot-by-shot script for the required walkthrough video
```

## Task 1 — QA report (summary)

Tested the maintained RealWorld demo **`https://demo.realworld.show`** (the assignment's
`demo.realworld.io` is **404**, verified live — not reported as an app bug). Six distinct, reproducible
issues, each with captured evidence:

1. **High** — Sign-up accepts a malformed email (`notanemail`) and a 1-char password (`POST /api/users` → `201`).
2. **Medium** — Every route shares the static page title "Conduit" (WCAG 2.4.2 + SEO).
3. **Medium** — Form fields have no real `<label>` (placeholder-only), email is `type="text"`, no `autocomplete`.
4. **Medium** — Edit-article form never loads the article's existing tags.
5. **Medium** — "Delete Article" is immediate & irreversible with no confirmation (`DELETE` → `204`).
6. **Low** — Article body renders raw HTML passthrough (active script XSS is *mitigated*; `<script>`/`onerror` stripped).

Correct behaviors (empty-form validation, failed-login message, route guard) are listed in the report so
nothing is invented. One surprising observation (an unexpected username) was investigated and traced to
the **shared demo backend**, not the app — documented honestly, not counted as a bug. Full detail,
plain-English impact, evidence, and the root-cause analysis are in **`Task1_QA_Report_Firoz_Ahmad.pdf`**.

## Task 2 — n8n workflow (summary)

`Webhook (GET) → GitHub search repos → Code (top 5 by stars) → GitHub README enrich → IF (stars > 1000)
→ Discord digest`, with a real error path (search failure / malformed payload → Discord failure alert)
that never sends a silent empty success. **Secrets live only in n8n Credentials** — the JSON has a
placeholder credential reference, no webhook URL. I verified the endpoints and both Code transforms
against the **live GitHub API** (search `200`, top-5 correct, README `README.md` ~6.4 KB, threshold
`true`). See **`Task2_README.md`** for the verified digest preview and import/run steps.

## Security

No secrets are committed. Discord webhook URLs, tokens and passwords are kept out of the JSON, report,
screenshots and git history. See `.gitignore`. Testing used a disposable account with no sensitive data.

## What I need you to do

See **`MANUAL_STEPS.md`** — importing the workflows into your n8n, creating/selecting the Discord
Webhook credential, running them, capturing the two Task 2 screenshots, and recording the Loom video.
These are the only steps I couldn't do from here, exactly as flagged.
