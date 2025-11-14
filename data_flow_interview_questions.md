# Power BI Dataflows – Interview‑Ready Notes

These notes summarize the key concepts and steps from the “Power BI Dataflows Interview Questions – Answered!” video, using the example of many reports sharing the same tables.

---

## 1. Problem: Many Reports, Same Tables, Too Many Refreshes

**Scenario**

- You might have:
  - ~20 reports today; potentially 100+ in the future.
  - All using the **same 10–15 tables** from a common data source (e.g., Snowflake, MySQL, Azure SQL, etc.).
- Traditional setup:
  - Each report has its own **semantic model** (dataset) pointing directly to the source.
  - Each semantic model has its own **scheduled refresh**.
  - 20 reports ⇒ 20 refreshes; 100 reports ⇒ 100 refreshes.

**Issues**

- Every refresh is a **live connection** to the production source:
  - Repeated, redundant queries for the same tables.
  - Increased load on the source system.
  - Longer refresh windows and potential performance impact on the database.
- Not scalable as number of reports grows.

---

## 2. What Is a Dataflow and Why Use It?

**Dataflow concept**

- A **dataflow** is a reusable ETL layer hosted in the Power BI Service.
- It is essentially **Power Query in the cloud**:
  - You define tables and transformations once.
  - Multiple semantic models (datasets) and reports can reuse those tables.

**Architecture change**

- Before:
  - Source (e.g., Snowflake) → many semantic models → many reports.
  - Each semantic model refresh hits the source.
- After:
  - Source → **single dataflow** (with 10–15 tables) → many semantic models → many reports.
  - Only the dataflow refresh hits the source; datasets refresh from the dataflow.

**Key advantage**

- From the **original data source’s perspective**, you now have:
  - One scheduled refresh hitting it (the dataflow).
  - Datasets still refresh, but they refresh from the dataflow, not the live source.
- Reduces load on the source while keeping the same number of report‑level refreshes.

---

## 3. Business Scenario Example

- Data source: **Snowflake** (but could be any DB).
- You have:
  - Multiple schemas and tables in Snowflake.
  - Team workspace with many reports (e.g., customer churn dashboards, Telco example).
- Initially:
  - Each report connects directly to Snowflake, with its own semantic model and refresh schedule.
  - Lineage: Snowflake → Semantic Model 1 → Report 1; Snowflake → Semantic Model 2 → Report 2; etc.

Goal: Insert a **dataflow** between Snowflake and the semantic models to centralize data access.

---

## 4. Creating a Dataflow (Step‑by‑Step)

1. **Open the workspace** in Power BI Service.
2. Click **New** (or “+ New item”) → **Dataflow**.
3. Choose **Define new tables**.
4. Select the data source:
   - Example: **Snowflake**.
   - Provide server/warehouse details (e.g., Snowflake server name, warehouse like `INNOVATION`).
5. Choose tables:
   - Browse schemas (e.g., `TEST` → `PUBLIC` → `CUSTOMER_CHURN`).
   - Optionally select **multiple tables** for the same dataflow.
   - Click **Transform data**.

6. **Transform in Power Query Online**:
   - You see a familiar Power Query interface in the browser.
   - Apply any transformations you need (same type as in Desktop).
   - In the demo, no changes were applied; they just clicked **Save & Close**.

7. Name and refresh:
   - Give the dataflow a name (e.g., `CustomerDataFlow`).
   - Choose **Refresh now** (ad‑hoc) and/or **Scheduled refresh** (for production).
   - After saving, the workspace lineage will show:
     - Source → Dataflow → (no reports yet, until connected).

---

## 5. Dataflow Refresh & History

- In the dataflow’s settings:
  - You can trigger **Refresh now**.
  - You can configure **scheduled refresh** (frequency, time zone, times).
- Refresh history:
  - Dataflows show a **detailed refresh history** (start/end time, status).
  - You can **download the history** as a file to analyze durations and failures.
- This is more detailed than the simple “last refresh” info shown for many datasets.

---

## 6. Switching Reports from Direct Source to Dataflow

To migrate existing reports from direct source (e.g., Snowflake) to dataflows:

1. **Open the report in Power BI Desktop**.
2. Go to **Transform data** (Power Query).
3. Observe:
   - Existing queries: source is Snowflake (or other DB).
4. Add a new data source:
   - **New source → Power BI dataflows**.
   - Connect to the workspace dataflow (e.g., Telco workspace → `CustomerDataFlow`).
   - Choose the table(s) you need.

5. Now you will have:
   - Original query (Snowflake‑based).
   - New query (dataflow‑based) for the same table.

6. Update the query’s M code:
   - Open **Advanced Editor** on the new dataflow query.
   - Copy the M script.
   - Open **Advanced Editor** on the original Snowflake query.
   - Replace its code with the dataflow M script.
   - Click **Done**.
7. Delete the extra dataflow query (so you keep just one query per table).
8. Click **Close & Apply**.
9. Publish the updated report to the same workspace.

Repeat for each report that should use the dataflow as its source.

---

## 7. Handling Filters and Transformations After Switching

- When you switch a query’s source from Snowflake to Dataflow:
  - All **Power Query steps** must either:
    - Be replicated in the dataflow, or
    - Be re‑applied in the report’s query if they’re report‑specific.
- Example:
  - Original report filtered `Churn = "Yes"` in Power Query.
  - After switching to dataflow, that filter was lost (since the new query was plain).
  - Fix: reopen **Transform data**, re‑apply the filter on `Churn = "Yes"`, then apply and republish.

Decide what belongs in the **central dataflow** vs what’s **report‑specific**.

---

## 8. Lineage View After Implementing Dataflows

- After migration, the lineage looks like:

  - Source (Snowflake)
    → **Dataflow** (e.g., `CustomerDataFlow`)
    → Semantic Model 1 → Report 1
    → Semantic Model 2 → Report 2
    → Semantic Model 3 → New Report, etc.

- Previously, it was:
  - Snowflake → Semantic Model 1 → Report 1
  - Snowflake → Semantic Model 2 → Report 2
  - Snowflake → Semantic Model 3 → Report 3, etc.

Now there is only **one link** from Snowflake into Power BI (via the dataflow).

---

## 9. Scheduling Refreshes with Dataflows

- **Dataflow level**
  - Configure scheduled refresh in the dataflow settings.
  - Only this refresh hits the original source.
- **Dataset/semantic model level**
  - Each dataset still has its own scheduled refresh, but:
    - It pulls from the dataflow, not the original DB.
- Example:
  - Dataflow refresh at 9:00 A.M.
  - Dataset refreshes follow (e.g., at 9:15 A.M., 9:30 A.M., etc.) to ingest from dataflows.

Net effect: more total refresh jobs, but **only one against the live source**.

---

## 10. Creating New Reports from an Existing Dataflow

- To build a new report using existing curated tables:

1. Open Power BI Desktop.
2. Choose **Get Data → Power BI dataflows**.
3. Select the appropriate workspace and dataflow (e.g., `CustomerDataFlow`).
4. Load the required table(s).
5. Build visuals as usual.
6. Publish to the same workspace.
7. Configure dataset refresh (schedule) as needed.

Benefits:
- New reports use the same **governed, transformed tables**.
- No additional load on the original database; they reuse the dataflow.

---

## 11. Extending a Dataflow (Adding More Tables)

- If you later want to add more tables into an existing dataflow:

1. In the Service, select the dataflow and click **Edit**.
2. Add new tables by:
   - Choosing the source (e.g., Snowflake again).
   - Selecting additional tables.
   - Applying any transformations.
3. Save and refresh the dataflow.

These new tables then become available to all datasets and reports that connect to that dataflow.

---

## 12. Interview‑Ready Summary

If asked “What are dataflows in Power BI and why would you use them?” you can answer:

- Dataflows are **Power Query‑based ETL objects in the Power BI Service** that centralize and reuse data transformations across reports.
- They are especially valuable when:
  - Many reports share the same source tables.
  - Directly refreshing each report from the source would create too many live connections and performance issues.
- With dataflows:
  - The **source is hit once** (via the dataflow refresh).
  - Multiple semantic models and reports pull from the dataflow.
  - You get better performance, shared logic, refresh history, and a cleaner data lineage.

Use this explanation plus a concrete scenario (like the Snowflake + Telco churn reports example) to give a strong, practical interview answer.
