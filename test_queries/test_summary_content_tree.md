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
