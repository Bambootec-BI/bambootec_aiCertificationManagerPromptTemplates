Práctica de ingesta y combinación (OData + CSV) — Objetivo práctico: crear una tabla “ready-to-model” combinando clientes con un catálogo de países para uso multinacional.

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

Si hay países que no “matchean”, normaliza mayúsculas/espacios antes del Merge

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

Captura de la tabla agregada y del paso Group By en “Pasos aplicados”

Pistas (opcional):

Si ShippedDate tiene nulos, filtra nulos antes de calcular el periodo

Tiempo estimado: 60-110 minutos