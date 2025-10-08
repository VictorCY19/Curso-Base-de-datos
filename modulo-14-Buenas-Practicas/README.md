# 📘 Módulo 14: Buenas Prácticas y Estándares
¡Felicidades! Después de cubrir la creación, consulta, optimización y administración de bases de datos, este módulo final te equipará con los estándares profesionales necesarios para trabajar en cualquier entorno de desarrollo o DBA.

## 1. Convenciones de Nombres en Bases de Datos (Estándares)
La consistencia en la nomenclatura es clave para la legibilidad y el mantenimiento.

### **a) Tablas y Vistas**

**Convención:** Usar nombres singulares (evita la pluralización automática).

**Estilo:** PascalCase (cada palabra empieza con mayúscula) o snake_case (palabras separadas por guiones bajos).

**Ejemplo (usado en el curso):** Paciente, Cita_Consulta.

**Ejemplo (alternativo):** DoctorEspecialidad, Cita_Consulta.

**Prefijos:** Evita prefijos como tbl_ o vw_; SQL Server ya define el tipo de objeto.

### **b) Columnas**

**Estilo:** Similar al de tablas (PascalCase o snake_case).

**Identificadores (IDs):** Usar siempre Id + Nombre del Objeto (Ej: IdPaciente, IdDoctor).

**Llaves Foráneas (FK):** Nombrarlas usando la convención del objeto referenciado (Ej: IdDoctor, IdProcedimiento).

### **c) Objetos Programables (SP, Funciones, Triggers)**

**Prefijos Recomendados:** Es una de las pocas excepciones donde los prefijos ayudan a identificar rápidamente el objeto.

**SP (Procedimientos Almacenados):** `sp_ o usp_` (User Stored Procedure).

Ej: usp_InsertarNuevoPaciente.

**Funciones:** `fn_`

Ej: fn_CalcularEdadPaciente.

**Triggers:** `trg_ + Nombre de la Tabla + Acción` (I/U/D).

Ej: trg_Cita_Consulta_AI (After Insert).


## 2. Documentación de Procedimientos y Vistas
Un código no documentado es un código roto. La documentación básica se hace directamente dentro del código SQL.

### **a) Documentación de Procedimientos Almacenados (Headers)**
Incluir un encabezado (header) con metadatos clave en cada objeto programable.

```sql
/*******************************************************
-- Procedimiento: usp_ObtenerHistorialPaciente
-- Descripción: Obtiene el historial completo de citas y procedimientos de un paciente.
-- Autor: [Tu Nombre]
-- Fecha Creación: 2025-10-07
-- Parámetros: 
--   @IdPaciente (INT): ID único del paciente a consultar.
-- Notas: Utiliza LEFT JOIN para incluir citas sin procedimientos.
********************************************************/
CREATE PROCEDURE usp_ObtenerHistorialPaciente
    @IdPaciente INT
AS
BEGIN
    -- [Cuerpo del procedimiento]
END
```

### **b) Comentarios en Línea**
Utiliza `-- o /* */` para explicar lógica compleja, reglas de negocio o JOINS delicados.

```sql
-- Calcula el costo total de los procedimientos asociados a la cita.
SELECT 
    SUM(Pr.Costo) 
FROM Detalle_Tratamiento DT
JOIN Procedimiento Pr ON DT.IdProcedimiento = Pr.IdProcedimiento
WHERE DT.IdCita = @IdCita; /* Asegura que la suma es atómica */
```


## 3. Estrategias de Versionamiento de Bases de Datos (Migrations)
A diferencia del código de aplicación, las bases de datos deben ser versionadas cuidadosamente, ya que un cambio afecta a los datos existentes.

**Problema:** Alguien añade una columna en el entorno de Desarrollo, pero olvida añadirla en Producción.

**Solución (Migrations):** Usar herramientas (como Flyway, Liquibase o Azure DevOps/GitHub Actions) que guardan todos los cambios de esquema (DDL) y datos (DML) en archivos SQL versionados (Ej: V1.0.1__añadir_campo_email_doctor.sql).

**Principio:** La base de datos siempre evoluciona hacia adelante aplicando los scripts faltantes en orden numérico. Esto garantiza que todos los entornos (Desarrollo, Prueba, Producción) tengan el mismo esquema.


## 4. Optimización de Consultas Paso a Paso
Un plan de optimización estructurado asegura que las mejoras de rendimiento sean duraderas.

**Identificar el cuello de botella:** Usar el Monitor de Actividad y el Plan de Ejecución de SSMS para encontrar las consultas más lentas (alto costo de I/O o CPU).

**Analizar el Plan de Ejecución:** Buscar operaciones costosas como Table Scan (escanear toda la tabla) o Key Lookups (saltar entre índice y tabla).

**Crear/Ajustar Índices:** La solución más común es crear el índice no-clustered que soporte los filtros (WHERE) y las uniones (JOIN) de la consulta lenta.

**Reescribir la Consulta:** Simplificar la lógica, evitar SELECT *, evitar cursores si es posible, y mover cálculos complejos a CTEs o subconsultas.

**Actualizar Estadísticas:** Si el índice ya existe pero el rendimiento es malo, a veces SQL Server tiene estadísticas desactualizadas sobre la distribución de datos. Ejecutar `UPDATE STATISTICS [NombreTabla];` ayuda al optimizador.


## 5. Checklist de un DBA Junior en el Mundo Laboral
Un resumen de las tareas que debes dominar para entrar al mundo laboral como DBA junior.

| **Área**         | **Tarea Esencial**                                                           | **Módulo Relacionado**   |
|------------------|------------------------------------------------------------------------------|---------------------------|
| **Integridad**   | Entender y usar `BEGIN TRAN`, `COMMIT`, `ROLLBACK` y `TRY...CATCH`.          | Módulo 09                |
| **Seguridad**    | Asignar permisos con `GRANT`/`REVOKE` y crear Roles y Usuarios.              | Módulo 08                |
| **Mantenimiento**| Configurar y probar Backups (Full/Differential/Log).                         | Módulo 11                |
| **Rendimiento**  | Analizar el Plan de Ejecución y crear/mantener Índices clave.                | Módulo 07                |
| **Automatización**| Crear y modificar Procedimientos Almacenados y Triggers sencillos.          | Módulo 06, 10            |
| **Modelado**     | Diseñar modelos relacionales con Normalización (Formas Normales).            | Módulo 02, 03            |
| **Datos**        | Usar `BULK INSERT` y asistentes de importación/exportación.                  | Módulo 12                |


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Convenciones de Nombres: Aplicar estándares profesionales (PascalCase, snake_case) y el uso de prefijos en objetos programables (usp_, fn_).
- ✅ Documentación: La importancia de documentar los objetos con headers y comentarios para un mantenimiento eficiente.
- ✅ Versionamiento: La necesidad de usar Estrategias de Migración para mantener la consistencia del esquema a través de entornos.
- ✅ Optimización: El proceso de 4 pasos para optimizar consultas (Identificar, Analizar Plan, Indexar, Reescribir).
- ✅ Checklist DBA: Un listado de las tareas críticas que debe dominar un DBA junior para asegurar la estabilidad y el rendimiento de la base de datos.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Estandarización de Objetos**

- El procedimiento que inserta un nuevo doctor en la tabla Doctor se llama actualmente InsertarDoctor. Renómbralo a la convención profesional que aprendiste en este módulo (Ej: usp_Doctor_Insertar).
- Crea un nuevo header de documentación para el procedimiento renombrado, incluyendo descripción, parámetros, autor y fecha de creación.

**Ejercicio 2: Optimización con Plan de Ejecución**

- Escribe una consulta en tu sistema de la clínica que realice una unión compleja (Ej: Paciente -> Cita_Consulta -> Detalle_Tratamiento -> Procedimiento).
- Muestra el Plan de Ejecución (simulado, si no estás en SSMS) y describe qué índices considerarías para mejorar el rendimiento si vieras un Table Scan en la tabla Paciente.

👉 **¡Felicidades, has completado el curso! Ahora tienes las herramientas no solo para crear, sino para administrar y optimizar bases de datos como un profesional.**

📖 **[Ir al Modulo 15: Proyecto Final](/modulo-15-Proyecto-Final/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*