# 📘 Problema: BD de pacientes para una clinica

En este ejemplo nos pondremos en el papel de un desarrollador junior para resolver el siguiente problema, para ello haremos la simulación de una entrevista con un cliente, el cual nos va a decir sus requisitos para nosotros poder diseñar la BD que el espera. 

👉 **Nota importante**: Primero realiza un analisis a todo el texto y crea la BD como mejor lo comprendas (no vayas directo a la solución) recuerda que no hay problema si te equivocas, estas aprendiendo y equivocarse es parte de aprender.


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
- Datos básicos: nombres completos, DNI, fecha de nacimiento, sexo, dirección, teléfono, correo electrónico y fecha de registro.

### 2. **Gestión de Doctores**
- Registrar doctores con sus especialidades
- Datos requeridos: nombres completos, CMP, especialidad médica, teléfono, correo
- Relacionar cada cita con el doctor que la atendió
- *Nota: Por ahora, un doctor tiene una especialidad, pero podría capacitarse en otra*

### 3. **Gestión de Citas / Consultas**
- Se debe poder registrar al paciente, doctor, fecha, hora y estado
- Estados posibles: **pendiente**, **atendida**, **cancelada**, **no asistió**

### 4. **Consultas Médicas**
- Cada cita atendida se convierte en una consulta
- Registrar diagnóstico y observaciones prescritos
- Mantener historial completo de consultas por paciente
- Relacionar consultas con procedimientos

### 5. **Procedimientos**
- Catálogo de procedimientos médicos disponibles
- Ejemplos: análisis de sangre, radiografías, cirugías menores
- Datos: nombre, descripción, costo del procedimiento
- Asociar procedimientos a citas específicas

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

![Diagrama-Entidad-Relacion](/assets/images/modulo-05/Diagrama_Entidad_Relacion.png)


📌 **Explicación del flujo:**

- Pacientes solicitan Citas.
- Doctores atienden esas Citas.
- Cada Cita incluye procedimiento.
- El procedimiento se incluye en la cita.

⚠️ **Nota importante**: Manejamos una tabla intermedia para citas y procedimientos ya que va de una relacion de M:N, y por consiguiente su llave principal se conforma de los id de ambas tablas. 


## 📋 Solución Propuesta - Scripts SQL

```sql
-- 1. Tabla Paciente
CREATE TABLE Paciente(
IdPaciente INT PRIMARY KEY IDENTITY (1,1),
DNI CHAR(8) UNIQUE NOT NULL, --aplicamos char ya que el DNI (Peru) es de 8 caracteres
Nombre NVARCHAR(100) NOT NULL,
Apellido NVARCHAR(100) NOT NULL, 
Fecha_Nacimiento DATE NOT NULL,
Sexo CHAR(1) CHECK (sexo IN ('M','F')),
Direccion NVARCHAR(200),
Telefono NVARCHAR(15),
Correo NVARCHAR(100),
Fecha_Registro DATETIME DEFAULT GETDATE()
);
-- 🔹 Lógica: El paciente se identifica por DNI. Se almacena su información de contacto y fecha de registro. Esto es fundamental porque todo gira alrededor del paciente.

-- 2. Tabla Doctor
CREATE TABLE Doctor(
IdDoctor INT PRIMARY KEY IDENTITY (1,1), 
CMP NVARCHAR(20) UNIQUE NOT NULL, 
Nombre NVARCHAR(100) NOT NULL,
Apellido NVARCHAR(100) NOT NULL, 
Especialidad NVARCHAR(100) NOT NULL,
Telefono NVARCHAR(15) NOT NULL, 
Correo NVARCHAR(100) NOT NULL
);
-- 🔹 Lógica: Cada médico tiene un CMP (registro profesional). La especialidad es importante para las citas (ej: pediatría, cardiología).

-- 3. Tabla Cita_Consulta
CREATE TABLE Cita_Consulta (
IdCita INT PRIMARY KEY IDENTITY (1,1),
IdPaciente INT NOT NULL, 
IdDoctor INt NOT NULL, 
FechaHora DATETIME NOT NULL,
Estado NVARCHAR(20) DEFAULT 'Pendiente' CHECK (estado IN ('Pendiente', 'Atendida', 'Cancelada', 'No Asistio')),
FOREIGN KEY (IdPaciente) REFERENCES Paciente(IdPaciente),
FOREIGN KEY (IdDoctor) REFERENCES Doctor(IdDoctor)
);
-- 🔹 Lógica: Relaciona un paciente con un médico en una fecha/hora. Estado indica si la cita fue atendida,cancelada, pendiento o no asistio.

-- 4. Tabla Procedimiento
CREATE TABLE Procedimiento (
IdProcedimiento INT PRIMARY KEY IDENTITY (1,1),
Nombre NVARCHAr(100) NOT NULL, 
Descripcion NVARCHAR(200),
Costo DECIMAL(10,2) NOT NULL
);
-- 🔹 Lógica: Cada procedimiento tiene un nombre, descripcion y un costo.

-- 5. Tabla Detalle_Tratamiento
CREATE TABLE Detalle_Tratamiento (
IdCita int not null,
IdProcedimiento int not null,
--Definicion de clave primaria compuesta
--Ambas columnas combinadas identifican un registro unico
Primary KEY (IdCita, IdProcedimiento),
--Restricciones de llave foranea (FK) 
FOREIGN KEY (IdCita) REFERENCES Cita_Consulta(IdCita), 
FOREIGN KEY (IdProcedimiento) REFERENCES Procedimiento(IdProcedimiento)
);
-- 🔹 Lógica: Cita y Procedimiento tienen una relacion de N:M por lo que se crea una tabla intermedia con ambos id como PK y ademas lo definimod como FK.

```

**Con esto hemos terminado de resolver el problema, pero esto no es todo realmente, podemos seguir mejorando esta BD con mas tablas para resolver mas problemas, asi de este modo crea diferentes BD que resuelvan varios problemas del dia a dia y practica** 

⚠️ **Nota importante**: En caso no entiendas algo recuerda apoyarte con IA o con alguna fuente de internet para poder resolver tus dudas. 


👉 Ahora continuaremos insertando datos a nuestra BD 


## 📋 Datos de prueba

⚠️ **Nota importante**: Estos datos puedes copiar directamente para empezar a trabajar con las consultas, eso si practica manualmente insertando los datos, una vez tengas dominado puedes empezar a copiar y pegar directamente. 

```sql
-- 1. Paciente
INSERT INTO Paciente (DNI, Nombre, Apellido, Fecha_Nacimiento, Sexo, Direccion, Telefono, Correo) VALUES
('12345678', 'Ana', 'Torres', '1985-05-20', 'F', 'Av. Las Flores 123', '987654321', 'ana.torres@mail.com'),
('23456789', 'Luis', 'Gómez', '1992-11-10', 'M', 'Calle Los Sauces 456', '912345678', 'luis.gomez@mail.com'),
('34567890', 'Elena', 'Pérez', '1970-01-15', 'F', 'Jirón Lima 789', '998877665', 'elena.perez@mail.com');

-- 2. Doctor
INSERT INTO Doctor (CMP, Nombre, Apellido, Especialidad, Telefono, Correo) VALUES
('CMP12345', 'Carlos', 'Mata', 'Cardiología', '900111222', 'carlos.mata@salud.com'),
('CMP67890', 'Elena', 'Soto', 'Medicina General', '900333444', 'elena.soto@salud.com'),
('CMP11223', 'Roberto', 'Lira', 'Pediatría', '900555666', 'roberto.lira@salud.com');

-- 3. Cita
INSERT INTO Cita_Consulta (IdPaciente, IdDoctor, FechaHora, Estado) VALUES
(1, 2, '2025-10-05 10:00:00', 'Atendida'),
(2, 1, '2025-10-07 15:30:00', 'Pendiente'),
(1, 1, '2025-10-06 11:00:00', 'Cancelada'),
(3, 3, '2025-10-05 16:00:00', 'No Asistio');

-- 4. Procedimiento
INSERT INTO Procedimiento (Nombre, Descripcion, Costo) VALUES
('Análisis de Sangre', 'Perfil completo de hematología y química.', 50.00),
('Radiografía de Tórax', 'Imagen para evaluar pulmones y corazón.', 80.00),
('Consulta de Seguimiento', 'Revisión posterior al diagnóstico inicial.', 30.00),
('Cirugía Menor', 'Extracción de quiste o sutura simple.', 250.00);

-- 5. Detalle
-- Detalle para la Cita 1 (Atendida a Ana Torres)
INSERT INTO Detalle_Tratamiento (IdCita, IdProcedimiento) VALUES
(1, 1), -- Cita 1: Análisis de Sangre
(1, 3); -- Cita 1: Consulta de Seguimiento

-- Detalle para la Cita 2 (Luis Gómez) - Aún no está atendida, pero se puede pre-registrar.
INSERT INTO Detalle_Tratamiento (IdCita, IdProcedimiento) VALUES
(2, 2); -- Cita 2: Radiografía de Tórax

```

## 📌 Explicación de la carga de datos
- 3 pacientes (diferentes edades y sexos).
- 3 doctores con distintas especialidades.
- 4 citas: una por cada estado posible.
- 4 procedimientos disponibles.
- Detalle para la cita 1 y 2.


👉 Ahora continuaremos con consultas SQL desde las mas sencillas hasta mas complicadas


## 📋 Consultas SQL 

⚠️ **Nota importante**: Aqui encontraremos ejercicios de consultas en base a lo aprendido hasta este capitulo, lee las preguntas e intentalo tu mismo, despues verifica con la solucion y corrige errores, gran parte de poder desarrollar bien las consultas es entender la logica y luego poder ponerla en codigo. 

## Nivel Facil 

```sql 
-- 1. Listar todos los pacientes registrados (usando SELECT *)
SELECT * FROM Paciente;

-- 2. Mostrar todos los doctores y sus especialidades.
SELECT IdDoctor, CMP, Nombre, Apellido, Especialidad
FROM Doctor;

-- 3. Ver todas las citas (citas/consultas) ordenadas por fecha.
SELECT IdCita, IdPaciente, IdDoctor, FechaHora, Estado
FROM Cita_Consulta
ORDER BY FechaHora;

-- 4. Consultar todos los procedimientos disponibles.
SELECT IdProcedimiento, Nombre, Descripcion, Costo
FROM Procedimiento;

```


## Nivel Intermedio 

```sql 
-- 1. Mostrar las citas pendientes de un paciente específico (ej: IdPaciente = 2).
SELECT CC.IdCita, CC.FechaHora, D.Nombre AS Doctor, D.Apellido, CC.Estado
FROM Cita_Consulta CC
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
WHERE CC.IdPaciente = 2 AND CC.Estado = 'Pendiente';
-- Lógica: Une 'Cita_Consulta' con 'Doctor' y filtra por IdPaciente y Estado.

-- 2. Listar los pacientes con más de una cita registrada.
SELECT P.Nombre, P.Apellido, COUNT(CC.IdCita) AS TotalCitas
FROM Paciente P
JOIN Cita_Consulta CC ON P.IdPaciente = CC.IdPaciente
GROUP BY P.Nombre, P.Apellido
HAVING COUNT(CC.IdCita) > 1;
-- Lógica: Utiliza COUNT y HAVING para identificar pacientes recurrentes.

-- 3. Mostrar todas las citas atendidas y su doctor asociado.
-- Nota: En este modelo, el 'Diagnostico' iría en la tabla Cita_Consulta.
SELECT CC.IdCita, CC.FechaHora, D.Nombre AS Doctor, D.Apellido
FROM Cita_Consulta CC
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
WHERE CC.Estado = 'Atendida';
-- Lógica: Filtra solo registros de 'Atendida' (que equivalen a consultas).

-- 4. Listar las citas atendidas en un rango de fechas.
SELECT CC.IdCita, P.Nombre AS Paciente, D.Nombre AS Doctor, CC.FechaHora
FROM Cita_Consulta CC
JOIN Paciente P ON CC.IdPaciente = P.IdPaciente
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
WHERE CC.Estado = 'Atendida'
  AND CC.FechaHora BETWEEN '2025-10-01' AND '2025-10-31';
-- Lógica: Utiliza el JOIN doble para obtener nombres y filtra por rango de fecha.

```


## Nivel Avanzado 

```sql 
-- 1. Top 3 doctores con más citas atendidas.
SELECT TOP 3 D.Nombre, D.Apellido, COUNT(CC.IdCita) AS TotalCitasAtendidas
FROM Doctor D
JOIN Cita_Consulta CC ON D.IdDoctor = CC.IdDoctor
WHERE CC.Estado = 'Atendida'
GROUP BY D.Nombre, D.Apellido
ORDER BY TotalCitasAtendidas DESC;
-- Lógica: Ranking de doctores activos.

-- 2. Pacientes que nunca han tenido una cita atendida.
SELECT P.IdPaciente, P.Nombre, P.Apellido
FROM Paciente P
WHERE NOT EXISTS (
    SELECT 1
    FROM Cita_Consulta CC
    WHERE CC.IdPaciente = P.IdPaciente
      AND CC.Estado = 'Atendida'
);
-- Lógica: Uso avanzado de NOT EXISTS para encontrar pacientes sin historial efectivo.

-- 3. Citas atendidas con sus procedimientos y costo total.
SELECT CC.IdCita, P.Nombre AS Paciente, CC.FechaHora, 
       Pr.Nombre AS Procedimiento, Pr.Costo
FROM Cita_Consulta CC
JOIN Paciente P ON CC.IdPaciente = P.IdPaciente
JOIN Detalle_Tratamiento DT ON CC.IdCita = DT.IdCita
JOIN Procedimiento Pr ON DT.IdProcedimiento = Pr.IdProcedimiento
WHERE CC.Estado = 'Atendida';
-- Lógica: Integra las 4 tablas centrales para ver qué se hizo en cada atención.

-- 4. Historial completo de un paciente (citas, doctor y procedimientos).
SELECT P.Nombre AS Paciente, D.Nombre AS Doctor, CC.FechaHora, CC.Estado,
         Pr.Nombre AS Procedimiento, Pr.Costo
FROM Paciente P
JOIN Cita_Consulta CC ON P.IdPaciente = CC.IdPaciente
JOIN Doctor D ON CC.IdDoctor = D.IdDoctor
LEFT JOIN Detalle_Tratamiento DT ON CC.IdCita = DT.IdCita
LEFT JOIN Procedimiento Pr ON DT.IdProcedimiento = Pr.IdProcedimiento
WHERE P.IdPaciente = 1 -- Historial de Ana Torres
ORDER BY CC.FechaHora;
-- Lógica: Reporte integral usando LEFT JOIN para incluir citas que no tuvieron procedimientos.
```


👉 Con esto hemos terminado nuestra primera practica integrada de BD, aprendimos la logica para construir nuestras tablas, luego insertamos datos para finalmente poder hacer todo tipo de consultas, asi con esta estructura puedes practicar por tu cuenta.