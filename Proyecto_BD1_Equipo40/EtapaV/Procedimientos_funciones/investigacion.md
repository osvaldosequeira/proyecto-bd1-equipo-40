investigacion.md

**Definición y objetivo:**
Los procedimientos almacenados (Stored Procedures) son programas SQL que se guardan dentro de la base de datos y pueden ejecutar múltiples operaciones. 
Las funciones almacenadas (Functions) son rutinas que reciben parámetros y devuelven un valor o una tabla.  
Objetivo: Encapsular la lógica de negocio del sistema de gestión hotelera en el propio motor de base de datos, garantizando consistencia, modularidad y un rendimiento superior.

**Ventajas y aplicaciones:** 
-Reutilización y centralización: La lógica de negocio (como reglas de tarifas, control de estados o cálculos de estadías) se centraliza en un único lugar, evitando duplicar código en las aplicaciones cliente.  
-Reducción de tráfico en la red: En lugar de enviar múltiples sentencias SQL consecutivas desde la aplicación, se ejecuta una única llamada al procedimiento.
-Mantenibilidad y seguridad: Facilita la actualización de procesos y permite otorgar permisos de ejecución específicos sin dar acceso directo de modificación sobre las tablas principales.   
-Aplicaciones concretas en el Hotel:
Calcular de forma dinámica el costo total de una estadía combinando los días transcurridos y la tarifa por noche de cada tipo de habitación.
Registrar una reserva completa validando automáticamente la disponibilidad de la habitación antes de realizar la inserción y actualizar su estado.
