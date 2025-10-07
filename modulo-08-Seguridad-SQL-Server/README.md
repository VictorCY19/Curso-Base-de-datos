# 📘 Módulo 08: Seguridad en SQL Server

La seguridad es primordial, especialmente cuando manejamos datos sensibles de pacientes (PHS/PHI). Este módulo explica cómo controlar quién tiene acceso a la información y qué acciones pueden realizar dentro del sistema de la Clínica "Salud Vital".


## 1. Principios de Roles y Usuarios
La seguridad en SQL Server opera en dos niveles:

### **a) Inicios de Sesión (Logins) - Nivel del Servidor**
Definición: Es una identidad de seguridad que permite a una persona o aplicación conectarse a la instancia de SQL Server.

**Tipos:**

**Autenticación de Windows:** Usa la cuenta de usuario del sistema operativo (recomendado).

**Autenticación de SQL Server:** El usuario debe proporcionar un nombre de usuario y contraseña manejados internamente por SQL Server.

### **b) Usuarios (Users) - Nivel de la Base de Datos**
**Definición:** Es una identidad de seguridad dentro de una base de datos específica. Un Login debe tener un User mapeado para interactuar con tablas y datos.

### **c) Roles (Roles) - Agrupación de Permisos**
**Definición:** Colecciones de permisos. En lugar de asignar permisos a 50 usuarios, asignas los permisos a un Role (Ej: Rol_Administrativo) y luego asignas los 50 usuarios a ese rol. Esto simplifica la administración.


## 2. GRANT, REVOKE, DENY (Manejo de Permisos)
Estos tres comandos son la base para el control de acceso:

| **Comando** | **Acción**                          | **Propósito**                                                                                                                                                 |
|--------------|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **GRANT**    | Concede permisos.                   | Permite que un usuario o rol ejecute una acción (Ej: `SELECT` en **Paciente**).                                         |
| **REVOKE**   | Retira permisos concedidos previamente. | Elimina permisos explícitos dados por **GRANT**.                                                                        |
| **DENY**     | Deniega permisos explícitos.        | Evita que un usuario realice una acción, incluso si un rol al que pertenece le otorga el permiso (**DENY** tiene prioridad sobre **GRANT**). |

**Sintaxis Genérica:**

```sql
-- Ejemplo: Otorgar permiso de SELECT en una tabla a un rol
GRANT SELECT ON [NombreEsquema].[NombreTabla] TO [NombreRol];

-- Ejemplo: Retirar permiso de DELETE a un usuario
REVOKE DELETE ON [NombreTabla] TO [NombreUsuario];

-- Ejemplo: Negar permiso de INSERT en una tabla sensible a un usuario
DENY INSERT ON Paciente TO UsuarioTemporal;
```


## 3. Creación de Inicios de Sesión y Usuarios
Siguiendo las mejores prácticas, crearemos un Login de SQL Server y lo mapearemos a un User en la base de datos Salud_Vital.

### **a) Crear un Inicio de Sesión (Login)**

```sql
-- Crear un Login (Nivel de Servidor) con autenticación SQL Server
CREATE LOGIN SupervisorClinica WITH PASSWORD = 'P@ssw0rdSeguro!', CHECK_POLICY = ON;
```

### **b) Crear un Usuario Mapeado (User)**

```sql
-- En la base de datos Salud_Vital:
USE Salud_Vital;
GO

-- Crear un usuario y asociarlo al Login que acabamos de crear
CREATE USER Supervisor_U FOR LOGIN SupervisorClinica;
```

### **c) Crear un Rol y Asignar Usuario**

```sql
-- 1. Crear un Rol de BD para el personal administrativo
CREATE ROLE Rol_Administrativo;

-- 2. Asignar el nuevo Usuario al Rol
ALTER ROLE Rol_Administrativo ADD MEMBER Supervisor_U;
```


## 4. Permisos a Nivel de Objeto
Una vez que el usuario está en el rol, podemos asignar permisos granulares (nivel de tabla, columna, o procedimiento).

### **a) Permisos a Nivel de Tabla (Lo más común)**
El personal administrativo (Rol_Administrativo) necesita ver todas las tablas, pero solo puede modificar datos en la agenda de citas.

```sql
-- 1. Permiso de Lectura (SELECT) en todas las tablas
GRANT SELECT ON Paciente TO Rol_Administrativo;
GRANT SELECT ON Doctor TO Rol_Administrativo;
GRANT SELECT ON Procedimiento TO Rol_Administrativo;
GRANT SELECT ON Detalle_Tratamiento TO Rol_Administrativo;

-- 2. Permiso de Modificación (INSERT, UPDATE, DELETE) solo en Cita_Consulta
GRANT INSERT, UPDATE, DELETE ON Cita_Consulta TO Rol_Administrativo;
```

### **b) Permisos a Nivel de Columna (Datos sensibles)**
Solo un rol específico (Rol_Gerente) debería ver el campo DNI en la tabla Paciente.

```sql
-- 1. Crear el Rol_Gerente
CREATE ROLE Rol_Gerente;

-- 2. Asignar permiso de SELECT SÓLO a la columna DNI
GRANT SELECT ON Paciente(DNI) TO Rol_Gerente;

-- 3. Negar el SELECT general para forzar la restricción si fuera necesario
-- DENY SELECT ON Paciente TO Rol_Administrativo; (Esto es más complejo, se usa la vista)
```

### **c) Permisos a Nivel de Procedimiento**
Si tenemos un procedimiento almacenado para RegistrarNuevaCita, solo el personal administrativo debe poder ejecutarlo.

```sql
-- Suponiendo que existe este SP (Stored Procedure) del Módulo 6:
-- CREATE PROCEDURE sp_RegistrarNuevaCita (@IdP INT, @IdD INT, @FH DATETIME) ...

GRANT EXECUTE ON sp_RegistrarNuevaCita TO Rol_Administrativo;
```


## 5. Buenas Prácticas de Seguridad en Entornos Laborales

**Principio del Mínimo Privilegio (POLP):** Otorga solo los permisos estrictamente necesarios para que un usuario realice su trabajo. Si solo necesita leer, no le des UPDATE.

**Usar Roles (Roles) siempre:** Nunca asignes permisos directamente a un usuario, usa roles de base de datos para facilitar el mantenimiento.

**Nunca usar cuentas de servicio (SA):** La cuenta sa (System Administrator) debe estar deshabilitada o fuertemente asegurada. Las aplicaciones deben usar cuentas de servicio dedicadas con permisos limitados.

**Auditoría:** Implementa la auditoría para registrar quién, cuándo y qué se hizo en las tablas críticas (ej: Paciente).

**Políticas de Contraseñas:** Fuerza el uso de contraseñas complejas, largas y con cambios periódicos (usando CHECK_POLICY = ON en el Login).


## ✅ Resumen del capítulo

**Ahora dominas:**

- ✅ Inicios de Sesión vs. Usuarios: Entender la diferencia entre la identidad para la conexión (Login) y la identidad dentro de la base de datos (Usuario).
- ✅ Roles: Crear grupos de permisos para simplificar la administración de seguridad de múltiples usuarios (Ej: Rol_Doctor, Rol_Administrativo).
- ✅ Comandos de Permisos: Utilizar GRANT (conceder), REVOKE (revocar) y DENY (negar, que tiene prioridad) para controlar acciones.
- ✅ Niveles Granulares: Aplicar permisos específicos a nivel de tabla, columna (para datos sensibles como DNI) y procedimiento almacenado (EXECUTE).
- ✅ Mínimo Privilegio: Aplicar la regla fundamental de seguridad: asignar solo los permisos estrictamente necesarios para cada rol.


## 🎯 Ejercicios de práctica

**Ejercicio 1: Creación y Asignación de Permisos**

- Crea un nuevo LOGIN llamado DoctorConsulta con la autenticación de SQL Server.
- Mapea este LOGIN a un USER llamado Doctor_U en la base de datos Salud_Vital.
- Crea un ROLE llamado Rol_Doctor.
- Asigna el USER Doctor_U al Rol_Doctor.
- Otorga al Rol_Doctor los permisos de SELECT en las tablas Paciente y Cita_Consulta.

**Ejercicio 2: Uso de DENY**

- Crea un nuevo USER llamado Investigador_U.
- Asigna al USER Investigador_U al Rol_Administrativo (que tiene permiso de SELECT en Paciente).
- Usa el comando DENY para explícitamente negarle al USER Investigador_U el permiso de SELECT en la tabla Paciente.
- (Reflexión: ¿Qué sucede cuando un usuario está en un rol que le da GRANT, pero tiene un DENY explícito? ¿Qué permiso gana?).

👉 **La seguridad es un proceso continuo. Al implementar estas prácticas, aseguras la integridad y privacidad de la información de la clínica.**

📖 **[Ir al Modulo 09: Transacciones y Control de Concurrencia](/modulo-09-Transacciones-Concurrencia/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este capítulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*