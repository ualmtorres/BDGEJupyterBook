# Lab 06. Desarrollo de un aplicación PHP para la consulta de información cinematográfica almacenada en Neo4j

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

---

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

## Actividades

Desarrollar una aplicación web en PHP sobre una base de datos Neo4j como la que hay disponible en la URL facilitada en Aula Virtual. Se trata de un ejemplo que muestra información cinematográfica. La aplicación ofrece las siguientes funciones básicas:

* Datos de una película: Título, año de producción y resumen.
* Datos personales de un actor: Nombre y año de nacimiento.
* Filmografía de un actor: Lista de películas con su título y año de producción.
* La aplicación también implementa una API REST que permite lo siguiente:
  * Obtener título, año de producción, casting, director y productor de la película proporcionada
  * Obtener nombre y año de nacimiento del actor proporcionado
  * Obtener nombre, año de nacimiento y la filmografía del actor proporcionado

### Ejercicios propuestos

* Hacer fork de [este repositorio](https://gitlab.com/ualmtorres/Neo4jMoviesIncompleto.git)
* Modificar los scripts siguientes completando con el código necesario en los lugares en los que aparece `YOUR CODE HERE`.
  * `connection.php`: Establece la conexión con Neo4j.
* Modificar el script `api/index.php` para proporcionar una API REST que implemente los métodos siguientes

| Method | URL | Descripción | Uso |
|--------|-----|-------------|-----|
| `GET`  | `/api/movie/{title}` | Devuelve título, año de producción, casting, director y productor de la película proporcionada | `curl -i -X GET http://host/Neo4jMovies/api/api/movie/The%20Matrix` |
| `GET`  | `/api/actor/{name}` | Devuelve nombre y año de nacimiento del actor proporcionado | `curl -i -X GET http://host/Neo4jMovies/api/api/actor/Keanu%20Reeves` |
| `GET`  | `/api/filmography/{name}` | Devuelve nombre, año de nacimiento y la filmografía del actor proporcionado | `curl -i -X GET http://host/Neo4jMovies/api/api/filmography/Keanu%20Reeves` |
:::{note}

En el `README` del proyecto podrás ver ejemplos del JSON que devuelven las peticiones a la API REST.
:::
