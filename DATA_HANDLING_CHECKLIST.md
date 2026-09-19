# Data Handling Checklist — Meridian Markets Capstone

Internal reference for handling Meridian Markets data per the terms in
[`raw/client-brief.md`](raw/client-brief.md) and the NDA. Solo project;
review this before touching the real data extract.

## 1. Intake setup

- [ ] NDA signed before requesting anything from Marcus (IT)
- [ ] `.gitignore` confirmed in place (excludes data files under `raw/`) *before* copying any extract into the repo folder
- [ ] Extract copied into `raw/` on local machine only — no cloud sync, no personal Drive/Dropbox
- [ ] After copying, run `git status` and confirm no data files show up as trackable

## 2. Data classification — what can touch an AI tool

| Dataset | Raw / row-level | Aggregated |
|---|---|---|
| Loyalty program (membership + purchase history, ~40K members) | **Forbidden** — no exceptions, no excerpts | **Forbidden** — this is customer data regardless of aggregation |
| Labor scheduling & hours | **Forbidden** — no exceptions, no excerpts | **Forbidden** — this is employee data regardless of aggregation |
| POS transactions (line-item, ~3 years) | **Forbidden** | **Fine**, once rolled up to sales totals by store and week (the brief's own line) |
| Store attributes (sqft, opening date, lease terms) | **Fine** | **Fine** |

- [ ] Before pasting or uploading anything to Claude/ChatGPT/Copilot/etc., check it against this table
- [ ] When in doubt, treat it as forbidden and aggregate further

## 3. AI-tool usage rule

- [ ] AI tools (incl. Claude Code) may run scripts **locally** against raw files — the code executes on my machine, no raw data leaves it
- [ ] Never paste raw rows, excerpts, or screenshots of loyalty/labor data into a chat, prompt, or a file an AI tool reads directly
- [ ] Only files under `processed/` (aggregated outputs) are ever referenced in an AI chat or session

## 4. Aggregation workflow

- [ ] Aggregation done via pandas/SQL scripts, not manual copy-paste
- [ ] Scripts read from `raw/`, write outputs to `processed/` (kept separate so it's obvious what's safe)
- [ ] Before treating an aggregate as safe, check for small-n cells (e.g. a store/category/week combo with only 1–2 transactions) that could effectively re-identify a customer or employee
- [ ] Loyalty and labor data are never aggregated "up" into an AI-safe form — they stay off-limits entirely per §2

## 5. Sharing & output review

- [ ] Before anything leaves the machine (board prelim, dashboard, drafts, emails to Dana) — scan for raw/individual-level rows, screenshots of raw data, or AI-chat excerpts that quote raw rows
- [ ] Applies to the 3-week preliminary board update, not just the final deliverable
- [ ] Charts/dashboards built from `processed/` outputs only

## 6. End-of-engagement disposal

- [ ] Check the signed NDA text for retention/disposal terms once available (not specified in the brief itself)
- [ ] At project close, confirm what happens to `raw/` extract and any local copies — delete or retain per NDA
- [ ] Confirm no data files were ever accidentally committed (`git log --all --full-history -- raw/*.csv` or similar, adjusted to the actual extensions used)
