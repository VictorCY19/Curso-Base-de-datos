# 📘 Problema: BD de pacientes para una clinica

En este ejemplo nos pondremos en el papel de un desarrollador junior para resolver el siguiente problema, para ello haremos la simulación de una entrevista con un cliente, el cual nos va a decir sus requisitos para nosotros poder diseñar la BD que el espera. 

👉 **Nota importante**: Primero de un analisis a todo el texto y crea la BD como mejor lo comprendas (no vayas directo a la solución) recuerda que no hay problema si te equivocas, estas aprendiendo y equivocarse es parte de aprender.


# 🏥 Caso de Estudio – Sistema Clínica "Salud Vital"

## 📋 Contexto del Proyecto

Un analista de sistemas ha sido convocado por el dueño de la clínica **Salud Vital**, el señor Ramírez, para diseñar una base de datos que permita gestionar adecuadamente la información de pacientes, doctores, citas, tratamientos y facturación. La clínica quiere modernizar su forma de trabajo y digitalizar sus procesos.

---

## 🎯 Entrevista con el Dueño (Señor Ramírez)

**Analista**: *"Buenos días, señor Ramírez. Entiendo que desea implementar un sistema para la clínica. ¿Podría contarme cuáles son sus principales necesidades?"*

**Dueño**: *"Claro. Nosotros llevamos años trabajando con registros en papel, pero eso ya no nos da resultados. Queremos una base de datos que nos permita tener información organizada de los pacientes, sus citas, sus tratamientos y todo lo relacionado con los pagos."*

---

## 🏗️ Requerimientos del Sistema

### 1. **Gestión de Pacientes**
- Registrar todos los pacientes que llegan a la clínica
- Datos básicos: nombres completos, DNI, fecha de nacimiento, sexo, dirección, teléfono, correo electrónico
- Información sobre seguros: saber qué pacientes están asegurados y con qué compañía
- Un teléfono principal y un correo electrónico por paciente

### 2. **Gestión de Doctores**
- Registrar doctores con sus especialidades
- Datos requeridos: nombres completos, DNI, especialidad médica, teléfono, correo
- Relacionar cada cita con el doctor que la atendió
- *Nota: Por ahora, un doctor tiene una especialidad, pero podría capacitarse en otra*

### 3. **Gestión de Citas Médicas**
- Reservar citas con doctor en fecha y hora específica
- Registrar: paciente, doctor, fecha, hora, estado de la cita
- Estados posibles: **pendiente**, **atendida**, **cancelada**, **no asistió**
- Control de pacientes que no asisten a sus citas

### 4. **Consultas Médicas**
- Cada cita atendida se convierte en una consulta
- Registrar diagnóstico y tratamientos prescritos
- Mantener historial completo de consultas por paciente
- Relacionar consultas con tratamientos y procedimientos

### 5. **Tratamientos y Procedimientos**
- Catálogo de procedimientos médicos disponibles
- Ejemplos: análisis de sangre, radiografías, cirugías menores
- Datos: nombre, descripción, costo del procedimiento
- Asociar procedimientos a consultas específicas

### 6. **Facturación y Pagos**
- Generar facturas por consultas y procedimientos
- Datos de factura: número, fecha, paciente, monto total
- Detalle de conceptos en cada factura
- Control de estado de pago: **pagado** o **pendiente**
- Registrar fecha de pago cuando se realiza

### 7. **Consideraciones Futuras**
- Sistema flexible para crecimiento futuro
- Posibles módulos adicionales: inventario de medicinas, historial de seguros, pagos con tarjeta
- *Prioridad actual: pacientes, doctores, citas, consultas, procedimientos y facturación*

---

## 📊 Ejercicio Práctico - Diseño de Base de Datos

### **🎯 Objetivo del Ejercicio**

Con la información proporcionada, los estudiantes deben:

1. **Identificar las entidades principales** (tablas necesarias)
2. **Detectar las relaciones** entre las entidades
3. **Plantear el diseño lógico** de la base de datos
4. **Definir claves primarias y foráneas**
5. **Identificar los tipos de relación** (1:1, 1:N, N:M)

👉 **Puede parecer muy complicado ahora mismo, toma tu tiempo y analisa todo el problema, luego compara con la respuesta y vuelve a analisar la solucion**


📌 **Resultado:**

[Diagrama-Entidad-Relacion](/assets/images/modulo-05/Diagrama_Entidad_Relacion.png)

Aqui puedes ver la solución del esquema previo que debiste haber creado, puedes ver como las palabras ayudan como conectores para saber como conectar nuestras tablas y como los puntos de conexion tienen diferente diseño para identificar si van de: uno a muchos, muchos a muchos o de uno a uno. Recuerda que esto es muy importante para poder colocar nuestras llaves FK donde corresponde, adicional de crear tablas intermedias de ser necesario. 

📌 **Explicación del flujo:**

- Pacientes solicitan Citas.
- Doctores atienden esas Citas.
- Cada Cita genera una Consulta.
- Una Consulta puede tener uno o varios Pagos asociados.
- Una Consulta puede incluir uno o varios Procedimientos médicos.


## 📋 Solución Propuesta - Scripts SQL

```sql
-- 1. Tabla Pacientes
CREATE TABLE Pacientes (
    IdPaciente INT PRIMARY KEY IDENTITY(1,1),
    DNI CHAR(8) UNIQUE NOT NULL,
    Nombre NVARCHAR(100) NOT NULL,
    Apellido NVARCHAR(100) NOT NULL,
    FechaNacimiento DATE NOT NULL,
    Sexo CHAR(1) CHECK (Sexo IN ('M','F')),
    Telefono NVARCHAR(15),
    Email NVARCHAR(100),
    Direccion NVARCHAR(200),
    FechaRegistro DATETIME DEFAULT GETDATE()
);
-- 🔹 Lógica: El paciente se identifica por DNI. Se almacena su información de contacto y fecha de registro. Esto es fundamental porque todo gira alrededor del paciente.

-- 2. Tabla Médicos
CREATE TABLE Medicos (
    IdMedico INT PRIMARY KEY IDENTITY(1,1),
    CMP NVARCHAR(20) UNIQUE NOT NULL,
    Nombre NVARCHAR(100) NOT NULL,
    Apellido NVARCHAR(100) NOT NULL,
    Especialidad NVARCHAR(100) NOT NULL,
    Telefono NVARCHAR(15),
    Email NVARCHAR(100)
);
-- 🔹 Lógica: Cada médico tiene un CMP (registro profesional). La especialidad es importante para las citas (ej: pediatría, cardiología).

-- 3. Tabla Citas Médicas
CREATE TABLE Citas (
    IdCita INT PRIMARY KEY IDENTITY(1,1),
    IdPaciente INT NOT NULL,
    IdMedico INT NOT NULL,
    FechaHora DATETIME NOT NULL,
    Estado NVARCHAR(20) DEFAULT 'Pendiente' CHECK (Estado IN ('Pendiente','Atendida','Cancelada')),
    Motivo NVARCHAR(200),
    FOREIGN KEY (IdPaciente) REFERENCES Pacientes(IdPaciente),
    FOREIGN KEY (IdMedico) REFERENCES Medicos(IdMedico)
);
-- 🔹 Lógica: Relaciona un paciente con un médico en una fecha/hora. Estado indica si la cita fue atendida o cancelada. "Motivo" es opcional (ejemplo: "dolor de cabeza").

-- 4. Tabla Consultas Médicas
CREATE TABLE Consultas (
    IdConsulta INT PRIMARY KEY IDENTITY(1,1),
    IdCita INT NOT NULL,
    Diagnostico NVARCHAR(500),
    Tratamiento NVARCHAR(500),
    Observaciones NVARCHAR(500),
    FechaConsulta DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (IdCita) REFERENCES Citas(IdCita)
);
-- 🔹 Lógica: Cada cita atendida genera una consulta. Incluye diagnóstico, tratamiento y observaciones médicas. Es la información más importante para el historial clínico.

-- 5. Tabla Procedimientos
CREATE TABLE Procedimientos (
    IdProcedimiento INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Descripcion NVARCHAR(200),
    Costo DECIMAL(10,2) NOT NULL
);
-- 🔹 Lógica: Procedimientos como "Ecografía", "Análisis de sangre", "Electrocardiograma". Se usarán en conjunto con consultas y pagos.

-- 6. Tabla Pagos
CREATE TABLE Pagos (
    IdPago INT PRIMARY KEY IDENTITY(1,1),
    IdConsulta INT NULL,
    IdProcedimiento INT NULL,
    Monto DECIMAL(10,2) NOT NULL,
    MetodoPago NVARCHAR(20) CHECK (MetodoPago IN ('Efectivo','Tarjeta','Transferencia')),
    FechaPago DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (IdConsulta) REFERENCES Consultas(IdConsulta),
    FOREIGN KEY (IdProcedimiento) REFERENCES Procedimientos(IdProcedimiento)
);
-- 🔹 Lógica: Un pago puede estar asociado a una consulta o a un procedimiento. Se registra monto, método y fecha. Refleja la parte administrativa de la clínica.
```

**Con esto hemos terminado de resolver el problema, pero esto no es todo realmente podemos seguir mejorando esta BD con mas tablas para resolver mas problemas, asi de este modo crea diferentes BD que resuelvan varios problemas del dia a dia y practica** 

👉 Ahora continuaremos practicando diferentes consultas para esta BD