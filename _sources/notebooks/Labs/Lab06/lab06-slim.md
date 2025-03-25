# Lab 06 Bases de datos a gran escala. Desarrollo de un aplicación PHP para la consulta de información cinematográfica almacenada en Neo4j (Versión Slim Framework). Máster en Ingeniería Informática

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

## Resumen
En este laboratorio se desarrolla una aplicación web PHP sobre una base de datos Neo4j. Se creará una API REST para interactuar con la base de datos.

**Objetivos**

* Familiarse con el driver Neo4j para PHP.
* Desarrollar una API REST en PHP sencilla sobre Neo4j.
* Crear una aplicación sencilla en PHP sobre Neo4j.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [Getting Started with Cypher - Neo4j](https://neo4j.com/developer/cypher/guide-cypher-basics/)
* [Learn Neo4j Cypher basics in 30 minutes](https://vladbatushkov.medium.com/learn-neo4j-cypher-basics-in-30-minutes-94d68a52544)
* [Creación de una API REST con Slim Framework](https://github.com/ualmtorres/SlimHelloWorld)
* [Desarrollo de una API REST para gestión de recomendaciones con Slim Framework y Neo4j](https://ualmtorres.github.io/posts/tutorial-api-rest-slim-neo4j-recomendaciones)

## Actividades

Desarrollar una aplicación web en PHP sobre una base de datos Neo4j. Se trata de un ejemplo que muestra información cinematográfica. La aplicación ofrece las siguientes funciones básicas:

* La aplicación implementa una API REST que permite lo siguiente:
  * Obtener todas las películas disponibles en la base de datos
  * Obtener información detallada de una película por su título
  * Obtener la filmografía completa de un actor
* En la página de inicio se muestran todas las películas disponibles.
* Cada película muestra su título, año de producción y tagline.
* Se puede mostrar información detallada de una película incluyendo su reparto completo y directores.
* Se puede buscar una película por su título y consultar la filmografía completa de un actor.

:::{note}

La aplicación clonada es totalmente funcional, pero omite toda la lógica de acceso a la base de datos (conexión y endpoints de la API). El objetivo de este laboratorio es completar la lógica de acceso a la base de datos para que la aplicación funcione correctamente.
:::

### Ejercicios propuestos

* Hacer fork del [repositorio de base](https://github.com/ualmtorres/Neo4jMoviesSlimIncompleto) y completar los scripts PHP para que la aplicación funcione correctamente. Los scripts PHP son los siguientes, modificando los lugares en los que aparece `YOUR CODE HERE`:
  * `connection.php`: Establece la conexión con Neo4j.
  * `api/index.php`: Implementa los endpoints de la API REST

#### Descripción de los endpoints de la API REST

| Method | URL | Descripción |
|--------|-----|-------------|
| GET | `/api/movies` | Obtener todas las películas |
| GET | `/api/movies/{title}` | Obtener una película por su título |
| GET | `/api/actors/{name}/movies` | Obtener todas las películas de un actor |

#### Ejemplos de respuesta de los endpoints de la API REST

* Operación: Obtener todas las películas
* Endpoint: `GET /api/movies`

  ```json
  [
      {
          "id": 0,
          "title": "The Matrix",
          "released": 1999,
          "tagline": "Welcome to the Real World"
      },
      {
          "id": 9,
          "title": "The Matrix Reloaded",
          "released": 2003,
          "tagline": "Free your mind"
      },
      {
          "id": 10,
          "title": "The Matrix Revolutions",
          "released": 2003,
          "tagline": "Everything that has a beginning has an end"
      },
      ...
  ]
  ```
* Operación: Obtener una película por su título
* Endpoint: `GET /api/movies/The Matrix`

  ```json
  {
      "title": "The Matrix",
      "year": 1999,
      "tagline": "Welcome to the Real World",
      "actors": [
          "Keanu Reeves",
          "Laurence Fishburne",
          "Carrie-Anne Moss",
          "Hugo Weaving"
      ],
      "directors": [
          "Lana Wachowski",
          "Lilly Wachowski"
      ]
  }
  ```
* Operación: Obtener todas las películas de un actor
* Endpoint: `GET /api/actors/Keanu Reeves/movies`

  ```json
  [
      {
          "id": 0,
          "title": "The Matrix",
          "released": 1999,
          "tagline": "Welcome to the Real World"
      },
      {
          "id": 9,
          "title": "The Matrix Reloaded",
          "released": 2003,
          "tagline": "Free your mind"
      },
      {
          "id": 10,
          "title": "The Matrix Revolutions",
          "released": 2003,
          "tagline": "Everything that has a beginning has an end"
      },
      ...
  ]
  ```

## Pantallas de la aplicación

A continuación se muestran algunas capturas de pantalla de la aplicación:

Esta sería la pantalla de inicio de la aplicación mostrando las películas. A la derecha permite la búsqueda de películas por título y la consulta de la filmografía de un actor.

![Home](../../images/lab06-movies-home.png)

Al hacer clic sobre una película se muestra la información completa de la película, incluyendo su reparto y directores. La figura muestra la información completa de una película.

![Movie](../../images/lab06-movies-movie.png)

La aplicación permite buscar una película por su título. La figura muestra el resultado de la búsqueda de la película `Top Gun`. (En este caso, la API permite la búsqueda sin distinguir mayúsculas y minúsculas. Se recomienda convertir a minúsculas antes de realizar la búsqueda y realizar la comparación con el campo en minúsculas).

![Search](../../images/lab06-movies-search-movie.png)

La aplicación permite consultar la filmografía de un actor. La figura muestra la filmografía de `Keanu Reeves`. (En este caso, la API permite la búsqueda sin distinguir mayúsculas y minúsculas. Se recomienda convertir a minúsculas antes de realizar la búsqueda y realizar la comparación con el campo en minúsculas).

![Actor](../../images/lab06-movies-actor.png)
