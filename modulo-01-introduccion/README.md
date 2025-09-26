# 📗 Módulo 1: Introducción a Bases de Datos

## **¿Qué es una base de datos?**

Una Base de datos es un conjunto organizado de información que se almacena y se gestiona electrónicamente en un sistema.

**Objetivo**: Permitir guardar, consultar, actualizar y eliminar datos de manera eficiente.

**Ejemplos**:
- Base de datos de clientes de un banco
- Base de datos de productos de una tienda online
- Base de datos de pacientes de una clínica

**Piensa en una hoja de Excel con filas y columnas. Una base de datos relacional funciona de manera similar, pero mucho más robusta y segura.**

## **Tipos de Base de datos**

### **Base de datos Relacional**
Se caracteriza por tener una estructura basada en tablas (filas para registros y columnas para los campos). Ejemplos:
- SQL Server
- Oracle
- MySQL
- PostgreSQL

### **Base de datos No Relacional**
Trabajan con documentos, grafos o pares clave-valor (imagina que tienes una lista con varios campos para un item 1, para un item 2 sería otra lista y puede tener más o menos campos que el item 1, no tiene una estructura definida como las relacionales). Ejemplos:
- MongoDB
- Redis
- Cassandra

⚠️ **En este curso trabajaremos únicamente con base de datos relacionales en SQL Server**

## **Instalación y configuración básica**

- Aquí encontrarás todos los pasos para poder instalar SQL sin problemas
- Se recomienda instalar en idioma inglés (recuerda que aprender inglés es muy importante)
- Como recomendación al seleccionar el modo mixto utiliza como contraseña `sa`, es el mismo del usuario y será mucho más fácil recordarlo
- Si tuvieras algún inconveniente puedes escribirme para poder ayudarte

### 📹 Video guía de instalación:
[Ver tutorial de instalación](https://www.youtube.com/watch?v=lBvBSWO9kn4)

## **Conectarse al servidor y crear una base de datos inicial**

Una vez entramos al SQL Server Management Studio, haremos la creación de nuestra primera base de datos.

### **Método 1: Crear sin usar query**
1. Dentro de la interfaz a la mano izquierda veremos una lista con varias carpetas
2. Busca la carpeta que dice **Databases**
3. Click derecho → **New Database**
4. Escribe el nombre: `CursoDB`
5. Click en **OK**

### **Método 2: Crear con query**
1. En la parte superior busca la opción **New Query** (Ctrl + N)
2. Escribe la siguiente sentencia:

```sql
CREATE DATABASE CursoDB;
```

3. Presiona **F5** o click en **Execute**
4. Para utilizar la base de datos:

```sql
USE CursoDB;
GO
```

### **Imágenes Referenciales**
![Creando sin Query](/assets/images/modulo-01/Modulo01_img01.png)
![Creando con Query](/assets/images/modulo-01/Modulo01_img02.png)
![Ejecutando con Query](/assets/images/modulo-01/Modulo01_img03.png)

**Con esto hemos creado y seleccionado nuestra base de datos con la cual vamos a trabajar en el curso**

## **Ejemplo de cómo funciona una base de datos**

En el siguiente ejemplo puedes ver cómo agregamos una tabla y le insertamos datos, el cual al ejecutar la sentencia SELECT nos muestra abajo nuestra tabla.

![Ejemplo](/assets/images/modulo-01/Modulo01_img04.png)

### **Código completo para probar:**

```sql
-- Crear base de datos (ejecutar solo una vez)
CREATE DATABASE CursoDB;
GO

-- Usar la base de datos
USE CursoDB;
GO

-- Crear tabla inicial de prueba
CREATE TABLE Clientes (
    IdCliente INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100) NOT NULL,
    Email NVARCHAR(100),
    FechaRegistro DATETIME DEFAULT GETDATE()
);
GO

-- Insertar registros de prueba
INSERT INTO Clientes (Nombre, Email) VALUES
('Juan Pérez', 'juanperez@mail.com'),
('María López', 'marialopez@mail.com');

-- Consultar registros
SELECT * FROM Clientes;
```

⚠️ **Nota importante**: Si ejecutas todo el código completo te dará error porque no puedes crear la misma base de datos dos veces.

**Solución**: Sombrea solo las partes que quieres ejecutar:
- Si ya creaste la base de datos, ejecuta solo desde `USE CursoDB;`
- Para ver la tabla, ejecuta solo `SELECT * FROM Clientes;`

## ✅ Resumen del módulo

**En este módulo aprendiste:**
- ✅ Qué es una base de datos y sus objetivos
- ✅ Diferencias entre bases de datos relacionales y no relacionales
- ✅ Cómo instalar y configurar SQL Server
- ✅ Cómo crear una base de datos usando ambos métodos (interfaz y query)
- ✅ Cómo crear tablas e insertar datos básicos

---

**¿Listo para continuar?** En el próximo módulo aprenderemos los fundamentos del lenguaje SQL.

📖 **[Ir al Módulo 2: Lenguaje SQL - Fundamentos](../modulo-02-Lenguaje-sql-Fundamentos/README.md)**

---

*Si encuentras algún error o tienes sugerencias para mejorar este módulo, por favor [abre un issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new) en el repositorio.*


