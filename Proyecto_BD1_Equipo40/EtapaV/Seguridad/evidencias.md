# Investigación Teórica - Modelo de Seguridad en SQL Server

## 1. Definición y Objetivo

### Definición:
La seguridad en SQL Server comprende el conjunto de mecanismos de autenticación y autorización orientados a proteger el motor de base de datos. Se estructura mediante:

* **Inicios de sesión (Logins):** Credenciales a nivel de instancia que validan el acceso al servidor.
* **Usuarios de base de datos (Users) y Roles:** Objetos internos que permiten conceder o restringir permisos específicos sobre los componentes de la base de datos del hotel.

### Objetivo:
Garantizar la confidencialidad, disponibilidad e integridad de los datos de los huéspedes, reservas y operaciones del hotel. Su propósito central es aplicar el **principio de mínimo privilegio**, asegurando que cada usuario u operador del sistema posea únicamente los permisos indispensables para su labor, previniendo alteraciones indebidas o accesos no autorizados.

## 2. Ventajas y Aplicaciones

### Ventajas:
* **Protección de información sensible:** Resguarda datos personales de clientes (como DNI, teléfonos) y registros financieros de pagos.
* **Mitigación de errores operativos:** Previene la ejecución accidental de consultas destructivas (`UPDATE` o `DELETE`) de forma directa sobre tablas críticas como `reserva` o `pago`.
* **Encapsulamiento de la lógica de negocio:** Restringe el acceso transaccional directo a las tablas, obligando a los usuarios a operar mediante procedimientos almacenados validados.
* **Administración escalable:** Facilita la gestión de permisos al agrupar usuarios en roles funcionales (ej. recepcionistas, auditores, personal de sistemas).

### Aplicaciones en el Sistema de Hotel:
* Otorgar privilegios de administración global al personal técnico o de infraestructura.
* Restringir al personal de recepción para que registre reservas y pasajeros mediante procedimientos seguros, sin permitirles modificar el esquema de tablas o precios arbitrariamente.
* Habilitar roles de solo lectura enfocados en la consulta del estado de las habitaciones o listados de productos para el personal de servicio.
