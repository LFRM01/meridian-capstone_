# Meridian Capstone — Repo Conventions

This repo holds a research wiki for the Meridian Markets capstone
engagement, built following the "LLM wiki" pattern: a persistent,
LLM-maintained markdown knowledge base that compounds across sessions
instead of being re-derived from scratch each time.

## Layout

- `raw/` — immutable source documents (client brief, later the data
  extract, NDA, workshop materials). Governed by `DATA_HANDLING_CHECKLIST.md`.
- `wiki/` — LLM-authored markdown pages. See structure below.
- `docs/superpowers/` — specs and implementation plans for wiki changes.

```
wiki/
  index.md                    — catalog of every page, one-liner each, by category
  log.md                      — append-only record of ingests/queries/lints
  interview-questions.md      — synthesized, prioritized interview questions
  company/meridian-markets.md — business facts
  data/data-landscape.md      — per-dataset scope, gaps, AI-tool status
  market/market-research.md   — outside research on the industry/market
  stakeholders/dana-okafor.md
  stakeholders/marcus.md
```

## Page conventions

- Plain markdown, relative links (e.g. `[Dana Okafor](../stakeholders/dana-okafor.md)`) — never `[[wikilink]]` syntax.
- Every content page opens with a one-sentence purpose line, uses `##` headings, ends with an **Open questions** subsection and a **Source(s)** line.
- "Content page" means anything under `company/`, `data/`, `market/`, or `stakeholders/`; `index.md`, `log.md`, and `interview-questions.md` are structural/synthesis pages and are exempt from the Open questions/Source requirement above.

## Workflows

**Ingest** (adding a new source): read the source, identify which existing
pages it touches or what new pages it needs, update those pages, then
update `index.md` (add/update entries) and append an entry to `log.md`
(date, source, pages touched, key takeaways).

**Query** (answering a question using the wiki): check `wiki/` first,
answer with citations to specific pages, and offer to file a valuable
answer back into the wiki as a page update.

**Lint** (periodic health check): scan for contradictions between pages,
stale claims, orphaned pages (not listed in `index.md`), and missing
**Open questions** sections that should exist given known gaps.

## Data sensitivity rule (hard constraint)

Wiki pages may only ever contain aggregate/business-level facts. Raw
loyalty records, labor schedules, or line-item POS data must never be
summarized, excerpted, or pasted into any wiki page — this applies even
after the real data extract arrives. This extends
`DATA_HANDLING_CHECKLIST.md` §2–3 (which governs AI-tool prompts) to the
wiki itself as a destination.
