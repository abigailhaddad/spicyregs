# 🌶️ Spicy Regs — Explore Public Comments in Colab

A single Google Colab notebook that lets anyone browse, count, and full-text-search
**24M+ federal public comments** live from the [Spicy Regs](https://github.com/civictechdc/spicy-regs)
public data lake — no API key, no login, no multi-GB downloads. Queries stream straight
from Cloudflare R2 (`https://r2.spicy-regs.dev`) with DuckDB.

These files hold the comments regulations.gov **posts publicly**. Counts can lag the raw
*received* total, since comments are posted on a delay.

## Open it

**Easiest:** go to [colab.research.google.com](https://colab.research.google.com) →
**File → Upload notebook** → pick `spicy_regs_explore_comments.ipynb`.

(Once this repo is on GitHub you can also add an *Open in Colab* badge pointing at the file.)

## What's in the notebook

| Section | Does |
|---|---|
| ⚙️ Setup | Installs DuckDB, connects to the R2 lake |
| 🔎 1. Find a docket | Keyword search over docket titles + abstracts |
| 📊 2. Count comments | Fast per-docket / per-month counts from the index file |
| 🗂️ 3. Browse & filter | Full-text search comment bodies + filter by org / date |
| 📈 4. Chart by docket | Comments across a set of dockets (defaults to the four 2 CFR dockets) |
| 🏢 5. Top organizations | Which orgs filed the most comments on a docket |
| 🔁 6. Duplicate comments | Group identical bodies; see how repeated a docket is |
| 🔥 7. Busiest dockets | Most-commented dockets of a year, all agencies |
| ⚖️ 8. Compare dockets | Bar-chart posted counts for any list of docket IDs |

Each section is a Colab **form** — edit the fields on the right and re-run the cell.

| Docket (year) | Posted comments |
|---|---|
| OMB-2013-0001 (2013) | 285 |
| OMB-2019-0005 (2019) | 215 |
| OMB-2023-0004 (2023) | 1,957 |
| OMB-2026-0034 (2026) | ~54,520 |

## How it works

Comments are hive-partitioned on R2 at
`comments/agency_code={A}/docket_id={D}/year={Y}/month={M}/part-0.parquet`.
The tiny `comments_index.parquet` says which partitions exist and their row counts, so the
notebook queries the index first, then reads only the partition files it needs
(`union_by_name=true` reconciles older partitions that have fewer columns).
