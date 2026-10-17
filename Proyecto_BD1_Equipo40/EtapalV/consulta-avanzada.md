# Consulta de Negocio Avanzada

```sql
SELECT 
    cl.id_cliente,
    cl.dni,
    cl.nombre,
    cl.apellido,
    COUNT(DISTINCT c.id_consumo) AS Cantidad_Consumos,
    SUM(dc.cantidad * dc.precio_unitario_historico) AS Total_Consumido
FROM cliente cl
INNER JOIN consumo c ON cl.id_cliente = c.id_cliente
INNER JOIN detalle_consumo dc ON c.id_consumo = dc.id_consumo
WHERE c.fecha_hora >= '2026-09-01'
  AND c.fecha_hora < '2026-11-01'
GROUP BY cl.id_cliente, cl.dni, cl.nombre, cl.apellido
HAVING SUM(dc.cantidad * dc.precio_unitario_historico) > 30000
   AND cl.id_cliente IN (
       SELECT c2.id_cliente
       FROM consumo c2
       WHERE c2.fecha_hora >= '2026-09-01'
         AND c2.fecha_hora < '2026-11-01'
       GROUP BY c2.id_cliente
       HAVING COUNT(*) >= 2
   )
ORDER BY Total_Consumido DESC;
