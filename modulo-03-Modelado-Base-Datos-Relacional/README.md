# 📘 Módulo 3: Modelado de Bases de Datos Relacionales

## 1. Conceptos de entidades, atributos y relaciones

**Entidad**: Representa un objeto del mundo real que queremos almacenar en la base de datos.

**Ejemplo**: Cliente, Producto, Empleado, Factura.

**Atributos**: Son las características de la entidad.

**Ejemplo (Cliente)**: Nombre, Email, Teléfono.

**Relación**: Es el vínculo entre dos entidades.

**Ejemplo**: Cliente realiza Factura, Factura contiene Producto.

📌 **Ejemplo práctico (texto)**:
```
Entidad Cliente
- IdCliente
- Nombre
- Email

Entidad Factura
- IdFactura
- Fecha
- IdCliente

Relación: Un cliente puede tener muchas facturas (1:N).
```

## 2. Claves primarias y foráneas

**Clave primaria (PK)**: identifica de forma única cada fila en una tabla.

**Clave foránea (FK)**: conecta dos tablas, apuntando a la PK de otra.

📌 **Ejemplo en SQL**:

```sql
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Email NVARCHAR(100)
);

CREATE TABLE Facturas (
    IdFactura INT PRIMARY KEY IDENTITY(1,1),
    Fecha DATE NOT NULL,
    IdCliente INT NOT NULL,
    FOREIGN KEY (IdCliente) REFERENCES Clientes(IdCliente)
);
```

👉 **Aquí, Facturas.IdCliente es FK que apunta a Clientes.IdCliente.**

## 3. Tipos de relaciones

**Uno a Uno (1:1)**:
Un registro de una tabla está vinculado a uno y solo un registro de otra tabla.

**Ejemplo**: Empleado ↔ DetalleEmpleado (número de pasaporte).

**Uno a Muchos (1:N)**:
Un registro de una tabla puede estar relacionado con muchos de otra.

**Ejemplo**: Cliente ↔ Facturas.

**Muchos a Muchos (N:M)**:
Un registro de una tabla puede estar relacionado con muchos de otra y viceversa.

Se resuelve con una tabla intermedia.

**Ejemplo**: Producto ↔ Factura → FacturaDetalle.

📌 **Ejemplo SQL de relación muchos a muchos**:

```sql
CREATE TABLE Productos (
    IdProducto INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Precio DECIMAL(10,2) NOT NULL
);

CREATE TABLE FacturaDetalle (
    IdFactura INT,
    IdProducto INT,
    Cantidad INT,
    PRIMARY KEY (IdFactura, IdProducto),
    FOREIGN KEY (IdFactura) REFERENCES Facturas(IdFactura),
    FOREIGN KEY (IdProducto) REFERENCES Productos(IdProducto)
);
```

## 4. Normalización (1FN, 2FN, 3FN)

El proceso de organizar datos para eliminar redundancia e inconsistencias.

### **1FN (Primera Forma Normal)**
Cada campo debe ser atómico (no dividir en subcampos).

**❌ Ejemplo incorrecto:**
```sql
-- Columna "Direcciones" con múltiples valores
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY,
    Nombre NVARCHAR(100),
    Direcciones NVARCHAR(500) -- "Av. Perú 123, Av. Brasil 456"
);
```

**✅ Ejemplo correcto:**
```sql
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY,
    Nombre NVARCHAR(100)
);

CREATE TABLE Direcciones (
    IdDireccion INT PRIMARY KEY,
    IdCliente INT,
    Direccion NVARCHAR(200),
    FOREIGN KEY (IdCliente) REFERENCES Clientes(IdCliente)
);
```

### **2FN (Segunda Forma Normal)**
Cada atributo depende de la clave primaria.

**❌ Ejemplo incorrecto:**
```sql
-- En FacturaDetalle guardar también el Nombre del Cliente
CREATE TABLE FacturaDetalle (
    IdFactura INT,
    IdProducto INT,
    NombreCliente NVARCHAR(100), -- No depende de la PK
    PRIMARY KEY (IdFactura, IdProducto)
);
```

### **3FN (Tercera Forma Normal)**
No debe haber dependencias transitivas.

**❌ Ejemplo incorrecto:**
```sql
-- En Clientes guardar también el Nombre de la Ciudad
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY,
    Nombre NVARCHAR(100),
    Ciudad NVARCHAR(50), -- Depende transitivamente
    Pais NVARCHAR(50)
);
```

**✅ Ejemplo correcto:**
```sql
CREATE TABLE Ciudades (
    IdCiudad INT PRIMARY KEY,
    Nombre NVARCHAR(50),
    IdPais INT
);

CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY,
    Nombre NVARCHAR(100),
    IdCiudad INT,
    FOREIGN KEY (IdCiudad) REFERENCES Ciudades(IdCiudad)
);
```


## 5. Diagramas entidad-relación en SSMS

En SSMS puedes crear diagramas visuales:

Botón derecho en Database Diagrams → New Database Diagram.

Seleccionar tablas (Clientes, Facturas, Productos, FacturaDetalle).

SSMS dibuja automáticamente las relaciones.

👉 **Esto es lo que usan los analistas antes de programar.**

## 6. Ejercicio práctico: Diseñar modelo para un sistema de ventas

**Requerimientos**:

Cada Cliente puede realizar varias Facturas.

Cada Factura puede contener varios Productos.

Los productos pueden estar en muchas facturas (relación N:M).

**Tablas y relaciones**:

```sql
-- Clientes
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Email NVARCHAR(100),
    Telefono NVARCHAR(15)
);

-- Facturas
CREATE TABLE Facturas (
    IdFactura INT PRIMARY KEY IDENTITY(1,1),
    Fecha DATE NOT NULL,
    IdCliente INT NOT NULL,
    FOREIGN KEY (IdCliente) REFERENCES Clientes(IdCliente)
);

-- Productos
CREATE TABLE Productos (
    IdProducto INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Precio DECIMAL(10,2) NOT NULL
);

-- Detalle de Factura (relación N:M)
CREATE TABLE FacturaDetalle (
    IdFactura INT NOT NULL,
    IdProducto INT NOT NULL,
    Cantidad INT NOT NULL,
    PRIMARY KEY (IdFactura, IdProducto),
    FOREIGN KEY (IdFactura) REFERENCES Facturas(IdFactura),
    FOREIGN KEY (IdProducto) REFERENCES Productos(IdProducto)
);
```

**Insertar datos de prueba**:

```sql
-- Clientes
INSERT INTO Clientes (Nombre, Email, Telefono) VALUES
('Juan Pérez', 'juan@mail.com', '987654321'),
('María López', 'maria@mail.com', '912345678');

-- Productos
INSERT INTO Productos (Nombre, Precio) VALUES
('Laptop Lenovo', 2500.00),
('Mouse Logitech', 80.00),
('Monitor Samsung', 1200.00);

-- Factura de Juan
INSERT INTO Facturas (Fecha, IdCliente) VALUES
(GETDATE(), 1);

-- Detalle de factura (Juan compra laptop y mouse)
INSERT INTO FacturaDetalle (IdFactura, IdProducto, Cantidad) VALUES
(1, 1, 1), -- Laptop
(1, 2, 2); -- Dos mouse
```

**Consultar facturas de un cliente**:

```sql
SELECT f.IdFactura, f.Fecha, c.Nombre AS Cliente, p.Nombre AS Producto, fd.Cantidad, p.Precio, (fd.Cantidad * p.Precio) AS Total
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
WHERE c.Nombre = 'Juan Pérez';
```

⚠️ **Nota importante**: Mas adelante veremos que significa `JOIN` esto solo es un ejemplo de consulta (puedes ejecutar tus propias consultas con los conceptos que ya has ido aprendiendo)

📌 **Resultado esperado**:

```yaml
IdFactura: 1
Fecha: 2025-09-25
Cliente: Juan Pérez
Producto: Laptop Lenovo
Cantidad: 1
Precio: 2500
Total: 2500

IdFactura: 1
Fecha: 2025-09-25
Cliente: Juan Pérez
Producto: Mouse Logitech
Cantidad: 2
Precio: 80
Total: 160
```

✅ **Con este capítulo ya puedes**:

Identificar entidades y atributos.

Usar claves primarias y foráneas.

Entender tipos de relaciones (1:1, 1:N, N:M).

Normalizar datos hasta 3FN.

Crear un modelo realista de ventas en SQL Server.

👉 **Con esto cierras la fase de diseño estructural. A partir del siguiente capítulo (Consultas SQL Intermedias) ya empezarás a explotar datos de tus modelos.**

📖 **[Ir al Módulo 4: Consultas SQL Intermedias](/modulo-04-Consultas-SQL-Intermedias/README.md)**

*Si encuentras algún error o tienes sugerencias para mejorar este módulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*