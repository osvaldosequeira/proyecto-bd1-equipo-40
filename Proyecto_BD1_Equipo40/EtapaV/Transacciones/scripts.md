# Script de Demostración y Manejo del Bloque TRY...CATCH en SQL SERVER
El script utiliza una estructura formal de manejo de excepciones (TRY...CATCH). 
Esto captura tanto los errores nativos del motor  mediante la instrucción THROW cuando se viola la regla de negocio.

** CREACIÓN DEL PROCEDIMIENTO ALMACENADO SEGURO CON CONTROL TRANSACCIONAL **

CREATE PROCEDURE dbo.sp_RegistrarConsumoHotelero
    @id_cliente INT,
    @id_producto INT,
    @cantidad INT
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @stock_disponible INT;
    DECLARE @precio_vigente DECIMAL(12,2);
    DECLARE @id_consumo_nuevo INT;

    BEGIN TRANSACTION;

    BEGIN TRY
        SELECT @stock_disponible = stock, @precio_vigente = precio
        FROM producto WITH (UPDLOCK) 
        WHERE id_producto = @id_producto;

        IF @stock_disponible IS NULL
        BEGIN
            THROW 50001, 'Error: El ID de producto proporcionado no existe en el catálogo.', 1;
        END

        IF @stock_disponible < @cantidad
        BEGIN
            -- Forzar la interrupción del bloque e ir directo al CATCH
            THROW 50002, 'Error: Stock insuficiente en el hotel para satisfacer la solicitud.', 1;
        END

        INSERT INTO consumo (id_cliente, fecha_hora)
        VALUES (@id_cliente, GETDATE());
        
        SET @id_consumo_nuevo = SCOPE_IDENTITY();

        INSERT INTO detalle_consumo (id_consumo, id_producto, cantidad, precio_unitario_historico)
        VALUES (@id_consumo_nuevo, @id_producto, @cantidad, @precio_vigente);

        UPDATE producto
        SET stock = stock - @cantidad
        WHERE id_producto = @id_producto;

        COMMIT TRANSACTION;
        PRINT 'TRANSACCION EXITOSA: Consumo e inventario actualizados correctamente.';

    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRANSACTION;
            PRINT 'TRANSACCION REVERTIDA: Se aplicó ROLLBACK de seguridad.';
        END

        DECLARE @MsgError NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @Severidad INT = ERROR_SEVERITY();
        DECLARE @Estado INT = ERROR_STATE();
        
        RAISERROR(@MsgError, @Severidad, @Estado);
    END CATCH
END;
GO

** Resultados de Demostración **

-- Escenarios de ejecución 

Caso de Prueba 1: Flujo Normal 

EXEC dbo.sp_RegistrarConsumoHotelero @id_cliente = 12, @id_producto = 5, @cantidad = 3;

Caso de Prueba 2: Flujo Amolado 

EXEC dbo.sp_RegistrarConsumoHotelero @id_cliente = 12, @id_producto = 5, @cantidad = 15;
