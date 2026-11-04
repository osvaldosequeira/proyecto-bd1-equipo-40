-- ===================================================================
-- TEMA TÉCNICO: OPTIMIZACIÓN DE CONSULTAS
-- Caso: Búsqueda intensiva de reservas por rango de fechas de entrada
-- ===================================================================

-- Activamos las estadísticas para medir la mejora de rendimiento
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
GO

-- ===================================================================
-- PASO 1: CONSULTA SIN OPTIMIZAR
-- Sin índice en fecha_entrada, SQL Server lee toda la tabla de reservas.
-- ===================================================================
PRINT '--- EJECUCIÓN SIN OPTIMIZAR ---';

SELECT 
    r.id_reserva,
    r.fecha_entrada,
    r.fecha_salida,
    c.nombre,
    c.apellido,
    h.codigo_habitacion
FROM reserva r
INNER JOIN cliente c ON r.id_cliente = c.id_cliente
INNER JOIN habitacion h ON r.id_habitacion = h.id_habitacion
WHERE r.fecha_entrada >= '2026-09-01' 
  AND r.fecha_entrada <= '2026-10-31';
GO

-- ===================================================================
-- PASO 2: IMPLEMENTACIÓN DE LA OPTIMIZACIÓN
-- Creamos un índice "cubierto" para que el motor no tenga que ir a 
-- buscar los otros campos a la tabla original (Key Lookup).
-- ===================================================================
PRINT '--- CREANDO ÍNDICE DE OPTIMIZACIÓN ---';

CREATE NONCLUSTERED INDEX IX_Reserva_FechaEntrada 
ON reserva (fecha_entrada)
INCLUDE (fecha_salida, id_cliente, id_habitacion);
GO

-- ===================================================================
-- PASO 3: CONSULTA OPTIMIZADA
-- El motor ahora usará un "Index Seek" directamente en el árbol B.
-- ===================================================================
PRINT '--- EJECUCIÓN OPTIMIZADA ---';

SELECT 
    r.id_reserva,
    r.fecha_entrada,
    r.fecha_salida,
    c.nombre,
    c.apellido,
    h.codigo_habitacion
FROM reserva r
INNER JOIN cliente c ON r.id_cliente = c.id_cliente
INNER JOIN habitacion h ON r.id_habitacion = h.id_habitacion
WHERE r.fecha_entrada >= '2026-09-01' 
  AND r.fecha_entrada <= '2026-10-31';
GO

-- Apagamos las métricas
SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
