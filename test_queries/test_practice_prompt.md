<name>
  skill_to_practice_exercises
</name>

<version>
  v1.9.0
</version>

<description>
  Generates practice exercises for ANY skill, ANY level, ANY context using XML-structured prompts for better instruction adherence.
</description>

<skill_tree>
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
</skill_tree>

<content_summary>
Ingestion and combination practice (OData + CSV) — Practical objective: create a "ready-to-model" table combining customers with a country catalog for multinational use.

Micro-skills:

Connectors, credentials, and navigation

Merge (join) and consistent typing

Estimated time: 60-110 minutes

1.1 Connect and combine sources — Practice focus: ingest OData + CSV and deliver a clean dimension for reporting.

Estimated time: 60-110 minutes
1.1.1 Exercise: DimCustomerCountry ready for BI

Problem statement:

Build a table that combines customers (Northwind) with a country catalog (ISO) to standardize the country in multinational projects.

Verifiable objective:

Deliver DimCustomerCountry with CountryName and CountryCode (ISO) without type errors.

Dataset (acquisition):

Northwind OData V4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Country list (CSV) - https://datahub.io/core/country-list/r/data.csv

Prerequisites:

Power BI Desktop or Excel (Power Query)

Setup (copy/paste):

Create 2 queries: Customers from OData and Countries from the CSV

Tasks:

Select minimum columns in Customers (CustomerID, CompanyName, Country)

Type columns (CustomerID text, CompanyName text, Country text)

In Countries, rename columns to CountryName and CountryCode

Merge Customers.Country = Countries.CountryName (Left join)

Expand CountryCode and validate nulls/errors

Execution command:

Power BI Desktop: Home > Refresh, then Close and Apply

Expected output:

Table DimCustomerCountry with columns CustomerID, CompanyName, CountryName, CountryCode

Expected file tree:

One PBIX/XLSX with 2 staging queries and 1 final query (DimCustomerCountry)

Definitions:

Dimension: descriptive table used to segment/filter metrics

Acceptance tests (how to test) — Pass/Fail:

PASS if CountryCode exists for the majority of rows and there are no type errors on load

Evidence:

Screenshot of the queries panel showing the 3 queries and preview of DimCustomerCountry

Hints (optional):

If some countries don't match, normalize case/spaces before the Merge

Estimated time: 60-110 minutes

Cleaning, typing, and quality practice — Practical objective: clean an operational dataset (text + coordinates) and make it consistent for merges and KPIs.

Micro-skills:

Text cleaning (Trim/Clean) and safe typing

Column profiling (quality/distribution)

Estimated time: 50-90 minutes

2.1 Standardize and validate reference data — Practice focus: create a clean and auditable dimension for logistics.

Estimated time: 50-90 minutes
2.1.1 Exercise: DimAirport clean and typed

Problem statement:

Clean and type an airport catalog so it can be used as a reference in logistics analysis.

Verifiable objective:

Deliver DimAirport filtered and typed without errors, with standardized text.

Dataset (acquisition):

Airport codes (CSV) - https://datahub.io/core/airport-codes/r/airport-codes.csv

Prerequisites:

Power BI Desktop or Excel (Power Query)

Setup (copy/paste):

Create 1 query from Web/CSV and enable profiling tools

Tasks:

Filter to a useful subset (e.g., type = large_airport or medium_airport)

Apply Trim/Clean on name and municipality (or equivalents)

Type latitude_deg and longitude_deg as decimal number

Review Column quality/distribution and fix critical nulls (if applicable) or flag them

Execution command:

Power BI Desktop: Transform Data > Close and Apply

Expected output:

Table DimAirport with clean text and numeric coordinates (no conversion errors)

Expected file tree:

One PBIX/XLSX with 1 staging query and 1 final query (DimAirport)

Definitions:

Profiling: review of nulls/errors/distribution to detect issues before modeling

Acceptance tests (how to test) — Pass/Fail:

PASS if latitude_deg/longitude_deg are typed as number and Column quality shows no errors

Evidence:

Screenshot of Column quality + preview of DimAirport with correct types

Hints (optional):

If a type error appears, check for non-numeric values or regional separators

Estimated time: 50-90 minutes

Operational aggregation practice (Group By + dates) — Practical objective: generate an aggregated table ready for KPI (by period and logistics carrier).

Micro-skills:

Group By and aggregated metrics

Date column preparation for periodization

Estimated time: 60-110 minutes

3.1 Build a metrics table by period — Practice focus: summarize orders by month and carrier for an operational dashboard.

Estimated time: 60-110 minutes
3.1.1 Exercise: Shipment KPI by month and shipper

Problem statement:

Generate an aggregated table of orders shipped by month and by carrier (shipper) from Northwind.

Verifiable objective:

Deliver a table KPI_Shipments_Month with counts and a consistent period date.

Dataset (acquisition):

Northwind OData V4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Prerequisites:

Power BI Desktop or Excel (Power Query)

Setup (copy/paste):

Create queries Orders and Shippers from OData

Tasks:

In Orders, keep OrderID, ShippedDate, ShipVia

Create a period column (e.g., start of month from ShippedDate)

Merge Orders.ShipVia = Shippers.ShipperID to bring CompanyName

Group By Period and ShipperName with Count Rows (orders shipped)

Execution command:

Power BI Desktop: Home > Refresh, then Close and Apply

Expected output:

Table KPI_Shipments_Month with Period, ShipperName, OrdersShipped (count)

Expected file tree:

One PBIX/XLSX with 2 staging (Orders, Shippers) and 1 final (KPI_Shipments_Month)

Definitions:

Group By: operation to group rows by keys and calculate aggregations

Acceptance tests (how to test) — Pass/Fail:

PASS if there are no type errors in dates and OrdersShipped is numeric and >0 across several periods

Evidence:

Screenshot of the aggregated table and the Group By step in "Applied Steps"

Hints (optional):

If ShippedDate has nulls, filter nulls before calculating the period

Estimated time: 60-110 minutes
</content_summary>

<role>
  Persona: Instructional designer + practitioner
  Mission: Create dataset-grounded practice exercises for Power Query at Junior level in role in a business intelligence company with multinational clients in manufacturing and logistics, aligned to the content_summary above.
</role>

<task>
  Generate an iterative practice exercise hierarchy for Power Query based solely on the Junior level, the context of role in a business intelligence company with multinational clients in manufacturing and logistics, and the content_summary. Every exercise must use real datasets only with immediate acquisition (exact URL or exact dataset id/name + loader steps). No search queries. No invented URLs. No inline datasets. No test code; acceptance tests must be directives/checklists with PASS/FAIL thresholds derived from inputs. Output in PARTS with stable numbering and running totals.
</task>

<agnosticity_guards>
  - Do NOT assume any specific domain, industry, data columns, schemas, KPIs, or file formats unless they are implied by the content_summary or the context
  - Do NOT assume any specific tools unless clearly implied by the context
  - Do NOT assume any specific level requirements; interpret level ONLY from the Junior level and the constraints/pitfalls in the content_summary
  - Do NOT include any dataset suggestions unless you can provide a real acquisition method (exact URL or exact dataset id/name + loader steps)
  - Never include search queries or placeholders such as 'look up'/'Search query'
</agnosticity_guards>

<code_policy>
  Code allowed ONLY in 'Dataset (acquisition)' section.
  Limit: Only minimal dataset load/download + save-to-disk (or tool steps). No solution code, no test code.
  Prefer no-code when possible.
</code_policy>

<use_content_summary>
  - For each exercise, explicitly tie the exercise to at least one item from content_summary (concept OR pitfall OR constraint OR expected artifact)
  - Use content_summary to define: what success looks like, which mistakes to catch, what outputs are expected, and which checks matter
</use_content_summary>

<acceptance_tests_no_code>
  - Do NOT provide code for tests
  - Provide 'Acceptance tests (how to test)' as a checklist with explicit PASS/FAIL criteria and concrete thresholds
  - Thresholds must come from the content_summary/context when available; otherwise use generic minimums (e.g., file exists; schema non-empty; counts coherent; rerun yields same counts)
</acceptance_tests_no_code>

<parts_system>
  Enabled: true
  Max numbered items per part: 28
  Counting rule: Only numbered items count (1., 1.1, 1.1.1...). Bullets do NOT count.

  Stop conditions:
  - Stop immediately when the max is reached
  - Stop early if overflow is likely

  End text: Reply: continue

  Continuity:
  - Maintain numbering across PARTS
  - Continue exactly where you left off; do not repeat items
</parts_system>

<time_total>
  - Every numbered node MUST include estimated time as an integer range in minutes
  - End of EVERY PART: show cumulative estimated total time
  - Final PART: also include final estimated total time
</time_total>

<output_format>
  ALL output MUST be in English.
  Output as plain Markdown with titles, subtitles, etc. — no XML tags in the output.
</output_format>
