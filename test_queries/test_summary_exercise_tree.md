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
