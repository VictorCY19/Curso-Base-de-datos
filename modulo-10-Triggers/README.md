# 📘 Módulo 10: Triggers
Los Triggers (o Disparadores) son un tipo especial de procedimiento almacenado que se ejecuta automáticamente cuando ocurre un evento específico en la base de datos (INSERT, UPDATE, DELETE). Son herramientas poderosas para automatizar reglas de negocio y asegurar la integridad de datos a bajo nivel.


## 1. ¿Qué es un Trigger?
Un Trigger es un código SQL que se adjunta a una tabla. Cuando se realiza una operación de modificación de datos (DML) en esa tabla, el trigger se "dispara" o se ejecuta.

**Tablas Virtuales `Inserted` y `Deleted`**
Dentro del cuerpo de un trigger, SQL Server crea dos tablas virtuales temporales muy importantes:

| **Tabla Virtual** | **Contenido**                                                        | **Se usa en**                                   |
|--------------------|----------------------------------------------------------------------|-------------------------------------------------|
| **Inserted**       | Contiene las filas de datos después de una operación INSERT o UPDATE. | Para auditar los nuevos valores.                |
| **Deleted**        | Contiene las filas de datos antes de una operación DELETE o UPDATE.  | Para auditar los valores antiguos o validar eliminaciones. |

⚠️ **Nota Importante:** En un `UPDATE`, la tabla `Deleted` tiene los valores viejos y la tabla `Inserted` tiene los valores nuevos.


## 2. Tipos de Trigger: AFTER y INSTEAD OF
SQL Server ofrece dos tipos principales de triggers, que definen cuándo se ejecuta la lógica:

### **a) Trigger AFTER (Por defecto)**
**Cuándo se dispara:** Se ejecuta después de que la acción de INSERT, UPDATE o DELETE ya ha ocurrido y los datos están en la tabla.

**Uso principal:** Auditoría, replicación de datos, y aplicación de lógica de negocio secundaria.

**Ejemplo de `AFTER` (Auditoría de Cambios de Estado de Cita)**

Crearemos un trigger para registrar automáticamente en una tabla de auditoría (`Auditoria_Citas`) cada vez que el `Estado` de una cita cambia (una operación de `UPDATE`).

```sql
-- 1. Crear una tabla simple para almacenar el historial de auditoría
CREATE TABLE Auditoria_Citas (
    IdAuditoria INT PRIMARY KEY IDENTITY(1,1),
    IdCita INT NOT NULL,
    EstadoAnterior NVARCHAR(20),
    EstadoNuevo NVARCHAR(20),
    FechaCambio DATETIME DEFAULT GETDATE(),
    UsuarioCambio NVARCHAR(100) DEFAULT SUSER_SNAME()
);
GO

-- 2. Crear el Trigger AFTER UPDATE
CREATE TRIGGER trg_AuditarEstadoCita
ON Cita_Consulta
AFTER UPDATE
AS
BEGIN
    -- Comprobar si el campo 'Estado' realmente cambió (solo para optimizar)
    IF UPDATE(Estado)
    BEGIN
        INSERT INTO Auditoria_Citas (IdCita, EstadoAnterior, EstadoNuevo)
        SELECT 
            i.IdCita,
            d.Estado, -- Valor antiguo (de la tabla DELETED)
            i.Estado  -- Valor nuevo (de la tabla INSERTED)
        FROM inserted i
        JOIN deleted d ON i.IdCita = d.IdCita;
    END
END
GO
```

### **b) Trigger `INSTEAD OF`**
**Cuándo se dispara:** Se ejecuta en lugar de la acción original de INSERT, UPDATE o DELETE. La acción original no ocurre a menos que se codifique explícitamente dentro del trigger.

**Uso principal:** Prevenir una eliminación directa, hacer validaciones complejas, o actualizar vistas no modificables.

**Ejemplo de `INSTEAD OF` (Prevenir Eliminación Directa de Pacientes)**

El trigger evita que se borre un paciente de la tabla Paciente directamente, obligando a que solo se pueda "desactivar" su registro.

```sql
-- Suponemos que la tabla Paciente tiene una columna BIT: 'Activo'
-- ALTER TABLE Paciente ADD Activo BIT DEFAULT 1;

CREATE TRIGGER trg_ProtegerEliminacionPaciente
ON Paciente
INSTEAD OF DELETE
AS
BEGIN
    -- En lugar de borrar, actualiza el estado 'Activo' a 0 (desactivado)
    UPDATE P
    SET Activo = 0,
        Direccion = NULL, -- Opcional: Anular datos sensibles al desactivar
        Telefono = NULL
    FROM Paciente P
    JOIN deleted d ON P.IdPaciente = d.IdPaciente;
    
    SELECT 'AVISO: Paciente marcado como inactivo. No se permitió la eliminación física.' AS Resultado;
END
GO
```


## 3. Casos de Uso (Auditoría, Validación, Automatización)

| **Tipo de Caso de Uso** | **Descripción**                                                                                           | **Aplicación en la Clínica**                                                                                     |
|--------------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| **Auditoría (AFTER)**    | Registrar quién, cuándo y qué cambió en tablas críticas.                                                  | Registrar todos los cambios de Costo en la tabla **Procedimiento**.                                              |
| **Validación (AFTER/INSTEAD OF)** | Asegurar que los datos cumplan una lógica compleja que una **CHECK CONSTRAINT** no puede manejar. | Antes de insertar una cita, verificar que el doctor no tenga más de **8 citas** ese día.                         |
| **Automatización (AFTER)** | Actualizar tablas relacionadas automáticamente.                                                         | Después de insertar un registro en **Detalle_Tratamiento**, actualizar el campo **CostoTotal** en **Cita_Consulta**. |


## 4. Limitaciones y Buenas Prácticas

**Limitaciones Clave**

- **Rendimiento:** Los triggers se ejecutan DENTRO de la transacción de la operación DML. Si el código del trigger es lento, ralentiza la operación original (INSERT/UPDATE/DELETE).
- **Debugging:** Son difíciles de depurar porque se ejecutan automáticamente y ocultan la lógica en el backend.
- **Transacciones:** Un error dentro de un trigger causa que toda la transacción se revierta.

**Buenas Prácticas**

- **Mantenerlos Simples y Cortos:** La lógica compleja debe ir en procedimientos almacenados, no en triggers. Los triggers solo deben llamar al SP.
- **Diseñar para Conjuntos (Sets):** Los triggers deben manejar múltiples filas insertadas/actualizadas/eliminadas a la vez (no asumas que solo se inserta una fila). Los ejemplos anteriores con JOIN deleted d lo cumplen.
- **Usar IF UPDATE():** Siempre usa la función UPDATE(NombreColumna) para verificar si una columna crítica fue modificada. Esto evita que el trigger se ejecute innecesariamente.
- **Nombrar Convencionalmente:** Usar prefijos claros como trg_ para identificarlos fácilmente.


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Concepto de Trigger: Son procedimientos que se ejecutan automáticamente en respuesta a eventos DML (INSERT, UPDATE, DELETE).
- ✅ Tablas Virtuales: Utilizar las tablas temporales Inserted (nuevos valores) y Deleted (viejos valores) para acceder a los datos antes y después del cambio.
- ✅ Tipos de Trigger: Diferenciar entre AFTER (se ejecuta después de la operación) para auditoría y INSTEAD OF (se ejecuta en lugar de la operación) para validaciones fuertes.
- ✅ Casos de Uso: Aplicar triggers para tareas como auditoría de cambios de estado, validación de reglas complejas y automatización de tareas en cascada.
- ✅ Manejo de Rendimiento: Conocer las limitaciones de rendimiento y aplicar buenas prácticas como mantener el código simple y diseñar para conjuntos de datos.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Trigger de Auditoría de Doctor**

- Crea una tabla llamada Auditoria_Doctor con campos: IdCambio, IdDoctor, CampoModificado, ValorAnterior, ValorNuevo, FechaCambio.
- Crea un trigger AFTER UPDATE en la tabla Doctor.
- Si se modifica el Telefono o el Correo del doctor, el trigger debe registrar el cambio en la tabla Auditoria_Doctor.

**Ejercicio 2: Trigger de Validación (INSTEAD OF)**

- Crea un trigger INSTEAD OF DELETE en la tabla Procedimiento.
- El trigger debe impedir la eliminación de cualquier procedimiento si su Costo es superior a $100.
- Si se intenta eliminar un procedimiento con costo mayor a 100, el trigger debe mostrar un mensaje de error y ejecutar un ROLLBACK.

👉 **Los Triggers son una herramienta poderosa, pero úsalos con cautela; demasiados triggers pueden complicar el mantenimiento de tu base de datos.**

📖 **[Ir al Modulo 11: Administracion de Base de Datos](/modulo-11-Admisnistracion-BD/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*