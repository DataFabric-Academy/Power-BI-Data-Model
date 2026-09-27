# AGENTS.md — Power-BI-Data-Model

## Purpose

Thai-language courseware (markdown only) for a Power BI **Semantic Model** training course by DataFabric-Academy / 9Expert. There is **no application code, build, lint, or test** — the deliverable is student-facing markdown. Remote: `https://github.com/DataFabric-Academy/Power-BI-Data-Model.git`.

## Structure

- `README.md` (root) — course map for 13 modules; course emphasis: Semantic Model + Relationships, DAX only when relationship-related, VertiPaq / Low Cardinality / Sorted Data.
- `01-Introduction/` … `13-Incremental-Refresh-Partitioning/` — one folder per module. Each has `README.md` (theory), `EXERCISES.md` (step-by-step labs), `CODE-EXAMPLES.md` (DAX / Power Query M). Modules **12 and 13 are thin stubs** (not yet fleshed out).
- `GITHUB-SETUP.md` — how this repo is published.
- `Trainer_Docs/` — **git-ignored instructor material** (152-slide PPTX "Version 6" + `9Expert-Case Study-Data Model for Power BI-Version 4/` with the actual sample `.pbix` files + one `.pqt`). Never commit it, never count it as a repo deliverable.

## Data source (course-wide convention)

**AdventureWorksDW2025** (schema identical to older AdventureWorksDW):

- Server `ake.database.windows.net`, Database `AdventureWorksDW2025`, Login `dwuser`
- Password must stay a placeholder: `<password ที่ได้รับจากผู้สอน>` — never commit real credentials
- Official download: [AdventureWorksDW2025.bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2025.bak) / [Microsoft Learn install guide](https://learn.microsoft.com/sql/samples/adventureworks-install-configure)
- Real table/column names: `FactInternetSales`, `FactResellerSales`, `FactSalesQuota`, `FactCurrencyRate`, `DimProduct`, `DimDate[FullDateAlternateKey]` / `DimDate[DateKey]`, `DimReseller`, `DimEmployee`, …

## Content rules for edits

- Write student docs in **Thai**; keep table/measure/DAX names in English.
- DAX examples that claim to use the course source must use the **real schema names** above (no `'Date'[Full Date]`, `'Currency Rate'`, `'Reseller Sales'`). Exception: generic pattern demos (e.g. `CROSSFILTER(Sales[…]…)` in 04, `ALL('Date'[Day])` hierarchy demos in 10) intentionally use generic names — leave them.
- Sample-file references must use the actual trainer `.pbix` filenames (`Data Model SCD.pbix`, `Data Model Conformed Date Dimension.pbix`, `Data Model - Reseller Sales.pbix`, `Data Model Time Intelligence with/without Calculation Group.pbix`, `Data Model Exchange Rate With Calculation Group.pbix`) plus the standard "Trainer Material — ขอไฟล์จากผู้สอน" note. Never invent `.SemanticModel` / `.Report` filenames.
- Correct measure spelling is `Shipped` / `Ordered` (older docs had `Shiped`/`Orderd` typos — do not reintroduce).
- Do not put HackMD teaching-history links or instructor-only details in student-visible files.

## Git / editing gotchas

- Files are **CRLF**. When scripting edits (perl -pi, etc.) preserve CRLF; verify with `grep -c $'\r'`.
- `.zcode/` holds ZCode session artifacts — do not commit (candidate for `.gitignore`).
- `.gitignore` excludes `*.pbix`, `*.pbip`, `*.pbit`, `*.bak`, `*.rdl`, and `Trainer_Docs/`.
- The parent folder `D:\dev\github\DataFabric-Academy\` contains **sibling course repos** (e.g. `powerbi-dax` uses Northwind, not AdventureWorksDW) — keep conventions separate per repo.

## Verification (no build system)

- Validate edits with `grep`: no `AdventureworksDW` (old casing), no `.SemanticModel`/`.Report` refs, no real passwords, `AdventureWorksDW2025` spelled consistently.
- Use the **Microsoft Learn MCP** tools to verify Microsoft docs links and DAX semantics before citing them.
