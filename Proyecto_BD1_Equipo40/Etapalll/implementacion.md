1. SCRIPT DDL

CREATE TABLE tipo_habitacion (
    id_tipo_habitacion INT IDENTITY(1,1) PRIMARY KEY,
    nombre_tipo VARCHAR(50) NOT NULL UNIQUE, 
    descripcion VARCHAR(255),
    capacidad_maxima INT NOT NULL CHECK (capacidad_maxima > 0),
    precio_por_noche DECIMAL(10, 2) NOT NULL CHECK (precio_por_noche > 0)
);

CREATE TABLE habitacion (
    id_habitacion INT IDENTITY(1,1) PRIMARY KEY,
    codigo_habitacion VARCHAR(10) NOT NULL UNIQUE,
    estado VARCHAR(20) DEFAULT 'libre' CHECK (estado IN ('libre', 'ocupada', 'en mantenimiento')),
    id_tipo_habitacion INT NOT NULL,
    CONSTRAINT fk_habitacion_tipo FOREIGN KEY (id_tipo_habitacion) REFERENCES tipo_habitacion(id_tipo_habitacion) ON DELETE NO ACTION ON UPDATE CASCADE
);
CREATE TABLE detalle_consumo (
    id_detalle INT IDENTITY(1,1) PRIMARY KEY,
    id_consumo INT NOT NULL,
    id_producto INT NOT NULL,
    cantidad INT NOT NULL CHECK (cantidad > 0),
    precio_unitario_historico DECIMAL(10, 2) NOT NULL CHECK (precio_unitario_historico >= 0),
    CONSTRAINT fk_detalle_consumo FOREIGN KEY (id_consumo) REFERENCES consumo(id_consumo) ON DELETE NO ACTION ON UPDATE CASCADE,
    CONSTRAINT fk_detalle_producto FOREIGN KEY (id_producto) REFERENCES producto(id_producto) ON DELETE NO ACTION ON UPDATE CASCADE
);

CREATE TABLE cliente (
    id_cliente INT IDENTITY(1,1) PRIMARY KEY,
    dni VARCHAR(10) NOT NULL UNIQUE,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL
);

CREATE TABLE cliente_telefono (
    telefono VARCHAR(30) NOT NULL,
    nro_dni VARCHAR(10) NOT NULL,
    PRIMARY KEY (telefono, nro_dni),
    CONSTRAINT fk_cliente_telefono FOREIGN KEY (nro_dni) REFERENCES cliente(dni) ON DELETE NO ACTION ON UPDATE CASCADE
);

CREATE TABLE reserva (
    id_reserva INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    fecha_reserva DATE DEFAULT CURRENT_DATE,
    fecha_entrada DATE NOT NULL,
    fecha_salida DATE NOT NULL,
    cantidad_personas INT NOT NULL CHECK (cantidad_personas > 0),
    id_cliente INT NOT NULL,
    id_habitacion INT NOT NULL,

    CONSTRAINT ck_fechas_reserva CHECK (fecha_salida > fecha_entrada),
    CONSTRAINT fk_reserva_cliente FOREIGN KEY (id_cliente) REFERENCES cliente(id_cliente) ON DELETE RESTRICT ON UPDATE CASCADE,
    CONSTRAINT fk_reserva_habitacion FOREIGN KEY (id_habitacion) REFERENCES habitacion(id_habitacion) ON DELETE RESTRICT ON UPDATE CASCADE
);


CREATE TABLE producto (
    id_producto INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    codigo_producto VARCHAR(20) NOT NULL UNIQUE,
    descripcion VARCHAR(150) NOT NULL,
    precio_unitario DECIMAL(10, 2) NOT NULL CHECK (precio_unitario >= 0),
    stock INT NOT NULL CHECK (stock >= 0)
);

CREATE TABLE mantenimiento (
    id_mantenimiento INT IDENTITY(1,1) PRIMARY KEY,
    fecha_hora DATETIME DEFAULT GETDATE(),
    categoria VARCHAR(20) NOT NULL CHECK (categoria IN ('limpieza', 'reparacion')),
    tipo_reparacion VARCHAR(100),
    id_habitacion INT NOT NULL,
    CONSTRAINT fk_mantenimiento_habitacion FOREIGN KEY (id_habitacion) REFERENCES habitacion(id_habitacion) ON DELETE NO ACTION ON UPDATE CASCADE
);

CREATE TABLE pago (
    id_pago INT IDENTITY(1,1) PRIMARY KEY,
    fecha DATE DEFAULT CAST(GETDATE() AS DATE),
    monto DECIMAL(10, 2) NOT NULL CHECK (monto > 0),
    medio_pago VARCHAR(50) NOT NULL,
    id_reserva INT,
    id_consumo INT,
    CONSTRAINT ck_origen_pago CHECK ((id_reserva IS NOT NULL AND id_consumo IS NULL) OR (id_reserva IS NULL AND id_consumo IS NOT NULL)),
    CONSTRAINT fk_pago_reserva FOREIGN KEY (id_reserva) REFERENCES reserva(id_reserva) ON DELETE NO ACTION ON UPDATE NO ACTION,
    CONSTRAINT fk_pago_consumo FOREIGN KEY (id_consumo) REFERENCES consumo(id_consumo) ON DELETE NO ACTION ON UPDATE NO ACTION
);



2. SCRIPT DML

INSERT INTO tipo_habitacion (nombre_tipo, descripcion, capacidad_maxima, precio_por_noche) VALUES
('Simple Standard', 'Habitacion individual con cama simple y bano privado', 1, 45000.00),
('Doble Matrimonial', 'Habitacion con cama sommier matrimonial', 2, 75000.00),
('Doble Twin', 'Habitacion con dos camas individuales', 2, 70000.00),
('Triple Familiar', 'Habitacion con tres camas individuales', 3, 95000.00),
('Suite Junior', 'Suite con zona de estar y cama King Size', 2, 130000.00),
('Suite Presidencial', 'Suite de lujo con hidromasaje y vista panoramica', 4, 210000.00),
('Super Executive', 'Habitacion ejecutiva con escritorio de trabajo', 3, 160000.00),
('Doble Superior', 'Habitacion doble amplia con balcon al jardin', 2, 85000.00);

INSERT INTO habitacion (codigo_habitacion, estado, id_tipo_habitacion) VALUES
('HAB-101', 'libre', 1),
('HAB-102', 'ocupada', 2),
('HAB-103', 'libre', 3),
('HAB-201', 'libre', 2),
('HAB-202', 'ocupada', 4),
('HAB-301', 'libre', 5),
('HAB-302', 'en mantenimiento', 6),
('HAB-401', 'ocupada', 7),
INSERT INTO detalle_consumo (id_consumo, id_producto, cantidad, precio_unitario_historico) VALUES
(1, 1, 1, 18500.00),
(1, 5, 1, 2500.00),
(2, 4, 3, 2200.00),
(2, 6, 2, 3200.00),
(3, 2, 2, 14200.00),
(3, 7, 1, 24000.00),
(4, 1, 3, 18500.00),
(4, 8, 3, 5500.00),
(5, 9, 2, 3800.00),
(6, 3, 2, 11800.00),
(7, 2, 1, 14200.00),
(8, 7, 2, 24000.00);

INSERT INTO cliente (dni, nombre, apellido) VALUES
('44466386', 'Lourdes', 'Aranda'),
('45939727', 'Valentina Belen', 'Gomez'),
('36675735', 'Veronica Stefania', 'Gomez Varela'),
('44543730', 'Natalia Magali', 'Lezcano'),
('45644904', 'Osvaldo Nolberto', 'Sequeira'),
('38123456', 'Juan Carlos', 'Perez'),
('40987654', 'Maria Elena', 'Gomez'),
('42111222', 'Pedro', 'Lopez'),
('35888999', 'Ana', 'Martinez'),
('33444555', 'Roberto', 'Diaz');

INSERT INTO cliente_telefono (telefono, nro_dni) VALUES
('3794-112233', '44466386'),
('3794-998877', '44466386'),
('3624-445566', '45939727'),
('3794-776655', '36675735'),
('3777-123456', '44543730'),
('3794-554433', '38123456'),
('3624-889900', '40987654'),
('3777-654321', '42111222'),
('3794-223344', '35888999'),
('3794-667788', '35888999');
('HAB-402', 'libre', 8),
('HAB-501', 'libre', 6);

INSERT INTO mantenimiento (fecha_hora, categoria, tipo_reparacion, id_habitacion) VALUES
('2026-09-09 10:00:00', 'limpieza', NULL, 1),
('2026-09-14 11:30:00', 'reparacion', 'Cambio de cuerito de canilla del bano', 2),
('2026-09-19 15:00:00', 'reparacion', 'Reparacion de tomacorriente defectuoso', 5),
('2026-09-21 09:00:00', 'limpieza', NULL, 6),
('2026-09-22 14:20:00', 'reparacion', 'Carga de gas refrigerante en aire acondicionado', 7),
('2026-09-30 08:30:00', 'limpieza', NULL, 8),
('2026-10-07 16:00:00', 'reparacion', 'Pintura de pared por humedad resuelta', 3),
('2026-10-11 10:15:00', 'limpieza', NULL, 10),
('2026-10-17 12:00:00', 'limpieza', NULL, 4),
('2026-10-31 17:00:00', 'reparacion', 'Ajuste de cerradura electronica', 9);

INSERT INTO pago (fecha, monto, medio_pago, id_reserva, id_consumo) VALUES
('2026-09-01', 45000.00, 'Tarjeta de Credito', 1, NULL),
('2026-09-02', 75000.00, 'Transferencia', 2, NULL),
('2026-09-03', 150000.00, 'Tarjeta de Debito', 3, NULL),
('2026-09-11', 21000.00, 'Efectivo', NULL, 1),
('2026-09-11', 13000.00, 'Efectivo', NULL, 2),
('2026-09-16', 52400.00, 'Tarjeta de Debito', NULL, 3),
('2026-09-20', 325000.00, 'Transferencia', 3, NULL),
('2026-09-22', 260000.00, 'Tarjeta de Credito', 4, NULL),
('2026-10-02', 23600.00, 'Efectivo', NULL, 6),
('2026-10-12', 630000.00, 'Transferencia', 7, NULL);
