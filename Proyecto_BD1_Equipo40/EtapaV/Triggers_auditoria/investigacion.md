TEMA 3: TRIGGERS DE AUDITORÍA

1. Definición y objetivo

Los triggers o disparadores son objetos de SQL Server que se ejecutan automáticamente cuando ocurre una operación determinada sobre una tabla, como INSERT, UPDATE o DELETE.

En el sistema gestor de reservas, estadías y consumos del hotel, los triggers permiten controlar determinadas operaciones y mantener un registro de los cambios realizados en la información.

El objetivo principal es implementar mecanismos de auditoría que permitan conocer qué modificaciones se realizaron, cuándo ocurrieron y qué usuario las efectuó. También permiten establecer controles para proteger información importante del sistema.

2. Ventajas y aplicaciones

La utilización de triggers de auditoría ofrece las siguientes ventajas:

Registro automático: las operaciones quedan registradas sin necesidad de que el usuario realice una acción adicional.

Control de modificaciones: permite identificar los valores anteriores y nuevos de los datos modificados.

Seguridad: ayuda a evitar operaciones que puedan afectar información importante.

Trazabilidad: permite consultar el historial de cambios y conocer cuándo se realizaron.

Integridad de la información: contribuye a conservar los registros necesarios para el funcionamiento del hotel.

En nuestro proyecto se aplican dos triggers: uno para registrar las operaciones realizadas sobre los productos del restaurante y otro para impedir la eliminación de reservas.

3. Implementación en SQL Server

3.1. Trigger de auditoría de productos

En el sistema del hotel, los productos del restaurante poseen información como código, descripción, precio unitario y stock.

Es importante conservar un registro de las operaciones realizadas sobre estos productos, especialmente cuando se modifican sus precios.

Para ello, se crea una tabla denominada auditoria_producto, donde se almacenan la fecha y hora de la operación, el usuario, el producto afectado, el tipo de operación y los precios anterior y nuevo.

4. Conclusión

La implementación de triggers en el sistema gestor del hotel permite automatizar controles y mejorar la seguridad de la información.

El trigger de auditoría de productos facilita el seguimiento de las operaciones realizadas, conservando los valores anteriores y nuevos de los precios. Por otra parte, el trigger aplicado a las reservas evita la eliminación de información que resulta importante para mantener el historial de los clientes y sus operaciones.

En conjunto, estas herramientas contribuyen a mejorar la trazabilidad, la integridad y el control de los datos almacenados en la base de datos del hotel.
