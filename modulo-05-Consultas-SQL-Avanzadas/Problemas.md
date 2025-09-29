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

![Diagrama-Entidad-Relacion](/assets/images/modulo-05/Diagrama_Entidad_Relacion.png)

Aqui puedes ver la solución del esquema previo que debiste haber creado, puedes ver como las palabras ayudan como conectores para saber como conectar nuestras tablas y como los puntos de conexion tienen diferente diseño para identificar si van de: uno a muchos, muchos a muchos o de uno a uno. Recuerda que esto es muy importante para poder colocar nuestras llaves FK donde corresponde, adicional de crear tablas intermedias de ser necesario. 

📌 **Explicación del flujo:**

- Pacientes solicitan Citas.
- Doctores atienden esas Citas.
- Cada Cita genera una Consulta.
- Una Consulta puede tener uno o varios Pagos asociados.
- Una Consulta puede incluir uno o varios Procedimientos médicos.

⚠️ **Nota importante**: este es solo el esquema inicial, ahora cuando pasemos al codigo aumentaremos algunos campos que aporten mas a nuestras tablas y puedan desarrollarse mejor. 


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

⚠️ **Nota importante**: En caso no entiendas algo recuerda apoyarte con IA o con alguna fuente de internet para poder resolver tus dudas. 


👉 Ahora continuaremos insertando datos a nuestra BD 


## 📋 Datos de prueba

⚠️ **Nota importante**: Estos datos puedes copiar directamente para empezar a trabajar con las consultas, eso si practica manualmente insertando los datos, una vez tengas dominado puedes empezar a copiar y pegar directamente. 

```sql
-- 1. Pacientes
INSERT INTO Pacientes (DNI, Nombre, Apellido, FechaNacimiento, Sexo, Telefono, Email, Direccion)
VALUES
('12345678','Juan','Pérez','1990-05-15','M','987654321','juan.perez@mail.com','Av. Siempre Viva 123'),
('87654321','María','González','1985-11-20','F','912345678','maria.gonzalez@mail.com','Jr. Los Olivos 456'),
('11223344','Pedro','Ramírez','2000-03-10','M','999888777','pedro.ramirez@mail.com','Calle Sol 789'),
('44332211','Ana','Torres','1995-07-25','F','955443322','ana.torres@mail.com','Av. Luna 222'),
('55667788','Luis','Fernández','1978-01-30','M','988776655','luis.fernandez@mail.com','Jr. Estrella 654');

-- 2. Médicos
INSERT INTO Medicos (CMP, Nombre, Apellido, Especialidad, Telefono, Email)
VALUES
('CMP001','Carlos','Lopez','Cardiología','913456789','carlos.lopez@clinica.com'),
('CMP002','Sofía','Martinez','Pediatría','914567890','sofia.martinez@clinica.com'),
('CMP003','Javier','Castro','Dermatología','915678901','javier.castro@clinica.com'),
('CMP004','Elena','Rivas','Medicina General','916789012','elena.rivas@clinica.com');

-- 3. Citas
INSERT INTO Citas (IdPaciente, IdMedico, FechaHora, Estado, Motivo)
VALUES
(1,1,'2025-09-01 10:00:00','Atendida','Dolor en el pecho'),
(2,2,'2025-09-02 11:00:00','Atendida','Fiebre y tos'),
(3,3,'2025-09-03 15:00:00','Pendiente','Irritación en la piel'),
(4,4,'2025-09-04 09:30:00','Atendida','Chequeo general'),
(5,1,'2025-09-05 14:00:00','Cancelada','Dolor de cabeza');

-- 4. Consultas
INSERT INTO Consultas (IdCita, Diagnostico, Tratamiento, Observaciones)
VALUES
(1,'Angina leve','Reposo y aspirina','Paciente debe controlarse en 1 mes'),
(2,'Bronquitis','Antibióticos y jarabe','Revisión en 2 semanas'),
(4,'Sin complicaciones','Vitaminas y dieta balanceada','Paciente en buen estado');

-- Nota: Cita 3 está pendiente, por eso no tiene consulta todavía.
-- Nota: Cita 5 fue cancelada, por eso tampoco tiene consulta.

-- 5. Procedimientos
INSERT INTO Procedimientos (Nombre, Descripcion, Costo)
VALUES
('Electrocardiograma','Examen del corazón','150.00'),
('Radiografía de tórax','Imagen diagnóstica pulmonar','200.00'),
('Análisis de sangre','Perfil completo','100.00'),
('Ecografía abdominal','Imagen de abdomen','180.00');

-- 6. Pagos
-- Pago de consulta de Juan (IdConsulta=1)
INSERT INTO Pagos (IdConsulta, Monto, MetodoPago)
VALUES (1,80.00,'Efectivo');

-- Pago de consulta de María (IdConsulta=2)
INSERT INTO Pagos (IdConsulta, Monto, MetodoPago)
VALUES (2,100.00,'Tarjeta');

-- Pago de procedimiento (Electrocardiograma) hecho por Juan
INSERT INTO Pagos (IdProcedimiento, Monto, MetodoPago)
VALUES (1,150.00,'Efectivo');

-- Pago de procedimiento (Radiografía de tórax) hecho por María
INSERT INTO Pagos (IdProcedimiento, Monto, MetodoPago)
VALUES (2,200.00,'Transferencia');

-- Pago de procedimiento (Análisis de sangre) hecho por Ana (Consulta=3 aún pendiente, lo dejamos NULL para simular un caso futuro)
INSERT INTO Pagos (IdProcedimiento, Monto, MetodoPago)
VALUES (3,100.00,'Efectivo');
```

## 📌 Explicación de la carga de datos
- 5 pacientes (diferentes edades y sexos).
- 4 médicos con distintas especialidades.
- 5 citas: unas atendidas, una pendiente y otra cancelada (ideal para probar filtros por estado).
- 3 consultas registradas (solo las citas atendidas).
- 4 procedimientos disponibles.
- 5 pagos (algunos por consulta, otros por procedimiento).


👉 Ahora continuaremos con consultas SQL desde las mas sencillas hasta mas complicadas


## 📋 Consultas SQL 

⚠️ **Nota importante**: Aqui encontraremos ejercicios de consultas en base a lo aprendido hasta este capitulo, lee las preguntas e intentalo tu mismo, despues verifica con la solucion y corrige errores, gran parte de poder desarrollar bien las consultas es entender la logica y luego poder ponerla en codigo. 

## Nivel Facil 

```sql 
--1. Listar todos los pacientes registrados (usando SELECT *)
SELECT * 
FROM Pacientes;
--Nota:
--El * significa “traer todas las columnas de la tabla”.
--Esto devolverá: IdPaciente, DNI, Nombre, Apellido, FechaNacimiento, Sexo, Telefono, Email, Direccion, FechaRegistro.
--Es útil cuando recién exploras la tabla o necesitas una vista rápida de todos los datos.

--Recomendación: en producción lo mejor es especificar las columnas que realmente necesitas para optimizar rendimiento y claridad.


--2. Mostrar todos los médicos y sus especialidades.
SELECT IdMedico, CMP, Nombre, Apellido, Especialidad
FROM Medicos;
--Lógica: Listado más limpio de los médicos, sin columnas extra.


--3. Ver todas las citas ordenadas por fecha.
SELECT IdCita, IdPaciente, IdMedico, FechaHora, Estado
FROM Citas
ORDER BY FechaHora;
--Lógica: Permite al administrador ver el calendario de citas en orden.


--4. Consultar todos los procedimientos disponibles.
SELECT IdProcedimiento, Nombre, Descripcion, Costo
FROM Procedimientos;
--Lógica: Muestra el catálogo de procedimientos y precios.


--5. Mostrar todos los pagos realizados.
SELECT IdPago, Monto, MetodoPago, FechaPago
FROM Pagos;
--Lógica: Permite controlar ingresos y revisar fechas de pago.
```


## Nivel Intermedio 

```sql 
--1. Mostrar las citas pendientes de un paciente específico.
SELECT C.IdCita, C.FechaHora, M.Nombre AS Medico, M.Apellido, C.Estado
FROM Citas C
JOIN Medicos M ON C.IdMedico = M.IdMedico
WHERE C.IdPaciente = 1 AND C.Estado = 'Pendiente';
--Lógica: Relaciona pacientes con médicos y filtra solo citas pendientes.


--2. Listar los pacientes con más de una cita registrada.
SELECT P.Nombre, P.Apellido, COUNT(C.IdCita) AS TotalCitas
FROM Pacientes P
JOIN Citas C ON P.IdPaciente = C.IdPaciente
GROUP BY P.Nombre, P.Apellido
HAVING COUNT(C.IdCita) > 1;
--Lógica: Identifica a los pacientes más recurrentes.


--3. Mostrar todas las consultas con diagnóstico y su médico asociado.
SELECT Con.IdConsulta, Con.Diagnostico, Con.Tratamiento, M.Nombre AS Medico, M.Apellido
FROM Consultas Con
JOIN Citas Ci ON Con.IdCita = Ci.IdCita
JOIN Medicos M ON Ci.IdMedico = M.IdMedico;
--Lógica: Une consultas con citas y médicos, útil para historial clínico.


--4. Obtener el total de pagos por método de pago.
SELECT MetodoPago, SUM(Monto) AS TotalRecaudado
FROM Pagos
GROUP BY MetodoPago;
--Lógica: Resumen financiero de ingresos por tipo de pago.


--5. Listar las citas atendidas en un rango de fechas.
SELECT C.IdCita, P.Nombre AS Paciente, M.Nombre AS Medico, C.FechaHora
FROM Citas C
JOIN Pacientes P ON C.IdPaciente = P.IdPaciente
JOIN Medicos M ON C.IdMedico = M.IdMedico
WHERE C.Estado = 'Atendida'
  AND C.FechaHora BETWEEN '2025-09-01' AND '2025-09-30';
--Lógica: Permite al administrador revisar el historial de un mes específico.
```


## Nivel Avanzado 

```sql 
--1. Top 3 médicos con más citas atendidas.
SELECT TOP 3 M.Nombre, M.Apellido, COUNT(C.IdCita) AS TotalCitas
FROM Medicos M
JOIN Citas C ON M.IdMedico = C.IdMedico
WHERE C.Estado = 'Atendida'
GROUP BY M.Nombre, M.Apellido
ORDER BY TotalCitas DESC;
--Lógica: Ranking de médicos más activos en la clínica.


--2. Pacientes que nunca han tenido una cita atendida.
SELECT P.IdPaciente, P.Nombre, P.Apellido
FROM Pacientes P
WHERE NOT EXISTS (
    SELECT 1
    FROM Citas C
    WHERE C.IdPaciente = P.IdPaciente
      AND C.Estado = 'Atendida'
);
--Lógica: Busca pacientes registrados sin historial de consultas efectivas.


--3. Consultas con procedimientos y pagos asociados.
SELECT Co.IdConsulta, Co.Diagnostico, Pr.Nombre AS Procedimiento, Pa.Monto, Pa.MetodoPago
FROM Consultas Co
LEFT JOIN Pagos Pa ON Co.IdConsulta = Pa.IdConsulta
LEFT JOIN Procedimientos Pr ON Pa.IdProcedimiento = Pr.IdProcedimiento;
--Lógica: Integra diagnóstico, tratamiento y pagos asociados en un solo reporte.


--4. Ingresos mensuales de la clínica.
SELECT YEAR(FechaPago) AS Año, MONTH(FechaPago) AS Mes, SUM(Monto) AS TotalIngresos
FROM Pagos
GROUP BY YEAR(FechaPago), MONTH(FechaPago)
ORDER BY Año DESC, Mes DESC;
--Lógica: Informe financiero agrupado por mes y año.


--5. Historial completo de un paciente (citas, consultas, pagos).
SELECT P.Nombre AS Paciente, C.FechaHora, C.Estado,
       Co.Diagnostico, Co.Tratamiento,
       Pr.Nombre AS Procedimiento, Pa.Monto, Pa.MetodoPago
FROM Pacientes P
JOIN Citas C ON P.IdPaciente = C.IdPaciente
LEFT JOIN Consultas Co ON C.IdCita = Co.IdCita
LEFT JOIN Pagos Pa ON Co.IdConsulta = Pa.IdConsulta
LEFT JOIN Procedimientos Pr ON Pa.IdProcedimiento = Pr.IdProcedimiento
WHERE P.IdPaciente = 1
ORDER BY C.FechaHora;
--Lógica: Reporte integral que combina todas las tablas para un paciente.
```


👉 Con esto hemos terminado nuestra primera practica integrada de BD, aprendimos la logica para construir nuestras tablas, luego insertamos datos para finalmente poder hacer todo tipo de consultas, asi con esta estructura puedes practicar por tu cuenta.