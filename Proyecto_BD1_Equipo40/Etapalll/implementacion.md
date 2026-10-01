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
