

***

## Interview Answers – Second Transcript Only

### Q1. Explain the difference between a calculated column and a measure.

A **calculated column** is evaluated row by row and stored in the model as a physical column. It’s used when you need a value per row, such as age bands (`0–20`, `21–40`, etc.) derived from an Age column, or a classification flag that must be available as a slicer or relationship key.  

A **measure** is a dynamic calculation evaluated at query time in the current filter context and is not stored as a column. It’s best for aggregations like total salary, total records, or average salary of female customers, similar to `SELECT AVG(Salary) FROM Table WHERE Gender='Female'` in SQL, implemented with DAX (`CALCULATE(AVERAGE(...), filter)`).  

***

### Q2. How do you handle missing values?

Handling missing values depends on context and where you control the pipeline. In some projects, missing values are cleaned upstream (e.g., using Python or SQL to impute, drop, or flag records before loading into Power BI).  

Within Power BI, missing values can be handled in Power Query using “Replace values,” custom conditional columns, or by filling blanks with appropriate defaults (average, median, 0, or “Unknown”), and in DAX using expressions like `IF(ISBLANK([Measure]), [Fallback], [Measure])` or `COALESCE`. The key is to tie the method to business meaning rather than treating it as a purely mechanical operation.  

***

### Q3. What is DAX, and can you give an example?

DAX (Data Analysis Expressions) is the formula language used in Power BI (and Excel data models) for creating measures, calculated columns, and calculated tables. It provides functions for filtering, aggregation, time intelligence, and logical operations.  

Example: to calculate total charges for female customers, you might create a measure like:
```DAX
Total Charges Female =
CALCULATE(
    SUM('Customers'[TotalCharges]),
    'Customers'[Gender] = "Female"
)
```
This is analogous to a SQL query that sums charges with a `WHERE Gender = 'Female'` condition.  

***

### Q4. How do you disable a graph from changing dynamically when slicers change?

Use the **Edit interactions** feature. First, select the slicer, then go to the Format tab and click **Edit interactions**. For the visual you want to keep static, choose the “none” (no filter) icon so that the slicer no longer affects it.  

This approach allows other visuals to respond normally to the slicer while one key chart remains fixed as a constant reference.  

***

### Q5. Explain RLS (Row‑Level Security) and how you implement it.

Row‑Level Security (RLS) restricts which rows of data a given user can see, enforcing data‑access rules at the row level. For example, Indian users see only India data, and German users see only Germany data, even though they all use the same report.  

Implementation typically involves defining roles and filters in Power BI Desktop using **Manage roles** (e.g., `[Country] = "India"`), publishing the model, and then mapping users to these roles in the Service. More advanced setups use dynamic RLS with functions like `USERPRINCIPALNAME()` and an entitlement table so that access is driven by metadata rather than hard‑coded filters.  

***

### Q6. Explain the process of publishing a report.

Once a report is ready in Power BI Desktop, click **Publish**. You will be prompted to choose a workspace in Power BI Service. After selection, Power BI uploads the report and underlying dataset (semantic model) to that workspace.  

Users can then access the report by going to `app.powerbi.com`, navigating to the appropriate workspace, and opening the report from there. From the workspace, further steps such as creating a dashboard, setting RLS, and scheduling refresh can be configured.  

***

### Q7. How do you create relationships in Power BI?

Relationships are created in the **Model** (Relationship) view. You drag a key column from one table (e.g., `CustomerID` in the Customers table) to the matching key in another table (e.g., `CustomerID` in the Orders table).  

Power BI suggests a cardinality: one‑to‑one, one‑to‑many, many‑to‑one, or many‑to‑many. A common example is a one‑to‑many relationship between Customers (one row per customer) and Orders (multiple rows per customer). Relationships can be adjusted for cardinality and cross‑filter direction as needed.  

***

### Q8. How do you schedule a refresh in Power BI?

After publishing, go to the Power BI Service, open the workspace, and locate the dataset/semantic model. Click the three dots (…) → **Settings** → **Scheduled refresh**.  

From there, configure refresh frequency (daily, weekly), specific times, and ensure credentials and gateway (if required) are correctly set. You can also enable notification on refresh failure and, if needed, add email recipients for status alerts.  

***

### Q9. How do you perform join operations in Power BI?

Join operations are primarily done in **Power Query**.  
- Use **Merge Queries** to join two tables based on one or more keys, choosing join type (inner, left outer, right outer, full outer, etc.).  
- Use **Append Queries** to stack tables with the same structure when you want to combine datasets vertically.  

This is conceptually equivalent to SQL joins and unions, allowing you to prepare the model’s tables before loading.  

***

### Q10. What are good practices in Power BI?

Examples of good practices include:  
- **Centralize measures**: Create a dedicated “Measures” or blank table and define all measures there, rather than scattering them, to keep the model organized.  
- **Avoid cluttered dashboards**: Limit visuals per page, highlight only key metrics, and use tooltips and drill‑through for detail rather than packing too many charts into one canvas.  
- **Use certified visuals**: Prefer Microsoft‑certified visuals from AppSource for reliability and governance.  
- **Be careful with DirectQuery on live sources**: Develop and prototype in Import mode for performance; switch to DirectQuery or composite models only when necessary.  
- **Design for the user**: Match themes (dark/light), layout, and navigation to stakeholder preferences; use sketches and feedback loops before finalizing.  

***

### Q11. How do you explain your latest Power BI project?

Use a structured storytelling approach:  
- Start with the **business problem** (e.g., churn analysis, revenue monitoring).  
- Describe the **data sources** and transformation steps (databases, Python/SQL scripts, Power Query).  
- Explain the **data model** (fact and dimension tables, relationships, RLS if relevant).  
- Walk through **key report pages**: home page, summary views, drill‑down pages, main KPIs (e.g., churn rate, senior‑citizen churn, total customers).  
- Conclude with **deployment and impact**: how you published it, who uses it, and what decisions it supports or improved.  

***

This README focuses exclusively on the questions and patterns discussed in the “Top 10 Power BI Interview Questions | Asked in Interviews 2024” video and is intended as a targeted interview prep guide.
```
