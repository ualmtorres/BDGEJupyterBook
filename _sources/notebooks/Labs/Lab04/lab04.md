# Lab 04. Framework de Agregación de MongoDB

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

En este laboratorio se practica con el framework de MongoDB y con la importación de datos. Se practicará con los principales operadores de agregación y el uso de pipelines.

**Objetivos**

* Usar los operadores de agregación.
* Combinar varias etapas de pipeline.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.
* Importar el archivo JSON `zips.json` disponible en [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `zips` dentro de una base de datos denominada `zips`.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [MongoDB Tutorial de Geeks for Geeks](https://www.geeksforgeeks.org/mongodb-tutorial/?ref=lbp)
* [MongoDB Tutorial de Java T Point](https://www.javatpoint.com/mongodb-tutorial)

## Actividades

1. Escribir una consulta que devuelva la cantidad de apartados postales por estado devolviendo dicha cantidad en un campo denominado `numberOfZips`

   Resultado:

   ```json
   {
   	"result" : [
   		…,
   		{
   			"_id" : "NV",
   			"numberOfZips" : 104
   		},
   		{
   			"_id" : "DE",
   			"numberOfZips" : 53
   		},
   		{
   			"_id" : "CA",
   			"numberOfZips" : 1523
   		}
   …}
   ```
2. Escribir una consulta que devuelva la población por estado devolviendo dicho valor en un campo denominado `statePopulation`

   Resultado:

   ```json
   {
   	"result" : [
   		…,
   {
   			"_id" : "NV",
   			"statePopulation" : 1201833
   		},
   		{
   			"_id" : "DE",
   			"statePopulation" : 666168
   		},
   		{
   			"_id" : "CA",
   			"statePopulation" : 29760021
   		}
   …}
   ```
3. Escribir una consulta que devuelva un array `cities` con los nombres de ciudades por estado.

   Resultado:

   ```json
   …
   		{
   			"_id" : "CA",
   			"cities" : [
   				"TRUCKEE",
   				"SOUTH LAKE TAHOE",
   				"TAHOMA",
   				"HOMEWOOD",
   …
   ```
4. Escribir una consulta que devuelva la población de habitantes por estado y ciudad. Devolver la población en un campo denominado `cityPopulation`.

   Resultado:

   ```json
   …
   		{
   			"_id" : {
   				"state" : "TN",
   				"city" : "STRAWBERRY PLAIN"
   			},
   			"cityPopulation" : 5600
   		},
   		{
   			"_id" : {
   				"state" : "AZ",
   				"city" : "GOODYEAR"
   			},
   			"cityPopulation" : 5819
   		}
   …
   ```
5. Escribir una consulta que devuelva la población en número de habitantes y la cantidad de códigos postales de las ciudades `SAN FRANCISCO` (del estado de `CA`) y `BOSTON` (del estado `MA`). Devolver la población en un campo denominado `population` y la cantidad de códigos postales en `numberOfZips`.

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"_id" : "SAN FRANCISCO",
   			"population" : 723993,
   			"numberOfZips" : 26
   		},
   		{
   			"_id" : "BOSTON",
   			"population" : 91302,
   			"numberOfZips" : 11
   		}
   	],
   	"ok" : 1
   }
   ```
6. Escribir una consulta que devuelva la población en número de habitantes por estado y ciudad de aquellas ciudades que tengan más de 1.000.000 de habitantes. Devolver la población en un campo denominado `cityPopulation`.

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"_id" : {
   				"state" : "TX",
   				"city" : "HOUSTON"
   			},
   			"cityPopulation" : 2095918
   		},
   		{
   			"_id" : {
   				"state" : "NY",
   				"city" : "NEW YORK"
   			},
   			"cityPopulation" : 1476790
   		},
   …
   ```
7. A partir de la consulta anterior, escribir una consulta que devuelva las tres ciudades que tienen más población. Además, usando el operador `$concat` (consultar https://docs.mongodb.org/manual/reference/operator/aggregation/concat/ ), devolver las ciudades de esta forma (ciudad, estado), tal y como se muestra a continuación.

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"cityPopulation" : 2452177,
   			"city" : "CHICAGO, IL"
   		},
   		{
   			"cityPopulation" : 2300504,
   			"city" : "BROOKLYN, NY"
   		},
   		{
   			"cityPopulation" : 2102295,
   			"city" : "LOS ANGELES, CA"
   		}
   	],
   	"ok" : 1
   }
   ```
8. Escribir una consulta que devuelva el Top 3 de ciudades que tengan más de 40 códigos postales. Usar el operador `$concat` para devolver las ciudades de esta forma (ciudad, estado), tal y como se muestra a continuación.

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"numberOfZips" : 93,
   			"city" : "HOUSTON, TX"
   		},
   		{
   			"numberOfZips" : 56,
   			"city" : "LOS ANGELES, CA"
   		},
   		{
   			"numberOfZips" : 48,
   			"city" : "PHILADELPHIA, PA"
   		}
   	],
   	"ok" : 1
   }
   ```
9. A partir de la colección `bios` de la base de datos `test`, escribir una consulta que devuelva la cantidad de premios por persona. Mostrar el listado en orden decreciente de número de premios.

   :::{note}

   Puedes importar el archivo JSON `bios.json` disponible en [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `bios`.
   :::

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"_id" : {
   				"name" : "Grace",
   				"lastname" : "Hopper"
   			},
   			"awardsQuantity" : 4
   		},
   		{
   			"_id" : {
   				"name" : "John",
   				"lastname" : "Backus"
   			},
   			"awardsQuantity" : 4
   		},
   …
   ```
10. Escribir una consulta que devuelva las celebridades que habiendo sido premiados con el `Turing Award` tengan más de tres premios. La consulta debe mostrar en un array `awardsReceived` la lista de premios recibidos. La consulta también mostrará la cantidad de premios recibidos en un campo denominado `numberOfAwards`.

   Resultado:

   ```json
   {
   	"result" : [
   		{
   			"_id" : {
   				"name" : "John",
   				"lastname" : "Backus"
   			},
   			"awardsReceived" : [
   				"W.W. McDowell Award",
   				"National Medal of Science",
   				"Turing Award",
   				"Draper Prize"
   			],
   			"numberOfAwards" : 4
   		}
   	],
   	"ok" : 1
   }
   ```
