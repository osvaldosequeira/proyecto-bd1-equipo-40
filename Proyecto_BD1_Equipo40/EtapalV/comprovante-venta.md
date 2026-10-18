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
