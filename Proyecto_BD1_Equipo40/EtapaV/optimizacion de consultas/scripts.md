-- ===================================================================
-- TEMA TÉCNICO: OPTIMIZACIÓN DE CONSULTAS
-- Caso: Reporte de facturación diaria en SUPER-EXPRESS
-- ===================================================================

-- Activamos las estadísticas de tiempo y lecturas para medir el rendimiento
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
GO

-- ===================================================================
-- PASO 1: CONSULTA SIN OPTIMIZAR (Simulando el cuello de botella)
-- Al no tener índice en fecha_hora, SQL Server hace un "Table Scan".
-- ===================================================================
PRINT '--- EJECUCIÓN SIN OPTIMIZAR ---';

SELECT 
    c.id_compra,
    c.fecha_hora,
    c.costo,
    p.nombre,
    p.apellido
FROM COMPRA c
INNER JOIN CLIENTE cli ON c.cod_cliente = cli.cod_cliente
INNER JOIN PERSONA p ON cli.id_persona = p.id_persona
WHERE c.fecha_hora >= '2026-10-01 00:00:00' 
  AND c.fecha_hora <= '2026-10-31 23:59:59';
GO

-- ===================================================================
-- PASO 2: IMPLEMENTACIÓN DE LA OPTIMIZACIÓN (Creación del Índice)
-- Creamos un índice No Agrupado para acelerar la búsqueda por fechas
-- ===================================================================
PRINT '--- CREANDO ÍNDICE DE OPTIMIZACIÓN ---';

CREATE NONCLUSTERED INDEX IX_Compra_FechaHora 
ON COMPRA (fecha_hora)
INCLUDE (costo, cod_cliente); -- Hacemos el índice "cubierto" para evitar lecturas extra
GO

-- ===================================================================
-- PASO 3: CONSULTA OPTIMIZADA (Verificando la mejora)
-- Ahora el motor usará un "Index Seek" directamente en el árbol B.
-- ===================================================================
PRINT '--- EJECUCIÓN OPTIMIZADA ---';

-- Ejecutamos exactamente la misma consulta para comparar el rendimiento
SELECT 
    c.id_compra,
    c.fecha_hora,
    c.costo,
    p.nombre,
    p.apellido
FROM COMPRA c
INNER JOIN CLIENTE cli ON c.cod_cliente = cli.cod_cliente
INNER JOIN PERSONA p ON cli.id_persona = p.id_persona
WHERE c.fecha_hora >= '2026-10-01 00:00:00' 
  AND c.fecha_hora <= '2026-10-31 23:59:59';
GO

-- Desactivamos las métricas
SET STATISTICS IO OFF;
SET STATISTICS TIME OFF;
GO
