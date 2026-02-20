Resumen de ejercicios generados (Power Query Junior)

Ejercicio: Pedidos globales con dimensión País

Objetivo: construir FactOrders uniendo Orders + Customers y estandarizar país con ISO2 (merge con tabla de países).

Dataset (adquisición):

Northwind OData v4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Country list (ISO2) - https://raw.githubusercontent.com/datasets/country-list/main/data.csv

Entregable: FactOrders con Country_Clean e ISO2 (match-rate objetivo ≥ 90%).

Tiempo estimado: 60-100 min

Ejercicio: Consolidación de reportes diarios desde carpeta

Objetivo: combinar múltiples CSV desde Folder, agregar ReportDate desde el nombre del archivo y tipar columnas.

Dataset (adquisición):

Daily report 01-01-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-01-2021.csv

Daily report 01-02-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-02-2021.csv

Daily report 01-05-2021 - https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports/01-05-2021.csv

Entregable: DailyReports_Combined con ReportDate tipo Date (sin nulos) y refresh estable.

Tiempo estimado: 75-130 min

Ejercicio: DimAirport + rutas limpias (OpenFlights)

Objetivo: importar .dat sin headers, asignar columnas, limpiar rangos (lat/long) y tipar claves; crear DimAirport y FactRoutes.

Dataset (adquisición):

OpenFlights airports.dat - https://raw.githubusercontent.com/jpatokal/openflights/master/data/airports.dat

OpenFlights routes.dat - https://raw.githubusercontent.com/jpatokal/openflights/master/data/routes.dat

Entregable: DimAirport y FactRoutes sin errores; IDs no nulos en ≥ 99% y lat/long en rango válido.

Tiempo estimado: 60-110 min

Ejercicio: Folding vs no folding en Northwind (OData)

Objetivo: crear dos variantes (Orders_Fold y Orders_NoFold), demostrar el punto donde se rompe folding y justificar la versión “producción”.

Dataset (adquisición):

Northwind OData v4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Entregable: evidencia del paso donde “View Native Query” deja de estar disponible + orden correcto de filtros/columnas.

Tiempo estimado: 60-110 min

Ejercicio: Usuarios + posts desde API (JSONPlaceholder)

Objetivo: consumir JSON, expandir campos (incluye anidados), tipar claves y hacer merge Posts ↔ Users.

Dataset (adquisición):

JSONPlaceholder Users - https://jsonplaceholder.typicode.com/users

JSONPlaceholder Posts - https://jsonplaceholder.typicode.com/posts

Entregable: FactUserPosts con userId/postId enteros y merge completo (nulos ≈ 0).

Tiempo estimado: 60-105 min

Ejercicio: Ventas por producto (Unpivot) con control de calidad

Objetivo: transformar tabla wide a long (Product/Units), crear Month y validar SUM(Units) vs total_units.

Dataset (adquisición):

Company sales (CSV) - https://gist.githubusercontent.com/fishtai0/5975c7e38c8566b9b3144e45a99b65cf/raw/941a7ecbab78d6032393ba8eed6579a563f126d6/company-sales.csv

Entregable: FactProductMonthly sin errores + verificación de coherencia por mes.

Tiempo estimado: 60-105 min

Ejercicio: LeadTimeDays + OnTimeFlag por región

Objetivo: tipar fechas (locale si aplica), calcular LeadTimeDays, crear OnTimeFlag y resumir KPI por Region/Country.

Dataset (adquisición):

100 Sales Records (CSV) - https://gist.githubusercontent.com/denandreychuk/b9aa812f10e4b60368cff69c6384a210/raw/d24f715d9350d674b0b1bf494d82ccdf81de0647/100%2520Sales%2520Records.csv

Entregable: KPI_LeadTime_ByRegion con AvgLeadTimeDays y OnTimeRate (0–1) sin errores de fechas.

Tiempo estimado: 70-120 min

Ejercicio: Ventana de pedidos por fecha (parametrizada)

Objetivo: crear pStartDate/pEndDate y filtrar por rango sin tocar pasos; demostrar que cambia el conteo al cambiar parámetros.

Dataset (adquisición):

100 Sales Records (CSV) - https://gist.githubusercontent.com/denandreychuk/b9aa812f10e4b60368cff69c6384a210/raw/d24f715d9350d674b0b1bf494d82ccdf81de0647/100%2520Sales%2520Records.csv

Entregable: Sales_InRange con filtro temprano estable y refresh reproducible.

Tiempo estimado: 50-85 min