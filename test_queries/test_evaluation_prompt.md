<name>
  skill_to_evaluation_exam_template
</name>

<version>
  v1.9.0
</version>

<description>
  Generates certification-style exam + evaluator guide as 3 separate documents: (1) exam-only PDF for the learner, (2) exam-only MD for the learner, (3) evaluation-guide-only MD for the AI evaluator.
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

<exercise_summary>
Summary of generated exercises (Power Query Junior)

Exercise: Global orders with Country dimension

Objective: build FactOrders joining Orders + Customers and standardize country with ISO2 (merge with country table).

Dataset (acquisition):

Northwind OData v4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Country list (ISO2) - https://raw.githubusercontent.com/datasets/country-list/main/data.csv

Deliverable: FactOrders with Country_Clean and ISO2 (target match-rate ≥ 90%).

Estimated time: 60-100 min

Exercise: Daily report consolidation from folder

Objective: combine multiple CSVs from Folder, add ReportDate from the file name, and type columns.

Dataset (acquisition):

Daily report 01-01-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-01-2021.csv

Daily report 01-02-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-02-2021.csv

Daily report 01-05-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-05-2021.csv

Deliverable: DailyReports_Combined with ReportDate as Date type (no nulls) and stable refresh.

Estimated time: 75-130 min

Exercise: DimAirport + clean routes (OpenFlights)

Objective: import .dat without headers, assign columns, clean ranges (lat/long) and type keys; create DimAirport and FactRoutes.

Dataset (acquisition):

OpenFlights airports.dat - https://raw.githubusercontent.com/jpatokal/openflights/master/data/airports.dat

OpenFlights routes.dat - https://raw.githubusercontent.com/jpatokal/openflights/master/data/routes.dat

Deliverable: DimAirport and FactRoutes without errors; non-null IDs in ≥ 99% and lat/long within valid range.

Estimated time: 60-110 min

Exercise: Folding vs no folding in Northwind (OData)

Objective: create two variants (Orders_Fold and Orders_NoFold), demonstrate the point where folding breaks, and justify the "production" version.

Dataset (acquisition):

Northwind OData v4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Deliverable: evidence of the step where "View Native Query" is no longer available + correct order of filters/columns.

Estimated time: 60-110 min

Exercise: Users + posts from API (JSONPlaceholder)

Objective: consume JSON, expand fields (including nested), type keys, and merge Posts ↔ Users.

Dataset (acquisition):

JSONPlaceholder Users - https://jsonplaceholder.typicode.com/users

JSONPlaceholder Posts - https://jsonplaceholder.typicode.com/posts

Deliverable: FactUserPosts with userId/postId as integers and complete merge (nulls ≈ 0).

Estimated time: 60-105 min

Exercise: Sales by product (Unpivot) with quality control

Objective: transform wide table to long (Product/Units), create Month, and validate SUM(Units) vs total_units.

Dataset (acquisition):

Company sales (CSV) - https://gist.githubusercontent.com/fishtai0/5975c7e38c8566b9b3144e45a99b65cf/raw/941a7ecbab78d6032393ba8eed6579a563f126d6/company-sales.csv

Deliverable: FactProductMonthly without errors + coherence verification by month.

Estimated time: 60-105 min

Exercise: LeadTimeDays + OnTimeFlag by region

Objective: type dates (locale if applicable), calculate LeadTimeDays, create OnTimeFlag, and summarize KPI by Region/Country.

Dataset (acquisition):

100 Sales Records (CSV) - https://gist.githubusercontent.com/denandreychuk/b9aa812f10e4b60368cff69c6384a210/raw/d24f715d9350d674b0b1bf494d82ccdf81de0647/100%2520Sales%2520Records.csv

Deliverable: KPI_LeadTime_ByRegion with AvgLeadTimeDays and OnTimeRate (0–1) without date errors.

Estimated time: 70-120 min

Exercise: Parameterized order date window

Objective: create pStartDate/pEndDate and filter by range without touching steps; demonstrate that the count changes when parameters change.

Dataset (acquisition):

100 Sales Records (CSV) - https://gist.githubusercontent.com/denandreychuk/b9aa812f10e4b60368cff69c6384a210/raw/d24f715d9350d674b0b1bf494d82ccdf81de0647/100%2520Sales%2520Records.csv

Deliverable: Sales_InRange with stable early filter and reproducible refresh.

Estimated time: 50-85 min
</exercise_summary>

<role>
  Persona: Instructional designer + certification examiner
  Mission: Design an evaluable certification-style exam for Power Query at Junior level in role in a business intelligence company with multinational clients in manufacturing and logistics, aligned to the content_summary and exercise_summary above.
</role>

<task>
  Generate a complete certification exam with evaluator guide, delivered as 3 separate documents:

  1. Exam PDF (for the learner): Contains ONLY the exam — instructions, questions, tasks, submission manifest. No answers, no rubrics, no evaluator notes.
  2. Exam MD (for the learner): Same content as the PDF but in Markdown format.
  3. Evaluation Guide MD (for the AI evaluator): Contains ONLY the evaluator guide — answer key, rubrics, scoring criteria, common mistakes, alignment map. No exam questions.

  The exam must test real understanding and practical ability in Power Query at Junior level for role in a business intelligence company with multinational clients in manufacturing and logistics. Every item must map to content_summary and exercise_summary. Use real datasets only. Include submission manifest for hands-on tasks.

  This step MUST produce exactly 3 separate downloadable files:
  1. exam_power_query.pdf — Exam only (for the learner). No answers, no rubrics.
  2. exam_power_query.md — Same exam content in Markdown (for the learner).
  3. evaluation_guide_power_query.md — Evaluation guide only (for the AI evaluator). No exam questions.
  Generate all 3 files and offer them for download before proceeding.
</task>

<skill_type_inference>
  Infer skill_type from Power Query + content_summary + exercise_summary:
  - If it requires implementation/coding/engineering → technical
  - If mostly decision-making/communication/process → non_technical
  - If both → mixed

  Question mix by skill_type:
  technical: MCQ 25-40%, Short answer 10-20%, Code completion 15-30%, Hands-on 25-45%, Use cases 0-10%
  non_technical: MCQ 20-35%, Short answer 15-25%, Use cases 35-55%, Hands-on 0-20%, Code completion 0%
  mixed: MCQ 20-35%, Short answer 10-20%, Code completion 10-20%, Use cases 15-30%, Hands-on 20-40%
</skill_type_inference>

<alignment>
  - Every item must explicitly map to (a) at least one concept/pitfall/constraint from content_summary AND (b) at least one coverage point from exercise_summary
</alignment>

<scoring>
  - Total score must normalize to exactly 100 points
  - Passing threshold: 70%
  - Each question/task must show points clearly
</scoring>

<dataset_constraints>
  NON-NEGOTIABLE: Every dataset MUST include a raw, working URL.

  URL requirements:
  - Just output the plain URL: https://example.com
  - Separate URLs from other text with blank lines
  - Do NOT use markdown [text](url) syntax
  - Do NOT wrap URLs in parentheses, quotes, or code blocks
  - Do NOT invent URLs - only real, accessible resources

  Prohibited patterns:
  - "Nota:", "Query:", or language tags before sources
  - Code blocks (mathematica, python, etc.) around URLs or descriptions
  - Parenthetical explanations on the same line as URL

  Dataset requirements: Free, legal, no login walls, max 200MB
  Acquisition methods: framework loader, registry loader, direct URL, public repo file, API export

  Blocking: If dataset required and no real acquisition method available, output BLOCKED and omit that item.
</dataset_constraints>

<code_completion_section>
  If technical or mixed skill_type:
  - Include 'Code completion' section
  - Provide incomplete code snippets with clearly marked gaps
  - Include official solution in evaluator guide
  - Rubric dimensions: Compiles/Executes, Correctness (output/effect), Minimum style (readability/names), Robustness (common errors/edge cases), Alignment to constraints (performance/security/privacy if applicable)
</code_completion_section>

<use_cases_section>
  If non_technical or mixed skill_type:
  - Include 'Use cases' section
  - Present realistic scenarios requiring judgment/decision-making
  - Define clear evaluation criteria
</use_cases_section>

<hands_on_tasks>
  For hands-on tasks requiring deliverables:
  - Include submission manifest specifying exactly which files candidate must upload
  - Provide dataset acquisition with real URL/loader
  - Define explicit rubric with scoring dimensions:
    * Functional correctness
    * Reproducibility (clean run)
    * Process quality/clarity
    * Maintainability (structure/names)
    * Relevant edge-case handling
    * Validations and quality controls
    * Alignment to context constraints
</hands_on_tasks>

<parts_system>
  Enabled: true
  Max items per part: 24
  Counting rule: Only numbered questions/tasks count

  Stop conditions:
  - Stop when max reached
  - Stop if overflow likely

  End text: Reply: continue

  Continuity:
  - Maintain numbering
  - Continue where left off
</parts_system>

<time_estimation>
  - Include total exam time estimate
  - Break down by section
</time_estimation>

<three_document_delivery>
  NON-NEGOTIABLE: You must produce exactly 3 separate, self-contained documents. Each document must be generated as a downloadable file.

  Document separation rule: The exam documents (1 and 2) must NEVER contain answers, rubrics, scoring criteria, or evaluator notes. The evaluation guide (3) must NEVER repeat the exam questions — it only references them by number.
</three_document_delivery>

<document_1_exam_pdf>
  Format: PDF file for download
  Filename: exam_power_query.pdf
  Audience: The learner/candidate (NO evaluator content)

  # Certification Exam: Power Query

  ## Candidate Instructions
  [Exam duration, passing score, submission requirements, allowed tools]

  ## Section 1: Multiple Choice (X points)
  [Questions with 4 options each — NO correct answer indicated]

  ## Section 2: Short Answer (X points)
  [Questions requiring brief written responses — NO evaluation criteria shown]

  ## Section 3: Code Completion (X points) [if technical/mixed]
  [Incomplete code with clearly marked gaps — NO solutions shown]

  ## Section 4: Use Cases (X points) [if non_technical/mixed]
  [Realistic scenarios requiring judgment — NO evaluation framework shown]

  ## Section 5: Hands-on Task (X points)
  [Real-world task with dataset, clear deliverables]

  ## Submission Manifest
  [Exact files to submit, folder structure, format requirements]

  Total: 100 points
  Passing: 70 points
</document_1_exam_pdf>

<document_2_exam_md>
  Format: Markdown file for download
  Filename: exam_power_query.md
  Audience: The learner/candidate (NO evaluator content)
  Content: Identical to Document 1 but in Markdown format instead of PDF.
</document_2_exam_md>

<document_3_evaluator_guide_md>
  Format: Markdown file for download
  Filename: evaluation_guide_power_query.md
  Audience: The AI evaluator or human grader (NO exam questions — reference by number only)

  # Evaluation Guide: Power Query

  ## Answer Key
  [MCQ correct answers by question number, short answer evaluation criteria, code completion official solutions]

  ## Hands-on Task Rubric
  [Detailed scoring rubric with point allocations per criterion]

  ## Common Mistakes to Watch
  [Based on exercise_summary weaknesses]

  ## Alignment Map
  [Each question/task number mapped to content_summary + exercise_summary concepts]
</document_3_evaluator_guide_md>

<submission_manifest>
  For hands-on tasks with multiple files (included in Documents 1 and 2):
  - List required files with exact names
  - Specify folder structure if applicable
  - Define file format requirements
  - State what must be included in each file
</submission_manifest>

<output_format>
  ALL output MUST be in English.
  Output as plain Markdown with titles, subtitles, etc. — no XML tags in the output.
</output_format>
