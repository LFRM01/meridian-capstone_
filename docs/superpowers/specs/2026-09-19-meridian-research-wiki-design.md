# Meridian Research Wiki — Design Spec

Date: 2026-09-19
Status: Implemented. See `docs/superpowers/plans/2026-09-19-meridian-research-wiki.md` for the implementation plan and completion status.

## Purpose

A general capstone knowledge base for the Meridian Markets engagement,
following the "LLM wiki" pattern (persistent, compounding, LLM-maintained
markdown knowledge base, as opposed to re-synthesizing from scratch each
session). The immediate driving use case is preparing sharp, well-grounded
questions for the upcoming stakeholder interview with Dana Okafor; the wiki
is built to keep growing as a general research base across the full 8-week
engagement.

## Scope

In scope for this spec: the wiki's folder structure, page conventions and
templates, the `CLAUDE.md` schema/workflow doc, and one initial ingest pass
(client-brief.md + targeted web research) producing every page listed
below, including the `interview-questions.md` synthesis.

Out of scope is listed in full further down, but the short version: no
Obsidian setup, no per-dataset page splitting yet, no formal `/ingest`
command, and no ingestion of the real (not-yet-received) data extract.

## Architecture

Three layers, per the LLM wiki pattern:

- **Raw sources** (`raw/`) — immutable inputs: `client-brief.md` today,
  later the data extract, NDA, workshop materials, etc. Already governed by
  `DATA_HANDLING_CHECKLIST.md`.
- **The wiki** (`wiki/`) — LLM-generated/maintained markdown pages: entity
  pages, a business-facts page, a data-landscape page, a market-research
  page, and a synthesis page (`interview-questions.md`).
- **The schema** (`CLAUDE.md`) — a repo-root config/instructions document
  defining wiki conventions and the ingest/query/lint workflow, so any
  future session (human or agent) follows the same pattern without
  re-deriving it.

## Folder structure

```
wiki/
  index.md
  log.md
  interview-questions.md
  company/
    meridian-markets.md
  data/
    data-landscape.md
  market/
    market-research.md
  stakeholders/
    dana-okafor.md
    marcus.md
CLAUDE.md
```

Rationale for page granularity: one page per dataset or per market-research
topic would be premature fragmentation before any of those topics have
enough content to need splitting. `data-landscape.md` and
`market-research.md` start as single pages with clear subsections (one per
dataset / one per research topic) and split into separate files only if a
subsection grows large enough to warrant it — a call made at future ingest
or lint time, not now.

## Page conventions

- Plain markdown, relative links (e.g. `[Dana Okafor](../stakeholders/dana-okafor.md)`) —
  no `[[wikilink]]` syntax, since the wiki is browsed in the IDE/GitHub, not
  Obsidian.
- Every page opens with a one-sentence purpose line, then content organized
  under `##` headings.
- Every page ends with an **Open questions** subsection — the specific gaps
  this page couldn't resolve from available sources. This is what
  `interview-questions.md` draws from.
- Every page states its sources inline or in a trailing **Sources** line
  (e.g. "Source: client-brief.md" or "Source: web research, see links
  below") so provenance is traceable.

## Page specifications

**`index.md`** — catalog of every wiki page: path, one-line summary,
category. Updated on every ingest.

**`log.md`** — append-only. One entry per ingest/query/lint action: date,
what was processed, which pages were touched/created, key takeaways. First
entry will be this session's initial ingest.

**`interview-questions.md`** — the synthesis deliverable. Pulls the **Open
questions** from every other page into one prioritized list, grouped by
topic (business/strategy, data, stakeholders, market). This is the primary
artifact you bring to the interview.

**`company/meridian-markets.md`** — business facts from the brief: revenue
(~$78M/yr), footprint (14 stores, LA/Orange/Ventura counties), headcount
(~620), growth history (6→14 stores in 5 years via lease takeovers),
competitive positioning (prepared foods, local sourcing, smaller footprint
than national chains), stated goals (revenue, cost, customer experience).

**`data/data-landscape.md`** — one subsection per dataset (POS
transactions, loyalty program, labor scheduling, store attributes): what's
known about scope/format from the brief, what's unknown, and its AI-tool
status per `DATA_HANDLING_CHECKLIST.md` §2. Explicitly notes the POS system
migration mentioned in the brief as an open question (what changed, does it
affect historical comparability).

**`market/market-research.md`** — outside research (not from the brief):
specialty grocery industry trends, competitive dynamics in the LA/Orange/
Ventura area, context on chains pulling out of neighborhoods (referenced in
the brief as the opportunity Meridian exploited). Grounds interview
questions in market reality Dana didn't hand us.

**`stakeholders/dana-okafor.md`** — role (VP Operations), what she's
stated (goals, priorities, constraints, communication preferences —
email-first, travels Tue/Wed, board meets in 3 weeks), and open questions
specific to her.

**`stakeholders/marcus.md`** — role (IT, extract contact), what's known
(minimal — brief only says he can pull an extract once NDA is signed), open
questions.

## CLAUDE.md schema contents

- Short statement of the wiki's purpose and the three-layer architecture
- The folder layout above, with a one-line purpose per file/folder
- Linking and page-format conventions (as above)
- **Ingest workflow**: when a new source arrives, read it, identify which
  existing pages it touches or what new pages it needs, update `index.md`
  and `log.md`
- **Query workflow**: when asked a question, check the wiki first; answer
  with citations to specific pages; offer to file a valuable answer back
  into the wiki as a page update
- **Lint workflow**: periodically check for contradictions between pages,
  stale claims, orphaned pages, and missing **Open questions** that should
  exist
- **Data sensitivity rule** (hard constraint, not a suggestion): wiki pages
  may only ever contain aggregate/business-level facts. Raw loyalty
  records, labor schedules, or line-item POS data must never be
  summarized, excerpted, or pasted into any wiki page — this applies even
  after the real data extract arrives later in the engagement. This
  extends `DATA_HANDLING_CHECKLIST.md` §2–3 to the wiki as a destination,
  not just to AI-tool prompts.

## This session's execution

1. Create the folder structure and all files listed above.
2. Populate stakeholder pages, `company/meridian-markets.md`, and
   `data/data-landscape.md` from `raw/client-brief.md`.
3. Run targeted web research (specialty grocery trends; LA/Orange/Ventura
   competitive landscape) and populate `market/market-research.md`, with
   source links.
4. Synthesize `interview-questions.md` from the **Open questions** sections
   across all other pages.
5. Write `CLAUDE.md`.
6. Add the first entry to `log.md` describing this ingest.

## Out of scope (for this pass)

- Obsidian vault setup — plain markdown only, per the approved tooling
  choice.
- Per-dataset / per-topic page splitting — deferred until a page actually
  grows unwieldy.
- A formal `/ingest` or `/lint-wiki` command — plain documented workflow
  only, per the approved workflow-style choice.
- Ingesting the real data extract — hasn't arrived yet; when it does,
  intake still follows `DATA_HANDLING_CHECKLIST.md` §1, and only aggregate
  findings derived from it (never raw rows) become wiki content.

## Verification

No automated tests apply to a markdown knowledge base. Verification is:
- Every content page (`company/meridian-markets.md`, `data/data-landscape.md`,
  `market/market-research.md`, `stakeholders/*.md`) has a one-line purpose,
  an **Open questions** subsection, and a **Sources** line.
- `index.md` lists every page that exists in `wiki/`, with no orphans.
- `interview-questions.md` traces back to specific **Open questions**
  entries elsewhere in the wiki, not invented ex nihilo.
- `git status` after the build shows only the intended new files (no
  accidental data files, per `.gitignore`).
