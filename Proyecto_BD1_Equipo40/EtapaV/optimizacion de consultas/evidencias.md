# Optimización de Consultas (Índices y Planes de Ejecución)

**1. Definición y objetivo**
La optimización de consultas es el proceso de analizar y mejorar las sentencias SQL y la estructura física de la base de datos (como los índices) para que el motor recupere los datos de la forma más eficiente posible. El objetivo principal es minimizar el consumo de recursos (CPU, memoria, lecturas de disco) y reducir el tiempo de respuesta al usuario.

**2. Ventajas y aplicaciones**
*   **Velocidad de lectura:** Las consultas con cláusulas `WHERE`, `JOIN` u `ORDER BY` se ejecutan en fracciones de segundo.
*   **Concurrencia:** Al liberar recursos más rápido, el servidor puede atender a más cajeros simultáneamente sin colgarse.
*   **Aplicación:** Se aplica en tablas transaccionales de alto volumen, creando índices No Agrupados (Non-Clustered) sobre columnas que se usan frecuentemente para filtrar o buscar.

**3. Implementación en SQL Server Express**
En SQL Server Express, la optimización se implementa analizando el **Plan de Ejecución Estimado/Real** (Execution Plan) para detectar cuellos de botella (como los costosos *Table Scans*). Luego, se utilizan comandos de DDL (`CREATE INDEX`) para estructurar los datos y comandos de sesión (`SET STATISTICS IO ON`) para medir la mejora en las lecturas lógicas.

**4. Caso concreto aplicado al sistema de ventas del equipo**
En el sistema SUPER-EXPRESS, los gerentes ejecutan diariamente reportes de ventas filtrando la tabla `COMPRA` por rangos de fecha (`fecha_hora`) e integrando datos del `CLIENTE`. A medida que el supermercado acumule miles de tickets, buscar compras de un día específico forzará a SQL Server a escanear toda la tabla. La optimización consistirá en crear un índice no agrupado sobre la fecha de compra para transformar el escaneo masivo en una búsqueda directa (*Index Seek*).

**5. Script de demostración y resultados obtenidos**
El script adjunto (`scripts.sql`) ejecuta la consulta de reporte antes y después de aplicar el índice. 
*   **Resultado sin optimizar:** El motor realiza un *Clustered Index Scan* (lee toda la tabla) con un alto costo de I/O.
*   **Resultado optimizado:** El motor utiliza un *Index Seek* (va directo al dato), reduciendo las lecturas lógicas a un mínimo y mejorando el costo de la consulta drásticamente. Las capturas de este rendimiento se adjuntan en la carpeta de evidencias.
