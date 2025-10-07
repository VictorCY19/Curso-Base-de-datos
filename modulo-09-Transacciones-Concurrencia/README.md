# 📘 Módulo 09: Transacciones y Control de Concurrencia
Este módulo se centra en las Transacciones, que son la columna vertebral de la integridad de datos. Una transacción asegura que una serie de operaciones se ejecuten como una única unidad lógica. Además, cubriremos cómo manejar múltiples usuarios modificando los mismos datos al mismo tiempo (concurrencia).

## 1. Concepto de Transacción
Una Transacción es un conjunto de tareas que se ejecutan como una sola unidad atómica. Si una parte de la tarea falla, toda la transacción falla y la base de datos revierte al estado original (como si nada hubiera pasado).

**Propiedades ACID**
Toda transacción en una base de datos relacional debe cumplir las propiedades ACID:

| **Propiedad**                | **Significado**                                                  | **Aplicación en la Clínica**                                                                                      |
|------------------------------|------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| **Atomicidad (Atomicity)**   | Todo o nada.                                                     | Registrar una cita y un detalle de tratamiento debe completarse en conjunto. Si el detalle falla, la cita se cancela. |
| **Consistencia (Consistency)**| Las transacciones deben llevar la base de datos de un estado válido a otro. | Una cita solo se puede asignar si el **IdPaciente** y el **IdDoctor** existen.                                    |
| **Aislamiento (Isolation)**  | Las transacciones simultáneas no deben interferir entre sí.      | El proceso de pago de la **Cita A** no debe ver ni afectar el proceso de registro de la **Cita B**.                |
| **Durabilidad (Durability)** | Una vez que la transacción se completa (**COMMIT**), los cambios son permanentes. | Los datos de una cita atendida deben persistir incluso si el servidor falla.                                       |


## 2. Comandos: BEGIN TRAN, COMMIT, ROLLBACK
Estos comandos definen el ciclo de vida de una transacción explícita en SQL Server:

| **Comando**                              | **Función**                                                                                   |
|------------------------------------------|-----------------------------------------------------------------------------------------------|
| **BEGIN TRANSACTION** o **BEGIN TRAN**   | Marca el punto de inicio de la transacción.                                                   |
| **COMMIT TRANSACTION** o **COMMIT**      | Confirma todos los cambios realizados durante la transacción y los hace permanentes.          |
| **ROLLBACK TRANSACTION** o **ROLLBACK**  | Deshace todos los cambios desde el **BEGIN TRAN** y revierte la base de datos al estado anterior. |


**Ejemplo Práctico de Transacción (Registro de Cita y Detalle)**
```sql
BEGIN TRAN; -- Inicia la Transacción

-- Tarea 1: Insertar la nueva cita
INSERT INTO Cita_Consulta (IdPaciente, IdDoctor, FechaHora, Estado)
VALUES (1, 2, '2026-01-10 09:00:00', 'Pendiente');

-- Obtener el ID de la cita recién creada (asumiendo que es IDENTITY)
DECLARE @NuevaCitaID INT = SCOPE_IDENTITY(); 

-- Tarea 2: Insertar el procedimiento asociado (requiere el Id de la cita)
INSERT INTO Detalle_Tratamiento (IdCita, IdProcedimiento)
VALUES (@NuevaCitaID, 1); -- Asocia el análisis de sangre

-- Verificar si ambas operaciones tuvieron éxito
IF @@ERROR = 0
BEGIN
    COMMIT TRAN; -- Si todo salió bien, confirma los dos INSERT
    SELECT 'Transacción exitosa: Cita y detalle registrados.';
END
ELSE
BEGIN
    ROLLBACK TRAN; -- Si algo falló, deshace ambos INSERT
    SELECT 'Transacción fallida: Se revirtieron los cambios.';
END
```

## 3. Manejo de Errores con TRY_CATCH
En producción, la forma recomendada de manejar errores y asegurar que siempre se ejecute un ROLLBACK ante cualquier falla es usando bloques TRY...CATCH.

```sql
BEGIN TRY
    BEGIN TRAN;

    -- Tarea 1: Intentar actualizar el estado de una cita existente
    UPDATE Cita_Consulta 
    SET Estado = 'Atendida'
    WHERE IdCita = 1;

    -- Tarea 2: (Simulación de un error intencional - intentar insertar un valor que no cabe)
    INSERT INTO Paciente (DNI, Nombre, Apellido, Fecha_Nacimiento, Sexo)
    VALUES ('1234567890', 'Error', 'Test', '1990-01-01', 'M'); -- DNI excede CHAR(8)

    -- Si llega hasta aquí, todo fue bien
    COMMIT TRAN; 

END TRY
BEGIN CATCH
    -- Si hubo un error en el bloque TRY
    IF @@TRANCOUNT > 0 -- Verifica si hay transacciones pendientes de confirmar/revertir
    BEGIN
        ROLLBACK TRAN; -- Deshace TODOS los cambios (incluida la actualización de la Cita 1)
    END

    -- Reportar el error
    SELECT 
        ERROR_NUMBER() AS NumeroError,
        ERROR_MESSAGE() AS MensajeError,
        'La transacción ha sido revertida.' AS Resultado;

END CATCH
```


## 4. Niveles de Aislamiento de Transacciones
Los Niveles de Aislamiento controlan cómo una transacción interactúa con los datos que están siendo modificados por otras transacciones concurrentes. Un nivel más alto de aislamiento mejora la consistencia, pero reduce la concurrencia (velocidad).

| **Nivel de Aislamiento** | **Descripción**                                                                                             | **Problema que Resuelve**          |
|---------------------------|-------------------------------------------------------------------------------------------------------------|------------------------------------|
| **READ UNCOMMITTED**      | Permite leer datos que aún no han sido confirmados por otra transacción (lecturas "sucias").               | Ninguno, introduce el problema de lecturas sucias. |
| **READ COMMITTED**        | Solo lee datos que ya fueron confirmados. (Nivel por defecto en SQL Server).                              | Lecturas Sucias.                   |
| **REPEATABLE READ**       | Asegura que, si se lee un dato dos veces en la misma transacción, el valor será el mismo.                 | Lecturas No Repetibles.            |
| **SERIALIZABLE**          | El más alto. Bloquea todo el rango de datos. Las transacciones se ejecutan como si fueran secuenciales.   | Lecturas Fantasma.                 |

**Ejemplo de Configuración**
```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

BEGIN TRAN
    -- Código de la transacción
COMMIT TRAN
```

## 5. Deadlocks (Interbloqueos) y Cómo Evitarlos

### **a) Concepto de Deadlock**
Un Deadlock ocurre cuando dos o más transacciones se bloquean mutuamente porque cada una necesita un recurso que la otra tiene bloqueado. La transacción se detiene en un punto muerto, y SQL Server debe elegir a una de ellas como la "víctima" para forzar su ROLLBACK y permitir que la otra continúe.

**Escenario Típico:**

**Transacción A:** Bloquea la Tabla X y luego intenta acceder a la Tabla Y.

**Transacción B:** Bloquea la Tabla Y y luego intenta acceder a la Tabla X.

### **b) Estrategias para Evitar Deadlocks**

- Acceder a los objetos en el mismo orden: Si todas las transacciones acceden a las tablas Paciente y luego a Cita_Consulta, nunca habrá conflicto.
- Mantener las transacciones cortas: Reducir el tiempo de bloqueo.
- Usar niveles de aislamiento apropiados: Evitar niveles muy altos (como SERIALIZABLE) si no es estrictamente necesario, ya que aumentan la probabilidad de bloqueo.


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Transacciones: Utilizar BEGIN TRAN, COMMIT y ROLLBACK para garantizar que las operaciones complejas (como registrar una cita y sus procedimientos) sean atómicas (todo o nada).
- ✅ Manejo de Errores: Implementar bloques TRY...CATCH para asegurar que se ejecute un ROLLBACK automático si ocurre cualquier error en el proceso.
- ✅ Niveles de Aislamiento: Entender cómo configurar la concurrencia (READ COMMITTED es el valor por defecto) para prevenir problemas como Lecturas Sucias o Fantasma.
- ✅ Deadlocks: Conocer la causa del interbloqueo y aplicar estrategias (como ordenar el acceso a los objetos) para minimizarlos.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Transacción con Rollback**
- Crea un bloque BEGIN TRAN que intente:
a. Actualizar el apellido del IdPaciente = 1 a 'Rojas'.
b. Insertar una cita con un IdDoctor que sabes que no existe (ej: IdDoctor = 999).
- Agrega una lógica simple con IF @@ERROR <> 0 para ejecutar un ROLLBACK si el INSERT falla.
- Verifica que el apellido del IdPaciente = 1 no haya cambiado después de ejecutar el bloque.

**Ejercicio 2: Implementación de TRY_CATCH**
- Implementa la lógica del Ejercicio 1, pero dentro de un bloque TRY...CATCH.
- En el bloque CATCH, asegúrate de usar IF @@TRANCOUNT > 0 ROLLBACK TRAN; y mostrar el mensaje de error.

**Ejercicio 3: Bloqueo (Concepto de Deadlock)**
- Abre dos ventanas de consulta separadas en SSMS (simulando dos usuarios concurrentes).
- En la Ventana 1, inicia una transacción y haz un UPDATE en la tabla Paciente (mantén la transacción abierta, sin COMMIT).
- En la Ventana 2, intenta hacer un SELECT a la tabla Paciente.
- (Reflexión: Observa cómo la Ventana 2 queda bloqueada hasta que la Ventana 1 ejecuta COMMIT o ROLLBACK).

👉 **Las transacciones son tu póliza de seguro de datos. ¡Úsalas siempre en operaciones de escritura críticas!**

📖 **[Ir al Modulo 10: Triggers](/modulo-10-Triggers/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*