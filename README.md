# Proyecto-BDI-Equipo-40
Sistema Gestor de Reservas, Estadías y Consumos de un Hotel

El presente proyecto consiste en el diseño de una base de datos diseñada para gestionar las principales operaciones de un hotel con restaurante integrado. El sistema permite organizar y administrar la información relacionada con los hoteles, habitaciones, categorias de habitación, clientes, reservas, consumos del restaurante, productos, pagos y servicios de mantenimiento.

Características Principales

Gestión de Reservas y Estadías: Control integral del ciclo de vida del hospedaje de los clientes.
Control de Stock y Restaurante: Administración de productos del restaurante, registro de consumos y preservación histórica de precios aplicados en el momento de la transacción.
Medios de Pago: Flexibilidad para registrar diferentes medios de pago y soporte para pagos parciales de reservas y consumos.
Seguridad y Roles: Implementación de esquemas de seguridad avanzados y roles personalizados (como rol_consulta_habitaciones) para restringir y asegurar el acceso a los datos.

Además, se contemplan los distintos medios de pago y la posibilidad de realizar pagos parciales, tanto para las reservas como para los consumos. El diseño de la base de datos se desarrolla a partir de las reglas de negocio y restricciones propias del funcionamiento de un hotel, buscando lograr  una estructura de datos organizada, consistente y normalizada que permita reducir redundancias y mantener la integridad de la información.

##  Diseño e Integridad de Datos
El modelo se construyó considerando rigurosamente las reglas de negocio y restricciones del dominio hotelero. Su arquitectura garantiza:
* **Normalización:** Estructura optimizada para reducir la redundancia de datos.
* **Consistencia e Integridad:** Aplicación de claves primarias, foráneas y restricciones lógicas que aseguran la validez de la información almacenada.
