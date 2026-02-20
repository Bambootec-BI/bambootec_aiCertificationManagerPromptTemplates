<name>
  skill_to_evaluation_exam_template
</name>

<version>
  v1.9.0
</version>

<description>
  Generates certification-style exam + evaluator guide as 3 separate documents: (1) exam-only PDF for the learner, (2) exam-only MD for the learner, (3) evaluation-guide-only MD for the AI evaluator. Instructions in English, output controlled by language_policy.
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
Práctica de ingesta y combinación (OData + CSV) — Objetivo práctico: crear una tabla "ready-to-model" combinando clientes con un catálogo de países para uso multinacional.

Micro-skills:

Conectores, credenciales y navegación

Merge (join) y tipado consistente

Tiempo estimado: 60-110 minutos

1.1 Conectar y combinar orígenes — Enfoque de práctica: ingestar OData + CSV y entregar una dimensión limpia para reporteo.

Tiempo estimado: 60-110 minutos
1.1.1 Ejercicio: DimCustomerCountry lista para BI

Enunciado:

Construye una tabla que combine clientes (Northwind) con un catálogo de países (ISO) para estandarizar el país en proyectos multinacionales.

Objetivo verificable:

Entregar DimCustomerCountry con CountryName y CountryCode (ISO) sin errores de tipo.

Dataset (adquisición):

Northwind OData V4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Country list (CSV) - https://datahub.io/core/country-list/r/data.csv

Prerrequisitos:

Power BI Desktop o Excel (Power Query)

Setup (copiar/pegar):

Crear 2 consultas: Customers desde OData y Countries desde el CSV

Tareas:

Seleccionar columnas mínimas en Customers (CustomerID, CompanyName, Country)

Tipar columnas (CustomerID texto, CompanyName texto, Country texto)

En Countries, renombrar columnas a CountryName y CountryCode

Hacer Merge Customers.Country = Countries.CountryName (Left join)

Expandir CountryCode y validar nulos/errores

Comando de ejecución:

Power BI Desktop: Inicio > Actualizar, luego Cerrar y aplicar

Salida esperada:

Tabla DimCustomerCountry con columnas CustomerID, CompanyName, CountryName, CountryCode

Árbol de archivos esperado:

Un PBIX/XLSX con 2 consultas staging y 1 consulta final (DimCustomerCountry)

Definiciones:

Dimensión: tabla descriptiva usada para segmentar/filtrar métricas

Pruebas de aceptación (cómo probar) — Pass/Fail:

PASS si CountryCode existe para la mayoría de filas y no hay errores de tipo en la carga

Evidencia:

Captura del panel de consultas mostrando las 3 consultas y vista previa de DimCustomerCountry

Pistas (opcional):

Si hay países que no "matchean", normaliza mayúsculas/espacios antes del Merge

Tiempo estimado: 60-110 minutos

Práctica de limpieza, tipado y calidad — Objetivo práctico: limpiar un dataset operativo (texto + coordenadas) y dejarlo consistente para merges y KPIs.

Micro-skills:

Limpieza de texto (Trim/Clean) y tipado seguro

Perfilado de columnas (calidad/distribución)

Tiempo estimado: 50-90 minutos

2.1 Estandarizar y validar datos de referencia — Enfoque de práctica: crear una dimensión limpia y auditable para logística.

Tiempo estimado: 50-90 minutos
2.1.1 Ejercicio: DimAirport limpia y tipada

Enunciado:

Limpia y tipa un catálogo de aeropuertos para que pueda usarse como referencia en análisis logístico.

Objetivo verificable:

Entregar DimAirport filtrada y tipada sin errores, con texto estandarizado.

Dataset (adquisición):

Airport codes (CSV) - https://datahub.io/core/airport-codes/r/airport-codes.csv

Prerrequisitos:

Power BI Desktop o Excel (Power Query)

Setup (copiar/pegar):

Crear 1 consulta desde Web/CSV y habilitar herramientas de perfilado

Tareas:

Filtrar a un subconjunto útil (por ejemplo: type = large_airport o medium_airport)

Aplicar Trim/Clean en name y municipality (o equivalentes)

Tipar latitude_deg y longitude_deg como número decimal

Revisar Column quality/distribution y corregir nulos críticos (si aplica) o marcarlos

Comando de ejecución:

Power BI Desktop: Transformar datos > Cerrar y aplicar

Salida esperada:

Tabla DimAirport con texto limpio y coordenadas numéricas (sin errores de conversión)

Árbol de archivos esperado:

Un PBIX/XLSX con 1 consulta staging y 1 consulta final (DimAirport)

Definiciones:

Perfilado: revisión de nulos/errores/distribución para detectar problemas antes del modelado

Pruebas de aceptación (cómo probar) — Pass/Fail:

PASS si latitude_deg/longitude_deg están tipadas como número y Column quality no muestra errores

Evidencia:

Captura de Column quality + vista previa de DimAirport con tipos correctos

Pistas (opcional):

Si aparece error de tipo, revisa si hay valores no numéricos o separadores regionales

Tiempo estimado: 50-90 minutos

Práctica de agregación operativa (Group By + fechas) — Objetivo práctico: generar una tabla agregada lista para KPI (por periodo y responsable logístico).

Micro-skills:

Group By y métricas agregadas

Preparación de columnas de fecha para periodización

Tiempo estimado: 60-110 minutos

3.1 Construir una tabla de métricas por periodo — Enfoque de práctica: resumir órdenes por mes y transportista para un dashboard operativo.

Tiempo estimado: 60-110 minutos
3.1.1 Ejercicio: KPI de envíos por mes y shipper

Enunciado:

Genera una tabla agregada de órdenes enviadas por mes y por transportista (shipper) desde Northwind.

Objetivo verificable:

Entregar una tabla KPI_Shipments_Month con conteos y una fecha de periodo consistente.

Dataset (adquisición):

Northwind OData V4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Prerrequisitos:

Power BI Desktop o Excel (Power Query)

Setup (copiar/pegar):

Crear consultas Orders y Shippers desde OData

Tareas:

En Orders, conservar OrderID, ShippedDate, ShipVia

Crear columna de periodo (por ejemplo: inicio de mes desde ShippedDate)

Merge Orders.ShipVia = Shippers.ShipperID para traer CompanyName

Group By por Periodo y ShipperName con Count Rows (órdenes enviadas)

Comando de ejecución:

Power BI Desktop: Inicio > Actualizar, luego Cerrar y aplicar

Salida esperada:

Tabla KPI_Shipments_Month con Periodo, ShipperName, OrdersShipped (conteo)

Árbol de archivos esperado:

Un PBIX/XLSX con 2 staging (Orders, Shippers) y 1 final (KPI_Shipments_Month)

Definiciones:

Group By: operación para agrupar filas por claves y calcular agregaciones

Pruebas de aceptación (cómo probar) — Pass/Fail:

PASS si no hay errores de tipo en fechas y OrdersShipped es numérico y >0 en varios periodos

Evidencia:

Captura de la tabla agregada y del paso Group By en "Pasos aplicados"

Pistas (opcional):

Si ShippedDate tiene nulos, filtra nulos antes de calcular el periodo

Tiempo estimado: 60-110 minutos
</content_summary>

<exercise_summary>
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

Objetivo: crear dos variantes (Orders_Fold y Orders_NoFold), demostrar el punto donde se rompe folding y justificar la versión "producción".

Dataset (adquisición):

Northwind OData v4 - https://services.odata.org/V4/Northwind/Northwind.svc/

Entregable: evidencia del paso donde "View Native Query" deja de estar disponible + orden correcto de filtros/columnas.

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
</exercise_summary>

<language_policy>
  Translate user input into English. Think in English. Only translate to Spanish for the output.

  Instruction language: English
  Output language: Spanish
  Exceptions: proper names, commonly used names, code blocks
</language_policy>

<role>
  Persona: Instructional designer + certification examiner
  Mission: Design an evaluable certification-style exam for Power Query at Junior level in rol en una empresa de inteligencia de negocios con clientes multinacionales en las areas de manufactura y logistica, aligned to the content_summary and exercise_summary above.
</role>

<task>
  Generate a complete certification exam with evaluator guide, delivered as 3 separate documents:

  1. Exam PDF (for the learner): Contains ONLY the exam — instructions, questions, tasks, submission manifest. No answers, no rubrics, no evaluator notes.
  2. Exam MD (for the learner): Same content as the PDF but in Markdown format.
  3. Evaluation Guide MD (for the AI evaluator): Contains ONLY the evaluator guide — answer key, rubrics, scoring criteria, common mistakes, alignment map. No exam questions.

  The exam must test real understanding and practical ability in Power Query at Junior level for rol en una empresa de inteligencia de negocios con clientes multinacionales en las areas de manufactura y logistica. Every item must map to content_summary and exercise_summary. Use real datasets only. Include submission manifest for hands-on tasks.

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

  # Examen de Certificación: Power Query

  ## Instrucciones para el candidato
  [Exam duration, passing score, submission requirements, allowed tools]

  ## Sección 1: Opción múltiple (X puntos)
  [Questions with 4 options each — NO correct answer indicated]

  ## Sección 2: Respuesta corta (X puntos)
  [Questions requiring brief written responses — NO evaluation criteria shown]

  ## Sección 3: Completar código (X puntos) [if technical/mixed]
  [Incomplete code with clearly marked gaps — NO solutions shown]

  ## Sección 4: Casos de uso (X puntos) [if non_technical/mixed]
  [Realistic scenarios requiring judgment — NO evaluation framework shown]

  ## Sección 5: Tarea práctica (X puntos)
  [Real-world task with dataset, clear deliverables]

  ## Manifiesto de entrega
  [Exact files to submit, folder structure, format requirements]

  Total: 100 puntos
  Aprobación: 70 puntos
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

  # Guía de Evaluación: Power Query

  ## Clave de respuestas
  [MCQ correct answers by question number, short answer evaluation criteria, code completion official solutions]

  ## Rúbrica de tarea práctica
  [Detailed scoring rubric with point allocations per criterion]

  ## Errores comunes a vigilar
  [Based on exercise_summary weaknesses]

  ## Mapa de alineación
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
  ALL bullet content and descriptions MUST be written in Spanish. Only source titles and proper names may remain in their original language.
  Output as plain Markdown with titles, subtitles, etc. — no XML tags in the output.
</output_format>
