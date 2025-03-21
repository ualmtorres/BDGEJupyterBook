# Bases de datos a gran escala

Manuel Torres

Máster en Ingeniería Informática. Universidad de Almería

---

La asignatura Bases de datos a gran escala es una asignatura optativa del módulo de Especialidad en Big Data del [Máster oficial en Ingeniería Informática](https://www.ual.es/estudios/masteres/presentacion/7114) de la UAL. En la asignatura se hace una introducción a las bases de datos no relacionales (NoSQL) y a su uso en aplicaciones escalables.

[![Jupyter Book Badge](https://jupyterbook.org/badge.svg)](https://ualmtorres.github.io/BDGEJupyterBook)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.15063708.svg)](https://doi.org/10.5281/zenodo.15063708)

**Objetivos**

* Conocer los principales modos de almacenamiento NoSQL 
* Realizar consultas basicas y de agregacion de datos 
* Conocer modos de indexacion para mejorar el rendimiento de la base de datos 
* Conocer las tecnicas de replicacion y sharding 
* Desarrollar aplicaciones sobre bases de datos NoSQL 
* Aplicar los conocimientos adquiridos y resolver problemas en entornos nuevos o poco conocidos dentro de contextos más amplios (o multidisciplinares) relacionados con su área de estudio 
* Aplicar los conocimientos adquiridos y resolver problemas en entornos nuevos o poco conocidos dentro de contextos más amplios y multidisciplinares, siendo capaces de integrar estos conocimientos 
* Modelar, diseñar, definir la arquitectura, implantar, gestionar, operar, administrar y mantener aplicaciones, redes, sistemas, servicios y contenidos informáticos 
* Analizar las necesidades de información que se plantean en un entorno y llevar a cabo en todas sus etapas el proceso de construcción de un sistema de información

## Introducción

El crecimiento en la produccion de los datos de usuario, sensores, sistemas GPS, y demas, han disparado el tamaño y el tipo de los datos generados, los cuales ademas pueden ser de naturaleza no estructurada. Estos grandes volumenes de datos suponen nuevos desafios en terminos de almacenamiento y procesamiento, por los que las tecnicas tradicionales de gestion de datos no son aplicables directamente en este contexto. Bajo el termino de NoSQL se encuentran los principales productos de bases de datos para tratar con este problema, como son las bases de datos orientadas a columnas, bases de datos clave-valor y bases de datos de documentos. En esta asignatura se estudian los conceptos fundamentales relacionados con NoSQL en el contexto de diferentes productos comerciales. 

## Sobre la Guía docente

### Materia con la que se relaciona en el Plan de estudios:

La asignatura Bases de datos a gran escala está directamente relacionada con las asignaturas siguientes:

* Cloud computing 
* Infraestructura Big Data 
* Análisis de grandes volúmenes de datos 
* Computación sobre datos masivos 
* Visualización de datos 
* Aplicaciones de Big data

### Conocimientos recomendables

* Bases de datos
* Linux
* Git
* Desarrollo web

## Contenidos

* Modelos de almacenamiento no relacionales (4 horas) 
  * El fenomeno NoSQL 
  * Modelos de almacenamiento 
* Bases de datos no relacionales (14 horas)
  * Bases de datos clave-valor 
  * Bases de datos orientadas a documentos 
  * Bases de datos orientadas a grafos
  * Bases de datos orientadas a columnas 
* Escalabilidad de bases de datos
  * Replicación
  * Sharding 
* Desarrollo de aplicaciones para bases de datos a gran escala 

[Planificación de la asignatura](PlanificacionBDGE)

## Horario de clase y de tutorías

* Clases: Aula 22 - Aulario V (Aulas de Informática)
* Sesiones presenciales ([Planificación de la asignatura](PlanificacionBDGE))
  * Horario: 16h a 18h
  * 8 sesiones presenciales = 16 horas.
  * 7 sesiones no presenciales = 14 horas
* Profesores 
  * Manuel Torres Gil
    * Tutorías: Lunes y Viernes de 11h a 13:30h. Martes de 12:30h a 13:30h. Cita previa y a través de Google Meet
    * Despacho: 2.19.5 CITE III (2a planta)
    * email: mailto:mtorres@ual.es[mtorres@ual.es]
    * Twitter: [@ualmtorres](https://twitter.com/ualmtorres)

	
## Cómo seguir la asignatura

* Material disponible en
  * Aula Virtual UAL
  * [Repositorio GitHub](https://ualmtorres.github.io/AsignaturaBasesDatosGranEscala/)
* Metodología docente
  * Clases participativas
  * Contenido práctico
  * Elaboración de trabajos prácticos
  * Actividades no presenciales
  * Tutorías

## Cómo superar la asignatura

* Cada tema tiene una o varias actividades teórico/prácticas, que se entregarán de forma individual sobre:
  * Consultas sobre bases de datos NoSQL
  * Desarrollo de aplicaciones sobre bases de datos NoSQL
  * Despliegue de aplicaciones

**Criterios e Instrumentos de evaluación**

* Los ejercicios y proyectos prácticos deberán ser presentados en la fecha indicada utilizando el Aula Virtual y/o las herramientas y servicios cloud, como repositorios de código, proveedores cloud, servicios en la nube, etc., donde quedan registradas la acciones realizadas.
* En las actividades en equipo, se tendrá en cuenta tanto el trabajo del equipo en su conjunto, como la aportación individual realizada por cada miembro del equipo.

## Encuesta inicial

Si eres alumno de la asignatura en la UAL completa esta [pequeña encuesta](https://forms.gle/V43eetd5r7D4KYuU9) que permita valorar tus conocimientos iniciales y adaptar el desarrollo de la asignatura.

## Recursos

**Entorno básico de pruebas**

Archivo [docker-compose.yml](https://gist.github.com/ualmtorres/c85c3caf61eb9db953ebd5bd9d77ab53) para pruebas con las principales bases de datos NoSQL usadas.

### Tema 1. Modelos de almacenamiento no relacionales. El fenómeno NoSQL

* [Presentación: El fenómeno NoSQL](https://docs.google.com/presentation/d/1jFvI2dYeVBPPnwzm8q4CmO2ABXXxSfThHPg33eHEHck/edit?usp=sharing)
* [Enlaces de interés](Docs/Tema1/Enlaces)

### Tema 2. Bases de datos clave-valor

* [Presentación: Bases de datos clave-valor](https://docs.google.com/presentation/d/1zzATSnpLQf2-7dmB-yRJkogC9e-HRd5YT5brjLgGiig/edit?usp=sharing)
* [Bases de datos clave-valor](Docs/Tema2/redis)
* [Código de ejemplos de la presentación](https://ualmtorres.github.io/SeminarioRedis)
* Archivo [docker-compose.yml](https://gist.github.com/ualmtorres/102a65abc4fea2f3929b48d6803a6b87) con Redis y Redis Commander
* [Tutorial: Interacción con Redis desde PHP](http://ualmtorres.github.io/howtos/RedisPHP/)
* [Tutorial: Interacción con Redis desde Java](http://ualmtorres.github.io/howtos/RedisJava/)
* [Desarrollo de una API REST para gestión de ofertas flash con Slim Framework y Redis](CasosDeUso/ofertasFlashRedis/index)
* [Enlaces de interés](Docs/Tema2/Enlaces)

### Tema 3. Bases de datos orientadas a documentos

* [Presentación: Bases de datos orientadas a documentos](https://docs.google.com/presentation/d/1V4eY5CV23bNb1PBBmzR3We5LsQht496WcymFM-EKRbM/edit?usp=sharing)
* Código de ejemplos de la presentación [[Introducción](https://gist.github.com/ualmtorres/c04eaaa15789bfef2ebec3f9bcf820a6)] [[Agregación](https://gist.github.com/ualmtorres/7fc041515f8a74e5c6fdb289ed241e4f)] [[Indexación](https://gist.github.com/ualmtorres/2f99fff89517687bcd87aaf8523fd112)] 
* Archivo [docker-compose.yml](https://gist.github.com/ualmtorres/48fd676d2e07cd9c905caf7fed3f5bbf) con MongoDB y Mongo-express
* [Tutorial: Interacción con MongoDB desde PHP](http://ualmtorres.github.io/howtos/MongoDBPHP/)
* [Tutorial: Interacción con MongoDB desde Java](http://ualmtorres.github.io/howtos/MongoDBJava/)
* [Desarrollo de una API REST para gestión de productos y valoraciones con Slim Framework y MongoDB](CasosDeUso/valoracionesMongoDB/index)
* [Enlaces de interés](Docs/Tema3/Enlaces)

### Tema 4. Bases de datos orientadas a grafos

* [Presentación: Bases de datos orientadas a grafos](https://docs.google.com/presentation/d/1Fgi6YYBOGAYAQWEyzLL8yGep22sMo0DR2RreYIZRyj4/edit?usp=sharing)
* [Desarrollo de una API REST para gestión de recomendaciones con Slim Framework y Neo4j](CasosDeUso/recomendacionesNeo4j/index)
* [Enlaces de interés](Docs/Tema4/Enlaces)

### Tema 5. Bases de datos orientadas a columnas

* [Presentación: Bases de datos orientadas a columnas](https://docs.google.com/presentation/d/1pHhOeKbeO4k2nzbZLoFeTx72kzUyDvhzGRCGfajk4no/edit?usp=sharing)
* [Cassandra con Docker](https://gist.github.com/ualmtorres/ca414f89b11765a651e32f9f48b08d42)
* [Cassandra con Docker Compose](https://github.com/ualmtorres/CassandraDocker)
* [Tutorial: Cassandra](Docs/Tema5/cassandra)
* [Scripts Tutorial Cassandra](https://gist.github.com/ualmtorres/0c3cb28cf35401d9ac984f2f02895dd4)
* [Desarrollo de una API REST para gestión historial de pedidos y seguimiento en tiempo real con Express y Cassandra](CasosDeUso/historialCassandra/index)
* [Enlaces de interés](Docs/Tema5/Enlaces)

## Trabajo autónomo

* [Lab 01. Consultas básicas en MongoDB](Labs/Lab01/lab01)
* [Lab 02. Manejo de índices en MongoDB](Labs/Lab02/lab02)
* [Lab 03. Desarrollo de un Blog con PHP y MongoDB](Labs/Lab03/lab03)
* [Lab 04. Framework de Agregación de MongoDB](Labs/Lab04/lab04)
* [Lab 05. Consultas en Neo4j](Labs/Lab05/lab05)
* [Lab 06. Desarrollo de un aplicación PHP para la consulta de información cinematográfica almacenada en Neo4j](Labs/Lab06/lab06)

## Actividades complementarias

Puedes profundizar en contenidos de la asignatura a través de estos recursos:

* [Desarrollo de una API REST para gestión de ofertas flash con Slim Framework y Redis](https://ualmtorres.github.io/posts/tutorial-api-rest-slim-redis-ofertas-flash)
* [Desarrollo de una API REST para gestión de productos y valoraciones con Slim Framework y MongoDB](https://ualmtorres.github.io/posts/tutorial-api-rest-slim-mongodb-productos-valoraciones)
* [Desarrollo de una API REST para gestión de recomendaciones con Slim Framework y Neo4j](https://ualmtorres.github.io/posts/tutorial-api-rest-slim-neo4j-recomendaciones)
* [Desarrollo de una API REST para gestión historial de pedidos y seguimiento en tiempo real con Express y Cassandra](https://ualmtorres.github.io/posts/tutorial-api-rest-express-cassandra-pedidos)
* [Actividades complementarias](ActividadesComplementarias/actividadesComplementarias)

## Actividades no presenciales

* No disponible aún.

## Licencia

Licencia CC BY-NC-ND 4.0

Copyright (c) 2025 [Manuel Torres - Departamento de Informática - Universidad de Almería]

Este proyecto está licenciado bajo la Licencia CC BY-NC-ND 4.0. Esto significa que puedes compartir el proyecto siempre que cites al autor, no lo uses para fines comerciales y no realices obras derivadas.