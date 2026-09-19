# Meridian Research Wiki Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the initial version of the Meridian research wiki — folder structure, schema doc, and a first ingest pass over `raw/client-brief.md` plus light web research — producing a prioritized interview-questions page for the Dana Okafor stakeholder interview.

**Architecture:** Three-layer LLM-wiki pattern: `raw/` (immutable sources, already governed by `DATA_HANDLING_CHECKLIST.md`), `wiki/` (LLM-authored markdown pages), and `CLAUDE.md` (schema/workflow doc at repo root). This is a content-authoring task, not code — "tests" below mean structural checks (required sections present, links resolve, no forbidden content), not unit tests.

**Tech Stack:** Plain markdown, relative links, no new tooling (no Obsidian, no custom commands).

**Spec:** `docs/superpowers/specs/2026-09-19-meridian-research-wiki-design.md`

## Global Constraints

- Wiki pages contain only aggregate/business-level facts — never raw loyalty, labor, or line-item POS data, per spec §"CLAUDE.md schema contents" and `DATA_HANDLING_CHECKLIST.md` §2–3. (Not a live risk yet — no data extract exists — but the rule must be written into `CLAUDE.md` so it holds once one arrives.)
- Links are plain relative markdown links, never `[[wikilink]]` syntax.
- Every content page: one-line purpose, `##`-headed sections, trailing **Open questions** subsection, trailing **Source(s)** line.
- No per-dataset or per-topic page splitting this pass — `data-landscape.md` and `market-research.md` stay single files with subsections.
- Research depth for `market-research.md`: a light pass (3–5 targeted searches) only. A deeper research pass is explicitly out of scope for this plan — Task 5 ends with a one-line flag in `log.md` naming it as future work, not a task here.

---

### Task 1: Scaffold wiki structure and write CLAUDE.md schema

**Files:**
- Create: `wiki/` (empty dirs `company/`, `data/`, `market/`, `stakeholders/` — git doesn't track empty dirs, so these get created implicitly by Task 2–6 writing into them; this task creates `wiki/` itself via the first file written into it)
- Create: `CLAUDE.md`

**Interfaces:**
- Produces: the folder layout and conventions every later task writes into. No later task can start until this one is committed.

- [ ] **Step 1: Write `CLAUDE.md`**

```markdown
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
```

- [ ] **Step 2: Verify the file exists and reads correctly**

Run: `cat CLAUDE.md | head -5`
Expected: shows the `# Meridian Capstone — Repo Conventions` heading.

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Add repo conventions and wiki schema doc"
```

**Done when:** `CLAUDE.md` exists at repo root with all four sections (Layout, Page conventions, Workflows, Data sensitivity rule).
**How to check:** Open `CLAUDE.md` and confirm all four `##`/`###`-level sections above are present; `git log --oneline -1` shows the commit.

---

### Task 2: Write company/meridian-markets.md

**Files:**
- Create: `wiki/company/meridian-markets.md`

**Interfaces:**
- Consumes: `raw/client-brief.md` (read-only source)
- Produces: business-facts page other pages and `interview-questions.md` (Task 6) link to and pull **Open questions** from.

- [ ] **Step 1: Write the page**

```markdown
# Meridian Markets

Specialty grocery chain, the client for this capstone engagement.

## Footprint & scale

- 14 stores across Los Angeles, Orange, and Ventura counties.
- Roughly $78M annual revenue.
- Roughly 620 employees.

## Growth history

- Grew from 6 to 14 stores over 5 years.
- Growth mode: taking over leases from national/regional chains that
  exited neighborhoods Meridian judged underserved — not greenfield site
  selection.
- Growth has been uneven: some stores succeeded quickly, others more
  slowly, with no stated pattern yet in the brief.

## Competitive positioning

- Competes against national chains on: prepared foods, local sourcing,
  and a smaller store footprint.

## Stated business goals

- Increase revenue.
- Reduce operating costs.
- Improve customer experience.
- Near-term decision: whether to open a next location in Pasadena (see
  [Dana Okafor](../stakeholders/dana-okafor.md) — presumed next step but
  not yet data-backed).

## Loyalty program

- Roughly 40,000 members.
- Purchase history collected but, per the brief, not meaningfully
  analyzed to date.

## Open questions

- What defines "underserved" in Meridian's historical site-selection
  logic — a documented criterion, or judgment call per lease opportunity?
- Is category-level sales performance tracked today, or is that itself
  new with this engagement?
- Is there a target date or specific dependency (e.g. a lease becoming
  available) driving the Pasadena decision timeline?

Source: raw/client-brief.md
```

- [ ] **Step 2: Verify required sections are present**

Run: `grep -c "^## " wiki/company/meridian-markets.md`
Expected: `6` (Footprint & scale, Growth history, Competitive positioning, Stated business goals, Loyalty program, Open questions).

- [ ] **Step 3: Commit**

```bash
git add wiki/company/meridian-markets.md
git commit -m "Add Meridian Markets company facts page"
```

**Done when:** the page exists with all six sections and a trailing Source line.
**How to check:** `grep -c "^## " wiki/company/meridian-markets.md` returns 6; `tail -1 wiki/company/meridian-markets.md` shows `Source: raw/client-brief.md`.

---

### Task 3: Write stakeholder pages

**Files:**
- Create: `wiki/stakeholders/dana-okafor.md`
- Create: `wiki/stakeholders/marcus.md`

**Interfaces:**
- Consumes: `raw/client-brief.md`
- Produces: two stakeholder pages `interview-questions.md` (Task 6) pulls from.

- [ ] **Step 1: Write `wiki/stakeholders/dana-okafor.md`**

```markdown
# Dana Okafor

VP of Operations, Meridian Markets — primary point of contact for this
engagement.

## Role & stated priorities

- Commissioned this analysis: wants a dashboard showing sales
  performance by store and category to support expansion decisions.
- Already leaning toward the Pasadena site as the next location, but
  wants data to justify it before committing.
- Named three success measures: increase revenue, reduce operating
  costs, improve customer experience.
- Wants to make better use of the ~40,000-member loyalty program, which
  she says has gone largely unused for analysis.

## Communication

- Email is the best channel.
- Travels Tuesdays and Wednesdays and is slow to reply — she's said not
  to read silence as a problem.
- Her assistant can schedule meetings but cannot answer analytics
  questions — route substantive questions to Dana directly.

## Context from the brief

- Wrote the brief "between flights" and flagged it might be rough —
  worth confirming details rather than assuming precision.
- Says growth "hasn't been even" — some stores took off immediately,
  others slower — without specifying which stores or why.
- States decisions have historically been made "on instinct and a
  spreadsheet" — implies limited existing analytics infrastructure.

## Open questions

- What specifically makes Pasadena "the obvious next step" — informal
  data behind that already, or pure instinct?
- Which stores "took off immediately" vs. struggled, and does she have a
  hypothesis why (location, timing, category mix, staffing)?
- Who besides Dana will actually use the dashboard — just her, or wider
  leadership/board?
- What would change her mind about Pasadena, if the data didn't support
  it?
- How is "operating cost" currently tracked, and by whom, given the
  spreadsheet-based history?

Source: raw/client-brief.md
```

- [ ] **Step 2: Write `wiki/stakeholders/marcus.md`**

```markdown
# Marcus

IT contact at Meridian Markets — named in the brief as the person to
reach once the NDA is signed, to pull the data extract.

## What's known

- Role: IT (title not given in the brief).
- Can produce a data extract covering POS transactions, loyalty program
  data, labor scheduling/hours, and store attributes.
- Extract is gated on the NDA being signed first.
- Meridian migrated to a new POS system last spring (2026); described as
  "an improvement" but with no detail on what changed.

## Open questions

- What format will the extract be in (flat files, database dump, API
  access)?
- Does the spring-2026 POS migration create a discontinuity in the
  three years of transaction history (different schema, category
  taxonomy, or store IDs before vs. after)?
- Is Marcus the right person to ask about data quality/known issues, or
  only extraction logistics?
- Full name and preferred contact method — brief gives first name only.

Source: raw/client-brief.md
```

- [ ] **Step 3: Verify both pages have required sections**

Run: `grep -l "^## Open questions" wiki/stakeholders/*.md | wc -l`
Expected: `2`

- [ ] **Step 4: Commit**

```bash
git add wiki/stakeholders/dana-okafor.md wiki/stakeholders/marcus.md
git commit -m "Add stakeholder pages for Dana Okafor and Marcus"
```

**Done when:** both stakeholder pages exist with Open questions and Source lines.
**How to check:** `grep -l "^## Open questions" wiki/stakeholders/*.md | wc -l` returns 2.

---

### Task 4: Write data/data-landscape.md

**Files:**
- Create: `wiki/data/data-landscape.md`

**Interfaces:**
- Consumes: `raw/client-brief.md`, `DATA_HANDLING_CHECKLIST.md`
- Produces: data-landscape page `interview-questions.md` (Task 6) pulls from.

- [ ] **Step 1: Write the page**

```markdown
# Data Landscape

What's known and unknown about each dataset Meridian has offered, and
how each may be used per [DATA_HANDLING_CHECKLIST.md](../../DATA_HANDLING_CHECKLIST.md).

## POS transactions

- Scope: roughly 3 years of point-of-sale transaction data.
- Meridian migrated to a new POS system last spring (2026); unclear
  whether the three years span both the old and new systems, and if so,
  whether schema/category definitions are consistent across the
  migration.
- AI-tool status: **forbidden raw** (line-item detail); **fine once
  aggregated** to sales totals by store and week, per the brief's own
  wording.

## Loyalty program

- Scope: roughly 40,000 members, membership + purchase history.
- Per the brief, largely unused for analysis so far.
- AI-tool status: **forbidden**, raw or aggregated — customer data, no
  exceptions per the NDA.

## Labor scheduling & hours

- Scope: scheduling and hours data; no further detail given in the
  brief.
- AI-tool status: **forbidden**, raw or aggregated — employee data, no
  exceptions per the NDA.

## Store attributes

- Scope: square footage, opening date, and lease terms, per store.
- AI-tool status: **fine**, raw or aggregated.

## Open questions

- Does the 3-year POS history predate the spring-2026 system migration,
  and if so, how much of it (if any) needs to be treated as a separate,
  possibly-incompatible period?
- What time granularity does labor data come in (per-shift, daily
  totals, payroll-period)?
- Is store square footage gross leasable area or selling floor area —
  matters for per-square-foot performance comparisons?
- What's the earliest reliable date for loyalty enrollment data — does
  the 3-year POS window fully overlap the loyalty data window, or is one
  shorter?

Source: raw/client-brief.md, DATA_HANDLING_CHECKLIST.md
```

- [ ] **Step 2: Verify all four datasets are covered**

Run: `grep -cE "^## (POS transactions|Loyalty program|Labor scheduling|Store attributes)" wiki/data/data-landscape.md`
Expected: `4`

- [ ] **Step 3: Commit**

```bash
git add wiki/data/data-landscape.md
git commit -m "Add data landscape page"
```

**Done when:** the page covers all four datasets with an AI-tool status line each, plus Open questions.
**How to check:** the grep in Step 2 returns 4; each dataset subsection contains the bolded text `AI-tool status:`.

---

### Task 5: Web research and market/market-research.md

**Files:**
- Create: `wiki/market/market-research.md`

**Interfaces:**
- Consumes: web search results (external, not a repo file)
- Produces: market-research page `interview-questions.md` (Task 6) pulls from.

- [ ] **Step 1: Run these searches** (light pass — 3 to 5 queries, per the approved research-depth decision)

1. `specialty grocery industry trends 2026`
2. `Los Angeles Orange Ventura county grocery market competition`
3. `national grocery chains closing stores neighborhoods California`
4. (optional, if 1–3 leave a gap) `specialty grocery prepared foods local sourcing strategy`

- [ ] **Step 2: Write the page**, using this template — fill each `##` section with 2-4 bullet points drawn from the search results, each bullet ending in a markdown link to its source:

```markdown
# Market Research

Outside research (not from the client brief) on the specialty grocery
industry and the LA/Orange/Ventura market, to ground interview questions
in market context Dana didn't hand us.

## Specialty grocery industry trends

- [finding 1, with source link]
- [finding 2, with source link]

## LA / Orange / Ventura competitive landscape

- [finding 1, with source link]
- [finding 2, with source link]

## Chains exiting neighborhoods (Meridian's growth mechanism)

- [finding 1, with source link]
- [finding 2, with source link]

## Open questions

- [gaps the research didn't resolve, framed as questions for Dana or
  future research]

Source: web research, links inline above
```

- [ ] **Step 3: Verify the page has real content, not template placeholders**

Run: `grep -c "finding [0-9], with source" wiki/market/market-research.md`
Expected: `0` (i.e. none of the literal template placeholders remain — all replaced with real bullets)

- [ ] **Step 4: Append a note to log.md flagging deeper research as future work**

This gets added in Task 7's `log.md` entry (see Task 7, Step 2) — no separate file write here.

- [ ] **Step 5: Commit**

```bash
git add wiki/market/market-research.md
git commit -m "Add market research page from light web-research pass"
```

**Done when:** the page has three findings sections plus Open questions, each bullet cited with a link, no template placeholder text remains.
**How to check:** the grep in Step 3 returns 0; manually skim that every bullet ends in `](http`.

---

### Task 6: Write interview-questions.md

**Files:**
- Create: `wiki/interview-questions.md`

**Interfaces:**
- Consumes: the **Open questions** sections of every page from Tasks 2–5.
- Produces: the primary interview-prep deliverable.

- [ ] **Step 1: Write the page**, grouping the Open Questions already drafted in Tasks 2–4 verbatim by topic, then adding a fourth group pulled from Task 5's actual Open questions section once it exists:

```markdown
# Interview Questions — Dana Okafor

Prioritized questions for the stakeholder interview, synthesized from
open questions across the wiki. Each question links back to the page it
came from.

## Business & strategy

1. What specifically makes Pasadena "the obvious next step" — informal
   data already, or instinct? ([company](company/meridian-markets.md), [Dana](stakeholders/dana-okafor.md))
2. Which stores "took off immediately" vs. struggled, and does she have
   a hypothesis why? ([Dana](stakeholders/dana-okafor.md))
3. What defines "underserved" in Meridian's site-selection history — a
   documented criterion or judgment call? ([company](company/meridian-markets.md))
4. Is there a target date or dependency driving the Pasadena timeline
   specifically? ([company](company/meridian-markets.md))
5. What would change her mind about Pasadena, if the data didn't support
   it? ([Dana](stakeholders/dana-okafor.md))

## Data

6. Does the 3-year POS history predate the spring-2026 system migration —
   and does that create a discontinuity? ([data](data/data-landscape.md), [Marcus](stakeholders/marcus.md))
7. What format will the data extract be in? ([Marcus](stakeholders/marcus.md))
8. What time granularity does labor data come in? ([data](data/data-landscape.md))
9. Is store square footage gross leasable or selling floor area?
   ([data](data/data-landscape.md))
10. Does the loyalty program's data window fully overlap the 3-year POS
    window? ([data](data/data-landscape.md))
11. Is category-level sales performance tracked today, or is that new
    with this engagement? ([company](company/meridian-markets.md))

## Stakeholders & process

12. Who besides Dana will use the dashboard? ([Dana](stakeholders/dana-okafor.md))
13. Is Marcus the right contact for data-quality questions, or only
    extraction logistics? ([Marcus](stakeholders/marcus.md))
14. How is "operating cost" currently tracked, given the
    spreadsheet-based history? ([Dana](stakeholders/dana-okafor.md))

## Market

<!-- Fill during execution: pull the Open questions bullets written in
Task 5's wiki/market/market-research.md verbatim, numbered continuing
from 14, each linking back to market/market-research.md. -->
```

- [ ] **Step 2: Replace the market section placeholder** with the actual Open Questions from `wiki/market/market-research.md` (written in Task 5), continuing the numbering, each linking to `market/market-research.md`. Remove the HTML comment once filled.

- [ ] **Step 3: Verify no placeholder comment remains and all four groups are populated**

Run: `grep -c "<!-- Fill during execution" wiki/interview-questions.md`
Expected: `0`

- [ ] **Step 4: Commit**

```bash
git add wiki/interview-questions.md
git commit -m "Add synthesized interview questions page"
```

**Done when:** all four topic groups (Business & strategy, Data, Stakeholders & process, Market) have numbered questions, each linking to its source page, no placeholder comment remains.
**How to check:** the grep in Step 3 returns 0; `grep -c "^[0-9]*\. " wiki/interview-questions.md` returns at least 14 (11 from Tasks 2-4 plus however many Task 5 added).

---

### Task 7: Write index.md, log.md, and final verification

**Files:**
- Create: `wiki/index.md`
- Create: `wiki/log.md`

**Interfaces:**
- Consumes: the file list of everything created in Tasks 1–6.
- Produces: nothing further consumes these — this is the terminal task.

- [ ] **Step 1: Write `wiki/index.md`**

```markdown
# Wiki Index

Catalog of every page in this wiki, by category.

## Synthesis

- [Interview Questions](interview-questions.md) — prioritized questions for the Dana Okafor stakeholder interview

## Company

- [Meridian Markets](company/meridian-markets.md) — business facts, footprint, growth history, stated goals

## Data

- [Data Landscape](data/data-landscape.md) — per-dataset scope, gaps, and AI-tool status

## Market

- [Market Research](market/market-research.md) — outside research on the specialty grocery industry and LA/OC/Ventura market

## Stakeholders

- [Dana Okafor](stakeholders/dana-okafor.md) — VP Operations, primary contact
- [Marcus](stakeholders/marcus.md) — IT, data extract contact
```

- [ ] **Step 2: Write `wiki/log.md`**

```markdown
# Ingest / Query / Lint Log

Append-only. One entry per ingest, query, or lint action.

## 2026-09-19 — Initial ingest

- Source: `raw/client-brief.md` + light web research (3-5 searches on
  specialty grocery trends and the LA/Orange/Ventura market)
- Pages created: `company/meridian-markets.md`, `data/data-landscape.md`,
  `market/market-research.md`, `stakeholders/dana-okafor.md`,
  `stakeholders/marcus.md`, `interview-questions.md`, `index.md`
- Key takeaway: biggest gaps are around the Pasadena decision's actual
  evidence base, the POS migration's effect on historical data
  continuity, and market context Dana didn't supply — all folded into
  `interview-questions.md`.
- Flagged for future work: this pass's market research was a light,
  3-5-search pass by design (see plan
  `docs/superpowers/plans/2026-09-19-meridian-research-wiki.md`). A
  deeper research pass (dedicated research agent, competitor profiles,
  more sources) is future work, not done here.
```

- [ ] **Step 3: Run full verification checklist from the spec**

Run each check and confirm:

```bash
# Every content page has Open questions and Source
grep -L "^## Open questions" wiki/company/*.md wiki/data/*.md wiki/market/*.md wiki/stakeholders/*.md
# Expected: empty output (no files missing the section)

grep -L "^Source" wiki/company/*.md wiki/data/*.md wiki/market/*.md wiki/stakeholders/*.md
# Expected: empty output

# index.md lists every page that exists
find wiki -name "*.md" -not -name "index.md" -not -name "log.md" -not -name "interview-questions.md" | wc -l
# Expected: 5 (company, data, market, 2 stakeholders)
grep -c "\.md)" wiki/index.md
# Expected: 7 (5 content pages + interview-questions.md self-reference not counted... verify manually: index.md should link to all 6 non-index/log pages)

# No accidental data files
git status --porcelain
# Expected: only the files this plan created, nothing under raw/ with a data extension
```

- [ ] **Step 4: Commit**

```bash
git add wiki/index.md wiki/log.md
git commit -m "Add wiki index and initial ingest log entry"
```

**Done when:** `wiki/index.md` links to all 6 content/synthesis pages, `wiki/log.md` has one dated entry describing this ingest, and every verification check in Step 3 passes.
**How to check:** run the Step 3 commands yourself and confirm the expected output for each; open `wiki/index.md` and click through each link to confirm it resolves.

---

## Self-Review Notes

- **Spec coverage:** every page in the spec's "Page specifications" section has a task (Tasks 2-7). The `CLAUDE.md` schema contents from the spec are fully reproduced in Task 1. The data-sensitivity rule appears in both `CLAUDE.md` (Task 1) and is cited in `data-landscape.md` (Task 4) — no gap.
- **Placeholder scan:** Task 5 and Task 6 contain template markers (`[finding N, with source]`, the HTML comment) by necessity, since their real content depends on live web-research results not available at plan-writing time — each is paired with an explicit verification step (grep for zero remaining placeholders) so the task cannot be marked done with a placeholder left in place. This is the plan's one deliberate deviation from "no placeholders," scoped narrowly to content that depends on runtime research.
- **Type/reference consistency:** all relative links between pages (e.g. `../stakeholders/dana-okafor.md` from `data-landscape.md`) checked against the actual folder structure in Task 1.
