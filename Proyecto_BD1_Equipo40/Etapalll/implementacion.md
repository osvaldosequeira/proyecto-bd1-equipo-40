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
('HAB-402', 'libre', 8),
('HAB-501', 'libre', 6);
