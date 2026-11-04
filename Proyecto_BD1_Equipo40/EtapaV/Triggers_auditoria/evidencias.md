<img width="1014" height="238" alt="Captura de pantalla 2026-11-03 221819" src="https://github.com/user-attachments/assets/feff982e-7dfa-4dc1-a00b-300a8416f87e" />
### 3.1. Demostración y Evidencia del Trigger de Auditoría (`AFTER UPDATE`)

Se ejecutó una prueba de modificación sobre la tabla `producto` para verificar el correcto almacenamiento de datos históricos por parte del disparador `trg_Auditoria_Producto_UpdateDelete`.

#### Sentencias SQL ejecutadas:
```sql
-- 1. Modificación de precio y stock sobre el producto ID 1
UPDATE producto 
SET precio_unitario = 19500.00, stock = 40
WHERE id_producto = 1;

-- 2. Consulta de verificación en la tabla auxiliar de auditoría
SELECT * FROM auditoria_producto WHERE id_producto = 1;


<img width="1015" height="311" alt="Captura de pantalla 2026-11-03 222656" src="https://github.com/user-attachments/assets/37e5ff91-ce35-4587-baf4-65e29b50769d" />

Esta imagen prueba que el sistema no pierde datos cuando un usuario elimina un registro: el trigger intercepta la fila eliminada desde la pseudotabla deleted y la respalda íntegramente en auditoria_producto marcando la operación como 'DELETE'.

<img width="1036" height="272" alt="Captura de pantalla 2026-11-03 230155" src="https://github.com/user-attachments/assets/0d6dd553-40ec-4b18-99b4-485e0d516743" />

Demuestra la trazabilidad de cambios en el sistema: el disparador trg_Auditoria_Producto_UpdateDelete detectó la edición, capturó los valores previos desde la pseudotabla deleted y guardó un respaldo histórico con el registro exacto de quién hizo la modificación, cuándo la hizo y qué valores tenía el producto antes.

<img width="1023" height="299" alt="Captura de pantalla 2026-11-03 230229" src="https://github.com/user-attachments/assets/ea7db524-73a4-41bd-a242-d7101206e4f5" />

Demuestra la protección e integridad de los datos de hospedaje: el trigger trg_Bloquear_Delete_Reserva interceptó el intento de eliminación antes de que afectara a la base de datos, canceló la transacción de inmediato y notificó la regla de negocio que exige cambiar el estado de la reserva a 'CANCELADA' mediante un UPDATE.
