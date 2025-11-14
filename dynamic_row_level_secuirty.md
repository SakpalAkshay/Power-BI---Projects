```markdown
# Dynamic Row Level Security (RLS) in Power BI

These notes summarize the key concepts and implementation steps from the video “Dynamic Row Level Security in Power BI | Top Power BI Interview Question Asked in Interviews.”

---

## 1. Why Dynamic RLS?

- **Static RLS pain points**
  - For static RLS, you manually:
    - Open each `.pbix` file.
    - Create roles per user/region.
    - Publish and map users to roles per dataset.
  - With many reports and thousands of users, this becomes tedious and error‑prone.

- **Dynamic RLS idea**
  - Instead of hard‑coding user filters in roles, use:
    - **Who is logged in** (user identity).
    - **What access each user should have** (from a table).
  - One generic role plus a DAX rule makes the model dynamically filter per user.

---

## 2. Example Data Model Used in the Demo

- **Sales table**
  - Columns: `Date`, `Salesperson`, `Amount` (and/or `SalespersonID`).
- **Salesperson table**
  - Columns: `SalespersonID`, `Name`, `Email`.

- **Relationship**
  - A relationship exists between `Sales.Salesperson` (or `SalespersonID`) and `Salesperson.SalespersonID`.
  - Cardinality: typically **many‑to‑one** (many sales rows per salesperson), single‑direction filter from Salesperson to Sales.

This simple model is enough to demonstrate per‑salesperson security.

---

## 3. Static RLS (Recap & Limitations)

- In static RLS you might:
  - Create a role like `Satyajit_Role` with a filter:
    - `Sales[Email] = "satyajit@zepanalytics.com"`.
  - Repeat for `Ankit`, `Namrata`, and so on.

- **Limitations**
  - One role per user does not scale when there are hundreds or thousands of users.
  - If there are multiple reports in a workspace, the process must be repeated per report/dataset.

Dynamic RLS solves this by using a single generic role and a user‑aware DAX filter.

---

## 4. Core DAX Function: USERPRINCIPALNAME()

- A measure is created, e.g.:

  
  User =
      USERPRINCIPALNAME()
  

- In Power BI Desktop this may show a machine‑style account (e.g., `DESKTOP\user`), but:
  - In the Power BI Service (app.powerbi.com), for real business users it returns their **Azure AD / work email** (e.g., `user@company.com`).

Dynamic RLS relies on this function to match logged‑in users to their allowed rows.

---

## 5. Building the Report (High‑Level)

1. **Connect data**
   - Use Excel (or any source) to load `Sales` and `Salesperson` tables.
   - Confirm that Power BI has created a relationship between them; fix it if needed (many‑to‑one, single‑direction).

2. **Create visuals**
   - Build a table visual with:
     - `SalespersonID`, `Name`, `Email`, `Amount`, `Date`.
   - Add a card to show total sales (`SUM(Amount)`).
   - Add an optional card showing the `User` measure (to see the identity used in the Service).

At this point there is no security: all rows are visible.

---

## 6. Defining the Dynamic RLS Role in Desktop

1. Go to **Modeling → Manage roles**.
2. Create a new role, e.g. **`UserRole`**.
3. Select the **Salesperson** table (or the table that holds user emails).
4. Add a DAX filter such as:

   
   'Salesperson'[Email] = USERPRINCIPALNAME()
   

   or, if you defined it as a measure:

   
   'Salesperson'[Email] = [User]
  

5. Save and close the role dialog.

This role says: *“For this role, keep only those Salesperson rows whose Email matches the current user’s principal name,”* and the relationship then filters Sales.

---

## 7. Publishing and Configuring Security in the Service

1. **Publish the report**
   - Click **Publish** from Desktop.
   - Choose the target workspace.
   - Confirm upload succeeds.

2. **Assign users/groups to the role**
   - In the Power BI Service:
     - Go to the workspace.
     - Find the **semantic model / dataset** associated with the report.
     - Open its **Security** settings.
   - Under the role you created (e.g. `UserRole`):
     - Add users or, preferably, **security groups** (e.g., an Azure AD group with thousands of users).

3. **Workspace access**
   - Ensure users (or groups) have at least **Viewer** access to the workspace or app.
   - The role assignment plus workspace permission controls what they see.

When a user in that role opens the report, the DAX filter evaluates with their identity and restricts rows accordingly.

---

## 8. Testing Dynamic RLS

- **Different identities**
  - Log in as different test users (e.g., `contact@...`, `user2@...`) and confirm each sees only their rows.
  - For example:
    - `contact@...` → one row, sum = 400.
    - `namrata@...` → only Namrata’s row, sum = 500.
  - The report creator/admin can see all data if viewing without “View as” or if they have bypass permissions.

- **View as role (optional)**
  - In Desktop, use **Modeling → View as** to simulate the role and verify filters before publishing.

---

## 9. Scaling to Thousands of Users

- **Avoid adding users one by one**
  - Instead of listing 10,000 users individually, create an **Azure AD / Office 365 group** that contains all relevant users.
  - Add that group once to the RLS role in the Service.
- **Entitlement model**
  - In more complex setups, the security table (e.g., Salesperson) can hold user‑to‑region mappings, hierarchies, etc.
  - Dynamic RLS then uses USERPRINCIPALNAME() plus that mapping to determine which rows are visible.

---

## 10. Interview‑Ready Summary

- **Problem with static RLS**
  - Manual, role‑per‑user setup across many reports and users is unmanageable.

- **Dynamic RLS solution**
  - Store user‑to‑access mapping in a table (e.g., Salesperson with Email).
  - Use a single role with a DAX filter like:
    - `'Salesperson'[Email] = USERPRINCIPALNAME()`.
  - Assign users or groups to this role in the Service.

- **Benefits**
  - One generic security rule for all users.
  - Scales to thousands of users and many reports.
  - Centralizes security logic in the model instead of per‑user hard‑coding.

Use these steps and explanations to both implement Dynamic RLS and to answer interview questions clearly and concretely.
```

[10](https://www.hexstream.com/tech-corner/achieving-dynamic-row-level-security-rls-with-organizational-hierarchy-in-power-bi)
