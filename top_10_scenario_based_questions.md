# Top 10 Power BI Scenario‑Based Interview Questions (2024)

These notes summarize the key scenario‑based Power BI questions and answer patterns from the video “Top 10 Power BI Interview Questions (Scenario Based) | Asked in Interviews 2024.”  
Use them to structure your own experience‑based answers.

---

## 1. Optimizing a Power BI Report for Performance & Efficiency

**Scenario**  
You are asked to describe a real situation where you optimized report performance.

**Key Answer Patterns**

- **Reduce data volume**
  - Load only required tables and **only needed columns**.
  - Apply row filters in Power Query to avoid unnecessary history or low‑value detail.
- **Use shared semantic layers**
  - When many reports use the same tables, create a **centralized model or dataflow** rather than each report connecting directly to the source.
  - Refresh the shared layer once and let all reports consume it.
- **Optimize visuals**
  - Limit the number of visuals per page; replace clutter with **tooltips** and **drill‑through** pages.
  - Avoid overly complex or high‑cardinality visuals where possible.
- **Improve DAX**
  - Rewrite inefficient measures, use variables, avoid row‑by‑row iterators where a simple aggregate works, and leverage proper modeling instead of complex DAX hacks.

Talk through a concrete project: number of reports, sources, problems observed, and the specific changes that improved performance.

---

## 2. Report Takes a Long Time to Load

**Scenario**  
A specific report is slow to open or interact with.

**Key Answer Patterns**

- **Visual & page design**
  - Reduce visuals on a page and remove unused or redundant visuals.
  - Avoid heavy visuals (large tables, many high‑cardinality slicers) on the landing page.
- **Connections & data model**
  - Reduce the number of live/direct connections where possible.
  - Remove unused columns/tables; ensure relationships are clean and minimal.
- **Model & DAX tuning**
  - Ensure star schema instead of many‑to‑many chains.
  - Optimize DAX: avoid unnecessary iterators, pre‑aggregate where appropriate, and cache complex logic in separate measures.

Link this question to Q1 but focus on a *single* slow report and what you did first, second, and third.

---

## 3. Working with Multiple Data Sources

**Scenario**  
You must combine, for example, Databricks data and Excel files in the same Power BI report.

**Key Answer Patterns**

- **Model design**
  - Connect to each source in Power Query and **normalize** structures where needed.
  - Manually create relationships between tables from different sources (Power BI will not auto‑relate across heterogeneous sources reliably).
- **Cardinality & direction**
  - Define proper cardinalities (1:* or *:1) and cross‑filter directions.
- **Refresh strategy**
  - Align refresh schedules so both sources are updated in a coordinated way.
- **Honesty if you lack direct experience**
  - If you have not done it, explain how you *would* design it: model planning, relationship design, and refresh orchestration.

---

## 4. Regional Sales Teams & Row‑Level Security (Dynamic RLS)

**Scenario**  
Multiple regional teams should see only their region’s sales; they must not see other regions.

**Key Answer Patterns**

- **Static RLS (baseline)**
  - Define roles in Desktop with filters like `[Region] = "India"`.
  - Assign users to roles in the Service.
- **Dynamic RLS (what the interviewer wants)**
  - Create an **entitlement table** mapping user identifiers (e.g., `UserPrincipalName`) to regions.
  - Use a DAX filter like `USERPRINCIPALNAME()` in the model to pick rows matching the current user.
  - Implement roles that filter via this mapping table so new users/regions are handled via data changes rather than new roles.

Be explicit that both static and dynamic RLS are possible, and why dynamic is usually preferred at scale.

---

## 5. Users Report “Wrong Data” in the Dashboard

**Scenario**  
Users complain that the numbers they see in Power BI are incorrect.

**Key Answer Patterns**

- **Check refresh status**
  - Confirm dataset refresh history and email notifications for failures.
  - Fix any refresh failures first (credentials, gateway, schema changes).
- **Trace from source to report**
  - Validate counts and totals in the **source system** using SQL or source queries.
  - Compare with **transformed tables** in Power Query.
  - Finally compare with **Power BI visuals**.
- **Investigate modeling issues**
  - Check for missing primary keys or surrogate keys.
  - Look for duplicates in keys that should be unique (e.g., `LineKey + Year` combos).
  - Check relationships (wrong join, inactive relationship, many‑to‑many causing double‑counting).
- **Iterative debugging**
  - Narrow down where numbers first diverge and fix at that layer.

Tell a concrete story: what was reported, where the error actually was, and what you changed.

---

## 6. Many Reports Using the Same 10–15 Tables (Dataflows)

**Scenario**  
Your team maintains ~10 reports that all use the same set of 10–15 tables. Refreshing each report separately hits the source multiple times.

**Key Answer Patterns**

- **Problem**
  - Many reports connect directly to the raw source, resulting in many live or scheduled refreshes, overloading the source and slowing refresh.
- **Solution: Dataflows / Shared ETL**
  - In the Service, create a **dataflow** containing the core 10–15 tables.
  - Refresh the dataflow once on a schedule.
  - Configure each report/dataset to connect to this dataflow instead of directly to the original source.
- **Benefits**
  - Reduced load on the original source.
  - Centralized transformations and logic.
  - Easier governance and performance tuning.

---

## 7. Ensuring Team Writes Good DAX (As a BI Lead)

**Scenario**  
You lead a BI team; how do you ensure your team writes good, maintainable DAX?

**Key Answer Patterns**

- **Standards & training**
  - Define coding standards for DAX structure, naming conventions, and use of variables.
  - Conduct knowledge transfer sessions on modeling vs DAX, filter context, and performance‑oriented patterns.
- **Tooling**
  - Use DAX Studio or similar tools to format and analyze DAX queries and query plans.
- **Reviews & rituals**
  - Include DAX reviews in code reviews or sprint ceremonies.
  - Discuss complex measures during daily stand‑ups and pair on refactoring where necessary.
- **Leverage assistants**
  - Use modern assistants (like LLMs) to help with formatting and basic refactoring, while still reviewing for correctness and performance.

Emphasize process (reviews, standards, training) rather than only tools.

---

## 8. Data Transformation Project: Oracle BI → Power BI (Plus ETL)

**Scenario**  
Client wants to move from traditional Oracle BI to Power BI and modernize ETL pipelines.

**Key Answer Patterns**

- **Assessment phase**
  - Inventory existing reports: how many, which subject areas, who uses them.
  - Catalog data sources, data volumes (GB/TB), refresh frequencies, and current SLAs.
- **Cloud & stack decision**
  - Compare cloud options (Azure, AWS, GCP) against requirements.
  - If Azure: Power BI is a natural fit (with Fabric, Synapse, Databricks, Data Lake).
  - If AWS: consider QuickSight; if GCP: Looker; avoid picking Power BI blindly if it doesn’t fit the stack.
- **Migration plan**
  - Build equivalent semantic models in the new stack.
  - Rebuild high‑value Oracle BI reports in Power BI first (MVP) and iterate.
  - Plan ETL migration to cloud data lake/warehouse and unify refresh logic.
- **User adoption**
  - Run knowledge‑transfer sessions comparing old vs new reports (and highlighting added value).
  - Transition users gradually, monitor feedback, then decommission legacy reports.

You can mention rough timelines as phased (discovery → pilot → rollout) rather than hard dates.

---

## 9. Near Real‑Time Power BI Reports

**Scenario**  
How to design a near real‑time report?

**Key Answer Patterns**

- **Push / streaming datasets**
  - Use **push datasets** that receive data via APIs and automatically update visuals.
  - Use **streaming datasets** (REST APIs, event streams) for dashboards that must update every few seconds.
- **DirectQuery**
  - Use DirectQuery when you need up‑to‑date data directly from the source and can accept performance trade‑offs.
  - Note that DirectQuery can be slow with large volumes and depends on source performance.
- **Hybrid approach**
  - Use scheduled Import for historical data and streaming/DirectQuery for most recent data.
- **Be honest about experience**
  - If you haven’t implemented near real‑time, say so and explain how you would design it given the toolset.

---

## 10. Exploring Internal Files Inside a `.pbix`

**Scenario**  
Have you ever unzipped a `.pbix` file and checked its internal structure?

**Key Answer Patterns**

- **What happens**
  - A `.pbix` file is essentially a zip; renaming to `.zip` or extracting reveals internal folders/files.
- **Typical internal components**
  - Connections and data model definitions.
  - Diagram/layout information.
  - Metadata and security bindings.
  - Report definition (json‑like structures).
- **Why it matters**
  - Advanced scenarios: diffing models, investigating corruption, bulk metadata updates, or advanced automation/scripts.
  - After edits, contents can be zipped back into a `.pbix` (with care).
- **Caution**
  - This is an advanced practice; emphasize using it carefully and typically in non‑production copies.

---

## Answering Strategy for Scenario‑Based Questions

- There is rarely a single “right” answer; interviewers are looking for:
  - Evidence you’ve **seen similar problems in real life**.
  - A **structured approach**: diagnosis → options → chosen solution → impact.
  - Awareness of trade‑offs (performance vs flexibility, stack choices, etc.).
- Always:
  - Ground your answer in a **real project** you’ve worked on.
  - Mention concrete tools/features (dataflows, RLS, DAX Studio, streaming datasets).
  - Close with the **business outcome** (faster refresh, fewer errors, better adoption).

