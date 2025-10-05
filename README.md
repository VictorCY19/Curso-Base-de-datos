# 🗃️ Guía de Estudio: Bases de Datos SQL - De Principiante a Avanzado

[![Estado del Curso](https://img.shields.io/badge/Estado-En%20Desarrollo-yellow)](https://github.com/VictorCY19/Curso-Base-de-datos)

## 🤔 ¿Qué es este proyecto?

Este repositorio contiene **mi ruta de aprendizaje personal** para dominar bases de datos SQL desde cero. Es el resultado de mi estudio autodidacta, organizado en formato de curso práctico que cualquiera puede seguir.

**Mi filosofía**: Aprendo haciendo, y documentando lo que aprendo.

## 🎯 Objetivo de esta guía

Al finalizar esta guía, serás capaz de:
- Entender los fundamentos teóricos de las bases de datos relacionales
- Diseñar modelos de datos eficientes usando diagramas Entidad-Relación
- Escribir consultas SQL complejas con confianza
- Gestionar bases de datos con herramientas profesionales
- Desarrollar un proyecto real que demuestre tus habilidades

## 📚 Estructura del Curso

### **Módulo 1: Introducción**
- ¿Qué es una base de datos? Conceptos básicos
- Tipos de bases de datos: relacionales vs no relacionales (énfasis en relacionales)
- Instalación y configuración básica de SQL Server y SSMS
- Conectarse al servidor, crear una base de datos inicial
- Ejercicio: crear la primera base de datos “CursoDB”

### **Módulo 2: Lenguaje SQL – Fundamentos**
- Lenguajes en SQL Server
- Tipos de Datos en SQL Server
- Crear Tablas con CREATE TABLE
- Insertar registros con INSERT
- Consultar registros con SELECT
- Actualizar registros con UPDATE
- Eliminar registros con DELETE
- Ejercicio Practico 

### **Módulo 3: Modelado de Bases de Datos Relacionales**
- Conceptos de entidades, atributos y relaciones
- Claves primarias y foráneas
- Tipos de relaciones
- Normalización (1FN, 2FN, 3FN)
- Diagramas entidad-relación en SSMS
- Ejercicio práctico: Diseñar modelo para un sistema de ventas

### **Módulo 4: Consultas SQL Intermedias**
- Operadores en WHERE
- Funciones de texto, matemáticas y de fecha
- Ordenar resultados con ORDER BY
- Limitar resultados con TOP
- Eliminar duplicados con DISTINCT
- Uso de alias con AS
- Ejercicios prácticos sobre el sistema de ventas
- Ejercicios de práctica

### **Módulo 5: Consultas Avanzadas**
- JOINS (Uniones entre tablas)
- Subconsultas
- GROUP BY y funciones de agregación
- HAVING (filtrar agrupaciones)
- Common Table Expressions (CTE)
- Nomenclatura y uso de alias en SQL
- Ejercicios prácticos del sistema de ventas
- Ejercicios de práctica

### **Módulo 6: Vistas, Funciones y Procedimientos**
- Vistas: Crear, Modificar, Eliminar 
- Funciones escalares y de tabla en SQL Server
- Procedimientos Almacenados: Creacion y Ejecucion 
- Parametros en Procedimientos 
- Beneficios en Rendimiento y Seguridad
- Ejercicios de practica

### **Módulo 7: Índices y Rendimiento**
- Concepto de Indices 
- Indices Clustered y No-Clustered 
- Indices Unicos y Compuestos 
- Fragmentación en Impacto en Consultas 
- Uso de EXPLAIN / planes de ejecucion en SSMS 
- Ejercicios de practica 

### **Módulo 8: Seguridad en SQL Server**
- Principios de Roles y Usuarios 
- GRANT, REVOKE, DENY 
- Creacion de inicios de sesion y usarios de BD 
- Permisos a nivel de tabla, columna y procedimientos
- Buenas practicas de seguridad en entornos laborales
- Ejercicios de practica

### **Módulo 9: Transacciones y Control de Concurrencia**
- Concepto de Transaccion 
- Comandos: BEGIN TRAN, COMMIT, ROLLBACK
- Manejo de errores con TRY_CATCH
- Niveles de aislamiento de transacciones 
- Deadlocks y como evitarlos 
- Ejercicio de practica 

### **Módulo 10: Triggers** 
- ¿Que es un Trigger? 
- Tipos de Trigger: AFTER y INSTEAD OF
- Casos de uso (auditoría, validación, automatización)
- Limitaciones y buenas prácticas
- Ejercicio de practica 

### **Módulo 11: Administración de Bases de Datos** 
- Backups: completos, diferenciales, transaccionales
- Restauración de bases de datos
- Estrategias de recuperación
- Tareas de mantenimiento: shrink, rebuild, update statistics
- Monitoreo básico en SSMS (Activity Monitor)
- Ejercicio de practica 

### **Módulo 12: Integración y Exportación de Datos**
- Importación desde archivos Excel, CSV
- Exportación de datos
- Uso de BULK INSERT
- Linked Servers (conexiones externas)
- Ejercicio de practica 

### **Módulo 13: Reporting y Análisis de Datos**
- Generar reportes en SQL Server con consultas avanzadas
- Funciones de ventana: ROW_NUMBER, RANK, LEAD, LAG
- Pivot y Unpivot en SQL Server
- Ejercicio de practica 

### **Módulo 14: Buenas Prácticas y Estándares**
- Convenciones de nombres en bases de datos
- Documentación de procedimientos y vistas
- Estrategias de versionamiento de bases de datos
- Optimización de consultas paso a paso
- Checklist de un DBA junior en el mundo laboral

### **Módulo 15: Proyecto Final** 
- Construcción completa de una base de datos para un sistema clínico o de ventas (similar a un caso real)

**Incluye:**
- Modelado ER.
- Creación de tablas con llaves y relaciones.
- Población de datos de prueba.
- Procedimientos almacenados para operaciones clave.
- Vistas y reportes de negocio.
- Manejo de seguridad (usuarios y roles).
- Backups y restauración de la base.

## 🚀 Características clave

### **✅ Enfoque Práctico**
- Cada concepto viene con ejemplos reales
- Ejercicios prácticos en cada módulo
- Código SQL listo para copiar y probar

### **✅ Aprendizaje Progresivo**
- Comienza desde lo más básico (¿qué es una base de datos?)
- Avanza gradualmente hacia temas complejos
- Cada módulo construye sobre el anterior

### **✅ Material Viviente**
- **Este curso está en constante evolución** mientras yo sigo aprendiendo
- Actualizo contenido basado en mi experiencia y feedback
- Nuevos ejemplos y ejercicios se añaden regularmente

## ⚠️ Aspectos importantes a considerar

### **Lo que ESTÁ incluido:**
- Guía estructurada paso a paso
- Ejemplos prácticos y ejercicios
- Proyectos realistas
- Recursos complementarios

### **Lo que NO ESTÁ incluido:**
- **No es una fuente exhaustiva**: Cubro lo esencial, pero siempre recomiendo complementar con documentación oficial
- **No soy un instructor certificado**: Soy un estudiante autodidacta compartiendo mi camino
- **Puede haber errores**: Si encuentras alguno, ¡ayúdame a corregirlo!

## 🛠️ Cómo usar esta guía

1. **Sigue el orden secuencial** de los módulos
2. **Haz todos los ejercicios** - la práctica es fundamental
3. **Experimenta con el código** - modifica los ejemplos y prueba variaciones
4. **Complementa con otros recursos** - nunca dependas de una sola fuente

## 🤝 ¿Encontraste un error o tienes sugerencias?

**¡Tu feedback es invaluable!** Como este es mi primer curso, aprecio cualquier comentario:

- **¿Encontraste un error?** → Abre un [Issue](https://github.com/VictorCY19/Curso-Base-de-datos/issues/new)
- **¿Tienes una mejora?** → Envía un [Pull Request](https://github.com/VictorCY19/Curso-Base-de-datos/pulls)
- **¿Tienes una duda?** → Revisa los [issues existentes](https://github.com/VictorCY19/Curso-Base-de-datos/issues) o crea uno nuevo

## 📝 Mi compromiso contigo

Como creador y estudiante al mismo tiempo, me comprometo a:
- Mantener el contenido actualizado y relevante
- Corregir errores tan pronto como los identifique
- Mejorar la guía basado en mi aprendizaje continuo
- Responder a issues y pull requests en la medida de lo posible

## 🎓 Proyecto Final

El curso culmina con un **proyecto integrador** donde aplicarás todo lo aprendido para construir un sistema completo de base de datos. Es la mejor manera de consolidar tus conocimientos.

---

## ⚡ Empezar ahora

**¿Listo para comenzar?** Ve al [Módulo 1: Introducción](./modulo-01-introduccion/README.md)

---

> **Nota del autor**: Si estás empezando como yo en este mundo, te animo a que sigas esta guía pero también explores por tu cuenta. El aprendizaje autodidacta es sobre curiosidad y experimentación. ¡Vamos a aprender juntos!

**¿Te unes a mi viaje de aprendizaje?** ⭐ Dale una estrella al repositorio para seguir el progreso.
