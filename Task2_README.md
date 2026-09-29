# Task 2 — n8n GitHub "Morning Brief" Workflow

**File:** `workflows/Task2_Workflow_Firoz_Ahmad.json` · **Candidate:** Firoz Ahmad

A webhook-triggered workflow that pulls the top JavaScript repositories from GitHub, keeps the
top 5, enriches the #1 repo with its README, branches on a star threshold, and posts a formatted
digest to **Discord** — with a real error path that never sends a silent empty success.

---

## Flow

```
Webhook (GET /github-digest)
      │
      ▼
GitHub Search Repos (HTTP #1) ──(error output)──► Build Error Alert (Code) ─► Discord: Failure Alert
      │ (success)
      ▼
Top 5 Repos (Code) ───────────(error output)──────────────────────────────►  (same error path)
      │ (success)
      ▼
Enrich Top Repo README (HTTP #2)   ← on error: continue (regular output) → "README unavailable"
      │
      ▼
Build Digest (Code)
      │
      ▼
IF  top.stars > 1000
   ├─ true  ─► Label: Trending ─┐
   └─ false ─► Label: Normal   ─┴─►  Discord: Send Digest   (both paths converge on ONE send)
```

## APIs used (and why)

| # | Endpoint | Why |
|---|----------|-----|
| 1 | `GET https://api.github.com/search/repositories?q=topic:javascript&sort=stars&order=desc&per_page=10` | Public GitHub REST search — **no account/token needed** for basic use, exactly as the brief requested. Sorted by stars so "top" is meaningful. |
| 2 | `GET https://api.github.com/repos/{owner}/{repo}/readme` | Enriches the **#1** repo with real README metadata (file name + size). `owner`/`repo` are taken from the top item and URL-encoded. `Accept: application/vnd.github+json`. |

Both HTTP nodes send explicit `Accept: application/vnd.github+json` and `User-Agent: n8n-github-digest`
headers (GitHub rejects requests without a User-Agent).

## Transformation

`Top 5 Repos (Code)` validates that `items` is a non-empty array (otherwise it throws → error path),
keeps only useful fields (`full_name`, `owner`, `repo`, `url`, `stars`, `description`), **sorts by
stars descending, and slices the top 5**. It emits a single item `{ repos:[…5], top:{…} }` so the
README node runs exactly once (on the top repo).

`Build Digest (Code)` reads the top-5 (by node reference) plus the README node's output and formats a
Discord-ready message with clickable `[name](url)` links, star counts, and one enriched detail. If the
README call failed, it shows **"README unavailable"** and still ships the digest.

## Threshold / conditional branch

`IF top stars > 1000`. Both branches produce an **honest label** and converge on one Discord send:
- **true** → `🔥 TRENDING — the #1 JavaScript repo has more than 1,000 stars.`
- **false** → `📋 Daily digest — top JavaScript repo is at or below 1,000 stars.`

(In practice the top JS repo has hundreds of thousands of stars, so the **true** branch fires — see
the verified preview below. The false branch is wired and honest; to watch it fire in a demo, raise
the threshold number in the IF node temporarily.)

## Error handling (does NOT crash silently)

- **Search HTTP node**: `On Error → Continue (error output)` + **1 retry** (2 tries, 2 s apart) and a
  15 s timeout. Any non-2xx, timeout, or rate-limit routes to `Build Error Alert (Code)` →
  **Discord Failure Alert** with status + message + timestamp.
- **Top 5 Code node**: a malformed/empty payload (HTTP 200 but no `items`) **throws** and is routed
  to the same error path — so we never format an empty "success" digest.
- **README HTTP node**: `On Error → Continue (regular output)` — a missing README (404) degrades to
  "README unavailable" instead of killing the brief.

## Credentials (no secrets in the JSON)

The two Discord nodes use n8n's **Discord Webhook** credential (`discordWebhookApi`). The exported
JSON contains **no** webhook URL — only a placeholder credential reference
(`REPLACE_WITH_YOUR_CREDENTIAL`). You select your own credential after import.

## Import & run (you operate n8n)

1. **Create the credential:** in Discord, *Channel → Edit → Integrations → Webhooks → New Webhook*,
   copy the URL. In n8n: *Credentials → New → "Discord Webhook"* and paste the URL. **Do not** paste
   it into the workflow or share it.
2. **Import:** n8n → *Workflows → Import from File* → `Task2_Workflow_Firoz_Ahmad.json`.
3. **Select the credential** on both `Discord: Send Digest` and `Discord: Send Failure Alert`.
4. **Success run:** open the `Webhook` node, copy the **Test URL**, click **Listen for test event**,
   then in a browser or curl: `curl "<TEST_WEBHOOK_URL>"`. Watch the canvas execute and check Discord.
5. **Deliberate failure test:** temporarily change the Search node URL host to something invalid
   (e.g. `api.github.invalid`), run again → the **Failure Alert** should post to Discord. **Restore**
   the URL afterward.
6. Activate the workflow to use the production webhook URL, or add a **Schedule Trigger (every 1 h)**
   in parallel for the "morning brief" cadence.

## Observed execution result (honesty note)

- ✅ **Logic verified** by me against the **live GitHub API** (replicating the two Code nodes): search
  returned `200` with 10 items; top-5-by-stars and the README enrichment (`README.md`, ~6.4 KB) both
  succeeded; the `stars > 1000` branch evaluates **true**. Verified digest preview:

  > 🔥 **TRENDING** — the #1 JavaScript repo has more than 1,000 stars.
  > **Top 5 JavaScript repositories on GitHub**
  > 1. freeCodeCamp/freeCodeCamp — ⭐ 456,517
  > 2. practical-tutorials/project-based-learning — ⭐ 285,236
  > 3. react/react — ⭐ 250,826
  > 4. vuejs/vue — ⭐ 212,840
  > 5. trekhleb/javascript-algorithms — ⭐ 196,841
  > 🔎 Enriched top repo **freeCodeCamp/freeCodeCamp**: README `README.md` (~6.4 KB)

- ⏳ **Pending your run in n8n:** importing the JSON, selecting the Discord credential, and capturing
  the two required screenshots (canvas + successful execution). I could not execute inside your n8n
  account, so I have **not** claimed the Discord delivery succeeded — that step is yours.
