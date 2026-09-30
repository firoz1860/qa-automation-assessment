# Walkthrough script (~5 min)

A shot-by-shot script for the required video. Record it yourself (screen + mic).
Everything below is real and already built/verified; the only thing the video adds is *you*
demonstrating it live in your n8n + Discord.

> Assumptions to state on camera: tested the maintained demo `https://demo.realworld.show`
> (the brief's `demo.realworld.io` is 404, verified live); Task 2 uses the public GitHub REST
> API (no token needed); Discord webhook lives only in n8n Credentials, never in the JSON;
> the n8n execution shown was performed by me (the operator).

---

## 0. Intro (~20s)
- "Hi, I'm Firoz. This is my Automation & QA take-home: a QA/debug report on the Conduit
  RealWorld demo, plus an n8n workflow that posts a GitHub 'morning brief' to Discord, and a
  bonus uptime monitor."

## 1. Reproduce one QA issue — weak sign-up validation (~60s)
- Open `https://demo.realworld.show/register`.
- Username `fzqatest2`, Email `notanemail` (no `@`), Password `1`.
- Click **Sign up** → account is created and you're logged in.
- Open DevTools → Network → the `POST /api/users` returns **201 Created**.
- Say: "Malformed email and a 1-char password are accepted on both client and server — this is
  finding #1 (High) in my report. Fix is defense-in-depth validation + 422 on the API."

## 2. Task 2 — the workflow canvas (~40s)
- Switch to n8n, open **Task2 GitHub Morning Brief**.
- Trace the path on screen: `Webhook → GitHub Search → Top 5 (Code) → README enrich →
  Build Digest → IF stars>1000 → Discord: Send Digest`, and the error branch
  `→ Build Error Alert → Discord: Send Failure Alert`.
- Point out the two Discord nodes use a **credential** (webhook), no URL in the workflow.

## 3. Task 2 — successful run (~70s)
- Click the **Webhook** node → **Listen for test event** → copy the **Test URL**.
- In a terminal: `curl "<TEST_URL>"` (or open in a browser).
- Canvas lights up green. Open **Build Digest** node output and show:
  - **top-5 transform**: freeCodeCamp, project-based-learning, react, vue, javascript-algorithms
    with star counts (re-verified live 2026-09-30);
  - **README enrichment**: `README.md (~6.4 KB)` on the #1 repo;
  - **IF branch**: `stars > 1000` is **true** → the 🔥 TRENDING label fires.
- Switch to Discord and show the **digest message actually delivered** in the channel.

## 4. Task 2 — error path (~40s)
- Edit **GitHub Search Repos** node URL host to `api.github.invalid`.
- Run again → NO digest; the red **🚨 FAILURE** alert is delivered to Discord.
- Say: "It never sends a silent empty success." Then **restore** the URL to `api.github.com`.

## 5. Bonus — uptime monitor (~30s)
- Open **Bonus Uptime Monitor**, click **Execute Workflow**: target `demo.realworld.show`
  returns 200 → the `OK - no alert` branch (no Discord message).
- (Optional) temporarily point it at a bad host, execute, show the 🔴 UPTIME ALERT in Discord,
  then restore. Note the 5-minute schedule is left **disabled**.

## 6. Close (~20s)
- "Secrets stay in n8n Credentials; the exported JSON has only a credential placeholder.
  Code and report are on my public GitHub repo. Thanks!"
