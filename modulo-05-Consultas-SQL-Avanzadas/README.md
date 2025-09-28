# 📘 Módulo 5: Consultas SQL Avanzadas

## 1. JOINS (Uniones entre tablas)

Permiten relacionar información de varias tablas.

### **a) INNER JOIN**
Muestra registros solo cuando hay coincidencia en ambas tablas.

👉 **Nota importante**: Cuando se escribe solo `JOIN`, SQL Server lo interpreta como `INNER JOIN`.

```sql
-- Forma explícita
SELECT f.IdFactura, f.Fecha, c.Nombre AS Cliente
FROM Facturas f
INNER JOIN Clientes c ON f.IdCliente = c.IdCliente;

-- Forma abreviada (JOIN = INNER JOIN)
SELECT f.IdFactura, f.Fecha, c.Nombre AS Cliente
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente;
```

📌 **Resultado esperado:**
```yaml
IdFactura | Fecha      | Cliente
1         | 2025-09-25 | Juan Pérez
```

### **b) LEFT JOIN**
Muestra todos los registros de la tabla izquierda, aunque no tenga coincidencias en la derecha.

```sql
-- Listar todos los clientes y sus facturas (si tienen)
SELECT c.Nombre, f.IdFactura, f.Fecha
FROM Clientes c
LEFT JOIN Facturas f ON c.IdCliente = f.IdCliente;
```

📌 **Resultado esperado:**
```yaml
Cliente     | IdFactura | Fecha
Juan Pérez  | 1         | 2025-09-25
María López | NULL      | NULL
Carlos Ruiz | NULL      | NULL
```

### **c) RIGHT JOIN**
Muestra todos los registros de la tabla derecha, aunque no tenga coincidencias en la izquierda.

```sql
-- Mostrar todas las facturas con sus clientes
SELECT f.IdFactura, f.Fecha, c.Nombre
FROM Facturas f
RIGHT JOIN Clientes c ON f.IdCliente = c.IdCliente;
```

### **d) FULL OUTER JOIN**
Muestra todos los registros de ambas tablas, coincidan o no.

```sql
-- Unir clientes y facturas (incluso sin relación)
SELECT c.Nombre, f.IdFactura, f.Fecha
FROM Clientes c
FULL OUTER JOIN Facturas f ON c.IdCliente = f.IdCliente;
```

### **📊 Resumen visual de JOINS:**
```
INNER JOIN:    Sólo coincidencias en A y B
LEFT JOIN:     Todos de A + coincidencias de B
RIGHT JOIN:    Todos de B + coincidencias de A
FULL JOIN:     Todos de A y todos de B
```

## 2. Subconsultas

Son consultas dentro de otras consultas.

### **a) Subconsulta en WHERE**
```sql
-- Mostrar clientes que han hecho facturas
SELECT * FROM Clientes
WHERE IdCliente IN (SELECT IdCliente FROM Facturas);

-- Productos más caros que el promedio
SELECT * FROM Productos
WHERE Precio > (SELECT AVG(Precio) FROM Productos);
```

### **b) Subconsulta en FROM**
```sql
-- Obtener el total por factura usando una subconsulta
SELECT t.IdFactura, t.TotalFactura
FROM (
    SELECT f.IdFactura, SUM(fd.Cantidad * p.Precio) AS TotalFactura
    FROM Facturas f
    JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
    JOIN Productos p ON fd.IdProducto = p.IdProducto
    GROUP BY f.IdFactura
) t;
```

### **c) Subconsulta en SELECT**
```sql
-- Mostrar cada producto con su diferencia respecto al precio promedio
SELECT Nombre, Precio,
       (SELECT AVG(Precio) FROM Productos) AS PrecioPromedio,
       Precio - (SELECT AVG(Precio) FROM Productos) AS Diferencia
FROM Productos;
```

## 3. GROUP BY y funciones de agregación

### **📝 ¿Cómo funciona GROUP BY?**
GROUP BY agrupa filas que tienen los mismos valores en las columnas especificadas, permitiendo aplicar funciones de agregación (SUM, COUNT, AVG, etc.) a cada grupo.

**Ejemplo conceptual:**
```
Datos originales:
Cliente    | Producto   | Cantidad
Juan Pérez | Laptop     | 1
Juan Pérez | Mouse      | 2
María López| Laptop     | 1

GROUP BY Cliente:
Grupo 1: Juan Pérez
  - SUM(Cantidad) = 3
  - COUNT(Producto) = 2

Grupo 2: María López
  - SUM(Cantidad) = 1
  - COUNT(Producto) = 1
```

### **a) Total gastado por cliente**
```sql
SELECT c.Nombre, SUM(fd.Cantidad * p.Precio) AS TotalGastado
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY c.Nombre;
```

📌 **Resultado esperado:**
```yaml
Cliente    | TotalGastado
Juan Pérez | 2660
```

### **b) Número de facturas por cliente**
```sql
SELECT c.Nombre, COUNT(f.IdFactura) AS CantidadFacturas
FROM Clientes c
LEFT JOIN Facturas f ON c.IdCliente = f.IdCliente
GROUP BY c.Nombre;
```

📌 **Resultado esperado:**
```yaml
Cliente     | CantidadFacturas
Juan Pérez  | 1
María López | 0
Carlos Ruiz | 0
```

### **c) Funciones de agregación comunes:**
```sql
-- Múltiples agregaciones en una consulta
SELECT 
    COUNT(*) AS TotalProductos,
    AVG(Precio) AS PrecioPromedio,
    MAX(Precio) AS PrecioMaximo,
    MIN(Precio) AS PrecioMinimo,
    SUM(Precio) AS SumaTotal
FROM Productos;
```

## 4. HAVING (filtrar agrupaciones)

Se usa junto con GROUP BY para filtrar resultados agrupados.

```sql
-- Mostrar clientes que hayan gastado más de 2000
SELECT c.Nombre, SUM(fd.Cantidad * p.Precio) AS TotalGastado
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY c.Nombre
HAVING SUM(fd.Cantidad * p.Precio) > 2000;
```

📌 **Resultado:**
```yaml
Cliente    | TotalGastado
Juan Pérez | 2660
```

### **📝 Diferencia entre WHERE y HAVING:**
- **WHERE** filtra filas **antes** de la agrupación
- **HAVING** filtra grupos **después** de la agrupación

```sql
-- WHERE + HAVING en la misma consulta
SELECT c.Nombre, SUM(fd.Cantidad * p.Precio) AS TotalGastado
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
WHERE p.Precio > 50  -- Filtra productos caros antes de agrupar
GROUP BY c.Nombre
HAVING SUM(fd.Cantidad * p.Precio) > 1000;  -- Filtra clientes que gastaron mucho
```

## 5. Common Table Expressions (CTE)

Son consultas temporales que mejoran la legibilidad.

### **CTE básico:**
```sql
-- Usar un CTE para calcular total por factura y luego filtrar
WITH Totales AS (
    SELECT f.IdFactura, c.Nombre,
           SUM(fd.Cantidad * p.Precio) AS TotalFactura
    FROM Facturas f
    JOIN Clientes c ON f.IdCliente = c.IdCliente
    JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
    JOIN Productos p ON fd.IdProducto = p.IdProducto
    GROUP BY f.IdFactura, c.Nombre
)
SELECT * FROM Totales
WHERE TotalFactura > 2000;
```

📌 **Resultado:**
```yaml
IdFactura | Nombre     | TotalFactura
1         | Juan Pérez | 2660
```

### **Múltiples CTEs:**
```sql
WITH 
ClientesActivos AS (
    SELECT * FROM Clientes WHERE Email IS NOT NULL
),
VentasPorCliente AS (
    SELECT c.Nombre, COUNT(f.IdFactura) AS TotalVentas
    FROM ClientesActivos c
    LEFT JOIN Facturas f ON c.IdCliente = f.IdCliente
    GROUP BY c.Nombre
)
SELECT * FROM VentasPorCliente
WHERE TotalVentas > 0;
```

## 6. Nomenclatura y uso de alias en SQL

### **¿Por qué usar alias?**
- **Claridad**: En consultas con muchas tablas, es más fácil leer `c.Nombre` que `Clientes.Nombre`
- **Brevedad**: Se escriben menos caracteres
- **Buenas prácticas**: Común en entornos profesionales

### **Convenciones comunes de alias:**
```sql
-- Alias descriptivos
SELECT cli.Nombre AS Cliente, fac.Fecha AS FechaFactura
FROM Clientes cli
JOIN Facturas fac ON cli.IdCliente = fac.IdCliente;

-- Alias cortos (muy comunes)
SELECT c.Nombre, f.IdFactura, p.Nombre AS Producto
FROM Clientes c
JOIN Facturas f ON c.IdCliente = f.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto;
```

### **Ejemplo comparativo:**
```sql
-- Sin alias (correcto pero largo)
SELECT Clientes.Nombre, Facturas.IdFactura
FROM Clientes
JOIN Facturas ON Clientes.IdCliente = Facturas.IdCliente;

-- Con alias (más claro y profesional)
SELECT c.Nombre, f.IdFactura
FROM Clientes c
JOIN Facturas f ON c.IdCliente = f.IdCliente;
```

## 7. Ejercicios prácticos del sistema de ventas

### **a) ¿Cuál es el producto más vendido (por cantidad total)?**
```sql
SELECT p.Nombre, SUM(fd.Cantidad) AS CantidadVendida
FROM FacturaDetalle fd
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY p.Nombre
ORDER BY CantidadVendida DESC;
```

### **b) ¿Qué cliente ha gastado más dinero en total?**
```sql
SELECT TOP 1 c.Nombre, SUM(fd.Cantidad * p.Precio) AS TotalGastado
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY c.Nombre
ORDER BY TotalGastado DESC;
```

### **c) Mostrar todas las facturas con su total calculado**
```sql
SELECT f.IdFactura, c.Nombre,
       SUM(fd.Cantidad * p.Precio) AS TotalFactura
FROM Facturas f
JOIN Clientes c ON f.IdCliente = c.IdCliente
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY f.IdFactura, c.Nombre;
```

### **d) Productos que nunca se han vendido**
```sql
SELECT p.Nombre, p.Precio
FROM Productos p
LEFT JOIN FacturaDetalle fd ON p.IdProducto = fd.IdProducto
WHERE fd.IdProducto IS NULL;
```

### **e) Ventas mensuales**
```sql
SELECT 
    YEAR(Fecha) AS Año,
    MONTH(Fecha) AS Mes,
    COUNT(*) AS Facturas,
    SUM(fd.Cantidad * p.Precio) AS TotalVentas
FROM Facturas f
JOIN FacturaDetalle fd ON f.IdFactura = fd.IdFactura
JOIN Productos p ON fd.IdProducto = p.IdProducto
GROUP BY YEAR(Fecha), MONTH(Fecha)
ORDER BY Año, Mes;
```

## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Diferentes tipos de JOINS (INNER, LEFT, RIGHT, FULL)
- ✅ Subconsultas en WHERE, FROM y SELECT
- ✅ Cómo funciona GROUP BY y agrupación de datos
- ✅ Agrupaciones con GROUP BY y filtros con HAVING
- ✅ CTE para consultas más limpias y organizadas
- ✅ Uso profesional de alias y nomenclatura
- ✅ Funciones de agregación avanzadas

---

## 🎯 Ejercicios de práctica

### Ejercicio 1: JOINS avanzados
1. Encuentra todos los clientes que nunca han comprado
2. Lista productos que han sido vendidos más de una vez
3. Muestra facturas con productos de más de un cliente

### Ejercicio 2: Agregaciones complejas
1. Calcula el ticket promedio por cliente
2. Encuentra el mes con más ventas
3. Identifica productos con ventas por encima del promedio

### Ejercicio 3: CTE y subconsultas
1. Usa CTE para calcular rankings de clientes por gasto
2. Combina múltiples CTEs para un reporte completo

---

👉 **Ahora tu sistema de ventas puede responder preguntas de negocio reales como:**
- ¿Quién es el mejor cliente?
- ¿Qué producto se vende más?
- ¿Cuál fue la factura más alta?
- ¿Cuál es la tendencia de ventas mensual?

📖 **[Ir al Capítulo 6: Vistas, Funciones y Procedimientos](/modulo-06-Vistas-Funciones-Procedimientos/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*