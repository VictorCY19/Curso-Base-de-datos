# 📘 Capítulo 2: Lenguaje SQL – Fundamentos

## 1. Lenguajes en SQL Server

SQL Server organiza los comandos en diferentes sub-lenguajes:

- **DDL (Data Definition Language)** → Definen la estructura de la base de datos.
  - `CREATE`, `ALTER`, `DROP`

- **DML (Data Manipulation Language)** → Manipulan los datos.
  - `INSERT`, `UPDATE`, `DELETE`

- **DQL (Data Query Language)** → Consultan información.
  - `SELECT`

- **DCL (Data Control Language)** → Control de permisos.
  - `GRANT`, `REVOKE`, `DENY`

- **TCL (Transaction Control Language)** → Manejo de transacciones.
  - `BEGIN TRAN`, `COMMIT`, `ROLLBACK`

👉 **En este capítulo nos centraremos en DDL, DML y DQL.**

## 2. Tipos de datos en SQL Server

SQL Server tiene muchos tipos de datos, los más comunes:

- **Números enteros**: `INT`, `BIGINT`, `SMALLINT`
- **Números decimales**: `DECIMAL`, `FLOAT`, `REAL`
- **Texto**: `CHAR`, `VARCHAR`, `NVARCHAR`
- **Fechas y tiempos**: `DATE`, `DATETIME`, `TIME`
- **Booleanos**: `BIT` (0 o 1)

### 📌 Ejemplo de tabla con diferentes tipos:

```sql
CREATE TABLE Productos (
    IdProducto INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Precio DECIMAL(10,2) NOT NULL,
    Stock INT DEFAULT 0,
    FechaRegistro DATETIME DEFAULT GETDATE(),
    Activo BIT DEFAULT 1
);
```

## 3. Crear tablas con CREATE TABLE

### Ejemplo: tabla de Empleados

```sql
CREATE TABLE Empleados (
    IdEmpleado INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Cargo NVARCHAR(50),
    Sueldo DECIMAL(10,2) NOT NULL,
    FechaIngreso DATE
);
```

### 📌 Aquí usamos:

- **PRIMARY KEY** → clave única para cada empleado
- **IDENTITY(1,1)** → autoincrementa el Id desde 1
- **NOT NULL** → campo obligatorio

## 4. Insertar registros con INSERT

### Ejemplo práctico:

```sql
INSERT INTO Empleados (Nombre, Cargo, Sueldo, FechaIngreso) VALUES
('Carlos Ramírez', 'Desarrollador', 2500.50, '2023-06-15'),
('Ana Torres', 'Analista', 2800.00, '2022-03-01');
```

## 5. Consultar registros con SELECT

### Consultar todos los empleados:

```sql
SELECT * FROM Empleados;
```

### Consultar columnas específicas:

```sql
SELECT Nombre, Cargo, Sueldo FROM Empleados;
```

### Renombrar columnas con AS:

```sql
SELECT Nombre AS 'Empleado', Sueldo AS 'Salario' FROM Empleados;
```

## 6. Actualizar registros con UPDATE

### Ejemplo: subir el sueldo de "Carlos Ramírez":

```sql
UPDATE Empleados
SET Sueldo = 2700.00
WHERE Nombre = 'Carlos Ramírez';
```

⚠️ **Nota importante**: Siempre usar `WHERE`, si no actualizarás toda la tabla.

## 7. Eliminar registros con DELETE

### Ejemplo: eliminar a "Ana Torres":

```sql
DELETE FROM Empleados
WHERE Nombre = 'Ana Torres';
```

### Eliminar todos los registros:

```sql
DELETE FROM Empleados;
```

⚠️ **Si quieres borrar toda la tabla completa, se usa:**

```sql
DROP TABLE Empleados;
```

## 8. Ejercicio práctico de todo el capítulo

### Crear tabla Clientes con estos campos:

- `IdCliente` (INT, PK, autoincremental)
- `Nombre` (NVARCHAR(100), requerido)
- `Telefono` (NVARCHAR(15), opcional)
- `Email` (NVARCHAR(100), opcional)
- `FechaRegistro` (DATETIME, por defecto la fecha actual)

```sql
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Telefono NVARCHAR(15),
    Email NVARCHAR(100),
    FechaRegistro DATETIME DEFAULT GETDATE()
);
```

### Insertar datos de ejemplo:

```sql
INSERT INTO Clientes (Nombre, Telefono, Email) VALUES
('Juan Pérez', '987654321', 'juan@mail.com'),
('María López', '912345678', 'maria@mail.com'),
('Pedro Castillo', NULL, 'pedro@mail.com');
```

### Consultar registros:

```sql
SELECT * FROM Clientes;
```

### Actualizar teléfono de Pedro:

```sql
UPDATE Clientes
SET Telefono = '900111222'
WHERE Nombre = 'Pedro Castillo';
```

### Eliminar a Juan Pérez:

```sql
DELETE FROM Clientes
WHERE Nombre = 'Juan Pérez';
```

## ✅ Resumen del capítulo

**Ahora puedes:**

- ✅ Crear tablas con diferentes tipos de datos
- ✅ Insertar datos en las tablas
- ✅ Consultar información con SELECT
- ✅ Modificar registros existentes con UPDATE
- ✅ Eliminar registros con DELETE

👉 **Es decir, ya tienes el mínimo que una empresa espera de un desarrollador junior que usa SQL Server.**

---

## 🎯 Ejercicios adicionales para practicar

### Ejercicio 1: Crear tabla Productos
Crea una tabla para gestionar productos de una tienda con los siguientes campos:
- IdProducto (autoincremental)
- Nombre (obligatorio)
- Precio (decimal)
- Categoría (texto)
- Stock (número entero)
- FechaCreación (fecha automática)

### Ejercicio 2: Insertar y manipular datos
1. Inserta 5 productos diferentes
2. Actualiza el precio de 2 productos
3. Elimina 1 producto
4. Consulta solo los productos con stock mayor a 10

### Ejercicio 3: Experimentar con consultas
Prueba diferentes consultas SELECT usando:
- Renombrado de columnas con AS
- Filtros con WHERE
- Ordenamiento con ORDER BY

---

**¿Listo para continuar?** En el próximo capítulo aprenderemos sobre consultas avanzadas con WHERE, ORDER BY, y funciones de agregación.

📖 **[Ir al Capítulo 3: Modelado de Base de Datos Relacional](../modulo-03-Modelado-Base-Datos-Relacional/README.md)**