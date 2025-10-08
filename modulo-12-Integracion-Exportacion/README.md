## 📘 Módulo 12: Integración y Exportación de Datos
Este módulo cubre las herramientas y comandos necesarios para mover datos masivamente hacia (importación) y desde (exportación) nuestra base de datos SQL Server, lo cual es vital para generar reportes para contabilidad o importar listados de pacientes desde archivos externos.

## 1. Importación desde archivos Excel y CSV
La forma más común y visual de importar datos a SQL Server es utilizando el Asistente de Importación y Exportación de SSMS (SQL Server Management Studio).

### **a) Uso del Asistente (SSMS)**

- En SSMS, haz clic derecho sobre la base de datos (Salud_Vital).

- Ve a Tareas (Tasks) -> Importar Datos (Import Data...).

- **Selecciona la fuente de datos:** Para Excel/CSV, selecciona el proveedor Microsoft Access Database Engine (o el más reciente para Office).

- **Selecciona el destino:** SQL Server Native Client.

- **Mapeo:** El asistente te permite mapear las columnas del archivo de origen con las columnas de tu tabla de destino (Ej: Columna A del Excel -> Columna Nombre de la tabla Paciente).

### **b) La importancia del formato**

- **CSV (Comma Separated Values):** Es el formato preferido para la importación masiva por su simplicidad y velocidad.

**Encabezados:** Asegúrate de que los nombres de las columnas en tu archivo CSV coincidan con los de tu tabla SQL para un mapeo automático más fácil.


## 2. Exportación de Datos
La exportación permite generar archivos planos (CSV, TXT) o transferir datos directamente a otra base de datos.

### **a) Exportación vía SSMS**
Se utiliza el mismo Asistente de Importación y Exportación, pero en este caso, se selecciona:

**Origen:** SQL Server Native Client (nuestra base de datos Salud_Vital).

**Destino:** Flat File Destination (para CSV o TXT) o un proveedor de base de datos diferente.

**Query:** Puedes exportar el resultado de una consulta específica (Ej: un reporte anual de procedimientos) en lugar de la tabla completa, lo cual es muy potente.

### **b) Exportación a Archivo (Uso de BCP)**
Para exportaciones masivas y automatizadas a archivos de texto, se utiliza la utilidad de línea de comandos BCP (Bulk Copy Program).

```sql
-- Exportar la tabla Doctor a un archivo CSV
bcp Salud_Vital.dbo.Doctor out "C:\temp\Doctores_Export.csv" -c -T -S [NOMBRE_TU_SERVIDOR]
```

## 3. Uso de `BULK INSERT` (Importación Masiva vía SQL)
El comando BULK INSERT es la forma más rápida y eficiente de importar grandes volúmenes de datos directamente desde un archivo plano (CSV o TXT) usando solo comandos SQL. Es ideal para la carga inicial de datos o para procesos nocturnos.

**Sintaxis y Ejemplo**
Supongamos que tienes un archivo CSV llamado nuevos_pacientes.csv.

```sql
-- Suponemos que el archivo contiene las columnas DNI, Nombre, Apellido, Fecha_Nacimiento...
BULK INSERT Paciente
FROM 'C:\DataImport\nuevos_pacientes.csv'
WITH (
    FIELDTERMINATOR = ',', -- El delimitador de campos es una coma
    ROWTERMINATOR = '\n',  -- El delimitador de filas es un salto de línea
    FIRSTROW = 2           -- Opcional: Ignorar la primera fila (si es el encabezado)
);
```

**Requisitos para `BULK INSERT`:**

- El archivo debe ser accesible por el servidor SQL (generalmente, debe estar en una ruta local del servidor).

- El usuario que ejecuta el comando debe tener el permiso **ADMINISTER BULK OPERATIONS**.


## 4. Linked Servers (Conexiones Externas)
Un Servidor Vinculado (Linked Server) permite a SQL Server conectarse a otra instancia de SQL Server (o a otra base de datos compatible, como Oracle o MySQL) y ejecutar consultas como si fuera una tabla local.

**Caso de Uso en la Clínica**
La clínica podría usar un Linked Server para conectarse al servidor de Contabilidad y obtener los estados de pago de los pacientes en tiempo real.

**Ejecutando Consultas con Linked Servers**
Para consultar una tabla en un servidor vinculado, se utiliza una sintaxis de cuatro partes (Servidor.BaseDeDatos.Esquema.Objeto):


```sql
SELECT T1.Nombre, T1.Apellido, T2.Estado_Pago
FROM Paciente T1
-- Consulta una tabla llamada 'Pagos' en el servidor vinculado 'SERVIDOR_CONTABILIDAD'
JOIN SERVIDOR_CONTABILIDAD.DB_Contabilidad.dbo.Pagos T2 
ON T1.DNI = T2.DNI_Paciente;
```


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ **Importación/Exportación:** Utilizar el Asistente de SSMS para la transferencia de datos de manera visual, especialmente desde/hacia archivos Excel y CSV.
- ✅ **BULK INSERT:** Emplear el comando SQL más eficiente para la importación masiva de datos desde archivos planos, ideal para grandes volúmenes.
✅ **Linked Servers:** Establecer conexiones persistentes con otras instancias de bases de datos para consultar tablas externas usando la sintaxis de cuatro partes.
✅ **Flujo de Datos:** Comprender las estrategias para mover datos a otros sistemas de reportes o contabilidad.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Simulación de Importación (BULK INSERT)**

- Crea un archivo de texto simple (TXT o CSV) con datos simulados para la tabla Procedimiento (ej: 3 filas con Nombre, Descripcion, Costo).

- Escribe el comando `BULK INSERT` completo para intentar importar esos datos en tu tabla Procedimiento, asegurando que los delimitadores de campo y fila sean correctos.

**Ejercicio 2: Exportación a Reporte (Query a CSV)**

- Escribe una consulta que obtenga el Nombre del doctor, la Especialidad y el TotalCitasAtendidas.

- Describe los pasos que usarías en el Asistente de Exportación de SSMS para guardar el resultado de esta consulta como un archivo CSV para el departamento de Recursos Humanos.

👉 **La capacidad de mover datos de manera eficiente es el puente que conecta tu base de datos con el resto de los procesos de negocio.**

📖 **[Ir al Modulo 13: Reporting y Analisis de Datos](/modulo-13-Reporting-Analisis/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*