
# Pruebas y Validación

El script DDL y DML fue sometido a un proceso de Aseguramiento de Calidad (QA) y testeado en el motor Microsoft SQL Server.

* **Ejecución limpia:** La compilación del entorno físico se realizó exitosamente sin arrojar errores de sintaxis ni bloqueos de motor.
* **Validación de poblado:** Se comprobó la correcta inserción de datos masiva, superando la cuota mínima exigida con más de 8 a 10 registros lógicos y coherentes por tabla.
* **Validación de restricciones:** Se comprobó que el motor respeta la integridad referencial y frena cualquier intento de ingreso de datos anómalos o fechas incongruentes.
* **Prueba de salida de datos:** La consulta de comprobación `SELECT * FROM pago;` devolvió la grilla con los 10 registros transaccionales cargados correctamente, confirmando que las claves foráneas enlazan perfectamente los módulos de consumo y reservas.

