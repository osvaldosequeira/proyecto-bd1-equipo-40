--Factura comprobante de consumo.
SELECT 
    c.id_consumo AS Nro_Comprobante,
    c.fecha_hora AS Fecha,
    cl.dni AS DNI_Cliente,
    cl.nombre,
    cl.apellido,
    p.codigo_producto,
    p.descripcion AS Producto,
    dc.cantidad,
    dc.precio_unitario_historico AS Precio_Unitario,
    (dc.cantidad * dc.precio_unitario_historico) AS Subtotal,
    -- Subconsulta escalar correlacionada en el SELECT (calcula el total de la comanda)
    (
        SELECT SUM(dc2.cantidad * dc2.precio_unitario_historico)
        FROM detalle_consumo dc2
        WHERE dc2.id_consumo = c.id_consumo
    ) AS Total_Comprobante
FROM consumo c
INNER JOIN cliente cl ON c.id_cliente = cl.id_cliente
INNER JOIN detalle_consumo dc ON c.id_consumo = dc.id_consumo
INNER JOIN producto p ON dc.id_producto = p.id_producto
WHERE c.id_consumo = 3
ORDER BY dc.id_detalle;

--Reporte agregado por producto 

DECLARE @FechaDesde DATE = '2026-09-01';
DECLARE @FechaHasta DATE = '2026-10-31';

SELECT 
    p.codigo_producto,
    p.descripcion AS Producto,
    SUM(dc.cantidad) AS Unidades_Vendidas,
    SUM(dc.cantidad * dc.precio_unitario_historico) AS Total_Vendido,
    COUNT(DISTINCT c.id_consumo) AS Cantidad_Consumos
FROM consumo c
INNER JOIN detalle_consumo dc ON c.id_consumo = dc.id_consumo
INNER JOIN producto p ON dc.id_producto = p.id_producto
WHERE c.fecha_hora >= @FechaDesde
  AND c.fecha_hora < DATEADD(DAY, 1, @FechaHasta)
GROUP BY p.codigo_producto, p.descripcion
ORDER BY Total_Vendido DESC;
