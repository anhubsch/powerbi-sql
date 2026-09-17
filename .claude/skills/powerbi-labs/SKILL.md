---
name: powerbi-labs
description: Generate a hands-on Power BI training lab series (Excel or SQL Server as source, six labs per industry scenario, increasing difficulty, inline hints and answers) for the anhubsch-style GitHub repos. Use when asked to write, add, or expand Power BI/Excel/SQL Server training labs, a new industry scenario for a labs repo, or a capstone lab.
---

Generate one complete six-lab Power BI training series for a named industry
scenario, matching the structure and voice established across the
coffee-shop, university, healthcare, libraries, and retail series already
built (`anhubsch/labs` for the original Scottish waste-data series;
`anhubsch/powerbi` for the five-industry Excel-sourced expansion;
`anhubsch/powerbi-sql` for the SQL Server-sourced replica of the same five
industries). Each repo is a separate, complete set: same five industries,
same six-lab arc, different ingestion technology.

## Before writing anything

Confirm with the user, or infer from context if obvious:

- **The industry/scenario** and what real question it should answer (every
  series exists to answer one concrete business question across all six
  labs, stated in the top-level README).
- **The source technology for Lab 01: Excel or SQL Server.** This changes
  Lab 01's entire mechanics (see the two tracks below) but nothing about
  Labs 02-06's Power BI/DAX content, which is identical either way once
  the data lands in a star schema. Default to whichever track the target
  repo already uses; ask if starting a new repo from scratch.
- **Data source**: prefer real, freely-downloadable open data (government
  portals, Kaggle CC0/open-license datasets, UCI ML repository) over
  synthetic. Use synthetic only when real data would carry a privacy risk
  at the grain needed (e.g. student enrollment records, patient-level
  health data) — and say so explicitly in that series' Lab 01. If the
  scenario touches health, education, or any individually-identifiable
  data, ask the user how to scope it before writing (operations-only vs.
  synthetic vs. a different angle entirely) rather than guessing.
- **Reproducibility constraints**: assume Power BI Desktop is already
  installed, plus whichever source tool the track needs (Excel, or SQL
  Server + SSMS — see below). Internet access is for fetching data only.
  State any real download URL explicitly and concretely (exact file, exact
  filter) in that lab's Required Resources — never a vague "find a
  dataset."

## Two source tracks for Lab 01

**Excel track** (original): Lab 01 imports or generates data directly in
Excel, cleans it with Power Query, produces a first pivot. Required
Resources lists Excel 2021+ or Microsoft 365. No other install needed.

**SQL Server track**: Lab 01 loads the same underlying dataset into SQL
Server first, via SQL Server Management Studio (SSMS), then does the
initial assessment and cleanup in T-SQL rather than Excel/Power Query.
Concretely:

- Required Resources lists **SQL Server** (Developer or Express edition,
  free) and **SSMS**, not Excel. Note that SQL Server is the engine
  (a background service) and SSMS is the client used to write queries
  against it — these are two different downloads, and conflating them is
  a common first-timer confusion worth flagging explicitly in Lab 01 of
  a new SQL-track series (see the precedent in `anhubsch/labs`'
  `docs/00-setup.md`).
- Loading real downloaded data (CSV/Excel source files) into SQL Server
  uses `BULK INSERT` into a staging table, then validated/typed rows move
  into a real table, the same staging-then-promote pattern as the
  original waste-data repo's `sql/03-load-staging.sql` /
  `sql/04-load-facts.sql`. For synthetic data (university, and
  healthcare's synthetic appointment rows), generate rows directly in
  T-SQL (a numbers table / recursive CTE / `CROSS JOIN` pattern, or
  `INSERT` statements built from a small seed set) rather than generating
  in Excel and importing, since there's no reason to round-trip through
  Excel when the target is SQL Server from the start.
- Data-quality assessment (Part 1 of Lab 01) uses T-SQL queries
  (`COUNT`, `GROUP BY`, `HAVING COUNT(*) > 1` for duplicates, `WHERE`
  filters for out-of-range values) instead of Excel formulas/
  `Data Validation`. The first pivot-table question from the Excel track
  becomes a `GROUP BY` / aggregate query with the equivalent question.
  Keep the same planted data-quality problem (see the data-planting
  pattern below) — express it as something a T-SQL query surfaces, not
  something Excel's `COUNTIF` surfaces.
- From Lab 02 onward, both tracks converge: **Get Data → SQL Server**
  instead of **Get Data → Excel workbook** in Power BI, then the star
  schema, DAX, dashboard, time intelligence, what-if, RLS, and capstone
  content is written exactly the same way regardless of which track Lab
  01 used. Do not duplicate Labs 02-06's substance between tracks beyond
  this one Get Data source change and whatever a lab's own text
  references back to "the table/database from Lab 01."
- The topology diagram for a SQL-track Lab 01 replaces the Excel/Power
  Query boxes with SQL Server-shaped ones (a staging table, a load step,
  a validated target table); Lab 02's diagram replaces "Excel workbook"
  with "SQL Server database" as the source node.

## The six-lab structure

Every lab is one markdown file, named `NN-slug.md`, in
`labs/<scenario>/`. Six files per scenario: 01 through 05 follow this
progression, 06 is always the capstone.

| # | Lab | Introduces | Leaves broken (fixed next lab) |
|---|---|---|---|
| 01 | Source cleanup/generation | Load or generate data in Excel or SQL Server (see the two tracks above), assess quality, fix types, answer a first real question | Excel: formulas beside a pivot, doesn't scale. SQL Server: ad hoc queries, no shared model or reusable calculation |
| 02 | Data model | Star schema in Power BI, first DAX measures, fixes a data problem planted in Lab 01 | No dashboard, no reusable calculation |
| 03 | Dashboard | Interactive report, slicers, bookmarks, conditional formatting, publish + scheduled refresh | Refresh depends on one file/machine |
| 04 | Time intelligence | Period-over-period DAX (MoM/YoY/YTD or a domain-appropriate equivalent — see note below), a deliberate break-it-to-diagnose exercise | No forward-looking or scenario view |
| 05 | What-if / RLS | A what-if parameter modeling a real decision, row-level security scoped to a real role in that industry | Access control is hand-maintained; the what-if measure ignores second-order effects |
| 06 | Capstone | Integrates all five prior labs into one polished report, PLUS one new realistic complication (schema drift, a second data source, a late correction, a structural change) solved with ONE genuinely advanced technique (composite models, incremental refresh, calculation groups, parameterized queries, Type 2 SCD) | — (closes the series) |

**Time intelligence note (Lab 04):** don't default to calendar-date
`DATEADD`/`SAMEPERIODLASTYEAR` if the domain's real cadence isn't calendar
months (an academic term structure, a fiscal year, a sparse event
calendar). Pick what's actually correct for the domain and justify the
choice in the lab text — this has already been a deliberate design
decision worth defending once (the university series compares terms via
a TermSequence pattern rather than forcing a calendar date table where
none naturally fits).

**Data-planting pattern (Lab 01 → Lab 02):** every series plants one
realistic data-quality problem in Lab 01 that the reader notices but can't
cleanly fix with a pivot table (Excel track) or a single query (SQL
Server track), then fixes properly in Lab 02 once a real data model
exists. Make it specific to the real mechanics of that industry's data,
not a generic "there are duplicates" placeholder — examples already used:
a product ID that's only unique within one store location (not globally),
a department code renamed mid-year, the same book title spelled
inconsistently across export rows, a StockCode drifting across multiple
Description strings. When writing the SQL Server track for a scenario
that already has an Excel-track series, reuse the same planted problem
rather than inventing a new one, since it's a property of the dataset,
not of the tool used to load it.

## Required section structure (every lab, no exceptions)

```
# Lab NN: <Title>

## Objectives
- **Part 1:** ...
- **Part 2:** ...
(bulleted, one line per Part)

## Background / Scenario
Short, grounded in the previous lab's ending. States the real business
question this lab moves toward answering.

## Required Resources
Tools, files (with real download URLs and license where applicable), a
time estimate.

## Topology
A ```mermaid
flowchart LR
``` diagram showing the actual data flow for this specific lab, not a
generic template diagram.

## Part 1: <Name>
### Step 1: <Action>
...instructional text...

<details>
<summary>Hint</summary>

1-3 sentences nudging toward the fix or reasoning move required. Never
just restates the answer that's already given in the step — reserve hints
for steps that ask the reader to work something out themselves.

</details>

> **Bold callout.** One paragraph explaining WHY, not just what — the
> underlying mechanism, not a restatement of the click sequence.

<details>
<summary>Expected result, Part N</summary>

Concrete numbers or shape: row counts, measure output ranges, specific
visual behavior. As concrete as the data actually allows — never vague
("should look reasonable").

</details>

(repeat Part/Step pattern for the whole lab)

## Troubleshooting
| Symptom | Cause | Fix |

## Reflection
2-4 questions requiring the reader to reason about what they built, not
just recall a step.

## What Went Wrong When I Did This
2-3 SPECIFIC first-person mistakes tied to this lab's actual mechanics —
a concrete wrong turn and how it was caught. Never generic BI platitudes.

## Where This Breaks
Bullets pointing at the specific gap the next lab closes.

**Next:** [Lab NN — Title](NN-slug.md)
```

Lab 06 replaces "Where This Breaks" + "Next" with a closing reflection on
the whole six-lab arc, since it ends the series. No forward link.

## Difficulty ramp — make it real, not just topical

Labs 01 through 05 must read as a genuine increasing difficulty curve:

- **Lab 01**: fully explicit, step-by-step, little independent judgment.
- **Lab 02-03**: the reader combines 2+ concepts or applies a pattern
  stated earlier in the lab rather than being told every click. Move some
  specifics into a hint instead of the main text.
- **Lab 04-05**: real independent problem-solving. At least one DAX
  measure per lab should be requested by description ("write a measure
  that does X") with the working code in the collapsed hint, not given
  inline in the main body.
- **Lab 06**: hardest in the series. Genuinely requires synthesizing
  everything before it plus reasoning through a new technique the reader
  hasn't used yet in this series.

## Answers and hints — required, not optional

This repo has no instructor. Every lab needs self-service verification:
a `<details><summary>Hint</summary>` at each step where a reader could
plausibly get stuck or produce a wrong number, and a
`<details><summary>Expected result</summary>` at the end of every Part
giving a concrete, checkable number or behavior. Never spoil the "what
went wrong" reflection value — hints nudge toward the reasoning, they
don't hand over code that isn't already given inline in the lab body.

## Writing rules

Apply the `unslop` skill's rules throughout, with zero exceptions:
**no em dashes anywhere**, including in headings (`Lab 01: Title`, not
`Lab 01 — Title`) and label text (`Expected result, Part 1`, not
`Expected result — Part 1`). No inline-header bold-list filler, no
hedging, no unnecessary passive voice, no AI-sounding abstract phrasing
("delve into", "leverage", "robust", "seamless", "it's important to
note"). Match the established first-person, practical, honest-about-
mistakes voice from the existing series exactly — write like a real
practitioner's notes, not a course platform's generated content.

Every DAX block must be syntactically correct, real DAX. Every mermaid
diagram must be valid `flowchart LR` syntax and reflect that lab's actual
data flow. Never invent a real person, real institution, or real
organization name — use a clearly generic or fictional placeholder.

## After writing a new series

1. Do a realism pass: read the whole series end to end as a learner
   would, and fix anything that reads like generic textbook busywork
   rather than a plausible situation in that industry.
2. Grep the new files for the em dash character to confirm zero remain.
3. Update the repo's top-level `README.md` table of industries and the
   six-lab table if anything about the structure changed.
4. Flag explicitly if the scenario involves real people's data (health,
   education, any individually-identifiable records) so the user can
   confirm the privacy approach before anything is pushed.
5. If this is a SQL Server-track replica of an existing Excel-track
   series, confirm Labs 02-06's substance actually matches the original
   beyond the intended Lab 01/Get Data differences — a divergence there
   is a mistake, not a deliberate variation, unless the user asked for one.
