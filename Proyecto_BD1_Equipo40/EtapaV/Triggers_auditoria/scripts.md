USE [Gestor de Reservas,Estadias y consumos de un Hotel];
GO

-- PASO 1: LIMPIEZA TOTAL (Elimina triggers y tablas previas para evitar errores)

IF OBJECT_ID('dbo.trg_Auditoria_Producto_UpdateDelete', 'TR') IS NOT NULL
    DROP TRIGGER dbo.trg_Auditoria_Producto_UpdateDelete;
GO

IF OBJECT_ID('dbo.trg_auditoria_producto', 'TR') IS NOT NULL
    DROP TRIGGER dbo.trg_auditoria_producto;
GO

IF OBJECT_ID('dbo.trg_Bloquear_Delete_Reserva', 'TR') IS NOT NULL
    DROP TRIGGER dbo.trg_Bloquear_Delete_Reserva;
GO

IF OBJECT_ID('dbo.auditoria_producto', 'U') IS NOT NULL
    DROP TABLE dbo.auditoria_producto;
GO

-- PASO 2: CREACIÓN DE LA TABLA AUXILIAR DE AUDITORÍA

CREATE TABLE dbo.auditoria_producto (
    id_auditoria INT IDENTITY(1,1) PRIMARY KEY,
    id_producto INT NOT NULL,
    codigo_producto_anterior VARCHAR(20),
    descripcion_anterior VARCHAR(150),
    precio_unitario_anterior DECIMAL(10,2),
    stock_anterior INT,
    tipo_operacion VARCHAR(10) NOT NULL CHECK (tipo_operacion IN ('UPDATE', 'DELETE')),
    fecha_operacion DATETIME NOT NULL DEFAULT GETDATE(),
    usuario_db VARCHAR(100) NOT NULL DEFAULT SYSTEM_USER
);
GO

-- 1. Eliminar el disparador si ya existe
IF OBJECT_ID('dbo.trg_Bloquear_Delete_Reserva', 'TR') IS NOT NULL
    DROP TRIGGER dbo.trg_Bloquear_Delete_Reserva;
GO

-- 2. Crear el disparador en un lote separado (precedido por GO)
CREATE TRIGGER dbo.trg_Bloquear_Delete_Reserva
ON reserva
INSTEAD OF DELETE
AS
BEGIN
    SET NOCOUNT ON;

    RAISERROR ('[CONTROL HOTEL]: Queda estrictamente prohibido eliminar registros de reservas. Para bajas, modifique el estado a "CANCELADA".', 16, 1);
    ROLLBACK TRANSACTION;
END;
GO

-- PASO 4: CREACIÓN DEL TRIGGER DE CONTROL DE RESERVAS (INSTEAD OF DELETE)

-- 1. Eliminar el trigger si ya existe para liberar la tabla
IF OBJECT_ID('dbo.trg_Bloquear_Delete_Reserva', 'TR') IS NOT NULL
    DROP TRIGGER dbo.trg_Bloquear_Delete_Reserva;
GO
CREATE TRIGGER dbo.trg_Bloquear_Delete_Reserva
ON reserva
INSTEAD OF DELETE
AS
BEGIN
    SET NOCOUNT ON;

    RAISERROR ('[CONTROL HOTEL]: Queda estrictamente prohibido eliminar registros de reservas. Para bajas, modifique el estado a "CANCELADA".', 16, 1);
    ROLLBACK TRANSACTION;
END;
GO

-- PASO 5: PRUEBAS Y DEMOSTRACIÓN DE FUNCIONAMIENTO

-- 1. Insertamos un producto específico para la prueba
INSERT INTO producto (codigo_producto, descripcion, precio_unitario, stock) 
VALUES ('TEST-UPD', 'Vino Cabernet Test 750ml', 15000.00, 20);

-- Capturamos automáticamente su ID
DECLARE @id_upd INT = SCOPE_IDENTITY();


-- 2. Modificamos su precio y stock para disparar el trigger
UPDATE producto 
SET precio_unitario = 18500.00, stock = 15
WHERE id_producto = @id_upd;

-- 3. Consultamos la auditoría para verificar la captura (Muestra precio anterior: 15000.00)
SELECT 
    id_auditoria,
    id_producto,
    descripcion_anterior,
    precio_unitario_anterior,
    stock_anterior,
    tipo_operacion,
    fecha_operacion,
    usuario_db
FROM auditoria_producto 
WHERE id_producto = @id_upd;
GO

-- PRUEBA 2: AUDITORÍA DE ELIMINACIÓN (DELETE)

-- 1. Insertamos otro producto de prueba
INSERT INTO producto (codigo_producto, descripcion, precio_unitario, stock) 
VALUES ('TEST-DEL', 'Producto a Eliminar Test', 2500.00, 10);

-- Capturamos automáticamente su ID
DECLARE @id_del INT = SCOPE_IDENTITY();

-- 2. Eliminamos el producto de la tabla
DELETE FROM producto 
WHERE id_producto = @id_del;

-- 3. Consultamos la auditoría para verificar el registro de borrado
SELECT 
    id_auditoria,
    id_producto,
    descripcion_anterior,
    precio_unitario_anterior,
    tipo_operacion,
    fecha_operacion,
    usuario_db
FROM auditoria_producto 
WHERE tipo_operacion = 'DELETE';
GO

-- PRUEBA 3: BLOQUEO DE ELIMINACIÓN EN RESERVA (INSTEAD OF DELETE)

-- Intentamos eliminar cualquier reserva existente
DELETE FROM reserva WHERE id_reserva = 1;
GO
