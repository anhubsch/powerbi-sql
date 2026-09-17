# Power BI Labs — SQL Server Edition

Thirty hands-on Power BI labs across five industries, each series loading
data into SQL Server via SSMS first, then building the same star schema,
DAX, dashboard, and security work as the sibling Excel-sourced series.
Same five industries, same six-lab arc, different ingestion technology,
because that's the part that actually changes between a spreadsheet-driven
team and one with a real database behind it.

## How this relates to the Excel-sourced series

This is the SQL Server track of the same five scenarios covered in
[powerbi (Excel edition)](https://github.com/anhubsch/powerbi): coffee
shop, university, healthcare, libraries, retail. Lab 01 in every series
here loads the same underlying dataset into SQL Server through SSMS
instead of Excel and Power Query, using a staging-table-then-promote
pattern. From Lab 02 onward the two tracks converge: same star schema,
same DAX, same dashboard, same what-if and row-level security content,
because once the data is in a real model, the source technology stops
mattering. Only the "Get Data" step and a handful of phrasing references
differ.

## The industries

| Industry | Data | License | Real or synthetic |
|---|---|---|---|
| [Coffee shop](labs/coffee-shop/) | Maven Roasters coffee shop sales, 3 NYC stores | CC0 (Kaggle) | Real, date range extended synthetically for time-intelligence labs |
| [University](labs/university/) | Enrollment and library-usage records | — | Fully synthetic, generated in T-SQL in Lab 01, deliberately carries no PII |
| [Healthcare](labs/healthcare/) | Hospital appointment operations + NHS England A&E benchmark | OGL v3.0 (NHS data) | Synthetic appointments, real NHS trust-level benchmark data |
| [Libraries](labs/libraries/) | Seattle Public Library checkouts by title | Public / open (data.gov) | Real |
| [Retail](labs/retail/) | UCI Online Retail, UK e-commerce transactions | CC BY 4.0 | Real, with a small synthetic inventory table |

## The six labs, every time

| # | Lab | What it adds | What it breaks |
|---|---|---|---|
| 01 | SQL Server cleanup | Load into SQL Server via SSMS, assess, fix types, first query answering a real question | Ad hoc queries, no shared model or reusable calculation |
| 02 | Data model | Star schema in Power BI (Get Data → SQL Server), first DAX measures | No dashboard, no shared calculation |
| 03 | Dashboard | Interactive report, bookmarks, publish + scheduled refresh via gateway | Refresh depends on a gateway reaching the database |
| 04 | Time intelligence | Period-over-period DAX, a deliberate break-it exercise | No forward-looking view |
| 05 | What-if / RLS | Scenario modeling, row-level security | Access control is hand-maintained, not sourced from a real identity system |
| 06 | Capstone | Everything above, plus one new complication and one genuinely advanced technique | — |

Read a series in order. Each lab assumes the one before it. Lab 06 in
every series is harder than anything before it on purpose, and closes
with a reflection on the whole arc rather than a link to a next lab.

## Working through a lab

Every lab follows the same shape: Objectives, Background, Required
Resources, a Topology diagram, then numbered Parts with Steps. Steps that
could trip you up carry a collapsed **Hint**; every Part ends with a
collapsed **Expected result** so you can check your own work without an
instructor in the room. Both are closed by default on GitHub, click to
open.

Struggling on a step is normal and is most of the point. Open the hint
before you open the T-SQL or DAX reference docs in a new tab. If you're
still stuck after the hint, the **Troubleshooting** table at the end of
each lab covers the most common wrong turns, and the **What Went Wrong
When I Did This** section is an honest account of the mistakes made
building these labs in the first place.

## Setup

- **SQL Server** (Developer or Express edition, both free) and **SSMS**
  (SQL Server Management Studio), plus **Power BI Desktop**, already
  installed. These are three separate installs: SQL Server is the
  database engine (a background service with no window of its own), SSMS
  is the client you use to write and run queries against it, and Power BI
  Desktop is where the model, DAX, and reports get built. Installing SSMS
  alone gives you a client with nothing to connect to.
- A free Power BI Service account for the publish steps in Lab 03 onward.
  A Microsoft 365 email works, or a free Fabric trial.
- Internet access is needed only to download real source data: the
  coffee shop, healthcare (NHS benchmark), libraries, and retail series
  each link the exact file to fetch in their own Lab 01. The university
  series generates its data locally in T-SQL and needs no download at
  all.
- Two labs use Tabular Editor (free, external tool) for calculation
  groups: the healthcare and retail Lab 06 capstones. Everything else
  uses only SSMS and Power BI Desktop.
- Scheduled refresh from Lab 03 onward assumes SQL Server is reachable
  from wherever the Power BI gateway runs. If SQL Server is on the same
  machine as Power BI Desktop, the on-premises data gateway install
  covers this; a remote SQL Server instance needs its own network access
  sorted out first, outside the scope of these labs.

## Repository layout

```
README.md                 this file

labs/
  coffee-shop/             01-06, real Kaggle sales data
  university/               01-06, synthetic enrollment + library data
  healthcare/               01-06, synthetic appointments + real NHS benchmark
  libraries/                01-06, real Seattle Public Library data
  retail/                   01-06, real UCI Online Retail data
```

## What's deliberately out of scope

- No clinical or diagnosis data anywhere in the healthcare series. It's
  hospital operations only: appointments, wait times, department
  throughput. Stated explicitly in that series' own Lab 01 and restated
  in Lab 06.
- No real person's data anywhere. The university series is synthetic by
  design specifically to avoid enrollment-record privacy questions; the
  real datasets used elsewhere (coffee shop sales, library checkouts,
  online retail transactions) are published open data about
  transactions, not about identifiable individuals.
