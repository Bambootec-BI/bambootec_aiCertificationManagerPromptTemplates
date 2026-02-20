1 Conceptual Overview

1.1 What is Power Query and where it fits (Excel, Power BI) — Impact: High

1.2 End-to-end query example (connect → transform → load, refresh) — Impact: High

1.3 Read and understand Applied Steps (dependencies) — Impact: Moderate

1.4 Step naming and ordering best practices (prefixes, atomic steps) — Impact: Moderate

1.5 Basic error handling (Replace Errors, try…otherwise) — Impact: High

2 Data sources and connectivity

2.1 Mental model: data contract (schema, credentials) — Impact: High

2.2 Example: consolidate logistics shipments (SQL Server, SharePoint folder) — Impact: High

2.3 Connect to files and folders (CSV, Excel) — Impact: High

2.4 Connect to databases and services (SQL Server, OData) — Impact: High

2.5 Manage credentials and privacy (privacy levels, gateway) — Impact: High

3 Data cleaning and transformation

3.1 Mental model: from raw data to trusted data (correct types, rules) — Impact: Critical

3.2 Example: clean production orders (dates, keys) — Impact: High

3.3 Set data types and control locale (es-ES, en-US) — Impact: Critical

3.4 Filter and select columns (remove nulls, keep fields) — Impact: High

3.5 Clean text and formats (Trim/Clean, replacements) — Impact: High

3.6 Handle nulls and errors in columns (Replace Values, try…otherwise) — Impact: High

3.7 Summarize and reshape data (Group By, Unpivot) — Impact: High

4 Combining and preparing for the model

4.1 Mental model: keys and granularity before combining (SKU, batch/lot) — Impact: Critical

4.2 Example: join inventory and movements (Merge, Expand) — Impact: High

4.3 Merge (joins) and join kinds (Left Outer, Inner) — Impact: Critical

4.4 Append (union tables) with a consistent schema (same columns, same types) — Impact: High

4.5 Calculated and conditional columns (if/then, business rules) — Impact: High

5 Reuse, parameters, and basic M

5.1 Mental model: reuse without duplicating logic (parameters, functions) — Impact: High

5.2 Example: country/plant parameter for multinationals (MX, DE) — Impact: High

5.3 Create and use parameters (date range, DEV/PROD environment) — Impact: High

5.4 Custom functions for repeatable cleaning (normalize SKU, standardize units) — Impact: High

5.5 Edit M and harden against schema changes (Advanced Editor, MissingField) — Impact: Moderate

6 Performance, quality, and delivery

6.1 Mental model: reliable refresh at scale (folding, governance) — Impact: Critical

6.2 Example: optimize daily logistics refresh (filter early, folding) — Impact: High

6.3 Query folding and how not to break it (filter early, compatible steps) — Impact: Critical

6.4 Staging vs final design (references, disable load) — Impact: High

6.5 Operations: validations and support (row counts, documentation) — Impact: High
