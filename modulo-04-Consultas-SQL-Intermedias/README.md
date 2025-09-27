# 📘 Módulo 04: Consultas SQL Intermedias

## 1. Operadores en WHERE

Sirven para filtrar datos en las consultas.

### **Operadores de comparación:**
- `=` Igual
- `>` Mayor que
- `<` Menor que
- `>=` Mayor o igual que
- `<=` Menor o igual que
- `<>` Diferente de

```sql
-- Clientes cuyo Id sea mayor a 1
SELECT * FROM Clientes
WHERE IdCliente > 1;
```

### **BETWEEN: dentro de un rango**
```sql
-- Productos con precio entre 100 y 2000
SELECT * FROM Productos
WHERE Precio BETWEEN 100 AND 2000;
```

### **IN: buscar dentro de una lista**
```sql
-- Consultar clientes con Id 1 o 3
SELECT * FROM Clientes
WHERE IdCliente IN (1, 3);
```

### **LIKE: buscar patrones en texto**
- `%` Cualquier cantidad de caracteres
- `_` Un solo carácter

```sql
-- Buscar clientes cuyo nombre empiece con 'M'
SELECT * FROM Clientes
WHERE Nombre LIKE 'M%';

-- Buscar clientes cuyo nombre termine con 'ez'
SELECT * FROM Clientes
WHERE Nombre LIKE '%ez';

-- Buscar clientes cuyo nombre tenga 'a' como segunda letra
SELECT * FROM Clientes
WHERE Nombre LIKE '_a%';
```

### **IS NULL / IS NOT NULL**
```sql
-- Clientes que no tengan teléfono registrado
SELECT * FROM Clientes
WHERE Telefono IS NULL;

-- Clientes que sí tengan teléfono registrado
SELECT * FROM Clientes
WHERE Telefono IS NOT NULL;
```

## 2. Funciones de texto, matemáticas y de fecha

### **Funciones de texto**
```sql
-- Convertir a mayúsculas
SELECT UPPER(Nombre) AS NombreMayus FROM Clientes;

-- Convertir a minúsculas
SELECT LOWER(Nombre) AS NombreMinus FROM Clientes;

-- Extraer primeros 5 caracteres
SELECT LEFT(Nombre, 5) AS Prefijo FROM Clientes;

-- Extraer últimos 5 caracteres
SELECT RIGHT(Nombre, 5) AS Sufijo FROM Clientes;

-- Longitud del texto
SELECT LEN(Nombre) AS LongitudNombre FROM Clientes;

-- Reemplazar texto
SELECT REPLACE(Nombre, 'á', 'a') AS NombreSinAcentos FROM Clientes;
```

### **Funciones matemáticas**
```sql
-- Precio con 18% de IGV (Impuesto General a las Ventas en Perú)
SELECT Nombre, Precio, Precio * 0.18 AS IGV FROM Productos;

-- Redondear a 2 decimales
SELECT ROUND(Precio, 2) AS PrecioRedondeado FROM Productos;

-- Valor absoluto
SELECT ABS(Precio) AS ValorAbsoluto FROM Productos;

-- Potencia (precio al cuadrado)
SELECT POWER(Precio, 2) AS PrecioCuadrado FROM Productos;
```

### **Funciones de fecha y hora**
```sql
-- Fecha actual
SELECT GETDATE() AS Hoy;

-- Año y mes de la factura
SELECT IdFactura, YEAR(Fecha) AS Año, MONTH(Fecha) AS Mes FROM Facturas;

-- Día de la semana (1=domingo, 2=lunes, etc.)
SELECT DATEPART(WEEKDAY, Fecha) AS DiaSemana FROM Facturas;

-- Diferencia en días entre fechas
SELECT DATEDIFF(DAY, Fecha, GETDATE()) AS DiasTranscurridos FROM Facturas;

-- Formatear fecha
SELECT FORMAT(Fecha, 'dd/MM/yyyy') AS FechaFormateada FROM Facturas;
```

## 3. Ordenar resultados con ORDER BY

```sql
-- Ordenar clientes por nombre alfabéticamente (A-Z)
SELECT * FROM Clientes
ORDER BY Nombre ASC;

-- Ordenar clientes por nombre descendente (Z-A)
SELECT * FROM Clientes
ORDER BY Nombre DESC;

-- Ordenar productos del más caro al más barato
SELECT * FROM Productos
ORDER BY Precio DESC;

-- Ordenar por múltiples columnas
SELECT * FROM Facturas
ORDER BY Fecha DESC, IdFactura ASC;
```

## 4. Limitar resultados con TOP

```sql
-- Mostrar solo el producto más caro
SELECT TOP 1 * FROM Productos
ORDER BY Precio DESC;

-- Mostrar los 3 productos más caros
SELECT TOP 3 * FROM Productos
ORDER BY Precio DESC;

-- Mostrar el 50% de los registros
SELECT TOP 50 PERCENT * FROM Productos
ORDER BY Precio DESC;
```

## 5. Eliminar duplicados con DISTINCT

```sql
-- Mostrar solo los clientes únicos que han hecho facturas
SELECT DISTINCT c.Nombre
FROM Clientes c
JOIN Facturas f ON c.IdCliente = f.IdCliente;

-- DISTINCT con múltiples columnas
SELECT DISTINCT c.Nombre, c.Email
FROM Clientes c
JOIN Facturas f ON c.IdCliente = f.IdCliente;
```

## 6. Uso de alias con AS

```sql
-- Cambiar nombre de columnas en la salida
SELECT c.Nombre AS Cliente, f.IdFactura AS Factura, f.Fecha
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente;

-- Alias para tablas (útil en consultas complejas)
SELECT cli.Nombre AS Cliente, fac.Fecha AS FechaFactura
FROM Facturas fac
JOIN Clientes cli ON fac.IdCliente = cli.IdCliente;

-- Alias en cálculos
SELECT Nombre, Precio, (Precio * 1.18) AS PrecioConIGV
FROM Productos;
```

## 7. Ejercicios prácticos sobre el sistema de ventas

Trabajemos con los datos de prueba insertados en el Capítulo 3.

### **a) Listar todos los clientes que tengan correo con dominio "@mail.com"**
```sql
SELECT * FROM Clientes
WHERE Email LIKE '%@mail.com';
```

### **b) Mostrar los productos cuyo precio sea mayor a 1000 ordenados de mayor a menor**
```sql
SELECT Nombre, Precio
FROM Productos
WHERE Precio > 1000
ORDER BY Precio DESC;
```

### **c) Consultar el detalle de facturas mostrando: Cliente, Producto, Cantidad y Total**
```sql
SELECT c.Nombre AS Cliente, p.Nombre AS Producto,
       fd.Cantidad, p.Precio,
       (fd.Cantidad * p.Precio) AS Total
FROM FacturaDetalle fd
JOIN Facturas f ON fd.IdFactura = f.IdFactura
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN Productos p ON fd.IdProducto = p.IdProducto;
```

📌 **Resultado esperado:**

```yaml
Cliente    | Producto        | Cantidad | Precio | Total
Juan Pérez | Laptop Lenovo   | 1        | 2500   | 2500
Juan Pérez | Mouse Logitech  | 2        | 80     | 160
```

### **d) Mostrar el cliente con la última factura registrada**
```sql
SELECT TOP 1 c.Nombre, f.IdFactura, f.Fecha
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
ORDER BY f.Fecha DESC;
```

## ✅ Resumen del capítulo

**Ahora sabes cómo:**

- ✅ Filtrar datos con operadores avanzados (WHERE, IN, LIKE, BETWEEN, IS NULL)
- ✅ Utilizar funciones de texto, matemáticas y fechas para transformar datos
- ✅ Ordenar resultados con ORDER BY (ascendente y descendente)
- ✅ Limitar resultados con TOP para obtener registros específicos
- ✅ Eliminar duplicados con DISTINCT para datos únicos
- ✅ Usar alias con AS para mejorar la legibilidad del código
- ✅ Realizar consultas complejas sobre sistemas reales

---

## 🎯 Ejercicios de práctica

⚠️ **Nota importante**: Puedes crear una nueva Base de Datos como se recomendo en el módulo 03 e insertar datos con ayuda de IA (puedes pedirle por ejemplo a la IA que te mande 10 registros para que empieces a hacer tus propias pruebas) eso si tienes que mostrarle el codigo de tus tablas para que te mande los registros acorde a tus tablas.

### Ejercicio 1: Consultas de filtrado
1. Encuentra todos los productos con precio entre 50 y 1000
2. Busca clientes cuyo nombre contenga la letra "a"
3. Lista las facturas del último mes

### Ejercicio 2: Funciones y formatos
1. Muestra los nombres de clientes en mayúsculas con su teléfono
2. Calcula el precio con IGV (18%) para cada producto
3. Formatea las fechas de factura como "dd/MM/yyyy"

### Ejercicio 3: Ordenamiento y límites
1. Muestra los 5 productos más baratos
2. Ordena los clientes por nombre descendente
3. Encuentra la factura más antigua

---

👉 **En el próximo capítulo aprenderemos sobre JOIN, Subconaultas, GROUP BY y HAVING para agregaciones avanzadas de datos.**

📖 **[Ir al Módulo 05: Consultas SQL Avanzadas](/modulo-05-Consultas-SQL-Avanzadas/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*