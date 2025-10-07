# 📘 Módulo 07: Índices y Rendimiento

Este módulo se enfoca en el rendimiento y la optimización de consultas. Los Índices son la herramienta principal para acelerar las operaciones de lectura, reducir los tiempos de respuesta del sistema y asegurar que la clínica pueda manejar un alto volumen de citas de manera eficiente.

## 1. Concepto de Índices

Un Índice es una estructura de datos que permite al motor de la base de datos encontrar información más rápido, similar a un índice alfabético en un libro de texto. Sin índices, el motor tendría que escanear (leer) toda la tabla fila por fila (proceso conocido como `Table Scan`), lo cual es muy lento en tablas grandes.

### **📊 Rendimiento vs. Escritura**
Es crucial entender que los índices mejoran la lectura (SELECT), pero ralentizan las operaciones de escritura (INSERT, UPDATE, DELETE). Esto sucede porque cada vez que se modifica una fila, el motor también debe actualizar todas las estructuras de los índices asociados a esa tabla. Por eso, la creación de índices debe ser estratégica.

## 2. Tipos de Índices

Existen dos tipos principales de índices, diferenciados por cómo almacenan la información.

### **a) Clustered Index (Índice Agrupado)**
**Definición:** Determina el orden físico en el que se almacenan los datos de la tabla en el disco. Es la forma en que las filas están literalmente ordenadas.

**Restricción:** Solo puede existir uno por tabla.

**Uso en la Clínica:** La Clave Primaria (PK) de tus tablas (IdPaciente, IdDoctor, IdCita, etc.) es automáticamente un Clustered Index.

### **b) Non-Clustered Index (Índice No Agrupado)**
**Definición:** Es una estructura separada de los datos de la tabla. Almacena las columnas indexadas y punteros que indican dónde encontrar el resto de los datos en el Clustered Index.

**Restricción:** Puedes crear múltiples índices Non-Clustered por tabla (hasta 999 en SQL Server).

**Uso en la Clínica:** Se usan en columnas frecuentemente consultadas, como DNI o CMP.

## 3. Índices Únicos y Compuestos

### **a) Índices Únicos (UNIQUE)**
**Propósito:** Garantizan que no haya valores duplicados en las columnas indexadas.

**Ejemplo de la Clínica:** Aunque la columna CMP (Colegiatura Médica del Doctor) ya tiene una restricción UNIQUE definida, la creación de un índice UNIQUE Non-Clustered garantiza la unicidad y acelera la búsqueda a la vez.

```sql
-- Índice Único en CMP: Acelera búsquedas y asegura que no haya dos doctores con el mismo CMP.
CREATE UNIQUE NONCLUSTERED INDEX IDX_Doctor_CMP
ON Doctor (CMP);

-- Índice Único en DNI: Fundamental para la recepción al verificar la identidad del paciente.
CREATE UNIQUE NONCLUSTERED INDEX IDX_Paciente_DNI
ON Paciente (DNI);
```

### **b) Indices Compuestos** 
**Proposito:** Aceleran consultas que filtran por dos o más columnas simultáneamente. La posición de las columnas dentro del índice es crítica.

**Regla de orden:** La columna más selectiva (la que reduce más el conjunto de resultados) debe ir primero.

**Ejemplo de la Clinica:** Los listados alfabéticos de pacientes se suelen ordenar por apellido y luego por nombre.

```sql
-- Índice Compuesto: Acelera listados ordenados por Apellido y luego por Nombre.
CREATE NONCLUSTERED INDEX IDX_Paciente_Apellido_Nombre
ON Paciente (Apellido, Nombre);
```


## 4. Fragmentacion e Impacto de Consultas 

### **a) ¿Que es la Fragmentacion? 
La fragmentación ocurre cuando las páginas de datos y de índice no se almacenan de forma contigua en el disco. Con el tiempo, debido a constantes INSERT, UPDATE y DELETE, los datos se dispersan.

**Impacto de Rendimiento:** Cuando los datos están fragmentados, el motor de la base de datos debe realizar más accesos al disco (I/O) para leer la información, lo que ralentiza considerablemente las consultas.

### **b) Mantenimiento (Reorganizacion y Reconstruccion)
Para combatir la fragmentación, se requiere mantenimiento periódico de los índices:

| **Acción**   | **Descripción**                                                                 | **Uso**                                                        |
|---------------|----------------------------------------------------------------------------------|----------------------------------------------------------------|
| **REORGANIZE** | Compacta las páginas de datos en su lugar. Es más rápido y menos intrusivo.     | Para niveles de fragmentación bajos a moderados (ej: 5% a 30%). |
| **REBUILD**    | Borra y recrea el índice desde cero, eliminando completamente la fragmentación. | Para niveles de fragmentación altos (ej: 30% o más).            |
