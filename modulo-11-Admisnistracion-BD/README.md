# 📘 Módulo 11: Administración de Bases de Datos
Este módulo cubre la administración y el mantenimiento esenciales para garantizar la disponibilidad, confiabilidad y rendimiento a largo plazo de la base de datos de la Clínica "Salud Vital".

## 1. Backups (Copias de Seguridad)
La estrategia de copia de seguridad es la defensa más crítica contra la pérdida de datos. SQL Server ofrece tres tipos fundamentales de backups que, combinados, permiten la recuperación de datos con mínima pérdida.

### **a) Backup Completo (Full Backup)**

**Contenido:** Una copia de seguridad de toda la base de datos en el momento en que se realiza la copia.

**Uso:** Es la base de cualquier estrategia de recuperación.

**Frecuencia Típica:** Semanal o diaria.

### **b) Backup Diferencial (Differential Backup)**

**Contenido:** Solo respalda los datos que han cambiado desde el último Backup Completo.

**Ventajas:** Es mucho más rápido de crear y consume menos espacio que un backup completo.

**Uso:** Requiere un Backup Completo previo para ser restaurado.

**Frecuencia Típica:** Diaria o varias veces a la semana.

### **c) Backup de Registro de Transacciones (Transactional Log Backup)**

**Contenido:** Copia las transacciones registradas desde el último Backup de Log (o diferencial, si aplica).

**Requisito:** La base de datos debe estar en modo de recuperación FULL.

**Ventajas:** Permite la recuperación hasta el punto exacto de un fallo (Point-in-Time Recovery), minimizando la pérdida de datos a segundos o minutos.

**Frecuencia Típica:** Cada 15 minutos, 5 minutos o incluso menos.


## 2. Restauración y Estrategias de Recuperación
La estrategia de restauración debe ser probada regularmente para garantizar que los backups sean utilizables.

**Proceso de Restauración Típico**

Para recuperar la base de datos a un punto reciente, se requiere el siguiente orden:

**Restaurar el último Backup Completo:** Establece el punto base de los datos.

**Restaurar el último Backup Diferencial:** Aplica todos los cambios posteriores al completo, si existe.

**Restaurar la secuencia de Backups de Log:** Aplica todos los logs creados después del diferencial (o el completo), permitiendo llegar al momento deseado del fallo.

**Modos de Recuperación (Recovery Models)**
El modo de recuperación define qué tipo de backups de log se pueden usar:

| **Modo de Recuperación** | **Backup de Log**              | **Uso Ideal**                                                                                                 |
|---------------------------|--------------------------------|---------------------------------------------------------------------------------------------------------------|
| **Simple**                | No permite Backups de Log.     | Bases de datos de **desarrollo o pruebas**, donde se tolera perder todos los datos desde el último backup completo. |
| **Full**                  | Permite Backups de Log.        | Bases de datos de **producción** (como *"Salud Vital"*), donde la pérdida de datos debe ser mínima.               |

```sql
-- Cambiar el modo de recuperación de la base de datos a FULL (obligatorio para logs)
ALTER DATABASE Salud_Vital SET RECOVERY FULL;
```


## 3. Tareas de Mantenimiento
Estas tareas son esenciales para que las consultas se ejecuten de manera eficiente y rápida, un requisito vital para evitar la frustración de pacientes y doctores.

### **a) Reconstrucción de Índices (Index Rebuild)**
Con el tiempo, las inserciones, actualizaciones y eliminaciones de datos causan fragmentación en los índices (visto en el Módulo 07).

**Acción:** `REBUILD` elimina y recrea la estructura del índice, eliminando la fragmentación y actualizando las estadísticas.

**Cuándo:** Cuando la fragmentación supera el 30% o en índices clave (ej: el DNI en Paciente).

**Frecuencia Típica:** Semanal o mensual.

```sql
-- Ejemplo: Reconstruir el índice primario en la tabla Paciente
ALTER INDEX PK_Paciente ON Paciente REBUILD;
```

### **b) Reorganización de Índices (Index Reorganize)**

**Acción:** Mueve páginas de datos dentro de los mismos archivos, siendo menos intensivo que REBUILD.

**Cuándo:** Cuando la fragmentación está entre el 5% y el 30%.

**Frecuencia Típica:** Semanal.

### **c) Actualización de Estadísticas (Update Statistics)**
SQL Server usa estadísticas sobre la distribución de datos en las columnas para decidir el mejor Plan de Ejecución de una consulta. Si las estadísticas son obsoletas, el motor puede elegir un mal plan, ralentizando la consulta.

**Acción:** Forzar al motor a analizar los datos actuales y actualizar la información estadística.

**Frecuencia Típica:** Diaria o cada vez que hay una gran cantidad de cambios de datos.

```sql
-- Ejemplo: Forzar la actualización de estadísticas en la tabla Cita_Consulta
UPDATE STATISTICS Cita_Consulta;
```

### **d) Reducción de Archivos (DBCC SHRINKFILE)**

**Uso:** Liberar espacio no utilizado al final de los archivos de la base de datos (.mdf o .ldf).

⚠️ **Advertencia:** Es una práctica controvertida y generalmente no se recomienda en bases de datos de producción que siguen creciendo, ya que puede causar más fragmentación. Se usa solo en casos extremos (ej: después de eliminar una cantidad masiva de datos).


## 4. Monitoreo Básico en SSMS (Activity Monitor)
El Monitor de Actividad (Activity Monitor) es la herramienta más sencilla en SQL Server Management Studio (SSMS) para ver qué está sucediendo en tiempo real.

**Propósito:** Identificar rápidamente bloqueos, consultas lentas y cuellos de botella de recursos.

**Métricas Clave:**

**Procesos (Processes):** Quién está conectado y qué están ejecutando.

**Bloqueos (Resource Waits):** Muestra si hay transacciones esperando por recursos (puede indicar un Deadlock o una consulta muy larga).

**I/O de Archivos de Datos (Data File I/O):** Mide la velocidad de lectura/escritura del disco. Los valores altos pueden indicar un cuello de botella en el hardware.

**Recomendación:** Monitorea los Bloqueos y los I/O de Archivos de Datos del servidor de "Salud Vital" para diagnosticar picos de lentitud.


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Estrategia de Backups: Implementar los tres tipos de backups (Completo, Diferencial y de Log) para asegurar una recuperación rápida con mínima pérdida de datos.
- ✅ Recuperación: Entender la secuencia de restauración y la necesidad del modo de recuperación FULL para la base de datos de producción.
- ✅ Mantenimiento de Índices: Utilizar REBUILD y REORGANIZE para combatir la fragmentación y mantener la velocidad de las consultas.
- ✅ Estadísticas: Conocer la importancia de UPDATE STATISTICS para que el motor SQL elija los mejores planes de ejecución.
- ✅ Monitoreo: Usar el Monitor de Actividad en SSMS para identificar en tiempo real bloqueos y cuellos de botella de rendimiento.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Simulación de Estrategia de Backup**

- En la base de datos Salud_Vital, asegura que el modo de recuperación sea FULL.

- Escribe el comando para realizar un Backup Completo y guárdalo en una ubicación de tu elección.

- Escribe el comando para realizar un Backup Diferencial después de hacer un INSERT en la tabla Paciente.

- Escribe el comando para realizar un Backup de Registro de Transacciones.

- (Opcional: Si tienes permisos, practica la restauración de esta secuencia de archivos).

**Ejercicio 2: Tareas de Mantenimiento**

- Escribe el comando para Reconstruir el índice Clustered de la tabla Cita_Consulta.

- Escribe el comando para Actualizar las Estadísticas en la tabla Detalle_Tratamiento.

👉 **La Administración de Bases de Datos es la fase donde conviertes la teoría de SQL en un sistema confiable y rápido 24/7.**

📖 **[Ir al Modulo 12: Integracion y Exportacion de Datos](/modulo-12-Integracion-Exportacion/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*