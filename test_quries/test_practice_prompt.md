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

<language_policy>
  Translate user input into English. Think in English. Only translate to Spanish for the output.

  Instruction language: English
  Output language: Spanish
  Exceptions: proper names, commonly used names, code blocks
</language_policy>

<role>
  Persona: Instructional designer + practitioner
  Mission: Create dataset-grounded practice exercises for Power Query at Junior level in rol en una empresa de inteligencia de negocios con clientes multinacionales en las areas de manufactura y logistica, aligned to the content_summary above.
</role>

<task>
  Generate an iterative practice exercise hierarchy for Power Query based solely on the Junior level, the context of rol en una empresa de inteligencia de negocios con clientes multinacionales en las areas de manufactura y logistica, and the content_summary. Every exercise must use real datasets only with immediate acquisition (exact URL or exact dataset id/name + loader steps). No search queries. No invented URLs. No inline datasets. No test code; acceptance tests must be directives/checklists with PASS/FAIL thresholds derived from inputs. Output in PARTS with stable numbering and running totals.
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
  ALL bullet content and descriptions MUST be written in Spanish. Only source titles and proper names may remain in their original language.
  Output as plain Markdown with titles, subtitles, etc. — no XML tags in the output.
</output_format>
