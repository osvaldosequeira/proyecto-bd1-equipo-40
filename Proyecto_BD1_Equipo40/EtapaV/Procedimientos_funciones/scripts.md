USE HotelBD;
GO

-- 0. LIMPIEZA PREVIA (Si los objetos ya existen)
IF OBJECT_ID(N'dbo.fn_CalcularCostoEstadia', N'FN') IS NOT NULL
    DROP FUNCTION dbo.fn_CalcularCostoEstadia;
GO

IF OBJECT_ID(N'dbo.sp_RegistrarReserva', N'P') IS NOT NULL
    DROP PROCEDURE dbo.sp_RegistrarReserva;
GO

-- 1. CREACIÓN DE LA FUNCIÓN ALMACENADA

CREATE FUNCTION dbo.fn_CalcularCostoEstadia (@id_reserva INT)
RETURNS DECIMAL(10,2)
AS
BEGIN
    DECLARE @costo_total DECIMAL(10,2);
    
SELECT @costo_total = DATEDIFF(DAY, r.fecha_entrada, r.fecha_salida) * th.precio_por_noche
FROM reserva r
JOIN habitacion h ON r.id_habitacion = h.id_habitacion
JOIN tipo_habitacion th ON h.id_tipo_habitacion = th.id_tipo_habitacion
WHERE r.id_reserva = @id_reserva;

RETURN ISNULL(@costo_total, 0);
END;
GO

-- 2. CREACIÓN DEL PROCEDIMIENTO ALMACENADO

CREATE PROCEDURE dbo.sp_RegistrarReserva
    @dni_cliente VARCHAR(10),
    @codigo_habitacion VARCHAR(10),
    @fecha_entrada DATE,
    @fecha_salida DATE,
    @cantidad_personas INT
AS
BEGIN
    DECLARE @id_cliente INT, @id_habitacion INT, @estado_hab VARCHAR(20);
    
    -- Obtener IDs correspondientes
    SELECT @id_cliente = id_cliente FROM cliente WHERE dni = @dni_cliente;
    SELECT @id_habitacion = id_habitacion, @estado_hab = estado FROM habitacion WHERE codigo_habitacion = @codigo_habitacion;

    -- Validar existencia del cliente
    IF @id_cliente IS NULL
    BEGIN
        RAISERROR('Error: El cliente no se encuentra registrado.', 16, 1);
        RETURN;
    END

    -- Validar disponibilidad de la habitación
    IF @estado_hab <> 'libre'
    BEGIN
        RAISERROR('Error: La habitación seleccionada no está disponible.', 16, 1);
        RETURN;
    END

    -- Registrar la reserva (incluyendo fecha actual y horarios obligatorios)
    INSERT INTO reserva (
        fecha_reserva, 
        fecha_entrada, 
        horario_entrada, 
        fecha_salida, 
        horario_salida, 
        cantidad_personas, 
        id_cliente, 
        id_habitacion
    )
    VALUES (
        GETDATE(), 
        @fecha_entrada, 
        '14:00:00', 
        @fecha_salida, 
        '10:00:00', 
        @cantidad_personas, 
        @id_cliente, 
        @id_habitacion
    );

    -- Actualizar el estado de la habitación a ocupada
    UPDATE habitacion 
    SET estado = 'ocupada' 
    WHERE id_habitacion = @id_habitacion;
    
    PRINT '¡Reserva registrada con éxito y habitación actualizada a ocupada!';
END;
GO

-- ==========================================================
-- 3. SCRIPTS DE DEMOSTRACIÓN Y PRUEBAS
-- ==========================================================

-- A. Prueba de caso exitoso (Liberamos la habitación primero y ejecutamos)
UPDATE habitacion SET estado = 'libre' WHERE codigo_habitacion = 'HAB-101';
GO

EXEC dbo.sp_RegistrarReserva 
    @dni_cliente = '44466386',          -- Lourdes Aranda (Cliente existente)
    @codigo_habitacion = 'HAB-101',     -- Habitación libre
    @fecha_entrada = '2026-12-01', 
    @fecha_salida = '2026-12-05', 
    @cantidad_personas = 1;
GO

-- B. Prueba de caso fallido (Intentar reservar una habitación que ya está ocupada)
EXEC dbo.sp_RegistrarReserva 
    @dni_cliente = '45939727',          
    @codigo_habitacion = 'HAB-101',     -- Habitación ya ocupada
    @fecha_entrada = '2026-12-10', 
    @fecha_salida = '2026-12-15', 
    @cantidad_personas = 2;
GO

-- C. Prueba de la Función Almacenada (Calcular costo de las reservas)
SELECT 
    id_reserva, 
    fecha_entrada, 
    fecha_salida, 
    dbo.fn_CalcularCostoEstadia(id_reserva) AS CostoTotalEstadia
FROM reserva;
GO
