## **¿Qué es una base de datos?** 
Una Base de datos es un conjunto de organizado de información que se almacena y se gestiona electrónicamente en un sistema. 

**Obejetivo**: Permitir guardar, consultar, actualizar y eliminar datos de manera eficiente. 

**Ejemplos**: 
- Base de datos de clientes de un banco. 
- Base de datos de productos de una tienda online. 
- Base de datos de pacientes de una clínica.  

**Piensa en una hoja de excel con filas y columnas. Una base de datos relacional funciona de manera similar, pero mucho mas robusta y segura** 


## **Tipos de Base de datos** 

**Base de datos Relacional**: Se caracteriza por tener una estructura basada en tablas (filas para registros y columnas para los campos) como ejemplo tenemos a SQL server, Oracle, MySQL, PostgreSQL. 

**Base de datos No Relacional**: Trabajan con documentos, grafos o pares clave-valor (imagina que tienes una lista con varios campos para un item 1, para un item 2 seria otra lista y puede tener mas o menos campos que el item 1, no tiene una estructura definda como las relacionales) como ejemplo tenemos a MongoDB, Redis, Cassandra. 

⚠️ **En este curso trabajaremos unicamente con base de datos relacionales en SQL Server**


## **Instalacion y configuracion basica** 

- Aqui encontraras todos los pasos para poder instalar sql sin problemas.
- Se recomienda instalar en idioma ingles (recuerda que aprender ingles es muy importante).
- Como recomendación al seleccionar el modo mixto utiliza como contraseña sa , es el mismo del usuario y será mucho mas facil recordarlo.
- Si tuvieras algún incoveniniente puedes escribirme para poder ayudarte.
- Instala con el siguiente [enlace](https://www.youtube.com/watch?v=lBvBSWO9kn4)


## **Conectarse al servidor y crear una base de datos inicial**
Una vez entramos al sql server managment studio, haremos la creación de nuestra primera base de datos. 

**Creando sin usar query**: Dentro de la interfaz a la mano izquierda veremos una lista con varias carpetas, entre ellas una que dice **Databases**, le daremos click derecho y le daremos a new database, luego escribiremos el siguiente nombre para nuestra base de datos (CursoDB) le damos en ok ... Y listo tenemos nuestra primera base de datos creada. 
Para utilizarla ubicamos un menu desplegable que se encuentra a la izquierda de execute, le damos y seleccionamos nuestra base de datos creada.

**Creando con query**: El query es la plantilla donde vamos a escribir nuestro codigo de base de datos, para ello en la parte superior ubicamos la opcion que diga new query (ctrl + n) y le damos click, esto nos dara nuestro query en blanco donde comenzaremos a escribir codigo. 

- Escribiremos la siguiente sentencia: 

CREATE DATABASE CursoDB;

- Luego buscamos la opcion execute (f5) el cual hara recorrer nuestro codigo y asi creando nuestra base de datos. 
- Para utilizarla utilizaremos la siguiente sentencia: 

USE CursoDB;
go

- Le damos en ejecutar y listo ya estamos utilizando nuestra base de datos. 

**Imagenes Referenciales** 
![Creando sin Query](/assets/images/modulo-01/Modulo01_img01.png)

![Creando con Query](/assets/images/modulo-01/Modulo01_img02.png)

![Ejecutando con Query](/assets/images/modulo-01/Modulo01_img03.png)

**Con esto hemos creado y seleccionado nuestra base de datos con la cual vamos a trabajar en el curso**

## **Ejemplo de como funciona una base de datos** 
En el siguiente ejemplo puedes ver como agregamos una tabla y le insertamos datos, el cual al ejecutar la setencia select nos muestra abajo nuestra tabla.

![Ejemplo](/assets/images/modulo-01/Modulo01_img04.png)

**Puedes ejecutar el codigo tu mismo, solo ten en cuenta que capaz te sale un error, esto es normal porque al ejecutar volvemos a ejecutar el create, lo cual no se puede ya que no podemos tener 2 bases con el mismo nombre** 

**Sombrea la sentencia select x from clientes y darle en ejecutar para que puedas ver la tabla** 

- Codigo: 

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

**Con esto concluimos el primer modulo del curso, aprendimos lo que es una base de datos relacional, como instalar y ejecutar nuestro sql server, ademas de como crear y utilizar nuestra propia base de datos.**





