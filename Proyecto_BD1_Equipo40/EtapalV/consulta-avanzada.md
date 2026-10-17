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


SELECT 
    r.id_reserva,
    cl.dni AS DNI_Cliente,
    cl.nombre + ' ' + cl.apellido AS Cliente,
    h.codigo_habitacion,
    p.monto AS Monto_Pagado
FROM reserva r
INNER JOIN cliente cl ON r.id_cliente = cl.id_cliente
INNER JOIN habitacion h ON r.id_habitacion = h.id_habitacion
INNER JOIN pago p ON r.id_reserva = p.id_reserva
WHERE p.monto > (
    SELECT AVG(p2.monto)
    FROM pago p2
    WHERE p2.id_reserva IS NOT NULL
)
ORDER BY p.monto DESC;

SELECT 
    cl.id_cliente,
    cl.dni,
    cl.nombre,
    cl.apellido
FROM cliente cl
WHERE EXISTS (
    SELECT 1 
    FROM consumo c 
    WHERE c.id_cliente = cl.id_cliente
)
ORDER BY cl.apellido, cl.nombre;
