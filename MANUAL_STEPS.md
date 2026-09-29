# Manual steps — the parts only you can do

I built and verified everything I could from here. These steps require **your** n8n account, **your**
Discord webhook, and **you** on camera. Nowhere should a webhook URL/token be pasted into the workflow
JSON, this repo, chat, screenshots, or the video.

---

## A. Task 2 workflow — REQUIRED (~10 min)

1. **Create a Discord webhook:** in your Discord server, pick a channel → *Edit Channel → Integrations →
   Webhooks → New Webhook → Copy Webhook URL*.
2. **Store it in n8n Credentials (not the workflow):** n8n → *Credentials → New → search "Discord Webhook"*
   → paste the URL → save as e.g. "My Discord Digest".
3. **Import the workflow:** n8n → *Workflows → ⋮ → Import from File* → `workflows/Task2_Workflow_Firoz_Ahmad.json`.
4. **Select your credential** on both **Discord: Send Digest** and **Discord: Send Failure Alert** nodes
   (they show a placeholder until you pick yours).
5. **Success run:** click the **Webhook** node → **Listen for test event** → copy the **Test URL** →
   in a terminal run `curl "<TEST_URL>"` (or open it in a browser). The canvas should light up green and
   a digest should appear in your Discord channel.
   - 📸 **Screenshot 1 (canvas):** the whole workflow after a successful run → save as
     `screenshots/task2-canvas.png`.
   - 📸 **Screenshot 2 (execution output):** click the **Build Digest** or **Discord: Send Digest** node
     and show its output data / the Discord message → save as `screenshots/task2-success.png`.
6. **Deliberate failure test:** edit the **GitHub Search Repos** node URL host to `api.github.invalid`
   → run again → you should get the **red failure alert** in Discord and NO digest. **Restore the URL.**
   (Optional 📸 `screenshots/task2-error.png`.)

> If your n8n's Discord node looks slightly different, the only field to set is the message/content
> (already wired to `{{ $json.content }}`) and the credential. Everything else imports as-is.

## B. Bonus uptime monitor — OPTIONAL (~5 min)

1. Import `workflows/Bonus_UptimeMonitor_Firoz_Ahmad.json`, select your Discord credential on
   **Discord: Uptime Alert**.
2. Click **Execute Workflow** once to run it manually (target is `https://demo.realworld.show`, expects
   200 → no alert). 📸 save the canvas as `screenshots/bonus-canvas.png`.
3. To see an alert, temporarily set the HTTP node URL to a bad host, execute, confirm the Discord alert,
   then **restore**. Don't leave the 5-minute schedule active against the public demo indefinitely.

## C. Loom video — REQUIRED by the assignment (~5 min)

Record a short walkthrough showing:
- **one reproduced QA issue** (e.g. sign up with `notanemail` / password `1` → account created), and
- the **Task 2** run: top-5 transform, README enrichment, the IF branch, the successful Discord digest,
  and the error path.
Mention assumptions and that the n8n execution was done by you. Paste the Loom link into the top-level
README (replace the placeholder) or send it with the submission.

## D. Publish to GitHub (I prepared the commit)

I initialized a git repo and made the first commit locally. To publish publicly, either:
- run `gh repo create qa-automation-assessment --public --source . --push` (if your `gh` is logged in), or
- create an empty public repo on github.com and `git remote add origin <url> && git push -u origin main`.

Then confirm the repo is **Public** and that `Task1_QA_Report_Firoz_Ahmad.pdf`, both workflow JSONs, and
your screenshots are visible.

## E. Final submission checklist (from the assignment)

- [ ] Public GitHub link
- [ ] `Task1_QA_Report_Firoz_Ahmad.pdf` (done)
- [ ] `workflows/Task2_Workflow_Firoz_Ahmad.json` (done) + 2 screenshots (yours: A5)
- [ ] `Task2_README.md` (done)
- [ ] `Bonus_UptimeMonitor_Firoz_Ahmad.json` (done) + 1 screenshot (yours: B2)
- [ ] Loom video link (yours: C)
