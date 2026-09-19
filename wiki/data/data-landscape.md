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
