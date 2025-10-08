# 📘 Módulo 13: Reporting y Análisis de Datos
Este módulo se centra en transformar los datos operacionales de la clínica en reportes informativos y métricas clave. Aprenderemos a usar técnicas avanzadas de consulta para el análisis de tendencias, rankings y comparaciones históricas.

## 1. Generación de Reportes con Consultas Avanzadas
La base de cualquier reporte complejo es una consulta robusta que combina información, aplica filtros y agrega datos.

### **a) Reporte: Recurrencia de Procedimientos por Doctor**
Este reporte muestra cuántas veces cada doctor ha solicitado los procedimientos más comunes.

```sql
SELECT 
    D.Nombre + ' ' + D.Apellido AS Doctor,
    P.Nombre AS Procedimiento,
    COUNT(DT.IdCita) AS TotalSolicitado
FROM Doctor D
JOIN Cita_Consulta CC ON D.IdDoctor = CC.IdDoctor
JOIN Detalle_Tratamiento DT ON CC.IdCita = DT.IdCita
JOIN Procedimiento P ON DT.IdProcedimiento = P.IdProcedimiento
WHERE CC.Estado = 'Atendida'
GROUP BY D.Nombre, D.Apellido, P.Nombre
ORDER BY Doctor, TotalSolicitado DESC;
```

### **b) Reporte: Costo Acumulado por Paciente**
Calcula cuánto ha costado el total de procedimientos asociados a cada paciente.

```sql
SELECT 
    P.Nombre + ' ' + P.Apellido AS Paciente,
    SUM(Pr.Costo) AS CostoTotalAcumulado
FROM Paciente P
JOIN Cita_Consulta CC ON P.IdPaciente = CC.IdPaciente
JOIN Detalle_Tratamiento DT ON CC.IdCita = DT.IdCita
JOIN Procedimiento Pr ON DT.IdProcedimiento = Pr.IdProcedimiento
GROUP BY P.Nombre, P.Apellido
ORDER BY CostoTotalAcumulado DESC;
```


## 2. Funciones de Ventana (Window Functions)
Las Funciones de Ventana (Window Functions) realizan cálculos sobre un conjunto de filas relacionadas (la "ventana") sin colapsar las filas en el resultado. Se usan con la cláusula OVER().

### **a) ROW_NUMBER()**
Asigna un número secuencial único a cada fila dentro de una partición (grupo). Útil para clasificar eventos.

```sql
-- Listar todas las citas de Ana Torres y numerarlas en orden cronológico.
SELECT 
    CC.IdCita,
    CC.FechaHora,
    D.Nombre AS Doctor,
    ROW_NUMBER() OVER (ORDER BY CC.FechaHora ASC) AS NumeroDeCita
FROM Cita_Consulta CC
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
WHERE CC.IdPaciente = 1;
```

### **b) RANK() y DENSE_RANK()**
Asignan un rango a las filas.

**RANK():** Salta números si hay empates.

**DENSE_RANK():** No salta números si hay empates.

```sql
-- Ranking de doctores según la cantidad de citas atendidas.
SELECT
    D.Nombre + ' ' + D.Apellido AS Doctor,
    COUNT(CC.IdCita) AS CitasAtendidas,
    RANK() OVER (ORDER BY COUNT(CC.IdCita) DESC) AS Posicion
FROM Doctor D
JOIN Cita_Consulta CC ON D.IdDoctor = CC.IdDoctor
WHERE CC.Estado = 'Atendida'
GROUP BY D.Nombre, D.Apellido;
```

### **c) LEAD() y LAG()**
Permiten acceder a datos de filas anteriores (LAG) o posteriores (LEAD) dentro de una partición. Ideal para análisis de series de tiempo.

```sql
-- Calcular el tiempo (en días) transcurrido entre la cita actual y la anterior del paciente 1.
SELECT 
    CC.FechaHora AS CitaActual,
    LAG(CC.FechaHora, 1) OVER (ORDER BY CC.FechaHora) AS CitaAnterior,
    DATEDIFF(DAY, LAG(CC.FechaHora, 1) OVER (ORDER BY CC.FechaHora), CC.FechaHora) AS DiasDesdeCitaAnterior
FROM Cita_Consulta CC
WHERE CC.IdPaciente = 1
ORDER BY CC.FechaHora;
```


## 3. PIVOT y UNPIVOT (Transformación de Datos)
Permiten cambiar la orientación de los datos: de filas a columnas (PIVOT) y de columnas a filas (UNPIVOT).

### **a) PIVOT (Filas a Columnas)**
Transforma los valores únicos de una columna en múltiples columnas. Ideal para ver la distribución de una métrica (ej. conteo) por diferentes categorías.

```sql
-- Mostrar el CONTEO de citas por doctor, pivotando el Estado de la cita.
SELECT Doctor, Pendiente, Atendida, Cancelada, [No Asistio]
FROM (
    SELECT 
        D.Nombre + ' ' + D.Apellido AS Doctor, 
        CC.Estado
    FROM Cita_Consulta CC
    JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
) AS Fuente
PIVOT
(
    -- Función de agregación
    COUNT(Estado)
    -- Columna a pivotar
    FOR Estado IN ([Pendiente], [Atendida], [Cancelada], [No Asistio])
) AS Pivotado;
```

**Lógica:** Agrupa las filas de cada doctor y gira los valores de la columna Estado para que sean las nuevas columnas.

### **b) UNPIVOT (Columnas a Filas)**
Realiza la operación inversa a PIVOT, convirtiendo columnas en filas. Es útil para normalizar datos para herramientas de BI.

**Nota:** UNPIVOT requiere que las columnas que se giran (CostoAnual, CostoTrimestral) tengan el mismo tipo de dato.


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Reportes Avanzados: La creación de consultas complejas utilizando múltiples JOINS y GROUP BY para obtener métricas de negocio como costos acumulados y recurrencia.
- ✅ Funciones de Ventana: El uso de la cláusula OVER() y funciones como ROW_NUMBER(), RANK(), LEAD() y LAG() para clasificar, comparar y analizar secuencias de datos sin colapsar filas.
- ✅ Transformación de Datos: La funcionalidad de PIVOT para girar datos de filas a columnas (ej. resumir estados por doctor) y UNPIVOT para normalizar datos.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Ranking y Partición (ROW_NUMBER)**

- Crea un reporte que liste todos los procedimientos y, usando ROW_NUMBER(), asígnales un ranking basado en su Costo de forma descendente.
- Modifica la consulta anterior para usar PARTITION BY Especialidad (de la tabla Doctor) para que el ranking de costo se reinicie para cada doctor. (Asume que Procedimiento tiene una columna IdDoctor o que puedes hacer un triple JOIN).

**Ejercicio 2: Análisis Temporal (LAG)**

- Escribe una consulta que obtenga las citas atendidas de todos los pacientes.
- Utiliza la función LAG() para mostrar el IdCita de la cita anteriormente atendida por ese mismo doctor.
- Usa PARTITION BY IdDoctor para asegurar que solo se compare el historial dentro de cada doctor.

👉 **El análisis es donde el DBA se convierte en un estratega de negocio, utilizando SQL para extraer valor de los datos.**

📖 **[Ir al Modulo 14: Buenas Practicas y Estandares](/modulo-14-Buenas-Practicas/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*