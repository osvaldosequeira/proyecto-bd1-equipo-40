Restricciones de Integridad de Entidad (Claves Primarias / PRIMARY KEY)
Garantizan que cada fila de una tabla sea única y perfectamente identificable, prohibiendo que la clave primaria contenga valores duplicados o nulos.
Identificadores simples auto-incrementales: id_tipo_habitacion, id_habitacion, id_cliente, id_reserva, id_producto, id_consumo, id_detalle, id_mantenimiento e id_pago.
Identificador compuesto (1FN): (telefono, nro_dni) en la tabla cliente_telefono.

Restricciones de Integridad Referencial (Claves Foráneas / FOREIGN KEY)
Aseguran la consistencia lógica entre tablas relacionadas, evitando la existencia de registros "huérfanos" (por ejemplo, una reserva asignada a un cliente inexistente). En este esquema se definen las siguientes referencias: habitacion.id_tipo_habitacion a tipo_habitacion(id_tipo_habitacion); habitacion.id_hotel a hotel(id_hotel); reserva.id_cliente y reserva.id_habitacion a cliente(id_cliente) y habitacion(id_habitacion); consumo.id_cliente a cliente(id_cliente); detalle_consumo.id_consumo y detalle_consumo.id_producto a consumo(id_consumo) y producto(id_producto); mantenimiento.id_habitacion a habitacion(id_habitacion); y pago.id_reserva y pago.id_consumo a reserva(id_reserva) y consumo(id_consumo) como referencias opcionales. Se establecen las acciones referenciales ON DELETE RESTRICT y ON UPDATE CASCADE para proteger la integridad del sistema ante cualquier intento de modificación o eliminación de registros padre.

Restricciones de Unicidad (UNIQUE / Claves Candidatas)
Garantizan que determinados atributos no contengan valores duplicados dentro de la tabla, aunque no sean la clave primaria.
tipo_habitacion.nombre_tipo: Evita crear dos categorías de habitación con el mismo nombre.
habitacion.codigo_habitacion: Garantiza que el código identificador de la habitación sea único.
cliente.dni: Impide registrar dos huéspedes con el mismo número de documento.
producto.codigo_producto: Garantiza identificadores unívocos para cada artículo del menú. 
