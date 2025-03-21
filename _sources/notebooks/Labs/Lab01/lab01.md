# Lab 01. Consultas en MongoDB

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

En este laboratorio se practica con consultas básicas en MongoDB y con la importación de datos. Se practicará con los operadores básicos de comparación, conteo de resultados, manejo de arrays y ordenación de resultados.

**Objetivos**

* Importar datos en una base de datos.
* Realizar operaciones básicas de consulta.
* Realizar consultas con manejo de arrays.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [MongoDB Tutorial de Geeks for Geeks](https://www.geeksforgeeks.org/mongodb-tutorial/?ref=lbp)
* [MongoDB Tutorial de Java T Point](https://www.javatpoint.com/mongodb-tutorial)

## Actividades

1. Importar el archivo JSON `zips.json` disponible en la carpeta [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `zips` dentro de una base de datos denominada `zips`.
2. Crear una consulta que recupere los documentos de ciudades del estado `AL` con población superior a 40000 habitantes

   Resultado:

   ```json
   { "city" : "BESSEMER", "loc" : [  -86.947547,  33.409002 ], "pop" : 40549, "state" : "AL", "_id" : "35020" }
   { "city" : "CENTER POINT", "loc" : [  -86.693197,  33.635447 ], "pop" : 43862, "state" : "AL", "_id" : "35215" }
   { "city" : "TUSCALOOSA", "loc" : [  -87.562666,  33.196891 ], "pop" : 42124, "state" : "AL", "_id" : "35401" }
   { "city" : "SOUTHSIDE", "loc" : [  -86.010279,  33.997248 ], "pop" : 44165, "state" : "AL", "_id" : "35901" }
   ```
3. Crear una consulta que recupere los documentos de ciudades del estado `AL` con población superior a 35000 e inferior a 37000 habitantes

   Resultado:

   ```json
   { "city" : "BIRMINGHAM", "loc" : [  -86.85904,  33.481565 ], "pop" : 35836, "state" : "AL", "_id" : "35211" }
   { "city" : "DECATUR", "loc" : [  -86.98868,  34.589599 ], "pop" : 36696, "state" : "AL", "_id" : "35601" }
   { "city" : "ATHENS", "loc" : [  -86.970733,  34.803604 ], "pop" : 35441, "state" : "AL", "_id" : "35611" }
   ```
4. Escribir una consulta que recupere los documentos de ciudades del estado `AL` o `WA` con población superior a 35000 e inferior a 37000 habitantes. Consejo: Se puede resolver con `$or` o con `$in`

   Resultado:

   ```json
   { "city" : "BIRMINGHAM", "loc" : [  -86.85904,  33.481565 ], "pop" : 35836, "state" : "AL", "_id" : "35211" }
   { "city" : "DECATUR", "loc" : [  -86.98868,  34.589599 ], "pop" : 36696, "state" : "AL", "_id" : "35601" }
   { "city" : "ATHENS", "loc" : [  -86.970733,  34.803604 ], "pop" : 35441, "state" : "AL", "_id" : "35611" }
   { "city" : "LYNNWOOD", "loc" : [  -122.282139,  47.850532 ], "pop" : 36995, "state" : "WA", "_id" : "98037" }
   { "city" : "SEATTLE", "loc" : [  -122.275021,  47.541234 ], "pop" : 36684, "state" : "WA", "_id" : "98118" }
   { "city" : "BONNEY LAKE", "loc" : [  -122.180275,  47.188801 ], "pop" : 35436, "state" : "WA", "_id" : "98390" }
   ```
5. Escribir una consulta que devuelva cuántas ciudades tiene el estado `AL` con poblaciones comprendidas entre 10.000 y 20.000 habitantes?

   Resultado: 89
6. Importar el archivo JSON `bios.json` disponible en [SampleDatabases](../../../SampleDatabases) en una colección `bios` dentro de la base de datos denominada `test`.
7. Escribir una consulta que recupere el nombre y apellidos de celebridades que hayan obtenido un premio después del año 2000.

   Resultado:

   ```json
   { "name" : { "first" : "Kristen", "last" : "Nygaard" } }
   { "name" : { "first" : "Ole-Johan", "last" : "Dahl" } }
   { "name" : { "first" : "Guido", "last" : "van Rossum" } }
   { "name" : { "first" : "Dennis", "last" : "Ritchie" } }
   { "name" : { "first" : "Yukihiro", "last" : "Matsumoto" } }
   { "name" : { "first" : "James", "last" : "Gosling" } }
   ```
8. Escribir una consukta que recupere nombre y apellidos de celebridades a los que se les atribuye la creación de `Lisp` y `ALGOL`

   Resultado:

   ```json
   { "name" : { "first" : "John", "last" : "McCarthy" } }
   ```
9. Escribir una consulta que añada en una sola operación las contribuciones `Gosling Emacs` y `(p-code) virtual machine` a `James Gosling`
10. Escribir una consulta que devuelva un listado alfabético de tres celebridades que hayan obtenido algún galardón por alguna organización de IEEE.

   Resultado:

   ```json
   { "name" : { "first" : "John", "last" : "Backus" } }
   { "name" : { "first" : "Ole-Johan", "last" : "Dahl" } }
   { "name" : { "first" : "Grace", "last" : "Hopper" } }
   ```
