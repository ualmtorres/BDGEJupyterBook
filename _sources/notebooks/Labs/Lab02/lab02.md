# Lab 02. Indexación en MongoDB

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

En este laboratorio se practica con el manejo de índices en MongoDB y con la importación de datos. Se practicará con los creación de índices para la mejora del rendimiento, el uso de la explicación de resultados y el uso de índices espaciales.

**Objetivos**

* Importar datos en una base de datos.
* Realizar operaciones de manejo de índices.
* Utilizar y comprender la información devuelta por la explicación de resultados.
* Realizar operaciones de manejo de índices espaciales.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [MongoDB Tutorial de Geeks for Geeks](https://www.geeksforgeeks.org/mongodb-tutorial/?ref=lbp)
* [MongoDB Tutorial de Java T Point](https://www.javatpoint.com/mongodb-tutorial)

## Actividades

1. Importar el archivo JSON `zips.json` disponible en la carpeta [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `zips` dentro de una base de datos denominada `zips`.
2. Obtener cuántos índices hay definidos y cuáles son los nombres de los índices 
3. Crear una consulta que recupere los documentos del estado `AL`
   1. ¿Cuántos documentos devuelve la consulta? 
   2. ¿Cuántos elementos del índice son leídos para ejecutar la consulta? 
   3. ¿Cuántos documentos son leídos de la base de datos? 
4. Definir un índice ascendente sobre el campo `state`. Volver a recuperar los documentos del estado `AL`.
   1. ¿Cuántos documentos devuelve la consulta? 
   2. ¿Cuántos elementos del índice son leídos para ejecutar la consulta? 
   3. ¿Cuántos documentos son leídos de la base de datos? 
5. Definir dos índices: uno para `city` y otro para `pop`. ¿Cuántos índices hay definidos? 
6. Crear una consulta para obtener todos los documentos de códigos postales con una población superior a 100.000 habitantes.
   1. ¿Cuántos documentos devuelve la consulta? 
   2. ¿Cuántos elementos del índice son leídos para ejecutar la consulta? 
   3. ¿Cuántos documentos son leídos de la base de datos? 
7. Definir un índice espacial 2d para el campo `loc`. Escribir una consulta que recupere los tres documentos de códigos postales más cercanos a las coordenadas `[-86.51, 33.58]`.

   Resultado:

   ```json
   { "city" : "ACMAR", "loc" : [  -86.51557,  33.584132 ], "pop" : 6055, "state" : "AL", "_id" : "35004" }
   { "city" : "LEEDS", "loc" : [  -86.574824,  33.528333 ], "pop" : 10421, "state" : "AL", "_id" : "35094" }
   { "city" : "VANDIVER", "loc" : [  -86.501278,  33.480704 ], "pop" : 1066, "state" : "AL", "_id" : "35176" }
   ```
8. Crear un índice compuesto para `city` y `pop`. Crear una consulta que recupere sólo la población de documentos del estado `NY` y con una población superior a 80.000 habitantes.
   1. ¿Cuántos documentos devuelve la consulta? 
   2. ¿Cuántas entradas del índice analiza la consulta? 
   3. ¿Es posible resolver la consulta usando sólo el índice? 
9. En la colección `bios` de la base de datos `test`, definir un índice disperso sobre el campo `aka` del campo `name`. 
   1. Crear una consulta que devuelva los documentos de galardonados con premios que hayan sido otorgados por organizaciones que contengan la palabra `Free`. 

      Resultado:

      ```json
      { "_id" : 6, "name" : { "first" : "Guido", "last" : "van Rossum" }, … }
      { "_id" : 8, "name" : { "first" : "Yukihiro", "aka" : "Matz", "last" : "Matsumoto" }, … }
      ```
   2. Modificar la consulta anterior para que muestre los resultados ordenados por el campo `aka` de `name`

      Resultado:

      ```json
      { "_id" : 8, "name" : { "first" : "Yukihiro", "aka" : "Matz", "last" : "Matsumoto" }, … }
      ```
   3. ¿Por qué la consulta del apartado _b_ devuelve menos documentos que la del apartado _a_? 
       
10. Crear un índice en la colección `bios` de la base de datos `test` sobre el campo `award` del campo `awards`. Crear una consulta que devuelva los documentos de celebridades que hayan obtenido el premio _(award)_ `Turing Award`.

   Resultado:

   ```json
   { "_id" : 1, "name" : { "first" : "John", "last" : "Backus" }, … }
   { "_id" : 2, "name" : { "first" : "John", "last" : "McCarthy" }, … }
   { "_id" : 7, "name" : { "first" : "Dennis", "last" : "Ritchie" }, … }
   { "_id" : 5, "name" : { "first" : "Ole-Johan", "last" : "Dahl" }, … }
   { "_id" : 4, "name" : { "first" : "Kristen", "last" : "Nygaard" }, … }
   ```
   1. ¿Se usa el índice definido para resolver la consulta? 
   2. ¿Cuántas entradas del índice analiza la consulta? 
   3. ¿Es posible resolver la consulta usando sólo el índice?
