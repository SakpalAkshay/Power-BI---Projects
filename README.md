A repo for my Power BI projects and Power BI related interview questions :
***

# Top 25 Power BI Interview Questions – Notes

## Overview

- Interview questions are grouped into four key areas: Power Query (transformation layer), Power View/Reports (presentation layer), DAX (calculation layer), and Power BI Service (publishing, security, and collaboration).  
- Expect scenario‑based questions where you must both define concepts and explain how you used them in real projects, with emphasis on clear, structured answers.  

***

## Power Query Editor (Transformation Layer)

### Role of Power Query

- Power Query Editor is the built‑in ETL layer where you connect to data sources, clean, shape, and transform data before loading it into the model.  
- Common tasks include removing/adding columns, joining and appending tables, replacing values, changing data types, and applying conditional logic.  

### Common Transformations

- Text transforms: trimming, splitting columns, changing case, extracting substrings.  
- Number transforms: rounding, standard arithmetic, handling nulls, and type conversions.  
- Date/time transforms: extracting year/month/quarter, calculating durations, and building calendar logic.  
- Other frequent operations: merges (joins), appends (unions), conditional columns, and data type standardization.  

### Data Sources

- Power BI supports many data sources; interviewers often ask which ones you have actually used.  
- Typical answers include SQL Server, Oracle, flat files (CSV/Excel), JSON, XML, folder sources, and shared/network folders.  

### Import Mode vs DirectQuery

- Import mode: data is copied into Power BI’s in‑memory cache, giving full control for transformations and DAX but limited by dataset size.  
- DirectQuery: data stays in the source; visuals send queries back to the source, allowing huge datasets but restricting some DAX/time‑intelligence and adding performance/latency considerations.  

### Fact vs Dimension Tables

- Fact table: contains transactional or measurable business data (e.g., sales, revenue, quantities) often at a grain like “one row per transaction.”  
- Dimension table: contains descriptive/lookup attributes (e.g., customer, product, country) with surrogate keys used to slice and filter facts.  

### Star vs Snowflake Schema

- Star schema: one central fact table directly connected to surrounding dimension tables, resulting in simpler joins and better performance.  
- Snowflake schema: dimensions are further normalized into sub‑dimensions, leading to more complex joins and generally less optimal performance.  

### Append vs Merge

- Merge: combines columns from two tables based on a key; conceptually equivalent to SQL joins (inner, left, etc.).  
- Append: stacks rows from tables with the same structure, equivalent to a SQL `UNION` (or `UNION ALL`) operation.  

***

## Power View / Report Layer

### Core Visualization Experience

- Interviewers ask about your experience building reports and which chart types you use for different scenarios.  
- Common built‑in visuals include column charts, bar charts, line charts, pie/donut charts, cards/multi‑row cards, KPIs, maps, waterfall charts, and funnel charts.  

### Example‑Driven Chart Selection

- Waterfall chart for explaining year‑over‑year changes or bridge analyses (e.g., how revenue moved from last year to this year).  
- Funnel chart for marketing/sales pipelines (e.g., impressions → leads → opportunities → conversions).  
- Cards and multi‑row cards for highlighting key metrics like total sales, active users, or conversion rate.  

### Custom Visuals

- Power BI supports hundreds of custom visuals from AppSource and third parties.  
- Useful examples: drill‑down visuals, infographic charts, tornado charts (for comparing two categories), and Sankey charts (for flows between categories).  

### Filters vs Slicers

- Filters: configured in the filter pane by developers; end users often cannot see or easily change them.  
  - Visual‑level filters: affect only one visual.  
  - Page‑level filters: affect all visuals on a page.  
  - Report‑level filters: affect all pages in the report.  
- Slicers: on‑canvas, user‑friendly controls that allow end users to filter data interactively.  
  - Slicers can be synced across multiple pages using “Sync slicer,” and can be configured to affect selected pages only.  

### Interactions, Bookmarks, and Dynamic Reports

- Visual interactions can be edited so that certain visuals are affected (or not) by a slicer or filter.  
- Bookmarks capture the current state of visuals, filters, and selections, and can be used with buttons and actions to create dynamic navigation and storytelling.  
- A common interview question: “How do you make your report dynamic?” Answer: by combining bookmarks, buttons, and actions to toggle views, drill paths, and layouts.  

***

## DAX (Data Analysis Expressions)

### Importance of DAX

- DAX is described as the “right hand” of Power BI, providing calculations, aggregations, and advanced logic on top of the data model.  
- There are hundreds of functions across many categories: filter, time intelligence, math, logical, text, and table functions.  

### Measure vs Calculated Column

- Measure: a reusable calculation evaluated at query time, dependent on filter context, and not physically stored in the table.  
- Calculated column: a new column computed row‑by‑row at data refresh time and stored in the model, consuming memory but useful when needed as a dimension or join key.  

### Key Function Categories

- Filter functions: `CALCULATE`, `CALCULATETABLE`, `ALL`, `ALLEXCEPT`, `ALLSELECTED`, etc., for modifying filter context.  
- Time intelligence functions: used for previous month/year, same period last year, year‑to‑date, quarter‑to‑date, etc., often requiring a dedicated date table.  
- Other categories: date functions, mathematical functions, and logical functions (`IF`, `AND`, `OR`, `SWITCH`) that support business rules.  

### SUM vs SUMX

- `SUM(column)`: simple aggregation over an existing numeric column, returning a single scalar total.  
- `SUMX(table, expression)`: iterates row‑by‑row over a table, evaluates an expression for each row, and then sums the results—useful when the required column does not physically exist.  
- `SUM` is aggregate context, while `SUMX` works at row context and can combine multiple columns within the expression.  

### SUMMARIZE

- `SUMMARIZE(table, groupByColumns…, [name], [expression]…)` returns a new table grouped by specified columns.  
- Used to pre‑aggregate data (e.g., sales by country or by customer) which can be reused across visuals or as the basis for further calculations.  

### ALL and ALLEXCEPT

- `ALL(tableOrColumn)`: removes filters from a table or column, often used to compute grand totals or percentages of total.  
- `ALLEXCEPT(table, columns…)`: removes filters on all columns in a table except those explicitly listed, preserving some grouping while clearing others.  

### Date Master (Date Dimension)

- A dedicated date (calendar) table, often called “Date Master,” is recommended for time intelligence.  
- This table defines the valid dates, as well as year, month, quarter, and fiscal boundaries, enabling correct calculations for YTD, QTD, and prior‑period comparisons.  

### Same Period Last Year

- Example use case: comparing this January’s sales with last January’s instead of comparing January with December, which may be seasonally biased.  
- Time intelligence functions (e.g., `SAMEPERIODLASTYEAR`) leverage the date table to shift the comparison period by one year.  

***

## Power BI Service (Publishing, Security, Governance)

### Licensing: Free, Pro, Premium

- Common interview question: which license types have you used and for what.  
- Pro: typical for most developers and analysts; supports sharing, collaboration workspaces, and scheduled refresh within limits.  
- Premium: capacity‑based, often held by team leads or admins; enables larger models, higher refresh frequency, dataflows, and advanced features.  

### My Workspace vs Workspaces

- My Workspace: personal area where only the owner can see reports; good for drafts, experiments, and personal research.  
- Workspaces: shared, collaborative environments for a project or team where multiple users can publish and manage reports and datasets.  

### Workspace Roles

- Admin: full control, including adding/removing users and managing all content and settings.  
- Member: broad permissions similar to admin but typically cannot change certain high‑level settings; can manage content and often add users (depending on configuration).  
- Contributor: can contribute to content (publish, edit reports/datasets) but cannot manage access or some workspace settings.  
- Viewer: read‑only access; can view reports and dashboards but cannot edit content or add others.  

### Row‑Level Security (RLS)

- RLS restricts data at the row level so users see only the records they are authorized to view (e.g., India users see only India data).  
- Static RLS: roles and filters defined in Power BI Desktop (Manage Roles) and then published; users are manually assigned to roles in the service.  
- Dynamic RLS: uses DAX functions like `USERPRINCIPALNAME()` plus an entitlement table mapping users to regions/segments, so filters are driven by the logged‑in user’s identity.  

### Reports vs Dashboards

- Report: typically multi‑page, interactive, and can contain several report pages per dataset; supports cross‑highlighting and exploration.  
- Dashboard: single‑page canvas composed of tiles pinned from one or more reports, often used as a high‑level snapshot across multiple datasets.  

### Gateways

- On‑premises data gateway connects on‑premises data sources to the Power BI Service for refresh and DirectQuery.  
- Personal gateway: installed and used by a single user, suitable for personal scenarios or small setups.  
- Standard/enterprise gateway: shared across many users and datasets, centrally managed for production environments.  

### Dataflows

- Dataflows are reusable, cloud‑based ETL pipelines in the Power BI Service that let you build and manage transformations in the browser.  
- Conceptually, a dataflow is “Power Query in the service,” allowing multiple workspaces and reports to reuse curated, transformed tables.  

***



