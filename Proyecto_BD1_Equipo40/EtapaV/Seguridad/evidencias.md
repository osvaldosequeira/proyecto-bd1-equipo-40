### 3. En el archivo `evidencias.md`

```markdown
# Caso Concreto Aplicado al Sistema del Equipo y Evidencias

En el sistema de gestión hotelera (`HotelDB`), la seguridad se implementa para segregar las responsabilidades operativas y financieras entre los distintos perfiles de usuarios:

* **Personal de Recepción (`usuario_recepcion` / `usuario_con_rol`):** Necesita consultar la disponibilidad de las instalaciones (`habitacion`, `cliente`) y registrar transacciones cotidianas. Mediante la asignación de roles específicos (`rol_consulta_habitaciones`) y el uso de procedimientos almacenados (como `sp_RegistrarPago`), el recepcionista puede operar el sistema de manera eficiente pero sin tener privilegios de modificación directa sobre las tablas estructurales o financieras de la base de datos.
* **Personal Administrador (`usuario_admin_hotel`):** Posee control total sobre el motor de base de datos (rol `db_owner`) para realizar tareas de mantenimiento, gestión de esquemas, auditorías y control global del sistema de reservas y pagos.

> **Principio de aplicación:** Esto evita que un usuario sin los permisos adecuados altere precios, elimine registros críticos de pagos o corrompa la integridad de la información del hotel.

---
## Pruebas de Verificación y Permisos

### Prueba 1: Inserción y consulta permitida por rol de Recepción
Se verifica que el usuario `usuario_recepcion` puede consultar y registrar datos operativos correctamente:

Ejecución Correcta con Permisos

### Prueba 2: Bloqueo de acción no autorizada (Denegación de permisos)
Se intenta modificar la estructura o eliminar registros sin privilegios desde la cuenta del recepcionista, generando la denegación explícita por el motor de SQL Server:
