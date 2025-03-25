# Lab 03 Bases de datos a gran escala. Desarrollo de un Blog con PHP y MongoDB (Versión Slim Framework). Máster en Ingeniería Informática

Manuel Torres

Bases de datos a gran escala. Máster en Ingeniería Informática. Universidad de Almería

## Resumen
En este laboratorio se desarrolla una aplicación web PHP sobre una base de datos MongoDB. Se creará una API REST para interactuar con la base de datos.

**Objetivos**

* Familiarse con el driver MongoDB para PHP.
* Desarrollar una API REST en PHP sencilla sobre MongoDB.
* Crear una aplicación sencilla en PHP sobre MongoDB.

**Prerrequisitos**

* Tener configurado el entorno de desarrollo de la asignatura.

## Recursos

Puedes obtener más información sobre cómo trabajar con MongoDB siguiendo estos tutoriales:

* [MongoDB Tutorial de Geeks for Geeks](https://www.geeksforgeeks.org/mongodb-tutorial/?ref=lbp)
* [Creación de una API REST con Slim Framework](https://github.com/ualmtorres/SlimHelloWorld)
* [Desarrollo de una API REST para gestión de productos y valoraciones con Slim Framework y MongoDB](https://ualmtorres.github.io/TutorialSlimMongoDBAPIProductosValoraciones)

## Actividades

Desarrollar una aplicación web en PHP sobre una base de datos MongoDB. Se trata de un ejemplo de un Blog sencillo sobre información legislativa norteamericana. La aplicación ofrece las siguientes funciones básicas:

* La aplicación implementa una API REST que permite lo siguiente:
  * Obtener toda la información de un post a partir de su `ObjectId`
  * Obtener todos los posts que tengan una etiqueta determinada
  * Crear un post
  * Añadir un comentario a un post
* En la página de inicio se muestran cinco posts.
* De forma predeterminada, cada post se presenta de forma parcial, mostrando su título, persona que lo ha escrito, un extracto del cuerpo del post y las etiquetas.
* Se puede mostrar información completa sobre un post.
* Se pueden mostrar los posts (máximo 5) asociados a una etiqueta.
* La aplicación también muestra la lista de todas las personas que hayan realizado algún comentario en algún post. Se pueden mostrar los posts (máximo 5) comentados por una persona.

:::{note}

La aplicación clonada es totalmente funcional, pero omite toda la lógica de acceso a la base de datos (conexión y endpoints de la API). El objetivo de este laboratorio es completar la lógica de acceso a la base de datos para que la aplicación funcione correctamente.
:::

### Ejercicios propuestos

1. Importar el archivo JSON `posts.json` disponible en [SampleDatabases](https://github.com/ualmtorres/SampleDatabases) en una colección `posts` dentro de una base de datos denominada `blog`.
2. Hacer un fork del [repositorio de base](https://github.com/ualmtorres/MongoDBBlogSlimIncompleto) y completar los scripts PHP para que la aplicación funcione correctamente. Los scripts PHP son los siguientes, modificando los lugares en los que aparece `YOUR CODE HERE`:
   * `public/connection.php`
   * `public/routes/posts.php`

#### Descripción de los endpoints de la API REST

| Method | URL | Descripción | Uso |
|--------|-----|-------------|-----|
| GET | `/api/posts/{id}` | Obtener un post por ID | Devuelve los detalles completos de un post específico |
| GET | `/api/posts?tag={tag}` | Obtener posts por etiqueta | Devuelve todos los posts que contienen la etiqueta proporcionada |
| POST | `/api/posts` | Crear un nuevo post | Crea un nuevo post con la información proporcionada en el cuerpo de la petición |
| POST | `/api/posts/{id}/comments` | Añadir comentario a un post | Añade un comentario al post especificado con la información proporcionada en el cuerpo |

#### Ejemplos de cuerpo y respuesta de los endpoints de la API REST

* Operación: Obtener la lista de posts
* Endpoint: `GET /api/posts`
* Respuesta:

  ```json
  {
      "status": 200,
      "data": [
          {
              "_id": "50ab0f8dbcf1bfe2536dc7df",
              "body": "Four score and seven years ago our fathers brought forth on this continent a new nation, conceived in liberty, and dedicated to ...",
              "permalink": "TLxrBfyxTZjqOKqxgnUP",
              "author": "machine",
              "title": "Gettysburg Address",
              "tags": [
                  "wrist",
                  "pasta",
                  "pantyhose",
                  "freon",
                  "tempo",
                  "pail",
                  "atom",
                  "hydrogen",
                  "pilot",
                  "secretary"
              ],
              "comments": [
                  {
                      "body": "Lorem ipsum dolor sit ...",
                      "email": "WCnznPGN@WrhZKAxu.com",
                      "author": "Aleida Elsass"
                  },
                  {
                      "body": "Lorem ipsum dolor sit amet, ...",
                      "email": "SjcQpnuO@LnriWoky.com",
                      "author": "Joaquina Arbuckle"
                  },
                  ...
              ]
          },
          {
              "_id": "50ab0f8dbcf1bfe2536dc7e0",
              "body": "When in the Course of human events, it becomes necessary for one people to dissolve the political bands which have connected them with another, ...",
              "permalink": "TLxrBfyxTZjqOKqxgnUQ",
              "author": "machine",
              "title": "Declaration of Independence",
              "tags": [
                  "pantyhose",
                  "freon",
                  "tempo",
                  "pail",
                  "atom",
                  "hydrogen",
                  "pilot",
                  "secretary",
                  "wrist",
                  "pasta"
              ],
              "comments": [
                  {
                      "body": "Lorem ipsum dolor sit ...",
                      "email": "
                      "author": "Aleida Elsass"
                  },
                  ...
              ]
          },
          ...
      ]
  }
  ```
* Operación: Crear un nuevo post
* Endpoint: `POST /api/posts`
* Cuerpo:

  ```json
  {
      "body": "En un lugar de La Mancha ...",
      "permalink": "TLxrBfyxTZjqOKqxgnUQ",
      "author": "nosqlist",
      "title": "El Quijote",
      "tags": [
          "caballerías",
          "fantasía",
          "héroes"
      ]
  }
  ```
* Operación: Añadir un comentario a un post
* Endpoint: `POST /api/posts/67e339d760c452c6800ff8a2/comments`
* Cuerpo:

  ```json
  {
      "body": "Lorem ipsum dolor sit amet, ...",
      "email": "johndoe@acme.com",
      "author": "John Doe"
  }
  ```
* Operación: Obtener una lista de posts por etiqueta
* Endpoint: `GET /api/posts?tag=caballerías`
* Respuesta:

  ```json
  {
      "status": 200,
      "data": [
          {
              "_id": "67e339d760c452c6800ff8a2",
              "body": "En un lugar de La Mancha ...",
              "permalink": "TLxrBfyxTZjqOKqxgnUQ",
              "author": "nosqlist",
              "title": "El Quijote",
              "tags": [
                  "caballerías",
                  "fantasía",
                  "héroes"
              ],
              "comments": [
                  {
                      "body": "Lorem ipsum dolor sit amet, ...",
                      "email": "johndoe@acme.com",
                      "author": "John Doe"
                  }
              ]
          }
      ],
      "total": 1
  }
  ```

## Pantallas de la aplicación

A continuación se muestran algunas capturas de pantalla de la aplicación:

Esta sería la pantalla de inicio de la aplicación mostrando los cinco primeros posts. A la derecha se muestra la lista de personas que han comentado algún post.

![Home](../../images/lab03-blog-home.png)

Al hacer clic en una etiqueta (p.e. `watch``) se muestran los posts asociados a esa etiqueta. La figura muestra los posts asociados a la etiqueta `watch`.

![Tag](../../images/lab03-blog-tag.png)

También se pueden mostrar los posts asociados a la persona que ha creado el post y a personas que han realizado comentarios. El resultado sería similar al de las figuras anteriores.

La aplicación permite crear nuevos posts pulsando el botón `Nuevo Post`. La figura muestra el formulario para crear un nuevo post.

![New Post](../../images/lab03-blog-new-post.png)

Al hacer clic en un post se muestra la información completa del post. La figura muestra la información completa de un post. Al final se presenta un botón para añadir un comentario, que se muestra en la figura siguiente.

![Post Comments](../../images/lab03-blog-post-comments.png)
