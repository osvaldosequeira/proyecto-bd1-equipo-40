# Implementación de Seguridad en SQL Server Express

En esta sección se detalla el script utilizado para configurar la seguridad a nivel de usuarios y roles dentro del motor SQL Server Express, aplicando los comandos de creación de inicios de sesión, usuarios, asignación de roles y permisos específicos.

```sql
USE HotelDB;
GO

-- ============================================================================
-- 1. CREACIÓN DE ROLES PERSONALIZADOS
-- ============================================================================
CREATE ROLE rol_consulta_habitaciones;
CREATE ROLE rol_recepcion;
GO

-- ============================================================================
-- 2. ASIGNACIÓN DE PERMISOS A ROLES
-- ============================================================================
-- Permisos de solo lectura para consulta de habitaciones y clientes
GRANT SELECT ON dbo.habitacion TO rol_consulta_habitaciones;
GRANT SELECT ON dbo.cliente TO rol_consulta_habitaciones;

-- Permisos operativos para recepción
GRANT SELECT, INSERT, UPDATE ON dbo.cliente TO rol_recepcion;
GRANT SELECT, INSERT, UPDATE ON dbo.habitacion TO rol_recepcion;

-- Permiso de ejecución sobre procedimientos almacenados seguros
GRANT EXECUTE ON dbo.sp_RegistrarPago TO rol_recepcion;
GO

-- ============================================================================
-- 3. CREACIÓN DE LOGINS Y USUARIOS DE BASE DE DATOS
-- ============================================================================
-- Usuario para el área administrativa
CREATE LOGIN usuario_admin_hotel WITH PASSWORD = 'AdminPassword123!';
CREATE USER usuario_admin_hotel FOR LOGIN usuario_admin_hotel;

-- Usuario para la recepción
CREATE LOGIN usuario_recepcion WITH PASSWORD = 'RecepcionPass123!';
CREATE USER usuario_recepcion FOR LOGIN usuario_recepcion;
GO

-- ============================================================================
-- 4. ASIGNACIÓN DE USUARIOS A ROLES
-- ============================================================================
-- Control total para el administrador
ALTER ROLE db_owner ADD MEMBER usuario_admin_hotel;

-- Permisos operativos y de consulta para el recepcionista
ALTER ROLE rol_consulta_habitaciones ADD MEMBER usuario_recepcion;
ALTER ROLE rol_recepcion ADD MEMBER usuario_recepcion;
GO
