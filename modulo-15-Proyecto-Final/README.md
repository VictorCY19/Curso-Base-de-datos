# 📘 Módulo 15: Proyecto Final
El Proyecto Final es la culminación de tu aprendizaje. Demuestra tu capacidad para diseñar, construir, poblar, automatizar, proteger y mantener una base de datos relacional completa, aplicando todos los conceptos vistos desde el Módulo 01 hasta el Módulo 14.

Elige un caso de estudio (por ejemplo, el sistema de la Clínica "Salud Vital" con algunas extensiones, o un sistema de gestión de inventario/ventas) y cumple con todos los siguientes requerimientos.

## 💡 Guía Detallada de Requerimientos

## 1. Modelado ER (Módulos 02 y 03)
¿Qué se pide realmente?
Demostrar la capacidad de conceptualizar un sistema real. Necesitas un diagrama (puede ser dibujado a mano o digital) que muestre la estructura de tu base de datos antes de escribir una sola línea de código.

### **Pistas y Clarificaciones:**

**Entidades Centrales:** Identifica al menos 5 entidades principales (Ej: Paciente, Doctor, Cita, Procedimiento, etc.).

**Normalización:** Asegúrate de que tu modelo esté al menos en Tercera Forma Normal (3FN). No debe haber datos repetidos ni dependencias parciales.

**Relaciones:** Define las relaciones entre ellas (1:N, N:M). Si encuentras una relación de muchos a muchos (N:M), asegúrate de crear la tabla intermedia que la resuelva (Ej: Detalle_Tratamiento).


## 2. Creación de Tablas con Llaves y Relaciones (Módulo 04)
¿Qué se pide realmente?
Implementar tu modelo ER en SQL, utilizando DDL (Data Definition Language) de forma rigurosa.

### **Pistas y Clarificaciones:**

**Integridad Referencial:** Todas las relaciones 1:N y N:M deben estar codificadas con Llaves Foráneas (FK).

**Tipos de Datos:** Elige tipos de datos óptimos. Usa `NVARCHAR(255)` solo cuando sea necesario; prefiere `INT` para IDs, `DATE` para fechas de nacimiento y `DECIMAL(10, 2)` para costos.

**Restricciones (Constraints): Incluye:**

`NOT NULL` en columnas críticas (Ej: DNI, Nombre).

`UNIQUE` en columnas que no se repiten (Ej: Número de CMP del Doctor).

`CHECK` para validar datos (Ej: un campo Costo debe ser siempre > 0).


## 3. Población de Datos de Prueba (Módulo 04, 12)
¿Qué se pide realmente?
Insertar un volumen de datos suficiente para que tus reportes y consultas tengan sentido.

### **Pistas y Clarificaciones:**

**Volumen:** Se recomienda un mínimo de 50 a 100 registros distribuidos entre las tablas principales.

**Realismo:** Los datos deben ser coherentes (Ej: Las citas deben estar en el futuro, pasado y presente; los doctores deben tener distintas especialidades).

**Importación Masiva:** (Opcional, pero recomendado) Utiliza `BULK INSERT` para simular la importación de una lista de pacientes, demostrando que puedes cargar datos de forma eficiente.


## 4. Procedimientos Almacenados para Operaciones Clave (Módulos 06 y 09)
¿Qué se pide realmente?
Automatizar las tareas más importantes del sistema y protegerlas contra fallos.

### **Pistas y Clarificaciones:**

**Operaciones Esenciales (CRUD):** Crea al menos 3 procedimientos para operaciones de negocio que afecten a varias tablas. Ejemplos:

`usp_RegistrarNuevaCita`: Debe insertar en Cita_Consulta y validar la disponibilidad del doctor.

`usp_CompletarConsulta`: Debe actualizar la cita a Atendida e insertar registros en Detalle_Tratamiento.

**Control de Transacciones:** Todos tus SPs críticos deben usar `BEGIN TRAN`, `COMMIT` y `ROLLBACK` dentro de un bloque `TRY...CATCH` para garantizar la integridad de los datos (todo o nada).


## 5. Vistas y Reportes de Negocio (Módulos 06 y 13)
¿Qué se pide realmente?
Crear objetos de solo lectura que simplifiquen consultas complejas y ofrezcan información valiosa a la gerencia.

### **Pistas y Clarificaciones:**

**Vista Simple:** Una vista que combine datos de 2-3 tablas y muestre el historial de pacientes de forma legible (Ej: `vw_HistorialClinicoDetallado`).

**Reporte Analítico (Funciones de Ventana):** Un reporte que utilice funciones avanzadas (`ROW_NUMBER`, `RANK`, `LEAD` o `LAG`). Ejemplos:

**Ranking:** `RANK()` de los doctores por el total de ingresos generados.

**Tendencia:** `LAG()` para ver la diferencia de costo entre la cita actual y la anterior de un paciente.


## 6. Manejo de Seguridad (Usuarios y Roles) (Módulo 08)
¿Qué se pide realmente?
Demostrar que puedes aplicar el principio de Mínimo Privilegio, limitando lo que cada tipo de usuario puede hacer.

### **Pistas y Clarificaciones:**

**Roles:** Define al menos 3 roles lógicos (Ej: `Rol_Administrador`, `Rol_Doctor`, `Rol_Recepcionista`).

**Permisos: Asigna permisos específicos a cada rol:**

**Rol_Doctor:** `SELECT` en las tablas de Paciente y `EXECUTE` solo en el SPs de diagnóstico.

**Rol_Recepcionista:** `INSERT` y `UPDATE` solo en Cita_Consulta.

**Rol_Administrador:** CONTROL total sobre la base de datos.

**Implementación:** Muestra el código `CREATE ROLE`, `GRANT` y `DENY` para implementar estos permisos.


## 7. Backups y Restauración de la Base (Módulo 11)
¿Qué se pide realmente?
Garantizar la recuperabilidad de la base de datos ante un fallo catastrófico.

### **Pistas y Clarificaciones:**

**Backups: Muestra el código SQL para generar una secuencia de recuperación:**

- Backup Completo (FULL).

- Backup Diferencial (después de insertar algunos datos de prueba adicionales).

- Backup del Log de Transacciones (si es aplicable al modelo de recuperación de tu DB).

**Restauración:** Muestra la secuencia de comandos `RESTORE DATABASE` para restaurar la base de datos a su estado final utilizando los archivos de backup generados.


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Integración Completa: La aplicación de todos los 14 módulos del curso en un proyecto cohesivo.
- ✅ Diseño a Producción: El flujo de trabajo completo, desde el modelado conceptual hasta el mantenimiento operativo y la seguridad.
- ✅ Pensamiento Analítico: La capacidad de traducir los requisitos de negocio en estructuras de datos, código SQL y reportes clave.

👉 **¡Buena suerte! Este proyecto es tu portafolio, demuestra tu dominio de SQL Server.**

📖 Puedes enviarme el link de portafolio o tu proyecto comprimido a mi**[Correo](mailto:al_victor99@hotmail.com?subject=Proyecto_Modulo15_Curso_BD)** tratare de responderte lo antes posible, muchas gracias por leer este primer curso, no olviden compartir este material con personas que crean que les puede ayudar bastante. 

¡Hasta el proximo curso! 🦘

```txt
#    #    #    #     #  #####  #     # ######  ####### 
#   #    # #   ##    # #     # #     # #     # #     # 
#  #    #   #  # #   # #       #     # #     # #     # 
###    #     # #  #  # #  #### #     # ######  #     # 
#  #   ####### #   # # #     # #     # #   #   #     # 
#   #  #     # #    ## #     # #     # #    #  #     # 
#    # #     # #     #  #####   #####  #     # ####### 
```
---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*