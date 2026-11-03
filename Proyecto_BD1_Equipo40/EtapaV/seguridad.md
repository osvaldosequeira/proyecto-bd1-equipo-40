1. Definición y Objetivo
Definición:

La seguridad en SQL Server comprende el conjunto de mecanismos de autenticación y autorización orientados a proteger el motor de base de datos. Se estructura mediante:

Inicios de sesión (Logins): Credenciales a nivel de instancia que validan el acceso al servidor.

Usuarios de base de datos (Users) y Roles: Objetos internos que permiten conceder o restringir permisos específicos sobre los componentes de la base de datos del hotel.

Objetivo:

Garantizar la confidencialidad, disponibilidad e integridad de los datos de los huéspedes, reservas y operaciones del hotel. Su propósito central es aplicar el principio de mínimo privilegio, asegurando que cada usuario u operador del sistema posea únicamente los permisos indispensables para su labor, previniendo alteraciones indebidas o accesos no autorizados.

2. Ventajas y Aplicaciones
Ventajas:

Protección de información sensible: Resguarda datos personales de clientes (como DNI, teléfonos) y registros financieros de pagos.

Mitigación de errores operativos: Previene la ejecución accidental de consultas destructivas (UPDATE o DELETE) de forma directa sobre tablas críticas como reserva o pago.

Encapsulamiento de la lógica de negocio: Restringe el acceso transaccional directo a las tablas, obligando a los usuarios a operar mediante procedimientos almacenados validados.

Administración escalable: Facilita la gestión de permisos al agrupar usuarios en roles funcionales (ej. recepcionistas, auditores, personal de sistemas).

Aplicaciones en el Sistema de Hotel:

Otorgar privilegios de administración global al personal técnico o de infraestructura.

Restringir al personal de recepción para que registre reservas y pasajeros mediante procedimientos seguros, sin permitirles modificar el esquema de tablas o precios arbitrariamente.

Habilitar roles de solo lectura enfocados en la consulta del estado de las habitaciones o listados de productos para el personal de servicio.

3. Implementación en SQL Server Express
En esta sección se detalla el script utilizado para configurar la seguridad a nivel de usuarios y roles dentro del motor SQL Server Express, aplicando los comandos de creación de inicios de sesión, usuarios, asignación de roles y permisos específicos mediante

-- ==========================================
-- 1. PERMISOS A NIVEL DE USUARIOS Y PROCEDIMIENTOS
-- ==========================================

-- Crear inicios de sesión (Logins) en el servidor
CREATE LOGIN usuario_admin_hotel WITH PASSWORD = 'Admin123*';
CREATE LOGIN usuario_recepcion WITH PASSWORD = 'Recepcion123*';
GO

USE HotelDB;
GO

-- Crear usuarios de base de datos asociados
CREATE USER usuario_admin_hotel FOR LOGIN usuario_admin_hotel;
CREATE USER usuario_recepcion FOR LOGIN usuario_recepcion;
GO

-- Asignar privilegios completos al administrador
ALTER ROLE db_owner ADD MEMBER usuario_admin_hotel;
GO

-- Otorgar permisos limitados de lectura y ejecución específica al recepcionista
GRANT SELECT ON dbo.cliente TO usuario_recepcion;
GRANT EXECUTE ON dbo.sp_RegistrarPago TO usuario_recepcion;
GO


-- ==========================================
-- 2. PERMISOS A NIVEL DE ROLES PERSONALIZADOS
-- ==========================================

-- Crear logins para la demostración de roles
CREATE LOGIN usuario_con_rol WITH PASSWORD = 'Rol123*';
CREATE LOGIN usuario_sin_rol WITH PASSWORD = 'Rol456*';
GO

USE HotelDB;
GO

CREATE USER usuario_con_rol FOR LOGIN usuario_con_rol;
CREATE USER usuario_sin_rol FOR LOGIN usuario_sin_rol;
GO

-- Crear un rol personalizado para la consulta de habitaciones
CREATE ROLE rol_consulta_habitaciones;
GO

-- Otorgar permisos sobre la tabla al rol creado
GRANT SELECT ON dbo.habitacion TO rol_consulta_habitaciones;
GO

-- Incorporar al usuario autorizado dentro del rol
ALTER ROLE rol_consulta_habitaciones ADD MEMBER usuario_con_rol;
GO

3. Implementación en SQL Server Express
En esta sección se detalla el script utilizado para configurar la seguridad a nivel de usuarios y roles dentro del motor SQL Server Express, aplicando los comandos de creación de inicios de sesión, usuarios, asignación de roles y permisos específicos mediante

4. Caso concreto aplicado al sistema de ventas del equipo
En el sistema de gestión hotelera (HotelDB), la seguridad se implementa para segregar las responsabilidades operativas y financieras entre los distintos perfiles de usuarios:
Personal de Recepción (usuario_recepcion / usuario_con_rol): Necesita consultar la disponibilidad de las instalaciones (habitacion, cliente) y registrar transacciones cotidianas. Mediante la asignación de roles específicos (rol_consulta_habitaciones) y el uso de procedimientos almacenados (como sp_RegistrarPago), el recepcionista puede operar el sistema de manera eficiente pero sin tener privilegios de modificación directa sobre las tablas estructurales o financieras de la base de datos.
Personal Administrador (usuario_admin_hotel): Posee control total sobre el motor de base de datos (rol db_owner) para realizar tareas de mantenimiento, gestión de esquemas, auditorías y control global del sistema de reservas y pagos.
Principio de aplicación: Esto evita que un usuario sin los permisos adecuados altere precios, elimine registros críticos de pagos o corrompa la integridad de la información del hotel.

