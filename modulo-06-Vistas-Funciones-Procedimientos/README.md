# 📘 Módulo 06: Vistas, Funciones y Procedimientos

Este módulo introduce herramientas esenciales para la programación en SQL Server. Las Vistas, Funciones y Procedimientos Almacenados no solo mejoran el rendimiento, sino que también son cruciales para la seguridad y la legibilidad del código. Nos permiten encapsular lógica de negocio compleja en bloques reutilizables.

⚠️ **Nota importante**: Usaremos la BD Salud_Vital que realizamos como ejercicio


## 1. Vistas (VIEWS)

Una Vista es una tabla virtual basada en el conjunto de resultados de una consulta SQL. No almacena datos por sí misma, sino que actúa como una capa de abstracción para simplificar consultas complejas y limitar el acceso a ciertos datos.

### **a) Creación de Vistas (Simplificación)**
Usamos una vista para combinar las tablas Paciente, Doctor y Cita_Consulta, facilitando el historial de atenciones.

```sql
CREATE VIEW Vista_Citas_Atendidas_Detalle AS
SELECT 
    CC.IdCita, 
    P.Nombre AS Paciente, 
    P.Apellido AS ApellidoPaciente,
    D.Nombre AS Doctor, 
    D.Especialidad,
    CC.FechaHora
FROM Cita_Consulta CC
JOIN Paciente P ON CC.IdPaciente = P.IdPaciente
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
WHERE CC.Estado = 'Atendida';

```

### **b) Uso de la vista**
Una vez creada, se consulta como si fuera una tabla normal.

```sql
-- Consultar el historial de todas las consultas atendidas:
SELECT * FROM Vista_Citas_Atendidas_Detalle
ORDER BY FechaHora DESC;
```

📌 Resultado esperado (basado en la data insertada):

```yaml
IdCita | Paciente | ApellidoPaciente | Doctor | Especialidad | FechaHora 
1      | Ana      | Torres           | Elena  | Medicina General | 2025-10-05 10:00:00 
```

### **c) Creación de Vistas (Cálculos)**
Las vistas son perfectas para encapsular cálculos complejos, como el costo total de los procedimientos asociados a una cita.

```sql
CREATE VIEW Vista_Costo_Tratamiento AS
SELECT 
    CC.IdCita, 
    CC.FechaHora, 
    P.Nombre AS Paciente,
    SUM(Pr.Costo) AS CostoTotalTratamientos
FROM Cita_Consulta CC
JOIN Paciente P ON CC.IdPaciente = P.IdPaciente
JOIN Detalle_Tratamiento DT ON CC.IdCita = DT.IdCita
JOIN Procedimiento Pr ON DT.IdProcedimiento = Pr.IdProcedimiento
GROUP BY CC.IdCita, CC.FechaHora, P.Nombre;
```


## 2. Funciones (FUNCTIONS)

Las Funciones en SQL están diseñadas para devolver un único valor (o una tabla) y pueden ser utilizadas directamente dentro de consultas SELECT, WHERE o HAVING. A diferencia de los procedimientos, son ideales para cálculos y validaciones.

### **a) Función de Cálculo (Escalar)**
Esta función calcula la edad de un paciente en años a partir de su fecha de nacimiento.

```sql 
--Nombramos a la funcion y declaramos la variable
CREATE FUNCTION fn_Calcular_Edad (@FechaNacimiento DATE)  
RETURNS INT --Declaramos el tipo de dato de salida, en este caso un numero entero
AS
BEGIN
    DECLARE @Edad INT; --Creamos nuestra variable edad y declaramos que tipo de dato es
    SET @Edad = DATEDIFF(year, @FechaNacimiento, GETDATE()); --declaramos year para que solo tome el año y colocamos los dos años que se deben restar
    
    -- Ajuste si el cumpleaños de este año aún no ha pasado
    IF (MONTH(@FechaNacimiento) > MONTH(GETDATE()) OR 
        (MONTH(@FechaNacimiento) = MONTH(GETDATE()) AND DAY(@FechaNacimiento) > DAY(GETDATE())))
    BEGIN
        SET @Edad = @Edad - 1; -- En caso el cumpleaños no ha pasado le quitamos un año y actualizamos la nueva edad
    END
    
    RETURN @Edad; 
END;
```

### **b) Uso de la funcion**
Se utiliza directamente en la cláusula SELECT, precedida por el esquema (dbo. en SQL Server).

```sql 
-- Listar pacientes y su edad actual
SELECT 
    Nombre, 
    Apellido, 
    Fecha_Nacimiento, 
    dbo.fn_Calcular_Edad(Fecha_Nacimiento) AS EdadActual
FROM Paciente;
```

# 📌 Resultado esperado (ejemplo si hoy fuera 2025-10-05):

```yaml
Nombre | Apellido | Fecha_Nacimiento | EdadActual
Ana    | Torres   | 1985-05-20       | 40
Luis   | Gómez    | 1992-11-10       | 32
Elena  | Pérez    | 1970-01-15       | 55
```


## 3. Procedimientos Almacenados (STORED PROCEDURES)

Un Procedimiento Almacenado es un conjunto de sentencias SQL precompiladas que se almacenan en la base de datos. Se utilizan para ejecutar tareas que requieren lógica de negocio, como insertar, actualizar o eliminar datos.

### **a) Procedimiento para Inserción de Datos**
Este procedimiento centraliza la lógica para registrar una nueva cita. Recibe parámetros y ejecuta la acción.

```sql
CREATE PROCEDURE sp_Registrar_Cita
    -- Parametros de Entrada
    @IdPaciente INT,
    @IdDoctor INT,
    @FechaHora DATETIME
AS
BEGIN
    -- Evita que se devuelva el número de filas afectadas
    SET NOCOUNT ON; 
    
    -- Lógica de negocio: Insertar una nueva cita en estado 'Pendiente'
    INSERT INTO Cita_Consulta (IdPaciente, IdDoctor, FechaHora, Estado)
    VALUES (@IdPaciente, @IdDoctor, @FechaHora, 'Pendiente');
    
    PRINT 'Cita registrada exitosamente.';
END;
```

### **b) Ejecución del Procedimiento**
Se llama usando el comando EXEC o EXECUTE.

```sql
-- Ejecutar el procedimiento para el Paciente 3 con el Doctor 2
EXEC sp_Registrar_Cita 
    @IdPaciente = 3, 
    @IdDoctor = 2, 
    @FechaHora = '2025-11-15 08:00:00';
```

### **c) Procedimiento para Reporte con Filtro**
Este procedimiento devuelve el listado de las citas pendientes para un doctor específico, facilitando la agenda del médico.

```sql
CREATE PROCEDURE sp_Obtener_Citas_Pendientes_Doctor
    @IdDoctor INT -- Parametro para filtrar por doctor
AS
BEGIN
    SET NOCOUNT ON;
    
    -- Consulta para obtener las citas pendientes
    SELECT 
        CC.IdCita, 
        P.Nombre AS Paciente, 
        P.Apellido, 
        CC.FechaHora
    FROM Cita_Consulta CC
    JOIN Paciente P ON CC.IdPaciente = P.IdPaciente
    WHERE CC.IdDoctor = @IdDoctor 
      AND CC.Estado = 'Pendiente'
    ORDER BY CC.FechaHora;
END;
```

### **d) Ejecución del Procedimiento de Reporte**

```sql
-- Obtener las citas pendientes del Doctor 1 (Dr. Carlos Mata)
EXEC sp_Obtener_Citas_Pendientes_Doctor @IdDoctor = 1;
```

# 📌 Resultado esperado (para Doctor 1):

```yaml
IdCita | Paciente | Apellido | FechaHora
2      | Luis     | Gómez    | 2025-10-07 15:30:00
```


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Vistas: Crear tablas virtuales para simplificar consultas y encapsular lógica
- ✅ Funciones: Crear bloques de código que devuelven un valor para usarse en consultas
- ✅ Procedimientos Almacenados: Crear bloques de código para automatizar tareas y lógica de negocio
- ✅ Parámetros: Usar @variables para hacer el código dinámico y reutilizable

---

## 🎯 Ejercicios de práctica

### Ejercicio 1: Vistas
1. Crea una vista llamada `Vista_Doctores_Disponibles` que solo muestre el Nombre, Apellido y Especialidad de los doctores.
2. Crea una vista llamada `Vista_Resumen_Cita` que muestre el IdCita, el nombre completo del paciente, el nombre completo del doctor y el Estado.

### Ejercicio 2: Funciones
1. Crea una función escalar `fn_Obtener_Costo_Procedimiento` que reciba el IdProcedimiento y devuelva el Costo de ese procedimiento

### Ejercicio 3: Procedimientos Almacenados
1. Crea un procedimiento `sp_Actualizar_Estado_Cita` que reciba IdCita y un @NuevoEstado (NVARCHAR(20)) y actualice el campo Estado de la tabla Cita_Consulta.
2. Crea un procedimiento `sp_Registrar_Tratamiento` que reciba IdCita e IdProcedimiento y realice la inserción en la tabla Detalle_Tratamiento

👉 **Con estas herramientas, has pasado de ser un simple consultor de datos a un programador de bases de datos. Ahora puedes crear una capa de lógica de negocio robusta para tu sistema de clínica.**

📖 **[Ir al Modulo 07: Indices y Rendimiento](/modulo-07-Indice-Rendimiento/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*






