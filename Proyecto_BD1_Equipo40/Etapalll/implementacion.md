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
