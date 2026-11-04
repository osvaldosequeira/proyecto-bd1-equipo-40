# Optimización de Consultas (Índices y Planes de Ejecución)

**1. Definición y objetivo**
La optimización de consultas es el proceso de analizar y reestructurar el acceso a los datos para que el motor SQL resuelva las peticiones consumiendo la menor cantidad de recursos posibles (CPU, memoria, disco). El objetivo central es reducir las lecturas lógicas transformando escaneos masivos de tablas en búsquedas directas mediante índices.

**2. Ventajas y aplicaciones**
*   **Velocidad:** Reduce drásticamente el tiempo de respuesta en consultas con cláusulas `WHERE` sobre columnas muy consultadas.
*   **Escalabilidad:** Permite que el sistema de reservas del hotel siga respondiendo rápido aunque acumule años de historial.
*   **Aplicación:** Se aplica en tablas transaccionales de alto volumen, utilizando índices No Agrupados (Non-Clustered) con columnas incluidas (INCLUDE) para cubrir la consulta y evitar saltos adicionales a la tabla base (Key Lookups).

**3. Implementación en SQL Server Express**
Se utiliza la herramienta del **Plan de Ejecución** de SQL Server Management Studio para detectar operaciones costosas (como *Clustered Index Scan*). Mediante el comando de sesión `SET STATISTICS IO ON`, se audita la cantidad de "lecturas lógicas" (páginas de datos leídas de la memoria) antes y después de aplicar el comando `CREATE NONCLUSTERED INDEX`.

**4. Caso concreto aplicado al sistema del hotel**
En el sistema de gestión del hotel, el área de recepción ejecuta constantemente reportes para ver qué clientes ingresan en un rango de fechas específico. Al consultar la tabla `reserva` filtrando por la columna `fecha_entrada`, el motor se ve obligado a leer secuencialmente toda la tabla. La optimización consiste en aplicar un índice sobre las fechas de entrada, cubriendo los IDs foráneos para acelerar los JOINs con las tablas `cliente` y `habitacion`.

**5. Script de demostración y resultados obtenidos**
El script adjunto (`scripts.sql`) ejecuta el reporte de ocupación antes y después de la creación del índice.
*   **Antes:** El motor realiza un *Clustered Index Scan*, leyendo toda la tabla de reservas con un alto costo de I/O.
*   **Después:** El motor ejecuta un *Index Seek*, yendo directo a las fechas solicitadas. Las lecturas lógicas se reducen casi a cero. Las evidencias visuales del rendimiento (Plan de Ejecución y Métricas de E/S) se encuentran en la carpeta de evidencias.
